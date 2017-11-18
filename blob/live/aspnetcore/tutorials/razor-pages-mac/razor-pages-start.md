---
title: "Mac의 ASP.NET Core에서 Razor 페이지 시작"
author: rick-anderson
description: "Mac용 Visual Studio를 사용하여 ASP.NET Core에서 Razor 페이지 시작"
keywords: "ASP.NET Core, Razor 페이지, 스캐폴딩, Entity Framework Core, EF, EF Core, 데이터베이스, mac, macOS, Mac용 Visual Studio"
ms.author: riande
manager: wpickett
ms.date: 07/27/2017
ms.topic: get-started-article
ms.technology: aspnet
ms.prod: aspnet-core
uid: tutorials/razor-pages-mac/razor-pages-start
ms.openlocfilehash: 9431e8160011d1485925db5cc4f9f83bf7381e97
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2017
---
# <a name="getting-started-with-razor-pages-in-aspnet-core-with-visual-studio-for-mac"></a><span data-ttu-id="b8d8a-104">Mac용 Visual Studio를 사용하여 ASP.NET Core에서 Razor 페이지 시작</span><span class="sxs-lookup"><span data-stu-id="b8d8a-104">Getting started with Razor Pages in ASP.NET Core with Visual Studio for Mac</span></span>

<span data-ttu-id="b8d8a-105">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="b8d8a-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="b8d8a-106">이 자습서에서는 ASP.NET Core Razor 페이지 웹앱을 빌드하는 작업의 기본 사항을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d8a-106">This tutorial teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span> <span data-ttu-id="b8d8a-107">이 자습서를 시작하기 전에 [Razor 페이지 소개](xref:mvc/razor-pages/index)를 완료하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="b8d8a-107">We recommend you complete [Introduction to Razor Pages](xref:mvc/razor-pages/index) before starting this tutorial.</span></span> <span data-ttu-id="b8d8a-108">Razor 페이지는 ASP.NET Core에서 웹 응용 프로그램 UI를 빌드하는 좋은 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="b8d8a-108">Razor Pages is the recommended way to build UI for web applications in ASP.NET Core.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="b8d8a-109">필수 구성 요소</span><span class="sxs-lookup"><span data-stu-id="b8d8a-109">Prerequisites</span></span>

<span data-ttu-id="b8d8a-110">다음을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d8a-110">Install the following:</span></span>

* <span data-ttu-id="b8d8a-111">[.NET Core 2.0.0 SDK](https://www.microsoft.com/net/core) 이상</span><span class="sxs-lookup"><span data-stu-id="b8d8a-111">[.NET Core 2.0.0 SDK](https://www.microsoft.com/net/core) or later</span></span>
* [<span data-ttu-id="b8d8a-112">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b8d8a-112">Visual Studio for Mac</span></span>](https://www.visualstudio.com/vs/visual-studio-mac/)

## <a name="create-a-razor-web-app"></a><span data-ttu-id="b8d8a-113">Razor 웹앱 만들기</span><span class="sxs-lookup"><span data-stu-id="b8d8a-113">Create a Razor web app</span></span>

<span data-ttu-id="b8d8a-114">터미널에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d8a-114">From a terminal, run the following commands:</span></span>

```console
dotnet new razor -o RazorPagesMovie
cd RazorPagesMovie
dotnet run
```

<span data-ttu-id="b8d8a-115">이전 명령은 [.NET Core CLI](https://docs.microsoft.com/dotnet/core/tools/dotnet)를 사용하여 Razor 페이지 프로젝트를 만들고 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d8a-115">The preceding commands use the [.NET Core CLI](https://docs.microsoft.com/dotnet/core/tools/dotnet) to create and run a Razor Pages project.</span></span> <span data-ttu-id="b8d8a-116">브라우저를 열고 http://localhost:5000으로 이동하여 응용 프로그램을 봅니다.</span><span class="sxs-lookup"><span data-stu-id="b8d8a-116">Open a browser to http://localhost:5000 to view the application.</span></span>

![홈 또는 인덱스 페이지](../razor-pages/razor-pages-start/_static/home.png)

[!INCLUDE[razor-pages-start](../../includes/RP/razor-pages-start.md)]

## <a name="open-the-project"></a><span data-ttu-id="b8d8a-118">프로젝트 열기</span><span class="sxs-lookup"><span data-stu-id="b8d8a-118">Open the project</span></span>

<span data-ttu-id="b8d8a-119">Ctrl+C를 눌러 응용 프로그램을 종료합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d8a-119">Press Ctrl+C to shut down the application.</span></span>

<span data-ttu-id="b8d8a-120">Visual Studio에서 **파일 > 열기**를 선택하고 *RazorPagesMovie.csproj* 파일을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d8a-120">From Visual Studio, select **File > Open**, and then select the *RazorPagesMovie.csproj* file.</span></span>

### <a name="launch-the-app"></a><span data-ttu-id="b8d8a-121">앱 시작</span><span class="sxs-lookup"><span data-stu-id="b8d8a-121">Launch the app</span></span>

<span data-ttu-id="b8d8a-122">Visual Studio에서 **실행 > 디버깅하지 않고 시작**을 선택하여 앱을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d8a-122">In Visual Studio, select **Run > Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="b8d8a-123">Visual Studio가 [IIS Express](https://docs.microsoft.com/iis/extensions/introduction-to-iis-express/iis-express-overview)를 시작하고, 브라우저를 시작하고, `http://localhost:5000`으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d8a-123">Visual Studio starts [IIS Express](https://docs.microsoft.com/iis/extensions/introduction-to-iis-express/iis-express-overview), launches a browser, and navigates to `http://localhost:5000`.</span></span>

<span data-ttu-id="b8d8a-124">다음 자습서에서는 프로젝트에 모델을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d8a-124">In the next tutorial, we add a model to the project.</span></span>

>[!div class="step-by-step"]
[<span data-ttu-id="b8d8a-125">다음: 모델 추가</span><span class="sxs-lookup"><span data-stu-id="b8d8a-125">Next: Adding a model</span></span>](xref:tutorials/razor-pages-mac/model)
