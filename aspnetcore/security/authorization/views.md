---
title: "보기 기반 권한 부여 | Microsoft 문서"
author: rick-anderson
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 24ce40d8-9b83-4bae-9d4c-a66350fcc8f8
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/authorization/views
translationtype: Machine Translation
ms.sourcegitcommit: 010b730d2716f9f536fef889bc2f767afb648ef4
ms.openlocfilehash: 34104e85324c1123886bd4c65197f98aa7d4597d
ms.lasthandoff: 03/23/2017

---
# <a name="view-based-authorization"></a>보기 기반 권한 부여

<a name=security-authorization-views></a>

종종 개발자 표시, 숨기기 또는 현재 사용자 id에 따라 UI를 수정 합니다. 권한 부여 서비스를 통해 MVC 뷰 내에서 액세스할 수 있습니다 [종속성 주입](../../fundamentals/dependency-injection.md#fundamentals-dependency-injection)합니다. Razor 보기를 사용 하도록 인증 서비스를 삽입 하는 `@inject` 예를 들어 지시문 `@inject IAuthorizationService AuthorizationService`합니다. 모든 보기에서 권한 부여 서비스를 하려는 경우 다음 배치는 `@inject` 지시문에 `_ViewImports.cshtml` 파일에 `Views` 디렉터리입니다. 종속성 주입 보기에 대 한 자세한 내용은 참조 [뷰로 종속성 주입](../../mvc/views/dependency-injection.md)합니다.

호출 하 여 사용 권한 부여 서비스를 삽입 한 후의 `AuthorizeAsync` 정확히 같은 방식으로 검사 하는 동안에 메서드 [리소스 기반 권한 부여](resourcebased.md#security-authorization-resource-based-imperative)합니다.

```csharp
@if (await AuthorizationService.AuthorizeAsync(User, "PolicyName"))
   {
       <p>This paragraph is displayed because you fulfilled PolicyName.</p>
   }
   ```

일부 경우에는 리소스 뷰 모델을 고를 호출할 수 있습니다 `AuthorizeAsync` 에 정확히 같은 방식으로 하는 동안 체크 [기반 권한 부여를 리소스](resourcebased.md#security-authorization-resource-based-imperative);

```csharp
@if (await AuthorizationService.AuthorizeAsync(User, Model, Operations.Edit))
   {
       <p><a class="btn btn-default" role="button"
           href="@Url.Action("Edit", "Document", new { id = Model.Id })">Edit</a></p>
   }
   ```

여기 모델 리소스 권한 부여 고려해 야 하는 변수로 전달 된 볼 수 있습니다.

>[!WARNING]
>표시 또는 숨기기 유일한 인증 방법으로 UI의 부분에 의존 하지 마십시오. UI를 숨기기 요소 의미가 아니라 사용자가 액세스할 수 없습니다. 또한 컨트롤러 코드 내에서 사용자 권한을 부여 해야 합니다.

