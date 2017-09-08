---
title: "Id 구성"
uid: security/authentication/identity-configuration
ms.openlocfilehash: 7ccd89360a8c7f5c8c6dfac76df42898e18a116a
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/11/2017
---
# <a name="configure-identity"></a>Id 구성

ASP.NET Core Id는 응용 프로그램의 시작 클래스에는 쉽게 재정의할 수 있는 몇 가지 기본 동작에 있습니다.

## <a name="passwords-policy"></a>암호 정책

기본적으로 Id는 암호에 대문자, 소문자 문자 및 숫자를 포함 필요 합니다. 몇 가지 제한 사항이 있습니다. 암호 제한 간소화 하려는 경우 응용 프로그램의 시작 클래스를 수행할 수 있습니다.

[!code-csharp[Main](identity/sample/src/ASPET-IdentityDemo-PrimaryKeysConfig/Startup.cs?highlight=2&range=60-65)]

## <a name="applications-cookie-settings"></a>응용 프로그램의 쿠키 설정

암호 정책와 같은 응용 프로그램의 쿠키의 모든 설정은 시작 클래스에서 변경할 수 있습니다.

[!code-csharp[Main](identity/sample/src/ASPET-IdentityDemo-PrimaryKeysConfig/Startup.cs?highlight=2&range=72-80)]

## <a name="users-lockout"></a>사용자의 잠금

[!code-csharp[Main](identity/sample/src/ASPET-IdentityDemo-PrimaryKeysConfig/Startup.cs?highlight=2&range=67-70)]
