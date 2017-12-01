+++
categories = ["dev"]
date = "2017-12-01T17:35:00+09:00"
draft = false
slug = ""
tags = ["japari-watch", "javascript", "pixijs"]
title = "ゲーム「じゃぱりうぉっち」を作りました"

+++

この記事は[Kobe University Advent Calendar 2017](https://adventar.org/calendars/2491)の1日の記事です。
なお私は当該大学の学部3年(2017年12月現在)です。

---

ブラウザ上で動作するゲーム「[じゃぱりうぉっち](https://tatamo.github.io/japari-watch/)」を作ったので、開発記を書きます。

<!--more-->

## じゃぱりうぉっち

https://tatamo.github.io/japari-watch/ で公開しており、PCとモバイル端末のどちらでも遊ぶことができます。

### 発端
期末試験前に現実逃避のためにTLを眺めていたところ、以下のツイートが流れてきました。
<blockquote class="twitter-tweet" data-lang="ja"><p lang="ja" dir="ltr">フェネック「アライさんに似合いそうな帽子を見つけてきたよー」<br>アライグマ「ありがとうなのだ！」<a href="https://twitter.com/hashtag/%E3%81%91%E3%82%82%E3%81%AE%E3%83%95%E3%83%AC%E3%83%B3%E3%82%BA?src=hash&amp;ref_src=twsrc%5Etfw">#けものフレンズ</a> <a href="https://twitter.com/hashtag/%E3%83%89%E3%83%83%E3%83%88%E7%B5%B5?src=hash&amp;ref_src=twsrc%5Etfw">#ドット絵</a> <a href="https://twitter.com/hashtag/pixelart?src=hash&amp;ref_src=twsrc%5Etfw">#pixelart</a> <a href="https://t.co/1VueMavObQ">pic.twitter.com/1VueMavObQ</a></p>&mdash; 鬼雷 昇炎 (@kirai_s) <a href="https://twitter.com/kirai_s/status/934412479597199361?ref_src=twsrc%5Etfw">2017年11月25日</a></blockquote>

<blockquote class="twitter-tweet" data-lang="ja"><p lang="en" dir="ltr">Version Upgrade Nanoda!! <a href="https://t.co/IXSjV6A1PN">pic.twitter.com/IXSjV6A1PN</a></p>&mdash; 鬼雷 昇炎 (@kirai_s) <a href="https://twitter.com/kirai_s/status/934695920414765056?ref_src=twsrc%5Etfw">2017年11月26日</a></blockquote>

作品としての完成度が高く、実際にゲームとして遊べそうに見えたので、これをゲーム化してみました。

### 素材
<blockquote class="twitter-tweet" data-lang="ja"><p lang="ja" dir="ltr">原寸(128*128px)なのだ <a href="https://twitter.com/hashtag/%E3%81%91%E3%82%82%E3%81%AE%E3%83%95%E3%83%AC%E3%83%B3%E3%82%BA?src=hash&amp;ref_src=twsrc%5Etfw">#けものフレンズ</a> <a href="https://twitter.com/hashtag/%E3%83%89%E3%83%83%E3%83%88%E7%B5%B5?src=hash&amp;ref_src=twsrc%5Etfw">#ドット絵</a> <a href="https://twitter.com/hashtag/pixelart?src=hash&amp;ref_src=twsrc%5Etfw">#pixelart</a> <a href="https://t.co/BJ0Mc27vR5">pic.twitter.com/BJ0Mc27vR5</a></p>&mdash; 鬼雷 昇炎 (@kirai_s) <a href="https://twitter.com/kirai_s/status/934699058542678016?ref_src=twsrc%5Etfw">2017年11月26日</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

元画像が上がっていたので使わせていただきました。

背景に相当する部分の画像がなかったので自分で加工したり、そのまま矩形状にスプライトを切り出すと被ってしまう部分があったのでいくつかのパーツに分けたりしました。

## 製作期間
ゲーム本体の作成(アセットの用意・環境構築含む)： 一晩  
公開のためのWebページデザインやTwitterカード、faviconの用意：半日  
公開後の難易度調整+自動モード機能の追加：半日

## フレームワーク選定
ブラウザで動作するゲームを作ろうと思ったため、基本的にはNode環境で開発を進めていくことになります。
高速に開発するためにTypeScriptで記述することとしたうえで、使用するツールを選定していきました。
### PIXI.js
PC・モバイル両方のブラウザで動作するゲームという方針のため、HTML5 Canvas系のライブラリを使うことにしました。
npm上で型定義が用意されているものを使いたかったので、今回は[PIXI.js](http://www.pixijs.com/)を選択しました。

<code class="shell">$ npm install --save-dev @types/pixi.js</code>で型定義ファイルをインストールし、
```TypeScript
import * as PIXI from "pixi.js";
```
でPIXI.jsの型情報を使用可能になります。

#### ビルドシステム
また、今回はブラウザを動作対象とするため、jsファイルのバンドルとBabel化のためにwebpackを使用しました。
実際のところwebpackもBabelもこれまで使ったことがなかったのですが、なんとかts-loaderとbabel-loaderでTypeScriptのソースコードをBabel化してバンドルするところまでやりました。

期末試験直前であまり学習コストをかけられず、これ以上webpackであれこれするのは怖くなったので、jsファイル以外の静的ファイルのコピーなどはrimrafやcpxを使い、npm scriptsでビルドすることにしました。
また開発中の自動コンパイルのために、chokidarとbrowser-syncを使いました。

## 設計
一定時間ごとにゲームループをまわし、大元のゲームオブジェクトから各マネージャのupdateメソッドを呼び出して処理を行い、最後にレンダリングを行うという基本的な設計としています。

今回は、エンティティを管理するエンティティマネージャ、スコアを管理するスコアマネージャ、エンティティとスコア以外のゲーム内表示を管理するエフェクトマネージャ、ユーザーからの入力を受け取るインプットマネージャを用意しました。
また、スコアマネージャはインスタンスを2つ生成してそれぞれ取得スコアとミス回数を管理するようにしています。

ステートマシンを用いてゲーム状態の遷移を管理する方法もありますが、今回はタイトルとゲーム中とゲームオーバーの三状態しかなく、また各エンティティも決まった動きしかしないため不要と判断しました。

エンティティごとにスプライトのテクスチャを用意しておく必要があり、これはstaticメンバとして各エンティティのクラスに紐付けることにしました。
このテクスチャの生成はアセットのローディングが終わった後に行う必要があるため、エンティティマネージャの初期化時に各staticメソッドを呼び出すことで初期化するようにしました。
### EventEmitterによるメッセージング
PIXI.jsで用意されているクラスは[EventEmitter](https://github.com/primus/eventemitter3)を継承しており、イベント駆動でのオブジェクト間のメッセージングが可能です。
たとえば`アライさん`オブジェクトは`Hat`コレクションに対する参照を持ちませんが、イベントの発火にフックしてエンティティマネージャがメソッド引数にコレクションの参照を流し込むことで、`アライさん`オブジェクトは外部の参照を持つことなく`Hat`コレクションにアクセスし、当たり判定の処理を行うことができます。
これにより、ゲーム内エンティティを表すオブジェクト同士を疎結合に保つことができます。
`Hat`のように複数のインスタンスが生成される場合、そのたびにイベントの登録を行う必要があるので、このようなクラスはファクトリを通して実体化するようにします。

その他にも、インプットマネージャがキーボード入力を受け取ったり、ゲーム画面上に配置されている透明のスプライトに対するクリック・タッチ入力を受け取った場合に、入力された動作に応じたイベントを発火することで、入力の種類を隠蔽したうえでそれにフックする処理を定義可能になります。

## はまりどころ
ちょっとしたミスなどですが、詰まった点について。

### エンティティの消去
ループ中にループ対象の配列を変更するな、というのは基本ですが、ゲームの場合当たり判定がヒットしたエンティティを消去したいという場面は多いです。
`Hat`コレクションに対してループを回して`update()`を呼び出し、その処理の中で`Hat`オブジェクトを消去してしまい、それ以降のオブジェクトの処理がおかしくなるというバグが発生しました。

対処として、まずエンティティに`alive`フラグを持たせておき、エンティティを消去したいときには以下のような処理を呼び出すだけにします。
```TypeScript
die() {
    this.alive = false;
}
```
次に、全エンティティのupdate処理終了後に再度ループを回し、ここでaliveフラグがfalseとなっているエンティティをコレクションから取り除きます。
ただし、死んだはずのオブジェクトの当たり判定が残ったりすることを防ぐため、一度aliveフラグがfalseになったエンティティは処理から除外するようにしておく必要があります。
また、結局ループ中の配列を弄ることになるので、このループの中でおかしなことが起きるかもしれないので注意が必要です(とはいえ削除処理を別のループに分けていることで、最悪でもエンティティの削除が次回以降のフレームに引き延ばされてしまう程度の影響しか起きません)。

ループ中に配列から安全に要素を取り除く方法としては、要素の削除時にイテレータをうまく調整したり、ループインデックスをずらして対処するなどといった方法がありますが、イテレータの仕様を正しく理解している必要があったり、削除処理のタイミングで自分の外側のループのことを考えないといけなかったりするため、処理がやや煩雑になりがちです。
そこで技巧的な方法として、ループのインデックスを配列の末尾から順に0に向けてデクリメントしていけば、途中で要素が取り除かれてもループに影響が出ない、といったものがあります。
ただし処理順は逆になってしまうので、それが気になるなら先述したエンティティ削除用のループでのみ行うなどする必要があります(エンティティの削除処理程度なら順序が変わっても影響が出ないため)。
最も単純なのはループ前にコレクションをコピーしてループを回すことですが、ゲームプログラムで毎フレームごとに配列のコピーを生成するのはパフォーマンス的に避けたいところでもあります。
なお今回は、`Hat`オブジェクトは同時に10個も存在することはないため、毎回配列をコピーして削除用のループを回すことにしました。

### faviconの配信
`favicon.ico`を配信しようと思ったのですが、https://tatamo.github.io/japari-watch/ というURLのルートパスはhttps://tatamo.github.io/ であるため、https://tatamo.github.io/japari-watch/ 直下にfaviconを配置しても正しく読み込ませることができません。
そのため、`<link rel="shortcut icon href="favicon.ico" type="image/x-icon"">`などとして明示的にfavicon.icoの位置を示す必要がありました。

また、`manifest.json`をhttps://tatamo.github.io/japari-watch/favicons/manifest.json に、同様にandroid用のアイコン画像を`favicons/`ディレクトリに配置していました。
`manifest.json`中の相対パスは基準となるディレクトリが`manifest.json`自身となることを知らなかったので、srcパスを`"favicons\/android-icon-***.png"`としたところ、`favicons/favicons/`が読まれてしまって正しくアイコンを配信できなかったりしました。

## おわりに
[PIXI.js](http://www.pixijs.com/)はチュートリアルだけは読んだことがあるという状態で、一度実際に使ってみたいと思っていたので今回使用することができて良かったです。
ドキュメントをろくに読み込まずに使いたい機能だけ調べて使うといった感じでしたが、かなり高速にゲーム開発ができたので非常に便利でした。
またwebpackやBabel、npm scriptsをメインに用いたビルドなどもはじめてだったのですがいい感じにできました。

使用した画像については作者である[@kirai_s](https://twitter.com/kirai_s)さんに確認を得ないまま加工して作り始めてしまったのですが、使用を快く承諾していただけました。
この場を借りてお礼申し上げます。
