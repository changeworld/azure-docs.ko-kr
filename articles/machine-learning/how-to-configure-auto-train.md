---
title: 자동화된 ML 실험 만들기
titleSuffix: Azure Machine Learning
description: 자동화 된 machine learning 실험의 데이터 원본, 계산 및 구성 설정을 정의 하는 방법에 대해 알아봅니다.
author: cartacioS
ms.author: sacartac
ms.reviewer: nibaccam
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.date: 09/29/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python,contperf-fy21q1, automl
ms.openlocfilehash: 24c0d57490ecd039039992310f93ca3e21c47b3b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103563490"
---
# <a name="configure-automated-ml-experiments-in-python"></a>Python에서 자동화된 ML 실험 구성


이 가이드에서는 [Azure Machine Learning SDK](/python/api/overview/azure/ml/intro)를 사용하여 자동화된 Machine Learning 실험의 다양한 구성 설정을 정의하는 방법에 대해 알아봅니다. 자동화된 Machine Learning은 사용자를 위한 알고리즘과 하이퍼 매개 변수를 선택하고 배포할 준비가 된 모델을 생성합니다. 자동화된 Machine Learning 실험을 구성하는 데 사용할 수 있는 옵션에 대해 알아봅니다.

자동화 된 기계 학습 실험의 예를 보려면 [자습서: 자동화 된 machine learning을 사용 하 여 분류 모델 학습](tutorial-auto-train-models.md) 또는 [클라우드에서 자동화 된 machine learning을 사용 하 여 모델 학습](how-to-auto-train-remote.md)을 참조 하세요.

자동화된 기계 학습에서 사용할 수 있는 구성 옵션은 다음과 같습니다.

* 실험 유형 선택: 분류, 회귀 또는 시계열 예측
* 데이터 원본, 형식 및 데이터 가져오기
* 컴퓨팅 대상 선택: 로컬 또는 원격
* 자동화된 Machine Learning 실험 설정
* 자동화된 Machine Learning 실험 실행
* 모델 메트릭 탐색
* 모델 등록 및 배포

코드 없는 환경을 선호하는 경우 [Azure Machine Learning 스튜디오에서 자동화된 Machine Learning 만들기](how-to-use-automated-ml-for-ml-models.md)가 가능합니다.

## <a name="prerequisites"></a>필수 조건

이 문서에는 다음이 필요 합니다. 
* Azure Machine Learning 작업 영역 작업 영역을 만들려면 [Azure Machine Learning 작업 영역 만들기](how-to-manage-workspace.md)를 참조하세요.

