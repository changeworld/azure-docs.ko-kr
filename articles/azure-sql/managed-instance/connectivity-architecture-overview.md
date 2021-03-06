---
title: 연결 아키텍처
titleSuffix: Azure SQL Managed Instance
description: 구성 요소가 관리 되는 인스턴스로 트래픽을 전달 하는 방법 뿐만 아니라 Azure SQL Managed Instance 통신 및 연결 아키텍처에 대해 알아봅니다.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: fasttrack-edit
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova
ms.date: 10/22/2020
ms.openlocfilehash: 58563629b30e7be764732a9810162e1a0b1931e6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98725839"
---
# <a name="connectivity-architecture-for-azure-sql-managed-instance"></a>Azure SQL Managed Instance의 연결 아키텍처
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

이 문서에서는 Azure SQL Managed Instance의 통신에 대해 설명 합니다. 또한 연결 아키텍처 및 구성 요소가 관리 되는 인스턴스에 트래픽을 전달 하는 방법을 설명 합니다.  

SQL Managed Instance는 Azure 가상 네트워크 및 관리 되는 인스턴스에 전용으로 적용 되는 서브넷 내에 배치 됩니다. 이 배포는 다음을 제공 합니다.

- 보안 개인 IP 주소입니다.
- 온-프레미스 네트워크를 SQL Managed Instance에 연결할 수 있습니다.
- SQL Managed Instance를 연결 된 서버 또는 다른 온-프레미스 데이터 저장소에 연결할 수 있습니다.
- SQL Managed Instance를 Azure 리소스에 연결 하는 기능입니다.

## <a name="communication-overview"></a>통신 개요

다음 다이어그램에서는 SQL Managed Instance에 연결 하는 엔터티를 보여 줍니다. 또한 관리 되는 인스턴스와 통신 해야 하는 리소스도 보여 줍니다. 다이어그램 하단의 통신 프로세스는 SQL Managed Instance에 데이터 원본으로 연결 하는 고객 응용 프로그램 및 도구를 나타냅니다.  

![연결 아키텍처의 엔터티](./media/connectivity-architecture-overview/connectivityarch001.png)

SQL Managed Instance는 PaaS (platform as a service) 제품입니다. Azure는 자동화 된 에이전트 (관리, 배포 및 유지 관리)를 사용 하 여 원격 분석 데이터 스트림에 기반 하 여이 서비스를 관리 합니다. Azure는 관리를 담당 하기 때문에 고객이 RDP (원격 데스크톱 프로토콜)를 통해 SQL Managed Instance 가상 클러스터 컴퓨터에 액세스할 수 없습니다.

최종 사용자 또는 응용 프로그램에 의해 시작 되는 일부 작업에는 플랫폼과 상호 작용 하기 위해 SQL Managed Instance 필요할 수 있습니다. 한 가지 사례는 SQL Managed Instance 데이터베이스를 만드는 것입니다. 이 리소스는 Azure Portal, PowerShell Azure CLI 및 REST API를 통해 노출 됩니다.

SQL Managed Instance는 백업용 Azure Storage, 원격 Azure Active Directory 분석에 대 한 Azure Event Hubs, 인증을 위한 Azure, TDE (Azure Key Vault 투명한 데이터 암호화) 및 보안 및 지원 가능성 기능을 제공 하는 몇 가지 Azure platform 서비스에 따라 달라 집니다. SQL Managed Instance은 이러한 서비스에 연결 합니다.

모든 통신은 인증서를 사용하여 암호화되고 서명됩니다. 통신 당사자를 신뢰할 수 있는지 확인 하기 위해 SQL Managed Instance는 인증서 해지 목록을 통해 이러한 인증서를 지속적으로 확인 합니다. 인증서가 해지 되 면 SQL Managed Instance는 데이터를 보호 하기 위해 연결을 닫습니다.

## <a name="high-level-connectivity-architecture"></a>고급 연결 아키텍처

개략적인 수준에서 SQL Managed Instance은 서비스 구성 요소 집합입니다. 이러한 구성 요소는 고객의 가상 네트워크 서브넷 내에서 실행 되는 격리 된 전용 가상 머신의 전용 집합에서 호스팅됩니다. 이러한 컴퓨터는 가상 클러스터를 형성 합니다.

가상 클러스터는 여러 관리 되는 인스턴스를 호스트할 수 있습니다. 필요한 경우 고객이 서브넷에서 프로 비전 된 인스턴스 수를 변경할 때 클러스터가 자동으로 확장 되거나 축소 됩니다.

고객 응용 프로그램은 SQL Managed Instance에 연결 하 고 가상 네트워크, 피어 링 가상 네트워크 또는 VPN 또는 Azure Express 경로를 통해 연결 된 네트워크 내에서 데이터베이스를 쿼리 및 업데이트할 수 있습니다. 이 네트워크는 끝점과 개인 IP 주소를 사용 해야 합니다.  

