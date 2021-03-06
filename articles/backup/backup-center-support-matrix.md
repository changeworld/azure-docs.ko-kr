---
title: 백업 센터에 대 한 지원 매트릭스
description: 이 문서에서는 백업 센터에서 각 워크 로드 유형에 대해 지 원하는 시나리오를 요약 합니다.
ms.topic: conceptual
ms.date: 09/07/2020
ms.openlocfilehash: d6e5d34e201edda4fd1e9fda85f210fb88211e28
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102504510"
---
# <a name="support-matrix-for-backup-center"></a>백업 센터에 대 한 지원 매트릭스

백업 센터는 [대규모의 백업을 관리 하 고 모니터링 하 고 운영 하 고 분석](backup-center-overview.md)하는 엔터프라이즈를 위한 단일 창을 제공 합니다. 이 문서에서는 백업 센터에서 각 워크 로드 유형에 대해 지 원하는 시나리오를 요약 합니다.

## <a name="supported-scenarios"></a>지원되는 시나리오

| **범주** | **시나리오**  | **지원되는 워크로드**  | **제한** |
| -------------| ------------- | ----------------------- |------------|
| 모니터링   | 모든 작업 보기 | <li> Azure Virtual Machine <br><br> <li> Azure Database for PostgreSQL 서버 <br><br> <li> Azure VM의 SQL <br><br> <li> Azure VM의 SAP HANA <br><br> <li> Azure 파일<br/><br/> <li>Azure Blob<br/><br/> <li>Azure Managed Disks | <li> 7 일 분량의 작업은 즉시 사용할 수 있습니다. <br> <li> 각 필터/드롭다운에서 최대 1000 항목을 지원 합니다. 따라서 Backup center를 사용 하 여 테 넌 트 간에 최대 1000 구독 및 1000 자격 증명 모음을 모니터링할 수 있습니다. |
| 모니터링 | 모든 백업 인스턴스 보기 | <li> Azure Virtual Machine <br><br> <li> Azure Database for PostgreSQL 서버 <br><br> <li> Azure VM의 SQL <br><br> <li> Azure VM의 SAP HANA <br><br> <li> Azure 파일<br/><br/> <li>Azure Blob<br/><br/> <li>Azure Managed Disks | 위와 동일 |
| 모니터링 | 모든 백업 정책 보기 | <li> Azure Virtual Machine <br><br> <li> Azure Database for PostgreSQL 서버 <br><br> <li> Azure VM의 SQL <br><br> <li> Azure VM의 SAP HANA <br><br> <li> Azure 파일<br/><br/> <li>Azure Blob<br/><br/> <li>Azure Managed Disks | 위와 동일 |
| 모니터링 | 모든 자격 증명 모음 보기 | <li> Azure Virtual Machine <br><br> <li> Azure Database for PostgreSQL 서버 <br><br> <li> Azure VM의 SQL <br><br> <li> Azure VM의 SAP HANA <br><br> <li> Azure 파일<br/><br/> <li>Azure Blob<br/><br/> <li>Azure Managed Disks | 위와 동일 |
| 작업 | 백업 구성 | <li> Azure Virtual Machine <br><br> <li> Azure Database for PostgreSQL 서버 <br><br> <li> Azure VM의 SQL <br><br> <li> Azure VM의 SAP HANA <br><br> <li> Azure 파일<br/><br/> <li>Azure Blob<br/><br/> <li>Azure Managed Disks | [AZURE VM 백업](./backup-support-matrix-iaas.md) 및 [Azure Database for PostgreSQL 서버 백업](backup-azure-database-postgresql.md#support-matrix) 에 대 한 지원 매트릭스를 참조 하세요. |
| 작업 | 백업 인스턴스 복원 | <li> Azure Virtual Machine <br><br> <li> Azure Database for PostgreSQL 서버 <br><br> <li> Azure VM의 SQL <br><br> <li> Azure VM의 SAP HANA <br><br> <li> Azure 파일<br/><br/> <li>Azure Blob<br/><br/> <li>Azure Managed Disks | [AZURE VM 백업](./backup-support-matrix-iaas.md) 및 [Azure Database for PostgreSQL 서버 백업](backup-azure-database-postgresql.md#support-matrix) 에 대 한 지원 매트릭스를 참조 하세요. |
| 작업 | 자격 증명 모음 만들기 | <li> Azure Virtual Machine <br><br> <li> Azure Database for PostgreSQL 서버 <br><br> <li> Azure VM의 SQL <br><br> <li> Azure VM의 SAP HANA <br><br> <li> Azure 파일<br/><br/> <li>Azure Blob<br/><br/> <li>Azure Managed Disks | [Recovery Services 자격 증명 모음](./backup-support-matrix.md#vault-support) 에 대 한 지원 매트릭스를 참조 하세요. |
| 작업 | 백업 정책 만들기 | <li> Azure Virtual Machine <br><br> <li> Azure Database for PostgreSQL 서버 <br><br> <li> Azure VM의 SQL <br><br> <li> Azure VM의 SAP HANA <br><br> <li> Azure 파일<br/><br/> <li>Azure Blob<br/><br/> <li>Azure Managed Disks | [Recovery Services 자격 증명 모음](./backup-support-matrix.md#vault-support) 에 대 한 지원 매트릭스를 참조 하세요. |
| 작업 | 백업 인스턴스에 대해 주문형 백업 실행 | <li> Azure Virtual Machine <br><br> <li> Azure Database for PostgreSQL 서버 <br><br> <li> Azure VM의 SQL <br><br> <li> Azure VM의 SAP HANA <br><br> <li> Azure 파일<br/><br/> <li>Azure Blob<br/><br/> <li>Azure Managed Disks | [AZURE VM 백업](./backup-support-matrix-iaas.md) 및 [Azure Database for PostgreSQL 서버 백업](backup-azure-database-postgresql.md#support-matrix) 에 대 한 지원 매트릭스를 참조 하세요. |
| 작업 | 백업 인스턴스 백업 중지 | <li> Azure Virtual Machine <br><br> <li> Azure Database for PostgreSQL 서버 <br><br> <li> Azure VM의 SQL <br><br> <li> Azure VM의 SAP HANA <br><br> <li> Azure 파일<br/><br/> <li>Azure Blob<br/><br/> <li>Azure Managed Disks | [AZURE VM 백업](./backup-support-matrix-iaas.md) 및 [Azure Database for PostgreSQL 서버 백업](backup-azure-database-postgresql.md#support-matrix) 에 대 한 지원 매트릭스를 참조 하세요. |
| 자세한 정보 | 백업 보고서 보기 | <li> Azure Virtual Machine <br><br> <li> Azure 가상 컴퓨터의 SQL <br><br> <li> Azure 가상 컴퓨터의 SAP HANA <br><br> <li> Azure 파일 <br><br> <li> System Center Data Protection Manager <br><br> <li> MARS (Azure Backup 에이전트) <br><br> <li> Azure Backup 서버(MABS) | [백업 보고서에 대해 지원 되는 시나리오](./configure-reports.md#supported-scenarios) 참조 |
| 거버넌스 | ' 백업 ' 범주에서 기본 제공 및 사용자 지정 Azure 정책을 보고 할당 합니다. | 해당 없음 | 해당 없음 |
| 거버넌스 | 백업에 대해 구성 되지 않은 데이터 원본 보기 | <li> Azure Virtual Machine <br><br> <li> Azure Database for PostgreSQL 서버 | 해당 없음 |

## <a name="unsupported-scenarios"></a>지원되지 않는 시나리오

| **범주** | **시나리오**  |
|--------------|---------------|
| 모니터링 | 대규모로 경고 보기 |
| 작업 | 대규모로 자격 증명 모음 설정 구성 |
| 작업 | 백업 센터에서 지역 간 복원 작업 실행 |

## <a name="next-steps"></a>다음 단계

* [Azure Backup에 대 한 지원 매트릭스를 검토 합니다.](./backup-support-matrix.md)
* [Azure VM 백업에 대 한 지원 매트릭스를 검토 합니다.](./backup-support-matrix-iaas.md)
* [Azure Database for PostgreSQL 서버 백업에 대 한 지원 매트릭스를 검토 합니다.](backup-azure-database-postgresql.md#support-matrix)