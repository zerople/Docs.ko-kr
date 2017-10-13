---
title: "ASP.NET Core에서 인증자 앱에 대 한 QR 코드를 생성 하도록 설정"
author: rick-anderson
description: "ASP.NET Core에서 인증자 앱에 대 한 QR 코드를 생성 하도록 설정"
keywords: "ASP.NET Core, MVC에서 QR 코드를 생성, 인증자, 2FA"
ms.author: riande
manager: wpickett
ms.date: 09/24/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/authentication/identity-enable-qrcodes
ms.openlocfilehash: a3029e68164dd91d1bc43704c5e96bd591bcae05
ms.sourcegitcommit: a73fb5161fbdbee1d2e8474205e9840a134ee2ad
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/12/2017
---
# <a name="enabling-qr-code-generation-for-authenticator-apps-in-aspnet-core"></a><span data-ttu-id="2eb27-104">ASP.NET Core에서 인증자 앱에 대 한 QR 코드를 생성 하도록 설정</span><span class="sxs-lookup"><span data-stu-id="2eb27-104">Enabling QR Code generation for authenticator apps in ASP.NET Core</span></span>

<span data-ttu-id="2eb27-105">참고:이 항목을 적용할 ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="2eb27-105">Note: This topic applies to ASP.NET Core 2.x</span></span>

<span data-ttu-id="2eb27-106">ASP.NET Core authenticator 응용 프로그램에서 개별 인증에 대 한 지원이 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2eb27-106">ASP.NET Core ships with support for authenticator applications for individual authentication.</span></span> <span data-ttu-id="2eb27-107">시간 기반 일회용 암호 알고리즘 (TOTP)를 사용 하 여 요소 인증 (2FA) 인증자 앱, 두 가지 권장 접근법 2FA에 대 한 업계 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2eb27-107">Two factor authentication (2FA) authenticator apps, using a Time-based One-time Password Algorithm (TOTP), are the industry recommended approach for 2FA.</span></span> <span data-ttu-id="2eb27-108">2FA TOTP를 사용 하 여는 SMS 2FA 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="2eb27-108">2FA using TOTP is preferred to SMS 2FA.</span></span> <span data-ttu-id="2eb27-109">Authenticator 앱에 있는 사용자는 자신의 사용자 이름과 암호를 확인 한 후에 들어가야 합니다. 6 to 8 자리 코드를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="2eb27-109">An authenticator app provides a 6 to 8 digit code which users must enter after confirming their username and password.</span></span> <span data-ttu-id="2eb27-110">일반적으로 ऍ 스마트 폰에 설치 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2eb27-110">Typically an authenticator app is installed on a smart phone.</span></span>

<span data-ttu-id="2eb27-111">ASP.NET Core 웹 응용 프로그램 템플릿 인증자를 지원 하지만 QRCode 생성에 대 한 지원을 제공 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2eb27-111">The ASP.NET Core web app templates support authenticators, but do not provide support for QRCode generation.</span></span> <span data-ttu-id="2eb27-112">QRCode 생성기 쉽게 2FA 설치.</span><span class="sxs-lookup"><span data-stu-id="2eb27-112">QRCode generators ease the setup of 2FA.</span></span> <span data-ttu-id="2eb27-113">이 문서 안내할 추가 [QR 코드](https://wikipedia.org/wiki/QR_code) 2FA 구성 페이지를 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="2eb27-113">This document will guide you through adding [QR Code](https://wikipedia.org/wiki/QR_code) generation to the 2FA configuration page.</span></span>

## <a name="adding-qr-codes-to-the-2fa-configuration-page"></a><span data-ttu-id="2eb27-114">2FA 구성 페이지에 QR 코드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2eb27-114">Adding QR Codes to the 2FA configuration page</span></span>

<span data-ttu-id="2eb27-115">이 지침은 사용 *qrcode.js* https://davidshimjs.github.io/qrcodejs/ 리포지토리에서 합니다.</span><span class="sxs-lookup"><span data-stu-id="2eb27-115">These instructions use *qrcode.js* from the https://davidshimjs.github.io/qrcodejs/ repo.</span></span>

