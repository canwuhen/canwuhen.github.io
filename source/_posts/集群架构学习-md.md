---
title: 集群架构学习
date: 2024-02-27 22:36:03
updated: 2024-03-14 12:36:03
cover: /gallery/Anime_Wallpapers/01.jpg
description: 综合架构项目，包括各种服务以及集群搭建
toc: true
---
综合架构项目，包括各种服务以及集群搭建
<!-- more -->
host 解析

```shell
cat >>/etc/hosts <<EOF
172.16.1.5 lb01
172.16.1.6 lb01
172.16.1.7 web01
172.16.1.8 web02
172.16.1.9 web03
172.16.1.10 web04
172.16.1.31 nfs01
172.16.1.41 backup
172.16.1.51 db01
172.16.1.61 m01
EOF
```



## 综合架构

什么是综合架构

要求

综合架构集群架构

综合架构技术栈



## 什么是综合架构

- 一般架构通用的结构，这就是综合架构
- 综合架构也是一个参考，每个公司都有自己的特别的架构或需求

## 新的要求

- 第一阶段-运维基操，故障及故障提示，知识点 [简书-[故障集合]Linux必备故障及原因50个](https://www.jianshu.com/p/99ec5bb4183f)
- 第二阶段-网站集群架构，遇到故障提示尝试解决

## 综合架构详解

- 分别通过开发，用户，运维角度分析网站架构

| 角度     | 说明                                               | 相关操作                                               |
| -------- | -------------------------------------------------- | ------------------------------------------------------ |
| 开发角度 | 主要关注书写代码，这些代码需要服务器服务运行       | 存放代码，开发如何提交运维，运维如何部署，如何测试代码 |
| 用户角度 | app 或浏览器访问网站，整个访问流程中涉及的服务功能 | 整个网站架构的核心                                     |
| 运维角度 | 如何快速部署，自动化部署，自动话监控               | 如何自动化维护，监控，故障修复                         |

开发角度架构

dev-> 代码存放(git\github\gitee\gitlab)->测试环境（Jenkins/Sonarqube）->发布网站

用户角度架构

user->DNS->互联网->CDN->防火墙(80\443)->网

## 技术栈

| 角度     | 功能                     | 服务                                                         |
| -------- | ------------------------ | ------------------------------------------------------------ |
| 开发角度 | 存放代码                 | Gitlab，Gogs，Github，Gitee                                  |
|          | 集成，发布               | Jenkins                                                      |
|          | 代码检查                 | Sonarqube                                                    |
|          | 私服，软件包仓库（JAVA） | Nexus                                                        |
| 用户角度 | DNS解析                  | DNS云解析（阿里），Bind9，DNSmasq，CoreDNs                   |
|          | CDN缓存                  | 各种厂商CDN服务阿里，蓝汛，七牛                              |
|          | 防火墙                   | 硬件防火墙：深信服，华为，奇安信 <br />软件防火墙：firewalld，iptables<br />公有云：安全组，态势感知，云安全中心 |
|          | 负载均衡                 | 硬件：F5，<br />软件：nginx（tengine，openResty），haproxy，lvs<br />公有云： |
|          | web网站服务器            | 基本：nginx，tengine，openResty<br />架构：<br />LNMP（PHP）：Linux，Nginx，MySQL，PHP<br />LNMT（JAVA）：Linux，Nginx，MySQL，Tomcat<br />LNMP（Python）：Linux，Nginx，MySQL，Python<br />LNMG（Go）：Linux，Nginx，MySQL，Go |
|          | 缓存服务器               | Redis，Kafka，xxxMQ                                          |
|          | 数据库服务器             | MySQL，Oracle<br />国产：TiDB，openGauess,OceanBase          |
|          | 存储                     | 硬件存储<br />软件存储：== **NFS** ==，分布式存储<br />对象存储：阿里OSS，七牛云对象存储，腾讯OSS |
| 运维角度 | VPN                      | 硬件：开源OpenVPN                                            |
|          | 堡垒机                   | 开源：Teleport，Jumpserver<br />商业：硬件，软件             |
|          | 批量管理                 | Ansible                                                      |
|          | 监控                     | Zabbix，Grafana，Prometheus                                  |
|          | 日志收集                 | Elastic Stack（ELK，EFK，EBLK,ElasticSearch,Filebeat,Logstash,Kibana) |
|          | 审计                     | SQL审计，Yearning，Archery                                   |
|          | 安全                     | 商业：AppScan，Nessus，<br />开源：ClamAV                    |



