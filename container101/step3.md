### コンテナへのアプリケーションの導入

コンテナにアプリケーションをインストールして、アプリの動作確認を行います。

このステップでは以下を実施します。

- CentOSコンテナを80番ポート接続可能な状態で起動する
- CentOSコンテナにhttpd（Apache HTTP Server）をインストールする
- テスト用のhtmlをhttpdのドキュメントルート配下に配置する
- ローカルホストにアクセスしてhttpdの動作を確認する

![Test Image 1](https://raw.githubusercontent.com/mayumi00/katacoda-scenarios/main/container101/images/image04.png)　

ベースとなるCentOSコンテナイメージはstep2と同じイメージを利用し、step2で生成したコンテナとは異なるコンテナを生成します。ここではコンテナ名として`mycentos02`としています。アプリケーションをインストールしたいので、bashを利用してコンテナの操作を行えるようにします。また「CentOSコンテナを80番ポート接続可能な状態」にするために`-p（or --publish）オプション`を利用します。コンテナ内でhttpdはTCP80で起動するので、ローカルホスト（自ホスト）の8080番ポートとコンテナ内の80番ポートをバインドします。

`docker run -p 8080:80 -it --name mycentos02 centos /bin/bash`{{execute}}

```text
[root@0893cd3e1c07 /]#
```
コンテナ内でhttpdをインストールします。

`dnf install -y httpd`{{execute}}

```text
[root@5f04e1706a34 /]# dnf install -y httpd
Failed to set locale, defaulting to C.UTF-8
CentOS Linux 8 - AppStream                                                                17 MB/s | 8.2 MB     00:00    
CentOS Linux 8 - BaseOS                                                                  6.6 MB/s | 3.5 MB     00:00    
CentOS Linux 8 - Extras                                                                  100 kB/s |  10 kB     00:00    
Dependencies resolved.
（略）
Installed:
  apr-1.6.3-12.el8.x86_64                                                                                                
  apr-util-1.6.1-6.el8.x86_64                                                                                            
  apr-util-bdb-1.6.1-6.el8.x86_64                                                                                        
  apr-util-openssl-1.6.1-6.el8.x86_64                                                                                    
  brotli-1.0.6-3.el8.x86_64                                                                                              
  centos-logos-httpd-85.8-2.el8.noarch                                                                                   
  httpd-2.4.37-43.module_el8.5.0+1022+b541f3b1.x86_64                                                                    
  httpd-filesystem-2.4.37-43.module_el8.5.0+1022+b541f3b1.noarch                                                         
  httpd-tools-2.4.37-43.module_el8.5.0+1022+b541f3b1.x86_64                                                              
  mailcap-2.1.48-3.el8.noarch                                                                                            
  mod_http2-1.15.7-3.module_el8.4.0+778+c970deab.x86_64                                                                  

Complete!
```

httpdがインストールされたので、httpd.confの中でServerNameのコメントをはずしlocalhostに書き換えます。
`sed -i -e "s/#ServerName www.example.com/ServerName localhost/" /etc/httpd/conf/httpd.conf`{{execute}}
 
ドキュメントルートにコンテンツを配置します。今回は、コンテナ内で完結するために、echoコマンドでファイルを生成しています。

`echo "<head><title>Apache on Docker Container</title></head><body><H1>Container 101 - Web</H1>Apache on Docker Container</body>"  > /var/www/html/index.html `{{execute}}

httpdを起動します。ここで「systemctlを使わないのか？」とツッコまれそうですが、訳あって使いません。その説明は別途します。

`/usr/sbin/httpd`{{execute}}

コンテナ内でhttpdが起動しているか確認します。

 `ps f -e`{{execute}}
 
 ```text
[root@f29637beaac6 /]# ps f -e
    PID TTY      STAT   TIME COMMAND
      1 pts/0    Ss     0:00 /bin/bash
     86 ?        Ss     0:00 /usr/sbin/httpd
     87 ?        S      0:00  \_ /usr/sbin/httpd
     88 ?        Sl     0:00  \_ /usr/sbin/httpd
     89 ?        Sl     0:00  \_ /usr/sbin/httpd
     90 ?        Sl     0:00  \_ /usr/sbin/httpd
    305 pts/0    R+     0:00 ps f -e
 ```

コンテナ内で、curlコマンドで先程作成したindex.htmlが表示されるか確認します。
 
 `curl http://localhost/index.html`{{execute}}

```text
CONTAINER ID   IMAGE         COMMAND    CREATED         STATUS                     PORTS     NAMES
655c23e742bf   hello-world   "/hello"   8 minutes ago   Exited (0) 8 minutes ago             elegant_yonath
 ```

ここで一旦コンテナから抜けます。

 `exit`{{execute}}

runで実行したコンテナからexitするとコンテナが停止してしまいます。

`docker ps -a`{{execute}}

```text
CONTAINER ID   IMAGE         COMMAND    CREATED         STATUS                     PORTS     NAMES
655c23e742bf   hello-world   "/hello"   8 minutes ago   Exited (0) 8 minutes ago             elegant_yonath
```
コンテナを開始して、execコマンドでbashを実行しコンテナ内を確認します。

`docker start mycentos02`{{execute}}
`docker exec -it mycentos02 /bin/bash`{{execute}}

コンテナ内のプロセスを確認します。

`ps f -e`{{execute}}
 
```text
[root@f29637beaac6 /]# ps f -e
    PID TTY      STAT   TIME COMMAND
     15 pts/1    Ss     0:00 /bin/bash
     30 pts/1    R+     0:00  \_ ps f -e
      1 pts/0    Ss+    0:00 /bin/bash
 ```

httpdは、コンテナ停止とともに停止してしまったので、プロセスはありません。 先程作ったindex.htmlを確認します。コンテナが停止しても、作成したファイルは影響を受けません。
 
 `cat /var/www/html/index.html `{{execute}}
 
 ```text
<head><title>Apache on Docker Container</title></head><body>Apache on Docker Container</body>
 ```
httpdを起動します。

`/usr/sbin/httpd`{{execute}}

httpdが起動したか確認します。

`ps f -e`{{execute}}
 
```text
[root@7ec897a6ca6c /]# /usr/sbin/httpd
[root@7ec897a6ca6c /]# ps f -e
    PID TTY      STAT   TIME COMMAND
     16 pts/1    Ss     0:00 /bin/bash
    250 pts/1    R+     0:00  \_ ps f -e
      1 pts/0    Ss+    0:00 /bin/bash
     34 ?        Ss     0:00 /usr/sbin/httpd
     35 ?        S      0:00  \_ /usr/sbin/httpd
     36 ?        Sl     0:00  \_ /usr/sbin/httpd
     37 ?        Sl     0:00  \_ /usr/sbin/httpd
     38 ?        Sl     0:00  \_ /usr/sbin/httpd
```
  
コンテナから抜けます。

`exit`{{execute}}

コンテナの起動状態確認を確認します。

`docker ps -a`{{execute}}

```text
$ docker ps -a
CONTAINER ID   IMAGE     COMMAND       CREATED         STATUS          PORTS                                   NAMES
7ec897a6ca6c   centos    "/bin/bash"   2 minutes ago   Up 38 seconds   0.0.0.0:8080->80/tcp, :::8080->80/tcp   mycentos02
```
execからexitしたので、mycentos02コンテナは停止せず80番で待ち受けしており、自ホストの8080番にアクセスすれば接続できるはずです。先程はコンテナ内でcurlで確認しましたが、今度は自ホストでcurlで確認します。ローカルホスト（自ホスト）の8080番ポートとコンテナ内の80番ポートをバインドしてあるので、8080番ポートへアクセスします。

`curl http://localhost:8080/index.html`{{execute}}

curlだけでなく、このkatacodaの仕組みを使ってブラウザで表示することも可能です。

https://[[HOST_SUBDOMAIN]]-8080-[[KATACODA_HOST]].environments.katacoda.com/

![Test Image 1](https://raw.githubusercontent.com/mayumi00/katacoda-scenarios/main/container101/images/image05.png)

以上がCentOSのイメージをベースにアプリケーション（Apache HTTP Server）をインストールする流れとなります。

