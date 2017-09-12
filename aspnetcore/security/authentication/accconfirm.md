---
title: "계정 확인 및 ASP.NET 코어에서 암호 복구"
author: rick-anderson
description: "전자 메일 확인 및 암호 재설정으로 ASP.NET Core 응용 프로그램을 구축 하는 방법을 보여 줍니다."
keywords: "ASP.NET Core 암호 다시 설정, 전자 메일 확인, 보안"
ms.author: riande
manager: wpickett
ms.date: 07/19/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/authentication/accconfirm
ms.openlocfilehash: 2f99a5d3db84c3fd3f7ebcb8bccd9a4b8bc8e2b8
ms.sourcegitcommit: 9cdbfd0d670d70b9c354216aabee260c52dad5ee
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/12/2017
---
# <a name="account-confirmation-and-password-recovery-in-aspnet-core"></a>계정 확인 및 ASP.NET 코어에서 암호 복구

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)

이 자습서에서는 전자 메일 확인 및 암호 재설정으로 ASP.NET Core 응용 프로그램을 구축 하는 방법을 보여 줍니다.

## <a name="create-a-new-aspnet-core-project"></a>새 ASP.NET Core 프로젝트 만들기

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

이 단계는 Windows에서 Visual Studio에 적용 됩니다. CLI 지침은 다음 섹션을 참조 하십시오.

이 자습서는 Visual Studio 2017 미리 보기 2 이상이 필요합니다.

* Visual Studio에서 새 웹 응용 프로그램 프로젝트를 만듭니다.
* 선택 **ASP.NET Core 2.0**합니다. 다음 그림 표시 **.NET Core** 선택 하면 선택할 수 있지만 **.NET Framework**합니다.
* 선택 **인증 변경** 로 설정 하 고 **개별 사용자 계정**합니다.
* 기본값을 그대로 두고 **저장 된 사용자 계정 앱에서**합니다.

![선택 하는 "개별 사용자 계정 radio"를 표시 하는 새 프로젝트 대화 상자](accconfirm/_static/2.png)

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

이 자습서는 Visual Studio 2017 이상 필요합니다.

* Visual Studio에서 새 웹 응용 프로그램 프로젝트를 만듭니다.
* 선택 **인증 변경** 로 설정 하 고 **개별 사용자 계정**합니다.

![선택 하는 "개별 사용자 계정 radio"를 표시 하는 새 프로젝트 대화 상자](accconfirm/_static/indiv.png)

---

### <a name="net-core-cli-project-creation-for-macos-and-linux"></a>MacOS 및 Linux 용.NET core CLI 프로젝트 만들기

CLI 또는 SQLite를 사용 하는 경우 명령 창에서 다음을 실행 합니다.

```console
dotnet new mvc --auth Individual
```

* `--auth Individual`개별 사용자 계정 템플릿을 지정합니다.
* Windows에서 추가 된 `-uld` 옵션입니다. `-uld` 옵션은 SQLite DB 보다는 LocalDB 연결 문자열을 만듭니다.
* 실행 `new mvc --help` 도움말을 보려면이 명령에 있습니다.

## <a name="test-new-user-registration"></a>새 사용자 등록을 테스트 합니다.

