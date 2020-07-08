# WSL安装oh-my-zsh/JDK/Tomcat/Mysql

---

## 一、安装zsh

### 1、更新和升级

```shell
$ sudo apt-get update
$ sudo apt-get upgrade
```

### 2、安装zsh

```shell
$ sudo apt-get install zsh
```

### 3、设置默认 shell 为 zsh

```shell
$ chsh -s $(which zsh)
```

### 4、检查是否成功

```shell
$ echo $SHELL
```

### 5、安装oh-my-zsh

```shell
$ sh -c "$(curl -fsSL https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
// 或者
$ sh -c "$(wget https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh -O -)"
```

### 6、上述安装方法报错怎么办？

```shell
$ git clone git://github.com/robbyrussell/oh-my-zsh.git ~/.oh-my-zsh
$ cp ~/.oh-my-zsh/templates/zshrc.zsh-template ~/.zshrc
$ ~ chsh -s /bin/zsh  # 切换 zsh
```

再去安装oh-my-zsh即可。

## 二、zsh主题

### 1、主题更换

打开.zshrc文件：

```shell
$ vim ~/.zshrc
```

### 2、修改XAH_THEME的值：

```shell
ZSH_THEME="ys"
```

## 三、zsh插件

### 1、下载语法高亮插件

```shell
$ git clone git://github.com/zsh-users/zsh-syntax-highlighting $ZSH_CUSTOM/plugins/zsh-syntax-highlighting
```

### 2、下载自动提示插件

下载该插件到.oh-my-zsh的插件目录

```shell
$ git clone git://github.com/zsh-users/zsh-autosuggestions $ZSH_CUSTOM/plugins/zsh-autosuggestions
```

### 3、加入自有插件

```properties
plugins=(git last-working-dir zsh-syntax-highlighting zsh-autosuggestions)
```

### 4、更新配置文件，使得插件生效

```shell
$ source ~/.zshrc
```

### 5、插件权限安全问题？

![image-20200610180915144](C:\Users\49769\AppData\Roaming\Typora\typora-user-images\image-20200610180915144.png)

```shell
$ compaudit | xargs chmod g-w,o-w
```

或者(不推荐)在该配置文件 .zshrc 中添加如下语句即可绕过权限验证：

```properties
ZSH_DISABLE_COMPFIX="true"
```

### 6、遇到"Permission denied"？

授权后再更新配置文件：

```shell
$ sudo chmod -R 777 /home
```

此时就可以在该路径下进行一系列的操作。

`注意：该授权操作不能直接对/usr进行授权，建议对子目录进行授权，不然访问权限会被锁死！`


## 四、JDK

### 1、下载并移动至目录

到 oracle 官网下载 linux-64 位压缩包，并将文件从windows目录移动到linux下：

```shell
$ sudo mv /mnt/c/Users/49769/Downloads/jdk-8u251-linux-x64.tar.gz /usr/local/jdk1.8
```

### 2、解压缩

```shell
$ sudo tar -xvzf jdk-8u251-linux-x64.tar.gz 
```

### 3、删除压缩包

```shell
$ sudo rm -f jdk-8u251-linux-x64.tar.gz
```

### 4、添加以下语句到 .zshrc，配置环境变量

 因为.zshrc控权到用户，更加安全，将JDK配置到该文件内。

进入配置文件：

```shell
$ vi ~/.zshrc
```

添加配置：

```properties
export JAVA_HOME=/usr/local/jdk1.8 
export JRE_HOME=${JAVA_HOME}/jre  
export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib  
export PATH=${JAVA_HOME}/bin:$PATH 
```

### 5、更新配置文件

```shell
$ source ~/.zshrc
```

### 6、测试

```shell
$ java -version
```

## 五、Tomcat

### 1、下载并移动至目录

到 tomcat 官网下载 tomcat9 的压缩包，并将文件从windows目录移动到linux下：

```shell
$ sudo mv /mnt/c/Users/49769/Downloads/apache-tomcat-9.0.36.tar.gz /usr/tomcat9
```

### 2、解压缩

若进不去bin目录，则先授权

```shell
$ sudo tar -xvzf apache-tomcat-9.0.36.tar.gz
```

### 3、删除压缩包

```shell
$ sudo rm -f apache-tomcat-9.0.36.tar.gz
```

### 4、修改端口

修改tomcat中的conf/server.xml配置文件即可。

### 5、修改配置文件

/usr/tomcat9/apache-tomcat-9.0.36/bin/`startup.sh`最后一行前加入：

/usr/tomcat9/apache-tomcat-9.0.36/bin/`shutdown.sh`最后一行前加入：

```properties
export JAVA_HOME=/usr/local/jdk1.8 
export JRE_HOME=${JAVA_HOME}/jre  
export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib  
export PATH=${JAVA_HOME}/bin:$PATH
export TOMCAT_HOME=/usr/tomcat9/apache-tomcat-9.0.36
```

### 5、启动

```shell
$ ./startup.sh
```

### 6、环境变量配置

注意：WSL和Windows的环境变量是共享的，导致配置的各种参数不起作用。使用`${PATH}`在wsl中查看path环境变量，默认包含win10的系统变量。

运行regedit进入注册表后，修改注册表可解决：

```properties
HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Lxss{GUID}\Flags Flags从更改7为5。
```

若不管用，则管理员运行powershell重启wsl

```shell
net stop LxssManager 
net start LxssManager
```

