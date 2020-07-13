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
+-----------+-----------------------+--------------------+--------------+--------------+------------+
| shohin_id | shohin_mei            | shohin_bunrui      | hanbai_tanka | shiire_tanka | torokubi   |
+-----------+-----------------------+--------------------+--------------+--------------+------------+
| 0001      | Tシャツ               | 衣服               |         1000 |          500 | 2009-09-20 |
| 0002      | 穴あけパンチ          | 事務用品           |          500 |          320 | 2009-09-11 |
| 0003      | カッターシャツ        | 衣服               |         4000 |         2800 | NULL       |
| 0004      | 包丁                  | キッチン用品       |         3000 |         2800 | 2009-09-20 |
| 0005      | 圧力なべ              | キッチン用品       |         6800 |         5000 | 2009-01-15 |
| 0006      | フォーク              | キッチン用品       |          500 |         NULL | 2009-09-20 |
| 0007      | おろし金              | キッチン用品       |          880 |          790 | 2008-04-28 |
| 0008      | ボールペン            | 事務用品           |          100 |         NULL | 2009-11-11 |
+-----------+-----------------------+--------------------+--------------+--------------+------------+
8 rows in set (0.00 sec)

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

select '商品' as mojiretsu, 38 as kazu, '2009-02-24' as hizuke,
        shohin_id, Shohin_mei
from Shohin;
+-----------+------+------------+-----------+-----------------------+
| mojiretsu | kazu | hizuke     | shohin_id | Shohin_mei            |
+-----------+------+------------+-----------+-----------------------+
| 商品      |   38 | 2009-02-24 | 0001      | Tシャツ               |
| 商品      |   38 | 2009-02-24 | 0002      | 穴あけパンチ          |
| 商品      |   38 | 2009-02-24 | 0003      | カッターシャツ        |
| 商品      |   38 | 2009-02-24 | 0004      | 包丁                  |
| 商品      |   38 | 2009-02-24 | 0005      | 圧力なべ              |
| 商品      |   38 | 2009-02-24 | 0006      | フォーク              |
| 商品      |   38 | 2009-02-24 | 0007      | おろし金              |
| 商品      |   38 | 2009-02-24 | 0008      | ボールペン            |
+-----------+------+------------+-----------+-----------------------+
8 rows in set (0.00 sec)

```

### distinctキーワード

- 重複をのぞいて選択する
    - 先頭の列名の前にしかかけない
    - 複数列指定する場合は複数列のまったく同じのデータを重複とする

``` BASH
# 重複を除いた抽出
# distinctキーワードを使う
select distinct shohin_bunrui
from Shohin;
+--------------------+
| shohin_bunrui      |
+--------------------+
| 衣服               |
| 事務用品           |
| キッチン用品       |
+--------------------+
3 rows in set (0.00 sec)

# nullも1種類のデータとして扱われる
select distinct shiire_tanka
from Shohin;
+--------------+
| shiire_tanka |
+--------------+
|          500 |
|          320 |
|         2800 |
|         5000 |
|         NULL |  # NULLも消えずに残る
|          790 |
+--------------+
6 rows in set (0.01 sec)

# 複数の列指定でdistinctを指定する場合は
# まったく同じ行が1つの行にまとめられる
select distinct shohin_bunrui, torokubi
from Shohin;
+--------------------+------------+
| shohin_bunrui      | torokubi   |
+--------------------+------------+
| 衣服               | 2009-09-20 |
| 事務用品           | 2009-09-11 |
| 衣服               | NULL       |
| キッチン用品       | 2009-09-20 |
| キッチン用品       | 2009-01-15 |
| キッチン用品       | 2008-04-28 |
| 事務用品           | 2009-11-11 |
+--------------------+------------+
7 rows in set (0.00 sec)

# distinctを指定しない場合
select shohin_bunrui, torokubi
from Shohin;
+--------------------+------------+
| shohin_bunrui      | torokubi   |
+--------------------+------------+
| 衣服               | 2009-09-20 |
| 事務用品           | 2009-09-11 |
| 衣服               | NULL       |
| キッチン用品       | 2009-09-20 |  # 重複
| キッチン用品       | 2009-01-15 |
| キッチン用品       | 2009-09-20 |　# 重複
| キッチン用品       | 2008-04-28 |
| 事務用品           | 2009-11-11 |
+--------------------+------------+
8 rows in set (0.00 sec)


