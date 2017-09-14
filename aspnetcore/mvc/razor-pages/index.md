---
title: "ASP.NET Core의 Razor 페이지 소개"
author: Rick-Anderson
description: "이 문서는 ASP.NET Core의 Razor 페이지를 사용하여 페이지에 초점을 맞춘 시나리오의 손쉬운 개발에 관한 개요를 제공합니다."
keywords: "ASP.NET Core, Razor 페이지"
ms.author: riande
manager: wpickett
ms.date: 09/12/2017
ms.topic: get-started-article
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/razor-pages/index
ms.openlocfilehash: e9678279db85ec03616e693b9772c6ee71c4fef8
ms.sourcegitcommit: d2f705f7a8ef2c1a940f590e4de188621fd48d2a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2017
---
# <a name="introduction-to-razor-pages-in-aspnet-core"></a>ASP.NET Core의 Razor 페이지 소개

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [Ryan Nowak](https://github.com/rynowak)

Razor 페이지는 더 쉽고 더 생산적으로 코딩 페이지에 초점을 맞춘 시나리오를 만드는 ASP.NET Core MVC의 새로운 기능입니다.

모델-뷰-컨트롤러 방법을 사용하는 자습서를 검색할 경우 [ASP.NET Core MVC 시작](xref:tutorials/first-mvc-app/start-mvc)을 참조하세요.

<a name="prerequisites"></a>

## <a name="aspnet-core-20-prerequisites"></a>ASP.NET Core 2.0 필요 구성 요소

[.NET Core](https://www.microsoft.com/net/core) 2.0.0 이상을 설치합니다.

Visual Studio를 사용할 경우 다음 워크로드가 포함된 [Visual Studio](https://www.visualstudio.com/vs/) 2017 버전 15.3 이상을 설치합니다.

* **ASP.NET 및 웹 개발**
* **.NET Core 플랫폼 간 개발**

<a name="rpvs17"></a>

## <a name="creating-a-razor-pages-project"></a>Razor 페이지 프로젝트 만들기

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio) 

Visual Studio를 사용하여 Razor 페이지 프로젝트를 만드는 방법에 대한 자세한 내용은 [Razor 페이지 시작](xref:tutorials/razor-pages/razor-pages-start)을 참조하세요.

#   <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

명령줄에서 `dotnet new razor`를 실행합니다.

Mac용 Visual Studio에서 생성된 *.csproj* 파일을 엽니다.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code) 

명령줄에서 `dotnet new razor`를 실행합니다.

#   <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli) 

명령줄에서 `dotnet new razor`를 실행합니다.

---

## <a name="razor-pages"></a>Razor 페이지

Razor 페이지는 *Startup.cs*에서 사용하도록 설정됩니다.

[!code-cs[main](index/sample/RazorPagesIntro/Startup.cs?name=snippet_Startup)]

기본 페이지를 고려해 봅니다. <a name="OnGet"></a>

[!code-cshtml[main](index/sample/RazorPagesIntro/Pages/Index.cshtml)]

이전 코드는 Razor 뷰 파일과 매우 비슷합니다. 차이점은 `@page` 지시문입니다. `@page`는 파일을 MVC 작업으로 만듭니다. 즉, 컨트롤러를 거치지 않고 요청을 직접 처리합니다. `@page`는 페이지의 첫 번째 Razor 지시문이어야 합니다. `@page`는 다른 Razor 생성자의 동작에 영향을 미칩니다.

`PageModel` 클래스를 사용하는 비슷한 페이지는 다음 두 파일에 표시됩니다. *Pages/Index2.cshtml* 파일:

[!code-cshtml[main](index/sample/RazorPagesIntro/Pages/Index2.cshtml)]

*Pages/Index2.cshtml.cs* "코드 숨김" 파일:

[!code-cs[main](index/sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

일반적으로 `PageModel` 클래스 파일의 이름은 Razor 페이지 파일과 동일하고 *.cs*가 추가됩니다. 예를 들어 이전 Razor 페이지는 *Pages/Index2.cshtml*입니다. `PageModel` 클래스가 포함된 파일의 이름은 *Pages/Index2.cshtml.cs*입니다.

페이지에 대한 URL 경로 연결은 파일 시스템의 페이지 위치에 따라 결정됩니다. 다음 표에서는 Razor 페이지 경로 및 일치하는 URL을 보여 줍니다.

| 파일 이름 및 경로               | 일치하는 URL |
| ----------------- | ------------ |
| */Pages/Index.cshtml* | `/` 또는 `/Index` |
| */Pages/Contact.cshtml* | `/Contact` |
| */Pages/Store/Contact.cshtml* | `/Store/Contact` |
| */Pages/Store/Index.cshtml* | `/Store` 또는 `/Store/Index` |

메모:

* 런타임은 기본적으로 *Pages* 폴더에서 Razor 페이지 파일을 검색합니다.
* URL에 페이지가 포함되어 있지 않을 경우 `Index`가 기본 페이지입니다.

## <a name="writing-a-basic-form"></a>기본 폼 작성

Razor 페이지 기능은 웹 브라우저에서 사용되는 일반 패턴을 쉽게 만들도록 고안되어 있습니다. [모델 바인딩](xref:mvc/models/model-binding), [태그 도우미](xref:mvc/views/tag-helpers/intro) 및 HTML 도우미는 모두 Razor 페이지 클래스에 정의된 속성에서 *제대로 작동*합니다. `Contact` 모델에 대한 기본 “연락처” 폼을 구현하는 페이지를 고려해 봅니다.

이 문서에 있는 샘플의 경우 `DbContext`는 [Startup.cs](https://github.com/aspnet/Docs/blob/master/aspnetcore/mvc/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16) 파일에서 초기화됩니다.

[!code-cs[main](index/sample/RazorPagesContacts/Startup.cs?highlight=15-16)]

데이터 모델:

[!code-cs[main](index/sample/RazorPagesContacts/Data/Customer.cs)]

*Pages/Create.cshtml* 뷰 파일:

[!code-cshtml[main](index/sample/RazorPagesContacts/Pages/Create.cshtml)]

뷰에 대한 *Pages/Create.cshtml.cs* 코드 숨김 파일:

[!code-cs[main](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_ALL)]

일반적으로 `PageModel` 클래스를 `<PageName>Model`이라고 하고 이 클래스는 페이지와 동일한 네임스페이스에 있습니다.

`PageModel` 코드 숨김 파일을 사용할 경우 유닛 테스트가 지원되지만, 명시적 생성자 및 클래스를 작성해야 합니다. `PageModel` 코드 숨김 파일이 없는 페이지는 런타임 컴파일을 지원하고 이 지원은 개발에 유용할 수 있습니다.  <!-- review: advantage because you can make changes and refresh the browser without explicitly compiling the app -->

페이지에는 `POST` 요청에서 실행되는 `OnPostAsync` *처리기 메서드*가 있습니다(사용자가 폼을 게시할 때). HTTP 동사에 대한 처리기 메서드를 추가할 수 있습니다. 가장 일반적인 처리기는 다음과 같습니다.

* `OnGet` - 페이지에 필요한 상태를 초기화합니다. [OnGet](#OnGet) 샘플.
* `OnPost` - 제출에서 처리합니다.

`Async` 명명 접미사는 선택 사항이지만 비동기 함수에 대한 규칙에서 종종 사용됩니다. 이전 예제의 `OnPostAsync` 코드는 일반적으로 컨트롤러에서 작성되는 것과 비슷해 보입니다. 이전 코드는 Razor 페이지에 일반적인 코드입니다. [모델 바인딩](xref:mvc/models/model-binding), [유효성 검사](xref:mvc/models/validation) 및 작업 결과 같은 대부분의 MVC 기본 형식이 공유됩니다.  <!-- Review: Ryan, can we get a list of what is shared and what isn't? -->

이전 `OnPostAsync` 메서드:

[!code-cs[main](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync)]

`OnPostAsync`의 기본 흐름:

유효성 검사 오류를 확인합니다.

*  오류가 없는 경우 데이터를 저장하고 리디렉션됩니다.
*  오류가 있는 경우 유효성 검사 메시지가 포함된 페이지를 다시 표시합니다. 클라이언트 쪽 유효성 검사는 기존 ASP.NET Core MVC 응용 프로그램과 동일합니다. 대부분의 경우 유효성 검사 오류는 클라이언트에서 검색되고 서버에는 제출되지 않습니다.

데이터를 성공적으로 입력하면 `OnPostAsync` 처리기 메서드는 `RedirectToPage` 도우미 메서드를 호출하여 `RedirectToPageResult` 인스턴스를 반환합니다. `RedirectToPage`는 `RedirectToAction` 또는 `RedirectToRoute`와 비슷하지만 페이지에 대해 사용자 지정된 새 작업 결과입니다. 이전 샘플에서 이 메서드는 루트 인덱스 페이지(`/Index`)로 리디렉션됩니다. `RedirectToPage`는 [페이지에 대한 URL 생성](#url_gen) 섹션에서 자세히 설명합니다.

서버에 전달되는 유효성 오류가 제출된 폼에 있는 경우 `OnPostAsync` 처리기 메서드는 `Page` 도우미 메서드를 호출합니다. `Page`는 `PageResult` 인스턴스를 반환합니다. `Page` 반환은 컨트롤의 작업이 `View`를 반환하는 방식과 비슷합니다. `PageResult`는 처리기 메서드의 기본 <!-- Review  --> 반환 형식입니다. `void`를 반환하는 처리기 메서드는 페이지를 렌더링합니다.

`Customer` 속성은 `[BindProperty]` 특성을 사용하여 모델 바인딩으로 옵트인(opt in)합니다.

[!code-cs[main](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_PageModel&highlight=10-11)]

Razor 페이지는 기본적으로 GET이 아닌 동사에만 속성을 바인딩합니다. 속성에 바인딩하면 작성해야 하는 코드 양이 감소할 수 있습니다. 바인딩은 동일한 속성을 사용하여 폼 필드(`<input asp-for="Customer.Name" />`)를 렌더링하고 입력을 허용하는 방식으로 코드를 줄입니다.

홈페이지(*Index.cshtml*):

[!code-cshtml[main](index/sample/RazorPagesContacts/Pages/Index.cshtml)]

코드 숨김 *Index.cshtml.cs* 파일:

[!code-cs[main](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs)]

*Index.cshtml* 파일에는 각 연락처의 편집 링크를 만들기 위해 다음 태그가 포함됩니다.

```cshtml
<a asp-page="./Edit" asp-route-id="@contact.Id">edit</a>
```

[앵커 태그 도우미](xref:mvc/views/tag-helpers/builtin-th/AnchorTagHelper)는 [asp-route-{value}](xref:mvc/views/tag-helpers/builtin-th/AnchorTagHelper#route) 특성을 사용하여 편집 페이지 링크를 생성했습니다. 링크에는 연락처 ID와 함께 경로 데이터가 포함됩니다. 예를 들어, `http://localhost:5000/Edit/1`을 입력합니다.

*Pages/Edit.cshtml* 파일:

[!code-cshtml[main](index/sample/RazorPagesContacts/Pages/Edit.cshtml?highlight=1)]

첫 번째 줄에는 `@page "{id:int}"` 지시문이 포함됩니다. 라우팅 제약 조건 `"{id:int}"`는 `int` 경로 데이터가 포함된 페이지에 대한 요청을 허용하도록 페이지에 지시합니다. 페이지에 대한 요청에 `int`로 변환될 수 있는 경로 데이터가 없으면 런타임은 HTTP 404(찾을 수 없음) 오류를 반환합니다.

*Pages/Edit.cshtml.cs* 파일:

[!code-cs[main](index/sample/RazorPagesContacts/Pages/Edit.cshtml.cs)]

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-razor-pages"></a>XSRF/CSRF 및 Razor 페이지

[위조 방지 유효성 검사](xref:security/anti-request-forgery)에 대한 코드를 작성할 필요가 없습니다. 위조 방지 토큰 생성 및 유효성 검사는 Razor 페이지에 자동으로 포함됩니다.

<a name="layout"></a>
## <a name="using-layouts-partials-templates-and-tag-helpers-with-razor-pages"></a>Razor 페이지와 함께 레이아웃, 부분, 템플릿 및 태그 도우미 사용

페이지는 Razor 뷰 엔진의 모든 기능을 사용합니다. 레이아웃, 부분, 템플릿, 태그 도우미, *_ViewStart.cshtml*, *_ViewImports.cshtml*은 기존 Razor 뷰와 동일한 방식으로 작동합니다.

해당 기능 중 일부를 활용하여 이 페이지의 문제를 해결해 보겠습니다.

[레이아웃 페이지](xref:mvc/views/layout)를 *Pages/_Layout.cshtml*에 추가합니다.

[!code-cshtml[main](index/sample/RazorPagesContacts2/Pages/_LayoutSimple.cshtml)]

[레이아웃](xref:mvc/views/layout):

* 각 페이지의 레이아웃을 제어합니다(페이지가 레이아웃에서 옵트아웃(opt out)되지 않는 경우).
* JavaScript 및 스타일시트 같은 HTML 구조를 가져옵니다.

자세한 내용은 [레이아웃 페이지](xref:mvc/views/layout)를 참조하세요.

[Layout](xref:mvc/views/layout#specifying-a-layout) 속성은 *Pages/_ViewStart.cshtml*에서 설정됩니다.

[!code-cshtml[main](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

**참고:** 레이아웃은 *Pages* 폴더에 있습니다. 페이지는 현재 페이지와 동일한 폴더에서 시작하여 다른 뷰(레이아웃, 템플릿, 부분)를 계층 구조로 검색합니다. *Pages* 폴더의 레이아웃은 *Pages* 폴더 아래의 Razor 페이지에서 사용될 수 있습니다.

레이아웃 파일은 *Views/Shared* 폴더에 두지 **않는** 것이 좋습니다. *Views/Shared*는 MVC 뷰 패턴입니다. Razor 페이지는 경로 규칙이 아니라 폴더 계층 구조를 사용해야 합니다.

Razor 페이지의 뷰 검색에는 *Pages* 폴더가 포함됩니다. MVC 컨트롤러 및 기존 Razor 뷰에서 사용 중인 레이아웃, 템플릿 및 부분이 *제대로 작동*합니다.

*Pages/_ViewImports.cshtml* 파일을 추가합니다.

[!code-cshtml[main](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

`@namespace`는 자습서의 뒷부분에서 설명합니다. `@addTagHelper` 지시문은 [기본 제공 태그 도우미](xref:mvc/views/tag-helpers/builtin-th/Index)를 *Pages* 폴더의 모든 페이지에 도입합니다.

<a name="namespace"></a>

`@namespace` 지시문은 페이지에서 명시적으로 사용됩니다.

[!code-cshtml[main](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

이 지시문은 페이지에 대한 네임스페이스를 설정합니다. `@model` 지시문에는 네임스페이스가 포함될 필요가 없습니다.

`@namespace` 지시문이 *_ViewImports.cshtml*에 포함되면 지정된 네임스페이스는 `@namespace` 지시문을 가져오는 페이지의 생성된 네임스페이스에 대한 접두사를 제공합니다. 생성된 네임스페이스의 나머지 부분(접미사 부분)은 *_ViewImports.cshtml*이 포함된 폴더와 페이지가 포함된 폴더 사이의 점으로 구분된 상대 경로입니다.

예를 들어 코드 숨김 파일 *Pages/Customers/Edit.cshtml.cs*는 네임스페이스를 명시적으로 설정합니다.

[!code-cs[main](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

*Pages/_ViewImports.cshtml* 파일은 다음 네임스페이스를 설정합니다.

[!code-cshtml[main](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

*Pages/Customers/Edit.cshtml* Razor 페이지에 대한 생성된 네임스페이스는 코드 숨김 파일과 동일합니다. `@namespace` 지시문은 코드 숨김 파일에 대한 `@using` 지시문을 추가할 필요 없이 프로젝트 및 페이지 생성 코드에 추가된 C# 클래스가 *제대로 작동*하도록 고안되었습니다.

**참고:** `@namespace`는 기존 Razor 뷰에서도 작동합니다.

원래 *Pages/Create.cshtml* 뷰 파일:

[!code-cshtml[main](index/sample/RazorPagesContacts/Pages/Create.cshtml?highlight=2)]

업데이트된 *Pages/Create.cshtml* 뷰 파일:

[!code-cshtml[main](index/sample/RazorPagesContacts2/Pages/Customers/Create.cshtml?highlight=2)]

[Razor 페이지 시작 프로젝트](#rpvs17)에는 클라이언트 쪽 유효성 검사를 연결하는 *Pages/_ValidationScriptsPartial.cshtml*이 포함됩니다.

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a>페이지에 대한 URL 생성

이전에 표시된 `Create` 페이지에서는 `RedirectToPage`를 사용합니다.

[!code-cs[main](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=10)]

앱에는 다음 파일/폴더 구조가 있습니다.

* */Pages*

  * *Index.cshtml*
  * */Customer*

    * *Create.cshtml*
    * *Edit.cshtml*
    * *Index.cshtml*

*Pages/Customers/Create.cshtml* 및 *Pages/Customers/Edit.cshtml* 페이지는 성공 후에 *Pages/Index.cshtml*로 리디렉션됩니다. 문자열 `/Index`는 이전 페이지에 액세스하기 위한 URI 부분입니다. 문자열 `/Index`는 *Pages/Index.cshtml* 페이지의 URI를 생성하는 데 사용될 수 있습니다. 예:

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">My Index Page</a>`
* `RedirectToPage("/Index")`

페이지 이름은 루트 */Pages* 폴더에서 시작되는 페이지 경로입니다(선행 `/` 포함, 예: `/Index`). 이전 URL 생성 샘플은 URL 하드 코딩보다 훨씬 더 기능이 다양합니다. URL 생성은 [라우팅](xref:mvc/controllers/routing)을 사용하며 경로가 대상 경로에서 정의되는 방식에 따라 매개 변수를 생성하고 인코딩할 수 있습니다.

페이지에 대한 URL 생성은 상대적 이름을 지원합니다. 다음 표에서는 *Pages/Customers/Create.cshtml*에서 다른 `RedirectToPage` 매개 변수를 통해 선택되는 인덱스 페이지를 보여 줍니다.

| RedirectToPage(x)| 페이지 |
| ----------------- | ------------ |
| RedirectToPage("/Index") | *Pages/Index* |
| RedirectToPage("./Index"); | *Pages/Customers/Index* |
| RedirectToPage("../Index") | *Pages/Index* |
| RedirectToPage("Index")  | *Pages/Customers/Index* |

`RedirectToPage("Index")`, `RedirectToPage("./Index")` 및 `RedirectToPage("../Index")`는 *상대적 이름*입니다. `RedirectToPage` 매개 변수는 현재 페이지의 경로와 *결합*되어 대상 페이지의 이름을 계산합니다.  <!-- Review: Original had The provided string is combined with the page name of the current page to compute the name of the destination page. -- page name, not page path -->

상대적 이름 연결은 구조가 복잡한 사이트를 빌드할 때 유용합니다. 상대적 이름을 사용하여 한 폴더의 여러 페이지 간을 연결하는 경우 해당 폴더의 이름을 바꿀 수 있습니다. 그래도 모든 링크가 작동합니다(폴더 이름을 포함하지 않기 때문).

## <a name="tempdata"></a>TempData

ASP.NET Core는 [TempData](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.controller#Microsoft_AspNetCore_Mvc_Controller_TempData) 속성을 [컨트롤러](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.controller)에서 노출합니다. 이 속성은 판독될 때까지 데이터를 저장합니다. `Keep` 및 `Peek` 메서드를 사용하여 삭제 없이 데이터를 검사할 수 있습니다. `TempData`는 두 개 이상의 요청에 대한 데이터가 필요할 경우 리디렉션에 유용합니다.

`[TempData]` 특성은 ASP.NET Core 2.0의 새로운 기능이고 컨트롤러 및 페이지에서 지원됩니다.

다음 코드는 `TempData`를 사용하여 `Message` 값을 설정합니다.

[!code-cs[main](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

*Pages/Customers/Index.cshtml* 파일의 다음 태그는 `TempData`를 사용하여 `Message` 값을 표시합니다.

```cshtml
<h3>Msg: @Model.Message</h3>
```

*Pages/Customers/Index.cshtml.cs* 코드 숨김 파일은 `[TempData]` 특성을 `Message` 속성에 적용합니다.

```cs
[TempData]
public string Message { get; set; }
```

자세한 내용은 [TempData](xref:fundamentals/app-state#temp)를 참조하세요.

<a name="mhpp"></a>
## <a name="multiple-handlers-per-page"></a>페이지당 여러 처리기

다음 페이지는 `asp-page-handler` 태그 도우미를 사용하여 두 개의 페이지 처리기에 대한 태그를 생성합니다.

[!code-cshtml[main](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<!-- Review: the FormActionTagHelper applies to all <form /> elements on a Razor page, even when there is no `asp-` attribute   -->

이전 예제의 폼에는 두 개의 제출 단추가 있고, 각 단추는 `FormActionTagHelper`를 사용하여 다른 URL에 제출됩니다. `asp-page-handler` 특성은 `asp-page`와 함께 사용됩니다. `asp-page-handler`는 페이지에서 정의된 각 처리기 메서드에 제출되는 URL을 생성합니다. 샘플이 현재 페이지에 연결되어 있으므로 `asp-page`가 지정되지 않습니다.

코드 숨김 파일:

[!code-cs[main](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

이전 코드는 *명명된 처리기 메서드*를 사용합니다. 명명된 처리기 메서드를 만들려면 `On<HTTP Verb>` 뒤와 `Async`(있는 경우) 앞에 이름의 텍스트를 사용합니다. 이전 예제에서 페이지 메서드는 OnPost**JoinList**Async 및 OnPost**JoinListUC**Async입니다. *OnPost* 및 *Async*가 제거된 처리기 이름은 `JoinList` 및 `JoinListUC`입니다.

[!code-cshtml[main](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

이전 코드를 사용할 경우 `OnPostJoinListAsync`에 제출되는 URL 경로는 `http://localhost:5000/Customers/CreateFATH?handler=JoinList`입니다. `OnPostJoinListUCAsync`에 제출되는 URL 경로는 `http://localhost:5000/Customers/CreateFATH?handler=JoinListUC`입니다.

## <a name="customizing-routing"></a>라우팅 사용자 지정

URL에서 쿼리 문자열 `?handler=JoinList`를 사용하지 않으려면 경로를 변경하여 처리기 이름을 URL의 경로 부분에 넣습니다. `@page` 지시문 뒤에 큰따옴표로 묶은 경로 템플릿을 추가하여 경로를 사용자 지정할 수 있습니다.

[!code-cshtml[main](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

이전 경로는 쿼리 문자열 대신 URL 경로에 처리기 이름을 넣습니다. `handler` 뒤의 `?`는 경로 매개 변수가 선택 사항임을 의미합니다.

`@page`를 사용하여 페이지 경로에 다른 세그먼트 및 매개 변수를 추가할 수 있습니다. 무엇이든 페이지의 기본 경로에 **추가**됩니다. 절대 또는 가상 경로를 사용하여 페이지 경로를 변경하는 기능(예: `"~/Some/Other/Path"`)은 지원되지 않습니다.

## <a name="configuration-and-settings"></a>구성 및 설정

고급 옵션을 구성하려면 MVC 빌더에서 확장 메서드 `AddRazorPagesOptions`를 사용합니다.

[!code-cs[main](index/sample/RazorPagesContacts/StartupAdvanced.cs?name=snippet_1)]

현재 `RazorPagesOptions`를 사용하여 페이지의 루트 디렉터리를 설정하거나 페이지에 대한 응용 프로그램 모델 규칙을 추가할 수 있습니다. 나중에 이 방법으로 더 큰 확장성을 사용할 수 있기를 기대합니다.

뷰를 미리 컴파일하려면 [Razor 뷰 컴파일](xref:mvc/views/view-compilation)을 참조하세요.

[샘플 코드를 다운로드하거나 봅니다](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/razor-pages/index/sample).

이 소개에 따라 빌드되는 [ASP.NET Core의 Razor 페이지 시작](xref:tutorials/razor-pages/razor-pages-start)을 참조하세요.