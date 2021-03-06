---
title: Azure Automation 계정 인증 개요
description: 이 문서에서는 Azure Automation 계정 인증의 개요를 제공합니다.
keywords: 자동화 보안, 안전한 자동화, 자동화 인증
services: automation
ms.subservice: process-automation
ms.date: 02/26/2021
ms.topic: conceptual
ms.openlocfilehash: c559a81b17b92f48b2d51b7c2d26325d6a1b1cca
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101708903"
---
# <a name="automation-account-authentication-overview"></a>Automation 계정 인증 개요

Azure Automation을 사용하여 Azure, 온-프레미스 및 AWS(Amazon 웹 서비스)와 같은 기타 클라우드 공급자의 리소스에 대해 작업을 자동화할 수 있습니다. Runbook을 사용 하 여 작업을 자동화 하거나, 비즈니스 또는 운영 프로세스가 Azure 외부에서 관리 되는 경우 Hybrid Runbook Worker 수 있습니다. 이러한 환경 중 하나에서 작업 하려면 필요한 최소한의 권한으로 리소스에 안전 하 게 액세스할 수 있는 권한이 필요 합니다.

이 문서에서는 Azure Automation에서 지원하는 인증 시나리오를 살펴보고 관리해야 하는 환경에 따라 시작하는 방법을 설명합니다.

## <a name="automation-account"></a>Automation 계정

Azure Automation을 처음 시작하려면 Automation 계정을 하나 이상 만들어야 합니다. Automation 계정을 사용 하면 자동화 리소스, runbook, 자산 및 구성을 다른 계정의 리소스에서 격리할 수 있습니다. Automation 계정을 사용 하 여 리소스를 별도의 논리적 환경 또는 위임 된 책임으로 구분할 수 있습니다. 예를 들어 개발, 프로덕션, 온-프레미스 환경에 서로 다른 계정을 사용할 수 있습니다. 또는 [업데이트 관리](update-management/overview.md)를 사용 하 여 모든 컴퓨터에서 운영 체제 업데이트를 관리 하는 Automation 계정을 전용으로 사용할 수 있습니다. 

Azure Automation 계정은 Microsoft 계정 또는 Azure 구독에서 만든 계정과 다릅니다. Automation 계정 만들기에 대한 소개는 [Automation 계정 만들기](automation-quickstart-create-account.md)를 참조하세요.

## <a name="automation-resources"></a>자동화 리소스

각 Automation 계정의 Automation 리소스는 단일 Azure 지역과 연결되지만 계정은 Azure 구독 내 모든 리소스를 관리할 수 있습니다. 여러 지역에서 Automation 계정을 만드는 주된 이유는 데이터 및 리소스를 특정 지역으로 격리해야 하는 정책이 있을 수 있기 때문입니다.

Azure Automation에서 Azure Resource Manager 및 PowerShell cmdlet을 사용하여 리소스에 대해 만드는 모든 작업은 Azure AD(Azure Active Directory) 조직 ID 자격 증명 기반 인증을 사용하여 Azure에 인증해야 합니다.

## <a name="run-as-accounts"></a>실행 계정

Azure Automation의 실행 계정은 클래식 배포 모델에 배포 된 Azure Resource Manager 리소스 또는 리소스를 관리 하기 위한 인증을 제공 합니다. Azure Automation에는 두 가지 유형의 실행 계정이 있습니다.

* Azure 실행 계정: azure에 대 한 Azure Resource Manager 배포 및 관리 서비스를 기반으로 Azure 리소스를 관리할 수 있습니다.
* Azure 클래식 실행 계정: 클래식 배포 모델을 기반으로 Azure 클래식 리소스를 관리할 수 있습니다.

Azure Resource Manager 및 클래식 배포 모델에 대해 자세히 알아보려면 [리소스 관리자 및 클래식 배포](../azure-resource-manager/management/deployment-models.md)를 참조 하세요.

>[!NOTE]
>Azure CSP(클라우드 솔루션 공급자) 구독은 Azure Resource Manager 모델만 지원합니다. Azure Resource Manager 기반이 아닌 서비스는 프로그램에서 사용할 수 없습니다. CSP 구독을 사용하는 경우 Azure 클래식 실행 계정이 생성되지 않지만 Azure 실행 계정은 생성됩니다. CSP 구독에 대해 자세히 알아보려면 [CSP 구독에서 사용 가능한 서비스](/azure/cloud-solution-provider/overview/azure-csp-available-services)를 참조하세요.

Automation 계정을 만들 때 실행 계정은 기본적으로 동시에 만들어집니다. Automation 계정과 함께 만들지 않기로 선택 하면 나중에 개별적으로 만들 수 있습니다. Azure 클래식 실행 계정은 선택 사항이 며 클래식 리소스를 관리 해야 하는 경우 별도로 생성 됩니다.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RWwtF3]

