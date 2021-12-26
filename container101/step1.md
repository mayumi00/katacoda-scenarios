## コンテナの起動

![Test Image 1](https://raw.githubusercontent.com/mayumi00/katacoda-scenarios/main/container101/images/image00.png)　

上の図における右側の「コンテナを稼働させるホスト（自ホスト）」が、このコースの左側に表示されているTerminalに対応しています。

この環境は既にDockerが利用できる状態になっていますので、Dockerを利用してコンテナを起動して、動作を確認してみましょう。`docker version`{{execute}}←このように表示されている部分をクリックすると右のTerminalでコマンドが実行されます。

「hello-world」というDockerについての簡単な説明を出力するコンテナを起動してみます。

`docker run hello-world`{{execute}}

以下のように出力されます。

```text
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
2db29710123e: Pull complete 
Digest: sha256:cc15c5b292d8525effc0f89cb299f1804f3a725c8d05e158653a563f15e4f685
Status: Downloaded newer image for hello-world:latest

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/
 ```

このコマンドでは次の処理が実施されています。

1. 自ホスト上ではhello-worldというイメージが見つけられなかったので、図の左にあるコンテナレジストリから最新版のhello-worldイメージをダウンロードして、自ホストのイメージ格納領域に保存します
1. 格納されたイメージからコンテナを生成します
1. 生成したコンテナを起動します
1. Dockerについての簡単な説明を出力します
1. 説明の表示が終わるとコンテナは停止します


![Test Image 1](https://raw.githubusercontent.com/mayumi00/katacoda-scenarios/main/container101/images/image01.png)

自ホストにhello-worldのイメージをダンロードされたので、イメージが格納されているか確認してみましょう。下から3行目にhello-worldというイメージがあることがわかります。
> Note: この環境では既にいくつかのコンテナイメージが準備されているのでhello-world以外も表示されます。

 `docker images`{{execute}}
 
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
 `docker images`コマンドで表示される項目には以下があります。
 
 | 項目 | 意味 | 
|:-----------|:------------|
| REPOSITORY | リポジトリ、名前は同じでタグが異なるDockerイメージの集まり | 
| TAG | イメージに付けられているタグ、リポジトリやバージョン情報 | 
| IMAGE ID | イメージID | 
| CREATED | イメージが作成された時刻（ 自ホストにダウンロードされた時刻ではありません） |
| SIZE | イメージのサイズ |

次にイメージから生成されたコンテナの一覧を見てみましょう。デフォルトでは実行中のコンテナのみが表示されます。`-a（or --all）オプション`を付けると、起動していないコンテナも含めてすべてを表示します。hello-worldコンテナは説明を出力すると終了してしまうので`-a（or --all）オプション`を付けないと表示されません。 

`docker ps -a `{{execute}}

```text
CONTAINER ID   IMAGE         COMMAND    CREATED         STATUS                     PORTS     NAMES
655c23e742bf   hello-world   "/hello"   8 minutes ago   Exited (0) 8 minutes ago             elegant_yonath
 ```
  `docker ps`コマンドで表示される項目は以下になります。ExitedというSTATUSはコンテナが実行され、終了した状態を指しています。コンテナの各ステータスと遷移については別途説明いたします。

  | 項目 | 意味 | 
|:-----------|:------------|
| CONTAINER ID | コンテナのIDでユニークな値が付けられる | 
| IMAGE | イメージに付けられているタグ、リポジトリやバージョン情報 | 
| COMMAND | コンテナで実行されたコマンド | 
| CREATED | コンテナが生成された時刻 |
| STATUS | コンテナの状態、起動中の場合は「Up」停止してる場合は「Exited」となる |
| PORTS | 公開ポート、プロトコル |
| NAMES | コンテナの名前（ コンテナを生成する際に `--nameオプション`で指定して名前を付けることが可能）|

特定の処理をして停止するコンテナを動かしてみましたが、停止してしまっているので今ひとつピンと来ないかもしれませんので、続いて、稼働していることがわかるコンテナを利用してみます。静的コンテンツを表示するWebサーバのコンテナを起動してみます。コンテナイメージ名称がわからないので、httpdという文字列でコンテナレジストリを検索します。

`docker search httpd`{{execute}}
```text
[root@ik1-314-17333 ~]# docker search httpd
NAME                                    DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
httpd                                   The Apache HTTP Server Project                  3806      [OK]
centos/httpd-24-centos7                 Platform for running Apache httpd 2.4 or bui…   40
centos/httpd                                                                            34                   [OK]
（以下、略）
```

いろいろ表示されますがDESCRIPTIONを見ると`httpd / The Apache HTTP Server Project`がApache Projectのhttpdイメージのようです。それではhttpdコンテナを起動してみましょう。hello-worldを起動した場合に比べると`docker run`にいくつかのオプションが追加されてますが、それは次のステップで説明しますのでとりあえずは「httpdコンテナを起動したんだな」くらいに思ってください。

`docker run -d --name httpd -p 80:80 httpd:latest `{{execute}}

```text
[root@ik1-314-17333 ~]# docker run -d --name httpd -p 80:80 httpd:latest
Unable to find image 'httpd:latest' locally
latest: Pulling from library/httpd
a2abf6c4d29d: Pull complete
dcc4698797c8: Pull complete
41c22baa66ec: Pull complete
67283bbdd4a0: Pull complete
d982c879c57e: Pull complete
Digest: sha256:0954cc1af252d824860b2c5dc0a10720af2b7a3d3435581ca788dff8480c7b32
Status: Downloaded newer image for httpd:latest
8d6cda605ee754f70f89d0fffb5048faafa10bddbc3137d18206793f037a6649
 ```
 
hello-world同様に、自ホストにはhttpdイメージがなかったので、コンテナレジストリからダウンロードしていることがわかります。-p 80:80の意味するところは、ローカルホスト（自ホスト）の80番ポートとコンテナ内の80番ポートをバインドすることです。curl でローカルホストの80番にアクセスすると「It works!」と表示され、httpdが稼働していることがわかります。

 `curl  http://localhost:80/ `{{execute}}
 
 ```text
[root@ik1-314-17333 ~]# curl http://localhost:80/
<html><body><h1>It works!</h1></body></html>
 ```
 
 ブラウザで確認する場合は以下をクリックしてください。
 https://[[HOST_SUBDOMAIN]]-80-[[KATACODA_HOST]].environments.katacoda.com/
 
 ![Test Image 1](https://raw.githubusercontent.com/mayumi00/katacoda-scenarios/main/container101/images/image02.png)　
 
 ` docker ps -a `{{execute}}

再度コンテナ一覧を取得すると、停止したhello-worldとは異なり、STATUSがUpと表示され、httpdコンテナが80番で待ち受けしながら稼働していることが確認できます。

```text
[root@ik1-314-17333 ~]# docker ps -a
CONTAINER ID   IMAGE                         COMMAND                  CREATED          STATUS                      PORTS                  NAMES
8d6cda605ee7   httpd:latest                  "httpd-foreground"       55 seconds ago   Up 53 seconds               0.0.0.0:80->80/tcp     httpd
41fa697c9b50   hello-world                   "/hello"                 23 minutes ago   Exited (0) 12 minutes ago                          stoic_goldwasser
 ```
 コンテナが簡単に起動できることを確認したところで、次のステップに進みます。



###  このステップで利用したdockerコマンド
- docker search [オプション] TERM
   - Docker Hub上のイメージを検索する
- docker run [オプション] IMAGE [COMMAND] [ARG...]
  - コンテナを起動する
- docker images [オプション] [REPOSITORY[:TAG]]
  - コンテナイメージ一覧を表示する
- docker ps [オプション]
  - コンテナ一覧を表示する



 