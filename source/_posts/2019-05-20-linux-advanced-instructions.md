---
layout: post
title: 'Linux 進階指令與工具'
date: 2019-05-20 09:19
comments: true
categories:
- Programming
---
## Linux Advance Command and Tools

### Text Manipulation
#### awk
awk 為文字指令，可以將文字按 “ ” 分欄位，分別儲存到 $0, $1 ...等等變數中。
例如以下指令會輸出某檔結構:
```bash
$ ls -lh
drwxr-xr-x   5 johnliu  staff     160  5 13 11:46 bootstrap
```
使用 pipeline | 轉入 awk 指令中，可分割自己喜歡的資料顯示格式
```bash
$ ls -lh | awk '{print "Permission: " $1 " User: " $3}'
Permission: drwxr-xr-x User: johnliu
```
要注意，$0 會儲存整個 row 的資料，因此第一格是儲存在 $1 中。

#### sed
sed 為文字取代指令，可以使用正規表達式。
例，將" This is a book "中獨立的 is ，改成 IS
```bash
$ echo 'This is a book' | sed 's/ is/ IS/g'
This IS a book 
```

#### sort 
可將文字，按造行去做排序。
```bash
// try.txt
apple
cat
banana
zebra
dog
```
```bash
$ sort try.txt
apple
banana
cat
dog
zebra
```
除了基本的文字排序，還可以按照不同的 flag 做到單位排序、排序結果輸出等等...

#### fmt
可以針對文本做限制行數的呈現
例如:
```bash
// text.txt
Lorem Ipsum is simply dummy text of the printing and typesetting industry. Lorem Ipsum has been the industry's standard dummy text ever since the 1500s, when an unknown printer took a galley of type and scrambled it to make a type specimen book. It has survived not only five centuries, but also the leap into electronic typesetting, remaining essentially unchanged. It was popularised in the 1960s with the release of Letraset sheets containing Lorem Ipsum passages, and more recently with desktop publishing software like Aldus PageMaker including versions of Lorem Ipsum.
```
設定每行寬度最長 40 個字元
```bash
$ fmt -w 40 text.txt
Lorem Ipsum is simply dummy text of the
printing and typesetting industry. Lorem
Ipsum has been the industry's standard
dummy text ever since the 1500s, when an
unknown printer took a galley of type
and scrambled it to make a type specimen
book. It has survived not only five
centuries, but also the leap into
electronic typesetting, remaining
essentially unchanged. It was
popularised in the 1960s with the
release of Letraset sheets containing
Lorem Ipsum passages, and more recently
with desktop publishing software like
Aldus PageMaker including versions of
Lorem Ipsum.
```

#### tr
這個指令可以當作 sed 的直觀簡易版，不需要寫正規式，就能做出文字取代的工作。
```bash
$ tr "\n\r" "，" < some.txt  > some.txt
```
第一個詞組 "\n\r" 是要被取代的文字，
第二個詞組 "，" 是替換的文字，
第三個詞組 < some.txt 是被此替換邏輯替換文字的文檔，
第四個詞組 > some.txt 是替換完後輸出的文檔。

#### nl
輸出包含行數的檔案文字內容
```bash
$ cat package.json
{
  "private": true,
  "devDependencies": {
    "gulp": "^3.8.8"
},
$ nl package.json
   1	 {
   2	  "private": true,
   3	  "devDependencies": {
   4	    "gulp": "^3.8.8"
   5	  },
```

#### egrep, fgrep
egrep 為可使用『延伸性正規表達式』，例如: +, | 等運用正規式符號的grep。
fgrep 常用來搜尋字元為正規表達式符號的文字，例如: $ 等。
```bash
$ egrep -n 'gd|good' some.txt
gd
good
$ grep some.txt
This is $

Lorem Ipsum is simply dummy text of the printing and typesetting industry. Lorem Ipsum has been the industrys standard dummy text ever since the 1500s, when an unknown prin

$ fgrep $ some.txt
This is $
```

### Process Monitoring

#### htop、atop
htop 是一個取代傳統 top 指令的套件，而不是指令，
可以用偏 GUI 的顯示做到 top 即時監控的功能，
atop 更加強調了負載的資料詳盡性。

#### lsof
lsof 全名為 List Open Files
可以列出所有程式正在使用的檔案

