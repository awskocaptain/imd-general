---
description: >-
  이 랩은 기본 VPC환경에서 Auto Scaling Group을 생성하고 EC2 자원을 생성하고 축소하는 과정을 포함합니다.. 
  (update : 2021-03-15)
---

# AutoScale

## **소개** 

AWS Auto Scaling은 애플리케이션을 모니터링하고 용량을 자동으로 조정하여, 최대한 저렴한 비용으로 안정적이고 예측 가능한 성능을 유지합니다. AWS Auto Scaling을 사용하면 몇 분 만에 손쉽게 여러 서비스 전체에서 여러 리소스에 대해 애플리케이션 규모 조정을 설정할 수 있습니다. 이 서비스는 간단하면서도 강력한 사용자 인터페이스를 제공하므로 이를 사용하여 [Amazon EC2](https://aws.amazon.com/ec2/) 인스턴스와 스팟 플릿, [Amazon ECS](https://aws.amazon.com/ecs/) 작업, [Amazon DynamoDB](https://aws.amazon.com/dynamodb/) 테이블 및 인덱스, [Amazon Aurora](https://aws.amazon.com/aurora/) 복제본 등 리소스에 대한 규모 조정 계획을 수립할 수 있습니다. AWS Auto Scaling을 사용하면 성능과 비용을 최적화하거나 둘 사이의 적절한 균형을 유지하기 위한 권장 사항을 활용해 간단하게 규모를 조정할 수 있습니다. 이미 [Amazon EC2 Auto Scaling](https://aws.amazon.com/ec2/autoscaling/)을 사용하여 Amazon EC2 인스턴스의 규모를 동적으로 조정하고 있는 경우, 이제 AWS Auto Scaling과 결합하여 다른 AWS 서비스의 추가 리소스를 조정할 수 있습니다. AWS Auto Scaling을 사용하면 항상 적시에 올바른 리소스가 애플리케이션에 할당됩니다.

[AWS Management Console](https://console.aws.amazon.com/console/home), 명령줄 인터페이스\(CLI\) 또는 SDK를 사용하면 AWS Auto Scaling을 손쉽게 시작할 수 있습니다. AWS Auto Scaling은 추가 요금 없이 사용할 수 있습니다. 애플리케이션을 실행하는 데 필요한 AWS 리소스와 [Amazon CloudWatch](https://aws.amazon.com/cloudwatch/) 모니터링 요금만 지불하면 됩니다.

## 목적 

본 랩은 아래와 같은 구성을 통해 EC2 인스턴스들의 Auto Scaling을 확인하는 데 도움을 드립니다.

![](../.gitbook/assets/image%20%28126%29.png)

* **EC2 생성**
* **Auto Scaling 시작 구성 방법**
* **Auto Scaling Group 생성**
* **Auto Scaling Group 을 통한 EC2 증가와 감소 이**

## Task1. AutoScaling 을 위한 EC2 생성

### 1. AutoScaling Group을 위한 EC2 인스턴스 생성

앞서 생성한 [EC2-LINUX](ec2-linux.md) 에서 처럼 EC2를 생성합니다.

* **AMI - Amazon Linux 2 AMI \(HVM\), SSD Volume Type - ami-0e17ad9abf7e5c818 \(64비트 x86\)**

![](../.gitbook/assets/image%20%28480%29.png)

* **인스턴스 유형 선택 - t2.micro**

![](../.gitbook/assets/image%20%28454%29.png)

* **인스턴스 세부 정보 구성** 
  * 개수 - 1
  * 네트워크 - IMD-VPC
  * 서브넷 - IMD-PUBLIC-A
  * 퍼블릭 IP 자동 활당 - 활성화
  * 사용자 데이터

```text
#!/bin/sh
sudo yum -y update
sudo yum -y install yum-utils 
sudo yum -y install https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
sudo yum -y install iotop iperf3 iptraf tcpdump git bash-completion 
sudo yum -y install httpd php mysql php-mysql 
sudo yum -y install python-pip
sudo yum -y install nethogs iftop lnav nmon tmux wireshark vsftpd ftp stress
sudo systemctl start httpd
sudo systemctl enable httpd
cd /var/www/html/
sudo git clone https://github.com/whchoi98/ec2meta-webpage.git
sudo systemctl restart httpd
exit

```

![](../.gitbook/assets/image%20%28484%29.png)

![](../.gitbook/assets/image%20%28511%29.png)

* **스토리지 추가 - 볼륨유형 gp3**

![](../.gitbook/assets/image%20%28452%29.png)

* **태그 추가 - 키 : Name , 값 : ASG-EC2**

![](../.gitbook/assets/image%20%28494%29.png)

* **보안 그룹 구성 - 기존 보안 그룹 선택 - IMD-PUB-SG**

![](../.gitbook/assets/image%20%28422%29.png)

* 기존 키 페어 선택 - IMD-PUB-PUTTY 

![](../.gitbook/assets/image%20%28477%29.png)

### 2. 생성된 AutoScale Group 용 인스턴스 확인

AutoScale Group 용으로 생성된 인스턴스를 확인합니다. 해당 인스턴스는 AutoScale Group을 위한 인스턴스 템플릿으로 생성할 것입니다.

![](../.gitbook/assets/image%20%28460%29.png)

### 3. EC2 시작 템플릿 만들기

인스턴스 메뉴에서 생성한 인스턴스를 선택 - 작업 - 이미지 및 템플릿 - 인스턴스에서 템플릿 생성을 선택합니다.

![](../.gitbook/assets/image%20%28459%29.png)

**아래에서 처럼 시작 템플릿 이름을 선언하고, 나머지 값은 그대로 사용합니다.**

* **시작 템플릿 이름 - MyTemplate**
* **나머지 값은 기본 값을 그대로 사용하고, 시작 템플릿을 선택합니다.**

![](../.gitbook/assets/image%20%28473%29.png)

* 시작 템플릿이 완성되었습니다. 해당 템플릿을 이용해서 Auto Scaling Group을 만들 것입니다.

![](../.gitbook/assets/image%20%28472%29.png)



## Task2 : Auto Scaling 시작 구성 \(Auto Scaling Launch Config\)

### 4.시작 구성 생성

* **EC2 대시보드 - AutoScaling - Auto Scaling Groups \(새로 만들기\) 를 선택합니다.**

![](../.gitbook/assets/image%20%28508%29.png)

### **5. 시작 템플릿 구성** 

* Auto Scaling 그룹 이름을 선언하고, 앞서 생성한 시작 템플릿을 선택합니다.

```text
ASG-Template
```

![](../.gitbook/assets/image%20%28500%29.png)

### 6. Auto Scaling 설정 구성 

![](../.gitbook/assets/image%20%28492%29.png)

* 시작 템플릿 준수 선택
* VPC - IMD-VPC 선택
* 서브넷 - AutoScaling Group을 배포할 Subnet 선택 

{% hint style="info" %}
여러개 서브넷에 배포하는 방식은 ELB와 함께 구성합니다. 여기에서는 AutoScaling Group 동작 방식을 보기 위해 구성하는 것입니다.
{% endhint %}

### 7. 고급 옵션 구성

테스트를 위해서 상태 확인 유예 기간을 10초로 조정하고, 모니터링 Cloudwatch 내에서 그룹 지표 수집 활성화를 선택합니다.

![](../.gitbook/assets/image%20%28507%29.png)

{% hint style="info" %}
대부분의 경우, 서비스 상태가 된 직후의 Auto Scaling 인스턴스는 웜 업을 거쳐야 상태 확인을 통과할 수 있습니다. Amazon EC2 Auto Scaling은 상태 확인 유예 기간이 끝날 때까지 기다린 후 인스턴스의 상태를 확인합니다. Amazon EC2 상태 확인과 Elastic Load Balancing 상태 확인은 상태 확인 유예 기간이 끝나기 전에 완료될 수 있습니다. 하지만 Amazon EC2 Auto Scaling은 상태 확인 유예 기간이 종료되기 전에는 그러한 상태를 반영하지 않습니다. 인스턴스에 충분한 웜 업 시간을 제공하려면 상태 확인 유예 기간이 애플리케이션의 예상 시작 시간을 포함하도록 해야 합니다. 수명 주기 후크를 추가할 경우 유예 기간은 수명 주기 후크 작업이 완료되고 인스턴스가 `InService` 상태로 전환되기까지 시작되지 않습니다.

상태 확인 유예 기간은 초 단위입니다. 따라서 예를 들어 300초를 지정하면 5분 간격이 생깁니다.
{% endhint %}

### 8. 그룹 크기 및 조정 정책 구성

![](../.gitbook/assets/image%20%28493%29.png)

### 9. 알림 추가

![](../.gitbook/assets/image%20%28433%29.png)

### 10. 태그 추가

![](../.gitbook/assets/image%20%28478%29.png)

### 11. 검토

* **AutoScaling 그룹 생성을 완료합니다.**
* 알림 수신을 설정한 이메일에 **SNS 승인요청**이 기다리고 있습니다. 수락하시기 바랍니다.

![](../.gitbook/assets/image%20%28474%29.png)

![](../.gitbook/assets/image%20%28438%29.png)

잠시 후 아래와 같이 인스턴스가 생성된 것을 확인 할 수 있습니다.

![](../.gitbook/assets/image%20%28513%29.png)

Auto Scaling 그룹에 생성된 그룹을 선택하고 , 인스턴스 관리 탭을 확인 해 봅니다.

![](../.gitbook/assets/image%20%28421%29.png)

### 12. Auto Scaling 증가 확인

* 생성된 각각의 EC2 인스턴스에서 CPU 로드를 생성합니다. 화면을 분할하고 "top" 명령을 통해 CPU Load를 확인합니다.

{% hint style="info" %}
생성된 EC2 인스턴스에 tmux 가 설치되어 있습니다. 화면 분할을 통해 명령을 top 를 입력해 봅니다. 

화면 분할 - ctrl + b , %  
화면 이동 - ctrl + b,  이동
{% endhint %}

```text
# 600초 동안 CPU 1개 스트레
sudo stress --cpu 1 --timeout 600
```

{% hint style="info" %}
AutoScaling Group에 할당한 인스턴스 타입의 CPU 숫자에 맞추어서 위의 명령을 조정합니다.
{% endhint %}

![](../.gitbook/assets/image%20%2891%29.png)

* EC2 인스턴스의 모니터링에서 Cloudwatch를 통해 1분간 평균 CPU를 확인합니다.

![](../.gitbook/assets/image%20%28453%29.png)

* 수분 뒤에 인스턴스가 증가하는 지 확인합니다.  \(Auto Scaling 그룹과 EC2 대쉬보드에서 확인\)

![](../.gitbook/assets/image%20%28428%29.png)

* CPU Stress가 종료된 수분 뒤에 인스턴스가 감하는 지 확인합니다.  \(Auto Scaling 그룹과 EC2 대쉬보드에서 확인\)

앞서 생성한 Auto Scaling Group의 SNS가 계속해서 메세지를 통보하게 됩니다.

![](../.gitbook/assets/image%20%28490%29.png)

{% hint style="success" %}
성공적으로 Auto Scaling 그룹 랩을 마치셨습니다. 
{% endhint %}

