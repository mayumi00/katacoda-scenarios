# コンテナとは

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

このコマンドで実施されたことは以下の２つの操作です。

自ホスト上では「hello-world」というイメージが見つけられなかったので、コンテナレジストリから、最新の「hello-world」イメージを持ってくる処理をします（docker pull）。図の左にある既存のコンテナレジストリ（代表的なものはDocker Hubです）からpullして、自ホストのイメージ格納領域に保存します。

```text
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
2db29710123e: Pull complete 
Digest: sha256:cc15c5b292d8525effc0f89cb299f1804f3a725c8d05e158653a563f15e4f685
Status: Downloaded newer image for hello-world:latest
```

自ホストに格納されたイメージからコンテナを起動します（docker run）

```text
Hello from Docker!
This message shows that your installation appears to be working correctly.
（略）
For more examples and ideas, visit:
 https://docs.docker.com/get-started/
 ```
 
  自ホストにhello-worldのイメージを持ってくる処理をしましたが、イメージが格納されているか確認してみましょう。実は、この環境では既にいくつかのコンテナイメージが準備されています。
  

 
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
| CREATED | イメージが作成された時刻（ 自ホストに持ってきた日ではありません） |
| SIZE | イメージのサイズ |

 ここで、コンテナの一覧を見てみましょう。-aオプションを付けると、起動していないコンテナも含めてすべてを表示します。-aを付けないデフォルトの場合は、実行中のコンテナーのみが表示されます。hello-worldコンテナは説明を出力すると終了してしまうので-aオプションを付けないと表示されません。ExitedというSTATUSはコンテナが実行され、終了した状態を指しています。
 
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
| PORTS | プロトコルによる公開ポート |
| NAMES | イメージのサイズ |
 



`docker pull centos:latest`{{execute}}

`docker images`{{execute}}

`docker run -itd --privileged -p 2222:22 --name centos8 --hostname centos8 centos:latest /sbin/init`{{execute}}

`docker ps`{{execute}}

`docker exec -it centos8 /bin/bash`{{execute}}