## 1.备份服务

- 备份服务：需要使用脚本，打包备份，定时任务
- rsyncd服务，不同主机之间传输
- 特点：
  - rsync是一个服务也是命令
  - 使用方便，有多种模式
  - 增量传输

```shell
#将backup的 /etc目录传输到 nfs的tmp下

 scp -r /etc/sysconfig/ root@10.0.0.31:/tmp/
 #-r 传输文件夹
```

### reync参数选项

| 参数                         | 说明                                                         |
| ---------------------------- | ------------------------------------------------------------ |
| -a                           | -r递归复制<br/>-L复制软连接<br/>-p保持权限不变<br/>-m保持修改时间不变<br/>-o所有者不变<br/>-g用户组不变<br/>-D --devices --sp<br/> |
| -v                           | 显示过程                                                     |
| -z                           | 传输数据的时候进行压缩                                       |
| -P                           | 显示每个文件传输过程（进度条)断点续传--partial --progress    |
| --bwlimit                    | 限速 注意不要与-z一起使用                                    |
| --exclude<br />--excludefrom | 排除                                                         |
| --delete                     | 目标目录与原目录保持一致的传输（高度保持2遍一致，实时同步）  |
| --password                   |                                                              |

```shell
#限速并传输
rsync -aP --bwlimit=500kb
/tmp/1g10.0.0.31:/mnt/
```



### rsync企业应用

| 应用场景                            | 应用建议                                                     |
| ----------------------------------- | ------------------------------------------------------------ |
| rsync作为命令使用                   | 临时拉取，推送数据，未来这个需求可以使用scp命令实现          |
| rsync服务+定时任务                  | 定时备份，定期备份案例（定时任务进行备份+通过rsync传输备份） |
| rsync服务+serync/lsyncd实现实时同步 | 解决存储服务单点问题                                         |

### rsunc不同模式

- 本地模式

```shell
rsync -a /etc/ /tmp/
rsync -a /etc /opt/

```

> :warning:在rsync中对于 **/etc**/和 **/etc** 是有区别的
>
> /etc 	/etc目录 +目录的内容
>
> /etc/          /etc/目录下面的内容	

- 远程模式

```shell
rsync -av /etc root@10.0.0.31
```



- 守护进程模式

  修改配置文件/etc/rsyncd.conf

```shell
fake super =yes  #如果不开启测C7传输报错（伪装成root）
uid = rsync
gid = rsync
use chroot = no
max connections = 2000 #最大连接数
timeout = 600          #超时链接时间
pid file = /var/run/rsyncd.pid  #存放服务的pid
lock file = /var/run/rsync.lock #进程锁文件 防止重复运行
log file = /var/log/rsyncd.log  #服务端日志
##ignore errors  #忽略错误
read only = false  #可以读写
list = false   #关闭rsrnc服务端列表
#hosts allow  = 10.0.0.0/24
#hosts deny = 0.0.0.0/32
auth users = rsync_backup
secrets file = /etc/rsync.password
###########################
[data]
path =/data           

```

后续配置（准备用户，目录以及密码文件）

```shell
#添加虚拟用户
useradd -s /sbin/nologin -M rsync

#创建密码文件
echo 'rsync_backup:123 '>/etc/rsync.password
chmod 600 /etc/rsync.password
#共享目录与权限
mkdir /data/
chown -R rsync.rsync /data/
```

启动服务

```
systemctl start rsyncd  #开启服务
systemctl enable rsyncd #设置运行
systemctl status rsyncd #查看状态
```

传输测试

```
rsync -av /etc/hostname rsync_backup@backup::data
```

设置免密登录

