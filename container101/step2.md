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
httpd              latest    dabbfbe0c57b   6 days ago     144MB
redis              latest    b8477f2e393b   2 months ago   113MB
mongo              latest    c1a14d3979c5   2 months ago   691MB
mariadb            10        b7220a722ce2   2 months ago   409MB
mariadb            latest    b7220a722ce2   2 months ago   409MB
ubuntu             latest    597ce1600cf4   2 months ago   72.8MB
postgres           12        fe603fe275ba   2 months ago   371MB
postgres           latest    6ce504119cc8   2 months ago   374MB
mysql              8         2fe463762680   2 months ago   514MB
mysql              latest    2fe463762680   2 months ago   514MB
hello-world        latest    feb5d9fea6a5   3 months ago   13.3kB
alpine             latest    14119a10abf4   4 months ago   5.59MB
weaveworks/scope   1.11.4    a082d48f0b39   2 years ago    78.5MB
```

`docker search`コマンドを使って、centosという文字列を含むコンテナイメージを検索します。

`docker search centos`{{execute}}
```text
$ docker search centos
NAME                              DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
centos                            The official build of CentOS.                   6947      [OK]       
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
a1d0c7532777: Downloading [==================================================>]  83.52MB/83.52MB
latest: Pulling from library/centos
a1d0c7532777: Pull complete 
Digest: sha256:a27fd8080b517143cbbbab9dfb7c8571c40d67d534bbdee55bd6c473f432b177
Status: Downloaded newer image for centos:latest
docker.io/library/centos:lates
```

再度、コンテナイメージ一覧を確認すると、下から3行目、CentOSの最新版コンテナイメージcentosが自ホストのイメージ格納領域にダウンロードされたことがわかります。

`docker images`{{execute}}

```text
docker.io/library/centos:latest
$ docker images
REPOSITORY         TAG       IMAGE ID       CREATED        SIZE
httpd              latest    dabbfbe0c57b   6 days ago     144MB
redis              latest    b8477f2e393b   2 months ago   113MB
mongo              latest    c1a14d3979c5   2 months ago   691MB
mariadb            10        b7220a722ce2   2 months ago   409MB
mariadb            latest    b7220a722ce2   2 months ago   409MB
ubuntu             latest    597ce1600cf4   2 months ago   72.8MB
postgres           12        fe603fe275ba   2 months ago   371MB
postgres           latest    6ce504119cc8   2 months ago   374MB
mysql              8         2fe463762680   2 months ago   514MB
mysql              latest    2fe463762680   2 months ago   514MB
hello-world        latest    feb5d9fea6a5   3 months ago   13.3kB
centos             latest    5d0da3dc9764   3 months ago   231MB
alpine             latest    14119a10abf4   4 months ago   5.59MB
weaveworks/scope   1.11.4    a082d48f0b39   2 years ago    78.5MB
```

---
**インタラクティブな状態のコンテナの起動**

それでは、このcentosイメージからコンテナを起動します。コンテナを起動する際に`--nameオプション`を付けるとコンテナに任意の名前をつけることができます。今回はmycentos01という名前を付けることにします。また、標準入力を受け付ける`-i（or --interactive）オプション`と疑似TTYの割当を行う`-t（or --tty）オプション`を組み合わせてbashをインタラクティブモードで起動すると、コンテナ内での操作が可能になります。

![Test Image 1](https://raw.githubusercontent.com/mayumi00/katacoda-scenarios/main/container101/images/image2-1.png)

`docker run -it --name mycentos01 centos /bin/bash`{{execute}}

```text
[root@7b1d77ed91f8 /]#
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
[root@7b1d77ed91f8 /]# cat /etc/os-release
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
[root@7b1d77ed91f8 /]# uname -a
Linux 7b1d77ed91f8 5.4.0-88-generic #99-Ubuntu SMP Thu Sep 23 17:29:00 UTC 2021 x86_64 x86_64 x86_64 GNU/Linux
```

`cat /etc/hostname`{{execute}}

ホスト名です。ホスト名を指定していないので、コンテナIDになっています。

```text
[root@7b1d77ed91f8 /]# cat /etc/hostname
7b1d77ed91f8
```
コンテナ内でのプロセスの状態を確認します。

`ps f -e`{{execute}}

```text
[root@7b1d77ed91f8 /]# ps f -e
    PID TTY      STAT   TIME COMMAND
      1 pts/0    Ss     0:00 /bin/bash
     18 pts/0    R+     0:00 ps f -e
