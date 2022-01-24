---
title: "CDK Custom Constructs Workshop"
chapter: false
weight: 00
---

# CDK Custom Constructs Workshop

Constructs 是 AWS CDK 應用程序的基本構建塊。Constructs 表示 “cloud component” 並封裝 AWS CloudFormation 創建component所需的一切。

{{% notice note %}}
Constructs 是構造編程模型 (Construct Programming Model, CPM) 的一部分，也被其他工具使用，例如用於 Terraform 的 CDK (CDKtf)、用於 Kubernetes 的 CDK (CDK8s) 和 Projen。
{{% /notice %}}

Construct 可以表示單個 AWS 資源，例如 Amazon Simple Storage Service (Amazon S3) 存儲桶(bucket)，也可以是由多個 AWS 相關資源組成的更高級別的抽象。此類組件的示例包括具有相關計算能力的工作隊列，或具有監控資源和儀表板的計劃作業。

AWS CDK 包含一組稱為 AWS Construct Library 的構造，其中包含每個 AWS 服務的構造。 [Construct Hub](https://constructs.dev/search?q=&cdk=aws-cdk&cdkver=2&sort=downloadsDesc&offset=0) 是一種資源，可幫助您從 AWS、第三方和開源 CDK 社區發現其他構造。

{{% notice info %}}
在 AWS CDK v1 中，Construct 基類位於 CDK 核心模塊中。在 CDK v2 中，有一個名為constructs 的單獨模塊包含此類。
{{% /notice %}}

## 對 Workshop 有何期待

AWS CDK Constructs 是可重用、可組合的模塊，用於封裝資源和 AWS CDK 應用程序的組件。

### 我會學到什麼？

您將在此 Workshop 了解 AWS CDK 是什麼，釐清 CDK Constructs 之間的關聯和 L1 以及 L2 Construct 的差異，並且了解如何做一個屬於你或是團隊專用的 Custom Constructs，並發布到 GitHub npm 上

### 要求的學習水平是多少？

Level: 300 (對 AWS CDK 充滿好奇及熱情就夠了)

### 它會花費我什麼嗎？

AWS CDK 本身沒有費用，但在進行 Workshop 使用自己的帳號，將會需要對已部署的資源，付出相對應的資源費用。

