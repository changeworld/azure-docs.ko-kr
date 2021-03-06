---
title: Text Analytics v3에서 모델 버전 지정
titleSuffix: Azure Cognitive Services
description: 데이터에 사용 되는 텍스트 분석 API 모델을 지정 하는 방법에 대해 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 02/17/2021
ms.author: aahi
ms.openlocfilehash: ee3a56fed87f493d79796c0d91e5324c94e87eca
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104599360"
---
# <a name="model-versioning-in-the-text-analytics-api"></a>텍스트 분석 API의 모델 버전 관리

텍스트 분석 API 버전 3을 사용 하 여 데이터에 사용 되는 모델 버전을 선택할 수 있습니다. 선택적 `model-version` 매개 변수를 사용 하 여 API 요청에서 모델의 버전을 선택 합니다. 예를 들어 `<resource-url>/text/analytics/v3.0/sentiment?model-version=2020-04-01`을 참조하십시오. 이 매개 변수를 지정 하지 않으면 API는 기본적으로 안정적인 최신 버전으로 지정 됩니다. 

## <a name="available-versions"></a>사용 가능한 버전

아래 표를 사용 하 여 각 호스팅된 끝점에서 지원 되는 모델 버전을 찾을 수 있습니다.


| 엔드포인트                        | 지원되는 버전                                     | 최신 버전 |
|---------------------------------|--------------------------------------------------------|----------------|
| `/sentiment`                    | `2019-10-01`, `2020-04-01`                             | `2020-04-01`   |
| `/languages`                    | `2019-10-01`, `2020-07-01`, `2020-09-01`, `2021-01-05` | `2021-01-05`   |
| `/entities/linking`             | `2019-10-01`, `2020-02-01`                             | `2020-02-01`   |
| `/entities/recognition/general` | `2019-10-01`, `2020-02-01`, `2020-04-01`,`2021-01-15`  | `2021-01-15`   |
| `/entities/recognition/pii`     | `2019-10-01`, `2020-02-01`, `2020-04-01`,`2020-07-01`, `2021-01-15`  | `2021-01-15`   |
| `/entities/health`              | `2021-03-01`                           | `2021-03-01`   |
| `/keyphrases`                   | `2019-10-01`, `2020-07-01`                             | `2020-07-01`   |


이러한 모델에 대 한 업데이트에 대 한 세부 정보는 [새로운 기능](../whats-new.md)에서 찾을 수 있습니다.

## <a name="text-analytics-for-health"></a>의료 분야 Text Analytics

[상태 컨테이너의 Text Analytics](../how-tos/text-analytics-for-health.md) 는 위의 API 끝점과 별도의 모델 버전 관리를 사용 합니다.  컨테이너 이미지 마다 하나의 모델 버전만 사용할 수 있습니다.

| 엔드포인트                        | 컨테이너 이미지 태그                     | 모델 버전 |
|---------------------------------|-----------------------------------------|---------------|
| `/entities/health`              | `3.0.015370001-onprem-amd64` 또는 최신          | `2021-03-01`  |
| `/entities/health`              | `1.1.013530001-amd64-preview`           | `2020-09-03`  |
| `/entities/health`              | `1.1.013150001-amd64-preview`           | `2020-07-24`  |
| `/domains/health`               | `1.1.012640001-amd64-preview`           | `2020-05-08`  |
| `/domains/health`               | `1.1.012420001-amd64-preview`           | `2020-05-08`  |
| `/domains/health`               | `1.1.012070001-amd64-preview`           | `2020-04-16`  |


## <a name="next-steps"></a>다음 단계

* [Text Analytics 개요](../overview.md)
* [감정 분석](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [엔터티 인식](../how-tos/text-analytics-how-to-entity-linking.md)
