---
title: "ASP.NET 핵심 모듈 | Microsoft 문서"
author: tdykstra
description: "역방향 프록시 서버와 IIS 또는 IIS Express를 사용 하는 Kestrel 웹 서버 수 있게 해 주는 IIS 모듈은 ASP.NET 핵심 모듈 (ANCM)를 소개 합니다."
keywords: "ASP.NET Core, IIS, IIS Express, ASP.NET 핵심 모듈 UseIISIntegration"
ms.author: tdykstra
manager: wpickett
ms.date: 10/27/2016
ms.topic: article
ms.assetid: 4661af33-34c5-4d71-93a0-8c7632f43580
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/servers/aspnet-core-module
ms.custom: H1Hack27Feb2017
translationtype: Machine Translation
ms.sourcegitcommit: 010b730d2716f9f536fef889bc2f767afb648ef4
ms.openlocfilehash: ff671e7c86d03f80de0fd32d834e4f5459cb087e
ms.lasthandoff: 03/23/2017

---
# <a name="introduction-to-aspnet-core-module"></a>ASP.NET 핵심 모듈 소개

여 [Tom Dykstra](http://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), 및 [Chris Ross](https://github.com/Tratcher) 

ASP.NET 핵심 모듈 (ANCM)를 사용 하면 ASP.NET 핵심 뒤에 IIS에서 응용 프로그램 실행에 어떤 것이 좋습니다 (보안, 관리 효율성, 그리고에 많은 더 많은) IIS 사용 및 사용 하 여 [Kestrel](kestrel.md) 무엇이 (빠른 않도록), 능숙 하 게 하는 것에 대 한 그리고 한 번에 두 기술을 모두에서 혜택을 받습니다. **ANCM은 Kestrel; 에서만 작동 호환 되지 [WebListener](weblistener.md)합니다.** 

지원 되는 Windows 버전:

* Windows 7 및 Windows Server 2008 R2 이상

[샘플 코드 보기 또는 다운로드](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/servers/aspnet-core-module/sample)

## <a name="what-aspnet-core-module-does"></a>ASP.NET 핵심 모듈의 용도

ANCM은 IIS 파이프라인에 후크하고 ASP.NET 핵심 응용 프로그램 백 엔드에 트래픽을 리디렉션하는 네이티브 IIS 모듈입니다. Windows 인증과 같은 다른 대부분 모듈에는 여전히 실행 되려면을 가져옵니다. ANCM 시간이 걸리며 제어 요청에 대해 처리기를 선택 하 고 응용 프로그램에 처리기 매핑이 정의 되어 *web.config* 파일입니다.

프로세스에서 실행 하는 ASP.NET 핵심 응용 프로그램을 IIS 작업자 프로세스에서 분리 하므로 ANCM 또한 않습니다 프로세스 관리 합니다. ANCM 제공 하 고 충돌 했을 때 다시 시작 하는 첫 번째 요청 하는 경우 ASP.NET 핵심 응용 프로그램의 프로세스를 시작 합니다. 이 동작은 기본적으로 동일한 기존 ASP.NET 응용 프로그램으로 실행 하는 IIS에서 프로세스 및 WAS (Windows Activation Service)에 의해 관리 됩니다.

다음은 IIS, ANCM, 및 ASP.NET 핵심 응용 프로그램 간의 관계를 보여 주는 다이어그램입니다.

![ASP.NET 핵심 모듈](aspnet-core-module/_static/ancm.png)

요청 웹에서 제공 하 고 기본 포트 (80) 또는 SSL 포트 (443)에 IIS로 라우팅하는 커널 모드 Http.Sys 드라이버를 눌러 합니다. 그런 다음 요청은 응용 프로그램 포트는 80/443에 대해 구성 된 HTTP 포트에서 ASP.NET 핵심 응용 프로그램에 전달 됩니다. Kestrel은 요청을 선택 하 고 다음 해당 요청을 처리 하 고로 전달 ASP.NET 핵심 미들웨어 파이프라인에 푸시하는 `HttpContext` 인스턴스 응용 프로그램 논리를 합니다. 응용 프로그램의 응답은 다음 IIS에 HTTP 요청을 시작한 클라이언트에 다시는 푸시 보기로 다시 전달 됩니다.

ANCM에 몇 가지 다른 기능도 있습니다.

* 환경 변수를 설정합니다.
* 로그 `stdout` 파일 저장소에 출력 합니다.
* Windows 인증 토큰을 전달합니다.

## <a name="how-to-use-ancm-in-aspnet-core-apps"></a>ANCM ASP.NET 핵심 응용 프로그램에서 사용 하는 방법

이 섹션에서는 IIS 서버와 ASP.NET 핵심 응용 프로그램 설정에 대 한 프로세스의 개요를 제공 합니다. 자세한 지침은 참조 [를 IIS에 게시](../../publishing/iis.md)합니다.

### <a name="install-ancm"></a>ANCM 설치

ASP.NET 핵심 모듈 이미 설치 되어 IIS에서 서버에 IIS Express에서 개발 컴퓨터에 있습니다. 서버, ANCM에 포함 되는 [ASP.NET 핵심 서버 호스팅 번들](https://go.microsoft.com/fwlink/?linkid=837808)합니다. 개발 컴퓨터에 대 한 Visual Studio 자동으로 설치 ANCM IIS 및 IIS Express에서 이미 컴퓨터에 설치 되어 있는 경우.

### <a name="install-the-iisintegration-nuget-package"></a>IISIntegration NuGet 패키지 설치

응용 프로그램에서 설치 [Microsoft.AspNetCore.Server.IISIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IISIntegration/)합니다. 이것이 ANCM 앱 설정에 의해 브로드캐스팅 되는 환경 변수를 읽고 하는 상호 운용성 팩입니다. 환경 변수 예: 포트에서 수신 하도록 구성 정보를 제공 합니다. 

### <a name="call-useiisintegration"></a>UseIISIntegration를 호출 합니다.

응용 프로그램의 `Main` 메서드를 호출의 `UseIISIntegration` 대 한 확장 메서드 [ `WebHostBuilder` ](http://docs.asp.net/projects/api/en/latest/autoapi/Microsoft/AspNetCore/Hosting/WebHostBuilder/index.html#Microsoft.AspNetCore.Hosting.WebHostBuilder.md)합니다. 

[!code-csharp[](aspnet-core-module/sample/Program.cs?name=snippet_Main&highlight=12)]

`UseIISIntegration` 메서드 ANCM 설정 하는 환경 변수를 찾아 없습니다은 검색 하는 경우. 이 이와 같은 시나리오를 용이 하 게 [개발 및 MacOS에서 테스트 하 고 IIS를 실행 하는 서버에 배포](../../tutorials/your-first-mac-aspnet.md)합니다.  Mac에서 실행 되는 동안 Kestrel 서버 역할을 하는 웹 응용 프로그램 IIS 환경에 배포 될 때 자동으로 연결 ANCM 및 IIS에 있지만

### <a name="dont-call-useurls"></a>UseUrls를 호출 하지 마십시오

ANCM 백 엔드 프로세스에 할당 하는 동적 포트를 생성 합니다. `IWebHostBuilder.UseIISIntegration`이 동적 포트를 선택 하 고 Kestrel에서 수신 하도록 구성 `http://locahost:{dynamicPort}/`합니다. 에 대 한 호출 같은 다른 URL 구성을 덮어씁니다 `IWebHostBuilder.UseUrls`합니다. 따라서 호출할 필요가 없습니다 `UseUrls` ANCM를 사용 하는 경우. 기본 포트 번호에서 수신 하는 IIS 없이 앱을 실행 하면 `http://localhost:5000`합니다.

IIS 없이 앱을 실행 하는 경우를 호출할 수에 대 한 포트 번호를 설정 해야 하는 경우 `UseURLs`합니다.  IIS에 게 제공 하는 포트 번호 없이 실행 하는 경우 `UseUrls` 사항을 적용 하기 때문에 `IISIntegration` 아무 작업도 하지 것입니다. 하지만 ANCM 지정 된 포트 번호를 재정의할에 관계 없이 전달 되는 IIS를 실행 하면 `UseUrls`합니다.

ASP.NET 핵심 1.0에서 호출 하는 경우 `UseUrls`, 그렇게 **전에** 호출 `IISIntegration` ANCM 구성 된 포트를 덮어쓰도록 가져오기 하지 않습니다. 이 호출 순서가 ANCM 설정은 항상 덮어씁니다 때문에 ASP.NET 핵심 1.1에서는 필요 하지 않은 `UseUrls`합니다.

### <a name="configure-ancm-options-in-webconfig"></a>Web.config의 ANCM 옵션 구성

ASP.NET 핵심 모듈에 대 한 구성에 저장 되는 *Web.config* 응용 프로그램의 루트 폴더에 있는 파일입니다. 이 파일의 설정은 시작 명령 및 ASP.NET 핵심 응용 프로그램을 시작 하는 인수를 가리킵니다. 샘플 Web.config 코드 및 구성 옵션에 대 한 지침을 참조 [ASP.NET 핵심 모듈 구성 참조](../../hosting/aspnet-core-module.md)합니다.

### <a name="run-with-iis-express-in-development"></a>개발에는 IIS Express로 실행

ASP.NET Core 템플릿에 의해 정의 된 기본 프로필을 사용 하 여 Visual Studio에서 IIS Express를 실행할 수 있습니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 리소스를 참조하세요.

* [이 문서에 대 한 샘플 응용 프로그램](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/servers/aspnet-core-module/sample)
* [ASP.NET 핵심 모듈의 소스 코드](https://github.com/aspnet/AspNetCoreModule)
* [ASP.NET 핵심 모듈 구성 참조](../../hosting/aspnet-core-module.md)
* [IIS에 게시](../../publishing/iis.md)

