---
title: Azure Monitor 메트릭 데이터베이스로 클래식 Windows VM 메트릭 보내기
description: Windows 가상 머신(클래식)용 Azure Monitor 데이터 저장소에 게스트 OS 메트릭 보내기
author: anirudhcavale
services: azure-monitor
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: ancav
ms.openlocfilehash: dcb2c0ceb3092bb6905ac1eb9fc58093da935f2f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102048984"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metrics-database-for-a-windows-virtual-machine-classic"></a>Windows 가상 머신에 대 한 Azure Monitor 메트릭 데이터베이스에 게스트 OS 메트릭 보내기 (클래식)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Azure Monitor [진단 확장](../agents/diagnostics-extension-overview.md)(“WAD” 또는 “진단”이라고도 함)을 사용하면 가상 머신, 클라우드 서비스 또는 Service Fabric 클러스터의 일부로 실행되는 게스트 OS(게스트 운영 체제)에서 메트릭과 로그를 수집할 수 있습니다. 이 확장은 [여러 다른 위치](../data-platform.md?toc=%2fazure%2fazure-monitor%2ftoc.json)에 원격 분석을 보낼 수 있습니다.

이 문서에서는 Windows 가상 머신 (클래식)에 대 한 게스트 OS 성능 메트릭을 Azure Monitor 메트릭 데이터베이스로 전송 하는 프로세스를 설명 합니다. 진단 버전 1.11부터 표준 플랫폼 메트릭이 이미 수집된 Azure Monitor 메트릭 저장소에 메트릭을 직접 기록할 수 있습니다. 

이 위치에 메트릭을 저장하면 플랫폼 메트릭의 경우와 동일한 작업에 액세스할 수 있습니다. 작업에는 실시간에 가까운 경고, 차트 작성, 라우팅, REST API에서 액세스 등이 포함됩니다. 과거에는 진단 확장을 Azure Monitor 데이터 저장소가 아니라 Azure Storage에 기록했습니다. 

이 문서에 설명된 프로세스는 Windows 운영 체제를 실행하는 클래식 가상 머신에서만 작동합니다.

## <a name="prerequisites"></a>필수 조건

- Azure 구독의 [서비스 관리자 또는 공동 관리자](../../cost-management-billing/manage/add-change-subscription-administrator.md)여야 합니다. 

- 구독이 [Microsoft.Insights](../../azure-resource-manager/management/resource-providers-and-types.md)에 등록되어야 합니다. 

- [Azure PowerShell](/powershell/azure) 또는 [Azure Cloud Shell](../../cloud-shell/overview.md)이 설치되어 있어야 합니다.

