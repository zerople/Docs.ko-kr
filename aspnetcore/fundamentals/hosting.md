---
title: "ASP.NET Core에서 호스팅"
author: guardrex
description: "ASP.NET Core 응용 프로그램 시작 및 수명 관리를 담당 하는 웹 호스트에 알아봅니다."
keywords: "ASP.NET Core 웹 IWebHost, WebHostBuilder, IHostingEnvironment, IApplicationLifetime 호스트"
ms.author: riande
manager: wpickett
ms.date: 09/21/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/hosting
ms.openlocfilehash: 455b992dc10129278f8e23366aac9d8bcbf5594c
ms.sourcegitcommit: ef9784dd7500f22fb98b3591ebd73d57d4f67544
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/21/2017
---
# <a name="hosting-in-aspnet-core"></a><span data-ttu-id="1634a-104">ASP.NET Core에서 호스팅</span><span class="sxs-lookup"><span data-stu-id="1634a-104">Hosting in ASP.NET Core</span></span>

<span data-ttu-id="1634a-105">으로 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="1634a-105">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="1634a-106">ASP.NET Core 응용 프로그램 구성 및 실행 한 *호스트*, 응용 프로그램 시작 및 수명 관리 담당 하는 합니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-106">ASP.NET Core apps configure and launch a *host*, which is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="1634a-107">여기에 최소한 호스트 서버 및 요청 처리 파이프라인을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-107">At a minimum, the host configures a server and a request processing pipeline.</span></span>

## <a name="setting-up-a-host"></a><span data-ttu-id="1634a-108">호스트 설정</span><span class="sxs-lookup"><span data-stu-id="1634a-108">Setting up a host</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="1634a-109">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="1634a-109">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="1634a-110">인스턴스를 사용 하 여 호스트 만들기 [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder)합니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-110">Create a host using an instance of [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder).</span></span> <span data-ttu-id="1634a-111">이 일반적으로 응용 프로그램의 진입점에서 수행 되어는 `Main` 메서드.</span><span class="sxs-lookup"><span data-stu-id="1634a-111">This is typically performed in your app's entry point, the `Main` method.</span></span> <span data-ttu-id="1634a-112">프로젝트 템플릿에서 `Main` 에 위치한 *Program.cs*합니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-112">In the project templates, `Main` is located in *Program.cs*.</span></span> <span data-ttu-id="1634a-113">일반적인 *Program.cs* 호출 [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) 설정은 호스트를 시작 하려면:</span><span class="sxs-lookup"><span data-stu-id="1634a-113">A typical *Program.cs* calls [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) to start setting up a host:</span></span>

[!code-csharp[Main](../common/samples/WebApplication1DotNetCore2.0App/Program.cs?name=snippet_Main)]

<span data-ttu-id="1634a-114">`CreateDefaultBuilder`다음 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-114">`CreateDefaultBuilder` performs the following tasks:</span></span>

