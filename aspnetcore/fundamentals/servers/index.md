---
title: "ASP.NET Core의 웹 서버 구현"
author: tdykstra
description: "ASP.NET Core에 대한 웹 서버 Kestrel 및 WebListener를 소개합니다. 항목을 선택하는 방법 및 역방향 프록시 서버에서 항목을 사용할 시기에 대한 지침을 제공합니다."
keywords: "ASP.NET Core, IServer, 웹 서버, Kestrel, WebListener, 역방향 프록시"
ms.author: tdykstra
manager: wpickett
ms.date: 08/03/2017
ms.topic: article
ms.assetid: dba74f39-58cd-4dee-a061-6d15f7346959
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/servers/index
ms.openlocfilehash: 17124f1ef181a4f1572d9375ae8cd27ce8845016
ms.sourcegitcommit: 9cdbfd0d670d70b9c354216aabee260c52dad5ee
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/12/2017
---
# <a name="web-server-implementations-in-aspnet-core"></a>ASP.NET Core의 웹 서버 구현

작성자: [Tom Dykstra](https://github.com/tdykstra), [Steve Smith](https://ardalis.com/), [Stephen Halter](https://twitter.com/halter73) 및 [Chris Ross](https://github.com/Tratcher)

ASP.NET Core 응용 프로그램은 In-process HTTP 서버 구현을 통해 실행됩니다. 서버 구현은 HTTP 요청을 수신하고 `HttpContext`를 구성하는 [요청 기능](https://docs.microsoft.com/aspnet/core/fundamentals/request-features) 집합으로 응용 프로그램에 표시합니다.

ASP.NET Core는 다음 두 가지 서버 구현을 제공합니다.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

* [Kestrel](kestrel.md)은 플랫폼 간 비동기 I/O 라이브러리인 [libuv](https://github.com/libuv/libuv)를 기반으로 하는 플랫폼 간 HTTP 서버입니다.

* [HTTP.sys](httpsys.md)는 [Http.Sys 커널 드라이버](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx)를 기반으로 하는 Windows 전용 HTTP 서버입니다.

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

* [Kestrel](kestrel.md)은 플랫폼 간 비동기 I/O 라이브러리인 [libuv](https://github.com/libuv/libuv)를 기반으로 하는 플랫폼 간 HTTP 서버입니다.

* [WebListener](weblistener.md)는 [Http.Sys 커널 드라이버](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx)를 기반으로 하는 Windows 전용 HTTP 서버입니다.

---

## <a name="kestrel"></a>Kestrel

Kestrel은 기본적으로 ASP.NET Core 새 프로젝트 템플릿에 포함된 웹 서버입니다. 

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

Kestrel을 단독으로 사용하거나 IIS, Nginx 또는 Apache 같은 *역방향 프록시 서버*와 함께 사용할 수 있습니다. 역방향 프록시 서버는 인터넷에서 HTTP 요청을 수신하고 몇몇 사전 처리 후에 Kestrel에 전달합니다.

![Kestrel은 역방향 프록시 서버 없이 직접 인터넷과 통신합니다.](kestrel/_static/kestrel-to-internet2.png)

![Kestrel은 IIS, Nginx 또는 Apache 같은 역방향 프록시 서버를 통해 간접적으로 인터넷과 통신합니다.](kestrel/_static/kestrel-to-internet.png)

Kestrel이 내부 네트워크에만 노출되는 경우 역방향 프록시 서버를 사용하거나 사용하지 않는 구성을 사용할 수도 있습니다.

Kestrel을 역방향 프록시와 함께 사용할 경우에 대한 자세한 내용은 [Kestrel 소개](kestrel.md)를 참조하세요.

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

응용 프로그램이 내부 네트워크의 요청만 허용할 경우 Kestrel을 단독으로 사용할 수 있습니다.

![Kestrel은 내부 네트워크와 직접 통신합니다.](kestrel/_static/kestrel-to-internal.png)

응용 프로그램을 인터넷에 노출할 경우 IIS, Nginx 또는 Apache를 *역방향 프록시 서버*로 사용해야 합니다. 역방향 프록시 서버는 인터넷에서 HTTP 요청을 수신하고 다음 다이어그램과 같이 몇몇 사전 처리 후에 Kestrel에 전달합니다.

![Kestrel은 IIS, Nginx 또는 Apache 같은 역방향 프록시 서버를 통해 간접적으로 인터넷과 통신합니다.](kestrel/_static/kestrel-to-internet.png)

인터넷에서 트래픽에 노출되는 에지 배포에 역방향 프록시를 사용하는 가장 중요한 이유는 보안입니다. Kestrel 1.x 버전에는 공격에 대한 전체 방어 기능이 포함되어 있지 않습니다. 이 방어 기능에는 적절한 시간 제한, 크기 제한, 동시 연결 제한이 포함되지만 이것으로 제한되지 않습니다.

Kestrel을 역방향 프록시와 함께 사용할 경우에 대한 자세한 내용은 [Kestrel 소개](kestrel.md)를 참조하세요.

---

Kestrel 또는 [사용자 지정 서버 구현](#custom-servers)이 없으면 IIS, Nginx 또는 Apache를 사용할 수 없습니다. ASP.NET Core는 플랫폼 간에 일관되게 동작하도록 자체 프로세스로 실행되도록 고안되었습니다. IIS, Nginx 및 Apache에는 자체 시작 프로세스와 환경이 설명되어 있습니다. 이러한 항목을 직접 사용하려면 ASP.NET Core를 각 항목의 필요에 맞게 조정해야 합니다. Kestrel 같은 웹 서버 구현을 사용하면 ASP.NET Core가 시작 프로세스 및 환경을 제어할 수 있습니다. 따라서 ASP.NET Core를 IIS, Nginx 또는 Apache에 맞게 조정하는 대신 해당 웹 서버를 Kestrel에 대한 프록시 요청으로 설정하면 됩니다. 이렇게 설정하면 `Program.Main` 및 `Startup` 클래스는 배포하는 위치와 관계없이 근본적으로 동일할 수 있습니다.

### <a name="iis-with-kestrel"></a>IIS 및 Kestrel

IIS 또는 IIS Express를 ASP.NET Core에 대한 역방향 프록시로 사용할 경우, ASP.NET Core 응용 프로그램은 IIS 작업자 프로세스에서 분리된 프로세스로 실행됩니다. IIS 프로세스에서는 역방향 프록시 관계를 조정하기 위해 특수 IIS 모듈이 실행됩니다.  이것은 *ASP.NET Core 모듈*입니다. ASP.NET Core 모듈의 기본 함수는 ASP.NET Core 응용 프로그램을 시작하고, 작동 중단 시 해당 응용 프로그램을 다시 시작하고, 응용 프로그램에 HTTP 트래픽을 전달합니다. 자세한 내용은 [ASP.NET Core 모듈](aspnet-core-module.md)을 참조하세요. 

### <a name="nginx-with-kestrel"></a>Nginx 및 Kestrel

Linux에서 Kestrel에 대한 역방향 프록시 서버로 Nginx를 사용하는 방법에 대한 자세한 내용은 [Linux 프로덕션 환경에 게시](../../publishing/linuxproduction.md)를 참조하세요.

### <a name="apache-with-kestrel"></a>Apache 및 Kestrel

Linux에서 Kestrel에 대한 역방향 프록시 서버로 Apache를 사용하는 방법에 대한 자세한 내용은 [역방향 프록시로 Apache 웹 서버 사용](../../publishing/apache-proxy.md)을 참조하세요.

## <a name="httpsys"></a>HTTP.sys

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

Windows에서 ASP.NET Core 앱을 실행할 경우 Kestrel 대신 HTTP.sys를 사용할 수 있습니다. 인터넷에 앱을 노출하고 Kestrel이 지원하지 않는 HTTP.sys 기능을 사용해야 하는 시나리오의 경우 HTTP.sys를 사용할 수 있습니다. 

![HTTP.sys는 인터넷과 직접 통신합니다.](httpsys/_static/httpsys-to-internet.png)

HTTP.sys는 내부 네트워크에만 노출되는 응용 프로그램에도 사용할 수 있습니다. 

![HTTP.sys는 내부 네트워크와 직접 통신합니다.](httpsys/_static/httpsys-to-internal.png)

내부 네트워크 시나리오의 경우 최고의 성능을 얻기 위해 일반적으로 Kestrel이 권장되지만 일부 시나리오에서는 HTTP.sys만 제공하는 기능을 사용해야 할 수 있습니다. HTTP.sys 기능에 대한 자세한 내용은 [HTTP.sys](httpsys.md)를 참조하세요.

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

ASP.NET Core 1.x에서는 HTTP.sys의 이름이 WebListener입니다. Windows에서 ASP.NET Core 앱을 실행할 경우 앱을 인터넷에 노출하려고 하지만 IIS를 사용할 수 없는 시나리오의 경우 WebListener를 대신 사용할 수 있습니다.

![WebListener는 인터넷과 직접 통신합니다.](weblistener/_static/weblistener-to-internet.png)

Kestrel이 지원하지 않는 WebListener가 필요한 경우 내부 네트워크에만 노출되는 응용 프로그램에는 Kestrel 대신 WebListener를 사용할 수 있습니다. 

![WebListener는 내부 네트워크와 직접 통신합니다.](weblistener/_static/weblistener-to-internal.png)

내부 네트워크 시나리오의 경우 성능을 극대화하기 위해 일반적으로 Kestrel이 권장되지만, 일부 시나리오에서는 WebListener만 제공하는 기능을 사용할 수 있습니다. WebListener 기능에 대한 자세한 내용은 [WebListener](weblistener.md)를 참조하세요.

---

## <a name="notes-about-aspnet-core-server-infrastructure"></a>ASP.NET Core 서버 인프라에 대한 참고

`Startup` 클래스 `Configure` 메서드에서 사용 가능한 [`IApplicationBuilder`](https://docs.microsoft.com/aspnet/core/api)는 [`IFeatureCollection`](https://docs.microsoft.com/aspnet/core/api) 형식의 `ServerFeatures` 속성을 노출합니다. Kestrel 및 WebListener는 둘 다 단일 기능인 [`IServerAddressesFeature`](https://docs.microsoft.com/aspnet/core/api)만 노출하지만, 다른 서버 구현이 추가 기능을 노출할 수 있습니다.

`IServerAddressesFeature`를 사용하여 런타임에 서버 구현이 바인딩된 포트를 확인할 수 있습니다.

## <a name="custom-servers"></a>사용자 지정 서버

기본 제공 서버가 필요에 맞지 않으면 사용자 지정 서버 구현을 만들 수 있습니다. [OWIN(Open Web Interface for .NET) 가이드](../owin.md)에서는 [Nowin](https://github.com/Bobris/Nowin) 기반 [IServer](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.hosting.server.iserver) 구현을 작성하는 방법을 보여 줍니다. 응용 프로그램에 필요한 기능 인터페이스만 자유롭게 구현할 수 있습니다. 단, 최소한 [IHttpRequestFeature](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.http.features.ihttprequestfeature) 및 [IHttpResponseFeature](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.http.features.ihttpresponsefeature)를 지원해야 합니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 리소스를 참조하세요.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

- [Kestrel](kestrel.md)
- [Kestrel 및 IIS](aspnet-core-module.md)
- [Kestrel 및 Nginx](../../publishing/linuxproduction.md)
- [Kestrel 및 Apache](../../publishing/apache-proxy.md)
- [HTTP.sys](httpsys.md)

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

- [Kestrel](kestrel.md)
- [Kestrel 및 IIS](aspnet-core-module.md)
- [Kestrel 및 Nginx](../../publishing/linuxproduction.md)
- [Kestrel 및 Apache](../../publishing/apache-proxy.md)
- [WebListener](weblistener.md)

---
