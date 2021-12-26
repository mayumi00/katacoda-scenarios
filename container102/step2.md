### Dockerfileの利用その２

　

Step1でDockerfileを利用することで、インストールや設定作業が自動化できることを体験できたと思います。

 `docker images`{{execute}}
 
```text
$ docker images 
REPOSITORY             TAG       IMAGE ID       CREATED          SIZE
apacheweb-dockerfile:1.0   latest    0c8e45773b1e   11 minutes ago   278MB
redis                  latest    b8477f2e393b   2 months ago     113MB
mongo                  latest    c1a14d3979c5   2 months ago     691MB
mariadb                10        b7220a722ce2   2 months ago     409MB
mariadb                latest    b7220a722ce2   2 months ago     409MB
ubuntu                 latest    597ce1600cf4   2 months ago     72.8MB
postgres               12        fe603fe275ba   2 months ago     371MB
postgres               latest    6ce504119cc8   2 months ago     374MB
mysql                  8         2fe463762680   2 months ago     514MB
mysql                  latest    2fe463762680   2 months ago     514MB
centos                 latest    5d0da3dc9764   3 months ago     231MB
alpine                 latest    14119a10abf4   4 months ago     5.59MB
weaveworks/scope       1.11.4    a082d48f0b39   2 years ago      78.5MB
```

同じ設定を持つコンテナを複数個起動したい場合などはDockerfileを使って作成したコンテナイメージからコンテンを起動すれば良いわけです。以下の例は、apacheweb-dockerfile:1.0から9個のコンテナを起動しています。

` for i in {1..9} ; do  echo testweb0${i} ; docker run -d -p 808${i}:80 --name testweb0${i} apacheweb-dockerfile:1.0 ; done `{{execute}}

```text
$ for i in {1..9} ; do  echo testweb0${i} ; docker run -d -p 808${i}:80 --name testweb0${i} apacheweb-dockerfile:1.0 ; done
testweb01
docker: Error response from daemon: Conflict. The container name "/testweb01" is already in use by container "0249c56bd7156724e4693e3124358271c547bc5bab3ebd6632d60e7d116db697". You have to remove (or rename) that container to be able to reuse that name.
See 'docker run --help'.
testweb02
87f98b5d837465449112f5a47b5da4b0c1e14098aa4c3154833e3f540d0334a1
testweb03
73b92a0f3588c71345788f38a2386c2d98abe3177e372f045f0b5d880ed01bd7
testweb04
1d40fff204027b2dc3b5084a7acc39b06e5ddf66c1a77e523a4aeb6e427ff72d
testweb05
3c97f0ee23a05b659933db0aa3e8bb2fd4b2347a3831dbff08285ea1b1230e10
testweb06
41ab1c192f743f99742558a4d0423a23ffc79665f7fc88139f92ab849b93c01d
testweb07
1f04291534d4395871a3bb558aab666c52a50bfa00d27c1d73399174ba999014
testweb08
3c03da4b31b5f6fc031e4c75a50b9eebfd8a54ac0ef47879a061c1816be7b1a9
testweb09
38bb1c9120b41a5380d6e3f4be9a199ee1c32b0dfa7fdda8d2fa45141c5af6bb
$
```

