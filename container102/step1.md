### Dockerfileの利用


![Test Image 1](https://raw.githubusercontent.com/mayumi00/katacoda-scenarios/main/container102/images/image201.png)　

> Note: この画面の右側の分割画面は、上部がエディターで下部がTerminalです。「Copy to Editor」部分をクリックすると、そのテキストが右上部のエディターに記述されます。Dockerfileはこのエディターで編集します。

このステップではDockerfileを利用することで、以下の操作をまとめて実施できることを学びます。

- ベースとなるCentOSコンテナイメージを取得する
- CentOSにhttpd（Apache HTTP Server）をインストールする
- httpd（Apache HTTP Server）の設定を行う
- ドキュメントルート配下にコンテンツを配置する
- httpdを起動する

まずは自ホスト内に、Webのコンテンツとなるindex.htmlを作成しておきます。Container 101の演習では、コンテナ内でindex.htmlを作成しましたが、この演習では自ホストに置かれたindex.htmlをコンテナにコピーして使う方法を取ります。

`echo "<head><title>Apache on Docker Container</title></head><body><H1>Container 102 - Web</H1>Apache on Docker Container using Dockerfile</body>"  > index.html `{{execute}}

続いてDockerfileに必要事項を記載します。

<pre class="file" data-filename="Dockerfile" data-target="append">FROM centos</pre>

`FROM`は、ベースとなるコンテナイメージを指定します。コンテナイメージが自ホストにない場合は、コンテナレジストリからpullでダウンロードします。Dockerfileは基本的にFROMから始める必要があります。

<pre class="file" data-filename="Dockerfile" data-target="append">RUN dnf install -y httpd</pre>

`RUN`は`FROM`で指定したコンテナイイメージに対してコマンドを実行します。`RUN`は複数使用可能です。`RUN`にはshell形式とexec形式があり、この例ではshell形式で記述しています。

<pre class="file" data-filename="Dockerfile" data-target="append">RUN sed -i -e "s/#ServerName www.example.com/ServerName localhost/" /etc/httpd/conf/httpd.conf</pre>

`COPY`は、sourceからファイルやディレクトリをコピーして、コンテナ内のファイルシステムのパス destinationに追加します。この例では、自ホストの、操作しているディレクトリにあるinde.htmlをファイルをコンテナイメージ内の/var/www/html/index.htmlにコピーしています。

<pre class="file" data-filename="Dockerfile" data-target="append">COPY index.html /var/www/html/index.html</pre>

`CMD`はコンテナが起動する際に実行するコマンドを指定します。記述方法はexec形式、shell形式、ENTRYPOINTのデフォルトパラメーターの３種類があります。Dockerfileでは`CMD`は１つしか記述できません。もし複数の`CMD`があった場合は、最後の`CMD`しか処理されません。記述方式はexec形式が推奨されており、[]内に、["コマンド","パラメータ1","パラメータ2"]のように記載します。この例では、/usr/sbin/httpdコマンドにパラメータ -DFOREGROUNDを指定してフォアグラウンドで実行します。

<pre class="file" data-filename="Dockerfile" data-target="append">CMD ["/usr/sbin/httpd","-DFOREGROUND"]</pre>

> Note: 上記の「Copy to Editor」が終わった後はエディターの7行目がハイライトされているはずです。エディター部分は触らずその状態にしておいてください。ハイライトの位置が代わると、後のbuild→runが正常に動作しない可能性があります。

Dockerfileの記載方法はDockerfile リファレンスを参照してください。https://matsuand.github.io/docs.docker.jp.onthefly/engine/reference/builder/

`docker build -t apacheweb-dockerfile .`{{execute}}
 
```text
REPOSITORY         TAG       IMAGE ID       CREATED        SIZE

```
- コンテナに特定のポートを割り当てて、かつ、バックグラウンドで起動する

`docker run -d -p 8080:80 --name testweb01  apacheweb-dockerfile:latest`{{execute}}

`curl http://localhost:8080/`{{execute}}

https://[[HOST_SUBDOMAIN]]-8080-[[KATACODA_HOST]].environments.katacoda.com/

![Test Image 1](https://raw.githubusercontent.com/mayumi00/katacoda-scenarios/main/container102/images/image202.png)

##  このステップで利用したdockerコマンド
- docker build [オプション] PATH | URL | -
  - Dockerfileからイメージをビルドする




 