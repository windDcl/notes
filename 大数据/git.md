## 解决无法clone github的项目
如果能够访问github网页的项目，但是无法git clone。大概率是因为需要配置一下github的域名和ip映射
首先查询一下当前github.com的ip地址。然后
```
vi /etc/host
# 将ip地址写进去
20.205.243.166 github.com
```
然后git clone再试试，成功解决

## 报错
![[Pasted image 20230813221809.png]]
```
git config --global http.proxy http://127.0.0.1:7890
```
解决