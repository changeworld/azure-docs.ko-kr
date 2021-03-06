---
title: Azure SQL 데이터 동기화 모범 사례
description: Azure SQL 데이터 동기화의 구성 및 실행에 대한 모범 사례를 알아봅니다.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/20/2018
ms.openlocfilehash: ee15bfaa1d69e2e5047e7d24986f8e4e7d5b8b31
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102180244"
---
# <a name="best-practices-for-azure-sql-data-sync"></a>Azure SQL 데이터 동기화 모범 사례 

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

이 문서에서는 Azure SQL 데이터 동기화에 대한 모범 사례를 설명합니다.

SQL 데이터 동기화에 대한 개요는 [Azure SQL 데이터 동기화를 사용하여 여러 클라우드 및 온-프레미스 데이터베이스에서 데이터 동기화](sql-data-sync-data-sql-server-sql-database.md)를 참조하세요.

> [!IMPORTANT]
> Azure SQL 데이터 동기화는 현재 Azure SQL Managed Instance를 지원 **하지** 않습니다.

## <a name="security-and-reliability"></a><a name="security-and-reliability"></a> 보안 및 안정성

### <a name="client-agent"></a>클라이언트 에이전트

-   네트워크 서비스 액세스 권한을 가진 최소 권한 사용자 계정을 사용하여 클라이언트 에이전트를 설치합니다.  
-   SQL Server 컴퓨터가 아닌 컴퓨터에 클라이언트 에이전트를 설치 합니다.  
-   온-프레미스 데이터베이스를 여러 에이전트에 등록하지 마세요.    
    -   여러 동기화 그룹에 대한 여러 테이블을 동기화하는 경우에도 마찬가지입니다.  
    -   온-프레미스 데이터베이스를 여러 클라이언트 에이전트에 등록하면 동기화 그룹 중 하나를 삭제하는 경우 문제가 발생합니다.

### <a name="database-accounts-with-least-required-privileges"></a>필요한 최소 권한이 있는 데이터베이스 계정

-   **동기화 설정의 경우**. 테이블 만들기/변경, 데이터베이스 변경, 프로시저 만들기, 스키마 선택/변경, 사용자 정의 형식 만들기.

-   **진행 중인 동기화의 경우**. 동기화를 위해 선택 된 테이블 및 동기화 메타 데이터와 추적 테이블에서 선택/삽입/업데이트/삭제 서비스에서 생성 된 저장 프로시저에 대 한 Execute 권한 사용자 정의 테이블 형식에 대 한 Execute 권한

-   **프로비전 해제의 경우**. 동기화 중 테이블 부분 변경, 동기화 메타데이터 테이블에서 선택/삭제, 동기화 추적 테이블, 저장 프로시저 및 사용자 정의 형식 제어.

Azure SQL Database는 단일 자격 증명 세트만 지원합니다. 이 제약 조건 내에서 작업을 수행하려면 다음 옵션을 고려해 봅니다.

-   자격 증명을 다른 단계로 변경합니다(예를 들어 설정에는 *credentials1*, 진행 중에는 *credentials2*).  
-   자격 증명의 권한을 변경합니다(즉, 동기화를 설정한 후 사용 권한을 변경).

### <a name="auditing"></a>감사

동기화 그룹의 데이터베이스 수준에서 감사를 사용 하도록 설정 하는 것이 좋습니다. 

## <a name="setup"></a>설정

### <a name="database-considerations-and-constraints"></a><a name="database-considerations-and-constraints"></a> 데이터베이스 고려 사항 및 제약 조건

#### <a name="database-size"></a>데이터베이스 크기

새 데이터베이스를 만들 때 최대 크기를 설정 하 여 배포 하는 데이터베이스 보다 항상 커집니다. 최대 크기를 배포된 데이터베이스보다 크게 설정하지 않으면 동기화가 실패합니다. SQL 데이터 동기화는 자동 확장을 제공하지 않지만, 데이터베이스가 생성된 후 `ALTER DATABASE` 명령을 실행하여 데이터베이스의 크기를 늘릴 수 있습니다. 데이터베이스 크기 제한을 벗어나지 않도록 합니다.

