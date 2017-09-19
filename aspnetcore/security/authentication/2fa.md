---
title: "SMS와 2 단계 인증"
author: rick-anderson
description: "ASP.NET Core와 2 단계 인증 (2FA)을 설정 하는 방법을 보여 줍니다."
keywords: "ASP.NET Core, SMS, 인증, 2FA, 2 단계 인증, 2 단계 인증"
ms.author: riande
manager: wpickett
ms.date: 08/15/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/authentication/2fa
ms.openlocfilehash: 05ce53fe9b65f85867a33fdff974b384bb943d37
ms.sourcegitcommit: 67f54fabbfa4e3942f5bfe1f8a7fdfe4a7a75358
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/19/2017
---
# <a name="two-factor-authentication-with-sms"></a>SMS와 2 단계 인증

여 [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [스위스 빌드하도록](https://github.com/Swiss-Devs)

이 자습서는 ASP.NET Core에 적용 됩니다. 1.x만 합니다. 참조 [ASP.NET Core에서 인증자 앱에 대 한 QR 코드를 사용 하도록 설정 생성](xref:security/authentication/identity-enable-qrcodes) 이상 ASP.NET 코어 2.0에 대 한 합니다.

이 자습서에는 SMS를 사용 하 여 2 단계 인증 (2FA)을 설정 하는 방법을 보여 줍니다. 에 대 한 지침이 제공 됩니다 [twilio](https://www.twilio.com/) 및 [ASPSMS](https://www.aspsms.com/asp.net/identity/core/testcredits/), 하지만 다른 SMS 공급자를 사용할 수 있습니다. 완료 하는 것이 좋습니다 [계정 확인 및 암호 복구](accconfirm.md) 이 자습서를 시작 하기 전에.

보기는 [완성 된 샘플](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authentication/2fa/sample/Web2FA)합니다. [다운로드 하는 방법](xref:tutorials/index#how-to-download-a-sample)합니다.

## <a name="create-a-new-aspnet-core-project"></a>새 ASP.NET Core 프로젝트 만들기

라는 새 ASP.NET Core 웹 앱 만들기 `Web2FA` 개별 사용자 계정을 사용 합니다. 지침에 따라 [ASP.NET Core 응용 프로그램에서 SSL 적용](xref:security/enforcing-ssl) 를 설정 하 고 SSL이 필요 합니다.

### <a name="create-an-sms-account"></a>SMS 계정 만들기

예를 들어, SMS 계정을에서 만들고 [twilio](https://www.twilio.com/) 또는 [ASPSMS](https://www.aspsms.com/asp.net/identity/core/testcredits/)합니다. 인증 자격 증명 기록 (twilio 용: accountSid 및 ASPSMS에 대 한 authToken: 사용자 키로 및 암호).

#### <a name="figuring-out-sms-provider-credentials"></a>SMS 공급자 자격 증명을 파악

**Twilio:**  
Twilio 계정 대시보드 탭에서 복사 된 **계정 SID** 및 **인증 토큰**합니다.

**ASPSMS:**  
계정 설정을에서으로 이동 **사용자 키로** 와 함께 복사 및 프로그램 **암호**합니다.

이러한 값을 키에 암호 관리자 도구를 사용 하 여 나중에 저장할 `SMSAccountIdentification` 및 `SMSAccountPassword`합니다.

#### <a name="specifying-senderid--originator"></a>SenderID 지정 / 송신자

**Twilio:**  
숫자 탭에서 프로그램 Twilio 복사 **전화 번호**합니다. 

**ASPSMS:**  
보낸 사람 잠금 해제 메뉴 내에서 하나 이상의 보낸 사람을 잠금 해제 하거나 (모든 네트워크에서 지원 되지 않음)는 영숫자 송신자를 선택 합니다. 

나중에이 값의 키 아래에 보안 관리자 도구로 저장할 `SMSAccountFrom`합니다.


### <a name="provide-credentials-for-the-sms-service"></a>SMS 서비스에 대 한 자격 증명을 제공 합니다.

에서는 [옵션 패턴](xref:fundamentals/configuration#options-config-objects) 사용자 계정 및 키 설정에 액세스 합니다. 

   * 보안 SMS 키를 인출 하는 클래스를 만듭니다. 이 샘플은 `SMSoptions` 클래스에 만들어집니다는 *Services/SMSoptions.cs* 파일입니다.

[!code-csharp[Main](2fa/sample/Web2FA/Services/SMSoptions.cs)]

설정의 `SMSAccountIdentification`, `SMSAccountPassword` 및 `SMSAccountFrom` 와 [암호 관리자 도구](xref:security/app-secrets)합니다. 예:

```none
C:/Web2FA/src/WebApp1>dotnet user-secrets set SMSAccountIdentification 12345
info: Successfully saved SMSAccountIdentification = 12345 to the secret store.
```
* SMS 공급자에 대 한 NuGet 패키지를 추가 합니다. 패키지 관리자 콘솔 (PMC) 실행:

**Twilio:**  
`Install-Package Twilio`

**ASPSMS:**  
`Install-Package ASPSMS`


* 코드를 추가 *Services/MessageServices.cs* SMS를 사용 하도록 설정할 파일입니다. Twilio 또는 ASPSMS 섹션 중 하나를 사용 합니다.


**Twilio:**  
[!code-csharp[Main](2fa/sample/Web2FA/Services/MessageServices_twilio.cs)]

**ASPSMS:**  
[!code-csharp[Main](2fa/sample/Web2FA/Services/MessageServices_ASPSMS.cs)]

### <a name="configure-startup-to-use-smsoptions"></a>사용 하는 시작 구성`SMSoptions`

추가 `SMSoptions` 서비스 컨테이너에 `ConfigureServices` 에서 메서드는 *Startup.cs*:

[!code-csharp[Main](2fa/sample/Web2FA/Startup.cs?name=snippet1&highlight=4)]

### <a name="enable-two-factor-authentication"></a>2 단계 인증을 사용 하도록 설정

열기는 *Views/Manage/Index.cshtml* Razor 파일 보기 및 주석 문자 (태그 없음 주석으로 처리 하므로) 제거 합니다.

## <a name="log-in-with-two-factor-authentication"></a>2 단계 인증으로 로그인

* 응용 프로그램을 실행 하 고 새 사용자 등록

![Microsoft Edge에서 열려 있는 웹 응용 프로그램 등록 보기](2fa/_static/login2fa1.png)

* 탭을 활성화 하는 사용자 이름에는 `Index` 관리 컨트롤러의 동작 메서드에 합니다. 전화 번호를 탭 합니다 **추가** 링크 합니다.

![보기를 관리](2fa/_static/login2fa2.png)

* 확인 코드를 수신 하 고 누릅니다 하 전화 번호 추가 **확인 코드를 보낼**합니다.

![전화 번호 페이지 추가](2fa/_static/login2fa3.png)

* 확인 코드가 있는 문자 메시지를 받아볼 수 있습니다. 입력 하 고 탭 **제출**

![전화 번호 페이지 확인](2fa/_static/login2fa4.png)

문자 메시지를 얻지 못하면 twilio 로그 페이지를 참조 하십시오.

* 관리 보기는 성공적으로 추가 전화 번호를 표시 합니다.

![보기를 관리](2fa/_static/login2fa5.png)

* 탭 **사용** 2 단계 인증을 사용 하도록 합니다.

![보기를 관리](2fa/_static/login2fa6.png)

### <a name="test-two-factor-authentication"></a>2 단계 인증 테스트

* 로그 오프 합니다.

* 로그인.

* 2 단계 인증을 제공 해야 하므로 사용자 계정 2 단계 인증이 있었습니다. 이 자습서에서는 전화 확인 사용 하도록 설정한 합니다. 템플릿이 제공된은 두 번째 요소로 전자 메일을 설정할 수 있습니다. QR 코드와 같은 인증에 대 한 추가 두 번째 요소를 설정할 수 있습니다. 탭 **제출**합니다.

![확인 코드 보기 보내기](2fa/_static/login2fa7.png)

* SMS 메시지에 코드를 입력 합니다.

* 클릭 하 고 **저장이 브라우저** 확인란 있습니다 2FA를 사용 하 여 동일한 장치 및 브라우저를 사용 하는 경우 로그온 하에서 제외 됩니다. 2FA를 사용 하도록 설정 하 고 클릭 하 **저장이 브라우저** 알려 강력한 2FA 보호 장치에 액세스할 수 없는 상태로 회원님의 계정에 액세스 하려고 하는 악의적인 사용자 로부터 합니다. 정기적으로 사용 하는 모든 개인 장치에서이 수행할 수 있습니다. 설정 하 여 **저장이 브라우저**, 정기적으로 사용 하지 않는 장치에서 2FA의 강화 된 보안을 얻게 및에 자신의 장치에 2FA를 통과 하지 않아도 되는 편의성을 가져옵니다.

![보기를 확인 합니다.](2fa/_static/login2fa8.png)

## <a name="account-lockout-for-protecting-against-brute-force-attacks"></a>무차별 암호 대입 공격 으로부터 보호 하기 위한 계정 잠금

계정 잠금 2FA 사용 하는 것이 좋습니다. 사용자가 로컬 계정 또는 소셜 계정) (통해 로그인 하 고 2FA에서 연결 시도가 실패할된 때마다 저장 됩니다 (기본값은 5)는 최대 시도 횟수에 도달 하면 사용자가 잠길 5 분 (잠금 시간 초과 설정할 수 있습니다 `DefaultAccountLockoutTimeSpan`). 다음에서는 10 번 실패 후 10 분 동안 잠길 수 계정을 구성 합니다.

[!code-csharp[Main](2fa/sample/Web2FA/Startup.cs?name=snippet2&highlight=13-17)] 