## 六、远程ssh登录

### 1、重装ssh服务

```shell
$ sudo apt-get remove openssh-server
$ sudo apt-get install openssh-server
```

### 2、配置ssh的配置文件

```shell
$ sudo vim /etc/ssh/sshd_config
```

写入：

```properties
Port 2222   		 		   # 设置ssh的端口号, 由于22在windows中有别的用处, 尽量不修改系统的端口号
PermitRootLogin yes  		   # 可以root远程登录
PasswordAuthentication yes     # 密码验证登录
AllowUsers wq57fan 			   # 远程登录时的用户名,wq57fan密码是root
```

### 3、重启ssh服务

```shell
$ sudo service ssh restart
```

### 4、开放端口

这个时候你自己电脑上的xhell是可以连接的了，但是想要在其他的计算机上访问，就需要系统开放端口了。这时需要到防火墙设置开放2222端口，步骤：

```properties
防火墙->高级设置->入站规则->新建规则->端口->下一步选择tcp，本地特定端口为2222即可
```

### 5、远程登录

现在可以使用ssh客户端连接WSL了：

```shell
$ ssh -p 2222 wq57fan@127.0.0.1
```

## 七、Mysql

### 1、先更新apt包

```shell
$ sudo apt-get update   #只检查，不更新
$ sudo apt-get update --fix-missing
$ sudo apt-get upgrade  #更新已安装的软件包
```

### 2、安装Mysql

到http://dev.mysql.com/downloads/repo/apt/自行下载之后放到某个文件夹下执行后再安装

```shell
$ sudo mv /mnt/c/Users/49769/Downloads/mysql-apt-config_0.8.15-1_all.deb /usr
$ sudo dpkg -i mysql-apt-config_0.8.15-1_all.deb
#服务端
$ sudo apt-get install mysql-server 
#客户端
$ sudo apt-get install mysql-client  
#程序编译时链接的库
$ sudo apt-get install libmysqlclient-dev
#安装需要的依赖库
$ sudo apt-get install apparmor-profiles
```

安装过程中会提示设置密码，安装完成之后可以使用如下命令来检查是否安装成功：

```shell
$ sudo netstat -tap | grep mysql
```

通过上述命令检查之后，如果看到有mysql 的socket处于 listen 状态则表示安装成功。

### 3、修改配置文件

因为win10系统已经装过mysql了，端口号要改下，还有其他的配置。

安装好之后会创建如下目录：

* /usr/bin                  客户端程序和mysql_install_db
* /var/lib/mysql        数据库和日志文件
* /var/run/mysqld    服务器
* /etc/mysql              配置文件my.cnf
* /usr/share/mysql   字符集，基准程序和错误消息
* /etc/init.d/mysql    启动mysql服务器

### 4、登陆Mysql

本地登陆：

```shell
$ mysql -uroot -proot 
```

远程登录(-h为远程IP，-P为端口号)：

```shell
$ mysql -h 127.0.0.1 -P 3306 -uroot -proot
```

### 5、卸载

```shell
#查看从MySQL APT存储库安装的软件包列表：
$ dpkg -l | grep mysql | grep ii
#首先使用以下命令删除MySQL服务器：
$ sudo apt-get remove mysql-server
$ sudo apt-get remove mysql-common
#然后，删除随MySQL服务器自动安装的任何其他软件：
$ sudo apt-get autoremove
#删除剩余依赖项
$ sudo apt-get autoremove --purge mysql-apt-config
#清理残留数据：
$ dpkg -l |grep ^rc|awk '{print $2}' |sudo xargs dpkg -P
```

### 6、管理

说明：通过这种方式安装好之后开机自启动都已经配置好，和命令行上的环境变量，无需手动配置。

```shell
#启动
$ sudo service mysql start
#停止
$ sudo service mysql stop
#查看服务状态
$ sudo service mysql status
```

### 7、安装时出错

#### 1.`E: Sub-process /usr/bin/dpkg returned an error code (1)`

解决办法：

将info文件夹更名

```shell
$ sudo mv /var/lib/dpkg/info /var/lib/dpkg/info.bk
```

新建一个新的info文件夹

```shell
$ sudo mkdir /var/lib/dpkg/info
```

安装修复

```shell
$ sudo apt-get update
$ sudo apt-get install -f 
```

执行完上一步操作后，在info文件夹下生成一些文件，现将这些文件全部移到info.bk文件夹下

```shell
$ sudo mv /var/lib/dpkg/info/* /var/lib/dpkg/info.bk
```

把自己新建的info文件夹删掉

```shell
$ sudo rm -rf /var/lib/dpkg/info
```

恢复原有info文件夹，修改名字

```shell
$ sudo mv /var/lib/dpkg/info.bk /var/lib/dpkg/info
```

现在能够正常安装了，还遇到了相关文件的缺失直接索性卸载重装

```shell
$ sudo apt-get --purge remove libapache2-mod-svn 
$ sudo apt-get --purge remove libapache2-svn 
$ sudo apt-get autoremove
```

再安装一遍Mysql即可成功。

#### 2.`No directory, logging in with HOME=/`

解决办法：

```shell
$ sudo usermod -d /var/lib/mysql/ mysql
```

#### 3.`Starting MySQL database server mysqld [fail]`

解决办法：

```shell
$ sudo chown -R mysql:mysql /var/lib/mysql
$ service mysql start
$ su   #即可登录root账户
```

