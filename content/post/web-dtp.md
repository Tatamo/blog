+++
date = "2017-12-06T18:30:00+09:00"
draft = false
slug = ""
tags = ["html", "css", "vivliostyle", "dtp"]
categories = ["dev"]
title = "Web技術ではじめるレポート・論文制作"

+++

この記事は[Kobe University Advent Calendar 2017](https://adventar.org/calendars/2491)の6日の記事です。
なお私は当該大学の学部3年(2017年12月現在)です。

---

[Vivliostyle](http://vivliostyle.com/ja/products/)やその他のWeb技術を用いて、HTMLとCSSで課題のレポートを書くための環境を作る話です。
あと「[CSSではじめる同人誌制作](https://pentapod.github.io/c92/)」を勝手に販促する記事でもあります。

<!--more-->

## HTML+CSSによる組版

個人的にはWordもTeXもあまり得意ではない、というか、HTML+CSSで似たようなことができそうなのにわざわざWordやTeXを使うのが大変だと感じています(InDesignはよく知りません)。
文書構造の明確化という観点においては、構造と見た目が明確に分離しているHTML+CSSに勝るものはありません(筆者個人の主観です)。

そこでWeb技術を用いてレポートや論文を書きたいなあと思っていたのですが、ちょうど[セキュキャンの事前準備で行けなかった](/2017/08/27/seccamp-2017/)ときのコミケで、pentapodさんより「[CSSではじめる同人誌制作](https://pentapod.github.io/c92/)」が頒布されていたので、友人に頼んで確保しておいてもらいました。

<blockquote>内容については同人誌制作だけをターゲットに絞らず， できるだけ色々な場面で使えるような説明を心がけています．</blockquote>
というあとがきの記述通り、CSS組版全般、また単にCSSの解説本としても読めるようになっており、本書を参考にしてレポートを書くための環境を整えることができました。
同人誌でもレポートでも論文でも、やりたいことは結局同じ組版であるため、整えるべき環境は変わりません。

## この記事で書くこと
CSSを用いた組版については、「[CSSではじめる同人誌制作](https://pentapod.github.io/c92/)」を直接読んでいただくのが一番早いため、今回はあまり詳しくは書きません。
[300円というお手頃価格で電子版も出ている](https://pentapod.booth.pm/items/601017)ので買ってください。
これは販促です。

そこでこの記事では、本書を参考にしながら行った環境構築を中心に書いていきます。
構築した環境は https://github.com/Tatamo/htmldtp に上げていますが、「そもそもボイラープレートという単語を数日前にはじめて知った」レベルのためディレクトリ構成ひとつとっても正直褒められたものではないと感じています。
なのでまたちゃんと体裁を整えたいと思います。

## Vivliostyle
とはいえ、[Vivliostyle](http://vivliostyle.com/ja/products/)については触れておかないと話が始まりません。
これはWeb技術による組版を目的としたプロジェクトで、CSSの将来的な仕様を先取りすることで、より多彩なレイアウトを実現できるようにしているものです。
[サンプル](http://vivliostyle.com/ja/samples/)が充実しており、実際にCSSによってデザインされた本と、その生のHTMLを閲覧することができます。

[こういうレベルのこと](http://vivliostyle.github.io/vivliostyle.js/samples/css-secrets-long/viewer/vivliostyle-viewer.html#x=../doc/)がCSSで既に可能となっているのです。

Vivliostyleを用いた印刷を行う方法は何種類かありますが、[Chrome拡張](https://chrome.google.com/webstore/detail/vivliostyle/ffeiildjegeigkbobbakjjmfeacadbne)を用いるのが最も簡単だと思われます。
目的のHTMLファイルを表示した状態で拡張機能を有効化するとレイアウトが紙面向けのものに変換されるので、これをChromeの印刷機能を用いて紙面やPDFに印刷します。

そのため、Vivliostyleに対応したCSSファイルと、それを適用するための原稿となるHTMLファイルを用意することが目的となります。
HTMLとCSSの状態からPDFを生成する作業を自動化する方法についても「[CSSではじめる同人誌制作](https://pentapod.github.io/c92/)」では解説されていますが、今回は省略するので本書を購入してください。

恥ずかしい話ですが、最近のCSS3の仕様をしっかり追いかけているわけではないため、どこからどこまでがVivliostyleによって実現されている機能で、どこまでがブラウザの標準機能で既にサポートされている部分なのかがよくわかっていません。
このあたりは書けば書くほどボロが出るに違いないので適当にごまかします。

## フレームワーク選定
書籍ではPug+marked+Stylus+Prism.js+MathJax+Browsersync+Gulpの構成が紹介されていましたが、このあたりは好みだと思うので適当に選んでいきました。

結論としては、Nunjucks+nunjucks-markdown+PostCSS(cssnext+stylelint)+highlight.js+MathJax+Browsersync+Gulpという構成になりました。
### Nunjucks
まずはHTMLテンプレートエンジンを選びます。
変数を用いた操作や別のHTMLの継承などの機能を取り入れることで、HTMLをより書きやすくして開発の高速化・再利用の促進を図ります。

今回は[Nunjucks](https://mozilla.github.io/nunjucks/)を使うことにしました。
昔Djangoを触ったことがあるので見慣れたテンプレートであるという点などが採用理由です。
他には以前使ったことのあるEJS、紹介されていたPug(Jade)なども候補に上がりました。

```html
<!-- template/_base.njk -->
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <title>{{ title }}</title>
</head>
<body>
{% block content %}
    <section>
        <p>default content</p>
    </section>
{% endblock %}
</body>
</html>
```

```html
<!-- index.njk -->
{% extends "template/_base.njk" %}
{% set title = "awesome-report" %}
{% block content %}
<section>
    <!-- awesome-report-content -->
</section>
{% endblock %}
```
のような感じでHTMLにいろいろなものをくっつけていきながら書けるようになります。
Nunjucksで記述したHTMLは、.htmlのほかに.njkなどの拡張子を使ったりするらしいです。
### nunjucks-markdown
ひたすら文章を書くのにいちいち`<p>`タグを書いたりしていられないので、Markdownを使えるようにすることが必要です。
Nunjucksを使うことにしたので、markedのNunjucks向けプラグインである[nunjucks-markdown](https://www.npmjs.com/package/nunjucks-markdown)を使うことにしました。

````html
{% block content %}
<section>
    {% markdown %}
    ## PPAP
    I have a pen.
    I have an apple.
    ```js
    console.log("Apple pen!");
    ```
    {% endmarkdown %}
</section>
{% endblock %}
````
のように、`{% markdown %}`で囲った部分をMarkdownとして書くことができようになります。

### PostCSS(cssnext+stylelint)
HTMLだけでなくCSSも次世代仕様を使用できるようにしておくと便利です。
SassやStylusという選択肢もありますが、この次世代CSS過渡期において独自記法マシマシのcoffeeとあくまでES2015をもとにしたtsならどちらを使うかという話です。
一通り必要な機能が入ったプラグイン集である[cssnext](http://cssnext.io/)と、lintをしてくれる[stylelint](https://stylelint.io/)を入れていますが、この構成だとSassのように`@import`できない点で不便さがあるので、CSSをモジュール化できるようにするためにpostcss-importも入れたほうが良いかもしれません。

<blockquote class="twitter-tweet" data-lang="ja"><p lang="ja" dir="ltr">gulp-postcssを使っている際にstylelintでインデント幅をlintするよう指定している状態でAutoprefixerなどで右寄せしたprefixが追加された場合、なぜかsrc側のCSSのインデントが崩れているとしてstylelintに怒られてしまうので、plugin設定にはstylelintを一番先に置くべきという知見を得ました <a href="https://t.co/IMnmqibetC">pic.twitter.com/IMnmqibetC</a></p>&mdash; たたもさん@がんばらない (@__tatamo__) <a href="https://twitter.com/__tatamo__/status/928286264914145280?ref_src=twsrc%5Etfw">2017年11月8日</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>
これは余談です。

### highlight.js
プログラムリストを貼り付けることが求められることもあるので、シンタックスハイライターを用意します。
他の候補としてはPrism.jsなどがあります。
今回はこのブログで使ったりして使い慣れている[highlight.js](https://highlightjs.org/)を用いました。

### MathJax
TeX形式で書いた数式を表示するために必須です。
[MathJax](https://www.mathjax.org/)以外の選択肢は特にないと言っていいでしょう。
適当なスニペットを`<head>`タグ内に追加し、
```html
<p>MathJax test: $E = mc^2$</p>
```
などと書くだけで数式部分が整形されて表示されます。

どうでもいい余談ですが、個人的にはMicrosoft Officeの数式エディタは見た目もきれいですし非常に使いやすいと思っています。
これについてはあまり同意されたことがない気がします。

### Browsersync
ソースコードが更新され(、そしてそれを検知してビルドが走って`dist/`ディレクトリが更新され)たことを検知し、[Browsersync](https://www.browsersync.io/)が自動でブラウザを再読み込みしてくれるようにします。
わざわざF5を押さなくても変更が反映され、ビルド結果がすぐにブラウザ上で見れて便利…… なのですが、今回はブラウザを更新したあとVivliostyleのChrome拡張を有効化しなければならず、結局F5相当の動作を手で行わなければならないという難点があります。

### Gulp
ビルドを自動化してくれるようにするためのタスクランナーです。
[Gulp](https://gulpjs.com/)は使っていますがGruntは使ったことがないです。
タスクランナーとは、`hoge`というパッケージと一緒に`gulp-hoge`を入れなければならなくなってpackage.jsonの依存パッケージ数が2倍近くに膨れ上がるという、とても素敵なものです。

最近は何でもかんでもnpm-scriptsでやろうという話があって、確かに一理あるなあという感じなのでGulpじゃなくてもよかったかもしれません。 

## 全部突っ込む
JavaScriptから利用するためのパッケージを一つも入れていない、というかJavaScriptを書くつもりがない(gulpfile除く)のに依存パッケージ数が10を越えるのはどういうことなんだ、という感じですが、とりあえず上記のツールを全部組み合わせます。
highlight.jsとMathJaxに関しては、`<script>`タグで読み込むだけでいいので`npm install`は不要です。
Nunjucksのテンプレート継承機能を用いて、これらの読み込み用のスニペットをモジュール化しておくのがいいでしょう。

Nunjucks(+nunjucks-markdown)とPostCSSを用いて、それぞれHTMLファイルとCSSファイルを生成する処理が行われるので、これをGulpのタスクとしてまとめてしまいます。
あとはソースコードの更新を検知して自動的にこのビルド処理が走るようにしたり、ビルド完了のタイミングでBrowsersyncに更新を伝えたりする処理を[gulpfile.js](https://github.com/Tatamo/htmldtp/blob/master/gulpfile.js)に書いて終わりです。

## 使ってみた
大学でパーセプトロンの出力を求めるだけのレポート課題が出たので、構築した環境を用いてレポートを書いてみました。

以下に、Vivliostyleを用いて出力されたPDF(のスクリーンショットにモザイクかけたただの画像)を示します。

<a href="/images/post/2017/12/06/output_report.png" target="_blank"><img src="/images/post/2017/12/06/output_report.png" alt="PDFに出力されたレポート"></a>

図はLibreOffice Drawで用意しましたがそのあたりは適当です。

これに用いたCSSは、
```css
@page {
  size: A4;
  margin: 20mm;
  @bottom-center {
    content: counter(page);
  }
}

figcaption {
  counter-increment: fig;
}

figcaption::before {
  content: "図" counter(fig) ". ";
}

/* うまく動かない(後述) */
a.figref::after {
  content: "図" target-counter(attr(href), fig);
}

img {
  max-width: 100%;
}
```

だけであり、非常に簡潔なCSSで十分に見た目の整ったレポートを書くことができます。

そういえば指定するのを忘れていましたが、当然`font-size`でフォントサイズをポイント指定することも可能です。

### 相互参照と問題点
図を示している部分のHTMLを抜粋すると、以下のようになります。
```html
<section>
    {% markdown %}
    ## パーセプトロンの提示
    まず、パーセプトロンの修正後の重みを<a class="figref" href="#fig-nn"></a>に示す。
    <figure>
        <img src="perceptron.png">
        <figcaption id="fig-nn">パーセプトロン</figcaption>
    </figure>
    簡単のため、パーセプトロンのそれぞれのノードに番号を付与した。
    {% endmarkdown %}
</section>
```
`figcaption::before`の`content`に`counter(fig)`を用いることで、自動的に図表番号を振ることができます。

それに加えて、`figcaption`の持つidに対するリンクのcontentに`target-counter(attr(href), fig)`を指定しています。
これは`attr(href)`---つまりhrefの属性値、`"#fig-nn"`---の地点での`counter(fig)`のカウンタの値を取得しており、つまりその図表に割り当てられた番号が得られます。

これによって相互参照が実現できます。

と言ってドヤ顔したいところなのですが、ここで致命的な問題が発生します。
なぜかChrome拡張のVivliostyleでは、この`target-counter()`がうまく動かず、図表番号が`??`で表示されます。

他の手段であるVivliostyle FormatterやVivliostyle Viewerを使えば動かせはするのですが、その場合highlight.jsによるシンタックスハイライトが効かなかったり、MathJaxは対応しているもののMathML以外の記法を使えない、などの別の問題が生じてきます。

なので現状、`a.figref::after`に対するスタイル指定を削除し、`<a class="figref" href="#fig-nn">図1</a>`などと直接書くしかないという本末転倒感があります。

これについては今のところ解決策を見つけられていないので、もう少し調べてみて必要ならissueを飛ばすなりしようと思っています。

## おわり
レポート・論文程度ならそこまで凝ったデザインである必要がないので、非常に短いCSSでそれなりの印刷が可能になります。
HTML+CSSを用いてレポートを書くというのは十分に実用の範囲内だと思ったので、これからも試していこうと思っています。

しかしながら、やはり現状で相互参照が実現できないというのはレポート・論文執筆には致命的なところがあります。
`target-counter()`は策定中の次期CSS仕様であることから、解決は時間の問題だとは思っているのですが、なんとかしたいところです。

今の時代のCSSは、もはやWebページだけでなく紙面上のレイアウトも自在に操れるようになってきています。「[CSSではじめる同人誌制作](https://pentapod.github.io/c92/)」はCSSを使ったさまざまな紙面レイアウトについて書かれており、とてもおすすめなのでぜひ購入してみてください。

