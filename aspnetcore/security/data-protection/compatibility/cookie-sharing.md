---
title: "쿠키 응용 프로그램 간 공유"
author: rick-anderson
description: 
keywords: "ASP.NET Core,ASP.NET,cookies,Interop,cookie 공유"
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 9a7aae45-8e21-4c54-950c-3c29df6c1082
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/compatibility/cookie-sharing
ms.openlocfilehash: dbf52b0a990a3627b8eded22db033c45d51ba6ad
ms.sourcegitcommit: 78d28178345a0eea91556e4cd1adad98b1446db8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2017
---
# <a name="sharing-cookies-between-applications"></a>쿠키 응용 프로그램 간 공유

웹 사이트 일반적으로 이루어져 많은 개별 웹 응용 프로그램, 작업 모든 함께 harmoniously 있습니다. 응용 프로그램 개발자를 우수한 single sign-on 환경을 제공 하려는 경우 종종 해야 서로 간에 인증 티켓을 공유 하는 사이트 내에서 다른 웹 응용 프로그램을 모두 합니다.

이 시나리오를 지원 하려면 데이터 보호 스택의 Katana 쿠키 인증 및 ASP.NET Core 쿠키 인증 티켓을 공유할 수 있도록 허용 합니다.

## <a name="sharing-authentication-cookies-between-applications"></a>응용 프로그램 간에 인증 쿠키를 공유합니다.

두 개의 ASP.NET Core 응용 프로그램과 인증 쿠키를 공유 하려면 다음과 같이 쿠키를 공유 해야 하는 각 응용 프로그램을 구성 합니다.

프로그램 쿠키에 대 한 데이터 보호 서비스를 설정 하려면 CookieAuthenticationOptions 및 ASP.NET에 맞게 AuthenticationScheme 메서드 사용 하 여 구성 4.X 합니다.

Identity 사용 중인 경우:

```csharp
app.AddIdentity<ApplicationUser, IdentityRole>(options =>
   {
       options.Cookies.ApplicationCookie.AuthenticationScheme = "ApplicationCookie";
       var protectionProvider = DataProtectionProvider.Create(new DirectoryInfo(@"c:\shared-auth-ticket-keys\"));
       options.Cookies.ApplicationCookie.DataProtectionProvider = protectionProvider;
       options.Cookies.ApplicationCookie.TicketDataFormat = new TicketDataFormat(protectionProvider.CreateProtector("Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationMiddleware", "Cookies", "v2"));
   });
   ```

사용 하 여 쿠키 직접:

```csharp
app.UseCookieAuthentication(new CookieAuthenticationOptions
   {
       DataProtectionProvider = DataProtectionProvider.Create(new DirectoryInfo(@"c:\shared-auth-ticket-keys\"))
   });
   ```
   
`DataProtectionProvider` 필요는 `Microsoft.AspNetCore.DataProtection.Extensions` NuGet 패키지 합니다.

이러한 방법으로 사용 하는 경우 DirectoryInfo 특히 인증 쿠키를 위해 따로 설정 하는 키 저장소 위치를 가리켜야 합니다. 쿠키 인증 미들웨어의는 이제 DataProtectionProvider 명시적으로 제공 된 구현을 사용 합니다. 응용 프로그램의 다른 부분에서 사용 되는 데이터 보호 시스템에서 격리 합니다. 응용 프로그램 이름은 무시 됩니다 (의도적으로 그러한 페이로드를 공유 하는 여러 응용 프로그램 가져오기 하려고 하므로).

>[!WARNING]
>와 같이 미사용 키는 암호화 됩니다 되도록는 DataProtectionProvider를 구성 해야는 아래 예제입니다.
>
>
>  ```csharp
>  app.UseCookieAuthentication(new CookieAuthenticationOptions
>  {
>      DataProtectionProvider = DataProtectionProvider.Create(
>          new DirectoryInfo(@"c:\shared-auth-ticket-keys\"),
>          configure =>
>          {
>              configure.ProtectKeysWithCertificate("thumbprint");
>          })
>  });
>  ```

