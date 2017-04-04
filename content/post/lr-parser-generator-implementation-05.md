+++
categories = ["dev"]
date = "2017-04-04T21:54:01+09:00"
draft = false
slug = ""
tags = ["parsing", "parser generator", "parser", "lexer"]
title = "LR(1)パーサジェネレータを自作して構文解析をする 第5回:LR(1)パーサジェネレータの実装"

+++

[前回](/2017/03/22/lr-parser-generator-implementation-04/)の記事ではLR(1)法の解説を行いました。
今回は前回解説した部分の実装を行います。

<!--more-->

今回はひたすらパーサジェネレータの実装をしていきます。
概略やアルゴリズムは既に紹介しているため、基本的に省略します。
適宜[前回](/2017/03/22/lr-parser-generator-implementation-04/)の記事を参照してください。

今回取り上げる部分は特にパフォーマンスに与える影響が大きいため、処理速度を重視した設計を行っています。
そのため、天下り的な解説となる箇所が増えるかもしれませんがご了承ください。

## SyntaxDBクラスの作成
[第3回](/2017/03/21/lr-parser-generator-implementation-03/)で終端記号と非終端記号の区別を行ったり、First集合を求めたりしたように、与えられた構文から様々な情報を得る必要があります。

そこで、それらの情報取得を担うクラスとしてSyntaxDBクラスを作成します。  
https://github.com/Tatamo/parsergenerator/blob/master/src/parsergenerator/syntaxdb.ts

```TypeScript
/// syntaxdb.d.ts
export declare class SyntaxDB {
    private syntax;
    private _start_symbol;
    private _first;
    private _symbols;
    private tokenmap;
    private tokenid_counter;
    private defmap;
    constructor(grammar: GrammarDefinition);
    private initDefMap();
    readonly start_symbol: Token;
    readonly first: FirstSet;
    readonly symbols: SymbolDiscriminator;
    findDef(x: Token): Array<{
        id: number;
        def: SyntaxDefinitionSection;
    }>;
    getTokenId(token: Token): number;
    get(id: number): SyntaxDefinitionSection;
}
```
SyntaxDBクラスの読み取り専用メンバとして第3回で作成したFirstSetとSymbolDiscriminatorを持たせており、このクラスから終端記号の区別、First集合の取得が可能です。
また、開始記号の取得も行えるようにします。
さらに、構文id(上から何番目かを表す番号)を与えるとそれに対応する規則を返す`get`メソッド、記号を与えるとそれが左辺に現れる規則すべてを、構文idとともに返す`findDef`メソッド、そして記号を与えるとそれに対応した一意なトークンidを返す`getTokenId`メソッドを持ちます。

findDefメソッドは愚直に実装した場合、複数回呼ばれるとそのたびに`O(n)`程度の計算量がかかります。
そのため、SyntaxDBインスタンスの初期化時に非終端記号と規則群を対応付けたMapを生成する(計算量は`O(n)`程度)ことで、findDefの呼び出し時にはMapの中身をそのまま返すだけで済むようにします。

getTokenIdメソッドは後述するアイテム集合部分で使用する必要がありますが、特に厳密なシリアライズや復元可能性を求めているわけではなく、単にハッシュ代わりに使用されます。
そのため、同一のSyntaxDBインスタンスのgetTokenIdメソッドにトークンを与えると、同一のトークンならば必ず同じ番号が返ってくるようになっていればそれで構いません。
よって、以下のような単純な実装で事足ります。
```TypeScript
// Tokenを与えると一意なidを返す
public getTokenId(token: Token): number{
	if(!this.tokenmap.has(token)) this.tokenmap.set(token, this.tokenid_counter++);
	return this.tokenmap.get(token)!; /// ※ !はTypeScriptのnon-nullable注釈
}
```

このSyntaxDBクラスは、パーサジェネレータ全体を通して一つのインスタンスのみを使いまわすようにします。


## アイテム集合とクロージャー展開
まずは、LRアイテムおよびその集合を表現するクラスを作っていきます。
アイテム集合クラスには、自身をクロージャー展開する機能を持たせることとします。

