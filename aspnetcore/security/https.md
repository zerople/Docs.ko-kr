---
title: "ASP.NET Core에서 개발을 위한 HTTPS 설정"
author: Rick-Anderson
description: "ASP.NET Core 2.0에서 개발에 대 한 HTTPS를 설정 하는 방법을 보여 줍니다."
keywords: ASP.NET Core, SSL, HTTPS
ms.author: riande
manager: wpickett
ms.date: 05/10/2017
ms.topic: article
ms.assetid: 94f2f1a4-7d46-45e2-a085-a57916e41724
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/https
ms.openlocfilehash: 7c366ffbac71152c2f29901ff12bac2962e83e3e
ms.sourcegitcommit: 9cdbfd0d670d70b9c354216aabee260c52dad5ee
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/12/2017
---
# <a name="setting-up-https-for-development-in-aspnet-core"></a><span data-ttu-id="3a836-104">ASP.NET Core에서 개발을 위한 HTTPS 설정</span><span class="sxs-lookup"><span data-stu-id="3a836-104">Setting up HTTPS for development in ASP.NET Core</span></span>

> [!NOTE] 
> <span data-ttu-id="3a836-105">이 항목은 ASP.NET 코어 2.0 Preview 1에 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a836-105">This topic applies to ASP.NET Core 2.0 Preview 1</span></span>

<span data-ttu-id="3a836-106">프로덕션 환경에서 HTTPS를 시뮬레이션 하에 개발 하는 동안 HTTPS를 사용 하도록 응용 프로그램을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3a836-106">You can configure your application to use HTTPS during development to simulate HTTPS in your production environment.</span></span> <span data-ttu-id="3a836-107">HTTPS를 사용 하도록 설정 해야 할 수 있지만 다양 한 id 공급자와의 통합 (같은 [Azure AD](https://azure.microsoft.com/services/active-directory) 및 [Azure AD B2C](https://azure.microsoft.com/services/active-directory-b2c/)).</span><span class="sxs-lookup"><span data-stu-id="3a836-107">Enabling HTTPS may be required to enable integration with various identity providers (like [Azure AD](https://azure.microsoft.com/services/active-directory) and [Azure AD B2C](https://azure.microsoft.com/services/active-directory-b2c/)).</span></span>

<a name="iisxpress"></a>

<span data-ttu-id="3a836-108">Windows에서 이전에 설치한 Visual Studio 또는 IIS Express를 IIS Express 개발 인증서, LocalMachine 인증서 저장소에 됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a836-108">On Windows if you’ve installed Visual Studio or IIS Express, the IIS Express Development Certificate will be in your LocalMachine certificate store.</span></span> <span data-ttu-id="3a836-109">IIS Express 뒤에서 실행 하는 경우이 인증서를 사용 하려면 Visual Studio에서 프로젝트 속성을 업데이트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3a836-109">You can update your project properties in Visual Studio to use this certificate when running behind IIS Express.</span></span>

   * <span data-ttu-id="3a836-110">솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭 하 고 선택 **속성**합니다.</span><span class="sxs-lookup"><span data-stu-id="3a836-110">In Solution Explorer, right-click the project and select **Properties**.</span></span>
   * <span data-ttu-id="3a836-111">왼쪽된 창에서 선택 **디버그**합니다.</span><span class="sxs-lookup"><span data-stu-id="3a836-111">On the left pane, select **Debug**.</span></span>
   * <span data-ttu-id="3a836-112">확인 **SSL 사용**</span><span class="sxs-lookup"><span data-stu-id="3a836-112">Check **Enable SSL**</span></span>
   * <span data-ttu-id="3a836-113">SSL URL을 복사 하 고에 붙여 넣은 **앱 URL**</span><span class="sxs-lookup"><span data-stu-id="3a836-113">Copy the SSL URL and paste it into the **App URL**</span></span>

![디버그 탭의 웹 응용 프로그램 속성](enforcing-ssl/_static/ssl.png)

<span data-ttu-id="3a836-115">개발에 대 한를 사용할 수 있는 경우 IIS Express 개발 인증서를 사용 하거나 개발 용도로 새 인증서를 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3a836-115">For development you can use the IIS Express Development Certificate if it is available, or create a new certificate for development purposes.</span></span> <span data-ttu-id="3a836-116">개발 인증서를 구성 하는 `appsettings.Development.json` 프로덕션 환경에는 사용 되지 않도록 파일:</span><span class="sxs-lookup"><span data-stu-id="3a836-116">The development certificate should be configured in the `appsettings.Development.json` file so that it is not used in production:</span></span>

```json
{
  "Certificates": {
    "HTTPS": {
      "Source": "Store",
      "StoreLocation": "LocalMachine",
      "StoreName": "My",
      "Subject": "CN=localhost",
      "AllowInvalid": true
    }
  }
}
```

<span data-ttu-id="3a836-117">이 구성에 대 한 프로덕션 환경에서 사용 하 여 앱에는 "인증서 라는 'HTTPS' 현재 환경 (프로덕션)에 대 한 구성에서 찾을 수 없습니다" 라는 예외가 throw 됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a836-117">An app with this configuration running in production will throw an exception saying "No certificate named 'HTTPS' found in configuration for the current environment (Production)".</span></span> <span data-ttu-id="3a836-118">전환 하는 [환경](xref:fundamentals/environments) 를 `Development`설정는 `ASPNETCORE_ENVIRONMENT` 환경 변수를 `Development`합니다.</span><span class="sxs-lookup"><span data-stu-id="3a836-118">To switch the [environment](xref:fundamentals/environments) to `Development`, set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development`.</span></span>