```
echo '123' >> /etc/rsync.client
chmod 600 /etc/rsync.client 
```

访问控制-安全措施

- hosts allow 只准指定的ip或网段访问
- hosts deny 拒绝

### 项目：全网备份项目

- #### 1）环境准备

| 角色     | 主机   | 运行服务     |
| -------- | ------ | ------------ |
| 存放备份 | backup | rsync 服务端 |
| 生成备份 | nfs01  | rsync 客户端 |
| 生成备份 | web01  | rsync 客户端 |

- #### 2）项目步骤

| 步骤说明                         | 详细内容                 |
| -------------------------------- | ------------------------ |
| :one:rsync服务端并调试           | 服务端配置 /backup目录   |
| rsync客户端节点配置配置脚本      | 备份<br />推送<br />清理 |
| 其他节点匹配值定时任务           | 定时运行脚本             |
| 在备份服务器上检查并发送结果邮件 |                          |

- #### 3）rsync服务端调试

- #### 4）客户端备份脚本

  bak_conf.sh

  ```sh
  #!/bin/bash
  #desc:备份服务器的配置文件
  
  #1.变量
  ip=$(hostname -I | awk '{print $2}')
  backup_dir=/backup/${ip}
  time=`date +%F_%w`
  backup_file=conf-${time}.tar.gz
  backup_server_ip=172.16.1.41
  mode_name=backup
  user_name=rsync_backup
  
  #2.备份命令
  mkdir -p ${backup_dir}
  tar zcf ${backup_dir}/${backup_file} /etc/ /var/spool/cron
  
  #md5
  md5sum ${backup_dir}/${backup_file} >${backup_dir}/fingerprint.md5
  
  #3.推送命令
  rsync -av ${backup_dir} ${user_name}@${backup_server_ip}::${mode_name} --password-file=/etc/rsync.client
  
  #4.删除旧备份
  find ${backup_dir} -type  f -name "*.tar.gz" -mtime +7 |xargs  rm -f
  
  ```

  

- #### 5）服务端脚本测试

  checkbak.sh

  ```sh
  #!/etc/bash
  #desc:检查旧的备份并删除
  
  result_file=/server/scripts/result.txt
  
  #0.清理旧备份
  find /backup/ -type f -name "*.tar.gz" -mtime +180  |xargs rm -f 
  #1.统计备份结果
  find /backup/ -type f -name "*.tar.gz" |xargs \
  ls -lhd |\
  awk -F '[ /]+' '{print $(NF-1),$NF,$5 }' |\
  column -t > ${result_file} 
  
  #校验
  find /backup/ -name "fingerprint.md5" |xargs md5sum -c &>>${result_file}    
  
  #2.发送结果
  mail -s "每日备份结果" 1296959377@qq.com <${result_file}  
  ```


- #### 6）增加校验功能

单个文件的校验

```sh
#创建oldboy.txt测试文件
echo oldboy >oldboy.txt
#创建md5的值
md5sum oldboy.txt
3fe396c01f03425cb5e2da8186eb090do1db0y.txt#MD5值和文件名
#保存md5值到文件
md5sum
oldboy.txt <oldboy.md5
#通过md5值文件，校验文件的md5信息（文件是否变化）
md5sum -c
oldboy.md5
oldboy.txt:0K#0K表示没有改变
#修改文件内容，测试MD5是否能发现文件是否变化.
echo lidao >oldboy.txt
#进行md5检查
md5sum -c oldboy.md5
oldboy.txt:FAILED#FAILED表示文件内容发生变化，md5值变了.
md5sum:WARNING:1 computed checksum did NOT match
```

> md5sum-c说明-check根据md5文件记录进行对比
> md5值   	路径/名字

多个文件的校验

```sh
#给/etc/下面所有的文件创建ND5保存到指定文件中
find /etc/-type f md5sum
>etc-all.md5
#进行校验
md5sum -c etc-all.md5
#修改/etc/下某个文件比如增加个"#"
vim /etc/rsyncd.conf
#进行校验
md5sum -c etc-all.md5
#只显示md5异常的文件
md5sum --quiet -c etc-all.md5
```

