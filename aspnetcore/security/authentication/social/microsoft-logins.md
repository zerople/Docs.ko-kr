---
title: "Microsoft 계정 외부 로그인 설정 | Microsoft 문서"
author: rick-anderson
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 11/2/2016
ms.topic: article
ms.assetid: 66DB4B94-C78C-4005-BA03-3D982B87C268
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/authentication/microsoft-logins
translationtype: Machine Translation
ms.sourcegitcommit: 010b730d2716f9f536fef889bc2f767afb648ef4
ms.openlocfilehash: 436008c022fdd427025a6d2612ee5809052d63f7
ms.lasthandoff: 03/23/2017

---
# <a name="configuring-microsoft-account-authentication"></a>Microsoft 계정 인증 구성

<a name=security-authentication-microsoft-logins></a>

여 [Rick Anderson](https://twitter.com/RickAndMSFT), [Pranav Rastogi](https://github.com/rustd), 및 [Valeriy Novytskyy](https://github.com/01binary)

이 자습서에서는 ASP.NET 핵심 프로젝트에서 만든 샘플을 사용 하 여 자신의 Microsoft 계정으로 로그인 하 여 사용자를 활성화 하는 방법을 보여 줍니다는 [이전 페이지](index.md)합니다.

## <a name="creating-the-app-in-microsoft-developer-portal"></a>Microsoft 개발자 포털에서 응용 프로그램 만들기

* 이동 [https://apps.dev.microsoft.com](https://apps.dev.microsoft.com):

![Microsoft Edge에 열려 있는 Microsoft 개발자 포털](index/_static/MicrosoftDev.png)

* 누르기 **로그인**:

![대화 상자에 로그인](index/_static/MicrosoftDevLogin.png)

Microsoft 계정이 없는 경우 누릅니다 **[만드세요!](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=13&ct=1478151035&rver=6.7.6643.0&wp=SAPI_LONG&wreply=https%3a%2f%2fapps.dev.microsoft.com%2fLoginPostBack&id=293053&aadredir=1&contextid=D70D4F21246BAB50&bk=1478151036&uiflavor=web&uaid=f0c3de863a914c358b8dc01b1ff49e85&mkt=EN-US&lc=1033&lic=1)**. 로그인 한 후 리디렉션됩니다 **내 응용 프로그램** 페이지:

![내 응용 프로그램 대화 상자](index/_static/MicrosoftDevApps.png)

* 누르기 **응용 프로그램 추가** 오른쪽 상단 모서리 하 고 입력 하면 **응용 프로그램 이름**:

![새 응용 프로그램 등록 대화 상자](index/_static/MicrosoftDevAppCreate.png)

* **등록** 페이지가 표시 됩니다.

![등록 페이지](index/_static/MicrosoftDevAppReg.png)

* 누르기 **Add Platform** 에 **플랫폼** 섹션 선택한는 **웹** 플랫폼:

![플랫폼 대화 상자를 추가 합니다.](index/_static/MicrosoftDevAppPlatform.png)

* 새 **웹** 플랫폼 섹션에서와 현재 사이트 URL을 입력 */signin-microsoft* 에 추가 된 **리디렉션 Uri** 필드입니다. 예를 들어 `https://localhost:44320/signin-microsoft`:

![웹 플랫폼 섹션](index/_static/MicrosoftRedirectUri.png)
  
  > [!NOTE]
  > 사이트를 배포할 때 새 공용 url을 등록 해야 합니다.

  > [!NOTE]
  > 구성할 필요가 없습니다 **/signin-microsoft** 으로 응용 프로그램의 경로입니다. Microsoft 계정 미들웨어는 자동으로이 경로에 대 한 요청을 중단 하 고 OAuth 흐름을 구현 하도록 처리 합니다.

* 누르기 잊지 **Url 추가** Url 추가 되었습니다.

* 누르기 **저장** 변경 내용을 저장 합니다.

## <a name="storing-microsoft-applicationid-and-secret"></a>Microsoft 응용 프로그램 Id 및 암호를 저장합니다.

Microsoft와 같은 중요 한 설정은 연결 `ApplicationId` 및 `Secret` 를 사용 하 여 응용 프로그램 구성에는 [암호 관리자 도구](../../app-secrets.md) 저장 하는 대신 해당 구성 파일에서을 직접에 설명 된 대로 [소셜 로그인 개요 페이지](index.md)합니다.

* 참고는 `Application Id` 에 표시 되는 **등록** 페이지입니다.

* 누르기 **새 암호 생성** 에 **응용 프로그램 암호** 섹션입니다. 이 응용 프로그램 암호를 복사할 수 있는 상자를 표시 합니다.

![새 암호가 생성 대화 상자](index/_static/MicrosoftDevPassword.png)

* 작업은 Microsoft 비밀 정보를 저장할 디렉터리 프로젝트에서 다음 명령을 실행 합니다.

  <!-- literal_block {"ids": [], "xml:space": "preserve"} -->

  ```
  dotnet user-secrets set Authentication:Microsoft:ClientId <client-id>
  dotnet user-secrets set Authentication:Microsoft:ClientSecret <client-secret>
     ```

다음 코드에서 저장 된 구성 값을 읽고는 [암호 관리자](../../app-secrets.md#security-app-secrets):

[!code-csharp[주](../../../common/samples/WebApplication1/Startup.cs?highlight=11&range=20-36)]

## <a name="enable-microsoft-account-middleware"></a>Microsoft 계정 미들웨어를 사용 하도록 설정

> [!NOTE]
> NuGet을 사용 하 여 설치 하는 [Microsoft.AspNetCore.Authentication.MicrosoftAccount](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.MicrosoftAccount) 아직 설치 되지 않은 경우 패키지 합니다. 프로젝트 디렉터리에서 다음 명령을 실행 또는 합니다.
>
> `dotnet add package Microsoft.AspNetCore.Authentication.MicrosoftAccount`

Microsoft 계정 미들웨어 추가 `Configure` 메서드에서 `Startup.cs`:

```csharp
app.UseMicrosoftAccountAuthentication(new MicrosoftAccountOptions()
{
    ClientId = Configuration["Authentication:Microsoft:ClientId"],
    ClientSecret = Configuration["Authentication:Microsoft:ClientSecret"]
});
```

## <a name="sign-in-with-microsoft-account"></a>Microsoft 계정으로 로그인

응용 프로그램을 실행 하 고 클릭 **로그인**합니다. Microsoft를 사용 하 여 로그인 하는 옵션이 나타납니다.

![웹 페이지에서 응용 프로그램 로그: 인증 되지 않은 사용자](index/_static/DoneMicrosoft.png)

Microsoft에서 누르면 리디렉션됩니다 Microsoft 인증을 위해:

![Microsoft 인증 대화 상자](index/_static/MicrosoftLogin.png)

Microsoft 계정 자격 증명을 입력 한 후 사용자의 전자 메일을 설정할 수 있는 웹 사이트로 다시 리디렉션됩니다.

이제 Microsoft 자격 증명을 사용 하 여 로그인 됩니다.

![웹 응용 프로그램: 사용자 인증](index/_static/Done.png)

> [!NOTE]
> Microsoft 계정 공급자 오류 페이지에 로그인 할 사용자를 리디렉션하를 하는 경우 오류 제목 및 설명 바로 다음을 참고 하십시오는 `#` (해시 태그) Uri에 있습니다. 가장 일반적인 원인은 응용 프로그램 중 하나에 일치 하지 않는 Uri는 **리디렉션 Uri** 에 대 한 지정 된는 **웹** 플랫폼입니다. 이 경우에 있는지 확인 프로토콜, 호스트 및 포트 모두 정확 합니다. 응용 프로그램을 사용 해야 `https` 프로토콜 및 리디렉션 uri로 끝나야 **/signin-microsoft** 경로 Microsoft 계정 미들웨어 하는 것을 리디렉션하는 로그인 공급자를 요청 합니다.

![Microsoft 오류 페이지: 우리 사용자 요청을 완료할 수 있습니다. Microsoft 계정 기술적인 문제가 발생 했습니다. 나중에 다시 시도 하십시오.](index/_static/MicrosoftLoginError.png)

## <a name="next-steps"></a>다음 단계

* 이 문서와 Microsoft 인증 방법에 대해 살펴보았습니다. 에 나열 된 다른 공급자를 사용 하 여 인증 하는 비슷한 접근 방법을 따를 수는 [이전 페이지](index.md)합니다.

* Azure 웹 앱에 웹 사이트를 게시 하면을 다시 설정 해야는 `Secret` Microsoft 개발자 포털에서.

* 설정의 `Authentication:Microsoft:ClientId` 및 `Authentication:Microsoft:ClientSecret` Azure 포털에서 응용 프로그램 설정으로 합니다. 구성 시스템 환경 변수에서 키를 읽을 수 설정 됩니다.

