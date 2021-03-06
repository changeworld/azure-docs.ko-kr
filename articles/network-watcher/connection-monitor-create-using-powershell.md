---
title: 연결 모니터 만들기-PowerShell
titleSuffix: Azure Network Watcher
description: PowerShell을 사용 하 여 연결 모니터를 만드는 방법에 대해 알아봅니다.
services: network-watcher
documentationcenter: na
author: vinigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: vinigam
ms.openlocfilehash: 7f175d82b650871437a506ea4513f0ae28360f68
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99833034"
---
# <a name="create-a-connection-monitor-by-using-powershell"></a>PowerShell을 사용 하 여 연결 모니터 만들기

> [!IMPORTANT]
> 2021 년 7 월 1 일부 터는 기존 작업 영역에 새 테스트를 추가 하거나 네트워크 성능 모니터에서 새 작업 영역을 사용 하도록 설정할 수 없습니다. 또한 연결 모니터 (클래식)에서 새 연결 모니터를 추가할 수 없습니다. 2021 년 7 월 1 일 이전에 만든 테스트 및 연결 모니터를 계속 사용할 수 있습니다. 현재 워크 로드에 대 한 서비스 중단을 최소화 하려면 [네트워크 성능 모니터에서 테스트를 마이그레이션하거나 ](migrate-to-connection-monitor-from-network-performance-monitor.md) ,  [연결 모니터 (클래식)](migrate-to-connection-monitor-from-connection-monitor-classic.md) 에서 Azure Network Watcher의 2024 년 2 월 29 일 이전에 새 연결 모니터로 마이그레이션합니다.


Azure Network Watcher의 연결 모니터 기능을 사용 하 여 리소스 간의 통신을 모니터링 하는 방법에 대해 알아봅니다.


## <a name="before-you-begin"></a>시작하기 전에

연결 모니터를 사용 하 여 만든 연결 모니터에서 온-프레미스 컴퓨터와 Azure Vm (가상 머신)을 모두 원본으로 추가할 수 있습니다. 이러한 연결 모니터는 끝점에 대 한 연결을 모니터링할 수도 있습니다. 끝점은 Azure 또는 다른 모든 URL 또는 IP에 있을 수 있습니다.

연결 모니터에는 다음 엔터티가 포함 됩니다.

* **연결 모니터 리소스**: 지역별 Azure 리소스입니다. 다음 엔터티는 모두 연결 모니터 리소스의 속성입니다.
* **끝점**: 연결 확인에 참여 하는 원본 또는 대상입니다. 끝점의 예로는 Azure Vm, 온-프레미스 에이전트, Url 및 Ip가 있습니다.
* **테스트 구성**: 테스트에 대 한 프로토콜 관련 구성입니다. 선택한 프로토콜에 따라 포트, 임계값, 테스트 빈도 및 기타 매개 변수를 정의할 수 있습니다.
* **테스트 그룹**: 소스 끝점, 대상 끝점 및 테스트 구성이 포함 된 그룹입니다. 연결 모니터에는 두 개 이상의 테스트 그룹이 포함 될 수 있습니다.
* **테스트**: 소스 끝점, 대상 끝점 및 테스트 구성의 조합입니다. 테스트는 모니터링 데이터를 사용할 수 있는 가장 세분화 된 수준입니다. 모니터링 데이터는 실패 한 검사 비율과 RTT (왕복 시간)를 포함 합니다.

    ![테스트 그룹 및 테스트 간의 관계를 정의 하는 연결 모니터를 보여 주는 다이어그램입니다.](./media/connection-monitor-2-preview/cm-tg-2.png)

## <a name="steps-to-create-a-connection-monitor"></a>연결 모니터를 만드는 단계

PowerShell을 사용 하 여 연결 모니터를 만들려면 다음 명령을 사용 합니다.