* Azure Machine Learning Python SDK가 설치 되어 있습니다.
    SDK를 설치 하려면 다음 중 하나를 수행할 수 있습니다. 
    * SDK를 자동으로 설치 하 고 ML 워크플로에 대해 미리 구성 된 계산 인스턴스를 만듭니다. 자세한 내용은 [Azure Machine Learning 계산 인스턴스 만들기 및 관리](how-to-create-manage-compute-instance.md) 를 참조 하세요. 

    * [패키지를 `automl` 직접 설치](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/README.md#setup-using-a-local-conda-environment)합니다. 여기에는 SDK의 [기본 설치가](/python/api/overview/azure/ml/install#default-install) 포함 됩니다.

## <a name="select-your-experiment-type"></a>실험 유형 선택

실험을 시작하기 전에 해결하려는 기계 학습 문제의 종류를 결정해야 합니다. 자동화 된 machine learning은, 및의 작업 유형을 지원 `classification` `regression` `forecasting` 합니다. [작업 유형](concept-automated-ml.md#when-to-use-automl-classify-regression--forecast)에 대해 자세히 알아보세요.

다음 코드는 `task` 생성자에서 매개 변수를 사용 하 여 `AutoMLConfig` 실험 형식을로 지정 합니다 `classification` .

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task = "classification")
```

## <a name="data-source-and-format"></a>데이터 원본 및 형식

자동화된 Machine Learning은 로컬 데스크톱 또는 Azure Blob Storage와 같은 클라우드의 데이터를 지원합니다. 데이터를 **Pandas 데이터 프레임** 또는 **Azure Machine Learning TabularDataset** 로 읽어 들일 수 있습니다. [데이터 세트](how-to-create-register-datasets.md)에 대해 자세히 알아보세요.

Machine learning에서 데이터 학습을 위한 요구 사항:
- 데이터는 테이블 형식이어야 합니다.
- 예측하려는 값(대상 열)이 데이터에 있어야 합니다.

**원격 실험의** 경우 원격 계산에서 학습 데이터에 액세스할 수 있어야 합니다. AutoML은 원격 계산에서 작업하는 경우에만 [Azure Machine Learning TabularDatasets](/python/api/azureml-core/azureml.data.tabulardataset)를 허용합니다. 

Azure Machine Learning 데이터 세트는 다음과 같은 기능을 보여줍니다.

* 정적 파일 또는 URL 원본에서 작업 영역으로 데이터를 쉽게 전송 합니다.
* 클라우드 컴퓨팅 리소스에서 실행할 때 데이터를 학습 스크립트에 사용 가능 클래스를 사용 하 여 원격 계산 대상에 데이터를 탑재 하는 예제는 데이터 [집합을 사용 하 여 학습 하는 방법](how-to-train-with-datasets.md#mount-files-to-remote-compute-targets) 을 참조 하세요 `Dataset` .

다음 코드는 웹 url에서 TabularDataset를 만듭니다. 로컬 파일 및 데이터 저장소와 같은 다른 원본에서 데이터 집합을 만드는 방법에 대 한 코드 예제는 [create a TabularDatasets](how-to-create-register-datasets.md#create-a-tabulardataset) 을 참조 하세요.

```python
from azureml.core.dataset import Dataset
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"
dataset = Dataset.Tabular.from_delimited_files(data)
  ```
**로컬 계산 실험의** 경우 처리 시간을 단축 하기 위해 pandas 데이터 프레임를 권장 합니다.

  ```python
  import pandas as pd
  from sklearn.model_selection import train_test_split

  df = pd.read_csv("your-local-file.csv")
  train_data, test_data = train_test_split(df, test_size=0.1, random_state=42)
  label = "label-col-name"
  ```

## <a name="training-validation-and-test-data"></a>학습, 유효성 검사 및 테스트 데이터

생성자에 별도의 **학습 데이터 및 유효성 검사 데이터 집합** 을 직접 지정할 수 있습니다 `AutoMLConfig` . AutoML 실험의 [데이터 분할 및 교차 유효성 검사를 구성 하는 방법](how-to-configure-cross-validation-data-splits.md) 에 대해 자세히 알아보세요. 

또는 매개 변수를 명시적으로 지정 하지 않는 경우 `validation_data` `n_cross_validation` 자동화 된 ML은 유효성 검사를 수행 하는 방법을 결정 하는 기본 기술을 적용 합니다. 이러한 결정은 매개 변수에 할당 된 데이터 집합의 행 수에 따라 달라 집니다 `training_data` . 

|학습 &nbsp; 데이터 &nbsp; 크기| 유효성 검사 기술 |
|---|-----|
|**&nbsp; &nbsp; 2만 행 보다 &nbsp; 큼**| 학습/유효성 검사 데이터 분할이 적용 됩니다. 기본값은 유효성 검사 집합으로 초기 학습 데이터 집합의 10%를 차지 하는 것입니다. 그러면이 유효성 검사 집합이 메트릭 계산에 사용 됩니다.
|**&nbsp; &nbsp; 2만 행 보다 &nbsp; 작음**| 교차 유효성 검사 방법이 적용 됩니다. 기본 접기 수는 행 수에 따라 달라 집니다. <br> **데이터 집합의 행이 1000 보다 작은 경우** 에는 10 개의 접기가 사용 됩니다. <br> **행이 1000 ~ 2만 사이인 경우** 세 가지 접기가 사용 됩니다.

지금은 모델 평가를 위한 고유한 **테스트 데이터** 를 제공 해야 합니다. 모델 평가를 위한 고유한 테스트 데이터를 가져오는 코드 예제는 [이 Jupyter 노트북](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-credit-card-fraud/auto-ml-classification-credit-card-fraud.ipynb)의 **테스트** 섹션을 참조 하세요.

## <a name="compute-to-run-experiment"></a>실험 실행 컴퓨팅

다음으로, 모델을 학습할 위치를 결정합니다. 자동화된 Machine Learning 실험은 다음 컴퓨팅 옵션에서 실행할 수 있습니다. [로컬 및 원격 컴퓨팅의 장단점](concept-automated-ml.md#local-remote) 옵션에 대해 알아봅니다. 

* 로컬 데스크톱 또는 랩톱 등의 **로컬** 컴퓨터-일반적으로 작은 데이터 집합이 있고 탐색 단계에 있는 경우입니다. 로컬 계산 예는 [이 Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/local-run-classification-credit-card-fraud/auto-ml-classification-credit-card-fraud-local.ipynb) 을 참조하세요. 
 
* 클라우드의 **원격** 컴퓨터 – 관리 되는 [계산 Azure Machine Learning](concept-compute-target.md#amlcompute) 는 Azure virtual machines의 클러스터에서 기계 학습 모델을 학습 하는 기능을 제공 하는 관리 되는 서비스입니다. 

    Azure Machine Learning Managed Compute를 사용하는 원격 예는 [이 Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb)을 참조하세요. 

* Azure 구독의 **Azure Databricks 클러스터** [자동화 된 ML에 대 한 Azure Databricks 클러스터 설정](how-to-configure-databricks-automl-environment.md)에서 자세한 내용을 확인할 수 있습니다. Azure Databricks를 사용하는 Notebook의 예제는 [이 GitHub 사이트](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-databricks/automl)를 참조하세요.

<a name='configure-experiment'></a>

## <a name="configure-your-experiment-settings"></a>실험 설정 구성

자동화된 Machine Learning 실험을 구성하는 데 사용할 수 있는 옵션에 대해 알아봅니다. 이러한 매개 변수는 `AutoMLConfig` 개체를 인스턴스화하여 설정됩니다. 매개 변수의 전체 목록은 [AutoMLConfig 클래스](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig)를 참조하세요.

일부 사례:

1. AUC 가중치를 기본 메트릭으로 사용하며 실험 제한 시간(분)이 30분으로 설정되고 교차 유효성 검사 접기가 2회인 분류 실험.

   ```python
       automl_classifier=AutoMLConfig(task='classification',
                                      primary_metric='AUC_weighted',
                                      experiment_timeout_minutes=30,
                                      blocked_models=['XGBoostClassifier'],
                                      training_data=train_data,
                                      label_column_name=label,
                                      n_cross_validations=2)
   ```
1. 다음 예제는 5 개의 유효성 검사 교차 접기를 사용 하 여 60 분 후에 종료 되는 회귀 실험을 설정 합니다.

   ```python
      automl_regressor = AutoMLConfig(task='regression',
                                      experiment_timeout_minutes=60,
                                      allowed_models=['KNN'],
                                      primary_metric='r2_score',
                                      training_data=train_data,
                                      label_column_name=label,
                                      n_cross_validations=5)
   ```


1. 예측 태스크에는 추가 설정이 필요 합니다. 자세한 내용은 [시계열 예측 모델 자동 학습](how-to-auto-train-forecast.md) 문서를 참조 하세요. 

    ```python
    time_series_settings = {
        'time_column_name': time_column_name,
        'time_series_id_column_names': time_series_id_column_names,
        'forecast_horizon': n_test_periods
    }
    
    automl_config = AutoMLConfig(task = 'forecasting',
                                 debug_log='automl_oj_sales_errors.log',
                                 primary_metric='normalized_root_mean_squared_error',
                                 experiment_timeout_minutes=20,
                                 training_data=train_data,
                                 label_column_name=label,
                                 n_cross_validations=5,
                                 path=project_folder,
                                 verbosity=logging.INFO,
                                 **time_series_settings)
    ```
    
### <a name="supported-models"></a>지원되는 모델

자동화 된 machine learning은 자동화 및 튜닝 프로세스 중에 다른 모델 및 알고리즘을 시도 합니다. 사용자는 알고리즘을 지정할 필요가 없습니다. 

세 가지 다른 `task` 매개 변수 값에 따라 적용할 알고리즘 또는 모델 목록이 결정 됩니다. `allowed_models` 또는 `blocked_models` 매개 변수를 사용하여 가용 모델을 포함 또는 제외하도록 반복을 추가로 수정합니다. 

다음 표에서는 작업 유형별로 지원 되는 모델을 요약 하 여 보여 줍니다. 

> [!NOTE]
> 자동 ML에서 만든 모델을 [ONNX 모델](concept-onnx.md)로 내보내려는 경우 *가 표시된 알고리즘만 ONNX 형식으로 변환할 수 있습니다. [모델을 ONNX로 변환](concept-automated-ml.md#use-with-onnx)하는 방법에 대해 자세히 알아보세요. <br> <br> ONNX는 현재 분류 및 회귀 작업만 지원한다는 사실도 기억하세요. 

분류 | 회귀 | 시계열 예측
|-- |-- |--
[로지스틱 회귀](https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression)* | [Elastic Net](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)* | [Elastic Net](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)
[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)* |[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)*|[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)
[경사 부스팅](https://scikit-learn.org/stable/modules/ensemble.html#classification)* |[경사 부스팅](https://scikit-learn.org/stable/modules/ensemble.html#regression)* |[경사 부스팅](https://scikit-learn.org/stable/modules/ensemble.html#regression)
[의사 결정 트리](https://scikit-learn.org/stable/modules/tree.html#decision-trees)* |[의사 결정 트리](https://scikit-learn.org/stable/modules/tree.html#regression)* |[Decision Tree](https://scikit-learn.org/stable/modules/tree.html#regression)
[K Nearest Neighbors](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)* |[K Nearest Neighbors](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)* |[K Nearest Neighbors](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)
[선형 SVC](https://scikit-learn.org/stable/modules/svm.html#classification)* |[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)* |[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)
[SVC(Support Vector Classification)](https://scikit-learn.org/stable/modules/svm.html#classification)* |[SGD(Stochastic Gradient Descent)](https://scikit-learn.org/stable/modules/sgd.html#regression)* |[SGD(Stochastic Gradient Descent)](https://scikit-learn.org/stable/modules/sgd.html#regression)
[임의 포리스트](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)* |[임의 포리스트](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)* |[Random Forest](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)
[Extremely Randomized Trees](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)* |[Extremely Randomized Trees](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)* |[Extremely Randomized Trees](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)
[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)* |[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)* | [Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)
[평균 퍼셉트론 분류자](/python/api/nimbusml/nimbusml.linear_model.averagedperceptronbinaryclassifier?preserve-view=true&view=nimbusml-py-latest)|[온라인 그라데이션 하강 회귀 변수](/python/api/nimbusml/nimbusml.linear_model.onlinegradientdescentregressor?preserve-view=true&view=nimbusml-py-latest) |[자동 ARIMA](https://www.alkaline-ml.com/pmdarima/modules/generated/pmdarima.arima.auto_arima.html#pmdarima.arima.auto_arima)
[Naive Bayes](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)* |[고속 선형 회귀 변수](/python/api/nimbusml/nimbusml.linear_model.fastlinearregressor?preserve-view=true&view=nimbusml-py-latest)|[Prophet](https://facebook.github.io/prophet/docs/quick_start.html)
[SGD(Stochastic Gradient Descent)](https://scikit-learn.org/stable/modules/sgd.html#sgd)* ||ForecastTCN
|[선형 SVM 분류자](/python/api/nimbusml/nimbusml.linear_model.linearsvmbinaryclassifier?preserve-view=true&view=nimbusml-py-latest)*||

### <a name="primary-metric"></a>기본 메트릭
`primary metric`매개 변수는 최적화를 위해 모델 학습 중에 사용할 메트릭을 결정 합니다. 선택하는 작업 유형에 따라 선택 가능한 메트릭이 결정되며, 다음 표에서는 각 작업 유형에 유효한 기본 메트릭을 보여줍니다.

자동화 된 기계 학습에 대 한 기본 메트릭을 선택 하는 것은 많은 요인에 따라 다릅니다. 비즈니스 요구를 가장 잘 나타내는 메트릭을 선택 하는 것이 가장 중요 한 고려 사항입니다. 그런 다음 메트릭이 데이터 집합 프로필에 적합 한지 (데이터 크기, 범위, 클래스 분포 등)를 고려 합니다.

[자동화된 Machine Learning 결과 이해](how-to-understand-automated-ml.md)에서 이러한 메트릭의 정의에 대해 알아보세요.

|분류 | 회귀 | 시계열 예측
|--|--|--
|`accuracy`| `spearman_correlation` | `spearman_correlation`
|`AUC_weighted` | `normalized_root_mean_squared_error` | `normalized_root_mean_squared_error`
|`average_precision_score_weighted` | `r2_score` | `r2_score`
|`norm_macro_recall` | `normalized_mean_absolute_error` | `normalized_mean_absolute_error`
|`precision_score_weighted` |

### <a name="primary-metrics-for-classification-scenarios"></a>분류 시나리오에 대 한 기본 메트릭 

Thresholded 메트릭 (예:,, `accuracy` `average_precision_score_weighted` `norm_macro_recall` 및 `precision_score_weighted` )은 매우 작고, 매우 큰 클래스 기울이기 (클래스 불균형)를 포함 하거나, 예상 된 메트릭 값이 0.0 또는 1.0에 매우 근접 한 경우에도 최적화 되지 않을 수 있습니다. 이러한 경우 `AUC_weighted` 기본 메트릭에 대해를 선택 하는 것이 더 적합할 수 있습니다. 자동화 된 machine learning이 완료 되 면 비즈니스 요구에 가장 적합 한 메트릭을 기반으로 우위 모델을 선택할 수 있습니다.

| 메트릭 | 사용 사례 예제 |
| ------ | ------- |
| `accuracy` | 이미지 분류, 감정 분석, 변동 예측 |
| `AUC_weighted` | 사기 감지, 이미지 분류, 변칙 검색/스팸 감지 |
| `average_precision_score_weighted` | 정서 분석 |
| `norm_macro_recall` | 변동 예측 |
| `precision_score_weighted` |  |

### <a name="primary-metrics-for-regression-scenarios"></a>회귀 시나리오에 대 한 기본 메트릭

및와 같은 메트릭은 `r2_score` `spearman_correlation` 값-예측의 눈금이 많은 크기의 주문을 처리 하는 경우 모델의 품질을 더 잘 나타낼 수 있습니다. 예를 들어 많은 사용자가 $20k ~ $10만의 급여를 가지 며 소수 자릿수가 $100M 범위에서 몇 가지 급여를 사용 하 여 매우 높은 경우 

`normalized_mean_absolute_error``normalized_root_mean_squared_error`이 경우 $30k salary를 사용 하는 작업자와 $20M을 사용 하는 작업자에 대해 $20k 예측 오류를 동일 하 게 처리 합니다. 실제로는 $20M 급여에서 $20k off를 예측 하는 것이 매우 가깝습니다 (0.1% 상대적 차이가 작음), $30k에서 $20k off는 닫히지 않습니다 (큰 67% 상대적 차이). `normalized_mean_absolute_error` 및 `normalized_root_mean_squared_error` 는 예측할 값이 비슷한 크기에 있는 경우에 유용 합니다.

| 메트릭 | 사용 사례 예제 |
| ------ | ------- |
| `spearman_correlation` | |
| `normalized_root_mean_squared_error` | 가격 예측 (집/제품/팁), 점수 예측 검토 |
| `r2_score` | 항공 지연, 급여 추정, 버그 해결 시간 |
| `normalized_mean_absolute_error` |  |

### <a name="primary-metrics-for-time-series-forecasting-scenarios"></a>시계열 예측 시나리오에 대 한 기본 메트릭

위의 회귀 메모를 참조 하세요.

| 메트릭 | 사용 사례 예제 |
| ------ | ------- |
| `spearman_correlation` | |
| `normalized_root_mean_squared_error` | 가격 예측 (예측), 재고 최적화, 수요 예측 |
| `r2_score` | 가격 예측 (예측), 재고 최적화, 수요 예측 |
| `normalized_mean_absolute_error` | |

### <a name="data-featurization"></a>데이터 기능화

모든 자동화된 Machine Learning 실험에서, 스케일이 다른 기능에 중요한 특정 알고리즘을 지원할 수 있도록 데이터가 *자동으로 스케일링 및 정규화* 됩니다. 이러한 크기 조정 및 정규화를 기능화 라고 합니다. 자세한 내용 및 코드 예제는 [AutoML에서 기능화](how-to-configure-auto-features.md#) 을 참조 하세요. 

개체에서 실험을 구성할 때 `AutoMLConfig` 설정을 사용 하거나 사용 하지 않도록 설정할 수 있습니다 `featurization` . 다음 표에서는 [AutoMLConfig 개체](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig)의 기능화에 대해 허용 되는 설정을 보여 줍니다. 

|기능화 구성 | Description |
| ------------- | ------------- |
|`"featurization": 'auto'`| 전처리의 일부로 [데이터 가드 레일 및 기능화 단계](how-to-configure-auto-features.md#featurization)가 자동으로 수행된다는 것을 나타냅니다. **기본 설정** 입니다.|
|`"featurization": 'off'`| 기능화 단계를 자동으로 수행 하지 않음을 나타냅니다.|
|`"featurization":`&nbsp;`'FeaturizationConfig'`| 사용자 지정된 기능화 단계를 사용해야 한다는 것을 나타냅니다. [기능화를 사용자 지정하는 방법을 알아보세요](how-to-configure-auto-features.md#customize-featurization).|

> [!NOTE]
> 자동화된 Machine Learning 기능화 단계(기능 정규화, 누락된 데이터 처리, 텍스트를 숫자로 변환 등)는 기본 모델의 일부가 됩니다. 예측에 모델을 사용하는 경우 학습 중에 적용되는 동일한 기능화 단계가 입력 데이터에 자동으로 적용됩니다.

<a name="ensemble"></a>

### <a name="ensemble-configuration"></a> 앙상블 구성

앙상블 모델은 기본적으로 사용 하도록 설정 되며 AutoML 실행에서 최종 실행 반복으로 나타납니다. 현재 **VotingEnsemble** 및 **StackEnsemble** 가 지원 됩니다. 

투표는 가중치 평균을 사용 하는 소프트 투표를 구현 합니다. 스택 구현은 두 계층 구현을 사용 합니다. 여기서 첫 번째 계층은 투표 앙상블 동일한 모델을 사용 하 고 두 번째 계층 모델은 첫 번째 계층에서 최적의 모델 조합을 찾는 데 사용 됩니다. 

ONNX 모델을 사용 **하거나** explainability를 사용 하는 경우에는 스태킹를 사용할 수 없으며 투표만 사용 됩니다.

`enable_voting_ensemble`및 부울 매개 변수를 사용 하 여 앙상블 교육을 사용 하지 않도록 설정할 수 있습니다 `enable_stack_ensemble` .

```python
automl_classifier = AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        experiment_timeout_minutes=30,
        training_data=data_train,
        label_column_name=label,
        n_cross_validations=5,
        enable_voting_ensemble=False,
        enable_stack_ensemble=False
        )