응용 프로그램을 실행, 선택는 **등록** 링크를 선택한 사용자를 등록 합니다. Entity Framework Core 마이그레이션을 실행 하는 지침을 따릅니다. 전자 메일에만 유효성 검사와는 시점에서 [[EmailAddress]](https://docs.microsoft.com/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute) 특성입니다. 등록을 제출 하면 앱에 로그인 되어 있습니다. 자습서의 뒷부분에 나오는 변경 합니다이 있으므로 새 사용자가 전자 메일의 유효성을 검사 될 때까지 로그인 할 수 없습니다.

## <a name="view-the-identity-database"></a>Id 데이터베이스 보기

# <a name="sql-servertabsql-server"></a>[SQL Server](#tab/sql-server)

* **보기** 메뉴 선택 **SQL Server 개체 탐색기** (SSOX). 
* 로 이동 **(localdb) (SQL Server 13) MSSQLLocalDB**합니다. 마우스 오른쪽 단추로 클릭 **dbo입니다. AspNetUsers** > **데이터를 볼**:

![SQL Server 개체 탐색기의 AspNetUsers 테이블에 대 한 상황에 맞는 메뉴](accconfirm/_static/ssox.png)

참고는 `EmailConfirmed` 필드는 `False`합니다.

응용 프로그램에서 확인 전자 메일을 보낼 때 다음 단계에서이 전자 메일에 다시 사용 수 있습니다. 선택한 행을 마우스 오른쪽 단추로 클릭 **삭제**합니다. 전자 메일을 지금 별칭 삭제는 간편한 방법이 다음 단계에 있습니다.

# <a name="sqlitetabsqlite"></a>[SQLite](#tab/sqlite)

참조 [SQLite ASP.NET Core MVC 프로젝트에서 작업](xref:tutorials/first-mvc-app-xplat/working-with-sql) SQLite DB를 확인 하는 방법에 대 한 지침은 합니다. 

---

## <a name="require-ssl-and-setup-iis-express-for-ssl"></a>Ssl을 사용 하 여 SSL에 대 한 IIS Express를 설치합니다

참조 [SSL 적용](xref:security/enforcing-ssl)합니다.

<a name="prevent-login-at-registration"></a>
## <a name="require-email-confirmation"></a>전자 메일 확인이 필요

다른 사용자에 가장 하지 않는 것을 확인 하려면 새 사용자 등록의 전자 메일을 확인 하는 것이 좋습니다 (즉, 이러한 하지 않은 등록 된 다른 사용자의 전자 메일). 토론 포럼을 가지 며 하지 못하도록 하려는 경우를 가정해 볼 "yli@example.com로 등록" 에서"nolivetto@contoso.com." 전자 메일 확인 하지 않고 "nolivetto@contoso.com" 응용 프로그램에서 원치 않는 전자 메일을 가져올 수 있습니다. 사용자는 실수로으로 등록 된다고 가정 합니다 "ylo@example.com" 맞춤법 오류를 발견 하지 않은 "yli"의 수 앱이 올바른 전자 메일에 없는 때문에 암호 복구를 사용 하도록 합니다. 전자 메일 확인 bot에서만 제한 된 보호를 제공 하 고 여러 작업 전자 메일 별칭을 등록 하는 데 사용할 수 있는 결정된 된 스팸에서 보호를 제공 하지 않습니다.

일반적으로 새 사용자는 확인 된 전자 메일을 갖기 전에 웹 사이트에 데이터를 게시 하는 것을 방지 하려고 합니다. 

업데이트 `ConfigureServices` 확인 된 전자 메일을 요구 하도록 합니다.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

[!code-csharp[Main](accconfirm/sample/WebPW/Startup.cs?name=snippet1&highlight=6-9)]


# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

[!code-csharp[Main](accconfirm/sample/WebApp1/Startup.cs?name=snippet1&highlight=13-16)]

---

 
```csharp
config.SignIn.RequireConfirmedEmail = true;
```
이전 줄에는 해당 전자 메일이 확인 될 때까지 로그인에서 등록 된 사용자 수 없습니다. 그러나 해당 줄에서 등록 한 후에 기록 되 고 새 사용자를 방해 되지는 않습니다. 기본 코드는 등록 한 후 사용자를 로그인 합니다. 로그 한 후에 다시 등록할 때 까지는 로그인 할 수 없습니다. 따라서 새로 등록 된 사용자는 이번에 변경 하는 자습서의 뒷부분에 나오는 **하지** 에 기록 합니다.

### <a name="configure-email-provider"></a>전자 메일 공급자를 구성 합니다.

이 자습서에서는 SendGrid 전자 메일을 보내는 데 사용 됩니다. SendGrid 계정 및 전자 메일을 보내는 키 필요. 다른 전자 메일 공급자를 사용할 수 있습니다. ASP.NET Core 2.x 포함 `System.Net.Mail`, 응용 프로그램에서 전자 메일을 보낼 수 있습니다. 전자 메일을 보내는 SendGrid 또는 다른 전자 메일 서비스를 사용 하는 것이 좋습니다.

[옵션 패턴](xref:fundamentals/configuration#options-config-objects) 사용자 계정 및 키 설정에 액세스 하는 데 사용 됩니다. 자세한 내용은 참조 [구성](xref:fundamentals/configuration#fundamentals-configuration)합니다.

전자 메일 보안 키를 인출 하는 클래스를 만듭니다. 이 샘플은 `AuthMessageSenderOptions` 클래스에 만들어집니다는 *Services/AuthMessageSenderOptions.cs* 파일입니다.

[!code-csharp[Main](accconfirm/sample/WebApp1/Services/AuthMessageSenderOptions.cs?name=snippet1)]

설정의 `SendGridUser` 및 `SendGridKey` 와 [암호 관리자 도구](../app-secrets.md)합니다. 예:

```none
C:\WebAppl\src\WebApp1>dotnet user-secrets set SendGridUser RickAndMSFT
info: Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

암호 관리자 windows에서의 사용자 키/값 쌍을 저장 한 *secrets.json* %APPDATA%/Microsoft/UserSecrets/ < WebAppName userSecretsId > 디렉터리에 파일입니다.

콘텐츠는 *secrets.json* 파일 암호화 되지 않습니다. *secrets.json* 파일은 다음과 같습니다 (의 `SendGridKey` 값 제거 되었습니다.)

  ```json
  {
    "SendGridUser": "RickAndMSFT",
    "SendGridKey": "<key removed>"
  }
  ```

### <a name="configure-startup-to-use-authmessagesenderoptions"></a>AuthMessageSenderOptions를 사용 하는 시작 구성

추가 `AuthMessageSenderOptions` 끝날 때 서비스 컨테이너에는 `ConfigureServices` 에서 메서드는 *Startup.cs* 파일:

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

[!code-csharp[Main](accconfirm/sample/WebPW/Startup.cs?name=snippet1&highlight=18)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)
[!code-csharp[Main](accconfirm/sample/WebApp1/Startup.cs?name=snippet1&highlight=26)]

---

### <a name="configure-the-authmessagesender-class"></a>AuthMessageSender 클래스 구성

이 자습서에서는 통해 전자 메일 알림을 추가 하는 방법을 보여 줍니다. [SendGrid](https://sendgrid.com/), 하지만 SMTP 및 다른 메커니즘을 사용 하 여 메일을 보낼 수 있습니다.

* 설치는 `SendGrid` NuGet 패키지 합니다. 패키지 관리자 콘솔에서 다음을 입력 다음 명령을:

  `Install-Package SendGrid`

* 참조 [SendGrid를 무료로 시작](https://sendgrid.com/free/) 무료 SendGrid 계정을 등록할 수 있습니다.

#### <a name="configure-sendgrid"></a>SendGrid를 구성 합니다.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

* 에 코드를 추가 *Services/EmailSender.cs* SendGrid를 구성 하는 다음과 비슷합니다.

[!code-csharp[Main](accconfirm/sample/WebPW/Services/EmailSender.cs)]


# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)
* 에 코드를 추가 *Services/MessageServices.cs* SendGrid를 구성 하는 다음과 비슷합니다.

[!code-csharp[Main](accconfirm/sample/WebApp1/Services/MessageServices.cs)]

---

## <a name="enable-account-confirmation-and-password-recovery"></a>계정 확인 및 암호 복구 사용

서식 파일에는 계정 확인 및 암호 복구를 위한 코드가 있습니다. 찾을 `[HttpPost] Register` 에서 메서드는 *AccountController.cs* 파일입니다.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

새로 등록 된 사용자가 있는 다음 줄을 주석 처리에 자동으로 로그온에서 함:

```csharp 
await _signInManager.SignInAsync(user, isPersistent: false);
```

전체 메서드 강조 표시 하는 변경 된 줄으로 표시 됩니다.

[!code-csharp[Main](accconfirm/sample/WebPW/Controllers/AccountController.cs?highlight=19&name=snippet_Register)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

계정 확인을 사용할 수 있도록 코드 주석 처리를 제거 합니다.

[!code-csharp[Main](accconfirm/sample/WebApp1/Controllers/AccountController.cs?highlight=16-25&name=snippet_Register)]

참고: 다음 줄을 주석 처리에 자동으로 로그온에서 새로 등록 된 사용자를 또한 방지 하는 것:

```csharp 
//await _signInManager.SignInAsync(user, isPersistent: false);
```

코드의 주석 처리 하 여 암호 복구 사용는 `ForgotPassword` 작업에는 *Controllers/AccountController.cs* 파일입니다.

[!code-csharp[Main](accconfirm/sample/WebApp1/Controllers/AccountController.cs?highlight=17-23&name=snippet_ForgotPassword)]

Form 요소에 주석 처리 제거 *Views/Account/ForgotPassword.cshtml*합니다. 제거 하려는 경우도 `<p> For more information on how to enable reset password ... </p>` 이 문서에 대 한 링크를 포함 하는 요소입니다.

[!code-html[Main](accconfirm/sample/WebApp1/Views/Account/ForgotPassword.cshtml?highlight=7-10,12,28)]

---

## <a name="register-confirm-email-and-reset-password"></a>등록 전자 메일을 확인 하 고 암호를 다시 설정

웹 응용 프로그램을 실행 하 고 계정 확인 및 암호 복구 흐름을 테스트 합니다.

* 응용 프로그램을 실행 하 고 새 사용자 등록

 ![웹 응용 프로그램 계정 등록 보기](accconfirm/_static/loginaccconfirm1.png)

* 계정 확인 링크에 대 한 전자 메일을 확인 합니다. 참조 [전자 메일을 디버그](#debug) 전자 메일을 얻지 못한 경우.
* 사용자의 전자 메일 확인에 대 한 링크를 클릭 합니다.
* 전자 메일 및 암호를 로그인 합니다.
* 로그 오프 합니다.

### <a name="view-the-manage-page"></a>관리 페이지 보기

브라우저에서 자신의 사용자 이름을 선택: ![사용자 이름으로 브라우저 창](accconfirm/_static/un.png)

사용자 이름을 보려면 탐색 모음을 확장 해야 할 수 있습니다.

![탐색 모음](accconfirm/_static/x.png)

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

관리 페이지에 표시 됩니다는 **프로필** 탭이 선택 되어 있습니다. **전자 메일** 확인 전자 메일을 나타내는 확인란을 보여 줍니다. 

![관리 페이지](accconfirm/_static/rick2.png)


# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

이 자습서의 뒷부분에 나오는이 페이지에 대 한 알아봅니다.
![관리 페이지](accconfirm/_static/rick2.png)

---

### <a name="test-password-reset"></a>테스트 암호 재설정

* 로그인 되어 있는 경우 선택 **로그 아웃**합니다.  
* 선택 된 **로그인** 연결 하 고 선택의 **암호를 잊으셨나요?** 링크 합니다.
* 계정 등록에 사용한 전자 메일을 입력 합니다.
* 암호를 다시 설정에 대 한 링크가 포함 된 메일이 전송 됩니다. 전자 메일을 확인 하 고 암호를 다시 설정에 대 한 링크를 클릭 합니다.  암호가 성공적으로 다시 설정, 전자 메일 및 새 암호 로그인 할 수 있습니다.

<a name="debug"></a>

### <a name="debug-email"></a>전자 메일을 디버그

전자 메일 작업을 가져올 수 없습니다 하는 경우:

* 검토는 [전자 메일 작업](https://sendgrid.com/docs/User_Guide/email_activity.html) 페이지.
* 스팸 폴더를 확인 합니다.
* 다른 전자 메일 별칭에는 다른 전자 메일 공급자 (Microsoft, Yahoo, Gmail 등)를 시도 하십시오.
* 만들기는 [전자 메일을 보내는 콘솔 응용 프로그램](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html)합니다.
* 다른 전자 메일 주소로 보내 보십시오.

**참고:** 보안 모범 사례는 테스트 및 개발에서 생산 암호를 사용 하지 않도록 합니다. Azure에 응용 프로그램을 게시 하는 경우에 Azure 웹 앱 포털에서 응용 프로그램 설정으로 SendGrid 암호를 설정할 수 있습니다. 구성 시스템에는 환경 변수에서 키를 읽을 설정 되었습니다.

## <a name="prevent-login-at-registration"></a>등록 시 로그인을 방지

현재 템플릿과 함께 사용자 등록 양식을 완료 되는 로그인 (인증). 일반적으로 자신의 전자 메일 기록 하기 전에 확인 하려고 합니다. 아래 섹션에 것을 요구 하도록 코드 수정 기록 하기 전에 새로운 사용자가 확인 된 전자 메일입니다. 업데이트는 `[HttpPost] Login` 작업에는 *AccountController.cs* 다음 강조 표시 된 변경 내용과 파일을 합니다.

[!code-csharp[Main](accconfirm/sample/WebApp1/Controllers/AccountController.cs?highlight=11-21&name=snippet_Login)]

**참고:** 보안 모범 사례는 테스트 및 개발에서 생산 암호를 사용 하지 않도록 합니다. Azure에 응용 프로그램을 게시 하는 경우에 Azure 웹 앱 포털에서 응용 프로그램 설정으로 SendGrid 암호를 설정할 수 있습니다. 구성 시스템에는 환경 변수에서 키를 읽을 설정 되었습니다.


## <a name="combine-social-and-local-login-accounts"></a>공유 및 로컬 로그인 계정을 결합합니다

참고:이 부분 ASP.NET Core에만 적용 1.x 합니다. ASP.NET에 대 한 핵심 2.x, 참조 [이](https://github.com/aspnet/Docs/issues/3753) 문제입니다.

이 섹션을 완료 하려면 외부 인증 공급자를 먼저 활성화 해야 합니다. 참조 [Facebook, Google 및 다른 외부 공급자를 사용 하 여 사용 하도록 설정 하면 인증](social/index.md)합니다.

사용자 전자 메일 링크를 클릭 하 여 로컬 및 소셜 계정을 결합할 수 있습니다. 그러나 다음 순서로 "RickAndMSFT@gmail.com"; 로컬 로그인으로 처음 만들어질를 만들어 계정을 소셜 로그인으로 먼저, 다음 로컬 로그인을 추가 합니다.

![웹 응용 프로그램: RickAndMSFT@gmail.com 인증 된 사용자](accconfirm/_static/rick.png)

클릭는 **관리** 링크 합니다. 이 계정에 연결 된 참고 0 외부 (소셜 로그인)

![보기를 관리](accconfirm/_static/manage.png)

다른 로그인 서비스에 대 한 링크를 클릭 하 고 응용 프로그램 요청을 수락 합니다. 아래 이미지 Facebook 외부 인증 공급자입니다.

![Facebook을 나열 하 여 외부 로그인 보기를 관리 합니다.](accconfirm/_static/fb.png)

두 개의 계정은 결합 되었습니다. 두 계정으로 로그온 할 수 있게 됩니다. 경우에 대비 다운 되는 인증 서비스에서 소셜의 로그 또는 쓰지만 대개은 स ॅ स 소셜 계정으로 로컬 계정을 추가 하려면 사용자가 할 수 있습니다.
