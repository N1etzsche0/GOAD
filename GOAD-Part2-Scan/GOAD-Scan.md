# GOAD 侦查和扫描

## 枚举网络

### CME侦查

使用crackmapexec(cme)扫描netbios协议,可以**快速获取**所有 Windows机器 IP、名称和域的

```bash
crackmapexec smb 192.168.56.0/24 
```

```bash
SMB         192.168.56.12   445    MEEREEN          [*] Windows Server 2016 Standard Evaluation 14393 x64 (name:MEEREEN) (domain:essos.local) (signing:True) (SMBv1:True)
SMB         192.168.56.10   445    KINGSLANDING     [*] Windows 10.0 Build 17763 x64 (name:KINGSLANDING) (domain:sevenkingdoms.local) (signing:True) (SMBv1:False)
SMB         192.168.56.11   445    WINTERFELL       [*] Windows 10.0 Build 17763 x64 (name:WINTERFELL) (domain:north.sevenkingdoms.local) (signing:True) (SMBv1:False)
SMB         192.168.56.22   445    CASTELBLACK      [*] Windows 10.0 Build 17763 x64 (name:CASTELBLACK) (domain:north.sevenkingdoms.local) (signing:False) (SMBv1:False)
SMB         192.168.56.23   445    BRAAVOS          [*] Windows Server 2016 Standard Evaluation 14393 x64 (name:BRAAVOS) (domain:essos.local) (signing:False) (SMBv1:True)
```

我们现在知道有 3 个域：

* north.sevenkingdoms.local (2 ip)
  * 192.168.56.22 CASTELBLACK(Windows server 2019)(signing false)
  * 192.168.56.11 WINTERFELL(Windows server 2019)
* sevenkingdoms.local (1 ip)
  * 192.168.56.10 KINGSLANDING(Windows server 2019)
* essos.local (2 ip)
  * 192.168.56.23 BRAAVOS(Windows server 2016)(signing false)
  * 192.168.56.12 MEEREEN(Windows server 2019)

有3个域，所以必须存在3个DC,微软默认将DC的smb签名设置为True

因此所有的DC都是签名为**signing true**

在安全环境中，签名必须在任何地方都为真，以避免 ntlm 中继攻击

SMB是一种网络文件共享协议
因此，SMB需要计算机或服务器上的网络端口才能与其他系统通信
SMB使用IP端口:139或445

* 端口 139：SMB最初使用端口139在NetBIOS之上运行NetBIOS是一个较旧的传输层，它允许Windows计算机在同一网络上相互通信

* 端口 445：更高版本的SMB(在Windows2000之后)开始在TCP堆栈之上使用端口445,使用TCP允许SMB通过Internet工作

### 寻找域控

通过使用nslookup查询dns来枚举DC的IP

```bash
nslookup -type=srv _ldap._tcp.dc._msdcs.north.sevenkingdoms.local 192.168.56.10
```

```bash
Server:  192.168.56.10
Address: 192.168.56.10#53

_ldap._tcp.dc._msdcs.sevenkingdoms.local service = 0 100 389 kingslanding.sevenkingdoms.local.
```

```bash
nslookup -type=srv _ldap._tcp.dc._msdcs.north.sevenkingdoms.local 192.168.56.10
```

```bash
Server:  192.168.56.10
Address: 192.168.56.10#53

Non-authoritative answer:
_ldap._tcp.dc._msdcs.north.sevenkingdoms.local service = 0 100 389 winterfell.north.sevenkingdoms.local.

Authoritative answers can be found from:
winterfell.north.sevenkingdoms.local internet address = 192.168.56.11
winterfell.north.sevenkingdoms.local internet address = 10.0.2.15
```

```bash
nslookup -type=srv _ldap._tcp.dc._msdcs.essos.local 192.168.56.10 
```

```bash
Server:  192.168.56.10
Address: 192.168.56.10#53

Non-authoritative answer:
_ldap._tcp.dc._msdcs.essos.local service = 0 100 389 meereen.essos.local.

Authoritative answers can be found from:
meereen.essos.local internet address = 192.168.56.12
```

