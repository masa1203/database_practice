# SQL操作　window関数編

ウィンドウ関数を学んでいく

- ランキング、連番生成など集約関数ではできない高度な操作を行う

- ウィンドウ関数,別名**OLAP関数, Online Analytical Processing**
    - OLAPとは**データベースを使ってリアルタイムにデータ分析を行う処理のこと。**

- ウィンドウ関数として使える関数
    - 集約関数
        - SUM, AVG, COUNT, MAX, MIN
    - ウィンドウ専用関数
        - RANK, DENSE_RANK, ROW_NUMBER

``` SQL
<ウィンド関数> OVER ([PARTITION BY <列リスト>]
                        order by <ソート用列リスト>)
```

## RANK関数 レコードのランキングを算出する関数

- PARTITION BY
    - 順位をつける対象の範囲
    - この部分集合をウィンドウと呼ぶ
- order by
    - どの列を、どんな順序で順位をすけるか指定
    - この場合は昇順
- 最終的にrankingでソートしたいときは最後にoreder by 句を指定するしか順序の保証は無い

``` SQL
select shohin_mei, shohin_bunrui, hanbai_tanka,
        RANK() OVER (PARTITION BY shohin_bunrui
                        order by hanbai_tanka)  as rankinng
from Shohin;
+-----------------------+--------------------+--------------+----------+
| shohin_mei            | shohin_bunrui      | hanbai_tanka | rankinng |
+-----------------------+--------------------+--------------+----------+
| フォーク              | キッチン用品       |          500 |        1 |
| おろし金              | キッチン用品       |          880 |        2 |
| 包丁                  | キッチン用品       |         3000 |        3 |
| 圧力なべ              | キッチン用品       |         6800 |        4 |
| ボールペン            | 事務用品           |          100 |        1 |
| 穴あけパンチ          | 事務用品           |          500 |        2 |
| Tシャツ               | 衣服               |         1000 |        1 |
| カッターシャツ        | 衣服               |         4000 |        2 |
+-----------------------+--------------------+--------------+----------+
8 rows in set (0.01 sec)

select shohin_mei, shohin_bunrui, hanbai_tanka,
        dense_RANK() OVER (PARTITION BY shohin_bunrui
                        order by hanbai_tanka)  as rankinng
from Shohin;

-- 集約関数をつかってみる

select shohin_id, shohin_mei, hanbai_tanka,
         sum(hanbai_tanka) over (order by shohin_id) as current_sum
from Shohin;
+-----------+-----------------------+--------------+-------------+
| shohin_id | shohin_mei            | hanbai_tanka | current_sum |
+-----------+-----------------------+--------------+-------------+
| 0001      | Tシャツ               |         1000 |        1000 |
| 0002      | 穴あけパンチ          |          500 |        1500 |
| 0003      | カッターシャツ        |         4000 |        5500 |
| 0004      | 包丁                  |         3000 |        8500 |
| 0005      | 圧力なべ              |         6800 |       15300 |
| 0006      | フォーク              |          500 |       15800 |
| 0007      | おろし金              |          880 |       16680 |
| 0008      | ボールペン            |          100 |       16780 |
+-----------+-----------------------+--------------+-------------+
8 rows in set (0.00 sec)

select shohin_id, shohin_mei, hanbai_tanka,
         avg(hanbai_tanka) over (order by shohin_id) as current_sum
from Shohin;
+-----------+-----------------------+--------------+-------------+
| shohin_id | shohin_mei            | hanbai_tanka | current_sum |
+-----------+-----------------------+--------------+-------------+
| 0001      | Tシャツ               |         1000 |   1000.0000 |
| 0002      | 穴あけパンチ          |          500 |    750.0000 |
| 0003      | カッターシャツ        |         4000 |   1833.3333 |
| 0004      | 包丁                  |         3000 |   2125.0000 |
| 0005      | 圧力なべ              |         6800 |   3060.0000 |
| 0006      | フォーク              |          500 |   2633.3333 |
| 0007      | おろし金              |          880 |   2382.8571 |
| 0008      | ボールペン            |          100 |   2097.5000 |
+-----------+-----------------------+--------------+-------------+
8 rows in set (0.00 sec)

```

## 移動平均を算出する

- ROWS: 行数
- PRECEDING: 前の
- FOLLOWING: 後の
- rows 2 preceding
    - 自分 + 自分より1行前のレコード + 自分より2行前のレコード = 直近の3行
- 前後の行を集計対象にする
    - rows between 1 preceding and 1 following

