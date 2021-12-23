# コンテナイメージの作成

![Test Image 1](https://raw.githubusercontent.com/mayumi00/katacoda-scenarios/main/container101/images/image01.png)　

ステップ３でhttpdがインストールされたコンテナを作成しました。このコンテナを元に、コンテナイメージを作成します。イメージは稼働中または停止中のコンテナいずれからも作成できます。

コンテナの起動状態確認を確認します。

`docker ps -a`{{execute}}

```text
$ docker ps -a
CONTAINER ID   IMAGE     COMMAND       CREATED         STATUS          PORTS                                   NAMES
7ec897a6ca6c   centos    "/bin/bash"   2 minutes ago   Up 38 seconds   0.0.0.0:8080->80/tcp, :::8080->80/tcp   mycentos2
```

`docker commit mycentos2 apacheweb:v1.0`{{execute}}

```text
[root@ik1-314-17333 ~]# docker commit mycentos2 apacheweb:v1.0
sha256:8f6df70219036bb1341eb156ccbc43f96ad1a533f5ca60a4ab530544f15bebc4
```

イメージができたか確認しましょう

`docker images`{{execute}}

```text
[root@ik1-314-17333 ~]# docker images
REPOSITORY    TAG       IMAGE ID       CREATED          SIZE
apacheweb     v1.0      8f6df7021903   19 seconds ago   277MB
hello-world   latest    feb5d9fea6a5   2 months ago     13.3kB
centos        latest    5d0da3dc9764   3 months ago     231MB
```

作成したイメージを元にコンテナを起動します。作成したコンテナにはタグを付けていたので、タグ（:v1.0）も指定します。タグを指定しないとデフォルトでlatestを探そうとするのでイメージを見つけられずに「docker: Error response from daemon」のようなエラーが返ってきます。`-d（or --detach）オプション` 

`docker run -d -p 8081:80 -it --name testweb2 apacheweb:v1.0 /bin/bash`{{execute}}

```text
[root@ik1-314-17333 ~]# docker run -d -p 8081:80 -it --name testweb2 apacheweb:v1.0 /bin/bash
2bb6bc5699d6017a07302a1068c7f9f87c18c1c9d02d9f435083286e49e50401
```


`curl http://localhost:8081/`{{execute}}

```text
curl: (56) Recv failure: Connection reset by peer
```

`docker exec -it testweb2 /bin/bash`{{execute}}
`ps f -e`{{execute}}
```text
[root@2bb6bc5699d6 /]# ps f -e
    PID TTY      STAT   TIME COMMAND
     15 pts/1    Ss     0:00 /bin/bash
     29 pts/1    R+     0:00  \_ ps f -e
      1 pts/0    Ss+    0:00 /bin/bash
```
`/usr/sbin/httpd`{{execute}}
`ps f -e`{{execute}}
```text
[root@2bb6bc5699d6 /]# ps f -e
    PID TTY      STAT   TIME COMMAND
     15 pts/1    Ss     0:00 /bin/bash
    247 pts/1    R+     0:00  \_ ps f -e
      1 pts/0    Ss+    0:00 /bin/bash
     31 ?        Ss     0:00 /usr/sbin/httpd
     32 ?        S      0:00  \_ /usr/sbin/httpd
     33 ?        Sl     0:00  \_ /usr/sbin/httpd
     34 ?        Sl     0:00  \_ /usr/sbin/httpd
     35 ?        Sl     0:00  \_ /usr/sbin/httpd
```

`exit`{{execute}}

`docker ps -a`{{execute}}

```text
[root@ik1-314-17333 ~]# docker ps -a
CONTAINER ID   IMAGE            COMMAND       CREATED          STATUS          PORTS                  NAMES
2bb6bc5699d6   apacheweb:v1.0   "/bin/bash"   2 minutes ago    Up 2 minutes    0.0.0.0:8081->80/tcp   testweb2
f29637beaac6   centos           "/bin/bash"   39 minutes ago   Up 36 minutes   0.0.0.0:8080->80/tcp   mycentos2
```

`curl http://localhost:8081/`{{execute}}

```text
[root@ik1-314-17333 ~]# curl http://localhost:8081/
<head><title>Apache on Docker Container</title></head><body><H1>Container 101 - Web</H1>Apache on Docker Container</body>
```

`ps f -e`{{execute}}
```text
[root@701ae92de4a9 /]# ps f -e
    PID TTY      STAT   TIME COMMAND
      1 pts/0    Ss     0:00 /bin/bash
     16 pts/0    R+     0:00 ps f -e
```

https://[[HOST_SUBDOMAIN]]-8081-[[KATACODA_HOST]].environments.katacoda.com/


なんとなくコンテナの起動やコンテナ内の操作がわかってきたと思いますが、手作業で行っている事が多い印象です。
やりたいことは
- ベースとなるCentOSイメージを取得する
- CentOSにアプリケーション（httpd）をインストールする
- httpd.confの設定
- ドキュメントルート配下にコンテンツを配置する
- httpdの起動
このように生成したコンテナを特定のポートを割り当てて、かつ、バックグラウンドで起動する
ということをコマンドをちまちま打たずに済ませたい、そのためのDockerFileというものを次のコースで学習します。


##  このステップで利用したdockerコマンド
- docker commit [オプション] CONTAINER [REPOSITORY[:TAG]]
  - コンテナをからイメージを作成する
 - docker run -dオプション
   - コンテナをバックグラウンドで起動する
 
