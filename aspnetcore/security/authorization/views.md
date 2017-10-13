---
title: "보기 기반 권한 부여"
author: rick-anderson
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 24ce40d8-9b83-4bae-9d4c-a66350fcc8f8
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/authorization/views
ms.openlocfilehash: 82c0c7282de34e496f529d964f99121ae2805c5a
ms.sourcegitcommit: e3b1726cc04e80dc28464c35259edbd3bc39a438
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/12/2017
---
# <a name="view-based-authorization"></a>보기 기반 권한 부여

<a name=security-authorization-views></a>

종종 개발자 표시, 숨기기 또는 그렇지 않으면 현재 사용자 id에 따라 UI를 수정 합니다. 권한 부여 서비스를 통해 MVC 뷰 내에서 액세스할 수 있습니다 [종속성 주입](../../fundamentals/dependency-injection.md#fundamentals-dependency-injection)합니다. Razor 뷰 사용을 권한 부여 서비스를 삽입할 수는 `@inject` 예를 들어 지시문 `@inject IAuthorizationService AuthorizationService` (필요 `@using Microsoft.AspNetCore.Authorization`). 모든 보기에서 권한 부여 서비스를 사용할 경우 배치 합니다.는 `@inject` 지시문에 `_ViewImports.cshtml` 파일에 `Views` 디렉터리입니다. 종속성 주입 보기에 대 한 자세한 내용은 참조 [뷰로 종속성 주입](../../mvc/views/dependency-injection.md)합니다.

호출 하 여 사용 권한 부여 서비스를 삽입 한 후의 `AuthorizeAsync` 정확히 같은 방식으로 검사 하는 동안에 메서드가 [리소스 기반 권한 부여](resourcebased.md#security-authorization-resource-based-imperative)합니다.

```cshtml
@if (await AuthorizationService.AuthorizeAsync(User, "PolicyName"))
   {
       <p>This paragraph is displayed because you fulfilled PolicyName.</p>
   }
   ```

일부 경우에는 리소스를 보기 모델을 되며 호출할 수 있습니다 `AuthorizeAsync` 정확히 같은 방식으로 검사 하는 동안에 [리소스 기반 권한 부여](resourcebased.md#security-authorization-resource-based-imperative);

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

```cshtml
   @if ((await AuthorizationService.AuthorizeAsync(User, Model, Operations.Edit)).Succeeded)
   {
       <p><a class="btn btn-default" role="button"
           href="@Url.Action("Edit", "Document", new { id = Model.Id })">Edit</a></p>
   }
   ```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

```cshtml
   @if (await AuthorizationService.AuthorizeAsync(User, Model, Operations.Edit))
   {
       <p><a class="btn btn-default" role="button"
           href="@Url.Action("Edit", "Document", new { id = Model.Id })">Edit</a></p>
   }
   ```
---

여기서 모델 리소스 권한 부여 고려해 야 하는 변수로 전달 된 확인할 수 있습니다.

>[!WARNING]
>표시 또는 숨기기 유일한 인증 방법으로 UI의 부분에 의존 하지 마십시오. UI를 숨기 거 요소 의미가 아니라 사용자가 액세스할 수 없습니다. 또한 컨트롤러 코드 내에서 사용자 권한을 부여 해야 합니다.