```

linuxのコマンドが使えることや、ファイルの内容を確認したところで、exitコマンドでコンテナから抜けます。

`exit`{{execute}}

```text
[root@7b1d77ed91f8 /]# exit
exit
```

---
**docker execコマンド**

コンテナを抜けた後、再度コンテナ一覧を取得します。

`docker ps -a`{{execute}}

```text
$ docker ps -a
CONTAINER ID   IMAGE          COMMAND              CREATED          STATUS                     PORTS                               NAMES
7b1d77ed91f8   centos         "/bin/bash"          32 seconds ago   Exited (0) 7 seconds ago                                       mycentos01
694ebd43af1d   httpd:latest   "httpd-foreground"   4 minutes ago    Up 4 minutes               0.0.0.0:80->80/tcp, :::80->80/tcp   httpd
e14e3cbf21be   hello-world    "/hello"             5 minutes ago    Exited (0) 5 minutes ago                                       musing_shamir
```

mycentos01は「Exited」と停止状態です。`docker run`で起動したコンテナのターミナルをexitで抜けると、コンテナが停止します。

再度、コンテナを起動します。

`docker start mycentos01`{{execute}}

起動後に、コンテナの起動状況を確認するとmycentos01のSTATUSはUpになっていることがわかります。

`docker ps -a`{{execute}}
```text
$ docker ps -a
CONTAINER ID   IMAGE          COMMAND              CREATED              STATUS                     PORTS                               NAMES
7b1d77ed91f8   centos         "/bin/bash"          About a minute ago   Up 2 seconds                                                   mycentos01
694ebd43af1d   httpd:latest   "httpd-foreground"   4 minutes ago        Up 4 minutes               0.0.0.0:80->80/tcp, :::80->80/tcp   httpd
e14e3cbf21be   hello-world    "/hello"             6 minutes ago        Exited (0) 6 minutes ago 
```

![Test Image 1](https://raw.githubusercontent.com/mayumi00/katacoda-scenarios/main/container101/images/image2-2.png)

起動はしましたが、標準入力を受け付けるオプションを指定しなかったので操作できない状態になってしまいました。そこで、実行中のコンテナ内において新たなコマンドを実行する`docker exec`コマンドを利用してbashの利用を可能にします。

`docker exec -it mycentos01 /bin/bash`{{execute}}
```text
$ docker exec -it mycentos01 /bin/bash
[root@7b1d77ed91f8 /]# ls
```

---
**コンテナ内でのファイル作成と停止・起動による影響**

再びbashが利用できる状態になったので、いくつかのコマンドを実行してみましょう。

***実施項目***
- 現在存在するファイル/ディレクトリの表示
- テキストファイルを作成
- ファイルが作られたことを確認

`ls`{{execute}}

現在コンテナ内に存在するファイル/ディレクトリ一覧を表示します

```text
[root@7b1d77ed91f8 /]# ls
bin  dev  etc  home  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  va
```
echoコマンドを使って、ファイルを作成します。

`echo ContainerExercise > testfile.txt`{{execute}}

`ls`{{execute}}

ファイルが作成されているか確認します。

```text
[root@7b1d77ed91f8 /]# ls
bin  etc   lib    lost+found  mnt  proc  run   srv  testfile.txt  usr
dev  home  lib64  media       opt  root  sbin  sys  tmp           var

