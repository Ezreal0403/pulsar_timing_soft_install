<h1 align="center">脉冲星计时软件安装指南</h1>

>**作者**：Ezreal  
>**更新时间**：2026.9.7
本文档适用于**X86_64**架构CPU,**Ubuntu 24.04**系统安装脉冲星软件。安装Docker以及下载Docker镜像要求连入互联网。

### 下载镜像
打开百度网盘地址,密码为3eBd,下载.tar格式镜像文件.它不是压缩文件,不要用tar解压缩.
```
https://pan.baidu.com/s/1e7qt0MRBTyLUFgsqkvhziw?pwd=3eBd
```

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
运行此命令将当前用户加入docker用户组.
```bash
sudo usermod -aG docker $USER
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

### 加载镜像并创建容器
##### 镜像加载至Docker
首先需要将本地下载好的镜像加载到Docker的系统文件夹中,用于后续创建容器.`cd`到存放.tar格式镜像文件所在的路径下.执行
```bash
sudo docker load i $MYIMAGE.tar
```
执行此命令,确认$MYIMAGE是否被加载
```bash
sudo docker images
```
##### 创建容器
容器有必要的系统文件但是和宿主机共享内核,无法直接与硬件交互,所以不需要单独安装驱动,也必须借助宿主机挂载硬盘.  
由于容器中完全没有图形界面,需要借用宿主机来执行容器中的画图命令.此命令用于宿主机授权容器.  
```bash
xhost +local:root
```
下面正式创建容器.`-it`使用终端与容器中系统交互,`--net=host`让容器中系统共享宿主机的ip,端口和网卡,`--name`给容器命名,`-e DISPLAY=$DISPLAY -e PGPLOT_DEV=/xs -v /tmp/.X11-unix:/tmp/.X11-unix`用于调用宿主机的画图功能,`-v $DATA:/data -v /mnt/my_image:/mnt:rshared`设置了2个挂载点,在宿主机`$DATA`路径下的文件会自动被容器系统识别,但是临时挂载移动硬盘到容器中,必须在宿主机以/mnt/$MYIMAGE为挂载点挂载.**将`$NAME $DATA $MYIMAGE`替换为你想设置的名称或路径.**
```bash
sudo docker run -it --net=host --name $NAME -e DISPLAY=$DISPLAY -e PGPLOT_DEV=/xs -v /tmp/.X11-unix:/tmp/.X11-unix -v $DATA:/data -v /mnt/$MYIMAGE:/mnt:rshared $MYIMAGE bash
```

### Docker常用命令
以下提供几个常用Docker命令.进入容器使用软件前需要先运行容器.
运行容器``sudo docker start $NAME``  
停运容器``sudo docker stop $NAME``  
查看容器``sudo docker ps -a``  
删除容器``sudo docker rm $NAME``  
进入容器``sudo docker exec -it $NAME bash``  
退出容器,在容器中输入``exit``  
删除镜像``sudo docker rmi $MYIMAGE``
 
