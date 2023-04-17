### 说明:
现有常用的版本控制管理工具（svn、git）,其实只能跟踪文本文件的改动,比如TXT、网页、所有的程序代码等等, git也不例外。如Windows下的word改动，具体改动了哪里,版本控制工具是不知道的。

### 推荐学习链接：

1. Pro Git: https://gitee.com/progit/
2. 廖雪峰教程：  https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000


# 安装

## Linux上面的安装

1. 打开命令行窗口；
2. 输入“git”；
3. 如果已经安装会提示你该端口已经被占用；如果还没安装，会给出相应的安装提示；
4. 如果没有安装，一般情况下。可以直接在命令行中输入：sudo apt-get install git
5. 如果安装成功了，输入：git 会出现git的使用方式的说明。

## Windows上面安装

为了方便可以直接下载已经帮我们配置好的环境，下载，执行可执行文件即可。从https://git-for-windows.github.io下载msysgit，选择默认安装即可。

## Max OS上面安装

1. 是安装homebrew，然后通过homebrew安装Git，具体方法请参考homebrew的文档：http://brew.sh/。

2. 第二种方法更简单，也是推荐的方法: 直接从AppStore安装Xcode，Xcode集成了Git，不过默认没有安装，你需要运行Xcode，选择菜单“Xcode”->“Preferences”，在弹出窗口中找到“Downloads”，选择“Command Line Tools”，点“Install”就可以完成安装了。

# 工作中常用到的git命令

* 创建一个空目录作为git的‘home’, 给一个可以存储的地方，以后这个目录就是可以用git来管理的仓库。

* git init —— 把一个空目录变成git可以管理的仓库

* ls -ah —— 检查是否初始化成功，如果能够显示.git文件，则说明已经init成功

* 设置仓库的user.name和user.email, 它们是用来表示需要上传到的远程仓库位置
  - git config --global user.name 'username' —— 设置username为你的需要上传的git的用户名
  - git config --global user.email 'email@demo.com' —— 设置你远程git上的邮箱
  - git config -global user.password 'demo1234' —— 设置你提交内容时的密码

* git pull [origin master] —— 拉远程上面[master分支]的代码

* git status —— 查看分支状态

* git log —— 查看提交记录

* git add 文件名 || * —— 添加需要跟踪的文件

* git commit -m "提交说明" —— 提交文件到本地仓库

* git push [origin 分支名] —— 提交到的远程（可以指定具体的分支）

* git branch dev(分支名) —— 创建分支

* git branch —— 查看现有的分支

* git checkout 分支名 —— 切换分支

* git merge --no-ff -m dev（分支）—— 当前在master分支合并dev分支上面的代码到当前的master分支上

* git stash —— 保存当前的工作现场

* git stash list —— 查看保存的工作现场

* git stash pop —— 默认恢复上一次保存的工作现场，并且删除工作现场

* git stash apply —— 如果有多个保存的现场时，需要制定恢复哪一个现场，如： stash@{0}

* git stash drop —— 手动删除里面的工作现场保存

* git branch -D dev —— 强行删除dev分支

* git diff 文件名 —— 查看文件修改前后不同的地方

* git reset --hard commit-id(commit的版本号) —— 返回到指定提交的版本，很少用到，只有到代码被覆盖或者共同开发人员出现冲突时有可能需要使用到

* git reset --hard HEAD^ —— 回退到上一个版本（不推荐使用）

> 远程仓库修改url

* git remote -v —— 查看远程仓库源

* git remote rm origin —— 删除远程仓库

* git remote add origin 地址 —— 添加远程仓库地址

* git branch --set-upstream-to=origin/master master 本地仓库关联到远程仓库

* git push -u origin master 提交所有代码到远程仓库

> 从远程仓库拉取某个分支代码且在本地创建分支

* git pull

* 1. git checkout -b dev origin/dev —— 拉取远程的dev分支上面的代码到本地dev分支，这种方式会关联到远程分支，会自动切换到新建的分支

* 2. git fetch orgin dev:dev  这种方式不会关联到远程分支，也不会自动切换本地的新建分支

> 打tag

* git tag v1.0.0 —— 标记当前代码版本为1.0.

* git push tag v1.0.0 —— 将tag推送到远程的git上

# 经验&&问题

## 使用ssh的方式克隆代码的过程（这里列举Linux系统 —— Ubuntu）

### ssh秘钥生成

```
例如：
ssh-keygen -t rsa -f ~/.ssh/id_rsa -C "test@qq.com"
解释:
    - ssh-keygen是生成秘钥的命令
    - rsa是秘钥生成方式
    - ~/.ssh/id_rsa 生成的秘钥文件
    - -C 后面是生成秘钥的邮件
重置：也可以用这个命令，原理是覆盖原来的秘钥，出现y/n提示的时候选择y即可覆盖，重置秘钥
```

### 将秘钥放到远程gitlab上面

```
1. 查看本地的公钥
- cd ~/.ssh
- ls
- cat id_rsa.pub  查看公钥
- 拷贝公钥
2. 在网页上登录gitlab账户，选择右侧的个人中心中->设置->ssh秘钥，将刚刚拷贝的秘钥粘贴到秘钥粘贴区域。

> 以上步骤完成后，即完成了ssh秘钥的生成和黏贴过程
```

#### clone一下代码

* git clone 远程代码的git地址

##### 出现如下错误：

```
1. 提示输入密码，但是却一直不成功（即使密码正确）
qydev@qydev009:~/workspace$ git clone git@gitlab2018:caiqingfeng/jtznewapproval.git
正克隆到 'jtznewapproval'...
sign_and_send_pubkey: signing failed: agent refused operation
git@gitlab2018's password: 
Permission denied, please try again.
git@gitlab2018's password: 
Permission denied, please try again.
git@gitlab2018's password: 
Permission denied (publickey,password).
fatal: Could not read from remote repository.

Please make sure you have the correct access rights
```

##### 分析错误原因：

这种情况可能是git的秘钥重置过后还没有生效，可以执行以下命令使得秘钥生效：

* eval "$(ssh-agent -s)"

* ssh-add

## push代码时403错误

#### 错误信息

```
remote: Permission to xzlgh/react_record.git denied to frontendkevin.
fatal: unable to access 'https://github.com/xzlgh/react_record.git/': The requested URL returned error: 403
```

#### 错误原因： 没有权限或者密码错误。

#### 解决办法

```
- vim .git/config —— 修改git本地仓库配置
- 修改
  [remote "origin"]
  url = https://github.com/xzlgh/react_record.git
  为：
  [remote "origin"]
  url = https://xzlgh@github.com/xzlgh/react_record.git
  
- 重新push，此时会提示输入密码，再重新输入正确的密码即可
```


#### 警告

```bash
hint: Pulling without specifying how to reconcile divergent branches is
hint: discouraged. You can squelch this message by running one of the following
hint: commands sometime before your next pull:
hint: 
hint:   git config pull.rebase false  # merge (the default strategy)
hint:   git config pull.rebase true   # rebase
hint:   git config pull.ff only       # fast-forward only
hint: 
hint: You can replace "git config" with "git config --global" to set a default
hint: preference for all repositories. You can also pass --rebase, --no-rebase,
hint: or --ff-only on the command line to override the configured default per
hint: invocation.
```


#### 撤回已经push的代码
- git log查看commit
- git reset --soft commitId. 软撤回，会保留提交前代码修改
- 或 git reset --hard commitId 强制舍弃提交


#### 如果在错误的分支上修改了代码
- 首先撤销add
- git stash
- git checkout 正确分支名
- git stash pop




