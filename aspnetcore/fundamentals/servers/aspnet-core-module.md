---
title: "ASP.NET Core 모듈"
author: tdykstra
description: "역방향 프록시 서버와 IIS 또는 IIS Express를 사용 하면 Kestrel 웹 서버는 IIS 모듈 ASP.NET Core 모듈 (ANCM)을 소개 합니다."
keywords: "ASP.NET Core, IIS, IIS Express,ASP.NET 핵심 모듈에 UseIISIntegration"
ms.author: tdykstra
manager: wpickett
ms.date: 08/03/2017
ms.topic: article
ms.assetid: 4661af33-34c5-4d71-93a0-8c7632f43580
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/servers/aspnet-core-module
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8ced1e667acb7d11954aea27de7701db89091fd9
ms.sourcegitcommit: 732cd2684246e49e796836596643a8d37e20c46d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2017
---
# <a name="introduction-to-aspnet-core-module"></a><span data-ttu-id="e7bc0-104">ASP.NET Core 모듈 소개</span><span class="sxs-lookup"><span data-stu-id="e7bc0-104">Introduction to ASP.NET Core Module</span></span>

<span data-ttu-id="e7bc0-105">여 [Tom Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), 및 [Chris Ross](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="e7bc0-105">By [Tom Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), and [Chris Ross](https://github.com/Tratcher)</span></span> 

<span data-ttu-id="e7bc0-106">ASP.NET Core 모듈 (ANCM)를 사용 하면 ASP.NET Core IIS 뒤에 있는 응용 프로그램을 실행 및 IIS를 사용 하 여 어떤 것이 좋습니다 (보안, 관리 효율성, 및에서 많은 자세한)에 대 한 [Kestrel](kestrel.md) 어떤 것이 좋습니다 (빠른 않도록)에 대 한 시점과 기술에서 한 번에 모두 하면 도움이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e7bc0-106">ASP.NET Core Module (ANCM) lets you run ASP.NET Core applications behind IIS, using IIS for what it's good at (security, manageability, and lots more) and using [Kestrel](kestrel.md) for what it's good at (being really fast), and getting the benefits from both technologies at once.</span></span> <span data-ttu-id="e7bc0-107">**ANCM은 Kestrel; 에서만 작동 WebListener와 호환 되지 (ASP.NET Core에서 1.x) 또는 (2.x)에서 HTTP.sys 합니다.**</span><span class="sxs-lookup"><span data-stu-id="e7bc0-107">**ANCM works only with Kestrel; it isn't compatible with WebListener (in ASP.NET Core 1.x) or HTTP.sys (in 2.x).**</span></span> 

<span data-ttu-id="e7bc0-108">지원 되는 Windows 버전:</span><span class="sxs-lookup"><span data-stu-id="e7bc0-108">Supported Windows versions:</span></span>

* <span data-ttu-id="e7bc0-109">Windows 7 및 Windows Server 2008 R2 이상</span><span class="sxs-lookup"><span data-stu-id="e7bc0-109">Windows 7 and Windows Server 2008 R2 and later</span></span>

<span data-ttu-id="e7bc0-110">[보거나 다운로드 샘플 코드](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/servers/aspnet-core-module/sample) ([다운로드 하는 방법을](xref:tutorials/index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e7bc0-110">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/servers/aspnet-core-module/sample) ([how to download](xref:tutorials/index#how-to-download-a-sample))</span></span>

## <a name="what-aspnet-core-module-does"></a><span data-ttu-id="e7bc0-111">ASP.NET Core 모듈에서 수행 하는 작업</span><span class="sxs-lookup"><span data-stu-id="e7bc0-111">What ASP.NET Core Module does</span></span>

<span data-ttu-id="e7bc0-112">ANCM은 IIS 파이프라인에 후크 및 ASP.NET Core 응용 프로그램 백 엔드 트래픽을 리디렉션하는 네이티브 IIS 모듈입니다.</span><span class="sxs-lookup"><span data-stu-id="e7bc0-112">ANCM is a native IIS module that hooks into the IIS pipeline and redirects traffic to the backend ASP.NET Core application.</span></span> <span data-ttu-id="e7bc0-113">Windows 인증과 같은 다른 대부분 모듈 여전히 실행 하는 기회를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="e7bc0-113">Most other modules, such as windows authentication, still get a chance to run.</span></span> <span data-ttu-id="e7bc0-114">요청에 대 한 처리기를 선택 하 고 응용 프로그램에 처리기 매핑이 정의 되어 ANCM 컨트롤에만 됩니다 *web.config* 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="e7bc0-114">ANCM only takes control when a handler is selected for the request, and handler mapping is defined in the application *web.config* file.</span></span>

<span data-ttu-id="e7bc0-115">프로세스에서 실행 되는 ASP.NET Core 응용 프로그램을 IIS 작업자 프로세스에서 분리 하므로 ANCM도는 처리 관리.</span><span class="sxs-lookup"><span data-stu-id="e7bc0-115">Because ASP.NET Core applications run in a process separate from the IIS worker process, ANCM also does process management.</span></span> <span data-ttu-id="e7bc0-116">ANCM 첫 번째 요청 제공 하 고 충돌 했을 때 다시 시작 하는 경우 ASP.NET Core 응용 프로그램에 대 한 프로세스를 시작 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7bc0-116">ANCM starts the process for the ASP.NET Core application when the first request comes in and restarts it when it crashes.</span></span> <span data-ttu-id="e7bc0-117">이 기본적으로 동일한 동작 클래식 ASP.NET 응용 프로그램을 실행 하는 IIS에서 프로세스 및 WAS (Windows Activation Service)에 의해 관리 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e7bc0-117">This is essentially the same behavior as classic ASP.NET applications that run in-process in IIS and are managed by WAS (Windows Activation Service).</span></span>

<span data-ttu-id="e7bc0-118">다음은 IIS, ANCM, 및 ASP.NET Core 응용 프로그램 간의 관계를 보여 주는 다이어그램입니다.</span><span class="sxs-lookup"><span data-stu-id="e7bc0-118">Here's a diagram that illustrates the relationship between IIS, ANCM, and ASP.NET Core applications.</span></span>

![ASP.NET Core 모듈](aspnet-core-module/_static/ancm.png)

<span data-ttu-id="e7bc0-120">요청는 웹에서 제공 및 기본 포트 (80) 또는 SSL 포트 (443)에 IIS로 라우팅하는 커널 모드 Http.Sys 드라이버에 도달 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7bc0-120">Requests come in from the Web and hit the kernel mode Http.Sys driver which routes them into IIS on the primary port (80) or SSL port (443).</span></span> <span data-ttu-id="e7bc0-121">ANCM 포트는 80/443 하는 응용 프로그램에 대해 구성 된 HTTP 포트에서 ASP.NET Core 응용 프로그램에 요청을 전달 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7bc0-121">ANCM forwards the requests to the ASP.NET Core application on the HTTP port configured for the application, which is not port 80/443.</span></span>

<span data-ttu-id="e7bc0-122">Kestrel은 ANCM에서 들어오는 트래픽을 수신 대기 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7bc0-122">Kestrel listens for traffic coming from ANCM.</span></span>  <span data-ttu-id="e7bc0-123">ANCM 시작 시 환경 변수를 통해 포트 지정 및 [UseIISIntegration](#call-useiisintegration) 에서 수신 하도록 서버를 구성 하는 메서드 `http://localhost:{port}`합니다.</span><span class="sxs-lookup"><span data-stu-id="e7bc0-123">ANCM specifies the port via environment variable at startup, and the [UseIISIntegration](#call-useiisintegration) method configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="e7bc0-124">ANCM에서가 아니라 요청을 거부 하도록 추가 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7bc0-124">There are additional checks to reject requests not from ANCM.</span></span> <span data-ttu-id="e7bc0-125">(ANCM 지원 하지 않습니다 HTTPS 전달 되므로 IIS에서 HTTPS를 통해 수신 하는 경우에 요청은 HTTP를 통해 전달 됩니다.)</span><span class="sxs-lookup"><span data-stu-id="e7bc0-125">(ANCM does not support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.)</span></span>

<span data-ttu-id="e7bc0-126">Kestrel ANCM에서 요청을 선택 하 고 그런 다음 처리 하 고 변수로 전달 하는 ASP.NET Core 미들웨어 파이프라인 밀어넣습니다 `HttpContext` 인스턴스 응용 프로그램 논리를 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7bc0-126">Kestrel picks up requests from ANCM and pushes them into the ASP.NET Core middleware pipeline, which then handles them and passes them on as `HttpContext` instances to application logic.</span></span> <span data-ttu-id="e7bc0-127">응용 프로그램의 응답은 다음 IIS에서 요청을 시작한 HTTP 클라이언트에 다시 해당 되는 푸시로 다시 전달 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e7bc0-127">The application's responses are then passed back to IIS, which pushes them back out to the HTTP client that initiated the requests.</span></span>

<span data-ttu-id="e7bc0-128">ANCM에 몇 가지 다른 기능도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e7bc0-128">ANCM has a few other functions as well:</span></span>

* <span data-ttu-id="e7bc0-129">환경 변수를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e7bc0-129">Sets environment variables.</span></span>
* <span data-ttu-id="e7bc0-130">로그 `stdout` 파일 저장소에 출력 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7bc0-130">Logs `stdout` output to file storage.</span></span>
* <span data-ttu-id="e7bc0-131">Windows 인증 토큰을 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="e7bc0-131">Forwards Windows authentication tokens.</span></span>

## <a name="how-to-use-ancm-in-aspnet-core-apps"></a><span data-ttu-id="e7bc0-132">ANCM ASP.NET Core 응용 프로그램에서 사용 하는 방법</span><span class="sxs-lookup"><span data-stu-id="e7bc0-132">How to use ANCM in ASP.NET Core apps</span></span>

<span data-ttu-id="e7bc0-133">이 섹션에서는 IIS 서버 및 ASP.NET Core 응용 프로그램 설정에 대 한 프로세스의 개요를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7bc0-133">This section provides an overview of the process for setting up an IIS server and ASP.NET Core application.</span></span> <span data-ttu-id="e7bc0-134">자세한 내용은 참조 [를 IIS에 게시](../../publishing/iis.md)합니다.</span><span class="sxs-lookup"><span data-stu-id="e7bc0-134">For detailed instructions, see [Publishing to IIS](../../publishing/iis.md).</span></span>

### <a name="install-ancm"></a><span data-ttu-id="e7bc0-135">ANCM 설치</span><span class="sxs-lookup"><span data-stu-id="e7bc0-135">Install ANCM</span></span>

<span data-ttu-id="e7bc0-136">ASP.NET Core 모듈 설치 되어 있어야 IIS에서 서버 및 IIS Express에서 개발 컴퓨터의 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7bc0-136">The ASP.NET Core Module has to be installed in IIS on your servers and in IIS Express on your development machines.</span></span> <span data-ttu-id="e7bc0-137">서버, ANCM에 포함 되는 [.NET 핵심 Windows Server 호스팅 번들](https://aka.ms/dotnetcore.2.0.0-windowshosting)합니다.</span><span class="sxs-lookup"><span data-stu-id="e7bc0-137">For servers, ANCM is included in the [.NET Core Windows Server Hosting bundle](https://aka.ms/dotnetcore.2.0.0-windowshosting).</span></span> <span data-ttu-id="e7bc0-138">개발 컴퓨터에 대 한 Visual Studio 자동으로 설치 ANCM IIS 및 IIS Express에서 이미 컴퓨터에 설치 된 경우.</span><span class="sxs-lookup"><span data-stu-id="e7bc0-138">For development machines, Visual Studio automatically installs ANCM in IIS Express, and in IIS if it is already installed on the machine.</span></span>

### <a name="install-the-iisintegration-nuget-package"></a><span data-ttu-id="e7bc0-139">IISIntegration NuGet 패키지 설치</span><span class="sxs-lookup"><span data-stu-id="e7bc0-139">Install the IISIntegration NuGet package</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="e7bc0-140">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="e7bc0-140">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="e7bc0-141">[Microsoft.AspNetCore.Server.IISIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IISIntegration/) 패키지 ASP.NET Core metapackages에 포함 됩니다 ([Microsoft.AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore/) 및 [Microsoft.AspNetCore.All](xref:fundamentals/metapackage) ).</span><span class="sxs-lookup"><span data-stu-id="e7bc0-141">The [Microsoft.AspNetCore.Server.IISIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IISIntegration/) package is included in the ASP.NET Core metapackages ([Microsoft.AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore/) and [Microsoft.AspNetCore.All](xref:fundamentals/metapackage)).</span></span> <span data-ttu-id="e7bc0-142">metapackages 중 하나를 사용 하지 않는 경우 설치 `Microsoft.AspNetCore.Server.IISIntegration` 별도로 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7bc0-142">If you don't use one of the metapackages, install `Microsoft.AspNetCore.Server.IISIntegration` separately.</span></span> <span data-ttu-id="e7bc0-143">`IISIntegration` 패키지는 앱을 설정 하는 ANCM에 의해 브로드캐스팅 되는 환경 변수를 읽을 수 있는 상호 운용성 팩.</span><span class="sxs-lookup"><span data-stu-id="e7bc0-143">The `IISIntegration` package is an interoperability pack that reads environment variables broadcast by ANCM to set up your app.</span></span> <span data-ttu-id="e7bc0-144">환경 변수에서 수신 하도록 포트 등의 구성 정보를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7bc0-144">The environment variables provide configuration information, such as the port to listen on.</span></span> 

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="e7bc0-145">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="e7bc0-145">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="e7bc0-146">응용 프로그램에서 설치 [Microsoft.AspNetCore.Server.IISIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IISIntegration/)합니다.</span><span class="sxs-lookup"><span data-stu-id="e7bc0-146">In your application, install [Microsoft.AspNetCore.Server.IISIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IISIntegration/).</span></span> <span data-ttu-id="e7bc0-147">`IISIntegration` 패키지는 앱을 설정 하는 ANCM에 의해 브로드캐스팅 되는 환경 변수를 읽을 수 있는 상호 운용성 팩.</span><span class="sxs-lookup"><span data-stu-id="e7bc0-147">The `IISIntegration` package  is an interoperability pack that reads environment variables broadcast by ANCM to set up your app.</span></span> <span data-ttu-id="e7bc0-148">환경 변수에서 수신 하도록 포트 등의 구성 정보를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7bc0-148">The environment variables provide configuration information, such as the port to listen on.</span></span> 

---

### <a name="call-useiisintegration"></a><span data-ttu-id="e7bc0-149">UseIISIntegration 호출</span><span class="sxs-lookup"><span data-stu-id="e7bc0-149">Call UseIISIntegration</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="e7bc0-150">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="e7bc0-150">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="e7bc0-151">`UseIISIntegration` 확장 메서드를 [ `WebHostBuilder` ](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.hosting.webhostbuilder) IIS 사용 실행 하면 자동으로 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e7bc0-151">The `UseIISIntegration` extension method on [`WebHostBuilder`](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.hosting.webhostbuilder) is called automatically when you run with IIS.</span></span>

<span data-ttu-id="e7bc0-152">ASP.NET Core metapackages 중 하나를 사용 하지 않는 하 고 설치 하지 않은 경우는 `Microsoft.AspNetCore.Server.IISIntegration` 패키지 하면 런타임 오류가 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7bc0-152">If you aren't using one of the ASP.NET Core metapackages and haven't installed the  `Microsoft.AspNetCore.Server.IISIntegration` package, you get a runtime error.</span></span> <span data-ttu-id="e7bc0-153">호출 하는 경우 `UseIISIntegration` 패키지가 설치 되지 않은 경우 컴파일 시간 오류가 발생 명시적으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7bc0-153">If you call `UseIISIntegration` explicitly, you get a compile time error if the package isn't installed.</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="e7bc0-154">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="e7bc0-154">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="e7bc0-155">응용 프로그램에서 `Main` 메서드를 호출은 `UseIISIntegration` 확장 메서드를 [ `WebHostBuilder` ](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.hosting.webhostbuilder)합니다.</span><span class="sxs-lookup"><span data-stu-id="e7bc0-155">In your application's `Main` method, call the `UseIISIntegration` extension method on [`WebHostBuilder`](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.hosting.webhostbuilder).</span></span> 

[!code-csharp[](aspnet-core-module/sample/Program.cs?name=snippet_Main&highlight=12)]

---

<span data-ttu-id="e7bc0-156">`UseIISIntegration` 메서드가 찾습니다 ANCM 설정 하는 환경 변수 이므로 아니요 ops 발견 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e7bc0-156">The `UseIISIntegration` method looks for environment variables that ANCM sets, and it no-ops if they aren't found.</span></span> <span data-ttu-id="e7bc0-157">이 문제는 개발 및 macOS 또는 Linux에 대 한 테스트 및 IIS를 실행 하는 서버에 배포와 같은 시나리오를 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7bc0-157">This behavior facilitates scenarios like developing and testing on macOS or Linux and deploying to a server that runs IIS.</span></span> <span data-ttu-id="e7bc0-158">MacOS 또는 Linux에서 실행 되는 동안 Kestrel 서버 역할을 웹; 하지만 응용 프로그램 IIS 환경에 배포 될 때 자동으로 사용 하 여 IIS 및 ANCM 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7bc0-158">While running on macOS or Linux, Kestrel acts as the web server; but when the app is deployed to the IIS environment, it automatically uses ANCM and IIS.</span></span>

### <a name="ancm-port-binding-overrides-other-port-bindings"></a><span data-ttu-id="e7bc0-159">포트 바인딩 ANCM 다른 포트 바인딩을 재정의합니다</span><span class="sxs-lookup"><span data-stu-id="e7bc0-159">ANCM port binding overrides other port bindings</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="e7bc0-160">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="e7bc0-160">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="e7bc0-161">ANCM 백 엔드 프로세스에 할당 하는 동적 포트를 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7bc0-161">ANCM generates a dynamic port to assign to the back-end process.</span></span> <span data-ttu-id="e7bc0-162">`UseIISIntegration` 메서드는이 동적 포트를 선택 하 고 Kestrel에서 수신 하도록 구성 `http://locahost:{dynamicPort}/`합니다.</span><span class="sxs-lookup"><span data-stu-id="e7bc0-162">The `UseIISIntegration` method picks up this dynamic port and configures Kestrel to listen on `http://locahost:{dynamicPort}/`.</span></span> <span data-ttu-id="e7bc0-163">이 재정의에 대 한 호출 등의 다른 URL 구성을 `UseUrls` 또는 [Kestrel의 수신 API](xref:fundamentals/servers/kestrel?tabs=aspnetcore2x#endpoint-configuration)합니다.</span><span class="sxs-lookup"><span data-stu-id="e7bc0-163">This overrides other URL configurations, such as calls to `UseUrls` or [Kestrel's Listen API](xref:fundamentals/servers/kestrel?tabs=aspnetcore2x#endpoint-configuration).</span></span> <span data-ttu-id="e7bc0-164">따라서 호출할 필요가 없습니다 `UseUrls` 또는 Kestrel의 `Listen` API ANCM를 사용 하는 경우.</span><span class="sxs-lookup"><span data-stu-id="e7bc0-164">Therefore, you don't need to call `UseUrls` or Kestrel's `Listen` API when you use ANCM.</span></span> <span data-ttu-id="e7bc0-165">호출 하면 `UseUrls` 또는 `Listen`, Kestrel IIS 없이 앱을 실행할 때 지정 하는 포트에서 수신 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7bc0-165">If you do call `UseUrls` or `Listen`, Kestrel listens on the port you specify when you run the app without IIS.</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="e7bc0-166">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="e7bc0-166">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="e7bc0-167">ANCM 백 엔드 프로세스에 할당 하는 동적 포트를 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7bc0-167">ANCM generates a dynamic port to assign to the back-end process.</span></span> <span data-ttu-id="e7bc0-168">`UseIISIntegration` 메서드는이 동적 포트를 선택 하 고 Kestrel에서 수신 하도록 구성 `http://locahost:{dynamicPort}/`합니다.</span><span class="sxs-lookup"><span data-stu-id="e7bc0-168">The `UseIISIntegration` method picks up this dynamic port and configures Kestrel to listen on `http://locahost:{dynamicPort}/`.</span></span> <span data-ttu-id="e7bc0-169">이 재정의에 대 한 호출 등의 다른 URL 구성을 `UseUrls`합니다.</span><span class="sxs-lookup"><span data-stu-id="e7bc0-169">This overrides other URL configurations, such as calls to `UseUrls`.</span></span> <span data-ttu-id="e7bc0-170">따라서 호출할 필요가 없습니다 `UseUrls` ANCM를 사용 하는 경우.</span><span class="sxs-lookup"><span data-stu-id="e7bc0-170">Therefore, you don't need to call `UseUrls` when you use ANCM.</span></span> <span data-ttu-id="e7bc0-171">호출 하면 `UseUrls`, Kestrel IIS 없이 앱을 실행할 때 지정 하는 포트에서 수신 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7bc0-171">If you do call `UseUrls`, Kestrel listens on the port you specify when you run the app without IIS.</span></span>

<span data-ttu-id="e7bc0-172">ASP.NET Core 1.0에서 호출 하는 경우 `UseUrls`, 호출 **전에** 호출 `UseIISIntegration` ANCM 구성 된 포트를 덮어쓰도록 가져오기 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e7bc0-172">In ASP.NET Core 1.0, if you call `UseUrls`, call it **before** you call `UseIISIntegration` so that the ANCM-configured port doesn't get overwritten.</span></span> <span data-ttu-id="e7bc0-173">이 호출 하는 순서는 ANCM 설정 보다 우선 하므로 ASP.NET Core 1.1에서는 필요 하지 않습니다 `UseUrls`합니다.</span><span class="sxs-lookup"><span data-stu-id="e7bc0-173">This calling order isn't required in ASP.NET Core 1.1, because the ANCM setting overrides `UseUrls`.</span></span>

---

### <a name="configure-ancm-options-in-webconfig"></a><span data-ttu-id="e7bc0-174">Web.config에서 ANCM 옵션을 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7bc0-174">Configure ANCM options in Web.config</span></span>

<span data-ttu-id="e7bc0-175">ASP.NET Core 모듈에 대 한 구성에 저장 됩니다는 *Web.config* 응용 프로그램의 루트 폴더에 있는 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="e7bc0-175">Configuration for the ASP.NET Core Module is stored in the *Web.config* file that is located in the application's root folder.</span></span> <span data-ttu-id="e7bc0-176">이 파일의 설정을 시작 명령 및 ASP.NET Core 응용 프로그램을 시작 하는 인수를 가리킵니다.</span><span class="sxs-lookup"><span data-stu-id="e7bc0-176">Settings in this file point to the startup command and arguments that start your ASP.NET Core app.</span></span> <span data-ttu-id="e7bc0-177">샘플 Web.config 코드 및 구성 옵션에 대 한 지침에 대 한 참조 [ASP.NET 핵심 모듈 구성 참조](../../hosting/aspnet-core-module.md)합니다.</span><span class="sxs-lookup"><span data-stu-id="e7bc0-177">For sample Web.config code and guidance on configuration options, see [ASP.NET Core Module Configuration Reference](../../hosting/aspnet-core-module.md).</span></span>

### <a name="run-with-iis-express-in-development"></a><span data-ttu-id="e7bc0-178">개발에서 IIS Express와 함께 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7bc0-178">Run with IIS Express in development</span></span>

<span data-ttu-id="e7bc0-179">IIS Express는 ASP.NET Core 템플릿에 정의 된 기본 프로필을 사용 하 여 Visual Studio에서 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e7bc0-179">IIS Express can be launched by Visual Studio using the default profile defined by the ASP.NET Core templates.</span></span>

## <a name="next-steps"></a><span data-ttu-id="e7bc0-180">다음 단계</span><span class="sxs-lookup"><span data-stu-id="e7bc0-180">Next steps</span></span>

<span data-ttu-id="e7bc0-181">자세한 내용은 다음 리소스를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e7bc0-181">For more information, see the following resources:</span></span>

* [<span data-ttu-id="e7bc0-182">이 문서에 대 한 샘플 응용 프로그램</span><span class="sxs-lookup"><span data-stu-id="e7bc0-182">Sample app for this article</span></span>](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/servers/aspnet-core-module/sample)
* [<span data-ttu-id="e7bc0-183">ASP.NET Core 모듈의 소스 코드</span><span class="sxs-lookup"><span data-stu-id="e7bc0-183">ASP.NET Core Module source code</span></span>](https://github.com/aspnet/AspNetCoreModule)
* [<span data-ttu-id="e7bc0-184">ASP.NET Core 모듈 구성 참조</span><span class="sxs-lookup"><span data-stu-id="e7bc0-184">ASP.NET Core Module Configuration Reference</span></span>](../../hosting/aspnet-core-module.md)
* [<span data-ttu-id="e7bc0-185">IIS에 게시</span><span class="sxs-lookup"><span data-stu-id="e7bc0-185">Publishing to IIS</span></span>](../../publishing/iis.md)