## 2.数据服务-存储服务

### 1）环境说明

| 主机            | 运行服务   |              |
| --------------- | ---------- | ------------ |
| nfs01 10.0.0.31 | nfs 服务端 |              |
| web01 10.0.0.7  | nfs 客户端 | 通过挂载使用 |

### 2）服务端部署

- 部署

  ```sh
  yum install -y rpcbind nfs-utils
  ```

  

- 启动

```
#启动rpcbind
systemctl enable rpcbind
systemctl start rpcbind
rpcinfo -p
#启动nfs
systemctl enable nfs
systemctl start nfs

```

- NFS服务端的配置文件 `/etc/exports`

  ```sh
  echo '/data/	172.16.1.0/24(rw)' >> /etc/exports
  #配置表示：172.16.1.0/24网段对NFS服务端的/data/目录，拥有rw读写权限
  systemctl reload nfs
  ```

  > reload 表示优雅重启，不会断开已有连接
  >
  > restart在nfs中，会导致客户端一段时间卡住

-  NFS创建数据目录修改权限

  ```
  mkdir -d /data/
  chown nfsnobody.nfsnobody /data/
  ```



### 3）客户端挂载

```sh
yum install -y nfs-utils
mount -t nfs 172.16.1.31:/data /mnt
```

### NFS相关文件

| nfs服务端或客户端 | 说明                                                         |
| ----------------- | ------------------------------------------------------------ |
| NFS服务端配置     | /etc/exports(配置文件里面的) -> /var/lib/nfs/etab(当前使用的nfs服务端配置) |
| NFS客户端配置     | 客户端：mount  /etc/rc.local 或/etc/fstab -> /proc/mounts(当前系统的挂载情况) |

- nfs 客户端永久挂载

  ```sh
  #方法01挂载命令写入到/etc/rc.Local
  chmod +x /etc/rc.d/rc.Local
  
  #方法02按照/etc/fstab格式要求书写
  设备 								挂载点 		文件系统类型 		挂载参数 		是否检查		是否备份
  172.16.1.31:/data/ /upload/ 		nfs 				defaults 		0 					0
  ```

  > 温馨提示：
  >
  > 如果配置了nfs客户端永久挂载，需要优先启动nfs服务端

### NFS服务端配置文件

- 核心配置

| 服务端配置选项 | 说明                                                         |
| -------------- | ------------------------------------------------------------ |
| rw             | 可以读写共享目录                                             |
| ro             | 只读read only                                                |
| sync           | 同步，只要用户上传，就把数据写到磁盘上，                     |
| async          | 异步，用户上传的数据，nfs先临时存放到内存中，过一段时间写入到磁盘.并发高，数据可能丢失 |

### 4）服务端用户压缩

| 服务端配置选项-用户压缩 | 说明                                                         |
| ----------------------- | ------------------------------------------------------------ |
| root_squash             | 如果客户端是root用户访问，则到了nfs服务端会被压缩（默认的）  |
| no_all_squash           | 如果客户端不是root用户访问，则不进行压缩（保存原始用户，默认的） |
| all_squash              | 所有用户都进行压缩（不是太安全）                             |
| anonuid和anongid        | 用于指定压缩的匿名用户（默认是nfsnobody用户）                |

### 5）用户压缩案例

- 用户压缩案例：设置/nfsdata共享目录，匿名用户为Www.客户端挂载到/upload-video/
- www用户的uid,gid:1999

```sh
#1.所有主机(nfs服务端和nfs客户端)添加用户www uid:统一1999 1999
groupadd -g 1999 www
useradd  -u 1999 -g www -s /sbin/nologin -M www

#2.nfs服务端配置
echo '/nfsdata/ 172.16.1.0/24(rw,all_squash,anonuid=1999,anongid=1999)' >>/etc/exports

mkdir -p /nfsdata/
chown www.www /nfsdata/

#3.nfs客户端挂载
mount -t nfs 172.16.1.31:/nfsdata/ /upload-video/
mkdir -p /upload-video/
touch abc{1..10}.mp4

```

