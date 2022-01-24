---
title: "深入探討 L1 & L2 Construct"
chapter: false
weight: 42
---

## Simple Example, S3

![Untitled](images/04201.png)

### L1 Structure

### L2 Structure

## Compare L1 & L2

# Dive Deep Example, ECS Fargate runtime

Ref: 

[feat(aws-ecs): support runtime platform property for create fargate windows runtime. by neilkuan · Pull Request #17622 · aws/aws-cdk](https://github.com/aws/aws-cdk/pull/17622/files)

```tsx
// aws-cdk-lib@2.6.0
// you can not define runtime platform in `FargateTaskDefinition`.
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

// aws-cdk-lib@2.7.0
// you can define runtime platform in `FargateTaskDefinition`
// https://docs.aws.amazon.com/cdk/api/v2/docs/aws-cdk-lib.aws_ecs.FargateTaskDefinition.html
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

- [ ]  Compare 2.6.0 and 2.7.0 **FargateTaskDefinition** document