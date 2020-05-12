[TOC]
# 1 RocketMQ集群的简单部署双主双备
准备两台服务器：192.168.149.11和192.168.149.12
## 1.1 启动namesrv节点
在两台服务器上分别执行以下命令
```
cd /usr/local/rocketmq/bin
nohup sh mqnamesrv &
```
## 1.2 修改broker节点信息
在两台服务器上，修改broker节点信息，进入配置文件目录
```
cd /usr/local/rocketmq/conf/2m-2s-sync
```
### 1.2.1 服务器192.168.149.11上节点配置信息
主节点test-broker-a.properties
```
# 集群中namesrv地址
namesrvAddr=192.168.149.11:9876;192.168.149.12:9876
# 节点集群的名称
brokerClusterName=DefaultCluster
# 节点的名称
brokerName=broker-a
# 0表示主节点，大于0的都是从节点
brokerId=0
# 凌晨4点删除
deleteWhen=04
# 文件在磁盘保留48小时，超时自动删除
fileReservedTime=48
# 节点角色有3种：SYNC MASTER 、ASYNC MASTER 、SLAVE
brokerRole=SYNC_MASTER
# 刷盘策略，分为同步SYNC_FLUSH和异步ASYNC_FLUSH
flushDiskType=ASYNC_FLUSH
# 监听端口，不同broker需要不同端口
listenPort=10911
# 存储消息及一些配置的根目录
storePathRootDir=/home/rocketmq/store-a
```
从节点test-broker-a-slave.properties
```
namesrvAddr=192.168.149.11:9876;192.168.149.12:9876
brokerClusterName=DefaultCluster
brokerName=broker-a
brokerId=1
deleteWhen=04
fileReservedTime=48
brokerRole=SLAVE
flushDiskType=ASYNC_FLUSH
listenPort=11011
storePathRootDir=/home/rocketmq/store-a-slave
```
### 1.2.2 服务器192.168.149.12上节点配置信息
主节点test-broker-b.properties
```
# 集群中namesrv地址
namesrvAddr=192.168.149.11:9876;192.168.149.12:9876
# 节点集群的名称
brokerClusterName=DefaultCluster
# 节点的名称
brokerName=broker-b
# 0表示主节点，大于0的都是从节点
brokerId=0
# 凌晨4点删除
deleteWhen=04
# 文件在磁盘保留48小时，超时自动删除
fileReservedTime=48
# 节点角色有3种：SYNC MASTER 、ASYNC MASTER 、SLAVE
brokerRole=SYNC_MASTER
# 刷盘策略，分为同步SYNC_FLUSH和异步ASYNC_FLUSH
flushDiskType=ASYNC_FLUSH
# 监听端口，不同broker需要不同端口
listenPort=10911
# 存储消息及一些配置的根目录
storePathRootDir=/home/rocketmq/store-b
```
从节点test-broker-b-slave.properties
```
namesrvAddr=192.168.149.11:9876;192.168.149.12:9876
brokerClusterName=DefaultCluster
brokerName=broker-b
brokerId=1
deleteWhen=04
fileReservedTime=48
brokerRole=SLAVE
flushDiskType=ASYNC_FLUSH
listenPort=11011
storePathRootDir=/home/rocketmq/store-b-slave
```
## 1.3 启动broker节点
注：上述的配置文件所存放的目录在`/usr/local/rocketmq/conf/2m-2s-sync`
- 在服务器192.168.149.11上执行以下命令
```
cd /usr/local/rocketmq/bin
sh mqbroker -c ../conf//2m-2s-sync/test-broker-a.properties &
sh mqbroker -c ../conf//2m-2s-sync/test-broker-a-slave.properties &
```
- 在服务器192.168.149.12上执行以下命令
```
cd /usr/local/rocketmq/bin
sh mqbroker -c ../conf//2m-2s-sync/test-broker-b.properties &
sh mqbroker -c ../conf//2m-2s-sync/test-broker-b-slave.properties &
```
# 1.4 验证
查看集群信息两种方式
- 在mq控制台上的cluster页查看
- 执行命令`sh mqadmin clusterList localhost:9876`