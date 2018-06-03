# bridge-static-cni

> Set static IP with bridge mode

This is a CNI practice. Learning how to write a CNI. Refer to https://github.com/containernetworking/plugins.

## Table of Contents
- [Install](#install)
- [Usage](#usage)

## Install
```sh
$ go get -u github.com/sufuf3/bridge-static-cni
$ go get -u github.com/kardianos/govendor
$ cd $GOPATH/src/github.com/sufuf3/bridge-static-cni
$ govendor sync
$ ./build.sh
```

## Usage
- Presetup
    - Create a new network namespace named ns3
    - change the path under bin, also copy example/example.conf under the directory.
```sh
$ cd $GOPATH/src/github.com/sufuf3/bridge-static-cni/bin
$ sudo ip netns add ns3
$ cp GOPATH/src/github.com/sufuf3/bridge-static-cni/example/example.conf $GOPATH/src/github.com/sufuf3/bridge-static-cni/bin
```

- Using CNI command to add the example config to namespace ns3
```sh
$ sudo CNI_COMMAND=ADD CNI_CONTAINERID=ns3 CNI_NETNS=/var/run/netns/ns3 CNI_IFNAME=eth2 CNI_PATH=`pwd` ./bridge-static <example.conf
{
    "cniVersion": "0.3.1",
    "interfaces": [
        {
            "name": "bs0",
            "mac": "c2:64:d2:24:66:b5"
        },
        {
            "name": "vethfd0b245b",
            "mac": "8a:04:bc:47:a9:56"
        },
        {
            "name": "eth2",
            "mac": "d2:b6:8d:5d:37:00",
            "sandbox": "/var/run/netns/ns3"
        }
    ],
    "ips": [
        {
            "version": "4",
            "interface": 2,
            "address": "10.246.1.2/24",
            "gateway": "10.246.1.1"
        }
    ],
    "routes": [
        {
            "dst": "0.0.0.0/0"
        },
        {
            "dst": "0.0.0.0/0",
            "gw": "10.246.1.1"
        }
    ],
    "dns": {}
```

- Testing
```sh
$ sudo ip netns exec ns3 ip addr
1: lo: <LOOPBACK> mtu 65536 qdisc noop state DOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
3: eth2@if47: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default
    link/ether d2:b6:8d:5d:37:00 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 10.246.1.2/24 brd 10.246.1.255 scope global eth2
       valid_lft forever preferred_lft forever
    inet6 fe80::d0b6:8dff:fe5d:3700/64 scope link
       valid_lft forever preferred_lft forever
```

- clean network namespace
```sh
$ sudo ip netns del ns3
```
