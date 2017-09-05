---
title: "ASP.NET Core 웹 서버 구현이 HTTP.sys"
author: rick-anderson
description: "Windows에서 ASP.NET Core에 대 한 웹 서버 HTTP.sys를 소개합니다. IIS 없이 인터넷에 직접 연결에 사용할 수 있는 Kestrel 하는 대신을 HTTP.sys는 Http.Sys 커널 모드 드라이버를 기반 합니다."
keywords: "ASP.NET Core, HttpSys, HTTP.sys, HttpListener, SSL url 접두사"
ms.author: riande
manager: wpickett
ms.date: 08/07/2017
ms.topic: article
ms.assetid: 0a7286e4-6428-424e-b5c4-5c98815cf61c
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/servers/httpsys
ms.openlocfilehash: 4173a2ef539859031674fb613b25031e3b343c67
ms.sourcegitcommit: 74e22e08e3b08cb576e5184d16f4af5656c13c0c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2017
---
# <a name="httpsys-web-server-implementation-in-aspnet-core"></a><span data-ttu-id="53769-105">ASP.NET Core 웹 서버 구현이 HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="53769-105">HTTP.sys web server implementation in ASP.NET Core</span></span>

<span data-ttu-id="53769-106">여 [Tom Dykstra](http://github.com/tdykstra) 및 [Chris Ross](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="53769-106">By [Tom Dykstra](http://github.com/tdykstra) and [Chris Ross](https://github.com/Tratcher)</span></span>

> [!NOTE]
> <span data-ttu-id="53769-107">이 항목에는 이상 ASP.NET 코어 2.0에만 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="53769-107">This topic applies only to ASP.NET Core 2.0 and later.</span></span> <span data-ttu-id="53769-108">이전 버전의 ASP.NET Core에서는 HTTP.sys 이름은 [WebListener](WebListener.md)합니다.</span><span class="sxs-lookup"><span data-stu-id="53769-108">In earlier versions of ASP.NET Core, HTTP.sys is named [WebListener](WebListener.md).</span></span>

<span data-ttu-id="53769-109">HTTP.sys는는 [ASP.NET Core 웹 서버로](index.md) windows를 실행 하 합니다.</span><span class="sxs-lookup"><span data-stu-id="53769-109">HTTP.sys is a [web server for ASP.NET Core](index.md) that runs only on Windows.</span></span> <span data-ttu-id="53769-110">상에 구축 되어는 [Http.Sys 커널 모드 드라이버](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx)합니다.</span><span class="sxs-lookup"><span data-stu-id="53769-110">It's built on the [Http.Sys kernel mode driver](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx).</span></span> <span data-ttu-id="53769-111">HTTP.sys는 하는 대신 [Kestrel](kestrel.md) Kestel 하지 않는 일부 기능을 제공 하는 합니다.</span><span class="sxs-lookup"><span data-stu-id="53769-111">HTTP.sys is an alternative to [Kestrel](kestrel.md) that offers some features that Kestel doesn't.</span></span> <span data-ttu-id="53769-112">**HTTP.sys와 호환 되지 않습니다 IIS 또는 IIS Express를 사용할 수 없습니다는 [ASP.NET Core 모듈](aspnet-core-module.md)합니다.**</span><span class="sxs-lookup"><span data-stu-id="53769-112">**HTTP.sys can't be used with IIS or IIS Express, as it isn't compatible with the [ASP.NET Core Module](aspnet-core-module.md).**</span></span>

<span data-ttu-id="53769-113">HTTP.sys는 다음과 같은 기능을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="53769-113">HTTP.sys supports the following features:</span></span>

- [<span data-ttu-id="53769-114">Windows 인증</span><span class="sxs-lookup"><span data-stu-id="53769-114">Windows Authentication</span></span>](xref:security/authentication/windowsauth)
- <span data-ttu-id="53769-115">포트 공유</span><span class="sxs-lookup"><span data-stu-id="53769-115">Port sharing</span></span>
- <span data-ttu-id="53769-116">SNI https /</span><span class="sxs-lookup"><span data-stu-id="53769-116">HTTPS with SNI</span></span>
- <span data-ttu-id="53769-117">HTTP/2 tls (Windows 10)</span><span class="sxs-lookup"><span data-stu-id="53769-117">HTTP/2 over TLS (Windows 10)</span></span>
- <span data-ttu-id="53769-118">직접 파일 전송</span><span class="sxs-lookup"><span data-stu-id="53769-118">Direct file transmission</span></span>
- <span data-ttu-id="53769-119">응답 캐시</span><span class="sxs-lookup"><span data-stu-id="53769-119">Response caching</span></span>
- <span data-ttu-id="53769-120">Websocket (Windows 8)</span><span class="sxs-lookup"><span data-stu-id="53769-120">WebSockets (Windows 8)</span></span>

<span data-ttu-id="53769-121">지원 되는 Windows 버전:</span><span class="sxs-lookup"><span data-stu-id="53769-121">Supported Windows versions:</span></span>

- <span data-ttu-id="53769-122">Windows 7 및 Windows Server 2008 R2 이상</span><span class="sxs-lookup"><span data-stu-id="53769-122">Windows 7 and Windows Server 2008 R2 and later</span></span>

[<span data-ttu-id="53769-123">샘플 코드 보기 또는 다운로드</span><span class="sxs-lookup"><span data-stu-id="53769-123">View or download sample code</span></span>](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/sample)

## <a name="when-to-use-httpsys"></a><span data-ttu-id="53769-124">HTTP.sys를 사용 하는 경우</span><span class="sxs-lookup"><span data-stu-id="53769-124">When to use HTTP.sys</span></span>

<span data-ttu-id="53769-125">HTTP.sys는 IIS를 사용 하지 않고 인터넷에 직접 서버를 노출 해야 하는 배포 하는 데 유용 합니다.</span><span class="sxs-lookup"><span data-stu-id="53769-125">HTTP.sys is useful for deployments where you need to expose the server directly to the Internet without using IIS.</span></span>

![HTTP.sys는 인터넷와 직접 통신](httpsys/_static/httpsys-to-internet.png)

<span data-ttu-id="53769-127">Http.Sys를 기반으로 하기 때문에 HTTP.sys 공격 으로부터 보호에 대 한 역방향 프록시 서버를 필요 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="53769-127">Because it's built on Http.Sys, HTTP.sys doesn't require a reverse proxy server for protection against attacks.</span></span> <span data-ttu-id="53769-128">Http.Sys는 많은 유형의 공격 으로부터 보호 하 고 견고성, 보안 및의 모든 기능을 갖춘 웹 서버 확장성을 제공 하는 완성도 높은 기술입니다.</span><span class="sxs-lookup"><span data-stu-id="53769-128">Http.Sys is mature technology that protects against many kinds of attacks and provides the robustness, security, and scalability of a full-featured web server.</span></span> <span data-ttu-id="53769-129">자체 IIS는 HTTP 수신기 Http.Sys 기반으로 실행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="53769-129">IIS itself runs as an HTTP listener on top of Http.Sys.</span></span> 

<span data-ttu-id="53769-130">HTTP.sys는 내부 배포에 대 한 좋은 선택 기능에서에서 사용할 수 없는 Kestrel, 예: Windows 인증을 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="53769-130">HTTP.sys is a good choice for internal deployments when you need a feature not available in Kestrel, such as Windows authentication.</span></span>

![내부 네트워크와 직접 통신 하는 HTTP.sys](httpsys/_static/httpsys-to-internal.png)

## <a name="how-to-use-httpsys"></a><span data-ttu-id="53769-132">HTTP.sys를 사용 하는 방법</span><span class="sxs-lookup"><span data-stu-id="53769-132">How to use HTTP.sys</span></span>

<span data-ttu-id="53769-133">호스트 OS 및 ASP.NET Core 응용 프로그램에 대 한 설치 작업의 개요는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="53769-133">Here's an overview of setup tasks for the host OS and your ASP.NET Core application.</span></span>

### <a name="configure-windows-server"></a><span data-ttu-id="53769-134">Windows Server를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="53769-134">Configure Windows Server</span></span>

* <span data-ttu-id="53769-135">버전의 응용 프로그램에 필요한와 같은.NET 설치 [.NET Core](https://www.microsoft.com/net/download/core) 또는 [.NET Framework](https://www.microsoft.com/net/download/framework)합니다.</span><span class="sxs-lookup"><span data-stu-id="53769-135">Install the version of .NET that your application requires, such as [.NET Core](https://www.microsoft.com/net/download/core) or [.NET Framework](https://www.microsoft.com/net/download/framework).</span></span>

* <span data-ttu-id="53769-136">HTTP.sys에 바인딩 및 SSL 인증서를 설정에 대 한 URL 접두사를 미리 등록</span><span class="sxs-lookup"><span data-stu-id="53769-136">Preregister URL prefixes to bind to HTTP.sys, and set up SSL certificates</span></span>

   <span data-ttu-id="53769-137">Windows의 URL 접두사를 미리 등록 하지 않는 경우 관리자 권한으로 응용 프로그램을 실행 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="53769-137">If you don't preregister URL prefixes in Windows, you have to run your application with administrator privileges.</span></span> <span data-ttu-id="53769-138">유일한 예외는 HTTP (HTTPS)을 사용 하 여 포트 번호를 1024; 개를 사용 하는 localhost에 바인딩할 경우 이 경우 관리자 권한이 필요 없습니다.</span><span class="sxs-lookup"><span data-stu-id="53769-138">The only exception is if you bind to localhost using HTTP (not HTTPS) with a port number greater than 1024; in that case, administrator privileges aren't required.</span></span>

   <span data-ttu-id="53769-139">자세한 내용은 참조 [접두사 __'asverify'__ 및 SSL을 구성 하는 방법](#preregister-url-prefixes-and-configure-ssl) 이 문서의 뒷부분에 나오는 합니다.</span><span class="sxs-lookup"><span data-stu-id="53769-139">For details, see [How to preregister prefixes and configure SSL](#preregister-url-prefixes-and-configure-ssl) later in this article.</span></span>

* <span data-ttu-id="53769-140">HTTP.sys에 도달 하도록 허용 하려면 방화벽 포트를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="53769-140">Open firewall ports to allow traffic to reach HTTP.sys.</span></span>

   <span data-ttu-id="53769-141">사용할 수 있습니다 *netsh.exe* 또는 [PowerShell cmdlet](https://technet.microsoft.com/library/jj554906)합니다.</span><span class="sxs-lookup"><span data-stu-id="53769-141">You can use *netsh.exe* or [PowerShell cmdlets](https://technet.microsoft.com/library/jj554906).</span></span>

<span data-ttu-id="53769-142">또한 [Http.Sys 레지스트리 설정](https://support.microsoft.com/kb/820129)합니다.</span><span class="sxs-lookup"><span data-stu-id="53769-142">There are also [Http.Sys registry settings](https://support.microsoft.com/kb/820129).</span></span>

### <a name="configure-your-aspnet-core-application-to-use-httpsys"></a><span data-ttu-id="53769-143">HTTP.sys를 사용 하도록 ASP.NET Core 응용 프로그램 구성</span><span class="sxs-lookup"><span data-stu-id="53769-143">Configure your ASP.NET Core application to use HTTP.sys</span></span>

* <span data-ttu-id="53769-144">사용 하는 경우 필요 없는 패키지 설치는 [Microsoft.AspNetCore.All](xref:fundamentals/metapackage) metapackage 합니다.</span><span class="sxs-lookup"><span data-stu-id="53769-144">No package install is needed if you use the [Microsoft.AspNetCore.All](xref:fundamentals/metapackage) metapackage.</span></span> <span data-ttu-id="53769-145">[Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/) 패키지는 metapackage에 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="53769-145">The [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/) package is included in the metapackage.</span></span>

* <span data-ttu-id="53769-146">호출 된 `UseHttpSys` 확장 메서드를 `WebHostBuilder` 에 프로그램 `Main` 메서드를 지정 하 [HTTP.sys 옵션](https://github.com/aspnet/HttpSysServer/blob/rel/2.0.0/src/Microsoft.AspNetCore.Server.HttpSys/HttpSysOptions.cs) 다음 예제와 같이 필요한:</span><span class="sxs-lookup"><span data-stu-id="53769-146">Call the `UseHttpSys` extension method on `WebHostBuilder` in your `Main` method, specifying any [HTTP.sys options](https://github.com/aspnet/HttpSysServer/blob/rel/2.0.0/src/Microsoft.AspNetCore.Server.HttpSys/HttpSysOptions.cs) that you need, as shown in the following example:</span></span>

  [!code-csharp[](HttpSys/sample/Program.cs?name=snippet_Main&highlight=11-19)]

### <a name="configure-httpsys-options"></a><span data-ttu-id="53769-147">HTTP.sys 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="53769-147">Configure HTTP.sys options</span></span>

<span data-ttu-id="53769-148">다음은 HTTP.sys 설정 및 구성할 수 있는 제한의 일부입니다.</span><span class="sxs-lookup"><span data-stu-id="53769-148">Here are some of the HTTP.sys settings and limits that you can configure.</span></span>

<span data-ttu-id="53769-149">**최대 클라이언트 연결**</span><span class="sxs-lookup"><span data-stu-id="53769-149">**Maximum client connections**</span></span>

<span data-ttu-id="53769-150">다음 코드는 전체 응용 프로그램에 대 한 동시에 열린 TCP 연결의 최대 수를 설정할 수 있습니다 *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="53769-150">The maximum number of concurrent open TCP connections can be set for the entire application with the following code in *Program.cs*:</span></span>

[!code-csharp[](HttpSys/sample/Program.cs?name=snippet_Options&highlight=5)]

<span data-ttu-id="53769-151">연결의 최대 수는 기본적으로 무제한 (null).</span><span class="sxs-lookup"><span data-stu-id="53769-151">The maximum number of connections is unlimited (null) by default.</span></span>

<span data-ttu-id="53769-152">**최대 요청 본문 크기**</span><span class="sxs-lookup"><span data-stu-id="53769-152">**Maximum request body size**</span></span>

<span data-ttu-id="53769-153">기본 최대 요청 본문 크기는 약 28.6 m B 인 30,000,000 바이트입니다.</span><span class="sxs-lookup"><span data-stu-id="53769-153">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6MB.</span></span>

<span data-ttu-id="53769-154">사용 하는 ASP.NET Core MVC 응용 프로그램에 대 한도 무시 하는 권장된 방법은 [RequestSizeLimit](https://github.com/aspnet/Mvc/blob/rel/2.0.0/src/Microsoft.AspNetCore.Mvc.Core/RequestSizeLimitAttribute.cs) 작업 메서드에 특성:</span><span class="sxs-lookup"><span data-stu-id="53769-154">The recommended way to override the limit in an ASP.NET Core MVC app is to use the [RequestSizeLimit](https://github.com/aspnet/Mvc/blob/rel/2.0.0/src/Microsoft.AspNetCore.Mvc.Core/RequestSizeLimitAttribute.cs) attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="53769-155">전체 응용 프로그램을 모든 요청에 대 한 제약 조건을 구성 하는 방법을 보여 주는 예제는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="53769-155">Here's an example that shows how to configure the constraint for the entire application, every request:</span></span>

[!code-csharp[](HttpSys/sample/Program.cs?name=snippet_Options&highlight=6)]

<span data-ttu-id="53769-156">특정 요청에 대 한 설정을 재정의할 수 있습니다 *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="53769-156">You can override the setting on a specific request in *Startup.cs*:</span></span>

[!code-csharp[](httpsys/sample/Startup.cs?name=snippet_Configure&highlight=9-10)]
 
<span data-ttu-id="53769-157">응용 프로그램 요청 읽기 시작 된 후 요청에 대 한 제한을 구성 하려고 하면 예외가 throw 됩니다.</span><span class="sxs-lookup"><span data-stu-id="53769-157">An exception is thrown if you try to configure the limit on a request after the application has started reading the request.</span></span> <span data-ttu-id="53769-158">`IsReadOnly` 를 알려 주는 속성 경우는 `MaxRequestBodySize` 제한을 구성 하려면에 너무 늦었습니다 의미 속성은 읽기 전용 상태에서입니다.</span><span class="sxs-lookup"><span data-stu-id="53769-158">There's an `IsReadOnly` property that tells you if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="53769-159">다른 HTTP.sys 옵션에 대 한 정보를 참조 하십시오. [HttpSysOptions](https://github.com/aspnet/HttpSysServer/blob/rel/2.0.0/src/Microsoft.AspNetCore.Server.HttpSys/HttpSysOptions.cs)합니다.</span><span class="sxs-lookup"><span data-stu-id="53769-159">For information about other HTTP.sys options, see [HttpSysOptions](https://github.com/aspnet/HttpSysServer/blob/rel/2.0.0/src/Microsoft.AspNetCore.Server.HttpSys/HttpSysOptions.cs).</span></span> 

### <a name="configure-urls-and-ports-to-listen-on"></a><span data-ttu-id="53769-160">Url 및 포트에서 수신 하도록 구성</span><span class="sxs-lookup"><span data-stu-id="53769-160">Configure URLs and ports to listen on</span></span> 

<span data-ttu-id="53769-161">기본적으로 ASP.NET Core을 바인딩합니다 `http://localhost:5000`합니다.</span><span class="sxs-lookup"><span data-stu-id="53769-161">By default ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="53769-162">URL 접두사와 포트를 구성 하려면 사용할 수 있습니다는 `UseUrls` 확장 메서드는 `urls` 명령줄 인수를 ASPNETCORE_URLS 환경 변수 또는 `UrlPrefixes` 속성 [HttpSysOptions](https://github.com/aspnet/HttpSysServer/blob/rel/2.0.0/src/Microsoft.AspNetCore.Server.HttpSys/HttpSysOptions.cs)합니다.</span><span class="sxs-lookup"><span data-stu-id="53769-162">To configure URL prefixes and ports, you can use the `UseUrls` extension method, the `urls` command-line argument, the ASPNETCORE_URLS environment variable, or the `UrlPrefixes` property on [HttpSysOptions](https://github.com/aspnet/HttpSysServer/blob/rel/2.0.0/src/Microsoft.AspNetCore.Server.HttpSys/HttpSysOptions.cs).</span></span> <span data-ttu-id="53769-163">다음 코드 예제에서는 `UrlPrefixes`합니다.</span><span class="sxs-lookup"><span data-stu-id="53769-163">The following code example uses `UrlPrefixes`.</span></span>

[!code-csharp[](HttpSys/sample/Program.cs?name=snippet_Main&highlight=17)]

<span data-ttu-id="53769-164">경우의 이점은 `UrlPrefixes` 는 수를 가져올 오류 메시지가 즉시 서식이 잘못 지정 된 접두사를 추가 하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="53769-164">An advantage of `UrlPrefixes` is that you get an error message immediately if you try to add a prefix that is formatted wrong.</span></span> <span data-ttu-id="53769-165">경우의 이점은 `UseUrls` (공유 하면 `urls` 및 ASPNETCORE_URLS) 보다 쉽게 Kestrel 고 HTTP.sys 파일 간에 전환할 수는 있습니다.</span><span class="sxs-lookup"><span data-stu-id="53769-165">An advantage of `UseUrls` (shared with `urls` and ASPNETCORE_URLS) is that you can more easily switch between Kestrel and HTTP.sys.</span></span>

<span data-ttu-id="53769-166">둘 다 사용 하는 경우 `UseUrls` (또는 `urls` 또는 ASPNETCORE_URLS) 및 `UrlPrefixes`에서 설정을 `UrlPrefixes` 재정의 `UseUrls`합니다.</span><span class="sxs-lookup"><span data-stu-id="53769-166">If you use both `UseUrls` (or `urls` or ASPNETCORE_URLS) and `UrlPrefixes`, the settings in `UrlPrefixes` override the ones in `UseUrls`.</span></span> <span data-ttu-id="53769-167">자세한 내용은 참조 [호스팅](xref:fundamentals/hosting)합니다.</span><span class="sxs-lookup"><span data-stu-id="53769-167">For more information, see [Hosting](xref:fundamentals/hosting).</span></span>

<span data-ttu-id="53769-168">HTTP.sys를 사용 하는 [HTTP 서버 API UrlPrefix 문자열 형식은](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)합니다.</span><span class="sxs-lookup"><span data-stu-id="53769-168">HTTP.sys uses the [HTTP Server API UrlPrefix string formats](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx).</span></span>

> [!NOTE]
> <span data-ttu-id="53769-169">에 동일한 접두사 문자열을 지정 해야 `UseUrls` 또는 `UrlPrefixes` 서버의 __'asverify'__입니다.</span><span class="sxs-lookup"><span data-stu-id="53769-169">Make sure that you specify the same prefix strings in `UseUrls` or `UrlPrefixes` that you preregister on the server.</span></span> 

### <a name="dont-use-iis"></a><span data-ttu-id="53769-170">IIS를 사용 하지 마십시오</span><span class="sxs-lookup"><span data-stu-id="53769-170">Don't use IIS</span></span>

<span data-ttu-id="53769-171">응용 프로그램은 IIS 또는 IIS Express를 실행 하도록 구성 되지 않았습니다 있는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="53769-171">Make sure your application isn't configured to run IIS or IIS Express.</span></span>

<span data-ttu-id="53769-172">Visual Studio에서 기본 실행 프로필은 IIS Express 합니다.</span><span class="sxs-lookup"><span data-stu-id="53769-172">In Visual Studio, the default launch profile is for IIS Express.</span></span> <span data-ttu-id="53769-173">콘솔 응용 프로그램 프로젝트를 실행 하려면 다음 스크린 샷에 표시 된 것 처럼 선택한 프로필까지 변경 수동으로.</span><span class="sxs-lookup"><span data-stu-id="53769-173">To run the project as a console application, manually change the selected profile, as shown in the following screen shot.</span></span>

![콘솔 응용 프로그램 프로필 선택](httpsys/_static/vs-choose-profile.png)

## <a name="preregister-url-prefixes-and-configure-ssl"></a><span data-ttu-id="53769-175">URL 접두사 __'asverify'__ 및 SSL 구성</span><span class="sxs-lookup"><span data-stu-id="53769-175">Preregister URL prefixes and configure SSL</span></span>

<span data-ttu-id="53769-176">HTTP.sys와 IIS 기본 Http.Sys 커널 모드 드라이버 요청을 수신 하 고 초기 처리를 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="53769-176">Both IIS and HTTP.sys rely on the underlying Http.Sys kernel mode driver to listen for requests and do initial processing.</span></span> <span data-ttu-id="53769-177">Iis에서 관리 UI 제공 모든 항목을 구성 하는 상대적으로 쉬운 방법이 합니다.</span><span class="sxs-lookup"><span data-stu-id="53769-177">In IIS, the management UI gives you a relatively easy way to configure everything.</span></span> <span data-ttu-id="53769-178">그러나 Http.Sys를 직접 구성 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="53769-178">However, you need to configure Http.Sys yourself.</span></span> <span data-ttu-id="53769-179">즉 수행 하는 데 기본 제공 도구 *netsh.exe*합니다.</span><span class="sxs-lookup"><span data-stu-id="53769-179">The built-in tool for doing that is *netsh.exe*.</span></span> 

<span data-ttu-id="53769-180">와 *netsh.exe* URL 접두사를 예약 하 고 SSL 인증서를 할당할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="53769-180">With *netsh.exe* you can reserve URL prefixes and assign SSL certificates.</span></span> <span data-ttu-id="53769-181">이 도구는 관리 권한이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="53769-181">The tool requires administrative privileges.</span></span>

<span data-ttu-id="53769-182">다음 예제에서는 포트 80 및 443에 대 한 URL 접두사를 예약 하는 데 필요한 최소:</span><span class="sxs-lookup"><span data-stu-id="53769-182">The following example shows the minimum needed to reserve URL prefixes for ports 80 and 443:</span></span>

```console
netsh http add urlacl url=http://+:80/ user=Users
netsh http add urlacl url=https://+:443/ user=Users
```

<span data-ttu-id="53769-183">다음 예제에는 SSL 인증서를 할당 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="53769-183">The following example shows how to assign an SSL certificate:</span></span>

```console
netsh http add sslcert ipport=0.0.0.0:443 certhash=MyCertHash_Here appid={00000000-0000-0000-0000-000000000000}"
```

<span data-ttu-id="53769-184">여기에 대 한 참조 설명서는 *netsh.exe*:</span><span class="sxs-lookup"><span data-stu-id="53769-184">Here is the reference documentation for *netsh.exe*:</span></span>

* [<span data-ttu-id="53769-185">Netsh 명령에 대 한 하이퍼텍스트 전송 프로토콜 (HTTP)</span><span class="sxs-lookup"><span data-stu-id="53769-185">Netsh Commands for Hypertext Transfer Protocol (HTTP)</span></span>](http://technet.microsoft.com/library/cc725882.aspx)
* [<span data-ttu-id="53769-186">UrlPrefix 문자열</span><span class="sxs-lookup"><span data-stu-id="53769-186">UrlPrefix Strings</span></span>](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)

<span data-ttu-id="53769-187">다음 리소스는 여러 시나리오에 대 한 자세한 지침을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="53769-187">The following resources provide detailed instructions for several scenarios.</span></span> <span data-ttu-id="53769-188">HttpListener를 참조 하는 문서 Http.Sys를 기반으로 둘 다으로 HTTP.sys에 동일 하 게 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="53769-188">Articles that refer to HttpListener apply equally to HTTP.sys, as both are based on Http.Sys.</span></span>

* [<span data-ttu-id="53769-189">방법: SSL 인증서로 포트 구성</span><span class="sxs-lookup"><span data-stu-id="53769-189">How to: Configure a Port with an SSL Certificate</span></span>](http://msdn.microsoft.com/library/ms733791.aspx)
* <span data-ttu-id="53769-190">[HTTPS 통신-HttpListener 기반 호스팅 및 클라이언트 인증](http://sunshaking.blogspot.com/2012/11/https-communication-httplistener-based.html) 이 제 3 자 블로그 비교적 오래 된 하지만 유용한 정보를 아직 있습니다.</span><span class="sxs-lookup"><span data-stu-id="53769-190">[HTTPS Communication - HttpListener based Hosting and Client Certification](http://sunshaking.blogspot.com/2012/11/https-communication-httplistener-based.html) This is a third-party blog and is fairly old but still has useful information.</span></span>
* <span data-ttu-id="53769-191">[방법: 연습을 사용 하 여 HttpListener 또는 Http 서버 비관리 코드 (c + +)는 SSL 단순 서버로](http://blogs.msdn.com/b/jpsanders/archive/2009/09/29/walkthrough-using-httplistener-as-an-ssl-simple-server.aspx) 너무 유용한 정보를 담은 한 오래 된 블로그입니다.</span><span class="sxs-lookup"><span data-stu-id="53769-191">[How To: Walkthrough Using HttpListener or Http Server unmanaged code (C++) as an SSL Simple Server](http://blogs.msdn.com/b/jpsanders/archive/2009/09/29/walkthrough-using-httplistener-as-an-ssl-simple-server.aspx) This too is an older blog with useful information.</span></span>

<span data-ttu-id="53769-192">다음은 보다 사용 하기 쉽게 될 수 있는 몇 가지 타사 도구는 *netsh.exe* 명령줄입니다.</span><span class="sxs-lookup"><span data-stu-id="53769-192">Here are some third-party tools that can be easier to use than the *netsh.exe* command line.</span></span> <span data-ttu-id="53769-193">제공 되거나 Microsoft에서 보증 되지 이러한 합니다.</span><span class="sxs-lookup"><span data-stu-id="53769-193">These are not provided by or endorsed by Microsoft.</span></span> <span data-ttu-id="53769-194">도구는 관리자 권한으로 실행 기본적으로 이후 *netsh.exe* 자체 하려면 관리자 권한이 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="53769-194">The tools run as administrator by default, since *netsh.exe* itself requires administrator privileges.</span></span>

* <span data-ttu-id="53769-195">[http.sys 관리자](http://httpsysmanager.codeplex.com/) 목록에 대 한 UI를 제공 하 고 예약, 접두사 및 인증서 신뢰 목록이 SSL 인증서 및 옵션을 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="53769-195">[http.sys Manager](http://httpsysmanager.codeplex.com/) provides UI for listing and configuring SSL certificates and options, prefix reservations, and certificate trust lists.</span></span> 
* <span data-ttu-id="53769-196">[HttpConfig](http://www.stevestechspot.com/ABetterHttpcfg.aspx) 나열 하거나 SSL 인증서와 URL 접두사를 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="53769-196">[HttpConfig](http://www.stevestechspot.com/ABetterHttpcfg.aspx) lets you list or configure SSL certificates and URL prefixes.</span></span> <span data-ttu-id="53769-197">UI 관리자 http.sys 보다 성능이 우수 하 고 몇 가지 추가 구성 옵션을 노출 하지만 그렇지 않으면 유사한 기능을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="53769-197">The UI is more refined than http.sys Manager and exposes a few more configuration options, but otherwise it provides similar functionality.</span></span> <span data-ttu-id="53769-198">것 새 인증서 신뢰 목록 (CTL)를 만들 수는 없지만 기존을 할당할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="53769-198">It cannot create a new certificate trust list (CTL), but can assign existing ones.</span></span>

[!INCLUDE[How to make an SSL cert](../../includes/make-ssl-cert.md)]

## <a name="next-steps"></a><span data-ttu-id="53769-199">다음 단계</span><span class="sxs-lookup"><span data-stu-id="53769-199">Next steps</span></span>

<span data-ttu-id="53769-200">자세한 내용은 다음 리소스를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="53769-200">For more information, see the following resources:</span></span>

* [<span data-ttu-id="53769-201">이 문서에 대 한 샘플 응용 프로그램</span><span class="sxs-lookup"><span data-stu-id="53769-201">Sample app for this article</span></span>](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/servers/HttpSys/sample)
* [<span data-ttu-id="53769-202">HTTP.sys 소스 코드</span><span class="sxs-lookup"><span data-stu-id="53769-202">HTTP.sys source code</span></span>](https://github.com/aspnet/HttpSysServer/)
* [<span data-ttu-id="53769-203">호스팅</span><span class="sxs-lookup"><span data-stu-id="53769-203">Hosting</span></span>](xref:fundamentals/hosting)
