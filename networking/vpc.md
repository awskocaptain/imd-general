---
description: >-
  이 랩은 기본 VPC 네트워킹의 다양한 기능을 구성하는 환경입니다. EC2 컴퓨팅 랩을 완료하였다면, Task3 전단계에서 Private
  EC2 인스턴스와 Bastion Host만 구성하고, Task 3를 진행하면 됩니다.
---

# VPC

## 소개

Amazon Virtual Private Cloud\(VPC\)를 사용하면 AWS 클라우드에서 논리적으로 격리된 공간을 프로비저닝하여 고객이 정의하는 가상 네트워크에서 AWS 리소스를 시작할 수 있습니다. IP 주소 범위 선택, 서브넷 생성, 라우팅 테이블 및 네트워크 게이트웨이 구성 등 가상 네트워킹 환경을 완벽하게 제어할 수 있습니다. VPC에서 IPv4와 IPv6를 모두 사용하여 리소스와 애플리케이션에 안전하고 쉽게 액세스할 수 있습니다.  


Amazon VPC의 네트워크 구성을 손쉽게 사용자 지정할 수 있습니다. 예를 들어, 인터넷에 액세스할 수 있는 웹 서버를 위해 퍼블릭 서브넷을 생성할 수 있습니다. 또한 인터넷 액세스가 없는 프라이빗 서브넷에 데이터베이스나 애플리케이션 서버 같은 백엔드 시스템을 배치할 수 있습니다. 보안 그룹 및 네트워크 액세스 제어 목록을 포함한 다중 보안 계층을 사용하여 각 서브넷에서 Amazon EC2 인스턴스에 대한 액세스를 제어하도록 지원할 수 있습니다.

## 목적

본 랩은 아래와 같은 구성을 통해 VPC 기반의 네트워킹을 통해 클라우드 자원을 효과적으로 연결, 제어하고 모니터링하는 방법을 익히도록 합니다.

![](../.gitbook/assets/image%20%28299%29.png)

* **VPC 생성**
* **퍼블릭, 프라이빗 서브넷 생성**
* **NAT Gateway 생성**
* **라우팅 구성**
* **System Manager 기반 Session Manager 구성**
* **Bastion host 구성**
* **VPC Endpoint 구성**
* **VPC Flow 구성**

## **Task1 : VPC 생성하기**

{% hint style="info" %}
EC2-Linux 또는 EC2-Windows 랩을 구성한 경우, Task1을 수행할 필요 없습니다.
{% endhint %}

### **1.AWS 서비스-VPC 탐색**

* **"AWS Management Console - AWS 서비스"** 에서 **"서비스 찾기"** 창에 VPC를 탐색하고, VPC를 선택합니다.

![](../.gitbook/assets/image%20%28169%29.png)

### **2. VPC 선택**

* **VPC를 선택**합니다.

![](../.gitbook/assets/image%20%286%29.png)

### **3. "VPC 생성" 및 값 입력**

![](../.gitbook/assets/image%20%28218%29.png)

* **이름 태그** : VPC 이름 태그를 입력합니다.
* **IPv4 CIDR 블록** : VPC에서 사용할 IPv4 주소 대역을 입력합니다.

![](../.gitbook/assets/image%20%28242%29.png)

생성된 VPC 정보를 확인합니다.

### **4. DNS 호스트 이름을 활성화**

* 생성되는 EC2 인스턴스의 DNS Name 서비스 활성화를 위해, **"작업"**을 선택하고 **"DNS 호스트 이름 편집"**을 선택합니다. **DNS 호스트 이름을 활성화**합니다. ****

![](../.gitbook/assets/image%20%28282%29.png)

![](../.gitbook/assets/image%20%28194%29.png)

### **5.Public ,Private 서브넷 생성.**

* 좌측 VPC 대시보드에서 **"가상 프라이빗 클라우드" - "서브넷"** 메뉴를 선택하고, **"서브넷 생성"**을 선택합니다.
* 4개의 서브넷을 각각 생성하고, 속성을 정의합니다.

{% hint style="info" %}
Public 서브넷은 Internet Gateway와 1:1 NAT로 직접 연결 가능한 네트워크 범위를 의미합니다. Private 서브넷은 외부로 직접 노출될 수 없으며, 외부와 연동하기 위해서는 Source NAT를 사용하는 방법으로 NAT 기능을 지원하는 인스턴스 또는 NAT Gateway 등과 같은 서비스를 사용해야 합니다.
{% endhint %}

