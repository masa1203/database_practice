# SQL操作メモ　CASE式偏

SQLのCASE式を学ぶ

- 条件分岐させることができる
- 検索CASE式, 単純CASE式
    - 単純CASE式は検索CASE式に含まれる
- ELSEは省略できるがかくべき
- ENDは省略できない
- TRUEになるとCASE式はそこで終わる


``` SQL
case when <評価式> then <式>
     when <評価式> then <式>
     when <評価式> then <式>
     .
     .
     .
     else <式>
end
```

``` SQL
-- case式がひとつの列に相当する
select shohin_mei,
        case when shohin_bunrui = '衣服'
             then concat('A:', shohin_bunrui)
             when shohin_bunrui = '事務用品'
             then concat('B:', shohin_bunrui)
             when shohin_bunrui = 'キッチン用品'
             then concat('C:', shohin_bunrui)
             else NULL　-- それ以外はNULLを返す
        end as abc_shohin_bunrui
from Shohin;

+-----------------------+----------------------+
| shohin_mei            | abc_shohin_bunrui    |
+-----------------------+----------------------+
| Tシャツ               | A:衣服               |
| 穴あけパンチ          | B:事務用品           |
| カッターシャツ        | A:衣服               |
| 包丁                  | C:キッチン用品       |
| 圧力なべ              | C:キッチン用品       |
| フォーク              | C:キッチン用品       |
| おろし金              | C:キッチン用品       |
| ボールペン            | B:事務用品           |
+-----------------------+----------------------+
8 rows in set (0.00 sec)
```

商品分類別の合計値を列として取り出したいとき

``` SQL
select shohin_bunrui, sum(hanbai_tanka) as sum_tank
from Shohin
group by shohin_bunrui;
+--------------------+----------+
| shohin_bunrui      | sum_tank |
+--------------------+----------+
| 衣服               |     5000 |
| 事務用品           |      600 |
| キッチン用品       |    11180 |
+--------------------+----------+
3 rows in set (0.00 sec)
```

これを列にして取り出すにはどうすればよいか

```SQL
select sum(case when shohin_bunrui = '衣服'
                then hanbai_tanka else 0 end) as sum_tanka_ihuku,
       sum(case when shohin_bunrui = 'キッチン用品'
                then hanbai_tanka else 0 end) as sum_tanka_kitchen,
       sum(case when shohin_bunrui = '事務用品'
                then hanbai_tanka else 0 end) as sum_tanka_jim
from Shohin;
+-----------------+-------------------+---------------+
| sum_tanka_ihuku | sum_tanka_kitchen | sum_tanka_jim |
+-----------------+-------------------+---------------+
|            5000 |             11180 |           600 |
+-----------------+-------------------+---------------+
1 row in set (0.00 sec)
```

