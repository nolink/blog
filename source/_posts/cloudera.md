title: 在Centos上搭建Cloudera Manager及Hadoop集群
date: 2016-02-26 17:18:05
tags: [cloudera,hadoop]
---

安装cloudera可以直接选择用repo安装，也可以直接下载RPM包进行安装，本文暂述通过repo安装的方法

* 准备好机器，并修改hostname，注意：hostname不能有下划线

使用以下两个命令来修改Hostname
``` bash
hostname hadoop-prod-slave1
vi /etc/sysconfig/network
```

* 准备好cloudera相关的repo
``` bash
wget http://archive.cloudera.com/cm5/redhat/6/x86_64/cm/cloudera-manager.repo
cp cloudera-manager.repo /etc/yum.repos.d/
```

* 安装cloudera server端，即cloudera manager，这里需要使用到数据库，本文使用cloudera内嵌postgresql

``` bash
yum install cloudera-manager-daemons
yum install cloudera-manager-server-db-2
yum install cloudera-manager-server
```

* 修改cloudera数据库设置

``` bash
vim /etc/cloudera-scm-server/db.properties
```

* 修改监控等功能数据库设置

``` bash
vim /etc/cloudera-scm-server/db.mgmt.properties
```

* 启动cloudera

``` bash
service cloudera-scm-server-db start
service cloudera-scm-server start
```

* 安装Hadoop集群——安装Cloudera agent，agent也需要使用daemons里面的包，因此各个机器也需要再安装一遍daemons

``` bash
yum install cloudera-manager-daemons
yum install cloudera-manager-agent
service cloudera-scm-agent start
```

* 安装Hadoop集群——部署Zookeeper, Kafka, HDFS, HBase

登录cloudera, http://cloudera:7180，用户名默认为admin, 密码默认为admin

点击添加群集，输入hostname列表，cloudera manager会自动侦测到cloudera agent
这里需要准备下id_rsa，在某一台机器运行ssh-keygen，一路回车，之后将id_rsa保存下来，
在cloudera的向导中，选择无密码登陆，然后将id_rsa上传


* 其他安装可以参考cloudera manager使用手册，这里注明下需要注意的点：

1. 如果客户端使用vpn连接到zookeeper，需要调整zookeeper的最大客户端连接数（maxClientCnxns）
2. 调整kafka的Java Heap Size of Broker in Megabytes（broker_max_heap_size）
3. 调整kafka的Default Number of Partitions（num.partitions）
4. 调整kafka的Replication Factor（default.replication.factor）
5. 修改kafka的Advertised Host（advertised.host.name），注意，这里最好填写可解析的域名
6. 与Zookeeper一样，修改kafka的Maximum Connections per IP Address（max.connections.per.ip）