---
title: One vs-One 다중 클래스
titleSuffix: Azure Machine Learning
description: Azure Machine Learning에서 한 vs-One 다중 클래스 모듈을 사용 하 여 이진 분류 모델 앙상블에서 다중 클래스 분류 모델을 만드는 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/13/2020
ms.openlocfilehash: 92ef5b372113d0d226335b8ec464a75a5a684bc3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94592909"
---
# <a name="one-vs-one-multiclass"></a>One vs-One 다중 클래스

이 문서에서는 Azure Machine Learning 디자이너에서 한 vs-One 다중 클래스 모듈을 사용 하는 방법을 설명 합니다. 목표 *는 한 가지 방법을 사용* 하 여 여러 클래스를 예측할 수 있는 분류 모델을 만드는 것입니다.

이 모듈은 결과가 연속 또는 범주 예측 변수에 의존하는 경우 세 개 이상의 가능한 결과를 예측하는 모델을 만드는 데 유용합니다. 이 메서드를 사용하면 여러 출력 클래스가 필요한 문제에 대해서도 이진 분류 방법을 사용할 수 있습니다.

### <a name="more-about-one-versus-one-models"></a>하나 이상의 모델에 대 한 자세한 정보

일부 분류 알고리즘은 설계에 따라 세 개 이상의 클래스를 사용할 수 있도록 합니다. 다른 값은 두 값 (이진 또는 2 클래스 모델) 중 하나로 가능한 결과를 제한 합니다. 하지만 다양 한 전략을 통해 다중 클래스 분류 태스크에 대 한 이진 분류 알고리즘을 적용할 수도 있습니다. 

이 모듈은 클래스 쌍 마다 이진 모델이 만들어지는 일 대 일 메서드를 구현 합니다. 예측 시 가장 많은 투표를 받은 클래스가 선택 됩니다. `n_classes * (n_classes - 1) / 2`이 메서드는 분류자에 맞아야 하므로 일반적으로 O (n_classes ^ 2) 복잡성으로 인해 1 ~ 모두 보다 느립니다. 그러나이 방법은에서 잘 확장 되지 않는 커널 알고리즘과 같은 알고리즘에 유용할 수 있습니다 `n_samples` . 각 개별 학습 문제는 데이터의 작은 하위 집합만 포함 하는 반면, 1 대 모두를 사용 하는 경우 전체 데이터 집합을 사용 합니다 `n_classes` .

기본적으로이 모듈은 개별 모델의 앙상블을 만든 다음 결과를 병합 하 여 모든 클래스를 예측 하는 단일 모델을 만듭니다. 모든 이진 분류자는 일 대 일 모델의 기준으로 사용할 수 있습니다.  

예를 들어 [2 클래스 지원 벡터 컴퓨터](two-class-support-vector-machine.md) 모델을 구성 하 고이를 일대일 다중 클래스 모듈에 대 한 입력으로 제공 한다고 가정해 보겠습니다. 모듈은 출력 클래스의 모든 멤버에 대해 2 클래스 지원 벡터 컴퓨터 모델을 만듭니다. 그런 다음, 일 대 일 메서드를 적용 하 여 모든 클래스에 대 한 결과를 결합 합니다.  

이 모듈은 OneVsOneClassifier를 사용 하 여 [자세한 내용을 볼 수 있습니다.](https://scikit-learn.org/stable/modules/generated/sklearn.multiclass.OneVsOneClassifier.html)

## <a name="how-to-configure-the-one-vs-one-multiclass-classifier"></a>One vs 다중 클래스 분류자를 구성 하는 방법  

이 모듈은 여러 클래스를 분석 하는 이진 분류 모델의 앙상블을 만듭니다. 이 모듈을 사용 하려면 먼저 *이진 분류* 모델을 구성 하 고 학습 해야 합니다. 

이진 모델을 One vs 다중 클래스 모듈에 연결 합니다. 그런 다음 레이블 학습 데이터 집합을 사용 하 여 [모델 학습](train-model.md) 을 사용 하 여 모델의 앙상블을 학습 합니다.

모델을 결합 하는 경우 일대일 다중 클래스는 여러 이진 분류 모델을 만들고 각 클래스에 대 한 알고리즘을 최적화 한 다음 모델을 병합 합니다. 이 모듈은 학습 데이터 집합에 여러 클래스 값이 있을 수 있는 경우에도이 작업을 수행 합니다.

1. 하나-vs-One 다중 클래스 모듈을 디자이너의 파이프라인에 추가 합니다. 이 모듈은 **분류** 범주의 **Machine Learning-Initialize** 에서 찾을 수 있습니다.

   일대일 다중 클래스 분류자에는 자체의 구성 가능한 매개 변수가 없습니다. 사용자 지정은 입력으로 제공 되는 이진 분류 모델에서 수행 해야 합니다.

2. 파이프라인에 이진 분류 모델을 추가 하 고 해당 모델을 구성 합니다. 예를 들어 [2 클래스 지원 벡터 컴퓨터](two-class-support-vector-machine.md) 또는 [2 클래스 승격 된 의사 결정 트리](two-class-boosted-decision-tree.md)를 사용할 수 있습니다.

3. 파이프라인에 [모델 학습](train-model.md) 모듈을 추가 합니다. 한 vs 다중 클래스의 출력 인 학습 되지 않은 분류자를 연결 합니다.

4. [학습 모델](train-model.md)의 다른 입력에서 여러 클래스 값이 있는 레이블이 지정 된 학습 데이터 집합을 연결 합니다.

5. 파이프라인을 제출합니다.

## <a name="results"></a>결과

학습을 완료 한 후에는 모델을 사용 하 여 다중 클래스 예측을 만들 수 있습니다.

또는 학습 되지 않은 분류자를 전달 하 여 레이블이 지정 된 유효성 검사 데이터 집합에 대 한 교차 유효성 검사를 위한 [모델 교차](cross-validate-model.md) 유효성 검사를 수행할 수 있습니다.


## <a name="next-steps"></a>다음 단계

Azure Machine Learning에서 [사용 가능한 모듈 세트](module-reference.md)를 참조하세요. 
