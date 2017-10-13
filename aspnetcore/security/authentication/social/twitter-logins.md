---
title: "Twitter 외부 로그인 설정"
author: rick-anderson
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 11/01/2016
ms.topic: article
ms.assetid: E5931607-31C0-4B20-B416-85E3550F5EA8
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/authentication/twitter-logins
ms.openlocfilehash: 90c8816997546fc186e0cc562a61cc856c260f1a
ms.sourcegitcommit: 93785b6b29a4996066fba05149348f1bdf881263
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/12/2017
---
# <a name="configuring-twitter-authentication"></a>Twitter 인증 구성

작성자: [Valeriy Novytskyy](https://github.com/01binary) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)

이 자습서에서는 사용자 수 있도록 하는 방법을 보여 줍니다 [Twitter 계정으로 इ न क](https://dev.twitter.com/web/sign-in/desktop-browser) 에서 만든 샘플 ASP.NET 코어 2.0 프로젝트를 사용 하 여 [이전 페이지](index.md)합니다.

## <a name="create-the-app-in-twitter"></a>Twitter에서 앱을 만들

* 로 이동 [https://apps.twitter.com/](https://apps.twitter.com/) 에 로그인 합니다. Twitter 계정 없는 경우 사용 하 여는  **[지금 등록](https://twitter.com/signup)**  링크를 만듭니다. 에 로그인 한 후의 **응용 프로그램 관리** 페이지가 표시 됩니다.

![Microsoft Edge에서 열려 있는 twitter 응용 프로그램 관리](index/_static/TwitterAppManage.png)

* 탭 **Create New App** 응용 프로그램을 확인 하 고 **이름**, **설명** 및 공용 **웹 사이트** (이 사용 가능 임시 할 때까지 URI 등록 된 도메인 이름):

![응용 프로그램 페이지 만들기](index/_static/TwitterCreate.png)

* 개발 URI를 입력으로 */signin-twitter* 에 추가 **유효한 OAuth 리디렉션 Uri** 필드 (예: `https://localhost:44320/signin-twitter`). 이 자습서의 뒷부분에 나오는 구성 된 Twitter 인증 구성표에는 요청을 자동으로 처리할 */signin-twitter* OAuth 흐름을 구현 하는 경로입니다.

* 폼의 나머지 부분을 입력 하 고 탭 **Twitter 응용 프로그램을 만드는**합니다. 새 응용 프로그램 세부 정보가 표시 됩니다.

![응용 프로그램 페이지에서 세부 정보 탭](index/_static/TwitterAppDetails.png)

* 다시 방문 해야 사이트를 배포 하는 경우는 **응용 프로그램 관리** 페이지 하 고 새 공용 URI를 등록 합니다.

## <a name="storing-twitter-consumerkey-and-consumersecret"></a>Twitter ConsumerKey 및 ConsumerSecret 저장

Twitter와 같은 중요 한 설정이 연결 `Consumer Key` 및 `Consumer Secret` 사용 하 여 응용 프로그램 구성에는 [암호 관리자](../../app-secrets.md)합니다. 이 자습서에서는 이름을 토큰 `Authentication:Twitter:ConsumerKey` 및 `Authentication:Twitter:ConsumerSecret`합니다.

이러한 토큰을 확인할 수 있습니다는 **키와 액세스 토큰이** 새 Twitter 응용 프로그램을 만든 후 탭:

![키 및 액세스 토큰 탭](index/_static/TwitterKeys.png)

## <a name="configure-twitter-authentication"></a>Twitter 인증 구성

이 자습서에 사용 된 프로젝트 템플릿을 사용 하면 [Microsoft.AspNetCore.Authentication.Twitter](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Twitter) 패키지가 이미 설치 되었습니다.

* Visual Studio 2017으로이 패키지를 설치 하려면 마우스 오른쪽 단추로 클릭 프로젝트와 선택 **NuGet 패키지 관리**합니다.
* .NET Core CLI를 설치 하려면 다음 프로젝트 디렉터리에 실행 합니다.

   `dotnet add package Microsoft.AspNetCore.Authentication.Twitter`

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

Twitter 서비스에 추가 `ConfigureServices` 메서드에서 *Startup.cs* 파일:

```csharp
services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

services.AddAuthentication().AddTwitter(twitterOptions =>
{
    twitterOptions.ConsumerKey = Configuration["Authentication:Twitter:ConsumerKey"];
    twitterOptions.ConsumerSecret = Configuration["Authentication:Twitter:ConsumerSecret"];
});
```

[!INCLUDE[default settings configuration](includes/default-settings.md)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

Twitter 미들웨어에서 추가 된 `Configure` 에서 메서드 *Startup.cs* 파일:

```csharp
app.UseTwitterAuthentication(new TwitterOptions()
{
    ConsumerKey = Configuration["Authentication:Twitter:ConsumerKey"],
    ConsumerSecret = Configuration["Authentication:Twitter:ConsumerSecret"]
});
```

---

참조는 [TwitterOptions](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.twitteroptions) Twitter 인증에서 지 원하는 구성 옵션에 대 한 자세한 내용은 API 참조 합니다. 이 사용 하 여 사용자에 대 한 다른 정보를 요청할 수 수 있습니다.

## <a name="sign-in-with-twitter"></a>Twitter를 사용 하 여 로그인

응용 프로그램을 실행 하 고 클릭 **로그인**합니다. Twitter를 사용 하 여 로그인 하는 옵션이 표시 됩니다.

![웹 응용 프로그램: 인증 되지 않은 사용자](index/_static/DoneTwitter.png)

클릭 하면 **Twitter** 인증에 대 한 Twitter에 리디렉션합니다.

![Twitter 인증 페이지](index/_static/TwitterLogin.png)

Twitter 자격 증명을 입력 한 후 사용자의 전자 메일을 설정할 수 있는 웹 사이트로 다시 리디렉션됩니다.

이제 Twitter 자격 증명을 사용 하 여 로그인:

![웹 응용 프로그램: 인증 된 사용자](index/_static/Done.png)

## <a name="troubleshooting"></a>문제 해결

* **ASP.NET Core 2.x만:** 경우 Identity를 호출 하 여 구성 되지 않은 `services.AddIdentity` 에 `ConfigureServices`, 인증을 시도 하면 *ArgumentException: 'SignInScheme' 옵션을 제공 해야*합니다. 이 자습서에 사용 된 프로젝트 템플릿을 확인이 수행 되도록 합니다.
* 사이트 데이터베이스 초기 마이그레이션을 적용 하 여 생성 되지 않은 경우 발생 합니다 *요청을 처리 하는 동안 데이터베이스 작업이 실패 했습니다* 오류입니다. 탭 **적용 마이그레이션** 는 데이터베이스를 만들고 오류 지 나 새로 고침 합니다.

## <a name="next-steps"></a>다음 단계

* 이 문서 Twitter와 인증 방법에 대해 살펴보았습니다. 이와 비슷한 방식에 제시 된 다른 공급자를 사용 하 여 인증을 따를 수 있습니다는 [이전 페이지](index.md)합니다.

* 다시 설정 해야 Azure 웹 앱에 웹 사이트를 게시 한 후의 `ConsumerSecret` Twitter 개발자 포털에 있습니다.

* 설정의 `Authentication:Twitter:ConsumerKey` 및 `Authentication:Twitter:ConsumerSecret` Azure 포털에서 응용 프로그램 설정으로 합니다. 구성 시스템 환경 변수에서 키를 읽을 수 설정 됩니다.
