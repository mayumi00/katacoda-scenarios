
# コンテナの起動と

![Test Image 1](https://raw.githubusercontent.com/mayumi00/katacoda-scenarios/main/container101/images/image01.png)　

特定の処理をして停止するコンテナを動かしてみましたが、停止してしまっているので今ひとつピンと来ない点もあるかと思います。そこで、普段利用している仮想マシンのような感覚で触れるコンテナを利用してみましょう。

今回はいきなりrunするのではなく、dockerイメージをpullするところから始めます。馴染みの深いCentOSを利用します。

再度、現在自ホストにあるコンテナイメージを確認します。centosという文字は見当たりません。

 `docker images`{{execute}}
 
 とりあえずCentOSの最新版のコンテナをコンテナレジストリからダウンロードします。pullする際にlatestのタグを指定しています。
 
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

それではこのCentOSのコンテナイメージを起動します。先程の章で、コンテナを起動する際に`--nameオプション`を付けるとコンテナの名前をつけることができると説明しました。今回はコンテナにmycentos1という名前を付けることにします。

`-i（or --interactive）オプション`は標準入力を受け付けるもので　`-t（or --tty）オプション`は疑似TTYの割当を行うものです、これらを組み合わせて、bashをインタラクティブモードで起動し、コンテナの操作を可能にします。コンテナ起動直後に実施するコマンドとして/bin/bashを指定します。

`docker run [オプション] IMAGE [COMMAND] [ARG...]`

`docker run -it --name mycentos1 centos /bin/bash`{{execute}}

`[root@187c07b01562 /]# ` プロンプトが表示され、bashの利用が可能になっています。このコンテナはCentOSなので、いくつか試しに確認してみます。

`cat /etc/redhat-release`{{execute}}

```text
[root@187c07b01562 /]# cat /etc/redhat-release 
CentOS Linux release 8.4.2105
```

`uname -a`{{execute}}

```text
[root@187c07b01562 /]# uname -a
Linux 187c07b01562 5.4.0-88-generic #99-Ubuntu SMP Thu Sep 23 17:29:00 UTC 2021 x86_64 x86_64 x86_64 GNU/Linux
```

`cat /etc/hostname`{{execute}}

```text
[root@187c07b01562 /]# cat /etc/hostname 
187c07b01562
```
`exit`{{execute}}

```text
[root@187c07b01562 /]# exit
```

`docker ps -a`{{execute}}
```text
CONTAINER ID   IMAGE     COMMAND       CREATED         STATUS                      PORTS     NAMES
187c07b01562   centos    "/bin/bash"   5 minutes ago   Exited (0) 12 seconds ago             mycentos1
```

exitで抜けると、コンテナが停止してしまいました。再度、コンテナを起動します。起動するコンテンを指定する方法にはコンテナ名またはコンテナIDを指定します。先程、mycentos1というコンテナ名を付けておいたので、それを指定します。

`docker start mycentos1`{{execute}}
```text
mycentos1
```

`docker ps -a`{{execute}}
```text
CONTAINER ID   IMAGE     COMMAND       CREATED         STATUS         PORTS     NAMES
187c07b01562   centos    "/bin/bash"   9 minutes ago   Up 5 seconds             mycentos1
```

起動したけど、標準入力を受け付ける設定をしなかったので操作できない状態。そこで、実行中のコンテナー内において新たなコマンドを実行するexecコマンドを利用してbashの利用を可能にします。

`docker exec -it mycentos1 /bin/bash`{{execute}}
```text
[root@187c07b01562 /]#
```

`ls`{{execute}}
```text
[root@187c07b01562 /]#
```

`touch testfile`{{execute}}
```text
[root@187c07b01562 /]#
```

`ls`{{execute}}
```text
[root@187c07b01562 /]#
```

`exit`{{execute}}
```text
exit
```

`docker ps -a`{{execute}}
```text
CONTAINER ID   IMAGE     COMMAND       CREATED         STATUS              PORTS     NAMES
187c07b01562   centos    "/bin/bash"   8 minutes ago   Up About a minute             mycentos1
```

`docker stop mycentos1`{{execute}}
```text
mycentos1
```

`docker ps -a`{{execute}}
```text
CONTAINER ID   IMAGE     COMMAND       CREATED         STATUS                      PORTS     NAMES
187c07b01562   centos    "/bin/bash"   9 minutes ago   Exited (0) 19 seconds ago             mycentos1
```

`docker start mycentos1`{{execute}}
```text
CONTAINER ID   IMAGE     COMMAND       CREATED         STATUS         PORTS     NAMES
187c07b01562   centos    "/bin/bash"   9 minutes ago   Up 5 seconds             mycentos1
```

`docker ps -a`{{execute}}
```text
mycentos1
```

`docker exec -it mycentos1 /bin/bash`{{execute}}
```text
[root@187c07b01562 /]#
```

`ls`{{execute}}
```text
[root@187c07b01562 /]#
```

`exit`{{execute}}
```text
exit
```


$ docker start mycentos1
mycentos1
$ docker ps -a
CONTAINER ID   IMAGE     COMMAND       CREATED         STATUS         PORTS     NAMES
187c07b01562   centos    "/bin/bash"   9 minutes ago   Up 5 seconds             mycentos1


$ docker exec -it mycentos1 /bin/bash




$ docker start mycentos1
mycentos1










Your Interactive Bash Terminal.

$ docker images
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
$ docker pull centos:latest
latest: Pulling from library/centos
a1d0c7532777: Pull complete 
Digest: sha256:a27fd8080b517143cbbbab9dfb7c8571c40d67d534bbdee55bd6c473f432b177
Status: Downloaded newer image for centos:latest
docker.io/library/centos:latest
$ docker images
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
centos             latest    5d0da3dc9764   3 months ago   231MB
alpine             latest    14119a10abf4   3 months ago   5.59MB
weaveworks/scope   1.11.4    a082d48f0b39   2 years ago    78.5MB
$ docker run -it --name mycentos1 centos /bin/bash
[root@187c07b01562 /]# cat /etc/redhat-release 
CentOS Linux release 8.4.2105
[root@187c07b01562 /]# cat /etc/hostname 
187c07b01562
[root@187c07b01562 /]# ps -a
    PID TTY          TIME CMD
     18 pts/0    00:00:00 ps
[root@187c07b01562 /]# df -k
Filesystem               1K-blocks    Used Available Use% Mounted on
overlay                   48626364 9079436  37047140  20% /
tmpfs                        65536       0     65536   0% /dev
tmpfs                       759508       0    759508   0% /sys/fs/cgroup
shm                          65536       0     65536   0% /dev/shm
/dev/mapper/primary-root  48626364 9079436  37047140  20% /etc/hosts
tmpfs                       759508       0    759508   0% /proc/acpi
tmpfs                       759508       0    759508   0% /proc/scsi
tmpfs                       759508       0    759508   0% /sys/firmware
[root@187c07b01562 /]# ls -a
.   .dockerenv  dev  home  lib64       media  opt   root  sbin  sys  usr
..  bin         etc  lib   lost+found  mnt    proc  run   srv   tmp  var
[root@187c07b01562 /]# ls -lsa
total 56
4 drwxr-xr-x   1 root root 4096 Dec 17 05:35 .
4 drwxr-xr-x   1 root root 4096 Dec 17 05:35 ..
0 -rwxr-xr-x   1 root root    0 Dec 17 05:35 .dockerenv
0 lrwxrwxrwx   1 root root    7 Nov  3  2020 bin -> usr/bin
0 drwxr-xr-x   5 root root  360 Dec 17 05:35 dev
4 drwxr-xr-x   1 root root 4096 Dec 17 05:35 etc
4 drwxr-xr-x   2 root root 4096 Nov  3  2020 home
0 lrwxrwxrwx   1 root root    7 Nov  3  2020 lib -> usr/lib
0 lrwxrwxrwx   1 root root    9 Nov  3  2020 lib64 -> usr/lib64
4 drwx------   2 root root 4096 Sep 15 14:17 lost+found
4 drwxr-xr-x   2 root root 4096 Nov  3  2020 media
4 drwxr-xr-x   2 root root 4096 Nov  3  2020 mnt
4 drwxr-xr-x   2 root root 4096 Nov  3  2020 opt
0 dr-xr-xr-x 166 root root    0 Dec 17 05:35 proc
4 dr-xr-x---   2 root root 4096 Sep 15 14:17 root
4 drwxr-xr-x  11 root root 4096 Sep 15 14:17 run
0 lrwxrwxrwx   1 root root    8 Nov  3  2020 sbin -> usr/sbin
4 drwxr-xr-x   2 root root 4096 Nov  3  2020 srv
0 dr-xr-xr-x  13 root root    0 Dec 17 05:35 sys
4 drwxrwxrwt   7 root root 4096 Sep 15 14:17 tmp
4 drwxr-xr-x  12 root root 4096 Sep 15 14:17 usr
4 drwxr-xr-x  20 root root 4096 Sep 15 14:17 var
[root@187c07b01562 /]# pwd
/
[root@187c07b01562 /]# touch testfile
[root@187c07b01562 /]# ls 
bin  etc   lib    lost+found  mnt  proc  run   srv  testfile  usr
dev  home  lib64  media       opt  root  sbin  sys  tmp       var
[root@187c07b01562 /]# ls -lsa
total 56
4 drwxr-xr-x   1 root root 4096 Dec 17 05:39 .
4 drwxr-xr-x   1 root root 4096 Dec 17 05:39 ..
0 -rwxr-xr-x   1 root root    0 Dec 17 05:35 .dockerenv
0 lrwxrwxrwx   1 root root    7 Nov  3  2020 bin -> usr/bin
0 drwxr-xr-x   5 root root  360 Dec 17 05:35 dev
4 drwxr-xr-x   1 root root 4096 Dec 17 05:35 etc
4 drwxr-xr-x   2 root root 4096 Nov  3  2020 home
0 lrwxrwxrwx   1 root root    7 Nov  3  2020 lib -> usr/lib
0 lrwxrwxrwx   1 root root    9 Nov  3  2020 lib64 -> usr/lib64
4 drwx------   2 root root 4096 Sep 15 14:17 lost+found
4 drwxr-xr-x   2 root root 4096 Nov  3  2020 media
4 drwxr-xr-x   2 root root 4096 Nov  3  2020 mnt
4 drwxr-xr-x   2 root root 4096 Nov  3  2020 opt
0 dr-xr-xr-x 170 root root    0 Dec 17 05:35 proc
4 dr-xr-x---   2 root root 4096 Sep 15 14:17 root
4 drwxr-xr-x  11 root root 4096 Sep 15 14:17 run
0 lrwxrwxrwx   1 root root    8 Nov  3  2020 sbin -> usr/sbin
4 drwxr-xr-x   2 root root 4096 Nov  3  2020 srv
0 dr-xr-xr-x  13 root root    0 Dec 17 05:35 sys
0 -rw-r--r--   1 root root    0 Dec 17 05:39 testfile
4 drwxrwxrwt   7 root root 4096 Sep 15 14:17 tmp
4 drwxr-xr-x  12 root root 4096 Sep 15 14:17 usr
4 drwxr-xr-x  20 root root 4096 Sep 15 14:17 var
[root@187c07b01562 /]# exit
exit
$ docker ps -a
CONTAINER ID   IMAGE     COMMAND       CREATED         STATUS                      PORTS     NAMES
187c07b01562   centos    "/bin/bash"   5 minutes ago   Exited (0) 12 seconds ago             mycentos1
$ docker exec -it mycentos1 /bin/bash
Error response from daemon: Container 187c07b015628c553fc06e3a7b5e377b8b3a78fc9fc7d4d2c67860b082658036 is not running
$ docker start 187c07b01562
187c07b01562
$ docker ps -a
CONTAINER ID   IMAGE     COMMAND       CREATED         STATUS          PORTS     NAMES
187c07b01562   centos    "/bin/bash"   7 minutes ago   Up 10 seconds             mycentos1
$ docker exec -it mycentos1 /bin/bash
[root@187c07b01562 /]# ls -lsa
total 56
4 drwxr-xr-x   1 root root 4096 Dec 17 05:39 .
4 drwxr-xr-x   1 root root 4096 Dec 17 05:39 ..
0 -rwxr-xr-x   1 root root    0 Dec 17 05:35 .dockerenv
0 lrwxrwxrwx   1 root root    7 Nov  3  2020 bin -> usr/bin
0 drwxr-xr-x   5 root root  360 Dec 17 05:42 dev
4 drwxr-xr-x   1 root root 4096 Dec 17 05:35 etc
4 drwxr-xr-x   2 root root 4096 Nov  3  2020 home
0 lrwxrwxrwx   1 root root    7 Nov  3  2020 lib -> usr/lib
0 lrwxrwxrwx   1 root root    9 Nov  3  2020 lib64 -> usr/lib64
4 drwx------   2 root root 4096 Sep 15 14:17 lost+found
4 drwxr-xr-x   2 root root 4096 Nov  3  2020 media
4 drwxr-xr-x   2 root root 4096 Nov  3  2020 mnt
4 drwxr-xr-x   2 root root 4096 Nov  3  2020 opt
0 dr-xr-xr-x 169 root root    0 Dec 17 05:42 proc
4 dr-xr-x---   1 root root 4096 Dec 17 05:41 root
4 drwxr-xr-x  11 root root 4096 Sep 15 14:17 run
0 lrwxrwxrwx   1 root root    8 Nov  3  2020 sbin -> usr/sbin
4 drwxr-xr-x   2 root root 4096 Nov  3  2020 srv
0 dr-xr-xr-x  13 root root    0 Dec 17 05:42 sys
0 -rw-r--r--   1 root root    0 Dec 17 05:39 testfile
4 drwxrwxrwt   7 root root 4096 Sep 15 14:17 tmp
4 drwxr-xr-x  12 root root 4096 Sep 15 14:17 usr
4 drwxr-xr-x  20 root root 4096 Sep 15 14:17 var
[root@187c07b01562 /]# exit
exit
$ docker ps -a
CONTAINER ID   IMAGE     COMMAND       CREATED         STATUS              PORTS     NAMES
187c07b01562   centos    "/bin/bash"   8 minutes ago   Up About a minute             mycentos1
$ docker stop mycentos1
mycentos1
$ docker ps -a
CONTAINER ID   IMAGE     COMMAND       CREATED         STATUS                      PORTS     NAMES
187c07b01562   centos    "/bin/bash"   9 minutes ago   Exited (0) 19 seconds ago             mycentos1
$ docker start mycentos1
mycentos1
$ docker ps -a
CONTAINER ID   IMAGE     COMMAND       CREATED         STATUS         PORTS     NAMES
187c07b01562   centos    "/bin/bash"   9 minutes ago   Up 5 seconds             mycentos1


[root@187c07b01562 /]# uname -a
Linux 187c07b01562 5.4.0-88-generic #99-Ubuntu SMP Thu Sep 23 17:29:00 UTC 2021 x86_64 x86_64 x86_64 GNU/Linux


[root@187c07b01562 /]# cat /etc/redhat-release 
CentOS Linux release 8.4.2105
[root@187c07b01562 /]# cat /etc/hostname 
187c07b01562
[root@187c07b01562 /]# 

Your Interactive Bash Terminal.

$ docker images
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
$ docker pull centos:latest
latest: Pulling from library/centos
a1d0c7532777: Pull complete 
Digest: sha256:a27fd8080b517143cbbbab9dfb7c8571c40d67d534bbdee55bd6c473f432b177
Status: Downloaded newer image for centos:latest
docker.io/library/centos:latest
$ docker images
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
centos             latest    5d0da3dc9764   3 months ago   231MB
alpine             latest    14119a10abf4   3 months ago   5.59MB
weaveworks/scope   1.11.4    a082d48f0b39   2 years ago    78.5MB
$ docker run -it --name mycentos1 centos /bin/bash
[root@187c07b01562 /]# cat /etc/redhat-release 
CentOS Linux release 8.4.2105
[root@187c07b01562 /]# cat /etc/hostname 
187c07b01562
[root@187c07b01562 /]# ps -a
    PID TTY          TIME CMD
     18 pts/0    00:00:00 ps
[root@187c07b01562 /]# df -k
Filesystem               1K-blocks    Used Available Use% Mounted on
overlay                   48626364 9079436  37047140  20% /
tmpfs                        65536       0     65536   0% /dev
tmpfs                       759508       0    759508   0% /sys/fs/cgroup
shm                          65536       0     65536   0% /dev/shm
/dev/mapper/primary-root  48626364 9079436  37047140  20% /etc/hosts
tmpfs                       759508       0    759508   0% /proc/acpi
tmpfs                       759508       0    759508   0% /proc/scsi
tmpfs                       759508       0    759508   0% /sys/firmware
[root@187c07b01562 /]# ls -a
.   .dockerenv  dev  home  lib64       media  opt   root  sbin  sys  usr
..  bin         etc  lib   lost+found  mnt    proc  run   srv   tmp  var
[root@187c07b01562 /]# ls -lsa
total 56
4 drwxr-xr-x   1 root root 4096 Dec 17 05:35 .
4 drwxr-xr-x   1 root root 4096 Dec 17 05:35 ..
0 -rwxr-xr-x   1 root root    0 Dec 17 05:35 .dockerenv
0 lrwxrwxrwx   1 root root    7 Nov  3  2020 bin -> usr/bin
0 drwxr-xr-x   5 root root  360 Dec 17 05:35 dev
4 drwxr-xr-x   1 root root 4096 Dec 17 05:35 etc
4 drwxr-xr-x   2 root root 4096 Nov  3  2020 home
0 lrwxrwxrwx   1 root root    7 Nov  3  2020 lib -> usr/lib
0 lrwxrwxrwx   1 root root    9 Nov  3  2020 lib64 -> usr/lib64
4 drwx------   2 root root 4096 Sep 15 14:17 lost+found
4 drwxr-xr-x   2 root root 4096 Nov  3  2020 media
4 drwxr-xr-x   2 root root 4096 Nov  3  2020 mnt
4 drwxr-xr-x   2 root root 4096 Nov  3  2020 opt
0 dr-xr-xr-x 166 root root    0 Dec 17 05:35 proc
4 dr-xr-x---   2 root root 4096 Sep 15 14:17 root
4 drwxr-xr-x  11 root root 4096 Sep 15 14:17 run
0 lrwxrwxrwx   1 root root    8 Nov  3  2020 sbin -> usr/sbin
4 drwxr-xr-x   2 root root 4096 Nov  3  2020 srv
0 dr-xr-xr-x  13 root root    0 Dec 17 05:35 sys
4 drwxrwxrwt   7 root root 4096 Sep 15 14:17 tmp
4 drwxr-xr-x  12 root root 4096 Sep 15 14:17 usr
4 drwxr-xr-x  20 root root 4096 Sep 15 14:17 var
[root@187c07b01562 /]# pwd
/
[root@187c07b01562 /]# touch testfile
[root@187c07b01562 /]# ls 
bin  etc   lib    lost+found  mnt  proc  run   srv  testfile  usr
dev  home  lib64  media       opt  root  sbin  sys  tmp       var
[root@187c07b01562 /]# ls -lsa
total 56
4 drwxr-xr-x   1 root root 4096 Dec 17 05:39 .
4 drwxr-xr-x   1 root root 4096 Dec 17 05:39 ..
0 -rwxr-xr-x   1 root root    0 Dec 17 05:35 .dockerenv
0 lrwxrwxrwx   1 root root    7 Nov  3  2020 bin -> usr/bin
0 drwxr-xr-x   5 root root  360 Dec 17 05:35 dev
4 drwxr-xr-x   1 root root 4096 Dec 17 05:35 etc
4 drwxr-xr-x   2 root root 4096 Nov  3  2020 home
0 lrwxrwxrwx   1 root root    7 Nov  3  2020 lib -> usr/lib
0 lrwxrwxrwx   1 root root    9 Nov  3  2020 lib64 -> usr/lib64
4 drwx------   2 root root 4096 Sep 15 14:17 lost+found
4 drwxr-xr-x   2 root root 4096 Nov  3  2020 media
4 drwxr-xr-x   2 root root 4096 Nov  3  2020 mnt
4 drwxr-xr-x   2 root root 4096 Nov  3  2020 opt
0 dr-xr-xr-x 170 root root    0 Dec 17 05:35 proc
4 dr-xr-x---   2 root root 4096 Sep 15 14:17 root
4 drwxr-xr-x  11 root root 4096 Sep 15 14:17 run
0 lrwxrwxrwx   1 root root    8 Nov  3  2020 sbin -> usr/sbin
4 drwxr-xr-x   2 root root 4096 Nov  3  2020 srv
0 dr-xr-xr-x  13 root root    0 Dec 17 05:35 sys
0 -rw-r--r--   1 root root    0 Dec 17 05:39 testfile
4 drwxrwxrwt   7 root root 4096 Sep 15 14:17 tmp
4 drwxr-xr-x  12 root root 4096 Sep 15 14:17 usr
4 drwxr-xr-x  20 root root 4096 Sep 15 14:17 var



`docker run -itd --privileged -p 2222:22 --name centos8 --hostname centos8 centos:latest /sbin/init`{{execute}}

`docker ps`{{execute}}

`docker exec -it centos8 /bin/bash`{{execute}}



## Playbook の基礎
---
`playbook` は [YAML](https://ja.wikipedia.org/wiki/YAML) 形式で記述します。YAMLに関して重要なポイントを以下に記載します。

- YAML はデータを表記するためのテキストフォーマットであること。
- ファイルの先頭は `---` から始まる
- インデントが意味を持つ
  - インデントは `space` で表記する。`tab` ではエラーとなります。
- `key`: `value` で辞書形式となる
- [json](https://ja.wikipedia.org/wiki/JavaScript_Object_Notation) と相互に変換可能

以下は playbook のサンプルです。
```yaml
---
- hosts: all
  become: yes
  tasks:
  - name: first task
    yum:
      name: httpd
      state: latest
  - name: second task
    service:
      name: httpd
      state: started
      enabled: yes
```

この内容は、json で表記すると以下のようになります。

```json
[
	{
		"hosts": "all",
		"become": "yes",
		"tasks": [
			{
				"name": "first task",
				"yum": {
					"name": "httpd",
					"state": "latest"
				}
			},
			{
				"name": "second task",
				"service": {
					"name": "httpd",
					"state": "started",
					"enabled": "yes"
				}
			}
		]
	}
]
```

## playbook の作成
---
実際に playbook を作成していきます。

`~/working/first_playbook.yml` をエディタで開いてください。このファイルには先頭に `---` のみが記載されています。以下の説明に従いこのファイルへ追記を行い、playbook として完成させます。

ここでは、WEBサーバーを構築する playbook を作成します。

### play パート
---
以下のようにファイルに追記してください。

```yaml
---
- name: deploy httpd server
  hosts: all
  become: yes
```

ここで記述した内容な以下になります。
- `name:`: ここには、この playbook で行う処理の概要を記載します。省略可能です。日本語を使うことも可能です。
- `hosts: all`: playbook の実行対象となるグループやノードを指定します。これは利用するインベントリー内に存在しているグループやノードである必要があります。
- `become: yes`: この playbook では実行時に特権ユーザーへの権限昇格を行うことを宣言しています。ansible コマンドで利用した `-b` と同じ意味です。

この部分は、playbook 内の `play` パートと呼ばれる部分で全体に関する挙動を宣言します。playbook全体のヘッダーのようなものだと理解してください。playパートで指定できる項目の[詳細については公式ドキュメント](https://docs.ansible.com/ansible/latest/reference_appendices/playbooks_keywords.html#play) を確認してください。

### task パート
---
次に以下の状態となるように先のファイルへ追記します。インデントの階層に注意してください。

```yaml
---
- name: deploy httpd server
  hosts: all
  become: yes
  tasks:
  - name: install httpd
    dnf:
      name: httpd
      state: latest

  - name: start & enabled httpd
    service:
      name: httpd
      state: started
      enabled: yes
```

ここで追記した内容は `task` パートと呼ばれる部分になり、実際にこの playbook が行う処理を記述していきます。task パートではモジュールを呼び出す順番に列挙し、あわせてモジュールに対して必要なパラメーターを与えます。

- `tasks:` これ以降が task パートであることを宣言しています。
- `- name: ...` このタスクの説明を記載しています。省略可能
- `dnf:` `service:` 呼び出すモジュールを指定しています。
- 以下はモジュールに与えられているパラメーターです。
  - `name: httpd` `state: latest`
  - `name: httpd` `state: started` `enabled: yes`

ここで呼び出しているモジュールは以下になります。
- `dnf`: httpd パッケージをインストールするために利用します。
- `service`: インストールされた httpd を起動し、自動起動の設定を有効にしています。

作成した playbook に構文エラーがないかを以下のコマンドで確認できます。

`cd ~/working`{{execute}}

`ansible-playbook first_playbook.yml --syntax-check`{{execute}}

```text
playbook: first_playbook.yml
```

上記はエラー無しのケースです。もしインデントなどに誤りがある場合は以下のようになります。
```text
$ ansible-playbook first_playbook.yml --syntax-check

ERROR! Syntax Error while loading YAML.
  expected <block end>, but found '<block sequence start>'

The error appears to be in '/notebooks/working/first_playbook.yml': line 6, column 2, but may
be elsewhere in the file depending on the exact syntax problem.

The offending line appears to be:

  tasks:
 - name: install httpd
 ^ here
```

この場合には、playbook のインデントなどがサンプルと同じになっているかを再度確認してください。

## playbook の実行
---
作成した playbook を実行します。playbook の実行には `ansible-playbook` コマンドを利用します。成功すれば httpd サーバーが起動して apache の初期画面が参照できるはずです。

`ansible-playbook first_playbook.yml`{{execute}}

```text
PLAY [deploy httpd server] **************************************************

TASK [Gathering Facts] ******************************************************
ok: [node-2]
ok: [node-3]
ok: [node-1]

TASK [install httpd] ********************************************************
changed: [node-1]
changed: [node-2]
changed: [node-3]

TASK [start & enabled httpd] ************************************************
changed: [node-1]
changed: [node-2]
changed: [node-3]

PLAY RECAP ******************************************************************
node-1  : ok=3 changed=2 unreachable=0 failed=0 skipped=0 rescued=0 ignored=0
node-2  : ok=3 changed=2 unreachable=0 failed=0 skipped=0 rescued=0 ignored=0
node-3  : ok=3 changed=2 unreachable=0 failed=0 skipped=0 rescued=0 ignored=0
```

上記のような出力となれば成功です。node-1,2,3 に対してブラウザでアクセスしてサイトの動作を確認してください。

> Note: katacoda 上で演習をしている場合は、画面上部の `node-1,2,3` をクリックします。これらは各ノードのポート80へとリダイレクトされます。

> Note: Jupyter 上で演習をしている場合は、アクセスするIPアドレスを `~/inventory_file` で確認し、`http_access=http://35.73.128.87:8081` に示されたアドレスへブラウザでアクセスしてください。このアドレスは各ノードのポート80へリダイレクトされます。

以下のような画面が表示されれば成功です。

![apache_top_page.png](https://raw.githubusercontent.com/irixjp/katacoda-scenarios/master/materials/images/apache_top_page.png)


## タスクの追加
---
作成した playbook にサイトのトップページを配布するタスクを追加します。

`~/working/files/index.html` をエディタで開きます。

ファイルを以下のように編集します。
```html
<body>
<h1>Apache is running fine</h1>
</body>
```

さらに `first_playbook.yml` を以下のように編集します。
```yaml
---
- name: deploy httpd server
  hosts: all
  become: yes
  tasks:
  - name: install httpd
    dnf:
      name: httpd
      state: latest

  - name: start & enabled httpd
    service:
      name: httpd
      state: started
      enabled: yes

  - name: copy index.html
    copy:
      src: files/index.html
      dest: /var/www/html/
```

編集が完了したら、構文チェックを実施した後に playbook を実行してみましょう。


`ansible-playbook first_playbook.yml --syntax-check`{{execute}}

`ansible-playbook first_playbook.yml`{{execute}}

```text
PLAY [deploy httpd server] **************************************************

TASK [Gathering Facts] ******************************************************
ok: [node-2]
ok: [node-3]
ok: [node-1]

TASK [install httpd] ********************************************************
ok: [node-1]
ok: [node-3]
ok: [node-2]

TASK [start & enabled httpd] ************************************************
ok: [node-2]
ok: [node-1]
ok: [node-3]

TASK [copy index.html] ******************************************************
changed: [node-1]
changed: [node-3]
changed: [node-2]

PLAY RECAP ******************************************************************
node-1  : ok=4 changed=1 unreachable=0 failed=0 skipped=0 rescued=0 ignored=0
node-2  : ok=4 changed=1 unreachable=0 failed=0 skipped=0 rescued=0 ignored=0
node-3  : ok=4 changed=1 unreachable=0 failed=0 skipped=0 rescued=0 ignored=0
```

正常終了したら再びブラウザで3台のノードへアクセスしてください。正しく playbook が記述され、動作したのならば先程作成した `index.html` の内容が表示されるはずです。


## 冪等性(べきとうせい)
---
Ansible のモジュールを利用するメリットして、記述量を大幅に減らせるという解説を行いましたが、その他にもメリットがあります。それが `冪等性` です。

この演習では `ansible-playbook first_playbook.yml` を2回実行しています。httpd のインストールと起動を行った時、そしてサイトのトップページを追加したときです。つまり、httpd のインストールと起動のタスクは2回実行されています。しかし、2回目の playbook 実行にもエラー等は起きていません。これは Ansible の `冪等性` が機能しているからです。

1回目の実行結果と2回目の実行結果を注意深く確認すると、出力結果に違いがあることに気づくはずです。異なる箇所は、それぞれの処理において `changed` と出力されたか、`ok`と出力されたかです。

- `changed`: Ansibleが処理を実行した結果、対象ホストの状態が変わった（Ansibleが実際に設定を行った）
- `ok`: Ansibleは処理を行おうとしたが、既に想定した設定になっているので状態が変わらなかった（Ansibleは設定を行わなかった・行う必要がなかった）

この動作が Ansible が備える冪等性になります。Ansible は今から行おうとする処理を実行する必要があるのか、無いのかを実行前に判断してくれています。

ここで再度この playbook を実行してみましょう。3つのタスクの状態がどのようになるか考えてから実行してください。

`ansible-playbook first_playbook.yml`{{execute}}

```text
PLAY [deploy httpd server] **************************************************

TASK [Gathering Facts] ******************************************************
ok: [node-3]
ok: [node-1]
ok: [node-2]

TASK [install httpd] ********************************************************
ok: [node-2]
ok: [node-1]
ok: [node-3]

TASK [start & enabled httpd] ************************************************
ok: [node-1]
ok: [node-2]
ok: [node-3]

TASK [copy index.html] ******************************************************
ok: [node-3]
ok: [node-1]
ok: [node-2]

PLAY RECAP ******************************************************************
node-1  : ok=4 changed=0 unreachable=0 failed=0 skipped=0 rescued=0 ignored=0
node-2  : ok=4 changed=0 unreachable=0 failed=0 skipped=0 rescued=0 ignored=0
node-3  : ok=4 changed=0 unreachable=0 failed=0 skipped=0 rescued=0 ignored=0
```

全てのタスクは `ok` となったはずです。playbook 実行時の最後の `PLAY RECAP` 部分を並べてみると結果の差が分かりやすく確認できます。ここでは各ノードにおいて、何個のタスクが `changed` になったのかを比較してください。


1回目(タスク2個がchanged)
```text
PLAY RECAP ******************************************************************
node-1  : ok=3 changed=2 unreachable=0 failed=0 skipped=0 rescued=0 ignored=0
node-2  : ok=3 changed=2 unreachable=0 failed=0 skipped=0 rescued=0 ignored=0
node-3  : ok=3 changed=2 unreachable=0 failed=0 skipped=0 rescued=0 ignored=0
```

2回目(タスク1個がchanged)
```text
PLAY RECAP ******************************************************************
node-1  : ok=4 changed=1 unreachable=0 failed=0 skipped=0 rescued=0 ignored=0
node-2  : ok=4 changed=1 unreachable=0 failed=0 skipped=0 rescued=0 ignored=0
node-3  : ok=4 changed=1 unreachable=0 failed=0 skipped=0 rescued=0 ignored=0
```

3回目(changedは0)
```text
PLAY RECAP ******************************************************************
node-1  : ok=4 changed=0 unreachable=0 failed=0 skipped=0 rescued=0 ignored=0
node-2  : ok=4 changed=0 unreachable=0 failed=0 skipped=0 rescued=0 ignored=0
node-3  : ok=4 changed=0 unreachable=0 failed=0 skipped=0 rescued=0 ignored=0
```

では、この冪等性は何が嬉しいのかというと、

- Playbookには「処理の手順ではなく、状態を宣言的に記述できる」→ Playbook＝設定パラメータ＋手順書として扱えるようになる。
- 仮に複数台のホストに対して実行した処理が途中で失敗しても、最初から流し直せる（設定が成功した部分はスキップ(ok)されるため）
- 手順の集約ができる。初期設定、ノードの追加など様々なシーンを１つのPlaybookだけでカバーできるようになる。

> Note: 特に最後の「手順を集約する」という部分は重要です。従来ならば状況に応じて個別に作成する必要があったものを、冪等性を活用することで1本化して自動化することが可能です。冪等性をうまく活用することができれば、作業を自動化して効率化するだけではなく、自動化の開発そのものも大きく効率化できます。

Ansible の各モジュールはこの冪等性を考慮するように作られており、このモジュールを利用することで簡単に、そして安全に自動化を記述することができるようになっています。

これが自作の自動化シェルスクリプトの場合、特に失敗ややり直し時の再実行において、スクリプトを頭から流し直していいのか？ダメなのか？等の面倒な考慮点が生まれてしまうことは容易に想像できると思います(途中から実行する場合の手順や別のスクリプトを準備したり・・・)

> Note: ただし、Ansibleも全てのモジュールが完全な冪等性を保証しているわけではありません。モジュールの中には shell のように何が実行されるかわからないものや、操作対象（NW系機器やクラウド環境）によっては原理的に冪等性の確保が難しいものも存在しています。こういったモジュールを使う場合は利用者が注意を払う必要があります。

## 演習の解答
- [first\_playbook.yml](https://github.com/irixjp/katacoda-scenarios/blob/master/materials/solutions/first_playbook.yml)
- [files/index.html](https://github.com/irixjp/katacoda-scenarios/blob/master/materials/solutions/files/index.html)
