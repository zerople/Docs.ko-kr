---
title: "구성 Id 기본 키 데이터 형식"
uid: security/authentication/identity-primary-key-configuration
ms.openlocfilehash: e6661708d003aa50204e7f79d3070442a3440391
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/11/2017
---
# <a name="configure-identity-primary-keys-data-type"></a>구성 Id 기본 키 데이터 형식

ASP.NET Core Identity를 사용 하면 기본 키에 사용할 데이터 형식을 쉽게 구성할 수 있습니다. 기본적으로 사용 하 여 Identity string 데이터 형식 있지만 매우 신속 하 게이 동작을 재정의할 수 있습니다.

## <a name="how-to"></a>방법

1.  이 Id의 모델을 구현 하 고 원하는 데이터 형식 사용 하 여 문자열 형식을 재정의 하는 첫 번째 단계가입니다.

    [!code-csharp[Main](identity/sample/src/ASPET-IdentityDemo-PrimaryKeysConfig/Models/ApplicationUser.cs?highlight=4-6&range=7-13)]

    [!code-csharp[Main](identity/sample/src/ASPET-IdentityDemo-PrimaryKeysConfig/Models/ApplicationRole.cs?highlight=3-5&range=7-12)]
    
2.  모델 및 기본 키에 대해 원하는 데이터 형식이 Id의 데이터베이스 컨텍스트를 구현 합니다.

    [!code-csharp[Main](identity/sample/src/ASPET-IdentityDemo-PrimaryKeysConfig/Data/ApplicationDbContext.cs?highlight=3&range=9-26)]
    
3.  모델 및 응용 프로그램의 시작 클래스의 id 서비스를 선언 하는 경우 기본 키에 사용할 데이터 형식을 사용 하 여

    [!code-csharp[Main](identity/sample/src/ASPET-IdentityDemo-PrimaryKeysConfig/Startup.cs?highlight=9-11&range=39-79)]
