# nginx-ingress获取访客真实IP

> 时间太久不确定是否还好用

nginx作为反向代理，客户端和web服务端之间增加了中间层，因此服务端无法直接拿到客户端的IP，通过$remote_addr变量拿到的是最后一层代理的地址。
以Node.js为例，通过`req.connection.remoteAddress`本质上是获取$remote_addr地址，也就是nginx地址。在nginx-ingress-controller容器中可以查看到nginx.conf文件，文件中有这样一条配置：

```
proxy_set_header X-Real-IP              $the_real_ip;
proxy_set_header X-Forwarded-For        $the_real_ip;
```

其中`X-real-ip`是一个自定义的变量名，获取的`$the_real_ip`是客户端源IP；`X-Forwarded-For`可能存在除客户端源IP外的多个IP，格式如下：

```
X-Forwarded-For: client, proxy1, proxy2
```

在web服务端可以获取`X-real-ip`得到客户端源IP：

```
req.headers['x-real-ip']
```

可参考[nginx-ingress官方文档](https://kubernetes.github.io/ingress-nginx/user-guide/nginx-configuration/log-format/)和nginx.conf文件中变量的对比获取想要的信息