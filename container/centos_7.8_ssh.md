# CentOS 7.8 ssh

## 命令

```bash
# build 镜像
docker build -f Dockerfile -t=<image:tag>
# run 容器
docker run -d -p 22:22 -name centos7ssh -e PASSWD=youpassword <image:tag>
```

## entrypoint.sh

```shell
#!/bin/bash

pd="--not-used--"
if [ ! -z "$PASSWD" ];then
    pd=$PASSWD
fi

/bin/echo "root:$pd"|chpasswd

$@

/usr/sbin/sshd -D
```

## Dockerfile

```Dockerfile
FROM uhub.service.ucloud.cn/hello123/centos:7.8.2003　 
RUN yum -y install openssh-server;yum clean all
RUN mkdir /var/run/sshd && \
    ssh-keygen -t rsa -f /etc/ssh/ssh_host_rsa_key && \
    ssh-keygen -t dsa -f /etc/ssh/ssh_host_dsa_key && \
    /bin/sed -i 's/.*session.*required.*pam_loginuid.so.*/session optional pam_loginuid.so/g' /etc/pam.d/sshd && \
    /bin/echo -e "LANG=\"en_US.UTF-8\"" > /etc/default/local
ADD entrypoint.sh /var/entrypoint.sh
RUN chmod +x /var/entrypoint.sh
EXPOSE 22
ENTRYPOINT [ "./var/entrypoint.sh" ]
```
