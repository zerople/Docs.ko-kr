---
title: "ASP.NET Core 1.1 시작"
author: rick-anderson
description: "ASP.NET Core 1.1을 사용하여 간단한 Hello World 앱을 만들고 실행하는 빠른 자습서입니다."
keywords: "ASP.NET Core, 자습서, 시작"
ms.author: riande
manager: wpickett
ms.date: 08/07/2017
ms.topic: get-started-article
ms.assetid: 73543e9d-d9d5-47d6-9664-17a9beea6cd3
ms.technology: aspnet
ms.prod: asp.net-core
uid: getting-started-1.1
ms.openlocfilehash: e8fd9ef60ebc1cff6ca0e03000ea50eebff0a9f9
ms.sourcegitcommit: 297ee5d2f3b3b24eb8a2c4a25195c9e2973cb91b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2017
---
# <a name="getting-started-with-aspnet-core-11"></a><span data-ttu-id="d6f07-104">ASP.NET Core 1.1 시작</span><span class="sxs-lookup"><span data-stu-id="d6f07-104">Getting Started with ASP.NET Core 1.1</span></span>

> [!NOTE]
> <span data-ttu-id="d6f07-105">이러한 지침은 ASP.NET Core 1.1에 대한 것입니다.</span><span class="sxs-lookup"><span data-stu-id="d6f07-105">These instructions are for ASP.NET Core 1.1.</span></span> <span data-ttu-id="d6f07-106">최신 버전을 찾고 있나요?</span><span class="sxs-lookup"><span data-stu-id="d6f07-106">Looking for the latest version?</span></span> <span data-ttu-id="d6f07-107">[이 자습서의 현재 버전](xref:getting-started)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d6f07-107">See [the current version of this tutorial](xref:getting-started).</span></span>

1. <span data-ttu-id="d6f07-108">[.NET Core 1.0.5 & 1.1.2 SDK 1.0.4 다운로드 페이지](https://github.com/dotnet/core/blob/master/release-notes/download-archives/1.0.5-download.md)에서 SDK 1.0.4에 대한 .NET Core **SDK 설치 관리자**를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="d6f07-108">Install the .NET Core **SDK Installer** for SDK 1.0.4 from the [.NET Core 1.0.5 & 1.1.2 SDK 1.0.4 downloads page](https://github.com/dotnet/core/blob/master/release-notes/download-archives/1.0.5-download.md).</span></span>

2. <span data-ttu-id="d6f07-109">새 .NET Core 프로젝트에 대한 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d6f07-109">Create a folder for a new .NET Core project.</span></span>

   <span data-ttu-id="d6f07-110">macOS 및 Linux에서 터미널 창을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="d6f07-110">On macOS and Linux, open a terminal window.</span></span> <span data-ttu-id="d6f07-111">Windows에서 명령 프롬프트를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="d6f07-111">On Windows, open a command prompt.</span></span>

   ```terminal
   mkdir aspnetcoreapp
   cd aspnetcoreapp
   ```

2. <span data-ttu-id="d6f07-112">컴퓨터에 최신 SDK 버전을 설치한 경우 *global.json* 파일을 만들어 1.0.4 SDK를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d6f07-112">If you have installed a later SDK version on your machine, create a *global.json* file to select the 1.0.4 SDK.</span></span>

   ```json
   {
     "sdk": { "version": "1.0.4" }
   }
   ```

2. <span data-ttu-id="d6f07-113">새 .NET Core 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d6f07-113">Create a new .NET Core project.</span></span>

   ```terminal
   dotnet new web
   ```
   
3.  <span data-ttu-id="d6f07-114">패키지를 복원합니다.</span><span class="sxs-lookup"><span data-stu-id="d6f07-114">Restore the packages.</span></span>

    ```terminal
    dotnet restore
    ```

4. <span data-ttu-id="d6f07-115">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="d6f07-115">Run the app.</span></span>

   <span data-ttu-id="d6f07-116">`dotnet run` 명령은 필요한 경우 앱을 먼저 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="d6f07-116">The `dotnet run` command builds the app first if needed.</span></span>

   ```terminal
   dotnet run
   ```

5. <span data-ttu-id="d6f07-117">`http://localhost:5000`으로 이동</span><span class="sxs-lookup"><span data-stu-id="d6f07-117">Browse to `http://localhost:5000`</span></span>

<!-- H3 to avoid a single-entry internal TOC -->
### <a name="next-steps"></a><span data-ttu-id="d6f07-118">다음 단계</span><span class="sxs-lookup"><span data-stu-id="d6f07-118">Next steps</span></span>

<span data-ttu-id="d6f07-119">시작 자습서는 [ASP.NET Core 자습서](tutorials/index.md)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d6f07-119">For getting-started tutorials, see [ASP.NET Core Tutorials](tutorials/index.md)</span></span>

<span data-ttu-id="d6f07-120">ASP.NET Core 개념 및 아키텍처에 대한 소개는 [ASP.NET Core 소개](index.md) 및 [ASP.NET Core 기본 사항](fundamentals/index.md)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d6f07-120">For an introduction to ASP.NET Core concepts and architecture, see [ASP.NET Core Introduction](index.md) and [ASP.NET Core Fundamentals](fundamentals/index.md).</span></span>

<span data-ttu-id="d6f07-121">ASP.NET Core 앱은 .NET Core 또는 .NET Framework 기본 클래스 라이브러리 및 런타임을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d6f07-121">An ASP.NET Core app can use the .NET Core or .NET Framework Base Class Library and runtime.</span></span> <span data-ttu-id="d6f07-122">자세한 내용은 [.NET Core와 .NET Framework 중에 선택](https://docs.microsoft.com/dotnet/articles/standard/choosing-core-framework-server)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d6f07-122">For more information, see [Choosing between .NET Core and .NET Framework](https://docs.microsoft.com/dotnet/articles/standard/choosing-core-framework-server).</span></span>
