---
title: "사용자 지정 정책 기반 권한 부여"
author: rick-anderson
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: e422a1b2-dc4a-4bcc-b8d9-7ee62009b6a3
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/authorization/policies
ms.openlocfilehash: 5021b5d20f6d9b9a4d8889f25b5e41f2c9306f64
ms.sourcegitcommit: 6e83c55eb0450a3073ef2b95fa5f5bcb20dbbf89
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/28/2017
---
# <a name="custom-policy-based-authorization"></a><span data-ttu-id="c172e-103">사용자 지정 정책 기반 권한 부여</span><span class="sxs-lookup"><span data-stu-id="c172e-103">Custom Policy-Based Authorization</span></span>

<a name=security-authorization-policies-based></a>

<span data-ttu-id="c172e-104">내부적는 [역할 권한 부여](roles.md#security-authorization-role-based) 및 [권한 부여 클레임](claims.md#security-authorization-claims-based) 요구 사항의 사용, 요구 사항 및 미리 구성 된 정책에 대 한 처리기를 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="c172e-104">Underneath the covers the [role authorization](roles.md#security-authorization-role-based) and [claims authorization](claims.md#security-authorization-claims-based) make use of a requirement, a handler for the requirement and a pre-configured policy.</span></span> <span data-ttu-id="c172e-105">이러한 빌딩 블록을 사용 하면 코드를 다시 사용할 수는 다양 한 작업에 대 한 허용 및 쉽게 테스트할 권한 부여 구조에서 권한 부여 평가 표현할 수 있도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="c172e-105">These building blocks allow you to express authorization evaluations in code, allowing for a richer, reusable, and easily testable authorization structure.</span></span>

<span data-ttu-id="c172e-106">권한 부여 정책 하나 이상의 요구 사항으로 구성 되며 응용 프로그램 시작 시에 권한 부여 서비스 구성의 일부로 등록 `ConfigureServices` 에 *Startup.cs* 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="c172e-106">An authorization policy is made up of one or more requirements and registered at application startup as part of the Authorization service configuration, in `ConfigureServices` in the *Startup.cs* file.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("Over21",
                          policy => policy.Requirements.Add(new MinimumAgeRequirement(21)));
    });
}
```

<span data-ttu-id="c172e-107">여기서는의 최소 연령 변수로 전달 되는 매개 변수 요구 사항에 단일 요구 사항에는 "Over21" 정책이 만들어지면이 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c172e-107">Here you can see an "Over21" policy is created with a single requirement, that of a minimum age, which is passed as a parameter to the requirement.</span></span>

<span data-ttu-id="c172e-108">사용 하 여 정책이 적용 되는 `Authorize` 예를 들어 정책 이름을 지정 하 여 특성</span><span class="sxs-lookup"><span data-stu-id="c172e-108">Policies are applied using the `Authorize` attribute by specifying the policy name, for example;</span></span>

```csharp
[Authorize(Policy="Over21")]
public class AlcoholPurchaseRequirementsController : Controller
{
    public ActionResult Login()
    {
    }

    public ActionResult Logout()
    {
    }
}
```

## <a name="requirements"></a><span data-ttu-id="c172e-109">요구 사항</span><span class="sxs-lookup"><span data-stu-id="c172e-109">Requirements</span></span>

<span data-ttu-id="c172e-110">권한 부여 요구 사항은 현재 사용자 보안 주체를 평가 하는 정책을 사용할 수 있는 데이터 매개 변수 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="c172e-110">An authorization requirement is a collection of data parameters that a policy can use to evaluate the current user principal.</span></span> <span data-ttu-id="c172e-111">보호 최소 보존 기간 정책 요구 사항이 있는 경우 단일 매개 변수를 최소 보존 기간</span><span class="sxs-lookup"><span data-stu-id="c172e-111">In our Minimum Age policy the requirement we have is a single parameter, the minimum age.</span></span> <span data-ttu-id="c172e-112">요구 사항을 구현 해야 `IAuthorizationRequirement`합니다.</span><span class="sxs-lookup"><span data-stu-id="c172e-112">A requirement must implement `IAuthorizationRequirement`.</span></span> <span data-ttu-id="c172e-113">빈, 표식 인터페이스입니다.</span><span class="sxs-lookup"><span data-stu-id="c172e-113">This is an empty, marker interface.</span></span> <span data-ttu-id="c172e-114">매개 변수가 있는 최소 연령 요구 사항을 다음과 같이; 구현 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c172e-114">A parameterized minimum age requirement might be implemented as follows;</span></span>

```csharp
public class MinimumAgeRequirement : IAuthorizationRequirement
{
    public int MinimumAge { get; private set; }
    
