因为`linux`和`shell`的学习对于生物信息来说很重要，而且平常使用机会较少
所以还是决定重读一遍《linux shell脚本攻略》并写下笔记
笔记真实代码输入输出自行理解

> 无特殊说明均在linux centos环境下运行得出
> 这是这本书的第一章，共40页
> 书是从交大图书馆借的，第二版，感觉不适合新手看

[TOC]
# 如何使用linux
> 这一章是我自己加的，供毫无linux经验的人阅读
## 介绍
我所熟悉使用的linux都是纯命令行版，一台电脑只有命令行可以使用，因此所有的操作都是基于命令行的
为什么要使用linux？
linux更稳定，兼容性更强，有很多专业的软件只有linux平台上才能使用，而且作为服务器需要7 X 24开机运行，linux系统适合作为服务器使用
> ps:用Linux还能装B，因为可以不用鼠标

我所购买的是腾讯云服务器，选的centos系统，centos是linux的一个发行版
在购买之后，会给你服务器的ip和root密码，root是系统管理员的意思
> 如果你没有钱买，但是也想学一下linux，可以用我的服务器，账号是temp，密码是temp，ip地址是118.126.109.155，相应域名是simonyang.club


## 连接服务器
打开cmd（window用户 win+r 输入cmd）
`C:\Users\Si tu m'aimes>ssh <username>@<server ip>`
server ip也可以写成域名
对于我来说
```
C:\Users\Si tu m'aimes>ssh root@118.126.109.155
C:\Users\Si tu m'aimes>ssh root@simonyang.club
```
下面用temp做演示
```
C:\Users\Si tu m'aimes>ssh temp@simonyang.club
The authenticity of host 'simonyang.club (118.126.109.155)' can't be established.
ECDSA key fingerprint is SHA256:qvZ8etqh8N24Iikc40VxhCj8IqD1jP4S8jz+ACK+5XM.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'simonyang.club' (ECDSA) to the list of known hosts.
temp@simonyang.club's password:
Last login: Sat Aug  7 12:09:54 2010 from 124.161.8.194
[temp@VM_0_7_centos ~]$
```
第一次登陆会有yes和no的问题，yes
注意，使用linux的第一个坑出现了，linux上输入任何密码都是不会回显的，意味着你输入的任何东西都不会在终端上体现，也不会出现****
但是你的键盘输入还是依旧在输入，所以直接输入密码，按enter即可。
退出用exit命令
```
[temp@VM_0_7_centos ~]$ exit
logout
Connection to simonyang.club closed.

C:\Users\Si tu m'aimes>
```
每次登陆都要ssh还挺麻烦的，可以使用[xshell](https://www.netsarang.com/download/down_form.html?code=622)

## pwd
登入之后，你的位置在`~`，`~`是用户主目录，linux是支持多用户的，每个用户都有一个主目录，linux也支持多终端登陆，所以temp账号你们可以随便用
对于root用户，用户主目录的位置是`/root`,对于其他用户，用户主目录的位置是`/home/<your username>`
linux没有盘符的概念，路径均以`/`开头，`/`是根目录
pwd命令，输出当前目录
```
C:\Users\Si tu m'aimes>ssh temp@simonyang.club
temp@simonyang.club's password:
Last login: Sat Aug  7 12:16:03 2010 from 124.161.8.194
[temp@VM_0_7_centos ~]$ pwd
/home/temp
```
## cd
cd命令，跳转目录，change directory，`..`的意思是上一级目录，`.`的意思是当前目录，这一点和windows相通，`-`上一次位置
```
[temp@VM_0_7_centos ~]$ cd ..
[temp@VM_0_7_centos home]$ pwd
/home
[temp@VM_0_7_centos home]$ cd .
[temp@VM_0_7_centos home]$ pwd
/home
[temp@VM_0_7_centos home]$ cd ~
[temp@VM_0_7_centos ~]$ pwd
/home/temp
[temp@VM_0_7_centos ~]$ cd /
[temp@VM_0_7_centos /]$ pwd
/
[temp@VM_0_7_centos /]$ cd /home/temp
[temp@VM_0_7_centos ~]$ pwd
/home/temp
[temp@VM_0_7_centos ~]$ cd -
/
```

## ls
列出当前目录文件
```
[temp@VM_0_7_centos /]$ ls
bin   data  etc   lib    lost+found  mnt  proc  run   srv  tmp  var
boot  dev   home  lib64  media       opt  root  sbin  sys  usr
[temp@VM_0_7_centos /]$ cd home
[temp@VM_0_7_centos home]$ pwd
/home
```

## mkdir rmdir
创建目录，删除目录
```
[temp@VM_0_7_centos ~]$ mkdir temp
[temp@VM_0_7_centos ~]$ ls
temp
[temp@VM_0_7_centos ~]$ rmdir temp
[temp@VM_0_7_centos ~]$ ls
[temp@VM_0_7_centos ~]$ mkdir temp/temp/temp
mkdir: cannot create directory ‘temp/temp/temp’: No such file or directory
[temp@VM_0_7_centos ~]$ mkdir -p temp/temp/temp #-p层级创建空目录
[temp@VM_0_7_centos ~]$ ls
temp
[temp@VM_0_7_centos ~]$ cd temp
[temp@VM_0_7_centos temp]$ ls
temp
```

## vim
创建编辑文件，vim是个非常强大的命令，但是我只讲怎么进入编辑退出
> linux第二大坑，进了vim出不来
```
[temp@VM_0_7_centos temp]$ cd ~
[temp@VM_0_7_centos ~]$ vim a.txt
```
vim 后面跟文件名，文件没有的时候新建，有的时候编辑
> linux系统中的文件后缀名无特殊含义
enter之后会进入一个新界面，左下角写着"a.txt" [New File]
这时候处于vim的命令模式，按下的字符当作命令而不是输入
按`i`
这时候切换到编辑模式，左下角写着-- INSERT --，这时候输入的字符才会显现
编辑完毕之后，按`Esc`，切换回命令模式，左下角insert消失
按`:` 
注意：linux下命令中的符号均是英文符号
这时候切换到底线命令模式，光标移至左下角，输入在左下角显示
这时的命令有
q 不保存，直接退出
q! 不保存，并强制退出
e! 放弃所有修改，从上次保存文件开始再编辑
w 保存文件，但不退出
w! 强制保存，不退出
wq或x 保存，并退出
wq! 强制保存，并退出
一般常用的就是wq，如果是readonly只读文件，需要q!才能顺利退出。
按`Esc`可以退出底线命令模式
> 小建议：当你不知道你处在什么模式的时候，不停的按`Esc`直到没有反应，然后按`:` `wq`退出

## rm
删除文件
```
[temp@VM_0_7_centos ~]$ rm a.txt #删除a.txt
[temp@VM_0_7_centos ~]$ rm temp #删除目录
rm: cannot remove ‘temp’: Is a directory
[temp@VM_0_7_centos ~]$ rm -r temp #层级删除
```
> `rm -rf /*`是个危险的命令，`rm -rf`在删除时无需确认，`/*`匹配所有根目录下的文件夹
linux信任操作者，如果你有权限，你可以删除所有系统文件，所以在`rm -rf`时一定要再三确认输对了目录名

> temp用户不是系统管理员，没那么大的权限，所以执行不会彻底成功，不过还是不试最好

## *
刚才讲到`/*`会通配所有根目录下的文件
类似的有`som*`可以匹配所有som开头的文件，`*om*`匹配所有中间含有om的文件，*的意思是0-n个字符
```
[temp@VM_0_7_centos ~]$ touch a.txt b.txt somea someb #touch创建空文件
[temp@VM_0_7_centos ~]$ ls
a.txt  b.txt  somea  someb
[temp@VM_0_7_centos ~]$ rm *.txt
[temp@VM_0_7_centos ~]$ ls
somea  someb
```

## ll
和ls -l效果相同，列出更详细的文件参数
```
[temp@VM_0_7_centos ~]$ mkdir temp
[temp@VM_0_7_centos ~]$ ls
somea  someb  temp
[temp@VM_0_7_centos ~]$ ll
total 4
-rw-rw-r-- 1 temp temp    0 Aug  7 12:50 somea
-rw-rw-r-- 1 temp temp    0 Aug  7 12:50 someb
drwxrwxr-x 2 temp temp 4096 Aug  7 12:53 temp
```
temp文件前的d表示这是一个文件夹，linux系统下的所有都被视为文件

## mv
移动，move
```
[temp@VM_0_7_centos ~]$ mv somea temp/  #前为待移动文件，后为目录
[temp@VM_0_7_centos ~]$ ls
someb  temp
[temp@VM_0_7_centos ~]$ cd temp
[temp@VM_0_7_centos temp]$ ls
somea
```

## cp
复制，copy
```
[temp@VM_0_7_centos temp]$ cp somea ../
[temp@VM_0_7_centos temp]$ ls
somea
[temp@VM_0_7_centos temp]$ cd ..
[temp@VM_0_7_centos ~]$ ls
somea  someb  temp
```

## cat head tail more less 
这些全部都是查看文件的命令
下面是一个文件，我已经编辑好了
```
[temp@VM_0_7_centos ~]$ cat a.txt #查看全部
a
b
c
d
e
f
g
h
i
[temp@VM_0_7_centos ~]$ head -n 3 a.txt #头部三行
a
b
c
[temp@VM_0_7_centos ~]$ tail -n 3 a.txt #尾部三行
g
h
i
```
`more` 类似 `cat`，`more`会一页一页的显示，方便使用者逐页阅读
`less` ，`more` 没有办法向前翻，只能往后看，`less`时可以使用 [pageup] [pagedown] 等按键的功能来前后翻看文件

## which
查看命令的位置
我们输入的`cd`命令是存储在一个目录下的，当输入`cd`时，系统会搜寻环境变量中的目录，看是否存在名叫`cd`的文件，如果有就执行，没有就报错
```
[temp@VM_0_7_centos ~]$ echo $PATH #打印出环境变量
/usr/local/bin:/usr/bin:/usr/local/sbin:/usr/sbin:/home/temp/.local/bin:/home/temp/binlocal/bin:/home/temp/bin #:分隔
[temp@VM_0_7_centos ~]$ which cd
/usr/bin/cd  #目录/usr/bin在环境目录中
```

## wget
下载文件，直接接对应的网址就好
[temp@VM_0_7_centos ~]$ wget http://www.baidu.com
--2010-08-17 15:06:09--  http://www.baidu.com/
Resolving www.baidu.com (www.baidu.com)... 180.97.33.108, 180.97.33.107
Connecting to www.baidu.com (www.baidu.com)|180.97.33.108|:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 2381 (2.3K) [text/html]
Saving to: ‘index.html’

100%[==============================================================================>] 2,381       --.-K/s   in 0s

2010-08-17 15:06:09 (165 MB/s) - ‘index.html’ saved [2381/2381]

[temp@VM_0_7_centos ~]$ ls
a.txt  index.html  somea  someb  temp
[temp@VM_0_7_centos ~]$ wget ftp://ftp.ncbi.nlm.nih.gov/geo/samples/GSM498nnn/GSM498363/suppl/GSM498363_S140.txt.gz
--2010-08-17 15:19:40--  ftp://ftp.ncbi.nlm.nih.gov/geo/samples/GSM498nnn/GSM498363/suppl/GSM498363_S140.txt.gz
           => ‘GSM498363_S140.txt.gz’
Resolving ftp.ncbi.nlm.nih.gov (ftp.ncbi.nlm.nih.gov)... 130.14.250.11, 2607:f220:41e:250::11
Connecting to ftp.ncbi.nlm.nih.gov (ftp.ncbi.nlm.nih.gov)|130.14.250.11|:21... connected.
Logging in as anonymous ... Logged in!
==> SYST ... done.    ==> PWD ... done.
==> TYPE I ... done.  ==> CWD (1) /geo/samples/GSM498nnn/GSM498363/suppl ... done.
==> SIZE GSM498363_S140.txt.gz ... 885554
==> PASV ... done.    ==> RETR GSM498363_S140.txt.gz ... done.
Length: 885554 (865K) (unauthoritative)

100%[==================================================================================================================================================>] 885,554      502KB/s   in 1.7s   

2010-08-17 15:19:46 (502 KB/s) - ‘GSM498363_S140.txt.gz’ saved [885554]

[temp@VM_0_7_centos ~]$ 
[temp@VM_0_7_centos ~]$ ls
a.txt  GSM498363_S140.txt.gz  index.html  somea  someb  temp


## man
man是用来查看帮助文档的，文档都是英文，但的确很详细
```
[temp@VM_0_7_centos ~]$ man cd
```
> 命令格式
命令名称 [命令参数] [命令对象]
命令参数长格式 --help 短格式 -h 这些都是约定俗成的

## who whoami id
查看用户相关信息
```
[temp@VM_0_7_centos ~]$ whoami #我是谁
temp
[temp@VM_0_7_centos ~]$ who #我的登陆信息
temp     pts/0        2010-08-17 14:38 (124.161.8.25)
temp     pts/1        2010-08-15 00:40 (204.16.247.203)
[temp@VM_0_7_centos ~]$ id #我的用户 用户组
uid=1001(temp) gid=1001(temp) groups=1001(temp)
```
### uname hostname uptime
查看计算机相关信息
```
[temp@VM_0_7_centos ~]$ uname #系统名
Linux
[temp@VM_0_7_centos ~]$ hostname #计算机名
VM_0_7_centos
[temp@VM_0_7_centos ~]$ uptime #运行时间
 15:16:07 up 67 days, 19:06,  2 users,  load average: 23.20, 21.05, 20.84
[temp@VM_0_7_centos ~]$ uname -a #所有计算机相关信息
Linux VM_0_7_centos 3.10.0-862.el7.x86_64 #1 SMP Fri Apr 20 16:44:24 UTC 2018 x86_64 x86_64 x86_64 GNU/Linux
```

# 小试牛刀
## 简介
> shell中的注释开头为`#`
> `~`表示主目录
> 每当一个新shell生成时，都会执行`~/.bashrc`  
```
[root@VM_0_14_centos ~]# pwd #linux centos root下
/root
vip7@VM-0-15-ubuntu:~$ pwd #linux ubuntu 非root下
/home/vip7
```
> root开头为`#` 非root开头为`$` `@`前为用户名后为主机名
非root用户使用`sudo <command> <arguments>`执行root命令
> shell脚本通常起始为 `#!/bin/bash`
```shell
> echo echo hello > hello.sh
> cat hello.sh
echo hello
> bash hello.sh
hello
> ./hello.sh
-bash: ./hello.sh: Permission denied
> ls -l
total 12
-rw-r--r--  1 root root   11 Jul 24 09:36 hello.sh
> chmod a+x hello.sh
> ./hello.sh
hello
> /root/hello.sh
hello
> ls -l
total 12
-rwxr-xr-x  1 root root   11 Jul 24 09:36 hello.sh
```
## 打印
```shell
> echo hello
hello
> echo hello world
hello world
> echo 'hello world'
hello world
> echo "hello world"
hello world
> echo "hello world !"
-bash: !": event not found
> echo 'hello world !'
hello world !
> echo "hello world \!"
hello world \!
> echo "hello world\$"
hello world$
```
> ubuntu稍有区别
```
vip7@VM-0-15-ubuntu:~$ echo "hello world !"
hello world !
vip7@VM-0-15-ubuntu:~$ echo 'hello world !'
hello world !
vip7@VM-0-15-ubuntu:~$ echo "hello world\$"
hello world$
```
```shell
> printf "%-5s %-10s %-4.2f\n" 1 simon 40.3524 
1     simon      40.35
> echo -n hello;echo hello 
hellohello
> echo -e "1\t2\t3" 
1	2	3
```
## 环境变量和变量
```shell
> pgrep ls #获取程序的PID
464
> cat /proc/464/environ
LANG=en_USUTF-8PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/binHOME=/rootLOGNAME=rootUSER=rootSHELL=/bin/sh   #中间用
> cat /proc/464/environ | tr '\0' '\n'
LANG=en_US.UTF-8
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin
HOME=/root
LOGNAME=root
USER=root
SHELL=/bin/sh
```
> 书中还讲到了环境变量LD_LIBRARY_PATH，该环境变量主要用于指定查找共享库（动态链接库）时除了默认路径之外的其他路径。但是两个服务器这个值都还不存在。
```shell
> var=value
> var = value
-bash: var: command not found
> var="hello world"
> echo var
var
> echo $var
hello world
> echo ${var}
hello world
> echo $vars

> echo ${var}s
hello worlds
> echo '$var'
$var
> echo "$var"
hello world
```
```shell
> var=value
> length=${#var}
> echo $length
5
```
> 一些内置值
```shell
> echo $SHELL
/bin/bash
> echo $0
-bash
> echo $PATH
/root/anaconda3/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/root/bin
> echo $HOME
/root
> echo $PWD
/root
> echo $USER
root
> echo $UID
0
```
```
vip7@VM-0-15-ubuntu:~$ echo $USER
vip7
vip7@VM-0-15-ubuntu:~$ echo $UID
1011
```
```shell
export PATH="/root/anaconda3/bin:$PATH" #只在本次shell对话中有效
> echo $PATH
/root/anaconda3/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/root/bin
``` 
## 数学运算
```shell
> a=1
> b=2
> let c=a+b
> echo $c
3
> let a++
> echo $a
2
> c=$[a+b]
> echo $c
4
> let b+=4
> echo $b
6
> c=$((a+b))
> echo $c
8
> d=`expr a+b`
> echo $d
a+b
> d=`expr $a+$b`
> echo $d
2+6
> d=`expr $a + $b`
> echo $d
8
> d=`expr $a \* $b` #只有这里的*需要转义
> echo $d
12
> echo "4*0.35" | bc
1.40
> result=`echo "$a*1.24" | bc`
> echo $result
2.48
> number=100
> echo "obase=2;$number" | bc
1100100
> number=1100100
> echo "obase=10;ibase=2;$number" | bc
100
> echo "sqrt(100);10^10" | bc
10
10000000000
```
## 文件描述符

```
重定向
0 --- stdin
1 --- stdout
2 --- stderr
```
```shell
> echo "some" > some.txt
> cat some.txt
some
> echo "word" >> some.txt
> cat some.txt
some
word
> ls +
ls: cannot access +: No such file or directory
> echo $? #命令执行状态
2  #非0代表失败
> ls
anaconda3  hello.sh  some.txt
> echo $?
0  #代表成功
> ls + 2> err.txt  #错误重定向
> cat err.txt
ls: cannot access +: No such file or directory 
> cmd > some.txt 2>&1 #标准错误重定向到标准输出
> cat some.txt
-bash: cmd: command not found
> ls + &> out.txt #标准错误和标准输出均重定向
> ls + 2> /dev/null #错误重定向到黑洞
```
```shell
> ls | tee out.txt | cat -n # tee将stdin写入文件的同时提供一份副本到管道
     1	anaconda3
     2	err.txt
     3	hello.sh
     4	out.txt
     5	some.txt
> cat out.txt
anaconda3
err.txt
hello.sh
out.txt
some.txt
> echo hello | tee -a out.txt
hello
> cat out.txt
anaconda3
err.txt
hello.sh
out.txt
some.txt
hello
> echo who | tee - # -代表stdin 提供一份副本到标准输出
who
who
```
```shell
> echo some > out.txt 
> cat < out.txt #文件内容重定向到命令
some

> cat<<EOF>out.txt #EOF之间的内容重定向cat 然后重定向到out.txt EOF是一个tag可以替换成其他词
> hello
> boy
> EOF
> cat out.txt
hello
boy
```
```
< --- 从文件读取到stdin
> --- 截断模式写入文件
>>--- 追加模式写入文件
```
```shell
> echo this is a line > input.txt
> exec 3< input.txt #不能使用0,1,2 创建自己的文件描述符
> cat <&3 
this is a line #再次读取需要重新分配文件描述符
```
## 数组

```shell
> array=(1 2 3 4 5) #普通数组
> array[5]=6
> echo ${array[5]}
6
> index=2
> echo ${array[$index]}
3
> echo ${array[*]}
1 2 3 4 5 6
> array[10]=1
> echo ${#array[*]}
7
> echo ${array[*]}
1 2 3 4 5 6 1
> echo ${array[10]}
1
> echo ${array[9]}

```
```shell
> declare -A ass_array #定义关联数组
> ass_array=([index1]=val1 [index2]=val2)
> ass_array[index3]=val3
> echo ${ass_array[index3]}
val3
> echo ${ass_array[*]}
val1 val2 val3
> echo ${!ass_array[*]}
index1 index2 index3
```
## 别名
```shell
> alias ls='ls -ll'
> ls
total 4
drwxr-xr-x 22 root root 4096 Oct  5  2018 anaconda3
> \ls
anaconda3
```
## 获取终端信息
```
stty -echo #禁止将输出发送到终端
stty echo #允许将输出发送到终端
tput sc #存储光标位置
tput rc #恢复光标位置
tput ed #清除从当前光标到行末尾的所有内容
```
## 时间和日期
```shell
> date
Sun Nov 18 18:22:25 CST 2018
> date +%s
1542536551
> date --date "Nov 18 2018" +%A
Sunday
> date "+%d %B %Y"
18 November 2018
> date -s "2018 11 18 18:23:22"
date: invalid date ‘2018 11 18 18:23:22’
> date -s "2018 Nov 18 18:23:22"
date: invalid date ‘2018 Nov 18 18:23:22’
> date -s "18 Nov 2018 18:23:22"
Sun Nov 18 18:23:22 CST 2018
> sleep 10

```
```
星期          %a %A
月            %b %B
日            %d
固定格式日期   %D
年            %y %Y
小时          %I %H
分钟          %M
秒            %S
纳秒          %N
Unix纪元时    %s
```
## 调试脚本
```shell
> echo echo some > out.sh
> bash -x out.sh
+ echo some
some
> for i in {1..3}; do set -x; echo $i; set +x; done
+ echo 1
1
+ set +x
+ echo 2
2
+ set +x
+ echo 3
3
+ set +x
```
```
set -x 在执行时显示参数和命令
set +x 禁止调试
set -v 当命令进行读取时显示输入
set +v 禁止打印输入
```
调试便捷方法
将`#!/bin/bash`改为`#!/bin/bash -xv`

## 函数
```shell
function fname()
{
    statements;
}
fname()
{
    statements;
}
```
```shell
> fname()
> {
> echo $1,$2;
> echo "$@"; 
> echo "$*"; 
> return 0;
> }
> fname my world
my,world
my world
my world
```
`$*` 和 `$@` 都表示传递给函数或脚本的所有参数，不被双引号`" "`包含时，都以`"$1" "$2" … "$n"` 的形式输出所有参数。
但是当它们被双引号`" "`包含时，`"$*"` 会将所有的参数作为一个整体，以`"$1 $2 … $n"`的形式输出所有参数；`"$@"` 会将各个参数分开，以`"$1" "$2" … "$n"` 的形式输出所有参数。
```
$0		当前脚本的文件名
$n		传递给脚本或函数的参数。n 是一个数字，表示第几个参数。例如，第一个参数是$1，第二个参数是$2。
$#		传递给脚本或函数的参数个数。
$*		传递给脚本或函数的所有参数。
$@		传递给脚本或函数的所有参数。
$?		上个命令的退出状态，或函数的返回值。一般情况下，大部分命令执行成功会返回 0，失败返回 非0。
$$		当前Shell进程ID。对于 Shell 脚本，就是这些脚本所在的进程ID。
```
```shell
> cat parent.sh
#!/bin/bash
plus1(){ echo $(($1+1));}
echo $(plus1 4)
export -f plus1  #使函数子进程可用
./child.sh 3 4
> vim child.sh
> cat child.sh
#!/bin/bash
echo $(plus1 $(($1 * $2)))
> chmod a+x *.sh
> ./parent.sh
5
13
> cmd="echo some"
> $cmd #好神奇啊我也不知道为啥
some
```
## 将输出写入变量
> 管道操作符 `cmd1 | cmd2 | cmd3`
```shell
> ls | cat -n >out.txt
> cat out.txt
     1	anaconda3
     2	out.txt
> output=$(ls | cat -n)
> echo $output
1 anaconda3 2 out.txt
> output=`ls | cat -n`
> echo $output
1 anaconda3 2 out.txt
```
> 子shell
```shell
> pwd
/root
> (cd /data;ls)
Anaconda3-5.1.0-Linux-x86_64.sh  index.txt
> pwd
/root
> cat text.txt
1
2
3
> out=$(cat text.txt)
> echo $out
1 2 3
> out="$(cat text.txt)"
> echo $out
1 2 3
```
## read
```shell
> read -n 2 var
12> echo $var
12
> read -s password #输入时无回显
> echo $password
simonyang
> read -p "Please enter your Name:" var
Please enter your Name:simonyang
> echo $var
simonyang
> read -d . var
simon.> echo $var
simon
```
> `read -t 2 var`要求两秒内输入
## 运行命令直至成功
> `true`作为`/bin`中的一个二进制文件实现
shell内建的`:`总是返回0的退出码
```shell
> repeat() {while :; do $@ && return ; done}
```
## 字段分隔符 IFS
> IFS的默认值为空白字符（换行符、制表符或者空格）
```shell
> for item in "1 2 3";do echo $item;done
1 2 3
> oldifs=$IFS
> IFS=,
> for item in "1,2,3";do echo $item;done
1 2 3
> IFS=$oldifs
> for item in "1,2,3";do echo $item;done
1,2,3
```
```shell
for var in list;
do 
    commands;
done
for((i=0;i<10;i++))
{
    commands;
}
while condition;
do 
    commands;
done
until condition;  #直到条件为真
do
    commands;
done
```
```shell
> for i in {a..g};do echo $i;done
a
b
c
d
e
f
g
```
## 比较
```shell
if condition;
then
    commands;
fi
if condition;
then
    commands;
else if condition;
then
    commands;
else
    commands;
fi
```
> `[ condition ] && action` #如果真，则action运行
> `[ condition ] || action` #如果假，则action运行

```
算术比较
-eq   equal返回真     
-ne   not equal返回真
-gt   greater than返回真
-lt   less than返回真
-ge   greater or equal返回真
-le   less or equal返回真
```
```shell
> if [ 5 -eq 5 -a 4 -gt 3]; then echo true;fi
-bash: [: missing `]' #3与]应有空格，5与[也是
> if [ 5 -eq 5 -a 4 -gt 3 ]; then echo true;fi
true
```
```
文件系统测试，是为真
[ -f $var ] 是否包含文件路径和文件名 
[ -x $var ] 是否可执行
[ -d $var ] 是否是目录
[ -e $var ] 是否存在
[ -c $var ] 是否是字符设备
[ -b $var ] 是否是块设备
[ -w $var ] 是否可写
[ -r $var ] 是否可读
[ -L $var ] 是否是符号链接
```
```shell
> if [ -f out.txt ]; then echo true;fi
true
> ls
anaconda3  out.txt  text.txt
> if [ ! -L out.txt ]; then echo true;fi # !取反
true
```
```
字符串比较，是为真
最好使用双中括号，str1和str2前后都有空格
[[ $str1 = $str2 ]] 是否相等
[[ $str1 == $str2 ]] 是否相等
[[ $str1 != $str2 ]] 是否不相等
[[ $str1 > $str2 ]] 按字母序，str1是否大于str2
[[ $str1 > $str2 ]] 按字母序，str1是否小于str1
[[ -z $str2 ]] 是否是空字符串
[[ -n $str2 ]] 是否是非空字符串
```
```shell 
> str1="hello"
> str2=''
> if [[ -n $str1 ]] && [[ -z $str2 ]];then echo true;fi
true
```
> 使用test
```shell
> if test $var -eq 1;then echo true;fi
true
> if ! test $var -eq 0;then echo true;fi
true
```


# 命令之乐
## cat
```shell
> echo this is a line > out.txt 
> cat out.txt
this is a line
> echo this is a new line >out2.txt
> cat out.txt out2.txt
this is a line
this is a new line
> echo some new | cat - out.txt
some new
this is a line
```
---------------
```shell
> cat out.txt
1
2




