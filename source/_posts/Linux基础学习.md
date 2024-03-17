---
title: Linux基础学习
date: 2024-02-23 10:54:59
updated: 2024-03-13 12:37:06
description: linux 基础命令以及环境搭建
cover: /gallery/Anime_Wallpapers/19.jpg
toc: true
---
linux 基础命令以及环境搭建
<!-- more -->

# Linux 版本选型

| 系统版本 |                                       |
| -------- | ------------------------------------- |
| CentOS   | 没有LTS 选择较新的版本，CentOS7.x 7.9 |
| Ubuntu   | LTS （长期稳定版）18.04 、20.04       |
| 麒麟     | Kylin v10（CentOS7 更新了内核）       |



# 工作学习环境部署

## 1.设置ROOT 密码

```shell
sudo  passwd
```



## 2.网卡配置

设置静态IP

```shell
ifconfig

vim /etc/sysconfig/network-scripts/ifcfg-ens33 

BOOTPROTO=static
ONBOOT=yes
IPADDR=你的静态IP地址
NETMASK=你的子网掩码
GATEWAY=你的默认网关
DNS1=首选DNS服务器
DNS2=备用DNS服务器

systemctl stop firewalld.service 关闭防火墙


```

重启网卡：



查看防火墙状态：

关闭防火墙：

禁用防火墙：

## 3.更改yum源

1. 备份原yum源


```shell
mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup
```

下载最新的centos yum源（centos7版本 ）

```shell
wget -O /etc/yum.repos.d/CentOS-Base.repo https://mirrors.aliyun.com/repo/Centos-7.repo #阿里云
#或者
curl -o /etc/yum.repos.d/CentOS-Base.repo https://mirrors.aliyun.com/repo/Centos-7.repo

```

2）互联网epel源配置
#扩展源，有很多软件在基本源中没有

```sh
 wget -q -O /etc/yum.repos.d/epel.repo \
http://mirrors.aliyun.com/repo/epel-7.repo
```

清空原yum缓存

```
yum clean all
```

运行 yum makecache 生成缓存

```
yum makecache
```

查看是否生效

```shell
yum repolist
```

## Linux核心目录结构

| 目录名称 | 说明                                   |
| -------- | -------------------------------------- |
| /        | 根目录                                 |
| /home    | 普通用户的家目录                       |
| /root    | Root 用户的家目录                      |
| /etc     | 系统，服务配置文件的目录               |
| /tmp     | 临时目录                               |
| /ptoc    | 系统服务，进程信息，类似于汽车的仪表盘 |
| /dev     | Device，设备文件目录，硬盘、光盘...    |

> [《鸟叔的Linux私房菜》 P165]()



# Liunx核心命令

- ## 进入某个目录：**`cd`**

- ## 列表显示目录内容：**`ls`**

  - -l 	long以长格式显示（显示详情信息），简写成ll
  - -t        time 按照修改时间顺序
  - -r        逆序排序，一般-rt一起使用
  - -h       以人类可读形式显示大小

- ## 限时自己所在目录：**`pwd`**

- ## 创建目录：**`mkdir`**

  - **-p**	创建多层目录

- ## 创建文件：**`touch`**

- ## 移动\改名：**`mv`**

- ## 复制，拷贝：**`cp`**

  - -a 	all简单理解为复制全部
  - -r          递归复制，复制目录
  - -p        复制时保持属性不变
  - -d        可以复制软连接 

- ## 输出信息到屏幕：**`echo`**  通常配合重定向符号使用：

  - \> :重定向符号，先清空文件再写入

  - \>>:追加重定向，把信息写入在末尾

  - ```shell
    [root@localhost tmp]# echo lada996 oldboy008 >> ./testecho.txt 
    [root@localhost tmp]# echo lada996 oldboy008 >> ./testecho.txt 
    [root@localhost tmp]# echo lada996 oldboy008 >> ./testecho.txt 
    [root@localhost tmp]# echo lada996 oldboy008 >> ./testecho.txt 
    [root@localhost tmp]# cat testecho.txt 
    dbd
    jdjfjdksjj
    lada996 oldboy008
    lada996 oldboy008
    lada996 oldboy008
    lada996 oldboy008
    [root@localhost tmp]# echo lada996 oldboy008 > ./testecho.txt 
    [root@localhost tmp]# cat testecho.txt 
    lada996 oldboy008
    
    [root@localhost tmp]# cat /etc/hostname >> testecho.txt 
    [root@localhost tmp]# cat testecho.txt 
    lada996 oldboy008
    localhost.localdomain
    ```

    