## Playbook の基礎
---
`playbook` は [YAML](https://ja.wikipedia.org/wiki/YAML) 形式で記述します。YAMLに関して重要なポイントを以下に記載します。

- YAML はデータを表記するためのテキストフォーマットであること。
- ファイルの先頭は `---` から始まる
- インデントが意味を持つ
  - インデントは `space` で表記する。`tab` ではエラーとなります。
- `key`: `value` で辞書形式となる
- [json](https://ja.wikipedia.org/wiki/JavaScript_Object_Notation) と相互に変換可能

以下は playbook のサンプルです。
```yaml
---
- hosts: all
  become: yes
  tasks:
  - name: first task
    yum:
      name: httpd
      state: latest
  - name: second task
    service:
      name: httpd
      state: started
      enabled: yes
```

この内容は、json で表記すると以下のようになります。

```json
[
	{
		"hosts": "all",
		"become": "yes",
		"tasks": [
			{
				"name": "first task",
				"yum": {
					"name": "httpd",
					"state": "latest"
				}
			},
			{
				"name": "second task",
				"service": {
					"name": "httpd",
					"state": "started",
					"enabled": "yes"
				}
			}
		]
	}
]
```

## playbook の作成
---
実際に playbook を作成していきます。

`~/working/first_playbook.yml` をエディタで開いてください。このファイルには先頭に `---` のみが記載されています。以下の説明に従いこのファイルへ追記を行い、playbook として完成させます。

ここでは、WEBサーバーを構築する playbook を作成します。

### play パート
---
以下のようにファイルに追記してください。

```yaml
---
- name: deploy httpd server
  hosts: all
  become: yes
```

ここで記述した内容な以下になります。
- `name:`: ここには、この playbook で行う処理の概要を記載します。省略可能です。日本語を使うことも可能です。
- `hosts: all`: playbook の実行対象となるグループやノードを指定します。これは利用するインベントリー内に存在しているグループやノードである必要があります。
- `become: yes`: この playbook では実行時に特権ユーザーへの権限昇格を行うことを宣言しています。ansible コマンドで利用した `-b` と同じ意味です。

この部分は、playbook 内の `play` パートと呼ばれる部分で全体に関する挙動を宣言します。playbook全体のヘッダーのようなものだと理解してください。playパートで指定できる項目の[詳細については公式ドキュメント](https://docs.ansible.com/ansible/latest/reference_appendices/playbooks_keywords.html#play) を確認してください。

### task パート
---
次に以下の状態となるように先のファイルへ追記します。インデントの階層に注意してください。

```yaml
---
- name: deploy httpd server
  hosts: all
  become: yes
  tasks:
  - name: install httpd
    dnf:
      name: httpd
      state: latest

  - name: start & enabled httpd
    service:
      name: httpd
      state: started
      enabled: yes
```

ここで追記した内容は `task` パートと呼ばれる部分になり、実際にこの playbook が行う処理を記述していきます。task パートではモジュールを呼び出す順番に列挙し、あわせてモジュールに対して必要なパラメーターを与えます。

- `tasks:` これ以降が task パートであることを宣言しています。
- `- name: ...` このタスクの説明を記載しています。省略可能
- `dnf:` `service:` 呼び出すモジュールを指定しています。
- 以下はモジュールに与えられているパラメーターです。
  - `name: httpd` `state: latest`
  - `name: httpd` `state: started` `enabled: yes`

ここで呼び出しているモジュールは以下になります。
- `dnf`: httpd パッケージをインストールするために利用します。
- `service`: インストールされた httpd を起動し、自動起動の設定を有効にしています。

作成した playbook に構文エラーがないかを以下のコマンドで確認できます。

`cd ~/working`{{execute}}

`ansible-playbook first_playbook.yml --syntax-check`{{execute}}

```text
playbook: first_playbook.yml
```

上記はエラー無しのケースです。もしインデントなどに誤りがある場合は以下のようになります。
```text
$ ansible-playbook first_playbook.yml --syntax-check

ERROR! Syntax Error while loading YAML.
  expected <block end>, but found '<block sequence start>'

The error appears to be in '/notebooks/working/first_playbook.yml': line 6, column 2, but may
be elsewhere in the file depending on the exact syntax problem.

The offending line appears to be:

  tasks:
 - name: install httpd
 ^ here
```

この場合には、playbook のインデントなどがサンプルと同じになっているかを再度確認してください。

## playbook の実行
---
作成した playbook を実行します。playbook の実行には `ansible-playbook` コマンドを利用します。成功すれば httpd サーバーが起動して apache の初期画面が参照できるはずです。

`ansible-playbook first_playbook.yml`{{execute}}

```text
PLAY [deploy httpd server] **************************************************

TASK [Gathering Facts] ******************************************************
ok: [node-2]
ok: [node-3]
ok: [node-1]

TASK [install httpd] ********************************************************
changed: [node-1]
changed: [node-2]
changed: [node-3]

TASK [start & enabled httpd] ************************************************
changed: [node-1]
changed: [node-2]
changed: [node-3]

PLAY RECAP ******************************************************************
node-1  : ok=3 changed=2 unreachable=0 failed=0 skipped=0 rescued=0 ignored=0
node-2  : ok=3 changed=2 unreachable=0 failed=0 skipped=0 rescued=0 ignored=0
node-3  : ok=3 changed=2 unreachable=0 failed=0 skipped=0 rescued=0 ignored=0
```

上記のような出力となれば成功です。node-1,2,3 に対してブラウザでアクセスしてサイトの動作を確認してください。

> Note: katacoda 上で演習をしている場合は、画面上部の `node-1,2,3` をクリックします。これらは各ノードのポート80へとリダイレクトされます。

> Note: Jupyter 上で演習をしている場合は、アクセスするIPアドレスを `~/inventory_file` で確認し、`http_access=http://35.73.128.87:8081` に示されたアドレスへブラウザでアクセスしてください。このアドレスは各ノードのポート80へリダイレクトされます。

以下のような画面が表示されれば成功です。

![apache_top_page.png](https://raw.githubusercontent.com/irixjp/katacoda-scenarios/master/materials/images/apache_top_page.png)


## タスクの追加
---
作成した playbook にサイトのトップページを配布するタスクを追加します。

`~/working/files/index.html` をエディタで開きます。

ファイルを以下のように編集します。
```html
<body>
<h1>Apache is running fine</h1>
</body>
```

さらに `first_playbook.yml` を以下のように編集します。
```yaml
---
- name: deploy httpd server
  hosts: all
  become: yes
  tasks:
  - name: install httpd
    dnf:
      name: httpd
      state: latest

  - name: start & enabled httpd
    service:
      name: httpd
      state: started
      enabled: yes

  - name: copy index.html
    copy:
      src: files/index.html
      dest: /var/www/html/
```

編集が完了したら、構文チェックを実施した後に playbook を実行してみましょう。


`ansible-playbook first_playbook.yml --syntax-check`{{execute}}

`ansible-playbook first_playbook.yml`{{execute}}

```text
PLAY [deploy httpd server] **************************************************

TASK [Gathering Facts] ******************************************************
ok: [node-2]
ok: [node-3]
ok: [node-1]

TASK [install httpd] ********************************************************
ok: [node-1]
ok: [node-3]
ok: [node-2]

TASK [start & enabled httpd] ************************************************
ok: [node-2]
ok: [node-1]
ok: [node-3]

TASK [copy index.html] ******************************************************
changed: [node-1]
changed: [node-3]
changed: [node-2]

PLAY RECAP ******************************************************************
node-1  : ok=4 changed=1 unreachable=0 failed=0 skipped=0 rescued=0 ignored=0
node-2  : ok=4 changed=1 unreachable=0 failed=0 skipped=0 rescued=0 ignored=0
node-3  : ok=4 changed=1 unreachable=0 failed=0 skipped=0 rescued=0 ignored=0
```

正常終了したら再びブラウザで3台のノードへアクセスしてください。正しく playbook が記述され、動作したのならば先程作成した `index.html` の内容が表示されるはずです。


## 冪等性(べきとうせい)
---
Ansible のモジュールを利用するメリットして、記述量を大幅に減らせるという解説を行いましたが、その他にもメリットがあります。それが `冪等性` です。

この演習では `ansible-playbook first_playbook.yml` を2回実行しています。httpd のインストールと起動を行った時、そしてサイトのトップページを追加したときです。つまり、httpd のインストールと起動のタスクは2回実行されています。しかし、2回目の playbook 実行にもエラー等は起きていません。これは Ansible の `冪等性` が機能しているからです。

1回目の実行結果と2回目の実行結果を注意深く確認すると、出力結果に違いがあることに気づくはずです。異なる箇所は、それぞれの処理において `changed` と出力されたか、`ok`と出力されたかです。

- `changed`: Ansibleが処理を実行した結果、対象ホストの状態が変わった（Ansibleが実際に設定を行った）
- `ok`: Ansibleは処理を行おうとしたが、既に想定した設定になっているので状態が変わらなかった（Ansibleは設定を行わなかった・行う必要がなかった）

この動作が Ansible が備える冪等性になります。Ansible は今から行おうとする処理を実行する必要があるのか、無いのかを実行前に判断してくれています。

ここで再度この playbook を実行してみましょう。3つのタスクの状態がどのようになるか考えてから実行してください。

`ansible-playbook first_playbook.yml`{{execute}}

```text
PLAY [deploy httpd server] **************************************************

TASK [Gathering Facts] ******************************************************
ok: [node-3]
ok: [node-1]
ok: [node-2]

TASK [install httpd] ********************************************************
ok: [node-2]
ok: [node-1]
ok: [node-3]

TASK [start & enabled httpd] ************************************************
ok: [node-1]
ok: [node-2]
ok: [node-3]

TASK [copy index.html] ******************************************************
ok: [node-3]
ok: [node-1]
ok: [node-2]

PLAY RECAP ******************************************************************
node-1  : ok=4 changed=0 unreachable=0 failed=0 skipped=0 rescued=0 ignored=0
node-2  : ok=4 changed=0 unreachable=0 failed=0 skipped=0 rescued=0 ignored=0
node-3  : ok=4 changed=0 unreachable=0 failed=0 skipped=0 rescued=0 ignored=0
```

全てのタスクは `ok` となったはずです。playbook 実行時の最後の `PLAY RECAP` 部分を並べてみると結果の差が分かりやすく確認できます。ここでは各ノードにおいて、何個のタスクが `changed` になったのかを比較してください。


1回目(タスク2個がchanged)
```text
PLAY RECAP ******************************************************************
node-1  : ok=3 changed=2 unreachable=0 failed=0 skipped=0 rescued=0 ignored=0
node-2  : ok=3 changed=2 unreachable=0 failed=0 skipped=0 rescued=0 ignored=0
node-3  : ok=3 changed=2 unreachable=0 failed=0 skipped=0 rescued=0 ignored=0
```

2回目(タスク1個がchanged)
```text
PLAY RECAP ******************************************************************
node-1  : ok=4 changed=1 unreachable=0 failed=0 skipped=0 rescued=0 ignored=0
node-2  : ok=4 changed=1 unreachable=0 failed=0 skipped=0 rescued=0 ignored=0
node-3  : ok=4 changed=1 unreachable=0 failed=0 skipped=0 rescued=0 ignored=0
```

3回目(changedは0)
```text
PLAY RECAP ******************************************************************
node-1  : ok=4 changed=0 unreachable=0 failed=0 skipped=0 rescued=0 ignored=0
node-2  : ok=4 changed=0 unreachable=0 failed=0 skipped=0 rescued=0 ignored=0
node-3  : ok=4 changed=0 unreachable=0 failed=0 skipped=0 rescued=0 ignored=0
```

では、この冪等性は何が嬉しいのかというと、

- Playbookには「処理の手順ではなく、状態を宣言的に記述できる」→ Playbook＝設定パラメータ＋手順書として扱えるようになる。
- 仮に複数台のホストに対して実行した処理が途中で失敗しても、最初から流し直せる（設定が成功した部分はスキップ(ok)されるため）
- 手順の集約ができる。初期設定、ノードの追加など様々なシーンを１つのPlaybookだけでカバーできるようになる。

> Note: 特に最後の「手順を集約する」という部分は重要です。従来ならば状況に応じて個別に作成する必要があったものを、冪等性を活用することで1本化して自動化することが可能です。冪等性をうまく活用することができれば、作業を自動化して効率化するだけではなく、自動化の開発そのものも大きく効率化できます。

Ansible の各モジュールはこの冪等性を考慮するように作られており、このモジュールを利用することで簡単に、そして安全に自動化を記述することができるようになっています。

これが自作の自動化シェルスクリプトの場合、特に失敗ややり直し時の再実行において、スクリプトを頭から流し直していいのか？ダメなのか？等の面倒な考慮点が生まれてしまうことは容易に想像できると思います(途中から実行する場合の手順や別のスクリプトを準備したり・・・)

> Note: ただし、Ansibleも全てのモジュールが完全な冪等性を保証しているわけではありません。モジュールの中には shell のように何が実行されるかわからないものや、操作対象（NW系機器やクラウド環境）によっては原理的に冪等性の確保が難しいものも存在しています。こういったモジュールを使う場合は利用者が注意を払う必要があります。

## 演習の解答
- [first\_playbook.yml](https://github.com/irixjp/katacoda-scenarios/blob/master/materials/solutions/first_playbook.yml)
- [files/index.html](https://github.com/irixjp/katacoda-scenarios/blob/master/materials/solutions/files/index.html)
