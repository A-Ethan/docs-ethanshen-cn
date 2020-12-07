# 污点、容忍污点

> 可以配合selector和affinity使用，取交集可用节点。
>
> 3个taint，2个toleration匹配不了。
> 
> 常用于**专用节点**、**特殊硬件节点**、**基于污点的驱逐**(有空试试)。

## 污点

给节点增加污点，必须容忍污点才能调度到该节点

```bash
# 添加污点
kubectl taint node node1 key1=value1:NoSchedule
# 删除污点
kubectl taint node node1 key1=value1:NoSchedule-
# 添加一组节点污点
kubectl taint node -l node=label key1=value1:NoSchedule
# 无value添加
kubectl taint node node1 key1:NoSchedule
```
### effect枚举值

* NoSchedule 不调度
* PreferNoSchedule  最好不调度
* NoExecute  不执行（驱逐已有且不符合的pod）

## 容忍污点

存在，不用指定value

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
      tolerations:
      - key: "key1"
        operator: "Exists"  # 存在，不用指定value
        effect: "NoSchedule"
```

相等，需要指定value 

```yaml
      tolerations:
      - key: "key1"
        operator: "Equal"  # 相等，需要指定value 
        value: "value1"
        effect: "NoSchedule"
```

## 有空试试系列

### 官网例子

> **Note:**
> There are two special cases:
>
> An empty `key` with operator `Exists` matches all keys, values and effects which means this will tolerate everything.
>
> An empty `effect` matches all effects with key `key1`.

我觉得这么写，有空试一试

```yaml
# An empty `key` with operator `Exists` matches all keys, values and effects which means this will tolerate everything.
      tolerations:
      - operator: "Exists"
        effect: "NoSchedule"
```

```yaml
# An empty `effect` matches all effects with key key1.
      tolerations:
      - key: "key1"
        operator: "Exists"
```


### 基于污点的驱逐

[v1.18 [stable]有空试试](https://kubernetes.io/docs/concepts/scheduling-eviction/taint-and-toleration/#taint-based-evictions)

The `NoExecute` taint effect, mentioned above, affects pods that are already
running on the node as follows

 * pods that do not tolerate the taint are evicted immediately
 * pods that tolerate the taint without specifying `tolerationSeconds` in
   their toleration specification remain bound forever
 * pods that tolerate the taint with a specified `tolerationSeconds` remain
   bound for the specified amount of time

The node controller automatically taints a Node when certain conditions
are true. The following taints are built in:

 * `node.kubernetes.io/not-ready`: Node is not ready. This corresponds to
   the NodeCondition `Ready` being "`False`".
 * `node.kubernetes.io/unreachable`: Node is unreachable from the node
   controller. This corresponds to the NodeCondition `Ready` being "`Unknown`".
 * `node.kubernetes.io/out-of-disk`: Node becomes out of disk.
 * `node.kubernetes.io/memory-pressure`: Node has memory pressure.
 * `node.kubernetes.io/disk-pressure`: Node has disk pressure.
 * `node.kubernetes.io/network-unavailable`: Node's network is unavailable.
 * `node.kubernetes.io/unschedulable`: Node is unschedulable.
 * `node.cloudprovider.kubernetes.io/uninitialized`: When the kubelet is started
    with "external" cloud provider, this taint is set on a node to mark it
    as unusable. After a controller from the cloud-controller-manager initializes
    this node, the kubelet removes this taint.

In case a node is to be evicted, the node controller or the kubelet adds relevant taints
with `NoExecute` effect. If the fault condition returns to normal the kubelet or node
controller can remove the relevant taint(s).


> The control plane limits the rate of adding node new taints to nodes. This rate limiting
> manages the number of evictions that are triggered when many nodes become unreachable at
> once (for example: if there is a network disruption).


You can specify `tolerationSeconds` for a Pod to define how long that Pod stays bound
to a failing or unresponsive Node.

For example, you might want to keep an application with a lot of local state
bound to node for a long time in the event of network partition, hoping
that the partition will recover and thus the pod eviction can be avoided.
The toleration you set for that Pod might look like:

```yaml
tolerations:
- key: "node.kubernetes.io/unreachable"
  operator: "Exists"
  effect: "NoExecute"
  tolerationSeconds: 6000
```

> Kubernetes automatically adds a toleration for `node.kubernetes.io/not-ready` and `node.kubernetes.io/unreachable` with `tolerationSeconds=300`,unless you, or a controller, set those tolerations explicitly.
>
> These automatically-added tolerations mean that Pods remain bound to
Nodes for 5 minutes after one of these problems is detected.


[DaemonSet](/docs/concepts/workloads/controllers/daemonset/) pods are created with
`NoExecute` tolerations for the following taints with no `tolerationSeconds`:

  * `node.kubernetes.io/unreachable`
  * `node.kubernetes.io/not-ready`

This ensures that DaemonSet pods are never evicted due to these problems.