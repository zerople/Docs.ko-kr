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
# <a name="setting-up-https-for-development-in-aspnet-core"></a>ASP.NET Core에서 개발을 위한 HTTPS 설정

> [!NOTE] 
> 이 항목은 ASP.NET 코어 2.0 Preview 1에 적용 됩니다.

프로덕션 환경에서 HTTPS를 시뮬레이션 하에 개발 하는 동안 HTTPS를 사용 하도록 응용 프로그램을 구성할 수 있습니다. HTTPS를 사용 하도록 설정 해야 할 수 있지만 다양 한 id 공급자와의 통합 (같은 [Azure AD](https://azure.microsoft.com/services/active-directory) 및 [Azure AD B2C](https://azure.microsoft.com/services/active-directory-b2c/)).

<a name="iisxpress"></a>

Windows에서 이전에 설치한 Visual Studio 또는 IIS Express를 IIS Express 개발 인증서, LocalMachine 인증서 저장소에 됩니다. IIS Express 뒤에서 실행 하는 경우이 인증서를 사용 하려면 Visual Studio에서 프로젝트 속성을 업데이트할 수 있습니다.

   * 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭 하 고 선택 **속성**합니다.
   * 왼쪽된 창에서 선택 **디버그**합니다.
   * 확인 **SSL 사용**
   * SSL URL을 복사 하 고에 붙여 넣은 **앱 URL**

![디버그 탭의 웹 응용 프로그램 속성](enforcing-ssl/_static/ssl.png)

개발에 대 한를 사용할 수 있는 경우 IIS Express 개발 인증서를 사용 하거나 개발 용도로 새 인증서를 만들 수 있습니다. 개발 인증서를 구성 하는 `appsettings.Development.json` 프로덕션 환경에는 사용 되지 않도록 파일:

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

이 구성에 대 한 프로덕션 환경에서 사용 하 여 앱에는 "인증서 라는 'HTTPS' 현재 환경 (프로덕션)에 대 한 구성에서 찾을 수 없습니다" 라는 예외가 throw 됩니다. 전환 하는 [환경](xref:fundamentals/environments) 를 `Development`설정는 `ASPNETCORE_ENVIRONMENT` 환경 변수를 `Development`합니다.

IIS Express 개발 설치 되는 인증서가 없는 경우에 사용자가 직접 개발 인증서를 만들 수 있습니다. Windows에서 개발 인증서를 만들고 관리자 권한 프롬프트에서 다음 PowerShell 명령을 실행 하 여 현재 사용자에 대 한 신뢰할 수 있는 루트 저장소에 추가 합니다.

```powershell
$cert = New-SelfSignedCertificate -Subject localhost -DnsName localhost -FriendlyName "ASP.NET Core Development" -KeyUsage DigitalSignature -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1") 
Export-Certificate -Cert $cert -FilePath cert.cer
Import-Certificate -FilePath cert.cer -CertStoreLocation cert:/CurrentUser/Root
```

<a name="OpenSSL"></a>

## <a name="kestrel-on--macos-and-linux"></a>Kestrel macOS 및 Linux에서

Kestrel 원하는 IP 주소, 포트 및 인증서를 사용 하 여 끝점을 구성 하 여 HTTPS를 통해 수신 대기 하도록 구성할 수 있습니다. 인증서 구성 된 인라인 될 수 있습니다 또는 최상위 `Certificates` 섹션 및 다음 이름으로 참조 합니다.

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

MacOS 및 Linux에서 HTTPS를 사용 하 여에 대 한 자체 서명 된 인증서를 만들 수 있습니다 [OpenSSL](https://www.openssl.org/):

```bash
openssl req -new -x509 -newkey rsa:2048 -keyout localhost.key -out localhost.cer -days 365 -subj /CN=localhost
openssl pkcs12 -export -out certificate.pfx -inkey localhost.key -in localhost.cer
```

한 번의 `certificate.pfx` 파일이 생성 되었으며에 HTTPS 인증서를 구성 하 여 `appsettings.Development.json` 파일:

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

"인증서: HTTPS:Password" 구성 속성을 설정 하 여 인증서에 대 한 암호를 지정 해야 합니다. 암호를 일반 텍스트로 저장 되어야 합니다. 참조 [안전한 저장소의 앱 암호 중 개발](app-secrets.md) 인증서 암호의 적절 한 처리에 대 한 합니다.

MacOS을 수행할 수 있습니다 [키 체인에 인증서를 추가할](https://support.apple.com/kb/PH20129?locale=en_US) 및 [신뢰 설정을 변경](https://support.apple.com/kb/PH20127?locale=en_US&viewlocale=en_US) 개발 하는 동안 HTTPS에 대 한 신뢰할 수 있도록 합니다. 인증서에 키 집합에 추가 하려면 (해당 하는 `CurrentUser/My` Windows에 저장) 다음 명령을 실행 합니다.

```bash
security import certificate.pfx -k ~/Library/Keychains/login.keychain-db
```

그리고 인증서를 신뢰

```bash
security add-trusted-cert localhost.cer
```

그런 다음 다음과 같은 개발에이 인증서를 사용 하도록 앱을 구성할 수 있습니다.

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
