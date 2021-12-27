### Dockerfileを利用してのコンテナイメージのビルド

このステップではDockerfileを利用することで、以下の操作をまとめて実施できることを学びます。

- ベースとなるCentOSコンテナイメージを取得する
- CentOSにhttpd（Apache HTTP Server）をインストールする
- httpd（Apache HTTP Server）の設定を行う
- ドキュメントルート配下にコンテンツを配置する
- httpdを起動する

まずは自ホスト内に、Webのコンテンツとなるindex.htmlを作成しておきます。Container 101の演習では、コンテナ内でindex.htmlを作成しましたが、この演習では自ホストに置かれたindex.htmlをコンテナにコピーして使う方法を取ります。

`echo "<head><title>Apache on Docker Container</title></head><body><H1>Container 102 - Web</H1>Apache on Docker Container using Dockerfile</body>"  > index.html `{{execute}}

続いてDockerfileに必要事項を記載してゆきます。

> Note: 今回はEditorを利用せずechoコマンドでファイルを作成します。

`echo "FROM centos"  > Dockerfile `{{execute}}

`FROM`は、ベースとなるコンテナイメージを指定します。コンテナイメージが自ホストにない場合は自動的にコンテナレジストリからダウンロードします。Dockerfileは基本的にFROMから始める必要があります。

`echo "RUN dnf install -y httpd"  >> Dockerfile `{{execute}}

`echo "RUN sed -i -e \"s/#ServerName www.example.com/ServerName localhost/\" /etc/httpd/conf/httpd.conf"  >> Dockerfile `{{execute}}

`RUN`は`FROM`で指定したコンテナイイメージに対してコマンドを実行します。`RUN`は複数使用可能です。`RUN`にはshell形式とexec形式があり、この例ではshell形式で記述しています。httpdのインストールとhttpd.confの設定を行っています。

`echo "COPY index.html /var/www/html/index.html"  >> Dockerfile `{{execute}}

`COPY`は、sourceからファイルやディレクトリをコピーして、コンテナ内のファイルシステムのパス destinationに追加します。この例では、自ホストの現在のディレクトリにあるindex.htmlをファイルをコンテナイメージ内の/var/www/html/index.htmlにコピーしています。

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

