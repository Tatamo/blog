+++
tags = ["competitive-programming", "atcoder", "atcoder-cli"]
slug = ""
draft = false
date = "2018-12-07T23:22:00+09:00"
title = "atcoder-cli チュートリアル"
categories = ["dev"]

+++

[atcoder-cli](https://www.npmjs.com/package/atcoder-cli)の使用方法を解説していきます。

<!--more-->

atcoder-cliは、[AtCoder](https://beta.atcoder.jp/)用のコマンドラインツールです。
このツールの特徴については、[紹介記事](/2018/12/07/atcoder-cli/)をご覧ください。

## インストール

```shell
$ pip3 install online-judge-tools
$ npm install -g atcoder-cli
```

詳細なインストール手順は、[atcoder-cli インストールガイド](2018/12/07/atcoder-cli-installation-guide/) を参照してください。

## atcoder-cliのインストール確認
```shell
$ acc -h
```
※行頭の「`$`」は入力しないでください

`acc`コマンドが使用できれば、インストールは正しく行えています。

[online-judge-tools](https://github.com/kmyk/online-judge-tools)がインストールされている場合、atcoder-cliは自動的にそれを検知します。
```shell
$ acc check-oj
```
を入力して、availableと表示されれば連携機能が有効になっています。

## AtCoderへのログイン
atcoder-cliを使用するためには、初回使用時にAtCoderへのログインが必要になります。

```shell
$ acc login
```
コマンドでAtCoderにログインしてください。
ユーザー名とパスワードの入力を求められますが、パスワードは安全のため入力しても画面には表示されないので注意してください。

ユーザー名・パスワードはatcoder-cliによって保存されることはありません。
そのかわりにログインセッションの情報が設定ファイルとして保存されますが、共用のPCなどで他の人が使用する可能性がある場合は
```shell
$ acc logout
```
で現在のログイン状態を破棄することができます。

ログインしている状態かどうか知るためには、
```shell
$ acc session
```
を使用してください。

online-judge-toolsを使用している場合は、`oj`コマンドを使用してそちらでもログインしておく必要があります。
```shell
$ oj login https://beta.atcoder.jp/
```
でログインを行ってください。

## atcoder-cliでコンテスト用ディレクトリを作成する
以後、online-judge-toolsを使用している前提とします。

[ABC101](https://beta.atcoder.jp/contests/abc101)のコンテストの問題を解くことを想定してみましょう。

ABC101のURLは https://beta.atcoder.jp/contests/abc101 ですが、この末尾の`abc101`がこのコンテストのIDとなります。
`acc n`コマンドにこのIDを指定することで、ABC101用のディレクトリを作らせてみましょう。

```shell
$ acc new abc101
```

コマンドを実行すると、問題の一覧が表示されます。
上下キーでカーソルを移動し、スペースキーで選択することで、サンプルケースをダウンロードしてくる問題を選ぶことができます。
この問題は後から追加することもできます。
一番上のA問題は予め選択されているはずなので、今回はそのままエンターキーを押しましょう。

コマンドの実行が完了すると、`abc101`という名前のディレクトリが作られていると思います。
このディレクトリの中に移動してみましょう。
```shell
$ cd abc101
$ ls # ディレクトリの中身を表示
a  contest.acc.json
```

`a/`という名前のディレクトリと、`contest.acc.json`というファイルが一つ存在していると思います。

`contest.acc.json`は、atcoder-cliがコンテストや問題の情報を取得したり、問題ディレクトリの情報を管理するためのファイルです。
通常の使用中は、このファイルに触る機会はありません。

ディレクトリ`a/`に移動してみましょう。
```shell
$ cd a/
```
このディレクトリの中には、`tests/`という名前のディレクトリが既に存在しています。
`online-judge-tools`との連携機能により、自動的に問題のサンプルケースがダウンロードされたものです。

この`a/`ディレクトリの中で、問題を解いていきます。
たとえば、`main.cpp`という名前のファイルを作り、[A問題](https://beta.atcoder.jp/contests/abc101/tasks/abc101_a)を解くプログラムを書いてみましょう。

## 問題を提出する
書き終わったら、正しくプログラムが書けているか確認します。

online-judge-toolsは入出力ケースの自動テスト機能を備えているため、自動ダウンロードされたサンプルケースを用いてテストができます。
現在atcoder-cliはテストに関する機能の連携が未実装のため、詳しくは[online-judge-toolsの解説記事](https://kimiyuki.net/blog/2017/01/19/pr-online-judge-tools/)を参照してください。

問題文で与えられた入力例について正しい答えが得られていることを確認したら、プログラムの提出をしましょう。

```shell
$ acc submit main.cpp
```
atcoder-cliでは、提出するファイルを指定するだけで提出先を自動的に判別します。
たとえば、`abc101/a/`ディレクトリの中にあるプログラムはABC101のA問題のプログラムとして提出が行われます。

提出が完了してA問題を解き終わったら、一つ上のディレクトリに戻りましょう。

```shell
$ cd ..
```

## 問題用ディレクトリを追加する
次はB問題を解いていくので、B問題用の新しいディレクトリを作る必要があります。
コンテストディレクトリの中で
```shell
$ acc add
```
コマンドを入力すると、再び問題を選択する画面になります。
先程既にディレクトリを作ったA問題は選択できなくなっています。

このようにして問題用のディレクトリを作って問題を解いていくのが、atcoder-cliでコンテストに取り組む流れになります。

## デフォルト動作の設定
`acc new`コマンドや`acc add`コマンドで問題ディレクトリを作成する際、毎回問題を選択してエンターキーを押さないといけないのが煩雑に感じるかもしれません。

```shell
$ acc config
```
コマンドによって、現在のグローバル設定を確認することができます。
この中に、`default-task-choice: inquire` と書かれた行があるのではないかと思います。

```shell
$ acc config default-task-choice next
```
などとすることで、問題の選択方法を変更することができます。

`next`を指定することで、問題を選ぶ画面を出さずに次の一問だけを自動で選ぶようにしたり、`all`を指定することで全ての問題ディレクトリを最初に作るようにできます。

`default-task-choice`に設定可能な選択肢については、
```shell
$ acc add -h
```
で確認することができます。

また、グローバル設定で設定可能な項目の一覧は、
```shell
$ acc config -h
```
で確認できます。

## テンプレート設定

予めマクロの定義や入力を受け付ける部分などの雛形が書かれたプログラムファイルを用意しておくことで、問題ディレクトリを作成する際にそのファイルをコピーして配置することができます。

たとえば、C++用のテンプレートを用意するには、まずatcoder-cliの設定ファイルが配置されたディレクトリに移動し、
```shell
$ cd `acc config-dir`
```

ここに`cpp` という名前のディレクトリを作ります。
テンプレートの名前は、このディレクトリ名によって決定されます。

その中に、コピーしたい雛形である`main.cpp`ファイルと、`template.json`ファイルを作成します。

`template.json`ファイルには以下のように記述します。
```json
{
  "task":{
    "program": ["main.cpp"],
    "submit": "main.cpp"
  }
}
```

`template.json`を配置したら、`acc templates`コマンドを使ってテンプレートが正常に認識されているか確認します。

```shell
$ acc templates
```

正しく読み込まれていれば、`cpp`という名前の項目が表示されます。

実際にこのテンプレートを利用するには、`acc new`コマンドと`acc add`コマンドを実行する際に、`--template`オプションを使用して使用するテンプレートを指定します。

```shell
$ acc add --template cpp
```

このようにして問題ディレクトリを作成すると、その中に`main.cpp`がコピーされます。
また`template.json`の`"submit"`プロパティに`main.cpp`を設定しているため、問題ディレクトリの中にいる場合は
```shell
$ acc submit
```
のように、明示的にファイルを指定しなくても`main.cpp`が提出対象のファイルであると判断されます。

頻繁に使用するテンプレートは、常に`--template`オプションをつけるかわりに、グローバル設定からデフォルトのテンプレートとして設定することが可能です。

```shell
$ acc config default-template cpp
```

これにより、`acc new`コマンドと`acc add`コマンドの実行時に自動的に`cpp`テンプレートが使用されるようになります。

`template.json`についてのより詳細な情報は、
```shell
$ acc template -h
```
と入力すると知ることができます。

## その他
使用可能なコマンドの一覧は、
```shell
$ acc -h
```
で表示することができます。

また、コマンドごとの詳細な解説、利用可能なオプションを知るためには、
```shell
$ acc <コマンド名> -h
```
を使用してください。

---

以上がatcoder-cliの基本的な使い方になります。
このツールによってAtCoderで問題を解く作業の効率化ができることを願っています。
