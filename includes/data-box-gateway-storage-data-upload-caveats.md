---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/15/2020
ms.author: alkohli
ms.openlocfilehash: 385ebffb4780543b532c81b89f1847f5c84cd0ac
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96582110"
---
다음 주의 사항은 Azure로 이동되는 데이터에 적용됩니다.

- 둘 이상의 디바이스가 같은 컨테이너에 기록하지 않도록 하는 것이 좋습니다.
- 클라우드에서 복사 중인 개체와 이름이 같은 기존 Azure 개체 (예: blob 또는 파일)가 있는 경우 장치에서 클라우드의 파일을 덮어씁니다.
- 공유 폴더 아래에 만들어진 빈 디렉터리 계층 구조(파일 없음)는 Blob 컨테이너로 업로드되지 않습니다.
- 파일 탐색기 또는 명령줄을 통해 끌어서 놓기를 사용 하 여 데이터를 복사할 수 있습니다. 복사할 파일의 집계 크기가 10gb 보다 큰 경우 또는와 같은 대량 복사 프로그램을 사용 하는 것이 좋습니다 `Robocopy` `rsync` . 대량 복사 도구는 일시적인 오류에 대 한 복사 작업을 다시 시도 하 고 추가 복원 력을 제공 합니다.
- Azure Storage 컨테이너와 연결된 공유가 만들 때 공유에 대해 정의된 Blob 유형과 일치하지 않는 Blob을 업로드하는 경우 해당 Blob은 업데이트되지 않습니다. 예를 들어 장치에서 블록 blob 공유를 만드는 경우 공유를 페이지 blob이 있는 기존 클라우드 컨테이너와 연결 하 고, 공유를 새로 고쳐 파일을 다운로드 한 다음, 이미 클라우드에서 페이지 blob으로 저장 된 새로 고친 파일 중 일부를 수정 하면 업로드가 실패 하 게 됩니다.
- 공유에 파일이 만들어지면 파일 이름 바꾸기가 지원되지 않습니다.
- 공유에서 파일을 삭제하더라도 스토리지 계정의 항목은 삭제되지 않습니다.
- `rsync`를 사용하여 데이터를 복사하는 경우 `rsync -a` 옵션이 지원되지 않습니다.