---
title: "ASP.NET Core 기본 사항"
author: rick-anderson
description: "이 문서에서는 ASP.NET Core 응용 프로그램을 빌드할 때 이해해야 하는 기본 개념의 간략한 개요를 제공합니다."
keywords: "ASP.NET Core, 기본 사항, 개요"
ms.author: riande
manager: wpickett
ms.date: 08/18/2017
ms.topic: get-started-article
ms.assetid: a19b7836-63e4-44e8-8250-50d426dd1070
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/index
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 99fbe0e02be27a0fbbb7ff65bc15713aab58c003
ms.sourcegitcommit: 74e22e08e3b08cb576e5184d16f4af5656c13c0c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2017
---
# <a name="aspnet-core-fundamentals-overview"></a><span data-ttu-id="83d4c-104">ASP.NET Core 기본 사항 개요</span><span class="sxs-lookup"><span data-stu-id="83d4c-104">ASP.NET Core fundamentals overview</span></span>

<span data-ttu-id="83d4c-105">ASP.NET Core 응용 프로그램은 `Main` 메서드에서 웹 서버를 만드는 콘솔 앱입니다.</span><span class="sxs-lookup"><span data-stu-id="83d4c-105">An ASP.NET Core application is a console app that creates a web server in its `Main` method:</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="83d4c-106">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="83d4c-106">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="83d4c-107">[!code-csharp[Main](../getting-started/sample/aspnetcoreapp/Program2x.cs)]</span><span class="sxs-lookup"><span data-stu-id="83d4c-107">[!code-csharp[Main](../getting-started/sample/aspnetcoreapp/Program2x.cs)]</span></span>

<span data-ttu-id="83d4c-108">`Main` 메서드는 빌드 패턴에 따라 웹 응용 프로그램 호스트를 만드는 `WebHost.CreateDefaultBuilder`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="83d4c-108">The `Main` method invokes `WebHost.CreateDefaultBuilder`, which follows the builder pattern to create a web application host.</span></span> <span data-ttu-id="83d4c-109">빌더에는 웹 서버(예: `UseKestrel`) 및 시작 클래스(`UseStartup`)를 정의하는 메서드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="83d4c-109">The builder has methods that define the web server (for example, `UseKestrel`) and the startup class (`UseStartup`).</span></span> <span data-ttu-id="83d4c-110">이전 예제에서 [Kestrel](xref:fundamentals/servers/kestrel) 웹 서버는 자동으로 할당됩니다.</span><span class="sxs-lookup"><span data-stu-id="83d4c-110">In the preceding example, a [Kestrel](xref:fundamentals/servers/kestrel) web server is automatically allocated.</span></span> <span data-ttu-id="83d4c-111">ASP.NET Core의 웹 호스트는 IIS(사용 가능한 경우)에서 실행하려고 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="83d4c-111">ASP.NET Core's web host will attempt to run on IIS, if it is available.</span></span> <span data-ttu-id="83d4c-112">[HTTP.sys](xref:fundamentals/servers/httpsys) 같은 다른 웹 서버는 해당하는 확장 메서드를 호출하여 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="83d4c-112">Other web servers, such as [HTTP.sys](xref:fundamentals/servers/httpsys), can be used by invoking the appropriate extension method.</span></span> <span data-ttu-id="83d4c-113">`UseStartup`은 다음 섹션에서 추가로 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="83d4c-113">`UseStartup` is explained further in the next section.</span></span>

