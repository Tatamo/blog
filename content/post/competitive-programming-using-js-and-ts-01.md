+++
date = "2018-02-09T17:25:00+09:00"
draft = false
slug = ""
tags = ["dev"]
categories = ["competitive-programming"]
title = "JavaScript/TypeScriptで競技プログラミングをするには 前編"

+++

主要コンテストサイトのJavaScript対応状況、および私がJavaScript/TypeScriptで競技プログラミングをする時に気をつけていることなどを書きます。
今回は前者についてです。

<!--more-->

追記：[後編](/2018/02/09/competitive-programming-using-js-and-ts-02/)書きました。

競技プログラミングをやるのにC++を選択しない時点で物好きという気はしますが、私は普段からTypeScriptをメインに使って問題を解いています。
この前AtCoder Problemsに[Language Owners Ranking](http://kenkoooo.com/atcoder/?kind=lang)ができていたので見てみたらTypeScriptで1位を取っていました(2018年2月時点)。
ちなみにJavaScriptランカーのAC数と比較すると3位にも入れていません。

AtCoderレーティングは水色、コンテスト参加回数もぎりぎり二桁に達する程度で、あまり真面目にやっているほうではないので参考になるかどうかはわかりませんが、JavaScriptまたはTypeScriptで競技プログラミングをするときに気をつけるべき点などを書いていきます。

## JavaScriptで競技プログラミングをすることについて
こんな記事を書いていて言うのもどうかと思いますが、他の言語ではなくJavaScriptを競技プログラミングに使用することに、何かメリットはあるのか？と聞かれれば、おそらく目立った利点を挙げることはできないと思います。

とはいえ、`function`だらけの黒魔術のようなコードとスクリプト言語特有の遅さは、ここ数年のJavaScriptの目覚ましい進化によって克服されてきています。
現在のJavaScript処理系はJITコンパイルが主流のため、C++やJavaのような言語には敵わないとはいえ、PythonやRubyのようなスクリプト言語よりも圧倒的に高速です。
またECMAScript2015の登場により、もはや`function`と書く必要はなくなりました。

もっとも、JavaScriptが競技プログラミングに不向きな理由もいくつか存在することは事実です(次の記事で紹介します)。
ですが、近年のJavaScriptが以前とは見違えるほどに書きやすい言語になったことから、決して選択肢に入らない言語ではないと私は考えています。

JavaScriptを使用する最大のモチベーションは、それが書きやすい・手慣れた言語であるからという点に尽きると思いますが、これは重要なことです。

## コンテストサイトのモダンJavaScriptやTypeScript対応状況
JavaScript/TypeScriptで競技プログラミングをするためには、コンテストサイトのオンラインジャッジシステムが少なくともJavaScriptに対応している必要があります。
特にJavaScriptという言語は、ECMAScript2015の策定によって言語仕様が大きく拡張され、その後もECMAScript2016, ECMAScript2017と機能追加が進んでおり、ここ数年間での移り変わりが非常に激しい言語であるため、言語環境が整備されていることの重要性は大きいです。

ACM-ICPCではC/C++, Java, Python2/3しか使えないのは周知の事実ですが、比較的多くの言語に対応しているコンテストサイトであってもJavaScriptの対応が行き届いていないのが現状です。

まずは、日本である程度知名度がある(と思われる)コンテストサイトについて、2018年2月現在でのJavaScriptの対応状況を見ていきたいと思います。
なおTopCoderはJavaScript非対応のため除外しています。

### [AtCoder](http://atcoder.jp/)
* JavaScript node v5.12.0
* TypeScript 2.1.6

TypeScriptについてはnode.jsのバージョンが記載されていなかったのですが、おそらく5.12.0であると思われます。

[node.green](http://node.green)によると、5.12.0ではES2015の対応率は59%程度となっており、次の項目の6.4.0で対応率が95%まで上昇していることを見ると残念な感じです。
しかしAtCoderで注目するべきは、オンラインジャッジ系のサイトでは非常に珍しく、TypeScriptに対応していることです。
たとえば、分割代入構文はnode.js v5.12.0では未対応のため、以下のコードはシンタックスエラーとなります。
```javascript
const [a, b, ...c] = [1, 2, 3, 4, 5];
```
しかしこれをTypeScript2.1.6に食わせると、勝手にES5(= Bad Old DaysのJavaScript)仕様にpolyfillしてトランスパイルしてくれるため、以下のJavaScriptコードが生成されます。
```javascript
var _a = [1, 2, 3, 4, 5], a = _a[0], b = _a[1], c = _a.slice(2);
```
つまり言語欄でTypeScriptを選択することで、ES2015の仕様をもう少し多く使うことができるようになります。

もっともProxyなんかは全く使うことができないのですが、とはいえSet・Map、class、アロー関数および分割代入が使えるので、競技プログラミングで用いる範囲内なら十分にモダンなJavaScriptを書くことができます。
コンパイラオプションなしでTypeScript2.1.6にSetやMapを食わせると`Cannot find name 'Map'.`等のエラーが出たような気もするのですが、AtCoder上だと特にエラーメッセージは表示されずに無理矢理動かしてくれます。

TypeScriptはJavaScriptの完全なスーパーセットであるため、すべてのJavaScriptコードはTypeScriptコードとみなすことができます。
TypeScriptの仕様を全く使っていないJavaScriptを書いたとしても、AtCoderではTypeScriptとして提出することをおすすめします。

なんともいえない感じですが、これでもモダンJavaScriptの対応状況としてはよっぽどマシなほうです。

### [yukicoder](https://yukicoder.me/)
(正直ここがどれくらいの知名度があるのかあまり把握していませんが)

* JavaScript node v9.2.1

調べた中で最も最新バージョンに近く、ES2017までのほぼすべての機能が使用可能なJavaScript環境が用意されています。
モダンなJavaScriptを書きながら競技プログラミングを楽しみたいなら、このサイトはおすすめといえます。
TypeScriptには対応していませんが、まあトランスパイルされた.jsファイルを提出すれば済む話でしょう。

### [AIZU ONLINE JUDGE](https://qiita.com/konu96/items/0b403c1b32b9dafc5ff9)
* JavaScript node 0.10.36

[http://judge.u-aizu.ac.jp/onlinejudge/AOJ_tutorial.pdf](http://judge.u-aizu.ac.jp/onlinejudge/AOJ_tutorial.pdf) に書いてありました。

ほぼ完全なES5時代の遺物です。
どうしてもJavaScriptが書きたいなら、babel-polyfillあたりをかませてJavaScriptからさらに古い時代のJavaScriptにトランスパイルして提出するのがいいでしょう。

### [Codeforces](http://codeforces.com/)
* JavaScript V8 4.8.0

node.jsを使っていただきたいところです。
V8 4.8.0はnode.jsでは5.12と6.0.0の間ぐらいなので、ES2015の対応状況ではAtCoderのやや上といったところです。古いことにかわりはありませんが。

最大の問題点は、標準入出力の方法がnode.jsとは全く異なるという点です。
node.jsでは`console.log()`で出力しますが、V8の場合は`print()`か`write()`になります。

JavaScriptの処理系としてnode.jsを使わずにわざわざV8を生で叩いている人間ってどれくらいいるんでしょうか？
ちょっと怖いのであまり触れたくない感じです。

---

ここまで書いていて思った以上に分量が膨らんでしまいました。
[後編](/2018/02/09/competitive-programming-using-js-and-ts-02/)では、実際にJavaScript/TypeScriptで問題を解く上で気をつけている点を書いていきます。