* <span data-ttu-id="1634a-115">구성 [Kestrel](servers/kestrel.md) 웹 서버로 합니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-115">Configures [Kestrel](servers/kestrel.md) as the web server.</span></span> <span data-ttu-id="1634a-116">Kestrel 기본 옵션을 참조 하십시오. [는 Kestrel 옵션 섹션에서 ASP.NET Core 웹 서버 구현 Kestrel](xref:fundamentals/servers/kestrel#kestrel-options)합니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-116">For the Kestrel default options, see [the Kestrel options section of Kestrel web server implementation in ASP.NET Core](xref:fundamentals/servers/kestrel#kestrel-options).</span></span>
* <span data-ttu-id="1634a-117">설정 하는 콘텐츠 루트 [Directory.GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory)합니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-117">Sets the content root to [Directory.GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory).</span></span>
* <span data-ttu-id="1634a-118">선택적 구성에서 로드 합니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-118">Loads optional configuration from:</span></span>
  * <span data-ttu-id="1634a-119">*appsettings.json*합니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-119">*appsettings.json*.</span></span>
  * <span data-ttu-id="1634a-120">*appsettings 합니다. {환경}.json*합니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-120">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="1634a-121">[사용자의 비밀](xref:security/app-secrets) 응용 프로그램을 실행 하는 경우는 `Development` 환경입니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-121">[User secrets](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="1634a-122">환경 변수.</span><span class="sxs-lookup"><span data-stu-id="1634a-122">Environment variables.</span></span>
  * <span data-ttu-id="1634a-123">명령줄 인수입니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-123">Command-line arguments.</span></span>
* <span data-ttu-id="1634a-124">구성 [로깅](xref:fundamentals/logging) 사용 하 여 콘솔 및 디버그 출력에 대 한 [로그 필터링](xref:fundamentals/logging#log-filtering) 의 로깅 구성 섹션에 지정 된 규칙은 *appsettings.json* 또는 *appsettings 합니다. {환경}.json* 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-124">Configures [logging](xref:fundamentals/logging) for console and debug output with [log filtering](xref:fundamentals/logging#log-filtering) rules specified in a Logging configuration section of an *appsettings.json* or *appsettings.{Environment}.json* file.</span></span>
* <span data-ttu-id="1634a-125">뒤에 IIS를 실행 하면 [IIS 통합](xref:publishing/iis) 기본 경로 포트를 구성 하 여 서버 수신 대기할 때 사용 하는 [ASP.NET Core 모듈](xref:fundamentals/servers/aspnet-core-module)합니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-125">When running behind IIS, enables [IIS integration](xref:publishing/iis) by configuring the base path and port the server should listen on when using the [ASP.NET Core Module](xref:fundamentals/servers/aspnet-core-module).</span></span> <span data-ttu-id="1634a-126">모듈은 IIS와 Kestrel 간의 역방향 프록시를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-126">The module creates a reverse-proxy between IIS and Kestrel.</span></span> <span data-ttu-id="1634a-127">또한 응용 프로그램을 구성 [시작 오류 캡처](#capture-startup-errors)합니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-127">Also configures the app to [capture startup errors](#capture-startup-errors).</span></span> <span data-ttu-id="1634a-128">IIS 기본 옵션을 참조 하십시오. [IIS의 IIS와 Windows에서 호스트 ASP.NET Core 섹션 옵션](xref:publishing/iis#iis-options)합니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-128">For the IIS default options, see [the IIS options section of Host ASP.NET Core on Windows with IIS](xref:publishing/iis#iis-options).</span></span>

<span data-ttu-id="1634a-129">*콘텐츠 루트* 호스트 MVC 뷰 파일과 같은 콘텐츠 파일을 검색 하는 위치를 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-129">The *content root* determines where the host searches for content files, such as MVC view files.</span></span> <span data-ttu-id="1634a-130">기본 콘텐츠 루트는 [Directory.GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory)합니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-130">The default content root is [Directory.GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory).</span></span> <span data-ttu-id="1634a-131">이 인해 응용 프로그램 루트 폴더에서 시작 될 때 웹 프로젝트의 루트 폴더 콘텐츠 루트로 사용 하 여 (예를 들어 호출 [실행 dotnet](/dotnet/core/tools/dotnet-run) 프로젝트 폴더에서).</span><span class="sxs-lookup"><span data-stu-id="1634a-131">This results in using the web project's root folder as the content root when the app is started from the root folder (for example, calling [dotnet run](/dotnet/core/tools/dotnet-run) from the project folder).</span></span> <span data-ttu-id="1634a-132">사용 되는 기본 이것이 [Visual Studio](https://www.visualstudio.com/) 및 [dotnet 새 템플릿을](/dotnet/core/tools/dotnet-new)합니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-132">This is the default used in [Visual Studio](https://www.visualstudio.com/) and the [dotnet new templates](/dotnet/core/tools/dotnet-new).</span></span>

<span data-ttu-id="1634a-133">참조 [ASP.NET Core에서 구성을](xref:fundamentals/configuration) 응용 프로그램 구성에 대 한 자세한 내용은 합니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-133">See [Configuration in ASP.NET Core](xref:fundamentals/configuration) for more information on app configuration.</span></span>

> [!NOTE]
> <span data-ttu-id="1634a-134">정적을 사용 하는 대신 `CreateDefaultBuilder` 에서 호스트를 만드는 메서드를 [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) 은 지원 되는 ASP.NET Core 방법을 2.x 합니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-134">As an alternative to using the static `CreateDefaultBuilder` method, creating a host from [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) is a supported approach with ASP.NET Core 2.x.</span></span> <span data-ttu-id="1634a-135">자세한 내용은 ASP.NET Core 1.x 탭을 참조 하십시오.</span><span class="sxs-lookup"><span data-stu-id="1634a-135">See the ASP.NET Core 1.x tab for more information.</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="1634a-136">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="1634a-136">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="1634a-137">인스턴스를 사용 하 여 호스트 만들기 [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder)합니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-137">Create a host using an instance of [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder).</span></span> <span data-ttu-id="1634a-138">이 일반적으로 응용 프로그램의 진입점에서 수행 되어는 `Main` 메서드.</span><span class="sxs-lookup"><span data-stu-id="1634a-138">This is typically performed in your app's entry point, the `Main` method.</span></span> <span data-ttu-id="1634a-139">프로젝트 템플릿에서 `Main` 에 위치한 *Program.cs*합니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-139">In the project templates, `Main` is located in *Program.cs*.</span></span> <span data-ttu-id="1634a-140">다음 *Program.cs* 사용 하는 방법을 보여 줍니다. `WebHostBuilder` 호스트 빌드하려면:</span><span class="sxs-lookup"><span data-stu-id="1634a-140">The following *Program.cs* demonstrates how to use `WebHostBuilder` to build the host:</span></span>

[!code-csharp[Main](../common/samples/WebApplication1/Program.cs)]

<span data-ttu-id="1634a-141">`WebHostBuilder`필요는 [IServer를 구현 하는 서버](servers/index.md)합니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-141">`WebHostBuilder` requires a [server that implements IServer](servers/index.md).</span></span> <span data-ttu-id="1634a-142">기본 제공 서버는 [Kestrel](servers/kestrel.md) 및 [HTTP.sys](servers/httpsys.md) (HTTP.sys ASP.NET 코어 2.0이 출시 되기 전에 호출 된 [WebListener](xref:fundamentals/servers/weblistener)).</span><span class="sxs-lookup"><span data-stu-id="1634a-142">The built-in servers are [Kestrel](servers/kestrel.md) and [HTTP.sys](servers/httpsys.md) (prior to the release of ASP.NET Core 2.0, HTTP.sys was called [WebListener](xref:fundamentals/servers/weblistener)).</span></span> <span data-ttu-id="1634a-143">이 예제는 [UseKestrel 확장 메서드](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel?view=aspnetcore-1.1) Kestrel 서버를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-143">In this example, the [UseKestrel extension method](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel?view=aspnetcore-1.1) specifies the Kestrel server.</span></span>

<span data-ttu-id="1634a-144">*콘텐츠 루트* 호스트 MVC 뷰 파일과 같은 콘텐츠 파일을 검색 하는 위치를 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-144">The *content root* determines where the host searches for content files, such as MVC view files.</span></span> <span data-ttu-id="1634a-145">에 제공 되는 기본 콘텐츠 루트 `UseContentRoot` 은 [Directory.GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory?view=netcore-1.1)합니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-145">The default content root supplied to `UseContentRoot` is [Directory.GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory?view=netcore-1.1).</span></span> <span data-ttu-id="1634a-146">이 인해 응용 프로그램 루트 폴더에서 시작 될 때 웹 프로젝트의 루트 폴더 콘텐츠 루트로 사용 하 여 (예를 들어 호출 [실행 dotnet](/dotnet/core/tools/dotnet-run) 프로젝트 폴더에서).</span><span class="sxs-lookup"><span data-stu-id="1634a-146">This results in using the web project's root folder as the content root when the app is started from the root folder (for example, calling [dotnet run](/dotnet/core/tools/dotnet-run) from the project folder).</span></span> <span data-ttu-id="1634a-147">사용 되는 기본 이것이 [Visual Studio](https://www.visualstudio.com/) 및 [dotnet 새 템플릿을](/dotnet/core/tools/dotnet-new)합니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-147">This is the default used in [Visual Studio](https://www.visualstudio.com/) and the [dotnet new templates](/dotnet/core/tools/dotnet-new).</span></span>

<span data-ttu-id="1634a-148">IIS는 역방향 프록시를 사용 하려면 호출 [UseIISIntegration](/aspnet/core/api/microsoft.aspnetcore.hosting.webhostbuilderiisextensions) 호스트 빌드 과정의 일환으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-148">To use IIS as a reverse proxy, call [UseIISIntegration](/aspnet/core/api/microsoft.aspnetcore.hosting.webhostbuilderiisextensions) as part of building the host.</span></span> <span data-ttu-id="1634a-149">`UseIISIntegration`구성 하지 않는 한 *서버*처럼 [UseKestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel?view=aspnetcore-1.1) 않습니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-149">`UseIISIntegration` doesn't configure a *server*, like [UseKestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel?view=aspnetcore-1.1) does.</span></span> <span data-ttu-id="1634a-150">`UseIISIntegration`구성 요소의 기본 경로 및 사용 하는 경우 서버에서 수신 대기 해야 하는 포트는 [ASP.NET Core 모듈](xref:fundamentals/servers/aspnet-core-module) Kestrel와 IIS 사이 역방향 프록시를 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-150">`UseIISIntegration` configures the base path and port the server should listen on when using the [ASP.NET Core Module](xref:fundamentals/servers/aspnet-core-module) to create a reverse-proxy between Kestrel and IIS.</span></span> <span data-ttu-id="1634a-151">IIS에서 ASP.NET Core를 사용 하려면 둘 다 지정 해야 하면 `UseKestrel` 및 `UseIISIntegration`합니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-151">To use IIS with ASP.NET Core, you must specify both `UseKestrel` and `UseIISIntegration`.</span></span> <span data-ttu-id="1634a-152">`UseIISIntegration`IIS 또는 IIS Express 뒤에서 실행 하는 경우에 활성화 됩니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-152">`UseIISIntegration` only activates when running behind IIS or IIS Express.</span></span> <span data-ttu-id="1634a-153">자세한 내용은 참조 [ASP.NET Core 모듈 소개](xref:fundamentals/servers/aspnet-core-module) 및 [ASP.NET Core 모듈 구성 참조](xref:hosting/aspnet-core-module)합니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-153">For more information, see [Introduction to ASP.NET Core Module](xref:fundamentals/servers/aspnet-core-module) and [ASP.NET Core Module configuration reference](xref:hosting/aspnet-core-module).</span></span>

<span data-ttu-id="1634a-154">호스트 (및 ASP.NET Core 응용 프로그램)를 구성 하는 최소 구현을 서버와 응용 프로그램의 요청 파이프라인의 구성을 지정 하는 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-154">A minimal implementation that configures a host (and an ASP.NET Core app) includes specifying a server and configuration of the app's request pipeline:</span></span>

```csharp
var host = new WebHostBuilder()
    .UseKestrel()
    .Configure(app =>
    {
        app.Run(context => context.Response.WriteAsync("Hello World!"));
    })
    .Build();

host.Run();
```

---

<span data-ttu-id="1634a-155">제공할 수는 호스트를 설정할 때 [구성](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configure?view=aspnetcore-1.1) 및 [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.configureservices?view=aspnetcore-1.1) 메서드.</span><span class="sxs-lookup"><span data-stu-id="1634a-155">When setting up a host, you can provide [Configure](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configure?view=aspnetcore-1.1) and [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.configureservices?view=aspnetcore-1.1) methods.</span></span> <span data-ttu-id="1634a-156">지정 하는 경우는 `Startup` 정의 해야 클래스는 `Configure` 메서드.</span><span class="sxs-lookup"><span data-stu-id="1634a-156">If you specify a `Startup` class, it must define a `Configure` method.</span></span> <span data-ttu-id="1634a-157">자세한 내용은 참조 [에서 ASP.NET Core 응용 프로그램 시작](startup.md)합니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-157">For more information, see [Application Startup in ASP.NET Core](startup.md).</span></span> <span data-ttu-id="1634a-158">여러 번 호출 `ConfigureServices` 서로에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-158">Multiple calls to `ConfigureServices` append to one another.</span></span> <span data-ttu-id="1634a-159">여러 번 호출 `Configure` 또는 `UseStartup` 에 `WebHostBuilder` 이전 설정을 대체 합니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-159">Multiple calls to `Configure` or `UseStartup` on the `WebHostBuilder` replace previous settings.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="1634a-160">호스트 구성 값</span><span class="sxs-lookup"><span data-stu-id="1634a-160">Host configuration values</span></span>

<span data-ttu-id="1634a-161">[WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) 와 직접 설정 될 수 있는 호스트에 대 한 대부분의 사용 가능한 구성 값을 설정 하기 위한 메서드를 제공 [UseSetting](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.usesetting) 와 연결 된 키입니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-161">[WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) provides methods for setting most of the available configuration values for the host, which can also be set directly with [UseSetting](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.usesetting) and the associated key.</span></span> <span data-ttu-id="1634a-162">사용 하 여 값을 설정할 때 `UseSetting`, 유형에 관계 없이 (따옴표로 묶음)을 문자열로 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-162">When setting a value with `UseSetting`, the value is set as a string (in quotes) regardless of the type.</span></span>

### <a name="capture-startup-errors"></a><span data-ttu-id="1634a-163">시작 오류 캡처</span><span class="sxs-lookup"><span data-stu-id="1634a-163">Capture Startup Errors</span></span>

<span data-ttu-id="1634a-164">이 설정은 시작 오류의 캡처를 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-164">This setting controls the capture of startup errors.</span></span>

<span data-ttu-id="1634a-165">**키**: captureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="1634a-165">**Key**: captureStartupErrors</span></span>  
<span data-ttu-id="1634a-166">**형식**: *bool* (`true` 또는 `1`)</span><span class="sxs-lookup"><span data-stu-id="1634a-166">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="1634a-167">**기본**: 기본값으로 `false` Kestrel 며 기본값은 IIS 뒤에 있는 앱이 실행 하지 않는 한 `true`합니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-167">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="1634a-168">**사용 하 여 설정**:`CaptureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="1634a-168">**Set using**: `CaptureStartupErrors`</span></span>

<span data-ttu-id="1634a-169">때 `false`, 종료 하는 호스트에서 시작 결과 동안 오류가 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-169">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="1634a-170">때 `true`, 호스트 시작 하는 동안 예외를 캡처하고 서버를 시작 하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-170">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="1634a-171">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="1634a-171">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```csharp
WebHost.CreateDefaultBuilder(args)
    .CaptureStartupErrors(true)
    ...
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="1634a-172">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="1634a-172">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

```csharp
var host = new WebHostBuilder()
    .CaptureStartupErrors(true)
    ...
```

---

### <a name="content-root"></a><span data-ttu-id="1634a-173">콘텐츠 루트</span><span class="sxs-lookup"><span data-stu-id="1634a-173">Content Root</span></span>

<span data-ttu-id="1634a-174">이 설정은 결정 ASP.NET Core MVC 뷰 등의 콘텐츠 파일을 검색 하기 시작 합니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-174">This setting determines where ASP.NET Core begins searching for content files, such as MVC views.</span></span> 

<span data-ttu-id="1634a-175">**키**: contentRoot</span><span class="sxs-lookup"><span data-stu-id="1634a-175">**Key**: contentRoot</span></span>  
<span data-ttu-id="1634a-176">**형식**: *문자열*</span><span class="sxs-lookup"><span data-stu-id="1634a-176">**Type**: *string*</span></span>  
<span data-ttu-id="1634a-177">**기본**: 응용 프로그램 어셈블리 파일이 있는 폴더를 기본값으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-177">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="1634a-178">**사용 하 여 설정**:`UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="1634a-178">**Set using**: `UseContentRoot`</span></span>

<span data-ttu-id="1634a-179">에 대 한 기본 경로로 콘텐츠 루트는 또한는 [웹 루트 설정을](#web-root)합니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-179">The content root is also used as the base path for the [Web Root setting](#web-root).</span></span> <span data-ttu-id="1634a-180">경로가 존재 하지 않는 경우 호스트가 시작 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-180">If the path doesn't exist, the host fails to start.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="1634a-181">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="1634a-181">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\mywebsite")
    ...
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="1634a-182">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="1634a-182">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

```csharp
var host = new WebHostBuilder()
    .UseContentRoot("c:\\mywebsite")
    ...
```

---

### <a name="detailed-errors"></a><span data-ttu-id="1634a-183">자세한 오류</span><span class="sxs-lookup"><span data-stu-id="1634a-183">Detailed Errors</span></span>

<span data-ttu-id="1634a-184">오류를 캡처해야 하나요 자세히 설명 하는 경우 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-184">Determines if detailed errors should be captured.</span></span>

<span data-ttu-id="1634a-185">**키**: detailedErrors</span><span class="sxs-lookup"><span data-stu-id="1634a-185">**Key**: detailedErrors</span></span>  
<span data-ttu-id="1634a-186">**형식**: *bool* (`true` 또는 `1`)</span><span class="sxs-lookup"><span data-stu-id="1634a-186">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="1634a-187">**기본**: false</span><span class="sxs-lookup"><span data-stu-id="1634a-187">**Default**: false</span></span>  
<span data-ttu-id="1634a-188">**사용 하 여 설정**:`UseSetting`</span><span class="sxs-lookup"><span data-stu-id="1634a-188">**Set using**: `UseSetting`</span></span>

<span data-ttu-id="1634a-189">사용 하는 경우 (또는 경우는 <a href="#environment">환경</a> 로 설정 되어 `Development`), 응용 프로그램 자세한 예외를 캡처합니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-189">When enabled (or when the <a href="#environment">Environment</a> is set to `Development`), the app captures detailed exceptions.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="1634a-190">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="1634a-190">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.DetailedErrorsKey, "true")
    ...
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="1634a-191">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="1634a-191">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

```csharp
var host = new WebHostBuilder()
    .UseSetting(WebHostDefaults.DetailedErrorsKey, "true")
    ...
```

---

### <a name="environment"></a><span data-ttu-id="1634a-192">환경</span><span class="sxs-lookup"><span data-stu-id="1634a-192">Environment</span></span>

<span data-ttu-id="1634a-193">앱의 환경을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-193">Sets the app's environment.</span></span>

<span data-ttu-id="1634a-194">**키**: 환경</span><span class="sxs-lookup"><span data-stu-id="1634a-194">**Key**: environment</span></span>  
<span data-ttu-id="1634a-195">**형식**: *문자열*</span><span class="sxs-lookup"><span data-stu-id="1634a-195">**Type**: *string*</span></span>  
<span data-ttu-id="1634a-196">**기본**: 프로덕션</span><span class="sxs-lookup"><span data-stu-id="1634a-196">**Default**: Production</span></span>  
<span data-ttu-id="1634a-197">**사용 하 여 설정**:`UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="1634a-197">**Set using**: `UseEnvironment`</span></span>

<span data-ttu-id="1634a-198">설정할 수 있습니다는 *환경* 값으로.</span><span class="sxs-lookup"><span data-stu-id="1634a-198">You can set the *Environment* to any value.</span></span> <span data-ttu-id="1634a-199">프레임 워크에서 정의 된 값 포함 `Development`, `Staging`, 및 `Production`합니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-199">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="1634a-200">값에 대/소문자 구분 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-200">Values aren't case sensitive.</span></span> <span data-ttu-id="1634a-201">기본적으로는 *환경* 에서 읽기는 `ASPNETCORE_ENVIRONMENT` 환경 변수입니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-201">By default, the *Environment* is read from the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span> <span data-ttu-id="1634a-202">사용 하는 경우 [Visual Studio](https://www.visualstudio.com/), 환경 변수를 설정할 수 있습니다는 *launchSettings.json* 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-202">When using [Visual Studio](https://www.visualstudio.com/), environment variables may be set in the *launchSettings.json* file.</span></span> <span data-ttu-id="1634a-203">자세한 내용은 [여러 환경 사용](xref:fundamentals/environments)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="1634a-203">For more information, see [Working with Multiple Environments](xref:fundamentals/environments).</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="1634a-204">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="1634a-204">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    ...
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="1634a-205">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="1634a-205">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

```csharp
var host = new WebHostBuilder()
    .UseEnvironment("Development")
    ...
```

---

### <a name="hosting-startup-assemblies"></a><span data-ttu-id="1634a-206">호스팅 시작 어셈블리</span><span class="sxs-lookup"><span data-stu-id="1634a-206">Hosting Startup Assemblies</span></span>

<span data-ttu-id="1634a-207">응용 프로그램의 호스팅 시작 어셈블리를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-207">Sets the app's hosting startup assemblies.</span></span>

<span data-ttu-id="1634a-208">**키**: hostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="1634a-208">**Key**: hostingStartupAssemblies</span></span>  
<span data-ttu-id="1634a-209">**형식**: *문자열*</span><span class="sxs-lookup"><span data-stu-id="1634a-209">**Type**: *string*</span></span>  
<span data-ttu-id="1634a-210">**기본**: 빈 문자열</span><span class="sxs-lookup"><span data-stu-id="1634a-210">**Default**: Empty string</span></span>  
<span data-ttu-id="1634a-211">**사용 하 여 설정**:`UseSetting`</span><span class="sxs-lookup"><span data-stu-id="1634a-211">**Set using**: `UseSetting`</span></span>

<span data-ttu-id="1634a-212">호스팅 시작 로드할 어셈블리를 시작할 때의 세미콜론으로 구분 된 문자열입니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-212">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="1634a-213">이 기능은 ASP.NET 코어 2.0의 새로운 기능입니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-213">This feature is new in ASP.NET Core 2.0.</span></span>

<span data-ttu-id="1634a-214">구성 기본값을 빈 문자열로 있지만 호스팅 시작 어셈블리는 항상 응용 프로그램의 어셈블리를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-214">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="1634a-215">호스팅 시작 어셈블리를 제공 하면 해당 파일은 응용 프로그램 시작 하는 동안 일반적인 서비스를 빌드할 때 로드에 대 한 응용 프로그램의 어셈블리에 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-215">When you provide hosting startup assemblies, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="1634a-216">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="1634a-216">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2")
    ...
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="1634a-217">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="1634a-217">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="1634a-218">이 기능은 ASP.NET Core에서 사용할 수 없는 1.x 합니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-218">This feature is unavailable in ASP.NET Core 1.x.</span></span>

---

### <a name="prefer-hosting-urls"></a><span data-ttu-id="1634a-219">Url을 호스트 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-219">Prefer Hosting URLs</span></span>

<span data-ttu-id="1634a-220">호스트와 구성 된 Url을 수신 해야 하는지 여부를 나타냅니다는 `WebHostBuilder` 대신 사용 하 여 구성 하는 것은 `IServer` 구현 합니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-220">Indicates whether the host should listen on the URLs configured with the `WebHostBuilder` instead of those configured with the `IServer` implementation.</span></span>

<span data-ttu-id="1634a-221">**키**: preferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="1634a-221">**Key**: preferHostingUrls</span></span>  
<span data-ttu-id="1634a-222">**형식**: *bool* (`true` 또는 `1`)</span><span class="sxs-lookup"><span data-stu-id="1634a-222">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="1634a-223">**기본**: true</span><span class="sxs-lookup"><span data-stu-id="1634a-223">**Default**: true</span></span>  
<span data-ttu-id="1634a-224">**사용 하 여 설정**:`PreferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="1634a-224">**Set using**: `PreferHostingUrls`</span></span>

<span data-ttu-id="1634a-225">이 기능은 ASP.NET 코어 2.0의 새로운 기능입니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-225">This feature is new in ASP.NET Core 2.0.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="1634a-226">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="1634a-226">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```csharp
WebHost.CreateDefaultBuilder(args)
    .PreferHostingUrls(false)
    ...
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="1634a-227">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="1634a-227">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="1634a-228">이 기능은 ASP.NET Core에서 사용할 수 없는 1.x 합니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-228">This feature is unavailable in ASP.NET Core 1.x.</span></span>

---

### <a name="prevent-hosting-startup"></a><span data-ttu-id="1634a-229">호스팅 시작 방지</span><span class="sxs-lookup"><span data-stu-id="1634a-229">Prevent Hosting Startup</span></span>

<span data-ttu-id="1634a-230">호스팅 시작 어셈블리, 응용 프로그램의 어셈블리를 포함 하 여 자동 로드를 방지 합니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-230">Prevents the automatic loading of hosting startup assemblies, including the app's assembly.</span></span>

<span data-ttu-id="1634a-231">**키**: preventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="1634a-231">**Key**: preventHostingStartup</span></span>  
<span data-ttu-id="1634a-232">**형식**: *bool* (`true` 또는 `1`)</span><span class="sxs-lookup"><span data-stu-id="1634a-232">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="1634a-233">**기본**: false</span><span class="sxs-lookup"><span data-stu-id="1634a-233">**Default**: false</span></span>  
<span data-ttu-id="1634a-234">**사용 하 여 설정**:`UseSetting`</span><span class="sxs-lookup"><span data-stu-id="1634a-234">**Set using**: `UseSetting`</span></span>

<span data-ttu-id="1634a-235">이 기능은 ASP.NET 코어 2.0의 새로운 기능입니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-235">This feature is new in ASP.NET Core 2.0.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="1634a-236">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="1634a-236">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.PreventHostingStartupKey, "true")
    ...
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="1634a-237">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="1634a-237">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="1634a-238">이 기능은 ASP.NET Core에서 사용할 수 없는 1.x 합니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-238">This feature is unavailable in ASP.NET Core 1.x.</span></span>

---

### <a name="server-urls"></a><span data-ttu-id="1634a-239">서버 Url</span><span class="sxs-lookup"><span data-stu-id="1634a-239">Server URLs</span></span>

<span data-ttu-id="1634a-240">IP 주소 또는 서버에서 요청을 수신 해야 하는 포트와 프로토콜 인 호스트 주소를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-240">Indicates the IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span>

<span data-ttu-id="1634a-241">**키**: url</span><span class="sxs-lookup"><span data-stu-id="1634a-241">**Key**: urls</span></span>  
<span data-ttu-id="1634a-242">**형식**: *문자열*</span><span class="sxs-lookup"><span data-stu-id="1634a-242">**Type**: *string*</span></span>  
<span data-ttu-id="1634a-243">**기본**: http://localhost:5000</span><span class="sxs-lookup"><span data-stu-id="1634a-243">**Default**: http://localhost:5000</span></span>  
<span data-ttu-id="1634a-244">**사용 하 여 설정**:`UseUrls`</span><span class="sxs-lookup"><span data-stu-id="1634a-244">**Set using**: `UseUrls`</span></span>

<span data-ttu-id="1634a-245">세미콜론으로 구분 된로 설정 (;) URL의 목록 prefixes 서버 응답 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-245">Set to a semicolon-separated (;) list of URL prefixes to which the server should respond.</span></span> <span data-ttu-id="1634a-246">예를 들어, `http://localhost:123`을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-246">For example, `http://localhost:123`.</span></span> <span data-ttu-id="1634a-247">사용 하 여 "\*" 서버 모든 IP 주소 또는 지정 된 포트 및 프로토콜을 사용 하 여 호스트 이름에 대 한 요청에 대 한 수신 대기 해야 함을 나타내기 위해 (예를 들어 `http://*:5000`).</span><span class="sxs-lookup"><span data-stu-id="1634a-247">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="1634a-248">프로토콜 (`http://` 또는 `https://`) 각 URL에 포함 되어 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-248">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="1634a-249">지원 되는 형식의 서버 마다 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-249">Supported formats vary between servers.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="1634a-250">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="1634a-250">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002")
    ...
```

<span data-ttu-id="1634a-251">Kestrel 자체 끝점 구성 API에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-251">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="1634a-252">자세한 내용은 [ASP.NET Core의 Kestrel 웹 서버 구현](xref:fundamentals/servers/kestrel?tabs=aspnetcore2x#endpoint-configuration)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="1634a-252">For more information, see [Kestrel web server implementation in ASP.NET Core](xref:fundamentals/servers/kestrel?tabs=aspnetcore2x#endpoint-configuration).</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="1634a-253">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="1634a-253">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

```csharp
var host = new WebHostBuilder()
    .UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002")
    ...
```

---

### <a name="shutdown-timeout"></a><span data-ttu-id="1634a-254">시스템 종료 제한 시간</span><span class="sxs-lookup"><span data-stu-id="1634a-254">Shutdown Timeout</span></span>

<span data-ttu-id="1634a-255">웹 호스트를 종료 될 때까지 기다리는 시간을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-255">Specifies the amount of time to wait for the web host to shutdown.</span></span>

<span data-ttu-id="1634a-256">**키**: shutdownTimeoutSeconds</span><span class="sxs-lookup"><span data-stu-id="1634a-256">**Key**: shutdownTimeoutSeconds</span></span>  
<span data-ttu-id="1634a-257">**형식**: *int*</span><span class="sxs-lookup"><span data-stu-id="1634a-257">**Type**: *int*</span></span>  
<span data-ttu-id="1634a-258">**기본**: 5</span><span class="sxs-lookup"><span data-stu-id="1634a-258">**Default**: 5</span></span>  
<span data-ttu-id="1634a-259">**사용 하 여 설정**:`UseShutdownTimeout`</span><span class="sxs-lookup"><span data-stu-id="1634a-259">**Set using**: `UseShutdownTimeout`</span></span>

<span data-ttu-id="1634a-260">키를 허용 하지만 *int* 와 `UseSetting` (예를 들어 `.UseSetting(WebHostDefaults.ShutdownTimeoutKey, "10")`), `UseShutdownTimeout` 확장 메서드를 사용는 `TimeSpan`합니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-260">Although the key accepts an *int* with `UseSetting` (for example, `.UseSetting(WebHostDefaults.ShutdownTimeoutKey, "10")`), the `UseShutdownTimeout` extension method takes a `TimeSpan`.</span></span> <span data-ttu-id="1634a-261">이 기능은 ASP.NET 코어 2.0의 새로운 기능입니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-261">This feature is new in ASP.NET Core 2.0.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="1634a-262">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="1634a-262">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseShutdownTimeout(TimeSpan.FromSeconds(10))
    ...
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="1634a-263">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="1634a-263">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="1634a-264">이 기능은 ASP.NET Core에서 사용할 수 없는 1.x 합니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-264">This feature is unavailable in ASP.NET Core 1.x.</span></span>

---

### <a name="startup-assembly"></a><span data-ttu-id="1634a-265">시작 어셈블리</span><span class="sxs-lookup"><span data-stu-id="1634a-265">Startup Assembly</span></span>

<span data-ttu-id="1634a-266">검색할 어셈블리 확인에서 `Startup` 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-266">Determines the assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="1634a-267">**키**: startupAssembly</span><span class="sxs-lookup"><span data-stu-id="1634a-267">**Key**: startupAssembly</span></span>  
<span data-ttu-id="1634a-268">**형식**: *문자열*</span><span class="sxs-lookup"><span data-stu-id="1634a-268">**Type**: *string*</span></span>  
<span data-ttu-id="1634a-269">**기본**: 응용 프로그램의 어셈블리</span><span class="sxs-lookup"><span data-stu-id="1634a-269">**Default**: The app's assembly</span></span>  
<span data-ttu-id="1634a-270">**사용 하 여 설정**:`UseStartup`</span><span class="sxs-lookup"><span data-stu-id="1634a-270">**Set using**: `UseStartup`</span></span>

<span data-ttu-id="1634a-271">이름으로 어셈블리를 참조할 수 있습니다 (`string`) 또는 형식 (`TStartup`).</span><span class="sxs-lookup"><span data-stu-id="1634a-271">You can reference the assembly by name (`string`) or type (`TStartup`).</span></span> <span data-ttu-id="1634a-272">여러 개인 경우 `UseStartup` 메서드가 호출 되어, 마지막 우선적으로 적용 합니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-272">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="1634a-273">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="1634a-273">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseStartup("StartupAssemblyName")
    ...
```

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseStartup<TStartup>()
    ...
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="1634a-274">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="1634a-274">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

```csharp
var host = new WebHostBuilder()
    .UseStartup("StartupAssemblyName")
    ...
```

```csharp
var host = new WebHostBuilder()
    .UseStartup<TStartup>()
    ...
```

---

### <a name="web-root"></a><span data-ttu-id="1634a-275">웹 루트</span><span class="sxs-lookup"><span data-stu-id="1634a-275">Web Root</span></span>

<span data-ttu-id="1634a-276">응용 프로그램의 정적 자산에 대 한 상대 경로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-276">Sets the relative path to the app's static assets.</span></span>

<span data-ttu-id="1634a-277">**키**: webroot</span><span class="sxs-lookup"><span data-stu-id="1634a-277">**Key**: webroot</span></span>  
<span data-ttu-id="1634a-278">**형식**: *문자열*</span><span class="sxs-lookup"><span data-stu-id="1634a-278">**Type**: *string*</span></span>  
<span data-ttu-id="1634a-279">**기본**: 기본값은 "(Content Root)/wwwroot 지정 하지 않으면" 경로가 존재 하는 경우.</span><span class="sxs-lookup"><span data-stu-id="1634a-279">**Default**: If not specified, the default is "(Content Root)/wwwroot", if the path exists.</span></span> <span data-ttu-id="1634a-280">경로가 존재 하지 않는 경우 아무 파일 공급자가 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-280">If the path doesn't exist, then a no-op file provider is used.</span></span>  
<span data-ttu-id="1634a-281">**사용 하 여 설정**:`UseWebRoot`</span><span class="sxs-lookup"><span data-stu-id="1634a-281">**Set using**: `UseWebRoot`</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="1634a-282">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="1634a-282">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseWebRoot("public")
    ...
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="1634a-283">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="1634a-283">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

```csharp
var host = new WebHostBuilder()
    .UseWebRoot("public")
    ...
```

---

## <a name="overriding-configuration"></a><span data-ttu-id="1634a-284">구성을 무시</span><span class="sxs-lookup"><span data-stu-id="1634a-284">Overriding configuration</span></span>

<span data-ttu-id="1634a-285">사용 하 여 [구성](configuration.md) 호스트를 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-285">Use [Configuration](configuration.md) to configure the host.</span></span> <span data-ttu-id="1634a-286">다음 예제에서는 호스트 구성을 필요에 따라 지정에 *hosting.json* 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-286">In the following example, host configuration is optionally specified in a *hosting.json* file.</span></span> <span data-ttu-id="1634a-287">모든 구성에서 로드 된 *hosting.json* 명령줄 인수 파일을 덮어쓸 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-287">Any configuration loaded from the *hosting.json* file may be overridden by command-line arguments.</span></span> <span data-ttu-id="1634a-288">기본 제공된 구성 (에 `config`) 사용 하 여 호스트를 구성 하는 데는 `UseConfiguration`합니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-288">The built configuration (in `config`) is used to configure the host with `UseConfiguration`.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="1634a-289">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="1634a-289">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="1634a-290">*hosting.json*:</span><span class="sxs-lookup"><span data-stu-id="1634a-290">*hosting.json*:</span></span>

```json
{
    urls: "http://*:5005"
}
```

<span data-ttu-id="1634a-291">제공 하는 구성을 재정의 `UseUrls` 와 *hosting.json* config 첫 번째, 명령줄 인수 구성 두 번째:</span><span class="sxs-lookup"><span data-stu-id="1634a-291">Overriding the configuration provided by `UseUrls` with *hosting.json* config first, command-line argument config second:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        BuildWebHost(args).Run();
    }

    public static IWebHost BuildWebHost(string[] args)
    {
        var config = new ConfigurationBuilder()
            .SetBasePath(Directory.GetCurrentDirectory())
            .AddJsonFile("hosting.json", optional: true)
            .AddCommandLine(args)
            .Build();

        return WebHost.CreateDefaultBuilder(args)
            .UseUrls("http://*:5000")
            .UseConfiguration(config)
            .Configure(app =>
            {
                app.Run(context => 
                    context.Response.WriteAsync("Hello, World!"));
            })
            .Build();
    }
}
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="1634a-292">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="1634a-292">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="1634a-293">*hosting.json*:</span><span class="sxs-lookup"><span data-stu-id="1634a-293">*hosting.json*:</span></span>

```json
{
    urls: "http://*:5005"
}
```

<span data-ttu-id="1634a-294">제공 하는 구성을 재정의 `UseUrls` 와 *hosting.json* config 첫 번째, 명령줄 인수 구성 두 번째:</span><span class="sxs-lookup"><span data-stu-id="1634a-294">Overriding the configuration provided by `UseUrls` with *hosting.json* config first, command-line argument config second:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        var config = new ConfigurationBuilder()
            .SetBasePath(Directory.GetCurrentDirectory())
            .AddJsonFile("hosting.json", optional: true)
            .AddCommandLine(args)
            .Build();

        var host = new WebHostBuilder()
            .UseUrls("http://*:5000")
            .UseConfiguration(config)
            .UseKestrel()
            .Configure(app =>
            {
                app.Run(context => 
                    context.Response.WriteAsync("Hello, World!"));
            })
            .Build();

        host.Run();
    }
}
```

---

> [!NOTE]
> <span data-ttu-id="1634a-295">`UseConfiguration` 확장 메서드는 현재에서 반환 되는 구성 섹션을 구문 분석할 수 없습니다 `GetSection` (예를 들어 `.UseConfiguration(Configuration.GetSection("section"))`합니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-295">The `UseConfiguration` extension method isn't currently capable of parsing a configuration section returned by `GetSection` (for example, `.UseConfiguration(Configuration.GetSection("section"))`.</span></span> <span data-ttu-id="1634a-296">`GetSection` 메서드 요청 섹션에 있는 구성 키를 필터링 되지만 섹션 이름에는 키 (예를 들어 `section:urls`, `section:environment`).</span><span class="sxs-lookup"><span data-stu-id="1634a-296">The `GetSection` method filters the configuration keys to the section requested but leaves the section name on the keys (for example, `section:urls`, `section:environment`).</span></span> <span data-ttu-id="1634a-297">`UseConfiguration` 메서드에서 예상 키와 일치 하도록는 `WebHostBuilder` 키 (예를 들어 `urls`, `environment`).</span><span class="sxs-lookup"><span data-stu-id="1634a-297">The `UseConfiguration` method expects the keys to match the `WebHostBuilder` keys (for example, `urls`, `environment`).</span></span> <span data-ttu-id="1634a-298">키에 대해 섹션 이름의 존재는 호스트 구성에서 섹션의 값을 방지 합니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-298">The presence of the section name on the keys prevents the section's values from configuring the host.</span></span> <span data-ttu-id="1634a-299">이 문제는 향후 릴리스에서 해결될 예정입니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-299">This issue will be addressed in an upcoming release.</span></span> <span data-ttu-id="1634a-300">자세한 내용 및 해결 방법에 대 한 참조 [전체 키를 사용 하 여 WebHostBuilder.UseConfiguration에 구성 섹션을 전달](https://github.com/aspnet/Hosting/issues/839)합니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-300">For more information and workarounds, see [Passing configuration section into WebHostBuilder.UseConfiguration uses full keys](https://github.com/aspnet/Hosting/issues/839).</span></span>

<span data-ttu-id="1634a-301">특정 URL에서 실행 하는 호스트를 지정 하려면 전달할 수 있습니다는 원하는 값에 명령 프롬프트에서 실행할 때 `dotnet run`합니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-301">To specify the host run on a particular URL, you could pass in the desired value from a command prompt when executing `dotnet run`.</span></span> <span data-ttu-id="1634a-302">명령줄 인수 재정의 `urls` 에서 값의 *hosting.json* 포트 8080에서 수신 하는 파일 및 서버:</span><span class="sxs-lookup"><span data-stu-id="1634a-302">The command-line argument overrides the `urls` value from the *hosting.json* file, and the server listens on port 8080:</span></span>

```console
dotnet run --urls "http://*:8080"
```

## <a name="ordering-importance"></a><span data-ttu-id="1634a-303">순서 중요성</span><span class="sxs-lookup"><span data-stu-id="1634a-303">Ordering importance</span></span>

<span data-ttu-id="1634a-304">일부는 `WebHostBuilder` 설정을 먼저 읽습니다에서 환경 변수를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-304">Some of the `WebHostBuilder` settings are first read from environment variables, if set.</span></span> <span data-ttu-id="1634a-305">이러한 환경 변수 형식을 사용 하 여 `ASPNETCORE_{configurationKey}`합니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-305">These environment variables use the format `ASPNETCORE_{configurationKey}`.</span></span> <span data-ttu-id="1634a-306">설정 하면 기본적으로에서 서버가 수신 하는 Url을 설정 하려면 `ASPNETCORE_URLS`합니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-306">To set the URLs that the server listens on by default, you set `ASPNETCORE_URLS`.</span></span>

<span data-ttu-id="1634a-307">구성을 지정 하 여 이러한 환경 변수 값을 재정의할 수 있습니다 (사용 하 여 `UseConfiguration`) 또는 값을 명시적으로 설정 하 여 (사용 하 여 `UseSetting` 명시적 확장 메서드 중 하나 또는 같은 `UseUrls`).</span><span class="sxs-lookup"><span data-stu-id="1634a-307">You can override any of these environment variable values by specifying configuration (using `UseConfiguration`) or by setting the value explicitly (using `UseSetting` or one of the explicit extension methods, such as `UseUrls`).</span></span> <span data-ttu-id="1634a-308">호스트 선택한 옵션에 설정 하는 마지막 값을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-308">The host uses whichever option sets the value last.</span></span> <span data-ttu-id="1634a-309">프로그래밍 방식으로 값 중 하나에 기본 URL을 설정 되었지만 구성으로 재정의 될 수 있도록 하려는 경우 URL을 설정 후 명령줄 구성을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-309">If you want to programmatically set the default URL to one value but allow it to be overridden with configuration, you can use command-line configuration after setting the URL.</span></span> <span data-ttu-id="1634a-310">참조 [덮어씁니다 구성](#overriding-configuration)합니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-310">See [Overriding configuration](#overriding-configuration).</span></span>

## <a name="starting-the-host"></a><span data-ttu-id="1634a-311">호스트를 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-311">Starting the host</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="1634a-312">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="1634a-312">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="1634a-313">**실행**</span><span class="sxs-lookup"><span data-stu-id="1634a-313">**Run**</span></span>

<span data-ttu-id="1634a-314">`Run` 메서드 웹 앱을 시작 하 고 호스트 종료 될 때까지 호출 스레드를 차단 합니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-314">The `Run` method starts the web app and blocks the calling thread until the host is shutdown:</span></span>

```csharp
host.Run();
```

<span data-ttu-id="1634a-315">**Start**</span><span class="sxs-lookup"><span data-stu-id="1634a-315">**Start**</span></span>

<span data-ttu-id="1634a-316">호출 하 여 비블로킹 방식으로 호스트를 실행할 수 있습니다는 `Start` 메서드:</span><span class="sxs-lookup"><span data-stu-id="1634a-316">You can run the host in a non-blocking manner by calling its `Start` method:</span></span>

```csharp
using (host)
{
    host.Start();
    Console.ReadLine();
}
```

<span data-ttu-id="1634a-317">에 대 한 Url 목록을 전달 하는 경우는 `Start` 지정 된 Url에서 수신 대기 메서드:</span><span class="sxs-lookup"><span data-stu-id="1634a-317">If you pass a list of URLs to the `Start` method, it listens on the URLs specified:</span></span>

```csharp
var urls = new List<string>()
{
    "http://*:5000",
    "http://localhost:5001"
};

var host = new WebHostBuilder()
    .UseKestrel()
    .UseStartup<Startup>()
    .Start(urls.ToArray());

using (host)
{
    Console.ReadLine();
}
```

<span data-ttu-id="1634a-318">초기화 하 고의 미리 구성 된 기본값을 사용 하 여 새 호스트를 시작할 수 있습니다 `CreateDefaultBuilder` 정적 편의 메서드를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-318">You can initialize and start a new host using the pre-configured defaults of `CreateDefaultBuilder` using a static convenience method.</span></span> <span data-ttu-id="1634a-319">이러한 메서드는을 콘솔 출력 하지 않고 서버를 시작 [WaitForShutdown](/dotnet/api/microsoft.aspnetcore.hosting.webhostextensions.waitforshutdown) (Ctrl-C/SIGINT 또는 SIGTERM) 중단 될 때까지 기다리는:</span><span class="sxs-lookup"><span data-stu-id="1634a-319">These methods start the server without console output and with [WaitForShutdown](/dotnet/api/microsoft.aspnetcore.hosting.webhostextensions.waitforshutdown) wait for a break (Ctrl-C/SIGINT or SIGTERM):</span></span>

<span data-ttu-id="1634a-320">**시작 (RequestDelegate 앱)**</span><span class="sxs-lookup"><span data-stu-id="1634a-320">**Start(RequestDelegate app)**</span></span>

<span data-ttu-id="1634a-321">로 시작는 `RequestDelegate`:</span><span class="sxs-lookup"><span data-stu-id="1634a-321">Start with a `RequestDelegate`:</span></span>

```csharp
using (var host = WebHost.Start(app => app.Response.WriteAsync("Hello, World!")))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="1634a-322">브라우저에서 요청 수행 `http://localhost:5000` "Hello World!" 응답을 받을 수</span><span class="sxs-lookup"><span data-stu-id="1634a-322">Make a request in the browser to `http://localhost:5000` to receive the response "Hello World!"</span></span> <span data-ttu-id="1634a-323">`WaitForShutdown`(Ctrl-C/SIGINT 또는 SIGTERM) 중단이 발생 될 때까지 차단 됩니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-323">`WaitForShutdown` blocks until a break (Ctrl-C/SIGINT or SIGTERM) is issued.</span></span> <span data-ttu-id="1634a-324">응용 프로그램 표시는 `Console.WriteLine` 메시지 및 대기 키 누르기를 종료 합니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-324">The app displays the `Console.WriteLine` message and waits for a keypress to exit.</span></span>

<span data-ttu-id="1634a-325">**시작 (string url, RequestDelegate 앱)**</span><span class="sxs-lookup"><span data-stu-id="1634a-325">**Start(string url, RequestDelegate app)**</span></span>

<span data-ttu-id="1634a-326">URL로 시작 하 고 `RequestDelegate`:</span><span class="sxs-lookup"><span data-stu-id="1634a-326">Start with a URL and `RequestDelegate`:</span></span>

```csharp
using (var host = WebHost.Start("http://localhost:8080", app => app.Response.WriteAsync("Hello, World!")))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="1634a-327">것과 동일한 결과가 생성 **시작 (RequestDelegate app)**를 제외 하 고 응용 프로그램에 대해 응답 `http://localhost:8080`합니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-327">Produces the same result as **Start(RequestDelegate app)**, except the app responds on `http://localhost:8080`.</span></span>

<span data-ttu-id="1634a-328">**시작 (동작<IRouteBuilder> routeBuilder)**</span><span class="sxs-lookup"><span data-stu-id="1634a-328">**Start(Action<IRouteBuilder> routeBuilder)**</span></span>

<span data-ttu-id="1634a-329">인스턴스를 사용 하 여 `IRouteBuilder` ([Microsoft.AspNetCore.Routing](https://www.nuget.org/packages/Microsoft.AspNetCore.Routing/)) 라우팅 미들웨어를 사용 하려면:</span><span class="sxs-lookup"><span data-stu-id="1634a-329">Use an instance of `IRouteBuilder` ([Microsoft.AspNetCore.Routing](https://www.nuget.org/packages/Microsoft.AspNetCore.Routing/)) to use routing middleware:</span></span>

```csharp
using (var host = WebHost.Start(router => router
    .MapGet("hello/{name}", (req, res, data) => 
        res.WriteAsync($"Hello, {data.Values["name"]}!"))
    .MapGet("buenosdias/{name}", (req, res, data) => 
        res.WriteAsync($"Buenos dias, {data.Values["name"]}!"))
    .MapGet("throw/{message?}", (req, res, data) => 
        throw new Exception((string)data.Values["message"] ?? "Uh oh!"))
    .MapGet("{greeting}/{name}", (req, res, data) => 
        res.WriteAsync($"{data.Values["greeting"]}, {data.Values["name"]}!"))
    .MapGet("", (req, res, data) => res.WriteAsync("Hello, World!"))))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="1634a-330">이 예제에서는 다음 브라우저 요청에서 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-330">Use the following browser requests with the example:</span></span>

| <span data-ttu-id="1634a-331">요청</span><span class="sxs-lookup"><span data-stu-id="1634a-331">Request</span></span>                                    | <span data-ttu-id="1634a-332">응답</span><span class="sxs-lookup"><span data-stu-id="1634a-332">Response</span></span>                                 |
| ------------------------------------------ | ---------------------------------------- |
| `http://localhost:5000/hello/Martin`       | <span data-ttu-id="1634a-333">Hello, Martin!</span><span class="sxs-lookup"><span data-stu-id="1634a-333">Hello, Martin!</span></span>                           |
| `http://localhost:5000/buenosdias/Catrina` | <span data-ttu-id="1634a-334">Catrina 부에노스아이레스 dias!</span><span class="sxs-lookup"><span data-stu-id="1634a-334">Buenos dias, Catrina!</span></span>                    |
| `http://localhost:5000/throw/ooops!`       | <span data-ttu-id="1634a-335">"Ooops!" 문자열을 사용 하 여 예외를 throw합니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-335">Throws an exception with string "ooops!"</span></span> |
| `http://localhost:5000/throw`              | <span data-ttu-id="1634a-336">문자열을 사용 하 여 예외를 throw "Uh 오!"</span><span class="sxs-lookup"><span data-stu-id="1634a-336">Throws an exception with string "Uh oh!"</span></span> |
| `http://localhost:5000/Sante/Kevin`        | <span data-ttu-id="1634a-337">Sante, Kevin!</span><span class="sxs-lookup"><span data-stu-id="1634a-337">Sante, Kevin!</span></span>                            |
| `http://localhost:5000`                    | <span data-ttu-id="1634a-338">Hello World!</span><span class="sxs-lookup"><span data-stu-id="1634a-338">Hello World!</span></span>                             |

<span data-ttu-id="1634a-339">`WaitForShutdown`(Ctrl-C/SIGINT 또는 SIGTERM) 중단이 발생 될 때까지 차단 됩니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-339">`WaitForShutdown` blocks until a break (Ctrl-C/SIGINT or SIGTERM) is issued.</span></span> <span data-ttu-id="1634a-340">응용 프로그램 표시는 `Console.WriteLine` 메시지 및 대기 키 누르기를 종료 합니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-340">The app displays the `Console.WriteLine` message and waits for a keypress to exit.</span></span>

<span data-ttu-id="1634a-341">**시작 (문자열 url 동작<IRouteBuilder> routeBuilder)**</span><span class="sxs-lookup"><span data-stu-id="1634a-341">**Start(string url, Action<IRouteBuilder> routeBuilder)**</span></span>

<span data-ttu-id="1634a-342">URL과의 인스턴스를 사용 하 여 `IRouteBuilder`:</span><span class="sxs-lookup"><span data-stu-id="1634a-342">Use a URL and an instance of `IRouteBuilder`:</span></span>

```csharp
using (var host = WebHost.Start("http://localhost:8080", router => router
    .MapGet("hello/{name}", (req, res, data) => 
        res.WriteAsync($"Hello, {data.Values["name"]}!"))
    .MapGet("buenosdias/{name}", (req, res, data) => 
        res.WriteAsync($"Buenos dias, {data.Values["name"]}!"))
    .MapGet("throw/{message?}", (req, res, data) => 
        throw new Exception((string)data.Values["message"] ?? "Uh oh!"))
    .MapGet("{greeting}/{name}", (req, res, data) => 
        res.WriteAsync($"{data.Values["greeting"]}, {data.Values["name"]}!"))
    .MapGet("", (req, res, data) => res.WriteAsync("Hello, World!"))))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="1634a-343">것과 동일한 결과가 생성 **시작 (동작<IRouteBuilder> routeBuilder)**를 제외 하 고 응용 프로그램에서 응답 `http://localhost:8080`합니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-343">Produces the same result as **Start(Action<IRouteBuilder> routeBuilder)**, except the app responds at `http://localhost:8080`.</span></span>

<span data-ttu-id="1634a-344">**StartWith (동작<IApplicationBuilder> 앱)**</span><span class="sxs-lookup"><span data-stu-id="1634a-344">**StartWith(Action<IApplicationBuilder> app)**</span></span>

<span data-ttu-id="1634a-345">구성 하는 대리자를 제공는 `IApplicationBuilder`:</span><span class="sxs-lookup"><span data-stu-id="1634a-345">Provide a delegate to configure an `IApplicationBuilder`:</span></span>

```csharp
using (var host = WebHost.StartWith(app => 
    app.Use(next => 
    {
        return async context => 
        {
            await context.Response.WriteAsync("Hello World!");
        };
    })))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="1634a-346">브라우저에서 요청 수행 `http://localhost:5000` "Hello World!" 응답을 받을 수</span><span class="sxs-lookup"><span data-stu-id="1634a-346">Make a request in the browser to `http://localhost:5000` to receive the response "Hello World!"</span></span> <span data-ttu-id="1634a-347">`WaitForShutdown`(Ctrl-C/SIGINT 또는 SIGTERM) 중단이 발생 될 때까지 차단 됩니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-347">`WaitForShutdown` blocks until a break (Ctrl-C/SIGINT or SIGTERM) is issued.</span></span> <span data-ttu-id="1634a-348">응용 프로그램 표시는 `Console.WriteLine` 메시지 및 대기 키 누르기를 종료 합니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-348">The app displays the `Console.WriteLine` message and waits for a keypress to exit.</span></span>

<span data-ttu-id="1634a-349">**StartWith (문자열 url 동작<IApplicationBuilder> 앱)**</span><span class="sxs-lookup"><span data-stu-id="1634a-349">**StartWith(string url, Action<IApplicationBuilder> app)**</span></span>

<span data-ttu-id="1634a-350">URL을 구성 하기 위한 대리자를 제공는 `IApplicationBuilder`:</span><span class="sxs-lookup"><span data-stu-id="1634a-350">Provide a URL and a delegate to configure an `IApplicationBuilder`:</span></span>

```csharp
using (var host = WebHost.StartWith("http://localhost:8080", app => 
    app.Use(next => 
    {
        return async context => 
        {
            await context.Response.WriteAsync("Hello World!");
        };
    })))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="1634a-351">것과 동일한 결과가 생성 **StartWith (동작<IApplicationBuilder> 응용 프로그램)**를 제외 하 고 응용 프로그램에 대해 응답 `http://localhost:8080`합니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-351">Produces the same result as **StartWith(Action<IApplicationBuilder> app)**, except the app responds on `http://localhost:8080`.</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="1634a-352">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="1634a-352">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="1634a-353">**실행**</span><span class="sxs-lookup"><span data-stu-id="1634a-353">**Run**</span></span>

<span data-ttu-id="1634a-354">`Run` 메서드 웹 앱을 시작 하 고 호스트 종료 될 때까지 호출 스레드를 차단 합니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-354">The `Run` method starts the web app and blocks the calling thread until the host is shutdown:</span></span>

```csharp
host.Run();
```

<span data-ttu-id="1634a-355">**Start**</span><span class="sxs-lookup"><span data-stu-id="1634a-355">**Start**</span></span>

<span data-ttu-id="1634a-356">호출 하 여 비블로킹 방식으로 호스트를 실행할 수 있습니다는 `Start` 메서드:</span><span class="sxs-lookup"><span data-stu-id="1634a-356">You can run the host in a non-blocking manner by calling its `Start` method:</span></span>

```csharp
using (host)
{
    host.Start();
    Console.ReadLine();
}
```

<span data-ttu-id="1634a-357">에 대 한 Url 목록을 전달 하는 경우는 `Start` 지정 된 Url에서 수신 대기 메서드:</span><span class="sxs-lookup"><span data-stu-id="1634a-357">If you pass a list of URLs to the `Start` method, it listens on the URLs specified:</span></span>


```csharp
var urls = new List<string>()
{
    "http://*:5000",
    "http://localhost:5001"
};

var host = new WebHostBuilder()
    .UseKestrel()
    .UseStartup<Startup>()
    .Start(urls.ToArray());

using (host)
{
    Console.ReadLine();
}
```

---

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="1634a-358">IHostingEnvironment 인터페이스</span><span class="sxs-lookup"><span data-stu-id="1634a-358">IHostingEnvironment interface</span></span>

<span data-ttu-id="1634a-359">[IHostingEnvironment 인터페이스](/aspnet/core/api/microsoft.aspnetcore.hosting.ihostingenvironment) 응용 프로그램의 웹 호스팅 환경에 대 한 정보를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-359">The [IHostingEnvironment interface](/aspnet/core/api/microsoft.aspnetcore.hosting.ihostingenvironment) provides information about the app's web hosting environment.</span></span> <span data-ttu-id="1634a-360">사용할 수 있습니다 [생성자 삽입](xref:fundamentals/dependency-injection) 얻으려고는 `IHostingEnvironment` 속성 및 확장 메서드를 사용 하려면:</span><span class="sxs-lookup"><span data-stu-id="1634a-360">You can use [constructor injection](xref:fundamentals/dependency-injection) to obtain the `IHostingEnvironment` in order to use its properties and extension methods:</span></span>

```csharp
public class CustomFileReader
{
    private readonly IHostingEnvironment _env;

    public CustomFileReader(IHostingEnvironment env)
    {
        _env = env;
    }

    public string ReadFile(string filePath)
    {
        var fileProvider = _env.WebRootFileProvider;
        // Process the file here
    }
}
```

<span data-ttu-id="1634a-361">사용할 수는 [규칙 기반 접근 방식을](xref:fundamentals/environments#startup-conventions) 를 환경에 따라 시작할 때 응용 프로그램을 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-361">You can use a [convention-based approach](xref:fundamentals/environments#startup-conventions) to configure your app at startup based on the environment.</span></span> <span data-ttu-id="1634a-362">삽입할 수 있습니다는 `IHostingEnvironment` 에 `Startup` 생성자에서 사용 하기 위해 `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="1634a-362">Alternatively, you can inject the `IHostingEnvironment` into the `Startup` constructor for use in `ConfigureServices`:</span></span>

```csharp
public class Startup
{
    public Startup(IHostingEnvironment env)
    {
        HostingEnvironment = env;
    }

    public IHostingEnvironment HostingEnvironment { get; }

    public void ConfigureServices(IServiceCollection services)
    {
        if (HostingEnvironment.IsDevelopment())
        {
            // Development configuration
        }
        else
        {
            // Staging/Production configuration
        }

        var contentRootPath = HostingEnvironment.ContentRootPath;
    }
}
```

> [!NOTE]
> <span data-ttu-id="1634a-363">이외에 `IsDevelopment` 확장 메서드를 `IHostingEnvironment` 제공 `IsStaging`, `IsProduction`, 및 `IsEnvironment(string environmentName)` 메서드.</span><span class="sxs-lookup"><span data-stu-id="1634a-363">In addition to the `IsDevelopment` extension method, `IHostingEnvironment` offers `IsStaging`, `IsProduction`, and `IsEnvironment(string environmentName)` methods.</span></span> <span data-ttu-id="1634a-364">참조 [여러 환경 작업](xref:fundamentals/environments) 대 한 자세한 내용은 합니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-364">See [Working with multiple environments](xref:fundamentals/environments) for details.</span></span>

<span data-ttu-id="1634a-365">`IHostingEnvironment` 서비스에 직접도 삽입 될 수 있습니다는 `Configure` 처리 파이프라인을 설정 하기 위한 메서드:</span><span class="sxs-lookup"><span data-stu-id="1634a-365">The `IHostingEnvironment` service can also be injected directly into the `Configure` method for setting up your processing pipeline:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        // In Development, use the developer exception page
        app.UseDeveloperExceptionPage();
    }
    else
    {
        // In Staging/Production, route exceptions to /error
        app.UseExceptionHandler("/error");
    }

    var contentRootPath = env.ContentRootPath;
}
```

<span data-ttu-id="1634a-366">삽입할 수 있으며 `IHostingEnvironment` 에 `Invoke` 메서드를 사용자 지정을 만들 때 [미들웨어](xref:fundamentals/middleware#writing-middleware):</span><span class="sxs-lookup"><span data-stu-id="1634a-366">You can inject `IHostingEnvironment` into the `Invoke` method when creating custom [middleware](xref:fundamentals/middleware#writing-middleware):</span></span>

```csharp
public async Task Invoke(HttpContext context, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        // Configure middleware for Development
    }
    else
    {
        // Configure middleware for Staging/Production
    }

    var contentRootPath = env.ContentRootPath;
}
```

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="1634a-367">IApplicationLifetime 인터페이스</span><span class="sxs-lookup"><span data-stu-id="1634a-367">IApplicationLifetime interface</span></span>

<span data-ttu-id="1634a-368">[IApplicationLifetime 인터페이스](/aspnet/core/api/microsoft.aspnetcore.hosting.iapplicationlifetime) 후 시작 및 종료 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-368">The [IApplicationLifetime interface](/aspnet/core/api/microsoft.aspnetcore.hosting.iapplicationlifetime) allows you to perform post-startup and shutdown activities.</span></span> <span data-ttu-id="1634a-369">인터페이스에서 세 가지 속성으로 등록할 수 있는 취소 토큰은 `Action` 시작 및 종료 이벤트를 정의 하는 메서드.</span><span class="sxs-lookup"><span data-stu-id="1634a-369">Three properties on the interface are cancellation tokens that you can register with `Action` methods to define startup and shutdown events.</span></span> <span data-ttu-id="1634a-370">또한 한 `StopApplication` 메서드.</span><span class="sxs-lookup"><span data-stu-id="1634a-370">There's also a `StopApplication` method.</span></span>

| <span data-ttu-id="1634a-371">취소 토큰</span><span class="sxs-lookup"><span data-stu-id="1634a-371">Cancellation Token</span></span>    | <span data-ttu-id="1634a-372">트리거된 경우 &#8230;</span><span class="sxs-lookup"><span data-stu-id="1634a-372">Triggered when&#8230;</span></span> |
| --------------------- | --------------------- |
| `ApplicationStarted`  | <span data-ttu-id="1634a-373">호스트 시작 완벽 하 게 했습니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-373">The host has fully started.</span></span> |
| `ApplicationStopping` | <span data-ttu-id="1634a-374">호스트에서 정상 종료를 수행 하 고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-374">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="1634a-375">요청은 계속 처리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-375">Requests may still be processing.</span></span> <span data-ttu-id="1634a-376">이 이벤트가 완료 될 때까지 차단을 종료 합니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-376">Shutdown blocks until this event completes.</span></span> |
| `ApplicationStopped`  | <span data-ttu-id="1634a-377">호스트에서 정상 종료를 완료 합니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-377">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="1634a-378">모든 요청을 완전히 처리 되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-378">All requests should be completely processed.</span></span> <span data-ttu-id="1634a-379">이 이벤트가 완료 될 때까지 차단을 종료 합니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-379">Shutdown blocks until this event completes.</span></span> |

| <span data-ttu-id="1634a-380">메서드</span><span class="sxs-lookup"><span data-stu-id="1634a-380">Method</span></span>            | <span data-ttu-id="1634a-381">작업</span><span class="sxs-lookup"><span data-stu-id="1634a-381">Action</span></span>                                           |
| ----------------- | ------------------------------------------------ |
| `StopApplication` | <span data-ttu-id="1634a-382">현재 응용 프로그램의 종료를 요청 합니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-382">Requests termination of the current application.</span></span> |

```csharp
public class Startup 
{
    public void Configure(IApplicationBuilder app, IApplicationLifetime appLifetime) 
    {
        appLifetime.ApplicationStarted.Register(OnStarted);
        appLifetime.ApplicationStopping.Register(OnStopping);
        appLifetime.ApplicationStopped.Register(OnStopped);

        Console.CancelKeyPress += (sender, eventArgs) =>
        {
            appLifetime.StopApplication();
            // Don't terminate the process immediately, wait for the Main thread to exit gracefully.
            eventArgs.Cancel = true;
        };
    }

    private void OnStarted()
    {
        // Perform post-startup activities here
    }

    private void OnStopping()
    {
        // Perform on-stopping activities here
    }

    private void OnStopped()
    {
        // Perform post-stopped activities here
    }
}
```

## <a name="troubleshooting-systemargumentexception"></a><span data-ttu-id="1634a-383">System.ArgumentException 문제 해결</span><span class="sxs-lookup"><span data-stu-id="1634a-383">Troubleshooting System.ArgumentException</span></span>

<span data-ttu-id="1634a-384">**ASP.NET Core 2.0만에 적용 됩니다.**</span><span class="sxs-lookup"><span data-stu-id="1634a-384">**Applies to ASP.NET Core 2.0 Only**</span></span>

<span data-ttu-id="1634a-385">삽입 하 여 호스트를 작성 하는 경우 `IStartup` 호출 보다는 종속성 주입 컨테이너에 직접 `UseStartup` 또는 `Configure`, 다음과 같은 오류가 발생할 수 있습니다: `Unhandled Exception: System.ArgumentException: A valid non-empty application name must be provided`합니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-385">If you build the host by injecting `IStartup` directly into the dependency injection container rather than calling `UseStartup` or `Configure`, you may encounter the following error: `Unhandled Exception: System.ArgumentException: A valid non-empty application name must be provided`.</span></span>

<span data-ttu-id="1634a-386">이 때문에 발생는 [applicationName(ApplicationKey)](/aspnet/core/api/microsoft.aspnetcore.hosting.webhostdefaults#Microsoft_AspNetCore_Hosting_WebHostDefaults_ApplicationKey) (현재 어셈블리)를 검색 하는 데 필요한 `HostingStartupAttributes`합니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-386">This occurs because the [applicationName(ApplicationKey)](/aspnet/core/api/microsoft.aspnetcore.hosting.webhostdefaults#Microsoft_AspNetCore_Hosting_WebHostDefaults_ApplicationKey) (the current assembly) is required to scan for `HostingStartupAttributes`.</span></span> <span data-ttu-id="1634a-387">수동으로 삽입할 경우 `IStartup` 종속성 주입 컨테이너에는 다음 호출을 추가 하면 `WebHostBuilder` 지정 된 어셈블리 이름:</span><span class="sxs-lookup"><span data-stu-id="1634a-387">If you manually inject `IStartup` into the dependency injection container, add the following call to your `WebHostBuilder` with the assembly name specified:</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting("applicationName", "<Assembly Name>")
    ...
```

<span data-ttu-id="1634a-388">Dummy 또는 추가할 `Configure` 하 여 `WebHostBuilder`로 설정 하는 `applicationName`(`ApplicationKey`) 자동으로:</span><span class="sxs-lookup"><span data-stu-id="1634a-388">Alternatively, add a dummy `Configure` to your `WebHostBuilder`, which sets the `applicationName`(`ApplicationKey`) automatically:</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .Configure(_ => { })
    ...
```

<span data-ttu-id="1634a-389">**참고**:는 유일한 및 ASP.NET 코어 2.0 릴리스로 필요한 경우에 호출 하지 않으면 `UseStartup` 또는 `Configure`합니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-389">**NOTE**: This is only required with the ASP.NET Core 2.0 release and only when you don't call `UseStartup` or `Configure`.</span></span>

<span data-ttu-id="1634a-390">자세한 내용은 참조 [공지: Microsoft.Extensions.PlatformAbstractions 되었습니다 (주석)를 제거](https://github.com/aspnet/Announcements/issues/237#issuecomment-323786938) 및 [StartupInjection 샘플](https://github.com/aspnet/Hosting/blob/8377d226f1e6e1a97dabdb6769a845eeccc829ed/samples/SampleStartups/StartupInjection.cs)합니다.</span><span class="sxs-lookup"><span data-stu-id="1634a-390">For more information, see [Announcements: Microsoft.Extensions.PlatformAbstractions has been removed (comment)](https://github.com/aspnet/Announcements/issues/237#issuecomment-323786938) and the [StartupInjection sample](https://github.com/aspnet/Hosting/blob/8377d226f1e6e1a97dabdb6769a845eeccc829ed/samples/SampleStartups/StartupInjection.cs).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1634a-391">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="1634a-391">Additional resources</span></span>

* [<span data-ttu-id="1634a-392">IIS를 사용 하 여 Windows에 게시</span><span class="sxs-lookup"><span data-stu-id="1634a-392">Publish to Windows using IIS</span></span>](../publishing/iis.md)
* [<span data-ttu-id="1634a-393">Nginx를 사용 하 여 Linux에 게시</span><span class="sxs-lookup"><span data-stu-id="1634a-393">Publish to Linux using Nginx</span></span>](../publishing/linuxproduction.md)
* [<span data-ttu-id="1634a-394">Apache를 사용 하 여 Linux에 게시</span><span class="sxs-lookup"><span data-stu-id="1634a-394">Publish to Linux using Apache</span></span>](../publishing/apache-proxy.md)
* [<span data-ttu-id="1634a-395">Windows 서비스의 호스트</span><span class="sxs-lookup"><span data-stu-id="1634a-395">Host in a Windows Service</span></span>](xref:hosting/windows-service)
