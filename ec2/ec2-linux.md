---
description: >-
  이 랩은 기본 VPC환경에서 EC2기반 LINUX LAMP를 설치 구성하는 랩입니다. VPC Networking에 대한 기본 내용을 일부
  포함하고 있습니다. (update : 2020-03-12)
---

# EC2-Linux

## 소개

Amazon Elastic Compute Cloud\(EC2\)는 안전하게 자원의 크기 조정이 가능한 컴퓨팅 파워를 클라우드상에서 제공하는 웹 서비스입니다. 개발자가 더 쉽게 클라우드 컴퓨팅 작업을 할 수 있도록 설계되었습니다. Amazon EC2의 간편한 AWS Management Console을 통해 간편하게 필요하게 용량을 얻고 구성할 수 있습니다. 컴퓨팅 리소스에 대한 포괄적인 제어권을 제공하며, Amazon의 검증된 컴퓨팅 환경에서 실행할 수 있습니다.

Amazon EC2는 AWS Management Console을 사용해 다양한 운영 체제로 인스턴스를 시작하고, 이를 사용자 정의기반 애플리케이션 환경으로 로드하며, 네트워크의 액세스 권한을 관리하고, 원하는 수의 시스템을 사용해 이미지를 실행할 수 있는 진정한 가상 컴퓨팅 환경을 제공합니다.

이 랩에서는 AWS Management Console을 사용하여 Amazon EC2에서 리눅스 기반 웹 서버를 시작, 구성 및 사용자 요구에 맞게 구성하는 과정을 안내합니다.

## 목적

본 랩은 아래와 같은 구성을 통해 EC2 기반의 운영체제와 어플리케이션을 구성하는 데 도움을 드립니다.