| **서브넷 이름 태그** | 가용영역 | **IPv4 CIDR 블록** |
| :--- | :--- | :--- |
| IMD-PUBLIC-A | ap-northeast-2a | 10.1.1.0/24 |
| IMD-PUBLIC-B | ap-northeast-2c | 10.1.2.0/24 |
| IMD-PRIVATE-A | ap-northeast-2a | 10.1.11.0/24 |
| IMD-PRIVATE-B | ap-northeast-2c | 10.1.12.0/24 |

![](../.gitbook/assets/image%20%288%29.png)

* 4개의 서브넷이 정상적으로 구성되어 있는지 확인합니다.

![](../.gitbook/assets/image%20%28183%29.png)

### **6. 라우팅 테이블을 생성.**

| 라우팅 테이블 이름 태그 | 서브넷 연결 |
| :--- | :--- |
| PUBLIC-RT | IMD-PUBLIC-A , IMD-PUBLIC-B |
| PRIVATE-RT | IMD-PRIVATE-A, IMD-PRIVATE-B |

* 좌측 VPC 대시보드에서 **"가상 프라이빗 클라우드" - "라우팅 테이블"** 메뉴를 선택하고, **"라우팅 테이블 생성"**을 선택합니다.
* 2개의 라우팅 테이블을 각각 생성하고, 속성을 정의합니다.

{% hint style="info" %}
라우팅 테이블은 서브넷과 연동하여 구성됩니다. 각 서브넷이 목적지로 가기 위한 경로들의 정보를 담고 있습니다. 여러개의 서브넷을 묶어서 연동할 수도 있고, 개별로 구성할 수도 있습니다.
{% endhint %}

![](../.gitbook/assets/image%20%28348%29.png)

* 각 라우팅 테이블에 서브넷을 연결합니다.  PUBLIC-RT, PRIVATE-RT 모두 구성합니다.

![](../.gitbook/assets/image%20%28193%29.png)

![](../.gitbook/assets/image%20%2841%29.png)

![](../.gitbook/assets/image%20%28116%29.png)

![](../.gitbook/assets/image%20%28168%29.png)



### **7. 인터넷 게이트웨이를 생성.**

* 좌측 VPC 대시보드에서 **"가상 프라이빗 클라우드" - "인터넷 게이트웨이"** 메뉴를 선택하고, **"인터넷 게이트웨이 생성"**을 선택합니다.
* 인터넷 게이트웨이 이름을 정의합니다.

{% hint style="info" %}
인터넷 게이트웨이는 VPC 내부 네트워크가 외부와 연결되는 구성을 담당합니다. 인터넷 게이트웨이는 한개의 VPC에 연결되며, 퍼블릭 서브넷의 인스턴스들과 1:1  NAT를 완전 관리형으로 제공합니다.
{% endhint %}

![](../.gitbook/assets/image%20%28181%29.png)

* 인터넷 게이트웨이를 VPC에 연결합니다.

![](../.gitbook/assets/image%20%28226%29.png)

![](../.gitbook/assets/image%20%28209%29.png)

### 8. 라우팅 테이블 업데이트

* Public-RT 라우팅 테이블에 인터넷 게이트웨이로 향하는 트래픽을 업데이트하기 위해, "**라우팅 테이블" - "PUBLIC-RT"**를 선택하고 **"라우팅 편집"**을  선택합니다.

![](../.gitbook/assets/image%20%28345%29.png)

* **"라우팅 추가"**를 선택하고, "**대상"**에 "**0.0.0.0/0"**, **"생성한 IGW"**를 입력하고 선택합니다.

![](../.gitbook/assets/image%20%28127%29.png)

## **Task2: EC2 생성하기**

{% hint style="info" %}
**EC2 Computing 랩을 완료한 경우, Private Subnet의 EC2 인스턴스와 Bastion EC2 인스턴스만 생성합니다.**
{% endhint %}

### **9. EC2 키페어 생성**

{% hint style="danger" %}
**키 페어는 1회 다운로드 이후, 다시 다운로드 받을 수 없습니다. 키 페어를 통해 EC2 인스턴스에 접속하기 때문에 키페어 다운로드 위치를 반드시 파악해 두고 잘 관리해야 합니다.**
{% endhint %}

* EC2 대시보드에서 **"네트워크 및 보안" - " 키 페어"**를 선택합니다.
* **"키페어 생성"**을 선택합니다.

![](../.gitbook/assets/image%20%28175%29.png)

