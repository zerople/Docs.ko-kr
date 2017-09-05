---
title: "ASP.NET Core에서 호스팅 | Microsoft Docs"
author: ardalis
description: "ASP.NET Core의 웹 호스트에 소개 합니다."
keywords: "ASP.NET Core, IWebHost 웹 호스트"
ms.author: riande
manager: wpickett
ms.date: 08/02/2017
ms.topic: article
ms.assetid: 4e45311d-8d56-46e2-b99d-6f65b648a277
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/hosting
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0725f3d2c130068094792e5ba9e17481155e4294
ms.sourcegitcommit: 74e22e08e3b08cb576e5184d16f4af5656c13c0c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2017
---
# <a name="introduction-to-hosting-in-aspnet-core"></a><span data-ttu-id="b6022-104">ASP.NET Core에서 호스팅 소개</span><span class="sxs-lookup"><span data-stu-id="b6022-104">Introduction to hosting in ASP.NET Core</span></span>

<span data-ttu-id="b6022-105">으로 [Steve Smith](http://ardalis.com)</span><span class="sxs-lookup"><span data-stu-id="b6022-105">By [Steve Smith](http://ardalis.com)</span></span>

<span data-ttu-id="b6022-106">ASP.NET Core 응용 프로그램을 실행 하려면 구성 하 고 사용 하 여 호스트를 시작할 `WebHostBuilder`합니다.</span><span class="sxs-lookup"><span data-stu-id="b6022-106">To run an ASP.NET Core app, you need to configure and launch a host using `WebHostBuilder`.</span></span>

## <a name="what-is-a-host"></a><span data-ttu-id="b6022-107">호스트 란?</span><span class="sxs-lookup"><span data-stu-id="b6022-107">What is a Host?</span></span>

<span data-ttu-id="b6022-108">ASP.NET Core 응용 프로그램에서는 한 *호스트* 를 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="b6022-108">ASP.NET Core apps require a *host* in which to execute.</span></span> <span data-ttu-id="b6022-109">호스트를 구현 해야 합니다는 `IWebHost` 기능 및 서비스의 컬렉션을 노출 하는 인터페이스 및 `Start` 메서드.</span><span class="sxs-lookup"><span data-stu-id="b6022-109">A host must implement the `IWebHost` interface, which exposes collections of features and services, and a `Start` method.</span></span> <span data-ttu-id="b6022-110">호스트의 인스턴스를 사용 하 여 일반적으로 생성 됩니다는 `WebHostBuilder`, 작성을 반환 하는 `WebHost` 인스턴스.</span><span class="sxs-lookup"><span data-stu-id="b6022-110">The host is typically created using an instance of a `WebHostBuilder`, which builds and returns a  `WebHost` instance.</span></span> <span data-ttu-id="b6022-111">`WebHost` 요청을 처리 하는 서버를 참조 합니다.</span><span class="sxs-lookup"><span data-stu-id="b6022-111">The `WebHost` references the server that will handle requests.</span></span> <span data-ttu-id="b6022-112">에 대 한 자세한 내용은 [서버](servers/index.md)합니다.</span><span class="sxs-lookup"><span data-stu-id="b6022-112">Learn more about [servers](servers/index.md).</span></span>

### <a name="what-is-the-difference-between-a-host-and-a-server"></a><span data-ttu-id="b6022-113">호스트와 서버 간의 차이점은 무엇입니까?</span><span class="sxs-lookup"><span data-stu-id="b6022-113">What is the difference between a host and a server?</span></span>

<span data-ttu-id="b6022-114">호스트는 응용 프로그램 시작 및 수명 관리 담당 합니다.</span><span class="sxs-lookup"><span data-stu-id="b6022-114">The host is responsible for application startup and lifetime management.</span></span> <span data-ttu-id="b6022-115">서버는 HTTP 요청을 수락 하는 일을 담당 합니다.</span><span class="sxs-lookup"><span data-stu-id="b6022-115">The server is responsible for accepting HTTP requests.</span></span> <span data-ttu-id="b6022-116">호스트의 책임의 일부 포함 응용 프로그램의 서비스와 서버 조건이 사용 가능 하며 올바르게 구성 되었는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="b6022-116">Part of the host's responsibility includes ensuring the application's services and the server are available and properly configured.</span></span> <span data-ttu-id="b6022-117">호스트 서버에 대 한 래퍼로 것으로 생각할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b6022-117">You can think of the host as being a wrapper around the server.</span></span> <span data-ttu-id="b6022-118">호스트가; 특정 서버를 사용 하도록 구성 되어 서버는 호스트의 인식 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b6022-118">The host is configured to use a particular server; the server is unaware of its host.</span></span>

## <a name="setting-up-a-host"></a><span data-ttu-id="b6022-119">호스트 설정</span><span class="sxs-lookup"><span data-stu-id="b6022-119">Setting up a Host</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="b6022-120">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="b6022-120">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="b6022-121">호스트의 인스턴스를 사용 하 여 만든 `WebHostBuilder`합니다.</span><span class="sxs-lookup"><span data-stu-id="b6022-121">You create a host using an instance of `WebHostBuilder`.</span></span> <span data-ttu-id="b6022-122">응용 프로그램의 진입점에서 일반적으로 이렇게: `public static void Main` (프로젝트 템플릿에서 있는 한 *Program.cs* 파일).</span><span class="sxs-lookup"><span data-stu-id="b6022-122">This is typically done in your app's entry point: `public static void Main` (which in the project templates is located in a *Program.cs* file).</span></span> <span data-ttu-id="b6022-123">일반적인 *Program.cs*표시 아래 사용 하는 방법을 보여 줍니다는 `WebHostBuilder` 호스트를 작성 합니다.</span><span class="sxs-lookup"><span data-stu-id="b6022-123">A typical *Program.cs*, shown below, demonstrates how to use a `WebHostBuilder` to build a host.</span></span>

<span data-ttu-id="b6022-124">[!code-csharp[Main](../common/samples/WebApplication1/Program.cs?highlight=14,15,16,17,18,19,20,21)]</span><span class="sxs-lookup"><span data-stu-id="b6022-124">[!code-csharp[Main](../common/samples/WebApplication1/Program.cs?highlight=14,15,16,17,18,19,20,21)]</span></span>

<span data-ttu-id="b6022-125">`WebHostBuilder` 담당 하는 호스트를 만드는 응용 프로그램에 대 한 서버 부트스트랩 합니다.</span><span class="sxs-lookup"><span data-stu-id="b6022-125">The `WebHostBuilder` is responsible for creating the host that will bootstrap the server for the app.</span></span> <span data-ttu-id="b6022-126">`WebHostBuilder`구현 하는 서버를 제공 해야 `IServer` (`UseKestrel` 위의 코드에서).</span><span class="sxs-lookup"><span data-stu-id="b6022-126">`WebHostBuilder` requires you provide a server that implements `IServer` (`UseKestrel` in the code above).</span></span> <span data-ttu-id="b6022-127">`UseKestrel`응용 프로그램에서 사용할 Kestrel 서버를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="b6022-127">`UseKestrel` specifies the Kestrel server will be used by the app.</span></span>

<span data-ttu-id="b6022-128">서버의 *콘텐츠 루트* MVC 뷰 파일과 같은 콘텐츠 파일에 대 한 검색 위치를 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="b6022-128">The server's *content root* determines where it searches for content files, like MVC View files.</span></span> <span data-ttu-id="b6022-129">기본 콘텐츠 루트는 응용 프로그램이 실행 되는 폴더입니다.</span><span class="sxs-lookup"><span data-stu-id="b6022-129">The default content root is the folder from which the application is run.</span></span>

> [!NOTE]
> <span data-ttu-id="b6022-130">지정 `Directory.GetCurrentDirectory` 이 폴더에서 앱이 시작 될 때 콘텐츠 루트 응용 프로그램의 콘텐츠 루트로 웹 프로젝트의 루트 폴더 사용 됩니다 (예를 들어 호출 `dotnet run` 웹 프로젝트 폴더에서).</span><span class="sxs-lookup"><span data-stu-id="b6022-130">Specifying `Directory.GetCurrentDirectory` as the content root will use the web project's root folder as the app's content root when the app is started from this folder (for example, calling `dotnet run` from the web project folder).</span></span> <span data-ttu-id="b6022-131">이 Visual Studio에서 사용 되는 기본 및 `dotnet new` 템플릿.</span><span class="sxs-lookup"><span data-stu-id="b6022-131">This is the default used in Visual Studio and `dotnet new` templates.</span></span>

<span data-ttu-id="b6022-132">IIS는 역방향 프록시를 사용 하려면 호출 `UseIISIntegration` 호스트 빌드 과정의 일환으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="b6022-132">To use IIS as a reverse proxy, call `UseIISIntegration` as part of building the host.</span></span> 

<span data-ttu-id="b6022-133">`UseIISIntegration` 구성 하지 않는 한 *서버*처럼 `UseKestrel` 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b6022-133">Note that `UseIISIntegration` doesn't configure a *server*, like `UseKestrel` does.</span></span> <span data-ttu-id="b6022-134">IIS에서 ASP.NET Core를 사용 하려면 둘 다 지정 해야 하면 `UseKestrel` 및 `UseIISIntegration`합니다.</span><span class="sxs-lookup"><span data-stu-id="b6022-134">To use IIS with ASP.NET Core, you must specify both `UseKestrel` and `UseIISIntegration`.</span></span> <span data-ttu-id="b6022-135">`UseKestrel`웹 서버를 만들고 응용 프로그램 호스트 합니다.</span><span class="sxs-lookup"><span data-stu-id="b6022-135">`UseKestrel` creates the web server and hosts the app.</span></span> <span data-ttu-id="b6022-136">`UseIISIntegration`IIS/IISExpress에서 사용 하는 환경 변수를 검사 하 고 포트에서 수신 하도록와 헤더를 사용 하는 등 설정을 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="b6022-136">`UseIISIntegration` examines environment variables used by IIS/IISExpress and configures settings such as the port to listen on and the headers to use.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="b6022-137">ASP.NET 2.x 핵심</span><span class="sxs-lookup"><span data-stu-id="b6022-137">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="b6022-138">호스트의 인스턴스를 사용 하 여 만든 `WebHostBuilder`합니다.</span><span class="sxs-lookup"><span data-stu-id="b6022-138">You create a host using an instance of `WebHostBuilder`.</span></span> <span data-ttu-id="b6022-139">응용 프로그램의 진입점에서 일반적으로 이렇게: `public static void Main` (프로젝트 템플릿에서 있는 한 *Program.cs* 파일).</span><span class="sxs-lookup"><span data-stu-id="b6022-139">This is typically done in your app's entry point: `public static void Main` (which in the project templates is located in a *Program.cs* file).</span></span> <span data-ttu-id="b6022-140">일반적인 *Program.cs*호출 아래에 표시 된, `CreateDefaultbuilder` 호스트 구축 하려면:</span><span class="sxs-lookup"><span data-stu-id="b6022-140">A typical *Program.cs*, shown below, calls `CreateDefaultbuilder` to build a host:</span></span>

<span data-ttu-id="b6022-141">[!code-csharp[Main](../common/samples/WebApplication1DotNetCore2.0App/Program.cs?name=snippet_Main&highlight=9)]</span><span class="sxs-lookup"><span data-stu-id="b6022-141">[!code-csharp[Main](../common/samples/WebApplication1DotNetCore2.0App/Program.cs?name=snippet_Main&highlight=9)]</span></span>

<span data-ttu-id="b6022-142">`CreateDefaultbuilder`인스턴스를 만들고 `WebHostBuilder` 응용 프로그램에 대 한 서버 시작 되도록 하는 호스트를 작성 합니다.</span><span class="sxs-lookup"><span data-stu-id="b6022-142">`CreateDefaultbuilder` creates an instance of `WebHostBuilder` to build the host that bootstraps the server for the app.</span></span> <span data-ttu-id="b6022-143">호스트 필요는 [IServer를 구현 하는 서버](servers/index.md)합니다.</span><span class="sxs-lookup"><span data-stu-id="b6022-143">The host requires a [server that implements IServer](servers/index.md).</span></span> <span data-ttu-id="b6022-144">기본 제공 서버는 [Kestrel](servers/kestrel.md) 및 [HTTP.sys](servers/httpsys.md); `CreateDefaultbuilder` Kestrel는 기본적으로 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="b6022-144">The built-in servers are [Kestrel](servers/kestrel.md) and [HTTP.sys](servers/httpsys.md); `CreateDefaultbuilder` use Kestrel by default.</span></span>

<span data-ttu-id="b6022-145">`CreateDefaultbuilder`Kestrel 웹 서버와 구성 외에도 설정 작업을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="b6022-145">`CreateDefaultbuilder` performs set-up tasks in addition to configuring Kestrel as the web server:</span></span>

* <span data-ttu-id="b6022-146">설정 하는 콘텐츠 루트 `Directory.GetCurrentDirectory`합니다.</span><span class="sxs-lookup"><span data-stu-id="b6022-146">Sets the content root to `Directory.GetCurrentDirectory`.</span></span>
* <span data-ttu-id="b6022-147">구성을 로드 합니다.</span><span class="sxs-lookup"><span data-stu-id="b6022-147">Loads configuration from:</span></span>
  * <span data-ttu-id="b6022-148">*appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="b6022-148">*appsettings.json*</span></span>
  * <span data-ttu-id="b6022-149">*appsettings 합니다. \<EnvironmentName >.json*합니다.</span><span class="sxs-lookup"><span data-stu-id="b6022-149">*appsettings.\<EnvironmentName>.json*.</span></span>
  * <span data-ttu-id="b6022-150">개발 환경에서 앱을 실행할 때 사용자의 비밀</span><span class="sxs-lookup"><span data-stu-id="b6022-150">user secrets when the app runs in the Development environment</span></span>
  * <span data-ttu-id="b6022-151">환경 변수</span><span class="sxs-lookup"><span data-stu-id="b6022-151">environment variables</span></span>
  * <span data-ttu-id="b6022-152">제공 된 명령줄 인수</span><span class="sxs-lookup"><span data-stu-id="b6022-152">supplied command line args</span></span>
* <span data-ttu-id="b6022-153">로깅 구성 섹션에 지정 된 규칙을 필터링 된 콘솔 및 디버그 출력에 대 한 로깅을 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="b6022-153">Configures logging for console and debug output, with filtering rules specified in a Logging configuration section.</span></span>
* <span data-ttu-id="b6022-154">IIS 통합할을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b6022-154">Enables IIS integration.</span></span>
* <span data-ttu-id="b6022-155">개발 환경에서 앱을 실행할 때 개발자 예외 페이지를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="b6022-155">Adds the developer exception page when the app runs in the Development environment.</span></span>

<span data-ttu-id="b6022-156">서버의 *콘텐츠 루트* MVC 뷰 파일과 같은 콘텐츠 파일에 대 한 검색 위치를 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="b6022-156">The server's *content root* determines where it searches for content files, like MVC View files.</span></span> <span data-ttu-id="b6022-157">기본 콘텐츠 루트는 응용 프로그램이 실행 되는 폴더입니다.</span><span class="sxs-lookup"><span data-stu-id="b6022-157">The default content root is the folder from which the application is run.</span></span>

> [!NOTE]
> <span data-ttu-id="b6022-158">지정 `Directory.GetCurrentDirectory` 이 폴더에서 앱이 시작 될 때 콘텐츠 루트 응용 프로그램의 콘텐츠 루트로 웹 프로젝트의 루트 폴더 사용 됩니다 (예를 들어 호출 `dotnet run` 웹 프로젝트 폴더에서).</span><span class="sxs-lookup"><span data-stu-id="b6022-158">Specifying `Directory.GetCurrentDirectory` as the content root will use the web project's root folder as the app's content root when the app is started from this folder (for example, calling `dotnet run` from the web project folder).</span></span> <span data-ttu-id="b6022-159">이 Visual Studio에서 사용 되는 기본 및 `dotnet new` 템플릿.</span><span class="sxs-lookup"><span data-stu-id="b6022-159">This is the default used in Visual Studio and `dotnet new` templates.</span></span>

<span data-ttu-id="b6022-160">ASP.NET Core를 자동으로 호출 IIS를 사용 하 여 역방향 프록시로 때 `UseIISIntegration` 호스트 빌드 과정의 일환으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="b6022-160">When you use IIS as a reverse proxy, ASP.NET Core automatically calls `UseIISIntegration` as part of building the host.</span></span> <span data-ttu-id="b6022-161">자세한 내용은 참조 [ASP.NET Core 모듈](xref:fundamentals/servers/aspnet-core-module)합니다.</span><span class="sxs-lookup"><span data-stu-id="b6022-161">For more information, see [ASP.NET Core Module](xref:fundamentals/servers/aspnet-core-module).</span></span>

<span data-ttu-id="b6022-162">`UseIISIntegration` 구성 하지 않는 한 *서버*처럼 `UseKestrel` 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b6022-162">Note that `UseIISIntegration` doesn't configure a *server*, like `UseKestrel` does.</span></span> <span data-ttu-id="b6022-163">`UseKestrel`웹 서버를 만들고 응용 프로그램 호스트 합니다.</span><span class="sxs-lookup"><span data-stu-id="b6022-163">`UseKestrel` creates the web server and hosts the app.</span></span> <span data-ttu-id="b6022-164">`UseIISIntegration`IIS/IISExpress에서 사용 하는 환경 변수를 검사 하 고 포트에서 수신 하도록와 헤더를 사용 하는 등 설정을 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="b6022-164">`UseIISIntegration` examines environment variables used by IIS/IISExpress and configures settings such as the port to listen on and the headers to use.</span></span>

---

<span data-ttu-id="b6022-165">호스트 (및 ASP.NET Core 응용 프로그램) 구성의 최소 구현을 서버 및 응용 프로그램의 요청 파이프라인의 구성만 포함:</span><span class="sxs-lookup"><span data-stu-id="b6022-165">A minimal implementation of configuring a host (and an ASP.NET Core app) would include just a server and configuration of the app's request pipeline:</span></span>

```csharp
var host = new WebHostBuilder()
    .UseKestrel()
    .Configure(app =>
    {
        app.Run(async (context) => await context.Response.WriteAsync("Hi!"));
    })
    .Build();

host.Run();
```

> [!NOTE]
> <span data-ttu-id="b6022-166">제공할 수는 호스트를 설정할 때 `Configure` 및 `ConfigureServices` 방법, 대신 또는 지정 외에도 `Startup` 클래스 (이러한 방법-을 정의 해야 하는 참조 [응용 프로그램 시작](startup.md)).</span><span class="sxs-lookup"><span data-stu-id="b6022-166">When setting up a host, you can provide `Configure` and `ConfigureServices` methods, instead of or in addition to specifying a `Startup` class (which must also define these methods - see [Application Startup](startup.md)).</span></span> <span data-ttu-id="b6022-167">여러 번 호출 `ConfigureServices` 서로 추가할;에 대 한 호출이 `Configure` 또는 `UseStartup` 이전 설정으로 바뀝니다.</span><span class="sxs-lookup"><span data-stu-id="b6022-167">Multiple calls to `ConfigureServices` will append to one another; calls to `Configure` or `UseStartup` will replace previous settings.</span></span>

## <a name="configuring-a-host"></a><span data-ttu-id="b6022-168">호스트 구성</span><span class="sxs-lookup"><span data-stu-id="b6022-168">Configuring a Host</span></span>

<span data-ttu-id="b6022-169">`WebHostBuilder` 메서드를 사용 하 여 직접도 설정할 수 있는 호스트에 대 한 대부분의 사용 가능한 구성 값을 설정 하기 위한 제공 `UseSetting` 와 연결 된 키입니다.</span><span class="sxs-lookup"><span data-stu-id="b6022-169">The `WebHostBuilder` provides methods for setting most of the available configuration values for the host, which can also be set directly using `UseSetting` and associated key.</span></span>

### <a name="host-configuration-values"></a><span data-ttu-id="b6022-170">호스트 구성 값</span><span class="sxs-lookup"><span data-stu-id="b6022-170">Host Configuration Values</span></span>

<span data-ttu-id="b6022-171">**시작 오류 캡처**`bool`</span><span class="sxs-lookup"><span data-stu-id="b6022-171">**Capture Startup Errors** `bool`</span></span>

<span data-ttu-id="b6022-172">키: `captureStartupErrors`합니다.</span><span class="sxs-lookup"><span data-stu-id="b6022-172">Key: `captureStartupErrors`.</span></span> <span data-ttu-id="b6022-173">기본값은 `false`입니다.</span><span class="sxs-lookup"><span data-stu-id="b6022-173">Defaults to `false`.</span></span> <span data-ttu-id="b6022-174">때 `false`, 종료 하는 호스트에서 시작 결과 동안 오류가 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="b6022-174">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="b6022-175">때 `true`, 호스트에서 발생 한 예외를 캡처합니다는 `Startup` 클래스 및 서버를 시작 하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="b6022-175">When `true`, the host will capture any exceptions from the `Startup` class and attempt to start the server.</span></span> <span data-ttu-id="b6022-176">오류 페이지가 표시 됩니다 (일반, 또는 자세한, 설정에 따라 자세한 오류, 아래) 모든 요청에 대 한 합니다.</span><span class="sxs-lookup"><span data-stu-id="b6022-176">It will display an error page (generic, or detailed, based on the Detailed Errors setting, below) for every request.</span></span> <span data-ttu-id="b6022-177">사용 하 여 설정 된 `CaptureStartupErrors` 메서드.</span><span class="sxs-lookup"><span data-stu-id="b6022-177">Set using the `CaptureStartupErrors` method.</span></span>

<span data-ttu-id="b6022-178">참고: 앱 Kestrel 및 IIS를 실행 하는 경우 기본 동작은 시작 오류 캡처합니다.</span><span class="sxs-lookup"><span data-stu-id="b6022-178">Note: When your app runs with Kestrel and IIS, the default behavior is to capture startup errors.</span></span> 

```csharp
new WebHostBuilder()
    .CaptureStartupErrors(true)
   ```

<span data-ttu-id="b6022-179">**루트 콘텐츠**`string`</span><span class="sxs-lookup"><span data-stu-id="b6022-179">**Content Root** `string`</span></span>

<span data-ttu-id="b6022-180">키: `contentRoot`합니다.</span><span class="sxs-lookup"><span data-stu-id="b6022-180">Key: `contentRoot`.</span></span> <span data-ttu-id="b6022-181">기본값으로 설정 (Kestrel;에 대 한 응용 프로그램 어셈블리 파일이 있는 폴더 IIS에서 웹 프로젝트 루트 기본적으로 사용).</span><span class="sxs-lookup"><span data-stu-id="b6022-181">Defaults to the folder where the application assembly resides (for Kestrel; IIS will use the web project root by default).</span></span> <span data-ttu-id="b6022-182">이 설정은 MVC 뷰 등의 콘텐츠 파일을 검색할 ASP.NET 코어는 시작 위치를 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="b6022-182">This setting determines where ASP.NET Core will begin searching for content files, such as MVC Views.</span></span> <span data-ttu-id="b6022-183">에 대 한 기본 경로로 사용도 [웹 루트 설정](#web-root-setting)합니다.</span><span class="sxs-lookup"><span data-stu-id="b6022-183">Also used as the base path for the [Web Root Setting](#web-root-setting).</span></span> <span data-ttu-id="b6022-184">사용 하 여 설정 된 `UseContentRoot` 메서드.</span><span class="sxs-lookup"><span data-stu-id="b6022-184">Set using the `UseContentRoot` method.</span></span> <span data-ttu-id="b6022-185">경로가 존재 해야 합니다 또는 호스트 시작 되지 것입니다.</span><span class="sxs-lookup"><span data-stu-id="b6022-185">Path must exist, or host will fail to start.</span></span>

```csharp
new WebHostBuilder()
    .UseContentRoot("c:\\mywebsite")
   ```

<span data-ttu-id="b6022-186">**자세한 오류**`bool`</span><span class="sxs-lookup"><span data-stu-id="b6022-186">**Detailed Errors** `bool`</span></span>

<span data-ttu-id="b6022-187">키: `detailedErrors`합니다.</span><span class="sxs-lookup"><span data-stu-id="b6022-187">Key: `detailedErrors`.</span></span> <span data-ttu-id="b6022-188">기본값은 `false`입니다.</span><span class="sxs-lookup"><span data-stu-id="b6022-188">Defaults to `false`.</span></span> <span data-ttu-id="b6022-189">때 `true` (또는 환경 "개발"로 설정 된 경우) 응용 프로그램에는 일반 오류 페이지 대신 시작 예외의 세부 정보가 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b6022-189">When `true` (or when Environment is set to "Development"), the app will display details of startup exceptions, instead of just a generic error page.</span></span> <span data-ttu-id="b6022-190">사용 하 여 설정 `UseSetting`합니다.</span><span class="sxs-lookup"><span data-stu-id="b6022-190">Set using `UseSetting`.</span></span>

```csharp
new WebHostBuilder()
    .UseSetting("detailedErrors", "true")
```

<span data-ttu-id="b6022-191">자세한 오류로 설정 된 경우 `false` 캡처 시작 오류 이며 `true`, 서버에 모든 요청에 대 한 응답에는 일반 오류 페이지가 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b6022-191">When Detailed Errors is set to `false` and Capture Startup Errors is `true`, a generic error page is displayed in response to every request to the server.</span></span>

![일반 오류 페이지](hosting/_static/generic-error-page.png)

<span data-ttu-id="b6022-193">자세한 오류로 설정 된 경우 `true` 캡처 시작 오류 이며 `true`, 서버에 모든 요청에 대 한 응답에 자세한 오류 페이지가 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b6022-193">When Detailed Errors is set to `true` and Capture Startup Errors is `true`, a detailed error page is displayed in response to every request to the server.</span></span>

![자세한 오류 페이지](hosting/_static/detailed-error-page.png)

<span data-ttu-id="b6022-195">**환경**`string`</span><span class="sxs-lookup"><span data-stu-id="b6022-195">**Environment** `string`</span></span>

<span data-ttu-id="b6022-196">키: `environment`합니다.</span><span class="sxs-lookup"><span data-stu-id="b6022-196">Key: `environment`.</span></span> <span data-ttu-id="b6022-197">기본값은 "Production"입니다.</span><span class="sxs-lookup"><span data-stu-id="b6022-197">Defaults to "Production".</span></span> <span data-ttu-id="b6022-198">값으로 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b6022-198">May be set to any value.</span></span> <span data-ttu-id="b6022-199">프레임 워크에서 정의 된 값 "개발", "준비" 및 "Production"를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="b6022-199">Framework-defined values include "Development", "Staging", and "Production".</span></span> <span data-ttu-id="b6022-200">값은 대/소문자 구분 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b6022-200">Values are not case sensitive.</span></span> <span data-ttu-id="b6022-201">참조 [여러 환경 작업](environments.md)합니다.</span><span class="sxs-lookup"><span data-stu-id="b6022-201">See [Working with Multiple Environments](environments.md).</span></span> <span data-ttu-id="b6022-202">사용 하 여 설정 된 `UseEnvironment` 메서드.</span><span class="sxs-lookup"><span data-stu-id="b6022-202">Set using the `UseEnvironment` method.</span></span>

```csharp
new WebHostBuilder()
    .UseEnvironment("Development")
```

> [!NOTE]
> <span data-ttu-id="b6022-203">기본적으로 환경에서 읽기는 `ASPNETCORE_ENVIRONMENT` 환경 변수입니다.</span><span class="sxs-lookup"><span data-stu-id="b6022-203">By default, the environment is read from the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span> <span data-ttu-id="b6022-204">Visual Studio를 사용 하는 경우 환경 변수를 설정할 수 있습니다는 *launchSettings.json* 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="b6022-204">When using Visual Studio, environment variables may be set in the *launchSettings.json* file.</span></span>

<a id="server-urls"></a>

<span data-ttu-id="b6022-205">**서버 Url**`string`</span><span class="sxs-lookup"><span data-stu-id="b6022-205">**Server URLs** `string`</span></span>

<span data-ttu-id="b6022-206">키: `urls`합니다.</span><span class="sxs-lookup"><span data-stu-id="b6022-206">Key: `urls`.</span></span> <span data-ttu-id="b6022-207">세미콜론 (;)으로 설정 분리 된 URL의 목록 prefixes 서버 응답 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b6022-207">Set to a semicolon (;) separated list of URL prefixes to which the server should respond.</span></span> <span data-ttu-id="b6022-208">예를 들어, `http://localhost:123`을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="b6022-208">For example, `http://localhost:123`.</span></span> <span data-ttu-id="b6022-209">도메인/호스트 이름으로 대체 될 수 있습니다 "\*" 서버에서 모든 IP 주소에서 요청을 수신 대기 하거나 지정 된 포트 및 프로토콜을 사용 하 여 호스트를 나타냅니다 (예를 들어 `http://*:5000` 또는 `https://*:5001`).</span><span class="sxs-lookup"><span data-stu-id="b6022-209">The domain/host name can be replaced with "\*" to indicate the server should listen to requests on any IP address or host using the specified port and protocol (for example, `http://*:5000` or `https://*:5001`).</span></span> <span data-ttu-id="b6022-210">프로토콜 (`http://` 또는 `https://`) 각 URL에 포함 되어 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b6022-210">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="b6022-211">접두사 구성 된 서버에서 해석 하는 지원 되는 형식의 서버 간에 달라 집니다.</span><span class="sxs-lookup"><span data-stu-id="b6022-211">The prefixes are interpreted by the configured server; supported formats will vary between servers.</span></span>

```csharp
new WebHostBuilder()
    .UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002")
```

<span data-ttu-id="b6022-212">ASP.NET Core 2.0에서 Kestrel 구성이 자체 끝점 API와 지원 하지 않습니다 `https://` 에 `urls` 문자열입니다.</span><span class="sxs-lookup"><span data-stu-id="b6022-212">In ASP.NET Core 2.0, Kestrel has its own endpoint configuration API and does not support `https://` in the `urls` string.</span></span> <span data-ttu-id="b6022-213">자세한 내용은 참조 [Kestrel 소개](xref:fundamentals/servers/kestrel?tabs=aspnetcore2x#endpoint-configuration)합니다.</span><span class="sxs-lookup"><span data-stu-id="b6022-213">For more information, see [Introduction to Kestrel](xref:fundamentals/servers/kestrel?tabs=aspnetcore2x#endpoint-configuration).</span></span>

<span data-ttu-id="b6022-214">**시작 어셈블리**`string`</span><span class="sxs-lookup"><span data-stu-id="b6022-214">**Startup Assembly** `string`</span></span>

<span data-ttu-id="b6022-215">키: `startupAssembly`합니다.</span><span class="sxs-lookup"><span data-stu-id="b6022-215">Key: `startupAssembly`.</span></span> <span data-ttu-id="b6022-216">검색할 어셈블리 확인에서 `Startup` 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="b6022-216">Determines the assembly to search for the `Startup` class.</span></span> <span data-ttu-id="b6022-217">사용 하 여 설정 된 `UseStartup` 메서드.</span><span class="sxs-lookup"><span data-stu-id="b6022-217">Set using the `UseStartup` method.</span></span> <span data-ttu-id="b6022-218">대신 특정 형식을 사용 하 여 참조 수 `WebHostBuilder.UseStartup<StartupType>`합니다.</span><span class="sxs-lookup"><span data-stu-id="b6022-218">May instead reference specific type using `WebHostBuilder.UseStartup<StartupType>`.</span></span> <span data-ttu-id="b6022-219">여러 개인 경우 `UseStartup` 메서드가 호출 되어, 마지막 우선적으로 적용 합니다.</span><span class="sxs-lookup"><span data-stu-id="b6022-219">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
new WebHostBuilder()
    .UseStartup("StartupAssemblyName")
```

<a name=web-root-setting></a>

<span data-ttu-id="b6022-220">**루트 웹**`string`</span><span class="sxs-lookup"><span data-stu-id="b6022-220">**Web Root** `string`</span></span>

<span data-ttu-id="b6022-221">키: `webroot`합니다.</span><span class="sxs-lookup"><span data-stu-id="b6022-221">Key: `webroot`.</span></span> <span data-ttu-id="b6022-222">기본값은 지정 하지 않으면 `(Content Root Path)\wwwroot`있는 경우.</span><span class="sxs-lookup"><span data-stu-id="b6022-222">If not specified the default is `(Content Root Path)\wwwroot`, if it exists.</span></span> <span data-ttu-id="b6022-223">이 경로가 존재 하지 않는 경우 아무 파일 공급자가 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b6022-223">If this path doesn't exist, then a no-op file provider is used.</span></span> <span data-ttu-id="b6022-224">사용 하 여 설정 `UseWebRoot`합니다.</span><span class="sxs-lookup"><span data-stu-id="b6022-224">Set using `UseWebRoot`.</span></span>

```csharp
new WebHostBuilder()
    .UseWebRoot("public")
```

### <a name="overriding-configuration"></a><span data-ttu-id="b6022-225">구성을 무시</span><span class="sxs-lookup"><span data-stu-id="b6022-225">Overriding Configuration</span></span>

<span data-ttu-id="b6022-226">사용 하 여 [구성](configuration.md) 호스트에서 사용할 구성 값을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="b6022-226">Use [Configuration](configuration.md) to set configuration values to be used by the host.</span></span> <span data-ttu-id="b6022-227">이러한 값은 이후에 재정의 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b6022-227">These values may be subsequently overridden.</span></span> <span data-ttu-id="b6022-228">이 사용 하 여 지정 됩니다 `UseConfiguration`합니다.</span><span class="sxs-lookup"><span data-stu-id="b6022-228">This is specified using `UseConfiguration`.</span></span>

```csharp
public static void Main(string[] args)
{
    var config = new ConfigurationBuilder()
        .AddJsonFile("hosting.json", optional: true)
        .AddCommandLine(args)
        .Build();

    var host = new WebHostBuilder()
        .UseConfiguration(config)
        .UseKestrel()
        .Configure(app =>
        {
            app.Run(async (context) => await context.Response.WriteAsync("Hi!"));
        })
        .Build();

    host.Run();
}
```

<span data-ttu-id="b6022-229">위의 예에서 명령줄 인수 전달 될 수 있습니다에 호스트를 구성 또는 구성 설정에 선택적으로 지정할 수 있습니다는 *hosting.json* 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="b6022-229">In the example above, command-line arguments may be passed in to configure the host, or configuration settings may optionally be specified in a *hosting.json* file.</span></span> <span data-ttu-id="b6022-230">특정 URL에서 실행 하는 호스트를 지정 하려면 명령 프롬프트에서 원하는 값에 전달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b6022-230">To specify the host run on a particular URL, you could pass in the desired value from a command prompt:</span></span>

```console
dotnet run --urls "http://*:5000"
```

<span data-ttu-id="b6022-231">`Run` 메서드 웹 앱을 시작 하 고 호스트 종료 될 때까지 호출 스레드를 차단 합니다.</span><span class="sxs-lookup"><span data-stu-id="b6022-231">The `Run` method starts the web app and blocks the calling thread until the host is shutdown.</span></span>

```csharp
host.Run();
```

<span data-ttu-id="b6022-232">호출 하 여 비블로킹 방식으로 호스트를 실행할 수 있습니다는 `Start` 메서드:</span><span class="sxs-lookup"><span data-stu-id="b6022-232">You can run the host in a non-blocking manner by calling its `Start` method:</span></span>

```csharp
using (host)
{
    host.Start();
    Console.ReadLine();
}
```

<span data-ttu-id="b6022-233">에 대 한 Url의 목록을 전달는 `Start` 메서드 하며 지정 된 Url에서 수신 합니다.</span><span class="sxs-lookup"><span data-stu-id="b6022-233">Pass a list of URLs to the `Start` method and it will listen on the URLs specified:</span></span>

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

<span data-ttu-id="b6022-234">사용 중인 서버에 사용할 수 있는 다음 URL 형식에 따라 달라 집니다.</span><span class="sxs-lookup"><span data-stu-id="b6022-234">The URL formats that are valid here depend on the server you're using.</span></span> <span data-ttu-id="b6022-235">자세한 내용은 참조 [서버 Url](#server-urls) 이 문서의 앞부분에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b6022-235">For more information, see [Server URLs](#server-urls) earlier in this article.</span></span>

> [!NOTE]
> <span data-ttu-id="b6022-236">`UseConfiguration` 확장 메서드는 현재에서 반환 되는 구성 섹션을 구문 분석할 수 없습니다 `GetSection` (예를 들어 `.UseConfiguration(Configuration.GetSection("section"))`합니다.</span><span class="sxs-lookup"><span data-stu-id="b6022-236">The `UseConfiguration` extension method isn't currently capable of parsing a configuration section returned by `GetSection` (for example, `.UseConfiguration(Configuration.GetSection("section"))`.</span></span> <span data-ttu-id="b6022-237">`GetSection` 메서드 요청 섹션에 있는 구성 키를 필터링 되지만 섹션 이름에는 키 (예를 들어 `section:urls`, `section:environment`).</span><span class="sxs-lookup"><span data-stu-id="b6022-237">The `GetSection` method filters the configuration keys to the section requested but leaves the section name on the keys (for example, `section:urls`, `section:environment`).</span></span> <span data-ttu-id="b6022-238">`UseConfiguration` 메서드에서 예상 키와 일치 하도록는 `WebHostBuilder` 키 (예를 들어 `urls`, `environment`).</span><span class="sxs-lookup"><span data-stu-id="b6022-238">The `UseConfiguration` method expects the keys to match the `WebHostBuilder` keys (for example, `urls`, `environment`).</span></span> <span data-ttu-id="b6022-239">키에 대해 섹션 이름의 존재는 호스트 구성에서 섹션의 값을 방지 합니다.</span><span class="sxs-lookup"><span data-stu-id="b6022-239">The presence of the section name on the keys prevents the section's values from configuring the host.</span></span> <span data-ttu-id="b6022-240">이 문제는 향후 릴리스에서 해결될 예정입니다.</span><span class="sxs-lookup"><span data-stu-id="b6022-240">This issue will be addressed in an upcoming release.</span></span> <span data-ttu-id="b6022-241">자세한 내용 및 해결 방법에 대 한 참조 [전체 키를 사용 하 여 WebHostBuilder.UseConfiguration에 구성 섹션을 전달](https://github.com/aspnet/Hosting/issues/839)합니다.</span><span class="sxs-lookup"><span data-stu-id="b6022-241">For more information and workarounds, see [Passing configuration section into WebHostBuilder.UseConfiguration uses full keys](https://github.com/aspnet/Hosting/issues/839).</span></span>

### <a name="ordering-importance"></a><span data-ttu-id="b6022-242">순서 중요성</span><span class="sxs-lookup"><span data-stu-id="b6022-242">Ordering Importance</span></span>

<span data-ttu-id="b6022-243">`WebHostBuilder`설정은 특정 환경 변수에서 읽은 먼저 경우 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="b6022-243">`WebHostBuilder` settings are first read from certain environment variables, if set.</span></span> <span data-ttu-id="b6022-244">이러한 환경 변수 형식을 사용 해야 `ASPNETCORE_{configurationKey}`설정 예: Url을 설정 하는 서버는 기본적으로에서 수신 하므로 `ASPNETCORE_URLS`합니다.</span><span class="sxs-lookup"><span data-stu-id="b6022-244">These environment variables must use the format `ASPNETCORE_{configurationKey}`, so for example to set the URLs the server will listen on by default, you would set `ASPNETCORE_URLS`.</span></span>

<span data-ttu-id="b6022-245">구성을 지정 하 여 이러한 환경 변수 값을 재정의할 수 있습니다 (사용 하 여 `UseConfiguration`) 또는 값을 명시적으로 설정 하 여 (사용 하 여 `UseUrls` 예를 들어).</span><span class="sxs-lookup"><span data-stu-id="b6022-245">You can override any of these environment variable values by specifying configuration (using `UseConfiguration`) or by setting the value explicitly (using `UseUrls` for instance).</span></span> <span data-ttu-id="b6022-246">호스트는 선택한 옵션에 설정 하는 마지막 값을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="b6022-246">The host will use whichever option sets the value last.</span></span> <span data-ttu-id="b6022-247">이러한 이유로 `UseIISIntegration` 뒤에 나타나야 합니다 `UseUrls`IIS에서 동적으로 제공 된 URL을 없어지기 때문에, 합니다.</span><span class="sxs-lookup"><span data-stu-id="b6022-247">For this reason, `UseIISIntegration` must appear after `UseUrls`, because it replaces the URL with one dynamically provided by IIS.</span></span> <span data-ttu-id="b6022-248">프로그래밍 방식으로 기본 URL 단일 값으로 설정 되었지만 구성으로 재정의 될 수 있도록 허용 하려면 호스트를 다음과 같이 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b6022-248">If you want to programmatically set the default URL to one value, but allow it to be overridden with configuration, you could configure the host as follows:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddCommandLine(args)
    .Build();

var host = new WebHostBuilder()
    .UseUrls("http://*:1000") // default URL
    .UseConfiguration(config) // override from command line
    .UseKestrel()
    .Build();
```

## <a name="additional-resources"></a><span data-ttu-id="b6022-249">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="b6022-249">Additional resources</span></span>

* [<span data-ttu-id="b6022-250">IIS를 사용 하 여 Windows에 게시</span><span class="sxs-lookup"><span data-stu-id="b6022-250">Publish to Windows using IIS</span></span>](../publishing/iis.md)
* [<span data-ttu-id="b6022-251">Nginx를 사용 하 여 Linux에 게시</span><span class="sxs-lookup"><span data-stu-id="b6022-251">Publish to Linux using Nginx</span></span>](../publishing/linuxproduction.md)
* [<span data-ttu-id="b6022-252">Apache를 사용 하 여 Linux에 게시</span><span class="sxs-lookup"><span data-stu-id="b6022-252">Publish to Linux using Apache</span></span>](../publishing/apache-proxy.md)
* [<span data-ttu-id="b6022-253">Windows 서비스의 호스트</span><span class="sxs-lookup"><span data-stu-id="b6022-253">Host in a Windows Service</span></span>](xref:hosting/windows-service)

