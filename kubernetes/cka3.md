1. 创建一个 `daemonset` 使用`toleration`，使 dedicated=special-user:NoSchedule 这个`taint` 所在的node上可以调度实例。
get

2. 创建一个 `secret`，分别挂载到pod的文件和env下面去。
get

3. 查找出某个 `service` 对应的 pod 列表，使用 `--selector` 查找出对应的pod追加到文件。
get

4. 创建一个 `persistentVolume`，使用 hostpath 挂载到指定路径，指定capacity 和读写模式。


5. 使用 `kubectl logs` 命令找出名为 foo 的pod的某一行日志追加到文件。
get

6. 创建一个 `deployment`使用 `--record` 参数记录创建的revision，然后修改这个deployment，最后还原回第一版。


7. 根据一个pod的yaml增加 `initContainer`字段,创建一个文件, 判断这个文件是否存在，不存在就退出.
get

8. 找出指定label的 top pod, 使用 `kubectl top --selector`命令找出cpu占用最高的pod名称,记录到指定文件里.
get

9. 根据 pv 排序, `kubectl get pc --sort-by=.spec.capacity.storage` 追加到指定文件.
get

10. 创建一个pod 名叫 nginx-dns , 并且创建同名的service, 使用`nslookup` 命令解析 pod 和 service 并追加到不同文件.
get

11. `etcdctl snapshot save`命令的使用,save到指定文件.
get

12. 创建一个pod并且指定nodeSelector.
get

13. 修改从节点的 `kubelet.service`, 增加`--pod-manifest-path=`参数指定static pod 字段, 并且创建一个pod使用jenkins镜像,名称为app的yaml文件,放到 /etc/kubernetes/manifests/ 目录下.

*  14. 创建deployment并且挂载**emptydir.**


15. kubelet.service 没有正常启动导致节点notReady的问题, 执行 `systemctl start kubelet.service` 和 `systemctl enable kubelet.service` 就可以了.


16.服务没有正常启动,题目会提示集群不正常,需要自己测试找出问题原因并解决，这个很多人说是--pod-manifest-path 目录错误，这个大家在找人确认一下.



17. 将一个节点设置为不可调度并且驱逐上面所有的pod, 使用 `kubectl drain nodename --ignore-daemonsets --delete-local-data` 命令设置node为不可调度并且忽略指定的警告.
get

18. 找出 ready 状态的node, 并且上面有指定taint标签的个数,把这个数字写到文件（）.
get

赵威：这个题我写的“3”，但是我感觉我切集群有问题，当时3个节点都是好的，感觉不正常，大家怎么做的？
19.创建一个POD ，包含多个容器，nginx,redis
get