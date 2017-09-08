---
title: "ASP.NET Core에 Windows 인증을 구성 합니다."
author: ardalis
description: "ASP.NET Core에 Windows 인증을 구성 하는 방법"
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 7/5/2017
ms.topic: article
ms.assetid: cf119f21-1a2b-49a2-b052-548ccb66ee83
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/authentication/windowsauth
ms.openlocfilehash: 008a647295334e957c33c6db7f80687645b3b928
ms.sourcegitcommit: 69b3255f8b6f5db9e7d21f391420602d7ba9f4db
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2017
---
# <a name="configure-windows-authentication-in-aspnet-core"></a>ASP.NET Core에 Windows 인증을 구성 합니다.

으로 [Steve Smith](https://ardalis.com)

IIS 또는 WebListener로 호스트 되는 ASP.NET Core 응용 프로그램에 대 한 Windows 인증을 구성할 수 있습니다.

## <a name="what-is-windows-authentication"></a>Windows 인증 이란

ASP.NET Core 응용 프로그램의 사용자를 인증 하는 운영 체제는 Windows 인증 사용 합니다. 서버에 다른 Windows 계정 또는 Active Directory 도메인 id를 사용 하 여 사용자를 식별 하는 회사 네트워크에서 실행 될 때 Windows 인증을 사용할 수 있습니다. Windows 인증은 사용자, 클라이언트 응용 프로그램 및 웹 서버는 동일한 Windows 도메인에 속해 있는 인트라넷 환경에 적합 한 인증 가장 안전한 형식입니다.

[Windows 인증에 대 한 자세한 정보 및 IIS에 대 한 설치](https://www.iis.net/configreference/system.webserver/security/authentication/windowsauthentication)합니다.

## <a name="enabling-windows-authentication-in-an-aspnet-core-application"></a>ASP.NET Core 응용 프로그램에서 Windows 인증을 사용 하도록 설정

Visual Studio 웹 응용 프로그램 템플릿은 Windows 인증을 지원 하도록 구성할 수 있습니다.

### <a name="using-the-windows-authentication-app-template"></a>Windows 인증 앱 템플릿을 사용 하 여

Visual Studio에서 합니다.
* 새 ASP.NET Core 웹 응용 프로그램을 만듭니다. 
* 템플릿 목록에서 웹 응용 프로그램을 선택 합니다.
* 인증 변경 단추를 선택 하 고 선택 **Windows 인증**합니다. 

응용 프로그램을 실행 합니다. 사용자 이름 상단에 표시 됩니다. 응용 프로그램의 오른쪽입니다.

![Windows 인증에 대 한 브라우저 스크린 샷](windowsauth/_static/browser-screenshot.png)

IIS Express를 사용 하 여 개발 작업에서는 서식 파일에 Windows 인증을 사용 하는 데 필요한 모든 구성을 제공 합니다. 다음 섹션에는 Windows 인증에 대해 수동으로 ASP.NET Core 응용 프로그램을 구성 하는 방법을 보여 줍니다.

### <a name="visual-studio-settings-for-windows-and-anonymous-authentication"></a>Windows 및 익명 인증에 대 한 visual Studio 설정

Visual Studio 속성 페이지, 디버그 탭 Windows 인증 및 익명 인증에 대 한 확인란을 제공 합니다.

![Windows 인증에 대 한 브라우저 스크린 샷](windowsauth/_static/vs-auth-property-menu.png)

이러한 속성을 구성할 수도 있습니다는 `launchSettings.json` 파일:

```json
{
  "iisSettings": {
    "windowsAuthentication": true,
    "anonymousAuthentication": false,
    "iisExpress": {
      "applicationUrl": "http://localhost:52171/",
      "sslPort": 0
    }
  } // additional options trimmed
}
```

## <a name="enabling-windows-authentication-with-iis"></a>Iis Windows 인증을 사용 하도록 설정

IIS에서 사용 하는 [ASP.NET Core 모듈](xref:fundamentals/servers/aspnet-core-module) ASP.NET Core 응용 프로그램을 호스트할 (ANCM). ANCM 흐름 Windows 인증을 IIS에 기본적으로입니다. Windows 인증의 구성이 IIS에서 응용 프로그램 프로젝트 내에서 수행 됩니다. 다음 섹션에서는 Windows 인증을 사용 하도록 ASP.NET Core 응용 프로그램을 구성 하려면 IIS 관리자를 사용 하는 방법을 보여 줍니다.

### <a name="create-a-new-iis-site"></a>새 IIS 사이트 만들기

이름 및 폴더를 지정 하 고 새 응용 프로그램 풀을 만들 수 있도록 허용 합니다.

### <a name="customize-authentication"></a>인증을 사용자 지정

사이트에 대 한 인증 메뉴를 엽니다.

![IIS 인증 메뉴](windowsauth/_static/iis-authentication-menu.png)

익명 인증을 사용 하지 않도록 설정 하 고 Windows 인증을 사용 하도록 설정 합니다.

![IIS 인증 설정](windowsauth/_static/iis-auth-settings.png)

### <a name="publish-your-project-to-the-iis-site-folder"></a>IIS 사이트 폴더에 프로젝트를 게시 합니다.

Visual Studio 또는.NET Core CLI를 사용 하 여 *게시* 대상 폴더에 응용 프로그램입니다.

![Visual Studio 게시 대화 상자](windowsauth/_static/vs-publish-app.png)

에 대 한 자세한 내용은 [를 IIS에 게시](https://docs.microsoft.com/aspnet/core/publishing/iis)합니다.

Windows 인증 작동 확인 응용 프로그램을 실행 합니다.

## <a name="enabling-windows-authentication-with-weblistener"></a>WebListener와 함께 Windows 인증을 사용 하도록 설정

Kestrel Windows 인증을 지원 하지 않지만 사용할 수 있습니다 [WebListener](xref:fundamentals/servers/weblistener) Windows에서 자체 호스팅된 시나리오를 지원 합니다. 다음 예제에서는 Windows 인증과 함께 WebListener를 사용 하도록 응용 프로그램의 웹 호스트를 구성 합니다.

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        var host = new WebHostBuilder()
            .UseWebListener(options =>
            {
                options.ListenerSettings.Authentication.Schemes = 
                    AuthenticationSchemes.Negotiate | AuthenticationSchemes.NTLM;
                options.ListenerSettings.Authentication.AllowAnonymous = false;
            })
            .UseContentRoot(Directory.GetCurrentDirectory())
            .UseStartup<Startup>()
            .Build();

        host.Run();
    }
}
```

## <a name="working-with-windows-authentication"></a>Windows 인증 사용

앱에서 Windows 인증 및 익명 액세스를 사용 하는 경우 사용할 수 있습니다는 ``[Authorize]`` 및 ``[AllowAnonymous]`` 특성입니다. 익명 사용된 안 함 필요 하지 않아도 앱 ``[Authorize]``앱 필수 인증으로 처리 됩니다., 익명 요청이 거부 됩니다. IIS 사이트를 구성 하는 경우 로컬 **하지** 익명 액세스를 허용 하는 ``[AllowAnonymous]`` 특성은 **하지** 익명 요청을 허용 합니다. ``[AllowAnonymous]`` 특성 재정의 ``[Authorize]`` 특성 익명 액세스를 허용 하는 앱 내에서 사용 합니다.

### <a name="impersonation"></a>가장

ASP.NET Core 가장을 구현 하지 않습니다. 앱에 대 한 모든 요청을 응용 프로그램 풀 또는 프로세스 id를 사용 하 여 응용 프로그램 id로 실행 됩니다. 사용 하 여 명시적으로 사용자를 대신 하 여 작업을 수행 해야 할 경우 ``WindowsIdentity.RunImpersonated``합니다. 이 컨텍스트에서 단일 작업을 실행 하 고 컨텍스트를 닫습니다. ``RunImpersonated`` 비동기 지원 하지 않으며 복잡 한 시나리오에 사용할 수 없습니다. 예를 들어 전체 요청 또는 미들웨어 체인 래핑은 지원 되지 않거나 것이 좋습니다.
