---
title: "단순 권한 부여"
author: rick-anderson
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 391bcaad-205f-43e4-badc-fa592d6f79f3
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/authorization/simple
ms.openlocfilehash: 91f402b1cbf73e212418d197a8a7230ce22e9e1d
ms.sourcegitcommit: 8f4d4fad1ca27adf9e396f5c205c9875a3963664
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/13/2017
---
# <a name="simple-authorization"></a><span data-ttu-id="a9c6d-103">단순 권한 부여</span><span class="sxs-lookup"><span data-stu-id="a9c6d-103">Simple Authorization</span></span>

<a name="security-authorization-simple"></a>

<span data-ttu-id="a9c6d-104">MVC에서 권한 부여를 통해 제어 됩니다는 `AuthorizeAttribute` 특성 및 해당 다양 한 매개 변수입니다.</span><span class="sxs-lookup"><span data-stu-id="a9c6d-104">Authorization in MVC is controlled through the `AuthorizeAttribute` attribute and its various parameters.</span></span> <span data-ttu-id="a9c6d-105">가장 간단한 적용에 `AuthorizeAttribute` 특성는 컨트롤러나 작업 제한에 대 한 액세스는 컨트롤러 또는 모든 인증 된 사용자 동작을 합니다.</span><span class="sxs-lookup"><span data-stu-id="a9c6d-105">At its simplest applying the `AuthorizeAttribute` attribute to a controller or action limits access to the controller or action to any authenticated user.</span></span>

<span data-ttu-id="a9c6d-106">다음 코드에 대 한 액세스를 제한 하는 예를 들어는 `AccountController` 모든 인증 된 사용자입니다.</span><span class="sxs-lookup"><span data-stu-id="a9c6d-106">For example, the following code limits access to the `AccountController` to any authenticated user.</span></span>

```csharp
[Authorize]
   public class AccountController : Controller
   {
       public ActionResult Login()
       {
       }

       public ActionResult Logout()
       {
       }
   }
   ```

<span data-ttu-id="a9c6d-107">단순히 컨트롤러 아니라 작업에 권한 부여를 적용 하려면 적용는 `AuthorizeAttribute` 특성을 작업 자체입니다.</span><span class="sxs-lookup"><span data-stu-id="a9c6d-107">If you want to apply authorization to an action rather than the controller simply apply the `AuthorizeAttribute` attribute to the action itself;</span></span>

```csharp
public class AccountController : Controller
   {
       public ActionResult Login()
       {
       }

       [Authorize]
       public ActionResult Logout()
       {
       }
   }
   ```

<span data-ttu-id="a9c6d-108">이제 인증 된 사용자만 로그 아웃 함수에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a9c6d-108">Now only authenticated users can access the logout function.</span></span>

<span data-ttu-id="a9c6d-109">사용할 수도 있습니다는 `AllowAnonymousAttribute` ; 개별 작업에 대 한 인증 되지 않은 사용자가 액세스할 수 있도록 특성 예</span><span class="sxs-lookup"><span data-stu-id="a9c6d-109">You can also use the `AllowAnonymousAttribute` attribute to allow access by non-authenticated users to individual actions; for example</span></span>

```csharp
[Authorize]
   public class AccountController : Controller
   {
       [AllowAnonymous]
       public ActionResult Login()
       {
       }

       public ActionResult Logout()
       {
       }
   }
   ```

<span data-ttu-id="a9c6d-110">이렇게 하면 인증 된 사용자만는 `AccountController`를 제외 하 고는 `Login` / 익명 인증 된 인증 되지 않은 상태에 관계 없이 모든 사용자가 액세스할 수 있는 작업입니다.</span><span class="sxs-lookup"><span data-stu-id="a9c6d-110">This would allow only authenticated users to the `AccountController`, except for the `Login` action, which is accessible by everyone, regardless of their authenticated or unauthenticated / anonymous status.</span></span>

>[!WARNING]
> <span data-ttu-id="a9c6d-111">`[AllowAnonymous]`모든 권한 부여 문을 무시합니다.</span><span class="sxs-lookup"><span data-stu-id="a9c6d-111">`[AllowAnonymous]` bypasses all authorization statements.</span></span> <span data-ttu-id="a9c6d-112">결합 되어 감사가 만들어집니다를 적용 하는 경우 `[AllowAnonymous]` 임의의 `[Authorize]` 특성 설정한 다음 권한 부여 특성은 항상 무시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a9c6d-112">If you apply combine `[AllowAnonymous]` and any `[Authorize]` attribute then the Authorize attributes will always be ignored.</span></span> <span data-ttu-id="a9c6d-113">예를 적용 하는 경우 `[AllowAnonymous]` 모든 컨트롤러 수준 `[Authorize]` 내 모든 작업 또는 동일한 컨트롤러에서 특성이 무시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a9c6d-113">For example if you apply `[AllowAnonymous]` at the controller level any `[Authorize]` attributes on the same controller, or on any action within it will be ignored.</span></span>