### NFS优化

- 终极优化目标：
  - 尽可能让用户的请求在访问网站架构之前解决掉。（尽可能把用户的请求往前推）

- NFS优化：硬件（物理服务器+nfs服务）

- NFS安全优化：客户端挂载只能上传，无法执行.

```sh
 mount -o noexec,nosuid,nodev -t nfs 172.16.1.31:/data /video/
#这几个是客户端挂载选项
#noexec 挂载的nfs目录中如果有命令，无法运行
#nouid 带有suid的命令
#nodev 带有特殊属性的文件
```

- NFS有单点故障，选择其他存储：公有云0SS(阿里云)（对象存储在代码里面调用）
- 课后研究分布式存储：GLusterFS(GFS)

## 3.实时同步存储服务



| 作用                                  | 服务器            | 环境准备                                                 |
| ------------------------------------- | ----------------- | -------------------------------------------------------- |
| rsync服务端                           | 10.0.0.31backup01 | 完成rsync服务端部署                                      |
| sersync服务端（rsync客户端）nfs服务器 | 10.0.0.41 nfs01   | 解压安装<br />配置sersync<br />配置并创建nfs共享目录文件 |
| 用户客户端                            | 10.0.0.7 web01    | 挂载到nfs01共享目录                                      |

`Sersync2/confxml.xml`

```sh
<?xml version="1.0"        
<head version="2.5">
    <host hostip="localhost" port="8008"></host>
    <debug start="false"/>
    <fileSystem xfs="false"/>
    <filter start="false">
        <exclude expression="(.*)\.svn"></exclude>
        <exclude expression="(.*)\.gz"></exclude>
        <exclude expression="^info/*"></exclude>
        <exclude expression="^static/*"></exclude>
    </filter>
    <inotify>
        <delete start="true"/>
        <createFolder start="true"/>
        <createFile start="false"/>
        <closeWrite start="true"/>
        <moveFrom start="true"/>
        <moveTo start="true"/>
        <attrib start="false"/>
        <modify start="false"/>
    </inotify>

    <sersync>
        <localpath watch="/opt/tongbu">
            <remote ip="127.0.0.1" name="tongbu1"/>
            <!--<remote ip="192.168.8.39" name="tongbu"/>-->
            <!--<remote ip="192.168.8.40" name="tongbu"/>-->
        </localpath>
        <rsync>
            <commonParams params="-artuz"/>
            <auth start="false" users="root" passwordfile="/etc/rsync.pas"/>
            <userDefinedPort start="false" port="874"/><!-- port=874 -->
            <timeout start="false" time="100"/><!-- timeout=100 -->
            <ssh start="false"/>
        </rsync>
        <failLog path="/tmp/rsync_fail_log.sh" timeToExecute="60"/><!--default every 60mins execute once-->
        <crontab start="false" schedule="600"><!--600mins-->
            <crontabfilter start="false">
                <exclude expression="*.php"></exclude>
                <exclude expression="info/*"></exclude>
            </crontabfilter>
        </crontab>
        <plugin start="false" name="command"/>
    </sersync>

    <plugin name="command">
        <param prefix="/bin/sh" suffix="" ignoreError="true"/>  <!--prefix /opt/tongbu/mmm.sh suffix-->
        <filter start="false">
            <include expression="(.*)\.php"/>
            <include expression="(.*)\.sh"/>
        </filter>
    </plugin>

    <plugin name="socket">
        <localpath watch="/opt/tongbu">
            <deshost ip="192.168.138.20" port="8009"/>
        </localpath>
    </plugin>
    <plugin name="refreshCDN">
        <localpath watch="/data0/htdocs/cms.xoyo.com/site/">
            <cdninfo domainname="ccms.chinacache.com" port="80" username="xxxx" passwd="xxxx"/>
            <sendurl base="http://pic.xoyo.com/cms"/>
            <regexurl regex="false" match="cms.xoyo.com/site([/a-zA-Z0-9]*).xoyo.com/images"/>
        </localpath>
    </plugin>
</head>
```

