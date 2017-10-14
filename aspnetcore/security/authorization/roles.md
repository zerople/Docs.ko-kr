---
title: "역할 기반 권한 부여"
author: rick-anderson
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 5e014da1-8bc0-409b-951a-88b92c661fdf
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/authorization/roles
ms.openlocfilehash: 3dfba8a492c5d592b1fa9c8893a0ec4b1a2e70b9
ms.sourcegitcommit: 8f4d4fad1ca27adf9e396f5c205c9875a3963664
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/13/2017
---
# <a name="role-based-authorization"></a>역할 기반 권한 부여

<a name="security-authorization-role-based"></a>

Id를 만들 때 하나 이상의 역할에 속할 수, Scott 사용자 역할에만 속할 수 하는 동안 Tracy 관리자 및 사용자 역할에 속할 수는 예입니다. 이러한 역할이 만들고 관리 하는 방법의 권한 부여 프로세스는 백업 저장소에 따라 달라 집니다. 역할을 통해 개발자에 게 노출 되는 [IsInRole](https://docs.microsoft.com/dotnet/api/system.security.principal.genericprincipal.isinrole) 속성에는 [ClaimsPrincipal](https://docs.microsoft.com/dotnet/api/system.security.claims.claimsprincipal) 클래스입니다.

## <a name="adding-role-checks"></a>추가 역할 검사

역할 기반 권한 부여 확인은 선언적-개발자 포함을 컨트롤러나는 컨트롤러 내 작업에 대해 해당 코드 내에서 현재 사용자는 요청 된 리소스에 액세스의 구성원 이어야 하는 역할을 지정 합니다.

예를 들어 다음 코드는 제한 작업에 대 한 액세스에는 `AdministrationController` 의 구성원 인 사용자에 게는 `Administrator` 그룹입니다.

```csharp
[Authorize(Roles = "Administrator")]
public class AdministrationController : Controller
{
}
```

쉼표로 구분 된 목록으로 여러 역할을 지정할 수 있습니다.

```csharp
[Authorize(Roles = "HRManager,Finance")]
public class SalaryController : Controller
{
}
```

이 컨트롤러는 구성원 인 사용자가 액세스할 수만의 `HRManager` 역할 또는 `Finance` 역할입니다.

에 액세스 하는 사용자 지정 된 모든 역할의 멤버 여야 합니다. 그런 다음 여러 특성을 적용 하는 경우 다음 예제에서는 사용자 둘 다의 구성원 이어야 하 고 `PowerUser` 및 `ControlPanelUser` 역할입니다.

```csharp
[Authorize(Roles = "PowerUser")]
[Authorize(Roles = "ControlPanelUser")]
public class ControlPanelController : Controller
{
}
```

작업 수준;에서 추가 역할 권한 부여 특성을 적용 하 여 액세스를 추가로 제한할 수 있습니다.

```csharp
[Authorize(Roles = "Administrator, PowerUser")]
public class ControlPanelController : Controller
{
    public ActionResult SetTime()
    {
    }

    [Authorize(Roles = "Administrator")]
    public ActionResult ShutDown()
    {
    }
}
```

이전 코드 조각 멤버에는 `Administrator` 역할 또는 `PowerUser` 역할 컨트롤러에 액세스할 수 있습니다 및 `SetTime` 동작 하지만의 구성원만는 `Administrator` 역할이 액세스할 수는 `ShutDown` 동작 합니다.

또한 컨트롤러를 잠글 수 있지만 개별 작업에 대 한 익명의 인증 되지 않은 액세스를 허용 합니다.

```csharp
[Authorize]
public class ControlPanelController : Controller
{
    public ActionResult SetTime()
    {
    }

    [AllowAnonymous]
    public ActionResult Login()
    {
    }
}
```

<a name="security-authorization-role-policy"></a>

## <a name="policy-based-role-checks"></a>정책 기반 역할 검사

역할 요구 사항은 개발자는 권한 부여 서비스 구성의 일부로 시작 시에 정책을 등록 새 정책 구문을 사용 하 여 표현할 수 있습니다. 일반적으로이에서 발생 `ConfigureServices()` 에 프로그램 *Startup.cs* 파일입니다.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("RequireAdministratorRole", policy => policy.RequireRole("Administrator"));
    });
}
```

사용 하 여 정책이 적용 되는 `Policy` 속성에는 `AuthorizeAttribute` ; 특성

```csharp
[Authorize(Policy = "RequireAdministratorRole")]
public IActionResult Shutdown()
{
    return View();
}
```

요구 사항에 허용 되는 여러 역할을 지정 하려는 경우에 매개 변수로 지정할 수 있습니다는 `RequireRole` 메서드도 있습니다.

```csharp
options.AddPolicy("ElevatedRights", policy =>
                  policy.RequireRole("Administrator", "PowerUser", "BackupAdministrator"));
```

이 예제에 속한 사용자에 게 권한을 부여는 `Administrator`, `PowerUser` 또는 `BackupAdministrator` 역할입니다.
