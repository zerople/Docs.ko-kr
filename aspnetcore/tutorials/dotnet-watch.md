---
title: "dotnet watch를 사용하여 ASP.NET Core 앱 개발"
author: rick-anderson
description: "dotnet watch를 사용하는 방법을 보여 줍니다."
keywords: "ASP.NET Core, dotnet watch 사용"
ms.author: riande
manager: wpickett
ms.date: 03/09/2017
ms.topic: article
ms.assetid: 563ffb3f-d369-4aa5-bf0a-7300b4e7832c
ms.technology: aspnet
ms.prod: asp.net-core
uid: tutorials/dotnet-watch
ms.openlocfilehash: 6a8943619e6174dbcd3d901b7bb736ba5d3af95d
ms.sourcegitcommit: 78d28178345a0eea91556e4cd1adad98b1446db8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2017
---
# <a name="developing-aspnet-core-apps-using-dotnet-watch"></a><span data-ttu-id="57218-104">dotnet watch를 사용하여 ASP.NET Core 앱 개발</span><span class="sxs-lookup"><span data-stu-id="57218-104">Developing ASP.NET Core apps using dotnet watch</span></span>


<span data-ttu-id="57218-105">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [Victor Hurdugaci](https://twitter.com/victorhurdugaci)</span><span class="sxs-lookup"><span data-stu-id="57218-105">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Victor Hurdugaci](https://twitter.com/victorhurdugaci)</span></span>

<span data-ttu-id="57218-106">`dotnet watch`은 원본 파일을 변경할 때 `dotnet` 명령을 실행하는 도구입니다.</span><span class="sxs-lookup"><span data-stu-id="57218-106">`dotnet watch` is a tool that runs a `dotnet` command when source files change.</span></span> <span data-ttu-id="57218-107">예를 들어 파일 변경은 컴파일, 테스트 또는 배포를 트리거할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57218-107">For example, a file change can trigger compilation, tests, or deployment.</span></span>

<span data-ttu-id="57218-108">이 자습서에서는 합계를 반환하는 끝점과 제품을 반환하는 끝점인 두 개의 끝점으로 기존 Web API 앱을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="57218-108">In this tutorial, we use an existing Web API app with two endpoints: one that returns a sum and one that returns a product.</span></span> <span data-ttu-id="57218-109">제품 메서드는 이 자습서의 일부로 해결할 버그를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="57218-109">The product method contains a bug that we'll fix as part of this tutorial.</span></span>

<span data-ttu-id="57218-110">[샘플 앱](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/dotnet-watch/sample)을 다운로드합니다.</span><span class="sxs-lookup"><span data-stu-id="57218-110">Download the [sample app](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/dotnet-watch/sample).</span></span> <span data-ttu-id="57218-111">`WebApp`(웹앱) 및 `WebAppTests`(웹앱에 대한 단위 테스트)로 두 개의 프로젝트를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="57218-111">It contains two projects, `WebApp` (a web app) and `WebAppTests` (unit tests for the web app).</span></span>

<span data-ttu-id="57218-112">콘솔에서 WebApp 폴더로 이동하고 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="57218-112">In a console, navigate to the WebApp folder and run the following commands:</span></span>

- `dotnet restore`
- `dotnet run`

<span data-ttu-id="57218-113">콘솔 출력은 다음과 유사한 메시지를 표시합니다(앱이 실행되고 요청을 대기하는 것을 나타냄).</span><span class="sxs-lookup"><span data-stu-id="57218-113">The console output will show messages similar to the following (indicating that the app is running and waiting for requests):</span></span>

```console
$ dotnet run
Hosting environment: Production
Content root path: C:/Docs/aspnetcore/tutorials/dotnet-watch/sample/WebApp
Now listening on: http://localhost:5000
Application started. Press Ctrl+C to shut down.
```

<span data-ttu-id="57218-114">웹 브라우저에서 `http://localhost:5000/api/math/sum?a=4&b=5`로 이동하면 결과 `9`가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="57218-114">In a web browser, navigate to `http://localhost:5000/api/math/sum?a=4&b=5`, you should see the result `9`.</span></span>

<span data-ttu-id="57218-115">제품 API로 이동하면(`http://localhost:5000/api/math/product?a=4&b=5`), 예상한 대로 `20`이 아닌 `9`를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="57218-115">Navigate to the product API (`http://localhost:5000/api/math/product?a=4&b=5`), it returns `9`, not `20` as you'd expect.</span></span> <span data-ttu-id="57218-116">자습서의 뒷부분에서 이를 해결합니다.</span><span class="sxs-lookup"><span data-stu-id="57218-116">We'll fix that later in the tutorial.</span></span>

## <a name="add-dotnet-watch-to-a-project"></a><span data-ttu-id="57218-117">프로젝트에 `dotnet watch` 추가</span><span class="sxs-lookup"><span data-stu-id="57218-117">Add `dotnet watch` to a project</span></span>

- <span data-ttu-id="57218-118">*.csproj* 파일에 `Microsoft.DotNet.Watcher.Tools`를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="57218-118">Add `Microsoft.DotNet.Watcher.Tools` to the *.csproj* file:</span></span>
 ```xml
 <ItemGroup>
   <DotNetCliToolReference Include="Microsoft.DotNet.Watcher.Tools" Version="2.0.0" />
 </ItemGroup> 
 ```

- <span data-ttu-id="57218-119">`dotnet restore`를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="57218-119">Run `dotnet restore`.</span></span>

## <a name="running-dotnet-commands-using-dotnet-watch"></a><span data-ttu-id="57218-120">`dotnet watch`를 사용하여 `dotnet` 명령 실행</span><span class="sxs-lookup"><span data-stu-id="57218-120">Running `dotnet` commands using `dotnet watch`</span></span>

<span data-ttu-id="57218-121">모든 `dotnet` 명령은 `dotnet watch`를 사용하여 실행할 수 있습니다. 예를 들어 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="57218-121">Any `dotnet` command can be run with `dotnet watch`, for example:</span></span>

| <span data-ttu-id="57218-122">명령</span><span class="sxs-lookup"><span data-stu-id="57218-122">Command</span></span> | <span data-ttu-id="57218-123">watch를 사용하는 명령</span><span class="sxs-lookup"><span data-stu-id="57218-123">Command with watch</span></span> |
| ---- | ----- |
| <span data-ttu-id="57218-124">dotnet run</span><span class="sxs-lookup"><span data-stu-id="57218-124">dotnet run</span></span> | <span data-ttu-id="57218-125">dotnet watch run</span><span class="sxs-lookup"><span data-stu-id="57218-125">dotnet watch run</span></span> |
| <span data-ttu-id="57218-126">dotnet run -f net451</span><span class="sxs-lookup"><span data-stu-id="57218-126">dotnet run -f net451</span></span> | <span data-ttu-id="57218-127">dotnet watch run -f net451</span><span class="sxs-lookup"><span data-stu-id="57218-127">dotnet watch run -f net451</span></span> |
| <span data-ttu-id="57218-128">dotnet run -f net451 -- --arg1</span><span class="sxs-lookup"><span data-stu-id="57218-128">dotnet run -f net451 -- --arg1</span></span> | <span data-ttu-id="57218-129">dotnet watch run -f net451 -- --arg1</span><span class="sxs-lookup"><span data-stu-id="57218-129">dotnet watch run -f net451 -- --arg1</span></span> |
| <span data-ttu-id="57218-130">dotnet test</span><span class="sxs-lookup"><span data-stu-id="57218-130">dotnet test</span></span> | <span data-ttu-id="57218-131">dotnet watch 테스트</span><span class="sxs-lookup"><span data-stu-id="57218-131">dotnet watch test</span></span> |

<span data-ttu-id="57218-132">`WebApp` 폴더에서 `dotnet watch run`을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="57218-132">Run `dotnet watch run` in the `WebApp` folder.</span></span> <span data-ttu-id="57218-133">콘솔 출력은 `watch`가 시작했음을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="57218-133">The console output will indicate `watch` has started.</span></span>

## <a name="making-changes-with-dotnet-watch"></a><span data-ttu-id="57218-134">`dotnet watch`를 사용하여 변경</span><span class="sxs-lookup"><span data-stu-id="57218-134">Making changes with `dotnet watch`</span></span>

<span data-ttu-id="57218-135">`dotnet watch`가 실행되고 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="57218-135">Make sure `dotnet watch` is running.</span></span>

<span data-ttu-id="57218-136">합계가 아닌 제품을 반환하도록 `MathController`의 `Product` 메서드에서 버그를 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="57218-136">Fix the bug in the `Product` method of the `MathController` so it returns the product and not the sum.</span></span>

```csharp
public static int Product(int a, int b)
{
  return a * b;
} 
```

<span data-ttu-id="57218-137">파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="57218-137">Save the file.</span></span> <span data-ttu-id="57218-138">콘솔 출력은 `dotnet watch`에서 파일 변경을 검색했고 앱을 다시 시작했음을 나타내는 메시지를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="57218-138">The console output will show messages indicating that `dotnet watch` detected a file change and restarted the app.</span></span>

<span data-ttu-id="57218-139">`http://localhost:5000/api/math/product?a=4&b=5`에서 올바른 결과를 반환하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="57218-139">Verify `http://localhost:5000/api/math/product?a=4&b=5` returns the correct result.</span></span>

## <a name="running-tests-using-dotnet-watch"></a><span data-ttu-id="57218-140">`dotnet watch`를 사용하여 테스트 실행</span><span class="sxs-lookup"><span data-stu-id="57218-140">Running tests using `dotnet watch`</span></span>

- <span data-ttu-id="57218-141">`MathController`의 `Product` 메서드를 합계 반환으로 변경하고 파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="57218-141">Change the `Product` method of the `MathController` back to returning the sum and save the file.</span></span>
- <span data-ttu-id="57218-142">명령 창에서 `WebAppTests` 폴더로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="57218-142">In a command window, naviagate to the `WebAppTests` folder.</span></span>
- <span data-ttu-id="57218-143">`dotnet restore` 실행</span><span class="sxs-lookup"><span data-stu-id="57218-143">Run `dotnet restore`</span></span>
- <span data-ttu-id="57218-144">`dotnet watch test`를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="57218-144">Run `dotnet watch test`.</span></span> <span data-ttu-id="57218-145">테스트에 실패했으며 해당 watcher가 파일 변경에 대해 대기 중임을 나타내는 출력이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="57218-145">You see output indicating that a test failed and that watcher is waiting for file changes:</span></span>

 ```console
 Total tests: 2. Passed: 1. Failed: 1. Skipped: 0.
 Test Run Failed.
  ```
- <span data-ttu-id="57218-146">제품을 반환하도록 `Product` 메서드 코드를 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="57218-146">Fix the `Product` method code so it returns the product.</span></span> <span data-ttu-id="57218-147">파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="57218-147">Save the file.</span></span>

<span data-ttu-id="57218-148">`dotnet watch`는 파일 변경을 검색하고 테스트를 다시 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="57218-148">`dotnet watch` detects the file change and reruns the tests.</span></span> <span data-ttu-id="57218-149">콘솔 출력은 통과된 테스트를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="57218-149">The console output will show the tests passed.</span></span>

## <a name="dotnet-watch-in-github"></a><span data-ttu-id="57218-150">GitHub에서 dotnet-watch</span><span class="sxs-lookup"><span data-stu-id="57218-150">dotnet-watch in GitHub</span></span>

<span data-ttu-id="57218-151">dotnet-watch는 GitHub [DotNetTools 리포지토리](https://github.com/aspnet/DotNetTools/tree/dev/src/Microsoft.DotNet.Watcher.Tools)의 일부입니다.</span><span class="sxs-lookup"><span data-stu-id="57218-151">dotnet-watch is part of the GitHub [DotNetTools repository](https://github.com/aspnet/DotNetTools/tree/dev/src/Microsoft.DotNet.Watcher.Tools).</span></span>

<span data-ttu-id="57218-152">[dotnet-watch 추가 정보](https://github.com/aspnet/DotNetTools/blob/dev/src/Microsoft.DotNet.Watcher.Tools/README.md)의 [MSBuild 섹션](https://github.com/aspnet/DotNetTools/blob/dev/src/Microsoft.DotNet.Watcher.Tools/README.md#msbuild)은 dotnet-watch가 조사되는 MSBuild 프로젝트 파일에서 구성될 수 있는 방법을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="57218-152">The [MSBuild section](https://github.com/aspnet/DotNetTools/blob/dev/src/Microsoft.DotNet.Watcher.Tools/README.md#msbuild) of the [dotnet-watch ReadMe](https://github.com/aspnet/DotNetTools/blob/dev/src/Microsoft.DotNet.Watcher.Tools/README.md) explains how dotnet-watch can be configured from the MSBuild project file being watched.</span></span> <span data-ttu-id="57218-153">[dotnet-watch 추가 정보](https://github.com/aspnet/DotNetTools/blob/dev/src/Microsoft.DotNet.Watcher.Tools/README.md)는 이 자습서에서 다루지 않는 dotnet-watch에 대한 정보를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="57218-153">The [dotnet-watch ReadMe](https://github.com/aspnet/DotNetTools/blob/dev/src/Microsoft.DotNet.Watcher.Tools/README.md) contains information on dotnet-watch not covered in this tutorial.</span></span>
