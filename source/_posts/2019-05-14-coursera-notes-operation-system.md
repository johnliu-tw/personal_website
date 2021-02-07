---
layout: post
title: 'Coursera 筆記 - Operation System'
date: 2019-05-14 10:01
comments: true
categories:
- Programming
---
### **IO**

#### ps aux | grep “Python”
| 符號，會將左邊指令所得到的結果文字，『輸出』到右邊的指令，當作基本文字資料。
例如這段指令，會將所有程序抓出，並輸出給右邊去搜尋含有 "Python" 這個字的指令細節。

#### 指令: echo cool >> test.txt
將 cool 這個詞，寫入在 test.txt 檔的最後方

### **User privilege**
#### -rwxrw-r--
這是一個 Linux 中，呼叫一個檔案時會顯示的使用者權限表，
第一個詞 “-” ，代表了這是一般的檔案，
第二個詞組 "rwx"，代表一般 user 的權限，第三個詞組 "rw-" 代表了 user group 的權限，
最後一個詞組 "r--" 代表了其他 user 的權限。

#### chmod 777 Desktop
X的數字是 1, W的數字是 2 , R的數字是 4, 加起來去決定權限值，第一個數字是 User 的, 再來 User Group, 最後 Other User

#### chmod 2775 test
第一個詞 2， 是 setgid，讓 test 被執行時，group 是用此檔案創建的 group 身份運行。
如果設定為 4 是 setuid，在 ls -lh 看權限目錄時，該檔案的 x 會由 s 替代
如果設定為 1 ，則為 sticky bit，會讓該檔案的權限有個 “t” ，指的是讓所有使用者都可使用，但只有擁有者可以刪除或更名。

#### chmod g+w file_name
g 是 group, u 是 user, o 是 other, a 是 everyone，+w 則為新增權限類別

### **Package**
#### PPA 
讓 Linux 使用者可以在套件有更新時，自動更新

### **File System**

#### Format
NTF : Windows file system 
Ext4 : Ubuntu file system
FAT32 : USB File system，適用於每個 OS( 單檔最大為 4 GB, system 最大容量為 32 GB )

#### Disk
Disk —> Partition(可以使用不同的 file system) —> volume 

#### Partition Table
MBR: volume 要少與 2 TB
GPT: volume 沒有任何大小限制

新增硬碟流程:
插入硬碟 —> 格式化 file system —> partition 硬碟 —>安裝 file system

#### Pages
硬碟中的 Block data 叫做 Pages( swap in Linux ), 這可以把資料暫時的狀態儲存在記憶體中, 在電腦 Crash 時，可以在 reboot 後復原。

#### Link
soft link: 直接連接到原始檔
hard link: 連接到Inode中，紀錄連結到原始檔的位置
Linux 中，原始檔會紀錄有多少的 hard link，可以確保有多少相似的檔案在系統中

### **Process and Log**

#### Process
Windows 的 child 和 parent process 都可以獨立存在，但 Linux 的 child process 如果沒了 parent process ，就會消失。
/proc/{pid} 可以查看 Linux 中的 process 位資訊。

#### Kill
Kill 不是專門砍掉 process 的指令，而是具備傳送暫停、中止、重啟指令的功能。

#### Loh 
/var/log/syslog 通常包含全面性的 system log