## 4.SSHD服务

openssh服务和telnet服务

|              | 共同点   | 区别       | 应用场景                            |
| ------------ | -------- | ---------- | ----------------------------------- |
| openssh 服务 | 远程连接 | 数据加密的 | 默认使用openssh                     |
| telnet 服务  | 远程连接 | 数据未加密 | 升级openssh服务时，可启用telnet服务 |

- telnet服务	

```sh
安装服务
yum install
-y telnet-server
#启动
systemctl disable telnet.socket
systemctl start telnet.socket
#本地shell中连接
telnet10.0.0.61 23
```

### openssh-server配置文件

- 核心配置文件：`/etc/ssh/sshd_config`

| Openssh服务端配置详解 |                                                              |
| --------------------- | ------------------------------------------------------------ |
| **连接加速**          |                                                              |
| UseDNS no             | 是否开启反向解析：ip→域名或主机名                            |
| GSSAPIAuthentication  | GSS认证功能关闭                                              |
| **安全优化相关**      |                                                              |
| Port                  | 默认是Pont22 端口范围1-65535<br/>推荐1w以上的端口            |
| PermitRootLogin       | 禁用root用户远程登录权限.默认是yes(可以让root远程登录)(ubt系统中默认是no) |
| ListenAddress         | 监听的地址（后面需要指定本地网卡的p地址）可以控制用户只能通过内网访问 |

## 5.集群批量管理-密钥认证与ansible模块

- 部署密钥认证
- ansible批量管理-部署-分发
- ansible的常用模块

### 密钥认证

1. 创建密钥对

   ```sh
   ssh-keygen -t rsa
   ```

2. 分发公钥

   ```sh
   ssh-copy-id -i /root/.ssh/id_rsa.pub root@10.0.41
   ```

3. 连接测试

   ```sh
   ssh root@10.0.41 hostname -I
   ```

4. 自动化创建密钥

   ```sh
   ssh-keygen -f ~/.ssh/id_rsa -P ''
   
   #-f用于指定私钥的位置
   #-P密码短语设置为空
   ```

   

5. 自动化分发

   ```sh
   #安装密码提供密码
   yum install -y sshpass
   #sshpass命令基本使用
   #-p指定密码
   
   ssh10.0.0.7 hostname-I #检查是否需要输入yes/no
   
   #然后使用：
   sshpass -p1 ssh 10.0.0.7 hostname -I
   
   #使用sshpass与ssh-copy-id分发公钥
   ssh-copy-id 10.0.0.31 #检查是否需要输入yes/no
   #然后使用：
   sshpass -p1 ssh-copy-id 10.0.0.31
   
   #第一次连接时候提示yes/no 临时不检查主机信息 -oStrictHostKeyChecking
   sshpass -p1 ssh-copy-id -i ~/ssh/id_rsa.pub -oStrictHostKeyChecking=no 10.0.0.41
   ```

6. 自动分发脚本 `fenfa.sh`

   ```sh
   
   ```



### Absible自动维护

`/etc/ansible/ansible.cfg`

```
#关闭取消验证
#开启日志
71:host_key_checking =False
111:log_path=/var/log/ansible.log
```

主机清单文件  `/etc/ansible/host`

```sh
[web]
172.16.1.7
172.16.1.8
[nfs]
172.16.1.31
[backup]
172.16.1.41


#子组 组名:children
[data:children]
backup
nfs

```

运行ansible

```sh
ansible all -m ping #所有的主机
andible web - m ping #web分组的主机
andible 172.16.1.x - m ping #某个主机
```

### Ansible模块

- ansible中的模块就类似于Linux中的命令，我们Linux命令管理系统，我们通过andible模块实现批量管理

