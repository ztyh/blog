---
title: "ジョインしたテーブルをグループ化して最大値を同順も含めて求める"
date: 2018-11-09T20:13:48-05:00
draft: false
tags: ['SQL','日本語','学習']
---

タイトルの説明じゃ多分わかりにくいですね。売り上げ情報が`sales_records`に入ってて商品情報が`items`に入っている。もっとも売れた商品を同順も含めて求めたい。以下で求められるが少し長いだろうし、同じクエリを二回も読んでしまっている。
```.sql
select *
from
(
select items.name, count(*) as sold
from sales_records
join items
on sales_records.item_id=items.id
group by items.name
)
where sold=
(
select max(sold)
from
(
select items.name, count(*) as sold
from sales_records
join items
on sales_records.item_id=items.id
group by items.name
)
)
```
多分他にも策はあるんだろう。又の機会に。


