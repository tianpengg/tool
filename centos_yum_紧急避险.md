# CentOS YUM源 紧急避险 手册

因为官方不再维护CentOS 8的yum源，导致CentOS 8已经不能安装/更新软件包，需要手动进行更改源操作。因为网络上的办法千奇百怪，这里提供一种自己的办法

## 报错日志

```bash
错误：为 repo 'AppStream' 下载元数据失败 : Cannot prepare internal mirrorlist: No URLs in mirrorlist
```

## 解决方法

- 备份YUM源
	> cp -R /etc/yum.repos.d /etc/yum.repos.d.backup
- 清空YUM源配置（**危险操作，执行慎重！**）
	> rm -rf /etc/yum.repos.d/* 
- 下载阿里云的备用源配置
	> wget -O /etc/yum.repos.d/CentOS-Base.repo https://mirrors.aliyun.com/repo/Centos-vault-8.5.2111.repo
- 清空缓存并重建
	> yum clean all && yum makecache

完成上述步骤，会导致有部分基础的linux包还是找不到，比如`htop`, 需要再用**dnf**来重新修复一下

```bash
dnf -y install epel-release
dnf clean all
dnf makecache
```

使用

> dnf repolist

检查一下源配置，完美~

有些时候还是需要严谨一些的，否则会造成别人误解，是吧，某云。

以上。

## 附录

- [centos-vault mirrors](https://mirrors.aliyun.com/centos-vault/8.5.2111/BaseOS/x86_64/os/)
- [Aliyun CentOS 镜像文档](https://mirrors.aliyun.com/repo/Centos-vault-8.5.2111.repo)
- [CentOS8系统配置国内yum源](https://blog.csdn.net/turbock/article/details/111033892)
- [CentOS 8 EOL如何切换源？](https://help.aliyun.com/document_detail/405635.html)
- [查看CentOS版本信息](https://blog.csdn.net/benben0729/article/details/81873422)

## 挂尸体

[误导别人的，还给发短信的推广的那种](https://developer.aliyun.com/mirror/centos?&msctype=sms&mscareaid=cn&mscsiteid=cn&mscmsgid=8900122022301582729&&spm=a2c4i.26787838.zh-cnc.1&utm_content=g_1000323853&short_name=f1.K7EXZ)