---
title: "마이그레이션 인증 및 ASP.NET 코어 2.0 Id"
author: scottaddie
description: "이 문서는 ASP.NET 코어 2.0으로 마이그레이션 ASP.NET Core 1.x 인증 및 Id에 대 한 가장 일반적인 단계를 간략하게 설명 합니다."
keywords: "ASP.NET Core, Id 인증"
ms.author: scaddie
manager: wpickett
ms.date: 08/02/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: migration/1x-to-2x/identity-2x
ms.openlocfilehash: 6505378c718d2ec676b534ddeb141231faf3fca3
ms.sourcegitcommit: 8cafdd1dd409d5070d227100ba0e094c779ac47b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2017
---
# <a name="migrating-authentication-and-identity-to-aspnet-core-20"></a><span data-ttu-id="4e3f4-104">마이그레이션 인증 및 ASP.NET 코어 2.0 Id</span><span class="sxs-lookup"><span data-stu-id="4e3f4-104">Migrating Authentication and Identity to ASP.NET Core 2.0</span></span>

<span data-ttu-id="4e3f4-105">여 [Scott Addie](https://github.com/scottaddie) 및 [Hao 둘러싼](https://github.com/HaoK)</span><span class="sxs-lookup"><span data-stu-id="4e3f4-105">By [Scott Addie](https://github.com/scottaddie) and [Hao Kung](https://github.com/HaoK)</span></span>

<span data-ttu-id="4e3f4-106">ASP.NET Core 2.0 인증을 위한 새 모델을 포함 하 고 [Identity](xref:security/authentication/identity) 서비스를 사용 하 여 구성을 간소화 합니다.</span><span class="sxs-lookup"><span data-stu-id="4e3f4-106">ASP.NET Core 2.0 has a new model for authentication and [Identity](xref:security/authentication/identity) which simplifies configuration by using services.</span></span> <span data-ttu-id="4e3f4-107">아래 설명 된 대로 새 모델을 사용 하도록 인증 또는 Id를 사용 하는 ASP.NET Core 1.x 응용 프로그램을 업데이트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4e3f4-107">ASP.NET Core 1.x applications that use authentication or Identity can be updated to use the new model as outlined below.</span></span>

<a name="auth-middleware"></a>

## <a name="authentication-middleware-and-services"></a><span data-ttu-id="4e3f4-108">인증 미들웨어 및 서비스</span><span class="sxs-lookup"><span data-stu-id="4e3f4-108">Authentication Middleware and Services</span></span>
<span data-ttu-id="4e3f4-109">1.x 프로젝트에서 인증 미들웨어를 통해 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4e3f4-109">In 1.x projects, authentication is configured via middleware.</span></span> <span data-ttu-id="4e3f4-110">미들웨어 메서드를 지원 하려면 각 인증 체계에 대해 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4e3f4-110">A middleware method is invoked for each authentication scheme you want to support.</span></span>

<span data-ttu-id="4e3f4-111">에 Id를 가진 Facebook 인증을 구성 하는 다음 1.x 예제 *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="4e3f4-111">The following 1.x example configures Facebook authentication with Identity in *Startup.cs*:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext>();
}

public void Configure(IApplicationBuilder app, ILoggerFactory loggerfactory)
{
    app.UseIdentity();
    app.UseFacebookAuthentication(new FacebookOptions { 
        AppId = Configuration["auth:facebook:appid"],
        AppSecret = Configuration["auth:facebook:appsecret"]
    });
} 
```

<span data-ttu-id="4e3f4-112">2.0 프로젝트에서 서비스를 통해 인증이 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4e3f4-112">In 2.0 projects, authentication is configured via services.</span></span> <span data-ttu-id="4e3f4-113">에 등록 된 각 인증 체계는 `ConfigureServices` 방식의 *Startup.cs*합니다.</span><span class="sxs-lookup"><span data-stu-id="4e3f4-113">Each authentication scheme is registered in the `ConfigureServices` method of *Startup.cs*.</span></span> <span data-ttu-id="4e3f4-114">`UseIdentity` 메서드 아래 템플릿으로 바뀝니다 `UseAuthentication`합니다.</span><span class="sxs-lookup"><span data-stu-id="4e3f4-114">The `UseIdentity` method is replaced with `UseAuthentication`.</span></span>

<span data-ttu-id="4e3f4-115">에 Id를 가진 Facebook 인증을 구성 하는 다음 2.0 예제 *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="4e3f4-115">The following 2.0 example configures Facebook authentication with Identity in *Startup.cs*:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext>();

    // If you want to tweak Identity cookies, they're no longer part of IdentityOptions.
    services.ConfigureApplicationCookie(options => options.LoginPath = "/Account/LogIn");
    services.AddAuthentication()
            .AddFacebook(options => {
                options.AppId = Configuration["auth:facebook:appid"];
                options.AppSecret = Configuration["auth:facebook:appsecret"];
            });
}

public void Configure(IApplicationBuilder app, ILoggerFactory loggerfactory) {
    app.UseAuthentication();
}
```

