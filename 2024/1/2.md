## データ指向アプリケーションデザイン 4章

### 概要
データのエンコーディング方式と送信方法の整理

### 気になったことメモ
- 互換性
  - エンコーディング方式において、スキーマ変わったら互換性なんてあるわけないみたいな気持ちがあったが、考慮されているのだなと思った
  - 基本は optional にすることで互換性に対応するという思想で、DBのマイグレーションに似ているなと思った
- XML/JSON スキーマ
  - XML はほぼ使ってないのでともかく、JSONスキーマを知らなかった
    - JSON 形式でのスキーマファイル自体は Big Query で触ったことあったが同じことかも
    - https://cloud.google.com/bigquery/docs/schemas?hl=ja#specifying_a_json_schema_file
  - かなり詳細に色々指定できそう
    - https://json-schema.org/learn/getting-started-step-by-step
    - JSON データについて、ドキュメント代わりに用いると便利かも？
- Protocol Buffer
  - しばしば耳にするもののよく知らなかったものだが、フォーマットの形式とのこと
  - https://protobuf.dev/overview/
    - メリットデメリットが書かれていて嬉しい
    - [Envoy Proxy](https://www.envoyproxy.io/)
      -Protocol Buffer の利用例として上記サイトで挙げられている
      - Envoy はサービスプロキシで、サービス間の通信のあれこれを担当するものらしい
        - Ngix と同じ立ち位置（使ったことないが...）
      - 調べると、Cloud Run のサイドカーコンテナの活用例としてよくあげられている
- Avro 
  - 既知のスキーマのデータを極力無駄なく格納したもの
  - スキーマの変更によってデータが変わりにくいのがメリットとしてあげられていて、なるほどなとなった
  - https://cloud.google.com/bigquery/docs/loading-data-cloud-storage-avro?hl=ja#advantages_of_avro
    - BigQuery にデータを読み込むのにおすすめされている
- スキーマの伝達方法
  - 意識したことがなかった
  - データ内に埋め込む方法、別データとして保存する方法、通信確立時に共有する方法が紹介されている
- RPC
  - 普及しているからこそなのか、デメリットが詳細に書かれている
  - ローカルのサービス内においては有効な選択肢ではある
    - ただ、REST に比べて扱いにくい（設定がめんどい、データが見にくい）などあるから、パフォーマンスが重要でなければ使わなくてもよさそう
      - https://aws.amazon.com/jp/compare/the-difference-between-grpc-and-rest/
- [Pub/Sub](https://cloud.google.com/pubsub/docs/overview?hl=ja)
  - GCP におけるメッセージパッシングシステム
    - イベントを Dataflow を通じてデータベースに入れる例どがよく見つかる
  - 使ったことはないが、サービス間で1対多や多対多の非同期メッセージをやり取りする場合などは便利そうという認識をしている
- SOAP
  - 完全に初見だったが、REST とよく比較される通信プロトコルらしい
  - セキュリティや信頼性の面で保証があるのは企業にとってはうれしいが、そのせいで重いらしい
    - https://www.redhat.com/en/topics/integration/whats-the-difference-between-soap-rest1:w
- アクターモデル
  - [並列計算の数学的モデル](https://ja.wikipedia.org/wiki/%E3%82%A2%E3%82%AF%E3%82%BF%E3%83%BC%E3%83%A2%E3%83%87%E3%83%AB)
  - こういったモデル周りの話の齧りは大学で少し教わった気もするが、ほぼ何もわからん
  - 分散アクターフレームワークはメッセージブローカーとアクターモデルを統合したものと書かれている
    - 例として Akka があげられている
      - https://developer.lightbend.com/guides/akka-quickstart-scala/
      - Actor を定義し、それぞれがメッセージによるイベント駆動で非同期に動作する仕組みみたい
