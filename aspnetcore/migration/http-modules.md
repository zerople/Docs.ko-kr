---
title: "HTTP 처리기 및 모듈이 ASP.NET 핵심 미들웨어를 마이그레이션 | Microsoft 문서"
author: rick-anderson
description: 
keywords: ASP.NET Core
ms.author: tdykstra
manager: wpickett
ms.date: 12/07/2016
ms.topic: article
ms.assetid: 9c826a76-fbd2-46b5-978d-6ca6df53531a
ms.technology: aspnet
ms.prod: asp.net-core
uid: migration/http-modules
translationtype: Machine Translation
ms.sourcegitcommit: 010b730d2716f9f536fef889bc2f767afb648ef4
ms.openlocfilehash: 7e11f84c04802ea103dc2b0bc3f9a17cdccb0359
ms.lasthandoff: 03/23/2017

---
# <a name="migrating-http-handlers-and-modules-to-aspnet-core-middleware"></a>마이그레이션 HTTP 처리기 및 ASP.NET 핵심 미들웨어 모듈 

[Matt Perdeck](https://www.linkedin.com/in/mattperdeck)

이 문서에서는 기존 ASP.NET 마이그레이션하는 방법을 보여 줍니다. [HTTP 모듈 및 처리기](https://msdn.microsoft.com/library/bb398986.aspx) ASP.NET core [미들웨어](../fundamentals/middleware.md)합니다.

## <a name="modules-and-handlers-revisited"></a>모듈 및 처리기 다시 보기

ASP.NET Core 미들웨어를 계속 하기 전에 먼저 정리해 보겠습니다 HTTP 모듈 및 처리기의 작동 방식:

![모듈 처리기](http-modules/_static/moduleshandlers.png)

**처리기는:**

   * 구현 하는 클래스 [IHttpHandler](https://msdn.microsoft.com/library/system.web.ihttphandler.aspx)

   * 지정 된 파일 이름이 나 확장명을 사용 하 여 요청을 처리 하는 데 *보고서*

   * [구성 된](https://msdn.microsoft.com/library/46c5ddfy.aspx) 에 *Web.config*

**모듈은:**

   * 구현 하는 클래스 [IHttpModule](https://msdn.microsoft.com/library/system.web.ihttpmodule.aspx)

   * 모든 요청에 대해 호출합니다.

   * (요청 이후 처리 중지) 단락 (short-circuit) 수

   * HTTP 응답에 추가 하거나 직접 만들 수

   * [구성 된](https://msdn.microsoft.com/library/ms227673.aspx) 에 *Web.config*

**모듈에는 들어오는 요청을 처리 하는 순서는 의해 결정 됩니다.**

   1. [응용 프로그램 수명 주기](https://msdn.microsoft.com/library/ms227673.aspx), ASP.NET에 의해 발생 하는 시리즈 이벤트는: [BeginRequest](https://msdn.microsoft.com/library/system.web.httpapplication.beginrequest.aspx), [AuthenticateRequest](https://msdn.microsoft.com/library/system.web.httpapplication.authenticaterequest.aspx)등입니다. 각 모듈 하나 이상의 이벤트에 대 한 처리기를 만들 수 있습니다.

   2. 같은 이벤트에 구성 된 순서에 대 한 *Web.config*합니다.

모듈 외에도 수명 주기 이벤트에 대 한 처리기를 추가할 수 있습니다 프로그램 *Global.asax.cs* 파일입니다. 이러한 처리기 구성된 모듈에서 처리기 후 실행합니다.

## <a name="from-handlers-and-modules-to-middleware"></a>처리기 및 미들웨어 모듈에서

**미들웨어는 HTTP 모듈 및 처리기 보다 간단 합니다.**

   * 모듈, 처리기, *Global.asax.cs*, *Web.config* (제외 IIS 구성) 및 응용 프로그램 수명 주기 사라짐

   * 미들웨어를 통해 수행 된 모듈 및 처리기를 둘 다의 역할

   * 코드를 사용 하 여 미들웨어 구성 대신 *Web.config*

   * [파이프라인 분기](../fundamentals/middleware.md#middleware-run-map-use) 뿐 아니라 요청 헤더, 쿼리 문자열 등의 경우에 URL 기반으로 하는 특정 미들웨어에 요청을 보낼 수 있습니다.

**미들웨어 모듈 매우 비슷합니다.**

   * 모든 요청에 대해 원칙적으로 호출합니다.

   * 요청에서 단락 (short-circuit) 할 [다음 미들웨어에 요청을 전달 하지 않을 경우](#http-modules-shortcircuiting-middleware)

   * 고유한 HTTP 응답을 만들 수

**미들웨어 및 모듈을 다른 순서로 처리 됩니다.**

   * 미들웨어의 순서는 삽입 요청 파이프라인으로 모듈의 순서는 주로 기반 하는 동안 순서에 따라은 [응용 프로그램 수명 주기](https://msdn.microsoft.com/library/ms227673.aspx) 이벤트

   * 모듈의 순서는 요청 및 응답에 대해 동일 하지만 응답에 대 한 미들웨어의 순서는 요청에 대 한 구현과 반대

   * 참조 [IApplicationBuilder 미들웨어 파이프라인 만들기](../fundamentals/middleware.md#creating-a-middleware-pipeline-with-iapplicationbuilder)

![미들웨어](http-modules/_static/middleware.png)

Note 어떻게 요청 short-circuited 인증 미들웨어 위 이미지에 있습니다.

## <a name="migrating-module-code-to-middleware"></a>미들웨어를 모듈 코드 마이그레이션

기존 HTTP 모듈은 다음과 유사 하 게 표시 됩니다.

[!code-csharp[주](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Modules/MyModule.cs?highlight=6,8,24,31)]

에 표시 된 대로 [미들웨어](../fundamentals/middleware.md) 페이지, ASP.NET 핵심 미들웨어는 노출 하는 클래스는 `Invoke` 메서드 만들기는 `HttpContext` 반환 하는 `Task`합니다. 새 미들웨어는 다음과 같이 표시 됩니다.

<a name=http-modules-usemiddleware></a>

[!code-csharp[주](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddleware.cs?highlight=9,13,20,24,28,30,32)]

위의 미들웨어 서식 파일에 섹션에서 사용 된 [미들웨어 작성](../fundamentals/middleware.md#middleware-writing-middleware)합니다.

*MyMiddlewareExtensions* 도우미 클래스를 사용 하면 쉽게에서 미들웨어를 구성 하 여 `Startup` 클래스입니다. `UseMyMiddleware` 메서드 요청 파이프라인에 미들웨어 클래스를 추가 합니다. 미들웨어에 필요한 서비스 미들웨어의 생성자에 지정 된 가져오기.

<a name=http-modules-shortcircuiting-middleware></a>

모듈에는 예를 들어 사용자 권한이 없으면 요청을 종료 될 수 있습니다.

[!code-csharp[주](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Modules/MyTerminatingModule.cs?highlight=9,10,11,12,13&name=snippet_Terminate)]

미들웨어는이 호출 하지 않음으로써 처리 `Invoke` 파이프라인의 다음 미들웨어에 있습니다. 이전 미들웨어 응답 파이프라인을 통해 다시 때에 호출 될 있으므로이 종료 되지 않는 완벽 하 게 요청을 염두에 유지 됩니다.

[!code-csharp[주](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyTerminatingMiddleware.cs?highlight=7,8&name=snippet_Terminate)]

새 미들웨어를 모듈의 기능을 마이그레이션할 때 사용할 수 있습니다 때문에 코드가 컴파일되지 않습니다는 `HttpContext` 클래스 ASP.NET 코어에서 크게 변경 되었습니다. [나중에](#migrating-to-the-new-httpcontext), 새로운 ASP.NET 핵심 HttpContext에 마이그레이션하는 방법을 확인할 수 있습니다.

## <a name="migrating-module-insertion-into-the-request-pipeline"></a>요청 파이프라인에 마이그레이션 모듈 삽입

HTTP 모듈은 일반적으로 사용 하 여 요청 파이프라인에 추가 됩니다 *Web.config*:

[!code-xml[주](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Web.config?highlight=6&range=1-3,32-33,36,43,50,101)]

변환 하 여이 [새 미들웨어 추가](../fundamentals/middleware.md#creating-a-middleware-pipeline-with-iapplicationbuilder) 요청 파이프라인에 프로그램 `Startup` 클래스:

[!code-csharp[주](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=16)]

새 미들웨어 삽입할 파이프라인에서 정확한 지점 모듈 취급 하는 이벤트에 따라 달라 집니다 (`BeginRequest`, `EndRequest`등)와의 모듈 목록에서 해당 순서 *Web.config*합니다.

앞에서 설명한 대로 ASP.NET 코어에 없는 응용 프로그램 수명 주기는 명시 하는 응답을 처리 하 여 미들웨어 순서 모듈에 의해 사용 되는 순서와 다릅니다. 이 정렬 결정 하는 데 더 재미를 적용할 수 있습니다.

순서 지정이 문제가 되 면 독립적으로 주문할 수 있습니다. 여러 미들웨어 구성 요소에 모듈을 분할할 수 있습니다.

## <a name="migrating-handler-code-to-middleware"></a>미들웨어를 처리기 코드 마이그레이션

HTTP 처리기는 다음과 같습니다.

[!code-csharp[주](../migration/http-modules/sample/Asp.Net4/Asp.Net4/HttpHandlers/ReportHandler.cs?highlight=5,7,13,14,15,16)]

ASP.NET Core 프로젝트에서 사용자는 변환이 다음과 유사 하 게 되는 미들웨어를 합니다.

[!code-csharp[주](../migration/http-modules/sample/Asp.Net.Core/Middleware/ReportHandlerMiddleware.cs?highlight=7,9,13,20,21,22,23,40,42,44)]

이 미들웨어 모듈에 해당 하는 미들웨어 매우 비슷합니다. 유일한 차이점은 다음과 같습니다에 대 한 호출이 `_next.Invoke(context)`합니다. 합리적이 지 않은 처리기가 요청 파이프라인의 끝에 될 수 있도록 없는 다음 미들웨어를 호출 합니다.

## <a name="migrating-handler-insertion-into-the-request-pipeline"></a>요청 파이프라인에 마이그레이션 처리기 삽입

수행 되는 HTTP 처리기 구성 *Web.config* 및 모습은 다음과 같습니다.

[!code-xml[주](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Web.config?highlight=6&range=1-3,32,46-48,50,101)]

새 처리기 미들웨어 요청 파이프라인에 추가 하 여 변환할 수 있습니다 프로그램 `Startup` 클래스, 모듈에서 변환 하는 미들웨어 비슷합니다. 이 방법의 문제는이 모든 요청을 보낼 새 처리기 미들웨어입니다. 그러나 하려는 미들웨어 도달 하기 위해 지정된 된 확장 프로그램으로 요청 합니다. HTTP 처리기가 동일한 기능을 가질 것입니다.

지정된 된 확장 프로그램을 사용 하 여 요청에 대 한 파이프라인 병렬 실행 하는 한 가지 방법은 사용 하는 `MapWhen` 확장 메서드. 이렇게 하면 동일한 `Configure` 다른 미들웨어를 추가 하는 방법:

[!code-csharp[주](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=27-34)]

`MapWhen`이러한 매개 변수를 사용합니다.

1. 사용 하는 람다는 `HttpContext` 반환 `true` 여 분기는 요청을 보내야 하는 경우. 즉, 요청 뿐만 아니라 해당 확장에 있지만 요청 헤더, 쿼리 문자열 매개 변수 등을 기반으로 분기할 수 있습니다.

2. 사용 하는 람다는 `IApplicationBuilder` 분기에 대 한 모든 미들웨어를 추가 합니다. 즉, 처리기 미들웨어 앞에 추가 미들웨어 분기에 추가할 수 있습니다.

미들웨어 분기 모든 요청에서 호출 될 전에 파이프라인에 추가 분기에 아무런 영향을 주지를 갖습니다.

## <a name="loading-middleware-options-using-the-options-pattern"></a>로드 옵션 패턴을 사용 하는 미들웨어 옵션

일부 모듈 및 처리기에 저장 되어 있는 구성 옵션을 사용할 *Web.config*합니다. 그러나 ASP.NET 핵심에는 새로운 구성 모델이 사용 됩니다 대신 *Web.config*합니다.

새 [구성 시스템](../fundamentals/configuration.md) 이 문제를 해결 하려면 다음이 옵션을 제공 합니다.

* 에 표시 된 대로 미들웨어에 대 한 옵션을 직접 삽입는 [절로](#loading-middleware-options-through-direct-injection)합니다.

* 사용 하 여 [옵션 패턴](../fundamentals/configuration.md#options-config-objects):

1.  예를 들어 미들웨어 옵션에 포함 된 클래스를 만듭니다.

    [!code-csharp[주](http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_Options)]

2.  옵션 값을 저장 합니다.

    구성 시스템을 사용 하면 옵션 어디서 나 원하는 값을 저장할 수 있습니다. 그러나 사용을 가장 사이트 *appsettings.json*이므로 방법을 살펴보겠습니다.

    [!code-json[주](http-modules/sample/Asp.Net.Core/appsettings.json?range=1,14-18)]

    *MyMiddlewareOptionsSection* 섹션 이름에는 다음과 같습니다. 옵션 클래스의 이름과 같이 필요는 없습니다.

3. 옵션 값 옵션 클래스와 연결

    옵션 패턴 ASP.NET 핵심 종속성 주입 프레임 워크를 사용 하 여 옵션의 유형을 연결 하 (예: `MyMiddlewareOptions`)와 `MyMiddlewareOptions` 실제 옵션이 있는 개체입니다.

    업데이트 프로그램 `Startup` 클래스:

    1.  사용 하는 경우 *appsettings.json*에서 구성 작성기에 추가 된 `Startup` 생성자:

      [!code-csharp[주](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Ctor&highlight=5-6)]

    2.  옵션 서비스를 구성 합니다.

      [!code-csharp[주](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

    3.  옵션 클래스 옵션을 연결 합니다.

      [!code-csharp[주](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_ConfigureServices&highlight=6-8)]

4.  미들웨어 생성자에 대 한 옵션을 삽입 합니다. 컨트롤러에 대 한 옵션을 삽입 하는 것과 비슷합니다.

  [!code-csharp[주](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_MiddlewareWithParams&highlight=4,7,10,15-16)]

  [UseMiddleware](#http-modules-usemiddleware) 미들웨어를 추가 하는 확장 메서드는 `IApplicationBuilder` 종속성 주입을 맡고 있습니다.

  이에 제한 되지 않습니다 `IOptions` 개체입니다. 미들웨어 해야 하는 다른 개체에는 이러한 방식으로 삽입할 수 있습니다.

## <a name="loading-middleware-options-through-direct-injection"></a>로드 직접 주입을 통해 미들웨어 옵션

옵션 패턴에 옵션 값와 소비자 간에 느슨한 연결 생성 되는 이점이 있습니다. 옵션 클래스의 실제 옵션 값으로, 연결 되 면 다른 모든 클래스 종속성 주입 프레임 워크를 통해 옵션에 대 한 액세스를 가져올 수 있습니다. 옵션 값 전달 않아도가 됩니다.

그러면 분할 하지만 다양 한 옵션으로 같은 미들웨어를 두 번 사용 하려는 경우. 예를 들어 한 권한 부여 미들웨어 다양 한 역할을 허용 하는 서로 다른 분기에서 사용 합니다. 하나의 옵션 클래스와 두 개의 서로 다른 옵션 개체를 연결할 수 없습니다.

솔루션의 실제 옵션 값이 있는 옵션 개체를 가져오는 것 프로그램 `Startup` 클래스 및이 미들웨어의 각 인스턴스를 직접 전달 합니다.

1.  두 번째 키를 추가 *appsettings.json*

    두 번째 옵션의 집합을 추가 하는 *appsettings.json* 파일을 고유 하 게 식별 하는 데 새 키를 사용 합니다.

    [!code-json[주](http-modules/sample/Asp.Net.Core/appsettings.json?range=1,10-18&highlight=2-5)]

2.  옵션 값을 검색 하 고 미들웨어를 전달 합니다. `Use...` 확장 메서드 (미들웨어 파이프라인에 추가)에 옵션 값에 전달할 수 있는 논리 위치입니다. 

    [!code-csharp[주](http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=20-23)]

4.  미들웨어 옵션 매개 변수를 사용 하도록 설정 합니다. 오버 로드를 제공 된 `Use...` 확장 메서드 (옵션 매개 변수를 사용 하 고 전달 `UseMiddleware`). 때 `UseMiddleware` 라고 매개 변수와 함께 매개 변수를 전달 미들웨어 생성자 미들웨어 개체를 인스턴스화할 때.

    [!code-csharp[주](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_Extensions&highlight=9-14)]

    이 옵션 개체에 배치 되는 방법을 확인는 `OptionsWrapper` 개체입니다. 이 구현 `IOptions`미들웨어 생성자가 예상 대로입니다.

## <a name="migrating-to-the-new-httpcontext"></a>새 HttpContext로 마이그레이션

앞서 살펴본 하는 `Invoke` 형식의 매개 변수를 사용 하는 미들웨어 메서드에서 `HttpContext`:

```csharp
public async Task Invoke(HttpContext context)
```

`HttpContext`ASP.NET Core 크게 변경 되었습니다. 이 섹션에서는 가장 일반적으로 사용 되는 속성을 변환 하는 방법을 보여 줍니다. [System.Web.HttpContext](https://msdn.microsoft.com/library/system.web.httpcontext.aspx) 새 `Microsoft.AspNetCore.Http.HttpContext`합니다.

### <a name="httpcontext"></a>HttpContext

**HttpContext.Items** 로 변환 합니다.

[!code-csharp[주](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Items)]

**고유한 요청 ID (System.Web.HttpContext 해당 하는 항목이)**

고유 id를 제공 각 요청에 대 한 합니다. 에 로그에 포함 하는 데 매우 유용 합니다.

[!code-csharp[주](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Trace)]

### <a name="httpcontextrequest"></a>HttpContext.Request

**HttpContext.Request.HttpMethod** 로 변환 합니다.

[!code-csharp[주](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Method)]

**HttpContext.Request.QueryString** 로 변환 합니다.

[!code-csharp[주](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Query)]

**HttpContext.Request.Url** 및 **HttpContext.Request.RawUrl** 로 변환 합니다.

[!code-csharp[주](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Url)]

**HttpContext.Request.IsSecureConnection** 로 변환 합니다.

[!code-csharp[주](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Secure)]

**HttpContext.Request.UserHostAddress** 로 변환 합니다.

[!code-csharp[주](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Host)]

**HttpContext.Request.Cookies** 로 변환 합니다.

[!code-csharp[주](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Cookies)]

**HttpContext.Request.RequestContext.RouteData** 로 변환 합니다.

[!code-csharp[주](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Route)]

**HttpContext.Request.Headers** 로 변환 합니다.

[!code-csharp[주](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Headers)]

**HttpContext.Request.UserAgent** 로 변환 합니다.

[!code-csharp[주](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Agent)]

**HttpContext.Request.UrlReferrer** 로 변환 합니다.

[!code-csharp[주](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Referrer)]

**HttpContext.Request.ContentType** 로 변환 합니다.

[!code-csharp[주](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Type)]

**HttpContext.Request.Form** 로 변환 합니다.

[!code-csharp[주](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Form)]

> [!WARNING]
> 콘텐츠 하위 형식이 경우 양식 값을 읽기 *x-www-형식-urlencoded* 또는 *양식 데이터*합니다.

**HttpContext.Request.InputStream** 로 변환 합니다.

[!code-csharp[주](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Input)]

> [!WARNING]
> 이 코드는 파이프라인의 끝에 처리기 유형 미들웨어, 에서만 사용 합니다.
>
>요청 마다 한 번만 위에 표시 된 대로 원시 본문을 읽을 수 있습니다. 미들웨어를 첫 번째 읽기 후 본문을 읽는 동안에 빈 본문이 읽습니다.
>
>버퍼에서 작업을 완료 하기 때문에 이전에 표시 된 것 처럼 폼 읽기에 적용 되지 않습니다.

### <a name="httpcontextresponse"></a>HttpContext.Response

**HttpContext.Response.Status** 및 **HttpContext.Response.StatusDescription** 로 변환 합니다.

[!code-csharp[주](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Status)]

**HttpContext.Response.ContentEncoding** 및 **HttpContext.Response.ContentType** 로 변환 합니다.

[!code-csharp[주](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_RespType)]

**HttpContext.Response.ContentType** 에 자체도로 변환 합니다.

[!code-csharp[주](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_RespTypeOnly)]

**HttpContext.Response.Output** 로 변환 합니다.

[!code-csharp[주](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Output)]

**HttpContext.Response.TransmitFile**

파일을 제공 하는 설명 [여기](../fundamentals/request-features.md#middleware-and-request-features)합니다.

**HttpContext.Response.Headers**

때문에 복잡는 보내는 응답 헤더를 설정할 경우에 응답 본문에 아무 것도 기록 된 후은 전송 되지 것입니다.

솔루션 응답 시작에 쓰기 전에 오른쪽 호출 될 콜백 메서드를 설정 하는 것입니다. 시작 부분에 가장 잘 이렇게는 `Invoke` 미들웨어에 메서드. 응답 헤더를 설정 하는이 콜백 메서드는

다음 코드에서는 설정 라는 콜백 메서드가 `SetHeaders`:

```csharp
public async Task Invoke(HttpContext httpContext)
{
    // ...
    httpContext.Response.OnStarting(SetHeaders, state: httpContext);
```

`SetHeaders` 콜백 메서드는 다음과 같습니다.

[!code-csharp[주](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_SetHeaders)]

**HttpContext.Response.Cookies**

브라우저에서 쿠키를 여행 한 *Set-cookie* 응답 헤더입니다. 결과적으로, 쿠키를 보내는 응답 헤더를 보내는 데 사용 된 것과 동일한 콜백 함수가 필요:

```csharp
public async Task Invoke(HttpContext httpContext)
{
    // ...
    httpContext.Response.OnStarting(SetCookies, state: httpContext);
    httpContext.Response.OnStarting(SetHeaders, state: httpContext);
```

`SetCookies` 콜백 메서드는 다음과 같습니다.

[!code-csharp[주](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_SetCookies)]

## <a name="additional-resources"></a>추가 리소스

* [HTTP 처리기 및 HTTP 모듈 개요](https://msdn.microsoft.com/library/bb398986.aspx)

* [구성](../fundamentals/configuration.md)

* [응용 프로그램 시작](../fundamentals/startup.md)

* [미들웨어](../fundamentals/middleware.md)

