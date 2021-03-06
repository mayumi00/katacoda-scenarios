## コンテナの操作とコンテナ内の操作

このステップでは、普段利用している仮想マシンのような感覚で触れるコンテナを利用してみましょう。

> Note: 出力例に表示されているコンテナIDやイメージID、起動時間などは使用している環境と状態によって異なります。このテキストとまったく同じにはならないのでご注意ください。

---
**コンテナのpull**

今回はいきなり`docker run`するのではなく、dockerイメージをpullするところから始めます。馴染みの深いCentOSを利用したいと思います。現在、自ホストにあるコンテナイメージを確認するとcentosという文字は見当たりません。

 `docker images`{{execute}}
 
```text
$ docker images
REPOSITORY         TAG       IMAGE ID       CREATED        SIZE
httpd              latest    dabbfbe0c57b   2 weeks ago    144MB
redis              latest    b8477f2e393b   2 months ago   113MB
mongo              latest    c1a14d3979c5   3 months ago   691MB
mariadb            10        b7220a722ce2   3 months ago   409MB
mariadb            latest    b7220a722ce2   3 months ago   409MB
ubuntu             latest    597ce1600cf4   3 months ago   72.8MB
postgres           12        fe603fe275ba   3 months ago   371MB
postgres           latest    6ce504119cc8   3 months ago   374MB
mysql              8         2fe463762680   3 months ago   514MB
mysql              latest    2fe463762680   3 months ago   514MB
hello-world        latest    feb5d9fea6a5   3 months ago   13.3kB
alpine             latest    14119a10abf4   4 months ago   5.59MB
weaveworks/scope   1.11.4    a082d48f0b39   2 years ago    78.5MB
```

`docker search`コマンドを使って、centosという文字列を含むコンテナイメージを検索します。

`docker search centos`{{execute}}

```text
$ docker search centos
NAME                              DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
centos                            The official build of CentOS.                   6959      [OK]       
ansible/centos7-ansible           Ansible on Centos7                              135                  [OK]
consol/centos-xfce-vnc            Centos container with "headless" VNC session…   132                  [OK]
jdeathe/centos-ssh                OpenSSH / Supervisor / EPEL/IUS/SCL Repos - …   121                  [OK]
centos/systemd                    systemd enabled base container.                 105                  [OK]
（略）
```

`centos / The official build of CentOS. `これが公式のコンテナイメージのようなので、このイメージを使うことにして、CentOSの最新版のコンテナイメージをコンテナレジストリからダウンロードします。特にタグを指定しない場合はデフォルトで最新を意味するlatestというタグがついたイメージを取得します。最新版ではなくバージョンを指定してpullしたい場合などは明示的にタグを指定します。
 
`docker pull centos`{{execute}}

```text
$ docker pull centos
Using default tag: latest
latest: Pulling from library/centos
a1d0c7532777: Pull complete 
Digest: sha256:a27fd8080b517143cbbbab9dfb7c8571c40d67d534bbdee55bd6c473f432b177
Status: Downloaded newer image for centos:latest
docker.io/library/centos:latest
```

再度、コンテナイメージ一覧を確認すると、下から3行目、CentOSの最新版コンテナイメージcentosが自ホストのイメージ格納領域にダウンロードされたことがわかります。

`docker images`{{execute}}

```text
$ docker images
REPOSITORY         TAG       IMAGE ID       CREATED        SIZE
httpd              latest    dabbfbe0c57b   2 weeks ago    144MB
redis              latest    b8477f2e393b   2 months ago   113MB
mongo              latest    c1a14d3979c5   3 months ago   691MB
mariadb            10        b7220a722ce2   3 months ago   409MB
mariadb            latest    b7220a722ce2   3 months ago   409MB
ubuntu             latest    597ce1600cf4   3 months ago   72.8MB
postgres           12        fe603fe275ba   3 months ago   371MB
postgres           latest    6ce504119cc8   3 months ago   374MB
mysql              8         2fe463762680   3 months ago   514MB
mysql              latest    2fe463762680   3 months ago   514MB
hello-world        latest    feb5d9fea6a5   3 months ago   13.3kB
centos             latest    5d0da3dc9764   3 months ago   231MB
alpine             latest    14119a10abf4   4 months ago   5.59MB
weaveworks/scope   1.11.4    a082d48f0b39   2 years ago    78.5MB
```

---
**インタラクティブな状態のコンテナの起動**

