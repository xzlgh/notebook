# Linux系统

## Linux系统开发必备环境以及开发工具

### 必备开发工具以及环境
- gcc\g++
- vim
- cmake
- gdb调试器（用于调试，可不安装）
- vscode
- svn
- git
- mysql
- ssh
- jdk

### 基础操作

- sudo apt-get update 更新
- sudo apt-get install subversion 安装svn
```
- apt-get install subversion
- mkdir -p /data/svn
- svnadmin create /data/svn/{pro_name}
- vim /data/svn/{pro_name}/conf/svnserve.conf

- anon_access = none   #匿名用户无法访问
- auth_access = write    #授权用户可写
- password_db = password    #用密码文件- - - password控制权限
- auth_db = authz    #访问控制文件

- svnserve -d -r /data/svn
- #netstat -antp | grep svnserve

- vim /data/svn/pdc/conf/passwd
- vim /data/svn/pdc/conf/auth
```
- sudo apt-get install openssh-server libssl-dev libreadline-dev cmake  安装ssh-server(ssh服务)\cmake\libssl（加密模块库）
- sudo apt-get install mysql-server  安装mysql服务器
- sudo apt-get install default-jdk 安装jdk
- sudo apt-get install valgrind 安装valgrind,它是一款用于内存调试、内存泄漏检测以及性能分析的软件开发工具
  - valgrind --leak-check=full ./*.o（执行文件）
- sudo apt-get install vim 安装vim
> vim配置
```
<!-- 设置编码格式UTF-8 -->
- sudo vim /etc/profile
- 在profile文件中添加：export LC_ALL="en_US.UTF-8" 
- source /etc/profile 使得profile文件生效

<!-- 配置vim -->
- sudo vim /etc/vim/vimrc.local
配置内容如下：
set ai		//设置文本编辑的时候自动对齐，取消自动对齐的是noai
set history=100	//设置历史文件中记录的行数为100行
set nu		//设置行数显示
syntax on		//语法高亮
set showcmd		//高亮显示命令
set encoding=utf-8		//设置默认编码方式		
set fileencoding=utf-8	//设置默认解码方式
set autoindent			//继承前一行缩进方式
set cindent		//使用c程序样式缩进
set smarttab		//为c程序提供自动缩进一个tab键
set smartindent	//为c程序提供自动缩进
set expandtab		//用空格符代替制表符，如果不要让他代替那么就在前面加一个no;		
set tabstop=4		//制表符为4个字节
set shiftwidth=4	//统一缩进为4
autocmd FileType make setlocal noexpandtab		//采用类似C语言的缩进风格
set nobackup		//不要备份文件
filetype on		//侦测文件类型
filetype plugin on		//载入文件类型插件
filetype indent on		//为特定文件载入相关缩进
set showmatch		//高亮显示匹配的括号

- source /etc/vim/vimrc.local 使配置立即生效
```

- vim ~./bashrc
  - export SVN_DEITOR=vim 配置SVN环境变量

- sudo sysctl -p 使生效
- sudo sysctl -a 显示所有
- sudo vim /etc/sysctl.conf
> 文件中加入以下代码：
net.core.somaxconn = 512
net.ipv4.tcp_tw_recycle = 1


