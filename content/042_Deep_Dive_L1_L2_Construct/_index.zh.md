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

CloudFormation Generate by cdk synthesize (synth)
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

CloudFormation Generate by cdk synthesize (synth)
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

## 深入探討 ECS Fargate runtime 案例

完整的案例和程式碼，請參閱 AWS CDK 的 [feat(aws-ecs): support runtime platform property for create fargate windows runtime PR](https://github.com/aws/aws-cdk/pull/17622/files)，這邊會擷取部分關鍵差異的程式碼與大家分享。

同樣是 ECS Fargate Runtime 的 **FargateTaskDefinition** 在下面 **aws-cdk-lib\@2.6.0** 和 **aws-cdk-lib\@2.7.0** 的程式碼中，我們可以看出兩者的差異。

### aws-cdk-lib\@2.6.0
在 **aws-cdk-lib\@2.6.0**，使用者沒法在 **FargateTaskDefinition** 中定義 runtime platform

```tsx
const taskdef = new ecs.TaskDefinition(this, 'TaskDef', { compatibility: ecs.Compatibility.FARGATE, memoryMiB: '2048', cpu: '1024' });

taskdef.addContainer('Container', {
      portMappings: [{ containerPort: 80 }],
      image: ecs.ContainerImage.fromRegistry('public.ecr.aws/nginx/nginx:latest-arm64v8'),
    });
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

在 **aws-cdk-lib\@2.7.0**，使用者可以在 **FargateTaskDefinition** 中定義 runtime platform

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

