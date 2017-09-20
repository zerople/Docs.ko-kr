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
ms.openlocfilehash: b728c3d62b59f28f1d020b6f3732918a1fcdf4eb
ms.sourcegitcommit: 74a8ad9c1ba5c155d7c4303e67632a0922c38e86
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/20/2017
---
# <a name="using-cookie-authentication-without-aspnet-core-identity"></a><span data-ttu-id="cf04c-104">ASP.NET Core Identity 없이 쿠키 인증을 사용 하 여</span><span class="sxs-lookup"><span data-stu-id="cf04c-104">Using Cookie Authentication without ASP.NET Core Identity</span></span>

<a name="security-authentication-cookie-middleware"></a>

<span data-ttu-id="cf04c-105">ASP.NET Core 1.x 쿠키를 제공 합니다. [미들웨어](../../fundamentals/middleware.md#fundamentals-middleware) 주 서버를 다시 만들어 암호화 된 쿠키에 사용자 계정 또는 그 반대로 serialize 하 고 그런 다음 이후 요청에서 쿠키를 유효성을 검사에 할당 합니다는 `HttpContext.User` 속성 .</span><span class="sxs-lookup"><span data-stu-id="cf04c-105">ASP.NET Core 1.x provides cookie [middleware](../../fundamentals/middleware.md#fundamentals-middleware) which serializes a user principal into an encrypted cookie and then, on subsequent requests, validates the cookie, recreates the principal, and assigns it to the `HttpContext.User` property.</span></span> <span data-ttu-id="cf04c-106">사용자 고유의 로그인 화면 및 사용자 데이터베이스를 제공 하려는 경우에 독립 실행형 기능으로 쿠키 미들웨어를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cf04c-106">If you want to provide your own login screens and user databases, you can use the cookie middleware as a standalone feature.</span></span>

<span data-ttu-id="cf04c-107">크게 변경 된 사항 2.x에서 ASP.NET Core 쿠키 미들웨어 absent 된다는 점입니다.</span><span class="sxs-lookup"><span data-stu-id="cf04c-107">A major change in ASP.NET Core 2.x is that the cookie middleware is absent.</span></span> <span data-ttu-id="cf04c-108">대신,는 `UseAuthentication` 메서드 호출에는 `Configure` 방식의 *Startup.cs* AuthenticationMiddleware 설정 하는 추가 `HttpContext.User` 속성입니다.</span><span class="sxs-lookup"><span data-stu-id="cf04c-108">Instead, the `UseAuthentication` method invocation in the `Configure` method of *Startup.cs* adds the AuthenticationMiddleware which sets the `HttpContext.User` property.</span></span>

<a name="security-authentication-cookie-middleware-configuring"></a>

## <a name="adding-and-configuring"></a><span data-ttu-id="cf04c-109">추가 및 구성</span><span class="sxs-lookup"><span data-stu-id="cf04c-109">Adding and configuring</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="cf04c-110">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="cf04c-110">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="cf04c-111">다음 단계를 완료 합니다.</span><span class="sxs-lookup"><span data-stu-id="cf04c-111">Complete the following steps:</span></span>

- <span data-ttu-id="cf04c-112">사용 하지 않는 경우는 `Microsoft.AspNetCore.All` [metapackage](xref:fundamentals/metapackage), 버전 2.0 +의 설치는 `Microsoft.AspNetCore.Authentication.Cookies` 프로젝트에서 NuGet 패키지 합니다.</span><span class="sxs-lookup"><span data-stu-id="cf04c-112">If not using the `Microsoft.AspNetCore.All` [metapackage](xref:fundamentals/metapackage), install version 2.0+ of the `Microsoft.AspNetCore.Authentication.Cookies` NuGet package in your project.</span></span>

- <span data-ttu-id="cf04c-113">호출 된 `UseAuthentication` 에서 메서드는 `Configure` 의 메서드는 *Startup.cs* 파일:</span><span class="sxs-lookup"><span data-stu-id="cf04c-113">Invoke the `UseAuthentication` method in the `Configure` method of the *Startup.cs* file:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="cf04c-114">호출 된 `AddAuthentication` 및 `AddCookie` 의 메서드는 `ConfigureServices` 의 메서드는 *Startup.cs* 파일:</span><span class="sxs-lookup"><span data-stu-id="cf04c-114">Invoke the `AddAuthentication` and `AddCookie` methods in the `ConfigureServices` method of the *Startup.cs* file:</span></span>

    ```csharp
    services.AddAuthentication("MyCookieAuthenticationScheme")
            .AddCookie(options => {
                options.AccessDeniedPath = "/Account/Forbidden/";
                options.LoginPath = "/Account/Unauthorized/";
            });
    ```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="cf04c-115">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="cf04c-115">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="cf04c-116">다음 단계를 완료 합니다.</span><span class="sxs-lookup"><span data-stu-id="cf04c-116">Complete the following steps:</span></span>

- <span data-ttu-id="cf04c-117">설치는 `Microsoft.AspNetCore.Authentication.Cookies` 프로젝트에서 NuGet 패키지 합니다.</span><span class="sxs-lookup"><span data-stu-id="cf04c-117">Install the `Microsoft.AspNetCore.Authentication.Cookies` NuGet package in your project.</span></span> <span data-ttu-id="cf04c-118">이 패키지는 쿠키 미들웨어를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="cf04c-118">This package contains the cookie middleware.</span></span>

- <span data-ttu-id="cf04c-119">다음 줄을 추가 하는 `Configure` 에서 메서드 프로그램 *Startup.cs* 하기 전에 파일의 `app.UseMvc()` 문:</span><span class="sxs-lookup"><span data-stu-id="cf04c-119">Add the following lines to the `Configure` method in your *Startup.cs* file before the `app.UseMvc()` statement:</span></span>

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

<span data-ttu-id="cf04c-120">위의 코드 조각에서 다음 옵션의 일부나 전부를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="cf04c-120">The code snippets above configure some or all of the following options:</span></span>

* <span data-ttu-id="cf04c-121">`AccessDeniedPath`-이 사용자 리소스에 액세스 하려고 시도 하지만 통과 하지 못하는 경우 요청 리디렉션하는 상대 경로 [권한 부여 정책](xref:security/authorization/policies#security-authorization-policies-based) 해당 리소스에 대 한 합니다.</span><span class="sxs-lookup"><span data-stu-id="cf04c-121">`AccessDeniedPath` - This is the relative path to which requests redirect when a user attempts to access a resource but does not pass any [authorization policies](xref:security/authorization/policies#security-authorization-policies-based) for that resource.</span></span>

* <span data-ttu-id="cf04c-122">`AuthenticationScheme`-특정 쿠키 인증 체계를 식별 하는 값입니다.</span><span class="sxs-lookup"><span data-stu-id="cf04c-122">`AuthenticationScheme` - This is a value by which a particular cookie authentication scheme is known.</span></span> <span data-ttu-id="cf04c-123">쿠키 인증의 여러 인스턴스가 있고 하려는 경우 유용 [인스턴스 하나에 대 한 권한 부여 제한](xref:security/authorization/limitingidentitybyscheme#security-authorization-limiting-by-scheme)합니다.</span><span class="sxs-lookup"><span data-stu-id="cf04c-123">This is useful when there are multiple instances of cookie authentication and you want to [limit authorization to one instance](xref:security/authorization/limitingidentitybyscheme#security-authorization-limiting-by-scheme).</span></span>

* <span data-ttu-id="cf04c-124">`AutomaticAuthenticate`-이 플래그는 ASP.NET Core에 대해서만 관련이 1.x 합니다.</span><span class="sxs-lookup"><span data-stu-id="cf04c-124">`AutomaticAuthenticate` - This flag is relevant only for ASP.NET Core 1.x.</span></span> <span data-ttu-id="cf04c-125">쿠키 인증 모든 요청에 대해 실행 및 유효성 검사 하 고 자신이 만든 직렬화 된 모든 보안 주체를 다시 생성을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="cf04c-125">It indicates that the cookie authentication should run on every request and attempt to validate and reconstruct any serialized principal it created.</span></span>

* <span data-ttu-id="cf04c-126">`AutomaticChallenge`-이 플래그는 ASP.NET Core에 대해서만 관련이 1.x 합니다.</span><span class="sxs-lookup"><span data-stu-id="cf04c-126">`AutomaticChallenge` - This flag is relevant only for ASP.NET Core 1.x.</span></span> <span data-ttu-id="cf04c-127">1.x 쿠키 인증 하도록 브라우저를 리디렉션할 해야 나타냅니다는 `LoginPath` 또는 `AccessDeniedPath` 권한 부여에 실패 한 경우.</span><span class="sxs-lookup"><span data-stu-id="cf04c-127">It indicates that the 1.x cookie authentication should redirect the browser to the `LoginPath` or `AccessDeniedPath` when authorization fails.</span></span>

* <span data-ttu-id="cf04c-128">`LoginPath`-사용자 리소스에 액세스 하려고 시도 하지만 인증 되지 않은 경우 요청 리디렉션하는 상대 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="cf04c-128">`LoginPath` - This is the relative path to which requests redirect when a user attempts to access a resource but has not been authenticated.</span></span>

<span data-ttu-id="cf04c-129">[다른 옵션](xref:security/authentication/cookie#security-authentication-cookie-options) 쿠키 인증을 만듭니다. 클레임의 발급자를 설정 하는 기능이 포함 된 인증 쿠키의 이름을 삭제, 쿠키 및 쿠키에 다양 한 보안 속성에 대 한 도메인입니다.</span><span class="sxs-lookup"><span data-stu-id="cf04c-129">[Other options](xref:security/authentication/cookie#security-authentication-cookie-options) include the ability to set the issuer for any claims the cookie authentication creates, the name of the cookie the authentication drops, the domain for the cookie and various security properties on the cookie.</span></span> <span data-ttu-id="cf04c-130">기본적으로 쿠키 인증 같은 작성 된 쿠키에 대해 적절 한 보안 옵션을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="cf04c-130">By default, the cookie authentication uses appropriate security options for any cookies it creates, such as:</span></span>
- <span data-ttu-id="cf04c-131">클라이언트 쪽 JavaScript에서 쿠키 액세스를 방지 하기 위해 HttpOnly 플래그를 설정.</span><span class="sxs-lookup"><span data-stu-id="cf04c-131">Setting the HttpOnly flag to prevent cookie access in client-side JavaScript</span></span>
- <span data-ttu-id="cf04c-132">HTTPS를 통해 요청을 이동한 경우 쿠키를 HTTPS로 제한</span><span class="sxs-lookup"><span data-stu-id="cf04c-132">Limiting the cookie to HTTPS if a request has traveled over HTTPS</span></span>

<a name="security-authentication-cookie-middleware-creating-a-cookie"></a>

## <a name="creating-an-identity-cookie"></a><span data-ttu-id="cf04c-133">Id 쿠키 만들기</span><span class="sxs-lookup"><span data-stu-id="cf04c-133">Creating an Identity cookie</span></span>

<span data-ttu-id="cf04c-134">사용자 정보를 보관 하는 쿠키를 만들려면 먼저 생성 해야는 [ClaimsPrincipal](https://docs.microsoft.com/dotnet/api/system.security.claims.claimsprincipal) 쿠키에 serialize 하려는 정보를 보관 합니다.</span><span class="sxs-lookup"><span data-stu-id="cf04c-134">To create a cookie holding your user information, you must construct a [ClaimsPrincipal](https://docs.microsoft.com/dotnet/api/system.security.claims.claimsprincipal) holding the information you wish to be serialized in the cookie.</span></span> <span data-ttu-id="cf04c-135">적절 한 있으면 `ClaimsPrincipal` 개체, 다음 호출 컨트롤러 메서드 내:</span><span class="sxs-lookup"><span data-stu-id="cf04c-135">Once you have a suitable `ClaimsPrincipal` object, call the following inside your controller method:</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="cf04c-136">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="cf04c-136">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```csharp
await HttpContext.SignInAsync("MyCookieAuthenticationScheme", principal);
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="cf04c-137">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="cf04c-137">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

```csharp
await HttpContext.Authentication.SignInAsync("MyCookieAuthenticationScheme", principal);
```

---

<span data-ttu-id="cf04c-138">이 암호화 된 쿠키를 만들어 현재 응답에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="cf04c-138">This creates an encrypted cookie and adds it to the current response.</span></span> <span data-ttu-id="cf04c-139">`AuthenticationScheme` 하는 동안 지정 된 [구성](xref:security/authentication/cookie#security-authentication-cookie-middleware-configuring) 를 호출할 때 사용 해야 `SignInAsync`합니다.</span><span class="sxs-lookup"><span data-stu-id="cf04c-139">The `AuthenticationScheme` specified during [configuration](xref:security/authentication/cookie#security-authentication-cookie-middleware-configuring) must be used when calling `SignInAsync`.</span></span>

<span data-ttu-id="cf04c-140">내부적으로 사용 되는 암호화는 ASP.NET Core [데이터 보호](xref:security/data-protection/using-data-protection#security-data-protection-getting-started) 시스템입니다.</span><span class="sxs-lookup"><span data-stu-id="cf04c-140">Under the covers, the encryption used is ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection#security-data-protection-getting-started) system.</span></span> <span data-ttu-id="cf04c-141">부하 분산 또는 웹 팜을 사용 하 여 여러 컴퓨터에서 호스팅하는 경우 해야 [데이터 보호를 구성](xref:security/data-protection/configuration/overview#data-protection-configuring) 동일한 키 링과 응용 프로그램 식별자를 사용 하도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="cf04c-141">If you are hosting on multiple machines, load balancing, or using a web farm, then you need to [configure data protection](xref:security/data-protection/configuration/overview#data-protection-configuring) to use the same key ring and application identifier.</span></span>

## <a name="signing-out"></a><span data-ttu-id="cf04c-142">로그 아웃</span><span class="sxs-lookup"><span data-stu-id="cf04c-142">Signing out</span></span>

<span data-ttu-id="cf04c-143">로그 아웃 현재 사용자를 해당 쿠키를 삭제 하려면 다음을 호출 컨트롤러 내:</span><span class="sxs-lookup"><span data-stu-id="cf04c-143">To sign out the current user and delete their cookie, call the following inside your controller:</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="cf04c-144">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="cf04c-144">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```csharp
await HttpContext.SignOutAsync("MyCookieAuthenticationScheme");
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="cf04c-145">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="cf04c-145">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

```csharp
await HttpContext.Authentication.SignOutAsync("MyCookieAuthenticationScheme");
```

---

## <a name="reacting-to-back-end-changes"></a><span data-ttu-id="cf04c-146">백 엔드 변경 내용에 응답</span><span class="sxs-lookup"><span data-stu-id="cf04c-146">Reacting to back-end changes</span></span>

>[!WARNING]
> <span data-ttu-id="cf04c-147">사용자 쿠키를 만든 후에 id의 단일 원본이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cf04c-147">Once a principal cookie has been created, it becomes the single source of identity.</span></span> <span data-ttu-id="cf04c-148">백 엔드 시스템에 사용자를 비활성화 하는 경우에 쿠키 인증에이 알지 및 해당 쿠키의 유효으로 사용자 로그인 유지 합니다.</span><span class="sxs-lookup"><span data-stu-id="cf04c-148">Even if you disable a user in your back-end systems, the cookie authentication has no knowledge of this, and a user stays logged in as long as their cookie is valid.</span></span>

<span data-ttu-id="cf04c-149">쿠키 인증 옵션 클래스에서는 이벤트를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="cf04c-149">The cookie authentication provides a series of events in its option class.</span></span> <span data-ttu-id="cf04c-150">`ValidateAsync()` 이벤트 가로채 고 쿠키 id의 유효성 검사 재정의를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cf04c-150">The `ValidateAsync()` event can be used to intercept and override validation of the cookie identity.</span></span>

<span data-ttu-id="cf04c-151">"LastChanged" 열을 가질 수 있는 백 엔드 사용자 데이터베이스가 있다고 가정 합니다.</span><span class="sxs-lookup"><span data-stu-id="cf04c-151">Consider a back-end user database that may have a "LastChanged" column.</span></span> <span data-ttu-id="cf04c-152">데이터베이스 변경 시 쿠키를 무효화 하기 위해 수행 해야 먼저 때 [쿠키를 만드는](xref:security/authentication/cookie#security-authentication-cookie-middleware-creating-a-cookie), 현재 값이 포함 된 "LastChanged" 클레임을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="cf04c-152">In order to invalidate a cookie when the database changes, you should first, when [creating the cookie](xref:security/authentication/cookie#security-authentication-cookie-middleware-creating-a-cookie), add a "LastChanged" claim containing the current value.</span></span> <span data-ttu-id="cf04c-153">데이터베이스 변경 시 "LastChanged" 값을 업데이트 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="cf04c-153">When the database changes, the "LastChanged" value should be updated.</span></span>

<span data-ttu-id="cf04c-154">에 대 한 재정의 구현 하는 `ValidateAsync()` 이벤트에는 다음 서명이 있는 메서드를 작성 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="cf04c-154">To implement an override for the `ValidateAsync()` event, you must write a method with the following signature:</span></span>

```csharp
Task ValidateAsync(CookieValidatePrincipalContext context);
```

<span data-ttu-id="cf04c-155">ASP.NET Core Id의 일부로이 검사를 구현 하는 `SecurityStampValidator`합니다.</span><span class="sxs-lookup"><span data-stu-id="cf04c-155">ASP.NET Core Identity implements this check as part of its `SecurityStampValidator`.</span></span> <span data-ttu-id="cf04c-156">예제는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="cf04c-156">An example looks like the following:</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="cf04c-157">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="cf04c-157">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

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

<span data-ttu-id="cf04c-158">이 다음 연결 될를 서비스에 등록 하는 쿠키 동안는 `ConfigureServices` 방식의 *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="cf04c-158">This would then be wired up during cookie service registration in the `ConfigureServices` method of *Startup.cs*:</span></span>

```csharp
services.AddAuthentication("MyCookieAuthenticationScheme")
        .AddCookie(options =>
        {
            options.Events = new CookieAuthenticationEvents
            {
                OnValidatePrincipal = LastChangedValidator.ValidateAsync
            };
        });
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="cf04c-159">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="cf04c-159">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

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

<span data-ttu-id="cf04c-160">이 다음 연결을 쿠키 인증 구성 작업에 `Configure` 방식의 *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="cf04c-160">This would then be wired up during cookie authentication configuration in the `Configure` method of *Startup.cs*:</span></span>

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

<span data-ttu-id="cf04c-161">해당 이름을 업데이트 한 예를 들어 &mdash; 어떤 방식으로든에서 보안에 영향을 주지는 의사 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="cf04c-161">Consider the example in which their name has been updated &mdash; a decision which doesn't affect security in any way.</span></span> <span data-ttu-id="cf04c-162">안전 하 게 사용자 계정이 업데이트 하려는 경우 호출할 수 있습니다 `context.ReplacePrincipal()` 설정 하 고는 `context.ShouldRenew` 속성을 `true`합니다.</span><span class="sxs-lookup"><span data-stu-id="cf04c-162">If you want to non-destructively update the user principal, you can call `context.ReplacePrincipal()` and set the `context.ShouldRenew` property to `true`.</span></span>

<a name="security-authentication-cookie-options"></a>

## <a name="controlling-cookie-options"></a><span data-ttu-id="cf04c-163">쿠키 옵션 제어</span><span class="sxs-lookup"><span data-stu-id="cf04c-163">Controlling cookie options</span></span>

<span data-ttu-id="cf04c-164">[CookieAuthenticationOptions](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.cookieauthenticationoptions) 만들려는 쿠키를 미세 조정할 수 있는 다양 한 구성 옵션 함께 클래스를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="cf04c-164">The [CookieAuthenticationOptions](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.cookieauthenticationoptions) class comes with various configuration options to fine-tune the cookies being created.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="cf04c-165">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="cf04c-165">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="cf04c-166">ASP.NET Core 2.x 쿠키를 구성 하는 데 사용 되는 Api를 통합 합니다.</span><span class="sxs-lookup"><span data-stu-id="cf04c-166">ASP.NET Core 2.x unifies the APIs used for configuring cookies.</span></span> <span data-ttu-id="cf04c-167">Api 사용 되지 않는 것으로 표시 된 1.x와 새 `Cookie` 형식의 속성이 `CookieBuilder` 에서 도입 된는 `CookieAuthenticationOptions` 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="cf04c-167">The 1.x APIs have been marked as obsolete, and a new `Cookie` property of type `CookieBuilder` has been introduced in the `CookieAuthenticationOptions` class.</span></span> <span data-ttu-id="cf04c-168">Api 2.x를 마이그레이션하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="cf04c-168">It's recommended that you migrate to the 2.x APIs.</span></span>

* <span data-ttu-id="cf04c-169">`ClaimsIssuer`에 사용할 발급자는 [발급자](https://docs.microsoft.com/dotnet/api/system.security.claims.claim.issuer) 쿠키 인증에서 만든 클레임의 속성입니다.</span><span class="sxs-lookup"><span data-stu-id="cf04c-169">`ClaimsIssuer` is the issuer to be used for the [Issuer](https://docs.microsoft.com/dotnet/api/system.security.claims.claim.issuer) property on any claims created by the cookie authentication.</span></span>

* <span data-ttu-id="cf04c-170">`CookieBuilder.Domain`쿠키 처리 되는 도메인 이름이입니다.</span><span class="sxs-lookup"><span data-stu-id="cf04c-170">`CookieBuilder.Domain` is the domain name to which the cookie is served.</span></span> <span data-ttu-id="cf04c-171">기본적으로에 요청을 보냈습니다. 호스트 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="cf04c-171">By default, this is the host name the request was sent to.</span></span> <span data-ttu-id="cf04c-172">브라우저 쿠키를 일치 하는 호스트 이름으로 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="cf04c-172">The browser only serves the cookie to a matching host name.</span></span> <span data-ttu-id="cf04c-173">이 도메인의 모든 호스트에 사용할 수 있는 쿠키를 조정할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cf04c-173">You may wish to adjust this to have cookies available to any host in your domain.</span></span> <span data-ttu-id="cf04c-174">예를 들어 쿠키 도메인 설정 `.contoso.com` 를 사용할 수 있도록 `contoso.com`, `www.contoso.com`, `staging.www.contoso.com`등입니다.</span><span class="sxs-lookup"><span data-stu-id="cf04c-174">For example, setting the cookie domain to `.contoso.com` makes it available to `contoso.com`, `www.contoso.com`, `staging.www.contoso.com`, etc.</span></span>

* <span data-ttu-id="cf04c-175">`CookieBuilder.HttpOnly`쿠키를 서버에만 액세스할 수 있어야 하는 경우 나타내는 플래그입니다.</span><span class="sxs-lookup"><span data-stu-id="cf04c-175">`CookieBuilder.HttpOnly` is a flag indicating if the cookie should be accessible only to servers.</span></span> <span data-ttu-id="cf04c-176">기본값은 `true`합니다.</span><span class="sxs-lookup"><span data-stu-id="cf04c-176">This defaults to `true`.</span></span> <span data-ttu-id="cf04c-177">이 값을 변경 하며 열림 쿠키 도용에 응용 프로그램을 응용 프로그램 사이트 간 스크립팅 버그 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="cf04c-177">Changing this value may open your application to cookie theft should your application have a Cross-Site Scripting bug.</span></span>

* <span data-ttu-id="cf04c-178">`CookieBuilder.Path`동일한 호스트 이름에서 실행 되는 응용 프로그램을 격리 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cf04c-178">`CookieBuilder.Path` can be used to isolate applications running on the same host name.</span></span> <span data-ttu-id="cf04c-179">실행 중인 앱이 있는 경우 `/app1` 해당 응용 프로그램에 전송에 발급 된 쿠키를 제한 하려면 설정 해야 합니다는 `CookiePath` 속성을 `/app1`합니다.</span><span class="sxs-lookup"><span data-stu-id="cf04c-179">If you have an app running in `/app1` and want to limit the cookies issued to just be sent to that application, then you should set the `CookiePath` property to `/app1`.</span></span> <span data-ttu-id="cf04c-180">이러한 작업을 통해 쿠키 에서만 사용 가능 하는 요청에 `/app1` 또는 그 아래에 있는 항목입니다.</span><span class="sxs-lookup"><span data-stu-id="cf04c-180">By doing so, the cookie is only available to requests to `/app1` or anything underneath it.</span></span>

* <span data-ttu-id="cf04c-181">`CookieBuilder.SameSite`브라우저에서 같은 사이트 또는 사이트 간 요청에 첨부할 쿠키를 허용 하는지 여부를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="cf04c-181">`CookieBuilder.SameSite` indicates whether the browser should allow the cookie to be attached to same-site or cross-site requests.</span></span> <span data-ttu-id="cf04c-182">기본값은 `SameSiteMode.Lax`합니다.</span><span class="sxs-lookup"><span data-stu-id="cf04c-182">This defaults to `SameSiteMode.Lax`.</span></span>

* <span data-ttu-id="cf04c-183">`CookieBuilder.SecurePolicy`만든 쿠키를 HTTPS, HTTP 또는 HTTPS 또는 요청와 동일한 프로토콜 제한 해야 하는 경우 나타내는 플래그입니다.</span><span class="sxs-lookup"><span data-stu-id="cf04c-183">`CookieBuilder.SecurePolicy` is a flag indicating if the cookie created should be limited to HTTPS, HTTP or HTTPS, or the same protocol as the request.</span></span> <span data-ttu-id="cf04c-184">기본값은 `SameAsRequest`합니다.</span><span class="sxs-lookup"><span data-stu-id="cf04c-184">This defaults to `SameAsRequest`.</span></span>

* <span data-ttu-id="cf04c-185">`ExpireTimeSpan`이 `TimeSpan` 쿠키가 만료 된 이후입니다.</span><span class="sxs-lookup"><span data-stu-id="cf04c-185">`ExpireTimeSpan` is the `TimeSpan` after which the cookie expires.</span></span> <span data-ttu-id="cf04c-186">쿠키의 만료 날짜를 만들 현재 날짜 및 시간에 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cf04c-186">It's added to the current date and time to create the expiry date for the cookie.</span></span>

* <span data-ttu-id="cf04c-187">`SlidingExpiration`이상의 절반 쿠키 만료 날짜를 다시 설정 하는지 여부를 나타내는 플래그는 `ExpireTimeSpan` 관리가 합니다.</span><span class="sxs-lookup"><span data-stu-id="cf04c-187">`SlidingExpiration` is a flag indicating whether the cookie expiration date resets when more than half of the `ExpireTimeSpan` interval has passed.</span></span> <span data-ttu-id="cf04c-188">이 새 만료 날짜가 현재 날짜를 앞으로 이동와 `ExpireTimespan`합니다.</span><span class="sxs-lookup"><span data-stu-id="cf04c-188">The new expiry date is moved forward to be the current date plus the `ExpireTimespan`.</span></span> <span data-ttu-id="cf04c-189">[절대 만료 시간](xref:security/authentication/cookie#security-authentication-absolute-expiry) 를 사용 하 여 설정할 수는 `AuthenticationProperties` 클래스를 호출할 때 `SignInAsync`합니다.</span><span class="sxs-lookup"><span data-stu-id="cf04c-189">An [absolute expiry time](xref:security/authentication/cookie#security-authentication-absolute-expiry) can be set by using the `AuthenticationProperties` class when calling `SignInAsync`.</span></span> <span data-ttu-id="cf04c-190">절대 만료 인증 쿠키의 유효 시간을 제한 하 여 응용 프로그램의 보안을 향상 시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cf04c-190">An absolute expiry can improve the security of your application by limiting the amount of time for which the authentication cookie is valid.</span></span>

<span data-ttu-id="cf04c-191">사용 하는 예제 `CookieAuthenticationOptions` 에 `ConfigureServices` 방식의 *Startup.cs* 뒤에 오는:</span><span class="sxs-lookup"><span data-stu-id="cf04c-191">An example of using `CookieAuthenticationOptions` in the `ConfigureServices` method of *Startup.cs* follows:</span></span>

```csharp
services.AddAuthentication()
        .AddCookie(options =>
        {
            options.Cookie.Name = "AuthCookie";
            options.Cookie.Domain = "contoso.com";
            options.Cookie.Path = "/";
            options.Cookie.HttpOnly = true;
            options.Cookie.SameSite = SameSiteMode.Lax;
            options.Cookie.SecurePolicy = CookieSecurePolicy.Always;
        });
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="cf04c-192">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="cf04c-192">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

* <span data-ttu-id="cf04c-193">`ClaimsIssuer`에 사용할 발급자는 [발급자](https://docs.microsoft.com/dotnet/api/system.security.claims.claim.issuer) 미들웨어에서 만든 클레임의 속성입니다.</span><span class="sxs-lookup"><span data-stu-id="cf04c-193">`ClaimsIssuer` is the issuer to be used for the [Issuer](https://docs.microsoft.com/dotnet/api/system.security.claims.claim.issuer) property on any claims created by the middleware.</span></span>

* <span data-ttu-id="cf04c-194">`CookieDomain`쿠키 처리 되는 도메인 이름이입니다.</span><span class="sxs-lookup"><span data-stu-id="cf04c-194">`CookieDomain` is the domain name to which the cookie is served.</span></span> <span data-ttu-id="cf04c-195">기본적으로에 요청을 보냈습니다. 호스트 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="cf04c-195">By default, this is the host name the request was sent to.</span></span> <span data-ttu-id="cf04c-196">브라우저 쿠키를 일치 하는 호스트 이름으로 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="cf04c-196">The browser only serves the cookie to a matching host name.</span></span> <span data-ttu-id="cf04c-197">이 도메인의 모든 호스트에 사용할 수 있는 쿠키를 조정할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cf04c-197">You may wish to adjust this to have cookies available to any host in your domain.</span></span> <span data-ttu-id="cf04c-198">예를 들어 쿠키 도메인 설정 `.contoso.com` 를 사용할 수 있도록 `contoso.com`, `www.contoso.com`, `staging.www.contoso.com`등입니다.</span><span class="sxs-lookup"><span data-stu-id="cf04c-198">For example, setting the cookie domain to `.contoso.com` makes it available to `contoso.com`, `www.contoso.com`, `staging.www.contoso.com`, etc.</span></span>

* <span data-ttu-id="cf04c-199">`CookieHttpOnly`쿠키를 서버에만 액세스할 수 있어야 하는 경우 나타내는 플래그입니다.</span><span class="sxs-lookup"><span data-stu-id="cf04c-199">`CookieHttpOnly` is a flag indicating if the cookie should be accessible only to servers.</span></span> <span data-ttu-id="cf04c-200">기본값은 `true`합니다.</span><span class="sxs-lookup"><span data-stu-id="cf04c-200">This defaults to `true`.</span></span> <span data-ttu-id="cf04c-201">이 값을 변경 하며 열림 쿠키 도용에 응용 프로그램을 응용 프로그램 사이트 간 스크립팅 버그 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="cf04c-201">Changing this value may open your application to cookie theft should your application have a Cross-Site Scripting bug.</span></span>

* <span data-ttu-id="cf04c-202">`CookiePath`동일한 호스트 이름에서 실행 되는 응용 프로그램을 격리 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cf04c-202">`CookiePath` can be used to isolate applications running on the same host name.</span></span> <span data-ttu-id="cf04c-203">실행 중인 앱이 있는 경우 `/app1` 해당 응용 프로그램에 전송에 발급 된 쿠키를 제한 하려면 설정 해야 합니다는 `CookiePath` 속성을 `/app1`합니다.</span><span class="sxs-lookup"><span data-stu-id="cf04c-203">If you have an app running in `/app1` and want to limit the cookies issued to just be sent to that application, then you should set the `CookiePath` property to `/app1`.</span></span> <span data-ttu-id="cf04c-204">이러한 작업을 통해 쿠키 에서만 사용 가능 하는 요청에 `/app1` 또는 그 아래에 있는 항목입니다.</span><span class="sxs-lookup"><span data-stu-id="cf04c-204">By doing so, the cookie is only available to requests to `/app1` or anything underneath it.</span></span>

* <span data-ttu-id="cf04c-205">`CookieSecure`만든 쿠키를 HTTPS, HTTP 또는 HTTPS 또는 요청와 동일한 프로토콜 제한 해야 하는 경우 나타내는 플래그입니다.</span><span class="sxs-lookup"><span data-stu-id="cf04c-205">`CookieSecure` is a flag indicating if the cookie created should be limited to HTTPS, HTTP or HTTPS, or the same protocol as the request.</span></span> <span data-ttu-id="cf04c-206">기본값은 `SameAsRequest`합니다.</span><span class="sxs-lookup"><span data-stu-id="cf04c-206">This defaults to `SameAsRequest`.</span></span>

* <span data-ttu-id="cf04c-207">`ExpireTimeSpan`이 `TimeSpan` 쿠키가 만료 된 이후입니다.</span><span class="sxs-lookup"><span data-stu-id="cf04c-207">`ExpireTimeSpan` is the `TimeSpan` after which the cookie expires.</span></span> <span data-ttu-id="cf04c-208">쿠키의 만료 날짜를 만들 현재 날짜 및 시간에 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cf04c-208">It's added to the current date and time to create the expiry date for the cookie.</span></span>

* <span data-ttu-id="cf04c-209">`SlidingExpiration`이상의 절반 쿠키 만료 날짜를 다시 설정 하는지 여부를 나타내는 플래그는 `ExpireTimeSpan` 관리가 합니다.</span><span class="sxs-lookup"><span data-stu-id="cf04c-209">`SlidingExpiration` is a flag indicating whether the cookie expiration date resets when more than half of the `ExpireTimeSpan` interval has passed.</span></span> <span data-ttu-id="cf04c-210">이 새 만료 날짜가 현재 날짜를 앞으로 이동와 `ExpireTimespan`합니다.</span><span class="sxs-lookup"><span data-stu-id="cf04c-210">The new expiry date is moved forward to be the current date plus the `ExpireTimespan`.</span></span> <span data-ttu-id="cf04c-211">[절대 만료 시간](xref:security/authentication/cookie#security-authentication-absolute-expiry) 를 사용 하 여 설정할 수는 `AuthenticationProperties` 클래스를 호출할 때 `SignInAsync`합니다.</span><span class="sxs-lookup"><span data-stu-id="cf04c-211">An [absolute expiry time](xref:security/authentication/cookie#security-authentication-absolute-expiry) can be set by using the `AuthenticationProperties` class when calling `SignInAsync`.</span></span> <span data-ttu-id="cf04c-212">절대 만료 인증 쿠키의 유효 시간을 제한 하 여 응용 프로그램의 보안을 향상 시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cf04c-212">An absolute expiry can improve the security of your application by limiting the amount of time for which the authentication cookie is valid.</span></span>

<span data-ttu-id="cf04c-213">사용 하는 예제 `CookieAuthenticationOptions` 에 `Configure` 방식의 *Startup.cs* 뒤에 오는:</span><span class="sxs-lookup"><span data-stu-id="cf04c-213">An example of using `CookieAuthenticationOptions` in the `Configure` method of *Startup.cs* follows:</span></span>

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

## <a name="persistent-cookies-and-absolute-expiry-times"></a><span data-ttu-id="cf04c-214">영구 쿠키 및 절대 만료 시간</span><span class="sxs-lookup"><span data-stu-id="cf04c-214">Persistent cookies and absolute expiry times</span></span>

<span data-ttu-id="cf04c-215">브라우저 세션에서 유지 하는 id 및 쿠키를 전송 하는 것에 절대 만료 원하는 쿠키 만료를 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cf04c-215">You may want the cookie expiry to persist across browser sessions and want an absolute expiry to the identity and the cookie transporting it.</span></span> <span data-ttu-id="cf04c-216">이 지 속성에 로그인 또는 유사한 메커니즘에서 "암호 저장" 확인란을 통해 명시적 사용자 동의가 있는 경우에 설정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="cf04c-216">This persistence should only be enabled with explicit user consent, via a "Remember Me" checkbox on login or a similar mechanism.</span></span> <span data-ttu-id="cf04c-217">사용 하 여 이러한 작업을 할 수 있습니다는 `AuthenticationProperties` 에 매개 변수는 `SignInAsync` 때이 메서드가 호출 [id의 서명 및 쿠키를 만드는](xref:security/authentication/cookie#security-authentication-cookie-middleware-creating-a-cookie)합니다.</span><span class="sxs-lookup"><span data-stu-id="cf04c-217">You can do these things by using the `AuthenticationProperties` parameter on the `SignInAsync` method called when [signing in an identity and creating the cookie](xref:security/authentication/cookie#security-authentication-cookie-middleware-creating-a-cookie).</span></span> <span data-ttu-id="cf04c-218">예:</span><span class="sxs-lookup"><span data-stu-id="cf04c-218">For example:</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="cf04c-219">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="cf04c-219">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```csharp
await HttpContext.SignInAsync(
    "MyCookieAuthenticationScheme",
    principal,
    new AuthenticationProperties
    {
        IsPersistent = true
    });
```

<span data-ttu-id="cf04c-220">`AuthenticationProperties` 에 위치 앞의 코드 조각에 사용 되는 클래스는 `Microsoft.AspNetCore.Authentication` 네임 스페이스입니다.</span><span class="sxs-lookup"><span data-stu-id="cf04c-220">The `AuthenticationProperties` class, used in the preceding code snippet, resides in the `Microsoft.AspNetCore.Authentication` namespace.</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="cf04c-221">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="cf04c-221">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

```csharp
await HttpContext.Authentication.SignInAsync(
    "MyCookieAuthenticationScheme",
    principal,
    new AuthenticationProperties
    {
        IsPersistent = true
    });
```

<span data-ttu-id="cf04c-222">`AuthenticationProperties` 에 위치 앞의 코드 조각에 사용 되는 클래스는 `Microsoft.AspNetCore.Http.Authentication` 네임 스페이스입니다.</span><span class="sxs-lookup"><span data-stu-id="cf04c-222">The `AuthenticationProperties` class, used in the preceding code snippet, resides in the `Microsoft.AspNetCore.Http.Authentication` namespace.</span></span>

---

<span data-ttu-id="cf04c-223">앞의 코드 조각 id 및 브라우저 클로저를 통해 되더라도 남아 있지만 해당 쿠키를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="cf04c-223">The preceding code snippet creates an identity and corresponding cookie which survives through browser closures.</span></span> <span data-ttu-id="cf04c-224">슬라이딩 만료 설정을 통해 이전에 구성 된 [쿠키 옵션](xref:security/authentication/cookie#security-authentication-cookie-options) 여전히 인식 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cf04c-224">Any sliding expiration settings previously configured via [cookie options](xref:security/authentication/cookie#security-authentication-cookie-options) are still honored.</span></span> <span data-ttu-id="cf04c-225">쿠키는 브라우저 종료 하는 동안 만료 되 면 다시 시작 되 면 브라우저 지우지 합니다.</span><span class="sxs-lookup"><span data-stu-id="cf04c-225">If the cookie expires whilst the browser is closed, the browser clears it once it is restarted.</span></span>

<a name="security-authentication-absolute-expiry"></a>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="cf04c-226">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="cf04c-226">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```csharp
await HttpContext.SignInAsync(
    "MyCookieAuthenticationScheme",
    principal,
    new AuthenticationProperties
    {
        ExpiresUtc = DateTime.UtcNow.AddMinutes(20)
    });
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="cf04c-227">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="cf04c-227">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

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

<span data-ttu-id="cf04c-228">앞의 코드 조각 id 및 20 분 동안 지속 되는 해당 쿠키를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="cf04c-228">The preceding code snippet creates an identity and corresponding cookie which lasts for 20 minutes.</span></span> <span data-ttu-id="cf04c-229">슬라이딩 만료 설정을 통해 이전에 구성 된 무시 [쿠키 옵션](xref:security/authentication/cookie#security-authentication-cookie-options)합니다.</span><span class="sxs-lookup"><span data-stu-id="cf04c-229">This ignores any sliding expiration settings previously configured via [cookie options](xref:security/authentication/cookie#security-authentication-cookie-options).</span></span>

<span data-ttu-id="cf04c-230">`ExpiresUtc` 및 `IsPersistent` 속성은 함께 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="cf04c-230">The `ExpiresUtc` and `IsPersistent` properties are mutually exclusive.</span></span>
