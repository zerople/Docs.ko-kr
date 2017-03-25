---
title: "ASP.NET Core에서 웹 서버 구현이 kestrel | Microsoft 문서"
author: tdykstra
description: "Libuv를 기반으로 ASP.NET 핵심 위한 Kestrel, 플랫폼 간 웹 서버를 소개 합니다."
keywords: "ASP.NET Core, Kestrel, libuv, url 접두사"
ms.author: tdykstra
manager: wpickett
ms.date: 10/27/2016
ms.topic: article
ms.assetid: 560bd66f-7dd0-4e68-b5fb-f31477e4b785
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/servers/kestrel
ms.custom: H1Hack27Feb2017
translationtype: Machine Translation
ms.sourcegitcommit: f93c93002fec0088a7040cd53f31fd5b5a62fea7
ms.openlocfilehash: 7eaf1775f3147d0d620ee64f9beec69276767277
ms.lasthandoff: 03/23/2017

---
# <a name="introduction-to-kestrel-web-server-implementation-in-aspnet-core"></a>ASP.NET Core에서 Kestrel 웹 서버 구현 소개

여 [Tom Dykstra](http://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher), 및 [Stephen Halter](https://twitter.com/halter73)

Kestrel는 플랫폼 간 [ASP.NET 핵심에 대 한 웹 서버](index.md) 기반 [libuv](https://github.com/libuv/libuv), 플랫폼 간 비동기 I/O 라이브러리입니다. Kestrel은 ASP.NET 핵심 새 프로젝트 템플릿에서 기본적으로 포함 된 웹 서버입니다. 

Kestrel은 다음과 같은 기능을 지원합니다.

  * HTTPS
  * 사용 하도록 설정 하는 데 사용 되는 불투명 업그레이드 [WebSockets](https://github.com/aspnet/websockets)
  * Nginx 뒤 고성능 Unix 소켓 

Kestrel 모든 플랫폼에서 지 원하는.NET Core 버전에서 지원 됩니다.

[샘플 코드 보기 또는 다운로드](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/sample)

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a>Kestrel 역방향 프록시를 사용 하는 경우

응용 프로그램에서 내부 네트워크 에서만에서 요청을 허용 하는 경우에 자체적으로 Kestrel를 사용할 수 있습니다.

![내부 네트워크에 kestrel](kestrel/_static/kestrel-to-internal.png)

IIS, Nginx, 또는 Apache로 사용 해야 인터넷에 응용 프로그램을 노출 하는 경우는 *역방향 프록시 서버*합니다. 역방향 프록시 서버는 인터넷에서 HTTP 요청을 받고을 일부 예비 처리 후 Kestrel 전달 합니다.

![인터넷에 kestrel](kestrel/_static/kestrel-to-internet.png)

역방향 프록시는 보안상의 이유로 가장자리 배포 (인터넷에서 트래픽에 노출 됨)에 필요 합니다. Kestrel은 비교적 새로운 및 완벽 한 공격에 대 한 방어 아직 없습니다. 이 포함 되어 있지만 적절 한 시간 제한, 크기 제한 및 동시 연결 제한으로 제한 되지 않습니다.

역방향 프록시를 필요로 하는 또 다른 시나리오는 단일 서버에서 실행 되는 동일한 포트를 공유 하는 여러 응용 프로그램을 사용 하는 경우. 작동 하지 않습니다 Kestrel와 직접 Kestrel 여러 프로세스 간에 포트 공유를 지원 하지 않습니다. 포트에서 수신 하도록 Kestrel를 구성할 때 호스트 헤더에 관계 없이 해당 포트에 대 한 모든 트래픽을 처리 합니다. 역방향 프록시 포트를 공유할 수 있는 고유한 포트에서 Kestrel에 다음 전달 해야 합니다.

역방향 프록시 서버는 필요 하지 않습니다, 경우에 하나를 사용 하 여 및를 간소화할 수 부하 분산 및 SSL 설정-역방향 프록시 서버만 필요한 SSL 인증서를 해당 서버는 일반 HTTP를 사용 하 여 내부 네트워크에 응용 프로그램 서버와 통신할 수 있습니다.

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a>Kestrel ASP.NET 핵심 응용 프로그램에서 사용 하는 방법

설치는 [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) NuGet 패키지입니다.

호출의 [ `UseKestrel` ](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions#Microsoft_AspNetCore_Hosting_WebHostBuilderKestrelExtensions_UseKestrel_Microsoft_AspNetCore_Hosting_IWebHostBuilder_) 대 한 확장 메서드 `WebHostBuilder` 에 프로그램 `Main` 메서드를 지정 하 [Kestrel 옵션](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.server.kestrel.kestrelserveroptions) 는 다음 예제와 같이 필요한:

[!code-csharp[](kestrel/sample/Program.cs?name=snippet_Main&highlight=13-19)]

### <a name="url-prefixes"></a>URL 접두사

ASP.NET 핵심이 바인딩됩니다 기본적으로 `http://localhost:5000`합니다. URL 접두사와 포트를 사용 하 여에서 수신 하도록 Kestrel 구성할 수 있습니다는 `UseUrls` 확장 메서드는 `urls` 명령줄 인수 또는 ASP.NET 핵심 구성 시스템입니다. 이러한 메서드에 대 한 자세한 내용은 참조 [호스팅](../../fundamentals/hosting.md)합니다. 역방향 프록시로 IIS를 사용 하는 경우 URL 바인딩을 작동 하는 방법에 대 한 정보를 참조 하십시오. [ASP.NET 핵심 모듈](aspnet-core-module.md)합니다. 

Kestrel에 대 한 URL 접두사 다음 형식 중 하나가 될 수 있습니다. 

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


* 포트 번호와 호스트 이름

  ```
  http://contoso.com:80/
  http://*:80/
  https://contoso.com:443/
  https://*:443/
  ```

  호스트 이름, *, 및 +은 특별 하지 않습니다. 이외의 모든 IP 주소 또는 "localhost"를 인식 되는 모든 IPv4 및 IPv6 Ip에 바인딩합니다. 사용 하 여 서로 다른 호스트 이름을 서로 다른 ASP.NET 핵심 응용 프로그램에서 동일한 포트를 바인딩할 해야 할 경우 [WebListener](weblistener.md) 또는 IIS, Nginx, 또는 Apache 같은 역방향 프록시 서버입니다.

* 포트 번호 또는 루프백 ip 포트 번호로 "Localhost" 이름

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  때 `localhost` 지정, Kestrel IPv4 및 IPv6 모두 루프백 인터페이스에 바인딩을 시도 합니다. 요청 된 포트를 루프백 인터페이스 중 하나에서 다른 서비스에서 사용 중인 Kestrel 시작 되지 않습니다. 루프백 인터페이스나 사용할 수 없으면 다른 이유로 대부분 IPv6 지원 되지 않기 때문에 일반적으로, Kestrel 경고를 기록 합니다. 

* Unix 소켓

  ```
  http://unix:/run/dan-live.sock  
  ```

포트 번호 0을 지정 하면 Kestrel는 동적으로 사용 가능한 포트를 바인딩합니다. 호스트 이름 또는 IP를 제외 하 고 사용할 수는 0 포트에 바인딩 `localhost` 이름입니다.

사용할 수 있습니다 포트 0을 지정 하면 [ `IServerAddressesFeature` ](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.hosting.server.features.iserveraddressesfeature) Kestrel 실제로 런타임에 바인딩되는 포트를 확인 하려면. 다음 예제에서는 바인딩된 포트를 가져와서 콘솔에 표시 됩니다.

[!code-csharp[](kestrel/sample/Startup.cs?name=snippet_Configure)]

### <a name="url-prefixes-for-ssl"></a>SSL에 대 한 URL 접두사

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

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 리소스를 참조하세요.

* [이 문서에 대 한 샘플 응용 프로그램](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/sample)
* [Kestrel 소스 코드](https://github.com/aspnet/KestrelHttpServer)
* [첫 번째 ASP.NET 핵심 응용 프로그램 Visual Studio 코드를 사용 하 여 Mac에서.](../../tutorials/your-first-mac-aspnet.md)

  이 자습서 사용 하 여 Kestrel 자체로 로컬로 후 역방향 프록시 서버와 IIS를 사용 하 여 Windows에서 실행 되는 Azure에 앱을 배포 합니다.

