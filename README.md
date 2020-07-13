# データベースの練習

自分のために雑記としてメモを置いておく。

## 参考図書
[SQL 第2版　ゼロからはじめるデータベース操作](https://www.shoeisha.co.jp/book/detail/9784798144450)

## 環境構築

dockerでmysqlの環境構築を行った。
最初はpostgresで環境構築を行おうとしたがうまくいかなかった。。
postgresで環境構築しようとしたときの残骸を`_docker-compose.yml`として置いておく。

`docker-compose up`を実行すると、カレントディレクトリに`mysql`ディレクトリが作成されてデータが共有される。

``` BASH
# 起動方法
$ docker-compose up

# 別のコマンドプロンプトを立ち上げて
# mysqlコンテナに入る
$ docker-compose exec mysql bash

# mysqlサーバーにログインする
$ mysql -u root -p
password

```
