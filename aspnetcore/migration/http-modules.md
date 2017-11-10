---
title: "HTTP 처리기 및 ASP.NET Core 미들웨어 모듈을 마이그레이션하는 방법"
author: rick-anderson
description: 
keywords: ASP.NET Core,
ms.author: tdykstra
manager: wpickett
ms.date: 12/07/2016
ms.topic: article
ms.assetid: 9c826a76-fbd2-46b5-978d-6ca6df53531a
ms.technology: aspnet
ms.prod: asp.net-core
uid: migration/http-modules
ms.openlocfilehash: eb5049d4d63c224ca74fc39072ae2c0d98ba330d
ms.sourcegitcommit: 8f4d4fad1ca27adf9e396f5c205c9875a3963664
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/13/2017
---
# <a name="migrating-http-handlers-and-modules-to-aspnet-core-middleware"></a>HTTP 처리기 및 ASP.NET Core 미들웨어 모듈을 마이그레이션하는 방법 

으로 [Matt Perdeck](https://www.linkedin.com/in/mattperdeck)

이 문서에서는 기존 ASP.NET 마이그레이션하는 방법을 보여 줍니다. [HTTP 모듈 및 처리기 system.webserver에서](https://docs.microsoft.com/iis/configuration/system.webserver/) ASP.NET Core로 [미들웨어](../fundamentals/middleware.md)합니다.

## <a name="modules-and-handlers-revisited"></a>모듈 및 revisited 처리기

ASP.NET Core 미들웨어를 계속 하기 전에 먼저 요약해 보면 HTTP 모듈 및 처리기의 작동 방식:

![모듈 처리기](http-modules/_static/moduleshandlers.png)

**처리기는:**

   * 구현 하는 클래스 [IHttpHandler](https://docs.microsoft.com/dotnet/api/system.web.ihttphandler)

   * 와 같은 지정 된 파일 이름이 나 확장명을 사용 하 여 요청을 처리 하는 데 사용 *보고서*

   * [구성 된](https://docs.microsoft.com//iis/configuration/system.webserver/handlers/) 에 *Web.config*

**모듈은:**

   * 구현 하는 클래스 [IHttpModule](https://docs.microsoft.com/dotnet/api/system.web.ihttpmodule)

   * 모든 요청에 대해 호출 됩니다.

   * (이후 처리를 중지 요청) 단락 (short-circuit) 수

   * HTTP 응답에 추가 하거나 직접 만들 수

   * [구성 된](https://docs.microsoft.com//iis/configuration/system.webserver/modules/) 에 *Web.config*

**모듈에는 들어오는 요청을 처리 하는 순서는 의해 결정 됩니다.**

   1. [응용 프로그램 수명 주기](https://msdn.microsoft.com/library/ms227673.aspx), ASP.NET에서 발생 하는 계열 이벤트 변수인: [BeginRequest](https://docs.microsoft.com/dotnet/api/system.web.httpapplication.beginrequest), [AuthenticateRequest](https://docs.microsoft.com/dotnet/api/system.web.httpapplication.authenticaterequest)등입니다. 각 모듈 하나 이상의 이벤트에 대 한 처리기를 만들 수 있습니다.

   2. 같은 이벤트에 구성 된 순서에 대 한 *Web.config*합니다.

모듈을 외에도 수명 주기 이벤트에 대 한 처리기를 추가할 수 있습니다 프로그램 *Global.asax.cs* 파일입니다. 이러한 처리기는 구성 된 모듈의 처리기 후 실행합니다.

## <a name="from-handlers-and-modules-to-middleware"></a>처리기와 미들웨어 모듈에서

**미들웨어는 HTTP 모듈 및 처리기 보다 간단 합니다.**

   * 모듈, 처리기, *Global.asax.cs*, *Web.config* (제외 IIS 구성) 및 응용 프로그램 수명 주기 사라지게 됩니다.

   * 미들웨어를 하나씩 수행 된 모듈과 처리기의 역할

   * 코드를 사용 하 여 미들웨어 구성 대신 *Web.config*

   * [파이프라인 분기](../fundamentals/middleware.md#middleware-run-map-use) 뿐만 아니라 요청 헤더, 쿼리 문자열 등에서 뿐만 아니라 URL을 기반으로 하는 특정 미들웨어에 요청을 보낼 수 있습니다.

**미들웨어 모듈 매우 비슷합니다.**

   * 모든 요청에 대해 원칙적으로 호출합니다.

   * 단락 (short-circuit)는 요청으로 할 [다음 미들웨어에서 요청을 전달 하지 않을 경우](#http-modules-shortcircuiting-middleware)

   * HTTP 응답을 만들 수

**미들웨어와 모듈은 다른 순서로 처리 됩니다.**

   * 미들웨어의 순서는는 삽입할 요청 파이프라인, 모듈의 순서는 주로 기반으로 하는 동안 순서에 따라 [응용 프로그램 수명 주기](https://msdn.microsoft.com/library/ms227673.aspx) 이벤트

   * 응답에 대 한 미들웨어의 순서는 모듈의 순서는 요청 및 응답에 대해 동일 하 게 하는 동안 요청에 대 한는 반대

   * 참조 [IApplicationBuilder 미들웨어 파이프라인 만들기](../fundamentals/middleware.md#creating-a-middleware-pipeline-with-iapplicationbuilder)

![미들웨어](http-modules/_static/middleware.png)

어떻게 요청 short-circuited 인증 미들웨어 위의 그림에서 note 합니다.

## <a name="migrating-module-code-to-middleware"></a>미들웨어 모듈 코드 마이그레이션

기존 HTTP 모듈은 다음과 같습니다.

[!code-csharp[Main](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Modules/MyModule.cs?highlight=6,8,24,31)]

에 나와 있는 것 처럼는 [미들웨어](../fundamentals/middleware.md) 페이지, ASP.NET Core 미들웨어는 노출 하는 클래스는 `Invoke` 메서드 만들기는 `HttpContext` 반환 하 고는 `Task`합니다. 새 미들웨어는 다음과 같이 표시 됩니다.

<a name="http-modules-usemiddleware"></a>

[!code-csharp[Main](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddleware.cs?highlight=9,13,20,24,28,30,32)]

위의 미들웨어 서식 파일에서 섹션에서 만들어졌다면 [미들웨어를 작성](../fundamentals/middleware.md#middleware-writing-middleware)합니다.

*MyMiddlewareExtensions* 도우미 클래스를 사용 하면 쉽게에 미들웨어를 구성 하 여 `Startup` 클래스입니다. `UseMyMiddleware` 메서드 요청 파이프라인에 미들웨어 클래스를 추가 합니다. 미들웨어에서 필요한 서비스 미들웨어의 생성자에 지정 된 가져오기.

<a name="http-modules-shortcircuiting-middleware"></a>

모듈에는 예를 들어 사용자는 권한이 없는 경우 요청을 종료 될 수 있습니다.

[!code-csharp[Main](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Modules/MyTerminatingModule.cs?highlight=9,10,11,12,13&name=snippet_Terminate)]

미들웨어는이 호출 하지 않음으로써 처리 `Invoke` 파이프라인의 다음 미들웨어에 있습니다. 이전 middlewares 응답 파이프라인을 통해 다시 때에 호출 될 때문에이 완벽 하 게을 종료 하지 않는 요청을 염두에서에 둬야 합니다.

[!code-csharp[Main](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyTerminatingMiddleware.cs?highlight=7,8&name=snippet_Terminate)]

새 미들웨어를 모듈의 기능을 마이그레이션할 때 때문에 코드가 컴파일되지 않습니다 알 수 있습니다는 `HttpContext` 클래스 ASP.NET Core에서 크게 변경 되었습니다. [나중에](#migrating-to-the-new-httpcontext), 새 ASP.NET Core HttpContext를 마이그레이션하는 방법에 표시 됩니다.

## <a name="migrating-module-insertion-into-the-request-pipeline"></a>마이그레이션 모듈 삽입 요청 파이프라인

HTTP 모듈은 일반적으로 사용 하 여 요청 파이프라인에 추가 됩니다 *Web.config*:

[!code-xml[Main](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Web.config?highlight=6&range=1-3,32-33,36,43,50,101)]

이 변환 [새 미들웨어를 추가](../fundamentals/middleware.md#creating-a-middleware-pipeline-with-iapplicationbuilder) 요청 파이프라인에 프로그램 `Startup` 클래스:

[!code-csharp[Main](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=16)]

새 미들웨어를 삽입 하면 파이프라인에서 정확한 지점 모듈로 처리 하는 이벤트에 따라 달라 집니다 (`BeginRequest`, `EndRequest`등)의 모듈 목록에서 해당 순서 및 *Web.config*합니다.

이전에 ASP.NET Core에 없는 응용 프로그램 수명 주기는 명시 하는 미들웨어에서 응답을 처리 하는 순서 모듈에 의해 사용 되는 순서와에서 다릅니다. 이 정렬 결정 좀 더 어렵습니다를 만들 수 있습니다.

순서 지정 문제가 되 면 독립적으로 주문할 수 있습니다. 여러 미들웨어 구성 요소에 모듈을 분할할 수 있습니다.

## <a name="migrating-handler-code-to-middleware"></a>미들웨어 처리기 코드 마이그레이션

HTTP 처리기는 다음과 같습니다.

[!code-csharp[Main](../migration/http-modules/sample/Asp.Net4/Asp.Net4/HttpHandlers/ReportHandler.cs?highlight=5,7,13,14,15,16)]

ASP.NET Core 프로젝트에는 번역할이 다음과 유사 하 게 되는 미들웨어:

[!code-csharp[Main](../migration/http-modules/sample/Asp.Net.Core/Middleware/ReportHandlerMiddleware.cs?highlight=7,9,13,20,21,22,23,40,42,44)]

이 미들웨어는 모듈에 해당 하는 미들웨어와 매우 비슷합니다. 유일한 차이점은 다음과 같습니다에 대 한 호출이 `_next.Invoke(context)`합니다. 지도록 관점에서 처리기가 요청 파이프라인의 끝에가 있으므로 호출할 다음 미들웨어 없습니다.

## <a name="migrating-handler-insertion-into-the-request-pipeline"></a>마이그레이션 처리기 삽입 요청 파이프라인

HTTP 처리기 구성 의해 이루어진다는 *Web.config* 다음과 같은 및:

[!code-xml[Main](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Web.config?highlight=6&range=1-3,32,46-48,50,101)]

새 처리기 미들웨어에서 요청 파이프라인에 추가 하 여 변환할 수 있습니다 프로그램 `Startup` 클래스, 모듈에서 변환 하는 미들웨어와 비슷합니다. 이 방법의 문제 새 처리기 미들웨어에 대 한 모든 요청을 보내는 것과 것입니다. 그러나만 원하는 미들웨어 연결할 지정된 된 확장 프로그램으로 요청 합니다. HTTP 처리기로 사용 했던 동일한 기능을 가질 것입니다.

한 가지 해결 방법은 지정된 된 확장 프로그램을 사용 하 여 요청에 대 한 파이프라인 분기를 사용 하 여는 `MapWhen` 확장 메서드. 이렇게 하면 동일한 `Configure` 다른 미들웨어를 추가 하는 메서드:

[!code-csharp[Main](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=27-34)]

`MapWhen`이러한 매개 변수를 사용 합니다.

1. 람다는는 `HttpContext` 반환 `true` 여 분기 요청이 전달 해야 하는 경우. 즉, 뿐 아니라 해당 확장명에 있지만 요청 헤더, 쿼리 문자열 매개 변수 등을 따라 요청을 분기할 수 있습니다.

2. 사용 하는 람다는 `IApplicationBuilder` 분기에 대 한 모든 미들웨어를 추가 합니다. 즉, 처리기 미들웨어 앞에 추가 미들웨어 분기에 추가할 수 있습니다.

분기는 모든 요청;에서 호출 됩니다. 전에 파이프라인에 미들웨어 추가 분기에 영향을 주어 갖습니다.

## <a name="loading-middleware-options-using-the-options-pattern"></a>로드 옵션 패턴을 사용 하 여 미들웨어 옵션입니다.

일부 모듈과 처리기에 저장 되어 있는 구성 옵션을 사용할 *Web.config*합니다. 그러나 ASP.NET Core에는 새 구성 모델이 사용 됩니다 대신 *Web.config*합니다.

새 [구성 시스템](../fundamentals/configuration.md) 이 문제를 해결 하려면 다음이 옵션을 제공 합니다.

* 에 표시 된 대로 미들웨어에 대 한 옵션을 직접 삽입는 [절로](#loading-middleware-options-through-direct-injection)합니다.

* 사용 하 여 [옵션 패턴](../fundamentals/configuration.md#options-config-objects):

1.  예를 들어 미들웨어 옵션을 저장 하는 클래스를 만듭니다.

    [!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_Options)]

2.  옵션 값을 저장 합니다.

    구성 시스템을 사용 하면 옵션 아무 곳 이나 원하는 값을 저장할 수 있습니다. 그러나 사용 하 여 가장 사이트 *appsettings.json*이므로 해당 접근 이동 합니다.

    [!code-json[Main](http-modules/sample/Asp.Net.Core/appsettings.json?range=1,14-18)]

    *MyMiddlewareOptionsSection* 섹션 이름에는 다음과 같습니다. 옵션 클래스의 이름이 같이 필요는 없습니다.

3. 옵션 클래스 옵션 값에 연결

    옵션 패턴 ASP.NET Core 종속성 주입 프레임 워크를 사용 하 여 연결 옵션의 유형을 (같은 `MyMiddlewareOptions`)와 `MyMiddlewareOptions` 실제 옵션이 있는 개체입니다.

    업데이트 프로그램 `Startup` 클래스:

    1.  사용 중인 경우 *appsettings.json*의 구성 작성기에 추가 `Startup` 생성자:

      [!code-csharp[Main](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Ctor&highlight=5-6)]

    2.  옵션 서비스를 구성 합니다.

      [!code-csharp[Main](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

    3.  옵션 클래스 옵션을 연결 합니다.

      [!code-csharp[Main](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_ConfigureServices&highlight=6-8)]

4.  미들웨어 생성자에 대 한 옵션을 삽입 합니다. 컨트롤러에 대 한 옵션을 삽입 하는 것과 비슷합니다.

  [!code-csharp[Main](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_MiddlewareWithParams&highlight=4,7,10,15-16)]

  [UseMiddleware](#http-modules-usemiddleware) 미들웨어를 추가 하는 확장 메서드는 `IApplicationBuilder` 종속성 주입 담당 합니다.

  에 국한 되지 않음 `IOptions` 개체입니다. 미들웨어 해야 하는 다른 개체에는 이러한 방식으로 삽입 될 수 있습니다.

## <a name="loading-middleware-options-through-direct-injection"></a>로드를 통해 직접 삽입 미들웨어 옵션입니다.

옵션 패턴에 낮은 옵션 값와 소비자 사이 결합은 생성 되는 이점이 있습니다. 실제 옵션 값에는 옵션 클래스 연결한, 되 면 다른 클래스 종속성 주입 프레임 워크를 통해 옵션에 대 한 액세스를 얻을 수 있습니다. 옵션 값 전달 않아도가 됩니다.

그러면 분할 하지만 여러 가지 옵션을 같은 미들웨어를 두 번 사용 하려는 경우. 예를 들어는 권한 부여 미들웨어 다양 한 역할을 허용 하는 서로 다른 분기에서 사용 되는입니다. 하나의 옵션 클래스와 두 개의 서로 다른 옵션 개체를 연결할 수 없습니다.

실제 옵션 값과 옵션 개체를 가져오는 데 솔루션은 프로그램 `Startup` 클래스 미들웨어의 각 인스턴스에 확인란을 직접 전달 합니다.

1.  두 번째 키를 추가 *appsettings.json*

    옵션 중 두 번째 집합을 추가 하려면는 *appsettings.json* 파일을 고유 하 게 식별 하는 데 새 키를 사용 합니다.

    [!code-json[Main](http-modules/sample/Asp.Net.Core/appsettings.json?range=1,10-18&highlight=2-5)]

2.  옵션 값을 검색 하 고 미들웨어에 전달 합니다. `Use...` (파이프라인에 미들웨어를 추가)는 확장 메서드는 옵션 값을 전달할 수 있는 논리적 위치: 

    [!code-csharp[Main](http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=20-23)]

4.  미들웨어 옵션 매개 변수를 사용 하도록 설정 합니다. 오버 로드를 제공 된 `Use...` 확장 메서드 (옵션 매개 변수를 사용 하 고로 전달 `UseMiddleware`). 때 `UseMiddleware` 라고 매개 변수와 함께 전달 매개 변수 미들웨어 생성자에 미들웨어 개체를 인스턴스화할 때.

    [!code-csharp[Main](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_Extensions&highlight=9-14)]

    이 옵션 개체에 배치 되는 방법을 확인 프로그램 `OptionsWrapper` 개체입니다. 이 구현 `IOptions`미들웨어 생성자가 예상 대로, 합니다.

## <a name="migrating-to-the-new-httpcontext"></a>새 HttpContext로 마이그레이션

앞에서 본 하는 `Invoke` 형식의 매개 변수를 사용 하는 미들웨어에서 메서드 `HttpContext`:

```csharp
public async Task Invoke(HttpContext context)
```

`HttpContext`ASP.NET Core 크게 변경 되었습니다. 이 섹션에서는의 가장 일반적으로 사용 되는 속성으로 변환 하는 방법을 보여 줍니다. [System.Web.HttpContext](https://docs.microsoft.com/dotnet/api/system.web.httpcontext) 새 `Microsoft.AspNetCore.Http.HttpContext`합니다.

### <a name="httpcontext"></a>HttpContext

**HttpContext.Items** 로 변환 합니다.

[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Items)]

**고유한 요청 ID (System.Web.HttpContext 해당)**

각 요청에 대 한 고유 id을 제공 합니다. 로그에 포함 하는 데 매우 유용 합니다.

[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Trace)]

### <a name="httpcontextrequest"></a>HttpContext.Request

**HttpContext.Request.HttpMethod** 로 변환 합니다.

[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Method)]

**HttpContext.Request.QueryString** 로 변환 합니다.

[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Query)]

**HttpContext.Request.Url** 및 **HttpContext.Request.RawUrl** 로 변환 합니다.

[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Url)]

**HttpContext.Request.IsSecureConnection** 로 변환 합니다.

[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Secure)]

**HttpContext.Request.UserHostAddress** 로 변환 합니다.

[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Host)]

**HttpContext.Request.Cookies** 로 변환 합니다.

[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Cookies)]

**HttpContext.Request.RequestContext.RouteData** 로 변환 합니다.

[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Route)]

**HttpContext.Request.Headers** 로 변환 합니다.

[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Headers)]

**HttpContext.Request.UserAgent** 로 변환 합니다.

[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Agent)]

**HttpContext.Request.UrlReferrer** 로 변환 합니다.

[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Referrer)]

**HttpContext.Request.ContentType** 로 변환 합니다.

[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Type)]

**HttpContext.Request.Form** 로 변환 합니다.

[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Form)]

> [!WARNING]
> 콘텐츠 하위 형식이 경우 양식 값을 읽기 *x-www-형식-urlencoded* 또는 *양식 데이터*합니다.

**HttpContext.Request.InputStream** 로 변환 합니다.

[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Input)]

> [!WARNING]
> 이 코드는 파이프라인의 끝에 처리기 형식 미들웨어, 에서만 사용 합니다.
>
>요청당 한 번만 위와 같이 원시 본문을 읽을 수 있습니다. 미들웨어를 첫 번째 읽기 후 본문을 읽는 동안에 빈 본문이 읽습니다.
>
>이 버퍼에서 수행 되기 때문에 앞에서 표시 된 것 처럼 폼 읽기에 적용 되지 않습니다.

### <a name="httpcontextresponse"></a>HttpContext.Response

**HttpContext.Response.Status** 및 **HttpContext.Response.StatusDescription** 로 변환 합니다.

[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Status)]

**HttpContext.Response.ContentEncoding** 및 **HttpContext.Response.ContentType** 로 변환 합니다.

[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_RespType)]

**HttpContext.Response.ContentType** 에 자체도로 변환 합니다.

[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_RespTypeOnly)]

**HttpContext.Response.Output** 로 변환 합니다.

[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Output)]

