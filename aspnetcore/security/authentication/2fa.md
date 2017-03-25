---
title: "SMS와&2; 단계 인증 | Microsoft 문서"
author: rick-anderson
description: "ASP.NET Core와&2; 단계 인증 (2FA)을 설정 하는 방법을 보여 줍니다."
keywords: "ASP.NET Core, SMS, 인증, 2FA, 2 단계 인증, 2 단계 인증"
ms.author: riande
manager: wpickett
ms.date: 02/14/2017
ms.topic: article
ms.assetid: ff1c22d1-d1f2-4616-84dd-94ba61ec299a
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/authentication/2fa
translationtype: Machine Translation
ms.sourcegitcommit: 010b730d2716f9f536fef889bc2f767afb648ef4
ms.openlocfilehash: a1daadbc3ff65c23417bd5070fbe96da6fee2dba
ms.lasthandoff: 03/23/2017

---
# <a name="two-factor-authentication-with-sms"></a>SMS와&2; 단계 인증

여 [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [스위스 개발자](https://github.com/Swiss-Devs)

이 자습서에서는 SMS를 사용 하 여&2; 단계 인증 (2FA)을 설정 하는 방법을 보여 줍니다. ASPSMS 사용 되지만 다른 SMS 공급자를 사용할 수 있습니다. 완료 하는 것이 좋습니다 [계정 확인 및 암호 복구](accconfirm.md) 이 자습서를 시작 하기 전에 합니다.

## <a name="create-a-new-aspnet-core-project"></a>새 ASP.NET 핵심 프로젝트 만들기

개별 사용자 계정으로 새로운 ASP.NET 핵심 웹 앱을 만듭니다.

![Visual Studio 새 프로젝트 대화 상자](accconfirm/_static/new-project.png)

프로젝트를 만든 후의 지침에 따라 [계정 확인 및 암호 복구](accconfirm.md) 를 설정 하 고 ssl을 사용 합니다.

## <a name="setup-up-sms-for-two-factor-authentication-with-aspsms"></a>2 단계 인증 ASPSMS에 SMS를 설치 합니다.

* 만들기는 [ASPSMS](https://www.aspsms.com/asp.net/identity/core/testcredits/) 계정.

* 계정 설정을에서으로 이동 **사용자 키로** 자체 정의 함께 복사 및 **암호**합니다. 나중에 이러한 값은 암호 관리자 도구를 사용 하 여 저장 합니다.
 
* 내에서 **발송자 잠금 해제** 메뉴에서 하나 이상의 보낸 사람을 잠금 해제 하거나 (모든 네트워크에서 지원 되지 않음)는 영숫자 송신자를 선택 합니다. 나중에 너무 암호 관리자 도구를 사용 하 여이 값을 저장 합니다.

* ASPSMS NuGet 패키지를 설치 합니다. 다음을 입력 하는 패키지 관리자 콘솔 (PMC)에서 다음 명령을:

   <!-- literal_block {"ids": [], "xml:space": "preserve"} -->

   ```
   Install-Package ASPSMS
   ```

* 코드를 추가 *Services/MessageServices.cs* SMS를 사용 하도록 설정 하는 파일입니다.

[!code-csharp[주](2fa/sample/WebSMS/src/WebSMS/Services/MessageServices.cs?range=12-44)]

> [!NOTE]
> 제거할 수 있습니다 `//` 줄에서 주석 문자는 `System.Diagnostics.Debug.WriteLine(message);` SMS 메시지를 가져올 수 없으면 응용 프로그램을 테스트 하려면. 기본 제공을 사용 하는 것이 좋습니다 [로깅 시스템](../../fundamentals/logging.md)합니다.

### <a name="configure-the-sms-provider-keyvalue"></a>SMS 공급자의 키/값 구성

사용 하 여는 [옵션 패턴](../../fundamentals/configuration.md#options-config-objects) 사용자 계정 및 키 설정에 액세스 합니다. 자세한 내용은 참조 [구성](../../fundamentals/configuration.md#fundamentals-configuration)합니다.

   * 보안 SMS 키를 인출 하는 클래스를 만듭니다. 이 샘플의 `AuthMessageSMSSenderOptions` 클래스에 만들어집니다.는 *Services/AuthMessageSMSSenderOptions.cs* 파일입니다.

[!code-csharp[주](2fa/sample/WebSMS/src/WebSMS/Services/AuthMessageSMSSenderOptions.cs?range=3-8)]

설정 `Userkey`, `Password`, 및 `Originator` 와 [암호 관리자 도구](../app-secrets.md)합니다. 예:

```none
C:/WebSMS/src/WebApp1>dotnet user-secrets set Userkey IT2VHGB23K3
info: Successfully saved Userkey = IT2VHGB23K3 to the secret store.
```

### <a name="configure-startup-to-use-authmessagesmssenderoptions"></a>사용 하 여 시작 구성`AuthMessageSMSSenderOptions`

추가 `AuthMessageSMSSenderOptions` 끝날 때 서비스 컨테이너에는 `ConfigureServices` 에서 메서드는 *Startup.cs* 파일:

[!code-csharp[주](./2fa/sample/WebSMS/src/WebSMS/Startup.cs?highlight=4&range=73-77)]

## <a name="enable-two-factor-authentication"></a>2 단계 인증을 사용 하도록 설정

*  열기는 *Views/Manage/Index.cshtml* Razor 뷰 파일입니다.

*  시작 하는 전화 번호 태그를 주석 처리 제거

    `@*@(Model.PhoneNumber ?? "None")`

*  주석 처리를 제거는 `Model.TwoFactor` 에서 시작 하는 태그

    `@*@if (Model.TwoFactor)`

* 주석 처리 하거나 제거는 `<p>There are no two-factor authentication providers configured.` 태그입니다.

    완성 된 코드는 다음과 같습니다.

[!code-html[주](2fa/sample/WebSMS/src/WebSMS/Views/Manage/Index.cshtml?range=32-77)]

## <a name="log-in-with-two-factor-authentication"></a>2 단계 인증으로 로그인

* 응용 프로그램을 실행 하 고 새 사용자 등록

![Microsoft Edge에 열려 있는 웹 응용 프로그램 등록 보기](2fa/_static/login2fa1.png)

* 활성화 하는 사용자 이름, 누르기는 `Index` 관리 컨트롤러의 작업 메서드. 다음 전화 번호를 눌러 **추가** 링크 합니다.

![보기 관리](2fa/_static/login2fa2.png)

* 확인 코드를 수신 하 고 탭 하는 전화 번호를 추가할 **확인 코드 보내기**합니다.

![전화 번호 페이지 추가](2fa/_static/login2fa3.png)

* 확인 코드가 포함 된 문자 메시지를 얻게 됩니다. 입력 하 고 탭 **전송**

![전화 번호 페이지 확인](2fa/_static/login2fa4.png)

문자 메시지를 얻지 못한 경우 참조 [ASPSMS Sendlog](#aspsms-sendlog)합니다.

* 관리 보기는 성공적으로 추가 전화 번호를 표시 합니다.

![보기 관리](2fa/_static/login2fa5.png)

* 누르기 **사용**&2; 단계 인증을 사용 하도록 합니다.

![보기 관리](2fa/_static/login2fa6.png)

### <a name="test-two-factor-authentication"></a>2 단계 인증 테스트

* 로그 오프 합니다.

* 로그인.

* 사용자 계정 인증의 두 번째 요소를 제공 해야 하므로 이중 인증이 있었습니다. 이 자습서에서는 휴대폰 확인 하도록 설정한 합니다. 또한 템플릿이 제공된을 통해 두 번째 단계로 전자 메일을 설정할 수 있습니다. QR 코드와 같은 인증에 대 한 추가 두 번째 요소를 설정할 수 있습니다. 누르기 **제출**합니다.

![확인 코드 보기 보내기](2fa/_static/login2fa7.png)

* SMS 메시지에서 가져올 코드를 입력 합니다.

* 클릭 하는 **이 브라우저 기억** 확인란 있습니다 2FA 같은 장치 및 브라우저를 사용 하는 경우 로그온을 사용 하 여 대상에서 제외 합니다. 클릭 하 고 2FA 사용 **이 브라우저를 기억** 제공 해 강력한 2FA 보호 장치에 액세스할 수 없는으로 계정에 액세스 하려고 하는 악의적인 사용자 로부터. 정기적으로 사용 하는 모든 개인 장치에서이 수행할 수 있습니다. 설정 하 여 **이 브라우저 기억**정기적으로 사용 하지 않는 장치에서 추가 보안을 2FA 얻게 하 고 자신의 장치에서 2FA 진행 하지 않아도에 편의 얻게 됩니다.

![보기를 확인 합니다.](2fa/_static/login2fa8.png)

## <a name="account-lockout-for-protecting-against-brute-force-attacks"></a>무차별 암호 대입 공격 으로부터 보호 하기 위한 계정 잠금

계정 잠금 2FA 사용 하는 것이 좋습니다. 사용자가 로컬 계정 또는 소셜 계정) (통해 로그인 하 고 2FA에서 연결 시도가 실패할된 때마다 저장 됩니다 (기본값은 5) 최대 시도 횟수에 도달 하는 경우는 사용자가 잠겨 5 분 (잠금을 사용 하 여 시간 초과 설정할 수 있습니다 `DefaultAccountLockoutTimeSpan`). 다음 10 번 실패 후 10 분 동안 잠긴 계정을 구성 합니다.

[!code-csharp[주](./2fa/sample/WebSMS/src/WebSMS/Startup.cs?highlight=1,2,3,4,5&range=67-77)]

## <a name="aspsms-sendlog"></a>ASPSMS Sendlog

SMS 메시지를 얻지 못한 경우에 로그인 하는 [ASPSMS 사이트](https://www.aspsms.com/en/) 로 이동 하 여 **Sendlog** 페이지. 메시지 전송 및 배달 된 것을 확인할 수 있습니다.

    

