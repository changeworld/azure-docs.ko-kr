---
title: Azure Communication Services의 SMS 개념
titleSuffix: An Azure Communication Services concept document
description: Communication Services SMS 개념에 대해 알아봅니다.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: e879b7938eb778d4cdbbef4a970325501a6124db
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105932151"
---
# <a name="sms-concepts"></a>SMS 개념

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include-phone-numbers.md)]

[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

Azure Communication Services를 사용하면 Communication Services SMS SDK를 사용하여 SMS 문자 메시지를 보내고 받을 수 있습니다. 이러한 SDK를 사용하여 고객 서비스 시나리오, 약속 미리 알림, 2단계 인증 및 기타 실시간 통신 요구를 지원할 수 있습니다. Communication Services SMS를 사용하면 배달 기능 및 응답 메트릭을 노출하면서 메시지를 안정적으로 전송할 수 있습니다.

Azure Communication Services SMS SDK의 주요 기능에는 다음이 포함됩니다.

-  애플리케이션에 SMS 기능을 추가하기 위한 **간단한** 설정 환경
- 미국에서 A2P(애플리케이션-사람) 사용 사례의 무료 전화 번호에 대한 **높은 속도** 메시지 지원
- **대량 메시징** 은 여러 받는 사람에게 한 번에 메시지를 보낼 수 있도록 지원합니다.
- 고객 지원, 경고 및 약속 미리 알림과 같은 시나리오를 지원하는 **양방향** 대화
- 애플리케이션에서 보낸 메시지에 대한 실시간 배달 보고서를 사용하여 **안정적인 배달**
- SMS 사용량 패턴을 추적하는 **Analytics**
- 무료 전화 번호에 대한 옵트아웃을 자동으로 검색하고 존중하는 **옵트아웃** 처리 지원 미국 수신자 부담 번호에 대한 옵트아웃은 미국 이동 통신 사업자에게 의무화되어 시행됩니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [sms 전송 시작](../../quickstarts/telephony-sms/send.md)

다음 문서는 사용자에게 유용할 수 있습니다.

- [SMS SDK](../telephony-sms/sdk-features.md) 숙지
- SMS 지원 [전화 번호](../../quickstarts/telephony-sms/get-phone-number.md) 가져오기
- [Azure Communication Services의 전화 번호 유형](../telephony-sms/plan-solution.md)
