---
title: Speech Devices SDK 설명서
titleSuffix: Azure Cognitive Services
description: 릴리스 정보는 업데이트, 개선 사항, 버그 수정 및 음성 장치 SDK의 변경에 대 한 로그를 제공 합니다. 이 문서는 각 버전의 음성 장치 SDK로 업데이트 되었습니다.
services: cognitive-services
author: wsturman
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: wellsi
ms.openlocfilehash: f9215469b1436ce9dc95c140e6969366d80d1ced
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105607136"
---
# <a name="release-notes-speech-devices-sdk"></a>릴리스 정보: 음성 장치 SDK

다음 섹션에는 최신 릴리스의 변경 내용이 나와 있습니다.

## <a name="speech-devices-sdk-1150"></a>음성 장치 SDK 1.15.0:

- 새 MA (Microsoft 오디오 스택)로 업그레이드 하 여 beamforming 및 음성에 대 한 노이즈 감소를 개선 했습니다.
- 대상에 따라 이진 크기를 70%까지 줄였습니다.
- [이진 릴리스](https://aka.ms/sdsdk-download-APAudio)를 사용 하는 [Azure Percept Audio](../../azure-percept/overview-azure-percept-audio.md) 지원.
- [SPEECH SDK](./speech-sdk.md) 구성 요소가 1.15.0 버전으로 업데이트 되었습니다. 해당 [릴리스 정보](./releasenotes.md)를 참조하세요.

## <a name="speech-devices-sdk-1110"></a>음성 장치 SDK 1.11.0:

- [임의의 마이크 배열 기 하 도형을](how-to-devices-microphone-array-configuration.md) 지원 하 고 [구성 파일](https://aka.ms/sdsdk-micarray-json)을 통해 작업 각도를 설정 합니다.
- [URBETTER](http://www.urbetter.com/products_56/278.html)지원.
- [음성 도우미 샘플](https://aka.ms/sdsdk-speaker)에서 사용 되는 [GGEC 스피커](https://aka.ms/sdsdk-download-speaker) 에 대 한 이진 파일을 릴리스 했습니다.
- Raspberry Pi 및 유사한 장치에 대해 [LINUX ARM32](https://aka.ms/sdsdk-download-linux-arm32) 및 [linux ARM 64](https://aka.ms/sdsdk-download-linux-arm64) 에 대 한 이진 파일을 릴리스 했습니다.
- [SPEECH SDK](./speech-sdk.md) 구성 요소가 1.11.0 버전으로 업데이트 되었습니다. 해당 [릴리스 정보](./releasenotes.md)를 참조하세요.

## <a name="speech-devices-sdk-190"></a>음성 장치 SDK 1.9.0:

- [Urbetter DDK](https://aka.ms/sdsdk-download-urbetter) (Linux ARM64)에 대 한 초기 이진 파일이 제공 됩니다.
- 이제 roobo v1이 Speech SDK에 Maven를 사용 합니다.
- [SPEECH SDK](./speech-sdk.md) 구성 요소가 1.9.0 버전으로 업데이트 되었습니다. 해당 [릴리스 정보](./releasenotes.md)를 참조하세요.

## <a name="speech-devices-sdk-170"></a>음성 장치 SDK 1.7.0:

- 이제 Linux ARM이 지원 됩니다.
- [Roobo V2 DDK](https://aka.ms/sdsdk-download-roobov2) 의 초기 이진 파일이 제공 됩니다 (Linux ARM64).
- Windows 사용자 `AudioConfig.fromDefaultMicrophoneInput()` 는 또는을 사용 하 여 `AudioConfig.fromMicrophoneInput(deviceName)` 사용할 마이크를 지정할 수 있습니다.
- 라이브러리 크기가 최적화 되었습니다.
- 동일한 음성/의도 인식기 개체를 사용 하 여 다중 전환 인식 지원.
- 인식을 중지 하는 동안 프로세스가 응답을 중지 하는 간헐적 문제를 해결 합니다.
- 이제 샘플 앱에는 파일 형식을 보여 주는 샘플 참가자. 속성 파일이 포함 되어 있습니다.
- [SPEECH SDK](./speech-sdk.md) 구성 요소가 1.7.0 버전으로 업데이트 되었습니다. 해당 [릴리스 정보](./releasenotes.md)를 참조하세요.

## <a name="speech-devices-sdk-160"></a>음성 장치 SDK 1.6.0:

- 일반적인 [샘플 응용 프로그램](./speech-devices-sdk.md) 을 사용 하 여 Windows 및 Linux에서 [Azure Kinect 진한](https://azure.microsoft.com/services/kinect-dk/) 지원
- [SPEECH SDK](./speech-sdk.md) 구성 요소가 1.6.0 버전으로 업데이트 되었습니다. 해당 [릴리스 정보](./releasenotes.md)를 참조하세요.

## <a name="speech-devices-sdk-151"></a>음성 장치 SDK 1.5.1:

- 샘플 앱에 [대화 기록을](./conversation-transcription.md) 포함 합니다.
- [SPEECH SDK](./speech-sdk.md) 구성 요소가 1.5.1 버전으로 업데이트 되었습니다. 해당 [릴리스 정보](./releasenotes.md)를 참조하세요.

## <a name="speech-devices-sdk-150-2019-may-release"></a>음성 장치 SDK 1.5.0:2019-릴리스 수

- 음성 장치 SDK는 이제 GA 이며 더 이상 제어 되는 미리 보기가 아닙니다.
- [SPEECH SDK](./speech-sdk.md) 구성 요소가 1.5.0 버전으로 업데이트 되었습니다. 해당 [릴리스 정보](./releasenotes.md)를 참조하세요.
- 새로운 키워드 기술은 상당한 품질 향상을 제공 하며 주요 변경 내용을 참조 하세요.
- 새 오디오 처리 파이프라인.

**주요 변경 내용**

- 새로운 키워드 기술 덕분에 향상 된 키워드 포털에서 모든 키워드를 다시 만들어야 합니다. 장치에서 이전 키워드를 완전히 제거 하려면 이전 앱을 제거 합니다.
  - adb cognitiveservices account. sdsdkstarterapp를 제거 합니다.

## <a name="speech-devices-sdk-140-2019-apr-release"></a>음성 장치 SDK 1.4.0:2019-Apr 릴리스

- [SPEECH SDK](./speech-sdk.md) 구성 요소가 1.4.0 버전으로 업데이트 되었습니다. 해당 [릴리스 정보](./releasenotes.md)를 참조하세요.

## <a name="speech-devices-sdk-131-2019-mar-release"></a>음성 장치 SDK 1.3.1:2019-3 월 릴리스

- [SPEECH SDK](./speech-sdk.md) 구성 요소가 1.3.1 버전으로 업데이트 되었습니다. 해당 [릴리스 정보](./releasenotes.md)를 참조하세요.
- 업데이트 된 키워드 처리, 주요 변경 내용을 참조 하세요.
- 샘플 응용 프로그램은 음성 인식과 번역을 위한 언어 선택 항목을 추가 합니다.

**주요 변경 내용**

- [키워드를 설치](./custom-keyword-basics.md) 하는 것은 간소화 되었으므로 이제 앱의 일부 이며 장치에 별도로 설치할 필요가 없습니다.
- 키워드 인식이 변경 되었으며 두 개의 이벤트가 지원 됩니다.
  - `RecognizingKeyword,` 음성 결과에 (확인 되지 않은) 키워드 텍스트가 포함 되어 있음을 나타냅니다.
  - `RecognizedKeyword`는 키워드 인식이 지정 된 키워드를 인식 하 여 완료 되었음을 나타냅니다.

## <a name="speech-devices-sdk-110-2018-nov-release"></a>음성 장치 SDK 1.1.0:2018-11 월 릴리스

- [Speech SDK](./speech-sdk.md) 구성 요소가 1.1.0 버전으로 업데이트되었습니다. 해당 [릴리스 정보](./releasenotes.md)를 참조하세요.
- 향상된 오디오 처리 알고리즘으로 원거리 음성 인식 정확도가 향상되었습니다.
- 샘플 애플리케이션에 중국어 음성 인식 지원이 추가되었습니다.

## <a name="speech-devices-sdk-101-2018-oct-release"></a>음성 장치 SDK 1.0.1:2018-10 월 릴리스

- [Speech SDK](./speech-sdk.md) 구성 요소가 1.0.1 버전으로 업데이트되었습니다. 해당 [릴리스 정보](./releasenotes.md)를 참조하세요.
- 향상된 오디오 처리 알고리즘을 사용하여 음성 인식 정확도가 향상됩니다.
- 연속 인식 오디오 세션 버그 하나가 해결되었습니다.

**주요 변경 내용**

- 이 릴리스에는 몇 가지 주요한 변경 내용이 도입되었습니다. API와 관련된 자세한 내용은 [이 페이지](https://aka.ms/csspeech/breakingchanges_1_0_0)를 확인하세요.
- 키워드 인식 모델 파일은 음성 장치 SDK 1.0.1와 호환 되지 않습니다. 새 키워드 파일이 장치에 기록 된 후 기존 키워드 파일이 삭제 됩니다.

## <a name="speech-devices-sdk-050-2018-aug-release"></a>음성 장치 SDK 0.5.0:2018-8 월 릴리스

- 오디오 처리 코드에서 버그를 수정하여 음성 인식 정확도를 향상했습니다.
- [Speech SDK](./speech-sdk.md) 구성 요소가 0.5.0 버전으로 업데이트되었습니다. 해당 [릴리스 정보](releasenotes.md#cognitive-services-speech-sdk-050-2018-july-release)를 참조하세요.

## <a name="speech-devices-sdk-0212733-2018-may-release"></a>음성 장치 SDK 0.2.12733:2018-릴리스 수

Cognitive Services Speech Devices SDK의 첫 번째 공개 미리 보기 릴리스입니다.