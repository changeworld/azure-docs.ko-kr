---
title: 웹 Api를 호출 하는 모바일 앱 빌드 | Microsoft
titleSuffix: Microsoft identity platform | Azure
description: 웹 Api를 호출 하는 모바일 앱을 빌드하는 방법 알아보기 (개요)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviewer: brandwe
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: a678e8545f761e98abbdf026886afa624f9d2b4e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98753651"
---
# <a name="scenario-mobile-application-that-calls-web-apis"></a>시나리오: 웹 API를 호출하는 모바일 애플리케이션

웹 API를 호출하는 모바일 앱을 빌드하는 방법에 대해 알아봅니다.

## <a name="getting-started"></a>시작

아직 시작 하지 않은 경우 빠른 시작을 완료 하 여 첫 번째 응용 프로그램을 만듭니다.

- [빠른 시작: Android 앱에서 토큰 획득 및 Microsoft Graph API 호출](./quickstart-v2-android.md)
- [빠른 시작: iOS 앱에서 토큰 획득 및 Microsoft Graph API 호출](./quickstart-v2-ios.md)
- [빠른 시작: Xamarin iOS 및 Android 앱 (GitHub)에서 토큰을 획득 하 고 MICROSOFT GRAPH API를 호출 합니다.](https://github.com/Azure-Samples/active-directory-xamarin-native-v2)

## <a name="overview"></a>개요

모바일 앱에는 개인화 된 원활한 사용자 환경이 필요 합니다. Microsoft id 플랫폼을 사용 하면 모바일 개발자가 iOS 및 Android 사용자에 대 한 환경을 만들 수 있습니다. 응용 프로그램은 Azure AD (Azure Active Directory) 사용자, 개인 Microsoft 계정 사용자 및 Azure AD B2C 사용자에 게 로그인 할 수 있습니다. 대신 웹 API를 호출 하는 토큰을 가져올 수도 있습니다. 이러한 흐름을 구현 하려면 MSAL (Microsoft 인증 라이브러리)을 사용 합니다. MSAL은 업계 표준 [oauth 2.0 인증 코드 흐름](v2-oauth2-auth-code-flow.md)을 구현 합니다.

![디먼 앱](./media/scenarios/mobile-app.svg)

모바일 앱에 대 한 고려 사항:

- **사용자 환경**: 로그인을 요청 하기 전에 사용자가 앱의 값을 볼 수 있도록 허용 합니다. 필요한 권한만 요청 합니다.
- **모든 사용자 구성 지원**: 많은 모바일 비즈니스 사용자가 조건부 액세스 정책 및 장치 준수 정책을 준수 해야 합니다. 이러한 주요 시나리오를 지원 해야 합니다.
- **SSO (Single Sign-On) 구현**: msal 및 Microsoft identity platform을 사용 하 여 장치의 브라우저 또는 Microsoft Authenticator를 통해 Single Sign-On를 사용 하도록 설정 하 고 Android에서 Intune 회사 포털 수 있습니다.
- **공유 장치 모드 구현**: 병원, 제조, 소매, 재무 등의 공유 장치 시나리오에서 사용할 수 있는 응용 프로그램을 사용 하도록 설정 합니다. [공유 장치 모드 지원에 대해 자세히](msal-shared-devices.md)알아보세요.

## <a name="specifics"></a>특수 적용 사항

Microsoft id 플랫폼에서 모바일 앱을 빌드하는 경우 다음 사항을 고려해 야 합니다.

- 플랫폼에 따라 사용자가 처음으로 로그인 할 때 일부 사용자 조작이 필요할 수 있습니다. 예를 들어 iOS에서는 앱이 처음으로 SSO를 사용 하는 경우 Microsoft Authenticator를 통해 (그리고 Android에서 Intune 회사 포털) 사용자 상호 작용을 표시 해야 합니다.
- IOS 및 Android에서 MSAL은 외부 브라우저를 사용 하 여 사용자를 로그인 할 수 있습니다. 외부 브라우저가 앱 위에 표시 될 수 있습니다.
- 모바일 응용 프로그램에서는 암호를 사용 하지 마세요. 이러한 응용 프로그램에서는 모든 사용자가 암호에 액세스할 수 있습니다.

## <a name="recommended-reading"></a>추천 자료

[!INCLUDE [recommended-topics](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="next-steps"></a>다음 단계

이 시나리오의 다음 문서 [앱 등록](scenario-mobile-app-registration.md)으로 이동 합니다.
