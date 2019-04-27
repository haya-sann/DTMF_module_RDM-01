RDM-01　-DTMF decoder module- manual
===

## RDM-01について
- **開発背景**

　以前スマートスピーカーと連携したガジェットをつくりたい！そう思ったものの、サーバーサイドの開発もわからず、どうやって実現したらいいのわかりませんでした。
 自分の場合多くの学びが必要で、実際にスマートスピーカーとガジェットの連携ができるようになるのに多くの時間がかかりました。
 
 このような経験を通し、スマートスピーカーをとハードウェア連携は未だ敷居が高く、システムを作ることが大変だとわかってきました。
 
 そこでRDM-01というモジュールを開発することにしました。
 
 RDM-01はスマートスピーカーやスマートフォンをユーザーインターフェースとしたオリジナルガジェットの開発を容易にするためのモジュールです。
 このモジュールの役割はデジタル処理ができる任意の音を使い、ローカル通信を実現する橋渡しをするところにあります。
　音をデジタル処理することで、スピーカーの音を信号処理の一部として利用することができ、結果としてwebの開発経験がなく、Arduino等の組み込みを主にされていた方でも、　web側の開発をすることなく、スマートスピーカーをユーザーインターフェースとして使うことができるようになります。
 
 
- **動作原理**

　RDM-01はMT8870というICを利用し、DTMF(Dual-Tone Multi-Frequency)と呼ばれる、電話のプッシュ回線で利用される「ピポパ」という音を解析します。
 
この音は特定の２種類の周波数の合成波(１６種類あります)で、２種類の音をICが解析することで割り当てられた特定の数字として認識し、その結果を４つのピンのHIGH、LOWで表現します。

[MT8870 datasheet](https://www.microsemi.com/document-portal/doc_download/127041-mt8870d-datasheet-oct2006)

 
 ## 使い方
 **1. 信号の取得方法**
  
  RDM-01は5つのGPIOとVCC、GNDの計７つのピンを使います。

 5つのGPIOのうち4つは、16種類あるDTMF信号の、何番なのかを表現するためにあり、1つは取得できたことを伝えるためにあります。
  
![RDMdetal](http://www.rino-make-fun.com/wp-content/uploads/2019/01/RDM-01View-1.png)
![Closeup PinOut](DTMF_module_RDM-01/PCB/PINOUT.jpg)
 
 実際の基板の画像と照らし合わせて説明すると、以下の機能を示します。
 - +5V -> ５Vピン　
 - GND -> GNDピン
 - Q1 -> 4bit表現のうち**0bit**を表現するピン
 - Q2 -> 4bit表現のうち**1bit**を表現するピン
 - Q3 -> 4bit表現のうち**2bit**を表現するピン
 - Q4 -> 4bit表現のうち**3bit**を表現するピン
 - StD -> DTMF信号が取得できたときに、立ち上がるピン。ArduinoではInterruptPinを利用すると便利です。

**このモジュールは５V駆動になります。当然出力も5Vとなるので、3.3V等で使用する場合は、分圧しマイコンの保護をしてください。**
また右上の大きな穴はGNDに接続されており、GNDの強化や筐体に取り付けるなどに活用してください。


そして、実際に取得できる１６種類の音は以下の表で示されます。

![DTMFDetal](http://www.rino-make-fun.com/wp-content/uploads/2019/01/dtmfDetail.png)

Arduino等のマイコンでQ1~Q4に対応したピンをReadすると、表の通りの結果を得ることができます。
連続してDTMFの音を出力することで、送信できるデータ量を増やし、より複雑な処理をすることが可能になります。

**2. 有線、無線通信の切り替え**

RDM-01は有線通信、無線通信のどちらもできるように設計しました。

- 有線通信 -> 3.5mmのステレオイヤホンジャックを使用します。スマートスピーカー等と接続してください。
- 無線通信 -> モジュールに実装されている無指向性コンデンサマイクで音を取得します。

有線、無線、それぞれメリットデメリットがあり、有線通信は距離が離れていても確実に信号を送ることができますが、1度に多数のデバイスを操作することはできません。
それに対して無線は、１対Nの通信が可能になります。しかし、スピーカーの特性や基板の固定方法、向きなどに依存して、取得しにくい音がでてくることがあります。

色々と試していただければと思います。

そして通信方法の切り替え方法ですが、基板に実装されているスライドスイッチを切り替えて行います。

**初期ロットはミスにより、実際のスイッチの位置と、機能を示す印刷の内容が入れ違いになっています。正しくは下の図の通りです。**

 ![switchDetal](http://www.rino-make-fun.com/wp-content/uploads/2019/01/switchDetailda-01.jpg)

## サンプルコード
このリポジトリではArduinoを使ったサンプルコードを提供しています。

サンプルコードは下記ピン設定で実行されています。動作に関してはソースコードをご確認ください。
 - +5V -> ５Vピン　
 - GND -> GNDピン
 - Q1 -> 6番ピン
 - Q２ -> 5番ピン
 - Q３ -> 4番ピン
 - Q４ -> 3番ピン
 - StD -> 2番ピン(割り込み0番ピン)
 
 ![Arduino](http://www.rino-make-fun.com/wp-content/uploads/2019/01/IMG_2194.jpg)
 
## その他
RDM-01をすぐに使って試せるように、Amazon Echoシリーズで使用するAlexaスキルを開発し、リリースしております。
ロボットとして遊べるようにすでにプリセットされたコマンドもありますので、ご活用ください。

またDTMFの信号は特殊な音ではなく、フリーで生成することが可能です。具体的にはAudacity等の音声ソフトウェアで簡単に作ることができます。

[Alexaスキル”音リモコン”](https://www.amazon.co.jp/rino-products-%E9%9F%B3%E3%83%AA%E3%83%A2%E3%82%B3%E3%83%B3/dp/B07KRBKFFJ)

[スキル説明　& コマンドリスト](http://www.rino-make-fun.com/2018/11/22/alexa-skill-%E9%9F%B3%E3%83%AA%E3%83%A2%E3%82%B3%E3%83%B3/)

[Audacity](https://www.audacityteam.org/)

## 購入方法
スイッチサイエンス様にて販売を行なっております。
[RDM-01販売ページ](https://www.switch-science.com/catalog/5381/)

また、手売り販売も行なっております。
イベントなどでお会いした時に声をかけていただければと思います。

## Licence
This software is released under the MIT License, see LICENSE.

## Authors
Norippy in rino products(http://www.rino-make-fun.com/)

