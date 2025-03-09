---
title: Linux From Scratch构建记录
date: 2025-03-07 21:28:26
tags:
 - Linux
---

# Linux From Scratch

Version 12.3

状态：正在进行

## 2. Preparing the Host System

### 2.2 Host System Requirements

> Host系统：Ubuntu 24.04.2 LTS

使用教程提供的脚本检查了下依赖包，发现有些依赖未装上

![](2_2_requirements_check.png)

使用`apt`安装以下包

```shell
sudo apt install build-essential
sudo apt install bison
sudo apt install gawk
sudo apt install texinfo
```

检查脚本发现`sh`不是指向`bash`，当前指向的是dash

```
lrwxrwxrwx 1 root root 4 Mar 31  2024 /bin/sh -> dash*
```

使用ln命令修改指向

```shell
sudo ln -sf /bin/bash /bin/sh
```

安装完依赖后效果如下

![](2_2_requirements.png)

### 2.3. Building LFS in Stages

先Mark住，重启系统后要恢复的操作

### 2.4 Creating a New Partition

#### VirtualBox创建硬盘

LFS教程指示需要创建一个分区，当前Host系统是运行在VirtualBox上，因此这里创建一个新的硬盘用于新分区创建

* 在设置`存储`中创建新的硬盘，然后注册进来

![](2_4_new_hd.png)

* 硬盘大小设置为30GB（LFS教程建议）

![](2_4_new_sata.png)

#### 创建分区

* 列出系统已识别的硬盘

```shell
luowanqian@LFS:~$ lsblk
NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
loop0    7:0    0 73.9M  1 loop /snap/core22/1748
loop1    7:1    0    4K  1 loop /snap/bare/5
loop2    7:2    0 11.1M  1 loop /snap/firmware-updater/167
loop3    7:3    0  516M  1 loop /snap/gnome-42-2204/202
loop4    7:4    0  258M  1 loop /snap/firefox/5751
loop5    7:5    0 91.7M  1 loop /snap/gtk-common-themes/1535
loop6    7:6    0 10.8M  1 loop /snap/snap-store/1248
loop7    7:7    0 44.4M  1 loop /snap/snapd/23771
loop8    7:8    0 44.4M  1 loop /snap/snapd/23545
loop9    7:9    0  568K  1 loop /snap/snapd-desktop-integration/253
sda      8:0    0   50G  0 disk
├─sda1   8:1    0    1M  0 part
└─sda2   8:2    0   50G  0 part /
sdb      8:16   0   30G  0 disk
sr0     11:0    1 1024M  0 rom
```

* `fdisk`查看硬盘`/dev/sdb`信息，当前空间大小为30GB

```shell
luowanqian@LFS:~$ sudo fdisk -l /dev/sdb
Disk /dev/sdb: 30 GiB, 32212254720 bytes, 62914560 sectors
Disk model: VBOX HARDDISK
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
```

* 创建分区，当前只创建一个分区

```shell
luowanqian@LFS:~$ sudo fdisk /dev/sdb

Welcome to fdisk (util-linux 2.39.3).
Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.

Device does not contain a recognized partition table.
Created a new DOS (MBR) disklabel with disk identifier 0x4dd1e2d5.

Command (m for help): p
Disk /dev/sdb: 30 GiB, 32212254720 bytes, 62914560 sectors
Disk model: VBOX HARDDISK
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x4dd1e2d5

Command (m for help): q

luowanqian@LFS:~$
luowanqian@LFS:~$
luowanqian@LFS:~$ sudo fdisk /dev/sdb

Welcome to fdisk (util-linux 2.39.3).
Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.

Device does not contain a recognized partition table.
Created a new DOS (MBR) disklabel with disk identifier 0x7fdee095.

Command (m for help): g
Created a new GPT disklabel (GUID: 44B24C46-37DF-4C7C-9C00-ACD7C1FED25F).

Command (m for help): n
Partition number (1-128, default 1):
First sector (2048-62914526, default 2048):
Last sector, +/-sectors or +/-size{K,M,G,T,P} (2048-62914526, default 62912511):

Created a new partition 1 of type 'Linux filesystem' and of size 30 GiB.

Command (m for help): p
Disk /dev/sdb: 30 GiB, 32212254720 bytes, 62914560 sectors
Disk model: VBOX HARDDISK
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: gpt
Disk identifier: 44B24C46-37DF-4C7C-9C00-ACD7C1FED25F

Device     Start      End  Sectors Size Type
/dev/sdb1   2048 62912511 62910464  30G Linux filesystem

Command (m for help): w
The partition table has been altered.
Calling ioctl() to re-read partition table.
Syncing disks.
```

查看`/dev/sdb`的分区情况，可以看到已有一个分区

```shell
luowanqian@LFS:~$ sudo fdisk -l /dev/sdb
Disk /dev/sdb: 30 GiB, 32212254720 bytes, 62914560 sectors
Disk model: VBOX HARDDISK
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: gpt
Disk identifier: 44B24C46-37DF-4C7C-9C00-ACD7C1FED25F

Device     Start      End  Sectors Size Type
/dev/sdb1   2048 62912511 62910464  30G Linux filesystem
```

### 2.5 Creating a File System on the Partition

* 在新分区`/dev/sdb1`上创建文件系统ext4

```
luowanqian@LFS:~/Documents/Code/LFS$ sudo mkfs -v -t ext4 /dev/sdb1
mke2fs 1.47.0 (5-Feb-2023)
fs_types for mke2fs.conf resolution: 'ext4'
Filesystem label=
OS type: Linux
Block size=4096 (log=2)
Fragment size=4096 (log=2)
Stride=0 blocks, Stripe width=0 blocks
1966080 inodes, 7863808 blocks
393190 blocks (5.00%) reserved for the super user
First data block=0
Maximum filesystem blocks=2155872256
240 block groups
32768 blocks per group, 32768 fragments per group
8192 inodes per group
Filesystem UUID: e24fc955-4016-4b3c-9ef3-712469fb2434
Superblock backups stored on blocks:
        32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632, 2654208,
        4096000

Allocating group tables: done
Writing inode tables: done
Creating journal (32768 blocks): done
Writing superblocks and filesystem accounting information: done
```

### 2.6 Setting the $LFS Variable and the Umask

为了系统重启后能够设置$LFS环境变量和umask，需要写入`.bash_profile`和`.bashrc`配置文件，按照教程说法，需要设置当前登录用户和root的配置文件

* 配置登录用户的bash

```shell
touch ~/.bash_profile

cat >> ~/.bash_profile << "EOF"
export LFS=/mnt/lfs
umask 022
EOF

cat >> ~/.bashrc << "EOF"

if [ -f ~/.bash_profile ]; then
    . ~/.bash_profile
fi
EOF

source ~/.bashrc
```

* 配置root用户的bash

```shell
sudo touch /root/.bash_profile

sudo bash -c 'cat >> /root/.bash_profile << "EOF"
export LFS=/mnt/lfs
umask 022
EOF'

sudo bash -c 'cat >> /root/.bashrc << "EOF"

if [ -f /root/.bash_profile ]; then
    . /root/.bash_profile
fi
EOF'
```

备注：使用sudo cat写入root配置文件需要用`bash -c`，参考 [sudo cat << EOF > File doesn't work, sudo su does](https://stackoverflow.com/questions/18836853/sudo-cat-eof-file-doesnt-work-sudo-su-does) 做法