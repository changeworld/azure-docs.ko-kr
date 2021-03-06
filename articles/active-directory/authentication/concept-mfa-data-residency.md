---
title: Azure AD 다단계 인증 데이터 상주
description: Azure AD 다단계 인증을 통해 사용자와 사용자 및 해당 국가/지역 내에 유지 되는 데이터에 대해 Azure AD 다단계 인증을 제공 하는 개인 및 조직 데이터에 대해 알아봅니다.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/14/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: inbarc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7381ab62eb39c555c6b4eb34f150fc71bea1f10f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103561467"
---
# <a name="data-residency-and-customer-data-for-azure-ad-multifactor-authentication"></a>Azure AD 다단계 인증을 위한 데이터 상주 및 고객 데이터

Azure AD (Azure Active Directory)는 Microsoft 365 또는 Azure와 같은 Microsoft 온라인 서비스를 구독할 때 조직에서 제공 하는 주소에 따라 지리적 위치에 고객 데이터를 저장 합니다. 고객 데이터가 저장 되는 위치에 대 한 자세한 내용은 Microsoft 보안 센터에서 [데이터가 어디에 있나요?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) 를 참조 하세요.

클라우드 기반 Azure AD 다단계 인증 및 Azure 다단계 인증 서버는 개인 데이터와 조직 데이터를 처리 하 고 저장 합니다. 이 문서에서는 저장되는 데이터 및 위치를 간략하게 설명합니다.

Azure AD 다단계 인증 서비스에는 미국, 유럽 및 아시아 태평양의 데이터 센터가 있습니다. 다음 활동은 설명 된 경우를 제외 하 고는 지역 데이터 센터에서 시작 됩니다.

* Multi-factor authentication 전화 통화는 미국 데이터 센터에서 시작 되며 전역 공급자에 의해 라우팅됩니다.
* 다른 지역의 범용 사용자 인증 요청은 현재 사용자의 위치에 따라 처리 됩니다.
* Microsoft Authenticator 앱을 사용 하는 푸시 알림은 현재 사용자의 위치를 기반으로 하는 지역 데이터 센터에서 처리 됩니다. Apple Push Notification Service와 같은 공급 업체별 장치 서비스는 사용자의 위치 외부에 있을 수 있습니다.

## <a name="personal-data-stored-by-azure-ad-multifactor-authentication"></a>Azure AD 다단계 인증을 통해 저장 된 개인 데이터

개인 데이터는 특정 사용자와 연결 된 사용자 수준 정보입니다. 다음 데이터 저장소에는 개인 정보가 포함되어 있습니다.

* 차단된 사용자
* 바이패스된 사용자
* Microsoft Authenticator 디바이스 토큰 변경 요청
* 다단계 인증 활동 보고서
* Microsoft Authenticator 활성화

이 정보는 90일 동안 보존됩니다.

Azure AD 다단계 인증은 사용자 이름, 전화 번호 또는 IP 주소와 같은 개인 데이터를 기록 하지 않습니다. 그러나 *Userobjectid* 는 사용자에 대 한 인증 시도를 식별 합니다. 로그 데이터는 30일 동안 저장됩니다.

### <a name="data-stored-by-azure-ad-multifactor-authentication"></a>Azure AD 다단계 인증에 의해 저장 되는 데이터

Azure AD B2C 인증, NPS 확장, Windows Server 2016 또는 2019 Active Directory Federation Services (AD FS) 어댑터를 제외한 Azure 공용 클라우드의 경우 다음 개인 데이터가 저장 됩니다.

| 이벤트 유형                           | 데이터 저장소 유형 |
|--------------------------------------|-----------------|
| OATH 토큰                           | 다단계 인증 로그     |
| 단방향 SMS                          | 다단계 인증 로그     |
| 음성 통화                           | 다단계 인증 로그<br/>다단계 인증 활동 보고서 데이터 저장소<br/>차단 된 사용자 (사기 행위를 보고 한 경우) |
| Microsoft Authenticator 알림 | 다단계 인증 로그<br/>다단계 인증 활동 보고서 데이터 저장소<br/>차단 된 사용자 (사기 행위를 보고 한 경우)<br/>Microsoft Authenticator 장치 토큰이 변경 될 때 요청 변경 |

21Vianet, Azure AD B2C 인증, NPS 확장 및 Windows Server 2016 또는 2019 AD FS 어댑터에서 운영 하는 Microsoft Azure Government, Microsoft Azure 독일 Microsoft Azure 다음 개인 데이터가 저장 됩니다.

| 이벤트 유형                           | 데이터 저장소 유형 |
|--------------------------------------|-----------------|
| OATH 토큰                           | 다단계 인증 로그<br/>다단계 인증 활동 보고서 데이터 저장소 |
| 단방향 SMS                          | 다단계 인증 로그<br/>다단계 인증 활동 보고서 데이터 저장소 |
| 음성 통화                           | 다단계 인증 로그<br/>다단계 인증 활동 보고서 데이터 저장소<br/>차단 된 사용자 (사기 행위를 보고 한 경우) |
| Microsoft Authenticator 알림 | 다단계 인증 로그<br/>다단계 인증 활동 보고서 데이터 저장소<br/>차단 된 사용자 (사기 행위를 보고 한 경우)<br/>Microsoft Authenticator 장치 토큰이 변경 될 때 요청 변경 |

