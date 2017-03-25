---
title: "컨트롤러 작업에 대 한 라우팅을 | Microsoft 문서"
author: rick-anderson
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 26250a4d-bf62-4d45-8549-26801cf956e9
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/controllers/routing
translationtype: Machine Translation
ms.sourcegitcommit: 3725c8e12be1b6b1f51a219e5ae36d5a6c5ecac3
ms.openlocfilehash: e747cd49c9b11859b41d46ea0d34cff2f4a2e263
ms.lasthandoff: 03/23/2017

---
# <a name="routing-to-controller-actions"></a>컨트롤러 작업에 대 한 라우팅을

여 [Ryan Nowak](https://github.com/rynowak) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core MVC에 라우팅 사용 하 여 [미들웨어](../../fundamentals/middleware.md) 하 들어오는 요청의 Url과 일치 하 고 작업에 매핑합니다. 경로 시작 코드 또는 특성에 정의 됩니다. 경로 URL 경로 동작에 일치 해야 하는 방법을 설명 합니다. 응답에 전송 (링크)에 대 한 Url을 생성 하는 경로 사용도 됩니다.

이 문서에서는 MVC 및 라우팅, 어떻게 일반적인 MVC 응용 프로그램 만들기 간의 상호 작용을 설명 합니다. 라우팅 기능을 사용 합니다. 참조 [라우팅](../../fundamentals/routing.md) 고급 라우팅에 대 한 내용은 합니다.

## <a name="setting-up-routing-middleware"></a>미들웨어 라우팅 설정

사용자 *구성* 메서드 유사한 코드가 표시 될 수 있습니다.

<!-- literal_block {"ids": [], "names": [], "backrefs": [], "dupnames": [], "xml:space": "preserve", "classes": []} -->

```
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

에 대 한 호출 내부 `UseMvc`, `MapRoute` 으로 지칭 하는 단일 경로 만드는 데 사용 되는 `default` 경로입니다. 대부분의 MVC 응용 프로그램 경로 사용 하 여 유사한 템플릿을 사용 하 여는 `default` 경로입니다.

경로 템플릿 `"{controller=Home}/{action=Index}/{id?}"` 과 같은 URL 경로 일치 시킬 수 `/Products/Details/5` 경로 값을 추출 하 고 `{ controller = Products, action = Details, id = 5 }` 경로 토큰화 하 여 합니다. MVC는 컨트롤러를 찾으려고 시도 `ProductsController` 동작을 실행 하 고 `Details`:

<!-- literal_block {"ids": [], "names": [], "backrefs": [], "dupnames": [], "xml:space": "preserve", "classes": []} -->

```
public class ProductsController : Controller
{
   public IActionResult Details(int id) { ... }
}
```

이 예제에서는 모델 바인딩을 사용 하 여 값을 `id = 5` 설정 하는 `id` 매개 변수를 `5` 이 작업을 호출 하는 경우. 참조는 [모델 바인딩](../models/model-binding.md) 대 한 자세한 내용은 합니다.

사용 하 여 `default` 경로:

<!-- literal_block {"ids": [], "names": [], "backrefs": [], "dupnames": [], "xml:space": "preserve", "classes": []} -->

```
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

경로 템플릿:

* `{controller=Home}`정의 `Home` 기본적으로`controller`

* `{action=Index}`정의 `Index` 기본적으로`action`

* `{id?}`정의 `id` 옵션으로

기본 및 선택적 경로 매개 변수 필요가 없습니다 일치 하는 항목에 대 한 URL 경로에 있어야 합니다. 참조 [경로 템플릿 참조](../../fundamentals/routing.md#route-template-reference) 에 대 한 자세한 설명은 경로 템플릿 구문입니다.

`"{controller=Home}/{action=Index}/{id?}"`URL 경로 일치 시킬 수 `/` 경로 값이 생성 됩니다 및 `{ controller = Home, action = Index }`합니다. 에 대 한 값 `controller` 및 `action` 확인의 기본값을 사용 하 여 `id` URL 경로에 해당 하는 세그먼트 이므로 값을 생성 하지 않습니다. MVC는 이러한 경로 값을 사용 하 여 선택 하 여 `HomeController` 및 `Index` 작업:

<!-- literal_block {"ids": [], "names": [], "backrefs": [], "dupnames": [], "xml:space": "preserve", "classes": []} -->

```
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

이 컨트롤러 정 및 경로 템플릿을 사용 하는 `HomeController.Index` 다음 URL 경로 대 한 작업을 실행할 수는 있습니다.

* `/Home/Index/17`

* `/Home/Index`

* `/Home`

* `/`

편리한 방법 `UseMvcWithDefaultRoute`:

<!-- literal_block {"ids": [], "names": [], "backrefs": [], "dupnames": [], "xml:space": "preserve", "classes": []} -->

```
app.UseMvcWithDefaultRoute();
```

대체를 사용할 수 있습니다.

<!-- literal_block {"ids": [], "names": [], "backrefs": [], "dupnames": [], "xml:space": "preserve", "classes": []} -->

```
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

`UseMvc`및 `UseMvcWithDefaultRoute` 의 인스턴스를 추가 `RouterMiddleware` 미들웨어 파이프라인에 있습니다. MVC는 미들웨어와 직접 상호 작용 하지 않고 및 라우팅을 사용 하 여 요청을 처리 합니다. MVC의 인스턴스를 통해 경로에 연결 되어 `MvcRouteHandler`합니다. 내부 코드 `UseMvc` 다음과 유사 하 게 됩니다.

<!-- literal_block {"ids": [], "names": [], "backrefs": [], "dupnames": [], "xml:space": "preserve", "classes": []} -->

```
var routes = new RouteBuilder(app);

// Add connection to MVC, will be hooked up by calls to MapRoute.
routes.DefaultHandler = new MvcRouteHandler(...);

// Execute callback to register routes.
// routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");

// Create route collection and add the middleware.
app.UseRouter(routes.Build());
```

`UseMvc`모든 경로 직접 정의 하지 않는 자리 표시자에 대 한 경로 컬렉션에 추가 하는 것은 `attribute` 경로. 오버 로드 `UseMvc(Action<IRouteBuilder>)` 특성 라우팅도 지원 하 고 직접 경로 추가할 수 있습니다.  `UseMvc`특성 경로 대 한 자리 표시자를 추가 하는 모든 해당 변형 및-특성 라우팅 항상 사용 가능 구성 하는 방법에 관계 없이 `UseMvc`합니다. `UseMvcWithDefaultRoute`기본 경로 정의 하 고 특성 라우팅을 지원 합니다. [특성 라우팅](#attribute-routing-ref-label) 섹션 특성 라우팅에 대 한 자세한 정보를 포함 합니다.

<a name=routing-conventional-ref-label></a>

## <a name="conventional-routing"></a>기존 라우팅

`default` 경로:

<!-- literal_block {"ids": [], "names": [], "backrefs": [], "dupnames": [], "xml:space": "preserve", "classes": []} -->

```
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

한 예로 *기존의 라우팅은*합니다. 이 스타일 이라고 *기존의 라우팅은* 를 설정 하는 *규칙* URL 경로 대 한:

* 컨트롤러 이름으로 매핑하는 첫 번째 경로 세그먼트

* 두 번째 작업 이름에 매핑됩니다.

* 세 번째 세그먼트는 선택적에 사용 됩니다 `id` 모델 요소에 매핑하는 데 사용

이 사용 하 여 `default` 경로, URL path `/Products/List` 매핑되는 `ProductsController.List` 동작을 및 `/Blog/Article/17` 매핑됩니다 `BlogController.Article`합니다. 이 매핑은 컨트롤러와 작업 이름을 기반 **만** 및 네임 스페이스, 소스 파일 위치 또는 메서드 매개 변수를 따르지 않습니다.

> [!TIP]
> 기본 경로와 기본 라우팅을 사용 하 여 정의한 각 동작에 대해 새 URL 패턴을 제공 하지 않고도 응용 프로그램을 신속 하 게 만들 수 있습니다. CRUD 스타일 동작이 포함 된 응용 프로그램을 위해 컨트롤러에 걸쳐 Url에 대 한 일관성 있는 유용 코드를 단순화 하 고 예측 가능성이 더욱 뛰어난 UI를 확인 합니다.

> [!WARNING]
> `id` URL의 일부로 제공 된 ID가 없는 사용자의 작업을 실행할 수 있도록 의미를 경로 템플릿에 의해 선택적으로 정의 됩니다. 일반적으로 어떻게 됩니까 `id` URL에서 생략로 설정 됩니다 것은 `0` 모델 바인딩에서 일치 하는 데이터베이스에 없는 엔터티를 찾을 수는 결과적으로 `id == 0`합니다. 특성 라우팅 세분화 된 제어 확인만 하 고 일부 동작에 필요한 ID를 제공할 수 있습니다. 선택적 매개 변수는 설명서에는 일반적으로 같은 `id` 올바른 사용법에 나타날 가능성이 있는 경우.

## <a name="multiple-routes"></a>여러 경로

내 여러 경로 추가할 수 있습니다 `UseMvc` 에 더 많은 호출을 추가 하 여 `MapRoute`합니다. 이렇게 하면 여러 규칙을 정의 하거나와 같은 특정 작업을 전용 기본 경로를 추가할 수 있습니다.

<!-- literal_block {"ids": [], "names": [], "backrefs": [], "dupnames": [], "xml:space": "preserve", "classes": []} -->

```
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
            defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
}
```

`blog` 여기 경로 *전용된 기본 경로*, 기존의 라우팅 시스템을 사용 하 여 특정 작업의 전용는 의미 합니다. 이후 `controller` 및 `action` 매개 변수로 경로 템플릿에 나타나지, 기본 값을 하나만 사용할 수 있습니다 않으며 따라서이 경로 항상 작업에 매핑됩니다 `BlogController.Article`합니다.

경로 컬렉션에는 경로 정렬 및 추가 순서에서 처리 됩니다. 따라서이 예에서 `blog` 경로 시도 하기 전에 `default` 경로입니다.

> [!NOTE]
> *기본 경로 전용* 종종와 같은 포괄적인 경로 매개 변수를 사용 하 여 `{*article}` 를 url의 나머지 부분을 캡처합니다. 이 가능 경로 '너무 greedy' 다른 경로 일치 하도록 하는 Url 일치 하는 것을 의미 합니다. 이 문제를 해결 하는 경로 테이블의 뒷부분에 나오는 'greedy' 경로 넣습니다.

### <a name="fallback"></a>대체 (fallback)

요청 처리의 일부로, MVC 응용 프로그램에서 컨트롤러와 작업을 찾을 경로 값을 사용할 수 있는지 확인 합니다. 경로 값의 동작에 일치 하지 않는 경우 다음 경로가 일치 하는 간주 되지 않습니다 하 고 경로 시도 합니다. 이 이라고 *대체 (fallback)*, 기본 경로 겹치는 경우 단순화 하기 위해 제공 했습니다.

### <a name="disambiguating-actions"></a>동작을 명확히 구분

두 가지 동작 라우팅을 통해 일치 하는 경우 'b e 후보를 선택 합니다. 그렇지 않으면 예외를 throw 하 MVC 명확 해야 합니다. 예:

<!-- literal_block {"ids": [], "names": [], "backrefs": [], "dupnames": [], "xml:space": "preserve", "classes": []} -->

```
public class ProductsController : Controller
{
   public IActionResult Edit(int id) { ... }

   [HttpPost]
   public IActionResult Edit(int id, Product product) { ... }
}
```

일치 하는 URL 경로 두 가지 동작을 정의 하는이 컨트롤러 `/Products/Edit/17` 데이터를 라우팅할 `{ controller = Products, action = Edit, id = 17 }`합니다. 이 MVC 컨트롤러에 대 한 일반적인 패턴은 여기서 `Edit(int)` 제품을 편집 하는 폼을 보여 주는 및 `Edit(int, Product)` 게시 된 폼을 처리 합니다. 이렇게 하려면 MVC를 선택 해야 `Edit(int, Product)` 경우 요청은 HTTP `POST` 및 `Edit(int)` HTTP 동사 이외 인 경우.

`HttpPostAttribute` ( `[HttpPost]` )의 구현인 `IActionConstraint` HTTP 동사가 선택할 작업 수만 `POST`합니다. 존재는 `IActionConstraint` 를 사용 하면는 `Edit(int, Product)` '좋다고' 보다 일치 `Edit(int)`하므로 `Edit(int, Product)` 먼저 시도 됩니다.

사용자 지정 작성만 할 수 `IActionConstraint` 같은 특성의 역할을 이해 하는 것이 중요 하지만 전문화 된 시나리오에서 구현을 `HttpPostAttribute` -유사한 특성은 다른 HTTP 동사에 대해 정의 됩니다. 일반적으로 작업의 일부인 경우 동일한 작업 이름을 사용 하는 기존의 라우팅에 `show form -> submit form` 워크플로. 이 패턴에서 편리 하 게 더욱 명확해 집니다 검토 한 후의 [이해 IActionConstraint](#understanding-iactionconstraint) 섹션입니다.

일치 하는 여러 경로가 MVC 'b e 경로 찾을 수 없는 경우 예외를 throw 한 `AmbiguousActionException`합니다.

<a name=routing-route-name-ref-label></a>

### <a name="route-names"></a>경로 이름

문자열 `"blog"` 및 `"default"` 다음 예에는 경로 이름:

<!-- literal_block {"ids": [], "names": [], "backrefs": [], "dupnames": [], "xml:space": "preserve", "classes": []} -->

```
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
               defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

경로 이름은 URL 생성에 대 한 명명 된 경로 사용할 수 있도록 논리적 이름 경로 지정 합니다. 이 복잡 한 URL 생성을 개선할 수 있는 경로 순서 지정 하는 경우 URL을 만들 크게 간소화 합니다. 경로 이름에는 응용 프로그램 전체에서 고유 해야 합니다.

경로 이름은 아무런 영향이 일치 하는 URL을 요청 처리 URL 생성에 대해서만 사용 됩니다. [라우팅](../../fundamentals/routing.md) 에 대 한 상세 정보가 MVC 전용 도우미에서 URL 생성을 포함 하 여 URL 생성 합니다.

<a name=attribute-routing-ref-label></a>

## <a name="attribute-routing"></a>특성 라우팅

특성 라우팅 경로 서식 파일에 직접 작업을 매핑할 특성의 집합을 사용 합니다. 다음 예에서 `app.UseMvc();` 에 사용 되는 `Configure` 메서드와 경로가 없는 전달 됩니다. `HomeController` Url 기본 경로 유사한 집합 일치시킬지 `{controller=Home}/{action=Index}/{id?}` 과 일치 합니다.

<!-- literal_block {"ids": [], "names": [], "highlight_args": {}, "backrefs": [], "dupnames": [], "linenos": false, "classes": [], "xml:space": "preserve", "language": "c#"} -->

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

`HomeController.Index()` URL 경로 대 한 작업을 실행할 수는 `/`, `/Home`, 또는 `/Home/Index`합니다.

> [!NOTE]
> 이 예제에서는 특성 라우팅 및 기존의 라우팅은 간의 프로그래밍 차이점을 강조 표시합니다. 경로를 지정 하는 더 많은 입력이 필요한 특성 라우팅 기존의 기본 경로 경로 더 간결 하 게 처리합니다. 그러나 특성 라우팅 (있으며 필요)는 경로 템플릿 각 작업에 적용 하는 정밀 하 게 제어 합니다.

라우팅 컨트롤러 이름 및 작업 이름 특성으로 재생 **없는** 동작 선택 되어 있는 역할입니다. 이 예제에서는 이전 예제와 동일한 Url 일치 합니다.

<!-- literal_block {"ids": [], "names": [], "highlight_args": {}, "backrefs": [], "dupnames": [], "linenos": false, "classes": [], "xml:space": "preserve", "language": "c#"} -->

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
> 위의 경로 템플릿 경로 매개 변수를 정의 하지는 않습니다 `action`, `area`, 및 `controller`합니다. 사실 이러한 경로 매개 변수 특성 경로에서 허용 되지 않습니다. 경로 템플릿와 이미 연결 되어 작업을 하므로 맞지 않습니다 URL에서 작업 이름을 구문 분석할 수 있습니다.

작업도 가능 특성 라우팅의 사용은 `Http[Verb]` 와 같은 특성 `HttpPostAttribute`합니다. 이러한 특성의 모든 경로 템플릿을 사용할 수 있습니다. 이 예제에서는 동일한 경로 템플릿과 일치 하는 두 가지 작업을 보여 줍니다.

<!-- literal_block {"ids": [], "names": [], "highlight_args": {}, "backrefs": [], "dupnames": [], "linenos": false, "classes": [], "xml:space": "preserve", "language": "c#"} -->

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

과 같은 URL 경로 대 한 `/products` 는 `ProductsApi.ListProducts` HTTP 동사가 때 동작이 실행 됩니다 `GET` 및 `ProductsApi.CreateProduct` HTTP 동사가 때 실행 될 `POST`합니다. 특성 먼저 라우팅 경로 템플릿 경로 특성에 의해 정의 된 집합에 대 한 URL과 일치 합니다. 경로 템플릿 일치 `IActionConstraint` 제한은 실행할 수 있는 작업 확인에 적용 됩니다.

> [!TIP]
> REST API를 작성할 때 경우는 거의 사용 하려는 `[Route(...)]` 작업 방법에 있습니다. 더 구체적인을 사용 하는 것이 좋습니다 `Http*Verb*Attributes` API에서 지 원하는 작업에 대 한 정확 하 게 합니다. REST Api의 클라이언트는 어떤 경로 및 HTTP 동사를 매핑할 특정 논리 연산을 알아야 예상 됩니다.

특성 경로 특정 작업에 적용 되므로 경로 템플릿 정의의 일부로 필요한 매개 변수를 확인 하기 쉽습니다. 이 예제에서는 `id` url의 일부분으로 필요 합니다.

<!-- literal_block {"ids": [], "names": [], "highlight_args": {}, "backrefs": [], "dupnames": [], "linenos": false, "classes": [], "xml:space": "preserve", "language": "c#"} -->

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

`ProductsApi.GetProduct(int)` 과 같은 URL 경로 대 한 작업을 실행할 수는 `/products/3` 과 같은 URL 경로 대 한 `/products`합니다. 참조 [라우팅](../../fundamentals/routing.md) 에 대 한 전체 설명은 경로 서식 파일 및 관련된 옵션입니다.

이 경로 특성 정의 *경로 이름* 의 `Products_List`합니다. 경로 이름은 특정 경로 기반으로 URL을 생성 하는 것입니다. 경로 라우팅의 동작을 일치 하는 URL에는 아무런 영향이 없습니다 이름과 URL 생성에만 사용 됩니다. 경로 이름은 응용 프로그램 전체에서 고유 해야 합니다.

> [!NOTE]
> 이와 반대로 컴퓨터를 폐기 *기본 경로*, 정의 하는 `id` 매개 변수를 선택적으로 (`{id?}`). 정확 하 게 Api를 지정 하는이 기능에는 허용 하는 등의 이점도 `/products` 및 `/products/5` 를 다양 한 작업에 디스패치할 수 있습니다.

<a name=routing-combining-ref-label></a>

### <a name="combining-routes"></a>경로 결합합니다.

특성 라우팅 덜 반복을 위해 컨트롤러에서 경로 특성 경로 특성에는 개별 작업으로 결합 됩니다. 컨트롤러에 정의 된 모든 경로 템플릿은 작업에서 서식 파일 경로 앞에 추가 합니다. 컨트롤러에서 route 특성을 배치 하면 수 **모든** 컨트롤러의 작업 특성 라우팅을 사용 합니다.

<!-- literal_block {"ids": [], "names": [], "highlight_args": {}, "backrefs": [], "dupnames": [], "linenos": false, "classes": [], "xml:space": "preserve", "language": "c#"} -->

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

이 예에서 URL 경로 `/products` 일치 시킬 수 `ProductsApi.ListProducts`, 및 URL 경로 `/products/5` 일치 시킬 수 `ProductsApi.GetProduct(int)`합니다. HTTP만 일치 두이 작업은 `GET` 으로 데코레이팅되기 때문에 `HttpGetAttribute`합니다.

로 시작 하는 작업에 적용 되는 템플릿은 라우팅하는 `/` 컨트롤러에 적용 하는 경로 템플릿을 사용 하 여 결합 되지 않습니다. 이 예제와 비슷한 URL 경로 집합과 일치는 *기본 경로*합니다.

<!-- literal_block {"ids": [], "names": [], "highlight_args": {"linenostart": 1}, "backrefs": [], "dupnames": [], "linenos": false, "classes": [], "xml:space": "preserve", "language": "c#", "source": "/Users/shirhatti/src/Docs/aspnet/mvc/controllers/routing/sample/main/Controllers/HomeController.cs"} -->

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

정의 된 순서로 실행 되는 기본 경로 달리 특성 라우팅 트리를 작성 및 동시에 모든 경로 일치 합니다. 이러한 동작으로-는 이상적인 순서에 배치 되는 경로 항목 가장 구체적인 경로는 보다 일반적인 경로 되기 전에 실행할 기회가 있습니다.

예를 들어 경로 같은 `blog/search/{topic}` 경로 같은 보다 구체적인 `blog/{*article}`합니다. 논리적으로 말해는 `blog/search/{topic}` 경로 '실행' 먼저 기본적으로 합리적인 순서는 이기 때문에 있습니다. 기존의 라우팅을 사용 하는 개발자는 경로 원하는 순서로 배치 담당 합니다.

특성 경로 순서를 구성할 수를 사용 하는 `Order` 모든 제공 된 프레임 워크 경로 특성의 속성입니다. 경로 오름차순에 따라 처리 되는 일종의 `Order` 속성입니다. 기본 순서는 `0`합니다. 사용 하 여 경로 설정 `Order = -1` 순서를 설정 하지 않은 경로 보다 먼저 실행 됩니다. 사용 하 여 경로 설정 `Order = 1` 기본 경로 순서를 지정한 후에 실행 됩니다.

> [!TIP]
> 에 따라 방지 `Order`합니다. URL-스페이스를 올바르게 라우팅할 명시적 순서 값이 필요한 경우 됩니다 클라이언트 에서도 혼란 스러운 경우가 많습니다. 일반적 특성 라우팅 URL 일치 하는 올바른 경로 선택 합니다. URL 생성에 사용 되는 기본 순서 작동 하지 않는 경우 경로 이름을 사용 하는 재정의 일반적으로 적용 하는 보다 간단는 `Order` 속성입니다.

<a name=routing-token-replacement-templates-ref-label></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a>경로 템플릿에 대 한 대체 토큰 ([controller] [작업] [지역])

특성 경로 편의 위해 지원 *대체 토큰* 토큰 정사각형 중괄호로 묶어 (`[`, `]`]). 토큰 `[action]`, `[area]`, 및 `[controller]` 작업 이름, 영역 이름 및 경로가 정의 되어 있는 작업의 컨트롤러 이름 값을 값으로 대체 됩니다. 이 예에서는 설명에 설명 된 대로 작업 URL 경로 찾을 수 있습니다.

[!code-csharp[주](routing/sample/main/Controllers/ProductsController.cs?range=7-11,13-17,20-22)]

토큰 교체 특성 경로 작성할 때의 마지막 단계로 발생 합니다. 위의 예에서는 다음 코드와 동일 합니다.

<!-- literal_block {"ids": [], "names": [], "highlight_args": {"linenostart": 1}, "backrefs": [], "dupnames": [], "linenos": false, "classes": [], "xml:space": "preserve", "language": "c#", "source": "/Users/shirhatti/src/Docs/aspnet/mvc/controllers/routing/sample/main/Controllers/ProductsController2.cs"} -->

[!code-csharp[주](routing/sample/main/Controllers/ProductsController2.cs?range=7-11,13-17,20-22)]

특성 경로 상속과 함께 사용할 수도 있습니다. 이 기능은 토큰 교체와 함께 특히 유용 합니다.

<!-- literal_block {"ids": [], "names": [], "highlight_args": {}, "backrefs": [], "dupnames": [], "linenos": false, "classes": [], "xml:space": "preserve", "language": "c#"} -->

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

리터럴 토큰 대체 구분 기호에 맞게 `[` 또는 `]`, 문자를 반복 하 여 이스케이프 처리 (`[[` 또는 `]]`).

<a name=routing-multiple-routes-ref-label></a>

### <a name="multiple-routes"></a>여러 경로

동일한 작업에 도달 하는 여러 경로 정의 하는 라우팅 지원 특성입니다. 동작을 모방 하는 가장 일반적인 사용은 *기존의 기본 경로* 다음 예와에서 같이 합니다.

<!-- literal_block {"ids": [], "names": [], "highlight_args": {}, "backrefs": [], "dupnames": [], "linenos": false, "classes": [], "xml:space": "preserve", "language": "c#"} -->

```csharp
[Route("[controller]")]
public class ProductsController : Controller
{
   [Route("")]     // Matches 'Products'
   [Route("Index")] // Matches 'Products/Index'
   public IActionResult Index()
}
```

컨트롤러에서 여러 경로 특성을 배치 각 하나는 작업 메서드에 대 한 경로 특성의 각 결합 하는 작업을 의미 합니다.

<!-- literal_block {"ids": [], "names": [], "highlight_args": {}, "backrefs": [], "dupnames": [], "linenos": false, "classes": [], "xml:space": "preserve", "language": "c#"} -->

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

때 여러 경로 특성 (구현 하는 `IActionConstraint`) 경로 템플릿을 정의 하는 특성에서 사용 하 여 각 작업 제약 조건 결합 한 후 작업을에 배치 됩니다.

<!-- literal_block {"ids": [], "names": [], "highlight_args": {}, "backrefs": [], "dupnames": [], "linenos": false, "classes": [], "xml:space": "preserve", "language": "c#"} -->

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
> 작업에서 여러 경로 사용 하 여 강력한 보일 수, 간단 하 고 잘 정의 된 응용 프로그램의 URL 공간을 유지 하는 것이 좋습니다. 예를 들어 기존 클라이언트를 지원 하기 위해 필요한 경우에 작업에서 여러 경로 사용 합니다.


<a name=routing-attr-options></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a>특성 경로 선택적 매개 변수, 기본값 및 제약 조건 지정

특성 경로는 선택적 매개 변수, 기본값 및 제약 조건을 지정 하려면 기본 경로와 같은 인라인 구문을 지원 합니다.

<!-- literal_block {"ids": [], "names": [], "highlight_args": {}, "backrefs": [], "dupnames": [], "linenos": false, "classes": [], "xml:space": "preserve", "language": "c#"} -->

```csharp
[HttpPost("product/{id:int}")]
public IActionResult ShowProduct(int id)
{
   // ...
}
```

참조 [경로 템플릿 참조](../../fundamentals/routing.md#route-template-reference) 에 대 한 자세한 설명은 경로 템플릿 구문입니다.

<a name=routing-cust-rt-attr-irt-ref-label></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a>사용 하 여 사용자 지정 경로 특성`IRouteTemplateProvider`

모든 프레임 워크에서 제공 하는 경로 특성 ( `[Route(...)]`, `[HttpGet(...)]` 등) 구현에서 `IRouteTemplateProvider` 인터페이스입니다. MVC 응용 프로그램 시작 되 고 구현 하는 것을 사용 하 여 특성을 컨트롤러 클래스 및 작업 메서드를 찾고 `IRouteTemplateProvider` 경로의 초기 집합을 만들 수 있습니다.

구현할 수 있습니다 `IRouteTemplateProvider` 고유한 경로 특성을 정의할 수 있습니다. 각 `IRouteTemplateProvider` 이름을 고 순서를 사용자 지정 경로 템플릿 사용 하 여 단일 경로 정의할 수 있습니다.

<!-- literal_block {"ids": [], "names": [], "highlight_args": {}, "backrefs": [], "dupnames": [], "linenos": false, "classes": [], "xml:space": "preserve", "language": "c#"} -->

```csharp
public class MyApiControllerAttribute : Attribute, IRouteTemplateProvider
{
   public string Template => "api/[controller]";

   public int? Order { get; set; }

   public string Name { get; set; }
}
```

위의 예제에서 특성을 자동으로 설정의 `Template` 를 `"api/[controller]"` 때 `[MyApiController]` 적용 됩니다.

<a name=routing-app-model-ref-label></a>

### <a name="using-application-model-to-customize-attribute-routes"></a>응용 프로그램 모델을 사용 하 여 속성 경로 사용자 지정

*응용 프로그램 모델* 은 시작 시 모든 라우팅하고 이러한 작업을 실행 하려면 MVC에서 사용 하는 메타 데이터를 사용 하 여 만든 개체 모델입니다. *응용 프로그램 모델* 경로 특성에서 수집 된 데이터를 모두 포함 (통해 `IRouteTemplateProvider`). 작성할 수 있습니다 *규칙* 라우팅 동작 하는 방식을 사용자 지정 하는 시작 시 응용 프로그램 모델을 수정할 수 있습니다. 이 섹션에서는 응용 프로그램 모델을 사용 하 여 라우팅 사용자 지정의 간단한 예를 보여 줍니다.

[!code-csharp[주](routing/sample/main/NamespaceRoutingConvention.cs)]

<a name=routing-mixed-ref-label></a>

## <a name="mixed-routing"></a>라우팅 혼합

MVC 응용 프로그램 기본 라우팅 및 특성 라우팅의 사용을 혼합할 수 있습니다. 일반적으로 브라우저에 대 한 HTML 페이지를 제공 하는 컨트롤러에 대 한 기본 경로 사용 하 여 및 REST Api를 제공 하는 컨트롤러에 대 한 라우팅 특성은

작업은 일반적으로 라우팅됩니다 하거나 또는 특성 라우팅. 컨트롤러 또는 작업에 대 한 경로 배치 하면 수 라우팅된 특성입니다. 특성 경로 정의 하는 작업은 기본 경로 및 반대로 통해 연결할 수 없습니다. **모든** 컨트롤러에서 route 특성 라우팅된 컨트롤러 특성에 모든 작업을 만듭니다.

> [!NOTE]
> 두 가지 라우팅 시스템을 구별 하는 것은 URL 경로 템플릿과 일치 한 후에 적용 하는 프로세스입니다. 기존 라우팅 경로 값에서 일치 하는 기존의 모든 라우팅된 동작의 조회 테이블에서 작업 및 컨트롤러를 선택 하는 데 사용 됩니다. 특성 라우팅, 각 템플릿에와 이미 연결 되어 작업을 하 고 추가 조회가 필요 합니다.

<a name=routing-url-gen-ref-label></a>

## <a name="url-generation"></a>URL 생성

MVC 응용 프로그램 작업에 대 한 URL 링크를 생성 하려면 라우팅의 URL 생성 기능을 사용할 수 있습니다. Url 생성 보다 강력 하 고 관리 하기 쉬운 코드를 작성 하는 하드 코딩 Url을 제거 합니다. 이 섹션은 MVC에서 제공 하는 URL 생성 기능을 중심으로 및만 URL 생성 방법의 기본 사항을 다룹니다. 참조 [라우팅](../../fundamentals/routing.md) 에 대 한 자세한 설명은 URL 생성 합니다.

`IUrlHelper` 인터페이스는 MVC와 URL 생성에 대 한 라우팅을 간의 인프라의 기본 요소입니다. 인스턴스를 찾을 수 `IUrlHelper` 를 통해 사용할 수는 `Url` 컨트롤러, 보기 및 구성 요소 보기에는 속성입니다.

이 예제에서는 `IUrlHelper` 인터페이스를 통해 사용 되는 `Controller.Url` 다른 작업에 대 한 URL을 생성 하는 속성입니다.

[!code-csharp[주](routing/sample/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

응용 프로그램이 기존 기본값을 사용 하는 경우 라우팅의 값은 `url` 변수가 URL 경로 문자열 됩니다 `/UrlGeneration/Destination`합니다. 이 URL 경로에 전달 된 값 (앰비언트 값), 현재 요청에서 경로 값을 결합 하 여 라우팅에 의해 만들어집니다 `Url.Action` 및 경로 서식 파일에 이러한 값을 대체 합니다.

<!-- literal_block {"ids": [], "names": [], "backrefs": [], "dupnames": [], "xml:space": "preserve", "classes": []} -->

```
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

각 경로 매개 변수를 경로 템플릿에 일치 하는 이름 값과 앰비언트 값을 사용 하 여 대체 값을 있습니다. 경로 매개 변수 값이 없는 선택적 이면 건너뛸 수 있는 경우 기본 값을 사용할 수 (의 경우 마찬가지로 `id` 이 예에서). URL 생성에 필요한 경로 매개 변수는 해당 값이 없는 경우 실패 합니다. 경로 대 한 URL 생성 실패 하면 다음 경로 일치 하는 모든 경로 시도할 때까지 시도 됩니다.

에 대 한 예제 `Url.Action` 위에 가정 기존의 라우팅은 하지만 특성 라우팅의 경우와 마찬가지로 URL 생성이 작동 하지만 개념은 다른 합니다. 기존의 라우팅을 사용 하 여는 경로 값을 사용 하는 서식 파일 및 경로 값에 대 한 확장 `controller` 및 `action` 는 일반적으로 표시-해당 서식 파일에 경로 일치 하는 Url을 준수 이므로이 방법을 사용는 *규칙*합니다. 에 대 한 값 경로에서 특성 라우팅 `controller` 및 `action` 어떤 템플릿을 사용 하 여 조회를 대신 사용 됩니다 템플릿-에 올 수 없습니다.

이 예제에서는 특성 라우팅을 사용 합니다.

[!code-csharp[주](routing/sample/main/StartupUseMvc.cs?name=snippet_1)]

[!code-csharp[주](routing/sample/main/Controllers/UrlGenerationControllerAttr.cs?name=snippet_1)]

MVC 모든 특성 라우팅 동작의 조회 테이블을 작성 및 일치시킬지는 `controller` 및 `action` URL 생성에 사용할 경로 템플릿을 선택 하는 값입니다. 위의 샘플에서 `custom/url/to/destination` 생성 됩니다.

### <a name="generating-urls-by-action-name"></a>작업 이름으로 Url을 생성합니다.

`Url.Action` (`IUrlHelper` . `Action`) 및 모든 관련 기능에 연결 하려면 컨트롤러 이름과 작업 이름을 지정 하 여 지정 하려면 해당 개념을 기반으로 모든 오버 로드 합니다.

> [!NOTE]
> 사용 하는 경우 `Url.Action`, 현재 경로 대 한 값 `controller` 및 `action` 있습니다-의 값에 대해 지정 된 `controller` 및 `action` 둘 다의 일부인 *앰비언트 값* **및** *값*합니다. 메서드가 `Url.Action`, 항상의 현재 값을 사용 하 여 `action` 및 `controller` 현재 작업에 라우팅하는 URL 경로 생성 합니다.

라우팅 URL을 생성 하는 경우를 지정 하지 않은 정보를 입력 앰비언트 값의 값을 사용 하려고 합니다. 와 같은 경로 사용 하 여 `{a}/{b}/{c}/{d}` 및 앰비언트 값 `{ a = Alice, b = Bob, c = Carol, d = David }`, 라우팅에서 충분 한 정보를 생성 한 추가 값 없이 URL-매개 변수 값이 있는 모든 경로 때문입니다. 값을 추가한 경우 `{ d = Donovan }`, 값 `{ d = David }` 는 무시 하 고 생성 된 URL 경로 `Alice/Bob/Carol/Donovan`합니다.

> [!WARNING]
> URL 경로 계층적입니다. 값을 추가한 경우 위의 예에서 `{ c = Cheryl }`, 두 값 모두 `{ c = Carol, d = David }` 무시 됩니다. 이 경우 값에 대 한 더 이상이 `d` URL 생성 실패 하 게 됩니다. 원하는 값을 지정 해야 `c` 및 `d`합니다.  기본 경로와이 문제가 발생 하는 데 하려는 경우가 있습니다 (`{controller}/{action}/{id?}`)-이 동작으로 실제로 거의 하지만 `Url.Action` 항상 명시적으로 지정 됩니다는 `controller` 및 `action` 값입니다.

오버 로드를 더 오래 `Url.Action` 대해서도 추가 *값 경로* 아닌 다른 경로 매개 변수에 대 한 값을 제공 하는 개체 `controller` 및 `action`합니다. 가장 일반적으로 같은 결과가 나타납니다 사용한 `id` 같은 `Url.Action("Buy", "Products", new { id = 17 })`합니다. 규칙에 따라는 *값 라우팅할* 또한 수 있지만 개체는 일반적으로 익명 형식의 개체는 `IDictionary<>` 또는 *일반 이전.NET 개체*합니다. 경로 매개 변수를 서로 일치 하지 않는 모든 추가 경로 값은 쿼리 문자열에 저장 됩니다.

[!code-csharp[주](routing/sample/main/Controllers/TestController.cs)]

> [!TIP]
> 절대 URL을 만들려면 허용 하는 오버 로드를 사용 하 여 한 `protocol`:`Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`

<a name=routing-gen-urls-route-ref-label></a>

### <a name="generating-urls-by-route"></a>Url 경로 의해 생성

위의 코드는 컨트롤러와 작업 이름에 전달 하 여 URL을 생성 나와 있습니다. `IUrlHelper`또한 제공 된 `Url.RouteUrl` 메서드 패밀리를 합니다. 이러한 메서드는 유사 `Url.Action`, 하지만의 현재 값을 복사 하지 않는 `action` 및 `controller` 경로 값을 합니다. 일반적으로 URL을 생성 하는 특정 경로 사용 하 여 경로 이름을 지정 하는 가장 일반적인 사용 *없이* 컨트롤러나 작업 이름을 지정 합니다.

<!-- literal_block {"ids": [], "names": [], "highlight_args": {"linenostart": 1}, "backrefs": [], "dupnames": [], "linenos": false, "classes": [], "xml:space": "preserve", "language": "none", "source": "/Users/shirhatti/src/Docs/aspnet/mvc/controllers/routing/sample/main/Controllers/UrlGenerationControllerRouting.cs"} -->

[!code-csharp[주](routing/sample/main/Controllers/UrlGenerationControllerRouting.cs?name=snippet_1)]

<a name=routing-gen-urls-html-ref-label></a>

### <a name="generating-urls-in-html"></a>HTML에 Url을 생성합니다.

`IHtmlHelper`제공 된 `HtmlHelper` 메서드 `Html.BeginForm` 및 `Html.ActionLink` 생성 하 `<form>` 및 `<a>` 요소 각각. 이러한 메서드를 사용 하 여는 `Url.Action` URL을 생성 하는 메서드 비슷한 인수를 수락 합니다. `Url.RouteUrl` 위한 추가 기능에 대 한 `HtmlHelper` 는 `Html.BeginRouteForm` 및 `Html.RouteLink` 기능이 유사 합니다. 참조 [ 🔧 HTML 도우미](../views/html-helpers.md) 대 한 자세한 내용은 합니다.

통해 Url을 생성 하는 TagHelpers는 `form` TagHelper 및 `<a>` TagHelper 합니다. 두 가지 모두 사용 하 여 `IUrlHelper` 해당 구현에 있습니다. 참조 [양식 작업](../views/working-with-forms.md) 에 대 한 자세한 내용은 합니다.

뷰 내에서 `IUrlHelper` 를 통해 사용할 수는 `Url` 위의에서 포함 하지 않는 모든 임시 URL 생성에 대 한 속성입니다.

<a name=routing-gen-urls-action-ref-label></a>

### <a name="generating-urls-in-action-results"></a>작업 결과에 URL을 생성합니다.

위 예제를 사용 하 여 표시 `IUrlHelper` 작업 결과의 일부분으로 URL을 생성 하는 컨트롤러에서 가장 많이 사용 되는 동안에 컨트롤러에 있습니다.

`ControllerBase` 및 `Controller` 기본 클래스는 다른 작업을 참조 하는 작업 결과 대해 편리한 메서드를 제공 합니다. 사용자 입력을 적용 한 후 리디렉션하를 하나의 일반적인 점이입니다.

<!-- literal_block {"ids": [], "names": [], "highlight_args": {}, "backrefs": [], "dupnames": [], "linenos": false, "classes": [], "xml:space": "preserve", "language": "c#"} -->

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

작업 결과 팩터리 메서드에 메서드에 비슷한 패턴에 따라 `IUrlHelper`합니다.

<a name=routing-dedicated-ref-label></a>

### <a name="special-case-for-dedicated-conventional-routes"></a>전용된 기본 경로 대 한 특별 한 경우

기존의 라우팅은 특수 한 유형의 호출 경로 정의 사용할 수는 *기존의 전용된 경로*합니다. 아래 예에서 경로 이름은 `blog` 전용 기본 경로입니다.

<!-- literal_block {"ids": [], "names": [], "highlight_args": {}, "backrefs": [], "dupnames": [], "linenos": false, "classes": [], "xml:space": "preserve", "language": "c#"} -->

```csharp
app.UseMvc(routes =>
{
    routes.MapRoute("blog", "blog/{*article}",
        defaults: new { controller = "Blog", action = "Article" });
    routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

이러한 경로 정의 사용 하 여 `Url.Action("Index", "Home")` URL 경로가 생성 됩니다 `/` 와 `default` 그 이유에 경로? 경로 값을 추측할 수 `{ controller = Home, action = Index }` 될 URL 생성을 사용 하는 것 `blog`때 결과 및 `/blog?action=Index&controller=Home`합니다.

전용된 기본 경로 특별 한 동작의 경로 되지 못하도록 하는 해당 경로 매개 변수 없는 기본 값에 의존 "너무 greedy" URL 생성 합니다. 이 경우에 기본값은 `{ controller = Blog, action = Article }`, 않으며 `controller` 나 `action` 경로 매개 변수도 나타납니다. 라우팅 URL 생성을 수행 하는 경우 제공 된 값에는 기본 값 일치 해야 합니다. URL 세대를 사용 하 여 `blog` 못하여 값 `{ controller = Home, action = Index }` 일치 하지 않으면 `{ controller = Blog, action = Article }`합니다. 시도로 대체 한 다음 라우팅 `default`, 성공입니다.

<a name=routing-areas-ref-label></a>

## <a name="areas"></a>영역

[영역](areas.md) 는 별도 라우팅-네임 스페이스 (컨트롤러 작업의 경우) 및 폴더 구조 (뷰)를 그룹으로 관련된 기능을 구성 하는 데 사용 하는 MVC 기능입니다. 다른 것으로 응용 프로그램을 동일한 이름의 컨트롤러가 여러 개 있는 영역을 사용 하면 *영역*합니다. 영역을 사용 하는 계층 구조를 만들고 다른 경로 매개 변수를 추가 하 여 라우팅 목적으로 `area` 를 `controller` 및 `action`합니다. 이 섹션은 영역-상호 작용 라우팅 방법을 설명 합니다 참조 [영역](areas.md) 영역 (뷰 포함) 사용 방법에 대 한 자세한 합니다.

다음 예제에서는 기존의 기본 경로 사용 하는 MVC를 구성 및 *영역 경로* 명명 된 영역에 대 한 `Blog`:

[!code-csharp[주](routing/sample/AreasRouting/Startup.cs?name=snippet1)]

과 같은 URL 경로 찾을 때 `/Manage/Users/AddUser`, 첫 번째 경로 경로 값이 생성 됩니다 `{ area = Blog, controller = Users, action = AddUser }`합니다. `area` 경로 값이 기본값을 지정 하 여 생성 `area`, 사실 생겨난 경로 `MapAreaRoute` 다음과 같습니다.

[!code-csharp[주](routing/sample/AreasRouting/Startup.cs?name=snippet2)]

`MapAreaRoute`기본값 및 제약 조건을 사용 하 여 경로 만듭니다 `area` 이 경우 제공 된 영역 이름을 사용 하 여 `Blog`합니다. 기본 값을 입력 하면 경로 항상 생성 `{ area = Blog, ... }`, 제약 조건 값이 필요한 `{ area = Blog, ... }` URL 생성에 대 한 합니다.

> [!TIP]
> 기존의 라우팅은 순서가 중요 합니다. 일반적으로 영역 경로 영역 없이 경로 보다 더 구체적으로 경로 테이블 앞에 배치 되어야 합니다.

위의 예제를 사용 하 여 경로 값이 다음 작업을 일치 합니다.

[!code-csharp[주](routing/sample/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

`AreaAttribute` 어떤 나타냅니다 컨트롤러는이 컨트롤러 인지 한다고 영역의 일부로 `Blog` 영역입니다. 없는 컨트롤러는 `[Area]` 영역으로의 멤버가 아닌 특성과 **하지** 일치 하는 경우는 `area` 라우팅하여 경로 값을 제공 합니다. 다음 예제에서는 나열 된 첫 번째 컨트롤러에는 경로 값 일치 시킬 수 `{ area = Blog, controller = Users, action = AddUser }`합니다.

[!code-csharp[주](routing/sample/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[주](routing/sample/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[주](routing/sample/AreasRouting/Controllers/UsersController.cs)]

> [!NOTE]
> 각 컨트롤러의 네임 스페이스는 다음과 같습니다 완전성을 위해-컨트롤러 제공 되어서는 명명 충돌 하 여 컴파일러 오류가 발생 합니다. 클래스의 네임 스페이스는 MVC의 라우팅에 대 한 영향을 주지 않습니다.

처음 두 명의 컨트롤러 영역 구성원 및 해당 해당 영역 이름을 제공 하는 경우에 일치는 `area` 값을 라우팅합니다. 세 번째 컨트롤러의 모든 영역 및 수에 대 한 값이 없는 경우 유일한 일치 구성원이 아닙니다. `area` 라우팅에 의해 제공 됩니다.

> [!NOTE]
> 일치 하는 측면에서 *값이 없는*, 없는 경우는 `area` 값이 동일한 처럼 값에 대 한 `area` null 또는 빈 문자열입니다.

경로 대 한 값 영역 내에서 동작을 실행할 때는 `area` 으로 사용할 수는 *앰비언트 값* URL 생성에 사용할 라우팅에 있습니다. 즉,는 기본적으로 영역 역할을 할 *스티커* 다음 예제에 나타난 것 처럼 URL 생성에 대 한 합니다.

[!code-csharp[주](routing/sample/AreasRouting/Startup.cs?name=snippet3)]

<!-- literal_block {"ids": [], "names": [], "highlight_args": {"linenostart": 1}, "backrefs": [], "dupnames": [], "linenos": false, "classes": [], "xml:space": "preserve", "language": "c#", "source": "/Users/shirhatti/src/Docs/aspnet/mvc/controllers/routing/sample/AreasRouting/Areas/Duck/Controllers/UsersController.cs"} -->

[!code-csharp[주](routing/sample/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

<a name=iactionconstraint-ref-label></a>

## <a name="understanding-iactionconstraint"></a>IActionConstraint 이해

> [!NOTE]
> 이 섹션은 프레임 워크 내부 정보 및 MVC에서 실행 하는 작업을 선택 하는 방식에 심층 분석 합니다. 일반적인 응용 프로그램에는 사용자 지정 필요 하지 않습니다.`IActionConstraint`

이미 사용 가능성이 `IActionConstraint` 인터페이스와 친숙 하지 않은 경우에 있습니다. `[HttpGet]` 특성 및 이와 유사한 `[Http-VERB]` 특성 구현 `IActionConstraint` 작업 메서드의 실행을 제한 하기 위해.

<!-- literal_block {"ids": [], "names": [], "highlight_args": {}, "backrefs": [], "dupnames": [], "linenos": false, "classes": [], "xml:space": "preserve", "language": "c#"} -->

```csharp
public class ProductsController : Controller
{
    [HttpGet]
    public IActionResult Edit() { }

    public IActionResult Edit(...) { }
}
```

기본 기존 경로 URL 경로 가정 `/Products/Edit` 값을 생성 합니다 `{ controller = Products, action = Edit }`, 일치 하는 것 **모두** 여기에 표시 된 작업입니다. `IActionConstraint` 되었다고 합니다 후보-으로 간주 됩니다 두이 작업은 경로 데이터를 일치 하는 둘 다 용어입니다.

때는 `HttpGetAttribute` 실행 것입니다. 그러면 *Edit()* 에 가장 맞는 *가져오기* 및 다른 HTTP 동사에 대 한 일치 하지 않습니다. `Edit(...)` 작업을 정의 하는 모든 제약 조건이 없는 및 HTTP 동사 되므로 일치 됩니다. 따라서 가정 하 고는 `POST` -만 `Edit(...)` 일치 합니다. 그러나 하지만 대 한는 `GET` 동작을 모두과 일치할 수 있습니다-작업과 `IActionConstraint` 는 항상 간주 *더 나은* 없이 작업 보다 합니다. 따라서 때문에 `Edit()` 가 `[HttpGet]` 는 보다 구체적인 것으로 간주 하 고 동작을 모두 일치할 수 있는 경우 선택 됩니다.

개념적으로 `IActionConstraint` 는 형태의 *오버 로드*, 하지만 동일한 이름 가진 메서드를 오버 로드 하는 대신 것은 오버 로드는 동일한 URL과 일치 하는 작업 사이입니다. 또한 특성 라우팅을 사용 하 여 `IActionConstraint` 을 모두 고려 하 고 후보 다른 컨트롤러에서 작업에서 발생할 수 있습니다.

<a name=iactionconstraint-impl-ref-label></a>

### <a name="implementing-iactionconstraint"></a>IActionConstraint 구현

구현 하는 가장 간단한 방법은 `IActionConstraint` 에서 파생 된 클래스를 만드는 `System.Attribute` 작업 및 컨트롤러에 배치 합니다. MVC에서 모든 검색을 자동으로 `IActionConstraint` 특성으로 적용 되는 합니다. 응용 프로그램 모델을 사용 하 여 제약 조건 적용 하며이 아마도 가장 융통성이 높은 방법은 적용 되는 방식을 metaprogram를 수 있으므로 합니다.

다음 예제에서는 제약 조건을 선택에 따라 작업 한 *국가 코드* 에서 경로 데이터입니다. [GitHub에서 전체 샘플](https://github.com/aspnet/Entropy/blob/dev/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs)합니다.

<!-- literal_block {"ids": [], "names": [], "highlight_args": {}, "backrefs": [], "dupnames": [], "linenos": false, "classes": [], "xml:space": "preserve", "language": "c#"} -->

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

구현 하는 것에 대 한 책임이 `Accept` 메서드와 '주문을' 제약 조건 실행 하려면 선택 합니다. 이 경우에 `Accept` 메서드 반환 `true` 작업은 일치 하는 항목을 표시 하기 위해 때는 `country` 경로 값이 일치 합니다. 이 다른는 `RouteValueAttribute` 특성이 아닌 작업에 대체 (fallback)을 허용 하 합니다. 이 샘플을 보여 줍니다 정의 하는 경우는 `en-US` 작업 후 같은 국가 코드 `fr-FR` 없는 하는 보다 일반적인 컨트롤러도 대체 됩니다 `[CountrySpecific(...)]` 적용 합니다.

`Order` 속성 결정 *단계* 제약 조건은의 일부입니다. 액션 제약 조건에 따라 그룹에서 실행 된 `Order`합니다. 예를 들어 모든 프레임 워크가 제공 HTTP 메서드 특성을 사용 하 여 동일한 `Order` 단계에서 실행할 수 있도록 값입니다. 원하는 정책을 구현 하는 데 필요한 만큼 많은 단계를 포함할 수 있습니다.

> [!TIP]
> 값을 결정 하려면 `Order` HTTP 메서드보다 먼저 제약 조건에 적용 해야 하는지 여부를 고려해 야 합니다. 숫자가 낮을수록 먼저 실행 됩니다.

