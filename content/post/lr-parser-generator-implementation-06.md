+++
categories = ["dev"]
date = "2017-04-04T23:41:09+09:00"
draft = false
slug = ""
tags = ["parsing", "parser generator", "parser", "lexer"]
title = "LR(1)パーサジェネレータを自作して構文解析をする 第6回:パーサの実装と構文解析の実行"

+++


[前回](/2017/04/04/lr-parser-generator-implementation-05/)ではLR(1)法による構文解析表の実装を済ませました。
今回はいよいよ、構文解析表をもとに実際に構文解析を行うことのできる構文解析器を実装し、実際に構文解析を行ってみます。

<!--more-->

概略は[第4回](/2017/03/22/lr-parser-generator-implementation-04/)で示した通りであり、ステートマシンの仕様に沿って構文解析表を解析できるようにします。
第4回でアルゴリズム面まで踏み込んで解説してしまったため、あまり書くことがなくてやや後悔しています。

## ASTの定義
パーサを実装する前に、パーサの出力結果となる抽象構文木の定義を行います。    
https://github.com/Tatamo/parsergenerator/blob/master/src/parser/ast.ts
```ts
/// ast.ts
export interface ASTNode{
	type: Token;
	value: string|null;
	children: Array<ASTNode>;
}
```
構文木は再帰的な木構造によって表されます。
それぞれのノードにはそれが何の記号かを示すトークン、およびそのトークンに紐つけられている実際の入力文字列(そのトークンが非終端記号である場合はnull)、自身の子となるノードの配列を持ちます。

たとえば、[第3回](/2017/03/21/lr-parser-generator-implementation-03/)で定義した構文規則によって`1+1`を解析した場合、結果として得られる抽象構文木は以下のようになるでしょう。
```json
{"type":"EXP", "value":null, "children":[
    {"type":"EXP", "value":null, "children":[
        {"type":"TERM", "value":null, "children":[
            {"type":"ATOM", "value":null, "children":[
                {"type":"DIGITS", "value":"1", "children":[]}
            ]}
        ]}
    ]}, 
    {"type":"PLUS", "value":"+", "children":[]}, 
    {"type":"TERM", "value":null, "children":[
        {"type":"ATOM", "value":null, "children":[
            {"type":"DIGITS", "value":"1", "children":[]}
        ]}
    ]}
]}

```
簡単ですね。

## パーサの実装
ではParserクラスを作っていきます。    
https://github.com/Tatamo/parsergenerator/blob/master/src/parser/parser.ts    
```ts
/// parser.ts
export interface TerminalCallbackArg {
        token: string;
        value: string;
        terminal: true;
}
export interface NonterminalCallbackArg {
        token: string;
        children: Array<any>;
        pattern: Array<string>;
        terminal: false;
}
export declare type ParserCallbackArg = TerminalCallbackArg | NonterminalCallbackArg;
export declare type ParserCallback = (arg: ParserCallbackArg) => any;
export declare class Parser {
        private lexer;
        private syntax;
        private parsingtable;
        private default_callback;
        constructor(lexer: ILexer, syntax: SyntaxDefinitions, parsingtable: ParsingTable, default_callback?: ParserCallback | null);
        setDefaultCallback(default_callback?: ParserCallback | null): void;
        parse(input: string, cb?: ParserCallback): any;
        private _parse(inputs, cb?);
}
```
できました。

