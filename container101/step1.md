# コンテナの起動


![Test Image 1](https://raw.githubusercontent.com/mayumi00/katacoda-scenarios/main/container101/images/image01.png)　

上の図における右側の「コンテナを稼働させるホスト（自ホスト）」が、このコースの左側に表示されているterminalに対応しています。この環境は既にDockerが利用できる状態になっていますので、Dockerを利用してコンテナを起動して、動作を確認してみましょう。

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

このコマンドでは以下のような処理が実施されています。

- 自ホスト上ではhello-worldというイメージが見つけられなかったので、コンテナレジストリから、最新のhello-worldイメージをダウンロードします（docker pull）。図の左にある既存のコンテナレジストリ（代表的なものはDocker Hubです）からpullして、自ホストのイメージ格納領域に保存します。

```text
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
2db29710123e: Pull complete 
Digest: sha256:cc15c5b292d8525effc0f89cb299f1804f3a725c8d05e158653a563f15e4f685
Status: Downloaded newer image for hello-world:latest
```
- 格納されたイメージからコンテナを生成します（途中経過は表示されません）
- 生成したコンテナをスタートします
- このコンテナは、Dockerについての説明を表示するものなので、説明の表示が終わると停止します

```text
Hello from Docker!
This message shows that your installation appears to be working correctly.
（略）
For more examples and ideas, visit:
 https://docs.docker.com/get-started/
 ```
 
  自ホストにhello-worldのイメージをダンロードされたので、イメージが格納されているか確認してみましょう。この環境では既にいくつかのコンテナイメージが準備されているのでhello-world以外も表示されます。
  

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

コンテナが簡単に起動できることを確認したところで、次のステップに進みます。

##  このステップで利用したdockerコマンド
- docker run [オプション] IMAGE [COMMAND] [ARG...]
- docker images [オプション] [REPOSITORY[:TAG]]
- docker ps [オプション]
 