それでは、このcentosイメージからコンテナを起動します。コンテナを起動する際に`--nameオプション`を付けるとコンテナに任意の名前をつけることができます。今回はmycentos01という名前を付けることにします。また、標準入力を受け付ける`-i（or --interactive）オプション`と疑似TTYの割当を行う`-t（or --tty）オプション`を組み合わせて、コンテナ内にインタラクティブなbashを起動すると、コンテナ内での操作が可能になります。

![Test Image 1](https://raw.githubusercontent.com/mayumi00/katacoda-scenarios/main/container101/images/image2-1.png)

`docker run -it --name mycentos01 centos /bin/bash`{{execute}}

```text
$ docker run -it --name mycentos01 centos /bin/bash
[root@3fcd0427f74b /]# 
```

[root@文字列]#のプロンプトが表示され、bashの利用が可能になっていることがわかります。プロンプトのroot@の後ろの文字列はコンテナのホスト名です。コンテナを起動する際に`-h（or --hostname）オプション`を付けるとコンテナのホスト名を指定することができますが、今回は指定していないので、ホスト名としてコンテナIDが利用されています。

> Note: コンテナIDなので、実行した環境によって異なります

---
**linuxコマンドの実行**

いくつかのファイルの内容の確認やコマンドの実行を行ってみます。

***確認項目***
- /etc/os-releaseの確認
- unameコマンド
- /etc/hostnameの確認

`cat /etc/os-release`{{execute}}

このコンテナは CentOS Linux 8 であることがわかります。
```text
[root@3fcd0427f74b /]# cat /etc/os-release
NAME="CentOS Linux"
VERSION="8"
ID="centos"
ID_LIKE="rhel fedora"
VERSION_ID="8"
PLATFORM_ID="platform:el8"
PRETTY_NAME="CentOS Linux 8"
ANSI_COLOR="0;31"
CPE_NAME="cpe:/o:centos:centos:8"
HOME_URL="https://centos.org/"
BUG_REPORT_URL="https://bugs.centos.org/"
CENTOS_MANTISBT_PROJECT="CentOS-8"
CENTOS_MANTISBT_PROJECT_VERSION="8"
```

`uname -a`{{execute}}

Linuxのカーネルの情報です。

```text
[root@3fcd0427f74b /]# uname -a
Linux 3fcd0427f74b 5.4.0-88-generic #99-Ubuntu SMP Thu Sep 23 17:29:00 UTC 2021 x86_64 x86_64 x86_64 GNU/Linux
```

`cat /etc/hostname`{{execute}}

ホスト名です。ホスト名を指定していないので、コンテナIDになっています。

```text
[root@3fcd0427f74b /]# cat /etc/hostname
3fcd0427f74b
```
コンテナ内でのプロセスの状態を確認します。

`ps f -ef`{{execute}}

```text
[root@3fcd0427f74b /]# ps f -ef
    PID TTY      STAT   TIME COMMAND
      1 pts/0    Ss     0:00 /bin/bash
     18 pts/0    R+     0:00 ps f -ef
```

linuxのコマンドが使えることや、ファイルの内容を確認したところで、exitコマンドでコンテナから抜けます。

`exit`{{execute}}

```text
[root@3fcd0427f74b /]# exit
exit
$ 
```

---
**docker execコマンド**

コンテナを抜けた後、再度コンテナ一覧を取得します。

`docker ps -a`{{execute}}

```text
$ docker ps -a
CONTAINER ID   IMAGE          COMMAND              CREATED          STATUS                     PORTS                               NAMES
3fcd0427f74b   centos         "/bin/bash"          40 seconds ago   Exited (0) 9 seconds ago                                       mycentos01
da36e29f033d   httpd:latest   "httpd-foreground"   4 minutes ago    Up 4 minutes               0.0.0.0:80->80/tcp, :::80->80/tcp   httpd
b157d4669472   hello-world    "/hello"             4 minutes ago    Exited (0) 4 minutes ago                                       trusting_shamir
```

mycentos01は「Exited」と停止状態です。`docker run`で起動したコンテナのターミナルをexitで抜けると、コンテナが停止します。

再度、コンテナを起動します。

`docker start mycentos01`{{execute}}

起動後に、コンテナの起動状況を確認するとmycentos01のSTATUSはUpになっていることがわかります。

`docker ps -a`{{execute}}

```text
$ docker ps -a
CONTAINER ID   IMAGE          COMMAND              CREATED              STATUS                     PORTS                               NAMES
3fcd0427f74b   centos         "/bin/bash"          About a minute ago   Up 5 seconds                                                   mycentos01
da36e29f033d   httpd:latest   "httpd-foreground"   4 minutes ago        Up 4 minutes               0.0.0.0:80->80/tcp, :::80->80/tcp   httpd
b157d4669472   hello-world    "/hello"             5 minutes ago        Exited (0) 5 minutes ago                                       trusting_shamir
```

![Test Image 1](https://raw.githubusercontent.com/mayumi00/katacoda-scenarios/main/container101/images/image2-2.png)

起動はしましたが、標準入力を受け付けるオプションを指定しなかったので操作できない状態になってしまいました。そこで、実行中のコンテナ内において新たなコマンドを実行する`docker exec`コマンドを利用してコンテナ内のbashを利用を可能にします。

`docker exec -it mycentos01 /bin/bash`{{execute}}

```text
$ docker exec -it mycentos01 /bin/bash
[root@3fcd0427f74b /]#
```

---
**コンテナ内でのファイル作成と停止・起動による影響**

再びコンテナ内の操作が可能になったので、いくつかのコマンドを実行してみましょう。

***実施項目***
- 現在存在するファイル/ディレクトリの表示
- ディレクトリとテキストファイルの作成
- ファイルが作られたことの確認

`ls -p`{{execute}}

現在コンテナ内に存在するファイル/ディレクトリ一覧を表示します。

```text
[root@3fcd0427f74b /]# ls -p
bin   etc/   lib    lost+found/  mnt/  proc/  run/  srv/  tmp/  var/
dev/  home/  lib64  media/       opt/  root/  sbin  sys/  usr/
```

newdirという名前の新しいディレクトリを作成します

`mkdir newdir`{{execute}}

echoコマンドを使って、newdirディレクトリの下にmessagefileというテキストファイルを作成します。

`echo "HELLO CONTAINER WORLD" > newdir/messagefile`{{execute}}

`ls -p`{{execute}}

newdirディレクトリが作成されているか確認します。

```text
[root@3fcd0427f74b /]# ls -p
bin   etc/   lib    lost+found/  mnt/     opt/   root/  sbin  sys/  usr/
dev/  home/  lib64  media/       newdir/  proc/  run/   srv/  tmp/  var/
```
newdir/messagefileの内容を確認します。
 
`cat newdir/messagefile`{{execute}}

```text
[root@3fcd0427f74b /]# cat newdir/messagefile
HELLO CONTAINER WORLD
```

`exit`{{execute}}

一通り確認したところで、先程同様exitでコンテナのターミナルから抜け、コンテナ一覧を確認します。

`docker ps -a`{{execute}}

```text
$ docker ps -a
CONTAINER ID   IMAGE          COMMAND              CREATED         STATUS                     PORTS                               NAMES
3fcd0427f74b   centos         "/bin/bash"          2 minutes ago   Up About a minute                                              mycentos01
da36e29f033d   httpd:latest   "httpd-foreground"   5 minutes ago   Up 5 minutes               0.0.0.0:80->80/tcp, :::80->80/tcp   httpd
b157d4669472   hello-world    "/hello"             6 minutes ago   Exited (0) 6 minutes ago                                       trusting_shamir
```

コンテナ一覧を確認すると、先程の`docker run`の場合と異なりコンテナは停止せず、起動したままです。続いてdockerコマンドで明示的にmycentos01コンテナを停止します。

`docker stop mycentos01`{{execute}}

`docker ps -a`{{execute}}

mycentos01が停止したことが確認できます。

```text
$ docker ps -a
CONTAINER ID   IMAGE          COMMAND              CREATED         STATUS                     PORTS                               NAMES
3fcd0427f74b   centos         "/bin/bash"          2 minutes ago   Exited (0) 2 seconds ago                                       mycentos01
da36e29f033d   httpd:latest   "httpd-foreground"   5 minutes ago   Up 5 minutes               0.0.0.0:80->80/tcp, :::80->80/tcp   httpd
b157d4669472   hello-world    "/hello"             6 minutes ago   Exited (0) 6 minutes ago                                       trusting_shamir
```

mycentos01コンテナをまた起動して、状態を確認します。

`docker start mycentos01`{{execute}}

`docker ps -a`{{execute}}

```text
$ docker ps -a
CONTAINER ID   IMAGE          COMMAND              CREATED         STATUS                     PORTS                               NAMES
3fcd0427f74b   centos         "/bin/bash"          2 minutes ago   Up 2 seconds                                                   mycentos01
da36e29f033d   httpd:latest   "httpd-foreground"   6 minutes ago   Up 6 minutes               0.0.0.0:80->80/tcp, :::80->80/tcp   httpd
b157d4669472   hello-world    "/hello"             6 minutes ago   Exited (0) 6 minutes ago                                       trusting_shamir
```

mycentos01が起動したので`docker exec`コマンドを利用してコンテナ内のbashを利用を可能にします。

`docker exec -it mycentos01 /bin/bash`{{execute}}

```text
$ docker exec -it mycentos01 /bin/bash
[root@3fcd0427f74b /]#
```
ここで、先程作成したファイル`newdir/messagefile`が、コンテナ停止の影響を受けてるか確認します。

`ls -p newdir`{{execute}}

```text
[root@3fcd0427f74b /]# ls -p newdir
messagefile
```

`cat newdir/messagefile`{{execute}}

```text
[root@3fcd0427f74b /]# cat newdir/messagefile
HELLO CONTAINER WORLD
```
先程作ったファイルはそのまま存在してます。コンテナの停止によって影響は受けないことはわかりました。


**コンテナの不変性**

現在コンテナ内に存在するファイル/ディレクトリ一覧を表示します。

`ls -p`{{execute}}

```text
[root@3fcd0427f74b /]# ls -p
bin   etc/   lib    lost+found/  mnt/     opt/   root/  sbin  sys/  usr/
dev/  home/  lib64  media/       newdir/  proc/  run/   srv/  tmp/  var/
```
コンテナ内のディレクトリをいくつか削除します。

`rm -rf  media mnt opt var home`{{execute}}

```text
[root@3fcd0427f74b /]# rm -rf  media mnt opt var home
```
削除されていることを確認します。

`ls -p`{{execute}}

```text
[root@3fcd0427f74b /]# ls -p
bin  dev/  etc/  lib  lib64  lost+found/  newdir/  proc/  root/  run/  sbin  srv/  sys/  tmp/  usr/
```

コンテナから抜けます。

`exit`{{execute}}

```text
[root@3fcd0427f74b /]# exit
exit
$ 
```

![Test Image 1](https://raw.githubusercontent.com/mayumi00/katacoda-scenarios/main/container101/images/image2-3.png)


この変更済コンテナを停止→削除して、同じコンテナイメージから新たに同じ名前でコンテナを起動します。

`docker stop mycentos01`{{execute}}

`docker rm mycentos01`{{execute}}

`docker run -it --name mycentos01 centos /bin/bash`{{execute}}

コンテナ内に存在するファイル/ディレクトリ一覧を表示します。

`ls -p`{{execute}}

先程のファイル作成やディレクトリ削除を行う前の状態になっているのがわかります。

```text
[root@7b51274d250c /]# ls -p
bin   etc/   lib    lost+found/  mnt/  proc/  run/  srv/  tmp/  var/
dev/  home/  lib64  media/       opt/  root/  sbin  sys/  usr/
```

コンテナに変更を加えたとしても、同じコンテナイメージから再度作成すれば変更を加えないコンテナを作成することができます。これがコンテナの不変性 (Immutable)という特徴です。

`exit`{{execute}}

確認ができたので、コンテナをexitで抜け（runで起動したのでコンテナは停止する）次のステップのためにmycentos01コンテナを起動しておきます。

`docker start mycentos01`{{execute}}

![Test Image 1](https://raw.githubusercontent.com/mayumi00/katacoda-scenarios/main/container101/images/image2-4.png)

コンテナの起動や停止・開始の一連の操作でコンテナの状態がどのように推移するかなんとなく理解できたかと思います。とはいえ、まだCentOSコンテナの操作をちょっと行ってみただけなので、次のステップではCentOSコンテナにアプリケーションをインストールして、アプリの動作を確認してみましょう。

---

###  このステップで利用したdockerコマンド

- docker pull [OPTIONS] NAME[:TAG|@DIGEST]
   - レジストリからイメージまたはリポジトリを取得する
- docker exec [OPTIONS] CONTAINER COMMAND [ARG...]
   - 実行中のコンテナ内において新たなコマンドを実行するる
