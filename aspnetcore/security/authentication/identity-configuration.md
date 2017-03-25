---
title: "Id 구성 | Microsoft 문서"
uid: security/authentication/identity-configuration
translationtype: Machine Translation
ms.sourcegitcommit: 8679c436ef4c8611d91828fc076181d0584359af
ms.openlocfilehash: 587343364c5ba34ecab8cc6a23347171f5aeefb2
ms.lasthandoff: 03/23/2017

---
# <a name="configure-identity"></a>Id 구성

ASP.NET Core Id는 응용 프로그램의 시작 클래스에는 쉽게 재정의할 수 있는 몇 가지 기본 동작에 있습니다.

## <a name="passwords-policy"></a>암호의 정책

기본적으로 Id는 대문자, 소문자, 숫자 및 매크로 포함 하는 매우 안전한 암호 필요 단순화 하기 위해 필요한 경우도 제한 합니다. 작업을 수행 하는 매우 간단, 모든 구성은 응용 프로그램의 startup 클래스에 액세스할 수 있습니다.

[!code-csharp[주](identity/sample/src/ASPET-IdentityDemo-PrimaryKeysConfig/Startup.cs?highlight=2&range=60-65)]

## <a name="applications-cookie-settings"></a>응용 프로그램의 쿠키 설정

암호 정책의 동일한 기본 원칙에 응용 프로그램의 쿠키에 대 한 모든 설정을 startup 클래스에는 변경 될 수 있습니다.

[!code-csharp[주](identity/sample/src/ASPET-IdentityDemo-PrimaryKeysConfig/Startup.cs?highlight=2&range=72-80)]

## <a name="users-lockout"></a>사용자의 잠금

[!code-csharp[주](identity/sample/src/ASPET-IdentityDemo-PrimaryKeysConfig/Startup.cs?highlight=2&range=67-70)]
