---
title: "ASP.NET Core의 라우팅 | Microsoft 문서"
author: ardalis
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: bbbcf9e4-3c4c-4f50-b91e-175fe9cae4e2
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/routing
translationtype: Machine Translation
ms.sourcegitcommit: a7d6980dab89b42cb63bb6840202719e802da4f3
ms.openlocfilehash: 14b8635ce517ae9c86e87a7ef698ea95e9caaa5b
ms.lasthandoff: 03/23/2017

---
# <a name="routing-in-aspnet-core"></a>ASP.NET Core의 라우팅

여 [Ryan Nowak](https://github.com/rynowak), [Steve Smith](http://ardalis.com), 및 [Rick Anderson](https://twitter.com/RickAndMSFT)

라우팅 기능은 경로 처리기에 들어오는 요청을 매핑하는 일을 담당 합니다. 경로 ASP.NET 응용 프로그램에 정의 되 고 앱이 시작 되 면 구성 합니다. 경로 요청에 포함 된 URL에서 필요에 따라 값을 추출할 수 및 이러한 값 요청 처리를 위해 사용할 수 있습니다. ASP.NET 응용 프로그램에서 경로 정보를 사용 하 여 라우팅 기능은 또한 경로 처리기에 매핑되는 Url을 생성할 수 있습니다. 따라서 라우팅이 URL 또는 경로 처리기 정보에 따라 지정 된 경로 처리기에 해당 하는 URL에 따라 경로 처리기를 찾을 수 있습니다.

>[!IMPORTANT]
> 이 문서에서는 라우팅 낮은 수준의 ASP.NET 핵심을 설명 합니다. ASP.NET 핵심 MVC 라우팅에 대 한 참조 [컨트롤러 작업에 대 한 라우팅을](../mvc/controllers/routing.md)

[샘플 코드 보기 또는 다운로드](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/routing/sample)

## <a name="routing-basics"></a>라우팅 기본 사항

라우팅 사용 하 여 *경로* (구현의 [IRouter](https://docs.microsoft.com/en-us/aspnet/core/api/microsoft.aspnetcore.routing.irouter) 에:

* 들어오는 요청을 매핑할 *경로 처리기*

* 응답에 사용 되는 Url을 생성 합니다.

일반적으로 응용 프로그램을 단일 컬렉션의 경로 있습니다. 요청이 도착 하면 경로 컬렉션 순서 대로 처리 됩니다. 들어오는 요청의 요청 URL을 호출 하 여 일치 하는 경로 찾습니다는 `RouteAsync` 메서드 경로 컬렉션에 사용 가능한 각 경로에 있습니다. 이와 반대로 응답 라우팅을 사용 하 여 경로 정보에 따라 (예: 리디렉션 또는 링크) Url을 생성 하 고 따라서 하드 코드 Url, 유지 관리 하도록 돕는 필요가 없도록 수 있습니다.

에 연결 된 라우팅는 [미들웨어](middleware.md) 파이프라인은 `RouterMiddleware` 클래스입니다. [ASP.NET MVC](../mvc/overview.md) 해당 구성의 일부로 라우팅 미들웨어 파이프라인에 추가 합니다. 에 독립 실행형 구성 요소로 라우팅을 사용 하는 방법에 대 한 자세한 참조 [를 사용 하 여 라우팅-미들웨어](#using-routing-middleware)합니다.

<a name=url-matching-ref></a>

### <a name="url-matching"></a>일치 하는 URL

라우팅 디스패치 하 여 들어오는 요청을 하는 프로세스는 일치 하는 URL을 *처리기*합니다. 이 프로세스는 일반적으로 데이터를 기반으로 URL 경로에 있지만 요청에 있는 모든 데이터를 고려 하도록 확장할 수 있습니다. 처리기를 구분 하는 요청을 디스패치 하는 기능은 키 크기와 응용 프로그램의 복잡성을 확장 하는 데 합니다.

들어오는 요청을 입력에서 `RouterMiddleware`를 호출 하는 `RouteAsync` 메서드 시퀀스의 각 경로를 합니다. `IRouter` 인스턴스 선택 것인지 *처리* 설정 하 여 요청 된 `RouteContext` `Handler` 에 null이 아닌 `RequestDelegate`. 경로 요청에 대 한 처리기로 설정 하는 경우 처리가 중지 되 고 처리기 경로 요청을 처리할 호출 됩니다. 미들웨어를 호출 하는 모든 경로 시도한 경우 처리기가 발견 되지 요청에 대해 *다음* 요청 파이프라인의 다음 미들웨어를 호출 합니다.

에 대 한 기본 입력 `RouteAsync` 는 `RouteContext` `HttpContext` 현재 요청과 관련 된 합니다. `RouteContext.Handler` 및 `RouteContext` `RouteData` 경로가 일치 한 후 설정 될 출력 합니다.

일치 하는 동안 `RouteAsync` 또한의 속성을 설정 합니다는 `RouteContext.RouteData` 를 지금까지 수행 하는 요청 처리에 따라 적절 한 값입니다. 경로가 일치를 요청 하는 경우는 `RouteContext.RouteData` 에 대 한 중요 한 상태 정보가 포함 됩니다는 *결과*합니다.

`RouteData``Values` 의 사전 인 *값 라우팅할* 경로에서 생성 합니다. 이러한 값은 일반적으로 URL을 토큰화 하 여 결정, 사용자 입력을 수락 하거나 응용 프로그램 내부 추가로 디스패치 내리는 데 사용할 수 있습니다.

`RouteData``DataTokens` 일치 하는 경로 관련 된 추가 데이터의 속성 모음입니다. `DataTokens`응용 프로그램 경로 나중에 기반 하는 결정을 내릴 수 있도록 각 경로 사용 하 여 데이터 일치 하는 연결 상태를 지원 하기 위해 제공 됩니다. 이러한 값은 개발자 정의 및 수행 **하지** 어떤 식으로든에서 라우팅의 동작에 영향을 줍니다. 또한 데이터 토큰에 보관 하는 값이 고 문자열을 쉽게 변환할 수 있어야 하는 경로 값을 달리 모든 형식의 수 있습니다.

`RouteData``Routers` 은 목록에서 요청을 성공적으로 일치에 일조 하는 경로입니다. 경로 서로 중첩 될 수 있습니다 및 `Routers` 속성은 논리적 트리는 일치에서 발생 하는 경로를 통해 경로 반영 합니다. 일반적으로 첫 번째 항목 `Routers` 경로 컬렉션 이며 URL 생성에 사용 해야 합니다. 마지막 항목 `Routers` 는 일치 하는 경로입니다.

### <a name="url-generation"></a>URL 생성

URL 생성 되는 프로세스는 라우팅에 의해 경로 값의 집합을 기반으로 하는 URL 경로 만들 수 있습니다. 따라서 처리기 및 액세스 하는 Url 사이의 논리적 분리를 수 있습니다.

URL 생성 유사한 반복적인 프로세스를 따르지만 사용자 또는 프레임 워크 코드에 대 한 호출로 시작 된 `GetVirtualPath` 메서드의 경로 컬렉션입니다. 각 *경로* 가 해당 `GetVirtualPath` 메서드는 시퀀스의 null이 아닌 될 때까지 호출 `VirtualPathData` 반환 됩니다.

입력 하는 주 `GetVirtualPath` 됩니다.

* `VirtualPathContext` `HttpContext`

* `VirtualPathContext` `Values`

* `VirtualPathContext` `AmbientValues`

제공 하는 경로 값에 주로 사용 하 여 경로 `Values` 및 `AmbientValues` 를 결정 한 다음 URL을 생성할 수 하는 값을 포함 합니다. `AmbientValues` 은 현재 요청 라우팅 시스템에 일치 하는에서 생성 된 경로 값의 집합입니다. 반면, `Values` 은 현재 작업에 대 한 원하는 URL을 생성 하는 방법을 지정 하는 경로 값입니다. `HttpContext` 경로 서비스 또는 현재 컨텍스트와 연결 된 추가 데이터를 가져와야 할 경우에 제공 됩니다.

>[!TIP]
> 생각 하면 `Values` 에 대 한 재정의 집합이 있는 것으로 `AmbientValues`합니다. URL 생성에서 현재 요청을 손쉽게 Url을 생성 하는 같은 경로 또는 경로 값을 사용 하 여 링크에 대 한 경로 값을 다시 사용 하려고 시도 합니다.

출력 `GetVirtualPath` 는 `VirtualPathData`합니다. `VirtualPathData`병렬 `RouteData`; 포함 된 `VirtualPath` 경로 의해 설정 해야 하는 몇 가지 추가 속성 뿐만 아니라 출력 URL에 대 한 합니다.

`VirtualPathData` `VirtualPath` 속성에 포함 된 *가상 경로* 경로 의해 생성 된 합니다. 필요에 따라 나중에 경로 처리 해야 합니다. 예를 들어, 생성 된 URL을 HTML로 렌더링 하려는 경우 응용 프로그램의 기본 경로 앞에 추가 해야 합니다.

`VirtualPathData` `Router` 성공적으로 URL을 생성 하는 경로에 대 한 참조입니다.

`VirtualPathData` `DataTokens` 속성은 URL을 생성 하는 경로에 관련 된 추가 데이터의 사전입니다. 이의 병렬 `RouteData.DataTokens`합니다.

### <a name="creating-routes"></a>경로 만들기

라우팅은 제공는 `Route` 클래스의 표준 구현으로 `IRouter`합니다. `Route`사용 하 여는 *경로 템플릿* URL 경로 대해 일치 하는 패턴을 정의 하는 구문을 때 `RouteAsync` 라고 합니다. `Route`템플릿을 사용 하 여 동일한 경로 URL 생성 때 `GetVirtualPath` 라고 합니다.

대부분의 응용 프로그램 경로 호출 하 여 만듭니다 `MapRoute` 에 정의 된 이와 유사한 확장 메서드 중 하나 또는 `IRouteBuilder`합니다. 인스턴스를 만들 것은 이러한 모든 메서드에 `Route` 경로 컬렉션에 추가 합니다.

> [!NOTE]
> `MapRoute`경로 처리기 매개 변수-갖지에서 처리 되는 경로 추가는 `DefaultHandler`합니다. 기본 처리기는는 `IRouter`, 요청을 처리할 필요가 결정할 수도 있습니다. 예를 들어 ASP.NET MVC만 처리 하는 기본 처리기는 사용 가능한 컨트롤러와 작업 일치 하는 요청으로 일반적으로 구성 됩니다. MVC에 라우팅에 대 한 자세한 참조 [컨트롤러 작업에 대 한 라우팅을](../mvc/controllers/routing.md)합니다.

이것은의 예로 `MapRoute` 일반적인 ASP.NET MVC 경로 정의에서 사용 하는 호출 합니다.

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

이 템플릿은 같은 URL 경로 일치시킬지 `/Products/Details/17` 경로 값을 추출 하 고 `{ controller = Products, action = Details, id = 17 }`합니다. URL 경로 세그먼트를 분할 하 고 각 세그먼트와 일치 하 여 경로 값을 결정은 *경로 매개 변수가* 경로 서식 파일의 이름입니다. 경로 매개 변수 이름이 지정 됩니다. 매개 변수 이름을 괄호로 묶어 정의 된 `{ }`합니다.

위의 서식 파일의 URL 경로 일치 수 `/` 값 생성 및 `{ controller = Home, action = Index }`합니다. 때문에 이런는 `{controller}` 및 `{action}` 경로 매개 변수는 기본값이 고 `id` 경로 매개 변수는 선택 사항입니다. 본사 `=` 기호와 값 후 경로 매개 변수 이름 매개 변수에 대해 기본값을 정의 합니다. 물음표 `?` 후 선택적으로 매개 변수를 정의 하는 경로 매개 변수 이름입니다. 경로 매개 변수 이며 기본값은 *항상* 경로가 일치-선택적 매개 변수는 경로 값을 생성 하지 않습니다 없는 해당 하는 URL 경로 세그먼트 되었으면 하는 경우 경로 값을 생성 합니다.

참조 [경로 템플릿 참조](#route-template-reference) 철저 한 경로 템플릿 기능 및 구문에 대 합니다.

이 예제에서는 포함 된 *제약 조건 라우팅할*:

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id:int}");
```

이 템플릿은 같은 URL 경로 일치시킬지 `/Products/Details/17`, 하지 않고 `/Products/Details/Apples`합니다. 경로 매개 변수 정의 `{id:int}` 정의 *제약 조건 라우팅할* 에 대 한는 `id` 경로 매개 변수가 있습니다. 경로 제약 조건을 구현 `IRouteConstraint` 에서 확인을 위해 경로 값을 검사 합니다. 이 예에서 경로 값 `id` 정수로 변환할 수 있어야 합니다. 참조 [경로 제약 조건 참조](#route-constraint-reference) 프레임 워크에서 제공 되는 경로 제약 조건에 대 한 자세한 설명은 대 한 합니다.

추가 오버 로드가 `MapRoute` 에 값을 허용 `constraints`, `dataTokens`, 및 `defaults`합니다. 이러한 추가 매개 변수도 `MapRoute` 형식으로 정의 된 `object`합니다. 이러한 매개 변수의 일반적인 사용법은 익명 형식 일치 항목의 속성 이름을 경로 매개 변수 이름은 여기서는 익명으로 형식화 된 개체를 전달 합니다.

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

>[!TIP]
> 제약 조건 및 기본값을 정의 하기 위한 인라인 구문을 단순 경로 대 한 더 편리할 수 있습니다. 그러나 인라인 구문을 사용 하 여 지원 되지 않는 데이터 토큰 같은 기능이 있습니다.

이 예제에서는 몇 가지 기능을 보여 줍니다.

```csharp
routes.MapRoute(
  name: "blog",
  template: "Blog/{*article}",
  defaults: new { controller = "Blog", action = "ReadArticle" });
```

이 템플릿은 같은 URL 경로 일치시킬지 `/Blog/All-About-Routing/Introduction` 값을 추출 하 고 `{ controller = Blog, action = ReadArticle, article = All-About-Routing/Introduction }`합니다. 기본 경로 대 한 값 `controller` 및 `action` 서식 파일에 해당 경로 매개 변수가 없는 경우에 경로 의해 생성 됩니다. 기본값은 경로 템플릿에 지정할 수 있습니다. `article` 로 경로 매개 변수는 정의 *포괄적인* 는 asterix 모양으로 `*` 경로 매개 변수 이름 앞입니다. 포괄적인 경로 매개 변수는 URL 경로의 나머지를 캡처하려고 하 고 빈 문자열과 일치할 수도 있습니다.

이 예에서는 제약 조건 및 데이터 토큰에 경로 추가합니다.

```csharp
routes.MapRoute(
    name: "us_english_products",
    template: "en-US/Products/{id}",
    defaults: new { controller = "Products", action = "Details" },
    constraints: new { id = new IntRouteConstraint() },
    dataTokens: new { locale = "en-US" });
```

이 템플릿은 같은 URL 경로 일치 `/en-US/Products/5` 값을 추출 하 고 `{ controller = Products, action = Details, id = 5 }` 데이터 토큰 및 `{ locale = en-US }`합니다.

![지역 Windows 토큰](routing/_static/tokens.png)

<a name=id1></a>

### <a name="url-generation"></a>URL 생성

`Route` 클래스 해당 경로 템플릿을 사용 하 여 경로 값의 집합을 결합 하 여 URL 생성을 수행할 수도 있습니다. 이 논리적으로 역방향 URL 경로 일치 시키는 프로세스입니다.

>[!TIP]
> URL 생성을 보다 잘 이해 하려면 어떤 URL을 생성 하 고 경로 템플릿 해당 URL은 일치 하는 방법을 생각해 한다고 가정 합니다. 어떤 값 생성 됩니다. 이 거의 동등 URL 생성에서 어떻게 작동 하는지는 `Route` 클래스입니다.

이 예제에서는 기본 ASP.NET MVC 스타일 경로 사용 합니다.

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

경로 값을 가진 `{ controller = Products, action = List }`,이 경로 URL 생성 `/Products/List`합니다. 경로 값의 URL 경로 구성 하기 위해 해당 경로 매개 변수를 대체 합니다. 이후 `id` 는 선택적 매개 변수를 경로, 값 없다는 문제가 되지 않습니다.

경로 값을 가진 `{ controller = Home, action = Index }`,이 경로 URL 생성 `/`합니다. 제공 된 경로 값 해당 값에 해당 하는 세그먼트 안전 하 게 생략 될 수 있도록 기본 값과 일치 합니다. 에 불과하며 생성 된 Url 모두 경로 정의 가진 라운드트립은 URL을 생성 하는 데 사용 된 동일한 경로 값을 생성 합니다.

>[!TIP]
> ASP.NET MVC를 사용 하는 앱을 사용할지 `UrlHelper` 직접 라우팅에 호출 하는 대신 Url을 생성할 수 있습니다.

URL 생성 프로세스에 대 한 자세한 내용은 참조 하십시오. [url 생성 참조](#url-generation-reference)합니다.

## <a name="using-routing-middleware"></a>라우팅 미들웨어를 사용 하 여

"Microsoft.AspNetCore.Routing" NuGet 패키지를 추가 합니다.

라우팅 서비스 컨테이너에 추가 *Startup.cs*:

[!code-csharp[주](../fundamentals/routing/sample/RoutingSample/Startup.cs?highlight=3&start=11&end=14)]

구성 해야 라우팅합니다는 `Configure` 에서 메서드는 `Startup` 클래스입니다. 아래 샘플은 이러한 Api를 사용합니다.

* `RouteBuilder`
* `Build`
* `MapGet`HTTP GET 요청만 일치
* `UseRouter`

<!-- literal_block {"xml:space": "preserve", "source": "fundamentals/routing/sample/RoutingSample/Startup.cs", "ids": [], "linenos": false, "highlight_args": {"linenostart": 1}} -->

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
        "package/{operation:regex(^track|create|detonate$)}/{id:int}");

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

아래 표에 지정 된 Uri는 응답 합니다.

| URI | 응답  |
| ------- | -------- |
| /package/create/3  | Hello! 값 경로: [작업을 만듭니다], [id, 3] |
| / /&3;을 패키지 추적 합니다.  | Hello! 값 경로: [작업, 트랙], [id,-3] |
| 패키지 / / /-3 추적 / | Hello! 값 경로: [작업, 트랙], [id,-3]  |
| /package/트랙 / | \<제어 일치 하는 항목이 이동 > |
| /Hello/Joe 가져오기 | 안녕하세요, Joe! |
| POST /hello/Joe | \<이동, 일치 하는 HTTP GET 항목만 > |
| /Hello/Joe/Smith 가져오기 | \<제어 일치 하는 항목이 이동 > |

단일 경로 구성 하는 경우 호출 `app.UseRouter` 에 전달 된 `IRouter` 인스턴스. 호출 하지 않아도 `RouteBuilder`합니다.

프레임 워크와 같은 경로 만드는 데 사용 되는 확장 메서드의 집합을 제공 합니다.

* `MapRoute`
* `MapGet`
* `MapPost`
* `MapPut`
* `MapDelete`
* `MapVerb`

와 같은 이러한 메서드의 일부 `MapGet` 필요는 `RequestDelegate` 를 제공 해야 합니다. `RequestDelegate` 로 사용 될는 *경로 처리기* 경로가 일치 하는 경우. 이 제품군의 다른 메서드에 경로 처리기로 사용 되는 미들웨어 파이프라인을 구성할 수 있음 하는 경우는 *맵* 메서드는 같은 한 처리기를 허용 하지 않습니다 `MapRoute`, 다음은 사용 하 여는 `DefaultHandler`합니다.

`Map[Verb]` 메서드 제약 조건을 사용 하 여 메서드 이름에는 HTTP 동사에 대 한 경로 제한 합니다. 예를 들어 참조 [MapGet](https://github.com/aspnet/Routing/blob/1.0.0/src/Microsoft.AspNetCore.Routing/RequestDelegateRouteBuilderExtensions.cs#L85-L88) 및 [MapVerb](https://github.com/aspnet/Routing/blob/1.0.0/src/Microsoft.AspNetCore.Routing/RequestDelegateRouteBuilderExtensions.cs#L156-L180)합니다.

## <a name="route-template-reference"></a>경로 템플릿 참조

중괄호 안에 토큰 (`{ }`) 정의 *매개 변수를 라우팅할* 는 바인딩될 경로가 일치 하는 경우. 경로 세그먼트에 둘 이상의 경로 매개 변수를 정의할 수 있지만 리터럴 값으로 구분 해야 합니다. 예를 들어 `{controller=Home}{action=Index}` 사이의 없는 리터럴 값 이므로 한 유효한 경로 설정 되지 않습니다 `{controller}` 및 `{action}`합니다. 이러한 경로 매개 변수는 이름이 있어야 합니다. 하 고 추가 특성 지정 했습니다.

경로 매개 변수 이외의 리터럴 텍스트 (예를 들어 `{id}`) 경로 구분 기호 및 `/` URL에 대 한 텍스트와 일치 해야 합니다. 텍스트 일치 이며 대/소문자 구분에 기반한 Url 경로의 디코딩된 표현 합니다. 리터럴 경로 매개 변수 구분 기호에 맞게 `{` 또는 `}`, 문자를 반복 하 여 이스케이프 처리 (`{{` 또는 `}}`).

선택적 파일 확장명을 가진 파일 이름을 캡처를 시도 하는 URL 패턴은 모두 추가로 고려해 야 합니다. 예를 들어, 템플릿을 사용 하 여 `files/{filename}.{ext?}` -모두 `filename` 및 `ext` 존재, 값이 모두 채워집니다. 만 `filename` 때문에 경로 일치를 사용 하 고 URL에 있는 마침표 `.` 선택 사항입니다. 다음 Url이이 경로 일치 합니다.

* `/files/myFile.txt`
* `/files/myFile.`
* `/files/myFile`

사용할 수는 `*` 접두사로 문자이 URI의 나머지 부분에 바인딩하는 경로 매개 변수를 이라고는 *포괄적인* 매개 변수입니다. 예를 들어 `blog/{*slug}` 시작 하는 모든 URI 일치 `/blog` 리에 다음 값 (에 할당 하는 `slug` 값 경로). 포괄적인 매개 변수가 빈 문자열을 일치도 수 있습니다.

경로 매개 변수가 있을 수 있습니다 *기본값*구분 하 여 매개 변수 이름 뒤 기본값을 지정 하 여 지정 된 프로그램 `=`합니다. 예를 들어 `{controller=Home}` 정의 `Home` 에 대 한 값을 기본값으로 `controller`합니다. 매개 변수에 대 한 URL에 있는 값이 없으면 기본값이 사용 됩니다. 기본 값 외에도 경로 매개 변수를 생략할 수 있습니다 (추가 하 여 지정 된 한 `?` 다음과 같이 매개 변수 이름의 끝에 `id?`). 선택적 간의 차이 경로 매개 변수 이며 기본값은 값을 항상 생성 하는 "가 기본값"이 고 선택적 매개 변수 하나를 제공 하는 경우에 값을 갖습니다.

경로 매개 변수는 URL에서 바인딩된 경로 값과 일치 해야 하는 제약 조건이 있을 수도 있습니다. 콜론 추가 `:` 및 경로 매개 변수 이름 지정 후 제약 조건 이름은 *인라인 제약 조건* 경로 매개 변수입니다. 제약 조건에는 괄호 안에 제공 된 인수가 필요한 경우 `( )` 후 제약 조건 이름입니다. 다른 콜론을 추가 하 여 여러 개의 인라인 제약 조건을 지정할 수 있습니다 `:` 및 제약 조건 이름입니다. 제약 조건 이름에 전달 되는 `IInlineConstraintResolver` 서비스의 인스턴스를 만드는 `IRouteConstraint` URL 처리에서 사용 하 여 합니다. 예를 들어 경로 템플릿 `blog/{article:minlength(10)}` 지정는 `minlength` 인수 제약 조건을 `10`합니다. 자세한 설명 경로 제약 조건 및 프레임 워크에서 제공 하는 제약 조건의 목록에 대 한 참조 [경로 제약 조건 참조](#route-constraint-reference)합니다.

다음 표에서 몇 가지 경로 템플릿 및 해당 동작을 보여 줍니다.

| 경로 템플릿 | 일치 하는 URL 예 | 노트 |
| -------- | -------- | ------- |
| hello  | /hello  | 단일 경로만 일치`/hello` |
| {페이지 홈 =} | / | 일치 하 고 설정 `Page` 를`Home` |
| {페이지 홈 =}  | / 연락처  | 일치 하 고 설정 `Page` 를`Contact` |
| {컨트롤러} / {동작} / {id}? | / 제품/목록 | 매핑됩니다 `Products` 컨트롤러 및 `List` 동작 |
| {컨트롤러} / {동작} / {id}? | / 제품/세부 정보/123  |  매핑됩니다 `Products` 컨트롤러 및 `Details` 동작 합니다.  `id`123으로 설정 |
| {컨트롤러 홈 =} / {action = 인덱스} / {id}? | /  |  매핑됩니다 `Home` 컨트롤러 및 `Index` 메서드도 있습니다. `id` 는 무시 됩니다. |

템플릿을 사용 하는 일반적으로 가장 간단한 방법은 라우팅입니다. 제약 조건 및 기본값을 경로 템플릿 외부 지정할 수도 있습니다.

>[!TIP]
> 사용 하도록 설정 [로깅](logging.md) 보려면 어떻게와 같은 라우팅 구현에 기본 제공 `Route`, 요청과 일치 합니다.

## <a name="route-constraint-reference"></a>경로 제한 참조

경로 제약 조건이 될 때 실행할는 `Route` 들어오는 URL의 구문은 일치 하 고 URL 경로 경로 값으로 토큰화 됩니다. 일반적으로 경로 제약 조건이 경로 템플릿을 통해 연결 된 경로 값을 검사 하 고 예/아니요 값이 허용 여부에 대 한 의사 결정 하는 간단한 확인 하십시오. 일부 경로 제약 조건이 경로 값 외부 데이터를 사용 하 여 요청을 라우팅할 수 있는지 여부를 고려해 야 합니다. 예를 들어는 `HttpMethodRouteConstraint` 수락 하거나 해당 HTTP 동사에 따라 요청을 거부할 수 있습니다.

>[!WARNING]
> 에 대 한 제약 조건을 사용 하지 않도록 **입력 유효성 검사**이므로 이렇게 잘못 된 입력 대신 적절 한 오류 메시지와 함께 400 404 (찾을 수 없음) 발생 합니다. 경로 제약 조건이 해야 하는 데 사용할 **명확 하 게** 하지 특정 경로 대 한 입력의 유효성을 검사 하려면 비슷한 경로 서로 합니다.

다음 표에서 몇 가지 경로 제약 조건 및 예상 되는 동작을 보여 줍니다.

| 제약 조건 | 예제 | 예제에서는 일치 하는 항목 | 노트 |
| --------   | ------- | ------------- | ----- |
| `int` | `{id:int}` | `123456789`, `-123456789`  | 임의의 정수와 일치 |
| `bool`  | `{active:bool}` | `true`, `FALSE` | 일치 하는 항목 `true` 또는 `false` (대/소문자 구분) |
| `datetime` | `{dob:datetime}` | `2016-12-31`, `2016-12-31 7:32pm`  | 일치 하는 유효한 `DateTime` 값 (-고정 문화권에 경고 참조) |
| `decimal` | `{price:decimal}` | `49.99`, `-1,000.01` | 일치 하는 유효한 `decimal` 값 (-고정 문화권에 경고 참조) |
| `double`  | `{weight:double}` | `1.234`, `-1,001.01e8` | 일치 하는 유효한 `double` 값 (-고정 문화권에 경고 참조) |
| `float`  | `{weight:float}` | `1.234`, `-1,001.01e8` | 일치 하는 유효한 `float` 값 (-고정 문화권에 경고 참조) |
| `guid`  | `{id:guid}` | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | 일치 하는 유효한 `Guid` 값 |
| `long` | `{ticks:long}` | `123456789`, `-123456789` | 일치 하는 유효한 `long` 값 |
| `minlength(value)` | `{username:minlength(4)}` | `Rick` | 문자열 4 자 이상 이어야 합니다. |
| `maxlength(value)` | `{filename:maxlength(8)}` | `Richard` | 문자열에 8 개 미만의 자 여야 합니다. |
| `length(length)` | `{filename:length(12)}` | `somefile.txt` | 문자열에 정확 하 게 12 자 이어야 합니다. |
| `length(min,max)` | `{filename:length(8,16)}` | `somefile.txt` | 문자열 적어도 8 및 16 개 이상의 자 이어야 |
| `min(value)` | `{age:min(18)}` | `19` | 정수 값에 18 이상 있어야 합니다. |
| `max(value)` | `{age:max(120)}` |  `91` | 정수 값 120 개 이하의 여야 합니다. |
| `range(min,max)` | `{age:range(18,120)}` | `91` | 18 이상 하지만 120 개 이하의 정수 값 이어야 합니다. |
| `alpha` | `{name:alpha}` | `Rick` | 문자열은 알파벳 문자를 하나 이상 구성 되어야 합니다 (`a`-`z`, 대/소문자 구분) |
| `regex(expression)` | `{ssn:regex(^\\d{{3}}-\\d{{2}}-\\d{{4}}$)}` | `123-45-6789` | 문자열에 정규식과 일치 해야 합니다 (정규식을 정의 하는 방법에 대 한 팁을 참조 하십시오.) |
| `required`  | `{name:required}` | `Rick` |  비-매개 변수 값을 URL 생성 하는 동안 존재 하도록 하는 데 사용 |

>[!WARNING]
> URL을 확인 하는 경로 제약 조건이 CLR 형식으로 변환할 수 있습니다 (예: `int` 또는 `DateTime`) URL은 지역화할 수 없는 가정-항상 고정 문화권을 사용 합니다. 프레임 워크에서 제공한 경로 제약 조건이 경로 값에 저장 된 값을 수정 하지 마십시오. URL에서 구문 분석 하는 모든 경로 값은 문자열로 저장 됩니다. 예를 들어는 [Float 경로 제약 조건을](https://github.com/aspnet/Routing/blob/1.0.0/src/Microsoft.AspNetCore.Routing/Constraints/FloatRouteConstraint.cs#L44-L60) 경로 값을 float으로 변환 하려고 하지만 변환 된 값이 float로 변환할 수 확인에 사용 됩니다.

>[!TIP]
> 정규식 구분 기호 및 라우팅 및 C# 언어에서 사용 하는 것과 유사한 토큰을 사용 하 고 이러한 토큰 이스케이프 되어야 합니다. 예를 들어, 정규식을 사용 하 여 `^\d{3}-\d{2}-\d{4}$` 필요 합니다, 라우팅에는 `\` 로 입력 된 문자 `\\` 이스케이프 하기 위해 C# 소스 파일에는 `\` 이스케이프 문자는 문자열 (사용 하지 않는 [축 자 문자열 리터럴은](https://msdn.microsoft.com/en-us/library/aa691090(v=vs.71).aspx)). `{` 및 `}` 문자를 이중으로 라우팅 매개 변수 구분 기호 문자를 이스케이프 하 여 이스케이프 처리 해야 합니다. 정규식은 `^\\d{{3}}-\\d{{2}}-\\d{{4}}$`합니다.

>[!TIP]
> 라우팅에 사용 된 정규식 종종부터 `^` 끝나야 합니다 (일치 문자열의 시작 위치) 문자는 `$` 문자 (문자열의 위치를 종료 하는 일치) 정규식의 전체 경로 매개 변수 값을 일치 해야 할 수 있도록 합니다. 없이 `^` 및 `$` 문자 정규식 문자열에 종종 것이 바람직하지 않은 내의 하위 문자열 일치 합니다. 예를 들어, 정규식 `[a-z]{2}` 문자열 일치시킬지 `hello` 및 `123abc456` 각 문자열에서 소문자 두 문자 시퀀스를 포함 하기 때문에 합니다. 그러나 정규식 `^[a-z]{2}$` 모두 일치시킬지 `hello` 또는 `123abc456` 예를 들어 두 개의 소문자 문자 시퀀스만 정확 하 게 일치 하기 때문에 `MZ`합니다. 참조 [.NET Framework 정규식](https://msdn.microsoft.com/en-us/library/hs600312(v=vs.110).aspx) 정규식 구문에 대 한 자세한 내용은 합니다.

>[!TIP]
> 알려진된 가능한 값 집합에 대 한 매개 변수를 제한 하려면 정규식을 사용할 수 있습니다 (예를 들어 `{action:regex(list|get|create)}`합니다. 와 일치 하 게만 `action` 값을 라우팅할 `list`, `get`, 또는 `create`합니다. 제약 조건 사전 문자열에 전달 하는 경우 "목록 | 가져오기 | 만들기"는 것과 같습니다. 또한 알려진된 제약 조건 중 하나는 서로 일치 하지 않는 제약 조건 사전 (템플릿 내에서 인라인 하지 않습니다)에 전달 되는 제약을 정규식으로 처리 됩니다.

## <a name="url-generation-reference"></a>URL 생성 참조

아래 예제에서는 경로 값의 사전에 지정 된 경로에 대 한 링크를 생성 하는 방법 및 `RouteCollection`합니다.

[!code-csharp[주](../fundamentals/routing/sample/RoutingSample/Startup.cs?range=45-59)]

`VirtualPath` 은 위의 샘플의 끝에 생성 된 `/package/create/123`합니다.

두 번째 매개 변수를는 `VirtualPathContext` 생성자의 컬렉션인 *앰비언트 값*합니다. 앰비언트 값에는 개발자는 특정 요청 컨텍스트 내에서 지정 해야 하는 값의 수를 제한 하 여 편리 하 게를 제공 합니다. 현재 요청의 현재 경로 값에는 링크 생성에 대 한 앰비언트 값 간주 됩니다. 예를 들어 있는 경우 ASP.NET MVC 응용 프로그램에 `About` 의 동작은 `HomeController`를 연결할 컨트롤러 경로 값을 지정할 필요가 없습니다는 `Index` 작업 (앰비언트 값 `Home` 사용 됩니다).

매개 변수가 일치 하지 않는 앰비언트 값은 무시 되 고 명시적으로 제공 된 값을 사용 하면 재정의 왼쪽에서 오른쪽으로 이동 하는 경우 앰비언트 값도 무시 됩니다 URL에 있습니다.

아무 것도 일치 하지는 명시적으로 제공 되는 값은 쿼리 문자열에 추가 됩니다. 다음 표에서 결과 경로 템플릿을 사용할 때 `{controller}/{action}/{id?}`합니다.

| 앰비언트 값 | 명시적 값 | 결과 |
| -------------   | -------------- | ------ |
| 컨트롤러 "Home" = | action = "About" | `/Home/About` |
| 컨트롤러 "Home" = | 컨트롤러 동작 "Order" = = "About" | `/Order/About` |
| 컨트롤러 color = "홈" = "Red" | action = "About" | `/Home/About` |
| 컨트롤러 "Home" = | action = "정보" 색 = "Red" | `/Home/About?color=Red`

경로 매개 변수에 해당 하지 않는 기본 값이 해당 값이 명시적으로 제공 하는 경우 기본값을 같아야 합니다. 예:

```csharp
routes.MapRoute("blog_route", "blog/{*slug}",
  defaults: new { controller = "Blog", action = "ReadPost" });
```

만 링크 생성은 컨트롤러와 작업에 대 한 일치 하는 값을 제공 하는 경우이 경로 대 한 링크를 생성 해야 합니다.