- VM 리소스는 [사용자 지정 메트릭을 지원하는 지역](./metrics-custom-overview.md#supported-regions)에 있어야 합니다.

## <a name="create-a-classic-virtual-machine-and-storage-account"></a>클래식 가상 머신 및 스토리지 계정 만들기

1. Azure Portal을 사용하여 클래식 VM을 만듭니다.
   ![클래식 VM 만들기](./media/collect-custom-metrics-guestos-vm-classic/create-classic-vm.png)

1. 이 VM을 만들 때 새 클래식 스토리지 계정을 만드는 옵션을 선택합니다. 나중 단계에서 이 스토리지 계정을 사용합니다.

1. Azure Portal에서 **스토리지 계정** 리소스 블레이드로 이동합니다. **키** 를 선택하고 스토리지 계정 이름과 스토리지 계정 키를 적어 둡니다. 이 정보는 이후 단계에서 필요합니다.
   ![스토리지 액세스 키](./media/collect-custom-metrics-guestos-vm-classic/storage-access-keys.png)

## <a name="create-a-service-principal"></a>서비스 주체 만들기

[서비스 사용자 만들기](../../active-directory/develop/howto-create-service-principal-portal.md)의 지침을 사용 하 여 Azure Active Directory 테 넌 트에서 서비스 주체를 만듭니다. 이 프로세스를 진행하는 동안 다음 사항에 유의하세요. 
- 이 앱에 대한 새 클라이언트 암호를 만듭니다.
- 이후 단계에서 사용하기 위해 키와 클라이언트 ID를 저장합니다.

메트릭을 내보내려는 리소스에 대한 “모니터링 메트릭 게시자” 권한을 이 앱에 부여합니다. 리소스 그룹 또는 전체 구독을 사용할 수 있습니다.  

> [!NOTE]
> 진단 확장에서는 서비스 주체를 사용하여 Azure Monitor에 대해 인증하고 클래식 VM에 대한 메트릭을 내보냅니다.

## <a name="author-diagnostics-extension-configuration"></a>진단 확장 구성 작성

1. 진단 확장 구성 파일을 준비합니다. 이 파일은 진단 확장에서 클래식 VM에 대해 수집해야 하는 로그와 성능 카운터를 지정합니다. 다음은 예제입니다.

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <DiagnosticsConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
        <WadCfg>
        <DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="applicationInsights.errors">
            <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error" />
            <Directories scheduledTransferPeriod="PT1M">
                <IISLogs containerName="wad-iis-logfiles" />
                <FailedRequestLogs containerName="wad-failedrequestlogs" />
            </Directories>
            <PerformanceCounters scheduledTransferPeriod="PT1M">
                <PerformanceCounterConfiguration counterSpecifier="\Processor(*)\% Processor Time" sampleRate="PT15S" />
                <PerformanceCounterConfiguration counterSpecifier="\Memory\Available Bytes" sampleRate="PT15S" />
                <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT15S" />
                <PerformanceCounterConfiguration counterSpecifier="\Memory\% Committed Bytes" sampleRate="PT15S" />
                <PerformanceCounterConfiguration counterSpecifier="\LogicalDisk(*)\Disk Read Bytes/sec" sampleRate="PT15S" />
            </PerformanceCounters>
            <WindowsEventLog scheduledTransferPeriod="PT1M">
                <DataSource name="Application!*[System[(Level=1 or Level=2 or Level=3)]]" />
                <DataSource name="Windows Azure!*[System[(Level=1 or Level=2 or Level=3 or Level=4)]]" />
            </WindowsEventLog>
            <CrashDumps>
                <CrashDumpConfiguration processName="WaIISHost.exe" />
                <CrashDumpConfiguration processName="WaWorkerHost.exe" />
                <CrashDumpConfiguration processName="w3wp.exe" />
            </CrashDumps>
            <Logs scheduledTransferPeriod="PT1M" scheduledTransferLogLevelFilter="Error" />
            <Metrics resourceId="/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.ClassicCompute/virtualMachines/MyClassicVM">
                <MetricAggregation scheduledTransferPeriod="PT1M" />
                <MetricAggregation scheduledTransferPeriod="PT1H" />
            </Metrics>
        </DiagnosticMonitorConfiguration>
        <SinksConfig>
        </SinksConfig>
        </WadCfg>
        <StorageAccount />
    </PublicConfig>
    <PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
        <StorageAccount name="" endpoint="" />
    </PrivateConfig>
    <IsEnabled>true</IsEnabled>
    </DiagnosticsConfiguration>
    ```
1. 진단 파일의 “SinksConfig” 섹션에서 새 Azure Monitor 싱크를 다음과 같이 정의합니다.

    ```xml
    <SinksConfig>
        <Sink name="AzMonSink">
            <AzureMonitor>
                <ResourceId>Provide the resource ID of your classic VM </ResourceId>
                <Region>The region your VM is deployed in</Region>
            </AzureMonitor>
        </Sink>
    </SinksConfig>
    ```

1. 수집할 성능 카운터 목록이 나열되는 구성 파일의 섹션에서 성능 카운터를 Azure Monitor 싱크 “AzMonSink”로 라우팅합니다.

    ```xml
    <PerformanceCounters scheduledTransferPeriod="PT1M" sinks="AzMonSink">
        <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" />
    ...
    </PerformanceCounters>
    ```

1. 프라이빗 구성에서 Azure Monitor 계정을 정의합니다. 그런 다음, 메트릭을 내보내는 데 사용할 서비스 주체 정보를 추가합니다.

    ```xml
    <PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <StorageAccount name="" endpoint="" />
        <AzureMonitorAccount>
            <ServicePrincipalMeta>
                <PrincipalId>clientId for your service principal</PrincipalId>
                <Secret>client secret of your service principal</Secret>
            </ServicePrincipalMeta>
        </AzureMonitorAccount>
    </PrivateConfig>
    ```

1. 이 파일을 로컬에 저장합니다.

## <a name="deploy-the-diagnostics-extension-to-your-cloud-service"></a>클라우드 서비스에 진단 확장 배포

1. PowerShell을 시작하고 로그인합니다.

    ```powershell
    Login-AzAccount
    ```

1. 먼저 클래식 VM에 대한 컨텍스트를 설정합니다.

    ```powershell
    $VM = Get-AzureVM -ServiceName <VM’s Service_Name> -Name <VM Name>
    ```

1. VM과 함께 생성된 클래식 스토리지 계정의 컨텍스트를 설정합니다.

    ```powershell
    $StorageContext = New-AzStorageContext -StorageAccountName <name of your storage account from earlier steps> -storageaccountkey "<storage account key from earlier steps>"
    ```

1.  다음 명령을 사용하여 진단 파일 경로를 변수에 설정합니다.

    ```powershell
    $diagconfig = “<path of the diagnostics configuration file with the Azure Monitor sink configured>”
    ```

1.  Azure Monitor 싱크가 구성된 진단 파일을 사용하여 클래식 VM에 대한 업데이트를 준비합니다.

    ```powershell
    $VM_Update = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $diagconfig -VM $VM -StorageContext $Storage_Context
    ```

1.  다음 명령을 실행하여 VM에 업데이트를 배포합니다.

    ```powershell
    Update-AzureVM -ServiceName "ClassicVMWAD7216" -Name "ClassicVMWAD" -VM $VM_Update.VM
    ```

> [!NOTE]
> 여전히 진단 확장 설치의 일부로 스토리지 계정을 제공해야 합니다. 진단 구성 파일에 지정된 모든 로그 또는 성능 카운터는 지정한 스토리지 계정에 기록됩니다.

## <a name="plot-the-metrics-in-the-azure-portal"></a>Azure Portal에서 메트릭을 도표로 구성

1.  Azure Portal로 이동합니다. 

1.  왼쪽 메뉴에서 모니터를 선택 **합니다.**

1.  **모니터** 블레이드에서 **메트릭** 을 선택합니다.

    ![메트릭 탐색](./media/collect-custom-metrics-guestos-vm-classic/navigate-metrics.png)

1. 리소스 드롭다운 메뉴에서 클래식 VM을 선택합니다.

1. 네임 스페이스 드롭다운 메뉴에서 **azure. v m. w i m**.

1. 메트릭 드롭다운 메뉴에서 **Memory\committed Bytes In Use** 를 선택 합니다.
   ![메트릭 플롯](./media/collect-custom-metrics-guestos-vm-classic/plot-metrics.png)


## <a name="next-steps"></a>다음 단계
- [사용자 지정 메트릭](./metrics-custom-overview.md)에 대해 자세히 알아보세요.