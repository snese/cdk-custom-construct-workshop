---
title: " 使用 AWS CDK 進行開發"
chapter: false
weight: 413
---

[設置](https://docs.aws.amazon.com/cdk/v2/guide/getting_started.html)和[編寫](https://docs.aws.amazon.com/cdk/v2/guide/hello_world.html)您的第一個 CDK 應用程序很容易。AWS CDK 文件中提供了 AWS CDK 支援的程式語言中的簡短程式碼範例：TypeScript、JavaScript、Python、Java 和 C#，供您做參考。 在 [GitHub  repository](https://github.com/aws-samples/aws-cdk-examples) 有提供了比較長的程式碼範例。

[AWS CDK Toolkit](https://docs.aws.amazon.com/cdk/v2/guide/cli.html) 是用於與 CDK 應用程序 (apps) 互動的指令工具 (command line tool )。 它使開發人員能夠綜合 AWS CloudFormation 模板等工件，將 stacks 部署到開發 AWS 賬戶，並與已部署的 stacks 進行**比較 (diff)** 以了解程式碼更改的影響。

[AWS Construct Library](https://docs.aws.amazon.com/cdk/v2/guide/constructs.html) 為每個 AWS 服務提供 constructs，其中許多具有提供高級抽象的“豐富”API。 AWS Construct Library 的目的是降低整合各種 AWS 服務以在 AWS 上實現目標時所需的複雜性和粘合邏輯。

{{% notice note %}}
使用 AWS CDK 是免費的，但您可能會因創建或使用 AWS [收費資源](https://docs.aws.amazon.com/general/latest/gr/glos-chap.html#chargeable-resources)（例如運行 Amazon EC2 instance 或使用 Amazon S3 storage 而產生 AWS 費用。 使用 [AWS 定價計算器](https://calculator.aws/#/)估算使用各種 AWS 資源的費用。
{{% /notice %}}


## The Construct Programming Model

Construct Programming Model (CPM) 將 AWS CDK 背後的概念擴展到其他領域。 使用 CPM 的其他工具包括：

- [CDK for Terraform](https://www.terraform.io/docs/cdktf/index.html) (CDKtf)
- [CDK for Kubernetes](https://cdk8s.io/) (CDK8s)
- [Projen](https://github.com/projen/projen), 用於構建專案配置

[Construct Hub](https://constructs.dev/) 是個 Constructs 的集中地，就好比是 GitHub 一般，您可以在其中查找和發佈用於 CDK 的 constructs，例如 AWS CDK。