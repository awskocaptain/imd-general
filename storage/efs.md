---
description: 'Update : 2021-03-17'
---

# EFS

## EFS

### 소개 

Amazon EFS\(Amazon Elastic File System\)는 AWS 클라우드 서비스와 온프레미스 리소스에서 사용할 수 있는, 간단하고 확장 가능하며 탄력적인 완전관리형 NFS 파일 시스템을 제공합니다. 이 제품은 애플리케이션을 중단하지 않고 온디맨드 방식으로 페타바이트 규모까지 확장하도록 구축되어, 파일을 추가하고 제거할 때 자동으로 확장하고 축소하며 확장 규모에 맞게 용량을 프로비저닝 및 관리할 필요가 없습니다.

Amazon EFS는 Standard 스토리지 클래스 및 [Infrequent Access 스토리지 클래스](https://aws.amazon.com/ko/efs/features/infrequent-access/)\(EFS IA\)의 두 가지 스토리지 클래스를 제공합니다. EFS IA에서는 매일 액세스하지 않는 파일에 대해 비용 최적화된 가격/성능을 제공합니다. 파일 시스템에서 EFS 수명 주기 관리를 활성화하기만 하면 선택한 수명 주기 정책에 따라 액세스하지 않은 파일은 EFS IA로 투명하게 자동 이동됩니다. EFS IA 스토리지 클래스 요금은 월별 GB당 0.025 USD에 불과합니다.

워크로드 패턴이 다양하지만, 고객은 일반적으로 80%의 데이터는 자주 액세스하지 않으며\(EFS IA에 적합함\), 20%는 자주 사용하므로\(EFS Standard에 적합함\), 월별 GB당 0.08 USD의 저렴한 스토리지 요금을 이용할 수 있습니다. Amazon EFS는 공통 파일 시스템 네임스페이스에서 두 스토리지 클래스 모두의 파일을 투명하게 지원합니다.

Amazon EFS는 수천 개의 Amazon EC2 인스턴스에 대한 병렬 공유 액세스를 대규모로 제공하도록 설계되었으므로, 애플리케이션은 일관되게 낮은 지연 시간을 유지하면서 높은 수준의 집계 처리량과 IOPS를 달성할 수 있습니다.

Amazon EFS는 홈 디렉터리에서 비즈니스 크리티컬 애플리케이션에 이르기까지 다양한 사용 사례에 적합합니다. 고객은 EFS를 사용하여 리프트 앤 시트프 방식으로 기존 엔터프라이드 애플리케이션을 AWS 클라우드로 마이그레이션할 수 있습니다. 기타 사용 사례로, 빅 데이터 분석, 웹 지원 및 콘텐츠 관리, 애플리케이션 개발 및 테스트, 미디어 및 엔터테인먼트 워크플로, 데이터베이스 백업, 컨테이너 스토리지 등이 있습니다.

Amazon EFS는 AZ\(가용 영역\) 내부와 여러 AZ에 걸쳐 데이터를 저장하여 높은 수준의 가용성과 내구성을 제공하는 지역별 서비스입니다. Amazon EC2 인스턴스는 여러 AZ, 지역 및 VPC에서 파일 시스템에 액세스할 수 있는 반면, 온프레미스 서버는 AWS Direct Connect 또는 AWS VPN을 사용하여 액세스할 수 있습니다.

### 장점 소개

#### POSIX 규정 준수 공유 파일 스토리지 <a id="POSIX-compliant_shared_file_storage"></a>

Amazon EFS는 NFSv4 프로토콜을 통해 기존 파일 권한 모델, 파일 잠금 기능 및 계층적 디렉터리 구조를 사용하여 Amazon EC2 인스턴스 및 온프레미스 서버에서 동시에 이러한 수천 개의 서버 연결에 대한 보안 액세스를 제공합니다. Amazon EC2 인스턴스는 여러 AZ, 리전 및 VPC에서 파일 시스템에 액세스할 수 있는 반면, 온프레미스 서버는 AWS Direct Connect 또는 AWS VPN을 사용하여 액세스할 수 있습니다.

#### 확장 가능한 성능 <a id="Scalable_performance"></a>

Amazon EFS는 Linux 워크로드에 필요한 처리량, IOPS 및 짧은 지연 시간을 제공하도록 설계되었습니다. 처리량과 IOPS는 파일 시스템 증가에 따라 확장되며, 파일 워크로드의 예측할 수 없는 성능 요구 사항을 지원하기 위해 단기간에 더 높은 처리량 수준으로 급증할 수 있습니다. 가장 수요가 많은 워크로드의 경우 Amazon EFS는 10GB/초 이상의 성능과 500,000보다 많은 IOPS를 지원할 수 있습니다.

#### 동적 탄력성 <a id="Dynamic_elasticity"></a>

Amazon EFS는 애플리케이션을 중단하지 않고 파일을 추가하거나 제거할 때 파일 시스템 스토리지 용량을 자동으로 즉시 확장/축소하여 필요한 만큼 스토리지 용량을 동적으로 제공합니다. 스토리지를 미리 프로비저닝할 필요 없이 간단히 파일 시스템을 생성하고 파일을 추가하면 됩니다.

#### 완전관리형 <a id="Fully_managed"></a>

Amazon EFS는 Linux 워크로드에 대한 공유 파일 시스템 스토리지를 제공하는 완전 관리형 서비스입니다. 이 서비스는 파일 시스템을 빠르게 생성하고 구성할 수 있는 간단한 인터페이스를 제공하며 파일 스토리지 인프라를 자동으로 관리하여 파일 시스템의 기초를 배포, 패치 및 유지 관리하는 복잡성을 제거합니다.

#### 비용 효율성 <a id="Cost-effective"></a>

Amazon EFS 스토리지를 사용하면 사용한 만큼만 비용을 지불합니다. 스토리지를 미리 프로비저닝할 필요가 없으며 최소 약정이나 선불 수수료가 없습니다. EFS 수명 주기 관리를 활용하면 액세스 빈도가 낮은 파일을 비용이 최적화된 스토리지 클래스로 자동으로 이동하여 스토리지 비용을 92%까지 절감할 수 있습니다. 또한 AWS 예산을 사용하여 파일 시스템 비용을 모니터링할 수 있습니다.

#### 보안 및 규정 준수 <a id="Security_and_compliance"></a>

Amazon EFS에서는 기존 보안 인프라를 사용하여 파일에 안전하게 액세스할 수 있습니다. POSIX 권한, [Amazon VPC](https://aws.amazon.com/ko/vpc/) 및 [AWS IAM](https://aws.amazon.com/ko/iam/)을 사용하여 Amazon EFS 파일 시스템에 대한 액세스를 제어합니다. 유휴 데이터와 전송 중인 데이터를 암호화하여 데이터를 보호합니다. 또한 Amazon EFS는 다양한 자격 및 규정 준수 요구 사항을 충족하여 사용자가 규제 요건을 충족할 수 있도록 도와줍니다. 

## 목적

본 랩은 아래와 같은 구성을 통해 EC2에서 EFS로 NFS 마운트를 구성해 보고, S3 의 데이터를 NFS로 복사해 봅니다.

* **EFS 생성 및 보안 설정**
* **EC2에서 EFS로 NFS 마운트**
* **EC2에 S3 접근 권한 설정**
* **VPC Endpoint 구성**
* **S3에서 NFS로 파일 복사**

## **Task1. EFS 구성하기**

### **1.Security Group \(보안그룹\) 생성**

* VPC - 보안 그룹 - 보안 그룹 생성 메뉴에서 EC2가 EFS로 접근 가능하도록 보안 설정을 합니다.
* 보안 그룹 이름 - " EFS-SG "
* VPC - IMD-VPC
* 인바운드 규칙
  * 유형 - NFS
  * 소스 - 위치무

![](../.gitbook/assets/image%20%28464%29.png)

### 2. EFS 구성

* AWS 관리 콘솔에서 " EFS " 를 검색하고, 선택합니다.
* 파일 시스템 생성을 선택합니다.
  * 이름 : IMD-EFS
  * VPC : IMD-VPC
  * 가용성 및 내구성 : 리전
  * 사용자 지정 선

![](../.gitbook/assets/image%20%28458%29.png)

### 3. EFS 파일시스템 설정

* 이름 : EFS 이름 선언
* 가용성 및 내구성 - 리전 선택
* 자동백업 - 활성화
* 수명주기 관리 - 마지막 액세스 이후 30일 경과
* 성능 모드 - 범용 선택
* 처리량 모드 - 버스트 선택
* 암호화 - 유휴 시 데이터 암호화 활성화 선택
* 태그 설

![](../.gitbook/assets/image%20%28445%29.png)

### 4. EFS 네트워크 액세스

* EC2가 접근할 네트워크를 설정합니다.
* VPC - IMD-VPC
* 탑재 대상 - EFS가 연결될 네트워크를 선택합니다.
  * 가용영역 - ap-northeast-2a, 2c 선택
  * 서브넷 ID - Private Subnet a,b 선택
  * IP 주소 - 10.1.11.201, 10.1.12.201 
  * 보안그룹 - 이전 단계에서 생성한 EFS-SG 선

![](../.gitbook/assets/image%20%28482%29.png)

### 5.파일 시스템 정책 \(선택사항\)

* 정책 옵션을 다양하게 추가할 수 있습니다.

![](../.gitbook/assets/image%20%28488%29.png)

### 6. 생성된 EFS 확인

* 파일 시스템 상태가 **사용가능**으로 변경되었는지 확인합니다.

![](../.gitbook/assets/image%20%28456%29.png)

### 7. EC2 보안 그룹 변경

*  Private01,02 인스턴스의 보안 정책에 신규 보안 그룹을 추가합니다.

![](../.gitbook/assets/image%20%28426%29.png)

![](../.gitbook/assets/image%20%28494%29.png)

## Task2. EFS Mount

### 8. EC2 NFS Mount

* Bastion Host 에서 Private-01,02로 접속하거나, Session Manager를 통해서 접속합니다.
* 아래 명령을 통해 Private-01,02 인스턴스가 EFS에 마운트 합니다.
  * efs의 파일 시스템 ID는[ 6. 생성된 EFS 확인](efs.md#6-efs) 에서 복사합니다.

```text
ssh ec2-user@10.1.11.101
mkdir ~/aws-efs
sudo yum install -y amazon-efs-utils
sudo mount -t efs fs-36892f56:/ ~/aws-efs/
df -h

ssh ec2-user@10.1.12.101
mkdir ~/aws-efs
sudo yum install -y amazon-efs-utils
sudo mount -t efs fs-36892f56:/ ~/aws-efs/
df -h

```

각 Private01,02 EC2에서 아래와 같이 EFS가 마운트 된 것을 확인합니다.

```text
[ec2-user@ip-10-1-11-101 ~]$ df -h
Filesystem                                      Size  Used Avail Use% Mounted on
devtmpfs                                        482M     0  482M   0% /dev
tmpfs                                           492M     0  492M   0% /dev/shm
tmpfs                                           492M  404K  492M   1% /run
tmpfs                                           492M     0  492M   0% /sys/fs/cgroup
/dev/xvda1                                      8.0G  1.5G  6.6G  19% /
tmpfs                                            99M     0   99M   0% /run/user/1000
fs-36892f56.efs.ap-northeast-2.amazonaws.com:/  8.0E     0  8.0E   0% /home/ec2-user/aws-efs

```

## Task3. S3 와 EFS 연

### 9.  S3 VPC Endpoint 구성

### 10. EC2에 S3 접근 권한 설정

### 11. EC2에서 S3 파일을 EFS로 Copy





