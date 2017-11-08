+++
categories = ["dev"]
date = "2017-11-08T18:00:00+09:00"
draft = false
slug = ""
tags = ["highlightjs", "javascript", "blog"]
title = "ブログのシンタックスハイライトを改善しました"

+++

このブログのシンタックスハイライトには[highlight.js](https://highlightjs.org/)を使用していますが、使い勝手を上げるために調整を行いました。

<!--more-->

## コードの強調は不要だがhighlight.jsのテーマは使いたい

プログラムの実行結果など、プログラムそのもの以外を<code class="html">&lt;code&gt;</code>タグで囲いたくなる場合が何度かありました。
class要素に`nohighlight`を指定すればハイライトは行われなくなりますが、highlight.jsのCSSテーマも適用されなくなります。

さらに、インライン要素のハイライトを行う場合、対象となるコードが短いために言語の自動検出がうまく働かないことが多いです。
またブロック要素ではMarkdownでcode部分を囲う際に
````nohighlight
```javascript
/* code */
```
````
とすることで言語注釈をつけることができますが、インラインの場合はこれができないため言語を明示的に指定する場合には直接codeタグを書く必要があります。

## 解決策
今のところ<code class="html">&lt;code&gt;</code>で囲った部分はすべてhighlight.jsのテーマを適用してしまって問題はないので、`nohighlight`を指定した場合でもCSSテーマが適用されるようにします。
また、インライン要素の場合はデフォルトで`nohighlight`にしてしまって、必要がある場合のみHTMLタグを記述して明示的に言語を指定することとしました。
[highlight.jsをインライン要素にも適用できるようにした](/2015/12/12/apply-highlightjs-to-inline/) で書いたコードを、さらに手直しします。

```javascript
// hljs.initHighlightingOnLoad();
window.addEventListener("DOMContentLoaded", () => {
	const elements = document.getElementsByTagName("code");
	for (const el of elements) {
		if (el.parentNode.tagName != "PRE") {
			// インライン要素
			el.style.display = "inline";
			el.style.margin = "0 2px";
			el.style.padding = "1px 3px";
			if (el.classList.length == 0) {
				// classが指定されていない場合はnohighlightとする
				el.classList.add("nohighlight");
			}
		}
		hljs.highlightBlock(el);
		// nohighlightでもテーマは適用する
		if (el.classList.contains("nohighlight") ||
			el.classList.contains("lang-nohighlight") ||
			el.classList.contains("language-nohighlight")) {
			el.classList.add("hljs");
		}
	}
}, false);
```
ついでにES6で書きました。

まず、<code class="html">&lt;code&gt;</code>タグでclassの指定が明示的に行われていない場合は自動的に`nohighlight`を追加しています。
この処理はhighlight.jsでのハイライト化の処理を行う前に持ってきました。
次に、`nohighlight`が指定されていてハイライトが行われていない要素に対して、classにhighlight.jsで使用されているクラスである`hljs`を指定することでテーマの適用を行っています。

あとは既存の記事をこの変更に合わせて修正しました。
ところどころ不自然なハイライトが行われていた箇所がありましたが、これで改善されました。
