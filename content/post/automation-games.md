+++
date = "2019-12-10T23:59:59+09:00"
draft = false
slug = ""
tags = ["automation"]
categories = ["game"]
title = "自動化というゲームジャンルの成立と変遷"

+++

この記事は[Kobe University Advent Calendar 2019](https://adventar.org/calendars/4690)の9日の記事です(遅刻…)。
なお私は当該大学の修士1年(2019年12月現在)です。

---


私がフォローしているゲームジャンルの一つで、近年よく見かけるようになった「自動化ゲー」について、これまでのタイトルを振り返るとともに語っていきたいと思います。

<!--more-->

一部のゲーマー、特にゲーマー兼プログラマであるような人たちに強く支持されているゲームのジャンルとして、「自動化」というものがあります。
これは比較的最近成立した(と私は思っている)カテゴリで、最も有名かつよくできた作品として[Factorio](https://factorio.com/)が挙げられますが、このゲームがリリースされたのは2016年です。

この種のカテゴリでゲームが作られ始めていることは一般にあまり認知されていない印象があり、自動化([Automation](https://store.steampowered.com/tags/ja/Automation/))という括りでこれらのゲームについて言及された例は(少なくとも日本のインターネット上では)ほとんど見たことがありません。
そこで、本記事ではこれら自動化ゲーにどのようなものが存在してきたのかを見ていきながら、その成り立ちを振り返ることにします。

なお、この記事ではゲーム全般に関する最低限の事前知識として、[Minecraft](https://www.minecraft.net/)がだいたいどのようなゲームかわかる(プレイしたことがある/プレイを見たことがある)程度を想定しています。

## 自動化ゲーとは
その名の通り、自動化ゲーは「自動化」をゲームの主題(の一部)としているものを指します。

資源やアイテムの獲得・生産などの目標が与えられたとき、プレイヤーやプレイヤーキャラクターの手作業ではコストや手間が膨大になりすぎるものを、「アイテムをたくさん作る」のではなく「アイテムをたくさん作ってくれる設備を作る」ことによって解決し、その過程自体をゲームプレイに組み込んだものが(特に黎明期の)自動化ゲーといえるものです。

おそらく一番話の通りやすいであろうMinecraftを例に出すと、たとえば農作物を育てて食料を得る場合、まずは畑に種を植えて育ったら収穫する、という手順を手作業で行うでしょう。
次に、ボタンを押すと水が流れるような仕組みをつくり、作物の収穫作業の一部を効率的に行えるようにすることもあるかもしれません。
さらに、NPCの村人(農家)の「付近の農作物の収穫と種まきを行う」という性質を利用すると、プレイヤーは一切手を出さなくても自動で農作物の育成が行われ、待っているだけで作物が手に入るような設備を作ることもできます。

アイテムが少ない労力で、または労力ゼロで手に入るようになると、プレイヤーはより価値の高い、複雑な作業工程を必要とする新しいアイテムの獲得にとりかかることができます。
やがては、その新しいアイテムも自動的に生産できるようになっていくかもしれません。
このような自動化の過程そのものに楽しみを見出し、アイテムの自動生産施設のようなものを次々に作っていくのが自動化ゲーのメインテーマであるといえます。

このあたりもゲームによって違ってくるところはありますが、いくつかの(私がプレイした)作品については、後に個別の紹介を行います。

### 隣接するゲームジャンル、および相対的位置付け
自動化と似たような位置付け、または上位にあたるようなゲームジャンルをいくつか提示し、同時にそれらとの比較によって自動化ゲーがどのようなものであるかを見ていきます。

#### シミュレーション
シミュレーションゲームという言葉の指す範囲が広すぎてよくわからないところはありますが、おおよその自動化ゲーはシミュレーションゲームに含まれるでしょう。
特にSimCityやTropicoのような都市建設・経営シミュレーションは典型的な資源管理要素を持ったゲームであり、これらの要素が自動化要素と同居していることはよくあります。

##### 資源管理
あらゆるゲームが資源管理、いわゆるリソースマネジメントの側面を持つという考えもありますが、基本的には、手元にある限られた資源を効率的に分配し、拡大再生産を行いながら資源の枯渇が起きないようにするゲーム要素のことを指します。
シミュレーションゲーム、またはストラテジーゲームの内政等でよく意識させられますが、よりシンプルな例としてはサバイバルゲームがあり、たとえば食料というリソースの枯渇はプレイヤーキャラクターの死・ゲームオーバーを意味するため、プレイヤーはこれを回避するべく行動します。

資源管理ゲー、といった場合は、具体的には[Banished](https://store.steampowered.com/app/242920/)のようなゲームを指すことが多い印象があります。
その系譜として、なぜかFactorioと比較されることの多い[RimWorld](https://store.steampowered.com/app/294100/)や、自動化要素も多く含んだ[Oxygen Not Included](https://store.steampowered.com/app/457140/)などが該当し、自動化ゲーとの繋がりは大きいといえます。

「限られた数のキャラクターの仕事をうまく管理し、持続可能なコロニーの成立を目指す」というようなゲーム目的を考えると、究極の目的はシステム全体の自動化、とみなすこともできるのかもしれませんが、この手のゲームは完全放置ですべての資源が増えていくようなシステムを作らせてはくれない気がします。

特にこの文脈で資源管理ゲーという単語を用いた場合、だいたいの自動化ゲーは資源管理ゲーの下位ジャンルにあたるとされます。
一方で、資源管理ゲーの重要な要素として「資源の枯渇を避けなければならない(特定の資源が尽きるとゲームオーバー)」というものがある気がしますが、純粋なゲームジャンルとしての自動化ゲーを見た場合、必ずしも資源の枯渇を避けるような動機付けがされる必要はないので、資源管理ゲーに含まれない自動化ゲー、というものは考えられます。

#### パズル
「自動化パズル」というゲームは複数存在し、そこそこ人気があります。
与えられた問題に対してその条件をみたすシステムを作る、という点から「プログラミングパズル」と呼ばれることもあり、自動化というゲームジャンルがプログラマに人気があるのも頷けるところです。

また、自動化ゲーではクエストのような形でプレイヤーに目標が示される形式になっている作品も多く、段階的に目標を達成していく手順は、パズルを順番に解いていくことと重ねることもできます。
もっとも、このようなパズルゲームに分類される自動化ゲーを自動化ゲーと呼ぶかどうかについては、個人的には疑問を感じるところでもあります。

あくまで一意見でしかありませんが、自動化パズルゲーとそうでない自動化ゲーの違いは、ステージクリアの概念にあると考えます。
Factorioは「やめ時が見つからないゲーム」にあたるとされていますが、それに寄与している重要な要素の一つとして、一度作った自動化設備はその後ずっとシステムの一部として動き続ける、という点があります。
ゲームの進展とともに、自動化のために使えるパーツの性能も向上していき、より効率的に自動生産が可能になるのですが、一方で上位パーツが解放される前に作られた施設は旧態依然とした低効率のままです。
そして生産物の需要の高まりとともにレガシーな生産ラインの存在がシステム全体のボトルネックとなりうるため、一度作った施設も中長期的な間隔で見直しが必要となります。
つまりゲームが進めば進むほどに考えることが増え、常にTODOキューにタスクが貯まった状態が維持され、「やめ時が見つからなく」なるわけです。
ステージクリア型のパズルゲームの場合、次のステージに作ったものは持ち越しとならないため、ゲーム全体の印象は大きく変わります。

具体的な作品の例を挙げるなら、[Zachtronics](https://store.steampowered.com/sale/zachtronics)が作っているゲーム、と言えばだいたい事足りるでしょう。
それ以外では[Automachef](https://store.steampowered.com/app/984800/)が最近発売されました。
あとは[Human Resource Machine](https://store.steampowered.com/app/375820/)が思い浮かびますが、これは自動化というより完全にプログラミングです。

## 自動化ゲーの成立と変遷
いくつかのゲームについて、リリース順に見ていくことによって自動化ゲーがどのように移り変わってきたのかを振り返ります。
### Minecraft
自動化というゲームジャンルの成立において、[Minecraft](https://www.minecraft.net/)とそのMODコミュニティの存在を抜きにして語ることはできないでしょう。
Zachtronicsによるプログラミングパズルの[SpaceChem](https://store.steampowered.com/app/92800/SpaceChem/)を除けば、自動化ゲー黎明期の歴史はそのままMinecraftにおけるMOD開発の歴史と言い換えることができます。
そのため、この記事でもMinecraftについては特に多くの紙面を割くことになります。

#### バニラ
バニラというのは、「MODが入っていない」という意味です。

Minecraftにはレッドストーン回路と呼ばれる要素が早くから組み込まれており、ゲームの中で論理回路が構成できるようになっていました。
これはゲーム内のオブジェクトから入力を受け付けたり、逆に回路の信号を用いてオブジェクトに影響を与えることも可能であるため、複雑な制御機構を要するような機械でもMinecraft内で作成できてしまうという自由度の高さがありました。

そして、その回路機能を用いて一部作業を自動化すれば、効率的なゲームプレイができるということもプレイヤーに対するモチベーションを与えました。

最新バージョンのMinecraftでは、先述した農作物や、敵のモンスターからのドロップ品全般、重要な鉱石である鉄や金も、「放置していれば勝手に増えていく」ような仕組みを作り上げることが可能です。
一度そのような施設を作ってしまえばそこから生産されるアイテムは事実上無制限に入手可能となるわけですが、そのような施設を作り上げることにも資源コストがかかることから、手間とリターンのトレードオフがあり、探索や探検を主体としたゲームプレイに悪影響を与えるには至らないという巧さがあります。

#### Minecraft1.2.5+工業化MOD
Minecraft バージョン1.2.5は2012年にリリースされたバージョンで、初期のMinecraftにおいて最も盛んにMOD開発が行われたバージョンです。
(厳密には1.2.5までの時点で既に開発は盛んだったのですが、次のバージョンである1.3に対応したMODを作るのが大変だったため、1.3以降への互換性対応を放棄して1.2.5でしばらく更新が続けられたMODが多数存在したという形になります)

今更ですが、MODとは(主に)開発元とは関係のない第三者が、ゲームに追加の要素を付け足したりすることを言います。
Minecraftでは、UIの改良などの便利機能を追加するものや、バニラにないアイテムを追加したり、新しいダンジョンや地形などをゲーム内の世界に登場させるようなMODが作られています。

その中で、IndustrialCraft2(IC2)、BuildCraft(BC)をはじめとした、工業化MODと呼ばれるMOD群が登場しました。
これらのMODの画期的であった点として、大規模な自動化要素の追加とエネルギー要素の導入が挙げられます。
まず、バニラのMinecraftでは不可能であったアイテムの自動クラフトを可能にしたり、バニラでは手作業を排することのできないようなアイテムの生産工程を自動化するような「機械」が追加され、これらの機械を使えばバニラ以上に多くの種類のアイテムが自動で入手できる(ような施設を作ることができる)ようになります。
同時に「石炭を燃やして発電する」だとか「石油(追加アイテム)を燃やして発電する」といった要素が導入され、機械を動かすためにはエネルギーが必要であるため、「作業を自動化するための機械を動かすための燃料の確保の自動化」が要求されるなど、アイテムの生産という目的に対して考えるべきことが増えていきます。

「アイテム生産のための機械作りのための機械作りのための素材作りのための機械稼働のための燃料確保のための自動化施設作り」と、プレイヤーは複雑に絡み合った依存関係を一つ一つ解決していかなければなりません。
具体的な例としては、たとえば機械を動かすエネルギーを届けるためには送電線が必要で、送電線を作るには銅線が必要で、そのためには銅のインゴットを平たい板に伸ばしてからカッティングしないといけません。
最初の機械を動かすための数本だけの銅線なら手作業でも作れますが、コストが嵩むため「銅線を作る機械」を早いうちに作る必要があり、そのような機械を作るためには素材アイテムである制御回路が必要で……といった具合になります。

もちろん、このような大変な作業を経た暁には、工業時代が幕開けて大量のアイテムを得られるようになったり、なんか技術が進んで空を飛べるようになったりと、様々な報酬が約束されています。
逆に、このような工程を自動化施設の作成という手順を踏まずに手作業でゴリ押そうとした場合、作業コストが指数関数的に増大して遠くないうちに詰むようになっています。

一度施設を作ってしまえば大きなリターンが得られ、次の段階に進めるといった要素がここには無数に埋め込まれており、アイテム生産のための素材の依存関係がそのままレベルデザインとなっています。
このような自動化ゲームのコアとなる要素は、この時代の工業化MODで既に確立されていました。

MODは(時々プログラムが競合を起こしますが)基本的に複数同時に導入することができるため、有名な工業化MODを複数入れて遊ぶことが主流でした。
次第に、特定のMOD同士を協調動作させるようなMOD(MODごとに別々に導入されるエネルギーを相互変換できるようにするなど)や、特定のMODが導入されていることを前提としたMODなども作られるようになっていきます。
大規模なMODの開発だけではなく、それを補助する小さなMODを作ることでもゲーム性の向上につながることから、MODコミュニティという特性はこの新しい形のゲームプレイに対して非常に良い影響を与えました。

いくつかのMODを軽く紹介しておきます。

* IndustrialCraft2 
    * Minecraftに電力の概念と工業機械と原子力と鬼のような数の中間素材アイテムをもたらした工業MOD。
    * 追加される道具も採掘ドリル/レーザーとか飛行用ジェットパックとか原子爆弾とかそこそこ魅力的だったのも○。
* BuildCraft 
    * 今でも若干禁忌っぽさのある、「鉱石の自動採掘」を可能にしたMOD(とはいえ全自動ではない)。
    * 最大の貢献は「アイテム導管」の追加。あちらにあるアイテムをこちらに移す、という作業の自動化はその後のMinecraftにおける工業化の一つの核となる。
        * それ故に時代が進歩しすぎたためか、今見るとこのMODのアイテム導管は使い難くて仕方がない
    * そもそも*Build* Craftのはずなのに建設要素よりも採掘要素ばかり当てにされている。
* ForestryForMinecraft
    * 農業関連の自動化設備を追加するMOD。バニラのMinecraftにおいて「木を伐る」作業は未だに効率化困難なタスクのひとつ。
    * 農産物の自動生産も魅力的だが、隠れた目玉は「蜂の交配」。メンデルの法則に従った品種改良では無限に時間が溶けました(Industrious蜂はもう何度作ったかわからない)
* RedPower2
    * レッドストーン回路は場所を取るので、いくつかのゲートを1ブロックにまとめることができて便利。
    * 自走機械のようなものが作れたが、今から考えると「複数のブロックを継続的に移動させる」というのはそもそもMinecraftのシステム上やらないほうがいい

#### Minecraft1.7.10+工業化MOD
バージョン1.2.5のように、「たくさんのMODが作られたバージョン」というのがいくつかあります。
基本的には、その次のバージョンでゲームの内部仕様が大きく変わるなどでMinecraft本体の更新に取り残されるMODが多発したバージョン、という気はしますが。
2014年にリリースされたバージョン1.7.10はその最たる例で、敢えて最新のバージョンではなく1.7.10を遊ぶ価値があるほどにMODが充実しています。

とはいえ工業化というゲームの主な要素は1.2.5時代には既に出揃っていたため、バージョン1.7.10では後続のMODの登場による改良とバリエーションの増加、そしてMODパックの充実がメインになります。
加えて、これまでMODごとに独自のエネルギー単位が設定されていましたが、多くのMODの間で一つのエネルギー単位を共通で使えるようになり、遊びやすさが向上しています。

1.2.5時代に提示された「工業化Minecraft」という遊び方に対して、それに呼応した新しいMODが多数登場したのがこの時代です。
有名なMODですら数が多すぎていくつかを精選するだけでも大変ですが、ここでも多少言及しておきます。
なお、1.2.5で挙げた主要な工業化MODは、(RedPower2の後継プロジェクトも含めると)すべて1.7.10でも利用することができます。

* EnderIO
    * 1.2.5の次の時代を提示した新しい工業化MOD(当時)。
    * アイテム輸送が大幅に改良された。EnderIO以前はアイテム導管と送電線がそれぞれ1ブロックのスペースを取るので配管が大変だったが、これらを1本でまとめることができるようになった
* AppliedEnergistics2
    * アイテムストレージMOD。アイテムを「データ化」して一箇所にまとめられるため、アイテムの集中管理が容易になる。
* Botania
    * 工業化MODの正反対に位置する「魔術MOD」、の見た目をしつつ実態は独自の自動化システムを内包した工業化MOD。個人的にはかなり好き。
* DraconicEvolution
    * 工業化のさらに先にあるエンドコンテンツの提供。基本的に中盤まではMOD要素に触れることすらできない。
    * エネルギーのワイヤレス伝送が可能となり、送電線が全く場所を取らなくなるのでとても良いが解放が遅すぎるのがネック。
    * だいたいの場合エンドコンテンツがこれになるのでそろそろワンパターンで飽きる

また、1.7.10より、MODパックと呼ばれるものが整備され、普及していきました。
工業化MODを含め、MinecraftのMODは複数導入するのが前提とみなされており、いわゆる「工業化Minecraft」のMOD環境を整備しようとした場合、導入MOD数が100を越えるのは珍しくありません。

それに加えて、「銅鉱石」などはよく追加されるポピュラーなアイテムのため、別々のMODで生成された同名アイテムが複数種類存在してしまうこともあります。
これはMOD開発環境が整備されたことで、「鉱石辞書」と呼ばれる機能により相互運用が可能になっていますが、依然として同名のアイテムが複数種生成されることはあり、その回避のために一つのMOD以外の鉱石生成を無効化するよう設定する必要が生じることがあります。
似たようなアイテムではクラフトのレシピも重複してしまうことがあり、片方のアイテムのレシピが上書きされて入手不可能になるなどの弊害もあるため、これらも調整が必要です。
また、そもそも複数のMODを導入することによるゲームバランスの偏りは無視できず、あるMODが低コスト高効率の発電機を実装していた場合に他のMODの発電機が全く使われなくなる、といった問題も生じることがあります。

これらの諸問題のため、同時に導入したいMODの数が増えれば増えるほど、満足にゲームをプレイするまでのハードルが高くなってしまいます。
そこで、複数のMODを競合しないように設定し、そのMOD構成を配布するMODパックが登場しました。

近年ではMODの配布が[CurseForge](https://minecraft.curseforge.com/)を利用する形に一極化しつつあることも伴い、CurseForgeのプラットフォーム上で提供されるMODパックは従来のMOD導入と違って非常に簡単に導入することができ、自分で詳細な設定をしなくてもすぐにプレイできます。
加えて、人気のあるMODパックはMOD間のバランスもよく考えられていることが多いため、プレイヤーにとってはとてもありがたい存在になります。

MODパックの登場によって可能となった遊び方として、クエスト形式で複数のMODの要素を横断的にプレイする、というものがあります。
工業化MODに顕著な傾向ですが、そのMODのエネルギーシステムやアイテムの種類、独自の機能等をある程度把握していなければ、どう進めていいかわからない状態に陥ることもあります。
MODパックに同梱されているクエストブックMODなどの機能により、次に何をすればよいかという目標が提示されることがあり、このようなMODパックは不慣れなプレイヤーのためのチュートリアルとしての役割も果たすことができます。

MODのチュートリアルとしてのクエスト方式の進化系に、SkyBlockやStoneBlockといった、周囲の世界に何もない状態からMOD要素だけで生産施設を構築していく、MOD要素を使わざるを得ない形式のMODパックもあります。

#### Minecraft1.12.2+工業化MOD
MODパック等々書きたかったことを上でほとんど書いてしまったのでここに書くことがありません。
1.12.2は2017年にリリースされたもので、MODが比較的充実しており、また比較的最新のバージョンに近いバージョンです。
もう少し古くて1.7.10よりは新しいバージョンとしては、1.10.2もMODが充実しているように思います。

基本的なUIの向上に加えて、MODパックに至るまでの環境が既に整った上で迎えた時代であるため、MOD導入の段階からプレイに至るまで「モダンな」MOD体験ができるでしょう。
モダンなMOD開発環境に乗っかった新しいMODや、1.7.10からのアップデートを乗り越えて開発が継続されたMODなどで構成されているため、洗練されたプレイ感を味わうことができる印象にあります。

また、1.7.10に「取り残された」MODが多くあることなどから、それまでの時代のMODに親しんでいてもまた違ったMOD構成でのゲームプレイを楽しめます。
たとえば1.7.10では何もない世界で鉱石を入手するにはまず砂利や砂をふるいにかけていましたが、1.12.2では盆栽を育てたりします(？？)

まあ殆どMinecraftの話になってきましたし、自動化に関する要素についてはMinecraftのMOD開発の流れの中で改良されていったことについては既に見てきた通りですので、次に向かいましょう。
いくつかMODを紹介したいとも思いましたが、1.7.10の時点でも存在していたがバージョンアップで遊びやすくなったMOD、などが多い気がしたので割愛します。

### Factorio
自動化というゲームジャンルはMinecraftのMODコミュニティの中で着実に育てられてきましたが、一本のゲームタイトルとして大きなインパクトを与えた作品といえば、[Factorio](https://factorio.com/)でしょう。
2016年というリリース年はMinecraftバージョン1.7.10よりも後で、このゲームは[Minecraftの工業化MODに影響を受けた](https://www.reddit.com/r/nerdcubed/comments/2c9rh7/nerd%C2%B3_fw_factorio/cjdtffh/)ものです。

FactorioはAutomationを主題としたおそらく初の作品でありながら、同時に非常に完成されたゲームとなっています。

基本的なゲームシステムは、あちらで作ったアイテムをベルトコンベアに載せ、こちらに流してきて他のアイテムと一緒に組み立て機械に放り込むことで新しいアイテムを生産し、それをまたコンベアに載せて向こうへ流す……といったことの繰り返しです。
要求されるアイテムの生産工程の複雑さが増すにつれて生産ラインの周到な設計が必要となり、また技術の陳腐化や需要の爆発的増大などによって一度作った施設のリファクタリングを迫られる機会もあります。

生産ラインを構築する上では、取り回しがよく局所輸送に適したベルトコンベア、信号待ちなどの調整が必要だが長距離・大規模輸送が可能な列車、手はかかるが輸送経路のためのスペースを取らずに直接アイテムを移動可能な輸送ロボットと、複数のアイテム輸送方式が提供されていることや、生産する施設が大規模になり、多くの繰り返しが発生してしまう場合に予めパターンを設定しておきロボットに建設を任せる建設計画機能が用意されていることなど、必要と思われる要素は纏まっておりやはり非常に完成度が高いといえます。

また資源管理要素とサバイバル要素も含んでおり、施設の稼働には電力が必要ですが、(特に初期Tierの)発電機はハイペースで環境を汚染し、環境汚染が広がるとそれに引き寄せられた「敵」が襲ってくるというシステムになっています。
そのため、ゲーム中盤では生産力の拡大に加えて施設を守るための防衛力の確保も課題となります。

このサバイバル要素についてはMinecraftからの影響が見られますが、後発の自動化ゲーにはこのような要素を廃したものも多くリリースされています。
また、「一次資源が枯渇する」ことについても言及しておいたほうがよいかもしれません。

Minecraftと同じで、Factorioのゲームマップは探索した分だけ広がっていきますから、鉱脈から鉱石を掘りつくしてしまっても、新たに鉱脈を見つければよいため、本当に資源が枯渇するということはありません。
ですが、どこかの鉱脈が枯渇した場合、代替となる新しい鉱脈に採掘設備を再設置する必要があります。
当然生産された一次資源を加工場に届けるための鉄道路線の拡張が必要になることもあります。
これらの作業は効率化はできても完全な自動化を行うことはできず、また全体で大量のアイテムを生産するようになると、それにともなって莫大な一次資源が消費されるため、鉱脈が枯渇するまでの間隔が短くなっていきます。

このような仕様は、自動化を楽しむという目的からすれば手間がかかるだけとも捉えられるため、他のゲームでは全リソースが無限に採掘可能になっている例も多くあります。
とはいえ、どれだけ掘っても尽きることのない鉱床というのもナンセンスなものではあるので、ここはゲーム性との相談になってくるのかもしれません。

また、この作品と後述のFactory Town、あと[Oxygen Not Included](https://store.steampowered.com/app/457140/)についていえることですが、特に用もないのに論理回路が使えるようになっています。
Minecraftの潮流から回路が組めるようにしたい気持ちはわかるし、それを使うことで便利になる場面もごく僅かに存在してはいるんですが、実際あってもなくても大差ないのにとりあえず入れてある感が否めません。
とても便利な「機械」が提供されているのに、今更論理ゲートで喜ぶというのも世界観に対してミスマッチという気はします。

いろいろと書きましたが、現状、自動化というゲームジャンルのエッセンスはすべてFactorioに詰まっているといえるタイトルであるため、自動化ゲーをやりたい場合はこれをやれば間違いはないでしょう。
まあ、最初に手を出すゲームとしては他にもう少しやさしい作品があるかもしれませんが。

FactorioがMinecraft+工業化MODを参考にしたのと同様に、Factorio以後のタイトルは全て少なからずこのゲームの影響を受けているのでは、と思います。

### Factory Town
[Factory Town](https://store.steampowered.com/app/860890/)は、2019年3月に早期アクセスとしてリリースされたゲームです。
資源の獲得や施設の稼働にワーカーというリソースが必要で、ワーカーの上限を増やすためには町を大きくしていく必要がある、という点では資源管理の要素を持ったゲームにも見えますが、基本的に詰みがないので単純に自動生産ラインの作成に没頭できるゲームといえます。

Factorioは2Dでしたが、Factory Townは3D視点で自動化を進めていきます。
また、プレイヤーキャラクターは存在しません。

独特な点として、「丸い物」を「水平または低いところへ」移動させることのできるシュート(Chute)の存在など、高低差を生かした生産ラインを組み上げることのできるシステムや、ゲーム中盤以降に登場する、施設とは別レイヤーで構築可能な魔術クリスタルの輸送ネットワークの存在を挙げられるかもしれません。

時間制限も詰みもなく、資源が非枯渇であることや、終盤に近づくまではエネルギーの概念も無いこと、それぞれの施設の稼働速度を知ることができることなどから、じっくりと考えて最も効率的な自動生産ラインを追求することのできるゲームであるといえます。
全体的に見てFactorioよりもとっつきやすそうな印象はあるのですが、とはいえゲームが進んでくるとその分多くの施設を用意しなければならず、またFactorioのように建設パターンを登録しておくこともできません(そもそも、地形が真っ平らではないので同じものを並べようとしてもそのスペースがないこともあります)。

まだUIが洗練されきっていないところもあり、足場を大量に設置するときなどは3D視点も相まって設置ミスをしやすい気もします。
また、生産施設が大量に並ぶ終盤になるに従ってCPUに対する負荷も上がるため、処理の最適化は自動化ゲーにとって重要な要素の一つですが、このゲームはどちらかというと終盤の処理負荷が高めな気がします。

とはいえ、全体的に生産ラインを組むことに集中できるゲームであり、また序盤の立ち上がりもシンプルかつ楽しいものなので、とっときやすさという面ではおすすめ度の高い作品です。

### Satisfactory
[Satisfactory](https://www.epicgames.com/store/ja/product/satisfactory/home)は、2019年3月に早期アクセスとしてリリースされたゲームです。ちなみにEpic専売です。
このゲームは3Dの一人称視点での自動化ゲームで、敵の存在はありますがプレイヤーの施設を攻撃するというようなことはなく、近づかなければ襲ってこない比較的無害な類のものです。

こちらも資源は非枯渇のため、一度設置した生産ラインは電力不足でも起きない限りアイテムを生産し続けます。
敵にぶつかると死んだりしますが、お腹が空いたり食料がなくなって詰みというようなこともないので、こちらもじっくり考えて自動化に取り組むことができます。
FPSという独自性を抜きにすれば、生産施設からコンベアを引いて次の加工施設に渡す、と、非常にオーソドックスな自動化の流れになっています。
まあ言うことはあまりないんですが、生産用の機械がかっこよくて、しかもFPSの視点で見れるので作った自動工場は非常にさまになります。
一方で生産機械はひとつひとつが巨大なのであまりばかでかい生産ラインを作るには向かず、また綺麗な3Dのグラフィックで機械を大量に並べることになるので、施設を作りまくった際のPCへの負荷は高い部類です。

現状ではそこまで勧められるほどの作品には感じませんが、悪くはないです。

### Autonauts
[Autonauts](https://store.steampowered.com/app/979120/)は2019年の10月にリリースされたゲームです。スコットランド製だそうです(タイトル画面に書いてありました)。
3D視点のゲームですが、Factory Townのような標高に関する要素は存在せず、2次元のマップ上で生産ラインを構築していきます。

このゲームの特徴は、「ベルトコンベアが存在しない」点にあるといえます。
これまでの自動化ゲーとは、基本的に施設から施設へとベルトコンベアを使ってアイテムを流すようなゲームでしたが、この作品ではすべての作業はプログラミングしたロボットにやらせます。
いわゆるビジュアルプログラミングですが、プレイヤーキャラクターの動作を記録させるといった形で命令を記述し、制御構文もwhileループただひとつで、if文もありません。

独自の、かつ簡潔なゲームシステムで自動化ゲーを実現しており、プログラミングとはいっても非常にシンプルなものであるため、遊びやすさはとても高いように思います。
ポップなゲームの雰囲気や段階的に目標が提示される方式と合わせて、自動化ゲー初心者やプログラミングに触れてみたい子供にとっても非常に良い作品となる可能性を秘めているように感じるのですが、その観点から見た場合、ゲーム内マニュアルの貧弱さが足を引っ張っているのが残念なところです。
アイテムが何に使えるかといった説明や一部の道具の使い方がゲーム内のどこにも記述されていないことがあり、もう少しゲームプレイに関する情報にアクセスしやすいようになっていれば文句なしに勧めることのできる作品ではあるのですが、現状では情報不足からいくつか罠があるかなといった感じです。

全ての作業をロボットにやらせるという作風は、隣接した加工設備と倉庫の間でアイテムを移動するだけでロボットが1体必要になる、という場面などでは、そのためだけにロボットをプログラムするのも手間ですし、ベルトコンベアが欲しいと感じることもあります。
しかし、逆に生産の規模が大きくなるに従って、これはむしろメリットとなるようにも感じてきています。

従来の自動化ゲーでは、生産施設のキャパシティが足りなくなった場合、その施設と同じものを増設して対処する必要がありました。
一方でロボットに作業をさせている場合、ある程度作業施設に余裕を持たせていた場合、単にロボットの数を増やすだけで対処が完了することもあります。
ベルトコンベアという設置物が存在しないことによってその流速がボトルネックになることはなく、ロボットであれば流速を上げるには数を増やせば足りるので、ある程度のスケーラビリティが自動的に確保されるような仕組みになっているのは良い点だといえます。
また、「この施設からこの施設へ」というプログラムをロボットに与えていると、命令が指しているのはあくまで施設というオブジェクトであるため、施設を丸ごと移設したり、拡張のために場所を少し変えたりしても、ロボットがそのまま動作を継続してくれるという嬉しい利点もあります。

あとは、プレイヤーができることはだいたい全部ロボットにやらせることができる、ということにはなっているのですが、厳密には「プレイヤーキャラクターが」できることはロボットでもできる、です。
どう違うのかという点ですが、建物の設置指示や建物の撤去、移動などは、一度建物メニューを開いてから行います。
そのため、これはプレイヤーキャラクターが行っている作業ではないので、プログラムに組み込むことができません。
一度作った施設などは、ノーコストで移動でき、撤去すると在庫として登録されるため、要求される素材コストを改めて支払う必要がありません。
そこで、よく使う保管庫や床などは予め作っておきたいと考えるのですが、建設予定地点に素材を持っていって建てることはロボットがやってくれても、実際の建設指示を出す作業の自動化はできません。
自動化ゲーというジャンルのゲームである故か、あらゆることを自動化したいと思ってしまうものではありますが、ここは割り切るところでしょう。

また、ゲームを進めているとロボットの数は3桁を越えてきますが、特に重いと感じることはありません。
ゲームそのもののクロックよりも遅いクロック周期でロボットが動作していることで、全体的に負荷がかかりにくいゲームシステムになっているのかもしれません。

発売されて間もないゲームということもあり、今のところ情報不足が足を引っ張ってしまっている点はあるのですが、ゲームそのもののはとてもよくできており、プレイしやすさも楽しさも高いレベルにあるように感じます。

## 自動化ゲーの抱える課題とこれからについて
Minecraftにおける工業化MODと呼ばれるものの中から自動化というゲーム要素が見出され、それがゲームのメインテーマたりうるものであったということを本記事では見てきました。
ここで、現在の自動化ゲーの抱えている課題について考えてみたいと思います。

### プレイヤーに対する動機付け
従来のMinecraftでは、自動化はプレイスタイルのひとつであり、手作業でアイテムを収集するような他のプレイスタイルと比較した際にアイテム自動生産の優位性が得られることも、プレイヤーが自動化施設を作ろうとするモチベーションの一部であったと考えることができます。
一方で、自動化を行わなければゲームを進行できない場合、手作業に対する優位性、というようなものはプレイヤーに対する報酬として働かなくなることに注意する必要があります。

「自分で生産ラインを作れた嬉しさ」「生産されたアイテムの数が増えていく喜び」「出来上がった生産ラインが稼働しているところを見る楽しさ」は自動化ゲーの面白さの重要な部分ではありますが、これだけでプレイヤーがずっと満足していられるかというと怪しいものです。
ポストFactorioの時代である2019年において、単に自動化をするだけのゲームを作った場合、どうしてもFactorio等の先発の自動化ゲーと比較される傾向にありますので、どのように差別化するかという点も重要になってくるのではないかと思っています。

### ゲームの進行に伴う作業コストの増大
自動化ゲーでは、ゲームが進むにつれて要求される生産工程が複雑になり、作らなければいけない施設の数も増加していきます。
その際、どうしてもプレイヤーが行う必要のある作業量が多くなる傾向にあります。

作りたい施設のデザインが決まっていても、それを作るために100回ぐらいクリックしなければならないとなると、ゲームの本質とは異なった点で作業ゲー感が出てしまい、自動化ラインを作る手が重くなりがちです。
それに関連して、一度生産ラインを設置してみたところ思ったように作れなかった、というようなこともありますから、撤去や移動などの形での修正が簡単にできるようになっていればいるほど、トライアンドエラーが容易になるため心理的抵抗が低減できるように感じます。

Factorioでは、予め用意したパターンを繰り返し並べることができるブループリントという形でこの問題に対する解答としているように感じます。
Factorioは生産能力を上げるために同じ生産施設を複数並列稼働させる、というケースが多いゲームですので、ゲーム性にマッチする形でプレイヤーの作業量をある程度低減することに成功しています。
また、Autonautsでは、そもそも同じ生産施設を複数ならべなくてもよい形のゲームシステムであることによって、ゲームが進行してもプレイヤーの作業量の増加がゆるやかになるようにデザインされています。
Factorioに類似したシステムを採用した自動化ゲーでは、たとえば「施設Aの時間あたりの出力速度は2」「施設Bの入力速度は3」であった場合、全体の作業効率を最大化させようとすると、施設Aを3基、施設Bを2基並べるというのが基本的な解決策となりますが、このように同じものを複数作らせる、というケースが多く発生するゲームほど作業コストの増大には注意を払わなければならないのかもしれなせん。

このような問題はUIの洗練度合いとも関連しており、出たばかりの新作ほどUIがこなれていないため、既存の作品と比較して操作性が良くないような印象を与えてしまう傾向にある点も懸念すべき事項であるように思います。

## おわりに
近年になって自動化というゲームジャンルが見出され、現在ではヒット作やそれを後追いする作品も出てきています。
つい先月に[Automation Empire](https://store.steampowered.com/app/1112790/)もリリースされたばかりですので、早いうちに購入してプレイしたいと思っています。

この先どうなるのかはわかりませんが、今のところ自動化を題材にしたゲームが充実しつつある時期にあるのではないかと思っており、今後の新タイトルの登場にも非常に期待しています。

とはいえ、キラータイトルにしてジャンルの完成形を示した感のある[Factorio](https://factorio.com/)の存在感は非常に大きく、Factorioに代わるゲームタイトルや、同ジャンルのゲームが複数生まれたことによって明らかとなってきた、自動化ゲー全般が抱える課題点について明確な解答を示しうる作品の登場も待たれているように感じます。

Factorioの次、といえるゲームがいつまでも現れず、新タイトルの目新しさがなくなってしまうとジャンル全体が飽きられていずれ新作が出なくなってしまうのでは、という懸念は今まさに感じているところではあります。
とはいえ、[Factory Town](https://store.steampowered.com/app/860890/)では「プレイヤーキャラクターがいない」ことや、[Satisfactory](https://www.epicgames.com/store/ja/product/satisfactory/home)でFPS視点での綺麗なグラフィックで巨大工場を眺める楽しみが見出されたこと、「ベルトコンベア」の概念を廃した[Autonauts](https://store.steampowered.com/app/979120/)の登場など、様々な試みは確実に行われているので、今後もこのジャンルの移り変わりに期待を持ちながらいろいろな自動化ゲーを楽しんでいければと思っています。