### <a name="run-as-account"></a>실행 계정

실행 계정을 만들 때 다음 작업을 수행 합니다.

* 자체 서명 된 인증서를 사용 하 여 Azure AD 응용 프로그램을 만들고, Azure AD에서 응용 프로그램에 대 한 서비스 주체 계정을 만들며, 현재 구독의 계정에 대 한 [참가자](../role-based-access-control/built-in-roles.md#contributor) 역할을 할당 합니다. 인증서 설정을 [판독기](../role-based-access-control/built-in-roles.md#reader) 또는 다른 역할로 변경할 수 있습니다. 자세한 내용은 [Azure Automation의 역할 기반 액세스 제어](automation-role-based-access-control.md)를 참조하세요.

* 지정된 Automation 계정에서 `AzureRunAsCertificate`라는 Automation 인증서 자산을 만듭니다. 인증서 자산은 Azure AD 애플리케이션에서 사용하는 인증서 프라이빗 키를 보유합니다.

* 지정된 Automation 계정에서 `AzureRunAsConnection`이라는 Automation 연결 자산을 만듭니다. 이 연결 자산은 애플리케이션 ID, 테넌트 ID, 구독 ID 및 인증서 지문을 보유합니다.

### <a name="azure-classic-run-as-account"></a>Azure 클래식 실행 계정

Azure 클래식 실행 계정을 만들 때 다음 작업을 수행 합니다.

> [!NOTE]
> 이 유형의 실행 계정을 만들거나 갱신 하려면 구독에 대 한 공동 관리자 여야 합니다.

* 구독에 관리 인증서를 만듭니다.

* 지정된 Automation 계정에서 `AzureClassicRunAsCertificate`라는 Automation 인증서 자산을 만듭니다. 인증서 자산은 관리 인증서에서 사용되는 인증서 프라이빗 키를 보유합니다.

* 지정된 Automation 계정에서 `AzureClassicRunAsConnection`이라는 Automation 연결 자산을 만듭니다. 연결 자산은 구독 이름, 구독 ID 및 인증서 자산 이름을 보유합니다.

## <a name="service-principal-for-run-as-account"></a>실행 계정에 대한 서비스 주체

실행 계정의 서비스 주체에는 기본적으로 Azure AD를 읽을 권한이 없습니다. Azure AD를 읽거나 관리하는 권한을 추가하려면 **API 권한** 에서 서비스 주체에 대한 권한을 부여해야 합니다. 자세한 내용은 [웹 API에 액세스할 수 있는 권한 추가](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-your-web-api)를 참조 하세요.

## <a name="run-as-account-permissions"></a><a name="permissions"></a>실행 계정 권한

이 섹션에서는 일반 실행 계정 및 클래식 실행 계정의 권한을 둘 다 정의합니다.

* 실행 계정을 만들거나 업데이트 하려면 Azure Active Directory의 응용 프로그램 관리자와 구독의 소유자가 모든 작업을 완료할 수 있습니다.
* 클래식 실행 계정을 구성하거나 갱신하려면 구독 수준에서 공동 관리자 역할이 있어야 합니다. 클래식 구독 권한을 자세히 알아보려면 [Azure 클래식 구독 관리자](../role-based-access-control/classic-administrators.md#add-a-co-administrator)를 참조하세요.

업무가 구분되어 있는 경우를 위해 다음 표에 작업, 해당 cmdlet 및 필요한 권한의 목록이 나와 있습니다.

|Task|Cmdlet  |최소 권한  |권한을 설정하는 위치|
|---|---------|---------|---|
|Azure AD 애플리케이션 만들기|[New-AzADApplication](/powershell/module/az.resources/new-azadapplication)     | 애플리케이션 개발자 역할<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>홈 > Azure AD > 앱 등록 |
|애플리케이션에 자격 증명을 추가합니다.|[New-AzADAppCredential](/powershell/module/az.resources/new-azadappcredential)     | 애플리케이션 관리자 또는 전역 관리자<sup>1</sup>         |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>홈 > Azure AD > 앱 등록|
|Azure AD 서비스 주체 만들기 및 가져오기|[New-AzADServicePrincipal](/powershell/module/az.resources/new-azadserviceprincipal)</br>[Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal)     | 애플리케이션 관리자 또는 전역 관리자<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>홈 > Azure AD > 앱 등록|
|지정 된 보안 주체에 대 한 Azure 역할을 할당 하거나 가져옵니다.|[New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment)</br>[Get-AzRoleAssignment](/powershell/module/Az.Resources/Get-AzRoleAssignment)      | 사용자 액세스 관리자 또는 소유자이거나 다음 권한이 있어야 합니다.</br></br><code>Microsoft.Authorization/Operations/read</br>Microsoft.Authorization/permissions/read</br>Microsoft.Authorization/roleDefinitions/read</br>Microsoft.Authorization/roleAssignments/write</br>Microsoft.Authorization/roleAssignments/read</br>Microsoft.Authorization/roleAssignments/delete</code></br></br> | [구독](../role-based-access-control/role-assignments-portal.md)</br>Home > Subscription > \<subscription name\> -Access Control (IAM)|
|Automation 인증서 생성 또는 제거|[New-AzAutomationCertificate](/powershell/module/Az.Automation/New-AzAutomationCertificate)</br>[Remove-AzAutomationCertificate](/powershell/module/az.automation/remove-azautomationcertificate)     | 리소스 그룹의 기여자         |Automation 계정 리소스 그룹|
|Automation 연결 생성 또는 제거|[New-AzAutomationConnection](/powershell/module/az.automation/new-azautomationconnection)</br>[Remove-AzAutomationConnection](/powershell/module/az.automation/remove-azautomationconnection)|리소스 그룹의 기여자 |Automation 계정 리소스 그룹|

<sup>1</sup> azure ad 테 넌 트의 관리자가 아닌 사용자는 azure ad 테 넌 트의 **사용자가 응용 프로그램을 등록할 수 있음** 옵션을 **예** 로 설정 **하는 경우** [AD 응용 프로그램을 등록할](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app) 수 있습니다. 애플리케이션 등록 설정이 **아니요** 인 경우 이 작업을 수행하는 사용자를 이 표에서 정의해야 합니다.

구독의 전역 관리자 역할에 추가되기 전에 구독 Active Directory 인스턴스의 멤버가 아닌 경우 게스트로 추가됩니다. 이 경우 `You do not have permissions to create…` **Automation 계정 추가** 페이지에서 경고가 표시 됩니다.

오류 메시지를 생성하는 상황이 해결되었는지 확인하려면 다음을 수행합니다.

1. Azure Portal의 Azure Active Directory 창에서 **사용자 및 그룹** 을 선택합니다.
2. **모든 사용자** 를 선택합니다.
3. 이름을 선택하고 **프로필** 을 선택합니다.
4. 사용자의 프로필에 있는 **사용자 유형** 특성의 값이 **게스트** 로 설정되지 않았는지 확인합니다.

## <a name="role-based-access-control"></a>역할 기반 액세스 제어

Azure Resource Manager에서 역할 기반 액세스 제어를 사용하여 Azure AD 사용자 계정 및 실행 계정에 허용된 작업을 수락하고 서비스 주체를 인증할 수 있습니다. Automation 사용 권한 관리 모델을 개발하는 방법에 대한 자세한 내용은 [Azure Automation에서 역할 기반 액세스 제어 문서](automation-role-based-access-control.md)를 참조하세요.

리소스 그룹의 권한 할당에 대 한 엄격한 보안 컨트롤이 있는 경우 실행 계정 멤버 자격을 리소스 그룹의 **참가자** 역할에 할당 해야 합니다.

## <a name="runbook-authentication-with-hybrid-runbook-worker"></a>Hybrid Runbook Worker를 사용하여 Runbook 인증

데이터 센터의 Hybrid Runbook Worker에서 또는 AWS와 같은 기타 클라우드 환경의 컴퓨팅 서비스에서 실행되는 Runbook은 일반적으로 Azure 리소스를 인증하는 Runbook에 사용되는 것과 동일한 방법을 사용할 수 없습니다. 이러한 리소스는 Azure 외부에서 실행되므로 로컬로 액세스하는 리소스에 인증하려면 Automation에서 정의한 자체 보안 자격 증명이 필요하기 때문입니다. Runbook Workers를 사용하는 Runbook 인증에 대한 자세한 내용은 [Hybrid Runbook Worker에서 Runbook 실행](automation-hrw-run-runbooks.md)을 참조하세요.

Azure VM에서 Hybrid Runbook Worker를 사용하는 Runbook의 경우 실행 계정 대신 [관리형 ID로 Runbook 인증](automation-hrw-run-runbooks.md#runbook-auth-managed-identities)을 사용하여 Azure 리소스를 인증할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* Azure Portal에서 Automation 계정을 만들려면 [독립 실행형 Azure Automation 계정 만들기](automation-create-standalone-account.md)를 참조하세요.
* 템플릿을 사용하여 계정을 만들려면 [Azure Resource Manager 템플릿을 사용하여 Automation 계정 만들기](quickstart-create-automation-account-template.md)를 참조하세요.
* Amazon Web Services를 사용하는 인증의 경우 [Amazon Web Services를 사용하여 Runbook 인증](automation-config-aws-account.md)을 참조하세요.