> [!IMPORTANT]
> SQL 데이터 동기화는 각 데이터베이스와 함께 추가 메타데이터를 저장합니다. 필요한 공간을 계산할 때 이 메타데이터를 고려해야 합니다. 추가 오버 헤드의 양은 테이블 너비(예: 테이블이 좁으면 더 많은 오버헤드가 필요) 및 트래픽 양에 비례합니다.

### <a name="table-considerations-and-constraints"></a><a name="table-considerations-and-constraints"></a> 테이블 고려 사항 및 제약 조건

#### <a name="selecting-tables"></a>테이블 선택

데이터베이스에 있는 모든 테이블을 동기화 그룹에 포함할 필요는 없습니다. 동기화 그룹에 포함하는 테이블은 효율성과 비용에 영향을 줍니다. 기업에서 요구하는 경우에만 테이블, 그리고 그 테이블이 종속된 테이블을 하나의 동기화 그룹에 포함하세요.

#### <a name="primary-keys"></a>기본 키

동기화 그룹의 각 테이블에는 기본 키가 있어야 합니다. SQL 데이터 동기화 기본 키가 없는 테이블을 동기화 할 수 없습니다.

SQL 데이터 동기화를 프로덕션에 사용하기 전에 초기 및 지속적인 동기화 성능을 테스트하세요.

#### <a name="empty-tables-provide-the-best-performance"></a>빈 테이블은 최상의 성능 제공

빈 테이블은 초기화 시 최상의 성능을 제공합니다. 대상 테이블이 비어 있으면 데이터 동기화에서 대량 삽입을 사용하여 데이터를 로드합니다. 그렇지 않으면 데이터 동기화에서 행 단위로 비교 및 삽입하여 충돌을 확인합니다. 하지만 성능 문제가 없는 경우 이미 데이터가 들어 있는 테이블 간에 동기화를 설정할 수 있습니다.

### <a name="provisioning-destination-databases"></a><a name="provisioning-destination-databases"></a> 대상 데이터베이스 프로비전

SQL 데이터 동기화는 기본 데이터베이스 자동 프로비전을 제공합니다.

이 섹션에서는 SQL 데이터 동기화의 프로비전 제한 사항에 대해 설명합니다.

#### <a name="autoprovisioning-limitations"></a>자동 프로비전 제한 사항

다음은 SQL 데이터 동기화의 자동 프로비전에 대한 제한 사항입니다.

-   대상 테이블에 생성된 열만 선택합니다. 동기화 그룹의 일부가 아닌 열은 대상 테이블에 프로비전되지 않습니다.
-   인덱스는 선택한 열에 대해서만 생성됩니다. 원본 테이블 인덱스에 동기화 그룹의 일부가 아닌 열이 있으면 해당 인덱스는 대상 테이블에서 프로비전되지 않습니다.  
-   XML 형식 열의 인덱스는 프로비전되지 않습니다.  
-   CHECK 제약 조건은 프로비전되지 않습니다.  
-   원본 테이블의 기존 트리거는 프로비전되지 않습니다.  
-   보기 및 저장 프로시저는 대상 데이터베이스에 생성되지 않습니다.
-   외래 키 제약 조건에 대한 UPDATE CASCADE 및 ON DELETE CASCADE 작업은 대상 테이블에서 다시 생성되지 않습니다.
-   전체 자릿수가 28 보다 큰 decimal 또는 numeric 열이 있는 경우 동기화 중에 SQL 데이터 동기화 변환 오버플로 문제가 발생할 수 있습니다. Decimal 또는 numeric 열의 전체 자릿수를 28 이하로 제한 하는 것이 좋습니다.

#### <a name="recommendations"></a>권장 사항

-   SQL 데이터 동기화 자동 프로비전 기능은 서비스를 시험해 보는 경우에만 사용합니다.  
-   프로덕션의 경우 데이터베이스 스키마를 프로비전합니다.

### <a name="where-to-locate-the-hub-database"></a><a name="locate-hub"></a> 허브 데이터베이스를 찾을 수 있는 위치

