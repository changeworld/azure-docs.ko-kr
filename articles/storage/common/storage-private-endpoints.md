---
title: 프라이빗 엔드포인트 사용
titleSuffix: Azure Storage
description: 가상 네트워크에서 저장소 계정에 안전 하 게 액세스 하기 위한 개인 끝점의 개요입니다.
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: normesta
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: 3fcc58f626622bcc728265e782906226859e1bf9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104600465"
---
# <a name="use-private-endpoints-for-azure-storage"></a>Azure Storage에 대 한 개인 끝점 사용

Azure Storage 계정에 대해 [개인 끝점](../../private-link/private-endpoint-overview.md) 을 사용 하 여 VNet (가상 네트워크)의 클라이언트가 [개인 링크](../../private-link/private-link-overview.md)를 통해 안전 하 게 데이터에 액세스할 수 있도록 할 수 있습니다. 개인 끝점은 저장소 계정 서비스에 대 한 VNet 주소 공간의 IP 주소를 사용 합니다. VNet 및 저장소 계정의 클라이언트 간의 네트워크 트래픽은 VNet을 통해 이동 하 여 공용 인터넷에서 노출 되는 것을 제거 하는 Microsoft 백본 네트워크의 개인 링크를 통해 이동 합니다.

저장소 계정에 대해 개인 끝점을 사용 하면 다음 작업을 수행할 수 있습니다.

- 저장소 서비스에 대 한 공용 끝점의 모든 연결을 차단 하도록 저장소 방화벽을 구성 하 여 저장소 계정을 보호 합니다.
- Vnet에서 데이터의 반출을 차단할 수 있도록 하 여 vnet (가상 네트워크)에 대 한 보안을 강화 합니다.
- 개인 피어 링을 사용 하 여 [VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md) 또는 [연결할 expressroutes](../../expressroute/expressroute-locations.md) 를 사용 하 여 VNet에 연결 하는 온-프레미스 네트워크에서 저장소 계정에 안전 하 게 연결 합니다.

## <a name="conceptual-overview"></a>개념적 개요

![Azure Storage에 대 한 개인 끝점 개요](media/storage-private-endpoints/storage-private-endpoints-overview.jpg)

개인 끝점은 VNet ( [Virtual Network](../../virtual-network/virtual-networks-overview.md) )의 Azure 서비스에 대 한 특별 한 네트워크 인터페이스입니다. 저장소 계정에 대 한 개인 끝점을 만들 때 VNet 및 저장소의 클라이언트 간에 보안 연결을 제공 합니다. 개인 끝점에는 VNet의 IP 주소 범위에서 IP 주소가 할당 됩니다. 개인 끝점과 저장소 서비스 간의 연결은 보안 개인 링크를 사용 합니다.

VNet의 응용 프로그램은 **다른 방법으로 사용 하는 것과 동일한 연결 문자열 및 권한 부여 메커니즘을 사용 하 여** 개인 끝점을 통해 저장소 서비스에 원활 하 게 연결할 수 있습니다. 개인 끝점은 REST 및 SMB를 포함 하 여 저장소 계정에서 지 원하는 모든 프로토콜과 함께 사용할 수 있습니다.

[서비스 끝점](../../virtual-network/virtual-network-service-endpoints-overview.md)을 사용 하는 서브넷에서 개인 끝점을 만들 수 있습니다. 따라서 서브넷의 클라이언트는 서비스 끝점을 사용 하 여 다른 저장소 계정에 액세스 하는 동안 개인 끝점을 사용 하 여 하나의 저장소 계정에 연결할 수 있습니다.

VNet에서 스토리지 서비스에 대한 프라이빗 엔드포인트를 만들면 스토리지 계정 소유자에게 승인을 위해 동의 요청이 전송됩니다. 개인 끝점의 생성을 요청 하는 사용자가 저장소 계정의 소유자 이기도 한 경우이 동의 요청은 자동으로 승인 됩니다.