![연결 아키텍처 다이어그램](./media/connectivity-architecture-overview/connectivityarch002.png)

Azure 관리 및 배포 서비스는 가상 네트워크 외부에서 실행 됩니다. SQL Managed Instance 및 Azure 서비스는 공용 IP 주소가 있는 끝점을 통해 연결 됩니다. SQL Managed Instance 아웃 바운드 연결을 만들 때 받는 쪽 NAT (네트워크 주소 변환)를 사용 하면이 공용 IP 주소에서 오는 것 처럼 연결이 보입니다.

관리 트래픽은 고객의 가상 네트워크를 통해 흐릅니다. 즉, 가상 네트워크 인프라의 요소는 인스턴스를 장애 조치 (failover) 하 고 사용할 수 없게 만들어서 관리 트래픽을 손상 시킬 수 있습니다.

> [!IMPORTANT]
> Azure는 사용자 환경 및 서비스 가용성을 개선 하기 위해 Azure 가상 네트워크 인프라 요소에 네트워크 의도 정책을 적용 합니다. 이 정책은 SQL Managed Instance 작동 방식에 영향을 줄 수 있습니다. 이 플랫폼 메커니즘은 네트워킹 요구 사항을 투명 하 게 사용자에 게 전달 합니다. 정책의 주요 목표는 네트워크 구성 오류를 방지 하 고 정상적인 SQL Managed Instance 작업을 보장 하는 것입니다. 관리 되는 인스턴스를 삭제 하는 경우에도 네트워크 의도 정책이 제거 됩니다.

## <a name="virtual-cluster-connectivity-architecture"></a>가상 클러스터 연결 아키텍처

SQL Managed Instance에 대 한 연결 아키텍처에 대해 좀 더 자세히 살펴보겠습니다. 다음 다이어그램은 가상 클러스터의 개념적 레이아웃을 보여 줍니다.

![가상 클러스터의 연결 아키텍처](./media/connectivity-architecture-overview/connectivityarch003.png)

