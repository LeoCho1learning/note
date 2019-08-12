# Linux必备软件

## Typora

```shell
# optional, but recommended
sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys BA300B7755AFCFAE
# add Typora's repository
sudo add-apt-repository 'deb https://typora.io/linux ./'
sudo apt-get update
```

## Shadowsocks-Qt5

```shell
sudo add-apt-repository ppa:hzwhuang/ss-qt5
```

对于Ubuntu18.04来说，会出现错误

```shell
E: 仓库 “http://ppa.launchpad.net/hzwhuang/ss-qt5/ubuntu bionic Release” 没有 Release 文件
```

此时需要更改源的文件，因为可能没有对应18.04版本的这个软件，因此要去16.04的那一边去找。

编辑文件/etc/apt/sources.list.d/hzwhuang-ubuntu-ss-qt5-bionic.list，将bionic（18.04版本代号）更改为xenial（16.04版本代号）。

然后执行

```shell
sudo apt-get update
sudo apt-get install shadowsocks-qt5
```