```bash
$ lsof
COMMAND    PID    USER   FD      TYPE             DEVICE   SIZE/OFF     NODE NAME
loginwind   91 johnliu  cwd       DIR                1,4        928        2 /
loginwind   91 johnliu  txt       REG                1,4    1278368   319782 /System/Library/CoreServices/loginwindow.app/Contents/MacOS/loginwindow
```

### Network

#### nmap
nmap 為 Linux 套件，可以掃描指定網路主機的網路狀況。
```bash
$ nmap www.hinet.com
Starting Nmap 6.40 ( http://nmap.org ) at 2014-10-02 08:09 CST
Nmap scan report for www.hinet.net (202.39.253.11)
Host is up (0.0034s latency).
rDNS record for 202.39.253.11: 202-39-253-11.HINET-IP.hinet.net
Not shown: 998 filtered ports
PORT    STATE  SERVICE
80/tcp  open   http
113/tcp closed ident
Nmap done: 1 IP address (1 host up) scanned in 6.05 seconds
```

#### tcpdump
tcpdump 是可以擷取網路封包的指令，語法像是:
tcpdump -i eth0 'tcp port 22 and host 192.168.0.X'
-i eth0 為指定網路介面為 eth
'tcp port 22 and host 192.168.0.X' 為擷取 tcp port 號 22，且來自或目的為 192.168.0.x 位址的封包資料
因此就有機會看到明碼傳輸的資料。

```bash
$ sudo tcpdump -nn -X 'host www.rentincredit.com'

00:20:11.667638 IP 172.20.10.4.51463 > 172.104.94.210.443: Flags [P.], seq 1398:1501, ack 1, win 2048, options [nop,nop,TS val 373105961 ecr 796896181], length 103
	0x0000:  6abf 6b00 f864 4abf 6b00 c784 0800 4502  j.k..dJ.k.....E.
	0x0010:  009b 0000 4000 4006 7908 ac14 0a04 ac68  ....@.@.y......h
	0x0020:  5ed2 c907 01bb 7a00 b193 7107 b104 8018  ^.....z...q.....
	0x0030:  0800 49f9 0000 0101 080a 163d 2529 2f7f  ..I........=%)/.
	0x0040:  abb5 8331 e324 c994 d003 6ffd 5d87 9f96  ...1.$....o.]...
	0x0050:  f26f 9bcd faec d559 1bf1 3a53 32a2 4540  .o.....Y..:S2.E@
	0x0060:  6406 8385 e0fa 67ba 5ab5 16a1 de1f b154  d.....g.Z......T
	0x0070:  47ff 12a3 ef2f d6f7 34d0 eb91 263c 4e7d  G..../..4...&<N}
	0x0080:  a3e1 c503 8f31 cbdc 3b01 1cff bda7 72a0  .....1..;.....r
```

此指令需有 root 權限，可配合 -nn 做 IP 轉碼和 -X 做封包查看

#### mtr 
這也是 Linux 的網路套件，結合 ping 和 traceroute ，針對網路節點去做管理與顯示。
```bash
$ sudo mtr www.rentincredit.com
 Host                  Loss%   Snt   Last   Avg  Best  Wrst StDev
 1. 172.20.10.1        0.0%   120    0.8   0.6   0.3   1.6   0.1
 2. ???
 3. 10.54.28.101       0.0%   120   31.9  35.1  22.8 104.0  11.3
 4. ???
 5. ???
 6. ???
 7. rentincredit.com   0.0%   119   67.6  62.2  47.2 105.6   8.7

```

#### airmon、airodump
兩者常用作為利用 kali 破解網路密碼的工具，
airmon將無線網卡打開為監聽模式，airodump 則會擷取並搜集 AP 的封包

#### dig
dig 會用來做 DNS 的解析，可以解析每個查詢的 DNS Server，
常新增 +trace 去做到細部追查
```bash
$ dig +trace www.rentincredit.com
; <<>> DiG 9.10.6 <<>> +trace www.rentincredit.com
;; global options: +cmd
.			29308	IN	NS	m.root-servers.net.
.			29308	IN	NS	d.root-servers.net.
.			29308	IN	NS	i.root-servers.net.
.			29308	IN	NS	b.root-servers.net.
.			29308	IN	NS	h.root-servers.net.
.			29308	IN	NS	l.root-servers.net.
.			29308	IN	NS	c.root-servers.net.
.			29308	IN	NS	g.root-servers.net.
.			29308	IN	NS	j.root-servers.net.
.			29308	IN	NS	e.root-servers.net.
.			29308	IN	NS	a.root-servers.net.
.			29308	IN	NS	f.root-servers.net.
.			29308	IN	NS	k.root-servers.net.
;; Received 239 bytes from 172.20.10.1#53(172.20.10.1) in 30 ms
.
.
.
www.rentincredit.com.	86400	IN	CNAME	rentincredit.com.
rentincredit.com.	86400	IN	A	172.104.94.210
;; Received 79 bytes from 216.66.80.18#53(ns5.he.net) in 62 ms
```

