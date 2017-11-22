---
title: "IIS가 있는 Windows에서 ASP.NET Core 호스팅"
author: guardrex
description: "Windows Server IIS(인터넷 정보 서비스)를 구성하고 ASP.NET Core 응용 프로그램을 배포합니다."
keywords: "ASP.NET Core, 인터넷 정보 서비스, IIS, Windows Server, 호스팅 번들, ASP.NET Core 모듈, 웹 배포"
ms.author: riande
manager: wpickett
ms.date: 03/13/2017
ms.topic: article
ms.assetid: a4449ad3-5bad-410c-afa7-dc32d832b552
ms.technology: aspnet
ms.prod: asp.net-core
uid: publishing/iis
ms.openlocfilehash: e9e9019d5b879498e8800bb579c177dd3ad64061
ms.sourcegitcommit: 96af03c9f44f7c206e68ae3ef8596068e6b4e5fd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2017
---
# <a name="host-aspnet-core-on-windows-with-iis"></a><span data-ttu-id="34e4c-104">IIS가 있는 Windows에서 ASP.NET Core 호스팅</span><span class="sxs-lookup"><span data-stu-id="34e4c-104">Host ASP.NET Core on Windows with IIS</span></span>

<span data-ttu-id="34e4c-105">이 문서의 작성자: [Luke Latham](https://github.com/guardrex) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="34e4c-105">By [Luke Latham](https://github.com/guardrex) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

## <a name="supported-operating-systems"></a><span data-ttu-id="34e4c-106">지원되는 운영 체제</span><span class="sxs-lookup"><span data-stu-id="34e4c-106">Supported operating systems</span></span>

<span data-ttu-id="34e4c-107">지원되는 운영 체제는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-107">The following operating systems are supported:</span></span>

* <span data-ttu-id="34e4c-108">Windows 7 이상</span><span class="sxs-lookup"><span data-stu-id="34e4c-108">Windows 7 and newer</span></span>
* <span data-ttu-id="34e4c-109">Windows Server 2008 R2 이상&#8224;</span><span class="sxs-lookup"><span data-stu-id="34e4c-109">Windows Server 2008 R2 and newer&#8224;</span></span>

<span data-ttu-id="34e4c-110">&#8224;개념적으로 이 문서에서 설명하는 IIS 구성은 Nano Server IIS에서 ASP.NET Core 응용 프로그램을 호스팅하는 데에도 적용되지만 구체적인 지침은 [Nano 서버에서 실행되는 IIS가 있는 ASP.NET Core](xref:tutorials/nano-server)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="34e4c-110">&#8224;Conceptually, the IIS configuration described in this document also applies to hosting ASP.NET Core applications on Nano Server IIS, but refer to [ASP.NET Core with IIS on Nano Server](xref:tutorials/nano-server) for specific instructions.</span></span>

<span data-ttu-id="34e4c-111">[HTTP.sys 서버](xref:fundamentals/servers/httpsys)(이전에는 [WebListener](xref:fundamentals/servers/weblistener)라고 함)는 IIS 역방향 프록시 구성에서 작동하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-111">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called [WebListener](xref:fundamentals/servers/weblistener)) won't work in a reverse-proxy configuration with IIS.</span></span> <span data-ttu-id="34e4c-112">이에 따라 [Kestrel 서버](xref:fundamentals/servers/kestrel)를 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-112">You must use the [Kestrel server](xref:fundamentals/servers/kestrel).</span></span>

## <a name="iis-configuration"></a><span data-ttu-id="34e4c-113">IIS 구성</span><span class="sxs-lookup"><span data-stu-id="34e4c-113">IIS configuration</span></span>

<span data-ttu-id="34e4c-114">**웹 서버(IIS)** 역할을 사용하도록 설정하고 역할 서비스를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-114">Enable the **Web Server (IIS)** role and establish role services.</span></span>

### <a name="windows-desktop-operating-systems"></a><span data-ttu-id="34e4c-115">Windows 데스크톱 운영 체제</span><span class="sxs-lookup"><span data-stu-id="34e4c-115">Windows desktop operating systems</span></span>

<span data-ttu-id="34e4c-116">**제어판** > **프로그램** > **프로그램 및 기능** > **Windows 기능 Windows 기능 사용/사용 안 함**(화면 왼쪽)으로 차례로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-116">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span> <span data-ttu-id="34e4c-117">**인터넷 정보 서비스** 및 **웹 관리 도구** 그룹을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-117">Open the group for **Internet Information Services** and **Web Management Tools**.</span></span> <span data-ttu-id="34e4c-118">**IIS 관리 콘솔** 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-118">Check the box for **IIS Management Console**.</span></span> <span data-ttu-id="34e4c-119">**World Wide Web 서비스** 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-119">Check the box for **World Wide Web Services**.</span></span> <span data-ttu-id="34e4c-120">**World Wide Web 서비스**의 기본 기능을 허용하거나 필요에 따라 IIS 기능을 사용자 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-120">Accept the default features for **World Wide Web Services** or customize the IIS features to suit your needs.</span></span>

![Windows 기능에서 선택된 IIS 관리 콘솔 및 World Wide Web 서비스](iis/_static/windows-features-win10.png)

### <a name="windows-server-operating-systems"></a><span data-ttu-id="34e4c-122">Windows Server 운영 체제</span><span class="sxs-lookup"><span data-stu-id="34e4c-122">Windows Server operating systems</span></span>

<span data-ttu-id="34e4c-123">서버 운영 체제의 경우 **관리** 메뉴 또는 **서버 관리자**의 링크를 통해 **역할 및 기능 추가** 마법사를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-123">For server operating systems, use the **Add Roles and Features** wizard via the **Manage** menu or the link in **Server Manager**.</span></span> <span data-ttu-id="34e4c-124">**서버 역할** 단계에서 **웹 서버(IIS)** 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-124">On the **Server Roles** step, check the box for **Web Server (IIS)**.</span></span>

![서버 역할 선택 단계에서 선택된 웹 서버 IIS 역할](iis/_static/server-roles-ws2016.png)

<span data-ttu-id="34e4c-126">**역할 서비스** 단계에서 원하는 IIS 역할 서비스를 선택하거나 제공된 기본 역할 서비스를 받아들입니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-126">On the **Role services** step, select the IIS role services you desire or accept the default role services provided.</span></span>

![역할 서비스 선택 단계에서 선택된 기본 역할 서비스](iis/_static/role-services-ws2016.png)

<span data-ttu-id="34e4c-128">**확인** 단계를 진행하여 웹 서버 역할 및 서비스를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-128">Proceed through the **Confirmation** step to install the web server role and services.</span></span> <span data-ttu-id="34e4c-129">웹 서버(IIS) 역할을 설치한 후에 서버/IIS를 다시 시작할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-129">A server/IIS restart isn't required after installing the Web Server (IIS) role.</span></span>

## <a name="install-the-net-core-windows-server-hosting-bundle"></a><span data-ttu-id="34e4c-130">.NET Core Windows Server 호스팅 번들 설치</span><span class="sxs-lookup"><span data-stu-id="34e4c-130">Install the .NET Core Windows Server Hosting bundle</span></span>

1. <span data-ttu-id="34e4c-131">호스팅 시스템에 [.NET Core Windows Server 호스팅 번들](https://download.microsoft.com/download/5/C/1/5C190037-632B-443D-842D-39085F02E1E8/DotNetCore.2.0.3-WindowsHosting.exe)을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-131">Install the [.NET Core Windows Server Hosting bundle](https://download.microsoft.com/download/5/C/1/5C190037-632B-443D-842D-39085F02E1E8/DotNetCore.2.0.3-WindowsHosting.exe) on the hosting system.</span></span> <span data-ttu-id="34e4c-132">번들은 .NET Core 런타임, .NET Core 라이브러리 및 [ASP.NET Core 모듈](xref:fundamentals/servers/aspnet-core-module)을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-132">The bundle installs the .NET Core Runtime, .NET Core Library, and the [ASP.NET Core Module](xref:fundamentals/servers/aspnet-core-module).</span></span> <span data-ttu-id="34e4c-133">이 모듈은 IIS와 Kestrel 서버 사이에 역방향 프록시를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-133">The module creates the reverse-proxy between IIS and the Kestrel server.</span></span> <span data-ttu-id="34e4c-134">시스템이 인터넷에 연결되지 않은 경우 [Microsoft Visual C++ 2015 재배포 가능 패키지](https://www.microsoft.com/download/details.aspx?id=53840)를 설치한 후에 .NET Core Windows Server 호스팅 번들을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-134">If the system doesn't have an Internet connection, obtain and install the [Microsoft Visual C++ 2015 Redistributable](https://www.microsoft.com/download/details.aspx?id=53840) before installing the .NET Core Windows Server Hosting bundle.</span></span>

2. <span data-ttu-id="34e4c-135">시스템을 다시 시작하거나 명령 프롬프트에서 **net stop was /y**에 이어 **net start w3svc**를 실행하여 시스템 PATH에 대한 변경 내용을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-135">Restart the system or execute **net stop was /y** followed by **net start w3svc** from a command prompt to pick up a change to the system PATH.</span></span>

> [!NOTE]
> <span data-ttu-id="34e4c-136">IIS 공유 구성을 사용하는 경우 [IIS 공유 구성을 사용하는 ASP.NET Core 모듈](xref:hosting/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="34e4c-136">If you use an IIS Shared Configuration, see [ASP.NET Core Module with IIS Shared Configuration](xref:hosting/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span></span>

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a><span data-ttu-id="34e4c-137">Visual Studio을 사용하여 게시할 때 웹 배포 설치</span><span class="sxs-lookup"><span data-stu-id="34e4c-137">Install Web Deploy when publishing with Visual Studio</span></span>

<span data-ttu-id="34e4c-138">[Visual Studio](https://www.visualstudio.com/vs/)에서 [웹 배포](/iis/publish/using-web-deploy/introduction-to-web-deploy)를 사용하여 응용 프로그램을 배포하려는 경우 호스팅 시스템에 최신 버전의 웹 배포를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-138">If you intend to deploy your applications with [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) in [Visual Studio](https://www.visualstudio.com/vs/), install the latest version of Web Deploy on the hosting system.</span></span> <span data-ttu-id="34e4c-139">웹 배포를 설치하려면 [WebPI(웹 플랫폼 설치 관리자)](https://www.microsoft.com/web/downloads/platform.aspx)를 사용하거나 [Microsoft 다운로드 센터](https://www.microsoft.com/download/details.aspx?id=43717)에서 설치 관리자를 직접 구할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-139">To install Web Deploy, you can use the [Web Platform Installer (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) or obtain an installer directly from the [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=43717).</span></span> <span data-ttu-id="34e4c-140">WebPI를 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-140">The preferred method is to use WebPI.</span></span> <span data-ttu-id="34e4c-141">WebPI는 호스팅 공급자에 대한 독립 실행형 설치 및 구성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-141">WebPI offers a standalone setup and a configuration for hosting providers.</span></span>

## <a name="application-configuration"></a><span data-ttu-id="34e4c-142">응용 프로그램 구성</span><span class="sxs-lookup"><span data-stu-id="34e4c-142">Application configuration</span></span>

### <a name="enabling-the-iisintegration-components"></a><span data-ttu-id="34e4c-143">IISIntegration 구성 요소 사용</span><span class="sxs-lookup"><span data-stu-id="34e4c-143">Enabling the IISIntegration components</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="34e4c-144">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="34e4c-144">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="34e4c-145">일반적인 *Program.cs*는 [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder)를 호출하여 호스트 설정을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-145">A typical *Program.cs* calls [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) to begin setting up a host.</span></span> <span data-ttu-id="34e4c-146">`CreateDefaultBuilder`는 [Kestrel](xref:fundamentals/servers/kestrel)을 웹 서버로 구성하고 [ASP.NET Core 모듈](xref:fundamentals/servers/aspnet-core-module)의 기본 경로 및 포트를 구성하여 IIS 통합을 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-146">`CreateDefaultBuilder` configures [Kestrel](xref:fundamentals/servers/kestrel) as the web server and enables IIS integration by configuring the base path and port for the [ASP.NET Core Module](xref:fundamentals/servers/aspnet-core-module):</span></span>

```csharp
public static IWebHost BuildWebHost(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        ...
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="34e4c-147">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="34e4c-147">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="34e4c-148">앱 종속성에 [Microsoft.AspNetCore.Server.IISIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IISIntegration/) 패키지에 대한 종속성을 포함시킵니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-148">Include a dependency on the [Microsoft.AspNetCore.Server.IISIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IISIntegration/) package in your app dependencies.</span></span> <span data-ttu-id="34e4c-149">*UseIISIntegration* 확장 메서드를 *WebHostBuilder*에 추가하여 IIS 통합 미들웨어를 앱에 통합합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-149">Incorporate IIS Integration middleware into the app by adding the *UseIISIntegration* extension method to *WebHostBuilder*:</span></span>

```csharp
var host = new WebHostBuilder()
    .UseKestrel()
    .UseIISIntegration()
    ...
```

<span data-ttu-id="34e4c-150">`UseKestrel` 및 `UseIISIntegration`이 둘 다 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-150">Both `UseKestrel` and `UseIISIntegration` are required.</span></span> <span data-ttu-id="34e4c-151">*UseIISIntegration*을 호출하는 코드는 코드 이식성에 영향을 주지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-151">Code calling *UseIISIntegration* doesn't affect code portability.</span></span> <span data-ttu-id="34e4c-152">앱이 IIS 배후에서 실행되지 않는 경우(예를 들어 앱이 Kestrel에서 바로 실행되는 경우) `UseIISIntegration`은 작동하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-152">If the app isn't run behind IIS (for example, the app is run directly on Kestrel), `UseIISIntegration` no-ops.</span></span>

---

<span data-ttu-id="34e4c-153">호스팅에 대한 자세한 내용은 [ASP.NET Core에서 호스팅](xref:fundamentals/hosting)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="34e4c-153">For more information on hosting, see [Hosting in ASP.NET Core](xref:fundamentals/hosting).</span></span>

### <a name="iis-options"></a><span data-ttu-id="34e4c-154">IIS 옵션</span><span class="sxs-lookup"><span data-stu-id="34e4c-154">IIS options</span></span>

<span data-ttu-id="34e4c-155">*IISIntegration* 서비스 옵션을 구성하려면 *ConfigureServices*에 *IISOptions*에 대한 서비스 구성을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-155">To configure *IISIntegration* service options, include a service configuration for *IISOptions* in *ConfigureServices*:</span></span>

```csharp
services.Configure<IISOptions>(options => 
{
    ...
});
```

| <span data-ttu-id="34e4c-156">옵션</span><span class="sxs-lookup"><span data-stu-id="34e4c-156">Option</span></span>                         | <span data-ttu-id="34e4c-157">기본</span><span class="sxs-lookup"><span data-stu-id="34e4c-157">Default</span></span> | <span data-ttu-id="34e4c-158">설정</span><span class="sxs-lookup"><span data-stu-id="34e4c-158">Setting</span></span> |
| ------------------------------ | ------- | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="34e4c-159">`true`면 인증 미들웨어가 `HttpContext.User`를 설정하고 제네릭 과제에 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-159">If `true`, the authentication middleware sets the `HttpContext.User` and responds to generic challenges.</span></span> <span data-ttu-id="34e4c-160">`false`면 인증 미들웨어가 ID(`HttpContext.User`)만 제공하고, `AuthenticationScheme`에 의해 명시적으로 요청될 때 과제에 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-160">If `false`, the authentication middleware only provides an identity (`HttpContext.User`) and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="34e4c-161">IIS에서 Windows 인증은 `AutomaticAuthentication`이 작동하기 위해 사용하도록 설정되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-161">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="34e4c-162">로그인 페이지에서 사용자에게 나타나는 표시 이름을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-162">Sets the display name shown to users on login pages.</span></span> |
| `ForwardClientCertificate`     | `true`  | <span data-ttu-id="34e4c-163">`true`면서 `MS-ASPNETCORE-CLIENTCERT` 요청 헤더가 있는 경우 `HttpContext.Connection.ClientCertificate`가 채워집니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-163">If `true` and the `MS-ASPNETCORE-CLIENTCERT` request header is present, the `HttpContext.Connection.ClientCertificate` is populated.</span></span> |

### <a name="webconfig"></a><span data-ttu-id="34e4c-164">web.config</span><span class="sxs-lookup"><span data-stu-id="34e4c-164">web.config</span></span>

<span data-ttu-id="34e4c-165">*web.config* 파일은 주로 ASP.NET Core 모듈을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-165">The *web.config* file primarily configures the ASP.NET Core Module.</span></span> <span data-ttu-id="34e4c-166">선택적으로 추가 IIS 구성 설정을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-166">It may optionally provide additional IIS configuration settings.</span></span> <span data-ttu-id="34e4c-167">*web.config*의 생성, 변환 및 게시는 .NET Core Web SDK(`Microsoft.NET.Sdk.Web`)에서 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-167">Creating, transforming, and publishing *web.config* is handled by the .NET Core Web SDK (`Microsoft.NET.Sdk.Web`).</span></span> <span data-ttu-id="34e4c-168">SDK는 프로젝트 파일의 맨 위(*.csproj*), `<Project Sdk="Microsoft.NET.Sdk.Web">`에서 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-168">The SDK is set  at the top of the project file (*.csproj*), `<Project Sdk="Microsoft.NET.Sdk.Web">`.</span></span> <span data-ttu-id="34e4c-169">SDK가 *web.config* 파일을 변환하지 못하게 하려면 프로젝트 파일에 `true` 설정을 갖는 **\<IsTransformWebConfigDisabled>** 속성을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-169">To prevent the SDK from transforming the *web.config* file, add the **\<IsTransformWebConfigDisabled>** property to the project file with a setting of `true`:</span></span>

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

<span data-ttu-id="34e4c-170">*dotnet publish* 또는 Visual Studio 게시를 사용하여 게시할 때 프로젝트에 *web.config* 파일이 없으면 [게시되는 출력](xref:hosting/directory-structure)에 해당 파일이 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-170">If you don't have a *web.config* file in the project when you publish with *dotnet publish* or with Visual Studio publish, the file is created for you in [published output](xref:hosting/directory-structure).</span></span> <span data-ttu-id="34e4c-171">프로젝트에 *web.config* 파일이 있는 경우 올바른 *processPath* 및 *arguments*를 통해 변환되어 [ASP.NET Core 모듈](xref:fundamentals/servers/aspnet-core-module)을 구성하고 게시된 출력으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-171">If you have a *web.config* file in your project, it's transformed with the correct *processPath* and *arguments* to configure the [ASP.NET Core Module](xref:fundamentals/servers/aspnet-core-module) and moved to published output.</span></span> <span data-ttu-id="34e4c-172">변환은 파일에 포함된 IIS 구성 설정에 영향을 주지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-172">The transformation doesn't touch IIS configuration settings that you've included in the file.</span></span>

## <a name="create-the-iis-website"></a><span data-ttu-id="34e4c-173">IIS 웹 사이트 만들기</span><span class="sxs-lookup"><span data-stu-id="34e4c-173">Create the IIS Website</span></span>

1. <span data-ttu-id="34e4c-174">대상 IIS 시스템에서는 [디렉터리 구조](xref:hosting/directory-structure)에서 설명하는 앱의 게시된 폴더와 파일을 포함할 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-174">On the target IIS system, create a folder to contain the app's published folders and files, which are described in [Directory Structure](xref:hosting/directory-structure).</span></span>

2. <span data-ttu-id="34e4c-175">만든 폴더 내에서 *logs* 폴더를 만들어 stdout 로그를 보관합니다(시작 문제를 해결하도록 로깅을 사용하려는 경우).</span><span class="sxs-lookup"><span data-stu-id="34e4c-175">Within the folder you created, create a *logs* folder to hold stdout logs (if you plan to enable logging to troubleshoot start-up issues).</span></span> <span data-ttu-id="34e4c-176">페이로드에 *logs* 폴더가 있는 응용 프로그램을 배포하려는 경우 이 단계를 건너뛸 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-176">If you plan to deploy your application with a *logs* folder in the payload, you may skip this step.</span></span> <span data-ttu-id="34e4c-177">[폴더를 자동으로 만드는 미해결 문제](https://github.com/aspnet/AspNetCoreModule/issues/30)가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-177">There's an [open issue to create the folder automatically](https://github.com/aspnet/AspNetCoreModule/issues/30).</span></span> <span data-ttu-id="34e4c-178">MSBuild에서 *log* 폴더를 만들려면 다음 `Target`을 프로젝트 파일에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-178">If you would like MSBuild to create the *log* folder for you, add the following `Target` to your project file:</span></span>

   ```xml
   <Target Name="CreateLogsFolder" AfterTargets="AfterPublish">
     <MakeDir Directories="$(PublishDir)logs" Condition="!Exists('$(PublishDir)logs')" />
     <MakeDir Directories="$(PublishUrl)logs" Condition="!Exists('$(PublishUrl)logs')" />
   </Target>
   ```

3. <span data-ttu-id="34e4c-179">**IIS 관리자**에서 새 웹 사이트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-179">In **IIS Manager**, create a new website.</span></span> <span data-ttu-id="34e4c-180">**사이트 이름**을 입력하고 **실제 경로**를 만든 앱의 배포 폴더로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-180">Provide a **Site name** and set the **Physical path** to the app's deployment folder that you created.</span></span> <span data-ttu-id="34e4c-181">**바인딩** 구성을 제공하고 웹 사이트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-181">Provide the **Binding** configuration and create the website.</span></span>

4. <span data-ttu-id="34e4c-182">응용 프로그램 풀을 **관리 코드 없음**으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-182">Set the application pool to **No Managed Code**.</span></span> <span data-ttu-id="34e4c-183">ASP.NET Core는 별도의 프로세스에서 실행되며 런타임을 관리합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-183">ASP.NET Core runs in a separate process and manages the runtime.</span></span>

5. <span data-ttu-id="34e4c-184">**웹 사이트 추가** 창을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-184">Open the **Add Website** window.</span></span>

   ![[사이트] 상황에 맞는 메뉴에서 [웹 사이트 추가]를 클릭합니다.](iis/_static/add-website-context-menu-ws2016.png)

6. <span data-ttu-id="34e4c-186">웹 사이트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-186">Configure the website.</span></span>

   ![[웹 사이트 추가] 단계에서 사이트 이름, 실제 경로 및 호스트 이름을 제공합니다.](iis/_static/add-website-ws2016.png)

7. <span data-ttu-id="34e4c-188">**응용 프로그램 풀** 패널에서 웹 사이트의 앱 풀을 마우스 오른쪽 단추로 클릭하고 팝업 메뉴에서 **기본 설정...**을 선택하여 **응용 프로그램 풀 편집** 창을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-188">In the **Application Pools** panel, open the **Edit Application Pool** window by right-clicking on the website's app pool and selecting **Basic Settings...** from the popup menu.</span></span>

   ![[응용 프로그램 풀]의 상황에 맞는 메뉴에서 [기본 설정]을 선택합니다.](iis/_static/apppools-basic-settings-ws2016.png)

8. <span data-ttu-id="34e4c-190">**.NET CLR 버전**을 **관리 코드 없음**으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-190">Set the **.NET CLR version** to **No Managed Code**.</span></span>

   ![관리 코드 없음으로 설정된 .NET CLR 버전](iis/_static/edit-apppool-ws2016.png)
     
    <span data-ttu-id="34e4c-192">참고: **.NET CLR 버전**을 **관리 코드 없음**으로 설정하는 것은 선택 사항입니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-192">Note: Setting the **.NET CLR version** to **No Managed Code** is optional.</span></span> <span data-ttu-id="34e4c-193">ASP.NET Core에서는 데스크톱 CLR을 로드할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-193">ASP.NET Core doesn't rely on loading the desktop CLR.</span></span>

9. <span data-ttu-id="34e4c-194">프로세스 모델 ID에 적절한 권한이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-194">Confirm the process model identity has the proper permissions.</span></span>

   <span data-ttu-id="34e4c-195">앱 풀의 기본 ID(**프로세스 모델** > **ID**)를 **ApplicationPoolIdentity**에서 다른 ID로 변경하는 경우, 새 ID에 앱 폴더, 데이터베이스 및 기타 필요한 리소스에 액세스하는 데 필요한 권한이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-195">If you change the default identity of the app pool (**Process Model** > **Identity**) from **ApplicationPoolIdentity** to another identity, verify that the new identity has the required permissions to access the app's folder, database, and other required resources.</span></span>
   
## <a name="deploy-the-application"></a><span data-ttu-id="34e4c-196">응용 프로그램 배포</span><span class="sxs-lookup"><span data-stu-id="34e4c-196">Deploy the application</span></span>
<span data-ttu-id="34e4c-197">대상 IIS 시스템에서 만든 폴더에 응용 프로그램을 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-197">Deploy the application to the folder you created on the target IIS system.</span></span> <span data-ttu-id="34e4c-198">[웹 배포](/iis/publish/using-web-deploy/introduction-to-web-deploy)는 배포에 권장되는 메커니즘입니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-198">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) is the recommended mechanism for deployment.</span></span> <span data-ttu-id="34e4c-199">웹 배포에 대한 대안은 아래와 같습니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-199">Alternatives to Web Deploy are listed below.</span></span>

<span data-ttu-id="34e4c-200">게시된 배포용 앱이 실행되고 있지 않은지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-200">Confirm that the published app for deployment isn't running.</span></span> <span data-ttu-id="34e4c-201">앱이 실행 중이면 *publish* 폴더의 파일이 잠겨 있습니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-201">Files in the *publish* folder are locked when the app is running.</span></span> <span data-ttu-id="34e4c-202">잠긴 파일을 복사할 수 없으므로 배포를 수행할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-202">Deployment can't occur because locked files can't be copied.</span></span>

### <a name="web-deploy-with-visual-studio"></a><span data-ttu-id="34e4c-203">Visual Studio를 사용한 웹 배포</span><span class="sxs-lookup"><span data-stu-id="34e4c-203">Web Deploy with Visual Studio</span></span>
<span data-ttu-id="34e4c-204">웹 배포에서 사용할 게시 프로필을 만드는 방법은 [Visual Studio 및 MSBuild용 게시 프로필을 만들어 ASP.NET Core 앱 배포](xref:publishing/web-publishing-vs#publish-profiles) 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="34e4c-204">See [Create publish profiles for Visual Studio and MSBuild, to deploy ASP.NET Core apps](xref:publishing/web-publishing-vs#publish-profiles) topic to learn how to create a publish profile for use with Web Deploy.</span></span> <span data-ttu-id="34e4c-205">호스팅 공급자에서 게시 프로필을 제공하거나 이 프로필을 만들 수 있도록 지원하는 경우 Visual Studio **게시** 대화 상자를 사용하여 해당 프로필을 다운로드하고 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-205">If your hosting provider supplies a Publish Profile or support for creating one, download their profile and import it using the Visual Studio **Publish** dialog.</span></span>

![게시 대화 상자 페이지](iis/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a><span data-ttu-id="34e4c-207">Visual Studio 외부에서 웹 배포</span><span class="sxs-lookup"><span data-stu-id="34e4c-207">Web Deploy outside of Visual Studio</span></span>
<span data-ttu-id="34e4c-208">명령줄을 통해 Visual Studio 외부에서 [웹 배포](/iis/publish/using-web-deploy/introduction-to-web-deploy)를 사용할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-208">You can also use [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) outside of Visual Studio from the command line.</span></span> <span data-ttu-id="34e4c-209">자세한 내용은 [웹 배포 도구](/iis/publish/using-web-deploy/use-the-web-deployment-tool)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="34e4c-209">For more information, see [Web Deployment Tool](/iis/publish/using-web-deploy/use-the-web-deployment-tool).</span></span>

### <a name="alternatives-to-web-deploy"></a><span data-ttu-id="34e4c-210">웹 배포에 대한 대안</span><span class="sxs-lookup"><span data-stu-id="34e4c-210">Alternatives to Web Deploy</span></span>
<span data-ttu-id="34e4c-211">웹 배포를 사용하지 않거나 Visual Studio를 사용하지 않으려면 Xcopy, Robocopy 또는 PowerShell과 같이 여러 가지 방법 중 하나를 사용하여 응용 프로그램을 호스팅 시스템으로 이동할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-211">If you don't wish to use Web Deploy or are not using Visual Studio, you may use any of several methods to move the application to the hosting system, such as Xcopy, Robocopy, or PowerShell.</span></span> <span data-ttu-id="34e4c-212">Visual Studio 사용자는 [게시 샘플(영문)](https://github.com/aspnet/vsweb-publish/blob/master/samples/samples.md)을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-212">Visual Studio users may use the [Publish Samples](https://github.com/aspnet/vsweb-publish/blob/master/samples/samples.md).</span></span>

## <a name="browse-the-website"></a><span data-ttu-id="34e4c-213">웹 사이트 찾아보기</span><span class="sxs-lookup"><span data-stu-id="34e4c-213">Browse the website</span></span>
![IIS 시작 페이지가 로드된 Microsoft Edge 브라우저](iis/_static/browsewebsite.png)
   
> [!WARNING]
> <span data-ttu-id="34e4c-215">.NET Core 앱은 IIS와 Kestrel 서버 간의 역방향 프록시를 통해 호스팅됩니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-215">.NET Core apps are hosted via a reverse-proxy between IIS and the Kestrel server.</span></span> <span data-ttu-id="34e4c-216">역방향 프록시를 만들려면 *web.config* 파일이 IIS에 제공되는 웹 사이트 실제 경로인 배포된 응용 프로그램의 콘텐츠 루트 경로(일반적으로 응용 프로그램 기본 경로)에 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-216">In order to create the reverse-proxy, the *web.config* file must be present at the content root path (typically the app base path) of the deployed application, which is the website physical path provided to IIS.</span></span> <span data-ttu-id="34e4c-217">중요한 파일은 *my_application.runtimeconfig.json*, *my_application.xml*(XML 문서 주석) 및 *my_application.deps.json*과 같은 하위 폴더를 포함한 앱의 실제 경로에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-217">Sensitive files exist on the app's physical path, including subfolders, such as *my_application.runtimeconfig.json*, *my_application.xml* (XML Documentation comments), and *my_application.deps.json*.</span></span> <span data-ttu-id="34e4c-218">*web.config* 파일은 Kestrel에 대한 역방향 프록시를 만들어 IIS에서 이러한 파일 및 기타 중요한 파일을 제공하지 못하도록 하는 데 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-218">The *web.config* file is required to create the reverse proxy to Kestrel, which prevents IIS from serving these and other sensitive files.</span></span> <span data-ttu-id="34e4c-219">**따라서 *web.config* 파일을 실수로 이름을 변경하거나 배포에서 제거하지 않아야 합니다.**</span><span class="sxs-lookup"><span data-stu-id="34e4c-219">**Therefore, it's important that the *web.config* file isn't accidently renamed or removed from the deployment.**</span></span>

## <a name="data-protection"></a><span data-ttu-id="34e4c-220">데이터 보호</span><span class="sxs-lookup"><span data-stu-id="34e4c-220">Data protection</span></span>

<span data-ttu-id="34e4c-221">ASP.NET Core 응용 프로그램은 다음 조건에서 키 링을 메모리에 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-221">An ASP.NET Core application stores the keyring in memory under the following conditions:</span></span>

* <span data-ttu-id="34e4c-222">웹 사이트가 IIS 뒤에 호스팅됩니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-222">A website is hosted behind IIS.</span></span>
* <span data-ttu-id="34e4c-223">데이터 보호 스택이 영구 저장소에 키 링을 저장하도록 구성되지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-223">The Data Protection stack hasn't been configured to store the keyring in a persistent store.</span></span>

<span data-ttu-id="34e4c-224">키 링이 메모리에 저장되는 경우 앱을 다시 시작할 때 다음과 같이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-224">If the keyring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="34e4c-225">모든 폼 인증 토큰이 유효하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-225">All forms authentication tokens are invalidated.</span></span> 
* <span data-ttu-id="34e4c-226">사용자가 다음 요청에서 다시 로그인해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-226">Users are required to login again on their next request.</span></span> 
* <span data-ttu-id="34e4c-227">키 링으로 보호한 데이터가 더 이상 보호되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-227">Any data you protected with the keyring are no longer protected.</span></span>

> [!WARNING]
> <span data-ttu-id="34e4c-228">데이터 보호는 인증에 사용되는 것을 포함하여 여러 ASP.NET 미들웨어에서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-228">Data Protection is used by several ASP.NET middlewares, including those used in authentication.</span></span> <span data-ttu-id="34e4c-229">사용자 고유 코드에서 데이터 보호 API를 호출하지 않아도 배포 스크립트 또는 사용자 코드를 사용하여 데이터 보호를 구성해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-229">Even if you don't call Data Protection APIs from your own code, you should configure Data Protection with a deployment script or in your code.</span></span> <span data-ttu-id="34e4c-230">데이터 보호를 구성하지 않으면 기본적으로 키가 메모리에 보관되어 앱을 다시 시작할 때 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-230">If you don't configure data protection, by default the keys are held in memory and discarded when your app restarts.</span></span> <span data-ttu-id="34e4c-231">다시 시작하면 쿠키 인증 미들웨어로 작성된 모든 쿠키가 무효화되고 사용자가 다시 로그인해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-231">Restarting invalidates cookies written by the cookie authentication middleware and users must login again.</span></span>

<span data-ttu-id="34e4c-232">IIS에서 데이터 보호를 구성하려면 다음 방법 중 하나를 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-232">To configure Data Protection under IIS, you must use one of the following approaches:</span></span>

* <span data-ttu-id="34e4c-233">[PowerShell 스크립트](https://github.com/aspnet/DataProtection/blob/dev/Provision-AutoGenKeys.ps1)를 실행하여 적절한 레지스트리 항목을 만듭니다(예: `.\Provision-AutoGenKeys.ps1 DefaultAppPool`).</span><span class="sxs-lookup"><span data-stu-id="34e4c-233">Run a [powershell script](https://github.com/aspnet/DataProtection/blob/dev/Provision-AutoGenKeys.ps1) to create suitable registry entries (For example, `.\Provision-AutoGenKeys.ps1 DefaultAppPool`).</span></span> <span data-ttu-id="34e4c-234">이렇게 하면 키가 레지스트리에 저장되고, 컴퓨터 수준 키가 있는 DPAPI를 사용하여 보호됩니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-234">This stores keys in the registry, protected using DPAPI with a machine-wide key.</span></span>
* <span data-ttu-id="34e4c-235">사용자 프로필을 로드하도록 IIS 응용 프로그램 풀을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-235">Configure the IIS Application Pool to load the user profile.</span></span> <span data-ttu-id="34e4c-236">이 설정은 응용 프로그램 풀에 대한 **고급 설정** 아래의 **프로세스 모델** 섹션에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-236">This setting is in the **Process Model** section under the **Advanced Settings** for the application pool.</span></span> <span data-ttu-id="34e4c-237">**사용자 프로필**을 `True`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-237">Set **Load User Profile** to `True`.</span></span> <span data-ttu-id="34e4c-238">이렇게 하면 사용자 프로필 디렉터리 아래에 키가 저장되고, 앱 풀에 사용되는 사용자 계정과 관련된 키가 있는 DPAPI를 사용하여 보호됩니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-238">This stores keys under the user profile directory and protects them using DPAPI with a key specific to the user account used for the app pool.</span></span>
* <span data-ttu-id="34e4c-239">[파일 시스템을 키 링 저장소로 사용](xref:security/data-protection/configuration/overview)하도록 앱 코드를 조정합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-239">Adjust your app code to [use the file system as a key ring store](xref:security/data-protection/configuration/overview).</span></span> <span data-ttu-id="34e4c-240">X509 인증서를 사용하여 키 링을 보호하고 신뢰할 수 있는 인증서인지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-240">Use an X509 certificate to protect the keyring and ensure it's a trusted certificate.</span></span> <span data-ttu-id="34e4c-241">자체 서명된 인증서인 경우 신뢰할 수 있는 루트 저장소에 배치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-241">If it's a self signed certificate, you must place it in the Trusted Root store.</span></span>

<span data-ttu-id="34e4c-242">웹 팜에서 IIS를 사용하는 경우 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-242">When using IIS in a web farm:</span></span>

* <span data-ttu-id="34e4c-243">모든 컴퓨터에서 액세스할 수 있는 파일 공유를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-243">Use a file share that all machines can access.</span></span>
* <span data-ttu-id="34e4c-244">각 시스템에 X509 인증서를 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-244">Deploy an X509 certificate to each machine.</span></span> <span data-ttu-id="34e4c-245">[코드에 데이터 보호](https://docs.microsoft.com/aspnet/core/security/data-protection/configuration/overview)를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-245">Configure [data protection in code](https://docs.microsoft.com/aspnet/core/security/data-protection/configuration/overview).</span></span>

### <a name="1-create-a-data-protection-registry-hive"></a><span data-ttu-id="34e4c-246">1. 데이터 보호 레지스트리 하이브 만들기</span><span class="sxs-lookup"><span data-stu-id="34e4c-246">1. Create a Data Protection Registry Hive</span></span>

<span data-ttu-id="34e4c-247">ASP.NET 응용 프로그램에서 사용하는 데이터 보호 키는 응용 프로그램 외부의 레지스트리 하이브에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-247">Data Protection keys used by ASP.NET applications are stored in registry hives external to the applications.</span></span> <span data-ttu-id="34e4c-248">지정된 응용 프로그램의 키를 유지하려면 해당 앱의 응용 프로그램 풀에 대한 레지스트리 하이브를 만들어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-248">To persist the keys for a given application, you must create a registry hive for the app's application pool.</span></span>

<span data-ttu-id="34e4c-249">독립 실행형 IIS 설치의 경우 ASP.NET Core 앱에서 사용되는 각 앱 풀에 대해 [Data Protection Provision-AutoGenKeys.ps1 PowerShell 스크립트](https://github.com/aspnet/DataProtection/blob/dev/Provision-AutoGenKeys.ps1)를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-249">For standalone IIS installations, you may use the [Data Protection Provision-AutoGenKeys.ps1 PowerShell script](https://github.com/aspnet/DataProtection/blob/dev/Provision-AutoGenKeys.ps1) for each app pool used with an ASP.NET Core app.</span></span> <span data-ttu-id="34e4c-250">이 스크립트는 작업자 프로세스 계정에만 ACL로 지정된 특수 레지스트리 키를 HKLM 레지스트리에 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-250">This script creates a special registry key in the HKLM registry that is ACLed only to the worker process account.</span></span> <span data-ttu-id="34e4c-251">미사용 키는 DPAPI를 사용하여 암호화됩니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-251">Keys are encrypted at rest using DPAPI.</span></span>

<span data-ttu-id="34e4c-252">웹 팜 시나리오에서는 UNC 경로를 사용하여 데이터 보호 키 링을 저장하도록 앱을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-252">In web farm scenarios, an app can be configured to use a UNC path to store its data protection keyring.</span></span> <span data-ttu-id="34e4c-253">기본적으로 데이터 보호 키는 암호화되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-253">By default, the data protection keys are not encrypted.</span></span> <span data-ttu-id="34e4c-254">이러한 공유에 대한 파일 권한은 앱이 실행되는 Windows 계정으로 제한되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-254">You should ensure that the file permissions for such a share are limited to the Windows account the app runs as.</span></span> <span data-ttu-id="34e4c-255">또한 X509 인증서를 사용하여 미사용 키를 보호하도록 선택할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-255">In addition, you may choose to protect keys at rest using an X509 certificate.</span></span> <span data-ttu-id="34e4c-256">사용자가 인증서를 업로드하여 사용자의 신뢰할 수 있는 인증서 저장소에 배치하고 사용자의 앱이 실행될 모든 컴퓨터에서 인증서를 사용할 수 있도록 하는 메커니즘을 고려해야 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-256">You may wish to consider a mechanism to allow users to upload certificates: Place certificates into the user's trusted certificate store and ensure they're available on all machines where the user's app runs.</span></span> <span data-ttu-id="34e4c-257">자세한 내용은 [데이터 보호 구성](xref:security/data-protection/configuration/overview)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="34e4c-257">See [Configuring Data Protection](xref:security/data-protection/configuration/overview) for details.</span></span>

### <a name="2-configure-the-iis-application-pool-to-load-the-user-profile"></a><span data-ttu-id="34e4c-258">2. 사용자 프로필을 로드하도록 IIS 응용 프로그램 풀 구성</span><span class="sxs-lookup"><span data-stu-id="34e4c-258">2. Configure the IIS Application Pool to load the user profile</span></span>

<span data-ttu-id="34e4c-259">이 설정은 앱 풀에 대한 **고급 설정** 아래의 **프로세스 모델** 섹션에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-259">This setting is in the **Process Model** section under the **Advanced Settings** for the app pool.</span></span> <span data-ttu-id="34e4c-260">사용자 프로필 로드를 `True`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-260">Set Load User Profile to `True`.</span></span> <span data-ttu-id="34e4c-261">이렇게 하면 사용자 프로필 디렉터리 아래에 키가 저장되고, 앱 풀에 사용되는 사용자 계정과 관련된 키가 있는 DPAPI를 사용하여 보호됩니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-261">This stores keys under the user profile directory and protects them using DPAPI with a key specific to the user account used for the app pool.</span></span>

### <a name="3-machine-wide-policy-for-data-protection"></a><span data-ttu-id="34e4c-262">3. 데이터 보호에 대한 컴퓨터 수준 정책</span><span class="sxs-lookup"><span data-stu-id="34e4c-262">3. Machine-wide policy for data protection</span></span>

<span data-ttu-id="34e4c-263">데이터 보호 시스템은 데이터 보호 API를 사용하는 모든 앱에 대한 기본 [컴퓨터 수준 정책](xref:security/data-protection/configuration/machine-wide-policy) 설정을 제한적으로 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-263">The data protection system has limited support for setting a default [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy) for all apps that consume the Data Protection APIs.</span></span> <span data-ttu-id="34e4c-264">자세한 내용은 [데이터 보호](xref:security/data-protection/index) 설명서를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="34e4c-264">See the [data protection](xref:security/data-protection/index) documentation for more details.</span></span>

## <a name="configuration-of-sub-applications"></a><span data-ttu-id="34e4c-265">하위 응용 프로그램 구성</span><span class="sxs-lookup"><span data-stu-id="34e4c-265">Configuration of sub-applications</span></span>

<span data-ttu-id="34e4c-266">루트 응용 프로그램 아래에 추가된 하위 응용 프로그램은 ASP.NET Core 모듈을 처리기로 포함하지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-266">Sub applications added under the root application shouldn't include the ASP.NET Core Module as a handler.</span></span> <span data-ttu-id="34e4c-267">하위 응용 프로그램의 *web.config* 파일에 모듈을 처리기로 추가하면 하위 앱을 찾아보려고 할 때 잘못된 구성 파일을 참조하는 500.19(내부 서버 오류)가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-267">If you add the module as a handler in a sub-application's *web.config* file, you receive a 500.19 (Internal Server Error) referencing the faulty config file when you attempt to browse the sub-app.</span></span> <span data-ttu-id="34e4c-268">다음 예제에서는 ASP.NET Core 하위 앱에 대해 게시된 *web.config* 파일의 내용을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-268">The following example shows the contents of a published *web.config* file for an ASP.NET Core sub-app:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <aspNetCore processPath="dotnet" 
      arguments=".\MyApp.dll" 
      stdoutLogEnabled="false" 
      stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

<span data-ttu-id="34e4c-269">ASP.NET Core 앱 아래에 비ASP .NET Core 하위 앱을 호스팅하려는 경우 하위 앱의 *web.config* 파일에서 상속된 처리기를 명시적으로 제거해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-269">If you intend to host a non-ASP.NET Core sub-app underneath an ASP.NET Core app, you must explicitly remove the inherited handler in the sub-app *web.config* file:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <remove name="aspNetCore"/>
    </handlers>
    <aspNetCore processPath="dotnet" 
      arguments=".\MyApp.dll" 
      stdoutLogEnabled="false" 
      stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

<span data-ttu-id="34e4c-270">*web.config* 파일을 사용하여 ASP.NET Core 모듈을 구성하는 방법에 대한 자세한 내용은 [ASP.NET Core 모듈 소개](xref:fundamentals/servers/aspnet-core-module) 항목 및 [ASP.NET Core 모듈 구성 참조](xref:hosting/aspnet-core-module)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="34e4c-270">For more information on configuring the ASP.NET Core Module with the *web.config* file, see the [Introduction to ASP.NET Core Module](xref:fundamentals/servers/aspnet-core-module) topic and the [ASP.NET Core Module configuration reference](xref:hosting/aspnet-core-module).</span></span>

## <a name="configuration-of-iis-with-webconfig"></a><span data-ttu-id="34e4c-271">web.config를 사용하여 IIS 구성</span><span class="sxs-lookup"><span data-stu-id="34e4c-271">Configuration of IIS with web.config</span></span>

<span data-ttu-id="34e4c-272">IIS 구성은 여전히 역방향 프록시 구성에 적용되는 IIS 기능에 대한 *web.config*에 포함된 `<system.webServer>` 섹션의 영향을 받습니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-272">IIS configuration is still influenced by the `<system.webServer>` section of *web.config* for those IIS features that apply to a reverse proxy configuration.</span></span> <span data-ttu-id="34e4c-273">예를 들어 시스템 수준에서 동적 압축을 사용하도록 IIS를 구성했을 수 있지만, 응용 프로그램의 *web.config* 파일에서 `<urlCompression>` 요소를 사용하여 응용 프로그램의 해당 설정을 사용하지 않도록 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-273">For example, you may have IIS configured at the system level to use dynamic compression, but you could disable that setting for an app with the `<urlCompression>` element in the app's *web.config* file.</span></span> <span data-ttu-id="34e4c-274">자세한 내용은 [`<system.webServer>`에 대한 구성 참조](https://docs.microsoft.com/iis/configuration/system.webServer/), [ASP.NET Core 모듈 구성 참조](xref:hosting/aspnet-core-module) 및 [ASP.NET Core와 함께 IIS 모듈 사용](xref:hosting/iis-modules)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="34e4c-274">For more information, see the [configuration reference for `<system.webServer>`](https://docs.microsoft.com/iis/configuration/system.webServer/), [ASP.NET Core Module Configuration Reference](xref:hosting/aspnet-core-module) and [Using IIS Modules with ASP.NET Core](xref:hosting/iis-modules).</span></span> <span data-ttu-id="34e4c-275">격리된 응용 프로그램 풀(IIS 10.0 이상에서 지원됨)에서 실행되는 개별 응용 프로그램에 대한 환경 변수를 설정해야 하는 경우 [\<environmentVariables> 환경 변수](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) 항목의 *AppCmd.exe 명령* 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="34e4c-275">If you need to set environment variables for individual apps running in isolated Application Pools (supported on IIS 10.0+), see the *AppCmd.exe command* section of the [Environment Variables \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic in the IIS reference documentation.</span></span>

## <a name="configuration-sections-of-webconfig"></a><span data-ttu-id="34e4c-276">web.config 구성 섹션</span><span class="sxs-lookup"><span data-stu-id="34e4c-276">Configuration sections of web.config</span></span>

<span data-ttu-id="34e4c-277">*web.config*의 `<system.web>`, `<appSettings>`, `<connectionStrings>` 및 `<location>` 요소로 구성된 .NET Framework 응용 프로그램과 달리 ASP.NET Core 응용 프로그램은 다른 구성 공급자를 사용하여 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-277">Unlike .NET Framework applications that are configured with the `<system.web>`, `<appSettings>`, `<connectionStrings>`, and `<location>` elements in *web.config*, ASP.NET Core apps are configured using other configuration providers.</span></span> <span data-ttu-id="34e4c-278">자세한 내용은 [구성](xref:fundamentals/configuration)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="34e4c-278">For more information, see [Configuration](xref:fundamentals/configuration).</span></span>

## <a name="application-pools"></a><span data-ttu-id="34e4c-279">응용 프로그램 풀</span><span class="sxs-lookup"><span data-stu-id="34e4c-279">Application Pools</span></span>

<span data-ttu-id="34e4c-280">단일 시스템에서 여러 웹 사이트를 호스팅하는 경우 각 앱을 자체의 응용 프로그램 풀에서 실행하여 앱을 서로 격리해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-280">When hosting multiple websites on a single system, you should isolate the apps from each other by running each app in its own application pool.</span></span> <span data-ttu-id="34e4c-281">IIS **웹 사이트 추가** 대화 상자에서는 이 동작이 기본적으로 설정되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-281">The IIS **Add Website** dialog defaults to this behavior.</span></span> <span data-ttu-id="34e4c-282">**사이트 이름**을 제공하면 해당 텍스트가 **응용 프로그램 풀** 텍스트 상자로 자동으로 전송됩니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-282">When you provide a **Site name**, the text is automatically transferred to the **Application pool** textbox.</span></span> <span data-ttu-id="34e4c-283">웹 사이트를 추가할 때 해당 사이트 이름을 사용하여 새 응용 프로그램 풀이 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-283">A new application pool is created using the site name when you add the website.</span></span>

## <a name="application-pool-identity"></a><span data-ttu-id="34e4c-284">응용 프로그램 풀 ID</span><span class="sxs-lookup"><span data-stu-id="34e4c-284">Application Pool Identity</span></span>

<span data-ttu-id="34e4c-285">응용 프로그램 풀 ID 계정을 사용하면 도메인 또는 로컬 계정을 만들고 관리할 필요 없이 고유한 계정으로 응용 프로그램을 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-285">An application pool identity account allows you to run an application under a unique account without having to create and manage domains or local accounts.</span></span> <span data-ttu-id="34e4c-286">IIS 8.0 이상에서 IIS 관리 작업자 프로세스(WAS)는 새 응용 프로그램 풀의 이름으로 가상 계정을 만들고, 기본적으로 이 계정으로 앱 풀의 작업자 프로세스를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-286">On IIS 8.0+, the IIS Admin Worker Process (WAS) creates a virtual account with the name of the new application pool and runs the app pool's worker processes under this account by default.</span></span> <span data-ttu-id="34e4c-287">IIS 관리 콘솔의 앱 풀에 대한 **고급 설정**에서 아래 이미지와 같이 **ID**에서 **ApplicationPoolIdentity**를 사용하도록 설정되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-287">In the IIS Management Console under **Advanced Settings** for your app pool, ensure that the **Identity** is set to use **ApplicationPoolIdentity** as shown in the image below.</span></span>

![응용 프로그램 풀 고급 설정 대화 상자](iis/_static/apppool-identity.png)

<span data-ttu-id="34e4c-289">IIS 관리 프로세스는 Windows 보안 시스템에 앱 풀 이름이 포함된 보안 식별자를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-289">The IIS management process creates a secure identifier with the name of the app pool in the Windows Security System.</span></span> <span data-ttu-id="34e4c-290">리소스는 이 ID를 사용하여 보호할 수 있습니다. 그러나 이 ID는 실제 사용자 계정이 아니며 Windows 사용자 관리 콘솔에 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-290">Resources can be secured by using this identity; however, this identity isn't a real user account and won't show up in the Windows User Management Console.</span></span>

<span data-ttu-id="34e4c-291">앱에 대한 높은 액세스 권한을 IIS 작업자 프로세스에 부여해야 하는 경우 응용 프로그램이 포함된 디렉터리에 대한 ACL(액세스 제어 목록)을 수정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-291">If you need to grant the IIS worker process elevated access to your app, you must modify the Access Control List (ACL) for the directory containing your application.</span></span>

1. <span data-ttu-id="34e4c-292">[Windows 탐색기]를 열고 해당 하위 디렉터리로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-292">Open Windows Explorer and navigate to the directory.</span></span>

2. <span data-ttu-id="34e4c-293">디렉터리를 마우스 오른쪽 단추로 클릭하고 **속성**을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-293">Right click on the directory and click **Properties**.</span></span>

3. <span data-ttu-id="34e4c-294">**보안** 탭 아래에서 **편집** 단추를 클릭한 다음 **추가** 단추를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-294">Under the **Security** tab, click the **Edit** button and then the **Add** button.</span></span>

4. <span data-ttu-id="34e4c-295">**위치** 단추를 클릭하고 시스템이 선택되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-295">Click the **Locations** button and make sure you select your system.</span></span>

5. <span data-ttu-id="34e4c-296">**선택할 개체 이름 입력** 텍스트 상자에서 **IIS AppPool\DefaultAppPool**을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-296">Enter **IIS AppPool\DefaultAppPool** in **Enter the object names to select** textbox.</span></span>

  ![응용 프로그램 폴더에 대한 사용자 또는 그룹 선택 대화 상자](iis/_static/select-users-or-groups-1.png)

6. <span data-ttu-id="34e4c-298">**이름 확인** 단추를 클릭한 다음 **확인**을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-298">Click the **Check Names** button and then click **OK**.</span></span>

  ![응용 프로그램 폴더에 대한 사용자 또는 그룹 선택 대화 상자](iis/_static/select-users-or-groups-2.png)

<span data-ttu-id="34e4c-300">**ICACLS** 도구를 사용하여 명령 프롬프트를 통해 이 작업을 수행할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-300">You can also do this via a command prompt using **ICACLS** tool:</span></span>

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

## <a name="troubleshooting-tips"></a><span data-ttu-id="34e4c-301">문제 해결 팁</span><span class="sxs-lookup"><span data-stu-id="34e4c-301">Troubleshooting tips</span></span>

<span data-ttu-id="34e4c-302">IIS 배포로 문제를 진단하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-302">To diagnose problems with IIS deployments:</span></span>

* <span data-ttu-id="34e4c-303">브라우저 출력을 검토합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-303">Study browser output.</span></span>
* <span data-ttu-id="34e4c-304">**이벤트 뷰어**를 통해 시스템의 **응용 프로그램** 로그를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-304">Examine the system's **Application** log through **Event Viewer**.</span></span>
* <span data-ttu-id="34e4c-305">`stdout` 로깅을 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-305">Enable `stdout` logging.</span></span> <span data-ttu-id="34e4c-306">**ASP.NET Core 모듈** 로그는 *web.config*에 있는 `<aspNetCore>` 요소의 *stdoutLogFile* 특성에 제공된 경로에 있습니다. 특성 값에 제공된 경로의 모든 폴더가 배포에 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-306">The **ASP.NET Core Module** log is found on the path provided in the *stdoutLogFile* attribute of the `<aspNetCore>` element in *web.config*. Any folders on the path provided in the attribute value must exist in the deployment.</span></span> <span data-ttu-id="34e4c-307">*stdoutLogEnabled="true"*도 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-307">You must also set *stdoutLogEnabled="true"*.</span></span> <span data-ttu-id="34e4c-308">`Microsoft.NET.Sdk.Web` SDK를 사용하여 *web.config* 파일을 만드는 앱은 기본적으로 *stdoutLogEnabled* 설정을 *false*로 설정하므로 직접 *web.config* 파일을 제공하거나 `stdout` 로깅을 사용하도록 파일을 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-308">Apps that use the the `Microsoft.NET.Sdk.Web` SDK to create the *web.config* file default the *stdoutLogEnabled* setting to *false*, so you must manually provide the *web.config* file or modify the file in order to enable `stdout` logging.</span></span>

<span data-ttu-id="34e4c-309">*startupTimeLimit*(기본값: 120초) 및 *startupRetryCount*(기본값: 2) 모듈이 통과할 때까지 일반적인 몇 가지 오류는 브라우저, 응용 프로그램 로그 및 ASP.NET Core 모듈 로그에 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-309">Several of the common errors don't appear in the browser, Application Log, and ASP.NET Core Module Log until the module *startupTimeLimit* (default: 120 seconds) and *startupRetryCount* (default: 2) have passed.</span></span> <span data-ttu-id="34e4c-310">따라서 모듈에서 앱의 프로세스를 시작하지 못했다고 추론될 때까지 6분 정도 기다립니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-310">Therefore, wait a full six minutes before deducing that the module has failed to start a process for the app.</span></span>

<span data-ttu-id="34e4c-311">앱이 제대로 작동하는지 확인하는 빠른 방법은 Kestrel에서 직접 앱을 실행하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-311">One quick way to determine if the app is working properly is to run the app directly on Kestrel.</span></span> <span data-ttu-id="34e4c-312">앱이 FDD(프레임워크 종속 배포)로 게시된 경우 앱의 IIS 실제 경로인 배포 폴더에 있는 `dotnet my_application.dll`을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-312">If the app was published as a framework-dependent deployment (FDD), execute `dotnet my_application.dll` in the deployment folder, which is the IIS physical path to the app.</span></span> <span data-ttu-id="34e4c-313">앱이 SCD(자체 포함 배포)로 게시된 경우 명령 프롬프트에서 직접 앱의 실행 파일, 즉 배포 폴더에 있는 `my_application.exe`를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-313">If the app was published as a self-contained deployment (SCD), run the app's executable directly from a command prompt, `my_application.exe`, in the deployment folder.</span></span> <span data-ttu-id="34e4c-314">Kestrel이 5000 기본 포트에서 수신 대기중인 경우 `http://localhost:5000/`에서 앱을 찾아볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-314">If Kestrel is listening on default port 5000, you should be able to browse the app at `http://localhost:5000/`.</span></span> <span data-ttu-id="34e4c-315">앱이 Kestrel 끝점 주소에서 정상적으로 응답하는 경우, 문제는 IIS-ASP.NET Core 모듈-Kestrel 구성과 관련이 있으며 앱 자체 내에서 관련되었을 가능성은 낮습니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-315">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the IIS-ASP.NET Core Module-Kestrel configuration and less likely within the app itself.</span></span>

<span data-ttu-id="34e4c-316">Kestrel 서버에 대한 IIS 역방향 프록시가 제대로 작동하는지 확인하는 한 가지 방법은 [정적 파일 미들웨어](xref:fundamentals/static-files)를 사용하여 *wwwroot*에 있는 앱의 정적 파일에서 스타일시트, 스크립트 또는 이미지에 대한 간단한 정적 파일 요청을 수행하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-316">One way to determine if the IIS reverse proxy to the Kestrel server is working properly is to perform a simple static file request for a stylesheet, script, or image from the app's static files in *wwwroot* using [Static File middleware](xref:fundamentals/static-files).</span></span> <span data-ttu-id="34e4c-317">앱에서 정적 파일을 제공할 수 있지만 MVC보기와 다른 끝점에서 오류가 발생하는 경우, 문제는 IIS-ASP.NET Core 모듈-Kestrel 구성과 관련이 없으며 앱 자체 내에서 관련되었을 가능성이 높습니다(예: MVC 라우팅 또는 500 내부 서버 오류).</span><span class="sxs-lookup"><span data-stu-id="34e4c-317">If the app can serve static files but MVC Views and other endpoints are failing, the problem is less likely related to the IIS-ASP.NET Core Module-Kestrel configuration and more likely within the app itself (for example, MVC routing or 500 Internal Server Error).</span></span>

<span data-ttu-id="34e4c-318">Kestrel이 IIS 뒤에서 정상적으로 시작되지만 로컬에서 성공적으로 실행한 후에 시스템에서 응용 프로그램이 실행되지 않으면 임시로 *web.config*에 환경 변수를 추가하여 `ASPNETCORE_ENVIRONMENT`를 `Development`로 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-318">When Kestrel starts normally behind IIS but the app won't run on the system after successfully running locally, you can temporarily add an environment variable to *web.config* to set the `ASPNETCORE_ENVIRONMENT` to `Development`.</span></span> <span data-ttu-id="34e4c-319">앱 시작에서 환경을 재정의하지 않는다면 시스템에서 앱이 실행될 때 [개발자 예외 페이지](xref:fundamentals/error-handling)가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-319">As long as you don't override the environment in app startup, this allows the [developer exception page](xref:fundamentals/error-handling) to appear when the app is run on the system.</span></span> <span data-ttu-id="34e4c-320">이 방식으로 `ASPNETCORE_ENVIRONMENT`에 대한 환경 변수를 설정하는 것은 인터넷에 노출되지 않은 준비/테스트 시스템에만 적용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-320">Setting the environment variable for `ASPNETCORE_ENVIRONMENT` in this way is only recommended for staging/testing systems that aren't exposed to the Internet.</span></span> <span data-ttu-id="34e4c-321">완료되면 반드시 *web.config* 파일에서 해당 환경 변수를 제거해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-321">Be sure you remove the environment variable from the *web.config* file when finished.</span></span> <span data-ttu-id="34e4c-322">*web.config*를 통해 역방향 프록시에 대한 환경 변수를 설정하는 방법에 대한 자세한 내용은[aspNetCore의 environmentVariables 자식 요소](xref:hosting/aspnet-core-module#setting-environment-variables)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="34e4c-322">For information on setting environment variables via *web.config* for the reverse proxy, see [environmentVariables child element of aspNetCore](xref:hosting/aspnet-core-module#setting-environment-variables).</span></span>

<span data-ttu-id="34e4c-323">응용 프로그램 로깅을 사용하면 대부분의 경우에서 앱 또는 역방향 프록시 문제를 해결하는 데 도움이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-323">In most cases, enabling application logging assists in troubleshooting problems with the app or the reverse proxy.</span></span> <span data-ttu-id="34e4c-324">자세한 내용은 [로깅](xref:fundamentals/logging/index)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="34e4c-324">See [Logging](xref:fundamentals/logging/index) for more information.</span></span>

<span data-ttu-id="34e4c-325">마지막 문제 해결 팁은 개발 컴퓨터의 .NET Core SDK 또는 응용프로그램 내의 패키지 버전을 업그레이드한 후에 실행되지 않는 응용프로그램에 관한 것입니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-325">Our last troubleshooting tip pertains to apps that fail to run after upgrading either the .NET Core SDK on the development machine or package versions within the app.</span></span> <span data-ttu-id="34e4c-326">경우에 따라 중요한 업그레이드를 수행할 때 일관되지 않은 패키지로 인해 응용 프로그램이 중단될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-326">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="34e4c-327">프로젝트의 `bin` 및 `obj` 폴더를 삭제하고, `%UserProfile%\.nuget\packages\` 및 `%LocalAppData%\Nuget\v3-cache`에 있는 패키지 캐시를 지우고, 프로젝트를 복원하고, 해당 응용 프로그램을 다시 배포하기 전에 시스템의 이전 배포가 완전히 삭제되었는지 확인하여 이러한 문제를 대부분 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-327">You can fix most of these issues by deleting the `bin` and `obj` folders in the project, clearing package caches at `%UserProfile%\.nuget\packages\` and `%LocalAppData%\Nuget\v3-cache`, restoring the project, and confirming that your prior deployment on the system has been completely deleted prior to re-deploying the app.</span></span>

> [!TIP]
> <span data-ttu-id="34e4c-328">패키지 캐시를 지우는 편리한 방법은 [NuGet.org](https://www.nuget.org/)에서 *NuGet.exe* 도구를 구하여 시스템 PATH에 추가하고 명령 프롬프트에서 `nuget locals all -clear`를 실행하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-328">A convenient way to clear package caches is to obtain the *NuGet.exe* tool from [NuGet.org](https://www.nuget.org/), add it to your system PATH, and execute `nuget locals all -clear` from a command prompt.</span></span> <span data-ttu-id="34e4c-329">*NuGet.exe*를 가져오지 않고도 명령 프롬프트에서 `dotnet nuget locals all --clear` 명령을 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-329">You can also execute the `dotnet nuget locals all --clear` command from a command prompt without obtaining *NuGet.exe*.</span></span>

## <a name="common-errors"></a><span data-ttu-id="34e4c-330">일반적인 오류</span><span class="sxs-lookup"><span data-stu-id="34e4c-330">Common errors</span></span>

<span data-ttu-id="34e4c-331">다음은 모든 오류를 나열한 목록이 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-331">The following isn't a complete list of errors.</span></span> <span data-ttu-id="34e4c-332">여기에 나열되지 않은 오류가 발생하면 아래의 참고 섹션에 자세한 오류 메시지를 남겨 주세요.</span><span class="sxs-lookup"><span data-stu-id="34e4c-332">Should you encounter an error not listed here, please leave a detailed error message in the comments section below.</span></span>

### <a name="installer-unable-to-obtain-vc-redistributable"></a><span data-ttu-id="34e4c-333">설치 관리자에서 VC++ 재배포 가능 패키지를 가져올 수 없음</span><span class="sxs-lookup"><span data-stu-id="34e4c-333">Installer unable to obtain VC++ Redistributable</span></span>

* <span data-ttu-id="34e4c-334">**설치 관리자 예외:** 0x80072efd 또는 0x80072f76 - 지정되지 않은 오류</span><span class="sxs-lookup"><span data-stu-id="34e4c-334">**Installer Exception:** 0x80072efd or 0x80072f76 - Unspecified error</span></span>

* <span data-ttu-id="34e4c-335">**설치 관리자 로그 예외&#8224;:** 오류 0x80072efd 또는 0x80072f76: EXE 패키지를 실행하지 못했습니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-335">**Installer Log Exception&#8224;:** Error 0x80072efd or 0x80072f76: Failed to execute EXE package</span></span>

  <span data-ttu-id="34e4c-336">&#8224;로그는 C:\Users\\{USER}\AppData\Local\Temp\dd_DotNetCoreWinSvrHosting__{timestamp}.log에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-336">&#8224;The log is located at C:\Users\\{USER}\AppData\Local\Temp\dd_DotNetCoreWinSvrHosting__{timestamp}.log.</span></span>

<span data-ttu-id="34e4c-337">문제 해결:</span><span class="sxs-lookup"><span data-stu-id="34e4c-337">Troubleshooting:</span></span>

* <span data-ttu-id="34e4c-338">시스템에서 서버 호스팅 번들을 설치하지만 인터넷에 액세스할 수 없는 경우 이 예외는 설치 관리자에서 *Microsoft Visual C++ 2015 재배포 가능 패키지*를 얻을 수 없을 때 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-338">If the system doesn't have Internet access while installing the server hosting bundle, this exception occurs when the installer is prevented from obtaining the *Microsoft Visual C++ 2015 Redistributable*.</span></span> <span data-ttu-id="34e4c-339">[Microsoft 다운로드 센터](https://www.microsoft.com/download/details.aspx?id=53840)에서 설치 관리자를 얻을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-339">You may obtain an installer from the [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=53840).</span></span> <span data-ttu-id="34e4c-340">설치 관리자에 오류가 발생하면 FDD(프레임워크 종속 배포)를 호스팅하는 데 필요한 .NET Core 런타임을 받지 못할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-340">If the installer fails, you may not receive the .NET Core runtime required to host a framework-dependent deployment (FDD).</span></span> <span data-ttu-id="34e4c-341">FDD를 호스팅하려면 프로그램 &amp; 기능에서 런타임이 설치되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-341">If you plan to host an FDD, confirm that the runtime is installed in Programs &amp; Features.</span></span> <span data-ttu-id="34e4c-342">[.NET 다운로드](https://www.microsoft.com/net/download/core)에서 런타임 설치 관리자를 얻을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-342">You may obtain a runtime installer from [.NET Downloads](https://www.microsoft.com/net/download/core).</span></span> <span data-ttu-id="34e4c-343">런타임을 설치한 후 시스템을 다시 시작하거나 명령 프롬프트에서 **net stop was /y**에 이어 **net start w3svc**를 실행하여 IIS를 다시 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-343">After installing the runtime, restart the system or restart IIS by executing **net stop was /y** followed by **net start w3svc** from a command prompt.</span></span>

### <a name="os-upgrade-removed-the-32-bit-aspnet-core-module"></a><span data-ttu-id="34e4c-344">OS 업그레이드에서 32비트 ASP.NET Core 모듈이 제거됨</span><span class="sxs-lookup"><span data-stu-id="34e4c-344">OS upgrade removed the 32-bit ASP.NET Core Module</span></span>

* <span data-ttu-id="34e4c-345">**응용 프로그램 로그:** 모듈 DLL **C:\WINDOWS\system32\inetsrv\aspnetcore.dll**을(를) 로드하지 못했습니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-345">**Application Log:** The Module DLL **C:\WINDOWS\system32\inetsrv\aspnetcore.dll** failed to load.</span></span> <span data-ttu-id="34e4c-346">데이터 오류입니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-346">The data is the error.</span></span>

<span data-ttu-id="34e4c-347">문제 해결:</span><span class="sxs-lookup"><span data-stu-id="34e4c-347">Troubleshooting:</span></span>

* <span data-ttu-id="34e4c-348">OS를 업그레이드하는 동안 **C:\Windows\SysWOW64\inetsrv** 디렉터리에 있는 비OS 파일은 보존되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-348">Non-OS files in the **C:\Windows\SysWOW64\inetsrv** directory aren't preserved during an OS upgrade.</span></span> <span data-ttu-id="34e4c-349">OS를 업그레이드하기 전에 ASP.NET Core 모듈을 설치한 다음 OS를 업그레이드한 후에 32비트 모드에서 AppPool을 실행하려고 하면 이 문제가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-349">If you have the ASP.NET Core Module installed prior to an OS upgrade and then try to run any AppPool in 32-bit mode after an OS upgrade, you encounter this issue.</span></span> <span data-ttu-id="34e4c-350">OS를 업그레이드한 후에 ASP.NET Core 모듈을 복구합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-350">After an OS upgrade, repair the ASP.NET Core Module.</span></span> <span data-ttu-id="34e4c-351">[.NET Core Windows Server 호스팅 번들 설치](#install-the-net-core-windows-server-hosting-bundle)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="34e4c-351">See [Install the .NET Core Windows Server Hosting bundle](#install-the-net-core-windows-server-hosting-bundle).</span></span> <span data-ttu-id="34e4c-352">설치 관리자를 실행할 때 **복구**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-352">Select **Repair** when you run the installer.</span></span>

### <a name="platform-conflicts-with-rid"></a><span data-ttu-id="34e4c-353">플랫폼이 RID와 충돌함</span><span class="sxs-lookup"><span data-stu-id="34e4c-353">Platform conflicts with RID</span></span>

* <span data-ttu-id="34e4c-354">**브라우저:** HTTP 오류 502.5 - 프로세스 오류</span><span class="sxs-lookup"><span data-stu-id="34e4c-354">**Browser:** HTTP Error 502.5 - Process Failure</span></span>

* <span data-ttu-id="34e4c-355">**응용 프로그램 로그:** 실제 루트 'C:\{PATH}\'이(가) 있는 응용 프로그램 'MACHINE/WEBROOT/APPHOST/MY_APPLICATION'에서 "C:\\{PATH}\my_application.{exe|dll}" 명령줄로 프로세스를 시작하지 못했습니다., 오류 코드 = '0x80004005 : ff.</span><span class="sxs-lookup"><span data-stu-id="34e4c-355">**Application Log:** Application 'MACHINE/WEBROOT/APPHOST/MY_APPLICATION' with physical root 'C:\{PATH}\' failed to start process with commandline '"C:\\{PATH}\my_application.{exe|dll}" ', ErrorCode = '0x80004005 : ff.</span></span>

* <span data-ttu-id="34e4c-356">**ASP.NET Core 모듈 로그:** 처리되지 않은 예외: System.BadImageFormatException: 파일이나 어셈블리 'my_application.dll'을(를) 로드할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-356">**ASP.NET Core Module Log:** Unhandled Exception: System.BadImageFormatException: Could not load file or assembly 'my_application.dll'.</span></span> <span data-ttu-id="34e4c-357">프로그램을 잘못된 형식으로 로드하려고 했습니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-357">An attempt was made to load a program with an incorrect format.</span></span>

<span data-ttu-id="34e4c-358">문제 해결:</span><span class="sxs-lookup"><span data-stu-id="34e4c-358">Troubleshooting:</span></span>

* <span data-ttu-id="34e4c-359">응용 프로그램이 Kestrel에서 로컬로 실행되는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-359">Confirm that the application runs locally on Kestrel.</span></span> <span data-ttu-id="34e4c-360">프로세스 오류는 응용 프로그램 내의 문제 때문일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-360">A process failure might be the result of a problem within the application.</span></span> <span data-ttu-id="34e4c-361">자세한 내용은 [문제 해결 팁](#troubleshooting-tips)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="34e4c-361">For more information, see [Troubleshooting tips](#troubleshooting-tips).</span></span>

* <span data-ttu-id="34e4c-362">*.csproj*에서 RID와 충돌하는 `<PlatformTarget>`을 설정하지 않았는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-362">Confirm that you didn't set a `<PlatformTarget>` in your *.csproj* that conflicts with the RID.</span></span> <span data-ttu-id="34e4c-363">예를 들어 *dotnet publish -c Release -r win10-x64*를 사용하거나 *.csproj*의 `<RuntimeIdentifiers>`를 `win10-x64`로 설정하여 `x86`인 `<PlatformTarget>`를 지정하지 않고 `win10-x64`인 RID를 사용하여 게시하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-363">For example, don't specify a `<PlatformTarget>` of `x86` and publish with an RID of `win10-x64`, either by using *dotnet publish -c Release -r win10-x64* or by setting the `<RuntimeIdentifiers>` in your *.csproj* to `win10-x64`.</span></span> <span data-ttu-id="34e4c-364">프로젝트는 경고 또는 오류 없이 게시되지만 시스템에서 위와 같이 로깅된 예외와 함께 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-364">The project publishes without warning or error but fails with the above logged exceptions on the system.</span></span>

* <span data-ttu-id="34e4c-365">Azure 앱 배포에서 응용 프로그램을 업그레이드하고 새 어셈블리를 배포할 때 이 예외가 발생하면 이전 배포에서 모든 파일을 수동으로 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-365">If this exception occurs for an Azure Apps deployment when upgrading an application and deploying newer assemblies, manually delete all files from the prior deployment.</span></span> <span data-ttu-id="34e4c-366">호환되지 않는 어셈블리가 오랫동안 남아 있으면 업그레이드된 응용 프로그램을 배포할 때 `System.BadImageFormatException` 예외가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-366">Lingering incompatible assemblies can result in a `System.BadImageFormatException` exception when deploying an upgraded app.</span></span>

### <a name="uri-endpoint-wrong-or-stopped-website"></a><span data-ttu-id="34e4c-367">URI 끝점이 잘못되었거나 중지된 웹 사이트</span><span class="sxs-lookup"><span data-stu-id="34e4c-367">URI endpoint wrong or stopped website</span></span>

* <span data-ttu-id="34e4c-368">**브라우저:** ERR_CONNECTION_REFUSED</span><span class="sxs-lookup"><span data-stu-id="34e4c-368">**Browser:** ERR_CONNECTION_REFUSED</span></span>

* <span data-ttu-id="34e4c-369">**응용 프로그램 로그:** 항목 없음</span><span class="sxs-lookup"><span data-stu-id="34e4c-369">**Application Log:** No entry</span></span>

* <span data-ttu-id="34e4c-370">**ASP.NET Core 모듈 로그:** 로그 파일이 만들어지지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-370">**ASP.NET Core Module Log:** Log file not created</span></span>

<span data-ttu-id="34e4c-371">문제 해결:</span><span class="sxs-lookup"><span data-stu-id="34e4c-371">Troubleshooting:</span></span>

* <span data-ttu-id="34e4c-372">응용 프로그램에 올바른 URI 끝점을 사용하고 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-372">Confirm you are using the correct URI endpoint for the application.</span></span> <span data-ttu-id="34e4c-373">바인딩을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-373">Check your bindings.</span></span>

* <span data-ttu-id="34e4c-374">IIS 웹 사이트가 *중지됨* 상태가 아닌지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-374">Confirm that the IIS website is not in the *Stopped* state.</span></span>

### <a name="corewebengine-or-w3svc-server-features-disabled"></a><span data-ttu-id="34e4c-375">CoreWebEngine 또는 W3SVC 서버 기능이 사용되지 않음</span><span class="sxs-lookup"><span data-stu-id="34e4c-375">CoreWebEngine or W3SVC server features disabled</span></span>

* <span data-ttu-id="34e4c-376">**OS 예외:** ASP.NET Core 모듈을 사용하려면 IIS 7.0 CoreWebEngine 및 W3SVC 기능이 설치되어 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-376">**OS Exception:** The IIS 7.0 CoreWebEngine and W3SVC features must be installed to use the ASP.NET Core Module.</span></span>

<span data-ttu-id="34e4c-377">문제 해결:</span><span class="sxs-lookup"><span data-stu-id="34e4c-377">Troubleshooting:</span></span>

* <span data-ttu-id="34e4c-378">적절한 역할과 기능을 사용하도록 설정했는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-378">Confirm that you have enabled the proper role and features.</span></span> <span data-ttu-id="34e4c-379">[IIS 구성](#iis-configuration)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="34e4c-379">See [IIS Configuration](#iis-configuration).</span></span>

### <a name="incorrect-website-physical-path-or-application-missing"></a><span data-ttu-id="34e4c-380">잘못된 웹 사이트 실제 경로 또는 누락된 응용 프로그램</span><span class="sxs-lookup"><span data-stu-id="34e4c-380">Incorrect website physical path or application missing</span></span>

* <span data-ttu-id="34e4c-381">**브라우저:** 403 사용할 수 없음 - 액세스가 거부되었습니다.  **- 또는 -**  403.14 사용할 수 없음 - 웹 서버가 이 디렉터리의 내용을 표시하지 못하도록 구성되었습니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-381">**Browser:** 403 Forbidden - Access is denied **--OR--** 403.14 Forbidden - The Web server is configured to not list the contents of this directory.</span></span>

* <span data-ttu-id="34e4c-382">**응용 프로그램 로그:** 항목 없음</span><span class="sxs-lookup"><span data-stu-id="34e4c-382">**Application Log:** No entry</span></span>

* <span data-ttu-id="34e4c-383">**ASP.NET Core 모듈 로그:** 로그 파일이 만들어지지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-383">**ASP.NET Core Module Log:** Log file not created</span></span>

<span data-ttu-id="34e4c-384">문제 해결:</span><span class="sxs-lookup"><span data-stu-id="34e4c-384">Troubleshooting:</span></span>

* <span data-ttu-id="34e4c-385">IIS 웹 사이트 **기본 설정**과 실제 응용 프로그램 폴더를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-385">Check the IIS website **Basic Settings** and the physical application folder.</span></span> <span data-ttu-id="34e4c-386">응용 프로그램이 IIS 웹 사이트 **실제 경로**의 폴더에 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-386">Confirm that the application is in the folder at the IIS website **Physical path**.</span></span>

### <a name="incorrect-role-module-not-installed-or-incorrect-permissions"></a><span data-ttu-id="34e4c-387">잘못된 역할, 설치되지 않은 모듈 또는 잘못된 권한</span><span class="sxs-lookup"><span data-stu-id="34e4c-387">Incorrect role, module not installed, or incorrect permissions</span></span>

* <span data-ttu-id="34e4c-388">**브라우저:** 500.19 내부 서버 오류 - 요청된 페이지와 관련된 구성 데이터가 잘못되어 해당 페이지에 액세스할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-388">**Browser:** 500.19 Internal Server Error - The requested page cannot be accessed because the related configuration data for the page is invalid.</span></span>

* <span data-ttu-id="34e4c-389">**응용 프로그램 로그:** 항목 없음</span><span class="sxs-lookup"><span data-stu-id="34e4c-389">**Application Log:** No entry</span></span>

* <span data-ttu-id="34e4c-390">**ASP.NET Core 모듈 로그:** 로그 파일이 만들어지지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-390">**ASP.NET Core Module Log:** Log file not created</span></span>

<span data-ttu-id="34e4c-391">문제 해결:</span><span class="sxs-lookup"><span data-stu-id="34e4c-391">Troubleshooting:</span></span>

* <span data-ttu-id="34e4c-392">적절한 역할을 사용하도록 설정했는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-392">Confirm that you've enabled the proper role.</span></span> <span data-ttu-id="34e4c-393">[IIS 구성](#iis-configuration)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="34e4c-393">See [IIS Configuration](#iis-configuration).</span></span>

* <span data-ttu-id="34e4c-394">**프로그램 &amp; 기능**을 확인하고 **Microsoft ASP.NET Core 모듈**이 설치되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-394">Check **Programs &amp; Features** and confirm that the **Microsoft ASP.NET Core Module** has been installed.</span></span> <span data-ttu-id="34e4c-395">**Microsoft ASP.NET Core 모듈**이 설치된 프로그램 목록에 없으면 해당 모듈을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-395">If the **Microsoft ASP.NET Core Module** isn't present in the list of installed programs, install the module.</span></span> <span data-ttu-id="34e4c-396">[.NET Core Windows Server 호스팅 번들 설치](#install-the-net-core-windows-server-hosting-bundle)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="34e4c-396">See [Install the .NET Core Windows Server Hosting bundle](#install-the-net-core-windows-server-hosting-bundle).</span></span>

* <span data-ttu-id="34e4c-397">**응용 프로그램 풀** > **프로세스 모델** > **ID**가 **ApplicationPoolIdentity**로 설정되어 있는지 또는 사용자 지정 ID에 앱의 배포 폴더에 액세스할 수 있는 올바른 권한이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-397">Make sure that the **Application Pool** > **Process Model** > **Identity** is set to **ApplicationPoolIdentity** or your custom identity has the correct permissions to access the app's deployment folder.</span></span>

### <a name="incorrect-processpath-missing-path-variable-hosting-bundle-not-installed-systemiis-not-restarted-vc-redistributable-not-installed-or-dotnetexe-access-violation"></a><span data-ttu-id="34e4c-398">잘못된 processPath, 누락된 PATH 변수, 설치되지 않은 호스팅 번들, 다시 시작되지 않은 시스템/IIS, 설치되지 않은 VC++ 재배포 가능 패키지 또는 dotnet.exe 액세스 위반</span><span class="sxs-lookup"><span data-stu-id="34e4c-398">Incorrect processPath, missing PATH variable, hosting bundle not installed, system/IIS not restarted, VC++ Redistributable not installed, or dotnet.exe access violation</span></span>

* <span data-ttu-id="34e4c-399">**브라우저:** HTTP 오류 502.5 - 프로세스 오류</span><span class="sxs-lookup"><span data-stu-id="34e4c-399">**Browser:** HTTP Error 502.5 - Process Failure</span></span>

* <span data-ttu-id="34e4c-400">**응용 프로그램 로그:** 실제 루트 'C:\\{PATH}\'이(가) 있는 응용 프로그램 'MACHINE/WEBROOT/APPHOST/MY_APPLICATION'에서 '".\my_application.exe"' 명령줄로 프로세스를 시작하지 못했습니다., 오류 코드 = '0x80070002 : 0.</span><span class="sxs-lookup"><span data-stu-id="34e4c-400">**Application Log:** Application 'MACHINE/WEBROOT/APPHOST/MY_APPLICATION' with physical root 'C:\\{PATH}\' failed to start process with commandline '".\my_application.exe" ', ErrorCode = '0x80070002 : 0.</span></span>

* <span data-ttu-id="34e4c-401">**ASP.NET Core 모듈 로그:** 로그 파일이 만들어졌지만 비어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-401">**ASP.NET Core Module Log:** Log file created but empty</span></span>

<span data-ttu-id="34e4c-402">문제 해결:</span><span class="sxs-lookup"><span data-stu-id="34e4c-402">Troubleshooting:</span></span>

* <span data-ttu-id="34e4c-403">응용 프로그램이 Kestrel에서 로컬로 실행되는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-403">Confirm that the application runs locally on Kestrel.</span></span> <span data-ttu-id="34e4c-404">프로세스 오류는 응용 프로그램 내의 문제 때문일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-404">A process failure might be the result of a problem within the application.</span></span> <span data-ttu-id="34e4c-405">자세한 내용은 [문제 해결 팁](#troubleshooting-tips)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="34e4c-405">For more information, see [Troubleshooting tips](#troubleshooting-tips).</span></span>

* <span data-ttu-id="34e4c-406">*web.config*의 `<aspNetCore>` 요소에서 *processPath* 특성을 확인하여 FDD(프레임워크 종속 배포)에 대한 *dotnet*인지 또는 SCD(자체 포함 배포)에 대한 *\my_application.exe*인지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-406">Check the *processPath* attribute on the `<aspNetCore>` element in *web.config* to confirm that it's *dotnet* for a framework-dependent deployment (FDD) or *.\my_application.exe* for a self-contained deployment (SCD).</span></span>

* <span data-ttu-id="34e4c-407">FDD의 경우 *dotnet.exe*에서 PATH 설정을 통해 액세스하지 못할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-407">For an FDD, *dotnet.exe* might not be accessible via the PATH settings.</span></span> <span data-ttu-id="34e4c-408">시스템 PATH 설정에 *C:\Program Files\dotnet\*이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-408">Confirm that *C:\Program Files\dotnet\* exists in the System PATH settings.</span></span>

* <span data-ttu-id="34e4c-409">FDD의 경우 *dotnet.exe*에서 응용 프로그램 풀의 사용자 ID에 액세스하지 못할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-409">For an FDD, *dotnet.exe* might not be accessible for the user identity of the Application Pool.</span></span> <span data-ttu-id="34e4c-410">AppPool 사용자 ID에 *C:\Program Files\dotnet* 디렉터리에 대한 액세스 권한이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-410">Confirm that the AppPool user identity has access to the *C:\Program Files\dotnet* directory.</span></span> <span data-ttu-id="34e4c-411">*C:\Program Files\dotnet* 및 응용 프로그램 디렉터리에 AppPool 사용자 ID에 대해 구성된 거부 규칙이 없는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-411">Confirm that there are no deny rules configured for the AppPool user identity on the *C:\Program Files\dotnet* and application directories.</span></span>

* <span data-ttu-id="34e4c-412">FDD를 배포한 다음 IIS를 다시 시작하지 않고 .NET Core를 설치했을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-412">You may have deployed an FDD and installed .NET Core without restarting IIS.</span></span> <span data-ttu-id="34e4c-413">서버를 다시 시작하거나 명령 프롬프트에서 **net stop was /y**에 이어 **net start w3svc**를 실행하여 IIS를 다시 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-413">Either restart the server or restart IIS by executing **net stop was /y** followed by **net start w3svc** from a command prompt.</span></span>

* <span data-ttu-id="34e4c-414">호스팅 시스템에 .NET Core 런타임을 설치하지 않고 FDD를 배포했을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-414">You may have deployed an FDD without installing the .NET Core runtime on the hosting system.</span></span> <span data-ttu-id="34e4c-415">FDD를 배포하려고 하지만 .NET Core 런타임을 설치하지 않은 경우 시스템에서 **.NET Core Windows Server 호스팅 번들 설치 관리자**를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-415">If you're attempting to deploy a FDD and haven't installed the .NET Core runtime, run the **.NET Core Windows Server Hosting bundle installer** on the system.</span></span> <span data-ttu-id="34e4c-416">[.NET Core Windows Server 호스팅 번들 설치](#install-the-net-core-windows-server-hosting-bundle)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="34e4c-416">See [Install the .NET Core Windows Server Hosting bundle](#install-the-net-core-windows-server-hosting-bundle).</span></span> <span data-ttu-id="34e4c-417">인터넷에 연결되지 않은 시스템에 .NET Core 런타임을 설치하려는 경우 [.NET 다운로드](https://www.microsoft.com/net/download/core)에서 런타임을 얻은 다음 호스팅 번들 설치 관리자를 실행하여 ASP.NET Core 모듈을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-417">If you're attempting to install the .NET Core runtime on a system without an Internet connection, obtain the runtime from [.NET Downloads](https://www.microsoft.com/net/download/core) and run the hosting bundle installer to install the ASP.NET Core Module.</span></span> <span data-ttu-id="34e4c-418">설치를 완료하려면 시스템을 다시 시작하거나 명령 프롬프트에서 **net stop was /y**에 이어 **net start w3svc**를 실행하여 IIS를 다시 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-418">Complete the installation by restarting the system or restarting IIS by executing **net stop was /y** followed by **net start w3svc** from a command prompt.</span></span>

* <span data-ttu-id="34e4c-419">FDD를 배포한 다음 시스템/IIS를 다시 시작하지 않고 .NET Core를 설치했을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-419">You may have deployed an FDD and installed .NET Core without restarting the system/IIS.</span></span> <span data-ttu-id="34e4c-420">시스템을 다시 시작하거나 명령 프롬프트에서 **net stop was /y**에 이어 **net start w3svc**를 실행하여 IIS를 다시 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-420">Either restart the system or restart IIS by executing **net stop was /y** followed by **net start w3svc** from a command prompt.</span></span>

* <span data-ttu-id="34e4c-421">FDD를 배포했을 수 있지만 *Microsoft Visual C++ 2015 재배포 가능 패키지(x64)*가 시스템에 설치되어 있지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-421">You may have deployed an FDD and the *Microsoft Visual C++ 2015 Redistributable (x64)* is not installed on the system.</span></span> <span data-ttu-id="34e4c-422">[Microsoft 다운로드 센터](https://www.microsoft.com/download/details.aspx?id=53840)에서 설치 관리자를 얻을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-422">You may obtain an installer from the [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=53840).</span></span>

### <a name="incorrect-arguments-of-aspnetcore-element"></a><span data-ttu-id="34e4c-423">\<aspNetCore\> 요소의 잘못된 인수</span><span class="sxs-lookup"><span data-stu-id="34e4c-423">Incorrect arguments of \<aspNetCore\> element</span></span>

* <span data-ttu-id="34e4c-424">**브라우저:** HTTP 오류 502.5 - 프로세스 오류</span><span class="sxs-lookup"><span data-stu-id="34e4c-424">**Browser:** HTTP Error 502.5 - Process Failure</span></span>

* <span data-ttu-id="34e4c-425">**응용 프로그램 로그:** 실제 루트 'C:\\{PATH}\'이(가) 있는 응용 프로그램 'MACHINE/WEBROOT/APPHOST/MY_APPLICATION'에서 '"dotnet" .\my_application.dll' 명령줄로 프로세스를 시작하지 못했습니다., 오류 코드 = '0x80004005 : 80008081.</span><span class="sxs-lookup"><span data-stu-id="34e4c-425">**Application Log:** Application 'MACHINE/WEBROOT/APPHOST/MY_APPLICATION' with physical root 'C:\\{PATH}\' failed to start process with commandline '"dotnet" .\my_application.dll', ErrorCode = '0x80004005 : 80008081.</span></span>

* <span data-ttu-id="34e4c-426">**ASP.NET Core 모듈 로그:** 실행할 응용 프로그램이 없습니다.: 'PATH\my_application.dll'</span><span class="sxs-lookup"><span data-stu-id="34e4c-426">**ASP.NET Core Module Log:** The application to execute does not exist: 'PATH\my_application.dll'</span></span>

<span data-ttu-id="34e4c-427">문제 해결:</span><span class="sxs-lookup"><span data-stu-id="34e4c-427">Troubleshooting:</span></span>

* <span data-ttu-id="34e4c-428">응용 프로그램이 Kestrel에서 로컬로 실행되는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-428">Confirm that the application runs locally on Kestrel.</span></span> <span data-ttu-id="34e4c-429">프로세스 오류는 응용 프로그램 내의 문제 때문일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-429">A process failure might be the result of a problem within the application.</span></span> <span data-ttu-id="34e4c-430">자세한 내용은 [문제 해결 팁](#troubleshooting-tips)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="34e4c-430">For more information, see [Troubleshooting tips](#troubleshooting-tips).</span></span>

* <span data-ttu-id="34e4c-431">*web.config*의 `<aspNetCore>` 요소에서 *arguments* 특성을 검사하여 (a) FDD(프레임워크 종속 배포)에 대한 *.\my_application.dll*인지, 또는 (b) 없는 경우 빈 문자열(*arguments=“”*)이거나 SCD(자체 포함 배포)에 대한 앱의 인수(*arguments=“arg1, arg2, ...”*) 목록인지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-431">Examine the *arguments* attribute on the `<aspNetCore>` element in *web.config* to confirm that it is either (a) *.\my_application.dll* for a framework-dependent deployment (FDD); or (b) not present, an empty string (*arguments=""*), or a list of your app's arguments (*arguments="arg1, arg2, ..."*) for a self-contained deployment (SCD).</span></span>

### <a name="missing-net-framework-version"></a><span data-ttu-id="34e4c-432">누락된 .NET Framework 버전</span><span class="sxs-lookup"><span data-stu-id="34e4c-432">Missing .NET Framework version</span></span>

* <span data-ttu-id="34e4c-433">**브라우저:** 502.3 잘못된 게이트웨이 - 요청을 라우팅하는 동안 연결 오류가 발생했습니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-433">**Browser:** 502.3 Bad Gateway - There was a connection error while trying to route the request.</span></span>

* <span data-ttu-id="34e4c-434">**응용 프로그램 로그:** 오류 코드 = 실제 루트 'C:\\{PATH}\'이(가) 있는 응용 프로그램 'MACHINE/WEBROOT/APPHOST/MY_APPLICATION'에서 '"dotnet" .\my_application.dll' 명령줄로 프로세스를 시작하지 못했습니다., 오류 코드 = '0x80004005 : 80008081.</span><span class="sxs-lookup"><span data-stu-id="34e4c-434">**Application Log:** ErrorCode = Application 'MACHINE/WEBROOT/APPHOST/MY_APPLICATION' with physical root 'C:\\{PATH}\' failed to start process with commandline '"dotnet" .\my_application.dll', ErrorCode = '0x80004005 : 80008081.</span></span>

* <span data-ttu-id="34e4c-435">**ASP.NET Core 모듈 로그:** 메서드, 파일 또는 어셈블리 예외가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-435">**ASP.NET Core Module Log:** Missing method, file, or assembly exception.</span></span> <span data-ttu-id="34e4c-436">예외에 지정된 메서드, 파일 또는 어셈블리는 .NET Framework 메서드, 파일 또는 어셈블리입니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-436">The method, file, or assembly specified in the exception is a .NET Framework method, file, or assembly.</span></span>

<span data-ttu-id="34e4c-437">문제 해결:</span><span class="sxs-lookup"><span data-stu-id="34e4c-437">Troubleshooting:</span></span>

* <span data-ttu-id="34e4c-438">시스템에서 누락된 .NET Framework 버전을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-438">Install the .NET Framework version missing from the system.</span></span>

* <span data-ttu-id="34e4c-439">FDD(프레임워크 종속 배포)의 경우 시스템에 올바른 런타임이 설치되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-439">For a framework-dependent deployment (FDD), confirm that you have the correct runtime installed on the system.</span></span> <span data-ttu-id="34e4c-440">프로젝트를 1.1에서 2.0으로 업그레이드하고 호스팅 시스템에 배포한 다음 이 예외가 발생하면 호스팅 시스템에 Framework 2.0을 설치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-440">If you upgrade a project from 1.1 to 2.0, deploy to the hosting system, and receive this exception, ensure you install the 2.0 framework on the hosting system.</span></span>

### <a name="stopped-application-pool"></a><span data-ttu-id="34e4c-441">중지된 응용 프로그램 풀</span><span class="sxs-lookup"><span data-stu-id="34e4c-441">Stopped Application Pool</span></span>

* <span data-ttu-id="34e4c-442">**브라우저:** 503 서비스를 사용할 수 없음</span><span class="sxs-lookup"><span data-stu-id="34e4c-442">**Browser:** 503 Service Unavailable</span></span>

* <span data-ttu-id="34e4c-443">**응용 프로그램 로그:** 항목 없음</span><span class="sxs-lookup"><span data-stu-id="34e4c-443">**Application Log:** No entry</span></span>

* <span data-ttu-id="34e4c-444">**ASP.NET Core 모듈 로그:** 로그 파일이 만들어지지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-444">**ASP.NET Core Module Log:** Log file not created</span></span>

<span data-ttu-id="34e4c-445">문제 해결</span><span class="sxs-lookup"><span data-stu-id="34e4c-445">Troubleshooting</span></span>

* <span data-ttu-id="34e4c-446">응용 프로그램 풀이 *중지됨* 상태가 아닌지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-446">Confirm that the Application Pool is not in the *Stopped* state.</span></span>

### <a name="iis-integration-middleware-not-implemented"></a><span data-ttu-id="34e4c-447">IIS 통합 미들웨어가 구현되지 않음</span><span class="sxs-lookup"><span data-stu-id="34e4c-447">IIS Integration middleware not implemented</span></span>

* <span data-ttu-id="34e4c-448">**브라우저:** HTTP 오류 502.5 - 프로세스 오류</span><span class="sxs-lookup"><span data-stu-id="34e4c-448">**Browser:** HTTP Error 502.5 - Process Failure</span></span>

* <span data-ttu-id="34e4c-449">**응용 프로그램 로그:** 실제 루트 'C:\\{PATH}\'이(가) 있는 응용 프로그램 'MACHINE/WEBROOT/APPHOST/MY_APPLICATION'에서 '"C:\\{PATH}\my_application.{exe|dll}"'명령줄로 프로세스를 만들었지만 지정된 포트 '{PORT}'에서 충돌하거나 응답하지 않거나 수신 대기하지 않습니다., 오류 코드 = '0x800705b4'</span><span class="sxs-lookup"><span data-stu-id="34e4c-449">**Application Log:** Application 'MACHINE/WEBROOT/APPHOST/MY_APPLICATION' with physical root 'C:\\{PATH}\' created process with commandline '"C:\\{PATH}\my_application.{exe|dll}" ' but either crashed or did not reponse or did not listen on the given port '{PORT}', ErrorCode = '0x800705b4'</span></span>

* <span data-ttu-id="34e4c-450">**ASP.NET Core 모듈 로그:**  로그 파일이 만들어졌고 정상 작동을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-450">**ASP.NET Core Module Log:** Log file created and shows normal operation.</span></span>

<span data-ttu-id="34e4c-451">문제 해결</span><span class="sxs-lookup"><span data-stu-id="34e4c-451">Troubleshooting</span></span>

* <span data-ttu-id="34e4c-452">앱이 Kestrel에서 로컬로 실행되는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-452">Confirm that the app runs locally on Kestrel.</span></span> <span data-ttu-id="34e4c-453">프로세스 오류는 앱 내의 문제 때문일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-453">A process failure might be the result of a problem within the app.</span></span> <span data-ttu-id="34e4c-454">자세한 내용은 [문제 해결 팁](#troubleshooting-tips)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="34e4c-454">For more information, see [Troubleshooting tips](#troubleshooting-tips).</span></span>

* <span data-ttu-id="34e4c-455">응용 프로그램의 *WebHostBuilder()*(ASP.NET Core 1.x)에서 *.UseIISIntegration()* 메서드를 호출하거나 `CreateDefaultBuilder` 메서드(ASP.NET Core 2.x)를 사용하는 IIS 통합 미들웨어를 올바르게 참조했는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-455">Confirm that you've correctly referenced the IIS Integration middleware by calling the *.UseIISIntegration()* method on the application's *WebHostBuilder()* (ASP.NET Core 1.x) or used the `CreateDefaultBuilder` method (ASP.NET Core 2.x).</span></span> <span data-ttu-id="34e4c-456">[ASP.NET Core에서의 호스팅](xref:fundamentals/hosting)에서 자세한 내용을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="34e4c-456">See [Hosting in ASP.NET Core](xref:fundamentals/hosting) for details.</span></span>

### <a name="sub-application-includes-a-handlers-section"></a><span data-ttu-id="34e4c-457">하위 응용 프로그램에 \<handlers\> 섹션이 포함되어 있음</span><span class="sxs-lookup"><span data-stu-id="34e4c-457">Sub-application includes a \<handlers\> section</span></span>

* <span data-ttu-id="34e4c-458">**브라우저:** HTTP 오류 500.19 - 내부 서버 오류</span><span class="sxs-lookup"><span data-stu-id="34e4c-458">**Browser:** HTTP Error 500.19 - Internal Server Error</span></span>

* <span data-ttu-id="34e4c-459">**응용 프로그램 로그:** 항목 없음</span><span class="sxs-lookup"><span data-stu-id="34e4c-459">**Application Log:** No entry</span></span>

* <span data-ttu-id="34e4c-460">**ASP.NET Core 모듈 로그:** 로그 파일이 만들어졌고 루트 응용 프로그램에 대해 정상 작동을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-460">**ASP.NET Core Module Log:** Log file created and shows normal operation for the root application.</span></span> <span data-ttu-id="34e4c-461">하위 응용 프로그램에 대한 로그 파일이 만들어지지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-461">Log file not created for the sub-application.</span></span>

<span data-ttu-id="34e4c-462">문제 해결</span><span class="sxs-lookup"><span data-stu-id="34e4c-462">Troubleshooting</span></span>

* <span data-ttu-id="34e4c-463">하위 앱의 *web.config* 파일에 `<handlers>` 섹션이 포함되어 있지 않은지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-463">Confirm that the sub-app's *web.config* file doesn't include a `<handlers>` section.</span></span>

### <a name="application-configuration-general-issue"></a><span data-ttu-id="34e4c-464">일반적인 응용 프로그램 구성 문제</span><span class="sxs-lookup"><span data-stu-id="34e4c-464">Application configuration general issue</span></span>

* <span data-ttu-id="34e4c-465">**브라우저:** HTTP 오류 502.5 - 프로세스 오류</span><span class="sxs-lookup"><span data-stu-id="34e4c-465">**Browser:** HTTP Error 502.5 - Process Failure</span></span>

* <span data-ttu-id="34e4c-466">**응용 프로그램 로그:** 실제 루트 'C:\\{PATH}\'이(가) 있는 응용 프로그램 'MACHINE/WEBROOT/APPHOST/MY_APPLICATION'에서 '"C:\\{PATH}\my_application.{exe|dll}"'명령줄로 프로세스를 만들었지만 지정된 포트 '{PORT}'에서 충돌하거나 응답하지 않거나 수신 대기하지 않습니다., 오류 코드 = '0x800705b4'</span><span class="sxs-lookup"><span data-stu-id="34e4c-466">**Application Log:** Application 'MACHINE/WEBROOT/APPHOST/MY_APPLICATION' with physical root 'C:\\{PATH}\' created process with commandline '"C:\\{PATH}\my_application.{exe|dll}" ' but either crashed or did not reponse or did not listen on the given port '{PORT}', ErrorCode = '0x800705b4'</span></span>

* <span data-ttu-id="34e4c-467">**ASP.NET Core 모듈 로그:** 로그 파일이 만들어졌지만 비어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-467">**ASP.NET Core Module Log:** Log file created but empty</span></span>

<span data-ttu-id="34e4c-468">문제 해결</span><span class="sxs-lookup"><span data-stu-id="34e4c-468">Troubleshooting</span></span>

* <span data-ttu-id="34e4c-469">이 일반 예외는 응용 프로그램 구성 문제로 인해 프로세스가 시작되지 못했음을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-469">This general exception indicates that the process failed to start, most likely due to an application configuration issue.</span></span> <span data-ttu-id="34e4c-470">[디렉터리 구조](xref:hosting/directory-structure)를 참조하여 앱의 배포된 파일과 폴더가 적절하고, 앱의 구성 파일이 있고, 앱과 환경에 대한 올바른 설정을 포함하고 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="34e4c-470">Referring to [Directory Structure](xref:hosting/directory-structure), confirm that your app's deployed files and folders are appropriate and that your app's configuration files are present and contain the correct settings for your app and environment.</span></span> <span data-ttu-id="34e4c-471">자세한 내용은 [문제 해결 팁](#troubleshooting-tips)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="34e4c-471">For more information, see [Troubleshooting tips](#troubleshooting-tips).</span></span>

## <a name="resources"></a><span data-ttu-id="34e4c-472">리소스</span><span class="sxs-lookup"><span data-stu-id="34e4c-472">Resources</span></span>

* [<span data-ttu-id="34e4c-473">ASP.NET Core 모듈 소개</span><span class="sxs-lookup"><span data-stu-id="34e4c-473">Introduction to ASP.NET Core Module</span></span>](xref:fundamentals/servers/aspnet-core-module)
* [<span data-ttu-id="34e4c-474">ASP.NET Core 모듈 구성 참조</span><span class="sxs-lookup"><span data-stu-id="34e4c-474">ASP.NET Core Module configuration reference</span></span>](xref:hosting/aspnet-core-module)
* [<span data-ttu-id="34e4c-475">ASP.NET Core와 함께 IIS 모듈 사용</span><span class="sxs-lookup"><span data-stu-id="34e4c-475">Using IIS Modules with ASP.NET Core</span></span>](xref:hosting/iis-modules)
* [<span data-ttu-id="34e4c-476">ASP.NET Core 소개</span><span class="sxs-lookup"><span data-stu-id="34e4c-476">Introduction to ASP.NET Core</span></span>](../index.md)
* [<span data-ttu-id="34e4c-477">공식 Microsoft IIS 사이트</span><span class="sxs-lookup"><span data-stu-id="34e4c-477">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)
* [<span data-ttu-id="34e4c-478">Microsoft TechNet 라이브러리: Windows Server</span><span class="sxs-lookup"><span data-stu-id="34e4c-478">Microsoft TechNet Library: Windows Server</span></span>](https://docs.microsoft.com/windows-server/windows-server-versions)