### 设置/etc/hosts和kerberos

设置/etc/hosts文件来配置DNS

```bash
192.168.56.10   sevenkingdoms.local kingslanding.sevenkingdoms.local kingslanding
192.168.56.11   winterfell.north.sevenkingdoms.local north.sevenkingdoms.local winterfell
192.168.56.12   essos.local meereen.essos.local meereen
192.168.56.22   castelblack.north.sevenkingdoms.local castelblack
192.168.56.23   braavos.essos.local braavos
```

安装Linux kerberos客户端

```bash
sudo apt install krb5-user
```

设置如下

```bash
realm: essos.local
servers: meereen.essos.local
```

配置/etc/krb5.conf

```bash
[libdefaults]
  default_realm = essos.local
  kdc_timesync = 1
  ccache_type = 4
  forwardable = true
  proxiable = true
  fcc-mit-ticketflags = true
[realms]
  north.sevenkingdoms.local = {
      kdc = winterfell.north.sevenkingdoms.local
      admin_server = winterfell.north.sevenkingdoms.local
    }
  sevenkingdoms.local = {
      kdc = kingslanding.sevenkingdoms.local
      admin_server = kingslanding.sevenkingdoms.local
    }
  essos.local = {
      kdc = meereen.essos.local
      admin_server = meereen.essos.local
    }
......
```

如果已经安装了 krb5-user，我们可以使用(dpkg-reconfigure 或通过修改/etc/krb5.conf)重新配置它

```bash
dpkg-reconfigure krb5-config
```

现在已在环境中设置好了kerberos，我们将尝试是否可以为用户获取 TGT(假设已知用户密码)

使用impacket/examples中的getTGT脚本

```bash
impacket-getTGT essos.local/khal.drogo:horse
```

设置环境变量

```bash
export KRB5CCNAME=khal.drogo.ccache
impacket-smbclient -k @braavos.essos.local
Impacket v0.10.0 - Copyright 2022 SecureAuth Corporation

Type help for list of commands
# shares
ADMIN$
all
C$
CertEnroll
IPC$
public
# use C$
# ls
drw-rw-rw-          0  Thu Feb 14 06:42:10 2019 $Recycle.Bin
-rw-rw-rw-     384322  Thu Feb 14 14:38:48 2019 bootmgr
-rw-rw-rw-          1  Thu Feb 14 14:38:48 2019 BOOTNXT
-rw-rw-rw-        557  Wed May 31 02:17:02 2023 dns_log.txt
drw-rw-rw-          0  Wed May 31 06:44:03 2023 Documents and Settings
drw-rw-rw-          0  Wed May 31 02:43:38 2023 inetpub
-rw-rw-rw- 1476395008  Fri Jun  2 03:37:03 2023 pagefile.sys
drw-rw-rw-          0  Thu Feb 14 07:19:12 2019 PerfLogs
drw-rw-rw-          0  Wed May 31 03:13:57 2023 Program Files
drw-rw-rw-          0  Wed May 31 06:18:09 2023 Program Files (x86)
drw-rw-rw-          0  Wed May 31 05:56:25 2023 ProgramData
drw-rw-rw-          0  Tue May 30 22:44:34 2023 Recovery
drw-rw-rw-          0  Wed May 31 03:07:19 2023 setup
drw-rw-rw-          0  Wed May 31 06:22:02 2023 shares
drw-rw-rw-          0  Thu Feb 14 14:40:57 2019 System Volume Information
drw-rw-rw-          0  Wed May 31 02:28:11 2023 tmp
drw-rw-rw-          0  Wed May 31 03:15:37 2023 Users
drw-rw-rw-          0  Wed May 31 02:47:28 2023 Windows
# Bye!
```

kerberos设置很好,取消Ticket

```bash
unset KRB5CCNAME
```

对winterfell进行测试,出现问题

