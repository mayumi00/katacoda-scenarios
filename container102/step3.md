# コンテナへのアプリケーションの導入

![Test Image 1](https://raw.githubusercontent.com/mayumi00/katacoda-scenarios/main/container101/images/image01.png)　

実施すること
- CentOSコンテナを80番ポート接続可能な状態で起動する
- CentOSコンテナにhttpdをdnf installする
- テスト用のhtmlをhttpdのドキュメントルート配下に配置する
- ローカルホストにアクセスしてhttpdの動作を確認する
- ブラウザでの接続を確認する

ベースとなるCentOSはステップ２と同じイメージを利用し、ステップ２で生成したコンテナとは異なるコンテナを生成します。ここではコンテナ名として`mycentos2`としています。アプリをインストールしたいので、bashを利用してコンテナの操作を行えるようにします。また「CentOSコンテナを80番ポート接続可能な状態」にするために`-p（or --publish）オプション`を利用します。コンテナ内ではhttpdはTCP80で起動するので、コンテナ内の80番ポートとローカルホスト（自ホスト）の8080番ポートをバインドします。

`docker run -p 8080:80 -it --name mycentos2 centos /bin/bash`{{execute}}

```text
[root@0893cd3e1c07 /]#
```

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

httpd.confの中でServerNameのコメントをはずしlocalhostに書き換えます。
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
 
 curlコマンドで先程作成したindex.htmlが表示されるか確認します。
 
 `curl http://localhost/index.html`{{execute}}

```text
CONTAINER ID   IMAGE         COMMAND    CREATED         STATUS                     PORTS     NAMES
655c23e742bf   hello-world   "/hello"   8 minutes ago   Exited (0) 8 minutes ago             elegant_yonath
 ```

ここで一旦コンテナから抜けます。

 `exit`{{execute}}

runで実行したコンテナからexitするとコンテナが停止してしまいます。なので、コンテナを開始して、execコマンドでbashを実行しコンテナ内を確認します。

`docker ps -a`{{execute}}
`docker start mycentos2`{{execute}}
`docker exec -it mycentos2 /bin/bash`{{execute}}
`ps f -e`{{execute}}
 
```text
[root@f29637beaac6 /]# ps f -e
    PID TTY      STAT   TIME COMMAND
     15 pts/1    Ss     0:00 /bin/bash
     30 pts/1    R+     0:00  \_ ps f -e
      1 pts/0    Ss+    0:00 /bin/bash
 ```

 httpdは、コンテナ停止とともに停止してしまったので、プロセスはありません。
 
 先程作ったindex.htmlを確認します。
 
 `cat /var/www/html/index.html `{{execute}}
 
 ```text
<head><title>Apache on Docker Container</title></head><body>Apache on Docker Container</body>
 ```
httpdを起動します。

`/usr/sbin/httpd`{{execute}}
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

execからexitしたので、mycentos2コンテナは停止せず80番で待ち受けしており、自ホストの8080にアクセスすれば接続できるはずです。

```text
$ docker ps -a
CONTAINER ID   IMAGE     COMMAND       CREATED         STATUS          PORTS                                   NAMES
7ec897a6ca6c   centos    "/bin/bash"   2 minutes ago   Up 38 seconds   0.0.0.0:8080->80/tcp, :::8080->80/tcp   mycentos2
```

先程はコンテナ内でcurlで確認します、今度は自ホストでcurlで確認します。ポート番号は8080です。
`curl http://localhost:8080/index.html`{{execute}}

curlだけでなく、このkatacodaの仕組みを使ってブラウザで表示することも可能です。

https://[[HOST_SUBDOMAIN]]-8080-[[KATACODA_HOST]].environments.katacoda.com/

おまけ：自ホストから見たときのプロセスの起動状態

`ps f -e`{{execute}}

```text
    683 ?        Ssl    0:06 /usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock
   1458 ?        Sl     0:00  \_ /usr/bin/docker-proxy -proto tcp -host-ip 0.0.0.0 -host-port 8080 -container-ip 172.18.0
   1464 ?        Sl     0:00  \_ /usr/bin/docker-proxy -proto tcp -host-ip :: -host-port 8080 -container-ip 172.18.0.2 -c
    874 ?        Ss     0:00 /lib/systemd/systemd --user
    875 ?        S      0:00  \_ (sd-pam)
   1481 ?        Sl     0:00 /usr/bin/containerd-shim-runc-v2 -namespace moby -id 7ec897a6ca6c1067348cd0d2913cb8adfed98fc
   1501 pts/0    Ss+    0:00  \_ /bin/bash
   1600 ?        Ss     0:00      \_ /usr/sbin/httpd
   1601 ?        S      0:00          \_ /usr/sbin/httpd
   1602 ?        Sl     0:00          \_ /usr/sbin/httpd
   1603 ?        Sl     0:00          \_ /usr/sbin/httpd
   1604 ?        Sl     0:00          \_ /usr/sbin/httpd
```