저장소 계정 소유자는 [Azure Portal](https://portal.azure.com)의 저장소 계정에 대 한 '*개인 끝점*' 탭을 통해 동의 요청 및 개인 끝점을 관리할 수 있습니다.

> [!TIP]
> 개인 끝점을 통해서만 저장소 계정에 대 한 액세스를 제한 하려는 경우 공용 끝점을 통해 액세스를 거부 하거나 제어 하도록 저장소 방화벽을 구성 합니다.

기본적으로 공용 끝점을 통해 액세스를 거부 하도록 [저장소 방화벽을 구성](storage-network-security.md#change-the-default-network-access-rule) 하 여 VNet의 연결만 허용 하도록 저장소 계정을 보호할 수 있습니다. 저장소 방화벽은 공용 끝점을 통해서만 액세스를 제어 하므로 개인 끝점이 있는 VNet의 트래픽을 허용 하는 방화벽 규칙이 필요 하지 않습니다. 대신 개인 끝점은 서브넷에 저장소 서비스에 대 한 액세스 권한을 부여 하는 동의 흐름을 사용 합니다.

> [!NOTE]
> 저장소 계정 간에 blob을 복사 하는 경우 클라이언트에 두 계정 모두에 대 한 네트워크 액세스 권한이 있어야 합니다. 따라서 하나의 계정 (원본 또는 대상)에 대해서만 개인 링크를 사용 하도록 선택 하는 경우 클라이언트에 다른 계정에 대 한 네트워크 액세스 권한이 있는지 확인 합니다. 네트워크 액세스를 구성 하는 다른 방법에 대해 알아보려면 [Azure Storage 방화벽 및 가상 네트워크 구성](storage-network-security.md?toc=/azure/storage/blobs/toc.json)을 참조 하세요. 

<a id="private-endpoints-for-azure-storage"></a>

## <a name="creating-a-private-endpoint"></a>개인 끝점 만들기

Azure Portal을 사용 하 여 개인 끝점을 만들려면 [Azure Portal의 저장소 계정 환경에서 저장소 계정에 개인 연결](../../private-link/tutorial-private-endpoint-storage-portal.md)을 참조 하세요.

PowerShell 또는 Azure CLI를 사용 하 여 개인 끝점을 만들려면 이러한 문서 중 하나를 참조 하세요. 둘 다 Azure 웹 앱을 대상 서비스로 기능 하지만 개인 링크를 만드는 단계는 Azure Storage 계정에 대해 동일 합니다.

- [Azure CLI를 사용하여 Azure 프라이빗 엔드포인트 만들기](../../private-link/create-private-endpoint-cli.md)

- [Azure PowerShell을 사용하여 프라이빗 엔드포인트 만들기](../../private-link/create-private-endpoint-powershell.md)



개인 끝점을 만들 때 저장소 계정 및 해당 끝점을 연결 하는 저장소 서비스를 지정 해야 합니다. 

액세스 해야 하는 각 저장소 리소스 (즉, [blob](../blobs/storage-blobs-overview.md), [Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md), [파일](../files/storage-files-introduction.md), [큐](../queues/storage-queues-introduction.md), [테이블](../tables/table-storage-overview.md)또는 [정적 웹 사이트](../blobs/storage-blob-static-website.md))에 대 한 별도의 개인 끝점이 필요 합니다. 개인 끝점에서 이러한 저장소 서비스는 연결 된 저장소 계정의 **대상 하위 리소스로** 정의 됩니다. 

Data Lake Storage Gen2 저장소 리소스에 대 한 개인 끝점을 만드는 경우 Blob Storage 리소스에 대해 하나를 만들어야 합니다. Data Lake Storage Gen2 끝점을 대상으로 하는 작업이 Blob 끝점으로 리디렉션되는 것 이기 때문입니다. 두 리소스에 대해 개인 끝점을 만들어 작업을 성공적으로 완료할 수 있는지 확인 합니다.

> [!TIP]
> RA GRS 계정에 대 한 읽기 성능을 향상 시키기 위해 저장소 서비스의 보조 인스턴스에 대 한 별도의 개인 끝점을 만듭니다.
> 범용 v2 (Standard 또는 Premium) 저장소 계정을 만들어야 합니다.

지역 중복 저장소에 대해 구성 된 저장소 계정을 사용 하 여 보조 지역에 대 한 읽기 액세스의 경우 서비스의 기본 및 보조 인스턴스에 대해 별도의 개인 끝점을 사용 해야 합니다. **장애 조치 (failover)** 를 위해 보조 인스턴스의 개인 끝점을 만들 필요가 없습니다. 장애 조치 (failover) 후 개인 끝점은 새 주 인스턴스에 자동으로 연결 됩니다. 저장소 중복성 옵션에 대 한 자세한 내용은 [Azure Storage 중복성](storage-redundancy.md)을 참조 하세요.

<a id="connecting-to-private-endpoints"></a>

## <a name="connecting-to-a-private-endpoint"></a>개인 끝점에 연결

개인 끝점을 사용 하는 VNet의 클라이언트는 공용 끝점에 연결 하는 클라이언트와 동일한 저장소 계정 연결 문자열을 사용 해야 합니다. DNS 확인에 의존 하 여 개인 링크를 통해 VNet에서 저장소 계정으로 연결을 자동으로 라우팅합니다.

> [!IMPORTANT]
> 다른 방법으로는 동일한 연결 문자열을 사용 하 여 개인 끝점을 통해 저장소 계정에 연결 합니다. 하위 도메인 URL을 사용 하 여 저장소 계정에 연결 하지 마세요 `privatelink` .

기본적으로 개인 끝점에 대 한 필수 업데이트를 사용 하 여 VNet에 연결 된 [개인 DNS 영역](../../dns/private-dns-overview.md) 을 만듭니다. 그러나 사용자 고유의 DNS 서버를 사용 하는 경우 DNS 구성을 추가로 변경 해야 할 수 있습니다. 아래 [DNS 변경](#dns-changes-for-private-endpoints) 에 대 한 섹션에서는 개인 끝점에 필요한 업데이트에 대해 설명 합니다.

## <a name="dns-changes-for-private-endpoints"></a>전용 끝점에 대 한 DNS 변경

개인 끝점을 만들 때 저장소 계정에 대 한 DNS CNAME 리소스 레코드는 접두사가 있는 하위 도메인의 별칭으로 업데이트 됩니다 `privatelink` . 또한 기본적으로 개인 끝점에 대 한 DNS A 리소스 레코드를 사용 하 여 하위 도메인에 해당 하는 [개인 DNS 영역](../../dns/private-dns-overview.md)을 만듭니다 `privatelink` .

개인 끝점을 사용 하 여 VNet 외부에서 저장소 끝점 URL을 확인 하면 저장소 서비스의 공용 끝점으로 확인 됩니다. 개인 끝점을 호스트 하는 VNet에서 확인 되 면 저장소 끝점 URL은 개인 끝점의 IP 주소로 확인 됩니다.

위의 예에서는 개인 끝점을 호스트 하는 VNet 외부에서 확인 되는 경우 저장소 계정 ' StorageAccountA '에 대 한 DNS 리소스 레코드는 다음과 같습니다.

| 이름                                                  | Type  | 값                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``StorageAccountA.blob.core.windows.net``             | CNAME | ``StorageAccountA.privatelink.blob.core.windows.net`` |
| ``StorageAccountA.privatelink.blob.core.windows.net`` | CNAME | \<storage service public endpoint\>                   |
| \<storage service public endpoint\>                   | A     | \<storage service public IP address\>                 |

앞에서 설명한 것 처럼 저장소 방화벽을 사용 하 여 공용 끝점을 통해 VNet 외부의 클라이언트에 대 한 액세스를 거부 하거나 제어할 수 있습니다.

StorageAccountA에 대 한 DNS 리소스 레코드는 개인 끝점을 호스트 하는 VNet의 클라이언트에서 확인 되는 경우 다음과 같습니다.

| 이름                                                  | Type  | 값                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``StorageAccountA.blob.core.windows.net``             | CNAME | ``StorageAccountA.privatelink.blob.core.windows.net`` |
| ``StorageAccountA.privatelink.blob.core.windows.net`` | A     | 10.1.1.5                                              |

이 접근 방식을 사용 하면 개인 끝점을 호스트 하는 VNet의 클라이언트와 VNet 외부의 클라이언트에 **동일한 연결 문자열을 사용 하** 여 저장소 계정에 액세스할 수 있습니다.

네트워크에서 사용자 지정 DNS 서버를 사용 하는 경우 클라이언트는 개인 끝점 IP 주소에 대 한 저장소 계정 끝점의 FQDN을 확인할 수 있어야 합니다. 개인 링크 하위 도메인을 VNet의 개인 DNS 영역에 위임 하도록 DNS 서버를 구성 하거나 개인 끝점 IP 주소를 사용 하 여 '*StorageAccountA.privatelink.blob.core.windows.net*'에 대 한 A 레코드를 구성 해야 합니다.

> [!TIP]
> 사용자 지정 또는 온-프레미스 DNS 서버를 사용 하는 경우 하위 도메인의 저장소 계정 이름을 `privatelink` 개인 끝점 IP 주소로 확인 하도록 DNS 서버를 구성 해야 합니다. 이렇게 하려면 하위 `privatelink` 도메인을 VNet의 개인 DNS 영역에 위임 하거나 dns 서버에서 dns 영역을 구성 하 고 Dns A 레코드를 추가 합니다.

저장소 서비스에 대 한 개인 끝점과 연결 된 끝점 대상 하위 리소스에 대해 권장 되는 DNS 영역 이름은 다음과 같습니다.

| 스토리지 서비스        | 대상 하위 리소스 | 영역 이름                            |
| :--------------------- | :------------------ | :----------------------------------- |
| Blob service           | blob                | `privatelink.blob.core.windows.net`  |
| Data Lake Storage Gen2 | fs                 | `privatelink.dfs.core.windows.net`   |
| 파일 서비스           | 파일                | `privatelink.file.core.windows.net`  |
| 큐 서비스          | queue               | `privatelink.queue.core.windows.net` |
| Table service          | 테이블               | `privatelink.table.core.windows.net` |
| 정적 웹 사이트        | web                 | `privatelink.web.core.windows.net`   |

전용 끝점을 지원 하기 위해 자체 DNS 서버를 구성 하는 방법에 대 한 자세한 내용은 다음 문서를 참조 하세요.

- [Azure 가상 네트워크의 리소스 이름 확인](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)
- [전용 끝점에 대 한 DNS 구성](../../private-link/private-endpoint-overview.md#dns-configuration)

## <a name="pricing"></a>가격 책정

가격 책정에 대한 자세한 내용은 [Azure Private Link 가격 책정](https://azure.microsoft.com/pricing/details/private-link)을 참조하세요.

## <a name="known-issues"></a>알려진 문제

Azure Storage에 대 한 개인 끝점에 대 한 다음과 같은 알려진 문제를 염두에 두어야 합니다.

### <a name="storage-access-constraints-for-clients-in-vnets-with-private-endpoints"></a>전용 끝점을 사용 하는 Vnet의 클라이언트에 대 한 저장소 액세스 제약 조건

기존 개인 끝점을 사용 하는 Vnet의 클라이언트는 개인 끝점이 있는 다른 저장소 계정에 액세스할 때 제약 조건을 사용 합니다. 예를 들어 VNet N1에 Blob 저장소에 대 한 저장소 계정 A1에 대 한 개인 끝점이 있다고 가정 합니다. 저장소 계정 A2에 Blob 저장소에 대 한 VNet N2에 개인 끝점이 있는 경우 VNet N1의 클라이언트는 개인 끝점을 사용 하 여 A2 계정에 있는 Blob storage에도 액세스 해야 합니다. 저장소 계정 A2에 Blob 저장소에 대 한 개인 끝점이 없는 경우 VNet N1의 클라이언트는 개인 끝점 없이 해당 계정의 Blob 저장소에 액세스할 수 있습니다.

이 제약 조건은 계정 A2가 개인 끝점을 만들 때 적용 되는 DNS 변경의 결과입니다.

### <a name="network-security-group-rules-for-subnets-with-private-endpoints"></a>프라이빗 엔드포인트가 있는 서브넷의 네트워크 보안 그룹 규칙

현재 개인 끝점에 대 한 nsg ( [네트워크 보안 그룹](../../virtual-network/network-security-groups-overview.md) ) 규칙 및 사용자 정의 경로를 구성할 수 없습니다. 개인 끝점을 호스트 하는 서브넷에 적용 된 NSG 규칙은 개인 끝점에 적용 되지 않습니다. 다른 끝점 (예: 네트워크 인터페이스 컨트롤러)에만 적용 됩니다. 이 문제에 대 한 제한 된 해결 방법은 원본 서브넷의 개인 끝점에 대 한 액세스 규칙을 구현 하는 것입니다. 단,이 방법에는 더 높은 관리 오버 헤드가 필요할 수 있습니다.

### <a name="copying-blobs-between-storage-accounts"></a>저장소 계정 간에 blob 복사

Azure REST API 또는 REST API를 사용 하는 도구를 사용 하는 경우에만 개인 끝점을 사용 하 여 저장소 계정 간에 blob을 복사할 수 있습니다. 이러한 도구에는 AzCopy, Storage 탐색기, Azure PowerShell, Azure CLI 및 Azure Blob Storage Sdk가 포함 됩니다. 

Blob 저장소 리소스를 대상으로 하는 전용 끝점만 지원 됩니다. Data Lake Storage Gen2 또는 파일 리소스를 대상으로 하는 개인 끝점은 아직 지원 되지 않습니다. 또한 NFS (네트워크 파일 시스템) 프로토콜을 사용 하 여 저장소 계정 간의 복사는 아직 지원 되지 않습니다. 

## <a name="next-steps"></a>다음 단계

- [Azure Storage 방화벽 및 가상 네트워크 구성](storage-network-security.md)
- [Blob Storage에 대한 보안 권장 사항](../blobs/security-recommendations.md)
