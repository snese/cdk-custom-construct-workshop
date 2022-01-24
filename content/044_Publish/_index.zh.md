---
title: "發佈到 GitHub npm"
chapter: false
weight: 44
---

## 建立 GitHub Personal access tokens (PATs)

請參考 GitHub 文件 [Creating a personal access token](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token)，建立個人用的 Personal access tokens (PATs)

其中 PATs 需要的權限為:
- repo:* 
- write:packages 📦
- read:packages  📦 
- (optional:  delete:packages 📦)
    
![Untitled](images/04401.png)
    

## 發佈 Package 到 GitHub npm repository

首先需要設置 **.npmrc** 檔案，讓 npm 知道你要發佈到 **GitHub npm repository**


接著執行 **yarn build** 且待測試都成功通過後，將會看到 **dist** 會裡面有 **js** 資料夾且已經內含有準備好發佈的 node package library


```bash
# command
ls -R dist/

# result
dist/:
js

dist/js:
github-username@0.0.0.jsii.tgz 
```

{{% notice tip %}}
發佈到 GitHub npm repository 需要的 Git PAT TOKEN.請將其設定在環境變數中。
{{% /notice %}}

```bash
export GITHUB_USERNAME=REPACE_GITHUB_USERNAME
export NPM_TOKEN=REPACE_GITHUB_PAT_TOKEN
```

{{% notice note %}}
創建 **.npmrc** 設定檔，指定的 **npm scope library** 從 **GitHub npm repository** 讀取。
{{% /notice %}}

```bash
touch .npmrc
echo "@$GITHUB_USERNAME:registry=http://npm.pkg.github.com/" >> .npmrc
echo "//npm.pkg.github.com/:_authToken=$NPM_TOKEN" >> .npmrc
```

### 輸出範例
```bash
cat .npmrc
@neilkuan:registry=http://npm.pkg.github.com/
//npm.pkg.github.com/:_authToken=ghp_oxxxxxxxxxxxxxxxxxxxxx
```

Package 的版本號是與 **package.json** 內的 **version** 連動，所以可以靠修改 **version** 來指定欲發佈的版本。

最後執行下列指令，便可以發佈到 **GitHub npm repository**
```bash
yarn release
```