![Test Image 1](https://raw.githubusercontent.com/mayumi00/katacoda-scenarios/main/container102/images/image201.png)

Dockerfileが作成できたので、これを利用してコンテナイメージをビルドします。`-t（or --tag）オプション` で名前およびタグを指定します。以下の例ではDockerfileを指定してないように見えますが、デフォルトで指定したPATHにあるDockerfileを使うので、「PATH .（現在のディレクトリ）にあるDockerfileを使用する」という意味になります。もし、他のファイルを利用する場合は`-f（or --file）オプション` で明示的に指定します。この例ではapacheweb-dockerfile:1.0という名前:タグでイメージをビルドします。

> Note: 途中で「warning: /var/cache/dnf」というwarningが出ることがありますが、今回は無視してください。

`docker build -t apacheweb-dockerfile:1.0 .`{{execute}}
 
```text
$ docker build -t apacheweb-dockerfile:1.0 .
Sending build context to Docker daemon    1.6MB
Step 1/5 : FROM centos
latest: Pulling from library/centos
a1d0c7532777: Downloading [==================================================>]  83.52MB/83.52MB
latest: Pulling from library/centos
a1d0c7532777: Pull complete 
Digest: sha256:a27fd8080b517143cbbbab9dfb7c8571c40d67d534bbdee55bd6c473f432b177
Status: Downloaded newer image for centos:latest
 ---> 5d0da3dc9764
Step 2/5 : RUN dnf install -y httpd
 ---> Running in 1842c4faeb7c
CentOS Linux 8 - AppStream                       37 MB/s | 8.4 MB     00:00    
（略）                     
  mod_http2-1.15.7-3.module_el8.4.0+778+c970deab.x86_64                         

Complete!
Removing intermediate container 1842c4faeb7c
 ---> 8a6a9a3055d2
Step 3/5 : RUN sed -i -e "s/#ServerName www.example.com/ServerName localhost/" /etc/httpd/conf/httpd.conf
 ---> Running in 43d2beb5233d
Removing intermediate container 43d2beb5233d
 ---> 1fe64e5c12ff
Step 4/5 : COPY index.html /var/www/html/index.html
 ---> b7b75e1539d6
Step 5/5 : CMD ["/usr/sbin/httpd","-DFOREGROUND"]
 ---> Running in 3004b5e6f50e
Removing intermediate container 3004b5e6f50e
 ---> 0c8e45773b1e
Successfully built 0c8e45773b1e
Successfully tagged apacheweb-dockerfile:latest
```

コンテナイメージが作成されたので、イメージ一覧を確認します。

`docker images`{{execute}}

大元のcentosイメージと、ビルドされたapacheweb-dockerfile:1.0があります。

```text
$ docker images 
REPOSITORY             TAG       IMAGE ID       CREATED          SIZE
apacheweb-dockerfile   latest    0c8e45773b1e   11 minutes ago   278MB
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

作成されたapacheweb-dockerfile:1.0イメージからtesteweb00をバックグラウンドで起動します。httpdにアクセスするため、自ホストの8080番とコンテナの80番をバインドしています。

`docker run -d -p 8080:80 --name testeweb00  apacheweb-dockerfile:1.0`{{execute}}

```text
$ docker run -d -p 8080:80 --name testeweb00  apacheweb-dockerfile:1.0
0249c56bd7156724e4693e3124358271c547bc5bab3ebd6632d60e7d116db697
```

コンテナ一覧を表示します。

`docker ps -a`{{execute}}

testeweb00というコンテナが起動していることがわかります。

```text
$ docker ps -a
CONTAINER ID   IMAGE                  COMMAND                  CREATED          STATUS          PORTS                                   NAMES
0249c56bd715   apacheweb-dockerfile   "/usr/sbin/httpd -DF…"   17 minutes ago   Up 17 minutes   0.0.0.0:8080->80/tcp, :::8080->80/tcp   testeweb00
```

httpdにアクセスすると、自ホストで作成したindex.htmlがコンテナ内に存在し、表示されることが確認できます。

`curl http://localhost:8080/`{{execute}}

```text
$ curl http://localhost:8080/
<head><title>Apache on Docker Container</title></head><body><H1>Container 102 - Web</H1>Apache on Docker Container using Dockerfile</body>
```
ブラウザでの確認
https://[[HOST_SUBDOMAIN]]-8080-[[KATACODA_HOST]].environments.katacoda.com/

![Test Image 1](https://raw.githubusercontent.com/mayumi00/katacoda-scenarios/main/container102/images/image202.png)

実行中のtesteweb00でbashを実行し、操作可能にします。

`docker exec -it testeweb00 /bin/bash`{{execute}}

`ps f -e`{{execute}}

コンテナ内のプロセスを見ると、httpdがフォアグラウンド（-DFOREGROUND）で起動していることがわかります。
```text
[root@0249c56bd715 /]# ps f -e
    PID TTY      STAT   TIME COMMAND
    223 pts/0    Ss     0:00 /bin/bash
    238 pts/0    R+     0:00  \_ ps f -e
      1 ?        Ss     0:00 /usr/sbin/httpd -DFOREGROUND
      8 ?        S      0:00 /usr/sbin/httpd -DFOREGROUND
      9 ?        Sl     0:00 /usr/sbin/httpd -DFOREGROUND
     10 ?        Sl     0:00 /usr/sbin/httpd -DFOREGROUND
     11 ?        Sl     0:00 /usr/sbin/httpd -DFOREGROUND
```

`exit`{{execute}}

Dockerfileを利用すると、Container 101の演習で手作業で行ったインストールや設定作業を自動的に行えることがわかります。

###  このステップで利用したdockerコマンド
- docker build [OPTIONS] PATH | URL | -
  - Dockerfileからイメージをビルドする




 