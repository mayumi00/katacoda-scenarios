

`echo "<head><title>Apache on Docker Container</title></head><body><H1>Container 102 - Web</H1>Apache on Docker Container using Dockerfile</body>"  > index.html `{{execute}}

続いてDockerfileに必要事項を記載してゆきます。

<pre class="file" data-filename="Dockerfile" data-target="append">FROM centos</pre>

<pre class="file" data-filename="Dockerfile" data-target="append">RUN dnf install -y httpd</pre>

<pre class="file" data-filename="Dockerfile" data-target="append">RUN sed -i -e "s/#ServerName www.example.com/ServerName localhost/" /etc/httpd/conf/httpd.conf</pre>

<pre class="file" data-filename="Dockerfile" data-target="append">COPY index.html /var/www/html/index.html</pre>


<pre class="file" data-filename="Dockerfile" data-target="append">CMD ["/usr/sbin/httpd","-DFOREGROUND"]</pre>

`docker build -t apacheweb-dockerfile:1.0 .`{{execute}}


`echo "<head><title>Apache on Docker Container</title></head><body><H1>Container 102 - Chage HTML Web</H1>Apache on Docker Container using Dockerfile</body>"  > index2.html `{{execute}}

<pre class="file" data-filename="Dockerfile2" data-target="replace">FROM centos
</pre>

`FROM`でベースとなるコンテナイメージを指定します。これはStep1と同じcentosを指定します。

<pre class="file" data-filename="Dockerfile2" data-target="append">RUN dnf install -y httpd
</pre>
<pre class="file" data-filename="Dockerfile" data-target="append">RUN sed -i -e "s/#ServerName www.example.com/ServerName localhost/" /etc/httpd/conf/httpd.conf
</pre>

`RUN`によるhttpdのインストールとhttpd.confの設定はStep1と同様です。

<pre class="file" data-filename="Dockerfile2" data-target="append">COPY index2.html /var/www/html/index.html
</pre>

`COPY`は、Step1とは異なり、自ホストの現在のディレクトリにあるindex2.htmlをファイルをコンテナイメージ内の/var/www/html/index.htmlにコピーしています。

<pre class="file" data-filename="Dockerfile2" data-target="append">CMD ["/usr/sbin/httpd","-DFOREGROUND"]
</pre>

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
 
