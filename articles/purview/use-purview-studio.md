---
title: Purview Studio 사용
description: 이 개념 문서에서는 Azure 부서의 범위 Studio를 사용 하는 방법을 설명 합니다.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/12/2020
ms.openlocfilehash: ca25bbb72ff853f819f3e8ce4e0092ddb762b156
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102183814"
---
# <a name="use-purview-studio"></a>Purview Studio 사용

이 문서에서는 Azure 부서의 범위의 몇 가지 주요 기능에 대 한 개요를 제공 합니다.

## <a name="prerequisites"></a>필수 구성 요소

* Azure Portal에서 Active 부서의 범위 계정이 이미 만들어지고 사용자에 게 부서의 범위 Studio에 액세스할 수 있는 권한이 있습니다.

## <a name="launch-purview-account"></a>부서의 범위 계정 시작

* 부서의 범위 계정을 시작 하려면 Azure Portal 부서의 범위 계정으로 이동 하 여 시작 하려는 계정을 선택 하 고 계정을 시작 합니다.

   :::image type="content" source="./media/use-purview-studio/launch-from-portal.png" alt-text="Azure Purview 계정 카탈로그를 시작하기 위한 선택 항목의 스크린샷":::

* 부서의 범위 계정을 시작 하는 또 다른 방법은로 이동 하 여 `https://web.purview.azure.com` **Azure Active Directory** 을 선택 하 고 계정 이름을 선택 하 여 계정을 시작 하는 것입니다.

## <a name="home-page"></a>홈 페이지

**Home** 은 Azure 부서의 범위 클라이언트의 시작 페이지입니다.

 :::image type="content" source="./media/use-purview-studio/purview-homepage.png" alt-text="홈페이지의 스크린샷":::

다음 목록에서는 **홈 페이지** 의 주요 기능을 요약 합니다. 목록의 각 숫자는 이전 스크린샷에서 강조 표시 된 숫자에 해당 합니다.

1. 카탈로그의 이름입니다. **관리 센터**  >  **계정 정보** 에서 카탈로그 이름을 설정할 수 있습니다.

2. 카탈로그 분석에는 다음의 수가 표시 됩니다.
    - 사용자, 그룹 및 응용 프로그램
    - 데이터 원본
    - 자산
    - 용어집 용어

3. 검색 상자를 사용 하 여 데이터 카탈로그에서 데이터 자산을 검색할 수 있습니다.

4. 빠른 실행 단추는 응용 프로그램의 자주 사용 되는 기능에 대 한 액세스를 제공 합니다. 표시 되는 단추는 사용자 계정에 할당 된 역할에 따라 달라 집니다.

    - *데이터 큐레이터* 에 대 한 단추는 **기술 자료 센터**, **자산 탐색**, **용어집 관리** 및 **정보 보기** 입니다.
    - *데이터 판독기* 의 경우 주요 단추는 **기술 자료** 이며, **자산을 찾아보고**, 용어집을 **보고**, **정보를 볼** 수 있습니다.
    - *데이터 원본 관리자*  +  *데이터 큐레이터* 에 대 한 주요 단추는 **지식 센터**, **데이터 원본 등록**, **자산 검색** 및 **용어집 관리** 입니다.
    - *데이터 원본 관리자*  +  *데이터 판독기* 의 경우 주요 단추는 **지식 센터**, **데이터 원본 등록**, **자산 찾아보기** 및 **용어집 보기** 입니다.

5. 왼쪽 탐색 모음을 사용 하면 응용 프로그램의 기본 페이지를 찾을 수 있습니다. 표시 되는 단추는 사용자 계정에 할당 된 역할에 따라 달라 집니다.

    - *데이터 큐레이터* 경우에는 **홈**, **용어집**, **통찰력** 및 **관리 센터** 단추가 있습니다.
    - *데이터 판독기* 의 경우 단추는 **홈**, **용어집**, **통찰력** 및 **관리 센터** 입니다.
    - *데이터 원본 관리자*  +  *데이터 큐레이터/읽기 권한자* 의 경우 단추는 **홈**, **원본**, **용어집**, **통찰력** 및 **관리 센터** 입니다.
  
6. **최근에 액세스** 한 탭에는 최근에 액세스 한 데이터 자산 목록이 표시 됩니다. 자산에 액세스 하는 방법에 대 한 자세한 내용은 [Data Catalog 검색](how-to-search-catalog.md) 및 [asset 유형별 찾아보기](how-to-browse-catalog.md#browse-experience)를 참조 하세요.  **내 항목** 탭은 로그온 한 사용자가 소유 하는 데이터 자산의 목록입니다.
7. **유용한 링크** 에는 지역 상태, 설명서, 가격 책정, 개요 및 부서의 범위 상태에 대 한 링크가 포함 되어 있습니다.
8. 위쪽 탐색 모음은 릴리스 정보/업데이트, 변경 부서의 범위 계정, 알림, 도움말 및 피드백 섹션에 대 한 정보를 포함 합니다.

## <a name="knowledge-center"></a>지식 센터

지식 센터에서는 부서의 범위 관련 된 모든 비디오 및 자습서를 찾을 수 있습니다.

## <a name="guided-tours"></a>둘러보기

Azure 부서의 범위 Studio의 각 UX에는 페이지에 대 한 개요를 제공 하는 둘러보기가 있습니다. 둘러보기를 시작 하려면 위쪽 표시줄에서 **도움말** 을 선택 하 **고 둘러보기를 선택 합니다.**

:::image type="content" source="./media/use-purview-studio/guided-tour.png" alt-text="둘러보기의 스크린샷":::

> [!Important]
   > 데이터 원본 관리자 역할은 부서의 범위 Studio에 대 한 액세스 권한이 없습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [보안 주체 추가](tutorial-scan-data.md)
