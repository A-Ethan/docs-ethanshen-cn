# CKA考试

> 有空再整理，还有2 3

## 集群搭建

> ubuntu 环境 1.15版本 抽空再整理吧


1. 设置主机名hostname，管理节点设置主机名为 master 。
```
sudo hostnamectl set-hostname master
```
需要设置其他主机名称时，可将 master 替换为正确的主机名node1、node2即可。

2. 编辑 /etc/hosts 文件，添加域名解析。
```
cat <<EOF >>/etc/hosts
10.23.9.138 master1
10.23.232.20 worker1
EOF
```

sudo ufw disable

sudo swapoff -a

sed -i 's/.*swap.*/#&/' /etc/fstab
### 安装docker
4. 安装docker
创建文件夹，准备docker的配置
mkdir /etc/docker
mkdir -p /data/docker

5. 使用ustc的镜像源

add-apt-repository \
"deb [arch=amd64] https://mirrors.ustc.edu.cn/docker-ce/linux/$(. /etc/os-release; echo "$ID") \
$(lsb_release -cs) \
stable"


6. 从源中查找grep抓取出版本号，并安装docker-ce 18.06版本
apt-get update

apt-cache madison docker-ce

apt-get update && apt-get install -y docker-ce=$(apt-cache madison docker-ce | grep 18.06 | head -1 | awk '{print $3}')

### 添加用户到docker的组
gpasswd -a root docker

### master安装kubelet kubeadm kubectl
cat <<EOF >/etc/apt/sources.list.d/kubernetes.list
deb http://apt.kubernetes.io/ kubernetes-xenial main
EOF

获取公钥，显示ok即表示正确
curl -S https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add - 

1. 下载kube_apt_key.gpg到当前工作目录下，并按如下指令添加

wget https://raw.githubusercontent.com/EagleChen/kubernetes_init/master/kube_apt_key.gpg

echo "deb [arch=amd64] https://mirrors.ustc.edu.cn/kubernetes/apt kubernetes-$(lsb_release -cs) main" | sudo tee -a /etc/apt/sources.list

2. 安装
apt-get update
apt-get install -y cri-tools=1.12.0-00 kubelet=1.14.2-00 kubeadm=1.14.2-00 kubectl=1.14.2-00 --allow-unauthenticated

sysctl net.bridge.bridge-nf-call-iptables=1

sed -i "s,ExecStart=$,Environment=\"KUBELET_EXTRA_ARGS=--pod-infra-container-image=uhub.service.ucloud.cn/google_containers/pause-amd64:3.0\"\nExecStart=,g" /etc/systemd/system/kubelet.service.d/10-kubeadm.conf

```
# Note: This dropin only works with kubeadm and kubelet v1.11+
[Service]
Environment="KUBELET_KUBECONFIG_ARGS=--bootstrap-kubeconfig=/etc/kubernetes/bootstrap-kubelet.conf --kubeconfig=/etc/kubernetes/kubelet.conf"
Environment="KUBELET_CONFIG_ARGS=--config=/var/lib/kubelet/config.yaml"
# This is a file that "kubeadm init" and "kubeadm join" generates at runtime, populating the KUBELET_KUBEADM_ARGS variable dynamically
EnvironmentFile=-/var/lib/kubelet/kubeadm-flags.env
# This is a file that the user can use for overrides of the kubelet args as a last resort. Preferably, the user should use
# the .NodeRegistration.KubeletExtraArgs object in the configuration files instead. KUBELET_EXTRA_ARGS should be sourced from this file.
EnvironmentFile=-/etc/default/kubelet
ExecStart=
ExecStart=/usr/bin/kubelet $KUBELET_KUBECONFIG_ARGS $KUBELET_CONFIG_ARGS $KUBELET_KUBEADM_ARGS $KUBELET_EXTRA_ARGS

```

systemctl daemon-reload
systemctl restart kubelet

