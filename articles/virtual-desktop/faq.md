---
title: Windows 가상 데스크톱 FAQ-Azure
description: Windows 가상 데스크톱에 대 한 질문과 대답 및 모범 사례입니다.
author: Heidilohr
ms.topic: conceptual
ms.date: 03/09/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: ffea2d84f1a5149670976beef3b9af847ae31a35
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104582139"
---
# <a name="windows-virtual-desktop-faq"></a>Windows Virtual Desktop FAQ

이 문서에서는 자주 묻는 질문과 대답을 소개 하 고 Windows 가상 데스크톱에 대 한 모범 사례에 대해 설명 합니다.

## <a name="what-are-the-minimum-admin-permissions-i-need-to-manage-objects"></a>개체를 관리 하는 데 필요한 최소 관리자 권한은 무엇입니까?

호스트 풀 및 기타 개체를 만들려면 작업 중인 구독 또는 리소스 그룹에 대 한 참가자 역할을 할당 해야 합니다.

앱 그룹에 사용자 액세스 관리자 역할을 할당 하 여 사용자 또는 사용자 그룹에 앱 그룹을 게시 해야 합니다.

사용자에 게 메시지 보내기, 사용자 로그 아웃 등의 사용자 세션만 관리 하도록 제한 하려면 사용자 지정 역할을 만들 수 있습니다. 예를 들면 다음과 같습니다.

```powershell
"actions": [
"Microsoft.Resources/deployments/operations/read",
"Microsoft.Resources/tags/read",
"Microsoft.Authorization/roleAssignments/read",
"Microsoft.DesktopVirtualization/hostpools/sessionhosts/usersessions/*",
"Microsoft.DesktopVirtualization/hostpools/sessionhosts/read",
"Microsoft.DesktopVirtualization/hostpools/sessionhosts/write"
],
"notActions": [],
"dataActions": [],
"notDataActions": []
}
```

## <a name="does-windows-virtual-desktop-support-split-azure-active-directory-models"></a>Windows 가상 데스크톱은 분할 Azure Active Directory 모델을 지원 하나요?

사용자가 앱 그룹에 할당 되 면 서비스는 간단한 Azure 역할 할당을 수행 합니다. 따라서 사용자의 AD (Azure Active Directory)와 앱 그룹의 Azure AD는 동일한 위치에 있어야 합니다. 호스트 풀, 앱 그룹 및 작업 영역과 같은 모든 서비스 개체도 사용자와 동일한 Azure AD에 있어야 합니다.

Active Directory를 동일한 VNET (가상 네트워크)에서 사용자의 Azure AD와 동기화 하는 경우 다른 Azure AD에서 Vm (가상 머신)을 만들 수 있습니다.

## <a name="what-are-location-restrictions"></a>위치 제한은 무엇 인가요?

모든 서비스 리소스에는 연결 된 위치가 있습니다. 호스트 풀의 위치는 호스트 풀의 서비스 메타 데이터가 저장 되는 지리를 결정 합니다. 호스트 풀이 없으면 앱 그룹을 사용할 수 없습니다. RemoteApp 앱 그룹에 앱을 추가 하는 경우 시작 메뉴 앱을 결정 하는 세션 호스트도 필요 합니다. 모든 앱 그룹 작업의 경우 호스트 풀에 관련 된 데이터 액세스 권한도 필요 합니다. 여러 위치 간에 데이터가 전송 되지 않도록 하려면 앱 그룹의 위치가 호스트 풀의 위치와 동일 해야 합니다.

또한 작업 영역은 해당 앱 그룹과 동일한 위치에 있어야 합니다. 작업 영역이 업데이트 될 때마다 관련 앱 그룹이 함께 업데이트 됩니다. 앱 그룹과 마찬가지로 서비스에서는 모든 작업 영역이 동일한 위치에 생성 된 앱 그룹과 연결 되어야 합니다.

## <a name="how-do-you-expand-an-objects-properties-in-powershell"></a>PowerShell에서 개체의 속성을 확장 하는 방법

PowerShell cmdlet을 실행 하면 리소스 이름과 위치만 표시 됩니다.

예를 들면 다음과 같습니다.

```powershell
Get-AzWvdHostPool -Name 0224hp -ResourceGroupName 0224rg

Location Name   Type
-------- ----   ----
westus   0224hp Microsoft.DesktopVirtualization/hostpools
```

리소스의 모든 속성을 보려면 `format-list` `fl` cmdlet의 끝에 또는를 추가 합니다.

예를 들면 다음과 같습니다.

```powershell
Get-AzWvdHostPool -Name 0224hp -ResourceGroupName 0224rg |fl
```

특정 속성을 보려면 또는 뒤에 특정 속성 이름을 추가 `format-list` 합니다 `fl` .

예를 들면 다음과 같습니다.

```powershell
Get-AzWvdHostPool -Name demohp -ResourceGroupName 0414rg |fl CustomRdpProperty

CustomRdpProperty : audiocapturemode:i:0;audiomode:i:0;drivestoredirect:s:;redirectclipboard:i:1;redirectcomports:i:0;redirectprinters:i:1;redirectsmartcards:i:1;screen modeid:i:2;
```

