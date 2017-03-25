---
title: "스키마에 의해 identity 제한 | Microsoft 문서"
author: rick-anderson
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: d3d6ca1b-b4b5-4bf7-898e-dcd90ec1bf8c
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/authorization/limitingidentitybyscheme
translationtype: Machine Translation
ms.sourcegitcommit: 010b730d2716f9f536fef889bc2f767afb648ef4
ms.openlocfilehash: c2a26e02532c54eaeeb82e3a0027ab3910777b99
ms.lasthandoff: 03/23/2017

---
# <a name="limiting-identity-by-scheme"></a>스키마에 의해 identity를 제한합니다.

<a name=security-authorization-limiting-by-scheme></a>

일부 시나리오에서는 단일 페이지 응용 프로그램 등 수는 여러 인증 방법을 끝나야 합니다. 예를 들어, 응용 프로그램 JavaScript 요청에 대 한 쿠키 기반 인증에 로그인 하 고 전달자 인증 사용할 수 있습니다. 일부 경우에는 인증 미들웨어의 여러 인스턴스를 할 수 있습니다. 예를 들어 두 개의 쿠키 미들웨어 여기서는 기본 id를 포함 하 고 추가 보안을 필요로 하는 작업을 요청 하는 사용자는 다단계 인증에 트리거되면 하나가 생성 됩니다.

예를 들어 인증 미들웨어 인증 하는 동안 구성 된 경우 인증 체계는 이름이 지정

```csharp
app.UseCookieAuthentication(new CookieAuthenticationOptions()
{
    AuthenticationScheme = "Cookie",
    LoginPath = new PathString("/Account/Unauthorized/"),
    AccessDeniedPath = new PathString("/Account/Forbidden/"),
    AutomaticAuthenticate = false
});

app.UseBearerAuthentication(options =>
{
    options.AuthenticationScheme = "Bearer";
    options.AutomaticAuthenticate = false;
});
```

이 구성에서 두 인증 미들웨어를 추가한 전달자에 대 한 여러 개 있는 쿠키에 대 한 합니다.

>[!NOTE]
>여러 인증 미들웨어를 추가 하는 경우 미들웨어 없이 자동으로 실행 되도록 구성 되어 있는지 확인 해야 합니다. 설정 하 여이 작업을 수행 하는 `AutomaticAuthenticate` 속성을 false로 옵션입니다. 수행 하지 스키마에 의해 필터링이 작동 하지 않습니다.

## <a name="selecting-the-scheme-with-the-authorize-attribute"></a>Authorize 특성으로 구성표를 선택합니다.

인증 없음으로 미들웨어를 자동으로 실행 하 고, 권한 부여 시점에서 선택 해야는 미들웨어 사용할 id를 만들 구성 됩니다. 사용 권한을 부여 하려는 미들웨어를 선택 하는 가장 간단한 방법은 사용 하는 `ActiveAuthenticationSchemes` 속성입니다. 이 속성은 사용 하 여 인증 체계의 쉼표로 구분 된 목록이 허용 합니다. 예를 들어 있습니다.

```csharp
[Authorize(ActiveAuthenticationSchemes = "Cookie,Bearer")]
public class MixedController : Controller
```

쿠키와 전달자 위의 예에서 미들웨어 실행를 만들고 현재 사용자에 대 한 id를 추가 합니다. 단일 구성표를 지정 하 여 지정한 미들웨어만 실행 됩니다.

```csharp
[Authorize(ActiveAuthenticationSchemes = "Bearer")]
```

전달자 체계와 미들웨어만을 실행 하는 경우 및 모든 쿠키를 기반으로 id를 무시 합니다.

## <a name="selecting-the-scheme-with-policies"></a>정책 사용 하 여 구성표를 선택합니다.

원하는 체계를 지정 하려는 경우 [정책](policies.md#security-authorization-policies-based) 설정할 수는 `AuthenticationSchemes` 정책에 추가할 때 컬렉션.

```csharp
options.AddPolicy("Over18", policy =>
{
    policy.AuthenticationSchemes.Add("Bearer");
    policy.RequireAuthenticatedUser();
    policy.Requirements.Add(new Over18Requirement());
});
```

이 예제는 Over18 정책에만 실행 됩니다에서 만든 id는 `Bearer` 미들웨어입니다.

