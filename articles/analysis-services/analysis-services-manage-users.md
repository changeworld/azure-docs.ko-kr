---
title: Azure Analysis Services 인증 및 사용자 권한 | Microsoft Docs
description: 이 문서에서는 Azure Analysis Services가 ID 관리 및 사용자 인증에 Azure AD(Azure Active Directory)를 사용하는 방법을 설명합니다.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 551bae56565140da3754e74a23b1cc18087f1171
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96487442"
---
# <a name="authentication-and-user-permissions"></a>인증 및 사용자 권한

Azure Analysis Services는 ID 관리 및 사용자 인증에 Azure AD(Azure Active Directory)를 사용합니다. Azure Analysis Services 서버를 만들거나, 관리하거나, 이 서버에 연결하는 모든 사용자는 동일한 구독의 [Azure AD 테넌트](../active-directory/fundamentals/active-directory-whatis.md)에 유효한 사용자 ID가 있어야 합니다.

Azure Analysis Services는 [Azure AD B2B 협업](../active-directory/external-identities/what-is-b2b.md)을 지원합니다. B2B를 사용하여 Azure AD 디렉터리의 게스트 사용자로 조직 외부의 사용자를 초대할 수 있습니다. 게스트는 다른 Azure AD 테넌트 디렉터리나 모든 유효한 메일 주소에서 가져올 수 있습니다. 초대된 사용자가 Azure에서 메일로 보낸 초대를 수락하면 사용자 ID가 테넌트 디렉터리에 추가됩니다. 해당 ID를 보안 그룹에 추가하거나 서버 관리자 또는 데이터베이스 역할의 멤버로 추가할 수 있습니다.

![Azure Analysis Services 인증 아키텍처](./media/analysis-services-manage-users/aas-manage-users-arch.png)

## <a name="authentication"></a>인증

모든 클라이언트 애플리케이션 및 도구에서 하나 이상의 Analysis Services [클라이언트 라이브러리](/analysis-services/client-libraries?view=azure-analysis-services-current&preserve-view=true)(AMO, MSOLAP, ADOMD)를 사용하여 서버에 연결합니다. 

세 클라이언트 라이브러리는 Azure AD 대화형 흐름과 비대화형 인증 방법을 모두 지원합니다. Active Directory 암호 및 Active Directory 통합 인증 방법의 두 가지 비대화형 방법은 AMOMD 및 MSOLAP를 활용하는 애플리케이션에서 사용할 수 있습니다. 이러한 두 가지 방법을 사용할 경우 팝업 대화 상자가 절대 표시되지 않습니다.

