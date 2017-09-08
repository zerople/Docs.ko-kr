---
title: "역할 기반 권한 부여"
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
ms.openlocfilehash: d8dfcbb16ee7977d197b019c4e5e1b30fff17755
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/11/2017
---
# <a name="role-based-authorization"></a><span data-ttu-id="999cb-103">역할 기반 권한 부여</span><span class="sxs-lookup"><span data-stu-id="999cb-103">Role based Authorization</span></span>

<a name=security-authorization-role-based></a>

<span data-ttu-id="999cb-104">Id를 만들 때 하나 이상의 역할에 속할 수, Scott 사용자 역할에만 속할 수 하는 동안 Tracy 관리자 및 사용자 역할에 속할 수는 예입니다.</span><span class="sxs-lookup"><span data-stu-id="999cb-104">When an identity is created it may belong to one or more roles, for example Tracy may belong to the Administrator and User roles whilst Scott may only belong to the user role.</span></span> <span data-ttu-id="999cb-105">이러한 역할이 만들고 관리 하는 방법의 권한 부여 프로세스는 백업 저장소에 따라 달라 집니다.</span><span class="sxs-lookup"><span data-stu-id="999cb-105">How these roles are created and managed depends on the backing store of the authorization process.</span></span> <span data-ttu-id="999cb-106">역할을 통해 개발자에 게 노출 되는 [IsInRole](https://msdn.microsoft.com/library/system.security.claims.claimsprincipal.isinrole(v=vs.110).aspx) 속성에는 [ClaimsPrincipal](https://msdn.microsoft.com/library/system.security.claims.claimsprincipal(v=vs.110).aspx) 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="999cb-106">Roles are exposed to the developer through the [IsInRole](https://msdn.microsoft.com/library/system.security.claims.claimsprincipal.isinrole(v=vs.110).aspx) property on the [ClaimsPrincipal](https://msdn.microsoft.com/library/system.security.claims.claimsprincipal(v=vs.110).aspx) class.</span></span>

## <a name="adding-role-checks"></a><span data-ttu-id="999cb-107">추가 역할 검사</span><span class="sxs-lookup"><span data-stu-id="999cb-107">Adding role checks</span></span>

<span data-ttu-id="999cb-108">역할 기반 권한 부여 확인은 선언적-개발자 포함을 컨트롤러나는 컨트롤러 내 작업에 대해 해당 코드 내에서 현재 사용자는 요청 된 리소스에 액세스의 구성원 이어야 하는 역할을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="999cb-108">Role based authorization checks are declarative - the developer embeds them within their code, against a controller or an action within a controller, specifying roles which the current user must be a member of to access the requested resource.</span></span>

<span data-ttu-id="999cb-109">예를 들어 다음 코드는 제한 작업에 대 한 액세스에는 `AdministrationController` 의 구성원 인 사용자에 게는 `Administrator` 그룹입니다.</span><span class="sxs-lookup"><span data-stu-id="999cb-109">For example the following code would limit access to any actions on the `AdministrationController` to users who are a member of the `Administrator` group.</span></span>

```csharp
[Authorize(Roles = "Administrator")]
public class AdministrationController : Controller
{
}
```

<span data-ttu-id="999cb-110">쉼표로 구분 된 목록으로 여러 역할을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="999cb-110">You can specify multiple roles as a comma separated list;</span></span>

```csharp
[Authorize(Roles = "HRManager,Finance")]
public class SalaryController : Controller
{
}
```

<span data-ttu-id="999cb-111">이 컨트롤러는 구성원 인 사용자가 액세스할 수만의 `HRManager` 역할 또는 `Finance` 역할입니다.</span><span class="sxs-lookup"><span data-stu-id="999cb-111">This controller would be only accessible by users who are members of the `HRManager` role or the `Finance` role.</span></span>

<span data-ttu-id="999cb-112">에 액세스 하는 사용자 지정 된 모든 역할의 멤버 여야 합니다. 그런 다음 여러 특성을 적용 하는 경우 다음 예제에서는 사용자 둘 다의 구성원 이어야 하 고 `PowerUser` 및 `ControlPanelUser` 역할입니다.</span><span class="sxs-lookup"><span data-stu-id="999cb-112">If you apply multiple attributes then an accessing user must be a member of all the roles specified; the following sample requires that a user must be a member of both the `PowerUser` and `ControlPanelUser` role.</span></span>

```csharp
[Authorize(Roles = "PowerUser")]
[Authorize(Roles = "ControlPanelUser")]
public class ControlPanelController : Controller
{
}
```

<span data-ttu-id="999cb-113">작업 수준;에서 추가 역할 권한 부여 특성을 적용 하 여 액세스를 추가로 제한할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="999cb-113">You can further limit access by applying additional role authorization attributes at the action level;</span></span>

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

<span data-ttu-id="999cb-114">이전 코드 조각 멤버에는 `Administrator` 역할 또는 `PowerUser` 역할 컨트롤러에 액세스할 수 있습니다 및 `SetTime` 동작 하지만의 구성원만는 `Administrator` 역할이 액세스할 수는 `ShutDown` 동작 합니다.</span><span class="sxs-lookup"><span data-stu-id="999cb-114">In the previous code snippet members of the `Administrator` role or the `PowerUser` role can access the controller and the `SetTime` action, but only members of the `Administrator` role can access the `ShutDown` action.</span></span>

<span data-ttu-id="999cb-115">또한 컨트롤러를 잠글 수 있지만 개별 작업에 대 한 익명의 인증 되지 않은 액세스를 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="999cb-115">You can also lock down a controller but allow anonymous, unauthenticated access to individual actions.</span></span>

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

## <a name="policy-based-role-checks"></a><span data-ttu-id="999cb-116">정책 기반 역할 검사</span><span class="sxs-lookup"><span data-stu-id="999cb-116">Policy based role checks</span></span>

<span data-ttu-id="999cb-117">역할 요구 사항은 개발자는 권한 부여 서비스 구성의 일부로 시작 시에 정책을 등록 새 정책 구문을 사용 하 여 표현할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="999cb-117">Role requirements can also be expressed using the new Policy syntax, where a developer registers a policy at startup as part of the Authorization service configuration.</span></span> <span data-ttu-id="999cb-118">일반적으로이에서 발생 `ConfigureServices()` 에 프로그램 *Startup.cs* 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="999cb-118">This normally occurs in `ConfigureServices()` in your *Startup.cs* file.</span></span>

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

<span data-ttu-id="999cb-119">사용 하 여 정책이 적용 되는 `Policy` 속성에는 `AuthorizeAttribute` ; 특성</span><span class="sxs-lookup"><span data-stu-id="999cb-119">Policies are applied using the `Policy` property on the `AuthorizeAttribute` attribute;</span></span>

```csharp
[Authorize(Policy = "RequireAdministratorRole")]
public IActionResult Shutdown()
{
    return View();
}
```

<span data-ttu-id="999cb-120">요구 사항에 허용 되는 여러 역할을 지정 하려는 경우에 매개 변수로 지정할 수 있습니다는 `RequireRole` 메서드도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="999cb-120">If you want to specify multiple allowed roles in a requirement then you can specify them as parameters to the `RequireRole` method;</span></span>

```csharp
options.AddPolicy("ElevatedRights", policy =>
                  policy.RequireRole("Administrator", "PowerUser", "BackupAdministrator"));
```

<span data-ttu-id="999cb-121">이 예제에 속한 사용자에 게 권한을 부여는 `Administrator`, `PowerUser` 또는 `BackupAdministrator` 역할입니다.</span><span class="sxs-lookup"><span data-stu-id="999cb-121">This example authorizes users who belong to the `Administrator`, `PowerUser` or `BackupAdministrator` roles.</span></span>
