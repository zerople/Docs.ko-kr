---
title: "구성 Id 기본 키 데이터 형식"
author: AdrienTorris
description: "이 문서에서는 ASP.NET Core Id 기본 키에 사용 되는 원하는 데이터 형식 구성 하기 위한 단계를 설명 합니다."
keywords: "ASP.NET Core, Identity, 기본 키"
ms.author: scaddie
manager: wpickett
ms.date: 09/28/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/authentication/identity-primary-key-configuration
ms.openlocfilehash: 5734a9aa86fb2831bd054593ad41c3e3bda4729e
ms.sourcegitcommit: 13291956ad858d465dfd46caa384df58f08e286b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2017
---
# <a name="configure-the-aspnet-core-identity-primary-key-data-type"></a>ASP.NET Core Id 기본 키 데이터 형식 구성

ASP.NET Core Identity를 사용 하면 기본 키를 나타내는 데 사용 되는 데이터 형식을 구성할 수 있습니다. 사용 하 여 identity는 `string` 기본적으로 데이터 형식입니다. 이 동작을 재정의할 수 있습니다.

## <a name="customize-the-primary-key-data-type"></a>기본 키 데이터 형식을 사용자 지정

1. 사용자 지정 구현을 만듭니다는 [IdentityUser](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.identity.entityframeworkcore.identityuser-1) 클래스입니다. 사용자 개체를 만드는 데 사용할 유형을 나타냅니다. 다음 예제에서는 기본 `string` 형식으로 대체 됩니다 `Guid`합니다.

    [!code-csharp[Main](identity/sample/src/ASPNET-IdentityDemo-PrimaryKeysConfig/Models/ApplicationUser.cs?highlight=4&range=7-13)]

1. 사용자 지정 구현을 만듭니다는 [IdentityRole](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.identity.entityframeworkcore.identityrole-1) 클래스입니다. 역할 개체를 만드는 데 사용할 유형을 나타냅니다. 다음 예제에서는 기본 `string` 형식으로 대체 됩니다 `Guid`합니다.
    
    [!code-csharp[Main](identity/sample/src/ASPNET-IdentityDemo-PrimaryKeysConfig/Models/ApplicationRole.cs?highlight=3&range=7-12)]
    
1. 사용자 지정 데이터베이스 컨텍스트 클래스를 만듭니다. Id에 사용 되는 Entity Framework 데이터베이스 컨텍스트 클래스에서 상속 합니다. `TUser` 및 `TRole` 인수는 각각 이전 단계에서 만든 사용자 지정 사용자 및 역할의 클래스를 참조 합니다. `Guid` 기본 키에 대 한 데이터 형식을 정의 합니다.

    [!code-csharp[Main](identity/sample/src/ASPNET-IdentityDemo-PrimaryKeysConfig/Data/ApplicationDbContext.cs?highlight=3&range=9-26)]
    
1. 응용 프로그램의 시작 클래스에서 Id 서비스를 추가할 때 사용자 지정 데이터베이스 컨텍스트 클래스를 등록 합니다.

    # <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)
    
    `AddEntityFrameworkStores` 이 메서드를 사용 하지 않는 한 `TKey` ASP.NET Core에서 수행한 것으로 인수 1.x 합니다. 분석 하 여 기본 키의 데이터 형식을 유추할는 `DbContext` 개체입니다.
    
    [!code-csharp[Main](identity/sample/src/ASPNETv2-IdentityDemo-PrimaryKeysConfig/Startup.cs?highlight=6-8&range=25-37)]
    
    # <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)
    
    `AddEntityFrameworkStores` 메서드에 `TKey` 기본 키의 데이터 형식을 나타내는 인수입니다.
    
    [!code-csharp[Main](identity/sample/src/ASPNET-IdentityDemo-PrimaryKeysConfig/Startup.cs?highlight=9-11&range=39-55)]
    
    ---

## <a name="test-the-changes"></a>변경 내용을 테스트합니다

구성 변경으로 완료 되 면 기본 키를 나타내는 속성에는 새 데이터 형식을 반영 합니다. 다음 예제에서는 MVC 컨트롤러의 속성에 액세스 하는 방법을 보여 줍니다.

[!code-csharp[Main](identity/sample/src/ASPNET-IdentityDemo-PrimaryKeysConfig/Controllers/AccountController.cs?name=snippet_GetCurrentUserId&highlight=6)]
