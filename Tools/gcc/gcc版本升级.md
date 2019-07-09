## gcc版本升级

### 升级实例
> 系统： Ubuntu14.04
> 当前版本：4.8
> 目标版本: 5.5

### 升级步骤
- sudo add-apt-repository ppa:ubuntu-toolchain-r/test
- sudo apt-get update
- sudo apt-get install g++-5

#### 链接到相应版本的gcc
- l /usr/bin/gcc*
- gcc -v

> sudo rm /usr/bin/gcc /usr/bin/g++   移除原来的gcc和g++
> sudo ln -s /usr/bin/g++-5 g++   链接到5.0版本的g++
> sudo ln -s /usr/bin/gcc-5 gcc   链接到5.0版本的gcc



