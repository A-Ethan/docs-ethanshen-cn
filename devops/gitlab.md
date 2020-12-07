# Gitlab

## git 命令

![](./images/bg2015120901.png)

自己常用的也就这些了

```bash
# git 全局设置
git config --global user.name "A-Ethan"
git config --global user.email "200839608@qq.com"

# git clone
git clone http://github.com/a-ethan/docs-huncloud-cn.git

# git clone password
git clone http://200839608%40qq.com:password@github.com/a-ethan/docs-huncloud-cn.git

# add 暂存区
git add .

# commit 仓库区
git commit -m "update"

# push 远程仓库
git push -u origin master

# push 所有分支
git push -u orgin --all

# 列分支
git branch -a

# 新建分支，更新工作区
git checkout -b v1

# 切分支，更新工作区
git checkout master

# 合并指定分支到当前分支
git merge v1
```

## gitlab安装

[wget yaml](./../yaml/gitlab.yaml)

```yaml
# ns-gitlab
apiVersion: v1
kind: Namespace
metadata:
  name: gitlab
---
 # pvc-gitlab
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: pvc-gitlab
  namespace: gitlab
  labels:
    release: "13.2.4-ce.0"
    app: "gitlab"
spec:
  accessModes: 
  - ReadWriteOnce
  storageClassName: ssd-csi-udisk
  resources:
    requests:
      storage: 20Gi
---
#deploy-portal
apiVersion: apps/v1
kind: Deployment
metadata:
  name: "gitlab"
  namespace: gitlab
  labels:
    release: "13.2.4-ce.0"
    app: "gitlab"
spec:
  replicas: 1
  selector:
    matchLabels:
      release: "13.2.4-ce.0"
      app: "gitlab"
  template:
    metadata:
      labels:
        release: "13.2.4-ce.0"
        app: "gitlab"
    spec:
      containers:
      - name: gitlab
        image: uhub.service.ucloud.cn/ethan/gitlab-ce:13.2.4-ce.0
        imagePullPolicy: IfNotPresent
        ports:
        - name: http
          containerPort: 80
        - name: https
          containerPort: 443
        - name: ssh 
          containerPort: 22
        volumeMounts:
        - name: gitlab
          mountPath: /etc/gitlab
          subPath: config
        - name: gitlab
          mountPath: /var/log/gitlab
          subPath: logs
        - name: gitlab
          mountPath: /var/opt/gitlab
          subPath: data
      volumes:
      - name: gitlab
        persistentVolumeClaim:
          claimName: "pvc-gitlab"
---
# svc-portal
apiVersion: v1
kind: Service
metadata:
  name: "gitlab"
  namespace: gitlab
  labels:
    release: "13.2.4-ce.0"
    app: "gitlab"
spec:
  type: LoadBalancer
  ports:
    - port: 80
      targetPort: 80
      name: http
    - port: 443
      targetPort: 443
      name: https
    - port: 22
      targetPort: 22
      name: ssh
  selector:
    release: "13.2.4-ce.0"
    app: "gitlab"

```