    public MinimumAgeRequirement(int minimumAge)
    {
        MinimumAge = minimumAge;
    }
}
```

<span data-ttu-id="c172e-115">데이터 또는 속성 요구 사항 필요 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c172e-115">A requirement doesn't need to have data or properties.</span></span>

<a name=security-authorization-policies-based-authorization-handler></a>

## <a name="authorization-handlers"></a><span data-ttu-id="c172e-116">권한 부여 처리기</span><span class="sxs-lookup"><span data-stu-id="c172e-116">Authorization Handlers</span></span>

<span data-ttu-id="c172e-117">권한 부여 처리기는 요구 사항 속성의 계산 합니다.</span><span class="sxs-lookup"><span data-stu-id="c172e-117">An authorization handler is responsible for the evaluation of any properties of a requirement.</span></span> <span data-ttu-id="c172e-118">권한 부여 처리기는 제공 된 기준으로 평가 해야 `AuthorizationHandlerContext` 권한 부여 허용 되는지 여부를 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="c172e-118">The  authorization handler must evaluate them against a provided `AuthorizationHandlerContext` to decide if authorization is allowed.</span></span> <span data-ttu-id="c172e-119">요구 사항이 있을 수 있습니다 [여러 처리기](policies.md#security-authorization-policies-based-multiple-handlers)합니다.</span><span class="sxs-lookup"><span data-stu-id="c172e-119">A requirement can have [multiple handlers](policies.md#security-authorization-policies-based-multiple-handlers).</span></span> <span data-ttu-id="c172e-120">처리기에서 상속 해야 합니다. `AuthorizationHandler<T>` 여기서 T는 요구 사항을 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="c172e-120">Handlers must inherit `AuthorizationHandler<T>` where T is the requirement it handles.</span></span>

<a name=security-authorization-handler-example></a>

<span data-ttu-id="c172e-121">최소 보존 기간 처리기는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="c172e-121">The minimum age handler might look like this:</span></span>

```csharp
public class MinimumAgeHandler : AuthorizationHandler<MinimumAgeRequirement>
{
    protected override Task HandleRequirementAsync(AuthorizationHandlerContext context, MinimumAgeRequirement requirement)
    {
        if (!context.User.HasClaim(c => c.Type == ClaimTypes.DateOfBirth &&
                                   c.Issuer == "http://contoso.com"))
        {
            // .NET 4.x -> return Task.FromResult(0);
            return Task.CompletedTask;
        }

        var dateOfBirth = Convert.ToDateTime(context.User.FindFirst(
            c => c.Type == ClaimTypes.DateOfBirth && c.Issuer == "http://contoso.com").Value);

        int calculatedAge = DateTime.Today.Year - dateOfBirth.Year;
        if (dateOfBirth > DateTime.Today.AddYears(-calculatedAge))
        {
            calculatedAge--;
        }

        if (calculatedAge >= requirement.MinimumAge)
        {
            context.Succeed(requirement);
        }
        return Task.CompletedTask;
    }
}
```

<span data-ttu-id="c172e-122">위의 코드에 먼저 의견에 귀 현재 사용자 계정이 회원님의 발급자와 신뢰에서 실행 하지는 클레임 생년월일 날짜에 있는지를 합니다.</span><span class="sxs-lookup"><span data-stu-id="c172e-122">In the code above we first look to see if the current user principal has a date of birth claim which has been issued by an Issuer we know and trust.</span></span> <span data-ttu-id="c172e-123">클레임이 없는 경우 반환 하므로 권한을 부여할 수 없습니다 것입니다.</span><span class="sxs-lookup"><span data-stu-id="c172e-123">If the claim is missing we can't authorize so we return.</span></span> <span data-ttu-id="c172e-124">오래 된 사용자가 파악 클레임 있는데 하 고 요구 사항에 의해 전달 된 최소 보존 기간을 충족 하는 경우 다음 권한 부여에 성공한 키를 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="c172e-124">If we have a claim, we figure out how old the user is, and if they meet the minimum age passed in by the requirement then authorization has been successful.</span></span> <span data-ttu-id="c172e-125">일단 부여 된 이라고 `context.Succeed()` 매개 변수로 성공적으로 실행 되었는지 요구 사항을 전달 합니다.</span><span class="sxs-lookup"><span data-stu-id="c172e-125">Once authorization is successful we call `context.Succeed()` passing in the requirement that has been successful as a parameter.</span></span>

<a name=security-authorization-policies-based-handler-registration></a>

<span data-ttu-id="c172e-126">예를 들어 처리기 구성 하는 동안 서비스 컬렉션에 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="c172e-126">Handlers must be registered in the services collection during configuration, for example;</span></span>

```csharp

