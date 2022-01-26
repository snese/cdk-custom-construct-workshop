---
title: "Gitpod - 設置"
chapter: false
weight: 432
---

## 使用 Gitpod 開發 Custom Construct (1/2)

### 建立 GitHub Custom Contstrcut 專案

請先登入自己的 GitHub 帳號，同時點擊進入 [neilkuan/cdk-construct-gitpod-template](https://github.com/neilkuan/cdk-construct-gitpod-template) 以此專案作為樣板 (tempalte) 建立你的專案。

如下圖所示，點擊 **Use this template** 按鈕

![Untitled](../images/04301.png)

接著輸入你想要的專案名稱至 **Repository name** ，任意選擇 **Public** 或是 **Private**，最後點擊 **Create repository from templates** 就建立完成。

{{% notice note %}}
選擇 **Public** 就是創建公開大家都看得見的 Repository，選擇 **Private** 就是創建私有大家看不見的 Repository，目前實測 Pub
{{% /notice %}}

![Untitled](../images/04302.png)

### 使用 Gitpod 開發 CDK Custom Construct

參考下面的範例連結，就能快速地建立起新的 Gitpod Workspace。

{{% notice tip %}}
https://gitpod.io/#[github repo url]
{{% /notice %}}

```bash
# example
https://gitpod.io/#https://github.com/neilkuan/cdk-construct-demo
```
{{% notice note %}}
想多了解 Gitpod 也能參考 [Gitpod Screencast 01: Getting started with Gitpod 🍊 ](https://www.youtube.com/watch?v=w65POyu3ZUQ) 的影片。
{{% /notice %}}

![Untitled](../images/04303.png)

{{% notice note %}}
先前沒有使用過 **Gitpod** 的用戶，可以透過 GitHub account 進行OAuth login
{{% /notice %}}

![Untitled](../images/04304.png)

{{% notice note %}}
**Gitpod** 將會由先前專案樣本中定義的 **.gitpod.yml** 進行環境配置
{{% /notice %}}

![Untitled](../images/04305.png)

{{% notice note %}}
按照 **.gitpod.yml** 的配置，推送到 Gitpod 內部 container registry，並拉取映像檔下來供開發用的 workspace 使用
{{% /notice %}}

![Untitled](../images/04306.png)

{{% notice note %}}
執行到此 IDE 畫面，表示已經準備好可以開發
{{% /notice %}}

![Untitled](../images/04307.png)

{{% notice note %}}
打開 Gitpod Workspace 的 **TERMINAL** ，並執行 **yarn** 安裝所需的 package
{{% /notice %}}

![Untitled](../images/04308.png)

{{% notice note %}}
接著參照 **DEV_README.md** 的說明文件進行 **package.json** 的配置設定。
{{% /notice %}}

{{% notice tip %}}
建議開啟一個 **split terminal** 執行 **yarn watch** ，方便開發同時也能即時檢查 **Compiles** 錯誤訊息
{{% /notice %}}

![Untitled](../images/04309.png)

{{% notice note %}}
從這邊可以看到即時發現 **Compiles** 錯誤訊息的狀態。
{{% /notice %}}

```
2022-01-20T11:49:17.715] [ERROR] jsii/compiler - Compilation errors prevented the JSII assembly from being created
src/integ-dev.ts(13,3): error TS18004: No value exists in scope for the shorthand property 'vpc'. Either declare one or provide an initializer.
```

![Untitled](../images/04310.png)