<span data-ttu-id="4e3f4-116">`UseAuthentication` 메서드 자동 인증 및 원격 인증 요청 처리를 담당 하는 단일 인증 미들웨어 구성 요소를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="4e3f4-116">The `UseAuthentication` method adds a single authentication middleware component which is responsible for automatic authentication and the handling of remote authentication requests.</span></span> <span data-ttu-id="4e3f4-117">단일의 공통 미들웨어 구성 요소와 개별 미들웨어 구성 요소를 모두 대체합니다.</span><span class="sxs-lookup"><span data-stu-id="4e3f4-117">It replaces all of the individual middleware components with a single, common middleware component.</span></span>

<span data-ttu-id="4e3f4-118">다음은 각 주요 인증 체계에 대 한 마이그레이션 지침 2.0입니다.</span><span class="sxs-lookup"><span data-stu-id="4e3f4-118">Below are 2.0 migration instructions for each major authentication scheme.</span></span>

### <a name="cookie-based-authentication"></a><span data-ttu-id="4e3f4-119">쿠키 기반 인증</span><span class="sxs-lookup"><span data-stu-id="4e3f4-119">Cookie-based Authentication</span></span>
<span data-ttu-id="4e3f4-120">아래 두 옵션 중 하나를 선택 하 고에 필요한 사항을 변경한 *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="4e3f4-120">Select one of the two options below, and make the necessary changes in *Startup.cs*:</span></span>

1. <span data-ttu-id="4e3f4-121">Id 가진 쿠키 사용</span><span class="sxs-lookup"><span data-stu-id="4e3f4-121">Use cookies with Identity</span></span>
    - <span data-ttu-id="4e3f4-122">대체 `UseIdentity` 와 `UseAuthentication` 에 `Configure` 메서드:</span><span class="sxs-lookup"><span data-stu-id="4e3f4-122">Replace `UseIdentity` with `UseAuthentication` in the `Configure` method:</span></span>

        ```csharp
        app.UseAuthentication();
        ```

    - <span data-ttu-id="4e3f4-123">호출는 `AddIdentity` 에서 메서드는 `ConfigureServices` 메서드 쿠키 인증 서비스를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="4e3f4-123">Invoke the `AddIdentity` method in the `ConfigureServices` method to add the cookie authentication services.</span></span>
    - <span data-ttu-id="4e3f4-124">필요에 따라 호출는 `ConfigureApplicationCookie` 또는 `ConfigureExternalCookie` 에서 메서드는 `ConfigureServices` 메서드 Id 쿠키 설정을 조정 합니다.</span><span class="sxs-lookup"><span data-stu-id="4e3f4-124">Optionally, invoke the `ConfigureApplicationCookie` or `ConfigureExternalCookie` method in the `ConfigureServices` method to tweak the Identity cookie settings.</span></span>

        ```csharp
        services.AddIdentity<ApplicationUser, IdentityRole>()
                .AddEntityFrameworkStores<ApplicationDbContext>()
                .AddDefaultTokenProviders();
    
        services.ConfigureApplicationCookie(options => options.LoginPath = "/Account/LogIn");
        ```

2. <span data-ttu-id="4e3f4-125">Identity 없이 쿠키를 사용 하 여</span><span class="sxs-lookup"><span data-stu-id="4e3f4-125">Use cookies without Identity</span></span>
    - <span data-ttu-id="4e3f4-126">대체는 `UseCookieAuthentication` 메서드 호출의 `Configure` 메서드 `UseAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="4e3f4-126">Replace the `UseCookieAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>
  
        ```csharp
        app.UseAuthentication();
        ```
 
    - <span data-ttu-id="4e3f4-127">호출 된 `AddAuthentication` 및 `AddCookie` 의 메서드는 `ConfigureServices` 메서드:</span><span class="sxs-lookup"><span data-stu-id="4e3f4-127">Invoke the `AddAuthentication` and `AddCookie` methods in the `ConfigureServices` method:</span></span>

        ```csharp
        // If you don't want the cookie to be automatically authenticated and assigned to HttpContext.User, 
        // remove the CookieAuthenticationDefaults.AuthenticationScheme parameter passed to AddAuthentication.
        services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
                .AddCookie(options => {
                    options.LoginPath = "/Account/LogIn";
                    options.LogoutPath = "/Account/LogOff";
                });
        ```