- ### 编辑文件与修改文件：**`vi/vim`**

  快捷键：

  ```sh
  行间调整：
  移动到最后一行 Shift+g
  移动到第一行 gg
  移动到某一行 100gg
  
  列间调整：
  光标移动到行首 ^
  光标移动到行尾 $
  ```

  - 显示行号：`:Set nu`

  - 隐藏行号： `：no nu`

  - 搜索：`/xxx`    `n`健下一个

  - 批量编辑：

    ```sh
    批量删除：
    ctrl + v 进入批量操作模式
    选择要操作的区域
    然后按 d，删除
    
    批量添加：
    ctrl + v 进入批量操作模式
    选择要操作的区域
    选完后按 shift + i 进入编辑模式
    写入内容
    按esc等待
    ```

    

- ## 删除：**`rm`**

  - -r : 递归删除
  - -f ：就是force的意思，忽略不存在的文件，不会出现警告


## 目录深入了解

### /etc/目录下

| /etc/文件                                        | 说明                                       | 企业应用                                            |
| ------------------------------------------------ | ------------------------------------------ | --------------------------------------------------- |
| **/etc/hostname**                                | 主机名配置文件                             | 每个Liunx主机配置主机名，根据主机名区别不同的功能   |
| **/etc/hosts**                                   | 主机名和ip的对应关系                       | 在网站中做访问用                                    |
| **/etc/sysconfig/network-scripts/ifconfig-eth0** | 网卡配置文件                               | 物理机需要改，云服务器自动                          |
| **/etc/rc.local**                                | 开机自启动文件                             | 吧需要开机的时候 自动运行的命令，服务放到这个文件中 |
| **/etc/fstab**                                   | 开机自动挂载的文件                         | 配置磁盘挂载使用                                    |
| **/etc/motd**                                    | 文件中的内容会在**用户登录系统后**显示出来 | 显示温馨提示，佛祖保佑永不宕机                      |
| **/etc/issue  /etc/issue.net**                   | 文件中的内容会在**用户登录系统前**显示出来 | 这个文件内容应该清空                                |

1.修改主机名

```
hostname 
```

2.网卡配置文件

​	/etc/sysconfig 系统配置文件

​	/etc/sysconfig/network-scripts/ 网络的配置文件

3./etc/rc.local 开机自启动文件

```
第一次使用这个文件要给/etc/rc.d/rc/local增加执行权限
```

### /var/目录下

> var 经常变化的内容，variable 比如：日志

| /var/ 经常变化的内容 | 说明                         |
| -------------------- | ---------------------------- |
| /var/log             | 系统以及服务的各种日志目录   |
| /varlog/secure       | 存放用户登录情况日志         |
| /var/log/messages    | 通用各种服务的日志，日志很多 |

### /proc/目录下面

| /proc/ 系统，进程信息，内核 | 说明                           |      |
| --------------------------- | ------------------------------ | ---- |
| /proc/cpuinfo               | cpu信息                        |      |
| /proc/meminfo               | 内存信息                       |      |
| /proc/loadavg               | 系统负载信息，衡量系统繁忙程度 |      |
| /proc/mounts                | 系统挂载信息                   |      |

1.cpu信息

> lscpu  查看cpu信息，核心数，颗数

2.内存信息



## 日志查询四剑客

> 查看日志不要使用，cat或vim，vi命令，未来日志可能会很多
>
> 如果使用cat查看，会刷屏
>
> 如果使用vi/vim查看，从磁盘中加载到内存，占用系统内存