構文解析処理を行う処理は、110行程度に及ぶ[Parser#_parse()メソッド](https://github.com/Tatamo/parsergenerator/blob/master/src/parser/parser.ts#L40)の内部で完結しています。
これは、[第4回](/2017/03/22/lr-parser-generator-implementation-04/)に示したオートマトンの動作を仕様通りに実装しています。
入力を前から順に読み込み、現在スタックに積まれている状態番号を取得し、構文解析表の該当する位置に書かれている命令を実行していくだけです。

ここでは、構文解析の実行中に衝突した命令が存在した場合、その時点で解析失敗としてエラーを返すようにしています。
コンフリクトが発生した際の対処についても[第4回](/2017/03/22/lr-parser-generator-implementation-04/)で触れていますが、一般に使用されているパーサジェネレータでは、構文規則を定義するファイルの中に演算子の優先度や右結合/左結合の指定を行うことができたり、指定のない場合はshift/reduceのいずれかを優先するようになっていたりします。

このあたりのコンフリクト回避処理や、エラーが発生した際の詳細なエラー情報の検知・回避などの機能面には大きく改善の余地があります。

あとは、このParserをParserGeneratorから利用できるようにするため、適当にファクトリクラスを作ります。
https://github.com/Tatamo/parsergenerator/blob/master/src/parser/factory.ts
```ts
/// factory.ts
export class ParserFactory{
	public static create(grammar: GrammarDefinition, parsing_table: ParsingTable, default_fallback?: ParserCallback):Parser{
		let lexer = new Lexer(grammar.lex);
		return new Parser(lexer, grammar.syntax, parsing_table, default_fallback);
	}
}
```

[ParserGenerator#getParser()メソッド](https://github.com/Tatamo/parsergenerator/blob/master/src/parsergenerator/parsergenerator.ts#L32)には、以下のような記述を行います。
```ts
/// parsergenerator.ts
public getParser(default_callback?: ParserCallback):Parser{
	return ParserFactory.create(this.grammar, this.parsing_table, default_callback);
}
```

これで完成です。

## 構文解析を実行する
### 構文木を生成する
では、実際に構文解析を実行してみましょう。
与える構文は、以下のようになります。
```ts
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
	{token:null, pattern:/(\r\n|\r|\n)+/},
	{token:null, pattern:/[ \f\t\v\u00a0\u1680\u180e\u2000-\u200a\u202f\u205f\u3000\ufeff]+/},
	{token:"INVALID", pattern:/./},
];

const grammar:GrammarDefinition = {
	lex: lex,
	syntax: syntax,
	start_symbol: "EXP"
};
```
`9 + 11 * (2 + 1)`という式を解析するためには、以下のように実行します。
```ts
new ParserGenerator(grammar).getParser().parse("9 + 11 * (2 + 1)");
```
すると、結果として以下のようなオブジェクトが得られます。
```json
{"type":"EXP","value":null,"children":[
    {"type":"EXP","value":null,"children":[
    {"type":"TERM","value":null,"children":[
        {"type":"ATOM","value":null,"children":[
                {"type":"DIGITS","value":"9","children":[]}
            ]}
        ]}
    ]},
    {"type":"PLUS","value":"+","children":[]},
    {"type":"TERM","value":null,"children":[
        {"type":"TERM","value":null,"children":[
            {"type":"ATOM","value":null,"children":[
                {"type":"DIGITS","value":"11","children":[]}
            ]}
        ]},
        {"type":"ASTERISK","value":"*","children":[]},
        {"type":"ATOM","value":null,"children":[
            {"type":"LPAREN","value":"(","children":[]},
            {"type":"EXP","value":null,"children":[
                {"type":"EXP","value":null,"children":[
                    {"type":"TERM","value":null,"children":[
                        {"type":"ATOM","value":null,"children":[
                            {"type":"DIGITS","value":"2","children":[]}
                        ]}
                    ]}
                ]},
                {"type":"PLUS","value":"+","children":[]},
                {"type":"TERM","value":null,"children":[
                    {"type":"ATOM","value":null,"children":[
                        {"type":"DIGITS","value":"1","children":[]}
                    ]}
                ]}
            ]},
            {"type":"RPAREN","value":")","children":[]}
        ]}
    ]}
]}
```
どこからどう見ても成功ですね。
おめでとうございます。

### コールバックを利用して構文木のさらなる解析を行う
ついに、本記事の目標である構文木の取得を達成しました。
あとはこれに適当な再帰的処理を挟めば、良い感じに数式処理ができるでしょう。

確かに一度構文木を生成してからそれを解析してもよいのですが、パーサに適当なコールバックを渡し、構文木を生成する代わりに数式処理を行ってしまうという手もあります。

先ほどのparsergenerator.tsに、以下のような定義が含まれていました。
```ts
export interface TerminalCallbackArg {
        token: string;
        value: string;
        terminal: true;
}
export interface NonterminalCallbackArg {
        token: string;
        children: Array<any>;
        pattern: Array<string>;
        terminal: false;
}
export declare type ParserCallbackArg = TerminalCallbackArg | NonterminalCallbackArg;
export declare type ParserCallback = (arg: ParserCallbackArg) => any;
```
Parserのコンストラクタ引数、ParserGeneratorのgetParserメソッドの引数、またはParserのparseメソッドの引数としてコールバックを与えると、reduce処理が行われた際にそれを呼び出すことができます。
処理するべきトークンが終端器号であった場合はそのトークンの種類と実際の入力が、非終端記号であった場合は対応する規則の情報、およびその子ノードの情報が引数として与えられます。

実際に、数式の処理を行うための関数を書いてみましょう。
```ts
let solve_terminal = (arg:TerminalCallbackArg)=>{
	switch(arg.token){
		case "DIGITS":
			return +arg.value;
		default:
			return null;
	}
}
let solve_nonterminal = (arg:NonterminalCallbackArg)=>{
	switch(arg.token){
		case "EXP":
			if(arg.children.length == 1) return arg.children[0];
			else return arg.children[0] + arg.children[2];
		case "TERM":
			if(arg.children.length == 1) return arg.children[0];
			else return arg.children[0] * arg.children[2];
		case "ATOM":
			if(arg.children.length == 1) return arg.children[0];
			else return arg.children[1];
	}
}
let solve = (arg:ParserCallbackArg)=>{
	if(arg.terminal) return solve_terminal(arg);
	else return solve_nonterminal(arg);
}
```
これをもとに、以下のように実行します。
```ts
new ParserGenerator(grammar).getParser(solve).parse("9 + 11 * (2 + 1)");
```
実行結果は、以下のようになります。
```shell
42
```

これにて、パーサジェネレータの実装および構文解析が完了しました。
ここまでお付き合いいただき、ありがとうございました。

[第1回:かんたん構文解析入門](/2016/12/22/lr-parser-generator-implementation/)  
[前回:LR(1)パーサジェネレータの実装](/2017/04/04/lr-parser-generator-implementation-05/)
