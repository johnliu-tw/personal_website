---
layout: post
title: ' Ruby on Rails - Batch, Bulk, 批次更新(JavaScript輔助)'
date: 2017-09-28 17:50
comments: true
categories:
- Ruby on Rails
---
最近有個需求，是要針對多筆資料，透過打勾選取後，做批次active或inactive。
雖然是RoR的專案，但我也使用javascript輔助我實作此功能。
假定我有一組鷹架產生的Papermodel、controller和view：
## 1 新增button tag
我寫了原生的html code，因為我還是比較習慣這種方式產生簡單的tag
``` erb
index.html.erb

<button class="btn btn-success" id="batch-active">批次有效</button>
<button class="btn btn-danger" id="batch-inactive">批次無效</button>
```

## 2 實作出checkbox儲存資料的id
``` erb
index.html.erb

<input type="checkbox" class="paper_id" value=<%= paper.id %>>
```

## 3 撰寫Ajax去做POST更新

將js檔放入public資料夾，並僅在想引入該功能的頁面import js檔，
那Rails專案就僅會在該頁使用此js，而不會在每頁都載入此js。

再來Ajax的方法是POST資料到 /papers/:id 的route
而POST的Data包含：1.要更新的資料 2.web verb: put

用此兩段式更新的方法，是因為Rails更新資料時會設立一個token避免資料庫受到傷害。
而此方法會產生token pass過Rails的檢查。
並最後在ajax complete後再callback自己，
已避免迴圈導致的異步化問題，會對資料庫過快做存取。

``` js
public/js/batchUpdate.js

  function ajaxBatchUpdate(paper_ids,active,count){
    $.ajax({
        type: "POST",
        url: "/papers/"+paper_ids[count],
        dataType: "script",
        contentType: 'application/json',
        data: JSON.stringify({ active:active, _method:'put' })
        })
        .complete(function(data){
          if(count< paper_ids.length-1){
            count += 1;
            ajaxBatchUpdate(paper_ids,active,count);
          }
        })
  }
```
 
## 4 更新路由

替路由papers/:id 新增post方法，並指向update方法
``` ruby
 	route.rb
 
  post 'papers/:id', :to => 'papers#update'
```
 
## 5 DOM綁定event

功能完成

``` js
$("#batch-active").click(function(){
    var paper_ids = [];
    var active = true;
    var count = 0;
    $(".paper_id:checked").each(function(){
      paper_ids.push($(this).val());
    });
    ajaxBatchUpdate(paper_ids,active,count)
  });
```
