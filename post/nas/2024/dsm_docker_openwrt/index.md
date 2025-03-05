# Dsm_docker_openwrt


### 前言
> 最近不想折腾贝壳云了 想着把openwrt的Pa*w**l改到群晖docker 上去

### 1.设置docker网卡

```shell 
#获取root权限
sudo -i
#设置混杂模式
ifconfig eth0 promisc 
# 禁用混杂模式
ifconfig eth0 \-promisc
```
#### 创建 macvlan 网络
```shell
docker network create -d macvlan \
   --subnet=192.168.2.0/24 \
   --gateway=192.168.2.1 \
   -o parent=eth0 \
   macnet
```
> 查看加入
```shell
docker network ls
```
#### 生成配置
```shell
# 新建文件夹 openwrt 和 子目录 
mkdir \-p /volume1/docker/openwrt/data/lock \
# 进入 openwrt 目录 
cd /volume1/docker/openwrt \
# 创建 network.conf 文件 
touch network.conf \
# 编辑 network.conf 文件 
vi network.conf
```
编辑配置
```config
config interface 'loopback'
        option ifname 'lo'
        option proto 'static'
        option ipaddr '127.0.0.1'
        option netmask '255.0.0.0'

config globals 'globals'
        option packet_steering '1'

config interface 'lan'
        option ifname 'eth0'
        option proto 'static'
        option netmask '255.255.255.0'
        option ip6assign '60'
        option ipaddr '192.168.2.3'
        option gateway '192.168.2.1'
        option dns '8.8.8.8 114.114.114.114'

```

# 启动容器
docker run -d \
   --restart always \
   --name openwrt \
   --network macnet \
   --privileged \
   --ip 192.168.2.3 \
   -v $(pwd)/network.conf:/etc/config/network \
   -v $(pwd)/data:/var \
   kiddin9_openwrt /sbin/init