```bash
impacket-getTGT north.sevenkingdoms.local/arya.stark:Needle
```

```bash
Impacket v0.10.0 - Copyright 2022 SecureAuth Corporation
[*] Saving ticket in arya.stark.ccache
```

```bash
export KRB5CCNAME=arya.stark.ccache
```

作者也不知道为什么kerberos不能在具有完整 FQDN 的 winterfell上运行，但是只需设置目标为 winterfell 而不是 winterfell.north.sevenkingdoms.loca就可以了

```bash
impacket-smbclient -k @winterfell.north.sevenkingdoms.local

Impacket v0.10.0 - Copyright 2022 SecureAuth Corporation

[-] SMB SessionError: STATUS_MORE_PROCESSING_REQUIRED({Still Busy} The specified I/O request packet (IRP) cannot be disposed of because the I/O operation is not complete.)
```

```bash
impacket-smbclient -k @winterfell          
Impacket v0.10.0 - Copyright 2022 SecureAuth Corporation

Type help for list of commands
# shares
ADMIN$
C$
IPC$
NETLOGON
SYSVOL
# use C$
[-] SMB SessionError: STATUS_ACCESS_DENIED({Access Denied} A process has requested access to an object but has not been granted those access rights.)
# use SYSVOL
# ls
drw-rw-rw-          0  Wed May 31 02:08:31 2023 .
drw-rw-rw-          0  Wed May 31 02:08:31 2023 ..
drw-rw-rw-          0  Wed May 31 02:08:31 2023 north.sevenkingdoms.local
# 
```

### Namp

nmap会在扫描目标之前执行ping,如果目标不响应ping,它将被忽略

确保我们不会遗漏TCP上任何内容的方法可能是使用以下选项进行扫描：

```bash
nmap -Pn -p- -sC -sV -oA full_scan_goad 192.168.56.10-12,22-23
```

* Pn 不ping 直接扫描提供的全部IP
* p- 扫描全部65535个端口
* sC 执行侦查脚本
* sV 遍历版本
* oA 以三种形式输出结果 (nmap classic, grep format, xml format)

