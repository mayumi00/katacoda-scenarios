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

httpdを起動します。ここで「systemctl start httpdを使わないのか？」とツッコまれそうですが、訳あって使いません。その説明は別途します。

`/usr/sbin/httpd`{{execute}}

コンテナ内でhttpdが起動しているか確認します。

 `ps f -e`{{execute}}
 
 ```text
[root@5f04e1706a34 /]# ps f -e
UID          PID    PPID  C STIME TTY          TIME CMD
root           1       0  0 01:04 pts/0    00:00:00 /bin/bash
root          85       1  0 01:08 ?        00:00:00 /usr/sbin/httpd
apache        86      85  0 01:08 ?        00:00:00 /usr/sbin/httpd
apache        87      85  0 01:08 ?        00:00:00 /usr/sbin/httpd
apache        88      85  0 01:08 ?        00:00:00 /usr/sbin/httpd
apache       113      85  0 01:08 ?        00:00:00 /usr/sbin/httpd
root         303       1  0 01:08 pts/0    00:00:00 ps -ef
 ```
 
 curlコマンドで先程作成したindex.htmlが表示されるか確認します。
 
 `curl http://localhost/index.html`{{execute}}

```text
CONTAINER ID   IMAGE         COMMAND    CREATED         STATUS                     PORTS     NAMES
655c23e742bf   hello-world   "/hello"   8 minutes ago   Exited (0) 8 minutes ago             elegant_yonath
 ```

ここで一旦コンテナから抜けます。

 `exit`{{execute}}

runで実行したコンテナからexitするとコンテナが停止してしまいます。なので、コンテナをスタートして、execコマンドでbashを実行しコンテナ内を確認します。

`docker exec -it mycentos2 /bin/bash`{{execute}}
`ps f -e`{{execute}}
 
```text
[root@5f04e1706a34 /]# ps -ef
UID          PID    PPID  C STIME TTY          TIME CMD
root           1       0  0 01:12 pts/0    00:00:00 /bin/bash
root          17       0  0 01:12 pts/1    00:00:00 /bin/bash
root          32      17  0 01:12 pts/1    00:00:00 ps -ef
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
[root@5f04e1706a34 /]# ps f -e
UID          PID    PPID  C STIME TTY          TIME CMD
root           1       0  0 01:12 pts/0    00:00:00 /bin/bash
root          17       0  0 01:12 pts/1    00:00:00 /bin/bash
root          32      17  0 01:12 pts/1    00:00:00 ps -ef
```
  
コンテナから抜けます。

`exit`{{execute}}

コンテナの起動状態確認
`docker ps -a`{{execute}}

```text
CONTAINER ID   IMAGE         COMMAND       CREATED          STATUS                      PORTS                               NAMES
5f04e1706a34   centos        "/bin/bash"   11 minutes ago   Up 3 minutes                0.0.0.0:80->80/tcp, :::80->80/tcp   mycentos2
879a5fb64810   centos        "/bin/bash"   25 minutes ago   Up 24 minutes                                                   mycentos1
10387c60ceb4   hello-world   "/hello"      27 minutes ago   Exited (0) 27 minutes ago                                       crazy_nobel
```
mycentos2コンテナが稼働しており、80番で待ち受けしてます。

`curl http://localhost:8080/index.html`{{execute}}

https://[[HOST_SUBDOMAIN]]-8080-[[KATACODA_HOST]].environments.katacoda.com/

`ps f -e`{{execute}}

```text
CONTAINER ID   IMAGE         COMMAND       CREATED          STATUS                      PORTS                               NAMES
5f04e1706a34   centos        "/bin/bash"   11 minutes ago   Up 3 minutes                0.0.0.0:80->80/tcp, :::80->80/tcp   mycentos2
879a5fb64810   centos        "/bin/bash"   25 minutes ago   Up 24 minutes                                                   mycentos1
10387c60ceb4   hello-world   "/hello"      27 minutes ago   Exited (0) 27 minutes ago                                       crazy_nobel
```