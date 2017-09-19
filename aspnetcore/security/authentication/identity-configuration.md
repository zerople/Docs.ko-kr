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
ms.openlocfilehash: adf577ae1e1c752c3b1a332ec94a7a7627a7a4b4
ms.sourcegitcommit: 76d42f09f3e0dd2f2105493eca6b29994aa47706
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/19/2017
---
# <a name="configure-identity"></a>Id 구성

ASP.NET Core Id 된 응용 프로그램에서 쉽게 재정의할 수 있는 몇 가지 기본 동작이 `Startup` 클래스입니다.

## <a name="passwords-policy"></a>암호 정책

기본적으로 Id는 암호에 대문자, 소문자, 숫자, 및는 영숫자 문자가 포함 필요 합니다. 몇 가지 제한 사항이 있습니다. 암호 제한 간소화 하려는 경우 있습니다에서 이렇게 할 수는 `Startup` 응용 프로그램의 클래스입니다.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

ASP.NET Core 추가 2.0는 `RequiredUniqueChars` 속성입니다. 그렇지 않으면 옵션은 ASP.NET Core를 동일한 1.x 합니다.

[!code-csharp[Main](identity/sample/src/ASPNETv2-IdentityDemo-Configuration/Startup.cs?range=29-37,50-52)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

[!code-csharp[Main](identity/sample/src/ASPNET-IdentityDemo-PrimaryKeysConfig/Startup.cs?range=58-65,84)]

---

`IdentityOptions.Password`에 다음과 같은 속성이 있습니다.
* `RequireDigit`: 암호에 0-9 사이의 숫자를 여야 합니다. 기본값은 true입니다.
* `RequiredLength`암호의: 최소 길이입니다. 기본값은 6입니다.
* `RequireNonAlphanumeric`: 암호에는 영숫자가 아닌 문자가 필요합니다. 기본값은 true입니다.
* `RequireUppercase`: 암호에는 대문자 문자가 필요합니다. 기본값은 true입니다.
* `RequireLowercase`: 암호에는 소문자 문자가 필요합니다. 기본값은 true입니다.
* `RequiredUniqueChars`: 암호에 고유한 문자 수가 필요합니다. 기본값은 1입니다.


## <a name="users-lockout"></a>사용자의 잠금

[!code-csharp[Main](identity/sample/src/ASPNETv2-IdentityDemo-Configuration/Startup.cs?range=29-30,39-42,50-52)]

`IdentityOptions.Lockout`에 다음과 같은 속성이 있습니다.
* `DefaultLockoutTimeSpan`: 시간의 양은 사용자가 잠겨 잠금이 발생 합니다. 기본값은 5 분입니다.
* `MaxFailedAccessAttempts`: 실패 한 액세스의 수에 잠금이 설정 된 경우 사용자가 잠겨 될 때까지 시도 합니다. 기본값은 5입니다.
* `AllowedForNewUsers`: 경우 새 사용자를 잠글 수를 결정 합니다. 기본값은 true입니다.


## <a name="sign-in-settings"></a>로그인 설정

[!code-csharp[Main](identity/sample/src/ASPNETv2-IdentityDemo-Configuration/Startup.cs?range=29-30,44-46,50-52)]

`IdentityOptions.SignIn`에 다음과 같은 속성이 있습니다.
* `RequireConfirmedEmail`:은 확인 된 전자 메일에 로그인 할 필요 합니다. 기본값은 false입니다.
* `RequireConfirmedPhoneNumber`: 확인 된 전화 번호가 시스템에 로그인에 필요 합니다. 기본값은 false입니다.


## <a name="user-validation-settings"></a>사용자 유효성 검사 설정

[!code-csharp[Main](identity/sample/src/ASPNETv2-IdentityDemo-Configuration/Startup.cs?range=29-30,48-52)]

`IdentityOptions.User`에 다음과 같은 속성이 있습니다.
* `RequireUniqueEmail`: 각 사용자에 게 고유한 전자 메일 필요 합니다. 기본값은 false입니다.
* `AllowedUserNameCharacters`: Username에서 허용된 되는 문자입니다. AbcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789-._@+ 기본값입니다.

## <a name="applications-cookie-settings"></a>응용 프로그램의 쿠키 설정

암호 정책와 같은 응용 프로그램의 쿠키의 모든 설정에서 변경할 수 있습니다는 `Startup` 클래스입니다.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

아래 `ConfigureServices` 에 `Startup` 클래스, 응용 프로그램의 쿠키를 구성할 수 있습니다.

[!code-csharp[Main](identity/sample/src/ASPNETv2-IdentityDemo-Configuration/Startup.cs?name=snippet_configurecookie)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

[!code-csharp[Main](identity/sample/src/ASPNET-IdentityDemo-PrimaryKeysConfig/Startup.cs?range=58-59,72-80,84)]

--- 

`CookieAuthenticationOptions`에 다음과 같은 속성이 있습니다.
* `Cookie.Name`: 쿠키의 이름입니다. 기본값입니다. AspNetCore.Cookies 합니다.
* `Cookie.HttpOnly`: True 인 경우는 쿠키는 클라이언트 쪽 스크립트에서 액세스할 수 없습니다. 기본값은 true입니다.
* `ExpireTimeSpan`: 쿠키에 저장 된 인증 티켓 시간 유효 하 게 유지 만들어진 시점에서 제어 합니다. 기본값은 14 일입니다.
* `LoginPath`: 사용자 권한이 부여 하지 않은 경우 로그인에이 경로로 이동 합니다. 기본값은 / 계정/로그인입니다.
* `LogoutPath`: 사용자 로그 아웃 하는 경우이 경로로 이동 합니다. / 계정/로그 아웃 하는 기본값입니다.
* `AccessDeniedPath`: 사용자 권한 부여 확인 실패 하면이 경로로 이동 합니다. / 계정/AccessDenied 기본값입니다.
* `SlidingExpiration`: True 인 경우 새 만료 시간 현재 쿠키 만료 창을 통해 중간 부분 이상으로 새로운 쿠키를 발급 합니다. 기본값은 true입니다.
* `ReturnUrlParameter`: ReturnUrlParameter는 401 권한이 없음된 상태 코드가 302 로그인 경로로 리디렉션으로 변경 되 면 미들웨어에서 추가 되는 쿼리 문자열 매개 변수의 이름을 결정 합니다.
* `AuthenticationScheme`:이 ASP.NET Core에 대 한 관련만 1.x 합니다. 특정 인증 체계에 대 한 논리적 이름입니다.
* `AutomaticAuthenticate`:이 플래그는 ASP.NET Core에 대 한 관련만 1.x 합니다. True 인 경우 쿠키 인증 모든 요청에서 실행 하 고 유효성 검사 하 고 자신이 만든 직렬화 된 모든 보안 주체를 다시 생성 해야 합니다.

