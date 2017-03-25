---
title: "ASP.NET Core의 서버 구현을 웹 | Microsoft 문서"
author: tdykstra
description: "ASP.NET Core에 대 한 웹 서버 Kestrel 및 WebListener를 소개합니다. 하나를 선택 하는 방법에 지침을 제공 하 고 역방향 프록시 서버와 하나를 사용 하는 경우."
keywords: "ASP.NET Core, IServer, 웹 서버, Kestrel, WebListener, 역방향 프록시"
ms.author: tdykstra
manager: wpickett
ms.date: 10/27/2016
ms.topic: article
ms.assetid: dba74f39-58cd-4dee-a061-6d15f7346959
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/servers/index
translationtype: Machine Translation
ms.sourcegitcommit: f93c93002fec0088a7040cd53f31fd5b5a62fea7
ms.openlocfilehash: fba8d4d9a2a1eedeed2e7f720c8ff4a24acdaaf8
ms.lasthandoff: 03/23/2017

---
# <a name="web-server-implementations-in-aspnet-core"></a>ASP.NET Core에서 웹 서버 구현

여 [Tom Dykstra](http://github.com/tdykstra), [Steve Smith](http://ardalis.com), [Stephen Halter](https://twitter.com/halter73), 및 [Chris Ross](https://github.com/Tratcher) 

ASP.NET 핵심 응용 프로그램 프로세스에서 HTTP 서버 구현으로 실행 됩니다. HTTP 요청 및 집합으로 응용 프로그램에이 표시에 대 한 수신 대기 하는 서버 구현 [기능을 요청 하려면](https://docs.asp.net/en/latest/fundamentals/request-features.html) 작성할는 `HttpContext`합니다.

ASP.NET 핵심에는 두 가지 서버 구현이 제공 됩니다.

* [Kestrel](kestrel.md) 기반으로 플랫폼 간 HTTP 서버 [libuv](https://github.com/libuv/libuv), 플랫폼 간 비동기 I/O 라이브러리입니다.

* [WebListener](weblistener.md) 기반으로 Windows 전용 HTTP 서버에서 [Http.Sys 커널 드라이버](https://msdn.microsoft.com/en-us/library/windows/desktop/aa364510.aspx)합니다.

## <a name="kestrel"></a>Kestrel

Kestrel은 ASP.NET 핵심 새 프로젝트 템플릿에서 기본적으로 포함 된 웹 서버입니다. 응용 프로그램에서 내부 네트워크 에서만에서 요청을 허용 하는 경우에 자체적으로 Kestrel를 사용할 수 있습니다.

![내부 네트워크에 kestrel](overview/_static/kestrel-to-internal.png)

인터넷 응용 프로그램을 노출 하는 경우 IIS, Nginx, 또는으로 Apache를 사용 하는 것이 좋습니다는 *역방향 프록시 서버*합니다. 역방향 프록시 서버는 인터넷에서 HTTP 요청을 받고을 전달 Kestrel 일부 예비 처리 한 후 다음 다이어그램에 표시 된 것 처럼.

![인터넷에 kestrel](overview/_static/kestrel-to-internet.png)

가장자리 배포 (인터넷에서 트래픽에 노출 됨)에 대 한 역방향 프록시를 사용 하 여 가장 중요 한 이유는 보안입니다. Kestrel은 비교적 새로운 및 완벽 한 공격에 대 한 방어 아직 없습니다. 이 포함 되어 있지만 적절 한 시간 제한, 크기 제한 및 동시 연결 제한으로 제한 되지 않습니다. Kestrel 역방향 프록시를 사용 하는 경우에 대 한 자세한 내용은 참조 [Kestrel](kestrel.md)합니다.

IIS, Nginx, 또는 Apache Kestrel 없이 사용할 수 없습니다 또는 [사용자 지정 서버 구현](#custom-servers)합니다. ASP.NET Core는 플랫폼 간에 일관성 있게 동작 수 있도록 자체 프로세스에서 실행 하도록 설계 되었습니다. IIS, Nginx 및 Apache 자신의 시작 프로세스 및 환경에 따라 결정 사용법이 직접 ASP.NET 핵심 해야 각각의 요구에 맞게 조정 합니다. Kestrel와 같은 웹 서버 구현을 사용 하 여 시작 프로세스 및 환경에 대 한 ASP.NET 핵심 제어를 제공 합니다. 따라서 ASP.NET 핵심 IIS, Nginx, 또는 Apache에 적응 하는 동안, 대신 방금 설정한 Kestrel에 대 한 프록시 요청에 해당 웹 서버입니다. 이 정렬을 사용 하면 프로그램 `Program.Main` 및 `Startup` 클래스가 기본적으로 배포 하는 위치에 관계 없이 동일 합니다.

### <a name="iis-with-kestrel"></a>Kestrel 포함 하 여 IIS

ASP.NET Core에 대 한 역방향 프록시로 IIS 또는 IIS Express를 사용 하는 경우 ASP.NET 핵심 응용 프로그램 프로세스 별도 IIS 작업자 프로세스에서 실행 됩니다. IIS 프로세스에서 역방향 프록시 관계를 조정 하는 특수 한 IIS 모듈인 실행 됩니다.  이 *ASP.NET 핵심 모듈*합니다. ASP.NET 핵심 모듈의 기본 기능, ASP.NET 핵심 응용 프로그램을 시작 하 고, 충돌 했을 때 다시 시작 하 고, HTTP 트래픽을 전달 되도록 합니다. 자세한 내용은 참조 [ASP.NET 핵심 모듈](aspnet-core-module.md)합니다. 

### <a name="nginx-with-kestrel"></a>Nginx Kestrel와

Linux에서 Kestrel에 대 한 역방향 프록시 서버로 Nginx를 사용 하는 방법에 대 한 정보를 참조 하십시오. [Linux 프로덕션 환경에 게시](../../publishing/linuxproduction.md)합니다.

### <a name="apache-with-kestrel"></a>Kestrel으로 Apache를

Apache Kestrel에 대 한 역방향 프록시 서버와 Linux에서 사용 하는 방법에 대 한 정보를 참조 하십시오. [역방향 프록시로 Apache 웹 서버를 사용 하 여](../../publishing/apache-proxy.md)합니다.

## <a name="weblistener"></a>WebListener

Windows에서는 WebListener 응용 프로그램은 대신에 사용할 수 있는 ASP.NET 핵심 프로그램을 실행 하는 경우 앱 수 있지만 인터넷에 노출 하려는 시나리오는 IIS를 사용할 수 없습니다. 

![WebListener](overview/_static/weblistener-to-internet.png)

또한 WebListener Kestrel를 지원 하지 않는 기능 중 하나를 해야 하는 경우 내부 네트워크에만 노출 되는 응용 프로그램에 대 한 Kestrel 대신 사용할 수 있습니다. 

![WebListener](overview/_static/weblistener-to-internal.png)

내부 네트워크 시나리오에 대 한 Kestrel 최상의 성능을 위해 일반적으로 권장 하지만 일부 시나리오에서는 있습니다 하려는 WebListener만 제공 하는 기능을 사용 합니다. WebListener 기능에 대 한 정보를 참조 하십시오. [WebListener](weblistener.md)합니다.

## <a name="notes-about-aspnet-core-server-infrastructure"></a>ASP.NET 핵심 서버 인프라에 대 한 참고 사항

[ `IApplicationBuilder` ](http://docs.asp.net/projects/api/en/latest/autoapi/Microsoft/AspNetCore/Builder/IApplicationBuilder/index.html#Microsoft.AspNetCore.Builder.IApplicationBuilder.md) 에서 사용할 수는 `Startup` 클래스 `Configure` 메서드 노출은 `ServerFeatures` 형식의 속성 [ `IFeatureCollection` ](http://docs.asp.net/projects/api/en/latest/autoapi/Microsoft/AspNetCore/Http/Features/IFeatureCollection/index.html#Microsoft.AspNetCore.Http.Features.IFeatureCollection.md)합니다. 단일 기능을 노출 kestrel 및 WebListener [ `IServerAddressesFeature` ](http://docs.asp.net/projects/api/en/latest/autoapi/Microsoft/AspNetCore/Hosting/Server/Features/IServerAddressesFeature/index.html#Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature.md), 다른 서버 구현에는 추가 기능 노출 될 수 있습니다.

`IServerAddressesFeature`용도 서버 구현에 런타임 시에 바인딩된 포트를 찾으려고 합니다.

## <a name="custom-servers"></a>사용자 지정 서버

Kestrel 또는 WebListener 대신 사용 하 여 사용자 지정 서버 구현을 만들 수 있습니다. [.NET (OWIN) 가이드에 대 한 공개 웹 인터페이스](../owin.md) 작성 하는 방법을 보여 줍니다는 [Nowin](https://github.com/Bobris/Nowin)-기반 [ `IServer` ](http://docs.asp.net/projects/api/en/latest/autoapi/Microsoft/AspNetCore/Hosting/Server/IServer/index.html#Microsoft.AspNetCore.Hosting.Server.IServer.md) 구현 합니다. 에 최소한을 지원 해야 하지만 바로 응용 프로그램에 필요한 기능 인터페이스를 구현 하도록 [ `IHttpRequestFeature` ](http://docs.asp.net/projects/api/en/latest/autoapi/Microsoft/AspNetCore/Http/Features/IHttpRequestFeature/index.html#Microsoft.AspNetCore.Http.Features.IHttpRequestFeature.md) 및 [ `IHttpResponseFeature` ](http://docs.asp.net/projects/api/en/latest/autoapi/Microsoft/AspNetCore/Http/Features/IHttpResponseFeature/index.html#Microsoft.AspNetCore.Http.Features.IHttpResponseFeature.md)합니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 리소스를 참조하세요.

- [Kestrel](kestrel.md)
- [IIS와 kestrel](aspnet-core-module.md)
- [Nginx와 kestrel](../../publishing/linuxproduction.md)
- [Apache kestrel](../../publishing/apache-proxy.md)
- [WebListener](weblistener.md)

