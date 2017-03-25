---
title: "리소스 기반 권한 부여 | Microsoft 문서"
author: rick-anderson
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 0902ba17-5304-4a12-a2d4-e0904569e988
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/authorization/resourcebased
translationtype: Machine Translation
ms.sourcegitcommit: 010b730d2716f9f536fef889bc2f767afb648ef4
ms.openlocfilehash: fdc08879c2919a9754450ec071ba59f8c4dc6044
ms.lasthandoff: 03/23/2017

---
# <a name="resource-based-authorization"></a>리소스 기반 권한 부여

<a name=security-authorization-resource-based></a>

권한 부여 자주 액세스 되는 리소스에 따라 달라 집니다. 예를 들어 문서 작성자 속성이 있을 수 있습니다. 권한 부여 평가 수행 하기 전에 문서 저장소에서 리소스를 로드 해야 하므로 업데이트 문서 작성자만 허용 합니다. 와 같이 특성 평가 데이터 바인딩 전에 및 액션 내 리소스를 로드 하는 코드를 실행 하기 전에 Authorize 특성으로 수행할 수 없습니다. 선언적 권한 부여 특성 메서드를 대신 사용 해야 명령적 권한 부여, 개발자가 자신의 코드에는 권한 부여 함수를 호출 하는 경우.

## <a name="authorizing-within-your-code"></a>코드 내에서 권한 부여

권한 부여는 서비스로 구현 됩니다 `IAuthorizationService`서비스 컬렉션에 등록 하 고을 통해 사용할 수 있는, [종속성 주입](../../fundamentals/dependency-injection.md#fundamentals-dependency-injection) 에 액세스 하는 컨트롤러에 대 한 합니다.

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

`IAuthorizationService`에 하나에 전달 하는 리소스 및 정책 이름 및 다른 리소스 및 평가 하는 요구 사항의 목록에 전달 하는 두 개의 메서드가 있습니다.

```csharp
Task<bool> AuthorizeAsync(ClaimsPrincipal user,
                             object resource,
                             IEnumerable<IAuthorizationRequirement> requirements);
   Task<bool> AuthorizeAsync(ClaimsPrincipal user,
                             object resource,
                             string policyName);
   ```

<a name=security-authorization-resource-based-imperative></a>

작업 내에서 리소스 서비스 부하를 호출 하려면 다음 호출는 `AuthorizeAsync` 필요한 오버 로드 합니다. 예

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

## <a name="writing-a-resource-based-handler"></a>리소스 기반 처리기 작성

크게 다르지 하는 것은 리소스 기반 권한 부여에 대 한 처리기를 작성 [일반 요구 사항 처리기 작성](policies.md#security-authorization-policies-based-authorization-handler)합니다. 반드시 그래야 만들고 그런 다음 앞으로 요구 사항 및 리소스 종류를 지정 하는 요구 사항에 대 한 처리기를 구현 합니다. 예를 들어 문서 리소스를 수락할 수 처리기 보이는지를 다음과 같습니다.

```csharp
public class DocumentAuthorizationHandler : AuthorizationHandler<MyRequirement, Document>
   {
       public override Task HandleRequirementAsync(AuthorizationHandlerContext context,
                                                   MyRequirement requirement,
                                                   Document resource)
       {
           // Validate the requirement against the resource and identity.

           return Task.CompletedTask;
       }
   }
   ```

에 처리기를 등록 해야 하는 데 잊지는 `ConfigureServices` 메서드도 있습니다.

```csharp
services.AddSingleton<IAuthorizationHandler, DocumentAuthorizationHandler>();
   ```

### <a name="operational-requirements"></a>운영 요구 사항

읽기, 쓰기, 업데이트 및 삭제 등의 작업에 따라 결정을 하는 경우 사용할 수 있습니다는 `OperationAuthorizationRequirement` 클래스에 `Microsoft.AspNetCore.Authorization.Infrastructure` 네임 스페이스입니다. 이 미리 작성 된 요구 사항 클래스를 사용 하면 각 작업에 대 한 개별 클래스를 만들기 보다는 매개 변수가 있는 작업 이름이 단일 처리기를 작성할 수 있습니다. 사용 하 여 일부 작업 이름을 제공 합니다.

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

처리기 다음 구현할 수 다음과 같이 가상을 사용 하 여 `Document` ; 리소스로 클래스

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

처리기는 볼 수 있습니다 `OperationAuthorizationRequirement`합니다. 처리기 내에서 코드의 평가 수행할 때 제공 된 요구 사항 계정으로의 Name 속성을 져야 합니다.

호출할 때 작업을 지정 해야 하는 작업 리소스 처리기를 호출 하 `AuthorizeAsync` 사용자 작업에서 합니다. 예

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

이 예제에서는 사용자 현재에 대 한 읽기 작업을 수행할 수 있는지 확인 `document` 인스턴스. 권한 부여에 성공 하면 문서에 대 한 보기 반환 됩니다. 권한 부여를 반환 하는 경우 실패 `ChallengeResult` 에 알립니다. 인증 미들웨어 권한 부여 실패 하 고 미들웨어 적절 한 응답 예를 들어 401 또는 403 상태 코드를 반환 하거나 대화형 브라우저 클라이언트에 대 한 사용자를 로그인 페이지로 리디렉션 걸릴 수 있습니다.

