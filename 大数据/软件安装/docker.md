参考官网： https://docs.docker.com/engine/install/ubuntu/#prerequisites
# 卸载旧版本
我这边第一次安装所以没有验证
```bash
for pkg in docker.io docker-doc docker-compose docker-compose-v2 podman-docker containerd runc; do sudo apt-get remove $pkg; done
```
images, containers, volumes, and networks stored in `/var/lib/docker/` aren't **automatically removed** when you uninstall Docker. If you want to start with a clean installation, and prefer to clean up any existing data, read the [uninstall Docker Engine](https://docs.docker.com/engine/install/ubuntu/#uninstall-docker-engine) section.

# 安装方法
有多种安装方法。这里我选择第二种：从apt仓库安装
You can install Docker Engine in different ways, depending on your needs:

- Docker Engine comes bundled with [Docker Desktop for Linux](https://docs.docker.com/desktop/install/linux-install/). This is the easiest and quickest way to get started.
    
- Set up and install Docker Engine from [Docker's `apt` repository](https://docs.docker.com/engine/install/ubuntu/#install-using-the-repository).
    
- [Install it manually](https://docs.docker.com/engine/install/ubuntu/#install-from-a-package) and manage upgrades manually.
    
- Use a [convenience script](https://docs.docker.com/engine/install/ubuntu/#install-using-the-convenience-script). Only recommended for testing and development environments.

# 使用apt 仓库安装

## 配置国内镜像仓库

由于官网示例的镜像比较慢，我改成了阿里云镜像

```
# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl gnupg
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://mirrors.aliyun.com/docker-ce/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg

# Add the repository to Apt sources:
echo \
  "deb [arch="$(dpkg --print-architecture)" signed-by=/etc/apt/keyrings/docker.gpg] https://mirrors.aliyun.com/docker-ce/linux/ubuntu \
  "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt-get update
```

## apt安装

```console
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```
## 测试hello-world
```shell
sudo docker run hello-world
```