public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("Over21",
                          policy => policy.Requirements.Add(new MinimumAgeRequirement(21)));
    });

    services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();
}
```

<span data-ttu-id="c172e-127">서비스 컬렉션을 사용 하 여 각 처리기가 추가 `services.AddSingleton<IAuthorizationHandler, YourHandlerClass>();` 처리기 클래스를 전달 합니다.</span><span class="sxs-lookup"><span data-stu-id="c172e-127">Each handler is added to the services collection by using `services.AddSingleton<IAuthorizationHandler, YourHandlerClass>();` passing in your handler class.</span></span>

## <a name="what-should-a-handler-return"></a><span data-ttu-id="c172e-128">처리기를 반환 해야?</span><span class="sxs-lookup"><span data-stu-id="c172e-128">What should a handler return?</span></span>

<span data-ttu-id="c172e-129">볼 수 있듯이 우리의 [처리기 예제](policies.md#security-authorization-handler-example) 하는 `Handle()` 메서드 반환 값이 없는 어떻게 수행 म 성공 또는 실패를 나타내는?</span><span class="sxs-lookup"><span data-stu-id="c172e-129">You can see in our [handler example](policies.md#security-authorization-handler-example) that the `Handle()` method has no return value, so how do we indicate success or failure?</span></span>

* <span data-ttu-id="c172e-130">처리기를 호출 하 여이 성공 했음을 의미 `context.Succeed(IAuthorizationRequirement requirement)`, 된 요구 사항을 전달 성공적으로 확인 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="c172e-130">A handler indicates success by calling `context.Succeed(IAuthorizationRequirement requirement)`, passing the requirement that has been successfully validated.</span></span>

* <span data-ttu-id="c172e-131">처리기를 배포할 수 있습니다. 동일한 요구 사항에 대해 다른 처리기 처럼 일반적으로 오류를 처리 하도록 필요는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c172e-131">A handler does not need to handle failures generally, as other handlers for the same requirement may succeed.</span></span>

* <span data-ttu-id="c172e-132">실패 한 요구 사항에 대해 다른 처리기 성공 하는 경우에, 호출 `context.Fail`합니다.</span><span class="sxs-lookup"><span data-stu-id="c172e-132">To guarantee failure even if other handlers for a requirement succeed, call `context.Fail`.</span></span>

<span data-ttu-id="c172e-133">호출 하는 처리기 내에 관계 없이 정책 요구 사항에서 요구 하는 경우 요구 사항에 대 한 모든 처리기가 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c172e-133">Regardless of what you call inside your handler all handlers for a requirement will be called when a policy requires the requirement.</span></span> <span data-ttu-id="c172e-134">이렇게 하면 요구 사항에는 항상 적용 하는 로깅 같은 의도 하지 않은 경우에 `context.Fail()` 다른 처리기에서 호출 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="c172e-134">This allows requirements to have side effects, such as logging, which will always take place even if `context.Fail()` has been called in another handler.</span></span>

<a name=security-authorization-policies-based-multiple-handlers></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a><span data-ttu-id="c172e-135">이유는 여러 처리기는 요구 사항에 대 한?</span><span class="sxs-lookup"><span data-stu-id="c172e-135">Why would I want multiple handlers for a requirement?</span></span>

<span data-ttu-id="c172e-136">에 있는 것으로 평가 하려는 경우에는 **또는** 별로 단일 요구 사항에 대해 여러 처리기를 구현 합니다.</span><span class="sxs-lookup"><span data-stu-id="c172e-136">In cases where you want evaluation to be on an **OR** basis you implement multiple handlers for a single requirement.</span></span> <span data-ttu-id="c172e-137">예를 들어 Microsoft는 주요 카드를 사용 하는 열만 있는 문.</span><span class="sxs-lookup"><span data-stu-id="c172e-137">For example, Microsoft has doors which only open with key cards.</span></span> <span data-ttu-id="c172e-138">키 카드를 집에 두면는 접수원이 임시 스티커 인쇄를 문을 열립니다.</span><span class="sxs-lookup"><span data-stu-id="c172e-138">If you leave your key card at home the receptionist prints a temporary sticker and opens the door for you.</span></span> <span data-ttu-id="c172e-139">이 시나리오에서는 단일 반드시 그래야 할 것 *EnterBuilding*, 하지만 단일 요구 사항 검사 하나씩 여러 처리기입니다.</span><span class="sxs-lookup"><span data-stu-id="c172e-139">In this scenario you'd have a single requirement, *EnterBuilding*, but multiple handlers, each one examining a single requirement.</span></span>

```csharp
public class EnterBuildingRequirement : IAuthorizationRequirement
{
}

