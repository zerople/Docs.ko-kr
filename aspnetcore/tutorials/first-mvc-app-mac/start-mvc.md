---
title: "ASP.NET Core MVC 및 Mac용 Visual Studio 시작"
author: rick-anderson
description: "ASP.NET Core MVC 및 Visual Studio 시작"
keywords: "ASP.NET Core, MVC, Mac용 Visual Studio, Entity Framework"
ms.author: riande
manager: wpickett
ms.date: 8/23/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: tutorials/first-mvc-app-mac/start-mvc
ms.openlocfilehash: 387d7a91ae7d58cbc293c04039017df1dd208c82
ms.sourcegitcommit: f017f940a164dbaf84307410c78eb14e0f3ac811
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2017
---
# <a name="getting-started-with-aspnet-core-mvc-and-visual-studio-for-mac"></a><span data-ttu-id="44d1c-104">ASP.NET Core MVC 및 Mac용 Visual Studio 시작</span><span class="sxs-lookup"><span data-stu-id="44d1c-104">Getting started with ASP.NET Core MVC and Visual Studio for Mac</span></span>

<span data-ttu-id="44d1c-105">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="44d1c-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="44d1c-106">이 자습서에서는 [Mac용 Visual Studio](https://www.visualstudio.com/vs/visual-studio-mac/)를 사용하여 ASP.NET Core MVC 웹앱을 빌드하는 기본 사항에 대해 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="44d1c-106">This tutorial teaches you the basics of building an ASP.NET Core MVC web app using [Visual Studio for Mac](https://www.visualstudio.com/vs/visual-studio-mac/).</span></span> 

[!INCLUDE[consider RP](../../includes/razor.md)]

<span data-ttu-id="44d1c-107">이 자습서는 세 가지 버전이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="44d1c-107">There are 3 versions of this tutorial:</span></span>

* <span data-ttu-id="44d1c-108">macOS: [Mac용 Visual Studio을 사용하여 ASP.NET Core MVC 앱 빌드](xref:tutorials/first-mvc-app-mac/start-mvc)</span><span class="sxs-lookup"><span data-stu-id="44d1c-108">macOS: [Build an ASP.NET Core MVC app with Visual Studio for Mac](xref:tutorials/first-mvc-app-mac/start-mvc)</span></span>
* <span data-ttu-id="44d1c-109">Windows: [Visual Studio를 사용하여 ASP.NET Core MVC 앱 빌드](xref:tutorials/first-mvc-app/start-mvc)</span><span class="sxs-lookup"><span data-stu-id="44d1c-109">Windows: [Build an ASP.NET Core MVC app with Visual Studio](xref:tutorials/first-mvc-app/start-mvc)</span></span>
* <span data-ttu-id="44d1c-110">Linux, macOS 및 Windows: [Visual Studio Code를 사용하여 ASP.NET Core MVC 앱 빌드](xref:tutorials/first-mvc-app-xplat/start-mvc)</span><span class="sxs-lookup"><span data-stu-id="44d1c-110">Linux, macOS, and Windows: [Build an ASP.NET Core MVC app with Visual Studio Code](xref:tutorials/first-mvc-app-xplat/start-mvc)</span></span>

## <a name="prerequisites"></a><span data-ttu-id="44d1c-111">필수 구성 요소</span><span class="sxs-lookup"><span data-stu-id="44d1c-111">Prerequisites</span></span>

