---
title: "ASP.NET Core MVC 및 Visual Studio 시작"
author: rick-anderson
description: "ASP.NET Core MVC 및 Visual Studio 시작"
keywords: ASP.NET Core, MVC
ms.author: riande
manager: wpickett
ms.date: 08/07/2017
ms.topic: get-started-article
ms.technology: aspnet
ms.prod: asp.net-core
uid: tutorials/first-mvc-app/start-mvc
ms.openlocfilehash: 9636e0e51e506d294ffb50a21165195c9d04fe20
ms.sourcegitcommit: bd05f7ea8f87ad076ef6e8b704698ebcba5ca80c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2017
---
# <a name="getting-started-with-aspnet-core-mvc-and-visual-studio"></a><span data-ttu-id="1e3ff-104">ASP.NET Core MVC 및 Visual Studio 시작</span><span class="sxs-lookup"><span data-stu-id="1e3ff-104">Getting started with ASP.NET Core MVC and Visual Studio</span></span>

<span data-ttu-id="1e3ff-105">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="1e3ff-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="1e3ff-106">이 자습서에서는 [Visual Studio 2017](https://www.visualstudio.com/)을 사용하여 ASP.NET Core MVC 웹앱을 빌드하는 기본 사항에 대해 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="1e3ff-106">This tutorial will teach you the basics of building an ASP.NET Core MVC web app using [Visual Studio 2017](https://www.visualstudio.com/).</span></span> [!INCLUDE[consider RP](../../includes/razor.md)]

<span data-ttu-id="1e3ff-107">이 자습서는 세 가지 버전이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1e3ff-107">There are 3 versions of this tutorial:</span></span>

* <span data-ttu-id="1e3ff-108">macOS: [Mac용 Visual Studio를 사용하여 ASP.NET Core MVC 앱 만들기](xref:tutorials/first-mvc-app-mac/start-mvc)</span><span class="sxs-lookup"><span data-stu-id="1e3ff-108">macOS: [Create an ASP.NET Core MVC app with Visual Studio for Mac](xref:tutorials/first-mvc-app-mac/start-mvc)</span></span>
* <span data-ttu-id="1e3ff-109">Windows: [Visual Studio를 사용하여 ASP.NET Core MVC 앱 만들기](xref:tutorials/first-mvc-app/start-mvc)</span><span class="sxs-lookup"><span data-stu-id="1e3ff-109">Windows: [Create an ASP.NET Core MVC app with Visual Studio](xref:tutorials/first-mvc-app/start-mvc)</span></span>
* <span data-ttu-id="1e3ff-110">macOS, Linux 및 Windows: [Visual Studio Code를 사용하여 ASP.NET Core MVC 앱 만들기](xref:tutorials/first-mvc-app-xplat/start-mvc)</span><span class="sxs-lookup"><span data-stu-id="1e3ff-110">macOS, Linux, and Windows: [Create an ASP.NET Core MVC app with Visual Studio Code](xref:tutorials/first-mvc-app-xplat/start-mvc)</span></span>

