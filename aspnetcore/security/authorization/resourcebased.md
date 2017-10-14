---
title: "리소스 기반 권한 부여"
author: rick-anderson
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 0902ba17-5304-4a12-a2d4-e0904569e988
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/authorization/resourcebased
ms.openlocfilehash: d3575619c53e77dadc293ea2bb7dc72501a8a1e3
ms.sourcegitcommit: 8f4d4fad1ca27adf9e396f5c205c9875a3963664
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/13/2017
---
# <a name="resource-based-authorization"></a><span data-ttu-id="7a3ba-103">리소스 기반 권한 부여</span><span class="sxs-lookup"><span data-stu-id="7a3ba-103">Resource Based Authorization</span></span>

<a name="security-authorization-resource-based"></a>

<span data-ttu-id="7a3ba-104">권한 부여 자주 액세스 되는 리소스에 따라 달라 집니다.</span><span class="sxs-lookup"><span data-stu-id="7a3ba-104">Often authorization depends upon the resource being accessed.</span></span> <span data-ttu-id="7a3ba-105">예를 들어, 문서 작성자 속성이 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7a3ba-105">For example, a document may have an author property.</span></span> <span data-ttu-id="7a3ba-106">리소스 권한 부여 평가 수행 하기 전에 문서 저장소에서 로드할 수 있도록 업데이트 문서 작성자만 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="7a3ba-106">Only the document author would be allowed to update it, so the resource must be loaded from the document repository before an authorization evaluation can be made.</span></span> <span data-ttu-id="7a3ba-107">이 같이 특성 평가 데이터 바인딩 전에 및 액션 내 리소스를 로드 하는 사용자 고유의 코드를 실행 하기 전에 권한 부여 특성으로 수행할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7a3ba-107">This cannot be done with an Authorize attribute, as attribute evaluation takes place before data binding and before your own code to load a resource runs inside an action.</span></span> <span data-ttu-id="7a3ba-108">선언적 권한 부여 특성 메서드를 대신 명령적 권한 부여 개발자가 자신의 코드 내에 권한 부여 함수를 호출 하는 경우 사용 해야 했습니다.</span><span class="sxs-lookup"><span data-stu-id="7a3ba-108">Instead of declarative authorization, the attribute method, we must use imperative authorization, where a developer calls an authorize function within their own code.</span></span>

## <a name="authorizing-within-your-code"></a><span data-ttu-id="7a3ba-109">코드 내에서 권한 부여</span><span class="sxs-lookup"><span data-stu-id="7a3ba-109">Authorizing within your code</span></span>

