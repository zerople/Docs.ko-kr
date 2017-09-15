---
title: "ASP.NET Core 1.x에서 2.0으로 마이그레이션"
author: scottaddie
description: "이 문서에서는 ASP.NET Core 1.x 프로젝트에서 ASP.NET Core 2.0으로 마이그레이션하기 위한 필수 구성 요소 및 일반적인 단계를 설명합니다."
keywords: "ASP.NET Core, 마이그레이션"
ms.author: scaddie
manager: wpickett
ms.date: 08/01/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: migration/1x-to-2x/index
ms.openlocfilehash: c14e7d61e8b353c18fc4a4f2bf3658069982bad5
ms.sourcegitcommit: e832a9b9f41a8b26a8c88edfd8fc35b8bfd97d5d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2017
---
# <a name="migrating-from-aspnet-core-1x-to-aspnet-core-20"></a><span data-ttu-id="347e6-104">ASP.NET Core 1.x에서 ASP.NET Core 2.0으로 마이그레이션</span><span class="sxs-lookup"><span data-stu-id="347e6-104">Migrating from ASP.NET Core 1.x to ASP.NET Core 2.0</span></span>

<span data-ttu-id="347e6-105">작성자: [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="347e6-105">By [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="347e6-106">이 문서에서는 기존 ASP.NET Core 1.x 프로젝트를 ASP.NET Core 2.0으로 업데이트하는 과정을 안내합니다.</span><span class="sxs-lookup"><span data-stu-id="347e6-106">In this article, we'll walk you through updating an existing ASP.NET Core 1.x project to ASP.NET Core 2.0.</span></span> <span data-ttu-id="347e6-107">응용 프로그램을 ASP.NET Core 2.0으로 마이그레이션하면 [여러 가지 새로운 기능 및 향상된 성능](https://go.microsoft.com/fwlink/?linkid=854094)을 활용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="347e6-107">Migrating your application to ASP.NET Core 2.0 enables you to take advantage of [many new features and performance improvements](https://go.microsoft.com/fwlink/?linkid=854094).</span></span> 

<span data-ttu-id="347e6-108">기존 ASP.NET Core 1.x 응용 프로그램은 버전별 프로젝트 템플릿을 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="347e6-108">Existing ASP.NET Core 1.x applications are based off of version-specific project templates.</span></span> <span data-ttu-id="347e6-109">ASP.NET Core 프레임워크가 진화함에 따라 프로젝트 템플릿 및 포함된 시작 코드도 함께 진화합니다.</span><span class="sxs-lookup"><span data-stu-id="347e6-109">As the ASP.NET Core framework evolves, so do the project templates and the starter code contained within them.</span></span> <span data-ttu-id="347e6-110">ASP.NET Core 프레임워크를 업데이트하는 것 외에도 응용 프로그램에 대한 코드를 업데이트해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="347e6-110">In addition to updating the ASP.NET Core framework, you need to update the code for your application.</span></span>

<a name="prerequisites"></a>

## <a name="prerequisites"></a><span data-ttu-id="347e6-111">필수 구성 요소</span><span class="sxs-lookup"><span data-stu-id="347e6-111">Prerequisites</span></span>
<span data-ttu-id="347e6-112">[ASP.NET Core 시작](xref:getting-started)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="347e6-112">Please see [Getting Started with ASP.NET Core](xref:getting-started).</span></span>

<a name="tfm"></a>

## <a name="update-target-framework-moniker-tfm"></a><span data-ttu-id="347e6-113">TFM(대상 프레임워크 모니커) 업데이트</span><span class="sxs-lookup"><span data-stu-id="347e6-113">Update Target Framework Moniker (TFM)</span></span>
<span data-ttu-id="347e6-114">.NET Core를 대상으로 하는 프로젝트는 .NET Core 2.0보다 크거나 같은 버전의 [TFM](/dotnet/standard/frameworks#referring-to-frameworks)을 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="347e6-114">Projects targeting .NET Core should use the [TFM](/dotnet/standard/frameworks#referring-to-frameworks) of a version greater than or equal to .NET Core 2.0.</span></span> <span data-ttu-id="347e6-115">*.csproj* 파일에서 `<TargetFramework>` 노드를 검색하고 해당 내부 텍스트를 `netcoreapp2.0`으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="347e6-115">Search for the `<TargetFramework>` node in the *.csproj* file, and replace its inner text with `netcoreapp2.0`:</span></span>

<span data-ttu-id="347e6-116">[!code-xml[Main](../1x-to-2x/samples/AspNetCoreDotNetCore2.0App/AspNetCoreDotNetCore2.0App/AspNetCoreDotNetCore2.0App.csproj?range=3)]</span><span class="sxs-lookup"><span data-stu-id="347e6-116">[!code-xml[Main](../1x-to-2x/samples/AspNetCoreDotNetCore2.0App/AspNetCoreDotNetCore2.0App/AspNetCoreDotNetCore2.0App.csproj?range=3)]</span></span>

<span data-ttu-id="347e6-117">.NET Framework를 대상으로 하는 프로젝트는 .NET Framework 4.6.1보다 크거나 같은 버전의 TFM을 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="347e6-117">Projects targeting .NET Framework should use the TFM of a version greater than or equal to .NET Framework 4.6.1.</span></span> <span data-ttu-id="347e6-118">*.csproj* 파일에서 `<TargetFramework>` 노드를 검색하고 해당 내부 텍스트를 `net461`로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="347e6-118">Search for the `<TargetFramework>` node in the *.csproj* file, and replace its inner text with `net461`:</span></span>

<span data-ttu-id="347e6-119">[!code-xml[Main](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App.csproj?range=4)]</span><span class="sxs-lookup"><span data-stu-id="347e6-119">[!code-xml[Main](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App.csproj?range=4)]</span></span>

> [!NOTE]
> <span data-ttu-id="347e6-120">.NET Core 2.0은 .NET Core 1.x보다 훨씬 더 큰 노출 영역을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="347e6-120">.NET Core 2.0 offers a much larger surface area than .NET Core 1.x.</span></span> <span data-ttu-id="347e6-121">.NET Core 1.x에 API가 없기 때문에 전적으로 .NET Framework를 대상으로 하는 경우 .NET Core 2.0을 대상으로 하는 것은 작동할 가능성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="347e6-121">If you're targeting .NET Framework solely because of missing APIs in .NET Core 1.x, targeting .NET Core 2.0 is likely to work.</span></span>

<a name="global-json"></a>

## <a name="update-net-core-sdk-version-in-globaljson"></a><span data-ttu-id="347e6-122">global.json에서 .NET Core SDK 버전 업데이트</span><span class="sxs-lookup"><span data-stu-id="347e6-122">Update .NET Core SDK version in global.json</span></span>
<span data-ttu-id="347e6-123">솔루션이 특정 .NET Core SDK 버전을 대상으로 하기 위해 [*global.json*](https://docs.microsoft.com/dotnet/core/tools/global-json) 파일에 의존하는 경우 해당 `version` 속성을 컴퓨터에 설치된 2.0 버전을 사용하도록 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="347e6-123">If your solution relies upon a [*global.json*](https://docs.microsoft.com/dotnet/core/tools/global-json) file to target a specific .NET Core SDK version, update its `version` property to use the 2.0 version installed on your machine:</span></span>

<span data-ttu-id="347e6-124">[!code-json[Main](../1x-to-2x/samples/AspNetCoreDotNetCore2.0App/global.json?highlight=3)]</span><span class="sxs-lookup"><span data-stu-id="347e6-124">[!code-json[Main](../1x-to-2x/samples/AspNetCoreDotNetCore2.0App/global.json?highlight=3)]</span></span>

<a name="package-reference"></a>

## <a name="update-package-references"></a><span data-ttu-id="347e6-125">패키지 참조 업데이트</span><span class="sxs-lookup"><span data-stu-id="347e6-125">Update package references</span></span>
<span data-ttu-id="347e6-126">1.x 프로젝트에서 *.csproj* 파일은 프로젝트에서 사용하는 각 NuGet 패키지를 나열합니다.</span><span class="sxs-lookup"><span data-stu-id="347e6-126">The *.csproj* file in a 1.x project lists each NuGet package used by the project.</span></span>

<span data-ttu-id="347e6-127">.NET Core 2.0을 대상으로 하는 ASP.NET Core 2.0 프로젝트에서 *.csproj* 파일의 단일 [metapackage](xref:fundamentals/metapackage) 참조는 패키지의 컬렉션을 대체합니다.</span><span class="sxs-lookup"><span data-stu-id="347e6-127">In an ASP.NET Core 2.0 project targeting .NET Core 2.0, a single [metapackage](xref:fundamentals/metapackage) reference in the *.csproj* file replaces the collection of packages:</span></span>

<span data-ttu-id="347e6-128">[!code-xml[Main](../1x-to-2x/samples/AspNetCoreDotNetCore2.0App/AspNetCoreDotNetCore2.0App/AspNetCoreDotNetCore2.0App.csproj?range=9-11)]</span><span class="sxs-lookup"><span data-stu-id="347e6-128">[!code-xml[Main](../1x-to-2x/samples/AspNetCoreDotNetCore2.0App/AspNetCoreDotNetCore2.0App/AspNetCoreDotNetCore2.0App.csproj?range=9-11)]</span></span>

<span data-ttu-id="347e6-129">ASP.NET Core 2.0 및 Entity Framework Core 2.0의 모든 기능은 metapackage에 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="347e6-129">All the features of ASP.NET Core 2.0 and Entity Framework Core 2.0 are included in the metapackage.</span></span>

<span data-ttu-id="347e6-130">.NET Framework를 대상으로 하는 ASP.NET Core 2.0 프로젝트는 계속해서 개별 NuGet 패키지를 참조해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="347e6-130">ASP.NET Core 2.0 projects targeting .NET Framework should continue to reference individual NuGet packages.</span></span> <span data-ttu-id="347e6-131">각 `<PackageReference />` 노드의 `Version` 특성을 2.0.0으로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="347e6-131">Update the `Version` attribute of each `<PackageReference />` node to 2.0.0.</span></span>

<span data-ttu-id="347e6-132">예를 들어 다음은 .NET Framework를 대상으로 하는 일반적인 ASP.NET Core 2.0 프로젝트에 사용되는 `<PackageReference />` 노드의 목록입니다.</span><span class="sxs-lookup"><span data-stu-id="347e6-132">For example, here's the list of `<PackageReference />` nodes used in a typical ASP.NET Core 2.0 project targeting .NET Framework:</span></span>

<span data-ttu-id="347e6-133">[!code-xml[Main](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App.csproj?range=9-22)]</span><span class="sxs-lookup"><span data-stu-id="347e6-133">[!code-xml[Main](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App.csproj?range=9-22)]</span></span>

<a name="dot-net-cli-tool-reference"></a>

## <a name="update-net-core-cli-tools"></a><span data-ttu-id="347e6-134">.NET Core CLI 도구 업데이트</span><span class="sxs-lookup"><span data-stu-id="347e6-134">Update .NET Core CLI tools</span></span>
<span data-ttu-id="347e6-135">*.csproj* 파일에서 각 `<DotNetCliToolReference />` 노드의 `Version` 특성을 2.0.0으로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="347e6-135">In the *.csproj* file, update the `Version` attribute of each `<DotNetCliToolReference />` node to 2.0.0.</span></span>

<span data-ttu-id="347e6-136">예를 들어 다음은 .NET Core 2.0을 대상으로 하는 일반적인 ASP.NET Core 2.0 프로젝트에 사용되는 CLI 도구의 목록입니다.</span><span class="sxs-lookup"><span data-stu-id="347e6-136">For example, here's the list of CLI tools used in a typical ASP.NET Core 2.0 project targeting .NET Core 2.0:</span></span>

<span data-ttu-id="347e6-137">[!code-xml[Main](../1x-to-2x/samples/AspNetCoreDotNetCore2.0App/AspNetCoreDotNetCore2.0App/AspNetCoreDotNetCore2.0App.csproj?range=13-17)]</span><span class="sxs-lookup"><span data-stu-id="347e6-137">[!code-xml[Main](../1x-to-2x/samples/AspNetCoreDotNetCore2.0App/AspNetCoreDotNetCore2.0App/AspNetCoreDotNetCore2.0App.csproj?range=13-17)]</span></span>

<a name="package-target-fallback"></a>

## <a name="rename-package-target-fallback-property"></a><span data-ttu-id="347e6-138">패키지 대상 대체 속성의 이름 바꾸기</span><span class="sxs-lookup"><span data-stu-id="347e6-138">Rename Package Target Fallback property</span></span>
<span data-ttu-id="347e6-139">`PackageTargetFallback` 노드 및 변수에서 사용되는 1.x 프로젝트의 *.csproj* 파일:</span><span class="sxs-lookup"><span data-stu-id="347e6-139">The *.csproj* file of a 1.x project used a `PackageTargetFallback` node and variable:</span></span>

<span data-ttu-id="347e6-140">[!code-xml[Main](../1x-to-2x/samples/AspNetCoreDotNetCore1.1App/AspNetCoreDotNetCore1.1App/AspNetCoreDotNetCore1.1App.csproj?range=5)]</span><span class="sxs-lookup"><span data-stu-id="347e6-140">[!code-xml[Main](../1x-to-2x/samples/AspNetCoreDotNetCore1.1App/AspNetCoreDotNetCore1.1App/AspNetCoreDotNetCore1.1App.csproj?range=5)]</span></span>

<span data-ttu-id="347e6-141">노드 및 변수의 이름을 모두 `AssetTargetFallback`으로 바꾸기:</span><span class="sxs-lookup"><span data-stu-id="347e6-141">Rename both the node and variable to `AssetTargetFallback`:</span></span>

<span data-ttu-id="347e6-142">[!code-xml[Main](../1x-to-2x/samples/AspNetCoreDotNetCore2.0App/AspNetCoreDotNetCore2.0App/AspNetCoreDotNetCore2.0App.csproj?range=5)]</span><span class="sxs-lookup"><span data-stu-id="347e6-142">[!code-xml[Main](../1x-to-2x/samples/AspNetCoreDotNetCore2.0App/AspNetCoreDotNetCore2.0App/AspNetCoreDotNetCore2.0App.csproj?range=5)]</span></span>

<a name="program-cs"></a>

## <a name="update-main-method-in-programcs"></a><span data-ttu-id="347e6-143">Program.cs의 Main 메서드 업데이트</span><span class="sxs-lookup"><span data-stu-id="347e6-143">Update Main method in Program.cs</span></span>
<span data-ttu-id="347e6-144">1.x 프로젝트에서 *Program.cs*의 `Main` 메서드는 다음과 같았습니다.</span><span class="sxs-lookup"><span data-stu-id="347e6-144">In 1.x projects, the `Main` method of *Program.cs* looked like this:</span></span>

<span data-ttu-id="347e6-145">[!code-csharp[Main](../1x-to-2x/samples/AspNetCoreDotNetCore1.1App/AspNetCoreDotNetCore1.1App/Program.cs?name=snippet_ProgramCs&highlight=8-19)]</span><span class="sxs-lookup"><span data-stu-id="347e6-145">[!code-csharp[Main](../1x-to-2x/samples/AspNetCoreDotNetCore1.1App/AspNetCoreDotNetCore1.1App/Program.cs?name=snippet_ProgramCs&highlight=8-19)]</span></span>

<span data-ttu-id="347e6-146">2.0 프로젝트에서 *Program.cs*의 `Main` 메서드는 간소화되었습니다.</span><span class="sxs-lookup"><span data-stu-id="347e6-146">In 2.0 projects, the `Main` method of *Program.cs* has been simplified:</span></span>

<span data-ttu-id="347e6-147">[!code-csharp[Main](../1x-to-2x/samples/AspNetCoreDotNetCore2.0App/AspNetCoreDotNetCore2.0App/Program.cs?highlight=8-11)]</span><span class="sxs-lookup"><span data-stu-id="347e6-147">[!code-csharp[Main](../1x-to-2x/samples/AspNetCoreDotNetCore2.0App/AspNetCoreDotNetCore2.0App/Program.cs?highlight=8-11)]</span></span>

<span data-ttu-id="347e6-148">이 새로운 2.0 패턴의 도입은 적극 권장되며 [Entity Framework Core 마이그레이션](xref:data/ef-mvc/migrations)과 같은 제품 기능을 작동하는 데 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="347e6-148">The adoption of this new 2.0 pattern is highly recommended and is required for product features like [Entity Framework Core Migrations](xref:data/ef-mvc/migrations) to work.</span></span> <span data-ttu-id="347e6-149">예를 들어 패키지 관리자 콘솔 창에서 `Update-Database` 또는 명령줄(ASP.NET Core 2.0으로 변환되는 프로젝트에서)에서 `dotnet ef database update` 실행은 다음 오류를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="347e6-149">For example, running `Update-Database` from the Package Manager Console window or `dotnet ef database update` from the command line (on projects converted to ASP.NET Core 2.0) generates the following error:</span></span>

```
Unable to create an object of type '<Context>'. Add an implementation of 'IDesignTimeDbContextFactory<Context>' to the project, or see https://go.microsoft.com/fwlink/?linkid=851728 for additional patterns supported at design time.
```

<a name="view-compilation"></a>

## <a name="review-your-razor-view-compilation-setting"></a><span data-ttu-id="347e6-150">Razor 보기 컴파일 설정 검토</span><span class="sxs-lookup"><span data-stu-id="347e6-150">Review your Razor View Compilation setting</span></span>
<span data-ttu-id="347e6-151">빠른 응용 프로그램 시작 시간 및 보다 작은 게시된 번들은 무엇보다도 중요합니다.</span><span class="sxs-lookup"><span data-stu-id="347e6-151">Faster application startup time and smaller published bundles are of utmost importance to you.</span></span> <span data-ttu-id="347e6-152">이러한 이유로 [Razor 보기 컴파일](xref:mvc/views/view-compilation)은 ASP.NET Core 2.0에서 기본적으로 활성화됩니다.</span><span class="sxs-lookup"><span data-stu-id="347e6-152">For these reasons, [Razor view compilation](xref:mvc/views/view-compilation) is enabled by default in ASP.NET Core 2.0.</span></span>

<span data-ttu-id="347e6-153">`MvcRazorCompileOnPublish` 속성을 true로 설정하는 것은 더 이상 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="347e6-153">Setting the `MvcRazorCompileOnPublish` property to true is no longer required.</span></span> <span data-ttu-id="347e6-154">보기 컴파일을 비활성화하는 경우가 아니면 *.csproj* 파일에서 속성을 제거할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="347e6-154">Unless you're disabling view compilation, the property may be removed from the *.csproj* file.</span></span>

<span data-ttu-id="347e6-155">.NET Framework를 대상으로 하는 경우 여전히 명시적으로 *.csproj* 파일에서 [Microsoft.AspNetCore.Mvc.Razor.ViewCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation) NuGet 패키지를 참조해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="347e6-155">When targeting .NET Framework, you still need to explicitly reference the [Microsoft.AspNetCore.Mvc.Razor.ViewCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation) NuGet package in your *.csproj* file:</span></span>

<span data-ttu-id="347e6-156">[!code-xml[Main](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App.csproj?range=15)]</span><span class="sxs-lookup"><span data-stu-id="347e6-156">[!code-xml[Main](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App.csproj?range=15)]</span></span>

<a name="app-insights"></a>

## <a name="rely-on-application-insights-light-up-features"></a><span data-ttu-id="347e6-157">Application Insights "강화" 기능 사용</span><span class="sxs-lookup"><span data-stu-id="347e6-157">Rely on Application Insights "Light-Up" features</span></span>
<span data-ttu-id="347e6-158">응용 프로그램 성능 계측의 손쉬운 설치는 중요합니다.</span><span class="sxs-lookup"><span data-stu-id="347e6-158">Effortless setup of application performance instrumentation is important.</span></span> <span data-ttu-id="347e6-159">이제 Visual Studio 2017 도구에서 제공하는 [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) "강화" 기능을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="347e6-159">You can now rely on the new [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) "light-up" features available in the Visual Studio 2017 tooling.</span></span>

<span data-ttu-id="347e6-160">Visual Studio 2017에서 만든 ASP.NET Core 1.1 프로젝트는 기본적으로 Application Insights를 추가했습니다.</span><span class="sxs-lookup"><span data-stu-id="347e6-160">ASP.NET Core 1.1 projects created in Visual Studio 2017 added Application Insights by default.</span></span> <span data-ttu-id="347e6-161">*Program.cs* 및 *Startup.cs* 외부에서 Application Insights SDK를 직접 사용하지 않을 경우 다음 단계를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="347e6-161">If you're not using the Application Insights SDK directly, outside of *Program.cs* and *Startup.cs*, follow these steps:</span></span>

1. <span data-ttu-id="347e6-162">*.csproj* 파일에서 다음 `<PackageReference />` 노드를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="347e6-162">Remove the following `<PackageReference />` node from the *.csproj* file:</span></span>
    
    <span data-ttu-id="347e6-163">[!code-xml[Main](../1x-to-2x/samples/AspNetCoreDotNetCore1.1App/AspNetCoreDotNetCore1.1App/AspNetCoreDotNetCore1.1App.csproj?range=10)]</span><span class="sxs-lookup"><span data-stu-id="347e6-163">[!code-xml[Main](../1x-to-2x/samples/AspNetCoreDotNetCore1.1App/AspNetCoreDotNetCore1.1App/AspNetCoreDotNetCore1.1App.csproj?range=10)]</span></span>

2. <span data-ttu-id="347e6-164">*Program.cs*에서 `UseApplicationInsights` 확장 메서드 호출을 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="347e6-164">Remove the `UseApplicationInsights` extension method invocation from *Program.cs*:</span></span>

    <span data-ttu-id="347e6-165">[!code-csharp[Main](../1x-to-2x/samples/AspNetCoreDotNetCore1.1App/AspNetCoreDotNetCore1.1App/Program.cs?name=snippet_ProgramCsMain&highlight=8)]</span><span class="sxs-lookup"><span data-stu-id="347e6-165">[!code-csharp[Main](../1x-to-2x/samples/AspNetCoreDotNetCore1.1App/AspNetCoreDotNetCore1.1App/Program.cs?name=snippet_ProgramCsMain&highlight=8)]</span></span>

3. <span data-ttu-id="347e6-166">*_Layout.cshtml*에서 Application Insights 클라이언트 쪽 API 호출을 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="347e6-166">Remove the Application Insights client-side API call from *_Layout.cshtml*.</span></span> <span data-ttu-id="347e6-167">다음 두 코드 줄을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="347e6-167">It comprises the following two lines of code:</span></span>

    <span data-ttu-id="347e6-168">[!code-cshtml[Main](../1x-to-2x/samples/AspNetCoreDotNetCore1.1App/AspNetCoreDotNetCore1.1App/Views/Shared/_Layout.cshtml?range=1,19)]</span><span class="sxs-lookup"><span data-stu-id="347e6-168">[!code-cshtml[Main](../1x-to-2x/samples/AspNetCoreDotNetCore1.1App/AspNetCoreDotNetCore1.1App/Views/Shared/_Layout.cshtml?range=1,19)]</span></span>

<span data-ttu-id="347e6-169">Application Insights SDK를 직접 사용하는 경우 계속 진행합니다.</span><span class="sxs-lookup"><span data-stu-id="347e6-169">If you are using the Application Insights SDK directly, continue to do so.</span></span> <span data-ttu-id="347e6-170">2.0 [metapackage](xref:fundamentals/metapackage)에는 최신 버전의 Application Insights가 포함되어 있으므로 이전 버전을 참조하는 경우 패키지 다운그레이드 오류가 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="347e6-170">The 2.0 [metapackage](xref:fundamentals/metapackage) includes the latest version of Application Insights, so a package downgrade error appears if you're referencing an older version.</span></span>

<a name="auth-and-identity"></a>

## <a name="adopt-authentication--identity-improvements"></a><span data-ttu-id="347e6-171">인증 / ID 기능 향상 도입</span><span class="sxs-lookup"><span data-stu-id="347e6-171">Adopt Authentication / Identity Improvements</span></span>
<span data-ttu-id="347e6-172">ASP.NET Core 2.0에는 새 인증 모델 및 ASP.NET Core ID에 대한 몇 가지 주요 변경 사항이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="347e6-172">ASP.NET Core 2.0 has a new authentication model and a number of significant changes to ASP.NET Core Identity.</span></span> <span data-ttu-id="347e6-173">개별 사용자 계정을 활성화하여 프로젝트를 만들거나 인증 또는 ID를 수동으로 추가한 경우 [ASP.NET Core 2.0으로 인증 및 ID 마이그레이션](xref:migration/1x-to-2x/identity-2x)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="347e6-173">If you created your project with Individual User Accounts enabled, or if you have manually added authentication or Identity, see [Migrating Authentication and Identity to ASP.NET Core 2.0](xref:migration/1x-to-2x/identity-2x).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="347e6-174">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="347e6-174">Additional Resources</span></span>
- [<span data-ttu-id="347e6-175">ASP.NET Core 2.0의 주요 변경 내용</span><span class="sxs-lookup"><span data-stu-id="347e6-175">Breaking Changes in ASP.NET Core 2.0</span></span>](https://github.com/aspnet/announcements/issues?page=1&q=is%3Aissue+is%3Aopen+label%3A2.0.0+label%3A%22Breaking+change%22&utf8=%E2%9C%93)
