---
title: "ASP.NET Core로 특정 구성표-권한 부여"
author: rick-anderson
description: "이 문서에서는 여러 가지 인증 방법을 사용할 때 id는 특정 체계를 제한 하는 방법을 설명 합니다."
keywords: "ASP.NET Core, id, 인증 체계"
ms.author: riande
manager: wpickett
ms.date: 10/12/2017
ms.topic: article
ms.assetid: d3d6ca1b-b4b5-4bf7-898e-dcd90ec1bf8c
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/authorization/limitingidentitybyscheme
ms.openlocfilehash: cf3259f206b8d970cc6f2b0b9e52e233c30d6df3
ms.sourcegitcommit: e3b1726cc04e80dc28464c35259edbd3bc39a438
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/12/2017
---
# <a name="authorize-with-a-specific-scheme"></a>특정 구성표로 권한 부여

단일 페이지 응용 프로그램 (SPAs)와 같은 일부 시나리오에서는 일반적으로 여러 인증 방법을 사용 하는 합니다. 예를 들어 응용 프로그램 JavaScript 요청에 대 한 로그인 쿠키 기반 인증 및 JWT 전달자 인증 사용할 수 있습니다. 경우에 따라 응용 프로그램 인증 처리기를 여러 개 있을 수 있습니다. 예를 들어 기본적인 id를 포함 한 두 명의 쿠키 처리기와 multi-factor authentication (MFA)가 트리거되면 생성 됩니다. 사용자 추가 보안을 필요로 하는 작업을 요청 했으므로 MFA 트리거될 수 있습니다.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

인증 서비스를 인증 하는 동안 구성할 때 인증 체계 라고 합니다. 예:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Code omitted for brevity

    services.AddAuthentication()
        .AddCookie(options => {
            options.LoginPath = "/Account/Unauthorized/";
            options.AccessDeniedPath = "/Account/Forbidden/";
        })
        .AddJwtBearer(options => {
            options.Audience = "http://localhost:5001/";
            options.Authority = "http://localhost:5000/";
        });
```

위의 코드에서는 두 가지 인증 처리기 추가 되었습니다: 쿠키와 전달자에 대 한 합니다.

>[!NOTE]
>기본 스키마를 지정 하면는 `HttpContext.User` 속성이 해당 id로 설정 되 고 있습니다. 해당 동작이 필요 없는 경우 매개 변수가 없는 형식으로 호출 하 여 비활성화 `AddAuthentication`합니다.

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

인증 체계는 인증 middlewares 인증 하는 동안 구성 된 경우 이름이 지정 됩니다. 예:

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
{
    // Code omitted for brevity

    app.UseCookieAuthentication(new CookieAuthenticationOptions()
    {
        AuthenticationScheme = "Cookie",
        LoginPath = "/Account/Unauthorized/",
        AccessDeniedPath = "/Account/Forbidden/",
        AutomaticAuthenticate = false
    });
    
    app.UseJwtBearerAuthentication(new JwtBearerOptions()
    {
        AuthenticationScheme = "Bearer",
        AutomaticAuthenticate = false,
        Audience = "http://localhost:5001/",
        Authority = "http://localhost:5000/",
        RequireHttpsMetadata = false
    });
```

위의 코드에서는 두 가지 인증 middlewares 추가 되었습니다: 쿠키와 전달자에 대 한 합니다.

>[!NOTE]
>기본 스키마를 지정 하면는 `HttpContext.User` 속성이 해당 id로 설정 되 고 있습니다. 해당 동작이 필요 없는 경우 설정 하 여 비활성화 된 `AuthenticationOptions.AutomaticAuthenticate` 속성을 `false`합니다.

---

## <a name="selecting-the-scheme-with-the-authorize-attribute"></a>권한 부여 특성을 가진 구성표 선택

권한 부여, 지점에서 응용 프로그램에 사용할 처리기를 나타냅니다. 응용 프로그램에 인증 체계의 쉼표로 구분 된 목록을 전달 하 여 승인 된 처리기를 선택 `[Authorize]`합니다. `[Authorize]` 특성 인증 체계 또는 기본 구성 되어 있는지 여부에 관계 없이 사용 하는 구성표를 지정 합니다. 예:

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

```csharp
[Authorize(AuthenticationSchemes = "Cookie,Bearer")]
public class MixedController : Controller
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

```csharp
[Authorize(ActiveAuthenticationSchemes = "Cookie,Bearer")]
public class MixedController : Controller
```

---

앞의 예에서 쿠키와 전달자 처리기 실행 하 고는 기회를 만들고 현재 사용자에 대 한 id를 추가 합니다. 단일 구성표를 지정 하 여 해당 처리기를 실행 합니다.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

```csharp
[Authorize(AuthenticationSchemes = "Bearer")]
public class MixedController : Controller
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

```csharp
[Authorize(ActiveAuthenticationSchemes = "Bearer")]
public class MixedController : Controller
```

---

위의 코드에서 "Bearer" 체계로 처리기만 실행 됩니다. 모든 쿠키 기반 id는 무시 됩니다.

## <a name="selecting-the-scheme-with-policies"></a>정책 사용 하 여 구성표 선택

원하는 체계를 지정 하려는 경우 [정책](xref:security/authorization/policies#security-authorization-policies-based)를 설정할 수 있습니다는 `AuthenticationSchemes` 정책에 추가할 때 컬렉션:

```csharp
services.AddAuthorization(options =>
{
    options.AddPolicy("Over18", policy =>
    {
        policy.AuthenticationSchemes.Add("Bearer");
        policy.RequireAuthenticatedUser();
        policy.Requirements.Add(new MinimumAgeRequirement());
    });
});
```

위의 예제에서는 "Over18" 정책 "Bearer" 처리기에 의해 생성 된 id에 대해만 실행 됩니다. 설정 하 여 정책을 사용 하 여는 `[Authorize]` 특성의 `Policy` 속성:

```csharp
[Authorize(Policy = "Over18")]
public class RegistrationController : Controller
```
