+++
categories = [""]
date = "2017-02-11T15:13:48+09:00"
draft = false
slug = ""
tags = ["parsing", "parser generator", "parser", "lexer"]
title = "LR(1)パーサジェネレータを自作して構文解析をする 第2回:字句解析器の実装"

+++

[前回](/2016/12/22/lr-parser-generator-implementation/)は構文解析の概略を紹介したので、今回から実装に移っていきたいと思います。
まずは字句解析器を用意する必要があるため、今回は字句解析器の作成について紹介します。

なお今回から実際のプログラムを記述していきますが、使用言語はTypeScriptとします。

<!--more-->

パーサジェネレータを作るのに比べれば字句解析器を作るのは非常に単純です。
早速はじめていきましょう。

## 字句解析器の仕様を確認する
[前回](/2016/12/22/lr-parser-generator-implementation/)の記事でも紹介しましたが、字句解析器の行う処理は以下のような流れになります。

まず、解析するべき入力を文字列として受け取ります。
```
9 + 11 * (2 + 1)
```
これに加えて、字句規則を用意します。
```
数字: /[1-9][0-9]*/
プラス: "+"
アステリスク: "*"
左括弧: "("
右括弧: ")"
(読み捨て): /\s/
(不正): /./
```
字句解析器は受け取った入力を先頭から順に字句規則にあてはめ、マッチするものがあればそのトークンを割り当てます。
結果として得られる出力は、以下のようなリストになります。
```
数字: 9
プラス: +
数字: 11
アステリスク: *
左括弧: (
数字: 2
プラス: +
数字: 1
右括弧: )
```
得られたトークンのリストを構文解析器の入力として渡すことで、構文解析器は文法の解析のみに注力することができます。

## 字句規則を定義する
実際に解析を行うタイミングでは文字列のみを入力として受け取りますが、字句解析器の生成時には字句規則が必要です。
そのため、予め字句規則を別の設定ファイルなどに書いておくなどして用意しておかなければなりません。
ただし、**字句規則の解析には構文解析器が必要**となるため、現時点ではプログラム内にハードコーディングしておくなどする必要があります。
今回は、字句規則を内部的に以下のようなデータ構造で扱うこととして、しばらくは字句規則をその内部データの形式で直接書くことにします。
```TypeScript
// 定義
export type Token = string|symbol;
export interface LexDefinitionSection{
	token: Token|null;
	pattern: string|RegExp;
}
export type LexDefinitions = Array<LexDefinitionSection>;

// 実際の字句規則
const lex: LexDefinitions = [
	{token:"NUMBER", pattern:/[1-9][0-9]*/},
	{token:"PLUS", pattern:"+"},
	{token:"ASTERISK", pattern:"*"},
	{token:"LPAREN", pattern:"("},
	{token:"RPAREN", pattern:")"},
	{token:null, pattern:/\s/},
	{token:"INVALID", pattern:/./},
];
```
この字句規則の定義について、実用上の理由で追加したいくつかの仕様に注意する必要があります。

Tokenの型定義にsymbolを含めている点についてはここで説明せずに後述することとします。

### 入力の読み捨て
```TypeScript
{token:null, pattern:/\s/}
// (読み捨て): /\s/
```
この部分は、何らかの空白文字が入力に存在していればマッチングされます。
`9 + 11 * (2 + 1)` を解析する際、ここに含まれている空白は文法上何の意味も持たず、`9+11*(2+1)` のように入力が与えられたとしても解析結果は変化しません。
このような場合、構文解析器に空白の情報を与えることすらせずに、字句解析器上で空白を検知した段階でその情報を捨ててしまったほうが、構文解析器に余計な処理をさせずに済みます。

今回は、トークンのラベル部分にnullを指定することで、読み取った結果をトークンとして保持することなく読み捨てることを表すようにしています。

### 正規表現パターンと文字列パターンの使い分け
(ごちゃごちゃ書いている割に小手先のテクニックという感じが強いため、よくわからなければ読み飛ばしてください)

`{token:"PLUS", pattern:"+"}` のように、パターン部分に正規表現ではなく文字列を用いて記述している箇所があります。
すべて正規表現を使って記述するのではなく文字列も許容している理由として、まず`/\+/`のように特殊記号をエスケープせずに済む点が挙げられます。
そして、「文字列でパターンを記述した場合は、アルファベットの途中でトークンを区切らないようにする」というルールを用いることで、一部のパターンを簡潔に書くことが可能になります。

`vwxyz` という入力を考えてみましょう。
ここでもし、`"vwx"` にマッチする規則と、`"vwxyz"` にマッチする規則の2つが存在した場合、`"vwxyz"` の規則を先に書かない限り、入力`vwxyz` は`"vwx" + yz` とみなされ、`"yz"` に対応する規則が存在しなければエラーとなります。
これを回避するためには、よりマッチするパターンが長い規則を常に短い規則よりも先に書くようにする必要がありますが、面倒です。
そこで、正規表現ではなく文字列で`"vwx"` などのパターンが定義され、かつその末尾の文字が`\w` にマッチする場合、マッチした部分の一文字先の文字が`\w` 以外でなければマッチしないようにします。
これは、正規表現で`/vwx(?!\w)/` 、`/vwxyz(?!\w)/` というような否定的前方先読みをパターンの最後に追加することに相当します。
このルールを追加することで、正規表現を用いる場合よりも簡潔に記述可能となります。