* <span data-ttu-id="2eb27-116">다운로드는 [qrcode.js javascript 라이브러리](https://davidshimjs.github.io/qrcodejs/) 에 `wwwroot\lib` 프로젝트의 폴더에에서 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2eb27-116">Download the [qrcode.js javascript library](https://davidshimjs.github.io/qrcodejs/) to the `wwwroot\lib` folder in your project.</span></span>

* <span data-ttu-id="2eb27-117">*Pages\Account\Manage\EnableAuthenticator.cshtml* (Razor 페이지) 또는 *Views\Manage\EnableAuthenticator.cshtml* (MVC)을 찾아는 `Scripts` 파일의 끝 섹션:</span><span class="sxs-lookup"><span data-stu-id="2eb27-117">In *Pages\Account\Manage\EnableAuthenticator.cshtml* (Razor Pages) or *Views\Manage\EnableAuthenticator.cshtml* (MVC), locate the `Scripts` section at the end of the file:</span></span>

```cshtml
@section Scripts {
    @await Html.PartialAsync("_ValidationScriptsPartial")
}
```

* <span data-ttu-id="2eb27-118">업데이트는 `Scripts` 섹션에 대 한 참조를 추가 하는 `qrcodejs` 추가한 라이브러리 및 호출을 QR 코드를 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="2eb27-118">Update the `Scripts` section to add a reference to the `qrcodejs` library you added and a call to generate the QR Code.</span></span> <span data-ttu-id="2eb27-119">다음과 같이 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2eb27-119">It should look as follows:</span></span>

```cshtml
@section Scripts {
    @await Html.PartialAsync("_ValidationScriptsPartial")

    <script type="text/javascript" src="~/lib/qrcode.js"></script>
    <script type="text/javascript">
        new QRCode(document.getElementById("qrCode"),
            {
                text: "@Html.Raw(Model.AuthenticatorUri)",
                width: 150,
                height: 150
            });
    </script>
}
```

* <span data-ttu-id="2eb27-120">이러한 지침에 대 한 링크를 제공 하는 단락을 삭제 합니다.</span><span class="sxs-lookup"><span data-stu-id="2eb27-120">Delete the paragraph which links you to these instructions.</span></span>

<span data-ttu-id="2eb27-121">응용 프로그램을 실행 하 고 QR 코드 스캔 하 고 인증자 증명 코드 유효성을 검사할 수 있는지를 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="2eb27-121">Run your app and ensure that you can scan the QR code and validate the code the authenticator proves.</span></span>

## <a name="change-the-site-name-in-the-qr-code"></a><span data-ttu-id="2eb27-122">QR 코드에 사이트 이름을 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="2eb27-122">Change the site name in the QR Code</span></span>

<span data-ttu-id="2eb27-123">처음 프로젝트를 만들 때 선택한 프로젝트 이름에서 QR 코드에 사이트 이름을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="2eb27-123">The site name in the QR Code is taken from the project name you choose when initially creating your project.</span></span> <span data-ttu-id="2eb27-124">검색 하 여 변경할 수는 `GenerateQrCodeUri(string email, string unformattedKey)` 에서 메서드는 *Pages\Account\Manage\EnableAuthenticator.cshtml.cs* (Razor 페이지) 파일 또는 *Controllers\ManageController.cs* (MVC) 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="2eb27-124">You can change it by looking for the `GenerateQrCodeUri(string email, string unformattedKey)` method in the *Pages\Account\Manage\EnableAuthenticator.cshtml.cs* (Razor Pages) file or the *Controllers\ManageController.cs* (MVC) file.</span></span> 

<span data-ttu-id="2eb27-125">서식 파일에서 기본 코드의 모양은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="2eb27-125">The default code from the template looks as follows:</span></span>

```c#
private string GenerateQrCodeUri(string email, string unformattedKey)
{
    return string.Format(
        AuthenicatorUriFormat,
        _urlEncoder.Encode("Razor Pages"),
        _urlEncoder.Encode(email),
        unformattedKey);
}
```

<span data-ttu-id="2eb27-126">에 대 한 호출에서 두 번째 매개 변수 `string.Format` 은 솔루션 이름에서 가져온 사이트 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="2eb27-126">The second parameter in the call to `string.Format` is your site name, taken from your solution name.</span></span> <span data-ttu-id="2eb27-127">모든 값을 변경할 수는 있지만 URL로 인코딩된 항상 존재 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2eb27-127">It can be changed to any value, but it must always be URL encoded.</span></span>

## <a name="using-a-different-qr-code-library"></a><span data-ttu-id="2eb27-128">다른 QR 코드 라이브러리를 사용 하 여</span><span class="sxs-lookup"><span data-stu-id="2eb27-128">Using a different QR Code library</span></span>

<span data-ttu-id="2eb27-129">기본 라이브러리에 있는 QR 코드 라이브러리를 바꿀 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2eb27-129">You can replace the QR Code library with your preferred library.</span></span> <span data-ttu-id="2eb27-130">HTML을 포함 한 `qrCode` 라이브러리에 제공 하는 요소는 어떤 메커니즘으로 QR 코드를 배치할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2eb27-130">The HTML contains a `qrCode` element into which you can place a QR Code by whatever mechanism your library provides.</span></span>

<span data-ttu-id="2eb27-131">QR 코드에 대 한 올바른 형식의 URL은에서 사용할 수는 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2eb27-131">The correctly formatted URL for the QR Code is available in the:</span></span>

* <span data-ttu-id="2eb27-132">`AuthenticatorUri`모델의 속성입니다.</span><span class="sxs-lookup"><span data-stu-id="2eb27-132">`AuthenticatorUri` property of the model.</span></span>
* <span data-ttu-id="2eb27-133">`data-url`속성에는 `qrCodeData` 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="2eb27-133">`data-url` property in the `qrCodeData` element.</span></span> 

<span data-ttu-id="2eb27-134">사용 하 여 `@Html.Raw` 뷰에서 모델 속성에 액세스 (url에서 앰퍼샌드 double 인코딩할 및 QR 코드의 레이블 매개 변수는 무시 합니다. 그렇지 않은 경우).</span><span class="sxs-lookup"><span data-stu-id="2eb27-134">Use `@Html.Raw` to access the model property in a view (otherwise the ampersands in the url will be double encoded and the label parameter of the QR Code will be ignored).</span></span>

## <a name="totp-client-and-server-time-skew"></a><span data-ttu-id="2eb27-135">TOTP 클라이언트 및 서버 시간 차가</span><span class="sxs-lookup"><span data-stu-id="2eb27-135">TOTP client and server time skew</span></span>

<span data-ttu-id="2eb27-136">TOTP 인증 서버와 인증자 장치 정확한 시간을 필요에 따라 달라 집니다.</span><span class="sxs-lookup"><span data-stu-id="2eb27-136">TOTP authentication depends on both the server and authenticator device having an accurate time.</span></span> <span data-ttu-id="2eb27-137">토큰 30 초만 지속 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2eb27-137">Tokens only last for 30 seconds.</span></span> <span data-ttu-id="2eb27-138">TOTP 2FA 로그인 실패 하는 경우 서버 시간이 정확 하 고 정확한 NTP 서비스에 동기화 된 가급적 인지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="2eb27-138">If TOTP 2FA logins are failing, check that the server time is accurate, and preferably synchronized to an accurate NTP service.</span></span>
