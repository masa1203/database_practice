# 起動方法
docker-compose up

# コンテナに入る

docker-compose exec mysql bash

# mysqlサーバーに入る

mysql -u root -p 
password

# データベースを指定して入る

mysql -u root -p database_name

データベースの作成
create database shop; 

使用するデータベースの指定
use shop; 

使用しているデータベースの確認
select database(); 

データベースの一覧
show databases; 

# テーブル

テーブルの作成
create table Shohin
(shohin_id  CHAR(4) NOT NULL, 
 shohin_mei VARCHAR(100) NOT NULL, 
 shohin_bunrui VARCHAR(32) NOT NULL, 
 hanbai_tanka INTEGER   , 
 shiire_tanka INTEGER   , 
 torokubi     DATE      , 
 PRIMARY KEY (shohin_id)); 

テーブルの削除
DROP TABLE Shohin; 

列を追加
ALTER TABLE shohin ADD COLUMN (

    shohin_mei_kana VARCHAR(100);

)

列の削除
ALTER TABLE Shohin DROP COLUMN (shohin_mei_kana); 

テーブル名の変更
RENAME TABLE Sohin to Shohin; 

データの追加
START TRANSACTION; 

INSERT INTO Shohin values ('0001', 'Tシャツ', '衣服', 1000, 500, '2009-09-20'); 
INSERT INTO Shohin values ('0002', '穴あけパンチ', '事務用品', 500, 320, '2009-09-11'); 
INSERT INTO Shohin values ('0003', 'カッターシャツ', '衣服', 4000, 2800, NULL); 
INSERT INTO Shohin values ('0004', '包丁', 'キッチン用品', 3000, 2800, '2009-09-20'); 
INSERT INTO Shohin values ('0005', '圧力なべ', 'キッチン用品', 6800, 5000, '2009-01-15'); 
INSERT INTO Shohin values ('0006', 'フォーク', 'キッチン用品', 500, NULL, '2009-09-20'); 
INSERT INTO Shohin values ('0007', 'おろし金', 'キッチン用品', 880, 790, '2008-04-28'); 
INSERT INTO Shohin values ('0008', 'ボールペン', '事務用品', 100, NULL, '2009-11-11'); 

COMMIT; 

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
