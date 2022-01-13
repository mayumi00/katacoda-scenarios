## コンテナイメージの作成

step3でhttpd（Apache HTTP Server）がインストールされたコンテナを作成しました。同じようなコンテナを生成する際に、同じようなステップを踏むのは手間になるので、このhttpdインストール済のmycentos02コンテナを元にコンテナイメージを作成してみましょう。コンテナイメージは稼働中または停止中のコンテナいずれからも作成できます。

コンテナの起動状態確認を確認します。

`docker ps -a`{{execute}}

mycentos02コンテナは起動した状態です。

```text
$ docker ps -a
CONTAINER ID   IMAGE          COMMAND              CREATED              STATUS                      PORTS                                   NAMES
fb322537fce3   centos         "/bin/bash"          About a minute ago   Up 29 seconds               0.0.0.0:8080->80/tcp, :::8080->80/tcp   mycentos02
7b51274d250c   centos         "/bin/bash"          3 minutes ago        Up About a minute                                                   mycentos01
da36e29f033d   httpd:latest   "httpd-foreground"   12 minutes ago       Up 12 minutes               0.0.0.0:80->80/tcp, :::80->80/tcp       httpd
b157d4669472   hello-world    "/hello"             13 minutes ago       Exited (0) 13 minutes ago                                           trusting_shamir
```

---
**コンテナからのコンテナイメージの作成**

