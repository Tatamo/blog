+++
date = "2018-09-16T18:00:00+09:00"
draft = false
slug = ""
tags = ["isucon"]
categories = ["diary"]
title = "ISUCON8の予選に出場して負けました"

+++

[isucon8](http://isucon.net/archives/52193980.html)の予選に参加しました。

結果としてスコアなしで敗退しました。

<!--more-->

序盤に行った環境構築の記事は[こちら](/2018/09/16/isucon8-qual-2/)

今回の予選で使用したリポジトリは https://github.com/Tatamo/isucon8-q です。

## メンバー編成
[わたし](https://twitter.com/__tatamo__), [@sensu\_watson](https://twitter.com/sensu_watson)氏, [@uhyo\_](https://twitter.com/uhyo_)氏の チーム 🈚️ で参加しました。

全員多少のサーバー運用経験はありますが、ちゃんとした構築を行ったりした経験はほとんどなく、どちらかというと普段はフロントエンド寄りがメインのメンバーが多い構成です。

## 開始前
### 前日まで
メンバー全員があまり暇ではなかったたため、事前の練習は時間が取れませんでした。
1週間前からConoHaでISUCON7のインスタンスを建てて軽く触った程度の対策しかできていません。

私と@uhyo\_氏がJavaScriptを読み書きできるので、nodejsを使用することにしました。

### 当日朝
そもそもスロークエリとか`nginx.conf`とか`/etc/systemd/system`とか言われてもあまりよくわからないので、役割分担のしようがなさそうという雰囲気が漂っていました。
そのため開始直後の作業のみ割り振りを決めておき、それ以降はその場で適当になんとかすることにしました。

## 経過
### 序盤
それぞれデプロイ環境の構築、解析ツールのインストール、HTTPサーバーとDBの設定確認・変更と最初のベンチマークの起動を担当することになったため、作業にとりかかりました。
解析ツールとしては、ISUCON7 などでgoogle検索したら1ページ目に出てきた[alp](https://github.com/tkuchiki/alp), [netdata](https://github.com/firehol/netdata), [pt-query-digest](https://www.percona.com/doc/percona-toolkit/LATEST/pt-query-digest.html)を導入すると決めていました。

始まる前はてっきりUbuntu+nginx+MySQLとかの構成かと思っていたんですが、蓋を開けてみるとRed Hat+H2O+MariaDBで、`nginx.conf`がない…などと困惑することになりました。
当然H2Oを触ったことのある人は誰もいません。

この段階で既に何かとトラブルが起きたりしていましたが、1時間経過ぐらいでアプリケーションの中身を読み進めるフェーズに入りました。
TypeScriptで書かれていたり`.prettierrc`が置かれていたりしたのが意外でした(我々はTypeScriptで開発することが多いためこれはありがたいことです)が、@uhyo\_氏曰く「`any`が多いのでTypeScriptのありがたみが少ない」とのことでした。確かにそんな感じはしました。
私の環境ではなぜかブラウザでアプリケーションにアクセスした際にフロントのJavaScript側でエラーが出たりしたため、今回のアプリがどのようなものか把握するにも少し手間取ったりしました。

alpのログと相談しつつ、`getEvent()`がid一つごとにクエリを1回発行しているから重いね、などと言いつつも生SQLに怖気づいて手を入れられずにいる横で@uhyo\_氏が着実に修正を加えていき、13時ごろにスコアが1,100から1,500に上昇して喜んだりしていました。

### 中盤
node.jsのログがとれていないのが厳しいという話になり、ログまわりを調べることにしました。
systemdに慣れている人がおらず、私が担当しましたがログをjournalに流し込んで取得するのに1時間はかかってしまいました。

また、`/var/log/h2o/access.log`がアクセスを受けても更新されないことを見つけたり、かと思えばしばらくして見に行くとログが溜まっていることに気付いたりして首を傾げたりする作業をしました。

また残り時間が減ってきたので、これまで1台で稼働させていたサーバーを複数台構成に切り替えようという話になり、以降アプリケーションまわりはすべて@uhyo\_氏が担当して私と@sensu\_watson氏で構築にとりかかることにしました。

15時に@uhyo\_氏が対処していたループ中でのsheetsテーブル取得解消の成果が出て、ここスコアが約1,500点から8,435点に上がったので大喜びしました。

<a href="/images/post/2018/09/16/isucon8_screenshot.png" target="_blank"><img src="/images/post/2018/09/16/isucon8_screenshot.png" alt="15時時点のスクリーンショット 8435点"></a>  
我々の全盛期の様子 なんと学生1位


### 終盤
どうでもいいことですが、3台のプライベートIPの末尾が.3, .2, .1となっていることに気付かずにグローバルIPだけ見てサーバーリストの上から順にA,B,Cサーバーと名前をつけたところ、競技中にサーバーリストの並びが.1, .2, .3 に切り替わったので上からC,B,Aとなり一時混乱しました。

netdataのグラフを見たところ、通信量やメモリ使用量よりもCPU使用率が飛び抜けて高負荷になっているようだったため、アプリケーション部分に2台割り当てて残りの1台をDBとすることに決めました。

私はまずDBを1つのサーバーに切り出す作業を始め、これはデータベース側でユーザーに外部からのアクセスを許可しないといけない点で少しつまづきつつも、基本的には環境変数の値を変えるだけだったのでおおよそうまくいきました(少なくともこの時はそう思っていました)。

続いてアプリケーション部分を2台に分けないといけないのですが、nginxですら誰も複数台構成を試した経験がないため非常に先行きが怪しい状況となっていました。
選択肢としてH2Oを捨ててnginxに切り替えるという手もあるのですが、環境構築にも手間取る我々の戦力では新しくアプリケーションをインストールする際に多大な面倒が生じうることは目に見えています。

```
upstream supercoolawesomeservice.com{
	server foo.com;
	server bar.com;
}
```
などと書けばnginxならロードバランスしてくれるということは知っていました(実際に試したことはない)が、H2Oのドキュメントを探したものの[どうにも難しそう](https://github.com/h2o/h2o/issues/775)な気配がしていました。

とはいえ現状で足りていないのは計算資源であるため、適当な切り分け方だったとしてもサーバー1台よりはきっと良くなるに違いないだろう、ということで、なんとなく負荷が高そうに見えた `/api/users` 以下に送られたリクエストだけをすべて2台目に流すことにしました。

その作業中に、 `/initialize`へのアクセスを受けるとDBではなくローカルファイル上の.shファイルが読まれるため、DBサーバーを別に分けると初期化処理に失敗することが判明しました。
そこでDBサーバーの停止させていたH2Oとnodejsを復活させ、`/initialize`へのアクセスはこのDBサーバー上で処理することとしました。
しかしこの対策は別サーバーへのリダイレクトが成功しないと有効でないため、H2Oをリバースプロキシとして使用する設定が完了するまではDBも動かないという状況になってしまいました。

この設定変更に2人とも苦戦し、3台構成がまともに動いたのは17時を大きくまわった頃で、残り時間は1時間もありませんでした。

細々とした不具合を修正したりしたものの、17時40分ごろに走らせたベンチマークがすべて失敗し、スコアが🈚️なのはよくないということでロールバックを決定しました。
ここで、ネットワークまわりをロールバックする方針を採用したもののうまくいかず、アプリケーション部分も合わせて巻き戻したものの`initialize/`か何かの処理が走らず失敗し、制限時間のためスコアなしの失格となりました。

実際にはこのロールバック方針は誤りで、アプリケーションロジック側に不具合が生じて時おりエラーが出るようになっていたというのが原因でした。
nodejsのアプリケーション内でエラーが出ているというログは上がっていたのですが、それまで設定に手間取って何度もバグを出していたネットワークまわりが原因であるように思い込んでしまいました。

## 反省会
明らかな敗因としては練習不足で、これはまあわかりきっていたことなのでよいのですが、最終的にスコアなしで終わってしまった直接の原因としては不具合の出どころを見誤ったことにあると思います。
複数台構成に切り替える際に断続的に一部のサーバーが動かなくなったり復帰したりを繰り返していたのですが、各自の作業内容のリアルタイムでの共有が不十分だったため、アプリケーションの動作確認ができない時間ができてしまったり、生きているサーバーの情報が行き渡らずに1台構成での動作確認のためのベンチマークを走らせられなかったりしました。
そのため後半以降にベンチマークを実行できる機会が減ってしまい、結果としてネットワークとアプリケーションのどちらで落ちているのか確証が持てない状況が発生し、時間切れ直前の焦りも加わって大きな失敗となってしまいました。
これはログをしっかり読んでいれば・ログを取得しやすい環境を構築していれば防げたことでもあると思いますし、チームからはVCを導入しておいたほうがよかったかもしれないという意見もありました。

まっとうに戦えるチームならこのぐらいのことは一瞬で終わらせているんだろうなあ、と思うことも多々ありました。
とはいえ、単純な設定変更や環境構築でも何らかの失敗をして時間がかかってしまうのは、ある意味最初から想定していたことではあります。
しかしせめてアプリケーションがちゃんと動く状態でのスコアがほしかったという悔しさがあります。
拙いながらも自分たちで考えた構成が動いているところが見たかったですし、アプリケーション側でもスコアへの影響の大きいスロークエリの改善コミットが入っていたので、これをスコアに結びつけられなかったことは残念です。

私は最初JavaScriptを書いていくことになるかと思っていましたが、蓋を開けてみればアプリケーションはほぼ@uhyo\_氏に投げてしまっていました。
実際生のSQLを動作を変えないまま書き換える能力は今の私にはなさそうな気もしたので、中盤までのスコア上昇を支えてくれた@uhyo\_氏の活躍には感謝の念に堪えません。

結局私がなにをしていたかというと、基本的には@sensu\_watson氏と一緒に設定ファイルやログファイルを眺めてウンウン唸っていたのが主な仕事になるような気がしますが、過去10年で打った`systemctl`コマンドの数を今日一日で軽く越えたり、見様見真似で全く未経験のサーバー複数台構成をその場ででっちあげたりすることができたので、これはこれで非常に刺激的な経験になったと思います。

最後にはなりますが、この場で謝辞を述べられるべきすべての方々に感謝です。