### 日志查询命令使用

1.head 限时文件头几行，默认是头10行

- -n ：限时头n行，默认限时头10行

2.tail 显示文件后几行，默认是后10行

- -n
- -f 显示文件末尾的实时更新

3.less 按页限时文件内容

- -N 显示行号

```
q		退出查看
空格或f  下一页
b		上一页

G 最后一行
g 到第一行
19g 到第19行
```

4.more 按页显示文件内容，达到最后一行就退出

## wc统计

> word count 统计文件单词数，大小，**行数**,主要用于统计行数

- -l 统计行数

```shell
#统计secure文件中密码失败的次数  （password check failed）

#1.过滤输出中错误信息
#2。交给wc -l统计

grep 'password check failed' /var/log/secure |wc -l
3

```



## which 

> 查询命令的位置



## 文件比较命令

> 对比新旧配置文件，修改了哪些内容
>
> 这时候我们需要进行文件对比操作，可以通过diff，vimdiff命令实现

- diff

  

- vimdiff（推荐使用）

## 排序去重组合:star::star::star:

> 统计日志，日志分析，系统信息统计必备命令

- sort：排序
  - -n   按照数字排列
  - -k   指定某一列，根据某一列进行排序
  - -r   逆序排序
  - -t   指定分隔符，单个字符
- uniq：去重
  - -c  去重并显示数量

## 日期组合

- date
- ntpdate
- 特殊符号

### date

- +: 格式
- -d 根据说明修改时间
- -s 修改时间

### ntpdate 同步时间

```shell
ntpdate ntp1.aliyun.com #同步阿里云时间
提示offset xxx sec 即成功
```

### timedatectl修改时区

```shell
timedatectl status #查看信息
timedatectl set-timezone Asia/Shanghai
```

### 特殊符号

- `` :反引号里的命令会先执行
- |  ：管道符会将前面每一个进程的输出（stdout）直接作为下一个进程的输入（stdin）,一般过滤



## Linux 四剑客

- grep 筛选

- find 查找文件

- sed 

- awk

  awk概述
  
  格式
  
  执行流程
  
  

# 文件属性体系

## inode和block部分

> - inode 索引节点 ，inode类似于身份证号，通过inode文件能够找到文件
>
> - :star: inode 是一个空间，类似于身份证
>   - inode 红建中存放的是文件属性信息，文件大小，修改时间，权限，所有者...
>   - inode 空间存放block的位置 （指向文件实体的指针）
> - :star:block块（数据块）：存放数据

查看

```
block： df -h （磁盘空间使用情况） 
inode df -i

du(查看目录所占磁盘空间)
du -sh /etc/
-s 仅列出总量，不展示子目录
-h 以人类读懂形式展示文件大小
```

## 文件类型

| Linux常见文件类型 | 含义                                   |
| ----------------- | -------------------------------------- |
| **-**             | 文件 file                              |
| **d**             | 目录 directory                         |
| **l**             | 软连接 ，类似于快捷方式                |
| c                 | 字符设备 char 特殊文件，不断输出，吸入 |
| b                 | 块设备 block 硬盘                      |
| s                 | 套接字 socket文件                      |
| p                 | 管道文件                               |

查看文件类型：`file`命令

| 文件类型   |      |
| ---------- | ---- |
| text       |      |
| data       |      |
| 二进制文件 |      |

### 软硬链接 :star: :star::star::star::star:

> - 软链接：类似于windows中的快捷方式，也是一种文件；用于存放源文件的路径（位置+名字）应用最多
> - 硬链接：在同一个分区中的inode号想通了，这些文件互为硬链接

#### 如何创建

- `ln`：（link）创建软硬链接，默认创建硬链接

## 打包压缩三剑客

> 运维活成中，我们需要备份文件，备份某一个目录中的文件，避免空间浪费

