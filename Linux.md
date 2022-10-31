# Linux部署node接口



1. 使用SSH连接服务器

   window 安装SSH（安装地址） [mls-software.com](http://www.mls-software.com/opensshd.html)

   安装过git也自带SSH

   也可以用Xshell代替SSH

   Macbook 免安装自带

2. 使用方式

   打开终端

   ssh 账号默认root@服务器地址 例子 ssh root@123.456.789 然后选择Y ，然后输入密码即可

![img](https://img-blog.csdnimg.cn/2beae3580b8446a4a889215bac17d63e.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP5ruhenM=,size_20,color_FFFFFF,t_70,g_se,x_16)

 ![img](https://img-blog.csdnimg.cn/b71b69d7b6844945b93e1b2c020c7466.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP5ruhenM=,size_20,color_FFFFFF,t_70,g_se,x_16)

 显示这个代表连接成功

3. 基本命令

```
ls // 用来查看当前的目录的文件
```

![img](https://img-blog.csdnimg.cn/effe3b94f7ea44f6b6334e6f61e52fd0.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP5ruhenM=,size_20,color_FFFFFF,t_70,g_se,x_16)

```
cd //  用来切换目录
```

```
touch index.js // 用来创建文件
```

![img](https://img-blog.csdnimg.cn/e0311d0f73924204a24c85c929c12202.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP5ruhenM=,size_20,color_FFFFFF,t_70,g_se,x_16)

```
wget // 用来下载一些东西
```

nodejs 下载地址[下载 | Node.js 中文网](http://nodejs.cn/download/current/)

执行以下命令下载Linux版本node

```
wget https://cdn.npmmirror.com/binaries/node/v14.19.1/node-v14.19.1-linux-x64.tar.xz
```

下载完成之后解压

```
tar
-c: 压缩
-x：解压
-t：查看内容
-r：向压缩归档文件末尾追加文件
-u：更新原压缩包中的文件

// 这五个是独立的命令，压缩解压都要用到其中一个，可以和别的命令连用但只能用其中一个。下面的参数是根据需要在压缩或解压档案时可选的。

-z：有gzip属性的
-j：有bz2属性的
-Z：有compress属性的
-v：显示所有过程
-O：将文件解开到标准输出
```

下面的参数-f是必须的

-f: 使用档案名字，切记，这个参数是最后一个参数，后面只能接档案名。

\# tar -cf all.tar *.jpg 
这条命令是将所有.jpg的文件打成一个名为all.tar的包。-c是表示产生新的包，-f指定包的文件名。 

\# tar -rf all.tar *.gif 
这条命令是将所有.gif的文件增加到all.tar的包里面去。-r是表示增加文件的意思。 

\# tar -uf all.tar logo.gif 
这条命令是更新原来tar包all.tar中logo.gif文件，-u是表示更新文件的意思。 

\# tar -tf all.tar 
这条命令是列出all.tar包中所有文件，-t是列出文件的意思 

\# tar -xf all.tar 
这条命令是解出all.tar包中所有文件，-x是解开的意思 

**压缩**
tar –cvf jpg.tar *.jpg //将目录里所有jpg文件打包成tar.jpg
tar –czf jpg.tar.gz *.jpg  //将目录里所有jpg文件打包成jpg.tar后，并且将其用gzip压缩，生成一个gzip压缩过的包，命名为jpg.tar.gz
tar –cjf jpg.tar.bz2 *.jpg //将目录里所有jpg文件打包成jpg.tar后，并且将其用bzip2压缩，生成一个bzip2压缩过的包，命名为jpg.tar.bz2
tar –cZf jpg.tar.Z *.jpg  //将目录里所有jpg文件打包成jpg.tar后，并且将其用compress压缩，生成一个umcompress压缩过的包，命名为jpg.tar.Z
rar a jpg.rar *.jpg //rar格式的压缩，需要先下载rar for [Linux](http://lib.csdn.net/base/linux)
zip jpg.zip *.jpg //zip格式的压缩，需要先下载zip for [linux](http://lib.csdn.net/base/linux)

**解压**
tar –xvf file.tar //解压 tar包
tar -xzvf file.tar.gz //解压tar.gz
tar -xjvf file.tar.bz2  //解压 tar.bz2
tar –xZvf file.tar.Z  //解压tar.Z
unrar e file.rar //解压rar
unzip file.zip //解压zip

我们执行 tar -xvf node-v14.19.1-linux-x64.tar.xz

4. 配置环境变量

```
pwd // 命令用于显示工作目录
```

![img](https://img-blog.csdnimg.cn/8ac221b9da7a4849ae5c6ab9a9516376.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP5ruhenM=,size_13,color_FFFFFF,t_70,g_se,x_16)

使用`pwd`命令获取node bin 的位置 

前往跟目录下面的etc/profile 此文件涉及系统的环境，即环境变量相关。这里修改会对所有用户起作用

使用`vi`或`vim`命令编辑该文件

```
vi profile 
vim profile
```

然后按一下`I`键 进入编辑模式

在最后一行添加

```
export PATH=$PATH:你的目录/node-v14.19.1-linux-x64/bin
```

然后执行`source profile`或者重启linux

```
npm config set registry https://registry.npm.taobao.org //配置淘宝代理
```





# 基本命令

## 一、目录结构

> Linux 文件系统的结构层次鲜明，就像一棵倒立的树，最顶层是其根目录 /root：

- /bin：存放二进制可执行文件（ls、cat、mkdir 等），常用命令一般都在这里；
- /etc：存放系统管理和配置文件；
- /home：存放所有用户文件的根目录，是用户主目录的基点，比如用户 user 的主目录就是 /home/user，可以用 ~user 表示；
- /usr：用于存放系统应用程序；
- /opt：额外安装的可选应用程序包所放置的位置。一般情况下，我们可以把 tomcat 等都安装到这里；
- /proc：虚拟文件系统目录，是系统内存的映射。可直接访问这个目录来获取系统信息；
- /root：超级用户（系统管理员）的主目录（特权阶级^o^）；
- /sbin：存放二进制可执行文件，只有 root 才能访问。通常存放系统管理员使用的系统级别的管理命令和程序。如 ifconfig 等；
- /dev：用于存放设备文件；
- /mnt：系统管理员安装临时文件系统的安装点，系统提供这个目录是让用户临时挂载其他的文件系统；
- /boot：存放用于系统引导时使用的各种文件；
- /lib：存放着和系统运行相关的库文件 ；
- /tmp：用于存放各种临时文件，是公用的临时文件存储点；
- /var：用于存放运行时需要改变数据的文件，也是某些大文件的溢出区，比方说各种服务的日志文件（系统启动日志等）等；
- /lost+found：这个目录平时是空的，系统非正常关机而留下“无家可归”的文件（windows 下叫什么 .chk）就在这里。

## 二、处理目录的常用命令

```bash
ls     # 列出目录
cd     # 切换目录
pwd    # 显示目前的目录
mkdir  # 创建一个新的目录
rmdir  # 删除一个空的目录
cp     # 复制文件或目录
rm     # 移除文件或目录
mv     # 移动文件与目录，或修改文件与目录的名称
```

## 三、防火墙常用命令

```bash
systemctl status firewalld                                    # 查看 firewall 服务状态
service firewalld start                                       # 开启防火墙
service firewalld restart                                     # 重启防火墙
service firewalld stop                                        # 关闭防火墙
firewall-cmd --list-all                                       # 查看全部信息
firewall-cmd --list-ports                                     # 只看已开放端口信息
firewall-cmd --zone=public --add-port=8080/tcp --permanent    # 永久开放 8080 端口
firewall-cmd --permanent --zone=public --remove-port=8080/tcp # 移除 8080 端口
```

## 四、启动、终止 jar 包

```bash
nohup java -jar xxx.jar &        # 后台运行 jar 包

netstat -nlp | grep :8080        # 根据端口号找到对应的 pid
kill -9 pid                      # 终止该进程
```

## 五、软件安装类常用命令

### rpm

```bash
rpm -ivh xxx.rpm # rpm安装
                 # -i(install) 安装
                 # -v(verbose) 显示详细安装信息
                 # -h(hash)    显示进度
                 # --nodeps    不检测依赖性

rpm -q xxx.rpm   # 查询是否安装
                 # -q(query) 查询

rpm -qa          # 查询所有已安装的rpm包
                 # -a(all)

-rpm -qi xxx.rpm # 查询软件包的详细信息
                 # -i(information) 查询软件信息

rpm -ql xxx.rpm  # 查询包中软件安装位置
                 # -l(list) 列表

rpm -e xxx.rpm   # rpm卸载
                 # -e(erase) 卸载
                 # --nodeps  不检测依赖性
```

### tar

```bash
tar -acvf test.tar.gz document/ # 将 document 文件夹下面的文件压缩
tar -axvf javaxxx.tar.gz        # 解压
```

Bash

## 六、查看文件内容

```bash
vi file1      # 打开并浏览文件
cat file1     # 从第一个字节开始正向查看文件的内容
tac file1     # cat 的反写，从最后一行开始反向查看一个文件的内容
head -2 file1 # 查看一个文件的前两行
tail -3 file1 # 查看一个文件的最后三行
more file1    # 分页查看长文件，空格下翻，b上翻页，q退出
```

## 七、查找文件

```bash
find / -name file1                               # 从 ‘/’ 开始进入根文件系统查找文件和目录
find / -user user1                               # 查找属于用户 ‘user1’ 的文件和目录
find /home/user1 -name *.bin                     # 在目录 ‘/home/user1’ 中查找以 ‘.bin’ 结尾的文件
find /usr/bin -type f -atime +100                # 查找在过去 100 天内未被使用过的执行文件
find /usr/bin -type f -mtime -10                 # 查找在 10 天内被创建或者修改过的文件
locate *.ps                                      # 寻找以 ‘.ps’ 结尾的文件，先运行 ‘updatedb’ 命令
find -name ‘*.[ch]’ | xargs grep -E ‘expr’       # 在当前目录及其子目录所有 ‘.c’ 和 ‘.h’ 文件中查找 ‘expr’
find -type f -print0 | xargs -r0 grep -F ‘expr’  # 在当前目录及其子目录的常规文件中查找 ‘expr’
find -maxdepth 1 -type f | xargs grep -F ‘expr’  # 在当前目录中查找 ‘expr’
```