### <a name="jwt-bearer-authentication"></a><span data-ttu-id="4e3f4-128">JWT 전달자 인증</span><span class="sxs-lookup"><span data-stu-id="4e3f4-128">JWT Bearer Authentication</span></span>
<span data-ttu-id="4e3f4-129">다음과 같이 변경 *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="4e3f4-129">Make the following changes in *Startup.cs*:</span></span>
- <span data-ttu-id="4e3f4-130">대체는 `UseJwtBearerAuthentication` 메서드 호출의 `Configure` 메서드 `UseAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="4e3f4-130">Replace the `UseJwtBearerAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>
 
    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="4e3f4-131">호출 된 `AddJwtBearer` 에서 메서드는 `ConfigureServices` 메서드:</span><span class="sxs-lookup"><span data-stu-id="4e3f4-131">Invoke the `AddJwtBearer` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
            .AddJwtBearer(options => {
                options.Audience = "http://localhost:5001/";
                options.Authority = "http://localhost:5000/";
            });
    ```

    <span data-ttu-id="4e3f4-132">전달 하 여 기본 체계를 설정 해야 하므로이 코드 조각은 Id를 사용 하지 않는 `JwtBearerDefaults.AuthenticationScheme` 에 `AddAuthentication` 메서드.</span><span class="sxs-lookup"><span data-stu-id="4e3f4-132">This code snippet doesn't use Identity, so the default scheme should be set by passing `JwtBearerDefaults.AuthenticationScheme` to the `AddAuthentication` method.</span></span>

### <a name="openid-connect-oidc-authentication"></a><span data-ttu-id="4e3f4-133">OpenID Connect (OIDC) 인증</span><span class="sxs-lookup"><span data-stu-id="4e3f4-133">OpenID Connect (OIDC) Authentication</span></span>
<span data-ttu-id="4e3f4-134">다음과 같이 변경 *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="4e3f4-134">Make the following changes in *Startup.cs*:</span></span>

- <span data-ttu-id="4e3f4-135">대체는 `UseOpenIdConnectAuthentication` 메서드 호출의 `Configure` 메서드 `UseAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="4e3f4-135">Replace the `UseOpenIdConnectAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="4e3f4-136">호출 된 `AddOpenIdConnect` 에서 메서드는 `ConfigureServices` 메서드:</span><span class="sxs-lookup"><span data-stu-id="4e3f4-136">Invoke the `AddOpenIdConnect` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication(options => {
        options.DefaultScheme = CookieAuthenticationDefaults.AuthenticationScheme;
        options.DefaultChallengeScheme = OpenIdConnectDefaults.AuthenticationScheme;
    })
    .AddCookie()
    .AddOpenIdConnect(options => {
        options.Authority = Configuration["auth:oidc:authority"];
        options.ClientId = Configuration["auth:oidc:clientid"];
    });
    ```

### <a name="facebook-authentication"></a><span data-ttu-id="4e3f4-137">Facebook 인증</span><span class="sxs-lookup"><span data-stu-id="4e3f4-137">Facebook Authentication</span></span>
<span data-ttu-id="4e3f4-138">다음과 같이 변경 *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="4e3f4-138">Make the following changes in *Startup.cs*:</span></span>
- <span data-ttu-id="4e3f4-139">대체는 `UseFacebookAuthentication` 메서드 호출의 `Configure` 메서드 `UseAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="4e3f4-139">Replace the `UseFacebookAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>
 
    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="4e3f4-140">호출 된 `AddFacebook` 에서 메서드는 `ConfigureServices` 메서드:</span><span class="sxs-lookup"><span data-stu-id="4e3f4-140">Invoke the `AddFacebook` method in the `ConfigureServices` method:</span></span>
    
    ```csharp
    services.AddAuthentication()
            .AddFacebook(options => {
                options.AppId = Configuration["auth:facebook:appid"];
                options.AppSecret = Configuration["auth:facebook:appsecret"];
            });
    ```

### <a name="google-authentication"></a><span data-ttu-id="4e3f4-141">Google 인증</span><span class="sxs-lookup"><span data-stu-id="4e3f4-141">Google Authentication</span></span>
<span data-ttu-id="4e3f4-142">다음과 같이 변경 *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="4e3f4-142">Make the following changes in *Startup.cs*:</span></span>
- <span data-ttu-id="4e3f4-143">대체는 `UseGoogleAuthentication` 메서드 호출의 `Configure` 메서드 `UseAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="4e3f4-143">Replace the `UseGoogleAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>
 
    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="4e3f4-144">호출 된 `AddGoogle` 에서 메서드는 `ConfigureServices` 메서드:</span><span class="sxs-lookup"><span data-stu-id="4e3f4-144">Invoke the `AddGoogle` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddGoogle(options => {
                options.ClientId = Configuration["auth:google:clientid"];
                options.ClientSecret = Configuration["auth:google:clientsecret"];
            });    
    ```

### <a name="microsoft-account-authentication"></a><span data-ttu-id="4e3f4-145">Microsoft 계정 인증</span><span class="sxs-lookup"><span data-stu-id="4e3f4-145">Microsoft Account Authentication</span></span>
<span data-ttu-id="4e3f4-146">다음과 같이 변경 *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="4e3f4-146">Make the following changes in *Startup.cs*:</span></span>
- <span data-ttu-id="4e3f4-147">대체는 `UseMicrosoftAccountAuthentication` 메서드 호출의 `Configure` 메서드 `UseAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="4e3f4-147">Replace the `UseMicrosoftAccountAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="4e3f4-148">호출 된 `AddMicrosoftAccount` 에서 메서드는 `ConfigureServices` 메서드:</span><span class="sxs-lookup"><span data-stu-id="4e3f4-148">Invoke the `AddMicrosoftAccount` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddMicrosoftAccount(options => {
                options.ClientId = Configuration["auth:microsoft:clientid"];
                options.ClientSecret = Configuration["auth:microsoft:clientsecret"];
            });
    ``` 

