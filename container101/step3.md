# コンテナへのアプリケーションの導入

![Test Image 1](https://raw.githubusercontent.com/mayumi00/katacoda-scenarios/main/container101/images/image01.png)　

実施すること
- CentOSコンテナを起動する
- centosにhttpdをdnf installする
- テスト用のhtmlをhttpdのドキュメントルート配下に配置する
- ローカルホストにアクセスしてhttpdの動作を確認する

ベースとなるCentOSはステップ２と同じイメージを利用し、ステップ２で生成したコンテナとは異なるコンテナを生成します。ここではコンテナ名として`mycentos2`としています。アプリをインストールしたいので、bashを利用してコンテナの操作を行えるようにします。

`docker run -d  -p 80:80 --name mycentos2 centos`{{execute}}

```text
[root@0893cd3e1c07 /]#
```

`docker exec  -it  mycentos2  /bin/bash`{{execute}}

```text
[root@0893cd3e1c07 /]#
```

`dnf install -y httpd{{execute}}`

```text
[root@b1e6aae8bde5 /]# dnf install -y httpd
Failed to set locale, defaulting to C.UTF-8
CentOS Linux 8 - AppStream                      5.1 MB/s | 8.2 MB     00:01
CentOS Linux 8 - BaseOS                         2.0 MB/s | 3.5 MB     00:01
CentOS Linux 8 - Extras                          20 kB/s |  10 kB     00:00
Dependencies resolved.
================================================================================
 Package          Arch   Version                                Repo       Size
================================================================================
Installing:
 httpd            x86_64 2.4.37-43.module_el8.5.0+1022+b541f3b1 appstream 1.4 M
Installing dependencies:
 apr              x86_64 1.6.3-12.el8                           appstream 129 k
 apr-util         x86_64 1.6.1-6.el8                            appstream 105 k
 brotli           x86_64 1.0.6-3.el8                            baseos    323 k
 centos-logos-httpd
                  noarch 85.8-2.el8                             baseos     75 k
 httpd-filesystem noarch 2.4.37-43.module_el8.5.0+1022+b541f3b1 appstream  39 k
 httpd-tools      x86_64 2.4.37-43.module_el8.5.0+1022+b541f3b1 appstream 107 k
 mailcap          noarch 2.1.48-3.el8                           baseos     39 k
 mod_http2        x86_64 1.15.7-3.module_el8.4.0+778+c970deab   appstream 154 k
Installing weak dependencies:
 apr-util-bdb     x86_64 1.6.1-6.el8                            appstream  25 k
 apr-util-openssl x86_64 1.6.1-6.el8                            appstream  27 k
Enabling module streams:
 httpd                   2.4

Transaction Summary
================================================================================
Install  11 Packages

Total download size: 2.4 M
Installed size: 7.1 M
Downloading Packages:
(1/11): apr-util-bdb-1.6.1-6.el8.x86_64.rpm     244 kB/s |  25 kB     00:00
(2/11): apr-util-openssl-1.6.1-6.el8.x86_64.rpm 523 kB/s |  27 kB     00:00
(3/11): apr-util-1.6.1-6.el8.x86_64.rpm         677 kB/s | 105 kB     00:00
(4/11): apr-1.6.3-12.el8.x86_64.rpm             729 kB/s | 129 kB     00:00
(5/11): httpd-filesystem-2.4.37-43.module_el8.5 1.3 MB/s |  39 kB     00:00
(6/11): httpd-tools-2.4.37-43.module_el8.5.0+10 766 kB/s | 107 kB     00:00
(7/11): mod_http2-1.15.7-3.module_el8.4.0+778+c 929 kB/s | 154 kB     00:00
(8/11): centos-logos-httpd-85.8-2.el8.noarch.rp 2.1 MB/s |  75 kB     00:00
(9/11): mailcap-2.1.48-3.el8.noarch.rpm         381 kB/s |  39 kB     00:00
(10/11): brotli-1.0.6-3.el8.x86_64.rpm          1.6 MB/s | 323 kB     00:00
(11/11): httpd-2.4.37-43.module_el8.5.0+1022+b5 3.8 MB/s | 1.4 MB     00:00
--------------------------------------------------------------------------------
Total                                           1.9 MB/s | 2.4 MB     00:01
warning: /var/cache/dnf/appstream-02e86d1c976ab532/packages/apr-1.6.3-12.el8.x86_64.rpm: Header V3 RSA/SHA256 Signature, key ID 8483c65d: NOKEY
CentOS Linux 8 - AppStream                      1.6 MB/s | 1.6 kB     00:00
Importing GPG key 0x8483C65D:
 Userid     : "CentOS (CentOS Official Signing Key) <security@centos.org>"
 Fingerprint: 99DB 70FA E1D7 CE22 7FB6 4882 05B5 55B3 8483 C65D
 From       : /etc/pki/rpm-gpg/RPM-GPG-KEY-centosofficial
Key imported successfully
Running transaction check
Transaction check succeeded.
Running transaction test
Transaction test succeeded.
Running transaction
  Preparing        :                                                        1/1
  Installing       : apr-1.6.3-12.el8.x86_64                               1/11
  Running scriptlet: apr-1.6.3-12.el8.x86_64                               1/11
  Installing       : apr-util-bdb-1.6.1-6.el8.x86_64                       2/11
  Installing       : apr-util-openssl-1.6.1-6.el8.x86_64                   3/11
  Installing       : apr-util-1.6.1-6.el8.x86_64                           4/11
  Running scriptlet: apr-util-1.6.1-6.el8.x86_64                           4/11
  Installing       : httpd-tools-2.4.37-43.module_el8.5.0+1022+b541f3b1    5/11
  Installing       : mailcap-2.1.48-3.el8.noarch                           6/11
  Installing       : centos-logos-httpd-85.8-2.el8.noarch                  7/11
  Installing       : brotli-1.0.6-3.el8.x86_64                             8/11
  Running scriptlet: httpd-filesystem-2.4.37-43.module_el8.5.0+1022+b54    9/11
  Installing       : httpd-filesystem-2.4.37-43.module_el8.5.0+1022+b54    9/11
  Installing       : mod_http2-1.15.7-3.module_el8.4.0+778+c970deab.x86   10/11
  Installing       : httpd-2.4.37-43.module_el8.5.0+1022+b541f3b1.x86_6   11/11
  Running scriptlet: httpd-2.4.37-43.module_el8.5.0+1022+b541f3b1.x86_6   11/11
  Verifying        : apr-1.6.3-12.el8.x86_64                               1/11
  Verifying        : apr-util-1.6.1-6.el8.x86_64                           2/11
  Verifying        : apr-util-bdb-1.6.1-6.el8.x86_64                       3/11
  Verifying        : apr-util-openssl-1.6.1-6.el8.x86_64                   4/11
  Verifying        : httpd-2.4.37-43.module_el8.5.0+1022+b541f3b1.x86_6    5/11
  Verifying        : httpd-filesystem-2.4.37-43.module_el8.5.0+1022+b54    6/11
  Verifying        : httpd-tools-2.4.37-43.module_el8.5.0+1022+b541f3b1    7/11
  Verifying        : mod_http2-1.15.7-3.module_el8.4.0+778+c970deab.x86    8/11
  Verifying        : brotli-1.0.6-3.el8.x86_64                             9/11
  Verifying        : centos-logos-httpd-85.8-2.el8.noarch                 10/11
  Verifying        : mailcap-2.1.48-3.el8.noarch                          11/11

Installed:
  apr-1.6.3-12.el8.x86_64
  apr-util-1.6.1-6.el8.x86_64
  apr-util-bdb-1.6.1-6.el8.x86_64
  apr-util-openssl-1.6.1-6.el8.x86_64
  brotli-1.0.6-3.el8.x86_64
  centos-logos-httpd-85.8-2.el8.noarch
  httpd-2.4.37-43.module_el8.5.0+1022+b541f3b1.x86_64
  httpd-filesystem-2.4.37-43.module_el8.5.0+1022+b541f3b1.noarch
  httpd-tools-2.4.37-43.module_el8.5.0+1022+b541f3b1.x86_64
  mailcap-2.1.48-3.el8.noarch
  mod_http2-1.15.7-3.module_el8.4.0+778+c970deab.x86_64

Complete!
[root@b1e6aae8bde5 /]#
```

http.confのServerNameのコメントをはずしlocalhostに書き換える
`sed -i -e "s/#ServerName www.example.com/ServerName localhost/" /etc/httpd/conf/httpd.conf `{{execute}}

```text
$ docker rm 9e2667865bd6
9e2667865bd6
 ```
 
ドキュメントルートにコンテンツを配置する
`echo "<head><title>Apache on Docker Container</title></head><body>Apache on Docker Container</body>" >> /var/www/html/index.html `{{execute}}

```text
$ docker rm 9e2667865bd6
9e2667865bd6
```

httpdを起動する

`/usr/sbin/httpd`{{execute}}

```text
CONTAINER ID   IMAGE         COMMAND    CREATED         STATUS                     PORTS     NAMES
655c23e742bf   hello-world   "/hello"   8 minutes ago   Exited (0) 8 minutes ago             elegant_yonath
 ```
 
 `curl http://localhost/index.html`{{execute}}

```text
CONTAINER ID   IMAGE         COMMAND    CREATED         STATUS                     PORTS     NAMES
655c23e742bf   hello-world   "/hello"   8 minutes ago   Exited (0) 8 minutes ago             elegant_yonath
 ```

 ` ps -ef`{{execute}}

```text
CONTAINER ID   IMAGE         COMMAND    CREATED         STATUS                     PORTS     NAMES
655c23e742bf   hello-world   "/hello"   8 minutes ago   Exited (0) 8 minutes ago             elegant_yonath
 ```

[[HOST_IP]]

https://[[HOST_SUBDOMAIN]]-80-[[KATACODA_HOST]].environments.katacoda.com/