Excel 및 Power BI Desktop과 같은 클라이언트 애플리케이션과 SSMS 및 Visual Studio용 Analysis Services 프로젝트 확장과 같은 도구는 최신 릴리스로 업데이트될 때 최신 버전의 라이브러리를 설치합니다. Power BI Desktop, SSMS 및 Analysis Services 프로젝트 확장은 매월 업데이트됩니다. Excel은 [Microsoft 365로 업데이트](https://support.microsoft.com/office/when-do-i-get-the-newest-features-for-microsoft-365-da36192c-58b9-4bc9-8d51-bb6eed468516)됩니다. Microsoft 365 업데이트는 빈도가 낮지만 일부 조직에서는 지연 된 채널을 사용 합니다. 즉, 업데이트는 최대 3 개월까지 지연 됩니다.

사용하는 클라이언트 애플리케이션이나 도구에 따라 인증 유형 및 로그인 방법이 달라질 수 있습니다. 각 애플리케이션은 Azure Analysis Services와 같은 클라우드 서비스에 연결하는 다양한 기능을 지원할 수 있습니다.

Power BI Desktop, Visual Studio 및 SSMS는 MFA (Azure AD Multi-Factor Authentication)도 지 원하는 대화형 메서드인 유니버설 인증 Active Directory 지원 합니다. Azure AD MFA는 간단한 로그인 프로세스를 제공 하는 동시에 데이터 및 응용 프로그램에 대 한 액세스를 보호 하는 데 도움이 됩니다. 전화 통화, 문자 메시지, 모바일 앱 알림 등의 몇 가지 간편한 검증 옵션을 제공하는 강력한 인증을 포함합니다. Azure AD를 사용하는 대화형 MFA는 유효성 검사를 위한 팝업 대화 상자를 표시할 수 있습니다. **유니버설 인증을 권장** 합니다.

Windows 계정을 사용하여 Azure에 로그인하고 유니버설 인증을 선택하거나 사용 가능하지 않은 경우(Excel), [AD FS(Active Directory Federation Services)](/windows-server/identity/ad-fs/deployment/how-to-connect-fed-azure-adfs)가 필요합니다. 페더레이션을 사용 하 여 Azure AD 및 Microsoft 365 사용자는 온-프레미스 자격 증명을 사용 하 여 인증 되며 Azure 리소스에 액세스할 수 있습니다.

### <a name="sql-server-management-studio-ssms"></a>SSMS(SQL Server Management Studio)

Azure Analysis Services 서버는 Windows 인증, Active Directory 암호 인증 및 Active Directory 유니버설 인증을 사용하여 [SSMS V17.1](/sql/ssms/download-sql-server-management-studio-ssms) 이상에서 연결하는 것을 지원합니다. 일반적으로 다음과 같은 이유로 Active Directory 유니버설 인증을 사용하는 것이 좋습니다.

*  대화형 및 비대화형 인증 방법을 지원합니다.

*  Azure AS 테넌트에 초대된 Azure B2B 게스트 사용자를 지원합니다. 서버에 연결될 때 게스트 사용자는 Active Directory 유니버설 인증을 선택해야 합니다.

*  MFA(Multi-Factor Authentication)를 지원합니다. Azure AD MFA는 전화 통화, 문자 메시지, pin을 사용 하는 스마트 카드 또는 모바일 앱 알림과 같은 다양 한 확인 옵션을 통해 데이터와 응용 프로그램에 대 한 액세스를 보호 합니다. Azure AD를 사용하는 대화형 MFA는 유효성 검사를 위한 팝업 대화 상자를 표시할 수 있습니다.

### <a name="visual-studio"></a>Visual Studio

Visual Studio는 MFA를 지원하는 Active Directory 유니버설 인증을 사용하여 Azure Analysis Services에 연결합니다. 처음으로 배포하면 사용자에게 Azure에 로그인하라는 메시지가 표시됩니다. 사용자는 배포되는 서버에 대해 서버 관리자 권한이 있는 계정으로 Azure에 로그인해야 합니다. 처음으로 Azure에 로그인하면 토큰이 할당됩니다. 토큰은 나중에 다시 연결할 수 있도록 메모리 내에 캐시합니다.

### <a name="power-bi-desktop"></a>Power BI Desktop

Power BI Desktop은 MFA를 지원하는 Active Directory 유니버설 인증을 사용하여 Azure Analysis Services에 연결합니다. 처음으로 연결하면 사용자에게 Azure에 로그인하라는 메시지가 표시됩니다. 사용자는 서버 관리자 또는 데이터베이스 역할에 포함되어 있는 계정으로 Azure에 로그인해야 합니다.

### <a name="excel"></a>Excel

Excel 사용자는 Windows 계정, 조직 ID(메일 주소) 또는 외부 메일 주소를 사용하여 서버에 연결할 수 있습니다. 외부 메일 ID는 게스트 사용자로 Azure AD에 있어야 합니다.

## <a name="user-permissions"></a>사용자 권한

**서버 관리자** 는 Azure Analysis Services 서버 인스턴스와 관련이 있습니다. 서버 관리자는 Azure Portal, SSMS 및 Visual Studio와 같은 도구에 연결하여 데이터베이스 추가, 사용자 역할 관리 등의 작업을 수행합니다. 기본적으로 서버를 만드는 사용자는 Analysis Services 서버 관리자로 자동 추가됩니다. 다른 관리자는 Azure Portal이나 SSMS를 사용하여 추가할 수 있습니다. 서버 관리자는 동일한 구독의 Azure AD 테넌트에 계정이 있어야 합니다. 자세한 내용은 [서버 관리자 관리](analysis-services-server-admins.md)를 참조하세요. 

**데이터베이스 사용자** 는 Excel 또는 Power BI와 같은 클라이언트 애플리케이션을 사용하여 model 데이터베이스에 연결합니다. 사용자는 데이터베이스 역할에 추가되어야 합니다. 데이터베이스 역할은 데이터베이스에 대해 관리자, 처리 또는 읽기 권한을 정의합니다. 관리자 권한이 있는 역할의 데이터베이스 사용자는 서버 관리자와 다르다는 점을 이해해야 합니다. 그러나 기본적으로 서버 관리자는 데이터베이스 관리자이기도 합니다. 자세한 내용은 [데이터베이스 역할 및 사용자 관리](analysis-services-database-users.md)를 참조하세요.

**Azure 리소스 소유자**. 리소스 소유자는 Azure 구독에 대한 리소스를 관리합니다. 리소스 소유자는 Azure Portal의 **액세스 제어** 나 Azure Resource Manager 템플릿을 사용하여 구독 내에서 소유자 또는 참가자 역할에 Azure AD 사용자 ID를 추가할 수 있습니다. 

![Azure Portal의 액세스 제어](./media/analysis-services-manage-users/aas-manage-users-rbac.png)

이 수준의 역할은 포털에서 완료할 수 있거나 Azure Resource Manager 템플릿을 사용하여 작업을 수행해야 하는 사용자 또는 계정에 적용됩니다. 자세히 알아보려면 azure [역할 기반 액세스 제어 (AZURE RBAC)](../role-based-access-control/overview.md)를 참조 하세요. 

## <a name="database-roles"></a>데이터베이스 역할

 테이블 형식 모델에 대해 정의된 역할이 데이터베이스 역할입니다. 즉, 이 역할에는 해당 멤버가 model 데이터베이스에서 수행할 수 있는 작업을 정의하는 특정 권한이 있는 Azure AD 사용자 및 보안 그룹으로 구성된 멤버가 포함됩니다. 데이터베이스 역할은 별도의 개체로 데이터베이스에 생성되며, 해당 역할이 생성된 데이터베이스에만 적용됩니다.   
  
 기본적으로 새 테이블 형식 모델 프로젝트를 만들 경우 모델 프로젝트에는 아무 역할도 없습니다. 역할은 Visual Studio의 [역할 관리자] 대화 상자를 사용하여 정의할 수 있습니다. 모델 프로젝트 디자인 중 정의된 역할은 모델 작업 영역 데이터베이스에만 적용됩니다. 모델을 배포하면 배포된 모델에 동일한 역할이 적용됩니다. 모델을 배포한 후 서버 및 데이터베이스 관리자는 SSMS를 사용하여 역할 및 멤버를 관리할 수 있습니다. 자세한 내용은 [데이터베이스 역할 및 사용자 관리](analysis-services-database-users.md)를 참조하세요.
  
## <a name="next-steps"></a>다음 단계

[Azure Active Directory 그룹을 사용하여 리소스에 대한 액세스 관리](../active-directory/fundamentals/active-directory-manage-groups.md)   
[데이터베이스 역할 및 사용자 관리](analysis-services-database-users.md)  
[서버 관리자 관리](analysis-services-server-admins.md)  
[Azure RBAC(Azure 역할 기반 액세스 제어)](../role-based-access-control/overview.md)