---
title: Azure Cosmos DB의 고가용성
description: 이 문서에서는 Azure Cosmos DB에서 고가용성을 제공하는 방법을 설명합니다.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/05/2021
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: fd704d45aa7dc10835a205f12ce26fc01a7ea44f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104584502"
---
# <a name="how-does-azure-cosmos-db-provide-high-availability"></a>Azure Cosmos DB에서 고가용성을 제공 하는 방법
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB는 두 가지 주요 방법으로 고가용성을 제공 합니다. 먼저, Azure Cosmos DB Cosmos 계정 내에서 구성 된 지역에 걸쳐 데이터를 복제 합니다. 둘째로, Azure Cosmos DB는 지역 내에서 4 개의 데이터 복제본을 유지 관리 합니다.

Azure Cosmos DB는 전역적으로 분산 된 데이터베이스 서비스 이며, [Azure를 사용할 수 있는 모든 지역](https://azure.microsoft.com/global-infrastructure/services/?products=cosmos-db&regions=all)에서 사용할 수 있는 기본 서비스입니다. 제한 없는 수의 Azure 지역을 Azure Cosmos 계정과 연결할 수 있으며 데이터는 자동으로 투명하게 복제됩니다. 언제든지 Azure Cosmos 계정에서 지역을 추가 또는 제거할 수 있습니다. Cosmos DB는 고객이 사용할 수 있는 5개의 고유한 Azure 클라우드 환경에서 사용할 수 있습니다.

* **Azure 공용** 클라우드: 전역적으로 사용할 수 있습니다.

* **Azure 중국 21vianet** 은 중국의 가장 큰 인터넷 공급자 중 하나인 Microsoft와 21vianet 간의 고유한 파트너 관계를 통해 사용할 수 있습니다.

* **Azure 독일** 은 데이터 트러스티 모델에서 서비스를 제공 하며,이는 독일 데이터 트러스티 역할을 하는 Deutsche Telekom의 자회사 인 T-Systems 국제 GmbH의 제어를 통해 고객 데이터가 독일에 유지 되도록 합니다.

* **Azure Government**: 미국 4개 지역에서 미국 정부 기관 및 해당 파트너가 사용할 수 있습니다.

* **DoD (방어 부서)에 대 한 Azure Government** 미국의 방어 부서와 미국의 두 지역에서 사용할 수 있습니다.

다음 그림에 표시 된 것 처럼 지역 내에서 Azure Cosmos DB는 실제 파티션 내의 복제본으로 4 개의 데이터 복사본을 유지 관리 합니다.

:::image type="content" source="./media/high-availability/cosmosdb-data-redundancy.png" alt-text="물리적 분할" border="false":::

* Azure Cosmos 컨테이너 내의 데이터는 [수평 분할](partitioning-overview.md)됩니다.

* 파티션 집합은 여러 복제본 집합의 컬렉션입니다. 각 지역 내에서 모든 파티션은 모든 쓰기가 복제되는 복제본 세트로 보호되며, 대부분의 복제본에서 지속적으로 커밋됩니다. 복제본은 10 ~ 20개의 장애 도메인 간에 분산됩니다.

* 모든 지역에 걸쳐 있는 각 파티션이 복제됩니다. 각 지역에는 Azure Cosmos 컨테이너의 모든 데이터 파티션이 포함 되며, 다중 지역 쓰기를 사용 하는 경우 읽기 뿐만 아니라 쓰기를 제공할 수 있습니다.  

Azure Cosmos 계정이 *n* 개의 azure 지역에 배포 되는 경우 모든 데이터의 최소 *n* x 4 복사본이 있습니다. 2 개 이상의 지역에 Azure Cosmos 계정이 있으면 응용 프로그램의 가용성이 향상 되 고 연결 된 지역에서 짧은 대기 시간을 제공 합니다.

## <a name="slas-for-availability"></a>가용성 SLA

Azure Cosmos DB 처리량, 99 번째 백분위 수의 대기 시간, 일관성 및 고가용성을 포함 하는 포괄적인 Sla를 제공 합니다. 아래 표에서는 단일 및 다중 지역 계정에 대해 Azure Cosmos DB에서 제공 하는 고가용성을 보장 합니다. 쓰기 가용성을 높이려면 Azure Cosmos 계정에 여러 쓰기 지역이 있도록 구성 합니다.

|작업 유형  | 단일 지역 |다중 지역 (단일 지역 쓰기)|다중 지역(다중 지역 쓰기) |
|---------|---------|---------|-------|
|쓰기    | 99.99    |99.99   |99.999|
|읽기     | 99.99    |99.999  |99.999|

> [!NOTE]
> 실제로 제한 된 부실, 세션, 일관 된 접두사 및 최종 일관성 모델에 대 한 실제 쓰기 가용성이 게시 된 Sla 보다 훨씬 더 높습니다. 모든 일관성 수준에 대한 실제 읽기 가용성은 게시된 SLA보다 훨씬 높습니다.

## <a name="high-availability-with-azure-cosmos-db-in-the-event-of-regional-outages"></a>지역 가동 중단 시 Azure Cosmos DB를 통한 고가용성

드물지만 지역 가동 중단이 발생 하는 경우에 Azure Cosmos DB 데이터베이스를 항상 항상 사용할 수 있도록 합니다. 다음 세부 정보는 Azure Cosmos 계정 구성에 따라 가동 중단 중에 Azure Cosmos DB 동작을 캡처합니다.

* Azure Cosmos DB를 사용 하는 경우 쓰기 작업이 클라이언트에 승인 되기 전에 쓰기 작업을 수락 하는 지역 내의 복제본 쿼럼에 의해 데이터가 지속적으로 커밋됩니다. 자세한 내용은 [일관성 수준 및 처리량](./consistency-levels.md#consistency-levels-and-throughput) 을 참조 하세요.

* 다중 쓰기 지역으로 구성된 다중 지역 계정은 쓰기 및 읽기 모두에 대해 고가용성을 유지합니다. 지역 장애 조치 (failover)는 Azure Cosmos DB 클라이언트에서 검색 되 고 처리 됩니다. 또한 즉시 적용 되며 응용 프로그램을 변경할 필요가 없습니다.

* 단일 지역 계정은 지역 중단으로 인해 가용성이 손실될 수도 있습니다. 항상 고가용성을 보장 하기 위해 **두 개 이상의 지역** (하나 이상의 쓰기 지역)을 Azure Cosmos 계정으로 설정 하는 것이 좋습니다.

### <a name="multi-region-accounts-with-a-single-write-region-write-region-outage"></a>단일 쓰기 지역이 있는 다중 지역 계정 (쓰기 영역 중단)

* Azure Cosmos 계정에 **자동 장애 조치 (failover)** 를 구성 하는 경우 azure Cosmos 계정은 쓰기 지역 가동 중단 중에 자동으로 보조 지역을 새 주 쓰기 지역으로 승격 합니다. 사용 하도록 설정 하면 지정한 지역 우선 순위에 따라 다른 지역으로 장애 조치 (failover)가 수행 됩니다.

* 수동 장애 조치 (failover)는 트리거되지 않으며 원본 또는 대상 지역이 중단 되는 경우에는 성공 하지 않습니다. 이는 장애 조치 (failover) 절차에서 지역 간에 연결을 요구 하는 일관성 확인을 수행 하기 때문입니다.

* 이전에 영향을 받은 지역이 다시 온라인 상태가 되 면 해당 지역이 실패 했을 때 복제 되지 않은 모든 쓰기 데이터는 [충돌 피드](how-to-manage-conflicts.md#read-from-conflict-feed)를 통해 사용할 수 있게 됩니다. 응용 프로그램은 충돌 피드를 읽고, 응용 프로그램별 논리에 따라 충돌을 해결 하 고, 업데이트 된 데이터를 적절 하 게 Azure Cosmos 컨테이너에 다시 쓸 수 있습니다.

* 이전에 영향을 받는 쓰기 지역이 복구되고 나면, 자동으로 읽기 지역으로 사용할 수 있게 됩니다. 쓰기 지역으로 복구 된 지역으로 다시 전환할 수 있습니다. [PowerShell, Azure CLI 또는 Azure Portal](how-to-manage-database-account.md#manual-failover)를 사용 하 여 지역을 전환할 수 있습니다. 쓰기 지역을 전환 하 고 응용 프로그램의 가용성이 계속 유지 되기 전에는 **데이터 나 가용성 손실이 발생 하지** 않습니다.

> [!IMPORTANT]
> **자동 장애 조치 (failover)를 사용 하도록 설정** 하려면 프로덕션 워크 로드에 사용 되는 Azure Cosmos 계정을 구성 하는 것이 좋습니다. 이를 통해 Cosmos DB는 계정 데이터베이스를 자동으로 사용 가능한 지역으로 장애 조치 (failover) 할 수 있습니다. 이 구성이 없는 경우 지역 연결이 부족 하 여 수동 장애 조치 (failover)가 성공 하지 않으므로 계정에 쓰기 지역 가동 중단 시간에 대 한 쓰기 가용성이 손실 됩니다.

### <a name="multi-region-accounts-with-a-single-write-region-read-region-outage"></a>단일 쓰기 지역이 있는 다중 지역 계정 (읽기 영역 중단)

* 읽기 지역 가동 중단 중에는 세 개 이상의 읽기 지역에서 일관성 수준 또는 강력한 일관성을 사용 하는 Azure Cosmos 계정이 읽기 및 쓰기에 항상 사용 가능한 상태로 유지 됩니다.

* 3 개 지역에서 강력한 일관성을 사용 하는 Azure Cosmos 계정은 읽기 지역 가동 중단 중에 쓰기 가용성을 유지 합니다. 두 지역 및 자동 장애 조치 (failover)가 설정 된 계정의 경우 해당 지역이 실패로 표시 되 고 자동 장애 조치 (failover)가 수행 될 때까지 계정이 쓰기 수신을 중지 합니다.

* 영향을 받는 지역은 자동으로 연결이 끊어지고 오프 라인으로 표시 됩니다. [Azure Cosmos DB sdk](sql-api-sdk-dotnet.md) 는 기본 지역 목록에서 사용 가능한 다음 지역으로 읽기 호출을 리디렉션합니다.

* 기본 설정 지역 목록의 어느 지역도 사용할 수 없는 경우 호출은 현재 쓰기 지역으로 자동으로 대체됩니다.

* 읽기 지역 중단을 처리하기 위해 애플리케이션 코드를 변경할 필요가 없습니다. 영향을 받는 읽기 지역이 다시 온라인 상태가 되 면 현재 쓰기 지역과 자동으로 동기화 되 고 읽기 요청을 제공 하기 위해 다시 사용할 수 있게 됩니다.

* 후속 읽기는 애플리케이션 코드를 변경하지 않고도 복구된 지역으로 리디렉션됩니다. 이전에 실패 한 지역에 대 한 장애 조치 (failover)와 다시 가입 하기 중에는 Azure Cosmos DB에서 읽기 일관성을 유지 합니다.

* Azure 지역이 영구적으로 복구할 수 없는 때 드물게 발생 하는 이벤트 에서도, 다중 지역 Azure Cosmos 계정이 *강력한* 일관성으로 구성 된 경우 데이터가 손실 되지 않습니다. 영구 복구할 수 없는 쓰기 지역의 경우, 제한 된 부실 일관성으로 구성 된 다중 지역 Azure Cosmos 계정에서 잠재적 데이터 손실 기간은 K = 100000 업데이트 또는 T = 5 분 (가장 먼저 발생) 인 부실 창 (*k* 또는 *t*)으로 제한 됩니다. 세션, 일관 된 접두사 및 최종 일관성 수준에 대 한 잠재적인 데이터 손실 기간은 최대 15 분으로 제한 됩니다. Azure Cosmos DB의 RTO 및 RPO 대상에 대 한 자세한 내용은 [일관성 수준 및 데이터](./consistency-levels.md#rto) 지 속성을 참조 하세요.

## <a name="availability-zone-support"></a>가용성 영역 지원

지역 간 복원 력 외에도, Azure Cosmos DB는 Azure Cosmos 계정에 연결할 지역을 선택할 때 지원 되는 지역에서 **영역 중복성** 을 지원 합니다.

가용성 영역 (AZ) 지원을 사용 하면 복제본이 지정 된 지역 내의 여러 영역에 배치 되어 영역 오류에 대 한 고가용성 및 복원 력을 제공 하는 Azure Cosmos DB. 가용성 영역는 대기 시간에 대 한 변경 없이 99.995% 가용성 SLA를 제공 합니다. 단일 영역 장애가 발생 하는 경우 영역 중복성은 RPO = 0 및 RTO = 0 인 가용성에 대 한 전체 데이터 내구성을 제공 합니다. 영역 중복성은 지역에서 복제에 대 한 추가 기능입니다. 영역 중복성 만으로는 지역 복원 력을 달성할 수 없습니다.

Azure Cosmos 계정에 새 지역을 추가 하는 경우에만 영역 중복성을 구성할 수 있습니다. 기존 지역의 경우 영역을 제거한 후 영역 중복성을 사용 하 여 다시 추가 하면 영역 중복성을 사용 하도록 설정할 수 있습니다. 단일 지역 계정의 경우에는 임시로 장애 조치 (failover)를 수행할 추가 지역을 하나 추가 하 고, 영역 중복성을 사용 하도록 설정 된 원하는 지역을 제거 하 고 추가 해야 합니다.

Azure Cosmos 계정에 대 한 다중 지역 쓰기를 구성 하는 경우 추가 비용 없이 영역 중복성을 옵트인 (opt in) 할 수 있습니다. 그렇지 않으면 영역 중복성 지원에 대 한 가격 책정과 관련 된 아래 표를 참조 하세요. 가용성 영역을 사용할 수 있는 지역 목록은 [가용성 영역](../availability-zones/az-region.md)을 참조 하세요.

다음 표에는 다양 한 계정 구성의 고가용성 기능이 요약 되어 있습니다.

|KPI|AZs 없는 단일 지역|AZs를 사용 하는 단일 지역|AZs를 사용 하 여 다중 지역, 단일 지역 쓰기|AZs를 사용 하 여 다중 지역, 다중 지역 쓰기|
|---------|---------|---------|---------|---------|
|쓰기 가용성 SLA | 99.99% | 99.995% | 99.995% | 99.999% |
|가용성 SLA 읽기  | 99.99% | 99.995% | 99.995% | 99.999% |
|영역 오류-데이터 손실 | 데이터 손실 | 데이터 손실 없음 | 데이터 손실 없음 | 데이터 손실 없음 |
|영역 오류-가용성 | 가용성 손실 | 가용성 손실 없음 | 가용성 손실 없음 | 가용성 손실 없음 |
|지역 가동 중단-데이터 손실 | 데이터 손실 |  데이터 손실 | 일관성 수준에 따라 달라 집니다. 자세한 내용은 [일관성, 가용성 및 성능 절충](./consistency-levels.md) 을 참조 하세요. | 일관성 수준에 따라 달라 집니다. 자세한 내용은 [일관성, 가용성 및 성능 절충](./consistency-levels.md) 을 참조 하세요.
|지역 가동 중단-가용성 | 가용성 손실 | 가용성 손실 | 읽기 지역 오류에 대 한 가용성 손실 없음, 쓰기 지역 오류에 대 한 임시 | 가용성 손실 없음 |
|가격 (***1** _) | 해당 없음 | 프로 비전 된 r u/초 x 1.25 율 | 프로 비전 된 r u/초 x 1.25 요금 (_ *_2_* *) | 다중 지역 쓰기 율 |

***1*** 서버를 사용 하지 않는 계정의 경우에는 1.25의 계수를 사용 하 여 요청 단위를 곱합니다.

***2*** 1.25 비율은 AZ를 사용 하는 지역에만 적용 됩니다.

다음을 통해 가용성 영역을 사용 하도록 설정할 수 있습니다.

* [Azure Portal](how-to-manage-database-account.md#addremove-regions-from-your-database-account)

* [Azure PowerShell](manage-with-powershell.md#create-account)

* [Azure CLI](manage-with-cli.md#add-or-remove-regions)

* [Azure 리소스 관리자 템플릿](./manage-with-templates.md)

## <a name="building-highly-available-applications"></a>고가용성 애플리케이션 빌드

* 이러한 이벤트 중에 [Azure Cosmos sdk의 예상 동작](troubleshoot-sdk-availability.md) 을 검토 하 고이에 영향을 주는 구성입니다.

* 높은 쓰기 및 읽기 가용성을 보장 하려면 여러 쓰기 지역이 있는 두 개 이상의 지역에 걸쳐 Azure Cosmos 계정을 구성 합니다. 이 구성은 Sla에서 지원 되는 읽기 및 쓰기 둘 다에 대해 최고 가용성, 가장 낮은 대기 시간 및 최고 확장성을 제공 합니다. 자세한 내용은 [여러 쓰기 영역을 사용 하 여 Azure Cosmos 계정을 구성](tutorial-global-distribution-sql-api.md)하는 방법을 참조 하세요.

* 단일 쓰기 지역으로 구성 된 다중 지역 Azure Cosmos 계정의 경우 [Azure CLI 또는 Azure Portal를 사용 하 여 자동 장애 조치 (failover)를 사용 하도록 설정](how-to-manage-database-account.md#automatic-failover)합니다. 자동 장애 조치(failover)를 사용하도록 설정하면, Cosmos DB는 지역 재해가 있을 때마다 자동으로 사용자 계정을 장애 조치(failover)합니다.  

* Azure Cosmos 계정을 항상 사용할 수 있는 경우에도 응용 프로그램이 항상 사용 가능한 상태로 유지 되도록 올바르게 설계 되지 않았을 수 있습니다. 응용 프로그램 테스트 또는 DR (재해 복구) 드릴의 일부로 응용 프로그램의 종단 간 고가용성을 테스트 하려면 계정에 대해 자동 장애 조치 (failover)를 일시적으로 사용 하지 않도록 설정 하 고 [PowerShell, Azure CLI 또는 Azure Portal를 사용 하 여 수동 장애](how-to-manage-database-account.md#manual-failover)조치 (failover)를 호출한 다음 응용 프로그램의 장애 조치 (failover)를 모니터링 합니다. 완료 되 면 주 지역으로 장애 복구 (failback) 하 고 계정에 대 한 자동 장애 조치 (failover)를 복원할 수 있습니다.

> [!IMPORTANT]
> 원본 또는 대상 지역에서 Cosmos DB 중단 시 수동 장애 조치 (failover)를 호출 하지 마십시오. 데이터 일관성을 유지 하기 위해 지역 연결이 필요 하 고 성공 하지 않습니다.

* 전역적으로 분산 된 데이터베이스 환경 내에서는 지역 전체 중단이 발생 했을 때 일관성 수준 및 데이터 내 구성을 직접 관계가 있습니다. 비즈니스 연속성 계획을 개발할 때는 중단 이벤트가 발생한 후 애플리케이션이 완전히 복구되기까지 허용되는 최대 시간을 이해해야 합니다. 애플리케이션을 완전히 복구하는 데 필요한 시간을 RTO(복구 시간 목표)라고 합니다. 또한 중단 이벤트가 발생한 후 복구될 때 애플리케이션에서 손실을 허용할 수 있는 최근 데이터 업데이트의 최대 기간도 이해해야 합니다. 손실될 수 있는 업데이트 기간을 RPO(복구 지점 목표)라고 합니다. Azure Cosmos DB의 RPO 및 RTO를 확인하려면 [일관성 수준 및 데이터 내구성](./consistency-levels.md#rto)을 참조하세요.

## <a name="what-to-expect-during-a-region-outage"></a>지역 가동 중단 중에 발생할 수 있는 작업

단일 지역 계정의 경우 클라이언트에서 읽기 및 쓰기 가용성을 잃게 됩니다.

다중 지역 계정은 다음 표에 따라 다양 한 동작을 발생 합니다.

| 지역 쓰기 | 자동 장애 조치(automatic failover) | 필요한 항목 | 알아두어야 할 사항 |
| -- | -- | -- | -- |
| 단일 쓰기 지역 | 사용 안 함 | 읽기 지역에서 가동 중단이 발생 하는 경우 모든 클라이언트는 다른 지역으로 리디렉션됩니다. 읽기 또는 쓰기 가용성이 손실 되지 않습니다. 데이터가 손실되지 않습니다. <p/> 쓰기 지역에서 가동 중단이 발생 하는 경우 클라이언트는 쓰기 가용성 손실이 발생 합니다. 데이터 손실은 선택한 constistency 수준에 따라 달라 집니다. <p/> 가동 중단이 종료 되 면 자동으로 쓰기 가용성을 복원 Cosmos DB 합니다. | 가동 중단 중에는 읽기 트래픽을 지원 하기 위해 남은 지역에 프로 비전 된 용량이 충분 한지 확인 합니다. <p/> 장애 조치 (failover) 중에는 수동 장애 조치 (failover) *를 트리거하지 않습니다* . <p/> 중단이 발생 하면 프로 비전 된 용량을 적절 하 게 다시 조정 합니다. |
| 단일 쓰기 지역 | 사용 | 읽기 지역에서 가동 중단이 발생 하는 경우 모든 클라이언트는 다른 지역으로 리디렉션됩니다. 읽기 또는 쓰기 가용성이 손실 되지 않습니다. 데이터가 손실되지 않습니다. <p/> 쓰기 지역에서 가동 중단이 발생 하는 경우 클라이언트는 기본 설정에 따라 새 영역을 새 쓰기 지역으로 자동으로 다시 입력 Cosmos DB 하기 전까지 쓰기 가용성 손실이 발생 합니다. 데이터 손실은 선택한 constistency 수준에 따라 달라 집니다. | 가동 중단 중에는 읽기 트래픽을 지원 하기 위해 남은 지역에 프로 비전 된 용량이 충분 한지 확인 합니다. <p/> 장애 조치 (failover) 중에는 수동 장애 조치 (failover) *를 트리거하지 않습니다* . <p/> 중단이 발생 하면 충돌 하는 지역에서 복제 되지 않은 데이터를 [충돌 피드에서](how-to-manage-conflicts.md#read-from-conflict-feed)복구 하 고, 쓰기 영역을 원래 지역으로 다시 이동 하 고, 프로 비전 된 용량을 적절 하 게 다시 조정할 수 있습니다. |
| 여러 쓰기 지역 | 해당 사항 없음 | 읽기 또는 쓰기 가용성이 손실 되지 않습니다. <p/> 선택한 일관성 수준에 따라 데이터 손실이 발생 했습니다. | 가동 중단 중에 추가 트래픽을 지원 하기 위해 남은 지역에 충분 한 용량이 프로 비전 되어 있는지 확인 합니다. <p/> 중단이 발생 하면 충돌 하는 지역에서 복제 되지 않은 데이터를 [충돌 피드에서](how-to-manage-conflicts.md#read-from-conflict-feed) 복구 하 고 적절히 프로 비전 된 용량을 다시 조정할 수 있습니다. |

## <a name="next-steps"></a>다음 단계

이제 다음 문서를 읽을 수 있습니다.

* [다양한 일관성 수준의 가용성 및 성능 절충](./consistency-levels.md)

* [전역적으로 프로비전된 처리량 크기 조정](./request-units.md)

* [글로벌 배포 - 내부 살펴보기](global-dist-under-the-hood.md)

* [Azure Cosmos DB의 일관성 수준](consistency-levels.md)

* [여러 쓰기 영역으로 Cosmos 계정을 구성 하는 방법](how-to-multi-master.md)

* [다중 지역 환경의 SDK 동작](troubleshoot-sdk-availability.md)