public class BadgeEntryHandler : AuthorizationHandler<EnterBuildingRequirement>
{
    protected override Task HandleRequirementAsync(AuthorizationHandlerContext context, EnterBuildingRequirement requirement)
    {
        if (context.User.HasClaim(c => c.Type == ClaimTypes.BadgeId &&
                                       c.Issuer == "http://microsoftsecurity"))
        {
            context.Succeed(requirement);
        }
        return Task.CompletedTask;
    }
}

public class HasTemporaryStickerHandler : AuthorizationHandler<EnterBuildingRequirement>
{
    protected override Task HandleRequirementAsync(AuthorizationHandlerContext context, EnterBuildingRequirement requirement)
    {
        if (context.User.HasClaim(c => c.Type == ClaimTypes.TemporaryBadgeId &&
                                       c.Issuer == "https://microsoftsecurity"))
        {
            // We'd also check the expiration date on the sticker.
            context.Succeed(requirement);
        }
        return Task.CompletedTask;
    }
}
```

<span data-ttu-id="c172e-140">이제는 두 처리기 가정 [등록](xref:security/authorization/policies#security-authorization-policies-based-handler-registration) 정책을 평가 하면는 `EnterBuildingRequirement` 처리기 중 하나에 성공 하면 정책 평가 성공 합니다.</span><span class="sxs-lookup"><span data-stu-id="c172e-140">Now, assuming both handlers are [registered](xref:security/authorization/policies#security-authorization-policies-based-handler-registration) when a policy evaluates the `EnterBuildingRequirement` if either handler succeeds the policy evaluation will succeed.</span></span>

## <a name="using-a-func-to-fulfill-a-policy"></a><span data-ttu-id="c172e-141">Func는 정책을 처리 하는 데 사용 하 여</span><span class="sxs-lookup"><span data-stu-id="c172e-141">Using a func to fulfill a policy</span></span>

<span data-ttu-id="c172e-142">경우가 정책 처리는 코드에서 표현 하기 간단 합니다.</span><span class="sxs-lookup"><span data-stu-id="c172e-142">There may be occasions where fulfilling a policy is simple to express in code.</span></span> <span data-ttu-id="c172e-143">만 제공 하는 것이 불가능 한 `Func<AuthorizationHandlerContext, bool>` 와 정책을 구성 하는 경우는 `RequireAssertion` 정책 작성기입니다.</span><span class="sxs-lookup"><span data-stu-id="c172e-143">It is possible to simply supply a `Func<AuthorizationHandlerContext, bool>` when configuring your policy with the `RequireAssertion` policy builder.</span></span>

<span data-ttu-id="c172e-144">예를 들어 이전 `BadgeEntryHandler` ; 다음과 같이 다시 작성할 수 있습니다</span><span class="sxs-lookup"><span data-stu-id="c172e-144">For example the previous `BadgeEntryHandler` could be rewritten as follows;</span></span>

```csharp
services.AddAuthorization(options =>
    {
        options.AddPolicy("BadgeEntry",
                          policy => policy.RequireAssertion(context =>
                                  context.User.HasClaim(c =>
                                     (c.Type == ClaimTypes.BadgeId ||
                                      c.Type == ClaimTypes.TemporaryBadgeId)
                                      && c.Issuer == "https://microsoftsecurity"));
                          }));
    }
 }
