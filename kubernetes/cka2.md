1. # 考试要求

   1. 考试环境：房间一个，要求安静无人，桌面干净。

   2. 考试方式： 只使用chrome浏览器，不会使用到其他软件。通过浏览器调用摄像头，麦克风，桌面分享等功能。

   3. 考官交流： 与对方聊天全程打字，对方全程监控，并下达各种指令。对方会要求我们分享摄像头，分享桌面，没事不要乱动。全英文交流，所以只要英文阅读能力不错就行，其他时间可以全程 输入ok！

   4. 考前准备步骤：
      1 在https://www.cncf.io/certification/CKA/处用信用卡报名，填上优惠码（DCUBEOFFER）在https://examslocal.com 处预订考试时间，直接预约15 天以后考试即可，时间够了，给自己紧迫感。

      2 看好华为云视频，主要是操作部分<https://www.bilibili.com/video/av46687897?from=search&seid=2972341230258168358> 。

      3 官方文档一定要熟悉。<https://kubernetes.io/docs/home/> 
      4 按照几套真题题目做一遍（就做这个，每一个都了解透彻就够了，老外很懒，每次考试90%的
      题目都是相似的，7月14号的一定要滚瓜烂熟，文档都是按照考题顺序记录下来的）

      5 练习集群可以通过本地虚拟机搭建，也可以申请华为云免费的体验集群。部署集群的方式看此链接：<https://github.com/luckman666/deploy_Kubernetes-v1.15.0> 

      注意点：
      1 最好有签证，没有的话使用身份证和带英文名字的证件即可，比如驾照翻译件。
      2 考试时的环境不能使用右键复制，mac 是原先的复制黏贴键，windows 是使用ctrl+insert
      和shift+insert，windows 笔记本一定要带外接键盘，不然按insert 有点麻烦，考试模拟的时
      候，记得熟悉这样的复制黏贴操作。
      3 考试时每次做题目前，记得看看是不是需要切换上下文环境，考试会给相应命令，需要的
      话，不要忘记切换，考试有好几个集群，上下文切换会转到不同的集群。注意要关注是否处
      在跳板机，某个集群某个node 的普通用户还是特殊用户，退出到跳板机使用的是exit 命令。
      4 每个创建的yaml 你要知道是哪一题哪一个小题，来命名，否则检查时会搞混。
      5 考试时只允许有考试网站和k8s 文档两个标签页。你可以用书签记录下有用的k8s 文档网
      站
      6 考试第一次没过没关系，有两次机会，记住题目差的不多，熟悉环境，熟悉题目第二次过
      就行。

2. # 考试技巧

   1. 要翻墙。要求稳定的梯子。

   2. 最好安排早上： 因为国际网络出口早上比较轻松。我是预约的9点。

   3. 加快考试速度。

      2.1使用命令补全：

      ```
      source <(kubectl completion bash)
      ```

      2.2使用别名；

      ```
      alias kc='kubectl'
      alias kgp='kubectl get pods'
      alias kgs='kubectl get svc'
      ```

      2.3能用命令行，就不要自己写yaml文件；

      2.4 能够从kubernetes.io中复制粘贴的，就不要自己敲命令；复制粘贴我用的是鼠标的粘贴复制，没有使用shift+insert,ctrl+insert，不支持ctrl+c,ctrl+v。

   4. 注意审题，认真做题

      3.1中英文切换审题，特别是有歧义的时候。

      3.2 注意切换运行环境，kubectl config set-config。

   5. 命令行和yaml问题

      考试中有关kubernetes本身的题目，除了pv和pvc，其他情况下基本都可以使用kubectl命令行去转换成yaml文件，没有命令行的可以直接粘贴复制kubernetes.io文件，请各位多加练习。可以将kubectl 命令行撸个好几遍。


# 1.监控 foobar Pod 的日志，提取 pod 相应的行'error'写入到/logs 文件中

```
  kubectl logs foobar | grep error > /logs
```

# 2.使用 name 排序列出所有的 PV，把输出内容存储到/opt/文件中 使用 kubectl own 对输出进行排序，并且不再进一步操作它

```
  kubectl get pv --all-namespace --sort-by=.metadata.name > /opt/
```

# 3.确保在 kubectl 集群的每个节点上运行一个 Nginx Pod。其中 Nginx Pod 必须使用 Nginx 镜像。不要覆盖当前环境中的任何 traints。 使用 Daemonset 来完成这个任务，Daemonset 的名字使用 ds。

	题目对应文档：https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/
	删除tolerations字段，复制到image: gcr.io/fluentd-elasticsearch/fluentd:v2.5.1这里即可，再按题意更改yaml文件。
	apiVersion: apps/v1
	kind: DaemonSet
	metadata:
	  name: ds
	  namespace: kube-system
	  labels:
		k8s-app: fluentd-logging
	spec:
	  selector:
		matchLabels:
		  name: fluentd-elasticsearch
	  template:
		metadata:
		  labels:
			name: fluentd-elasticsearch
		spec:
		  containers:
		  - name: fluentd-elasticsearch
			image: nginx

