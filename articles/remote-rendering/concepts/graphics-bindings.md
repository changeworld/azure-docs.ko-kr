---
title: 그래픽 바인딩
description: 그래픽 바인딩 및 사용 사례 설정
author: florianborn71
manager: jlyons
services: azure-remote-rendering
titleSuffix: Azure Remote Rendering
ms.author: flborn
ms.date: 12/11/2019
ms.topic: conceptual
ms.service: azure-remote-rendering
ms.custom: devx-track-csharp
ms.openlocfilehash: 69bcc521b4cd00320a5fbecc5244e913ac16c68b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99593911"
---
# <a name="graphics-binding"></a>그래픽 바인딩

사용자 지정 애플리케이션에서 Azure Remote Rendering을 사용할 수 있으려면 애플리케이션의 렌더링 파이프라인에 통합되어 있어야 합니다. 이 통합은 그래픽 바인딩의 책임입니다.

일단 설정되면 그래픽 바인딩은 렌더링된 이미지에 영향을 주는 다양한 함수에 대한 액세스를 제공합니다. 이러한 함수는 항상 사용할 수 있는 일반 함수와 선택한 `Microsoft.Azure.RemoteRendering.GraphicsApiType`에만 관련된 특정 함수의 두 가지 범주로 구분할 수 있습니다.

## <a name="graphics-binding-in-unity"></a>Unity의 그래픽 바인딩

Unity에서 전체 바인딩은 `RemoteManagerUnity.InitializeManager`에 전달된 `RemoteUnityClientInit` 구조체에 의해 처리됩니다. 그래픽 모드를 설정하려면 `GraphicsApiType` 필드를 선택한 바인딩으로 설정해야 합니다. XRDevice가 있는지에 따라 필드가 자동으로 채워집니다. 다음 동작을 사용하여 동작을 수동으로 재정의할 수 있습니다.

