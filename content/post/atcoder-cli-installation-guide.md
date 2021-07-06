+++
categories = ["dev"]
tags = ["competitive-programming", "atcoder", "atcoder-cli"]
slug = ""
draft = false
date = "2018-12-07T23:21:00+09:00"
title = "atcoder-cli インストールガイド"

+++

[atcoder-cli](https://www.npmjs.com/package/atcoder-cli) のインストール方法を解説していきます。

## わかる人向け
```shell
$ pip3 install online-judge-tools
$ npm install -g atcoder-cli
```
以上です。

<!--more-->

## インストールガイド
atcoder-cli をインストールするためには、Node.js 環境が必要です。
また、Python3 で動作する [online-judge-tools](https://github.com/kmyk/online-judge-tools) を併用することを強く推奨しているため、こちらも同時にインストールします。

※ 以後、コマンド例の行頭の「`$`」はターミナルのプロンプト記号を表しているため、実際に入力する必要はありません。
`$` より後の部分を入力して実行してください。

### 事前準備：WindowsでWSLを使う場合
Windows の場合、直接言語環境をインストールする方法と、<abbr title="Windows Subsystem for Linux">WSL</abbr> を使う方法があります。
どちらを使用するかはお好みですが、 Linux の使い方がわかるという方は WSL を選択するとよいでしょう。
WSL を使う場合は設定より有効化し、ストアから Ubuntu などの Linux ディストリビューションをインストールします。


* 参考: https://qiita.com/yoshikingt/items/ab86411e6031459db805

WSL のインストール後は、Linux の場合のインストール方法を参照してください。

### 事前準備：Macの場合
[Homebrew](https://brew.sh/index_ja) の使用を想定します。
`brew` コマンドが存在しない場合、以下のコマンドをターミナルで実行することでインストールしてください。

```shell
$ /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

実行が完了したら、
```shell
$ brew -v
```
コマンドをターミナルで実行して、エラーなどが起きずに Homebrew のバージョン名が表示されていることを確認してください。

### 必要な環境のインストール
Python3 と Node.js の両方をインストールしてください。
OS ごとにインストール方法が違うため、お使いの OS の欄を参照してください。

環境によってはもう少し良いやり方がある場合もありますが、この記事では少ない手順でインストールが完了することを目的として方法を選んでいます。


#### Windows
##### Python3
公式インストーラーより [Python](https://www.python.org/downloads/) をインストールします。
"Download Python 3.x.x (最新のバージョン番号)" と書かれたボタンをクリックしてインストーラーをダウンロードし、実行してください。
インストーラーが立ち上がると "Add Python 3.x to PATH" という項目があるので、忘れずにチェックを入れるようにしましょう。

* 参考: https://azriton.github.io/2017/07/25/Python-3.6-64bit%E3%81%AE%E3%82%A4%E3%83%B3%E3%82%B9%E3%83%88%E3%83%BC%E3%83%AB/

##### Node.js
公式インストーラーより [Node.js](https://nodejs.org/ja/download/) をインストールします。

* 参考: https://qiita.com/taiponrock/items/9001ae194571feb63a5e

#### Mac/Linux
端末コンソールを開いて作業をしてください。

##### Python3
Linux の場合は、`brew` のかわりにディストリビューションごとのパッケージマネージャを使用してください。
```shell
$ # Mac の場合はこちらを実行してください
$ brew install python3
```

```shell
$ # Ubuntu または Debian を使用している場合はこちらを実行してください
$ # 他の Linux ディストリビューションを使っている人はこんな記事を読むまでもなくインストールを終わらせていると思われるため触れません
$ sudo apt-get install python3
```

##### Node.js
パッケージマネージャによって古いバージョンの Node しか手に入らないことがあるので、[nvm](https://github.com/creationix/nvm) を使ってインストールします。

```shell
$ curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.11/install.sh | bash
```
このコマンドの実行が完了したら、一度ターミナルを閉じてください。
新しいターミナルを開き、続きのコマンドを実行します。
```shell
$ nvm install node
$ npm install -g npm
```

### atcoder-cli, online-judge-toolsのインストール
言語環境のインストールが正しく行われていることを確認します。
Windows の場合はコマンドプロンプトを、Mac/Linux の場合は端末コンソールを開いてください。

```
pip3 -V
```
と入力してエンターキーを押し、バージョン番号が表示されることを確認してください。

同様にして、
```
npm -v
```
を実行し、バージョン番号が表示されることを確認してください。

「コマンドが見つかりません」や「command not found」、それに似たメッセージが表示される場合は Python3 および Node.js のインストールに失敗している可能性があります。
その場合は再度手順を確認してインストールし直したり、「Python3 インストール (+OS名)」などで検索して調べてください。

問題がなかった場合は、atcoder-cli および online-judge-tools のインストールを行います。

```
npm install -g atcoder-cli
```

```
pip3 install online-judge-tools
```
とそれぞれ入力してエンターキーを押すことで、 atcoder-cli と online-judge-tools がインストールされます。

```
acc -h
```
```
oj -h
```
とそれぞれ入力し、ヘルプが表示されることを確認してください。

ここまででインストール作業は完了です。

[atcoder-cli チュートリアル](2018/12/07/atcoder-cli-tutorial/)に進み、atcoder-cli の基本的な使い方を確認しましょう。

## トラブルシューティング
`oj` コマンドのインストールに関する問題が解決しない場合、 [online-judge-tools リポジトリ](https://github.com/kmyk/online-judge-tools) にも案内があるため、そちらも併せて確認してください。 たとえば、 [docs/INSTALL.ja.md](https://github.com/online-judge-tools/oj/blob/master/docs/INSTALL.ja.md)  にはより詳細で正確なインストール手順が書かれています。

### (Windows) PowerShell で `acc` コマンドが実行できない
コマンドプロンプト (cmd.exe とタイトルに書かれている黒い画面) ではなく PowerShell (青い画面) を使っている場合、「このシステムではスクリプトの実行が無効になっているため、……」 というようなメッセージが表示されて `acc` コマンドが実行できないことがあります。

この場合、まず管理者モードで PowerShell を開きます。たとえば、スタートメニューボタンを右クリックして表示されるメニューに `Windows PowerShell (管理者)` というような項目があるので、これを選択すると `管理者: Windows PowerShell` というウインドウが立ち上がります。
この中で、以下のコマンドを実行してください。

```
Set-ExecutionPolicy RemoteSigned
```

実行ポリシーを変更するかどうか聞かれるので、 `Y` と入力してエンターキーを押します。

そうしたら管理者モードの PowerShell ウインドウは閉じてしまって、 `acc` コマンドが実行できるようになっているか確認してください。

* 参考: https://qiita.com/Targityen/items/3d2e0b5b0b7b04963750

### (Mac, Linux) `npm install -g atcoder-cli` すると ERR と書かれた行がたくさん表示される
エラーメッセージをよく見ると、 permission がどうとか書かれているかもしれません。
公式のインストーラーなどを使用して、すでにシステムに Node.js がインストールされていた場合に発生することがあるようです。
この記事に書かれた手順に従って Node.js をインストールしなおすことで解消されます(すでにインストールされていた Node.js を個別にアンインストールしたりする必要はないので、そのまま手順通りに進めてください)。

### (Mac, Linux) `oj` コマンドが見つからないといわれる・ `pip3 install online-judge-tools` すると Permission denied といわれる
`sudo` コマンドが使える場合、
```
$ sudo pip3 install online-judge-tools
```
で解決することがあります。
`sudo` から始まるコマンドを実行するとパスワードの入力を求められるので、 PC にログインしているユーザーのパスワードを入力します。
セキュリティのためにパスワードを入力しても画面に文字が何も表示されないはずですが、内容は読み取られているので正しくパスワードを入力してエンターキーを押してください。

または、 online-judge-tools のインストールドキュメント ([docs/INSTALL.ja.md](https://github.com/online-judge-tools/oj/blob/master/docs/INSTALL.ja.md)) を読み、 `~/.local/bin` を `PATH` に追加してから
```
$ pip3 install --user online-judge-tools
```
を実行してください。
