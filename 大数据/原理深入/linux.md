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