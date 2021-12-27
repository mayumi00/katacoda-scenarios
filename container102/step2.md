## ビルドされたイメージの利用例

step1で、Dockerfileを利用することでインストールや設定作業が自動化できることを体験できたと思います。このように作成したコンテナイメージから同一のコンテナを大量に扱うことなどが可能になります。


---
**複数のコンテナの起動**

同じ設定を持つコンテナを複数個起動したい場合などはDockerfileを使って作成したコンテナイメージからコンテンを起動すれば良いわけです。以下の例は、apacheweb-dockerfile:1.0から9個のコンテナを起動しています。


` for i in {1..9} ; do  echo testweb0${i} ; docker run -d -p 808${i}:80 --name testweb0${i} apacheweb-dockerfile:1.0 ; done `{{execute}}

```text
$ for i in {1..9} ; do  echo testweb0${i} ; docker run -d -p 808${i}:80 --name testweb0${i} apacheweb-dockerfile:1.0 ; done
testweb01
3f2c0f3eda01d5cf345acddcf167825fcc9138a95721ca4b2d9786debb991410
testweb02
57403617f93f0374ec711343647c286512f67fa6f220b0c49734731c68be7aa3
testweb03
02c76f7d9c5de1a788c8888668fc108312e493df1d97199ee668602eee3d55d5
testweb04
b26e7070b45a375857406f3eb5890314f2b40c3dcd7026bd1a0dc9e288db0226
testweb05
8fdef0cdd27d881425020535fb07c7ae517d1bf9777817689d188f8dff242269
testweb06
13d6eeb16dd80139498ef52d2a2663d6689abf8c46190a8bb73413659a6b4672
testweb07
3c53983b694a8802c8c4baf4611687cee728674708c4e8ba3e3c5c8090099f3c
testweb08
7cbac08014c6c97066ede9bfc23c5cb3aca98fba30562702fad5fa5bcd1bdc4a
testweb09
5660c3ddb092f317104103542ea88296c0f58d35707c8911f3d72987e020b9bb
```

図のように、Dockerfileからビルドされたapacheweb-dockerfile:1.0を元に同じコンテナが9個起動してます。

