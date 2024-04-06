# データ指向アプリケーションデザイン 2章

### 概要
主流なデータモデルとクエリ言語の紹介

特に、リレーショナルモデル、ドキュメントモデル、グラフモデルの特徴と比較

### 気になったことメモ
- インピーダンスミスマッチ
  - application と DB の間で不格好なモデル変換が必要になる
  - 実際、虚無みたいな変換をレイヤの間で噛ませる必要があってテストが面倒と感じることがよくある
- SQL で JSON 及び XML が使える
  - JSON は知ってたが XML は知らなかった（JSON 使えれば十分ではと思うが...？）
  - とはいえ、JSON や XML をテーブルに入れると正規化とかよくわからなくなりそう
- MongoDB
  - よく聞く NoSQL 筆頭だけど使ったことはない
  - [lookup で外部キーを指定すれば](https://www.mongodb.com/docs/v5.0/core/views/join-collections-with-view/) join もできる
  - [BSON](https://www.mongodb.com/basics/bson#:~:text=BSON)
    - MongoDB 内で使われている JSON のバイナリ版
    - 多分 CSV と Parquet、Avro の関係みたいなもの
  - MongoDB Atlas
    - DBaaS (GCPでもサポートされている)
    - インフラ周りの設定を楽にしてくれるぐらいの認識しかない
      - 確かにわかりやすそう https://qiita.com/n0bisuke/items/4d4a4599ee7ce9cf4fd9
- MapReduce
  - 1章で Hadoop 調べてた時にも出てきたワード
    - 詳しくは後の章でやるらしいから深追いはせず
  - MongoDB だと [aggregation pipeline](https://www.mongodb.com/basics/aggregation-pipeline) という選択肢もある
    - match -> group -> limit みたいな標準的な流れがサポートされている
- プロパティグラフ
  - 普通にグラフを実装するとこれに類するものをよく用いがち
  - SQL で再帰を使ってのグラフクエリの話は何度か聞いたことがあるが、やりたくないなという気持ち
- Datalog
  - 大学で学んだ Prolog の古の記憶
    - こうして見ると、確かにグラフモデルへのアプローチとしてよさげに見える
  - Cypher、SPARQL よりもとっつきやすそうに見えるのは単に慣れの問題かも
- AuraDB (AuraDS)
  - graph model database でググると真っ先に出てくるもの
  - Neo4j という会社は確かにグラフ系の検索で何度か見かけていた気がする
  - Cypher でクエリを記述できる
  - 価格が高めに見えるのは気になる

### 所感
- 章内にも書いてあるが、リレーションモデルとドキュメントモデル（例えば PostgreSQL と MongoDB）のそれぞれが互いの機能を組み込んでるから選択が難しそう
  - 根本の仕組みは変わってないはずだから、素直に選択するのでよさそう
  - とりあえず RDB にしとけという言及を前に X で見かけた気がする（要出典）
- 結局のところ、グラフ操作は極力DBじゃなくてアプリ側で行いたいという気持ち
