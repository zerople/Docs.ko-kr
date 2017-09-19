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
# <a name="configure-identity"></a><span data-ttu-id="394bb-104">Id 구성</span><span class="sxs-lookup"><span data-stu-id="394bb-104">Configure Identity</span></span>

<span data-ttu-id="394bb-105">ASP.NET Core Id 된 응용 프로그램에서 쉽게 재정의할 수 있는 몇 가지 기본 동작이 `Startup` 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="394bb-105">ASP.NET Core Identity has some default behaviors that you can override easily in your application's `Startup` class.</span></span>

## <a name="passwords-policy"></a><span data-ttu-id="394bb-106">암호 정책</span><span class="sxs-lookup"><span data-stu-id="394bb-106">Passwords policy</span></span>

<span data-ttu-id="394bb-107">기본적으로 Id는 암호에 대문자, 소문자, 숫자, 및는 영숫자 문자가 포함 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="394bb-107">By default, Identity requires that passwords contain an uppercase character, lowercase character, a digit, and an alphanumeric character.</span></span> <span data-ttu-id="394bb-108">몇 가지 제한 사항이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="394bb-108">There are also some other restrictions.</span></span> <span data-ttu-id="394bb-109">암호 제한 간소화 하려는 경우 있습니다에서 이렇게 할 수는 `Startup` 응용 프로그램의 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="394bb-109">If you want to simplify password restrictions, you can do that in the `Startup` class of your application.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="394bb-110">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="394bb-110">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="394bb-111">ASP.NET Core 추가 2.0는 `RequiredUniqueChars` 속성입니다.</span><span class="sxs-lookup"><span data-stu-id="394bb-111">ASP.NET Core 2.0 added the `RequiredUniqueChars` property.</span></span> <span data-ttu-id="394bb-112">그렇지 않으면 옵션은 ASP.NET Core를 동일한 1.x 합니다.</span><span class="sxs-lookup"><span data-stu-id="394bb-112">Otherwise, the options are the same from ASP.NET Core 1.x.</span></span>