## <a name="does-windows-virtual-desktop-support-guest-users"></a>Windows 가상 데스크톱에서 게스트 사용자를 지원 하나요?

Windows 가상 데스크톱은 Azure AD 게스트 사용자 계정을 지원 하지 않습니다. 예를 들어, 게스트 사용자 그룹은 자신의 회사에서 E3 사용자 당 E3, Windows E3/사용자 당 또는 WIN VDA 라이선스를가지고 있지만 다른 회사의 Azure AD에 있는 게스트 사용자를 Microsoft 365 가정해 보겠습니다. 다른 회사는 Azure AD에서 게스트 사용자의 사용자 개체를 관리 하 고 로컬 계정 처럼 Active Directory 합니다.

타사의 혜택을 위해 사용자 고유의 라이선스를 사용할 수 없습니다. 또한 Windows 가상 데스크톱은 현재 MSA (Microsoft 계정)를 지원 하지 않습니다.

## <a name="why-dont-i-see-the-client-ip-address-in-the-wvdconnections-table"></a>WVDConnections 테이블에 클라이언트 IP 주소가 표시 되지 않는 이유는 무엇 인가요?

현재 웹 클라이언트의 IP 주소를 수집 하는 신뢰할 수 있는 방법이 없기 때문에 테이블에 해당 값을 포함 하지 않습니다.

## <a name="how-does-windows-virtual-desktop-handle-backups"></a>Windows 가상 데스크톱에서 백업을 처리 하는 방법

Azure에는 백업 처리를 위한 여러 옵션이 있습니다. Azure backup, Site Recovery 및 스냅숏을 사용할 수 있습니다.

## <a name="does-windows-virtual-desktop-support-third-party-collaboration-apps"></a>Windows 가상 데스크톱은 타사 공동 작업 앱을 지원 하나요?

Windows 가상 데스크톱은 현재 팀에 최적화 되어 있습니다. Microsoft는 현재 확대/축소와 같은 타사 공동 작업 앱을 지원 하지 않습니다. 타사 조직은 고객에 게 호환성 지침을 제공할 책임이 있습니다. Windows 가상 데스크톱은 비즈니스용 Skype도 지원 하지 않습니다.

## <a name="can-i-change-from-pooled-to-personal-host-pools"></a>풀링된에서 개인 호스트 풀로 변경할 수 있나요?

호스트 풀을 만든 후에는 해당 유형을 변경할 수 없습니다. 그러나 호스트 풀에 등록 하는 모든 Vm을 다른 유형의 호스트 풀로 이동할 수 있습니다.

## <a name="whats-the-largest-profile-size-fslogix-can-handle"></a>FSLogix에서 처리할 수 있는 가장 큰 프로필 크기는 무엇 인가요?

FSLogix의 제한 또는 할당량은 사용자 프로필 VHD (X) 파일을 저장 하는 데 사용 되는 저장소 패브릭에 따라 다릅니다.

다음 표에서는 FSLogix 프로필에서 각 사용자를 지 원하는 데 필요한 IOPS 수를 보여 줍니다. 요구 사항은 각 프로필의 사용자, 응용 프로그램 및 작업에 따라 크게 달라질 수 있습니다.

| 리소스 | 요구 사항 |
|---|---|
| 안정적인 상태 IOPS | 10 |
| 로그인/로그 아웃 IOPS | 50 |

이 표의 예는 단일 사용자 이지만 사용자 환경의 총 사용자 수에 대 한 요구 사항을 예측 하는 데 사용할 수 있습니다. 예를 들어 100 사용자의 경우 1000 IOPS를, 로그인 및 로그 아웃 중에는 5000 IOPS를 기준으로 해야 합니다.

## <a name="is-there-a-scale-limit-for-host-pools-created-in-the-azure-portal"></a>Azure Portal에서 만든 호스트 풀에 대 한 크기 제한이 있나요?

이러한 요인은 호스트 풀의 확장 제한에 영향을 줄 수 있습니다.

