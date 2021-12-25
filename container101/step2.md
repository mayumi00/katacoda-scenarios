
## コンテナの操作とコンテナ内の操作



このステップでは、普段利用している仮想マシンのような感覚で触れるコンテナを利用してみましょう。

> Note: 出力例に表示されているコンテナIDやイメージID、起動時間などは使用している環境と状態によって異なります。このテキストとまったく同じにはならないのでご注意ください。

![Test Image 1](https://raw.githubusercontent.com/mayumi00/katacoda-scenarios/main/container101/images/image03.png)　

今回はいきなり`docker run`するのではなく、dockerイメージをpullするところから始めます。馴染みの深いCentOSを利用します。再度、現在自ホストにあるコンテナイメージを確認します。centosという文字は見当たりません。

 `docker images`{{execute}}
 
```text
REPOSITORY         TAG       IMAGE ID       CREATED        SIZE
redis              latest    b8477f2e393b   2 months ago   113MB
mongo              latest    c1a14d3979c5   2 months ago   691MB
mariadb            10        b7220a722ce2   2 months ago   409MB
mariadb            latest    b7220a722ce2   2 months ago   409MB
ubuntu             latest    597ce1600cf4   2 months ago   72.8MB
postgres           12        fe603fe275ba   2 months ago   371MB
postgres           latest    6ce504119cc8   2 months ago   374MB
mysql              8         2fe463762680   2 months ago   514MB
mysql              latest    2fe463762680   2 months ago   514MB
alpine             latest    14119a10abf4   3 months ago   5.59MB
weaveworks/scope   1.11.4    a082d48f0b39   2 years ago    78.5MB
```

`docker search`コマンドを使って、centosという文字列を含むコンテナイメージを検索します。

`docker search centos`{{execute}}
```text
NAME                              DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
centos                            The official build of CentOS.                   6928      [OK]       
ansible/centos7-ansible           Ansible on Centos7                              135                  [OK]
consol/centos-xfce-vnc            Centos container with "headless" VNC session…   132                  [OK]
jdeathe/centos-ssh                OpenSSH / Supervisor / EPEL/IUS/SCL Repos - …   121                  [OK]
centos/systemd                    systemd enabled base container.                 105                  [OK]
（略）
```

`centos / The official build of CentOS. `これが公式のコンテナイメージのようなので、このイメージを使うことにして、CentOSの最新版のコンテナイメージをコンテナレジストリからダウンロードします。pullする際にlatestのタグを指定してみました。タグを指定しない場合はデフォルトで:latestというタグを用いますので、latestを取得する場合はタグは不要です。どのような場合にタグを利用するのかと言えば、例えば、最新版ではなくバージョンを指定してpullしたい場合などです。
 
`docker pull centos:latest`{{execute}}

```text
latest: Pulling from library/centos
a1d0c7532777: Pull complete 
Digest: sha256:a27fd8080b517143cbbbab9dfb7c8571c40d67d534bbdee55bd6c473f432b177
Status: Downloaded newer image for centos:latest
docker.io/library/centos:latest
```

再度、コンテナイメージを確認すると、CentOSの最新版が自ホストのイメージ格納領域にダウンロードされたことがわかります。

`docker images`{{execute}}

```text
REPOSITORY         TAG       IMAGE ID       CREATED        SIZE
redis              latest    b8477f2e393b   2 months ago   113MB
mongo              latest    c1a14d3979c5   2 months ago   691MB
mariadb            10        b7220a722ce2   2 months ago   409MB
mariadb            latest    b7220a722ce2   2 months ago   409MB
ubuntu             latest    597ce1600cf4   2 months ago   72.8MB
postgres           12        fe603fe275ba   2 months ago   371MB
postgres           latest    6ce504119cc8   2 months ago   374MB
mysql              8         2fe463762680   2 months ago   514MB
mysql              latest    2fe463762680   2 months ago   514MB
hello-world        latest    feb5d9fea6a5   2 months ago   13.3kB
centos             latest    5d0da3dc9764   3 months ago   231MB
alpine             latest    14119a10abf4   3 months ago   5.59MB
weaveworks/scope   1.11.4    a082d48f0b39   2 years ago    78.5MB
```

それでは、このCentOSコンテナイメージからコンテナを起動します。コンテナを起動する際に`--nameオプション`を付けるとコンテナに任意の名前をつけることができます。今回はmycentos1という名前を付けることにします。また、標準入力を受け付ける`-i（or --interactive）オプション`と疑似TTYの割当を行う`-t（or --tty）オプション`を組み合わせてbashをインタラクティブモードで起動すると、コンテナ内での操作が可能になります。

`docker run -it --name mycentos1 centos /bin/bash`{{execute}}

```text
[root@0893cd3e1c07 /]#
```

[root@文字列]#のプロンプトが表示され、bashの利用が可能になっていることがわかります。プロンプトのroot@の後ろの文字列はコンテナホスト名＝コンテナIDなので、実行した環境によって異なります。コンテナを起動する際に`-h（or --hostname）オプション`を付けるとコンテナのホスト名を指定することができますが、今回は指定していないので、ホスト名としてコンテナIDが利用されています。

いくつかのファイルの内容の確認やコマンドの実行を行ってみます。

- /etc/os-releaseの確認
- unameコマンド
- /etc/hostnameの確認

`cat /etc/os-release`{{execute}}

このコンテナは CentOS Linux 8 であることがわかります。
```text
[root@0893cd3e1c07 /]# cat /etc/os-release
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
[root@187c07b01562 /]# uname -a
Linux 187c07b01562 5.4.0-88-generic #99-Ubuntu SMP Thu Sep 23 17:29:00 UTC 2021 x86_64 x86_64 x86_64 GNU/Linux
```

`cat /etc/hostname`{{execute}}

ホスト名です。

```text
[root@0893cd3e1c07 /]# cat /etc/hostname
0893cd3e1c07
```
コンテナ内でのプロセスの状態を確認します。

`ps f -e`{{execute}}

```text
[root@0893cd3e1c07 /]# ps f -e
0893cd3e1c07
```

一通り確認したところで、exitコマンドでコンテナから抜けます。

`exit`{{execute}}

```text
[root@0893cd3e1c07 /]# exit
exit
```
コンテナを抜けた後、再度コンテナ一覧を取得します。

`docker ps -a`{{execute}}
```text
CONTAINER ID   IMAGE     COMMAND       CREATED          STATUS                     PORTS     NAMES
0893cd3e1c07   centos    "/bin/bash"   49 seconds ago   Exited (0) 4 seconds ago             mycentos1
```

`docker run`で起動したコンテナのターミナルをexitで抜けると、コンテナが停止します。再度、コンテナを起動します。起動するコンテナを指定する方法にはコンテナ名またはコンテナIDが利用できます。先程、mycentos1というコンテナ名を付けておいたので、それを指定してコンテナを起動します。

`docker start mycentos1`{{execute}}

起動後に、コンテナの起動状況を確認するとmycentos1のSTATUSはUpになっていることがわかります。

`docker ps -a`{{execute}}
```text
CONTAINER ID   IMAGE     COMMAND       CREATED              STATUS         PORTS     NAMES
0893cd3e1c07   centos    "/bin/bash"   About a minute ago   Up 7 seconds             mycentos1
```

起動はしましたが、標準入力を受け付けるオプションを指定しなかったので操作できない状態になってしまいました。そこで、実行中のコンテナ内において新たなコマンドを実行する`docker exec`execコマンドを利用してbashの利用を可能にします。

`docker exec -it mycentos1 /bin/bash`{{execute}}
```text
[root@0893cd3e1c07 /]# 
```
再びbashが利用できる状態になったので、いくつかのコマンドを実行してみましょう。

- 現在存在するファイル/ディレクトリの表示
- テキストファイルを作成
- ファイルが作られたことを確認

`ls`{{execute}}

現在コンテナ内に存在するファイル/ディレクトリ一覧を表示します

```text
[root@0893cd3e1c07 /]# ls
bin  dev  etc  home  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
```

echoコマンドを使って、ファイルを作成します。

`echo ContainerExercise > testfile.txt`{{execute}}

`ls`{{execute}}

ファイルが作成されているか確認します。

```text
[root@0893cd3e1c07 /]# ls
bin  etc   lib    lost+found  mnt  proc  run   srv  testfile.txt  usr
dev  home  lib64  media       opt  root  sbin  sys  tmp           var
```
 testfile.txtが作成されていることが確認できます。
 
 
`cat testfile.txt`{{execute}}

testfile.txtの内容を確認します。

```text
[root@0893cd3e1c07 /]# cat testfile.txt
ContainerExercise
```

`exit`{{execute}}

一通り確認したところで、先程同様exitコマンドでコンテナのターミナルから抜け、コンテナ一覧を確認します。

`docker ps -a`{{execute}}
```text
CONTAINER ID   IMAGE     COMMAND       CREATED              STATUS          PORTS     NAMES
0893cd3e1c07   centos    "/bin/bash"   About a minute ago   Up 37 seconds             mycentos1
```

コンテナ一覧を確認すると、先程の`docker run`の場合と異なりコンテナは停止せず、起動したままです。今度はdockerコマンドで明示的にコンテナを停止します。

`docker stop mycentos1`{{execute}}

`docker ps -a`{{execute}}

停止したことが確認できます。

```text
CONTAINER ID   IMAGE     COMMAND       CREATED              STATUS                     PORTS     NAMES
0893cd3e1c07   centos    "/bin/bash"   About a minute ago   Exited (0) 3 seconds ago             mycentos1
```
コンテナをまた起動して、状態を確認します。

`docker start mycentos1`{{execute}}

`docker ps -a`{{execute}}

```text
CONTAINER ID   IMAGE     COMMAND       CREATED         STATUS         PORTS     NAMES
0893cd3e1c07   centos    "/bin/bash"   2 minutes ago   Up 4 seconds             mycentos1
```

execコマンドを利用してbashの利用を可能にします。

`docker exec -it mycentos1 /bin/bash`{{execute}}
```text
[root@0893cd3e1c07 /]# 
```
ここで、先程作成したファイル`testfile.txt`が、コンテナ停止の影響を受けてるか確認します。

`ls`{{execute}}
```text
[root@0893cd3e1c07 /]# ls
bin  etc   lib    lost+found  mnt  proc  run   srv  testfile.txt  usr
dev  home  lib64  media       opt  root  sbin  sys  tmp           var
```
`cat testfile.txt`{{execute}}
```text
[root@0893cd3e1c07 /]# cat testfile.txt
ContainerExercise
```

先程作ったファイルはそのまま存在してます。コンテナの停止によって影響は受けないことはわかりました。

`exit`{{execute}}
```text
exit
```

コンテナの起動や停止・開始の一連の操作でコンテナの状態がどのように推移するかなんとなく理解できたかと思います。とはいえ、まだCentOSコンテナの操作をちょっと行ってみただけなので、次のステップではCentOSコンテナにアプリケーションをインストールして、アプリの動作を確認してみましょう。

###  このステップで利用したdockerコマンド
- docker pull [オプション] NAME[:TAG|@DIGEST] 
   - レジストリからイメージまたはリポジトリを取得する
- docker exec [オプション] CONTAINER COMMAND [ARG...]
   - 実行中のコンテナ内において新たなコマンドを実行する
- docker start [オプション] CONTAINER [CONTAINER...]
   - 停止しているコンテナを起動する
- docker stop [オプション] CONTAINER [CONTAINER...]
   - 実行中のコンテナを停止する