```

## <a name="accessing-mvc-request-context-in-handlers"></a><span data-ttu-id="c172e-145">처리기에서 MVC 요청 컨텍스트 액세스</span><span class="sxs-lookup"><span data-stu-id="c172e-145">Accessing MVC Request Context In Handlers</span></span>

<span data-ttu-id="c172e-146">`Handle` 권한 부여 처리기에서 구현 해야 하는 메서드에 두 개의 매개 변수가 있는 `AuthorizationContext` 및 `Requirement` 를 처리 하는 합니다.</span><span class="sxs-lookup"><span data-stu-id="c172e-146">The `Handle` method you must implement in an authorization handler has two parameters, an `AuthorizationContext` and the `Requirement` you are handling.</span></span> <span data-ttu-id="c172e-147">MVC 또는 Jabbr 등의 프레임 워크는에 개체를 추가할 수는 `Resource` 속성에는 `AuthorizationContext` 추가 정보를 통과 하도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="c172e-147">Frameworks such as MVC or Jabbr are free to add any object to the `Resource` property on the `AuthorizationContext` to pass through extra information.</span></span>

<span data-ttu-id="c172e-148">MVC의 인스턴스를 전달 하는 예를 들어 `Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext` 다른 MVC HttpContext RouteData 및 모든 항목에 액세스 하는 데 사용 되는 리소스 속성에 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="c172e-148">For example MVC passes an instance of `Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext` in the resource property which is used to access HttpContext, RouteData and everything else MVC provides.</span></span>

<span data-ttu-id="c172e-149">사용 하 여 `Resource` 속성은 특정 프레임 워크입니다.</span><span class="sxs-lookup"><span data-stu-id="c172e-149">The use of the `Resource` property is framework specific.</span></span> <span data-ttu-id="c172e-150">정보를 사용 하 여 `Resource` 속성에는 권한 부여 정책을 특정 프레임 워크를 제한 합니다.</span><span class="sxs-lookup"><span data-stu-id="c172e-150">Using information in the `Resource` property will limit your authorization policies to particular frameworks.</span></span> <span data-ttu-id="c172e-151">캐스팅 해야는 `Resource` 사용 하 여 속성의 `as` 키워드 및 다음 확인은 캐스트에 성공 코드 충돌 하지 않는 되도록와 `InvalidCastExceptions` ; 다른 프레임 워크에서 실행할 때</span><span class="sxs-lookup"><span data-stu-id="c172e-151">You should cast the `Resource` property using the `as` keyword, and then check the cast has succeed to ensure your code doesn't crash with `InvalidCastExceptions` when run on other frameworks;</span></span>

```csharp
if (context.Resource is Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext mvcContext)
{
    // Examine MVC specific things like routing data.
}
```