<details>
<summary>展开查看</summary>
<pre><code class="bash">
# Nmap 7.93 scan initiated Fri Jun  2 05:50:26 2023 as: nmap -Pn -p- -sC -sV -oA full_scan_goad 192.168.56.10-12,22-23
Nmap scan report for sevenkingdoms.local (192.168.56.10)
Host is up (0.0023s latency).
Not shown: 65511 filtered tcp ports (no-response)
PORT      STATE SERVICE       VERSION
53/tcp    open  domain        Simple DNS Plus
80/tcp    open  http          Microsoft IIS httpd 10.0
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-server-header: Microsoft-IIS/10.0
|_http-title: IIS Windows Server
88/tcp    open  kerberos-sec  Microsoft Windows Kerberos (server time: 2023-06-02 09:54:26Z)
135/tcp   open  msrpc         Microsoft Windows RPC
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp   open  ldap          Microsoft Windows Active Directory LDAP (Domain: sevenkingdoms.local0., Site: Default-First-Site-Name)
| ssl-cert: Subject: commonName=kingslanding.sevenkingdoms.local
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1::<unsupported>, DNS:kingslanding.sevenkingdoms.local
| Not valid before: 2023-05-31T06:37:34
|_Not valid after:  2024-05-30T06:37:34
|_ssl-date: 2023-06-02T09:57:10+00:00; +10s from scanner time.
445/tcp   open  microsoft-ds?
464/tcp   open  kpasswd5?
593/tcp   open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp   open  ssl/ldap      Microsoft Windows Active Directory LDAP (Domain: sevenkingdoms.local0., Site: Default-First-Site-Name)
|_ssl-date: 2023-06-02T09:57:10+00:00; +10s from scanner time.
| ssl-cert: Subject: commonName=kingslanding.sevenkingdoms.local
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1::<unsupported>, DNS:kingslanding.sevenkingdoms.local
| Not valid before: 2023-05-31T06:37:34
|_Not valid after:  2024-05-30T06:37:34
3268/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: sevenkingdoms.local0., Site: Default-First-Site-Name)
|_ssl-date: 2023-06-02T09:57:10+00:00; +10s from scanner time.
| ssl-cert: Subject: commonName=kingslanding.sevenkingdoms.local
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1::<unsupported>, DNS:kingslanding.sevenkingdoms.local
| Not valid before: 2023-05-31T06:37:34
|_Not valid after:  2024-05-30T06:37:34
3269/tcp  open  ssl/ldap      Microsoft Windows Active Directory LDAP (Domain: sevenkingdoms.local0., Site: Default-First-Site-Name)
| ssl-cert: Subject: commonName=kingslanding.sevenkingdoms.local
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1::<unsupported>, DNS:kingslanding.sevenkingdoms.local
| Not valid before: 2023-05-31T06:37:34
|_Not valid after:  2024-05-30T06:37:34
|_ssl-date: 2023-06-02T09:57:10+00:00; +10s from scanner time.
3389/tcp  open  ms-wbt-server Microsoft Terminal Services
| ssl-cert: Subject: commonName=kingslanding.sevenkingdoms.local
| Not valid before: 2023-05-30T05:54:08
|_Not valid after:  2023-11-29T05:54:08
|_ssl-date: 2023-06-02T09:57:10+00:00; +10s from scanner time.
5985/tcp  open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
5986/tcp  open  ssl/http      Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
| tls-alpn: 
|_  http/1.1
|_ssl-date: 2023-06-02T09:57:10+00:00; +10s from scanner time.
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
| ssl-cert: Subject: commonName=VAGRANT
| Subject Alternative Name: DNS:VAGRANT, DNS:vagrant
| Not valid before: 2023-05-29T19:28:41
|_Not valid after:  2026-05-28T19:28:41
9389/tcp  open  mc-nmf        .NET Message Framing
49668/tcp open  msrpc         Microsoft Windows RPC
49670/tcp open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
49671/tcp open  msrpc         Microsoft Windows RPC
49673/tcp open  msrpc         Microsoft Windows RPC
49674/tcp open  msrpc         Microsoft Windows RPC
49687/tcp open  msrpc         Microsoft Windows RPC
49699/tcp open  msrpc         Microsoft Windows RPC
53365/tcp open  msrpc         Microsoft Windows RPC
Service Info: Host: KINGSLANDING; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
|_nbstat: NetBIOS name: KINGSLANDING, NetBIOS user: <unknown>, NetBIOS MAC: 080027078d64 (Oracle VirtualBox virtual NIC)
| smb2-time: 
|   date: 2023-06-02T09:56:27
|_  start_date: N/A
| smb2-security-mode: 
|   311: 
|_    Message signing enabled and required
|_clock-skew: mean: 9s, deviation: 0s, median: 9s