## 字句解析器を実装する
ではLexerクラスを作っていきましょう。
とはいえ字句規則さえ定義してしまえば、やることはほとんどありません。
コンストラクタ引数として字句規則データを受け取って保持しておくようにして、解析実行時に上から順に字句規則のマッチングを試みるだけです。

今回はコード量が少ないので、 https://github.com/Tatamo/parsergenerator/blob/master/src/lexer.ts 全体をそのまま貼り付けます。
```TypeScript
/// token.ts
export type Token = string|symbol;
export const SYMBOL_EOF:Token = Symbol("EOF");
export const SYMBOL_SYNTAX:Token = Symbol("S'");
export const SYMBOL_DOT:Token = Symbol(".");

export type TokenList = Array<{token:Token, value:string}>;
```

```TypeScript
/// lexer.ts
/// LexDefinitionsの定義は先述のものと同一
import {Token, SYMBOL_EOF, TokenList} from "./token";
import {LexDefinitions} from "./grammar";

export interface ILexer{
	exec(str: string):TokenList;
}

export class Lexer implements ILexer{
	constructor(public def: LexDefinitions){
		// 正しいトークン定義が与えられているかチェック
		for(var i=0; i<this.def.length; i++){
			var token_pattern = this.def[i].pattern;
			if(typeof token_pattern == "string"){
				continue;
			}
			else if(token_pattern instanceof RegExp){
				// フラグを整形する
				let flags:string = "";
				// gフラグは邪魔なので取り除く
				// i,m,uフラグがあれば維持する
				if(token_pattern.ignoreCase){
					flags += "i";
				}
				if(token_pattern.multiline){
					flags += "m";
				}
				if(token_pattern.unicode){
					flags += "u";
				}
				// yフラグは必ずつける
				flags += "y";
				// フラグをつけなおして新しい正規表現オブジェクトにする
				this.def[i].pattern = new RegExp(token_pattern, flags);
				continue;
			}
			throw new Error("invalid token definition: neither string nor RegExp object");
		}
	}
	exec(str: string):TokenList{
		var result:TokenList = [];
		let lastindex = 0;
		while(lastindex < str.length){
			for(var i=0; i<this.def.length; i++){
				var token:Token|null = this.def[i].token;
				var token_pattern = this.def[i].pattern;
				var match:string;
				if(typeof token_pattern == "string"){
					let last_tmp = lastindex+token_pattern.length;
					if(str.substring(lastindex,last_tmp) != token_pattern) continue;
					if(last_tmp < str.length && /\w/.test(token_pattern.slice(-1)) && /\w/.test(str[last_tmp])) continue; // ヒットした文字の末尾が\wで、そのすぐ後ろが\wの場合はスキップ
					match = token_pattern;
					lastindex += token_pattern.length;
				}
				else{
					// token_pattern: RegExp
					token_pattern.lastIndex = lastindex;
					let m = token_pattern.exec(str);
					if(m === null) continue; // マッチ失敗
					match = m[0];
					lastindex = token_pattern.lastIndex; // lastindexを進める
				}
				// tokenがnullなら処理を飛ばします
				if(token != null){
					result.push({token:token, value:match});
				}
				break;
			}
		}
		// 最後にEOFトークンを付与
		result.push({token:SYMBOL_EOF, value:""});
		return result;
	}
}
```

まずコンストラクタでは、与えられた字句規則に簡単な型チェックと正規表現の整形を行っています。
グローバルマッチは今回は邪魔なだけなので、与えられた正規表現にgフラグがついていれば取り除きます。
ES2015でRegExpに追加されたstickyフラグ(**ほぼ**全ての主要モダンブラウザ上で実装済み)を使うと楽なので、ここでyフラグの追加も行います。

execメソッドでは入力を読み終えるまでマッチングを繰り返し、`{token:Token, value:string}` というオブジェクトを結果の配列に追加していきます。
先述のようにパターンが文字列であれば`\w`が連続した場所では区切らないようにして、マッチングが成功するたびにインデックス位置を先に進めていきます。

また、すべての入力を読み終えた後、最後にSymbol(EOF)を名前としたトークンを結果に追加します。
これは入力の末尾を意味するトークンで、構文解析の際に内部的に使用されます。

(Symbolは、それ自身と比較しない限り`==`や`===`の評価結果が常にfalseになるプリミティブ型で、ES2015で追加されたものです。
字句規則で定義されたトークンとの衝突が発生しないようにここでSymbolを使用していますが、Symbolそのものはオブジェクトのプロパティとして使用することで後方互換性を維持することを目的としてJavaScriptに追加された型であるため、この用途で用いるのに適しているのかどうかは議論の余地があります。
とはいえプログラミング言語個別の問題はこの記事の主題とは関係がないため、詳しくは言及しません。)

この字句解析器に先ほどの字句規則を与え、`9 + 11 * (2 + 1)` を入力すると、
```TypeScript
[
	{token:"NUMBER", value:"9"},
	{token:"PLUS", value:"+"},
	{token:"NUMBER", value:"11"},
	{token:"ASTERISK", value:"*"},
	{token:"LPAREN", value:"("},
	{token:"NUMBER", value:"2"},
	{token:"PLUS", value:"+"},
	{token:"NUMBER", value:"1"},
	{token:"RPAREN", value:")"},
	{token:Symbol(EOF), value:""}
]
```
という結果が得られます。
これでトークンの並びを得ることに成功したので、次回以降はいよいよパーサジェネレータの作成に移っていくことになります。


[前回:かんたん構文解析入門](/2016/12/22/lr-parser-generator-implementation/)
