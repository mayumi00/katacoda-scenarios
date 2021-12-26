### Dockerfileの変更とコンテナイメージの変更

![Test Image 1](https://raw.githubusercontent.com/mayumi00/katacoda-scenarios/main/container102/images/image204.png)

Step1でコンテナイメージをビルドしましたが、これに変更を加えたいと思います。ビルドされたイメージから起動しているコンテナに変更を加えて、それを`docker commit`でイメージ化することも可能ですが、変更の履歴の管理も考慮して、Dockerfileを変更して、そこからタグの異なるコンテナイメージを作成します。Dockerfileのバージョン管理を行えばスマートですが、今回は更新したDockerfile2を使ってビルドします。

Step1ではコンテナ内にコピーするhtmlファイルとしてindex.htmlを指定しましたが、新しく作成したindex2.htmlをコンテナ内のindex.htmlにコピーします。


`echo "<head><title>Apache on Docker Container</title></head><body><H1>Container 102 - Chage HTML Web</H1>Apache on Docker Container using Dockerfile</body>"  > index2.html `{{execute}}

<pre class="file" data-filename="Dockerfile2" data-target="append">FROM centos</pre>

`FROM`でベースとなるコンテナイメージを指定します。これはStep1と同じcentosを指定します。

<pre class="file" data-filename="Dockerfile2" data-target="append">RUN dnf install -y httpd</pre>
<pre class="file" data-filename="Dockerfile" data-target="append">RUN sed -i -e "s/#ServerName www.example.com/ServerName localhost/" /etc/httpd/conf/httpd.conf</pre>

`RUN`によるhttpdのインストールとhttpd.confの設定はStep1と同様です。

<pre class="file" data-filename="Dockerfile2" data-target="append">COPY index2.html /var/www/html/index.html</pre>

`COPY`は、Step1とは異なり、自ホストの現在のディレクトリにあるindex2.htmlをファイルをコンテナイメージ内の/var/www/html/index.htmlにコピーしています。

<pre class="file" data-filename="Dockerfile2" data-target="append">CMD ["/usr/sbin/httpd","-DFOREGROUND"]</pre>

`CMD`は先程と同様/usr/sbin/httpdコマンドにパラメータ -DFOREGROUNDを指定してフォアグラウンドで実行します

> Note: 上記の「Copy to Editor」が終わった後はエディターの7行目がハイライトされているはずです。エディター部分は触らずその状態にしておいてください。ハイライトの位置が代わると、後のbuild→runが正常に動作しない可能性があります。

DockerfileとDockerfile2の違いは1箇所です。

`diff -C0 Dockerfile  Dockerfile2 `{{execute}}
 
 ```text
 *** Dockerfile  2021-12-26 09:20:53.448000000 +0000
--- Dockerfile2 2021-12-26 09:28:19.760000000 +0000
***************
*** 6,7 ****
! COPY index.html /var/www/html/index.html
--- 6,7 ----
! COPY index2.html /var/www/html/index.html
 ```
新しく作成したDockerfile2を使ってコンテナイメージをビルドします。`-t（or --tag）オプション` で名前およびタグを指定します。この例ではapacheweb-dockerfile:2.0という名前のイメージをビルドします。

`docker build -t apacheweb-dockerfile:2.0 -f Dockerfile2 .`{{execute}}
 
```text
$ docker build -t apacheweb-dockerfile:2.0 -f Dockerfile2 .
Sending build context to Docker daemon  1.604MB
Step 1/5 : FROM centos
 ---> 5d0da3dc9764
Step 2/5 : RUN dnf install -y httpd
 ---> Using cache
 ---> 11746cf3cda8
Step 3/5 : RUN sed -i -e "s/#ServerName www.example.com/ServerName localhost/" /etc/httpd/conf/httpd.conf
 ---> Using cache
 ---> 57d058e60ead
Step 4/5 : COPY index2.html /var/www/html/index.html
 ---> 2b313cfc0336
Step 5/5 : CMD ["/usr/sbin/httpd","-DFOREGROUND"]
 ---> Running in af63086acad0
Removing intermediate container af63086acad0
 ---> 765b2ec091a4
Successfully built 765b2ec091a4
Successfully tagged apacheweb-dockerfile:2.0
```
Step1と異なり、centosコンテナイメージは既に自ホストにあるのでダウンロードは行われません。また、httpdのインストールに関してもキャッシュを利用しています。

イメージ一覧を確認します。

`docker images`{{execute}}

大元のcentosイメージと、Step1でビルドされたapacheweb-dockerfile:1.0、新しくビルドされたapacheweb-dockerfile:2.0があります。
```text
$ docker images
REPOSITORY             TAG       IMAGE ID       CREATED          SIZE
apacheweb-dockerfile   2.0       765b2ec091a4   16 seconds ago   278MB
apacheweb-dockerfile   1.0       3224927b24a4   6 minutes ago    278MB
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

作成されたapacheweb-dockerfile:2.0イメージからtesteweb20をバックグラウンドで起動します。httpdにアクセスするため、自ホストの8000番とコンテナの80番をバインドしています。

`docker run -d -p 8000:80 --name testeweb20  apacheweb-dockerfile:2.0`{{execute}}

```text
See 'docker run --help'.
$ docker run -d  -p 8000:80 --name testweb20 apacheweb-dockerfile:2.0
37cffa45324d0047e6e7bd3fb9ec101228b9b871f345aa18bd46ed2158c44491
```

コンテナ一覧を表示します。

`docker ps -a`{{execute}}

testeweb20というコンテナが起動していることがわかります。

```text
$ docker ps -a
CONTAINER ID   IMAGE                      COMMAND                  CREATED         STATUS                     PORTS                                   NAMES
37cffa45324d   apacheweb-dockerfile:2.0   "/usr/sbin/httpd -DF…"   5 seconds ago   Up 3 seconds               0.0.0.0:8000->80/tcp, :::8000->80/tcp   testweb20
c8f2d3a6d38a   apacheweb-dockerfile:1.0   "/usr/sbin/httpd -DF…"   5 minutes ago   Exited (0) 5 minutes ago                                           testweb01
14d51cd305c8   apacheweb-dockerfile:1.0   "/usr/sbin/httpd -DF…"   7 minutes ago   Up 7 minutes               0.0.0.0:8080->80/tcp, :::8080->80/tcp
```

httpdにアクセスすると、自ホストで作成したindex2.htmlの内容が表示されることが確認できます。

`curl http://localhost:8000/`{{execute}}

```text
$ curl http://localhost:8000/
<head><title>Apache on Docker Container</title></head><body><H1>Container 102 - Chage HTML Web</H1>Apache on Docker Container using Dockerfile</body>
```
ブラウザでの確認
https://[[HOST_SUBDOMAIN]]-8000-[[KATACODA_HOST]].environments.katacoda.com/

![Test Image 1](https://raw.githubusercontent.com/mayumi00/katacoda-scenarios/main/container102/images/image202.png)

実行中のtesteweb20でbashを実行し、操作可能にします。

`docker exec -it testweb20 /bin/bash`{{execute}}

`ps f -e`{{execute}}

コンテナ内のプロセスを見ると、httpdがフォアグラウンド（-DFOREGROUND）で起動していることがわかります。
```text
[root@37cffa45324d /]# ps f -e 
    PID TTY      STAT   TIME COMMAND
    223 pts/0    Ss     0:00 /bin/bash
    239 pts/0    R+     0:00  \_ ps f -e
      1 ?        Ss     0:00 /usr/sbin/httpd -DFOREGROUND
      8 ?        S      0:00 /usr/sbin/httpd -DFOREGROUND
      9 ?        Sl     0:00 /usr/sbin/httpd -DFOREGROUND
     10 ?        Sl     0:00 /usr/sbin/httpd -DFOREGROUND
     11 ?        Sl     0:00 /usr/sbin/httpd -DFOREGROUND
```

`exit`{{execute}}

Dockerfileを利用すると、Container 101の演習で手作業で行ったインストールや設定作業を自動的に行えることがわかります。

