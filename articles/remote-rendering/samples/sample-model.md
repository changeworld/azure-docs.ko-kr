---
title: 샘플 모델
description: 샘플 모델의 소스를 나열합니다.
author: florianborn71
ms.author: flborn
ms.date: 01/29/2020
ms.topic: sample
ms.openlocfilehash: 6817601659c841ca98031f4e3e1590743bbed171
ms.sourcegitcommit: 7ec45b7325e36debadb960bae4cf33164176bc24
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100530538"
---
# <a name="sample-models"></a>샘플 모델

이 문서에서는 Azure Remote Rendering 서비스를 테스트하는 데 사용할 수 있는 샘플 데이터에 대한 몇 가지 리소스를 나열합니다.

## <a name="built-in-sample-model"></a>기본 제공 샘플 모델

URL **builtin://Engine** 을 사용하여 항상 로드할 수 있는 기본 제공 샘플 모델을 제공합니다.

![샘플 모델](./media/sample-model.png "샘플 모델")

모델 통계:

| 속성 | 값 |
|-----------|:-----------|
| [필요한 서버 크기](../reference/vm-sizes.md) | 표준 |
| 삼각형 수 | 1870만 |
| 이동 가능한 파트 수 | 2073 |
| 재료 수 | 94 |

## <a name="third-party-data"></a>타사 데이터

Khronos 그룹은 테스트를 위한 glTF 샘플 모델 세트를 유지 관리합니다. ARR은 텍스트( *.gltf*)와 이진( *.glb*) 형식 둘 다에서 glTF 형식을 지원합니다. 최상의 시각적 결과를 위해 PBR 모델을 사용하는 것이 좋습니다.

* [glTF 샘플 모델](https://github.com/KhronosGroup/glTF-Sample-Models)

## <a name="next-steps"></a>다음 단계

* [빠른 시작: Unity를 사용하여 모델 렌더링](../quickstarts/render-model.md)
* [빠른 시작: 렌더링을 위해 모델 변환](../quickstarts/convert-model.md)
