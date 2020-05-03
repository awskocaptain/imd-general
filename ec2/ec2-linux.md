---
description: 이 랩은 기본 VPC환경에서 EC2기반 LINUX LAMP를 설치 구성하는 랩입니다.
---

# EC2-Linux

## 소개

## 목적

## **Task1 : VPC 생성하기**

### **1.AWS 서비스-VPC 탐색**

* **"AWS Management Console - AWS 서비스"** 에서 **"서비스 찾기"** 창에 VPC를 탐색하고, VPC를 선택합니다.

![](../.gitbook/assets/image%20%2834%29.png)

### **2. VPC 선택**

* **VPC를 선택**합니다.

![](../.gitbook/assets/image%20%287%29.png)

### **3. "VPC 생성" 및 값 입력**

![](../.gitbook/assets/image%20%2842%29.png)

* **이름 태그** : VPC 이름 태그를 입력합니다.
* **IPv4 CIDR 블록** : VPC에서 사용할 IPv4 주소 대역을 입력합니다.

![](../.gitbook/assets/image%20%2862%29.png)

생성된 VPC 정보를 확인합니다.

### **4. DNS 호스트 이름을 활성화**

* 생성되는 EC2 인스턴스의 DNS Name 서비스 활성화를 위해, **"작업"**을 선택하고 **"DNS 호스트 이름 편집"**을 선택합니다. **DNS 호스트 이름을 활성화**합니다. ****

![](../.gitbook/assets/image%20%2821%29.png)

![](../.gitbook/assets/image%20%2838%29.png)

### **5.Public ,Private 서브넷 생성.**

* 좌측 VPC 대시보드에서 **"가상 프라이빗 클라우드" - "서브넷"** 메뉴를 선택하고, **"서브넷 생성"**을 선택합니다.
* 4개의 서브넷을 각각 생성하고, 속성을 정의합니다.

| **서브넷 이름 태그** | 가용영역 | **IPv4 CIDR 블록** |
| :--- | :--- | :--- |
| IMD-PUBLIC-A | ap-northeast-2a | 10.1.1.0/24 |
| IMD-PUBLIC-B | ap-northeast-2c | 10.1.2.0/24 |
| IMD-PRIVATE-A | ap-northeast-2a | 10.1.11.0/24 |
| IMD-PRIVATE-B | ap-northeast-2c | 10.1.12.0/24 |

![](../.gitbook/assets/image.png)

* 4개의 서브넷이 정상적으로 구성되어 있는지 확인합니다.

![](../.gitbook/assets/image%20%289%29.png)

### **6. 라우팅 테이블을 생성.**

| 라우팅 테이블 이름 태그 | 서브넷 연결 |
| :--- | :--- |
| PUBLIC-RT | IMD-PUBLIC-A , IMD-PUBLIC-B |
| PRIVATE-RT | IMD-PRIVATE-A, IMD-PRIVATE-B |

* 좌측 VPC 대시보드에서 **"가상 프라이빗 클라우드" - "라우팅 테이블"** 메뉴를 선택하고, **"라우팅 테이블 생성"**을 선택합니다.
* 2개의 라우팅 테이블을 각각 생성하고, 속성을 정의합니다.

![](../.gitbook/assets/image%20%286%29.png)

* 각 라우팅 테이블에 서브넷을 연결합니다.  PUBLIC-RT, PRIVATE-RT 모두 구성합니다.

![](../.gitbook/assets/image%20%2840%29.png)

![](../.gitbook/assets/image%20%2865%29.png)

![](../.gitbook/assets/image%20%2831%29.png)

![](../.gitbook/assets/image%20%2857%29.png)

### **7. 인터넷 게이트웨이를 생성.**

* 좌측 VPC 대시보드에서 **"가상 프라이빗 클라우드" - "인터넷 게이트웨이"** 메뉴를 선택하고, **"인터넷 게이트웨이 생성"**을 선택합니다.
* 인터넷 게이트웨이 이름을 정의합니다.

![](../.gitbook/assets/image%20%2832%29.png)

* 인터넷 게이트웨이를 VPC에 연결합니다.

![](../.gitbook/assets/image%20%2844%29.png)

![](../.gitbook/assets/image%20%2855%29.png)

