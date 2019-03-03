---
title: さくらVPS上のUbuntuにLXCを導入して、vpnに繋げるまで
date: 2013-10-27T20:51:26+09:00
draft: false
tags: [ネットワーク, インフラ]
aliases:
    - /archives/73
---

VritualBoxなどの仮想マシンになれていた自分にとって、LXCはハードルが高かったのだが、三度目の挑戦ぐらいで使い方が分かったので、記録しておく。

### LXCのインストール
~~~{.sh}
sudo apt-get install debootstrap lxc lxctl
~~~
### コンテナの作成
~~~{.sh}
sudo lxc-create -t ubuntu -n develop
~~~

### ネットワークの設定
[SoftEther VPNをraspberry piにインストールした ](http://blog.gepuro.net/archives/72)のようにして、SoftEther VPNを導入しておく。
#### /etc/network/interfaces
~~~{.text}
auto vpn_se_vpn
iface vpn_se_vpn inet static
        address 192.168.0.100
        netmask 255.255.255.0
        network 192.168.0.0
        broadcast 192.168.11.255
        gateway 192.168.0.1
        dns-nameserver 192.168.0.1

auto br0
iface br0 inet static
        address 192.168.0.100
        netmask 255.255.255.0
        network 192.168.0.0
        broadcast 192.168.0.255
        gateway 192.168.0.1
        dns-nameserver 192.168.0.1
        bridge_ports vpn_se_vpn
        bridge_stp off
        bridge_maxwait 3
~~~
として設定が終わったら、vpn clientを起動する。

### コンテナのネットワーク設定
~~~{.text}
lxc.network.type=veth
lxc.network.link=br0
lxc.network.flags=up
lxc.network.name=eth0
lxc.network.ipv4=192.168.0.0/24
lxc.network.hwaddr = 00:16:3e:d8:9f:35
lxc.utsname = develop
~~~
とする。

### コンテナの操作
#### コンテナの起動
~~~{.sh}
sudo lxc-start -n develop
~~~
#### コンテナの終了
~~~{.sh}
sudo lxc-stop -n develop
~~~
#### コンテナの複製
~~~{.sh}
sudo lxc-clone -o develop -n my_template
~~~

これで、VPS上で気楽に開発を行えるようになった。コンテナ作成直後のユーザ名、パスワードはubuntuとなっているので、新しく作成したユーザに管理者権限を与えた後に削除しておく。作業途中で様子がおかしくなった場合は、vpn clientを再起動すると上手くいった。作業手順を正しくすれば、このような手間は無かったかもしれない。

