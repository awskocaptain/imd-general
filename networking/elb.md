---
description: 'Update : 2021-03-15'
---

# ELB

## 소개

Elastic Load Balancing은 들어오는 애플리케이션 트래픽을 Amazon EC2 인스턴스, 컨테이너, IP 주소, Lambda 함수와 같은 여러 대상에 자동으로 분산시킵니다. Elastic Load Balancing은 단일 가용 영역 또는 여러 가용 영역에서 다양한 애플리케이션 부하를 처리할 수 있습니다. Elastic Load Balancing이 제공하는 세 가지 로드 밸런서는 모두 애플리케이션의 내결함성에 필요한 고가용성, 자동 확장/축소, 강력한 보안을 갖추고 있습니다.

##  목적

본 랩은 아래와 같은 구성을 통해 NLB와 ALB 구성을 통해 차이점과 구성 방

![NLB &#xBAA9;&#xD45C; &#xAD6C;&#xC131;&#xB3C4;](../.gitbook/assets/image%20%28276%29.png)

![ALB &#xBAA9;&#xD45C; &#xAD6C;&#xC131;&#xB3C4;](../.gitbook/assets/image%20%2895%29.png)

* [ ] **NLB와 ALB 구성이 차이 이해**
* [ ] **NLB의 로드밸런싱 방식 구성과 이해**
* [ ] **NLB EIP 할당**
* [ ] **ALB의 로드밸런싱 방식 구성과 이해**

## Task1 : NLB 구성

### 1.NLB용 인스턴스 구성

* NLB 시험용 인스턴스 4개를 구성합니다.
* AMI - Amazon Linux 2
* 인스턴스 유형 - t2.micro
* 네트워크 - IMD-VPC / 각 Public Subnet 별 2개 EC2 인스턴스 / 퍼블릭 IP 자동할당 활성화
* 사용자 데이터

```text
#!/bin/sh
sudo yum -y update
sudo yum -y install yum-utils
sudo yum -y install https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
sudo yum -y install httpd php mysql php-mysql
sudo usermod -a -G apache ec2-user
sudo chown -R ec2-user:apache /var/www
sudo chmod 2775 /var/www
find /var/www -type f -exec sudo chmod 0664 {} \;
sudo touch /var/www/index.html
sudo systemctl start httpd
sudo systemctl enable httpd
```

* 보안그룹을 새로 작성합니다. \(보안그룹 이름 - NLB-SG : 보안 정책은 22번, 80포트를 허용합니다.\)
* 기존 키 페어 또는 새로운 키페어를 생성합니다.

![](../.gitbook/assets/image%20%28238%29.png)

### 2. NLB를 위한 EIP 생성

* 먼저 NLB 고정 IP 부여를 위한 EIP를 생성합니다. \(NLB는 ALB와 다르게 EIP부여가 가능합니다. 필수 조건은 아니므로 생략해도 됩니다.\)

![](../.gitbook/assets/image%20%28261%29.png)

* EIP 할당을 위한 Amazon IP 주소풀을 선택하여 고정 IP를 할당 받습니다. 2개의 서브넷에 할당하게 되므로 2번 반복해서 2개를 할당 받습니다.

![](../.gitbook/assets/image%20%2844%29.png)

* 2개를 할당 받으면 아래와 같이 2개의 EIP가 생성됩니다.

![](../.gitbook/assets/image%20%28350%29.png)

### 3. NLB 생성

* NLB 생성을 위해 로드밸런서 생성을 선택합니다.

![](../.gitbook/assets/image%20%28141%29.png)

* 로드밸런서 유형을 NLB를 선택합니다.

![](../.gitbook/assets/image%20%28251%29.png)

* 1단계  로드밸런서 구성에서 이름을 선택하고, 체계는 인터넷 연결 \(Public Subnet\)을 선택합니다. Private 의 경우에는 내부를 선택하면 됩니다.

![](../.gitbook/assets/image%20%28138%29.png)

* 가용영역은 2개의 서브넷을 선택합니다. 또한 IPv4 주소는 "탄력적 IP 선택"을 선택하고, 미리 할당해 놓은 EIP를 선택합니다.

![](../.gitbook/assets/image%20%2838%29.png)

{% hint style="info" %}
가용 영역과 서브넷을 신중하게 선택하십시오. 로드 밸런서를 생성한 후에는 활성화된 서브넷을 비활성화할 수 없지만, 서브넷을 추가로 활성화할 수 있습니다.
{% endhint %}

* 3단계 라우팅 구성에서는 대상 그룹을 선택합니다. 대상 유형을 인스턴스를 선택합니다.

![](../.gitbook/assets/image%20%28195%29.png)

* 상태검사에서는 간격을 Default 30초에서 10초로 변경합니다.

![](../.gitbook/assets/image%20%28268%29.png)

* 4단계 대상 등록에서 미리 만들어 놓은 NLB 대상의 **EC2 인스턴스들의 보안그룹을 선택**합니다. **상단의 등록된 항목에 추가 버튼을 선택**해서 대상등록을 마칩니다.

![](../.gitbook/assets/image%20%28324%29.png)

### 4. NLB 대상그룹 활성화 확인

* NLB가 대상 그룹에 대한 Healthy 체크를 시작합니다. 대상 그룹 상태를 확인합니다.

![](../.gitbook/assets/image%20%28341%29.png)

* 앞서 생성한 EC2 인스턴스에 SSH로 접속해서 아래 Script를 복사합니다.

```text
sudo echo "<html><h2>My Public IP is: $(curl -s http://169.254.169.254/latest/meta-data/public-ipv4/)</h2></html>" >> /var/www/html/index.html 
sudo echo "<html><h2>My Private IP is: $(curl -s http://169.254.169.254/latest/meta-data/local-ipv4/)</h2></html>" >> /var/www/html/index.html 
sudo echo "<html><h2>My Host Name is: $(curl -s http://169.254.169.254/latest/meta-data/hostname/)</h2></html>" >> /var/www/html/index.html 
sudo echo "<html><h2>My instance-id is: $(curl -s http://169.254.169.254/latest/meta-data/instance-id/)</h2></html>" >> /var/www/html/index.html 
sudo echo "<html><h2>My instance-type is: $(curl -s http://169.254.169.254/latest/meta-data/instance-type)</h2></html>" >> /var/www/html/index.html 
sudo echo "<html><h2>My placement/availability-zone is: $(curl -s http://169.254.169.254/latest/meta-data/placement/availability-zone)</h2></html>" >> /var/www/html/index.html 

```

* NLB가 대상 그룹에 대한 Healthy 체크가 정상인지 확인합니다.

![](../.gitbook/assets/image%20%28308%29.png)

* NLB는 서로 다른 리전간의 LB를 속성 편집에서 구성해야 합니다. 비용은 ALB와 다르게 부과 됩니다.

![](../.gitbook/assets/image%20%28145%29.png)

5. NLB 시험

* NLB DNS Name 또는 EIP에 접속하여 결과를 확인합니다.

{% hint style="info" %}
로드 밸런서는 프로토콜, 원본 IP 주소, 원본 포트, 대상 IP 주소, 대상 포트, TCP 시퀀스 번호에 따라 흐름 해시 알고리즘을 사용하여 대상을 선택합니다. 클라이언트로부터의 TCP 연결은 소스 포트와 시퀀스 번호가 서로 다르므로 다른 대상에 라우팅될 수 있습니다. 각 TCP 연결은 연결 수명 동안 하나의 대상에 라우팅됩니다.

따라서 ALB와 같은 결과 처럼 LB가 원하는 결과가 아닐 수 있습니다.
{% endhint %}

![](../.gitbook/assets/image%20%28143%29.png)



## Task2 : ALB 생성

{% hint style="info" %}
**ALB랩에서는 EC2-Linux에서 생성한 EC2 자원을 그대로 사용합니다.**
{% endhint %}

### 5.로드밸런서 생성

* AWS 관리 콘솔에서 **EC2 서비스**를 선택합니다. **"로드밸런싱"-"로드밸런서"를 선택**합니다. 로드밸런서 유형은 **ALB를 선택**합니다.

![](../.gitbook/assets/image%20%28290%29.png)

### **6. 로드밸런서 구성**

* **1단계 로드밸런스 구성을 선택**합니다. **로드밸런서 이름을 생성**합니다. **리스너는 기본 HTTP , 80**을 유지 합니다.

![](../.gitbook/assets/image%20%28266%29.png)

### 7. 보안 그룹 생성

* **EC2 Computing LAB에서 생성한 보안그룹을 사용**합니다. Public 보안 그룹을 사용합니다. 해당 보안그룹에는 **80 포트가 허용**되어 있어야 합니다

![](../.gitbook/assets/image%20%28248%29.png)

### 8. 라우팅 구성

* 라우팅 대상 그룹 \(Real Server\)를 선택합니다. Real Server 들의 그룹을 정의하고 대상 유형은 인스턴스로 선택합니다. \(PUBLIC-01,02\) 
* 상태 검사는 PUBLIC-01,02에 포함되어 있는 웹서비스 URL을 선택합니다. PUBLIC-01,02에는 "/var/www/html/ec2meta-webpage/index.php" 에서 웹서비스를 제공하고 있습니다. 해당 경로에 대해서 상태 검사를 수행합니다.

```text
/ec2meta-webpage/index.php
```

![](../.gitbook/assets/image%20%28173%29.png)

### 9. 대상서버 \(Real Server\)를 등록

* 대상서버를 등록합니다. 대상서버는 Public Subnet-A,B에 할당된 서버입니다. \(PUBLIC-A, PUBLIC-B\)

![](../.gitbook/assets/image%20%28182%29.png)

### 10. 대상 서버 상태 확인

* 대상서버가 **"Healthy" 상태**인지를 확인합니다.

![](../.gitbook/assets/image%20%28163%29.png)

### 11. 웹서비스 확인

* ELB DNS A레코드 주소로 정상적으로 웹서비스가 제공되는 지 확인합니다. 웹페이지를 Refresh 할 때 마다 페이지의 정보가 다르게 변경됩니다. 라운드로빈으로 로드밸런싱이 이뤄지기 때문입니다.

![](../.gitbook/assets/image%20%28337%29.png)

![](../.gitbook/assets/image%20%2868%29.png)

{% hint style="success" %}
**성공적으로 ELB 랩을 마치셨습니다.**
{% endhint %}

