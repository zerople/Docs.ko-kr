---
title: "Facebook, Google 및 기타 외부를 사용 하 여 인증 사용 | Microsoft 문서"
author: rick-anderson
description: 
keywords: "ASP.NET Core, 인증, 사회, 인증 공급자, google, facebook, twitter, microsoft 계정"
ms.author: riande
manager: wpickett
ms.date: 11/1/2016
ms.topic: article
ms.assetid: eda7ee17-f38c-462e-8d1d-63f459901cf3
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/authentication/social/index
translationtype: Machine Translation
ms.sourcegitcommit: 010b730d2716f9f536fef889bc2f767afb648ef4
ms.openlocfilehash: 654d37831589a5631aa0406020173e746cffeb0f
ms.lasthandoff: 03/23/2017

---

# <a name="enabling-authentication-using-facebook-google-and-other-external-providers"></a>Facebook, Google 및 다른 외부 공급자를 사용 하 여 인증을 사용 하도록 설정

<a name=security-authentication-social-logins></a>

여 [Rick Anderson](https://twitter.com/RickAndMSFT), [Pranav Rastogi](https://github.com/rustd), 및 [Valeriy Novytskyy](https://github.com/01binary)

이 자습서에서는 OAuth 2.0을 사용 하 여 사용자가 로그인 할 수 있게 해 주는 ASP.NET 핵심 응용 프로그램을 구축 하는 방법을 보여 줍니다. 외부 인증 공급자에서 자격 증명으로 1.0 a /.

[Facebook](facebook-logins.md), [Twitter](twitter-logins.md), [Google](google-logins.md), 및 [Microsoft](microsoft-logins.md) 공급자는 다음 섹션에 설명 되어 있습니다. 많은 타사 패키지에는 다른 공급자를 여기에서 설명 하지을 사용할을 수 있습니다.

![Facebook, Twitter, Google plus, 및 Windows에 대 한 소셜 미디어 아이콘](index/_static/social.png)

사용자가 자신의 기존 자격 증명을 사용 하 여 로그인을 사용자에 게 편리한 하 고 다양 한 제&3; 자에 로그인 프로세스를 관리의 복잡성을 이동 합니다. 어떻게 소셜 로그인의 예는 트래픽 및 고객 변환 드라이브 수, 사례 연구 하 여 참조 [Facebook](https://developers.facebook.com/case-studies) 및 [Twitter](https://dev.twitter.com/resources/case-studies)합니다.

> [!NOTE]
> 여기에 제시 된 패키지 상당한 OAuth 인증 흐름의 복잡성을 추상화 하지만 자세히 이해 될 수 있습니다 필요한 문제를 해결할 때. 많은 리소스를 사용할 수 있습니다. 예를 들어 참조 [OAuth 2 소개](https://www.digitalocean.com/community/tutorials/an-introduction-to-oauth-2) 또는 [이해 OAuth 2](http://www.bubblecode.net/en/2016/01/22/understanding-oauth2/)합니다. 확인 하 여 몇 가지 문제를 해결할 수는 [공급자 패키지에 대 한 소스 코드를 ASP.NET 핵심](https://github.com/aspnet/Security/tree/dev/src)합니다.

## <a name="create-a-new-aspnet-core-project"></a>새 ASP.NET 핵심 프로젝트 만들기

> [!NOTE]
> 이 자습서를 사용 하려면 최신 업데이트 (**업데이트 3**) Visual Studio 2015 및 ASP.NET 핵심입니다.

* Visual Studio에서 새 프로젝트를 만듭니다 (시작 페이지에서 또는 통해 **파일 > 새로 만들기 > 프로젝트**):

![새 프로젝트 대화 상자](index/_static/new-project.png)

* 누르기 **웹 응용 프로그램** 확인 **인증** 로 설정 된 **개별 사용자 계정**:

![새 웹 응용 프로그램 대화 상자](index/_static/select-project.png)

## <a name="enable-ssl"></a>SSL 사용

일부 외부 인증 공급자를 사용 하지 않는 원본에서 들어오는 요청을 거부는 **https** 프로토콜입니다. 이 같은 주요 공급자의 추세를 반영 [Google](https://security.googleblog.com/2014/08/https-as-ranking-signal_6.html) https로 공용 API 서비스를 이동 하 고 암호화 되지 않은 끝점의 사용 중단 합니다. 이러한 추세에 따라 전체 사이트에 SSL을 사용 하는 것이 좋습니다. Visual Studio 2017 r c 1에 대 한 아래의 지침을 참조 합니다.

* **솔루션 탐색기**프로젝트를 마우스 오른쪽 단추로 클릭 하 고 선택 **속성**합니다.

* 왼쪽된 창에서 탭 **디버그**합니다.

* 확인 **SSL 사용**합니다.

* SSL URL을 복사 하 고 붙여 넣습니다는 **앱 URL**:

![디버그 탭의 응용 프로그램 속성](index/_static/ssl.png)

Visual Studio 2017 RC1:

편집 된 *properties/launchsettings.json* 파일입니다. sslPort 44300 사이의 44399를 추가 합니다.

```
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iisExpress": {
      "applicationUrl": "http://localhost:62183/",
      "sslPort": 0
    }
  },
```

* 수정 된 `services.AddMvc();` 코드에서 `Startup.cs` 에서 `ConfigureServices` 하지를 통해 들어오는 모든 요청을 거부 하도록 *https*:

```csharp
services.AddMvc(options =>
{
    options.SslPort = 44321;
    options.Filters.Add(new RequireHttpsAttribute ());
});
```

* 정적 파일은 여전히 제공 되 고 공개적으로 노출 된 경로는 액세스할 수 있도록 앱을 테스트 합니다.
   * 개발자 도구의 브라우저 콘솔에 기록 된 경고나가 아니어야 합니다.
   * 사용 된 이전 URL로 이동 하려고는 *http* 프로토콜 이제 유발 **연결이 거부** 브라우저 또는 빈 페이지는 오류.

## <a name="use-secretmanager-to-store-tokens-assigned-by-login-providers"></a>SecretManager를 사용 하 여 로그인 공급자에 의해 할당 된 토큰을 저장 하려면

이 자습서에서는 샘플 프로젝트를 만드는 데 서식 파일에 코드가 있는 `Startup.cs` 암호 저장소에서 구성 값을 읽습니다.

[!code-csharp[주](../../../common/samples/WebApplication1/Startup.cs?highlight=11&range=20-36)]

모범 사례로, 공개적으로 액세스할 수 있는 소스 제어에 체크 이후 응용 프로그램의 구성 파일에서 암호를 저장 하기 위해 좋습니다 하지 않습니다.

**SecretManager** 도구는 로컬 컴퓨터에서 사용자 프로필 폴더에 중요 한 응용 프로그램 설정을 저장 합니다. 이러한 설정은 다음 원활 하 게 설정과 병합 됩니다 다른 모든 원본에서 응용 프로그램 시작 중입니다.

> [!NOTE]
> 대부분의 로그인 공급자 할당 **응용 프로그램 Id** 및 **응용 프로그램 암호** 등록 프로세스 중입니다. 이러한 값은 효과적으로 *사용자 이름* 및 *암호* 해당 API에 액세스 하 고 사용 하 여 응용 프로그램 구성에 연결 된 "secrets" 구성에 사용 하 여 응용 프로그램 **암호 관리자** 직접 구성 파일에 저장 하는 대신 합니다.

설치는 [암호 관리자 도구](../../app-secrets.md) 아래 각 로그인 공급자에 의해 할당 되는 토큰을 저장 하는 사용할 수 있도록 합니다.

## <a name="setup-login-providers-required-by-your-application"></a>응용 프로그램에 필요한 로그인 공급자를 설치 합니다.

다음 페이지를 사용 하 여 해당 공급자를 사용 하 여 응용 프로그램을 구성 합니다.

* [Facebook](facebook-logins.md) 지침
* [Twitter](twitter-logins.md) 지침
* [Google](google-logins.md) 지침
* [Microsoft](microsoft-logins.md) 지침
* [다른 공급자](other-logins.md) 지침

> [!NOTE]
> 호출 `app.UseIdentity` (에서 `Configure`) 전에 다른 외부 공급자입니다.

## <a name="optionally-set-password"></a>필요에 따라 암호 설정

외부 로그인 공급자를 등록할 때에 등록 된 앱 암호를 설치할 필요가 없습니다. 이를 작성 하 고 사이트에 대 한 암호를 기억할 수를 줄여줍니다 하지만 있기 있습니다 외부 로그인 공급자에 따라 다릅니다. 외부 로그인 공급자를 사용할 수 없는 경우에 웹 사이트에 로그인 할 수 없습니다.

에 암호를 만들고 외부 공급자가 포함 된 로그인 프로세스 동안 설정한 전자 메일을 사용 하 여 로그인 합니다.

* 탭은 **Hello <email alias>** 이동 하려면 오른쪽 위에 있는 링크는 **관리** 보기.

![웹 응용 프로그램 관리 보기](index/_static/pass1a.png)

* 누르기 **만들기**

![암호 페이지 설정](index/_static/pass2a.png)

* 올바른 암호를 설정 하 고이 사용자의 전자 메일을 사용 하 여 로그인을 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* 이 문서는 외부 인증을 도입 하 고 ASP.NET 핵심 응용 프로그램에 외부 로그인을 추가 하는 데 필요한 필수 구성 요소를 설명 합니다.

* 응용 프로그램에 필요한 공급자에 대 한 로그인을 구성 하는 참조 공급자별 페이지입니다.

