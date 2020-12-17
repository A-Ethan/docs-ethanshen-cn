> 整理自互联网

# 自动补全
source <(kubectl completion bash)

# 常用举例

生成pod

```bash
  kubectl run pod --image=nginx --restart=Never --requests=
```

生成job

```bash
  kubectl run job --image=nginx --restart=OnFailure
```

生成deploy

```bash
  kubectl run deploy --image=nginx  --replicas=2
```

生成svc

```bash
  kubectl expose deployment sd2 --type=ClusterIP --target-port=80 --name=sd2 
```

生成名称空间

```bash
  kubectl create ns ns
```

生成configmap

```bash
  kubectl create configmap  db2   --from-literal=nt=nt
```

# 查看

查看所有namespace的pods运行情况

```bash
kubectl get pods --all-namespaces
```

查看具体pods，记得后边跟namespace名字哦

```bash
kubectl get pods  kubernetes-dashboard-76479d66bb-nj8wr --namespace=kube-system
```

查看pods具体信息

```bash
kubectl get pods -o wide kubernetes-dashboard-76479d66bb-nj8wr --namespace=kube-system
```

查看集群健康状态

```bash
kubectl get cs
```

获取所有deployment

```bash
kubectl get deployment --all-namespaces
```

列出该 namespace 中的所有 pod 包括未初始化的

```bash
kubectl get pods --include-uninitialized
```

查看deployment()

```bash
kubectl get deployment nginx-app
```

查看deployment和servers

```bash
kubectl get deploy,services
```

查看pod详情

```bash
kubectl describe pods xxxxpodsname --namespace=xxxnamespace
```

查看pod日志

```bash
kubectl logs $POD_NAME
```

查看pod变量

```bash
kubectl exec my-nginx-5j8ok -- printenv | grep SERVICE
```


# 集群

集群健康情况

```bash
kubectl get cs           
```

集群核心组件运行情况

```bash
kubectl cluster-info
```

命名空间

```bash
kubectl get namespaces
```

版本

```bash
kubectl version
```

api

```bash
kubectl api-versions
```

查看事件

```bash
kubectl get events
```

获取全部节点

```bash
kubectl get nodes
```

删除节点

```bash
kubectl delete node k8s2
```

# 创建

创建资源

```bash
kubectl create -f ./nginx.yaml
```

创建当前目录下的所有yaml资源

```bash
kubectl create -f ./
```

使用多个文件创建资源

```bash
kubectl create -f ./nginx1.yaml -f ./mysql2.yaml
```

使用目录下的所有文件夹的yaml来创建资源

```bash
kubectl create -f ./dir
```

使用 url 来创建资源

```bash
kubectl create -f https://xxxx/xxx
```

创建带有终端的pod

```bash
kubectl run -i --tty busybox --image=busybox
```

创建一个nginx实例

```bash
kubectl run nginx --image=nginx
```

创建deployment

```bash
kubectl run mybusybox --image=busybox --replicas=5
```

获取 pod 和 svc 的api文档

```bash
kubectl explain pods,svc
```

# 更新

滚动更新 pod frontend-v1

```bash
kubectl rolling-update python-v1 -f python-v2.json
```

更新资源名称并更新镜像

```bash
kubectl rolling-update python-v1 python-v2 --image=image:v2
```

更新 frontend pod 中的镜像

```bash
kubectl rolling-update python --image=image:v2
```

退出已存在的进行中的滚动更新

```bash
kubectl rolling-update python-v1 python-v2 --rollback
```

为 nginx RC 创建svc

```bash
kubectl expose rc nginx --port=80 --target-port=8000
```

添加标签

```bash
kubectl label pods nginx-pod new-label=awesome
```

添加注解

```bash
kubectl annotate pods nginx-pod icon-url=http://goo.gl/XXBTWq
```



# 编辑资源

编辑名为 docker-registry 的 service

```bash
kubectl edit svc/docker-registry
```

# 动态伸缩pod

HPA

```bash
kubectl autoscale deployment foo --min=2 --max=10
```

将foo副本集变成3个

```bash
kubectl scale --replicas=3 rs/foo
```

变更“foo”中指定的资源数量

```bash
kubectl scale --replicas=3 -f foo.yaml
```

将deployment/mysql从2个变成3个

```bash
kubectl scale --current-replicas=2 --replicas=3 deployment/mysql
```

变更多个控制器的数量

```bash
kubectl scale --replicas=5 rc/foo rc/bar rc/baz
```

查看变更进度

```bash
kubectl rollout status deploy deployment/mysql
```

# 删除

删除 pod.json 文件中定义的类型和名称的 pod

```bash
kubectl delete -f ./pod.json
```

删除名为“baz”的 pod 和名为“foo”的 service

```bash
kubectl delete pod,service baz foo
```

删除具有 name=myLabel 标签的 pod 和 serivce

```bash
kubectl delete pods,services -l name=myLabel
```

删除具有 name=myLabel 标签的 pod 和 service，包括尚未初始化的

```bash
kubectl delete pods,services -l name=myLabel --include-uninitialized
```

删除 my-ns namespace下的所有 pod 和 serivce，包括尚未初始化的

```bash
kubectl -n my-ns delete po,svc --all
```

强制删除

```bash
kubectl delete pods prometheus-7fcfcb9f89-qkkf7 --grace-period=0 --force 
```


# 交互

输出pod的日志

```bash
kubectl logs nginx-pod
```

输出pod中容器的日志

```bash
kubectl logs nginx-pod -c my-container
```

滚动持续输出pod的日志

```bash
kubectl logs -f nginx-pod
```

滚动持续输出pod中容器的日志

```bash
kubectl logs -f nginx-pod -c my-container
```

交互式 shell 的方式运行 pod

```bash
kubectl run -i --tty busybox --image=busybox -- sh
```

连接到运行中的容器

```bash
kubectl attach nginx-pod -i
```

在已存在的pod单容器中执行命令

```bash
kubectl exec nginx-pod -- ls /
```

在已存在的pod中的容器执行命令

```bash
kubectl exec nginx-pod -c my-container -- ls /
```

metrics 显示指定pod和容器的监控指标度量

```bash
kubectl top pod POD_NAME --containers
```

# 调度配置

标记my-node不可调度

```bash
kubectl cordon k8s-node
```

驱逐my-node上的pod以待维护

```bash
kubectl drain k8s-node
```

标记my-node可调度

```bash
kubectl uncordon k8s-node
```

metrics 显示my-node的监控指标度量

```bash
kubectl top node k8s-node
```

将当前集群状态输出到 stdout

```bash
kubectl cluster-info dump
```

将当前集群状态输出到 /path/to/cluster-state

```bash
kubectl cluster-info dump --output-directory=/path/to/cluster-state
```

如果该键和影响的污点（taint）已存在，则使用指定的值替换

```bash
kubectl taint nodes foo dedicated=special-user:NoSchedule
```