| 模块分类       | 模块                                                         |
| -------------- | ------------------------------------------------------------ |
| 命令和脚本模块 | command模块：默认的模块，支持简单的命令不支持特殊符号        |
|                | shell 模块：执行命令，支持特殊符号                           |
|                | script模块：分发脚本并执行                                   |
| 文件           | file ：创建目录，文件，软连接                                |
|                | copy：远程分发文件，修改权限，所有者，备份                   |
|                | systemd：服务管理                                            |
|                | service：服务管理（了解）                                    |
| 软件包         | yum源 yum_repository                                         |
|                | yum命令                                                      |
|                | get_url 下载软甲                                             |
| 系统管理       | mount模块 挂载                                               |
|                | cron模块 定时任务                                            |
| 用户管理       | group模块 管理用户组                                         |
|                | user模块 管理用户                                            |
| 其他           | 压缩解压(unarchive),rsync模块(synchronize),数据库模块(mysql_db,mysql_user)... |
|                | ansible管理docker k8s zabbix grafana...                      |
| 用于调试检查   | ping模块检查ansible与其他节点连通性.                         |
|                | debug模块 用于检查/显示变量                                  |

ansible选项

| ansible             |      |
| ------------------- | ---- |
| -i 指定主机清单文件 |      |
| -m 指定模块         |      |
| -a 指定模块中的选项 |      |
|                     |      |

- ### Command 模块

  ```sh
  #批量获取主机名(ansible 默认command模块)
  ansible all -a 'hostname'
  ```

  

- ### shell模块

  ```sh
  #批量获取ip地址
  ansible all -m shell -a "ip a s eth0 | awk -F'[/ ]+' 'NR==3{print \$3}'
  "
  ```

  > :warning: 温馨提示：
  > shell模块不推荐执行较为复杂的指令，如果需要执行放在脚本中执行.
  > 避免因为特殊符号和引号导致的问题.

- ### script模块

  - 执行流程：分发脚本，在被管理端运行脚本

  ```sh
  #!bin/bash
  #author:lidao996
  #desc:系统巡检脚本
  nostname
  hostname -I
  ip a s eth0 | awk -F'[/ ]+' 'NR==3{print $3}'
  uptime
  whoami
  date +%F
  sleep 10
  
  
  #测试巡检脚本
  ansible all -m script -a '/server/scripts/sys_info.sh'
  
  ```

  

- ### file模块

  > file模块不仅可以创建文件，还可以管理目录，管理软连接

  file模块选项

  | file 模块 | 模块说明                                                     |
  | --------- | ------------------------------------------------------------ |
  | path      | 路径（目录，文件）**必填**                                   |
  | src       | 源文件一般用于link 创建软连接                                |
  | state     | 状态（模式）<br />state=directory 创建目录<br />state=file（默认）更新文件，如果文件不存在也不创建<br />state=link 创建软连接<br />state=touch 创建文件 |
  | state     | state=absent 删除（ :warning: 注意如果是目录则会递归删除目录 ） |
  |           |                                                              |
  | mode      | mode=755 创建并修改权限                                      |
  | onwer     | onwer=root                                                   |
  | group     | group=root                                                   |

  ```sh
  #创建 /opt/lidao.txt
  ansible all -m file -a 'path=/opt/lidao.txt state=touch'
  
  #创建目录 /app/
  ansible all -m file -a 'path=/app/ state=directory'
  
  #创建软连接 /etc/hosts 创建软连接到/opt/下
  ansible all -m file -a 'src=/etc/hosts path=/opt/hosts state=link'
  
  #创建/ans-backup/目录 所有者是canwuhen
  ansible all -m file -a 'path=/ans-backip/ owner=canwuhen group=cawuhen mode=700'
  ```

  

  

- ### copy模块

批量分发，类似于scp

| copy模块 |                                |
| -------- | ------------------------------ |
| **src**  | source管理端源文件             |
| **dest** | destination，被管理端的目录    |
| backup   | backup=yes则会在覆盖前进行备份 |
| mode     | 修改权限                       |
| owner    | 修改为指定所有者               |
| group    | 修改为指定用户组               |

```sh
# 分发书写好的/etc/hosts文件，如过文件存在则备份下
ansible all -m copy -a 'src=/etc/hosts dest=/etc/hosts backup=yes'
```

> 额外扩展：
>
> copy是推送
>
> fetch是拉取

- ### lineinfile模块

> 修改文件使用

服务管理-systemd

