---
title: "ASP.NET Core MVC 개요"
author: ardalis
description: "ASP.NET Core MVC 웹 응용 프로그램을 빌드하기 위한 풍부한 프레임 워크는 방법과 모델-뷰-컨트롤러를 사용 하 여 Api 디자인 패턴에 알아봅니다."
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 89af38d1-52e0-4db7-b791-dbce909b0714
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/overview
ms.openlocfilehash: 2492b6aa4602dbbf3b9cd3dca00d40690c640cab
ms.sourcegitcommit: 6e83c55eb0450a3073ef2b95fa5f5bcb20dbbf89
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/28/2017
---
# <a name="overview-of-aspnet-core-mvc"></a>ASP.NET Core MVC 개요

으로 [Steve Smith](https://ardalis.com/)

ASP.NET Core MVC는 웹 앱을 빌드하기 위한 풍부한 프레임 워크 및 Api 모델-뷰-컨트롤러를 사용 하 여 패턴을 디자인 합니다.

## <a name="what-is-the-mvc-pattern"></a>MVC 패턴 무엇입니까?

모델-뷰-컨트롤러 (MVC) 아키텍처 패턴에서는 응용 프로그램을 3 개의 주요 구성 요소 그룹이: 모델, 뷰 및 컨트롤러입니다. 이 패턴을 달성 하기 위해 사용 하면 [문제의 분리](http://deviq.com/separation-of-concerns/)합니다. 이 패턴을 사용 하 여 사용자 요청은 사용자 작업을 수행할 및/또는 쿼리 결과 검색할 모델 작업을 담당 하는 컨트롤러에 라우팅됩니다. 컨트롤러는 사용자에 게 표시 하려면 보기를 선택 하 고 필요한 모든 모델 데이터를 제공 합니다.

다음 그림에 세 가지 주요 구성 요소 및 다른 알림을 참조:

![MVC 패턴](overview/_static/mvc.png)

책임의이 경계를 사용 하면 보다 쉽게 코딩, 디버깅, 및가 소유한 항목 (모델, 뷰 또는 컨트롤러)을 단일 작업을 테스트할 수 있기 때문에 복잡성 측면에서 응용 프로그램의 크기를 조정 (따르는 [단일 책임 원칙 ](http://deviq.com/single-responsibility-principle/)). 업데이트, 테스트 및 디버그 종속성이 코드를 두 개 이상의 이러한 세 가지 영역에 분산 하려면 더 어렵습니다. 예를 들어, 사용자 인터페이스 논리 비즈니스 논리 보다 더 자주 변경 하는 경향이 있습니다. 단일 개체에 프레젠테이션 코드 및 비즈니스 논리를 결합 하는 경우 사용자 인터페이스를 변경할 때마다 비즈니스 논리를 포함 하는 개체를 수정 해야 합니다. 이 오류가 및 인증의 재 테스트 모든 비즈니스 논리의 모든 최소한의 사용자 인터페이스를 변경 합니다.

> [!NOTE]
> 뷰와 컨트롤러 모두 모델에 따라 달라 집니다. 그러나 모델 보기도 아니고 컨트롤러에 따라 달라 집니다. 분리의 주요 이점 중 하나입니다. 이 분리를 통해 작성 하 고 테스트할 모델이 화면 표시와 무관 합니다.

### <a name="model-responsibilities"></a>모델의 책임

MVC 응용 프로그램에서 모델은 응용 프로그램 및 모든 비즈니스 논리 또는 것을 통해 수행 해야 하는 작업의 상태를 나타냅니다. 응용 프로그램의 상태를 유지 하기 위한 모든 구현 논리와 함께 모델에 비즈니스 논리를 캡슐화 해야 합니다. 강력한 형식의 뷰 데이터를 포함 하도록 특별히 설계 된 ViewModel 형식; 해당 보기에 표시 하려면 일반적으로 사용 합니다. 컨트롤러를 만들고 모델에서 이러한 ViewModel 인스턴스를 채웁니다.

> [!NOTE]
> MVC 아키텍처 패턴을 사용 하는 응용 프로그램에서 모델을 구성 하는 방법은 여러 가지가 있습니다. 일부에 대 한 자세한 [여러 가지 모델 유형에](http://deviq.com/kinds-of-models/)합니다.

### <a name="view-responsibilities"></a>보기의 책임

뷰는 사용자 인터페이스를 통해 콘텐츠를 제공 합니다. 사용 하는 [Razor 뷰 엔진](#razor-view-engine) HTML 태그에.NET 코드를 포함 합니다. 뷰 내에서 논리를 최소화 해야 하며 모든 논리 관련 콘텐츠를 제공 해야 합니다. 사용 하 여 보기에 있는 다양 한 논리를 수행할 필요가 복잡 한 모델의 데이터를에서 표시 하기 위해 파일을 찾으면 고려는 [뷰 구성 요소](views/view-components.md), ViewModel, 또는 보기를 간소화 하기 위해 템플릿 보기.

### <a name="controller-responsibilities"></a>컨트롤러의 책임

컨트롤러는 사용자 상호 작용 처리, 모델로 작업 하 고, 궁극적으로에 렌더링할 뷰를 선택 하는 구성 요소입니다. MVC 응용 프로그램에서 뷰 정보만 표시 됩니다. 컨트롤러 처리 하며 사용자 입력 및 상호 작용에 응답 합니다. MVC 패턴에서 컨트롤러는 초기 진입점 하 고 모델을 사용 하려면 형식 및 렌더링 하는 보기를 선택 (따라서 이름-제어 응용 프로그램 요청에 응답 하는 방법을).

> [!NOTE]
> 너무 많은 책임으로 컨트롤러를 지나치게 복잡 수 해야 합니다. 컨트롤러 논리를 지나치게 복잡 되는 것을 유지 하기 위해 사용 하 여는 [단일 책임 원칙](http://deviq.com/single-responsibility-principle/) 푸시 비즈니스 논리는 컨트롤러를 도메인 모델입니다.

>[!TIP]
> 컨트롤러 작업 같은 종류의 작업을 자주 수행를 찾을 경우 참고할 수는 [직접 원칙 반복 안 함](http://deviq.com/don-t-repeat-yourself/) 이러한 일반적인 작업을 이동 하 여 [필터](#filters)합니다.

## <a name="what-is-aspnet-core-mvc"></a>ASP.NET Core MVC 이란

ASP.NET Core MVC 프레임 워크는는 간단한 오픈 소스로 테스트 하기 편리한 프레젠테이션 프레임 워크를 ASP.NET Core에 맞게 최적화 합니다.

ASP.NET Core MVC 문제의 확실히 구분할 수 있도록 하는 동적 웹 사이트를 구축 하는 패턴 기반의 방법을 제공 합니다. 가능 하 게 지원 하며 최신 웹 표준을 사용 하 여 태그에 대 한 모든 권한을 제공 것입니다.

## <a name="features"></a>기능

ASP.NET Core MVC는 다음과 같습니다.

* [라우팅](#routing)
* [모델 바인딩](#model-binding)
* [모델 유효성 검사](#model-validation)
* [종속성 주입](../fundamentals/dependency-injection.md)
* [필터](#filters)
* [영역](#areas)
* [웹 Api](#web-apis)
* [테스트 가능성](#testability)
* [Razor 뷰 엔진](#razor-view-engine)
* [강력한 형식의 뷰](#strongly-typed-views)
* [태그 도우미](#tag-helpers)
* [구성 요소 보기](#view-components)

### <a name="routing"></a>라우팅

ASP.NET Core MVC의 맨 위에 빌드됩니다 [ASP.NET Core 라우팅](../fundamentals/routing.md), 수 있는 강력한 URL 매핑 구성 요소 알기 쉽고 검색 가능한 Url이 있는 응용 프로그램을 작성 합니다. 이렇게 하면 웹 서버에 있는 파일의 구성 방법에 관계 없이 링크 생성 및 검색 엔진 최적화 (SEO)에 대 한 잘 작동 하는 응용 프로그램의 URL 이름 지정 패턴을 정의할 수 있습니다. 경로 값 제약 조건, 기본값 및 선택적 값을 지원 하는 편리한 경로 템플릿 구문을 사용 하 여 프로그램 경로 정의할 수 있습니다.

*규칙 기반 라우팅* 사용 되는 형식 전체적으로 URL을 정의할 수 있습니다 응용 프로그램 허용 하 고 지정한 컨트롤러에 특정 동작 메서드에 매핑됩니다 이들의 각 형식을 지정 하는 방법입니다. 들어오는 요청을 받으면 라우팅 엔진 URL을 구문 분석 및 정의 된 URL 형식 중 하나로 일치 하 고 연결된 된 컨트롤러 동작 메서드를 호출 합니다.

```csharp
routes.MapRoute(name: "Default", template: "{controller=Home}/{action=Index}/{id?}");
```

*라우팅 특성* 컨트롤러와 응용 프로그램의 경로 정의 하는 특성을 사용 하 여 동작 데코레이팅하여 라우팅 정보를 지정할 수 있습니다. 즉, route 정의 컨트롤러 및 작업 자신이 연결 옆에 배치 됩니다.

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

ASP.NET Core MVC [모델 바인딩](models/model-binding.md) 클라이언트 요청 데이터 (폼 값, 경로 데이터, 쿼리 문자열 매개 변수, HTTP 헤더)는 컨트롤러 처리할 수 있는 개체를 변환 합니다. 컨트롤러 논리에서 들어오는 요청 데이터는 사항을 알아내는의 작업을 수행할 수 없는 결과적으로, 단순히 해당 작업 메서드에 매개 변수로 데이터를 갖습니다.

```csharp
public async Task<IActionResult> Login(LoginViewModel model, string returnUrl = null) { ... }
   ```

### <a name="model-validation"></a>모델 유효성 검사

ASP.NET Core MVC 지원 [유효성 검사](models/validation.md) 데이터 주석 유효성 검사 특성으로 모델 개체를 데코레이팅하여 합니다. 유효성 검사 특성 값은 서버에 게시 전에 클라이언트 쪽에서 확인으로 컨트롤러 작업 전에 서버에 호출 됩니다.

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

컨트롤러 동작:

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

프레임 워크 클라이언트 및 서버에서 요청 데이터 유효성 검사를 처리 합니다. 모델 유형에 대해 지정 된 유효성 검사 논리 비간섭 주석으로 렌더링 된 보기에 추가 되 고 사용 하 여 브라우저에 적용 됩니다 [jQuery 유효성 검사](https://jqueryvalidation.org/)합니다.

### <a name="dependency-injection"></a>종속성 주입

ASP.NET Core에서 기본적으로 지원 [종속성 주입 (DI)](../fundamentals/dependency-injection.md)합니다. ASP.NET Core MVC에서 [컨트롤러](controllers/dependency-injection.md) 요청 수를 수행 하도록 허용 하는 생성자를 통해 서비스 필요는 [명시적 종속성 원칙](http://deviq.com/explicit-dependencies-principle/)합니다.

앱 ´ ï ´ [종속성 주입 보이는 파일](views/dependency-injection.md)를 사용 하 여는 `@inject` 지시문:

```cshtml
@inject SomeService ServiceName
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ServiceName.GetTitle</title>
</head>
<body>
    <h1>@ServiceName.GetTitle</h1>
</body>
</html>
```

### <a name="filters"></a>필터

[필터](controllers/filters.md) 개발자가 예외 처리 또는 권한 부여와 같은 일반적인 문제를 캡슐화 합니다. 필터 작업 메서드에 대 한 전처리 및 후 처리 논리를 실행 중인 사용자 지정을 사용 하도록 설정 하며 지정된 된 요청에 대 한 실행 파이프라인 내에서 특정 지점에서 실행 되도록 구성할 수 있습니다. 컨트롤러 또는 특성으로 작업에 적용할 수 있습니다 (필터나 전체적으로 실행 될 수 있습니다). 여러 필터 (예: `Authorize`) 프레임 워크에 포함 됩니다.


```csharp
[Authorize]
   public class AccountController : Controller
   {
```

### <a name="areas"></a>영역

[영역](controllers/areas.md) 큰 ASP.NET Core MVC 웹 응용 프로그램 여러 개의 작은 기능 그룹으로 분할 하는 방법을 제공 합니다. 실질적으로 영역은 응용 프로그램 내의 MVC 구조입니다. MVC 프로젝트에서 모델, 컨트롤러 및 보기와 같은 논리적 구성 요소는 서로 다른 폴더에 저장 하는 및 MVC 명명 규칙을 사용 하 여 이러한 구성 요소 간의 관계를 만들 수 있습니다. 규모가 큰 앱에 대 한 별도 높은 수준의 기능 영역을를 응용 프로그램을 분할 하는 것이 도움이 수도 있습니다. 예를 들어, 체크 아웃, 청구 및 검색 등과 같은 여러 비즈니스 단위를 사용 하 여 전자 상거래 앱입니다. 각 이러한 단위가 있는 자신의 논리적 구성 요소 뷰, 컨트롤러 및 모델입니다.

### <a name="web-apis"></a>웹 Api

웹 사이트를 구축 하기 위한 훌륭한 플랫폼 않도록, ASP.NET Core MVC는 웹 Api를 구축 하기 위한 훌륭한 지원 합니다. 다양 한 브라우저 및 모바일 장치를 포함 하 여 클라이언트를 연결할 수 있는 서비스를 작성할 수 있습니다.

프레임 워크에서는 기본적으로 지 원하는 HTTP 콘텐츠 협상을 지원 [데이터 서식 지정](models/formatting.md) JSON 또는 XML입니다. 쓰기 [사용자 지정 포맷터](advanced/custom-formatters.md) 사용자의 고유 형식에 대 한 지원을 추가 합니다.

링크 생성을 사용 하 여 하이퍼미디어에 대 한 지원을 사용 하도록 설정 합니다. 쉽게에 대 한 지원을 사용 하도록 설정할 [크로스-원본 자원 공유 (CORS)](http://www.w3.org/TR/cors/) 여러 웹 응용 프로그램에서 Web Api를 공유할 수 있도록 합니다.

### <a name="testability"></a>테스트 가능성

인터페이스 및 종속성 주입의 프레임 워크의 사용 더 적합 한 단위 테스트를 지정 하 고 구성 하는 기능 (예: Entity Framework 용 TestHost 및 InMemory 공급자)를 포함 하는 프레임 워크 [통합 테스트](../testing/integration-testing.md) 빠른 쉽고도 합니다. 에 대 한 자세한 내용은 [컨트롤러 논리를 테스트](controllers/testing.md)합니다.

### <a name="razor-view-engine"></a>Razor 뷰 엔진

[ASP.NET Core MVC 뷰](views/overview.md) 사용는 [Razor 뷰 엔진](views/razor.md) 뷰를 렌더링 합니다. Razor은 포함 된 C# 코드를 사용 하 여 뷰를 정의 하기 위한 compact 표현이 다양 하 고 유연한 템플릿 태그 언어입니다. Razor 웹 콘텐츠 서버에서 동적으로 생성 하는 데 사용 됩니다. 클라이언트 쪽 내용 및 코드와 서버 코드를 완전히 혼합할 수 있습니다.

```text
<ul>
  @for (int i = 0; i < 5; i++) {
    <li>List item @i</li>
  }
</ul>
```

Razor 뷰 엔진을 정의할 수 있습니다를 사용 하 여 [레이아웃](views/layout.md), [부분 뷰](views/partial.md) 및 대체 가능 섹션입니다.

### <a name="strongly-typed-views"></a>강력한 형식의 뷰

Mvc에서 razor 뷰 수 강력한 형식 모델에 따라 합니다. 컨트롤러 형식 검사 및 IntelliSense 지원을 보기를 사용 하도록 설정 하는 보기에는 강력한 형식의 모델을 전달할 수 있습니다.

다음 보기 형식의 모델을 정의 하는 예를 들어 `IEnumerable<Product>`:

```cshtml
@model IEnumerable<Product>
<ul>
    @foreach (Product p in Model)
    {
        <li>@p.Name</li>
    }
</ul>
```

### <a name="tag-helpers"></a>태그 도우미

[태그 도우미](views/tag-helpers/intro.md) 만들기 및 Razor 파일에서 HTML 요소 렌더링에 참여 하도록 서버 쪽 코드를 사용 하도록 설정 합니다. 사용자 지정 태그를 정의 하려면 태그 도우미를 사용할 수 있습니다 (예를 들어 `<environment>`) 또는 기존 태그의 동작을 수정 (예를 들어 `<label>`). 태그 도우미 요소 이름 및 해당 특성에 따라 특정 요소에 바인딩합니다. 서버 쪽 렌더링 편집 환경을 제공 하는 HTML을 유지 하면서의 이점을 제공 합니다.

-양식, 링크, 로드 자산 및 점점-공용 GitHub 리포지토리에 및 NuGet로 훨씬 더 사용할 수 있는 패키지 만들기와 같은 일반적인 작업에 대 한 기본 제공 태그 도우미 여러 가지가 있습니다. 태그 도우미 C#에서 작성 하 고 요소 이름, 특성 이름 또는 부모 태그를 기반으로 하는 HTML 요소를 대상입니다. 예를 들어 기본적으로 제공 된 LinkTagHelper에 대 한 링크를 만드는 데 사용할 수는 `Login` 의 작업은 `AccountsController`:

```cshtml
<p>
    Thank you for confirming your email.
    Please <a asp-controller="Account" asp-action="Login">Click here to Log in</a>.
</p>
```

`EnvironmentTagHelper` 는 개발, 스테이징 또는 프로덕션 같은 런타임 환경에 따라 (예: 원시 또는 최소화 된) 보기에서 다른 스크립트를 포함 하는 데 사용할 수 있습니다.

```cshtml
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

태그 도우미 HTML 기반의 개발 환경 및 HTML 및 Razor 태그를 만들기 위한 다양 한 IntelliSense 환경을 제공 합니다. 대부분의 기본 제공 태그 도우미 기존 HTML 요소를 대상 하 고 요소에 대 한 서버 쪽 특성을 제공 합니다.

### <a name="view-components"></a>구성 요소 보기

[구성 요소 확인](views/view-components.md) 렌더링 논리를 패키지 하 고 응용 프로그램에 걸쳐 다시 사용할 수 있습니다. 유사 [부분 뷰](views/partial.md), 서로 관련된 논리입니다.
