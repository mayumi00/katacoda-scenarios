### Dockerfileの変更に基づくコンテナイメージの変更

![Test Image 1](https://raw.githubusercontent.com/mayumi00/katacoda-scenarios/main/container102/images/image204.png)

step1でコンテナイメージをビルドしましたが、これに変更を加えたいと思います。ビルドされたイメージから起動しているコンテナに変更を加えて、それを`docker commit`でイメージ化することも可能ですが、変更の履歴の管理も考慮して、Dockerfileを変更して、そこからタグの異なるコンテナイメージを作成します。Dockerfileのバージョン管理を行えばスマートですが、今回は更新したDockerfile2を使ってビルドします。

> Note: 今回はEditorを利用せずechoコマンドでファイルを作成します。

step1ではコンテナ内にコピーするhtmlファイルとしてindex.htmlを指定しましたが、新しく作成したindex2.htmlをコンテナ内のindex.htmlにコピーします。自ホスト内にindex2.htmlを作成します。

`echo "<head><title>Apache on Docker Container</title></head><body><H1>Container 102 - Chage HTML Web</H1>Apache on Docker Container using Dockerfile</body>"  > index2.html `{{execute}}

新しいDockerfileであるDockerfile2を作成します。

`FROM centos`

`echo "FROM centos"  > Dockerfile2 `{{execute}}

`FROM`でベースとなるコンテナイメージを指定します。これはStep1と同じcentosを指定します。

`RUN dnf install -y httpd`

`echo "RUN dnf install -y httpd"  >> Dockerfile2 `{{execute}}

`RUN sed -i -e "s/#ServerName www.example.com/ServerName localhost/" /etc/httpd/conf/httpd.conf`

`echo "RUN sed -i -e \"s/#ServerName www.example.com/ServerName localhost/\" /etc/httpd/conf/httpd.conf"  >> Dockerfile2 `{{execute}}

`RUN`によるhttpdのインストールとhttpd.confの設定はstep1と同様です。

`COPY index.html /var/www/html/index.html`

`echo "COPY index2.html /var/www/html/index.html"  >> Dockerfile2 `{{execute}}

`COPY`は、Step1とは異なり、自ホストの現在のディレクトリにあるindex2.htmlをファイルをコンテナイメージ内の/var/www/html/index.htmlにコピーしています。

`CMD ["/usr/sbin/httpd","-DFOREGROUND"]`

`echo "CMD [\"/usr/sbin/httpd\",\"-DFOREGROUND\"]" >> Dockerfile2 `{{execute}}

`CMD`は先程と同様/usr/sbin/httpdコマンドにパラメータ -DFOREGROUNDを指定してフォアグラウンドで実行します

作成したDockerfile2の内容を確認します。

`cat Dockerfie2 `{{execute}}

以下の内容になっていればOKです。

```text
FROM centos
RUN dnf install -y httpd
RUN sed -i -e "s/#ServerName www.example.com/ServerName localhost/" /etc/httpd/conf/httpd.conf
COPY index2.html /var/www/html/index.html
CMD ["/usr/sbin/httpd","-DFOREGROUND"]
```
DockerfileとDockerfile2の違いはCOPY部分の1箇所です。

`diff -C0 Dockerfile  Dockerfile2 `{{execute}}
 
 ```text
$ diff -C0 Dockerfile  Dockerfile2
*** Dockerfile  2021-12-27 01:41:35.420000000 +0000
--- Dockerfile2 2021-12-27 01:47:47.920000000 +0000
***************
*** 4 ****
! COPY index.html /var/www/html/index.html
--- 4 ----
! COPY index2.html /var/www/html/index.html
 ```

新しく作成したDockerfile2を使ってコンテナイメージをビルドします。`-t（or --tag）オプション` でapacheweb-dockerfile:2.0という名前:タグのイメージをビルドします。ビルドに使うDockerfileとして`-f（or --file）オプション`でDockerfile2を指定します。PATHは.（現在のディレクトリ）です。

`docker build -t apacheweb-dockerfile:2.0 -f Dockerfile2 .`{{execute}}
 
```text
$ docker build -t apacheweb-dockerfile:2.0 -f Dockerfile2 .
Sending build context to Docker daemon  1.604MB
Step 1/5 : FROM centos
 ---> 5d0da3dc9764
Step 2/5 : RUN dnf install -y httpd
 ---> Using cache
 ---> ac63352f59be
Step 3/5 : RUN sed -i -e "s/#ServerName www.example.com/ServerName localhost/" /etc/httpd/conf/httpd.conf
 ---> Using cache
 ---> 4013705a208b
Step 4/5 : COPY index2.html /var/www/html/index.html
 ---> c99c2fded417
Step 5/5 : CMD ["/usr/sbin/httpd","-DFOREGROUND"]
 ---> Running in 032c7f526bfc
Removing intermediate container 032c7f526bfc
 ---> c6a1ddf70f30
Successfully built c6a1ddf70f30
Successfully tagged apacheweb-dockerfile:2.0
```
以下のように表示されれば、ビルドの成功です。
```text
Successfully built c6a1ddf70f30
Successfully tagged apacheweb-dockerfile:2.0
```
step1と異なり、centosコンテナイメージは既に自ホストにあるのでダウンロードは行われません。また、httpdのインストールに関してもキャッシュを利用しています。

コンテナイメージが作成されたので、イメージ一覧を確認します。

`docker images`{{execute}}

大元のcentosイメージと、step1でビルドされたapacheweb-dockerfile:1.0、新しくビルドされたapacheweb-dockerfile:2.0があります。
```text
$ docker images
REPOSITORY             TAG       IMAGE ID       CREATED         SIZE
apacheweb-dockerfile   2.0       c6a1ddf70f30   6 seconds ago   278MB
apacheweb-dockerfile   1.0       e6dd36800f1d   3 minutes ago   278MB
redis                  latest    b8477f2e393b   2 months ago    113MB
mongo                  latest    c1a14d3979c5   2 months ago    691MB
mariadb                10        b7220a722ce2   2 months ago    409MB
mariadb                latest    b7220a722ce2   2 months ago    409MB
ubuntu                 latest    597ce1600cf4   2 months ago    72.8MB
postgres               12        fe603fe275ba   2 months ago    371MB
postgres               latest    6ce504119cc8   2 months ago    374MB
mysql                  8         2fe463762680   2 months ago    514MB
mysql                  latest    2fe463762680   2 months ago    514MB
centos                 latest    5d0da3dc9764   3 months ago    231MB
alpine                 latest    14119a10abf4   4 months ago    5.59MB
weaveworks/scope       1.11.4    a082d48f0b39   2 years ago     78.5MB
```

作成されたapacheweb-dockerfile:2.0イメージからtestweb20をバックグラウンドで起動します。httpdにアクセスするため、自ホストの8000番とコンテナの80番をバインドしています。

`docker run -d -p 8000:80 --name testweb20  apacheweb-dockerfile:2.0`{{execute}}

```text
$ docker run -d -p 8000:80 --name testweb20  apacheweb-dockerfile:2.0
01e9f996ddf908f98fb8e2965841564a3345d537e9924493a00694ae7d24764f
```

コンテナ一覧を表示します。

`docker ps -a`{{execute}}

apacheweb-dockerfile:2.0から生成されたtestweb20というコンテナが起動していることがわかります。

```text
$ docker ps -a
CONTAINER ID   IMAGE                      COMMAND                  CREATED         STATUS         PORTS                                   NAMES
01e9f996ddf9   apacheweb-dockerfile:2.0   "/usr/sbin/httpd -DF…"   4 seconds ago   Up 2 seconds   0.0.0.0:8000->80/tcp, :::8000->80/tcp   testweb20
d92f2cf8234f   apacheweb-dockerfile:1.0   "/usr/sbin/httpd -DF…"   3 minutes ago   Up 3 minutes   0.0.0.0:8080->80/tcp, :::8080->80/tcp   testweb00
```

httpdにアクセスすると、自ホストで作成したindex2.htmlの内容が表示されることが確認できます。

`curl http://localhost:8000/`{{execute}}

```text
$ curl http://localhost:8000/
<head><title>Apache on Docker Container</title></head><body><H1>Container 102 - Chage HTML Web</H1>Apache on Docker Container using Dockerfile</body>
```

ブラウザでの確認。以下のように表示されます。

https://[[HOST_SUBDOMAIN]]-8000-[[KATACODA_HOST]].environments.katacoda.com/
![Test Image 1](https://raw.githubusercontent.com/mayumi00/katacoda-scenarios/main/container102/images/image102web2.png)


実行中のtestweb20でbashを実行し、操作可能にします。

`docker exec -it testweb20 /bin/bash`{{execute}}

`ps f -e`{{execute}}

コンテナ内のプロセスを見ると、httpdがフォアグラウンド（-DFOREGROUND）で起動していることがわかります。
```text
[root@01e9f996ddf9 /]# ps f -e
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

