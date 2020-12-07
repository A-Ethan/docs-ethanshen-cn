# ConfigMap修改nginx端口

Deployment中的容器使用ConfigMap，挂载配置文件

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: defaultconfig
data:
  default.conf: | 
    server {
        listen      8080; 
        server_name localhost; 
        location / {
            root   /usr/share/nginx/html; 
            index  index.html index.htm; 
        } 
        error_page   500 502 503 504  /50x.html; 
        location = /50x.html { 
            root   /usr/share/nginx/html; 
        }
      }
---
apiVersion: apps/v1
kind: Deployment 
metadata: 
  name: hello-nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: hello-nginx
  template:
    metadata:
      labels:
        app: hello-nginx
    spec:
      containers:
      - name:  nginx
        image: uhub.service.ucloud.cn/hello123/nginx:1.17.10-alpine
        env:
        - name: NGINX_VERSION
          value: 1.17.10
        workingDir: /
        command: ["nginx"]
        args: ["-g", "daemon off;"]
        ports:
        - name: http
          containerPort: 8080
        volumeMounts:
        - name: nginx-defaultconfig
          mountPath: /etc/nginx/conf.d/
      volumes:
      - name: nginx-defaultconfig
        configMap:
            name: defaultconfig
```