3
> cat -s out.txt
1
2

3
> cat some.py
def hello():
	print("hello world")
> cat -T some.py
def hello():
^Iprint("hello world")
> cat -n some.py
     1	def hello():
     2		print("hello world")
> cat -n out.txt
     1	1
     2	2
     3	
     4	
     5	
     6	
     7	3
> cat -nb out.txt
     1	1
     2	2




     3	3
```
## script
```shell
> script -t 2> timelog.log -a output.session
Script started, file is output.session
> echo hello > new.txt
> cat new.txt
hello
> exit
exit
Script done, file is output.session
> cat output.session
Script started on Sat 24 Nov 2018 04:24:45 PM CST
> echo hello > new.txt
> cat new.txt
hello
> exit
exit

Script done on Sat 24 Nov 2018 04:25:07 PM CST

> scriptreplay timelog.log output.session #重放
```
## find
```shell
> cd /home
> touch some.py out1.txt out2.txt
> ls
out1.txt  out2.txt  some.py
> find . 
.
./out2.txt
./out1.txt
./some.py
> find . -print0
../out2.txt./out1.txt./some.py> find . -name "*.txt"
./out2.txt
./out1.txt
> find . -name *.txt
find: paths must precede expression: out2.txt
Usage: find [-H] [-L] [-P] [-Olevel] [-D help|tree|search|stat|rates|opt|exec] [path...] [expression]
> touch OUT.txt
> find . -iname "out*"
./out2.txt
./OUT.txt
./out1.txt
> touch some.log
> ls
out1.txt  out2.txt  OUT.txt  some.log  some.py
> find . \( -name "*.txt" -o -name "*.log" \) 
./out2.txt
./OUT.txt
./out1.txt
./some.log
> find /root/ -path "*/.ssh/*"
/root/.ssh/authorized_keys
> find . -regex  ".*\(\.py\|\.txt\)$"
./out2.txt
./OUT.txt
./out1.txt
./some.py
> touch some.PY
> find . -iregex  ".*\.py$"
./some.PY
./some.py
> find . ! -iregex  ".*\.py$"
.
./out2.txt
./OUT.txt
./out1.txt
./some.log
> mkdir some
> mv out* some/
> find . -maxdepth 1 
.
./some.PY
./OUT.txt
./some
./some.py
./some.log
> find .
.
./some.PY
./OUT.txt
./some
./some/out2.txt
./some/out1.txt
./some.py
./some.log
> find . -mindepth 2
./some/out2.txt
./some/out1.txt
> find . -type d  # d指目录，同样的还有f l 
.
./some
```
> 访问时间 -atime 用户最近一次访问文件的时间 access
修改时间 -mtime 文件内容最后一次被修改的时间 modify
变化时间 -ctime 文件元数据最后一次改变的时间 change
作为时间选项 单位是天 -表示小于 +表示大于
以分钟为计量单位 -amin -mmin -cmin
```shell
> vim OUT.txt
> find . -cmin -1
.
./OUT.txt
> find . -newer some.PY
.
./OUT.txt
./some
> find . -size -1  #
./some.PY
./some/out2.txt
./some/out1.txt
./some.py
./some.log
> find . -size +1c #大于1字节 
.
./OUT.txt
./some
```
> b  块  512字节
  c    字节
  w    字  2字节
  k    1024字节
  M    1024K字节
  G    1024M字节
```shell
> ls
OUT.txt  some  some.log  some.py  some.PY
> find . -size +1c -delete
find: cannot delete ‘./some’: Directory not empty
> ls -l
total 4
drwxr-xr-x 2 root root 4096 Nov 24 16:52 some
-rw-r--r-- 1 root root    0 Nov 24 16:41 some.log
-rw-r--r-- 1 root root    0 Nov 24 16:37 some.py
-rw-r--r-- 1 root root    0 Nov 24 16:48 some.PY
> find . -maxdepth 1 -perm 755 
.
./some
> find . -maxdepth 1 -user root
.
./some.PY
./some
./some.py
./some.log
> find . -maxdepth 1 -name "some.*" -exec chmod 755 {} \;  # {} 每次代入文件名然后每次运行
> ls -l
total 4
drwxr-xr-x 2 755 root 4096 Nov 24 16:52 some
-rwxr-xr-x 1 755 root    0 Nov 24 16:41 some.log
-rwxr-xr-x 1 755 root    0 Nov 24 16:37 some.py
-rwxr-xr-x 1 755 root    0 Nov 24 16:48 some.PY
> find . -maxdepth 1 -name "some.*" -exec chmod 644 {} +;  # {} 传入所有文件名然后一次运行
> ls -l
total 4
drwxr-xr-x 2 755 root 4096 Nov 24 16:52 some
-rw-r--r-- 1 755 root    0 Nov 24 16:41 some.log
-rw-r--r-- 1 755 root    0 Nov 24 16:37 some.py
-rw-r--r-- 1 755 root    0 Nov 24 16:48 some.PY
> cat some.py
def hi():
	pass