* 인터넷 게이트웨이의 상태가 "Attached"로 변경되었는지 확인합니다.

![](../.gitbook/assets/image%20%2866%29.png)

### 8. 라우팅 테이블 업데이트

* Public-RT 라우팅 테이블에 인터넷 게이트웨이로 향하는 트래픽을 업데이트하기 위해, "**라우팅 테이블" - "PUBLIC-RT"**를 선택하고 **"라우팅 편집"**을  선택합니다.

![](../.gitbook/assets/image%20%2876%29.png)

* **"라우팅 추가"**를 선택하고, "**대상"**에 "**0.0.0.0/0"**, **"생성한 IGW"**를 입력하고 선택합니다.

![](../.gitbook/assets/image%20%2870%29.png)

## **Task2: EC2 생성하기**

### **9. EC2 키페어 생성**

{% hint style="danger" %}
**키 페어는 1회 다운로드 이후, 다시 다운로드 받을 수 없습니다. 키 페어를 통해 EC2 인스턴스에 접속하기 때문에 키페어 다운로드 위치를 반드시 파악해 두고 잘 관리해야 합니다.**
{% endhint %}

* EC2 대시보드에서 **"네트워크 및 보안" - " 키 페어"**를 선택합니다.
* **"키페어 생성"**을 선택합니다.

![](../.gitbook/assets/image%20%2851%29.png)

* Mac OS , Linux 계열의 OpenSSH 사용자는 파일형식을 pem을 선택하고, Window OS 계열의 Putty 사용자는 파일형식을 ppk를 선택합니다.

![](../.gitbook/assets/image%20%285%29.png)

* 키페어를 로컬로 다운로드 받습니다.

### **10. EC2 대시보드에서 인스턴스 시작을 선택.**

![](../.gitbook/assets/image%20%2810%29.png)

### **11. AMI를 선택.**

* **Amazon Linux2 AMI\(Amazon Machine Image\)** 를 선택합니다. 

![](../.gitbook/assets/image%20%2841%29.png)

### **12. 인스턴스 유형을 선택.**

* **인스턴스 유형 - t2.micro** 를 선택합니다.

![](../.gitbook/assets/image%20%2816%29.png)

### **13. 인스턴스 세부 정보를 구성.**

| 인스턴스 세부 정보 | 값 |
| :--- | :--- |
| 인스턴스 개수 | 1 |
| 네트워크 | IMD-VPC |
| 서브넷 | IMD-PUBLIC-A \| ap-northeast-2a |
| 퍼블릭 IP 자동할당 | 활성화 |

![](../.gitbook/assets/image%20%2811%29.png)

* 사용자 데이터 예.

```text
#!/bin/sh
sudo yum -y update
sudo yum -y install yum-utils 
sudo yum -y install httpd php mysql php-mysql git
sudo systemctl start httpd
sudo systemctl enable httpd
cd /var/www/html/
sudo git clone https://github.com/whchoi98/ec2meta-webpage.git
sudo systemctl restart httpd
```

* 위의 "사용자 데이터 예"를 복사해서 값을 입력합니다.

![](../.gitbook/assets/image%20%2869%29.png)

* "다음:스토리지 추가"를 선택합니다.

### 14. 스토리지 추가.

![](../.gitbook/assets/image%20%2860%29.png)

* AWS에서 제공하는 볼륨 유형들을 확인합니다.
* **범용 SSD\(gp2\) 타입**을 선택합니다.
* **"다음-태그 추가"**를 선택합니다.

### **15. 태그 추가를 구성.**

* 키와 값의 내용을 입력합니다.
* **키 : IMD-EC2 , 값: PUBLIC-01**

![](../.gitbook/assets/image%20%2830%29.png)

* "다음:보안그룹구성"을 선택합니다.

### **16.보안그룹구성 구성.**

* 아래와 같은 값으로 보안 그룹\(Security Group\)을 구성합니다.

| 유형 | 프로토콜 | 포트범위 | 소스 |
| :--- | :--- | :--- | :--- |
| SSH | TCP | 22 | 위치무관 |
| HTTP | TCP | 80 | 위치무관 |
| HTTPS | TCP | 443 | 위치무관 |
| 모든 ICMP-IPv4 | ICMP | 0-65535 | 위치무관 |