## <a name="sharing-authentication-cookies-between-aspnet-4x-and-aspnet-core-applications"></a>인증 쿠키 ASP.NET 간의 공유 4.x 및 ASP.NET Core 응용 프로그램

ASP.NET Core 쿠키 인증 미들웨어와 호환 되는 인증 쿠키를 생성 하는 Katana 쿠키 인증 미들웨어를 사용 하 여 ASP.NET 4.x 응용 프로그램을 구성할 수 있습니다. 이렇게 하면 사이트에 걸쳐 부드러운 단일 로그인 경험을 제공 하면서 증분 대규모 사이트의 개별 응용 프로그램을 업그레이드 합니다.

>[!TIP]
> 기존 응용 프로그램 프로젝트의 Startup.Auth.cs에 UseCookieAuthentication 호출의 현재 상태에 따라 Katana 쿠키 인증 미들웨어를 사용 하는 경우 알 수 있습니다. Visual Studio 2013을 사용 하 여 만든 하 고 나중 Katana 쿠키 인증 미들웨어를 사용 하 여 기본적으로 ASP.NET 4.x 웹 응용 프로그램 프로젝트입니다.

> [!NOTE]
> ASP.NET 4.x 응용 프로그램이.NET Framework 4.5.1 대상으로 해야 합니다 또는 이상 그렇지 않은 경우 필요한 NuGet 패키지가 설치 되지 것입니다.

ASP.NET 4.x 응용 프로그램 및 ASP.NET Core 응용 프로그램 간의 인증 쿠키를 공유 하려면 위에서 설명 했 듯이 ASP.NET Core 응용 프로그램을 구성 하 다음 아래 단계를 수행 하 여 ASP.NET 4.x 응용 프로그램을 구성 합니다.

1.  각 ASP.NET 4.x 응용 프로그램에 Microsoft.Owin.Security.Interop 패키지를 설치 합니다.

2.   Startup.Auth.cs, 일반적으로 다음과 같이 표시 됩니다는 UseCookieAuthentication에 대 한 호출을 찾습니다.

    ```csharp
    app.UseCookieAuthentication(new CookieAuthenticationOptions
    {
        // ...
    });
    ```
    
3.  다음과 같이 UseCookieAuthentication에 대 한 호출을 수정, CookieName 키 저장소 위치로 초기화 DataProtectionProvider의 인스턴스를 제공 하는 ASP.NET Core 쿠키 인증 미들웨어에서 사용 하는 이름과 일치 하도록 변경 하 고 청크 형식이 호환 이므로 CookieManager interop으로 ChunkingCookieManager를 설정 합니다.

    ```csharp
    app.UseCookieAuthentication(new CookieAuthenticationOptions
       {
           AuthenticationType = DefaultAuthenticationTypes.ApplicationCookie,
           CookieName = ".AspNetCore.Cookies",
           // CookieName = ".AspNetCore.ApplicationCookie", (if you're using identity)
           // CookiePath = "...", (if necessary)
           // ...
           TicketDataFormat = new AspNetTicketDataFormat(
               new DataProtectorShim(
                   DataProtectionProvider.Create(new DirectoryInfo(@"c:\shared-auth-ticket-keys\"))
                   .CreateProtector("Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationMiddleware",
                   "Cookies", "v2"))),
           CookieManager = new ChunkingCookieManager()
       });
       ```
    ASP.NET Core 응용 프로그램을 지정 하며 동일한 설정을 사용 하 여 구성 해야 하는 동일한 저장소 위치를 가리키도록 DirectoryInfo에 있습니다.

ASP.NET 4.x 및 ASP.NET Core 응용 프로그램은 이제 인증 쿠키를 공유 하도록 구성 됩니다.

> [!NOTE]
> 동일한 사용자 데이터베이스에서 각 응용 프로그램에 대 한 id 시스템이 지정 되었는지 확인 해야 합니다. 그렇지 않으면 id 시스템 데이터베이스의 정보에 대해 인증 쿠키의 정보와 일치 하려고 하면 런타임 시 오류를 생성 합니다.
