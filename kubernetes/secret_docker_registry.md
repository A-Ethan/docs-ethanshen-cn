# 使用镜像仓库

```bash
kubectl create secret docker-registry pull-secret \
--docker-server=uhub.service.ucloud.cn \
--docker-username=xxx@qq.com \
--docker-password=xxxpassword
```