![Test Image 1](https://raw.githubusercontent.com/mayumi00/katacoda-scenarios/main/container102/images/image203.png)


`for i in {1..9} ; do echo testweb0${i} ; curl http://localhost:808${i} ; done`{{execute}}

```text
<head><title>Apache on Docker Container</title></head><body><H1>Container 102 - Web</H1>Apache on Docker Container using Dockerfile</body>
<head><title>Apache on Docker Container</title></head><body><H1>Container 102 - Web</H1>Apache on Docker Container using Dockerfile</body>
<head><title>Apache on Docker Container</title></head><body><H1>Container 102 - Web</H1>Apache on Docker Container using Dockerfile</body>
<head><title>Apache on Docker Container</title></head><body><H1>Container 102 - Web</H1>Apache on Docker Container using Dockerfile</body>
<head><title>Apache on Docker Container</title></head><body><H1>Container 102 - Web</H1>Apache on Docker Container using Dockerfile</body>
<head><title>Apache on Docker Container</title></head><body><H1>Container 102 - Web</H1>Apache on Docker Container using Dockerfile</body>
<head><title>Apache on Docker Container</title></head><body><H1>Container 102 - Web</H1>Apache on Docker Container using Dockerfile</body>
<head><title>Apache on Docker Container</title></head><body><H1>Container 102 - Web</H1>Apache on Docker Container using Dockerfile</body>
```

`docker ps -a`{{execute}}

```text
$ docker ps -a
CONTAINER ID   IMAGE                  COMMAND                  CREATED             STATUS             PORTS                                   NAMES
38bb1c9120b4   apacheweb-dockerfile:1.0   "/usr/sbin/httpd -DF…"   6 minutes ago       Up 6 minutes       0.0.0.0:8089->80/tcp, :::8089->80/tcp   testweb09
3c03da4b31b5   apacheweb-dockerfile:1.0   "/usr/sbin/httpd -DF…"   6 minutes ago       Up 6 minutes       0.0.0.0:8088->80/tcp, :::8088->80/tcp   testweb08
1f04291534d4   apacheweb-dockerfile:1.0   "/usr/sbin/httpd -DF…"   6 minutes ago       Up 6 minutes       0.0.0.0:8087->80/tcp, :::8087->80/tcp   testweb07
41ab1c192f74   apacheweb-dockerfile:1.0   "/usr/sbin/httpd -DF…"   6 minutes ago       Up 6 minutes       0.0.0.0:8086->80/tcp, :::8086->80/tcp   testweb06
3c97f0ee23a0   apacheweb-dockerfile:1.0   "/usr/sbin/httpd -DF…"   6 minutes ago       Up 6 minutes       0.0.0.0:8085->80/tcp, :::8085->80/tcp   testweb05
1d40fff20402   apacheweb-dockerfile:1.0   "/usr/sbin/httpd -DF…"   6 minutes ago       Up 6 minutes       0.0.0.0:8084->80/tcp, :::8084->80/tcp   testweb04
73b92a0f3588   apacheweb-dockerfile:1.0   "/usr/sbin/httpd -DF…"   6 minutes ago       Up 6 minutes       0.0.0.0:8083->80/tcp, :::8083->80/tcp   testweb03
87f98b5d8374   apacheweb-dockerfile:1.0   "/usr/sbin/httpd -DF…"   6 minutes ago       Up 6 minutes       0.0.0.0:8082->80/tcp, :::8082->80/tcp   testweb02
0249c56bd715   apacheweb-dockerfile:1.0   "/usr/sbin/httpd -DF…"   About an hour ago   Up About an hour   0.0.0.0:8080->80/tcp, :::8080->80/tcp   testweb01
```

次のステップに進む前に、不要なコンテナを削除します。停止→削除という流れになります。

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
CONTAINER ID   IMAGE                         COMMAND                  CREATED        STATUS                      PORTS                  NAMES
2cd97d3f908b   apacheweb:v1.0                "/bin/bash"              47 hours ago   Exited (0) 47 hours ago                            testweb10
8d6cda605ee7   httpd:latest                  "httpd-foreground"       2 days ago     Up 2 days                   0.0.0.0:80->80/tcp     httpd
41fa697c9b50   hello-world                   "/hello"                 2 days ago     Exited (0) 2 days ago                              stoic_goldwasser
d7ec0a8b9812   apacheweb-dockerfile:1.0:latest   "/usr/sbin/httpd -DF…"   2 days ago     Exited (0) 11 seconds ago                          testweb09
25edfe178f68   apacheweb-dockerfile:1.0:latest   "/usr/sbin/httpd -DF…"   2 days ago     Exited (0) 11 seconds ago                          testweb08
70a73dbea6d4   apacheweb-dockerfile:1.0:latest   "/usr/sbin/httpd -DF…"   2 days ago     Exited (0) 12 seconds ago                          testweb07
51af578d9295   apacheweb-dockerfile:1.0:latest   "/usr/sbin/httpd -DF…"   2 days ago     Exited (0) 12 seconds ago                          testweb06
fa6d86813e94   apacheweb-dockerfile:1.0:latest   "/usr/sbin/httpd -DF…"   2 days ago     Exited (0) 12 seconds ago                          testweb05
ee9be191c9bb   apacheweb-dockerfile:1.0:latest   "/usr/sbin/httpd -DF…"   2 days ago     Exited (0) 13 seconds ago                          testweb04
6a9b5d2bc978   apacheweb-dockerfile:1.0:latest   "/usr/sbin/httpd -DF…"   2 days ago     Exited (0) 14 seconds ago                          testweb03
47762d8d669d   apacheweb-dockerfile:1.0:latest   "/usr/sbin/httpd -DF…"   2 days ago     Created                                            testweb02
d108c4296367   apacheweb-dockerfile:1.0:latest   "-it /bin/bash"          3 days ago     Created                                            testtest
cded4b21dcad   apacheweb-dockerfile:1.0:latest   "/usr/sbin/httpd -DF…"   3 days ago     Exited (0) 14 seconds ago                          testweb01
2bb6bc5699d6   apacheweb:v1.0                "/bin/bash"              5 days ago     Up 5 days (Paused)          0.0.0.0:8081->80/tcp   testweb2
f29637beaac6   centos                        "/bin/bash"              5 days ago     Up 5 days                   0.0.0.0:8080->80/tcp   mycentos2
```

停止したtestweb01からtestweb09を削除します。

`for i in {2..9} ; do docker rm testweb0${i} ; done`{{execute}}

```text
[root@ik1-314-17333 ~]# for i in {2..9} ; do docker rm testweb0${i} ; done
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
[root@ik1-314-17333 ~]# docker ps -a
CONTAINER ID   IMAGE                         COMMAND                  CREATED        STATUS                     PORTS                  NAMES
2cd97d3f908b   apacheweb:v1.0                "/bin/bash"              47 hours ago   Exited (0) 47 hours ago                           testweb10
8d6cda605ee7   httpd:latest                  "httpd-foreground"       2 days ago     Up 2 days                  0.0.0.0:80->80/tcp     httpd
41fa697c9b50   hello-world                   "/hello"                 2 days ago     Exited (0) 2 days ago                             stoic_goldwasser
d108c4296367   apacheweb-dockerfile:1.0:latest   "-it /bin/bash"          3 days ago     Created                                           testtest
cded4b21dcad   apacheweb-dockerfile:1.0:latest   "/usr/sbin/httpd -DF…"   3 days ago     Exited (0) 3 minutes ago                          testweb01
2bb6bc5699d6   apacheweb:v1.0                "/bin/bash"              5 days ago     Up 5 days (Paused)         0.0.0.0:8081->80/tcp   testweb2
f29637beaac6   centos                        "/bin/bash"              5 days ago     Up 5 days                  0.0.0.0:8080->80/tcp   mycentos2
```
現在は以下の図のような状態です。

![Test Image 1](https://raw.githubusercontent.com/mayumi00/katacoda-scenarios/main/container102/images/image204.png)
