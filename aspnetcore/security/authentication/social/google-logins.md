---
title: "ASP.NET Core에서 Google 외부 로그인 설정"
author: rick-anderson
description: "ASP.NET Core에서 Google 외부 로그인 설정"
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 08/02/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/authentication/google-logins
ms.openlocfilehash: 8723a74250ff1b0a63139057bfc17fdd31dd169e
ms.sourcegitcommit: 6e83c55eb0450a3073ef2b95fa5f5bcb20dbbf89
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/28/2017
---
# <a name="configuring-google-authentication-in-aspnet-core"></a>ASP.NET Core에서 Google 인증 구성

<a name=security-authentication-google-logins></a>

작성자: [Valeriy Novytskyy](https://github.com/01binary) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)

이 자습서에서 만든 샘플 ASP.NET 코어 2.0 프로젝트를 사용 하 여 해당 Google + 계정으로 로그인 하 여 사용자가 사용할 수 있도록 하는 방법을 보여 줍니다.는 [이전 페이지](index.md)합니다. 수행 하 여 시작는 [공식 단계](https://developers.google.com/identity/sign-in/web/devconsole-project) Google API 콘솔에서 새 응용 프로그램을 만들려고 합니다.

## <a name="create-the-app-in-google-api-console"></a>Google API 콘솔에서 응용 프로그램 만들기

* 로 이동 [https://console.developers.google.com/projectselector/apis/library](https://console.developers.google.com/projectselector/apis/library) 에 로그인 합니다. Google 계정 없는 경우 사용 하 여 **더 많은 옵션** > **[계정 만들기](https://accounts.google.com/SignUpWithoutGmail?service=cloudconsole&continue=https%3A%2F%2Fconsole.developers.google.com%2Fprojectselector%2Fapis%2Flibrary&ltmpl=api)**  새로 만들려면 링크:

![Google API 콘솔](index/_static/GoogleConsoleLogin.png)

* 리디렉션됩니다 **API 관리자 라이브러리** 페이지:

![API 관리자 라이브러리 페이지](index/_static/GoogleConsoleSwitchboard.png)

* 탭 **만들기** 입력 프로그램 **프로젝트 이름을**:

![새 프로젝트 대화 상자](index/_static/GoogleConsoleNewProj.png)

* 대화 상자를 수락한 후 새 응용 프로그램에 대 한 기능을 선택할 수 있도록 라이브러리 페이지에 다시 리디렉션됩니다. 찾을 **Google + API** 목록과 API 기능을 추가 하려면 해당 링크를 클릭 합니다.

![API 관리자 라이브러리 페이지](index/_static/GoogleConsoleChooseApi.png)

* 새로 추가 된 API에 대 한 페이지가 표시 됩니다. 탭 **사용** 기능에서 응용 프로그램에 Google + 기호를 추가 하려면:

![API 관리자 Google + API 페이지](index/_static/GoogleConsoleEnableApi.png)

* API를 사용 하도록 설정한 후 탭 **자격 증명을 만들어** 비밀 정보를 구성 하려면:

![API 관리자 Google + API 페이지](index/_static/GoogleConsoleGoCredentials.png)

* 다음 중 하나를 선택합니다.
   * **Google + API**
   * **웹 서버 (예:: node.js, Tomcat)**, 및
   * **사용자 데이터**:

![API 관리자 자격 증명 페이지: 어떤 종류의 자격 증명에 대해 알아봅니다 패널 필요](index/_static/GoogleConsoleChooseCred.png)

* 탭 **하는 자격 증명 필요 합니까?** 를 응용 프로그램 구성의 두 번째 단계를 따르세요 **OAuth 2.0 클라이언트 ID 만들기**:

![API 관리자 자격 증명 페이지: OAuth 2.0 클라이언트 ID 만들기](index/_static/GoogleConsoleCreateClient.png)

* 한 기능 (로그인), 동일한를 입력할 수 있는 Google + 프로젝트 만들기 때문에 **이름** 에서는 프로젝트에 대해 사용 되는 OAuth 2.0 클라이언트 id입니다.

* 개발 URI를 입력으로 */signin-google* 에 추가 **권한이 부여 된 리디렉션 URIs** 필드 (예: `https://localhost:44320/signin-google`). 이 자습서의 뒷부분에 나오는 구성 된 Google 인증에는 요청을 자동으로 처리할 */signin-google* OAuth 흐름을 구현 하는 경로입니다.

* Tab 키를 추가 하는 **권한이 부여 된 리디렉션 URIs** 항목입니다.

* 탭 **클라이언트 ID 만들기**를 세 번째 단계를 따르세요 **OAuth 2.0 동의 화면 설정**:

![API 관리자 자격 증명 페이지: OAuth 2.0 동의 화면 설정](index/_static/GoogleConsoleAddCred.png)

* 입력에 공용 **전자 메일 주소** 및 **제품 이름** Google + 하 라는 메시지에 로그인 하는 경우 앱에 대 한 표시 합니다. 추가 옵션에서 사용할 수 있는 **많은 사용자 지정 옵션**합니다.

* 탭 **계속** 마지막 단계를 진행 하려면 **자격 증명을 다운로드**:

![API 관리자 자격 증명 페이지: 자격 증명 다운로드](index/_static/GoogleConsoleFinish.png)

* 탭 **다운로드** 응용 프로그램의 비밀으로 JSON 파일을 저장 하 고 **수행** 새 앱 만들기를 완료 합니다.

* 다시 방문 해야 사이트를 배포 하는 경우는 **Google 콘솔** 새 공용 url을 등록 합니다.

## <a name="store-google-clientid-and-clientsecret"></a>저장소 Google ClientID 및 ClientSecret

Google와 같은 중요 한 설정이 연결 `Client ID` 및 `Client Secret` 사용 하 여 응용 프로그램 구성에는 [암호 관리자](../../app-secrets.md)합니다. 이 자습서에서는 이름을 토큰 `Authentication:Google:ClientId` 및 `Authentication:Google:ClientSecret`합니다.

이러한 토큰에 대 한 값에서 이전 단계에서 다운로드 한 JSON 파일에서 찾을 수 있습니다 `web.client_id` 및 `web.client_secret`합니다.

## <a name="configure-google-authentication"></a>Google 인증 구성

이 자습서에 사용 된 프로젝트 템플릿을 사용 하면 [Microsoft.AspNetCore.Authentication.Google](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Google) 패키지를 설치 합니다.

 * Visual Studio 2017으로이 패키지를 설치 하려면 마우스 오른쪽 단추로 클릭 프로젝트와 선택 **NuGet 패키지 관리**합니다.
 * .NET Core CLI를 설치 하려면 다음 프로젝트 디렉터리에 실행 합니다.

   `dotnet add package Microsoft.AspNetCore.Authentication.Google`

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

Google 서비스에 추가 `ConfigureServices` 메서드에서 *Startup.cs* 파일:

```csharp
services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

services.AddAuthentication().AddGoogle(googleOptions =>
{
    googleOptions.ClientId = Configuration["Authentication:Google:ClientId"];
    googleOptions.ClientSecret = Configuration["Authentication:Google:ClientSecret"];
});
```

[!INCLUDE[default settings configuration](includes/default-settings.md)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

Google 미들웨어에서 추가 된 `Configure` 에서 메서드 *Startup.cs* 파일:

```csharp
app.UseGoogleAuthentication(new GoogleOptions()
{
    ClientId = Configuration["Authentication:Google:ClientId"],
    ClientSecret = Configuration["Authentication:Google:ClientSecret"]
});
```

---

참조는 [GoogleOptions](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.googleoptions) Google 인증에서 지 원하는 구성 옵션에 대 한 자세한 내용은 API 참조 합니다. 이 사용 하 여 사용자에 대 한 다른 정보를 요청할 수 수 있습니다.

## <a name="sign-in-with-google"></a>Google을 사용 하 여 로그인

응용 프로그램을 실행 하 고 클릭 **로그인**합니다. Google을 사용 하 여 로그인 하는 옵션이 표시 됩니다.

![Microsoft Edge에서 실행 되는 웹 응용 프로그램: 인증 되지 않은 사용자](index/_static/DoneGoogle.png)

Google을 클릭할 때 리디렉션됩니다 Google 인증:

![Google 인증 대화 상자](index/_static/GoogleLogin.png)

Google 자격 증명을 입력 한 후 다음 리디렉션됩니다 전자 메일을 설정할 수 있는 웹 사이트에 다시.

이제 Google 자격 증명을 사용 하 여 로그인:

![Microsoft Edge에서 실행 되는 웹 응용 프로그램: 인증 된 사용자](index/_static/Done.png)

## <a name="troubleshooting"></a>문제 해결

* 표시 되 면 한 `403 (Forbidden)` 개발 모드 (또는 동일한 오류가 발생 하 여 디버거를 중단)에서 실행 되 고 있는지 확인 하는 경우 사용자 고유의 응용 프로그램에서 오류 페이지 **Google + API** 에서 설정 되어는 **API관리자라이브러리** 나열 된 단계를 수행 하 여 [이 페이지에 이전](#create-the-app-in-google-api-console)합니다. 로그인의 작동 하지 않는 경우 오류가 나타나지 문제를 보다 쉽게 디버그 하려면 개발 모드로 전환 합니다.
* **ASP.NET Core 2.x만:** 경우 Identity를 호출 하 여 구성 되지 않은 `services.AddIdentity` 에 `ConfigureServices`, 인증을 시도 하면 *ArgumentException: 'SignInScheme' 옵션을 제공 해야*합니다. 이 자습서에 사용 된 프로젝트 템플릿을 확인이 수행 되도록 합니다.
* 사이트 데이터베이스 초기 마이그레이션을 적용 하 여 생성 되지 않은 경우 발생 합니다 *요청을 처리 하는 동안 데이터베이스 작업이 실패 했습니다* 오류입니다. 탭 **적용 마이그레이션** 는 데이터베이스를 만들고 오류 지 나 새로 고침 합니다.

## <a name="next-steps"></a>다음 단계

* 이 문서 google 인증 방법에 대해 살펴보았습니다. 이와 비슷한 방식에 제시 된 다른 공급자를 사용 하 여 인증을 따를 수 있습니다는 [이전 페이지](index.md)합니다.

* 다시 설정 해야 Azure 웹 앱에 웹 사이트를 게시 한 후의 `ClientSecret` Google API 콘솔에서 합니다.

* 설정의 `Authentication:Google:ClientId` 및 `Authentication:Google:ClientSecret` Azure 포털에서 응용 프로그램 설정으로 합니다. 구성 시스템 환경 변수에서 키를 읽을 수 설정 됩니다.