<span data-ttu-id="1e3ff-111">이 자습서의 Visual Studio 2015 버전을 보려면 [VS 2015 version of ASP.NET Core documentation in PDF format](https://github.com/aspnet/Docs/blob/master/aspnetcore/common/_static/aspnet-core-project-json.pdf)(ASP.NET Core의 VS 2015 버전 설명서(PDF 형식))을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="1e3ff-111">For the Visual Studio 2015 version of this tutorial, see the [VS 2015 version of ASP.NET Core documentation in PDF format](https://github.com/aspnet/Docs/blob/master/aspnetcore/common/_static/aspnet-core-project-json.pdf).</span></span>

## <a name="install-visual-studio-and-net-core"></a><span data-ttu-id="1e3ff-112">Visual Studio 및 .NET Core 설치</span><span class="sxs-lookup"><span data-stu-id="1e3ff-112">Install Visual Studio and .NET Core</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="1e3ff-113">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="1e3ff-113">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

[!INCLUDE[install 2.0](../../includes/install2.0.md)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="1e3ff-114">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="1e3ff-114">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="1e3ff-115">Visual Studio Community 2017을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="1e3ff-115">Install Visual Studio Community 2017.</span></span> <span data-ttu-id="1e3ff-116">커뮤니티 다운로드를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="1e3ff-116">Select the Community download.</span></span> <span data-ttu-id="1e3ff-117">Visual Studio 2017을 설치한 경우 이 단계를 건너뜁니다.</span><span class="sxs-lookup"><span data-stu-id="1e3ff-117">Skip this step if you have Visual Studio 2017 installed.</span></span>

* <span data-ttu-id="1e3ff-118">[Visual Studio 2017 Home page installer](https://www.visualstudio.com/visual-studio-homepage-vs.aspx)(Visual Studio 2017 홈페이지 설치 관리자)</span><span class="sxs-lookup"><span data-stu-id="1e3ff-118">[Visual Studio 2017 Home page installer](https://www.visualstudio.com/visual-studio-homepage-vs.aspx)</span></span>

<span data-ttu-id="1e3ff-119">설치 관리자를 실행하고 다음 워크로드를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="1e3ff-119">Run the installer and select the following workloads:</span></span>

* <span data-ttu-id="1e3ff-120">**ASP.NET 및 웹 개발**(**웹 및 클라우드** 아래)</span><span class="sxs-lookup"><span data-stu-id="1e3ff-120">**ASP.NET and web development** (under **Web & Cloud**)</span></span>
* <span data-ttu-id="1e3ff-121">**.NET Core 플랫폼 간 개발**(**기타 도구 집합** 아래)</span><span class="sxs-lookup"><span data-stu-id="1e3ff-121">**.NET Core cross-platform development** (under **Other Toolsets**)</span></span>

![**ASP.NET 및 웹 개발**(**웹 및 클라우드** 아래)](start-mvc/_static/web_workload.png)

![**.NET Core 플랫폼 간 개발**(**기타 도구 집합** 아래)](start-mvc/_static/x_plat_wl.png)

---

## <a name="create-a-web-app"></a><span data-ttu-id="1e3ff-124">웹앱 만들기</span><span class="sxs-lookup"><span data-stu-id="1e3ff-124">Create a web app</span></span>

<span data-ttu-id="1e3ff-125">Visual Studio에서 **파일 > 새로 만들기 > 프로젝트**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="1e3ff-125">From Visual Studio, select  **File > New > Project**.</span></span>

![파일 > 새로 만들기 > 프로젝트](start-mvc/_static/alt_new_project.png)

<span data-ttu-id="1e3ff-127">**새 프로젝트** 대화 상자를 완료합니다.</span><span class="sxs-lookup"><span data-stu-id="1e3ff-127">Complete the **New Project** dialog:</span></span>

* <span data-ttu-id="1e3ff-128">왼쪽 창에서 **.NET Core**를 탭합니다.</span><span class="sxs-lookup"><span data-stu-id="1e3ff-128">In the left pane, tap **.NET Core**</span></span>
* <span data-ttu-id="1e3ff-129">가운데 창에서 **ASP.NET Core 웹 응용 프로그램(.NET Core)**을 탭합니다.</span><span class="sxs-lookup"><span data-stu-id="1e3ff-129">In the center pane, tap **ASP.NET Core Web Application (.NET Core)**</span></span>
* <span data-ttu-id="1e3ff-130">프로젝트 이름을 “MvcMovie”로 지정합니다(코드를 복사할 때 네임스페이스가 일치하도록 프로젝트 이름을 “MvcMovie”로 지정해야 함).</span><span class="sxs-lookup"><span data-stu-id="1e3ff-130">Name the project "MvcMovie" (It's important to name the project "MvcMovie" so when you copy code, the namespace will match.)</span></span>
* <span data-ttu-id="1e3ff-131">**확인**을 탭합니다.</span><span class="sxs-lookup"><span data-stu-id="1e3ff-131">Tap **OK**</span></span>

![<span data-ttu-id="1e3ff-132">새 프로젝트 대화 상자, 왼쪽 창의 .Net core, ASP.NET Core 웹</span><span class="sxs-lookup"><span data-stu-id="1e3ff-132">New project dialog, .Net core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project2.png)


# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="1e3ff-133">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="1e3ff-133">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="1e3ff-134">**새 ASP.NET Core 웹 응용 프로그램(.NET Core) - MvcMovie** 대화 상자를 완료합니다.</span><span class="sxs-lookup"><span data-stu-id="1e3ff-134">Complete the **New ASP.NET Core Web Application (.NET Core) - MvcMovie** dialog:</span></span>

* <span data-ttu-id="1e3ff-135">버전 선택기 드롭다운 상자에서 **ASP.NET Core 2.-**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="1e3ff-135">In the version selector drop-down box select **ASP.NET Core 2.-**</span></span>
* <span data-ttu-id="1e3ff-136">**웹 응용 프로그램(모델-보기-컨트롤러)**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="1e3ff-136">Select **Web Application(Model-View-Controller)**</span></span>
* <span data-ttu-id="1e3ff-137">**확인**을 탭합니다.</span><span class="sxs-lookup"><span data-stu-id="1e3ff-137">Tap **OK**.</span></span>

![<span data-ttu-id="1e3ff-138">새 프로젝트 대화 상자, 왼쪽 창의 .Net core, ASP.NET Core 웹</span><span class="sxs-lookup"><span data-stu-id="1e3ff-138">New project dialog, .Net core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project22.png)

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="1e3ff-139">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="1e3ff-139">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="1e3ff-140">**새 ASP.NET Core 웹 응용 프로그램(.NET Core) - MvcMovie** 대화 상자를 완료합니다.</span><span class="sxs-lookup"><span data-stu-id="1e3ff-140">Complete the **New ASP.NET Core Web Application (.NET Core) - MvcMovie** dialog:</span></span>

* <span data-ttu-id="1e3ff-141">버전 선택기 드롭다운 상자에서 **ASP.NET Core 1.1**을 탭합니다.</span><span class="sxs-lookup"><span data-stu-id="1e3ff-141">In the version selector drop-down box tap **ASP.NET Core 1.1**</span></span>
* <span data-ttu-id="1e3ff-142">**웹 응용 프로그램**을 탭합니다.</span><span class="sxs-lookup"><span data-stu-id="1e3ff-142">Tap **Web Application**</span></span>
* <span data-ttu-id="1e3ff-143">기본 **인증 없음**을 유지합니다.</span><span class="sxs-lookup"><span data-stu-id="1e3ff-143">Keep the default **No Authentication**</span></span>
* <span data-ttu-id="1e3ff-144">**확인**을 탭합니다.</span><span class="sxs-lookup"><span data-stu-id="1e3ff-144">Tap **OK**.</span></span>

![새 ASP.NET Core 웹앱](start-mvc/_static/p3.png)

---

<span data-ttu-id="1e3ff-146">Visual Studio에서는 방금 만든 MVC 프로젝트에 대한 기본 템플릿을 사용했습니다.</span><span class="sxs-lookup"><span data-stu-id="1e3ff-146">Visual Studio used a default template for the MVC project you just created.</span></span> <span data-ttu-id="1e3ff-147">프로젝트 이름을 입력하고 몇 가지 옵션을 선택하면 바로 앱이 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="1e3ff-147">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="1e3ff-148">이것은 간단한 시작 프로젝트이며 여기서 시작하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="1e3ff-148">This is a simple starter project, and it's a good place to start,</span></span>

<span data-ttu-id="1e3ff-149">**F5** 키를 탭하여 앱을 디버그 모드에서 실행하거나 **Ctrl-F5**를 탭하여 디버그 이외 모드에서 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="1e3ff-149">Tap **F5** to run the app in debug mode or **Ctrl-F5** in non-debug mode.</span></span>
<!-- These images are also used by uid: tutorials/first-mvc-app-xplat/start-mvc -->
![앱 실행](start-mvc/_static/1.png)

* <span data-ttu-id="1e3ff-151">Visual Studio가 [IIS Express](http://www.iis.net/learn/extensions/introduction-to-iis-express/iis-express-overview)를 시작하고 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="1e3ff-151">Visual Studio starts [IIS Express](http://www.iis.net/learn/extensions/introduction-to-iis-express/iis-express-overview) and runs your app.</span></span> <span data-ttu-id="1e3ff-152">주소 표시줄에 `localhost:port#`이 표시되고 `example.com` 등은 표시되지 않음을 알 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1e3ff-152">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="1e3ff-153">그 이유는 `localhost`가 로컬 컴퓨터의 표준 이름이기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="1e3ff-153">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="1e3ff-154">Visual Studio에서 웹 프로젝트를 만들 경우 웹 서버에는 임의 포트가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="1e3ff-154">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="1e3ff-155">위 이미지에서 포트 번호는 5000입니다.</span><span class="sxs-lookup"><span data-stu-id="1e3ff-155">In the image above, the port number is 5000.</span></span> <span data-ttu-id="1e3ff-156">앱을 실행할 경우 다른 포트 번호가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="1e3ff-156">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="1e3ff-157">**Ctrl+F5**(디버그 이외 모드)를 사용하여 앱을 시작하면 코드를 변경하고, 파일을 저장하고, 브라우저를 새로 고치고, 코드 변경 내용을 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1e3ff-157">Launching the app with **Ctrl+F5** (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="1e3ff-158">대부분의 개발자는 앱을 빠르게 시작하고 변경 내용을 확인하기 위해 디버그 이외 모드를 사용하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="1e3ff-158">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="1e3ff-159">**디버그** 메뉴 항목에서 앱을 디버그 또는 디버그 이외 모드로 시작할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1e3ff-159">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

![디버그 메뉴](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="1e3ff-161">**IIS Express** 단추를 탭하여 앱을 디버그할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1e3ff-161">You can debug the app by tapping the **IIS Express** button</span></span>

![IIS Express](start-mvc/_static/iis_express.png)

<span data-ttu-id="1e3ff-163">기본 템플릿은 작동하는 **홈, 정보** 및 **연락처** 링크를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="1e3ff-163">The default template gives you working **Home, About** and **Contact** links.</span></span> <span data-ttu-id="1e3ff-164">위의 브라우저 이미지에는 이러한 링크가 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="1e3ff-164">The browser image above doesn't show these links.</span></span> <span data-ttu-id="1e3ff-165">브라우저 크기에 따라 탐색 아이콘을 클릭하여 링크를 표시해야 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1e3ff-165">Depending on the size of your browser, you might need to click the navigation icon to show them.</span></span>

![오른쪽 위의 탐색 아이콘](start-mvc/_static/2.png)

<span data-ttu-id="1e3ff-167">디버그 모드에서 실행 중인 경우 **Shift-F5**를 탭하여 디버깅을 중지합니다.</span><span class="sxs-lookup"><span data-stu-id="1e3ff-167">If you were running in debug mode, tap **Shift-F5** to stop debugging.</span></span>

<span data-ttu-id="1e3ff-168">이 자습서의 다음 부분에서는 MVC에 대해 알아보고 일부 코드 작성을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="1e3ff-168">In the next part of this tutorial, we'll learn about MVC and start writing some code.</span></span>

>[!div class="step-by-step"]
[<span data-ttu-id="1e3ff-169">다음</span><span class="sxs-lookup"><span data-stu-id="1e3ff-169">Next</span></span>](adding-controller.md)  
