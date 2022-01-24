---
title: "Gitpod - 開發"
chapter: false
weight: 433
---

## 使用 Gitpod 開發 Custom Construct (2/2)

### 開始創建 Custom Construct Library

打開 **src/main.ts** ，可以觀察到這裡已經定義了一個 **Class** 叫做 **CdkCustomConstruct** ，並且宣告了 **new CdkCustomConstruct()** ，這將會幫助我們創建一個 S3 Bucket。

![Untitled](../images/04311.png)

現在我們將 **new cdk.aws_s3.Bucket(this, 'Bucket', {});**  移除，打造一個全新的 **ec2 alarm instance** construct.

如果沒有頭緒也沒關係，下面這邊附上 **src/main.ts** 改動好的範例，供參考。

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

就軟體工程的實踐，這邊也需要同步進行修改 **test/cdk-construct-gitpod-template.test.ts** 以方便讓 AWS CDK 謝祝我們進行單元測試。

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

改動完成之後，只要執行 **yarn test** 便可進行單元測試

![Untitled](../images/04312.png)

接者修改 **src/integ-dev.ts** 內容，便可以進行測試部署。

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

重頭戲來了，開始進行測試部署。

{{% notice warning %}}
為了避免發生權限問題，請記得使用 aws configure 設置
{{% /notice %}}

```bash
# synth - Synthesizes and prints the CloudFormation template for the specified stack(s)
cdk synth --app='npx ts-node ./src/integ-dev.ts'

# diff - Compares the specified stack with the deployed stack or a local CloudFormation template
cdk diff --app='npx ts-node ./src/integ-dev.ts'

# deploy - Deploys the specified stack(s)
cdk deploy --app='npx ts-node ./src/integ-dev.ts'

# destroy - Destroys the specified stack(s)
cdk destory --app='npx ts-node ./src/integ-dev.ts'
```