<span data-ttu-id="7a3ba-110">권한 부여는 서비스로 구현 됩니다 `IAuthorizationService`서비스 컬렉션에 등록 된, 및을 통해 사용할 [종속성 주입](../../fundamentals/dependency-injection.md#fundamentals-dependency-injection) 액세스 하는 컨트롤러에 대 한 합니다.</span><span class="sxs-lookup"><span data-stu-id="7a3ba-110">Authorization is implemented as a service, `IAuthorizationService`, registered in the service collection and available via [dependency injection](../../fundamentals/dependency-injection.md#fundamentals-dependency-injection) for Controllers to access.</span></span>

```csharp
public class DocumentController : Controller
{
    IAuthorizationService _authorizationService;

    public DocumentController(IAuthorizationService authorizationService)
    {
        _authorizationService = authorizationService;
    }
}
```

<span data-ttu-id="7a3ba-111">`IAuthorizationService`에 전달 하는 리소스와 정책 이름 및 다른 리소스 및 평가 하기 위한 요구 사항 목록을 전달 하면 한 두 개의 메서드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7a3ba-111">`IAuthorizationService` has two methods, one where you pass the resource and the policy name and the other where you pass the resource and a list of requirements to evaluate.</span></span>

```csharp
Task<bool> AuthorizeAsync(ClaimsPrincipal user,
                          object resource,
                          IEnumerable<IAuthorizationRequirement> requirements);
Task<bool> AuthorizeAsync(ClaimsPrincipal user,
                          object resource,
                          string policyName);
```

<a name="security-authorization-resource-based-imperative"></a>

<span data-ttu-id="7a3ba-112">서비스를 호출 하려면 작업 내에서 리소스를 로드 한 다음 호출는 `AuthorizeAsync` 필요한 오버 로드 합니다.</span><span class="sxs-lookup"><span data-stu-id="7a3ba-112">To call the service, load your resource within your action then call the `AuthorizeAsync` overload you require.</span></span> <span data-ttu-id="7a3ba-113">예:</span><span class="sxs-lookup"><span data-stu-id="7a3ba-113">For example:</span></span>

```csharp
public async Task<IActionResult> Edit(Guid documentId)
{
    Document document = documentRepository.Find(documentId);

    if (document == null)
    {
        return new HttpNotFoundResult();
    }

    if (await _authorizationService.AuthorizeAsync(User, document, "EditPolicy"))
    {
        return View(document);
    }
    else
    {
        return new ChallengeResult();
    }
}
```

## <a name="writing-a-resource-based-handler"></a><span data-ttu-id="7a3ba-114">리소스 기반 처리기 작성</span><span class="sxs-lookup"><span data-stu-id="7a3ba-114">Writing a resource based handler</span></span>

<span data-ttu-id="7a3ba-115">작성 리소스 기반 권한 부여에 대 한 처리기를 많이 다릅니다 하는 것은 [일반 요구 사항 처리기 작성](policies.md#security-authorization-policies-based-authorization-handler)합니다.</span><span class="sxs-lookup"><span data-stu-id="7a3ba-115">Writing a handler for resource based authorization is not that much different to [writing a plain requirements handler](policies.md#security-authorization-policies-based-authorization-handler).</span></span> <span data-ttu-id="7a3ba-116">에서는 요구 사항을 만들고 그런 다음 앞으로 요구 사항 및 리소스 종류를 지정 하는 요구 사항에 대 한 처리기를 구현 합니다.</span><span class="sxs-lookup"><span data-stu-id="7a3ba-116">You create a requirement, and then implement a handler for the requirement, specifying the requirement as before and also the resource type.</span></span> <span data-ttu-id="7a3ba-117">예를 들어 문서 리소스를 수락할 수 처리기 다음과 같이 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7a3ba-117">For example, a handler which might accept a Document resource would look as follows:</span></span>

```csharp
public class DocumentAuthorizationHandler : AuthorizationHandler<MyRequirement, Document>
{
    protected override Task HandleRequirementAsync(AuthorizationHandlerContext context,
                                                MyRequirement requirement,
                                                Document resource)
    {
        // Validate the requirement against the resource and identity.

        return Task.CompletedTask;
    }
}
```

<span data-ttu-id="7a3ba-118">에 처리기를 등록 해야 하는 데 반드시는 `ConfigureServices` 메서드:</span><span class="sxs-lookup"><span data-stu-id="7a3ba-118">Don't forget you also need to register your handler in the `ConfigureServices` method:</span></span>

```csharp
services.AddSingleton<IAuthorizationHandler, DocumentAuthorizationHandler>();
```

### <a name="operational-requirements"></a><span data-ttu-id="7a3ba-119">운영 요구 사항</span><span class="sxs-lookup"><span data-stu-id="7a3ba-119">Operational Requirements</span></span>

<span data-ttu-id="7a3ba-120">읽기, 쓰기, update 및 delete 등의 작업에 따라 하는 결정을 내려야 하는 경우 사용할 수 있습니다는 `OperationAuthorizationRequirement` 클래스에 `Microsoft.AspNetCore.Authorization.Infrastructure` 네임 스페이스입니다.</span><span class="sxs-lookup"><span data-stu-id="7a3ba-120">If you are making decisions based on operations such as read, write, update and delete, you can use the `OperationAuthorizationRequirement` class in the `Microsoft.AspNetCore.Authorization.Infrastructure` namespace.</span></span> <span data-ttu-id="7a3ba-121">이 미리 작성 된 요구 사항 클래스를 사용 하면 각 작업에 대 한 개별 클래스를 만드는 대신 매개 변수가 있는 작업 이름이 단일 처리기를 작성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7a3ba-121">This prebuilt requirement class enables you to write a single handler which has a parameterized operation name, rather than create individual classes for each operation.</span></span> <span data-ttu-id="7a3ba-122">이 기능을 사용 하려면 일부 작업 이름을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="7a3ba-122">To use it, provide some operation names:</span></span>

```csharp
public static class Operations
{
    public static OperationAuthorizationRequirement Create =
        new OperationAuthorizationRequirement { Name = "Create" };
    public static OperationAuthorizationRequirement Read =
        new OperationAuthorizationRequirement   { Name = "Read" };
    public static OperationAuthorizationRequirement Update =
        new OperationAuthorizationRequirement { Name = "Update" };
    public static OperationAuthorizationRequirement Delete =
        new OperationAuthorizationRequirement { Name = "Delete" };
}
```

<span data-ttu-id="7a3ba-123">처리기에 다음 구현할 수 있습니다 다음과 같은 가상의 사용 하 여 `Document` 리소스로 클래스:</span><span class="sxs-lookup"><span data-stu-id="7a3ba-123">Your handler could then be implemented as follows, using a hypothetical `Document` class as the resource:</span></span>

```csharp
public class DocumentAuthorizationHandler :
    AuthorizationHandler<OperationAuthorizationRequirement, Document>
{
    public override Task HandleRequirementAsync(AuthorizationHandlerContext context,
                                                OperationAuthorizationRequirement requirement,
                                                Document resource)
    {
        // Validate the operation using the resource, the identity and
        // the Name property value from the requirement.

        return Task.CompletedTask;
    }
}
```

<span data-ttu-id="7a3ba-124">처리기는 확인할 수 있습니다 `OperationAuthorizationRequirement`합니다.</span><span class="sxs-lookup"><span data-stu-id="7a3ba-124">You can see the handler works on `OperationAuthorizationRequirement`.</span></span> <span data-ttu-id="7a3ba-125">처리기 내에서 코드를 해당 평가 만들 때 제공 된 요구 사항 계정으로의 Name 속성을 취해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7a3ba-125">The code inside the handler must take the Name property of the supplied requirement into account when making its evaluations.</span></span>

<span data-ttu-id="7a3ba-126">호출할 때 작업을 지정 해야 하는 작업 리소스 처리기를 호출 하려면 `AuthorizeAsync` 사용자 작업에서 합니다.</span><span class="sxs-lookup"><span data-stu-id="7a3ba-126">To call an operational resource handler you need to specify the operation when calling `AuthorizeAsync` in your action.</span></span> <span data-ttu-id="7a3ba-127">예:</span><span class="sxs-lookup"><span data-stu-id="7a3ba-127">For example:</span></span>

```csharp
if (await _authorizationService.AuthorizeAsync(User, document, Operations.Read))
{
    return View(document);
}
else
{
    return new ChallengeResult();
}
```

<span data-ttu-id="7a3ba-128">이 예에서는 사용자가 현재에 대 한 읽기 작업을 수행할 수 있으면 확인 `document` 인스턴스.</span><span class="sxs-lookup"><span data-stu-id="7a3ba-128">This example checks if the User is able to perform the Read operation for the current `document` instance.</span></span> <span data-ttu-id="7a3ba-129">권한 부여에 성공 하면 문서에 대 한 보기 반환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7a3ba-129">If authorization succeeds the view for the document will be returned.</span></span> <span data-ttu-id="7a3ba-130">권한 부여 실패 반환 `ChallengeResult` 는 알립니다 모든 인증 미들웨어 권한 부여는 실패 했 고 미들웨어 적절 한 응답 예를 들어 403 또는 401 상태 코드를 반환 하거나 사용자에 대 한 로그인 페이지로 리디렉션하여 걸릴 수 있습니다 대화형 브라우저 클라이언트입니다.</span><span class="sxs-lookup"><span data-stu-id="7a3ba-130">If authorization fails returning `ChallengeResult` will inform any authentication middleware authorization has failed and the middleware can take the appropriate response, for example returning a 401 or 403 status code, or redirecting the user to a login page for interactive browser clients.</span></span>
