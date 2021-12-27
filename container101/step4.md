## コンテナイメージの作成

step3でhttpd（Apache HTTP Server）がインストールされたコンテナを作成しました。同じようなコンテナを生成する際に、同じようなステップを踏むのは手間になるので、このhttpdインストール済のコンテナを元にコンテナイメージを作成してみましょう。コンテナイメージは稼働中または停止中のコンテナいずれからも作成できます。

コンテナの起動状態確認を確認します。

`docker ps -a`{{execute}}

コンテナは起動した状態です。

```text
$ docker ps -a
CONTAINER ID   IMAGE          COMMAND              CREATED          STATUS                      PORTS                                   NAMES
1af9407b90df   centos         "/bin/bash"          2 minutes ago    Up About a minute           0.0.0.0:8080->80/tcp, :::8080->80/tcp   mycentos02
7b1d77ed91f8   centos         "/bin/bash"          5 minutes ago    Up 3 minutes                                                        mycentos01
694ebd43af1d   httpd:latest   "httpd-foreground"   9 minutes ago    Up 9 minutes                0.0.0.0:80->80/tcp, :::80->80/tcp       httpd
e14e3cbf21be   hello-world    "/hello"             10 minutes ago   Exited (0) 10 minutes ago                                           musing_shamir

```

---
**コンテナからのコンテナイメージの作成**

![Test Image 1](https://raw.githubusercontent.com/mayumi00/katacoda-scenarios/main/container101/images/image4-1.png)　

`docker commit`コマンドを使うと、コンテナからコンテナイメージを作成できます。コマンド実行時に、元になるコンテナ名と、作成するイメージ名とタグを指定します。ここでは1.0というタグを付けました。

`docker commit mycentos02 apacheweb:1.0`{{execute}}

```text
$ docker commit mycentos02 apacheweb:1.0
sha256:3518d1945326dad229c9987dd8022bed7197064538e8a6db96493bffca8397fe
```

イメージができたか確認しましょう。

`docker images`{{execute}}

1.0のタグがついたapachewebが作成されていることが確認できます。

```text
$ docker images
REPOSITORY         TAG       IMAGE ID       CREATED         SIZE
apacheweb          1.0       3518d1945326   6 seconds ago   278MB
httpd              latest    dabbfbe0c57b   6 days ago      144MB
（略）
hello-world        latest    feb5d9fea6a5   3 months ago    13.3kB
centos             latest    5d0da3dc9764   3 months ago    231MB
alpine             latest    14119a10abf4   4 months ago    5.59MB
weaveworks/scope   1.11.4    a082d48f0b39   2 years ago     78.5MB
```

---
**コンテナイメージからのコンテナの起動**

作成したapachewebイメージを元にコンテナを起動します。作成したapachewebイメージにはタグを付けていたので、タグ（:1.0）も指定します。タグを指定しないとデフォルトでlatestを探そうとするのでイメージを見つけられずに「docker: Error response from daemon」のようなエラーが返ってきます。`-d（or --detach）オプション` はコンテナをバックグラウンドで実行し、コンテナIDを出力するものです。前のステップでローカルホストの8080番は既に割当済で使用中なので、8081番にバインドしてます。

![Test Image 1](https://raw.githubusercontent.com/mayumi00/katacoda-scenarios/main/container101/images/image4-2.png)

`docker run -d -p 8081:80 -it --name testweb2 apacheweb:1.0 /bin/bash`{{execute}}

コンテナが生成され、コンテナIDが出力されました。

```text
$ docker run -d -p 8081:80 -it --name testweb2 apacheweb:1.0 /bin/bash
e11268340a162720529606f64c7a2102b06d4132bfaa8853db7014e48a137e47
```

execコマンドを利用してbashの利用を可能にします。

`docker exec -it testweb2 /bin/bash`{{execute}}

コンテナ内のプロセスを確認します。

`ps f -e`{{execute}}

そもそも、作成したコンテナイメージは、httpdインストール済ですがhttpdを自動起動する設定が無いのコンテナを元にしているので、httpdは自動起動しません。

```text
[root@e11268340a16 /]# ps f -e
    PID TTY      STAT   TIME COMMAND
     15 pts/1    Ss     0:00 /bin/bash
     30 pts/1    R+     0:00  \_ ps f -e
      1 pts/0    Ss+    0:00 /bin/bash
```

手動でhttpdを起動します。

`/usr/sbin/httpd`{{execute}}

コンテナ内のプロセスを確認します。

`ps f -e`{{execute}}

```text
[root@e11268340a16 /]# ps f -e
    PID TTY      STAT   TIME COMMAND
     15 pts/1    Ss     0:00 /bin/bash
    248 pts/1    R+     0:00  \_ ps f -e
      1 pts/0    Ss+    0:00 /bin/bash
     32 ?        Ss     0:00 /usr/sbin/httpd
     33 ?        S      0:00  \_ /usr/sbin/httpd
     34 ?        Sl     0:00  \_ /usr/sbin/httpd
     35 ?        Sl     0:00  \_ /usr/sbin/httpd
     36 ?        Sl     0:00  \_ /usr/sbin/httpd
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
e11268340a16   apacheweb:1.0   "/bin/bash"          45 seconds ago   Up 43 seconds               0.0.0.0:8081->80/tcp, :::8081->80/tcp   testweb2
1af9407b90df   centos          "/bin/bash"          3 minutes ago    Up 2 minutes                0.0.0.0:8080->80/tcp, :::8080->80/tcp   mycentos02
7b1d77ed91f8   centos          "/bin/bash"          7 minutes ago    Up 5 minutes                                                        mycentos01
694ebd43af1d   httpd:latest    "httpd-foreground"   10 minutes ago   Up 10 minutes               0.0.0.0:80->80/tcp, :::80->80/tcp       httpd
e14e3cbf21be   hello-world     "/hello"             12 minutes ago   Exited (0) 12 minutes ago                                           musing_shamir
```

8081番で待ち受けしていることがわかります。curlでの確認とブラウザでの確認を行います。

`curl http://localhost:8081/`{{execute}}

```text
[root@ik1-314-17333 ~]# curl http://localhost:8081/
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
 
