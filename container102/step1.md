# コンテナの起動


![Test Image 1](https://raw.githubusercontent.com/mayumi00/katacoda-scenarios/main/container101/images/image01.png)　

この画面の右側の分割画面は、上部がエディターで下部がターミナルです。「Copy to Editor」部分をクリックすると、そのテキストが右上部のエディターに記述されます。Dockerfileはこのエディターで編集します。

その前に、まずはWebのindex.htmlを作成しておきます。
`echo "<head><title>Apache on Docker Container</title></head><body><H1>Container 102 - Web</H1>Apache on Docker Container using Dockerfile</body>"  > /var/www/html/index.html `{{execute}}

続いてDockerfileに必要事項を記載します。

<pre class="file" data-filename="Dockerfile" data-target="append">
FROM centos
</pre>

<pre class="file" data-filename="Dockerfile" data-target="append">
RUN dnf install -y httpd
</pre>

<pre class="file" data-filename="Dockerfile" data-target="append">
RUN sed -i -e "s/#ServerName www.example.com/ServerName localhost/" /etc/httpd/conf/httpd.conf
</pre>

<pre class="file" data-filename="Dockerfile" data-target="append">
COPY index.html /var/www/html/index.html
</pre>

<pre class="file" data-filename="Dockerfile" data-target="append">
CMD ["/usr/sbin/httpd","-DFOREGROUND"]
</pre>


`docker build -t apacheweb-dockerfile .`{{execute}}
 
```text
REPOSITORY         TAG       IMAGE ID       CREATED        SIZE

```

`docker run -d -p 8080:80 --name testweb01  apacheweb-dockerfile:latest`{{execute}}

`curl http://localhost:8080/`

https://[[HOST_SUBDOMAIN]]-8080-[[KATACODA_HOST]].environments.katacoda.com/


##  このステップで利用したdockerコマンド
- docker run [オプション] IMAGE [COMMAND] [ARG...]
  - コンテナを起動する
- docker images [オプション] [REPOSITORY[:TAG]]
  - コンテナイメージ一覧を表示する
- docker ps [オプション]
  - コンテナ一覧を表示する
- docker rm [オプション] CONTAINER [CONTAINER...]
  - ひとつまたは複数のコンテナを削除する



 