클라이언트는 형식의 호스트 이름을 사용 하 여 SQL Managed Instance에 연결 `<mi_name>.<dns_zone>.database.windows.net` 합니다. 이 호스트 이름은 공용 DNS (도메인 이름 시스템) 영역에 등록 되어 있지만 공개적으로 확인할 수 있는 개인 IP 주소로 확인 됩니다. 는 `zone-id` 클러스터를 만들 때 자동으로 생성 됩니다. 새로 만든 클러스터가 보조 관리 되는 인스턴스를 호스트 하는 경우 주 클러스터와 해당 영역 ID를 공유 합니다. 자세한 내용은 [자동 장애 조치 그룹을 사용 하 여 여러 데이터베이스의 투명 한 장애 조치 (failover)를](../database/auto-failover-group-overview.md#enabling-geo-replication-between-managed-instances-and-their-vnets)사용 하도록 설정을 참조 하세요.

이 개인 IP 주소는 SQL Managed Instance에 대 한 내부 부하 분산 장치에 속합니다. 부하 분산 장치는 트래픽을 SQL Managed Instance 게이트웨이로 보냅니다. 여러 관리 되는 인스턴스가 동일한 클러스터 내에서 실행 될 수 있기 때문에 게이트웨이는 SQL Managed Instance 호스트 이름을 사용 하 여 트래픽을 올바른 SQL 엔진 서비스로 리디렉션합니다.

관리 및 배포 서비스는 외부 부하 분산 장치에 매핑되는 [관리 끝점](#management-endpoint) 을 사용 하 여 SQL Managed Instance에 연결 합니다. SQL Managed Instance 사용 하는 관리 구성 요소만 사용 하는 미리 정의 된 포트 집합에서 수신 된 경우에만 트래픽이 노드로 라우팅됩니다. 노드의 기본 제공 방화벽은 Microsoft IP 범위의 트래픽만 허용 하도록 설정 됩니다. 인증서는 관리 구성 요소와 관리 평면 간의 모든 통신을 상호 인증 합니다.

## <a name="management-endpoint"></a>관리 엔드포인트

Azure는 관리 끝점을 사용 하 여 SQL Managed Instance를 관리 합니다. 이 끝점은 인스턴스의 가상 클러스터 내부에 있습니다. 관리 끝점은 네트워크 수준에서 기본 제공 방화벽으로 보호 됩니다. 응용 프로그램 수준에서 상호 인증서 확인으로 보호 됩니다. 끝점의 IP 주소를 찾으려면 [관리 끝점의 ip 주소 확인](management-endpoint-find-ip-address.md)을 참조 하세요.

백업 및 감사 로그와 마찬가지로 SQL Managed Instance 내에서 연결이 시작 되 면 관리 끝점의 공용 IP 주소에서 시작 하는 것으로 트래픽이 표시 됩니다. SQL Managed Instance에 대 한 IP 주소만 허용 하도록 방화벽 규칙을 설정 하 여 SQL Managed Instance에서 공용 서비스에 대 한 액세스를 제한할 수 있습니다. 자세한 내용은 [SQL Managed Instance 기본 제공 방화벽 확인](management-endpoint-verify-built-in-firewall.md)을 참조 하세요.

> [!NOTE]
> SQL Managed Instance 지역 내에 있는 Azure 서비스로 이동 하는 트래픽은 최적화 되며 해당 이유는 관리 끝점에 대 한 공용 IP 주소에는 포함 되지 않습니다. 이러한 이유로, 일반적으로 저장소에 대해 가장 일반적으로 사용 되는 IP 기반 방화벽 규칙을 사용 해야 하는 경우 서비스는 SQL Managed Instance와 다른 지역에 있어야 합니다.

## <a name="service-aided-subnet-configuration"></a>서비스 지원 서브넷 구성

고객 보안 및 관리 효율성 요구 사항을 해결 하기 위해 SQL Managed Instance은 수동에서 서비스 간 서브넷 구성으로 전환 됩니다.

서비스 관련 서브넷 구성을 사용 하는 경우 사용자는 TDS (데이터에 대 한 모든 권한) 트래픽을 포함 하는 반면 SQL Managed Instance는 SLA를 달성 하기 위해 지속적으로 관리 트래픽의 흐름을 보장 합니다.

서비스-자동 네트워크 구성 관리를 제공 하 고 서비스 끝점을 사용 하도록 설정 하기 위해 가상 네트워크 [서브넷 위임](../../virtual-network/subnet-delegation-overview.md) 기능을 기반으로 구성 됩니다. 

서비스 끝점은 백업 및 감사 로그를 보관 하는 저장소 계정에서 가상 네트워크 방화벽 규칙을 구성 하는 데 사용할 수 있습니다. 서비스 끝점을 사용 하는 경우에도 고객은 서비스 끝점 보다 보안을 강화 하는 [개인 링크](../../private-link/private-link-overview.md) 를 사용 하는 것이 좋습니다.

> [!IMPORTANT]
> 제어 평면 구성 specificities 때문에 서비스 사용 서브넷 구성은 국가별 클라우드의 서비스 끝점을 사용 하도록 설정 하지 않습니다. 

### <a name="network-requirements"></a>네트워크 요구 사항

가상 네트워크 내의 전용 서브넷에 SQL Managed Instance를 배포 합니다. 서브넷에는 다음과 같은 특징이 있어야 합니다.

- **전용 서브넷:** SQL Managed Instance 서브넷은 연결 된 다른 클라우드 서비스를 포함할 수 없으며 게이트웨이 서브넷이 될 수 없습니다. 서브넷은 리소스를 포함할 수 없지만 SQL Managed Instance를 포함할 수 없으며 나중에 서브넷에 다른 유형의 리소스를 추가할 수 없습니다.
- **서브넷 위임:** SQL Managed Instance 서브넷을 리소스 공급자에 위임 해야 합니다 `Microsoft.Sql/managedInstances` .
- **NSG (네트워크 보안 그룹):** NSG는 SQL Managed Instance 서브넷에 연결 해야 합니다. SQL Managed Instance가 리디렉션 연결에 대해 구성 된 경우 NSG를 사용 하 여 포트 1433 및 포트 11000-11999에서 트래픽을 필터링 하 여 SQL Managed Instance 데이터 끝점에 대 한 액세스를 제어할 수 있습니다. 서비스는 중단 된 관리 트래픽 흐름을 허용 하는 데 필요한 현재 [규칙](#mandatory-inbound-security-rules-with-service-aided-subnet-configuration) 을 자동으로 프로 비전 하 고 유지 합니다.
- **UDR (사용자 정의 경로) 테이블:** UDR 테이블은 SQL Managed Instance 서브넷과 연결 해야 합니다. 경로 테이블에 항목을 추가하여 가상 네트워크 게이트웨이 또는 NVA(가상 네트워크 어플라이언스)를 통해 온-프레미스 프라이빗 IP 범위를 대상으로 하는 트래픽을 라우팅할 수 있습니다. 서비스는 중단 없는 관리 트래픽 흐름을 허용하는 데 필요한 현재 [항목](#user-defined-routes-with-service-aided-subnet-configuration)을 자동으로 프로비닝하고 유지합니다.
- **충분 한 IP 주소:** SQL Managed Instance 서브넷의 IP 주소는 32 이상 이어야 합니다. 자세한 내용은 [SQL Managed Instance에 대 한 서브넷 크기 확인](vnet-subnet-determine-size.md)을 참조 하세요. [SQL Managed Instance에 대 한 네트워킹 요구 사항을](#network-requirements)충족 하도록 구성한 후 [기존 네트워크](vnet-existing-add-subnet.md) 에서 관리 되는 인스턴스를 배포할 수 있습니다. 그러지 않으면 [새 네트워크 및 서브넷](virtual-network-subnet-create-arm-template.md)을 만듭니다.

> [!IMPORTANT]
> 관리 되는 인스턴스를 만들 때 네트워킹 설정에 대 한 비규격 변경을 방지 하기 위해 서브넷에 네트워크 의도 정책이 적용 됩니다. 서브넷에서 마지막 인스턴스를 제거한 후에도 네트워크 의도 정책이 제거 됩니다. 아래 규칙은 정보를 제공 하기 위한 것 이며 ARM template/PowerShell/CLI를 사용 하 여 배포 하면 안 됩니다. 최신 공식 템플릿을 사용 하려는 경우 언제 든 지 [포털에서 검색할](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)수 있습니다.

### <a name="mandatory-inbound-security-rules-with-service-aided-subnet-configuration"></a>서비스 관련 서브넷 구성을 사용 하는 필수 인바운드 보안 규칙

| Name       |포트                        |프로토콜|원본           |대상|작업|
|------------|----------------------------|--------|-----------------|-----------|------|
|관리  |9000, 9003, 1438, 1440, 1452|TCP     |SqlManagement    |MI SUBNET  |허용 |
|            |9000, 9003                  |TCP     |CorpnetSaw       |MI SUBNET  |허용 |
|            |9000, 9003                  |TCP     |CorpnetPublic    |MI SUBNET  |허용 |
|mi_subnet   |모두                         |모두     |MI SUBNET        |MI SUBNET  |허용 |
|health_probe|모두                         |모두     |AzureLoadBalancer|MI SUBNET  |허용 |

### <a name="mandatory-outbound-security-rules-with-service-aided-subnet-configuration"></a>서비스 관련 서브넷 구성을 사용 하는 필수 아웃 바운드 보안 규칙

| Name       |포트          |프로토콜|원본           |대상|작업|
|------------|--------------|--------|-----------------|-----------|------|
|관리  |443, 12000    |TCP     |MI SUBNET        |AzureCloud |허용 |
|mi_subnet   |모두           |모두     |MI SUBNET        |MI SUBNET  |허용 |

### <a name="user-defined-routes-with-service-aided-subnet-configuration"></a>서비스 관련 서브넷 구성을 사용 하 여 사용자 정의 경로

|Name|주소 접두사|다음 홉|
|----|--------------|-------|
|서브넷-vnetlocal|MI SUBNET|가상 네트워크|
|mi-13-64-11-nexthop-인터넷|13.64.0.0/11|인터넷|
|mi-13-104-14-nexthop-인터넷|13.104.0.0/14|인터넷|
|mi-20-33-16-nexthop-인터넷|20.33.0.0/16|인터넷|
|mi-20-34-15-nexthop-인터넷|20.34.0.0/15|인터넷|
|mi-20-36-14-nexthop-인터넷|20.36.0.0/14|인터넷|
|mi-20-40-13-nexthop-인터넷|20.40.0.0/13|인터넷|
|mi-20-48-12-nexthop-인터넷|20.48.0.0/12|인터넷|
|mi-20-64-10-nexthop-인터넷|20.64.0.0/10|인터넷|
|mi-20-128-16-nexthop-인터넷|20.128.0.0/16|인터넷|
|mi-20-135-16-nexthop-인터넷|20.135.0.0/16|인터넷|
|mi-20-136-16-nexthop-인터넷|20.136.0.0/16|인터넷|
|mi-20-140-15-nexthop-인터넷|20.140.0.0/15|인터넷|
|mi-20-143-16-nexthop-인터넷|20.143.0.0/16|인터넷|
|mi-20-144-14-nexthop-인터넷|20.144.0.0/14|인터넷|
|mi-20-150-15-nexthop-인터넷|20.150.0.0/15|인터넷|
|mi-20-160-12-nexthop-인터넷|20.160.0.0/12|인터넷|
|mi-20-176-14-nexthop-인터넷|20.176.0.0/14|인터넷|
|mi-20-180-14-nexthop-인터넷|20.180.0.0/14|인터넷|
|mi-20-184-13-nexthop-인터넷|20.184.0.0/13|인터넷|
|mi-20-192-10-nexthop-인터넷|20.192.0.0/10|인터넷|
|mi-40-64-10-nexthop-인터넷|40.64.0.0/10|인터넷|
|mi-51-4-15-nexthop-인터넷|51.4.0.0/15|인터넷|
|mi-51-8-16-nexthop-인터넷|51.8.0.0/16|인터넷|
|mi-51-10-15-nexthop-인터넷|51.10.0.0/15|인터넷|
|mi-51-18-16-nexthop-인터넷|51.18.0.0/16|인터넷|
|mi-51-51-16-nexthop-인터넷|51.51.0.0/16|인터넷|
|mi-51-53-16-nexthop-인터넷|51.53.0.0/16|인터넷|
|mi-51-103-16-nexthop-인터넷|51.103.0.0/16|인터넷|
|mi-51-104-15-nexthop-인터넷|51.104.0.0/15|인터넷|
|mi-51-132-16-nexthop-인터넷|51.132.0.0/16|인터넷|
|mi-51-136-15-nexthop-인터넷|51.136.0.0/15|인터넷|
|mi-51-138-16-nexthop-인터넷|51.138.0.0/16|인터넷|
|mi-51-140-14-nexthop-인터넷|51.140.0.0/14|인터넷|
|mi-51-144-15-nexthop-인터넷|51.144.0.0/15|인터넷|
|mi-52-96-12-nexthop-인터넷|52.96.0.0/12|인터넷|
|mi-52-112-14-nexthop-인터넷|52.112.0.0/14|인터넷|
|mi-52-125-16-nexthop-인터넷|52.125.0.0/16|인터넷|
|mi-52-126-15-nexthop-인터넷|52.126.0.0/15|인터넷|
|mi-52-130-15-nexthop-인터넷|52.130.0.0/15|인터넷|
|mi-52-132-14-nexthop-인터넷|52.132.0.0/14|인터넷|
|mi-52-136-13-nexthop-인터넷|52.136.0.0/13|인터넷|
|mi-52-145-16-nexthop-인터넷|52.145.0.0/16|인터넷|
|mi-52-146-15-nexthop-인터넷|52.146.0.0/15|인터넷|
|mi-52-148-14-nexthop-인터넷|52.148.0.0/14|인터넷|
|mi-52-152-13-nexthop-인터넷|52.152.0.0/13|인터넷|
|mi-52-160-11-nexthop-인터넷|52.160.0.0/11|인터넷|
|mi-52-224-11-nexthop-인터넷|52.224.0.0/11|인터넷|
|mi-64-4-18-nexthop-인터넷|64.4.0.0/18|인터넷|
|mi-65-52-14-nexthop-인터넷|65.52.0.0/14|인터넷|
|mi-66-119-144-20-nexthop-인터넷|66.119.144.0/20|인터넷|
|mi-70-37-17-nexthop-인터넷|70.37.0.0/17|인터넷|
|mi-70-37-128-18-nexthop-인터넷|70.37.128.0/18|인터넷|
|mi-91-190-216-21-nexthop-인터넷|91.190.216.0/21|인터넷|
|mi-94-245-64-18-nexthop-인터넷|94.245.64.0/18|인터넷|
|mi-103-9-8-22-nexthop-인터넷|103.9.8.0/22|인터넷|
|mi-103-25-156-24-nexthop-인터넷|103.25.156.0/24|인터넷|
|mi-103-25-157-24-nexthop-인터넷|103.25.157.0/24|인터넷|
|mi-103-25-158-23-nexthop-인터넷|103.25.158.0/23|인터넷|
|mi-103-36-96-22-nexthop-인터넷|103.36.96.0/22|인터넷|
|mi-103-255-140-22-nexthop-인터넷|103.255.140.0/22|인터넷|
|mi-104-40-13-nexthop-인터넷|104.40.0.0/13|인터넷|
|mi-104-146-15-nexthop-인터넷|104.146.0.0/15|인터넷|
|mi-104-208-13-nexthop-인터넷|104.208.0.0/13|인터넷|
|mi-111-221-16-20-nexthop-인터넷|111.221.16.0/20|인터넷|
|mi-111-221-64-18-nexthop-인터넷|111.221.64.0/18|인터넷|
|mi-129-75-16-nexthop-인터넷|129.75.0.0/16|인터넷|
|mi-131-107-16-nexthop-인터넷|131.107.0.0/16|인터넷|
|mi-131-253-1-24-nexthop-인터넷|131.253.1.0/24|인터넷|
|mi-131-253-3-24-nexthop-인터넷|131.253.3.0/24|인터넷|
|mi-131-253-5-24-nexthop-인터넷|131.253.5.0/24|인터넷|
|mi-131-253-6-24-nexthop-인터넷|131.253.6.0/24|인터넷|
|mi-131-253-8-24-nexthop-인터넷|131.253.8.0/24|인터넷|
|mi-131-253-12-22-nexthop-인터넷|131.253.12.0/22|인터넷|
|mi-131-253-16-23-nexthop-인터넷|131.253.16.0/23|인터넷|
|mi-131-253-18-24-nexthop-인터넷|131.253.18.0/24|인터넷|
|mi-131-253-21-24-nexthop-인터넷|131.253.21.0/24|인터넷|
|mi-131-253-22-23-nexthop-인터넷|131.253.22.0/23|인터넷|
|mi-131-253-24-21-nexthop-인터넷|131.253.24.0/21|인터넷|
|mi-131-253-32-20-nexthop-인터넷|131.253.32.0/20|인터넷|
|mi-131-253-61-24-nexthop-인터넷|131.253.61.0/24|인터넷|
|mi-131-253-62-23-nexthop-인터넷|131.253.62.0/23|인터넷|
|mi-131-253-64-18-nexthop-인터넷|131.253.64.0/18|인터넷|
|mi-131-253-128-17-nexthop-인터넷|131.253.128.0/17|인터넷|
|mi-132-245-16-nexthop-인터넷|132.245.0.0/16|인터넷|
|mi-134-170-16-nexthop-인터넷|134.170.0.0/16|인터넷|
|mi-134-177-16-nexthop-인터넷|134.177.0.0/16|인터넷|
|mi-137-116-15-nexthop-인터넷|137.116.0.0/15|인터넷|
|mi-137-135-16-nexthop-인터넷|137.135.0.0/16|인터넷|
|mi-138-91-16-nexthop-인터넷|138.91.0.0/16|인터넷|
|mi-138-196-16-nexthop-인터넷|138.196.0.0/16|인터넷|
|mi-139-217-16-nexthop-인터넷|139.217.0.0/16|인터넷|
|mi-139-219-16-nexthop-인터넷|139.219.0.0/16|인터넷|
|mi-141-251-16-nexthop-인터넷|141.251.0.0/16|인터넷|
|mi-146-147-16-nexthop-인터넷|146.147.0.0/16|인터넷|
|mi-147-243-16-nexthop-인터넷|147.243.0.0/16|인터넷|
|mi-150-171-16-nexthop-인터넷|150.171.0.0/16|인터넷|
|mi-150-242-48-22-nexthop-인터넷|150.242.48.0/22|인터넷|
|mi-157-54-15-nexthop-인터넷|157.54.0.0/15|인터넷|
|mi-157-56-14-nexthop-인터넷|157.56.0.0/14|인터넷|
|mi-157-60-16-nexthop-인터넷|157.60.0.0/16|인터넷|
|mi-167-105-16-nexthop-인터넷|167.105.0.0/16|인터넷|
|mi-167-220-16-nexthop-인터넷|167.220.0.0/16|인터넷|
|mi-168-61-16-nexthop-인터넷|168.61.0.0/16|인터넷|
|mi-168-62-15-nexthop-인터넷|168.62.0.0/15|인터넷|
|mi-191-232-13-nexthop-인터넷|191.232.0.0/13|인터넷|
|mi-192-32-16-nexthop-인터넷|192.32.0.0/16|인터넷|
|mi-192-48-225-24-nexthop-인터넷|192.48.225.0/24|인터넷|
|mi-192-84-159-24-nexthop-인터넷|192.84.159.0/24|인터넷|
|mi-192-84-160-23-nexthop-인터넷|192.84.160.0/23|인터넷|
|mi-192-197-157-24-nexthop-인터넷|192.197.157.0/24|인터넷|
|mi-193-149-64-19-nexthop-인터넷|193.149.64.0/19|인터넷|
|mi-193-221-113-24-nexthop-인터넷|193.221.113.0/24|인터넷|
|mi-194-69-96-19-nexthop-인터넷|194.69.96.0/19|인터넷|
|mi-194-110-197-24-nexthop-인터넷|194.110.197.0/24|인터넷|
|mi-198-105-232-22-nexthop-인터넷|198.105.232.0/22|인터넷|
|mi-198-200-130-24-nexthop-인터넷|198.200.130.0/24|인터넷|
|mi-198-206-164-24-nexthop-인터넷|198.206.164.0/24|인터넷|
|mi-199-60-28-24-nexthop-인터넷|199.60.28.0/24|인터넷|
|mi-199-74-210-24-nexthop-인터넷|199.74.210.0/24|인터넷|
|mi-199-103-90-23-nexthop-인터넷|199.103.90.0/23|인터넷|
|mi-199-103-122-24-nexthop-인터넷|199.103.122.0/24|인터넷|
|mi-199-242-32-20-nexthop-인터넷|199.242.32.0/20|인터넷|
|mi-199-242-48-21-nexthop-인터넷|199.242.48.0/21|인터넷|
|mi-202-89-224-20-nexthop-인터넷|202.89.224.0/20|인터넷|
|mi-204-13-120-21-nexthop-인터넷|204.13.120.0/21|인터넷|
|mi-204-14-180-22-nexthop-인터넷|204.14.180.0/22|인터넷|
|mi-204-79-135-24-nexthop-인터넷|204.79.135.0/24|인터넷|
|mi-204-79-179-24-nexthop-인터넷|204.79.179.0/24|인터넷|
|mi-204-79-181-24-nexthop-인터넷|204.79.181.0/24|인터넷|
|mi-204-79-188-24-nexthop-인터넷|204.79.188.0/24|인터넷|
|mi-204-79-195-24-nexthop-인터넷|204.79.195.0/24|인터넷|
|mi-204-79-196-23-nexthop-인터넷|204.79.196.0/23|인터넷|
|mi-204-79-252-24-nexthop-인터넷|204.79.252.0/24|인터넷|
|mi-204-152-18-23-nexthop-인터넷|204.152.18.0/23|인터넷|
|mi-204-152-140-23-nexthop-인터넷|204.152.140.0/23|인터넷|
|mi-204-231-192-24-nexthop-인터넷|204.231.192.0/24|인터넷|
|mi-204-231-194-23-nexthop-인터넷|204.231.194.0/23|인터넷|
|mi-204-231-197-24-nexthop-인터넷|204.231.197.0/24|인터넷|
|mi-204-231-198-23-nexthop-인터넷|204.231.198.0/23|인터넷|
|mi-204-231-200-21-nexthop-인터넷|204.231.200.0/21|인터넷|
|mi-204-231-208-20-nexthop-인터넷|204.231.208.0/20|인터넷|
|mi-204-231-236-24-nexthop-인터넷|204.231.236.0/24|인터넷|
|mi-205-174-224-20-nexthop-인터넷|205.174.224.0/20|인터넷|
|mi-206-138-168-21-nexthop-인터넷|206.138.168.0/21|인터넷|
|mi-206-191-224-19-nexthop-인터넷|206.191.224.0/19|인터넷|
|mi-207-46-16-nexthop-인터넷|207.46.0.0/16|인터넷|
|mi-207-68-128-18-nexthop-인터넷|207.68.128.0/18|인터넷|
|mi-208-68-136-21-nexthop-인터넷|208.68.136.0/21|인터넷|
|mi-208-76-44-22-nexthop-인터넷|208.76.44.0/22|인터넷|
|mi-208-84-21-nexthop-인터넷|208.84.0.0/21|인터넷|
|mi-209-240-192-19-nexthop-인터넷|209.240.192.0/19|인터넷|
|mi-213-199-128-18-nexthop-인터넷|213.199.128.0/18|인터넷|
|mi-216-32-180-22-nexthop-인터넷|216.32.180.0/22|인터넷|
|mi-216-220-208-20-nexthop-인터넷|216.220.208.0/20|인터넷|
|mi-23-96-13-nexthop-인터넷|23.96.0.0/13|인터넷|
|mi-42-159-16-nexthop-인터넷|42.159.0.0/16|인터넷|
|mi-51-13-17-nexthop-인터넷|51.13.0.0/17|인터넷|
|mi-51-107-16-nexthop-인터넷|51.107.0.0/16|인터넷|
|mi-51-116-16-nexthop-인터넷|51.116.0.0/16|인터넷|
|mi-51-120-16-nexthop-인터넷|51.120.0.0/16|인터넷|
|mi-51-120-128-17-nexthop-인터넷|51.120.128.0/17|인터넷|
|mi-51-124-16-nexthop-인터넷|51.124.0.0/16|인터넷|
|mi-102-37-18-nexthop-인터넷|102.37.0.0/18|인터넷|
|mi-102-133-16-nexthop-인터넷|102.133.0.0/16|인터넷|
|mi-199-30-16-20-nexthop-인터넷|199.30.16.0/20|인터넷|
|mi-204-79-180-24-nexthop-인터넷|204.79.180.0/24|인터넷|
||||

\* MI 서브넷은 x. x. x/y 형식으로 된 서브넷의 IP 주소 범위를 참조 합니다. 이 정보는 Azure Portal의 서브넷 속성에서 찾을 수 있습니다.

\** 대상 주소가 Azure의 서비스 중 하나에 대 한 것 인 경우 Azure는 트래픽을 인터넷으로 라우팅하는 대신 Azure의 백본 네트워크를 통해 서비스에 직접 트래픽을 라우팅합니다. Azure 서비스 간 트래픽은 가상 네트워크가 있는 Azure 지역 또는 Azure 서비스 인스턴스가 배포된 Azure 지역과 관계없이 인터넷을 거치지 않습니다. 자세한 내용은 [Udr 설명서 페이지](../../virtual-network/virtual-networks-udr-overview.md)를 참조 하세요.

또한 경로 테이블에 항목을 추가 하 여 가상 네트워크 게이트웨이 또는 NVA (가상 네트워크 어플라이언스)를 통해 온-프레미스 개인 IP 범위를 대상으로 하는 트래픽을 라우팅할 수 있습니다.

가상 네트워크에 사용자 지정 DNS가 포함 된 경우 사용자 지정 DNS 서버에서 공용 DNS 레코드를 확인할 수 있어야 합니다. Azure AD 인증와 같은 추가 기능을 사용 하는 경우 추가 Fqdn을 확인 해야 할 수 있습니다. 자세한 내용은 [사용자 지정 DNS 설정](custom-dns-configure.md)을 참조 하세요.

### <a name="networking-constraints"></a>네트워킹 제약 조건

**Tls 1.2는 아웃 바운드 연결에 적용 됩니다**. Microsoft는 모든 Azure 서비스의 서비스 간 트래픽에 대해 Microsoft 2020에서 tls 1.2를 적용 했습니다. Azure SQL Managed Instance의 경우이로 인해 SQL Server에 대 한 복제 및 연결 된 서버 연결에 사용 되는 아웃 바운드 연결에 TLS 1.2이 적용 되었습니다. SQL Managed Instance에서 2016 보다 오래 된 SQL Server 버전을 사용 하는 경우 [TLS 1.2 관련 업데이트가](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server) 적용 되었는지 확인 하십시오.

다음 가상 네트워크 기능은 현재 SQL Managed Instance에서 *지원 되지 않습니다* .

- **Microsoft 피어 링**: express 경로 회로에서 [microsoft 피어 링](../../expressroute/expressroute-faqs.md#microsoft-peering) 을 사용 하도록 설정 하는 것은 가상 네트워크와 직접 또는 전이적으로 피어 링 하는 가상 Managed Instance 네트워크에서 가상 네트워크 내의 구성 요소 Managed Instance 간 트래픽 흐름에 영향을 줍니다. Microsoft 피어 링을 사용 하 여 가상 네트워크에 대 한 SQL Managed Instance 배포가 실패 하 게 됩니다.
- **글로벌 가상 네트워크 피어 링**: Azure 지역 간에 [가상 네트워크 피어 링](../../virtual-network/virtual-network-peering-overview.md) 연결은 9/22/2020 이전에 만든 서브넷에 배치 되는 SQL 관리 되는 인스턴스에 대해 작동 하지 않습니다.
- **AzurePlatformDNS**: AzurePlatformDNS [service 태그](../../virtual-network/service-tags-overview.md) 를 사용 하 여 플랫폼 DNS 확인을 차단 하면 SQL Managed Instance를 사용할 수 없게 됩니다. SQL Managed Instance는 엔진 내에서 DNS 확인을 위해 고객이 정의한 DNS를 지원 하지만 플랫폼 작업을 위한 플랫폼 DNS에는 종속성이 있습니다.
- **Nat 게이트웨이**: [Azure Virtual Network nat](../../virtual-network/nat-overview.md) 를 사용 하 여 특정 공용 IP 주소와의 아웃 바운드 연결을 제어 하면 SQL Managed Instance를 사용할 수 없게 됩니다. SQL Managed Instance 서비스는 현재 Virtual Network NAT를 사용 하 여 인바운드 및 아웃 바운드 흐름을 공존 하지 않는 기본 부하 분산 장치의 사용으로 제한 됩니다.
- **Azure Virtual Network에 대 한 IPv6**: SQL Managed Instance를 [이중 스택 IPv4/IPv6 가상 네트워크](../../virtual-network/ipv6-overview.md) 에 배포 하는 작업이 실패할 것으로 예상 됩니다. IPv6 주소 접두사를 포함 하는 NSG (네트워크 보안 그룹) 또는 경로 테이블 (UDR)을 SQL Managed Instance 서브넷에 연결 하거나, 관리 되는 인스턴스 서브넷에 이미 연결 되어 있는 NSG 또는 UDR에 IPv6 주소 접두사를 추가 하면 SQL Managed Instance를 사용할 수 없게 됩니다. 이미 IPv6 접두사가 있는 NSG 및 UDR을 사용 하는 서브넷에 대 한 SQL Managed Instance 배포가 실패할 것으로 예상 됩니다.

## <a name="next-steps"></a>다음 단계

- 개요는 [AZURE SQL Managed Instance?](sql-managed-instance-paas-overview.md)을 참조 하세요.
- SQL Managed Instance를 배포할 수 있는 [새 azure 가상 네트워크](virtual-network-subnet-create-arm-template.md) 또는 [기존 azure 가상 네트워크](vnet-existing-add-subnet.md) 를 설정 하는 방법에 대해 알아봅니다.
- SQL Managed Instance를 배포 하려는 [서브넷 크기를 계산](vnet-subnet-determine-size.md) 합니다.
- 관리 되는 인스턴스를 만드는 방법에 대해 알아봅니다.
  - [Azure Portal](instance-create-quickstart.md)합니다.
  - [PowerShell](scripts/create-configure-managed-instance-powershell.md)사용.
  - [Azure Resource Manager 템플릿을](https://azure.microsoft.com/resources/templates/101-sqlmi-new-vnet/)사용 합니다.
  - [Azure Resource Manager 템플릿을 사용 합니다 (SSMS 포함 된 JumpBox 사용)](https://azure.microsoft.com/resources/templates/201-sqlmi-new-vnet-w-jumpbox/).