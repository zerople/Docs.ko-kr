---
title: "클레임 기반 권한 부여"
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
ms.openlocfilehash: fca75952429d48b02c2c4350b79e29a1957599dc
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/11/2017
---
# <a name="claims-based-authorization"></a><span data-ttu-id="b7f79-103">클레임 기반 권한 부여</span><span class="sxs-lookup"><span data-stu-id="b7f79-103">Claims-Based Authorization</span></span>

<a name=security-authorization-claims-based></a>

<span data-ttu-id="b7f79-104">Id를 만들 때 신뢰할 수 있는 당사자가 발급 하는 하나 이상의 클레임 할당할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b7f79-104">When an identity is created it may be assigned one or more claims issued by a trusted party.</span></span> <span data-ttu-id="b7f79-105">클레임은 어떤 주제를 나타내는 쌍은 이름 값, 하지 어떤 주체 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b7f79-105">A claim is name value pair that represents what the subject is, not what the subject can do.</span></span> <span data-ttu-id="b7f79-106">예를 들어 로컬 구동 라이선스 기관에서 발급 한 운전 면허 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b7f79-106">For example you may have a Drivers License, issued by a local driving license authority.</span></span> <span data-ttu-id="b7f79-107">드라이버의 라이선스 생년월일 갖고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b7f79-107">Your driver's license has your date of birth on it.</span></span> <span data-ttu-id="b7f79-108">이 경우 클레임 이름이 표시 됩니다 `DateOfBirth`, 클레임 값과 수 생년월일, 예를 들어 `8th June 1970` 발급자 구동 라이선스 기관 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b7f79-108">In this case the claim name would be `DateOfBirth`, the claim value would be your date of birth, for example `8th June 1970` and the issuer would be the driving license authority.</span></span> <span data-ttu-id="b7f79-109">가장 간단한 클레임 기반 권한 부여 클레임의 값을 확인 하 고 해당 값에 따라 리소스에 대 한 액세스를 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="b7f79-109">Claims based authorization, at its simplest, checks the value of a claim and allows access to a resource based upon that value.</span></span> <span data-ttu-id="b7f79-110">에 권한 부여 프로세스 밤 클럽에 액세스 하려는 경우를 예로 들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b7f79-110">For example if you want access to a night club the authorization process might be:</span></span>

<span data-ttu-id="b7f79-111">문 보안 책임자 출생 클레임 및 액세스 권한을 부여 하기 전에 (구동 라이선스 기관) 발급자 서로 신뢰 하는지 여부의 날짜 값을 평가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b7f79-111">The door security officer would evaluate the value of your date of birth claim and whether they trust the issuer (the driving license authority) before granting you access.</span></span>

<span data-ttu-id="b7f79-112">id 값이 여러 개인 여러 클레임을 포함할 수 있습니다 및 동일한 유형의 여러 클레임을 포함할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b7f79-112">An identity can contain multiple claims with multiple values and can contain multiple claims of the same type.</span></span>

## <a name="adding-claims-checks"></a><span data-ttu-id="b7f79-113">검사 하는 클레임 추가</span><span class="sxs-lookup"><span data-stu-id="b7f79-113">Adding claims checks</span></span>

<span data-ttu-id="b7f79-114">클레임 기반된 권한 부여 확인은 선언적-개발자 포함 하는 컨트롤러 또는 컨트롤러 내 작업에 대해 해당 코드 내에서 현재 사용자가 소유 해야, 필요에 따라 클레임 값 해야 하 고 있으면 액세스 하는 클레임을 지정 하는 요청 된 리소스입니다.</span><span class="sxs-lookup"><span data-stu-id="b7f79-114">Claim based authorization checks are declarative - the developer embeds them within their code, against a controller or an action within a controller, specifying claims which the current user must possess, and optionally the value the claim must hold to access the requested resource.</span></span> <span data-ttu-id="b7f79-115">요구 사항은 정책 기반 클레임 개발자 값이 빌드 및 클레임 요구를 표현 하는 정책을 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="b7f79-115">Claims requirements are policy based, the developer must build and register a policy expressing the claims requirements.</span></span>

<span data-ttu-id="b7f79-116">가장 단순한 유형의 클레임을 클레임의 존재에 대 한 정책 검색 하 고 값을 확인 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b7f79-116">The simplest type of claim policy looks for the presence of a claim and does not check the value.</span></span>

<span data-ttu-id="b7f79-117">먼저를 만들고 정책을 등록 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b7f79-117">First you need to build and register the policy.</span></span> <span data-ttu-id="b7f79-118">에 일반적으로 참여 하는 권한 부여 서비스 구성의 일부분으로 수행 됩니다이 `ConfigureServices()` 에 프로그램 *Startup.cs* 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="b7f79-118">This takes place as part of the Authorization service configuration, which normally takes part in `ConfigureServices()` in your *Startup.cs* file.</span></span>

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

