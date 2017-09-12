---
title: "호스팅 및 배포 개요 - ASP.NET Core"
author: tdykstra
description: "호스팅 환경을 설정하고 ASP.NET Core 앱을 해당 환경에 배포하는 방법의 개요입니다."
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 08/07/2017
ms.topic: article
ms.assetid: f0930c68-4d17-4748-adbf-801e17601eb6
ms.technology: aspnet
ms.prod: asp.net-core
uid: publishing/index
ms.openlocfilehash: df3c1f0c2768b89c3ea5dc901782170c530a542e
ms.sourcegitcommit: 9cdbfd0d670d70b9c354216aabee260c52dad5ee
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/12/2017
---
# <a name="hosting-and-deployment-overview-for-aspnet-core-apps"></a><span data-ttu-id="90d06-104">ASP.NET Core 앱에 대한 호스팅 및 배포 개요</span><span class="sxs-lookup"><span data-stu-id="90d06-104">Hosting and deployment overview for ASP.NET Core apps</span></span>

<span data-ttu-id="90d06-105">ASP.NET Core 앱을 호스팅 환경에 배포하기 위해 수행할 기본 단계는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="90d06-105">Here are the main steps you perform to deploy an ASP.NET Core app to a hosting environment:</span></span>

* <span data-ttu-id="90d06-106">호스팅 서버의 폴더에 앱을 게시합니다.</span><span class="sxs-lookup"><span data-stu-id="90d06-106">Publish the app to a folder on the hosting server.</span></span>
* <span data-ttu-id="90d06-107">요청이 들어올 때 앱을 시작하고 작동이 중단되거나 서버가 다시 부팅된 후 앱을 다시 시작하는 프로세스 관리자를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="90d06-107">Set up a process manager that starts the app when requests come in and restarts it after it crashes or the server reboots.</span></span>
* <span data-ttu-id="90d06-108">요청을 앱에 전달하는 역방향 프록시를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="90d06-108">Set up a reverse proxy that forwards requests to the app.</span></span>

## <a name="publish-to-a-folder"></a><span data-ttu-id="90d06-109">폴더에 게시</span><span class="sxs-lookup"><span data-stu-id="90d06-109">Publish to a folder</span></span> 