**HttpContext.Response.TransmitFile**

파일을 서비스 설명 [여기](../fundamentals/request-features.md#middleware-and-request-features)합니다.

**HttpContext.Response.Headers**

때문에 복잡는 보내는 응답 헤더를 설정할 경우 응답 본문에 아무 것도 기록 된 후은 전송 되지 것입니다.

솔루션 응답 시작에 쓰기 전에 오른쪽 호출 될 콜백 메서드를 설정 하는 것입니다. 시작 부분에 가장 잘 이렇게는 `Invoke` 미들웨어에서 메서드. 응답 헤더를 설정 하는이 콜백 메서드는

다음 코드에서는 호출 하는 콜백 메서드 설정 `SetHeaders`:

```csharp
public async Task Invoke(HttpContext httpContext)
{
    // ...
    httpContext.Response.OnStarting(SetHeaders, state: httpContext);
```

`SetHeaders` 콜백 메서드는 다음과 같습니다.

[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_SetHeaders)]

**HttpContext.Response.Cookies**

브라우저에서 쿠키 여행는 *Set-cookie* 응답 헤더입니다. 결과적으로, 쿠키 보내기를 응답 헤더를 보내는 데 사용 된 것과 동일한 콜백 함수가 필요 합니다.

```csharp
public async Task Invoke(HttpContext httpContext)
{
    // ...
    httpContext.Response.OnStarting(SetCookies, state: httpContext);
    httpContext.Response.OnStarting(SetHeaders, state: httpContext);
```

`SetCookies` 콜백 메서드는 다음과 같습니다.

[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_SetCookies)]

## <a name="additional-resources"></a>추가 리소스

* [HTTP 처리기 및 HTTP 모듈 개요](https://docs.microsoft.com/iis/configuration/system.webserver/)

* [구성](../fundamentals/configuration.md)

* [응용 프로그램 시작](../fundamentals/startup.md)

* [미들웨어](../fundamentals/middleware.md)
