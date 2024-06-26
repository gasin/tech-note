## 並行プログラミング入門 3章

### 概要
プリミティブな同期処理の仕組みの説明

### 個人的まとめ
- Compare and Swap
  - 条件を満たしていたら値を上書きする処理
  - 素直に実装するとアトミックではないが、アトミックな命令がコンパイラで用意されている
- Test and Set
  - Compare and Swap の1変数 bool 版
  - コンパイラでアトミックな命令が用意されている
- Load-Link / Store-Conditional
  - メモリのロック用の命令群
  - メモリのサイズごとに命令が用意されている
- mutex
  - 処理の排他実行のこと
  - Test and Set を用いるなどして実現される
  - スピンロック
    - ロックを取得できるまでループを繰り返す手法
    - アトミック命令を呼ぶ回数を減らした Test and Test and Set という手法がある
- セマフォ
  - ロックを取得できるプロセス数を複数で制限する手法
  - レースコンディションを防げないケースが多いので避けれるなら避けたほうが良い
  - キューのサイズが有限なチャネルの実装などに用いる
- 条件変数
  - プロセスの実行許可を管理する変数
  - 条件変数の更新・取得はロックを通じて行う
  - producer-consumer モデル
    - 共有データの生産と消費を分離すると見通しがよくなるというパターン
    - [参考](https://www.hyuki.com/dp/dpinfo.html#ProducerConsumer)
- バリア同期
  - プロセス間の同期の手段の1つ
  - 各プロセスが共有変数をインクリメントし、共有変数が閾値を超えたら実行を再開する
- Readers-Writer ロック
  - 読み込みだけなら複数存在しても競合しないということを活用したロック
  - 読み込みが支配的な場合には効果的で、DB でもよく使われる技術
- Rustの同期処理
  - 共有変数へのアクセスにはロックの取得が必須
  - ロックの開放は自動で行われる
- パン屋のアルゴリズム
  - ハードウェアがアトミック命令を提供しない場合の同期処理手法
  - アウトオブオーダ実行を防ぐためにはメモリバリアを使う
  - 取得したチケット番号が被った際には素直に待機するという部分がミソな気がしている
