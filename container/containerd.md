# Containerd（还没玩明白）
https://kubernetes.io/zh/docs/setup/production-environment/container-runtimes/
学会接受，学会享受。ps：新手还是推荐docker呀。

> centos8.2 
> 
> root权限
> 
> root目录


## 安装

官网文档给的需要自己打包，可以直接go程序对接。[文档链接](https://containerd.io/docs/getting-started/)

> 我就图省事儿了，根据要求自己调整安装目录吧

```bash
# 下载二进制包
wget https://github.com/containerd/containerd/releases/download/v1.4.3/containerd-1.4.3-linux-amd64.tar.gz
# tar解压缩
tar xvf containerd-1.4.3-linux-amd64.tar.gz
```

### cmd补全

```bash
# 下载补全文件
wget https://raw.githubusercontent.com/containerd/containerd/master/contrib/autocomplete/ctr
# 补全
source /root/ctr
# 永久补全
echo "source /root/ctr" >> ~/.bashrc
```
## cmd初体验

### 写一些常用命令在前面



### cmd初体验

ctr为客户端工具，官方不保证其可用性，我也是醉了，估计作为k8s的CRI挺好，单体使用的话，这一句立马就完了，**新手不友好**。

```bash
# ctr命令
$ ctr -h
```

```bash
NAME:
   ctr - 
        __
  _____/ /______
 / ___/ __/ ___/
/ /__/ /_/ /
\___/\__/_/

containerd CLI


USAGE:
   ctr [global options] command [command options] [arguments...]

VERSION:
   v1.4.3

DESCRIPTION:

ctr is an unsupported debug and administrative client for interacting with the containerd daemon. Because it is unsupported, the commands,options, and operations are not guaranteed to be backward compatible or stable from release to release of the containerd project.

COMMANDS:
   plugins, plugin            provides information about containerd plugins
   version                    print the client and server versions
   containers, c, container   manage containers
   content                    manage content
   events, event              display containerd events
   images, image, i           manage images
   leases                     manage leases
   namespaces, namespace, ns  manage namespaces
   pprof                      provide golang pprof outputs for containerd
   run                        run a container
   snapshots, snapshot        manage snapshots
   tasks, t, task             manage tasks
   install                    install a new package
   oci                        OCI tools
   shim                       interact with a shim directly
   help, h                    Shows a list of commands or help for one command

GLOBAL OPTIONS:
   --debug                      enable debug output in logs
   --address value, -a value    address for containerd's GRPC server (default: "/run/containerd/containerd.sock") [$CONTAINERD_ADDRESS]
   --timeout value              total timeout for ctr commands (default: 0s)
   --connect-timeout value      timeout for connecting to containerd (default: 0s)
   --namespace value, -n value  namespace to use with commands (default: "default") [$CONTAINERD_NAMESPACE]
   --help, -h                   show help
   --version, -v                print the version
```
NAME:
   ctr images pull -k service.ucloud.cn/cicd/harbor-db:v2.0.0 
   
   - pull an image from a remote

USAGE:
   ctr images pull [command options] [flags] <ref>

DESCRIPTION:
   Fetch and prepare an image for use in containerd.

After pulling an image, it should be ready to use the same reference in a run command. As part of this process, we do the following:

1. Fetch all resources into containerd.
2. Prepare the snapshot filesystem with the pulled resources.
3. Register metadata for the image.


OPTIONS:
   --skip-verify, -k       skip SSL certificate validation
   --plain-http            allow connections using plain HTTP
   --user value, -u value  user[:password] Registry user and password
   --refresh value         refresh token for authorization server
   --hosts-dir value       Custom hosts configuration directory
   --tlscacert value       path to TLS root CA
   --tlscert value         path to TLS client certificate
   --tlskey value          path to TLS client key
   --snapshotter value     snapshotter name. Empty value stands for the default value. [$CONTAINERD_SNAPSHOTTER]
   --label value           labels to attach to the image
   --platform value        Pull content from a specific platform
   --all-platforms         pull content and metadata from all platforms
   --all-metadata          Pull metadata for all platforms