```

### WHERE句による行の選択

すべての行を選択するのではなく、条件をつけてしぼりこみたい。

- WHERE句で指定した条件にあう行をまず選択し、その後にselect句で指定された列を出力する
    - **WHERE句は必ずFROM句の直後にかく**

``` BASH
SELECT <列名>, ...
FROM <テーブル名>
WHERE <条件式>;
```

``` BASH
select shohin_mei, shohin_bunrui
from Shohin
where shohin_bunrui = '衣服';
+-----------------------+---------------+
| shohin_mei            | shohin_bunrui |
+-----------------------+---------------+
| Tシャツ               | 衣服          |
| カッターシャツ        | 衣服          |
+-----------------------+---------------+
2 rows in set (0.00 sec)

```

### コメント
``` BASH
--一行コメント

/*これは複数行コメントを
  するためのもの*/

```

### 算術演算子

- Select句では指定した列に算術演算子を使って演算をおこなうことができる
    - **NULLがある場合は演算結果はNULLになるので注意**

``` BASH
select shohin_mei, hanbai_tanka,
    hanbai_tanka * 2 as "hanbai_tanka_x2"
from Shohin;
+-----------------------+--------------+-----------------+
| shohin_mei            | hanbai_tanka | hanbai_tanka_x2 |
+-----------------------+--------------+-----------------+
| Tシャツ               |         1000 |            2000 |
| 穴あけパンチ          |          500 |            1000 |
| カッターシャツ        |         4000 |            8000 |
| 包丁                  |         3000 |            6000 |
| 圧力なべ              |         6800 |           13600 |
| フォーク              |          500 |            1000 |
| おろし金              |          880 |            1760 |
| ボールペン            |          100 |             200 |
+-----------------------+--------------+-----------------+
8 rows in set (0.00 sec)

# where句でも比較演算子
select shohin_mei, shohin_bunrui, hanbai_tanka
from Shohin
where hanbai_tanka >= 1000;
+-----------------------+--------------------+--------------+
| shohin_mei            | shohin_bunrui      | hanbai_tanka |
+-----------------------+--------------------+--------------+
| Tシャツ               | 衣服               |         1000 |
| カッターシャツ        | 衣服               |         4000 |
| 包丁                  | キッチン用品       |         3000 |
| 圧力なべ              | キッチン用品       |         6800 |
+-----------------------+--------------------+--------------+
4 rows in set (0.00 sec)

# where句の日付型でも比較演算子
select shohin_mei, shohin_bunrui, torokubi
from Shohin
where torokubi < '2009-09-27';
+--------------------+--------------------+------------+
| shohin_mei         | shohin_bunrui      | torokubi   |
+--------------------+--------------------+------------+
| Tシャツ            | 衣服               | 2009-09-20 |
| 穴あけパンチ       | 事務用品           | 2009-09-11 |
| 包丁               | キッチン用品       | 2009-09-20 |
| 圧力なべ           | キッチン用品       | 2009-01-15 |
| フォーク           | キッチン用品       | 2009-09-20 |
| おろし金           | キッチン用品       | 2008-04-28 |
+--------------------+--------------------+------------+
6 rows in set (0.00 sec)

# where句で条件式と比較演算子
select shohin_mei, hanbai_tanka, shiire_tanka
from Shohin
where hanbai_tanka - shiire_tanka >= 500;
+-----------------------+--------------+--------------+
| shohin_mei            | hanbai_tanka | shiire_tanka |
+-----------------------+--------------+--------------+
| Tシャツ               |         1000 |          500 |
| カッターシャツ        |         4000 |         2800 |
| 圧力なべ              |         6800 |         5000 |
+-----------------------+--------------+--------------+
3 rows in set (0.00 sec)
```

#### 文字列型の比較演算子

CHAR型の列に比較演算子を使うと辞書式順序で大小が比較される
    - `'2' < '1'`と`2 < 1`は扱いがことなる。

``` BASH
-- DDL テーブル作成
create table Chars(
    chr CHAR(3) not null,
    primary key (chr)
);

-- DML: データ登録
start transaction;

insert into Chars values ('1');
insert into Chars values ('2');
insert into Chars values ('3');
insert into Chars values ('10');
insert into Chars values ('11');
insert into Chars values ('222');

commit;

# データ表示
# 辞書順にならんでいる
select * from Chars;
+-----+
| chr |
+-----+
| 1   |
| 10  |
| 11  |
| 2   |
| 222 |
| 3   |
+-----+
6 rows in set (0.00 sec)

# 比較
select chr
from Chars
where chr > '2';
+-----+
| chr |
+-----+
| 222 |
| 3   |
+-----+
2 rows in set (0.01 sec)

