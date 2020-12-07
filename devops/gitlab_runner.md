# Gitlab runner

[gitlab 流水线语法（待整理）](https://docs.gitlab.com/ee/ci/pipelines/)

## 云主机安装runner

```bash
# 加源
curl -L https://packages.gitlab.com/install/repositories/runner/gitlab-runner/script.rpm.sh | sudo bash

# 安装
yum install gitlab-runner -y

# 注册
gitlab-runner register
# 填写信息 访问 http://git.stayai.cn/ethan/java-demo/-/settings/ci_cd
# 地址
http://git.stayai.cn
# token
$(token)
```

在/etc/sudoers加上一行

```
gitlab-runner   ALL=(ALL)       NOPASSWD: ALL
```

增加系统服务

```bash
gitlab-runner install -n gitlab-runner -u root
systemctl start gitlab-runner.service
systemctl enable gitlab-runner.service
```

[Shell executor的文档](https://docs.gitlab.com/runner/executors/shell.html)

[docker executor](https://docs.gitlab.com/runner/executors/docker.html#workflow)

