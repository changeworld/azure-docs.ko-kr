---
title: 앱에 id 공급자 액세스 토큰 전달
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C의 사용자 흐름에서 OAuth 2.0 id 공급자에 대 한 액세스 토큰을 클레임으로 전달 하는 방법에 대해 알아봅니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/15/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: a99d41f5f9fc9538aaf563bd3ae56075d269c94a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97584649"
---
# <a name="pass-an-identity-provider-access-token-to-your-application-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 응용 프로그램에 id 공급자 액세스 토큰 전달

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Azure Active Directory B2C (Azure AD B2C) [사용자 흐름](user-flow-overview.md) 은 응용 프로그램 사용자에 게 id 공급자를 등록 하거나 로그인 할 수 있는 기회를 제공 합니다. 이 과정이 시작되면 Azure AD B2C는 ID 공급자로부터 [액세스 토큰](tokens-overview.md)을 받습니다. Azure AD B2C는 이 토큰을 사용하여 해당 사용자에 대한 정보를 검색합니다. 사용자는 Azure AD B2C에서 등록한 애플리케이션으로 토큰이 통과되도록 사용자 흐름에서 클레임을 활성화합니다.

::: zone pivot="b2c-user-flow"

Azure AD B2C는 [Facebook](identity-provider-facebook.md) 및 [Google](identity-provider-google.md)을 포함 하는 [OAuth 2.0](add-identity-provider.md) id 공급자의 액세스 토큰 전달을 지원 합니다. 다른 모든 ID 공급자에 대한 클레임은 빈 상태로 반환됩니다.

::: zone-end

::: zone pivot="b2c-custom-policy"

Azure AD B2C에서는 [OAuth 2.0](authorization-code-flow.md) 및 [OpenID Connect](openid-connect.md) ID 공급자의 액세스 토큰을 전달할 수 있습니다. 다른 모든 ID 공급자에 대한 클레임은 빈 상태로 반환됩니다.

::: zone-end

다음 다이어그램에서는 id 공급자 토큰이 앱에 반환 되는 방법을 보여 줍니다. 

![Id 공급자 통과 흐름](./media/idp-pass-through-user-flow/identity-provider-pass-through-flow.png)

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

::: zone pivot="b2c-user-flow"

## <a name="enable-the-claim"></a>클레임 사용

