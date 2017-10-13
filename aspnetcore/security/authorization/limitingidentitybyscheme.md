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
# <a name="authorize-with-a-specific-scheme"></a><span data-ttu-id="ff86f-104">특정 구성표로 권한 부여</span><span class="sxs-lookup"><span data-stu-id="ff86f-104">Authorize with a specific scheme</span></span>

<span data-ttu-id="ff86f-105">단일 페이지 응용 프로그램 (SPAs)와 같은 일부 시나리오에서는 일반적으로 여러 인증 방법을 사용 하는 합니다.</span><span class="sxs-lookup"><span data-stu-id="ff86f-105">In some scenarios, such as Single Page Applications (SPAs), it's common to use multiple authentication methods.</span></span> <span data-ttu-id="ff86f-106">예를 들어 응용 프로그램 JavaScript 요청에 대 한 로그인 쿠키 기반 인증 및 JWT 전달자 인증 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ff86f-106">For example, the app may use cookie-based authentication to log in and JWT bearer authentication for JavaScript requests.</span></span> <span data-ttu-id="ff86f-107">경우에 따라 응용 프로그램 인증 처리기를 여러 개 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ff86f-107">In some cases, the app may have multiple instances of an authentication handler.</span></span> <span data-ttu-id="ff86f-108">예를 들어 기본적인 id를 포함 한 두 명의 쿠키 처리기와 multi-factor authentication (MFA)가 트리거되면 생성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ff86f-108">For example, two cookie handlers where one contains a basic identity and one is created when a multi-factor authentication (MFA) has been triggered.</span></span> <span data-ttu-id="ff86f-109">사용자 추가 보안을 필요로 하는 작업을 요청 했으므로 MFA 트리거될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ff86f-109">MFA may be triggered because the user requested an operation that requires extra security.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="ff86f-110">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="ff86f-110">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="ff86f-111">인증 서비스를 인증 하는 동안 구성할 때 인증 체계 라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="ff86f-111">An authentication scheme is named when the authentication service is configured during authentication.</span></span> <span data-ttu-id="ff86f-112">예:</span><span class="sxs-lookup"><span data-stu-id="ff86f-112">For example:</span></span>

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

<span data-ttu-id="ff86f-113">위의 코드에서는 두 가지 인증 처리기 추가 되었습니다: 쿠키와 전달자에 대 한 합니다.</span><span class="sxs-lookup"><span data-stu-id="ff86f-113">In the preceding code, two authentication handlers have been added: one for cookies and one for bearer.</span></span>

>[!NOTE]
><span data-ttu-id="ff86f-114">기본 스키마를 지정 하면는 `HttpContext.User` 속성이 해당 id로 설정 되 고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ff86f-114">Specifying the default scheme results in the `HttpContext.User` property being set to that identity.</span></span> <span data-ttu-id="ff86f-115">해당 동작이 필요 없는 경우 매개 변수가 없는 형식으로 호출 하 여 비활성화 `AddAuthentication`합니다.</span><span class="sxs-lookup"><span data-stu-id="ff86f-115">If that behavior isn't desired, disable it by invoking the parameterless form of `AddAuthentication`.</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="ff86f-116">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="ff86f-116">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="ff86f-117">인증 체계는 인증 middlewares 인증 하는 동안 구성 된 경우 이름이 지정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ff86f-117">Authentication schemes are named when authentication middlewares are configured during authentication.</span></span> <span data-ttu-id="ff86f-118">예:</span><span class="sxs-lookup"><span data-stu-id="ff86f-118">For example:</span></span>

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

<span data-ttu-id="ff86f-119">위의 코드에서는 두 가지 인증 middlewares 추가 되었습니다: 쿠키와 전달자에 대 한 합니다.</span><span class="sxs-lookup"><span data-stu-id="ff86f-119">In the preceding code, two authentication middlewares have been added: one for cookies and one for bearer.</span></span>

>[!NOTE]
><span data-ttu-id="ff86f-120">기본 스키마를 지정 하면는 `HttpContext.User` 속성이 해당 id로 설정 되 고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ff86f-120">Specifying the default scheme results in the `HttpContext.User` property being set to that identity.</span></span> <span data-ttu-id="ff86f-121">해당 동작이 필요 없는 경우 설정 하 여 비활성화 된 `AuthenticationOptions.AutomaticAuthenticate` 속성을 `false`합니다.</span><span class="sxs-lookup"><span data-stu-id="ff86f-121">If that behavior isn't desired, disable it by setting the `AuthenticationOptions.AutomaticAuthenticate` property to `false`.</span></span>

