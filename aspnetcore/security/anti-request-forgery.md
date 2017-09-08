---
title: "ASP.NET Core에서 교차 사이트 요청 위조 (XSRF/CSRF) 공격 방지"
author: steve-smith
ms.author: riande
description: "ASP.NET Core에서 교차 사이트 요청 위조 (XSRF/CSRF) 공격 방지"
manager: wpickett
ms.date: 7/14/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/anti-request-forgery
ms.openlocfilehash: 466453bff68f3e0da8b90924edb13095c7548db5
ms.sourcegitcommit: 4f075f2c22c5a4b5345ffa759be4365824110788
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2017
---
# <a name="preventing-cross-site-request-forgery-xsrfcsrf-attacks-in-aspnet-core"></a>ASP.NET Core에서 교차 사이트 요청 위조 (XSRF/CSRF) 공격 방지

[Steve Smith](http://ardalis.com/), [Fiyaz Hasan](https://twitter.com/FiyazBinHasan), 및 [Rick Anderson](https://twitter.com/RickAndMSFT)

## <a name="what-attack-does-anti-forgery-prevent"></a>위조 방지 어떤 공격을 방지가?

교차 사이트 요청 위조 (XSRF 또는 CSRF, 발음 함 *참조 surf*)가 악의적인 웹 사이트 클라이언트 브라우저와 신뢰 하는 웹 사이트 간의 상호 작용 영향을 줄 수는 그에 따라 웹 호스팅 응용 프로그램에 대 한 공격 해당 브라우저입니다. 이러한 공격은 웹 브라우저가 웹 사이트에 일부 유형의 인증 토큰 모든 요청에 자동으로 전송 하기 때문에 가능 합니다. 이러한 형태의 악용은 라고도 *원클릭 공격* 또는 as *세션 도용을*공격 활용 하는 사용자의 세션을 이전에 인증의 때문에, 합니다.

CSRF 공격의 예:

1. 사용자가에 로그인 `www.example.com`, 폼 인증을 사용 하 여 합니다.
2. 서버에서 사용자를 인증 하지 않으며 인증 쿠키를 포함 하는 응답 합니다.
3. 사용자가 악성 사이트를 방문 합니다.

   악성 사이트 다음과 유사한 HTML 폼을 포함 되어 있습니다.

```html
   <h1>You Are a Winner!</h1>
     <form action="http://example.com/api/account" method="post">
       <input type="hidden" name="Transaction" value="withdraw" />
       <input type="hidden" name="Amount" value="1000000" />
     <input type="submit" value="Click Me"/>
   </form>
```

Form action에는 취약 한 사이트 악성 사이트에 게시 확인 합니다. CSRF의 "사이트 간" 부분입니다.

4. 사용자가 제출 단추를 클릭 합니다. 브라우저 요청을 사용 하 여 요청 된 도메인 (이 경우에 취약 한 사이트)에 대 한 인증 쿠키를 자동으로 포함 됩니다.
5. 요청은 사용자의 인증 컨텍스트를 사용 하 여 서버에서 실행 되며 인증된 된 사용자가 수행할 수 있는 모든 작업을 수행할 수 있습니다.

이 예제에서는 사용자 양식 단추를 클릭 합니다. 악의적인 페이지는 다음 작업을 수행할 수 있습니다.

* 폼을 자동으로 전송 하는 스크립트를 실행 합니다.
* 양식을 제출을 하 여 AJAX 요청으로 보냅니다. 
* Css 숨겨진된 폼을 사용 합니다. 

SSL을 사용 하 여 해도 CSRF 공격을 악성 사이트로 보낼 수는 `https://` 요청 합니다. 

일부 공격 대상에 응답 하는 사이트 끝점 `GET` (공격의이 폼은 일반적인 포럼 사이트에 이미지를 허용 하지만 JavaScript를 차단 하는) 작업을 수행할 경우의 이미지 태그를 사용할 수 있는 요청 합니다. 상태 변경 하는 응용 프로그램 `GET` 요청은 악의적인 공격에 취약 합니다.

CSRF 공격 브라우저가 대상 웹 사이트에 모든 관련 쿠키를 전송 하기 때문에 인증을 위한 쿠키를 사용 하는 웹 사이트에 대해 수는 있습니다. 그러나 CSRF 공격 쿠키 악용에 제한 되지 않습니다. 예를 들어, 기본 및 다이제스트 인증 취약 됩니다. 기본 또는 다이제스트 인증을 사용 하 여 사용자가 로그 되어 세션이 종료 될 때까지 브라우저가 자동으로 자격 증명을 보냅니다.

참고:이 컨텍스트에서 *세션* 참조 하는 사용자가 인증 하는 클라이언트 세션입니다. 서버 쪽 세션와 관련 없는 또는 [세션 미들웨어](xref:fundamentals/app-state)합니다.

사용자가 여 CSRF 취약점을 방지할 수 있습니다.:
* 웹 사이트에서 사용 하 여 종료 되었음을 로깅입니다.
* 주기적으로 브라우저의 쿠키를 지우고 있습니다.

그러나 CSRF 취약점은 기본적으로 웹 앱을 최종 사용자가 아닌 문제입니다.

## <a name="how-does-aspnet-core-mvc-address-csrf"></a>ASP.NET Core MVC CSRF를 처리 하는 방법

> [!WARNING]
> ASP.NET Core request 위조 방지를 사용 하 여 구현 하는 [ASP.NET Core 데이터 보호 스택의](xref:security/data-protection/introduction)합니다. ASP.NET Core 데이터 보호 서버 팜에서 작동 하도록 구성 되어야 합니다. 참조 [데이터 보호를 구성](xref:security/data-protection/configuration/overview) 자세한 정보에 대 한 합니다.

ASP.NET Core anti request 위조 기본 데이터 보호 구성 

ASP.NET Core MVC 2.0에서는 [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) HTML 폼 요소에 대 한 위조 방지 토큰을 삽입 합니다. 예를 들어 Razor 파일에 다음 태그 위조 방지 토큰을 자동으로 생성 됩니다.

```html
<form method="post">
  <!-- form markup -->
</form>
```

발생 하는 HTML 폼 요소에 대 한 자동 생성 위조 방지 토큰의 경우:

* `form` 태그에는 `method="post"` 특성 AND

  * Action 특성은 비어 있습니다. ( `action=""`) 또는
  * 작업 특성이 제공 되지 않습니다. (`<form method="post">`)

위조 방지 토큰에서 HTML 폼 요소에 대 한 자동 생성을 비활성화할 수 있습니다.

* 명시적으로 사용 하지 않도록 설정 `asp-antiforgery`합니다. 예

 ```html
  <form method="post" asp-antiforgery="false">
  </form>
  ```

* 태그 도우미를 사용 하 여 태그 도우미 부족 form 요소를 선택할 [! 옵트아웃 기호](xref:mvc/views/tag-helpers/intro#opt-out)합니다.

 ```html
  <!form method="post">
  </!form>
  ```

* 제거는 `FormTagHelper` 보기에서 합니다. 제거할 수는 `FormTagHelper` Razor 보기에는 다음 지시문을 추가 하 여 보기에서:

 ```html
  @removeTagHelper Microsoft.AspNetCore.Mvc.TagHelpers.FormTagHelper, Microsoft.AspNetCore.Mvc.TagHelpers
  ```

> [!NOTE]
> [Razor 페이지](xref:mvc/razor-pages/index) XSRF/CSRF에서 자동으로 보호 됩니다. 추가 코드를 작성할 필요가 없습니다. 참조 [XSRF/CSRF 및 Razor 페이지](xref:mvc/razor-pages/index#xsrf) 자세한 정보에 대 한 합니다.

CSRF 공격 으로부터 보호 하는 데 가장 일반적인 방법은 동기화 장치 토큰 패턴이 (STP)입니다. STP에는 사용자 페이지를 양식 데이터로 요청할 때 사용 되는 기술입니다. 서버는 클라이언트에 현재 사용자의 id와 연결 된 토큰을 보냅니다. 클라이언트가 보냅니다 확인을 위해 서버를 다시 토큰. 인증 된 사용자의 id와 일치 하지 않는 토큰을 수신 하는 서버, 요청이 거부 됩니다. 토큰이 고유 하 고 예측할 수 없는 되었습니다. 토큰은 적절 한 시퀀스에는 일련의 요청 (3 페이지 앞에 오는 페이지 2 앞에 오는 등과 페이지 1) 되도록 데도 사용할 수 있습니다. ASP.NET Core MVC 템플릿의 모든 양식을 antiforgery 토큰을 생성 합니다. 뷰 논리의 다음 두 예제는 antiforgery 토큰을 생성 합니다.

```html
<form asp-controller="Manage" asp-action="ChangePassword" method="post">

</form>

@using (Html.BeginForm("ChangePassword", "Manage"))
{
    
}
```

Antiforgery 토큰을 명시적으로 추가할 수는 ``<form>`` HTML 도우미와 태그 도우미를 사용 하지 않고 요소 ``@Html.AntiForgeryToken``:


```html
<form action="/" method="post">
    @Html.AntiForgeryToken()
</form>
```

```html
In each of the preceding cases, ASP.NET Core will add a hidden form field similar to the following:

<input name="__RequestVerificationToken" type="hidden" value="CfDJ8NrAkSldwD9CpLRyOtm6FiJB1Jr_F3FQJQDvhlHoLNJJrLA6zaMUmhjMsisu2D2tFkAiYgyWQawJk9vNm36sYP1esHOtamBEPvSk1_x--Sg8Ey2a-d9CV2zHVWIN9MVhvKHOSyKqdZFlYDVd69XYx-rOWPw3ilHGLN6K0Km-1p83jZzF0E4WU5OGg5ns2-m9Yw" />
```

ASP.NET Core 3 개 포함 [필터](xref:mvc/controllers/filters) antiforgery 토큰을 사용 하기 위한: ``ValidateAntiForgeryToken``, ``AutoValidateAntiforgeryToken``, 및 ``IgnoreAntiforgeryToken``합니다.

<a name="vaft"></a>

### <a name="validateantiforgerytoken"></a>ValidateAntiForgeryToken

``ValidateAntiForgeryToken`` 는 개별 작업은 컨트롤러에 적용할 수 있는 작업 필터는 전역적으로 또는 합니다. 요청에 유효한 antiforgery 토큰에 포함 되지 않으면이 필터 적용이 영향을 주는 작업에 대 한 요청을 차단 됩니다.

```c#
[HttpPost]
[ValidateAntiForgeryToken]
public async Task<IActionResult> RemoveLogin(RemoveLoginViewModel account)
{
    ManageMessageId? message = ManageMessageId.Error;
    var user = await GetCurrentUserAsync();
    if (user != null)
    {
        var result = await _userManager.RemoveLoginAsync(user, account.LoginProvider, account.ProviderKey);
        if (result.Succeeded)
        {
            await _signInManager.SignInAsync(user, isPersistent: false);
            message = ManageMessageId.RemoveLoginSuccess;
        }
    }
    return RedirectToAction(nameof(ManageLogins), new { Message = message });
}
```

``ValidateAntiForgeryToken`` 특성 포함 하 여 데코레이팅되 작업 메서드에 요청에 대 한 토큰 요구 `HTTP GET` 요청 합니다. 광범위 하 게 적용 하는 경우 사용 하 여 재정의할 수 있습니다는 ``IgnoreAntiforgeryToken`` 특성입니다.

### <a name="autovalidateantiforgerytoken"></a>AutoValidateAntiforgeryToken

일반적으로 ASP.NET Core 앱은 HTTP 안전 메서드 (GET, HEAD, 옵션 및 추적)에 대 한 antiforgery 토큰을 생성 하지 않습니다. 광범위 하 게 적용 하는 대신는 ``ValidateAntiForgeryToken`` 특성과 다음 사용 하 여 재정의 ``IgnoreAntiforgeryToken`` 사용할 수 있습니다 특성은 ``AutoValidateAntiforgeryToken`` 특성입니다. 이 특성은 동일 하 게 작동는 ``ValidateAntiForgeryToken`` 특성을 제외 하 고 토큰에 대 한 다음과 같은 HTTP 메서드를 사용 하 여 요청 필요 하지 않습니다.

* 가져오기
* HEAD
* 옵션
* TRACE

사용 하는 것이 좋습니다 ``AutoValidateAntiforgeryToken`` 비 API 시나리오에 대 한 광범위 하 게 합니다. 이렇게 하면 기본적으로 POST 작업 보호 됩니다. 대신 사용 하는 표시 되지 않으면 기본적으로 antiforgery 토큰을 무시 하도록 ``ValidateAntiForgeryToken`` 개별 작업 메서드에 적용 합니다. 되도록 POST 작업 메서드에 대 한이 시나리오에서 더 많이 왼쪽 보호 되지 CSRF 공격에 취약 한 응용 프로그램을 종료 합니다. 도 익명 게시물 antiforgery 토큰을 보내야 합니다.

참고: Api 토큰;의 쿠키 일부로 보내기 위한 자동 메커니즘 없는 구현은 클라이언트 코드 구현에 따라 가능성이 높습니다. 몇 가지 예는 다음과 같습니다.


예 (클래스 수준):

```c#
[Authorize]
[AutoValidateAntiforgeryToken]
public class ManageController : Controller
{
```

예 (전역):

```c#
services.AddMvc(options => 
    options.Filters.Add(new AutoValidateAntiforgeryTokenAttribute()));
```

<a name="iaft"></a>

### <a name="ignoreantiforgerytoken"></a>IgnoreAntiforgeryToken

``IgnoreAntiforgeryToken`` 필터는 지정 된 작업 (또는 컨트롤러)를 사용할 수는 antiforgery 토큰에 대 한 필요성을 제거 하는 데 사용 됩니다. 이 필터는 재정의 적용 하면 ``ValidateAntiForgeryToken`` 및/또는 ``AutoValidateAntiforgeryToken`` (전역적으로 또는 컨트롤러)는 더 높은 수준에서 지정 된 필터입니다.

```c#
[Authorize]
[AutoValidateAntiforgeryToken]
public class ManageController : Controller
{
  [HttpPost]
  [IgnoreAntiforgeryToken]
  public async Task<IActionResult> DoSomethingSafe(SomeViewModel model)
  {
    // no antiforgery token required
  }
}
```

## <a name="javascript-ajax-and-spas"></a>JavaScript, AJAX 및 SPAs

기존의 HTML 기반 응용 프로그램, antiforgery 토큰 숨겨진된 양식 필드를 사용 하 여 서버에 전달 됩니다. 최신 JavaScript 기반 응용 프로그램 및 단일 페이지 응용 프로그램 (SPAs)에서 많은 요청이 프로그래밍 방식으로 이루어집니다. 이러한 AJAX 요청 토큰 다른 기술 (예: 요청 헤더 또는 쿠키)를 사용할 수 있습니다. 인증 토큰을 저장 하 고 서버에 대 한 API 요청을 인증 쿠키를 사용 하는 경우에 CSRF 잠재적인 문제 수 됩니다. 그러나 로컬 저장소를 사용 하 여 토큰을 저장, CSRF 취약점 완화할 수 있습니다, 이후 모든 새 요청을 사용 하 여 서버에 로컬 저장소의 값을 자동으로 보내지지 않습니다. 따라서 요청 헤더는 권장된 방법으로 토큰을 보내는 클라이언트에 antiforgery 토큰을 저장할 로컬 저장소를 사용 합니다.

### <a name="angularjs"></a>AngularJS

AngularJS는 CSRF 주소로 규칙을 사용합니다. 서버 이름으로 쿠키를 전송 하는 경우 ``XSRF-TOKEN``는 각 ``$http`` 서비스에서 값에서이 쿠키를 추가 헤더는이 서버에 요청을 보낼 때. 이 프로세스는 자동; 헤더를 명시적으로 설정할 필요가 없습니다. 헤더 이름이 ``X-XSRF-TOKEN``합니다. 서버는이 헤더를 검색 하 고 해당 내용의 유효성을 검사 해야 합니다.

ASP.NET Core API에 대 한이 규칙을 통해 작동 합니다.

* 호출 하는 쿠키에 토큰을 제공할 앱 구성``XSRF-TOKEN``
* 라는 헤더 찾으려는 antiforgery 서비스 구성``X-XSRF-TOKEN``

```c#
services.AddAntiforgery(options => options.HeaderName = "X-XSRF-TOKEN");
```

[보기 샘플](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/anti-request-forgery/sample/AngularSample)합니다.

### <a name="javascript"></a>JavaScript

JavaScript에서 뷰를 사용 하 여 보기 내에서 서비스를 사용 하 여 토큰을 만들 수 있습니다. 이 위해 삽입는 `Microsoft.AspNetCore.Antiforgery.IAntiforgery` 보기 및 호출 계층에 서비스 `GetAndStoreTokens`표시 된 것 처럼:

[!code-csharp[Main](anti-request-forgery/sample/MvcSample/Views/Home/Ajax.cshtml?highlight=4-10,24)]

이 방법은 서버에서 쿠키를 설정 하거나 클라이언트에서 읽기를 직접 사용 않아도 됩니다.

JavaScript 쿠키, 제공 된 토큰에 액세스할 수도 아래와 같이 쿠키의 내용이 토큰의 값을 가진 헤더를 만드는 데 사용할 수 있으며

```c#
context.Response.Cookies.Append("CSRF-TOKEN", tokens.RequestToken, 
  new Microsoft.AspNetCore.Http.CookieOptions { HttpOnly = false });
```

그런 다음 스크립트를 작성 하는 것으로 가정 요청 헤더에 토큰을 보냅니다 ``X-CSRF-TOKEN``, antiforgery 서비스를 찾도록 구성 된 ``X-CSRF-TOKEN`` 헤더:

```c#
services.AddAntiforgery(options => options.HeaderName = "X-CSRF-TOKEN");
```

다음 예제에서는 jQuery를 사용 하 여 적절 한 헤더를 사용 하는 AJAX 요청 확인.

```javascript
var csrfToken = $.cookie("CSRF-TOKEN");

$.ajax({
    url: "/api/password/changepassword",
    contentType: "application/json",
    data: JSON.stringify({ "newPassword": "ReallySecurePassword999$$$" }),
    type: "POST",
    headers: {
        "X-CSRF-TOKEN": csrfToken
    }
});
```

## <a name="configuring-antiforgery"></a>Antiforgery 구성

`IAntiforgery`antiforgery 시스템을 구성 하는 API를 제공 합니다. 서비스를 요청할 수 있습니다는 `Configure` 의 메서드는 `Startup` 클래스입니다. 다음 예제에서는 응용 프로그램의 홈 페이지에서 미들웨어를 사용 하 여 antiforgery 토큰을 생성 하 고 쿠키 (기본 각도 명명 규칙 위에서 설명한 사용)으로 응답에 보냅니다.


```c#
public void Configure(IApplicationBuilder app, 
    IAntiforgery antiforgery)
{
    app.Use(next => context =>
    {
        string path = context.Request.Path.Value;
        if (
            string.Equals(path, "/", StringComparison.OrdinalIgnoreCase) ||
            string.Equals(path, "/index.html", StringComparison.OrdinalIgnoreCase))
        {
            // We can send the request token as a JavaScript-readable cookie, 
            // and Angular will use it by default.
            var tokens = antiforgery.GetAndStoreTokens(context);
            context.Response.Cookies.Append("XSRF-TOKEN", tokens.RequestToken, 
                new CookieOptions() { HttpOnly = false });
        }

        return next(context);
    });
    //
}
```

### <a name="options"></a>옵션

사용자 지정할 수 있습니다 [antiforgery 옵션](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions#fields_summary) 에 `ConfigureServices`:

```c#
services.AddAntiforgery(options => 
{
  options.CookieDomain = "mydomain.com";
  options.CookieName = "X-CSRF-TOKEN-COOKIENAME";
  options.CookiePath = "Path";
  options.FormFieldName = "AntiforgeryFieldname";
  options.HeaderName = "X-CSRF-TOKEN-HEADERNAME";
  options.RequireSsl = false;
  options.SuppressXFrameOptionsHeader = false;
});
```

<!-- QAfix fix table -->

|옵션        | 설명 |
|------------- | ----------- |
|CookieDomain  | 쿠키의 도메인입니다. 기본값은 `null`입니다. |
|CookieName    | 쿠키의 이름입니다. 을 설정 하지는 시스템 생성 됩니다로 시작 하는 고유한 이름을 `DefaultCookiePrefix` (". AspNetCore.Antiforgery입니다. ")입니다. |
|CookiePath    | 쿠키에 설정 된 경로입니다. |
|FormFieldName | 뷰에서 antiforgery 토큰을 렌더링 하는 antiforgery 시스템에서 사용 하는 숨겨진된 폼 필드의 이름입니다. |
|HeaderName    | Antiforgery 시스템에서 사용 하는 헤더의 이름입니다. 경우 `null`, 시스템은만 양식 데이터를 고려 합니다. |
|RequireSsl    | Antiforgery 시스템에서 SSL이 필요한 지 여부를 지정 합니다. 기본값은 `false`입니다. 경우 `true`, 비 SSL 요청이 실패 합니다. |
|SuppressXFrameOptionsHeader  | 생성을 표시 하지 않을 것인지를 지정 된 `X-Frame-Options` 헤더입니다. 기본적으로 머리글은 "SAMEORIGIN"의 값으로 생성 됩니다. 기본값은 `false`입니다. |

Https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.cookieauthenticationoptions 대 한 자세한 정보를 참조 하십시오.

### <a name="extending-antiforgery"></a>Antiforgery 확장

[IAntiForgeryAdditionalDataProvider](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider) 형식에서는 개발자가 각 토큰의 추가 데이터를 왕복 하 여 ANTI-XSRF 시스템의 동작을 확장할 수 있습니다. [GetAdditionalData](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider#Microsoft_AspNetCore_Antiforgery_IAntiforgeryAdditionalDataProvider_GetAdditionalData_Microsoft_AspNetCore_Http_HttpContext_) 될 때마다 메서드는 필드 토큰이 생성 되 고 반환 값은 생성 되는 토큰 내에 포함 되어 있습니다. 구현 자가 수 타임 스탬프, nonce, 또는 다른 모든 값을 반환 하 고 호출 [ValidateAdditionalData](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider#Microsoft_AspNetCore_Antiforgery_IAntiforgeryAdditionalDataProvider_ValidateAdditionalData_Microsoft_AspNetCore_Http_HttpContext_System_String_) 토큰의 유효성을 검사할 때이 데이터를 유효성 검사 합니다. 클라이언트의 사용자 이름은 이미 생성 된 토큰에 포함 되어 있으므로이 정보를 포함할 필요가 없습니다. 아니지만 추가 데이터 토큰을 포함 하는 경우 `IAntiForgeryAdditionalDataProvider` 된 구성, 추가 데이터 유효성이 검사 되지 않습니다.

## <a name="fundamentals"></a>기본 사항

CSRF 공격 도메인과 해당 도메인에 대 한 모든 요청 연관 된 쿠키를 보낼 기본 브라우저 동작에 의존 합니다. 이러한 쿠키는 브라우저 내에서 저장 됩니다. 인증 된 사용자에 대 한 세션 쿠키가 포함 경우가 많습니다. 쿠키 기반 인증에는 인기 있는 형태의 인증입니다. SPAs 및 기타 "스마트 클라이언트" 시나리오에 특히 토큰 기반 인증 시스템에서 인기, 증가 하 되었습니다.

### <a name="cookie-based-authentication"></a>쿠키 기반 인증

자신의 사용자 이름과 암호를 사용 하 여 사용자가 인증 되 면 나오기 식별 하 고 인증 되었는지 유효성을 검사 하는 데 사용할 수 있는 토큰입니다. 토큰이는 클라이언트는 모든 요청을 함께 제공 되는 쿠키 함에 따라 저장 됩니다. 생성 하 고이 쿠키를 유효성 검사 쿠키 인증 미들웨어에서 수행 됩니다. ASP.NET Core 쿠키를 제공 합니다. [미들웨어](../fundamentals/middleware.md) 주 서버를 다시 만들어 암호화 된 쿠키에 사용자 계정 또는 그 반대로 serialize 하 고 그런 다음 이후 요청에서 쿠키를 유효성을 검사에 할당 합니다는 `User` 속성 `HttpContext`.

쿠키를 사용 하면 인증 쿠키가 단순한 폼 인증 티켓에 대 한 컨테이너입니다. 티켓 각 요청과 함께 폼 인증 쿠키의 값으로 전달 되 고 인증된 된 사용자를 식별 하는 서버에서 폼 인증에서 사용 됩니다.

사용자가 시스템에 로그인 할 때 사용자 세션 서버 측에서 만들어지고 데이터베이스나 다른 영구 저장소에 저장 됩니다. 시스템 데이터 저장소에 실제 세션에 연결 되는 세션 키를 생성 하 고 클라이언트 쪽 쿠키로 전송 됩니다. 웹 서버 사용자 권한 부여를 요구 하는 리소스를 요청 하는 언제 든 지가 세션 키를 확인 합니다. 시스템 연결 된 사용자 세션에 요청된 된 리소스에 액세스할 수 있는 권한이 있는지 여부를 확인 합니다. 이 경우 요청은 계속 됩니다. 그렇지 않으면, 요청이 인증 되지 않았습니다.으로 반환합니다. 이 방법에서는 상태 저장 것 처럼 응용 프로그램을 쿠키를 사용, 사용자가 서버와 이전에 인증 하 고 "기억" 하는 수 있기 때문입니다.

### <a name="user-tokens"></a>사용자 토큰

토큰 기반 인증 서버에 세션을 저장 하지 않습니다. 대신, 사용자가 로그인 할 때 나오기 (하지 antiforgery 토큰) 토큰입니다. 이 토큰은 토큰의 유효성을 검사 하는 데 필요한 모든 데이터를 보유 합니다. 또한 형식으로 사용자 정보를 포함 [클레임](https://msdn.microsoft.com/library/ff359101.aspx)합니다. 사용자가 인증을 요구 하는 서버 리소스에 액세스 하려고 하는 경우 토큰이 전달자 {토큰}의 양식에서 추가 인증 헤더를 사용 하 여 서버에 전송 됩니다. 이렇게 하면 응용 프로그램 상태 비저장 각 후속 요청에 토큰은 전달 요청에 서버 쪽 유효성 검사에 대 한 이후 있습니다. 이 토큰은 하지 *암호화 된*; 보다는 *인코딩된*합니다. 서버 쪽에서 토큰 내에서 원시 정보에 액세스 하려면 토큰을 디코딩할 수 있습니다. 토큰의 후속 요청을 보내려면 저장할 수 있습니다 하거나 브라우저의 로컬 저장소에 또는 쿠키에 있습니다. 토큰의 로컬 저장소에 저장 되어 있지만 토큰 쿠키에 저장 되는 경우 문제가 발생 하는 경우 XSRF 취약점에 걱정할 필요가 없습니다.

### <a name="multiple-applications-are-hosted-in-one-domain"></a>여러 응용 프로그램 도메인에서 호스팅됩니다.

경우에 `example1.cloudapp.net` 및 `example2.cloudapp.net` 는 서로 다른 호스트의 모든 호스트 간의 암시적 트러스트 관계가 있는 `*.cloudapp.net` 도메인입니다. 이 암시적 트러스트 관계에는 신뢰할 수 없는 호스트를 (AJAX 요청을 제어 하는 동일 원본 정책 반드시에 적용 되지 않습니다 HTTP 쿠키) 다른 사용자의 쿠키에 영향을 줄 수 있습니다. ASP.NET Core 런타임 사용자 이름 필드 토큰에 포함 되어 있으므로 악의적인 하위 도메인은 세션 토큰을 덮어쓸 수 있는 경우에 됩니다 하지 사용자에 대 한 유효한 필드 토큰을 생성할 수 있다는 점에서 몇 가지 완화를 제공 합니다. 그러나 이러한 환경에서 호스팅되는 경우 기본 제공 ANTI-XSRF 루틴 여전히 없습니다 공격을 방어할 세션 하이재킹 또는 CSRF 로그인 합니다. 공유 호스팅 환경 세션 하이재킹, CSRF, 로그인 및 기타 공격 vunerable 됩니다.


### <a name="additional-resources"></a>추가 리소스

* [XSRF](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) 에 [웹 응용 프로그램 보안 프로젝트를 열고](https://www.owasp.org/index.php/Main_Page) (OWASP).