```
testfile.txtが作成されていることが確認できたので、testfile.txtの内容を確認します。
 
`cat testfile.txt`{{execute}}

```text
[root@7b1d77ed91f8 /]# cat testfile.txt
ContainerExercise
```

`exit`{{execute}}

一通り確認したところで、先程同様exitでコンテナのターミナルから抜け、コンテナ一覧を確認します。

`docker ps -a`{{execute}}
```text
$ docker ps -a
CONTAINER ID   IMAGE          COMMAND              CREATED              STATUS                     PORTS                               NAMES
7b1d77ed91f8   centos         "/bin/bash"          About a minute ago   Up 37 seconds                                                  mycentos01
694ebd43af1d   httpd:latest   "httpd-foreground"   5 minutes ago        Up 5 minutes               0.0.0.0:80->80/tcp, :::80->80/tcp   httpd
e14e3cbf21be   hello-world    "/hello"             6 minutes ago        Exited (0) 6 minutes ago                                       musing_shamir
```

コンテナ一覧を確認すると、先程の`docker run`の場合と異なりコンテナは停止せず、起動したままです。続いてdockerコマンドで明示的にコンテナを停止します。

`docker stop mycentos01`{{execute}}

`docker ps -a`{{execute}}

停止したことが確認できます。

```text
$ docker ps -a
CONTAINER ID   IMAGE          COMMAND              CREATED              STATUS                     PORTS                               NAMES
7b1d77ed91f8   centos         "/bin/bash"          About a minute ago   Exited (0) 2 seconds ago                                       mycentos01
694ebd43af1d   httpd:latest   "httpd-foreground"   5 minutes ago        Up 5 minutes               0.0.0.0:80->80/tcp, :::80->80/tcp   httpd
e14e3cbf21be   hello-world    "/hello"             7 minutes ago        Exited (0) 7 minutes ago                                       musing_shamir
```
コンテナをまた起動して、状態を確認します。

`docker start mycentos01`{{execute}}

`docker ps -a`{{execute}}

```text
$ docker ps -a
CONTAINER ID   IMAGE          COMMAND              CREATED         STATUS                     PORTS                               NAMES
7b1d77ed91f8   centos         "/bin/bash"          2 minutes ago   Up 2 seconds                                                   mycentos01
694ebd43af1d   httpd:latest   "httpd-foreground"   5 minutes ago   Up 5 minutes               0.0.0.0:80->80/tcp, :::80->80/tcp   httpd
e14e3cbf21be   hello-world    "/hello"             7 minutes ago   Exited (0) 7 minutes ago                                       musing_shamir
```

mycentos01が起動したので`docker exec`コマンドを利用してbashの利用を可能にします。

`docker exec -it mycentos01 /bin/bash`{{execute}}
```text
[root@7b1d77ed91f8 /]#
```
ここで、先程作成したファイル`testfile.txt`が、コンテナ停止の影響を受けてるか確認します。

`ls`{{execute}}
```text
[root@7b1d77ed91f8 /]# ls
bin  etc   lib    lost+found  mnt  proc  run   srv  testfile.txt  usr
dev  home  lib64  media       opt  root  sbin  sys  tmp           var
```
`cat testfile.txt`{{execute}}
```text
[root@7b1d77ed91f8 /]# cat testfile.txt
ContainerExercise
```
先程作ったファイルはそのまま存在してます。コンテナの停止によって影響は受けないことはわかりました。

`exit`{{execute}}
```text
exit
```

コンテナの起動や停止・開始の一連の操作でコンテナの状態がどのように推移するかなんとなく理解できたかと思います。とはいえ、まだCentOSコンテナの操作をちょっと行ってみただけなので、次のステップではCentOSコンテナにアプリケーションをインストールして、アプリの動作を確認してみましょう。

---

###  このステップで利用したdockerコマンド

- docker pull [OPTIONS] NAME[:TAG|@DIGEST]
   - レジストリからイメージまたはリポジトリを取得する
- docker exec [OPTIONS] CONTAINER COMMAND [ARG...]
   - 実行中のコンテナ内において新たなコマンドを実行するる
