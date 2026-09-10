本文档适用于X86_64架构CPU,Ubuntu 24.04系统安装脉冲星软件。安装Docker以及下载Docker镜像要求连入互联网。
### 安装Docker
##### 安装Docker引擎
```bash
sudo apt update
sudo apt install curl
curl -fsSL https://get.docker.com -o get-docker.sh  
sudo sh get-docker.sh
```
**以下三步用于拉取线上镜像和验证安装,如果通过云盘下载好了镜像可以省略**
##### 配置镜像下载地址
```bash
vim /etc/docker/daemon.json
```
按`i`进入输入模式,将下面的内容复制进去,`Esc`进入一般模式并`:wq`保存.
```json
{
  "registry-mirrors":[  
    "https://docker.m.daocloud.io",  
    "https://mirror.ccs.tencentyun.com"  
  ]
}
```
##### 重启Docker使配置生效
```bash
sudo systemctl daemon-reload  
sudo systemctl restart docker
```
##### 拉取在线镜像Hello World,验证安装
```bash
sudo docker run hello-world
```
若输出大段文字且带有**Hello from Docker!**,说明安装成功.
