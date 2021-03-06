---
title: Azure Monitor Application Insights Java
description: 코드를 수정할 필요 없이 모든 환경에서 실행 되는 Java 응용 프로그램에 대 한 응용 프로그램 성능 모니터링. 분산 추적 및 애플리케이션 맵.
ms.topic: conceptual
ms.date: 03/29/2020
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: b695df29b7a4704ee9e4e25e402fa0de8f2b7685
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103008215"
---
# <a name="java-codeless-application-monitoring-azure-monitor-application-insights"></a>Java 코드 없는 응용 프로그램 모니터링 Azure Monitor Application Insights

Java 코드리스 애플리케이션 모니터링은 단순성에 관한 것입니다. 코드 변경이 없으며 Java 에이전트는 몇 가지 구성 변경만으로 활성화할 수 있습니다.

 Java 에이전트는 모든 환경에서 작동 하며 모든 Java 응용 프로그램을 모니터링할 수 있도록 합니다. 즉, 온-프레미스 Vm, 온-프레미스, Windows, Linux에서 Java 앱을 실행 하 고 있는지 여부에 관계 없이 Java 3.0 에이전트가 앱을 모니터링 하 게 됩니다.

3.0 에이전트가 요청, 종속성 및 로그를 모두 자동으로 수집할 수 있으므로 응용 프로그램에 Application Insights Java SDK를 추가 하는 것은 더 이상 필요 하지 않습니다.

응용 프로그램에서 사용자 지정 원격 분석을 계속 보낼 수 있습니다. 3.0 에이전트는 자동으로 수집 된 모든 원격 분석과 함께 추적 하 고 상관 관계를 바꿉니다.

3.0 에이전트는 Java 8 이상을 지원 합니다.

## <a name="quickstart"></a>빠른 시작

**1. 에이전트를 다운로드 합니다.**

> [!WARNING]
> **3.0 미리 보기에서 업그레이드 하는 경우**
>
> 모든 구성 옵션은 json 구조가 완전히 변경 되었으므로 모든 [구성 옵션](./java-standalone-config.md) 을 신중 하 게 검토 해야 합니다.