また、DFAの構築において、「既存のアイテム集合の中に、新しく生成されたアイテム集合と一致するものが存在するかどうか判定する」処理が必要とされます。
愚直に実装すると、この処理を1回行うだけで`O(n^3)`程度の計算量が必要となり、パーサジェネレータの処理全体の中で最大のボトルネックとなりえます。
そのため、2つのアイテム集合の高速な一致判定を行えるようにする必要があります。

https://github.com/Tatamo/parsergenerator/blob/master/src/parsergenerator/closure.ts

```TypeScript
/// closure.d.ts
export declare class ClosureItem {
    private syntax;
    private _syntax_id;
    private _dot_index;
    private _lookaheads;
    private _lr0_hash;
    private _lr1_hash;
    constructor(syntax: SyntaxDB, _syntax_id: number, _dot_index: number, _lookaheads: Array<Token>);
    readonly syntax_id: number;
    readonly dot_index: number;
    readonly lookaheads: Array<Token>;
    private sortLA();
    private updateHash();
    getLR0Hash(): string;
    getLR1Hash(): string;
    isSameLR0(c: ClosureItem): boolean;
    isSameLR1(c: ClosureItem): boolean;
    merge(c: ClosureItem): ClosureItem | null;
}
export declare class ClosureSet {
    private syntax;
    private closureset;
    private _lr0_hash;
    private _lr1_hash;
    constructor(syntax: SyntaxDB, closureset: Array<ClosureItem>);
    private sort();
    readonly size: number;
    getArray(): ClosureItem[];
    includes(item: ClosureItem): boolean;
    isSameLR0(c: ClosureSet): boolean;
    isSameLR1(c: ClosureSet): boolean;
    private updateHash();
    getLR0Hash(): string;
    getLR1Hash(): string;
    mergeLA(cs: ClosureSet): ClosureSet | null;
    private expandClosure();
}
```

ClosureItemクラスはLRアイテム1つに対応し、構文id、`.`の位置、および先読み記号の情報を持ちます。
ClosureSetクラスは複数のClosureItemをまとめた集合を表し、自身をクロージャー展開することが出来ます。
さらに、後述するハッシュに関連した機能、および先読み部分のマージ機能などを持ちます。

### クロージャー展開

```TypeScript
// クロージャー展開を行う
// TODO: リファクタリング
private expandClosure(){
	// 展開処理中はClosureItemのlookaheadsの要素数を常に1に保つこととする
	// 初期化
	let set:Array<ClosureItem> = [];
	// ClosureItemをlookaheadsごとに分解する
	for(let ci of this.closureset){
		for(let la of ci.lookaheads){
			set.push(new ClosureItem(this.syntax, ci.syntax_id, ci.dot_index, [la]));
		}
	}
	this.closureset = set;
	this.sort();

	// 変更がなくなるまで繰り返す
	let i=0;
	while(i<this.closureset.length){
		let ci = this.closureset[i++];
		let {ltoken, pattern} = this.syntax.get(ci.syntax_id);

		if(ci.dot_index == pattern.length) continue; // .が末尾にある場合はスキップ
		let follow = pattern[ci.dot_index];
		if(!this.syntax.symbols.isNonterminalSymbol(follow)) continue; // .の次の記号が非終端記号でないならばスキップ

		// クロージャー展開を行う

		// 先読み記号を導出
		// ci.lookaheadsは要素数1のため、0番目のインデックスのみを参照すればよい
		let lookaheads = [...this.syntax.first.get(pattern.slice(ci.dot_index+1).concat(ci.lookaheads[0])).values()];
		lookaheads.sort((t1:Token, t2:Token)=>{
			return this.syntax.getTokenId(t1) - this.syntax.getTokenId(t2);
		});

		// symbolを左辺にもつ全ての規則を、先読み記号を付与して追加
		let definitions = this.syntax.findDef(follow);
		for(let {id, def} of definitions){
			for(let la of lookaheads){
				let new_ci = new ClosureItem(this.syntax, id, 0, [la]);
				// 重複がなければ新しいアイテムを追加する
				let flg_duplicated = false;
				for(let existing_item of this.closureset){
					if(new_ci.isSameLR1(existing_item)){
						flg_duplicated = true;
						break;
					}
				}
				if(!flg_duplicated){
					this.closureset.push(new_ci);
				}
			}
		}
	}
	this.sort();

	// ClosureItemの先読み部分をマージする
	let tmp = this.closureset;
	this.closureset = [];
	let lookaheads = [];
	for(let i=0; i<tmp.length; i++){
		lookaheads.push(tmp[i].lookaheads[0]);
		if(i == tmp.length-1 || !tmp[i].isSameLR0(tmp[i+1])){
			this.closureset.push(new ClosureItem(this.syntax, tmp[i].syntax_id, tmp[i].dot_index, lookaheads));
			lookaheads = [];
		}
	}
}
```
まだリファクタリングしていません。
すみません。

