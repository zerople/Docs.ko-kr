---
title: "역할 기반 권한 부여 | Microsoft 문서"
author: rick-anderson
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 5e014da1-8bc0-409b-951a-88b92c661fdf
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/authorization/roles
translationtype: Machine Translation
ms.sourcegitcommit: 010b730d2716f9f536fef889bc2f767afb648ef4
ms.openlocfilehash: aba29d436a6e7a64fdbcc974e815f4584f773ec5
ms.lasthandoff: 03/23/2017

---
# <a name="role-based-authorization"></a>역할 기반 권한 부여

<a name=security-authorization-role-based></a>

Id를 만들 때 하나 이상의 역할에 속할 수 있으며, Scott 사용자 역할에만 속할 수 있으며 동시 Tracy 관리자 및 사용자 역할에 속할 수는 예를 들어 있습니다. 이러한 역할을 만들고 관리 하는 방법에 권한 부여 프로세스의 백업 저장소에 따라 다릅니다. 역할을 통해 개발자에 게 노출 되는 [IsInRole](https://msdn.microsoft.com/en-us/library/system.security.claims.claimsprincipal.isinrole(v=vs.110).aspx) 속성에는 [ClaimsPrincipal](https://msdn.microsoft.com/en-us/library/system.security.claims.claimsprincipal(v=vs.110).aspx) 클래스입니다.

## <a name="adding-role-checks"></a>추가 역할 검사

역할 기반 권한 부여 검사는 선언적-개발자 포함을 컨트롤러나 컨트롤러 내의 작업에 대해 프로그램 코드 내에서 요청된 된 리소스에 액세스 하는 현재 사용자의 멤버 여야 하는 역할을 지정 합니다.

예를 들어 다음 코드는 제한 작업에 대 한 액세스는 `AdministrationController` 의 구성원 인 사용자에 게는 `Administrator` 그룹입니다.

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

이 컨트롤러는 구성원 인 사용자가 액세스할 수 있는 것만의 `HRManager` 역할 또는 `Finance` 역할입니다.

에 액세스 하는 사용자 지정 하며 모든 역할의 멤버 여야 합니다. 그런 다음 여러 특성을 적용 하는 경우 다음 예제에서는 사용자 모두의 구성원 이어야 하 고 `PowerUser` 및 `ControlPanelUser` 역할입니다.

```csharp
[Authorize(Roles = "PowerUser")]
[Authorize(Roles = "ControlPanelUser")]
public class ControlPanelController : Controller
{
}
```

작업 수준;에서 추가 역할 권한 부여 특성을 적용 하 여 액세스를 더욱 제한할 수 있습니다.

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

이전 코드 조각 멤버에서는 `Administrator` 역할 또는 `PowerUser` 역할 컨트롤러에 액세스할 수 및 `SetTime` 동작 하지만의 구성원만는 `Administrator` 역할이 액세스할 수는 `ShutDown` 동작 합니다.

또한 개별 작업에 대 한 익명, 인증 되지 않은 액세스를 허용 하지만 컨트롤러를 잠글 수 있습니다.

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

<a name=security-authorization-role-policy></a>

## <a name="policy-based-role-checks"></a>정책 기반 역할 검사

역할 요구 사항은 개발자는 권한 부여 서비스 구성의 일부로 시작 시 정책을 등록 새 정책 구문을 사용 하 여 표현할 수 있습니다. 이에 대해 일반적으로 발생 `ConfigureServices()` 에 프로그램 *Startup.cs* 파일입니다.

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

정책을 사용 하 여 적용 되는 `Policy` 속성에는 `AuthorizeAttribute` ; 특성

```csharp
[Authorize(Policy = "RequireAdministratorRole")]
public IActionResult Shutdown()
{
    return View();
}
```

요구 사항에서 허용 되는 여러 역할을 지정 하려는 경우에 매개 변수로 지정할 수 있습니다는 `RequireRole` 메서드도 있습니다.

```csharp
options.AddPolicy("ElevatedRights", policy =>
                  policy.RequireRole("Administrator", "PowerUser", "BackupAdministrator"));
```

이 예제에서는 인증에 속한 사용자는 `Administrator`, `PowerUser` 또는 `BackupAdministrator` 역할입니다.

