---
layout: page
title: "git"
date: 2014-05-16 00:45
comments: true
sharing: true
footer: true
---
## Gitの初期化
Gitをつかうには、まずGitリポジトリを作成します。Gitリポジトリの作成は、バージョン管理したいファイルが入っているディレクトリに移動し(まだバージョン管理したいファイルを作成していない場合は空のディレクトリを作成)、次のコマンドを実行します。

$ git init
Initialized empty Git repository in /hoge/gittest/.git/

.gitが作成され、これがGitリポジトリとなります。

# すでに存在しているGitリポジトリからcloneする
すでに存在しているGitリポジトリを手元にコピーしたい場合はgit cloneを使います。git cloneには二つのパラメータ、リポジトリの場所と、そのリポジトリをローカルのどこにcloneするかを指定します。
たとえば、このページはGithub上のGitリポジトリで管理しています。そして、そのリポジトリのパスは git@github.com:kenjikitamura/kenjikitamura.github.com.git となります。
よって、このリポジトリをcloneするには次のコマンドを実行します。

$ git clone git@github.com:kenjikitamura/kenjikitamura.github.com.git pages

すると、次のように表示され、完了します。

Cloning into 'pages'...
remote: Reusing existing pack: 4853, done.
remote: Total 4853 (delta 0), reused 0 (delta 0)
Receiving objects: 100% (4853/4853), 1.38 MiB | 259.00 KiB/s, done.
Resolving deltas: 100% (2303/2303), done.
Checking connectivity... done.

これで、リモートリポジトリの内容(コンテンツとその歴史)を手元にコピーすることができます。
インターネットに接続するのにプロキシ経由での接続が必要な場合は、次のようにコマンドを実行し設定します。

$ git config --global http.proxy http://proxy.example.com:8080
$ git config --global https.proxy http://proxy.example.com:8080

上記の設定はhttpとhttps経由でのアクセスで、もしgitプロトコルのままcloneしたい場合は次のコマンドを実行します。

$ git config --global url."https://".insteadOf git://

### サーバ用のGitリポジトリの作成
通常のGitリポジトリの作成(git initまたはgit clone)では、作業ディレクトリが作成されます。しかし、サーバ上にGitリポジトリを作成し、それを複数ユーザで共有する場合は作業ディレクトリが必要ありません。その場合はベアリポジトリを作成します。

ベアリポジトリの新規作成は次のコマンドを実行します。--bareはベアリポジトリの作成を意味します。--sharedは同一グループに対する書き込み権限を設定します。
$ git init --bare --shared

すでに存在しているGitリポジトリのベアリポジトリを作成する場合は、git cloneに--bareオプションを追加します。
先ほどのGithub上のリポジトリのベアリポジトリをcloneする場合は次のコマンドを実行します。

$ git clone --bare git@github.com:kenjikitamura/kenjikitamura.github.com.git pages

## コミット
### ステージ
Gitでのコミットは2段階に分かれています。まず、編集したファイルのうち、コミットしたいファイルをgit addコマンドでステージングエリアに追加します。そして、コミットしたいファイルをすべてステージングエリアに追加できれば、git commitコマンドでコミットを実行します。