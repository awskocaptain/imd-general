# VPC

## 소개

Amazon Virtual Private Cloud\(VPC\)를 사용하면 AWS 클라우드에서 논리적으로 격리된 공간을 프로비저닝하여 고객이 정의하는 가상 네트워크에서 AWS 리소스를 시작할 수 있습니다. IP 주소 범위 선택, 서브넷 생성, 라우팅 테이블 및 네트워크 게이트웨이 구성 등 가상 네트워킹 환경을 완벽하게 제어할 수 있습니다. VPC에서 IPv4와 IPv6를 모두 사용하여 리소스와 애플리케이션에 안전하고 쉽게 액세스할 수 있습니다.  


Amazon VPC의 네트워크 구성을 손쉽게 사용자 지정할 수 있습니다. 예를 들어, 인터넷에 액세스할 수 있는 웹 서버를 위해 퍼블릭 서브넷을 생성할 수 있습니다. 또한 인터넷 액세스가 없는 프라이빗 서브넷에 데이터베이스나 애플리케이션 서버 같은 백엔드 시스템을 배치할 수 있습니다. 보안 그룹 및 네트워크 액세스 제어 목록을 포함한 다중 보안 계층을 사용하여 각 서브넷에서 Amazon EC2 인스턴스에 대한 액세스를 제어하도록 지원할 수 있습니다.

## 목적

본 랩은 아래와 같은 구성을 통해 VPC 기반의 네트워킹을 통해 클라우드 자원을 효과적으로 연결, 제어하고 모니터링하는 방법을 익히도록 합니다.

![](../.gitbook/assets/image%20%28122%29.png)



## **Task1 : VPC 생성하기**

{% hint style="info" %}
EC2-Linux 또는 EC2-Windows 랩을 구성한 경우, Task1을 수행할 필요 없습니다.
{% endhint %}

### **1.AWS 서비스-VPC 탐색**

* **"AWS Management Console - AWS 서비스"** 에서 **"서비스 찾기"** 창에 VPC를 탐색하고, VPC를 선택합니다.

![](../.gitbook/assets/image%20%2891%29.png)

### **2. VPC 선택**

* **VPC를 선택**합니다.

![](../.gitbook/assets/image%20%284%29.png)

### **3. "VPC 생성" 및 값 입력**

![](../.gitbook/assets/image%20%28121%29.png)

* **이름 태그** : VPC 이름 태그를 입력합니다.
* **IPv4 CIDR 블록** : VPC에서 사용할 IPv4 주소 대역을 입력합니다.

![](../.gitbook/assets/image%20%28139%29.png)

생성된 VPC 정보를 확인합니다.

### **4. DNS 호스트 이름을 활성화**

* 생성되는 EC2 인스턴스의 DNS Name 서비스 활성화를 위해, **"작업"**을 선택하고 **"DNS 호스트 이름 편집"**을 선택합니다. **DNS 호스트 이름을 활성화**합니다. ****

![](../.gitbook/assets/image%20%28154%29.png)

![](../.gitbook/assets/image%20%28108%29.png)

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

![](../.gitbook/assets/image%20%285%29.png)

* 4개의 서브넷이 정상적으로 구성되어 있는지 확인합니다.

![](../.gitbook/assets/image%20%28101%29.png)

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

![](../.gitbook/assets/image%20%28187%29.png)

* 각 라우팅 테이블에 서브넷을 연결합니다.  PUBLIC-RT, PRIVATE-RT 모두 구성합니다.

![](../.gitbook/assets/image%20%28107%29.png)

![](../.gitbook/assets/image%20%2822%29.png)

![](../.gitbook/assets/image%20%2858%29.png)

![](../.gitbook/assets/image%20%2890%29.png)



### **7. 인터넷 게이트웨이를 생성.**

* 좌측 VPC 대시보드에서 **"가상 프라이빗 클라우드" - "인터넷 게이트웨이"** 메뉴를 선택하고, **"인터넷 게이트웨이 생성"**을 선택합니다.
* 인터넷 게이트웨이 이름을 정의합니다.