<span data-ttu-id="83d4c-114">`WebHost.CreateDefaultBuilder` 호출의 반환 형식인 `IWebHostBuilder`는 다양한 선택적 메서드를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="83d4c-114">`IWebHostBuilder`, the return type of the `WebHost.CreateDefaultBuilder` invocation, provides many optional methods.</span></span> <span data-ttu-id="83d4c-115">이러한 일부 메서드에는 응용 프로그램을 HTTP.sys에서 호스트하기 위한 `UseHttpSys` 및 루트 콘텐츠 디렉터리를 지정하기 위한 `UseContentRoot`가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="83d4c-115">Some of these methods include `UseHttpSys` for hosting the application in HTTP.sys, and `UseContentRoot` for specifying the root content directory.</span></span> <span data-ttu-id="83d4c-116">`Build` 및 `Run` 메서드는 응용 프로그램을 호스트하고 HTTP 요청을 수신하기 시작할 `IWebHost` 개체를 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="83d4c-116">The `Build` and `Run` methods build the `IWebHost` object that will host the application and begin listening for HTTP requests.</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="83d4c-117">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="83d4c-117">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="83d4c-118">[!code-csharp[Main](../getting-started/sample/aspnetcoreapp/Program.cs)]</span><span class="sxs-lookup"><span data-stu-id="83d4c-118">[!code-csharp[Main](../getting-started/sample/aspnetcoreapp/Program.cs)]</span></span>

<span data-ttu-id="83d4c-119">`Main` 메서드는 빌드 패턴에 따라 웹 응용 프로그램 호스트를 만드는 `WebHostBuilder`를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="83d4c-119">The `Main` method uses `WebHostBuilder`, which follows the builder pattern to create a web application host.</span></span> <span data-ttu-id="83d4c-120">빌더에는 웹 서버(예: `UseKestrel`) 및 시작 클래스(`UseStartup`)를 정의하는 메서드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="83d4c-120">The builder has methods that define the web server (for example, `UseKestrel`) and the startup class (`UseStartup`).</span></span> <span data-ttu-id="83d4c-121">이전 예제에서는 [Kestrel](xref:fundamentals/servers/kestrel) 웹 서버가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="83d4c-121">In the preceding example, the [Kestrel](xref:fundamentals/servers/kestrel) web server is used.</span></span> <span data-ttu-id="83d4c-122">[WebListener](xref:fundamentals/servers/weblistener) 같은 다른 웹 서버는 해당하는 확장 메서드를 호출하여 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="83d4c-122">Other web servers, such as [WebListener](xref:fundamentals/servers/weblistener), can be used by invoking the appropriate extension method.</span></span> <span data-ttu-id="83d4c-123">`UseStartup`은 다음 섹션에서 추가로 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="83d4c-123">`UseStartup` is explained further in the next section.</span></span>

<span data-ttu-id="83d4c-124">`WebHostBuilder`는 IIS 및 IIS Express를 호스트하기 위한 `UseIISIntegration` 및 루트 콘텐츠 디렉터리를 지정하기 위한 `UseContentRoot`를 포함한 다양한 선택적 메서드를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="83d4c-124">`WebHostBuilder` provides many optional methods, including `UseIISIntegration` for hosting in IIS and IIS Express, and `UseContentRoot` for specifying the root content directory.</span></span> <span data-ttu-id="83d4c-125">`Build` 및 `Run` 메서드는 응용 프로그램을 호스트하고 HTTP 요청을 수신하기 시작할 `IWebHost` 개체를 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="83d4c-125">The `Build` and `Run` methods build the `IWebHost` object that will host the application and begin listening for HTTP requests.</span></span>

---

## <a name="startup"></a><span data-ttu-id="83d4c-126">시작</span><span class="sxs-lookup"><span data-stu-id="83d4c-126">Startup</span></span>

<span data-ttu-id="83d4c-127">`WebHostBuilder`의 `UseStartup` 메서드는 앱에 대한 `Startup` 클래스를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="83d4c-127">The `UseStartup` method on `WebHostBuilder` specifies the `Startup` class for your app:</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="83d4c-128">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="83d4c-128">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="83d4c-129">[!code-csharp[Main](../getting-started/sample/aspnetcoreapp/Program2x.cs?highlight=10&range=6-17)]</span><span class="sxs-lookup"><span data-stu-id="83d4c-129">[!code-csharp[Main](../getting-started/sample/aspnetcoreapp/Program2x.cs?highlight=10&range=6-17)]</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="83d4c-130">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="83d4c-130">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="83d4c-131">[!code-csharp[Main](../getting-started/sample/aspnetcoreapp/Program.cs?highlight=7&range=6-17)]</span><span class="sxs-lookup"><span data-stu-id="83d4c-131">[!code-csharp[Main](../getting-started/sample/aspnetcoreapp/Program.cs?highlight=7&range=6-17)]</span></span>

