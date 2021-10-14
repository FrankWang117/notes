# Mac 上安装使用 MongoDB
## 1. 下载 MongoDB
官网下载地址 [点击此处访问](https://www.mongodb.com/download-center/community?jmp=nav)，可以看到：
![mongodb download page](https://raw.githubusercontent.com/FrankWang1991/images/master/ajcDdg.png)
选择适合自己的版本和系统以及想要的 Package 类型，点击 `Download` ,浏览器就会自动开始下载。以下以 macOS 为例，进行安装和运行 MongoDB
## 2. 安装
下载完成之后，打开文件下载位置，进行安装操作。
### 2.1 解压文件并重命名

进入 `Finder` 找到刚才下载的 MongoDB 压缩包位置，并将**解压后的文件夹**重命名为 `mongodb` ：
![mongodb 压缩包与重命名文件夹](https://raw.githubusercontent.com/FrankWang1991/images/master/ccNPa2.png) 
### 2.2 移动 `mongodb` 文件夹到 `/usr/local` 目录下
在 `Finder` 任意窗口内，使用组合快捷键 `command ⌘ + shift ⇧ + G`  打开快速访问框，输入 `/usr/local` 进入此目录，将重命名后的文件夹 `mongodb` 复制入此文件夹：
![组合快捷键功能](https://raw.githubusercontent.com/FrankWang1991/images/master/niSY1G.png)
移动之后：
![移动后的 /usr/local 文件夹内容](https://raw.githubusercontent.com/FrankWang1991/images/master/YNQufr.png)
### 2.3 配置环境变量
mongodb 需要我们配置环境变量后，相应的命令才能够被正确执行。
需要我们在根目录 `~` 中的 `.bash_profile` 文件添加 mongodb 环境变量参数 :
`export PATH=${PATH}:/usr/local/MongoDB/bin` .
添加的方式为：
打开命令行，输入
``` command
cd ~
```
进入根目录：
``` command
vim .bash_profile
```
![vim 命令打开配置文件](https://raw.githubusercontent.com/FrankWang1991/images/master/BSqbnu.png)

进入相应的文件内，使用 `vim` 命令添加
``` command
export PATH=${PATH}:/usr/local/MongoDB/bin
```
保存后（[如何使用 vim 保存当前修改](https://www.jianshu.com/p/0009bf462f15))，再在命令行中执行：
``` command
source .bash_profile
```
命令，使环境变量配置生效。  
### 2.4 创建 data/db 文件夹
使用上述进入 `/usr/local` 的组合快捷键  `command ⌘ + shift ⇧ + G`   进入，并创建 `data/db` 文件夹📁：
![data/db 文件夹地址](https://raw.githubusercontent.com/FrankWang1991/images/master/T3gxc0.png) 

## 3. 启动服务  

使用 terminal 工具输入命令 
``` command 
sudo mongod
```
启动服务。如果遇到报错，请检查是否遗漏 sudo 命令。输入密码就可以看到启动日志了：
![启动日志](https://raw.githubusercontent.com/FrankWang1991/images/master/Ws21OS.png) 

再重新打开一个 terminal 工具（`command + N`）输入 ：
``` command  
mongo
```
命令就可以进入 mogodb 环境了.
## 4. 关闭服务
想要关闭 MongoDB 服务，首先需要使用命令 
``` command 
use admin
```
切换到 admin 用户，然后，使用命令：
``` command
db.shutdownServer()
```
关闭 MongoDB 服务。