1. Azure AD B2C 테넌트의 전역 관리자로 [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. Azure AD B2C 테넌트가 포함된 디렉터리를 사용하고 있는지 확인합니다. 최상위 메뉴에서 **디렉터리 + 구독** 필터를 선택하고 테넌트가 포함된 디렉터리를 선택합니다.
3. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스** 를 선택하고 **Azure AD B2C** 를 검색하여 선택합니다.
4. **사용자 흐름 (정책)** 을 선택 하 고 사용자 흐름을 선택 합니다. 예를 들어 **B2C_1_signupsignin1** 으로 업데이트합니다.
5. **애플리케이션 클레임** 을 선택합니다.
6. **Id 공급자 액세스 토큰** 클레임을 사용 하도록 설정 합니다.

    ![Id 공급자 액세스 토큰 클레임 사용](./media/idp-pass-through-user-flow/identity-provider-pass-through-app-claim.png)

7. **저장** 을 클릭하여 사용자 흐름을 저장합니다.

## <a name="test-the-user-flow"></a>사용자 흐름 테스트

Azure AD B2C에서 애플리케이션을 테스트하는 경우 포함된 클레임을 검토하기 위해 Azure AD B2C 토큰이 `https://jwt.ms`로 반환되도록 하는 것이 유용할 수 있습니다.

1. 사용자 흐름의 개요 페이지에서 **사용자 흐름 실행** 을 선택합니다.
2. **애플리케이션** 은 이전에 등록한 애플리케이션을 선택합니다. 아래 예제에서 토큰을 보려면 **회신 URL** 에서 `https://jwt.ms`가 표시되어야 합니다.
3. **사용자 흐름 실행** 을 클릭한 다음, 계정 자격 증명으로 로그인합니다. **idp_access_token** 클레임에 ID 공급자의 액세스 토큰이 표시되어야 합니다.

    다음 예제와 비슷한 내용이 표시됩니다.

    ![Idp_access_token 블록이 강조 표시 된 jwt.ms의 디코딩된 토큰](./media/idp-pass-through-user-flow/identity-provider-pass-through-token.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="add-the-claim-elements"></a>클레임 요소 추가

1. TrustframeworkExtensions.xml 파일을 열고 `identityProviderAccessToken` 식별자를 사용하여 다음 **ClaimType** 요소를 **ClaimsSchema** 요소에 추가합니다.

    ```xml
    <BuildingBlocks>
      <ClaimsSchema>
        <ClaimType Id="identityProviderAccessToken">
          <DisplayName>Identity Provider Access Token</DisplayName>
          <DataType>string</DataType>
          <AdminHelpText>Stores the access token of the identity provider.</AdminHelpText>
        </ClaimType>
        ...
      </ClaimsSchema>
    </BuildingBlocks>
    ```

2. 액세스 토큰을 사용할 각 OAuth 2.0 공급자에 대해 **TechnicalProfile** 요소에 **OutputClaim** 요소를 추가합니다. 다음 예제는 Facebook 기술 프로필에 추가된 요소를 보여 줍니다.

    ```xml
    <ClaimsProvider>
      <DisplayName>Facebook</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Facebook-OAUTH">
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="identityProviderAccessToken" PartnerClaimType="{oauth2:access_token}" />
          </OutputClaims>
          ...
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

3. TrustframeworkExtensions.xml 파일을 저장합니다.
4. 신뢰 당사자 정책 파일(예: SignUpOrSignIn.xml)을 열고 **OutputClaim** 요소를 **TechnicalProfile** 에 추가합니다.

    ```xml
    <RelyingParty>
      <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
      <TechnicalProfile Id="PolicyProfile">
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="identityProviderAccessToken" PartnerClaimType="idp_access_token"/>
        </OutputClaims>
        ...
      </TechnicalProfile>
    </RelyingParty>
    ```

5. 정책 파일을 저장합니다.

## <a name="test-your-policy"></a>정책 테스트

Azure AD B2C에서 애플리케이션을 테스트하는 경우 포함된 클레임을 검토할 수 있도록 Azure AD B2C 토큰이 `https://jwt.ms`에 반환되도록 하는 것이 유용할 수 있습니다.

### <a name="upload-the-files"></a>파일 업로드

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. 상단 메뉴에서 **디렉터리 + 구독** 필터를 클릭 하 고 테 넌 트가 포함 된 디렉터리를 선택 하 여 Azure AD B2C 테 넌 트를 포함 하는 디렉터리를 사용 하 고 있는지 확인 합니다.
3. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스** 를 선택하고 **Azure AD B2C** 를 검색하여 선택합니다.
4. **ID 경험 프레임워크** 를 선택합니다.
5. 사용자 지정 정책 페이지에서 **정책 업로드** 를 클릭합니다.
6. **정책이 있는 경우 덮어쓰기** 를 선택한 후 TrustFrameworkExtensions.xml 파일을 찾아서 선택합니다.
7. **업로드** 를 선택합니다.
8. 신뢰 당사자 파일(예: SignUpOrSignIn.xml)에 대해 5~7단계를 반복합니다.

### <a name="run-the-policy"></a>정책 실행

1. 변경한 정책을 엽니다. 예를 들어 B2C_1A_signup_signin입니다.
2. **애플리케이션** 은 이전에 등록한 애플리케이션을 선택합니다. 아래 예제에서 토큰을 보려면 **회신 URL** 에서 `https://jwt.ms`가 표시되어야 합니다.
3. **지금 실행** 을 선택합니다.

    다음 예제와 비슷한 내용이 표시됩니다.

    ![Idp_access_token 블록이 강조 표시 된 jwt.ms의 디코딩된 토큰](./media/idp-pass-through-user-flow/identity-provider-pass-through-token-custom.png)

::: zone-end

## <a name="next-steps"></a>다음 단계

[Azure AD B2C 토큰 개요](tokens-overview.md)에서 자세히 알아보세요.
