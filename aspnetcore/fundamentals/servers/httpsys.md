---
title: "ASP.NET Core 웹 서버 구현이 HTTP.sys"
author: rick-anderson
description: "Windows에서 ASP.NET Core에 대 한 웹 서버 HTTP.sys를 소개합니다. IIS 없이 인터넷에 직접 연결에 사용할 수 있는 Kestrel 하는 대신을 HTTP.sys는 Http.Sys 커널 모드 드라이버를 기반 합니다."
keywords: "ASP.NET Core,HttpSys,HTTP.sys,HttpListener,url 접두사, SSL"
ms.author: riande
manager: wpickett
ms.date: 08/07/2017
ms.topic: article
ms.assetid: 0a7286e4-6428-424e-b5c4-5c98815cf61c
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/servers/httpsys
ms.openlocfilehash: d3f9eb4943ed62b674d6bb2ab1b275b0a3c02343
ms.sourcegitcommit: 732cd2684246e49e796836596643a8d37e20c46d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2017
---
# <a name="httpsys-web-server-implementation-in-aspnet-core"></a>ASP.NET Core 웹 서버 구현이 HTTP.sys

여 [Tom Dykstra](https://github.com/tdykstra) 및 [Chris Ross](https://github.com/Tratcher)

> [!NOTE]
> 이 항목에는 이상 ASP.NET 코어 2.0에만 적용 됩니다. 이전 버전의 ASP.NET Core에서는 HTTP.sys 이름은 [WebListener](xref:fundamentals/servers/weblistener)합니다.

HTTP.sys는는 [ASP.NET Core 웹 서버로](index.md) windows를 실행 하 합니다. 상에 구축 되어는 [Http.Sys 커널 모드 드라이버](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx)합니다. HTTP.sys는 하는 대신 [Kestrel](kestrel.md) Kestel 하지 않는 일부 기능을 제공 하는 합니다. **HTTP.sys와 호환 되지 않습니다 IIS 또는 IIS Express를 사용할 수 없습니다는 [ASP.NET Core 모듈](aspnet-core-module.md)합니다.**

HTTP.sys는 다음과 같은 기능을 지원합니다.

- [Windows 인증](xref:security/authentication/windowsauth)
- 포트 공유
- SNI https /
- HTTP/2 tls (Windows 10)
- 직접 파일 전송
- 응답 캐시
- Websocket (Windows 8)

지원 되는 Windows 버전:

- Windows 7 및 Windows Server 2008 R2 이상

[보거나 다운로드 샘플 코드](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/sample) ([다운로드 하는 방법을](xref:tutorials/index#how-to-download-a-sample))

## <a name="when-to-use-httpsys"></a>HTTP.sys를 사용 하는 경우

HTTP.sys는 IIS를 사용 하지 않고 인터넷에 직접 서버를 노출 해야 하는 배포 하는 데 유용 합니다.

![HTTP.sys는 인터넷과 직접 통신합니다.](httpsys/_static/httpsys-to-internet.png)

Http.Sys를 기반으로 하기 때문에 HTTP.sys 공격 으로부터 보호에 대 한 역방향 프록시 서버를 필요 하지 않습니다. Http.Sys는 많은 유형의 공격 으로부터 보호 하 고 견고성, 보안 및의 모든 기능을 갖춘 웹 서버 확장성을 제공 하는 완성도 높은 기술입니다. 자체 IIS는 HTTP 수신기 Http.Sys 기반으로 실행 됩니다. 

HTTP.sys는 내부 배포에 대 한 좋은 선택 기능에서에서 사용할 수 없는 Kestrel, 예: Windows 인증을 해야 합니다.

![HTTP.sys는 내부 네트워크와 직접 통신합니다.](httpsys/_static/httpsys-to-internal.png)

## <a name="how-to-use-httpsys"></a>HTTP.sys를 사용 하는 방법

호스트 OS 및 ASP.NET Core 응용 프로그램에 대 한 설치 작업의 개요는 다음과 같습니다.

### <a name="configure-windows-server"></a>Windows Server를 구성 합니다.

* 버전의 응용 프로그램에 필요한와 같은.NET 설치 [.NET Core](https://www.microsoft.com/net/download/core) 또는 [.NET Framework](https://www.microsoft.com/net/download/framework)합니다.

* HTTP.sys에 바인딩 및 SSL 인증서를 설정에 대 한 URL 접두사를 미리 등록

   Windows의 URL 접두사를 미리 등록 하지 않는 경우 관리자 권한으로 응용 프로그램을 실행 해야 합니다. 유일한 예외는 HTTP (HTTPS)을 사용 하 여 포트 번호를 1024; 개를 사용 하는 localhost에 바인딩할 경우 이 경우 관리자 권한이 필요 없습니다.

   자세한 내용은 참조 [접두사 __'asverify'__ 및 SSL을 구성 하는 방법](#preregister-url-prefixes-and-configure-ssl) 이 문서의 뒷부분에 나오는 합니다.

* HTTP.sys에 도달 하도록 허용 하려면 방화벽 포트를 엽니다.

   사용할 수 있습니다 *netsh.exe* 또는 [PowerShell cmdlet](https://technet.microsoft.com/library/jj554906)합니다.

또한 [Http.Sys 레지스트리 설정](https://support.microsoft.com/kb/820129)합니다.

### <a name="configure-your-aspnet-core-application-to-use-httpsys"></a>HTTP.sys를 사용 하도록 ASP.NET Core 응용 프로그램 구성

* 사용 하는 경우 필요 없는 패키지 설치는 [Microsoft.AspNetCore.All](xref:fundamentals/metapackage) metapackage 합니다. [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/) 패키지는 metapackage에 포함 됩니다.

* 호출 된 `UseHttpSys` 확장 메서드를 `WebHostBuilder` 에 프로그램 `Main` 메서드를 지정 하 [HTTP.sys 옵션](https://github.com/aspnet/HttpSysServer/blob/rel/2.0.0/src/Microsoft.AspNetCore.Server.HttpSys/HttpSysOptions.cs) 다음 예제와 같이 필요한:

  [!code-csharp[](httpsys/sample/Program.cs?name=snippet_Main&highlight=11-19)]

### <a name="configure-httpsys-options"></a>HTTP.sys 옵션 구성

다음은 HTTP.sys 설정 및 구성할 수 있는 제한의 일부입니다.

**최대 클라이언트 연결**

다음 코드는 전체 응용 프로그램에 대 한 동시에 열린 TCP 연결의 최대 수를 설정할 수 있습니다 *Program.cs*:

[!code-csharp[](httpsys/sample/Program.cs?name=snippet_Options&highlight=5)]

연결의 최대 수는 기본적으로 무제한 (null).

**최대 요청 본문 크기**

기본 최대 요청 본문 크기는 약 28.6 m B 인 30,000,000 바이트입니다.

사용 하는 ASP.NET Core MVC 응용 프로그램에 대 한도 무시 하는 권장된 방법은 [RequestSizeLimit](https://github.com/aspnet/Mvc/blob/rel/2.0.0/src/Microsoft.AspNetCore.Mvc.Core/RequestSizeLimitAttribute.cs) 작업 메서드에 특성:

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

전체 응용 프로그램을 모든 요청에 대 한 제약 조건을 구성 하는 방법을 보여 주는 예제는 다음과 같습니다.

[!code-csharp[](httpsys/sample/Program.cs?name=snippet_Options&highlight=6)]

특정 요청에 대 한 설정을 재정의할 수 있습니다 *Startup.cs*:

[!code-csharp[](httpsys/sample/Startup.cs?name=snippet_Configure&highlight=9-10)]
 
응용 프로그램 요청 읽기 시작 된 후 요청에 대 한 제한을 구성 하려고 하면 예외가 throw 됩니다. `IsReadOnly` 를 알려 주는 속성 경우는 `MaxRequestBodySize` 제한을 구성 하려면에 너무 늦었습니다 의미 속성은 읽기 전용 상태에서입니다.

다른 HTTP.sys 옵션에 대 한 정보를 참조 하십시오. [HttpSysOptions](https://github.com/aspnet/HttpSysServer/blob/rel/2.0.0/src/Microsoft.AspNetCore.Server.HttpSys/HttpSysOptions.cs)합니다. 

### <a name="configure-urls-and-ports-to-listen-on"></a>Url 및 포트에서 수신 하도록 구성 

기본적으로 ASP.NET Core을 바인딩합니다 `http://localhost:5000`합니다. URL 접두사와 포트를 구성 하려면 사용할 수 있습니다는 `UseUrls` 확장 메서드는 `urls` 명령줄 인수를 ASPNETCORE_URLS 환경 변수 또는 `UrlPrefixes` 속성 [HttpSysOptions](https://github.com/aspnet/HttpSysServer/blob/rel/2.0.0/src/Microsoft.AspNetCore.Server.HttpSys/HttpSysOptions.cs)합니다. 다음 코드 예제에서는 `UrlPrefixes`합니다.

[!code-csharp[](httpsys/sample/Program.cs?name=snippet_Main&highlight=17)]

경우의 이점은 `UrlPrefixes` 는 수를 가져올 오류 메시지가 즉시 서식이 잘못 지정 된 접두사를 추가 하려고 합니다. 경우의 이점은 `UseUrls` (공유 하면 `urls` 및 ASPNETCORE_URLS) 보다 쉽게 Kestrel 고 HTTP.sys 파일 간에 전환할 수는 있습니다.

둘 다 사용 하는 경우 `UseUrls` (또는 `urls` 또는 ASPNETCORE_URLS) 및 `UrlPrefixes`에서 설정을 `UrlPrefixes` 재정의 `UseUrls`합니다. 자세한 내용은 참조 [호스팅](xref:fundamentals/hosting)합니다.

HTTP.sys를 사용 하는 [HTTP 서버 API UrlPrefix 문자열 형식은](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)합니다.

> [!NOTE]
> 에 동일한 접두사 문자열을 지정 해야 `UseUrls` 또는 `UrlPrefixes` 서버의 __'asverify'__입니다. 

### <a name="dont-use-iis"></a>IIS를 사용 하지 마십시오

응용 프로그램은 IIS 또는 IIS Express를 실행 하도록 구성 되지 않았습니다 있는지 확인 합니다.

Visual Studio에서 기본 실행 프로필은 IIS Express 합니다. 콘솔 응용 프로그램 프로젝트를 실행 하려면 다음 스크린 샷에 표시 된 것 처럼 선택한 프로필까지 변경 수동으로.

![콘솔 응용 프로그램 프로필 선택](httpsys/_static/vs-choose-profile.png)

## <a name="preregister-url-prefixes-and-configure-ssl"></a>URL 접두사 __'asverify'__ 및 SSL 구성

HTTP.sys와 IIS 기본 Http.Sys 커널 모드 드라이버 요청을 수신 하 고 초기 처리를 수행 합니다. Iis에서 관리 UI 제공 모든 항목을 구성 하는 상대적으로 쉬운 방법이 합니다. 그러나 Http.Sys를 직접 구성 해야 합니다. 즉 수행 하는 데 기본 제공 도구 *netsh.exe*합니다. 

와 *netsh.exe* URL 접두사를 예약 하 고 SSL 인증서를 할당할 수 있습니다. 이 도구는 관리 권한이 필요합니다.

다음 예제에서는 포트 80 및 443에 대 한 URL 접두사를 예약 하는 데 필요한 최소:

```console
netsh http add urlacl url=http://+:80/ user=Users
netsh http add urlacl url=https://+:443/ user=Users
```

다음 예제에는 SSL 인증서를 할당 하는 방법을 보여 줍니다.

```console
netsh http add sslcert ipport=0.0.0.0:443 certhash=MyCertHash_Here appid={00000000-0000-0000-0000-000000000000}"
```

여기에 대 한 참조 설명서는 *netsh.exe*:

* [Netsh 명령에 대 한 하이퍼텍스트 전송 프로토콜 (HTTP)](https://technet.microsoft.com/library/cc725882.aspx)
* [UrlPrefix 문자열](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)

다음 리소스는 여러 시나리오에 대 한 자세한 지침을 제공 합니다. HttpListener를 참조 하는 문서 Http.Sys를 기반으로 둘 다으로 HTTP.sys에 동일 하 게 적용 됩니다.

* [방법: SSL 인증서로 포트 구성](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/how-to-configure-a-port-with-an-ssl-certificate)
* [HTTPS 통신-HttpListener 기반 호스팅 및 클라이언트 인증](http://sunshaking.blogspot.com/2012/11/https-communication-httplistener-based.html) 이 제 3 자 블로그 비교적 오래 된 하지만 유용한 정보를 아직 있습니다.
* [방법: 연습을 사용 하 여 HttpListener 또는 Http 서버 비관리 코드 (c + +)는 SSL 단순 서버로](https://blogs.msdn.microsoft.com/jpsanders/2009/09/29/how-to-walkthrough-using-httplistener-or-http-server-unmanaged-code-c-as-an-ssl-simple-server/) 너무 유용한 정보를 담은 한 오래 된 블로그입니다.

다음은 보다 사용 하기 쉽게 될 수 있는 몇 가지 타사 도구는 *netsh.exe* 명령줄입니다. 제공 되거나 Microsoft에서 보증 되지 이러한 합니다. 도구는 관리자 권한으로 실행 기본적으로 이후 *netsh.exe* 자체 하려면 관리자 권한이 필요 합니다.

* [http.sys 관리자](http://httpsysmanager.codeplex.com/) 목록에 대 한 UI를 제공 하 고 예약, 접두사 및 인증서 신뢰 목록이 SSL 인증서 및 옵션을 구성 합니다. 
* [HttpConfig](http://www.stevestechspot.com/ABetterHttpcfg.aspx) 나열 하거나 SSL 인증서와 URL 접두사를 구성할 수 있습니다. UI 관리자 http.sys 보다 성능이 우수 하 고 몇 가지 추가 구성 옵션을 노출 하지만 그렇지 않으면 유사한 기능을 제공 합니다. 것 새 인증서 신뢰 목록 (CTL)를 만들 수는 없지만 기존을 할당할 수 있습니다.

[!INCLUDE[How to make an SSL cert](../../includes/make-ssl-cert.md)]

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 리소스를 참조하세요.

* [이 문서에 대 한 샘플 응용 프로그램](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/sample)
* [HTTP.sys 소스 코드](https://github.com/aspnet/HttpSysServer/)
* [호스팅](xref:fundamentals/hosting)
