## Dockerfileを利用してのコンテナイメージのビルド

この演習環境は下図のようになっています。図の右側の「コンテナを稼働させるホスト（自ホスト）」が、このコースの左側に表示されているTerminalに対応しています。

![Test Image 1](https://raw.githubusercontent.com/mayumi00/katacoda-scenarios/main/container101/images/image1-1.png)　

この環境は既にDockerが利用できる状態になっていますので、Dockerを利用してコンテナを起動して、動作を確認してみましょう。`docker version`{{execute}} ←このように表示されている部分をクリックすると右のTerminalでコマンドが実行されます。

このステップではDockerfileを利用してコンテナイメージをビルドすることを学習します。

Dockerfileを利用すると以下のような処理を行うイメージを作成することができます。

- ベースとなるCentOSコンテナイメージを取得する
- CentOSにhttpd（Apache HTTP Server）をインストールする
- httpd（Apache HTTP Server）の設定を行う
- ドキュメントルート配下にコンテンツを配置する
- httpdを起動する

---
**コンテンツの作成**

まずは自ホスト内に、Webのコンテンツとなるindex.htmlを作成しておきます。Container 101の演習では、コンテナ内でindex.htmlを作成しましたが、この演習では自ホストに置かれたHTMLファイルをコンテナにコピーして使う方法を取ります。

`echo "<head><title>Apache on Docker Container</title></head><body><H1>Container 102 - Web</H1>Apache on Docker Container using Dockerfile</body>"  > index.html `{{execute}}

---
**Dockerfileの作成**

Dockerfileに行いたい処理を記述します。

> Note: 今回はEditorを利用せずechoコマンドでファイルを作成します。

***FROM*** 

`FROM centos`

`echo "FROM centos"  > Dockerfile `{{execute}}

`FROM`は、ベースとなるコンテナイメージを指定します。コンテナイメージが自ホストにない場合は自動的にコンテナレジストリからダウンロードします。Dockerfileは基本的にFROMから始める必要があります。

***RUN*** 

`RUN dnf install -y httpd`

`echo "RUN dnf install -y httpd"  >> Dockerfile `{{execute}}

`RUN sed -i -e "s/#ServerName www.example.com/ServerName localhost/" /etc/httpd/conf/httpd.conf`

`echo "RUN sed -i -e \"s/#ServerName www.example.com/ServerName localhost/\" /etc/httpd/conf/httpd.conf"  >> Dockerfile `{{execute}}

`RUN`は`FROM`で指定したコンテナイイメージに対してコマンドを実行します。`RUN`は複数使用可能です。`RUN`にはshell形式とexec形式があり、この例ではshell形式で記述しています。httpdのインストールとhttpd.confの設定を行っています。

***COPY*** 

`COPY index.html /var/www/html/index.html`

`echo "COPY index.html /var/www/html/index.html"  >> Dockerfile `{{execute}}

`COPY`は、sourceからファイルやディレクトリをコピーして、コンテナ内のファイルシステムのパス destinationに追加します。この例では、自ホストの現在のディレクトリにあるindex.htmlをファイルをコンテナイメージ内の/var/www/html/index.htmlにコピーしています。

***CMD*** 

`CMD ["/usr/sbin/httpd","-DFOREGROUND"]`

`echo "CMD [\"/usr/sbin/httpd\",\"-DFOREGROUND\"]" >> Dockerfile `{{execute}}

`CMD`はコンテナが起動する際に実行するコマンドを指定します。記述方法はexec形式、shell形式、ENTRYPOINTのデフォルトパラメーターの３種類があります。Dockerfileでは`CMD`は１つしか記述できません。もし複数の`CMD`があった場合は、最後の`CMD`しか処理されません。記述方式はexec形式が推奨されており、[]内に、["コマンド","パラメータ1","パラメータ2"]のように記載します。この例では、/usr/sbin/httpdコマンドにパラメータ -DFOREGROUNDを指定してフォアグラウンドで実行します。

作成したDockerfileの内容を確認します。

`cat Dockerfile `{{execute}}

以下の内容になっていればOKです。

```text
FROM centos
RUN dnf install -y httpd
RUN sed -i -e "s/#ServerName www.example.com/ServerName localhost/" /etc/httpd/conf/httpd.conf
COPY index.html /var/www/html/index.html
CMD ["/usr/sbin/httpd","-DFOREGROUND"]
```

Dockerfileの記載方法はDockerfile リファレンスを参照してください。
https://docs.docker.com/engine/reference/builder/

---
**コンテナイメージのビルド**

![Test Image 1](https://raw.githubusercontent.com/mayumi00/katacoda-scenarios/main/container102/images/image1-1.png)

Dockerfileが作成できたので、これを利用してコンテナイメージをビルドします。`-t（or --tag）オプション` で名前およびタグを指定します。以下の例ではDockerfileを指定してないように見えますが、デフォルトで指定したPATHにあるDockerfileを使うので、「PATH .（現在のディレクトリ）にあるDockerfileを使用する」という意味になります。もし、他のファイルを利用する場合は`-f（or --file）オプション` で明示的に指定します。この例ではapacheweb-dockerfile:1.0という名前:タグでイメージをビルドします。

> Note: 途中で「warning: /var/cache/dnf」というwarningが出ることがありますが、今回は無視してください。

`docker build -t apacheweb-dockerfile:1.0 .`{{execute}}
 
```text
$ docker build -t apacheweb-dockerfile:1.0 .
Sending build context to Docker daemon  1.602MB
Step 1/5 : FROM centos
latest: Pulling from library/centos
a1d0c7532777: Downloading [==================================================>]  83.52MB/83.52MB
latest: Pulling from library/centos
a1d0c7532777: Pull complete 
Digest: sha256:a27fd8080b517143cbbbab9dfb7c8571c40d67d534bbdee55bd6c473f432b177
Status: Downloaded newer image for centos:latest
 ---> 5d0da3dc9764
Step 2/5 : RUN dnf install -y httpd
 ---> Running in 6ba7745852dd
CentOS Linux 8 - AppStream                       41 MB/s | 8.4 MB     00:00    
CentOS Linux 8 - BaseOS                          18 MB/s | 3.6 MB     00:00    
CentOS Linux 8 - Extras                         309  B/s |  10 kB     00:34    
（略）                                                  
  mod_http2-1.15.7-3.module_el8.4.0+778+c970deab.x86_64                         

Complete!
Removing intermediate container 6ba7745852dd
 ---> ac63352f59be
Step 3/5 : RUN sed -i -e "s/#ServerName www.example.com/ServerName localhost/" /etc/httpd/conf/httpd.conf
 ---> Running in 186ed67a3e14
Removing intermediate container 186ed67a3e14
 ---> 4013705a208b
Step 4/5 : COPY index.html /var/www/html/index.html
 ---> 64850eabb784
Step 5/5 : CMD ["/usr/sbin/httpd","-DFOREGROUND"]
 ---> Running in 9aafd8345b5c
Removing intermediate container 9aafd8345b5c
 ---> e6dd36800f1d
Successfully built e6dd36800f1d
Successfully tagged apacheweb-dockerfile:1.0
```

以下のように表示されれば、ビルドの成功です。
```text
Successfully built e6dd36800f1d
Successfully tagged apacheweb-dockerfile:1.0
```

コンテナイメージが作成されたので、イメージ一覧を確認します。

`docker images`{{execute}}

大元のcentosイメージと、ビルドされたapacheweb-dockerfile:1.0があります。

```text
$ docker images
REPOSITORY             TAG       IMAGE ID       CREATED          SIZE
apacheweb-dockerfile   1.0       e6dd36800f1d   22 seconds ago   278MB
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

---
**作成したコンテナイメージからのコンテナの起動**

![Test Image 1](https://raw.githubusercontent.com/mayumi00/katacoda-scenarios/main/container102/images/image1-2.png)

作成されたapacheweb-dockerfile:1.0イメージからtestweb00をバックグラウンドで起動します。httpdにアクセスするため、自ホストの8080番とコンテナの80番をバインドしています。

`docker run -d -p 8080:80 --name testweb00  apacheweb-dockerfile:1.0`{{execute}}

```text
$ docker run -d -p 8080:80 --name testweb00  apacheweb-dockerfile:1.0
d92f2cf8234f57fa883567078fa946f76326cf7ec7e93aa93e4df053b6b3377e
```

コンテナ一覧を表示します。

`docker ps -a`{{execute}}

testweb00というコンテナが起動していることがわかります。

```text
$ docker ps -a
CONTAINER ID   IMAGE                      COMMAND                  CREATED         STATUS         PORTS                                   NAMES
d92f2cf8234f   apacheweb-dockerfile:1.0   "/usr/sbin/httpd -DF…"   3 seconds ago   Up 2 seconds   0.0.0.0:8080->80/tcp, :::8080->80/tcp   testweb00
```

---
**httpdの動作確認**

httpdにアクセスすると、自ホストで作成したindex.htmlがコンテナ内に存在し、表示されることが確認できます。

`curl http://localhost:8080/`{{execute}}

```text
$ curl http://localhost:8080/
<head><title>Apache on Docker Container</title></head><body><H1>Container 102 - Web</H1>Apache on Docker Container using Dockerfile</body>
```
 ブラウザで確認する場合は以下をクリックしてください。index.htmlの内容が表示されます。

https://[[HOST_SUBDOMAIN]]-8080-[[KATACODA_HOST]].environments.katacoda.com/

![Test Image 1](https://raw.githubusercontent.com/mayumi00/katacoda-scenarios/main/container102/images/image102web1.png)


---
**コンテナ内でのプロセスの確認**

実行中のtestweb00でbashを実行し、操作可能にします。

`docker exec -it testweb00 /bin/bash`{{execute}}

`ps f -e`{{execute}}

コンテナ内のプロセスを見ると、httpdがフォアグラウンド（-DFOREGROUND）で起動していることがわかります。
```text
[root@d92f2cf8234f /]# ps f -e
    PID TTY      STAT   TIME COMMAND
    223 pts/0    Ss     0:00 /bin/bash
    239 pts/0    R+     0:00  \_ ps f -e
      1 ?        Ss     0:00 /usr/sbin/httpd -DFOREGROUND
      8 ?        S      0:00 /usr/sbin/httpd -DFOREGROUND
      9 ?        Sl     0:00 /usr/sbin/httpd -DFOREGROUND
     10 ?        Sl     0:00 /usr/sbin/httpd -DFOREGROUND
     12 ?        Sl     0:00 /usr/sbin/httpd -DFOREGROUND
```

`exit`{{execute}}

Dockerfileを利用すると、Container 101の演習で手作業で行ったインストールや設定作業を自動的に行えることがわかります。

---

###  このステップで利用したdockerコマンド
- docker build [OPTIONS] PATH | URL | -
  - Dockerfileからイメージをビルドする




 