<span data-ttu-id="b7f79-119">이 경우에 `EmployeeOnly` 정책은 있는지 확인 한 `EmployeeNumber` 현재 id에서 클레임 합니다.</span><span class="sxs-lookup"><span data-stu-id="b7f79-119">In this case the `EmployeeOnly` policy checks for the presence of an `EmployeeNumber` claim on the current identity.</span></span>

<span data-ttu-id="b7f79-120">그런 다음 사용 하 여 정책 적용의 `Policy` 속성에는 `AuthorizeAttribute` ; 정책 이름을 지정 하는 특성</span><span class="sxs-lookup"><span data-stu-id="b7f79-120">You then apply the policy using the `Policy` property on the `AuthorizeAttribute` attribute to specify the policy name;</span></span>

```csharp
[Authorize(Policy = "EmployeeOnly")]
public IActionResult VacationBalance()
{
    return View();
}
```

<span data-ttu-id="b7f79-121">`AuthorizeAttribute` 만 identities는 정책과 일치 하는 컨트롤러에서 모든 작업에 대 한 액세스 허용 수는이 인스턴스에서 전체 컨트롤러에 특성을 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b7f79-121">The `AuthorizeAttribute` attribute can be applied to an entire controller, in this instance only identities matching the policy will be allowed access to any Action on the controller.</span></span>

```csharp
[Authorize(Policy = "EmployeeOnly")]
public class VacationController : Controller
{
    public ActionResult VacationBalance()
    {
    }
}
```

<span data-ttu-id="b7f79-122">로 보호 되는 컨트롤러가 있는 경우는 `AuthorizeAttribute` 특성을 적용 하면 특정 작업에 대 한 익명 액세스를 허용 하지만 `AllowAnonymousAttribute` 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="b7f79-122">If you have a controller that is protected by the `AuthorizeAttribute` attribute, but want to allow anonymous access to particular actions you apply the `AllowAnonymousAttribute` attribute.</span></span>

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

<span data-ttu-id="b7f79-123">대부분 클레임 값을 함께 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b7f79-123">Most claims come with a value.</span></span> <span data-ttu-id="b7f79-124">정책을 만들 때 허용 되는 값 목록을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b7f79-124">You can specify a list of allowed values when creating the policy.</span></span> <span data-ttu-id="b7f79-125">다음 예제에서는는 성공 직원에 대 한를 가진 직원 수는 1, 2, 3, 4 또는 5입니다.</span><span class="sxs-lookup"><span data-stu-id="b7f79-125">The following example would only succeed for employees whose employee number was 1, 2, 3, 4 or 5.</span></span>

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

## <a name="multiple-policy-evaluation"></a><span data-ttu-id="b7f79-126">여러 정책 평가</span><span class="sxs-lookup"><span data-stu-id="b7f79-126">Multiple Policy Evaluation</span></span>

<span data-ttu-id="b7f79-127">컨트롤러 또는 동작에 여러 정책을 적용 하는 경우 액세스 권한을 부여 하기 전에 모든 정책 통과 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b7f79-127">If you apply multiple policies to a controller or action then all policies must pass before access is granted.</span></span> <span data-ttu-id="b7f79-128">예:</span><span class="sxs-lookup"><span data-stu-id="b7f79-128">For example:</span></span>

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

<span data-ttu-id="b7f79-129">위의 예에 충족 하는 모든 id는 `EmployeeOnly` 정책에 액세스할 수는 `Payslip` 해당 정책으로는 작업의 컨트롤러에 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b7f79-129">In the above example any identity which fulfills the `EmployeeOnly` policy can access the `Payslip` action as that policy is enforced on the controller.</span></span> <span data-ttu-id="b7f79-130">그러나 호출 하기 위해는 `UpdateSalary` id 충족 해야 하는 작업 *둘 다* 는 `EmployeeOnly` 정책 및 `HumanResources` 정책입니다.</span><span class="sxs-lookup"><span data-stu-id="b7f79-130">However in order to call the `UpdateSalary` action the identity must fulfill *both* the `EmployeeOnly` policy and the `HumanResources` policy.</span></span>

<span data-ttu-id="b7f79-131">더 복잡 한 정책을 원하는 출생 클레임의 날짜를 가져와 같은 보존 기간을 확인 한 다음에서 나 계산 21 또는 오래 된 경우를 작성 해야 [사용자 지정 정책 처리기](policies.md#security-authorization-policies-based)합니다.</span><span class="sxs-lookup"><span data-stu-id="b7f79-131">If you want more complicated policies, such as taking a date of birth claim, calculating an age from it then checking the age is 21 or older then you need to write [custom policy handlers](policies.md#security-authorization-policies-based).</span></span>
