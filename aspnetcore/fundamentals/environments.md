---
title: "여러 환경 작업"
author: ardalis
description: 
keywords: "ASP.NET Core, 환경 설정, ASPNETCORE_ENVIRONMENT"
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: b5bba985-be12-4464-9a01-df3599b2a6f1
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/environments
ms.openlocfilehash: 8f8612fd9c92370d9b0a86572dca654a6a034916
ms.sourcegitcommit: 9cdbfd0d670d70b9c354216aabee260c52dad5ee
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/12/2017
---
# <a name="working-with-multiple-environments"></a><span data-ttu-id="b5f79-103">여러 환경 작업</span><span class="sxs-lookup"><span data-stu-id="b5f79-103">Working with multiple environments</span></span>

<span data-ttu-id="b5f79-104">으로 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="b5f79-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="b5f79-105">ASP.NET Core 개발, 스테이징 및 프로덕션 등의 여러 환경에 걸쳐 앱 동작을 제어 하기 위한 지원을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="b5f79-105">ASP.NET Core provides support for controlling app behavior across multiple environments, such as development, staging, and production.</span></span> <span data-ttu-id="b5f79-106">환경 변수는 해당 환경에 구성 된 응용 프로그램 런타임 환경을 나타내는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b5f79-106">Environment variables are used to indicate the runtime environment, allowing the app to be configured for that environment.</span></span>

[<span data-ttu-id="b5f79-107">샘플 코드 보기 또는 다운로드</span><span class="sxs-lookup"><span data-stu-id="b5f79-107">View or download sample code</span></span>](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/environments/sample)

## <a name="development-staging-production"></a><span data-ttu-id="b5f79-108">개발, 스테이징 프로덕션</span><span class="sxs-lookup"><span data-stu-id="b5f79-108">Development, Staging, Production</span></span>

