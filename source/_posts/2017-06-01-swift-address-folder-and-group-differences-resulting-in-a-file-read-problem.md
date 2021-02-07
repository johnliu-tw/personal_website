---
layout: post
title: 'Swift - 解決因為group和folder差異，導致的檔案讀取問題'
date: 2017-06-01 15:41
comments: true
categories:
- Mobile
---
在開發swift專案時，就像web開發一樣的邏輯，將大量相似的圖片檔案丟進某個檔案資料夾裡，期望透過Airport Data/min.jpg的形式讀取檔案。
但Xcode的開發IDE新增資料夾的方式很多元，有許多方法會讓開發者誤以為已經『正確』的加資料夾進專案內，但仍無法正確的讀檔案。
今天爬了一些文後發現，會有這個問題是因為Swift的檔案系統有『Group』和『Folder』的差別。
## Group
Group的資料夾會已黃色的資料夾方式顯示：
![螢幕快照 2017-06-01 下午11.49.36.png](http://user-image.logdown.io/user/26132/blog/25104/post/1906885/9a95w5GSTSydR15Ildna_%E8%9E%A2%E5%B9%95%E5%BF%AB%E7%85%A7%202017-06-01%20%E4%B8%8B%E5%8D%8811.49.36.png)
此資料夾裡的檔案會被編譯，而且是以『參考』的形式去做目錄，因此在專案內變動其路徑不會更改實際檔案路徑，通常用做第三方framework方式使用。
主要新增方式，是直接對根資料夾右鍵點選"Add file to..."或是"New Group"。

## Folder
Group的資料夾會已藍色的資料夾方式顯示：
![螢幕快照 2017-06-01 下午11.54.15.png](http://user-image.logdown.io/user/26132/blog/25104/post/1906885/n0JIZJx0T1ayWjqEH3kR_%E8%9E%A2%E5%B9%95%E5%BF%AB%E7%85%A7%202017-06-01%20%E4%B8%8B%E5%8D%8811.54.15.png)
此資料夾不會參與編譯，而且是實際的讀檔目錄，因此透過UIImage(name:)等方法指定檔案路徑時，須使用此資料夾類型方能正確讀檔。
主要新增方式，是打開xcode和要被移入的資料夾，將資料夾直接拖進專案中，並選擇"Create Folder Reference"

