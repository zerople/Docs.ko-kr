---
title: "ASP.NET Core에서 Facebook 외부 로그인 설정"
author: rick-anderson
description: "ASP.NET Core에서 Facebook 외부 로그인 설정"
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 8/1/2017
ms.topic: article
ms.assetid: 8c65179b-688c-4af1-8f5e-1862920cda95
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/authentication/facebook-logins
ms.openlocfilehash: da019ad3fd6cefa23b8331c98cc36e50ac9c1105
ms.sourcegitcommit: 9cdbfd0d670d70b9c354216aabee260c52dad5ee
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/12/2017
---
# <a name="configuring-facebook-authentication"></a>Facebook 인증 구성

<a name=security-authentication-facebook-logins></a>

여 [Valeriy Novytskyy](https://github.com/01binary) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)

이 자습서에서 만든 샘플 ASP.NET 코어 2.0 프로젝트를 사용 하 여 들이 Facebook 계정으로 로그인 할 사용자를 활성화 하는 방법을 보여 줍니다.는 [이전 페이지](index.md)합니다. Facebook 응용 프로그램 ID에 따라 만들어서 시작는 [공식 단계](https://www.facebook.com/unsupportedbrowser)합니다.

## <a name="create-the-app-in-facebook"></a>Facebook에서 앱을 만들

*  로 이동 된 [개발자를 위한 Facebook](https://www.facebook.com/unsupportedbrowser) 페이지 하 고 로그인 합니다. Facebook 계정 없는 경우 사용 하 여는 **Facebook에 등록** 새로 만들려면 로그인 페이지에 링크 합니다.

* 탭의 **앱 만들기** 는 새 응용 프로그램 ID를 만들려면 오른쪽 위 모퉁이의 단추

   ![Facebook 개발자 포털에 대 한 Microsoft Edge에서 열기](index/_static/FBMyApps.png)

* 양식을 작성 하 고 탭는 **응용 프로그램 ID 만들기** 단추입니다.

   ![새 앱 ID 양식 만들기](index/_static/FBNewAppId.png)

* 구현과 **제품 선택** 프롬프트를 클릭 하 여 **Set Up** 에 **Facebook 로그인** 카드입니다.

   ![제품 설치 페이지](index/_static/FBProductSetup.png)

* **퀵 스타트** 마법사가 시작 됩니다 **플랫폼 선택** 의 첫 번째 페이지입니다. 마법사를 클릭 하 여 지금은 무시는 **설정을** 왼쪽 메뉴에서 링크:

   ![Skip 빠른 시작](index/_static/FBSkipQuickStart.png)

* 표시 되는 **클라이언트 OAuth 설정** 페이지:

![클라이언트 OAuth 설정 페이지](index/_static/FBOAuthSetup.png)

* 개발 URI를 입력으로 */signin-facebook* 에 추가 **유효한 OAuth 리디렉션 Uri** 필드 (예: `https://localhost:44320/signin-facebook`). 이 자습서의 뒷부분에 나오는 구성 된 Facebook 인증에는 요청을 자동으로 처리할 */signin-facebook* OAuth 흐름을 구현 하는 경로입니다.

* 클릭 **ब ा ळ**합니다.

* 클릭는 **대시보드** 왼쪽된 탐색 창에서 링크 합니다. 

    이 페이지에서 적어 프로그램 `App ID` 하였고 `App Secret`합니다. 다음 섹션에서 ASP.NET Core 응용 프로그램에 둘 다를 추가 합니다.

   ![Facebook 개발자 대시보드](index/_static/FBDashboard.png)

* 다시 방문 해야 하는 사이트를 배포 하는 경우는 **Facebook 로그인** 페이지를 설치 하 고 새 공용 URI를 등록 합니다.

## <a name="store-facebook-app-id-and-app-secret"></a>Facebook 응용 프로그램 ID 및 응용 프로그램 암호 저장

Facebook와 같은 중요 한 설정이 연결 `App ID` 및 `App Secret` 사용 하 여 응용 프로그램 구성에는 [암호 관리자](xref:security/app-secrets)합니다. 이 자습서에서는 이름을 토큰 `Authentication:Facebook:AppId` 및 `Authentication:Facebook:AppSecret`합니다.

## <a name="configure-facebook-authentication"></a>Facebook 인증 구성

이 자습서에 사용 된 프로젝트 템플릿을 사용 하면 [Microsoft.AspNetCore.Authentication.Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) 패키지가 이미 설치 되었습니다.

* Visual Studio 2017으로이 패키지를 설치 하려면 마우스 오른쪽 단추로 클릭 프로젝트와 선택 **NuGet 패키지 관리**합니다.
* .NET Core CLI를 설치 하려면 다음 프로젝트 디렉터리에 실행 합니다.

   `dotnet add package Microsoft.AspNetCore.Authentication.Facebook`

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

Facebook 서비스에 추가 된 `ConfigureServices` 에서 메서드는 *Startup.cs* 파일:

```csharp
services.AddAuthentication().AddFacebook(facebookOptions =>
{
    facebookOptions.AppId = Configuration["Authentication:Facebook:AppId"];
    facebookOptions.AppSecret = Configuration["Authentication:Facebook:AppSecret"];
});
```

`AddAuthentication` 만 메서드 한 번 추가 하는 경우 여러 인증 공급자입니다. 에 대 한 후속 호출 이전에 구성 된 모든 재정의의 가능성이 있는 [AuthenticationOptions](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.authenticationoptions) 속성입니다.

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

Facebook 미들웨어에서 추가 된 `Configure` 에서 메서드 *Startup.cs* 파일:

```csharp
app.UseFacebookAuthentication(new FacebookOptions()
{
    AppId = Configuration["Authentication:Facebook:AppId"],
    AppSecret = Configuration["Authentication:Facebook:AppSecret"]
});
```

---

참조는 [FacebookOptions](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.facebookoptions) Facebook 인증에서 지 원하는 구성 옵션에 대 한 자세한 내용은 API 참조 합니다. 구성 옵션을 사용할 수 있습니다.

* 사용자에 대 한 다른 정보를 요청 합니다.
* 로그인 환경을 사용자 지정 하는 쿼리 문자열 인수를 추가 합니다.

## <a name="sign-in-with-facebook"></a>Facebook으로 로그인

응용 프로그램을 실행 하 고 클릭 **로그인**합니다. Facebook으로 로그인 하는 옵션이 표시 됩니다.

![웹 응용 프로그램: 인증 되지 않은 사용자](index/_static/DoneFacebook.png)

클릭 **Facebook**, 인증에 대 한 Facebook으로 리디렉션됩니다.

![Facebook 인증 페이지](index/_static/FBLogin.png)

기본적으로 공개 프로필 및 전자 메일 주소를 요청 하는 Facebook 인증:

![Facebook 인증 페이지](index/_static/FBLoginDone.png)

Facebook 사용자의 자격 증명을 입력 하면 사용자의 전자 메일을 설정할 수 있는 사이트로 다시 리디렉션됩니다.

이제 Facebook 자격 증명을 사용 하 여 로그인:

![웹 응용 프로그램: 인증 된 사용자](index/_static/Done.png)

## <a name="troubleshooting"></a>문제 해결

* **ASP.NET Core 2.x만:** 경우 Identity를 호출 하 여 구성 되지 않은 `services.AddIdentity` 에 `ConfigureServices`, 인증을 시도 하면 *ArgumentException: 'SignInScheme' 옵션을 제공 해야*합니다. 이 자습서에 사용 된 프로젝트 템플릿을 확인이 수행 되도록 합니다.
* 사이트 데이터베이스 초기 마이그레이션을 적용 하 여 생성 되지 않은 경우 메시지가 *요청을 처리 하는 동안 데이터베이스 작업이 실패 했습니다* 오류입니다. 탭 **적용 마이그레이션** 는 데이터베이스를 만들고 오류 지 나 새로 고침 합니다.

## <a name="next-steps"></a>다음 단계

* 이 문서를 Facebook 인증 방법에 대해 살펴보았습니다. 이와 비슷한 방식에 제시 된 다른 공급자를 사용 하 여 인증을 따를 수 있습니다는 [이전 페이지](index.md)합니다.

* 다시 설정 해야 Azure 웹 앱에 웹 사이트를 게시 한 후의 `AppSecret` Facebook 개발자 포털에 있습니다.

* 설정의 `Authentication:Facebook:AppId` 및 `Authentication:Facebook:AppSecret` Azure 포털에서 응용 프로그램 설정으로 합니다. 구성 시스템 환경 변수에서 키를 읽을 수 설정 됩니다.