Nmap scan report for winterfell.north.sevenkingdoms.local (192.168.56.11)
Host is up (0.0028s latency).
Not shown: 65513 filtered tcp ports (no-response)
PORT      STATE SERVICE       VERSION
53/tcp    open  domain        Simple DNS Plus
88/tcp    open  kerberos-sec  Microsoft Windows Kerberos (server time: 2023-06-02 09:54:32Z)
135/tcp   open  msrpc         Microsoft Windows RPC
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp   open  ldap          Microsoft Windows Active Directory LDAP (Domain: sevenkingdoms.local0., Site: Default-First-Site-Name)
| ssl-cert: Subject: commonName=winterfell.north.sevenkingdoms.local
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1::<unsupported>, DNS:winterfell.north.sevenkingdoms.local
| Not valid before: 2023-05-31T09:47:43
|_Not valid after:  2024-05-30T09:47:43
|_ssl-date: 2023-06-02T09:57:10+00:00; +10s from scanner time.
445/tcp   open  microsoft-ds?
464/tcp   open  kpasswd5?
593/tcp   open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp   open  ssl/ldap      Microsoft Windows Active Directory LDAP (Domain: sevenkingdoms.local0., Site: Default-First-Site-Name)
|_ssl-date: 2023-06-02T09:57:10+00:00; +10s from scanner time.
| ssl-cert: Subject: commonName=winterfell.north.sevenkingdoms.local
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1::<unsupported>, DNS:winterfell.north.sevenkingdoms.local
| Not valid before: 2023-05-31T09:47:43
|_Not valid after:  2024-05-30T09:47:43
3268/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: sevenkingdoms.local0., Site: Default-First-Site-Name)
|_ssl-date: 2023-06-02T09:57:10+00:00; +10s from scanner time.
| ssl-cert: Subject: commonName=winterfell.north.sevenkingdoms.local
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1::<unsupported>, DNS:winterfell.north.sevenkingdoms.local
| Not valid before: 2023-05-31T09:47:43
|_Not valid after:  2024-05-30T09:47:43
3269/tcp  open  ssl/ldap      Microsoft Windows Active Directory LDAP (Domain: sevenkingdoms.local0., Site: Default-First-Site-Name)
| ssl-cert: Subject: commonName=winterfell.north.sevenkingdoms.local
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1::<unsupported>, DNS:winterfell.north.sevenkingdoms.local
| Not valid before: 2023-05-31T09:47:43
|_Not valid after:  2024-05-30T09:47:43
|_ssl-date: 2023-06-02T09:57:10+00:00; +10s from scanner time.
3389/tcp  open  ms-wbt-server Microsoft Terminal Services
| ssl-cert: Subject: commonName=winterfell.north.sevenkingdoms.local
| Not valid before: 2023-05-30T06:09:19
|_Not valid after:  2023-11-29T06:09:19
|_ssl-date: 2023-06-02T09:57:10+00:00; +10s from scanner time.
| rdp-ntlm-info: 
|   Target_Name: NORTH
|   NetBIOS_Domain_Name: NORTH
|   NetBIOS_Computer_Name: WINTERFELL
|   DNS_Domain_Name: north.sevenkingdoms.local
|   DNS_Computer_Name: winterfell.north.sevenkingdoms.local
|   DNS_Tree_Name: sevenkingdoms.local
|   Product_Version: 10.0.17763
|_  System_Time: 2023-06-02T09:56:28+00:00
5985/tcp  open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
5986/tcp  open  ssl/http      Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_ssl-date: 2023-06-02T09:57:10+00:00; +10s from scanner time.
| tls-alpn: 
|_  http/1.1
|_http-server-header: Microsoft-HTTPAPI/2.0
| ssl-cert: Subject: commonName=VAGRANT
| Subject Alternative Name: DNS:VAGRANT, DNS:vagrant
| Not valid before: 2023-05-29T19:33:15
|_Not valid after:  2026-05-28T19:33:15
9389/tcp  open  mc-nmf        .NET Message Framing
49668/tcp open  msrpc         Microsoft Windows RPC
49670/tcp open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
49671/tcp open  msrpc         Microsoft Windows RPC
49675/tcp open  msrpc         Microsoft Windows RPC
49678/tcp open  msrpc         Microsoft Windows RPC
49709/tcp open  msrpc         Microsoft Windows RPC
63300/tcp open  msrpc         Microsoft Windows RPC
Service Info: Host: WINTERFELL; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
|_nbstat: NetBIOS name: WINTERFELL, NetBIOS user: <unknown>, NetBIOS MAC: 080027341fc8 (Oracle VirtualBox virtual NIC)
| smb2-security-mode: 
|   311: 
|_    Message signing enabled and required
|_clock-skew: mean: 9s, deviation: 0s, median: 9s
| smb2-time: 
|   date: 2023-06-02T09:56:25
|_  start_date: N/A

