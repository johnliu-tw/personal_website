---
layout: post
title: '資安 - Google Hacking '
date: 2018-12-31 03:24
comments: true
categories:
- Programming
---
Google Search 除了一般人常用的搜尋方法外，
也有很多特殊的指定搜尋方法，
而這些方法就有可能導致資安的漏洞，
我們來看一下有哪些常見簡易的方法吧！

(1) cache: http://www.google.com.tw/
搜尋某網址的暫存檔，可以抓到暫時被 Google cache 住的檔案。

(2) related: http://www.google.com.tw/
搜尋與被搜尋網站的相同網站，可以做概略相同類別的搜尋

(3) link: http://www.google.com.tw/
列出所有含有查詢的網頁的其他網站頁面，可以找到哪些網站連到你指定的頁面。

(4) filetype: pdf online-book
列出搜尋條件所有符合格式的結果，因此有可能直接去搜尋你專案目錄下，某種公定命名的靜態檔案。

(5) SEO site:google.com.tw
在 google.com.tw 中搜尋所有跟 SEO 相關的內容，對網站內做搜尋，如果有敏感資料沒有清到，
就算放在不顯眼的地方，還是有可能被抓到。

(6) intitle: password 
搜尋所有標題含有 password 的網站，因此可以利用 intitle:"index of" htpasswd
這樣的語句，根據 apache 的特性，搜尋到根目錄的結構圖

(7) intext: etc/pass
搜尋所有內文含有 etc/pass 的網站
