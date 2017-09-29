---
title: "ASP.NET Core에서 브라우저 링크"
author: ncarandini
description: "어떻게 브라우저 링크는 하나 이상의 웹 브라우저와 함께 개발 환경에 연결 하는 Visual Studio 기능에 알아봅니다."
keywords: "ASP.NET Core, 브라우저 링크 CSS 동기화"
ms.author: riande
manager: wpickett
ms.date: 09/22/2017
ms.topic: article
ms.assetid: 11813d4c-3f8a-445a-b23b-e4a57d001abc
ms.technology: aspnet
ms.prod: asp.net-core
uid: client-side/using-browserlink
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 67ddc58e38962bd876050739a2a1447be4f589bb
ms.sourcegitcommit: 6e83c55eb0450a3073ef2b95fa5f5bcb20dbbf89
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/28/2017
---
# <a name="browser-link-in-aspnet-core"></a><span data-ttu-id="5400f-104">ASP.NET Core에서 브라우저 링크</span><span class="sxs-lookup"><span data-stu-id="5400f-104">Browser Link in ASP.NET Core</span></span> 

<span data-ttu-id="5400f-105">여 [Nicolò Carandini](https://github.com/ncarandini), [Mike Wasson](https://github.com/MikeWasson), 및 [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="5400f-105">By [Nicolò Carandini](https://github.com/ncarandini), [Mike Wasson](https://github.com/MikeWasson), and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="5400f-106">브라우저 링크는 Visual studio 개발 환경 및 하나 이상의 웹 브라우저 간에 통신 채널을 만드는 기능입니다.</span><span class="sxs-lookup"><span data-stu-id="5400f-106">Browser Link is a feature in Visual Studio that creates a communication channel between the development environment and one or more web browsers.</span></span> <span data-ttu-id="5400f-107">다중 브라우저 테스트에 대 한 유용한 여러 브라우저에서 웹 응용 프로그램 한 번에 새로 고치려면 브라우저 링크를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5400f-107">You can use Browser Link to refresh your web application in several browsers at once, which is useful for cross-browser testing.</span></span>

## <a name="browser-link-setup"></a><span data-ttu-id="5400f-108">브라우저 링크 설치</span><span class="sxs-lookup"><span data-stu-id="5400f-108">Browser Link setup</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="5400f-109">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="5400f-109">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="5400f-110">ASP.NET Core 2.x **웹 응용 프로그램**, **빈**, 및 **웹 API** 템플릿을 사용 하 여 프로젝트의 [Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All/) 메타 패키지에 대 한 패키지 참조가 포함 된 [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/)합니다.</span><span class="sxs-lookup"><span data-stu-id="5400f-110">The ASP.NET Core 2.x **Web Application**, **Empty**, and **Web API** template projects use the [Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All/) meta-package, which contains a package reference for [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/).</span></span> <span data-ttu-id="5400f-111">따라서 사용 하는 `Microsoft.AspNetCore.All` 메타 패키지 브라우저 링크를 사용 하기 위해 사용할 수 있도록 하려면 추가 작업이 없으므로 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="5400f-111">Therefore, using the `Microsoft.AspNetCore.All` meta-package requires no further action to make Browser Link available for use.</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="5400f-112">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="5400f-112">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="5400f-113">ASP.NET Core 1.x **웹 응용 프로그램** 프로젝트 템플릿에에 대 한 패키지 참조는 [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) 패키지 합니다.</span><span class="sxs-lookup"><span data-stu-id="5400f-113">The ASP.NET Core 1.x **Web Application** project template has a package reference for the [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) package.</span></span> <span data-ttu-id="5400f-114">**빈** 또는 **웹 API** 서식 파일 프로젝트에 패키지 참조를 추가 해야 `Microsoft.VisualStudio.Web.BrowserLink`합니다.</span><span class="sxs-lookup"><span data-stu-id="5400f-114">The **Empty** or **Web API** template projects require you to add a package reference to `Microsoft.VisualStudio.Web.BrowserLink`.</span></span>

<span data-ttu-id="5400f-115">이 Visual Studio 기능을 가장 쉬운 방법은 패키지를 추가 하는 **빈** 또는 **웹 API** 서식 파일 프로젝트를 여는 것은 **패키지 관리자 콘솔** (**보기** > **다른 창** > **패키지 관리자 콘솔**) 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="5400f-115">Since this is a Visual Studio feature, the easiest way to add the package to an **Empty** or **Web API** template project is to open the **Package Manager Console** (**View** > **Other Windows** > **Package Manager Console**) and run the following command:</span></span>

```console
install-package Microsoft.VisualStudio.Web.BrowserLink
```

<span data-ttu-id="5400f-116">사용할 수 있습니다 **NuGet 패키지 관리자**합니다.</span><span class="sxs-lookup"><span data-stu-id="5400f-116">Alternatively, you can use **NuGet Package Manager**.</span></span> <span data-ttu-id="5400f-117">프로젝트 이름을 마우스 오른쪽 단추로 클릭 **솔루션 탐색기** 선택 **NuGet 패키지 관리**:</span><span class="sxs-lookup"><span data-stu-id="5400f-117">Right-click the project name in **Solution Explorer** and choose **Manage NuGet Packages**:</span></span>

![열기 NuGet 패키지 관리자](using-browserlink/_static/open-nuget-package-manager.png)

<span data-ttu-id="5400f-119">찾기 및 패키지를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="5400f-119">Find and install the package:</span></span>

![NuGet 패키지 관리자를 사용 하 여 패키지를 추가 합니다.](using-browserlink/_static/add-package-with-nuget-package-manager.png)

---

### <a name="configuration"></a><span data-ttu-id="5400f-121">구성</span><span class="sxs-lookup"><span data-stu-id="5400f-121">Configuration</span></span>

<span data-ttu-id="5400f-122">에 `Configure` 의 메서드는 *Startup.cs* 파일:</span><span class="sxs-lookup"><span data-stu-id="5400f-122">In the `Configure` method of the *Startup.cs* file:</span></span>

```csharp
app.UseBrowserLink();
```

<span data-ttu-id="5400f-123">내 코드는 일반적으로 `if` 아래 그림과 같이 개발 환경에서 브라우저 링크의 사용 여부만 블록:</span><span class="sxs-lookup"><span data-stu-id="5400f-123">Usually the code is inside an `if` block that only enables Browser Link in the Development environment, as shown here:</span></span>

```csharp
if (env.IsDevelopment())
{
    app.UseDeveloperExceptionPage();
    app.UseBrowserLink();
}
```

<span data-ttu-id="5400f-124">자세한 내용은 [여러 환경 사용](xref:fundamentals/environments)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5400f-124">For more information, see [Working with Multiple Environments](xref:fundamentals/environments).</span></span>

## <a name="how-to-use-browser-link"></a><span data-ttu-id="5400f-125">브라우저 링크를 사용 하는 방법</span><span class="sxs-lookup"><span data-stu-id="5400f-125">How to use Browser Link</span></span>

<span data-ttu-id="5400f-126">Visual Studio 브라우저 링크 도구 모음 컨트롤 옆에 표시 ASP.NET Core 프로젝트가 열려 있는 경우는 **디버그 대상** 도구 모음 컨트롤:</span><span class="sxs-lookup"><span data-stu-id="5400f-126">When you have an ASP.NET Core project open, Visual Studio shows the Browser Link toolbar control next to the **Debug Target** toolbar control:</span></span>

![브라우저 링크 드롭 다운 메뉴](using-browserlink/_static/browserLink-dropdown-menu.png)

<span data-ttu-id="5400f-128">브라우저 링크 도구 모음 컨트롤에서 다음을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5400f-128">From the Browser Link toolbar control, you can:</span></span>

* <span data-ttu-id="5400f-129">여러 브라우저에서 웹 응용 프로그램을 한 번에 새로 고칩니다.</span><span class="sxs-lookup"><span data-stu-id="5400f-129">Refresh the web application in several browsers at once.</span></span>
* <span data-ttu-id="5400f-130">열기는 **브라우저 링크 대시보드에**합니다.</span><span class="sxs-lookup"><span data-stu-id="5400f-130">Open the **Browser Link Dashboard**.</span></span>
* <span data-ttu-id="5400f-131">또는 사용 안 함 **브라우저 링크**합니다.</span><span class="sxs-lookup"><span data-stu-id="5400f-131">Enable or disable **Browser Link**.</span></span> <span data-ttu-id="5400f-132">참고: 브라우저 링크는 Visual Studio 2017 (15.3)에서 기본적으로 비활성화 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5400f-132">Note: Browser Link is disabled by default in Visual Studio 2017 (15.3).</span></span>
* <span data-ttu-id="5400f-133">CSS 자동 동기화를 사용 하지 않도록 설정 하거나 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="5400f-133">Enable or disable CSS Auto-Sync.</span></span>

> [!NOTE]
> <span data-ttu-id="5400f-134">일부 Visual Studio 플러그 인, 특히 *웹 확장 팩 2015* 및 *웹 확장 팩 2017*, 브라우저 링크에 대 한 확장 된 기능을 제공 하지만 추가 기능 중 일부는 asp 작동 하지 않습니다. NET 핵심 프로젝트입니다.</span><span class="sxs-lookup"><span data-stu-id="5400f-134">Some Visual Studio plug-ins, most notably *Web Extension Pack 2015* and *Web Extension Pack 2017*, offer extended functionality for Browser Link, but some of the additional features don't work with ASP.NET Core projects.</span></span>

## <a name="refresh-the-web-application-in-several-browsers-at-once"></a><span data-ttu-id="5400f-135">여러 브라우저에서 웹 응용 프로그램 한 번에 새로 고침</span><span class="sxs-lookup"><span data-stu-id="5400f-135">Refresh the web application in several browsers at once</span></span>

<span data-ttu-id="5400f-136">에 있는 드롭 다운 메뉴를 사용 하 여 프로젝트를 시작할 때 시작 하려면 단일 웹 브라우저를 선택 하 고 **디버그 대상** 도구 모음 컨트롤:</span><span class="sxs-lookup"><span data-stu-id="5400f-136">To choose a single web browser to launch when starting the project, use the drop-down menu in the **Debug Target** toolbar control:</span></span>

![F 5를 눌러 드롭다운 메뉴](using-browserlink/_static/debug-target-dropdown-menu.png)

<span data-ttu-id="5400f-138">한 번에 여러 브라우저를 열려면 선택 **브라우저 선택...**  동일한 드롭다운 목록에서 합니다.</span><span class="sxs-lookup"><span data-stu-id="5400f-138">To open multiple browsers at once, choose **Browse with...** from the same drop-down.</span></span> <span data-ttu-id="5400f-139">원하는 브라우저를 선택 하려면 CTRL 키를 누른 채 클릭 **찾아보기**:</span><span class="sxs-lookup"><span data-stu-id="5400f-139">Hold down the CTRL key to select the browsers you want, and then click **Browse**:</span></span>

![한 번에 다양 한 브라우저를 열으십시오](using-browserlink/_static/open-many-browsers-at-once.png)

<span data-ttu-id="5400f-141">인덱스 보기가 표시 된 Visual Studio를 열고 보여 주는 스크린샷 및 두 개의 열린 브라우저 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="5400f-141">Here's a screenshot showing Visual Studio with the Index view open and two open browsers:</span></span>

![브라우저는 예제와 동기화](using-browserlink/_static/sync-with-two-browsers-example.png)

<span data-ttu-id="5400f-143">프로젝트에 연결 된 브라우저를 보려면 브라우저 링크 도구 모음 컨트롤을 마우스로:</span><span class="sxs-lookup"><span data-stu-id="5400f-143">Hover over the Browser Link toolbar control to see the browsers that are connected to the project:</span></span>

![Hover 팁](using-browserlink/_static/hoover-tip.png)

<span data-ttu-id="5400f-145">인덱스 뷰를 변경 하 고 연결 된 모든 브라우저 브라우저 링크 새로 고침 단추를 클릭할 때 업데이트 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5400f-145">Change the Index view, and all connected browsers are updated when you click the Browser Link refresh button:</span></span>

![변경 하는 브라우저 동기화](using-browserlink/_static/browsers-sync-to-changes.png)

<span data-ttu-id="5400f-147">브라우저 링크는 Visual Studio 외부에서 시작 하 고 응용 프로그램 URL로 이동 하는 브라우저 에서도 작동 합니다.</span><span class="sxs-lookup"><span data-stu-id="5400f-147">Browser Link also works with browsers that you launch from outside Visual Studio and navigate to the application URL.</span></span>

### <a name="the-browser-link-dashboard"></a><span data-ttu-id="5400f-148">브라우저 링크 대시보드</span><span class="sxs-lookup"><span data-stu-id="5400f-148">The Browser Link Dashboard</span></span>

<span data-ttu-id="5400f-149">브라우저 링크에 대 한 드롭다운 메뉴를 열고 브라우저와 연결을 관리에서 브라우저 링크 대시보드를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="5400f-149">Open the Browser Link Dashboard from the Browser Link drop down menu to manage the connection with open browsers:</span></span>

![오픈 browserslink 대시보드](using-browserlink/_static/open-browserlink-dashboard.png)

<span data-ttu-id="5400f-151">브라우저가 없으므로 연결 되어 있는 경우 선택 하 여 비-디버깅 세션을 시작할 수 있습니다는 *브라우저에서 보기* 링크:</span><span class="sxs-lookup"><span data-stu-id="5400f-151">If no browser is connected, you can start a non-debugging session by selecting the *View in Browser* link:</span></span>

![browserlink 대시보드-no 연결](using-browserlink/_static/browserlink-dashboard-no-connections.png)

<span data-ttu-id="5400f-153">그렇지 않으면 연결 된 브라우저와 각 브라우저 표시 하는 페이지의 경로를 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5400f-153">Otherwise, the connected browsers are shown with the path to the page that each browser is showing:</span></span>

![browserlink 대시보드-2 연결](using-browserlink/_static/browserlink-dashboard-two-connections.png)

<span data-ttu-id="5400f-155">원하는 경우 해당 단일 브라우저를 새로 고치려면 나열 된 브라우저 이름을 클릭할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5400f-155">If you like, you can click on a listed browser name to refresh that single browser.</span></span>

### <a name="enable-or-disable-browser-link"></a><span data-ttu-id="5400f-156">브라우저 링크를 사용할지 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="5400f-156">Enable or disable Browser Link</span></span>

<span data-ttu-id="5400f-157">사용 중지 한 후 브라우저 링크를 다시 활성화 하면 브라우저에 다시 연결을 새로 고쳐야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5400f-157">When you re-enable Browser Link after disabling it, you must refresh the browsers to reconnect them.</span></span>

### <a name="enable-or-disable-css-auto-sync"></a><span data-ttu-id="5400f-158">CSS 자동 동기화를 사용할지 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="5400f-158">Enable or disable CSS Auto-Sync</span></span>

<span data-ttu-id="5400f-159">CSS 자동 동기화를 사용 하는 CSS 파일에 변경 내용을 확인 하는 경우 자동으로 연결 된 브라우저가 새로 고쳐지지 합니다.</span><span class="sxs-lookup"><span data-stu-id="5400f-159">When CSS Auto-Sync is enabled, connected browsers are automatically refreshed when you make any change to CSS files.</span></span>

## <a name="how-does-it-work"></a><span data-ttu-id="5400f-160">작동 방식</span><span class="sxs-lookup"><span data-stu-id="5400f-160">How does it work?</span></span>

<span data-ttu-id="5400f-161">브라우저 링크 SignalR를 사용 하 여 Visual Studio 및 브라우저 간 통신 채널을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="5400f-161">Browser Link uses SignalR to create a communication channel between Visual Studio and the browser.</span></span> <span data-ttu-id="5400f-162">브라우저 링크를 사용 하는 Visual Studio는 여러 명의 클라이언트 (브라우저)에 연결할 수 있는 SignalR 서버로 작동 합니다.</span><span class="sxs-lookup"><span data-stu-id="5400f-162">When Browser Link is enabled, Visual Studio acts as a SignalR server that multiple clients (browsers) can connect to.</span></span> <span data-ttu-id="5400f-163">또한 브라우저 링크는 ASP.NET 요청 파이프라인에 미들웨어 구성 요소를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="5400f-163">Browser Link also registers a middleware component in the ASP.NET request pipeline.</span></span> <span data-ttu-id="5400f-164">이 구성 요소를 특수 삽입 `<script>` 서버에서 모든 페이지 요청에 대 한 참조입니다.</span><span class="sxs-lookup"><span data-stu-id="5400f-164">This component injects special `<script>` references into every page request from the server.</span></span> <span data-ttu-id="5400f-165">선택 하 여 스크립트 참조를 볼 수 **소스 보기** 브라우저와의 끝으로 스크롤하면에 `<body>` 콘텐츠에 태그:</span><span class="sxs-lookup"><span data-stu-id="5400f-165">You can see the script references by selecting **View source** in the browser and scrolling to the end of the `<body>` tag content:</span></span>

```javascript
    <!-- Visual Studio Browser Link -->
    <script type="application/json" id="__browserLink_initializationData">
        {"requestId":"a717d5a07c1741949a7cefd6fa2bad08","requestMappingFromServer":false}
    </script>
    <script type="text/javascript" src="http://localhost:54139/b6e36e429d034f578ebccd6a79bf19bf/browserLink" async="async"></script>
    <!-- End Browser Link -->
</body>
```

<span data-ttu-id="5400f-166">소스 파일이 수정 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5400f-166">Your source files aren't modified.</span></span> <span data-ttu-id="5400f-167">미들웨어 구성 요소는 스크립트 참조를 동적으로 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="5400f-167">The middleware component injects the script references dynamically.</span></span> 

<span data-ttu-id="5400f-168">브라우저 쪽 코드는 모든 JavaScript 이기 때문에 브라우저 플러그 인을 없이 SignalR를 지 원하는 모든 브라우저에서 작동 합니다.</span><span class="sxs-lookup"><span data-stu-id="5400f-168">Because the browser-side code is all JavaScript, it works on all browsers that SignalR supports without requiring a browser plug-in.</span></span>
