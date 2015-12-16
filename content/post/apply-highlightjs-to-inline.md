+++
categories = ["tech"]
date = "2015-12-12T01:01:51+09:00"
draft = false
slug = ""
tags = ["highlight_js", "javascript", "blog"]
title = "highlight.jsをインライン要素にも適用できるようにした"

+++

このブログのシンタックスハイライトには[highlight.js](https://highlightjs.org/)を使用しています。

デフォルトでは適用できないインラインのコードのハイライトも行えるようにしましたので、適用の流れなど。

<!--more-->

## highlight.jsって何

JavaScriptによって、Webページ内のソースコードを色付けしてくれるものです。
たとえば:

```python
for i in range(1,101) :
	print ("Fizz" if not i%3 else "") + ("Buzz" if not i%5 else "") or i
```

[highlight.js demo](https://highlightjs.org/static/demo/)を見れば、どのようにハイライトされるのか、どのようなテーマがあるのかが簡単にわかるので良いと思います。
(しかし思うのですが、自分のサイトに導入を検討する際に、この濃い赤色の背景色だと白色の背景と感覚が違ってくるのでやや選びにくいのではないでしょうか……。
コンソールから`document.body.style.backgroundColor="#FFFFFF";`とでも入力して、背景を白く変更してから見てみるのがいいかもしれません。)


## インライン要素でもハイライトできるようにしてみた
通常では、highlight.jsによるハイライトは`<pre><code>`から`</code></pre>`で囲まれたブロック要素にのみ適用され、インライン要素に適用することはできません。1行の中にコードを埋め込みたいことも多いので、これではやや不便です。

Markdownの展開方法に適用できるように、`<code></code>`で囲まれた要素をハイライトすることにします。
既に先人たちが記事を書いてくれているので、基本的にはそれを参考にするだけでした。

* [highlight.jsでインラインのソースコードもハイライトする | blog@kenzauros.com](http://kenzauros.com/blog/apply-highlight-js-to-inline-source-code/)
* [軽いとうわさのhighlight.jsをTwenty Fifteenに導入、ソースコードを綺麗に表示 &#8211; SWIFTRITHM](http://swiftrithm.com/blog/highlightjs-install/)

上記のサイトでは共にJQueryを用いていますが、このサイトでは特にJQueryを導入する気もなかったため、生のJavaScript+DOMで適当に書き直しました。

```html
<script>
// hljs.initHighlightingOnLoad();
window.addEventListener("DOMContentLoaded", function(){
	var elements = document.getElementsByTagName("code");
	var l=elements.length;
	for(var i=0; i<l;i++){
		var el = elements[i];
		hljs.highlightBlock(el);
		if(el.parentNode.tagName.toLowerCase() != "pre") {
			el.style.display = "inline";
			el.style.margin = "0 2px";
			el.style.padding = "1px 3px";
		}
	}
}, false);
</script>
```
通常`hljs.initHighlightingOnLoad();`を呼ぶところを、上記のコードで置き換えるだけで完了です。
親要素が`<pre>`ではない場合にインライン化するようにしています。
またスタイルの指定も同時に行っているので、別途CSSを記述する必要はありません。

既に記事内で何回も使用しているので今更感はありますが、実際にインラインのコードがハイライトされるか見てみましょう。
たとえば、このように:`print "\n".join(["Fizz"*(not i%3)+"Buzz"*(not i%5) or str(i) for i in range(1,101)])`1行のコードでもうまくハイライトされているのがわかります。
やったね。