```

기본 앙상블 동작을 변경 하기 위해 개체에서로 제공할 수 있는 여러 기본 인수가 있습니다 `kwargs` `AutoMLConfig` .

> [!IMPORTANT]
>  다음 매개 변수는 AutoMLConfig 클래스의 명시적 매개 변수가 아닙니다. 

* `ensemble_download_models_timeout_sec`: **VotingEnsemble** 및 **StackEnsemble** 모델이 생성되는 동안 이전 자식 실행에서 여러 맞춤 모델이 다운로드됩니다. `AutoMLEnsembleException: Could not find any models for running ensembling` 오류가 발생할 경우 모델 다운로드 시간을 더 길게 해야 할 수도 있습니다. 모델을 병렬로 다운로드하는 기본값은 300초이고 최대 시간 제한은 없습니다. 시간이 더 필요한 경우 이 매개 변수를 300초보다 큰 값으로 구성합니다. 

  > [!NOTE]
  >  시간 제한에 도달했을 때 다운로드된 모델이 있으면 그때까지 다운로드된 모델을 사용하여 앙상블이 진행됩니다. 시간 제한 내에 완료하려면 반드시 모든 모델을 다운로드해야 하는 것은 아닙니다.

다음 매개 변수는 **StackEnsemble** 모델에만 적용됩니다. 

* `stack_meta_learner_type`: 메타 학습자은 개별 이기종 모델의 출력에 대해 학습된 모델입니다. 기본 메타 학습자는 분류 작업인 경우 `LogisticRegression`(또는 교차 유효성 검사를 사용하는 경우 `LogisticRegressionCV`)이고, 회귀/예측 작업인 경우 `ElasticNet`(교차 유효성 검사를 사용하는 경우 `ElasticNetCV`)입니다. 이 매개 변수는 문자열 `LogisticRegression`, `LogisticRegressionCV`, `LightGBMClassifier`, `ElasticNet`, `ElasticNetCV`, `LightGBMRegressor` 또는 `LinearRegression` 중 하나입니다.

* `stack_meta_learner_train_percentage`: (학습의 학습 및 유효성 검사 유형을 선택할 때) 메타 학습자의 학습을 위해 예약할 학습 세트의 비율을 지정합니다. 기본값은 `0.2`입니다. 

* `stack_meta_learner_kwargs`: 메타 학습자의 이니셜라이저에 전달할 선택적 매개 변수입니다. 이러한 매개 변수 및 매개 변수 형식은 해당 모델 생성자의 매개 변수 및 매개 변수 형식을 미러링하며, 모델 생성자에 전달됩니다.

다음 코드는 `AutoMLConfig` 개체에서 사용자 지정 앙상블 동작을 지정하는 예를 보여줍니다.

```python
ensemble_settings = {
    "ensemble_download_models_timeout_sec": 600
    "stack_meta_learner_type": "LogisticRegressionCV",
    "stack_meta_learner_train_percentage": 0.3,
    "stack_meta_learner_kwargs": {
        "refit": True,
        "fit_intercept": False,
        "class_weight": "balanced",
        "multi_class": "auto",
        "n_jobs": -1
    }
}

