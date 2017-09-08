---
title: "Microsoft 계정 외부 로그인 설정"
author: rick-anderson
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 08/24/2017
ms.topic: article
ms.assetid: 66DB4B94-C78C-4005-BA03-3D982B87C268
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/authentication/microsoft-logins
ms.openlocfilehash: cc2d119ae2f7cf06ed0ea4b8a91fd8fa15804468
ms.sourcegitcommit: fb518f856f31fe53c09196a13309eacb85b37a22
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/08/2017
---
# <a name="configuring-microsoft-account-authentication"></a>Microsoft 계정 인증 구성

<a name=security-authentication-microsoft-logins></a>

여 [Valeriy Novytskyy](https://github.com/01binary) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)

이 자습서에서 만든 샘플 ASP.NET 코어 2.0 프로젝트를 사용 하 여 자신의 Microsoft 계정으로 로그인 하 여 사용자가 사용할 수 있도록 하는 방법을 보여 줍니다.는 [이전 페이지](index.md)합니다.

## <a name="create-the-app-in-microsoft-developer-portal"></a>Microsoft 개발자 포털에서 응용 프로그램 만들기

* 로 이동 [https://apps.dev.microsoft.com](https://apps.dev.microsoft.com) 또는 Microsoft 계정으로 로그인을 만듭니다.

![대화 상자에 로그인](index/_static/MicrosoftDevLogin.png)

Microsoft 계정이 없는 경우 탭  **[만드세요!](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=13&ct=1478151035&rver=6.7.6643.0&wp=SAPI_LONG&wreply=https%3a%2f%2fapps.dev.microsoft.com%2fLoginPostBack&id=293053&aadredir=1&contextid=D70D4F21246BAB50&bk=1478151036&uiflavor=web&uaid=f0c3de863a914c358b8dc01b1ff49e85&mkt=EN-US&lc=1033&lic=1)** 로그인 한 후 리디렉션됩니다 **내 응용 프로그램이** 페이지:

![Microsoft 개발자 포털에서 Microsoft Edge 열기](index/_static/MicrosoftDev.png)

* 탭 **앱 추가** 오른쪽 상단 모서리 고을 입력 하면 **응용 프로그램 이름** 및 **Contact Email**:

![새 응용 프로그램 등록 대화 상자](index/_static/MicrosoftDevAppCreate.png)

* 이 자습서에서는 선택을 취소는 **단계별 설치** 확인란 합니다.

* 탭 **만들기** 를 계속 하려면는 **등록** 페이지:

![등록 페이지](index/_static/MicrosoftDevAppReg.png)

* 탭 **추가 플랫폼** 에 **플랫폼** 선택한 섹션의 **웹** 플랫폼:

![추가 플랫폼 대화 상자](index/_static/MicrosoftDevAppPlatform.png)

* 새 **웹** 플랫폼 섹션에서와 개발 URL 입력 */signin-microsoft* 에 추가 **리디렉션 Url** 필드 (예: `https://localhost:44320/signin-microsoft`). 이 자습서의 뒷부분에 나오는 구성 된 Microsoft 인증 구성표에는 요청을 자동으로 처리할 */signin-microsoft* OAuth 흐름을 구현 하는 경로:

![웹 플랫폼 섹션](index/_static/MicrosoftRedirectUri.png)

* 탭 **URL 추가** URL 추가 되었습니다.

* 필요한 경우 다른 응용 프로그램 설정 작성 하 고 탭 **저장** 앱 구성 변경 내용을 저장 하려면 페이지 맨 아래에 있습니다.

* 다시 방문 해야 사이트를 배포 하는 경우는 **등록** 페이지 하 고 새 공개 URL을 설정 합니다.

## <a name="store-microsoft-application-id-and-password"></a>Microsoft 응용 프로그램 Id와 암호를 저장 합니다.

* 참고는 `Application Id` 에 표시 되는 **등록** 페이지.

* 탭 **새 암호를 생성할** 에 **응용 프로그램 암호** 섹션. 응용 프로그램 암호를 복사할 수 있습니다는 상자가 표시 됩니다.

![새 암호가 생성 대화 상자](index/_static/MicrosoftDevPassword.png)

Microsoft와 같은 중요 한 설정이 연결 `Application ID` 및 `Password` 사용 하 여 응용 프로그램 구성에는 [암호 관리자](../../app-secrets.md)합니다. 이 자습서에서는 이름을 토큰 `Authentication:Microsoft:ApplicationId` 및 `Authentication:Microsoft:Password`합니다.

## <a name="configure-microsoft-account-authentication"></a>Microsoft 계정 인증 구성

이 자습서에 사용 된 프로젝트 템플릿을 사용 하면 [Microsoft.AspNetCore.Authentication.MicrosoftAccount](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.MicrosoftAccount) 패키지가 이미 설치 되었습니다.

* Visual Studio 2017으로이 패키지를 설치 하려면 마우스 오른쪽 단추로 클릭 프로젝트와 선택 **NuGet 패키지 관리**합니다.
* .NET Core CLI를 설치 하려면 다음 프로젝트 디렉터리에 실행 합니다.

   `dotnet add package Microsoft.AspNetCore.Authentication.MicrosoftAccount`

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET 2.x 핵심](#tab/aspnetcore2x)

Microsoft 계정 서비스에 추가 `ConfigureServices` 메서드에서 *Startup.cs* 파일:

```csharp
services.AddAuthentication().AddMicrosoftAccount(microsoftOptions =>
{
    microsoftOptions.ClientId = Configuration["Authentication:Microsoft:ApplicationId"];
    microsoftOptions.ClientSecret = Configuration["Authentication:Microsoft:Password"];
});
```

`AddAuthentication` 만 메서드 한 번 추가 하는 경우 여러 인증 공급자입니다. 에 대 한 후속 호출 이전에 구성 된 모든 재정의의 가능성이 있는 [AuthenticationOptions](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.authenticationoptions) 속성입니다.

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

Microsoft 계정 미들웨어에서 추가 `Configure` 메서드에서 *Startup.cs* 파일:

```csharp
app.UseMicrosoftAccountAuthentication(new MicrosoftAccountOptions()
{
    ClientId = Configuration["Authentication:Microsoft:ApplicationId"],
    ClientSecret = Configuration["Authentication:Microsoft:Password"]
});
```

---

하지만 Microsoft 개발자 포털에서 사용 되는 용어 이러한 토큰 이름을 `ApplicationId` 및 `Password`,으로 노출 된 `ClientId` 및 `ClientSecret` 구성 API에 합니다.

참조는 [MicrosoftAccountOptions](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.microsoftaccountoptions) Microsoft 계정 인증에서 지 원하는 구성 옵션에 대 한 자세한 내용은 API 참조 합니다. 이 사용 하 여 사용자에 대 한 다른 정보를 요청할 수 수 있습니다.

## <a name="sign-in-with-microsoft-account"></a>Microsoft 계정으로 로그인

응용 프로그램을 실행 하 고 클릭 **로그인**합니다. Microsoft를 사용 하 여 로그인 하는 옵션이 표시 됩니다.

![웹 페이지에 응용 프로그램 로그: 인증 되지 않은 사용자](index/_static/DoneMicrosoft.png)

Microsoft을 클릭할 때 리디렉션됩니다 Microsoft 인증에 대 한 합니다. (아직 로그인) 하는 경우 Microsoft 계정으로 로그인 한 후 정보에 액세스 하는 앱을 사용 하 라는 메시지가 표시 됩니다.

![Microsoft 인증 대화 상자](index/_static/MicrosoftLogin.png)

탭 **예** 및 전자 메일을 설정할 수 있는 웹 사이트로 다시 리디렉션됩니다.

이제 Microsoft 자격 증명을 사용 하 여 로그인:

![웹 응용 프로그램: 인증 된 사용자](index/_static/Done.png)

## <a name="troubleshooting"></a>문제 해결

* Microsoft 계정 공급자 오류 페이지에 로그인 할 사용자를 리디렉션하를 하는 경우 오류 제목 및 설명 쿼리 문자열 매개 변수 바로 다음에 유의 `#` (hashtag) Uri에 있습니다.

  가장 일반적인 원인은 Uri의 일치 하지 않는 응용 프로그램 오류 메시지가 Microsoft 인증 문제가 있는 것 처럼 보이지만는 **리디렉션 Uri** 에 대해 지정 된 된 **웹** 플랫폼 .
* **ASP.NET Core 2.x만:** 경우 Identity를 호출 하 여 구성 되지 않은 `services.AddIdentity` 에 `ConfigureServices`, 인증을 시도 하면 *ArgumentException: 'SignInScheme' 옵션을 제공 해야*합니다. 이 자습서에 사용 된 프로젝트 템플릿을 확인이 수행 되도록 합니다.
* 사이트 데이터베이스 초기 마이그레이션을 적용 하 여 생성 되지 않은 경우 발생 합니다 *요청을 처리 하는 동안 데이터베이스 작업이 실패 했습니다* 오류입니다. 탭 **적용 마이그레이션** 는 데이터베이스를 만들고 오류 지 나 새로 고침 합니다.

## <a name="next-steps"></a>다음 단계

* 이 문서를 Microsoft와 인증 방법에 대해 살펴보았습니다. 이와 비슷한 방식에 제시 된 다른 공급자를 사용 하 여 인증을 따를 수 있습니다는 [이전 페이지](index.md)합니다.

* 새 만들어야 Azure 웹 앱에 웹 사이트를 게시 한 `Password` Microsoft 개발자 포털에서 합니다.

* 설정의 `Authentication:Microsoft:ApplicationId` 및 `Authentication:Microsoft:Password` Azure 포털에서 응용 프로그램 설정으로 합니다. 구성 시스템 환경 변수에서 키를 읽을 수 설정 됩니다.
