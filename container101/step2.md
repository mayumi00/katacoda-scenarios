
# コンテナの起動とコンテナでのプロセス

![Test Image 1](https://raw.githubusercontent.com/mayumi00/katacoda-scenarios/main/container101/images/image01.png)　

特定の処理をして停止するコンテナを動かしてみましたが、停止してしまっているので今ひとつピンと来ない点もあるかと思います。そこで、普段利用している仮想マシンのような感覚で触れるコンテナを利用してみましょう。

> Note: 出力例に表示されているコンテナIDやイメージID、起動時間などは使用している環境と状態によって異なります。このテキストとまったく同じにはならないのでご注意ください。

今回はいきなりrunするのではなく、dockerイメージをpullするところから始めます。馴染みの深いCentOSを利用します。再度、現在自ホストにあるコンテナイメージを確認します。centosという文字は見当たりません。

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

とりあえずCentOSの最新版のコンテナをコンテナレジストリからダウンロードします。pullする際にlatestのタグを指定します。
 
利用方法  `docker pull [オプション] NAME[:TAG|@DIGEST] `
 
`docker pull centos:latest`{{execute}}

```text
latest: Pulling from library/centos
a1d0c7532777: Pull complete 
Digest: sha256:a27fd8080b517143cbbbab9dfb7c8571c40d67d534bbdee55bd6c473f432b177
Status: Downloaded newer image for centos:latest
docker.io/library/centos:latest
```

再度、コンテナイメージを確認します。

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
centosの最新版が自ホストのイメージ格納領域にダウンロードされたことがわかります。

> Note: 特定の文字列を含むイメージを検索することができます。この例ではcentosという文字列を含むコンテナイメージを検索しています。

`docker search centos`{{execute}}
```text
NAME                              DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
centos                            The official build of CentOS.                   6928      [OK]       
ansible/centos7-ansible           Ansible on Centos7                              135                  [OK]
consol/centos-xfce-vnc            Centos container with "headless" VNC session…   132                  [OK]
jdeathe/centos-ssh                OpenSSH / Supervisor / EPEL/IUS/SCL Repos - …   121                  [OK]
centos/systemd                    systemd enabled base container.                 105                  [OK]
centos/mysql-57-centos7           MySQL 5.7 SQL database server                   92                   
imagine10255/centos6-lnmp-php56   centos6-lnmp-php56                              58                   [OK]
tutum/centos                      Simple CentOS docker image with SSH access      48                   
centos/postgresql-96-centos7      PostgreSQL is an advanced Object-Relational …   45                   
kinogmt/centos-ssh                CentOS with SSH                                 29                   [OK]
centos/mariadb                                                                    22                   [OK]
guyton/centos6                    From official centos6 container with full up…   10                   [OK]
nathonfowlie/centos-jre           Latest CentOS image with the JRE pre-install…   8                    [OK]
centos/tools                      Docker image that has systems administration…   7                    [OK]
drecom/centos-ruby                centos ruby                                     6                    [OK]
centos/redis                      Redis built for CentOS                          6                    [OK]
mamohr/centos-java                Oracle Java 8 Docker image based on Centos 7    3                    [OK]
darksheer/centos                  Base Centos Image -- Updated hourly             3                    [OK]
miko2u/centos6                    CentOS6 日本語環境                                   2                    [OK]
amd64/centos                      The official build of CentOS.                   2                    
dokken/centos-7                   CentOS 7 image for kitchen-dokken               2                    
blacklabelops/centos              CentOS Base Image! Built and Updates Daily!     1                    [OK]
mcnaughton/centos-base            centos base image                               1                    [OK]
starlabio/centos-native-build     Our CentOS image for native builds              0                    [OK]
smartentry/centos                 centos with smartentry                          0                    [OK]
```

それでは、このCentOSのコンテナイメージを起動します。先程の章で、コンテナを起動する際に`--nameオプション`を付けるとコンテナの名前をつけることができると説明しました。今回はコンテナにmycentos1という名前を付けることにします。また、`-i（or --interactive）オプション`は標準入力を受け付けるもので　`-t（or --tty）オプション`は疑似TTYの割当を行うものです。これらを組み合わせてbashをインタラクティブモードで起動し、コンテナ内での操作を可能にします。コンテナ起動直後に実施するコマンドとして/bin/bashを指定します。

利用方法 `docker run [オプション] IMAGE [COMMAND] [ARG...]`

`docker run -it --name mycentos1 centos /bin/bash`{{execute}}

```text
[root@0893cd3e1c07 /]#
```

プロンプトが表示され、bashの利用が可能になっています。コンテナを起動する際に`-h（or --hostname）オプション`を付けるとコンテナのホスト名を指定することができますが、今回は指定していないので、コンテナIDが利用されます。プロンプトのroot@の後ろの文字列はコンテナホスト名＝コンテナIDなので、実行した環境によって異なります。いくつかのファイルの内容の確認やコマンドの実行を行ってみます。

実施項目
- /etc/os-releaseの確認
- unameコマンド
- /etc/hostnameの確認

`cat /etc/os-release`{{execute}}

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

```text
[root@187c07b01562 /]# uname -a
Linux 187c07b01562 5.4.0-88-generic #99-Ubuntu SMP Thu Sep 23 17:29:00 UTC 2021 x86_64 x86_64 x86_64 GNU/Linux
```

`cat /etc/hostname`{{execute}}

```text
[root@0893cd3e1c07 /]# cat /etc/hostname
0893cd3e1c07
```
`exit`{{execute}}

```text
[root@0893cd3e1c07 /]# exit
exit
```

`docker ps -a`{{execute}}
```text
CONTAINER ID   IMAGE     COMMAND       CREATED          STATUS                     PORTS     NAMES
0893cd3e1c07   centos    "/bin/bash"   49 seconds ago   Exited (0) 4 seconds ago             mycentos1
```

runで起動したコンテナのターミナルをexitで抜けると、コンテナが停止します。再度、コンテナを起動します。起動するコンテンを指定する方法にはコンテナ名またはコンテナIDを指定します。先程、mycentos1というコンテナ名を付けておいたので、それを指定します。

`docker start mycentos1`{{execute}}

コンテナの起動状況を確認します。STATUSはUpになっていることがわかります。

`docker ps -a`{{execute}}
```text
CONTAINER ID   IMAGE     COMMAND       CREATED              STATUS         PORTS     NAMES
0893cd3e1c07   centos    "/bin/bash"   About a minute ago   Up 7 seconds             mycentos1
```

起動したけど、標準入力を受け付けるオプションを指定しなかったので操作できない状態。そこで、実行中のコンテナー内において新たなコマンドを実行するexecコマンドを利用してbashの利用を可能にします。

利用方法 `docker exec [オプション] CONTAINER COMMAND [ARG...]`

`docker exec -it mycentos1 /bin/bash`{{execute}}
```text
[root@0893cd3e1c07 /]# 
```

bashが利用できる状態になったので、いくつかのコマンドを実行してみましょう。

- 現在存在するファイル/ディレクトリの表示
- テキストファイルを作成
- ファイルが作られたことを確認

`ls`{{execute}}
```text
[root@0893cd3e1c07 /]# ls
bin  dev  etc  home  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var

```

`echo ContainerExercise > testfile.txt`{{execute}}


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

`exit`{{execute}}


`docker ps -a`{{execute}}
```text
CONTAINER ID   IMAGE     COMMAND       CREATED              STATUS          PORTS     NAMES
0893cd3e1c07   centos    "/bin/bash"   About a minute ago   Up 37 seconds             mycentos1
```

コンテナのターミナルから`exit`で抜けます。先程のrunの場合と異なりコンテナは停止せず、起動したままです。今度はdockerコマンドで明示的にコンテナを停止します。

`docker stop mycentos1`{{execute}}
`docker ps -a`{{execute}}
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
