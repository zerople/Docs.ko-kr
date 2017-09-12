---
title: "ASP.NET Core 웹 서버 구현이 WebListener"
author: rick-anderson
description: "Windows에서 ASP.NET Core 웹 서버로 WebListener를 소개합니다. Http.Sys 커널 모드 드라이버 기술을 기반으로 한 WebListener는 IIS가 없이 인터넷에 직접 연결에 사용할 수 있는 Kestrel 하지 않아도 됩니다."
keywords: "ASP.NET Core, WebListener, HttpListener, SSL url 접두사"
ms.author: riande
manager: wpickett
ms.date: 08/07/2017
ms.topic: article
ms.assetid: 0a7286e4-6428-424e-b5c4-5c98815cf61c
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/servers/weblistener
ms.openlocfilehash: 93e8b99e7fbac88aabd347c077d923214ba7aebe
ms.sourcegitcommit: 9cdbfd0d670d70b9c354216aabee260c52dad5ee
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/12/2017
---
# <a name="weblistener-web-server-implementation-in-aspnet-core"></a>ASP.NET Core 웹 서버 구현이 WebListener

여 [Tom Dykstra](https://github.com/tdykstra) 및 [Chris Ross](https://github.com/Tratcher)

> [!NOTE]
> 이 항목은 ASP.NET Core에만 적용 됩니다. 1.x 합니다. ASP.NET Core 2.0 WebListener 이름은 [HTTP.sys](httpsys.md)합니다.

WebListener는는 [ASP.NET Core 웹 서버로](index.md) windows를 실행 하 합니다. 상에 구축 되어는 [Http.Sys 커널 모드 드라이버](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx)합니다. WebListener 하지 않아도 됩니다 [Kestrel](kestrel.md) 역방향 프록시 서버와 IIS에 의존 하지 않고 인터넷에 직접 연결에 사용할 수 있습니다. 사실, **WebListener와 호환 되지 않습니다 IIS 또는 IIS Express를 사용할 수 없습니다는 [ASP.NET Core 모듈](aspnet-core-module.md)합니다.**

통해.NET Core 또는.NET Framework 응용 프로그램에서 직접 사용할 수 있지만 WebListener ASP.NET Core에 대 한 개발 된는 [Microsoft.Net.Http.Server](https://www.nuget.org/packages/Microsoft.Net.Http.Server/) NuGet 패키지 합니다.

WebListener는 다음과 같은 기능을 지원합니다.

- [Windows 인증](xref:security/authentication/windowsauth)
- 포트 공유
- SNI https /
- HTTP/2 tls (Windows 10)
- 직접 파일 전송
- 응답 캐시
- Websocket (Windows 8)

지원 되는 Windows 버전:

- Windows 7 및 Windows Server 2008 R2 이상

[샘플 코드 보기 또는 다운로드](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/servers/weblistener/sample)

## <a name="when-to-use-weblistener"></a>WebListener를 사용 하는 경우

WebListener는 IIS를 사용 하지 않고 인터넷에 직접 서버를 노출 해야 하는 배포에 유용 합니다.

![WebListener는 인터넷과 직접 통신합니다.](weblistener/_static/weblistener-to-internet.png)

Http.Sys를 기반으로 하기 때문에 WebListener 공격 으로부터 보호에 대 한 역방향 프록시 서버를 필요 하지 않습니다. Http.Sys는 많은 유형의 공격 으로부터 보호 하 고 견고성, 보안 및의 모든 기능을 갖춘 웹 서버 확장성을 제공 하는 완성도 높은 기술입니다. 자체 IIS는 HTTP 수신기 Http.Sys 기반으로 실행 됩니다. 

WebListener는 Kestrel를 사용 하 여 가져올 수 없습니다 제공 하는 기능 중 하나 필요할 때 내부 배포에 대 한 적합 한 선택 이기도 합니다.

![WebListener는 내부 네트워크와 직접 통신합니다.](weblistener/_static/weblistener-to-internal.png)

## <a name="how-to-use-weblistener"></a>WebListener를 사용 하는 방법

호스트 OS 및 ASP.NET Core 응용 프로그램에 대 한 설치 작업의 개요는 다음과 같습니다.

### <a name="configure-windows-server"></a>Windows Server를 구성 합니다.

* 버전의 응용 프로그램에 필요한와 같은.NET 설치 [.NET Core](https://download.microsoft.com/download/0/A/3/0A372822-205D-4A86-BFA7-084D2CBE9EDF/DotNetCore.1.0.1-SDK.1.0.0.Preview2-003133-x64.exe) 또는.NET Framework 4.5.1입니다.

* URL 접두사에 WebListener, 바인딩 및 SSL 인증서를 설정 하려면 미리 등록

   Windows의 URL 접두사를 미리 등록 하지 않는 경우 관리자 권한으로 응용 프로그램을 실행 해야 합니다. 유일한 예외는 HTTP (HTTPS)을 사용 하 여 포트 번호를 1024; 개를 사용 하는 localhost에 바인딩할 경우 이 경우 관리자 권한이 필요 없습니다.

   자세한 내용은 참조 [접두사 __'asverify'__ 및 SSL을 구성 하는 방법](#preregister-url-prefixes-and-configure-ssl) 이 문서의 뒷부분에 나오는 합니다.

* WebListener 도달 하는 트래픽을 허용 하도록 방화벽 포트를 엽니다.

   Netsh.exe를 사용할 수 있습니다 또는 [PowerShell cmdlet](https://technet.microsoft.com/library/jj554906)합니다.

또한 [Http.Sys 레지스트리 설정](https://support.microsoft.com/kb/820129)합니다.

### <a name="configure-your-aspnet-core-application"></a>ASP.NET Core 응용 프로그램 구성

* NuGet 패키지 설치 [Microsoft.AspNetCore.Server.WebListener](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.WebListener/)합니다. 도 설치 [Microsoft.Net.Http.Server](https://www.nuget.org/packages/Microsoft.Net.Http.Server/) 종속성으로 있습니다.

* 호출 된 [ `UseWebListener` ](https://docs.microsoft.com/aspnet/core/api) 확장 메서드를 [WebHostBuilder](https://docs.microsoft.com/aspnet/core/api) 에 프로그램 `Main` 모든 WebListener를 지정 하는 메서드를 [옵션](https://github.com/aspnet/HttpSysServer/blob/rel/1.1.2/src/Microsoft.AspNetCore.Server.WebListener/WebListenerOptions.cs) 및 [ 설정](https://github.com/aspnet/HttpSysServer/blob/rel/1.1.2/src/Microsoft.Net.Http.Server/WebListenerSettings.cs) 다음 예제와 같이 필요한:

  [!code-csharp[](weblistener/sample/Program.cs?name=snippet_Main&highlight=13-17)]

* Url 및 포트에서 수신 하도록 구성 

  기본적으로 ASP.NET Core을 바인딩합니다 `http://localhost:5000`합니다. URL 접두사와 포트를 구성 하려면 사용할 수 있습니다는 `UseURLs` 확장 메서드는 `urls` 명령줄 인수 또는 ASP.NET Core 구성 시스템입니다. 자세한 내용은 참조 [호스팅](../../fundamentals/hosting.md)합니다.

  수신기 사용 하 여 웹에서 [Http.Sys 접두사 문자열 형식은](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)합니다. WebListener 관련이 없는 접두사 문자열 형식 요구 사항이 있습니다.

  > [!NOTE]
  > 동일한 접두사 문자열을 지정 해야 `UseUrls` 하는 서버에서 미리 등록 합니다. 

* 응용 프로그램 IIS 또는 IIS Express를 실행 하도록 구성 되지 않은 있는지 확인 합니다.

  Visual Studio에서 기본 실행 프로필은 IIS Express 합니다.  콘솔 응용 프로그램 프로젝트를 실행 하려면 수동으로 변경 해야 선택한 프로필을 다음 스크린 샷에 표시 된 것 처럼 합니다.

  ![콘솔 응용 프로그램 프로필 선택](weblistener/_static/vs-choose-profile.png)

## <a name="how-to-use-weblistener-outside-of-aspnet-core"></a>ASP.NET Core 외부 WebListener를 사용 하는 방법

* 설치는 [Microsoft.Net.Http.Server](https://www.nuget.org/packages/Microsoft.Net.Http.Server/) NuGet 패키지 합니다.

* [URL 접두사에 WebListener, 바인딩 및 SSL 인증서를 설정 하려면 __'asverify'__](#preregister-url-prefixes-and-configure-ssl) ASP.NET Core에서 사용 하기 위해와 마찬가지로 합니다.

또한 [Http.Sys 레지스트리 설정](https://support.microsoft.com/kb/820129)합니다.


ASP.NET Core 외부에서 WebListener 사용을 보여 주는 코드 샘플은 다음과 같습니다.

```csharp
var settings = new WebListenerSettings();
settings.UrlPrefixes.Add("http://localhost:8080");

using (WebListener listener = new WebListener(settings))
{
    listener.Start();

    while (true)
    {
        var context = await listener.AcceptAsync();
        byte[] bytes = Encoding.ASCII.GetBytes("Hello World: " + DateTime.Now);
        context.Response.ContentLength = bytes.Length;
        context.Response.ContentType = "text/plain";

        await context.Response.Body.WriteAsync(bytes, 0, bytes.Length);
        context.Dispose();
    }
}
```

## <a name="preregister-url-prefixes-and-configure-ssl"></a>URL 접두사 __'asverify'__ 및 SSL 구성

IIS와 WebListener 기본 Http.Sys 커널 모드 드라이버 요청을 수신 하 고 초기 처리를 수행 합니다. Iis에서 관리 UI 제공 모든 항목을 구성 하는 상대적으로 쉬운 방법이 합니다. 그러나 WebListener를 사용 하 여 Http.Sys를 직접 구성 해야 합니다. 즉 netsh.exe를 수행 하는 데 사용 되는 기본 제공 도구입니다. 

가장 일반적인 작업에 대 한 netsh.exe를 사용 해야는 URL 접두사를 예약 하 고 SSL 인증서를 할당 합니다.

NetSh.exe는 초보자를 위한 사용 하기 편리한 도구 않습니다. 다음 예제에서는 포트 80 및 443에 대 한 URL 접두사를 예약 하는 데 필요한 최소한 보여 줍니다.

```console
netsh http add urlacl url=http://+:80/ user=Users
netsh http add urlacl url=https://+:443/ user=Users
```

다음 예제에는 SSL 인증서를 할당 하는 방법을 보여 줍니다.

```console
netsh http add sslcert ipport=0.0.0.0:443 certhash=MyCertHash_Here appid={00000000-0000-0000-0000-000000000000}".
```

다음은 공식 참조 설명서가입니다.

* [Netsh 명령에 대 한 하이퍼텍스트 전송 프로토콜 (HTTP)](https://technet.microsoft.com/library/cc725882.aspx)
* [UrlPrefix 문자열](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)

다음 리소스는 여러 시나리오에 대 한 자세한 지침을 제공 합니다. 참조 하는 문서 `HttpListener` 에 동일 하 게 적용 `WebListener`, 둘 다 Http.Sys 기반으로 합니다.

* [방법: SSL 인증서로 포트 구성](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/how-to-configure-a-port-with-an-ssl-certificate)
* [HTTPS 통신-HttpListener 기반 호스팅 및 클라이언트 인증](http://sunshaking.blogspot.com/2012/11/https-communication-httplistener-based.html) 이 제 3 자 블로그 비교적 오래 된 하지만 유용한 정보를 아직 있습니다.
* [방법: 연습을 사용 하 여 HttpListener 또는 Http 서버 비관리 코드 (c + +)는 SSL 단순 서버로](https://blogs.msdn.microsoft.com/jpsanders/2009/09/29/how-to-walkthrough-using-httplistener-or-http-server-unmanaged-code-c-as-an-ssl-simple-server/) 너무 유용한 정보를 담은 한 오래 된 블로그입니다.
* [SSL과 함께.NET Core WebListener를 설정 하려면 어떻게 해야 합니까?](https://blogs.msdn.microsoft.com/timomta/2016/11/04/how-do-i-set-up-a-net-core-weblistener-with-ssl/)

다음은 netsh.exe 명령줄 보다 쉽게 사용할 수 있는 몇 가지 타사 도구입니다. 제공 되거나 Microsoft에서 보증 되지 이러한 합니다. 도구는 netsh.exe 자체에 대 한 관리자 권한이 필요 하므로 관리자 권한으로 기본적으로 실행 합니다.

* [http.sys 관리자](http://httpsysmanager.codeplex.com/) 목록에 대 한 UI를 제공 하 고 예약, 접두사 및 인증서 신뢰 목록이 SSL 인증서 및 옵션을 구성 합니다. 
* [HttpConfig](http://www.stevestechspot.com/ABetterHttpcfg.aspx) 나열 하거나 SSL 인증서와 URL 접두사를 구성할 수 있습니다. UI 관리자 http.sys 보다 성능이 우수 하 고 몇 가지 추가 구성 옵션을 노출 하지만 그렇지 않으면 유사한 기능을 제공 합니다. 것 새 인증서 신뢰 목록 (CTL)를 만들 수는 없지만 기존을 할당할 수 있습니다.

자체 서명 된 SSL 인증서를 생성 하기 위한 Microsoft 제공 명령줄 도구: [MakeCert.exe](https://msdn.microsoft.com/library/windows/desktop/aa386968) 및 PowerShell cmdlet [New-selfsignedcertificate](https://technet.microsoft.com/itpro/powershell/windows/pki/new-selfsignedcertificate)합니다. 타사 UI 도구를 자체 서명 된 SSL 인증서를 생성 하기 위해 쉽게 해 주는 있습니다.

* [SelfCert](https://www.pluralsight.com/blog/software-development/selfcert-create-a-self-signed-certificate-interactively-gui-or-programmatically-in-net)
* [Makecert UI](http://makecertui.codeplex.com/)

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 리소스를 참조하세요.

* [이 문서에 대 한 샘플 응용 프로그램](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/servers/weblistener/sample)
* [WebListener 소스 코드](https://github.com/aspnet/HttpSysServer/)
* [호스팅](../hosting.md)
