# Node节点Pod控制

每个节点kubelet控制的参数

## 登录节点执行

```bash
# 修改kubelet参数
vim /etc/kubernetes/kubelet.conf
```

```yaml
maxpods: 110 # 单节点最大的pod数量
podsPerCore: 8 # 单核最大的pod数量
```

```bash
# 重启kubelet
systemctl restart kubelet
```

## pssh批量替换

安装pssh

```bash
yum install pssh -y
```

创建主机列表

```bash
#全部主机节点
vim hosts.txt
#master节点
vim master.txt 
#node节点
vim node.txt
```

文件内容格式

```
root@10.10.10.10
root@10.10.10.11
root@10.10.10.12
root@10.10.10.13
```

批量替换命令，将aaa，bbb替换至所有节点

```bash
# 内容不对，使用记得修改
pssh -Ah hosts.txt -x '-o StrictHostKeyChecking=no' -i 'sed -i -e "s/.*UCLOUD_ACCESS_PUBKEY=.*/UCLOUD_ACCESS_PUBKEY=aaa/g" -e "s/.*UCLOUD_ACCESS_PRIKEY=.*/UCLOUD_ACCESS_PRIKEY=bbb/g" -e "s#/root#/var/log/ucloud#g" /etc/kubernetes/ucloud'
```

重启kubelet

```bash
# 重启node节点kubelet服务
pssh -Ah node.txt -x '-o StrictHostKeyChecking=no' -i 'sudo systemctl restart kubelet'
```

### pssh 常用命令


```
--version：查看版本
--help：查看帮助，即此信息
-h：主机文件列表，内容格式"[user@]host[:port]"
-H：主机字符串，内容格式"[user@]host[:port]"
-l：登录使用的用户名
-p：并发的线程数【可选】
-o：输出的文件目录【可选】
-e：错误输入文件【可选】
-t：TIMEOUT 超时时间设置，0无限制【可选】
-O：SSH的选项
-v：详细模式
-A：手动输入密码模式
-x：额外的命令行参数使用空白符号，引号，反斜线处理
-X：额外的命令行参数，单个参数模式，同-x
-i：每个服务器内部处理信息输出
-P：打印出服务器返回信息
```