---
title: "ASP.NET에서 ASP.NET Core 2.0 마이그레이션"
author: isaac2004
description: "이 참조 문서에서는 기존 ASP.NET MVC 또는 Web API 응용 프로그램을 ASP.NET Core 2.0으로 마이그레이션하기 위한 지침을 제공합니다."
keywords: "ASP.NET Core, MVC, 마이그레이션"
ms.author: scaddie
manager: wpickett
ms.date: 08/27/2017
ms.topic: article
ms.assetid: 3155cc9e-d0c9-424b-886c-35c0ec6f9f4e
ms.technology: aspnet
ms.prod: asp.net-core
uid: migration/mvc2
ms.openlocfilehash: e0691b276b63ee12d3163ac48d1392696fb97aa6
ms.sourcegitcommit: 9cdbfd0d670d70b9c354216aabee260c52dad5ee
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/12/2017
---
# <a name="migrating-from-aspnet-to-aspnet-core-20"></a><span data-ttu-id="e8b69-104">ASP.NET에서 ASP.NET Core 2.0 마이그레이션</span><span class="sxs-lookup"><span data-stu-id="e8b69-104">Migrating From ASP.NET to ASP.NET Core 2.0</span></span>

<span data-ttu-id="e8b69-105">작성자: [Isaac Levin](https://isaaclevin.com)</span><span class="sxs-lookup"><span data-stu-id="e8b69-105">By [Isaac Levin](https://isaaclevin.com)</span></span>

<span data-ttu-id="e8b69-106">이 문서는 ASP.NET 응용 프로그램을 ASP.NET Core 2.0으로 마이그레이션하기 위한 참조 가이드로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="e8b69-106">This article serves as a reference guide for migrating ASP.NET applications to ASP.NET Core 2.0.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="e8b69-107">필수 구성 요소</span><span class="sxs-lookup"><span data-stu-id="e8b69-107">Prerequisites</span></span>

* <span data-ttu-id="e8b69-108">[.NET Core 2.0.0 SDK](https://www.microsoft.com/net/core) 이상.</span><span class="sxs-lookup"><span data-stu-id="e8b69-108">[.NET Core 2.0.0 SDK](https://www.microsoft.com/net/core) or later.</span></span>

## <a name="target-frameworks"></a><span data-ttu-id="e8b69-109">대상 프레임워크</span><span class="sxs-lookup"><span data-stu-id="e8b69-109">Target Frameworks</span></span>
<span data-ttu-id="e8b69-110">ASP.NET Core 2.0 프로젝트를 사용하면 개발자가 유연하게 .NET Core, .NET Framework 또는 두 항목을 모두 대상으로 지정하거나 둘 다 대상으로 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8b69-110">ASP.NET Core 2.0 projects offer developers the flexibility of targeting .NET Core, .NET Framework, or both.</span></span> <span data-ttu-id="e8b69-111">가장 적절한 대상 프레임워크를 결정하려면 [서버 앱에 대해 .NET Core와 .NET Framework 중에서 선택](https://docs.microsoft.com/dotnet/standard/choosing-core-framework-server)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e8b69-111">See [Choosing between .NET Core and .NET Framework for server apps](https://docs.microsoft.com/dotnet/standard/choosing-core-framework-server) to determine which target framework is most appropriate.</span></span>

<span data-ttu-id="e8b69-112">.NET Framework를 대상으로 지정할 경우 프로젝트는 개별 NuGet 패키지를 참조해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8b69-112">When targeting .NET Framework, projects need to reference individual NuGet packages.</span></span>

<span data-ttu-id="e8b69-113">.NET Core를 대상으로 지정하면 ASP.NET Core 2.0 [메타패키지](xref:fundamentals/metapackage) 덕분에 수많은 명시적 패키지 참조를 제거할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8b69-113">Targeting .NET Core allows you to eliminate numerous explicit package references, thanks to the ASP.NET Core 2.0 [metapackage](xref:fundamentals/metapackage).</span></span> <span data-ttu-id="e8b69-114">프로젝트에서 `Microsoft.AspNetCore.All` 메타패키지를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="e8b69-114">Install the `Microsoft.AspNetCore.All` metapackage in your project:</span></span>

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.AspNetCore.All" Version="2.0.0" />
</ItemGroup>
```

<span data-ttu-id="e8b69-115">메타패키지를 사용하면 메타패키지에서 참조되는 패키지가 앱과 함께 배포되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e8b69-115">When the metapackage is used, no packages referenced in the metapackage are deployed with the app.</span></span> <span data-ttu-id="e8b69-116">.NET Core 런타임 저장소에는 이러한 자산이 포함되고 해당 자산은 성능을 개선하도록 미리 컴파일되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8b69-116">The .NET Core Runtime Store includes these assets, and they are precompiled to improve performance.</span></span> <span data-ttu-id="e8b69-117">자세한 내용은 [ASP.NET Core 2.x에 대한 Microsoft.AspNetCore.All 메타패키지](xref:fundamentals/metapackage)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e8b69-117">See [Microsoft.AspNetCore.All metapackage for ASP.NET Core 2.x](xref:fundamentals/metapackage) for more detail.</span></span>

## <a name="project-structure-differences"></a><span data-ttu-id="e8b69-118">프로젝트 구조 차이점</span><span class="sxs-lookup"><span data-stu-id="e8b69-118">Project structure differences</span></span>
<span data-ttu-id="e8b69-119">*.csproj* 파일 형식은 ASP.NET Core에서 간소화되었습니다.</span><span class="sxs-lookup"><span data-stu-id="e8b69-119">The *.csproj* file format has been simplified in ASP.NET Core.</span></span> <span data-ttu-id="e8b69-120">몇 가지 주요 변경 내용은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="e8b69-120">Some notable changes include:</span></span>
- <span data-ttu-id="e8b69-121">파일을 프로젝트의 일부로 간주하기 위해 파일을 명시적으로 포함할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="e8b69-121">Explicit inclusion of files is not necessary for them to be considered part of the project.</span></span> <span data-ttu-id="e8b69-122">이 덕분에 대규모 팀에서 작업할 때 XML 병합 충돌의 위험이 감소합니다.</span><span class="sxs-lookup"><span data-stu-id="e8b69-122">This reduces the risk of XML merge conflicts when working on large teams.</span></span>
- <span data-ttu-id="e8b69-123">다른 프로젝트에 대한 GUID 기반 참조가 없으므로 파일 가독성이 향상됩니다.</span><span class="sxs-lookup"><span data-stu-id="e8b69-123">There are no GUID-based references to other projects, which improves file readability.</span></span>
- <span data-ttu-id="e8b69-124">Visual Studio에서 파일을 언로드하지 않고 파일을 편집할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8b69-124">The file can be edited without unloading it in Visual Studio:</span></span>

    ![Visual Studio 2017에서 CSPROJ 상황에 맞는 메뉴 옵션 편집](_static/EditProjectVs2017.png)

## <a name="globalasax-file-replacement"></a><span data-ttu-id="e8b69-126">Global.asax 파일 바꾸기</span><span class="sxs-lookup"><span data-stu-id="e8b69-126">Global.asax file replacement</span></span>
<span data-ttu-id="e8b69-127">ASP.NET Core에는 앱을 부트스트랩하기 위한 새로운 메커니즘이 도입되었습니다.</span><span class="sxs-lookup"><span data-stu-id="e8b69-127">ASP.NET Core introduced a new mechanism for bootstrapping an app.</span></span> <span data-ttu-id="e8b69-128">ASP.NET 응용 프로그램의 진입점은 *Global.asax* 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="e8b69-128">The entry point for ASP.NET applications is the *Global.asax* file.</span></span> <span data-ttu-id="e8b69-129">경로 구성 및 필터/영역 등록과 같은 작업은 *Global.asax* 파일에서 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="e8b69-129">Tasks such as route configuration and filter and area registrations are handled in the *Global.asax* file.</span></span>

<span data-ttu-id="e8b69-130">[!code-csharp[Main](samples/globalasax-sample.cs)]</span><span class="sxs-lookup"><span data-stu-id="e8b69-130">[!code-csharp[Main](samples/globalasax-sample.cs)]</span></span>

<span data-ttu-id="e8b69-131">이 방법은 구현을 방해하는 방식으로 응용 프로그램 및 응용 프로그램이 배포되는 서버를 결합합니다.</span><span class="sxs-lookup"><span data-stu-id="e8b69-131">This approach couples the application and the server to which it's deployed in a way that interferes with the implementation.</span></span> <span data-ttu-id="e8b69-132">분리 작업 시 여러 프레임워크를 함께 사용할 수 있는 더 분명한 방식을 제공하도록 [OWIN](http://owin.org/)이 도입되었습니다.</span><span class="sxs-lookup"><span data-stu-id="e8b69-132">In an effort to decouple, [OWIN](http://owin.org/) was introduced to provide a cleaner way to use multiple frameworks together.</span></span> <span data-ttu-id="e8b69-133">OWIN은 필요한 모듈만 추가하기 위한 파이프라인을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="e8b69-133">OWIN provides a pipeline to add only the modules needed.</span></span> <span data-ttu-id="e8b69-134">호스팅 환경에서는 [Startup](xref:fundamentals/startup) 함수를 사용하여 서비스 및 앱 요청 파이프라인을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="e8b69-134">The hosting environment takes a [Startup](xref:fundamentals/startup) function to configure services and the app's request pipeline.</span></span> <span data-ttu-id="e8b69-135">`Startup`은 미들웨어 집합을 응용 프로그램에 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="e8b69-135">`Startup` registers a set of middleware with the application.</span></span> <span data-ttu-id="e8b69-136">각 요청에 대해 응용 프로그램은 기존 처리기 집합에 대한 연결된 목록의 head 포인터를 사용하여 각 미들웨어 구성 요소를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="e8b69-136">For each request, the application calls each of the the middleware components with the head pointer of a linked list to an existing set of handlers.</span></span> <span data-ttu-id="e8b69-137">각 미들웨어 구성 요소는 요청 처리 파이프라인에 하나 이상의 처리기를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8b69-137">Each middleware component can add one or more handlers to the request handling pipeline.</span></span> <span data-ttu-id="e8b69-138">이 작업을 수행하려면 목록의 새 헤드인 처리기에 참조를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="e8b69-138">This is accomplished by returning a reference to the handler that is the new head of the list.</span></span> <span data-ttu-id="e8b69-139">각 처리기는 목록의 다음 처리기를 기억하고 호출해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8b69-139">Each handler is responsible for remembering and invoking the next handler in the list.</span></span> <span data-ttu-id="e8b69-140">ASP.NET Core에서는 응용 프로그램에 대한 진입점이 `Startup`이고 더 이상 *Global.asax*에 대한 종속성을 포함하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e8b69-140">With ASP.NET Core, the entry point to an application is `Startup`, and you no longer have a dependency on *Global.asax*.</span></span> <span data-ttu-id="e8b69-141">.NET Framework에서 OWIN을 사용할 경우 다음과 같은 항목을 파이프라인으로 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="e8b69-141">When using OWIN with .NET Framework, use something like the following as a pipeline:</span></span>

<span data-ttu-id="e8b69-142">[!code-csharp[Main](samples/webapi-owin.cs)]</span><span class="sxs-lookup"><span data-stu-id="e8b69-142">[!code-csharp[Main](samples/webapi-owin.cs)]</span></span>

<span data-ttu-id="e8b69-143">이렇게 하면 기본 경로가 구성되고 기본적으로 JSON을 통한 XmlSerialization으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="e8b69-143">This configures your default routes, and defaults to XmlSerialization over Json.</span></span> <span data-ttu-id="e8b69-144">필요에 따라 이 파이프라인에 다른 미들웨어를 추가합니다(로딩 서비스, 구성 설정, 정적 파일 등).</span><span class="sxs-lookup"><span data-stu-id="e8b69-144">Add other Middleware to this pipeline as needed (loading services, configuration settings, static files, etc.).</span></span>

<span data-ttu-id="e8b69-145">ASP.NET Core는 비슷한 방법을 사용하지만 항목을 처리하는 데 OWIN을 사용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e8b69-145">ASP.NET Core uses a similar approach, but doesn't rely on OWIN to handle the entry.</span></span> <span data-ttu-id="e8b69-146">대신에 이 작업에는 *Program.cs* `Main` 메서드(콘솔 응용 프로그램과 비슷함)가 사용되고 `Startup`도 이 작업을 통해 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="e8b69-146">Instead, that is done through the *Program.cs* `Main` method (similar to console applications) and `Startup` is loaded through there.</span></span>

<span data-ttu-id="e8b69-147">[!code-csharp[Main](samples/program.cs)]</span><span class="sxs-lookup"><span data-stu-id="e8b69-147">[!code-csharp[Main](samples/program.cs)]</span></span>

<span data-ttu-id="e8b69-148">`Startup`에는 `Configure` 메서드가 포함되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8b69-148">`Startup` must include a `Configure` method.</span></span> <span data-ttu-id="e8b69-149">`Configure`에서 파이프라인에 필요한 미들웨어를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="e8b69-149">In `Configure`, add the necessary middleware to the pipeline.</span></span> <span data-ttu-id="e8b69-150">기본 웹 사이트 템플릿을 기반으로 한 다음 예제에서는 여러 확장 메서드를 사용하여 다음 지원을 통해 파이프라인을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="e8b69-150">In the following example (from the default web site template), several extension methods are used to configure the pipeline with support for:</span></span>

* [<span data-ttu-id="e8b69-151">BrowserLink</span><span class="sxs-lookup"><span data-stu-id="e8b69-151">BrowserLink</span></span>](http://vswebessentials.com/features/browserlink)
* <span data-ttu-id="e8b69-152">오류 페이지</span><span class="sxs-lookup"><span data-stu-id="e8b69-152">Error pages</span></span>
* <span data-ttu-id="e8b69-153">정적 파일</span><span class="sxs-lookup"><span data-stu-id="e8b69-153">Static files</span></span>
* <span data-ttu-id="e8b69-154">ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="e8b69-154">ASP.NET Core MVC</span></span>
* <span data-ttu-id="e8b69-155">ID</span><span class="sxs-lookup"><span data-stu-id="e8b69-155">Identity</span></span>

<span data-ttu-id="e8b69-156">[!code-csharp[Main](../../common/samples/WebApplication1/Startup.cs?highlight=8,9,10,14,17,19,21&start=58&end=84)]</span><span class="sxs-lookup"><span data-stu-id="e8b69-156">[!code-csharp[Main](../../common/samples/WebApplication1/Startup.cs?highlight=8,9,10,14,17,19,21&start=58&end=84)]</span></span>

<span data-ttu-id="e8b69-157">호스트와 응용 프로그램이 분리되었으므로 나중에 유연하게 다른 플랫폼으로 이동할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8b69-157">The host and application have been decoupled, which provides the flexibility of moving to a different platform in the future.</span></span>

<span data-ttu-id="e8b69-158">**참고:** ASP.NET Core 시작 및 미들웨어에 대한 자세한 내용은 [ASP.NET Core의 시작](xref:fundamentals/startup)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e8b69-158">**Note:** For a more in-depth reference to ASP.NET Core Startup and Middleware, see [Startup in ASP.NET Core](xref:fundamentals/startup)</span></span>

## <a name="storing-configurations"></a><span data-ttu-id="e8b69-159">구성 저장</span><span class="sxs-lookup"><span data-stu-id="e8b69-159">Storing Configurations</span></span>
<span data-ttu-id="e8b69-160">ASP.NET은 정렬 설정을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="e8b69-160">ASP.NET supports storing settings.</span></span> <span data-ttu-id="e8b69-161">예를 들어 이러한 설정은 응용 프로그램이 배포된 환경을 지원하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="e8b69-161">These setting are used, for example, to support the environment to which the applications were deployed.</span></span> <span data-ttu-id="e8b69-162">일반적으로 모든 사용자 지정 키 값 쌍은 *Web.config* 파일의 `<appSettings>` 섹션에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="e8b69-162">A common practice was to store all custom key-value pairs in the `<appSettings>` section of the *Web.config* file:</span></span>

<span data-ttu-id="e8b69-163">[!code-xml[Main](samples/webconfig-sample.xml)]</span><span class="sxs-lookup"><span data-stu-id="e8b69-163">[!code-xml[Main](samples/webconfig-sample.xml)]</span></span>

<span data-ttu-id="e8b69-164">응용 프로그램은 `System.Configuration` 네임스페이스의 `ConfigurationManager.AppSettings` 컬렉션을 사용하여 이러한 설정을 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="e8b69-164">Applications read these settings using the `ConfigurationManager.AppSettings` collection in the `System.Configuration` namespace:</span></span>

<span data-ttu-id="e8b69-165">[!code-csharp[Main](samples/read-webconfig.cs)]</span><span class="sxs-lookup"><span data-stu-id="e8b69-165">[!code-csharp[Main](samples/read-webconfig.cs)]</span></span>

<span data-ttu-id="e8b69-166">ASP.NET Core는 응용 프로그램에 대한 구성 데이터를 파일에 저장하고 미들웨어 부트스트래핑의 일부로 로드할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8b69-166">ASP.NET Core can store configuration data for the application in any file and load them as part of middleware bootstrapping.</span></span> <span data-ttu-id="e8b69-167">프로젝트 템플릿에 사용되는 기본 파일은 *appsettings.json*입니다.</span><span class="sxs-lookup"><span data-stu-id="e8b69-167">The default file used in the project templates is *appsettings.json*:</span></span>

<span data-ttu-id="e8b69-168">[!code-json[Main](samples/appsettings-sample.json)]</span><span class="sxs-lookup"><span data-stu-id="e8b69-168">[!code-json[Main](samples/appsettings-sample.json)]</span></span>

<span data-ttu-id="e8b69-169">파일을 응용 프로그램 내부의 `IConfiguration` 인스턴스로 로드하는 작업은 *Startup.cs*에서 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="e8b69-169">Loading this file into an instance of `IConfiguration` inside your application is done in *Startup.cs*:</span></span>

<span data-ttu-id="e8b69-170">[!code-csharp[Main](samples/startup-builder.cs)]</span><span class="sxs-lookup"><span data-stu-id="e8b69-170">[!code-csharp[Main](samples/startup-builder.cs)]</span></span>

<span data-ttu-id="e8b69-171">앱은 `Configuration`을 읽어서 설정을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="e8b69-171">The app reads from `Configuration` to get the settings:</span></span>

<span data-ttu-id="e8b69-172">[!code-csharp[Main](samples/read-appsettings.cs)]</span><span class="sxs-lookup"><span data-stu-id="e8b69-172">[!code-csharp[Main](samples/read-appsettings.cs)]</span></span>

<span data-ttu-id="e8b69-173">DI([종속성 주입](xref:fundamentals/dependency-injection))를 사용하여 이러한 값으로 서비스를 로드하는 것과 같이 이 방법을 확장하여 프로세스를 더 강력하게 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8b69-173">There are extensions to this approach to make the process more robust, such as using [Dependency Injection](xref:fundamentals/dependency-injection) (DI) to load a service with these values.</span></span> <span data-ttu-id="e8b69-174">DI 방법은 강력한 형식의 구성 개체 집합을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="e8b69-174">The DI approach provides a strongly-typed set of configuration objects.</span></span>

````csharp
// Assume AppConfiguration is a class representing a strongly-typed version of AppConfiguration section
services.Configure<AppConfiguration>(Configuration.GetSection("AppConfiguration"));
````

<span data-ttu-id="e8b69-175">**참고:** ASP.NET Core 구성에 대한 자세한 내용은 [ASP.NET Core의 구성](xref:fundamentals/configuration)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e8b69-175">**Note:** For a more in-depth reference to ASP.NET Core configuration, see [Configuration in ASP.NET Core](xref:fundamentals/configuration).</span></span>

## <a name="native-dependency-injection"></a><span data-ttu-id="e8b69-176">네이티브 종속성 주입</span><span class="sxs-lookup"><span data-stu-id="e8b69-176">Native Dependency Injection</span></span>
<span data-ttu-id="e8b69-177">크고 확장 가능한 응용 프로그램을 빌드할 때 중요한 목표는 구성 요소와 서비스를 느슨하게 결합하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="e8b69-177">An important goal when building large, scalable applications is the loose coupling of components and services.</span></span> <span data-ttu-id="e8b69-178">[종속성 주입](xref:fundamentals/dependency-injection)은 이 목표를 위해 널리 사용되는 기술이고 ASP.NET Core의 네이티브 구성 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="e8b69-178">[Dependency Injection](xref:fundamentals/dependency-injection) is a popular technique for achieving this, and it is a native component of ASP.NET Core.</span></span>

<span data-ttu-id="e8b69-179">ASP.NET 응용 프로그램에서 개발자는 타사 라이브러리를 사용하여 종속성 주입을 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="e8b69-179">In ASP.NET applications, developers rely on a third-party library to implement Dependency Injection.</span></span> <span data-ttu-id="e8b69-180">이러한 라이브러리 중 하나는 Microsoft Patterns & Practices에서 제공하는 [Unity](https://github.com/unitycontainer/unity)입니다.</span><span class="sxs-lookup"><span data-stu-id="e8b69-180">One such library is [Unity](https://github.com/unitycontainer/unity), provided by Microsoft Patterns & Practices.</span></span> 

<span data-ttu-id="e8b69-181">Unity를 사용한 종속성 주입 설정의 예로는 `UnityContainer`를 래핑하는 `IDependencyResolver`를 구현하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="e8b69-181">An example of setting up Dependency Injection with Unity is implementing `IDependencyResolver` that wraps a `UnityContainer`:</span></span>

<span data-ttu-id="e8b69-182">[!code-csharp[Main](../../../aspnet/web-api/overview/advanced/dependency-injection/samples/sample8.cs)]</span><span class="sxs-lookup"><span data-stu-id="e8b69-182">[!code-csharp[Main](../../../aspnet/web-api/overview/advanced/dependency-injection/samples/sample8.cs)]</span></span>

<span data-ttu-id="e8b69-183">`UnityContainer`의 인스턴스를 만들고, 서비스를 등록하고, `HttpConfiguration`의 종속성 확인자를 컨테이너에 대한 `UnityResolver`의 새 인스턴스로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e8b69-183">Create an instance of your `UnityContainer`, register your service, and set the dependency resolver of `HttpConfiguration` to the new instance of `UnityResolver` for your container:</span></span>

<span data-ttu-id="e8b69-184">[!code-csharp[Main](../../../aspnet/web-api/overview/advanced/dependency-injection/samples/sample9.cs)]</span><span class="sxs-lookup"><span data-stu-id="e8b69-184">[!code-csharp[Main](../../../aspnet/web-api/overview/advanced/dependency-injection/samples/sample9.cs)]</span></span>

<span data-ttu-id="e8b69-185">필요한 경우 `IProductRepository`를 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="e8b69-185">Inject `IProductRepository` where needed:</span></span>

<span data-ttu-id="e8b69-186">[!code-csharp[Main](../../../aspnet/web-api/overview/advanced/dependency-injection/samples/sample5.cs)]</span><span class="sxs-lookup"><span data-stu-id="e8b69-186">[!code-csharp[Main](../../../aspnet/web-api/overview/advanced/dependency-injection/samples/sample5.cs)]</span></span>

<span data-ttu-id="e8b69-187">종속성 주입은 ASP.NET Core의 일부이므로 *Startup.cs*의 `ConfigureServices` 메서드에서 서비스를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8b69-187">Because Dependency Injection is part of ASP.NET Core, you can add your service in the `ConfigureServices` method of *Startup.cs*:</span></span>

<span data-ttu-id="e8b69-188">[!code-csharp[Main](samples/configure-services.cs)]</span><span class="sxs-lookup"><span data-stu-id="e8b69-188">[!code-csharp[Main](samples/configure-services.cs)]</span></span>

<span data-ttu-id="e8b69-189">Unity에서 삽입한 것처럼 리포지토리는 어디든지 삽입될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8b69-189">The repository can be injected anywhere, as was true with Unity.</span></span>

<span data-ttu-id="e8b69-190">**참고:** ASP.NET Core의 종속성 주입에 대한 자세한 내용은 [ASP.NET Core의 종속성 주입](xref:fundamentals/dependency-injection#replacing-the-default-services-container)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e8b69-190">**Note:** For an in-depth reference to dependency injection in ASP.NET Core, see [Dependency Injection in ASP.NET Core](xref:fundamentals/dependency-injection#replacing-the-default-services-container)</span></span>

## <a name="serving-static-files"></a><span data-ttu-id="e8b69-191">정적 파일 지원</span><span class="sxs-lookup"><span data-stu-id="e8b69-191">Serving Static Files</span></span>
<span data-ttu-id="e8b69-192">웹 개발의 중요한 부분은 정적 클라이언트 쪽 자산을 지원하는 기능입니다.</span><span class="sxs-lookup"><span data-stu-id="e8b69-192">An important part of web development is the ability to serve static, client-side assets.</span></span> <span data-ttu-id="e8b69-193">정적 파일의 가장 일반적인 예로는 HTML, CSS, Javascript 및 이미지가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8b69-193">The most common examples of static files are HTML, CSS, Javascript, and images.</span></span> <span data-ttu-id="e8b69-194">이러한 파일은 앱(또는 CDN)의 게시된 위치에 저장되고 요청을 통해 로드될 수 있도록 참조되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8b69-194">These files need to be saved in the published location of the app (or CDN) and referenced so they can be loaded by a request.</span></span> <span data-ttu-id="e8b69-195">이 프로세스는 ASP.NET Core에서 변경되었습니다.</span><span class="sxs-lookup"><span data-stu-id="e8b69-195">This process has changed in ASP.NET Core.</span></span>

<span data-ttu-id="e8b69-196">ASP.NET에서 정적 파일은 다양한 디렉터리에 저장되고 뷰에서 참조됩니다.</span><span class="sxs-lookup"><span data-stu-id="e8b69-196">In ASP.NET, static files are stored in various directories and referenced in the views.</span></span>

<span data-ttu-id="e8b69-197">ASP.NET Core에서 정적 파일은 별도로 구성되지 않는 한 “웹 루트”(*&lt;content root&gt;/wwwroot*)에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="e8b69-197">In ASP.NET Core, static files are stored in the "web root" (*&lt;content root&gt;/wwwroot*), unless configured otherwise.</span></span> <span data-ttu-id="e8b69-198">파일은 `Startup.Configure`에서 `UseStaticFiles` 확장 메서드를 호출하는 방식으로 요청 파이프라인에 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="e8b69-198">The files are loaded into the request pipeline by invoking the `UseStaticFiles` extension method from `Startup.Configure`:</span></span>

<span data-ttu-id="e8b69-199">[!code-csharp[Main](../../fundamentals/static-files/sample/StartupStaticFiles.cs?highlight=3&name=snippet1)]</span><span class="sxs-lookup"><span data-stu-id="e8b69-199">[!code-csharp[Main](../../fundamentals/static-files/sample/StartupStaticFiles.cs?highlight=3&name=snippet1)]</span></span>

<span data-ttu-id="e8b69-200">**참고:** .NET Framework를 대상으로 지정할 경우 NuGet 패키지 `Microsoft.AspNetCore.StaticFiles`를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="e8b69-200">**Note:** If targeting .NET Framework, install the NuGet package `Microsoft.AspNetCore.StaticFiles`.</span></span>

<span data-ttu-id="e8b69-201">예를 들어 *wwwroot/images* 폴더의 이미지 자산은 `http://<app>/images/<imageFileName>`과 같은 위치의 브라우저에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8b69-201">For example, an image asset in the *wwwroot/images* folder is accessible to the browser at a location such as `http://<app>/images/<imageFileName>`.</span></span>

<span data-ttu-id="e8b69-202">**참고:** ASP.NET Core의 정적 파일 지원에 대한 자세한 내용은 [ASP.NET Core에서 정적 파일 사용 소개](xref:fundamentals/static-files)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e8b69-202">**Note:** For a more in-depth reference to serving static files in ASP.NET Core, see [Introduction to working with static files in ASP.NET Core](xref:fundamentals/static-files).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e8b69-203">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="e8b69-203">Additional Resources</span></span>
* [<span data-ttu-id="e8b69-204">.NET Core로 라이브러리 이식</span><span class="sxs-lookup"><span data-stu-id="e8b69-204">Porting Libraries to .NET Core</span></span>](https://docs.microsoft.com/dotnet/core/porting/libraries)