<span data-ttu-id="b5f79-109">ASP.NET Core 특정 참조 [환경 변수](https://github.com/aspnet/Home/wiki), `ASPNETCORE_ENVIRONMENT` 를 응용 프로그램에서 현재 실행 중인 환경에 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="b5f79-109">ASP.NET Core references a particular [environment variable](https://github.com/aspnet/Home/wiki), `ASPNETCORE_ENVIRONMENT` to describe the environment the application is currently running in.</span></span> <span data-ttu-id="b5f79-110">이 변수를 설정 값을 원하는 하지만 규칙에 따라 3 개의 값 사용: `Development`, `Staging`, 및 `Production`합니다.</span><span class="sxs-lookup"><span data-stu-id="b5f79-110">This variable can be set to any value you like, but three values are used by convention: `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="b5f79-111">이러한 샘플에서 사용 되는 값 및 ASP.NET Core와 함께 제공 되는 서식 파일을 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b5f79-111">You will find these values used in the samples and templates provided with ASP.NET Core.</span></span>

<span data-ttu-id="b5f79-112">현재 환경 설정은 응용 프로그램 내에서 프로그래밍 방식으로 검색할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b5f79-112">The current environment setting can be detected programmatically from within your application.</span></span> <span data-ttu-id="b5f79-113">환경을 사용할 수는 또한 [태그 도우미](../mvc/views/tag-helpers/index.md) 의 특정 섹션에 포함 하도록 프로그램 [보기](../mvc/views/index.md) 현재 응용 프로그램 환경에 따라 합니다.</span><span class="sxs-lookup"><span data-stu-id="b5f79-113">In addition, you can use the Environment [tag helper](../mvc/views/tag-helpers/index.md) to include certain sections in your [view](../mvc/views/index.md) based on the current application environment.</span></span>

<span data-ttu-id="b5f79-114">참고: Windows 및 macOS에서 지정한 환경 이름을 대/소문자 구분.</span><span class="sxs-lookup"><span data-stu-id="b5f79-114">Note: On Windows and macOS, the specified environment name is case insensitive.</span></span> <span data-ttu-id="b5f79-115">이 변수를 설정 하는지 여부를 `Development` 또는 `development` 또는 `DEVELOPMENT` 결과 같은 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b5f79-115">Whether you set the variable to `Development` or `development` or `DEVELOPMENT` the results will be the same.</span></span> <span data-ttu-id="b5f79-116">그러나 Linux는는 **대/소문자 구분** 기본적으로 운영 체제.</span><span class="sxs-lookup"><span data-stu-id="b5f79-116">However, Linux is a **case sensitive** OS by default.</span></span> <span data-ttu-id="b5f79-117">환경 변수, 파일 이름 및 설정을 대/소문자 구분을 해야합니다.</span><span class="sxs-lookup"><span data-stu-id="b5f79-117">Environment variables, file names and settings require case sensitivity.</span></span>

### <a name="development"></a><span data-ttu-id="b5f79-118">개발</span><span class="sxs-lookup"><span data-stu-id="b5f79-118">Development</span></span>

<span data-ttu-id="b5f79-119">이 응용 프로그램을 개발할 때 사용 되는 환경 이어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b5f79-119">This should be the environment used when developing an application.</span></span> <span data-ttu-id="b5f79-120">앱을 실행할 때 프로덕션 환경에서와 같은 사용 가능 하도록 해서는 안 되는 기능을 활성화 하는 일반적으로 [개발자 예외 페이지](xref:fundamentals/error-handling#the-developer-exception-page)합니다.</span><span class="sxs-lookup"><span data-stu-id="b5f79-120">It is typically used to enable features that you wouldn't want to be available when the app runs in production, such as the [developer exception page](xref:fundamentals/error-handling#the-developer-exception-page).</span></span>

<span data-ttu-id="b5f79-121">Visual Studio를 사용 하는 경우 프로젝트의 디버그 프로필에 환경을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b5f79-121">If you're using Visual Studio, the environment can be configured in your project's debug profiles.</span></span> <span data-ttu-id="b5f79-122">디버그 프로필 지정는 [서버](xref:fundamentals/servers/index) 를 설정할 응용 프로그램 및 모든 환경 변수를 시작할 때 사용 하도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="b5f79-122">Debug profiles specify the [server](xref:fundamentals/servers/index) to use when launching the application and any environment variables to be set.</span></span> <span data-ttu-id="b5f79-123">프로젝트는 환경 변수를 서로 다르게 설정 하는 여러 디버그 프로필을 가질 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b5f79-123">Your project can have multiple debug profiles that set environment variables differently.</span></span> <span data-ttu-id="b5f79-124">사용 하 여이 프로필을 관리 하는 **디버그** 웹 응용 프로그램 프로젝트의 탭 **속성** 메뉴.</span><span class="sxs-lookup"><span data-stu-id="b5f79-124">You manage these profiles by using the **Debug** tab of your web application project's **Properties** menu.</span></span> <span data-ttu-id="b5f79-125">프로젝트 속성에 설정 된 값에 유지 되는 *launchSettings.json* 파일과 있습니다 또한 프로필을 구성할 수 해당 파일을 직접 편집 하 여 합니다.</span><span class="sxs-lookup"><span data-stu-id="b5f79-125">The values you set in project properties are persisted in the *launchSettings.json* file, and you can also configure profiles by editing that file directly.</span></span>

<span data-ttu-id="b5f79-126">IIS Express에 대 한 프로필은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="b5f79-126">The profile for IIS Express is shown here:</span></span>

![프로젝트 속성 설정 환경 변수](environments/_static/project-properties-debug.png)

<span data-ttu-id="b5f79-128">다음은 한 `launchSettings.json` 프로필에 대 한 포함 된 파일 `Development` 및 `Staging`:</span><span class="sxs-lookup"><span data-stu-id="b5f79-128">Here is a `launchSettings.json` file that includes profiles for `Development` and `Staging`:</span></span>

<span data-ttu-id="b5f79-129">[!code-json[Main](../fundamentals/environments/sample/src/Environments/Properties/launchSettings.json?highlight=15,22)]</span><span class="sxs-lookup"><span data-stu-id="b5f79-129">[!code-json[Main](../fundamentals/environments/sample/src/Environments/Properties/launchSettings.json?highlight=15,22)]</span></span>

<span data-ttu-id="b5f79-130">사용 되는 웹 서버를 다시 시작 될 때까지 프로젝트 프로필에 대 한 변경 내용이 적용 되지 않을 수 있습니다 (특히 Kestrel를 다시 시작 해야 해당 환경에 대해 변경 내용을 감지 합니다).</span><span class="sxs-lookup"><span data-stu-id="b5f79-130">Changes made to project profiles may not take effect until the web server used is restarted (in particular, Kestrel must be restarted before it will detect changes made to its environment).</span></span>

>[!WARNING]
> <span data-ttu-id="b5f79-131">에 저장 된 환경 변수가 *launchSettings.json* 어떤 방식으로든에서 보호 하지 않는 하 고 사용 하는 경우 프로젝트에 대 한 소스 코드 리포지토리의 일부가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b5f79-131">Environment variables stored in *launchSettings.json* are not secured in any way and will be part of the source code repository for your project, if you use one.</span></span> <span data-ttu-id="b5f79-132">**이 파일에 자격 증명 또는 기타 보안 데이터를 저장 하지 마십시오.**</span><span class="sxs-lookup"><span data-stu-id="b5f79-132">**Never store credentials or other secret data in this file.**</span></span> <span data-ttu-id="b5f79-133">사용 하 여 이러한 데이터를 저장 해야 할 경우는 *암호 관리자* 에 설명 된 도구 [개발 하는 동안 앱 암호의 안전한 저장소](../security/app-secrets.md#security-app-secrets)합니다.</span><span class="sxs-lookup"><span data-stu-id="b5f79-133">If you need a place to store such data, use the *Secret Manager* tool described in [Safe storage of app secrets during development](../security/app-secrets.md#security-app-secrets).</span></span>

### <a name="staging"></a><span data-ttu-id="b5f79-134">스테이징</span><span class="sxs-lookup"><span data-stu-id="b5f79-134">Staging</span></span>

<span data-ttu-id="b5f79-135">일반적으로는 `Staging` 환경은 프로덕션에 배포 하기 전에 최종 테스트에 사용 되는 사전 프로덕션 환경입니다.</span><span class="sxs-lookup"><span data-stu-id="b5f79-135">By convention, a `Staging` environment is a pre-production environment used for final testing before deployment to production.</span></span> <span data-ttu-id="b5f79-136">이상적으로 실제 특성도 그대로 반영 해야 프로덕션, 프로덕션 환경에서 발생할 수 있는 모든 문제는 사용자에 게 영향을 주지 않고 해결할 수 있습니다를 스테이징 환경에서 먼저 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="b5f79-136">Ideally, its physical characteristics should mirror that of production, so that any issues that may arise in production occur first in the staging environment, where they can be addressed without impact to users.</span></span>

### <a name="production"></a><span data-ttu-id="b5f79-137">프로덕션</span><span class="sxs-lookup"><span data-stu-id="b5f79-137">Production</span></span>

<span data-ttu-id="b5f79-138">`Production` 환경은 응용 프로그램이 라이브 상태일 때 실행 하는 환경 이며 최종 사용자가 사용 되 고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b5f79-138">The `Production` environment is the environment in which the application runs when it is live and being used by end users.</span></span> <span data-ttu-id="b5f79-139">이 환경 보안, 성능 및 응용 프로그램의 견고성을 최대화 하기 위해 구성 되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b5f79-139">This environment should be configured to maximize security, performance, and application robustness.</span></span> <span data-ttu-id="b5f79-140">다음과 같은 몇 가지 일반적인 프로덕션 환경에 있을 수 있는 것 다른 설정을 개발</span><span class="sxs-lookup"><span data-stu-id="b5f79-140">Some common settings that a production environment might have that would differ from development include:</span></span>

* <span data-ttu-id="b5f79-141">캐싱 설정</span><span class="sxs-lookup"><span data-stu-id="b5f79-141">Turn on caching</span></span>

* <span data-ttu-id="b5f79-142">모든 클라이언트 측 리소스가 번들로, 축소, 및 CDN에서 잠재적으로 제공</span><span class="sxs-lookup"><span data-stu-id="b5f79-142">Ensure all client-side resources are bundled, minified, and potentially served from a CDN</span></span>

* <span data-ttu-id="b5f79-143">진단 ErrorPages 해제</span><span class="sxs-lookup"><span data-stu-id="b5f79-143">Turn off diagnostic ErrorPages</span></span>

* <span data-ttu-id="b5f79-144">오류 페이지 설정</span><span class="sxs-lookup"><span data-stu-id="b5f79-144">Turn on friendly error pages</span></span>

* <span data-ttu-id="b5f79-145">프로덕션 로깅 및 모니터링을 사용 하도록 설정 (예를 들어 [Application Insights](https://azure.microsoft.com/documentation/articles/app-insights-asp-net-five/))</span><span class="sxs-lookup"><span data-stu-id="b5f79-145">Enable production logging and monitoring (for example, [Application Insights](https://azure.microsoft.com/documentation/articles/app-insights-asp-net-five/))</span></span>

<span data-ttu-id="b5f79-146">이 속성은 결코 전체 목록은으로 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b5f79-146">This is by no means meant to be a complete list.</span></span> <span data-ttu-id="b5f79-147">응용 프로그램의 많은 부분에서 환경 검사 분산 되지 않도록 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="b5f79-147">It's best to avoid scattering environment checks in many parts of your application.</span></span> <span data-ttu-id="b5f79-148">응용 프로그램의 내에서 이러한 검사를 수행 하는 방법이 권장된 되는 대신, `Startup` 클래스가 가능</span><span class="sxs-lookup"><span data-stu-id="b5f79-148">Instead, the recommended approach is to perform such checks within the application's `Startup` class(es) wherever possible</span></span>

## <a name="setting-the-environment"></a><span data-ttu-id="b5f79-149">환경 설정</span><span class="sxs-lookup"><span data-stu-id="b5f79-149">Setting the environment</span></span>

<span data-ttu-id="b5f79-150">환경 설정에 대 한 메서드는 운영 체제에 따라 달라 집니다.</span><span class="sxs-lookup"><span data-stu-id="b5f79-150">The method for setting the environment depends on the operating system.</span></span>

### <a name="windows"></a><span data-ttu-id="b5f79-151">창</span><span class="sxs-lookup"><span data-stu-id="b5f79-151">Windows</span></span>
<span data-ttu-id="b5f79-152">설정 하는 `ASPNETCORE_ENVIRONMENT` 를 사용 하 여 앱이 시작 하는 경우 현재 세션에 대 한 `dotnet run`, 다음 명령을 사용 하는</span><span class="sxs-lookup"><span data-stu-id="b5f79-152">To set the `ASPNETCORE_ENVIRONMENT` for the current session, if the app is started using `dotnet run`, the following commands are used</span></span>

<span data-ttu-id="b5f79-153">**명령줄**</span><span class="sxs-lookup"><span data-stu-id="b5f79-153">**Command line**</span></span>
```
set ASPNETCORE_ENVIRONMENT=Development
```
<span data-ttu-id="b5f79-154">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="b5f79-154">**PowerShell**</span></span>
```
$Env:ASPNETCORE_ENVIRONMENT = "Development"
```

<span data-ttu-id="b5f79-155">이 명령은 현재 창에 대해서만 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b5f79-155">These commands take effect only for the current window.</span></span> <span data-ttu-id="b5f79-156">창이 닫히면 ASPNETCORE_ENVIRONMENT 설정을 기본 설정 또는 컴퓨터 값 되돌아갑니다.</span><span class="sxs-lookup"><span data-stu-id="b5f79-156">When the window is closed, the ASPNETCORE_ENVIRONMENT setting reverts to the default setting or machine value.</span></span> <span data-ttu-id="b5f79-157">Windows 열 값을 전역으로 설정 된 **제어판** > **시스템** > **고급 시스템 설정** 추가 하거나는 편집`ASPNETCORE_ENVIRONMENT` 값입니다.</span><span class="sxs-lookup"><span data-stu-id="b5f79-157">In order to set the value globally on Windows open the **Control Panel** > **System** > **Advanced system settings** and add or edit the `ASPNETCORE_ENVIRONMENT` value.</span></span>

![시스템의 고급 속성](environments/_static/systemsetting_environment.png)

![ASPNET 코어 환경 변수](environments/_static/windows_aspnetcore_environment.png) 

<span data-ttu-id="b5f79-160">**web.config**</span><span class="sxs-lookup"><span data-stu-id="b5f79-160">**web.config**</span></span>

<span data-ttu-id="b5f79-161">참조는 *환경 변수 설정* 의 섹션은 [ASP.NET Core 모듈 구성 참조](xref:hosting/aspnet-core-module#setting-environment-variables) 항목입니다.</span><span class="sxs-lookup"><span data-stu-id="b5f79-161">See the *Setting environment variables* section of the [ASP.NET Core Module configuration reference](xref:hosting/aspnet-core-module#setting-environment-variables) topic.</span></span>

<span data-ttu-id="b5f79-162">**IIS 응용 프로그램 풀 당**</span><span class="sxs-lookup"><span data-stu-id="b5f79-162">**Per IIS Application Pool**</span></span>

<span data-ttu-id="b5f79-163">로 설정 해야 하는 경우 (IIS 10.0 이상에서 지원 됨) 격리 된 응용 프로그램 풀에서 실행 되는 개별 앱에 대 한 환경 변수 참조는 *AppCmd.exe 명령을* 의 섹션은 [환경 변수 \< environmentVariables >](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) IIS의 항목을 참조 설명서입니다.</span><span class="sxs-lookup"><span data-stu-id="b5f79-163">If you need to set environment variables for individual apps running in isolated Application Pools (supported on IIS 10.0+), see the *AppCmd.exe command* section of the [Environment Variables \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic in the IIS reference documentation.</span></span>

### <a name="macos"></a><span data-ttu-id="b5f79-164">MacOS</span><span class="sxs-lookup"><span data-stu-id="b5f79-164">macOS</span></span>
<span data-ttu-id="b5f79-165">MacOS에 대 한 현재 환경 설정 구성 방법은 인라인 응용 프로그램을 실행 하는 경우</span><span class="sxs-lookup"><span data-stu-id="b5f79-165">Setting the current environment for macOS can be done in-line when running the application;</span></span>

```bash
ASPNETCORE_ENVIRONMENT=Development dotnet run
```
<span data-ttu-id="b5f79-166">사용 하 여 또는 `export` 응용 프로그램을 실행 하기 전에 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b5f79-166">or using `export` to set it prior to running the app.</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Development
``` 
<span data-ttu-id="b5f79-167">시스템 수준 환경 변수 설정는 *.bashrc* 또는 *.bash_profile* 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="b5f79-167">Machine level environment variables are set in the *.bashrc*  or *.bash_profile* file.</span></span> <span data-ttu-id="b5f79-168">임의의 텍스트 편집기를 사용 하 여 파일을 편집 하 고 다음 문을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="b5f79-168">Edit the file using any text editor and add the following statment.</span></span>

```
export ASPNETCORE_ENVIRONMENT=Development
```  

### <a name="linux"></a><span data-ttu-id="b5f79-169">Linux</span><span class="sxs-lookup"><span data-stu-id="b5f79-169">Linux</span></span>
<span data-ttu-id="b5f79-170">사용 하 여 Linux 배포판는 `export` 세션 변수 설정을 기반으로 하는 명령줄에서 명령 및 *bash_profile* 시스템 수준 환경 설정에 대 한 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="b5f79-170">For Linux distros, use the `export` command at the command line for session based variable settings and *bash_profile* file for machine level environment settings.</span></span>

## <a name="determining-the-environment-at-runtime"></a><span data-ttu-id="b5f79-171">런타임 시 환경 확인</span><span class="sxs-lookup"><span data-stu-id="b5f79-171">Determining the environment at runtime</span></span>

<span data-ttu-id="b5f79-172">`IHostingEnvironment` 서비스 환경 작업에 대 한 핵심 추상화를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="b5f79-172">The `IHostingEnvironment` service provides the core abstraction for working with environments.</span></span> <span data-ttu-id="b5f79-173">이 서비스 제공 되며 ASP.NET에서 호스팅 계층을 통해 시작 논리에 삽입할 수 [종속성 주입](dependency-injection.md)합니다.</span><span class="sxs-lookup"><span data-stu-id="b5f79-173">This service is provided by the ASP.NET hosting layer, and can be injected into your startup logic via [Dependency Injection](dependency-injection.md).</span></span> <span data-ttu-id="b5f79-174">Visual Studio에서 ASP.NET Core 웹 사이트 템플릿을 사용 하는이 방식 (있는 경우) 환경 관련 구성 파일을 로드 하 고 응용 프로그램의 오류 처리 설정을 사용자 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b5f79-174">The ASP.NET Core web site template in Visual Studio uses this approach to load environment-specific configuration files (if present) and to customize the app's error handling settings.</span></span> <span data-ttu-id="b5f79-175">두 경우 모두이 문제를 호출 하 여 현재 지정 된 환경 참조에 의해 이루어집니다 `EnvironmentName` 또는 `IsEnvironment` 인스턴스의 `IHostingEnvironment` 적절 한 메서드에 전달 합니다.</span><span class="sxs-lookup"><span data-stu-id="b5f79-175">In both cases, this behavior is achieved by referring to the currently specified environment by calling `EnvironmentName` or `IsEnvironment` on the instance of `IHostingEnvironment` passed into the appropriate method.</span></span>

> [!NOTE]
> <span data-ttu-id="b5f79-176">응용 프로그램이 사용 하 여 특정 한 환경에서 실행 되 고 있는지 여부를 확인 하는 경우 `env.IsEnvironment("environmentname")` 이후 대/소문자를 무시 올바르게 됩니다 (하는 경우를 확인 하는 대신 `env.EnvironmentName == "Development"` 예를 들어).</span><span class="sxs-lookup"><span data-stu-id="b5f79-176">If you need to check whether the application is running in a particular environment, use `env.IsEnvironment("environmentname")` since it will correctly ignore case (instead of checking if `env.EnvironmentName == "Development"` for example).</span></span>

<span data-ttu-id="b5f79-177">예를 들어 Configure 메서드 환경 특정 오류 처리를 설정 하려면 다음 코드를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b5f79-177">For example, you can use the following code in your Configure method to setup environment specific error handling:</span></span>

<span data-ttu-id="b5f79-178">[!code-csharp[Main](environments/sample/src/Environments/Startup.cs?range=19-30)]</span><span class="sxs-lookup"><span data-stu-id="b5f79-178">[!code-csharp[Main](environments/sample/src/Environments/Startup.cs?range=19-30)]</span></span>

<span data-ttu-id="b5f79-179">응용 프로그램에서 실행 중인 경우는 `Development` 이면 환경 사용 하면 Visual Studio, 개발 특정 오류 페이지 (일반적으로 실행 해서는 안 프로덕션 환경에서) 및 특별 한 데이터베이스 오류 "BrowserLink" 기능을 사용 하는 데 필요한 런타임 지원 페이지 (마이그레이션을 적용 하는 방법을 제공 하 고 개발에만 사용 하므로 해야) 합니다.</span><span class="sxs-lookup"><span data-stu-id="b5f79-179">If the app is running in a `Development` environment, then it enables the runtime support necessary to use the "BrowserLink" feature in Visual Studio, development-specific error pages (which typically should not be run in production) and special database error pages (which provide a way to apply migrations and should therefore only be used in development).</span></span> <span data-ttu-id="b5f79-180">그렇지 않으면 응용 프로그램 개발 환경에서 실행 되지 않는 경우 처리 되지 않은 예외에 대 한 응답에 표시 되는 표준 오류 처리 페이지 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b5f79-180">Otherwise, if the app is not running in a development environment, a standard error handling page is configured to be displayed in response to any unhandled exceptions.</span></span>

<span data-ttu-id="b5f79-181">현재 환경에 따라 런타임에 클라이언트에 보내도록 내용을 결정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b5f79-181">You may need to determine which content to send to the client at runtime, depending on the current environment.</span></span> <span data-ttu-id="b5f79-182">예를 들어 개발 환경에서 하면 일반적으로 사용할 최소화 하지 않은 스크립트 및 스타일 시트를 디버깅을 보다 쉽게 해줍니다.</span><span class="sxs-lookup"><span data-stu-id="b5f79-182">For example, in a development environment you generally serve non-minimized scripts and style sheets, which makes debugging easier.</span></span> <span data-ttu-id="b5f79-183">프로덕션 환경과 테스트 환경 축소 된 버전을 제공 해야 하 고 CDN에서 일반적으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="b5f79-183">Production and test environments should serve the minified versions and generally from a CDN.</span></span> <span data-ttu-id="b5f79-184">환경을 사용 하 여 수행할 수 있습니다 [태그 도우미](../mvc/views/tag-helpers/intro.md)합니다.</span><span class="sxs-lookup"><span data-stu-id="b5f79-184">You can do this using the Environment [tag helper](../mvc/views/tag-helpers/intro.md).</span></span> <span data-ttu-id="b5f79-185">환경 태그 도우미 됩니다 현재 환경을 사용 하 여 지정 하는 환경 중 하 나와 일치 하는 경우에 해당 콘텐츠를 렌더링 된 `names` 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="b5f79-185">The Environment tag helper will only render its contents if the current environment matches one of the environments specified using the `names` attribute.</span></span>

<span data-ttu-id="b5f79-186">[!code-html[Main](environments/sample/src/Environments/Views/Shared/_Layout.cshtml?range=13-22)]</span><span class="sxs-lookup"><span data-stu-id="b5f79-186">[!code-html[Main](environments/sample/src/Environments/Views/Shared/_Layout.cshtml?range=13-22)]</span></span>

<span data-ttu-id="b5f79-187">태그 도우미를 사용 하 여 응용 프로그램 보고를 시작 하려면 [태그 도우미 소개](../mvc/views/tag-helpers/intro.md)합니다.</span><span class="sxs-lookup"><span data-stu-id="b5f79-187">To get started with using tag helpers in your application see [Introduction to Tag Helpers](../mvc/views/tag-helpers/intro.md).</span></span>

## <a name="startup-conventions"></a><span data-ttu-id="b5f79-188">시작 규칙</span><span class="sxs-lookup"><span data-stu-id="b5f79-188">Startup conventions</span></span>

<span data-ttu-id="b5f79-189">ASP.NET Core 응용 프로그램의 시작을 현재 환경에 따라 구성 하는 규칙 기반 방식을 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="b5f79-189">ASP.NET Core supports a convention-based approach to configuring an application's startup based on the current environment.</span></span> <span data-ttu-id="b5f79-190">또한 프로그래밍 방식으로 환경의을 만들고 사용자 지정 규칙을 관리할 수에 따라 응용 프로그램의 동작을 제어할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b5f79-190">You can also programmatically control how your application behaves according to which environment it is in, allowing you to create and manage your own conventions.</span></span>

<span data-ttu-id="b5f79-191">ASP.NET Core 응용 프로그램 시작 되 면는 `Startup` 클래스 부트스트랩 하는 응용 프로그램, 등 구성 설정을 로드 하는 데 사용 됩니다 ([ASP.NET 시작에 대 한 자세한](startup.md)).</span><span class="sxs-lookup"><span data-stu-id="b5f79-191">When an ASP.NET Core application starts, the `Startup` class is used to bootstrap the application, load its configuration settings, etc. ([learn more about ASP.NET startup](startup.md)).</span></span> <span data-ttu-id="b5f79-192">그러나 클래스가 있는 경우 라는 `Startup{EnvironmentName}` (예를 들어 `StartupDevelopment`), 및 `ASPNETCORE_ENVIRONMENT` 해당 이름 다음에 일치 하는 환경 변수 `Startup` 클래스 대신 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b5f79-192">However, if a class exists named `Startup{EnvironmentName}` (for example `StartupDevelopment`), and the `ASPNETCORE_ENVIRONMENT` environment variable matches that name, then that `Startup` class is used instead.</span></span> <span data-ttu-id="b5f79-193">따라서 구성할 수 있습니다. `Startup` 개발을 위한 별도 있지만 저마다 `StartupProduction` 프로덕션 환경에서 앱을 실행할 때 사용할 수는 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b5f79-193">Thus, you could configure `Startup` for development, but have a separate `StartupProduction` that would be used when the app is run in production.</span></span> <span data-ttu-id="b5f79-194">또는 그 반대의 경우도 마찬가지입니다.</span><span class="sxs-lookup"><span data-stu-id="b5f79-194">Or vice versa.</span></span>

> [!NOTE]
> <span data-ttu-id="b5f79-195">호출 `WebHostBuilder.UseStartup<TStartup>()` 구성 섹션을 재정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="b5f79-195">Calling `WebHostBuilder.UseStartup<TStartup>()` overrides configuration sections.</span></span>

<span data-ttu-id="b5f79-196">사용 하 여 완전히 별개의 `Startup` 현재 환경에 따라 클래스 내에서 응용 프로그램은 구성 하는 방법에 대 한 조정이 만들 수도 있습니다는 `Startup` 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="b5f79-196">In addition to using an entirely separate `Startup` class based on the current environment, you can also make adjustments to how the application is configured within a `Startup` class.</span></span> <span data-ttu-id="b5f79-197">`Configure()` 및 `ConfigureServices()` 유사한 환경 관련 버전을 지원 하는 메서드는 `Startup` 클래스 형식의 자체 `Configure{EnvironmentName}()` 및 `Configure{EnvironmentName}Services()`합니다.</span><span class="sxs-lookup"><span data-stu-id="b5f79-197">The `Configure()` and `ConfigureServices()` methods support environment-specific versions similar to the `Startup` class itself, of the form `Configure{EnvironmentName}()` and `Configure{EnvironmentName}Services()`.</span></span> <span data-ttu-id="b5f79-198">메서드를 정의 하는 경우 `ConfigureDevelopment()` 대신 호출 됩니다 `Configure()` 개발 환경 설정 된 경우.</span><span class="sxs-lookup"><span data-stu-id="b5f79-198">If you define a method `ConfigureDevelopment()` it will be called instead of `Configure()` when the environment is set to development.</span></span> <span data-ttu-id="b5f79-199">마찬가지로, `ConfigureDevelopmentServices()` 대신 이라고 `ConfigureServices()` 동일한 환경에서 합니다.</span><span class="sxs-lookup"><span data-stu-id="b5f79-199">Likewise, `ConfigureDevelopmentServices()` would be called instead of `ConfigureServices()` in the same environment.</span></span>

## <a name="summary"></a><span data-ttu-id="b5f79-200">요약</span><span class="sxs-lookup"><span data-stu-id="b5f79-200">Summary</span></span>

<span data-ttu-id="b5f79-201">ASP.NET Core 다양 한 기능 및 다양 한 환경에서 응용 프로그램의 동작 방식을 쉽게 제어할 수 있는 규칙을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="b5f79-201">ASP.NET Core provides a number of features and conventions that allow developers to easily control how their applications behave in different environments.</span></span> <span data-ttu-id="b5f79-202">프로덕션 환경에 스테이징 개발에서 응용 프로그램을 게시할 때 환경 변수 설정 적절 하 게 환경에 대 한 허용 적절 하 게 디버깅, 테스트 또는 프로덕션 용도로 응용 프로그램의 최적화를 위해 합니다.</span><span class="sxs-lookup"><span data-stu-id="b5f79-202">When publishing an application from development to staging to production, environment variables set appropriately for the environment allow for optimization of the application for debugging, testing, or production use, as appropriate.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b5f79-203">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="b5f79-203">Additional Resources</span></span>

* [<span data-ttu-id="b5f79-204">구성</span><span class="sxs-lookup"><span data-stu-id="b5f79-204">Configuration</span></span>](configuration.md)

* [<span data-ttu-id="b5f79-205">태그 도우미 소개</span><span class="sxs-lookup"><span data-stu-id="b5f79-205">Introduction to Tag Helpers</span></span>](../mvc/views/tag-helpers/intro.md)
