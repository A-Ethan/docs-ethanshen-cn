# PVC Deployment

```yaml
# pvc-registry
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: nginx-ns
  labels:
    app: nginx-ns
spec:
  accessModes: 
  - ReadWriteOnce
  storageClassName: ssd-csi-udisk
  resources:
    requests:
      storage: 20Gi
---
#deploy-registry
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-ns
  labels:
    app: nginx-ns
spec:
  replicas: 1
  strategy:
    type: RollingUpdate
  selector:
    matchLabels:
      app: nginx-ns
  template:
    metadata:
      labels:
        app: nginx-ns
    spec:
      containers:
      - name: nginx-ns
        image: uhub.service.ucloud.cn/hello123/nginx:1.17.10-alpine
        imagePullPolicy: IfNotPresent
        volumeMounts:
        - name: data
          mountPath: /data
      volumes:
      - name: data
        persistentVolumeClaim:
          claimName: nginx-ns
```