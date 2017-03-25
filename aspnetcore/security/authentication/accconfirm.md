---
title: "계정 확인 및 암호 복구 | Microsoft 문서"
author: rick-anderson
description: "전자 메일 확인 및 암호 다시 설정이 지정 된 ASP.NET 핵심 응용 프로그램을 구축 하는 방법을 보여 줍니다."
keywords: "ASP.NET Core 암호 재설정, 확인 전자 메일 보안"
ms.author: riande
manager: wpickett
ms.date: 03/14/2017
ms.topic: article
ms.assetid: d794500b-86f7-4229-a237-e0dd00e2dc08
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/authentication/accconfirm
translationtype: Machine Translation
ms.sourcegitcommit: 6d3767417ae4b0ed7bc4e0ddd20180473ac0f408
ms.openlocfilehash: a7f394d4222c0b4560a4612f323f0960bf876ecd
ms.lasthandoff: 03/23/2017

---
# <a name="account-confirmation-and-password-recovery"></a>계정 확인 및 암호 복구

<a name=security-authentication-account-confirmation></a>

[Rick Anderson](https://twitter.com/RickAndMSFT)

이 자습서에서는 전자 메일 확인 및 암호 다시 설정이 지정 된 ASP.NET 핵심 응용 프로그램을 구축 하는 방법을 보여 줍니다.

## <a name="create-a-new-aspnet-core-project"></a>새 ASP.NET 핵심 프로젝트 만들기

이 자습서는 Visual Studio 2017 이상 필요합니다.

* Visual Studio에서 새 웹 응용 프로그램 프로젝트를 만듭니다.
* 선택 **인증 변경** 로 설정 하 고 **개별 사용자 계정**합니다.

![선택 하는 "개별 사용자 계정을 라디오"를 표시 하는 새 프로젝트 대화 상자](accconfirm/_static/indiv.png)

앱을 실행 선택은 **등록** 링크를 선택한 사용자를 등록 합니다. Entity Framework 마이그레이션을 실행 하는 지침을 따릅니다. 전자 메일에만 유효성 검사는이 시점에서 [[전자 메일 주소]](http://msdn.microsoft.com/en-us/library/system.componentmodel.dataannotations.emailaddressattribute(v=vs.110).aspx) 특성입니다. 등록을 제출 하면 앱에 로그인 합니다. 이 자습서의 뒷부분에 나오는 전자 메일 유효성을 검사 한 될 때까지 새 사용자가 로그인 할 수 없습니다 하도록 변경 합니다 했습니다.

## <a name="view-the-identity-database"></a>Id 데이터베이스 보기

* **보기** 메뉴 **SQL Server 개체 탐색기** (SSOX). 
* 이동 **(localdb) (SQL Server 13) MSSQLLocalDB**합니다. 마우스 오른쪽 단추로 클릭 **dbo. AspNetUsers** > **데이터를 볼**:

![SQL Server 개체 탐색기에서 할 수 없으면 AspNetUsers 테이블의 상황에 맞는 메뉴](accconfirm/_static/ssox.png)

참고는 `EmailConfirmed` 필드는 `False`합니다.

응용 프로그램에서 확인 전자 메일을 보낼 때 다음 단계에서이 전자 메일에 다시 사용할 수도 있습니다. 선택한 행을 마우스 오른쪽 단추로 클릭 **삭제**합니다. 전자 메일을 지금 별칭 삭제 더 쉽게 다음 단계에 있습니다.


## <a name="require-ssl-and-setup-iis-express-for-ssl"></a>Ssl을 사용 하 여 SSL에 대 한 IIS Express를 설치 합니다.

참조 [SSL 적용](xref:security/enforcing-ssl)합니다.

## <a name="require-email-confirmation"></a>전자 메일 확인 필요

다른 사용자에 가장 하지 않는 것을 확인 하는 새 사용자 등록 전자 메일을 확인 하는 것이 좋습니다 (즉, 이러한 하지 않은 등록 된 다른 사람의 전자 메일). 토론 포럼을 사용 하는 경우를 생각해 방지 하기 위해 보겠습니다 것 "yli@example.com로 등록" 에서"nolivetto@contoso.com." 전자 메일 확인 하지 않고 "nolivetto@contoso.com" 응용 프로그램에서 원치 않는 전자 메일을 가져올 수 있습니다. 사용자가 실수로 라고 가정 "ylo@example.com" 맞춤법 오류를 발견 하지 않은 "yli"의 수 앱이 올바른 전자 메일 없기 때문에 암호 복구를 사용 하 여 합니다. 확인 전자 메일 보트에서 제한 된 보호만을 제공 하 고 여러 작업 전자 메일 별칭을 등록 하는 데 사용할 수 있는 결정된 된 스패머가에서 보호를 제공 하지 않습니다.

일반적으로 새 사용자는 확인 된 전자 메일을 갖기 전에 웹 사이트에 모든 데이터를 게시 하는 것을 방지 하려고 합니다. 아래 섹션에서 확인 전자 메일을 사용 하도록 설정 하 고 새로 등록 된 사용자가 전자 메일 확인 된 될 때까지 로그인 하는 것을 방지 하도록 코드를 수정 합니다.

업데이트 `ConfigureServices` 확인된 전자 메일을 요구 하도록 합니다.

[!code-csharp[주](accconfirm/sample/WebApp1/Startup.cs?name=snippet1&highlight=13-16)]

### <a name="configure-email-provider"></a>전자 메일 공급자를 구성 합니다.

이 자습서에서는 전자 메일을 보내려면 SendGrid를 사용 합니다. SendGrid 계정 및 전자 메일을 보내는 키 필요 합니다. 사용 하 여는 [옵션 패턴](xref:fundamentals/configuration#options-config-objects) 사용자 계정 및 키 설정에 액세스 합니다. 자세한 내용은 참조 [구성](xref:fundamentals/configuration#fundamentals-configuration)합니다.

전자 메일 보안 키를 인출 하는 클래스를 만듭니다. 이 샘플의 `AuthMessageSenderOptions` 클래스에 만들어집니다.는 *Services/AuthMessageSenderOptions.cs* 파일입니다.

[!code-csharp[주](accconfirm/sample/WebApp1/Services/AuthMessageSenderOptions.cs?name=snippet1)]

설정의 `SendGridUser` 및 `SendGridKey` 와 [암호 관리자 도구](../app-secrets.md)합니다. 예:

```none
C:\WebAppl\src\WebApp1>dotnet user-secrets set SendGridUser RickAndMSFT
info: Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

Windows에서 암호 관리자의 사용자 키/값 쌍 저장 하는 *secrets.json* 파일에는 %APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId> 디렉터리.</WebAppName-userSecretsId>

콘텐츠는 *secrets.json* 파일은 암호화 되지 않습니다. *secrets.json* 파일은 다음과 같습니다 (의 `SendGridKey` 값 제거 되었습니다.)

  ```json
  {
    "SendGridUser": "RickAndMSFT",
    "SendGridKey": "<key removed>"
  }
  ```

### <a name="configure-startup-to-use-authmessagesenderoptions"></a>사용 하 여 시작 구성`AuthMessageSenderOptions`

추가 `AuthMessageSenderOptions` 끝날 때 서비스 컨테이너에는 `ConfigureServices` 에서 메서드는 *Startup.cs* 파일:

[!code-csharp[주](accconfirm/sample/WebApp1/Startup.cs?name=snippet1&highlight=26)]

### <a name="configure-the-authmessagesender-class"></a>AuthMessageSender 클래스를 구성 합니다.

이 자습서를 통해 전자 메일 알림을 추가 하는 방법을 보여 줍니다 [SendGrid](https://sendgrid.com/), 하지만 SMTP 및 다른 메커니즘을 사용 하 여 메일을 보낼 수 있습니다.

* 설치는 `SendGrid` NuGet 패키지입니다. 다음을 입력 하 여 패키지 관리자 콘솔에서 다음 명령을:

  `Install-Package SendGrid`

* 참조 [무료로 sendgrid 시작](https://sendgrid.com/free/) 무료 SendGrid 계정을 등록 합니다.
* 코드를 추가 *Services/MessageServices.cs* SendGrid를 구성 하는 다음과 비슷합니다.

[!code-csharp[주](accconfirm/sample/WebApp1/Services/MessageServices.cs)]

## <a name="enable-account-confirmation-and-password-recovery"></a>계정 확인 및 암호 복구 사용

서식 파일은 계정 확인 및 암호 복구에 대 한 코드를 이미 있습니다. 사용 하도록 설정 하려면 다음이 단계를 수행 합니다.

*  찾기는 `[HttpPost] Register` 에서 메서드는 *AccountController.cs* 파일입니다.
*  계정 확인을 사용 하도록 설정 하는 코드 주석 처리를 제거 합니다.

[!code-csharp[주](accconfirm/sample/WebApp1/Controllers/AccountController.cs?highlight=16-25&name=snippet_Register)]

참고: 다음 줄을 주석으로 자동으로 로그온 하에서 새로 등록 된 사용자를 또한 방지 하 했습니다.


```csharp 
//await _signInManager.SignInAsync(user, isPersistent: false);
```

*  코드를 주석 처리를 제거 하 여 암호 복구를 활성화는 `ForgotPassword` 작업에는 *controllers/Accountcontroller.cs* 파일입니다.

[!code-csharp[주](accconfirm/sample/WebApp1/Controllers/AccountController.cs?highlight=17-23&name=snippet_ForgotPassword)]

태그를 주석 처리 제거 *Views/Account/ForgotPassword.cshtml*:

[!code-html[주](accconfirm/sample/WebApp1/Views/Account/ForgotPassword.cshtml)]

## <a name="register-confirm-email-and-reset-password"></a>등록 전자 메일을 확인 하 고 암호 재설정

이 섹션에서는 웹 응용 프로그램을 실행 하 고 계정 확인 및 암호 복구 흐름을 보여 줍니다.

* 응용 프로그램을 실행 하 고 새 사용자 등록

 ![웹 응용 프로그램 계정 등록 보기](accconfirm/_static/loginaccconfirm1.png)

* 계정 확인 링크에 대 한 전자 메일을 확인 합니다. 전자 메일 알림 얻지 못한 경우:

  * 전송된 된 메일 메시지를 확인할 SendGrid 웹 사이트를 확인 합니다.
  * 스팸 폴더를 확인 합니다.
  * 다른 전자 메일 별칭에는 다른 전자 메일 공급자 (Microsoft, Yahoo, Gmail 등)를 시도 하십시오.
  * SSOX를에서 이동 **dbo. AspNetUsers** 전자 메일 항목을 삭제 하 고 다시 시도 하십시오.

* 확인 전자 메일에 대 한 링크를 클릭 합니다.
* 사용자 전자 메일 및 암호로 로그인 합니다.
* 로그 오프 합니다.

### <a name="test-password-reset"></a>테스트 암호 재설정

* 로그인 하는 경우 선택 **로그 아웃**합니다.  
* 선택의 **로그인** 연결 하 고 선택 된 **암호를 잊으셨나요?** 링크.
* 계정을 등록 하는 데 사용 되는 전자 메일을 입력 합니다.
* 암호 재설정에 대 한 링크가 포함 된 전자 메일이 전송 됩니다. 전자 메일을 확인 하 고 암호 재설정에 대 한 링크를 클릭 합니다.  암호가 성공적으로 다시 설정, 전자 메일 및 새 암호로 로그인 할 수 있습니다.

## <a name="prevent-login-at-registration"></a>등록에서 로그인을 방지

현재 템플릿을 사용 하 여 사용자 등록 양식을 완료 한 후에 기록 됩니다 (인증 됨). 일반적으로 전자 메일 기록 하기 전에 확인 하려고 합니다. 아래 섹션에서 요구 하는 코드 수정 합니다 없는 새 사용자의 확인 된 전자 메일 기록 하기 전에 합니다. 업데이트는 `[HttpPost] Login` 작업에는 *AccountController.cs* 다음 강조 표시 된 변경 내용과 파일을 합니다.

[!code-csharp[주](accconfirm/sample/WebApp1/Controllers/AccountController.cs?highlight=11-21&name=snippet_Login)]

참고: 프로덕션 테스트 및 개발에는 암호를 사용 하지 않도록 보안 모범 사례는. Azure에 응용 프로그램을 게시 하는 경우에 Azure 웹 앱 포털에서 응용 프로그램 설정으로 SendGrid 암호를 설정할 수 있습니다. 구성 시스템 환경 변수에서 키를 읽을 수 설치 합니다.

## <a name="combine-social-and-local-login-accounts"></a>공유 및 로컬 로그인 계정을 결합합니다

이 섹션을 완료 하려면 먼저 외부 인증 공급자를 사용 해야 합니다. 참조 [Facebook, Google 및 다른 외부 공급자를 사용 하 여 활성화할 때 인증](social/index.md)합니다.

전자 메일 링크를 클릭 하 여 로컬 및 소셜 계정을 결합할 수 있습니다. 다음과 같은 순서로 "RickAndMSFT@gmail.com" 로컬 로그인으로 처음 만들어질 하지만 소셜 로그인으로 계정을 먼저 만든 다음 로컬 로그인을 추가 수 있습니다.

![웹 응용 프로그램: RickAndMSFT@gmail.com 인증 된 사용자](accconfirm/_static/rick.png)

클릭 된 **관리** 링크 합니다. 이 계정에 연결 된 참고 0 외부 (소셜 로그인)

![보기 관리](accconfirm/_static/manage.png)

다른 로그인 서비스에 대 한 링크를 클릭 하 고 응용 프로그램 요청을 수락 합니다. 아래 이미지에서 Facebook에는 외부 인증 공급자입니다.

![Facebook을 나열 하 여 외부 로그인 보기를 관리 합니다.](accconfirm/_static/fb.png)

두 개의 계정은 결합 되었습니다. 두 계정으로 로그온 할 수 있게 됩니다. 다운 되는 인증 서비스의 소셜 로그인 또는 소셜 자신의 계정에 대 한 액세스 사라지므로 있을 가능성이 높습니다 경우 로컬 계정을 추가 하 여 사용자를 할 수 있습니다.