<span data-ttu-id="44d1c-112">이 자습서에서는 [.NET Core 2.0.0 SDK](https://www.microsoft.com/net/core) 이상이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="44d1c-112">This tutorial requires the [.NET Core 2.0.0 SDK](https://www.microsoft.com/net/core) or later.</span></span> <span data-ttu-id="44d1c-113">ASP.NET Core 1.1 버전에 대해서는 [PDF](https://github.com/aspnet/Docs/blob/master/aspnetcore/tutorials/first-mvc-app-mac/start-mvc/8-23-17.pdf)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="44d1c-113">See [the pdf](https://github.com/aspnet/Docs/blob/master/aspnetcore/tutorials/first-mvc-app-mac/start-mvc/8-23-17.pdf) for the ASP.NET Core 1.1 version.</span></span>

<span data-ttu-id="44d1c-114">다음을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="44d1c-114">Install the following:</span></span>

- <span data-ttu-id="44d1c-115">[.NET Core 2.0.0 SDK](https://www.microsoft.com/net/core) 이상</span><span class="sxs-lookup"><span data-stu-id="44d1c-115">[.NET Core 2.0.0 SDK](https://www.microsoft.com/net/core) or later</span></span>
- [<span data-ttu-id="44d1c-116">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="44d1c-116">Visual Studio for Mac</span></span>](https://www.visualstudio.com/vs/visual-studio-mac/)

## <a name="create-a-web-app"></a><span data-ttu-id="44d1c-117">웹앱 만들기</span><span class="sxs-lookup"><span data-stu-id="44d1c-117">Create a web app</span></span>

<span data-ttu-id="44d1c-118">Visual Studio에서 **파일 > 새 솔루션**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="44d1c-118">From Visual Studio, select **File > New Solution**.</span></span>

![macOS 새 솔루션](../first-web-api-mac/_static/sln.png)

<span data-ttu-id="44d1c-120">**.NET Core App > ASP.NET Core > 웹앱 > 다음**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="44d1c-120">Select **.NET Core App >  ASP.NET Core > Web App > Next**.</span></span>

![macOS 새 프로젝트 대화 상자](start-mvc/1.png)

<span data-ttu-id="44d1c-122">프로젝트 이름을 **MvcMovie**로 지정하고 **만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="44d1c-122">Name the project **MvcMovie**, and then select **Create**.</span></span>

![macOS 새 프로젝트 대화 상자](start-mvc/2.png)

### <a name="launch-the-app"></a><span data-ttu-id="44d1c-124">앱 시작</span><span class="sxs-lookup"><span data-stu-id="44d1c-124">Launch the app</span></span>

<span data-ttu-id="44d1c-125">Visual Studio에서 **실행 > 디버깅하지 않고 시작**을 선택하여 앱을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="44d1c-125">In Visual Studio, select **Run > Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="44d1c-126">Visual Studio가 [Kestrel](xref:fundamentals/servers/index#Kestrel)을 시작하고 브라우저를 실행하며 `http://localhost:port`로 이동합니다. 여기서 *port*는 임의로 선택된 포트 번호입니다.</span><span class="sxs-lookup"><span data-stu-id="44d1c-126">Visual Studio starts [Kestrel](xref:fundamentals/servers/index#Kestrel), launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

![새 프로젝트가 있는 브라우저](start-mvc/b1.png)

* <span data-ttu-id="44d1c-128">주소 표시줄에 `localhost:port#`이 표시되고 `example.com` 등은 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="44d1c-128">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="44d1c-129">그 이유는 `localhost`가 로컬 컴퓨터의 표준 이름이기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="44d1c-129">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="44d1c-130">Visual Studio에서 웹 프로젝트를 만들 경우 웹 서버에는 임의 포트가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="44d1c-130">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="44d1c-131">앱을 실행할 경우 다른 포트 번호가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="44d1c-131">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="44d1c-132">**실행** 메뉴 항목에서 앱을 디버그 또는 디버그 이외 모드로 시작할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="44d1c-132">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

<span data-ttu-id="44d1c-133">기본 템플릿은 **홈, 정보** 및 **연락처** 링크를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="44d1c-133">The default template gives you **Home, About** and **Contact** links.</span></span> <span data-ttu-id="44d1c-134">위의 브라우저 이미지에는 이러한 링크가 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="44d1c-134">The browser image above doesn't show these links.</span></span> <span data-ttu-id="44d1c-135">브라우저 크기에 따라 탐색 아이콘을 클릭하여 링크를 표시해야 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="44d1c-135">Depending on the size of your browser, you might need to click the navigation icon to show them.</span></span>

![새 프로젝트가 있는 브라우저](start-mvc/b2.png)

<span data-ttu-id="44d1c-137">이 자습서의 다음 부분에서는 MVC에 대해 알아보고 일부 코드 작성을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="44d1c-137">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

>[!div class="step-by-step"]
[<span data-ttu-id="44d1c-138">다음</span><span class="sxs-lookup"><span data-stu-id="44d1c-138">Next</span></span>](adding-controller.md)  