* 보안 그룹 할당 - 새 보안 그룹 생성
* 보안 그룹 이름 : IMD-PUB-SG
* 설명 : Security Group for IMD-PUB

![](../.gitbook/assets/image%20%2828%29.png)

* "검토 및 시작"을 선택합니다.

### 17. 인스턴스 시작 검토를 확인.

![](../.gitbook/assets/image%20%2858%29.png)

* 검토를 완료하고 **"시작하기"**를 선택합니다.

### **18. 키 페어 선택 .**

* **"기존 키 페어 선택"을 선택하고 , 해당 키페어를 선택합니다.**\(예. IMD-LAB-PUTTY\)

![](../.gitbook/assets/image%20%2853%29.png)

{% hint style="warning" %}
**위의 과정을 반복하여, 서브넷 "IMD-PUBLIC-B" - 가용영역 "ap-northeast-2c"에 EC2 인스턴스를 한 개 더 만들어 봅니다.**
{% endhint %}

## Task3: EC2 연결 및 속성 변경

### 19. EC2 접속 환경 구성

* **정상적으로 EC2 인스턴스들이 구성되었는 지 확인해 봅니다. 인스턴스를 선택하면 Meta 정보들을 확인 할 수 있습니다.**

![](../.gitbook/assets/image%20%2864%29.png)

* **\(Window 사용자\)** EC2 접속 환경을 위해 로컬 PC에 Putty를 다운로드 받습니다.

```text
https://the.earth.li/~sgtatham/putty/latest/w64/putty-64bit-0.73-installer.msi
```

{% hint style="info" %}
맥 사용자 또는 리눅스 환경에서는 별도로 요구하지 않고, 기본 내장되어 있는 Open SSH Client를 사용합니다.
{% endhint %}