<span data-ttu-id="3a836-119">IIS Express 개발 설치 되는 인증서가 없는 경우에 사용자가 직접 개발 인증서를 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3a836-119">If you do not have the IIS Express Development Certificate installed, you can create a development certificate yourself.</span></span> <span data-ttu-id="3a836-120">Windows에서 개발 인증서를 만들고 관리자 권한 프롬프트에서 다음 PowerShell 명령을 실행 하 여 현재 사용자에 대 한 신뢰할 수 있는 루트 저장소에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="3a836-120">On Windows you can create a development certificate and add it to the trusted root store for the current user by running the following PowerShell commands in an elevated prompt:</span></span>

```powershell
$cert = New-SelfSignedCertificate -Subject localhost -DnsName localhost -FriendlyName "ASP.NET Core Development" -KeyUsage DigitalSignature -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1") 
Export-Certificate -Cert $cert -FilePath cert.cer
Import-Certificate -FilePath cert.cer -CertStoreLocation cert:/CurrentUser/Root
```

<a name="OpenSSL"></a>

## <a name="kestrel-on--macos-and-linux"></a><span data-ttu-id="3a836-121">Kestrel macOS 및 Linux에서</span><span class="sxs-lookup"><span data-stu-id="3a836-121">Kestrel on  macOS and Linux</span></span>

<span data-ttu-id="3a836-122">Kestrel 원하는 IP 주소, 포트 및 인증서를 사용 하 여 끝점을 구성 하 여 HTTPS를 통해 수신 대기 하도록 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3a836-122">You can  configure Kestrel to listen over HTTPS by configuring an endpoint with the desired IP address, port, and certificate.</span></span> <span data-ttu-id="3a836-123">인증서 구성 된 인라인 될 수 있습니다 또는 최상위 `Certificates` 섹션 및 다음 이름으로 참조 합니다.</span><span class="sxs-lookup"><span data-stu-id="3a836-123">The certificate can be configured inline, or in the top-level `Certificates` section and then referenced by name:</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "LocalhostHttps": {
        "Address": "127.0.0.1",
        "Port": "43434",
        "Certificate": "HTTPS"
      }
    }
  }
}

```

<span data-ttu-id="3a836-124">MacOS 및 Linux에서 HTTPS를 사용 하 여에 대 한 자체 서명 된 인증서를 만들 수 있습니다 [OpenSSL](https://www.openssl.org/):</span><span class="sxs-lookup"><span data-stu-id="3a836-124">On macOS and Linux you can create a self-signed certificate for HTTPS using [OpenSSL](https://www.openssl.org/):</span></span>

```bash
openssl req -new -x509 -newkey rsa:2048 -keyout localhost.key -out localhost.cer -days 365 -subj /CN=localhost
openssl pkcs12 -export -out certificate.pfx -inkey localhost.key -in localhost.cer
```

<span data-ttu-id="3a836-125">한 번의 `certificate.pfx` 파일이 생성 되었으며에 HTTPS 인증서를 구성 하 여 `appsettings.Development.json` 파일:</span><span class="sxs-lookup"><span data-stu-id="3a836-125">Once the `certificate.pfx` file has been generated, configure the HTTPS certificate in your `appsettings.Development.json` file:</span></span>

```json
{
  "Certificates": {
    "HTTPS": {
      "Source": "File",
      "Path": "certificate.pfx"
    }
  }
}
```

<span data-ttu-id="3a836-126">"인증서: HTTPS:Password" 구성 속성을 설정 하 여 인증서에 대 한 암호를 지정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="3a836-126">You will also need to specify the passphrase for the certificate by setting the “Certificates:HTTPS:Password” config property.</span></span> <span data-ttu-id="3a836-127">암호를 일반 텍스트로 저장 되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="3a836-127">Passwords should not be stored in plain text.</span></span> <span data-ttu-id="3a836-128">참조 [안전한 저장소의 앱 암호 중 개발](app-secrets.md) 인증서 암호의 적절 한 처리에 대 한 합니다.</span><span class="sxs-lookup"><span data-stu-id="3a836-128">See [Safe Storage of App Secrets During Development](app-secrets.md) for appropriate handling of the certificate passphrase.</span></span>

<span data-ttu-id="3a836-129">MacOS을 수행할 수 있습니다 [키 체인에 인증서를 추가할](https://support.apple.com/kb/PH20129?locale=en_US) 및 [신뢰 설정을 변경](https://support.apple.com/kb/PH20127?locale=en_US&viewlocale=en_US) 개발 하는 동안 HTTPS에 대 한 신뢰할 수 있도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="3a836-129">On macOS you can [add the certificate to your keychain](https://support.apple.com/kb/PH20129?locale=en_US) and [change its trust settings](https://support.apple.com/kb/PH20127?locale=en_US&viewlocale=en_US) so that it is trusted for HTTPS during development.</span></span> <span data-ttu-id="3a836-130">인증서에 키 집합에 추가 하려면 (해당 하는 `CurrentUser/My` Windows에 저장) 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="3a836-130">To add the certificate to your keychain (the equivalent of the `CurrentUser/My` store on Windows) run the following command:</span></span>

```bash
security import certificate.pfx -k ~/Library/Keychains/login.keychain-db
```

<span data-ttu-id="3a836-131">그리고 인증서를 신뢰</span><span class="sxs-lookup"><span data-stu-id="3a836-131">And then to trust the certificate:</span></span>

```bash
security add-trusted-cert localhost.cer
```

<span data-ttu-id="3a836-132">그런 다음 다음과 같은 개발에이 인증서를 사용 하도록 앱을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3a836-132">You can then configure your app to use this certificate in development like this:</span></span>

```json
{
  "Certificates": {
    "HTTPS": {
      "Source": "Store",
      "StoreLocation": "CurrentUser",
      "StoreName": "My",
      "Subject": "CN=localhost",
      "AllowInvalid": true
    }
  }
}
```