Nmap scan report for essos.local (192.168.56.12)
Host is up (0.0022s latency).
Not shown: 65513 filtered tcp ports (no-response)
PORT      STATE SERVICE       VERSION
53/tcp    open  domain        Simple DNS Plus
88/tcp    open  kerberos-sec  Microsoft Windows Kerberos (server time: 2023-06-02 09:54:45Z)
135/tcp   open  msrpc         Microsoft Windows RPC
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp   open  ldap          Microsoft Windows Active Directory LDAP (Domain: essos.local, Site: Default-First-Site-Name)
|_ssl-date: 2023-06-02T09:57:10+00:00; +10s from scanner time.
| ssl-cert: Subject: commonName=meereen.essos.local
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1::<unsupported>, DNS:meereen.essos.local
| Not valid before: 2023-05-31T06:37:56
|_Not valid after:  2024-05-30T06:37:56
445/tcp   open  microsoft-ds  Windows Server 2016 Standard Evaluation 14393 microsoft-ds (workgroup: ESSOS)
464/tcp   open  kpasswd5?
593/tcp   open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp   open  ssl/ldap      Microsoft Windows Active Directory LDAP (Domain: essos.local, Site: Default-First-Site-Name)
| ssl-cert: Subject: commonName=meereen.essos.local
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1::<unsupported>, DNS:meereen.essos.local
| Not valid before: 2023-05-31T06:37:56
|_Not valid after:  2024-05-30T06:37:56
|_ssl-date: 2023-06-02T09:57:10+00:00; +10s from scanner time.
3268/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: essos.local, Site: Default-First-Site-Name)
|_ssl-date: 2023-06-02T09:57:10+00:00; +10s from scanner time.
| ssl-cert: Subject: commonName=meereen.essos.local
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1::<unsupported>, DNS:meereen.essos.local
| Not valid before: 2023-05-31T06:37:56
|_Not valid after:  2024-05-30T06:37:56
3269/tcp  open  ssl/ldap      Microsoft Windows Active Directory LDAP (Domain: essos.local, Site: Default-First-Site-Name)
|_ssl-date: 2023-06-02T09:57:10+00:00; +10s from scanner time.
| ssl-cert: Subject: commonName=meereen.essos.local
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1::<unsupported>, DNS:meereen.essos.local
| Not valid before: 2023-05-31T06:37:56
|_Not valid after:  2024-05-30T06:37:56
3389/tcp  open  ms-wbt-server Microsoft Terminal Services
| ssl-cert: Subject: commonName=meereen.essos.local
| Not valid before: 2023-05-30T05:54:18
|_Not valid after:  2023-11-29T05:54:18
|_ssl-date: 2023-06-02T09:57:10+00:00; +10s from scanner time.
5985/tcp  open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
5986/tcp  open  ssl/http      Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_ssl-date: 2023-06-02T09:57:10+00:00; +10s from scanner time.
| ssl-cert: Subject: commonName=VAGRANT
| Subject Alternative Name: DNS:VAGRANT, DNS:vagrant
| Not valid before: 2023-05-29T19:36:59
|_Not valid after:  2026-05-28T19:36:59
| tls-alpn: 
|   h2
|_  http/1.1
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
9389/tcp  open  mc-nmf        .NET Message Framing
49666/tcp open  msrpc         Microsoft Windows RPC
49667/tcp open  msrpc         Microsoft Windows RPC
49669/tcp open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
49670/tcp open  msrpc         Microsoft Windows RPC
49672/tcp open  msrpc         Microsoft Windows RPC
49685/tcp open  msrpc         Microsoft Windows RPC
60199/tcp open  msrpc         Microsoft Windows RPC
Service Info: Host: MEEREEN; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-time: 
|   date: 2023-06-02T09:56:26
|_  start_date: 2023-06-02T00:35:27
|_clock-skew: mean: 46m50s, deviation: 2h20m02s, median: 9s
| smb-os-discovery: 
|   OS: Windows Server 2016 Standard Evaluation 14393 (Windows Server 2016 Standard Evaluation 6.3)
|   Computer name: meereen
|   NetBIOS computer name: MEEREEN\x00
|   Domain name: essos.local
|   Forest name: essos.local
|   FQDN: meereen.essos.local
|_  System time: 2023-06-02T02:56:29-07:00
|_nbstat: NetBIOS name: MEEREEN, NetBIOS user: <unknown>, NetBIOS MAC: 080027b0403a (Oracle VirtualBox virtual NIC)
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: required
| smb2-security-mode: 
|   311: 
|_    Message signing enabled and required

