---
title: 기본 제공 방화벽에서 포트 보안 확인
description: Azure SQL Managed Instance에서 기본 제공 방화벽 보호를 확인 하는 방법에 대해 알아봅니다.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein
ms.date: 12/04/2018
ms.openlocfilehash: d8d46518415dc56e2792ef090b0be654d538426f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96853274"
---
# <a name="verify-the-azure-sql-managed-instance-built-in-firewall"></a>Azure SQL Managed Instance 기본 제공 방화벽 확인
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

SQL Managed Instance를 보호 하는 NSG (네트워크 보안 그룹)의 **모든 원본** 에서 관리 포트 9000, 9003, 1438, 1440 및 1452를 열려면 Azure sql Managed Instance [필수 인바운드 보안 규칙](connectivity-architecture-overview.md#mandatory-inbound-security-rules-with-service-aided-subnet-configuration) 을 설정 해야 합니다. 이러한 포트는 NSG 수준에서 열리지만 기본 제공 방화벽으로는 네트워크 수준에서 보호됩니다.

## <a name="verify-firewall"></a>방화벽 확인

이러한 포트를 확인하려면 보안 스캐너 도구를 사용하여 이러한 포트를 테스트합니다. 다음 스크린샷은 이러한 도구 중 하나를 사용하는 방법을 보여 줍니다.

![기본 제공 방화벽 확인](./media/management-endpoint-verify-built-in-firewall/03_verify_firewall.png)

## <a name="next-steps"></a>다음 단계

SQL Managed Instance 및 연결에 대 한 자세한 내용은 [AZURE sql Managed Instance 연결 아키텍처](connectivity-architecture-overview.md)를 참조 하세요.