{% hint style="info" %}
인터넷 게이트웨이는 VPC 내부 네트워크가 외부와 연결되는 구성을 담당합니다. 인터넷 게이트웨이는 한개의 VPC에 연결되며, 퍼블릭 서브넷의 인스턴스들과 1:1  NAT를 완전 관리형으로 제공합니다.
{% endhint %}

![](../.gitbook/assets/image%20%28100%29.png)

* 인터넷 게이트웨이를 VPC에 연결합니다.

![](../.gitbook/assets/image%20%28127%29.png)

![](../.gitbook/assets/image%20%28116%29.png)

### 8. 라우팅 테이블 업데이트

* Public-RT 라우팅 테이블에 인터넷 게이트웨이로 향하는 트래픽을 업데이트하기 위해, "**라우팅 테이블" - "PUBLIC-RT"**를 선택하고 **"라우팅 편집"**을  선택합니다.

![](../.gitbook/assets/image%20%28185%29.png)

* **"라우팅 추가"**를 선택하고, "**대상"**에 "**0.0.0.0/0"**, **"생성한 IGW"**를 입력하고 선택합니다.

![](../.gitbook/assets/image%20%2863%29.png)

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

![](../.gitbook/assets/image%20%2895%29.png)

* Mac OS , Linux 계열의 OpenSSH 사용자는 파일형식을 pem을 선택하고, Window OS 계열의 Putty 사용자는 파일형식을 ppk를 선택합니다.

![](../.gitbook/assets/image%20%2880%29.png)

### **10. EC2 대시보드에서 인스턴스 시작을 선택.**

![](../.gitbook/assets/image%20%2827%29.png)

### **11. AMI를 선택.**

* **Amazon Linux2 AMI\(Amazon Machine Image\)** 를 선택합니다. 

![](../.gitbook/assets/image%20%28119%29.png)

### **12. 인스턴스 유형을 선택.**

* **인스턴스 유형 - t2.micro** 를 선택합니다.

![](../.gitbook/assets/image%20%2841%29.png)

### **13. 인스턴스 세부 정보를 구성.**

| 인스턴스 세부 정보 | 값 |
| :--- | :--- |
| 인스턴스 개수 | 1 |
| 네트워크 | IMD-VPC |
| 서브넷 | IMD-PUBLIC-A \| ap-northeast-2a |
| 퍼블릭 IP 자동할당 | 활성화 |

![](../.gitbook/assets/image%20%2830%29.png)

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

![](../.gitbook/assets/image%20%28177%29.png)

* "다음:스토리지 추가"를 선택합니다.

### 14. 스토리지 추가.

![](../.gitbook/assets/image%20%28153%29.png)

* AWS에서 제공하는 볼륨 유형들을 확인합니다.
* **범용 SSD\(gp2\) 타입**을 선택합니다.
* **"다음-태그 추가"**를 선택합니다.

### **15. 태그 추가를 구성.**

* 키와 값의 내용을 입력합니다.
* **키 : IMD-EC2 , 값: PUBLIC-01**

![](../.gitbook/assets/image%20%28103%29.png)

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

![](../.gitbook/assets/image%20%2885%29.png)

* "검토 및 시작"을 선택합니다. ****

### 17. 인스턴스 시작 검토를 확인.

![](../.gitbook/assets/image%20%28151%29.png)

* 검토를 완료하고 **"시작하기"**를 선택합니다.

### **18. 키 페어 선택 .**

* **"기존 키 페어 선택"을 선택하고 , 해당 키페어를 선택합니다.**\(예. IMD-LAB-PUTTY\)
* **"인스턴스 시작"**을 선택해서 인스턴스를 생성합니다.

![](../.gitbook/assets/image%20%2845%29.png)

{% hint style="warning" %}
**위의 과정을 반복하여, 아래표의 인스턴스들을 추가로 생성합니다.**
{% endhint %}

