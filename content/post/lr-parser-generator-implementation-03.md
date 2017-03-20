+++
categories = ["dev"]
date = "2017-03-21T00:29:18+09:00"
draft = false
slug = ""
tags = ["parsing", "parser generator", "parser", "lexer"]
title = "LR(1)パーサジェネレータを自作して構文解析をする 第3回:儀式の下準備"

+++

[前回](/2017/02/11/lr-parser-generator-implementation-02/)は字句解析器の作成を行ったので、次にLR(1)法による構文解析のためのパーサジェネレータの作成に入っていきます。
今回は、LR(1)構文解析器の構築のために必要な、終端記号と非終端記号の区別、Nulls集合、First集合の導出等を行えるようにしていきます。

<!--more-->

いよいよ構文解析部分の実装にとりかかるため、今後はLR(1)法に焦点を絞って解説していきます。

今回はその準備段階として必要になる部分を作っていくため、どんどん実装を進めていきます。

## 構文規則を定義する
まずは、前回字句規則を定義したように、構文規則を定義していく必要があります。

```TypeScript
export interface LexDefinitionSection{
	token: Token|null;
	pattern: string|RegExp;
}
export type LexDefinitions = Array<LexDefinitionSection>;

export interface SyntaxDefinitionSection{
	ltoken: Token;
	pattern: Array<Token>;
}
export type SyntaxDefinitions = Array<SyntaxDefinitionSection>;

export interface GrammarDefinition{
	lex: LexDefinitions;
	syntax: SyntaxDefinitions;
	start_symbol: Token;
}
```

字句規則と構文規則を合わせて、上記のように定義しておきましょう。

[第1回](/2016/12/22/lr-parser-generator-implementation/)で定義した構文規則をこのデータ形式に直すと、以下のようになります。

```TypeScript
const syntax:SyntaxDefinitions = [
	{
		ltoken: "EXP",
		pattern: ["EXP", "PLUS", "TERM"]
	},
	{
		ltoken: "EXP",
		pattern: ["TERM"]
	},
	{
		ltoken: "TERM",
		pattern: ["TERM", "ASTERISK", "ATOM"]
	},
	{
		ltoken: "TERM",
		pattern: ["ATOM"]
	},
	{
		ltoken: "ATOM",
		pattern:["DIGITS"]
	},
	{
		ltoken: "ATOM",
		pattern:["LPAREN", "EXP", "RPAREN"]
	}
];
const lex:LexDefinitions = [
	{token:"DIGITS", pattern:/[1-9][0-9]*/},
	{token:"PLUS", pattern:"+"},
	{token:"ASTERISK", pattern:"*"},
	{token:"LPAREN", pattern:"("},
	{token:"RPAREN", pattern:")"},
	{token:null, pattern:/\s/},
	{token:"INVALID", pattern:/./},
];
const grammar:GrammarDefinition = {
	lex: lex,
	syntax: syntax,
	start_symbol: "EXP"
};
```

ルール一行ごとに一つのオブジェクトを割り当てています。
また、`start_symbol`の定義を加えていることに注意してください。
これは文字通り開始記号のことであり、最初にどの記号から構文解析を開始するか表すために必要です。
最終的に構文解析を行った結果として構文木を得た場合、この開始記号が構文木の根となります。

## 終端記号と非終端記号を区別する
構文解析器を作るためには、与えられた構文から終端記号と非終端記号を区別できるようにする必要があります。
定義を再確認しておくと、規則の左辺に現れることのない記号が終端記号、現れる記号が非終端記号です。

実装上難しい点は特にないので、簡単に済ませてしまいましょう。

ひとまず、SymbolDiscriminatorというクラスを作って終端記号と非終端記号の問い合わせをできるようにします。
```TypeScript
/// symboldiscriminator.d.ts
export declare class SymbolDiscriminator {
    private terminal_symbols;
    private nonterminal_symbols;
    constructor(syntaxdef: SyntaxDefinitions);
    getTerminalSymbols(): Set<Token>;
    getNonterminalSymbols(): Set<Token>;
    isTerminalSymbol(symbol: Token): boolean;
    isNonterminalSymbol(symbol: Token): boolean;
}
```
実際のコードは以下を参照してください。  
https://github.com/Tatamo/parsergenerator/blob/master/src/parsergenerator/symboldiscriminator.ts  

