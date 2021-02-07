---
layout: post
title: 'AWS Architectural 初級架構師課程筆記 - Day1'
date: 2019-04-28 07:30
comments: true
categories:
- Devops
---
## Instruction

News shared: 南非為近日要開啟的服務器點
五大 AWS 服務的考量點:安全性、可靠性，成本、效能和可維護性。

### 基礎服務架構
![影像.png](http://user-image.logdown.io/user/26132/blog/25104/post/7830415/E6niEwNT3qJnASi2nWAl_%E5%BD%B1%E5%83%8F.png)

1. 每個 AZ 間間隔約 40 - 60 英里，彼此間的 latency 盡可能控制在 2ms 以下。
2. 北京 region 主要維運商：『光環星網』，中國 region 和其他 region 的環境都是隔開來的。
3. CDN 預設或需要 North Virginia(USA) 的機器當作 base
4. Edge location, 設立在無 region 的區塊，通常與當地 ISP 合作 (ex: 中華電信)，提供與 AWS 相關的部分服務

## Simplest Architected

### S3 vs EBS 
| S3 | EBS |
| ------------ | :-----------: |
| Object 層(更新資料慢 )      |  Block 層(更新資料快 ) |
| 檔案儲存使用      |   檔案分享使用    |
| 公開，可透過 url 訪問  |    與 Ec2 Instance 綁定     |

### S3 特性
1. 因備份特性，會備份同筆資料於不同的 AZ，因此當不同 user 針對同一檔案做修改時，可能會有 conflict 的情況
2. 可綁定 event trigger ，做到在 S3 層可直接裁切圖片的功能
3. 可透過 AWS policy genenrater 協助設定 bucket config
4. 可啟用 version control，替靜態檔案保存過去版本 -->儲存 cost 會相對應的增加

### S3 使用須知
1. 預設是阻擋 CROS 傳輸，因此需手動關閉 CROS Block 功能
2. EC2 相關的 Snapshot 會儲存在 S3
3. 可透過 S3 專用 API，直接上傳檔案
4. 單檔 5TB 傳輸限制，但總量無
5. 傳輸過程，會將同個檔案切割上傳，並再上傳後才在 S3 端 merge 回同一檔案。
6. 同檔名之檔案的上傳，將會自動蓋過舊檔案
7. 手動上傳之檔案，需手動點選【 Make Public 】啟用，讓其能被 public 讀取


eg: File1.jpg 會切割成 File1-1.jpg, File1-2.jpg....然後才上傳。

### S3 價格須知
1. 計費為 per GBs/月和流量傳輸費
2. 每筆 Request 皆會計費，因此建議使用 CDN Server 套在常被 Request 的資源上

### Glacier 
1. 儲存時間長，但價格成本低(S3 1/10)
2. 推薦使用在 log file, 備份, 和長期存儲類型檔案
3. 使用 Vault Lock，因此只存讀不修改
4. 讀取方式分為三種，越長越便宜:
	- expedited retrieval (3 - 5 mins)
  - Standard retrieval (3 - 5 hours)
  - build retrieval(8 - 10 hours)
  
## Computing

Ec2 的使用範疇: Web Server, Database Server, Authentication....etc，但不建議使用在 broadcast 型服務

### AMI
1. AWS Ec2 專用之 Image
2. 類似的服務: snapshot，但snapshot 只能儲存 disk level 的資料，無法儲存環境參數
3. 可直接製作不需 reboot EC2
4. AWS 提供的部分 AMI 包含軟體授權費，例如 SQL Sever + Microsoft server(但不代表較貴，RDS 計價更高)
5. 預先載入之 bash script( User Data )，僅限建立時載入，之後 reboot 等等動作皆不執行

### EBS(EC2 專用 storage)
1.應用程式所須之 block 層的儲存方式
2.資料在 shutdown 後，仍須存在
3.備份應用程式層之資料
4.Instance Data 是暫存的，因此 stop 機器後，內部資料將遺失
5.一個 EBS 只連接一台 EC2 ，可使用 EFS 連接多台 EC2。應用範例: File system

如需固態硬碟，仍可選用 SSD:

| General SSD | Provisioned IOPS SSD |
| ------------ | :-----------: |
| 通用，合理的收費和傳輸速度 | 可修改 IOPS 數量，因此計價較貴 |

### EC2
命名規則: m5.large (m 是機器類型名, 5 是世代數, large 是 instance 大小)
Note: 調整 EC2 大小，將會停機一陣子

| On-Demand | Reserved Instance | Spot Instance |
| ------------ | -----------| ----------- |
| 標準定價      |  標準的 6.5 - 8 折不等 |  最便宜，可能會便宜到 1 - 2 折 |
| 可隨時暫停，不付費      |   根據折數簽訂不同的年限，不用仍須走完(建議一年)    |   可隨時暫停，或遇其他更高出價者，資源被轉移 |

可使用 Dedicate Instance(獨佔)，成本最高

部署分佈方式:

| Cluster Group | Spread Group |
| ------------ | :-----------: |
| 針對 performance 最佳化部署方式 | 分散式部署，較安全，不會被區域問題影響 |

#### EC2 使用須知
1. Public IP 需在部署成功後幾分鐘，才能在 www 上讀取到
2. 可使用 Security Group 設定權限
3. 推薦使用 User-Data 設計預先建立的環境( eg: PHP+Nginx )

## Database

選用 Relation DB 的時機: 嚴格的資料結構和品質、峰值不高的讀寫需求
選用 No-SQL 時機: 須能快速水平增加 DB 資源

| RDS | DynamoDB | Amazon Elasticache |
| ------------ | -----------| ----------- |
| 標準 Relation DB     |  No-SQL  |  Memory Cache, 像是 Redis |
Note: DynamoDB 可以拆分資料在不同 region，並在同一個 global 環境做讀取(可能有 1-2 秒非同步狀況)

### Aurora DB
1. 因為備份機制的因素，需至少有三台 AZ 的 Region 環境
2. 一般機器的等級，比一般 MySQL server 快約 1.3 倍，如為 r3.8xlarge 以上，則為接近 5 倍

### RDS 使用技巧
1. 提供 private key 加密，也能自行設定加密方式
2. Amazon Migration Service 轉移 DB 不收費，費用取決於 Ec2
