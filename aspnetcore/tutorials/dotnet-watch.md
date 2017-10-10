---
title: "dotnet watch를 사용하여 ASP.NET Core 앱 개발"
author: rick-anderson
description: "이 자습서에서는 ASP.NET Core 응용 프로그램에서 .NET Core CLI 파일 감시자(dotnet 감시자) 도구를 사용하는 방법을 보여줍니다."
keywords: "ASP.NET Core, dotnet watch 사용"
ms.author: riande
manager: wpickett
ms.date: 10/05/2017
ms.topic: article
ms.assetid: 563ffb3f-d369-4aa5-bf0a-7300b4e7832c
ms.technology: aspnet
ms.prod: asp.net-core
uid: tutorials/dotnet-watch
ms.openlocfilehash: 9baf2ce2a1270a728616a8a2ab45deca9a9cde6f
ms.sourcegitcommit: e7f01a649f240b6b57118c53314ab82f7f36f2eb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2017
---
# <a name="developing-aspnet-core-apps-using-dotnet-watch"></a><span data-ttu-id="cb316-104">dotnet watch를 사용하여 ASP.NET Core 앱 개발</span><span class="sxs-lookup"><span data-stu-id="cb316-104">Developing ASP.NET Core apps using dotnet watch</span></span>

<span data-ttu-id="cb316-105">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [Victor Hurdugaci](https://twitter.com/victorhurdugaci)</span><span class="sxs-lookup"><span data-stu-id="cb316-105">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Victor Hurdugaci](https://twitter.com/victorhurdugaci)</span></span>

<span data-ttu-id="cb316-106">`dotnet watch`은 원본 파일을 변경할 때 [.NET Core CLI](/dotnet/core/tools) 명령을 실행하는 도구입니다.</span><span class="sxs-lookup"><span data-stu-id="cb316-106">`dotnet watch` is a tool that runs a [.NET Core CLI](/dotnet/core/tools) command when source files change.</span></span> <span data-ttu-id="cb316-107">예를 들어 파일 변경은 컴파일, 테스트 실행 또는 배포를 트리거할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb316-107">For example, a file change can trigger compilation, test execution, or deployment.</span></span>

<span data-ttu-id="cb316-108">이 자습서에서는 합계를 반환하는 끝점과 제품을 반환하는 끝점인 두 개의 끝점으로 기존 Web API 앱을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="cb316-108">In this tutorial, we use an existing Web API app with two endpoints: one that returns a sum and one that returns a product.</span></span> <span data-ttu-id="cb316-109">제품 메서드는 이 자습서의 일부로 해결할 버그를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="cb316-109">The product method contains a bug that we'll fix as part of this tutorial.</span></span>

<span data-ttu-id="cb316-110">[샘플 앱](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/dotnet-watch/sample)을 다운로드합니다.</span><span class="sxs-lookup"><span data-stu-id="cb316-110">Download the [sample app](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/dotnet-watch/sample).</span></span> <span data-ttu-id="cb316-111">*WebApp*(ASP.NET Core Web API) 및 *WebAppTests*(Web API의 단위 테스트)라는 두 개의 프로젝트가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="cb316-111">It contains two projects: *WebApp* (an ASP.NET Core Web API) and *WebAppTests* (unit tests for the Web API).</span></span>

<span data-ttu-id="cb316-112">명령 셸에서 *WebApp* 폴더로 이동하고 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="cb316-112">In a command shell, navigate to the *WebApp* folder and run the following command:</span></span>

```console
dotnet run
```

<span data-ttu-id="cb316-113">콘솔 출력은 다음과 유사한 메시지를 표시합니다(앱이 실행되고 요청을 대기하는 것을 나타냄).</span><span class="sxs-lookup"><span data-stu-id="cb316-113">The console output shows messages similar to the following (indicating that the app is running and awaiting requests):</span></span>

```console
$ dotnet run
Hosting environment: Development
Content root path: C:/Docs/aspnetcore/tutorials/dotnet-watch/sample/WebApp
Now listening on: http://localhost:5000
Application started. Press Ctrl+C to shut down.
```

<span data-ttu-id="cb316-114">웹 브라우저에서 `http://localhost:<port number>/api/math/sum?a=4&b=5`로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="cb316-114">In a web browser, navigate to `http://localhost:<port number>/api/math/sum?a=4&b=5`.</span></span> <span data-ttu-id="cb316-115">`9`라는 결과가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="cb316-115">You should see the result of `9`.</span></span>

<span data-ttu-id="cb316-116">API 제품으로 이동합니다(`http://localhost:<port number>/api/math/product?a=4&b=5`).</span><span class="sxs-lookup"><span data-stu-id="cb316-116">Navigate to the product API (`http://localhost:<port number>/api/math/product?a=4&b=5`).</span></span> <span data-ttu-id="cb316-117">예상한 대로 `20`이 아니라 `9`를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="cb316-117">It returns `9`, not `20` as you'd expect.</span></span> <span data-ttu-id="cb316-118">자습서의 뒷부분에서 이를 해결합니다.</span><span class="sxs-lookup"><span data-stu-id="cb316-118">We'll fix that later in the tutorial.</span></span>

## <a name="add-dotnet-watch-to-a-project"></a><span data-ttu-id="cb316-119">프로젝트에 `dotnet watch` 추가</span><span class="sxs-lookup"><span data-stu-id="cb316-119">Add `dotnet watch` to a project</span></span>

1. <span data-ttu-id="cb316-120">`Microsoft.DotNet.Watcher.Tools` 패키지 참조를 *.csproj* 파일에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="cb316-120">Add a `Microsoft.DotNet.Watcher.Tools` package reference to the *.csproj* file:</span></span>

    ```xml
    <ItemGroup>
        <DotNetCliToolReference Include="Microsoft.DotNet.Watcher.Tools" Version="2.0.0" />
    </ItemGroup> 
    ```

1. <span data-ttu-id="cb316-121">다음 명령을 실행하여 `Microsoft.DotNet.Watcher.Tools` 패키지를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="cb316-121">Install the `Microsoft.DotNet.Watcher.Tools` package by running the following command:</span></span>
    
    ```console
    dotnet restore
    ```

## <a name="running-net-core-cli-commands-using-dotnet-watch"></a><span data-ttu-id="cb316-122">`dotnet watch`을 사용하여 .NET Core CLI 명령 실행</span><span class="sxs-lookup"><span data-stu-id="cb316-122">Running .NET Core CLI commands using `dotnet watch`</span></span>

<span data-ttu-id="cb316-123">모든 [.NET Core CLI 명령](/dotnet/core/tools#cli-commands)을 `dotnet watch`로 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb316-123">Any [.NET Core CLI command](/dotnet/core/tools#cli-commands) can be run with `dotnet watch`.</span></span> <span data-ttu-id="cb316-124">예:</span><span class="sxs-lookup"><span data-stu-id="cb316-124">For example:</span></span>

| <span data-ttu-id="cb316-125">명령</span><span class="sxs-lookup"><span data-stu-id="cb316-125">Command</span></span> | <span data-ttu-id="cb316-126">watch를 사용하는 명령</span><span class="sxs-lookup"><span data-stu-id="cb316-126">Command with watch</span></span> |
| ---- | ----- |
| <span data-ttu-id="cb316-127">dotnet run</span><span class="sxs-lookup"><span data-stu-id="cb316-127">dotnet run</span></span> | <span data-ttu-id="cb316-128">dotnet watch run</span><span class="sxs-lookup"><span data-stu-id="cb316-128">dotnet watch run</span></span> |
| <span data-ttu-id="cb316-129">dotnet run -f netcoreapp2.0</span><span class="sxs-lookup"><span data-stu-id="cb316-129">dotnet run -f netcoreapp2.0</span></span> | <span data-ttu-id="cb316-130">dotnet watch run -f netcoreapp2.0</span><span class="sxs-lookup"><span data-stu-id="cb316-130">dotnet watch run -f netcoreapp2.0</span></span> |
| <span data-ttu-id="cb316-131">dotnet run -f netcoreapp2.0 -- --arg1</span><span class="sxs-lookup"><span data-stu-id="cb316-131">dotnet run -f netcoreapp2.0 -- --arg1</span></span> | <span data-ttu-id="cb316-132">dotnet watch run -f netcoreapp2.0 -- --arg1</span><span class="sxs-lookup"><span data-stu-id="cb316-132">dotnet watch run -f netcoreapp2.0 -- --arg1</span></span> |
| <span data-ttu-id="cb316-133">dotnet test</span><span class="sxs-lookup"><span data-stu-id="cb316-133">dotnet test</span></span> | <span data-ttu-id="cb316-134">dotnet watch 테스트</span><span class="sxs-lookup"><span data-stu-id="cb316-134">dotnet watch test</span></span> |

<span data-ttu-id="cb316-135">*WebApp* 폴더에서 `dotnet watch run`을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="cb316-135">Run `dotnet watch run` in the *WebApp* folder.</span></span> <span data-ttu-id="cb316-136">콘솔 출력은 `watch`가 시작했음을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="cb316-136">The console output indicates `watch` has started.</span></span>

## <a name="making-changes-with-dotnet-watch"></a><span data-ttu-id="cb316-137">`dotnet watch`를 사용하여 변경</span><span class="sxs-lookup"><span data-stu-id="cb316-137">Making changes with `dotnet watch`</span></span>

<span data-ttu-id="cb316-138">`dotnet watch`가 실행되고 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="cb316-138">Make sure `dotnet watch` is running.</span></span>

<span data-ttu-id="cb316-139">합계가 아닌 제품을 반환하도록 *MathController.cs*의 `Product` 메서드에서 버그를 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="cb316-139">Fix the bug in the `Product` method of *MathController.cs* so it returns the product and not the sum:</span></span>

```csharp
public static int Product(int a, int b)
{
  return a * b;
} 
```

<span data-ttu-id="cb316-140">파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="cb316-140">Save the file.</span></span> <span data-ttu-id="cb316-141">콘솔 출력은 `dotnet watch`에서 파일 변경을 검색했고 앱을 다시 시작했음을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="cb316-141">The console output indicates that `dotnet watch` detected a file change and restarted the app.</span></span>

<span data-ttu-id="cb316-142">`http://localhost:<port number>/api/math/product?a=4&b=5`에서 올바른 결과를 반환하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="cb316-142">Verify `http://localhost:<port number>/api/math/product?a=4&b=5` returns the correct result.</span></span>

## <a name="running-tests-using-dotnet-watch"></a><span data-ttu-id="cb316-143">`dotnet watch`를 사용하여 테스트 실행</span><span class="sxs-lookup"><span data-stu-id="cb316-143">Running tests using `dotnet watch`</span></span>

1. <span data-ttu-id="cb316-144">*MathController.cs*의 `Product` 메서드가 합계를 반환하도록 변경하고 파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="cb316-144">Change the `Product` method of *MathController.cs* back to returning the sum and save the file.</span></span>
1. <span data-ttu-id="cb316-145">명령 셸에서 *WebAppTests* 폴더로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="cb316-145">In a command shell, navigate to the *WebAppTests* folder.</span></span>
1. <span data-ttu-id="cb316-146">`dotnet restore`를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="cb316-146">Run `dotnet restore`.</span></span>
1. <span data-ttu-id="cb316-147">`dotnet watch test`를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="cb316-147">Run `dotnet watch test`.</span></span> <span data-ttu-id="cb316-148">이 출력은 테스트에 실패했으며 감시자가 파일 변경을 대기 중임을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="cb316-148">Its output indicates that a test failed and that watcher is awaiting file changes:</span></span>

     ```console
     Total tests: 2. Passed: 1. Failed: 1. Skipped: 0.
     Test Run Failed.
     ```

1. <span data-ttu-id="cb316-149">제품을 반환하도록 `Product` 메서드 코드를 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="cb316-149">Fix the `Product` method code so it returns the product.</span></span> <span data-ttu-id="cb316-150">파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="cb316-150">Save the file.</span></span>

<span data-ttu-id="cb316-151">`dotnet watch`는 파일 변경을 검색하고 테스트를 다시 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="cb316-151">`dotnet watch` detects the file change and reruns the tests.</span></span> <span data-ttu-id="cb316-152">콘솔 출력은 통과된 테스트를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="cb316-152">The console output indicates the tests passed.</span></span>

## <a name="dotnet-watch-in-github"></a><span data-ttu-id="cb316-153">GitHub에서 dotnet-watch</span><span class="sxs-lookup"><span data-stu-id="cb316-153">dotnet-watch in GitHub</span></span>

<span data-ttu-id="cb316-154">dotnet-watch는 GitHub [DotNetTools 리포지토리](https://github.com/aspnet/DotNetTools/tree/dev/src/Microsoft.DotNet.Watcher.Tools)의 일부입니다.</span><span class="sxs-lookup"><span data-stu-id="cb316-154">dotnet-watch is part of the GitHub [DotNetTools repository](https://github.com/aspnet/DotNetTools/tree/dev/src/Microsoft.DotNet.Watcher.Tools).</span></span>

<span data-ttu-id="cb316-155">[dotnet-watch 추가 정보](https://github.com/aspnet/DotNetTools/blob/dev/src/Microsoft.DotNet.Watcher.Tools/README.md)의 [MSBuild 섹션](https://github.com/aspnet/DotNetTools/blob/dev/src/Microsoft.DotNet.Watcher.Tools/README.md#msbuild)은 dotnet-watch가 조사되는 MSBuild 프로젝트 파일에서 구성될 수 있는 방법을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="cb316-155">The [MSBuild section](https://github.com/aspnet/DotNetTools/blob/dev/src/Microsoft.DotNet.Watcher.Tools/README.md#msbuild) of the [dotnet-watch ReadMe](https://github.com/aspnet/DotNetTools/blob/dev/src/Microsoft.DotNet.Watcher.Tools/README.md) explains how dotnet-watch can be configured from the MSBuild project file being watched.</span></span> <span data-ttu-id="cb316-156">[dotnet-watch 추가 정보](https://github.com/aspnet/DotNetTools/blob/dev/src/Microsoft.DotNet.Watcher.Tools/README.md)는 이 자습서에서 다루지 않는 dotnet-watch에 대한 정보를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="cb316-156">The [dotnet-watch ReadMe](https://github.com/aspnet/DotNetTools/blob/dev/src/Microsoft.DotNet.Watcher.Tools/README.md) contains information on dotnet-watch not covered in this tutorial.</span></span>