* Mac OS , Linux 계열의 OpenSSH 사용자는 파일형식을 pem을 선택하고, Window OS 계열의 Putty 사용자는 파일형식을 ppk를 선택합니다.

![](../.gitbook/assets/image%20%28156%29.png)

### **10. EC2 대시보드에서 인스턴스 시작을 선택.**

![](../.gitbook/assets/image%20%2850%29.png)

### **11. AMI를 선택.**

* **Amazon Linux2 AMI\(Amazon Machine Image\)** 를 선택합니다. 

![](../.gitbook/assets/image%20%28213%29.png)

### **12. 인스턴스 유형을 선택.**

* **인스턴스 유형 - t2.micro** 를 선택합니다.

![](../.gitbook/assets/image%20%2892%29.png)

### **13. 인스턴스 세부 정보를 구성.**

| 인스턴스 세부 정보 | 값 |
| :--- | :--- |
| 인스턴스 개수 | 1 |
| 네트워크 | IMD-VPC |
| 서브넷 | IMD-PUBLIC-A \| ap-northeast-2a |
| 퍼블릭 IP 자동할당 | 활성화 |

![](../.gitbook/assets/image%20%2861%29.png)

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

![](../.gitbook/assets/image%20%28328%29.png)

* "다음:스토리지 추가"를 선택합니다.

### 14. 스토리지 추가.

![](../.gitbook/assets/image%20%28281%29.png)

* AWS에서 제공하는 볼륨 유형들을 확인합니다.
* **범용 SSD\(gp2\) 타입**을 선택합니다.
* **"다음-태그 추가"**를 선택합니다.

### **15. 태그 추가를 구성.**

* 키와 값의 내용을 입력합니다.
* **키 : IMD-EC2 , 값: PUBLIC-01**

![](../.gitbook/assets/image%20%28185%29.png)

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

![](../.gitbook/assets/image%20%28162%29.png)

* "검토 및 시작"을 선택합니다. ****

### 17. 인스턴스 시작 검토를 확인.

![](../.gitbook/assets/image%20%28274%29.png)

* 검토를 완료하고 **"시작하기"**를 선택합니다.

### **18. 키 페어 선택 .**

* **"기존 키 페어 선택"을 선택하고 , 해당 키페어를 선택합니다.**\(예. IMD-LAB-PUTTY\)
* **"인스턴스 시작"**을 선택해서 인스턴스를 생성합니다.

![](../.gitbook/assets/image%20%2897%29.png)

{% hint style="warning" %}
**위의 과정을 반복하여, 아래표의 인스턴스들을 추가로 생성합니다.**
{% endhint %}

| EC2 인스턴스 이름 / 태그 | 서브넷 | 서브넷 주소 대역 | 가용영역 | 보안그룹 |
| :--- | :--- | :--- | :--- | :--- |
| IMD-EC2 PUBLIC-01 | IMD-PUBLIC-A | 10.1.1.0/24 | ap-northeast-2a | IMD-PUB-SG |
| IMD-EC2 PUBLIC-02 | IMD-PUBLIC-B | 10.1.2.0/24 | ap-northeast-2c | IMD-PUB-SG |
| IMD-EC2 PRIVATE-01 | IMD-PRIVATE-A | 10.1.11.0/24 | ap-northeast-2a | IMD-PRI-SG |
| IMD-EC2 PRIVATE-02 | IMD-PRIVATE-B | 10.1.12.0/24 | ap-northeast-2c | IMD-PRI-SG |
| BASTION | IMD-PUBLIC-B | 10.1.2.0/24 | ap-northeast-2c | IMD-BASTION-SG |

