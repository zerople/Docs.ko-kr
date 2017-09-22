---
title: "영역"
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
ms.openlocfilehash: 0f388ba090ada11a0ac7937606cbcd5a89d6263e
ms.sourcegitcommit: 78d28178345a0eea91556e4cd1adad98b1446db8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2017
---
# <a name="areas"></a>영역

여 [Dhananjay Kumar](https://twitter.com/debug_mode) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)

영역은 별도 네임 스페이스 (라우팅) 및 (views)에 대 한 폴더 구조와 관련 된 기능 그룹으로 구성 하는 데 사용 되는 ASP.NET MVC 기능입니다. 영역을 사용 하 여 다른 경로 매개 변수를 추가 하 여 라우팅 목적으로 계층 구조를 만듭니다 `area`을 `controller` 및 `action`합니다.

영역은 여러 개의 작은 기능 그룹으로 큰 ASP.NET Core MVC 웹 응용 프로그램을 분할 하는 방법을 제공 합니다. 실질적으로 영역은 응용 프로그램 내의 MVC 구조입니다. MVC 프로젝트에서 모델, 컨트롤러 및 보기와 같은 논리적 구성 요소는 서로 다른 폴더에 저장 하는 및 MVC 명명 규칙을 사용 하 여 이러한 구성 요소 간의 관계를 만들 수 있습니다. 규모가 큰 앱에 대 한 별도 높은 수준의 기능 영역을를 응용 프로그램을 분할 하는 것이 도움이 수도 있습니다. 예를 들어, 체크 아웃, 청구 및 검색 등과 같은 여러 비즈니스 단위를 사용 하 여 전자 상거래 앱입니다. 각 이러한 단위가 있는 자신의 논리적 구성 요소 뷰, 컨트롤러 및 모델입니다. 이 시나리오에서는 물리적으로 같은 프로젝트에 비즈니스 구성 요소를 분할 영역을 사용할 수 있습니다.

영역은 컨트롤러, 뷰 및 모델의 자체 집합으로 ASP.NET Core MVC 프로젝트에 기능 단위 보다 작은으로 정의할 수 있습니다.

MVC의 영역을 사용 하는 것이 좋습니다. 때 프로젝트:

* 응용 프로그램은 논리적으로 분리 해야 하는 여러 기능 높은 수준의 구성 요소 구성

* 각 기능 영역 수 수에 독립적으로 작업 않도록 MVC 프로젝트를 분할.

영역 기능:

* ASP.NET Core MVC 응용 프로그램에 다양 한 영역의 있을 수 있습니다.

* 각 영역에는 자체 컨트롤러, 모델 및 뷰

* 있습니다. 수에 독립적으로 작업을 여러 높은 수준의 구성 요소에 대형 MVC 프로젝트를 구성할 수 있습니다.

* 서로 다른으로 동일한 이름의 여러 컨트롤러 지원 *영역*

영역 생성 및 사용 방법을 설명 하는 예제를 살펴보겠습니다. 컨트롤러와 뷰의 두 가지 그룹화 된 스토어 앱이 있는 경우를 가정해: 제품 및 서비스입니다. MVC 영역을 사용 하 여 다음과 같은 아래에 대 한 일반적인 폴더 구조:

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

MVC을 기본적으로는 영역에는 뷰를 렌더링 하려고 하는 경우 다음 위치에서 찾을 시도:

```text
/Areas/<Area-Name>/Views/<Controller-Name>/<Action-Name>.cshtml
   /Areas/<Area-Name>/Views/Shared/<Action-Name>.cshtml
   /Views/Shared/<Action-Name>.cshtml
   ```

이 위치는 통해 변경할 수 있는 기본 위치는 `AreaViewLocationFormats` 에 `Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions`합니다.

예를 들어는 아래 '영역'으로 폴더 이름을 포함 하는 대신 코드를 변경 된 '범주'에 있습니다.

```csharp
services.Configure<RazorViewEngineOptions>(options =>
   {
       options.AreaViewLocationFormats.Clear();
       options.AreaViewLocationFormats.Add("/Categories/{2}/Views/{1}/{0}.cshtml");
       options.AreaViewLocationFormats.Add("/Categories/{2}/Views/Shared/{0}.cshtml");
       options.AreaViewLocationFormats.Add("/Views/Shared/{0}.cshtml");
   });
   ```

한 가지 주의할 점은의 구조는 *뷰* 폴더는 유일 하 게 여기 중요 한 선호 되 고 나머지 폴더의 콘텐츠 형식 *컨트롤러* 및 *모델* 않습니다 **하지** 중요 합니다. 예를 들어 필요 하면는 *컨트롤러* 및 *모델* 전혀 폴더입니다. 하므로이 작업이 내용의 *컨트롤러* 및 *모델* 는의 내용으로 작업 하는 경우.dll으로 컴파일 가져옵니다 정당한 코드는 *뷰* 가 요청을 하는 되어야만 보기는 다음과 같이 변경 되었습니다.

폴더 계층 구조를 정의 하 고 나면 영역 연결 된 각 컨트롤러에 MVC를 지시 해야 합니다. 그렇게 하려면 사용 하 여 컨트롤러 이름을 데코레이팅하여는 `[Area]` 특성입니다.

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

새로 만든된 사용자 영역을 사용 하는 경로 정의를 설정 합니다. [컨트롤러 작업에 대 한 라우팅을](routing.md) 문서 특성 경로와 규칙에 따른 경로 사용 하는 등의 route 정의 만드는 방법에 대 한 정보를 확인할 이동 합니다. 이 예제에서는 규칙에 따른 경로 사용 합니다. 이 위해 열고는 *Startup.cs* 파일을 추가 하 여 수정는 `areaRoute` 라는 아래 경로 정의 합니다.

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

찾아 `http://<yourApp>/products`, `Index` 의 동작 메서드는 `HomeController` 에 `Products` 영역 호출 됩니다.

## <a name="link-generation"></a>링크 생성

* 컨트롤러를 같은 컨트롤러 내에서 다른 작업을 기반으로 작업 영역 내에서 링크를 생성 합니다.

  현재 요청 경로 같은 경우를 가정해합니다`/Products/Home/Create`

  HtmlHelper 구문을 사용 하십시오.`@Html.ActionLink("Go to Product's Home Page", "Index")`

  TagHelper 구문을 사용 하십시오.`<a asp-action="Index">Go to Product's Home Page</a>`

  '영역' 및 'controller' 값 제공 하지 않아도 म 참고 여기에 현재 요청의 컨텍스트에서 사용할 수 있는 이미 있습니다. 이러한 종류의 값 이라고 `ambient` 값입니다.

* 컨트롤러를 다른 컨트롤러에 다른 작업을 기반으로 작업 영역 내에서 링크를 생성 합니다.

  현재 요청 경로 같은 경우를 가정해합니다`/Products/Home/Create`

  HtmlHelper 구문을 사용 하십시오.`@Html.ActionLink("Go to Manage Products’  Home Page", "Index", "Manage")`

  TagHelper 구문을 사용 하십시오.`<a asp-controller="Manage" asp-action="Index">Go to Manage Products’  Home Page</a>`

  Note 앰비언트 값 '영역'를 사용 하는 여기 하지만 위에서 'controller' 값은 명시적으로 지정 됩니다.

* 다른 컨트롤러와 다른 영역에 컨트롤러를 다른 작업 기반 작업 영역 내에서 링크를 생성 합니다.

  현재 요청 경로 같은 경우를 가정해합니다`/Products/Home/Create`

  HtmlHelper 구문을 사용 하십시오.`@Html.ActionLink("Go to Services’ Home Page", "Index", "Home", new { area = "Services" })`

  TagHelper 구문을 사용 하십시오.`<a asp-area="Services" asp-controller="Home" asp-action="Index">Go to Services’ Home Page</a>`

  앰비언트 값이 없는 여기서 사용 됩니다.

* 이 지역의 컨트롤러 내에서 액션에서 다른 컨트롤러에 다른 작업에 링크를 생성 하 고 **하지** 영역에서입니다.

  HtmlHelper 구문을 사용 하십시오.`@Html.ActionLink("Go to Manage Products’  Home Page", "Index", "Home", new { area = "" })`

  TagHelper 구문을 사용 하십시오.`<a asp-area="" asp-controller="Manage" asp-action="Index">Go to Manage Products’  Home Page</a>`

  생성 하는 데는 비 영역에 대 한 링크 기반에서는 빈 컨트롤러 동작 '영역' 여기에 대 한 앰비언트 값

## <a name="publishing-areas"></a>게시 영역

모든 `*.cshtml` 및 `wwwroot/**` 파일 경우 출력을 게시할 `<Project Sdk="Microsoft.NET.Sdk.Web">` 에 포함 되어는 *.csproj* 파일입니다.