#### <a name="enterprise-to-cloud-scenario"></a>엔터프라이즈-클라우드 시나리오

대기 시간을 최소화하기 위해 허브 데이터베이스를 동기화 그룹의 데이터베이스 트래픽이 가장 집중된 곳과 가까운 곳에 배치합니다.

#### <a name="cloud-to-cloud-scenario"></a>클라우드 간 시나리오

-   동기화 그룹에 있는 모든 데이터베이스가 하나의 데이터 센터에 있는 경우 허브는 동일한 데이터 센터에 있어야 합니다. 이 구성은 대기 시간 및 데이터 센터 간의 데이터 전송 비용을 줄입니다.
-   동기화 그룹의 데이터베이스가 여러 데이터 센터에 있는 경우 허브는 데이터베이스 및 데이터베이스 트래픽이 가장 많은 데이터 센터에 있어야 합니다.

#### <a name="mixed-scenarios"></a>혼합 시나리오

앞에서 설명한 지침을 엔터프라이즈-클라우드 및 클라우드-클라우드 혼합 시나리오처럼 복잡한 동기화 그룹 구성에 적용합니다.

## <a name="sync"></a>동기화

### <a name="avoid-slow-and-costly-initial-sync"></a><a name="avoid-a-slow-and-costly-initial-synchronization"></a> 느리고 비용이 많이 드는 초기 동기화 방지

이 섹션에서는 동기화 그룹의 초기 동기화에 대해 설명합니다. 꼭 필요한 시간과 비용보다 더 많은 시간과 비용이 드는 초기 동기화를 방지하는 방법을 알아봅니다.

#### <a name="how-initial-sync-works"></a>초기 동기화의 작동 원리

동기화 그룹을 만들 때 단 하나의 데이터베이스에 있는 데이터부터 시작합니다. 여러 데이터베이스에 데이터가 있으면 SQL 데이터 동기화는 각 행을 해결해야 하는 충돌로 처리합니다. 이 충돌 해결이 초기 동기화를 느리게 만드는 원인입니다. 여러 데이터베이스에 데이터가 있으면 데이터베이스 크기에 따라 초기 동기화가 며칠부터 몇 달까지 걸릴 수 있습니다.

데이터베이스가 여러 데이터 센터에 있는 경우 각 행은 여러 데이터 센터 사이를 이동해야 합니다. 이로 인해 초기 동기화의 비용이 증가합니다.

#### <a name="recommendation"></a>권장

되도록이면 동기화 그룹의 여러 데이터베이스 중 특정 데이터베이스의 데이터부터 시작합니다.

### <a name="design-to-avoid-sync-loops"></a><a name="design-to-avoid-synchronization-loops"></a> 동기화 루프를 방지하는 디자인

동기화 루프는 동기화 그룹 내에 순환 참조가 있을 때 발생합니다. 이 시나리오에서는 하나의 데이터베이스의 변경 내용이 동기화 그룹의 데이터베이스를 통해 끊임없이 순환하면서 복제됩니다.   

동기화 루프는 성능 저하를 일으키고 상당히 많은 비용을 발생시킬 수 있으므로 피해야 합니다.

### <a name="changes-that-fail-to-propagate"></a><a name="handling-changes-that-fail-to-propagate"></a> 전파되지 않는 변경 내용

#### <a name="reasons-that-changes-fail-to-propagate"></a>변경 내용이 전파되지 않는 이유

다음 중 한 가지 이유로 변경 내용이 전파되지 않을 수 있습니다.

-   스키마/데이터 형식이 호환되지 않는 경우.
-   null이 아닌 열에 null을 삽입하는 경우.
-   외래 키 제약 조건을 위반하는 경우.

#### <a name="what-happens-when-changes-fail-to-propagate"></a>변경 내용이 전파되지 않으면 어떻게 될까요?

-   동기화 그룹이 **경고 상태** 로 표시됩니다.
-   세부 정보는 포털 UI 로그 뷰어에 표시됩니다.
-   문제를 45일 내 해결하지 않으면 데이터베이스가 만료됩니다.