ClosureItemは本来複数の先読み記号を保持することができるのですが、クロージャー展開の初期化段階では、まずそれを分解し、一つのClosureItemが必ず一つの先読み記号のみを持つようにします。

この制約がなければ、たとえば`[x,y]`という先読み記号を持ったAというClosureItemの展開処理が済んだあと、BというClosureItemを展開したところ、`[y,z]`という先読み記号を持ち、先読み部分以外はAと全く同じClosureItemが生成される、といった自体が発生する可能性があります。
この場合、先読み記号`y`については2度展開されてしまったり、それを防ぐために処理済みのClosureItemの走査を行うことで余計な計算が必要になってしまうなど、様々な弊害が発生します。
クロージャー展開の処理中だけはClosureItemの先読み記号は常に一つであるという制約を設けてることで、展開処理が非常に簡潔になります。

処理が終わった段階で、ClosureItemの先読み部分をマージすることによってクロージャー展開後のClosureSetが完成します。

### ハッシュ
先述したように、2つのClosureSetの高速な比較を可能とする必要があります。
そこで、ClosureItemおよびClosureSetにハッシュ文字列の生成機能を追加し、構文番号、`.`の位置、先読み記号という要素が一致していれば同じ文字列を生成するようにすれば、同一性の比較を文字列比較に落としこむことができ、非常に高速になります。
ハッシュといっても、SHA-1やMD5のようなビット列を出力する必要はなく、要素が同じなら同じ結果が得られ、かつ衝突が発生しなければそれでよいので、単純に要素を文字列化してしまえば事足ります。

たとえば、構文番号1、`.`の位置が左から2番目、先読み記号が`[$,x]`のClosureItemなら、
```JSON
"1,2,[$,x]"
```
というような文字列を出力すれば目的は果たせます。
ClosureItemの集合であるClosureSetの場合は、まず自身の持っているClosureItemを、このハッシュ文字列を使ってソートした上で、以下のような文字列を生成します。
```JSON
"1,2,[$,x]|2,3,[$]"
```
ClosureItemが常にソートされていると仮定したならば、こちらも同一性の判定に用いることができます。

ただしこの場合、ひとつだけ問題が生じます。
もし上記の`x`のかわりに、`x]|2,2,[y`という名前の終端記号があったとすると、さきほどのClosureSetのハッシュ文字列は
```JSON
"1,2,[$,x]|2,2,[y]|2,3,[$]"
```
となってしまい、`2,2,[y]`というClosureItemが存在した時に衝突が発生してしまう可能性があります。
これを防ぐため、先ほどのSyntaxDBを用いて、それぞれの終端記号を個別の数値に直してしまいます。
```JSON
"1,2,[0,1]"
"1,2,[0,1]|2,3,[1]"
```
のようにすることで、危険性は回避できます。

また、LALR(1)法のDFAマージの際に、先読み記号を除いた部分が一致しているかどうか調べる必要もあるため、ハッシュ文字列は先読み部分を除いたLR(0)ハッシュと、先読み部分も含めたLR(1)ハッシュの2種類を用意しておきます。

#### ClosureItemおよびClosureSetのconst性
ハッシュの再生成や、それに伴うClosureSetのソート処理を回避するため、ClosureItemおよびClosureSetは、初期化終了後は自身の状態を変化させることがないようにします。

