---
title: "ASP.NET Core 기본 사항"
author: rick-anderson
description: "이 문서에서는 ASP.NET Core 응용 프로그램을 빌드할 때 이해해야 하는 기본 개념의 간략한 개요를 제공합니다."
keywords: "ASP.NET Core, 기본 사항, 개요"
ms.author: riande
manager: wpickett
ms.date: 08/18/2017
ms.topic: get-started-article
ms.assetid: a19b7836-63e4-44e8-8250-50d426dd1070
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/index
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5d8ca35b0e2e4b6e9b1ec745a3a7cf7c3983c461
ms.sourcegitcommit: 9cdbfd0d670d70b9c354216aabee260c52dad5ee
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/12/2017
---
# <a name="aspnet-core-fundamentals-overview"></a>ASP.NET Core 기본 사항 개요

ASP.NET Core 응용 프로그램은 `Main` 메서드에서 웹 서버를 만드는 콘솔 앱입니다.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

[!code-csharp[Main](../getting-started/sample/aspnetcoreapp/Program2x.cs)]

`Main` 메서드는 빌드 패턴에 따라 웹 응용 프로그램 호스트를 만드는 `WebHost.CreateDefaultBuilder`를 호출합니다. 빌더에는 웹 서버(예: `UseKestrel`) 및 시작 클래스(`UseStartup`)를 정의하는 메서드가 있습니다. 이전 예제에서 [Kestrel](xref:fundamentals/servers/kestrel) 웹 서버는 자동으로 할당됩니다. ASP.NET Core의 웹 호스트는 IIS(사용 가능한 경우)에서 실행하려고 시도합니다. [HTTP.sys](xref:fundamentals/servers/httpsys) 같은 다른 웹 서버는 해당하는 확장 메서드를 호출하여 사용할 수 있습니다. `UseStartup`은 다음 섹션에서 추가로 설명합니다.

`WebHost.CreateDefaultBuilder` 호출의 반환 형식인 `IWebHostBuilder`는 다양한 선택적 메서드를 제공합니다. 이러한 일부 메서드에는 응용 프로그램을 HTTP.sys에서 호스트하기 위한 `UseHttpSys` 및 루트 콘텐츠 디렉터리를 지정하기 위한 `UseContentRoot`가 포함됩니다. `Build` 및 `Run` 메서드는 응용 프로그램을 호스트하고 HTTP 요청을 수신하기 시작할 `IWebHost` 개체를 빌드합니다.

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

[!code-csharp[Main](../getting-started/sample/aspnetcoreapp/Program.cs)]

`Main` 메서드는 빌드 패턴에 따라 웹 응용 프로그램 호스트를 만드는 `WebHostBuilder`를 사용합니다. 빌더에는 웹 서버(예: `UseKestrel`) 및 시작 클래스(`UseStartup`)를 정의하는 메서드가 있습니다. 이전 예제에서는 [Kestrel](xref:fundamentals/servers/kestrel) 웹 서버가 사용됩니다. [WebListener](xref:fundamentals/servers/weblistener) 같은 다른 웹 서버는 해당하는 확장 메서드를 호출하여 사용할 수 있습니다. `UseStartup`은 다음 섹션에서 추가로 설명합니다.

`WebHostBuilder`는 IIS 및 IIS Express를 호스트하기 위한 `UseIISIntegration` 및 루트 콘텐츠 디렉터리를 지정하기 위한 `UseContentRoot`를 포함한 다양한 선택적 메서드를 제공합니다. `Build` 및 `Run` 메서드는 응용 프로그램을 호스트하고 HTTP 요청을 수신하기 시작할 `IWebHost` 개체를 빌드합니다.

---

## <a name="startup"></a>시작

`WebHostBuilder`의 `UseStartup` 메서드는 앱에 대한 `Startup` 클래스를 지정합니다.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

