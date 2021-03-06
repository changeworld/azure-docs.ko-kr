---
title: 외부 ID용 ID 공급자 - Azure AD
description: 외부 사용자와 공유 하기 위한 기본 id 공급자로 Azure AD를 사용 하는 방법에 대해 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 03/02/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.collection: M365-identity-device-management
ms.openlocfilehash: bdef929b27c636b3908dd7a88eb93adc2382a53f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101687749"
---
# <a name="identity-providers-for-external-identities"></a>외부 ID용 ID 공급자

> [!NOTE]
> 이 문서에서 설명 하는 기능 중 일부는 Azure Active Directory의 공개 미리 보기 기능입니다. 미리 보기에 대한 자세한 내용은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

‘ID 공급자’는 애플리케이션에 인증 서비스를 제공하는 동시에 ID 정보를 만들고 유지 관리합니다. 외부 사용자와 앱 및 리소스를 공유하는 경우 Azure AD는 공유를 위한 기본 ID 공급자입니다. 즉, 이미 Azure AD 또는 Microsoft 계정이 있는 외부 사용자를 초대하는 경우 사용자가 추가 구성 없이 자동으로 로그인할 수 있습니다.

외부 Id는 Azure AD 계정 외에도 다양 한 id 공급자를 제공 합니다.

- **Microsoft 계정** (미리 보기): 게스트 사용자는 자신의 MSA (개인 Microsoft 계정)를 사용 하 여 B2B 공동 작업 초대를 교환할 수 있습니다. 셀프 서비스 등록 사용자 흐름을 설정 하는 경우 허용 되는 id 공급자 중 하나로 [Microsoft 계정 (미리 보기)](microsoft-account.md) 을 추가할 수 있습니다. 사용자 흐름에서이 id 공급자를 사용할 수 있도록 설정 하는 데 필요한 추가 구성은 없습니다.

- **일회용 암호** (미리 보기) 전자 메일: 초대를 교환 하거나 공유 리소스에 액세스 하는 경우 게스트 사용자는 전자 메일 주소로 전송 되는 임시 코드를 요청할 수 있습니다. 그런 다음, 이 코드를 입력하여 로그인을 계속합니다. 전자 메일 일회용 암호 기능은 다른 방법으로 인증 될 수 없는 B2B 게스트 사용자를 인증 합니다. 셀프 서비스 등록 사용자 흐름을 설정 하는 경우 허용 되는 id 공급자 중 하나로 **전자 메일 One-Time 암호 (미리 보기)** 를 추가할 수 있습니다. 일부 설정이 필요 합니다. [전자 메일 일회용 암호 인증](one-time-passcode.md)을 참조 하세요.

- **Google**: Google 페더레이션을 통해 외부 사용자는 자신의 Gmail 계정으로 앱에 로그인하여 초대를 사용할 수 있습니다. 셀프 서비스 등록 사용자 흐름에서 Google 페더레이션을 사용할 수도 있습니다. [Google을 id 공급자로 추가](google-federation.md)하는 방법을 참조 하세요.
   > [!IMPORTANT]
   > **2021년 1월 4일부터** Google은 [WebView 로그인 지원을 중단](https://developers.googleblog.com/2020/08/guidance-for-our-effort-to-block-less-secure-browser-and-apps.html)합니다. Gmail에서 Google 페더레이션 또는 셀프 서비스 등록을 사용하는 경우 [기간 업무 네이티브 애플리케이션의 호환성을 테스트](google-federation.md#deprecation-of-webview-sign-in-support)해야 합니다.

- **Facebook**: 앱을 빌드할 때 사용자가 자신의 Facebook 계정을 사용하여 앱에 등록할 수 있도록 셀프 서비스 등록을 구성하고 Facebook 페더레이션을 사용하도록 설정할 수 있습니다. Facebook은 셀프 서비스 등록 사용자 흐름에만 사용할 수 있으며 사용자가 초대를 사용할 때 로그인 옵션으로 사용할 수 없습니다. [Facebook을 id 공급자로 추가](facebook-federation.md)하는 방법을 참조 하세요.

- **직접 페더레이션**: SAML 또는 WS-Fed 프로토콜을 지원하는 외부 ID 공급자와 직접 페더레이션을 설정할 수도 있습니다. 직접 페더레이션을 통해 외부 사용자는 기존 소셜 또는 엔터프라이즈 계정으로 앱에 로그인하여 초대를 사용할 수 있습니다. [직접 페더레이션을 설정](direct-federation.md)하는 방법을 참조 하세요.
   > [!NOTE]
   > 셀프 서비스 등록 사용자 흐름에서는 직접 페더레이션 ID 공급자를 사용할 수 없습니다.

## <a name="adding-social-identity-providers"></a>소셜 id 공급자 추가

셀프 서비스 등록에는 기본적으로 Azure AD가 사용하도록 설정되므로 사용자는 언제든지 Azure AD 계정을 사용하여 등록할 수 있습니다. 그러나 Google 또는 Facebook과 같은 소셜 id 공급자를 비롯 한 다른 id 공급자를 사용 하도록 설정할 수 있습니다. Azure AD 테 넌 트에서 소셜 id 공급자를 설정 하려면 id 공급자에서 응용 프로그램을 만들고 자격 증명을 구성 합니다. 클라이언트 또는 앱 ID와 클라이언트 또는 앱 비밀을 가져올 수 있습니다. 그런 다음, Azure AD 테넌트에 추가할 수 있습니다.

Azure AD 테넌트에 ID 공급자를 추가하면 다음을 수행합니다.

- 외부 사용자를 조직의 앱 또는 리소스에 초대하는 경우 외부 사용자가 해당 ID 공급자와 함께 자신의 계정을 사용하여 로그인할 수 있습니다.
- 앱에 대해 [셀프 서비스 가입](self-service-sign-up-overview.md)을 사용하도록 설정하면 외부 사용자는 추가한 ID 공급자와 함께 자신의 계정을 사용하여 앱에 가입할 수 있습니다. 등록 페이지에서 사용할 수 있는 소셜 id 공급자 옵션 중에서 선택할 수 있습니다.

   ![Google 및 Facebook 옵션을 사용하여 로그인 화면을 보여 주는 스크린샷](media/identity-providers/sign-in-with-social-identity.png)

최적의 로그인 환경을 위해 가능한 경우 ID 공급자와 페더레이션하여 초대된 게스트가 앱에 액세스할 때 원활한 로그인 환경을 제공할 수 있도록 합니다.  

## <a name="next-steps"></a>다음 단계

애플리케이션에 로그인하기 위해 ID 공급자를 추가하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

- [전자 메일 일회용 암호 인증 추가](one-time-passcode.md)
- 허용 되는 소셜 id 공급자로 [Google 추가](google-federation.md)
- 허용 되는 소셜 id 공급자로 [Facebook 추가](facebook-federation.md)
- ID 공급자가 SAML 2.0 또는 WS-Fed 프로토콜을 지원하는 모든 조직과의 [직접 페더레이션을 설정](direct-federation.md)합니다. 직접 페더레이션은 셀프 서비스 가입 사용자 흐름에 대한 옵션이 아닙니다.