3. kubeadm 初始化
kubeadm init --kubernetes-version=1.14.2 \
--apiserver-advertise-address=106.75.241.44 \
--image-repository registry.aliyuncs.com/google_containers \
--service-cidr=10.1.0.0/16 \
--pod-network-cidr=10.244.0.0/16


Your Kubernetes control-plane has initialized successfully!

To start using your cluster, you need to run the following as a regular user:

  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config

You should now deploy a pod network to the cluster.
Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
  https://kubernetes.io/docs/concepts/cluster-administration/addons/

Then you can join any number of worker nodes by running the following on each as root:

kubeadm join 10.8.171.241:6443 --token 415u63.7lsdw2fdurciym4s \
    --discovery-token-ca-cert-hash sha256:8933205d3c45e4bfdc150a45cf6af07e5547a6a0ece67c06771c6d7a2f28a321 



ubectl config set-cluster k8s --server https://10.0.0.11:6443 --certificate-authority=/etc/kubernetes/pki/ca.crt --embed-certs=true --kubeconfig=/var/lib/kubelet/bootstrap-kubeconfig


kubectl config set-credentials test-node --token=abcdef.0123456789abcdef --kubeconfig=/var/lib/kubelet/bootstrap-kubeconfig


kubectl config set-context test-node-bootstrap --cluster kubernetes --user test-node --kubeconfig=bootstrap-kubeconfig


kubectl config use-context test-node-bootstrap --kubeconfig=bootstrap-kubeconfig

1. 

kubectl config set-cluster k8s --kubeconfig=bootstrap.kubeconfig --server=https://10.8.171.241:6443 --certificate-authority=/etc/kubernetes/pki/ca.crt --embed-certs=true

kubectl config set-credentials k8s --token=abcdef.0123456789abcdef --kubeconfig=bootstrap.kubeconfig

kubectl config set-context k8s --kubeconfig=bootstrap.kubeconfig  --cluster=k8s --user=k8s

cfssl gencert \
  -ca=/etc/kubernetes/pki/ca.pem \
  -ca-key=/etc/kubernetes/pki/ca-key.pem \
  -config=ca-config.json \
  -profile=kubernetes \
  apiserver-csr.json | cfssljson -bare ${PKI_DIR}/apiserver


```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  volumes:
  - name: workdir
    hostPath:
      path: /data
    containers:
  containers:
  - name: apline
    image: nginx
    command: [......]
    volumeMounts:
    - name: work
      mountPath: /workdir
###增加init Container####
  initContainers:
 - name: init
    image: busybox
    command: ['sh', '-c', 'touch /.../work;']
    volumeMounts:
    - name: work
      mountPath: /workdir

apiVersion: v1
kind: Secret
metadata:
  name: mysecret
type: Opaque
data:
  username: YWRtaW4=
  password: MWYyZDFlMmU2N2Rm
---
apiVersion: v1
kind: Secret
metadata:
  name: mysecret
type: Opaque
stringData:
  username: user
  password: password
---
apiVersion: v1
kind: Secret
metadata:
  name: mysecret
type: Opaque
data:
  username: YWRtaW4=
stringData:
  username: administrator
---
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - name: mypod
    image: redis
    volumeMounts:
    - name: foo
      mountPath: "/etc/foo"
      readOnly: true
  volumes:
  - name: foo
    secret:
      secretName: mysecret
---
apiVersion: v1
kind: Pod
metadata:
  name: secret-env-pod
spec:
  containers:
  - name: mycontainer
    image: redis
    env:
      - name: SECRET_USERNAME
        valueFrom:
          secretKeyRef:
            name: mysecret
            key: username
      - name: SECRET_PASSWORD
        valueFrom:
          secretKeyRef:
            name: mysecret
            key: password
  restartPolicy: Never
---
apiVersion: v1
kind: Pod
metadata:
  name: test-pd
spec:
  containers:
  - image: k8s.gcr.io/test-webserver
    name: test-container
    volumeMounts:
    - mountPath: /cache
      name: cache-volume
  volumes:
  - name: cache-volume
    emptyDir: {}

```
