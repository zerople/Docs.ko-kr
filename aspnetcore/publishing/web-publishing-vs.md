---
title: "Visual Studio 및 MSBuild에 대한 게시 프로필을 만들기"
author: rick-anderson
description: "Visual Studio의 웹 게시에 대해 설명합니다."
keywords: "ASP.NET Core, 웹 게시, 게시, msbuild, 웹 배포, dotnet publish, Visual Studio 2017"
ms.author: riande
manager: wpickett
ms.date: 09/26/2017
ms.topic: article
ms.assetid: 0377a02d-8fda-47a5-929a-24a16e1d2c93
ms.technology: aspnet
ms.prod: asp.net-core
uid: publishing/web-publishing-vs
ms.openlocfilehash: 8a2584363cbf418281cc0e2d796debe57fab846f
ms.sourcegitcommit: 6e83c55eb0450a3073ef2b95fa5f5bcb20dbbf89
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/28/2017
---
# <a name="create-publish-profiles-for-visual-studio-and-msbuild-to-deploy-aspnet-core-apps"></a><span data-ttu-id="20277-104">Visual Studio 및 MSBuild에 대한 게시 프로필을 만들어 ASP.NET Core 앱 배포</span><span class="sxs-lookup"><span data-stu-id="20277-104">Create publish profiles for Visual Studio and MSBuild, to deploy ASP.NET Core apps</span></span>

