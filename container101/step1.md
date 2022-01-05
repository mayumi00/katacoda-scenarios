## コンテナの起動

この演習環境は下図のようになっています。図の右側の「コンテナを稼働させるホスト（自ホスト）」が、このコースの左側に表示されているTerminalに対応しています。

![Test Image 1](https://raw.githubusercontent.com/mayumi00/katacoda-scenarios/main/container101/images/image1-1.png)　

この環境は既にDockerが利用できる状態になっていますので、Dockerを利用してコンテナを起動して、動作を確認してみましょう。`docker version`{{execute}} ←このように表示されている部分をクリックすると右のTerminalでコマンドが実行されます。

```text
$ docker version
Client: Docker Engine - Community
 Version:           20.10.9
 API version:       1.41
 Go version:        go1.16.8
 Git commit:        c2ea9bc
 Built:             Mon Oct  4 16:08:29 2021
 OS/Arch:           linux/amd64
 Context:           default
 Experimental:      true

Server: Docker Engine - Community
 Engine:
  Version:          20.10.9
  API version:      1.41 (minimum version 1.12)
  Go version:       go1.16.8
  Git commit:       79ea9d3
  Built:            Mon Oct  4 16:06:34 2021
  OS/Arch:          linux/amd64
  Experimental:     false
 containerd:
  Version:          1.4.11
  GitCommit:        5b46e404f6b9f661a205e28d59c982d3634148f8
 runc:
  Version:          1.0.2
  GitCommit:        v1.0.2-0-g52b36a2
 docker-init:
  Version:          0.19.0
  GitCommit:        de40ad0
  ```

---
**hello-world コンテナ**

「hello-world」というDockerについての簡単な説明を出力するコンテナを起動してみます。

`docker run hello-world`{{execute}}

以下のように出力されます。

```text
$ docker run hello-world
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
2db29710123e: Pull complete 
Digest: sha256:2498fce14358aa50ead0cc6c19990fc6ff866ce72aeb5546e1d59caac3d0d60f
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

この`docker run`コマンドでは次の処理が実施されています。

![Test Image 1](https://raw.githubusercontent.com/mayumi00/katacoda-scenarios/main/container101/images/image1-2.png)

1. 自ホスト上ではhello-worldというイメージが見つけられなかったので、図の左にあるコンテナレジストリから最新版のhello-worldイメージをダウンロードして、自ホストのイメージ格納領域に保存します
1. 格納されたイメージからコンテナを生成します
1. 生成したコンテナを起動します
1. Dockerについての簡単な説明を出力します
1. 説明の表示が終わるとコンテナは停止します

---
**コンテナイメージ一覧**

自ホストにhello-worldのイメージをダンロードされたので、コンテナ一覧を表示する `docker images`コマンドを使ってイメージが格納されているか確認してみましょう。 下から3行目にhello-worldというイメージがあることがわかります。

> Note: この環境では既にいくつかのコンテナイメージが準備されているのでhello-world以外も表示されます。

 `docker images`{{execute}}
 
```text
$ docker images
REPOSITORY         TAG       IMAGE ID       CREATED        SIZE
redis              latest    b8477f2e393b   2 months ago   113MB
mongo              latest    c1a14d3979c5   3 months ago   691MB
mariadb            10        b7220a722ce2   3 months ago   409MB
mariadb            latest    b7220a722ce2   3 months ago   409MB
ubuntu             latest    597ce1600cf4   3 months ago   72.8MB
postgres           12        fe603fe275ba   3 months ago   371MB
postgres           latest    6ce504119cc8   3 months ago   374MB
mysql              8         2fe463762680   3 months ago   514MB
mysql              latest    2fe463762680   3 months ago   514MB
hello-world        latest    feb5d9fea6a5   3 months ago   13.3kB
alpine             latest    14119a10abf4   4 months ago   5.59MB
weaveworks/scope   1.11.4    a082d48f0b39   2 years ago    78.5MB
```

---
**コンテナ一覧**

イメージから生成されたコンテナの一覧を見てみましょう。`docker ps`がコンテナ一覧を表示するコマンドです。デフォルトでは実行中のコンテナのみが表示されます。`-a（or --all）オプション`を付けると、起動していないコンテナも含めてすべてを表示します。hello-worldコンテナは説明を出力すると終了してしまうので`-a（or --all）オプション`を付けないと表示されません。 

`docker ps -a `{{execute}}

```text
$ docker ps -a
CONTAINER ID   IMAGE         COMMAND    CREATED          STATUS                      PORTS     NAMES
b157d4669472   hello-world   "/hello"   22 seconds ago   Exited (0) 20 seconds ago             trusting_shamir
 ```
  `docker ps`コマンドで表示される項目のSTATUS「Exited」という状態は、コンテナが実行され、終了した状態を指しています。コンテナの各ステータスと遷移については別途説明いたします。

---
**コンテナイメージの検索**

特定の処理をして停止するコンテナを動かしてみましたが、停止してしまっているので今ひとつピンと来ないかもしれないので、続いて、稼働していることがわかるコンテナを利用してみます。静的コンテンツを表示するWebサーバのコンテナを起動してみます。コンテナイメージ名称がわからないので、httpdという文字列を指定してコンテナレジストリを検索します。`docker search`コマンドは指定された文字列でコンテナレジストリを検索します。

`docker search httpd`{{execute}}

```text
$ docker search httpd
NAME                                    DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
httpd                                   The Apache HTTP Server Project                  3819      [OK]       
centos/httpd-24-centos7                 Platform for running Apache httpd 2.4 or bui…   41                   
centos/httpd                                                                            34                   [OK]
（以下、略）
```

---
**コンテナの起動**

 ![Test Image 1](https://raw.githubusercontent.com/mayumi00/katacoda-scenarios/main/container101/images/image1-3.png)
 
検索の結果、文字列が含まれるコンテナイメージがいろいろ表示されますが、DESCRIPTIONを見ると`httpd / The Apache HTTP Server Project`がApache Projectのhttpdイメージのようです。それではhttpdコンテナを起動してみましょう。hello-worldを起動した場合に比べると`docker run`にいくつかのオプションが追加されてますが、それは次のステップで説明しますのでとりあえずは「httpdコンテナを起動したんだな」くらいに思ってください。

`docker run -d --name httpd -p 80:80 httpd:latest `{{execute}}

```text
$ docker run -d --name httpd -p 80:80 httpd:latest
Unable to find image 'httpd:latest' locally
latest: Pulling from library/httpd
a2abf6c4d29d: Pull complete 
dcc4698797c8: Pull complete 
41c22baa66ec: Pull complete 
67283bbdd4a0: Pull complete 
d982c879c57e: Pull complete 
Digest: sha256:0954cc1af252d824860b2c5dc0a10720af2b7a3d3435581ca788dff8480c7b32
Status: Downloaded newer image for httpd:latest
da36e29f033d3a85182ea91c805b69c8b3dbe1594bab74b59e26dc637e05da69
 ```
 
hello-world同様に、自ホストにはhttpdイメージがなかったので、コンテナレジストリからダウンロードしていることがわかります。「-p 80:80」の意味は、ローカルホスト（自ホスト）の80番ポートとコンテナ内の80番ポートをバインドすることです。

 ` docker ps -a `{{execute}}
 
再度コンテナ一覧を取得すると、停止したhello-worldとは異なり、STATUSがUpと表示され、httpdコンテナが80番で待ちながら稼働していることが確認できます。

```text
$ docker ps -a
CONTAINER ID   IMAGE          COMMAND              CREATED          STATUS                      PORTS                               NAMES
da36e29f033d   httpd:latest   "httpd-foreground"   12 seconds ago   Up 10 seconds               0.0.0.0:80->80/tcp, :::80->80/tcp   httpd
b157d4669472   hello-world    "/hello"             54 seconds ago   Exited (0) 53 seconds ago                                       trusting_shamir
 ```
 curl でローカルホストの80番にアクセスすると「It works!」と表示さます。
 
 `curl  http://localhost:80/ `{{execute}}
 
 ```text
$ curl  http://localhost:80/
<html><body><h1>It works!</h1></body></html>
 ```
 
 ブラウザで確認する場合は以下をクリックしてください。「It works!」という画面が表示されます。
 
 https://[[HOST_SUBDOMAIN]]-80-[[KATACODA_HOST]].environments.katacoda.com/
 
  ![Test Image 1](https://raw.githubusercontent.com/mayumi00/katacoda-scenarios/main/container101/images/image101web1.png)
 
 このように既存のコンテナイメージを使うことで簡単にコンテナを利用できます。

---
**コンテナの操作**
 
 コンテナに対して頻繁に行う操作として、起動以外に開始・停止・削除があります。実際に操作を行ってみましょう。
 
 ![Test Image 1](https://raw.githubusercontent.com/mayumi00/katacoda-scenarios/main/container101/images/image1-4.png)
 
先程のhttpdコンテナイメージから新しいコンテナを作成します。ローカルホストの80番ポートは使用中なので、ローカルホストの81番ポートとコンテナ内の80番ポートをバインドします。コンテナ名はユニークでないといけないので、httpd2とします。
 
 `docker run -d --name httpd2 -p 81:80 httpd:latest `{{execute}}
 
 ```text
$ docker run -d --name httpd2 -p 81:80 httpd:latest
66515ebd0e4c40d014f2650a8ba14e1a1d74b9ec687a7abffc572e775d5d709c
 ```

` docker ps -a `{{execute}}
 
コンテナ一覧を取得すると、httpd2コンテナがSTATUSがUpと表示され、81番で待ちながら稼働していることが確認できます。

```text
$ docker ps -a
CONTAINER ID   IMAGE          COMMAND              CREATED              STATUS                          PORTS                               NAMES
66515ebd0e4c   httpd:latest   "httpd-foreground"   4 seconds ago        Up 3 seconds                    0.0.0.0:81->80/tcp, :::81->80/tcp   httpd2
da36e29f033d   httpd:latest   "httpd-foreground"   40 seconds ago       Up 38 seconds                   0.0.0.0:80->80/tcp, :::80->80/tcp   httpd
b157d4669472   hello-world    "/hello"             About a minute ago   Exited (0) About a minute ago                                       trusting_shamir
 ```
 curl でローカルホストの81番にアクセスするとhttpdコンテナと同様、「It works!」と表示さます。
 
 `curl  http://localhost:81/ `{{execute}}
 
 ```text
$ curl  http://localhost:81/
<html><body><h1>It works!</h1></body></html>
 ```

***コンテナの停止***

稼働中のコンテナを停止するには`docker stop`コマンドでコンテナ名またはコンテナIDを指定します。
 
`docker stop httpd2 `{{execute}}

停止したコンテナ名が表示されます。

```text
$ docker stop httpd2
httpd2
```

`docker ps -a `{{execute}}
 
コンテナ一覧を取得すると、httpd2コンテナのSTATUSがExitedになっており停止していることが確認できます。

```text
$ docker ps -a
CONTAINER ID   IMAGE          COMMAND              CREATED              STATUS                          PORTS                               NAMES
66515ebd0e4c   httpd:latest   "httpd-foreground"   21 seconds ago       Exited (0) 3 seconds ago                                            httpd2
da36e29f033d   httpd:latest   "httpd-foreground"   57 seconds ago       Up 54 seconds                   0.0.0.0:80->80/tcp, :::80->80/tcp   httpd
b157d4669472   hello-world    "/hello"             About a minute ago   Exited (0) About a minute ago                                       trusting_shamir
 ```
 `curl  http://localhost:81/`{{execute}}
 
接続を試みると、稼働していないので接続できません。
 
 ```text
$ curl  http://localhost:81/
curl: (7) Failed to connect to localhost port 81: Connection refused
 ```

***コンテナの開始***

停止中のコンテナを起動するには`docker start`コマンドでコンテナ名またはコンテナIDを指定します。

`docker start httpd2 `{{execute}}

開始されたコンテナ名が表示されます。

```text
$ docker stop httpd2
httpd2
```

`docker ps -a `{{execute}}
 
コンテナ一覧を取得すると、httpd2コンテナのSTATUSがUpになり、81番でアクセス可能になっています。

```text
$ docker ps -a
CONTAINER ID   IMAGE          COMMAND              CREATED              STATUS                     PORTS                               NAMES
66515ebd0e4c   httpd:latest   "httpd-foreground"   45 seconds ago       Up 4 seconds               0.0.0.0:81->80/tcp, :::81->80/tcp   httpd2
da36e29f033d   httpd:latest   "httpd-foreground"   About a minute ago   Up About a minute          0.0.0.0:80->80/tcp, :::80->80/tcp   httpd
b157d4669472   hello-world    "/hello"             2 minutes ago        Exited (0) 2 minutes ago                                       trusting_shamir
 ```
 
 `curl  http://localhost:81/ `{{execute}}
 
 ```text
$ curl  http://localhost:81/
<html><body><h1>It works!</h1></body></html>
 ```
 
![Test Image 1](https://raw.githubusercontent.com/mayumi00/katacoda-scenarios/main/container101/images/image1-5.png)

***コンテナの削除***

不要になったコンテナを削除するには`docker rm`コマンドでコンテナ名またはコンテナIDを指定します。デフォルトでは停止しているコンテナのみ削除可能で、稼働中のコンテナを指定するとエラーになります。稼働状況に関わらず強制的に削除する場合は`-f（or --force）オプション`を利用します。

`docker stop httpd2 `{{execute}}

停止したコンテナ名が表示されます。

```text
$ docker stop httpd2
httpd2
```

`docker ps -a `{{execute}}
 
 コンテナ一覧を取得すると、httpd2コンテナのSTATUSがExitedになっており停止していることが確認できます。
 
```text
$ docker ps -a
CONTAINER ID   IMAGE          COMMAND              CREATED              STATUS                     PORTS                               NAMES
66515ebd0e4c   httpd:latest   "httpd-foreground"   About a minute ago   Exited (0) 3 seconds ago                                       httpd2
da36e29f033d   httpd:latest   "httpd-foreground"   About a minute ago   Up About a minute          0.0.0.0:80->80/tcp, :::80->80/tcp   httpd
b157d4669472   hello-world    "/hello"             2 minutes ago        Exited (0) 2 minutes ago                                       trusting_shamir
 ```

httpd2を削除します。

`docker rm httpd2 `{{execute}}

削除されたコンテナ名が表示されます。

```text
$ docker rm httpd2
httpd2
```

削除実行後、コンテナ一覧を確認すると、-aオプションを付けても表示されず、httpd2が削除されていることがわかります。

`docker ps -a `{{execute}}

```text
$ docker ps -a
CONTAINER ID   IMAGE          COMMAND              CREATED         STATUS                     PORTS                               NAMES
da36e29f033d   httpd:latest   "httpd-foreground"   2 minutes ago   Up 2 minutes               0.0.0.0:80->80/tcp, :::80->80/tcp   httpd
b157d4669472   hello-world    "/hello"             2 minutes ago   Exited (0) 2 minutes ago                                       trusting_shamir
 ```
  ![Test Image 1](https://raw.githubusercontent.com/mayumi00/katacoda-scenarios/main/container101/images/image1-6.png)

 
 このように、コンテナが簡単に起動できることを確認したところで、次のステップに進みます。

---
###  このステップで利用したdockerコマンド

- docker run [OPTIONS] IMAGE [COMMAND] [ARG...]
  - コンテナを起動する
- docker start [OPTIONS] CONTAINER [CONTAINER...]
   - 停止しているコンテナを起動する
- docker stop [OPTIONS] CONTAINER [CONTAINER...]
   - 実行中のコンテナを停止する
- docker images [OPTIONS] [REPOSITORY[:TAG]]
  - コンテナイメージ一覧を表示する
- docker ps [OPTIONS]
  -  コンテナ一覧を表示する
- docker search [OPTIONS] TERM
  - Docker Hub上のイメージを検索する



 