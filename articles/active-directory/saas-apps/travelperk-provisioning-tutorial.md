---
title: '자습서: Azure Active Directory을 사용 하 여 자동 사용자 프로 비전을 위한 TravelPerk 구성 | Microsoft Docs'
description: Azure AD에서 TravelPerk로 사용자 계정을 자동으로 프로 비전 및 프로 비전 해제 하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 3e40f87d-8624-4b14-b098-80ff916103c3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2021
ms.author: Zhchia
ms.openlocfilehash: 19436b7faef081757e4500c76e7537ee78081bfa
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/24/2021
ms.locfileid: "104950383"
---
# <a name="tutorial-configure-travelperk-for-automatic-user-provisioning"></a>자습서: 자동 사용자 프로 비전을 위한 TravelPerk 구성

이 자습서에서는 자동 사용자 프로 비전을 구성 하기 위해 TravelPerk 및 Azure Active Directory (Azure AD)에서 수행 해야 하는 단계를 설명 합니다. 구성 된 경우 azure AD는 Azure AD 프로 비전 서비스를 사용 하 여 사용자 및 그룹을 [TravelPerk](https://www.travelperk.com/) 에 자동으로 프로 비전 하 고 프로 비전 합니다. 이 서비스의 기능, 작동 방법 및 질문과 대답에 대한 중요한 내용은 [Azure Active Directory를 사용하여 SaaS 애플리케이션의 사용자를 자동으로 프로비저닝 및 프로비저닝 해제](../app-provisioning/user-provisioning.md)를 참조하세요.

## <a name="capabilities-supported"></a>지원되는 기능

> [!div class="checklist"]
>
> - TravelPerk에서 사용자 만들기
> - 더 이상 액세스할 필요가 없는 경우 TravelPerk에서 사용자 제거
> - Azure AD와 TravelPerk 간에 사용자 특성을 동기화 상태로 유지
> - TravelPerk에 대 [한 Single sign-on](./travelperk-tutorial.md) (권장)

## <a name="prerequisites"></a>필수 구성 요소

이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 필수 구성 요소가 있다고 가정합니다.

- [Azure AD 테넌트](../develop/quickstart-create-new-tenant.md)
- 프로비저닝을 구성할 [권한](../roles/permissions-reference.md)이 있는 Azure AD의 사용자 계정(예: 애플리케이션 관리자, 클라우드 애플리케이션 관리자, 애플리케이션 소유자 또는 전역 관리자).
- 활성 [TravelPerk](https://app.travelperk.com/signup) 관리자 계정입니다.
- 프리미엄/Pro [요금제](https://www.travelperk.com/pricing/).


## <a name="step-1-plan-your-provisioning-deployment"></a>1단계. 프로비저닝 배포 계획

1. [프로비저닝 서비스의 작동 방식](../app-provisioning/user-provisioning.md)에 대해 알아봅니다.
2. [프로비저닝 범위](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)에 있는 사용자를 결정합니다.
3. [AZURE AD와 TravelPerk 간에 매핑할](../app-provisioning/customize-application-attributes.md)데이터를 결정 합니다.

## <a name="step-2-configure-travelperk-to-support-provisioning-with-azure-ad"></a>2단계. Azure AD를 사용 하 여 프로 비전을 지원 하도록 TravelPerk 구성

1. 관리자 계정을 사용 하 여 [TravelPerk](https://app.travelperk.com/company/integrations/scim) 응용 프로그램에 로그인 합니다.

2. **회사 설정**  >  **통합**  >  **scim** 으로 이동 합니다.

3. **SCIM API 사용** 을 클릭 합니다.

   ![사용](./media/travelperk-provisioning-tutorial/configuration.png)

4. SCIM을 통해 승인을 사용 하도록 설정할 수도 있습니다. 승인은 지정 된 승인자가 먼저 승인을 받도록 하 여 추가 거 버 넌 스를 설정 하는 데 도움이 됩니다. [여기](https://support.travelperk.com/hc/en-us/articles/360044168971-How-do-approval-processes-work-)에서 자세히 알아볼 수 있습니다.

5. 모든 사용자의 관리자가 자동으로 여행 승인 담당자가 되도록 할지 여부를 지정할 수 있습니다. 따라서 승인자는 해당 자동 승인 프로세스에서 할당 됩니다. TravelPerk는 Azure의 **관리자** 값을 사용자의 desired 승인자에 게 매핑합니다. 사용자가 프로 비전 된 사용자 승인자가 되기 전에 플랫폼에 있어야 합니다.
TravelPerk에서 적절히 구성 되지 않은 승인자는 생성 되지 않습니다.

6. 자동 승인 프로세스 만들기는 통합 페이지에서 SCIM을 사용 하도록 설정한 후 **scim 설정** 에서 사용할 수 있습니다. 이 기능을 설정 하려면 **Id 공급자** 를 선택 하 고 **자동 승인 사용** 설정/해제를 전환 합니다.

7. 필요한 승인 프로세스가 구성 되 면 **변경 내용 저장** 을 클릭 합니다.

   ![자동화](./media/travelperk-provisioning-tutorial/approval.png)

## <a name="step-3-add-travelperk-from-the-azure-ad-application-gallery"></a>3단계. Azure AD 응용 프로그램 갤러리에서 TravelPerk 추가

Azure AD 응용 프로그램 갤러리에서 TravelPerk를 추가 하 여 TravelPerk에 대 한 프로 비전 관리를 시작 합니다. 이전에 SSO에 대해 TravelPerk를 설정한 경우 동일한 응용 프로그램을 사용할 수 있습니다. 그러나 처음 통합을 테스트하는 경우 별도의 앱을 만드는 것이 좋습니다. [여기](../manage-apps/add-application-portal.md)를 클릭하여 갤러리에서 애플리케이션을 추가하는 방법에 대해 자세히 알아봅니다.

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4단계. 프로비저닝 범위에 있는 사용자 정의

Azure AD 프로비저닝 서비스를 사용하면 애플리케이션에 대한 할당 또는 사용자/그룹의 특성을 기반으로 프로비저닝되는 사용자의 범위를 지정할 수 있습니다. 할당을 기준으로 앱에 프로비저닝할 사용자의 범위를 선택하려면 다음 [단계](../manage-apps/assign-user-or-group-access-portal.md)를 사용하여 애플리케이션에 사용자 및 그룹을 할당할 수 있습니다. 사용자 또는 그룹의 특성만을 기준으로 프로비저닝할 사용자의 범위를 선택하려면 [여기](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) 설명된 대로 범위 지정 필터를 사용할 수 있습니다.

- TravelPerk에 사용자를 할당할 때 **기본 액세스** 외의 다른 역할을 선택 해야 합니다. 기본 액세스 역할이 있는 사용자는 프로비저닝에서 제외되고 프로비저닝 로그에 실질적으로 권한을 부여받지 않은 것으로 표시됩니다. 애플리케이션에서 사용할 수 있는 유일한 역할이 기본 액세스 역할인 경우에는 [애플리케이션 매니페스트를 업데이트](../develop/howto-add-app-roles-in-azure-ad-apps.md)하여 역할을 더 추가할 수 있습니다.

- 소규모로 시작합니다. 모든 사용자에게 배포하기 전에 소수의 사용자 및 그룹 집합으로 테스트합니다. 할당된 사용자 및 그룹으로 프로비저닝 범위가 설정된 경우 앱에 하나 또는 두 개의 사용자 또는 그룹을 할당하여 범위를 제어할 수 있습니다. 모든 사용자 및 그룹으로 범위가 설정된 경우 [특성 기반 범위 지정 필터](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)를 지정할 수 있습니다.

## <a name="step-5-configure-automatic-user-provisioning-to-travelperk"></a>5단계. TravelPerk에 자동 사용자 프로 비전 구성

이 섹션에서는 Azure AD의 사용자 및/또는 그룹 할당에 따라 TestApp에서 사용자 및/또는 그룹을 만들고, 업데이트하고, 사용 해제하도록 Azure AD 프로비저닝 서비스를 구성하는 단계를 안내합니다.

### <a name="to-configure-automatic-user-provisioning-for-travelperk-in-azure-ad"></a>Azure AD에서 TravelPerk에 대 한 자동 사용자 프로 비전을 구성 하려면:

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. **엔터프라이즈 애플리케이션**, **모든 애플리케이션** 을 차례로 선택합니다.

   ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **TravelPerk** 을 선택합니다.

   ![응용 프로그램 목록의 TravelPerk 링크](common/all-applications.png)

3. **프로비전** 탭을 선택합니다.

   ![프로비저닝 탭](common/provisioning.png)

4. **프로비전 모드** 를 **자동** 으로 설정합니다.

   ![프로비저닝 탭 자동](common/provisioning-automatic.png)

5. **관리자 자격 증명** 섹션 아래에서 **권한 부여** 를 클릭합니다. **TravelPerk** 의 로그인 페이지로 리디렉션됩니다. **사용자 이름** 및 **암호** 를 입력 하 고 **로그인** 단추를 클릭 합니다. 권한 부여 페이지에서 **앱 권한 부여** 를 클릭 합니다. **연결 테스트** 를 클릭 하 여 Azure AD가 TravelPerk에 연결할 수 있는지 확인 합니다. 연결에 실패 하면 SecureLogin 계정에 관리자 권한이 있는지 확인 하 고 다시 시도 하세요.

   ![관리자 자격 증명](./media/travelperk-provisioning-tutorial/authorize.png)

   ![시작](./media/travelperk-provisioning-tutorial/login.png)

   ![액세스 권한](./media/travelperk-provisioning-tutorial/authorization.png)

6. **알림 이메일** 필드에 프로비저닝 오류 알림을 받을 개인 또는 그룹의 메일 주소를 입력하고, **오류가 발생할 경우 메일 알림 보내기** 확인란을 선택합니다.

   ![알림 이메일](common/provisioning-notification-email.png)

7. **저장** 을 선택합니다.

8. **매핑** 섹션 아래에서 **TravelPerk에 사용자 Azure Active Directory 동기화를** 선택 합니다.

9. **특성 매핑** 섹션에서 Azure AD에서 TravelPerk로 동기화 되는 사용자 특성을 검토 합니다. **일치** 속성으로 선택한 특성은 업데이트 작업을 위해 TravelPerk의 사용자 계정을 일치 시키는 데 사용 됩니다. 일치 하는 [대상 특성](../app-provisioning/customize-application-attributes.md)을 변경 하도록 선택 하는 경우 TravelPerk API에서 해당 특성에 따라 사용자 필터링을 지원 하는지 확인 해야 합니다. **저장** 단추를 선택하여 변경 내용을 커밋합니다.

   | attribute                                                                         | Type      | 필터링에 지원 됨 |
   | --------------------------------------------------------------------------------- | --------- | ----------------------- |
   | userName                                                                          | String    | &check;                 |
   | externalId                                                                        | String    |
   | 활성                                                                            | 부울   |
   | name.honorificPrefix                                                              | String    |
   | name.familyName                                                                   | String    |
   | name.givenName                                                                    | String    |
   | name.middleName                                                                   | String    |
   | preferredLanguage                                                                 | String    |
   | locale                                                                            | String    |
   | phoneNumbers[type eq "work"].value                                                | String    |
   | externalId                                                                        | String    |
   | title                                                                             | String    |
   | urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:costCenter             | String    |
   | urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager                | 참조 |
   | urn: ietf: params: scim: 스키마: 확장: travelperk: 2.0: 사용자: 성별                 | String    |
   | urn: ietf: params: scim: 스키마: 확장: travelperk: 2.0: User: dateOfBirth            | String    |
   | urn: ietf: params: scim: 스키마: 확장: travelperk: 2.0: User: invoiceProfiles        | Array     |
   | urn:ietf: params: scim:ietf:ietf:travelperk: 2.0: User:emergencyContact. name  | String    |
   | urn: ietf: params: scim: 스키마: 확장: travelperk: 2.0: User: emergencyContact | String    |
   | urn: ietf: params: scim: 스키마: 확장: travelperk: 2.0: User: travelPolicy           | String    |

10. 범위 지정 필터를 구성하려면 [범위 지정 필터 자습서](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)에서 제공하는 다음 지침을 참조합니다.

11. TravelPerk에 대 한 Azure AD 프로 비전 서비스를 사용 하도록 **설정 하려면 설정** 섹션에서 **프로 비전 상태** 를 **켜기** 로 변경 합니다.

    ![프로비전 상태 켜기로 전환](common/provisioning-toggle-on.png)

12. **설정** 섹션의 **범위** 에서 원하는 값을 선택 하 여 TravelPerk에 프로 비전 하려는 사용자 및/또는 그룹을 정의 합니다.

    ![프로비전 범위](common/provisioning-scope.png)

13. 프로비전할 준비가 되면 **저장** 을 클릭합니다.

    ![프로비전 구성 저장](common/provisioning-configuration-save.png)

이 작업은 **설정** 의 **범위** 섹션에 정의된 모든 사용자 및/또는 그룹의 초기 동기화 주기를 시작합니다. 초기 주기는 Azure AD 프로비저닝 서비스가 실행되는 동안 약 40분마다 발생하는 후속 주기보다 더 많은 시간이 걸립니다.

## <a name="step-6-monitor-your-deployment"></a>6단계. 배포 모니터링

프로비저닝을 구성한 후에는 다음 리소스를 사용하여 배포를 모니터링합니다.

1. [프로비저닝 로그](../reports-monitoring/concept-provisioning-logs.md)를 사용하여 어떤 사용자가 성공적으로 프로비저닝되었는지 확인합니다.
2. [진행률 표시줄](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)을 통해 프로비저닝 주기 상태와 완료 정도를 확인합니다.
3. 프로비저닝 구성이 비정상 상태로 보이면 애플리케이션이 격리됩니다. 격리 상태에 대한 자세한 내용은 [여기](../app-provisioning/application-provisioning-quarantine-status.md)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [엔터프라이즈 앱에 대한 사용자 계정 프로비전 관리](../app-provisioning/configure-automatic-user-provisioning-portal.md)
- [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>다음 단계

- [프로비저닝 작업에 대한 로그를 검토하고 보고서를 받아보는 방법을 알아봅니다](../app-provisioning/check-status-user-account-provisioning.md).