| EC2 인스턴스 이름 / 태그 | 서브넷 | 서브넷 주소 대역 | 가용영역 | 운영체제 |
| :--- | :--- | :--- | :--- | :--- |
| IMD-EC2 PUBLIC-01 | IMD-PUBLIC-A | 10.1.1.0/24 | ap-northeast-2a | AWS LINUX2 |
| IMD-EC2 PUBLIC-02 | IMD-PUBLIC-B | 10.1.2.0/24 | ap-northeast-2c | AWS LINUX2 |
| IMD-EC2 PRIVATE-01 | IMD-PRIVATE-A | 10.1.11.0/24 | ap-northeast-2a | AWS LINUX2 |
| IMD-EC2 PRIVATE-02 | IMD-PRIVATE-B | 10.1.12.0/24 | ap-northeast-2c | AWS LINUX2 |
| BASTION | IMD-PUBLIC-B | 10.1.2.0/24 | ap-northeast-2c | AWS LINUX2 |

{% hint style="info" %}
\*\*\*\*[**"EC2-Linux Task3:EC2 연결 및 속성 변경"**](../ec2/ec2-windows.md#task-3-ec2)을 참조하여 PUBLIC-01,02 인스턴스 접속이 정상적으로 이뤄지는 지 확인합니다.
{% endhint %}

## Task3: NAT Gateway 구성 및 Private 서브넷 연결

### 19. NAT Gateway 생성

![](../.gitbook/assets/image%20%2877%29.png)

![](../.gitbook/assets/image%20%28137%29.png)

![](../.gitbook/assets/image%20%28176%29.png)



### 20. Priavet Routing Table 구성

![](../.gitbook/assets/image%20%28149%29.png)

![](../.gitbook/assets/image%20%2865%29.png)

![](../.gitbook/assets/image%20%28148%29.png)

## Task4: Private Network 연결.

Private Network은 외부에서 직접 접속이 불가능 하기 때문에 , 여러가지 방법을 통해 접속이 가능합니다.

* AWS 관리 콘솔에서 EC2를 위한 Session Manager 기반으로 접속
* Client 측에서 AWS CLI - Session Manager Plugin을 통한 콘솔 접속
* Public 서브넷에 Bastion Server를 배치하여, SSH Agent Forwarding을 통한 접속
* Public 서브넷에 Bastion Server를 배치하여, SSH Proxy Tunneling을 통한 접속

![](../.gitbook/assets/image%20%2854%29.png)

### 21. Bastion Server 기반 Private EC2 접속

* Private Subnet에 위치한 EC2 인스턴스는 외부에서 직접 연결할 수 없습니다. Bastion Server는 이러한 이슈를 해결하기 위해 Public Subnet에 서버를 설치하고 SSH port를 허용하고, Bastion Server를 통해 내부의 Private EC2에 연결할 수 있습니다. 아래 그림은 외부 SSH Client에서 Private EC2 인스턴스를 Bastion Server를 통해 연결하는 논리적 흐름입니다.

![](../.gitbook/assets/image%20%2833%29.png)

* 먼저 Linux/Mac OS의 OpenSSH Client를 기반으로 Bastion Host를 통해 접속하는 방법을 살펴봅니다. SSH Client에서 아래와 같은 명령으로 key를 저장합니다.\(ssh-add -K는 로컬에 Key를 저장해 두는 명령입니다.\)

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

![](../.gitbook/assets/image%20%28114%29.png)

* Windows 환경의 Putty 프로그램에서는 아래와 같이 Bastion Host 접속을 위해 설정합니다.

![](../.gitbook/assets/image%20%28167%29.png)

![](../.gitbook/assets/image%20%2883%29.png)

{% hint style="info" %}
Linux/Mac OS에서 처럼 로컬에 Key를 임시저장해서, Putty에서 Private key파일을 선택하지 않아도 됩니다. [Pageat](https://the.earth.li/~sgtatham/putty/latest/w64/pageant.exe) 프로그램에서 Private Key를 업로드 할 수 있습니다. 
{% endhint %}

![](../.gitbook/assets/image%20%2837%29.png)

* \(option\) SSH Tunneling을 통해 접속 할 수 있습니다. Bastion Host로 22번 포트를 연결 한 이후, 별도의 포트 번호로 터널링 하는 방식입니다. 먼저 아래와 같이 터미널에서 백그라운드 또는 포그라운드로 실행하고, 터미널을 닫지 않습니다.

```text
ssh -i “bastion key.pem path” -N -L 22001:"Private EC2 IP" ec2-user@"Bastion IP"
```

* 새로운 터미널을 생성해서 아래와 같이 위에서 선언한 포트로 접속하면 Private EC2로 접속합니다.

```text
ssh -i "target-key.pem" -p 22001 ec2-user@localhost
```



![](../.gitbook/assets/image%20%28102%29.png)

![](../.gitbook/assets/image%20%2867%29.png)

![](../.gitbook/assets/image%20%2887%29.png)

![](../.gitbook/assets/image%20%28144%29.png)

### 22. Session Manager를 통한 Private Network 연결 구성 \(web\)

* Session Manager를 통한 접속을 위해서는 EC2를 위한 SSM Role이 필요합니다.
* Private Subnet의 Private-01,02 Instance에 IAM Role을 생성해서 연결합니다. **EC2 대시보드**에서 **"Private-01**"을 선택하고, 상단 메뉴 "**작업"-"인스턴스 설정"-"IAM 역할 연결/바꾸기"**를 선택합니다.

![](../.gitbook/assets/image%20%28113%29.png)

* **새 IAM 역할 생성**을 선택합니다.

![](../.gitbook/assets/image%20%2869%29.png)

* **IAM 대시보드**로 이동하면, 상단의 **"역할 만들기"**를 선택합니다.

![](../.gitbook/assets/image%20%2821%29.png)

* **역할만들기**에서 **EC2**를 선택합니다.

![](../.gitbook/assets/image%20%28147%29.png)

* 정책필터에서 **SSM을 검색**합니다.**"AmazonEC2RoleforSSM"**을 선택하고, "**정책생성"**을 선택합니다.

![](../.gitbook/assets/image%20%288%29.png)

* IAM 역할에 대한 태그를 추가합니다.

![](../.gitbook/assets/image%20%289%29.png)

* **역할이름**을 정의합니다.

![](../.gitbook/assets/image%20%2898%29.png)

* 정상적으로 역할이 추가되었습니다.

![](../.gitbook/assets/image%20%2866%29.png)

* 생성된 IAM 역할을 선택합니다.

![](../.gitbook/assets/image%20%2878%29.png)

* Private-02 인스턴스에도 생성된 IAM 역할을 할당합니다.

{% hint style="info" %}
SSMRole을 정의하더라도, Private EC2 인스턴스는 외부에서 접속 할 수 없습니다. Task5에서 VPC Endpoint 설정이 완료되면 System Manager의 Session Manager 기능을 통해 연결할 수 있습니다.
{% endhint %}

## Task5. VPC Endpoint

23. 



![](../.gitbook/assets/image%20%28188%29.png)

![](../.gitbook/assets/image%20%28166%29.png)



![](../.gitbook/assets/image%20%2823%29.png)

![](../.gitbook/assets/image%20%28161%29.png)

![](../.gitbook/assets/image%20%2872%29.png)

![](../.gitbook/assets/image%20%2851%29.png)

![](../.gitbook/assets/image%20%28182%29.png)

![](../.gitbook/assets/image%20%2817%29.png)

![](../.gitbook/assets/image%20%28158%29.png)

![](../.gitbook/assets/image%20%2846%29.png)

아래에 출력된 결과와 AWS 관리콘솔 - Private-01 인스턴스의 주소를 비교해 봅니다.

```text
curl http://169.254.169.254/latest/meta-data/local-ipv4
```





## Task6: Security

## \*\*\*\*

## **Task7: VPC Flow**

\*\*\*\*

\*\*\*\*