| 压缩命令                          | 应用场景                                        |
| --------------------------------- | ----------------------------------------------- |
| tar:star::star::star::star::star: | 大部分场景使用tar即可（创建，查看，解压，压缩） |
| gzip                              | 一般配合其他命令使用                            |
| zip unzip                         | 一般用于解压zip格式的压缩包                     |

### tar:star: :star::star::star::star:

> Linux 打包压缩
>
> - ​	打包（放在一起）tar
>
> - ​	压缩 （节约空间）tar配合参数命令

#### 创建

```shell
tar zcvf /tmp/etc.tar.gz /etc/

v verbose 显示过程
c create 创建包，如果只cf选项就是打包，不压缩
z gzip tar命令创建包后通过gzip工具压缩 所以tar打包 gz压缩 扩展名.tar.gz
f file 指定压缩包，f选项放在最后
```

#### 查看

```shell
tar -tf /tmp/etc.tar.gz

t list列表，查看压缩包内容
```

#### 解压

```shell
tar -zxvf /tmp/etc.tar.gz  默认解压得到当前目录
x 解压
```

#### 解压到指定目录

```shell
tar -zxvf /tmp/etc.tar.gz -C /mnt/

-C 指定目录
```

### zip

> 专门处理.zip格式的压缩包，tar 无法处理
>
> .zip 是windows 和linux共同支持的格式

```shell
压缩
zip -r etc.zip /etc/
解压
unzip etc.zip
```

# 别名与用户管理体系

## 别名

> 别名，给命令进行设置
>
> 应用场景：
>
> - 给常用命令设置一个快捷键
>
> - **给危险命令加上防护措施**	

### 如何创建别名

给rm 设置别名，只要运行就提示“ rm is dangerous ,pls use mv”

#### 0）查看系统别名

alias

```shell
alias
```



#### 1）设置别名

```shell
alias rm='echo rm is dangerous ,pls use mv.'
#设置别名 alias 昵称='命令'
```

> 如果真想删除文件（临时取消别名）
>
> 使用绝对路径 /bin/rm
>
> 使用反斜杠 \别名

#### 2）永久生效

修改配置文件 ~/.bashrc     	/etc/profile(全局)

```
source /etc/profile #配置生效
```

## 用户管理

### UID,GID

> Linux中的用户是有一个标记，类似于身份证号码，叫做UID，GID

- UID user id 用户的id号，身份证号码
- GID group id 用户组ID号，户口本号码

### 用户相关的文件

| 用户相关的文件 |                |
| -------------- | -------------- |
| /etc/passwd    | 存放用户信息   |
| /etc/shadow    | 存放密码信息   |
| /etc/group     | 用户组信息     |
| /etc/gshadow   | 用户组密码信息 |

- /etc/passwd 解释（以：为分隔符）

  ```shell
  root	:x:       0:0	     :root	 :/root	 :/bin/bash
  nobody:x:       99:99    :Nobody :/        :/sbin/nologin
  用户名| 密码标记|	UID:GID|说明信息|	家目录  |  用户使用的命令解释器
  ```

- /etc/shadow

  ```shell
  master:$6$5d5ADhLgwQVeNIaB$/3NkToxaay4KurZlZagewsE67CD4RTdEEQ09ItL.N6cny1Y3XeH1YVwexok9Ow0njIt9w40/OZlv0HwMOgvpq.::0:99999:7:::
  ntp:!!:19780::::::
  用户名 |密码 |密码过期时间
  ```

  

- /etc/group

  ```shell
  root:x:0:
  bin:x:1:
  组名（默认和用户名一致） |密码 |gid |组里的额外用户
  ```

### 用户管理命令

- 增加
- 删除
- 修改
- 查看

#### 增加

- useradd 增加用户

| useradd的选项 | 说明         |
| ------------- | ------------ |
| -u            | 指定用户uid  |
| -s            | 指定解释器   |
| -M            | 不创建家目录 |

- passwd 修改密码

| passwd 选项 | 说明             |
| ----------- | ---------------- |
| --stdin     | 非交互式设置密码 |



- su 切换用户