![Test Image 1](https://raw.githubusercontent.com/mayumi00/katacoda-scenarios/main/container102/images/image2-1.png)

`docker ps -a`{{execute}}

コンテナ一覧を見ると、step1で起動したtestweb00とその後起動したtestweb01〜testweb09が起動していることがわかります。

```text
$ docker ps -a
CONTAINER ID   IMAGE                      COMMAND                  CREATED              STATUS              PORTS                                   NAMES
5660c3ddb092   apacheweb-dockerfile:1.0   "/usr/sbin/httpd -DF…"   11 seconds ago       Up 10 seconds       0.0.0.0:8089->80/tcp, :::8089->80/tcp   testweb09
7cbac08014c6   apacheweb-dockerfile:1.0   "/usr/sbin/httpd -DF…"   11 seconds ago       Up 10 seconds       0.0.0.0:8088->80/tcp, :::8088->80/tcp   testweb08
3c53983b694a   apacheweb-dockerfile:1.0   "/usr/sbin/httpd -DF…"   12 seconds ago       Up 11 seconds       0.0.0.0:8087->80/tcp, :::8087->80/tcp   testweb07
13d6eeb16dd8   apacheweb-dockerfile:1.0   "/usr/sbin/httpd -DF…"   13 seconds ago       Up 12 seconds       0.0.0.0:8086->80/tcp, :::8086->80/tcp   testweb06
8fdef0cdd27d   apacheweb-dockerfile:1.0   "/usr/sbin/httpd -DF…"   13 seconds ago       Up 12 seconds       0.0.0.0:8085->80/tcp, :::8085->80/tcp   testweb05
b26e7070b45a   apacheweb-dockerfile:1.0   "/usr/sbin/httpd -DF…"   14 seconds ago       Up 13 seconds       0.0.0.0:8084->80/tcp, :::8084->80/tcp   testweb04
02c76f7d9c5d   apacheweb-dockerfile:1.0   "/usr/sbin/httpd -DF…"   15 seconds ago       Up 14 seconds       0.0.0.0:8083->80/tcp, :::8083->80/tcp   testweb03
57403617f93f   apacheweb-dockerfile:1.0   "/usr/sbin/httpd -DF…"   16 seconds ago       Up 14 seconds       0.0.0.0:8082->80/tcp, :::8082->80/tcp   testweb02
3f2c0f3eda01   apacheweb-dockerfile:1.0   "/usr/sbin/httpd -DF…"   16 seconds ago       Up 15 seconds       0.0.0.0:8081->80/tcp, :::8081->80/tcp   testweb01
d92f2cf8234f   apacheweb-dockerfile:1.0   "/usr/sbin/httpd -DF…"   About a minute ago   Up About a minute   0.0.0.0:8080->80/tcp, :::8080->80/tcp   testweb00
```

---
**複数のコンテナの動作確認**

`for i in {1..9} ; do echo testweb0${i} ; curl http://localhost:808${i} ; done`{{execute}}

```text
$ for i in {1..9} ; do echo testweb0${i} ; curl http://localhost:808${i} ; done
testweb01
<head><title>Apache on Docker Container</title></head><body><H1>Container 102 - Web</H1>Apache on Docker Container using Dockerfile</body>
testweb02
<head><title>Apache on Docker Container</title></head><body><H1>Container 102 - Web</H1>Apache on Docker Container using Dockerfile</body>
testweb03
<head><title>Apache on Docker Container</title></head><body><H1>Container 102 - Web</H1>Apache on Docker Container using Dockerfile</body>
testweb04
<head><title>Apache on Docker Container</title></head><body><H1>Container 102 - Web</H1>Apache on Docker Container using Dockerfile</body>
testweb05
<head><title>Apache on Docker Container</title></head><body><H1>Container 102 - Web</H1>Apache on Docker Container using Dockerfile</body>
testweb06
<head><title>Apache on Docker Container</title></head><body><H1>Container 102 - Web</H1>Apache on Docker Container using Dockerfile</body>
testweb07
<head><title>Apache on Docker Container</title></head><body><H1>Container 102 - Web</H1>Apache on Docker Container using Dockerfile</body>
testweb08
<head><title>Apache on Docker Container</title></head><body><H1>Container 102 - Web</H1>Apache on Docker Container using Dockerfile</body>
testweb09
<head><title>Apache on Docker Container</title></head><body><H1>Container 102 - Web</H1>Apache on Docker Container using Dockerfile</body>
```


---
**複数のコンテナの停止**

次のステップに進む前に、大量に起動してしまったコンテナを停止し削除します。

> Note: コンテナイメージは削除しません。

testweb01からtestweb09を停止します。

` for i in {1..9} ; do docker stop testweb0${i} ; done`{{execute}}

```text
$ for i in {1..9} ; do docker stop testweb0${i} ; done
testweb01
testweb02
testweb03
testweb04
testweb05
testweb06
testweb07
testweb08
testweb09
```

停止したことを確認します。

`docker ps -a`{{execute}}

```text
$ docker ps -a
CONTAINER ID   IMAGE                      COMMAND                  CREATED              STATUS                     PORTS                                   NAMES
5660c3ddb092   apacheweb-dockerfile:1.0   "/usr/sbin/httpd -DF…"   35 seconds ago       Exited (0) 5 seconds ago                                           testweb09
7cbac08014c6   apacheweb-dockerfile:1.0   "/usr/sbin/httpd -DF…"   35 seconds ago       Exited (0) 5 seconds ago                                           testweb08
3c53983b694a   apacheweb-dockerfile:1.0   "/usr/sbin/httpd -DF…"   36 seconds ago       Exited (0) 5 seconds ago                                           testweb07
13d6eeb16dd8   apacheweb-dockerfile:1.0   "/usr/sbin/httpd -DF…"   37 seconds ago       Exited (0) 6 seconds ago                                           testweb06
8fdef0cdd27d   apacheweb-dockerfile:1.0   "/usr/sbin/httpd -DF…"   37 seconds ago       Exited (0) 6 seconds ago                                           testweb05
b26e7070b45a   apacheweb-dockerfile:1.0   "/usr/sbin/httpd -DF…"   38 seconds ago       Exited (0) 6 seconds ago                                           testweb04
02c76f7d9c5d   apacheweb-dockerfile:1.0   "/usr/sbin/httpd -DF…"   39 seconds ago       Exited (0) 7 seconds ago                                           testweb03
57403617f93f   apacheweb-dockerfile:1.0   "/usr/sbin/httpd -DF…"   40 seconds ago       Exited (0) 7 seconds ago                                           testweb02
3f2c0f3eda01   apacheweb-dockerfile:1.0   "/usr/sbin/httpd -DF…"   40 seconds ago       Exited (0) 7 seconds ago                                           testweb01
d92f2cf8234f   apacheweb-dockerfile:1.0   "/usr/sbin/httpd -DF…"   About a minute ago   Up About a minute          0.0.0.0:8080->80/tcp, :::8080->80/tcp   testweb00
```
![Test Image 1](https://raw.githubusercontent.com/mayumi00/katacoda-scenarios/main/container102/images/image2-2.png)

---
**複数のコンテナの削除**

testweb00以外は停止（Exited）していることがわかります。続いて、停止したtestweb01からtestweb09を削除します。

`for i in {1..9} ; do docker rm testweb0${i} ; done`{{execute}}

```text
$ for i in {1..9} ; do docker rm testweb0${i} ; done
testweb01
testweb02
testweb03
testweb04
testweb05
testweb06
testweb07
testweb08
testweb09
```
コンテナ一覧を確認すると停止したtestweb01からtestweb09までが一覧から消えていることが確認できます。

`docker ps -a`{{execute}}

```text
$ docker ps -a
CONTAINER ID   IMAGE                      COMMAND                  CREATED              STATUS              PORTS                                   NAMES
d92f2cf8234f   apacheweb-dockerfile:1.0   "/usr/sbin/httpd -DF…"   About a minute ago   Up About a minute   0.0.0.0:8080->80/tcp, :::8080->80/tcp   testweb00

![Test Image 1](https://raw.githubusercontent.com/mayumi00/katacoda-scenarios/main/container102/images/image2-3.png)

```


コンテナの操作性というものの良さをちょっとは感じられるのではないでしょうか？