[Applicationinsights-agent-3.0.2를 다운로드 합니다.](https://github.com/microsoft/ApplicationInsights-Java/releases/download/3.0.2/applicationinsights-agent-3.0.2.jar)

**2. JVM을 에이전트로 가리키기**

`-javaagent:path/to/applicationinsights-agent-3.0.2.jar`응용 프로그램의 JVM 인수에를 추가 합니다.

일반적인 JVM 인수에는 및가 포함 됩니다 `-Xmx512m` `-XX:+UseG1GC` . 따라서이를 추가할 위치를 알고 있으면이를 추가할 위치를 이미 알고 있는 것입니다.

응용 프로그램의 JVM 인수를 구성 하는 방법에 대 한 추가 도움말은 [jvm 인수를 업데이트 하기 위한 팁](./java-standalone-arguments.md)을 참조 하세요.

**3. 에이전트가 Application Insights 리소스를 가리키도록 합니다.**

Application Insights 리소스가 아직 없는 경우 [리소스 만들기 가이드](./create-new-resource.md)의 단계에 따라 새 리소스를 만들 수 있습니다.

환경 변수를 설정 하 여 에이전트가 Application Insights 리소스를 가리키도록 합니다.

```
APPLICATIONINSIGHTS_CONNECTION_STRING=InstrumentationKey=...
```

또는 이라는 구성 파일을 만들고와 `applicationinsights.json` 동일한 디렉터리에 배치 하 여 `applicationinsights-agent-3.0.2.jar` 다음과 같은 내용을 포함 합니다.

```json
{
  "connectionString": "InstrumentationKey=..."
}
```

Application Insights 리소스에서 연결 문자열을 찾을 수 있습니다.

:::image type="content" source="media/java-ipa/connection-string.png" alt-text="연결 문자열 Application Insights":::

**4. 이제 끝났습니다.**

이제 응용 프로그램을 시작 하 고 Azure Portal의 Application Insights 리소스로 이동 하 여 모니터링 데이터를 확인 합니다.

> [!NOTE]
> 모니터링 데이터가 포털에 표시 되는 데 몇 분 정도 걸릴 수 있습니다.


## <a name="configuration-options"></a>구성 옵션

파일에서 `applicationinsights.json` 다음을 추가로 구성할 수 있습니다.

* 클라우드 역할 이름
* 클라우드 역할 인스턴스
* 샘플링
* JMX 메트릭
* 사용자 지정 차원
* 원격 분석 프로세서 (미리 보기)
* 자동 수집 된 로깅
* 자동 수집 된 마이크로 측정기 메트릭 (스프링 부트 발동기 메트릭 포함)
* 하트비트
* HTTP 프록시
* 자체 진단

전체 정보는 [구성 옵션](./java-standalone-config.md) 을 참조 하세요.

## <a name="auto-collected-requests-dependencies-logs-and-metrics"></a>자동 수집 된 요청, 종속성, 로그 및 메트릭

### <a name="requests"></a>요청

* JMS 소비자
* Kafka 소비자
* Netty/WebFlux
* 서블릿
* 스프링 예약

### <a name="dependencies-with-distributed-trace-propagation"></a>분산 추적 전파를 사용한 종속성

* Apache HttpClient 및 HttpAsyncClient
* gRPC
* HttpURLConnection
* JMS
* Kafka
* Netty 클라이언트
* OkHttp

### <a name="other-dependencies"></a>기타 종속성

* Cassandra
* JDBC
* MongoDB (async 및 sync)
* Redis (선하라 및 Jedis)

### <a name="logs"></a>로그

* java.util.logging
* Log4j (MDC 속성 포함)
* SLF4J/Logback (MDC 속성 포함)

### <a name="metrics"></a>메트릭

* 마이크로 측정기 (스프링 부트 발동기 메트릭 포함)
* JMX 메트릭

## <a name="send-custom-telemetry-from-your-application"></a>응용 프로그램에서 사용자 지정 원격 분석 보내기

3.0 +의 목표는 표준 Api를 사용 하 여 사용자 지정 원격 분석을 보낼 수 있도록 하는 것입니다.

지금까지 마이크로 측정기, 인기 있는 로깅 프레임 워크 및 Application Insights Java 2.x SDK를 지원 합니다.
Application Insights Java 3.0은 이러한 Api를 통해 전송 되는 원격 분석을 자동으로 캡처하고 자동 수집 된 원격 분석과 상관 관계를 합니다.

### <a name="supported-custom-telemetry"></a>지원 되는 사용자 지정 원격 분석

다음 표에서는 Java 3.0 에이전트를 보완 하기 위해 사용할 수 있는 현재 지원 되는 사용자 지정 원격 분석 유형을 나타냅니다. 요약 하자면, 사용자 지정 메트릭은 마이크로 측정기를 통해 지원 되 고, 사용자 지정 예외 및 추적은 로깅 프레임 워크를 통해 사용 하도록 설정할 수 있으며, 사용자 지정 원격 분석의 모든 유형은 [Java 2.X SDK Application Insights](#send-custom-telemetry-using-the-2x-sdk)을 통해 지원 됩니다.

|                     | 마이크로미터 | Log4j, logback, 7 월 | 2.x SDK |
|---------------------|------------|---------------------|---------|
| **사용자 지정 이벤트**   |            |                     |  예    |
| **사용자 지정 메트릭**  |  예       |                     |  yes    |
| **종속성**    |            |                     |  예    |
| **예외**      |            |  예                |  예    |
| **페이지 보기**      |            |                     |  예    |
| **요청**        |            |                     |  예    |
| **추적**          |            |  예                |  예    |

현재 Application Insights 3.0를 사용 하 여 SDK를 릴리스할 계획은 아닙니다.

Application Insights Java 3.0는 Application Insights Java 2.x SDK로 전송 되는 원격 분석을 이미 수신 대기 하 고 있습니다. 이 기능은 기존 2.x 사용자를 위한 업그레이드 스토리의 중요 한 부분으로, OpenTelemetry API가 GA 될 때까지 사용자 지정 원격 분석 지원의 중요 한 격차를 채웁니다.

### <a name="send-custom-metrics-using-micrometer"></a>마이크로 측정기를 사용 하 여 사용자 지정 메트릭 보내기

응용 프로그램에 마이크로 측정기를 추가 합니다.

```xml
<dependency>
  <groupId>io.micrometer</groupId>
  <artifactId>micrometer-core</artifactId>
  <version>1.6.1</version>
</dependency>
```

마이크로 측정기 [글로벌 레지스트리](https://micrometer.io/docs/concepts#_global_registry) 를 사용 하 여 측정기를 만듭니다.

```java
static final Counter counter = Metrics.counter("test_counter");
```

그리고 메트릭을 기록 하는 데 사용 합니다.

```java
counter.increment();
```

### <a name="send-custom-traces-and-exceptions-using-your-favorite-logging-framework"></a>즐겨 사용 하는 로깅 프레임 워크를 사용 하 여 사용자 지정 추적 및 예외 보내기

Log4j, Logback 및 java는 자동으로 계측 되며 이러한 로깅 프레임 워크를 통해 수행 된 로깅은 추적 및 예외 원격 분석으로 자동 수집 됩니다.

기본적으로 로깅은 정보 수준 이상에서 수행 되는 경우에만 수집 됩니다.
이 수준을 변경 하는 방법에 대 한 [구성 옵션](./java-standalone-config.md#auto-collected-logging) 을 참조 하십시오.

로그에 사용자 지정 차원을 연결 하려면 [Log4j 1.2 mdc](https://logging.apache.org/log4j/1.2/apidocs/org/apache/log4j/MDC.html), [Log4j 2 Mdc](https://logging.apache.org/log4j/2.x/manual/thread-context.html)또는 [logback MDC](http://logback.qos.ch/manual/mdc.html)Application Insights를 사용 하면 됩니다. 그러면 Java 3.0에서 이러한 mdc 속성을 추적 및 예외 원격 분석에서 사용자 지정 차원으로 자동으로 캡처합니다.

### <a name="send-custom-telemetry-using-the-2x-sdk"></a>2.x SDK를 사용 하 여 사용자 지정 원격 분석 보내기

`applicationinsights-core-2.6.2.jar`응용 프로그램에를 추가 합니다. (모든 2.x 버전은 Application Insights Java 3.0에서 지원 되지만, 원하는 경우 최신 버전을 사용 하는 것이 좋습니다.)

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>applicationinsights-core</artifactId>
  <version>2.6.2</version>
</dependency>
```

TelemetryClient를 만듭니다.

  ```java
static final TelemetryClient telemetryClient = new TelemetryClient();
```

그리고이를 사용 하 여 사용자 지정 원격 분석을 보냅니다.

##### <a name="events"></a>이벤트

```java
telemetryClient.trackEvent("WinGame");
```

##### <a name="metrics"></a>메트릭

```java
telemetryClient.trackMetric("queueLength", 42.0);
```

##### <a name="dependencies"></a>종속성

```java
boolean success = false;
long startTime = System.currentTimeMillis();
try {
    success = dependency.call();
} finally {
    long endTime = System.currentTimeMillis();
    RemoteDependencyTelemetry telemetry = new RemoteDependencyTelemetry();
    telemetry.setSuccess(success);
    telemetry.setTimestamp(new Date(startTime));
    telemetry.setDuration(new Duration(endTime - startTime));
    telemetryClient.trackDependency(telemetry);
}
```

##### <a name="logs"></a>로그

```java
telemetryClient.trackTrace(message, SeverityLevel.Warning, properties);
```

##### <a name="exceptions"></a>예외

```java
try {
    ...
} catch (Exception e) {
    telemetryClient.trackException(e);
}
```

### <a name="add-request-custom-dimensions-using-the-2x-sdk"></a>2.x SDK를 사용 하 여 요청 사용자 지정 차원 추가

> [!NOTE]
> 이 기능은 3.0.2 이상에만 해당 됩니다.

`applicationinsights-web-2.6.2.jar`응용 프로그램에를 추가 합니다. (모든 2.x 버전은 Application Insights Java 3.0에서 지원 되지만, 원하는 경우 최신 버전을 사용 하는 것이 좋습니다.)

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>applicationinsights-web</artifactId>
  <version>2.6.2</version>
</dependency>
```

코드에 사용자 지정 차원을 추가 합니다.

```java
import com.microsoft.applicationinsights.web.internal.ThreadContext;

RequestTelemetry requestTelemetry = ThreadContext.getRequestTelemetryContext().getHttpRequestTelemetry();
requestTelemetry.getProperties().put("mydimension", "myvalue");
```

### <a name="set-the-request-telemetry-user_id-using-the-2x-sdk"></a>2.x SDK를 사용 하 여 user_Id 요청 원격 분석 설정

> [!NOTE]
> 이 기능은 3.0.2 이상에만 해당 됩니다.

`applicationinsights-web-2.6.2.jar`응용 프로그램에를 추가 합니다. (모든 2.x 버전은 Application Insights Java 3.0에서 지원 되지만, 원하는 경우 최신 버전을 사용 하는 것이 좋습니다.)

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>applicationinsights-web</artifactId>
  <version>2.6.2</version>
</dependency>
```

코드에서를 설정 합니다 `user_Id` .

```java
import com.microsoft.applicationinsights.web.internal.ThreadContext;

RequestTelemetry requestTelemetry = ThreadContext.getRequestTelemetryContext().getHttpRequestTelemetry();
requestTelemetry.getContext().getUser().setId("myuser");
```

### <a name="override-the-request-telemetry-name-using-the-2x-sdk"></a>2.x SDK를 사용 하 여 요청 원격 분석 이름을 재정의 합니다.

> [!NOTE]
> 이 기능은 3.0.2 이상에만 해당 됩니다.

`applicationinsights-web-2.6.2.jar`응용 프로그램에를 추가 합니다. (모든 2.x 버전은 Application Insights Java 3.0에서 지원 되지만, 원하는 경우 최신 버전을 사용 하는 것이 좋습니다.)

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>applicationinsights-web</artifactId>
  <version>2.6.2</version>
</dependency>
```

코드에 이름을 설정 합니다.

```java
import com.microsoft.applicationinsights.web.internal.ThreadContext;

RequestTelemetry requestTelemetry = ThreadContext.getRequestTelemetryContext().getHttpRequestTelemetry();
requestTelemetry.setName("myname");
```

### <a name="get-the-request-telemetry-id-and-the-operation-id-using-the-2x-sdk"></a>2.x SDK를 사용 하 여 요청 원격 분석 id 및 작업 id를 가져옵니다.

> [!NOTE]
> 이 기능은 3.0.3-BETA 이상에만 해당 됩니다.

`applicationinsights-web-2.6.2.jar`응용 프로그램에를 추가 합니다. (모든 2.x 버전은 Application Insights Java 3.0에서 지원 되지만, 원하는 경우 최신 버전을 사용 하는 것이 좋습니다.)

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>applicationinsights-web</artifactId>
  <version>2.6.2</version>
</dependency>
```

그리고 코드에서 요청 원격 분석 id 및 작업 id를 가져옵니다.

```java
import com.microsoft.applicationinsights.web.internal.ThreadContext;

RequestTelemetry requestTelemetry = ThreadContext.getRequestTelemetryContext().getHttpRequestTelemetry();
String requestId = requestTelemetry.getId();
String operationId = requestTelemetry.getContext().getOperation().getId();
```
