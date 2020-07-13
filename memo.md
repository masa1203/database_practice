# SQL操作メモ

## 起動方法

``` BASH
# 起動方法
$ docker-compose up

# コンテナに入る
$ docker-compose exec mysql bash

# mysqlサーバーに入る
$ mysql -u root -p
> password

# データベースを指定して入る場合
$ mysql -u root -p database_name

# mysqlからログアウトする
$ exit

```

## データベースの操作

``` bash
# データベースの一覧
mysql> show databases;

# データベースンの作成方法
mysql> CREATE DATABASE shop;

# 使用するデータベースの指定
mysql> use shop;

# 使用しているデータベースの確認
mysql> select database();

# データベースの削除
mysql>
```

## RDBMSとは

- 加工したデータの集まり
    - データベース(DB)

- データベースを管理するコンピュータマネジメントシステム
    - データベースマネジメントシステム(Database Management System, **DBMS**)

- 列と行からなる2次元表の形式でデータを管理する形式のデータベース
    - リレーショナルデータベース(Relational Database, **RDB**)
    - 構造化問い合わせ言語(Structured Query Language, **SQL**)という専用の言語を用いてデータを操作する

- リレーショナルデータベースを管理するコンピュータマネジメントシステム
    - Relational Database Management System, **RDBMS**
        - クライアント/サーバー型のシステム
        - 1つのデータベースの中に複数のテーブルを保存することができる
        - 必ず行単位でデータを読み書きする

## SQLとは

- データベースのデータを効率よく操作するために開発された言語

- ISO(国際標準化機構)で定められた標準規格があり、これに準拠したSQL言語
    - 標準SQL
        - 過去にはRDBMSによって方言があったが、現在は標準SQLが進められている

- 1つの文(SQL文)として操作内容を記述する
    - 命令の種類により、3つに分類される
    - 90％はDMLを操作する
        - データを格納する入れ物であるデータベースやテーブルを作成したり削除したりする**データ定義言語(DDL)**
            - CREATE, DROP, ALTER
        - テーブルの行を検索したり変更したりする**データ操作言語(DML))**
            - SELECT, INSERT, UPDATE, DELETE
        - データベースに対して行った変更を確定したり取り消したりする**データ制御言語(DCL)**
            - COMMIT, ROLLBACK, GRANT, REVOKE

- 大文字、小文字は区別されないが、データについては区別される
- 名前に日本語は使えない,

## テーブルの作成

``` BASH
# テーブルの作成
create table Shohin
(shohin_id  CHAR(4) NOT NULL,
 shohin_mei VARCHAR(100) NOT NULL,
 shohin_bunrui VARCHAR(32) NOT NULL,
 hanbai_tanka INTEGER   ,
 shiire_tanka INTEGER   ,
 torokubi     DATE      ,
 PRIMARY KEY (shohin_id));

# テーブルの詳細
describe Shohin;

# ShohinのところをSohinという名前にしてしまった
# テーブルの名前を変更したい
# postgres, oracle
ALTER TABLE Sohin RENAME TO Shohin;
# mysql
RENAME TABLE Sohin to Shohin;
```

### データ型

- INTEGER型
    - 整数。少数はなし。

- CHAR型
    - 固定長文字列
        - CHAR(10)なら'ABC'というデータを入れても'ABC       '(10文字)という長さになる。
        - 文字数かバイト長である場合がある

- VARCAHR型
    - 可変長文字列
        - CAHR(10)に'ABC'を入れても'ABC'のままはいっている

- DATE型
    - 日付(年月日)を入れる列に指定


### 制約

テーブルのいカラムにはデータ型と制約を追加する。
- NOT NULL
    - 無記入が禁止。必ず何かはいっている。

- PRIMARY KEY
    - 主キーの追加。特定のデータを指定するときに使う列の組み合わせのこと。

## テーブル操作