[!code-csharp[Main](identity/sample/src/ASPNETv2-IdentityDemo-Configuration/Startup.cs?range=29-37,50-52)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="394bb-113">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="394bb-113">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

[!code-csharp[Main](identity/sample/src/ASPNET-IdentityDemo-PrimaryKeysConfig/Startup.cs?range=58-65,84)]

---

<span data-ttu-id="394bb-114">`IdentityOptions.Password`에 다음과 같은 속성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="394bb-114">`IdentityOptions.Password` has the following properties:</span></span>
* <span data-ttu-id="394bb-115">`RequireDigit`: 암호에 0-9 사이의 숫자를 여야 합니다.</span><span class="sxs-lookup"><span data-stu-id="394bb-115">`RequireDigit`: Requires a number between 0-9 in the password.</span></span> <span data-ttu-id="394bb-116">기본값은 true입니다.</span><span class="sxs-lookup"><span data-stu-id="394bb-116">Defaults to true.</span></span>
* <span data-ttu-id="394bb-117">`RequiredLength`암호의: 최소 길이입니다.</span><span class="sxs-lookup"><span data-stu-id="394bb-117">`RequiredLength`: The minimum length of the password.</span></span> <span data-ttu-id="394bb-118">기본값은 6입니다.</span><span class="sxs-lookup"><span data-stu-id="394bb-118">Defaults to 6.</span></span>
* <span data-ttu-id="394bb-119">`RequireNonAlphanumeric`: 암호에는 영숫자가 아닌 문자가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="394bb-119">`RequireNonAlphanumeric`: Requires a non-alphanumeric character in the password.</span></span> <span data-ttu-id="394bb-120">기본값은 true입니다.</span><span class="sxs-lookup"><span data-stu-id="394bb-120">Defaults to true.</span></span>
* <span data-ttu-id="394bb-121">`RequireUppercase`: 암호에는 대문자 문자가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="394bb-121">`RequireUppercase`: Requires an upper case character in the password.</span></span> <span data-ttu-id="394bb-122">기본값은 true입니다.</span><span class="sxs-lookup"><span data-stu-id="394bb-122">Defaults to true.</span></span>
* <span data-ttu-id="394bb-123">`RequireLowercase`: 암호에는 소문자 문자가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="394bb-123">`RequireLowercase`: Requires a lower case character in the password.</span></span> <span data-ttu-id="394bb-124">기본값은 true입니다.</span><span class="sxs-lookup"><span data-stu-id="394bb-124">Defaults to true.</span></span>
* <span data-ttu-id="394bb-125">`RequiredUniqueChars`: 암호에 고유한 문자 수가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="394bb-125">`RequiredUniqueChars`: Requires the number of distinct characters in the password.</span></span> <span data-ttu-id="394bb-126">기본값은 1입니다.</span><span class="sxs-lookup"><span data-stu-id="394bb-126">Defaults to 1.</span></span>


## <a name="users-lockout"></a><span data-ttu-id="394bb-127">사용자의 잠금</span><span class="sxs-lookup"><span data-stu-id="394bb-127">User's lockout</span></span>

[!code-csharp[Main](identity/sample/src/ASPNETv2-IdentityDemo-Configuration/Startup.cs?range=29-30,39-42,50-52)]

<span data-ttu-id="394bb-128">`IdentityOptions.Lockout`에 다음과 같은 속성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="394bb-128">`IdentityOptions.Lockout` has the following properties:</span></span>
* <span data-ttu-id="394bb-129">`DefaultLockoutTimeSpan`: 시간의 양은 사용자가 잠겨 잠금이 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="394bb-129">`DefaultLockoutTimeSpan`: The amount of time a user is locked out when a lockout occurs.</span></span> <span data-ttu-id="394bb-130">기본값은 5 분입니다.</span><span class="sxs-lookup"><span data-stu-id="394bb-130">Defaults to 5 minutes.</span></span>
* <span data-ttu-id="394bb-131">`MaxFailedAccessAttempts`: 실패 한 액세스의 수에 잠금이 설정 된 경우 사용자가 잠겨 될 때까지 시도 합니다.</span><span class="sxs-lookup"><span data-stu-id="394bb-131">`MaxFailedAccessAttempts`: The number of failed access attempts until a user is locked out, if lockout is enabled.</span></span> <span data-ttu-id="394bb-132">기본값은 5입니다.</span><span class="sxs-lookup"><span data-stu-id="394bb-132">Defaults to 5.</span></span>
* <span data-ttu-id="394bb-133">`AllowedForNewUsers`: 경우 새 사용자를 잠글 수를 결정 합니다. 기본값은 true입니다.</span><span class="sxs-lookup"><span data-stu-id="394bb-133">`AllowedForNewUsers`: Determines if a new user can be locked out. Defaults to true.</span></span>


## <a name="sign-in-settings"></a><span data-ttu-id="394bb-134">로그인 설정</span><span class="sxs-lookup"><span data-stu-id="394bb-134">Sign in settings</span></span>

[!code-csharp[Main](identity/sample/src/ASPNETv2-IdentityDemo-Configuration/Startup.cs?range=29-30,44-46,50-52)]

<span data-ttu-id="394bb-135">`IdentityOptions.SignIn`에 다음과 같은 속성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="394bb-135">`IdentityOptions.SignIn` has the following properties:</span></span>
* <span data-ttu-id="394bb-136">`RequireConfirmedEmail`:은 확인 된 전자 메일에 로그인 할 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="394bb-136">`RequireConfirmedEmail`: Requires a confirmed email to sign in.</span></span> <span data-ttu-id="394bb-137">기본값은 false입니다.</span><span class="sxs-lookup"><span data-stu-id="394bb-137">Defaults to false.</span></span>
* <span data-ttu-id="394bb-138">`RequireConfirmedPhoneNumber`: 확인 된 전화 번호가 시스템에 로그인에 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="394bb-138">`RequireConfirmedPhoneNumber`: Requires a confirmed phone number to sign in.</span></span> <span data-ttu-id="394bb-139">기본값은 false입니다.</span><span class="sxs-lookup"><span data-stu-id="394bb-139">Defaults to false.</span></span>


## <a name="user-validation-settings"></a><span data-ttu-id="394bb-140">사용자 유효성 검사 설정</span><span class="sxs-lookup"><span data-stu-id="394bb-140">User validation settings</span></span>

[!code-csharp[Main](identity/sample/src/ASPNETv2-IdentityDemo-Configuration/Startup.cs?range=29-30,48-52)]

<span data-ttu-id="394bb-141">`IdentityOptions.User`에 다음과 같은 속성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="394bb-141">`IdentityOptions.User` has the following properties:</span></span>
* <span data-ttu-id="394bb-142">`RequireUniqueEmail`: 각 사용자에 게 고유한 전자 메일 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="394bb-142">`RequireUniqueEmail`: Requires each User to have a unique email.</span></span> <span data-ttu-id="394bb-143">기본값은 false입니다.</span><span class="sxs-lookup"><span data-stu-id="394bb-143">Defaults to false.</span></span>
* <span data-ttu-id="394bb-144">`AllowedUserNameCharacters`: Username에서 허용된 되는 문자입니다.</span><span class="sxs-lookup"><span data-stu-id="394bb-144">`AllowedUserNameCharacters`: Allowed characters in the username.</span></span> <span data-ttu-id="394bb-145">AbcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789-._@+ 기본값입니다.</span><span class="sxs-lookup"><span data-stu-id="394bb-145">Defaults to abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789-._@+.</span></span>

## <a name="applications-cookie-settings"></a><span data-ttu-id="394bb-146">응용 프로그램의 쿠키 설정</span><span class="sxs-lookup"><span data-stu-id="394bb-146">Application's cookie settings</span></span>

<span data-ttu-id="394bb-147">암호 정책와 같은 응용 프로그램의 쿠키의 모든 설정에서 변경할 수 있습니다는 `Startup` 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="394bb-147">Like the passwords policy, all the settings of the application's cookie can be changed in the `Startup` class.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="394bb-148">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="394bb-148">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="394bb-149">아래 `ConfigureServices` 에 `Startup` 클래스, 응용 프로그램의 쿠키를 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="394bb-149">Under `ConfigureServices` in the `Startup` class, you can configure the application's cookie.</span></span>

[!code-csharp[Main](identity/sample/src/ASPNETv2-IdentityDemo-Configuration/Startup.cs?name=snippet_configurecookie)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="394bb-150">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="394bb-150">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

[!code-csharp[Main](identity/sample/src/ASPNET-IdentityDemo-PrimaryKeysConfig/Startup.cs?range=58-59,72-80,84)]

--- 

<span data-ttu-id="394bb-151">`CookieAuthenticationOptions`에 다음과 같은 속성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="394bb-151">`CookieAuthenticationOptions` has the following properties:</span></span>
* <span data-ttu-id="394bb-152">`Cookie.Name`: 쿠키의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="394bb-152">`Cookie.Name`: The name of the cookie.</span></span> <span data-ttu-id="394bb-153">기본값입니다. AspNetCore.Cookies 합니다.</span><span class="sxs-lookup"><span data-stu-id="394bb-153">Defaults to .AspNetCore.Cookies.</span></span>
* <span data-ttu-id="394bb-154">`Cookie.HttpOnly`: True 인 경우는 쿠키는 클라이언트 쪽 스크립트에서 액세스할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="394bb-154">`Cookie.HttpOnly`: When true, the cookie is not accessible from client-side scripts.</span></span> <span data-ttu-id="394bb-155">기본값은 true입니다.</span><span class="sxs-lookup"><span data-stu-id="394bb-155">Defaults to true.</span></span>
* <span data-ttu-id="394bb-156">`ExpireTimeSpan`: 쿠키에 저장 된 인증 티켓 시간 유효 하 게 유지 만들어진 시점에서 제어 합니다.</span><span class="sxs-lookup"><span data-stu-id="394bb-156">`ExpireTimeSpan`: Controls how much time the authentication ticket stored in the cookie will remain valid from the point it is created.</span></span> <span data-ttu-id="394bb-157">기본값은 14 일입니다.</span><span class="sxs-lookup"><span data-stu-id="394bb-157">Defaults to 14 days.</span></span>
* <span data-ttu-id="394bb-158">`LoginPath`: 사용자 권한이 부여 하지 않은 경우 로그인에이 경로로 이동 합니다.</span><span class="sxs-lookup"><span data-stu-id="394bb-158">`LoginPath`: When a user is unauthorized, they will be redirected to this path to login.</span></span> <span data-ttu-id="394bb-159">기본값은 / 계정/로그인입니다.</span><span class="sxs-lookup"><span data-stu-id="394bb-159">Defaults to /Account/Login.</span></span>
* <span data-ttu-id="394bb-160">`LogoutPath`: 사용자 로그 아웃 하는 경우이 경로로 이동 합니다.</span><span class="sxs-lookup"><span data-stu-id="394bb-160">`LogoutPath`: When a user is logged out, they will be redirected to this path.</span></span> <span data-ttu-id="394bb-161">/ 계정/로그 아웃 하는 기본값입니다.</span><span class="sxs-lookup"><span data-stu-id="394bb-161">Defaults to /Account/Logout.</span></span>
* <span data-ttu-id="394bb-162">`AccessDeniedPath`: 사용자 권한 부여 확인 실패 하면이 경로로 이동 합니다.</span><span class="sxs-lookup"><span data-stu-id="394bb-162">`AccessDeniedPath`: When a user fails an authorization check, they will be redirected to this path.</span></span> <span data-ttu-id="394bb-163">/ 계정/AccessDenied 기본값입니다.</span><span class="sxs-lookup"><span data-stu-id="394bb-163">Defaults to /Account/AccessDenied.</span></span>
* <span data-ttu-id="394bb-164">`SlidingExpiration`: True 인 경우 새 만료 시간 현재 쿠키 만료 창을 통해 중간 부분 이상으로 새로운 쿠키를 발급 합니다.</span><span class="sxs-lookup"><span data-stu-id="394bb-164">`SlidingExpiration`: When true, a new cookie will be issued with a new expiration time when the current cookie is more than halfway through the expiration window.</span></span> <span data-ttu-id="394bb-165">기본값은 true입니다.</span><span class="sxs-lookup"><span data-stu-id="394bb-165">Defaults to true.</span></span>
* <span data-ttu-id="394bb-166">`ReturnUrlParameter`: ReturnUrlParameter는 401 권한이 없음된 상태 코드가 302 로그인 경로로 리디렉션으로 변경 되 면 미들웨어에서 추가 되는 쿼리 문자열 매개 변수의 이름을 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="394bb-166">`ReturnUrlParameter`: The ReturnUrlParameter determines the name of the query string parameter which is appended by the middleware when a 401 Unauthorized status code is changed to a 302 redirect onto the login path.</span></span>
* <span data-ttu-id="394bb-167">`AuthenticationScheme`:이 ASP.NET Core에 대 한 관련만 1.x 합니다.</span><span class="sxs-lookup"><span data-stu-id="394bb-167">`AuthenticationScheme`: This is only relevant for ASP.NET Core 1.x.</span></span> <span data-ttu-id="394bb-168">특정 인증 체계에 대 한 논리적 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="394bb-168">The logical name for a particular authentication scheme.</span></span>
* <span data-ttu-id="394bb-169">`AutomaticAuthenticate`:이 플래그는 ASP.NET Core에 대 한 관련만 1.x 합니다.</span><span class="sxs-lookup"><span data-stu-id="394bb-169">`AutomaticAuthenticate`: This flag is only relevant for ASP.NET Core 1.x.</span></span> <span data-ttu-id="394bb-170">True 인 경우 쿠키 인증 모든 요청에서 실행 하 고 유효성 검사 하 고 자신이 만든 직렬화 된 모든 보안 주체를 다시 생성 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="394bb-170">When true, cookie authentication should run on every request and attempt to validate and reconstruct any serialized principal it created.</span></span>