``` SQL
-- 2行前までの平均
select shohin_id, shohin_mei, hanbai_tanka,
        avg(hanbai_tanka) over (order by shohin_id rows 2 preceding) as moving_avg
from Shohin;
+-----------+-----------------------+--------------+------------+
| shohin_id | shohin_mei            | hanbai_tanka | moving_avg |
+-----------+-----------------------+--------------+------------+
| 0001      | Tシャツ               |         1000 |  1000.0000 |
| 0002      | 穴あけパンチ          |          500 |   750.0000 |
| 0003      | カッターシャツ        |         4000 |  1833.3333 |
| 0004      | 包丁                  |         3000 |  2500.0000 |
| 0005      | 圧力なべ              |         6800 |  4600.0000 |
| 0006      | フォーク              |          500 |  3433.3333 |
| 0007      | おろし金              |          880 |  2726.6667 |
| 0008      | ボールペン            |          100 |   493.3333 |
+-----------+-----------------------+--------------+------------+
8 rows in set (0.01 sec)

-- 前後1行
select shohin_id, shohin_mei, hanbai_tanka,
        avg(hanbai_tanka) over (order by shohin_id rows between 1 preceding and 1 following) as moving_avg
from Shohin;

+-----------+-----------------------+--------------+------------+
| shohin_id | shohin_mei            | hanbai_tanka | moving_avg |
+-----------+-----------------------+--------------+------------+
| 0001      | Tシャツ               |         1000 |   750.0000 |
| 0002      | 穴あけパンチ          |          500 |  1833.3333 |
| 0003      | カッターシャツ        |         4000 |  2500.0000 |
| 0004      | 包丁                  |         3000 |  4600.0000 |
| 0005      | 圧力なべ              |         6800 |  3433.3333 |
| 0006      | フォーク              |          500 |  2726.6667 |
| 0007      | おろし金              |          880 |   493.3333 |
| 0008      | ボールペン            |          100 |   490.0000 |
+-----------+-----------------------+--------------+------------+
8 rows in set (0.00 sec)
```

## GROUPING演算子

- group by 句と集約関数だけでは小計・合計を同時にやるのは無理だった
- 無理やりやるならunion allでくっつけてしまう

- ROLLUP
- CUBE
- GROUPING SETS

``` SQL
select '合計' as shohin_bunrui, sum(hanbai_tanka)
from Shohin
union all
select shohin_bunrui, sum(hanbai_tanka)
from Shohin
group by shohin_bunrui;

+--------------------+-------------------+
| shohin_bunrui      | sum(hanbai_tanka) |
+--------------------+-------------------+
| 合計               |             16780 |
| 衣服               |              5000 |
| 事務用品           |               600 |
| キッチン用品       |             11180 |
+--------------------+-------------------+
4 rows in set (0.00 sec)
```

### ROOLUP 合計と小計を一度に求める

``` SQL
select shohin_bunrui, sum(hanbai_tanka) as sum_tanka
from Shohin
group by shohin_bunrui with rollup;

+--------------------+-----------+
| shohin_bunrui      | sum_tanka |
+--------------------+-----------+
| キッチン用品       |     11180 |
| 事務用品           |       600 |
| 衣服               |      5000 |
| NULL               |     16780 |
+--------------------+-----------+
4 rows in set (0.00 sec)

-- 二つのキーを指定した場合

select shohin_bunrui, torokubi, sum(hanbai_tanka) as sum_tanka
from Shohin
group by shohin_bunrui, torokubi with rollup;
+--------------------+------------+-----------+
| shohin_bunrui      | torokubi   | sum_tanka |
+--------------------+------------+-----------+
| キッチン用品       | 2008-04-28 |       880 |
| キッチン用品       | 2009-01-15 |      6800 |
| キッチン用品       | 2009-09-20 |      3500 |
| キッチン用品       | NULL       |     11180 |
| 事務用品           | 2009-09-11 |       500 |
| 事務用品           | 2009-11-11 |       100 |
| 事務用品           | NULL       |       600 |
| 衣服               | NULL       |      4000 |
| 衣服               | 2009-09-20 |      1000 |
| 衣服               | NULL       |      5000 |
| NULL               | NULL       |     16780 |
+--------------------+------------+-----------+
11 rows in set (0.01 sec)
```

| 衣服               | NULL       |      4000 |
| 衣服               | 2009-09-20 |      1000 |
| 衣服               | NULL       |      5000 |

この場合、どっちが小計(超集合行)なのか見分けづらいので、**grouping関数** を使って見分ける

``` SQL
select grouping(shohin_bunrui) as shohin_bunrui,
        grouping(torokubi) as torokubi, sum(hanbai_tanka) as sum_tanka
from Shohin
group by shohin_bunrui, torokubi with rollup;

+---------------+----------+-----------+
| shohin_bunrui | torokubi | sum_tanka |
+---------------+----------+-----------+
|             0 |        0 |       880 |
|             0 |        0 |      6800 |
|             0 |        0 |      3500 |
|             0 |        1 |     11180 |
|             0 |        0 |       500 |
|             0 |        0 |       100 |
|             0 |        1 |       600 |
|             0 |        0 |      4000 |
|             0 |        0 |      1000 |
|             0 |        1 |      5000 |
|             1 |        1 |     16780 |
+---------------+----------+-----------+
11 rows in set, 2 warnings (0.02 sec)]

-- 0,1ではなく適用な文字をいれこむ

select case when grouping(shohin_bunrui) = 1
            then '商品分類 合計'
            else shohin_bunrui end as shohin_bunrui,
        case when grouping(torokubi) = 1
            then '登録日 合計'
            else cast(torokubi as VARCHAR(16)) end as torokubi,
        sum(hanbai_tanka) as sum_tanka
from Shohin
group by shohin_bunrui, torokubi with rollup;
```