<span data-ttu-id="20277-105">작성자: [Sayed Ibrahim Hashimi](https://github.com/sayedihashimi) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="20277-105">By [Sayed Ibrahim Hashimi](https://github.com/sayedihashimi) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="20277-106">이 문서에서는 Visual Studio 2017을 사용하여 게시 프로필을 만드는 방법에 초점을 맞춥니다.</span><span class="sxs-lookup"><span data-stu-id="20277-106">This article focuses on using Visual Studio 2017 to create publish profiles.</span></span> <span data-ttu-id="20277-107">Visual Studio에서 만들어진 게시 프로필은 MSBuild 및 Visual Studio 2017에서 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="20277-107">The publish profiles created with Visual Studio can be run from MSBuild and Visual Studio 2017.</span></span> <span data-ttu-id="20277-108">문서는 게시 프로세스의 세부 정보를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="20277-108">The article provides details of the publishing process.</span></span> <span data-ttu-id="20277-109">Azure에 게시에 관한 지침은 [Visual Studio를 사용하여 Azure App Service에 ASP.NET Core 웹앱 게시](xref:tutorials/publish-to-azure-webapp-using-vs)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="20277-109">See [Publish an ASP.NET Core web app to Azure App Service using Visual Studio](xref:tutorials/publish-to-azure-webapp-using-vs) for instructions on publishing to Azure.</span></span>

<span data-ttu-id="20277-110">다음 *.csproj* 파일은 `dotnet new mvc` 명령을 사용하여 만들어졌습니다.</span><span class="sxs-lookup"><span data-stu-id="20277-110">The following *.csproj* file was created with the command `dotnet new mvc`:</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="20277-111">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="20277-111">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">

  <PropertyGroup>
    <TargetFramework>netcoreapp2.0</TargetFramework>
  </PropertyGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.All" Version="2.0.0" />
  </ItemGroup>

  <ItemGroup>
    <DotNetCliToolReference Include="Microsoft.VisualStudio.Web.CodeGeneration.Tools" Version="2.0.0" />
  </ItemGroup>

</Project>
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="20277-112">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="20277-112">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">

  <PropertyGroup>
    <TargetFramework>netcoreapp1.1</TargetFramework>
  </PropertyGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore" Version="1.1.1" />
    <PackageReference Include="Microsoft.AspNetCore.Mvc" Version="1.1.2" />
    <PackageReference Include="Microsoft.AspNetCore.StaticFiles" Version="1.1.1" />
  </ItemGroup>

</Project>
```

---

<span data-ttu-id="20277-113">위 태그의 첫째 줄에 있는 `<Project>` 요소의 `Sdk` 특성은 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="20277-113">The `Sdk` attribute in the `<Project>` element (in the first line) of the markup above does the following:</span></span>

* <span data-ttu-id="20277-114">시작 시 *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.Props*에서 `props` 파일을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="20277-114">Imports the `props` file from *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.Props* at the beginning.</span></span>
* <span data-ttu-id="20277-115">종료 시 *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.targets*에서 대상 파일을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="20277-115">Imports the targets file from *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.targets* at the end.</span></span>

<span data-ttu-id="20277-116">`MSBuildSDKsPath`의 기본 위치(Visual Studio 2017 Enterprise 사용)는 *%programfiles(x86)%\Microsoft Visual Studio\2017\Enterprise\MSBuild\Sdks* 폴더입니다.</span><span class="sxs-lookup"><span data-stu-id="20277-116">The default location for `MSBuildSDKsPath` (with Visual Studio 2017 Enterprise) is the *%programfiles(x86)%\Microsoft Visual Studio\2017\Enterprise\MSBuild\Sdks* folder.</span></span>

<span data-ttu-id="20277-117">`Microsoft.NET.Sdk.Web`은 다음에 종속됩니다.</span><span class="sxs-lookup"><span data-stu-id="20277-117">`Microsoft.NET.Sdk.Web` depends on:</span></span>

* <span data-ttu-id="20277-118">*Microsoft.NET.Sdk.Web.ProjectSystem*</span><span class="sxs-lookup"><span data-stu-id="20277-118">*Microsoft.NET.Sdk.Web.ProjectSystem*</span></span>
* <span data-ttu-id="20277-119">*Microsoft.NET.Sdk.Publish*</span><span class="sxs-lookup"><span data-stu-id="20277-119">*Microsoft.NET.Sdk.Publish*</span></span>

<span data-ttu-id="20277-120">이러한 항목은 다음 `props` 및 `targets`를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="20277-120">Which causes the following `props` and `targets` to be imported:</span></span>

* <span data-ttu-id="20277-121">$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web.ProjectSystem\Sdk\Sdk.Props</span><span class="sxs-lookup"><span data-stu-id="20277-121">$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web.ProjectSystem\Sdk\Sdk.Props</span></span>
* <span data-ttu-id="20277-122">$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web.ProjectSystem\Sdk\Sdk.targets</span><span class="sxs-lookup"><span data-stu-id="20277-122">$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web.ProjectSystem\Sdk\Sdk.targets</span></span>
* <span data-ttu-id="20277-123">$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Publish\Sdk\Sdk.Props</span><span class="sxs-lookup"><span data-stu-id="20277-123">$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Publish\Sdk\Sdk.Props</span></span>
* <span data-ttu-id="20277-124">$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Publish\Sdk\Sdk.targets</span><span class="sxs-lookup"><span data-stu-id="20277-124">$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Publish\Sdk\Sdk.targets</span></span>

<span data-ttu-id="20277-125">게시 대상은 사용된 게시 방법에 따라 올바른 대상 집합을 다시 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="20277-125">Publish targets will again import the right set of targets based on the publish method used.</span></span>

<span data-ttu-id="20277-126">MSBuild 또는 Visual Studio가 프로젝트를 로드하면 다음 높은 수준의 작업이 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="20277-126">When MSBuild or Visual Studio loads a project, the following high level actions are performed:</span></span>

* <span data-ttu-id="20277-127">프로젝트 빌드</span><span class="sxs-lookup"><span data-stu-id="20277-127">Build project</span></span>
* <span data-ttu-id="20277-128">게시할 파일 계산</span><span class="sxs-lookup"><span data-stu-id="20277-128">Compute files to publish</span></span>
* <span data-ttu-id="20277-129">대상에 파일 게시</span><span class="sxs-lookup"><span data-stu-id="20277-129">Publish files to destination</span></span>

### <a name="compute-project-items"></a><span data-ttu-id="20277-130">프로젝트 항목 계산</span><span class="sxs-lookup"><span data-stu-id="20277-130">Compute project items</span></span>

<span data-ttu-id="20277-131">프로젝트가 로드되면 프로젝트 항목(파일)이 계산됩니다.</span><span class="sxs-lookup"><span data-stu-id="20277-131">When the project is loaded, the project items (files) are computed.</span></span> <span data-ttu-id="20277-132">`item type` 특성에 따라 파일 처리 방법이 결정됩니다.</span><span class="sxs-lookup"><span data-stu-id="20277-132">The `item type` attribute determines how the file is processed.</span></span> <span data-ttu-id="20277-133">기본적으로 *.cs* 파일은 `Compile` 항목 목록에 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="20277-133">By default, *.cs* files are included in the `Compile` item list.</span></span> <span data-ttu-id="20277-134">`Compile` 항목 목록의 파일이 컴파일됩니다.</span><span class="sxs-lookup"><span data-stu-id="20277-134">Files in the `Compile` item list are compiled.</span></span>

<span data-ttu-id="20277-135">`Content` 항목 목록에는 빌드 출력 이외에 게시될 파일이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="20277-135">The `Content` item list contains files that will be published in addition to the build outputs.</span></span> <span data-ttu-id="20277-136">기본적으로 패턴 wwwroot/**와 일치하는 파일은 `Content` 항목에 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="20277-136">By default, files matching the pattern wwwroot/** will be included in the `Content` item.</span></span> <span data-ttu-id="20277-137">[와일드카드 사용 패턴인 wwwroot/**](https://gruntjs.com/configuring-tasks#globbing-patterns)는 *wwwroot* 폴더 **및** 하위 폴더에서 모든 파일을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="20277-137">[wwwroot/** is a globbing pattern](https://gruntjs.com/configuring-tasks#globbing-patterns) that specifies all files in the *wwwroot* folder **and** subfolders.</span></span> <span data-ttu-id="20277-138">게시 목록에 파일을 명시적으로 추가하려면 [포함하는 파일](#including-files)에 표시된 대로 *.csproj* 파일에서 직접 파일을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="20277-138">To explicitly add a file to the publish list, add the file directly in the *.csproj* file as shown in [Including Files](#including-files).</span></span>

<span data-ttu-id="20277-139">Visual Studio에서 **게시** 단추를 선택할 경우 또는 명령줄에서 게시할 경우:</span><span class="sxs-lookup"><span data-stu-id="20277-139">When you select the **Publish** button in Visual Studio or when you publish from command line:</span></span>

- <span data-ttu-id="20277-140">속성/항목이 계산됩니다(빌드하는 데 필요한 파일).</span><span class="sxs-lookup"><span data-stu-id="20277-140">The properties/items are computed (the files that are needed to build).</span></span>
- <span data-ttu-id="20277-141">Visual Studio에만 해당: NuGet 패키지가 복원됩니다.</span><span class="sxs-lookup"><span data-stu-id="20277-141">Visual Studio only: NuGet packages are restored.</span></span>  <span data-ttu-id="20277-142">(CLI에서 사용자가 명시적으로 복원해야 합니다.)</span><span class="sxs-lookup"><span data-stu-id="20277-142">(Restore needs to be explicit by the user on the CLI.)</span></span>
- <span data-ttu-id="20277-143">프로젝트가 빌드됩니다.</span><span class="sxs-lookup"><span data-stu-id="20277-143">The project builds.</span></span>
- <span data-ttu-id="20277-144">게시 항목이 계산됩니다(게시하는 데 필요한 파일).</span><span class="sxs-lookup"><span data-stu-id="20277-144">The publish items are computed (the files that are needed to publish).</span></span>
- <span data-ttu-id="20277-145">프로젝트가 게시됩니다.</span><span class="sxs-lookup"><span data-stu-id="20277-145">The project is published.</span></span> <span data-ttu-id="20277-146">계산된 파일이 게시 대상에 복사됩니다.</span><span class="sxs-lookup"><span data-stu-id="20277-146">(The computed files are copied to the publish destination.)</span></span>

## <a name="basic-command-line-publishing"></a><span data-ttu-id="20277-147">기본 명령줄 게시</span><span class="sxs-lookup"><span data-stu-id="20277-147">Basic command line publishing</span></span>

<span data-ttu-id="20277-148">이 섹션은 모든 .NET Core 지원 플랫폼에 적용되며 Visual Studio가 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="20277-148">This section works on all .NET Core supported platforms and doesn't require Visual Studio.</span></span> <span data-ttu-id="20277-149">아래 샘플에서 `dotnet publish` 명령은 *.csproj* 파일이 포함된 프로젝트 디렉터리에서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="20277-149">In the samples below, the `dotnet publish` command is run from the project directory (which contains the *.csproj* file).</span></span> <span data-ttu-id="20277-150">프로젝트 폴더에 있지 않다면 프로젝트 파일 경로를 명시적으로 전달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="20277-150">If you're not in the project folder, you can explicitly pass in the project file path.</span></span> <span data-ttu-id="20277-151">예:</span><span class="sxs-lookup"><span data-stu-id="20277-151">For example:</span></span>

```console
dotnet publish  c:/webs/web1
```

<span data-ttu-id="20277-152">다음 명령을 실행하여 웹앱을 만들고 게시합니다.</span><span class="sxs-lookup"><span data-stu-id="20277-152">Run the following commands to create and publish a web app:</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="20277-153">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="20277-153">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```console
dotnet new mvc
dotnet publish
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="20277-154">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="20277-154">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

```console
dotnet new mvc
dotnet restore
dotnet publish
```

--------------

<span data-ttu-id="20277-155">`dotnet publish`는 다음과 비슷한 출력을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="20277-155">The `dotnet publish` produces output similar to the following:</span></span>

```console
C:\Webs\Web1>dotnet publish
Microsoft (R) Build Engine version 15.3.409.57025 for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.

  Web1 -> C:\Webs\Web1\bin\Debug\netcoreapp2.0\Web1.dll
  Web1 -> C:\Webs\Web1\bin\Debug\netcoreapp2.0\publish\
```

<span data-ttu-id="20277-156">기본 게시 폴더는 `bin\$(Configuration)\netcoreapp<version>\publish`입니다.</span><span class="sxs-lookup"><span data-stu-id="20277-156">The default publish folder is `bin\$(Configuration)\netcoreapp<version>\publish`.</span></span> <span data-ttu-id="20277-157">`$(Configuration)`의 기본값은 Debug입니다.</span><span class="sxs-lookup"><span data-stu-id="20277-157">The default for `$(Configuration)` is Debug.</span></span> <span data-ttu-id="20277-158">위의 샘플에서 `<TargetFramework>`는 `netcoreapp2.0`입니다.</span><span class="sxs-lookup"><span data-stu-id="20277-158">In the sample above, the `<TargetFramework>` is `netcoreapp2.0`.</span></span>

<span data-ttu-id="20277-159">`dotnet publish -h`는 게시에 대한 도움말 정보를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="20277-159">`dotnet publish -h` displays help information for publish.</span></span>

<span data-ttu-id="20277-160">다음 명령은 `Release` 빌드 및 게시 디렉터리를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="20277-160">The following command specifies a `Release` build and the publishing directory:</span></span>

```console
dotnet publish -c Release -o C:/MyWebs/test
```

<span data-ttu-id="20277-161">`dotnet publish` 명령은 `Publish` 대상을 호출하는 `MSBuild`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="20277-161">The `dotnet publish` command calls `MSBuild` which invokes the `Publish` target.</span></span> <span data-ttu-id="20277-162">`dotnet publish` 및 `MSBuild`에 전달된 모든 매개 변수.</span><span class="sxs-lookup"><span data-stu-id="20277-162">Any parameters passed to `dotnet publish` are passed to `MSBuild`.</span></span> <span data-ttu-id="20277-163">`-c` 매개 변수는 `Configuration` MSBuild 속성에 매핑됩니다.</span><span class="sxs-lookup"><span data-stu-id="20277-163">The `-c` parameter maps to the `Configuration` MSBuild property.</span></span> <span data-ttu-id="20277-164">`-o` 매개 변수는 `OutputPath`에 매핑됩니다.</span><span class="sxs-lookup"><span data-stu-id="20277-164">The `-o` parameter maps to `OutputPath`.</span></span>

<span data-ttu-id="20277-165">다음 형식 중 하나를 사용하여 MSBuild 속성을 전달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="20277-165">You can pass MSBuild properties using either of the following formats:</span></span>

- ` p:<NAME>=<VALUE>`
- `/p:<NAME>=<VALUE>`

<span data-ttu-id="20277-166">다음 명령은 `Release` 빌드를 네트워크 공유에 게시합니다.</span><span class="sxs-lookup"><span data-stu-id="20277-166">The following command publishes a `Release` build to a network share:</span></span>

`dotnet publish -c Release /p:PublishDir=//r8/release/AdminWeb`

<span data-ttu-id="20277-167">네트워크 공유는 슬래시(*//r8/*)를 사용하여 지정하고 모든 .NET Core 지원 플랫폼에서 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="20277-167">The network share is specified with forward slashes (*//r8/*) and works on all .NET Core supported platforms.</span></span>

<span data-ttu-id="20277-168">배포할 게시된 앱이 실행되고 있지 않은지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="20277-168">Confirm that the published app for deployment isn't running.</span></span> <span data-ttu-id="20277-169">앱이 실행 중이면 *publish* 폴더의 파일이 잠겨 있습니다.</span><span class="sxs-lookup"><span data-stu-id="20277-169">Files in the *publish* folder are locked when the app is running.</span></span> <span data-ttu-id="20277-170">잠긴 파일을 복사할 수 없으므로 배포를 수행할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="20277-170">Deployment can't occur because locked files can't be copied.</span></span>

## <a name="publish-profiles"></a><span data-ttu-id="20277-171">게시 프로필</span><span class="sxs-lookup"><span data-stu-id="20277-171">Publish profiles</span></span>

<span data-ttu-id="20277-172">이 섹션에서는 Visual Studio 2017 이상을 사용하여 게시 프로필을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="20277-172">This section uses Visual Studio 2017 and higher to create publishing profiles.</span></span> <span data-ttu-id="20277-173">만들어진 후 Visual Studio 또는 명령줄에서 게시할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="20277-173">Once created, you can publish from Visual Studio or the command line.</span></span>

<span data-ttu-id="20277-174">게시 프로필을 사용하면 게시 프로세스를 간소화할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="20277-174">Publish profiles can simplify the publishing process.</span></span> <span data-ttu-id="20277-175">여러 게시 프로필이 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="20277-175">You can have multiple publish profiles.</span></span> <span data-ttu-id="20277-176">Visual Studio에서 게시 프로필을 만들려면 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="20277-176">To create a publish profile in Visual Studio, right click on the project in Solution Explore and select **Publish**.</span></span> <span data-ttu-id="20277-177">또는 빌드 메뉴에서 **\<프로젝트 이름> 게시**를 선택할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="20277-177">Alternatively, you can select **Publish     \<project name>** from the build menu.</span></span> <span data-ttu-id="20277-178">응용 프로그램 기능 페이지의 **게시** 탭이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="20277-178">The **Publish** tab of the application capacities page is displayed.</span></span> <span data-ttu-id="20277-179">프로젝트에 게시 프로필이 없으면 다음 페이지가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="20277-179">If the project doesn't contain a publish profile, the following page is displayed:</span></span>

![Azure, IIS, FTB, Azure가 선택된 폴더를 보여 주는 응용 프로그램 기능 페이지의 **게시** 탭.](web-publishing-vs/_static/az.png)

<span data-ttu-id="20277-182">**폴더**가 선택되면 **게시** 단추는 폴더 게시 프로필을 만들고 게시합니다.</span><span class="sxs-lookup"><span data-stu-id="20277-182">When **Folder** is selected, the **Publish** button creates a folder publish profile and publishes.</span></span>

![Azure, IIS, FTB, 폴더를 보여 주는 응용 프로그램 기능 페이지의 **게시** 탭](web-publishing-vs/_static/pub1.png)

<span data-ttu-id="20277-184">게시 프로필이 만들어진 후 **게시** 탭이 변경되고 **새 프로필 만들기**를 선택하여 새 프로필을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="20277-184">Once a publish profile is created, the **Publish** tab changes, and you select **Create new profile** to create a new profile.</span></span>

![마지막 단계에서 만들어진 FolderProfile 및 새 프로필 만들기 링크를 보여 주는 응용 프로그램 기능 페이지의 **게시** 탭](web-publishing-vs/_static/create_new.png)

<span data-ttu-id="20277-186">게시 마법사는 다음 게시 대상을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="20277-186">The Publish wizard supports the following publish targets:</span></span>

- <span data-ttu-id="20277-187">Microsoft Azure App Service</span><span class="sxs-lookup"><span data-stu-id="20277-187">Microsoft Azure App Service</span></span>
- <span data-ttu-id="20277-188">IIS, FTP 등(웹 서버의 경우)</span><span class="sxs-lookup"><span data-stu-id="20277-188">IIS, FTP, etc (for any web server)</span></span>
- <span data-ttu-id="20277-189">폴더</span><span class="sxs-lookup"><span data-stu-id="20277-189">Folder</span></span>
- <span data-ttu-id="20277-190">프로필 가져오기(프로필을 가져올 수 있음).</span><span class="sxs-lookup"><span data-stu-id="20277-190">Import profile (allows you to import a profile).</span></span>
- <span data-ttu-id="20277-191">Microsoft Azure Virtual Machines</span><span class="sxs-lookup"><span data-stu-id="20277-191">Microsoft Azure Virtual Machines</span></span>

<span data-ttu-id="20277-192">자세한 내용은 [내게 적합한 게시 옵션](https://docs.microsoft.com/visualstudio/ide/not-in-toc/web-publish-options)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="20277-192">See [What publishing options are right for me?](https://docs.microsoft.com/visualstudio/ide/not-in-toc/web-publish-options) for more information.</span></span>

<span data-ttu-id="20277-193">Visual Studio를 사용하여 게시 프로필을 만들면 *Properties/PublishProfiles/\<publish name>.pubxml* MSBuild 파일이 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="20277-193">When you create a publish profile with Visual Studio, a *Properties/PublishProfiles/\<publish name>.pubxml* MSBuild file is created.</span></span> <span data-ttu-id="20277-194">이 *.pubxml* 파일은 MSBuild 파일이고 게시 구성 설정을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="20277-194">This *.pubxml* file is a MSBuild file and contains publish configuration settings.</span></span> <span data-ttu-id="20277-195">이 파일을 변경하여 빌드 및 게시 프로세스를 사용자 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="20277-195">You can change this file to customize the build and publish process.</span></span> <span data-ttu-id="20277-196">게시 프로세스에서 이 파일을 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="20277-196">This file is read by the publishing process.</span></span> <span data-ttu-id="20277-197">`<LastUsedBuildConfiguration>`은 전역 속성이고 빌드에서 가져온 파일에 포함되면 안 되므로 특별합니다.</span><span class="sxs-lookup"><span data-stu-id="20277-197">`<LastUsedBuildConfiguration>` is special because it’s a global property and shouldn’t be in any file that’s imported in the build.</span></span> <span data-ttu-id="20277-198">자세한 내용은 [MSBuild: 구성 속성을 설정하는 방법](http://sedodream.com/2012/10/27/MSBuildHowToSetTheConfigurationProperty.aspx)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="20277-198">See [MSBuild: how to set the configuration property](http://sedodream.com/2012/10/27/MSBuildHowToSetTheConfigurationProperty.aspx) for more info.</span></span>
<span data-ttu-id="20277-199">*.pubxml* 파일은 *.user* 파일에 종속되므로 소스 제어에 체크 인되면 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="20277-199">The *.pubxml* file should not be checked into source control because it depends on the *.user* file.</span></span> <span data-ttu-id="20277-200">*.user* 파일은 중요 정보를 포함할 수 있고 하나의 사용자 및 컴퓨터에 대해서만 유효하므로 소스 제어에 체크 인되면 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="20277-200">The *.user* file should never be checked into source control because it can contain sensitive information and it's only valid for one user and machine.</span></span>

<span data-ttu-id="20277-201">중요 정보(예: 게시 암호)는 사용자/컴퓨터 수준별로 암호화되고 *Properties/PublishProfiles/\<publish name>.pubxml.user* 파일에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="20277-201">Sensitive information (like the publish password) is encrypted on a per user/machine level and stored in the *Properties/PublishProfiles/\<publish name>.pubxml.user* file.</span></span> <span data-ttu-id="20277-202">이 파일에는 중요 정보가 포함될 수 있으므로 소스 제어에 체크 인되면 **안 됩니다**.</span><span class="sxs-lookup"><span data-stu-id="20277-202">Because this file can contain sensitive information, it should **not** be checked into source control.</span></span>

<span data-ttu-id="20277-203">ASP.NET Core에서 웹앱을 게시하는 방법에 대한 개요는 [게시 및 배포](index.md)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="20277-203">For an overview of how to publish a web app on ASP.NET Core see [Publishing and Deployment](index.md).</span></span> <span data-ttu-id="20277-204">[게시 및 배포](index.md)는 오픈 소스 프로젝트(https://github.com/aspnet/websdk)입니다.</span><span class="sxs-lookup"><span data-stu-id="20277-204">[Publishing and Deployment](index.md) is an open source project at https://github.com/aspnet/websdk.</span></span>

<span data-ttu-id="20277-205">현재 `dotnet publish`에는 게시 프로필을 사용하는 기능이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="20277-205">Currently `dotnet publish` doesn’t have the ability to use publish profiles.</span></span> <span data-ttu-id="20277-206">게시 프로필을 사용하려면 `dotnet build`를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="20277-206">To use publish profiles, use `dotnet build`.</span></span> <span data-ttu-id="20277-207">`dotnet build`는 프로젝트에서 MSBuild를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="20277-207">`dotnet build` invokes MSBuild on the project.</span></span> <span data-ttu-id="20277-208">또는 `msbuild`를 직접 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="20277-208">Alternatively, call `msbuild` directly.</span></span>

<span data-ttu-id="20277-209">게시 프로필을 사용할 경우 다음 MSBuild 속성을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="20277-209">Set the following MSBuild properties when using a publish profile:</span></span>

- `DeployOnBuild=true`
- `PublishProfile=<Publish profile name>`

<span data-ttu-id="20277-210">예를 들어 이름이 *FolderProfile*인 프로필을 사용하여 게시할 경우 아래 명령 중 하나를 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="20277-210">For example, when publishing with a profile named *FolderProfile* you can execute either of the commands below.</span></span>

- `dotnet build /p:DeployOnBuild=true /p:PublishProfile=FolderProfile`
- `msbuild      /p:DeployOnBuild=true /p:PublishProfile=FolderProfile`

<span data-ttu-id="20277-211">`dotnet build`를 호출하면 이 명령은 `msbuild`를 호출하여 빌드 및 게시 프로세스를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="20277-211">When you invoke `dotnet build` it will call `msbuild` to run the build and publish process.</span></span> <span data-ttu-id="20277-212">폴더 프로필을 전달할 경우 `dotnet build` 또는 `msbuild` 호출은 근본적으로 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="20277-212">Calling `dotnet build` or `msbuild` is essentially equivalent when you pass in a folder profile.</span></span> <span data-ttu-id="20277-213">Windows에서 MSBuild를 직접 호출하면 MSBuild의 .NET Framework 버전이 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="20277-213">When calling MSBuild directly on Windows you get the .NET Framework version of MSBuild.</span></span>  <span data-ttu-id="20277-214">MSDeploy는 현재 게시를 위해 Windows 컴퓨터로 제한됩니다.</span><span class="sxs-lookup"><span data-stu-id="20277-214">MSDeploy is currently limited to Windows machines for publishing.</span></span> <span data-ttu-id="20277-215">폴더가 아닌 프로필에서 `dotnet build`를 호출하면 MSBuild가 호출되고 MSBuild는 폴더가 아닌 프로필에서 MSDeploy를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="20277-215">Calling `dotnet build` on a non-folder profile invokes MSBuild, and MSBuild uses MSDeploy on non-folder profiles.</span></span> <span data-ttu-id="20277-216">폴더가 아닌 프로필에서 `dotnet build`를 호출하면 MSBuild가 호출되고(MSDeploy 사용) Windows 플랫폼에서 실행될 경우에도 오류가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="20277-216">Calling `dotnet build` on a non-folder profile invokes MSBuild (using MSDeploy) and results in a failure (even when running on a Windows platform).</span></span> <span data-ttu-id="20277-217">폴더가 아닌 프로필을 사용하여 게시하려면 MSBuild를 직접 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="20277-217">To publish with a non-folder profile, call MSBuild directly.</span></span>

<span data-ttu-id="20277-218">다음 폴더 게시 프로필은 Visual Studio를 사용하여 만들어졌고 네트워크 공유에 게시됩니다.</span><span class="sxs-lookup"><span data-stu-id="20277-218">The following folder publish profile was created with Visual Studio and publishes to a network share:</span></span>

[!code-xml[Main](web-publishing-vs/sample/FolderProfile.pubxml?highlight=5,9,11,18)]

<span data-ttu-id="20277-219">`<LastUsedBuildConfiguration>`은 `Release`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="20277-219">Note `<LastUsedBuildConfiguration>` is set to `Release`.</span></span>  <span data-ttu-id="20277-220">Visual Studio에서 게시할 경우 `<LastUsedBuildConfiguration>` 구성 속성 값은 게시 프로세스가 시작될 때의 값을 사용하여 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="20277-220">When publishing from Visual Studio, the `<LastUsedBuildConfiguration>` configuration property value is set using the value when the publish process is started.</span></span> <span data-ttu-id="20277-221">`<LastUsedBuildConfiguration>` 구성 속성은 특별하고 가져온 MSBuild 파일에서 재정의되면 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="20277-221">The `<LastUsedBuildConfiguration>` configuration property is special and shouldn’t be overridden in an imported MSBuild file.</span></span> <span data-ttu-id="20277-222">명령줄에서 이 속성을 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="20277-222">You can override this property from the command line.</span></span> <span data-ttu-id="20277-223">예:</span><span class="sxs-lookup"><span data-stu-id="20277-223">For example:</span></span>

`dotnet build -c Release /p:DeployOnBuild=true /p:PublishProfile=FolderProfile`

<span data-ttu-id="20277-224">MSBuild 사용:</span><span class="sxs-lookup"><span data-stu-id="20277-224">Using MSBuild:</span></span>

`msbuild /p:Configuration=Release /p:DeployOnBuild=true /p:PublishProfile=FolderProfile`

## <a name="publish-to-an-msdeploy-endpoint-from-the-command-line"></a><span data-ttu-id="20277-225">명령줄에서 MSDeploy 끝점에 게시</span><span class="sxs-lookup"><span data-stu-id="20277-225">Publish to an MSDeploy endpoint from the command line</span></span>

<span data-ttu-id="20277-226">앞에서 설명한 대로 `dotnet publish` 또는 `msbuild` 명령을 사용하여 게시할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="20277-226">As previously mentioned, you can publish using `dotnet publish` or the `msbuild` command.</span></span> <span data-ttu-id="20277-227">`dotnet publish`는 .NET Core의 컨텍스트에서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="20277-227">`dotnet publish` runs in the context of .NET Core.</span></span> <span data-ttu-id="20277-228">`msbuild`의 경우 .NET Framework가 필요하므로 Windows 환경으로 제한됩니다.</span><span class="sxs-lookup"><span data-stu-id="20277-228">`msbuild` requires .NET framework, and is therefore limited to Windows environments.</span></span>

<span data-ttu-id="20277-229">MSDeploy를 사용하여 게시하는 가장 좋은 방법은 먼저 Visual Studio 2017에서 게시 프로필을 만들고 명령줄에서 프로필을 사용하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="20277-229">The easiest way to publish with MSDeploy is to first create a publish profile in Visual Studio 2017 and use the profile from the command line.</span></span>

<span data-ttu-id="20277-230">다음 샘플에서는 ASP.NET Core 웹앱을 만들고(`dotnet new mvc` 사용) Visual Studio를 사용하여 Azure 게시 프로필을 추가했습니다.</span><span class="sxs-lookup"><span data-stu-id="20277-230">In the following sample, an ASP.NET Core web app is created ( using `dotnet new mvc`) and an Azure publish profile is added with Visual Studio.</span></span>

<span data-ttu-id="20277-231">**VS 2017에 대한 개발자 명령 프롬프트**에서 `msbuild`를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="20277-231">You run `msbuild` from a **Developer Command Prompt for VS 2017**.</span></span> <span data-ttu-id="20277-232">개발자 명령 프롬프트에서는 해당 경로에 올바른 *msbuild.exe*가 있고 일부 MSBuild 변수를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="20277-232">The Developer Command Prompt will have the correct *msbuild.exe* in its path and set some MSBuild variables.</span></span>

<span data-ttu-id="20277-233">MSBuild는 다음 구문을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="20277-233">MSBuild uses the following syntax:</span></span>

`msbuild <path-to-project-file> /p:DeployOnBuild=true /p:PublishProfile=<Publish Profile>  /p:Username=<USERNAME> /p:Password=<PASSWORD>`

<span data-ttu-id="20277-234">*\<Publish name>.PublishSettings* 파일에서 `Password`를 가져올 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="20277-234">You can get the `Password` from the *\<Publish name>.PublishSettings* file.</span></span> <span data-ttu-id="20277-235">다음 위치에서 *.PublishSettings* 파일을 다운로드할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="20277-235">You can download the *.PublishSettings* file from:</span></span>

- <span data-ttu-id="20277-236">솔루션 탐색기: 웹앱을 마우스 오른쪽 단추로 클릭하고 **게시 프로필 다운로드**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="20277-236">Solution Explorer: Right click on the Web App and select **Download Publish Profile**.</span></span>
- <span data-ttu-id="20277-237">Azure 관리 포털: 웹앱 블레이드에서 **게시 프로필 가져오기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="20277-237">The Azure Management Portal: Select **Get publish profile** from the  Web App blade.</span></span>

<span data-ttu-id="20277-238">`Username`은 게시 프로필에서 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="20277-238">`Username` can be found in the publish profile.</span></span>

<span data-ttu-id="20277-239">다음 샘플에서는 “Web11112 - Web Deploy” 게시 프로필을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="20277-239">The following sample uses the "Web11112 - Web Deploy" publish profile:</span></span>

```console
msbuild "C:\Webs\Web1\Web1.csproj" /p:DeployOnBuild=true
 /p:PublishProfile="Web11112 - Web Deploy"  /p:Username="$Web11112"
 /p:Password="<password removed>"
```

## <a name="excluding-files"></a><span data-ttu-id="20277-240">파일 제외</span><span class="sxs-lookup"><span data-stu-id="20277-240">Excluding files</span></span>

<span data-ttu-id="20277-241">ASP.NET Core 웹앱을 게시할 경우 *wwwroot* 폴더의 빌드 아티팩트 및 콘텐츠가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="20277-241">When publishing ASP.NET Core web apps, the build artifacts and contents of the *wwwroot* folder are included.</span></span> <span data-ttu-id="20277-242">`msbuild`는 [와일드카드 사용 패턴](https://gruntjs.com/configuring-tasks#globbing-patterns)을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="20277-242">`msbuild` supports [globbing patterns](https://gruntjs.com/configuring-tasks#globbing-patterns).</span></span> <span data-ttu-id="20277-243">예를 들어 다음 `<Content>` 요소 태그는 *wwwroot/content* 폴더와 모든 하위 폴더에서 모든 텍스트(*.txt*) 파일을 제외합니다.</span><span class="sxs-lookup"><span data-stu-id="20277-243">For example, the following `<Content>` element markup will exclude all text (*.txt*) files from the *wwwroot/content* folder and all its subfolders.</span></span>

```xml
<ItemGroup>
  <Content Update="wwwroot/content/**/*.txt" CopyToPublishDirectory="Never" />
</ItemGroup>
```

<span data-ttu-id="20277-244">위의 태그는 게시 프로필 또는 *.csproj* 파일에 추가될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="20277-244">The markup above can be added to a publish profile or the *.csproj* file.</span></span> <span data-ttu-id="20277-245">*.csproj* 파일에 추가된 규칙은 프로젝트의 모든 게시 프로필에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="20277-245">When added to the *.csproj* file, the rule is added to all publish profiles in the project.</span></span>

<span data-ttu-id="20277-246">다음 `<MsDeploySkipRules>` 요소 태그는 *wwwroot/content* 폴더에서 모든 파일을 제외합니다.</span><span class="sxs-lookup"><span data-stu-id="20277-246">The following `<MsDeploySkipRules>` element markup exludes all files from the *wwwroot/content* folder:</span></span>

```xml
<ItemGroup>
  <MsDeploySkipRules Include="CustomSkipFolder">
    <ObjectName>dirPath</ObjectName>
    <AbsolutePath>wwwroot\content</AbsolutePath>
  </MsDeploySkipRules>
</ItemGroup>
```

<span data-ttu-id="20277-247">`<MsDeploySkipRules>`는 배포 사이트에서 *건너뛰기* 대상을 삭제하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="20277-247">`<MsDeploySkipRules>`  will not delete the *skip* targets from the deployment site.</span></span> <span data-ttu-id="20277-248">`<Content>` 대상 파일 및 폴더는 배포 사이트에서 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="20277-248">`<Content>` targeted files and folders will be deleted from the deployment site.</span></span> <span data-ttu-id="20277-249">예를 들어 다음 파일을 사용하여 웹앱을 배포했다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="20277-249">For example, suppose you had deployed a web app with the following files:</span></span>

- <span data-ttu-id="20277-250">*Views/Home/About1.cshtml*</span><span class="sxs-lookup"><span data-stu-id="20277-250">*Views/Home/About1.cshtml*</span></span>
- <span data-ttu-id="20277-251">*Views/Home/About2.cshtml*</span><span class="sxs-lookup"><span data-stu-id="20277-251">*Views/Home/About2.cshtml*</span></span>
- <span data-ttu-id="20277-252">*Views/Home/About3.cshtml*</span><span class="sxs-lookup"><span data-stu-id="20277-252">*Views/Home/About3.cshtml*</span></span>

<span data-ttu-id="20277-253">다음 `<MsDeploySkipRules>` 태그를 추가한 경우 해당 파일은 배포 사이트에서 삭제되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="20277-253">If you added the following `<MsDeploySkipRules>` markup, those files would not be deleted on the deployment site.</span></span>

``` xml
<ItemGroup>
  <MsDeploySkipRules Include="CustomSkipFile">
    <ObjectName>filePath</ObjectName>
    <AbsolutePath>Views\\Home\\About1.cshtml</AbsolutePath>
  </MsDeploySkipRules>

  <MsDeploySkipRules Include="CustomSkipFile">
    <ObjectName>filePath</ObjectName>
    <AbsolutePath>Views\\Home\\About2.cshtml</AbsolutePath>
  </MsDeploySkipRules>

  <MsDeploySkipRules Include="CustomSkipFile">
    <ObjectName>filePath</ObjectName>
    <AbsolutePath>Views\\Home\\About3.cshtml</AbsolutePath>
  </MsDeploySkipRules>
</ItemGroup>
```

<span data-ttu-id="20277-254">위에 표시된 `<MsDeploySkipRules>` 태그를 사용하면 *건너뛴* 파일이 배포되지 않지만 해당 파일이 배포된 후 파일이 삭제되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="20277-254">The `<MsDeploySkipRules>` markup shown above prevents the *skipped* files from being depoyed, but will not delete those files once they are deployed.</span></span>

<span data-ttu-id="20277-255">다음 `<Content>` 태그는 배포 사이트에서 대상 파일을 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="20277-255">The following `<Content>` markup would delete the targeted files at the deployment site:</span></span>

``` xml
<ItemGroup>
  <Content Update="Views/Home/About?.cshtml" CopyToPublishDirectory="Never" />
</ItemGroup>
```

<span data-ttu-id="20277-256">위의 `<Content>` 태그와 함께 명령줄 배포를 사용하면 다음과 비슷한 출력이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="20277-256">Using command line deployment with the `<Content>` markup above would result in output similar to the following:</span></span>

``` console
MSDeployPublish:
  Starting Web deployment task from source: manifest(C:\Webs\Web1\obj\Release\netcoreapp1.1\PubTmp\Web1.SourceManifest.
  xml) to Destination: auto().
  Deleting file (Web11112\Views\Home\About1.cshtml).
  Deleting file (Web11112\Views\Home\About2.cshtml).
  Deleting file (Web11112\Views\Home\About3.cshtml).
  Updating file (Web11112\web.config).
  Updating file (Web11112\Web1.deps.json).
  Updating file (Web11112\Web1.dll).
  Updating file (Web11112\Web1.pdb).
  Updating file (Web11112\Web1.runtimeconfig.json).
  Successfully executed Web deployment task.
  Publish Succeeded.
Done Building Project "C:\Webs\Web1\Web1.csproj" (default targets).
```

## <a name="including-files"></a><span data-ttu-id="20277-257">포함하는 파일</span><span class="sxs-lookup"><span data-stu-id="20277-257">Including files</span></span>

<span data-ttu-id="20277-258">다음 태그를 사용하여 게시 사이트의 *wwwroot/images* 폴더에 대한 프로젝트 외부 디렉터리에 *images* 폴더를 포함할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="20277-258">The following markup can be used to include an *images* folder outside the project directory to the *wwwroot/images* folder of the publish site.</span></span>

``` xml
<ItemGroup>
  <_CustomFiles Include="$(MSBuildProjectDirectory)/../images/**/*" />
  <DotnetPublishFiles Include="@(_CustomFiles)">
    <DestinationRelativePath>wwwroot/images/%(RecursiveDir)%(Filename)%(Extension)</DestinationRelativePath>
  </DotnetPublishFiles>
</ItemGroup>
```

<span data-ttu-id="20277-259">태그는 *.csproj* 파일 또는 게시 프로필에 추가될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="20277-259">The markup can be added to the *.csproj* file or the publish profile.</span></span> <span data-ttu-id="20277-260">*.csproj* 파일에 추가된 경우 프로젝트의 각 게시 프로필에 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="20277-260">If it's added to the *.csproj* file, it will be included in each publish profile in the project.</span></span>

<span data-ttu-id="20277-261">다음 강조 표시된 태그는 방법을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="20277-261">The following highlighted markup shows how to:</span></span>

* <span data-ttu-id="20277-262">프로젝트 외부에서 *wwwroot* 폴더로 파일을 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="20277-262">Copy a file from outside the project into the *wwwroot* folder.</span></span>
* <span data-ttu-id="20277-263">*wwwroot\Content* 폴더를 제외합니다.</span><span class="sxs-lookup"><span data-stu-id="20277-263">Exclude the *wwwroot\Content* folder.</span></span>
* <span data-ttu-id="20277-264">*Views\Home\About2.cshtml*을 제외합니다.</span><span class="sxs-lookup"><span data-stu-id="20277-264">Exclude *Views\Home\About2.cshtml*.</span></span>

[!code-xml[Main](web-publishing-vs/sample/FolderProfile2.pubxml?highlight=21-29)]

<span data-ttu-id="20277-265">더 많은 배포 샘플을 보려면 [WebSDK Readme](https://github.com/aspnet/websdk)(WebSDK 추가 정보)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="20277-265">See the [WebSDK Readme](https://github.com/aspnet/websdk) for more deployment samples.</span></span>

### <a name="run-a-target-before-or-after-publishing"></a><span data-ttu-id="20277-266">게시 이전 또는 이후 대상 실행</span><span class="sxs-lookup"><span data-stu-id="20277-266">Run a target before or after publishing</span></span>

<span data-ttu-id="20277-267">기본 제공 `BeforePublish` 및 `AfterPublish` 대상을 사용하여 게시 대상 이전 또는 이후에 대상을 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="20277-267">The builtin `BeforePublish` and `AfterPublish` targets can be used to execute a target before or after the publish target.</span></span> <span data-ttu-id="20277-268">다음 태그를 게시 프로필에 추가하여 게시 이전 및 이후에 콘솔 출력에 메시지를 기록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="20277-268">The following markup can be added to the publish profile to log messages to the console output before and after publishing:</span></span>

``` xml
<Target Name="CustomActionsBeforePublish" BeforeTargets="BeforePublish">
    <Message Text="Inside BeforePublish" Importance="high" />
  </Target>
  <Target Name="CustomActionsAfterPublish" AfterTargets="AfterPublish">
    <Message Text="Inside AfterPublish" Importance="high" />
</Target>
```

## <a name="the-kudu-service"></a><span data-ttu-id="20277-269">Kudu 서비스</span><span class="sxs-lookup"><span data-stu-id="20277-269">The Kudu service</span></span>

<span data-ttu-id="20277-270">Azure 웹앱에서 파일을 보려면 [kudu 서비스](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="20277-270">To view the files on your Azure Web App, use the [kudu service](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service).</span></span> <span data-ttu-id="20277-271">이름 또는 웹앱에 `scm` 토큰을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="20277-271">Append the `scm` token to the name or your Web App.</span></span> <span data-ttu-id="20277-272">예:</span><span class="sxs-lookup"><span data-stu-id="20277-272">For example:</span></span>

| <span data-ttu-id="20277-273">URL</span><span class="sxs-lookup"><span data-stu-id="20277-273">URL</span></span>               | <span data-ttu-id="20277-274">결과</span><span class="sxs-lookup"><span data-stu-id="20277-274">Result</span></span>|
| ----------------- | ------------ |
| `http://mysite.azurewebsites.net/` | <span data-ttu-id="20277-275">웹앱</span><span class="sxs-lookup"><span data-stu-id="20277-275">Web App</span></span> |
| `http://mysite.scm.azurewebsites.net/` | <span data-ttu-id="20277-276">Kudu 서비스</span><span class="sxs-lookup"><span data-stu-id="20277-276">Kudu sevice</span></span> |

<span data-ttu-id="20277-277">[디버그 콘솔](https://github.com/projectkudu/kudu/wiki/Kudu-console) 메뉴 항목을 선택하여 파일을 표시/편집/삭제/추가합니다.</span><span class="sxs-lookup"><span data-stu-id="20277-277">Select the [Debug Console](https://github.com/projectkudu/kudu/wiki/Kudu-console) menu item to view/edit/delete/add files.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="20277-278">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="20277-278">Additional resources</span></span>

- <span data-ttu-id="20277-279">[웹 배포](https://www.iis.net/downloads/microsoft/web-deploy)(msdeploy)는 IIS 서버에 대한 웹 응용 프로그램 및 웹 사이트 배포를 간소화합니다.</span><span class="sxs-lookup"><span data-stu-id="20277-279">[Web Deploy](https://www.iis.net/downloads/microsoft/web-deploy)  (msdeploy) simplifies deployment of Web applications and Web sites to IIS servers.</span></span>

- <span data-ttu-id="20277-280">[https://github.com/aspnet/websdk](https://github.com/aspnet/websdk/issues): 배포에 대한 파일 문제 및 요청 기능.</span><span class="sxs-lookup"><span data-stu-id="20277-280">[https://github.com/aspnet/websdk](https://github.com/aspnet/websdk/issues): File issues and request features for deployment.</span></span>