automl_classifier = AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        experiment_timeout_minutes=30,
        training_data=train_data,
        label_column_name=label,
        n_cross_validations=5,
        **ensemble_settings
        )
```

<a name="exit"></a> 

### <a name="exit-criteria"></a>종료 기준

실험을 종료 하기 위해 AutoMLConfig에서 정의할 수 있는 몇 가지 옵션이 있습니다.

|조건| 설명
|----|----
&nbsp;조건 없음 | 종료 매개 변수를 정의 하지 않으면 기본 메트릭에 대 한 추가 진행률이 표시 되지 않을 때까지 실험을 계속 합니다.
시간이 지난 후 &nbsp; &nbsp; &nbsp; &nbsp;| `experiment_timeout_minutes`설정에서를 사용 하 여 실험을 계속 실행 해야 하는 시간 (분)을 정의 합니다. <br><br> 실험 시간 초과 오류를 방지 하기 위해 열 크기의 행이 1000만를 초과 하는 경우 최소 15 분 또는 60 분이 발생 합니다.
&nbsp;점수에 &nbsp; &nbsp; &nbsp; 도달 했습니다.| `experiment_exit_score`지정 된 기본 메트릭 점수에 도달한 후에는를 사용 하 여 실험을 완료 합니다.

## <a name="run-experiment"></a>실험 실행

자동화된 ML의 경우 실험을 실행하는 데 사용되는 `Workspace`의 명명된 개체인 `Experiment` 개체를 만들 수 있습니다.

```python
from azureml.core.experiment import Experiment

