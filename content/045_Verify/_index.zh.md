---
title: "驗證 Custom Construct"
chapter: false
weight: 45
---

## 建立新 CDK 專案進行測試驗證

```bash
mkdir demo-project
cd demo-project
cdk init -l typescript
```

## 從你的 GitHub npm 安裝 Package

使用 [發佈到 GitHub npm](044_publish.html#輸出範例.html) 完成的成果，從上層資料夾內的 **.npmrc** 複製到此資料夾中

```bash
cp ../.npmrc ./
```

確認資料夾內的 **.npmrc** 格式為下
```bash
cat .npmrc
@neilkuan:registry=http://npm.pkg.github.com/
//npm.pkg.github.com/:_authToken=ghp_oxxxxxxxxxxxxxxxxxxxxx
```

從 GitHub npm repository 安裝你的 custom construct library
```bash
npm install @github-username/project-name
```

## 引入你的 Custom Construct

```tsx
import { Stack, StackProps } from 'aws-cdk-lib';
import { Construct } from 'constructs';
import * as custom_cdk from '@github-username/project-name';

export class DemoCdkStack extends Stack {
  constructor(scope: Construct, id: string, props?: StackProps) {
    super(scope, id, props);

    new custom_cdk.CdkCustomConstruct(this, 'hello');
  }
}
```

## 部署你的 Custom Construct

```bash
# synth - Synthesizes and prints the CloudFormation template for the specified stack(s)
cdk synth

# deploy - Deploys the specified stack(s)
cdk deploy
```