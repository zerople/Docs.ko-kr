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
ms.openlocfilehash: b4e67e7cfea3c01e3ca8c0d5df2a04e789749932
ms.sourcegitcommit: f8f6b5934bd071a349f5bc1e389365c52b1c00fa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/14/2017
---
# <a name="migrating-authentication-and-identity-to-aspnet-core-20"></a>마이그레이션 인증 및 ASP.NET 코어 2.0 Id

여 [Scott Addie](https://github.com/scottaddie) 및 [Hao 둘러싼](https://github.com/HaoK)

ASP.NET Core 2.0 인증을 위한 새 모델을 포함 하 고 [Identity](xref:security/authentication/identity) 서비스를 사용 하 여 구성을 간소화 합니다. 아래 설명 된 대로 새 모델을 사용 하도록 인증 또는 Id를 사용 하는 ASP.NET Core 1.x 응용 프로그램을 업데이트할 수 있습니다.

<a name="auth-middleware"></a>

## <a name="authentication-middleware-and-services"></a>인증 미들웨어 및 서비스
1.x 프로젝트에서 인증 미들웨어를 통해 구성 됩니다. 미들웨어 메서드를 지원 하려면 각 인증 체계에 대해 호출 됩니다.

에 Id를 가진 Facebook 인증을 구성 하는 다음 1.x 예제 *Startup.cs*:

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

2.0 프로젝트에서 서비스를 통해 인증이 구성 됩니다. 에 등록 된 각 인증 체계는 `ConfigureServices` 방식의 *Startup.cs*합니다. `UseIdentity` 메서드 아래 템플릿으로 바뀝니다 `UseAuthentication`합니다.

에 Id를 가진 Facebook 인증을 구성 하는 다음 2.0 예제 *Startup.cs*:

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

`UseAuthentication` 메서드 자동 인증 및 원격 인증 요청 처리를 담당 하는 단일 인증 미들웨어 구성 요소를 추가 합니다. 단일의 공통 미들웨어 구성 요소와 개별 미들웨어 구성 요소를 모두 대체합니다.

다음은 각 주요 인증 체계에 대 한 마이그레이션 지침 2.0입니다.

### <a name="cookie-based-authentication"></a>쿠키 기반 인증
아래 두 옵션 중 하나를 선택 하 고에 필요한 사항을 변경한 *Startup.cs*:

1. Id 가진 쿠키 사용
    - 대체 `UseIdentity` 와 `UseAuthentication` 에 `Configure` 메서드:

        ```csharp
        app.UseAuthentication();
        ```

    - 호출는 `AddIdentity` 에서 메서드는 `ConfigureServices` 메서드 쿠키 인증 서비스를 추가 합니다.
    - 필요에 따라 호출는 `ConfigureApplicationCookie` 또는 `ConfigureExternalCookie` 에서 메서드는 `ConfigureServices` 메서드 Id 쿠키 설정을 조정 합니다.

        ```csharp
        services.AddIdentity<ApplicationUser, IdentityRole>()
                .AddEntityFrameworkStores<ApplicationDbContext>()
                .AddDefaultTokenProviders();
    
        services.ConfigureApplicationCookie(options => options.LoginPath = "/Account/LogIn");
        ```

2. Identity 없이 쿠키를 사용 하 여
    - 대체는 `UseCookieAuthentication` 메서드 호출의 `Configure` 메서드 `UseAuthentication`:
  
        ```csharp
        app.UseAuthentication();
        ```
 
    - 호출 된 `AddAuthentication` 및 `AddCookie` 의 메서드는 `ConfigureServices` 메서드:

        ```csharp
        // If you don't want the cookie to be automatically authenticated and assigned to HttpContext.User, 
        // remove the CookieAuthenticationDefaults.AuthenticationScheme parameter passed to AddAuthentication.
        services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
                .AddCookie(options => {
                    options.LoginPath = "/Account/LogIn";
                    options.LogoutPath = "/Account/LogOff";
                });
        ```

### <a name="jwt-bearer-authentication"></a>JWT 전달자 인증
다음과 같이 변경 *Startup.cs*:
- 대체는 `UseJwtBearerAuthentication` 메서드 호출의 `Configure` 메서드 `UseAuthentication`:
 
    ```csharp
    app.UseAuthentication();
    ```

- 호출 된 `AddJwtBearer` 에서 메서드는 `ConfigureServices` 메서드:

    ```csharp
    services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
            .AddJwtBearer(options => {
                options.Audience = "http://localhost:5001/";
                options.Authority = "http://localhost:5000/";
            });
    ```

    전달 하 여 기본 체계를 설정 해야 하므로이 코드 조각은 Id를 사용 하지 않는 `JwtBearerDefaults.AuthenticationScheme` 에 `AddAuthentication` 메서드.

### <a name="openid-connect-oidc-authentication"></a>OpenID Connect (OIDC) 인증
다음과 같이 변경 *Startup.cs*:

- 대체는 `UseOpenIdConnectAuthentication` 메서드 호출의 `Configure` 메서드 `UseAuthentication`:

    ```csharp
    app.UseAuthentication();
    ```

- 호출 된 `AddOpenIdConnect` 에서 메서드는 `ConfigureServices` 메서드:

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

### <a name="facebook-authentication"></a>Facebook 인증
다음과 같이 변경 *Startup.cs*:
- 대체는 `UseFacebookAuthentication` 메서드 호출의 `Configure` 메서드 `UseAuthentication`:
 
    ```csharp
    app.UseAuthentication();
    ```

- 호출 된 `AddFacebook` 에서 메서드는 `ConfigureServices` 메서드:
    
    ```csharp
    services.AddAuthentication()
            .AddFacebook(options => {
                options.AppId = Configuration["auth:facebook:appid"];
                options.AppSecret = Configuration["auth:facebook:appsecret"];
            });
    ```

### <a name="google-authentication"></a>Google 인증
다음과 같이 변경 *Startup.cs*:
- 대체는 `UseGoogleAuthentication` 메서드 호출의 `Configure` 메서드 `UseAuthentication`:
 
    ```csharp
    app.UseAuthentication();
    ```

- 호출 된 `AddGoogle` 에서 메서드는 `ConfigureServices` 메서드:

    ```csharp
    services.AddAuthentication()
            .AddGoogle(options => {
                options.ClientId = Configuration["auth:google:clientid"];
                options.ClientSecret = Configuration["auth:google:clientsecret"];
            });    
    ```

### <a name="microsoft-account-authentication"></a>Microsoft 계정 인증
다음과 같이 변경 *Startup.cs*:
- 대체는 `UseMicrosoftAccountAuthentication` 메서드 호출의 `Configure` 메서드 `UseAuthentication`:

    ```csharp
    app.UseAuthentication();
    ```

- 호출 된 `AddMicrosoftAccount` 에서 메서드는 `ConfigureServices` 메서드:

    ```csharp
    services.AddAuthentication()
            .AddMicrosoftAccount(options => {
                options.ClientId = Configuration["auth:microsoft:clientid"];
                options.ClientSecret = Configuration["auth:microsoft:clientsecret"];
            });
    ``` 

### <a name="twitter-authentication"></a>Twitter 인증
다음과 같이 변경 *Startup.cs*:
- 대체는 `UseTwitterAuthentication` 메서드 호출의 `Configure` 메서드 `UseAuthentication`:
 
    ```csharp
    app.UseAuthentication();
    ```

- 호출 된 `AddTwitter` 에서 메서드는 `ConfigureServices` 메서드:

    ```csharp
    services.AddAuthentication()
            .AddTwitter(options => {
                options.ConsumerKey = Configuration["auth:twitter:consumerkey"];
                options.ConsumerSecret = Configuration["auth:twitter:consumersecret"];
            });
    ```

### <a name="setting-default-authentication-schemes"></a>기본 인증 체계를 설정합니다.
1.x에서는 `AutomaticAuthenticate` 및 `AutomaticChallenge` 속성의 서로을 하나의 인증 구성표에 설정할 수 있습니다. 이 적용할 수 있는 좋은 방법이 없었습니다.

2.0에서는 이러한 두 속성에서 제거 되었을 개인에 대 한 플래그 `AuthenticationOptions` 인스턴스 및 기본으로 이동 되어 [AuthenticationOptions](/aspnet/core/api/microsoft.aspnetcore.builder.authenticationoptions) 클래스입니다. 속성에서 구성할 수 있습니다는 `AddAuthentication` 내에서 메서드 호출에서 `ConfigureServices` 방식의 *Startup.cs*:

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme);
```

또는 오버 로드 된 버전의를 사용 하 여는 `AddAuthentication` 둘 이상의 속성을 설정 하는 메서드. 다음 오버 로드 된 메서드 예제에서는 기본 스키마로 설정 되어 `CookieAuthenticationDefaults.AuthenticationScheme`합니다. 인증 체계 또는 개별 내에서 지정할 수 있습니다 `[Authorize]` 특성 또는 권한 부여 정책.

```csharp
services.AddAuthentication(options => {
    options.DefaultScheme = CookieAuthenticationDefaults.AuthenticationScheme;
    options.DefaultChallengeScheme = OpenIdConnectDefaults.AuthenticationScheme;
});
```

다음 조건 중 하나 이면 2.0에서 기본 체계를 정의 합니다.
- 사용자 자동으로 로그인 하려면
- 사용 된 `[Authorize]` 구성표를 지정 하지 않고 특성 또는 권한 부여 정책

이 규칙의 예외는 `AddIdentity` 메서드. 이 메서드는 쿠키 및 인증 하 고 인증 구성표는 응용 프로그램 쿠키를 기본 설정에 대 한 추가 `IdentityConstants.ApplicationScheme`합니다. 또한 기본 로그인 구성표 외부 쿠키를 설정 `IdentityConstants.ExternalScheme`합니다.

<a name="obsolete-interface"></a>

## <a name="use-httpcontext-authentication-extensions"></a>HttpContext 인증 확장 프로그램을 사용 하 여
`IAuthenticationManager` 인터페이스는 1.x 인증 시스템의 주 진입점입니다. 새로운 집합으로 대체 되었습니다 `HttpContext` 의 확장 메서드는 `Microsoft.AspNetCore.Authentication` 네임 스페이스입니다.

예를 들어 1.x 프로젝트 참조는 `Authentication` 속성:

[!code-csharp[Main](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

2.0 프로젝트에서 가져올는 `Microsoft.AspNetCore.Authentication` 네임 스페이스를 삭제 하 고는 `Authentication` 속성 참조:

[!code-csharp[Main](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<a name="windows-auth-changes"></a>

## <a name="windows-authentication-httpsys--iisintegration"></a>Windows 인증 (HTTP.sys / IISIntegration)
Windows 인증의 두 가지 변형이 있습니다.
1. 호스트에는 인증 된 사용자만 허용
2. 호스트는 수 있도록 모두 익명 및 인증 된 사용자

위에서 설명한 첫 번째 변형 2.0 변경의 영향을 받지 않습니다.

위에서 설명한 두 번째 변형은 2.0 변경의 영향을 줍니다. 예를 들어, 있습니다 수 허용 해서는 익명 사용자가 IIS에 응용 프로그램에 또는 [HTTP.sys](xref:fundamentals/servers/weblistener) 컨트롤러 수준에서 권한을 부여 하지만 사용자가 계층입니다. 이 시나리오에서는 기본 스키마로 설정 `IISDefaults.AuthenticationScheme` 에 `ConfigureServices` 방식의 *Startup.cs*:

```csharp
services.AddAuthentication(IISDefaults.AuthenticationScheme);
```

기본 체계를 설정 하려면 그에 따라 제대로 수행 되지 않습니다 authorize 요청의 작동에서 보도록 하기 위해서입니다.

<a name="identity-cookie-options"></a>

## <a name="identitycookieoptions-instances"></a>IdentityCookieOptions 인스턴스
2.0 변경의 부작용은 옵션 쿠키 옵션 인스턴스 대신 명명 된 사용으로 전환 합니다. Identity 쿠키 구성표 이름을 사용자 지정 하는 기능이 제거 됩니다.

예를 들어 1.x 프로젝트 사용 [생성자 삽입](xref:mvc/controllers/dependency-injection#constructor-injection) 전달 하는 `IdentityCookieOptions` 매개 변수를 *AccountController.cs*합니다. 외부 쿠키 인증 체계는에서 제공된 된 인스턴스에 액세스 합니다.

[!code-csharp[Main](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AccountControllerConstructor&highlight=4,11)]

앞에서 언급 한 생성자 삽입 2.0 프로젝트에서 불필요 하 게 만드는 및 `_externalCookieScheme` 필드를 삭제할 수 있습니다.

[!code-csharp[Main](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AccountControllerConstructor)]

`IdentityConstants.ExternalScheme` 상수를 직접 사용할 수 있습니다.

[!code-csharp[Main](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<a name="navigation-properties"></a>

## <a name="add-identityuser-poco-navigation-properties"></a>IdentityUser POCO 탐색 속성 추가
자료의 Entity Framework (EF) 핵심 탐색 속성 `IdentityUser` POCO (Plain Old CLR Object) 제거 되었습니다. 이러한 속성을 사용 하는 1.x 프로젝트에 수동으로 추가 다시 2.0 프로젝트에:

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

중복 된 외래 키 EF 코어 마이그레이션을 실행 하는 경우를 방지 하려면 다음을 추가 하면 `IdentityDbContext` 클래스의 `OnModelCreating` 메서드 (후의 `base.OnModelCreating();` 호출):

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

## <a name="replace-getexternalauthenticationschemes"></a>GetExternalAuthenticationSchemes 바꾸기
동기 메서드로 `GetExternalAuthenticationSchemes` 비동기 버전 제거 되었습니다. 1.x 프로젝트 다음 코드를 있는 *ManageController.cs*:

[!code-csharp[Main](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/ManageController.cs?name=snippet_GetExternalAuthenticationSchemes)]

이 메서드의 나타납니다 *Login.cshtml* 너무:

[!code-cshtml[Main](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Views/Account/Login.cshtml?range=62,75-84)]

2.0 프로젝트에서 사용 하 여는 `GetExternalAuthenticationSchemesAsync` 메서드:

[!code-csharp[Main](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/ManageController.cs?name=snippet_GetExternalAuthenticationSchemesAsync)]

*Login.cshtml*, `AuthenticationScheme` 액세스 속성은 `foreach` 루프 변경 `Name`:

[!code-cshtml[Main](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Views/Account/Login.cshtml?range=62,75-84)]

<a name="property-change"></a>

## <a name="manageloginsviewmodel-property-change"></a>ManageLoginsViewModel 속성 변경
A `ManageLoginsViewModel` 개체가 사용 되는 `ManageLogins` 의 동작 *ManageController.cs*합니다. 1.x 프로젝트에서 개체의 `OtherLogins` 속성 반환 형식이 `IList<AuthenticationDescription>`합니다. 이 반환 형식은 가져오기를 `Microsoft.AspNetCore.Http.Authentication`:

[!code-csharp[Main](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Models/ManageViewModels/ManageLoginsViewModel.cs?name=snippet_ManageLoginsViewModel&highlight=2,11)]

2.0 프로젝트에서 반환 형식으로 변경 `IList<AuthenticationScheme>`합니다. 이 새로운 반환 형식을 바꿀 필요는 `Microsoft.AspNetCore.Http.Authentication` 를 가져오려면는 `Microsoft.AspNetCore.Authentication` 가져오기.

[!code-csharp[Main](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Models/ManageViewModels/ManageLoginsViewModel.cs?name=snippet_ManageLoginsViewModel&highlight=2,11)]

<a name="additional-resources"></a>

## <a name="additional-resources"></a>추가 리소스
추가 세부 정보 및 토론에 대 한 참조는 [Auth 2.0에 대 한 토론](https://github.com/aspnet/Security/issues/1338) GitHub에서 문제입니다.
