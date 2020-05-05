# RDS

## 소개

Amazon Relational Database Service\(RDS\)를 사용하면 클라우드에서 관계형 데이터베이스를 간편하게 설정, 운영 및 확장할 수 있습니다. 하드웨어 프로비저닝, 데이터베이스 설정, 패치 및 백업과 같은 시간 소모적인 관리 작업을 자동화하면서 비용 효율적이고 크기 조정 가능한 용량을 제공합니다. 사용자가 애플리케이션에 집중하여 애플리케이션에 필요한 빠른 성능, 고가용성, 보안 및 호환성을 제공할 수 있도록 지원합니다.

Amazon RDS는 여러 [데이터베이스 인스턴스 유형](https://aws.amazon.com/ko/rds/instance-types/)\(메모리, 성능 또는 I/O 최적화\)으로 제공되며 [Amazon Aurora](https://aws.amazon.com/ko/rds/aurora/), [PostgreSQL](https://aws.amazon.com/ko/rds/postgresql/), [MySQL](https://aws.amazon.com/ko/rds/mysql/), [MariaDB](https://aws.amazon.com/ko/rds/mariadb/), [Oracle Database](https://aws.amazon.com/ko/rds/oracle/) 및 [SQL Server](https://aws.amazon.com/ko/rds/sqlserver/)를 비롯한 6개의 익숙한 데이터베이스 엔진 중에서 선택할 수 있습니다. [AWS Database Migration Service](https://aws.amazon.com/ko/dms/)를 사용하여 기존 데이터베이스를 Amazon RDS로 손쉽게 마이그레이션 또는 복제할 수 있습니다.

## **목적**

\*\*\*\*

**Task1.**

**Task2. RDS 만들기**

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

![](../.gitbook/assets/image%20%28178%29.png)

![](../.gitbook/assets/image%20%2871%29.png)

![](../.gitbook/assets/image%20%2881%29.png)

![](../.gitbook/assets/image%20%2813%29.png)

![](../.gitbook/assets/image%20%2832%29.png)

![](../.gitbook/assets/image%20%2888%29.png)

![](../.gitbook/assets/image%20%28210%29.png)

![](../.gitbook/assets/image%20%28146%29.png)

![](../.gitbook/assets/image%20%28202%29.png)

![](../.gitbook/assets/image%20%286%29.png)

![](../.gitbook/assets/image%20%28118%29.png)

![](../.gitbook/assets/image%20%28136%29.png)

![](../.gitbook/assets/image%20%28182%29.png)

![](../.gitbook/assets/image%20%2843%29.png)

