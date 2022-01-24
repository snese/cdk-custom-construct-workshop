---
title: "什麼是 AWS CDK？"
chapter: false
weight: 411
---

AWS Cloud Development Kit (CDK) 讓您可以在雲中構建可靠、可擴展、經濟高效的應用程序，並具有編程語言的強大表達能力。這種方法產生了許多好處，包括：

- 使用自動為您的 AWS 資源提供合理、安全的默認值的高級構造進行構建，用更少的程式碼定義更多的基礎設施。

- 使用參數 (parameters)、條件 (conditionals)、循環 (loops)、組合 (composition) 和繼承 (inheritance) 等編程習慣，根據 AWS 和其他提供的構建塊對系統設計進行建模。

- 將您的基礎架構 (infrastructure)、應用程序程式碼 (application code) 和配置 (configuration) 放在一個地方，確保在每個里程碑您都擁有一個完整的、可在雲上部署的系統。

- 採用程式碼審查 (code reviews)、單元測試 (unit tests) 和源程式碼控制 (source control) 等軟體工程實踐，使您的基礎架構更加健壯。

- 將您的 AWS 資源連接在一起（甚至跨 stacks）並使用簡單、面向意圖的 API 授予權限。

- 匯入現有 AWS CloudFormation 模板，為您的資源提供 CDK API。

- 使用 AWS CloudFormation 的強大功能以可預測的方式重複執行基礎設施部署，並在出錯時回滾。

- 在組織內的團隊之間甚至與公眾輕鬆共享基礎架構設計模式。

AWS CDK 支持 TypeScript、JavaScript、Python、Java、C#/.Net 和（在開發人員預覽版中）Go。開發人員可以使用其中一種受支持的編程語言來定義稱為 [Constructs](https://docs.aws.amazon.com/cdk/v2/guide/constructs.html) 的可重用雲組件。您將它們組合成 [Stacks](https://docs.aws.amazon.com/cdk/v2/guide/stacks.html) 和 [Apps](https://docs.aws.amazon.com/cdk/v2/guide/apps.html)。

![AppStacks](../images/04101.png)
