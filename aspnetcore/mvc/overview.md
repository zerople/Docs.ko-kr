---
title: "ASP.NET Core MVC 개요 | Microsoft 문서"
author: ardalis
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 89af38d1-52e0-4db7-b791-dbce909b0714
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/overview
translationtype: Machine Translation
ms.sourcegitcommit: 010b730d2716f9f536fef889bc2f767afb648ef4
ms.openlocfilehash: e498082cb5fb5c782bf7d9a2fe9c853e1be3170e
ms.lasthandoff: 03/23/2017

---
# <a name="overview-of-aspnet-core-mvc"></a>ASP.NET Core MVC의 개요

[Steve Smith](http://ardalis.com)

ASP.NET Core MVC 웹 응용 프로그램을 구축 하기 위한 다양 한 프레임 워크가 및 모델-뷰-컨트롤러를 사용 하 여 Api 패턴을 디자인 합니다.

## <a name="what-is-the-mvc-pattern"></a>MVC 패턴은 무엇입니까?

구성 요소의 세 가지 기본 그룹으로 응용 프로그램을 구분 하는 모델-뷰-컨트롤러 (MVC) 아키텍처 패턴: 모델, 뷰 및 컨트롤러입니다. 이 패턴을 달성 하기 위해 사용 하면 [중요 한 부분의 분리](http://deviq.com/separation-of-concerns/)합니다. 이 패턴을 사용 하 여, 사용자 요청은 사용자 작업을 수행 및/또는 쿼리 결과 검색 하려면 모델 작업을 담당 하는 컨트롤러에 라우팅됩니다. 컨트롤러는 사용자에 게 표시 하는 뷰를 선택 하 고 필요한 모든 모델 데이터를 제공 합니다.

다음 다이어그램에서는 세 가지 주요 구성 요소 및 장치와 다른 참조:

![MVC 패턴](overview/_static/mvc.png)

책임의이 묘사를 사용 하면 보다 쉽게 코드, 디버그 및 테스트할 (모델, 뷰 또는 컨트롤러)는 단일 작업에 있는 수 있기 때문에 복잡성 측면에서 응용 프로그램을 확장 (따릅니다는 [단일 책임 원칙](http://deviq.com/single-responsibility-principle/)). 업데이트, 테스트 및 디버그 종속성이 코드를 두 개 이상의 다음 세 가지 영역에 분산 하려면 더 어렵습니다. 예를 들어, 사용자 인터페이스 논리 비즈니스 논리 보다 더 자주 변경 하는 경향이 있습니다. 프레젠테이션 코드와 비즈니스 논리를 단일 개체에 결합 하는 경우에 사용자 인터페이스를 변경할 때마다 비즈니스 논리를 포함 하는 개체를 수정 해야 합니다. 이 경우 오류를 소개 하 고 최소한의 사용자 인터페이스를 일부 변경 후 모든 비즈니스 논리의 재 필요에 나타납니다.

> [!NOTE]
> 뷰 및 컨트롤러를 모두 모델에 따라 달라 집니다. 그러나 모델 보기도 아니고 컨트롤러에 따라 다릅니다. 이 하나 분리의 주요 이점입니다. 이러한 분리를 통해 빌드되고 테스트 될 모델이 화면 표시와 무관 합니다.

### <a name="model-responsibilities"></a>모델 책임

MVC 응용 프로그램에서 모델에서 수행 해야 하는 작업 및 모든 비즈니스 논리 또는 응용 프로그램 상태를 나타냅니다. 응용 프로그램의 상태를 유지 하는 것에 대 한 모든 구현 논리와 함께 모델에서 비즈니스 논리를 캡슐화 해야 합니다. 강력한 형식의 뷰; 해당 보기에 표시할 데이터를 포함 하도록 특별히 설계 된 ViewModel 형식 일반적으로 사용 합니다. 컨트롤러를 만들고 모델에서 이러한 ViewModel 인스턴스를 채웁니다.

> [!NOTE]
> MVC 아키텍처 패턴을 사용 하는 응용 프로그램에서 모델을 구성 하는 방법은 여러 가지가 있습니다. 일부에 대 한 자세한 [다른 종류의 모델 형식](http://deviq.com/kinds-of-models/)합니다.

### <a name="view-responsibilities"></a>보기 책임

뷰는 사용자 인터페이스를 통해 콘텐츠를 제공 하는 일을 담당 합니다. 사용 하는 [Razor 뷰 엔진](#razor-view-engine) HTML 태그에서.NET 코드를 포함 합니다. 뷰 내에서 논리를 최소화 해야 하 고 모든 논리를 관련 콘텐츠를 제공 해야 합니다. 보기에 많은 양의 논리를 수행할 필요가 복잡 한 모델의에서 데이터를 표시 하기 위해 파일을 찾을 것이 좋습니다를 사용 하는 [뷰 구성 요소](views/view-components.md), ViewModel 또는 뷰를 단순화 하기 위해 템플릿 보기.

### <a name="controller-responsibilities"></a>컨트롤러 책임

컨트롤러는 사용자 상호 작용을 처리 하 고 작업은 모델을 궁극적으로 렌더링 하는 뷰를 선택 하는 구성 요소입니다. MVC 응용 프로그램에서 뷰 정보만 표시 됩니다. 컨트롤러는 처리 하 고 사용자 입력 및 상호 작용에 응답 합니다. MVC 패턴에서 컨트롤러 초기 진입점 이며 어떤 모델에 맞게 형식과 렌더링 하는 보기를 선택 하는 일을 담당 (따라서 이름-제어 하는 응용 프로그램 요청에 응답 하는 방법을).

> [!NOTE]
> 너무 많은 책임으로 컨트롤러를 과도 하 게 복잡 수 해야 합니다. 컨트롤러 논리 지나치게 복잡 한 작동을 유지 하기 위해 사용 하 여는 [단일 책임 원칙](http://deviq.com/single-responsibility-principle/) 푸시 비즈니스 논리는 컨트롤러를 도메인 모델입니다.

>[!TIP]
> 따르면 컨트롤러 작업 같은 종류의 작업을 자주 수행 한다면는 [직접 원칙 반복 안 함](http://deviq.com/don-t-repeat-yourself/) 이러한 공통 작업으로 이동 하 여 [필터](#filters)합니다.

## <a name="what-is-aspnet-core-mvc"></a>ASP.NET 핵심 MVC 무엇입니까

ASP.NET Core MVC 프레임 워크는 간단 하 고 오픈 소스 테스트 하기 쉬운 프레젠테이션 프레임 워크 ASP.NET 코어에 맞게 최적화 합니다.

ASP.NET Core MVC 부분의 확실히 구분할 수 있는 동적 웹 사이트를 구축 하는 패턴 기반의 방법을 제공 합니다. 전체 제어 태그를 제공, TDD 방식의 개발을 지원 하 고 최신 웹 표준을 사용 합니다.

## <a name="features"></a>기능

ASP.NET Core MVC에는 다음과 같은 기능이 포함 됩니다.

* [라우팅](#routing)

* [모델 바인딩](#model-binding)

* [모델 유효성 검사](#model-validation)

* [종속성 주입](../fundamentals/dependency-injection.md)

* [필터](#filters)

* [영역](#areas)

* [웹 Api](#web-apis)

* [테스트 용이성](#testability)

* [Razor 뷰 엔진](#razor-view-engine)

* [강력한 형식의 뷰](#strongly-typed-views)

* [태그 도우미](#tag-helpers)

* [구성 요소 보기](#view-components)

### <a name="routing"></a>라우팅

ASP.NET 핵심 MVC의 맨 위에 빌드됩니다 [ASP.NET 핵심 라우팅](../fundamentals/routing.md), 수 있게 해 주는 강력한 URL 매핑 구성 요소로 이해할 수 및 검색 가능한 Url이 있는 응용 프로그램을 작성 합니다. 이렇게 하면 웹 서버에 있는 파일 구성 되는 방식에 관계 없이 링크 생성 및 검색 엔진 최적화 (SEO)에 대 한 잘 작동 하는 응용 프로그램의 URL 명명 패턴을 정의할 수 있습니다. 경로 값 제약 조건, 기본값 및 옵션 값을 지 원하는 편리한 경로 템플릿 구문을 사용 하 여 경로 정의할 수 있습니다.

*규칙 기반 라우팅* 사용 되는 형식의 URL을 전체적으로 정의할 수 응용 프로그램을 받아들이고 지정한 컨트롤러의 특정 동작 메서드에 맵은 각 형식입니다. 들어오는 요청을 받으면 라우팅 엔진 URL을 구문 분석 하 및에 정의 된 URL 형식 중 하나에 일치 하 고 연결된 된 컨트롤러 작업 메서드를 호출 합니다.

```csharp
routes.MapRoute(name: "Default", template: "{controller=Home}/{action=Index}/{id?}");
```

*라우팅 특성* 컨트롤러 및 작업 응용 프로그램의 경로 정의 하는 특성으로 데코 레이트 하 여 라우팅 정보를 지정할 수 있습니다. 이 컨트롤러와 자신이 연결 하는 작업 옆에 있는 경로 정의 배치 하는 것을 의미 합니다.

<!-- literal_block {"ids": [], "linenos": false, "xml:space": "preserve", "language": "csharp", "highlight_args": {"hl_lines": [1, 4]}} -->

```csharp
[Route("api/[controller]")]
public class ProductsController : Controller
{
  [HttpGet("{id}")]
  public IActionResult GetProduct(int id)
  {
    ...
  }
}
```

### <a name="model-binding"></a>모델 바인딩

ASP.NET Core MVC [모델 바인딩](models/model-binding.md) 클라이언트 요청 데이터 (폼 값, 경로 데이터, 쿼리 문자열 매개 변수, HTTP 헤더)는 컨트롤러를 처리할 수 있는 개체를 변환 합니다. 결과적으로, 컨트롤러 논리 할 필요가 없으며 서는 들어오는 요청 데이터; 작업 단순히 데이터를 해당 작업 메서드에 매개 변수로 갖습니다.

```csharp
public async Task<IActionResult> Login(LoginViewModel model, string returnUrl = null) { ... }
   ```

### <a name="model-validation"></a>모델 유효성 검사

ASP.NET Core MVC 지원 [유효성 검사](models/validation.md) 데이터 주석 유효성 검사 특성을 사용 하 여 모델 개체를 데코 레이트 하 여 합니다. 유효성 검사 특성 값이 서버에 게시 됩니다 전에 클라이언트 쪽에서 확인으로 컨트롤러 작업 전에 서버에 호출 됩니다.

<!-- literal_block {"ids": [], "linenos": false, "xml:space": "preserve", "language": "csharp", "highlight_args": {"hl_lines": [4, 5, 8, 9]}} -->

```csharp
using System.ComponentModel.DataAnnotations;
public class LoginViewModel
{
    [Required]
    [EmailAddress]
    public string Email { get; set; }

    [Required]
    [DataType(DataType.Password)]
    public string Password { get; set; }

    [Display(Name = "Remember me?")]
    public bool RememberMe { get; set; }
}
```

컨트롤러 작업의 경우:

<!-- literal_block {"ids": [], "linenos": false, "xml:space": "preserve", "language": "csharp", "highlight_args": {"hl_lines": [3]}} -->

```csharp
public async Task<IActionResult> Login(LoginViewModel model, string returnUrl = null)
{
    if (ModelState.IsValid)
    {
      // work with the model
    }
    // If we got this far, something failed, redisplay form
    return View(model);
}
```

프레임 워크는 클라이언트 및 서버에서 요청 데이터의 유효성 검사 처리 합니다. 모델 형식에 지정 된 유효성 검사 논리 눈에 띄지 않는 주석으로 렌더링 된 보기에 추가 되 고 사용 하 여 브라우저에 적용 됩니다 [jQuery Validation](http://jqueryvalidation.org/)합니다.

### <a name="dependency-injection"></a>종속성 주입

ASP.NET Core에서 기본적으로 지원 [DI (종속성 주입)](../fundamentals/dependency-injection.md)합니다. ASP.NET 핵심 mvc에서 [컨트롤러](controllers/dependency-injection.md) 요청 수행 하도록 허용 하는 생성자를 통해 서비스를 필요한 수의 [명시적 종속성 원칙](http://deviq.com/explicit-dependencies-principle/)합니다.

앱도 사용할 수 있습니다 [종속성 주입 보기에 파일](views/dependency-injection.md)를 사용 하 여는 `@inject` 지시문:

<!-- literal_block {"ids": [], "linenos": false, "xml:space": "preserve", "language": "html", "highlight_args": {"hl_lines": [1]}} -->

```html
@inject SomeService ServiceName
<!DOCTYPE html>
<html>
<head>
  <title>@ServiceName.GetTitle</title>
</head>
<body>
  <h1>@ServiceName.GetTitle</h1>
</body>
</html>
```

### <a name="filters"></a>필터

[필터](controllers/filters.md) 개발자가 예외 처리 또는 권한 부여와 같은 크로스 커팅 우려를 캡슐화 합니다. 필터를 사용 하면 작업 메서드에 대 한 전처리 및 후 처리 논리를 실행 중인 사용자 지정 하며 지정된 된 요청에 대 한 실행 파이프라인 내에서 특정 지점에서 실행 하도록 구성할 수 있습니다. 컨트롤러 또는 특성으로 작업에 적용할 수 있습니다 (필터나 전체적으로 실행 될 수 있습니다). 몇 가지 필터 (예: `Authorize`) 프레임 워크에 포함 됩니다.


```csharp
[Authorize]
   public class AccountController : Controller
   {

```

### <a name="areas"></a>영역

[영역](controllers/areas.md) 여러 개의 작은 기능 그룹으로 큰 ASP.NET 핵심 MVC 웹 앱을 분할 하는 방법을 제공 합니다. 영역은 응용 프로그램 내의 MVC 구조 효과적입니다. MVC 프로젝트에서는 모델, 컨트롤러 및 보기와 같은 논리적 구성 요소는 서로 다른 폴더에 고 MVC 명명 규칙을 사용 하 여 이러한 구성 요소 사이의 관계를 만듭니다. 큰 응용 프로그램에 대 한 별도 높은 수준 기능 영역으로 응용 프로그램을 분할 하는 것이 좋습니다 수 있습니다. 예를 들어, 체크 아웃, 청구 및 검색 등과 같은 여러 비즈니스 단위를 사용 하 여 전자 상거래 앱입니다. 이러한 단위 모두 자체 논리 구성 요소 뷰, 컨트롤러 및 모델 있습니다.

### <a name="web-apis"></a>웹 Api

있을 뿐만 아니라 웹 사이트 구축을 위한 뛰어난 플랫폼, ASP.NET 핵심 MVC는 웹 Api를 구축 하는 훌륭한 지원 합니다. 다양 한 브라우저 및 모바일 장치를 포함 하 여 클라이언트를 연결할 수 있는 서비스를 빌드할 수 있습니다.

프레임 워크는 기본적으로 지 원하는 HTTP 콘텐츠 협상을 지원 [데이터 서식 지정](models/formatting.md) JSON 또는 XML로 합니다. 쓰기 [사용자 지정 포맷터](advanced/custom-formatters.md) 사용자의 고유 형식에 대 한 지원을 추가할 수 있습니다.

링크 생성을 사용 하 여 하이퍼미디어에 대 한 지원을 사용 하도록 설정 합니다. 에 대 한 지원을 쉽게 사용 [크로스-원본 자원 공유 (CORS)](http://www.w3.org/TR/cors/) 웹 Api를 여러 웹 응용 프로그램에서 공유할 수 있도록 합니다.

### <a name="testability"></a>테스트 가능성

프레임 워크의 종속성 주입 및 인터페이스를 사용 하 여 더 적합 한 단위 테스트를 하 고 구성 하는 기능 (예: Entity Framework 용 TestHost 및 InMemory 공급자)를 포함 하는 프레임 워크 [통합 테스트](../testing/integration-testing.md) 쉽고 빠른도 합니다. 에 대 한 자세한 내용은 [컨트롤러 논리를 테스트](controllers/testing.md)합니다.

### <a name="razor-view-engine"></a>Razor 뷰 엔진

[ASP.NET 핵심 MVC 뷰](views/overview.md) 사용는 [Razor 뷰 엔진](views/razor.md) 뷰를 렌더링 합니다. Razor은 포함 된 C# 코드를 사용 하 여 뷰를 정의 하기 위한 단순 하 고 표현 적인 유체 템플릿 태그 언어입니다. Razor는 서버에서 웹 콘텐츠를 동적으로 생성 하는 데 사용 됩니다. 클라이언트 쪽 콘텐츠 및 코드를 정상적으로 서버 코드를 혼합할 수 있습니다.

```text
<ul>
  @for (int i = 0; i < 5; i++) {
    <li>List item @i</li>
  }
</ul>
```

정의할 수는 Razor 뷰 엔진을 사용 하 여 [레이아웃](views/layout.md), [부분 뷰](views/partial.md) 및 대체 가능 섹션입니다.

### <a name="strongly-typed-views"></a>강력한 형식의 뷰

Mvc에서 razor 뷰에서 강력한 형식을 사용할 수 모델에 기반 합니다. 컨트롤러는 형식 검사 및 IntelliSense를 지원 하 여 뷰를 사용 하도록 설정 하는 보기에는 강력한 형식의 모델을 전달할 수 있습니다.

다음 보기 형식의 모델을 정의 하는 예를 들어 `IEnumerable<Product>`:

```html
@model IEnumerable<Product>
<ul>
    @foreach (Product p in Model)
    {
        <li>@p.Name</li>
    }
</ul>
```

### <a name="tag-helpers"></a>태그 도우미

[태그 도우미](views/tag-helpers/intro.md) 만들기 및 Razor 파일의 요소를 HTML 렌더링에 참여 하는 서버 측 코드를 사용 하도록 설정 합니다. 태그 도우미를 사용 하면 사용자 지정 태그를 정의할 수 있습니다 (예를 들어 `<environment>`) 또는 기존 태그의 동작을 수정 하려면 (예를 들어 `<label>`). 태그 도우미 요소 이름 및 해당 특성에 따라 특정 요소에 바인딩합니다. HTML 편집 환경을 유지 하면서 서버 쪽 렌더링의 이점을 제공 합니다.

양식, 링크, 로드 자산, 점점-더욱 NuGet와 공용 GitHub 리포지토리에 사용할 수 있는 패키지를 만드는 것과 같은 일반적인 작업-에 대 한 기본 제공 태그 도우미 여러 가지가 있습니다. 태그 도우미 C#으로 작성 되며 및 요소 이름, 특성 이름 또는 부모 태그를 기반으로 하는 HTML 요소를 대상입니다. 예를 들어 기본적으로 제공 된 LinkTagHelper에 대 한 링크를 만드는 데 사용할 수는 `Login` 의 작업은 `AccountsController`:

<!-- literal_block {"ids": [], "linenos": false, "xml:space": "preserve", "language": "html", "highlight_args": {"hl_lines": [3]}} -->

```html
<p>
    Thank you for confirming your email.
    Please <a asp-controller="Account" asp-action="Login">Click here to Log in</a>.
</p>
```

`EnvironmentTagHelper` 개발, 준비 또는 프로덕션와 같은 런타임 환경에 따라 (예: 원시 또는 축소 된) 보기에 다른 스크립트를 포함 시키는 데 사용할 수 있습니다.

<!-- literal_block {"ids": [], "linenos": false, "xml:space": "preserve", "language": "html", "highlight_args": {"hl_lines": [1, 3, 4, 9]}} -->

```html
<environment names="Development">
    <script src="~/lib/jquery/dist/jquery.js"></script>
</environment>
<environment names="Staging,Production">
    <script src="https://ajax.aspnetcdn.com/ajax/jquery/jquery-2.1.4.min.js"
            asp-fallback-src="~/lib/jquery/dist/jquery.min.js"
            asp-fallback-test="window.jQuery">
    </script>
</environment>
```

태그 도우미는 HTML에 게 친숙 한 개발 환경 및 HTML 및 Razor 태그를 만들기 위한 다양 한 IntelliSense 환경을 제공 합니다. 대부분의 기본 제공 태그 도우미 기존 HTML 요소를 대상으로 하 고 요소에 대 한 서버 쪽 특성을 제공 합니다.

### <a name="view-components"></a>구성 요소 보기

[구성 요소를 볼](views/view-components.md) 렌더링 논리를 패키지 하 고 응용 프로그램에 걸쳐 다시 사용할 수 있습니다. 유사 [부분 뷰](views/partial.md), 서로 관련된 논리입니다.

