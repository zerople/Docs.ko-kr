---
title: "간단한 권한 부여 | Microsoft 문서"
author: rick-anderson
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 391bcaad-205f-43e4-badc-fa592d6f79f3
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/authorization/simple
translationtype: Machine Translation
ms.sourcegitcommit: 010b730d2716f9f536fef889bc2f767afb648ef4
ms.openlocfilehash: 385a61f0202cf6ee7994c7f051e732c1b8f60ce9
ms.lasthandoff: 03/23/2017

---
# <a name="simple-authorization"></a>간단한 권한 부여

<a name=security-authorization-simple></a>

MVC에서 권한 부여를 통해 제어 됩니다는 `AuthorizeAttribute` 특성 및 다양 한 매개 변수입니다. 가장 간단한 적용에 `AuthorizeAttribute` 특성을 컨트롤러에 컨트롤러나 작업 제한 액세스 또는 인증 된 사용자에 게 작업 합니다.

다음 코드에 대 한 액세스를 제한 하는 예를 들어는 `AccountController` 모든 인증 된 사용자입니다.

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

단순히 컨트롤러 아니라 작업에 권한 부여를 적용 하려는 경우 적용 된 `AuthorizeAttribute` 작업 자체에 특성

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

이제 인증 된 사용자만 로그 아웃 함수에 액세스할 수 있습니다.

사용할 수도 있습니다는 `AllowAnonymousAttribute` ; 개별 작업에 인증 되지 않은 사용자가 액세스할 수 있도록 특성 예

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

이렇게 하면 인증 된 사용자만는 `AccountController`를 제외 하 고는 `Login` 인증 또는 인증 되지 않은 / 익명 상태에 관계 없이 모든 사용자가 액세스할 수 있는 작업입니다.

>[!WARNING]
> `[AllowAnonymous]`모든 권한 문을 무시합니다. 결합을 적용 하는 경우 `[AllowAnonymous]` 및 `[Authorize]` 특성 설정한 다음 권한 부여 특성은 항상 무시 됩니다. 예를 들어 적용 하는 경우 `[AllowAnonymous]` 컨트롤러에서 모든 수준 `[Authorize]` 특성 내에서 모든 작업 또는 같은 컨트롤러에서 무시 됩니다.

