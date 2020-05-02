---
description: 이 랩은 기본 VPC환경에서 EC2기반 Window Server를 설치 구성하는 랩입니다.
---

# EC2-Windows

## 소개

## 목적

## **Task1. VPC 생성하기**

### **1.AWS 서비스-VPC 탐색**

* **"AWS Management Console - AWS 서비스"** 에서 **"서비스 찾기"** 창에 VPC를 탐색하고, VPC를 선택합니다.

![](../.gitbook/assets/image%20%2828%29.png)

### **2. VPC 선택**

* **VPC를 선택**합니다.

![](../.gitbook/assets/image%20%284%29.png)

### **3. "VPC 생성" 및 값 입력**

![](../.gitbook/assets/image%20%2834%29.png)

* **이름 태그** : VPC 이름 태그를 입력합니다.
* **IPv4 CIDR 블록** : VPC에서 사용할 IPv4 주소 대역을 입력합니다.

![](../.gitbook/assets/image%20%2852%29.png)

생성된 VPC 정보를 확인합니다.

### **4. DNS 호스트 이름을 활성화**

* 생성되는 EC2 인스턴스의 DNS Name 서비스 활성화를 위해, **"작업"**을 선택하고 **"DNS 호스트 이름 편집"**을 선택합니다. **DNS 호스트 이름을 활성화**합니다. ****

![](../.gitbook/assets/image%20%2816%29.png)

![](../.gitbook/assets/image%20%2831%29.png)

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

![](../.gitbook/assets/image%20%286%29.png)

### **6. 라우팅 테이블을 생성.**

| 라우팅 테이블 이름 태그 | 서브넷 연결 |
| :--- | :--- |
| PUBLIC-RT | IMD-PUBLIC-A , IMD-PUBLIC-B |
| PRIVATE-RT | IMD-PRIVATE-A, IMD-PRIVATE-B |

* 좌측 VPC 대시보드에서 **"가상 프라이빗 클라우드" - "라우팅 테이블"** 메뉴를 선택하고, **"라우팅 테이블 생성"**을 선택합니다.
* 2개의 라우팅 테이블을 각각 생성하고, 속성을 정의합니다.

![](../.gitbook/assets/image%20%283%29.png)

* 각 라우팅 테이블에 서브넷을 연결합니다.  PUBLIC-RT, PRIVATE-RT 모두 구성합니다.

![](../.gitbook/assets/image%20%2832%29.png)

![](../.gitbook/assets/image%20%2855%29.png)

![](../.gitbook/assets/image%20%2825%29.png)

![](../.gitbook/assets/image%20%2847%29.png)

### **7. 인터넷 게이트웨이를 생성.**

* 좌측 VPC 대시보드에서 **"가상 프라이빗 클라우드" - "인터넷 게이트웨이"** 메뉴를 선택하고, **"인터넷 게이트웨이 생성"**을 선택합니다.
* 인터넷 게이트웨이 이름을 정의합니다.

![](../.gitbook/assets/image%20%2826%29.png)

* 인터넷 게이트웨이를 VPC에 연결합니다.

![](../.gitbook/assets/image%20%2836%29.png)

![](../.gitbook/assets/image%20%2845%29.png)

* 인터넷 게이트웨이의 상태가 "Attached"로 변경되었는지 확인합니다.

![](../.gitbook/assets/image%20%2856%29.png)

### 8. 라우팅 테이블 업데이트

* Public-RT 라우팅 테이블에 인터넷 게이트웨이로 향하는 트래픽을 업데이트하기 위해, "**라우팅 테이블" - "PUBLIC-RT"**를 선택하고 **"라우팅 편집"**을  선택합니다.

![](../.gitbook/assets/image%20%2865%29.png)

* **"라우팅 추가"**를 선택하고, "**대상"**에 "**0.0.0.0/0"**, **"생성한 IGW"**를 입력하고 선택합니다.

![](../.gitbook/assets/image%20%2860%29.png)

## **Task2. EC2 생성하기**

### **9. EC2 키페어 생성**

* EC2 대시보드에서 **"네트워크 및 보안" - " 키 페어"**를 선택합니다.
* **"키페어 생성"**을 선택합니다.

![](../.gitbook/assets/image%20%2841%29.png)

* 윈도우 Administrator 접속 시 사용되는 키페어를 생성합니다.

![](../.gitbook/assets/image%20%2835%29.png)

* 키페어를 로컬로 다운로드 받습니다.

### **10. EC2 대시보드에서 인스턴스 시작을 선택.**

![](../.gitbook/assets/image%20%287%29.png)

### **11. AMI를 선택.**

* **Microsoft Windows Server 2012R2 Base AMI\(Amazon Machine Image\)** 를 선택합니다. 

![](../.gitbook/assets/image%20%2827%29.png)

### **12. 인스턴스 유형을 선택.**

* **인스턴스 유형 - t2.medium** 을 선택합니다.

![](../.gitbook/assets/image%20%2842%29.png)