# 4.添加一个 initcontainer 到 lum(/etc/data)这个 initcontainer 应该创建一个名为/workdir/calm.txt 的空文件，如果/workdir/calm.txt 没有被检测到，这个 Pod 应该退出

  - - ```
      题目中yaml文件已经给出，只需要增加initcontainers部分，以及emptyDir: {} 即可
      init文档位置：https://kubernetes.io/docs/concepts/workloads/pods/init-containers/
      apiVersion: v1
      kind: Pod
      metadata:
        name: nginx
        labels:
          env: test
      spec:
        volumes:
      - name: workdir
        emptyDir: {} 
          containers:
      - name: nginx
        image: nginx
        command: [if ..]
        volumeMounts:
        - name: work
          mountPath: /workdir
          initContainers:
      - name: init-myservice
        image: busybox:1.28
        command: 
        - touch
        - /workdir/calm.txt
          volumeMounts:
        - name: work
          mountPath: /workdir
        
      ```

      

# 5.创建一个名为 kucc 的 Pod,其中内部运行着 nginx+redis+memcached+consul 4 个容器

```
https://v1-14.docs.kubernetes.io/docs/concepts/workloads/pods/pod-overview/
	apiVersion: v1
	kind: Pod
	metadata:
	  name: kucc
	spec:
	  containers:
	  - name: nginx
		image: nginx
	  - name: redis
		image: redis
	  - name: memcached
		image: memcached
	  - name: consul
		image: consul
```

# 6.创建 Pod，名字为 nginx，镜像为 nginx，添加 label disk=ssd

```
https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
apiVersion: v1
	kind: Pod
	metadata:
	  name: nginx
	  labels:
		env: test
	spec:
	  containers:
	  - name: nginx
		image: nginx
		imagePullPolicy: IfNotPresent
	  nodeSelector:
		disk: ssd
```

# 7.创建 deployment 名字为 nginx-app 容器采用 1.11.9 版本的 nginx  这个 deployment 包含 3 个副本,接下来通过滚动升级的方式更新镜像版本为 1.12.0，并记录这个更新，最后，回滚这个更新到之前的 1.11.9 版本

	kubectl run deployment nginx-app --image=nginx:1.11.9 --replicas=3
	kubectl set image deployment nginx-app nginx-app=nginx:1.12.0 --record  (nginx-app container名字)
	kubectl rollout history deployment nginx-app
	kubectl rollout undo deployment nginx-app

# 8.创建和配置 service，名字为 front-end-service。可以通过 NodePort/ClusterIp 开访问，并且路由到 front-end 的 Pod 上

```
kubectl expose pod fron-end --name=front-end-service --port=80  --type=NodePort
```

# 9.创建一个 Pod，名字为 Jenkins，镜像使用 Jenkins。在新的 namespace website-frontend 上创建

	kubectl create ns website-frontend
	
	apiVersion: v1
	kind: Pod
	metadata:
	  name: Jenkins
	  namespace: website-frontend
	spec:
	  containers:
	  - name: Jenkins
		image: Jenkins
		
	kubectl apply -f ./xxx.yaml 	

# 10.创建 deployment 的 spec 文件: 使用 redis 镜像，7 个副本，label 为 app_enb_stage=dev deployment 名字为 kual00201 保存这个 spec 文件到/opt/KUAL00201/deploy_spec.yaml完成后，清理(删除)在此任务期间生成的任何新的 k8s API 对象

```
kubectl run kual00201 --image=redis --labels=app_enb_stage=dev --dry-run -oyaml > /opt/KUAL00201/deploy_spec.yaml
```

# 11.创建一个文件/opt/kucc.txt ，这个文件列出所有的 service 为 foo ,在 namespace 为 production 的 Pod这个文件的格式是每行一个 Pod的名字

```
kubectl get svc -n production --show-labels | grep foo

kubectl get pods -l app=foo(label标签) -o=custom-columns=NAME:.spec.name > kucc.txt
```

# 12.创建一个secret,名字为super-secret包含用户名bob,创建pod1挂载该secret，路径为/secret，创建pod2，使用环境变量引用该secret，该变量的环境变量名为ABC

	https://kubernetes.io/zh/docs/concepts/configuration/secret/#%E8%AF%A6%E7%BB%86
	echo -n "bob" | base64
	
	apiVersion: v1
	kind: Secret
	metadata:
	  name: super-secret
	type: Opaque
	data:
	  username: Ym9i
	  
	apiVersion: v1
	kind: Pod
	metadata:
	  name: pod1
	spec:
	containers:
	- name: mypod
	  image: redis
	  volumeMounts:
	- name: foo
	  mountPath: "/secret"
	  readOnly: true
	volumes: secret
	- name: foo
	  secret:
	    secretName: super-secret


	apiVersion: v1
	kind: Pod
	metadata:
	  name: pod-evn-eee
	spec:
	containers:
	- name: mycontainer
	image: redis
	env:
	- name: ABC
	    valueFrom:
	      secretKeyRef:
	        name: super-secret
	        key: username
	restartPolicy: Never

