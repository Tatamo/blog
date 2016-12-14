+++
categories = ["dev"]
date = "2016-12-15T01:11:23+09:00"
draft = false
slug = ""
tags = ["java", "generics", "reflection"]
title = "Javaのリフレクションを悪用してクラス設計してみる"

+++

この記事は[Kobe University Advent Calendar 2016](http://www.adventar.org/calendars/1881)の14日の記事です。遅刻です。
なお私は当該大学の学部2年(2016年12月現在)です。

---

最近、大学の授業などで少しだけJavaに触れる機会があったので、ちょっとした黒魔術っぽいことでもやってみようと思います。  

<!--more-->

と思ったのですが、記事を書こうとして調べていたら普通に書きたかったことをわかりやすく書いている記事がありました。  
* [new T()したいケースへの対処法 - プログラマーの脳みそ](http://d.hatena.ne.jp/Nagise/20131121/1385046248)  

もう帰っていいですかね。

## 総称型Tに対してnew T()したい

### ジェネリクスとは
わざわざ書くまでもないとは思いますが、ジェネリクスについておさらいをしておきます。
```Java
ArrayList<String> strlist = new ArrayList<String>();
ArrayList<Integer> intlist = new ArrayList<Integer>();
```
ArrayList<T>に対し、ジェネリクスとしてStringやIntegerなど、リストの要素として入れたい型をTの部分に具体的に書くことで、一つのクラスであるArrayListに利用者側が好きな要素を入れることができるようになります。

ジェネリクスを用いて自分でメソッドを定義する場合は、以下のようになります。

```Java
public <T> void doSomething(T arg){
	// doSomething
}

// TをSomeClassのサブクラスに限定する
public <T extends SomeClass> void notDoAnything(T arg){
	return;
}
```
これらのメソッドを呼び出すときは、`hoge.<SomeClass>doSomething();`のようになります。  
どうして`hoge.doSomething<SomeClass>();`じゃないんだろう。

### ジェネリクスはT.classやnew T()をさせてくれない
Javaのジェネリクスでは、以下のコードはコンパイルエラーとなります。
```Java
public <T extends SomeClass> List<T> getSubClassList(List<SomeClass> list){
	return list
		.stream()
		.filter(value -> value instanceof T) // ここでlistの要素valueがTのインスタンスかどうか判別したい -> error
		.collect(Collectors.toList());
}

public <T extends SomeClass> List<T> getSubClassList2(List<SomeClass> list){
	return list
		.stream()
		.filter(value -> value.getClass() == T.class) // instanceofがだめならclassを取得して判別したい -> error
		.collect(Collectors.toList());
}

public <T> void doSomething(String arg){
	T foo = new T(arg); // T型の新しいインスタンスfooを作りたい -> error
}
```

ジェネリクス型に対して、hoge instanceof T、T.class、new T()といった操作を行うことはできません。
残念でしたね。

## なぜT.classしたいのか

### もしかして： 設計が間違っている

先ほどのgetSubClassListメソッドを見てみましょう。
正しく動くかどうかはさておき、やりたいことは以下の通りです：  
* SomeClassとそのサブクラスを含んだリストlistの要素のうち、SomeClassのサブクラスTのインスタンスだけを要素とする新しいリストを取得したい。

そもそも、抽象化された集合から具体的なクラスのインスタンスを取り出そうとすること自体がオブジェクト指向やポリモーフィズムの考え方に対する反逆である可能性があります。

SomeClassのリストであるlistをイテレートして、それぞれの要素のdoSomething()メソッドを呼べば、あとはそれがSomeClassのインスタンスであろうと、もしくはSomeClassを継承した別のクラスのインスタンスであろうと、外部からはただdoSomething()メソッドを呼ぶだけで操作が完結していなければなりません。
それがサブクラス固有のふるまいをするとしても、その実装はそのクラス自身に隠蔽されるべきなのです。

よって、サブクラスだけを取り出そうとする試み自体がナンセンスなものである可能性が非常に高く、まずそのような試みが必要になる設計そのものを見直す必要があります。

終わり。

### そうも言っていられない
実際その通りなのですが、それではこの記事が続きません。  

ここからは、少し具体的なプログラムの設計を例にとって考えてみたいと思います。  
あなたはちょっとしたゲームを作るためのフレームワークを作っています。
その一部分が以下のようになります。

```Java
// 抽象的なゲーム内エンティティクラス
public abstract class Entity {
	private int x;
	private int y;
	public Entity(int x, int y){
		this.setX(x);
		this.setY(y);
	}
	public int getX() { return x; }
	public void setX(int x) { this.x = x; }
	public int getY() { return y; }
	public void setY(int y) { this.y = y; }

	public abstract void update(EntityManager manager);
}

// なんでもかんでも××Managerという名前を付けるのはやめましょう
public class EntityManager {
	private ArrayList<Entity> all_entities;
	public EntityManager(){
		this.all_entities = new ArrayList<Entity>();
	}
	public void add(Entity e){
		this.all_entities.add(e);
	}
	public ArrayList<Entity> getAll() {
		return new ArrayList<Entity>(this.all_entities);
	}
	public void updateAll(){
		this.all_entities.forEach(e->e.update(this));
	}
}
```
なんのことはありませんね。
ゲーム内に登場するエンティティを表す抽象クラスEntityを作り、それをEntityManagerで管理します(なんでもかんでも××Managerという名前を付けるのはManagerクラスの肥大化を招きかねないため避けるべきであるなどという話もありますが、今回は置いておきます)。
たとえば毎フレームごとにメインループ内で`EntityManager#updateAll()`を呼び出すことで、全てのエンティティの更新を行ったりすることが想定されます。
オーソドックスなループ駆動型のゲームプログラム設計です。  
Entityインスタンスを作成するためのFactory Methodパターンを使うべきとか、そういう話もあっちでやってください。

あとは、このフレームワークの利用者にはEntityクラスを継承したクラスを作らせ、それをもとにゲームを作ってもらえればいいだけです。
以下のクラスを追加してみましょう。

```Java
// あなたです
public class Player extends Entity{
	private int hp; // プレイヤーはヒットポイントを持ちます
	public Player(int x, int y, int hp){
		super(x, y);
		this.setHp(hp);
	}
	public int getHp() { return hp; }
	public void setHp(int hp) { this.hp = hp; }
	@Override
	public void update(EntityManager manager){
		// プレイヤーが動きます
	}
}

// 敵です
public class Enemy extends Entity {
	private int damage; // 敵がプレイヤーに与えることのできるダメージ値です
	public Enemy(int x, int y, int damage){
		super(x, y);
		this.setDamage(damage);
	}
	public int getDamage() { return damage; }
	public void setDamage(int damage) { this.damage = damage; }
	@Override
	public void update() {
		// 敵が動きます
	}
}
```
プレイヤーと敵を登場させました。
常命の者であるプレイヤーにはヒットポイントが定められており、敵はこれを脅かすダメージ値を持っています。

注意しておく必要があるのは、これらのクラスはあくまでフレームワークの利用者が作るものだということです。
よって、EntityクラスやEntityManagerクラスからはこのようなEntityサブクラスの存在を知ることはできません。  
知る必要もないというのがオブジェクト指向の考え方だったはずです。
そうですよね？

ここで、「敵のダメージ値を参照して、プレイヤーのヒットポイントを減らす」処理をしたいという需要が当然生まれます。
実装の方法はいろいろあると思いますが、ひとまずこの処理を、Enemyのメソッドとして書いてみます。
```Java
// 敵です
public class Enemy extends Entity {
	private int damage; // 敵がプレイヤーに与えることのできるダメージ値です
	public Enemy(int x, int y, int damage){
		super(x, y);
		this.setDamage(damage);
	}
	public int getDamage() { return damage; }
	public void setDamage(int damage) { this.damage = damage; }
	@Override
	public void update(EntityManager manager) {
		// 敵が動きます
		// プレイヤーがいたらダメージを与えます
		manager.getAll().forEach((entity)->{ // 全てのエンティティを調べる
			if(entity instanceof Player){ //  エンティティがプレイヤーなら
				if(this.getX() == entity.getX() && this.getY() == entity.getY()){ // プレイヤーと敵の座標が同じなら
					this.doDamage((Player) entity); // ダメージを与える
				}
			}
		});
	}
	public void doDamage(Player p){
		p.setHp(p.getHp() - this.getDamage()); // プレイヤーのヒットポイントをダメージ値分だけ減らします
	}
}
```

ここであなたは気を利かせます。
```Java
manager.getAll().forEach((entity)->{
	if(entity instanceof Player){
		if(...){
			...
		}
	}
}
```
この部分です、どう見ても冗長ですし、いちいち全エンティティのリストをforEachで回しているので高速化も望めませんし、同様のコードクローンが至る所に発生するのは目に見えています。

できるならたとえばこう書きたい。
```Java
manager.<Player>getAllOfSubClass().forEach((player)->{
	if(...){
		...
	}
}
```
これでPlayerクラスだけが得られるなら、呼び出し側のコード量は減りますし、もしあなたがO(n^2)の処理をO(nlogn)に落とすような作業に慣れているなら、Managerクラスの内側でより高速になるような最適化を行い、その実装も内部に隠蔽することができます。

また、ゲームのセーブとロードをするときのことも考えてみます。
たとえばデータのセーブを行うときはとりあえずオブジェクトを文字列化するとして、  
`public String Entity#serialize()`などといったメソッドを作ってセーブし、ロード時には文字列配列を引数として受け取るようオーバーロードされたコンストラクタを使用してオブジェクトを再生成するといったことが考えられます。
そういうことはFactory Methodあたりに委譲するべきな気もしますし、うまくやらないとフレームワークの利用者側の負担が増える気もしますが、今は置いておきます。

結局、セーブデータのロード時にこのようなコードを書くことになるでしょう。
```Java
	public <E extends Entity> E createEntity(List<String> args){ // 可変長引数としてもよい
		return new E(args); // ジェネリクス型はnewできない -> error
	}
	public loadSaveData(SaveData savedata){
		// セーブデータから1行ずつ読み込んでオブジェクトを再生成する
		... {
			entityManager.add(this.createEntity(agrs));
		}
	}
```
しかしジェネリクス型はnewできないため、コンパイルエラーとなります。

## 解決策
さて、だいぶん勿体つけたが、解決編に入ろう。

……というか、例として取り上げた題材が無理やりすぎた気がしてやる気が尽きかけてきました。

### リフレクションを使用する

Javaにはリフレクションという機能があり、プログラムの実行中にクラス名やメソッド名を動的に取り扱うことができます。
これのClass<T>型を、ジェネリクスと併用しましょう。
`EntityManager#getAllOfSubClass`メソッドを考えてみます。
実装は以下のようになります。
```Java
	@SuppressWarnings("unchecked")
	public <E extends Entity> List<E> getAllOfSubClass(Class<E> cls){
		return (List<E>) all_entities.stream()
			.filter(entity -> entity.getClass() == cls)
			.collect(Collectors.toList());
	}
```
これを呼び出す際は、
```Java
entityManager.getAllOfSubClass(Player.class);
```
のようにして、Class型の`クラス名.class`を引数として与えます。
ここで型推論が働くので、
```Java
entityManager.<Player>getAllOfSubClass(Player.class);
```
のようにPlayerクラス名を2度も書く必要はありません。

また、Playerクラスだけでなく、Playerクラスを継承したクラスも含めて判別したいときは、以下のようにします。
```Java
	@SuppressWarnings("unchecked")
	public <E extends Entity> List<E> getAllOfSubClass(Class<E> cls){
		return (List<E>) all_entities.stream()
			.filter(entity -> cls.isAssignableFrom(entity.getClass()))
			.collect(Collectors.toList());
	}
```
`createEntity`メソッドも、以下のようにすれば書くことができます。
```Java
	public <E extends Entity> E createEntity(Class<E> cls, List<String> args){
		// cls型のString[]を引数にとるコンストラクタを呼び出す
		return cls.getConstructor(new Class<?>[] { String[].class }).newInstance(args);
	}
```

やや黒魔術じみてきました。
実際にはE型がString[]を引数にとるコンストラクタを持つということが担保されていないためこれだけでは動かず、例外回避のためにいろいろやる必要があります。

最初の例に戻ってみましょう。`getSubClassList`は、以下のように書けます。
```Java
public <T extends SomeClass> List<T> getSubClassList(List<SomeClass> list, Class<T> cls){
	return list
		.stream()
		.filter(value -> value.getClass() == cls)
		.collect(Collectors.toList());
}

// 呼び出し側
getSubClassList(list, SubClassOfSomeClass.class);
```

めでたしめでたし。

### おまけ

```Java
public <T> int getStaticValue(Class<T> cls) throws Exception{ // なにがthrows Exceptionだやる気あんのか
	return (int) cls.getMethod("getStaticValue").invoke(null);
}

// 呼び出し
getStaticValue(SomeClass.class); // SomeClass.getStaticValue() を呼んでいるのと同じになる
```
staticメソッドも呼び出し可能なうえ、「そのクラスに与えられた名前のstaticメソッドが存在しない場合、その親クラスの同名のstaticメソッドを呼び出す」というようなふるまいを疑似的に再現することも可能です。

## おわり
`Class<T> cls`を引数にとり、`value.getClass() == cls`で比較を行うのは、実質的にはinstanceofの迂回といえます。  
この程度なら型安全性は一応担保されますが、コンストラクタやメソッドの取得と呼び出しまで始めた場合、throw宣言やcatch節が山のように膨らんだり、メソッドの存在確認や型の確認などに多大なコストを支払う必要がありそうです。
実際に使用するには相当の覚悟を必要とするでしょう。**やめましょう。**

自分でプログラムを組んでいる際にこのような需要が発生することが時々あったので、Javaではリフレクションを使うことで強引に解決できると知って記事を書いてみましたが、自分の中ではむしろ「設計が悪い」の一言に実感が沸くという結果となった気がします。  
今回例に挙げたゲームプログラムの設計はまさしく昔私が作ったものそのものなのですが、記事を書く上で「リフレクションを使いたい理由付け」を明確にしようとすればするほど、その理由が無理やりなものにしかならないのではないかという気持ちに陥りました。
途中まで書いた記事がグダグダになりそうな予感がしてくると結構精神的につらい。  
……うーん。Abstract Factoryパターンとか、もととなるクラスが増えるとそれに合わせてFactoryクラスも増やさないといけないのがあまり好きじゃないので、そのあたりも動的になんとかできないかなどと思っていたのですが、なかなか難しいようです。

私感としては、instanceof Tの代用としてClass型を使用する程度は許容できますが、普通のプログラムを書くならそれ以上は安易に手を出すべきではないという印象です。

プログラミングをするときはまともな設計をするようにしましょう。

