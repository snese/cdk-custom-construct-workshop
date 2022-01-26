---
title: "深入探討 L1 & L2 Construct"
chapter: false
weight: 42
---

## AWS Simple Cloud Storage (S3) 的簡易範例

### L1 Construct
AWS CDK Code (TS)
```typescript
// L1 contruct library - Create S3 bucket
new s3.CfnBucket(this, 'L1bucket');
```

Synthesizes and prints the CloudFormation template for the specified stack(s) by cdk synthesize (synth)
```yaml
Resources:
  L1bucket:
    Type: AWS::S3::Bucket
    Metadata:
      aws:cdk:path: DemoCdkStack/L1bucket
```

### L2 Construct
AWS CDK Code (TS)
```typescript
// L2 contruct library -  Create S3 bucket
new s3.Bucket(this, 'L2bucket');
```

Synthesizes and prints the CloudFormation template for the specified stack(s) by cdk synthesize (synth)
```yaml
Resources:
  L2bucket6B7A9881:
    Type: AWS::S3::Bucket
    UpdateReplacePolicy: Retain
    DeletionPolicy: Retain
    Metadata:
      aws:cdk:path: DemoCdkStack/L2bucket/Resource
```


### 比較 L1 & L2 Construct

![Untitled](images/04201.png)


### L1 method for **CfnResource**

每個 L1 Construct 都是 extends **CfnResource** 這個 Class 出來的，並且每當有Cloudformation 某個服務有新的參數可以透過 Cloudformation 的 Properites 來定義時，aws-cdk 將會有一個 [cfnspce 的 PR](https://github.com/aws/aws-cdk/pull/18305)，來新增這個Properites 給該 L1 的 Construct，而且每個 L1 的 Construct 都帶有 **CfnResource** 獨特的 method，可以幫助你 **Resource Overrides** [method: addPropertyOverride](https://docs.aws.amazon.com/cdk/api/v2/docs/aws-cdk-lib.CfnResource.html#methods)，它可以幫助你在定義完 L1 Construct 之後再透過這個 method 進行 覆蓋/新增，所以就算 [cfnspce 的 PR](https://github.com/aws/aws-cdk/pull/18305) 還沒被 merged ，你也可以透過它來搶先體驗這個功能。 


![L1 method for **CfnResource**](images/04202.png)



## 深入探討 ECS Fargate runtime 案例

完整的案例和程式碼，請參閱 AWS CDK 的 [feat(aws-ecs): support runtime platform property for create fargate windows runtime PR](https://github.com/aws/aws-cdk/pull/17622/files)，這邊會擷取部分關鍵差異的程式碼與大家分享。

同樣是 ECS Fargate Runtime 的 **FargateTaskDefinition** 在下面 **aws-cdk-lib\@2.6.0** 和 **aws-cdk-lib\@2.7.0** 的程式碼中，我們可以看出兩者的差異。

### aws-cdk-lib\@2.6.0
在 **aws-cdk-lib\@2.6.0**，使用者沒法在 **FargateTaskDefinition** 中定義 runtime platform，想要搶先體驗[新功能](https://aws.amazon.com/tw/blogs/containers/running-windows-containers-with-amazon-ecs-on-aws-fargate/)，但 L2 Construct (此處是指 FargateTaskDefinition L2 Construct ) 的 Property 又還沒 suuport 此 Property ( 此處是指 runtime platform ) 去定義時可以怎麼做？

```tsx
const taskdef = new ecs.TaskDefinition(this, 'TaskDef', { compatibility: ecs.Compatibility.FARGATE, memoryMiB: '2048', cpu: '1024' });

taskdef.addContainer('Container', {
      portMappings: [{ containerPort: 80 }],
      image: ecs.ContainerImage.fromRegistry('public.ecr.aws/nginx/nginx:latest-arm64v8'),
    });
    
    // find CfnTaskDefinition back, and use addPropertyOverride() method to add RuntimePlatform Property
    (taskdef.node.defaultChild as ecs.CfnTaskDefinition).addPropertyOverride('RuntimePlatform', {
      OperatingSystemFamily: 'LINUX',
      CpuArchitecture: 'ARM64',
    });

    const fargateWindowsService = new ecs.FargateService(this, 'Service', {
      taskDefinition: taskdef,
      cluster,
      assignPublicIp: true,
    });
```

### aws-cdk-lib\@2.7.0

在 **aws-cdk-lib\@2.7.0**，使用者已可以在 **FargateTaskDefinition** 中定義 runtime platform

參考文件: [class FargateTaskDefinition (construct)](https://docs.aws.amazon.com/cdk/api/v2/docs/aws-cdk-lib.aws_ecs.FargateTaskDefinition.html)

```tsx
const taskDefinition = new ecs.FargateTaskDefinition(this, 'TaskDef', {
  runtimePlatform: {
    operatingSystemFamily: ecs.OperatingSystemFamily.LINUX,
    cpuArchitecture: ecs.CpuArchitecture.ARM64,
  },
  cpu: 1024,
  memoryLimitMiB: 2048,
});

taskDefinition.addContainer('webarm64', {
  logging: ecs.LogDriver.awsLogs({ streamPrefix: 'graviton2-on-fargate' }),
  portMappings: [{ containerPort: 80 }],
  image: ecs.ContainerImage.fromRegistry('public.ecr.aws/nginx/nginx:latest-arm64v8'),
});
```

