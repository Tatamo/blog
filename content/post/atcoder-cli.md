+++
title = "コマンドラインツールatcoder-cliを公開しました"
categories = ["dev"]
tags = ["competitive-programming", "atcoder", "atcoder-cli"]
slug = ""
draft = false
date = "2018-12-07T23:20:00+09:00"

+++

この記事は[Competitive Programming (2) Advent Calendar 2018](https://adventar.org/calendars/3095)の7日の記事です。

---

[AtCoder](https://beta.atcoder.jp/)のためのコマンドラインツール、[atcoder-cli](https://www.npmjs.com/package/atcoder-cli)をリリースしました。

[インストールガイド](2018/12/07/atcoder-cli-installation-guide/)と[チュートリアル](2018/12/07/atcoder-cli-tutorial/)の記事も用意しています。

<!--more-->

## モチベーション
[AtCoder](https://beta.atcoder.jp/)のコンテストで問題を解く際に、問題用のディレクトリを作る、プログラム用のファイルを用意する、問題を解き終わったらソースコードをコピーして提出するなど、プログラムを書く以外の部分で手間がかかることがあります。
そのため、問題を提出する、プログラムがサンプルケースで失敗しないかチェックする、問題を解くのに必要なファイルやディレクトリを構築する、といった処理をコマンドラインから実行できればコンテスト中の処理の自動化が可能になり、快適に問題を解くことができるようになります。

コマンドラインからAtCoderに問題を提出したりするツールとしては、[online-judge-tools](https://github.com/kmyk/online-judge-tools)などが存在します。

とはいえ、既存のツールでは複数のコンテストサイトを動作対象としていることなどから、「プログラムを提出するたびに提出先の問題IDやURLを指定しなければならない」といった問題点がありました。
提出ファイルのコピペなどの手間を省くためにコマンドラインツールを使っているはずなのに、その提出先を指定するためにURLをブラウザからコピーしてくる必要があるのでは本末転倒感があります。

また、問題を一問解く度にプログラムのテンプレートを用意したり、既存ツールを用いてサンプルケースをダウンロードしてくるのも手間がかかります。

そこで、数ある競技プログラミングのサイトの中からAtCoderに特化することで、コンテスト中にコンソールに打ち込むコマンドの数を極限まで減らせるようにするツールとして、[atcoder-cli](https://www.npmjs.com/package/atcoder-cli)を開発しました。

atcoder-cliはディレクトリ構成から自動的に提出するコンテスト・問題を検出し、短いコマンドの入力だけで問題を提出できるようにするのみならず、テンプレートを自動的に展開する機能などを備えており、AtCoder用のプロジェクト管理ツールとしても機能するソフトウェアです。
また、online-judge-toolsとの連携によって、サンプルケースの自動ダウンロードなどの機能を余計なURL指定なしで快適に使用できるようになります。

## インストール方法
```sh
$ npm install -g atcoder-cli
$ acc -v # 正しくインストールされたかどうか確認する
```

atcoder-cliはnpmパッケージとして公開しているため、node.jsが必要です。
また、[online-judge-tools](https://github.com/kmyk/online-judge-tools)はインストールされていなくても問題なく動作しますが、連携機能のため同時にインストールしておくことを強く推奨します。

[atcoder-cli インストールガイド](2018/12/07/atcoder-cli-installation-guide/)を公開していますので、詳しいインストール手順はそちらをお読みください。

## 特徴
詳細な機能については[README](https://github.com/Tatamo/atcoder-cli)や`acc [COMMAND] -h`コマンドを参照してください。
日本語の解説記事として、[atcoder-cli チュートリアル](2018/12/07/atcoder-cli-tutorial/)があります。

### コンテスト情報取得
```nohighlight
$ acc contest abc100
AtCoder Beginner Contest 100  https://beta.atcoder.jp/contests/abc100
$ acc tasks abc100
A  Happy Birthday!           https://beta.atcoder.jp/contests/abc100/tasks/abc100_a
B  Ringo's Favorite Numbers  https://beta.atcoder.jp/contests/abc100/tasks/abc100_b
C  *3 or /2                  https://beta.atcoder.jp/contests/abc100/tasks/abc100_c
D  Patisserie ABC            https://beta.atcoder.jp/contests/abc100/tasks/abc100_d
```
CLIツールとして、問題のURLなどをコマンドラインから取得できます。他のシェルコマンドと組み合わせることで、AtCoderに関連する処理を自動化する際に役立てることができます。

### コンテスト用ディレクトリ作成
```sh
$ acc new abc100 # abc100用のディレクトリを作成
$ cd abc100
$ cat contest.acc.json
{
  "contest": {
    "id": "abc100",
    "title": "AtCoder Beginner Contest 100",
    "url": "https://beta.atcoder.jp/contests/abc100"
  },
  "tasks": [
    {
      "id": "abc100_a",
      "label": "A",
      "title": "Happy Birthday!",
      "url": "https://beta.atcoder.jp/contests/abc100/tasks/abc100_a",
      "directory": {
        "path": "a",
        "testdir": "tests"
      }
    },
    ...
}
```

atcoder-cliでは、コンテストごとにディレクトリを作成してプログラムを管理します。
デフォルトではコンテストディレクトリ内でさらに問題ごとにディレクトリを分け、その中で問題を解いていくことを想定していますが、コンテストディレクトリ直下でディレクトリを分けずに複数のプログラムファイルを配置することもサポートしています。

この際に、以下のような処理を自動的に行うことができます。

- サンプルケースの自動ダウンロード (online-judge-toolsが必要)
- テンプレートを予め用意しておくことで、プログラム等のファイルを問題ディレクトリにコピー
- 任意のシェルコマンドを実行

これらを組み合わせることで、コンテスト用ディレクトリを作成した時点であらゆる前準備を終えることができます。

### 提出機能
atcoder-cli本体は今のところAtCoderにファイルを提出する機能を備えていませんが、内側でonline-judge-toolsを呼び出すことにより、もともとのonline-judge-toolsの機能よりもさらに簡単に問題を提出することができるようになっています。

```sh
$ oj s https://beta.atcoder.jp/contests/abc100/tasks/abc100_a main.cpp
```

online-judge-toolsでは問題の提出のために上記のコマンドが必要でしたが、

```sh
$ acc s main.cpp # s はsubmitの省略名
```

atcoder-cliはファイルが配置されたディレクトリから自動的に提出するべき問題を判別し、URLなどを明示する必要がなくなっています。

さらに、問題テンプレート機能を使用すれば提出するプログラムのファイル名を予め指定しておくことができるため、以下のように一切の引数を与えることなく提出が可能になります。
```sh
$ acc s # これだけで提出ができる！
```

### 高速な応答性
レスポンスの速さはCLIツールにとって重要な要素であるため、依存パッケージのロードを必要なタイミングまで遅延させることで、そのコマンドの実行時に使用されないパッケージの読み込みを防止して応答性を高めています。
```sh
$ time acc -h
...
real    0m0.093s
user    0m0.080s
sys     0m0.008s
```

## 類似ツール
### [kmyk/online-judge-tools](https://github.com/kmyk/online-judge-tools)
- AtCoderに限定せず様々なサイトに対応
    - それ故にややコマンドのタイプ数が増えやすい傾向にある
- テストツールとしての機能も有する

atcoder-cliは現状online-judge-toolsにどっぷり依存しているので頭が上がりません。
便利なツールを作って頂いてありがとうございます。

とはいえatcoder-cliはnpm, ojはpipでインストールしないといけないため、atcoder-cli単体で同等の機能が提供できたほうがインストールの手間は減ると思うので好ましいのかもしれません(サンプルケースの取得はアドホックにやらざるを得ないし、提出ファイルの言語指定も割とつらさがあるので既にあるものをあまり再実装したくない)。

### [nodchip/OnlineJudgeHelper](https://github.com/nodchip/OnlineJudgeHelper)
- 様々なサイトに対応、多機能
- outdated

URLをすべて入力しなくても、コンテストID+問題IDでの提出ができるため少しは楽です。
年単位でメンテナンスされていなかったり、アカウントのパスワードをローカルに置いておくことを求められたりするので、今使うには微妙かもしれません。

### [XMLPro/atam](https://github.com/XMLPro/atam)
- AtCoder用の問題提出ツール
- node.jsで動作

AtCoder専用のツールで、現状では問題提出のみの機能を持っています。
npmからインストールすることができ、連携が比較的容易と思われるので、今後連携機能の実装を行うかもしれません。

## Contribution
バグ報告、要望などは[https://github.com/Tatamo/atcoder-cli/issues](https://github.com/Tatamo/atcoder-cli/issues)で受け付けています。

## 余談
このツールが良いと思っていただけましたら、ぜひ**[★Starをください](https://github.com/Tatamo/atcoder-cli)**。

というのも、AtCoder勢で利用していない人はほとんどいないであろう[AtCoder Problems](https://kenkoooo.com/atcoder/)が、その圧倒的な知名度と比較して[GitHubリポジトリ](https://github.com/kenkoooo/AtCoderProblems)についている★の数があまりに少ないのではないか、という話があります。

★とはTwitterの~~ふぁぼ~~いいねのようなものです。
競プロ勢はGitHubの使い方もよくわからないのだ、という不名誉な評判がつきかねませんから、ぜひ良いと思ったリポジトリには★を投げてみましょう。

ここに[良い練習台 (Tatamo/atcoder-cli)](https://github.com/Tatamo/atcoder-cli)がありますね。

GitHubのアカウントを持っていない方は、有効なメールアドレスとユーザー名、パスワードだけあれば[https://github.com/join](https://github.com/join)から簡単にアカウントの作成を行うことができます。
([参考](https://www.google.co.jp/search?q=github+登録))

ログインができたら[適当なソフトウェア (Tatamo/atcoder-cli)](https://github.com/Tatamo/atcoder-cli)のページに行き、ページの上の方にある「★Star」をクリック/タップするだけで★をつけることができます。

(露骨な宣伝で申し訳ありません。
私のツールに★を投げる必要はありませんが、AtCoder Problemsは実際もっと★がついていてもよいのではと思っています。
皆さんどんどんGitHubで★を投げあいましょう)
