# RDS

## 소개

Amazon Relational Database Service\(RDS\)를 사용하면 클라우드에서 관계형 데이터베이스를 간편하게 설정, 운영 및 확장할 수 있습니다. 하드웨어 프로비저닝, 데이터베이스 설정, 패치 및 백업과 같은 시간 소모적인 관리 작업을 자동화하면서 비용 효율적이고 크기 조정 가능한 용량을 제공합니다. 사용자가 애플리케이션에 집중하여 애플리케이션에 필요한 빠른 성능, 고가용성, 보안 및 호환성을 제공할 수 있도록 지원합니다.

Amazon RDS는 여러 [데이터베이스 인스턴스 유형](https://aws.amazon.com/ko/rds/instance-types/)\(메모리, 성능 또는 I/O 최적화\)으로 제공되며 [Amazon Aurora](https://aws.amazon.com/ko/rds/aurora/), [PostgreSQL](https://aws.amazon.com/ko/rds/postgresql/), [MySQL](https://aws.amazon.com/ko/rds/mysql/), [MariaDB](https://aws.amazon.com/ko/rds/mariadb/), [Oracle Database](https://aws.amazon.com/ko/rds/oracle/) 및 [SQL Server](https://aws.amazon.com/ko/rds/sqlserver/)를 비롯한 6개의 익숙한 데이터베이스 엔진 중에서 선택할 수 있습니다. [AWS Database Migration Service](https://aws.amazon.com/ko/dms/)를 사용하여 기존 데이터베이스를 Amazon RDS로 손쉽게 마이그레이션 또는 복제할 수 있습니다.

## **목적**

본 Lab은 아래의 내용을 포함하고 있으며, 목표 구성도는 아래와 같습니다.

![](../.gitbook/assets/image%20%28169%29.png)

* [ ] **RDS를 위한 보안 그룹\(Security Group\) 생성**
* [ ] **RDS 인스턴스 생성**
* [ ] **EC2 인스턴스에서의 접근 설정**
* [ ] **Web Server에서 RDS 연결 확인**
* [ ] **RDS Failover , RDS Snapshot**

## **Task1.RDS 연결용 WebServer 만들기**

### 1.RDS연결용 WebServer 만들기

* 단계1 - Amazon Linux 2 AMI를 선택합니다.

![](../.gitbook/assets/image%20%28140%29.png)

* 단계2 - T2 micro 타입을 선택합니다.

![](../.gitbook/assets/image%20%2858%29.png)

* 단계3 - 인스턴스 세부정보를 구성합니다. 기존 생성된 VPC를 선택하고, 서브넷은 Public Subnet을 선택합니다.고급세부정보에는 아래 user data를 복사해서 넣습니다. 아래 2개의 Script 중에 한개만 복사하면 됩니다.

```text
#include https://s3.amazonaws.com/immersionday-labs/bootstrap.sh
```

```text
#!/bin/sh
yum -y install httpd php mysql php-mysql

case $(ps -p 1 -o comm | tail -1) in
systemd) systemctl enable --now httpd ;;
init) chkconfig httpd on; service httpd start ;;
*) echo "Error starting httpd (OS not using init or systemd)." 2>&1
esac

if [ ! -f /var/www/html/bootcamp-app.tar.gz ]; then
cd /var/www/html
wget https://s3.amazonaws.com/immersionday-labs/bootcamp-app.tar
tar xvf bootcamp-app.tar
chown apache:root /var/www/html/rds.conf.php
fi
yum -y update
```

![](../.gitbook/assets/image%20%2831%29.png)

![](../.gitbook/assets/image%20%28192%29.png)

* 단계4 - 스토리지를 추가합니다.

![](../.gitbook/assets/image%20%2823%29.png)

* 단계5 - 태그를 추가합니다.

![](../.gitbook/assets/image%20%28154%29.png)

* 단계6 - 보안그룹을 구성합니다.

![](../.gitbook/assets/image%20%28205%29.png)

* 단계7 - 검토와 키페어 선택을 합니다.앞서 랩에서 만들어 둔 키페어를 선택합니다.

![](../.gitbook/assets/image%20%2841%29.png)

### 2. RDS연결을 위한 보안그룹 수정

* RDS연결을 위한 보안 그룹을 수정합니다.

![](../.gitbook/assets/image%20%2828%29.png)

![](../.gitbook/assets/image%20%2850%29.png)

## **Task2. RDS 만들기**

AWS RDS를 구성하기 위해 설정하는 속성 값들을 아래에서 참조합니다. 본랩에서는 Amazon Aurora를 구성합니다.

### 3. Amazon Aurora 구성

* AWS서비스에서 RDS를 선택하고 Amazon Aurora의 **데이터베이스 생성을** 선택합니다.

![](../.gitbook/assets/image%20%28278%29.png)

### **4. RDS-데이터베이스 생성**

* 아래와 같이 표준생성으로 RDS 데이터베이스를 생성합니다. 

| **키** | 값 |
| :--- | :--- |
| 생성방식 | 표준생성 |
| 엔진유형 | Amazon Aurora |
| 에디션 | MySQL과 호환되는 Amazon Aurora |
| 버전 | Aurora \(MySQL\)-5.6.10a |
| 데이터베이스위치 | 리전 |
| 데이터베이스 기능 | 단일 쓰기 및 다중 읽기 |
| 템플릿 | 프로덕션 |
| DB 클러스터 식별자 | rdscluster |
| 마스터 사용자 이름 | awsuser |
| 마스터 암호 | awspassword |
| DB 인스턴스 크기 | 버스터블 클래스 |
| 가용성 및 내구성 | 다른 AZ에 Aurora 복제본/읽기 노드 생성 |
| VPC | IMD-VPC |
| 서브넷 그룹 | 새 DB 서브넷 그룹 생성 |
| 퍼블릭 액세스 가능 | 아니오 |
| VPC 보안 그룹 | 기존 항목 선택: IMD-PRI-SG |
| 데이터베이스포트 | 3306 |
| DB인스턴스 식별자 | awsdb |
| 초기 데이터베이스 이름 | imd |
| DB 클러스터 파라미터 그룹 | default.aurora5.6 |
| DB 파라미터 그룹 | default.aurora5.6 |
| 옵션그룹 | default:aurora-5-6 |

![](../.gitbook/assets/image%20%28217%29.png)

![](../.gitbook/assets/image%20%2894%29.png)

* 마스터 사용자 이름  

```text
awsuser
```

* 마스터 암호

```text
awspassword
```

![](../.gitbook/assets/image%20%28107%29.png)

![](../.gitbook/assets/image%20%2813%29.png)

![](../.gitbook/assets/image%20%2838%29.png)

![](../.gitbook/assets/image%20%28115%29.png)

* 구성이 완료되면 아래와 같이 **"사용가능"**으로 변경됩니다.

![](../.gitbook/assets/image%20%28257%29.png)

{% hint style="warning" %}
DB 생성까지는 10분 이상 시간이 발생할 수 있습니다.
{% endhint %}

### 5.RDS 접속 및 DB추가

* RDS연결을 위해 구성한 EC2 인스턴스의 Public DNS 주소를 확인하고 입력해서 접속합니다. 웹 화면에서 RDS를 클릭합니다.

![](../.gitbook/assets/image%20%28185%29.png)

* RDS 엔드포인트 주소는 RDSCluster를 선택하면 하단에서 확인 가능하며, 쓰기 엔드포인트 이름을 복사합니다.

![](../.gitbook/assets/image%20%2814%29.png)

* 앞서 구성했던 Database 이름과 Username, Password를 입력합니다. 

database

```text
imd
```

username

```text
awsuser
```

password

```text
awspassword
```

![](../.gitbook/assets/image%20%28248%29.png)

* Submit을 클릭하면 새로운 DB 값이 입력되는 것을 확인 할 수 있습니다.

![](../.gitbook/assets/image%20%286%29.png)

{% hint style="info" %}
정상적으로 값이 입력되지 않는 것은, 대부분 보안 그룹 설정 이슈일 수 있습니다. 보안 그룹 설정을 확인 합니다.
{% endhint %}

## Task3. RDS 장애조치와 스냅샷

### 6. 장애조치

* 현재 사용중인 DB의 Master를 Failover로 넘겨봅니다. Read Mode의 DB가 정상적으로 Failover 되는지 확인합니다. 쓰기 역할의 DB를 선택하고 "작업"-"장애조치"를 선택합니다.

![](../.gitbook/assets/image%20%28147%29.png)

![](../.gitbook/assets/image%20%28167%29.png)

* 정상적으로 Failover가 일어나는 지 확인합니다.

![](../.gitbook/assets/image%20%28223%29.png)

### 7. DB Snapshot

* DB 스냅샷 구성을 위해서 현재 쓰기 역할의 DB를 "작업"-"스냅샷" 생성을 선택합니다.

![](../.gitbook/assets/image%20%2859%29.png)

* DB 스냅샷 이름을 생성하고, 스냅샷 만들기를 선택합니다.

![](../.gitbook/assets/image%20%28109%29.png)

* DB Cluster가 백업중으로 상태가 표시됩니다.

![](../.gitbook/assets/image%20%28170%29.png)

* 백업이 완료되면 스냅샷 목록에서 사용가능을 확인 할 수 있습니다. 복원을 원하면 스냅샷 작업을 선택하고 복원 할 수 있습니다.

![](../.gitbook/assets/image%20%28124%29.png)

{% hint style="warning" %}
스냅샷 사용가능 상태까지 시간이 소요 될 수 있습니다. 본랩에서는 스냅샷 복원은 하지 않습니다.
{% endhint %}

![](../.gitbook/assets/image%20%28253%29.png)

{% hint style="success" %}
RDS DB구성 랩을 모두 완료하셨습니다.
{% endhint %}

## 참조자료

* [AWS DataBase](https://aws.amazon.com/ko/products/databases/)
* [RDS 소개](https://aws.amazon.com/ko/rds/?nc2=h_ql_prod_fs_rds)
* [Amazon Aurora 소개](https://aws.amazon.com/ko/rds/aurora/)
* [RDS 기능](https://aws.amazon.com/ko/rds/features/)
* [RDS 리소스](https://aws.amazon.com/ko/rds/resources/)
* [RDS FAQ](https://aws.amazon.com/ko/rds/faqs/)



