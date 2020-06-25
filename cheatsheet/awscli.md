---
description: awscli 유용한 명령
---

# awscli

## AWS CLI 환경 구성

AWS 명령줄 인터페이스\(CLI\)는 AWS 서비스를 관리하는 통합 도구입니다. 도구 하나만 다운로드하여 구성하면 여러 AWS 서비스를 명령줄에서 제어하고 스크립트를 통해 자동화할 수 있습니다.

![](../.gitbook/assets/image%20%2873%29.png)

## Region List

```text
aws ec2 describe-regions --output table
```

## Region AZ List

```text
aws ec2 describe-availability-zones --region us-east-1 --output text
```

## Instance list

```text
aws ec2 describe-instances --query 'Reservations[].Instances[].[Placement.AvailabilityZone, State.Name, InstanceId, InstanceType,VpcId,SubnetId,ImageId,Tags[?Key==Name].Value|[0]]' --output table
```

```text
aws ec2 describe-instances --query 'Reservations[].Instances[].[Tags[?Key==Name] | [0].Value, InstanceId, State.Name, PrivateIpAddress, PublicIpAddress ]' --output table
```

## My instance list

```text
aws ec2 describe-instances --query 'Reservations[].Instances[].[Tags[?Key==Name] | [0].Value, Placement.AvailabilityZone,InstanceId, InstanceType, ImageId,State.Name, PrivateIpAddress, PublicIpAddress ]' --output table
```

```text
aws ec2 describe-instances --query 'Reservations[].Instances[].[Tags[?Key==Name] | [0].Value, Placement.AvailabilityZone,State.Name, VpcId,SubnetId,PrivateIpAddress, PublicIpAddress ]' --output table

```

## My instance start

```text
aws ec2 start-instances --instance-ids i-XXXXXXXX --output text | grep -w CURRENTSTATE
```

## My instance stop

```text
aws ec2 stop-instances --instance-ids i-XXXXXXXX --output text | grep -w CURRENTSTATE
```

## Spot instance price history

```text
aws ec2 describe-spot-price-history --start-time $(date -u +"%Y%m%dT%H0000") --product "Linux/UNIX" --instance-type "m3.medium" --region us-east-1 --output table
```