```TypeScript
/// コンストラクタの実装のみ抜粋
constructor(syntaxdef:SyntaxDefinitions){
	this.terminal_symbols = new Set<Token>();
	this.nonterminal_symbols = new Set<Token>();

	// 左辺値の登録
	for(let sect of syntaxdef){
		let symbol = sect.ltoken;
		// 構文規則の左辺に現れる記号は非終端記号
		this.nonterminal_symbols.add(symbol);
	}
	// 右辺値の登録
	for(let sect of syntaxdef){
		for(let symbol of sect.pattern){
			if(!this.nonterminal_symbols.has(symbol)){
				// 非終端記号でない(=左辺値に現れない)場合、終端記号である
				this.terminal_symbols.add(symbol);
			}
		}
	}
}
```
2つのSetを用意し、コンストラクタの呼び出し時に左辺に現れる記号とそうでない記号で分けています。
これで、トークンを与えるとそれが終端記号かどうか、非終端記号かどうかを判別できるようになりました。

なおこの記事において、「トークン」と「記号」は概念上は同様の意味を持ちますが、前者はプログラム内で記号を表すための構造として扱い、理論的な概念について触れる際は後者を使うものとします。

## Nulls集合とFirst集合
構文解析器の作成のためには、First集合というものを求める必要があります。
そしてFisrt集合を求めるためには、Nulls集合が必要です。

