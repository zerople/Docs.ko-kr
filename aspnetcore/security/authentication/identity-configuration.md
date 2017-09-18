---
title: "ASP.NET Core Id 구성"
author: AdrienTorris
description: "ASP.NET Core Id 기본값을 이해 하 고 사용자 지정 값을 사용 하도록 다양 한 Id 속성을 구성 합니다."
keywords: "ASP.NET Core, Identity, 인증, 보안"
ms.author: scaddie
manager: wpickett
ms.date: 09/18/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/authentication/identity-configuration
ms.openlocfilehash: 629fcc2941b2d2fda9604a3eac04be3d0f5294b2
ms.sourcegitcommit: ddefc78270bd9b5ae0b1bd8de6c45f6977e7dceb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/18/2017
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
