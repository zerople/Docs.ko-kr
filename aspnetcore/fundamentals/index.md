---
title: "ASP.NET Core 기본 사항 | Microsoft 문서"
author: rick-anderson
description: "ASP.NET Core의 기본적인 concpepts 간략하게 설명 합니다."
keywords: "ASP.NET 핵심 원칙, 개요"
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: a19b7836-63e4-44e8-8250-50d426dd1070
ms.technology: aspnet
ms.prod: asp.net-core
ms.custom: H1Hack27Feb2017
translationtype: Machine Translation
ms.sourcegitcommit: 010b730d2716f9f536fef889bc2f767afb648ef4
ms.openlocfilehash: 29bdfeba06faf6d66a4dbd702b603deeb6cec4bf
ms.lasthandoff: 03/23/2017

---

# <a name="aspnet-core-fundamentals-overview"></a>ASP.NET Core 기본 사항 개요

ASP.NET 핵심 응용 프로그램은 웹 서버에서 만드는 콘솔 응용 프로그램 단순히 해당 `Main` 메서드:

[!code-csharp[주](../getting-started/sample/aspnetcoreapp/Program.cs)]

`Main`사용 하 여 `WebHostBuilder`, 웹 응용 프로그램 호스트를 만들려면 작성기 패턴을 따릅니다입니다. 작성기가 웹 서버를 정의 하는 메서드 (예를 들어 `UseKestrel`) 및 시작 클래스 (`UseStartup`). 위의 예제는 [Kestrel](servers/kestrel.md) 웹 서버는 사용 되지 않지만 다른 웹 서버를 지정할 수 있습니다. 에 대 한 자세한 알아봅니다 `UseStartup` 다음 섹션에 있습니다. `WebHostBuilder`포함 하 여 선택적 메서드를 제공 `UseIISIntegration` IIS 및 IIS Express에서 호스팅에 대 한 고 `UseContentRoot` 루트 콘텐츠 디렉터리를 지정 하는 데 있습니다. `Build` 및 `Run` 빌드 방법을 `IWebHost` 들어오는 HTTP 요청에 대 한 수신 대기를 시작 응용 프로그램을 호스트 하는 개체입니다.

## <a name="startup"></a>시작

`UseStartup` 메서드를 `WebHostBuilder` 지정는 `Startup` 응용 프로그램에 대 한 클래스입니다.

[!code-csharp[주](../getting-started/sample/aspnetcoreapp/Program.cs?highlight=7&range=6-17)]

`Startup` 클래스는 요청 처리 파이프라인을 정의 하 고 여기에 앱에 필요한 모든 서비스 구성 됩니다. `Startup` 클래스에서 public 이어야 하 고 다음 메서드를 포함 해야 합니다.

```csharp
public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
    }

    public void Configure(IApplicationBuilder app)
    {
    }
}
```

