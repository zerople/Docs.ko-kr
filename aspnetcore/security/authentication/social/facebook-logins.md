---
title: "Facebook 외부 로그인 설정 | Microsoft 문서"
author: rick-anderson
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 11/1/2016
ms.topic: article
ms.assetid: 8c65179b-688c-4af1-8f5e-1862920cda95
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/authentication/facebook-logins
translationtype: Machine Translation
ms.sourcegitcommit: 010b730d2716f9f536fef889bc2f767afb648ef4
ms.openlocfilehash: e256adbeaf66f28249d8d12817b8069f3d54c389
ms.lasthandoff: 03/23/2017

---
# <a name="configuring-facebook-authentication"></a>Facebook 인증 구성

<a name=security-authentication-facebook-logins></a>

여 [Rick Anderson](https://twitter.com/RickAndMSFT), [Pranav Rastogi](https://github.com/rustd), 및 [Valeriy Novytskyy](https://github.com/01binary)

이 자습서에서는 ASP.NET 핵심 프로젝트에서 만든 샘플을 사용 하 여 Facebook 계정으로 로그인 하 여 사용자를 활성화 하는 방법을 보여 줍니다는 [이전 페이지](index.md)합니다. Facebook 응용 프로그램 ID에 따라 만들어서 시작는 [공식 단계](https://developers.facebook.com/docs/apps/register)합니다.

## <a name="creating-the-app-in-facebook"></a>Facebook에서 앱 만들기

*  탐색 하는 [Facebook 개발자를 위한](https://developers.facebook.com/apps) 페이지 하 고 로그인 합니다. 사용 하 여 Facebook 계정이 없는 경우는 **Facebook에 등록** 새로 만들려면 로그인 페이지에 링크 합니다.

* 탭의 **새 앱 추가 +** ID를 만듭니다. 새 응용 프로그램을 오른쪽 위 모퉁이의 단추 (Facebook과 첫 번째 앱 인 경우 단추의 텍스트 됩니다 **새 앱 만들기**.)

![Facebook 개발자 포털에 대 한 Microsoft Edge에서 열](index/_static/FBMyApps.png)

* 양식을 작성 하 고 탭은 **Create App ID** 단추입니다.

![앱 ID를 새 양식 만들기](index/_static/FBNewAppId.png)

* **제품 설치** 새 앱에 대 한 기능을 선택할 수 있도록 페이지가 표시 됩니다. 클릭 **시작** 에 **Facebook 로그인**합니다.

![제품 설치 페이지](index/_static/FBProductSetup.png)

* 빠른 시작 프로세스에서 시작 하는 다음으로 **플랫폼 선택** 화면입니다. 이 자습서에서는 다루지 클라이언트 쪽 로그인 통합을 설정 하는 데 도움이 됩니다. 

    이 무시 하려면 클릭는 **설정을** 왼쪽 메뉴에서 링크 합니다.


* 표시 되는 **클라이언트 설정을** 페이지에서 일부 기본값 이미 설정 합니다.

![클라이언트 OAuth 설정 페이지](index/_static/FBOAuthSetup.png)

* 사용 하 여 기본 URI를 입력 */signin-facebook* 에 추가 **유효한 OAuth 리디렉션 Uri** 필드 (예: `https://localhost:44320/signin-facebook`). 
* 클릭 **변경 내용을 저장**합니다.
  
  > [!NOTE]
  > 사이트를 배포할 때 새 공용 url을 등록 해야 합니다.

  > [!NOTE]
  > 구성할 필요가 없습니다 **/signin-facebook** 으로 응용 프로그램의 경로입니다. Facebook 미들웨어는 자동으로이 경로에 대 한 요청을 중단 하 고 OAuth 흐름을 구현 하도록 처리 합니다.

* 클릭 하 고 **대시보드** 왼쪽된 탐색 영역에서 링크 합니다. 
    
    메모 해야이 페이지에서는 프로그램 `App ID` 및 `App Secret`합니다. 이 자습서의 뒷부분에서 ASP.NET 핵심 응용 프로그램에 모두 추가 합니다.

## <a name="storing-facebook-app-id-and-appsecret"></a>Facebook 응용 프로그램 ID 및 AppSecret 저장

예: Facebook 중요 한 설정은 연결 `App ID` 및 `App Secret` 를 사용 하 여 응용 프로그램 구성에는 [암호 관리자 도구](../../app-secrets.md) 저장 하는 대신 해당 구성 파일에서을 직접에 설명 된 대로 [소셜 로그인 개요 페이지](index.md)합니다. 프로젝트 작업 디렉터리에서 다음 명령을 실행 합니다.

* Facebook AppId를 설정 합니다.

  <!-- literal_block {"ids": [], "xml:space": "preserve"} -->

  ```
  dotnet user-secrets set Authentication:Facebook:AppId <app-Id>
     ```

* Facebook AppSecret 설정

  <!-- literal_block {"ids": [], "xml:space": "preserve"} -->

  ```
  dotnet user-secrets set Authentication:Facebook:AppSecret <app-secret>
     ```

다음 코드에서 저장 된 구성 값을 읽고는 [암호 관리자](../../app-secrets.md#security-app-secrets):

[!code-csharp[주](../../../common/samples/WebApplication1/Startup.cs?highlight=11&range=20-36)]

## <a name="enable-facebook-middleware"></a>Facebook 미들웨어를 사용 하도록 설정

> [!NOTE]
> NuGet을 사용 하 여 설치 해야 합니다는 [Microsoft.AspNetCore.Authentication.Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) 아직 설치 되지 않은 경우 패키지 합니다. 프로젝트 디렉터리에서 다음 명령을 실행 또는 합니다.
>
> `dotnet add package Microsoft.AspNetCore.Authentication.Facebook`

추가에서 Facebook 미들웨어는 `Configure` 메서드에서 `Startup.cs`:

```csharp
app.UseFacebookAuthentication(new FacebookOptions()
{
    AppId = Configuration["Authentication:Facebook:AppId"],
    AppSecret = Configuration["Authentication:Facebook:AppSecret"]
});
```

## <a name="sign-in-with-facebook"></a>Facebook을 사용 하 여 로그인

응용 프로그램을 실행 하 고 클릭 **로그인**합니다. Facebook을 사용 하 여 로그인 하는 옵션이 표시 됩니다.

![웹 응용 프로그램: 인증 되지 않은 사용자](index/_static/DoneFacebook.png)

Facebook을 클릭할 때 리디렉션됩니다 facebook 인증을 위해.

![Facebook 인증 페이지](index/_static/FBLogin2a.png)

Facebook 자격 증명을 입력 하면 다음 전자 메일을 설정할 수 있는 웹 사이트로 다시 리디렉션됩니다.

이제 Facebook 자격 증명을 사용 하 여 로그인 됩니다.

![웹 응용 프로그램: 사용자 인증](index/_static/Done.png)

## <a name="next-steps"></a>다음 단계

* 이 문서 facebook 인증 방법에 대해 살펴보았습니다. 에 나열 된 다른 공급자를 사용 하 여 인증 하는 비슷한 접근 방법을 따를 수는 [이전 페이지](index.md)합니다.

* Azure 웹 앱에 웹 사이트를 게시 하면을 다시 설정 해야는 `AppSecret` Facebook 개발자 포털에서.

* 설정의 `Authentication:Facebook:AppId` 및 `Authentication:Facebook:AppSecret` Azure 포털에서 응용 프로그램 설정으로 합니다. 구성 시스템 환경 변수에서 키를 읽을 수 설정 됩니다.

