---
title: "ASP.NET Core Identity 없이 쿠키 인증을 사용 하 여"
author: rick-anderson
description: "ASP.NET Core Id 없이 쿠키 인증을 사용 하 여 설명"
keywords: "ASP.NET Core, 쿠키"
ms.author: riande
manager: wpickett
ms.date: 08/14/2017
ms.topic: article
ms.assetid: 2bdcbf95-8d9d-4537-a4a0-a5ee439dcb62
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/authentication/cookie
ms.openlocfilehash: e5c53a7044edb56e065b2dc1536343fdaf9fb007
ms.sourcegitcommit: 7d8f4e3443a2989a64343f8fec83e6a4c4ed2f97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2017
---
# <a name="using-cookie-authentication-without-aspnet-core-identity"></a>ASP.NET Core Identity 없이 쿠키 인증을 사용 하 여

<a name="security-authentication-cookie-middleware"></a>

ASP.NET Core 1.x 쿠키를 제공 합니다. [미들웨어](../../fundamentals/middleware.md#fundamentals-middleware) 주 서버를 다시 만들어 암호화 된 쿠키에 사용자 계정 또는 그 반대로 serialize 하 고 그런 다음 이후 요청에서 쿠키를 유효성을 검사에 할당 합니다는 `HttpContext.User` 속성 . 사용자 고유의 로그인 화면 및 사용자 데이터베이스를 제공 하려는 경우에 독립 실행형 기능으로 쿠키 미들웨어를 사용할 수 있습니다.

크게 변경 된 사항 2.x에서 ASP.NET Core 쿠키 미들웨어 absent 된다는 점입니다. 대신,는 `UseAuthentication` 메서드 호출에는 `Configure` 방식의 *Startup.cs* AuthenticationMiddleware 설정 하는 추가 `HttpContext.User` 속성입니다.

<a name="security-authentication-cookie-middleware-configuring"></a>

## <a name="adding-and-configuring"></a>추가 및 구성

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

다음 단계를 완료 합니다.

- 사용 하지 않는 경우는 `Microsoft.AspNetCore.All` [metapackage](xref:fundamentals/metapackage), 버전 2.0 +의 설치는 `Microsoft.AspNetCore.Authentication.Cookies` 프로젝트에서 NuGet 패키지 합니다.

- 호출 된 `UseAuthentication` 에서 메서드는 `Configure` 의 메서드는 *Startup.cs* 파일:

    ```csharp
    app.UseAuthentication();
    ```

- 호출 된 `AddAuthentication` 및 `AddCookie` 의 메서드는 `ConfigureServices` 의 메서드는 *Startup.cs* 파일:

    ```csharp
    services.AddAuthentication("MyCookieAuthenticationScheme")
            .AddCookie("MyCookieAuthenticationScheme", options => {
                options.AccessDeniedPath = "/Account/Forbidden/";
                options.LoginPath = "/Account/Unauthorized/";
            });
    ```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

다음 단계를 완료 합니다.

- 설치는 `Microsoft.AspNetCore.Authentication.Cookies` 프로젝트에서 NuGet 패키지 합니다. 이 패키지는 쿠키 미들웨어를 포함 합니다.

- 다음 줄을 추가 하는 `Configure` 에서 메서드 프로그램 *Startup.cs* 하기 전에 파일의 `app.UseMvc()` 문:

    ```csharp
    app.UseCookieAuthentication(new CookieAuthenticationOptions()
    {
        AccessDeniedPath = "/Account/Forbidden/",
        AuthenticationScheme = "MyCookieAuthenticationScheme",
        AutomaticAuthenticate = true,
        AutomaticChallenge = true,
        LoginPath = "/Account/Unauthorized/"
    });
    ```

---

위의 코드 조각에서 다음 옵션의 일부나 전부를 구성합니다.

* `AccessDeniedPath`-이 사용자 리소스에 액세스 하려고 시도 하지만 통과 하지 못하는 경우 요청 리디렉션하는 상대 경로 [권한 부여 정책](xref:security/authorization/policies#security-authorization-policies-based) 해당 리소스에 대 한 합니다.

* `AuthenticationScheme`-특정 쿠키 인증 체계를 식별 하는 값입니다. 쿠키 인증의 여러 인스턴스가 있고 하려는 경우 유용 [인스턴스 하나에 대 한 권한 부여 제한](xref:security/authorization/limitingidentitybyscheme#security-authorization-limiting-by-scheme)합니다.

* `AutomaticAuthenticate`-이 플래그는 ASP.NET Core에 대해서만 관련이 1.x 합니다. 쿠키 인증 모든 요청에 대해 실행 및 유효성 검사 하 고 자신이 만든 직렬화 된 모든 보안 주체를 다시 생성을 나타냅니다.

* `AutomaticChallenge`-이 플래그는 ASP.NET Core에 대해서만 관련이 1.x 합니다. 1.x 쿠키 인증 하도록 브라우저를 리디렉션할 해야 나타냅니다는 `LoginPath` 또는 `AccessDeniedPath` 권한 부여에 실패 한 경우.

* `LoginPath`-사용자 리소스에 액세스 하려고 시도 하지만 인증 되지 않은 경우 요청 리디렉션하는 상대 경로입니다.

[다른 옵션](xref:security/authentication/cookie#security-authentication-cookie-options) 쿠키 인증을 만듭니다. 클레임의 발급자를 설정 하는 기능이 포함 된 인증 쿠키의 이름을 삭제, 쿠키 및 쿠키에 다양 한 보안 속성에 대 한 도메인입니다. 기본적으로 쿠키 인증 같은 작성 된 쿠키에 대해 적절 한 보안 옵션을 사용 합니다.
- 클라이언트 쪽 JavaScript에서 쿠키 액세스를 방지 하기 위해 HttpOnly 플래그를 설정.
- HTTPS를 통해 요청을 이동한 경우 쿠키를 HTTPS로 제한

<a name="security-authentication-cookie-middleware-creating-a-cookie"></a>

## <a name="creating-an-identity-cookie"></a>Id 쿠키 만들기

사용자 정보를 보관 하는 쿠키를 만들려면 먼저 생성 해야는 [ClaimsPrincipal](https://docs.microsoft.com/dotnet/api/system.security.claims.claimsprincipal) 쿠키에 serialize 하려는 정보를 보관 합니다. 적절 한 있으면 `ClaimsPrincipal` 개체, 다음 호출 컨트롤러 메서드 내:

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

```csharp
await HttpContext.SignInAsync("MyCookieAuthenticationScheme", principal);
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

```csharp
await HttpContext.Authentication.SignInAsync("MyCookieAuthenticationScheme", principal);
```

---

이 암호화 된 쿠키를 만들어 현재 응답에 추가 합니다. `AuthenticationScheme` 하는 동안 지정 된 [구성](xref:security/authentication/cookie#security-authentication-cookie-middleware-configuring) 를 호출할 때 사용 해야 `SignInAsync`합니다.

내부적으로 사용 되는 암호화는 ASP.NET Core [데이터 보호](xref:security/data-protection/using-data-protection#security-data-protection-getting-started) 시스템입니다. 부하 분산 또는 웹 팜을 사용 하 여 여러 컴퓨터에서 호스팅하는 경우 해야 [데이터 보호를 구성](xref:security/data-protection/configuration/overview#data-protection-configuring) 동일한 키 링과 응용 프로그램 식별자를 사용 하도록 합니다.

## <a name="signing-out"></a>로그 아웃

로그 아웃 현재 사용자를 해당 쿠키를 삭제 하려면 다음을 호출 컨트롤러 내:

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

```csharp
await HttpContext.SignOutAsync("MyCookieAuthenticationScheme");
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

```csharp
await HttpContext.Authentication.SignOutAsync("MyCookieAuthenticationScheme");
```

---

## <a name="reacting-to-back-end-changes"></a>백 엔드 변경 내용에 응답

>[!WARNING]
> 사용자 쿠키를 만든 후에 id의 단일 원본이 됩니다. 백 엔드 시스템에 사용자를 비활성화 하는 경우에 쿠키 인증에이 알지 및 해당 쿠키의 유효으로 사용자 로그인 유지 합니다.

쿠키 인증 옵션 클래스에서는 이벤트를 제공합니다. `ValidateAsync()` 이벤트 가로채 고 쿠키 id의 유효성 검사 재정의를 사용할 수 있습니다.

"LastChanged" 열을 가질 수 있는 백 엔드 사용자 데이터베이스가 있다고 가정 합니다. 데이터베이스 변경 시 쿠키를 무효화 하기 위해 수행 해야 먼저 때 [쿠키를 만드는](xref:security/authentication/cookie#security-authentication-cookie-middleware-creating-a-cookie), 현재 값이 포함 된 "LastChanged" 클레임을 추가 합니다. 데이터베이스 변경 시 "LastChanged" 값을 업데이트 해야 합니다.

에 대 한 재정의 구현 하는 `ValidateAsync()` 이벤트에는 다음 서명이 있는 메서드를 작성 해야 합니다.

```csharp
Task ValidateAsync(CookieValidatePrincipalContext context);
```

ASP.NET Core Id의 일부로이 검사를 구현 하는 `SecurityStampValidator`합니다. 예제는 다음과 같습니다.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

```csharp
public static class LastChangedValidator
{
    public static async Task ValidateAsync(CookieValidatePrincipalContext context)
    {
        // Pull database from registered DI services.
        var userRepository = context.HttpContext.RequestServices.GetRequiredService<IUserRepository>();
        var userPrincipal = context.Principal;

        // Look for the last changed claim.
        string lastChanged;
        lastChanged = (from c in userPrincipal.Claims
                        where c.Type == "LastUpdated"
                        select c.Value).FirstOrDefault();

        if (string.IsNullOrEmpty(lastChanged) ||
            !userRepository.ValidateLastChanged(userPrincipal, lastChanged))
        {
            context.RejectPrincipal();
            await context.HttpContext.SignOutAsync("MyCookieAuthenticationScheme");
        }
    }
}
```

이 다음 연결 될를 서비스에 등록 하는 쿠키 동안는 `ConfigureServices` 방식의 *Startup.cs*:

```csharp
services.AddAuthentication("MyCookieAuthenticationScheme")
        .AddCookie("MyCookieAuthenticationScheme", options =>
        {
            options.Events = new CookieAuthenticationEvents
            {
                OnValidatePrincipal = LastChangedValidator.ValidateAsync
            };
        });
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

```csharp
public static class LastChangedValidator
{
    public static async Task ValidateAsync(CookieValidatePrincipalContext context)
    {
        // Pull database from registered DI services.
        var userRepository = context.HttpContext.RequestServices.GetRequiredService<IUserRepository>();
        var userPrincipal = context.Principal;

        // Look for the last changed claim.
        string lastChanged;
        lastChanged = (from c in userPrincipal.Claims
                        where c.Type == "LastUpdated"
                        select c.Value).FirstOrDefault();

        if (string.IsNullOrEmpty(lastChanged) ||
            !userRepository.ValidateLastChanged(userPrincipal, lastChanged))
        {
            context.RejectPrincipal();
            await context.HttpContext.Authentication.SignOutAsync("MyCookieAuthenticationScheme");
        }
    }
}
```

이 다음 연결을 쿠키 인증 구성 작업에 `Configure` 방식의 *Startup.cs*:

```csharp
app.UseCookieAuthentication(new CookieAuthenticationOptions
{
    Events = new CookieAuthenticationEvents
    {
        OnValidatePrincipal = LastChangedValidator.ValidateAsync
    }
});
```

---

해당 이름을 업데이트 한 예를 들어 &mdash; 어떤 방식으로든에서 보안에 영향을 주지는 의사 결정 합니다. 안전 하 게 사용자 계정이 업데이트 하려는 경우 호출할 수 있습니다 `context.ReplacePrincipal()` 설정 하 고는 `context.ShouldRenew` 속성을 `true`합니다.

<a name="security-authentication-cookie-options"></a>

## <a name="controlling-cookie-options"></a>쿠키 옵션 제어

[CookieAuthenticationOptions](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.cookieauthenticationoptions) 만들려는 쿠키를 미세 조정할 수 있는 다양 한 구성 옵션 함께 클래스를 제공 합니다.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

ASP.NET Core 2.x 쿠키를 구성 하는 데 사용 되는 Api를 통합 합니다. Api 사용 되지 않는 것으로 표시 된 1.x와 새 `Cookie` 형식의 속성이 `CookieBuilder` 에서 도입 된는 `CookieAuthenticationOptions` 클래스입니다. Api 2.x를 마이그레이션하는 것이 좋습니다.

* `ClaimsIssuer`에 사용할 발급자는 [발급자](https://docs.microsoft.com/dotnet/api/system.security.claims.claim.issuer) 쿠키 인증에서 만든 클레임의 속성입니다.

* `CookieBuilder.Domain`쿠키 처리 되는 도메인 이름이입니다. 기본적으로에 요청을 보냈습니다. 호스트 이름입니다. 브라우저 쿠키를 일치 하는 호스트 이름으로 처리합니다. 이 도메인의 모든 호스트에 사용할 수 있는 쿠키를 조정할 수도 있습니다. 예를 들어 쿠키 도메인 설정 `.contoso.com` 를 사용할 수 있도록 `contoso.com`, `www.contoso.com`, `staging.www.contoso.com`등입니다.

* `CookieBuilder.HttpOnly`쿠키를 서버에만 액세스할 수 있어야 하는 경우 나타내는 플래그입니다. 기본값은 `true`합니다. 이 값을 변경 하며 열림 쿠키 도용에 응용 프로그램을 응용 프로그램 사이트 간 스크립팅 버그 있어야 합니다.

* `CookieBuilder.Path`동일한 호스트 이름에서 실행 되는 응용 프로그램을 격리 사용할 수 있습니다. 실행 중인 앱이 있는 경우 `/app1` 해당 응용 프로그램에 전송에 발급 된 쿠키를 제한 하려면 설정 해야 합니다는 `CookiePath` 속성을 `/app1`합니다. 이러한 작업을 통해 쿠키 에서만 사용 가능 하는 요청에 `/app1` 또는 그 아래에 있는 항목입니다.

* `CookieBuilder.SameSite`브라우저에서 같은 사이트 또는 사이트 간 요청에 첨부할 쿠키를 허용 하는지 여부를 나타냅니다. 기본값은 `SameSiteMode.Lax`합니다.

* `CookieBuilder.SecurePolicy`만든 쿠키를 HTTPS, HTTP 또는 HTTPS 또는 요청와 동일한 프로토콜 제한 해야 하는 경우 나타내는 플래그입니다. 기본값은 `SameAsRequest`합니다.

* `ExpireTimeSpan`이 `TimeSpan` 쿠키가 만료 된 이후입니다. 쿠키의 만료 날짜를 만들 현재 날짜 및 시간에 추가 됩니다.

* `SlidingExpiration`이상의 절반 쿠키 만료 날짜를 다시 설정 하는지 여부를 나타내는 플래그는 `ExpireTimeSpan` 관리가 합니다. 이 새 만료 날짜가 현재 날짜를 앞으로 이동와 `ExpireTimespan`합니다. [절대 만료 시간](xref:security/authentication/cookie#security-authentication-absolute-expiry) 를 사용 하 여 설정할 수는 `AuthenticationProperties` 클래스를 호출할 때 `SignInAsync`합니다. 절대 만료 인증 쿠키의 유효 시간을 제한 하 여 응용 프로그램의 보안을 향상 시킬 수 있습니다.

사용 하는 예제 `CookieAuthenticationOptions` 에 `ConfigureServices` 방식의 *Startup.cs* 뒤에 오는:

```csharp
services.AddAuthentication("MyCookieAuthenticationScheme")
        .AddCookie("MyCookieAuthenticationScheme", options =>
        {
            options.Cookie.Name = "AuthCookie";
            options.Cookie.Domain = "contoso.com";
            options.Cookie.Path = "/";
            options.Cookie.HttpOnly = true;
            options.Cookie.SameSite = SameSiteMode.Lax;
            options.Cookie.SecurePolicy = CookieSecurePolicy.Always;
        });
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

* `ClaimsIssuer`에 사용할 발급자는 [발급자](https://docs.microsoft.com/dotnet/api/system.security.claims.claim.issuer) 미들웨어에서 만든 클레임의 속성입니다.

* `CookieDomain`쿠키 처리 되는 도메인 이름이입니다. 기본적으로에 요청을 보냈습니다. 호스트 이름입니다. 브라우저 쿠키를 일치 하는 호스트 이름으로 처리합니다. 이 도메인의 모든 호스트에 사용할 수 있는 쿠키를 조정할 수도 있습니다. 예를 들어 쿠키 도메인 설정 `.contoso.com` 를 사용할 수 있도록 `contoso.com`, `www.contoso.com`, `staging.www.contoso.com`등입니다.

* `CookieHttpOnly`쿠키를 서버에만 액세스할 수 있어야 하는 경우 나타내는 플래그입니다. 기본값은 `true`합니다. 이 값을 변경 하며 열림 쿠키 도용에 응용 프로그램을 응용 프로그램 사이트 간 스크립팅 버그 있어야 합니다.

* `CookiePath`동일한 호스트 이름에서 실행 되는 응용 프로그램을 격리 사용할 수 있습니다. 실행 중인 앱이 있는 경우 `/app1` 해당 응용 프로그램에 전송에 발급 된 쿠키를 제한 하려면 설정 해야 합니다는 `CookiePath` 속성을 `/app1`합니다. 이러한 작업을 통해 쿠키 에서만 사용 가능 하는 요청에 `/app1` 또는 그 아래에 있는 항목입니다.

* `CookieSecure`만든 쿠키를 HTTPS, HTTP 또는 HTTPS 또는 요청와 동일한 프로토콜 제한 해야 하는 경우 나타내는 플래그입니다. 기본값은 `SameAsRequest`합니다.

* `ExpireTimeSpan`이 `TimeSpan` 쿠키가 만료 된 이후입니다. 쿠키의 만료 날짜를 만들 현재 날짜 및 시간에 추가 됩니다.

* `SlidingExpiration`이상의 절반 쿠키 만료 날짜를 다시 설정 하는지 여부를 나타내는 플래그는 `ExpireTimeSpan` 관리가 합니다. 이 새 만료 날짜가 현재 날짜를 앞으로 이동와 `ExpireTimespan`합니다. [절대 만료 시간](xref:security/authentication/cookie#security-authentication-absolute-expiry) 를 사용 하 여 설정할 수는 `AuthenticationProperties` 클래스를 호출할 때 `SignInAsync`합니다. 절대 만료 인증 쿠키의 유효 시간을 제한 하 여 응용 프로그램의 보안을 향상 시킬 수 있습니다.

사용 하는 예제 `CookieAuthenticationOptions` 에 `Configure` 방식의 *Startup.cs* 뒤에 오는:

```csharp
app.UseCookieAuthentication(new CookieAuthenticationOptions
{
    CookieName = "AuthCookie",
    CookieDomain = "contoso.com",
    CookiePath = "/",
    CookieHttpOnly = true,
    CookieSecure = CookieSecurePolicy.Always
});
```

---

## <a name="persistent-cookies-and-absolute-expiry-times"></a>영구 쿠키 및 절대 만료 시간

브라우저 세션에서 유지 하는 id 및 쿠키를 전송 하는 것에 절대 만료 원하는 쿠키 만료를 할 수 있습니다. 이 지 속성에 로그인 또는 유사한 메커니즘에서 "암호 저장" 확인란을 통해 명시적 사용자 동의가 있는 경우에 설정 해야 합니다. 사용 하 여 이러한 작업을 할 수 있습니다는 `AuthenticationProperties` 에 매개 변수는 `SignInAsync` 때이 메서드가 호출 [id의 서명 및 쿠키를 만드는](xref:security/authentication/cookie#security-authentication-cookie-middleware-creating-a-cookie)합니다. 예:

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

```csharp
await HttpContext.SignInAsync(
    "MyCookieAuthenticationScheme",
    principal,
    new AuthenticationProperties
    {
        IsPersistent = true
    });
```

`AuthenticationProperties` 에 위치 앞의 코드 조각에 사용 되는 클래스는 `Microsoft.AspNetCore.Authentication` 네임 스페이스입니다.

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

```csharp
await HttpContext.Authentication.SignInAsync(
    "MyCookieAuthenticationScheme",
    principal,
    new AuthenticationProperties
    {
        IsPersistent = true
    });
```

`AuthenticationProperties` 에 위치 앞의 코드 조각에 사용 되는 클래스는 `Microsoft.AspNetCore.Http.Authentication` 네임 스페이스입니다.

---

앞의 코드 조각 id 및 브라우저 클로저를 통해 되더라도 남아 있지만 해당 쿠키를 만듭니다. 슬라이딩 만료 설정을 통해 이전에 구성 된 [쿠키 옵션](xref:security/authentication/cookie#security-authentication-cookie-options) 여전히 인식 됩니다. 쿠키는 브라우저 종료 하는 동안 만료 되 면 다시 시작 되 면 브라우저 지우지 합니다.

<a name="security-authentication-absolute-expiry"></a>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

```csharp
await HttpContext.SignInAsync(
    "MyCookieAuthenticationScheme",
    principal,
    new AuthenticationProperties
    {
        ExpiresUtc = DateTime.UtcNow.AddMinutes(20)
    });
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

```csharp
await HttpContext.Authentication.SignInAsync(
    "MyCookieAuthenticationScheme",
    principal,
    new AuthenticationProperties
    {
        ExpiresUtc = DateTime.UtcNow.AddMinutes(20)
    });
```

---

앞의 코드 조각 id 및 20 분 동안 지속 되는 해당 쿠키를 만듭니다. 슬라이딩 만료 설정을 통해 이전에 구성 된 무시 [쿠키 옵션](xref:security/authentication/cookie#security-authentication-cookie-options)합니다.

`ExpiresUtc` 및 `IsPersistent` 속성은 함께 사용할 수 없습니다.