- Azure 템플릿은 800 개체로 제한 됩니다. 자세히 알아보려면 [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../azure-resource-manager/management/azure-subscription-service-limits.md#template-limits)을 참조 하세요. 또한 각 VM은 6 개 개체에 대 한 정보를 만들기 때문에 템플릿을 실행할 때마다 132 Vm을 만들 수 있습니다.

- 지역 및 구독 당 만들 수 있는 코어 수에는 제한이 있습니다. 예를 들어 기업계약 구독이 있는 경우 350 코어를 만들 수 있습니다. 템플릿을 실행할 때마다 만들 수 있는 Vm 수를 결정 하려면 VM 당 기본 코어 수 또는 고유한 코어 제한으로 350을 나누어야 합니다. [Virtual Machines 한도 Azure Resource Manager](../azure-resource-manager/management/azure-subscription-service-limits.md#virtual-machines-limits---azure-resource-manager)에서 자세히 알아보세요.

- VM 접두사 이름과 Vm 수가 15 자 미만입니다. 자세히 알아보려면 [Azure 리소스에 대 한 명명 규칙 및 제한 사항](../azure-resource-manager/management/resource-name-rules.md#microsoftcompute)을 참조 하세요.

## <a name="can-i-manage-windows-virtual-desktop-environments-with-azure-lighthouse"></a>Azure Lighthouse를 사용 하 여 Windows 가상 데스크톱 환경을 관리할 수 있나요?

Azure Lighthouse는 Windows 가상 데스크톱 환경 관리를 완벽 하 게 지원 하지 않습니다. Lighthouse는 현재 Azure AD 테 넌 트 사용자 관리를 지원 하지 않으므로, Lighthouse 고객은 고객이 사용자를 관리 하는 데 사용 하는 Azure AD에 여전히 로그인 해야 합니다.

또한 Windows 가상 데스크톱 서비스에서 CSP 샌드박스 구독을 사용할 수 없습니다. 자세히 알아보려면 [Integration sandbox 계정](/partner-center/develop/set-up-api-access-in-partner-center#integration-sandbox-account)을 참조 하세요.

마지막으로, CSP 소유자 계정에서 리소스 공급자를 사용 하도록 설정한 경우 CSP 고객 계정은 리소스 공급자를 수정할 수 없습니다.

## <a name="how-often-should-i-turn-my-vms-on-to-prevent-registration-issues"></a>등록 문제를 방지 하기 위해 Vm을 켜는 빈도

Windows 가상 데스크톱 서비스 내의 호스트 풀에 VM을 등록 하면 vm이 활성화 될 때마다 에이전트가 VM의 토큰을 정기적으로 새로 고칩니다. 등록 토큰에 대 한 인증서는 90 일 동안 유효 합니다. 90 일 제한 때문에 90 일 마다 Vm을 시작 하는 것이 좋습니다. 이 시간 제한 내에서 VM을 켜면 등록 토큰이 만료 되거나 유효 하지 않게 됩니다. 90 일 후에 VM을 시작 하 고 등록 문제가 발생 한 경우 [Windows 가상 데스크톱 에이전트 문제 해결 가이드](troubleshoot-agent.md#your-issue-isnt-listed-here-or-wasnt-resolved) 의 지침에 따라 호스트 풀에서 vm을 제거 하 고, 에이전트를 다시 설치 하 고, 풀에 등록 합니다.

## <a name="can-i-set-availability-options-when-creating-host-pools"></a>호스트 풀을 만들 때 가용성 옵션을 설정할 수 있나요?

예. Windows 가상 데스크톱 호스트 풀에는 VM을 만들 때 가용성 집합 또는 가용성 영역을 선택할 수 있는 옵션이 있습니다. 이러한 가용성 옵션은 Azure Compute에서 사용 하는 것과 동일 합니다. 호스트 풀에서 만든 VM의 영역을 선택 하면 해당 영역에서 만드는 모든 Vm에 설정이 자동으로 적용 됩니다. 호스트 풀 Vm을 여러 영역에 분산 하는 것을 선호 하는 경우 새 VM에 대 한 새 영역을 수동으로 선택 하려면 [Azure Portal를 사용 하 여 가상 머신 추가](expand-existing-host-pool.md#add-virtual-machines-with-the-azure-portal) 의 지침을 따라야 합니다.

## <a name="which-availability-option-is-best-for-me"></a>내게 적합 한 가용성 옵션은 무엇 인가요?

Vm에 사용 해야 하는 가용성 옵션은 이미지의 위치 및 관리 디스크 필드에 따라 달라 집니다. 다음 표에서는 배포에 가장 적합 한 옵션을 파악 하는 데 도움이 되도록 각 설정에 포함 된 관계에 대해 설명 합니다. 

| Availability 옵션 | 이미지 위치 | 관리 디스크 사용 옵션 단추 (라디오 단추) |
|---|---|---|
| 없음 | 갤러리 | 기본값으로 "예"를 사용 하지 않도록 설정 |
| 없음 | Blob 스토리지 | 기본값으로 "아니요" 사용 |
| 가용성 영역 | 갤러리 (blob 저장소 옵션 사용 안 함) | 기본값으로 "예"를 사용 하지 않도록 설정 |
| 관리 되는 SKU를 사용 하는 가용성 집합 (관리 디스크) | 갤러리 | 기본값으로 "예"를 사용 하지 않도록 설정 |
| 관리 되는 SKU를 사용 하는 가용성 집합 (관리 디스크) | Blob 스토리지 | 기본값으로 "아니요" 사용 |
| 관리 되는 SKU를 사용 하는 가용성 집합 (관리 디스크) | Blob 저장소 (갤러리 옵션 사용 안 함) | 기본값으로 "아니요"를 사용 하 여 사용 안 함 |
| 가용성 집합 (사용자가 새로 만듦) | 갤러리 | 기본값으로 "예"를 사용 하지 않도록 설정 |
| 가용성 집합 (사용자가 새로 만듦) | Blob 스토리지 | 기본값으로 "아니요" 사용 |