#### iptables
iptables 是用 linux 核心直接執行的防火牆軟體，
因此防火牆的設定皆會透過 command 方式設定，
防火的方法，是會解析封包內容是否符合設定的要求，去解析。

### System Performance
#### nmon
nmon 為系統監控軟體，可以區別不同的資訊塊，例如 I/O, Memory Status 等等...
在 mac os 上沒有此軟體，需要另外下載類似的其他軟體。

#### iostat, vmstat
iostat 專注在 disk 上的 IO， Linux 顯示的資訊會比 Mac Os 詳盡
```bash
root@localhost:~# iostat
Linux 4.4.0-130-generic (localhost) 	05/21/2019 	_x86_64_	(2 CPU)

avg-cpu:  %user   %nice %system %iowait  %steal   %idle
           4.57    0.03    1.99    0.31    0.32   92.77

Device:            tps    kB_read/s    kB_wrtn/s    kB_read    kB_wrtn
sda               0.84         0.09        10.14    1794177  206840904
sdb               0.01         0.11         0.14    2326764    2946620
sdc              21.71         1.69        91.47   34555012 1866536056

johnliu@mac:~# iostat
              disk0       cpu    load average
    KB/t  tps  MB/s  us sy id   1m   5m   15m
   15.78   29  0.45  15  8 77  5.80 4.03 3.18


```

vmstat 主要是查看虛擬記憶體的狀態。
```bash 
$ vmstat
procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
 1  0 462824 2401392  34120 532116    0    0     1    49    1    1  5  2 93  0  0
```

#### sar 
sar 主要用來分析 CPU 的狀況，且能控制輸出頻率和次數，
使用不同的 flag ，也能知道不同面向的負載程度為何。

```bash
$ sar 1 3
Linux 4.4.0-130-generic (localhost) 	05/21/2019 	_x86_64_	(2 CPU)

11:06:09 AM     CPU     %user     %nice   %system   %iowait    %steal     %idle
11:06:10 AM     all      4.95      0.00      2.97      0.00      0.99     91.09
11:06:11 AM     all      4.50      0.00      2.00      0.00      0.50     93.00
11:06:12 AM     all      3.55      0.00      1.52      0.00      0.00     94.92
Average:        all      4.34      0.00      2.17      0.00      0.50     92.99
```
1 3 這兩個數字組合，為每 1 秒鐘產生一次狀態報告，總共產生三次。

#### strace
strace 可以監控一個『指令』使用的參數、輸入輸出的資料等等，
由於 Linux 無法直接監控硬碟，例: /dev，因此需要用 cat 、 tail 先讀取該硬碟，
再做 strace

```bash
$ strace cat /dev/
execve("/bin/cat", ["cat", "/dev/"], [/* 22 vars */]) = 0
brk(NULL)                               = 0x1dbd000
access("/etc/ld.so.nohwcap", F_OK)      = -1 ENOENT (No such file or directory)
access("/etc/ld.so.preload", R_OK)      = -1 ENOENT (No such file or directory)
open("/etc/ld.so.cache", O_RDONLY|O_CLOEXEC) = 3
fstat(3, {st_mode=S_IFREG|0644, st_size=43921, ...}) = 0
mmap(NULL, 43921, PROT_READ, MAP_PRIVATE, 3, 0) = 0x7fd6a228f000
close(3)                                = 0
access("/etc/ld.so.nohwcap", F_OK)      = -1 ENOENT (No such file or directory)
open("/lib/x86_64-linux-gnu/libc.so.6", O_RDONLY|O_CLOEXEC) = 3
read(3, "\177ELF\2\1\1\3\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0P\t\2\0\0\0\0\0"..., 832) = 832
```



