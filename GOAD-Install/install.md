# GOAD搭建指南

## 安装指令

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
sudo docker run -ti --rm --network host -h goadansible -v $(pwd):/goad -w /goad/ansible goadansible ansible-playbook main.yml
```

进行ansible的时候,一堆报错,多次尝试还是失败,可能是系统原因或者配置不够???

正在尝试用学校服务器搭建
