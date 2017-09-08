---
title: "Identity 체계로 제한"
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
ms.openlocfilehash: 2483c441da317a5c29b611b3a4910eae3c01fd7a
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/11/2017
---
# <a name="limiting-identity-by-scheme"></a><span data-ttu-id="de570-103">Identity 체계로 제한</span><span class="sxs-lookup"><span data-stu-id="de570-103">Limiting identity by scheme</span></span>

<a name=security-authorization-limiting-by-scheme></a>

<span data-ttu-id="de570-104">일부 시나리오에서는 예: 단일 페이지 응용 프로그램 수 있기 여러 인증 방법을 끝나야 합니다.</span><span class="sxs-lookup"><span data-stu-id="de570-104">In some scenarios, such as Single Page Applications it is possible to end up with multiple authentication methods.</span></span> <span data-ttu-id="de570-105">예를 들어 응용 프로그램이 JavaScript 요청에 대 한 로그인 쿠키 기반 인증 및 전달자 인증 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="de570-105">For example, your application may use cookie-based authentication to log in and bearer authentication for JavaScript requests.</span></span> <span data-ttu-id="de570-106">경우에 따라 인증 미들웨어의 여러 인스턴스를 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="de570-106">In some cases you may have multiple instances of an authentication middleware.</span></span> <span data-ttu-id="de570-107">예를 들어 두 개의 쿠키 middlewares 여기서 기본 id를 포함 하 고 추가 보안을 필요로 하는 작업을 요청 하는 사용자는 multi-factor authentication에 트리거된 경우에 생성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="de570-107">For example, two cookie middlewares where one contains a basic identity and one is created when a multi-factor authentication has triggered because the user requested an operation that requires extra security.</span></span>

<span data-ttu-id="de570-108">예를 들어 인증 미들웨어 인증 하는 동안 구성 된 경우 인증 스키마 이름은</span><span class="sxs-lookup"><span data-stu-id="de570-108">Authentication schemes are named when authentication middleware is configured during authentication, for example</span></span>

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

<span data-ttu-id="de570-109">이 구성에서 두 인증 middlewares 추가한, 쿠키와 전달자에 대 한 합니다.</span><span class="sxs-lookup"><span data-stu-id="de570-109">In this configuration two authentication middlewares have been added, one for cookies and one for bearer.</span></span>

>[!NOTE]
><span data-ttu-id="de570-110">여러 인증 미들웨어를 추가 하는 경우 없는 미들웨어를 자동으로 실행 되도록 구성 되어 있는지 확인 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="de570-110">When adding multiple authentication middleware you should ensure that no middleware is configured to run automatically.</span></span> <span data-ttu-id="de570-111">설정 하 여이 작업을 수행는 `AutomaticAuthenticate` 속성을 false로 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="de570-111">You do this by setting the `AutomaticAuthenticate` options property to false.</span></span> <span data-ttu-id="de570-112">수행 하지 스키마에 의해 필터링이 작동 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="de570-112">If you fail to do this filtering by scheme will not work.</span></span>

## <a name="selecting-the-scheme-with-the-authorize-attribute"></a><span data-ttu-id="de570-113">권한 부여 특성을 가진 구성표 선택</span><span class="sxs-lookup"><span data-stu-id="de570-113">Selecting the scheme with the Authorize attribute</span></span>

<span data-ttu-id="de570-114">인증 안 함으로 미들웨어는 자동으로 실행 하 고, 권한 부여 시점에서 선택 해야 미들웨어가 사용 하는 id를 생성 하도록 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="de570-114">As no authentication middleware is configured to automatically run and create an identity you must, at the point of authorization choose which middleware will be used.</span></span> <span data-ttu-id="de570-115">사용 하는 사용 권한을 부여 하려는 미들웨어를 선택 하는 가장 간단한 방법은 `ActiveAuthenticationSchemes` 속성입니다.</span><span class="sxs-lookup"><span data-stu-id="de570-115">The simplest way to select the middleware you wish to authorize with is to use the `ActiveAuthenticationSchemes` property.</span></span> <span data-ttu-id="de570-116">이 속성은 사용 하도록 인증 체계의 쉼표로 구분 된 목록을 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="de570-116">This property accepts a comma delimited list of Authentication Schemes to use.</span></span> <span data-ttu-id="de570-117">예를 들어</span><span class="sxs-lookup"><span data-stu-id="de570-117">For example;</span></span>

```csharp
[Authorize(ActiveAuthenticationSchemes = "Cookie,Bearer")]
public class MixedController : Controller
```

<span data-ttu-id="de570-118">쿠키와 전달자 위 예제에서 middlewares 실행 되어는 기회를 만들고 현재 사용자에 대 한 id를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="de570-118">In the example above both the cookie and bearer middlewares will run and have a chance to create and append an identity for the current user.</span></span> <span data-ttu-id="de570-119">하나의 방법을 지정 하 여 지정 된 미들웨어만 실행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="de570-119">By specifying a single scheme only the specified middleware will run;</span></span>

```csharp
[Authorize(ActiveAuthenticationSchemes = "Bearer")]
```

<span data-ttu-id="de570-120">이 경우에 사용 하 여 미들웨어 전달자 체계가 실행 하 고 모든 쿠키를 기반으로 id는 무시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="de570-120">In this case only the middleware with the Bearer scheme would run, and any cookie based identities would be ignored.</span></span>

## <a name="selecting-the-scheme-with-policies"></a><span data-ttu-id="de570-121">정책 사용 하 여 구성표 선택</span><span class="sxs-lookup"><span data-stu-id="de570-121">Selecting the scheme with policies</span></span>

<span data-ttu-id="de570-122">원하는 체계를 지정 하려는 경우 [정책](policies.md#security-authorization-policies-based) 설정할 수 있습니다는 `AuthenticationSchemes` 정책에 추가할 때 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="de570-122">If you prefer to specify the desired schemes in [policy](policies.md#security-authorization-policies-based) you can set the `AuthenticationSchemes` collection when adding your policy.</span></span>

```csharp
options.AddPolicy("Over18", policy =>
{
    policy.AuthenticationSchemes.Add("Bearer");
    policy.RequireAuthenticatedUser();
    policy.Requirements.Add(new Over18Requirement());
});
```

<span data-ttu-id="de570-123">이 예제는 Over18 정책 됩니다 대해서만 실행 하 여 만든 id는 `Bearer` 미들웨어입니다.</span><span class="sxs-lookup"><span data-stu-id="de570-123">In this example the Over18 policy will only run against the identity created by the `Bearer` middleware.</span></span>