![\[&#xC2DC;&#xC2A4;&#xD15C; &#xBAA9;&#xD45C; &#xAD6C;&#xC131;&#xB3C4;\]](../.gitbook/assets/image%20%28158%29.png)

* **간단한 VPC 자원의 생성과 구성**
* **EC2 생성을 위한 키페어 생성**
* **EC2 생성을 위한 속성과 상세 사항 설정**
* **EC2를 위한 스토리지 구성과 보안 그룹 설정**
* **EC2 접속 방법**
* **EC2 인스턴스 타입 , 볼륨 크기 변경**
* **EC2 기반 스냅샷 생성과 볼륨 생성**

## **Task1 : VPC 생성하기**

### **1.AWS 서비스-VPC 탐색**

* **"AWS Management Console - AWS 서비스"** 에서 **"서비스 찾기"** 창에 VPC를 탐색하고, VPC를 선택합니다.

![](../.gitbook/assets/image%20%28358%29.png)

![](../.gitbook/assets/image%20%28377%29.png)

* 아래와 같은 결과를 볼 수 있습니다.

![](../.gitbook/assets/image%20%28379%29.png)

{% hint style="info" %}
각 계정에 최대 5개까지 VPC를 제공하며, Service Quotas 메뉴를 통해서 추가 요청 가능합니다. 또한 Default VPC가 1개 자동으로 구성되어 있습니다.
{% endhint %}

### **2. VPC 선택**

* **VPC를 선택**합니다.

![](../.gitbook/assets/image%20%28367%29.png)

### **3. "VPC 생성" 및 값 입력**

* **이름 태그** : VPC 이름 태그를 입력합니다.

```text
IMD-VPC
```

* **IPv4 CIDR 블록** : VPC에서 사용할 IPv4 주소 대역을 입력합니다.

```text
10.1.0.0/16
```

![](../.gitbook/assets/image%20%28376%29.png)

생성된 VPC 정보를 확인합니다.

![](../.gitbook/assets/image%20%28360%29.png)



### **4. DNS 호스트 이름을 활성화**

* 생성되는 EC2 인스턴스의 DNS Name 서비스 활성화를 위해, 화면 우측 상단에서 **"작업"**을 선택하고 **"DNS 호스트 이름 편집"**을 선택합니다. **DNS 호스트 이름을 활성화를 선택하고, 변경사항을 저장** 합니다. ****

![](../.gitbook/assets/image%20%28373%29.png)

![](../.gitbook/assets/image%20%28355%29.png)

![](../.gitbook/assets/image%20%28388%29.png)

### **5.Public ,Private 서브넷 생성.**

* 좌측 VPC 대시보드에서 `"가상 프라이빗 클라우드" - "서브넷"` 메뉴를 선택하고, **`"서브넷 생성"`**을 선택합니다.

![](../.gitbook/assets/image%20%28378%29.png)

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

![](../.gitbook/assets/image%20%28353%29.png)

* 4개의 서브넷이 정상적으로 구성되어 있는지 확인합니다.

![](../.gitbook/assets/image%20%28364%29.png)

### **6. 라우팅 테이블을 생성.**

| 라우팅 테이블 이름 태그 | 서브넷 연결 |
| :--- | :--- |
| PUBLIC-RT | IMD-PUBLIC-A , IMD-PUBLIC-C |
| PRIVATE-RT | IMD-PRIVATE-A, IMD-PRIVATE-C |

* 좌측 VPC 대시보드에서 **"가상 프라이빗 클라우드" - "라우팅 테이블"** 메뉴를 선택하고, **"라우팅 테이블 생성"**을 선택합니다.
* 2개의 라우팅 테이블을 각각 생성하고, 속성을 정의합니다.

{% hint style="info" %}
라우팅 테이블은 서브넷과 연동하여 구성됩니다. 각 서브넷이 목적지로 가기 위한 경로들의 정보를 담고 있습니다. 여러개의 서브넷을 묶어서 연동할 수도 있고, 개별로 구성할 수도 있습니다.
{% endhint %}

![](../.gitbook/assets/image%20%2823%29.png)

* 각 라우팅 테이블에 서브넷을 연결합니다.  PUBLIC-RT, PRIVATE-RT 모두 구성합니다.

**`VPC - 라우팅 테이블 - 서브넷 연결 탭 - 서브넷 연결 편집`**  선택 

![](../.gitbook/assets/image%20%28212%29.png)



![](../.gitbook/assets/image%20%28313%29.png)

![](../.gitbook/assets/image%20%28172%29.png)

![](../.gitbook/assets/image%20%28273%29.png)

### **7. 인터넷 게이트웨이를 생성.**

* 좌측 VPC 대시보드에서 **"가상 프라이빗 클라우드" - "인터넷 게이트웨이"** 메뉴를 선택하고, **"인터넷 게이트웨이 생성"**을 선택합니다.
* 인터넷 게이트웨이 이름을 정의합니다.

```text
IMD-IGW
```

{% hint style="info" %}
인터넷 게이트웨이는 VPC 내부 네트워크가 외부와 연결되는 구성을 담당합니다. 인터넷 게이트웨이는 한개의 VPC에 연결되며, 퍼블릭 서브넷의 인스턴스들과 1:1  NAT를 완전 관리형으로 제공합니다.
{% endhint %}

![](../.gitbook/assets/image%20%28371%29.png)

* 인터넷 게이트웨이를 VPC에 연결합니다.

생성된 인터넷 게이트웨어 **"IMD-IGW"** 를 선택하고, 우측 상단의 작업을 선택하고 **`VPC에 연결`**을 선택합니다. 

![](../.gitbook/assets/image%20%28356%29.png)

앞서 생성한 VPC를 선택합니다.

![](../.gitbook/assets/image%20%28363%29.png)

* 인터넷 게이트웨이의 상태가 "Attached"로 변경되었는지 확인합니다.

![](../.gitbook/assets/image%20%28359%29.png)

### 8. 라우팅 테이블 업데이트

* Public-RT 라우팅 테이블에 인터넷 게이트웨이로 향하는 트래픽을 업데이트하기 위해, "**라우팅 테이블" - "PUBLIC-RT"**를 선택하고 **"라우팅 편집"**을  선택합니다.

![](../.gitbook/assets/image%20%28345%29.png)

* **"라우팅 추가"**를 선택하고, "**대상"**에 "**0.0.0.0/0"**, **"생성한 IGW"**를 입력하고 선택합니다.

![](../.gitbook/assets/image%20%28329%29.png)

![](../.gitbook/assets/image%20%28368%29.png)

## **Task2: EC2 생성하기**

### **9. EC2 키페어 생성**

{% hint style="danger" %}
**키 페어는 1회 다운로드 이후, 다시 다운로드 받을 수 없습니다. 키 페어를 통해 EC2 인스턴스에 접속하기 때문에 키페어 다운로드 위치를 반드시 파악해 두고 잘 관리해야 합니다.**
{% endhint %}

* EC2 대시보드에서 **"네트워크 및 보안" - " 키 페어"**를 선택합니다.
* **"키페어 생성"**을 선택합니다.

![](../.gitbook/assets/image%20%28237%29.png)

* Mac OS , Linux 계열의 OpenSSH 사용자는 파일형식을 pem을 선택하고, Window OS 계열의 Putty 사용자는 파일형식을 ppk를 선택합니다.

```text
IMD-PUB-PUTTY
```

![](../.gitbook/assets/image%20%2822%29.png)

* 키페어를 로컬로 다운로드 받습니다. \(예. Window 사용자, Putty의 경우\)

![](../.gitbook/assets/image%20%28361%29.png)

### **10. EC2 대시보드에서 인스턴스 시작을 선택.**

![](../.gitbook/assets/image%20%2850%29.png)

### **11. AMI를 선택.**

* **Amazon Linux2 AMI\(Amazon Machine Image\)** 를 선택합니다. 

![](../.gitbook/assets/image%20%28213%29.png)

### **12. 인스턴스 유형을 선택.**

* **인스턴스 유형 - "원하는 유형"**  선택합니다.

![](../.gitbook/assets/image%20%2892%29.png)

### **13. 인스턴스 세부 정보를 구성.**

| 인스턴스 세부 정보 | 값 |
| :--- | :--- |
| 인스턴스 개수 | 1 |
| 네트워크 | IMD-VPC |
| 서브넷 | IMD-PUBLIC-A \| ap-northeast-2a |
| 퍼블릭 IP 자동할당 | 활성화 |

![](../.gitbook/assets/image%20%28351%29.png)

* 고급 세부 정보의 사용자 데이터를 아래와 같은 값을 복사해서 입력합니다.

```text
#!/bin/sh
sudo yum -y update
sudo yum -y install yum-utils 
sudo yum -y install https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
sudo yum -y install iotop iperf3 iptraf tcpdump git bash-completion 
sudo yum -y install httpd php mysql php-mysql 
sudo yum -y install python-pip
sudo yum -y install nethogs iftop lnav nmon tmux wireshark vsftpd ftp
sudo systemctl start httpd
sudo systemctl enable httpd
cd /var/www/html/
sudo git clone https://github.com/whchoi98/ec2meta-webpage.git
sudo systemctl restart httpd
exit

```

![](../.gitbook/assets/image%20%28366%29.png)

* "다음:스토리지 추가"를 선택합니다.

### 14. 스토리지 추가.

![](../.gitbook/assets/image%20%28372%29.png)

* AWS에서 제공하는 볼륨 유형들을 확인합니다.

> [https://docs.aws.amazon.com/ko\_kr/AWSEC2/latest/UserGuide/ebs-volume-types.html](https://docs.aws.amazon.com/ko_kr/AWSEC2/latest/UserGuide/ebs-volume-types.html)

* **범용 SSD\(gp3\) 타입**을 선택합니다.
* **"다음-태그 추가"**를 선택합니다.

### **15. 태그 추가를 구성.**

* 키와 값의 내용을 입력합니다.
* **키 : Name , 값: PUBLIC-01**
* "다음:보안그룹구성"을 선택합니다.

![](../.gitbook/assets/image%20%28370%29.png)

### **16.보안그룹구성 구성.**

* 아래와 같은 값으로 보안 그룹\(Security Group\)을 구성합니다. Security Group은 Stateful 기반으로 동작하게 되므로 Inbound에 대한 정책을 구성하면, Outbound \(Return\) 트래픽은 자동으로 허용됩니다. \(방화벽과 유사\)

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

* "검토 및 시작"을 선택합니다.

### 17. 인스턴스 시작 검토를 확인.

![](../.gitbook/assets/image%20%28274%29.png)

* 검토를 완료하고 **"시작하기"**를 선택합니다.

### **18. 키 페어 선택 .**

* **"기존 키 페어 선택"을 선택하고 , 해당 키페어를 선택합니다.**\(예. IMD-LAB-PUTTY\)
* **"인스턴스 시작"**을 선택해서 인스턴스를 생성합니다.

![](../.gitbook/assets/image%20%28241%29.png)

{% hint style="warning" %}
**위의 과정을 반복하여, 서브넷 "IMD-PUBLIC-B" - 가용영역 "ap-northeast-2c"에 EC2 인스턴스를 한 개 더 만들어 봅니다.**
{% endhint %}

#### IMD-Private-A,B 에 PRIVATE-01,PRIVATE-02 인스턴스도 각각 구성해 봅니다. Security Group은 동일한 방법으로 IMD-PRIVATE-SG로 생성합니다.

## Task3: EC2 연결 및 속성 변경

### 19. EC2 접속 환경 구성

* **정상적으로 EC2 인스턴스들이 구성되었는 지 확인해 봅니다. 인스턴스를 선택하면 Meta 정보들을 확인 할 수 있습니다.**

![](../.gitbook/assets/image%20%28352%29.png)

⚙ **Windows 사용자를 위한 EC2 연결 환경 구성** 

* **\(Window 사용자\)** EC2 접속 환경을 위해 로컬 PC에 Putty를 다운로드 받습니다.

```text
https://the.earth.li/~sgtatham/putty/latest/w64/putty-64bit-0.73-installer.msi
```

{% hint style="info" %}
맥 사용자 또는 리눅스 환경에서는 별도로 요구하지 않고, 기본 내장되어 있는 Open SSH Client를 사용합니다.
{% endhint %}

* **\(Window 사용자\)** 윈도우 사용자는 Putty 클라이언트 프로그램을 통해 접속하도록 설정합니다. \(Host Name : ec2-user@"Public IP or Public DNS\)

![](../.gitbook/assets/image%20%28362%29.png)

* **\(Window 사용자\)** SSH-Auth에서 "Allow agent forwarding" 을 체크하고, 키페어 파일은 "8.EC2 키페어 생성" 에서 로컬로 다운로드한 ppk 파일을 선택합니다.

![](../.gitbook/assets/image%20%2896%29.png)

{% hint style="info" %}
Allow agent forwarding은 Private 인스턴스 접속시에 사용됩니다.
{% endhint %}

* **\(Window 사용자\)** Open 을 선택하면 정상적으로 SSH 접속이 이뤄집니다.

![](../.gitbook/assets/image%20%28285%29.png)

⚙ **Mac OS 사용자를 위한 EC2 연결 환경 구성** 

* **\(Mac OS 사용자\)** 다운로드 받은 키 페어 pem 파일에 대한 권한 수정을 합니다.

```text
chmod 400 ~/Downloads/IMD-PUB-OPENSSH.pem
```

* **\(Mac OS 사용자\)** SSH를 통해 접속합니다.

```text
ssh -i ~/Downloads/IMD-PUB-OPENSSH.pem ec2-user@x.x.x.x
```

⚙ **AWS Web 관리 콘솔 기반 EC2 접속 방법** 

* 웹기반 클라이언트로 접속이 가능합니다. 접속하려는 **EC2 인스턴스를 선택**하고, EC2 대시보드 상단의 메뉴에서 **"연결"**을 선택합니다. 
* **"EC2 인스턴스 연결"**을 선택하고, 사용자 이름은 **"ec2-user"**를 입력합니다. **"연결"**을 선택합니다.

![](../.gitbook/assets/image%20%28369%29.png)

![](../.gitbook/assets/image%20%28375%29.png)

* 정상적으로 웹브라우져 기반으로 SSH가 연결되는 것을 확인할 수 있습니다.

![](../.gitbook/assets/image%20%283%29.png)

### **20. EC2 메타 정보 확인**

* 2개의 EC2 인스턴스에 SSH로 접속해서, Web 서비스가 정상적으로 설치되었는지 확인합니다. "index.php" 파일이 설치되었는지 확인합니다.

```text
ls /var/www/html/ec2meta-webpage/
```

* 로컬 브라우저\(파이어폭스, 크롬\)에서 아래 URL로 접속합니다.아래 예제와 같이 EC2 정보들을 확인해 봅니다.

```text
"ec2-public-ip or public DNS"/ec2meta-webpage/index.php
```

![](../.gitbook/assets/image%20%28374%29.png)

* 각 인스턴스의 Public IP 연결 주소는 아래 명령을 통해 확인 가능합니다.

```text
curl -s ifconfig.co
curl http://169.254.169.254/latest/meta-data/public-ipv4

```

### 21. EC2 Instance Type 변경

* EC2 인스턴스의 자원이 추가적으로 요구 될 경우, 인스턴스 유형을 변경할 수 있습니다.
* 실행 중인 인스턴스를 선택하고 EC2 대시보드 상단 메뉴 **"작업" - "인스턴스 상태" - "중지"**를 선택합니다.

![](../.gitbook/assets/image%20%282%29.png)

* **"Stopped"**된 인스턴스를 선택하고 EC2 대시보드 상단 메뉴 "작업" - "인스턴스 설정" - "인스턴스 유형 변경"을 선택합니다. **t2.small 인스턴스 유형**으로 변경하고 **"적용"**을 선택합니다.

![](../.gitbook/assets/image%20%28231%29.png)

인스턴스 상태 - 인스턴스 시작 을 선택해 봅니다.

![](../.gitbook/assets/image%20%28381%29.png)

해당 인스턴스의 Web을 접속해서 정보를 확인해 봅니다. 인스턴스를 중지하고 다시 시작하게 되면 Public IP가 변경됩니다. 해당 인스턴스에서 확인 후에 접속합니다.

### 22. EC2 Volume 크기 변경

* EC2 인스턴스의 볼륨 크기가 추가적으로 요구 될 경우, 볼륨 유형을 변경하거나 크기를 늘릴 수 있습니다.
* 실행 중인 인스턴스를 선택하고 EC2 대시보드 좌측 메뉴에서 **"ELASTIC BLOCK STORE" - "볼륨"**을 선택합니다.
* EC2 대시보드 상단 메뉴에서 "작업" - "볼륨 수정"을 선택합니다.

![](../.gitbook/assets/image%20%28389%29.png)

![](../.gitbook/assets/image%20%284%29.png)

* **볼륨 크기를 10GB**로 증가 시킵니다. 해당 메뉴에서 볼륨 유형을 변경할 수도 있습니다. 볼륨 크기를 변경하고, "**수정"** 을 선택한 후 **"예"**를 선택하고 수정합니다.

![](../.gitbook/assets/image%20%28382%29.png)

![](../.gitbook/assets/image%20%28200%29.png)

* 잠시 후 EC2 대시보드에서 확인하면 아래와 같이 볼륨이 증가 한 것을 확인 할 수 있습니다.

![](../.gitbook/assets/image%20%28346%29.png)

{% hint style="danger" %}
볼륨 변경은 시간이 10분 이상 필요합니다. EC2 인스턴스의 볼륨 상태가 "In-Use"로 활성화 될 때까지 기다린 후에 , EC2 인스턴스를 다시 시작합니다.
{% endhint %}

* 아래 명령을 통해 볼륨이 정상적으로 확장되었는지 확인해 봅니다. 10GB로 볼륨의 크기가 정상적으로 변경된 것을 확인 할 수 있습니다.

```text
lsblk
df -h

```

출력 결과 예제 입니다.

```text
[ec2-user@ip-10-1-1-101 ~]$ lsblk
NAME    MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
xvda    202:0    0  10G  0 disk
└─xvda1 202:1    0   8G  0 part /
[ec2-user@ip-10-1-1-101 ~]$ df -h
Filesystem      Size  Used Avail Use% Mounted on
devtmpfs        482M     0  482M   0% /dev
tmpfs           492M     0  492M   0% /dev/shm
tmpfs           492M  408K  492M   1% /run
tmpfs           492M     0  492M   0% /sys/fs/cgroup
/dev/xvda1      8.0G  1.7G  6.4G  21% /
tmpfs            99M     0   99M   0% /run/user/1000

```

**growpart** 명령을 사용하여 파티션을 확장하고, 볼륨에서 파일 시스템을 확장하기 위 **xfs\_growfs** 명령을 사용합니다.

```text
## 파티션 확
sudo growpart /dev/xvda 1
## 파일 시스템 확장
sudo xfs_growfs -d /

lsblk
df -h

```

출력 결과 예제입니다. 

```text
[ec2-user@ip-10-1-1-101 ~]$ lsblk
NAME    MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
xvda    202:0    0  10G  0 disk
└─xvda1 202:1    0  10G  0 part /

[ec2-user@ip-10-1-1-101 ~]$ df -h
Filesystem      Size  Used Avail Use% Mounted on
devtmpfs        482M     0  482M   0% /dev
tmpfs           492M     0  492M   0% /dev/shm
tmpfs           492M  464K  492M   1% /run
tmpfs           492M     0  492M   0% /sys/fs/cgroup
/dev/xvda1       10G  1.7G  8.4G  17% /
tmpfs            99M     0   99M   0% /run/user/1000
tmpfs            99M     0   99M   0% /run/user/0

```

### 23. 스냅샷 생성 및 복구 \(Option\)

* EBS 볼륨의 특정 시점 스냅샷을 생성하여 새 볼륨이나 데이터 백업의 기준으로 사용할 수 있습니다. 볼륨의 스냅샷이 주기적으로 생성되는 경우 스냅샷은 증분식이어서 새 스냅샷은 마지막 스냅샷 이후 변경된 블록만 저장합니다.스냅샷은 비동기적으로 생성됩니다. 특정 시점 스냅샷은 즉시 생성되지만 스냅샷이 완료될 때까지, 즉 수정된 블록이 `pending`로 모두 이동할 때까지 스냅샷 상태는 Amazon S3입니다. 따라서 크기가 큰 최초의 스냅샷이나 변경된 블록이 많은 후속 스냅샷의 경우 몇 시간씩 시간이 걸릴 수 있습니다. 완료하는 동안 진행 중인 스냅샷은 볼륨에 대한 지속적인 읽기 및 쓰기의 영향을 받지 않습니다.
* 랩에서는 기존 EC2에서 생성된 스냅샷을 새로운 볼륨으로 만들고, 기존 EC2 인스턴스에 연결하여 볼륨을 복구하는 과정을 구성해 봅니다.

![](../.gitbook/assets/image%20%2848%29.png)

* 스냅샷을 생성할 인스턴스를 선택하고, 인스턴스 ID를 복사합니다.

![](../.gitbook/assets/image%20%28103%29.png)

* EC2 대시보드 좌측 메뉴에서 "ELASTIC BLOCK STORE" - "스냅샷"을 선택하고 , "스냅샷 생성"을 선택합니다.

![](../.gitbook/assets/image%20%28383%29.png)

![](../.gitbook/assets/image%20%28174%29.png)

* 정상적으로 스냅샷이 구성되었는지 확인합니다.

![](../.gitbook/assets/image%20%28320%29.png)

* 생성된 스냅샷을 새로운 볼륨으로 생성합니다. **작업 - 볼륨생성** 을 선택합니다.

![](../.gitbook/assets/image%20%28136%29.png)

![](../.gitbook/assets/image%20%28380%29.png)

* 새로운 볼륨이 정상적으로 생성되었는지 확인합니다.

![](../.gitbook/assets/image%20%2878%29.png)

* 스냅샷으로 생성된 볼륨을 테스트 하기 위해 " PUBLIC-02" EC2 인스턴스에 SSH로 접속하고  "git" 패키지를 삭제합니다. 

```text
yum list installed | grep "git"
sudo yum -y remove git*
yum list installed | grep "git"

```

* git이 더 이상 동작하지 않습니다.

![](../.gitbook/assets/image%20%28289%29.png)

* 이제 "PUBLIC-02" 인스턴스에서 기존 볼륨을 제거하고, 스냅샷을 통해서 생성한 새로운 볼륨을 "PUBLIC-02" 인스턴스에 연결합니다. 먼저 "PUBLIC-02" 인스턴스를 중지합니다.

**"PUBLIC-02" 인스턴스 선택 - 인스턴스 상태 - 인스턴스 중지 선택** 

![](../.gitbook/assets/image%20%28314%29.png)

* EC2 대쉬보드에서 "ELASTIC BLOCK STORE" - "볼륨"을 선택합니다. "PUBLIC-02" 볼륨을 선택하고 연결정보에서 루트 디바이스의 이름을 확인해 둡니다. 상단 메뉴 "작업"-"볼륨분리"를 선택합니다. 

![](../.gitbook/assets/image%20%28386%29.png)

* EC2 대쉬보드에서 **"ELASTIC BLOCK STORE" - "볼륨"**을 선택하고 새로 만든 볼륨을 선택합니다. 상단 메뉴 **"작업"-"볼륨연결"**를 선택합니다. Stop되어 있는 **"PUBLIC-02" 인스턴스 ID**를 선택합니다. 디바이스는 **기존에 루트 디바이스 이름**을 입력합니다. **\(dev/xvda\)**

![](../.gitbook/assets/image%20%28387%29.png)

![](../.gitbook/assets/image%20%28115%29.png)

* 정상적으로 볼륨이 연결되었는지 확인합니다.

![](../.gitbook/assets/image%20%28215%29.png)

* Stop 시켜둔 "PUBLIC-02" EC2 인스턴스를 다시 "시작"하고 SSH로 접속합니다. 스냅샷 이후 삭제된 "git" 패키지가 보이는지 확인합니다.

![](../.gitbook/assets/image%20%28384%29.png)

{% hint style="danger" %}
인스턴스를 중지하고 다시 시작할 경우 IP 주소가 변경됩니다. 확인하고 SSH에 접속합니다.
{% endhint %}

```text
yum list installed | grep "git"

```

![](../.gitbook/assets/image%20%28234%29.png)

{% hint style="success" %}
EC2 Linux LAB을 성공적으로 마치셨습니다. 추가 상세한 내용들은 아래 참고 자료에서 확인 하실 수 있습니다.
{% endhint %}

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



