+++
categories = ["dev"]
tags = ["competitive-programming", "atcoder", "atcoder-cli"]
slug = ""
draft = true
date = "2018-12-07T23:21:00+09:00"
title = "atcoder-cli インストールガイド"

+++

[atcoder-cli](https://www.npmjs.com/package/atcoder-cli)のインストール方法を解説していきます。

## わかる人向け
```shell
$ pip3 install online-judge-tools
$ npm install -g atcoder-cli
```
以上です。

<!--more-->

## インストールガイド
atcoder-cliをインストールするためには、Node.js環境が必要です。
また、Python3で動作する[online-judge-tools](https://github.com/kmyk/online-judge-tools)を併用することを強く推奨しているため、こちらも同時にインストールします。

※ 以後、コマンド例の行頭の「`$`」はターミナルのプロンプト記号を表しているため、実際に入力する必要はありません。
`$`より後の部分を入力して実行してください。

### 事前準備：WindowsでWSLを使う場合
Windowsの場合、直接言語環境をインストールする方法と、<abbr title="Windows Subsystem for Linux">WSL</abbr>を使う方法があります。
どちらを使用するかはお好みですが、Linuxの使い方がわかるという方はWSLを選択するとよいでしょう。
WSLを使う場合は設定より有効化し、ストアからUbuntuなどのLinuxディストリビューションをインストールします。


* 参考: https://qiita.com/yoshikingt/items/ab86411e6031459db805

WSLのインストール後は、Linuxの場合のインストール方法を参照してください。

### 事前準備：Macの場合
[Homebrew](https://brew.sh/index_ja)の使用を想定します。
`brew`コマンドが存在しない場合、以下のコマンドをターミナルで実行することでインストールしてください。

```shell
$ /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

実行が完了したら、
```shell
$ brew -v
```
コマンドをターミナルで実行して、エラーなどが起きずにHomebrewのバージョン名が表示されていることを確認してください。

### 必要な環境のインストール
Python3とNode.jsの両方をインストールしてください。
OSごとにインストール方法が違うため、お使いのOSの欄を参照してください。

環境によってはもう少し良いやり方がある場合もありますが、この記事では少ない手順でインストールが完了することを目的として方法を選んでいます。


#### Windows
##### Python3
公式インストーラーより[Python](https://www.python.org/downloads/windows/)をインストールします。
最新バージョンの"Download Windows x86-64 web-based installer"からインストーラーをダウンロードし、実行してください。
インストール時に、"Add Python 3.x to PATH"のチェックを忘れずに入れるようにしましょう。

* 参考: https://azriton.github.io/2017/07/25/Python-3.6-64bit%E3%81%AE%E3%82%A4%E3%83%B3%E3%82%B9%E3%83%88%E3%83%BC%E3%83%AB/

##### Node.js
公式インストーラーより[Node.js](https://nodejs.org/ja/download/)をインストールします。

* 参考: https://qiita.com/taiponrock/items/9001ae194571feb63a5e

#### Mac/Linux
端末コンソールを開いて作業をしてください。

##### Python3
Linuxの場合は、`brew`のかわりにディストリビューションごとのパッケージマネージャを使用してください。
```shell
$ # Macの場合はこちらを実行してください
$ brew install python3
```

```shell
$ # UbuntuまたはDebianを使用している場合はこちらを実行してください
$ # 他のLinuxディストリビューションを使っている人はこんな記事を読むまでもなくインストールを終わらせていると思われるため触れません
$ sudo apt-get install python3
```

##### Node.js
パッケージマネージャによって古いバージョンのNodeしか手に入らないことがあるので、[nvm](https://github.com/creationix/nvm)を使ってインストールします。

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
Windowsの場合はコマンドプロンプトを、Mac/Linuxの場合は端末コンソールを開いてください。

```
pip3 -V
```
と入力してエンターキーを押し、バージョン番号が表示されることを確認してください。

同様にして、
```
npm -v
```
を実行し、バージョン番号が表示されることを確認してください。

「コマンドが見つかりません」や「command not found」、それに似たメッセージが表示される場合はPython3およびNode.jsのインストールに失敗している可能性があります。
その場合は再度手順を確認してインストールし直したり、「Python3 インストール (+OS名)」などで検索して調べてください。

問題がなかった場合は、atcoder-cliおよびonline-judge-toolsのインストールを行います。

```
npm install -g atcoder-cli
```

```
pip3 install online-judge-tools
```
とそれぞれ入力してエンターキーを押すことで、atcoder-cliとonline-judge-toolsがインストールされます。

```
acc -h
```
```
oj -h
```
とそれぞれ入力し、ヘルプが表示されることを確認してください。

ここまででインストール作業は完了です。

[atcoder-cli チュートリアル](2018/12/07/atcoder-cli-tutorial/)に進み、atcoder-cliの基本的な使い方を確認しましょう。