> [!NOTE]
> 이러한 변경 내용은 전파되지 않습니다. 이 시나리오에서 유일한 복구 방법은 동기화 그룹을 다시 만드는 것입니다.

#### <a name="recommendation"></a>권장

포털 및 로그 인터페이스를 통해 동기화 그룹 및 데이터베이스 상태를 정기적으로 모니터링합니다.


## <a name="maintenance"></a>유지 관리

### <a name="avoid-out-of-date-databases-and-sync-groups"></a><a name="avoid-out-of-date-databases-and-sync-groups"></a> 만료된 데이터베이스 및 동기화 그룹 방지

동기화 그룹 내 동기화 그룹 또는 데이터베이스가 만료될 수 있습니다. 동기화 그룹의 상태가 **만료** 인 경우 작동이 중지됩니다. 데이터베이스의 상태가 **만료** 인 경우 데이터가 손실될 수 있습니다. 이 시나리오를 복구하기 보다는 예방하는 것이 좋습니다.

#### <a name="avoid-out-of-date-databases"></a>만료된 데이터베이스 방지

데이터베이스의 상태는 45일 이상 오프라인일 경우 **만료** 로 설정됩니다. 데이터베이스가 **만료** 상태가 되지 않게 하려면 데이터베이스 중 어떤 것도 45일 이상 오프라인이 되지 않도록 주의해야 합니다.

#### <a name="avoid-out-of-date-sync-groups"></a>만료된 동기화 그룹 방지

동기화 그룹의 상태는 동기화 그룹 내 임의의 변경 내용이 45일 이상 동안 동기화 그룹의 나머지 부분으로 전파되지 못하는 경우 **만료** 로 설정됩니다. 동기화 그룹이 **만료** 상태가 되는 것을 방지하려면 정기적으로 동기화 그룹의 기록 로그를 확인합니다. 모든 충돌을 해결하고 변경 내용이 동기화 그룹 데이터베이스 전체에 성공적으로 전파됨을 확인합니다.

동기화 그룹이 다음 이유 중 하나 때문에 변경 내용을 적용하지 않을 수 있습니다.

-   테이블 간 스키마 비호환성.
-   테이블 간 데이터 비호환성.
-   null 값을 허용하지 않는 열에 null 값이 있는 행 삽입.
-   외래 키 제약 조건을 위반하는 값이 있는 행 업데이트.

동기화 그룹이 만료되지 않게 하려면:

-   실패한 행에 포함된 값을 허용하도록 스키마를 업데이트합니다.
-   실패한 행에 들어 있는 값을 포함하도록 외래 키를 업데이트합니다.
-   대상 데이터베이스의 스키마 또는 외래 키와 호환되도록 실패한 행의 데이터 값을 업데이트합니다.

### <a name="avoid-deprovisioning-issues"></a><a name="avoid-deprovisioning-issues"></a> 프로비전 해제 문제 방지

특정 상황에서는 클라이언트 에이전트로 데이터베이스의 등록을 취소하면 동기화가 실패할 수 있습니다.

#### <a name="scenario"></a>시나리오

1. 로컬 에이전트 1과 연결 된 SQL Database 인스턴스 및 SQL Server 데이터베이스를 사용 하 여 동기화 그룹 A를 만들었습니다.
2. 동일한 온-프레미스 데이터베이스가 로컬 에이전트 2(이 에이전트는 어떠한 동기화 그룹에도 연결되지 않음)로 등록됩니다.
3. 온-프레미스 데이터베이스를 로컬 에이전트 2에서 등록 취소하면 추적/메타 테이블이 온-프레미스 데이터베이스의 동기화 그룹에서 제거됩니다.
4. “데이터베이스가 동기화에 대해 프로비전되지 않거나 동기화 구성 테이블에 대한 사용 권한이 없으므로 현재 작업을 완료할 수 없습니다.”라는 오류와 함께 동기화 그룹 A 작업에 실패합니다.

#### <a name="solution"></a>솔루션

이 시나리오를 방지하려면 데이터베이스를 여러 에이전트에 등록하지 마세요.

