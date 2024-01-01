## LightGBMTunerCV

### 背景
社内イベントに向けて LightGBM に触ってみたのだが、今時はかなり環境が整備されていた

具体的には、交差検証法やハイパーパラメータチューニングがほぼ0コストで走るようになっていて驚いた

### LightGBM
> LightGBM is a gradient boosting framework that uses tree based learning algorithms  
https://lightgbm.readthedocs.io/en/stable/

Dart とか Random Forest もサポートしてて、オプションがたくさんある

テーブルデータの時はとりあえず LightGBM 使えばいいらしい（諸説有）

### 交差検証法
過学習を防ぐために使うもの

昔に大学で習って実装した気がしないでもない

### ハイパーパラメータチューニング
LightGBM にはパラメータが多く、何も設定せずともデフォルト値が振られるが、精度を上げたいならチューニングする必要がある

そうは言われても初学者には、何をどうチューニングすればいいのかわからないものだが Optuna がよしなにチューニングするライブラリを提供してくれている

> LightGBM Tuner は「重要なハイパーパラメータから順に最適なハイパーパラメータをチューニングする」という経験則をコードに落とし込み、実装したモジュールです  
https://tech.preferred.jp/ja/blog/hyperparameter-tuning-with-optuna-integration-lightgbm-tuner/

実行ログを見るとどのパラメータを何回チューニングしてるのかも大体わかる

### Titanicでの利用例

全体のnotebook https://www.kaggle.com/code/gascin/titanic-lightgbm

lightgbm の optuna 拡張のノリで import を書き換えて
```
import optuna.integration.lightgbm as lgbm
```
lgbm の設定を書いて
```
lgbm_params = {
    'application': 'binary',
    'metric': 'binary_logloss',
    'verbosity': -1,
}
```
Cross Validation 及び Tuning の設定などを書いて
```
clf = lgbm.LightGBMTunerCV(
    lgbm_params,
    train_data,
    return_cvbooster=True,
    stratified=True,
    folds=KFold(n_splits=5),
    seed=42,
    callbacks=[lgbm.early_stopping(100)],
)
```
走らせると
```
clf.run()
```
Tuning されたハイパーパラメータが得られ
```
clf.best_params
> {'application': 'binary',
>  'metric': 'binary_logloss',
>  'verbosity': -1,
>  'feature_pre_filter': False,
>  'lambda_l1': 1.0293220258381378e-08,
>  'lambda_l2': 0.0001601475782585461,
>  'num_leaves': 31,
>  'feature_fraction': 0.8,
>  'bagging_fraction': 1.0,
>  'bagging_freq': 0,
>  'min_child_samples': 20}
```
簡単に予測もできる
```
model = clf.get_best_booster()
train_pred_prob = np.mean(model.predict(train_df, num_iteration=model.best_iteration), axis=0)
train_pred = np.round(train_pred_prob).astype(int)
print('Accuracy score = \t {}'.format(accuracy_score(y, train_pred)))
> Accuracy score = 	 0.8496071829405163
```
すごい

## メモ
- モデルのハイパーパラメータチューニングは交差検証法によるモデル学習を1stepとしているので、過学習するのではという気はしている
  - ハイパーパラメータだと実データからレイヤが少し離れているのであまり影響がないのかも？
  - train で多少過学習しても test と相関がとれている範疇ならよい？
