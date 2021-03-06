---
title: Video Indexer 편집기를 사용 하 여 프로젝트를 만들고 비디오 클립을 추가 합니다.
titleSuffix: Azure Media Services
description: 이 항목에서는 Video Indexer 편집기를 사용 하 여 프로젝트를 만들고 비디오 클립을 추가 하는 방법을 보여 줍니다.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 11/28/2020
ms.author: juliako
ms.openlocfilehash: 07e4b05e12a5994c707a171c5736aea04a9c9723
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98632929"
---
# <a name="add-video-clips-to-your-projects"></a>프로젝트에 비디오 클립 추가

[Video Indexer](https://www.videoindexer.ai/) 웹 사이트를 사용 하면 비디오의 심층 정보를 사용 하 여 올바른 미디어 콘텐츠를 찾고 관심 있는 부분을 찾은 다음 결과를 사용 하 여 완전히 새로운 프로젝트를 만들 수 있습니다. 

프로젝트를 만든 후에는 Video Indexer에서 렌더링 하 고 다운로드 한 다음 자체 편집 응용 프로그램 또는 다운스트림 워크플로에서 사용할 수 있습니다.

이 기능을 유용 하 게 사용할 수 있는 몇 가지 시나리오는 다음과 같습니다. 

* 트레일러에 대 한 영화 하이라이트 만들기
* 뉴스 캐스트에서 비디오의 이전 클립을 사용 합니다.
* 소셜 미디어에 대 한 짧은 콘텐츠를 만듭니다.

이 문서에서는 프로젝트를 만들고 비디오에서 선택한 클립을 프로젝트에 추가 하는 방법을 보여 줍니다. 

## <a name="create-new-project-and-manage-videos"></a>새 프로젝트 만들기 및 비디오 관리

1. [Video Indexer](https://www.videoindexer.ai/) 웹 사이트로 이동하고 로그인합니다.
1. **프로젝트** 탭을 선택 합니다. 이전에 프로젝트를 만든 경우에는 여기에 다른 프로젝트가 모두 표시 됩니다.
1. **새 프로젝트 만들기** 를 클릭 합니다.  

    :::image type="content" source="./media/video-indexer-view-edit/new-project.png" alt-text="새 프로젝트 만들기":::
1. 연필 아이콘을 클릭 하 여 프로젝트에 이름을 지정 합니다. "제목이 없는 프로젝트" 라는 텍스트를 프로젝트 이름으로 바꾸고 확인을 클릭 합니다.

    :::image type="content" source="./media/video-indexer-view-edit/new-project-edit-name.png" alt-text="새 프로젝트":::
    
### <a name="add-videos-to-the-project"></a>프로젝트에 비디오 추가

> [!NOTE]
> 현재 프로젝트에는 동일한 언어로 인덱싱된 비디오만 포함 될 수 있습니다. </br>한 언어로 비디오를 선택 하면 다른 언어로 된 비디오를 계정에 추가할 수 없습니다. 다른 언어의 비디오는 회색으로 표시 되거나 비활성화 됩니다.

1. **비디오 추가** 를 선택 하 여이 프로젝트에서 사용할 비디오를 추가 합니다.

    계정에 모든 비디오와 "텍스트, 키워드 또는 시각적 콘텐츠 검색" 이라는 검색 상자가 표시 됩니다. 기록 및 OCR에서 지정 된 사람, 레이블, 브랜드, 키워드 또는 항목이 있는 비디오를 검색할 수 있습니다.
    
    예를 들어 아래 이미지에서 기록에만 "사용자 지정 비전"을 언급 하는 비디오를 찾고 있습니다 (검색 결과를 필터링 하려면 **필터** 사용).
    
    :::image type="content" source="./media/video-indexer-view-edit/custom-vision.png" alt-text="사용자 지정 비전을 언급 하는 비디오 검색을 보여 주는 스크린샷":::
1. **추가** 를 클릭 하 여 프로젝트에 비디오를 추가 합니다.
1. 이제 선택한 비디오가 모두 표시 됩니다. 이러한 비디오는 프로젝트에 대 한 클립을 선택할 비디오입니다.

    끌어서 놓는 방법으로 또는 목록 메뉴 단추를 선택 하 고 **아래로 이동** 또는 **위로 이동** 을 선택 하 여 비디오 순서를 다시 정렬할 수 있습니다. 목록 메뉴에서이 프로젝트의 비디오를 제거할 수도 있습니다. 
    
    언제 든 지 **비디오 추가** 를 선택 하 여이 프로젝트에 비디오를 추가할 수 있습니다. 동일한 비디오의 여러 항목을 프로젝트에 추가할 수도 있습니다. 한 비디오에서 클립을 표시 한 다음 다른 비디오의 클립을 표시 하 고 첫 번째 비디오에서 다른 클립을 표시 하려는 경우이 작업을 수행할 수 있습니다. 

### <a name="select-clips-to-use-in-your-project"></a>프로젝트에서 사용할 클립 선택

각 비디오의 오른쪽에 있는 아래쪽 화살표를 클릭 하면 타임 스탬프 (비디오 클립)에 따라 비디오에서 정보를 엽니다. 

1. 특정 클립에 대 한 쿼리를 만들려면 "기록에서 검색, 시각적 텍스트, 사람 및 레이블" 이라는 검색 상자를 사용 합니다.
1. 표시 하려는 정보를 사용자 지정 하 고 표시 하지 않으려는 정보를 사용자 지정 하려면 **정보 보기** 를 선택 합니다. 

    :::image type="content" source="./media/video-indexer-view-edit/search-try-cognitive-services.png" alt-text="인식 서비스를 시도 하는 비디오 검색을 보여 주는 스크린샷":::
1. 필터를 추가 하 여 원하는 장면에서 **필터 옵션** 을 선택 하 여 세부 정보를 추가로 지정 합니다.

    여러 필터를 추가할 수 있습니다. 
1. 결과가 만족 스 러 우면 추가 하려는 세그먼트를 선택 하 여이 프로젝트에 추가 하려는 클립을 선택 합니다. 세그먼트를 다시 클릭 하 여이 클립을 선택 취소할 수 있습니다.
    
    비디오 옆에 있는 목록 메뉴 옵션을 클릭 하 고 **모두 선택** 을 선택 하 여 비디오의 모든 세그먼트를 추가 하거나 검색 후 반환 된 모든 세그먼트를 추가 합니다. 

클립을 선택 하 고 정렬 하는 동안 페이지의 오른쪽에 있는 플레이어의 비디오를 미리 볼 수 있습니다. 

> [!IMPORTANT]
> 페이지 위쪽에서 **프로젝트 저장** 을 선택 하 여 변경할 경우 프로젝트를 저장 해야 합니다. 

### <a name="render-and-download-the-project"></a>프로젝트를 렌더링 하 고 다운로드 합니다.

> [!NOTE]
> 유료 계정 Video Indexer 프로젝트를 렌더링 하려면 인코딩 비용이 발생 합니다. Video Indexer 평가판 계정은 5 시간 렌더링으로 제한 됩니다.

1. 완료 되 면 프로젝트가 저장 되었는지 확인 합니다. 이제이 프로젝트를 렌더링할 수 있습니다. **렌더링** 을 클릭 하면 비디오 인덱서가 파일을 렌더링 하는 팝업 대화 상자가 표시 되 고 다운로드 링크가 전자 메일로 전송 됩니다. 계속을 선택합니다. 

    :::image type="content" source="./media/video-indexer-view-edit/render-download.png" alt-text="프로젝트를 렌더링 하 고 다운로드 하는 옵션과 함께 Video Indexer를 보여 주는 스크린샷":::
    
    프로젝트를 페이지 위쪽에 렌더링 하는 것도 알림이 표시 됩니다. 렌더링이 완료 되 면 프로젝트가 성공적으로 렌더링 되었음을 나타내는 새 알림이 표시 됩니다. 알림을 클릭 하 여 프로젝트를 다운로드 합니다. 프로젝트를 mp4 형식으로 다운로드 합니다.
1. **프로젝트** 탭에서 저장 된 프로젝트에 액세스할 수 있습니다. 

    이 프로젝트를 선택 하면이 프로젝트의 모든 정보 및 타임 라인이 표시 됩니다. **비디오 편집기** 를 선택 하면이 프로젝트를 계속 편집할 수 있습니다. 편집에는 비디오와 클립을 추가 또는 제거 하거나 프로젝트 이름을 바꾸는 작업이 포함 됩니다.
    
## <a name="create-a-project-from-your-video"></a>비디오에서 프로젝트 만들기

계정의 비디오에서 직접 새 프로젝트를 만들 수 있습니다. 

1. Video Indexer 웹 사이트의 **라이브러리** 탭으로 이동 합니다.
1. 프로젝트를 만드는 데 사용할 비디오를 엽니다. Insights 및 타임 라인 페이지에서 **비디오 편집기** 단추를 선택 합니다.

    그러면 새 프로젝트를 만드는 데 사용한 것과 같은 페이지로 이동 합니다. 새 프로젝트와 달리 이전에 편집을 시작한 비디오의 타임 스탬프 된 정보 세그먼트가 표시 됩니다.

## <a name="see-also"></a>참고 항목

[Video Indexer 개요](video-indexer-overview.md)

