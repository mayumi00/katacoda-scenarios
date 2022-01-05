## コンテナへのアプリケーションの導入

コンテナにアプリケーションをインストールして、アプリの動作確認を行います。

このステップでは以下を実施します。

- CentOSコンテナを80番ポート接続可能な状態で起動
- CentOSコンテナにhttpd（Apache HTTP Server）をインストール
- テスト用のhtmlをhttpdのドキュメントルート配下に配置
- コンテナ内でのhttpdの動作を確認
- ローカルホスト（自ホスト）でのhttpdの動作を確認

---
**CentOSコンテナを80番ポート接続可能な状態で起動**

![Test Image 1](https://raw.githubusercontent.com/mayumi00/katacoda-scenarios/main/container101/images/image3-1.png)　

ベースとなるCentOSコンテナイメージはstep2と同じイメージを利用し、step2で生成したコンテナとは異なるコンテナを生成します。ここではコンテナ名として`mycentos02`としています。アプリケーションをインストールしたいので、bashを利用してコンテナの操作を行えるようにします。また「CentOSコンテナを80番ポート接続可能な状態」にするために`-p（or --publish）オプション`を利用します。コンテナ内でhttpdはTCP80で起動するので、ローカルホスト（自ホスト）の8080番ポートとコンテナ内の80番ポートをバインドします。

`docker run -p 8080:80 -it --name mycentos02 centos /bin/bash`{{execute}}

```text
$ docker run -p 8080:80 -it --name mycentos02 centos /bin/bash
[root@fb322537fce3 /]#
```

---
**CentOSコンテナにhttpd（Apache HTTP Server）をインストール**

コンテナ内でhttpdをインストールします。

`dnf install -y httpd`{{execute}}

```text
$ docker run -p 8080:80 -it --name mycentos02 centos /bin/bash
[root@fb322537fce3 /]# dnf install -y httpd
Failed to set locale, defaulting to C.UTF-8
CentOS Linux 8 - AppStream                                                                22 MB/s | 8.4 MB     00:00    
CentOS Linux 8 - BaseOS                                                                   11 MB/s | 4.6 MB     00:00    
CentOS Linux 8 - Extras                                                                   35 kB/s |  10 kB     00:00    
Dependencies resolved.
（略）                                                                                            
  mod_http2-1.15.7-3.module_el8.4.0+778+c970deab.x86_64                                                                  

Complete!
```

httpdがインストールされたので、httpd.confの中でServerNameのコメントをはずしlocalhostに書き換えます。

`sed -i -e "s/#ServerName www.example.com/ServerName localhost/" /etc/httpd/conf/httpd.conf`{{execute}}

---
**テスト用のhtmlをhttpdのドキュメントルート配下に配置**
 
ドキュメントルートにコンテンツを配置します。今回は、コンテナ内で完結するために、echoコマンドでファイルを生成しています。

`echo "<head><title>Apache on Docker Container</title></head><body><H1>Container 101 - Web</H1>Apache on Docker Container</body>"  > /var/www/html/index.html `{{execute}}

---
**コンテナ内でのhttpdの動作を確認**

httpdを起動します。ここで「systemctlを使わないのか？」とツッコまれそうですが、訳あって使いません。その説明は別途します。

`/usr/sbin/httpd`{{execute}}

コンテナ内でhttpdが起動しているか確認します。

 `ps f -e`{{execute}}
 
 ```text
[root@fb322537fce3 /]# ps f -e
    PID TTY      STAT   TIME COMMAND
      1 pts/0    Ss     0:00 /bin/bash
     86 ?        Ss     0:00 /usr/sbin/httpd
     87 ?        S      0:00  \_ /usr/sbin/httpd
     88 ?        Sl     0:00  \_ /usr/sbin/httpd
     89 ?        Sl     0:00  \_ /usr/sbin/httpd
     91 ?        Sl     0:00  \_ /usr/sbin/httpd
    302 pts/0    R+     0:00 ps f -e
 ```

コンテナ内で、curlコマンドで先程作成したindex.htmlが表示されるか確認します。
 
 `curl http://localhost/index.html`{{execute}}

```text
[root@fb322537fce3 /]# curl http://localhost/index.html
<head><title>Apache on Docker Container</title></head><body><H1>Container 101 - Web</H1>Apache on Docker Container</body>
 ```

---
**ローカルホスト（自ホスト）でのhttpdの動作を確認**

![Test Image 1](https://raw.githubusercontent.com/mayumi00/katacoda-scenarios/main/container101/images/image3-2.png)

ここで一旦コンテナから抜けます。

 `exit`{{execute}}

runで実行したコンテナからexitするとコンテナが停止してしまいます。

`docker ps -a`{{execute}}

```text
$ docker ps -a
CONTAINER ID   IMAGE          COMMAND              CREATED          STATUS                      PORTS                               NAMES
fb322537fce3   centos         "/bin/bash"          32 seconds ago   Exited (0) 2 seconds ago                                        mycentos02
7b51274d250c   centos         "/bin/bash"          2 minutes ago    Up 41 seconds                                                   mycentos01
da36e29f033d   httpd:latest   "httpd-foreground"   11 minutes ago   Up 11 minutes               0.0.0.0:80->80/tcp, :::80->80/tcp   httpd
b157d4669472   hello-world    "/hello"             12 minutes ago   Exited (0) 12 minutes ago                                       trusting_shamir
```
コンテナを開始して、execコマンドでbashを実行しコンテナ内を確認します。

`docker start mycentos02`{{execute}}

`docker exec -it mycentos02 /bin/bash`{{execute}}

コンテナ内のプロセスを確認します。

`ps f -e`{{execute}}
 
```text
[root@fb322537fce3 /]# ps f -e
    PID TTY      STAT   TIME COMMAND
     15 pts/1    Ss     0:00 /bin/bash
     29 pts/1    R+     0:00  \_ ps f -e
      1 pts/0    Ss+    0:00 /bin/bash
 ```

httpdは、コンテナ停止とともに停止してしまったので、プロセスはありません。 先程作ったindex.htmlを確認します。コンテナが停止しても、作成したファイルは影響を受けません。
 
 `cat /var/www/html/index.html `{{execute}}
 
 ```text
<head><title>Apache on Docker Container</title></head><body><H1>Container 101 - Web</H1>Apache on Docker Container</body>
 ```
httpdを起動します。

`/usr/sbin/httpd`{{execute}}

httpdが起動したか確認します。

`ps f -e`{{execute}}
 
```text
[root@fb322537fce3 /]# ps f -e
    PID TTY      STAT   TIME COMMAND
     15 pts/1    Ss     0:00 /bin/bash
    248 pts/1    R+     0:00  \_ ps f -e
      1 pts/0    Ss+    0:00 /bin/bash
     32 ?        Ss     0:00 /usr/sbin/httpd
     33 ?        S      0:00  \_ /usr/sbin/httpd
     34 ?        Sl     0:00  \_ /usr/sbin/httpd
     35 ?        Sl     0:00  \_ /usr/sbin/httpd
     36 ?        Sl     0:00  \_ /usr/sbin/httpd
```
  
コンテナから抜けます。

`exit`{{execute}}

コンテナの起動状態確認を確認します。

`docker ps -a`{{execute}}

```text
$ docker ps -a
CONTAINER ID   IMAGE          COMMAND              CREATED          STATUS                      PORTS                                   NAMES
fb322537fce3   centos         "/bin/bash"          57 seconds ago   Up 18 seconds               0.0.0.0:8080->80/tcp, :::8080->80/tcp   mycentos02
7b51274d250c   centos         "/bin/bash"          3 minutes ago    Up About a minute                                                   mycentos01
da36e29f033d   httpd:latest   "httpd-foreground"   12 minutes ago   Up 12 minutes               0.0.0.0:80->80/tcp, :::80->80/tcp       httpd
b157d4669472   hello-world    "/hello"             12 minutes ago   Exited (0) 12 minutes ago                                           trusting_shamir
```
execからexitしたので、mycentos02コンテナは停止せず80番で待ち受けしており、自ホストの8080番にアクセスすれば接続できるはずです。先程はコンテナ内でcurlで確認しましたが、今度は自ホストでcurlで確認します。ローカルホストの8080番ポートとコンテナ内の80番ポートをバインドしてあるので、8080番ポートへアクセスします。

`curl http://localhost:8080/index.html`{{execute}}

ブラウザで確認する場合は以下をクリックしてください。index.htmlの内容が表示されます。
 
https://[[HOST_SUBDOMAIN]]-8080-[[KATACODA_HOST]].environments.katacoda.com/

 ![Test Image 1](https://raw.githubusercontent.com/mayumi00/katacoda-scenarios/main/container101/images/image101web2.png)
 
---

以上がCentOSのイメージをベースにアプリケーション（Apache HTTP Server）をインストールする流れとなります。

