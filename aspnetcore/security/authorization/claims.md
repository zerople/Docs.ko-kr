---
title: "클레임 기반 권한 부여 | Microsoft 문서"
author: rick-anderson
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 737be5cd-3511-4f1c-b0ce-65403fb5eed3
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/authorization/claims
ms.translationtype: Machine Translation
ms.sourcegitcommit: 010b730d2716f9f536fef889bc2f767afb648ef4
ms.openlocfilehash: 807195b82f3f2873ee4115a6d1efe3cf91d86c01
ms.contentlocale: ko-kr
ms.lasthandoff: 03/23/2017

---
# <a name="claims-based-authorization"></a>클레임 기반 권한 부여

<a name=security-authorization-claims-based></a>

Id를 만들 때 신뢰할 수 있는 당사자가 발급 하는 하나 이상의 클레임 할당 될 수 있습니다. 클레임은 어떤의 제목을 나타내는 쌍은 이름 값, 하지 어떤 주체 작업을 수행할 수 있습니다. 예를 들어 로컬 구동 라이선스 기관에서 발급 한 운전 면허 해야 합니다. 운전 면허증에 생년월일 것입니다. 이 경우 클레임 이름 수는 `DateOfBirth`, 클레임 값은 수 생년월일, 예를 들어 `8th June 1970` 발급자 구동 라이선스 기관 것입니다. 간단히 말하면, 클레임 기반 권한 부여 클레임의 값을 확인 하 고 해당 값에 따라 리소스에 액세스할 수 있습니다. 들어 밤 클럽에 권한 부여 프로세스에 액세스 하는 것 두 일 수 있습니다.

문 보안 책임자 출생 클레임 및 액세스 권한을 부여 하기 전에 (구동 라이선스 기관) 발급자를 신뢰 하는지 여부에 날짜 값을 평가 됩니다.

Id는 여러 값을 갖는 여러 클레임을 포함할 수와 동일한 유형의 여러 클레임을 포함할 수 있습니다.

## <a name="adding-claims-checks"></a>검사 하는 클레임 추가

클레임 기반된 권한 부여 검사는 선언적-개발자 포함 하는 컨트롤러 또는 컨트롤러 내의 작업에 대해 프로그램 코드 내에서 현재 사용자 보유 해야 하는 클레임을 지정 하 고 요청한 리소스에 액세스 하려면 클레임 값 보관 하는 필요에 따라입니다. 요구 사항은 정책 기반 클레임, 개발자 빌드 하며 등록 클레임 요구를 표현 하는 정책이 있습니다.

가장 간단한 정책 표시 된 클레임의 존재에 대 한 클레임 형식과 값을 확인 하지 않습니다.

먼저를 만들고 정책을 등록 해야 합니다. 일반적으로 참여 하는 권한 부여 서비스 구성의 일부로 이러한 대체가 발생할 `ConfigureServices()` 에 프로그램 *Startup.cs* 파일입니다.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("EmployeeOnly", policy => policy.RequireClaim("EmployeeNumber"));
    });
}
```

이 경우는 `EmployeeOnly` 정책은 있는지 확인 한 `EmployeeNumber` 현재 id에 클레임입니다.

그런 다음 사용 하 여 정책을 적용 된 `Policy` 속성에는 `AuthorizeAttribute` 정책 이름, 특성

```csharp
[Authorize(Policy = "EmployeeOnly")]
public IActionResult VacationBalance()
{
    return View();
}
```

`AuthorizeAttribute` 만 identities 정책을 일치 하는 컨트롤러에서 모든 작업에 대 한 액세스 허용 수는이 인스턴스의 전체 컨트롤러에 특성을 적용할 수 있습니다.

```csharp
[Authorize(Policy = "EmployeeOnly")]
public class VacationController : Controller
{
    public ActionResult VacationBalance()
    {
    }
}
```

로 보호 되는 컨트롤러가 있는 경우는 `AuthorizeAttribute` 특성을 적용 하는 특정 작업에 대 한 익명 액세스를 허용 하지만 `AllowAnonymousAttribute` 특성입니다.

```csharp
[Authorize(Policy = "EmployeeOnly")]
public class VacationController : Controller
{
    public ActionResult VacationBalance()
    {
    }

    [AllowAnonymous]
    public ActionResult VacationPolicy()
    {
    }
}
```

대부분 클레임 값을 함께 제공 됩니다. 정책을 만들 때 허용 되는 값 목록을 지정할 수 있습니다. 다음 예제는 직원에 대 한 성공만 수정한 인 직원 수는 1, 2, 3, 4 또는 5입니다.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("Founders", policy =>
                          policy.RequireClaim("EmployeeNumber", "1", "2", "3", "4", "5"));
    });
}
```

## <a name="multiple-policy-evaluation"></a>여러 정책 평가

컨트롤러 또는 작업에 여러 정책을 적용 하는 경우 액세스 권한을 부여 하기 전에 모든 정책을 전달 해야 합니다. 예를 들어 있습니다.

```csharp
[Authorize(Policy = "EmployeeOnly")]
public class SalaryController : Controller
{
    public ActionResult Payslip()
    {
    }

    [Authorize(Policy = "HumanResources")]
    public ActionResult UpdateSalary()
    {
    }
}
```

위의 예제에서 충족 되는 id는 `EmployeeOnly` 정책에 액세스할 수는 `Payslip` 컨트롤러에서 해당 정책으로 동작 적용 됩니다. 그러나 호출 하기 위해는 `UpdateSalary` id 충족 해야 하는 작업 *모두* 는 `EmployeeOnly` 정책 및 `HumanResources` 정책입니다.

더 복잡 한 정책을 원하는 출생 클레임의 날짜를 가져와 같은 보존 기간을 확인 한 다음 계산에서 age 21 또는 오래 된 경우를 작성 해야 [사용자 지정 정책 처리기](policies.md#security-authorization-policies-based)합니다.