---

<span data-ttu-id="83d4c-132">`Startup` 클래스는 요청 처리 파이프라인을 정의하고 응용 프로그램에 필요한 서비스를 구성하는 위치입니다.</span><span class="sxs-lookup"><span data-stu-id="83d4c-132">The `Startup` class is where you define the request handling pipeline and where any services needed by the application are configured.</span></span> <span data-ttu-id="83d4c-133">`Startup` 클래스는 공용이어야 하고 다음 메서드를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="83d4c-133">The `Startup` class must be public and contain the following methods:</span></span>

```csharp
public class Startup
{
    // This method gets called by the runtime. Use this method to add services to the container.
    public void ConfigureServices(IServiceCollection services)
    {
    }

    // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
    public void Configure(IApplicationBuilder app)
    {
    }
}
```

* <span data-ttu-id="83d4c-134">`ConfigureServices`는 응용 프로그램에서 사용되는 [서비스](#services)를 정의합니다(예: ASP.NET Core MVC, Entity Framework Core, ID 등).</span><span class="sxs-lookup"><span data-stu-id="83d4c-134">`ConfigureServices` defines the [Services](#services) used by your application (such as ASP.NET Core MVC, Entity Framework Core, Identity, etc.).</span></span>

* <span data-ttu-id="83d4c-135">`Configure`는 요청 파이프라인에서 [미들웨어](xref:fundamentals/middleware)를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="83d4c-135">`Configure` defines the [middleware](xref:fundamentals/middleware) in the request pipeline.</span></span>

<span data-ttu-id="83d4c-136">자세한 내용은 [응용 프로그램 시작](xref:fundamentals/startup)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="83d4c-136">For more information, see [Application startup](xref:fundamentals/startup).</span></span>

## <a name="services"></a><span data-ttu-id="83d4c-137">서비스</span><span class="sxs-lookup"><span data-stu-id="83d4c-137">Services</span></span>

<span data-ttu-id="83d4c-138">서비스는 응용 프로그램에서 공통으로 사용해야 하는 구성 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="83d4c-138">A service is a component that is intended for common consumption in an application.</span></span> <span data-ttu-id="83d4c-139">서비스는 DI([종속성 주입](xref:fundamentals/dependency-injection))를 통해 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="83d4c-139">Services are made available through [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="83d4c-140">ASP.NET Core에는 기본적으로 [생성자 주입](xref:mvc/controllers/dependency-injection#constructor-injection)을 지원하는 네이티브 IoC(Inversion of Control) 컨테이너가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="83d4c-140">ASP.NET Core includes a native inversion of control (IoC) container that supports [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) by default.</span></span> <span data-ttu-id="83d4c-141">네이티브 컨테이너는 선택한 컨테이너로 바꿀 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="83d4c-141">The native container can be replaced with your container of choice.</span></span> <span data-ttu-id="83d4c-142">느슨한 결합의 이점 이외에 DI는 응용 프로그램 전체에서 서비스를 사용할 수 있게 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="83d4c-142">In addition to its loose coupling benefit, DI makes services available throughout your application.</span></span> <span data-ttu-id="83d4c-143">예를 들어 [로깅](xref:fundamentals/logging)은 응용 프로그램 전체에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="83d4c-143">For example, [logging](xref:fundamentals/logging) is available throughout your application.</span></span>

<span data-ttu-id="83d4c-144">자세한 내용은 [종속성 주입](xref:fundamentals/dependency-injection)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="83d4c-144">For more information, see [Dependency injection](xref:fundamentals/dependency-injection).</span></span>

## <a name="middleware"></a><span data-ttu-id="83d4c-145">미들웨어</span><span class="sxs-lookup"><span data-stu-id="83d4c-145">Middleware</span></span>

<span data-ttu-id="83d4c-146">ASP.NET Core에서 [미들웨어](xref:fundamentals/middleware)를 사용하여 요청 파이프라인을 작성합니다.</span><span class="sxs-lookup"><span data-stu-id="83d4c-146">In ASP.NET Core, you compose your request pipeline using [Middleware](xref:fundamentals/middleware).</span></span> <span data-ttu-id="83d4c-147">ASP.NET Core 미들웨어는 `HttpContext`에서 비동기 논리를 수행하고 나서 시퀀스에서 다음 미들웨어를 호출하거나 요청을 직접 종료합니다.</span><span class="sxs-lookup"><span data-stu-id="83d4c-147">ASP.NET Core middleware performs asynchronous logic on an `HttpContext` and then either invokes the next middleware in the sequence or terminates the request directly.</span></span> <span data-ttu-id="83d4c-148">“XYZ”라는 미들웨어 구성 요소는 `Configure` 메서드에서 `UseXYZ` 확장 메서드를 호출하여 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="83d4c-148">A middleware component called "XYZ" is added by invoking a `UseXYZ` extension method in the `Configure` method.</span></span>

<span data-ttu-id="83d4c-149">ASP.NET Core는 다양한 기본 제공 미들웨어 집합이 함께 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="83d4c-149">ASP.NET Core comes with a rich set of built-in middleware:</span></span>

* [<span data-ttu-id="83d4c-150">정적 파일</span><span class="sxs-lookup"><span data-stu-id="83d4c-150">Static files</span></span>](xref:fundamentals/static-files)

* [<span data-ttu-id="83d4c-151">라우팅</span><span class="sxs-lookup"><span data-stu-id="83d4c-151">Routing</span></span>](xref:fundamentals/routing)

* [<span data-ttu-id="83d4c-152">인증</span><span class="sxs-lookup"><span data-stu-id="83d4c-152">Authentication</span></span>](xref:security/authentication/index)

<span data-ttu-id="83d4c-153">ASP.NET Core에서 [OWIN](http://owin.org) 기반 미들웨어를 사용할 수 있고 자체 사용자 지정 미들웨어를 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="83d4c-153">You can use any [OWIN](http://owin.org)-based middleware with ASP.NET Core, and you can write your own custom middleware.</span></span>

<span data-ttu-id="83d4c-154">자세한 내용은 [미들웨어](xref:fundamentals/middleware) 및 [OWIN(Open Web Interface for .NET)](xref:fundamentals/owin)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="83d4c-154">For more information, see [Middleware](xref:fundamentals/middleware) and [Open Web Interface for .NET (OWIN)](xref:fundamentals/owin).</span></span>

## <a name="servers"></a><span data-ttu-id="83d4c-155">서버</span><span class="sxs-lookup"><span data-stu-id="83d4c-155">Servers</span></span>

<span data-ttu-id="83d4c-156">ASP.NET Core 호스팅 모델은 직접 요청을 수신하지 않고, 오히려 HTTP 서버 구현을 사용하여 요청을 응용 프로그램에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="83d4c-156">The ASP.NET Core hosting model does not directly listen for requests; rather, it relies on an HTTP server implementation to forward the request to the application.</span></span> <span data-ttu-id="83d4c-157">전달된 요청은 인터페이스를 통해 액세스할 수 있는 기능 개체 집합으로 래핑됩니다.</span><span class="sxs-lookup"><span data-stu-id="83d4c-157">The forwarded request is wrapped as a set of feature objects that you can access through interfaces.</span></span> <span data-ttu-id="83d4c-158">응용 프로그램은 이 집합을 `HttpContext`로 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="83d4c-158">The application composes this set into an `HttpContext`.</span></span> <span data-ttu-id="83d4c-159">ASP.NET Core에는 [Kestrel](xref:fundamentals/servers/kestrel)이라는 관리되는 플랫폼 간 웹 서버가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="83d4c-159">ASP.NET Core includes a managed, cross-platform web server, called [Kestrel](xref:fundamentals/servers/kestrel).</span></span> <span data-ttu-id="83d4c-160">일반적으로 Kestrel은 [IIS](https://iis.net) 또는 [nginx](http://nginx.org) 같은 프로덕션 웹 서버의 백그라운드에서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="83d4c-160">Kestrel is typically run behind a production web server like [IIS](https://iis.net) or [nginx](http://nginx.org).</span></span>

<span data-ttu-id="83d4c-161">자세한 내용은 [서버](xref:fundamentals/servers/index) 및 [호스팅](xref:fundamentals/hosting)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="83d4c-161">For more information, see [Servers](xref:fundamentals/servers/index) and [Hosting](xref:fundamentals/hosting).</span></span>

## <a name="content-root"></a><span data-ttu-id="83d4c-162">콘텐츠 루트</span><span class="sxs-lookup"><span data-stu-id="83d4c-162">Content root</span></span>

<span data-ttu-id="83d4c-163">콘텐츠 루트는 앱에서 사용되는 뷰, [Razor 페이지](xref:mvc/razor-pages/index) 및 정적 자산 같은 콘텐츠의 기본 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="83d4c-163">The content root is the base path to any content used by the app, such as views, [Razor Pages](xref:mvc/razor-pages/index), and static assets.</span></span> <span data-ttu-id="83d4c-164">기본적으로 콘텐츠 루트는 응용 프로그램을 호스트하는 실행 파일의 응용 프로그램 기본 경로와 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="83d4c-164">By default, the content root is the same as application base path for the executable hosting the application.</span></span> <span data-ttu-id="83d4c-165">콘텐츠 루트의 대체 위치는 `WebHostBuilder`를 사용하여 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="83d4c-165">An alternative location for content root is specified with `WebHostBuilder`.</span></span>

## <a name="web-root"></a><span data-ttu-id="83d4c-166">웹 루트</span><span class="sxs-lookup"><span data-stu-id="83d4c-166">Web root</span></span>

<span data-ttu-id="83d4c-167">응용 프로그램의 웹 루트는 CSS, JavaScript 및 이미지 파일 같은 공용 정적 리소스를 포함하는 프로젝트의 디렉터리입니다.</span><span class="sxs-lookup"><span data-stu-id="83d4c-167">The web root of an application is the directory in the project containing public, static resources like CSS, JavaScript, and image files.</span></span> <span data-ttu-id="83d4c-168">기본적으로 정적 파일 미들웨어는 웹 루트 디렉터리 및 하위 디렉터리에 있는 파일만 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="83d4c-168">By default, the static files middleware will only serve files from the web root directory and its sub-directories.</span></span> <span data-ttu-id="83d4c-169">자세한 내용은 [정적 파일 작업](xref:fundamentals/static-files)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="83d4c-169">See [working with static files](xref:fundamentals/static-files) for more info.</span></span> <span data-ttu-id="83d4c-170">웹 루트 경로는 기본적으로 */wwwroot*로 설정되지만, `WebHostBuilder`를 사용하여 다른 위치를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="83d4c-170">The web root path defaults to */wwwroot*, but you can specify a different location using the `WebHostBuilder`.</span></span>

## <a name="configuration"></a><span data-ttu-id="83d4c-171">구성</span><span class="sxs-lookup"><span data-stu-id="83d4c-171">Configuration</span></span>

<span data-ttu-id="83d4c-172">ASP.NET Core는 간단한 이름 값 쌍을 처리하기 위한 새 구성 모델을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="83d4c-172">ASP.NET Core uses a new configuration model for handling simple name-value pairs.</span></span> <span data-ttu-id="83d4c-173">새 구성 모델은 `System.Configuration` 또는 *web.config*를 기반으로 하지 않고, 오히려 구성 공급자의 정렬된 집합에서 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="83d4c-173">The new configuration model is not based on `System.Configuration` or *web.config*; rather, it pulls from an ordered set of configuration providers.</span></span> <span data-ttu-id="83d4c-174">기본 제공 구성 공급자는 환경 기반 구성을 가능하게 하는 다양한 파일 형식(XML, JSON, INI) 및 환경 변수를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="83d4c-174">The built-in configuration providers support a variety of file formats (XML, JSON, INI) and environment variables to enable environment-based configuration.</span></span> <span data-ttu-id="83d4c-175">자체 사용자 지정 구성 공급자를 작성할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="83d4c-175">You can also write your own custom configuration providers.</span></span>

<span data-ttu-id="83d4c-176">자세한 내용은 [구성](xref:fundamentals/configuration)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="83d4c-176">For more information, see [Configuration](xref:fundamentals/configuration).</span></span>

## <a name="environments"></a><span data-ttu-id="83d4c-177">환경</span><span class="sxs-lookup"><span data-stu-id="83d4c-177">Environments</span></span>

<span data-ttu-id="83d4c-178">“개발” 및 “프로덕션” 같은 환경은 ASP.NET Core의 첫 번째 클래스 개념이고 환경 변수를 사용하여 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="83d4c-178">Environments, like "Development" and "Production", are a first-class notion in ASP.NET Core and can be set using environment variables.</span></span>

<span data-ttu-id="83d4c-179">자세한 내용은 [여러 환경 사용](xref:fundamentals/environments)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="83d4c-179">For more information, see [Working with Multiple Environments](xref:fundamentals/environments).</span></span>

## <a name="net-core-vs-net-framework-runtime"></a><span data-ttu-id="83d4c-180">.NET Core 및 .NET Framework 런타임</span><span class="sxs-lookup"><span data-stu-id="83d4c-180">.NET Core vs. .NET Framework runtime</span></span>

<span data-ttu-id="83d4c-181">ASP.NET Core 응용 프로그램은 .NET Core 또는 .NET Framework 런타임을 대상으로 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="83d4c-181">An ASP.NET Core application can target the .NET Core or .NET Framework runtime.</span></span> <span data-ttu-id="83d4c-182">자세한 내용은 [.NET Core와 .NET Framework 중에 선택](https://docs.microsoft.com/dotnet/articles/standard/choosing-core-framework-server)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="83d4c-182">For more information, see [Choosing between .NET Core and .NET Framework](https://docs.microsoft.com/dotnet/articles/standard/choosing-core-framework-server).</span></span>

## <a name="additional-information"></a><span data-ttu-id="83d4c-183">추가 정보</span><span class="sxs-lookup"><span data-stu-id="83d4c-183">Additional information</span></span>

<span data-ttu-id="83d4c-184">또한 다음 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="83d4c-184">See also the following topics:</span></span>

- [<span data-ttu-id="83d4c-185">오류 처리</span><span class="sxs-lookup"><span data-stu-id="83d4c-185">Error Handling</span></span>](xref:fundamentals/error-handling)
- [<span data-ttu-id="83d4c-186">파일 공급자</span><span class="sxs-lookup"><span data-stu-id="83d4c-186">File Providers</span></span>](xref:fundamentals/file-providers)
- [<span data-ttu-id="83d4c-187">전역화 및 지역화</span><span class="sxs-lookup"><span data-stu-id="83d4c-187">Globalization and localization</span></span>](xref:fundamentals/localization)
- [<span data-ttu-id="83d4c-188">로깅</span><span class="sxs-lookup"><span data-stu-id="83d4c-188">Logging</span></span>](xref:fundamentals/logging)
- [<span data-ttu-id="83d4c-189">응용 프로그램 상태 관리</span><span class="sxs-lookup"><span data-stu-id="83d4c-189">Managing Application State</span></span>](xref:fundamentals/app-state)