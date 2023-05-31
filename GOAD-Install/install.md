# GOAD搭建指南

## 成功案例

建议仔细阅读[官方文档](https://github.com/Orange-Cyberdefense/GOAD)和u21h2大佬[文章](https://qusec.cn/posts/G0AD/)

### 安装历程

本文仅供参考,推荐Ubuntu系统安装,服务器配置如下

```bash
            .-/+oossssoo+/-.               ctf@gh-virtual-machine 
        `:+ssssssssssssssssss+:`           ---------------------- 
      -+ssssssssssssssssssyyssss+-         OS: Ubuntu 22.04.2 LTS x86_64 
    .ossssssssssssssssssdMMMNysssso.       Host: VMware Virtual Platform None 
   /ssssssssssshdmmNNmmyNMMMMhssssss/      Kernel: 5.15.0-74-generic 
  +ssssssssshmydMMMMMMMNddddyssssssss+     Uptime: 9 hours, 7 mins 
 /sssssssshNMMMyhhyyyyhmNMMMNhssssssss/    Packages: 2754 (dpkg), 13 (snap) 
.ssssssssdMMMNhsssssssssshNMMMdssssssss.   Shell: zsh 5.8.1 
+sssshhhyNMMNyssssssssssssyNMMMysssssss+   Resolution: 800x600 
ossyNMMMNyMMhsssssssssssssshmmmhssssssso   Terminal: /dev/pts/4 
ossyNMMMNyMMhsssssssssssssshmmmhssssssso   CPU: Intel Xeon Silver 4210 (20) @ 2.194GHz 
+sssshhhyNMMNyssssssssssssyNMMMysssssss+   GPU: 00:0f.0 VMware SVGA II Adapter 
.ssssssssdMMMNhsssssssssshNMMMdssssssss.   Memory: 22997MiB / 64393MiB 
 /sssssssshNMMMyhhyyyyhdNMMMNhssssssss/
  +sssssssssdmydMMMMMMMMddddyssssssss+                             
   /ssssssssssshdmNNNNmyNMMMMhssssss/
    .ossssssssssssssssssdMMMNysssso.
      -+sssssssssssssssssyyyssss+-
        `:+ssssssssssssssssss+:`
            .-/+oossssoo+/-.
```

用国外大佬的[安装脚本](https://github.com/lkarlslund/deploy-goad.git),十分方便

```bash
sudo su (give it your password)
apt install git
git clone https://github.com/lkarlslund/deploy-goad
cd deploy-goad
./deploy-goad.sh #注意根据系统版本修改脚本
cd /opt/goad/ansible
ansible-playbook main.yml
```

如果出现失败,可以多运行几次ansible-playbook main.yml或者按顺序逐个执行文件,直到无报错

```bash
 ansible-playbook build.yml        # Install stuff and prepare vm
 ansible-playbook ad-servers.yml   # create main domains, child domain and enroll servers
 ansible-playbook ad-trusts.yml    # create the trust relationships
 ansible-playbook ad-data.yml      # import the ad datas : users/groups...
 ansible-playbook laps.yml
 ansible-playbook ad-relations.yml # set the rights and the group domains relations
 ansible-playbook adcs.yml         # Install ADCS on essos
 ansible-playbook ad-acl.yml       # set the ACE/ACL
 ansible-playbook servers.yml      # Install IIS and MSSQL
 ansible-playbook security.yml     # Configure some securities (adjust av enable/disable)
 ansible-playbook vulnerabilities.yml        # Configure some vulnerabilities
```

### 测试与备份

使用socks代理,进行测试

服务器端启动 chisel 服务端

```bash
ubuntu@ctf:~$./chisel server -v -p port --socks5
```

攻击机进行连接

```bash
arch@ctf:~$./chisel client host:port socks
arch@ctf:~$cat /etc/proxychains.conf
......
[ProxyList]
# add proxy here ...
# meanwile
# defaults set to "tor"
socks5  127.0.0.1 1080
```

使用proxychains代理crackmapexec测试

```bash
sudo proxychains -q poetry run crackmapexec smb  192.168.56.1/24        
SMB         192.168.56.11   445    WINTERFELL       [*] Windows 10.0 Build 17763 x64 (name:WINTERFELL) (domain:north.sevenkingdoms.local) (signing:True) (SMBv1:False)
SMB         192.168.56.12   445    MEEREEN          [*] Windows Server 2016 Standard Evaluation 14393 x64 (name:MEEREEN) (domain:essos.local) (signing:True) (SMBv1:True)
SMB         192.168.56.22   445    CASTELBLACK      [*] Windows 10.0 Build 17763 x64 (name:CASTELBLACK) (domain:north.sevenkingdoms.local) (signing:False) (SMBv1:False)
SMB         192.168.56.10   445    KINGSLANDING     [*] Windows 10.0 Build 17763 x64 (name:KINGSLANDING) (domain:sevenkingdoms.local) (signing:True) (SMBv1:False)
SMB         192.168.56.23   445    BRAAVOS          [*] Windows Server 2016 Standard Evaluation 14393 x64 (name:BRAAVOS) (domain:essos.local) (signing:False) (SMBv1:True)
```

备份

```bash
ubuntu@ctf:~$vagrant snapshot save initial-setup
```

### 失败经验

在服务器上按照[官方文档](https://github.com/Orange-Cyberdefense/GOAD)中的用docker创建并运行ansible,总是会停止在某个阶段,原因未知

```bash
sudo docker build -t goadansible .
sudo docker run -ti --rm --network host -h goadansible -v $(pwd):/goad -w /goad/ansible goadansible ansible-playbook -i ../ad/sevenkingdoms.local/inventory main.yml
```

## 失败案例

我的笔记本配置

```bash
 neofetch                                                                                           
                   -`                    joker@kali 
                  .o+`                   ---------- 
                 `ooo/                   OS: Arch Linux x86_64 
                `+oooo:                  Host: Dell G15 5511 
               `+oooooo:                 Kernel: 6.3.4-arch1-1 
               -+oooooo+:                Uptime: 3 hours, 5 mins 
             `/:-:++oooo+:               Packages: 1359 (pacman), 7 (flatpak) 
            `/++++/+++++++:              Shell: zsh 5.9 
           `/++++++++++++++:             Resolution: 2560x1440 
          `/+++ooooooooooooo/`           DE: GNOME 44.1 
         ./ooosssso++osssssso+`          WM: Mutter 
        .oossssso-````/ossssss+`         WM Theme: Orchis-Dark-Compact 
       -osssssso.      :ssssssso.        Theme: Orchis-Dark [GTK2/3] 
      :osssssss/        osssso+++.       Icons: Tela-black-dark [GTK2/3] 
     /ossssssss/        +ssssooo/-       Terminal: terminator 
   `/ossssso+/:-        -:/+osssso+-     CPU: 11th Gen Intel i7-11800H (16) @ 4.600GHz 
  `+sso+:-`                 `.-/+oso:    GPU: Intel TigerLake-H GT1 [UHD Graphics] 
 `++:.                           `-/+/   GPU: NVIDIA GeForce RTX 3060 Mobile / Max-Q 
 .`                                 `/   Memory: 28445MiB / 31845MiB 
```

所以修改Vagrantfile文件46、47配置为

```txt
v.memory = 2000
v.cpus = 1
```

```bash
vagrant up --provision #最好添加
```

```bash
docker build -t goadansible .
sudo  ansible-playbook main.yml
```

在运行ansible,一堆报错,多次尝试还是失败,推测可能是系统原因或者配置不够???

正在尝试用学校服务器重新搭建
