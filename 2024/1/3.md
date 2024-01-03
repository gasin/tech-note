## Google Cloud Databases

### 背景
GCP のデータベースサービスで知らないものもいくつかあるため、雰囲気を把握することで、サービスの選定ができるようにしたい

https://cloud.google.com/products/databases?hl=ja に乗っているサービスについて雰囲気を把握する（細かい技術詳細はまた次の機会に...）

### Cloud SQL
- 触ったことがあるサービス
- いわゆる普通の RDB
  - コスパもよさそうで単純なので、これで問題なければ RDB の文脈ではこれを使えばよさそう
- インスタンスの CPU やメモリを設定することで垂直スケーリングは簡単に行えるが、水平スケーリングは単純ではなさそう
  - https://github.com/GoogleCloudPlatform/community/blob/master/archived/horizontally-scale-mysql-database-backend-with-google-cloud-sql-and-proxysql/index.md
  - Proxy SQL を使えば水平スケーリングもできるらしいが、少なくとも単純ではなさそう

### AlloyDB for PostgreSQL
- 触ったことないサービス
- [Ggen のサイト](https://blog.g-gen.co.jp/entry/alloydb-for-postgresql-explained)及び[公式のブログ](https://cloud.google.com/blog/products/databases/alloydb-for-postgresql-intelligent-scalable-storage?hl=en)がわかりやすい
  - プライマリインスタンスとリードプールインスタンスに分かれていて、後者は水平スケーリングができる仕組みになっている
    - log 形式でのストレージに書き込んで、それを LPS(log processing service)が DB に登録する仕組みになっている
  - AlloyDB カラムナエンジンによって、OLAP としても利用可能
- 表側からは PostgreSQL でいじれるようなので、Cloud SQL でパフォーマンスが問題になった時は検討するとよさそう
  - インスタンスの設定などが Cloud SQL と比べると少し面倒（？）なのと、少し高価という欠点はありそうだった

### Cloud Spanner
- 触ったことないサービス
- [参考記事](https://developers.cyberagent.co.jp/blog/archives/39843/)
  - 外部キーに加え、インタリーブという概念を使うことで、局所性により join が高速に行える
  - STRUCT の扱いといい、使い勝手としては ORM での relationship に似てそう
  - シャーディングの仕組みより、インデックスを散らすといいらしい
- マルチリージョンであったり、高いスケーラビリティが必要な場合に使うとよいみたい
  - とはいえややこしそうなので、Cloud SQL だとスケーラビリティやパフォーマンスに問題があり、AlloyDB でも厳しいみたいなときに使うとよさそう？
- [whitepaper](https://cloud.google.com/spanner/docs/whitepapers/life-of-reads-and-writes?hl=ja)
  - TrueTime など色々な技術を使っているらしい（また今度読む）


### BigQuery
- 使ったことあるサービス
- スキーマを定義してデータを格納する形で、SQL文によるクエリを発行できる
- 超有名どころの OLAP ツールということで、可視化や分析用のツールがたくさん存在している
  - 整形後の分析用のデータはとりあえず BigQuery に入れておけ感がある
- [中身の話](https://cloud.google.com/blog/products/bigquery/bigquery-under-the-hood?hl=en)
  - Borg, Colossus, Jupiter, Dremel などの技術を使っているらしい
  - またいつか読む

### Bigtable
- 使ったことないサービス
- [key-value 形式の DB で高いスループットとスケーラビリティを実現するサービス](https://cloud.google.com/bigtable/docs/overview?hl=ja)
  - SSTable(猪本で学んだ)とか、Colossus(BigQueryでも出てきた)などが見える
- RDB のように色々クエリを叩けるわけではないので、あまり加工してないログのようなデータをあとから見る用にとりあえず突っ込む用途で使うとよさそうに見える
  - 行キーという概念があるので、適当な log ファイルに垂れ流すよりは扱いやすい
  - BigQuery よりは可用性が高いのでリアルタイム処理に向いていそう

### Firestore
- 使ったことないサービス
  - Firebase 社は 2014年に Google に[買収されてた](https://en.wikipedia.org/wiki/Firebase)
- NoSQL のドキュメント型 DB
  - Document DB VS RDB の話は猪本で読んだ
  - MongoDB Atlas というサービスも GCP 上に存在している
  - クエリは SQL チックに書くことができる
- フルマネージドサービスなので、サーバレスで利用可能
  - Cloud SQL とかその他 DB のように、インフラの設定をしないでいい
- モード
  - [native と Datastore がある](https://cloud.google.com/datastore/docs/firestore-or-datastore?hl=ja)
  - Datastore は Firestore の前任の NoSQL サービスで、互換性のために Firestore サービスの中にモードとして存在していそう
  - その意味で言うと色々比較されているが native を今後は選べばよい？

### Memorystore
- 使ったことないサービス
- Redis や Memcached のようなインメモリのデータベースを使うためのサービス
- 可用性を高く保ちつつ、簡単にアプリ側から触れるのがメリット
  - インスタンスを立て続けるとかなり料金が高い
  - キャッシュや一時的なデータ置き場のような用途として用いるのがよさそう

### まとめ
- 分析用途ではとりあえず BigQuery を使い続けていればよさそう
- RDB を使う際は、要件に応じて Cloud SQL -> AlloyDB -> Cloud Spanner を用いるとよさそう
  - 後ろに行くほど使うのが大変になりそうという印象
- ドキュメント指向 DB を使いたい場合は Firestore (native)を使えばよさそう
  - join 操作をあまり必要としなくて、塊でデータを取得するのが主な場合はドキュメント指向が向いているという淡い認識をしている
- Bgtable はログのようなデータの保存にスケーラビリティとリアルタイム性が求められているときに使うとよさそう
- Memorystore はキャッシュのような一時的な保管場所の用途に使うとよさそう
