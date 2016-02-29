title: 在Centos上搭建ETCD集群
date: 2016-02-26 13:52:32
tags: etcd
---

* 首先，下载可执行包

``` bash
wget https://github.com/coreos/etcd/releases/download/v2.2.2/etcd-v2.2.2-linux-amd64.tar.gz
```

* 解压
``` bash
tar -xzf etcd-v2.2.2-linux-amd64.tar.gz
```
* 使etcd命令可直接执行
``` bash
cp etcd-v2.2.2-linux-amd64/etcd /usr/bin
```

* 编辑启动命令，每个etcd节点都相同
``` bash
vi /etc/init/etcd.conf
```

内容如下

``` bash
description "etcd 2.2 distributed key-value store"

start on (net-device-up
          and local-filesystems
          and runlevel [2345])
stop on runlevel [016]

respawn
respawn limit 10 5

script
  if [ -f "/etc/default/etcd" ]; then
    . /etc/default/etcd
  fi

exec /usr/bin/etcd >>/opt/log/etcd.log 2>&1
end script
```

* 编辑启动参数，每个etcd节点都不同

第二个节点的内容如下，其他节点内容依次类推
``` bash
env ETCD_INITIAL_CLUSTER="etcd-01=http://etcd-01:2380,etcd-02=http://etcd-02:2380,etcd-03=http://etcd-03:2380"
env ETCD_INITIAL_CLUSTER_STATE="new"
env ETCD_INITIAL_CLUSTER_TOKEN="etcd-cluster-01"
env ETCD_INITIAL_ADVERTISE_PEER_URLS="http://etcd-02:2380"
env ETCD_DATA_DIR="/opt/etcd"
env ETCD_LISTEN_PEER_URLS="http://etcd-02:2380"
env ETCD_LISTEN_CLIENT_URLS="http://etcd-02:2379"
env ETCD_ADVERTISE_CLIENT_URLS="http://etcd-02:2379"
env ETCD_NAME="etcd-02"
```

* 启动etcd实例
``` bash
initctl start etcd
```

* 停止etcd实例
``` bash
initctl stop etcd
```