* `ConfigureServices`서비스 정의 (참조 [서비스](#services) 아래) 응용 프로그램에서 사용 하는 (ASP.NET MVC 코어 프레임 워크, Entity Framework 코어, Id 등입니다.)

* `Configure`정의 [미들웨어](middleware.md) 요청 파이프라인에서

자세한 내용은 참조 [응용 프로그램 시작](startup.md)합니다.

## <a name="services"></a>서비스

서비스는 응용 프로그램에서 일반적인 소비 하는 데 사용 되는 구성 요소. 서비스는 DI (종속성 주입)를 통해 사용할 수 있습니다. ASP.NET Core는 간단한 기본 제공 제어 반전 (IoC) 컨테이너 생성자 주입 기본적으로 지 포함 됩니다. 선택한 컨테이너와 기본 제공 컨테이너를 쉽게 대체할 수 있습니다. 느슨한 결합 혜택 외에도 DI 서비스를 응용 프로그램 전체에서 사용할 수 있습니다. 예를 들어 [로깅](logging.md) 응용 프로그램 전체에서 사용할 수 있습니다.

자세한 내용은 참조 [종속성 주입](dependency-injection.md) 합니다.

## <a name="middleware"></a>미들웨어

ASP.NET Core에서 사용 하 여 요청 파이프라인 작성할 [미들웨어](middleware.md)합니다. 비동기 논리를 수행 하는 ASP.NET 핵심 미들웨어는 `HttpContext` 고 시퀀스의 다음 미들웨어를 호출 하거나 직접 요청을 종료 합니다. "사용" 미들웨어 NuGet 패키지에 의존 하 고 해당 호출 하 여 `UseXYZ` 대 한 확장 메서드는 `IApplicationBuilder` 에 `Configure` 메서드.

ASP.NET Core 다양 한 기본 제공 미들웨어 함께 제공 됩니다.

* [정적 파일](static-files.md)

* [라우팅](routing.md)

* [인증](../security/authentication/index.md)

모든 사용할 수 있습니다 [OWIN](http://owin.org)-ASP.NET 코어 미들웨어를 기반으로 하 고 사용자 고유의 사용자 지정 미들웨어를 작성할 수 있습니다.

자세한 내용은 참조 [미들웨어](middleware.md) 및 [공개 웹 인터페이스.NET (OWIN)](owin.md)합니다.

## <a name="servers"></a>서버

호스팅 모델 ASP.NET 핵심 직접 요청;에 대 한 수신 대기 하지 않습니다. 대신 응용 프로그램에 요청을 전달 하는 HTTP 서버 구현을 사용 합니다. 그런 다음 응용 프로그램으로 구성 하는 기능 인터페이스 집합으로 전달된 된 요청 래핑된는 `HttpContext`합니다.  라고 하는 관리 되는 플랫폼 간 웹 서버를 포함 하는 ASP.NET 핵심 [Kestrel](servers/kestrel.md) 뒤에 프로덕션 웹 서버와 같은 일반적으로 실행 하 [IIS](https://iis.net) 또는 [nginx](http://nginx.org)합니다.

자세한 내용은 참조 [서버](servers/index.md) 및 [호스팅](hosting.md)합니다.

## <a name="content-root"></a>콘텐츠 루트

콘텐츠 루트 뷰 및 웹 콘텐츠 같은 응용 프로그램에서 사용 하는 모든 내용에 기본 경로입니다. 기본적으로 콘텐츠 루트와 같습니다 앱; 호스팅 실행 파일에 대 한 응용 프로그램 기본 경로 대체 위치를 지정할 수 있습니다 *WebHostBuilder*합니다.

## <a name="web-root"></a>웹 루트

응용 프로그램의 웹 루트에는 css, js 및 이미지 파일과 같은 public, 정적 리소스에 대 한 프로젝트의 디렉터리입니다. 정적 파일 미들웨어는 기본적으로 웹 루트 디렉터리 (및 하위 디렉터리)에서 파일을 제공만 합니다. 웹 루트 경로 기본값으로 *<content root>/wwwroot*, 이지만 사용 하 여 다른 위치를 지정할 수는 *WebHostBuilder*합니다.

## <a name="configuration"></a>구성

ASP.NET Core 간단한 이름-값 쌍을 처리 하기 위한 새 구성 모델을 사용 합니다. 새 구성 모델 기반 하지 않는 `System.Configuration` 또는 *web.config*아니라 것에 구성 공급자의 정렬된 된 집합에서 가져옵니다. 기본 제공 구성 공급자는 다양 한 파일 형식 (XML, JSON, INI) 및 환경 기반 구성을 사용 하도록 환경 변수를 지원 합니다. 또한 사용자 고유의 사용자 지정 구성 공급자를 작성할 수 있습니다.

자세한 내용은 참조 [구성](configuration.md)합니다.

## <a name="environments"></a>환경

"개발" 및 "프로덕션"와 같은 환경에서는 ASP.NET 핵심에는 첫 번째 클래스 개념 이며 환경 변수를 사용 하 여 설정할 수 있습니다.

자세한 내용은 참조 [여러 환경 작업](environments.md)합니다.

## <a name="net-core-vs-net-framework-runtime"></a>.NET Framework 및.NET core 런타임

ASP.NET 핵심 응용 프로그램에.NET Core 또는.NET Framework 런타임을 사용할 수 있습니다. 자세한 내용은 참조 [.NET Core와.NET Framework 간의 선택](https://docs.microsoft.com/dotnet/articles/standard/choosing-core-framework-server)합니다.

## <a name="additional-information"></a>추가 정보

다음 항목을 참조 하십시오.

- [로깅](logging.md)
- [오류 처리](error-handling.md)
- [전역화 및 지역화](localization.md)
- [파일 공급자](file-providers.md)
- [응용 프로그램 상태 관리](app-state.md)

