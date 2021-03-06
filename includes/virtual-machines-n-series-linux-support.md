---
title: 파일 포함
description: 파일 포함
services: virtual-machines-linux
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 02/11/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 4bfac9be5041fdf4ebfe7ea56f064b8b85806703
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98860127"
---
## <a name="supported-distributions-and-drivers"></a>지원되는 배포판 및 버전

### <a name="nvidia-cuda-drivers"></a>NVIDIA CUDA 드라이버

NC, NCv2, NCv3, ND 및 NDv2 시리즈 VM(NV 시리즈의 경우 선택적임)용 NVIDIA CUDA 드라이버는 다음 표에 나와 있는 Linux 배포판에서만 사용할 수 있습니다. CUDA 드라이버 정보는 게시 시점의 최신 정보입니다. 최신 verda 드라이버와 지원 되는 운영 체제는 [NVIDIA](https://developer.nvidia.com/cuda-zone) 웹 사이트를 참조 하세요. 배포에 필요한 최신 CUDA 드라이버를 설치 또는 업그레이드해야 합니다. 

> [!TIP]
> Linux VM에서 수동 CUDA 드라이버를 설치하는 대신, Azure [Data Science Virtual Machine](../articles/machine-learning/data-science-virtual-machine/overview.md) 이미지를 배포할 수 있습니다. Ubuntu 16.04 LTS 또는 CentOS 7.4용 DSVM 에디션은 NVIDIA CUDA 드라이버, CUDA 심층 신경망 네트워크 라이브러리 및 기타 도구를 사전 설치합니다.


### <a name="nvidia-grid-drivers"></a>NVIDIA GRID 드라이버

Microsoft는 가상 워크스테이션 또는 가상 응용 프로그램에 사용 되는 NV 및 NVv3 시리즈 Vm에 대 한 NVIDIA GRID 드라이버 설치 관리자를 재배포 합니다. Azure NV VM에서 다음 표에 나열된 운영 체제에만 이러한 GRID 드라이버를 설치합니다. 이 드라이버에는 Azure의 GRID 가상 GPU 소프트웨어에 대한 라이선스가 포함됩니다. NVIDIA vGPU 소프트웨어 라이선스 서버를 설정할 필요는 없습니다.

Azure에서 재배포 하는 그리드 드라이버는 NC, NCv2, NCv3, ND 및 NDv2 시리즈 Vm과 같은 NV 이외의 시리즈 Vm에서는 작동 하지 않습니다.

|배포|드라이버|
| --- | -- |
|Ubuntu 18.04 LTS<br/><br/>Ubuntu 16.04 LTS<br/><br/>7.7 Red Hat Enterprise Linux 7.9, 8.0, 8.1<br/><br/>SUSE Linux Enterprise Server 12 SP2 <br/><br/>SUSE Linux Enterprise Server 15 SP2 | NVIDIA GRID 12.0, driver branch [R460](https://go.microsoft.com/fwlink/?linkid=874272)(.exe)|

이전 Nvidia 그리드 드라이버 링크의 전체 목록은 [GitHub](https://github.com/Azure/azhpc-extensions/blob/master/NvidiaGPU/resources.json) 를 참조 하세요.

> [!WARNING] 
> Red Hat 제품에 대한 타사 소프트웨어 설치는 Red Hat 지원 조건에 영향을 줄 수 있습니다. [Red Hat 기술 자료 문서](https://access.redhat.com/articles/1067)를 참조하세요.
>
