## ubuntu命令
### 关闭防火墙
```
ufw disable
```
### 切换root并修改密码
```
sudo su

passwd root
```
### 修改root可以远程登录
```shell
在 Linux 中，要实现 root 免密登录，需要以下步骤：

1. 登录到服务器，并以 root 身份登录。
    
2. 打开 /etc/ssh/sshd_config 文件。
    
3. 寻找 PermitRootLogin 参数，并将其值改为 yes。
    
4. 保存文件并退出。
    
5. 重启 ssh 服务，使修改生效。
systemctl restart sshd
```
## ubuntu换apt源

注意这里的源需要加上-ports，因为我是arm架构的。非arm架构不加
```shell
vim /etc/apt/source.list

deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu-ports/ focal main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu-ports/ focal-updates main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu-ports/ focal-backports main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu-ports/ focal-security main restricted universe multiverse


apt update
```

## 给根目录扩容
ubuntu安装后。/目录下的容量远不及拥有的容量，这是因为lvm（逻辑分区）容量分配的量少。我们只需要分配给lvm分区的容量扩一下即可
参考： https://cloud.tencent.com/developer/article/1965711 
1、执行命令lvdisplay查看系统逻辑分区信息
![[Pasted image 20230901071213.png]]
2、执行命令fdisk -l查看磁盘信息
![[Pasted image 20230901071233.png]]
从上图可以看出磁盘大小(1T)远大于系统逻辑分区大小(200G)，知道了现在磁盘完全可以提升利用率，就可以直接进行扩容，不需要额外增加硬盘

3、执行命令lvextend -l +100%FREE /dev/ubuntu-vg/ubuntu-lv，注意后面的逻辑分区名字使用lvdisplay命令获取到的

4、执行 命令resize2fs /dev/ubuntu-vg/ubuntu-lv 刷新逻辑卷

5、执行命令df -h查看效果
![[Pasted image 20230901071252.png]]


## 重新分区
虚拟机增加硬盘的容量后，需要手动的划分分区和格式化，否则无法使用。具体的步骤：

1. 查看当前的磁盘情况
```
lsblk
```
![[Pasted image 20230712173840.png]]
2. 对 nvme01n1 分区

这里我使用gdisk而不是fdisk进行分区，因为那个做到最后有个报错。没办法解决。参考 https://blog.csdn.net/weixin_42118880/article/details/115013743

```
yum install gdisk

gdisk /dev/nvme01n1
输入n，然后直接默认执行
输入p查看分区是否正常。
最后w保存退出

然后必须reboot重启一下，才能生效
```
3. 重启后查看分区情况
```
lsblk
```
4. 格式化分区
```
mkfs.ext4 /dev/nvme01n1
```
5. 挂载分区
手动创建一个/test目录作为磁盘这个分区的挂载点
```
mkdir /test
mount /dev/nvme01n1 /test
lsblk 观察是否挂载成功
```
6. 设置重启自动挂载
上面的配置后，重启后，还需要手动挂载，需要设置下自动挂载
```
先重启  reboot
输入lsblk 再次确定硬盘名称，这里硬盘名称为“nvme01n1”

# 查看磁盘分区的UUID
blkid /dev/nvme01n1
得到uuid=545567aa-ab7d-4d7d-97f6-87b0ad3783f7

# 编辑这个文件将内容更新到最后一行
vi /etc/fstab 
UUID=ad0dadac-3f33-47dc-8290-159a8adacc15 /test ext4 defaults 0 0
```
7. 重启服务查看是是否正常
```
lsblk
```

参考：
https://blog.csdn.net/weixin_42118880/article/details/115013743
https://blog.csdn.net/qq_45664055/article/details/107516419


## 常用命令

```shell
# 查看/opt目录下所有的文件夹大小。
du -h -d 1 /opt 
```
![[Pasted image 20230917212733.png]]