![Test Image 1](https://raw.githubusercontent.com/mayumi00/katacoda-scenarios/main/container101/images/image4-1.png)　

`docker commit`コマンドを使うと、コンテナからコンテナイメージを作成できます。コマンド実行時に、元になるコンテナ名と、作成するイメージ名とタグを指定します。ここでは1.0というタグを付けました。

`docker commit mycentos02 apacheweb:1.0`{{execute}}

```text
$ docker commit mycentos02 apacheweb:1.0
sha256:a4bc75a2a7947220ebc6de5091fdba02edd4b14d7fa32b5c0c5a304341673bd6
```

イメージができたか確認しましょう。

`docker images`{{execute}}

1.0のタグがついたapachewebが作成されていることが確認できます。

```text
$ docker images
REPOSITORY         TAG       IMAGE ID       CREATED         SIZE
apacheweb          1.0       a4bc75a2a794   2 seconds ago   280MB
httpd              latest    dabbfbe0c57b   2 weeks ago     144MB
（略）
hello-world        latest    feb5d9fea6a5   3 months ago    13.3kB
centos             latest    5d0da3dc9764   3 months ago    231MB
alpine             latest    14119a10abf4   4 months ago    5.59MB
weaveworks/scope   1.11.4    a082d48f0b39   2 years ago     78.5MB
```

---
**コンテナイメージからのコンテナの起動**

作成したapachewebイメージを元にコンテナを起動します。作成したapachewebイメージにはタグを付けていたので、タグ（:1.0）も指定します。タグを指定しないとデフォルトでlatestを探そうとするのでイメージを見つけられずに「docker: Error response from daemon」のようなエラーが返ってきます。`-d（or --detach）オプション` はコンテナをバックグラウンドで実行し、コンテナIDを出力するものです。前のステップでローカルホストの8080番は既に割当済で使用中なので、8081番にフォワードしてます。

![Test Image 1](https://raw.githubusercontent.com/mayumi00/katacoda-scenarios/main/container101/images/image4-2.png)

`docker run -d -p 8081:80 -it --name testweb2 apacheweb:1.0 /bin/bash`{{execute}}

コンテナが生成され、コンテナIDが出力されました。

```text
$ docker run -d -p 8081:80 -it --name testweb2 apacheweb:1.0 /bin/bash
331b468aebaf72998783af7ce0d2c7cb46c04ed9cdf14305fdc58fa4b88e2252
```

execコマンドを利用してbashの利用を可能にします。

`docker exec -it testweb2 /bin/bash`{{execute}}

コンテナ内のプロセスを確認します。

`ps f -ef`{{execute}}

そもそも、作成したコンテナイメージは、httpdインストール済ですがhttpdを自動起動する設定が無いのコンテナを元にしているので、httpdは自動起動しません。

```text
[root@331b468aebaf /]# ps f -ef
    PID TTY      STAT   TIME COMMAND
     16 pts/1    Ss     0:00 /bin/bash
     31 pts/1    R+     0:00  \_ ps f -ef
      1 pts/0    Ss+    0:00 /bin/bash
```

手動でhttpdを起動します。

`/usr/sbin/httpd`{{execute}}

コンテナ内のプロセスを確認します。

`ps f -ef`{{execute}}

```text
[root@331b468aebaf /]# ps f -ef
    PID TTY      STAT   TIME COMMAND
     16 pts/1    Ss     0:00 /bin/bash
    249 pts/1    R+     0:00  \_ ps f -ef
      1 pts/0    Ss+    0:00 /bin/bash
     33 ?        Ss     0:00 /usr/sbin/httpd
     34 ?        S      0:00  \_ /usr/sbin/httpd
     35 ?        Sl     0:00  \_ /usr/sbin/httpd
     36 ?        Sl     0:00  \_ /usr/sbin/httpd
     38 ?        Sl     0:00  \_ /usr/sbin/httpd
```

httpdが起動したので、コンテナから抜けます。

`exit`{{execute}}

---
**コンテナの動作確認**

![Test Image 1](https://raw.githubusercontent.com/mayumi00/katacoda-scenarios/main/container101/images/image4-3.png)

コンテナの一覧を表示します。

`docker ps -a`{{execute}}

```text
$ docker ps -a
CONTAINER ID   IMAGE           COMMAND              CREATED          STATUS                      PORTS                                   NAMES
331b468aebaf   apacheweb:1.0   "/bin/bash"          24 seconds ago   Up 23 seconds               0.0.0.0:8081->80/tcp, :::8081->80/tcp   testweb2
fb322537fce3   centos          "/bin/bash"          2 minutes ago    Up About a minute           0.0.0.0:8080->80/tcp, :::8080->80/tcp   mycentos02
7b51274d250c   centos          "/bin/bash"          4 minutes ago    Up 2 minutes                                                        mycentos01
da36e29f033d   httpd:latest    "httpd-foreground"   13 minutes ago   Up 13 minutes               0.0.0.0:80->80/tcp, :::80->80/tcp       httpd
b157d4669472   hello-world     "/hello"             14 minutes ago   Exited (0) 14 minutes ago                                           trusting_shamir
```

8081番で待ち受けしていることがわかります。curlでの確認とブラウザでの確認を行います。

`curl http://localhost:8081/`{{execute}}

```text
$ curl http://localhost:8081/
<head><title>Apache on Docker Container</title></head><body><H1>Container 101 - Web</H1>Apache on Docker Container</body>
```

ブラウザで確認する場合は以下をクリックしてください。index.htmlの内容が表示されます

https://[[HOST_SUBDOMAIN]]-8081-[[KATACODA_HOST]].environments.katacoda.com/

 ![Test Image 1](https://raw.githubusercontent.com/mayumi00/katacoda-scenarios/main/container101/images/image101web2.png)

なんとなくコンテナの起動やコンテナ内の操作がわかってきたと思いますが、まだ手作業で行っている事が多い印象です。次のコースで、下記のような作業を、コマンドをちまちま打たずに済ませるために、DockerFileというもの使う方法を学習します。

- ベースとなるCentOSコンテナイメージを取得する
- CentOSにhttpd（Apache HTTP Server）をインストールする
- httpd（Apache HTTP Server）の設定を行う
- ドキュメントルート配下にコンテンツを配置する
- httpdを起動する
- コンテナに特定のポートを割り当てて、かつ、バックグラウンドで起動する

---
###  このステップで利用したdockerコマンド

- docker commit [OPTIONS] CONTAINER [REPOSITORY[:TAG]]
  - コンテナをからイメージを作成する
 
