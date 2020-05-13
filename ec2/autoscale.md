# AutoScale

## **소개** 

AWS Auto Scaling은 애플리케이션을 모니터링하고 용량을 자동으로 조정하여, 최대한 저렴한 비용으로 안정적이고 예측 가능한 성능을 유지합니다. AWS Auto Scaling을 사용하면 몇 분 만에 손쉽게 여러 서비스 전체에서 여러 리소스에 대해 애플리케이션 규모 조정을 설정할 수 있습니다. 이 서비스는 간단하면서도 강력한 사용자 인터페이스를 제공하므로 이를 사용하여 [Amazon EC2](https://aws.amazon.com/ec2/) 인스턴스와 스팟 플릿, [Amazon ECS](https://aws.amazon.com/ecs/) 작업, [Amazon DynamoDB](https://aws.amazon.com/dynamodb/) 테이블 및 인덱스, [Amazon Aurora](https://aws.amazon.com/aurora/) 복제본 등 리소스에 대한 규모 조정 계획을 수립할 수 있습니다. AWS Auto Scaling을 사용하면 성능과 비용을 최적화하거나 둘 사이의 적절한 균형을 유지하기 위한 권장 사항을 활용해 간단하게 규모를 조정할 수 있습니다. 이미 [Amazon EC2 Auto Scaling](https://aws.amazon.com/ec2/autoscaling/)을 사용하여 Amazon EC2 인스턴스의 규모를 동적으로 조정하고 있는 경우, 이제 AWS Auto Scaling과 결합하여 다른 AWS 서비스의 추가 리소스를 조정할 수 있습니다. AWS Auto Scaling을 사용하면 항상 적시에 올바른 리소스가 애플리케이션에 할당됩니다.

[AWS Management Console](https://console.aws.amazon.com/console/home), 명령줄 인터페이스\(CLI\) 또는 SDK를 사용하면 AWS Auto Scaling을 손쉽게 시작할 수 있습니다. AWS Auto Scaling은 추가 요금 없이 사용할 수 있습니다. 애플리케이션을 실행하는 데 필요한 AWS 리소스와 [Amazon CloudWatch](https://aws.amazon.com/cloudwatch/) 모니터링 요금만 지불하면 됩니다.

## 목적 



## Task1 : Auto Scaling 시작 구성 \(Auto Scaling Launch Config\)

### 1.시작 구성 생성

* **EC2 대시보드 - Auto Scaling - 시작 구성**을 선택하고, **시작 구성 생성**을 선택합니다.

![](../.gitbook/assets/image%20%2860%29.png)

### 2. AMI 선택

* Auto Scaling 구성에 포함 EC2 인스턴스의 이미지를 선택합니다. \(랩에서는 Amazon Linux 2 AMI 선택\)

![](../.gitbook/assets/image%20%28242%29.png)

### 3.인스턴스 유형 선택

* Auto Scaling 구성에 포함 EC2 인스턴스의 유형을 선택합니다. \(랩에서는 T2.micro를 선택\)

![](../.gitbook/assets/image%20%28124%29.png)

### 4. 세부정보 구성

* Auto Scaling 시작 구성의 이름을 생성합니다.
* User Data를 작성합니다.

```text

```

* Public IP 할당 유무를 선택합니다. Public Subnet에 할당될 EC2 인스턴스일 경우에는 **"모든 인스턴스에 퍼블릭 IP 주소 할당"**을 선택합니다.

![](../.gitbook/assets/image%20%2814%29.png)

### 5. 스토리지 추가

* AutoScaling 그룹에 포함되는 EC2의 디스크를 유형을 선택합니다.

![](../.gitbook/assets/image%20%28134%29.png)

### 6. 보안 그룹 구성

* 새롭게 보안 그룹을 생성하거나 , **기존에 생성된 보안 그룹을 선택**합니다. \(랩에서는 앞서 EC2-LINUX 랩에서 생성한 Security Group - **IMD-PUB-SG**를 선택\)

![](../.gitbook/assets/image%20%28116%29.png)

### 7. 키페어 생성 또는 선택

* EC2에 포함될 키 페어를 선택합니다. \(랩에서는 앞서 EC2-LINUX랩에서 생성한 키페어를 선택합니다.\)

![](../.gitbook/assets/image%20%28310%29.png)

Auto Scaling  시작 구성 생성을 선택하고, 완료합니다.

## Task2: Auto Scaling 그룹

### 8. Auto Scaling 그룹 생성

* Auto Scaling 그룹 생성을 선택합니다.

![](../.gitbook/assets/image%20%28233%29.png)



![](../.gitbook/assets/image%20%28268%29.png)

![](../.gitbook/assets/image%20%28107%29.png)

![](../.gitbook/assets/image%20%2866%29.png)

![](../.gitbook/assets/image%20%28203%29.png)

![](../.gitbook/assets/image%20%2818%29.png)

![](../.gitbook/assets/image%20%28287%29.png)

![](../.gitbook/assets/image%20%28243%29.png)

![](../.gitbook/assets/image%20%28171%29.png)

![](../.gitbook/assets/image%20%28277%29.png)

![](../.gitbook/assets/image%20%2887%29.png)

![](../.gitbook/assets/image%20%28259%29.png)

![](../.gitbook/assets/image%20%28308%29.png)

![](../.gitbook/assets/image%20%2846%29.png)

![](../.gitbook/assets/image%20%28234%29.png)

![](../.gitbook/assets/image%20%28285%29.png)

![](../.gitbook/assets/image%20%28215%29.png)

![](../.gitbook/assets/image%20%2815%29.png)



