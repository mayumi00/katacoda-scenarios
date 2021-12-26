### Dockerfileの変更とコンテナイメージの変更

![Test Image 1](https://raw.githubusercontent.com/mayumi00/katacoda-scenarios/main/container102/images/image204.png)

コンテナイメージをビルドしましたが、これに変更を加えたいと思います。ビルドされたイメージから起動しているコンテナに変更を加えて、それを`docker commit`でイメージ化することも可能ですが、変更の履歴の管理も考慮して、Dockerfileを変更して、そこからタグの異なるコンテナイメージを作成します。Dockerfileのバージョン管理を行えばスマートですが、今回は更新したDockerfile2を使ってビルドします。

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
 
新しく作成したDockerfile2を使ってコンテナイメージをビルドします。`-t（or --tag）オプション` で名前およびタグを指定します。この例ではapacheweb-dockerfile:2.0という名前のイメージをビルドします。

`docker build -t apacheweb-dockerfile:2.0 -f Dockerfile2 .`{{execute}}
 
```text
$ docker build -t apacheweb-dockerfile:2.0 -f Dockerfile2 .
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

イメージ一覧を確認します。

`docker images`{{execute}}

大元のcentosイメージと、Step1でビルドされたapacheweb-dockerfile:1.0、新しくビルドされたapacheweb-dockerfile:2.0があります。
```text
$ docker images 
REPOSITORY             TAG       IMAGE ID       CREATED          SIZE
apacheweb-dockerfile   2.0       42b46d5d8af1   16 seconds ago   278MB
apacheweb-dockerfile   latest    5c695c4ca4a3   3 days ago       278MB
apacheweb              v1.0      8f6df7021903   5 days ago       277MB
httpd                  latest    dabbfbe0c57b   5 days ago       144MB
hello-world            latest    feb5d9fea6a5   3 months ago     13.3kB
centos                 latest    5d0da3dc9764   3 months ago     231MB
```

作成されたapacheweb-dockerfile:2.0イメージからtesteweb20をバックグラウンドで起動します。httpdにアクセスするため、自ホストの8000番とコンテナの80番をバインドしています。

`docker run -d -p 8000:80 --name testeweb00  apacheweb-dockerfile:2.0`{{execute}}

```text
$ docker run -d -p 8080:80 --name testeweb00  apacheweb-dockerfile:2.0
0249c56bd7156724e4693e3124358271c547bc5bab3ebd6632d60e7d116db697
```

コンテナ一覧を表示します。

`docker ps -a`{{execute}}

testeweb20というコンテナが起動していることがわかります。

```text
$ docker ps -a
CONTAINER ID   IMAGE                  COMMAND                  CREATED          STATUS          PORTS                                   NAMES
0249c56bd715   apacheweb-dockerfile   "/usr/sbin/httpd -DF…"   17 minutes ago   Up 17 minutes   0.0.0.0:8080->80/tcp, :::8080->80/tcp   testeweb00
```

httpdにアクセスすると、自ホストで作成したindex.htmlがコンテナ内に存在し、表示されることが確認できます。

`curl http://localhost:8000/`{{execute}}

```text
$ curl http://localhost:8000/
<head><title>Apache on Docker Container</title></head><body><H1>Container 102 - Web</H1>Apache on Docker Container using Dockerfile</body>
```
ブラウザでの確認
https://[[HOST_SUBDOMAIN]]-8000-[[KATACODA_HOST]].environments.katacoda.com/

![Test Image 1](https://raw.githubusercontent.com/mayumi00/katacoda-scenarios/main/container102/images/image202.png)

実行中のtesteweb20でbashを実行し、操作可能にします。

`docker exec -it testeweb20 /bin/bash`{{execute}}

`[root@0249c56bd715 /]# ps f -e`{{execute}}

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

