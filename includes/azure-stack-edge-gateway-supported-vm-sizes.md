---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/09/2020
ms.author: alkohli
ms.openlocfilehash: d22d40b21671b148083b48efe9772f118dc3b292
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105582554"
---
VM 크기에 따라 VM에서 사용할 수 있는 계산 리소스 (예: CPU, GPU, 메모리)의 양이 결정 됩니다. 워크 로드에 적합 한 VM 크기를 사용 하 여 가상 컴퓨터를 만들어야 합니다. 모든 컴퓨터가 동일한 하드웨어에서 실행 되는 경우에도 컴퓨터 크기는 디스크 액세스에 대해 다른 제한이 적용 됩니다. 이렇게 하면 Vm 전체에서 전체 디스크 액세스를 관리할 수 있습니다. 워크 로드가 증가할 경우 기존 가상 머신의 크기를 조정할 수도 있습니다.

다음 Vm은 Azure Stack Edge 장치에서 만들 수 있도록 지원 됩니다.

### <a name="dv2-series"></a>Dv2 시리즈
|크기     |vCPU     |메모리(GiB) | 리소스 디스크 크기 (GiB)  | OS 디스크 크기 (GiB) | 최대 데이터 디스크 수 | 최대 NIC 수 |
|-------------------|----|----|-----|----|------|------------|
|**Standard_D1_v2** |1   |3.5 |50   |1000| 4    |2 |
|**Standard_D2_v2** |2   |7   |100  |1000| 8    |4 |
|**Standard_D3_v2** |4   |14  |200  |1000| 16  |4 |
|**Standard_D4_v2** |8   |28  |400  |1000| 32  |8 |
|**Standard_D5_v2** |16  |56  |800  |1000| 64  |8 |
|**Standard_D11_v2** |2   |14  |100  |1000| 8     |2 |
|**Standard_D12_v2** |4   |28  |200  |1000| 16   |4 |
|**Standard_D13_v2** |8   |56  |400  |1000| 32  |8 |

### <a name="dsv2-series"></a>DSv2 시리즈
|크기     |vCPU     |메모리(GiB) |  리소스 디스크 크기 (GiB)  | OS 디스크 크기 (GiB) | 최대 데이터 디스크 수| 최대 NIC 수 |
|--------------------|----|----|----|-----|------|-------------|
|**Standard_DS1_v2** |1   |3.5 |7  |4000  |1000 |4  |
|**Standard_DS2_v2** |2   |7   |14 |8000  |1000 |8  |
|**Standard_DS3_v2** |4   |14  |28 |16000 |1000 |16 |
|**Standard_DS4_v2** |8   |28  |56 |32000 |1000 |32 |
|**Standard_DS5_v2** |16  |56  |112|64000 |1000 |64 |
|**Standard_DS11_v2**|2   |14  |28 |8000  |1000 |4  |
|**Standard_DS12_v2**|4   |28  |56 |16000 |1000 |8  |
|**Standard_DS13_v2**|8   |56  |112|32000 |1000 |16 |


자세한 내용은 [Dv2 및 DSv2 시리즈](../articles/virtual-machines/dv2-dsv2-series.md#dv2-series)를 참조 하세요.

### <a name="ncast4_v3-series-preview"></a>NCasT4_v3 시리즈 (미리 보기)

이러한 크기는 장치의 GPU Vm에 대해 지원 되며 계산 집약적 GPU 가속 응용 프로그램에 최적화 되어 있습니다. 이 시리즈는 Nvidia의 Tesla T4 GPU를 갖춘 유추 워크 로드에 초점을 맞추고 있습니다. 

|크기     |vCPU     |메모리(GiB) | 리소스 디스크 크기 (GiB)  |OS 디스크 크기 (GiB)| GPU | GPU 메모리 (GiB) | 최대 NIC 수 |
|---------------------|----|----|-----|-----|-------|--------------|---|
|**Standard_NC4as_T4_v3** |4   |28  |180   |1000|1 |16   |4 |
|**Standard_NC8as_T4_v3** |8   |56  |360   |1000|1 |16  |8 |

자세한 내용은 [NCasT4_v3 시리즈](../articles/virtual-machines/nct4-v3-series.md)를 참조 하세요.

### <a name="f-series"></a>F 시리즈

이러한 시리즈는 계산 워크 로드에 최적화 되 고 Intel Xeon 프로세서에서 실행 됩니다. 

| 크기 | vCPU 수 | 메모리: GiB |리소스 디스크 크기 (GiB) |OS 디스크 크기 (GiB)|  최대 데이터 디스크 수 | 최대 NIC 수 |
|---|---|---|---|---|---|---|
| Standard_F1  | 1  | 2   |16      |1000| 4  |  2 |
| Standard_F2 | 2  | 4 |32      |1000| 8  |  4 |
| Standard_F4  | 4  | 8 |64   |1000| 16 |  4 |
| Standard_F8 | 8 | 16  |132    |1000| 32 |  8 |
| Standard_F16 | 16 | 32  |262   |1000| 64 |  8 |
| Standard_F1s | 1 | 2  | 4  |1000| 4 | 1 |
| Standard_F2s | 2 | 4 |8   |1000| 8 | 4 |
| Standard_F4s | 4 | 8 |16 |1000| 16 |  4 |
| Standard_F8s | 8 | 16 |32 |1000| 32 |  8 |
| Standard_F16s | 16 | 32 |64 |1000| 64 |  8 |

자세한 내용은 [Fsv2 시리즈](../articles/virtual-machines/fsv2-series.md)를 참조 하세요.

