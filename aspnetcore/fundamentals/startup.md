---
title: "ASP.NET 핵심에서 응용 프로그램 시작 | Microsoft 문서"
author: ardalis
description: "ASP.NET Core에 Startup 클래스에 설명합니다."
keywords: "ASP.NET Core, 시작 시 구성 방법, ConfigureServices 방법"
ms.author: tdykstra
manager: wpickett
ms.date: 02/29/2017
ms.topic: article
ms.assetid: 6538df00-4ec2-45e4-811a-d7ce2ee608ed
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/startup
translationtype: Machine Translation
ms.sourcegitcommit: 010b730d2716f9f536fef889bc2f767afb648ef4
ms.openlocfilehash: 108f1f8043081fdcadfd3ba6a6cf8e703f3c24d6
ms.lasthandoff: 03/23/2017

---
# <a name="application-startup-in-aspnet-core"></a>ASP.NET 핵심에서 응용 프로그램 시작

여 [Steve Smith](http://ardalis.com) 및 [Tom Dykstra](https://github.com/tdykstra/)

`Startup` 클래스는 응용 프로그램에 대 한 모든 요청을 처리 하는 요청 파이프라인을 구성 합니다.

## <a name="the-startup-class"></a>Startup 클래스

ASP.NET 핵심 응용 프로그램에서는 한 `Startup` 클래스입니다. 규칙에 따라는 `Startup` 클래스 이름은 "시작"입니다. 에 시작 클래스 이름을 지정 하는 `Main` 프로그램 [WebHostBuilderExtensions](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.hosting.webhostbuilderextensions) [ `UseStartup<TStartup>` ](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.hosting.webhostbuilderextensions#Microsoft_AspNetCore_Hosting_WebHostBuilderExtensions_UseStartup__1_Microsoft_AspNetCore_Hosting_IWebHostBuilder_) 메서드.

별도 정의할 수 있습니다 `Startup` 클래스를 서로 다른 환경 및 적절 한 런타임 시 선택 됩니다. 지정 하는 경우 `startupAssembly` WebHost 구성 또는 옵션에서 호스팅 해당 시작 어셈블리를 로드 되며 검색할는 `Startup` 또는 `Startup[Environment]` 유형입니다. 참조 [FindStartupType](https://github.com/aspnet/Hosting/blob/rel/1.1.0/src/Microsoft.AspNetCore.Hosting/Internal/StartupLoader.cs) 에서 `StartupLoader` 및 [여러 환경 작업](environments.md#startup-conventions)합니다. `UseStartup<TStartup>`것이 좋습니다.

`Startup` 클래스 생성자를 통해 제공 되는 종속성을 수락할 수 있는 [종속성 주입](dependency-injection.md)합니다. 사용할 수 있습니다 `IHostingEnvironment` 를 설정 하려면 [구성](configuration.md) 소스 및 `ILoggerFactory` 를 설정 하려면 [로깅](logging.md) 공급자입니다. 

`Startup` 클래스에 포함 해야는 `Configure` 메서드 및 선택적으로 포함할 수는 `ConfigureServices` 응용 프로그램 시작 될 때 호출 되는 메서드. 클래스를 포함할 수도 [이러한 메서드의 환경별 버전](environments.md#startup-conventions)합니다.

에 대 한 자세한 [응용 프로그램 시작 시 예외를 처리](error-handling.md#startup-exception-handling)합니다.

## <a name="the-configure-method"></a>Configure 메서드

`Configure` 메서드는 ASP.NET 응용 프로그램에서 HTTP 요청에 응답 하는 방법을 지정 하는 데 사용 됩니다. 요청 파이프라인을 추가 하 여 구성할 [미들웨어](middleware.md) 구성 요소는 `IApplicationBuilder` 종속성 주입 하 여 제공 되는 인스턴스.

기본 웹 사이트 서식 파일에서 다음 예제에서는 여러 확장 메서드를 지 원하는 파이프라인을 구성 하려면 사용 됩니다 [BrowserLink](http://vswebessentials.com/features/browserlink), 오류 페이지, 정적 파일, ASP.NET MVC 및 Id입니다.

[!code-csharp[주](../common/samples/WebApplication1/Startup.cs?highlight=8,9,10,14,17,19,21&start=58&end=84)]

각 `Use` 추가 하는 확장 메서드는 [미들웨어](middleware.md) 요청 파이프라인 구성 요소입니다. 예를 들어,는 `UseMvc` 확장 메서드를 추가 [라우팅](routing.md) 미들웨어 요청 파이프라인을 구성 하 고 [MVC](../mvc/index.md) 기본 처리기로 합니다.

사용 하는 방법에 대 한 자세한 내용은 `IApplicationBuilder`, 참조 [미들웨어](middleware.md)합니다.

추가 서비스와 같은 `IHostingEnvironment` 및 `ILoggerFactory` 메서드 시그니처의 지정할 수 있습니다 이러한 서비스를 수 있는 경우 [주입](dependency-injection.md) 사용할 수 있는 경우. 

## <a name="the-configureservices-method"></a>ConfigureServices 메서드

[ConfigureServices](https://docs.microsoft.com/en-us/aspnet/core/api/microsoft.aspnetcore.hosting.startupbase#Microsoft_AspNetCore_Hosting_StartupBase_ConfigureServices_Microsoft_Extensions_DependencyInjection_IServiceCollection_) 메서드는 선택 사항입니다; 하지만 사용 하는 경우 보다 먼저 호출 됩니다는 `Configure` (일부 기능을 추가 요청 파이프라인까지 유선 것은 전에) 런타임에서 메서드. [구성 옵션](configuration.md) 이 메서드에서 설정 됩니다.

상당한 설치 해야 하는 기능에 대 한 가지 `Add[Service]` 확장 메서드를 [IServiceCollection](https://docs.microsoft.com/en-us/aspnet/core/api/microsoft.extensions.dependencyinjection.iservicecollection)합니다. 이 예제에서는 기본 웹 사이트 서식 파일에서 Entity Framework, Id 및 MVC에 대 한 서비스를 사용 하 여 응용 프로그램을 구성 합니다.

[!code-csharp[주](../common/samples/WebApplication1/Startup.cs?highlight=4,7,11&start=40&end=55)]

서비스를 서비스 컨테이너에 추가 값을 사용 하면 통해 응용 프로그램 내에서 사용할 수 있는 [종속성 주입](dependency-injection.md)합니다.

## <a name="services-available-in-startup"></a>시작에 사용할 수 있는 서비스

ASP.NET Core 종속성 주입 응용 프로그램의 시작 하는 동안 응용 프로그램 서비스를 제공합니다. 적절 한 인터페이스를 매개 변수로 포함 하 여 이러한 서비스를 요청할 수 있습니다 프로그램 `Startup` 클래스의 생성자 또는 중 하나는 `Configure` 또는 `ConfigureServices` 메서드. 

각 메서드를 보면는 `Startup` 순서에서는 클래스에서 호출 된 다음 서비스를 요청할 수 있습니다 매개 변수로:

* 생성자에서: `IHostingEnvironment`,`ILoggerFactory`
* 에 `ConfigureServices` 메서드:`IServiceCollection`
* In the `Configure` method:  `IApplicationBuilder`, `IHostingEnvironment`, `ILoggerFactory`,`IApplicationLifetime`

## <a name="additional-resources"></a>추가 리소스

* [여러 환경 작업](environments.md)
* [미들웨어](middleware.md)
* [로깅](logging.md)
* [구성](configuration.md)