### <a name="twitter-authentication"></a><span data-ttu-id="4e3f4-149">Twitter 인증</span><span class="sxs-lookup"><span data-stu-id="4e3f4-149">Twitter Authentication</span></span>
<span data-ttu-id="4e3f4-150">다음과 같이 변경 *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="4e3f4-150">Make the following changes in *Startup.cs*:</span></span>
- <span data-ttu-id="4e3f4-151">대체는 `UseTwitterAuthentication` 메서드 호출의 `Configure` 메서드 `UseAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="4e3f4-151">Replace the `UseTwitterAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>
 
    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="4e3f4-152">호출 된 `AddTwitter` 에서 메서드는 `ConfigureServices` 메서드:</span><span class="sxs-lookup"><span data-stu-id="4e3f4-152">Invoke the `AddTwitter` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddTwitter(options => {
                options.ConsumerKey = Configuration["auth:twitter:consumerkey"];
                options.ConsumerSecret = Configuration["auth:twitter:consumersecret"];
            });
    ```

### <a name="setting-default-authentication-schemes"></a><span data-ttu-id="4e3f4-153">기본 인증 체계를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="4e3f4-153">Setting Default Authentication Schemes</span></span>
<span data-ttu-id="4e3f4-154">1.x에서는 `AutomaticAuthenticate` 및 `AutomaticChallenge` 속성의 서로을 하나의 인증 구성표에 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4e3f4-154">In 1.x, the `AutomaticAuthenticate` and `AutomaticChallenge` properties were intended to be set on a single authentication scheme.</span></span> <span data-ttu-id="4e3f4-155">이 적용할 수 있는 좋은 방법이 없었습니다.</span><span class="sxs-lookup"><span data-stu-id="4e3f4-155">There was no good way to enforce this.</span></span>

<span data-ttu-id="4e3f4-156">2.0에서는 이러한 두 속성에서 제거 되었을 개인에 대 한 플래그 `AuthenticationOptions` 인스턴스 및 기본으로 이동 되어 [AuthenticationOptions](/aspnet/core/api/microsoft.aspnetcore.builder.authenticationoptions) 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="4e3f4-156">In 2.0, these two properties have been removed as flags on the individual `AuthenticationOptions` instance and have moved into the base [AuthenticationOptions](/aspnet/core/api/microsoft.aspnetcore.builder.authenticationoptions) class.</span></span> <span data-ttu-id="4e3f4-157">속성에서 구성할 수 있습니다는 `AddAuthentication` 내에서 메서드 호출에서 `ConfigureServices` 방식의 *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="4e3f4-157">The properties can be configured in the `AddAuthentication` method call within the `ConfigureServices` method of *Startup.cs*:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme);
```

<span data-ttu-id="4e3f4-158">또는 오버 로드 된 버전의를 사용 하 여는 `AddAuthentication` 둘 이상의 속성을 설정 하는 메서드.</span><span class="sxs-lookup"><span data-stu-id="4e3f4-158">Alternatively, use an overloaded version of the `AddAuthentication` method to set more than one property.</span></span> <span data-ttu-id="4e3f4-159">다음 오버 로드 된 메서드 예제에서는 기본 스키마로 설정 되어 `CookieAuthenticationDefaults.AuthenticationScheme`합니다.</span><span class="sxs-lookup"><span data-stu-id="4e3f4-159">In the following overloaded method example, the default scheme is set to `CookieAuthenticationDefaults.AuthenticationScheme`.</span></span> <span data-ttu-id="4e3f4-160">인증 체계 또는 개별 내에서 지정할 수 있습니다 `[Authorize]` 특성 또는 권한 부여 정책.</span><span class="sxs-lookup"><span data-stu-id="4e3f4-160">The authentication scheme may alternatively be specified within your individual `[Authorize]` attributes or authorization policies.</span></span>

```csharp
services.AddAuthentication(options => {
    options.DefaultScheme = CookieAuthenticationDefaults.AuthenticationScheme;
    options.DefaultChallengeScheme = OpenIdConnectDefaults.AuthenticationScheme;
});
```

<span data-ttu-id="4e3f4-161">다음 조건 중 하나 이면 2.0에서 기본 체계를 정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="4e3f4-161">Define a default scheme in 2.0 if one of the following conditions is true:</span></span>
- <span data-ttu-id="4e3f4-162">사용자 자동으로 로그인 하려면</span><span class="sxs-lookup"><span data-stu-id="4e3f4-162">You want the user to be automatically signed in</span></span>
- <span data-ttu-id="4e3f4-163">사용 된 `[Authorize]` 구성표를 지정 하지 않고 특성 또는 권한 부여 정책</span><span class="sxs-lookup"><span data-stu-id="4e3f4-163">You use the `[Authorize]` attribute or authorization policies without specifying schemes</span></span>

<span data-ttu-id="4e3f4-164">이 규칙의 예외는 `AddIdentity` 메서드.</span><span class="sxs-lookup"><span data-stu-id="4e3f4-164">An exception to this rule is the `AddIdentity` method.</span></span> <span data-ttu-id="4e3f4-165">이 메서드는 쿠키 및 인증 하 고 인증 구성표는 응용 프로그램 쿠키를 기본 설정에 대 한 추가 `IdentityConstants.ApplicationScheme`합니다.</span><span class="sxs-lookup"><span data-stu-id="4e3f4-165">This method adds cookies for you and sets the default authenticate and challenge schemes to the application cookie `IdentityConstants.ApplicationScheme`.</span></span> <span data-ttu-id="4e3f4-166">또한 기본 로그인 구성표 외부 쿠키를 설정 `IdentityConstants.ExternalScheme`합니다.</span><span class="sxs-lookup"><span data-stu-id="4e3f4-166">Additionally, it sets the default sign-in scheme to the external cookie `IdentityConstants.ExternalScheme`.</span></span>