```

#### NULLに比較演算子はつかえない

- NULLがある場合は比較演算子で指定しても何もでてこない。
    - NULLかどうかを調べたいなら**IS NULL** で調べる。
    - NULLじゃない場合は**IS NOT NULL** で調べる。

``` BASH
# 比較演算子でNULLはでてこない
select shohin_mei, shiire_tanka
from Shohin
where shiire_tanka = NULL;
Empty set (0.00 sec)

# IS NULLを使う
select shohin_mei, shiire_tanka
from Shohin
where shiire_tanka is null;
+-----------------+--------------+
| shohin_mei      | shiire_tanka |
+-----------------+--------------+
| フォーク        |         NULL |
| ボールペン      |         NULL |
+-----------------+--------------+
2 rows in set (0.01 sec)

# IS NOT NULL
select shohin_mei, shiire_tanka
from Shohin
where shiire_tanka is not null;
+-----------------------+--------------+
| shohin_mei            | shiire_tanka |
+-----------------------+--------------+
| Tシャツ               |          500 |
| 穴あけパンチ          |          320 |
| カッターシャツ        |         2800 |
| 包丁                  |         2800 |
| 圧力なべ              |         5000 |
| おろし金              |          790 |
+-----------------------+--------------+
6 rows in set (0.00 sec)
```

### 論理演算子

- 論理演算子AND, OR, NOT
    - 複数の検索条件を調べられる
- 真理値 TRUE(真), FALSE(偽), UNKNOWN(不明)
- 論理演算子による真理値の操作とその結果が**真理表**
- NULLに対して論理演算子を適用すると、UNKNOWNとなる

``` BASH
# NOT演算子
select shohin_mei, shohin_bunrui, hanbai_tanka
from Shohin
where NOT hanbai_tanka >= 1000;
# hanbai_tanka < 1000と同値
+--------------------+--------------------+--------------+
| shohin_mei         | shohin_bunrui      | hanbai_tanka |
+--------------------+--------------------+--------------+
| 穴あけパンチ       | 事務用品           |          500 |
| フォーク           | キッチン用品       |          500 |
| おろし金           | キッチン用品       |          880 |
| ボールペン         | 事務用品           |          100 |
+--------------------+--------------------+--------------+
4 rows in set (0.00 sec)

# AND演算子
select shohin_mei, shiire_tanka
from Shohin
where shohin_bunrui = "キッチン用品"
and hanbai_tanka >= 3000;
+--------------+--------------+
| shohin_mei   | shiire_tanka |
+--------------+--------------+
| 包丁         |         2800 |
| 圧力なべ     |         5000 |
+--------------+--------------+
2 rows in set (0.00 sec)

# OR演算子
select shohin_mei, shiire_tanka
from Shohin
where shohin_bunrui = "キッチン用品"
or hanbai_tanka >= 3000;
+-----------------------+--------------+
| shohin_mei            | shiire_tanka |
+-----------------------+--------------+
| カッターシャツ        |         2800 |
| 包丁                  |         2800 |
| 圧力なべ              |         5000 |
| フォーク              |         NULL |
| おろし金              |          790 |
+-----------------------+--------------+
5 rows in set (0.00 sec)

```

#### ()で比較をくくる

- AND演算子の方がOR演算子よりも優先されることに注意する。

```
「商品分類が事務用品」
かつ
「登録日が2009年9月11日または2009年9月20日」
```

という条件で検索したいとき、()でくくる。

``` BASH
# ()でくくらない場合
select shohin_mei, shohin_bunrui, torokubi
from Shohin
where shohin_bunrui = '事務用品'
and torokubi = '2009-09-11'
or torokubi = '2009-09-20';
+--------------------+--------------------+------------+
| shohin_mei         | shohin_bunrui      | torokubi   |
+--------------------+--------------------+------------+
| Tシャツ            | 衣服               | 2009-09-20 |
| 穴あけパンチ       | 事務用品           | 2009-09-11 |
| 包丁               | キッチン用品       | 2009-09-20 |
| フォーク           | キッチン用品       | 2009-09-20 |
+--------------------+--------------------+------------+
4 rows in set (0.00 sec)
```

「shohin_bunrui = '事務用品' and torokubi = '2009-09-11'」
or
「torokubi = '2009-09-20'」
と解釈される。

OR演算子を優先したい場合、()でくくる必要がある。

``` bash
select shohin_mei, shohin_bunrui, torokubi
from Shohin
where shohin_bunrui = '事務用品'
and (    torokubi = '2009-09-11'
      or torokubi = '2009-09-20');
+--------------------+---------------+------------+
| shohin_mei         | shohin_bunrui | torokubi   |
+--------------------+---------------+------------+
| 穴あけパンチ       | 事務用品      | 2009-09-11 |
+--------------------+---------------+------------+
1 row in set (0.01 sec)

```

