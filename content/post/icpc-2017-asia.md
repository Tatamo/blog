+++
title = "ACM-ICPC2017アジアつくば大会に参加しました"
categories = ["diary"]
tags = ["icpc","competitive-programming", "contest"]
slug = ""
draft = false 
date = "2017-12-18T01:30:00+09:00"

+++

この記事は[Kobe University Advent Calendar 2017](https://adventar.org/calendars/2491)の18日の記事です。
なお私は当該大学の学部3年(2017年12月現在)です。

---

[ICPC国内予選39位](https://icpc.iisf.or.jp/2017-tsukuba/results/)で神戸大学の第2チームとしてぎりぎり予選通過できたので、[アジア大会](https://icpc.iisf.or.jp/2017-tsukuba/)に参加しました。

結果としては3問通して[39位](http://icpc2017.yamagula.ic.i.u-tokyo.ac.jp/standings/)でした。

<!--more-->

<blockquote class="twitter-tweet" data-lang="ja"><p lang="ja" dir="ltr">実績「座っているだけのお仕事」<br>解除条件：ICPCで一度たりとも競技用マシンに触れない<br>を達成しました💺💤</p>&mdash; Tatamo@Super-Srups (@__tatamo__) <a href="https://twitter.com/__tatamo__/status/942290374382383104?ref_src=twsrc%5Etfw">2017年12月17日</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>
なにもしないをしました

## チーム構成・方針
私の所属するチームであるSuper-Srupsのメンバー構成は以下の通りです。

* [@mmxsrup](https://twitter.com/mmxsrup)氏
	* 学部2年
	* リーダー
* [@kurenai3110](https://twitter.com/kurenai3110)氏
	* 学部2年
	* 実装担当
* [@\_\_tatamo\_\_](https://twitter.com/__tatamo__)
	* わたし 学部3年
	* 座っている担当

私は3人の中で最も実力が低く、かつここ数ヶ月のあいだ全くC++を書いていないという状態です。
つまり論理的結論として、私は一行もコードを書くべきではありません。
そのため私は英語で与えられる問題文の読解、他メンバーの補助および考察少しを担当しました。
他の2人はUSキーボードのEnterキーが空振りするとかgeditがつらいとかubuntuの使い方がわからないとか大変そうでした。

## 解いた問題
問題の内容については[問題文](https://storage.googleapis.com/icpcsec/icpc2017-regional/problems.pdf)を参照してください。

### A
やるだけ問です。
問題文を読んで[@kurenai3110](https://twitter.com/kurenai3110)氏に投げたらよろしくやってくれました。

### C
[@mmxsrup](https://twitter.com/mmxsrup)氏と考察を担当し、実装は[@kurenai3110](https://twitter.com/kurenai3110)氏にやってもらいました。
値が負になるときの特殊ケースや変数の更新タイミングのミス等のちょっとした考察漏れ・実装ミスを3つほど同時に仕込んでしまって数十分ほど無駄に消費してしまったので反省点が多いです。

### I
B問題・C問題で無限に時間を費やしているうちに他チームは問題をたくさん解いていたので、風船の浮かんでいる状況を見てI,F,G問題に解く対象を絞りました。
なお今年の問題の並び順は、A,B,C問題までが易しい順に並んでおりそれ以降の問題はランダムになっています。

I問題は問題文を読んで[@kurenai3110](https://twitter.com/kurenai3110)氏に丸投げしたら見事に解いてくれました。

## 解けなかった問題
### B
[@mmxsrup](https://twitter.com/mmxsrup)氏がメインで担当しました。
制限実行時間10秒の問題ですが、最初にすべての可能な傾きの同じ線分の集合を持ったりしていたらハマってしまって通せませんでした。
普通に全探索すれば解けるなどということを終了後に聞きましたが、全然通らなかったのでいっそ早めに放り投げるべきだったのかもしれません。

### F
私はグラフは最短経路の超典型問ぐらいしか解けないので、問題を訳すだけして他の２人に投げました。
実装の最後の方まで行っていたようなのですが間に合いませんでした。

### G
考察を担当しました。
座標変換で簡単になるというのは気付きませんでしたが、極座標→XY座標への変換をしてからdX=正三角形の辺の長さの半分, dY=正三角形の高さ で区切って場合分け2回ほどで数学的に求めて終わりです。

## 結果
A問題解いた直後に沼にはまって時間を無為に費やし、終盤になってB,F,Gの解法が出揃ったが実装時間が圧倒的に足りないという状況に陥り、結局4問目を通せず3完でした。
予選と全く同じ順位の39位です。

ICPCはやはりマシン1台のチーム戦ということで、特有の難しさがあるので事前の慣らしはもっとしておいたほうがよかったような気がします。
あとUSキーボードは思った以上に慣れない(らしい)のでこちらも事前準備が必要そうです。

とはいえ、そもそも私は自力で問題を解けないので、問題文の和訳と考察補助とただ座っているぐらいしか仕事がない感じです。
こういう場にカジュアル勢が紛れ込んでしまって肩身が狭いというか申し訳無さです。

## 雑事
1日目は昼過ぎに集合で、参加受付+開会式+練習+懇親会だけで、解放される時間がなぜか20時です。
思うところがないでもありません。

2日目についても、終了後の懇親会で2時間拘束されます。
こちらでは企業ブースがいくつかあり、物をもらったり話を聞いたりすることができました。[ツアー](2017/09/17/indeed-austin-tour/)で一緒だった某r(i)社の方々とまた会ったりしました。

[量子リバーシ](https://proc-cpuinfo.fixstars.com/2017/12/in-house-procon-06-announcement/)なるクイズが出題されており、こういうのは[量子将棋](http://shogitter.com/rule/108)とか[量子人狼](http://uhyo.hatenablog.com/entry/2016/04/22/161011)とか[量子2048](https://uhyohyo.net/quantum2048/)とか作ってる人たちが周りにいるから見慣れてるんだよなあなどと思いつつ、順に確定させてから普通のオセロをシミュレートするだけのやるだけ問を3分ぐらいで解いたら驚かれました。

また他のブースではよくわからないクイズを解くだけでANKERの10000mAhモバイルバッテリー(自分が持っている物の一つ下のモデル)をもらうことができました。

## つくば
交通費補助が新幹線代程度出るものの、宿泊施設の提供なしで宿泊補助費は5000円しか出ないので新幹線で行くと大赤字です。
そこでLCCで飛んでいくことで交通費を浮かせて2泊するための宿泊費に充てました(これって書いていいんでしょうか)。

宿泊施設が用意されなくなったのは今年からとのことですが、ICPC協賛の某企業の方に聞いた話によると、どうもスポンサー費用は例年上がっていく一方らしいです。
よくわかりません。

そこそこ移動がつらく、朝6時頃には家を出て空港まで行ったかと思えば移動の間に2時間ぐらい待機する必要があったりしました。
また宿はできるだけ安いところを取りましたが、1日目は浴場の給湯器が故障していて使えず、2日目は浴場に入ることはできたものの、あまりにお湯がぬるいので仕方なくサウナで温まるなどすることになりました。

1日目は部屋に備え付けの浴室を使いましたが、湯を張るために蛇口を開くと汚れの混じった赤っぽい水が出てきたので、無心でシャワーを浴びてそのまま何も考えずに寝ました。

## 3日目
エクスカーションや企業見学に参加する人に合わせて飛行機を取ったので、3日目は夜まで暇です。
ほとんどの人は企業見学に行ったので、残りの3人で東京観光に行きました。
大洗は非常に遠かったのでやめておいて、VRの体験や靖国神社に行ったりして所々で先輩におごってもらいました。
大荷物を持って長距離を移動した上に懇親会等で立ちっぱなしで疲労が蓄積していたので、夕方はカフェで休憩しながら時間を潰していました。
