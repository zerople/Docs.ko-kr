---
title: "ASP.NET Core 응용 프로그램 시작"
author: ardalis
description: "ASP.NET Core 시작 클래스에 설명합니다."
keywords: "ASP.NET Core, 시작, 구성 메서드, ConfigureServices 메서드"
ms.author: tdykstra
manager: wpickett
ms.date: 02/29/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/startup
ms.openlocfilehash: 16969386c55ae2fd2ab574c1799a765e74f59278
ms.sourcegitcommit: 4147d2d29ea50e7e9b87879c572ac2a9fb51798c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/15/2017
---
# <a name="application-startup-in-aspnet-core"></a>ASP.NET Core 응용 프로그램 시작

여 [Steve Smith](http://ardalis.com) 및 [Tom Dykstra](https://github.com/tdykstra/)

`Startup` 클래스 서비스 및 응용 프로그램의 요청 파이프라인을 구성 합니다. 

## <a name="the-startup-class"></a>시작 클래스입니다.

ASP.NET Core 응용 프로그램에서는 한 `Startup` 클래스입니다. 일반적으로는 `Startup` 클래스 이름이 "시작"입니다. 에 시작 클래스 이름을 지정 하는 `Main` 프로그램의 [WebHostBuilderExtensions](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.hosting.webhostbuilderextensions) [ `UseStartup<TStartup>` ](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.hosting.webhostbuilderextensions#Microsoft_AspNetCore_Hosting_WebHostBuilderExtensions_UseStartup__1_Microsoft_AspNetCore_Hosting_IWebHostBuilder_) 메서드. 참조 [호스팅](xref:fundamentals/hosting) 에 대 한 자세한 내용은 `WebHostBuilder`, 하기 전에 실행 되는 `Startup`합니다.

별도 정의한 `Startup` 다양 한 환경 및 적절 한 런타임 시 선택 하나에 대 한 클래스입니다. 지정 하는 경우 `startupAssembly` 에 [WebHost 구성](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/hosting?tabs=aspnetcore2x#configuring-a-host) 호스팅 옵션을 해당 시작 어셈블리를 로드 하 고 검색할 또는 `Startup` 또는 `Startup[Environment]` 유형입니다. 현재 환경 우선 순위가 지정 해당 이름 접미사 일치, 하면 앱에서 실행 되는 클래스는 *개발* 환경 모두 포함 하 고는 `Startup` 및 `StartupDevelopment` 클래스는 `StartupDevelopment` 클래스 됩니다. 사용. 참조 [FindStartupType](https://github.com/aspnet/Hosting/blob/rel/1.1.0/src/Microsoft.AspNetCore.Hosting/Internal/StartupLoader.cs) 에 `StartupLoader` 및 [여러 환경 작업](environments.md#startup-conventions)합니다.

또는 정의할 수 있습니다는 고정 `Startup` 호출 하 여 환경에 관계 없이 사용 되는 클래스 `UseStartup<TStartup>`합니다. 이는 권장되는 방법입니다.

`Startup` 클래스 생성자를 통해 제공 되는 종속성을 수락할 수 있는 [종속성 주입](xref:fundamentals/dependency-injection)합니다. 사용 하는 일반적인 방법은 것 `IHostingEnvironment` 를 설정 하려면 [구성](xref:fundamentals/configuration) 원본입니다.

`Startup` 클래스에 포함 해야 합니다는 `Configure` 메서드 및 선택적으로 포함할 수는 `ConfigureServices` 응용 프로그램을 시작할 때 호출 되는 메서드. 클래스를 포함할 수도 [이러한 메서드의 환경별 버전](xref:fundamentals/environments#startup-conventions)합니다. `ConfigureServices`있는 경우 전에 호출 됩니다 `Configure`합니다.

에 대 한 자세한 내용은 [응용 프로그램 시작 하는 동안 예외를 처리](xref:fundamentals/error-handling#startup-exception-handling)합니다.

## <a name="the-configureservices-method"></a>ConfigureServices 메서드

[ConfigureServices](https://docs.microsoft.com/en-us/aspnet/core/api/microsoft.aspnetcore.hosting.startupbase#Microsoft_AspNetCore_Hosting_StartupBase_ConfigureServices_Microsoft_Extensions_DependencyInjection_IServiceCollection_) 방법은 선택 사항 이지만 사용 하는 경우 전에 호출 됩니다는 `Configure` 웹 호스트에 의해 메서드. 웹 호스트 되기 전에 일부 서비스를 구성할 수 있습니다 ``Startup`` 메서드가 호출 되어 (참조 [호스팅](xref:fundamentals/hosting)). 규칙에 따라 [구성 옵션](xref:fundamentals/configuration) 이 메서드에서 설정 됩니다.

상당한 설치 해야 하는 기능에 대 한 없는 `Add[Service]` 에 확장 메서드 [IServiceCollection](https://docs.microsoft.com/en-us/aspnet/core/api/microsoft.extensions.dependencyinjection.iservicecollection)합니다. 이 예제에서는 기본 웹 사이트 서식 파일에서 서비스 Entity Framework, Id 및 MVC를 사용 하도록 앱을 구성 합니다.

[!code-csharp[Main](../common/samples/WebApplication1/Startup.cs?highlight=4,7,11&start=40&end=55)]

서비스 컨테이너에 서비스를 추가 하면 사용할 수를 통해 응용 프로그램 내에서 [종속성 주입](xref:fundamentals/dependency-injection)합니다.

## <a name="services-available-in-startup"></a>시작에 사용할 수 있는 서비스

종속성 주입 ASP.NET Core 응용 프로그램의 시작 하는 동안 서비스를 제공합니다. 에 매개 변수로 적절 한 인터페이스를 포함 하 여 이러한 서비스를 요청할 수 있습니다 프로그램 `Startup` 클래스의 생성자 나 해당 `Configure` 메서드. `ConfigureServices` 만 메서드에서 `IServiceCollection` 매개 변수 (하지만 있어 추가 매개 변수가 필요 하지 않습니다.이 컬렉션에서 검색할 수 있는 등록 된 서비스 있습니다).

다음은 일반적으로 요청 하는 서비스의 `Startup` 메서드:

* 생성자에서: `IHostingEnvironment`,`ILogger<Startup>`
* `ConfigureServices`:`IServiceCollection`
* `Configure`: `IApplicationBuilder`, `IHostingEnvironment`,`ILoggerFactory`

의해 추가 된 모든 서비스는 ``WebHostBuilder`` ``ConfigureServices`` 메서드를 요청할 수 있습니다는 ``Startup`` 클래스 생성자 나 해당 ``Configure`` 메서드. 사용 하 여 `WebHostBuilder` 서비스를 제공 하는 동안 필요한 `Startup` 메서드.

## <a name="the-configure-method"></a>Configure 메서드

`Configure` 메서드는 ASP.NET 응용 프로그램이 HTTP 요청에 응답 하는 방법을 지정 하는 데 사용 됩니다. 요청 파이프라인을 추가 하 여 구성할 [미들웨어](middleware.md) 구성 요소는 `IApplicationBuilder` 종속성 주입에서 제공 되는 인스턴스.

기본 웹 사이트 서식 파일에서 다음 예제에서는 여러 확장 메서드를 지 원하는 파이프라인을 구성 하 사용 됩니다 [BrowserLink](http://vswebessentials.com/features/browserlink), 오류 페이지, 정적 파일, ASP.NET MVC 및 Id입니다.

[!code-csharp[Main](../common/samples/WebApplication1/Startup.cs?highlight=8,9,10,14,17,19,21&start=58&end=84)]

각 `Use` 추가 하는 확장 메서드는 [미들웨어](xref:fundamentals/middleware) 요청 파이프라인 구성 요소입니다. 예를 들어,는 `UseMvc` 추가 하는 확장 메서드는 [라우팅](routing.md) 요청 파이프라인에 미들웨어를 구성 하 고 [MVC](xref:mvc/overview) 기본 처리기로 합니다.

사용 하는 방법에 대 한 자세한 내용은 `IApplicationBuilder`, 참조 [미들웨어](xref:fundamentals/middleware)합니다.

추가 서비스와 같은 `IHostingEnvironment` 및 `ILoggerFactory` 메서드 시그니처의 지정할 수 있습니다 이러한 서비스를 수는 쿼리에서 [삽입](dependency-injection.md) 사용 가능한 경우. 

## <a name="additional-resources"></a>추가 리소스

* [여러 환경 작업](xref:fundamentals/environments)
* [미들웨어](xref:fundamentals/middleware)
* [로깅](xref:fundamentals/logging)
* [구성](xref:fundamentals/configuration)
