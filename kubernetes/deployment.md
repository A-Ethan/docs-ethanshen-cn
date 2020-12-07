# Deployment

## 直接用

```yaml
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
          containerPort: 80
```