> cat some.PY
def hello():
	pass
> find . -iname "*.py" -exec cat {} \;> final
> cat final
def hello():
	pass
def hi():
	pass
> find ~/ -name ".git" -prune  #跳过.git目录
```
## xargs
> xargs 命令将标准输入数据转换成命令行参数
```shell
> cat out.txt
1 2 3 4 5
6 7 8
9
> cat out.txt | xargs
1 2 3 4 5 6 7 8 9
> cat out.txt | xargs -n 3
1 2 3
4 5 6
7 8 9
> echo "splitXsplitXsplit" | xargs -d X # d delimiter
split split split
```
-------
```shell
> cat demo.sh
echo $* #
> cat args.txt
arg1
arg2
arg3
> cat args.txt | xargs -n 2 ./demo.sh
arg1 arg2
arg3
> cat args.txt | xargs -I {}  ./demo.sh -p {}  # -I指定替换名称 对于每个参数执行一次
-p arg1
-p arg2
-p arg3
> cat args.txt | xargs -I p  ./demo.sh -p -l
-arg1 -l
-arg2 -l
-arg3 -l
```
> 将find与xargs结合使用时定界符应该是`\0`
```shell
> ls
args.txt  demo.sh  out.txt
> find . -name "out.*" -print0 | xargs -0 rm -f  #-print0 将`\0`作为定界符输出文件名 xargs -0 将`\0`作为定界符分开
> ls
demo.sh
> cat files.txt
args1.txt
args2.txt
> cat args1.txt
hello
> cat args2.txt
hi
> cat files.txt | xargs -I {} cat {}
hello
hi
> cat files.txt | (while read arg; do cat $arg; done )  #利用子shell实现相似功能
hello
hi
```
## tr
> tr只能通过stdin接受输入 
```shell
> echo "HELLO" | tr "A-Z" 'a-z' #'z-a'是行不通的
hello
> echo "HELLO" | tr "A-Z" 'b'
bbbbb
> echo "HELLO I AM" | tr -d 'A-H'
LLO I M
> echo "HELLO I AM" | tr -d -c 'A-H\n' #-c选择补集
HEA
> echo hello i am    your    friend  | tr -s ' ' # 压缩
hello i am your friend
> echo hello i am your friend | tr [:lower:] [:upper:] 
HELLO I AM YOUR FRIEND
```
## md5sum sha1sum 
> 计算校验和
md5sum 和 sha1sum 使用非常相似
```shell
> md5sum demo.sh
e88d83ea97b4461177c708b8760045a8  demo.sh
> md5sum demo.sh > demo.md5
> md5sum -c demo.md5
demo.sh: OK
```
## gpg base64 
```shell
> cat demo.sh
echo $* #
> gpg -c demo.sh
> gpg demo.sh.gpg
> base64 demo.sh > out
> cat out
ZWNobyAkKiAjCg==
> base64 -d out | cat -
echo $* #
```
## sort uniq
```shell
> cat files.txt
3
3
9
3
2
8
> sort files.txt
2
3
3
3
8
9
> sort -r files.txt
9
8
3
3
3
2
> sort -r files.txt | uniq
9
8
3
2
```
> uniq的输入要求必须排序过，如果已经排序sort会返回0的退出码，否则返回非0
```shell
> cat data.txt
9 mac 8000
7 dell 7000
10 lenovo 6000
> sort -nk 1 data.txt  #按数字大小
7 dell 7000
9 mac 8000
10 lenovo 6000
> sort -k 1 data.txt #字母序
10 lenovo 6000
7 dell 7000
9 mac 8000
> sort -rk 3 data.txt
9 mac 8000
7 dell 7000
10 lenovo 6000
> sort -k 2.2 data.txt  #按照第二列第二个字符排列，因为默认的分隔是把 "非空白到空白转换" 做为分段的依据，所以按照d,l,m排列
7 dell 7000
10 lenovo 6000
9 mac 8000
> sort -t ' ' -k 2.2 data.txt  #-t 制定空格作为分隔符，第二列第二个字符所以按照a,e,e排列
9 mac 8000
7 dell 7000
10 lenovo 6000
```
> -o 输出到指定文件，可以覆盖原文件
--f 忽略大小写
-c 检查文件是否已排好序，如果乱序，则输出第一个乱序的行的相关信息，最后返回1
-C 检查文件是否已排好序，如果乱序，不输出内容，仅返回1
-M 以月份来排序，比如JAN小于FEB等等
-b 忽略每一行前面的所有空白部分
-z 用`\0`结束行
-m 合并
-u 删除重复行
```shell
> cat files.txt
3
3
9
3
2
8
> sort files.txt -o files.txt
> uniq -u files.txt  #只显示唯一行
2
8
9
> uniq -c files.txt #统计次数
      1 2
      3 3
      1 8
      1 9