```shell
su - master
#退出当前用户
#ctrl + d
```



#### 删除

- userdel

| 选项 |                |
| ---- | -------------- |
| -r   | 删除用户家目录 |

#### 修改

- passwd 修改密码
- usermod 修改用户信息

#### 查询

- id 查询用户uid，gid 用户组信息，用户是否存在
- whoami 查询当前用户名字
- w 查看当前登录的用户信息，并且正在做什么
- last 用户的登录情况

### sudo 权限管理

> 用于给普通用户配置的命令，配置了sudo权限后可以以root权限命令

#### sudo授权使用

```
#root 授权配置
visudo
#普通用户使用
sudo + 命令
```

### 堡垒机

| 堡垒机产品   |                      |
| ------------ | -------------------- |
| 开源产品     | Jumpserver，Teleport |
| 物理硬件设备 | 购买                 |
| 云服务       | 购买                 |

-  安装teleport

```shell
#解压
tar -zxvf teleport-server-linux-x64-3.6.4-b3.tar.gz

cd teleport-server-linux-x64-3.6.4-b3
#运行安装命令
setup.sh

#查看状态
/etc/init.d/teleprot status

/etc/init.d/teleprot stop #关闭
/etc/init.d/teleprot start #开启
/etc/init.d/teleprot restart #重启
```

- 关闭防火墙和selinux

  ```shell
  #关闭防火墙
  systemctl stop firewalld
  systemctl disable fiewalld
  
  #关闭selinux（工作中基本关闭）
  setenforce  0 #临时关闭
  vim /etc/selinux/config
  #找出中间行SELINUX=enforcing 修改为 SELINUX=disabled
  
  getenfore #结果是permissive或diabled都表示关闭，如果是enforcing表示开启
  ```

  

- 浏览器访问 http：//10.0.0.200:7190



## Liunx权限

### rwx

| 权限 | 含义          |
| ---- | ------------- |
| r    | read 可读     |
| w    | write 可写    |
| x    | excute 可执行 |

### Linux特殊属性

-  应用场景：预防重要文件或命令被修改.

-  lsattr 查看这种特殊属性

-  chatrr 修改这种特殊属性

  -  a属性 append 只能追加
  -  i属性 immutable 不朽的，无法被毁灭的

  ```
  chattr +a oldboy.txt #-a
  
  chattr +i oldboy.txt #-i
  ```

## 系统管理之软件包管理

- 安装方式推荐
  - yum 优先
  - rpm包
  - 二进制
  - 编译安装

### rpm 安装方式

1. 安装rpm包

   > -ivh
   >
   > -i install
   >
   > -v 显示过程
   >
   > -h 人类可读过程

   ```shell
   案例01 安装rpm包
   #下载软件包
   mkdir -p /server/tools/
   wget --no-check-certificate -P /server/tools/ https://mirrors.tuna.tsinghua.edu.cn/zabbix/zabbix/6.0/rhel/7/x86_64/zabbix-agent2-6.0.0-1.el7.x86_64.rpm
   
   wget下载指定内容，默认下载到当前目录
   -P 下载到指定目录，目录不存在会创建
   --no-check-certificate 下载地址https，加上这个选项，下载失败。
   
   #安装依赖
   yum install -y pcre2
   
   #安装软件包zabbix-agent2
    rpm -ivh zabbix-agent2-6.0.0-1.el7.x86_64.rpm 
   ```

2. 查询

   ```shell
    #检查软件包是否安装成功
    rpm -qa | grep zabbix
    
    #查看软件包内容
    rpm -ql zabbix-agent2
   ```

   

3. 修改-升级

4. 删除

## 三剑客日志处理

特殊符号

| 引号     | 含义                                               |
| -------- | -------------------------------------------------- |
| 单引号   | 所见即所得                                         |
| 双引号   | 和单引号类似，其中的特殊符号可以运行，对于{}不解析 |
| 不加引号 | 和双引号类似 ，额外支持通配符                      |
| 反引号   | 优先执行，先执行反引号里的命令                     |

