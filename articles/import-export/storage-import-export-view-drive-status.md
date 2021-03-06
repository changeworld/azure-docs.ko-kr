---
title: Azure Import/Export의 상태 보기 | Microsoft Docs
description: Azure Import/Export 작업 및 사용 된 드라이브의 상태를 보는 방법에 대해 알아봅니다. 작업을 처리 하는 데 걸리는 시간에 영향을 주는 요소를 이해 합니다.
author: alkohli
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 03/04/2021
ms.author: alkohli
ms.subservice: common
ms.custom: contperf-fy21q3
ms.openlocfilehash: 8ef18ea663f3a77589d61ed89c50df38f5cf0d0e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102176150"
---
# <a name="view-the-status-of-azure-importexport-jobs"></a>Azure Import/Export의 상태 보기

이 아티클에서는 Azure Import/Export 작업에 대한 드라이브 및 작업 상태를 확인하는 방법을 설명합니다. Azure Import/Export 서비스를 사용하여 Azure Blob 및 Azure Files에 대량의 데이터를 안전하게 전송합니다. 이 서비스는 Azure Blob Storage에서 데이터를 내보내는 데도 사용됩니다.  

## <a name="view-job-and-drive-status"></a>작업 및 드라이브 상태 보기
Azure Portal의 **가져오기/내보내기** 탭에서 가져오기 또는 내보내기 작업의 상태를 추적할 수 있습니다.
1. https://portal.azure.com/에 로그온합니다.
2. **가져오기/내보내기 작업** 을 검색 합니다.

    ![가져오기/내보내기 작업에서 검색](./media/storage-import-export-view-drive-status/open-import-export-tab.png)

 3. 가져오기/내보내기 작업 목록이 페이지에 표시 됩니다.

    ![작업 상태 보기](./media/storage-import-export-view-drive-status/job-state.png)

4. 작업 정보를 보려면 작업을 선택 하 고 클릭 합니다.

   ![자세한 작업 상태 보기](./media/storage-import-export-view-drive-status/job-detail.png)
  
## <a name="view-job-status"></a>작업 상태 보기

프로세스에서 드라이브 위치에 따라 다음 작업 상태 중 하나가 표시됩니다.