ws = Workspace.from_config()

# Choose a name for the experiment and specify the project folder.
experiment_name = 'Tutorial-automl'
project_folder = './sample_projects/automl-classification'

experiment = Experiment(ws, experiment_name)
```

실행하려는 실험을 제출하고 모델을 생성합니다. `AutoMLConfig`를 `submit` 메서드에 전달하여 모델을 생성합니다.

```python
run = experiment.submit(automl_config, show_output=True)
```

>[!NOTE]
>새 머신에 먼저 종속성이 설치됩니다.  출력이 표시되는 데 최대 10분이 걸릴 수 있습니다.
>`show_output`을 `True`로 설정하면 출력이 콘솔에 표시됩니다.

### <a name="multiple-child-runs-on-clusters"></a>클러스터에서 여러 자식 실행

자동 ML 실험 자식 실행은 이미 다른 실험을 실행 하 고 있는 클러스터에서 수행할 수 있습니다. 그러나 타이밍은 클러스터에 포함 된 노드 수와 해당 노드를 사용 하 여 다른 실험을 실행할 수 있는지 여부에 따라 달라 집니다.

클러스터의 각 노드는 단일 학습 실행을 달성할 수 있는 개별 VM (가상 머신)의 역할을 합니다. 자동화 된 ML의 경우이는 자식 실행을 의미 합니다. 모든 노드가 사용 중이면 새 실험은 대기 중입니다. 그러나 무료 노드가 있는 경우 새 실험은 사용 가능한 노드/v m에서 병렬로 자동화 된 ML 자식 실행을 실행 합니다.

자식 실행을 관리 하 고 수행할 수 있는 경우 실험 당 전용 클러스터를 만들고 실험의 수 `max_concurrent_iterations` 를 클러스터의 노드 수와 일치 시키는 것이 좋습니다. 이러한 방식으로 클러스터의 모든 노드를 동시에 사용 하 여 동시 자식 실행/반복 횟수를 지정할 수 있습니다.

`max_concurrent_iterations`개체에서를 구성 `AutoMLConfig` 합니다. 구성 되지 않은 경우에는 기본적으로 실험 당 하나의 동시 자식 실행/반복만 허용 됩니다.  

## <a name="explore-models-and-metrics"></a>모델 및 메트릭 탐색

Notebook을 사용 중이면 위젯 또는 인라인에서 결과를 볼 수 있습니다. 자세한 내용은 [모델 추적 및 평가](how-to-monitor-view-training-logs.md#monitor-automated-machine-learning-runs)를 참조하세요.

각 실행에 대해 제공 되는 성능 차트 및 메트릭에 대 한 정의 및 예제는 [자동화 된 기계 학습 실험 결과 평가](how-to-understand-automated-ml.md) 를 참조 하세요. 

기능화 요약을 얻고 특정 모델에 추가 된 기능을 이해 하려면 [기능화 투명도](how-to-configure-auto-features.md#featurization-transparency)를 참조 하세요. 

> [!NOTE]
> 자동화 된 ML의 알고리즘에는 정확도와 같은 권장 모델의 최종 메트릭 점수에 약간의 변형이 발생할 수 있는 내재 된 무작위성이 있습니다. 또한 자동화 된 ML은 필요한 경우 학습-테스트 분할, 학습-유효성 검사 분할 또는 교차 유효성 검사와 같은 데이터에 대 한 작업을 수행 합니다. 따라서 동일한 구성 설정 및 기본 메트릭을 사용 하 여 실험을 여러 번 실행 하는 경우 이러한 요인으로 인해 각 실험 최종 메트릭 점수에 변형이 표시 될 수 있습니다. 

## <a name="register-and-deploy-models"></a>모델 등록 및 배포
모델을 등록 하 여 나중에 사용 하기 위해 다시 돌아올 수 있습니다. 

자동화 된 ML 실행에서 모델을 등록 하려면 메서드를 사용 [`register_model()`](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun#register-model-model-name-none--description-none--tags-none--iteration-none--metric-none-) 합니다. 

```Python

