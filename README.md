# AWS_OPE_kenshu

このリポジトリは、AWS 上でのインフラ設計・構築・運用をテーマにした  
ハンズオン形式の課題演習（ポートフォリオ）をまとめたものです。

- 課題演習 No.1：VPC / EC2 / NAT Gateway / IAM
- 課題演習 No.2：WAF / CloudFront / S3 / API Gateway / Lambda（翻訳アプリ）
- 課題演習 No.3：CloudFormation / Auto Scaling / CloudWatch / SNS

各演習の詳細は `AWS_OPE_Kenshu_vol.1〜3.pdf` にまとめています。

---

## 📘 課題演習 No.1（VPC + EC2 + NAT Gateway + IAM）

**テーマ：**  
オンプレミスの社内システムを AWS へ移行することを想定し、  
インターネット非公開の社内システムと、踏み台サーバーを使った  
セキュアな運用環境を構築する演習。 [oai_citation:0‡AWS＿OPE _Kenshu _vol.1.pdf]  

**ポイント：**

- 単一 VPC 内に **パブリックサブネット／プライベートサブネット** を設計
- 社内システム用 EC2 をプライベートサブネットに配置し、  
  インターネットからの直接アクセスを禁止
- パブリックサブネットに **踏み台サーバー（EC2）＋ Elastic IP** を配置し、  
  社内システムへの SSH 接続を踏み台経由に限定
- **NAT Gateway** を利用し、社内システム側 EC2 からのみ  
  ソフトウェアアップデートなどのアウトバウンド通信を許可
- 社内システムへの SSH ポートを **10022** に変更し、セキュリティ強化
- IAM グループとカスタムポリシーにより、  
  運用者アカウントから **EC2 の削除（終了）を禁止** する権限制御を実装

**学んだこと：**

- プライベートサブネット／踏み台構成によるセキュアな運用設計
- NAT Gateway・ルートテーブルを利用したアウトバウンド制御
- セキュリティグループ・ポート設計の考え方
- IAM グループを用いた運用者権限の設計（インスタンス削除の禁止）

---

## 📗 課題演習 No.2（WAF + CloudFront + S3 + 動的コンテンツ）

**テーマ：**  
世界中のユーザー向けの Web 翻訳サイトを想定し、  
静的コンテンツと動的コンテンツをサーバーレスで構成しつつ、  
低レイテンシかつセキュアに配信する構成を設計・構築する演習。 [oai_citation:1‡AWS_OPE _Kenshu _vol.2.pdf] 

**ポイント：**

- **S3 を完全プライベート**（パブリックアクセスブロック）で作成し、  
  静的 Web コンテンツ（HTML / CSS / JS）を格納
- **CloudFront + Origin Access Control (OAC)** を利用し、  
  CloudFront 経由からのみ S3 へアクセスできるように制御
- **API Gateway + Lambda + Amazon Translate** を利用した  
  翻訳 API（動的コンテンツ）を構築
  - Lambda には `TranslateReadOnly` ポリシーを付与
- CloudFront の別オリジンとして API Gateway を組み込み、  
  静的サイトから動的 API を呼び出せる構成を実現
- **AWS WAF** を CloudFront にアタッチし、  
  アプリケーションレベルの攻撃から Web アプリを保護

**学んだこと：**

- S3 + CloudFront + OAC によるセキュアな静的サイトホスティング
- API Gateway + Lambda + 各種マネージドサービスを用いたサーバーレス API 構成
- WAF を用いた Web アプリケーションの保護と、CDN との組み合わせ
- 静的コンテンツと動的コンテンツを統合したフロント設計

---

## 📙 課題演習 No.3（CloudFormation + Auto Scaling + CloudWatch + SNS）

**テーマ：**  
検証環境／本番環境などを素早く再現できるよう、  
CloudFormation テンプレートで Web システムを IaC 化し、  
Auto Scaling・監視・通知までを含めた運用を自動化する演習。 [oai_citation:2‡AWS_OPE _Kenshu _vol.3.pdf] 

**ポイント：**

- CloudFormation テンプレートで以下の構成を自動構築
  - 2 つの AZ に跨るパブリックサブネット
  - 事前に作成した AMI を利用した Web サーバー EC2（各 AZ に 1 台）
  - Application Load Balancer（ALB）＋ ターゲットグループ
- Route 53 & ACM を利用し、独自ドメイン＋HTTPS（SSL/TLS）終端を設定
- **Auto Scaling Group** を構成し、
  - CPU 使用率 **70% 超でスケールアウト**
  - CPU 使用率 **20% 未満でスケールイン**
  となるダイナミックスケーリングポリシーを設定
- **CloudWatch Alarm + SNS** でメトリクスを監視し、  
  スケールイベントやしきい値超過時にメール通知

**学んだこと：**

- CloudFormation による VPC / EC2 / ALB / Auto Scaling の IaC 実装
- 独自ドメイン・証明書を利用した HTTPS 環境の構築フロー
- CPU メトリクスを用いたスケール戦略の設計
- CloudWatch + SNS を組み合わせたアラート・通知設計

---

## 👨‍💻 技術スタックまとめ

- **インフラ / ネットワーク**
  - VPC, Subnet, Internet Gateway, NAT Gateway, Security Group
- **コンピュート**
  - EC2, Auto Scaling Group, AMI
- **ストレージ / コンテンツ配信**
  - S3, CloudFront, Origin Access Control (OAC)
- **サーバーレス / アプリケーション**
  - Lambda, API Gateway, Amazon Translate
- **セキュリティ / 認証**
  - IAM（ユーザー／グループ／ロール／カスタムポリシー）
  - AWS WAF, SSL/TLS（ACM）
- **運用 / モニタリング**
  - CloudWatch（メトリクス／アラーム）
  - Amazon SNS（メール通知）
- **IaC**
  - AWS CloudFormation（YAML テンプレート）

---

## 📂 ファイル構成（例）

```text
.
├── AWS_OPE_Kenshu_vol.1.pdf  # 課題演習 No.1：VPC + EC2 + NATGW + IAM
├── AWS_OPE_Kenshu_vol.2.pdf  # 課題演習 No.2：WAF + CloudFront + S3 + API GW + Lambda
└── AWS_OPE_Kenshu_vol.3.pdf  # 課題演習 No.3：CloudFormation + ASG + CloudWatch + SNS