``` BASH
# テーブルを削除する
DROP TABLE Shohin;

# 列を追加する
ALTER TABLE <TABLE> ADD COLUMN (<列の定義>, <列の定義>, ...);
ALTER TABLE Shohin ADD COLUMN (
    Shohin_mei_kana VARCAHR(20)
    );

# 列を削除する
ALTER TABLE <TABLE> DROP COLUMN <列名>;
ALTER TABLE Shohin DROP COLUMN Shohin_mei_kana;

# 列名を削除する
ALTER TABLE <TABLE> RENAME COLUMN <列名> TO <新列名>;

```

## データの追加

``` BASH
# データの登録
# BEGIN TRANSACTION; # postgres
START TRANSACTION; # mysql

INSERT INTO Shohin values ('0001', 'Tシャツ', '衣服', 1000, 500, '2009-09-20');
INSERT INTO Shohin values ('0002', '穴あけパンチ', '事務用品', 500, 320, '2009-09-11');
INSERT INTO Shohin values ('0003', 'カッターシャツ', '衣服', 4000, 2800, NULL);
INSERT INTO Shohin values ('0004', '包丁', 'キッチン用品', 3000, 2800, '2009-09-20');
INSERT INTO Shohin values ('0005', '圧力なべ', 'キッチン用品', 6800, 5000, '2009-01-15');
INSERT INTO Shohin values ('0006', 'フォーク', 'キッチン用品', 500, NULL, '2009-09-20');
INSERT INTO Shohin values ('0007', 'おろし金', 'キッチン用品', 880, 790, '2008-04-28');
INSERT INTO Shohin values ('0008', 'ボールペン', '事務用品', 100, NULL, '2009-11-11');

COMMIT;

```

## データの選択

SELECT文を使う。Select文でデータを検索し、取り出すことを**問い合わせ** や **query** といったりする。

- Select文は**句(clause)**にわかれる
    - SELECT句
        - 結果の列はSELECT句と同じ順番で並ぶ
    - FROM句
- 句ごとに改行するとわかりやすいのでこれを原則とする
- 空行を挟むと正しく実行されない

``` BASH
SELECT <列名>, ...
    FROM <テーブル>;
```

``` BASH
# 全ての列を出力
SELECT *
    FROM Shohin;

# 3つの列を出力
mysql> SELECT shohin_id, shohin_mei, shiire_tanka
    ->     FROM Shohin;
+-----------+-----------------------+--------------+
| shohin_id | shohin_mei            | shiire_tanka |
+-----------+-----------------------+--------------+
| 0001      | Tシャツ               |          500 |
| 0002      | 穴あけパンチ          |          320 |
| 0003      | カッターシャツ        |         2800 |
| 0004      | 包丁                  |         2800 |
| 0005      | 圧力なべ              |         5000 |
| 0006      | フォーク              |         NULL |
| 0007      | おろし金              |          790 |
| 0008      | ボールペン            |         NULL |
+-----------+-----------------------+--------------+
8 rows in set (0.00 sec)

# 列に別名をつける
# 日本語の場合はダブルクウォーテーション
SELECT shohin_id as id,
        shohin_mei as "名前",
        shiire_tanka as tanka
FROM Shohin;
+------+-----------------------+-------+
| id   | namae                 | tanka |
+------+-----------------------+-------+
| 0001 | Tシャツ               |   500 |
| 0002 | 穴あけパンチ          |   320 |
| 0003 | カッターシャツ        |  2800 |
| 0004 | 包丁                  |  2800 |
| 0005 | 圧力なべ              |  5000 |
| 0006 | フォーク              |  NULL |
| 0007 | おろし金              |   790 |
| 0008 | ボールペン            |  NULL |
+------+-----------------------+-------+
8 rows in set (0.01 sec)

# 定数の出力

```


重複を除いた抽出
select distinct shohin_bunrui from Shohin;

複数のカラムを選択した場合は列の組み合わせがまったく同じ行が1つの行にまとめられる
select distinct shohin_bunrui shiire_tanka from Shohin;

where句で条件式を追加する
where句は必ずfrom句の後に追加する。
select shohin_mei from Sohin
where shohin_bunrui = "衣服";

GROUP BY句
select shohin_bunrui, COUNT(*)
from Shohin
group by shohin_bunrui
;

実行順序
select⇒