First集合はFirst関数などとも呼ばれます。
まあ名前なんてどうでもいいのですが、とにかくFirstとNullsを導出しなければなりません。
順を追って見て行きましょう。
例に漏れず、[うさぎさん(@ki6o4)](https://twitter.com/ki6o4)の

* [文脈自由文法とその構文解析法 &middot; うさぎ小屋](https://kimiyuki.net/blog/2016/08/03/context-free-grammar/)

が詳しいので厳密な定義等はそちらを参照してください(First,NullableはLL(1)の項で紹介されています)。

### Nulls集合を求める
#### Nulls集合とは
ある終端記号または非終端記号について、それが「Nullableである」かどうかを判別する必要があります。
「Nullableな記号」を集めた集合をNulls集合ということにします。
記号がNullableであるとは、その記号から空列が導かれうることを意味します。

今回題材としている数式の構文規則には、「右辺が存在しない」ルールはありません。
しかし、解析したい構文によっては、右辺が存在しない、つまり左辺から空列が導かれるルールが存在することがあります。
空列とはスペース(空白)等を意味するのではなく、長さ0の入力を意味します。
具体的には、次のようなルールを見てみましょう。
```
X -> Y "0"
Y -> "1"
Y ->
```
構文規則の三行目の右辺には何も書かれていません。
このような場合、`Y`は`1`もしくは空列となり得ます。
よって`X`は、`10`と`0`の2通りが許容されるのです。
ここで、`Y`は空列となり得るため、Nulls集合に含まれます。
さらに、次のような例を見てください。
```
Z -> Y
Y -> "1"
Y ->
```
この場合、`Z`は`1`と空列の2通りとなる可能性があります。
よって、`Z`もNullableであるといえます。
このように、その記号自体が空列となるルールを指定していなくても、右辺の記号次第では空列となることがあります。
ひとつでも空列となるパターンが存在する場合、Nulls集合に含まなければなりません。
また当然ですが、左辺に現れることのない終端記号はNullableではありません。

#### Nulls集合を実装する
なにやら面倒そうですが、実装はそう複雑ではありません。
NullableSetクラスを作ってみましょう。  
https://github.com/Tatamo/parsergenerator/blob/master/src/parsergenerator/nullableset.ts
```TypeScript
/// nullableset.ts
export class NullableSet{
	private nulls:Set<Token>;
	constructor(private syntax: SyntaxDefinitions){
		this.generateNulls();
	}
	// nulls初期化
	private generateNulls(){
		// 制約条件を導出するために、
		// 空列になりうる記号の集合nullsを導出
		this.nulls = new Set<Token>();
		for(let rule of this.syntax){
			// 右辺の記号の数が0の規則を持つ記号は空列になりうる
			if(rule.pattern.length == 0){
				this.nulls.add(rule.ltoken);
			}
		}

		// 変更が起きなくなるまでループする
		let flg_changed:boolean = true;
		while(flg_changed){
			flg_changed = false;
			for(let rule of this.syntax){
				// 既にnullsに含まれていればスキップ
				if(this.isNullable(rule.ltoken)) continue;

				let flg_nulls = true;
				// 右辺に含まれる記号がすべてnullableの場合はその左辺はnullable
				for(let token of rule.pattern){
					if(!this.isNullable(token)){
						// 一つでもnullableでない記号があるならnon-nullable
						flg_nulls = false;
						break;
					}
				}
				if(flg_nulls){
					flg_changed = true;
					this.nulls.add(rule.ltoken);
				}
			}
		}
	}
	public isNullable(x:Token){
		return this.nulls.has(x);
	}
}
```
アルゴリズムは以下の通りです。  
まず、右辺の記号の数が0になるような規則を持っている記号は明らかにNullableです。
そのため、初期化段階として、そのような規則を探してNulls集合に追加します。  
次に、それ以外の記号がNullableであるためには、規則の右辺がすべてNullableな記号である必要があります。
そこで、すべての規則を調べ、その右辺の記号がすべて既存のNull集合に含まれているならば、その記号もNulls集合に追加します。  
この処理を、一巡しても新しいNullableな規則が追加されなくなるまで繰り返せば終了です。

### First集合を求める
#### First集合とは
たとえば先ほど挙げた`X`が`10`や`0`となるように、非終端記号は規則をたどっていくと終端記号のみの列に変換することができます。
構文解析を行うためには、ある非終端記号から得られるそのような終端記号の列のうち、最も左側にどのような終端記号が来るのかを知る必要があります。
つまり(1)先読みを行うわけです([第1回](/2016/12/22/lr-parser-generator-implementation/)参照)。

このように、ある非終端記号と、そこから得られる可能性のある終端記号の列の先頭に来る記号の集合を対応付けたものを、First集合またはFirst関数と呼びます。
たとえば、先ほどの`X`を例にすると、`First(X)`は、`10`と`0`のそれぞれ左端の記号をとって`{1, 0}`となります。

Aが終端記号であるなら、`First(A)`は`{A}`(A自身のみを要素とする集合)です。
また、Firstに与える引数は記号だけでなく記号列である可能性もあります。
`First(YA)`なら、`Y`がNullableであるため、`YA`から得られうる文字列の左端になりうるのは`1`と`A`なので、`{1, A}`のようになるでしょう。

#### First集合の実装
話がごちゃごちゃしてきましたが、実装に移りましょう。
基本的なアルゴリズムは、以下のようになります。

* ある記号`A`が終端記号なら、`First(A)`は`{A}`である。全ての終端記号についてそのように初期化する。
* 非終端記号に対応するFirst集合は、まず空集合で初期化する。
* ルール `X -> Y1 Y2 ... Yi` について、以下の制約を生成する。
	* `First(X) ⊇ First(Y1)`
	* `Y1`がNullableなら `First(X) ⊇ First(Y2)`
	* `Y2`がNullableなら `First(X) ⊇ First(Y3)`
	* 以下繰り返し
* 生成した制約に従い、スーパーセット側にサブセット側の集合の持つ記号を追加していく(制約の解消)。
* 制約の解消を全ての集合に変化がなくなるまで繰り返す。

わかりにくいですね。
ちなみにこのアルゴリズムは、言葉で定義するよりもプログラムを書いたほうがわかりやすい類のものです。

とはいえちょっとコードが長いので、URLから参照をお願いします。
https://github.com/Tatamo/parsergenerator/blob/master/src/parsergenerator/firstset.ts

```TypeScript
/// firstset.d.ts
export declare class FirstSet {
    private syntax;
    private symbols;
    private first_map;
    private nulls;
    constructor(syntax: SyntaxDefinitions, symbols: SymbolDiscriminator);
    private generateFirst();
    get(arg: Token): Set<Token>;
    get(arg: Array<Token>): Set<Token>;
}
```

実のところNulls集合はFirst集合を求める時にしか使わないので、First関数のprivateメンバとしてNullableSetインスタンスを生成して使用します。
SymbolDiscriminatorは他でも使いまわす必要があるので、コンストラクタ引数による依存性の注入を行います。

また、First関数の引数には単一の記号だけでなく、記号列も与えられるようにする必要があります。
getメソッドではトークンを引数にとるだけでなく、トークンの配列も引数として与えることができるようにします。
トークンの配列が与えられた場合は、左から順に個別のトークンのFirst関数を呼び、そのトークンがNullableである限り、その右隣のFirst関数も呼び出し、その結果として得られた記号全てを要素とする集合を返すものとします。

説明が適当かつわかりにくくて申し訳ありませんが、どうせ準備段階なので軽く飛ばして先に進めていきましょう([第1回](/2016/12/22/lr-parser-generator-implementation/)に参考資料をまとめてあるため、詳細かつ厳密に知りたい方はそちらを参照してください)。

これでひとまずFirst集合の導出まで終わったので、次回からは構文解析表の作成にとりかかります。


[第1回:かんたん構文解析入門](/2016/12/22/lr-parser-generator-implementation/)  
[前回:字句解析器の実装](/2017/02/11/lr-parser-generator-implementation-02/)
