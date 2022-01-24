---
title: "發佈到 GitHub npm"
chapter: false
weight: 44
---

## 建立 GitHub personal access token

- 請參考 GitHub 文件，建立個人用的 Personal access tokens (PATs)
  - [Creating a personal access token](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token)
- PATs 需要的權限為:
    - repo:* 
    - write:packages 📦
    - read:packages  📦 
    - (optional:  delete:packages 📦)
    
    ![Untitled](images/04401.png)
    

## Publish package to github npm repository

- 設定 **.npmrc** 檔案，讓 npm 知道你要發布到 **GitHub npm repository**

{{% notice note %}}
在執行 yarn build 且測試都成功通過後，你將會看到，dist 會有 js 資料夾內，將會有準備好發布的 node package library
{{% /notice %}}

```bash
ls -R dist/
dist/:
js

dist/js:
github-username@0.0.0.jsii.tgz 
```

{{% notice tip %}}
發布到 GitHub npm repository 需要的 Git PAT TOKEN.請將其設定在環境變數中。
{{% /notice %}}

```bash
export NPM_TOKEN=REPACE_GITHUB_PAT_TOKEN
```

{{% notice note %}}
創建 **.npmrc** 設定檔，指定的 **npm scope library** 從 **GitHub npm repository** 讀取。
{{% /notice %}}

```bash
touch .npmrc
echo "@REPACE_GITHUB_USERNAME:registry=http://npm.pkg.github.com/" >> .npmrc
echo "//npm.pkg.github.com/:_authToken=REPACE_GITHUB_PAT_TOKEN" >> .npmrc
```

### 輸出範例
```bash
cat .npmrc
@neilkuan:registry=http://npm.pkg.github.com/
//npm.pkg.github.com/:_authToken=ghp_oxxxxxxxxxxxxxxxxxxxxx
```

- 發布之前可以修改 **package.json** 內的 **version** 來指定發布的版本。
- 發布到 **GitHub npm repository**

{{% notice tip %}}
release tag by change package.json **version**，發佈 version 將會跟著 package.json 內的 version
{{% /notice %}}

```bash
yarn release
```