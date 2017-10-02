---
title: "ASP.NET Core의 라우팅"
author: ardalis
description: "ASP.NET Core 라우팅 기능 경로 처리기에 들어오는 요청을 매핑하는 일을 담당 어떻게를 파악 합니다."
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: bbbcf9e4-3c4c-4f50-b91e-175fe9cae4e2
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/routing
ms.openlocfilehash: 9d24c2956c24a7995b3eeffc19e8c0a827349493
ms.sourcegitcommit: ed401027aac45c5938c917c7f518a33ceffe9f95
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/02/2017
---
# <a name="routing-in-aspnet-core"></a>ASP.NET Core의 라우팅

여 [사람인 Ryan Nowak](https://github.com/rynowak), [Steve Smith](https://ardalis.com/), 및 [Rick Anderson](https://twitter.com/RickAndMSFT)

라우팅 기능은 경로 처리기에 들어오는 요청을 매핑하는 일을 담당 합니다. 경로 ASP.NET 응용 프로그램에 정의 되 고 응용 프로그램 시작 될 때 구성 합니다. 경로 요청에 포함 된 URL에서 필요에 따라 값을 추출할 수 있습니다 및 이러한 값 요청 처리를 위해 사용할 수 있습니다. ASP.NET 응용 프로그램에서 경로 정보를 사용 하 여 라우팅 기능은 또한 경로 처리기에 매핑되는 Url을 생성할 수 있습니다. 따라서 라우팅 URL 또는 경로 처리기 정보에 따라 지정 된 경로 처리기에 해당 하는 URL에 따라 경로 처리기를 찾을 수 있습니다.

>[!IMPORTANT]
> 이 문서에서는 라우팅 낮은 수준의 ASP.NET Core를 설명 합니다. ASP.NET Core MVC 라우팅에 대 한 참조 [컨트롤러 작업에 대 한 라우팅을](../mvc/controllers/routing.md)

[보거나 다운로드 샘플 코드](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/routing/sample) ([다운로드 하는 방법을](xref:tutorials/index#how-to-download-a-sample))

## <a name="routing-basics"></a>라우팅 기본 사항

라우팅 사용 하 여 *경로* (구현의 [IRouter](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.routing.irouter)) 하려면:

* 들어오는 요청을 매핑할 *경로 처리기*

* 응답에 사용 되는 Url을 생성 합니다.

일반적으로 응용 프로그램에 단일 경로의 컬렉션입니다. 요청이 도착 하면 경로 컬렉션 순서 대로 처리 됩니다. 들어오는 요청을 호출 하 여 요청 URL과 일치 하는 경로 찾습니다는 `RouteAsync` 메서드 경로 컬렉션에 사용 가능한 각 경로에 있습니다. 이와 반대로 응답 (예: 리디렉션 또는 링크) Url을 기반으로 경로 정보를 생성 하 라우팅를 사용할 수 있으며 따라서 필요가 하드 코딩 Url, 유지 관리에 도움이 됩니다.

에 연결 되어 라우팅는 [미들웨어](middleware.md) 하 여 파이프라인의 `RouterMiddleware` 클래스입니다. [ASP.NET MVC](../mvc/overview.md) 해당 구성의 일부분으로 라우팅 미들웨어 파이프라인에 추가 합니다. 독립 실행형 구성 요소로 라우팅을 사용 하는 방법에 대 한 자세한 내용은 [를 사용 하 여 라우팅-미들웨어](#using-routing-middleware)합니다.

<a name=url-matching-ref></a>

### <a name="url-matching"></a>일치 하는 URL

URL 일치 하는 라우팅 디스패치 하 여 들어오는 요청을 하는 프로세스는 한 *처리기*합니다. 이 프로세스는 일반적으로 데이터를 기반으로 URL 경로에 하지만 요청에 있는 모든 데이터를 고려해 야 할 확장할 수 있습니다. 처리기를 분리 하는 요청을 디스패치 하는 기능은 키 크기와 복잡 한 응용 프로그램을 확장 합니다.

들어오는 요청 입력는 `RouterMiddleware`, 호출 하는 `RouteAsync` 시퀀스의 각 경로에 메서드. `IRouter` 인스턴스 선택 것인지 *처리* 설정 하 여 요청에서 `RouteContext.Handler` 에 null이 아닌 `RequestDelegate`합니다. 경로 요청에 대 한 처리기를 설정한 경우 처리가 중지 되 고 처리기 경로 요청을 처리 하도록 호출 됩니다. 미들웨어를 호출 하는 모든 경로 시도 하는 경우 요청에 대 한 처리기를 찾을 수 *다음* 요청 파이프라인의 다음 미들웨어를 호출 합니다.

에 대 한 기본 입력 `RouteAsync` 는 `RouteContext.HttpContext` 현재 요청과 연결 된입니다. `RouteContext.Handler` 및 `RouteContext.RouteData` 경로가 일치 후 설정할 수 있는 출력 합니다.

일치 하는 동안 `RouteAsync` 또한의 속성을 설정 합니다는 `RouteContext.RouteData` 를 지금까지 완료 요청 처리에 따라 적절 한 값입니다. 경로 요청을 일치 하는 경우는 `RouteContext.RouteData` 에 대 한 중요 한 상태 정보가 포함 됩니다는 *결과*합니다.

`RouteData.Values`사전 인 *경로 값* 경로에서 생성 합니다. 이러한 값은 일반적으로 URL을 토큰화 하 여 결정, 사용자 입력을 수락 하거나 응용 프로그램 내부 추가로 디스패치 내리는 데 사용할 수 있습니다.

`RouteData.DataTokens`일치 하는 경로 관련 된 추가 데이터의 속성 모음이입니다. `DataTokens`응용 프로그램 경로에 따라 나중에 결정을 내릴 수 있도록 각 경로 사용 하 여 데이터 일치 하는 연결 상태를 지원 하기 위해 제공 됩니다. 이러한 값이 개발자 정의 하 고 수행 **하지** 어떤 방식으로든에서 라우팅의 동작에 영향을 줍니다. 또한 데이터 토큰에 보관 하는 값이 고 문자열을 쉽게 변환할 수 있어야 하는 경로 값을 달리 모든 형식의 수 있습니다.

`RouteData.Routers`요청을 성공적으로 일치에 참여 하는 경로가 목록이입니다. 경로 서로 중첩 될 수 있습니다 및 `Routers` 속성은 일치 하는 발생 하는 경로가 논리 트리를 통해 경로 반영 합니다. 일반적으로 첫 번째 항목 `Routers` 경로 컬렉션 이며 URL 생성을 위해 사용 해야 합니다. 마지막 항목 `Routers` 일치 하는 경로 처리기입니다.

### <a name="url-generation"></a>URL 생성

URL 생성이 프로세스는 라우팅에 의해 경로 값의 집합을 기반으로 하는 URL 경로 만들 수 있습니다. 따라서 처리기와 Url에 액세스 하는 논리 구분 있습니다.

URL 생성 비슷한 반복적인 프로세스를 따르지만 사용자 또는 프레임 워크 코드 호출로 시작는 `GetVirtualPath` 메서드 경로 컬렉션입니다. 각 *경로* 가 해당 `GetVirtualPath` null이 아닌 될 때까지 시퀀스에서 메서드를 호출 `VirtualPathData` 반환 됩니다.

입력 하는 주 `GetVirtualPath` 됩니다.

* `VirtualPathContext.HttpContext`

* `VirtualPathContext.Values`

* `VirtualPathContext.AmbientValues`

제공 된 경로 값에 주로 사용 하 여 경로 `Values` 및 `AmbientValues` 는 URL을 생성할 수와 값 포함을 결정 합니다. `AmbientValues` 라우팅 시스템을 사용 하 여 현재 요청을 일치 하는에서 생성 된 경로 값의 집합입니다. 반면, `Values` 은 현재 작업에 대 한 원하는 URL을 생성 하는 방법을 지정 하는 경로 값입니다. `HttpContext` 경로 서비스 또는 현재 컨텍스트와 연결 된 추가 데이터를 가져와야 할 경우에 제공 됩니다.

팁: 생각할 `Values` 에 대 한 재정의 집합이 있는 것으로 `AmbientValues`합니다. URL 생성을 쉽게 동일한 경로 또는 경로 값을 사용 하 여 링크에 대 한 Url을 생성 하는 현재 요청에서 경로 값을 다시 사용 하려고 시도 합니다.

출력 `GetVirtualPath` 는 `VirtualPathData`합니다. `VirtualPathData`병렬 처리는 `RouteData`; 포함 된 `VirtualPath` 출력 URL 경로 의해 설정 해야 하는 몇 가지 추가 속성에 대 한 합니다.

`VirtualPathData.VirtualPath` 속성에 포함 된 *가상 경로* 경로 의해 생성 합니다. 필요에 따라 추가 경로 처리 해야 합니다. 예를 들어, HTML에서 생성 된 URL을 렌더링 하려는 경우 응용 프로그램의 기본 경로 앞에 추가 해야 합니다.

`VirtualPathData.Router` 성공적으로 URL을 생성 하는 경로에 대 한 참조입니다.

`VirtualPathData.DataTokens` 속성은 URL을 생성 하는 경로 관련 된 추가 데이터의 사전입니다. 이의 병렬 처리 `RouteData.DataTokens`합니다.

### <a name="creating-routes"></a>경로 만들기

제공 된 `Route` 클래스의 표준 구현으로 `IRouter`합니다. `Route`사용 하 여는 *경로 템플릿을* URL 경로 대해 일치 하는 패턴을 정의 하는 구문을 때 `RouteAsync` 라고 합니다. `Route`동일한 경로 템플릿을 URL 생성를 사용 하 여 때 `GetVirtualPath` 라고 합니다.

대부분의 응용 프로그램 경로 호출 하 여 만듭니다 `MapRoute` 에 정의 된 유사한 확장 메서드 중 하나 또는 `IRouteBuilder`합니다. 인스턴스를 만들 것은 이러한 모든 메서드에 `Route` 경로 컬렉션에 추가 합니다.

참고: `MapRoute` 경로 처리기 매개 변수-를 사용 하지 않습니다 추가에서 처리 되는 경로 `DefaultHandler`합니다. 기본 처리기는는 `IRouter`, 요청을 처리 하지 않도록 결정 것입니다. 예를 들어 ASP.NET MVC만 처리 하는 기본 처리기는 사용 가능한 컨트롤러 및 작업와 일치 하는 요청으로 일반적으로 구성 됩니다. Mvc 라우팅에 대 한 자세한 참조 [컨트롤러 작업에 대 한 라우팅을](../mvc/controllers/routing.md)합니다.

이의 예는 `MapRoute` 일반적인 ASP.NET MVC 경로 정의에서 사용 하는 호출:

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

이 서식 파일의 URL 경로 같은 일치시킬지 `/Products/Details/17` 경로 값을 추출 하 고 `{ controller = Products, action = Details, id = 17 }`합니다. URL 경로 세그먼트로 분할 하 고 각 세그먼트와 일치 하 여 경로 값을 결정는 *경로 매개 변수가* 경로 서식 파일의 이름입니다. 경로 매개 변수 이름이 지정 됩니다. 중괄호에 매개 변수 이름을 포함 하 여 정의 된 `{ }`합니다.

위의 서식 파일의 URL 경로 일치 수 `/` 값 생성 및 `{ controller = Home, action = Index }`합니다. 때문에 이런는 `{controller}` 및 `{action}` 경로 매개 변수에 기본값이 및 `id` 경로 매개 변수는 선택 사항입니다. 본사 `=` 경로 매개 변수 이름에는 매개 변수에 대 한 기본값을 정의 하는 후 부호와 값입니다. 물음표 `?` 후 선택적으로 매개 변수를 정의 하는 경로 매개 변수 이름입니다. 기본값은 매개 변수를 라우팅할 *항상* 경로가 일치-선택적 매개 변수는 해당 URL 경로 세그먼트가 있는 경우 경로 값을 생성 하지 때 경로 값을 생성 합니다.

참조 [경로-템플릿-참조](#route-template-reference) 에 대 한 철저 한 설명은 경로 템플릿 기능 및 구문입니다.

이 예제를 포함 한 *제약 조건 라우팅할*:

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id:int}");
```

이 서식 파일의 URL 경로 같은 일치시킬지 `/Products/Details/17`, 아닌 `/Products/Details/Apples`합니다. 경로 매개 변수 정의 `{id:int}` 정의 *제약 조건 라우팅할* 에 대 한는 `id` 경로 매개 변수가 있습니다. 경로 제약 조건을 구현 `IRouteConstraint` 하 고 확인 하는 경로 값을 검사 합니다. 이 예제에서는 경로 값에 `id` 정수로 변환할 수 있어야 합니다. 참조 [경로 제약 조건 참조](#route-constraint-reference) 에 대 한 자세한 설명은 프레임 워크에서 제공 되는 경로 제약 조건이 있습니다.

추가 오버 로드 `MapRoute` 에 값을 허용 `constraints`, `dataTokens`, 및 `defaults`합니다. 이러한 추가 매개 변수 `MapRoute` 형식으로 정의 된 `object`합니다. 이러한 매개 변수의 일반적인 사용법은 익명 형식 일치 항목의 속성 이름을 경로 매개 변수 이름은 익명으로 형식화 된 개체를 전달 합니다.

다음 두 예제는 해당 경로 만듭니다.

```csharp
routes.MapRoute(
    name: "default_route",
    template: "{controller}/{action}/{id?}",
    defaults: new { controller = "Home", action = "Index" });

routes.MapRoute(
    name: "default_route",
    template: "{controller=Home}/{action=Index}/{id?}");
```

팁: 제약 조건 및 기본값을 정의 하기 위한 인라인 구문을 단순 경로 대 한 더 편리할 수 있습니다. 그러나 인라인 구문을 사용 하 여 지원 되지 않는 데이터 토큰 등의 기능 있습니다.

이 예제에서는 몇 가지 기능을 보여 줍니다.

```csharp
routes.MapRoute(
  name: "blog",
  template: "Blog/{*article}",
  defaults: new { controller = "Blog", action = "ReadArticle" });
```

이 서식 파일의 URL 경로 같은 일치시킬지 `/Blog/All-About-Routing/Introduction` 및 값을 추출 합니다 `{ controller = Blog, action = ReadArticle, article = All-About-Routing/Introduction }`합니다. 에 대 한 기본 경로 값 `controller` 및 `action` 서식 파일에 해당 경로 매개 변수가 없는 경우에 경로 의해 생성 됩니다. 기본값은 경로 템플릿에 지정할 수 있습니다. `article` 경로 매개 변수를 이루어집니다는 *범용* 별표 모양으로 `*` 경로 매개 변수 이름 앞입니다. 범용 루트 매개 변수는 URL 경로의 나머지를 캡처하려고와 빈 문자열 일치 수 있습니다.

이 예제에서는 경로 제약 조건 및 데이터 토큰을 추가합니다.

```csharp
routes.MapRoute(
    name: "us_english_products",
    template: "en-US/Products/{id}",
    defaults: new { controller = "Products", action = "Details" },
    constraints: new { id = new IntRouteConstraint() },
    dataTokens: new { locale = "en-US" });
```

이 서식 파일의 URL 경로 같은 일치시킬지 `/Products/5` 및 값을 추출 합니다 `{ controller = Products, action = Details, id = 5 }` 데이터 토큰 및 `{ locale = en-US }`합니다.

![지역 Windows 토큰](routing/_static/tokens.png)

<a name=id1></a>

### <a name="url-generation"></a>URL 생성

`Route` 클래스는 경로 템플릿을 사용 하 여 경로 값의 집합을 결합 하 여 URL 생성을 수행할 수도 있습니다. 논리적으로 역방향 URL 경로 일치 시키는 프로세스입니다.

팁: URL 생성을 보다 잘 이해 하려면 어떤 URL을 생성 하는 경로 템플릿을 해당 URL은 일치 하는 방법을 생각해 가정 합니다. 값 조각이 생성 됩니다. 이 동등에서 URL 생성의 작동 방식는 `Route` 클래스입니다.

이 예제에서는 기본 ASP.NET MVC 스타일 경로 사용합니다.

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

경로 값을 가진 `{ controller = Products, action = List }`,이 경로 URL을 생성 합니다 `/Products/List`합니다. 경로 값에 대 한 URL 경로 구성 하기 위해 해당 경로 매개 변수 대신 사용 됩니다. 이후 `id` 는 선택적 매개 변수를 경로, 값 없다는 문제가 되지 않습니다.

경로 값을 가진 `{ controller = Home, action = Index }`,이 경로 URL을 생성 합니다 `/`합니다. 제공 된 경로 값 해당 값에 해당 하는 세그먼트 안전 하 게 생략 될 수 있으므로 기본 값과 일치 합니다. 에 불과하며 생성 두 Url이 경로 정의로 왕복 합니다는 URL을 생성 하는 데 사용 된 동일한 경로 값을 생성 합니다.

팁: ASP.NET MVC를 사용 하 여 앱 사용 `UrlHelper` 직접 라우팅에 호출 하는 대신 Url을 생성 하 합니다.

URL 생성 프로세스에 대 한 자세한 내용은 참조 하십시오. [url 생성 참조](#url-generation-reference)합니다.

## <a name="using-routing-middleware"></a>라우팅 미들웨어를 사용 하 여

"Microsoft.AspNetCore.Routing" NuGet 패키지를 추가 합니다.

서비스 컨테이너에 대 한 라우팅을 추가 *Startup.cs*:

[!code-csharp[Main](../fundamentals/routing/sample/RoutingSample/Startup.cs?highlight=3&start=11&end=14)]

경로에 구성 해야 합니다는 `Configure` 에서 메서드는 `Startup` 클래스입니다. 아래 예제에서는 이러한 Api를 사용합니다.

* `RouteBuilder`
* `Build`
* `MapGet`HTTP GET 요청만 일치
* `UseRouter`

```csharp
public void Configure(IApplicationBuilder app, ILoggerFactory loggerFactory)
{
    var trackPackageRouteHandler = new RouteHandler(context =>
    {
        var routeValues = context.GetRouteData().Values;
        return context.Response.WriteAsync(
            $"Hello! Route values: {string.Join(", ", routeValues)}");
    });

    var routeBuilder = new RouteBuilder(app, trackPackageRouteHandler);

    routeBuilder.MapRoute(
        "Track Package Route",
        "package/{operation:regex(^(track|create|detonate)$)}/{id:int}");

    routeBuilder.MapGet("hello/{name}", context =>
    {
        var name = context.GetRouteValue("name");
        // This is the route handler when HTTP GET "hello/<anything>"  matches
        // To match HTTP GET "hello/<anything>/<anything>,
        // use routeBuilder.MapGet("hello/{*name}"
        return context.Response.WriteAsync($"Hi, {name}!");
    });

    var routes = routeBuilder.Build();
    app.UseRouter(routes);
}
```

아래 표에 지정 된 Uri에 응답 합니다.

| URI | 응답  |
| ------- | -------- |
| /package/create/3  | Hello! 경로 값: [작업을 만듭니다], [3 id] |
| 패키지/추적/3 /  | Hello! 경로 값: [작업, 트랙] [-3 id] |
| 패키지/추적/3 / / | Hello! 경로 값: [작업, 트랙] [-3 id]  |
| /package/추적 / | \<진행 일치 없음 > |
| /Hello/Joe 가져오기 | 안녕하세요, Joe! |
| POST /hello/Joe | \<진행, 일치 하는 HTTP GET 항목만 > |
| /Hello/Joe/Smith 가져오기 | \<진행 일치 없음 > |

단일 경로 구성 하는 경우 호출 `app.UseRouter` 전달는 `IRouter` 인스턴스. 호출할 필요 하지는 않습니다 `RouteBuilder`합니다.

프레임 워크와 같은 경로 만들기 위한 확장 메서드 집합을 제공 합니다.

* `MapRoute`
* `MapGet`
* `MapPost`
* `MapPut`
* `MapDelete`
* `MapVerb`

와 같은 이러한 메서드의 일부 `MapGet` 필요는 `RequestDelegate` 를 제공 해야 합니다. `RequestDelegate` 으로 사용할 수는 *경로 처리기* 경로 일치 하는 경우. 이 제품군의 다른 메서드에 경로 처리기로 사용 되는 미들웨어 파이프라인을 구성할 수 있도록 합니다. 경우는 *지도* 메서드는 처리기를와 같은 허용 하지 않습니다 `MapRoute`를 사용 합니다는 `DefaultHandler`합니다.

`Map[Verb]` 메서드 제약 조건을 사용 하 여 HTTP 동사 메서드 이름에 경로 제한 합니다. 예를 들어 참조 [MapGet](https://github.com/aspnet/Routing/blob/1.0.0/src/Microsoft.AspNetCore.Routing/RequestDelegateRouteBuilderExtensions.cs#L85-L88) 및 [MapVerb](https://github.com/aspnet/Routing/blob/1.0.0/src/Microsoft.AspNetCore.Routing/RequestDelegateRouteBuilderExtensions.cs#L156-L180)합니다.

## <a name="route-template-reference"></a>경로 템플릿 참조

중괄호 안에 토큰 (`{ }`) 정의 *매개 변수를 라우팅할* 를 바인딩할 경로의 일치 하는 경우. 경로 세그먼트에 둘 이상의 경로 매개 변수를 정의할 수 있지만는 리터럴 값으로 구분 되어야 합니다. 예를 들어 `{controller=Home}{action=Index}` 됩니다 유효한 경로 사이의 리터럴 값이 없는 이므로 `{controller}` 및 `{action}`합니다. 이러한 경로 매개 변수는 이름이 있어야 합니다. 및 추가 특성 지정한 경우 사용할 수 있습니다.

경로 매개 변수 이외의 리터럴 텍스트 (예를 들어 `{id}`) 경로 구분 기호 및 `/` URL에 텍스트와 일치 해야 합니다. 텍스트 일치 이며 대/소문자 구분에 기반한 Url 경로의 디코딩된 표현 합니다. 리터럴 경로 매개 변수 구분 기호에 맞게 `{` 또는 `}`, 문자를 반복 하 여 이스케이프 (`{{` 또는 `}}`).

선택적 파일 확장명을 가진 파일 이름을 캡처를 시도 하는 URL 패턴에는 추가 고려 사항이 있습니다. 예를 들어, 서식 파일을 사용 하 여 `files/{filename}.{ext?}` -둘 다 `filename` 및 `ext` 존재, 두 값이 채워집니다. 경우에 `filename` URL 경로 일치에에서 존재 하기 때문에 끝의 마침표 `.` 는 선택 사항입니다. 다음 Url이이 경로도 일치 합니다.

* `/files/myFile.txt`
* `/files/myFile.`
* `/files/myFile`

사용할 수는 `*` 접두사로 문자 URI의 나머지 부분에 바인딩할 경로 매개 변수를 라고는 *범용* 매개 변수입니다. 예를 들어 `blog/{*slug}` 시작 하는 모든 URI 일치 `/blog` 했으며 다음 값 (하 게 할당 되는 `slug` 값 경로). 범용 매개 변수가 빈 문자열을 일치도 수 있습니다.

경로 매개 변수가 있을 수 *기본값*구분 하 여 매개 변수 이름 뒤에 오는 기본값을 지정 하 여 지정 된 프로그램 `=`합니다. 예를 들어 `{controller=Home}` 정의 `Home` 에 대 한 값을 기본값으로 `controller`합니다. 변수가 URL에 매개 변수 값이 없으면 기본값이 사용 됩니다. 기본 값 뿐만 아니라 경로 매개 변수를 생략할 수 있습니다 (추가 하 여 지정 된 한 `?` 다음과 같이 매개 변수 이름의 끝에 `id?`). 선택적 차이점 경로 매개 변수 이며 기본값은 값을 항상 생성 하는 "가 기본값"이 고 선택적 매개 변수 하나를 제공 하는 경우에 값을 갖습니다.

경로 매개 변수에 바인딩된 URL에서 경로 값과 일치 해야 하는 제약 조건이 있을 수 있습니다. 콜론 추가 `:` 및 경로 매개 변수 이름 지정 후 제약 조건 이름을 *인라인 제약 조건* 경로 매개 변수에서 합니다. 제약 조건을 괄호로 제공 된 인수를 사용 해야 하는 경우 `( )` 제약 조건 이름 뒤에 오는 합니다. 다른 콜론을 추가 하 여 여러 개의 인라인 제약 조건을 지정할 수 있습니다 `:` 및 제약 조건 이름입니다. 제약 조건 이름에 전달 되는 `IInlineConstraintResolver` 서비스의 인스턴스를 만드는 `IRouteConstraint` URL 처리에서 사용 하도록 합니다. 예를 들어 경로 템플릿을 `blog/{article:minlength(10)}` 지정는 `minlength` 제약 조건 인수 `10`합니다. 자세한 설명 경로 제약 조건 및 프레임 워크에서 제공 되는 제약 조건 목록에 대 한 참조 [경로 제약 조건 참조](#route-constraint-reference)합니다.

다음 표에서 일부 경로 템플릿 및 해당 동작을 보여 줍니다.

| 경로 템플릿 | 예를 들어 일치 하는 URL | 참고 |
| -------- | -------- | ------- |
| hello  | /hello  | 단일 경로만 일치`/hello` |
| {페이지 홈 =} | / | 일치 하 고 설정 `Page` 를`Home` |
| {페이지 홈 =}  | / 연락처  | 일치 하 고 설정 `Page` 를`Contact` |
| {controller} / {action} / {id}? | / 제품/목록 | 매핑될 `Products` 컨트롤러 및 `List` 동작 |
| {controller} / {action} / {id}? | / 제품/세부 정보/123  |  매핑될 `Products` 컨트롤러 및 `Details` 동작 합니다.  `id`123으로 설정 |
| {컨트롤러 = Home} / {action = 인덱스} / {id}? | /  |  매핑될 `Home` 컨트롤러 및 `Index` 메서드도 있습니다. `id` 는 무시 됩니다. |

템플릿을 사용 하는 일반적으로 가장 간단한 방식은 라우팅입니다. 제약 조건 및 기본값을 외부 경로 템플릿을 지정할 수도 있습니다.

팁: 사용 하도록 설정 [로깅](logging.md) 참조 하는 방법을 같은 라우팅 구현에 기본 제공 `Route`, 요청과 일치 합니다.

## <a name="route-constraint-reference"></a>경로 제약 조건 참조

경로 제약 조건이 될 때 실행할는 `Route` 받는 URL의 구문은 일치 하 고 URL 경로 경로 값으로 토큰화 됩니다. 일반적으로 경로 제약 조건이 경로 템플릿을 통해 연결 된 경로 값을 검사 하 고 간단한 예/아니요 값이 허용 여부에 대 한 의사 결정을 확인 합니다. 요청을 라우팅할 수 있는지 여부를 고려해 야 할 경로 값 외부에 있는 데이터를 사용 하는 일부 경로 제약 조건. 예를 들어는 `HttpMethodRouteConstraint` 적용 하거나 해당 HTTP 동사에 따라 요청을 취소할 수 있습니다.

>[!WARNING]
> 에 대 한 제약 조건을 사용 하지 않도록 **입력 유효성 검사**하므로 이렇게 하면 해당 잘못 된 입력 하 고 적절 한 오류 메시지가 400 대신 404 (찾을 수 없음) 발생 합니다. 경로 제약 조건이 해야 하는 데 사용할 **명확 하 게** 특정 경로 대 한 입력의 유효성을 검사 하지 비슷한 경로 서로 합니다.

다음 표에서 일부 경로 제약 조건 및 예상 되는 동작을 보여 줍니다.

| 제약 조건 | 예제 | 예제에서는 일치 하는 항목 | 참고 |
| --------   | ------- | ------------- | ----- |
| `int` | `{id:int}` | `123456789`, `-123456789`  | 임의의 정수와 일치 |
| `bool`  | `{active:bool}` | `true`, `FALSE` | 일치 하는 항목 `true` 또는 `false` (대/소문자 구분) |
| `datetime` | `{dob:datetime}` | `2016-12-31`, `2016-12-31 7:32pm`  | 유효한 일치 `DateTime` 값 (-고정 문화권에서 경고 참조) |
| `decimal` | `{price:decimal}` | `49.99`, `-1,000.01` | 유효한 일치 `decimal` 값 (-고정 문화권에서 경고 참조) |
| `double`  | `{weight:double}` | `1.234`, `-1,001.01e8` | 유효한 일치 `double` 값 (-고정 문화권에서 경고 참조) |
| `float`  | `{weight:float}` | `1.234`, `-1,001.01e8` | 유효한 일치 `float` 값 (-고정 문화권에서 경고 참조) |
| `guid`  | `{id:guid}` | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | 유효한 일치 `Guid` 값 |
| `long` | `{ticks:long}` | `123456789`, `-123456789` | 유효한 일치 `long` 값 |
| `minlength(value)` | `{username:minlength(4)}` | `Rick` | 문자열 4 자 이상 이어야 합니다. |
| `maxlength(value)` | `{filename:maxlength(8)}` | `Richard` | 문자열에 8 개 미만의 자 이어야 합니다. |
| `length(length)` | `{filename:length(12)}` | `somefile.txt` | 문자열에 정확 하 게 12 자 여야 합니다. |
| `length(min,max)` | `{filename:length(8,16)}` | `somefile.txt` | 문자열 적어도 8 ~ 없습니다 개 이상의 16 자 여야 합니다. |
| `min(value)` | `{age:min(18)}` | `19` | ं क ज ा 18 이상 |
| `max(value)` | `{age:max(120)}` |  `91` | 정수 값 120 개 이하의 여야 합니다. |
| `range(min,max)` | `{age:range(18,120)}` | `91` | 18 이상 하지만 120 개 이하의 정수 값 이어야 합니다. |
| `alpha` | `{name:alpha}` | `Rick` | 문자열 알파벳 문자를 하나 이상 구성 되어야 합니다 (`a`-`z`, 대/소문자 구분) |
| `regex(expression)` | `{ssn:regex(^\\d{{3}}-\\d{{2}}-\\d{{4}}$)}` | `123-45-6789` | 문자열에 정규식과 일치 해야 합니다 (정규식을 정의 하는 방법에 대 한 팁을 참조 하십시오.) |
| `required`  | `{name:required}` | `Rick` |  비-매개 변수 값을 URL 생성 하는 동안 있는지를 적용 하는 데 사용 |

>[!WARNING]
> URL을 확인 하는 경로 제약 조건이 CLR 형식으로 변환할 수 있습니다 (같은 `int` 또는 `DateTime`) 항상 고정 문화권을 사용 하 여-URL은 지역화할 수 없는 가정 합니다. 프레임 워크에서 제공한 경로 제약 조건이 경로 값에 저장 된 값을 수정 하지 마십시오. URL에서 구문 분석 하는 모든 경로 값을 문자열로 저장 됩니다. 예를 들어는 [Float 경로 제약 조건이](https://github.com/aspnet/Routing/blob/1.0.0/src/Microsoft.AspNetCore.Routing/Constraints/FloatRouteConstraint.cs#L44-L60) float, 경로 값으로 변환 하려고 하지만 변환 된 값이 float로 변환할 수 확인에 사용 됩니다.

## <a name="regular-expressions"></a>정규식 

ASP.NET Core 프레임 워크 추가 `RegexOptions.IgnoreCase | RegexOptions.Compiled | RegexOptions.CultureInvariant` 정규식 생성자에 있습니다. 참조 [RegexOptions 열거형](https://docs.microsoft.com/dotnet/api/system.text.regularexpressions.regexoptions) 이러한 멤버에 대 한 합니다.

정규식 구분 기호 및 라우팅 및 C# 언어에서 사용 하는 것과 유사한 토큰을 사용 합니다. 정규식 토큰 이스케이프 되어야 합니다. 예를 들어, 정규식을 사용 하 `^\d{3}-\d{2}-\d{4}$` 필요 합니다, 라우팅에는 `\` 로 입력 된 문자 `\\` 이스케이프 하는 C# 소스 파일에는 `\` 이스케이프 문자는 문자열 (사용 하지 않는 [축 자 문자열 리터럴](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/string)합니다. `{` , `}` , ' [' 및 ']' 문자를 이중으로 라우팅 매개 변수 구분 기호 문자를 이스케이프 하 여 이스케이프 처리 해야 합니다.  다음 표에서 정규식 및 이스케이프 된 버전을 보여 줍니다.

| 식               | 참고 |
| ----------------- | ------------ | 
| `^\d{3}-\d{2}-\d{4}$` | 정규식 |
| `^\\d{{3}}-\\d{{2}}-\\d{{4}}$` | 이스케이프 처리  |
| `^[a-z]{2}$` | 정규식 |
| `^[[a-z]]{{2}}$` | 이스케이프 처리  |

라우팅에 사용 된 정규식으로 자주 시작 합니다는 `^` 끝나야 합니다 (일치 문자열의 시작 위치) 문자는 `$` 문자 (문자열의 위치를 종료 하는 일치). `^` 및 `$` 문자는 정규식 일치의 전체 경로 매개 변수 값 확인 합니다. 없이 `^` 및 `$` 문자는 정규식에는 종종 않으려면 하는 문자열 내에서 모든 하위 문자열 일치 합니다. 다음 표에서 몇 가지 예가 나와 하 고 일치 하거나 일치 하도록 실패 이유를 설명 합니다.

| 식               | 문자열 | 일치 | 주석 |
| ----------------- | ------------ |  ------------ |  ------------ | 
| `[a-z]{2}` | hello | 예 | 하위 문자열 일치 |
| `[a-z]{2}` | 123abc456 | 예 | 하위 문자열 일치 |
| `[a-z]{2}` | 없으며 mz | 예 | 식과 일치 |
| `[a-z]{2}` | 없으며 MZ | 예 | 하지 대/소문자 구분 |
| `^[a-z]{2}$` |  hello | no | 참조 `^` 및 `$` 위에 |
| `^[a-z]{2}$` |  123abc456 | no | 참조 `^` 및 `$` 위에 |

참조 [.NET Framework 정규식](https://docs.microsoft.com/dotnet/standard/base-types/regular-expression-language-quick-reference) 정규식 구문에 대 한 자세한 내용은 합니다.

가능한 값에 대 한 알려진 집합 매개 변수를 제한 하려면 정규식을 사용 합니다. 예를 들어 `{action:regex(^(list|get|create)$)}` 에 일치는 `action` 값을 라우팅할 `list`, `get`, 또는 `create`합니다. 제약 조건 사전 문자열에 전달 되 면 "^ (목록 | get | 만들기) $"는 것과 같습니다. 또한 알려진된 제한 중 하나에 일치 하지 않는 제약 조건 사전 (템플릿 내에서 인라인 하지)에 전달 되는 제약을 정규식으로 처리 됩니다.

## <a name="url-generation-reference"></a>URL 생성 참조

다음 예제에서는 경로 값의 사전에 지정 된 경로에 대 한 링크를 생성 하는 방법을 보여 줍니다 및 `RouteCollection`합니다.

[!code-csharp[Main](../fundamentals/routing/sample/RoutingSample/Startup.cs?range=45-59)]

`VirtualPath` 은 위의 샘플의 끝 부분에서 생성 된 `/package/create/123`합니다.

두 번째 매개 변수는 `VirtualPathContext` 생성자의 컬렉션인 *앰비언트 값*합니다. 앰비언트 값 개발자는 특정 요청 컨텍스트 내에서 지정 해야 하는 값의 수를 제한 하 여 편의 제공 합니다. 현재 요청의 현재 경로 값에는 링크 생성에 대 한 앰비언트 값 간주 됩니다. 예를 들어 ASP.NET MVC 응용 프로그램에 있는 경우에 `About` 의 동작은 `HomeController`를 연결할 컨트롤러 경로 값을 지정할 필요가 없습니다는 `Index` 작업 (의 앰비언트 값 `Home` 사용 됩니다).

매개 변수가 일치 하지 않는 앰비언트 값은 무시 되 고 앰비언트 값은 무시 되는 명시적으로 제공 값을 사용 하면 재정의 왼쪽에서 오른쪽으로 이동 하는 경우 URL에 있습니다.

명시적으로 제공 되는 되지만 아무 것 과도 일치 하지 않습니다는 값은 쿼리 문자열에 추가 됩니다. 다음 표에서 경로 템플릿을 사용 하는 경우 결과 보여 줍니다 `{controller}/{action}/{id?}`합니다.

| 앰비언트 값 | 명시적 값 | 결과 |
| -------------   | -------------- | ------ |
| 컨트롤러 "홈" = | action = "About" | `/Home/About` |
| 컨트롤러 "홈" = | 컨트롤러 동작 "Order" = = "About" | `/Order/About` |
| 컨트롤러 = "홈" color = "Red" | action = "About" | `/Home/About` |
| 컨트롤러 "홈" = | action = "정보" 색 "Red" = | `/Home/About?color=Red`

경로 매개 변수에 해당 하지 않는 기본 값이 해당 값이 명시적으로 제공 하는 경우 기본값을 일치 해야 합니다. 예:

```csharp
routes.MapRoute("blog_route", "blog/{*slug}",
  defaults: new { controller = "Blog", action = "ReadPost" });
```

만 링크 생성은 컨트롤러 및 작업에 대 한 일치 하는 값에 제공 되는 경우이 경로 대 한 링크를 생성 해야 합니다.
