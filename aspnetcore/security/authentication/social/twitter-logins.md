---
title: "외부 로그인 설정 twitter | Microsoft 문서"
author: rick-anderson
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 11/1/2016
ms.topic: article
ms.assetid: E5931607-31C0-4B20-B416-85E3550F5EA8
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/authentication/twitter-logins
translationtype: Machine Translation
ms.sourcegitcommit: 010b730d2716f9f536fef889bc2f767afb648ef4
ms.openlocfilehash: 50e02c1ac2618ffe2e69bed18879b6bc6faf0d2a
ms.lasthandoff: 03/23/2017

---
# <a name="configuring-twitter-authentication"></a>Twitter 인증 구성

<a name=security-authentication-twitter-logins></a>

여 [Rick Anderson](https://twitter.com/RickAndMSFT), [Pranav Rastogi](https://github.com/rustd), 및 [Valeriy Novytskyy](https://github.com/01binary)

이 자습서를 사용자가 사용 하도록 설정 하는 방법을 보여 줍니다. [Twitter 계정으로 로그인](https://dev.twitter.com/web/sign-in/desktop-browser) ASP.NET 핵심 프로젝트에서 만든 샘플을 사용 하는 [이전 페이지](index.md)합니다.

## <a name="creating-the-app-in-twitter"></a>Twitter에서 응용 프로그램 만들기

* 이동 [https://apps.twitter.com/](https://apps.twitter.com/) 에 로그인 합니다. Twitter 계정이 없는 경우 사용 하 여는 **[지금 등록](https://twitter.com/signup)** 링크를 만듭니다. 로그인은 **응용 프로그램 관리** 페이지가 표시 됩니다.

![Microsoft Edge에 열려 있는 twitter 응용 프로그램 관리](index/_static/TwitterAppManage.png)

* 누르기 **Create New App** 하 고 응용 프로그램을 채울 **이름**:

![응용 프로그램 페이지 만들기](index/_static/TwitterCreate.png)

* 와 현재 사이트 URL을 입력 */signin-twitter* 에 추가 된 **콜백 URL** 필드입니다. 예를 들어, `https://localhost:44320/signin-twitter`을 입력합니다.
  
  > [!NOTE]
  > 사이트를 배포할 때 새 공용 url을 등록 해야 합니다.

  > [!NOTE]
  > 구성할 필요가 없습니다 **/signin-twitter** 으로 응용 프로그램의 경로입니다. Twitter 미들웨어는 자동으로이 경로에 대 한 요청을 중단 하 고 OAuth 흐름을 구현 하도록 처리 합니다.

* 누르기 **Twitter 응용 프로그램을 만드는**합니다. 새 응용 프로그램 세부 정보가 표시 됩니다.

![응용 프로그램 페이지에서 세부 정보 탭](index/_static/TwitterAppDetails.png)

## <a name="storing-twitter-consumerkey-and-consumersecret"></a>Twitter ConsumerKey 및 ConsumerSecret 저장

Twitter와 같은 중요 한 설정은 연결 `ConsumerKey` 및 `ConsumerSecret` 를 사용 하 여 응용 프로그램 구성에는 [암호 관리자 도구](../../app-secrets.md) 저장 하는 대신 해당 구성 파일에서을 직접에 설명 된 대로 [소셜 로그인 개요 페이지](index.md)합니다.

* 전환의 **Keys and Access Tokens** 탭 합니다. 참고는 `Consumer Key` 및 `Consumer Secret`:

![Keys and Access Tokens 탭](index/_static/TwitterKeys.png)

* 프로젝트 작업 Twitter 비밀 정보를 저장할 디렉터리에서에서 다음 명령을 실행 합니다.

  <!-- literal_block {"ids": [], "xml:space": "preserve"} -->

  ```
  dotnet user-secrets set Authentication:Twitter:ConsumerKey <consumer-key>
  dotnet user-secrets set Authentication:Twitter:ConsumerSecret <consumer-secret>
     ```

다음 코드에서 저장 된 구성 값을 읽고는 [암호 관리자](../../app-secrets.md#security-app-secrets):

[!code-csharp[주](../../../common/samples/WebApplication1/Startup.cs?highlight=11&range=20-36)]

## <a name="enable-twitter-middleware"></a>Twitter 미들웨어를 사용 하도록 설정

> [!NOTE]
> NuGet을 사용 하 여 설치 하는 [Microsoft.AspNetCore.Authentication.Twitter](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Twitter) 아직 설치 되지 않은 경우 패키지 합니다. 프로젝트 디렉터리에서 다음 명령을 실행 또는 합니다.
>
> `dotnet add package Microsoft.AspNetCore.Authentication.Twitter`

Twitter 미들웨어에 추가 `Configure` 메서드에서 `Startup.cs`:

```csharp
app.UseTwitterAuthentication(new TwitterOptions()
{
    ConsumerKey = Configuration["Authentication:Twitter:ConsumerKey"],
    ConsumerSecret = Configuration["Authentication:Twitter:ConsumerSecret"]
});
```

## <a name="sign-in-with-twitter"></a>Sign in with Twitter

응용 프로그램을 실행 하 고 클릭 **로그인**합니다. Twitter를 사용 하 여 로그인 하는 옵션이 나타납니다.

![웹 응용 프로그램: 인증 되지 않은 사용자](index/_static/DoneTwitter.png)

클릭 하면 **Twitter** 인증에 Twitter를 리디렉션합니다.

![Twitter 인증 페이지](index/_static/TwitterLogin.png)

Twitter 자격 증명을 입력 한 후 사용자의 전자 메일을 설정할 수 있는 웹 사이트로 다시 리디렉션됩니다.

이제 Twitter 자격 증명을 사용 하 여 로그인 됩니다.

![웹 응용 프로그램: 사용자 인증](index/_static/Done.png)

## <a name="next-steps"></a>다음 단계

* 이 문서에 살펴보았습니다 방법을 Twitter로 인증할 수 있습니다. 에 나열 된 다른 공급자를 사용 하 여 인증 하는 비슷한 접근 방법을 따를 수는 [이전 페이지](index.md)합니다.

* Azure 웹 앱에 웹 사이트를 게시 하면을 다시 설정 해야는 `ConsumerSecret` Twitter 개발자 포털에서.

* 설정의 `Authentication:Twitter:ConsumerKey` 및 `Authentication:Twitter:ConsumerSecret` Azure 포털에서 응용 프로그램 설정으로 합니다. 구성 시스템 환경 변수에서 키를 읽을 수 설정 됩니다.