Nmap scan report for castelblack.north.sevenkingdoms.local (192.168.56.22)
Host is up (0.0025s latency).
Not shown: 65526 filtered tcp ports (no-response)
PORT      STATE SERVICE       VERSION
80/tcp    open  http          Microsoft IIS httpd 10.0
|_http-server-header: Microsoft-IIS/10.0
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-title: Site doesn't have a title (text/html).
135/tcp   open  msrpc         Microsoft Windows RPC
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds?
1433/tcp  open  ms-sql-s      Microsoft SQL Server 2019 15.00.2000.00; RTM
| ms-sql-ntlm-info: 
|   192.168.56.22:1433: 
|     Target_Name: NORTH
|     NetBIOS_Domain_Name: NORTH
|     NetBIOS_Computer_Name: CASTELBLACK
|     DNS_Domain_Name: north.sevenkingdoms.local
|     DNS_Computer_Name: castelblack.north.sevenkingdoms.local
|     DNS_Tree_Name: sevenkingdoms.local
|_    Product_Version: 10.0.17763
| ms-sql-info: 
|   192.168.56.22:1433: 
|     Version: 
|       name: Microsoft SQL Server 2019 RTM
|       number: 15.00.2000.00
|       Product: Microsoft SQL Server 2019
|       Service pack level: RTM
|       Post-SP patches applied: false
|_    TCP port: 1433
|_ssl-date: 2023-06-02T09:57:10+00:00; +10s from scanner time.
| ssl-cert: Subject: commonName=SSL_Self_Signed_Fallback
| Not valid before: 2023-06-02T00:36:44
|_Not valid after:  2053-06-02T00:36:44
3389/tcp  open  ms-wbt-server Microsoft Terminal Services
| ssl-cert: Subject: commonName=castelblack.north.sevenkingdoms.local
| Not valid before: 2023-05-30T06:19:24
|_Not valid after:  2023-11-29T06:19:24
|_ssl-date: 2023-06-02T09:57:10+00:00; +10s from scanner time.
5985/tcp  open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
5986/tcp  open  ssl/http      Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
|_ssl-date: 2023-06-02T09:57:10+00:00; +10s from scanner time.
| tls-alpn: 
|_  http/1.1
| ssl-cert: Subject: commonName=VAGRANT
| Subject Alternative Name: DNS:VAGRANT, DNS:vagrant
| Not valid before: 2023-05-29T19:41:29
|_Not valid after:  2026-05-28T19:41:29
49682/tcp open  msrpc         Microsoft Windows RPC
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-time: 
|   date: 2023-06-02T09:56:29
|_  start_date: N/A
|_nbstat: NetBIOS name: CASTELBLACK, NetBIOS user: <unknown>, NetBIOS MAC: 080027415a28 (Oracle VirtualBox virtual NIC)
|_clock-skew: mean: 9s, deviation: 0s, median: 9s
| smb2-security-mode: 
|   311: 
|_    Message signing enabled but not required