<a name="obsolete-interface"></a>

## <a name="use-httpcontext-authentication-extensions"></a><span data-ttu-id="4e3f4-167">HttpContext 인증 확장 프로그램을 사용 하 여</span><span class="sxs-lookup"><span data-stu-id="4e3f4-167">Use HttpContext Authentication Extensions</span></span>
<span data-ttu-id="4e3f4-168">`IAuthenticationManager` 인터페이스는 1.x 인증 시스템의 주 진입점입니다.</span><span class="sxs-lookup"><span data-stu-id="4e3f4-168">The `IAuthenticationManager` interface is the main entry point into the 1.x authentication system.</span></span> <span data-ttu-id="4e3f4-169">새로운 집합으로 대체 되었습니다 `HttpContext` 의 확장 메서드는 `Microsoft.AspNetCore.Authentication` 네임 스페이스입니다.</span><span class="sxs-lookup"><span data-stu-id="4e3f4-169">It has been replaced with a new set of `HttpContext` extension methods in the `Microsoft.AspNetCore.Authentication` namespace.</span></span>

<span data-ttu-id="4e3f4-170">예를 들어 1.x 프로젝트 참조는 `Authentication` 속성:</span><span class="sxs-lookup"><span data-stu-id="4e3f4-170">For example, 1.x projects reference an `Authentication` property:</span></span>

