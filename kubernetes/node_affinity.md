# 节点亲和性

## 给节点打标签

```bash
kubectl label nodes 10.10.10.10 node=nodeaffinity
```

## 给pod增加亲和性

硬匹配，运行在`node=nodeaffinity`的节点上

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
        nodeAffinity:
          requiredDuringSchedulingIgnoredDuringExecution: 
            nodeSelectorTerms: 
            - matchExpressions:
              - key: node # node节点的key
                operator: In # In、NotIn、Exists、DoesNotExist、Gt、Lt
                values:
                - "nodeaffinity" # node节点的values
      containers:
      - image: nginx
        name: nginx
        ports:
        - containerPort: 80
        resources: {}
```

不运行在`node=nodeaffinity`的节点上

```yaml
- matchExpressions:
  - key: node
    operator: NotIn
    values:
    - "nodeaffinity"
```

软匹配，加权调度

对于满足所有调度要求的每个节点，调度程序将通过迭加此字段的元素计算总和，并在节点与对应的节点匹配时将“权重”添加到总和MatchExpressions，然后将该分数与节点的其他优先级函数的分数组合。总得分最高的节点是最优选的。

```yaml
preferredDuringSchedulingIgnoredDuringExecution:
- weight: 1 # 1-100 
  preference:
    matchExpressions:
    - key: node
      operator: In
      values:
      - "nodeaffinity"
```



### 没用过

[matchFields](https://v1-18.docs.kubernetes.io/docs/reference/generated/kubernetes-api/v1.18/#nodeselectorterm-v1-core) 没用过，没用过

[Exists、DoesNotExist](https://v1-18.docs.kubernetes.io/docs/reference/generated/kubernetes-api/v1.18/#nodeselectorrequirement-v1-core) 忽略values，没用过

[Gt、Lt](https://v1-18.docs.kubernetes.io/docs/reference/generated/kubernetes-api/v1.18/#nodeselectorrequirement-v1-core) values必须有一个值，且解析为int，没用过