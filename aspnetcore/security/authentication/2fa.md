---
title: "SMS와 2 단계 인증"
author: rick-anderson
description: "ASP.NET Core와 2 단계 인증 (2FA)을 설정 하는 방법을 보여 줍니다."
keywords: "ASP.NET Core, SMS, 인증, 2FA, 2 단계 인증, 2 단계 인증"
ms.author: riande
manager: wpickett
ms.date: 8/15/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/authentication/2fa
ms.openlocfilehash: 77404de2f367cb12ba25433899198b69f9e5a7f2
ms.sourcegitcommit: 9a22c64759a7285ba788a37039bea5fe95f45f21
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/15/2017
---
# <a name="two-factor-authentication-with-sms"></a><span data-ttu-id="0b8c2-104">SMS와 2 단계 인증</span><span class="sxs-lookup"><span data-stu-id="0b8c2-104">Two-factor authentication with SMS</span></span>

<span data-ttu-id="0b8c2-105">여 [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [스위스 빌드하도록](https://github.com/Swiss-Devs)</span><span class="sxs-lookup"><span data-stu-id="0b8c2-105">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Swiss-Devs](https://github.com/Swiss-Devs)</span></span>

<span data-ttu-id="0b8c2-106">이 자습서는 ASP.NET Core에 적용 됩니다. 1.x만 합니다.</span><span class="sxs-lookup"><span data-stu-id="0b8c2-106">This tutorial applies to ASP.NET Core 1.x only.</span></span> <span data-ttu-id="0b8c2-107">참조 [ASP.NET Core에서 인증자 앱에 대 한 QR 코드를 사용 하도록 설정 생성](xref:security/authentication/identity-enable-qrcodes) 이상 ASP.NET 코어 2.0에 대 한 합니다.</span><span class="sxs-lookup"><span data-stu-id="0b8c2-107">See [Enabling QR Code generation for authenticator apps in ASP.NET Core](xref:security/authentication/identity-enable-qrcodes) for ASP.NET Core 2.0 and later.</span></span>

<span data-ttu-id="0b8c2-108">이 자습서에는 SMS를 사용 하 여 2 단계 인증 (2FA)을 설정 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="0b8c2-108">This tutorial shows how to set up two-factor authentication (2FA) using SMS.</span></span> <span data-ttu-id="0b8c2-109">에 대 한 지침이 제공 됩니다 [twilio](https://www.twilio.com/) 및 [ASPSMS](https://www.aspsms.com/asp.net/identity/core/testcredits/), 하지만 다른 SMS 공급자를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0b8c2-109">Instructions are given for [twilio](https://www.twilio.com/) and [ASPSMS](https://www.aspsms.com/asp.net/identity/core/testcredits/), but you can use any other SMS provider.</span></span> <span data-ttu-id="0b8c2-110">완료 하는 것이 좋습니다 [계정 확인 및 암호 복구](accconfirm.md) 이 자습서를 시작 하기 전에.</span><span class="sxs-lookup"><span data-stu-id="0b8c2-110">We recommend you complete [Account Confirmation and Password Recovery](accconfirm.md) before starting this tutorial.</span></span>

<span data-ttu-id="0b8c2-111">보기는 [완성 된 샘플](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authentication/2fa/sample/Web2FA)합니다.</span><span class="sxs-lookup"><span data-stu-id="0b8c2-111">View the [completed sample](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authentication/2fa/sample/Web2FA).</span></span> <span data-ttu-id="0b8c2-112">[다운로드 하는 방법](xref:tutorials/index#how-to-download-a-sample)합니다.</span><span class="sxs-lookup"><span data-stu-id="0b8c2-112">[How to download](xref:tutorials/index#how-to-download-a-sample).</span></span>

## <a name="create-a-new-aspnet-core-project"></a><span data-ttu-id="0b8c2-113">새 ASP.NET Core 프로젝트 만들기</span><span class="sxs-lookup"><span data-stu-id="0b8c2-113">Create a new ASP.NET Core project</span></span>

<span data-ttu-id="0b8c2-114">라는 새 ASP.NET Core 웹 앱 만들기 `Web2FA` 개별 사용자 계정을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="0b8c2-114">Create a new ASP.NET Core web app named `Web2FA` with individual user accounts.</span></span> <span data-ttu-id="0b8c2-115">지침에 따라 [ASP.NET Core 응용 프로그램에서 SSL 적용](xref:security/enforcing-ssl) 를 설정 하 고 SSL이 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="0b8c2-115">Follow the instructions in [Enforcing SSL in an ASP.NET Core app](xref:security/enforcing-ssl) to set up and require SSL.</span></span>

### <a name="create-an-sms-account"></a><span data-ttu-id="0b8c2-116">SMS 계정 만들기</span><span class="sxs-lookup"><span data-stu-id="0b8c2-116">Create an SMS account</span></span>

<span data-ttu-id="0b8c2-117">예를 들어, SMS 계정을에서 만들고 [twilio](https://www.twilio.com/) 또는 [ASPSMS](https://www.aspsms.com/asp.net/identity/core/testcredits/)합니다.</span><span class="sxs-lookup"><span data-stu-id="0b8c2-117">Create an SMS account, for example, from [twilio](https://www.twilio.com/) or [ASPSMS](https://www.aspsms.com/asp.net/identity/core/testcredits/).</span></span> <span data-ttu-id="0b8c2-118">인증 자격 증명 기록 (twilio 용: accountSid 및 ASPSMS에 대 한 authToken: 사용자 키로 및 암호).</span><span class="sxs-lookup"><span data-stu-id="0b8c2-118">Record the authentication credentials (for twilio: accountSid and authToken, for ASPSMS: Userkey and Password).</span></span>

#### <a name="figuring-out-sms-provider-credentials"></a><span data-ttu-id="0b8c2-119">SMS 공급자 자격 증명을 파악</span><span class="sxs-lookup"><span data-stu-id="0b8c2-119">Figuring out SMS Provider credentials</span></span>

<span data-ttu-id="0b8c2-120">**Twilio:**</span><span class="sxs-lookup"><span data-stu-id="0b8c2-120">**Twilio:**</span></span>  
<span data-ttu-id="0b8c2-121">Twilio 계정 대시보드 탭에서 복사 된 **계정 SID** 및 **인증 토큰**합니다.</span><span class="sxs-lookup"><span data-stu-id="0b8c2-121">From the Dashboard tab of your Twilio account, copy the **Account SID** and **Auth token**.</span></span>

<span data-ttu-id="0b8c2-122">**ASPSMS:**</span><span class="sxs-lookup"><span data-stu-id="0b8c2-122">**ASPSMS:**</span></span>  
<span data-ttu-id="0b8c2-123">계정 설정을에서으로 이동 **사용자 키로** 와 함께 복사 및 프로그램 **암호**합니다.</span><span class="sxs-lookup"><span data-stu-id="0b8c2-123">From your account settings, navigate to **Userkey** and copy it together with your **Password**.</span></span>

<span data-ttu-id="0b8c2-124">이러한 값을 키에 암호 관리자 도구를 사용 하 여 나중에 저장할 `SMSAccountIdentification` 및 `SMSAccountPassword`합니다.</span><span class="sxs-lookup"><span data-stu-id="0b8c2-124">We will later store these values in with the secret-manager tool within the keys `SMSAccountIdentification` and `SMSAccountPassword`.</span></span>

#### <a name="specifying-senderid--originator"></a><span data-ttu-id="0b8c2-125">SenderID 지정 / 송신자</span><span class="sxs-lookup"><span data-stu-id="0b8c2-125">Specifying SenderID / Originator</span></span>

<span data-ttu-id="0b8c2-126">**Twilio:**</span><span class="sxs-lookup"><span data-stu-id="0b8c2-126">**Twilio:**</span></span>  
<span data-ttu-id="0b8c2-127">숫자 탭에서 프로그램 Twilio 복사 **전화 번호**합니다.</span><span class="sxs-lookup"><span data-stu-id="0b8c2-127">From the Numbers tab, copy your Twilio **phone number**.</span></span> 

<span data-ttu-id="0b8c2-128">**ASPSMS:**</span><span class="sxs-lookup"><span data-stu-id="0b8c2-128">**ASPSMS:**</span></span>  
<span data-ttu-id="0b8c2-129">보낸 사람 잠금 해제 메뉴 내에서 하나 이상의 보낸 사람을 잠금 해제 하거나 (모든 네트워크에서 지원 되지 않음)는 영숫자 송신자를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="0b8c2-129">Within the Unlock Originators Menu, unlock one or more Originators or choose an alphanumeric Originator (Not supported by all networks).</span></span> 

<span data-ttu-id="0b8c2-130">나중에이 값의 키 아래에 보안 관리자 도구로 저장할 `SMSAccountFrom`합니다.</span><span class="sxs-lookup"><span data-stu-id="0b8c2-130">We will later store this value with the secret-manager tool within the key `SMSAccountFrom`.</span></span>


### <a name="provide-credentials-for-the-sms-service"></a><span data-ttu-id="0b8c2-131">SMS 서비스에 대 한 자격 증명을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="0b8c2-131">Provide credentials for the SMS service</span></span>

<span data-ttu-id="0b8c2-132">에서는 [옵션 패턴](xref:fundamentals/configuration#options-config-objects) 사용자 계정 및 키 설정에 액세스 합니다.</span><span class="sxs-lookup"><span data-stu-id="0b8c2-132">We'll use the [Options pattern](xref:fundamentals/configuration#options-config-objects) to access the user account and key settings.</span></span> 

   * <span data-ttu-id="0b8c2-133">보안 SMS 키를 인출 하는 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="0b8c2-133">Create a class to fetch the secure SMS key.</span></span> <span data-ttu-id="0b8c2-134">이 샘플은 `SMSoptions` 클래스에 만들어집니다는 *Services/SMSoptions.cs* 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="0b8c2-134">For this sample, the `SMSoptions` class is created in the *Services/SMSoptions.cs* file.</span></span>

<span data-ttu-id="0b8c2-135">[!code-csharp[Main](2fa/sample/Web2FA/Services/SMSoptions.cs)]</span><span class="sxs-lookup"><span data-stu-id="0b8c2-135">[!code-csharp[Main](2fa/sample/Web2FA/Services/SMSoptions.cs)]</span></span>

<span data-ttu-id="0b8c2-136">설정의 `SMSAccountIdentification`, `SMSAccountPassword` 및 `SMSAccountFrom` 와 [암호 관리자 도구](xref:security/app-secrets)합니다.</span><span class="sxs-lookup"><span data-stu-id="0b8c2-136">Set the `SMSAccountIdentification`, `SMSAccountPassword` and `SMSAccountFrom` with the [secret-manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="0b8c2-137">예:</span><span class="sxs-lookup"><span data-stu-id="0b8c2-137">For example:</span></span>

```none
C:/Web2FA/src/WebApp1>dotnet user-secrets set SMSAccountIdentification 12345
info: Successfully saved SMSAccountIdentification = 12345 to the secret store.
```
* <span data-ttu-id="0b8c2-138">SMS 공급자에 대 한 NuGet 패키지를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="0b8c2-138">Add the NuGet package for the SMS provider.</span></span> <span data-ttu-id="0b8c2-139">패키지 관리자 콘솔 (PMC) 실행:</span><span class="sxs-lookup"><span data-stu-id="0b8c2-139">From the Package Manager Console (PMC) run:</span></span>

<span data-ttu-id="0b8c2-140">**Twilio:**</span><span class="sxs-lookup"><span data-stu-id="0b8c2-140">**Twilio:**</span></span>  
`Install-Package Twilio`

<span data-ttu-id="0b8c2-141">**ASPSMS:**</span><span class="sxs-lookup"><span data-stu-id="0b8c2-141">**ASPSMS:**</span></span>  
`Install-Package ASPSMS`


* <span data-ttu-id="0b8c2-142">코드를 추가 *Services/MessageServices.cs* SMS를 사용 하도록 설정할 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="0b8c2-142">Add code in the *Services/MessageServices.cs* file to enable SMS.</span></span> <span data-ttu-id="0b8c2-143">Twilio 또는 ASPSMS 섹션 중 하나를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="0b8c2-143">Use either the Twilio or the ASPSMS section:</span></span>


<span data-ttu-id="0b8c2-144">**Twilio:**</span><span class="sxs-lookup"><span data-stu-id="0b8c2-144">**Twilio:**</span></span>  
<span data-ttu-id="0b8c2-145">[!code-csharp[Main](2fa/sample/Web2FA/Services/MessageServices_twilio.cs)]</span><span class="sxs-lookup"><span data-stu-id="0b8c2-145">[!code-csharp[Main](2fa/sample/Web2FA/Services/MessageServices_twilio.cs)]</span></span>

<span data-ttu-id="0b8c2-146">**ASPSMS:**</span><span class="sxs-lookup"><span data-stu-id="0b8c2-146">**ASPSMS:**</span></span>  
<span data-ttu-id="0b8c2-147">[!code-csharp[Main](2fa/sample/Web2FA/Services/MessageServices_ASPSMS.cs)]</span><span class="sxs-lookup"><span data-stu-id="0b8c2-147">[!code-csharp[Main](2fa/sample/Web2FA/Services/MessageServices_ASPSMS.cs)]</span></span>

### <a name="configure-startup-to-use-smsoptions"></a><span data-ttu-id="0b8c2-148">사용 하는 시작 구성`SMSoptions`</span><span class="sxs-lookup"><span data-stu-id="0b8c2-148">Configure startup to use `SMSoptions`</span></span>

<span data-ttu-id="0b8c2-149">추가 `SMSoptions` 서비스 컨테이너에 `ConfigureServices` 에서 메서드는 *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="0b8c2-149">Add `SMSoptions` to the service container in the `ConfigureServices` method in the *Startup.cs*:</span></span>

<span data-ttu-id="0b8c2-150">[!code-csharp[Main](2fa/sample/Web2FA/Startup.cs?name=snippet1&highlight=4)]</span><span class="sxs-lookup"><span data-stu-id="0b8c2-150">[!code-csharp[Main](2fa/sample/Web2FA/Startup.cs?name=snippet1&highlight=4)]</span></span>

### <a name="enable-two-factor-authentication"></a><span data-ttu-id="0b8c2-151">2 단계 인증을 사용 하도록 설정</span><span class="sxs-lookup"><span data-stu-id="0b8c2-151">Enable two-factor authentication</span></span>

<span data-ttu-id="0b8c2-152">열기는 *Views/Manage/Index.cshtml* Razor 파일 보기 및 주석 문자 (태그 없음 주석으로 처리 하므로) 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="0b8c2-152">Open the *Views/Manage/Index.cshtml* Razor view file and remove the comment characters (so no markup is commnted out).</span></span>

## <a name="log-in-with-two-factor-authentication"></a><span data-ttu-id="0b8c2-153">2 단계 인증으로 로그인</span><span class="sxs-lookup"><span data-stu-id="0b8c2-153">Log in with two-factor authentication</span></span>

* <span data-ttu-id="0b8c2-154">응용 프로그램을 실행 하 고 새 사용자 등록</span><span class="sxs-lookup"><span data-stu-id="0b8c2-154">Run the app and register a new user</span></span>

![Microsoft Edge에서 열려 있는 웹 응용 프로그램 등록 보기](2fa/_static/login2fa1.png)

* <span data-ttu-id="0b8c2-156">탭을 활성화 하는 사용자 이름에는 `Index` 관리 컨트롤러의 동작 메서드에 합니다.</span><span class="sxs-lookup"><span data-stu-id="0b8c2-156">Tap on your user name, which activates the `Index` action method in Manage controller.</span></span> <span data-ttu-id="0b8c2-157">전화 번호를 탭 합니다 **추가** 링크 합니다.</span><span class="sxs-lookup"><span data-stu-id="0b8c2-157">Then tap the phone number **Add** link.</span></span>

![보기를 관리](2fa/_static/login2fa2.png)

* <span data-ttu-id="0b8c2-159">확인 코드를 수신 하 고 누릅니다 하 전화 번호 추가 **확인 코드를 보낼**합니다.</span><span class="sxs-lookup"><span data-stu-id="0b8c2-159">Add a phone number that will receive the verification code, and tap **Send verification code**.</span></span>

![전화 번호 페이지 추가](2fa/_static/login2fa3.png)

* <span data-ttu-id="0b8c2-161">확인 코드가 있는 문자 메시지를 받아볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0b8c2-161">You will get a text message with the verification code.</span></span> <span data-ttu-id="0b8c2-162">입력 하 고 탭 **제출**</span><span class="sxs-lookup"><span data-stu-id="0b8c2-162">Enter it and tap **Submit**</span></span>

![전화 번호 페이지 확인](2fa/_static/login2fa4.png)

<span data-ttu-id="0b8c2-164">문자 메시지를 얻지 못하면 twilio 로그 페이지를 참조 하십시오.</span><span class="sxs-lookup"><span data-stu-id="0b8c2-164">If you don't get a text message, see twilio log page.</span></span>

* <span data-ttu-id="0b8c2-165">관리 보기는 성공적으로 추가 전화 번호를 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="0b8c2-165">The Manage view shows your phone number was added successfully.</span></span>

![보기를 관리](2fa/_static/login2fa5.png)

* <span data-ttu-id="0b8c2-167">탭 **사용** 2 단계 인증을 사용 하도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="0b8c2-167">Tap **Enable** to enable two-factor authentication.</span></span>

![보기를 관리](2fa/_static/login2fa6.png)

### <a name="test-two-factor-authentication"></a><span data-ttu-id="0b8c2-169">2 단계 인증 테스트</span><span class="sxs-lookup"><span data-stu-id="0b8c2-169">Test two-factor authentication</span></span>

* <span data-ttu-id="0b8c2-170">로그 오프 합니다.</span><span class="sxs-lookup"><span data-stu-id="0b8c2-170">Log off.</span></span>

* <span data-ttu-id="0b8c2-171">로그인.</span><span class="sxs-lookup"><span data-stu-id="0b8c2-171">Log in.</span></span>

* <span data-ttu-id="0b8c2-172">2 단계 인증을 제공 해야 하므로 사용자 계정 2 단계 인증이 있었습니다.</span><span class="sxs-lookup"><span data-stu-id="0b8c2-172">The user account has enabled two-factor authentication, so you have to provide the second factor of authentication .</span></span> <span data-ttu-id="0b8c2-173">이 자습서에서는 전화 확인 사용 하도록 설정한 합니다.</span><span class="sxs-lookup"><span data-stu-id="0b8c2-173">In this tutorial you have enabled phone verification.</span></span> <span data-ttu-id="0b8c2-174">템플릿이 제공된은 두 번째 요소로 전자 메일을 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0b8c2-174">The built in templates also allow you to set up email as the second factor.</span></span> <span data-ttu-id="0b8c2-175">QR 코드와 같은 인증에 대 한 추가 두 번째 요소를 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0b8c2-175">You can set up additional second factors for authentication such as QR codes.</span></span> <span data-ttu-id="0b8c2-176">탭 **제출**합니다.</span><span class="sxs-lookup"><span data-stu-id="0b8c2-176">Tap **Submit**.</span></span>

![확인 코드 보기 보내기](2fa/_static/login2fa7.png)

* <span data-ttu-id="0b8c2-178">SMS 메시지에 코드를 입력 합니다.</span><span class="sxs-lookup"><span data-stu-id="0b8c2-178">Enter the code you get in the SMS message.</span></span>

* <span data-ttu-id="0b8c2-179">클릭 하 고 **저장이 브라우저** 확인란 있습니다 2FA를 사용 하 여 동일한 장치 및 브라우저를 사용 하는 경우 로그온 하에서 제외 됩니다.</span><span class="sxs-lookup"><span data-stu-id="0b8c2-179">Clicking on the **Remember this browser** check box will exempt you from needing to use 2FA to log on when using the same device and browser.</span></span> <span data-ttu-id="0b8c2-180">2FA를 사용 하도록 설정 하 고 클릭 하 **저장이 브라우저** 알려 강력한 2FA 보호 장치에 액세스할 수 없는 상태로 회원님의 계정에 액세스 하려고 하는 악의적인 사용자 로부터 합니다.</span><span class="sxs-lookup"><span data-stu-id="0b8c2-180">Enabling 2FA and clicking on **Remember this browser** will provide you with strong 2FA protection from malicious users trying to access your account, as long as they don't have access to your device.</span></span> <span data-ttu-id="0b8c2-181">정기적으로 사용 하는 모든 개인 장치에서이 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0b8c2-181">You can do this on any private device you regularly use.</span></span> <span data-ttu-id="0b8c2-182">설정 하 여 **저장이 브라우저**, 정기적으로 사용 하지 않는 장치에서 2FA의 강화 된 보안을 얻게 및에 자신의 장치에 2FA를 통과 하지 않아도 되는 편의성을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="0b8c2-182">By setting  **Remember this browser**, you get the added security of 2FA from devices you don't regularly use, and you get the convenience on not having to go through 2FA on your own devices.</span></span>

![보기를 확인 합니다.](2fa/_static/login2fa8.png)

## <a name="account-lockout-for-protecting-against-brute-force-attacks"></a><span data-ttu-id="0b8c2-184">무차별 암호 대입 공격 으로부터 보호 하기 위한 계정 잠금</span><span class="sxs-lookup"><span data-stu-id="0b8c2-184">Account lockout for protecting against brute force attacks</span></span>

<span data-ttu-id="0b8c2-185">계정 잠금 2FA 사용 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="0b8c2-185">We recommend you use account lockout with 2FA.</span></span> <span data-ttu-id="0b8c2-186">사용자가 로컬 계정 또는 소셜 계정) (통해 로그인 하 고 2FA에서 연결 시도가 실패할된 때마다 저장 됩니다 (기본값은 5)는 최대 시도 횟수에 도달 하면 사용자가 잠길 5 분 (잠금 시간 초과 설정할 수 있습니다 `DefaultAccountLockoutTimeSpan`).</span><span class="sxs-lookup"><span data-stu-id="0b8c2-186">Once a user logs in (through a local account or social account), each failed attempt at 2FA is stored, and if the maximum attempts (default is 5) is reached, the user is locked out for five minutes (you can set the lock out time with `DefaultAccountLockoutTimeSpan`).</span></span> <span data-ttu-id="0b8c2-187">다음에서는 10 번 실패 후 10 분 동안 잠길 수 계정을 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="0b8c2-187">The following configures Account to be locked out for 10 minutes after 10 failed attempts.</span></span>

<span data-ttu-id="0b8c2-188">[!code-csharp[Main](2fa/sample/Web2FA/Startup.cs?name=snippet2&highlight=13-17)]</span><span class="sxs-lookup"><span data-stu-id="0b8c2-188">[!code-csharp[Main](2fa/sample/Web2FA/Startup.cs?name=snippet2&highlight=13-17)]</span></span> 
