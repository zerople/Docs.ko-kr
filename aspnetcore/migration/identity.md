---
title: "마이그레이션 인증 및 Id"
author: ardalis
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 0db145cb-41a5-448a-b889-72e2d789ad7f
ms.technology: aspnet
ms.prod: asp.net-core
uid: migration/identity
ms.openlocfilehash: c86e9b98bcb43b383ac1077fe2749d0dfcd7392a
ms.sourcegitcommit: fb518f856f31fe53c09196a13309eacb85b37a22
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/08/2017
---
# <a name="migrating-authentication-and-identity"></a>마이그레이션 인증 및 Id

<a name=migration-identity></a>

으로 [Steve Smith](http://ardalis.com)

이전 문서에서는 [ASP.NET MVC 프로젝트에서 ASP.NET Core MVC 구성을 마이그레이션할](configuration.md)합니다. 이 문서에서는 등록, 로그인 및 사용자 관리 기능을 마이그레이션할 했습니다.

## <a name="configure-identity-and-membership"></a>Id 및 멤버 자격을 구성 합니다.

ASP.NET MVC에서 인증 및 id 기능 Startup.Auth.cs 및 App_Start 폴더에 있는 IdentityConfig.cs ASP.NET Identity를 사용 하 여 구성 됩니다. ASP.NET Core MVC에서 이러한 기능에 구성 된 *Startup.cs*합니다.

설치는 `Microsoft.AspNetCore.Identity.EntityFrameworkCore` 및 `Microsoft.AspNetCore.Authentication.Cookies` NuGet 패키지 합니다.

그런 다음 Startup.cs 및 update를 열고는 `ConfigureServices()` Entity Framework 및 Id 서비스를 사용 하는 메서드:

```csharp
public void ConfigureServices(IServiceCollection services)
{
  // Add EF services to the services container.
  services.AddEntityFramework(Configuration)
    .AddSqlServer()
    .AddDbContext<ApplicationDbContext>();

  // Add Identity services to the services container.
  services.AddIdentity<ApplicationUser, IdentityRole>(Configuration)
    .AddEntityFrameworkStores<ApplicationDbContext>();

  services.AddMvc();
}
```

이 시점에서 ASP.NET MVC 프로젝트에서 사용한 아직 하지 않은 위의 코드에서 참조 하는 형식은 두 가지가: `ApplicationDbContext` 및 `ApplicationUser`합니다. 새 *모델* 폴더에서 ASP.NET Core 프로젝트를 하 고 이러한 형식에 해당를 두 개의 클래스를 추가 합니다. ASP.NET MVC에서 이러한 클래스의 버전을 찾이 됩니다 `/Models/IdentityModels.cs`, 있지만 명확 하 게 되므로 마이그레이션된 프로젝트에서 클래스 마다 파일 하나를 사용 하는 것입니다.

ApplicationUser.cs:

<!-- literal_block {"ids": [], "names": [], "highlight_args": {}, "backrefs": [], "dupnames": [], "linenos": false, "classes": [], "xml:space": "preserve", "language": "c#"} -->

```csharp
using Microsoft.AspNetCore.Identity.EntityFrameworkCore;

namespace NewMvc6Project.Models
{
  public class ApplicationUser : IdentityUser
  {
  }
}
```

ApplicationDbContext.cs:

```csharp
using Microsoft.AspNetCore.Identity.EntityFramework;
using Microsoft.Data.Entity;

namespace NewMvc6Project.Models
{
  public class ApplicationDbContext : IdentityDbContext<ApplicationUser>
  {
    public ApplicationDbContext()
    {
      Database.EnsureCreated();
    }

    protected override void OnConfiguring(DbContextOptionsBuilder options)
    {
      options.UseSqlServer();
    }
  }
}
```

ASP.NET Core MVC 시작 웹 프로젝트는 사용자의 많은 사용자 지정 또는 ApplicationDbContext 포함 되지 않습니다. 실제 응용 프로그램을 마이그레이션할 때는 응용 프로그램 (예를 들어 프로그램 DbContext에 경우 DbSet활용모델클래스뿐만아니라응용프로그램의사용자및DbContext클래스의메서드와사용자지정속성을모두마이그레이션할해야합니다.<Album>, 앨범 클래스를 마이그레이션해야 할 물론).

사용 하 여 해당 업데이트 하 여 컴파일하는 데 만들 있습니다 Startup.cs 파일 위치에 이러한 파일을 통해 문:

<!-- literal_block {"ids": [], "names": [], "highlight_args": {}, "backrefs": [], "dupnames": [], "linenos": false, "classes": [], "xml:space": "preserve", "language": "c#"} -->

```csharp
using Microsoft.Framework.ConfigurationModel;
using Microsoft.AspNetCore.Hosting;
using NewMvc6Project.Models;
using Microsoft.AspNetCore.Identity;
```

응용 프로그램 인증 및 id 서비스를 지원 하도록 준비 되었습니다.-사용자에 게 이러한 기능을 갖추고 하기만 합니다.

## <a name="migrate-registration-and-login-logic"></a>등록 및 로그인 논리 마이그레이션

응용 프로그램 및 Entity Framework 및 SQL Server를 사용 하 여 구성 하는 데이터 액세스에 대해 구성 하는 id 서비스를 우리 준비가 이제 응용 프로그램에 등록 및 로그인에 대 한 지원을 추가 합니다. 이전에 설명한 대로 [마이그레이션 프로세스의 앞부분에 나오는](mvc.md#migrate-layout-file) _LoginPartial _Layout.cshtml에 대 한 참조를 주석으로 했습니다. 이제 해당 코드를 반환 합니다. 주석 처리를 제거, 로그인 기능을 지 원하는 데 필요한 컨트롤러를 추가 하는 시간입니다.

_Layout.cshtml; 업데이트 주석 처리 제거는 @Html.Partial 줄:

<!-- literal_block {"ids": [], "names": [], "highlight_args": {}, "backrefs": [], "dupnames": [], "linenos": false, "classes": [], "xml:space": "preserve", "language": "none"} -->

```none
      <li>@Html.ActionLink("Contact", "Contact", "Home")</li>
    </ul>
    @*@Html.Partial("_LoginPartial")*@
  </div>
</div>
```

이제, _LoginPartial 호출 뷰/공유 폴더를 새 MVC 뷰 페이지를 추가 합니다.

다음 코드로 _LoginPartial.cshtml 업데이트 (모든 내용을 바꾸기):

<!-- literal_block {"ids": [], "names": [], "highlight_args": {}, "backrefs": [], "dupnames": [], "linenos": false, "classes": [], "xml:space": "preserve", "language": "c#"} -->

```csharp
@inject SignInManager<User> SignInManager
@inject UserManager<User> UserManager

@if (SignInManager.IsSignedIn(User))
{
    <form asp-area="" asp-controller="Account" asp-action="LogOff" method="post" id="logoutForm" class="navbar-right">
        <ul class="nav navbar-nav navbar-right">
            <li>
                <a asp-area="" asp-controller="Manage" asp-action="Index" title="Manage">Hello @UserManager.GetUserName(User)!</a>
            </li>
            <li>
                <button type="submit" class="btn btn-link navbar-btn navbar-link">Log off</button>
            </li>
        </ul>
    </form>
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li><a asp-area="" asp-controller="Account" asp-action="Register">Register</a></li>
        <li><a asp-area="" asp-controller="Account" asp-action="Login">Log in</a></li>
    </ul>
}
```

이 시점에서 브라우저에서 사이트를 새로 고칠 수 있습니다.

## <a name="summary"></a>요약

ASP.NET Core에서는 ASP.NET Id 기능을 변경 합니다. 이 문서에서 ASP.NET Core에는 ASP.NET Identity의 인증 및 사용자 관리 기능을 마이그레이션하는 방법에 알아보았습니다.
