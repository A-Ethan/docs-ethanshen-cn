# docker环境安装

## CentOS 7.8

> docker rpm包，放到对象存储去搞

```bash
# 下载
wget https://download.docker.com/linux/centos/7/x86_64/stable/Packages/docker-ce-18.09.2-3.el7.x86_64.rpm \
wget https://download.docker.com/linux/centos/7/x86_64/stable/Packages/docker-ce-cli-18.09.2-3.el7.x86_64.rpm \
wget https://download.docker.com/linux/centos/7/x86_64/stable/Packages/containerd.io-1.2.2-3.3.el7.x86_64.rpm
# 安装
yum install docker-ce-cli-18.09.2-3.el7.x86_64.rpm -y
yum install containerd.io-1.2.2-3.3.el7.x86_64.rpm -y
yum install docker-ce-18.09.2-3.el7.x86_64.rpm -y
# 启动
systemctl start docker
```