### <a name="data-stored-by-azure-multifactor-authentication-server"></a>Azure 다단계 인증 서버에서 저장 하는 데이터

Azure 다단계 인증 서버를 사용 하는 경우 다음 개인 데이터가 저장 됩니다.

> [!IMPORTANT]
> 2019 년 7 월 1 일부 터 Microsoft는 더 이상 새 배포를 위한 다단계 인증 서버를 제공 하지 않습니다. 사용자 로부터 다단계 인증을 요구 하려는 새 고객은 클라우드 기반 Azure AD 다단계 인증을 사용 해야 합니다. 2019 년 7 월 1 일 이전에 다단계 인증 서버를 활성화 한 기존 고객은 최신 버전 및 업데이트를 다운로드 하 고 일반적인 방법으로 활성화 자격 증명을 생성할 수 있습니다.

| 이벤트 유형                           | 데이터 저장소 유형 |
|--------------------------------------|-----------------|
| OATH 토큰                           | 다단계 인증 로그<br />다단계 인증 활동 보고서 데이터 저장소 |
| 단방향 SMS                          | 다단계 인증 로그<br />다단계 인증 활동 보고서 데이터 저장소 |
| 음성 통화                           | 다단계 인증 로그<br />다단계 인증 활동 보고서 데이터 저장소<br />차단 된 사용자 (사기 행위를 보고 한 경우) |
| Microsoft Authenticator 알림 | 다단계 인증 로그<br />다단계 인증 활동 보고서 데이터 저장소<br />차단 된 사용자 (사기 행위를 보고 한 경우)<br />Microsoft Authenticator 디바이스 토큰이 변경될 때 변경 요청 |

## <a name="organizational-data-stored-by-azure-ad-multifactor-authentication"></a>Azure AD 다단계 인증을 통해 저장 된 조직 데이터

조직 데이터는 구성 또는 환경 설치를 노출할 수 있는 테 넌 트 수준 정보입니다. 다음 Azure Portal 다단계 인증 페이지에서 테 넌 트 설정은 수신 전화 인증 요청에 대 한 잠금 임계값 또는 호출자 ID 정보와 같은 조직 데이터를 저장할 수 있습니다.

* 계정 잠금
* 사기 행위 경고
* 공지
* 전화 통화 설정

Azure 다단계 인증 서버의 경우 다음 Azure Portal 페이지에 조직 데이터가 포함 될 수 있습니다.

* 서버 설정
* 일회성 바이패스
* 캐싱 규칙
* 다단계 인증 서버 상태

## <a name="multifactor-authentication-logs-location"></a>다단계 인증 로그 위치

다음 표에서는 공용 클라우드에 대 한 서비스 로그의 위치를 보여 줍니다.

| 퍼블릭 클라우드| 로그인 로그 | 다단계 인증 활동 보고서        | 다단계 인증 서비스 로그       |
|-------------|--------------|----------------------------------------|------------------------|
| 미국          | 미국           | 미국                                     | 미국                     |
| 유럽      | 유럽       | 미국                                     | 유럽 <sup>2</sup>    |
| 오스트레일리아   | 오스트레일리아    | 미국<sup>1</sup>                         | 오스트레일리아 <sup>2</sup> |

<sup>1</sup> OATH 코드 로그는 오스트레일리아에 저장 됩니다.

<sup>2</sup> 음성 통화 다단계 인증 서비스 로그는 미국에 저장 됩니다.

다음 표에서는 소 버린 클라우드의 서비스 로그 위치를 보여 줍니다.

| 소버린 클라우드                      | 로그인 로그                         | 다단계 인증 활동 보고서 (개인 데이터 포함)| 다단계 인증 서비스 로그 |
|--------------------------------------|--------------------------------------|-------------------------------|------------------|
| Microsoft Azure Germany              | 독일                              | 미국                            | 미국               |
| Azure 중국 21Vianet                 | 중국                                | 미국                            | 미국               |
| Microsoft 정부 클라우드           | 미국                                   | 미국                            | 미국               |

다단계 인증 활동 보고서는 UPN (사용자 계정 이름) 및 전체 전화 번호와 같은 개인 데이터를 포함 합니다.

다단계 인증 서비스 로그는 서비스를 작동 하는 데 사용 됩니다.

## <a name="next-steps"></a>다음 단계

클라우드 기반 Azure AD 다단계 인증 및 Azure 다단계 인증 서버에서 수집 하는 사용자 정보에 대 한 자세한 내용은 [AZURE ad 다단계 인증 사용자 데이터 수집](howto-mfa-reporting-datacollection.md)을 참조 하세요.
