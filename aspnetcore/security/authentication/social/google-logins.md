---
title: "Google 외부 로그인 설정 | Microsoft 문서"
author: rick-anderson
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 11/2/2016
ms.topic: article
ms.assetid: 8BA389D6-0911-4415-A818-C7B3F5B5CC8D
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/authentication/google-logins
translationtype: Machine Translation
ms.sourcegitcommit: 010b730d2716f9f536fef889bc2f767afb648ef4
ms.openlocfilehash: 9d5e94a3b545baec3e6f7cca8d0fc70d87536ea5
ms.lasthandoff: 03/23/2017

---
# <a name="configuring-google-authentication"></a>Google 인증 구성

<a name=security-authentication-google-logins></a>

여 [Rick Anderson](https://twitter.com/RickAndMSFT), [Pranav Rastogi](https://github.com/rustd), 및 [Valeriy Novytskyy](https://github.com/01binary)

이 자습서에서는 ASP.NET 핵심 프로젝트에서 만든 샘플을 사용 하 여 Google + 계정으로 로그인 하 여 사용자를 활성화 하는 방법을 보여 줍니다는 [이전 페이지](index.md)합니다. 수행 하 여 시작 된 [공식 단계](https://developers.google.com/identity/sign-in/web/devconsole-project) Google API 콘솔에서 새 앱을 만듭니다.

## <a name="creating-the-app-in-google-api-console"></a>Google API 콘솔에서 응용 프로그램 만들기

* 이동 [https://console.developers.google.com/projectselector/apis/library](https://console.developers.google.com/projectselector/apis/library) 에 로그인 합니다. Google 계정이 없는 경우 사용 하 여는 **[계정 만들기](https://accounts.google.com/SignUpWithoutGmail?service=cloudconsole&continue=https%3A%2F%2Fconsole.developers.google.com%2Fprojectselector%2Fapis%2Flibrary&ltmpl=api)** 링크를 만듭니다.

![Google API 콘솔](index/_static/GoogleConsoleLogin.png)

* API 관리자 라이브러리 페이지로 리디렉션됩니다.

![API 관리자 라이브러리 페이지](index/_static/GoogleConsoleSwitchboard.png)

* 누르기 **프로젝트를 만들** 하 고 응용 프로그램 이름 입력:

![새 프로젝트 대화 상자](index/_static/GoogleConsoleNewProj.png)

* 대화 상자를 수락한 후 새 응용 프로그램에 대 한 기능을 선택할 수 있도록 라이브러리 페이지로 다시 리디렉션됩니다. <a name="enable-googleplus">찾을 **Google + API** 목록의</a> API 기능을 추가 하려면 해당 링크를 클릭 합니다.

![API 관리자 라이브러리 페이지](index/_static/GoogleConsoleChooseApi.png)

* 새로 추가 된 API에 대 한 페이지가 표시 됩니다. 누르기 **사용** 기능에서 앱에 Google + 기호를 추가 하려면:

![Google + API API 관리자 페이지](index/_static/GoogleConsoleEnableApi.png)

* 탭은 API를 사용 하도록 설정한 후 **자격 증명으로 이동** 비밀 정보를 구성 하려면:

![Google + API API 관리자 페이지](index/_static/GoogleConsoleGoCredentials.png)

* 다음 중 하나를 선택합니다.
   * **Google + API**
   * **웹 서버 (예:: node.js, Tomcat)**, 및
   * **사용자 데이터**:

![API 관리자 자격 증명 페이지: 어떤 종류의 자격 증명에 대해 알아봅니다 필요한 패널](index/_static/GoogleConsoleChooseCred.png)

* 누르기 **하는 자격 증명 필요 합니까?** 응용 프로그램 구성의 두 번째 단계로 이동 하 합니다.

![API 관리자 자격 증명 페이지: OAuth 2.0 클라이언트 ID 만들기](index/_static/GoogleConsoleCreateClient.png)

* 하나의 기능 (로그인)을 동일한 입력할 수 있습니다 Google + 프로젝트를 만드는 것 때문에 **이름** 프로젝트에 사용한 것과 OAuth 2.0 클라이언트 id입니다.

* 와 현재 사이트 URL을 입력 */signin-google* 에 추가 된 **Authorized redirect URIs** 필드. 예를 들어, `https://localhost:44320/signin-google`을 입력합니다.
  
  > [!NOTE]
  > 사이트를 배포할 때 새 공용 url을 등록 해야 합니다.

  > [!NOTE]
  > 구성할 필요가 없습니다 **/signin-google** 으로 응용 프로그램의 경로입니다. Google 미들웨어는 자동으로이 경로에 대 한 요청을 중단 하 고 OAuth 흐름을 구현 하도록 처리 합니다.

* 누르기 **Create client ID**, 세 번째 단계로 이동 있는:

![API 관리자 자격 증명 페이지: OAuth 2.0 동의 화면 설정](index/_static/GoogleConsoleAddCred.png)

* 프로그램 공용 주소가 입력 **전자 메일 주소** 및 **제품 이름** Google + 하 라는 메시지 서명 하는 경우 앱에 대 한 표시 합니다.

* 누르기 **계속** 마지막 단계를 진행 하려면:

![API 관리자 자격 증명 페이지: 자격 증명 다운로드](index/_static/GoogleConsoleFinish.png)

* 누르기 **다운로드** 응용 프로그램의 비밀으로 JSON 파일을 저장 하 고 **수행** 새 앱 만들기를 완료 합니다.

## <a name="storing-google-clientid-and-clientsecret"></a>Google ClientID 및 ClientSecret를 저장합니다.

Google와 같은 중요 한 설정은 연결 `ClientID` 및 `ClientSecret` 를 사용 하 여 응용 프로그램 구성에는 [암호 관리자 도구](../../app-secrets.md) 저장 하는 대신 해당 구성 파일에서을 직접에 설명 된 대로 [소셜 인증 개요 페이지](index.md)합니다.

* 마지막 단계에서 다운로드 한 JSON 파일을 엽니다. 참고는 `client_id` 및 `client_secret` JSON 구조에 있는 값입니다.

* 작업 디렉터리에 Google 비밀 정보를 저장할 프로젝트에서 다음 명령을 실행 합니다.

  <!-- literal_block {"ids": [], "xml:space": "preserve"} -->

  ```
  dotnet user-secrets set Authentication:Google:ClientID <client_id>
  dotnet user-secrets set Authentication:Google:ClientSecret <client-secret>
     ```

다음 코드에서 저장 된 구성 값을 읽고는 [암호 관리자](../../app-secrets.md#security-app-secrets):

[!code-csharp[주](../../../common/samples/WebApplication1/Startup.cs?highlight=11&range=20-36)]

## <a name="enable-google-middleware"></a>Google 미들웨어를 사용 하도록 설정

> [!NOTE]
> NuGet을 사용 하 여 설치 하는 [Microsoft.AspNetCore.Authentication.Google](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Google) 아직 설치 되지 않은 경우 패키지 합니다. 프로젝트 디렉터리에서 다음 명령을 실행 또는 합니다.
>
> `dotnet add package Microsoft.AspNetCore.Authentication.Google`

추가에서 Google 미들웨어는 `Configure` 메서드에서 `Startup.cs`:

```csharp
app.UseGoogleAuthentication(new GoogleOptions()
{
    ClientId = Configuration["Authentication:Google:ClientId"],
    ClientSecret = Configuration["Authentication:Google:ClientSecret"]
});
```

> [!NOTE]
> 호출 `UseIdentity` 호출 하기 전에 `UseGoogleAuthentication`합니다. 참조는 [소셜 인증 개요 페이지](index.md)합니다.

## <a name="sign-in-with-google"></a>Google을 사용 하 여 로그인

응용 프로그램을 실행 하 고 클릭 **로그인**합니다. Google을 사용 하 여 로그인 하는 옵션이 나타납니다.

![Microsoft Edge에서 실행 되는 웹 응용 프로그램: 인증 되지 않은 사용자](index/_static/DoneGoogle.png)

Google을 클릭할 때 리디렉션됩니다 Google 인증을 위해:

![Google 인증 대화 상자](index/_static/GoogleLogin.png)

Google 자격 증명을 입력 한 후 다음 리디렉션됩니다 전자 메일을 설정할 수 있는 웹 사이트에 다시.

이제 Google 자격 증명을 사용 하 여 로그인 됩니다.

![Microsoft Edge에서 실행 되는 웹 응용 프로그램: 사용자 인증](index/_static/Done.png)

> [!NOTE]
> 수신 하는 대신 한 `403 (Forbidden)` 개발 모드 (또는 동일한 오류가 발생 하 여 디버거를 중단)에서 실행 되 고 있는지 확인 하는 경우 사용자 고유의 응용 프로그램에서 오류 페이지 **Google + API** 에서 설정 되어는 **API 관리자 라이브러리** 나열 된 단계에 따라 [이 페이지에서 이전](#enable-googleplus)합니다. 로그인이 작동 하지 않는 경우 오류가 나타나지 문제를 더 쉽게 디버깅 하려면 개발 모드로 전환 합니다.

## <a name="next-steps"></a>다음 단계

* 이 문서에 살펴보았습니다 방법을 Google로 인증할 수 있습니다. 에 나열 된 다른 공급자를 사용 하 여 인증 하는 비슷한 접근 방법을 따를 수는 [이전 페이지](index.md)합니다.

* Azure 웹 앱에 웹 사이트를 게시 하면을 다시 설정 해야는 `ClientSecret` Google API 콘솔에 있습니다.

* 설정의 `Authentication:Google:ClientId` 및 `Authentication:Google:ClientSecret` Azure 포털에서 응용 프로그램 설정으로 합니다. 구성 시스템 환경 변수에서 키를 읽을 수 설정 됩니다.

