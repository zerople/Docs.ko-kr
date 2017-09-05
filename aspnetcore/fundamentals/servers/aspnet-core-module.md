---
title: "ASP.NET Core 모듈"
author: tdykstra
description: "역방향 프록시 서버와 IIS 또는 IIS Express를 사용 하면 Kestrel 웹 서버는 IIS 모듈 ASP.NET Core 모듈 (ANCM)을 소개 합니다."
keywords: "ASP.NET Core, IIS, IIS Express에서 ASP.NET Core 모듈 UseIISIntegration"
ms.author: tdykstra
manager: wpickett
ms.date: 08/03/2017
ms.topic: article
ms.assetid: 4661af33-34c5-4d71-93a0-8c7632f43580
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/servers/aspnet-core-module
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c4124f71f30b758d82a6bf641328a8d5abf779f2
ms.sourcegitcommit: 74e22e08e3b08cb576e5184d16f4af5656c13c0c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2017
---
# <a name="introduction-to-aspnet-core-module"></a>ASP.NET Core 모듈 소개

여 [Tom Dykstra](http://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), 및 [Chris Ross](https://github.com/Tratcher) 

ASP.NET Core 모듈 (ANCM)를 사용 하면 ASP.NET Core IIS 뒤에 있는 응용 프로그램을 실행 및 IIS를 사용 하 여 어떤 것이 좋습니다 (보안, 관리 효율성, 및에서 많은 자세한)에 대 한 [Kestrel](kestrel.md) 어떤 것이 좋습니다 (빠른 않도록)에 대 한 시점과 기술에서 한 번에 모두 하면 도움이 됩니다. **ANCM은 Kestrel; 에서만 작동 WebListener와 호환 되지 (ASP.NET Core에서 1.x) 또는 (2.x)에서 HTTP.sys 합니다.** 

지원 되는 Windows 버전:

* Windows 7 및 Windows Server 2008 R2 이상

[샘플 코드 보기 또는 다운로드](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/servers/aspnet-core-module/sample)

## <a name="what-aspnet-core-module-does"></a>ASP.NET Core 모듈에서 수행 하는 작업

ANCM은 IIS 파이프라인에 후크 및 ASP.NET Core 응용 프로그램 백 엔드 트래픽을 리디렉션하는 네이티브 IIS 모듈입니다. Windows 인증과 같은 다른 대부분 모듈 여전히 실행 하는 기회를 가져옵니다. 요청에 대 한 처리기를 선택 하 고 응용 프로그램에 처리기 매핑이 정의 되어 ANCM 컨트롤에만 됩니다 *web.config* 파일입니다.

프로세스에서 실행 되는 ASP.NET Core 응용 프로그램을 IIS 작업자 프로세스에서 분리 하므로 ANCM도는 처리 관리. ANCM 첫 번째 요청 제공 하 고 충돌 했을 때 다시 시작 하는 경우 ASP.NET Core 응용 프로그램에 대 한 프로세스를 시작 합니다. 이 기본적으로 동일한 동작 클래식 ASP.NET 응용 프로그램을 실행 하는 IIS에서 프로세스 및 WAS (Windows Activation Service)에 의해 관리 됩니다.

다음은 IIS, ANCM, 및 ASP.NET Core 응용 프로그램 간의 관계를 보여 주는 다이어그램입니다.

![ASP.NET Core 모듈](aspnet-core-module/_static/ancm.png)

요청는 웹에서 제공 및 기본 포트 (80) 또는 SSL 포트 (443)에 IIS로 라우팅하는 커널 모드 Http.Sys 드라이버에 도달 합니다. ANCM 포트는 80/443 하는 응용 프로그램에 대해 구성 된 HTTP 포트에서 ASP.NET Core 응용 프로그램에 요청을 전달 합니다.

Kestrel은 ANCM에서 들어오는 트래픽을 수신 대기 합니다.  ANCM 시작 시 환경 변수를 통해 포트 지정 및 [UseIISIntegration](#call-useiisintegration) 에서 수신 하도록 서버를 구성 하는 메서드 `http://localhost:{port}`합니다. ANCM에서가 아니라 요청을 거부 하도록 추가 확인 합니다. (ANCM 지원 하지 않습니다 HTTPS 전달 되므로 IIS에서 HTTPS를 통해 수신 하는 경우에 요청은 HTTP를 통해 전달 됩니다.)

Kestrel ANCM에서 요청을 선택 하 고 그런 다음 처리 하 고 변수로 전달 하는 ASP.NET Core 미들웨어 파이프라인 밀어넣습니다 `HttpContext` 인스턴스 응용 프로그램 논리를 합니다. 응용 프로그램의 응답은 다음 IIS에서 요청을 시작한 HTTP 클라이언트에 다시 해당 되는 푸시로 다시 전달 됩니다.

ANCM에 몇 가지 다른 기능도 있습니다.

* 환경 변수를 설정합니다.
* 로그 `stdout` 파일 저장소에 출력 합니다.
* Windows 인증 토큰을 전달합니다.

## <a name="how-to-use-ancm-in-aspnet-core-apps"></a>ANCM ASP.NET Core 응용 프로그램에서 사용 하는 방법

이 섹션에서는 IIS 서버 및 ASP.NET Core 응용 프로그램 설정에 대 한 프로세스의 개요를 제공 합니다. 자세한 내용은 참조 [를 IIS에 게시](../../publishing/iis.md)합니다.

### <a name="install-ancm"></a>ANCM 설치

ASP.NET Core 모듈 설치 되어 있어야 IIS에서 서버 및 IIS Express에서 개발 컴퓨터의 합니다. 서버, ANCM에 포함 되는 [.NET 핵심 Windows Server 호스팅 번들](https://aka.ms/dotnetcore.2.0.0-windowshosting)합니다. 개발 컴퓨터에 대 한 Visual Studio 자동으로 설치 ANCM IIS 및 IIS Express에서 이미 컴퓨터에 설치 된 경우.

### <a name="install-the-iisintegration-nuget-package"></a>IISIntegration NuGet 패키지 설치

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET 2.x 핵심](#tab/aspnetcore2x)

[Microsoft.AspNetCore.Server.IISIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IISIntegration/) 패키지 ASP.NET Core metapackages에 포함 됩니다 ([Microsoft.AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore/) 및 [Microsoft.AspNetCore.All](xref:fundamentals/metapackage) ). metapackages 중 하나를 사용 하지 않는 경우 설치 `Microsoft.AspNetCore.Server.IISIntegration` 별도로 합니다. `IISIntegration` 패키지는 앱을 설정 하는 ANCM에 의해 브로드캐스팅 되는 환경 변수를 읽을 수 있는 상호 운용성 팩. 환경 변수에서 수신 하도록 포트 등의 구성 정보를 제공 합니다. 

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

응용 프로그램에서 설치 [Microsoft.AspNetCore.Server.IISIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IISIntegration/)합니다. `IISIntegration` 패키지는 앱을 설정 하는 ANCM에 의해 브로드캐스팅 되는 환경 변수를 읽을 수 있는 상호 운용성 팩. 환경 변수에서 수신 하도록 포트 등의 구성 정보를 제공 합니다. 

---

### <a name="call-useiisintegration"></a>UseIISIntegration 호출

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET 2.x 핵심](#tab/aspnetcore2x)

`UseIISIntegration` 확장 메서드를 [ `WebHostBuilder` ](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.hosting.webhostbuilder) IIS 사용 실행 하면 자동으로 호출 됩니다.

ASP.NET Core metapackages 중 하나를 사용 하지 않는 하 고 설치 하지 않은 경우는 `Microsoft.AspNetCore.Server.IISIntegration` 패키지 하면 런타임 오류가 발생 합니다. 호출 하는 경우 `UseIISIntegration` 패키지가 설치 되지 않은 경우 컴파일 시간 오류가 발생 명시적으로 합니다.

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

응용 프로그램에서 `Main` 메서드를 호출은 `UseIISIntegration` 확장 메서드를 [ `WebHostBuilder` ](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.hosting.webhostbuilder)합니다. 

[!code-csharp[](aspnet-core-module/sample/Program.cs?name=snippet_Main&highlight=12)]

---

`UseIISIntegration` 메서드가 찾습니다 ANCM 설정 하는 환경 변수 이므로 아니요 ops 발견 되지 않습니다. 이 문제는 개발 및 macOS 또는 Linux에 대 한 테스트 및 IIS를 실행 하는 서버에 배포와 같은 시나리오를 지원 합니다. MacOS 또는 Linux에서 실행 되는 동안 Kestrel 서버 역할을 웹; 하지만 응용 프로그램 IIS 환경에 배포 될 때 자동으로 사용 하 여 IIS 및 ANCM 합니다.

### <a name="ancm-port-binding-overrides-other-port-bindings"></a>포트 바인딩 ANCM 다른 포트 바인딩을 재정의합니다

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET 2.x 핵심](#tab/aspnetcore2x)

ANCM 백 엔드 프로세스에 할당 하는 동적 포트를 생성 합니다. `UseIISIntegration` 메서드는이 동적 포트를 선택 하 고 Kestrel에서 수신 하도록 구성 `http://locahost:{dynamicPort}/`합니다. 이 재정의에 대 한 호출 등의 다른 URL 구성을 `UseUrls` 또는 [Kestrel의 수신 API](xref:fundamentals/servers/kestrel?tabs=aspnetcore2x#endpoint-configuration)합니다. 따라서 호출할 필요가 없습니다 `UseUrls` 또는 Kestrel의 `Listen` API ANCM를 사용 하는 경우. 호출 하면 `UseUrls` 또는 `Listen`, Kestrel IIS 없이 앱을 실행할 때 지정 하는 포트에서 수신 합니다.

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

ANCM 백 엔드 프로세스에 할당 하는 동적 포트를 생성 합니다. `UseIISIntegration` 메서드는이 동적 포트를 선택 하 고 Kestrel에서 수신 하도록 구성 `http://locahost:{dynamicPort}/`합니다. 이 재정의에 대 한 호출 등의 다른 URL 구성을 `UseUrls`합니다. 따라서 호출할 필요가 없습니다 `UseUrls` ANCM를 사용 하는 경우. 호출 하면 `UseUrls`, Kestrel IIS 없이 앱을 실행할 때 지정 하는 포트에서 수신 합니다.

ASP.NET Core 1.0에서 호출 하는 경우 `UseUrls`, 호출 **전에** 호출 `UseIISIntegration` ANCM 구성 된 포트를 덮어쓰도록 가져오기 하지 않습니다. 이 호출 하는 순서는 ANCM 설정 보다 우선 하므로 ASP.NET Core 1.1에서는 필요 하지 않습니다 `UseUrls`합니다.

---

### <a name="configure-ancm-options-in-webconfig"></a>Web.config에서 ANCM 옵션을 구성 합니다.

ASP.NET Core 모듈에 대 한 구성에 저장 됩니다는 *Web.config* 응용 프로그램의 루트 폴더에 있는 파일입니다. 이 파일의 설정을 시작 명령 및 ASP.NET Core 응용 프로그램을 시작 하는 인수를 가리킵니다. 샘플 Web.config 코드 및 구성 옵션에 대 한 지침에 대 한 참조 [ASP.NET 핵심 모듈 구성 참조](../../hosting/aspnet-core-module.md)합니다.

### <a name="run-with-iis-express-in-development"></a>개발에서 IIS Express와 함께 실행 합니다.

IIS Express는 ASP.NET Core 템플릿에 정의 된 기본 프로필을 사용 하 여 Visual Studio에서 실행할 수 있습니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 리소스를 참조하세요.

* [이 문서에 대 한 샘플 응용 프로그램](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/servers/aspnet-core-module/sample)
* [ASP.NET Core 모듈의 소스 코드](https://github.com/aspnet/AspNetCoreModule)
* [ASP.NET Core 모듈 구성 참조](../../hosting/aspnet-core-module.md)
* [IIS에 게시](../../publishing/iis.md)
