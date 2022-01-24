---
title: "驗證 Custom Construct"
chapter: false
weight: 45
---

## Create new `cdk` project for testing

```bash
mkdir demo-project
cd demo-project
cdk init -l typescript
```

## Install package from your own GitHub npm

複製上層的資料夾內的 **.npmrc** 到此資料夾中

```bash
cp ../.npmrc ./
```

請確定當資料夾下的 **.npmrc** 格式為下
```bash
cat .npmrc
@neilkuan:registry=http://npm.pkg.github.com/
//npm.pkg.github.com/:_authToken=ghp_oxxxxxxxxxxxxxxxxxxxxx
```

從 GitHub npm repository install your custom construct library
```bash
npm install @github-username/project-name
```

## Import your own custom construct

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

## Deploy the construct

synth
```bash
cdk synth 
```

diff
```bash
cdk diff 
```

deploy
```bash
cdk deploy 
```

## *load testing