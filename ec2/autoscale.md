# AutoScale

## **소개** 

AWS Auto Scaling은 애플리케이션을 모니터링하고 용량을 자동으로 조정하여, 최대한 저렴한 비용으로 안정적이고 예측 가능한 성능을 유지합니다. AWS Auto Scaling을 사용하면 몇 분 만에 손쉽게 여러 서비스 전체에서 여러 리소스에 대해 애플리케이션 규모 조정을 설정할 수 있습니다. 이 서비스는 간단하면서도 강력한 사용자 인터페이스를 제공하므로 이를 사용하여 [Amazon EC2](https://aws.amazon.com/ec2/) 인스턴스와 스팟 플릿, [Amazon ECS](https://aws.amazon.com/ecs/) 작업, [Amazon DynamoDB](https://aws.amazon.com/dynamodb/) 테이블 및 인덱스, [Amazon Aurora](https://aws.amazon.com/aurora/) 복제본 등 리소스에 대한 규모 조정 계획을 수립할 수 있습니다. AWS Auto Scaling을 사용하면 성능과 비용을 최적화하거나 둘 사이의 적절한 균형을 유지하기 위한 권장 사항을 활용해 간단하게 규모를 조정할 수 있습니다. 이미 [Amazon EC2 Auto Scaling](https://aws.amazon.com/ec2/autoscaling/)을 사용하여 Amazon EC2 인스턴스의 규모를 동적으로 조정하고 있는 경우, 이제 AWS Auto Scaling과 결합하여 다른 AWS 서비스의 추가 리소스를 조정할 수 있습니다. AWS Auto Scaling을 사용하면 항상 적시에 올바른 리소스가 애플리케이션에 할당됩니다.

[AWS Management Console](https://console.aws.amazon.com/console/home), 명령줄 인터페이스\(CLI\) 또는 SDK를 사용하면 AWS Auto Scaling을 손쉽게 시작할 수 있습니다. AWS Auto Scaling은 추가 요금 없이 사용할 수 있습니다. 애플리케이션을 실행하는 데 필요한 AWS 리소스와 [Amazon CloudWatch](https://aws.amazon.com/cloudwatch/) 모니터링 요금만 지불하면 됩니다.

## 목적 



## Task1 : Auto Scaling 시작 구성 \(Auto Scaling Launch Config\)

### 1.시작 구성 생성

* **EC2 대시보드 - Auto Scaling - 시작 구성**을 선택하고, **시작 구성 생성**을 선택합니다.

![](../.gitbook/assets/image%20%2861%29.png)

### 2. AMI 선택

* Auto Scaling 구성에 포함 EC2 인스턴스의 이미지를 선택합니다. \(랩에서는 Amazon Linux 2 AMI 선택\)

![](../.gitbook/assets/image%20%28246%29.png)

### 3.인스턴스 유형 선택

* Auto Scaling 구성에 포함 EC2 인스턴스의 유형을 선택합니다. \(랩에서는 T2.micro를 선택\)

![](../.gitbook/assets/image%20%28126%29.png)

### 4. 세부정보 구성

* Auto Scaling 시작 구성의 이름을 생성합니다.
* User Data를 작성합니다.

```text
#!/bin/sh
sudo yum -y update
sudo yum -y install yum-util
sudo yum -y install https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm 
sudo yum -y install httpd php mysql php-mysql git stress tmux
sudo systemctl start httpd
sudo systemctl enable httpd
cd /var/www/html/
sudo git clone https://github.com/whchoi98/ec2meta-webpage.git
sudo systemctl restart httpd
```

* Public IP 할당 유무를 선택합니다. Public Subnet에 할당될 EC2 인스턴스일 경우에는 **"모든 인스턴스에 퍼블릭 IP 주소 할당"**을 선택합니다.

![](../.gitbook/assets/image%20%2814%29.png)

### 5. 스토리지 추가

* AutoScaling 그룹에 포함되는 EC2의 디스크를 유형을 선택합니다.

![](../.gitbook/assets/image%20%28136%29.png)

### 6. 보안 그룹 구성

* 새롭게 보안 그룹을 생성하거나 , **기존에 생성된 보안 그룹을 선택**합니다. \(랩에서는 앞서 EC2-LINUX 랩에서 생성한 Security Group - **IMD-PUB-SG**를 선택\)

![](../.gitbook/assets/image%20%28117%29.png)

### 7. 키페어 생성 또는 선택

* EC2에 포함될 키 페어를 선택합니다. \(랩에서는 앞서 EC2-LINUX랩에서 생성한 키페어를 선택합니다.\)

![](../.gitbook/assets/image%20%28315%29.png)

Auto Scaling  시작 구성 생성을 선택하고, 완료합니다.

## Task2: Auto Scaling 그룹

### 8. Auto Scaling 그룹 생성

* Auto Scaling 그룹 생성을 선택합니다.

![](../.gitbook/assets/image%20%28237%29.png)

### 9. Auto Scaling 그룹 생성과 시작 구성 선

* 앞서 생성한 **Auto Scaling 시작 구성 \(AS-Config01\)을 선택**하고, **시작 구성을 선택**합니다.

![](../.gitbook/assets/image%20%28119%29.png)

### 10. Auto Scaling 그룹 세부 정보 구성

* Auto Scaling 그룹 세부 정보를 구성합니다.
* 그룹 이름 - Auto Scaling 그룹 이름을 입력합니다.
* 그룹 크기 - Auto Scaling 그룹에서 생성되는 최소 EC2 숫자를 입력합니다.
* 네트워크 - 생성되어 있는 VPC를 선택합니다.
* 서브넷 - Auto Scaling 그룹에 포함되어 있는 EC2가 배포될 서브넷을 선택합니다.
* 상태 검사 유예 기간 - EC2 Instance가 생성 후 기본 설정이 완료될 때까지는 Health Check를 정상적으로 수행할 수 없기 때문에 설정하는 유예 시간입니다. \(랩에서는 테스트를 위해서 10초로 설정했으나, 기본값은 300초 입니다.\)

{% hint style="info" %}
대부분의 경우, 서비스 상태가 된 직후의 Auto Scaling 인스턴스는 웜 업을 거쳐야 상태 확인을 통과할 수 있습니다. Amazon EC2 Auto Scaling은 상태 확인 유예 기간이 끝날 때까지 기다린 후 인스턴스의 상태를 확인합니다. Amazon EC2 상태 확인과 Elastic Load Balancing 상태 확인은 상태 확인 유예 기간이 끝나기 전에 완료될 수 있습니다. 하지만 Amazon EC2 Auto Scaling은 상태 확인 유예 기간이 종료되기 전에는 그러한 상태를 반영하지 않습니다. 인스턴스에 충분한 웜 업 시간을 제공하려면 상태 확인 유예 기간이 애플리케이션의 예상 시작 시간을 포함하도록 해야 합니다. 수명 주기 후크를 추가할 경우 유예 기간은 수명 주기 후크 작업이 완료되고 인스턴스가 `InService` 상태로 전환되기까지 시작되지 않습니다.

상태 확인 유예 기간은 초 단위입니다. 따라서 예를 들어 300초를 지정하면 5분 간격이 생깁니다.
{% endhint %}

![](../.gitbook/assets/image%20%28196%29.png)

### 11. 조정 정책 구성

* Auto Scaling 그룹 내의 EC2가 증가하거나 축소되는 조건을 설정합니다.
* 최소 2개에서 최대 8개까지 증가 또는 감소 조건으로 구성합니다.
* EC2가 증가하는 조건 - EC2 의 1분간 평균 CPU 70% 이상이면 , 2개씩 EC2 증가
* EC2가 감소하는 조건 - EC2 의 1분간 평균 CPU 20% 이하이면 , 1개씩 EC2 감

![](../.gitbook/assets/image%20%28270%29.png)

![](../.gitbook/assets/image%20%2867%29.png)

![](../.gitbook/assets/image%20%28207%29.png)

### 12. 알림 구성

* EC2 인스턴스의 시작 또는 종료 , 시작 실패, 종료 실패에 대한 알림 전송.

![](../.gitbook/assets/image%20%28292%29.png)

* 해당 이메일에서 확인하고, 알림 수신을 수락합니다.

![](../.gitbook/assets/image%20%2829%29.png)

### 13. 태그 구성

* 태그를 구성하지 않으면, EC2를 식별하기 어렵습니다. EC2에 대한 태그를 설정합니다. \(랩에서는 Key-ASG , 값-01 로 설정\)

![](../.gitbook/assets/image%20%28183%29.png)

* 검토를 완료합니다.

### 14. Auto Scaling 그룹 확인 

* 생성된 Auto Scaling 그룹에서 EC2가 생성되는 지 확인합니다. [10번 단계](autoscale.md#10-auto-scaling)에서 최소 인스턴스 단위를 2개로 설정했기 때문에 2개의 EC2 인스턴스가 생성됩니다.

![](../.gitbook/assets/image%20%28173%29.png)

* EC2 대쉬보드에서도 정상적으로 2개의 EC2 인스턴스가 생성된 것을 확인 할 수 있습니다.

![](../.gitbook/assets/image%20%28282%29.png)

### 15. Auto Scaling 증가 확인

* 생성된 각각의 EC2 인스턴스에서 CPU 로드를 생성합니다. "top" 명령을 통해 CPU Load를 확인합니다.

{% hint style="info" %}
생성된 EC2 인스턴스에 tmux 가 설치되어 있습니다. 화면 분할을 통해 명령을 top 를 입력해 봅니다. 

화면 분할 - ctrl + b , "  
화면 이동 - ctrl + 화살
{% endhint %}

```text
sudo stress --cpu 1 --timeout 320
```

![](../.gitbook/assets/image%20%2888%29.png)

* EC2 인스턴스의 모니터링에서 Cloudwatch를 통해 1분간 평균 CPU를 확인합니다.

![](../.gitbook/assets/image%20%28263%29.png)

* 수분 뒤에 인스턴스가 증가하는 지 확인합니다.  \(Auto Scaling 그룹과 EC2 대쉬보드에서 확인\)

![](../.gitbook/assets/image%20%28313%29.png)

![](../.gitbook/assets/image%20%2847%29.png)

![](../.gitbook/assets/image%20%28238%29.png)

![](../.gitbook/assets/image%20%28290%29.png)

* CPU Stress가 종료된 수분 뒤에 인스턴스가 감하는 지 확인합니다.  \(Auto Scaling 그룹과 EC2 대쉬보드에서 확인\)

![](../.gitbook/assets/image%20%28313%29.png)

![](../.gitbook/assets/image%20%28219%29.png)

![](../.gitbook/assets/image%20%2815%29.png)

{% hint style="success" %}
성공적으로 Auto Scaling 그룹 랩을 마치셨습니다. Auto Scaling 그룹을 삭제합니다.
{% endhint %}

