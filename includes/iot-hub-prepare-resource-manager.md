---
author: robinsh
ms.author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 26c158145de6ce729d8a7060152b19fb14b63d58
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "95556253"
---
## <a name="prepare-to-authenticate-azure-resource-manager-requests"></a>Azure Resource Manager 요청 인증 준비
Azure AD(Active Directory)에서 [Azure Resource Manager][lnk-authenticate-arm]를 사용하여 리소스에서 수행하는 모든 작업을 인증해야 합니다. 가장 쉽게 구성할 수 있는 방법은 PowerShell 또는 Azure CLI를 사용하는 것입니다.

계속하기 전에 [Azure PowerShell cmdlet][lnk-powershell-install]을 설치합니다.

다음 단계는 PowerShell을 사용하여 AD 애플리케이션에 대해 암호 인증을 설정하는 방법을 보여 줍니다. 표준 PowerShell 세션에서 이러한 명령을 실행할 수 있습니다.

1. 다음 명령을 사용하여 Azure 구독에 로그인합니다.

    ```powershell
    Connect-AzAccount
    ```

1. Azure 구독이 여러 개 있는 경우 Azure에 로그인하면 자격 증명과 연결된 모든 Azure 구독에 액세스할 수 있습니다. 다음 명령을 사용하여 사용할 수 있는 Azure 구독을 나열합니다.

    ```powershell
    Get-AzSubscription
    ```

    다음 명령을 사용하여 IoT Hub를 관리하는 명령을 실행하는 데 사용할 구독을 선택합니다. 이전 명령의 출력에서 구독 이름 또는 ID를 사용할 수 있습니다.

    ```powershell
    Select-AzSubscription `
        -SubscriptionName "{your subscription name}"
    ```

2. **TenantId** 및 **SubscriptionId** 를 적어둡니다. 나중에 필요합니다.
3. 다음 명령을 사용하여 새 Azure Active Directory 애플리케이션을 만듭니다. 자리 표시자는 바꿉니다.
   
   * **{표시 이름}:****MySampleApp** 과 같은 애플리케이션의 표시 이름입니다.
   * **{Home PAGE URL}:** **http: \/ /mysampleapp/home** 와 같은 응용 프로그램 홈페이지의 url입니다. 이 URL이 실제 애플리케이션을 가리킬 필요는 없습니다.
   * **{응용 프로그램 식별자}:** **Http: \/ /mysampleapp** 와 같은 고유 식별자입니다. 이 URL이 실제 애플리케이션을 가리킬 필요는 없습니다.
   * **{암호}:** 앱에서 인증하기 위해 사용할 암호입니다.
     
     ```powershell
     $SecurePassword=ConvertTo-SecureString {password} –asplaintext –force
     New-AzADApplication -DisplayName {Display name} -HomePage {Home page URL} -IdentifierUris {Application identifier} -Password $SecurePassword
     ```
4. 만든 애플리케이션의 **ApplicationId** 를 적어 둡니다. 나중에 필요합니다.
5. 다음 명령을 사용하여 새 서비스 주체를 만듭니다. 이전 단계에서 **{MyApplicationId}** 를 **ApplicationId** 로 바꿉니다.
   
    ```powershell
    New-AzADServicePrincipal -ApplicationId {MyApplicationId}
    ```
6. 다음 명령을 사용하여 역할 할당을 설정합니다. **{MyApplicationId}** 를 **ApplicationId** 로 바꿉니다.
   
    ```powershell
    New-AzRoleAssignment -RoleDefinitionName Owner -ServicePrincipalName {MyApplicationId}
    ```

이제 사용자 지정 C# 애플리케이션에서 인증할 수 있는 Azure AD 애플리케이션을 만드는 작업을 완료했습니다. 이 자습서의 뒷부분에서 다음 값이 필요합니다.

* TenantId
* SubscriptionId
* ApplicationId
* 암호

[lnk-authenticate-arm]: /rest/api/
[lnk-powershell-install]: /powershell/azure/install-az-ps