<span data-ttu-id="90d06-110">[dotnet publish](https://docs.microsoft.com/dotnet/articles/core/tools/dotnet-publish) CLI 명령은 응용 프로그램 코드를 컴파일하고 응용 프로그램을 *publish* 폴더로 실행하는 데 필요한 파일을 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="90d06-110">The [dotnet publish](https://docs.microsoft.com/dotnet/articles/core/tools/dotnet-publish) CLI command compiles application code and copies the files needed to run the application into a *publish* folder.</span></span> <span data-ttu-id="90d06-111">Visual Studio에서 배포할 경우 파일이 배포 대상에 복사되기 전에 `dotnet publish` 단계가 자동으로 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="90d06-111">When you deploy from Visual Studio the `dotnet publish` step is done for you automatically before files are copied to the deployment destination.</span></span>

### <a name="folder-contents"></a><span data-ttu-id="90d06-112">폴더 콘텐츠</span><span class="sxs-lookup"><span data-stu-id="90d06-112">Folder contents</span></span>

<span data-ttu-id="90d06-113">*publish* 폴더에는 응용 프로그램, 해당 종속성 및 필요한 경우 .NET 런타임에 대한 *.exe* 및 *.dll* 파일이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="90d06-113">The *publish* folder contains *.exe* and *.dll* files for the application, its dependencies, and optionally the .NET runtime.</span></span>

<span data-ttu-id="90d06-114">.NET Core 앱은 *자체 포함* 또는 *프레임워크 종속*으로 게시할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="90d06-114">A .NET Core app can be published as *self-contained* or *framework-dependent*.</span></span> <span data-ttu-id="90d06-115">앱이 자체 포함인 경우 .NET 런타임이 포함된 *.dll* 파일은 *publish* 폴더에 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="90d06-115">If the app is self-contained, the *.dll* files that contain the .NET runtime are included in the *publish* folder.</span></span>  <span data-ttu-id="90d06-116">앱이 프레임워크 종속인 경우 앱에 컴퓨터에 설치된 .NET 버전에 대한 참조가 있으므로 .NET 런타임 파일이 포함되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="90d06-116">If the app is framework-dependent, the .NET runtime files are not included because the app has a reference to a version of .NET that is installed on the computer.</span></span> <span data-ttu-id="90d06-117">기본 배포 모델은 프레임워크 종속입니다.</span><span class="sxs-lookup"><span data-stu-id="90d06-117">The default deployment model is framework-dependent.</span></span> <span data-ttu-id="90d06-118">자세한 내용은 [.NET Core 응용 프로그램 배포](https://docs.microsoft.com/dotnet/articles/core/deploying/index)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="90d06-118">For more information, see [.NET Core application deployment](https://docs.microsoft.com/dotnet/articles/core/deploying/index).</span></span>

<span data-ttu-id="90d06-119">*.exe* 및 *.dll* 파일 이외에 ASP.NET Core 앱에 대한 *publish* 폴더에는 일반적으로 구성 파일, 정적 자산 및 MVC 뷰가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="90d06-119">In addition to *.exe* and *.dll* files, the *publish* folder for an ASP.NET Core app typically contains configuration files, static assets, and MVC views.</span></span>  <span data-ttu-id="90d06-120">자세한 내용은 [디렉터리 구조](xref:hosting/directory-structure)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="90d06-120">For more information, see [Directory structure](xref:hosting/directory-structure).</span></span>

## <a name="set-up-a-process-manager"></a><span data-ttu-id="90d06-121">프로세스 관리자 설정</span><span class="sxs-lookup"><span data-stu-id="90d06-121">Set up a process manager</span></span>

<span data-ttu-id="90d06-122">ASP.NET Core 앱은 서버가 부팅되고 작동 중단 후 다시 시작될 때 시작되어야 하는 콘솔 앱입니다.</span><span class="sxs-lookup"><span data-stu-id="90d06-122">An ASP.NET Core app is a console app that has to be started when a server boots and restarted after crashes.</span></span> <span data-ttu-id="90d06-123">시작 및 다시 시작을 자동화하려면 프로세스 관리자가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="90d06-123">To automate starts and restarts you need a process manager.</span></span> <span data-ttu-id="90d06-124">ASP.NET Core에 대한 가장 일반적인 프로세스 관리자는 Linux의 [Nginx](xref:publishing/linuxproduction) 및 [Apache](xref:publishing/apache-proxy)와 Windows의 [IIS](xref:publishing/iis) 및 [Windows 서비스](xref:hosting/windows-service)입니다.</span><span class="sxs-lookup"><span data-stu-id="90d06-124">The most common process managers for ASP.NET Core are [Nginx](xref:publishing/linuxproduction) and [Apache](xref:publishing/apache-proxy) on Linux, and [IIS](xref:publishing/iis) and [Windows Service](xref:hosting/windows-service) on Windows.</span></span>

## <a name="set-up-a-reverse-proxy"></a><span data-ttu-id="90d06-125">역방향 프록시 설정</span><span class="sxs-lookup"><span data-stu-id="90d06-125">Set up a reverse proxy</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="90d06-126">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="90d06-126">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="90d06-127">앱에서 [Kestrel](xref:fundamentals/servers/kestrel) 웹 서버를 사용할 경우 [Nginx](xref:publishing/linuxproduction), [Apache](xref:publishing/apache-proxy) 또는 [IIS](xref:publishing/iis)를 역방향 프록시 서버로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="90d06-127">If your app uses the [Kestrel](xref:fundamentals/servers/kestrel) web server, you can use [Nginx](xref:publishing/linuxproduction), [Apache](xref:publishing/apache-proxy), or [IIS](xref:publishing/iis) as a reverse proxy server.</span></span> <span data-ttu-id="90d06-128">역방향 프록시 서버는 인터넷에서 HTTP 요청을 수신하고 몇몇 사전 처리 후에 Kestrel에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="90d06-128">A reverse proxy server receives HTTP requests from the Internet and forwards them to Kestrel after some preliminary handling.</span></span> <span data-ttu-id="90d06-129">자세한 내용은 [Kestrel를 역방향 프록시와 함께 사용할 경우](xref:fundamentals/servers/kestrel?tabs=aspnetcore2x#when-to-use-kestrel-with-a-reverse-proxy)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="90d06-129">For more information, see [When to use Kestrel with a reverse proxy](xref:fundamentals/servers/kestrel?tabs=aspnetcore2x#when-to-use-kestrel-with-a-reverse-proxy).</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="90d06-130">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="90d06-130">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="90d06-131">앱에서 [Kestrel](xref:fundamentals/servers/kestrel) 웹 서버를 사용하고 앱이 인터넷에 노출될 경우 [Nginx](xref:publishing/linuxproduction), [Apache](xref:publishing/apache-proxy) 또는 [IIS](xref:publishing/iis)를 역방향 프록시 서버로 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="90d06-131">If your app uses the [Kestrel](xref:fundamentals/servers/kestrel) web server and will be exposed to the Internet, you must use [Nginx](xref:publishing/linuxproduction), [Apache](xref:publishing/apache-proxy), or [IIS](xref:publishing/iis) as a reverse proxy server.</span></span> <span data-ttu-id="90d06-132">역방향 프록시 서버는 인터넷에서 HTTP 요청을 수신하고 몇몇 사전 처리 후에 Kestrel에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="90d06-132">A reverse proxy server receives HTTP requests from the Internet and forwards them to Kestrel after some preliminary handling.</span></span> <span data-ttu-id="90d06-133">역방향 프록시를 사용하는 주요 이유는 보안 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="90d06-133">The main reason for using a reverse proxy is security.</span></span> <span data-ttu-id="90d06-134">자세한 내용은 [Kestrel를 역방향 프록시와 함께 사용할 경우](xref:fundamentals/servers/kestrel?tabs=aspnetcore1x#when-to-use-kestrel-with-a-reverse-proxy)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="90d06-134">For more information, see [When to use Kestrel with a reverse proxy](xref:fundamentals/servers/kestrel?tabs=aspnetcore1x#when-to-use-kestrel-with-a-reverse-proxy).</span></span>

---

## <a name="using-visual-studio-and-msbuild-to-automate-deployment"></a><span data-ttu-id="90d06-135">Visual Studio 및 MSBuild를 사용하여 배포 자동화</span><span class="sxs-lookup"><span data-stu-id="90d06-135">Using Visual Studio and MSBuild to automate deployment</span></span>

<span data-ttu-id="90d06-136">일반적으로 배포에는 `dotnet publish`에서 서버로 출력을 복사하는 것 외에 추가 작업이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="90d06-136">Deployment often requires additional tasks besides copying the output from `dotnet publish` to a server.</span></span> <span data-ttu-id="90d06-137">예를 들어 추가 파일을 *publish* 폴더에 포함하거나 파일을 제외할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="90d06-137">For example, you might want to include extra files in the *publish* folder, or exclude files from it.</span></span> <span data-ttu-id="90d06-138">Visual Studio에서는 웹 배포에 MSBuild를 사용하고 MSBuild를 사용자 지정하여 배포 중에 많은 다른 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="90d06-138">Visual Studio uses MSBuild for web deployment, and you can customize MSBuild to do many other tasks during deployment.</span></span> <span data-ttu-id="90d06-139">자세한 내용은 [Visual Studio에서 프로필 게시](xref:publishing/web-publishing-vs) 및 [MSBuild 및 Team Foundation Build 사용](http://msbuildbook.com/) 문서를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="90d06-139">For more information, see [Publish profiles in Visual Studio](xref:publishing/web-publishing-vs) and the [Using MSBuild and Team Foundation Build](http://msbuildbook.com/) book.</span></span>

<span data-ttu-id="90d06-140">[웹 게시 기능](xref:tutorials/publish-to-azure-webapp-using-vs)을 사용하거나 [기본 제공 Git 지원](xref:publishing/azure-continuous-deployment)을 사용하여 Visual Studio에서 Azure App Service로 직접 배포할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="90d06-140">You can deploy directly from Visual Studio to Azure App Service by using [the Publish Web feature](xref:tutorials/publish-to-azure-webapp-using-vs) or by using [built-in Git support](xref:publishing/azure-continuous-deployment).</span></span> <span data-ttu-id="90d06-141">Visual Studio Team Services에서는 [Azure App Service에 연속 배포](https://www.visualstudio.com/docs/build/aspnet/core/quick-to-azure)를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="90d06-141">Visual Studio Team Services supports [continuous deployment to Azure App Service](https://www.visualstudio.com/docs/build/aspnet/core/quick-to-azure).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="90d06-142">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="90d06-142">Additional resources</span></span>

<span data-ttu-id="90d06-143">Docker를 호스팅 환경으로 사용하는 방법에 대한 자세한 내용은 [Docker에서 ASP.NET Core 앱 호스트](xref:publishing/docker)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="90d06-143">For information about using Docker as a hosting environment, see [Host ASP.NET Core apps in Docker](xref:publishing/docker).</span></span>
