# DaemonSet暴露hostPort

DaemonSet部署，注意每个节点端口都要空出，否则该节点会启动失败

```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: nginx-daemonset
spec:
  selector:
    matchLabels:
      name: nginx-daemonset
  template:
    metadata:
      labels:
        name: nginx-daemonset
    spec:
      containers:
        - image: uhub.service.ucloud.cn/hello123/nginx:1.17.10-alpine
          imagePullPolicy: IfNotPresent
          name: nginx-daemonset
          ports:
            - containerPort: 80
              hostPort: 8001
              protocol: TCP
      restartPolicy: Always
```