| 작업 상태 | 설명 |
|:--- |:--- |
| 만드는 중 | 작업이 만들어지면 작업 상태는 **만드는 중** 으로 설정됩니다. 작업이 **만들기** 상태에 있는 동안 가져오기/내보내기 서비스는 드라이브가 데이터 센터에 배송 되지 않았다고 가정 합니다. 작업은 최대 2 주 동안이 상태로 남아 있을 수 있으며, 그 후에는 서비스에 의해 자동으로 삭제 됩니다. |
| 발송 | 패키지를 운송한 후 Azure Portal에서 추적 정보를 업데이트해야 합니다.  이렇게 하면 작업을 **배송** 상태로 전환 합니다. 작업은 최대 2주 동안 **운송 중** 상태로 유지됩니다. 
| 수신됨 | 데이터 센터에서 모든 드라이브를 수신한 후에 작업은 **수신됨** 으로 설정됩니다. |
| 전송 중 | 하나 이상의 드라이브에서 처리를 시작하면 작업 상태가 **전송 중** 으로 설정됩니다. 자세한 내용은 [드라이브 상태](#view-drive-status)로 이동하세요. |
| 패키징 | 모든 드라이브에서 처리를 완료한 후에 고객에게 드라이브를 반송할 때까지 작업은 **포장 중** 상태가 됩니다. |
| 완료됨 | 사용자에게 모든 드라이브를 반송한 후에 작업이 오류 없이 완료되면 작업은 **완료됨** 상태로 설정됩니다. **완료됨** 상태에서 90일이 경과하면 작업이 자동으로 삭제됩니다. |
| 종결 | 모든 드라이브를 다시 배송 한 후에는 작업을 처리 하는 동안 오류가 발생 하면 작업이 **종결** 됨으로 설정 됩니다. **종료됨** 상태에서 90일이 경과하면 작업이 자동으로 삭제됩니다. |

## <a name="view-drive-status"></a>드라이브 상태 보기

아래 표에서는 개별 드라이브가 가져오기 또는 내보내기 작업을 통해 전환할 때의 수명 주기를 설명합니다. 이제 Azure Portal에서 작업에 포함된 각 드라이브의 현재 상태를 볼 수 있습니다.

다음 표에서는 작업에 속한 각 드라이브가 통과할 수 있는 각각의 상태를 설명합니다.

| 드라이브 상태 | 설명 |
|:--- |:--- |
| 지정됨 | 가져오기 작업의 경우 Azure Portal에서 작업을 만들 때 드라이브의 초기 상태는 **지정됨** 입니다. 내보내기 작업의 경우 작업을 만들 때 드라이브가 지정되지 않으므로 초기 드라이브 상태는 **수신됨** 입니다. |
| 수신됨 | Import/Export 서비스가 가져오기 작업을 위해 운송 회사로부터 받은 드라이브를 처리할 때 드라이브가 **수신됨** 상태로 전환합니다. 내보내기 작업의 경우 초기 드라이브 상태는 **수신됨** 상태입니다. |
| 수신되지 않음 | 작업 패키지가 도착했지만 패키지에 드라이브가 포함되어 있지 않으면 해당 드라이브가 **NeverReceived** 상태로 전환합니다. 데이터 센터에서 아직 패키지를 받지 않았고 서비스에서 2 주 전에 배송 정보를 받은 경우에도 드라이브가이 상태로 전환 됩니다. |
| 전송 중 | 서비스에서 드라이브로부터 Azure Storage로 데이터를 전송하기 시작하면 드라이브가 **전송 중** 상태로 전환합니다. |
| 완료됨 | 서비스에서 모든 데이터를 오류 없이 성공적으로 전송하면 드라이브가 **완료됨** 상태로 전환합니다.
| 완료됨(추가 정보 필요) | 드라이브는 또는 드라이브에서 데이터를 복사 하는 동안 문제가 발생 했을 때 **CompletedMoreInfo** 상태로 전환 됩니다. 정보에는 Blob 덮어쓰기에 대한 오류, 경고 또는 정보 메시지가 포함될 수 있습니다.
| 반송됨 | 드라이브가 데이터 센터에서 반송 주소로 반송되면 **ShippedBack** 상태로 전환합니다. |

Azure Portal의 이 이미지는 예제 작업의 드라이브 상태를 나타냅니다.

![드라이브 상태 보기](./media/storage-import-export-view-drive-status/drive-state.png)

다음 표에서는 드라이브 오류 상태 및 각 상태에 대해 수행하는 작업을 설명합니다.

| 드라이브 상태 | 이벤트 | 해결 방법/다음 단계 |
|:--- |:--- |:--- |
| 수신 안 됨 | **NeverReceived** 로 표시 된 드라이브가 작업 배송의 일부로 수신 되지 않았기 때문에 다른 배송에 도착 합니다. | 작업 팀은 드라이브를 **수신됨** 상태로 전환합니다. |
| 해당 없음 | 작업의 일부가 아닌 드라이브가 데이터 센터에 다른 작업의 일부로 도착 합니다. | 드라이브가 추가 드라이브로 표시 됩니다. 원본 패키지와 연결 된 작업이 완료 되 면 사용자에 게 반환 됩니다. |

## <a name="time-to-process-job"></a>작업 처리 시간
Import/Export 작업을 처리하는 데 걸리는 시간은 다음과 같은 다양한 요인에 따라 달라집니다.

-  운송 시간
-  데이터 센터의 부하
-  작업 형식 및 복사되는 데이터의 크기
-  작업에서 디스크의 수 

Import/Export 서비스에는 SLA가 없지만이 서비스는 디스크를 받은 후 7 ~ 10 일 내에 복사를 완료 하기 위해 노력 합니다. Azure Portal에 게시 된 상태 외에 REST Api를 사용 하 여 작업 진행률을 추적할 수 있습니다. [작업 나열](/previous-versions/azure/dn529083(v=azure.100)) 작업 API 호출에서 백분율 완료 매개 변수를 사용 하 여 복사 진행률을 볼 수 있습니다.


## <a name="next-steps"></a>다음 단계

* [AzCopy 명령줄 유틸리티를 사용하여 데이터 전송](../storage/common/storage-use-azcopy-v10.md)
* [Azure 가져오기 내보내기 REST API 샘플](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/)