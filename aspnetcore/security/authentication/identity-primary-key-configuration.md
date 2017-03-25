---
title: "Id 기본 키 데이터 형식 구성 | Microsoft 문서"
uid: security/authentication/identity-primary-key-configuration
translationtype: Machine Translation
ms.sourcegitcommit: beec578005e6c3f4d7fad39438dd4b67b06c8b3b
ms.openlocfilehash: 66cfdbe40dee89503588d43e4f893656e7d648ef
ms.lasthandoff: 03/23/2017

---
# <a name="configure-identity-primary-keys-data-type"></a>Id 기본 키 데이터 형식 구성

ASP.NET Core Id를 통해 기본 키에 대 한 원하는 데이터 형식을 쉽게 구성할 수 있습니다. 기본적으로 사용 하 여 Identity 문자열 데이터 형식 하지만 매우 신속 하 게이 동작을 재정의할 수 있습니다.

## <a name="how-to"></a>방법

1.  첫 번째 단계는이 Id의 모델을 구현 하 고 재정의 <string> 동작을 <data type you want>의 하나입니다.

    [!code-csharp[주](identity/sample/src/ASPET-IdentityDemo-PrimaryKeysConfig/Models/ApplicationUser.cs?highlight=4-6&range=7-13)]

    [!code-csharp[주](identity/sample/src/ASPET-IdentityDemo-PrimaryKeysConfig/Models/ApplicationRole.cs?highlight=3-5&range=7-12)]
    
2.  모델 및 기본 키에 대해 원하는 데이터 형식이 Id의 데이터베이스 컨텍스트를 구현 합니다.

    [!code-csharp[주](identity/sample/src/ASPET-IdentityDemo-PrimaryKeysConfig/Data/ApplicationDbContext.cs?highlight=3&range=9-26)]
    
3.  모델 및 응용 프로그램의 시작 클래스의 id 서비스를 선언 하는 경우 기본 키에 대해 원하는 데이터 형식을 사용 하 여

    [!code-csharp[주](identity/sample/src/ASPET-IdentityDemo-PrimaryKeysConfig/Startup.cs?highlight=9-11&range=39-79)]

