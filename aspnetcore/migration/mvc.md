---
title: "ASP.NET MVC에서 ASP.NET Core MVC로 마이그레이션"
author: ardalis
description: 
keywords: "ASP.NET Core, MVC, 마이그레이션"
ms.author: riande
manager: wpickett
ms.date: 03/07/2017
ms.topic: article
ms.assetid: 3155cc9e-d0c9-424b-886c-35c0ec6f9f4e
ms.technology: aspnet
ms.prod: asp.net-core
uid: migration/mvc
ms.openlocfilehash: 7a4357da4cc97d7c60cc7e309add7583ef096597
ms.sourcegitcommit: 8f4d4fad1ca27adf9e396f5c205c9875a3963664
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/13/2017
---
# <a name="migrating-from-aspnet-mvc-to-aspnet-core-mvc"></a><span data-ttu-id="98f15-103">ASP.NET MVC에서 ASP.NET Core MVC로 마이그레이션</span><span class="sxs-lookup"><span data-stu-id="98f15-103">Migrating From ASP.NET MVC to ASP.NET Core MVC</span></span>

<span data-ttu-id="98f15-104">여 [Rick Anderson](https://twitter.com/RickAndMSFT), [김 Roth](https://github.com/danroth27), [Steve Smith](https://ardalis.com/), 및 [Scott Addie](https://scottaddie.com)</span><span class="sxs-lookup"><span data-stu-id="98f15-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27), [Steve Smith](https://ardalis.com/), and [Scott Addie](https://scottaddie.com)</span></span>

<span data-ttu-id="98f15-105">이 문서에서는 마이그레이션하는 ASP.NET MVC 프로젝트를 시작 하는 방법을 보여 줍니다. [ASP.NET Core MVC](../mvc/overview.md)합니다.</span><span class="sxs-lookup"><span data-stu-id="98f15-105">This article shows how to get started migrating an ASP.NET MVC project to [ASP.NET Core MVC](../mvc/overview.md).</span></span> <span data-ttu-id="98f15-106">프로세스에서 강조 표시 다양 한 ASP.NET MVC에서 변경 된 사항입니다.</span><span class="sxs-lookup"><span data-stu-id="98f15-106">In the process, it highlights many of the things that have changed from ASP.NET MVC.</span></span> <span data-ttu-id="98f15-107">ASP.NET MVC에서 마이그레이션 프로세스는 여러 단계 이며이 문서에서는 초기 설정, 기본 컨트롤러와 뷰, 정적 콘텐츠 및 클라이언트 쪽 종속성에 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="98f15-107">Migrating from ASP.NET MVC is a multiple step process and this article covers the initial setup, basic controllers and views, static content, and client-side dependencies.</span></span> <span data-ttu-id="98f15-108">추가 문서 구성 마이그레이션 및 identity 코드 많은 ASP.NET MVC 프로젝트에 다룹니다.</span><span class="sxs-lookup"><span data-stu-id="98f15-108">Additional articles cover migrating configuration and identity code found in many ASP.NET MVC projects.</span></span>

> [!NOTE]
> <span data-ttu-id="98f15-109">샘플에서 버전 번호는 현재 아닐 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="98f15-109">The version numbers in the samples might not be current.</span></span> <span data-ttu-id="98f15-110">프로젝트를 적절 하 게 업데이트 해야 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="98f15-110">You may need to update your projects accordingly.</span></span>

## <a name="create-the-starter-aspnet-mvc-project"></a><span data-ttu-id="98f15-111">스타터 ASP.NET MVC 프로젝트 만들기</span><span class="sxs-lookup"><span data-stu-id="98f15-111">Create the starter ASP.NET MVC project</span></span>

<span data-ttu-id="98f15-112">업그레이드를 보여 주기 위해 ASP.NET MVC 앱을 만들어 시작 합니다.</span><span class="sxs-lookup"><span data-stu-id="98f15-112">To demonstrate the upgrade, we'll start by creating a ASP.NET MVC app.</span></span> <span data-ttu-id="98f15-113">만들 이름의 *WebApp1* 되므로 네임 스페이스는 다음 단계에서 생성 하는 ASP.NET Core 프로젝트와 일치 합니다.</span><span class="sxs-lookup"><span data-stu-id="98f15-113">Create it with the name *WebApp1* so the namespace will match the ASP.NET Core project we create in the next step.</span></span>

![Visual Studio 새 프로젝트 대화 상자](mvc/_static/new-project.png)

![새 웹 응용 프로그램 대화 상자: ASP.NET 템플릿 패널에서 선택한 MVC 프로젝트 템플릿](mvc/_static/new-project-select-mvc-template.png)

<span data-ttu-id="98f15-116">*선택 사항:* 에서 솔루션의 이름을 변경 *WebApp1* 를 *m v c 5*합니다.</span><span class="sxs-lookup"><span data-stu-id="98f15-116">*Optional:* Change the name of the Solution from *WebApp1* to *Mvc5*.</span></span> <span data-ttu-id="98f15-117">Visual Studio 새 솔루션 이름으로 표시 됩니다 (*m v c 5*)는 쉽게 다음 프로젝트에서이 프로젝트를 알 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="98f15-117">Visual Studio will display the new solution name (*Mvc5*), which will make it easier to tell this project from the next project.</span></span>

## <a name="create-the-aspnet-core-project"></a><span data-ttu-id="98f15-118">ASP.NET Core 프로젝트 만들기</span><span class="sxs-lookup"><span data-stu-id="98f15-118">Create the ASP.NET Core project</span></span>

<span data-ttu-id="98f15-119">새 *빈* 이전 프로젝트와 이름이 같은 ASP.NET Core 웹 앱 (*WebApp1*) 되므로 두 프로젝트의 네임 스페이스와 일치 합니다.</span><span class="sxs-lookup"><span data-stu-id="98f15-119">Create a new *empty* ASP.NET Core web app with the same name as the previous project (*WebApp1*) so the namespaces in the two projects match.</span></span> <span data-ttu-id="98f15-120">동일한 네임 스페이스 하면를 손쉽게 두 프로젝트 간에 코드를 복사할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="98f15-120">Having the same namespace makes it easier to copy code between the two projects.</span></span> <span data-ttu-id="98f15-121">동일한 이름을 사용 하 여 이전 프로젝트 이외의 다른 디렉터리에서이 프로젝트를 만드는 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="98f15-121">You'll have to create this project in a different directory than the previous project to use the same name.</span></span>

![새 프로젝트 대화 상자](mvc/_static/new_core.png)

![새 ASP.NET 웹 응용 프로그램 대화 상자: ASP.NET Core 템플릿 패널에서 선택 하는 빈 프로젝트 템플릿](mvc/_static/new-project-select-empty-aspnet5-template.png)

* <span data-ttu-id="98f15-124">*선택 사항:* 사용 하 여 새 ASP.NET Core 응용 프로그램 만들기는 *웹 응용 프로그램* 서식 파일 프로젝트.</span><span class="sxs-lookup"><span data-stu-id="98f15-124">*Optional:* Create a new ASP.NET Core app using the *Web Application* project template.</span></span> <span data-ttu-id="98f15-125">프로젝트 이름을 *WebApp1*의 인증 옵션을 선택 하 고 **개별 사용자 계정**합니다.</span><span class="sxs-lookup"><span data-stu-id="98f15-125">Name the project *WebApp1*, and select an authentication option of **Individual User Accounts**.</span></span> <span data-ttu-id="98f15-126">이 앱을 이름 바꾸기 *FullAspNetCore*합니다.</span><span class="sxs-lookup"><span data-stu-id="98f15-126">Rename this app to *FullAspNetCore*.</span></span> <span data-ttu-id="98f15-127">이 프로젝트에는 시간을 절약할 수 변환을 만드는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="98f15-127">Creating this project will save you time in the conversion.</span></span> <span data-ttu-id="98f15-128">최종 결과 확인 하거나 변환 프로젝트에 코드를 복사 하려면 서식 파일에서 생성 된 코드를 살펴볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="98f15-128">You can look at the template-generated code to see the end result or to copy code to the conversion project.</span></span> <span data-ttu-id="98f15-129">템플릿에서 생성 된 프로젝트와 비교할 개체 변환 단계에 갇 하는 경우에 도움이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="98f15-129">It's also helpful when you get stuck on a conversion step to compare with the template-generated project.</span></span>

## <a name="configure-the-site-to-use-mvc"></a><span data-ttu-id="98f15-130">MVC를 사용 하도록 사이트 구성</span><span class="sxs-lookup"><span data-stu-id="98f15-130">Configure the site to use MVC</span></span>

* <span data-ttu-id="98f15-131">설치는 `Microsoft.AspNetCore.Mvc` 및 `Microsoft.AspNetCore.StaticFiles` NuGet 패키지 합니다.</span><span class="sxs-lookup"><span data-stu-id="98f15-131">Install the `Microsoft.AspNetCore.Mvc` and `Microsoft.AspNetCore.StaticFiles` NuGet packages.</span></span>

  <span data-ttu-id="98f15-132">`Microsoft.AspNetCore.Mvc`ASP.NET Core MVC 프레임 워크가입니다.</span><span class="sxs-lookup"><span data-stu-id="98f15-132">`Microsoft.AspNetCore.Mvc` is the ASP.NET Core MVC framework.</span></span> <span data-ttu-id="98f15-133">`Microsoft.AspNetCore.StaticFiles`정적 파일 처리기입니다.</span><span class="sxs-lookup"><span data-stu-id="98f15-133">`Microsoft.AspNetCore.StaticFiles` is the static file handler.</span></span> <span data-ttu-id="98f15-134">ASP.NET 런타임은 모듈식 및 정적 파일을 제공 하려면에 명시적으로 선택 해야 합니다 (참조 [정적 파일 작업](../fundamentals/static-files.md)).</span><span class="sxs-lookup"><span data-stu-id="98f15-134">The ASP.NET runtime is modular, and you must explicitly opt in to serve static files (see [Working with Static Files](../fundamentals/static-files.md)).</span></span>

* <span data-ttu-id="98f15-135">열기는 *.csproj* 파일 (에서 프로젝트를 마우스 오른쪽 단추로 클릭 **솔루션 탐색기** 선택 **편집 WebApp1.csproj**) 추가 하 고는 `PrepareForPublish` 대상:</span><span class="sxs-lookup"><span data-stu-id="98f15-135">Open the *.csproj* file (right-click the project in **Solution Explorer** and select **Edit WebApp1.csproj**) and add a `PrepareForPublish` target:</span></span>

  [!code-xml[Main](mvc/sample/WebApp1.csproj?range=21-23)]

  <span data-ttu-id="98f15-136">`PrepareForPublish` 대상 Bower 통한 클라이언트 라이브러리를 획득 하기 위해 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="98f15-136">The `PrepareForPublish` target is needed for acquiring client-side libraries via Bower.</span></span> <span data-ttu-id="98f15-137">알아보겠습니다이 대해서는 나중에.</span><span class="sxs-lookup"><span data-stu-id="98f15-137">We'll talk about that later.</span></span>

* <span data-ttu-id="98f15-138">열기는 *Startup.cs* 파일을 다음과 일치 하도록 코드를 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="98f15-138">Open the *Startup.cs* file and change the code to match the following:</span></span>

  [!code-csharp[Main](mvc/sample/Startup.cs?highlight=14,27-34)]

  <span data-ttu-id="98f15-139">`UseStaticFiles` 확장 메서드는 정적 파일 처리기를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="98f15-139">The `UseStaticFiles` extension method adds the static file handler.</span></span> <span data-ttu-id="98f15-140">ASP.NET 런타임이 모듈식 앞에서 설명한 대로 및 정적 파일을 제공 하려면에 명시적으로 선택 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="98f15-140">As mentioned previously, the ASP.NET runtime is modular, and you must explicitly opt in to serve static files.</span></span> <span data-ttu-id="98f15-141">`UseMvc` 라우팅 확장 메서드를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="98f15-141">The `UseMvc` extension method adds routing.</span></span> <span data-ttu-id="98f15-142">자세한 내용은 참조 [응용 프로그램 시작](../fundamentals/startup.md) 및 [라우팅](../fundamentals/routing.md)합니다.</span><span class="sxs-lookup"><span data-stu-id="98f15-142">For more information, see [Application Startup](../fundamentals/startup.md) and [Routing](../fundamentals/routing.md).</span></span>

## <a name="add-a-controller-and-view"></a><span data-ttu-id="98f15-143">컨트롤러와 뷰를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="98f15-143">Add a controller and view</span></span>

<span data-ttu-id="98f15-144">이 섹션에서는 최소한의 컨트롤러 및 ASP.NET MVC 컨트롤러에 대 한 자리 표시자로 사용 하는 보기 및 보기는 다음 섹션에서 마이그레이션할 수를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="98f15-144">In this section, you'll add a minimal controller and view to serve as placeholders for the ASP.NET MVC controller and views you'll migrate in the next section.</span></span>

* <span data-ttu-id="98f15-145">추가 *컨트롤러* 폴더입니다.</span><span class="sxs-lookup"><span data-stu-id="98f15-145">Add a *Controllers* folder.</span></span>

* <span data-ttu-id="98f15-146">추가 **MVC 컨트롤러 클래스** 이름의 *HomeController.cs* 에 *컨트롤러* 폴더입니다.</span><span class="sxs-lookup"><span data-stu-id="98f15-146">Add an **MVC controller class** with the name *HomeController.cs* to the *Controllers* folder.</span></span>

![새 항목 추가 대화 상자](mvc/_static/add_mvc_ctl.png)

* <span data-ttu-id="98f15-148">추가 *뷰* 폴더입니다.</span><span class="sxs-lookup"><span data-stu-id="98f15-148">Add a *Views* folder.</span></span>

* <span data-ttu-id="98f15-149">추가 *뷰/홈* 폴더입니다.</span><span class="sxs-lookup"><span data-stu-id="98f15-149">Add a *Views/Home* folder.</span></span>

* <span data-ttu-id="98f15-150">추가 *Index.cshtml* 에 MVC 뷰 페이지는 *뷰/홈* 폴더입니다.</span><span class="sxs-lookup"><span data-stu-id="98f15-150">Add an *Index.cshtml* MVC view page to the *Views/Home* folder.</span></span>

![새 항목 추가 대화 상자](mvc/_static/view.png)

<span data-ttu-id="98f15-152">프로젝트 구조는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="98f15-152">The project structure is shown below:</span></span>

![파일 및 폴더의 WebApp1 나타내는 솔루션 탐색기](mvc/_static/project-structure-controller-view.png)

<span data-ttu-id="98f15-154">내용을 대체는 *Views/Home/Index.cshtml* 다음 파일:</span><span class="sxs-lookup"><span data-stu-id="98f15-154">Replace the contents of the *Views/Home/Index.cshtml* file with the following:</span></span>

```html
<h1>Hello world!</h1>
```

<span data-ttu-id="98f15-155">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="98f15-155">Run the app.</span></span>

![웹 응용 프로그램에서 Microsoft Edge 열기](mvc/_static/hello-world.png)

<span data-ttu-id="98f15-157">참조 [컨트롤러](../mvc/controllers/index.md) 및 [뷰](../mvc/views/index.md) 자세한 정보에 대 한 합니다.</span><span class="sxs-lookup"><span data-stu-id="98f15-157">See [Controllers](../mvc/controllers/index.md) and [Views](../mvc/views/index.md) for more information.</span></span>

<span data-ttu-id="98f15-158">최소 작업 ASP.NET Core 프로젝트를 만들었으므로 이제 해당 ASP.NET MVC 프로젝트에서 기능을 마이그레이션할 시작할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="98f15-158">Now that we have a minimal working ASP.NET Core project, we can start migrating functionality from the ASP.NET MVC project.</span></span> <span data-ttu-id="98f15-159">다음 이동 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="98f15-159">We will need to move the following:</span></span>

* <span data-ttu-id="98f15-160">클라이언트 쪽 콘텐츠 (CSS, 글꼴 및 스크립트)</span><span class="sxs-lookup"><span data-stu-id="98f15-160">client-side content (CSS, fonts, and scripts)</span></span>

* <span data-ttu-id="98f15-161">컨트롤러</span><span class="sxs-lookup"><span data-stu-id="98f15-161">controllers</span></span>

* <span data-ttu-id="98f15-162">뷰</span><span class="sxs-lookup"><span data-stu-id="98f15-162">views</span></span>

* <span data-ttu-id="98f15-163">모델</span><span class="sxs-lookup"><span data-stu-id="98f15-163">models</span></span>

* <span data-ttu-id="98f15-164">번들</span><span class="sxs-lookup"><span data-stu-id="98f15-164">bundling</span></span>

* <span data-ttu-id="98f15-165">필터</span><span class="sxs-lookup"><span data-stu-id="98f15-165">filters</span></span>

* <span data-ttu-id="98f15-166">In/out 로그, id (이 수행 됩니다 다음 자습서에서.)</span><span class="sxs-lookup"><span data-stu-id="98f15-166">Log in/out, identity (This will be done in the next tutorial.)</span></span>

## <a name="controllers-and-views"></a><span data-ttu-id="98f15-167">컨트롤러와 뷰</span><span class="sxs-lookup"><span data-stu-id="98f15-167">Controllers and views</span></span>

* <span data-ttu-id="98f15-168">ASP.NET MVC에서 복사 된 각 메서드의 `HomeController` 새 `HomeController`합니다.</span><span class="sxs-lookup"><span data-stu-id="98f15-168">Copy each of the methods from the ASP.NET MVC `HomeController` to the new `HomeController`.</span></span> <span data-ttu-id="98f15-169">ASP.NET MVC에서 기본 제공 템플릿의 컨트롤러 동작 메서드 반환 형식은 [ActionResult](https://msdn.microsoft.com/library/system.web.mvc.actionresult(v=vs.118).aspx); ASP.NET Core mvc에서 반환 하는 작업 메서드 `IActionResult` 대신 합니다.</span><span class="sxs-lookup"><span data-stu-id="98f15-169">Note that in ASP.NET MVC, the built-in template's controller action method return type is [ActionResult](https://msdn.microsoft.com/library/system.web.mvc.actionresult(v=vs.118).aspx); in ASP.NET Core MVC, the action methods return `IActionResult` instead.</span></span> <span data-ttu-id="98f15-170">`ActionResult`구현 `IActionResult`이므로 동작 메서드의 반환 형식을 변경할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="98f15-170">`ActionResult` implements `IActionResult`, so there is no need to change the return type of your action methods.</span></span>

* <span data-ttu-id="98f15-171">복사는 *About.cshtml*, *Contact.cshtml*, 및 *Index.cshtml* Razor 파일 보기 ASP.NET MVC 프로젝트에서 ASP.NET Core 프로젝트에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="98f15-171">Copy the *About.cshtml*, *Contact.cshtml*, and *Index.cshtml* Razor view files from the ASP.NET MVC project to the ASP.NET Core project.</span></span>

* <span data-ttu-id="98f15-172">ASP.NET Core 응용 프로그램을 실행 하 고 각 메서드를 테스트 합니다.</span><span class="sxs-lookup"><span data-stu-id="98f15-172">Run the ASP.NET Core app and test each method.</span></span> <span data-ttu-id="98f15-173">하지 않은 마이그레이션할 레이아웃 파일 또는 스타일 아직 있으므로 렌더링 된 뷰 보기 파일의 콘텐츠를만 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="98f15-173">We haven't migrated the layout file or styles yet, so the rendered views will only contain the content in the view files.</span></span> <span data-ttu-id="98f15-174">에 대 한 레이아웃 파일 생성 된 링크를 가질 수 없습니다는 `About` 및 `Contact` 브라우저에서이 호출 하도록 설정 해야 하므로 뷰 (대체 **4492** 프로젝트에서 사용 하는 포트 번호로 바꿉니다).</span><span class="sxs-lookup"><span data-stu-id="98f15-174">You won't have the layout file generated links for the `About` and `Contact` views, so you'll have to invoke them from the browser (replace **4492** with the port number used in your project).</span></span>

  * `http://localhost:4492/home/about`

  * `http://localhost:4492/home/contact`

![연락처 페이지](mvc/_static/contact-page.png)

<span data-ttu-id="98f15-176">Note 스타일 지정 및 메뉴 항목의 부족 합니다.</span><span class="sxs-lookup"><span data-stu-id="98f15-176">Note the lack of styling and menu items.</span></span> <span data-ttu-id="98f15-177">다음 섹션에서이 문제를 해결 합니다 했습니다.</span><span class="sxs-lookup"><span data-stu-id="98f15-177">We'll fix that in the next section.</span></span>

## <a name="static-content"></a><span data-ttu-id="98f15-178">정적 콘텐츠</span><span class="sxs-lookup"><span data-stu-id="98f15-178">Static content</span></span>

<span data-ttu-id="98f15-179">이전 버전의 ASP.NET MVC에서는 정적 콘텐츠 웹 프로젝트의 루트에서 호스팅된 및 서버 쪽 파일와 결합 된 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="98f15-179">In previous versions of  ASP.NET MVC, static content was hosted from the root of the web project and was intermixed with server-side files.</span></span> <span data-ttu-id="98f15-180">ASP.NET Core 정적 콘텐츠가 담긴에서 호스트 되는 *wwwroot* 폴더입니다.</span><span class="sxs-lookup"><span data-stu-id="98f15-180">In ASP.NET Core, static content is hosted in the *wwwroot* folder.</span></span> <span data-ttu-id="98f15-181">정적 콘텐츠를 이전 ASP.NET MVC 응용 프로그램에서 복사 하려는 *wwwroot* ASP.NET Core 프로젝트의 폴더에에서 있습니다.</span><span class="sxs-lookup"><span data-stu-id="98f15-181">You'll want to copy the static content from your old ASP.NET MVC app to the *wwwroot* folder in your ASP.NET Core project.</span></span> <span data-ttu-id="98f15-182">이 샘플 구현:</span><span class="sxs-lookup"><span data-stu-id="98f15-182">In this sample conversion:</span></span>

* <span data-ttu-id="98f15-183">복사는 *favicon.ico* 파일에 이전 MVC 프로젝트에서는 *wwwroot* ASP.NET Core 프로젝트의 폴더에에서 있습니다.</span><span class="sxs-lookup"><span data-stu-id="98f15-183">Copy the *favicon.ico* file from the old MVC project to the *wwwroot* folder in the ASP.NET Core project.</span></span>

<span data-ttu-id="98f15-184">ASP.NET MVC 이전 프로젝트에서 사용 [부트스트랩](http://getbootstrap.com/) 부트스트랩에 파일의 스타일 지정 및 저장소는 *콘텐츠* 및 *스크립트* 폴더입니다.</span><span class="sxs-lookup"><span data-stu-id="98f15-184">The old ASP.NET MVC project uses [Bootstrap](http://getbootstrap.com/) for its styling and stores the Bootstrap files in the *Content* and *Scripts* folders.</span></span> <span data-ttu-id="98f15-185">부트스트랩 레이아웃 파일에서 참조 하는 이전 ASP.NET MVC 프로젝트를 생성 하는 템플릿을 (*Views/Shared/_Layout.cshtml*).</span><span class="sxs-lookup"><span data-stu-id="98f15-185">The template, which generated the old ASP.NET MVC project, references Bootstrap in the layout file (*Views/Shared/_Layout.cshtml*).</span></span> <span data-ttu-id="98f15-186">복사할 수는 *bootstrap.js* 및 *bootstrap.css* ASP.NET MVC에서 파일에 프로젝트는 *wwwroot* 폴더에 새 프로젝트는이 방법을 사용 하지는 ASP.NET Core의 클라이언트 쪽 종속성을 관리 하기 위한 향상 된 메커니즘입니다.</span><span class="sxs-lookup"><span data-stu-id="98f15-186">You could copy the *bootstrap.js* and *bootstrap.css* files from the ASP.NET MVC project to the *wwwroot* folder in the new project, but that approach doesn't use the improved mechanism for managing client-side dependencies in ASP.NET Core.</span></span>

<span data-ttu-id="98f15-187">새 프로젝트를 사용 하 여 부트스트랩 (및 다른 클라이언트 라이브러리)에 대 한 지원을 추가 [Bower](https://bower.io/):</span><span class="sxs-lookup"><span data-stu-id="98f15-187">In the new project, we'll add support for Bootstrap (and other client-side libraries) using [Bower](https://bower.io/):</span></span>

* <span data-ttu-id="98f15-188">추가 [Bower](https://bower.io/) 라는 구성 파일 *bower.json* 프로젝트 루트에 (에서 프로젝트를 마우스 오른쪽 단추로 클릭 한 다음 **추가 > 새 항목 > Bower 구성 파일**).</span><span class="sxs-lookup"><span data-stu-id="98f15-188">Add a [Bower](https://bower.io/) configuration file named *bower.json* to the project root (Right-click on the project, and then **Add > New Item > Bower Configuration File**).</span></span> <span data-ttu-id="98f15-189">추가 [부트스트랩](http://getbootstrap.com/) 및 [jQuery](https://jquery.com/) 파일 (아래의 강조 표시 된 줄 참조).</span><span class="sxs-lookup"><span data-stu-id="98f15-189">Add [Bootstrap](http://getbootstrap.com/) and [jQuery](https://jquery.com/) to the file (see the highlighted lines below).</span></span>

  [!code-json[Main](mvc/sample/bower.json?highlight=5-6)]

<span data-ttu-id="98f15-190">파일을 저장할 때 Bower에서는 자동으로 다운로드를 종속성의 *wwwroot/lib* 폴더입니다.</span><span class="sxs-lookup"><span data-stu-id="98f15-190">Upon saving the file, Bower will automatically download the dependencies to the *wwwroot/lib* folder.</span></span> <span data-ttu-id="98f15-191">사용할 수는 **솔루션 탐색기 검색** 상자 자산의 경로 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="98f15-191">You can use the **Search Solution Explorer** box to find the path of the assets:</span></span>

![솔루션 탐색기 검색 결과에 표시 된 jquery 자산](mvc/_static/search.png)

<span data-ttu-id="98f15-193">참조 [Bower 클라이언트 쪽 패키지 관리](../client-side/bower.md) 자세한 정보에 대 한 합니다.</span><span class="sxs-lookup"><span data-stu-id="98f15-193">See [Manage Client-Side Packages with Bower](../client-side/bower.md) for more information.</span></span>

<a name="migrate-layout-file"></a>

## <a name="migrate-the-layout-file"></a><span data-ttu-id="98f15-194">레이아웃 파일 마이그레이션</span><span class="sxs-lookup"><span data-stu-id="98f15-194">Migrate the layout file</span></span>

* <span data-ttu-id="98f15-195">복사는 *_viewstart.vbhtml* 이전 ASP.NET MVC 프로젝트에서 파일 *뷰* ASP.NET Core 프로젝트에 폴더 *뷰* 폴더입니다.</span><span class="sxs-lookup"><span data-stu-id="98f15-195">Copy the *_ViewStart.cshtml* file from the old ASP.NET MVC project's *Views* folder into the ASP.NET Core project's *Views* folder.</span></span> <span data-ttu-id="98f15-196">*_viewstart.vbhtml* ASP.NET Core MVC에서 파일 변경 되지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="98f15-196">The *_ViewStart.cshtml* file has not changed in ASP.NET Core MVC.</span></span>

* <span data-ttu-id="98f15-197">만들기는 *뷰/공유* 폴더입니다.</span><span class="sxs-lookup"><span data-stu-id="98f15-197">Create a *Views/Shared* folder.</span></span>

* <span data-ttu-id="98f15-198">*선택 사항:* 복사 *_ViewImports.cshtml* 에서 *FullAspNetCore* MVC 프로젝트의 *뷰* ASP.NET Core 프로젝트 폴더*뷰* 폴더입니다.</span><span class="sxs-lookup"><span data-stu-id="98f15-198">*Optional:* Copy *_ViewImports.cshtml* from the *FullAspNetCore* MVC project's *Views* folder into the ASP.NET Core project's *Views* folder.</span></span> <span data-ttu-id="98f15-199">모든 네임 스페이스 선언을 제거는 *_ViewImports.cshtml* 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="98f15-199">Remove any namespace declaration in the *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="98f15-200">*_ViewImports.cshtml* 파일 모든 보기 파일에 대 한 네임 스페이스를 제공 하 고는에서 [태그 도우미](../mvc/views/tag-helpers/index.md)합니다.</span><span class="sxs-lookup"><span data-stu-id="98f15-200">The *_ViewImports.cshtml* file provides namespaces for all the view files and brings in [Tag Helpers](../mvc/views/tag-helpers/index.md).</span></span> <span data-ttu-id="98f15-201">태그 도우미 새 레이아웃 파일에 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="98f15-201">Tag Helpers are used in the new layout file.</span></span> <span data-ttu-id="98f15-202">*_ViewImports.cshtml* ASP.NET Core에 대 한 새 파일을 합니다.</span><span class="sxs-lookup"><span data-stu-id="98f15-202">The *_ViewImports.cshtml* file is new for ASP.NET Core.</span></span>

* <span data-ttu-id="98f15-203">복사는 *_Layout.cshtml* 이전 ASP.NET MVC 프로젝트에서 파일 *뷰/공유* ASP.NET Core 프로젝트에 폴더 *뷰/공유* 폴더입니다.</span><span class="sxs-lookup"><span data-stu-id="98f15-203">Copy the *_Layout.cshtml* file from the old ASP.NET MVC project's *Views/Shared* folder into the ASP.NET Core project's *Views/Shared* folder.</span></span>

<span data-ttu-id="98f15-204">열기 *_Layout.cshtml* 파일을 다음과 같이 변경 (완성 된 코드는 아래 표시):</span><span class="sxs-lookup"><span data-stu-id="98f15-204">Open *_Layout.cshtml* file and make the following changes (the completed code is shown below):</span></span>

   * <span data-ttu-id="98f15-205">대체 `@Styles.Render("~/Content/css")` 와 `<link>` 로드할 요소 *bootstrap.css* (아래 참조).</span><span class="sxs-lookup"><span data-stu-id="98f15-205">Replace `@Styles.Render("~/Content/css")` with a `<link>` element to load *bootstrap.css* (see below).</span></span>

   * <span data-ttu-id="98f15-206">`@Scripts.Render("~/bundles/modernizr")`를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="98f15-206">Remove `@Scripts.Render("~/bundles/modernizr")`.</span></span>

   * <span data-ttu-id="98f15-207">주석으로 처리는 `@Html.Partial("_LoginPartial")` 줄 (한 줄으로 둘러싸고 `@*...*@`).</span><span class="sxs-lookup"><span data-stu-id="98f15-207">Comment out the `@Html.Partial("_LoginPartial")` line (surround the line with `@*...*@`).</span></span> <span data-ttu-id="98f15-208">이후 자습서에서를 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="98f15-208">We'll return to it in a future tutorial.</span></span>

   * <span data-ttu-id="98f15-209">대체 `@Scripts.Render("~/bundles/jquery")` 와 `<script>` 요소 (아래 참조).</span><span class="sxs-lookup"><span data-stu-id="98f15-209">Replace `@Scripts.Render("~/bundles/jquery")` with a `<script>` element (see below).</span></span>

   * <span data-ttu-id="98f15-210">대체 `@Scripts.Render("~/bundles/bootstrap")` 와 `<script>` 요소 (아래 참조).</span><span class="sxs-lookup"><span data-stu-id="98f15-210">Replace `@Scripts.Render("~/bundles/bootstrap")` with a `<script>` element (see below)..</span></span>

<span data-ttu-id="98f15-211">새 CSS 링크:</span><span class="sxs-lookup"><span data-stu-id="98f15-211">The replacement CSS link:</span></span>

```html
<link rel="stylesheet" href="~/lib/bootstrap/dist/css/bootstrap.css" />
```

<span data-ttu-id="98f15-212">대체 스크립트 태그:</span><span class="sxs-lookup"><span data-stu-id="98f15-212">The replacement script tags:</span></span>

```html
<script src="~/lib/jquery/dist/jquery.js"></script>
<script src="~/lib/bootstrap/dist/js/bootstrap.js"></script>
```

<span data-ttu-id="98f15-213">업데이트 된 *_Layout.cshtml* 파일은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="98f15-213">The updated *_Layout.cshtml* file is shown below:</span></span>

[!code-html[Main](mvc/sample/Views/Shared/_Layout.cshtml?highlight=7,27,39-40)]

<span data-ttu-id="98f15-214">브라우저에서 사이트를 검토 합니다.</span><span class="sxs-lookup"><span data-stu-id="98f15-214">View the site in the browser.</span></span> <span data-ttu-id="98f15-215">원위치에서 예상된 스타일과는 올바르게 로드 이제 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="98f15-215">It should now load correctly, with the expected styles in place.</span></span>

* <span data-ttu-id="98f15-216">*선택 사항:* 새 레이아웃 파일을 사용 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="98f15-216">*Optional:* You might want to try using the new layout file.</span></span> <span data-ttu-id="98f15-217">이 프로젝트에 대 한 레이아웃 파일에서 복사할 수 있습니다는 *FullAspNetCore* 프로젝트.</span><span class="sxs-lookup"><span data-stu-id="98f15-217">For this project you can copy the layout file from the *FullAspNetCore* project.</span></span> <span data-ttu-id="98f15-218">사용 하는 새 레이아웃 파일 [태그 도우미](../mvc/views/tag-helpers/index.md) 있으며 다른 개선 합니다.</span><span class="sxs-lookup"><span data-stu-id="98f15-218">The new layout file uses [Tag Helpers](../mvc/views/tag-helpers/index.md) and has other improvements.</span></span>

## <a name="configure-bundling--minification"></a><span data-ttu-id="98f15-219">구성 묶음 및 축소</span><span class="sxs-lookup"><span data-stu-id="98f15-219">Configure Bundling & Minification</span></span>

<span data-ttu-id="98f15-220">묶음 및 축소를 구성 하는 방법에 대 한 정보를 참조 하십시오. [묶음 및 축소](../client-side/bundling-and-minification.md)합니다.</span><span class="sxs-lookup"><span data-stu-id="98f15-220">For information about how to configure bundling and minification, see [Bundling and Minification](../client-side/bundling-and-minification.md).</span></span>

## <a name="solving-http-500-errors"></a><span data-ttu-id="98f15-221">HTTP 500 오류를 해결합니다.</span><span class="sxs-lookup"><span data-stu-id="98f15-221">Solving HTTP 500 errors</span></span>

<span data-ttu-id="98f15-222">문제의 소스에 없는 정보가 포함 된 HTTP 500 오류 메시지를 발생 시킬 수 있는 많은 문제가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="98f15-222">There are many problems that can cause a HTTP 500 error message that contain no information on the source of the problem.</span></span> <span data-ttu-id="98f15-223">예를 들어 경우는 *Views/_ViewImports.cshtml* 프로젝트에 존재 하지 않는 네임 스페이스를 포함 하는 파일, HTTP 500 오류를 얻게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="98f15-223">For example, if the *Views/_ViewImports.cshtml* file contains a namespace that doesn't exist in your project, you'll get a HTTP 500 error.</span></span> <span data-ttu-id="98f15-224">자세한 오류 메시지를 가져오려면 다음 코드를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="98f15-224">To get a detailed error message, add the following code:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
{
    if (env.IsDevelopment())
    {
         app.UseDeveloperExceptionPage();
    }

    app.UseStaticFiles();

    app.UseMvc(routes =>
    {
        routes.MapRoute(
            name: "default",
            template: "{controller=Home}/{action=Index}/{id?}");
    });
}
```

<span data-ttu-id="98f15-225">참조 **개발자 예외 페이지를 사용 하 여** 에 [의 오류 처리](../fundamentals/error-handling.md) 자세한 정보에 대 한 합니다.</span><span class="sxs-lookup"><span data-stu-id="98f15-225">See **Using the Developer Exception Page** in [Error Handling](../fundamentals/error-handling.md) for more information.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="98f15-226">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="98f15-226">Additional Resources</span></span>

* [<span data-ttu-id="98f15-227">클라이언트 쪽 개발</span><span class="sxs-lookup"><span data-stu-id="98f15-227">Client-Side Development</span></span>](../client-side/index.md)

* [<span data-ttu-id="98f15-228">태그 도우미</span><span class="sxs-lookup"><span data-stu-id="98f15-228">Tag Helpers</span></span>](../mvc/views/tag-helpers/index.md)