이 시나리오에서 복구하려면:

1. 데이터베이스가 속한 각 동기화 그룹에서 데이터베이스를 제거합니다.  
2. 데이터베이스를 제거한 각 동기화 그룹에 다시 추가합니다.  
3. 영향을 받는 각 동기화 그룹을 배포합니다(이 작업은 데이터베이스를 프로비전).  

### <a name="modifying-a-sync-group"></a><a name="modifying-your-sync-group"></a> 동기화 그룹 수정

데이터베이스를 동기화 그룹에서 제거한 다음, 변경 내용 중 하나가 먼저 배포되기 전에는 동기화 그룹을 편집하지 마세요.

대신, 먼저 데이터베이스를 동기화 그룹에서 제거합니다. 그런 다음 변경 내용을 배포하고 프로비전 해제가 완료될 때까지 기다립니다. 프로비전 해제가 완료되면 동기화 그룹을 편집하고 변경 내용을 배포할 수 있습니다.

데이터베이스를 제거한 후 변경 내용 중 하나가 먼저 배포되기 전에 동기화 그룹을 편집하려고 하면 해당 작업 또는 다른 작업이 실패합니다. 포털 인터페이스가 불일치 상태가 될 수 있습니다. 이 상황이 발생하면 페이지를 새로 고쳐서 올바른 상태로 복원할 수 있습니다.

### <a name="avoid-schema-refresh-timeout"></a>스키마 새로 고침 제한 시간 방지

동기화 할 복잡 한 스키마가 있는 경우 동기화 메타 데이터 데이터베이스에 더 낮은 SKU (예: 기본)가 있는 경우 스키마를 새로 고치는 동안 "작업 시간 제한"이 발생할 수 있습니다. 

#### <a name="solution"></a>솔루션

이 문제를 완화 하려면 S3와 같이 더 높은 SKU를 갖도록 동기화 메타 데이터 데이터베이스를 확장 하세요. 

## <a name="next-steps"></a>다음 단계
SQL 데이터 동기화에 대한 자세한 내용은 다음 항목을 참조하세요.

-   개요 - [Azure SQL 데이터 동기화를 사용하여 여러 클라우드 및 온-프레미스 데이터베이스에서 데이터 동기화](sql-data-sync-data-sql-server-sql-database.md)
-   SQL 데이터 동기화 설정
    - 포털에서- [자습서: Azure SQL Database와 SQL Server 간에 데이터를 동기화 SQL 데이터 동기화 설정](sql-data-sync-sql-server-configure.md)
    - PowerShell 사용
        -  [PowerShell을 사용 하 여 Azure SQL Database에서 여러 데이터베이스 간 동기화](scripts/sql-data-sync-sync-data-between-sql-databases.md)
        -  [PowerShell을 사용 하 여 SQL Database 데이터베이스와 SQL Server 인스턴스의 데이터베이스 간 동기화](scripts/sql-data-sync-sync-data-between-azure-onprem.md)
-   데이터 동기화 에이전트 - [Azure SQL 데이터 동기화용 데이터 동기화 에이전트](sql-data-sync-agent-overview.md)
-   모니터 - [Azure Monitor 로그를 사용하여 SQL 데이터 동기화 모니터링](./monitor-tune-overview.md)
-   문제 해결 - [Azure SQL 데이터 동기화 문제 해결](sql-data-sync-troubleshoot.md)
-   동기화 스키마 업데이트
    -   Transact-SQL 사용 - [Azure SQL 데이터 동기화에서 스키마 변경 내용 복제 자동화](sql-data-sync-update-sync-schema.md)
    -   PowerShell 사용 - [PowerShell을 사용하여 기존 동기화 그룹의 동기화 스키마 업데이트](scripts/update-sync-schema-in-sync-group.md)

SQL Database에 대한 자세한 내용은 다음 항목을 참조하세요.

-   [SQL Database 개요](sql-database-paas-overview.md)
-   [데이터베이스 수명 주기 관리](/previous-versions/sql/sql-server-guides/jj907294(v=sql.110))