```powershell

//Connect to your Azure account with the subscription
Connect-AzAccount
Select-AzSubscription -SubscriptionId <your-subscription>
//Select region
$nw = "NetworkWatcher_centraluseuap"
//Declare endpoints like Azure VM below. You can also give VNET,Subnet,Log Analytics workspace
$sourcevmid1 = New-AzNetworkWatcherConnectionMonitorEndpointObject -Name MyAzureVm -ResourceID /subscriptions/<your-subscription>/resourceGroups/<your resourceGroup>/providers/Microsoft.Compute/virtualMachines/<vm-name>
//Declare endpoints like URL, IPs
$bingEndpoint = New-AzNetworkWatcherConnectionMonitorEndpointObject -name Bing -Address www.bing.com # Destination URL
//Create test configuration.Choose Protocol and parametersSample configs below.

$IcmpProtocolConfiguration = New-AzNetworkWatcherConnectionMonitorProtocolConfigurationObject -IcmpProtocol
$TcpProtocolConfiguration = New-AzNetworkWatcherConnectionMonitorProtocolConfigurationObject -TcpProtocol -Port 80
$httpProtocolConfiguration = New-AzNetworkWatcherConnectionMonitorProtocolConfigurationObject -HttpProtocol -Port 443 -Method GET -RequestHeader @{Allow = "GET"} -ValidStatusCodeRange 2xx, 300-308 -PreferHTTPS
$httpTestConfiguration = New-AzNetworkWatcherConnectionMonitorTestConfigurationObject -Name http-tc -TestFrequencySec 60 -ProtocolConfiguration $httpProtocolConfiguration -SuccessThresholdChecksFailedPercent 20 -SuccessThresholdRoundTripTimeMs 30
$icmpTestConfiguration = New-AzNetworkWatcherConnectionMonitorTestConfigurationObject -Name icmp-tc -TestFrequencySec 30 -ProtocolConfiguration $icmpProtocolConfiguration -SuccessThresholdChecksFailedPercent 5 -SuccessThresholdRoundTripTimeMs 500
$tcpTestConfiguration = New-AzNetworkWatcherConnectionMonitorTestConfigurationObject -Name tcp-tc -TestFrequencySec 60 -ProtocolConfiguration $TcpProtocolConfiguration -SuccessThresholdChecksFailedPercent 20 -SuccessThresholdRoundTripTimeMs 30
//Create Test Group
$testGroup1 = New-AzNetworkWatcherConnectionMonitorTestGroupObject -Name testGroup1 -TestConfiguration $httpTestConfiguration, $tcpTestConfiguration, $icmpTestConfiguration -Source $sourcevmid1 -Destination $bingEndpoint,
$testname = "cmtest9"
//Create Connection Monitor
New-AzNetworkWatcherConnectionMonitor -NetworkWatcherName $nw -ResourceGroupName NetworkWatcherRG -Name $testname -TestGroup $testGroup1

```

## <a name="description-of-properties"></a>속성 설명

* **Connectionmonitorname**: 연결 모니터 리소스의 이름입니다.

* **SUB**: 연결 모니터를 만들려는 구독의 구독 ID입니다.

* **NW**: 연결 모니터가 생성 된 리소스 ID를 Network Watcher 합니다.

* **Location**: 연결 모니터가 만들어지는 지역입니다.

* **엔드포인트**
    * **이름**: 각 끝점에 대 한 고유한 이름입니다.
    * **리소스 id**: Azure 끝점의 경우 리소스 Id는 vm에 대 한 AZURE RESOURCE MANAGER 리소스 id를 참조 합니다. 비 Azure 끝점의 경우 리소스 ID는 비 Azure 에이전트에 연결 된 Log Analytics 작업 영역에 대 한 Azure Resource Manager 리소스 ID를 나타냅니다.
    * **주소**: 리소스 id가 지정 되지 않았거나 Log Analytics 작업 영역에 리소스 id가 있는 경우에만 적용 됩니다. 리소스 ID 없이 사용 하는 경우이는 모든 공용 끝점의 URL 또는 IP 일 수 있습니다. Log Analytics 리소스 ID와 함께 사용 하는 경우 모니터링 에이전트의 FQDN을 나타냅니다.
    * **필터**: 비 Azure 끝점의 경우 필터를 사용 하 여 연결 모니터 리소스의 Log Analytics 작업 영역에서 모니터링 에이전트를 선택 합니다. 필터를 설정 하지 않으면 Log Analytics 작업 영역에 속하는 모든 에이전트를 모니터링에 사용할 수 있습니다.
        * **유형**: **에이전트 주소로** 설정 합니다.
        * **주소**: 온-프레미스 에이전트의 FQDN으로 설정 합니다.