> uniq -d files.txt #显示重复行
3
> cat data.txt
9mac8000
7dell7000
1lenovo6000
> cat data.txt | uniq -s 2 -w 1  # 忽略前两个字符比较一个字符
9mac8000
7dell7000
```
## mktemp
> /tmp目录中的内容系统重启后清空
```shell
> filename=`mktemp`  #反引号运行
> echo $filename
/tmp/tmp.iBLhJgtGp3
> filename=`mktemp -d`
> echo $filename
/tmp/tmp.0ojbVHR0ZG
> filename=`mktemp -u` #不创建实际文件
> mktemp test.XXX  #根据模板创建
test.7Ou
```
## split
```shell
> dd if=/dev/urandom of=test.file bs=13MB count=1 #创建13m大小的文件
1+0 records in
1+0 records out
13000000 bytes (13 MB) copied, 0.0903847 s, 144 MB/s
> split -b 1m test.file  #以1M大小划分
> ls
test.file  xaa  xab  xac  xad  xae  xaf  xag  xah  xai  xaj  xak  xal  xam
> rm -f x*    #删除x开头所有文件
> split -b 5m test.file  -d -a 3 split  #数字结尾 结尾三个数字 split开头
> ls
split000  split001  split002  test.file
> split -l 10 test.file  #10行分
> cat server.log
SERVER-1
[connection]
[disconnect]
[connection]
[disconnect]
SERVER-2
[connection]
[disconnect]
SERVER-3
[disconnect]
[connection]
> csplit server.log /SERVER/ -n 1 {*} -f split -b "%d.log"  #/SERVER/正则匹配 -n 1 文件后数字后缀一个 -f 前缀是split -b 后缀格式 {*} 分割执行次数
0
61
35
36
> ls
server.log  split0.log  split1.log  split2.log  split3.log
> cat split0.log   #split0.log是分隔前的，所以没有内容
> cat split1.log
SERVER-1
[connection]
[disconnect]
[connection]
[disconnect]
```
##  { }
```shell
> filename=some.tar.gz  #必须使用变量
> name=${filename%.*} #从尾向前匹配.*短数据删除
> echo $name
new.tar
> name=${filename%%.*} #从尾向前匹配.*长数据删除
> echo $name
some
> name=${filename#*.}  #从前向尾匹配*.短数据删除
> echo $name
tar.gz
> name=${filename##*.} #从前向尾匹配*.长数据删除
> echo $name
gz
> filename=new.new.some
> name=${filename/new/hi} #new替换成hi，值替换一次
> echo $name
hi.new.some
> name=${filename//new/hi} 
> echo $name
hi.hi.some
```
抄一张图，懒得挨个试了
![20181125-20150620201642326](http://cdn.simonyang.club/20181125-20150620201642326.jpg)
## rename mv cp
```shell
#!/bin/bash
count=1;
for img in `find . -maxdepth 1 -iname "*.jpg" -o -iname "*.png" -type f`;
do
    new=image-$count.${img##*.}   #重命名
    mv "$img" "$new"   # 移动
    let count++
done
```
-----
```shell
> rename JPG jpg *.JPG
> ls
new.jpg  old.jpg
```
> 在我的服务器上rename正则使用失败，网上查不到原因
```shell
> mkdir pic
> mv new.jpg ./pic
> ls
old.jpg  pic
> (cd pic;ls)
new.jpg
> cp old.jpg ./pic
> (cd pic;ls)
new.jpg  old.jpg
> ls
old.jpg  pic
```
## look read 
```shell
> cat word.txt
abc
bcd
cde
> look bc word.txt  #寻找bc开头的行
bcd
> grep "^a" word.txt  #寻找a开头的行
abc
```
```shell
#!/bin/bash
read -p "Enter your name:" name
echo "you name is $name"
```
```shell
> echo -e "hello\n" | ./hello.sh
you name is hello
> echo -e "simon\n" > input
> ./hello.sh <input
you name is simon
```

## &后台执行
```shell
#!/bin/bash
pidarray=()
for file in File1.iso File2.iso;
do
    md5sum $file &     #后台运行
    pidarray+=("$!")   #获取进程pid
done
wait ${pidarray[@]}
```

# 以文件之名
## dd
> 创建特定大小的文件

```
vip7@VM-0-15-ubuntu:~/test$ dd if=/dev/zero of=junk.data bs=1M count=1
1+0 records in
1+0 records out
1048576 bytes (1.0 MB, 1.0 MiB) copied, 0.00133993 s, 783 MB/s
vip7@VM-0-15-ubuntu:~/test$ ls
junk.data
```
> `/dev/zero`是一个字符设备，他会不断的返回0值字节

## comm
> 两个文件之间的比较

```
vip7@VM-0-15-ubuntu:~/test$ cat a.txt
apple
organge
pear
vip7@VM-0-15-ubuntu:~/test$ cat b.txt
carrot
apple
banana
vip7@VM-0-15-ubuntu:~/test$ sort a.txt -o a.txt;sort b.txt -o b.txt
vip7@VM-0-15-ubuntu:~/test$ comm a.txt b.txt  #第一列仅a.txt中出现的，第二列仅b.txt中出现的，第三列共同出现的，定界符\t
		apple
	banana
	carrot
organge
pear     
vip7@VM-0-15-ubuntu:~/test$ comm a.txt b.txt -1 -2
apple
vip7@VM-0-15-ubuntu:~/test$ comm a.txt b.txt -3
	banana
	carrot
organge
pear
```
## chmod chown

> 用户权限分为三类 用户 文件所有者 用户组 用户的集合 其他用户 除了用户和用户组之外的其他用户

```
vip7@VM-0-15-ubuntu:~/test$ ls -l
total 1032
-rw-rw-r-- 1 vip7 vip7      19 Nov 30 19:35 a.txt
-rw-rw-r-- 1 vip7 vip7      20 Nov 30 19:35 b.txt
-rw-rw-r-- 1 vip7 vip7 1048576 Nov 30 19:31 junk.data
```
> 第一列 -  普通文件
d    目录
c    字符设备
b    块设备
l    符号链接
s    套接字
p    管道

剩下的部分化为三组，每组三个字符 --- --- --- 第一组 用户权限 第二组 用户组权限 第三组 其他用户权限 无权限- 有权限为字母
文件 r 读权限 w 写权限 x执行权限  
目录 r 读取目录中文件和子目录 w 创建或删除文件和目录 x 访问目录中的文件和子目录

用户还有一个成为setuid(S)的权限，出现在x的位置 该权限允许用户以其拥有者的权限来执行文件
用户组还有一个setgid(S)的权限，出现在x的位置 允许以目录拥有者所在组相同的有效组权限运行文件

目录有一个特殊权限 粘滞位，如果被设置，只有创建该目录的用户才能删除目录中的文件 粘滞位出现在其他用户权限中的x位置
t 无执行权限有粘滞位
T 有执行权限有粘滞位 
左侧vip7代表用户 右侧vip7代表用户组
drwxrwxrwt  11 root root    135168 Nov 30 20:21 tmp
```shell
chmod命令 u 用户权限 g 用户组权限 o 其他用户权限 a 所有
+ 表示增加权限、- 表示取消权限、= 表示唯一设定权限。
vip7@VM-0-15-ubuntu:~/test$ chmod u=x,g=wx,o=rwx a.txt
vip7@VM-0-15-ubuntu:~/test$ ls -l
total 19
---x-wxrwx 1 vip7 vip7      19 Nov 30 19:35 a.txt
vip7@VM-0-15-ubuntu:~/test$ ls -l
total 20
-rwxrwxrwx 1 vip7 vip7      20 Nov 30 19:35 b.txt
```
也可以用8进制 r--=100=4 -w-=010=2 --x=001=1 rwx=4+2+1=111=7 r-x=101=4+1=5
```
vip7@VM-0-15-ubuntu:~/test$ chmod 776 a.txt
vip7@VM-0-15-ubuntu:~/test$ ls -l
total 19
-rwxrwxrw- 1 vip7 vip7      19 Nov 30 19:35 a.txt
```

```
更改所有权
chown <user>.<group> <filename>
设置粘滞位
chmod a+t <directoryname>
递归方式更改权限
chmod 777 <directoryname> -R
递归方式更改所有权
chown <user>.<group> <filename> -R
```
## chattr
```
chattr +i <filename> 将文件设为不可修改
chattr -i <filename> 将文件取消不可修改
```
## touch
> 创建空白文件或者更改已有文件的时间戳
```
vip7@VM-0-15-ubuntu:~/test$ touch a
vip7@VM-0-15-ubuntu:~/test$ ls
a
vip7@VM-0-15-ubuntu:~/test$ ls -l
total 0
-rw-rw-r-- 1 vip7 vip7 0 Nov 30 20:54 a
vip7@VM-0-15-ubuntu:~/test$ touch -d "Fri Nov 30  00:00:00 CST 2018" a  #若没有则创建并改时间，有则改时间
vip7@VM-0-15-ubuntu:~/test$ ls -l
total 0
-rw-rw-r-- 1 vip7 vip7 0 Nov 30 00:00 a
```

## ln
> 符号链接是指向其他文件的指针
```
vip7@VM-0-15-ubuntu:~/test$ ln -s /var/www/ web
vip7@VM-0-15-ubuntu:~/test$ ls -l
total 0
lrwxrwxrwx 1 vip7 vip7 9 Nov 30 20:58 web -> /var/www/
vip7@VM-0-15-ubuntu:~/test$ readlink web
/var/www/
```

## file
打印文件类型基本信息
```
vip7@VM-0-15-ubuntu:~/test$ file web
web: symbolic link to /var/www/
vip7@VM-0-15-ubuntu:~/test$ file -b web
symbolic link to /var/www/
vip7@VM-0-15-ubuntu:~/test$ file -b a
empty
```

## < <与<<<
利用命令的输出
```
while read line;
do something
done < filename
```
如果利用子进程
```
while read line;
do something;
done < <(find $path -type -f -print)
```
<()等同于文件名，用子进程的输出来代替文件名，第一个<用于输入重定向，第二个<用于将子进程的输出转化成文件名，两个<之间有空格。
bash 3.x之后有一个<<<
```
while read line;
do something;
done <<< "`find $path -type -f -print`"
```

## mount umount
环回文件系统是指那些文件中而非物理设备中创建的文件系统
```
[root@VM_0_7_centos test]# dd if=/dev/zero of=lookbackfile bs=1G count=1
1+0 records in
1+0 records out
1073741824 bytes (1.1 GB) copied, 10.923 s, 98.3 MB/s
[root@VM_0_7_centos test]# file lookbackfile
lookbackfile: data
```
文件大小超过1G，因为分配存储空间是按照块的大小的整数倍来进行的。
```
[root@VM_0_7_centos test]# mkfs.ext4 lookbackfile
mke2fs 1.42.9 (28-Dec-2013)
lookbackfile is not a block special device.
Proceed anyway? (y,n) y
Discarding device blocks: done                            
Filesystem label=
OS type: Linux
Block size=4096 (log=2)
Fragment size=4096 (log=2)
Stride=0 blocks, Stripe width=0 blocks
65536 inodes, 262144 blocks
13107 blocks (5.00%) reserved for the super user
First data block=0
Maximum filesystem blocks=268435456
8 block groups
32768 blocks per group, 32768 fragments per group
8192 inodes per group
Superblock backups stored on blocks: 
	32768, 98304, 163840, 229376

Allocating group tables: done                            
Writing inode tables: done                            
Creating journal (8192 blocks): done
Writing superblocks and filesystem accounting information: done

[root@VM_0_7_centos test]# file lookbackfile
lookbackfile: Linux rev 1.0 ext4 filesystem data, UUID=7b221749-5eb5-45ff-922d-7b0bdc47a972 (extents) (64bit) (large files) (huge files)
[root@VM_0_7_centos test]# losetup /dev/loop1 lookbackfile
[root@VM_0_7_centos test]# mount /dev/loop1 /mnt/lookback
[root@VM_0_7_centos test]# umount /mnt/lookback
```
umount mount都是特权命令

## diff 
```
[root@VM_0_7_centos test]# cat version1.txt
this is a line
line2
line3
line4
final
[root@VM_0_7_centos test]# cat version2.txt
this is a line
line2
line4
final
happy
[root@VM_0_7_centos test]# diff version1.txt version2.txt
3d2
< line3
5a5
> happy
[root@VM_0_7_centos test]# diff -u version1.txt version2.txt
--- version1.txt	2010-08-20 07:24:14.316168252 +0800
+++ version2.txt	2010-08-20 07:24:37.103153802 +0800
@@ -1,5 +1,5 @@
 this is a line
 line2
-line3
 line4
 final
+happy
```
以递归方式应用于目录
`diff -Naur directory1 directory2`
-N 所有缺失文件视为空文件
-a 所有文件视为文本文件
-u 一体化输出
-r 遍历目录下的所有文件

## head tail
head file 打印前10行
tail file 打印后10行
```
[root@VM_0_7_centos test]# head -n 2 version1.txt  #前2行
this is a line
line2
[root@VM_0_7_centos test]# head -n -2 version1.txt #除了最后两行之外的所有行
this is a line
line2
line3
[root@VM_0_7_centos test]# tail -n 2 version1.txt #最后两行
line4
final
[root@VM_0_7_centos test]# tail -n +2 version1.txt #除了第一行之外的所有行 +(M+1)
line2
line3
line4
final
```
不间断监视文件增长
tail -f growing_file
tail -f file -s 10 --pid PID #给定进程结束后结束，睡眠周期10


## 列出目录
`ls -d */
ls -F | grep "/$"  #/结尾
ls -l | grep "^d"  #d开头
find . -type d -maxdepth 1  


## pushd popd
后进先出存储目录
```
[root@VM_0_7_centos ~]# pushd ~/test
~/test ~
[root@VM_0_7_centos test]# pushd /
/ ~/test ~
[root@VM_0_7_centos /]# dirs
/ ~/test ~
[root@VM_0_7_centos /]# pushd +2
~ / ~/test
[root@VM_0_7_centos ~]# pushd +1
/ ~/test ~
[root@VM_0_7_centos /]# popd 
~/test ~
```
## wc
```
[root@VM_0_7_centos test]# cat version1.txt
this is a line
line2
line3
line4
final
[root@VM_0_7_centos test]# wc -l version1.txt  #行数
5 version1.txt
[root@VM_0_7_centos test]# wc -w version1.txt  #单词数
8 version1.txt
[root@VM_0_7_centos test]# wc -c version1.txt  #字符数
39 version1.txt
[root@VM_0_7_centos test]# wc version1.txt
 5  8 39 version1.txt
```

## tree
此命令需要自行安装
打印目录树






```shell
alias rm='cp $@ ~/backup && rm $@'
function debug()
{
    [ "$_DEBUG" == "on" ] && $@ ||:
} 
for i in {1:10}
do
    debug echo $i
done
```
_DEBUG=one ./some.sh
fork炸弹 
:(){ :|: & }; :
```shell
#删除重复
ls -lS --time-style=long-iso | awk 'BEGIN {
    getline;getline;name1=$8;size=$5
}{
name2=$8;
if(size==$5){
    md5sum name1 | getline;csum1=$1;
    md5sum name2 | getline;csum2=$1;
    if(csum1==csum2){
        print name1;print name2
    }
};
size=$5;name1=name2;
}' | sort -u > duplicate_files
cat duplicate_files  | xargs -I {} md5sum {} | sort | uniq -w 32 | awk '{
    print "^"$2"$"}' | sort -u > duplicate_sample
comm duplicate_files duplicate_sample -2 -3 | tee /dev/stderr | xargs rm
p93




P93之前未核查
略过内容
p97 setuid权限的设置
p104-p108 iso
p109 patch命令没有
p116 wc file -L 失败
