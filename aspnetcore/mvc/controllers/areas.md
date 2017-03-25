---
title: "영역 | Microsoft 문서"
author: rick-anderson
description: "영역을 사용 하는 방법을 보여 줍니다."
keywords: "ASP.NET Core, 영역, 라우팅, 뷰"
ms.author: riande
manager: wpickett
ms.date: 02/14/2017
ms.topic: article
ms.assetid: 5e16d5e8-5696-4cb2-8ec7-d36be305c922
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/controllers/areas
translationtype: Machine Translation
ms.sourcegitcommit: 115a74c97de6052ec707ee164641f9c41224b9b3
ms.openlocfilehash: 59191bff0129ab3674a71a54c8df4c62e8017b72
ms.lasthandoff: 03/23/2017

---
# <a name="areas"></a>영역

여 [Dhananjay Kumar](https://twitter.com/debug_mode) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)

영역은 별도 네임 스페이스 (라우팅) 및 폴더 구조 (뷰)를 그룹으로 관련된 기능을 구성 하는 데 사용 되는 ASP.NET MVC 기능입니다. 영역을 사용 하는 계층 구조를 만들고 다른 경로 매개 변수를 추가 하 여 라우팅 목적으로 `area`, `controller` 및 `action`합니다.

영역에는 여러 개의 작은 기능 그룹으로 큰 ASP.NET 핵심 MVC 웹 앱을 분할 하는 방법을 제공 합니다. 영역은 응용 프로그램 내의 MVC 구조 효과적입니다. MVC 프로젝트에서는 모델, 컨트롤러 및 보기와 같은 논리적 구성 요소는 서로 다른 폴더에 고 MVC 명명 규칙을 사용 하 여 이러한 구성 요소 사이의 관계를 만듭니다. 큰 응용 프로그램에 대 한 별도 높은 수준 기능 영역으로 응용 프로그램을 분할 하는 것이 좋습니다 수 있습니다. 예를 들어, 체크 아웃, 청구 및 검색 등과 같은 여러 비즈니스 단위를 사용 하 여 전자 상거래 앱입니다. 이러한 단위 모두 자체 논리 구성 요소 뷰, 컨트롤러 및 모델 있습니다. 이 시나리오에서는 물리적으로 같은 프로젝트의 비즈니스 구성 요소를 분할 영역을 사용할 수 있습니다.

컨트롤러, 뷰 및 모델 자체 집합이 포함 된 ASP.NET 핵심 MVC 프로젝트에서 더 작은 기능 단위도 영역을 정의할 수 있습니다.

MVC의 영역을 사용 하는 것이 좋습니다 때 프로젝트:

* 응용 프로그램은 논리적으로 분리 해야 하는 여러 기능 높은 수준의 구성 요소 구성

* 각 기능 영역 작동할 수 없습니다 독립적으로 않도록 MVC 프로젝트를 분할.

영역 기능:

* ASP.NET 핵심 MVC 앱을 다양 한 영역을 가질 수 있습니다.

* 각 영역에는 자체 컨트롤러, 모델 및 뷰

* 대규모 MVC 프로젝트를 작업할 수 있도록 하지에 독립적으로 여러 개의 상위 수준의 구성 요소를 구성할 수 있습니다.

* 서로 다른 것으로 같은 이름의 여러 컨트롤러 지원 *영역*

영역 생성 및 사용 방법을 설명 하는 예제를 살펴보겠습니다. 컨트롤러 및 보기의 두 고유 그룹화 된 스토어 앱이 있는 경우를 가정해: 제품 및 서비스입니다. 일반적인 폴더에 대 한 구조는 MVC 영역을 사용 하는 다음과 같습니다.

* 프로젝트 이름

  * 영역

    * 제품

      * 컨트롤러

        * HomeController.cs

        * ManageController.cs

      * 보기

        * 홈

          * Index.cshtml

        * 관리

          * Index.cshtml

    * 서비스

      * 컨트롤러

        * HomeController.cs

      * 보기

        * 홈

          * Index.cshtml

MVC를 기본적으로는 영역에서 보기를 렌더링 하려고 하는 경우 다음 위치를 확인 하에서는 시도 합니다.

```text
/Areas/<Area-Name>/Views/<Controller-Name>/<Action-Name>.cshtml
   /Areas/<Area-Name>/Views/Shared/<Action-Name>.cshtml
   /Views/Shared/<Action-Name>.cshtml
   ```

이 통해 변경 될 수 있는 기본 위치는는 `AreaViewLocationFormats` 에 `Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions`합니다.

예를 들어,는 아래 코드를 사용 하는 대신 폴더 이름의 '영역'으로, 변경 된 '범주'에 있습니다.

```csharp
services.Configure<RazorViewEngineOptions>(options =>
   {
       options.AreaViewLocationFormats.Clear();
       options.AreaViewLocationFormats.Add("/Categories/{2}/Views/{1}/{0}.cshtml");
       options.AreaViewLocationFormats.Add("/Categories/{2}/Views/Shared/{0}.cshtml");
       options.AreaViewLocationFormats.Add("/Views/Shared/{0}.cshtml");
   });
   ```

구조는 한 가지 주의할 점은는 *뷰* 폴더는 유일 하 게 여기는 것이 중요 간주 되는 고 나머지 폴더의 내용을 같은 *컨트롤러* 및 *모델* 않습니다 **하지** 중요 합니다. 예를 들어 하면 필요 하지는 *컨트롤러* 및 *모델* 전혀 폴더입니다. 에서는이 방법이 내용의 *컨트롤러* 및 *모델* 은 단지 코드의 내용으로 작업 하는 경우.dll으로 컴파일 가져옵니다입니다는 *뷰* 가 되어야만 하는 요청 보기 만들어졌음을.

폴더 계층 구조를 정의한 후에 각 컨트롤러는 영역과 관련이 MVC 지시 해야 합니다. 컨트롤러 이름으로 데코 레이트 하 여 그렇게는 `[Area]` 특성입니다.

<!-- literal_block {"ids": [], "linenos": false, "xml:space": "preserve", "language": "csharp", "highlight_args": {"hl_lines": [4]}} -->

```csharp
...
   namespace MyStore.Areas.Products.Controllers
   {
       [Area("Products")]
       public class HomeController : Controller
       {
           // GET: /Products/Home/Index
           public IActionResult Index()
           {
               return View();
           }

           // GET: /Products/Home/Create
           public IActionResult Create()
           {
               return View();
           }
       }
   }
   ```

새로 만든된 분야와 함께 작동 하는 경로 정의를 설정 합니다. [컨트롤러 작업에 대 한 라우팅을](routing.md) 문서 특성 경로와 기본 경로 사용 하 여 포함 하는 경로 정의 만드는 방법에 대 한 세부 정보로 이동 합니다. 이 예제에서는 기본 경로 사용 합니다. 이렇게 하려면를 열고는 *Startup.cs* 파일을 추가 하 여 수정는 `areaRoute` 라는 아래 경로 정의 합니다.

<!-- literal_block {"ids": [], "linenos": false, "xml:space": "preserve", "language": "csharp", "highlight_args": {"hl_lines": [4, 5, 6]}} -->

```csharp
...
   app.UseMvc(routes =>
   {
     routes.MapRoute(name: "areaRoute",
       template: "{area:exists}/{controller=Home}/{action=Index}");

     routes.MapRoute(
         name: "default",
         template: "{controller=Home}/{action=Index}/{id?}");
   });
   ```

찾아 `http://<yourApp>/products`, `Index` 작업 메서드는 `HomeController` 에 `Products` 영역 호출 됩니다.

## <a name="link-generation"></a>링크 생성

* 작업 영역 내에서 링크 생성 컨트롤러를 같은 컨트롤러 내에서 다른 작업을 기반으로 합니다.

  현재 요청 경로 같은 경우를 가정해합니다`/Products/Home/Create`

  HtmlHelper 구문을 사용 하십시오.`@Html.ActionLink("Go to Product's Home Page", "Index")`

  TagHelper 구문을 사용 하십시오.`<a asp-action="Index">Go to Product's Home Page</a>`

  참고 우리 필요 '영역' 및 '컨트롤러' 값을 제공 하지 않은 현재 요청의 컨텍스트에서 사용할 수 있는 이미 여기 있습니다. 이러한 종류의 값 이라고 `ambient` 값입니다.

* 컨트롤러를 다른 컨트롤러에 다른 작업을 기반으로 작업 영역 내에서 링크 생성

  현재 요청 경로 같은 경우를 가정해합니다`/Products/Home/Create`

  HtmlHelper 구문을 사용 하십시오.`@Html.ActionLink("Go to Manage Products’  Home Page", "Index", "Manage")`

  TagHelper 구문을 사용 하십시오.`<a asp-controller="Manage" asp-action="Index">Go to Manage Products’  Home Page</a>`

  Note '영역' 앰비언트 값이 사용 하는 여기는 위에서 '컨트롤러' 값은 명시적으로 지정 됩니다.

* 다른 컨트롤러와 다른 영역에 컨트롤러를 다른 작업 기반 작업 영역 내에서 링크를 생성 합니다.

  현재 요청 경로 같은 경우를 가정해합니다`/Products/Home/Create`

  HtmlHelper 구문을 사용 하십시오.`@Html.ActionLink("Go to Services’ Home Page", "Index", "Home", new { area = "Services" })`

  TagHelper 구문을 사용 하십시오.`<a asp-area="Services" asp-controller="Home" asp-action="Index">Go to Services’ Home Page</a>`

  앰비언트 값이 없는 여기 사용 됩니다.

* 다른 컨트롤러에 다른 작업 영역을 기반으로 컨트롤러 내에서 액션에서 링크를 생성 하 고 **하지** 영역에서입니다.

  HtmlHelper 구문을 사용 하십시오.`@Html.ActionLink("Go to Manage Products’  Home Page", "Index", "Home", new { area = "" })`

  TagHelper 구문을 사용 하십시오.`<a asp-area="" asp-controller="Manage" asp-action="Index">Go to Manage Products’  Home Page</a>`

  생성 하려고 하므로 아닌 영역에 대 한 링크 값을 기반으로 빈 우리 컨트롤러 작업 앰비언트 '영역' 여기에 대 한 합니다.

## <a name="publishing-areas"></a>게시 영역

모든 `*.cshtml` 및 `wwwroot/**` 파일 때 출력을 게시할 `<Project Sdk="Microsoft.NET.Sdk.Web">` 에 포함 되는 *.csproj* 파일.

