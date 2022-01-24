---
title: "建立 Custom Construct"
chapter: false
weight: 43
---

## Example, EC2 Alarm

[https://github.com/neilkuan/cdk-demo-construct](https://github.com/neilkuan/cdk-demo-construct)

[cdk-demo-construct/index.ts at main · neilkuan/cdk-demo-construct](https://github.com/neilkuan/cdk-demo-construct/blob/main/src/index.ts)

[cdk-demo-construct/integ.default.ts at main · neilkuan/cdk-demo-construct](https://github.com/neilkuan/cdk-demo-construct/blob/main/src/integ.default.ts)

### Use github [repo](https://github.com/neilkuan/cdk-construct-gitpod-template) template to create project

- 點擊 **Use this template** 按鈕

![Untitled](images/04301.png)

- 輸入 **Repository name**
- 選擇 **Public**  或是 **Private**

{{% notice note %}}
選擇 **Public** 就是創建公開大家都看得見的 Repository，選擇 **Private** 就是創建私有大家看不見的 Repository，目前實測 Pub
{{% /notice %}}

![Untitled](images/04302.png)

### Develop CDK Custom Construct at `Gitpod`

- Create a new gitpod workspace using the this repo. ([sample video](https://youtu.be/w65POyu3ZUQ))

```bash
# example
# https://gitpod.io/#[github repo url]
https://gitpod.io/#https://github.com/neilkuan/cdk-construct-demo
```

![Untitled](images/04303.png)

- 沒有使用過 **gitpod** 的用戶，可以透過 github account 進行OAuth login

![Untitled](images/04304.png)

- 他將會由我所定義的 **.gitpod.yml** 進行 ，

![Untitled](images/04305.png)

- 並推送到 gitpod 內部 container registry ，並 pull image下來給您的 workspace 使用

![Untitled](images/04306.png)

- 執行到此畫面，表示已經準備好可以開發

![Untitled](images/04307.png)

- 打開 terminal 執行 **yarn** 安裝所需的package

![Untitled](images/04308.png)

- 接著進行 **package.json** 的配置，參照 **DEV_README.md** 的說明文件進行設定。

- Note: 開啟一個 **split terminal** 執行 **yarn watch** 即時檢查 **Compiles** 錯誤訊息**。**

![Untitled](images/04309.png)

- 即時發現 **Compiles** 錯誤訊息**。**


```
2022-01-20T11:49:17.715] [ERROR] jsii/compiler - Compilation errors prevented the JSII assembly from being created
src/integ-dev.ts(13,3): error TS18004: No value exists in scope for the shorthand property 'vpc'. Either declare one or provide an initializer.
```



![Untitled](images/04310.png)

### Start to build Custom Construct library

- 打開 **src/main.ts** ，將會發現，已定義了一個 **Class** 叫做 **CdkCustomConstruct** ，並且定義了 **new CdkCustomConstruct()** 將會幫你創建一個 S3 Bucket。

![Untitled](images/04311.png)

- 我們將 **new cdk.aws_s3.Bucket(this, 'Bucket', {});**  移除，打造一個 **ec2 alarm instance** construct.

{{% notice note %}}
以下為範例 code。 **src/main.ts**
{{% /notice %}}

```tsx
import * as cdk from 'aws-cdk-lib';
import { Construct } from 'constructs';

export interface CdkCustomConstructProps {
  readonly userData?: cdk.aws_ec2.UserData;
  readonly vpc: cdk.aws_ec2.IVpc;
  readonly topic?: cdk.aws_sns.Topic;
  readonly notifyMail?: string[];
}

export class CdkCustomConstruct extends Construct {
  public instance: cdk.aws_ec2.Instance;
  public topic: cdk.aws_sns.Topic;
  constructor(scope: Construct, id: string, props: CdkCustomConstructProps) {
    super(scope, id);
    this.instance = new cdk.aws_ec2.Instance(this, 'Instance', {
      instanceName: this.node.id,
      instanceType: new cdk.aws_ec2.InstanceType('t3.micro'),
      machineImage: new cdk.aws_ec2.AmazonLinuxImage({ generation: cdk.aws_ec2.AmazonLinuxGeneration.AMAZON_LINUX_2 }),
      userData: props.userData,
      vpc: props.vpc,
    });

    const metrics = new cdk.aws_cloudwatch.Metric({
      namespace: 'AWS/EC2',
      metricName: 'CPUUtilization',
      dimensionsMap: { InstanceId: this.instance.instanceId },
    });

    const alarm = new cdk.aws_cloudwatch.Alarm(this, 'Alarm', {
      alarmDescription: 'Alarm for instance',
      metric: metrics,
      threshold: 50,
      evaluationPeriods: 1,
    });

    this.topic = props.topic ?? new cdk.aws_sns.Topic(this, 'Topic');
    alarm.addAlarmAction(new cdk.aws_cloudwatch_actions.SnsAction(this.topic));
    if (props.notifyMail) {
      props.notifyMail.forEach((mail, index) => {
        new cdk.aws_sns.Subscription(this, `AlarmSns${index}`, {
          topic: this.topic,
          protocol: cdk.aws_sns.SubscriptionProtocol.EMAIL,
          endpoint: mail,
        });
      });
    }
  }
}
```

- 修改 **test/cdk-construct-gitpod-template.test.ts** 以進行單元測試。

{{% notice note %}}
檢查 **new CdkCustomConstruct();** 會有一個 **AWS::EC2::Instance** Resource 被定義在 Cloudformation Template 當中。
{{% /notice %}}


```tsx
import * as cdk from 'aws-cdk-lib';
import { Template } from 'aws-cdk-lib/assertions';
import { CdkCustomConstruct } from '../src';

test('No Resource been Created', () => {
  const app = new cdk.App();
  // WHEN
  const stack = new cdk.Stack(app, 'MyTestStack');
  const vpc = new cdk.aws_ec2.Vpc(stack, 'vpc');
  new CdkCustomConstruct(stack, 'MyTestConstruct', {
    vpc,
  });
  // THEN
  const template = Template.fromStack(stack);

  template.resourceCountIs('AWS::EC2::Instance', 1);
});
```

- 單元測試： 執行 **yarn test**

![Untitled](images/04312.png)

- 修改 **src/integ-dev.ts** 進行測試部署。

```tsx
import * as cdk from 'aws-cdk-lib';
import { CdkCustomConstruct } from './index';

const devEnv = {
  account: process.env.CDK_DEFAULT_ACCOUNT,
  region: process.env.CDK_DEFAULT_REGION,
};

const app = new cdk.App();
const stack = new cdk.Stack(app, 'dev', { env: devEnv });
const vpc = cdk.aws_ec2.Vpc.fromLookup(stack, 'defvpc', { isDefault: true });
new CdkCustomConstruct(stack, 'MyTestConstruct', {
  vpc,
});

app.synth();
```

- 測試部署。

{{% notice warning %}}
請先設定 aws configure !!!
{{% /notice %}}

```bash
# synth
cdk synth --app='npx ts-node ./src/integ-dev.ts'

# diff
cdk diff --app='npx ts-node ./src/integ-dev.ts'

# deploy
cdk deploy --app='npx ts-node ./src/integ-dev.ts'

# destroy
cdk destory --app='npx ts-node ./src/integ-dev.ts'
```