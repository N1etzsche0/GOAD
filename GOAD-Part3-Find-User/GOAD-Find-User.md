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
获取了用户信息,发现samwell.tarly备注密码是Heartsbane

在开始爆破前,检索密码策略
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
SMB         192.168.56.11   445    WINTERFELL       	Domain Refuse Password Change: 0
SMB         192.168.56.11   445    WINTERFELL       	Domain Password Store Cleartext: 0
SMB         192.168.56.11   445    WINTERFELL       	Domain Password Lockout Admins: 0
SMB         192.168.56.11   445    WINTERFELL       	Domain Password No Clear Change: 0
SMB         192.168.56.11   445    WINTERFELL       	Domain Password No Anon Change: 0
SMB         192.168.56.11   445    WINTERFELL       	Domain Password Complex: 0
SMB         192.168.56.11   445    WINTERFELL       
SMB         192.168.56.11   445    WINTERFELL       Minimum password age: 1 day 4 minutes 
SMB         192.168.56.11   445    WINTERFELL       Reset Account Lockout Counter: 5 minutes 
SMB         192.168.56.11   445    WINTERFELL       Locked Account Duration: 5 minutes 
SMB         192.168.56.11   445    WINTERFELL       Account Lockout Threshold: 5
SMB         192.168.56.11   445    WINTERFELL       Forced Log off Time: Not Set
```
密码策略设置是,如果在 5 分钟内失败 5 次，我们将锁定帐户 5 分钟
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

index: 0x189b RID: 0x456 acb: 0x00000210 Account: arya.stark	Name: (null)	Desc: Arya Stark
index: 0x18a7 RID: 0x45b acb: 0x00010210 Account: brandon.stark	Name: (null)	Desc: Brandon Stark
index: 0x16fa RID: 0x1f5 acb: 0x00000215 Account: Guest	Name: (null)	Desc: Built-in account for guest access to the computer/domain
index: 0x18ab RID: 0x45d acb: 0x00000210 Account: hodor	Name: (null)	Desc: Brainless Giant
index: 0x18b1 RID: 0x460 acb: 0x00000210 Account: jeor.mormont	Name: (null)	Desc: Jeor Mormont
index: 0x18ad RID: 0x45e acb: 0x00040210 Account: jon.snow	Name: (null)	Desc: Jon Snow
index: 0x18a9 RID: 0x45c acb: 0x00000210 Account: rickon.stark	Name: (null)	Desc: Rickon Stark
index: 0x18af RID: 0x45f acb: 0x00000210 Account: samwell.tarly	Name: (null)	Desc: Samwell Tarly (Password : Heartsbane)
index: 0x18a6 RID: 0x45a acb: 0x00000210 Account: sansa.stark	Name: (null)	Desc: Sansa Stark
index: 0x18b3 RID: 0x461 acb: 0x00000210 Account: sql_svc	Name: (null)	Desc: sql service

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
