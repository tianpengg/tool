##使用Jenkins 自动发布.NET CORE 项目

### 安装Jenkins
1. 安装docker

2. 在服务器创建 Jenkins 工作目录 /var/jenkins_mount 并授权权限，启容器时将 jenkins 容器目录挂载到这个目录上
``` bash
mkdir -p /var/jenkins_mount
chmod 777 /var/jenkins_mount
```
3. 启动容器
``` docker
docker run --restart=always -d -p 10240:8080 -p 10241:50000 -v /var/jenkins_mount:/var/jenkins_home -v /etc/localtime:/etc/localtime --name myjenkins --privileged=true jenkins/jenkins:latest
```
4. 检查容器
```
docker ps
```
5. 设置镜像源
进入宿主机目录 /var/jenkins_mount/，编辑文件 hudson.model.UpdateCenter.xml
``` bash
cd /var/jenkins_mount/
vi  hudson.model.UpdateCenter.xml
```
将url内容修改为https://mirrors.tuna.tsinghua.edu.cn/jenkins/updates/update-center.json（清华大学官方镜像）

6. 登录Jenkins
浏览器输入 http://192.168.200.167:10240/ ，登录 Jenkins，第一次登录需要输入管理员密码。此密码在宿主机 /var/jenkins_mount/secrets/initialAdminPassword 这个文件内。登录成功后回让新建账号密码即可。

``` bash
cat /var/jenkins_mount/secrets/initialAdminPassword
```
### 安装.NET CORE环境
因为jenkins的docker版本本身没有 .NET CORE的环境，所以我们需要先自己动手制作下包含dotnet环境的jenkins Docker Container
1. 进去jenkins的容器
```
docker exec -u -it df7b /bin/bash
```
2. 替换源
```shell
mv /etc/apt/sources.list /etc/apt/sources.list.bak
    echo "deb http://mirrors.aliyun.com/debian/ bullseye main non-free contrib" >> /etc/apt/sources.list
    echo "deb-src http://mirrors.aliyun.com/debian/ bullseye main non-free contrib" >> /etc/apt/sources.list
    echo "deb http://mirrors.aliyun.com/debian-security/ bullseye-security main" >> /etc/apt/sources.list
    echo "deb-src http://mirrors.aliyun.com/debian-security/ bullseye-security main" >> /etc/apt/sources.list
    echo "deb http://mirrors.aliyun.com/debian/ bullseye-updates main non-free contrib" >> /etc/apt/sources.list
    echo "deb-src http://mirrors.aliyun.com/debian/ bullseye-updates main non-free contrib" >> /etc/apt/sources.list
    echo "deb http://mirrors.aliyun.com/debian/ bullseye-backports main non-free contrib" >> /etc/apt/sources.list
    echo "deb-src http://mirrors.aliyun.com/debian/ bullseye-backports main non-free contrib" >> /etc/apt/sources.list

apt-get update
```
由于本机是阿里云的，这里使用阿里云的镜像

3. 安装.NET CORE 
参考微软官网文档
https://docs.microsoft.com/zh-cn/dotnet/core/install/linux-debian
``` bash
wget https://packages.microsoft.com/config/debian/11/packages-microsoft-prod.deb -O packages-microsoft-prod.deb
sudo dpkg -i packages-microsoft-prod.deb
rm packages-microsoft-prod.deb
sudo apt-get update; \
  sudo apt-get install -y apt-transport-https && \
  sudo apt-get update && \
  sudo apt-get install -y dotnet-sdk-3.1

```

## 使用Jenkins自动发布.NET CORE 项目

剩下的就自己根据自己的需求来发布了
1. 配置git
2. 配置ssh
3. 创建jenkins发布项目
4. 编写shell脚本
5. 发布！