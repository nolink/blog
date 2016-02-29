title: 在Centos上搭建Elastic Search集群
date: 2016-02-26 17:18:05
tags: [es,elastic-search]
---

* 下载安装包

地址 https://www.elastic.co/downloads/elasticsearch

``` bash
wget https://download.elasticsearch.org/elasticsearch/release/org/elasticsearch/distribution/rpm/elasticsearch/2.2.0/elasticsearch-2.2.0.rpm
```

* 安装es

``` bash
yum -y install elasticsearch-2.2.0.rpm
```

* 如果无法找到java命令，可以修改es的配置，/etc/sysconfig/elasticsearch

配置java路径，并将ES集群的堆内存调整为4GB，默认为256MB 

``` bash
export JAVA_HOME=/usr/java/jdk1.7.0_67-cloudera/
export PATH=${PATH}:${JAVA_HOME}/bin
ES_HEAP_SIZE=4g
```

* 修改es集群的配置, /etc/elasticsearch/elasticsearch.yml

``` bash
cluster.name: clog
path.data: /opt/es
discovery.zen.ping.unicast.hosts: ["xxx:9300","xxx:9300","xxx:9300"]
threadpool.bulk.size: 4
threadpool.bulk.queue_size: 500
```

* 安装Head插件

``` bash
/usr/share/elasticsearch/bin/plugin --install mobz/elasticsearch-head
```

* 启动es服务

``` bash
mkdir /opt/es
chown elasticsearch:elasticsearch /opt/es
service elasticsearch start
```

* 使用head插件直接查看数据

http://elasticsearch:9200/_plugin/head