たとえば、クロージャー展開を行うとClosureSetの中身は変化しますが、この処理はコンストラクタ呼び出し時に行うようにすることで、オブジェクトの初期化が終了した時点でクロージャー展開は終わっており、それ以降ClosureSetの状態が変化することはありません。
また、ClosureSetおよびClosureItemは、LR(0)部分(先読み部分を除いた部分)が同じなら先読み記号をマージするmergeメソッドを持っていますが、このメソッドもそのオブジェクト自身の状態を変更するのではなく、新しいオブジェクトを生成してそれを返すようにしています。

## DFAの構築
アイテム集合を扱えるようになったため、次はDFAの構築です。
DFAGeneratorクラスを作っていきます。  
https://github.com/Tatamo/parsergenerator/blob/master/src/parsergenerator/dfagenerator.ts

```TypeScript
/// dfagenerator.d.ts
export declare type DFAEdge = Map<Token, number>;
export declare type DFANode = {
    closure: ClosureSet;
    edge: DFAEdge;
};
export declare type DFA = Array<DFANode>;
export declare class DFAGenerator {
    private syntax;
    private lr_dfa;
    private lalr_dfa;
    constructor(syntax: SyntaxDB);
    getLR1DFA(): DFA;
    getLALR1DFA(): DFA;
    private generateNewClosureSets(closureset);
    private indexOfDuplicatedNode(dfa, new_node);
    private generateDFA();
    private mergeLA(dfa);
}
```
とはいえ、やるべきことはDFAの構築処理、およびLALR(1)法のDFAを生成するためのマージ処理のみです。

DFAは、DFANodeの配列として表現します。
DFANodeは、一つのClosureSetとDFAEdgeを持ちます。
DFAEdgeはトークンをキーとしてDFANodeのインデックスを持つMapであり、これはトークンをラベルとして他のDFANodeに向けて張られた辺の情報を意味します。

前回紹介したアルゴリズムをそのままコードにするだけなので、DFAの構築処理は特に解説する点はありません。

## 構文解析表の構築
構文解析表と、それに必要な四種類の命令群を定義しておきます。
また、コンフリクトが発生したことを表す命令も定義します。
``` TypeScript
/// parsingtable.ts
export type ShiftOperation = {type: "shift", to: number};
export type ReduceOperation = {type: "reduce", syntax: number};
export type ConflictedOperation = {type: "conflict", shift_to:Array<number>, reduce_syntax:Array<number>};
export type AcceptOperation = {type: "accept"};
export type GotoOperation = {type : "goto", to: number};
export type ParsingOperation = ShiftOperation|ReduceOperation|ConflictedOperation|AcceptOperation|GotoOperation;
export type ParsingTable = Array<Map<Token, ParsingOperation>>;
```

構文解析表の構築処理は、ParserGeneratorクラスのメソッドとして実装します。  
https://github.com/Tatamo/parsergenerator/blob/master/src/parsergenerator/parsergenerator.ts

```TypeScript
/// parsergenerator.d.ts
export declare class ParserGenerator {
    private grammar;
    private parsing_table;
    private syntax;
    private dfa_generator;
    constructor(grammar: GrammarDefinition);
    init(): void;
    getParser(default_callback?: ParserCallback): Parser;
    getParsingTable(): ParsingTable;
    private generateParsingTable(dfa);
}
```
これも前回紹介したアルゴリズムの通り、DFAを読み取って適切な命令を配置していくだけです。
ParserGeneratorクラスは、初期化時にSyntaxDBおよびDFAGeneratorのインスタンスを生成し、DFAGeneratorから得られたDFAをもとに構文解析表の生成を行います。

ここまでで構文解析表の構築まで終了したので、パーサジェネレータはほぼ完成となります。
あとは、この構文解析表を読み取ることのできるパーサの実装が必要です。
次回はパーサの実装を行いますが、そのパーサの動作等についても前回の記事である程度解説しているため、やはり実装中心となります。


[第1回:かんたん構文解析入門](/2016/12/22/lr-parser-generator-implementation/)  
[前回:かんたんLR(1)法入門](/2017/03/22/lr-parser-generator-implementation-04/)  
[次回:パーサの実装と構文解析の実行](/2017/04/04/lr-parser-generator-implementation-06/)

