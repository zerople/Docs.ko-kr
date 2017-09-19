---
title: "ASP.NET Core의 웹 서버 구현"
author: tdykstra
description: "ASP.NET Core에 대한 웹 서버 Kestrel 및 WebListener를 소개합니다. 항목을 선택하는 방법 및 역방향 프록시 서버에서 항목을 사용할 시기에 대한 지침을 제공합니다."
keywords: "ASP.NET Core, IServer, 웹 서버, Kestrel, WebListener, 역방향 프록시"
ms.author: tdykstra
manager: wpickett
ms.date: 08/03/2017
ms.topic: article
ms.assetid: dba74f39-58cd-4dee-a061-6d15f7346959
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/servers/index
ms.openlocfilehash: 54c8e1ad7d4de7f953d9801c214c0bd577304f46
ms.sourcegitcommit: 67f54fabbfa4e3942f5bfe1f8a7fdfe4a7a75358
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/19/2017
---
# <a name="web-server-implementations-in-aspnet-core"></a><span data-ttu-id="e9514-105">ASP.NET Core의 웹 서버 구현</span><span class="sxs-lookup"><span data-stu-id="e9514-105">Web server implementations in ASP.NET Core</span></span>

<span data-ttu-id="e9514-106">작성자: [Tom Dykstra](https://github.com/tdykstra), [Steve Smith](https://ardalis.com/), [Stephen Halter](https://twitter.com/halter73) 및 [Chris Ross](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="e9514-106">By [Tom Dykstra](https://github.com/tdykstra), [Steve Smith](https://ardalis.com/), [Stephen Halter](https://twitter.com/halter73), and [Chris Ross](https://github.com/Tratcher)</span></span>

<span data-ttu-id="e9514-107">ASP.NET Core 응용 프로그램은 In-process HTTP 서버 구현을 통해 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="e9514-107">An ASP.NET Core application runs with an in-process HTTP server implementation.</span></span> <span data-ttu-id="e9514-108">서버 구현은 HTTP 요청을 수신하고 `HttpContext`를 구성하는 [요청 기능](https://docs.microsoft.com/aspnet/core/fundamentals/request-features) 집합으로 응용 프로그램에 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="e9514-108">The server implementation listens for HTTP requests and surfaces them to the application as sets of [request features](https://docs.microsoft.com/aspnet/core/fundamentals/request-features) composed into an `HttpContext`.</span></span>

<span data-ttu-id="e9514-109">ASP.NET Core는 다음 두 가지 서버 구현을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="e9514-109">ASP.NET Core ships two server implementations:</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="e9514-110">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="e9514-110">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

* <span data-ttu-id="e9514-111">[Kestrel](kestrel.md)은 플랫폼 간 비동기 I/O 라이브러리인 [libuv](https://github.com/libuv/libuv)를 기반으로 하는 플랫폼 간 HTTP 서버입니다.</span><span class="sxs-lookup"><span data-stu-id="e9514-111">[Kestrel](kestrel.md) is a cross-platform HTTP server based on [libuv](https://github.com/libuv/libuv), a cross-platform asynchronous I/O library.</span></span>

* <span data-ttu-id="e9514-112">[HTTP.sys](httpsys.md)는 [Http.Sys 커널 드라이버](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx)를 기반으로 하는 Windows 전용 HTTP 서버입니다.</span><span class="sxs-lookup"><span data-stu-id="e9514-112">[HTTP.sys](httpsys.md) is a Windows-only HTTP server based on the [Http.Sys kernel driver](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx).</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="e9514-113">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="e9514-113">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

* <span data-ttu-id="e9514-114">[Kestrel](kestrel.md)은 플랫폼 간 비동기 I/O 라이브러리인 [libuv](https://github.com/libuv/libuv)를 기반으로 하는 플랫폼 간 HTTP 서버입니다.</span><span class="sxs-lookup"><span data-stu-id="e9514-114">[Kestrel](kestrel.md) is a cross-platform HTTP server based on [libuv](https://github.com/libuv/libuv), a cross-platform asynchronous I/O library.</span></span>

* <span data-ttu-id="e9514-115">[WebListener](weblistener.md)는 [Http.Sys 커널 드라이버](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx)를 기반으로 하는 Windows 전용 HTTP 서버입니다.</span><span class="sxs-lookup"><span data-stu-id="e9514-115">[WebListener](weblistener.md) is a Windows-only HTTP server based on the [Http.Sys kernel driver](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx).</span></span>

---

## <a name="kestrel"></a><span data-ttu-id="e9514-116">Kestrel</span><span class="sxs-lookup"><span data-stu-id="e9514-116">Kestrel</span></span>

<span data-ttu-id="e9514-117">Kestrel은 기본적으로 ASP.NET Core 새 프로젝트 템플릿에 포함된 웹 서버입니다.</span><span class="sxs-lookup"><span data-stu-id="e9514-117">Kestrel is the web server that is included by default in ASP.NET Core new-project templates.</span></span> 

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="e9514-118">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="e9514-118">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="e9514-119">Kestrel을 단독으로 사용하거나 IIS, Nginx 또는 Apache 같은 *역방향 프록시 서버*와 함께 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e9514-119">You can use Kestrel by itself or with a *reverse proxy server*, such as IIS, Nginx, or Apache.</span></span> <span data-ttu-id="e9514-120">역방향 프록시 서버는 인터넷에서 HTTP 요청을 수신하고 몇몇 사전 처리 후에 Kestrel에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="e9514-120">A reverse proxy server receives HTTP requests from the Internet and forwards them to Kestrel after some preliminary handling.</span></span>

![Kestrel은 역방향 프록시 서버 없이 직접 인터넷과 통신합니다.](kestrel/_static/kestrel-to-internet2.png)

![Kestrel은 IIS, Nginx 또는 Apache 같은 역방향 프록시 서버를 통해 간접적으로 인터넷과 통신합니다.](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="e9514-123">Kestrel이 내부 네트워크에만 노출되는 경우 역방향 프록시 서버를 사용하거나 사용하지 않는 구성을 사용할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e9514-123">Either configuration &mdash; with or without a reverse proxy server &mdash; can also be used if Kestrel is exposed only to an internal network.</span></span>

<span data-ttu-id="e9514-124">Kestrel을 역방향 프록시와 함께 사용할 경우에 대한 자세한 내용은 [Kestrel 소개](kestrel.md)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e9514-124">For information about when to use Kestrel with a reverse proxy, see [Introduction to Kestrel](kestrel.md).</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="e9514-125">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="e9514-125">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="e9514-126">응용 프로그램이 내부 네트워크의 요청만 허용할 경우 Kestrel을 단독으로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e9514-126">If your application accepts requests only from an internal network, you can use Kestrel by itself.</span></span>

![Kestrel은 내부 네트워크와 직접 통신합니다.](kestrel/_static/kestrel-to-internal.png)

<span data-ttu-id="e9514-128">응용 프로그램을 인터넷에 노출할 경우 IIS, Nginx 또는 Apache를 *역방향 프록시 서버*로 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e9514-128">If you expose your application to the Internet, you must use IIS, Nginx, or Apache as a *reverse proxy server*.</span></span> <span data-ttu-id="e9514-129">역방향 프록시 서버는 인터넷에서 HTTP 요청을 수신하고 다음 다이어그램과 같이 몇몇 사전 처리 후에 Kestrel에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="e9514-129">A reverse proxy server receives HTTP requests from the Internet and forwards them to Kestrel after some preliminary handling, as shown in the following diagram.</span></span>

![Kestrel은 IIS, Nginx 또는 Apache 같은 역방향 프록시 서버를 통해 간접적으로 인터넷과 통신합니다.](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="e9514-131">인터넷에서 트래픽에 노출되는 에지 배포에 역방향 프록시를 사용하는 가장 중요한 이유는 보안입니다.</span><span class="sxs-lookup"><span data-stu-id="e9514-131">The most important reason for using a reverse proxy for edge deployments (exposed to traffic from the Internet) is security.</span></span> <span data-ttu-id="e9514-132">Kestrel 1.x 버전에는 공격에 대한 전체 방어 기능이 포함되어 있지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e9514-132">The 1.x versions of Kestrel don't have a full complement of defenses against attacks.</span></span> <span data-ttu-id="e9514-133">이 방어 기능에는 적절한 시간 제한, 크기 제한, 동시 연결 제한이 포함되지만 이것으로 제한되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e9514-133">This includes, but isn't limited to, appropriate timeouts, size limits, and concurrent connection limits.</span></span>

<span data-ttu-id="e9514-134">Kestrel을 역방향 프록시와 함께 사용할 경우에 대한 자세한 내용은 [Kestrel 소개](kestrel.md)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e9514-134">For information about when to use Kestrel with a reverse proxy, see [Introduction to Kestrel](kestrel.md).</span></span>

---

<span data-ttu-id="e9514-135">Kestrel 또는 [사용자 지정 서버 구현](#custom-servers)이 없으면 IIS, Nginx 또는 Apache를 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="e9514-135">You can't use IIS, Nginx, or Apache without Kestrel or a [custom server implementation](#custom-servers).</span></span> <span data-ttu-id="e9514-136">ASP.NET Core는 플랫폼 간에 일관되게 동작하도록 자체 프로세스로 실행되도록 고안되었습니다.</span><span class="sxs-lookup"><span data-stu-id="e9514-136">ASP.NET Core was designed to run in its own process so that it can behave consistently across platforms.</span></span> <span data-ttu-id="e9514-137">IIS, Nginx 및 Apache에는 자체 시작 프로세스와 환경이 설명되어 있습니다. 이러한 항목을 직접 사용하려면 ASP.NET Core를 각 항목의 필요에 맞게 조정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e9514-137">IIS, Nginx, and Apache dictate their own startup process and environment; to use them directly, ASP.NET Core would have to adapt to the needs of each one.</span></span> <span data-ttu-id="e9514-138">Kestrel 같은 웹 서버 구현을 사용하면 ASP.NET Core가 시작 프로세스 및 환경을 제어할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e9514-138">Using a web server implementation such as Kestrel gives ASP.NET Core control over the startup process and environment.</span></span> <span data-ttu-id="e9514-139">따라서 ASP.NET Core를 IIS, Nginx 또는 Apache에 맞게 조정하는 대신 해당 웹 서버를 Kestrel에 대한 프록시 요청으로 설정하면 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e9514-139">So rather than trying to adapt ASP.NET Core to IIS, Nginx, or Apache, you just set up those web servers to proxy requests to Kestrel.</span></span> <span data-ttu-id="e9514-140">이렇게 설정하면 `Program.Main` 및 `Startup` 클래스는 배포하는 위치와 관계없이 근본적으로 동일할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e9514-140">This arrangement allows your `Program.Main` and `Startup` classes to be essentially the same no matter where you deploy.</span></span>

### <a name="iis-with-kestrel"></a><span data-ttu-id="e9514-141">IIS 및 Kestrel</span><span class="sxs-lookup"><span data-stu-id="e9514-141">IIS with Kestrel</span></span>

<span data-ttu-id="e9514-142">IIS 또는 IIS Express를 ASP.NET Core에 대한 역방향 프록시로 사용할 경우, ASP.NET Core 응용 프로그램은 IIS 작업자 프로세스에서 분리된 프로세스로 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="e9514-142">When you use IIS or IIS Express as a reverse proxy for ASP.NET Core, the ASP.NET Core application runs in a process separate from the IIS worker process.</span></span> <span data-ttu-id="e9514-143">IIS 프로세스에서는 역방향 프록시 관계를 조정하기 위해 특수 IIS 모듈이 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="e9514-143">In the IIS process, a special IIS module runs to coordinate the reverse proxy relationship.</span></span>  <span data-ttu-id="e9514-144">이것은 *ASP.NET Core 모듈*입니다.</span><span class="sxs-lookup"><span data-stu-id="e9514-144">This is the *ASP.NET Core Module*.</span></span> <span data-ttu-id="e9514-145">ASP.NET Core 모듈의 기본 함수는 ASP.NET Core 응용 프로그램을 시작하고, 작동 중단 시 해당 응용 프로그램을 다시 시작하고, 응용 프로그램에 HTTP 트래픽을 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="e9514-145">The primary functions of the ASP.NET Core Module are to start the ASP.NET Core application, restart it when it crashes, and forward HTTP traffic to it.</span></span> <span data-ttu-id="e9514-146">자세한 내용은 [ASP.NET Core 모듈](aspnet-core-module.md)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e9514-146">For more information, see [ASP.NET Core Module](aspnet-core-module.md).</span></span> 

### <a name="nginx-with-kestrel"></a><span data-ttu-id="e9514-147">Nginx 및 Kestrel</span><span class="sxs-lookup"><span data-stu-id="e9514-147">Nginx with Kestrel</span></span>

<span data-ttu-id="e9514-148">Linux에서 Kestrel에 대한 역방향 프록시 서버로 Nginx를 사용하는 방법에 대한 자세한 내용은 [Linux 프로덕션 환경에 게시](../../publishing/linuxproduction.md)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e9514-148">For information about how to use Nginx on Linux as a reverse proxy server for Kestrel, see [Publish to a Linux Production Environment](../../publishing/linuxproduction.md).</span></span>

### <a name="apache-with-kestrel"></a><span data-ttu-id="e9514-149">Apache 및 Kestrel</span><span class="sxs-lookup"><span data-stu-id="e9514-149">Apache with Kestrel</span></span>

<span data-ttu-id="e9514-150">Linux에서 Kestrel에 대한 역방향 프록시 서버로 Apache를 사용하는 방법에 대한 자세한 내용은 [역방향 프록시로 Apache 웹 서버 사용](../../publishing/apache-proxy.md)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e9514-150">For information about how to use Apache on Linux as a reverse proxy server for Kestrel, see [Using Apache Web Server as a reverse proxy](../../publishing/apache-proxy.md).</span></span>

## <a name="httpsys"></a><span data-ttu-id="e9514-151">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="e9514-151">HTTP.sys</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="e9514-152">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="e9514-152">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="e9514-153">Windows에서 ASP.NET Core 앱을 실행할 경우 Kestrel 대신 HTTP.sys를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e9514-153">If you run your ASP.NET Core app on Windows, HTTP.sys is an alternative to Kestrel.</span></span> <span data-ttu-id="e9514-154">인터넷에 앱을 노출하고 Kestrel이 지원하지 않는 HTTP.sys 기능을 사용해야 하는 시나리오의 경우 HTTP.sys를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e9514-154">You can use HTTP.sys for scenarios where you expose your app to the Internet and you need HTTP.sys features that Kestrel doesn't support.</span></span> 

![HTTP.sys는 인터넷과 직접 통신합니다.](httpsys/_static/httpsys-to-internet.png)

<span data-ttu-id="e9514-156">HTTP.sys는 내부 네트워크에만 노출되는 응용 프로그램에도 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e9514-156">HTTP.sys can also be used for applications that are exposed only to an internal network.</span></span> 

![HTTP.sys는 내부 네트워크와 직접 통신합니다.](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="e9514-158">내부 네트워크 시나리오의 경우 최고의 성능을 얻기 위해 일반적으로 Kestrel이 권장되지만 일부 시나리오에서는 HTTP.sys만 제공하는 기능을 사용해야 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e9514-158">For internal network scenarios, Kestrel is generally recommended for best performance; but in some scenarios, you might want to use a feature that only HTTP.sys offers.</span></span> <span data-ttu-id="e9514-159">HTTP.sys 기능에 대한 자세한 내용은 [HTTP.sys](httpsys.md)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e9514-159">For information about HTTP.sys features, see [HTTP.sys](httpsys.md).</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="e9514-160">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="e9514-160">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="e9514-161">ASP.NET Core 1.x에서는 HTTP.sys의 이름이 WebListener입니다.</span><span class="sxs-lookup"><span data-stu-id="e9514-161">HTTP.sys is named WebListener in ASP.NET Core 1.x.</span></span> <span data-ttu-id="e9514-162">Windows에서 ASP.NET Core 앱을 실행할 경우 앱을 인터넷에 노출하려고 하지만 IIS를 사용할 수 없는 시나리오의 경우 WebListener를 대신 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e9514-162">If you run your ASP.NET Core app on Windows, WebListener is an alternative that you can use for scenarios where you want to expose your app to the Internet but you can't use IIS.</span></span>

![WebListener는 인터넷과 직접 통신합니다.](weblistener/_static/weblistener-to-internet.png)

<span data-ttu-id="e9514-164">Kestrel이 지원하지 않는 WebListener가 필요한 경우 내부 네트워크에만 노출되는 응용 프로그램에는 Kestrel 대신 WebListener를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e9514-164">WebListener can also be used in place of Kestrel for applications that are exposed only to an internal network, if you need WebListener features that Kestrel doesn't support.</span></span> 

![WebListener는 내부 네트워크와 직접 통신합니다.](weblistener/_static/weblistener-to-internal.png)

<span data-ttu-id="e9514-166">내부 네트워크 시나리오의 경우 성능을 극대화하기 위해 일반적으로 Kestrel이 권장되지만, 일부 시나리오에서는 WebListener만 제공하는 기능을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e9514-166">For internal network scenarios, Kestrel is generally recommended for best performance, but in some scenarios you might want to use a feature that only WebListener offers.</span></span> <span data-ttu-id="e9514-167">WebListener 기능에 대한 자세한 내용은 [WebListener](weblistener.md)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e9514-167">For information about WebListener features, see [WebListener](weblistener.md).</span></span>

---

## <a name="notes-about-aspnet-core-server-infrastructure"></a><span data-ttu-id="e9514-168">ASP.NET Core 서버 인프라에 대한 참고</span><span class="sxs-lookup"><span data-stu-id="e9514-168">Notes about ASP.NET Core server infrastructure</span></span>

<span data-ttu-id="e9514-169">`Startup` 클래스 `Configure` 메서드에서 사용 가능한 [`IApplicationBuilder`](/aspnet/core/api/microsoft.aspnetcore.builder.iapplicationbuilder)는 [`IFeatureCollection`](/aspnet/core/api/microsoft.aspnetcore.http.features.ifeaturecollection) 형식의 `ServerFeatures` 속성을 노출합니다.</span><span class="sxs-lookup"><span data-stu-id="e9514-169">The [`IApplicationBuilder`](/aspnet/core/api/microsoft.aspnetcore.builder.iapplicationbuilder) available in the `Startup` class `Configure` method exposes the `ServerFeatures` property of type [`IFeatureCollection`](/aspnet/core/api/microsoft.aspnetcore.http.features.ifeaturecollection).</span></span> <span data-ttu-id="e9514-170">Kestrel 및 WebListener는 둘 다 단일 기능인 [`IServerAddressesFeature`](/aspnet/core/api/microsoft.aspnetcore.hosting.server.features.iserveraddressesfeature)만 노출하지만, 다른 서버 구현이 추가 기능을 노출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e9514-170">Kestrel and WebListener both expose only a single feature, [`IServerAddressesFeature`](/aspnet/core/api/microsoft.aspnetcore.hosting.server.features.iserveraddressesfeature), but different server implementations may expose additional functionality.</span></span>

<span data-ttu-id="e9514-171">`IServerAddressesFeature`를 사용하여 런타임에 서버 구현이 바인딩된 포트를 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e9514-171">`IServerAddressesFeature` can be used to find out which port the server implementation has bound to at runtime.</span></span>

## <a name="custom-servers"></a><span data-ttu-id="e9514-172">사용자 지정 서버</span><span class="sxs-lookup"><span data-stu-id="e9514-172">Custom servers</span></span>

<span data-ttu-id="e9514-173">기본 제공 서버가 필요에 맞지 않으면 사용자 지정 서버 구현을 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e9514-173">If the built-in servers don't meet your needs, you can create a custom server implementation.</span></span> <span data-ttu-id="e9514-174">[OWIN(Open Web Interface for .NET) 가이드](../owin.md)에서는 [Nowin](https://github.com/Bobris/Nowin) 기반 [IServer](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.hosting.server.iserver) 구현을 작성하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="e9514-174">The [Open Web Interface for .NET (OWIN) guide](../owin.md) demonstrates how to write a [Nowin](https://github.com/Bobris/Nowin)-based [IServer](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.hosting.server.iserver) implementation.</span></span> <span data-ttu-id="e9514-175">응용 프로그램에 필요한 기능 인터페이스만 자유롭게 구현할 수 있습니다. 단, 최소한 [IHttpRequestFeature](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.http.features.ihttprequestfeature) 및 [IHttpResponseFeature](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.http.features.ihttpresponsefeature)를 지원해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e9514-175">You're free to implement just the feature interfaces your application needs, though at a minimum you must support [IHttpRequestFeature](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.http.features.ihttprequestfeature) and [IHttpResponseFeature](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.http.features.ihttpresponsefeature).</span></span>

## <a name="next-steps"></a><span data-ttu-id="e9514-176">다음 단계</span><span class="sxs-lookup"><span data-stu-id="e9514-176">Next steps</span></span>

<span data-ttu-id="e9514-177">자세한 내용은 다음 리소스를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e9514-177">For more information, see the following resources:</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="e9514-178">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="e9514-178">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

- [<span data-ttu-id="e9514-179">Kestrel</span><span class="sxs-lookup"><span data-stu-id="e9514-179">Kestrel</span></span>](kestrel.md)
- [<span data-ttu-id="e9514-180">Kestrel 및 IIS</span><span class="sxs-lookup"><span data-stu-id="e9514-180">Kestrel with IIS</span></span>](aspnet-core-module.md)
- [<span data-ttu-id="e9514-181">Kestrel 및 Nginx</span><span class="sxs-lookup"><span data-stu-id="e9514-181">Kestrel with Nginx</span></span>](../../publishing/linuxproduction.md)
- [<span data-ttu-id="e9514-182">Kestrel 및 Apache</span><span class="sxs-lookup"><span data-stu-id="e9514-182">Kestrel with Apache</span></span>](../../publishing/apache-proxy.md)
- [<span data-ttu-id="e9514-183">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="e9514-183">HTTP.sys</span></span>](httpsys.md)

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="e9514-184">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="e9514-184">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

- [<span data-ttu-id="e9514-185">Kestrel</span><span class="sxs-lookup"><span data-stu-id="e9514-185">Kestrel</span></span>](kestrel.md)
- [<span data-ttu-id="e9514-186">Kestrel 및 IIS</span><span class="sxs-lookup"><span data-stu-id="e9514-186">Kestrel with IIS</span></span>](aspnet-core-module.md)
- [<span data-ttu-id="e9514-187">Kestrel 및 Nginx</span><span class="sxs-lookup"><span data-stu-id="e9514-187">Kestrel with Nginx</span></span>](../../publishing/linuxproduction.md)
- [<span data-ttu-id="e9514-188">Kestrel 및 Apache</span><span class="sxs-lookup"><span data-stu-id="e9514-188">Kestrel with Apache</span></span>](../../publishing/apache-proxy.md)
- [<span data-ttu-id="e9514-189">WebListener</span><span class="sxs-lookup"><span data-stu-id="e9514-189">WebListener</span></span>](weblistener.md)

---
