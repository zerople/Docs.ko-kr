---
title: "사용자 지정 정책 기반 권한 부여 | Microsoft 문서"
author: rick-anderson
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: e422a1b2-dc4a-4bcc-b8d9-7ee62009b6a3
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/authorization/policies
ms.translationtype: Machine Translation
ms.sourcegitcommit: 010b730d2716f9f536fef889bc2f767afb648ef4
ms.openlocfilehash: a7af01a0e8457428b8947c544d9921e2511daee2
ms.contentlocale: ko-kr
ms.lasthandoff: 03/23/2017

---
# <a name="custom-policy-based-authorization"></a>사용자 지정 정책 기반 권한 부여

<a name=security-authorization-policies-based></a>

내부적는 [역할 권한 부여](roles.md#security-authorization-role-based) 및 [클레임 권한 부여](claims.md#security-authorization-claims-based) 요구는 사용, 요구 사항 및 미리 구성 된 정책에 대 한 처리기를 확인 합니다. 이러한 빌딩 블록에는 허용 다양 하며, 재사용 가능한 코드를 쉽게 테스트할 권한 부여 구조에서 권한 부여 평가 표현할 수 있습니다.

권한 부여 정책 하나 이상의 요구 사항으로 구성 되며 응용 프로그램 시작 시에 권한 부여 서비스 구성의 일부로 등록 `ConfigureServices` 에 *Startup.cs* 파일입니다.

<!-- literal_block {"ids": [], "names": [], "highlight_args": {}, "backrefs": [], "dupnames": [], "linenos": false, "classes": [], "xml:space": "preserve", "language": "c#"} -->

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

여기서는 최소 연령의 변수로 전달 되는 매개 변수 요구 사항에 단일 요구 사항에는 "Over21" 정책이 만들어집니다 볼 수 있습니다.

사용 하 여 정책이 적용 되는 `Authorize` 예를 들어 정책 이름을 지정 하 여 특성

<!-- literal_block {"ids": [], "names": [], "highlight_args": {}, "backrefs": [], "dupnames": [], "linenos": false, "classes": [], "xml:space": "preserve", "language": "c#"} -->

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

## <a name="requirements"></a>요구 사항

권한 부여 요구 사항을 현재 사용자 보안 주체를 평가 하는 정책을 사용할 수 있는 데이터 매개 변수 컬렉션입니다. 최소 보존 기간 정책에는 요구 사항을 단일 매개 변수를 최소 기간. 요구 사항을 구현 해야 `IAuthorizationRequirement`합니다. 비어 있는 경우는 마커 인터페이스입니다. 매개 변수가 있는 최소 연령을 다음과 같이; 구현 될 수 있습니다.

<!-- literal_block {"ids": [], "names": [], "highlight_args": {}, "backrefs": [], "dupnames": [], "linenos": false, "classes": [], "xml:space": "preserve", "language": "c#"} -->

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

데이터 또는 속성 요구 사항이 필요 하지 않습니다.

<a name=security-authorization-policies-based-authorization-handler></a>

## <a name="authorization-handlers"></a>권한 부여 처리기

권한 부여 처리기의 요구 사항 속성의 계산 담당합니다. 권한 부여 처리기는 제공 된 기준으로 평가 해야 `AuthorizationHandlerContext` 권한 부여가 허용 하는 경우를 결정 합니다. 요구 사항이 있을 수 있습니다 [여러 처리기](policies.md#security-authorization-policies-based-multiple-handlers)합니다. 처리기에서 상속 해야 `AuthorizationHandler<T>` 여기서 T는 요구 사항을 처리 합니다.

<a name=security-authorization-handler-example></a>

최소 보존 기간 처리기는 다음과 같이 표시 될 수 있습니다.

<!-- literal_block {"ids": [], "names": [], "highlight_args": {}, "backrefs": [], "dupnames": [], "linenos": false, "classes": [], "xml:space": "preserve", "language": "c#"} -->

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

위의 코드에는 생년월일 알고 발급자와 신뢰에 의해 발급 되었는지는 클레임에 게 현재 사용자 계정이 있는지 소개 했습니다. 클레임이 누락 된 경우 반환 하도록 권한을 부여할 수 없습니다 했습니다. 오래 된 사용자가 파악 클레임, 있는 경우 및 요구 사항에 의해 전달 된 최소 보존 기간을 충족 하는 경우 다음 권한 부여 완료 됨. 권한 부여는 성공적으로 호출 하 여 `context.Succeed()` 매개 변수로 성공적으로 실행 되었는지 요구 사항을 전달 합니다.

<a name=security-authorization-policies-based-handler-registration></a>

예를 들어 처리기 구성 하는 동안 서비스 컬렉션에 등록 해야 합니다.

<!-- literal_block {"ids": [], "names": [], "highlight_args": {}, "backrefs": [], "dupnames": [], "linenos": false, "classes": [], "xml:space": "preserve", "language": "c#"} -->

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

각 처리기를 사용 하 여 서비스 컬렉션에 추가 됩니다 `services.AddSingleton<IAuthorizationHandler, YourHandlerClass>();` 처리기 클래스를 전달 합니다.

## <a name="what-should-a-handler-return"></a>처리기를 반환 해야?

볼 수 있듯이 우리의 [처리기 예제](policies.md#security-authorization-handler-example) 하는 `Handle()` 메서드 반환 값이 없는, 어떻게 수행 우리 성공 또는 실패를 나타내는?

* 처리기를 호출 하 여 성공을 나타냅니다 `context.Succeed(IAuthorizationRequirement requirement)`, 된 요구 사항을 전달 성공적으로 확인 되었습니다.

* 처리기를 배포할 수 있습니다. 동일한 요구 사항에 대해 다른 처리기 처럼 일반적으로 오류를 처리 하도록 필요는 없습니다.

* 오류는 요구 사항에 대해 다른 처리기 성공 하는 경우를 위해 호출 `context.Fail`합니다.

처리기 내 호출 하면 관계 없이 정책 요구 사항을 해야 하는 경우 요구 사항에 대 한 모든 처리기가 호출 됩니다. 이렇게 하면 요구 사항에는 항상 적용 하는 로깅 같은 의도 하지 않은 경우에 `context.Fail()` 다른 처리기에서 호출 했습니다.

<a name=security-authorization-policies-based-multiple-handlers></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a>이유는 여러 처리기 요구 사항에 대 한?

에 있는 것으로 평가 원하는 경우에는 **또는** 별로 하나의 요구 사항에 대해 여러 처리기를 구현 합니다. 예를 들어 Microsoft는 키 카드를 사용 하는 열만 있는 문. 키 카드를 집에 두고 접수원 임시 스티커를 인쇄 하 고 문을 자동으로 열립니다. 이 시나리오에서는 단일 요구 사항이 있을 *EnterBuilding*, 하지만 여러 처리기를 각각 하나의 단일 요구 사항을 검사 합니다.

<!-- literal_block {"ids": [], "names": [], "highlight_args": {}, "backrefs": [], "dupnames": [], "linenos": false, "classes": [], "xml:space": "preserve", "language": "c#"} -->

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

이제는 두 처리기 가정 [등록](xref:security/authorization/policies#security-authorization-policies-based-handler-registration) 정책을 평가 하면는 `EnterBuildingRequirement` 처리기 중 하나에 성공 하면 정책 평가 성공 합니다.

## <a name="using-a-func-to-fufill-a-policy"></a>Fufill 정책에 확인 되는 함수를 사용 하 여

경우가 fufilling 정책을 코드에서 표현 하기 간단 합니다. 간단히 제공 하는 것이 불가능 한 `Func<AuthorizationHandlerContext, bool>` 와 정책을 구성 하는 경우는 `RequireAssertion` 정책 작성기입니다.

예를 들어 이전 `BadgeEntryHandler` ; 다음과 같이 다시 작성할 수 있습니다

<!-- literal_block {"ids": [], "names": [], "highlight_args": {}, "backrefs": [], "dupnames": [], "linenos": false, "classes": [], "xml:space": "preserve", "language": "c#"} -->

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

## <a name="accessing-mvc-request-context-in-handlers"></a>처리기에서 MVC 요청 컨텍스트 액세스

`Handle` 권한 부여 처리기에서 구현 해야 하는 메서드에 두 개의 매개 변수는 `AuthorizationContext` 및 `Requirement` 를 처리 하는 합니다. MVC 또는 Jabbr과 같은 프레임 워크는 모든 개체를 추가할 수 있습니다는 `Resource` 속성에는 `AuthorizationContext` 추가 정보를 통과 하도록 합니다.

MVC의 인스턴스를 전달 하는 예를 들어 `Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext` 다른 MVC HttpContext, RouteData 및 모든 항목에 액세스 하는 데 사용 되는 리소스 속성을 제공 합니다.

사용 하 여 `Resource` 속성은 특정 프레임 워크입니다. 정보를 사용 하 여 `Resource` 속성은 권한 부여 정책을 특정 프레임 워크를 제한 합니다. 캐스팅 해야는 `Resource` 사용 하 여 속성의 `as` 키워드 및 캐스트에는 다음 체크 인 코드 충돌 하지 않도록 하려면 성공으로 `InvalidCastExceptions` ;의 다른 프레임 워크에서 실행할 때

<!-- literal_block {"ids": [], "names": [], "highlight_args": {}, "backrefs": [], "dupnames": [], "linenos": false, "classes": [], "xml:space": "preserve", "language": "c#"} -->

```csharp
if (context.Resource is Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext mvcContext)
{
    // Examine MVC specific things like routing data.
}
```