### **13. 인스턴스 세부 정보를 구성.**

| 인스턴스 세부 정보 | 값 |
| :--- | :--- |
| 인스턴스 개수 | 1 |
| 네트워크 | IMD-VPC |
| 서브넷 | IMD-PUBLIC-A \| ap-northeast-2a |
| 퍼블릭 IP 자동할당 | 활성화 |

![](../.gitbook/assets/image%20%288%29.png)

* 사용자 데이터 예.

```text
<powershell>
Import-Module ServerManager;
Install-WindowsFeature Web-Server -IncludeManagementTools -IncludeAllSubFeature
remove-item -recurse c:\inetpub\wwwroot\*
(New-Object System.Net.WebClient).DownloadFile("https://immersionday-labs.s3.amazonaws.com/ec2-windows.zip", "c:\inetpub\wwwroot\ec2-windows.zip")

$shell = new-object -com shell.application
$zip = $shell.NameSpace("c:\inetpub\wwwroot\ec2-windows.zip")
foreach($item in $zip.items())
{
$shell.Namespace("c:\inetpub\wwwroot\").copyhere($item)
}
</powershell>
```

* 위의 "사용자 데이터 예"를 복사해서 값을 입력합니다.

![](../.gitbook/assets/image%20%285%29.png)

* "다음:스토리지 추가"를 선택합니다.

### 14. 스토리지 추가.

![](../.gitbook/assets/image%20%2850%29.png)

* AWS에서 제공하는 볼륨 유형들을 확인합니다.
* **범용 SSD\(gp2\) 타입**을 선택합니다.
* **"다음-태그 추가"**를 선택합니다.

### **15. 태그 추가를 구성.**

* 키와 값의 내용을 입력합니다.
* **키 : IMD-EC2 , 값: PUBLIC-03**

![](../.gitbook/assets/image%20%2846%29.png)

* "다음:보안그룹구성"을 선택합니다.

### **16.보안그룹구성 구성.**

* 아래와 같은 값으로 보안 그룹\(Security Group\)을 구성합니다.

| 유형 | 프로토콜 | 포트범위 | 소스 |
| :--- | :--- | :--- | :--- |
| RDP | TCP | 22 | 위치무관 |
| HTTP | TCP | 80 | 위치무관 |
| 모든 ICMP-IPv4 | ICMP | 0-65535 | 위치무관 |

* 보안 그룹 할당 - 새 보안 그룹 생성
* 보안 그룹 이름 : IMD-W2K-PUB-SG
* 설명 : Security Group for IMD-W2K-PUB
* "검토 및 시작"을 선택합니다.

![](../.gitbook/assets/image%20%2811%29.png)

### 17. 인스턴스 시작 검토를 확인.

* 검토를 완료하고 **"시작하기"**를 선택합니다.

![](../.gitbook/assets/image%20%2839%29.png)

### **18. 키 페어 선택 .**

* **"기존 키 페어 선택"을 선택하고 , 해당 키페어를 선택합니다.**\(예. W2K-Key\)

![](../.gitbook/assets/image%20%2818%29.png)

{% hint style="warning" %}
**위의 과정을 반복하여, 서브넷 "IMD-PUBLIC-B" - 가용영역 "ap-northeast-2c"에 EC2 인스턴스를 한 개 더 만들어 봅니다.**
{% endhint %}

## Task3. EC2 접속하기

### 19. EC2 접속 환경 구성

* **정상적으로 EC2 인스턴스들이 구성되었는 지 확인해 봅니다. 인스턴스를 선택하면 Meta 정보들을 확인 할 수 있습니다.**

![](../.gitbook/assets/image%20%2864%29.png)

* 웹기반 클라이언트로 접속이 가능합니다. 접속하려는 **EC2 인스턴스를 선택**하고, EC2 대시보드 상단의 메뉴에서 **"연결"**을 선택합니다. 

![](../.gitbook/assets/image%20%2837%29.png)

* **"독립 실행형 RDP 클라이언트"** 를 선택하고, **암호 - 암호가져오기** 를 선택합니다.

![](../.gitbook/assets/image%20%2821%29.png)

* **로컬에 저장해 둔 키페어를 "키 페어 경로"** 에 선택하고, "**암호해독"** 을 선택합니다.

![](../.gitbook/assets/image%20%2819%29.png)

* RDP 접속 프로그램을 통해 해독된 Administrator 암호를 입력하여 접속합니다.

![](../.gitbook/assets/image%20%2814%29.png)

* 정상적으로 접속되는 지 확인합니다.

![](../.gitbook/assets/image%20%2844%29.png)

### **20. EC2 Linux Web 접속**

* 로컬 브라우저\(파이어폭스, 크롬\)에서 아래 URL로 접속합니다.아래 예제와 같이 EC2 정보들을 확인해 봅니다.

```text
"ec2-public-ip or public DNS"
```

![](../.gitbook/assets/image%20%2838%29.png)