best_run, fitted_model = run.get_output()
print(fitted_model.steps)

model_name = best_run.properties['model_name']
description = 'AutoML forecast example'
tags = None

model = remote_run.register_model(model_name = model_name, 
                                  description = description, 
                                  tags = tags)
```


배포 구성을 만들고 웹 서비스에 등록 된 모델을 배포 하는 방법에 대 한 자세한 내용은 모델을 배포 하는 [방법 및 위치](how-to-deploy-and-where.md?tabs=python#define-a-deployment-configuration)를 참조 하세요.

> [!TIP]
> 등록 된 모델의 경우 [Azure Machine Learning studio](https://ml.azure.com)를 통해 한 번 클릭으로 배포를 사용할 수 있습니다. [스튜디오에서 등록 된 모델을 배포 하는 방법을](how-to-use-automated-ml-for-ml-models.md#deploy-your-model)참조 하세요. 
<a name="explain"></a>

## <a name="model-interpretability"></a>모델 해석력

모델 해석력을 통해 모델이 예측을 수행한 이유와 기본 기능 중요도 값을 이해할 수 있습니다. SDK에는 로컬 모델과 배포된 모델의 학습 및 유추 시간에 모델 해석력 기능을 사용할 수 있도록 다양한 패키지가 포함되어 있습니다.

특히 자동화된 Machine Learning 실험 내에서 해석력 기능을 사용하도록 설정하는 방법에 대한 코드 샘플은 [방법](how-to-machine-learning-interpretability-automl.md)을 참조하세요.

자동화된 Machine Learning 외부의 다른 SDK 영역에서 모델 설명 및 기능 중요도를 사용하는 방법에 대한 일반적인 내용은 해석력에 대한 [개념](how-to-machine-learning-interpretability.md) 문서를 참조하세요.

> [!NOTE]
> ForecastTCN 모델은 현재 설명 클라이언트에서 지원 되지 않습니다. 이 모델은 최상의 모델로 반환 되 고 주문형 설명 실행을 지원 하지 않는 경우 설명 대시보드를 반환 하지 않습니다.

## <a name="next-steps"></a>다음 단계

+ [모델 배포 방법 및 위치](how-to-deploy-and-where.md)에 대해 자세히 알아봅니다.

+ [자동화된 Machine Learning을 사용하여 회귀 모델을 학습시키는 방법](tutorial-auto-train-models.md) 또는 [원격 리소스에서 자동화된 Machine Learning을 사용하여 학습하는 방법](how-to-auto-train-remote.md)에 대해 자세히 알아봅니다.

+ 여러 [모델 솔루션 가속기](https://aka.ms/many-models)에서 automl을 사용 하 여 여러 모델을 학습 하는 방법에 대해 알아봅니다.

+ [자동화 된 ML 실험 문제를 해결](how-to-troubleshoot-auto-ml.md)합니다. 
