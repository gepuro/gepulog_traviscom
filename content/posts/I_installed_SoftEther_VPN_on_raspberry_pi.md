---
title: SoftEther VPNをraspberry piにインストールした
date: 2013-10-26T16:46:26+09:00
draft: false
tags: [ネットワーク]
aliases:
    - /archives/72
---

[SoftEther VPN プロジェクト](https://ja.softether.org/)で開発されているVPNをraspberyy piにインストールしました。クライアントは、windows7とubuntu 12.04のserver版で、動作確認しました。

### raspberyy pi での作業
~~~{.sh}
wget http://www.softether-download.com/files/softether/v2.00-9387-rtm-2013.09.16-tree/Linux/SoftEther%20VPN%20Server/32bit%20-%20ARM%20EABI/softether-vpnserver-v2.00-9387-rtm-2013.09.16-linux-arm_eabi-32bit.tar.gz
tar zxvf softether-vpnserver-v2.00-9387-rtm-2013.09.16-linux-arm_eabi-32bit.tar.gz
cd vpnclient
make
~~~
でコンパイルが完了する。
あとは、
~~~{.sh}
sudo ./vpnserver start
~~~
でvpnが起動する。

### vpnの設定をする
これよりwindowsで作業をする。
[SoftEther VPN Server and VPN Bridge (Ver 2.00, Build 9387, rtm)](http://www.softether-download.com/files/softether/v2.00-9387-rtm-2013.09.16-tree/Windows/SoftEther%20VPN%20Server%20and%20VPN%20Bridge/softether-vpnserver_vpnbridge-v2.00-9387-rtm-2013.09.16-windows-x86_x64-intel.exe)よりGUIで操作可能なサーバー管理マネージャをインストールする。

ここでの作業メモが残っていないが、画面の指示に従えっていけば、設定が完了した。

### クライアントのインストール(windows)
[SoftEther VPN Client (Ver 2.00, Build 9387, rtm)](http://www.softether-download.com/iles/softether/v2.00-9387-rtm-2013.09.16-tree/Windows/SoftEther%20VPN%20Client/softether-vpnclient-v2.00-9387-rtm-2013.09.16-windows-x86_x64-intel.exe)よりwindows用のクライアントをダウンロードして、インストールすれば良い。ポート番号は8888にしたら、上手く動いた。

### クライアントのインストール(ubuntu server)
[SoftEther VPN Client (Ver 2.00, Build 9387, rtm)](http://www.softether-download.com/files/softether/v2.00-9387-rtm-2013.09.16-tree/Linux/SoftEther%20VPN%20Client/64bit%20-%20Intel%20x64%20or%20AMD64/softether-vpnclient-v2.00-9387-rtm-2013.09.16-linux-x64-64bit.tar.gz)よりlinuxで64bit用のvpnクライアントをダウンロードした。

解凍して、makeすれば、<code>vpnclient</code>と<code>vpncmd</code>が現れる。
まず、
~~~{.sh}
sudo ./vpnclient start
~~~
を実行する。どうやら、管理者権限の必要があるみたい。
~~~{.sh}
./vpncmd
~~~
でvpnの設定を行う.

vpnのコンソール画面に移動したら、
~~~{.sh}
NicCreate
~~~
でNicを作成したのち、
~~~{.sh}
NicEnable
~~~
で有効にする。次に、
~~~{.sh}
AccountCreate
AccountPasswordSet
~~~
でwindowsでの作業で作成したアカウントと同じものを作る。（作業後のまとめて書いているので、足りないのがあるかもしれない）
~~~{.sh}
AccountList
~~~
でアカウントの一覧を確認することが出来る。また、
~~~{.sh}
AccountExport
AccountImport
~~~
で作成したアカウントの保存、読み込みできるので、残しておくと別のコンピュータで利用するときに便利。

~~~{.sh}
AccountConnect
AccountDisconnect
~~~
でvpnへの接続、遮断を行う。

~~~{.sh}
AccountList
AcountStatusShow
~~~
で接続状況を確認できる。

最後に、<code>/etc/network/interfaces</code>の設定を行う
~~~{.sh}
sudo apt-get install resolvconf
~~~
をインストールしておく。（標準で入っているのかな？）

~~~{.text}
auto Nic名
iface Nic名 inet dhcp
~~~
でdhcpによって、IPアドレスを取得できるようになる。手動の場合は、
~~~{.text}
auto NIc名
iface NIc名 inet static
        address 192.168.0.100
        netmask 255.255.255.0
        network 192.168.0.0
        broadcast 192.168.0.255
        gateway 192.168.0.1
        dns-nameserver 192.168.0.1
~~~
などのように設定すればよい。

設定を反映するために、
~~~{.sh}
sudo service networking restart
~~~
を実行すると、VPNで接続できるようになる。
~~~{.sh}
ifconfig
~~~
などを実行して、IPアドレスを取得できていることを確認しておく。


p.s.

Desktop版のubuntuでも、動作を確認したいのだが、最後のネットワークの設定の部分が上手くいっていない。昔、似たような状況に出くわした記憶があるので、単なる知識不足と思われる。


参考:

* [Raspberry Pi + SoftEtherVPN で マイVPN構築](http://helloidea.org/index.php/archives/1171.html)
* [6.5 VPN Client の管理コマンドリファレンス ](http://ja.softether.org/4-docs/1-manual/6/6.5)


