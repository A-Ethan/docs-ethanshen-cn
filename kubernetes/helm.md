# HELM 常用命令

> 有空再搞

通过 **helm fetch** 命令获取到安装包
```
helm fetch stable/tomcat --untar
```

通过 **helm inspect** 命令查看可以配置的选项
```
helm inspect values stable/tomcat
```

--debug

--dry-run