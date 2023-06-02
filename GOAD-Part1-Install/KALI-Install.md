# KALI攻击机搭建指南(可选)

## 案例

### 安装指令

本文参考[KALI官方教程](https://www.kali.org/docs/virtualization/install-vagrant-guest-vm/)

```bash
vagrant init kalilinux/rolling
```

Vagrantfile配置如下

```config
Vagrant.configure("2") do |config|
  
  config.vm.box = "kalilinux/rolling"
  config.vm.network "forwarded_port", guest: 22, host: 3333
  config.vm.network "private_network", ip: "192.168.56.50"
  config.vm.provider "virtualbox" do |v|
    v.gui = false
    v.memory = "4096"
  end
  config.vm.provision "shell", inline: <<-EOF
    sudo apt update
    sudo apt install -y crowbar
  EOF
end
```

启动

```bash
vagrant up
```

可以使用本地3333端口进行登陆或者vagrant ssh

### 测试

```bash
cat /etc/hosts
```

```bash
192.168.56.10   sevenkingdoms.local kingslanding.sevenkingdoms.local kingslanding
192.168.56.11   winterfell.north.sevenkingdoms.local north.sevenkingdoms.local winterfell
192.168.56.12   essos.local meereen.essos.local meereen
192.168.56.22   castelblack.north.sevenkingdoms.local castelblack
192.168.56.23   braavos.essos.local braavos
```

```bash
crackmapexec smb 192.168.56.0/24
```

```bash
SMB         192.168.56.22   445    CASTELBLACK      [*] Windows 10.0 Build 17763 x64 (name:CASTELBLACK) (domain:north.sevenkingdoms.local) (signing:False) (SMBv1:False)
SMB         192.168.56.12   445    MEEREEN          [*] Windows Server 2016 Standard Evaluation 14393 x64 (name:MEEREEN) (domain:essos.local) (signing:True) (SMBv1:True)
SMB         192.168.56.10   445    KINGSLANDING     [*] Windows 10.0 Build 17763 x64 (name:KINGSLANDING) (domain:sevenkingdoms.local) (signing:True) (SMBv1:False)
SMB         192.168.56.23   445    BRAAVOS          [*] Windows Server 2016 Standard Evaluation 14393 x64 (name:BRAAVOS) (domain:essos.local) (signing:False) (SMBv1:True)
SMB         192.168.56.11   445    WINTERFELL       [*] Windows 10.0 Build 17763 x64 (name:WINTERFELL) (domain:north.sevenkingdoms.local) (signing:True) (SMBv1:False)
```

测试成功
