---
title: "ASP.NET Core Identity 없이 쿠키 미들웨어를 사용 하 여 | Microsoft 문서"
author: rick-anderson
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 2bdcbf95-8d9d-4537-a4a0-a5ee439dcb62
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/authentication/cookie
translationtype: Machine Translation
ms.sourcegitcommit: 68724aeff1d36084c636452683349ffa6255b943
ms.openlocfilehash: 5ca23b768f52832d0754f27ec9a7bc16ac400661
ms.lasthandoff: 03/23/2017

---
# <a name="using-cookie-middleware-without-aspnet-core-identity"></a>ASP.NET Core Identity 없이 쿠키 미들웨어를 사용 하 여

<a name=security-authentication-cookie-middleware></a>

ASP.NET Core 쿠키를 제공 합니다. [미들웨어](../../fundamentals/middleware.md#fundamentals-middleware) 에 할당를 주 서버를 다시 만들고 암호화 된 쿠키에 사용자 보안 주체를 serialize 하 고 그런 다음 이후 요청에서의 유효성을 검사에서 쿠키를는 `User` 속성을 `HttpContext`합니다. 고유한 로그인 화면 및 사용자 데이터베이스를 제공 하려는 경우에 독립 실행형 기능으로 쿠키 미들웨어를 사용할 수 있습니다.

<a name=security-authentication-cookie-middleware-configuring></a>

## <a name="adding-and-configuring"></a>추가 및 구성

첫 번째 단계는 쿠키 미들웨어 응용 프로그램에 추가 합니다. 먼저 nuget을 사용 하 여 추가 하 여 `Microsoft.AspNetCore.Authentication.Cookies` 패키지 합니다. 다음에 다음 줄을 추가 `Configure` 에서 메서드 프로그램 *Startup.cs* 하기 전에 파일의 `app.UseMvc()` 문;

```csharp
app.UseCookieAuthentication(new CookieAuthenticationOptions()
   {
       AuthenticationScheme = "MyCookieMiddlewareInstance",
       LoginPath = new PathString("/Account/Unauthorized/"),
       AccessDeniedPath = new PathString("/Account/Forbidden/"),
       AutomaticAuthenticate = true,
       AutomaticChallenge = true
   });
   ```

몇 가지 옵션을 구성 하는 위의 코드 조각

* AuthenticationScheme-미들웨어 라고 하는 값입니다. 미들웨어의 여러 인스턴스가 있는 하려는 경우이 방법이 유용 [인스턴스 하나에 대 한 권한 부여 제한](../authorization/limitingidentitybyscheme.md#security-authorization-limiting-by-scheme)합니다.

* LoginPath-사용자는 리소스에 액세스 하려고 하지만 인증 되지 않은 경우에 요청을 리디렉션할 수는 상대 경로입니다.

* 사용자 리소스에 액세스 하려고 하지만 통과 하지 못하는 경우에 요청을 리디렉션할 수는 상대 경로 AccessDeniedPath- [권한 부여 정책](../authorization/policies.md#security-authorization-policies-based) 해당 리소스에 대 한 합니다.

* AutomaticAuthenticate-이 플래그는 미들웨어 모든 요청에서 실행 하 고 유효성 검사 하 고 자신이 만든 모든 직렬화 된 보안 주체를 재구성 해야 나타냅니다.

* AutomaticChallenge-이 플래그를 나타냅니다 미들웨어 브라우저를 리디렉션해야 하는 `LoginPath` 또는 `AccessDeniedPath` 권한 부여에 실패 한 경우.

[다른 옵션](xref:security/authentication/cookie#security-authentication-cookie-options) 쿠키에서 미들웨어를 삭제 하는 쿠키, 쿠키의 도메인 및 다양 한 보안 속성의 이름, 미들웨어 만듭니다 클레임의 발급자를 설정 하는 기능을 포함 합니다. 기본적으로 쿠키 미들웨어 HTTPONLY 쿠키 클라이언트측 JavaScript에서에서 액세스 하 고 HTTPS를 통한 요청으로 전환 된 경우 쿠키 HTTPS 제한 하지 않으려면 설정, 생성 된 쿠키에 대해 적절 한 보안 옵션을 사용 합니다.

<a name=security-authentication-cookie-middleware-creating-a-cookie></a>

## <a name="creating-an-identity-cookie"></a>Identity 쿠키 만들기

먼저 생성 해야 사용자 정보를 보유 하는 쿠키 만들려면는 [ClaimsPrincipal](https://msdn.microsoft.com/en-us/library/system.security.claims.claimsprincipal(v=vs.110).aspx) 쿠키에서 serialize 하려는 정보를 포함 합니다. 적절 한 있으면 *ClaimsPrincipal* 컨트롤러 메서드 호출 내


   ```csharp
   await HttpContext.Authentication.SignInAsync("MyCookieMiddlewareInstance", principal);
      ```

This will create an encrypted cookie and add it to the current response. The `AuthenticationScheme` specified during [configuration](xref:security/authentication/cookie#security-authentication-cookie-middleware-configuring) must also be used when calling `SignInAsync`.

Under the covers the encryption used is ASP.NET's [Data Protection](../data-protection/using-data-protection.md#security-data-protection-getting-started) system. If you are hosting on multiple machines, load balancing or using a web farm then you will need to [configure](../data-protection/configuration/overview.md#data-protection-configuring) data protection to use the same key ring and application identifier.

## Signing out

To sign out the current user, and delete their cookie call the following inside your controller

```csharp
await HttpContext.Authentication.SignOutAsync("MyCookieMiddlewareInstance");
   ```

## <a name="reacting-to-back-end-changes"></a>백 엔드 변경에 반응

>[!WARNING]
> 주 쿠키를 만든 후이 대해 알지 및 계속 자신의 쿠키의 유효으로 로그인 한 상태를 유지 하는 사용자가 쿠키 미들웨어에 백 엔드 시스템에 사용자를 비활성화 하는 경우에 id-의 단일 원본이 됩니다.

쿠키 인증 미들웨어는 옵션 클래스에는 일련의 이벤트를 제공합니다. `ValidateAsync()` 이벤트를 가로채 고 쿠키 id의 유효성 검사 재정의를 사용할 수 있습니다.

LastChanged 열을 가질 수 있는 백 엔드 사용자 데이터베이스를 고려해 야 합니다. 데이터베이스 변경 내용을 수행 해야 먼저 때 쿠키를 무효화 하기 위해 때 [쿠키를 만드는](xref:security/authentication/cookie#security-authentication-cookie-middleware-creating-a-cookie), 현재 값을 포함 하는 LastChanged 클레임을 추가 합니다. 그런 다음 데이터베이스 변경 시 LastChanged 값도 업데이트 되어야 합니다.

에 대 한 재정의 구현 하는 `ValidateAsync()` 이벤트에 다음 서명이; 있는 메서드를 작성 해야 합니다

```csharp
Task ValidateAsync(CookieValidatePrincipalContext context);
   ```

ASP.NET 핵심 Id의 일부로이 검사를 구현 하는 `SecurityStampValidator`합니다. 간단한 예로 다음과 비슷한 형태가 다음과 같습니다.

```csharp
public static class LastChangedValidator
   {
       public static async Task ValidateAsync(CookieValidatePrincipalContext context)
       {
           // Pull database from registered DI services.
           var userRepository = context.HttpContext.RequestServices.GetRequiredService<IUserRepository>();
           var userPrincipal = context.Principal;

           // Look for the last changed claim.
           string lastChanged;
           lastChanged = (from c in userPrincipal.Claims
                          where c.Type == "LastUpdated"
                          select c.Value).FirstOrDefault();

           if (string.IsNullOrEmpty(lastChanged) ||
               !userRepository.ValidateLastChanged(userPrincipal, lastChanged))
           {
               context.RejectPrincipal();
               await context.HttpContext.Authentication.SignOutAsync("MyCookieMiddlewareInstance");
           }
       }
   }
   ```

이 다음으로 연결할 쿠키 미들웨어 구성 중

```csharp
app.UseCookieAuthentication(options =>
   {
       options.Events = new CookieAuthenticationEvents
       {
           // Set other options
           OnValidatePrincipal = LastChangedValidator.ValidateAsync
       };
   });
   ```

안전 하 게 사용자 보안 주체를 업데이트 하려는 경우 예를 들어 자신의 이름을 업데이트, 호출할 수 있는 보안에 전혀 영향을 주지 않습니다 의사 결정 `context.ReplacePrincipal()` 설정 하 고는 `context.ShouldRenew` 플래그를 `true`합니다.

<a name=security-authentication-cookie-options></a>

## <a name="controlling-cookie-options"></a>쿠키 옵션 제어

`CookieAuthenticationOptions` 클래스는 사용 하면 미세 조정 하려면 만든 쿠키를 다양 한 구성 옵션을 사용 합니다.

* **ClaimsIssuer** -에 사용할 발급자는 [발급자](https://msdn.microsoft.com/en-us/library/microsoft.identitymodel.claims.claim.issuer.aspx) 미들웨어에서 만든 모든 클레임에는 속성입니다.

* **CookieDomain** -도메인 이름에 쿠키를 제공 합니다. 기본적으로이 이름은 호스트에 요청을 보냈습니다. 브라우저는 쿠키를 일치 하는 호스트 이름으로 역할만 합니다. 이 도메인의 모든 호스트에 사용할 수 있는 쿠키를 조정할 수도 있습니다. 예를 들어 쿠키 도메인 설정을 `.contoso.com` 를 사용할 수 있게 됩니다 `contoso.com`, `www.contoso.com`, `staging.www.contoso.com` 등입니다.

* **CookieHttpOnly** -쿠키만 서버에 액세스할 수 있어야 하는 경우를 나타내는 플래그입니다. 기본값은 `true`합니다. 이 값을 변경 될 수 있습니다 응용 프로그램을 열고 쿠키 도난 가능성이 응용 프로그램 사이트 간 스크립팅 버그 있어야 합니다.

* **CookiePath** -동일한 호스트 이름에서 실행 되는 응용 프로그램 격리를 사용할 수 있습니다. 실행 되는 앱이 있는 경우 `/app1` 전송 해당 응용 프로그램으로 설정 해야 하는 다음에 발급 된 쿠키를 제한 하려면는 `CookiePath` 속성을 `/app1`합니다. 쿠키는 이제에 사용할 수에 대 한 요청 `/app1` 또는 그 아래에 있는 항목입니다.

* **CookieSecure** -경우 만든 쿠키는 HTTPS, HTTP 또는 HTTPS를 제한 해야 합니다. 또는 동일한 요청으로 프로토콜을 나타내는 플래그입니다. 기본값은 `SameAsRequest`합니다.

* **ExpireTimeSpan** - `TimeSpan` 후 쿠키 만료 됩니다. 이 특성은 쿠키에 대 한 만료 날짜를 만들 현재 날짜 및 시간에 추가 합니다.

* **SlidingExpiration** -쿠키 만료 날짜 될 때 다시 설정 될 경우를 나타내는 플래그의 절반은 보다는 `ExpireTimeSpan` 간격이 경과 합니다. 새 만료 날짜가 현재 날짜를 앞으로 이동 됩니다와 `ExpireTimespan`합니다. [절대 만료 시간](xref:security/authentication/cookie#security-authentication-absolute-expiry) 를 사용 하 여 설정할 수는 `AuthenticationProperties` 클래스를 호출할 때 `SignInAsync`합니다. 절대 만료는 인증 쿠키의 유효 시간을 제한 하 여 응용 프로그램의 보안을 향상 시킬 수 있습니다.

## <a name="persistent-cookies-and-absolute-expiry-times"></a>영구 쿠키 및 절대 만료 시간

쿠키 만료를 확인 하려면 브라우저 세션에 대해 해당 레이아웃이 기억 합니다. Id는 쿠키를 전송 하는 것을 절대 만료를 할 수 있습니다. 사용 하 여 이러한 작업을 할 수 있는 `AuthenticationProperties` 에서 매개 변수는 `HttpContext.Authentication.SignInAsync` 때이 메서드가 호출 [쿠키를 만들고 로그인 id에](xref:security/authentication/cookie#security-authentication-cookie-middleware-creating-a-cookie)합니다. `AuthenticationProperties` 클래스는 `Microsoft.AspNetCore.Http.Authentication` 네임 스페이스입니다.

예를 들어 있습니다.

```csharp
await HttpContext.Authentication.SignInAsync(
       "MyCookieMiddlewareInstance",
       principal,
       new AuthenticationProperties
       {
           IsPersistent = true
       });
   ```

이 코드 조각에는 id 및 브라우저 클로저를 통해 유지가 해당 쿠키 만들어집니다. 슬라이딩 만료 설정을 통해 이전에 구성 된 [쿠키 옵션](xref:security/authentication/cookie#security-authentication-cookie-options) 쿠키는 브라우저 종료 하는 동안 만료 된 경우 브라우저는 확인란의 선택을 취소 다시 시작 되 면, 적용할 수 있습니다.

<a name=security-authentication-absolute-expiry></a>

<!-- literal_block {"ids": ["security-authentication-absolute-expiry"], "linenos": false, "names": ["security-authentication-absolute-expiry"], "xml:space": "preserve", "language": "csharp"} -->

```csharp
await HttpContext.Authentication.SignInAsync(
       "MyCookieMiddlewareInstance",
       principal,
       new AuthenticationProperties
       {
           ExpiresUtc = DateTime.UtcNow.AddMinutes(20)
       });
   ```

이 코드 조각에는 id 및 20 분 동안 지속 되며 해당 쿠키 만들어집니다. 슬라이딩 만료 설정을 통해 이전에 구성 된 무시 [쿠키 옵션](xref:security/authentication/cookie#security-authentication-cookie-options)합니다.

ExpiresUtc 및 IsPersistent 속성은 함께 사용할 수 없습니다.

