# 多进程容器镜像

## 第一个Dockerfile

初始环境dockerfile，复制程序到镜像内

```Dockerfile
FROM uhub.service.ucloud.cn/hello123/centos:centos7.6.1810
RUN mkdir /data && mkdir /deadline10
ADD data0 /data
```

## shell脚本

```shell
#!/bin/sh

while true
do
process=`ps aux | grep deadlineslave | grep -v grep`;

if [ "$process" == "" ]; then
sleep 1;
echo "`date '+%Y%m%d %H:%M:%S'`: no process" >> /var/log/deadlineslavecheck.log;
/etc/init.d/deadline10launcher restart
sleep 600;
else
sleep 600;
fi
done
```

## 第二个Dockerfile

```Dockerfile
FROM centosdeadline:v2
CMD [ "/etc/init.d/deadlineshell.sh" ] 
```