---

## <a name="selecting-the-scheme-with-the-authorize-attribute"></a><span data-ttu-id="ff86f-122">권한 부여 특성을 가진 구성표 선택</span><span class="sxs-lookup"><span data-stu-id="ff86f-122">Selecting the scheme with the Authorize attribute</span></span>

<span data-ttu-id="ff86f-123">권한 부여, 지점에서 응용 프로그램에 사용할 처리기를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="ff86f-123">At the point of authorization, the app indicates the handler to be used.</span></span> <span data-ttu-id="ff86f-124">응용 프로그램에 인증 체계의 쉼표로 구분 된 목록을 전달 하 여 승인 된 처리기를 선택 `[Authorize]`합니다.</span><span class="sxs-lookup"><span data-stu-id="ff86f-124">Select the handler with which the app will authorize by passing a comma-delimited list of authentication schemes to `[Authorize]`.</span></span> <span data-ttu-id="ff86f-125">`[Authorize]` 특성 인증 체계 또는 기본 구성 되어 있는지 여부에 관계 없이 사용 하는 구성표를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="ff86f-125">The `[Authorize]` attribute specifies the authentication scheme or schemes to use regardless of whether a default is configured.</span></span> <span data-ttu-id="ff86f-126">예:</span><span class="sxs-lookup"><span data-stu-id="ff86f-126">For example:</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="ff86f-127">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="ff86f-127">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```csharp
[Authorize(AuthenticationSchemes = "Cookie,Bearer")]
public class MixedController : Controller
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="ff86f-128">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="ff86f-128">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

```csharp
[Authorize(ActiveAuthenticationSchemes = "Cookie,Bearer")]
public class MixedController : Controller
```

---

<span data-ttu-id="ff86f-129">앞의 예에서 쿠키와 전달자 처리기 실행 하 고는 기회를 만들고 현재 사용자에 대 한 id를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="ff86f-129">In the preceding example, both the cookie and bearer handlers run and have a chance to create and append an identity for the current user.</span></span> <span data-ttu-id="ff86f-130">단일 구성표를 지정 하 여 해당 처리기를 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="ff86f-130">By specifying a single scheme only, the corresponding handler runs.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="ff86f-131">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="ff86f-131">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```csharp
[Authorize(AuthenticationSchemes = "Bearer")]
public class MixedController : Controller
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="ff86f-132">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="ff86f-132">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

```csharp
[Authorize(ActiveAuthenticationSchemes = "Bearer")]
public class MixedController : Controller
```

---

<span data-ttu-id="ff86f-133">위의 코드에서 "Bearer" 체계로 처리기만 실행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ff86f-133">In the preceding code, only the handler with the "Bearer" scheme runs.</span></span> <span data-ttu-id="ff86f-134">모든 쿠키 기반 id는 무시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ff86f-134">Any cookie-based identities are ignored.</span></span>

## <a name="selecting-the-scheme-with-policies"></a><span data-ttu-id="ff86f-135">정책 사용 하 여 구성표 선택</span><span class="sxs-lookup"><span data-stu-id="ff86f-135">Selecting the scheme with policies</span></span>

<span data-ttu-id="ff86f-136">원하는 체계를 지정 하려는 경우 [정책](xref:security/authorization/policies#security-authorization-policies-based)를 설정할 수 있습니다는 `AuthenticationSchemes` 정책에 추가할 때 컬렉션:</span><span class="sxs-lookup"><span data-stu-id="ff86f-136">If you prefer to specify the desired schemes in [policy](xref:security/authorization/policies#security-authorization-policies-based), you can set the `AuthenticationSchemes` collection when adding your policy:</span></span>

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

<span data-ttu-id="ff86f-137">위의 예제에서는 "Over18" 정책 "Bearer" 처리기에 의해 생성 된 id에 대해만 실행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ff86f-137">In the preceding example, the "Over18" policy only runs against the identity created by the "Bearer" handler.</span></span> <span data-ttu-id="ff86f-138">설정 하 여 정책을 사용 하 여는 `[Authorize]` 특성의 `Policy` 속성:</span><span class="sxs-lookup"><span data-stu-id="ff86f-138">Use the policy by setting the `[Authorize]` attribute's `Policy` property:</span></span>

```csharp
[Authorize(Policy = "Over18")]
public class RegistrationController : Controller
```
