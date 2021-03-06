---
title: Azure 파일 동기화 토폴로지에 폴더 구조 매핑
description: 기존 파일 및 폴더 구조를 Azure 파일 동기화와 함께 사용 하기 위해 Azure 파일 공유에 매핑합니다. 마이그레이션 문서 전체에서 공유 되는 일반 텍스트 블록
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: cde85e245c8cc6ae8c55b24270f125bacc111737
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102547556"
---
```console
Robocopy /MT:16 /NP /NFL /NDL /B /MIR /IT /COPY:DATSO /COPY:DATSO /DCOPY:DAT /UNILOG:<FilePathAndName> <SourcePath> <Dest.Path> 
```

| 스위치              | 의미 |
|---------------------|---------|
| /MT                 | 는 RoboCopy가 다중 스레드를 실행할 수 있도록 합니다. 기본값은 8이 고 최대값은 128입니다. 이 값을 코어 당 프로세서 코어 수 및 스레드 수와 일치 시킵니다. 프로덕션 서버에서 사용할 수 있는 다른 작업에 대해 코어를 예약 해야 하는지 여부를 고려 합니다. |
| /NP                 | 각 파일 및 폴더에 대 한 복사본의 진행률이 표시 되지 않습니다. 진행률을 표시 하면 복사 성능이 크게 저하 됩니다. |
| /NFL                | 파일 이름을 기록하지 않도록 지정합니다. 복사 성능을 향상 시킵니다. |
| /NDL                | 디렉터리 이름을 기록하지 않도록 지정합니다. 복사 성능을 향상 시킵니다. |
| /B                  | 백업 응용 프로그램에서 사용 하는 것과 동일한 모드에서 RoboCopy를 실행 합니다. 이를 통해 RoboCopy는 현재 사용자에 게 권한이 없는 파일을 이동할 수 있습니다. |
| /MIR                | *원본에서 대상으로의 미러* 를 사용 하면 RoboCopy가 원본과 대상 간에 델타를 복사 해야 합니다. 빈 하위 디렉터리가 복사 됩니다. 대상에 변경 되었거나 존재 하지 않는 항목 (파일 또는 폴더)이 복사 됩니다. 대상에는 있지만 소스에는 없는 항목은 대상에서 제거 (삭제) 됩니다. 이 스위치를 사용 하는 경우 원본 및 대상 폴더 구조를 정확 하 게 일치 시켜야 합니다. "일치"는 대상의 일치 하는 폴더 수준에서 올바른 소스 및 폴더 수준으로 복사 하는 것을 의미 합니다. 그 다음에만 "catch up" 복사가 성공할 수 있습니다. 원본 및 대상이 일치 하지 않을 경우를 사용 하면 `/MIR` 대규모 크기 삭제 및 수행 하면이 발생 합니다. |
| /IT                 | 특정 미러 시나리오에서 충실도가 유지 되도록 합니다. </br>*예* -두 RoboCopy 사이에서 파일을 실행 하는 경우 ACL 변경 및 특성 업데이트가 있습니다. 예를 들어 *숨김으로* 표시 됩니다. /IT를 사용 하지 않으면 RoboCopy에서 ACL 변경을 생략 하 고 대상 위치로 전송 하지 않을 수 있습니다. |
|복사`[copyflags]`  | 파일 복사의 충실도 (지정 하지 않으면 기본값 `/COPY:DAT` ), 복사 플래그: `D` = Data, = `A` Attributes, = `T` 타임 스탬프, `S` = Security = NTFS acl, `O` = Owner information, `U` = A<u>u</u>ath information. Azure 파일 공유에는 감사 정보를 저장할 수 없습니다. |
| /DCOPY:`[copyflags]`| 디렉터리 복사본에 대 한 충실도 (지정 하지 않은 경우 기본값 `/DCOPY:DA` ), 복사 플래그: `D` = Data, = `A` Attributes, `T` = 타임 스탬프입니다. |
| /UNILOG:<file name> | 로그 파일에 대 한 상태를 유니코드로 출력 합니다 (기존 로그 덮어쓰기). |
| /LFSM               | **계층화 된 저장소를 사용 하는 대상에만 해당** </br>/LFSM를 사용 하 여 RoboCopy는 ' 사용 가능한 공간 모드 '로 작업을 요청 합니다. 이 스위치는 저장소 저장소를 사용 하는 대상에만 유용 합니다 .이 경우에는 RoboCopy가 완료 되기 전에 로컬 용량이 부족 하 게 될 수 있습니다. 이 스위치는 Azure 파일 동기화 클라우드 계층화 사용 대상에 사용 하기 위해 특별히 추가 되었습니다. Azure 파일 동기화에 독립적으로 사용할 수 있습니다. 이 모드에서 RoboCopy는 파일 복사 시 대상 볼륨의 사용 가능한 공간이 ' floor ' 값 아래로 떨어질 때마다 일시 중지 됩니다. 이 값은 플래그 형식으로 지정할 수 있습니다 `/LFSM:n` . 매개 변수는 `n` 기본 2:, 또는에 지정 됩니다 `nKB` `nMB` `nGB` . `/LFSM`가 명시적 바닥 값 없이 지정 된 경우 floor는 대상 볼륨 크기의 10%로 설정 됩니다. 사용 가능한 공간 부족 모드가/MT,/EFSRAW,/B 및/ZB.와 호환 되지 않습니다. |
| /Z                  | **신중한 사용** </br>다시 시작 모드에서 파일을 복사 하 고, 불안정 한 네트워크 환경 에서만 사용 하는 것이 좋습니다. 이 옵션을 선택 하면 추가 로깅으로 인해 복사 성능이 크게 저하 됩니다. |
| /ZB                 | **신중한 사용** </br>다시 시작 모드를 사용 합니다. 액세스가 거부 된 경우이 옵션은 백업 모드를 사용 합니다. 이 옵션을 선택 하면 검사점으로 인해 복사 성능이 크게 저하 됩니다. |
   