# 13.在新的ns中创建pv，指定pv名字和挂载路径，镜像等

	https://kubernetes.io/docs/tasks/configure-pod-container/configure-persistent-volume-storage/#create-a-persistentvolume
	kubectl create ns new
	
	apiVersion: v1
	kind: PersistentVolume
	metadata:
	  name: pv0003
	spec:
	  capacity:
	    storage: 5Gi
	volumeMode: Filesystem
	accessModes:
	- ReadWriteOnce
	persistentVolumeReclaimPolicy: Recycle
	storageClassName: slow
	hostPath:
	  path: "/etc/data"
	
	kubectlc apply -f ./xxx.yaml --namespace=new

# 14.为给定deploy  websity副本扩容到6

```
 kubectl scale deployment website --replicas=6
```

# 15.查看给定集群ready的node个数(不包含NoSchedule) 

```
1.kubectl get nodes 
2.把所有ready得都执行kubectl describe node $nodename | grep Taint  如果有NoSchedule
```

# 16.找出指定ns中使用cup最高的pod名写出到指定文件

```
   kubectc top pod -l xxx --namespace=xxx
```

# 17.创建一个 deployment 名字为:nginx-dns 路由服务名为：nginx-dns 确保服务和 pod 可以通过各自的 DNS 记录访问 容器使用 nginx 镜像，使用 nslookup 工具来解析 service 和 pod 的记录并写入相应的/opt/service.dns 和/opt/pod.dns 文件中，确保你使用 busybox:1.28 的镜像用来测试。

  - ```
    busybox这里找：https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/
    1.kubectl run nginx-dns --image=nginx
      kubectl expose deployment nginx-dns --name=nginx-dns --port=80 --type=NodePort
      kubectl get pod -owide xxx (查看pod IP)
    2.建立busybox
       apiVersion: v1
    	kind: Pod
    	metadata:
      name: busybox1
      labels:
        name: busybox
    	spec:
      hostname: busybox-1
      subdomain: default-subdomain
      containers:
    - image: busybox:1.28
      command:
      sleep
      "3600"
      name: busybo
    3.解析
      kubectl exec -it busybox -- nslookup nginx-dns
      kubectl exec -it busybox -- nslookup 10.244.0.122(pod IP)
    ```

# 18.给定https地址，ca，cert证书，key备份该数据到指定目录 

```
ETCDCTL_API=3 etcdctl --endpoints=https://127.0.0.1:1111 --ca-file=/pki/ca.crt --cert-file=/pki/cert.crt --key-file=/pki/key.crt snapshot save 给的路径
有些题目下--ca-file会报错，记得看endpoints -h 里的字段怎么要求的
```

# 19.在ek8s集群中使name=ek8s-node-1节点不能被调度，并使已被调度的pod重新调度

```
先切换集群到ek8s    
再执行
kubectl drain node1 --ignore-daemonsets --delete-local-data  
```

# 20.给定集群中的一个node未处于ready状态，解决该问题并具有持久性

```
进入集群
ssh node  

systemctl status kubelet

systemctl start kubelet   
systemctl enable kubelet
```

# 21.题目很绕，大致是 在k8s的集群中的node1节点配置kubelet的service服务，去拉起一个由kubelet直接管理的pod(说明了是静态pod)，

```
该文件应该放置在/etc/kubernetes/manifest目录下(给出了pod路径)

创建  1.vi /etc/kubernetes/manifest/static-pod.yaml
      2.systemctl status kubelet   查找kubelet.service路径  考试目录是: /etc/systemd/system/kubernetes.service
	  3.vi /etc/systemd/system/kubernetes.service   观察有没有 --pod-manifest-path=/etc/kubernetes/manifest 这句话   没有就加上得
	  4.sudo -i   ssh node  sudo -i
	  5.systemctl daemon-reload            systemctl restart kubelet.service
	  6.systemctl enable kubelet
      7.检查  kubectl get pods -n kube-system | grep static-pod  实际是static-pod+系统  static-pod-kubelet-service
```

# 22.给出了指定的集群，该集群中kubelet.service服务无法正常启动，解决该问题，并具有持久性

```
情形一：kubectl 命令能用 kubectl get cs 健康检查  看manager-controller  是否ready   如果不ready   systemctl start kube-manager-controller.service   
情形二：kubectl 命令不能用   systemctl status kubelet 查找kubelet.service目录   vi /etc/systemd/system/kubernetes.service    添加 --pod-manifest-path=/etc/kubernetes/manifest   
systemctl daemon-reload  systemctl restart kubelet  systemctl enable kubelet   
```


23.TLS问题 （一道很长的题目，建议放弃，难度特别大）

# 24.创建指定大小和路径的pv mode ReadWriteOnce      

```
https://kubernetes.io/docs/tasks/configure-pod-container/configure-persistent-volume-storage/#create-a-persistentvolume

apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv0003
spec
  capacity:
    storage: 1Gi
  volumeMode: Filesystem
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Recycle
  storageClassName: slow
  hostPath:
    path: /data
```