* **\(Window 사용자\)** 윈도우 사용자는 Putty 클라이언트 프로그램을 통해 접속하도록 설정합니다. \(Host Name : ec2-user@"Public IP or Public DNS\)

![](../.gitbook/assets/image%20%2871%29.png)

* **\(Window 사용자\)** SSH-Auth에서 "Allow agent forwarding" 을 체크하고, 키페어 파일은 "8.EC2 키페어 생성" 에서 로컬로 다운로드한 ppk 파일을 선택합니다.

![](../.gitbook/assets/image%20%2817%29.png)

* **\(Window 사용자\)** Open 을 선택하면 정상적으로 SSH 접속이 이뤄집니다.

![](../.gitbook/assets/image%20%2861%29.png)

* **\(Mac OS 사용자\)** 다운로드 받은 키 페어 pem 파일에 대한 권한 수정을 합니다.

```text
chmod 400 ~/Downloads/IMD-PUB-OPENSSH.pem
```

* **\(Mac OS 사용자\)** SSH를 통해 접속합니다.

```text
ssh -i ~/Downloads/IMD-PUB-OPENSSH.pem ec2-user@x.x.x.x
```

* 웹기반 클라이언트로 접속이 가능합니다. 접속하려는 **EC2 인스턴스를 선택**하고, EC2 대시보드 상단의 메뉴에서 **"연결"**을 선택합니다. 

![](../.gitbook/assets/image%20%2813%29.png)

* **"EC2 인스턴스 연결"**을 선택하고, 사용자 이름은 **"ec2-user"**를 입력합니다. **"연결"**을 선택합니다.

![](../.gitbook/assets/image%20%2849%29.png)

* 정상적으로 웹브라우져 기반으로 SSH가 연결되는 것을 확인할 수 있습니다.

![](../.gitbook/assets/image%20%282%29.png)

### **20. EC2 Linux Web 접속 및 Resize**

* 2개의 EC2 인스턴스에 SSH로 접속해서, Web 서비스가 정상적으로 설치되었는지 확인합니다. "index.php" 파일이 설치되었는지 확인합니다.

```text
ls /var/www/html/ec2meta-webpage/
```

* 로컬 브라우저\(파이어폭스, 크롬\)에서 아래 URL로 접속합니다.아래 예제와 같이 EC2 정보들을 확인해 봅니다.

```text
"ec2-public-ip or public DNS"/ec2meta-webpage/index.php
```

![](../.gitbook/assets/image%20%2859%29.png)

### 21. EC2 Instance Type 변경

* EC2 인스턴스의 자원이 추가적으로 요구 될 경우, 인스턴스 유형을 변경할 수 있습니다.
* 실행 중인 인스턴스를 선택하고 EC2 대시보드 상단 메뉴 **"작업" - "인스턴스 상태" - "중지"**를 선택합니다.

![](../.gitbook/assets/image%20%281%29.png)

* **"Stopped"**된 인스턴스를 선택하고 EC2 대시보드 상단 메뉴 "작업" - "인스턴스 설정" - "인스턴스 유형 변경"을 선택합니다. **t2.small 인스턴스 유형**으로 변경하고 **"적용"**을 선택합니다.

![](../.gitbook/assets/image%20%2848%29.png)

### 22. EC2 Volume 크기 변경

* EC2 인스턴스의 볼륨 크기가 추가적으로 요구 될 경우, 볼륨 유형을 변경하거나 크기를 늘릴 수 있습니다.
* 실행 중인 인스턴스를 선택하고 EC2 대시보드 좌측 메뉴에서 **"ELASTIC BLOCK STORE" - "볼륨"**을 선택합니다.
* EC2 대시보드 상단 메뉴에서 "작업" - "볼륨 수정"을 선택합니다.

![](../.gitbook/assets/image%20%283%29.png)

* **볼륨 크기를 10GB**로 증가 시킵니다. 해당 메뉴에서 볼륨 유형을 변경할 수도 있습니다. 볼륨 크기를 변경하고, "**수정"** 을 선택한 후 **"예"**를 선택하고 수정합니다.

![](../.gitbook/assets/image%20%2839%29.png)

![](../.gitbook/assets/image%20%2837%29.png)

* 잠시 후 EC2 대시보드에서 확인하면 아래와 같이 볼륨이 증가 한 것을 확인 할 수 있습니다.

![](../.gitbook/assets/image%20%2877%29.png)

{% hint style="danger" %}
볼륨 변경은 시간이 10분 이상 필요합니다. EC2 인스턴스의 볼륨 상태가 "In-Use"로 활성화 될 때까지 기다린 후에 , EC2 인스턴스를 다시 시작합니다.
{% endhint %}

* \(Option\)Stop 했던 EC2 인스턴스를 다시 시작합니다. 인스턴스 타입과 볼륨이 정상적으로 OS에서 확인이 되는지 점검합니다. 아래 명령을 통해 메모리 용량을 EC2 인스턴스에서 확인합니다. 2GB로 변경된 것을 확인 할 수 있습니다.

```text
cat /proc/meminfo | grep MemTotal
```

![](../.gitbook/assets/image%20%2820%29.png)

* \(Option\)아래 명령을 통해 볼륨이 정상적으로 확장되었는지 확인해 봅니다. 10GB로 볼륨의 크기가 정상적으로 변경된 것을 확인 할 수 있습니다.

```text
lsblk
df -h
```

![](../.gitbook/assets/image%20%2812%29.png)

### 23. 스냅샷 생성하기.



## 참조 자료

* [Lunch Your Instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/LaunchingAndUsingInstances.html)
* [Amazon EC2 Instance Type](https://aws.amazon.com/ec2/instance-types)
* [Amazon Machine Images \(AMI\)](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AMIs.html)
* [Amazon EC2- User Data and Shell Scripts](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/user-data.html)
* [Amazone EC2 Root Device Volume](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/RootDeviceStorage.html)
* [Tagging Your Amazone EC2 Resources](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/Using_Tags.html)
* [Security Groups](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-network-security.html)
* [Amazone EC2 Key Pairs](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html)
* [Status Checks for Your Instances](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/monitoring-system-instance-status-check.html?icmpid=docs_ec2_console)
* [Amazon EC2 Metrics and Dimensions](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/ec2-metricscollected.html)
* [Resizing Your Instanse](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-instance-resize.html)
* [Stop and Start Your Instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/Stop_Start.html)
* [Amazon EC2 Service Limits](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-resource-limits.html)
* [Terminate Your Instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/terminating-instances.html)
* [Terminate Protection for an Instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/terminating-instances.html)



