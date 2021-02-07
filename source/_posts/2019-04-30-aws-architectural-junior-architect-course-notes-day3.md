---
layout: post
title: 'AWS Architectural 初級架構師課程筆記 - Day3'
date: 2019-04-30 07:20
comments: true
categories:
- Devops
---
Day1 的筆記 - [AWS Architectural 初級架構師課程筆記 - Day1](http://johnliutw.logdown.com/posts/7830415/aws-architectural-junior-architect-course-notes-day1)
Day2 的筆記 - [AWS Architectural 初級架構師課程筆記 - Day2](http://johnliutw.logdown.com/posts/7830416/aws-architectural-junior-architect-course-notes-day2)

## Automation
建議 automation 的 service: 
設立 storage, route 網路, EC2 和 database

### CloudFormation
1. AWS 課程的 Lab 進行方式，就是使用 CloudFormation 機制建立
2. 可以建立常用的 Resource(EC2..)，但可能無法支援最新的 Resource
3. 可參考編輯檔案中的"condition"欄位，就能針對不同環境，設立不同的 cloudformation (同設定檔)
4. 建立可自動化，但移除資源需手動移除
5. 應用層的 network stack name ，會取用在 CloudFormation 上建立的網路樣板名稱，設定上也會帶入參數的概念
6. 移除 template 後，會替根據此 CloudFormation 建立的 EC2 創建 snapshot

### AWS OpsWorks 
應用層級的自動化功能

### AWS Beanstalk
推薦給初級架構師使用的常用簡單自動架構產生器，方便但不夠彈性

## Cache

### Amazon CloudFront 
1. 支援 CDN 和 Websocket
2. 建議使用在 cache 靜態檔案，動態檔案不建議 cache，但有個例子可使用:高峰值買票活動，可把座位資料先 cache 起來，可能精準度不夠，但可用性提高許多
3. 預設存活 1 天，但熱門資源可存在 1 年(AWS 系統判定)
4. CName 的設定和 Domain Name 雷同
5. 有設定 WAF，可以抵禦應用層的攻擊
6. 可以設定什麼類型的檔案會優先被 cache

### Sticky Session
問題描述: 需要儲存 session 在特定的機器上(常發生在 auto-scaling 或 load-balance 後)。
解法:將 session hash 值存入 No-Sql 服務，透過該服務存取對應資料

### Elastic Cache
Cluster 的架構方式，具有很多 cache node（但運算能力還是取決於對應之 EC2 instance）

## Decoupled Architect
首要概念: 系統規劃盡量『高內聚、低耦合』- 功能皆獨立、透過 API 溝通

| Standard queues | FIFO queues |
| ------------ | ----------- |
| 一接到就按照 queues 規則送出     |  因需針對每個 queue 加 timestamp，速度較慢(300/s, 大小256kb 以下) |

### Amazon SQS( Simple Queue Service )
1. 具有恆定性
2. 不推薦推播巨大的 message
3. 獨立使用，且一對一的 model

### Amazon SNS ( Simple Notification Service )
1. 支援 Email, App 推播, SMS 簡訊和 Lamdba 函式
2. 分散式，many to one model

## Microservice and Serverless

### Amazon elastic container service (ECS)
1. 具有 Amason ECR 服務，類似 Docker hub
2. 管理一個 cluster 而不是一個 container
3. 必須設定 Application load balancer，而不只是 load balancer
4. 可以使用 fargate 完全管理 cluster，但必須在建立 container 時使用

### Lambda
1. serverless 服務關鍵
2. 執行時間最多為 15 分鐘
3. 預設是沒有設定在任何 VPC 下，如果欲擺在 VPC 下，必須設定 NAT Gateway
4. Workflow 有四種: parallel, if else, retry, series

### API Gateway
1. 可用來保護 Lambda 端點，避免類似 DDos 攻擊的資安風險
2. 非常出色可以用來管理 API 的功能
3. 如果 API 是時常被呼叫的資源，也會建立 Cache 機制，替 Server 紓解壓力

### Aurora serverless 
建議設定在 test 環境，做靜態資料庫的測試

## Backup recovery

思考方向: 高可用性 --> 備份機制 --> 補救機制
可使用 RDS 和 DynamoDB 做 snapshot，會透過 bin log 去自動備份(約 5 分鐘前狀態)

### AWS Storage Gateway 
1. 分三種類型：
  - tap gateway(glacier)
  - file gateway(s3)
  - volumn gateway(EBS)
2. 可做備份管理

### 備援性檢查流程
1. 先檢查每個 Service 的狀態
2. 總是開啟備份
3. 可以使用 Chaos monkey 外部服務，測試資源的可用性

## Optimization and cloud 
如果需要轉移資源從本地到雲端，最好先設立同等級的機器，並使用一週觀察。