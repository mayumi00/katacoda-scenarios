### ビルドされたイメージの利用例

Step1でDockerfileを利用することで、インストールや設定作業が自動化できることを体験できたと思います。

 `docker images`{{execute}}
 
```text
$ docker images
REPOSITORY             TAG       IMAGE ID       CREATED              SIZE
apacheweb-dockerfile   1.0       3224927b24a4   About a minute ago   278MB
redis                  latest    b8477f2e393b   2 months ago         113MB
mongo                  latest    c1a14d3979c5   2 months ago         691MB
mariadb                10        b7220a722ce2   2 months ago         409MB
mariadb                latest    b7220a722ce2   2 months ago         409MB
ubuntu                 latest    597ce1600cf4   2 months ago         72.8MB
postgres               12        fe603fe275ba   2 months ago         371MB
postgres               latest    6ce504119cc8   2 months ago         374MB
mysql                  8         2fe463762680   2 months ago         514MB
mysql                  latest    2fe463762680   2 months ago         514MB
centos                 latest    5d0da3dc9764   3 months ago         231MB
alpine                 latest    14119a10abf4   4 months ago         5.59MB
weaveworks/scope       1.11.4    a082d48f0b39   2 years ago          78.5MB
```

同じ設定を持つコンテナを複数個起動したい場合などはDockerfileを使って作成したコンテナイメージからコンテンを起動すれば良いわけです。以下の例は、apacheweb-dockerfile:1.0から9個のコンテナを起動しています。


` for i in {1..9} ; do  echo testweb0${i} ; docker run -d -p 808${i}:80 --name testweb0${i} apacheweb-dockerfile:1.0 ; done `{{execute}}

```text
$ for i in {1..9} ; do  echo testweb0${i} ; docker run -d -p 808${i}:80 --name testweb0${i} apacheweb-dockerfile:1.0 ; done
testweb01
c8f2d3a6d38afd25fa38291ef6ad9e6062b853581e1502450cc57a738ba33c37
testweb02
fa59ff20d5d0ed9b248cdea153ec5ef1f6494e835a82f18c61dff4c7226987f2
testweb03
e4248d60288764ed0e992f0e7611511b9364e2091126a52fd47e51aa4b252e3f
testweb04
9bdf98fc9ba067bb838d8717c001b70b40eea8b984a1fc271c7b89c4b65018a9
testweb05
9ae5cc511156c4cc14ab571f62622681aca65464f3efd8d73812a04cc90d7ac2
testweb06
bb3f5a7b6acb14f274dc79e70ac3f5b81f785d4a05ccb489f1bce069f81df0c1
testweb07
adbb50899a65ce1265c94fbd41dee764344c3946a1ee7cef6d3212aaca139134
testweb08
b015d6ea166566b446a30a37c66bff6e2df90309e2d31300a9caba835a9879ae
testweb09
51b9a819ea63d3acf91a8c72c33a28f2e42727326cfb20c5e355185ea04af0cb
```

`for i in {1..9} ; do echo testweb0${i} ; curl http://localhost:808${i} ; done`{{execute}}

```text
$ for i in {1..9} ; do echo testweb0${i} ; curl http://localhost:808${i} ; done
testweb01
<head><title>Apache on Docker Container</title></head><body><H1>Container 102 - Web</H1>Apache on Docker Container using Dockerfile</body>
testweb02
<head><title>Apache on Docker Container</title></head><body><H1>Container 102 - Web</H1>Apache on Docker Container using Dockerfile</body>
testweb03
<head><title>Apache on Docker Container</title></head><body><H1>Container 102 - Web</H1>Apache on Docker Container using Dockerfile</body>
testweb04
<head><title>Apache on Docker Container</title></head><body><H1>Container 102 - Web</H1>Apache on Docker Container using Dockerfile</body>
testweb05
<head><title>Apache on Docker Container</title></head><body><H1>Container 102 - Web</H1>Apache on Docker Container using Dockerfile</body>
testweb06
<head><title>Apache on Docker Container</title></head><body><H1>Container 102 - Web</H1>Apache on Docker Container using Dockerfile</body>
testweb07
<head><title>Apache on Docker Container</title></head><body><H1>Container 102 - Web</H1>Apache on Docker Container using Dockerfile</body>
testweb08
<head><title>Apache on Docker Container</title></head><body><H1>Container 102 - Web</H1>Apache on Docker Container using Dockerfile</body>
testweb09
<head><title>Apache on Docker Container</title></head><body><H1>Container 102 - Web</H1>Apache on Docker Container using Dockerfile</body>
```

`docker ps -a`{{execute}}