* **HoloLens 2**: [Windows Mixed Reality](#windows-mixed-reality) 그래픽 바인딩이 항상 사용됩니다.
* **플랫 UWP 데스크톱 앱**: [시뮬레이션](#simulation)이 항상 사용됩니다.
* **Unity 편집기**: WMR VR 헤드셋이 연결되어 있지 않는 한 [시뮬레이션](#simulation)이 항상 사용됩니다. 이 경우 ARR은 애플리케이션의 비 ARR 관련 부분을 디버그할 수 있도록 사용하지 않도록 설정됩니다. 또한 [Holographic Remoting](../how-tos/unity/holographic-remoting.md)을 참조하세요.

Unity와 관련된 유일한 다른 관련 부분은 [기본 바인딩](#access)에 액세스하는 것입니다. 아래의 다른 모든 섹션을 건너뛸 수 있습니다.

## <a name="graphics-binding-setup-in-custom-applications"></a>사용자 지정 애플리케이션의 그래픽 바인딩 설정

그래픽 바인딩을 선택하려면 다음 두 단계를 수행합니다. 먼저 그래픽 바인딩은 프로그램이 초기화될 때 정적으로 초기화되어야 합니다.

```cs
RemoteRenderingInitialization managerInit = new RemoteRenderingInitialization();
managerInit.GraphicsApi = GraphicsApiType.WmrD3D11;
managerInit.ConnectionType = ConnectionType.General;
managerInit.Right = ///...
RemoteManagerStatic.StartupRemoteRendering(managerInit);
```

```cpp
RemoteRenderingInitialization managerInit;
managerInit.GraphicsApi = GraphicsApiType::WmrD3D11;
managerInit.ConnectionType = ConnectionType::General;
managerInit.Right = ///...
StartupRemoteRendering(managerInit); // static function in namespace Microsoft::Azure::RemoteRendering

```

위의 호출은 Azure Remote Rendering을 holographic API로 초기화하는 데 필요합니다. 이 함수는 holographic API를 호출하고 다른 Remote Rendering API에 액세스하기 전에 호출해야 합니다. 마찬가지로 holographic API가 더 이상 호출되지 않은 후에는 해당 de-init 함수 `RemoteManagerStatic.ShutdownRemoteRendering();`을 호출해야 합니다.

## <a name="span-idaccessaccessing-graphics-binding"></a><span id="access">그래픽 바인딩 액세스

클라이언트를 설정하면 기본 그래픽 바인딩에 `RenderingSession.GraphicsBinding` getter를 사용하여 액세스할 수 있습니다. 예를 들어 마지막 프레임 통계는 다음과 같이 검색할 수 있습니다.

```cs
RenderingSession currentSession = ...;
if (currentSession.GraphicsBinding != null)
{
    FrameStatistics frameStatistics;
    if (currentSession.GraphicsBinding.GetLastFrameStatistics(out frameStatistics) == Result.Success)
    {
        ...
    }
}
```

```cpp
ApiHandle<RenderingSession> currentSession = ...;
if (ApiHandle<GraphicsBinding> binding = currentSession->GetGraphicsBinding())
{
    FrameStatistics frameStatistics;
    if (binding->GetLastFrameStatistics(&frameStatistics) == Result::Success)
    {
        ...
    }
}
```

## <a name="graphic-apis"></a>Graphic API

현재 선택할 수 있는 두 개의 그래픽 API, `WmrD3D11` 및 `SimD3D11`이 있습니다. 세 번째 `Headless`가 있지만 아직 클라이언트 쪽에서 지원되지 않습니다.

### <a name="windows-mixed-reality"></a>Windows Mixed Reality

`GraphicsApiType.WmrD3D11`은 HoloLens 2에서 실행되는 기본 바인딩입니다. `GraphicsBindingWmrD3d11` 바인딩이 만들어집니다. 이 모드에서 Azure Remote Rendering은 holographic API에 직접 후크됩니다.

파생 그래픽 바인딩에 액세스하려면 기본 `GraphicsBinding`을 캐스팅해야 합니다.
WMR 바인딩을 사용하려면 다음 두 가지 작업을 수행해야 합니다.

#### <a name="inform-remote-rendering-of-the-used-coordinate-system"></a>사용된 좌표계의 Remote Rendering 알림

```cs
RenderingSession currentSession = ...;
IntPtr ptr = ...; // native pointer to ISpatialCoordinateSystem
GraphicsBindingWmrD3d11 wmrBinding = (currentSession.GraphicsBinding as GraphicsBindingWmrD3d11);
if (wmrBinding.UpdateUserCoordinateSystem(ptr) == Result.Success)
{
    ...
}
```

```cpp
ApiHandle<RenderingSession> currentSession = ...;
void* ptr = ...; // native pointer to ISpatialCoordinateSystem
ApiHandle<GraphicsBindingWmrD3d11> wmrBinding = currentSession->GetGraphicsBinding().as<GraphicsBindingWmrD3d11>();
if (wmrBinding->UpdateUserCoordinateSystem(ptr) == Result::Success)
{
    //...
}
```

위의 `ptr`은 API의 좌표가 표시되는 세계 좌표 시스템을 정의하는 네이티브 `ABI::Windows::Perception::Spatial::ISpatialCoordinateSystem` 개체에 대한 포인터여야 합니다.

#### <a name="render-remote-image"></a>원격 이미지 렌더링

각 프레임의 시작 부분에서 원격 프레임을 백 버퍼로 렌더링 해야 합니다. 이 작업은 `BlitRemoteFrame` 두 눈동자에 대 한 색 및 깊이 정보를 모두 현재 바인딩된 렌더링 대상에 채우는를 호출 하 여 수행 됩니다. 따라서 전체 백 버퍼를 렌더링 대상으로 바인딩한 후에이 작업을 수행 하는 것이 중요 합니다.

> [!WARNING]
> 원격 이미지가 backbuffer로 array.blit 면 **SV_RenderTargetArrayIndex** 를 사용 하는 것과 같이 단일 패스 스테레오 렌더링 기술을 사용 하 여 로컬 콘텐츠를 렌더링 해야 합니다. 별도의 단계에서 각 눈동자를 렌더링 하는 것과 같은 다른 스테레오 렌더링 기술을 사용 하는 경우에는 심각한 성능 저하 나 그래픽 아티팩트가 발생할 수 있으므로 피해 야 합니다.

```cs
RenderingSession currentSession = ...;
GraphicsBindingWmrD3d11 wmrBinding = (currentSession.GraphicsBinding as GraphicsBindingWmrD3d11);
wmrBinding.BlitRemoteFrame();
```

```cpp
ApiHandle<RenderingSession> currentSession = ...;
ApiHandle<GraphicsBindingWmrD3d11> wmrBinding = currentSession->GetGraphicsBinding().as<GraphicsBindingWmrD3d11>();
wmrBinding->BlitRemoteFrame();
```

### <a name="simulation"></a>시뮬레이션

`GraphicsApiType.SimD3D11`은 시뮬레이션 바인딩이며 선택하는 경우 `GraphicsBindingSimD3d11` 그래픽 바인딩을 만듭니다. 이 인터페이스는 데스크톱 애플리케이션에서와 같이 헤드 이동을 시뮬레이션하는 데 사용되며 Monoscopic 이미지를 렌더링합니다.

시뮬레이션 바인딩을 구현 하려면 [카메라](../overview/features/camera.md) 페이지에 설명 된 대로 로컬 카메라와 원격 프레임 간의 차이점을 이해 하는 것이 중요 합니다.

두 개의 카메라가 필요 합니다.

* **로컬 카메라**:이 카메라는 응용 프로그램 논리에 의해 구동 되는 현재 카메라 위치를 나타냅니다.
* **프록시 카메라**:이 카메라는 서버에서 보낸 현재 *원격 프레임과* 일치 합니다. 프레임 및 도착을 요청 하는 클라이언트 사이에는 시간이 지연 되므로 *원격 프레임* 은 항상 로컬 카메라의 이동 보다 약간 뒤에 있습니다.

여기서 기본적인 방식은 원격 이미지와 로컬 콘텐츠가 모두 프록시 카메라를 사용 하 여 오프 스크린 대상으로 렌더링 된다는 것입니다. 그런 다음 프록시 이미지를 로컬 카메라 공간으로 다시 프로젝션 합니다 .이에 대해서는 [후기 단계 다시 프로젝션](../overview/features/late-stage-reprojection.md)에 자세히 설명 되어 있습니다.

`GraphicsApiType.SimD3D11` 는 아래 설정 호출 중에 사용 하도록 설정 해야 하는 stereoscopic 렌더링도 지원 `InitSimulation` 합니다. 설정은 약간 더 복잡하며 다음과 같이 작동합니다.

#### <a name="create-proxy-render-target"></a>프록시 렌더링 대상 만들기

`GraphicsBindingSimD3d11.Update` 함수에서 제공하는 프록시 카메라 데이터를 사용하여 원격 및 로컬 콘텐츠를 '프록시'라는 오프스크린 색상/깊이 렌더링 대상으로 렌더링해야 합니다.

프록시는 백 버퍼의 확인과 일치 해야 하며 *DXGI_FORMAT_R8G8B8A8_UNORM* 또는 *DXGI_FORMAT_B8G8R8A8_UNORM* 형식 이어야 합니다. Stereoscopic 렌더링의 경우 색 프록시 질감 및 깊이가 사용 되는 경우 깊이 프록시 질감에 두 개의 배열 계층이 아닌 두 개의 배열 계층이 있어야 합니다. 세션이 준비되면 연결하기 전에 `GraphicsBindingSimD3d11.InitSimulation`을 호출해야 합니다.

```cs
RenderingSession currentSession = ...;
IntPtr d3dDevice = ...; // native pointer to ID3D11Device
IntPtr color = ...; // native pointer to ID3D11Texture2D
IntPtr depth = ...; // native pointer to ID3D11Texture2D
float refreshRate = 60.0f; // Monitor refresh rate up to 60hz.
bool flipBlitRemoteFrameTextureVertically = false;
bool flipReprojectTextureVertically = false;
bool stereoscopicRendering = false;
GraphicsBindingSimD3d11 simBinding = (currentSession.GraphicsBinding as GraphicsBindingSimD3d11);
simBinding.InitSimulation(d3dDevice, depth, color, refreshRate, flipBlitRemoteFrameTextureVertically, flipReprojectTextureVertically, stereoscopicRendering);
```

```cpp
ApiHandle<RenderingSession> currentSession = ...;
void* d3dDevice = ...; // native pointer to ID3D11Device
void* color = ...; // native pointer to ID3D11Texture2D
void* depth = ...; // native pointer to ID3D11Texture2D
float refreshRate = 60.0f; // Monitor refresh rate up to 60hz.
bool flipBlitRemoteFrameTextureVertically = false;
bool flipReprojectTextureVertically = false;
bool stereoscopicRendering = false;
ApiHandle<GraphicsBindingSimD3d11> simBinding = currentSession->GetGraphicsBinding().as<GraphicsBindingSimD3d11>();
simBinding->InitSimulation(d3dDevice, depth, color, refreshRate, flipBlitRemoteFrameTextureVertically, flipReprojectTextureVertically, stereoscopicRendering);
```

init 함수에는 네이티브 d3d 디바이스 및 프록시 렌더링 대상의 색상 및 깊이 텍스처에 대한 포인터를 함께 제공해야 합니다. 초기화되면 `RenderingSession.ConnectAsync` 및 `Disconnect`을 여러 번 호출할 수 있지만 다른 세션으로 전환할 때 다른 세션에서 `GraphicsBindingSimD3d11.InitSimulation`을 호출하려면 먼저 이전 세션에서 `GraphicsBindingSimD3d11.DeinitSimulation`을 먼저 호출해야 합니다.

#### <a name="render-loop-update"></a>렌더링 루프 업데이트

렌더링 루프 업데이트는 여러 단계로 구성됩니다.

1. 렌더링을 수행하기 전에 각 프레임은 렌더링 되는 서버에 전송되는 현재 카메라 변환으로 `GraphicsBindingSimD3d11.Update`가 호출됩니다. 동시에 반환된 프록시 변환이 프록시 카메라에 적용되어 프록시 렌더링 대상으로 렌더링되어야 합니다.
반환된 프록시 업데이트 `SimulationUpdate.frameId`가 null이면 아직 원격 데이터가 없습니다. 이 경우 프록시 렌더링 대상으로 렌더링하는 대신, 현재 카메라 데이터를 사용하여 로컬 콘텐츠를 백 버퍼에 직접 렌더링하고 다음 두 단계를 건너뜁니다.
1. 애플리케이션은 이제 프록시 렌더링 대상을 바인딩하고 `GraphicsBindingSimD3d11.BlitRemoteFrameToProxy`를 호출해야 합니다. 그러면 원격 색상 및 깊이 정보가 프록시 렌더링 대상에 채워집니다. 이제 프록시 카메라 변환을 사용하여 모든 로컬 콘텐츠를 프록시에 렌더링할 수 있습니다.
1. 그런 다음 백 버퍼는 렌더링 대상으로 바인딩되어야 하고 백 버퍼를 표시할 수 있는 지점에서 `GraphicsBindingSimD3d11.ReprojectProxy`가 호출됩니다.

```cs
RenderingSession currentSession = ...;
GraphicsBindingSimD3d11 simBinding = (currentSession.GraphicsBinding as GraphicsBindingSimD3d11);
SimulationUpdateParameters updateParameters = new SimulationUpdateParameters();
// Fill out camera data with current camera data
// (see "Simulation Update structures" section below)
...
SimulationUpdateResult updateResult = new SimulationUpdateResult();
simBinding.Update(updateParameters, out updateResult);
// Is the frame data valid?
if (updateResult.FrameId != 0)
{
    // Bind proxy render target
    simBinding.BlitRemoteFrameToProxy();
    // Use proxy camera data to render local content
    ...
    // Bind back buffer
    simBinding.ReprojectProxy();
}
else
{
    // Bind back buffer
    // Use current camera data to render local content
    ...
}
```

```cpp
ApiHandle<RenderingSession> currentSession;
ApiHandle<GraphicsBindingSimD3d11> simBinding = currentSession->GetGraphicsBinding().as<GraphicsBindingSimD3d11>();

SimulationUpdateParameters updateParameters;
// Fill out camera data with current camera data
// (see "Simulation Update structures" section below)
...
SimulationUpdateResult updateResult;
simBinding->Update(updateParameters, &updateResult);
// Is the frame data valid?
if (updateResult.FrameId != 0)
{
    // Bind proxy render target
    simBinding->BlitRemoteFrameToProxy();
    // Use proxy camera data to render local content
    ...
    // Bind back buffer
    simBinding->ReprojectProxy();
}
else
{
    // Bind back buffer
    // Use current camera data to render local content
    ...
}
```

#### <a name="simulation-update-structures"></a>시뮬레이션 업데이트 구조

이전 섹션에서 **렌더링 루프 업데이트** 를 수행 하려면 각 프레임에서 로컬 카메라에 해당 하는 카메라 매개 변수 범위를 입력 하 고 사용 가능한 다음 프레임의 카메라에 해당 하는 카메라 매개 변수 집합을 반환 해야 합니다. 이러한 두 집합은 `SimulationUpdateParameters` 각각 및 구조체에서 캡처됩니다 `SimulationUpdateResult` .

```cs
public struct SimulationUpdateParameters
{
    public int FrameId;
    public StereoMatrix4x4 ViewTransform;
    public StereoCameraFov FieldOfView;
};

public struct SimulationUpdateResult
{
    public int FrameId;
    public float NearPlaneDistance;
    public float FarPlaneDistance;
    public StereoMatrix4x4 ViewTransform;
    public StereoCameraFov FieldOfView;
};
```

구조체 멤버에는 다음과 같은 의미가 있습니다.

| 멤버 | 설명 |
|--------|-------------|
| 프레임 Id | 연속 프레임 식별자입니다. SimulationUpdateParameters 입력에 필요 하며, 새 프레임 마다 지속적으로 증가 해야 합니다. 아직 프레임 데이터를 사용할 수 없는 경우 SimulationUpdateResult에서 0이 됩니다. |
| ViewTransform | 프레임의 카메라 뷰 변환 매트릭스의 왼쪽 오른쪽 스테레오 쌍입니다. Monoscopic 렌더링의 경우 `Left` 멤버만 유효 합니다. |
| FieldOfView | [뷰 규칙의 OpenXR 필드](https://www.khronos.org/registry/OpenXR/specs/1.0/html/xrspec.html#angles)에 있는 프레임 카메라의 왼쪽 오른쪽 스테레오 쌍입니다. Monoscopic 렌더링의 경우 `Left` 멤버만 유효 합니다. |
| NearPlaneDistance | 현재 원격 프레임의 프로젝션 행렬에 사용 되는 근거리 평면 거리입니다. |
| FarPlaneDistance | 현재 원격 프레임의 프로젝션 행렬에 사용 되는 먼 평면 거리입니다. |

`ViewTransform` `FieldOfView` Stereoscopic 렌더링이 사용 되는 경우 스테레오 쌍을 사용 하 여 눈에 값을 설정할 수 있습니다. 그렇지 않으면 `Right` 멤버가 무시 됩니다. 여기에서 볼 수 있듯이, 프로젝션 매트릭스가 지정 되지 않은 상태에서 카메라의 변형만 일반 4x4 변환 매트릭스로 전달 됩니다. 실제 행렬은 지정 된 뷰 필드를 사용 하 여 내부적으로 Azure 원격 렌더링에서 계산 되며 [CAMERASETTINGS API](../overview/features/camera.md)에 설정 된 현재 근거리 및 far 평면을 사용 합니다.

런타임 중에 [CameraSettings](../overview/features/camera.md) 에서 근접 평면 및 far 평면을 원하는 대로 변경할 수 있으며 서비스는 이러한 설정을 비동기적으로 적용 하므로 각 SimulationUpdateResult는 해당 하는 프레임을 렌더링 하는 동안 사용 되는 특정 근접 평면 및 far 평면도 전달 합니다. 이러한 평면 값을 사용 하 여 원격 프레임 렌더링과 일치 하도록 로컬 개체를 렌더링 하는 프로젝션 행렬을 조정할 수 있습니다.

마지막으로, **시뮬레이션 업데이트** 호출에 OpenXR 규칙의 뷰 필드가 필요 하지만 표준화 및 알고리즘 안전성을 위해 다음과 같은 구조 채우기 예제에 설명 된 변환 함수를 사용할 수 있습니다.

```cs
public SimulationUpdateParameters CreateSimulationUpdateParameters(int frameId, Matrix4x4 viewTransform, Matrix4x4 projectionMatrix)
{
    SimulationUpdateParameters parameters = default;
    parameters.FrameId = frameId;
    parameters.ViewTransform.Left = viewTransform;
    if (parameters.FieldOfView.Left.FromProjectionMatrix(projectionMatrix) != Result.Success)
    {
        // Invalid projection matrix
        throw new ArgumentException("Invalid projection settings");
    }
    return parameters;
}

public void GetCameraSettingsFromSimulationUpdateResult(SimulationUpdateResult result, out Matrix4x4 projectionMatrix, out Matrix4x4 viewTransform, out int frameId)
{
    projectionMatrix = default;
    viewTransform = default;
    frameId = 0;

    if (result.FrameId == 0)
    {
        // Invalid frame data
        return;
    }

    // Use the screenspace depth convention you expect for your projection matrix locally
    if (result.FieldOfView.Left.ToProjectionMatrix(result.NearPlaneDistance, result.FarPlaneDistance, DepthConvention.ZeroToOne, out projectionMatrix) != Result.Success)
    {
        // Invalid field-of-view
        return;
    }
    viewTransform = result.ViewTransform.Left;
    frameId = result.FrameId;
}
```

```cpp
SimulationUpdateParameters CreateSimulationUpdateParameters(uint32_t frameId, Matrix4x4 viewTransform, Matrix4x4 projectionMatrix)
{
    SimulationUpdateParameters parameters;
    parameters.FrameId = frameId;
    parameters.ViewTransform.Left = viewTransform;
    if (FovFromProjectionMatrix(projectionMatrix, parameters.FieldOfView.Left) != Result::Success)
    {
        // Invalid projection matrix
        return {};
    }
    return parameters;
}

void GetCameraSettingsFromSimulationUpdateResult(const SimulationUpdateResult& result, Matrix4x4& projectionMatrix, Matrix4x4& viewTransform, uint32_t& frameId)
{
    if (result.FrameId == 0)
    {
        // Invalid frame data
        return;
    }

    // Use the screenspace depth convention you expect for your projection matrix locally
    if (FovToProjectionMatrix(result.FieldOfView.Left, result.NearPlaneDistance, result.FarPlaneDistance, DepthConvention::ZeroToOne, projectionMatrix) != Result::Success)
    {
        // Invalid field-of-view
        return;
    }
    viewTransform = result.ViewTransform.Left;
    frameId = result.FrameId;
}
```

이러한 변환 함수를 사용 하면 로컬 렌더링에 대 한 필요에 따라 뷰의 필드 사양 및 일반 4x4 큐브 뷰 프로젝션 매트릭스를 빠르게 전환할 수 있습니다. 이러한 변환 함수는 확인 논리를 포함 하며 입력 프로젝션 행렬 또는 입력 필드의 입력이 잘못 된 경우 유효한 결과를 설정 하지 않고 오류를 반환 합니다.

## <a name="api-documentation"></a>API 설명서

* [C # RemoteManagerStatic StartupRemoteRendering ()](/dotnet/api/microsoft.azure.remoterendering.remotemanagerstatic.startupremoterendering)
* [C # GraphicsBinding 클래스](/dotnet/api/microsoft.azure.remoterendering.graphicsbinding)
* [C # GraphicsBindingWmrD3d11 클래스](/dotnet/api/microsoft.azure.remoterendering.graphicsbindingwmrd3d11)
* [C # GraphicsBindingSimD3d11 클래스](/dotnet/api/microsoft.azure.remoterendering.graphicsbindingsimd3d11)
* [C + + RemoteRenderingInitialization 구조체](/cpp/api/remote-rendering/remoterenderinginitialization)
* [C + + GraphicsBinding 클래스](/cpp/api/remote-rendering/graphicsbinding)
* [C + + GraphicsBindingWmrD3d11 클래스](/cpp/api/remote-rendering/graphicsbindingwmrd3d11)
* [C + + GraphicsBindingSimD3d11 클래스](/cpp/api/remote-rendering/graphicsbindingsimd3d11)

## <a name="next-steps"></a>다음 단계

* [카메라](../overview/features/camera.md)
* [후기 단계 다시 프로젝션](../overview/features/late-stage-reprojection.md)
* [자습서: 원격으로 렌더링된 모델 보기](../tutorials/unity/view-remote-models/view-remote-models.md)