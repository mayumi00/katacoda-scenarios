## コンテナのステータス

step3でhttpd（Apache HTTP Server）がインストールされたコンテナを作成しました。同じようなコンテナを生成する際に、同じようなステップを踏むのは手間になるので、このhttpdインストール済のコンテナを元にコンテナイメージを作成してみましょう。コンテナイメージは稼働中または停止中のコンテナいずれからも作成できます。

コンテナの起動状態確認を確認します。

`docker ps -a`{{execute}}

コンテナは起動した状態です。

```text
$ docker ps -a
CONTAINER ID   IMAGE     COMMAND       CREATED         STATUS          PORTS                                   NAMES
7ec897a6ca6c   centos    "/bin/bash"   2 minutes ago   Up 38 seconds   0.0.0.0:8080->80/tcp, :::8080->80/tcp   mycentos02
```

コンテナイメージを作成します。元になるコンテナ名と、作成するイメージ名とタグを指定します。ここでは1.0というタグを付けました。

`docker commit mycentos02 apacheweb:1.0`{{execute}}

```text
[root@ik1-314-17333 ~]# docker commit mycentos02 apacheweb:1.0
sha256:8f6df70219036bb1341eb156ccbc43f96ad1a533f5ca60a4ab530544f15bebc4
```

イメージができたか確認しましょう

`docker images`{{execute}}

1.0のタグがついたapachewebが作成されていることが確認できます。

```text
[root@ik1-314-17333 ~]# docker images
REPOSITORY    TAG       IMAGE ID       CREATED          SIZE
apacheweb     v1.0      8f6df7021903   19 seconds ago   277MB
hello-world   latest    feb5d9fea6a5   2 months ago     13.3kB
centos        latest    5d0da3dc9764   3 months ago     231MB
```

![Test Image 1](https://raw.githubusercontent.com/mayumi00/katacoda-scenarios/main/container101/images/image06.png)　

作成したapachewebイメージを元にコンテナを起動します。作成したapachewebイメージにはタグを付けていたので、タグ（:1.0）も指定します。タグを指定しないとデフォルトでlatestを探そうとするのでイメージを見つけられずに「docker: Error response from daemon」のようなエラーが返ってきます。`-d（or --detach）オプション` はコンテナをバックグラウンドで実行し、コンテナIDを出力するものです。前のステップでローカルホストの8080番は既に割当済で使用中なので、8081番にフォワードしてます。

`docker run -d -p 8081:80 -it --name testweb2 apacheweb:1.0 /bin/bash`{{execute}}

コンテナが生成され、コンテナIDが出力されました。

```text
[root@ik1-314-17333 ~]# docker run -d -p 8081:80 -it --name testweb2 apacheweb:1.0 /bin/bash
2bb6bc5699d6017a07302a1068c7f9f87c18c1c9d02d9f435083286e49e50401
```

![Test Image 1](https://raw.githubusercontent.com/mayumi00/katacoda-scenarios/main/container101/images/image07.png)

execコマンドを利用してbashの利用を可能にします。

`docker exec -it testweb2 /bin/bash`{{execute}}

コンテナ内のプロセスを確認します。

`ps f -ef`{{execute}}

そもそも、作成したコンテナイメージは、httpdインストール済だがhttpdを自動起動する設定無しのコンテナを元にしているので、httpdは自動起動しません。

```text
[root@2bb6bc5699d6 /]# ps f -ef
    PID TTY      STAT   TIME COMMAND
     15 pts/1    Ss     0:00 /bin/bash
     29 pts/1    R+     0:00  \_ ps f -ef
      1 pts/0    Ss+    0:00 /bin/bash
```

手動でhttpdを起動します。

`/usr/sbin/httpd`{{execute}}

コンテナ内のプロセスを確認します。

`ps f -ef`{{execute}}

```text
[root@2bb6bc5699d6 /]# ps f -ef
    PID TTY      STAT   TIME COMMAND
     15 pts/1    Ss     0:00 /bin/bash
    247 pts/1    R+     0:00  \_ ps f -ef
      1 pts/0    Ss+    0:00 /bin/bash
     31 ?        Ss     0:00 /usr/sbin/httpd
     32 ?        S      0:00  \_ /usr/sbin/httpd
     33 ?        Sl     0:00  \_ /usr/sbin/httpd
     34 ?        Sl     0:00  \_ /usr/sbin/httpd
     35 ?        Sl     0:00  \_ /usr/sbin/httpd
```

httpdが起動したので、コンテナから抜けます。

`exit`{{execute}}

コンテナの一覧を表示します。

`docker ps -a`{{execute}}

```text
[root@ik1-314-17333 ~]# docker ps -a
CONTAINER ID   IMAGE            COMMAND       CREATED          STATUS          PORTS                  NAMES
2bb6bc5699d6   apacheweb:1.0   "/bin/bash"   2 minutes ago    Up 2 minutes    0.0.0.0:8081->80/tcp   testweb2
f29637beaac6   centos           "/bin/bash"   39 minutes ago   Up 36 minutes   0.0.0.0:8080->80/tcp   mycentos02
```

8081番で待ち受けしていることがわかります。curlでの確認とブラウザでの確認を行います。

`curl http://localhost:8081/`{{execute}}

```text
[root@ik1-314-17333 ~]# curl http://localhost:8081/
<head><title>Apache on Docker Container</title></head><body><H1>Container 101 - Web</H1>Apache on Docker Container</body>
```
https://[[HOST_SUBDOMAIN]]-8081-[[KATACODA_HOST]].environments.katacoda.com/

![Test Image 1](https://raw.githubusercontent.com/mayumi00/katacoda-scenarios/main/container101/images/image08.png)

なんとなくコンテナの起動やコンテナ内の操作がわかってきたと思いますが、まだ手作業で行っている事が多い印象です。次のコースで、下記のような作業を、コマンドをちまちま打たずに済ませるために、DockerFileというもの使う方法を学習します。

- ベースとなるCentOSコンテナイメージを取得する
- CentOSにhttpd（Apache HTTP Server）をインストールする
- httpd（Apache HTTP Server）の設定を行う
- ドキュメントルート配下にコンテンツを配置する
- httpdを起動する
- コンテナに特定のポートを割り当てて、かつ、バックグラウンドで起動する

##  このステップで利用したdockerコマンド

- docker commit [オプション] CONTAINER [REPOSITORY[:TAG]]
  - コンテナをからイメージを作成する
 - docker run -dオプション
   - コンテナをバックグラウンドで起動する
 