* **테스트 그룹**
    * **이름**: 테스트 그룹의 이름을로 합니다.
    * **소스**: 이전에 만든 끝점에서 선택 합니다. Azure 기반 소스 끝점은 Azure Network Watcher 확장을 설치 해야 합니다. Azure 기반이 아닌 소스 끝점에는 Azure Log Analytics 에이전트가 설치 되어 있어야 합니다. 원본에 대 한 에이전트를 설치 하려면 [모니터링 에이전트 설치](./connection-monitor-overview.md#install-monitoring-agents)를 참조 하세요.
    * **대상**: 앞에서 만든 끝점에서 선택 합니다. 대상으로 지정 하 여 Azure Vm 또는 끝점 (공용 IP, URL 또는 FQDN)에 대 한 연결을 모니터링할 수 있습니다. 단일 테스트 그룹에서 Azure Vm, Office 365 Url, Dynamics 365 Url 및 사용자 지정 끝점을 추가할 수 있습니다.
    * **Disable**: 테스트 그룹이 지정 하는 모든 원본 및 대상에 대해 모니터링을 사용 하지 않도록 설정 합니다.

* **테스트 구성**
    * **이름**: 테스트 구성의 이름을로 설정 합니다.
    * **TestFrequencySec**: 지정한 프로토콜 및 포트에서 원본에서 대상으로 ping을 수행 하는 빈도를 지정 합니다. 30 초, 1 분, 5 분, 15 분 또는 30 분을 선택할 수 있습니다. 원본 선택한 값을 기준으로 대상에 대 한 연결을 테스트 합니다. 예를 들어 30 초를 선택 하는 경우 소스는 30 초 동안 한 번 이상 대상에 대 한 연결을 확인 합니다.
    * **프로토콜**: TCP, ICMP, HTTP 또는 HTTPS를 선택 합니다. 프로토콜에 따라 다음과 같은 프로토콜 관련 구성을 선택할 수도 있습니다.
        * **preferHTTPS**: HTTP를 통해 HTTPS를 사용할지 여부를 지정 합니다.
        * **포트**: 선택한 대상 포트를 지정 합니다.
        * **disableTraceRoute**: 원본에서 토폴로지 및 홉 단위 RTT를 검색 하지 못하도록 합니다. 이는 TCP 또는 ICMP를 사용 하는 테스트 그룹에 적용 됩니다.
        * **메서드**: HTTP 요청 메서드 (GET 또는 POST)를 선택 합니다. 이는 HTTP를 사용 하는 테스트 구성에 적용 됩니다.
        * **경로**: URL에 추가할 경로 매개 변수를 지정 합니다.
        * **유효한 statuscode**: 적용 가능한 상태 코드를 선택 합니다. 응답 코드가 일치 하지 않으면 진단 메시지가 표시 됩니다.
        * **requestHeaders**: 대상에 전달 되는 사용자 지정 요청 헤더 문자열을 지정 합니다.
    * **성공 임계값**: 다음 네트워크 매개 변수에 대 한 임계값을 설정 합니다.
        * **Checksfailedpercent**: 원본이 지정 된 조건을 사용 하 여 대상에 대 한 연결을 확인할 때 실패할 수 있는 검사 비율을 설정 합니다. TCP 또는 ICMP 프로토콜의 경우 실패 한 검사의 백분율이 패킷 손실의 비율과 같을 수 있습니다. HTTP 프로토콜의 경우이 필드는 응답을 받지 못한 HTTP 요청 비율을 나타냅니다.
        * **roundTripTimeMs**: 테스트 구성에서 대상에 연결 하는 데 사용할 수 있는 소스 시간 (밀리초)을 설정 합니다.

## <a name="scale-limits"></a> 확장 한도

연결 모니터의 크기 제한은 다음과 같습니다.

* 지역별 구독 당 최대 연결 모니터 수: 100
* 연결당 최대 테스트 그룹 수 모니터: 20
* 연결 당 최대 원본 및 대상 모니터: 100
* 연결 모니터 당 최대 테스트 구성 수: 20

## <a name="next-steps"></a>다음 단계

* [모니터링 데이터를 분석 하 고 경고를 설정 하는 방법을](./connection-monitor-overview.md#analyze-monitoring-data-and-set-alerts)알아봅니다.
* [네트워크에서 문제를 진단 하는 방법을](./connection-monitor-overview.md#diagnose-issues-in-your-network)알아봅니다.
