---
title: "ASP.NET Core 웹 서버 구현이 kestrel"
author: tdykstra
description: "ASP.NET Core libuv 기반 Kestrel, 플랫폼 간 웹 서버를 소개 합니다."
keywords: "ASP.NET Core, Kestrel, libuv, url 접두사"
ms.author: tdykstra
manager: wpickett
ms.date: 08/02/2017
ms.topic: article
ms.assetid: 560bd66f-7dd0-4e68-b5fb-f31477e4b785
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/servers/kestrel
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 451c36fc9095b6e076e5287c992b6163205c523b
ms.sourcegitcommit: 732cd2684246e49e796836596643a8d37e20c46d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2017
---
# <a name="introduction-to-kestrel-web-server-implementation-in-aspnet-core"></a>ASP.NET Core 웹 서버 구현이 Kestrel 소개

여 [Tom Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher), 및 [Stephen Halter](https://twitter.com/halter73)

Kestrel는 플랫폼 간 [ASP.NET Core 웹 서버로](index.md) 기반 [libuv](https://github.com/libuv/libuv), 비동기 I/O 플랫폼 간 라이브러리입니다. Kestrel은 ASP.NET Core 프로젝트 템플릿에서 기본적으로 포함 되는 웹 서버입니다. 

Kestrel은 다음과 같은 기능을 지원합니다.

  * HTTPS
  * 사용 하도록 설정 하는 데 사용 되는 불투명 업그레이드 [Websocket](https://github.com/aspnet/websockets)
  * 고성능 Nginx 뒤에 대 한 Unix 소켓 

Kestrel 모든 플랫폼 및 버전을 지 원하는.NET Core에서 지원 됩니다.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

[보기 또는 2.x에 대 한 샘플 코드를 다운로드](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/sample2) ([다운로드 하는 방법을](xref:tutorials/index#how-to-download-a-sample))

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

[보기 또는 1.x에 대 한 샘플 코드를 다운로드](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/sample1) ([다운로드 하는 방법을](xref:tutorials/index#how-to-download-a-sample))

---

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a>Kestrel 역방향 프록시를 사용 하는 경우

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

Kestrel을 단독으로 사용하거나 IIS, Nginx 또는 Apache 같은 *역방향 프록시 서버*와 함께 사용할 수 있습니다. 역방향 프록시 서버는 인터넷에서 HTTP 요청을 수신하고 몇몇 사전 처리 후에 Kestrel에 전달합니다.

![Kestrel은 역방향 프록시 서버 없이 직접 인터넷과 통신합니다.](kestrel/_static/kestrel-to-internet2.png)

![Kestrel은 IIS, Nginx 또는 Apache 같은 역방향 프록시 서버를 통해 간접적으로 인터넷과 통신합니다.](kestrel/_static/kestrel-to-internet.png)

Kestrel이 내부 네트워크에만 노출되는 경우 역방향 프록시 서버를 사용하거나 사용하지 않는 구성을 사용할 수도 있습니다.

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

응용 프로그램이 내부 네트워크의 요청만 허용할 경우 Kestrel을 단독으로 사용할 수 있습니다.

![Kestrel은 내부 네트워크와 직접 통신합니다.](kestrel/_static/kestrel-to-internal.png)

응용 프로그램을 인터넷에 노출할 경우 IIS, Nginx 또는 Apache를 *역방향 프록시 서버*로 사용해야 합니다. 역방향 프록시 서버는 인터넷에서 HTTP 요청을 수신하고 몇몇 사전 처리 후에 Kestrel에 전달합니다.

![Kestrel은 IIS, Nginx 또는 Apache 같은 역방향 프록시 서버를 통해 간접적으로 인터넷과 통신합니다.](kestrel/_static/kestrel-to-internet.png)

역방향 프록시는 보안상의 이유로 가장자리 배포 (인터넷에서 트래픽에 노출)에 필요 합니다. Kestrel 1.x 버전에는 공격에 대한 전체 방어 기능이 포함되어 있지 않습니다. 여기에 포함 되지만 적절 한 제한 시간, 크기 제한 및 동시 연결 제한으로 제한 되지 않습니다.

---

역방향 프록시를 필요로 하는 시나리오에는 동일한 IP 및 단일 서버에서 실행 되는 포트를 공유 하는 여러 응용 프로그램이 있는 경우입니다. 하지 않는 함께 작동 Kestrel 직접 Kestrel 동일한 IP 및 여러 프로세스 간에 포트 공유를 지원 하지 않습니다. 포트에서 수신 하도록 Kestrel를 구성할 때 호스트 헤더에 관계 없이 해당 포트에 대 한 모든 트래픽을 처리 합니다. 포트를 공유할 수 있는 역방향 프록시 해야 후 Kestrel에 고유 IP 및 포트에 전달 됩니다.

역방향 프록시 서버는 필요 하지 않습니다, 경우에 하나를 사용 하 여 다른 이유로 좋은 적합할 수 있습니다.

* 프로그램 노출 된 노출 영역을 제한할 수 있습니다.
* 선택적 추가 계층을 구성 및 철저 한 방어를 제공합니다.
* 기존 인프라와 잘 통합 될 수 있습니다.
* 부하 분산 및 SSL 설정을 간소화합니다. 역방향 프록시 서버에만 필요한 SSL 인증서를 하 고 해당 서버가 일반 HTTP를 사용 하 여 내부 네트워크에 응용 프로그램 서버와 통신할 수 있습니다.

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a>Kestrel ASP.NET Core 응용 프로그램에서 사용 하는 방법

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) 패키지에 포함 되어는 [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage)합니다.

ASP.NET Core 프로젝트 템플릿은 기본적으로 Kestrel를 사용합니다. *Program.cs*, 템플릿 코드 호출 `CreateDefaultBuilder`, 되는 호출 [UseKestrel](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions#Microsoft_AspNetCore_Hosting_WebHostBuilderKestrelExtensions_UseKestrel_Microsoft_AspNetCore_Hosting_IWebHostBuilder_) 내부적입니다.

[!code-csharp[](kestrel/sample2/Program.cs?name=snippet_DefaultBuilder&highlight=7)]

Kestrel 옵션을 구성 해야 할 경우 호출 `UseKestrel` 에 *Program.cs* 다음 예제와 같이:

[!code-csharp[](kestrel/sample2/Program.cs?name=snippet_DefaultBuilder&highlight=9-16)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

설치는 [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) NuGet 패키지 합니다.

호출 된 [UseKestrel](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions#Microsoft_AspNetCore_Hosting_WebHostBuilderKestrelExtensions_UseKestrel_Microsoft_AspNetCore_Hosting_IWebHostBuilder_) 확장 메서드를 `WebHostBuilder` 에 프로그램 `Main` 메서드를 지정 하 [Kestrel 옵션](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.server.kestrel.kestrelserveroptions) 다음 섹션에 나와 있는 것 처럼 필요한 합니다.

[!code-csharp[](kestrel/sample1/Program.cs?name=snippet_Main&highlight=13-19)]

---

### <a name="kestrel-options"></a>Kestrel 옵션

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

Kestrel 웹 서버에는 인터넷 연결 배포에 특히 유용한 제약 조건 구성 옵션이 있습니다. 다음은 몇 제한을 설정할 수 있습니다.

- 최대 클라이언트 연결
- 최대 요청 본문 크기
- 최소 요청 본문 데이터 속도

이러한 제약 조건 및의 다른 설정의 `Limits` 의 속성은 [KestrelServerOptions](https://github.com/aspnet/KestrelHttpServer/blob/rel/2.0.0/src/Microsoft.AspNetCore.Server.Kestrel.Core/KestrelServerOptions.cs) 클래스. `Limits` 의 인스턴스를 보유 하는 속성은 [KestrelServerLimits](https://github.com/aspnet/KestrelHttpServer/blob/rel/2.0.0/src/Microsoft.AspNetCore.Server.Kestrel.Core/KestrelServerLimits.cs) 클래스입니다. 

**최대 클라이언트 연결**

다음 코드를 사용 하는 전체 응용 프로그램에 대 한 열려 있는 동시 TCP 연결의 최대 수를 설정할 수 있습니다.

[!code-csharp[](kestrel/sample2/Program.cs?name=snippet_Limits&highlight=3-4)]

다른 프로토콜 (예를 들어 Websocket 요청)에서 HTTP 또는 HTTPS에서 업그레이드 된 연결에 대 한 별도 제한이 있습니다. 에 따라 계산 되지 않습니다 연결이 업그레이드 되는 `MaxConcurrentConnections` 제한 합니다. 

연결의 최대 수는 기본적으로 무제한 (null).

**최대 요청 본문 크기**

기본 최대 요청 본문 크기는 약 28.6 m B 인 30,000,000 바이트입니다. 

사용 하는 ASP.NET Core MVC 응용 프로그램에 대 한도 무시 하는 권장된 방법은 [RequestSizeLimit](https://github.com/aspnet/Mvc/blob/rel/2.0.0/src/Microsoft.AspNetCore.Mvc.Core/RequestSizeLimitAttribute.cs) 작업 메서드에 특성:

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

전체 응용 프로그램을 모든 요청에 대 한 제약 조건을 구성 하는 방법을 보여 주는 예제는 다음과 같습니다.

[!code-csharp[](kestrel/sample2/Program.cs?name=snippet_Limits&highlight=5)]

미들웨어의 특정 요청에 설정을 재정의할 수 있습니다.

[!code-csharp[](kestrel/sample2/Startup.cs?name=snippet_Limits&highlight=3-4)]
 
응용 프로그램 요청 읽기 시작 된 후 요청에 대 한 제한을 구성 하려고 하면 예외가 throw 됩니다. `IsReadOnly` 를 알려 주는 속성 경우는 `MaxRequestBodySize` 제한을 구성 하려면에 너무 늦었습니다 의미 속성은 읽기 전용 상태에서입니다.

**최소 요청 본문 데이터 속도**

Kestrel은 데이터 제공 되는 경우는 지정 된 비율에 바이트 수/초 1 초 마다 확인 합니다. 속도가 최소 아래로 떨어지면, 연결 시간이 초과 됩니다. 유예 기간은 Kestrel 최소;까지 해당 전송 속도 높이기 위해 클라이언트에 제공 하는 시간 이 시간 동안 속도 확인 하지 않습니다. 유예 기간에 TCP 느린 시작으로 인해 느린 속도로 데이터 보내는 처음 연결을 삭제 하지 않도록 하는 데 도움이 됩니다.

기본 최소 속도 240 바이트/초가 고 5 초의 유예 기간입니다.

최소 속도 응답에도 적용 됩니다. 요청 제한 및 응답 용량 한도 설정 하는 코드는 것을 제외 하 고 동일 `RequestBody` 또는 `Response` 속성 및 인터페이스 이름에 있습니다. 

여기에 최소 데이터 속도 구성 하는 방법을 보여 주는 예제는 *Program.cs*:

[!code-csharp[](kestrel/sample2/Program.cs?name=snippet_Limits&highlight=6-9)]

미들웨어에서 요청에 따라 속도 구성할 수 있습니다.

[!code-csharp[](kestrel/sample2/Startup.cs?name=snippet_Limits&highlight=5-8)]

다른 Kestrel 옵션에 대 한 내용은 다음 클래스를 참조 하십시오.

* [KestrelServerOptions](https://github.com/aspnet/KestrelHttpServer/blob/rel/2.0.0/src/Microsoft.AspNetCore.Server.Kestrel.Core/KestrelServerOptions.cs)
* [KestrelServerLimits](https://github.com/aspnet/KestrelHttpServer/blob/rel/2.0.0/src/Microsoft.AspNetCore.Server.Kestrel.Core/KestrelServerLimits.cs)
* [ListenOptions](https://github.com/aspnet/KestrelHttpServer/blob/rel/2.0.0/src/Microsoft.AspNetCore.Server.Kestrel.Core/ListenOptions.cs)

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

Kestrel 옵션에 대 한 정보를 참조 하십시오. [KestrelServerOptions 클래스](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.server.kestrel.kestrelserveroptions)합니다.

---

### <a name="endpoint-configuration"></a>끝점 구성

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

기본적으로 ASP.NET Core을 바인딩합니다 `http://localhost:5000`합니다. URL 접두사 및 호출 하 여에서 수신 하도록 Kestrel에 대 한 포트 구성 `Listen` 또는 `ListenUnixSocket` 에 대 한 메서드 `KestrelServerOptions`합니다. (`UseUrls`, `urls` 명령줄 인수 및 ASPNETCORE_URLS 환경 변수 에서도 작동 하지만 제한이 명시 된 [이 문서의 뒷부분에 나오는](#useurls-limitations).)

**TCP 소켓에 바인딩**

`Listen` 메서드는 TCP 소켓을 바인딩하고 옵션 람다는 SSL 인증서를 구성할 수 있습니다.

[!code-csharp[](kestrel/sample2/Program.cs?name=snippet_DefaultBuilder&highlight=9-16)]

어떻게이 예에서는 SSL을 특정 끝점에 대 한 사용 하 여 구성 확인 [ListenOptions](https://github.com/aspnet/KestrelHttpServer/blob/rel/2.0.0/src/Microsoft.AspNetCore.Server.Kestrel.Core/ListenOptions.cs)합니다. 특정 끝점에 대 한 다른 Kestrel 설정을 구성 하는 동일한 API를 사용할 수 있습니다.

[!INCLUDE[How to make an SSL cert](../../includes/make-ssl-cert.md)]

**Unix 소켓에 바인딩**

이 예제에 나와 있는 것 처럼에 향상 된 성능을 얻으려면 Nginx, Unix 소켓에서 수신할 수 있습니다.:

[!code-csharp[](kestrel/sample2/Program.cs?name=snippet_UnixSocket)]

**포트 0**

포트 번호 0을 지정 하면 Kestrel는 동적으로 사용 가능한 포트를 바인딩합니다. 다음 예제에서는 Kestrel 실제로 런타임에 바인딩할 포트를 확인 하는 방법을 보여 줍니다.

[!code-csharp[](kestrel/sample2/Startup.cs?name=snippet_Configure&highlight=3,13,16-17)]

<a id="useurls-limitations"></a>

**UseUrls 제한 사항**

호출 하 여 끝점을 구성할 수 있습니다는 `UseUrls` 메서드 또는 사용 하는 `urls` ASPNETCORE_URLS 환경 변수나 명령줄 인수입니다. 이러한 메서드는 코드 Kestrel 아닌 서버와 작동 하도록 하려는 경우 유용 합니다. 그러나 이러한 제한을 고려해 야 합니다.

* 이러한 방법으로 SSL을 사용할 수 없습니다.
* 둘 다 사용 하는 경우는 `Listen` 메서드 및 `UseUrls`, `Listen` 끝점 재정의 `UseUrls` 끝점입니다.

**IIS에 대 한 끝점 구성**

IIS를 사용 하는 경우 IIS에 대 한 URL 바인딩을 재정의 중 하나를 호출 하 여 설정할 수 있는 모든 바인딩에 `Listen` 또는 `UseUrls`합니다. 자세한 내용은 참조 [ASP.NET Core 모듈 소개](aspnet-core-module.md)합니다.

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

기본적으로 ASP.NET Core을 바인딩합니다 `http://localhost:5000`합니다. URL 접두사 및에서 사용 하 여 수신 하도록 Kestrel에 대 한 포트를 구성할 수는 `UseUrls` 확장 메서드는 `urls` 명령줄 인수 또는 ASP.NET Core 구성 시스템입니다. 이러한 메서드에 대 한 자세한 내용은 참조 [호스팅](../../fundamentals/hosting.md)합니다. 역방향 프록시로 IIS를 사용 하는 경우 URL 바인딩을 작동 하는 방법에 대 한 자세한 내용은 참조 하십시오. [ASP.NET Core 모듈](aspnet-core-module.md)합니다. 

---

### <a name="url-prefixes"></a>URL 접두사

호출 하는 경우 `UseUrls` 하거나 사용 하 여는 `urls` 명령줄 인수나 ASPNETCORE_URLS 환경 변수 URL 접두사는 다음 형식 중 하나일 수 있습니다. 

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

HTTP URL 접두사만 사용할 수 있습니다. Kestrel URL 바인딩을 사용 하 여 구성할 때 SSL을 지원 하지 않습니다 `UseUrls`합니다.

* IPv4 주소와 포트 번호

  ```
  http://65.55.39.10:80/
  ```

  0.0.0.0은 특별 한 경우로 모든 IPv4 주소를 바인딩합니다.


* 포트 번호가 있는 IPv6 주소

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/ 
  ```

  [:]는 i p v 4에 해당 하는 IPv6 0.0.0.0입니다.


* 호스트 이름과 포트 번호

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  호스트 이름, *, 고 +, 특별 한 되지 않습니다. IP 주소 또는 "localhost"를 인식 하지 않은 모든 항목은 모든 IPv4 및 IPv6 Ip에 바인딩합니다. 서로 다른 호스트 이름을 같은 포트에서 서로 다른 ASP.NET Core 응용 프로그램에 바인딩할 필요 사용 [HTTP.sys](httpsys.md) 또는 IIS, Nginx, 또는 Apache 같은 역방향 프록시 서버.

* 포트 번호 또는 루프백 ip 포트 번호로 "Localhost" 이름

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  때 `localhost` 를 지정, Kestrel IPv4 및 IPv6 모두 루프백 인터페이스에 바인딩합니다. 요청 된 포트는 루프백 인터페이스 중 하나에 다른 서비스에서 사용 중인 경우 Kestrel 시작 되지 않습니다. 루프백 인터페이스 중 하나 사용할 수 없으면 다른 이유로 대부분 IPv6 지원 되지 않기 때문에 일반적으로, Kestrel 경고를 기록 합니다. 

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

* IPv4 주소와 포트 번호

  ```
  http://65.55.39.10:80/
  https://65.55.39.10:443/
  ```

  0.0.0.0은 특별 한 경우로 모든 IPv4 주소를 바인딩합니다.


* 포트 번호가 있는 IPv6 주소

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/ 
  https://[0:0:0:0:0:ffff:4137:270a]:443/ 
  ```

  [:]는 i p v 4에 해당 하는 IPv6 0.0.0.0입니다.


* 호스트 이름과 포트 번호

  ```
  http://contoso.com:80/
  http://*:80/
  https://contoso.com:443/
  https://*:443/
  ```

  호스트 이름, \*, 및 + 특별 한 되지 않습니다. 이외의 모든 IP 주소 또는 "localhost"를 인식 된 모든 IPv4 및 IPv6 Ip에 바인딩합니다. 서로 다른 호스트 이름을 같은 포트에서 서로 다른 ASP.NET Core 응용 프로그램에 바인딩할 필요 사용 [WebListener](weblistener.md) 또는 IIS, Nginx, 또는 Apache 같은 역방향 프록시 서버.

* 포트 번호 또는 루프백 ip 포트 번호로 "Localhost" 이름

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  때 `localhost` 를 지정, Kestrel IPv4 및 IPv6 모두 루프백 인터페이스에 바인딩합니다. 요청 된 포트는 루프백 인터페이스 중 하나에 다른 서비스에서 사용 중인 경우 Kestrel 시작 되지 않습니다. 루프백 인터페이스 중 하나 사용할 수 없으면 다른 이유로 대부분 IPv6 지원 되지 않기 때문에 일반적으로, Kestrel 경고를 기록 합니다. 

* Unix 소켓

  ```
  http://unix:/run/dan-live.sock  
  ```

**포트 0**

포트 번호 0을 지정 하면 Kestrel는 동적으로 사용 가능한 포트를 바인딩합니다. 호스트 이름 또는 IP를 제외 하 고 사용할 수는 0 포트에 바인딩 `localhost` 이름입니다.

다음 예제에서는 Kestrel 실제로 런타임에 바인딩할 포트를 확인 하는 방법을 보여 줍니다.

[!code-csharp[](kestrel/sample1/Startup.cs?name=snippet_Configure)]

**SSL에 대 한 URL 접두사**

URL 접두사를 포함 해야 `https:` 호출 하는 경우는 `UseHttps` 확장 메서드를 다음과 같이 합니다.

```csharp
var host = new WebHostBuilder() 
    .UseKestrel(options => 
    { 
        options.UseHttps("testCert.pfx", "testPassword"); 
    }) 
   .UseUrls("http://localhost:5000", "https://localhost:5001") 
   .UseContentRoot(Directory.GetCurrentDirectory()) 
   .UseStartup<Startup>() 
   .Build(); 
```

> [!NOTE]
> HTTPS 및 HTTP 같은 포트에서 호스팅할 수 없습니다.

[!INCLUDE[How to make an SSL cert](../../includes/make-ssl-cert.md)]

---
## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 리소스를 참조하세요.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

* [2.x에 대 한 샘플 응용 프로그램](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/sample2)
* [Kestrel 소스 코드](https://github.com/aspnet/KestrelHttpServer)

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

* [1.x 용 예제 응용 프로그램](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/sample1)
* [Kestrel 소스 코드](https://github.com/aspnet/KestrelHttpServer)

---