Nmap scan report for braavos.essos.local (192.168.56.23)
Host is up (0.0056s latency).
Not shown: 65524 filtered tcp ports (no-response)
PORT      STATE SERVICE       VERSION
80/tcp    open  http          Microsoft IIS httpd 10.0
|_http-title: IIS Windows Server
|_http-server-header: Microsoft-IIS/10.0
| http-methods: 
|_  Potentially risky methods: TRACE
135/tcp   open  msrpc         Microsoft Windows RPC
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds  Windows Server 2016 Standard Evaluation 14393 microsoft-ds
1433/tcp  open  ms-sql-s      Microsoft SQL Server 2019 15.00.2000.00; RTM
|_ssl-date: 2023-06-02T10:00:51+00:00; +10s from scanner time.
| ssl-cert: Subject: commonName=SSL_Self_Signed_Fallback
| Not valid before: 2023-06-02T00:37:20
|_Not valid after:  2053-06-02T00:37:20
| ms-sql-info: 
|   192.168.56.23:1433: 
|     Version: 
|       name: Microsoft SQL Server 2019 RTM
|       number: 15.00.2000.00
|       Product: Microsoft SQL Server 2019
|       Service pack level: RTM
|       Post-SP patches applied: false
|_    TCP port: 1433
| ms-sql-ntlm-info: 
|   192.168.56.23:1433: 
|     Target_Name: ESSOS
|     NetBIOS_Domain_Name: ESSOS
|     NetBIOS_Computer_Name: BRAAVOS
|     DNS_Domain_Name: essos.local
|     DNS_Computer_Name: braavos.essos.local
|     DNS_Tree_Name: essos.local
|_    Product_Version: 10.0.14393
3389/tcp  open  ms-wbt-server Microsoft Terminal Services
| ssl-cert: Subject: commonName=braavos.essos.local
| Not valid before: 2023-05-30T06:19:19
|_Not valid after:  2023-11-29T06:19:19
|_ssl-date: 2023-06-02T10:00:51+00:00; +10s from scanner time.
| rdp-ntlm-info: 
|   Target_Name: ESSOS
|   NetBIOS_Domain_Name: ESSOS
|   NetBIOS_Computer_Name: BRAAVOS
|   DNS_Domain_Name: essos.local
|   DNS_Computer_Name: braavos.essos.local
|   DNS_Tree_Name: essos.local
|   Product_Version: 10.0.14393
|_  System_Time: 2023-06-02T10:00:08+00:00
5985/tcp  open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
5986/tcp  open  ssl/http      Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
| tls-alpn: 
|   h2
|_  http/1.1
|_ssl-date: 2023-06-02T10:00:51+00:00; +10s from scanner time.
| ssl-cert: Subject: commonName=VAGRANT
| Subject Alternative Name: DNS:VAGRANT, DNS:vagrant
| Not valid before: 2023-05-29T19:46:16
|_Not valid after:  2026-05-28T19:46:16
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
49683/tcp open  msrpc         Microsoft Windows RPC
49691/tcp open  msrpc         Microsoft Windows RPC
49778/tcp open  msrpc         Microsoft Windows RPC
Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows

Host script results:
|_clock-skew: mean: 52m39s, deviation: 2h28m29s, median: 9s
| smb-os-discovery: 
|   OS: Windows Server 2016 Standard Evaluation 14393 (Windows Server 2016 Standard Evaluation 6.3)
|   Computer name: braavos
|   NetBIOS computer name: BRAAVOS\x00
|   Domain name: essos.local
|   Forest name: essos.local
|   FQDN: braavos.essos.local
|_  System time: 2023-06-02T03:00:08-07:00
| smb2-security-mode: 
|   311: 
|_    Message signing enabled but not required
|_nbstat: NetBIOS name: BRAAVOS, NetBIOS user: <unknown>, NetBIOS MAC: 080027fe0441 (Oracle VirtualBox virtual NIC)
| smb2-time: 
|   date: 2023-06-02T10:00:08
|_  start_date: 2023-06-02T00:37:10
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)

Post-scan script results:
| clock-skew: 
|   9s: 
|     192.168.56.10 (sevenkingdoms.local)
|     192.168.56.22 (castelblack.north.sevenkingdoms.local)
|     192.168.56.11 (winterfell.north.sevenkingdoms.local)
|     192.168.56.12 (essos.local)
|_    192.168.56.23 (braavos.essos.local)
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Fri Jun  2 06:00:41 2023 -- 5 IP addresses (5 hosts up) scanned in 615.07 seconds

</code></pre>
</details>

