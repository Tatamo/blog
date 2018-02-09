+++
date = "2018-02-09T19:30:00+09:00"
draft = false
slug = ""
tags = ["dev"]
categories = ["competitive-programming"]
title = "JavaScript/TypeScriptで競技プログラミングをするには 後編"

+++

主要コンテストサイトのJavaScript対応状況、および私がJavaScript/TypeScriptで競技プログラミングをする時に気をつけていることなどを書きます。
今回は後者についてです。

<!--more-->

[前回の記事](/2018/02/09/competitive-programming-using-js-and-ts-01/)ではコンテストサイトのJavaScript対応状況をメインに書きました。
この記事では、実際にJavaScript/TypeScriptで競技プログラミングをする際に気をつけている点について書いていきます。

## 競技プログラミングにおけるJavaScriptの致命的な問題点
残念ながら、JavaScriptが競技プログラミングに向いていない理由となる仕様がいくつか存在します。
### 末尾再帰最適化が実装されていない上にコールスタック上限が小さく、変更できない
JavaScriptではPythonの`sys.setrecursionlimit()`のように再帰呼び出し回数の最大値を変更することができません。
コールスタックの上限は処理系に依存しますが、たとえばnode.js v5.12.0では15712です。
また、末尾再帰最適化はES2015の仕様に盛り込まれていた内容ですが、現在においてもこれを実装している処理系は非常に少ないのが現状です。

これらの要因のため、要素数が数万程度の何かに対して手っ取り早く再帰でDFSを書こうとすると、ほぼ間違いなくMaximum call stack size exceededで落ちます。
自分で配列をスタックとして使ってBFSと同じような書き方で書くか、DFS専用のイテレータでも自作しましょう。
### 64bit整数値を扱う手段が存在しない
JavaScriptのすべての数値は、いわゆるdouble型で保持されています。
`Number.MAX_SAFE_INTEGER`で確認できますが、整数値としての精度が保証される最大値は2^53-1までです。
これはおよそ9.0\*10^15程度なので、入力中に10^18ぐらいの数値が与えられるとその時点で詰みです。

まあ[Int64](https://github.com/broofa/node-int64)とか[biginteger](https://github.com/silentmatt/javascript-biginteger)的なものを使う・作るしかないです。

余談ですが、JavaScriptは歴史的経緯から複数のJavaScriptファイルをまとめて一つのソースコードとすることが頻繁に行われているため、browserifyやwebpackといったツールを使えばサードパーティのライブラリを使うことは割と平気で可能です(ソースコード上限を越えなければ)。
しかし競技プログラミング的にどうなのかよくわからないので、私は今のところ自作以外のライブラリを使ったことはないです。

まあできないことを気にしても仕方がありませんので、以上のような問題点が存在することを把握しておく、ということが大切です。

## 入出力テンプレート
```TypeScript
import * as fs from "fs";

const input = (fs.readFileSync("/dev/stdin", "utf8") as string).split("\n");
const n = +input[0];
// const [n] = input[0].split(" ").map((x: string): number => +x);
const a = [];
for(let i=0;i<n;i++){
	a.push(input[i+1].split(" ").map((x:string):number=>+x));
}

console.log(input);
```

入力部分は多少長くなってしまうので、テンプレートを用意しています。
```
N M
A1 A2 ... A3
```
というような入力に対しては、少し書き換えて
```TypeScript
const [n, m] = input[0].split(" ").map((x: string): number => +x);
const a = input[1].split(" ").map((x: string): number => +x);
```
とすれば対応できます。

`fs.readFileSync("/dev/stdin", "utf8")`部分がWindowsでは動かない気がするので、適当にUNIX環境を用意しましょう。

package.jsonやtsconfig.jsonについては、[このへん](https://github.com/Tatamo/comp/tree/master/template/ts)に上げています。

## (TypeScript) 複数のプログラムを同時にコンパイルする際の注意
npm scripts等で自動コンパイルを行っている場合などに、[モジュール](https://www.typescriptlang.org/docs/handbook/modules.html)でない複数のTypeScriptプログラムを同時にコンパイルすると、すべてのプログラムの変数が同一スコープ上で扱われるため、`input`などの変数が衝突してエラーになります。

これは、それぞれのプログラムがモジュールであると認識されれば解決されます。
そのためのワークアラウンドとして、1行目でfsを取得する際に`require()`ではなく`import`文を使うようにしています。
```TypeScript
import * as fs from "fs";
```
`export`を行っていなくても、TypeScript的にはこれだけでモジュールとして扱われるようなので、複数のプログラムを同時にコンパイルすることができるようになります。

## Array.prototype.shift()の計算量はO(n)
JavaScriptで計算量について触れられている資料は少ないので、下手をすると[仕様書](http://www.ecma-international.org/ecma-262/6.0/#sec-array.prototype.shift)まで見に行かないと気付けないのですが、`shift()`の計算量はO(n)です。
inputに10万行程度の入力を入れて、先頭から1行ずつ`shift()`で取り出すと、それだけで2秒が過ぎます。
`push()`および`pop()`は高速でできるので、普通の配列はスタックとしてのみ使用し、キューは自前で実装する必要があります。

参考までに、私は次のようなコードを使用しています。
```TypeScript
export default class Queue<T> {
	private _in: Array<T>;
	private _out: Array<T>;

	get length(): number {
		return this._in.length + this._out.length;
	}

	constructor(iterable?: Iterable<T>) {
		this._in = iterable === undefined ? new Array<T>() : [...iterable];
		this._out = new Array<T>();
	}

	private _fix() {
		this._out = this._in.reverse().concat(this._out);
		this._in = new Array<T>();
	}

	push(...values: Array<T>): void {
		this._in.push(...values);
	}

	shift(): T | undefined {
		if (this._out.length === 0) this._fix();
		return this._out.pop();
	}

	toArray(): Array<T> {
		this._fix();
		return this._out.slice().reverse();
	}
}
```

## Array.prototype.fill()は同一オブジェクトで埋められる
まあこれは気付かなかった私がよくないという例ですが、一応。

0埋めされた3×3の二次元配列を用意しようとして、次のようなコードを書いたとします。
```javascript
const a = new Array(3).fill(new Array(3).fill(0));
```
まあ確かに0埋めされた3×3の二次元配列は得られますが、a[0]とa[1]とa[2]は同じ一次元配列を参照しています。
```javascript
a[0][0] = 1;
```
とすると、aの中身は
```javascript
[[1,0,0]
 [1,0,0]
 [1,0,0]]
```
となってしまいます。
そのため、多次元配列を初期化するときは横着せずにforで回すようにします。
```javascript
const a = new Array(3);
for(let i=0; i<3; i++){
	a[i].push(new Array(3).fill(0));
}
```

---

以上が、私がJavaScript/TypeScriptで競技プログラミングをする際に気をつけている点です。

こうして書き出してみると、数値が大きくなると詰むとか再帰がろくに使えないだとかでろくな言語じゃないなという感じですね。
競技プログラミングの問題自体がC/C++で解くことを想定されている節があるので、仕方のないことではあります。

とはいえ、以上のことさえ気に留めていれば、JavaScript/TypeScriptを使っていることが原因で詰むことはほとんどないと思います。
JavaScriptそのものは非常に書きやすい言語なので、ハマりどころを把握した上で選択肢にしていただける方が増えればいいなと思っています。