[!code-csharp[Main](../getting-started/sample/aspnetcoreapp/Program2x.cs?highlight=10&range=6-17)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

[!code-csharp[Main](../getting-started/sample/aspnetcoreapp/Program.cs?highlight=7&range=6-17)]

---

`Startup` 클래스는 요청 처리 파이프라인을 정의하고 응용 프로그램에 필요한 서비스를 구성하는 위치입니다. `Startup` 클래스는 공용이어야 하고 다음 메서드를 포함합니다.

```csharp
public class Startup
{
    // This method gets called by the runtime. Use this method to add services to the container.
    public void ConfigureServices(IServiceCollection services)
    {
    }

    // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
    public void Configure(IApplicationBuilder app)
    {
    }
}
```

* `ConfigureServices`는 응용 프로그램에서 사용되는 [서비스](#services)를 정의합니다(예: ASP.NET Core MVC, Entity Framework Core, ID 등).

* `Configure`는 요청 파이프라인에서 [미들웨어](xref:fundamentals/middleware)를 정의합니다.

자세한 내용은 [응용 프로그램 시작](xref:fundamentals/startup)을 참조하세요.

## <a name="services"></a>서비스

서비스는 응용 프로그램에서 공통으로 사용해야 하는 구성 요소입니다. 서비스는 DI([종속성 주입](xref:fundamentals/dependency-injection))를 통해 생성됩니다. ASP.NET Core에는 기본적으로 [생성자 주입](xref:mvc/controllers/dependency-injection#constructor-injection)을 지원하는 네이티브 IoC(Inversion of Control) 컨테이너가 포함됩니다. 네이티브 컨테이너는 선택한 컨테이너로 바꿀 수 있습니다. 느슨한 결합의 이점 이외에 DI는 응용 프로그램 전체에서 서비스를 사용할 수 있게 만듭니다. 예를 들어 [로깅](xref:fundamentals/logging)은 응용 프로그램 전체에서 사용할 수 있습니다.

자세한 내용은 [종속성 주입](xref:fundamentals/dependency-injection)을 참조하세요.

## <a name="middleware"></a>미들웨어

ASP.NET Core에서 [미들웨어](xref:fundamentals/middleware)를 사용하여 요청 파이프라인을 작성합니다. ASP.NET Core 미들웨어는 `HttpContext`에서 비동기 논리를 수행하고 나서 시퀀스에서 다음 미들웨어를 호출하거나 요청을 직접 종료합니다. “XYZ”라는 미들웨어 구성 요소는 `Configure` 메서드에서 `UseXYZ` 확장 메서드를 호출하여 추가됩니다.

ASP.NET Core는 다양한 기본 제공 미들웨어 집합이 함께 제공됩니다.

* [정적 파일](xref:fundamentals/static-files)

* [라우팅](xref:fundamentals/routing)

* [인증](xref:security/authentication/index)

ASP.NET Core에서 [OWIN](http://owin.org) 기반 미들웨어를 사용할 수 있고 자체 사용자 지정 미들웨어를 구성할 수 있습니다.

자세한 내용은 [미들웨어](xref:fundamentals/middleware) 및 [OWIN(Open Web Interface for .NET)](xref:fundamentals/owin)을 참조하세요.

## <a name="servers"></a>서버

ASP.NET Core 호스팅 모델은 직접 요청을 수신하지 않고, 오히려 HTTP 서버 구현을 사용하여 요청을 응용 프로그램에 전달합니다. 전달된 요청은 인터페이스를 통해 액세스할 수 있는 기능 개체 집합으로 래핑됩니다. 응용 프로그램은 이 집합을 `HttpContext`로 구성합니다. ASP.NET Core에는 [Kestrel](xref:fundamentals/servers/kestrel)이라는 관리되는 플랫폼 간 웹 서버가 포함됩니다. 일반적으로 Kestrel은 [IIS](https://www.iis.net/) 또는 [nginx](http://nginx.org) 같은 프로덕션 웹 서버의 백그라운드에서 실행됩니다.

자세한 내용은 [서버](xref:fundamentals/servers/index) 및 [호스팅](xref:fundamentals/hosting)을 참조하세요.

## <a name="content-root"></a>콘텐츠 루트

콘텐츠 루트는 앱에서 사용되는 뷰, [Razor 페이지](xref:mvc/razor-pages/index) 및 정적 자산 같은 콘텐츠의 기본 경로입니다. 기본적으로 콘텐츠 루트는 응용 프로그램을 호스트하는 실행 파일의 응용 프로그램 기본 경로와 동일합니다. 콘텐츠 루트의 대체 위치는 `WebHostBuilder`를 사용하여 지정됩니다.

## <a name="web-root"></a>웹 루트

응용 프로그램의 웹 루트는 CSS, JavaScript 및 이미지 파일 같은 공용 정적 리소스를 포함하는 프로젝트의 디렉터리입니다. 기본적으로 정적 파일 미들웨어는 웹 루트 디렉터리 및 하위 디렉터리에 있는 파일만 지원합니다. 자세한 내용은 [정적 파일 작업](xref:fundamentals/static-files)을 참조하세요. 웹 루트 경로는 기본적으로 */wwwroot*로 설정되지만, `WebHostBuilder`를 사용하여 다른 위치를 지정할 수 있습니다.

## <a name="configuration"></a>구성

ASP.NET Core는 간단한 이름 값 쌍을 처리하기 위한 새 구성 모델을 사용합니다. 새 구성 모델은 `System.Configuration` 또는 *web.config*를 기반으로 하지 않고, 오히려 구성 공급자의 정렬된 집합에서 가져옵니다. 기본 제공 구성 공급자는 환경 기반 구성을 가능하게 하는 다양한 파일 형식(XML, JSON, INI) 및 환경 변수를 지원합니다. 자체 사용자 지정 구성 공급자를 작성할 수도 있습니다.

자세한 내용은 [구성](xref:fundamentals/configuration)을 참조하세요.

## <a name="environments"></a>환경

“개발” 및 “프로덕션” 같은 환경은 ASP.NET Core의 첫 번째 클래스 개념이고 환경 변수를 사용하여 설정할 수 있습니다.

자세한 내용은 [여러 환경 사용](xref:fundamentals/environments)을 참조하세요.

## <a name="net-core-vs-net-framework-runtime"></a>.NET Core 및 .NET Framework 런타임

ASP.NET Core 응용 프로그램은 .NET Core 또는 .NET Framework 런타임을 대상으로 할 수 있습니다. 자세한 내용은 [.NET Core와 .NET Framework 중에 선택](https://docs.microsoft.com/dotnet/articles/standard/choosing-core-framework-server)을 참조하세요.

## <a name="additional-information"></a>추가 정보

또한 다음 항목을 참조하세요.

- [오류 처리](xref:fundamentals/error-handling)
- [파일 공급자](xref:fundamentals/file-providers)
- [전역화 및 지역화](xref:fundamentals/localization)
- [로깅](xref:fundamentals/logging)
- [응용 프로그램 상태 관리](xref:fundamentals/app-state)
