# Java Demo

## 有jar包，从centos镜像开始制作

```Dockerfile
FROM centos:latest 
ADD jdk-8u221-linux-x64.tar.gz /usr/local/
RUN chown -R root:root /usr/local/jdk1.8.0_221 && mkdir -p /apps && mkdir -p /logs
#COPY application.yml /apps/application.yml
COPY underwrite.jar /apps/underwrite.jar
ENV JAVA_HOME=/usr/local/jdk1.8.0_221
ENV CLASSPATH=$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar:.
ENV PATH=$PATH:$JAVA_HOME/bin:.
WORKDIR /apps
EXPOSE 8081
CMD /usr/local/jdk1.8.0_221/bin/java -jar /apps/underwrite.jar --spring.profiles.active=ucloud
```

## 从maven开始

CentOS 7.8

```bash
yum install git maven -y
mkdir git
cd git/
git init
git clone https://github.com/A-Ethan/hello-world.git
cd hello-world/
mvn package
docker build -f Dockerfile -t uhub.service.ucloud.cn/hello/spring:v1 .
docker run -d -p 80:8080 --name ucloud uhub.service.ucloud.cn/hello/spring:v1
```


## 制作tomcat镜像

openjdk自下载tomcat进行安装

```Dockerfile
FROM openjdk:8u212-jre-alpine3.9

ENV TOMCAT_VER 9.0.26
ENV CATALINA_HOME /usr/local/apache-tomcat-${TOMCAT_VER}
ENV PATH ${CATALINA_HOME}/bin/:${PATH}

# tomcat0 Download url
ENV DOWNLOAD_URL http://ethan-helloworld.cn-sh2.ufileos.com/pub/software/apache/tomcat/tomcat-9/v${TOMCAT_VER}/bin/apache-tomcat-${TOMCAT_VER}.tar.gz

WORKDIR /usr/local/

RUN set -eux; \
    /bin/cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime && echo 'Asia/Shanghai' >/etc/timezone; \
    wget ${DOWNLOAD_URL}; \
    tar zxf apache-tomcat-${TOMCAT_VER}.tar.gz; \
    rm -rf apache-tomcat-${TOMCAT_VER}.tar.gz; \
    chmod 755 ${CATALINA_HOME}/bin/*.sh
    
EXPOSE 8080

CMD ["catalina.sh", "run"]
```