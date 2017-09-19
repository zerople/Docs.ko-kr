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
ms.openlocfilehash: 6d9f5d2a488935f9512a12c7ce6d2400f109abdb
ms.sourcegitcommit: 67f54fabbfa4e3942f5bfe1f8a7fdfe4a7a75358
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/19/2017
---
# <a name="weblistener-web-server-implementation-in-aspnet-core"></a><span data-ttu-id="8dcaa-105">ASP.NET Core 웹 서버 구현이 WebListener</span><span class="sxs-lookup"><span data-stu-id="8dcaa-105">WebListener web server implementation in ASP.NET Core</span></span>

<span data-ttu-id="8dcaa-106">여 [Tom Dykstra](https://github.com/tdykstra) 및 [Chris Ross](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="8dcaa-106">By [Tom Dykstra](https://github.com/tdykstra) and [Chris Ross](https://github.com/Tratcher)</span></span>

> [!NOTE]
> <span data-ttu-id="8dcaa-107">이 항목은 ASP.NET Core에만 적용 됩니다. 1.x 합니다.</span><span class="sxs-lookup"><span data-stu-id="8dcaa-107">This topic applies only to ASP.NET Core 1.x.</span></span> <span data-ttu-id="8dcaa-108">ASP.NET Core 2.0 WebListener 이름은 [HTTP.sys](httpsys.md)합니다.</span><span class="sxs-lookup"><span data-stu-id="8dcaa-108">In ASP.NET Core 2.0, WebListener is named [HTTP.sys](httpsys.md).</span></span>

<span data-ttu-id="8dcaa-109">WebListener는는 [ASP.NET Core 웹 서버로](index.md) windows를 실행 하 합니다.</span><span class="sxs-lookup"><span data-stu-id="8dcaa-109">WebListener is a [web server for ASP.NET Core](index.md) that runs only on Windows.</span></span> <span data-ttu-id="8dcaa-110">상에 구축 되어는 [Http.Sys 커널 모드 드라이버](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx)합니다.</span><span class="sxs-lookup"><span data-stu-id="8dcaa-110">It's built on the [Http.Sys kernel mode driver](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx).</span></span> <span data-ttu-id="8dcaa-111">WebListener 하지 않아도 됩니다 [Kestrel](kestrel.md) 역방향 프록시 서버와 IIS에 의존 하지 않고 인터넷에 직접 연결에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8dcaa-111">WebListener is an alternative to [Kestrel](kestrel.md) that can be used for direct connection to the Internet without relying on IIS as a reverse proxy server.</span></span> <span data-ttu-id="8dcaa-112">사실, **WebListener와 호환 되지 않습니다 IIS 또는 IIS Express를 사용할 수 없습니다는 [ASP.NET Core 모듈](aspnet-core-module.md)합니다.**</span><span class="sxs-lookup"><span data-stu-id="8dcaa-112">In fact, **WebListener can't be used with IIS or IIS Express, as it isn't compatible with the [ASP.NET Core Module](aspnet-core-module.md).**</span></span>

<span data-ttu-id="8dcaa-113">통해.NET Core 또는.NET Framework 응용 프로그램에서 직접 사용할 수 있지만 WebListener ASP.NET Core에 대 한 개발 된는 [Microsoft.Net.Http.Server](https://www.nuget.org/packages/Microsoft.Net.Http.Server/) NuGet 패키지 합니다.</span><span class="sxs-lookup"><span data-stu-id="8dcaa-113">Although WebListener was developed for ASP.NET Core, it can be used directly in any .NET Core or .NET Framework application via the [Microsoft.Net.Http.Server](https://www.nuget.org/packages/Microsoft.Net.Http.Server/) NuGet package.</span></span>

<span data-ttu-id="8dcaa-114">WebListener는 다음과 같은 기능을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="8dcaa-114">WebListener supports the following features:</span></span>

- [<span data-ttu-id="8dcaa-115">Windows 인증</span><span class="sxs-lookup"><span data-stu-id="8dcaa-115">Windows Authentication</span></span>](xref:security/authentication/windowsauth)
- <span data-ttu-id="8dcaa-116">포트 공유</span><span class="sxs-lookup"><span data-stu-id="8dcaa-116">Port sharing</span></span>
- <span data-ttu-id="8dcaa-117">SNI https /</span><span class="sxs-lookup"><span data-stu-id="8dcaa-117">HTTPS with SNI</span></span>
- <span data-ttu-id="8dcaa-118">HTTP/2 tls (Windows 10)</span><span class="sxs-lookup"><span data-stu-id="8dcaa-118">HTTP/2 over TLS (Windows 10)</span></span>
- <span data-ttu-id="8dcaa-119">직접 파일 전송</span><span class="sxs-lookup"><span data-stu-id="8dcaa-119">Direct file transmission</span></span>
- <span data-ttu-id="8dcaa-120">응답 캐시</span><span class="sxs-lookup"><span data-stu-id="8dcaa-120">Response caching</span></span>
- <span data-ttu-id="8dcaa-121">Websocket (Windows 8)</span><span class="sxs-lookup"><span data-stu-id="8dcaa-121">WebSockets (Windows 8)</span></span>

<span data-ttu-id="8dcaa-122">지원 되는 Windows 버전:</span><span class="sxs-lookup"><span data-stu-id="8dcaa-122">Supported Windows versions:</span></span>

- <span data-ttu-id="8dcaa-123">Windows 7 및 Windows Server 2008 R2 이상</span><span class="sxs-lookup"><span data-stu-id="8dcaa-123">Windows 7 and Windows Server 2008 R2 and later</span></span>

[<span data-ttu-id="8dcaa-124">샘플 코드 보기 또는 다운로드</span><span class="sxs-lookup"><span data-stu-id="8dcaa-124">View or download sample code</span></span>](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/servers/weblistener/sample)

## <a name="when-to-use-weblistener"></a><span data-ttu-id="8dcaa-125">WebListener를 사용 하는 경우</span><span class="sxs-lookup"><span data-stu-id="8dcaa-125">When to use WebListener</span></span>

<span data-ttu-id="8dcaa-126">WebListener는 IIS를 사용 하지 않고 인터넷에 직접 서버를 노출 해야 하는 배포에 유용 합니다.</span><span class="sxs-lookup"><span data-stu-id="8dcaa-126">WebListener is useful for deployments where you need to expose the server directly to the Internet without using IIS.</span></span>

![WebListener는 인터넷과 직접 통신합니다.](weblistener/_static/weblistener-to-internet.png)

<span data-ttu-id="8dcaa-128">Http.Sys를 기반으로 하기 때문에 WebListener 공격 으로부터 보호에 대 한 역방향 프록시 서버를 필요 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8dcaa-128">Because it's built on Http.Sys, WebListener doesn't require a reverse proxy server for protection against attacks.</span></span> <span data-ttu-id="8dcaa-129">Http.Sys는 많은 유형의 공격 으로부터 보호 하 고 견고성, 보안 및의 모든 기능을 갖춘 웹 서버 확장성을 제공 하는 완성도 높은 기술입니다.</span><span class="sxs-lookup"><span data-stu-id="8dcaa-129">Http.Sys is mature technology that protects against many kinds of attacks and provides the robustness, security, and scalability of a full-featured web server.</span></span> <span data-ttu-id="8dcaa-130">자체 IIS는 HTTP 수신기 Http.Sys 기반으로 실행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8dcaa-130">IIS itself runs as an HTTP listener on top of Http.Sys.</span></span> 

<span data-ttu-id="8dcaa-131">WebListener는 Kestrel를 사용 하 여 가져올 수 없습니다 제공 하는 기능 중 하나 필요할 때 내부 배포에 대 한 적합 한 선택 이기도 합니다.</span><span class="sxs-lookup"><span data-stu-id="8dcaa-131">WebListener is also a good choice for internal deployments when you need one of the features it offers that you can't get by using Kestrel.</span></span>

![WebListener는 내부 네트워크와 직접 통신합니다.](weblistener/_static/weblistener-to-internal.png)

## <a name="how-to-use-weblistener"></a><span data-ttu-id="8dcaa-133">WebListener를 사용 하는 방법</span><span class="sxs-lookup"><span data-stu-id="8dcaa-133">How to use WebListener</span></span>

<span data-ttu-id="8dcaa-134">호스트 OS 및 ASP.NET Core 응용 프로그램에 대 한 설치 작업의 개요는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="8dcaa-134">Here's an overview of setup tasks for the host OS and your ASP.NET Core application.</span></span>

### <a name="configure-windows-server"></a><span data-ttu-id="8dcaa-135">Windows Server를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="8dcaa-135">Configure Windows Server</span></span>

* <span data-ttu-id="8dcaa-136">버전의 응용 프로그램에 필요한와 같은.NET 설치 [.NET Core](https://download.microsoft.com/download/0/A/3/0A372822-205D-4A86-BFA7-084D2CBE9EDF/DotNetCore.1.0.1-SDK.1.0.0.Preview2-003133-x64.exe) 또는.NET Framework 4.5.1입니다.</span><span class="sxs-lookup"><span data-stu-id="8dcaa-136">Install the version of .NET that your application requires, such as [.NET Core](https://download.microsoft.com/download/0/A/3/0A372822-205D-4A86-BFA7-084D2CBE9EDF/DotNetCore.1.0.1-SDK.1.0.0.Preview2-003133-x64.exe) or .NET Framework 4.5.1.</span></span>

* <span data-ttu-id="8dcaa-137">URL 접두사에 WebListener, 바인딩 및 SSL 인증서를 설정 하려면 미리 등록</span><span class="sxs-lookup"><span data-stu-id="8dcaa-137">Preregister URL prefixes to bind to WebListener, and set up SSL certificates</span></span>

   <span data-ttu-id="8dcaa-138">Windows의 URL 접두사를 미리 등록 하지 않는 경우 관리자 권한으로 응용 프로그램을 실행 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="8dcaa-138">If you don't preregister URL prefixes in Windows, you have to run your application with administrator privileges.</span></span> <span data-ttu-id="8dcaa-139">유일한 예외는 HTTP (HTTPS)을 사용 하 여 포트 번호를 1024; 개를 사용 하는 localhost에 바인딩할 경우 이 경우 관리자 권한이 필요 없습니다.</span><span class="sxs-lookup"><span data-stu-id="8dcaa-139">The only exception is if you bind to localhost using HTTP (not HTTPS) with a port number greater than 1024; in that case administrator privileges aren't required.</span></span>

   <span data-ttu-id="8dcaa-140">자세한 내용은 참조 [접두사 __'asverify'__ 및 SSL을 구성 하는 방법](#preregister-url-prefixes-and-configure-ssl) 이 문서의 뒷부분에 나오는 합니다.</span><span class="sxs-lookup"><span data-stu-id="8dcaa-140">For details, see [How to preregister prefixes and configure SSL](#preregister-url-prefixes-and-configure-ssl) later in this article.</span></span>

* <span data-ttu-id="8dcaa-141">WebListener 도달 하는 트래픽을 허용 하도록 방화벽 포트를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="8dcaa-141">Open firewall ports to allow traffic to reach WebListener.</span></span>

   <span data-ttu-id="8dcaa-142">Netsh.exe를 사용할 수 있습니다 또는 [PowerShell cmdlet](https://technet.microsoft.com/library/jj554906)합니다.</span><span class="sxs-lookup"><span data-stu-id="8dcaa-142">You can use netsh.exe or [PowerShell cmdlets](https://technet.microsoft.com/library/jj554906).</span></span>

<span data-ttu-id="8dcaa-143">또한 [Http.Sys 레지스트리 설정](https://support.microsoft.com/kb/820129)합니다.</span><span class="sxs-lookup"><span data-stu-id="8dcaa-143">There are also [Http.Sys registry settings](https://support.microsoft.com/kb/820129).</span></span>

### <a name="configure-your-aspnet-core-application"></a><span data-ttu-id="8dcaa-144">ASP.NET Core 응용 프로그램 구성</span><span class="sxs-lookup"><span data-stu-id="8dcaa-144">Configure your ASP.NET Core application</span></span>

* <span data-ttu-id="8dcaa-145">NuGet 패키지 설치 [Microsoft.AspNetCore.Server.WebListener](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.WebListener/)합니다.</span><span class="sxs-lookup"><span data-stu-id="8dcaa-145">Install the NuGet package [Microsoft.AspNetCore.Server.WebListener](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.WebListener/).</span></span> <span data-ttu-id="8dcaa-146">도 설치 [Microsoft.Net.Http.Server](https://www.nuget.org/packages/Microsoft.Net.Http.Server/) 종속성으로 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8dcaa-146">This also installs [Microsoft.Net.Http.Server](https://www.nuget.org/packages/Microsoft.Net.Http.Server/) as a dependency.</span></span>

* <span data-ttu-id="8dcaa-147">호출 된 `UseWebListener` 확장 메서드를 [WebHostBuilder](/aspnet/core/api/microsoft.aspnetcore.hosting.webhostbuilder) 에 프로그램 `Main` 모든 WebListener를 지정 하는 메서드를 [옵션](https://github.com/aspnet/HttpSysServer/blob/rel/1.1.2/src/Microsoft.AspNetCore.Server.WebListener/WebListenerOptions.cs) 및 [설정](https://github.com/aspnet/HttpSysServer/blob/rel/1.1.2/src/Microsoft.Net.Http.Server/WebListenerSettings.cs) 해야 하는 를 다음 예제와 같이:</span><span class="sxs-lookup"><span data-stu-id="8dcaa-147">Call the `UseWebListener` extension method on [WebHostBuilder](/aspnet/core/api/microsoft.aspnetcore.hosting.webhostbuilder) in your `Main` method, specifying any WebListener [options](https://github.com/aspnet/HttpSysServer/blob/rel/1.1.2/src/Microsoft.AspNetCore.Server.WebListener/WebListenerOptions.cs) and [settings](https://github.com/aspnet/HttpSysServer/blob/rel/1.1.2/src/Microsoft.Net.Http.Server/WebListenerSettings.cs) that you need, as shown in the following example:</span></span>

  [!code-csharp[](weblistener/sample/Program.cs?name=snippet_Main&highlight=13-17)]

* <span data-ttu-id="8dcaa-148">Url 및 포트에서 수신 하도록 구성</span><span class="sxs-lookup"><span data-stu-id="8dcaa-148">Configure URLs and ports to listen on</span></span> 

  <span data-ttu-id="8dcaa-149">기본적으로 ASP.NET Core을 바인딩합니다 `http://localhost:5000`합니다.</span><span class="sxs-lookup"><span data-stu-id="8dcaa-149">By default ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="8dcaa-150">URL 접두사와 포트를 구성 하려면 사용할 수 있습니다는 `UseURLs` 확장 메서드는 `urls` 명령줄 인수 또는 ASP.NET Core 구성 시스템입니다.</span><span class="sxs-lookup"><span data-stu-id="8dcaa-150">To configure URL prefixes and ports, you can use the `UseURLs` extension method, the `urls` command-line argument or the ASP.NET Core configuration system.</span></span> <span data-ttu-id="8dcaa-151">자세한 내용은 참조 [호스팅](../../fundamentals/hosting.md)합니다.</span><span class="sxs-lookup"><span data-stu-id="8dcaa-151">For more information, see [Hosting](../../fundamentals/hosting.md).</span></span>

  <span data-ttu-id="8dcaa-152">수신기 사용 하 여 웹에서 [Http.Sys 접두사 문자열 형식은](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)합니다.</span><span class="sxs-lookup"><span data-stu-id="8dcaa-152">Web Listener uses the [Http.Sys prefix string formats](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx).</span></span> <span data-ttu-id="8dcaa-153">WebListener 관련이 없는 접두사 문자열 형식 요구 사항이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8dcaa-153">There are no prefix string format requirements that are specific to WebListener.</span></span>

  > [!NOTE]
  > <span data-ttu-id="8dcaa-154">동일한 접두사 문자열을 지정 해야 `UseUrls` 하는 서버에서 미리 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="8dcaa-154">Make sure that you specify the same prefix strings in `UseUrls` that you preregister on the server.</span></span> 

* <span data-ttu-id="8dcaa-155">응용 프로그램 IIS 또는 IIS Express를 실행 하도록 구성 되지 않은 있는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="8dcaa-155">Make sure your application is not configured to run IIS or IIS Express.</span></span>

  <span data-ttu-id="8dcaa-156">Visual Studio에서 기본 실행 프로필은 IIS Express 합니다.</span><span class="sxs-lookup"><span data-stu-id="8dcaa-156">In Visual Studio, the default launch profile is for IIS Express.</span></span>  <span data-ttu-id="8dcaa-157">콘솔 응용 프로그램 프로젝트를 실행 하려면 수동으로 변경 해야 선택한 프로필을 다음 스크린 샷에 표시 된 것 처럼 합니다.</span><span class="sxs-lookup"><span data-stu-id="8dcaa-157">To run the project as a console application you have to manually change the selected profile, as shown in the following screen shot.</span></span>

  ![콘솔 응용 프로그램 프로필 선택](weblistener/_static/vs-choose-profile.png)

## <a name="how-to-use-weblistener-outside-of-aspnet-core"></a><span data-ttu-id="8dcaa-159">ASP.NET Core 외부 WebListener를 사용 하는 방법</span><span class="sxs-lookup"><span data-stu-id="8dcaa-159">How to use WebListener outside of ASP.NET Core</span></span>

* <span data-ttu-id="8dcaa-160">설치는 [Microsoft.Net.Http.Server](https://www.nuget.org/packages/Microsoft.Net.Http.Server/) NuGet 패키지 합니다.</span><span class="sxs-lookup"><span data-stu-id="8dcaa-160">Install the [Microsoft.Net.Http.Server](https://www.nuget.org/packages/Microsoft.Net.Http.Server/) NuGet package.</span></span>

* <span data-ttu-id="8dcaa-161">[URL 접두사에 WebListener, 바인딩 및 SSL 인증서를 설정 하려면 __'asverify'__](#preregister-url-prefixes-and-configure-ssl) ASP.NET Core에서 사용 하기 위해와 마찬가지로 합니다.</span><span class="sxs-lookup"><span data-stu-id="8dcaa-161">[Preregister URL prefixes to bind to WebListener, and set up SSL certificates](#preregister-url-prefixes-and-configure-ssl) as you would for use in ASP.NET Core.</span></span>

<span data-ttu-id="8dcaa-162">또한 [Http.Sys 레지스트리 설정](https://support.microsoft.com/kb/820129)합니다.</span><span class="sxs-lookup"><span data-stu-id="8dcaa-162">There are also [Http.Sys registry settings](https://support.microsoft.com/kb/820129).</span></span>


<span data-ttu-id="8dcaa-163">ASP.NET Core 외부에서 WebListener 사용을 보여 주는 코드 샘플은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="8dcaa-163">Here's a code sample that demonstrates WebListener use outside of ASP.NET Core:</span></span>

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

## <a name="preregister-url-prefixes-and-configure-ssl"></a><span data-ttu-id="8dcaa-164">URL 접두사 __'asverify'__ 및 SSL 구성</span><span class="sxs-lookup"><span data-stu-id="8dcaa-164">Preregister URL prefixes and configure SSL</span></span>

<span data-ttu-id="8dcaa-165">IIS와 WebListener 기본 Http.Sys 커널 모드 드라이버 요청을 수신 하 고 초기 처리를 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="8dcaa-165">Both IIS and WebListener rely on the underlying Http.Sys kernel mode driver to listen for requests and do initial processing.</span></span> <span data-ttu-id="8dcaa-166">Iis에서 관리 UI 제공 모든 항목을 구성 하는 상대적으로 쉬운 방법이 합니다.</span><span class="sxs-lookup"><span data-stu-id="8dcaa-166">In IIS, the management UI gives you a relatively easy way to configure everything.</span></span> <span data-ttu-id="8dcaa-167">그러나 WebListener를 사용 하 여 Http.Sys를 직접 구성 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="8dcaa-167">However, if you're using WebListener you need to configure Http.Sys yourself.</span></span> <span data-ttu-id="8dcaa-168">즉 netsh.exe를 수행 하는 데 사용 되는 기본 제공 도구입니다.</span><span class="sxs-lookup"><span data-stu-id="8dcaa-168">The built-in tool for doing that is netsh.exe.</span></span> 

<span data-ttu-id="8dcaa-169">가장 일반적인 작업에 대 한 netsh.exe를 사용 해야는 URL 접두사를 예약 하 고 SSL 인증서를 할당 합니다.</span><span class="sxs-lookup"><span data-stu-id="8dcaa-169">The most common tasks you need to use netsh.exe for are reserving URL prefixes and assigning SSL certificates.</span></span>

<span data-ttu-id="8dcaa-170">NetSh.exe는 초보자를 위한 사용 하기 편리한 도구 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8dcaa-170">NetSh.exe is not an easy tool to use for beginners.</span></span> <span data-ttu-id="8dcaa-171">다음 예제에서는 포트 80 및 443에 대 한 URL 접두사를 예약 하는 데 필요한 최소한 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="8dcaa-171">The following example shows the bare minimum needed to reserve URL prefixes for ports 80 and 443:</span></span>

```console
netsh http add urlacl url=http://+:80/ user=Users
netsh http add urlacl url=https://+:443/ user=Users
```

<span data-ttu-id="8dcaa-172">다음 예제에는 SSL 인증서를 할당 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="8dcaa-172">The following example shows how to assign an SSL certificate:</span></span>

```console
netsh http add sslcert ipport=0.0.0.0:443 certhash=MyCertHash_Here appid={00000000-0000-0000-0000-000000000000}".
```

<span data-ttu-id="8dcaa-173">다음은 공식 참조 설명서가입니다.</span><span class="sxs-lookup"><span data-stu-id="8dcaa-173">Here is the official reference documentation:</span></span>

* [<span data-ttu-id="8dcaa-174">Netsh 명령에 대 한 하이퍼텍스트 전송 프로토콜 (HTTP)</span><span class="sxs-lookup"><span data-stu-id="8dcaa-174">Netsh Commands for Hypertext Transfer Protocol (HTTP)</span></span>](https://technet.microsoft.com/library/cc725882.aspx)
* [<span data-ttu-id="8dcaa-175">UrlPrefix 문자열</span><span class="sxs-lookup"><span data-stu-id="8dcaa-175">UrlPrefix Strings</span></span>](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)

<span data-ttu-id="8dcaa-176">다음 리소스는 여러 시나리오에 대 한 자세한 지침을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="8dcaa-176">The following resources provide detailed instructions for several scenarios.</span></span> <span data-ttu-id="8dcaa-177">참조 하는 문서 `HttpListener` 에 동일 하 게 적용 `WebListener`, 둘 다 Http.Sys 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="8dcaa-177">Articles that refer to `HttpListener` apply equally to `WebListener`, as both are based on Http.Sys.</span></span>

* [<span data-ttu-id="8dcaa-178">방법: SSL 인증서로 포트 구성</span><span class="sxs-lookup"><span data-stu-id="8dcaa-178">How to: Configure a Port with an SSL Certificate</span></span>](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/how-to-configure-a-port-with-an-ssl-certificate)
* <span data-ttu-id="8dcaa-179">[HTTPS 통신-HttpListener 기반 호스팅 및 클라이언트 인증](http://sunshaking.blogspot.com/2012/11/https-communication-httplistener-based.html) 이 제 3 자 블로그 비교적 오래 된 하지만 유용한 정보를 아직 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8dcaa-179">[HTTPS Communication - HttpListener based Hosting and Client Certification](http://sunshaking.blogspot.com/2012/11/https-communication-httplistener-based.html) This is a third-party blog and is fairly old but still has useful information.</span></span>
* <span data-ttu-id="8dcaa-180">[방법: 연습을 사용 하 여 HttpListener 또는 Http 서버 비관리 코드 (c + +)는 SSL 단순 서버로](https://blogs.msdn.microsoft.com/jpsanders/2009/09/29/how-to-walkthrough-using-httplistener-or-http-server-unmanaged-code-c-as-an-ssl-simple-server/) 너무 유용한 정보를 담은 한 오래 된 블로그입니다.</span><span class="sxs-lookup"><span data-stu-id="8dcaa-180">[How To: Walkthrough Using HttpListener or Http Server unmanaged code (C++) as an SSL Simple Server](https://blogs.msdn.microsoft.com/jpsanders/2009/09/29/how-to-walkthrough-using-httplistener-or-http-server-unmanaged-code-c-as-an-ssl-simple-server/) This too is an older blog with useful information.</span></span>
* [<span data-ttu-id="8dcaa-181">SSL과 함께.NET Core WebListener를 설정 하려면 어떻게 해야 합니까?</span><span class="sxs-lookup"><span data-stu-id="8dcaa-181">How Do I Set Up A .NET Core WebListener With SSL?</span></span>](https://blogs.msdn.microsoft.com/timomta/2016/11/04/how-do-i-set-up-a-net-core-weblistener-with-ssl/)

<span data-ttu-id="8dcaa-182">다음은 netsh.exe 명령줄 보다 쉽게 사용할 수 있는 몇 가지 타사 도구입니다.</span><span class="sxs-lookup"><span data-stu-id="8dcaa-182">Here are some third-party tools that can be easier to use than the netsh.exe command line.</span></span> <span data-ttu-id="8dcaa-183">제공 되거나 Microsoft에서 보증 되지 이러한 합니다.</span><span class="sxs-lookup"><span data-stu-id="8dcaa-183">These are not provided by or endorsed by Microsoft.</span></span> <span data-ttu-id="8dcaa-184">도구는 netsh.exe 자체에 대 한 관리자 권한이 필요 하므로 관리자 권한으로 기본적으로 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="8dcaa-184">The tools run as administrator by default, since netsh.exe itself requires administrator privileges.</span></span>

* <span data-ttu-id="8dcaa-185">[http.sys 관리자](http://httpsysmanager.codeplex.com/) 목록에 대 한 UI를 제공 하 고 예약, 접두사 및 인증서 신뢰 목록이 SSL 인증서 및 옵션을 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="8dcaa-185">[http.sys Manager](http://httpsysmanager.codeplex.com/) provides UI for listing and configuring SSL certificates and options, prefix reservations, and certificate trust lists.</span></span> 
* <span data-ttu-id="8dcaa-186">[HttpConfig](http://www.stevestechspot.com/ABetterHttpcfg.aspx) 나열 하거나 SSL 인증서와 URL 접두사를 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8dcaa-186">[HttpConfig](http://www.stevestechspot.com/ABetterHttpcfg.aspx) lets you list or configure SSL certificates and URL prefixes.</span></span> <span data-ttu-id="8dcaa-187">UI 관리자 http.sys 보다 성능이 우수 하 고 몇 가지 추가 구성 옵션을 노출 하지만 그렇지 않으면 유사한 기능을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="8dcaa-187">The UI is more refined than http.sys Manager and exposes a few more configuration options, but otherwise it provides similar functionality.</span></span> <span data-ttu-id="8dcaa-188">것 새 인증서 신뢰 목록 (CTL)를 만들 수는 없지만 기존을 할당할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8dcaa-188">It cannot create a new certificate trust list (CTL), but can assign existing ones.</span></span>

<span data-ttu-id="8dcaa-189">자체 서명 된 SSL 인증서를 생성 하기 위한 Microsoft 제공 명령줄 도구: [MakeCert.exe](https://msdn.microsoft.com/library/windows/desktop/aa386968) 및 PowerShell cmdlet [New-selfsignedcertificate](https://technet.microsoft.com/itpro/powershell/windows/pki/new-selfsignedcertificate)합니다.</span><span class="sxs-lookup"><span data-stu-id="8dcaa-189">For generating self-signed SSL certificates, Microsoft provides command-line tools: [MakeCert.exe](https://msdn.microsoft.com/library/windows/desktop/aa386968) and the PowerShell cmdlet [New-SelfSignedCertificate](https://technet.microsoft.com/itpro/powershell/windows/pki/new-selfsignedcertificate).</span></span> <span data-ttu-id="8dcaa-190">타사 UI 도구를 자체 서명 된 SSL 인증서를 생성 하기 위해 쉽게 해 주는 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8dcaa-190">There are also third-party UI tools that make it easier for you to generate self-signed SSL certificates:</span></span>

* [<span data-ttu-id="8dcaa-191">SelfCert</span><span class="sxs-lookup"><span data-stu-id="8dcaa-191">SelfCert</span></span>](https://www.pluralsight.com/blog/software-development/selfcert-create-a-self-signed-certificate-interactively-gui-or-programmatically-in-net)
* [<span data-ttu-id="8dcaa-192">Makecert UI</span><span class="sxs-lookup"><span data-stu-id="8dcaa-192">Makecert UI</span></span>](http://makecertui.codeplex.com/)

## <a name="next-steps"></a><span data-ttu-id="8dcaa-193">다음 단계</span><span class="sxs-lookup"><span data-stu-id="8dcaa-193">Next steps</span></span>

<span data-ttu-id="8dcaa-194">자세한 내용은 다음 리소스를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8dcaa-194">For more information, see the following resources:</span></span>

* [<span data-ttu-id="8dcaa-195">이 문서에 대 한 샘플 응용 프로그램</span><span class="sxs-lookup"><span data-stu-id="8dcaa-195">Sample app for this article</span></span>](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/servers/weblistener/sample)
* [<span data-ttu-id="8dcaa-196">WebListener 소스 코드</span><span class="sxs-lookup"><span data-stu-id="8dcaa-196">WebListener source code</span></span>](https://github.com/aspnet/HttpSysServer/)
* [<span data-ttu-id="8dcaa-197">호스팅</span><span class="sxs-lookup"><span data-stu-id="8dcaa-197">Hosting</span></span>](../hosting.md)
