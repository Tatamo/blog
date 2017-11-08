+++
categories = ["dev"]
date = "2016-01-05T20:50:32+09:00"
draft = false
slug = ""
tags = ["blog", "Hugo", "GitHub", "wercker", "DeployBot"]
title = "Hugoを導入してブログを作った デプロイ編"

+++

[Hugo](https://gohugo.io/)によって構築したサイトを、GitHub,Wercker,DeployBotを経由してWebサーバー上にデプロイした流れです。今回はHugo自体はほとんど触れていません。

<!--more-->

## Werckerを使って自動ビルドする
まずはビルドを手元ではなくオンライン上で自動的に行うようにします。
記事を書いて反映させる際は記事編集→ビルド→アップロードという3つの手順を踏みますが、これによってビルド作業を手元で行わなくてもよくなります。
また、Hugoの環境がなくても、gitさえ使うことができれば記事の更新を行うことができるようになります。

今回使用した[Wercker](http://wercker.com/)は、このような自動ビルド作業およびデプロイ作業を自動化してくれるCIサービスです。
Werckerを利用した自動デプロイは、[Hugoの公式ドキュメント](https://gohugo.io/tutorials/automated-deployments/)に記事があるため、これを参考に行います。
この記事はGitHub Pagesへのデプロイまで解説してありますが、今回の環境ではFTPによるサーバーへのアップロードが必要だったため、もうひと手間かける必要がありました。

### wercker.ymlの記述
公式ドキュメントによる解説にも書いてありますが、Werckerによる自動ビルド/デプロイを利用するには、対象のGitHubリポジトリ内にwercker.ymlというファイルを作成し、そこに設定やビルド時の処理を記述する必要があります。
既にHugoビルド用の設定を作ってくれている人がいるので、それを利用すれば簡単にビルドができます。
```yml
box: debian 
build:
  steps:
    - arjen/hugo-build:
        version: "0.14"
```
これだけでHugoのビルドが可能です。
ビルドが完了しただけではまだ目的のサーバーにHugoサイトがアップロードされていませんので、続いてデプロイの設定を行います。

## WerckerとDeployBotを使って自動デプロイする
前述のように、今回使用している環境ではWebサーバーにFTPでアップロードすることが目的のため、デプロイはwerckerからlftpを使用して行おうとしましたが、うまくいきませんでした。
```yml
box: debian 
build:
  steps:
    - arjen/hugo-build:
        version: "0.14"
deploy:
  steps:
    - install-packages:
        packages: lftp
    - script:
        name: mirror over lftp
        code: |
            lftp -u $DEPLOY_FTP_USER,$DEPLOY_FTP_PASS -e "set ftp:passive-mode off;set ftp:ssl-allow off;set net:timeout 5;set net:max-retries 3;set net:reconnect-interval-max 60;mirror -enR public /blog;quit" $DEPLOY_FTP_SERVER
```

lftpでの接続に失敗し、アップロードを行うことができませんでした。
PASVモードのon/off、sslを使用しないようにするなどのオプションを試みましたが、コネクションがうまく確立できず失敗しました。

後日また調べてみたいと思っていますが、ひとまずFTPでのアップロードを行うため、別のサービスを利用することにしました。
[DeployBot](http://deploybot.com/)は1プロジェクトまで無料で利用できるサービスで、FTPによるアップロードにも対応しているため、これを利用しました。
DeployBotによるデプロイは、[Hugoをセットアップしてデプロイするまで［後編］CIサービスでビルドからデプロイを自動化するまで | creative tweet.](http://creative-tweet.net/blog/2015/10/usage-hugo-2.html)で解説されているので、ここを参考にして行いました。

全体としては、

1. 記事を書いてGitHub上にpushする
2. Werckerが更新を検知して自動的にビルドを行い、別のGitHubリポジトリを更新する
3. そのリポジトリの更新を検知してDeployBotによって自動的にサーバーへアップロードする

というような流れでのデプロイを行いました。

Werckerから別のGitHubリポジトリへのpushを行うようにするため、公式ドキュメントに書かれている通りにwercker.ymlを書き換えます。

```yml
box: debian 
build:
  steps:
    - arjen/hugo-build:
        version: "0.14"
deploy:
  steps:
    - install-packages:
        packages: git ssh-client
    - leipert/git-push:
        gh-oauth: $GIT_TOKEN
        basedir: public
        repo: $GITHUB_REPO
        clean_removed_files: true
```

`$GIT_TOKEN`および`$GITHUB_REPO`は、werckerの設定画面でdeploy targetごとに変数を定義できるため、そこに記述します。$GIT_TOKENはprotectedにチェックを入れ、外部から取得できないようにしておきます。

あとはwerckerのデプロイ先に指定したGitHubリポジトリをDeployBotに登録するだけです。
DeployBotはリポジトリが更新されるとその内容をそのままサーバー上へアップロードしてくれ、設定も簡単でした。
これで目的のサーバーへの自動デプロイが完了しました。

## 補足など

### GitHubリポジトリの分割
Hugoのビルドをローカルでは行わないようにしたため、リポジトリ内に/publicを含める必要がありません。.gitignoreに次の行を記述します。

```nohighlight
public/
```

- リポジトリA:publicディレクトリを除くHugoディレクトリ
- リポジトリB:生成されたpublicディレクトリの内容

というようなGitHubリポジトリ構成とし、リポジトリBはWerckerとDeployBotをリンクさせるために使用するため手動では触れません。

### Werckerの設定

#### Dockerの使用
Werckerにプロジェクトを登録する際、wercker.yml設定の項目で「Dockerを使用する」かどうかのチェックボックスが存在します。
Werckerで使用するboxはDockerを使用しているものと使用していないものがあり、Hugo公式ドキュメントで使用しているbox:debianなどはDockerを使用しているため、Docker使用の設定をオンにしておかないと、[boxが見つからないというエラー](http://thleap.net/blog/recovering-wercker/)が発生します。
逆にwercker/defaultなどのDockerを使用しないboxを使用する場合、設定をオンにしておく必要はないでしょう。
(なお私はWerckerのBoxについてはよくわかっていません。Hugoをデプロイする場合は公式ドキュメント通りにdebianを使用すればいいのではないでしょうか。wercker/defaultでは環境のセットアップに15秒ほどかかりますが、debianでは1秒で済みます。)

#### デプロイターゲット登録時のAuto deploy設定
ちょっとしたこと(というかちゃんと読めばわかること)ですが、werckerででプロイターゲットを登録する時、"auto deploy successful builds to branch(es):"という設定項目でブランチ名を指定する箇所がありますが、これはどのブランチが更新されたときに自動的にビルドが走るかを指定するものであり、デプロイ先のブランチ名を指定するものではありません。
GitHubからGitHubへのデプロイを行うときは少し混乱するかもしれません。

なお、デプロイ先のGitHubブランチ名は前述したwercker.yml内に指定する箇所があります。

---

以上で、Hugoで生成したサイトを自動的にデプロイすることができました。
とはいえWerckerから直接デプロイすることに失敗したためにやや回りくどい方法をとっており、いずれ改善したいと思っています。

Werckerはビルドごとに環境を最初から設定するため、ライブラリのインストールなども毎回行われ、ビルド環境が整うまでに1分ほどかかります。
手元でビルドができる環境ならばローカルでビルドしてアップロードしたほうが明らかに速いので、すぐに更新したいときなどはデプロイまでにかなり時間がかかる印象を受けます。
Werckerを経由せず、手動でアップロードできる方法も用意しておいたほうがいいのかもしれません。

また、稀にですがWerckerのビルドが最初のsetup environmentの段階で動かなくなり、Abortも効かない状態でビルドに失敗したことがありました。
デプロイを自動で行っているとはいえ、結局デプロイがうまくいったかどうか確認しないといけないのは問題かもしれません。
