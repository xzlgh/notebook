# Linux系统操作

## 基础命令

推荐链接： https://blog.csdn.net/hezemin0315/article/details/50666803

```
1. find -name fileName  查找当前目录下的某个文件是否存在，输出该文件的路径

2. lsof -i:8081   查找端口8081被那个进程占用

3. kill pid \ kill -9 pid   杀掉进程\使用最高权限杀掉进程
```

## 系统host

### 修改\增加配置

- sudo vim /etc/hosts

### 修改docker配置

- sudo vim /etc/docker/daemon.json

### 重启Docker配置

- sudo systemctl daemon-reload
- sudo systemctl restart docker

### Ubuntu工作区切换快捷键失效

> 解决方案：

- sudo apt-get install dconf-tools
- dconf reset -f /org/compiz/
- setsid unity
- 重启电脑，重新设置快捷键即可