{% hint style="info" %}
\*\*\*\*[**"EC2-Linux Task3:EC2 연결 및 속성 변경"**](../ec2/ec2-windows.md#task-3-ec2)을 참조하여 PUBLIC-01,02 인스턴스 접속이 정상적으로 이뤄지는 지 확인합니다.
{% endhint %}

## Task3: NAT Gateway 구성 및 Private 서브넷 연결

### 19. NAT Gateway 생성

![](../.gitbook/assets/image%20%28153%29.png)

![](../.gitbook/assets/image%20%28240%29.png)

![](../.gitbook/assets/image%20%28327%29.png)



### 20. Private Routing Table 구성

![](../.gitbook/assets/image%20%28270%29.png)

![](../.gitbook/assets/image%20%28133%29.png)

![](../.gitbook/assets/image%20%28267%29.png)

{% hint style="info" %}
**Task4. Private Network 연결을 완료한 이후에는 NAT Gateway를 통해 Private EC2에서 외부망으로 연결이 가능하지 확인 할 수 있습니다.**
{% endhint %}

## Task4: Private Network 연결.

Private Network은 외부에서 직접 접속이 불가능 하기 때문에 , 여러가지 방법을 통해 접속이 가능합니다.

* AWS 관리 콘솔에서 EC2를 위한 Session Manager 기반으로 접속
* Client 측에서 AWS CLI - Session Manager Plugin을 통한 콘솔 접속
* Public 서브넷에 Bastion Server를 배치하여, SSH Agent Forwarding을 통한 접속
* Public 서브넷에 Bastion Server를 배치하여, SSH Proxy Tunneling을 통한 접속

![](../.gitbook/assets/image%20%28107%29.png)

### 21. Bastion Server 기반 Private EC2 접속

* Private Subnet에 위치한 EC2 인스턴스는 외부에서 직접 연결할 수 없습니다. Bastion Server는 이러한 이슈를 해결하기 위해 Public Subnet에 서버를 설치하고 SSH port를 허용하고, Bastion Server를 통해 내부의 Private EC2에 연결할 수 있습니다. 아래 그림은 외부 SSH Client에서 Private EC2 인스턴스를 Bastion Server를 통해 연결하는 논리적 흐름입니다.

![](../.gitbook/assets/image%20%2867%29.png)

* **Linux/Mac OS 환경일 경우 \(Window 사용자의 경우 생략 합니다.\)** 먼저 Linux/Mac OS의 OpenSSH Client를 기반으로 Bastion Host를 통해 접속하는 방법을 살펴봅니다. SSH Client에서 아래와 같은 명령으로 key를 저장합니다.\(ssh-add -K는 로컬에 Key를 저장해 두는 명령입니다.\)

```text
ssh-add -K "key_path/key.pem"
```

* 저장된 Key값을 확인합니다. \(ssh-add -L 은 저장된 key값을 보여주는 명령입니다.\)

```text
ssh-add -L
```

* Bastion Server에 정상적으로 접속이 완료되면 연결을 원하는 Private EC2 인스턴스에 SSH로 접속합니다.

```text
ssh -A ec2-user@ec2-13-125-15-101.ap-northeast-2.compute.amazonaws.com
```

* key값을 저장해 놓지 않은 경우에는 아래와 같이 Bastion Server로 접속합니다. \(-A 옵션은 SSH Agent Forwarding을 위한 옵션입니다.\)

```text
ssh -i "key_path/IMD-PUB-OPENSSH.pem" ec2-user@PUBLIC_DNS -A
```

![](../.gitbook/assets/image%20%28207%29.png)

* **Windows 환경의  Putty 사용자일 경우** Windows 환경의 Putty 프로그램에서는 아래와 같이 Bastion Host 접속을 위해 설정합니다.

![](../.gitbook/assets/image%20%28312%29.png)

![](../.gitbook/assets/image%20%28160%29.png)

{% hint style="info" %}
Linux/Mac OS에서 처럼 로컬에 Key를 임시저장해서, Putty에서 Private key파일을 선택하지 않아도 됩니다. [Pageat](https://the.earth.li/~sgtatham/putty/latest/w64/pageant.exe) 프로그램에서 Private Key를 업로드 할 수 있습니다. 
{% endhint %}

![](../.gitbook/assets/image%20%2883%29.png)

* \(option- Mac OS /Linux 사용자를 위한 Bastion Tunneling\)  SSH Tunneling을 통해 접속 할 수 있습니다. Bastion Host로 22번 포트를 연결 한 이후, 별도의 포트 번호로 터널링 하는 방식입니다. 먼저 아래와 같이 터미널에서 백그라운드 또는 포그라운드로 실행하고, 터미널을 닫지 않습니다.

```text
ssh -i “bastion key.pem path” -N -L 22001:"Private EC2 IP" ec2-user@"Bastion IP"
```

* 새로운 터미널을 생성해서 아래와 같이 위에서 선언한 포트로 접속하면 Private EC2로 접속합니다.

```text
ssh -i "target-key.pem" -p 22001 ec2-user@localhost
```

* \(Option - Window 사용자를 위한 Bastion Tunneling\) 앞서 구성한 Bastion 접속을 위한 Putty 구성에서 아래와 같이 추가 구성을 하고, Bastion Host에 접속합니다.

![](../.gitbook/assets/image%20%28184%29.png)

![](../.gitbook/assets/image%20%28135%29.png)

* 터널링 구성이 완성되었으므로, 이제 다시 Putty 창을 한개 더 열고, 아래와 같이 22번 포트로 접속하면, 터널링으로 내부 접속이 가능해 집니다.

![](../.gitbook/assets/image%20%28165%29.png)

![](../.gitbook/assets/image%20%28253%29.png)

## Task5. VPC Endpoint

VPC 엔드포인트를 통해 인터넷 게이트웨이, NAT 디바이스, VPN 연결 또는 AWS Direct Connect 연결을 필요로 하지 않고 AWS PrivateLink 구동 지원 AWS 서비스 및 VPC 엔드포인트 서비스에 비공개로 연결할 수 있습니다. VPC의 인스턴스는 서비스의 리소스와 통신하는 데 퍼블릭 IP 주소를 필요로 하지 않습니다.  랩에서 Private EC2로 연결할 수 있는 방법은 Bastion Host를 통해 연결하거나, VPC Endpoint를 연결하여 Session Manager로 접속하는 방법이 있습니다.

Task5에서는 VPC EndPoint를 통해서 Session Manager로 연결하는 방법을 구성해 봅니다.

![](../.gitbook/assets/image%20%28128%29.png)

### 22. Session Manager 연결을 위한 Role 생성

* Session Manager를 통한 접속을 위해서는 EC2를 위한 SSM Role이 필요합니다.
* Private Subnet의 Private-01,02 Instance에 IAM Role을 생성해서 연결합니다. **EC2 대시보드**에서 **"Private-01**"을 선택하고, 상단 메뉴 "**작업"-"인스턴스 설정"-"IAM 역할 연결/바꾸기"**를 선택합니다.

![](../.gitbook/assets/image%20%28202%29.png)

* **새 IAM 역할 생성**을 선택합니다.

![](../.gitbook/assets/image%20%28137%29.png)

* **IAM 대시보드**로 이동하면, 상단의 **"역할 만들기"**를 선택합니다.

![](../.gitbook/assets/image%20%2840%29.png)

* **역할만들기**에서 **EC2**를 선택합니다.

![](../.gitbook/assets/image%20%28263%29.png)

* 정책필터에서 **SSM을 검색**합니다.**"AmazonEC2RoleforSSM"**을 선택하고, "**정책생성"**을 선택합니다.

![](../.gitbook/assets/image%20%2811%29.png)

* IAM 역할에 대한 태그를 추가합니다.

![](../.gitbook/assets/image%20%2813%29.png)

* **역할이름**을 정의합니다.

![](../.gitbook/assets/image%20%28179%29.png)

* 정상적으로 역할이 추가되었습니다.

![](../.gitbook/assets/image%20%28134%29.png)

* 생성된 IAM 역할을 선택합니다.

![](../.gitbook/assets/image%20%28154%29.png)

* **Private-02** 인스턴스에도 생성된 IAM 역할을 할당합니다.

{% hint style="info" %}
SSMRole을 정의하더라도, Private EC2 인스턴스는 외부에서 접속 할 수 없습니다. VPC Endpoint 설정이 완료되면 System Manager의 Session Manager 기능을 통해 연결할 수 있습니다.
{% endhint %}

### 23. VPC EndPoint 설정 

* VPC Endpoint설정을 위해서 **"VPC 대시보드" - "가상 프라이빗 클라우드" - "엔드포인트" -"엔트포인트 생성"**을 선택합니다.

![](../.gitbook/assets/image%20%28349%29.png)

* 서비스를 선택하기 위해 **"서비스 이름" - "ssmmessages"**를 선택합니다. 현재 **사용 중인 VPC**를 선택합니다. 서브넷은 **Private 서브넷 2개 \(2개의 가용영역\)**를 선택합니다.

![](../.gitbook/assets/image%20%28309%29.png)

* 새로운 **보안그룹\(Security Group\)을 생성**하고 선택합니다. 보안 정책은 **"HTTPS"를 인바운드 허용**해 줍니다. **"IMD-SSM-SG"** 보안 그룹을 생성하고 **Search 창에서 "IMD-SSM-SG"**를 찾아서 선택합니다.
* 적절한 **태그를 구성**하고, **엔드포인트 생성**을 완료합니다.

![](../.gitbook/assets/image%20%2842%29.png)

* **"VPC 대시보드" - "가상 프라이빗 클라우드" - "엔드포인트"** 에서 **상태-사용가능으로 변경**되었는지 확인합니다. 서브넷 창을 선택하고 **2개의 ENI\(Elastic Network Interface\)가 생성**된 것을 확인합니다.

![](../.gitbook/assets/image%20%28292%29.png)

* AWS 서비스에서 **"System Manager"**를 선택합니다.

![](../.gitbook/assets/image%20%28147%29.png)

* **"System Manager"**에서 **"Session Manager"**를 선택합니다.

![](../.gitbook/assets/image%20%28104%29.png)

* **세션 시작**을 선택합니다.

{% hint style="danger" %}
세션시작 화면에서  대상 인스턴스가 보이지 않습니다. EC2 인스턴스를 최초에 생성할 때 IAM Role 역할을 정의하지 않고, 생성 이후에 추가했기 때문입니다. **IAM Role을 추가로 연결한 Private 인스턴스들을 재시작**합니다. EC2 인스턴스를 재시작하면 **System Manager - 세션관리자**에서 정상적으로 인스턴스들이 보입니다.
{% endhint %}

![](../.gitbook/assets/image%20%28335%29.png)

![](../.gitbook/assets/image%20%2833%29.png)

* 이제 Bastion Host를 경유하지 않고 , VPC Endpoint 서비스와 Session Manager를 통해서 Private 인스턴스들에 직접 연결이 가능합니다. **EC2 대쉬보드**에서 **Private-01, Private-02 인스턴스**를 선택하고 "**인스턴스 연결" - "Session Manager"**를 선택합니다.

![](../.gitbook/assets/image%20%28288%29.png)

* 정상적으로 Private 인스턴스에 연결되는 것을 확인 할 수 있습니다.

{% hint style="info" %}
Session Manager에 최초 연결될때 , Linux 계정을 확인해 봅니다.**ec2-user**로 로그인 되지 않습니다. IAM-Role에 의해 생성된 **ssm-user** 계정입니다.

```text
whoami
sudo -u ec2-user bash
```
{% endhint %}

![](../.gitbook/assets/image%20%2898%29.png)

아래에 출력된 결과와 AWS 관리콘솔 - Private-01 인스턴스의 주소를 비교해 봅니다.

```text
curl http://169.254.169.254/latest/meta-data/local-ipv4
```

## Task6: Security

**보안 그룹**은 인스턴스에 대한 인바운드 및 아웃바운드 트래픽을 제어하는 가상 방화벽 역할을 합니다. VPC에서 인스턴스를 시작할 때 최대 5개의 보안 그룹에 인스턴스를 할당할 수 있습니다. 보안 그룹은 서브넷 수준이 아니라 인스턴스 수준에서 작동하므로 VPC에 있는 서브넷의 각 인스턴스를 서로 다른 보안 그룹 세트에 할당할 수 있습니다.

네트워크 ACL\(액세스 제어 목록\)은 1개 이상의 서브넷 내부와 외부의 트래픽을 제어하기 위한 방화벽 역할을 하는 VPC를 위한 선택적 보안 계층입니다. 보안 그룹과 비슷한 규칙으로 네트워크 ACL을 설정하여 VPC에 보안 계층을 더 추가할 수 있습니다.

| 보안그룹 | 네트워크 ACL |
| :--- | :--- |
| 인스턴스 레벨에서 운영 | 서브넷 레벨에서 운영 |
| 허용 규칙만 지원 | 허용 및 거부 규칙 지원 |
| 상태 저장: 규칙에 관계없이 반환 트래픽이 자동 허용됨 | 상태 비저장:  반환 트래픽이 규칙에 의해 명시적으로 허용되어야 함 |
| 트래픽 허용 여부를 결정하기 전에 모든 규칙을 평가 | 트래픽 허용 여부를 결정 시 규칙을 번호순으로 처리 |
| 인스턴스 시작 시 누군가 보안 그룹을 지정하거나, 나중에 보안 그룹을 인스턴스와 연결하는 경우에만 인스턴스에 적용됨 | 연결된 서브넷의 모든 인스턴스에 자동 적용됨\(보안 그룹 규칙이 지나치게 허용적일 경우 추가 보안 계층 제공\) |

### **24.Network ACL 구성**

![](../.gitbook/assets/image%20%2877%29.png)

* NACL \(Network Access Control List\) 구성을 위해 , **"VPC대시보드" - "보안" - "네트워크 ACL" - " 네트워크 ACL 생성"**을 선택합니다.

![](../.gitbook/assets/image%20%28204%29.png)

* **이름태그를 입력**하고, **VPC를 선택**합니다.

![](../.gitbook/assets/image%20%2879%29.png)

* **NACL 인바운드 규칙**을 편집 합니다. **소스 주소**를 입력하고 **모든 트래픽 유형**을 선택합니다. 또한 **허용/거부에 "ALLOW**"를 선택합니다. 이때 소스 주소는 **Public Subnet-A \(가용영역 :ap-northeast-2a\) 주소를 입력**합니다.

![](../.gitbook/assets/image%20%28307%29.png)

* NACL은 상태비저장 방식으로 트래픽 반환시에도 정책이 필요합니다. 트래픽 반환은 모두 허용하기 위해 **대상을 모두로 정의**하고, **유형도 모두 트래픽을 선택**합니다.

![](../.gitbook/assets/image%20%2858%29.png)

* **서브넷 연결 편집은 Private-A,B** 모두 적용합니다.

![](../.gitbook/assets/image%20%2832%29.png)

* 3개의 콘솔 창을 열어서 트래픽이 어떻게 제어되는지 확인합니다. Public-01, Public-02 인스턴스에서 Private-01 인스턴스로 Ping을 적용해 봅니다. 또한 Private-01에 접속하여 외부로 Ping이 가능한지 확인합니다.
* 아래와 같이 Public-01\(10.1.1.0/24\)는 Private-01로 Ping이 가능하고, Public-02\(10.1.2.0/24\)는 Private-01,02로 어떠한 연결도 불가능합니다. 또한 Private-01,02는 NACL로 인해서 트래픽을 받지 못합니다.

![](../.gitbook/assets/image%20%28189%29.png)

![](../.gitbook/assets/image%20%2881%29.png)

![](../.gitbook/assets/image%20%28117%29.png)

{% hint style="info" %}
**Network ACL은 사용자가 정의하지 않아도 기본 NACL이 인바운드,아웃바운드 모두 허용입니다. 랩에서는 별도의 NACL을 적용해서 서브넷에 적용한 것입니다.**
{% endhint %}

### **25.보안 그룹 - Security Group 구성**

* 네트워크 ACL을 모두 허용합니다. \(인바운드 소스 허용을 0.0.0.0/0 으로 변경합니다.\) 또는 규칙 번호를 10번 이전 번호로 만들고 모든 규칙을 허용해도 됩니다. 이것은 Network ACL이 순차적으로 보안규칙을 탐색하고, 곧바로 적용하기 때문입니다.

![](../.gitbook/assets/image%20%28318%29.png)

* **VPC 대시보드 - 보안 - 보안그룹에서 "IMD-PUB-SG\(Public subnet을 위한 Security Group\)"을 수정**합니다. **인바운드 ICMP 허용을 모두 제거**합니다.

![](../.gitbook/assets/image%20%2886%29.png)

* 적용 이후 외부에서 더 이상 ICMP가 허용되지 않습니다. 하지만 내부 Public EC2 에서는 아웃바운드 규칙에서 모두 허용이므로 ICMP가 정상적으로 허용됩니다. 상태저장 방식으로 반환트래픽에 대해서는 제어하지 않기 때문입니다.

![](../.gitbook/assets/image%20%285%29.png)

{% hint style="warning" %}
외부에서 PING을 계속 적용한 상태에서 , 인바운드 규칙을 제거해도 PING은 정상적으로 허용됩니다. 하지만 PING을 끊고 다시 PING을 시도하면 더 이상 응답하지 않습니다. 이것은 상태저장방식의 특징으로 대부분 보안제품도 동일합니다.
{% endhint %}

## **Task7: VPC Flow \(Option\)**

VPC flow log는 VPC 트워크에서 전송되고 수신되는 IP 트래픽에 대한 정보를 수집할 수 있는 기능입니다. 플로우 로그 데이터를 Amazon CloudWatch Logs 및 Amazon S3로 게시할 수 있습니다. 플로우 로그를 생성한 다음 선택된 대상의 데이터를 가져와 확인할 수 있습니다.

VPC flow log는 다음과 같은 여러 작업에 도움이 될 수 있습니다.

* 보안 그룹 규칙에 대 진단
* 인스턴스에 도달하는 트래픽 모니터링
* 네트워크 인터페이스를 오가는 트래픽에 대한 분석

VPC Flow log 데이터는 네트워크 트래픽 경로 외부에서 수집되므로 네트워크 처리량이나 지연 시간에 영향을 주지 않습니다. 네트워크 성능에 영향을 주지 않고 VPC Flow log를 생성하거나 삭제할 수 있습니다. 또한 VPC Flow log를 사용하면, CloudWatch Logs 또는 Amazon S3 중 어디로 보내든, CloudWatch Logs 요금이 적용됩니다.

VPC, 서브넷 또는 네트워크 인터페이스에 대한 VPC Flow를 생성할 수 있습니다. 서브넷이나 VPC에 대한 VPC Flow log를 생성할 경우, VPC 또는 서브넷의 각 네트워크 인터페이스가 모니터링됩니다.

모니터링된 네트워크 인터페이스를 위한 VPC Flow log 데이터는 트래픽 Flow 설명하는 필드로 구성된 로그 이벤트인 flow log record로서 기록됩니다. 

### 26. VPC Flow log를 수신할 CloudWatch Log Group을 생성

* 먼저 VPC Flow log를 수신할 Cloud watch log group을 생성합니다.  **AWS 서비스 - CloudWatch - 로그 - 로그그룹 - 로그그룹 생성을 선택**합니다.

![](../.gitbook/assets/image%20%28101%29.png)

* **새로운 로그 그룹을 생성**합니다.

![](../.gitbook/assets/image%20%28217%29.png)

* 로그그룹이 정상적으로 생성되었는지 확인합니다.

![](../.gitbook/assets/image%20%28296%29.png)

* 다시 **VPC 대시보드**로 돌아와, V**PC를 선택하고 플로우 로그 생성**을 선택합니다.

![](../.gitbook/assets/image%20%28258%29.png)

* Maximum aggregation Interval 을 1분으로 선택합니다. 대상로그 그룹은 앞서 생성한 로그 그룹을 섵개합니다.\(IMD-VPCFlow\) IAM역할을 "flowlogsRole"을 선택합니다. 만약 없는 경우 생성버튼을 눌러서 생성합니다.

![](../.gitbook/assets/image%20%28260%29.png)

* 앞서 IAM Role에 "flowlogsRole"이 없는 경우에만 해당됩니다. 생성 버튼을 누르면 자동으로 IAM 메뉴로 연결됩니다. 별도의 변경없이 생성합니다.

![](../.gitbook/assets/image%20%2831%29.png)

* VPC 플로우 로그가 정상적으로 생성되었는지 확인합니다.

![](../.gitbook/assets/image%20%28146%29.png)

* **CloudWatch 대시보드**로 이동합니다. **"로그"-"로그그룹"을 선택**하면 최초에 생성한 로그 그룹이 보입니다. **해당 로그 그룹을 선택**합니다.

![](../.gitbook/assets/image%20%28247%29.png)

* 로그그룹에 스트림이 정상적으로 로그들이 쌓이는 것을 확인합니다.

![](../.gitbook/assets/image%20%28301%29.png)

* **로그 그룹의 스트림 중에 한개를 선택**해서 내용을 살펴봅니다.

![](../.gitbook/assets/image%20%28339%29.png)

* 상세한 VPC Flow 분석 방법은 아래 URL을 참조합니다.

{% embed url="https://whchoi98.gitbook.io/aws-hybrid/5networkmonitoring" %}

{% hint style="success" %}
VPC 랩을 모두 성공적으로 마치셨습니다.
{% endhint %}

## 참조 자료

* [**VPC 사용설명서**](https://docs.aws.amazon.com/ko_kr/vpc/latest/userguide/what-is-amazon-vpc.html)\*\*\*\*
* \*\*\*\*[**VPC 구성 예**](https://docs.aws.amazon.com/ko_kr/vpc/latest/userguide/VPC_Scenarios.html)\*\*\*\*
* \*\*\*\*[**VPC 네트워킹 구성 요소**](https://docs.aws.amazon.com/ko_kr/vpc/latest/userguide/VPC_Networking.html)\*\*\*\*
* \*\*\*\*[**VPC Endpoint 및 VPC 엔드포인트 서비스**](https://docs.aws.amazon.com/ko_kr/vpc/latest/userguide/endpoint-services-overview.html)\*\*\*\*
* \*\*\*\*[**VPC FAQ**](https://aws.amazon.com/ko/vpc/faqs/)\*\*\*\*
* \*\*\*\*[**VPC Flow log**](https://docs.aws.amazon.com/ko_kr/vpc/latest/userguide/flow-logs.html)\*\*\*\*







