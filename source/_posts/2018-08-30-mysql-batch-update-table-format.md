---
layout: post
title: 'MySql - 批次更新 Table 格式 ( collate )'
date: 2018-08-30 09:25
comments: true
categories:
- 其他程式語言
---
需求: 將所有 Table 格式更新成 UTF-8

基本上，批次更新 MySql Table 格式，是要用 Sql 生成 Sql 語句進行。
請參考以下句子：

``` sql
SELECT CONCAT("ALTER TABLE ", TABLE_SCHEMA, '.', TABLE_NAME," CONVERT TO CHARACTER SET utf8 COLLATE utf8_general_ci;") AS    ExecuteTheString
FROM INFORMATION_SCHEMA.TABLES
WHERE TABLE_SCHEMA="yourschema"
AND TABLE_TYPE="BASE TABLE";
```

上面的 sql 語句會根據 schema 內的所有 table，
要填寫的變數，只有裡面的 yourschema, utf8, utf8_general_ci ，這幾個，
產出對應的 sql 語句，並放在 ExecuteTheString 這個欄位中。
一個一個抓出來執行，即可完成此需求。

執行結果
![螢幕快照 2018-08-29 上午11.24.41.png](http://user-image.logdown.io/user/26132/blog/25104/post/7808955/Qoh359S7QQy3zRAqniii_%E8%9E%A2%E5%B9%95%E5%BF%AB%E7%85%A7%202018-08-29%20%E4%B8%8A%E5%8D%8811.24.41.png)

