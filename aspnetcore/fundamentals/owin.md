---
title: ".NET (OWIN)에 대 한 웹 인터페이스가 | Microsoft 문서"
author: ardalis
description: ".NET (OWIN)에 대 한 웹 인터페이스를 소개 합니다."
keywords: "ASP.NET Core,.NET, OWIN에 대 한 공개 웹 인터페이스"
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 70c4e6bc-a773-4039-96ec-6fe557c9369d
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/owin
ms.custom: H1Hack27Feb2017
translationtype: Machine Translation
ms.sourcegitcommit: 010b730d2716f9f536fef889bc2f767afb648ef4
ms.openlocfilehash: 6753a4445c539cc9b8c4412027d64e998d57e710
ms.lasthandoff: 03/23/2017

---
# <a name="introduction-to-open-web-interface-for-net-owin"></a>.NET (OWIN)에 대 한 웹 인터페이스를 열려면 소개

여 [Steve Smith](http://ardalis.com) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET 핵심.NET (OWIN)에 대 한 공개 웹 인터페이스를 지원합니다. OWIN 웹 앱을을 웹 서버에서 분리 될 수 있습니다. 미들웨어를 요청과 연결 된 응답을 처리 하는 파이프라인에서 사용할 수 있는 표준 방법을 정의 합니다. ASP.NET 핵심 응용 프로그램 및 미들웨어 OWIN 기반 응용 프로그램, 서버 및 미들웨어와 상호 운용할 수 있습니다.

[샘플 코드 보기 또는 다운로드](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/owin/sample)

## <a name="running-owin-middleware-in-the-aspnet-pipeline"></a>ASP.NET 파이프라인에는 OWIN 미들웨어를 실행합니다.

ASP.NET 핵심 OWIN 지원의 일부로 배포 되는 `Microsoft.AspNetCore.Owin` 패키지 합니다. 이 패키지를 설치 하 여 프로젝트에 OWIN 지원을 가져올 수 있습니다.

OWIN 미들웨어를 준수 하는 [OWIN 사양](http://owin.org/spec/spec/owin-1.0.0.html), 요구 하는 `Func<IDictionary<string, object>, Task>` 인터페이스 및 특정 키를 설정할 수 (예: `owin.ResponseBody`). 다음과 같은 간단한 OWIN 미들웨어는 "Hello World"를 표시합니다.

```csharp
public Task OwinHello(IDictionary<string, object> environment)
{
    string responseText = "Hello World via OWIN";
    byte[] responseBytes = Encoding.UTF8.GetBytes(responseText);

    // OWIN Environment Keys: http://owin.org/spec/spec/owin-1.0.0.html
    var responseStream = (Stream)environment["owin.ResponseBody"];
    var responseHeaders = (IDictionary<string, string[]>)environment["owin.ResponseHeaders"];

    responseHeaders["Content-Length"] = new string[] { responseBytes.Length.ToString(CultureInfo.InvariantCulture) };
    responseHeaders["Content-Type"] = new string[] { "text/plain" };

    return responseStream.WriteAsync(responseBytes, 0, responseBytes.Length);
}


```

샘플 서명을 반환는 `Task` 수락 하 고는 `IDictionary<string, object>` OWIN 하 여 필요에 따라 합니다.

다음 코드를 추가 하는 방법을 보여 줍니다는 `OwinHello` 미들웨어 (위에 표시 됨)와 ASP.NET 파이프라인에는 `UseOwin` 확장 메서드.

<!-- literal_block {"ids": [], "names": [], "highlight_args": {"linenostart": 1}, "backrefs": [], "dupnames": [], "linenos": false, "classes": [], "xml:space": "preserve", "language": "c#", "source": "/Users/shirhatti/src/Docs/aspnet/fundamentals/owin/sample/src/OwinSample/Startup.cs"} -->

```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseOwin(pipeline =>
    {
        pipeline(next => OwinHello);
    });
}


```

OWIN 파이프라인 내에서 수행 하는 기타 작업을 구성할 수 있습니다.

> [!NOTE]
> 응답 헤더를 응답 스트림에 최초 쓰기 전에 수정 되어야 합니다.

> [!NOTE]
> 여러 번 호출 `UseOwin` 성능상의 이유로 사용 하지 않고 있습니다. OWIN 구성 요소는 함께 그룹화 하는 경우 가장 잘 작동 합니다.

<!-- literal_block {"ids": [], "names": [], "highlight_args": {}, "backrefs": [], "dupnames": [], "linenos": false, "classes": [], "xml:space": "preserve", "language": "c#"} -->

```csharp
app.UseOwin(pipeline =>
{
    pipeline(next =>
    {
        // do something before
        return OwinHello;
        // do something after
    });
});
```

<a name=hosting-on-owin></a>

## <a name="using-aspnet-hosting-on-an-owin-based-server"></a>OWIN 기반 서버에서 ASP.NET 호스팅 사용

OWIN 기반 서버 ASP.NET 응용 프로그램을 호스팅할 수 있습니다. 이러한 서버 하나가 [Nowin](https://github.com/Bobris/Nowin),.NET OWIN 웹 서버입니다. 이 기사의 샘플에서 사용 하 여 만듭니다를 Nowin 참조 하는 프로젝트를 추가 했습니다는 `IServer` ASP.NET 코어를 자체 호스트할 수 있습니다.

[!code-csharp[주](owin/sample/src/NowinSample/Program.cs?highlight=15)]

`IServer`필요한 인터페이스는 `Features` 속성 및 `Start` 메서드.

`Start`구성 하 고이 경우에 일련의 구문 분석 하는 주소는 IServerAddressesFeature 설정 하는 fluent API 호출을 통해 수행 하는 서버를 시작 하는 일을 담당 합니다. fluent 구성을 `_builder` 요청에서 처리 되는 변수를 지정 된 `appFunc` 메서드 이전에 정의 된 합니다. 이 `Func` 들어오는 요청을 처리 하는 각 요청에서 호출 됩니다.

추가 `IWebHostBuilder` 확장을 쉽게 추가 하 고 Nowin 서버를 구성 합니다.

<!-- literal_block {"ids": [], "names": [], "highlight_args": {"hl_lines": [11], "linenostart": 1}, "backrefs": [], "dupnames": [], "linenos": false, "classes": [], "xml:space": "preserve", "language": "c#", "source": "/Users/shirhatti/src/Docs/aspnet/fundamentals/owin/sample/src/NowinSample/NowinWebHostBuilderExtensions.cs"} -->

```csharp
using System;
using Microsoft.AspNetCore.Hosting.Server;
using Microsoft.Extensions.DependencyInjection;
using Nowin;
using NowinSample;

namespace Microsoft.AspNetCore.Hosting
{
    public static class NowinWebHostBuilderExtensions
    {
        public static IWebHostBuilder UseNowin(this IWebHostBuilder builder)
        {
            return builder.ConfigureServices(services =>
            {
                services.AddSingleton<IServer, NowinServer>();
            });
        }

        public static IWebHostBuilder UseNowin(this IWebHostBuilder builder, Action<ServerBuilder> configure)
        {
            builder.ConfigureServices(services =>
            {
                services.Configure(configure);
            });
            return builder.UseNowin();
        }
    }
}
```

이 위치를에서 확장을 호출 하 여 사용자 지정이 서버를 사용 하 여 ASP.NET 응용 프로그램을 실행 하는 데 필요한에 있는 모든 *Program.cs*:

<!-- literal_block {"ids": [], "names": [], "highlight_args": {"hl_lines": [15], "linenostart": 1}, "backrefs": [], "dupnames": [], "linenos": false, "classes": [], "xml:space": "preserve", "language": "c#", "source": "/Users/shirhatti/src/Docs/aspnet/fundamentals/owin/sample/src/NowinSample/Program.cs"} -->

```csharp

using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Hosting;

namespace NowinSample
{
    public class Program
    {
        public static void Main(string[] args)
        {
            var host = new WebHostBuilder()
                .UseNowin()
                .UseContentRoot(Directory.GetCurrentDirectory())
                .UseIISIntegration()
                .UseStartup<Startup>()
                .Build();

            host.Run();
        }
    }
}

```

ASP.NET에 대 한 자세한 [서버](servers/index.md)합니다.

## <a name="run-aspnet-core-on-an-owin-based-server-and-use-its-websockets-support"></a>ASP.NET Core OWIN 기반 서버에서 실행 하 고 Websocket 지원을 사용 하 여

또 다른 예로, OWIN 기반 서버의 기능을 활용할 수 ASP.NET 핵심은 Websocket와 같은 기능에 액세스할 수 있습니다. 이전 예제에서 사용 되는.NET OWIN 웹 서버는 ASP.NET 핵심 응용 프로그램에서 활용할 수 있는 기본 제공 되는 웹 소켓 지원 합니다. 다음 예제에서는 웹 소켓을 지원 하 고 Websocket 통해 서버에 보낸 모든 다시 에코 하는 간단한 웹 앱을 보여 줍니다.

<!-- literal_block {"ids": [], "names": [], "highlight_args": {"hl_lines": [7, 9, 10], "linenostart": 1}, "backrefs": [], "dupnames": [], "linenos": true, "classes": [], "xml:space": "preserve", "language": "c#", "source": "/Users/shirhatti/src/Docs/aspnet/fundamentals/owin/sample/src/NowinWebSockets/Startup.cs"} -->

```csharp
public class Startup
{
    public void Configure(IApplicationBuilder app)
    {
        app.Use(async (context, next) =>
        {
            if (context.WebSockets.IsWebSocketRequest)
            {
                WebSocket webSocket = await context.WebSockets.AcceptWebSocketAsync();
                await EchoWebSocket(webSocket);
            }
            else
            {
                await next();
            }
        });

        app.Run(context =>
        {
            return context.Response.WriteAsync("Hello World");
        });
    }

    private async Task EchoWebSocket(WebSocket webSocket)
    {
        byte[] buffer = new byte[1024];
        WebSocketReceiveResult received = await webSocket.ReceiveAsync(
            new ArraySegment<byte>(buffer), CancellationToken.None);

        while (!webSocket.CloseStatus.HasValue)
        {
            // Echo anything we receive
            await webSocket.SendAsync(new ArraySegment<byte>(buffer, 0, received.Count), 
                received.MessageType, received.EndOfMessage, CancellationToken.None);

            received = await webSocket.ReceiveAsync(new ArraySegment<byte>(buffer), 
                CancellationToken.None);
        }

        await webSocket.CloseAsync(webSocket.CloseStatus.Value, 
            webSocket.CloseStatusDescription, CancellationToken.None);
    }
}
```

이 [샘플](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/owin/sample) 동일한를 사용 하 여 구성 된 `NowinServer` 유일한 차이점은 이전과-응용 프로그램의 구성 방식에 해당 `Configure` 메서드. 사용 하 여 테스트 [간단한 websocket 클라이언트](https://chrome.google.com/webstore/detail/simple-websocket-client/pfdhoblngboilpfeibdedpjgfnlcodoo?hl=en) 응용 프로그램을 보여 줍니다.

![웹 소켓 테스트 클라이언트](owin/_static/websocket-test.png)

## <a name="owin-environment"></a>OWIN 환경

사용 하 여 OWIN 환경 생성할 수는 `HttpContext`합니다.

<!-- literal_block {"ids": [], "names": [], "highlight_args": {}, "backrefs": [], "dupnames": [], "linenos": false, "classes": [], "xml:space": "preserve", "language": "c#"} -->

```csharp

   var environment = new OwinEnvironment(HttpContext);
   var features = new OwinFeatureCollection(environment);
   ```

## <a name="owin-keys"></a>OWIN 키

OWIN에 따라 달라 집니다는 `IDictionary<string,object>` HTTP 요청/응답 exchange 전체에서 정보를 전달 하는 개체입니다. ASP.NET Core 아래에 나열 된 키를 구현 합니다. 참조는 [기본 사양, 확장](http://owin.org/#spec), 및 [OWIN 키 지침 및 일반적인 키](http://owin.org/spec/spec/CommonKeys.html)합니다.

### <a name="request-data-owin-v100"></a>요청 데이터 (OWIN v1.0.0)

| 키               | 값 (형식) | 설명 |
| ----------------- | ------------ | ----------- |
| owin 합니다. RequestScheme | `String` |  |
| owin 합니다. RequestMethod  | `String` | |    
| owin 합니다. RequestPathBase  | `String` | |    
| owin 합니다. RequestPath | `String` | |     
| owin 합니다. RequestQueryString  | `String` | |    
| owin 합니다. RequestProtocol  | `String` | |    
| owin 합니다. RequestHeaders | `IDictionary<string,string[]>`  | |
| owin 합니다. RequestBody | `Stream`  | |

### <a name="request-data-owin-v110"></a>요청 데이터 (OWIN v1.1.0)

| 키               | 값 (형식) | 설명 |
| ----------------- | ------------ | ----------- |
| owin 합니다. 요청 Id | `String` | Optional |

### <a name="response-data-owin-v100"></a>응답 데이터 (OWIN v1.0.0)

| 키               | 값 (형식) | 설명 |
| ----------------- | ------------ | ----------- |
| owin 합니다. ResponseStatusCode | `int` | Optional |
| owin 합니다. ResponseReasonPhrase | `String` | Optional |
| owin 합니다. ResponseHeaders | `IDictionary<string,string[]>`  | |
| owin 합니다. ResponseBody | `Stream`  | |


### <a name="other-data-owin-v100"></a>다른 데이터 (OWIN v1.0.0)

| 키               | 값 (형식) | 설명 |
| ----------------- | ------------ | ----------- |
| owin 합니다. CallCancelled | `CancellationToken` |  |
| owin 합니다. 버전  | `String` | |   


### <a name="common-keys"></a>공통 키

| 키               | 값 (형식) | 설명 |
| ----------------- | ------------ | ----------- |
| ssl입니다. ClientCertificate | `X509Certificate` |  |
| ssl입니다. LoadClientCertAsync  | `Func<Task>` | |    
| 서버입니다. RemoteIpAddress  | `String` | |    
| 서버입니다. 포트 원격 포트 | `String` | |     
| 서버입니다. LocalIpAddress  | `String` | |    
| 서버입니다. LocalPort  | `String` | |    
| 서버입니다. IsLocal  | `bool` | |    
| 서버입니다. OnSendingHeaders  | `Action<Action<object>,object>` | |


### <a name="sendfiles-v030"></a>SendFiles v0.3.0

| 키               | 값 (형식) | 설명 |
| ----------------- | ------------ | ----------- |
| sendfile 합니다. SendAsync | 참조 [대리자 시그니처](http://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm) | 요청당 |


### <a name="opaque-v030"></a>불투명 v0.3.0

| 키               | 값 (형식) | 설명 |
| ----------------- | ------------ | ----------- |
| 불투명 합니다. 버전 | `String` |  |
| 불투명 합니다. 업그레이드 | `OpaqueUpgrade` | 참조 [대리자 시그니처](http://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm) |
| 불투명 합니다. 스트림 | `Stream` |  |
| 불투명 합니다. CallCancelled | `CancellationToken` |  |


### <a name="websocket-v030"></a>WebSocket v0.3.0

| 키               | 값 (형식) | 설명 |
| ----------------- | ------------ | ----------- |
| websocket 합니다. 버전 | `String` |  |
| websocket 합니다. 수락 | `WebSocketAccept` | 참조 [대리자 시그니처](http://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm) |
| websocket 합니다. AcceptAlt |  | 비-사양 |
| websocket 합니다. SubProtocol | `String` | 참조 [RFC6455 섹션 4.2.2](https://tools.ietf.org/html/rfc6455#section-4.2.2) 5.5 단계 |
| websocket 합니다. SendAsync | `WebSocketSendAsync` | 참조 [대리자 시그니처](http://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)  |
| websocket 합니다. ReceiveAsync | `WebSocketReceiveAsync` | 참조 [대리자 시그니처](http://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)  |
| websocket 합니다. CloseAsync | `WebSocketCloseAsync` | 참조 [대리자 시그니처](http://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)  |
| websocket 합니다. CallCancelled | `CancellationToken` |  |
| websocket 합니다. ClientCloseStatus | `int` | Optional |
| websocket 합니다. ClientCloseDescription | `String` | Optional |


## <a name="additional-resources"></a>추가 리소스

* [미들웨어](middleware.md)

* [서버](servers/index.md)

