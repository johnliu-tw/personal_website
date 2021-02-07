---
layout: post
title: 'AWS Architectural 初級架構師課程筆記 - Day2'
date: 2019-04-28 08:47
comments: true
categories:
- Devops
---
Day1 的筆記 - [AWS Architectural 初級架構師課程筆記 - Day1](http://johnliutw.logdown.com/posts/7830415/aws-architectural-junior-architect-course-notes-day1)

## Network

VPC 層級的設計是非常重要，但時常被忘記考量的區塊。

### Route53
跨 region 層級的路由，至少有 2 個 name server，因此不會有 naming 失效的問題

標準網路架構圖:
![影像.png](http://user-image.logdown.io/user/26132/blog/25104/post/7830416/2YvEuERuTYS3LRXRKQ4T_%E5%BD%B1%E5%83%8F.png)

### VPC
1. 預設 private IP 位址為 172.16.xx.xx...
2. 預設為 Ipv4, 且設定完後是不可被修改的。可調整為 Ipv6，且設定完後可修改。
3. 至少需橫跨 2 個 AZ。
4. Multi Patteren 選擇
  - 多 account 單 VPC
  - 多 VPC 單 account
5. 使用 peering 連接不同的 VPC，但一個 account VPC 上限值為 5
6. 可使用 VPC endpoint，跨過中間所有 gatway ，能直接取用 private subnet 的資源

### Subnet
1. 支援 16 - 28，共 28 - 62xxx ips。
2. 每次切割網段，會損失四個 ip，且不能跨 AZ
  - 頭和尾(0,255)系統使用，常用在 broadcast
  - 1,2 保留做 gateway 用
  - 3 保留為緊急處理時使用
3. S3 的 ACL(訪問控制列表)在這個層級

### Route Table 
1. 預設 destination 為 10.0.0.0/16，且 target 為 local(朝向主要的 VPC)
2. 建議針對每個 subnet 設定對應的 RouteTable
3. 預設Public Subnet 10.0.0.0/24、Private Subnet 10.0.2.0/23 
4. 設定完 VPC, Subnet和 Internet Gatway 後，需記得設 routetable
Peering 的 RouteTable 設計:
- AAA VPC route table  10.5.0.0/16( 參考 BBB VPC CIDR )
- BBB VPC route table 10.0.0.0/16( 參考 BBB VPC CIDR )


### Gateway
##### Internet Gatway
純粹的邏輯組件，需要連接在 VPC 上才能使用，用作 VPC 對外溝通的 Gateway。
在 RouteTable 上預設為 0.0.0.0

##### NAT Gateway
1. 必須部署在 public subnet，是外部 user 要使用 private subnet service 的 gatway(常用在更新 patch 檔)
2. 無法支援轉 port 號的服務
3. 無提供 log，log 必須做在 VPC 層

##### Transit Gateway
可支援 50000 組以上的 VPCs 連接

###  IP address
Elastic 的 IP address 是浮動的，如果需要固定 IP ，則必須支付額外成本

### Security Group
預設開啟所有 Outbound，並阻擋所有 Inbound

### ACL
Subnet 層級的防火牆

轉換費用圖:
![影像png](https://s1.imgs.cc/img/aaaaaAalc.png)

### VPN
1. VPN 無法連線時，優先排除硬體問題，並檢查 VPN Log
2. AWS 有提供穩定版的 VPN 連線(DX)，可設定一條普通、一條穩定版的架構
3. 如果 VPN 有一陣子不用，可能會被 shutdown (可設定 cronjob 固定傳送小封包)
4. 需要類似 VPC 的架構，可使用 VGW
5. 建立 VPN 簡易流程: 建立 VPN Gateway -> 連接 VPC -> 建立 local end 的 customer gateway -> 建立 site to site 的連接

## Identity
tips:
- 建議在建立好對應的權限群組後，封存 root user 的 account
- 最小單位為 IAM User
- 利用 AkSk(Access & secret key) 就可以透過 CLI 使用 AWS

### IAM User
1. 沒有預設的權限
2. 透過 policy 設定( 可使用 Json 格式更新 )
3. 可設定僅能透過 console 操作或是 CLI 介面
4. 具有 switch role 功能，可測試不同使用者使用權限

| Resourced base | Identity base |
| ------------ | :-----------: |
| 根據 AWS Resource 設定權限      |  根據 IAM 規則設定權限 |

Policy Type 區分為 3 個層級: AWS Policy, Customer Policy 和 inline Policy(只有在 my account 內設定)

### IAM Role 
1. 可給予使用者或 Service (例如 EC2)
2. 使用後，就不需替使用者設定 permission

### AWS Organization
可解決不同帳戶權限，但相同計價方式的『部門級』使用問題。

## Elasticity, High Availability 
兩個取捨: Time 或 Volumn

### CloudWatch
類似 Azure 的 Application Insight，提供 metrics, log, alarms, events, rules, targets..etc

### About Data
1. EBS 只能增不能減
2. Data 的 monitor 預設是關閉的( 因資料外洩資安問題 )
3. 可使用 CloudTrail 追蹤 API Log(90天內)

### Auto Scale
1. 三種方式: 預定型、動態偵測型、預測型
2. 三種 Instance type 皆支援(on-demand, reserved, spot)
3. 建議寬鬆的 CPU 偵測設定(閥值 50 比 30 好)
建議設定方式:
如果期待 5/10 12:00 需要 auto-scaling，建議在 4/20 時提交申請並設定區間為 11:30 - 12:30

Auto-scaling 結束後的 instance 移除判斷
狀況:Az1 — 3 Ec2, Az2 — 2 Ec2
1. 優先平衡 Az 狀況(移除 Az1)
2. 確認 AMI 版本，留下最新者(因此需定期更新 cloudformation)
3. 優先刪除離下個 payment 週期較近者

##### RDS 的 Auto Scale
1. 提供 replication 方式的水平展延
2. 只有 EBS 沒有 downtime 

### DynamoDB
1. 因為計價方式緣故，建議設定限制
2. 分散式存儲，因此會儲存類似 style 的資料在同一個 partition，因此建議 date log 類型資料，做 Hash 化儲存，之後取出較有效率

### Elastic Load Balance
1. 需給與 DNS Name
2. 可以使用 ACM (AWS 的 SSL 憑證機制)
3. 可設定 Application 或 Network 的 load balancer 
4. 可以在 public 和 private subnet 中設定
5. Web Application 可設定在 private 中，並透過 ELB 公開，這樣攻擊者只能攻擊到 ELB。
6. 對應的 Application 要設定 ELB 的 Inbound

### High Availability 
情境說明:
多個 RDS 內的 Az 可以複製 database 去 secondary server，當 original DB Crash 時，需要 60 - 120 秒時間自動切換(但 console 內可能顯示約 2 小時)


