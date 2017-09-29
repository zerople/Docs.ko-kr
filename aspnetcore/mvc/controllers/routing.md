---
title: "컨트롤러 작업에 대 한 라우팅을"
author: rick-anderson
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 03/14/2017
ms.topic: article
ms.assetid: 26250a4d-bf62-4d45-8549-26801cf956e9
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/controllers/routing
ms.openlocfilehash: 5a0b5399f7441035cb1231a009681ca22b07ab4e
ms.sourcegitcommit: 6e83c55eb0450a3073ef2b95fa5f5bcb20dbbf89
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/28/2017
---
# <a name="routing-to-controller-actions"></a>컨트롤러 작업에 대 한 라우팅을

여 [사람인 Ryan Nowak](https://github.com/rynowak) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core MVC 라우팅 사용 하 여 [미들웨어](../../fundamentals/middleware.md) 를 들어오는 요청의 Url을 일치 시키고 작업에 매핑합니다. 경로 시작 코드 또는 특성에 정의 됩니다. 경로는 URL 경로 동작에 일치 해야 하는 방법을 설명 합니다. 경로 응답에 전송 (링크)에 대 한 Url을 생성 하도 사용 됩니다. 

작업은 일반적으로 라우팅됩니다 하거나 또는 특성 라우팅됩니다. 컨트롤러 또는 작업에 대 한 경로 배치를 사용 하면 특성 라우팅됩니다. 참조 [라우팅 혼합](#routing-mixed-ref-label) 자세한 정보에 대 한 합니다.

이 문서에서는 MVC 라우팅 및 MVC 응용 프로그램 만들기 어떻게 일반적인 간의 상호 작용에 설명 라우팅 기능을 사용 합니다. 참조 [라우팅](xref:fundamentals/routing) 고급 라우팅에 대 한 내용은 합니다.

## <a name="setting-up-routing-middleware"></a>미들웨어 라우팅 설정

사용자 *구성* 메서드 유사한 코드가 표시 될 수 있습니다.

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

에 대 한 호출 안에 `UseMvc`, `MapRoute` 으로 지칭 하는 단일 경로 만드는 데 사용 되는 `default` 경로입니다. 대부분의 MVC 응용 프로그램 경로 사용 하 여 비슷한 템플릿을 사용 하 여는 `default` 경로입니다.

경로 템플릿을 `"{controller=Home}/{action=Index}/{id?}"` 과 같은 URL 경로 일치 시킬 수 `/Products/Details/5` 및 경로 값을 추출 합니다 `{ controller = Products, action = Details, id = 5 }` 경로 토큰화 하 여 합니다. MVC는 라는 컨트롤러를 찾으려고 시도 `ProductsController` 동작을 실행 하 고 `Details`:

```csharp
public class ProductsController : Controller
{
   public IActionResult Details(int id) { ... }
}
```

이 예제에서는 모델 바인딩이 사용 하 여 값 `id = 5` 설정 하는 `id` 매개 변수를 `5` 이 작업을 호출할 때입니다. 참조는 [모델 바인딩](../models/model-binding.md) 내용을 확인 합니다.

사용 하 여 `default` 경로:

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

경로 템플릿.

* `{controller=Home}`정의 `Home` 기본값으로`controller`

* `{action=Index}`정의 `Index` 기본값으로`action`

* `{id?}`정의 `id` 옵션으로

기본 및 선택적 경로 매개 변수에 일치 하는 항목에 대 한 URL 경로에 있어야 않아도 됩니다. 참조 [경로 템플릿 참조](../../fundamentals/routing.md#route-template-reference) 경로 템플릿 구문에 대 한 자세한 설명은 합니다.

`"{controller=Home}/{action=Index}/{id?}"`URL 경로 일치 시킬 수 `/` 및 경로 값이 생성 됩니다 `{ controller = Home, action = Index }`합니다. 에 대 한 값 `controller` 및 `action` 확인의 기본값을 사용 하 여 `id` URL 경로에 해당 하는 세그먼트 이므로 값을 생성 하지 않습니다. MVC를 사용 하 여 이러한 경로 값을 선택 된 `HomeController` 및 `Index` 동작:

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

이 컨트롤러 정의 경로 템플릿을 사용 하는 `HomeController.Index` 다음 URL 경로 대 한 작업을 실행할 수는 있습니다.

* `/Home/Index/17`

* `/Home/Index`

* `/Home`

* `/`

편의 메서드 `UseMvcWithDefaultRoute`:

```csharp
app.UseMvcWithDefaultRoute();
```

사용할 수 바꾸려면:

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

`UseMvc`및 `UseMvcWithDefaultRoute` 의 인스턴스를 추가 `RouterMiddleware` 미들웨어 파이프라인을 합니다. MVC는 미들웨어와 직접 상호 작용 하지 않고 및 라우팅을 사용 하 여 요청을 처리 합니다. MVC의 인스턴스를 통해 경로에 연결 되어 `MvcRouteHandler`합니다. 내부 코드 `UseMvc` 다음과 유사 합니다.

```csharp
var routes = new RouteBuilder(app);

// Add connection to MVC, will be hooked up by calls to MapRoute.
routes.DefaultHandler = new MvcRouteHandler(...);

// Execute callback to register routes.
// routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");

// Create route collection and add the middleware.
app.UseRouter(routes.Build());
```

`UseMvc`모든 경로 직접 정의 하지 않는 자리 표시자에 대 한 경로 컬렉션에 추가 `attribute` 경로입니다. 오버 로드 `UseMvc(Action<IRouteBuilder>)` 직접 경로 추가할 수 있습니다 하 고도 특성 라우팅을 지원 합니다.  `UseMvc`특성 경로 대 한 자리 표시자를 추가 하는 모든 해당 변형 중-특성 라우팅을 항상 구성 하는 방법에 관계 없이 사용할 수 및 `UseMvc`합니다. `UseMvcWithDefaultRoute`기본 경로 정의 하 고 특성 라우팅을 지원 합니다. [특성 라우팅을](#attribute-routing-ref-label) 특성 라우팅을 대 한 자세한 내용은 섹션에 포함 되어 있습니다.

<a name=routing-conventional-ref-label></a>

## <a name="conventional-routing"></a>기존 라우팅

`default` 경로:

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

예로 *기존의 라우팅은*합니다. 이 스타일 이라고 *기존의 라우팅은* 을 설정 하기 때문에 *규칙* URL 경로 대 한:

* 컨트롤러 이름에 매핑됩니다 첫 번째 경로 세그먼트

* 두 번째 작업 이름에 매핑됩니다.

* 세 번째 세그먼트 사용 되는 선택적 `id` 모델 요소에 매핑하는 데 사용

이 사용 하 여 `default` 경로, URL path `/Products/List` 매핑되는 `ProductsController.List` 동작을 및 `/Blog/Article/17` 에 매핑됩니다 `BlogController.Article`합니다. 이 매핑은 컨트롤러 및 작업 이름을 기준 **만** 기반 하지 않는 네임 스페이스, 소스 파일 위치 또는 메서드 매개 변수 및 합니다.

> [!TIP]
> 기본 경로와 기존 라우팅을 사용 하 여 정의한 각 작업에 대 한 새 URL 패턴을 제공 하지 않고 응용 프로그램을 신속 하 게 만들 수 있습니다. CRUD 스타일 동작이 포함 된 응용 프로그램에 대 한 컨트롤러를 통해 Url에 대 한 일관성 있는 유용 코드를 단순화 하 고 예측 가능성이 더욱 뛰어난 UI를 확인 합니다.

> [!WARNING]
> `id` 작업을 수행할 때 URL의 일부분으로 제공 하는 ID 않고 실행할 수 있는 즉 경로 템플릿에 의해 선택적으로 정의 됩니다. 일반적으로 어떤 동작이 발생 하는 경우 `id` URL에서 생략로 설정 됩니다 것은 `0` 모델 바인딩에서 그 엔터티가 일치 하는 데이터베이스에 나타나게 됩니다 결과로 `id == 0`합니다. 특성 라우팅을 확인만 하 고 일부 동작에 필요한 ID에 대 한 세분화 된 제어를 제공할 수 있습니다. 선택적 매개 변수는 설명서에는 일반적으로 같은 `id` 공백 문자가 올바른 사용법에 나타날 가능성이 있는 경우.

## <a name="multiple-routes"></a>여러 경로

내 여러 경로 추가할 수 `UseMvc` 에 대 한 더 많은 호출을 추가 하 여 `MapRoute`합니다. 이렇게 하면 여러 규칙을 정의 하거나와 같은 특정 작업을 지정 된 규칙에 따른 경로를 추가할 수 있습니다.

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
            defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

`blog` 경로 여기는 *전용된 규칙에 따른 경로*,이 규칙에 따른 라우팅 시스템을 사용 하지만 특정 작업의 전용를 의미 합니다. 이후 `controller` 및 `action` 매개 변수로 경로 템플릿을에 나타나지 않습니다, 기본값을 하나만 사용할 수 있습니다 및 따라서이 경로 작업에 매핑됩니다 항상 `BlogController.Article`합니다.

경로 컬렉션에 대 한 경로 정렬 하 고 추가 되는 순서에서 처리 됩니다. 이 예제에는 `blog` 하기 전에 경로 시도 합니다는 `default` 경로입니다.

> [!NOTE]
> *규칙에 따른 경로 전용* 종종 같은 범용 경로 매개 변수를 사용 하 여 `{*article}` URL 경로의 나머지 부분 캡처를 합니다. 이 수 없게 경로 '너무 greedy' 다른 경로 일치 하도록 하는 Url 일치 하는 것을 의미 합니다. 이 문제를 해결 하려면 경로 테이블의 뒷부분에 나오는 'greedy' 경로 넣습니다.

### <a name="fallback"></a>대체 (fallback)

요청 처리의 일부로, MVC 응용 프로그램의 컨트롤러 및 작업을 찾을 경로 값을 사용할 수 있는지 확인 합니다. 경로 값의 동작에 일치 하지 않는 경우 경로 일치 하는 항목으로 간주 되지 않으므로 다음 및 다음 경로 시도 합니다. 이 라고 *대체*, 규칙에 따른 경로 겹치는 경우 단순화 하기 위한 것이 고 합니다.

### <a name="disambiguating-actions"></a>작업을 명확히 구분

두 가지 동작 라우팅을 통해 일치 MVC 명확 하 '가장' 후보를 선택 합니다. 그렇지 않으면 예외를 throw 하 게 해야 합니다. 예:

```csharp
public class ProductsController : Controller
{
   public IActionResult Edit(int id) { ... }

   [HttpPost]
   public IActionResult Edit(int id, Product product) { ... }
}
```

일치 하는 URL 경로 두 가지 동작을 정의 하는이 컨트롤러 `/Products/Edit/17` 및 데이터 경로 `{ controller = Products, action = Edit, id = 17 }`합니다. 이 MVC 컨트롤러에 대 한 일반적인 패턴은 여기서 `Edit(int)` 제품을 편집할 수 있는 폼을 표시 및 `Edit(int, Product)` 게시 된 폼을 처리 합니다. MVC 선택 해야 할 것이 가능 하 게 하려면 `Edit(int, Product)` 경우 요청은 HTTP `POST` 및 `Edit(int)` HTTP 동사 80 이외 인 경우.

`HttpPostAttribute` ( `[HttpPost]` )는의 구현 `IActionConstraint` HTTP 동사가 때 선택할 동작 수만 `POST`합니다. 존재는 `IActionConstraint` 만듭니다는 `Edit(int, Product)` '더' 보다 일치 `Edit(int)`하므로 `Edit(int, Product)` 먼저 시도 됩니다.

사용자 지정 작성 하기만 됩니다 `IActionConstraint` 같은 특성의 역할을 이해 하는 것이 중요 하지만 특별 한 시나리오의 구현을 `HttpPostAttribute` -다른 HTTP 동사에 유사한 속성이 정의 됩니다. 기존 라우팅의 일부인 경우 동일한 작업 이름을 사용 하는 작업에 대 한 일반적 이기는 `show form -> submit form` 워크플로 합니다. 이 패턴에서 편리 하 게 검토 하 고 나면 더 분명 하 게 될는 [이해 IActionConstraint](#understanding-iactionconstraint) 섹션.

일치 하는 여러 경로가 및 MVC '최적의' 경로 찾을 수 없습니다, 예외를 throw 한 `AmbiguousActionException`합니다.

<a name=routing-route-name-ref-label></a>

### <a name="route-names"></a>경로 이름

문자열 `"blog"` 및 `"default"` 다음 예제에는 경로 이름:


```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
               defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

경로 이름은 URL 생성을 위한 명명 된 경로 사용할 수 있도록 논리 이름 경로 지정 합니다. 이 복잡 한 URL 생성을 만들 수의 경로 순서 지정 하는 경우 URL을 만들 크게 간소화 합니다. 경로 이름은 응용 프로그램 전체에서 고유 해야 합니다.

경로 이름은 URL 일치 또는; 요청 처리에 영향을 미치지 않습니다 URL 생성에만 사용 됩니다. [라우팅](xref:fundamentals/routing) 에 대 한 상세 정보가 MVC 관련 도우미에서 URL 생성을 포함 하 여 URL 생성 합니다.

<a name=attribute-routing-ref-label></a>

## <a name="attribute-routing"></a>특성 라우팅

특성 라우팅을 작업 경로의 서식 파일에 직접 매핑할 특성 집합을 사용 합니다. 다음 예에서 `app.UseMvc();` 에 사용 되는 `Configure` 메서드와 경로가 없는 전달 됩니다. `HomeController` 기본 경로 유사한 Url 집합과 일치 됩니다 `{controller=Home}/{action=Index}/{id?}` 과 일치 합니다.

```csharp
public class HomeController : Controller
{
   [Route("")]
   [Route("Home")]
   [Route("Home/Index")]
   public IActionResult Index()
   {
      return View();
   }
   [Route("Home/About")]
   public IActionResult About()
   {
      return View();
   }
   [Route("Home/Contact")]
   public IActionResult Contact()
   {
      return View();
   }
}
```

`HomeController.Index()` 된 URL 경로 대 한 작업을 실행할지 `/`, `/Home`, 또는 `/Home/Index`합니다.

> [!NOTE]
> 이 예제에서는 특성 라우팅 및 라우팅 규칙에 따른 주요 프로그래밍 차이점을 강조 표시합니다. 추가 정보를 입력 한 경로;를 지정 하려면 특성 라우팅이 필요 기존의 기본 경로 경로 보다 간결 하 게 처리합니다. 그러나 특성 라우팅을 허용 (및 필요)는 경로 템플릿을 각 작업에 적용 되는 정확 하 게 제어 합니다.

라우팅 컨트롤러 이름 및 작업 이름 특성으로 재생 **없는** 역할 작업 선택 합니다. 이 예에서는 앞의 예제와 동일한 Url과 일치 합니다.

```csharp
public class MyDemoController : Controller
{
   [Route("")]
   [Route("Home")]
   [Route("Home/Index")]
   public IActionResult MyIndex()
   {
      return View("Index");
   }
   [Route("Home/About")]
   public IActionResult MyAbout()
   {
      return View("About");
   }
   [Route("Home/Contact")]
   public IActionResult MyContact()
   {
      return View("Contact");
   }
}
```

> [!NOTE]
> 경로 템플릿을 위의 대 한 경로 매개 변수를 정의 하지 않은 `action`, `area`, 및 `controller`합니다. 실제로 이러한 경로 매개 변수 특성 경로에 허용 되지 않습니다. 경로 템플릿을 액션에 연결할 이미 있으므로 내 맞지 URL에서 작업 이름을 구문 분석할 수 있습니다.

## <a name="attribute-routing-with-httpverb-attributes"></a>Http [동사] 특성으로 라우팅 특성

특성 라우팅을 수행할 수도의 사용은 `Http[Verb]` 와 같은 특성 `HttpPostAttribute`합니다. 이러한 특성의 모든 경로 템플릿을 사용할 수 있습니다. 이 예에서는 동일한 경로 템플릿을 일치 하는 두 가지 동작을 보여 줍니다.

```csharp
[HttpGet("/products")]
public IActionResult ListProducts()
{
   // ...
}

[HttpPost("/products")]
public IActionResult CreateProduct(...)
{
   // ...
}
```

과 같은 URL 경로 대 한 `/products` 는 `ProductsApi.ListProducts` HTTP 동사가 때 동작이 실행 됩니다 `GET` 및 `ProductsApi.CreateProduct` HTTP 동사가 때 실행할 `POST`합니다. 특성을 먼저 라우팅 경로 특성에 정의 된 경로 템플릿의 집합에 대해 URL과 일치 합니다. 경로 템플릿을 일치 하지 않게, `IActionConstraint` 제한은 실행할 수 있는 작업 확인에 적용 됩니다.

> [!TIP]
> REST API를 작성할 때 경우는 거의 사용 하려는 됩니다 `[Route(...)]` 동작 메서드의 합니다. 보다 구체적인을 사용 하는 것이 좋습니다 `Http*Verb*Attributes` API에서 지 원하는 작업에 대 한 정확 하 게 합니다. REST Api의 클라이언트는 어떤 경로 및 HTTP 동사를 매핑할 특정 논리 작업에 알아야 예상 됩니다.

특정 작업에 있는 특성 경로 적용 됨, 이후 경로 템플릿 정의의 일환으로 필요한 매개 변수를 확인 하기 쉽습니다. 이 예제에서는 `id` 는 URL 경로 포함 되어야 합니다.

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

`ProductsApi.GetProduct(int)` 과 같은 URL 경로 대 한 작업을 실행할지 `/products/3` 과 같은 URL 경로 대해서가 아니라 `/products`합니다. 참조 [라우팅](../../fundamentals/routing.md) 에 대 한 전체 설명은 경로 템플릿 및 관련된 옵션입니다.

## <a name="route-name"></a>경로 이름

다음 코드 정의 *경로 이름* 의 `Products_List`:

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

특정 경로에 따라 URL을 생성 하 경로 이름은 사용할 수 있습니다. 경로 이름은 URL 일치 라우팅의 동작에 영향을 미치지 되며 URL 생성에만 사용 됩니다. 경로 이름은 응용 프로그램 전체에서 고유 해야 합니다.

> [!NOTE]
> 이와 반대로 기존의 *기본 경로*, 정의 하는 `id` 매개 변수를 선택적으로 (`{id?}`). Api를 정확 하 게 지정 하는이 기능에 허용 하는 등의 이점도 `/products` 및 `/products/5` 를 다른 동작에 디스패치할 수 있습니다.

<a name=routing-combining-ref-label></a>

### <a name="combining-routes"></a>조합 경로

특성 라우팅을 덜 반복적인를 하려면 컨트롤러에서 경로 특성 경로 특성에는 개별 작업와 결합 됩니다. 컨트롤러에 정의 된 경로 템플릿은 모든 작업에 대 한 경로 템플릿에 앞에 추가 됩니다. 컨트롤러에서 경로 특성을 배치 하면 **모든** 컨트롤러의 동작 특성 라우팅을 사용 합니다.

```csharp
[Route("products")]
public class ProductsApiController : Controller
{
   [HttpGet]
   public IActionResult ListProducts() { ... }

   [HttpGet("{id}")]
   public ActionResult GetProduct(int id) { ... }
}
```

이 예에서 URL 경로 `/products` 일치 시킬 수 `ProductsApi.ListProducts`, 및 URL 경로 `/products/5` 맞는지 `ProductsApi.GetProduct(int)`합니다. HTTP만 일치 두이 작업은 `GET` 으로 데코레이팅되기 때문에 `HttpGetAttribute`합니다.

로 시작 하는 작업에 적용할 템플릿 경로 `/` 컨트롤러에 적용 하는 경로 템플릿을와 가져올 결합 되지 않습니다. 이 예제와 비슷한 URL 경로 집합과 일치는 *기본 경로*합니다.

```csharp
[Route("Home")]
public class HomeController : Controller
{
    [Route("")]      // Combines to define the route template "Home"
    [Route("Index")] // Combines to define the route template "Home/Index"
    [Route("/")]     // Does not combine, defines the route template ""
    public IActionResult Index()
    {
        ViewData["Message"] = "Home index";
        var url = Url.Action("Index", "Home");
        ViewData["Message"] = "Home index" + "var url = Url.Action; =  " + url;
        return View();
    }

    [Route("About")] // Combines to define the route template "Home/About"
    public IActionResult About()
    {
        return View();
    }   
}
```

<a name=routing-ordering-ref-label></a>

### <a name="ordering-attribute-routes"></a>특성 경로 순서 지정

정의 된 순서로 실행 되는 규칙에 따른 경로 달리 특성 라우팅을 트리를 작성 및 동시에 모든 경로 일치 합니다. 이 역할-에 이상적인 정렬이; 배치 되는 경로 항목 가장 구체적인 경로는 보다 일반적인 경로 보다 실행 기회를 해야 합니다.

예를 들어 경로 같은 `blog/search/{topic}` 경로 같은 보다 구체적인 `blog/{*article}`합니다. 논리적으로 말해는 `blog/search/{topic}` 경로 '실행' 먼저 기본적으로 붙여 구분이 가능한만 순서 지정 하기 때문에 있습니다. 규칙에 따른 라우팅을 사용 하 여, 개발자는 경로 원하는 순서로 배치할 담당 합니다.

특성 경로 순서를 구성할 수를 사용 하 여 `Order` 제공 프레임 워크 경로 특성의 모든 속성. 경로 오름차순 따라 처리 되는 일종의 `Order` 속성입니다. 기본 순서는 `0`합니다. 사용 하 여 경로 설정 `Order = -1` 경로 순서를 설정 하지 않은 전에 실행 됩니다. 사용 하 여 경로 설정 `Order = 1` 기본 경로 순서를 지정한 후 실행 됩니다.

> [!TIP]
> 에 따라 방지 `Order`합니다. URL-스페이스를 올바르게 라우팅할 명시적 순서 값이 필요한 경우 됩니다 클라이언트 에서도 혼동 가능성이 높습니다. 일반적 특성 라우팅을 URL 일치 하는 올바른 경로 선택 합니다. URL 생성에 사용 되는 기본 순서 작동 하지 않는 경우 사용 하 여 경로 이름 재정의 적용할 때 보다 더 간단 일반적으로 `Order` 속성입니다.

<a name=routing-token-replacement-templates-ref-label></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a>경로 템플릿에서 토큰을 바꾸지 ([컨트롤러] [action] [지역])

편의 위해 특성 경로 지원 *토큰을 바꾸지* 정사각형 중괄호에는 토큰을 포함 하 여 (`[`, `]`). 토큰 `[action]`, `[area]`, 및 `[controller]` 작업 이름, 영역 이름 및 경로가 정의 된 작업에서 컨트롤러 이름을 값으로 바뀝니다. 이 예에서는 설명에 설명 된 대로 작업 URL 경로 찾을 수 있습니다.

[!code-csharp[Main](routing/sample/main/Controllers/ProductsController.cs?range=7-11,13-17,20-22)]

토큰 교체 특성 경로 빌드 과정의 마지막 단계로 발생 합니다. 위의 예에서는 다음 코드와 동일 하 게 동작 합니다.

[!code-csharp[Main](routing/sample/main/Controllers/ProductsController2.cs?range=7-11,13-17,20-22)]

특성 경로 상속와 함께 사용할 수도 있습니다. 이 기능은 토큰 교체를 함께 특히 유용 합니다.

```csharp
[Route("api/[controller]")]
public abstract class MyBaseController : Controller { ... }

public class ProductsController : MyBaseController
{
   [HttpGet] // Matches '/api/Products'
   public IActionResult List() { ... }

   [HttpPost("{id}")] // Matches '/api/Products/{id}'
   public IActionResult Edit(int id) { ... }
}
```

토큰 교체 특성 경로에 정의 된 경로 이름에도 적용 됩니다. `[Route("[controller]/[action]", Name="[controller]_[action]")]`각 작업에 대 한 고유한 경로 이름이 생성 됩니다.

리터럴 토큰 대체 구분 기호에 맞게 `[` 또는 `]`, 문자를 반복 하 여 이스케이프 (`[[` 또는 `]]`).

<a name=routing-multiple-routes-ref-label></a>

### <a name="multiple-routes"></a>여러 경로

동일한 작업에 도달 하는 여러 경로 정의 하는 라우팅 지원 특성입니다. 가장 일반적인 사용의 동작을 모방 하는 것은 *기본 규칙에 따른 경로* 다음 예제와 같이:

```csharp
[Route("[controller]")]
public class ProductsController : Controller
{
   [Route("")]     // Matches 'Products'
   [Route("Index")] // Matches 'Products/Index'
   public IActionResult Index()
}
```

다중 경로 특성은 컨트롤러에 배치 하는 각 작업 메서드에 대 한 경로 특성와 결합 합니다. 각 의미 합니다.

```csharp
[Route("Store")]
[Route("[controller]")]
public class ProductsController : Controller
{
   [HttpPost("Buy")]     // Matches 'Products/Buy' and 'Store/Buy'
   [HttpPost("Checkout")] // Matches 'Products/Checkout' and 'Store/Checkout'
   public IActionResult Buy()
}
```

경우 다중 경로 특성 (구현 하는 `IActionConstraint`) 각 작업 제약 조건 정의 된 특성에서 경로 템플릿을 사용 하 여 결합 한 다음 동작을에 배치 됩니다.

```csharp
[Route("api/[controller]")]
public class ProductsController : Controller
{
   [HttpPut("Buy")]      // Matches PUT 'api/Products/Buy'
   [HttpPost("Checkout")] // Matches POST 'api/Products/Checkout'
   public IActionResult Buy()
}
```

> [!TIP]
> 작업에서 여러 경로 사용 하 여 강력한 보일 수를 간단 하 고 잘 정의 된 응용 프로그램의 URL 공간을 유지 하는 것이 좋습니다. 예를 들어 기존 클라이언트를 지원 하기 위해 필요한 경우에 작업에서 여러 경로 사용 합니다.

<a name=routing-attr-options></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a>특성 경로 선택적 매개 변수, 기본값 및 제약 조건 지정

특성 경로는 선택적 매개 변수, 기본값 및 제약 조건을 지정 하려면 기본 경로와 같은 인라인 구문을 지원 합니다.

```csharp
[HttpPost("product/{id:int}")]
public IActionResult ShowProduct(int id)
{
   // ...
}
```

참조 [경로 템플릿 참조](../../fundamentals/routing.md#route-template-reference) 경로 템플릿 구문에 대 한 자세한 설명은 합니다.

<a name=routing-cust-rt-attr-irt-ref-label></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a>사용 하 여 사용자 지정 경로 특성`IRouteTemplateProvider`

프레임 워크에서 제공 하는 경로 특성의 모든 ( `[Route(...)]`, `[HttpGet(...)]` 등) 구현에서 `IRouteTemplateProvider` 인터페이스입니다. 앱을 시작 하 고 구현 하는 것을 사용 하 여 MVC 동작 메서드 및 컨트롤러 클래스에서 특성을 찾고 `IRouteTemplateProvider` 경로의 초기 집합을 빌드할 수 있습니다.

구현할 수 있습니다 `IRouteTemplateProvider` 직접 경로 특성을 정의할 수 있습니다. 각 `IRouteTemplateProvider` 순서와 이름 사용자 지정 경로 템플릿 사용 하 여 단일 경로 정의할 수 있습니다.

```csharp
public class MyApiControllerAttribute : Attribute, IRouteTemplateProvider
{
   public string Template => "api/[controller]";

   public int? Order { get; set; }

   public string Name { get; set; }
}
```

위의 예제에서이 특성을 자동으로 설정 된 `Template` 를 `"api/[controller]"` 때 `[MyApiController]` 적용 됩니다.

<a name=routing-app-model-ref-label></a>

### <a name="using-application-model-to-customize-attribute-routes"></a>응용 프로그램 모델을 사용 하 여 특성 경로 사용자 지정 하려면

*응용 프로그램 모델* 은 시작 시 모든 라우팅 및 작업을 수행할 때 실행 MVC에서 사용 하는 메타 데이터를 사용 하 여 만든 개체 모델입니다. *응용 프로그램 모델* 경로 특성에서 수집 되는 데이터를 모두 포함 (통해 `IRouteTemplateProvider`). 작성할 수 있습니다 *규칙* 라우팅 동작 하는 방법을 사용자 지정 하려면 시작 시 응용 프로그램 모델을 수정할 수 있습니다. 이 섹션에서는 응용 프로그램 모델을 사용 하 여 라우팅 사용자 지정의 간단한 예를 보여 줍니다.

[!code-csharp[Main](routing/sample/main/NamespaceRoutingConvention.cs)]

<a name=routing-mixed-ref-label></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a>라우팅 혼합: 라우팅 vs 기존의 라우팅은 특성

MVC 응용 프로그램에 기존의 라우팅은 및 특성 라우팅을 사용 하는 혼합할 수 있습니다. 일반적으로 브라우저에 대 한 HTML 페이지를 처리 하는 컨트롤러에 대 한 규칙에 따른 경로 사용 하 고 REST Api를 제공 하는 컨트롤러에 대 한 라우팅 특성은 합니다.

작업은 일반적으로 라우팅됩니다 하거나 또는 특성 라우팅됩니다. 컨트롤러 또는 작업에 대 한 경로 배치를 사용 하면 특성 라우팅됩니다. 기본 경로 반대로 통해 특성 경로 정의 하는 동작을 연결할 수 없습니다. **모든** 컨트롤러에서 경로 특성 라우팅된 컨트롤러 특성의 모든 작업을 사용 합니다.

> [!NOTE]
> URL 경로 템플릿과 일치 한 후에 적용 하는 프로세스는 두 가지 유형의 라우팅 시스템을 구별 합니다. 기본 라우팅 경로 값을 일치 하는 모든 규칙에 따른 라우트된 동작의 조회 테이블에서 작업 및 컨트롤러 선택 사용 됩니다. 특성 라우팅을에서 각 템플릿에 이미 연결 되어 작업을 하 고 조회가 없습니다. 더 이상 필요 합니다.

<a name=routing-url-gen-ref-label></a>

## <a name="url-generation"></a>URL 생성

MVC 응용 프로그램 동작에 대 한 URL 링크를 생성 하 라우팅의 URL 생성 기능을 사용할 수 있습니다. Url을 생성할 보다 강력 하 고 유지 관리 가능한 코드를 작성 하는 하드 코딩 Url을 제거 합니다. 이 섹션 MVC에서 제공 하는 URL 생성 기능을 중점적으로 다루며 URL 생성 작동 하는 방법에 대 한 기본 사항 설명만 합니다. 참조 [라우팅](../../fundamentals/routing.md) 에 대 한 자세한 설명은 URL 생성 합니다.

`IUrlHelper` 인터페이스는 MVC 및 URL 생성에 대 한 라우팅 사이의 인프라의 기본 부분입니다. 인스턴스를 찾을 수 `IUrlHelper` 를 통해 사용할 수는 `Url` 컨트롤러, 뷰 및 구성 요소 보기에서 속성입니다.

이 예제는 `IUrlHelper` 인터페이스를 통해 사용는 `Controller.Url` 다른 작업에 대 한 URL을 생성 하는 속성입니다.

[!code-csharp[Main](routing/sample/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

응용 프로그램에는 규칙에 따른 기본 사용 하는 경우를 라우팅할의 값은 `url` 변수가 URL 경로 문자열 됩니다 `/UrlGeneration/Destination`합니다. 이 URL 경로에 전달 된 값이 포함 된 현재 요청 (앰비언트 값)의 경로 값을 결합 하 여 라우팅에 의해 만들어집니다 `Url.Action` 경로 서식 파일에 해당 값을 대체 합니다.

```
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

각 경로 매개 변수를 경로 템플릿에 일치 하는 이름 값과 앰비언트 값을 사용 하 여 대체 값을 있습니다. 경로 매개 변수는 값이 없는 경우 선택 사항이 기는 건너뛸 수 또는 하나, 된 경우에 기본값을 사용할 수 (의 경우 마찬가지로 `id` 이 예에서). URL 생성에 필요한 경로 매개 변수가 해당 값이 없는 경우 실패 합니다. 경로 대 한 URL 생성 실패 하면 다음 경로 모든 경로 시도할 때까지 일치 하는 시도 됩니다.

예 `Url.Action` 위에 개념은 다르지만 기존의 라우팅은 하지만 URL 생성 작동 하는 비슷한 특성 라우팅을 가정 합니다. 규칙에 따른 라우팅을 사용 하 여 경로 값 하는 데는 서식 파일 및 경로 값에 대 한 확장 `controller` 및 `action` 일반적으로 라우팅에 의해 일치 되는 Url을 준수 하므로이 작업이 해당 템플릿에-나타날는 *규칙*. 에 대 한 값 경로 특성 라우팅에 `controller` 및 `action` 대신 사용 하는 서식 파일을 조회 하는 템플릿-에 나타날 수 없습니다.

이 예제에서는 특성 라우팅을 사용합니다.

[!code-csharp[Main](routing/sample/main/StartupUseMvc.cs?name=snippet_1)]

[!code-csharp[Main](routing/sample/main/Controllers/UrlGenerationControllerAttr.cs?name=snippet_1)]

MVC 라우팅 특성의 모든 동작의 조회 테이블을 작성 및 일치시킬지는 `controller` 및 `action` 값을 URL 생성에 사용할 경로 템플릿을 선택 합니다. 위의 샘플에서 `custom/url/to/destination` 생성 됩니다.

### <a name="generating-urls-by-action-name"></a>작업 이름으로 Url을 생성합니다.

`Url.Action` (`IUrlHelper` . `Action`)과 관련 된 모든 컨트롤러 이름과 작업 이름을 지정 하 여 연결할 무엇을 지정 하려면 해당 아이디어를 기반으로 모든 오버 로드 합니다.

> [!NOTE]
> 사용 하는 경우 `Url.Action`, 현재 경로 대 한 값 `controller` 및 `action` 있습니다-의 값에 대해 지정 된 `controller` 및 `action` 둘 다의 일부인 *앰비언트 값* **및** *값*합니다. 메서드가 `Url.Action`, 항상의 현재 값을 사용 하 여 `action` 및 `controller` 현재 작업에 라우트하는 URL 경로 생성 합니다.

라우팅 URL을 생성 하는 경우를 지정 하지 않은 정보를 입력 앰비언트 값에서 값을 사용 하려고 합니다. 와 같은 경로 사용 하 여 `{a}/{b}/{c}/{d}` 및 앰비언트 값 `{ a = Alice, b = Bob, c = Carol, d = David }`, 충분 한 정보가 추가 값이 없는 URL을 생성 하는 바가-모든 경로 매개 변수는 값입니다. 값을 추가한 경우 `{ d = Donovan }`, 값 `{ d = David }` 는 무시 하 고 생성 된 URL 경로 `Alice/Bob/Carol/Donovan`합니다.

> [!WARNING]
> URL 경로 계층적입니다. 위의 예에서에서 값 추가 `{ c = Cheryl }`, 두 값 모두 `{ c = Carol, d = David }` 가 무시 됩니다. 이 경우에 없는 값에 대 한 `d` 및 URL 생성에 실패 합니다. 원하는 값을 지정 해야 `c` 및 `d`합니다.  기본 경로와이 문제가 발생 하는 데 하려는 경우가 있습니다 (`{controller}/{action}/{id?}`)-이 동작으로 실제로 거의 하지만 `Url.Action` 항상 명시적으로 지정 됩니다는 `controller` 및 `action` 값입니다.

오버 로드는 더 긴 `Url.Action` 추가 수행할 수도 *경로 값* 아닌 다른 경로 매개 변수의 값을 제공 하는 개체 `controller` 및 `action`합니다. 표시 됩니다. 가장 일반적으로 함께 사용할 `id` 같은 `Url.Action("Buy", "Products", new { id = 17 })`합니다. 일반적으로는 *경로 값* 개체는 일반적으로 익명 형식의 개체 하지만 수도 있습니다는 `IDictionary<>` 또는 *일반 이전.NET 개체*합니다. 경로 매개 변수에 일치 하지 않는 모든 추가 경로 값은 쿼리 문자열에 저장 됩니다.

[!code-csharp[Main](routing/sample/main/Controllers/TestController.cs)]

> [!TIP]
> 절대 URL을 만들려면 사용을 허용 하는 오버 로드는 `protocol`:`Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`

<a name=routing-gen-urls-route-ref-label></a>

### <a name="generating-urls-by-route"></a>Url 경로 의해 생성

위의 코드 설명 컨트롤러 및 작업 이름 전달 하 여 URL을 생성 합니다. `IUrlHelper`또한 제공 된 `Url.RouteUrl` 메서드 패밀리를 합니다. 이러한 메서드는 비슷합니다 `Url.Action`, 하지만의 현재 값을 복사 하지 않는 `action` 및 `controller` 경로 값을 합니다. 일반적으로 URL을 생성 하는 특정 경로 사용 하는 경로 이름을 지정 하는 가장 일반적인 사용 *없이* 컨트롤러나 작업 이름을 지정 합니다.

[!code-csharp[Main](routing/sample/main/Controllers/UrlGenerationControllerRouting.cs?name=snippet_1)]

<a name=routing-gen-urls-html-ref-label></a>

### <a name="generating-urls-in-html"></a>HTML에 Url을 생성합니다.

`IHtmlHelper`제공 된 `HtmlHelper` 메서드 `Html.BeginForm` 및 `Html.ActionLink` 생성 하 `<form>` 및 `<a>` 요소 각각. 이러한 메서드를 사용 하 여는 `Url.Action` 는 URL을 생성 하는 메서드 비슷한 인수를 수락 합니다. `Url.RouteUrl` 보조 도구에 대 한 `HtmlHelper` 는 `Html.BeginRouteForm` 및 `Html.RouteLink` 충족 하는 유사한 기능을 합니다.

TagHelpers Url을 통해 생성 된 `form` TagHelper 및 `<a>` TagHelper 합니다. 둘 다 사용 하 여 `IUrlHelper` 의 구현에 대 한 합니다. 참조 [양식 작업](../views/working-with-forms.md) 자세한 정보에 대 한 합니다.

뷰 내에서 `IUrlHelper` 를 통해 사용할 수는 `Url` 위의에서 포함 하지 않는 모든 임시 URL 생성에 대 한 속성.

<a name=routing-gen-urls-action-ref-label></a>

### <a name="generating-urls-in-action-results"></a>작업 결과에 URL을 생성합니다.

위 예제를 사용 하 여 이루었습니다 `IUrlHelper` 는 컨트롤러의 가장 일반적인 사용 하는 작업 결과의 일부분으로 URL을 생성 하는 동안에 컨트롤러에 있습니다.

`ControllerBase` 및 `Controller` 기본 클래스는 다른 작업을 참조 하는 작업 결과 대 한 편의 메서드를 제공 합니다. 하나의 일반적인 사용법은 사용자 입력을 적용 하면 리디렉션됩니다.

```csharp
public Task<IActionResult> Edit(int id, Customer customer)
{
    if (ModelState.IsValid)
    {
        // Update DB with new details.
        return RedirectToAction("Index");
    }
}
```

작업 결과 팩터리 메서드는 메서드는 유사한 패턴에 따라 `IUrlHelper`합니다.

<a name=routing-dedicated-ref-label></a>

### <a name="special-case-for-dedicated-conventional-routes"></a>전용된 기본 경로 대 한 특별 한 경우

기존의 라우팅은 라고 하는 경로 정의의 특별 한 종류를 사용할 수는 *전용된 규칙에 따른 경로*합니다. 아래 예제에서는 경로 이름은 `blog` 는 전용된 규칙에 따른 경로입니다.

```csharp
app.UseMvc(routes =>
{
    routes.MapRoute("blog", "blog/{*article}",
        defaults: new { controller = "Blog", action = "Article" });
    routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

이러한 경로 정의 사용 하 여 `Url.Action("Index", "Home")` URL 경로가 생성 됩니다 `/` 와 `default` 이유 하지만 경로? 경로 값을 추측할 수 `{ controller = Home, action = Index }` 사용 될 수는 URL을 생성할 수 있을 만큼 `blog`, 하 고 결과 `/blog?action=Index&controller=Home`합니다.

규칙에 따른 전용된 경로 해당 경로 되지 않도록 하는 경로 매개 변수 없는 기본 값의 특별 한 동작에 의존 "너무 greedy"으로 URL 생성 합니다. 이 경우에 기본값은 `{ controller = Blog, action = Article }`, 않으며 `controller` 나 `action` 경로 매개 변수도 나타납니다. 라우팅 URL 생성을 수행할 때 제공 된 값 기본 값이 일치 해야 합니다. 사용 하 여 URL 생성 `blog` 실패 하 게 값 `{ controller = Home, action = Index }` 일치 하지 않습니다. `{ controller = Blog, action = Article }`합니다. 시도로 대체 다음 라우팅 `default`, 성공입니다.

<a name=routing-areas-ref-label></a>

## <a name="areas"></a>영역

[영역](areas.md) 는 별도 라우팅-네임 스페이스 (예: 컨트롤러 동작) 및 (views)에 대 한 폴더 구조 그룹으로 관련된 기능을 구성 하는 데 사용 되는 MVC 기능입니다. 영역을 사용 하면 같은 이름의 컨트롤러가 여러 개 있는에 응용 프로그램으로 서로 다른 *영역*합니다. 영역을 사용 하 여 다른 경로 매개 변수를 추가 하 여 라우팅 목적으로 계층 구조를 만듭니다 `area` 를 `controller` 및 `action`합니다. 라우팅와 상호 작용 방법 영역-이 섹션에 설명 합니다 참조 [영역](areas.md) 영역 보기와 사용 방법에 대 한 세부 정보에 대 한 합니다.

다음 예제에서는 구성 기본 규칙에 따른 경로 사용 하는 MVC 및 *영역 경로* 명명 된 영역에 대 한 `Blog`:

[!code-csharp[Main](routing/sample/AreasRouting/Startup.cs?name=snippet1)]

과 같은 URL 경로 일치 시킬 때 `/Manage/Users/AddUser`, 첫 번째 경로 경로 값이 생성 됩니다 `{ area = Blog, controller = Users, action = AddUser }`합니다. `area` 여에 대 한 기본값을 경로 값이 생성 `area`, 사실에서 만든 경로 `MapAreaRoute` 다음과 같습니다.

[!code-csharp[Main](routing/sample/AreasRouting/Startup.cs?name=snippet2)]

`MapAreaRoute`기본값 및 제약 조건을 사용 하 여 경로 만듭니다 `area` 이 경우 제공 된 영역 이름을 사용 하 여 `Blog`합니다. 기본 값을 입력 하면 경로 항상 생성 `{ area = Blog, ... }`, 제약 조건 값이 필요한 `{ area = Blog, ... }` URL 생성에 대 한 합니다.

> [!TIP]
> 기존의 라우팅은 순서가 중요 합니다. 일반적으로 경로 영역에 배치 해야 경로 테이블의 앞부분에 나오는 영역 없이 경로 보다 더 구체적인는 합니다.

위의 예제를 사용 하 여 경로 값 다음 동작과 일치 합니다.

[!code-csharp[Main](routing/sample/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

`AreaAttribute` 어떤 나타냅니다는 컨트롤러는 영역의 일환으로,이 컨트롤러 인지 테는 `Blog` 영역입니다. 없는 컨트롤러는 `[Area]` 영역으로의 구성원이 아닌 특성과 됩니다 **하지** 일치 하는 경우는 `area` 라우팅하여 경로 값을 제공 합니다. 다음 예제에서는 나열 된 첫 번째 컨트롤러에 경로 값과 일치 수 `{ area = Blog, controller = Users, action = AddUser }`합니다.

[!code-csharp[Main](routing/sample/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[Main](routing/sample/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[Main](routing/sample/AreasRouting/Controllers/UsersController.cs)]

> [!NOTE]
> 완전성을 위해 각 컨트롤러의 네임 스페이스는 여기에 표시 하는-그렇지 않으면 컨트롤러 갖기 명명 충돌 및 컴파일러 오류가 발생 합니다. 클래스의 네임 스페이스에 MVC의 라우팅 효과가 없습니다.

처음 두 명의 컨트롤러 영역의 구성원이 며 해당 해당 영역 이름을 제공 하는 경우에 일치는 `area` 값 라우팅합니다. 세 번째 컨트롤러 모든 영역과 수 있는 유일한 일치에 대 한 값이 없는 경우의 구성원이 아닙니다. `area` 라우팅에 의해 제공 됩니다.

> [!NOTE]
> 일치를 기준으로 *값이 없는*, 없는 경우는 `area` 값은 동일 처럼 값에 대 한 `area` null 또는 빈 문자열입니다.

경로 대 한 값 영역 내에서 동작을 실행할 때는 `area` 을으로 사용할 수는 *앰비언트 값* URL 생성에 사용할 라우팅에 대 한 합니다. 즉, 기본적으로 영역 헤드 하도록 *스티커* 다음 샘플에 표시 된 대로 URL 생성에 대 한 합니다.

[!code-csharp[Main](routing/sample/AreasRouting/Startup.cs?name=snippet3)]

[!code-csharp[Main](routing/sample/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

<a name=iactionconstraint-ref-label></a>

## <a name="understanding-iactionconstraint"></a>IActionConstraint 이해

> [!NOTE]
> 이 섹션은 프레임 워크 내부 구조 및 MVC 실행 하는 작업을 선택 하는 방법에 심층적입니다. 일반적인 응용 프로그램에 사용자 지정 필요 하지는 않습니다.`IActionConstraint`

이미 사용 가능성이 `IActionConstraint` 인터페이스와 모르는 경우에 합니다. `[HttpGet]` 특성 및 유사한 항목과 `[Http-VERB]` 특성 구현 `IActionConstraint` 작업 메서드의 실행은 가급적 제한 하기 위해 합니다.

```csharp
public class ProductsController : Controller
{
    [HttpGet]
    public IActionResult Edit() { }

    public IActionResult Edit(...) { }
}
```

기본 규칙에 따른 경로 URL 경로 가정 `/Products/Edit` 값을 생성 합니다 `{ controller = Products, action = Edit }`, 일치는 **둘 다** 여기에 표시 된 작업 중입니다. `IActionConstraint` 용어 것 말할 후보-하는 것은 두이 작업은 모두 일치 하는 경로 데이터입니다.

때는 `HttpGetAttribute` 실행 되 면 그에 해당 내용이 명시 *Edit()* 에 대 한 일치 하는 항목이 *가져오기* 및 다른 HTTP 동사에 대 한 일치 하지 않습니다. `Edit(...)` 작업 정의 된 모든 제약 조건이 없는 경우 및 되므로 모든 HTTP 동사를 일치 됩니다. 따라서 가정는 `POST` -만 `Edit(...)` 일치 합니다. 그러나 하지만 대 한는 `GET` 두 가지 동작 모두과 일치할 수 있습니다-작업과 `IActionConstraint` 항상 간주 *더 나은* 하지 않고 작업 보다 합니다. 따라서 때문에 `Edit()` 가 `[HttpGet]` 보다 구체적인으로 간주 하 고 두 가지 동작 수와 일치 하는 경우 선택 됩니다.

개념적으로 `IActionConstraint` 는 형태의 *오버 로드*, 하지만 동일한 URL 일치 하는 작업 간 오버은 동일한 이름 가진 메서드를 오버 로드를 대신 합니다. 또한 특성 라우팅을 사용 하 여 `IActionConstraint` 모두 고려 하 고 후보 다른 컨트롤러에서 작업에서 발생할 수 있습니다.

<a name=iactionconstraint-impl-ref-label></a>

### <a name="implementing-iactionconstraint"></a>IActionConstraint 구현

구현 하는 가장 간단한 방법은 `IActionConstraint` 에서 파생 된 클래스를 만드는 `System.Attribute` 작업 및 컨트롤러에 배치 합니다. MVC이 자동으로 검색 된 `IActionConstraint` 특성으로 적용 되는 합니다. 응용 프로그램 모델을 사용 하 여 제약 조건 적용 및 때문일 가장 융통성이 높은 방법은에 적용 되는 방식을 metaprogram 수 있으므로 합니다.

다음 예제는 제약 조건에 따라 작업을 선택는 *국가 코드* 에서 경로 데이터입니다. [GitHub에서 전체 샘플](https://github.com/aspnet/Entropy/blob/dev/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs)합니다.

```csharp
public class CountrySpecificAttribute : Attribute, IActionConstraint
{
    private readonly string _countryCode;

    public CountrySpecificAttribute(string countryCode)
    {
        _countryCode = countryCode;
    }

    public int Order
    {
        get
        {
            return 0;
        }
    }

    public bool Accept(ActionConstraintContext context)
    {
        return string.Equals(
            context.RouteContext.RouteData.Values["country"].ToString(),
            _countryCode,
            StringComparison.OrdinalIgnoreCase);
    }
}
```

구현 해야 하는 `Accept` 메서드와 'Order' 제약 조건에 대 한 실행 하려면 선택 합니다. 이 경우는 `Accept` 메서드가 반환 되 `true` 를 일치 하는 항목을 마우스 오른쪽 단추로 동작은 나타내는 때는 `country` 값 일치 항목을 라우팅합니다. 이 다른는 `RouteValueAttribute` 대체 특성을 사용 비에서 수행한 작업을 허용 하 합니다. 이 샘플을 보여 줍니다 정의 하는 경우는 `en-US` 작업 후 같은 국가 코드 `fr-FR` 없는 하는 보다 일반적인 컨트롤러도 대체 됩니다 `[CountrySpecific(...)]` 적용 합니다.

`Order` 속성 결정 *단계* 제약 조건의 일부입니다. 액션 제약 조건에 따라 그룹에서 실행 된 `Order`합니다. 예를 들어 프레임 워크의 모든 제공 된 HTTP 메서드 특성을 사용 하 여 동일한 `Order` 값 단계에서 실행할 수 있도록 합니다. 원하는 정책을 구현 하는 데 필요한 만큼 많은 단계를 포함할 수 있습니다.

> [!TIP]
> 에 대 한 값을 결정 하려면 `Order` HTTP 메서드보다 먼저 제약 조건 적용 해야 할지 여부에 대해 고려 합니다. 숫자가 낮을수록 먼저 실행 됩니다.
