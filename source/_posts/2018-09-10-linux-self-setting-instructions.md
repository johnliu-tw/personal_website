---
layout: post
title: 'Linux - 自行設定指令'
date: 2018-09-10 15:44
comments: true
categories:
- Devops
---
相信大家都很懶，有些長的要死的指令都不想打，
尤其像是透過 ssh 連線到雲端主機，管理遠端 server ，一定要透過指令。
首先我使用的 OS 是 ubuntu，但其實 CentOS 等主流 Linux 其實都通啦～

首先先直接在 root 目錄下開啟或建立 .bashrc 這個批次檔
``` linux 
$ vim .bashrc
```
這個檔案會放置很多 Terminal 設定啊 ~ 已經設定好的縮寫指令啊等等....
你要找到的是有 alias 字樣的任意區塊，
alias 中文意思是別號，我們就是替指令設定別號，
像我的就是設定在這個地方:
![螢幕快照 2018-09-10 下午11.50.10.png](http://user-image.logdown.io/user/26132/blog/25104/post/7810086/mSnBQuARAKkouoe14IVw_%E8%9E%A2%E5%B9%95%E5%BF%AB%E7%85%A7%202018-09-10%20%E4%B8%8B%E5%8D%8811.50.10.png)
我設定了 nginx web server 的重啟和看 log 檔的快捷指令，
結構就是 
``` linux
$ alias + [你的快捷指令] = [你原始得輸入的指令]
```

記得輸入存檔完後，要執行 
```
$ source .bashrc 
``` 
去重新吃進這個指令設定檔喔～
