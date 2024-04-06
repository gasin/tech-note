## SQLAlchemy + FactoryBoy

2023/12/28

### 背景
DB操作のテストを書く際にモデルを用意するのが非常に面倒くさい

特に、外部キー制約を満たすために、テスト対象のテーブルの依存元テーブルを再帰的に用意するのが面倒だった


### SQLAlchemy

Python の ORM ライブラリ

例えば relationship を用いることで手軽に外部キーを辿ったテーブル操作ができる

### FactoryBoy

主にテスト用に、モデルを簡単に生成するためのライブラリ

例えば変数が多いモデルについて、一部の変数のみ手動で設定して、残りの変数はデフォルト値にするみたいな使い方ができる

### SQLAlchemy + FactoryBoy

これ https://factoryboy.readthedocs.io/en/stable/orms.html#sqlalchemy

SubFactory や RelatedFactory という機能があり、外部キー制約について、依存元と依存先のモデルを自動で生成するように設定できる

例えば、以下のような外部キー制約があるモデルがあった時、`C`を設定するだけで`A,B,D,E`を自動生成できる
```
A <- B <- C <- D
          ^
          + -- E
```

SubFactory や RelatedFactory の引数で生成する際の値も制御できるので、モデル間で満たしてほしい制約なども指定できる

### 細かいメモ
- `1:n`の関係について、`1`側から複数の`n`側のモデルを生成することもできるが、SubFactoryList や RelatedFactoryList だとモデルごとの引数は制御できなさそうだった
  - post_generation にてループ文などを用いて生成すれば解決する
- 深いモデルの引数は`C__B__arg`みたいに`__`で辿れば上書きできる
  - メソッドの引数も同様の形で挿入できる
- session 管理を単体テストの外に丸投げできて嬉しい
  - https://factoryboy.readthedocs.io/en/stable/orms.html#managing-sessions
- FactoryBoy を使うと mypy がうまく使えなくて VSCode 上での補間もあまり効かなくなる
  - この行は型を無視しますをあちこちでしていて気分がよくない