```text
$ docker ps -a
CONTAINER ID   IMAGE                      COMMAND                  CREATED              STATUS              PORTS                                   NAMES
51b9a819ea63   apacheweb-dockerfile:1.0   "/usr/sbin/httpd -DF…"   11 seconds ago       Up 11 seconds       0.0.0.0:8089->80/tcp, :::8089->80/tcp   testweb09
b015d6ea1665   apacheweb-dockerfile:1.0   "/usr/sbin/httpd -DF…"   12 seconds ago       Up 11 seconds       0.0.0.0:8088->80/tcp, :::8088->80/tcp   testweb08
adbb50899a65   apacheweb-dockerfile:1.0   "/usr/sbin/httpd -DF…"   13 seconds ago       Up 12 seconds       0.0.0.0:8087->80/tcp, :::8087->80/tcp   testweb07
bb3f5a7b6acb   apacheweb-dockerfile:1.0   "/usr/sbin/httpd -DF…"   13 seconds ago       Up 13 seconds       0.0.0.0:8086->80/tcp, :::8086->80/tcp   testweb06
9ae5cc511156   apacheweb-dockerfile:1.0   "/usr/sbin/httpd -DF…"   14 seconds ago       Up 13 seconds       0.0.0.0:8085->80/tcp, :::8085->80/tcp   testweb05
9bdf98fc9ba0   apacheweb-dockerfile:1.0   "/usr/sbin/httpd -DF…"   14 seconds ago       Up 14 seconds       0.0.0.0:8084->80/tcp, :::8084->80/tcp   testweb04
e4248d602887   apacheweb-dockerfile:1.0   "/usr/sbin/httpd -DF…"   15 seconds ago       Up 14 seconds       0.0.0.0:8083->80/tcp, :::8083->80/tcp   testweb03
fa59ff20d5d0   apacheweb-dockerfile:1.0   "/usr/sbin/httpd -DF…"   15 seconds ago       Up 15 seconds       0.0.0.0:8082->80/tcp, :::8082->80/tcp   testweb02
c8f2d3a6d38a   apacheweb-dockerfile:1.0   "/usr/sbin/httpd -DF…"   16 seconds ago       Up 15 seconds       0.0.0.0:8081->80/tcp, :::8081->80/tcp   testweb01
14d51cd305c8   apacheweb-dockerfile:1.0   "/usr/sbin/httpd -DF…"   About a minute ago   Up About a minute   0.0.0.0:8080->80/tcp, :::8080->80/tcp   testeweb00
```

現在は図のように、Dockerfileからビルドされたapacheweb-dockerfile:1.0を元に同じコンテナが10起動してます。

![Test Image 1](https://raw.githubusercontent.com/mayumi00/katacoda-scenarios/main/container102/images/image203.png)

次のステップに進む前に、大量に起動してしまったコンテナを停止し削除します。

> Note: コンテナイメージは削除しません。

testweb01からtestweb09を停止します。

` for i in {1..9} ; do docker stop testweb0${i} ; done`{{execute}}

```text
[root@ik1-314-17333 ~]#  for i in {1..9} ; do docker stop testweb0${i} ; done
testweb01
testweb02
testweb03
testweb04
testweb05
testweb06
testweb07
testweb08
testweb09
```

停止したことを確認します。

`docker ps -a`{{execute}}

```text
[root@ik1-314-17333 ~]# docker ps -a
$ docker ps -a
CONTAINER ID   IMAGE                      COMMAND                  CREATED              STATUS                     PORTS                                   NAMES
51b9a819ea63   apacheweb-dockerfile:1.0   "/usr/sbin/httpd -DF…"   28 seconds ago       Exited (0) 4 seconds ago                                           testweb09
b015d6ea1665   apacheweb-dockerfile:1.0   "/usr/sbin/httpd -DF…"   29 seconds ago       Exited (0) 4 seconds ago                                           testweb08
adbb50899a65   apacheweb-dockerfile:1.0   "/usr/sbin/httpd -DF…"   30 seconds ago       Exited (0) 5 seconds ago                                           testweb07
bb3f5a7b6acb   apacheweb-dockerfile:1.0   "/usr/sbin/httpd -DF…"   30 seconds ago       Exited (0) 5 seconds ago                                           testweb06
9ae5cc511156   apacheweb-dockerfile:1.0   "/usr/sbin/httpd -DF…"   31 seconds ago       Exited (0) 5 seconds ago                                           testweb05
9bdf98fc9ba0   apacheweb-dockerfile:1.0   "/usr/sbin/httpd -DF…"   31 seconds ago       Exited (0) 6 seconds ago                                           testweb04
e4248d602887   apacheweb-dockerfile:1.0   "/usr/sbin/httpd -DF…"   32 seconds ago       Exited (0) 6 seconds ago                                           testweb03
fa59ff20d5d0   apacheweb-dockerfile:1.0   "/usr/sbin/httpd -DF…"   32 seconds ago       Exited (0) 6 seconds ago                                           testweb02
c8f2d3a6d38a   apacheweb-dockerfile:1.0   "/usr/sbin/httpd -DF…"   33 seconds ago       Exited (0) 7 seconds ago                                           testweb01
14d51cd305c8   apacheweb-dockerfile:1.0   "/usr/sbin/httpd -DF…"   About a minute ago   Up About a minute          0.0.0.0:8080->80/tcp, :::8080->80/tcp   testeweb00
```

testweb00以外は停止（Exited）していることがわかります。続いて、停止したtestweb01からtestweb09を削除します。

`for i in {1..9} ; do docker rm testweb0${i} ; done`{{execute}}

```text
[root@ik1-314-17333 ~]# for i in {1..9} ; do docker rm testweb0${i} ; done
testweb01
testweb02
testweb03
testweb04
testweb05
testweb06
testweb07
testweb08
testweb09
```
コンテナ一覧を確認すると停止したtestweb01からtestweb09までが一覧から消えていることが確認できます。

`docker ps -a`{{execute}}

```text
CONTAINER ID   IMAGE                      COMMAND                  CREATED          STATUS                      PORTS                                   NAMES
c8f2d3a6d38a   apacheweb-dockerfile:1.0   "/usr/sbin/httpd -DF…"   47 seconds ago   Exited (0) 20 seconds ago                                           testweb01
14d51cd305c8   apacheweb-dockerfile:1.0   "/usr/sbin/httpd -DF…"   2 minutes ago    Up 2 minutes                0.0.0.0:8080->80/tcp, :::8080->80/tcp   testeweb00
```
現在は以下の図のような状態です。

![Test Image 1](https://raw.githubusercontent.com/mayumi00/katacoda-scenarios/main/container102/images/image204.png)

コンテナの操作性というものの良さをちょっとは感じられるのではないでしょうか？


###  このステップで利用したdockerコマンド
- docker rm [OPTIONS] CONTAINER [CONTAINER...]
  - コンテナを削除する