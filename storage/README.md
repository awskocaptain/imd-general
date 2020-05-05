---
description: Storage Hands On LAB 소개
---

# Storage

## 클라우드 스토리지 소개

클라우드 스토리지는 클라우드 컴퓨팅의 매우 중요한 구성 요소로서, 애플리케이션에서 사용하는 정보를 보관합니다. 모든 빅 데이터 분석, 데이터 웨어하우스, 사물 인터넷, 데이터베이스, 백업 및 아카이브 애플리케이션에는 일정 형태의 데이터 스토리지 아키텍처가 필요합니다. 클라우드 스토리지는 일반적으로 기존 온프레미스 스토리지 시스템보다 안정성, 확장성 및 보안성이 뛰어납니다.  


AWS에서는 모든 범위의 클라우드 스토리지 서비스를 제공하여 애플리케이션과 아카이브 규정 준수 요구 사항을 모두 지원합니다. 객체, 파일 및 블록 스토리지 서비스와 클라우드 데이터 마이그레이션 옵션을 선택하여 클라우드 IT 환경의 기초 설계를 시작하십시오.

## AWS 클라우드 스토리지 제품 소개

| 요구 사항:  | 추천 제품: |
| :--- | :--- |
| Amazon EC2, 관계형 및 NoSQL 데이터베이스, 데이터 웨어하우징, 엔터프라이즈 애플리케이션, 빅 데이터 처리 또는 백업 및 복구를 위한 영구 로컬 스토리지 | [Amazon Elastic Block Store  \(Amazon EBS\)](https://aws.amazon.com/ko/products/storage/?nc2=h_ql_prod_st#ebs) |
| Linux 기반의 워크로드를 AWS 클라우드 서비스와 온프레미스 리소스에서 사용할 수 있도록 지원하는 간단하고 확장 가능하며 탄력적인 파일 시스템입니다. 이 제품은 애플리케이션을 중단하지 않고 온디맨드 방식으로 페타바이트 규모까지 확장하도록 구축되어 파일을 추가하고 제거할 때 자동으로 확장되고 축소되므로, 애플리케이션은 필요할 때 필요한 만큼 스토리지를 사용할 수 있습니다. | [Amazon Elastic File System  \(Amazon EFS\)](https://aws.amazon.com/ko/products/storage/?nc2=h_ql_prod_st#efs) |
| 고성능 컴퓨팅, 기계 학습 및 미디어 데이터 처리 워크플로우와 같이 컴퓨팅 집약적 워크로드에 최적화된 완전관리형 파일 시스템으로, Amazon S3에 완벽하게 통합되어 있습니다. | [Amazon FSx for Lustre](https://aws.amazon.com/ko/fsx/lustre/) |
| Windows Server를 기반으로 구축된 완전관리형 네이티브 Microsoft Windows 파일 시스템으로, 이 제품을 사용하면 SMB 프로토콜 및 Windows NTFS, AD\(Active Directory\) 통합, DFS\(분산 파일 시스템\)에 대한 전체 지원을 비롯하여, 파일 스토리지가 필요한 Windows 기반 애플리케이션을 AWS로 쉽게 이전할 수 있습니다.  | [Amazon FSx for Windows File Server](https://aws.amazon.com/ko/fsx/windows/) |
| 사용자 생성 콘텐츠, 활성 아카이브, 서버리스 컴퓨팅, 빅 데이터 스토리지 또는 백업 및 복구를 위해 인터넷 위치에서 데이터에 액세스할 수 있도록 지원하는 확장 가능하고 안정적인 플랫폼입니다. | [Amazon Simple Storage Service  \(Amazon S3\)](https://aws.amazon.com/ko/products/storage/?nc2=h_ql_prod_st#s3) |
| 아카이브 및 규제 준수를 위해 테이프를 대체할 수 있는 매우 저렴한 장기 스토리지 클래스입니다. | [Amazon S3 Glacier 및 Amazon S3 Glacier Deep Archive](https://aws.amazon.com/ko/products/storage/?nc2=h_ql_prod_st#glacier)  |
| 버스팅, 계층화 또는 마이그레이션을 위해 Amazon 클라우드 스토리지로 온프레미스 환경을 보강해주는 하이브리드 스토리지 클라우드입니다. | [AWS Storage Gateway](https://aws.amazon.com/ko/products/storage/?nc2=h_ql_prod_st#gateway)  |
| 유형 및 크기에 상관없이 모든 데이터를 AWS 클라우드로, 혹은 AWS 클라우드에서 이동하는 작업을 간소화 및 가속화할 수 있도록 지원하는 서비스 포트폴리오입니다.  | [클라우드 데이터 마이그레이션 서비스](https://aws.amazon.com/ko/cloud-data-migration/)  |
| 클라우드뿐 아니라 온프레미스에서도 AWS Storage Gateway를 사용해 AWS 서비스 전체에서 데이터 백업을 손쉽게 중앙화하고 자동화할 수 있는 완전관리형 백업 서비스입니다.  | [AWS Backup](https://aws.amazon.com/ko/backup/) |