<span data-ttu-id="4e3f4-171">[!code-csharp[Main](../1x-to-2x/samples/AspNetCoreDotNetCore1.1App/AspNetCoreDotNetCore1.1App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]</span><span class="sxs-lookup"><span data-stu-id="4e3f4-171">[!code-csharp[Main](../1x-to-2x/samples/AspNetCoreDotNetCore1.1App/AspNetCoreDotNetCore1.1App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]</span></span>

<span data-ttu-id="4e3f4-172">2.0 프로젝트에서 가져올는 `Microsoft.AspNetCore.Authentication` 네임 스페이스를 삭제 하 고는 `Authentication` 속성 참조:</span><span class="sxs-lookup"><span data-stu-id="4e3f4-172">In 2.0 projects, import the `Microsoft.AspNetCore.Authentication` namespace, and delete the `Authentication` property references:</span></span>

<span data-ttu-id="4e3f4-173">[!code-csharp[Main](../1x-to-2x/samples/AspNetCoreDotNetCore2.0App/AspNetCoreDotNetCore2.0App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]</span><span class="sxs-lookup"><span data-stu-id="4e3f4-173">[!code-csharp[Main](../1x-to-2x/samples/AspNetCoreDotNetCore2.0App/AspNetCoreDotNetCore2.0App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]</span></span>

<a name="windows-auth-changes"></a>

## <a name="windows-authentication-httpsys--iisintegration"></a><span data-ttu-id="4e3f4-174">Windows 인증 (HTTP.sys / IISIntegration)</span><span class="sxs-lookup"><span data-stu-id="4e3f4-174">Windows Authentication (HTTP.sys / IISIntegration)</span></span>
<span data-ttu-id="4e3f4-175">Windows 인증의 두 가지 변형이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4e3f4-175">There are two variations of Windows authentication:</span></span>
1. <span data-ttu-id="4e3f4-176">호스트에는 인증 된 사용자만 허용</span><span class="sxs-lookup"><span data-stu-id="4e3f4-176">The host only allows authenticated users</span></span>
2. <span data-ttu-id="4e3f4-177">호스트는 수 있도록 모두 익명 및 인증 된 사용자</span><span class="sxs-lookup"><span data-stu-id="4e3f4-177">The host allows both anonymous and authenticated users</span></span>

<span data-ttu-id="4e3f4-178">위에서 설명한 첫 번째 변형 2.0 변경의 영향을 받지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4e3f4-178">The first variation described above is unaffected by the 2.0 changes.</span></span>

<span data-ttu-id="4e3f4-179">위에서 설명한 두 번째 변형은 2.0 변경의 영향을 줍니다.</span><span class="sxs-lookup"><span data-stu-id="4e3f4-179">The second variation described above is affected by the 2.0 changes.</span></span> <span data-ttu-id="4e3f4-180">예를 들어, 있습니다 수 허용 해서는 익명 사용자가 IIS에 응용 프로그램에 또는 [HTTP.sys](xref:fundamentals/servers/weblistener) 컨트롤러 수준에서 권한을 부여 하지만 사용자가 계층입니다.</span><span class="sxs-lookup"><span data-stu-id="4e3f4-180">As an example, you may be allowing anonymous users into your application at the IIS or [HTTP.sys](xref:fundamentals/servers/weblistener) layer but authorizing users at the Controller level.</span></span> <span data-ttu-id="4e3f4-181">이 시나리오에서는 기본 스키마로 설정 `IISDefaults.AuthenticationScheme` 에 `ConfigureServices` 방식의 *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="4e3f4-181">In this scenario, set the default scheme to `IISDefaults.AuthenticationScheme` in the `ConfigureServices` method of *Startup.cs*:</span></span>

```csharp
services.AddAuthentication(IISDefaults.AuthenticationScheme);
```

<span data-ttu-id="4e3f4-182">기본 체계를 설정 하려면 그에 따라 제대로 수행 되지 않습니다 authorize 요청의 작동에서 보도록 하기 위해서입니다.</span><span class="sxs-lookup"><span data-stu-id="4e3f4-182">Failure to set the default scheme accordingly prevents the authorize request to challenge from working.</span></span>

<a name="identity-cookie-options"></a>

## <a name="identitycookieoptions-instances"></a><span data-ttu-id="4e3f4-183">IdentityCookieOptions 인스턴스</span><span class="sxs-lookup"><span data-stu-id="4e3f4-183">IdentityCookieOptions Instances</span></span>
<span data-ttu-id="4e3f4-184">2.0 변경의 부작용은 옵션 쿠키 옵션 인스턴스 대신 명명 된 사용으로 전환 합니다.</span><span class="sxs-lookup"><span data-stu-id="4e3f4-184">A side effect of the 2.0 changes is the switch to using named options instead of cookie options instances.</span></span> <span data-ttu-id="4e3f4-185">Identity 쿠키 구성표 이름을 사용자 지정 하는 기능이 제거 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4e3f4-185">The ability to customize the Identity cookie scheme names is removed.</span></span>

<span data-ttu-id="4e3f4-186">예를 들어 1.x 프로젝트 사용 [생성자 삽입](xref:mvc/controllers/dependency-injection#constructor-injection) 전달 하는 `IdentityCookieOptions` 매개 변수를 *AccountController.cs*합니다.</span><span class="sxs-lookup"><span data-stu-id="4e3f4-186">For example, 1.x projects use [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) to pass an `IdentityCookieOptions` parameter into *AccountController.cs*.</span></span> <span data-ttu-id="4e3f4-187">외부 쿠키 인증 체계는에서 제공된 된 인스턴스에 액세스 합니다.</span><span class="sxs-lookup"><span data-stu-id="4e3f4-187">The external cookie authentication scheme is accessed from the provided instance:</span></span>

<span data-ttu-id="4e3f4-188">[!code-csharp[Main](../1x-to-2x/samples/AspNetCoreDotNetCore1.1App/AspNetCoreDotNetCore1.1App/Controllers/AccountController.cs?name=snippet_AccountControllerConstructor&highlight=4,11)]</span><span class="sxs-lookup"><span data-stu-id="4e3f4-188">[!code-csharp[Main](../1x-to-2x/samples/AspNetCoreDotNetCore1.1App/AspNetCoreDotNetCore1.1App/Controllers/AccountController.cs?name=snippet_AccountControllerConstructor&highlight=4,11)]</span></span>

<span data-ttu-id="4e3f4-189">앞에서 언급 한 생성자 삽입 2.0 프로젝트에서 불필요 하 게 만드는 및 `_externalCookieScheme` 필드를 삭제할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4e3f4-189">The aforementioned constructor injection becomes unnecessary in 2.0 projects, and the `_externalCookieScheme` field can be deleted:</span></span>

<span data-ttu-id="4e3f4-190">[!code-csharp[Main](../1x-to-2x/samples/AspNetCoreDotNetCore2.0App/AspNetCoreDotNetCore2.0App/Controllers/AccountController.cs?name=snippet_AccountControllerConstructor)]</span><span class="sxs-lookup"><span data-stu-id="4e3f4-190">[!code-csharp[Main](../1x-to-2x/samples/AspNetCoreDotNetCore2.0App/AspNetCoreDotNetCore2.0App/Controllers/AccountController.cs?name=snippet_AccountControllerConstructor)]</span></span>

<span data-ttu-id="4e3f4-191">`IdentityConstants.ExternalScheme` 상수를 직접 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4e3f4-191">The `IdentityConstants.ExternalScheme` constant can be used directly:</span></span>

<span data-ttu-id="4e3f4-192">[!code-csharp[Main](../1x-to-2x/samples/AspNetCoreDotNetCore2.0App/AspNetCoreDotNetCore2.0App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]</span><span class="sxs-lookup"><span data-stu-id="4e3f4-192">[!code-csharp[Main](../1x-to-2x/samples/AspNetCoreDotNetCore2.0App/AspNetCoreDotNetCore2.0App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]</span></span>

<a name="navigation-properties"></a>

## <a name="add-identityuser-poco-navigation-properties"></a><span data-ttu-id="4e3f4-193">IdentityUser POCO 탐색 속성 추가</span><span class="sxs-lookup"><span data-stu-id="4e3f4-193">Add IdentityUser POCO Navigation Properties</span></span>
<span data-ttu-id="4e3f4-194">자료의 Entity Framework (EF) 핵심 탐색 속성 `IdentityUser` POCO (Plain Old CLR Object) 제거 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="4e3f4-194">The Entity Framework (EF) Core navigation properties of the base `IdentityUser` POCO (Plain Old CLR Object) have been removed.</span></span> <span data-ttu-id="4e3f4-195">이러한 속성을 사용 하는 1.x 프로젝트에 수동으로 추가 다시 2.0 프로젝트에:</span><span class="sxs-lookup"><span data-stu-id="4e3f4-195">If your 1.x project used these properties, manually add them back to the 2.0 project:</span></span>

```csharp
/// <summary>
/// Navigation property for the roles this user belongs to.
/// </summary>
public virtual ICollection<IdentityUserRole<int>> Roles { get; } = new List<IdentityUserRole<int>>();

/// <summary>
/// Navigation property for the claims this user possesses.
/// </summary>
public virtual ICollection<IdentityUserClaim<int>> Claims { get; } = new List<IdentityUserClaim<int>>();

/// <summary>
/// Navigation property for this users login accounts.
/// </summary>
public virtual ICollection<IdentityUserLogin<int>> Logins { get; } = new List<IdentityUserLogin<int>>();
```

<span data-ttu-id="4e3f4-196">중복 된 외래 키 EF 코어 마이그레이션을 실행 하는 경우를 방지 하려면 다음을 추가 하면 `IdentityDbContext` 클래스의 `OnModelCreating` 메서드 (후의 `base.OnModelCreating();` 호출):</span><span class="sxs-lookup"><span data-stu-id="4e3f4-196">To prevent duplicate foreign keys when running EF Core Migrations, add the following to your `IdentityDbContext` class' `OnModelCreating` method (after the `base.OnModelCreating();` call):</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder builder)
{
    base.OnModelCreating(builder);
    // Customize the ASP.NET Identity model and override the defaults if needed.
    // For example, you can rename the ASP.NET Identity table names and more.
    // Add your customizations after calling base.OnModelCreating(builder);

    builder.Entity<ApplicationUser>()
        .HasMany(e => e.Claims)
        .WithOne()
        .HasForeignKey(e => e.UserId)
        .IsRequired()
        .OnDelete(DeleteBehavior.Cascade);

    builder.Entity<ApplicationUser>()
        .HasMany(e => e.Logins)
        .WithOne()
        .HasForeignKey(e => e.UserId)
        .IsRequired()
        .OnDelete(DeleteBehavior.Cascade);

    builder.Entity<ApplicationUser>()
        .HasMany(e => e.Roles)
        .WithOne()
        .HasForeignKey(e => e.UserId)
        .IsRequired()
        .OnDelete(DeleteBehavior.Cascade);
}
```

<a name="synchronous-method-removal"></a>

## <a name="replace-getexternalauthenticationschemes"></a><span data-ttu-id="4e3f4-197">GetExternalAuthenticationSchemes 바꾸기</span><span class="sxs-lookup"><span data-stu-id="4e3f4-197">Replace GetExternalAuthenticationSchemes</span></span>
<span data-ttu-id="4e3f4-198">동기 메서드로 `GetExternalAuthenticationSchemes` 비동기 버전 제거 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="4e3f4-198">The synchronous method `GetExternalAuthenticationSchemes` was removed in favor of an asynchronous version.</span></span> <span data-ttu-id="4e3f4-199">1.x 프로젝트 다음 코드를 있는 *ManageController.cs*:</span><span class="sxs-lookup"><span data-stu-id="4e3f4-199">1.x projects have the following code in *ManageController.cs*:</span></span>

<span data-ttu-id="4e3f4-200">[!code-csharp[Main](../1x-to-2x/samples/AspNetCoreDotNetCore1.1App/AspNetCoreDotNetCore1.1App/Controllers/ManageController.cs?name=snippet_GetExternalAuthenticationSchemes)]</span><span class="sxs-lookup"><span data-stu-id="4e3f4-200">[!code-csharp[Main](../1x-to-2x/samples/AspNetCoreDotNetCore1.1App/AspNetCoreDotNetCore1.1App/Controllers/ManageController.cs?name=snippet_GetExternalAuthenticationSchemes)]</span></span>

<span data-ttu-id="4e3f4-201">이 메서드의 나타납니다 *Login.cshtml* 너무:</span><span class="sxs-lookup"><span data-stu-id="4e3f4-201">This method appears in *Login.cshtml* too:</span></span>

<span data-ttu-id="4e3f4-202">[!code-cshtml[Main](../1x-to-2x/samples/AspNetCoreDotNetCore1.1App/AspNetCoreDotNetCore1.1App/Views/Account/Login.cshtml?range=62,75-84)]</span><span class="sxs-lookup"><span data-stu-id="4e3f4-202">[!code-cshtml[Main](../1x-to-2x/samples/AspNetCoreDotNetCore1.1App/AspNetCoreDotNetCore1.1App/Views/Account/Login.cshtml?range=62,75-84)]</span></span>

<span data-ttu-id="4e3f4-203">2.0 프로젝트에서 사용 하 여는 `GetExternalAuthenticationSchemesAsync` 메서드:</span><span class="sxs-lookup"><span data-stu-id="4e3f4-203">In 2.0 projects, use the `GetExternalAuthenticationSchemesAsync` method:</span></span>

<span data-ttu-id="4e3f4-204">[!code-csharp[Main](../1x-to-2x/samples/AspNetCoreDotNetCore2.0App/AspNetCoreDotNetCore2.0App/Controllers/ManageController.cs?name=snippet_GetExternalAuthenticationSchemesAsync)]</span><span class="sxs-lookup"><span data-stu-id="4e3f4-204">[!code-csharp[Main](../1x-to-2x/samples/AspNetCoreDotNetCore2.0App/AspNetCoreDotNetCore2.0App/Controllers/ManageController.cs?name=snippet_GetExternalAuthenticationSchemesAsync)]</span></span>

<span data-ttu-id="4e3f4-205">*Login.cshtml*, `AuthenticationScheme` 액세스 속성은 `foreach` 루프 변경 `Name`:</span><span class="sxs-lookup"><span data-stu-id="4e3f4-205">In *Login.cshtml*, the `AuthenticationScheme` property accessed in the `foreach` loop changes to `Name`:</span></span>

<span data-ttu-id="4e3f4-206">[!code-cshtml[Main](../1x-to-2x/samples/AspNetCoreDotNetCore2.0App/AspNetCoreDotNetCore2.0App/Views/Account/Login.cshtml?range=62,75-84)]</span><span class="sxs-lookup"><span data-stu-id="4e3f4-206">[!code-cshtml[Main](../1x-to-2x/samples/AspNetCoreDotNetCore2.0App/AspNetCoreDotNetCore2.0App/Views/Account/Login.cshtml?range=62,75-84)]</span></span>

<a name="property-change"></a>

## <a name="manageloginsviewmodel-property-change"></a><span data-ttu-id="4e3f4-207">ManageLoginsViewModel 속성 변경</span><span class="sxs-lookup"><span data-stu-id="4e3f4-207">ManageLoginsViewModel Property Change</span></span>
<span data-ttu-id="4e3f4-208">A `ManageLoginsViewModel` 개체가 사용 되는 `ManageLogins` 의 동작 *ManageController.cs*합니다.</span><span class="sxs-lookup"><span data-stu-id="4e3f4-208">A `ManageLoginsViewModel` object is used in the `ManageLogins` action of *ManageController.cs*.</span></span> <span data-ttu-id="4e3f4-209">1.x 프로젝트에서 개체의 `OtherLogins` 속성 반환 형식이 `IList<AuthenticationDescription>`합니다.</span><span class="sxs-lookup"><span data-stu-id="4e3f4-209">In 1.x projects, the object's `OtherLogins` property return type is `IList<AuthenticationDescription>`.</span></span> <span data-ttu-id="4e3f4-210">이 반환 형식은 가져오기를 `Microsoft.AspNetCore.Http.Authentication`:</span><span class="sxs-lookup"><span data-stu-id="4e3f4-210">This return type requires an import of `Microsoft.AspNetCore.Http.Authentication`:</span></span>

<span data-ttu-id="4e3f4-211">[!code-csharp[Main](../1x-to-2x/samples/AspNetCoreDotNetCore1.1App/AspNetCoreDotNetCore1.1App/Models/ManageViewModels/ManageLoginsViewModel.cs?name=snippet_ManageLoginsViewModel&highlight=2,11)]</span><span class="sxs-lookup"><span data-stu-id="4e3f4-211">[!code-csharp[Main](../1x-to-2x/samples/AspNetCoreDotNetCore1.1App/AspNetCoreDotNetCore1.1App/Models/ManageViewModels/ManageLoginsViewModel.cs?name=snippet_ManageLoginsViewModel&highlight=2,11)]</span></span>

<span data-ttu-id="4e3f4-212">2.0 프로젝트에서 반환 형식으로 변경 `IList<AuthenticationScheme>`합니다.</span><span class="sxs-lookup"><span data-stu-id="4e3f4-212">In 2.0 projects, the return type changes to `IList<AuthenticationScheme>`.</span></span> <span data-ttu-id="4e3f4-213">이 새로운 반환 형식을 바꿀 필요는 `Microsoft.AspNetCore.Http.Authentication` 를 가져오려면는 `Microsoft.AspNetCore.Authentication` 가져오기.</span><span class="sxs-lookup"><span data-stu-id="4e3f4-213">This new return type requires replacing the `Microsoft.AspNetCore.Http.Authentication` import with a `Microsoft.AspNetCore.Authentication` import.</span></span>

<span data-ttu-id="4e3f4-214">[!code-csharp[Main](../1x-to-2x/samples/AspNetCoreDotNetCore2.0App/AspNetCoreDotNetCore2.0App/Models/ManageViewModels/ManageLoginsViewModel.cs?name=snippet_ManageLoginsViewModel&highlight=2,11)]</span><span class="sxs-lookup"><span data-stu-id="4e3f4-214">[!code-csharp[Main](../1x-to-2x/samples/AspNetCoreDotNetCore2.0App/AspNetCoreDotNetCore2.0App/Models/ManageViewModels/ManageLoginsViewModel.cs?name=snippet_ManageLoginsViewModel&highlight=2,11)]</span></span>

<a name="additional-resources"></a>

## <a name="additional-resources"></a><span data-ttu-id="4e3f4-215">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="4e3f4-215">Additional Resources</span></span>
<span data-ttu-id="4e3f4-216">추가 세부 정보 및 토론에 대 한 참조는 [Auth 2.0에 대 한 토론](https://github.com/aspnet/Security/issues/1338) GitHub에서 문제입니다.</span><span class="sxs-lookup"><span data-stu-id="4e3f4-216">For additional details and discussion, see the [Discussion for Auth 2.0](https://github.com/aspnet/Security/issues/1338) issue on GitHub.</span></span>
