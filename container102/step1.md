# コンテナの起動


![Test Image 1](https://raw.githubusercontent.com/mayumi00/katacoda-scenarios/main/container101/images/image01.png)　

<pre class="file" data-filename="index.html" data-target="append">
<head><title>Apache on Docker Container</title></head>
<body><H1>Container 101 - Web</H1>Apache on Docker Container</body>
</pre>

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
CMD ["/usr/sbin/httpsd","-DFOREGROUND"]
</pre>


`docker build -t apacheweb-dockerfile .`{{execute}}
 
```text
REPOSITORY         TAG       IMAGE ID       CREATED        SIZE
redis              latest    b8477f2e393b   2 months ago   113MB
mongo              latest    c1a14d3979c5   2 months ago   691MB
mariadb            10        b7220a722ce2   2 months ago   409MB
mariadb            latest    b7220a722ce2   2 months ago   409MB
ubuntu             latest    597ce1600cf4   2 months ago   72.8MB
postgres           12        fe603fe275ba   2 months ago   371MB
postgres           latest    6ce504119cc8   2 months ago   374MB
mysql              8         2fe463762680   2 months ago   514MB
mysql              latest    2fe463762680   2 months ago   514MB
hello-world        latest    feb5d9fea6a5   2 months ago   13.3kB
alpine             latest    14119a10abf4   3 months ago   5.59MB
weaveworks/scope   1.11.4    a082d48f0b39   2 years ago    78.5MB
```
 | 項目 | 意味 | 
|:-----------|:------------|
| REPOSITORY | リポジトリ、名前は同じでタグが異なるDockerイメージの集まり | 
| TAG | イメージに付けられているタグ、リポジトリやバージョン情報 | 
| IMAGE ID | イメージID | 
| CREATED | イメージが作成された時刻（ 自ホストにダウンロードされた時刻ではありません） |
| SIZE | イメージのサイズ |

 ここで、コンテナの一覧を見てみましょう。`-a（or --all）オプション`を付けると、起動していないコンテナも含めてすべてを表示します。`-a（or --all）オプション`を付けない場合は、実行中のコンテナのみが表示されます。hello-worldコンテナは説明を出力すると終了してしまうので`-a（or --all）オプション`を付けないと表示されません。ExitedというSTATUSはコンテナが実行され、終了した状態を指しています。
 
`docker ps -a `{{execute}}

```text
CONTAINER ID   IMAGE         COMMAND    CREATED         STATUS                     PORTS     NAMES
655c23e742bf   hello-world   "/hello"   8 minutes ago   Exited (0) 8 minutes ago             elegant_yonath
 ```
 
  | 項目 | 意味 | 
|:-----------|:------------|
| CONTAINER ID | コンテナのIDでユニークな値が付けられる | 
| IMAGE | イメージに付けられているタグ、リポジトリやバージョン情報 | 
| COMMAND | コンテナで実行されたコマンド | 
| CREATED | コンテナが生成された時刻 |
| STATUS | コンテナの状態、右のいずれか（created、restarting、running、removing、paused、exited、dead） |
| PORTS | 公開ポート、プロトコル |
| NAMES | コンテナの名前（ コンテナを生成する際に `--nameオプション`で指定して名前を付けることが可能）|


`docker ps -a `{{execute}}

コンテナが不要になった場合は削除することが可能です。削除のオプションには実行中のコンテナを強制的に削除したり、コンテナに紐付いたボリュームを削除するものがあります。コンテナを削除しても、コンテナイメージは削除されませんので、必要に応じて起動すれば、同じコンテナを生成することが可能です。

`docker rm  655c23e742bf `{{execute}}

```text
$ docker rm 9e2667865bd6
9e2667865bd6
 ```
 
`docker ps -a `{{execute}}

```text
CONTAINER ID   IMAGE         COMMAND    CREATED         STATUS                     PORTS     NAMES
655c23e742bf   hello-world   "/hello"   8 minutes ago   Exited (0) 8 minutes ago             elegant_yonath
 ```
 
コンテナが簡単に起動できることを確認したところで、次のステップに進みます。



##  このステップで利用したdockerコマンド
- docker run [オプション] IMAGE [COMMAND] [ARG...]
  - コンテナを起動する
- docker images [オプション] [REPOSITORY[:TAG]]
  - コンテナイメージ一覧を表示する
- docker ps [オプション]
  - コンテナ一覧を表示する
- docker rm [オプション] CONTAINER [CONTAINER...]
  - ひとつまたは複数のコンテナを削除する



 