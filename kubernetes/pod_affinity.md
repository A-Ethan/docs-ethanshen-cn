# pod亲和性和反亲和性

## 给pod打标签

```bash
# kubectl label还支持deploy、svc等资源对象
kubectl label po nginx-7db67b8c69-zcxmm pod=podaffinity
```

## 给pod增加亲和性

硬匹配，和具有`pod=podaffinity`标签的pod运行在一起

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: nginx
  name: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: nginx
    spec:
      affinity:
        podAffinity:
          requiredDuringSchedulingIgnoredDuringExecution: 
            labelSelector: 
            - matchExpressions:
              - key: pod # pod label的key
                operator: In # In、NotIn、Exists、DoesNotExist、Gt、Lt
                values:
                - "podaffinity" # pod label的values
              namespaces: default # 指定namespaces
              topologyKey: topology.kubernetes.io/zone  #同时匹配node的标签
      containers:
      - image: nginx
        name: nginx
        ports:
        - containerPort: 80
        resources: {}
```

`topology.kubernetes.io/zone(并不限制只有这个标签)`可以在`kubectl get node --show-label`中查看到

## 亲和反亲和（官网例子）

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web-server
spec:
  selector:
    matchLabels:
      app: web-store
  replicas: 3
  template:
    metadata:
      labels:
        app: web-store
    spec:
      affinity:
        podAntiAffinity: # 不和自己在一起，节点不重复
          requiredDuringSchedulingIgnoredDuringExecution:
          - labelSelector:
              matchExpressions:
              - key: app
                operator: In
                values:
                - web-store
            topologyKey: "kubernetes.io/hostname"
        podAffinity: # 和app=store在一起，每个节点都有
          requiredDuringSchedulingIgnoredDuringExecution:
          - labelSelector:
              matchExpressions:
              - key: app
                operator: In
                values:
                - store
            topologyKey: "kubernetes.io/hostname"
      containers:
      - name: web-app
        image: nginx:1.16-alpine
```