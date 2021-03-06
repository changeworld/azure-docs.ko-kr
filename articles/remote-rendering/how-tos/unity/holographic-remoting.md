---
title: Unity에서 Holographic 원격 및 Remote Rendering 사용
description: Holographic Remoting 미리 보기를 Azure 원격 렌더링과 함께 사용할 수 있는 방법
author: christophermanthei
ms.author: chmant
ms.date: 03/23/2020
ms.topic: how-to
ms.openlocfilehash: bd69710b6e4404a76d3cca385b6c07ea7c1f3f5c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92201820"
---
# <a name="use-holographic-remoting-and-remote-rendering-in-unity"></a>Unity에서 Holographic 원격 및 Remote Rendering 사용

[Holographic Remoting](/windows/mixed-reality/holographic-remoting-player) 과 Azure 원격 렌더링은 하나의 응용 프로그램 내에서 함께 사용할 수 없습니다. 따라서 [Unity 재생 모드](/windows/mixed-reality/unity-play-mode) 도 사용할 수 없습니다.

Unity 편집기를 실행할 때마다 둘 중 하나만 사용할 수 있습니다. 다른 항목을 사용 하려면 먼저 Unity를 다시 시작 합니다.

## <a name="use-unity-play-mode-to-preview-on-hololens-2"></a>Unity 재생 모드를 사용 하 여 HoloLens 2에서 미리 보기

 응용 프로그램의 UI를 테스트 하는 등의 방법으로 Unity 재생 모드를 계속 사용할 수 있습니다. 그러나 ARR을 초기화 하지 않는 것이 중요 합니다. 그렇지 않으면 작동이 중단 됩니다.

## <a name="use-a-wmr-vr-headset-to-preview-on-desktop"></a>WMR VR 헤드셋을 사용 하 여 데스크톱에서 미리 보기

Windows Mixed Reality VR 헤드셋이 있는 경우이를 사용 하 여 Unity 내에서 미리 볼 수 있습니다. 이 경우 ARR을 초기화 해도 괜찮습니다. WMR 헤드셋이 사용 되는 동안에는 세션에 연결할 수 없습니다.