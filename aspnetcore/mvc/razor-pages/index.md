---
title: "ASP.NET Core의 Razor 페이지 소개"
author: Rick-Anderson
description: "이 문서는 ASP.NET Core의 Razor 페이지를 사용하여 페이지에 초점을 맞춘 시나리오의 손쉬운 개발에 관한 개요를 제공합니다."
keywords: "ASP.NET Core, Razor 페이지"
ms.author: riande
manager: wpickett
ms.date: 09/12/2017
ms.topic: get-started-article
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/razor-pages/index
ms.openlocfilehash: 72ab979c6c718544955ae5734903ec936fc5afbc
ms.sourcegitcommit: 195b2b331434f74334c5c5b7dfeba62d744a1e38
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/15/2017
---
# <a name="introduction-to-razor-pages-in-aspnet-core"></a><span data-ttu-id="01266-104">ASP.NET Core의 Razor 페이지 소개</span><span class="sxs-lookup"><span data-stu-id="01266-104">Introduction to Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="01266-105">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [Ryan Nowak](https://github.com/rynowak)</span><span class="sxs-lookup"><span data-stu-id="01266-105">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Ryan Nowak](https://github.com/rynowak)</span></span>

<span data-ttu-id="01266-106">Razor 페이지는 더 쉽고 더 생산적으로 코딩 페이지에 초점을 맞춘 시나리오를 만드는 ASP.NET Core MVC의 새로운 기능입니다.</span><span class="sxs-lookup"><span data-stu-id="01266-106">Razor Pages is a new feature of ASP.NET Core MVC that makes coding page-focused scenarios easier and more productive.</span></span>

<span data-ttu-id="01266-107">모델-뷰-컨트롤러 방법을 사용하는 자습서를 검색할 경우 [ASP.NET Core MVC 시작](xref:tutorials/first-mvc-app/start-mvc)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="01266-107">If you're looking for a tutorial that uses the Model-View-Controller approach, see [Getting started with ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span></span>

<a name="prerequisites"></a>

## <a name="aspnet-core-20-prerequisites"></a><span data-ttu-id="01266-108">ASP.NET Core 2.0 필요 구성 요소</span><span class="sxs-lookup"><span data-stu-id="01266-108">ASP.NET Core 2.0 prerequisites</span></span>

<span data-ttu-id="01266-109">[.NET Core](https://www.microsoft.com/net/core) 2.0.0 이상을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="01266-109">Install [.NET Core](https://www.microsoft.com/net/core) 2.0.0 or later.</span></span>

<span data-ttu-id="01266-110">Visual Studio를 사용할 경우 다음 워크로드가 포함된 [Visual Studio](https://www.visualstudio.com/vs/) 2017 버전 15.3 이상을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="01266-110">If you're using Visual Studio, install [Visual Studio](https://www.visualstudio.com/vs/) 2017 version 15.3 or later with the following workloads:</span></span>

* <span data-ttu-id="01266-111">**ASP.NET 및 웹 개발**</span><span class="sxs-lookup"><span data-stu-id="01266-111">**ASP.NET and web development**</span></span>
* <span data-ttu-id="01266-112">**.NET Core 플랫폼 간 개발**</span><span class="sxs-lookup"><span data-stu-id="01266-112">**.NET Core cross-platform development**</span></span>

<a name="rpvs17"></a>

## <a name="creating-a-razor-pages-project"></a><span data-ttu-id="01266-113">Razor 페이지 프로젝트 만들기</span><span class="sxs-lookup"><span data-stu-id="01266-113">Creating a Razor Pages project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="01266-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="01266-114">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="01266-115">Visual Studio를 사용하여 Razor 페이지 프로젝트를 만드는 방법에 대한 자세한 내용은 [Razor 페이지 시작](xref:tutorials/razor-pages/razor-pages-start)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="01266-115">See [Getting started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project using Visual Studio.</span></span>

#   <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="01266-116">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="01266-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="01266-117">명령줄에서 `dotnet new razor`를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="01266-117">Run `dotnet new razor` from the command line.</span></span>

<span data-ttu-id="01266-118">Mac용 Visual Studio에서 생성된 *.csproj* 파일을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="01266-118">Open the generated *.csproj* file from Visual Studio for Mac.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="01266-119">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="01266-119">Visual Studio Code</span></span>](#tab/visual-studio-code) 

<span data-ttu-id="01266-120">명령줄에서 `dotnet new razor`를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="01266-120">Run `dotnet new razor` from the command line.</span></span>

#   <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="01266-121">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="01266-121">.NET Core CLI</span></span>](#tab/netcore-cli) 

<span data-ttu-id="01266-122">명령줄에서 `dotnet new razor`를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="01266-122">Run `dotnet new razor` from the command line.</span></span>

---

## <a name="razor-pages"></a><span data-ttu-id="01266-123">Razor 페이지</span><span class="sxs-lookup"><span data-stu-id="01266-123">Razor Pages</span></span>

<span data-ttu-id="01266-124">Razor 페이지는 *Startup.cs*에서 사용하도록 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="01266-124">Razor Pages is enabled in *Startup.cs*:</span></span>

[!code-cs[main](index/sample/RazorPagesIntro/Startup.cs?name=snippet_Startup)]

<span data-ttu-id="01266-125">기본 페이지를 고려해 봅니다. <a name="OnGet"></a></span><span class="sxs-lookup"><span data-stu-id="01266-125">Consider a basic page: <a name="OnGet"></a></span></span>

[!code-cshtml[main](index/sample/RazorPagesIntro/Pages/Index.cshtml)]

<span data-ttu-id="01266-126">이전 코드는 Razor 뷰 파일과 매우 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="01266-126">The preceding code looks a lot like a Razor view file.</span></span> <span data-ttu-id="01266-127">차이점은 `@page` 지시문입니다.</span><span class="sxs-lookup"><span data-stu-id="01266-127">What makes it different is the `@page` directive.</span></span> <span data-ttu-id="01266-128">`@page`는 파일을 MVC 작업으로 만듭니다. 즉, 컨트롤러를 거치지 않고 요청을 직접 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="01266-128">`@page` makes the file into an MVC action - which means that it handles requests directly, without going through a controller.</span></span> <span data-ttu-id="01266-129">`@page`는 페이지의 첫 번째 Razor 지시문이어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="01266-129">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="01266-130">`@page`는 다른 Razor 생성자의 동작에 영향을 미칩니다.</span><span class="sxs-lookup"><span data-stu-id="01266-130">`@page` affects the behavior of other Razor constructs.</span></span>

<span data-ttu-id="01266-131">`PageModel` 클래스를 사용하는 비슷한 페이지는 다음 두 파일에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="01266-131">A similar page, using a `PageModel` class, is shown in the following two files.</span></span> <span data-ttu-id="01266-132">*Pages/Index2.cshtml* 파일:</span><span class="sxs-lookup"><span data-stu-id="01266-132">The *Pages/Index2.cshtml* file:</span></span>

[!code-cshtml[main](index/sample/RazorPagesIntro/Pages/Index2.cshtml)]

<span data-ttu-id="01266-133">*Pages/Index2.cshtml.cs* "코드 숨김" 파일:</span><span class="sxs-lookup"><span data-stu-id="01266-133">The *Pages/Index2.cshtml.cs* "code-behind" file:</span></span>

[!code-cs[main](index/sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

<span data-ttu-id="01266-134">일반적으로 `PageModel` 클래스 파일의 이름은 Razor 페이지 파일과 동일하고 *.cs*가 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="01266-134">By convention, the `PageModel` class file has the same name as the Razor Page file with *.cs* appended.</span></span> <span data-ttu-id="01266-135">예를 들어 이전 Razor 페이지는 *Pages/Index2.cshtml*입니다.</span><span class="sxs-lookup"><span data-stu-id="01266-135">For example, the previous Razor Page is *Pages/Index2.cshtml*.</span></span> <span data-ttu-id="01266-136">`PageModel` 클래스가 포함된 파일의 이름은 *Pages/Index2.cshtml.cs*입니다.</span><span class="sxs-lookup"><span data-stu-id="01266-136">The file containing the `PageModel` class is named *Pages/Index2.cshtml.cs*.</span></span>

<span data-ttu-id="01266-137">페이지에 대한 URL 경로 연결은 파일 시스템의 페이지 위치에 따라 결정됩니다.</span><span class="sxs-lookup"><span data-stu-id="01266-137">The associations of URL paths to pages are determined by the page's location in the file system.</span></span> <span data-ttu-id="01266-138">다음 표에서는 Razor 페이지 경로 및 일치하는 URL을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="01266-138">The following table shows a Razor Page path and the matching URL:</span></span>

| <span data-ttu-id="01266-139">파일 이름 및 경로</span><span class="sxs-lookup"><span data-stu-id="01266-139">File name and path</span></span>               | <span data-ttu-id="01266-140">일치하는 URL</span><span class="sxs-lookup"><span data-stu-id="01266-140">matching URL</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="01266-141">*/Pages/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="01266-141">*/Pages/Index.cshtml*</span></span> | <span data-ttu-id="01266-142">`/` 또는 `/Index`</span><span class="sxs-lookup"><span data-stu-id="01266-142">`/` or `/Index`</span></span> |
| <span data-ttu-id="01266-143">*/Pages/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="01266-143">*/Pages/Contact.cshtml*</span></span> | `/Contact` |
| <span data-ttu-id="01266-144">*/Pages/Store/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="01266-144">*/Pages/Store/Contact.cshtml*</span></span> | `/Store/Contact` |
| <span data-ttu-id="01266-145">*/Pages/Store/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="01266-145">*/Pages/Store/Index.cshtml*</span></span> | <span data-ttu-id="01266-146">`/Store` 또는 `/Store/Index`</span><span class="sxs-lookup"><span data-stu-id="01266-146">`/Store` or `/Store/Index`</span></span> |

<span data-ttu-id="01266-147">메모:</span><span class="sxs-lookup"><span data-stu-id="01266-147">Notes:</span></span>

* <span data-ttu-id="01266-148">런타임은 기본적으로 *Pages* 폴더에서 Razor 페이지 파일을 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="01266-148">The runtime looks for Razor Pages files in the *Pages* folder by default.</span></span>
* <span data-ttu-id="01266-149">URL에 페이지가 포함되어 있지 않을 경우 `Index`가 기본 페이지입니다.</span><span class="sxs-lookup"><span data-stu-id="01266-149">`Index` is the default page when a URL doesn't include a page.</span></span>

## <a name="writing-a-basic-form"></a><span data-ttu-id="01266-150">기본 폼 작성</span><span class="sxs-lookup"><span data-stu-id="01266-150">Writing a basic form</span></span>

<span data-ttu-id="01266-151">Razor 페이지 기능은 웹 브라우저에서 사용되는 일반 패턴을 쉽게 만들도록 고안되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="01266-151">Razor Pages features are designed to make common patterns used with web browsers easy.</span></span> <span data-ttu-id="01266-152">[모델 바인딩](xref:mvc/models/model-binding), [태그 도우미](xref:mvc/views/tag-helpers/intro) 및 HTML 도우미는 모두 Razor 페이지 클래스에 정의된 속성에서 *제대로 작동*합니다.</span><span class="sxs-lookup"><span data-stu-id="01266-152">[Model binding](xref:mvc/models/model-binding), [Tag Helpers](xref:mvc/views/tag-helpers/intro), and HTML helpers all *just work* with the properties defined in a Razor Page class.</span></span> <span data-ttu-id="01266-153">`Contact` 모델에 대한 기본 “연락처” 폼을 구현하는 페이지를 고려해 봅니다.</span><span class="sxs-lookup"><span data-stu-id="01266-153">Consider a page that implements a basic "contact us" form for the `Contact` model:</span></span>

<span data-ttu-id="01266-154">이 문서에 있는 샘플의 경우 `DbContext`는 [Startup.cs](https://github.com/aspnet/Docs/blob/master/aspnetcore/mvc/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16) 파일에서 초기화됩니다.</span><span class="sxs-lookup"><span data-stu-id="01266-154">For the samples in this document, the `DbContext` is initialized in the [Startup.cs](https://github.com/aspnet/Docs/blob/master/aspnetcore/mvc/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16) file.</span></span>

[!code-cs[main](index/sample/RazorPagesContacts/Startup.cs?highlight=15-16)]

<span data-ttu-id="01266-155">데이터 모델:</span><span class="sxs-lookup"><span data-stu-id="01266-155">The data model:</span></span>

[!code-cs[main](index/sample/RazorPagesContacts/Data/Customer.cs)]

<span data-ttu-id="01266-156">*Pages/Create.cshtml* 뷰 파일:</span><span class="sxs-lookup"><span data-stu-id="01266-156">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[main](index/sample/RazorPagesContacts/Pages/Create.cshtml)]

<span data-ttu-id="01266-157">뷰에 대한 *Pages/Create.cshtml.cs* 코드 숨김 파일:</span><span class="sxs-lookup"><span data-stu-id="01266-157">The *Pages/Create.cshtml.cs* code-behind file for the view:</span></span>

[!code-cs[main](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_ALL)]

<span data-ttu-id="01266-158">일반적으로 `PageModel` 클래스를 `<PageName>Model`이라고 하고 이 클래스는 페이지와 동일한 네임스페이스에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="01266-158">By convention, the `PageModel` class is called `<PageName>Model` and is in the same namespace as the page.</span></span>

<span data-ttu-id="01266-159">`PageModel` 코드 숨김 파일을 사용할 경우 유닛 테스트가 지원되지만, 명시적 생성자 및 클래스를 작성해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="01266-159">Using a `PageModel` code-behind file supports unit testing, but requires you to write an explicit constructor and class.</span></span> <span data-ttu-id="01266-160">`PageModel` 코드 숨김 파일이 없는 페이지는 런타임 컴파일을 지원하고 이 지원은 개발에 유용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="01266-160">Pages without `PageModel` code-behind files support runtime compilation, which can be an advantage in development.</span></span>  <!-- review: advantage because you can make changes and refresh the browser without explicitly compiling the app -->

<span data-ttu-id="01266-161">페이지에는 `POST` 요청에서 실행되는 `OnPostAsync` *처리기 메서드*가 있습니다(사용자가 폼을 게시할 때).</span><span class="sxs-lookup"><span data-stu-id="01266-161">The page has an `OnPostAsync` *handler method*, which runs on `POST` requests (when a user posts the form).</span></span> <span data-ttu-id="01266-162">HTTP 동사에 대한 처리기 메서드를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="01266-162">You can add handler methods for any HTTP verb.</span></span> <span data-ttu-id="01266-163">가장 일반적인 처리기는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="01266-163">The most common handlers are:</span></span>

* <span data-ttu-id="01266-164">`OnGet` - 페이지에 필요한 상태를 초기화합니다.</span><span class="sxs-lookup"><span data-stu-id="01266-164">`OnGet` to initialize state needed for the page.</span></span> <span data-ttu-id="01266-165">[OnGet](#OnGet) 샘플.</span><span class="sxs-lookup"><span data-stu-id="01266-165">[OnGet](#OnGet) sample.</span></span>
* <span data-ttu-id="01266-166">`OnPost` - 제출에서 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="01266-166">`OnPost` to handle form submissions.</span></span>

<span data-ttu-id="01266-167">`Async` 명명 접미사는 선택 사항이지만 비동기 함수에 대한 규칙에서 종종 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="01266-167">The `Async` naming suffix is optional but is often used by convention for asynchronous functions.</span></span> <span data-ttu-id="01266-168">이전 예제의 `OnPostAsync` 코드는 일반적으로 컨트롤러에서 작성되는 것과 비슷해 보입니다.</span><span class="sxs-lookup"><span data-stu-id="01266-168">The `OnPostAsync` code in the preceding example looks similar to what you would normally write in a controller.</span></span> <span data-ttu-id="01266-169">이전 코드는 Razor 페이지에 일반적인 코드입니다.</span><span class="sxs-lookup"><span data-stu-id="01266-169">The preceding code is typical for Razor Pages.</span></span> <span data-ttu-id="01266-170">[모델 바인딩](xref:mvc/models/model-binding), [유효성 검사](xref:mvc/models/validation) 및 작업 결과 같은 대부분의 MVC 기본 형식이 공유됩니다.</span><span class="sxs-lookup"><span data-stu-id="01266-170">Most of the MVC primitives like [model binding](xref:mvc/models/model-binding), [validation](xref:mvc/models/validation), and action results are shared.</span></span>  <!-- Review: Ryan, can we get a list of what is shared and what isn't? -->

<span data-ttu-id="01266-171">이전 `OnPostAsync` 메서드:</span><span class="sxs-lookup"><span data-stu-id="01266-171">The previous `OnPostAsync` method:</span></span>

[!code-cs[main](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="01266-172">`OnPostAsync`의 기본 흐름:</span><span class="sxs-lookup"><span data-stu-id="01266-172">The basic flow of `OnPostAsync`:</span></span>

<span data-ttu-id="01266-173">유효성 검사 오류를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="01266-173">Check for validation errors.</span></span>

*  <span data-ttu-id="01266-174">오류가 없는 경우 데이터를 저장하고 리디렉션됩니다.</span><span class="sxs-lookup"><span data-stu-id="01266-174">If there are no errors, save the data and redirect.</span></span>
*  <span data-ttu-id="01266-175">오류가 있는 경우 유효성 검사 메시지가 포함된 페이지를 다시 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="01266-175">If there are errors, show the page again with validation messages.</span></span> <span data-ttu-id="01266-176">클라이언트 쪽 유효성 검사는 기존 ASP.NET Core MVC 응용 프로그램과 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="01266-176">Client-side validation is identical to traditional ASP.NET Core MVC applications.</span></span> <span data-ttu-id="01266-177">대부분의 경우 유효성 검사 오류는 클라이언트에서 검색되고 서버에는 제출되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="01266-177">In many cases, validation errors would be detected on the client, and never submitted to the server.</span></span>

<span data-ttu-id="01266-178">데이터를 성공적으로 입력하면 `OnPostAsync` 처리기 메서드는 `RedirectToPage` 도우미 메서드를 호출하여 `RedirectToPageResult` 인스턴스를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="01266-178">When the data is entered successfully, the `OnPostAsync` handler method calls the `RedirectToPage` helper method to return an instance of `RedirectToPageResult`.</span></span> <span data-ttu-id="01266-179">`RedirectToPage`는 `RedirectToAction` 또는 `RedirectToRoute`와 비슷하지만 페이지에 대해 사용자 지정된 새 작업 결과입니다.</span><span class="sxs-lookup"><span data-stu-id="01266-179">`RedirectToPage` is a new action result, similar to `RedirectToAction` or `RedirectToRoute`, but customized for pages.</span></span> <span data-ttu-id="01266-180">이전 샘플에서 이 메서드는 루트 인덱스 페이지(`/Index`)로 리디렉션됩니다.</span><span class="sxs-lookup"><span data-stu-id="01266-180">In the preceding sample, it redirects to the root Index page (`/Index`).</span></span> <span data-ttu-id="01266-181">`RedirectToPage`는 [페이지에 대한 URL 생성](#url_gen) 섹션에서 자세히 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="01266-181">`RedirectToPage` is detailed in the [URL generation for Pages](#url_gen) section.</span></span>

<span data-ttu-id="01266-182">서버에 전달되는 유효성 오류가 제출된 폼에 있는 경우 `OnPostAsync` 처리기 메서드는 `Page` 도우미 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="01266-182">When the submitted form has validation errors (that are passed to the server), the`OnPostAsync` handler method calls the `Page` helper method.</span></span> <span data-ttu-id="01266-183">`Page`는 `PageResult` 인스턴스를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="01266-183">`Page` returns an instance of `PageResult`.</span></span> <span data-ttu-id="01266-184">`Page` 반환은 컨트롤의 작업이 `View`를 반환하는 방식과 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="01266-184">Returning `Page` is similar to how actions in controllers return `View`.</span></span> <span data-ttu-id="01266-185">`PageResult`는 처리기 메서드의 기본 <!-- Review  --> 반환 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="01266-185">`PageResult` is the default <!-- Review  --> return type for a handler method.</span></span> <span data-ttu-id="01266-186">`void`를 반환하는 처리기 메서드는 페이지를 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="01266-186">A handler method that returns `void` renders the page.</span></span>

<span data-ttu-id="01266-187">`Customer` 속성은 `[BindProperty]` 특성을 사용하여 모델 바인딩으로 옵트인(opt in)합니다.</span><span class="sxs-lookup"><span data-stu-id="01266-187">The `Customer` property uses `[BindProperty]` attribute to opt in to model binding.</span></span>

[!code-cs[main](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_PageModel&highlight=10-11)]

<span data-ttu-id="01266-188">Razor 페이지는 기본적으로 GET이 아닌 동사에만 속성을 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="01266-188">Razor Pages, by default, bind properties only with non-GET verbs.</span></span> <span data-ttu-id="01266-189">속성에 바인딩하면 작성해야 하는 코드 양이 감소할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="01266-189">Binding to properties can reduce the amount of code you have to write.</span></span> <span data-ttu-id="01266-190">바인딩은 동일한 속성을 사용하여 폼 필드(`<input asp-for="Customer.Name" />`)를 렌더링하고 입력을 허용하는 방식으로 코드를 줄입니다.</span><span class="sxs-lookup"><span data-stu-id="01266-190">Binding reduces code by using the same property to render form fields (`<input asp-for="Customer.Name" />`) and accept the input.</span></span>

<span data-ttu-id="01266-191">홈페이지(*Index.cshtml*):</span><span class="sxs-lookup"><span data-stu-id="01266-191">The home page (*Index.cshtml*):</span></span>

[!code-cshtml[main](index/sample/RazorPagesContacts/Pages/Index.cshtml)]

<span data-ttu-id="01266-192">코드 숨김 *Index.cshtml.cs* 파일:</span><span class="sxs-lookup"><span data-stu-id="01266-192">The code behind *Index.cshtml.cs* file:</span></span>

[!code-cs[main](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs)]

<span data-ttu-id="01266-193">*Index.cshtml* 파일에는 각 연락처의 편집 링크를 만들기 위해 다음 태그가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="01266-193">The *Index.cshtml* file contains the following markup to create an edit link for each contact:</span></span>

```cshtml
<a asp-page="./Edit" asp-route-id="@contact.Id">edit</a>
```

<span data-ttu-id="01266-194">[앵커 태그 도우미](xref:mvc/views/tag-helpers/builtin-th/AnchorTagHelper)는 [asp-route-{value}](xref:mvc/views/tag-helpers/builtin-th/AnchorTagHelper#route) 특성을 사용하여 편집 페이지 링크를 생성했습니다.</span><span class="sxs-lookup"><span data-stu-id="01266-194">The [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/AnchorTagHelper) used the [asp-route-{value}](xref:mvc/views/tag-helpers/builtin-th/AnchorTagHelper#route) attribute to generate a link to the Edit page.</span></span> <span data-ttu-id="01266-195">링크에는 연락처 ID와 함께 경로 데이터가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="01266-195">The link contains route data with the contact ID.</span></span> <span data-ttu-id="01266-196">예를 들어, `http://localhost:5000/Edit/1`을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="01266-196">For example, `http://localhost:5000/Edit/1`.</span></span>

<span data-ttu-id="01266-197">*Pages/Edit.cshtml* 파일:</span><span class="sxs-lookup"><span data-stu-id="01266-197">The *Pages/Edit.cshtml* file:</span></span>

[!code-cshtml[main](index/sample/RazorPagesContacts/Pages/Edit.cshtml?highlight=1)]

<span data-ttu-id="01266-198">첫 번째 줄에는 `@page "{id:int}"` 지시문이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="01266-198">The first line contains the `@page "{id:int}"` directive.</span></span> <span data-ttu-id="01266-199">라우팅 제약 조건 `"{id:int}"`는 `int` 경로 데이터가 포함된 페이지에 대한 요청을 허용하도록 페이지에 지시합니다.</span><span class="sxs-lookup"><span data-stu-id="01266-199">The routing constraint`"{id:int}"` tells the page to accept requests to the page that contain `int` route data.</span></span> <span data-ttu-id="01266-200">페이지에 대한 요청에 `int`로 변환될 수 있는 경로 데이터가 없으면 런타임은 HTTP 404(찾을 수 없음) 오류를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="01266-200">If a request to the page doesn't contain route data that can be converted to an `int`, the runtime returns an HTTP 404 (not found) error.</span></span>

<span data-ttu-id="01266-201">*Pages/Edit.cshtml.cs* 파일:</span><span class="sxs-lookup"><span data-stu-id="01266-201">The *Pages/Edit.cshtml.cs* file:</span></span>

[!code-cs[main](index/sample/RazorPagesContacts/Pages/Edit.cshtml.cs)]

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-razor-pages"></a><span data-ttu-id="01266-202">XSRF/CSRF 및 Razor 페이지</span><span class="sxs-lookup"><span data-stu-id="01266-202">XSRF/CSRF and Razor Pages</span></span>

<span data-ttu-id="01266-203">[위조 방지 유효성 검사](xref:security/anti-request-forgery)에 대한 코드를 작성할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="01266-203">You don't have to write any code for [antiforgery validation](xref:security/anti-request-forgery).</span></span> <span data-ttu-id="01266-204">위조 방지 토큰 생성 및 유효성 검사는 Razor 페이지에 자동으로 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="01266-204">Antiforgery token generation and validation are automatically included in Razor Pages.</span></span>

<a name="layout"></a>
## <a name="using-layouts-partials-templates-and-tag-helpers-with-razor-pages"></a><span data-ttu-id="01266-205">Razor 페이지와 함께 레이아웃, 부분, 템플릿 및 태그 도우미 사용</span><span class="sxs-lookup"><span data-stu-id="01266-205">Using Layouts, partials, templates, and Tag Helpers with Razor Pages</span></span>

<span data-ttu-id="01266-206">페이지는 Razor 뷰 엔진의 모든 기능을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="01266-206">Pages work with all the features of the Razor view engine.</span></span> <span data-ttu-id="01266-207">레이아웃, 부분, 템플릿, 태그 도우미, *_ViewStart.cshtml*, *_ViewImports.cshtml*은 기존 Razor 뷰와 동일한 방식으로 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="01266-207">Layouts, partials, templates, Tag Helpers, *_ViewStart.cshtml*, *_ViewImports.cshtml* work in the same way they do for conventional Razor views.</span></span>

<span data-ttu-id="01266-208">해당 기능 중 일부를 활용하여 이 페이지의 문제를 해결해 보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="01266-208">Let's declutter this page by taking advantage of some of those features.</span></span>

<span data-ttu-id="01266-209">[레이아웃 페이지](xref:mvc/views/layout)를 *Pages/_Layout.cshtml*에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="01266-209">Add a [layout page](xref:mvc/views/layout) to *Pages/_Layout.cshtml*:</span></span>

[!code-cshtml[main](index/sample/RazorPagesContacts2/Pages/_LayoutSimple.cshtml)]

<span data-ttu-id="01266-210">[레이아웃](xref:mvc/views/layout):</span><span class="sxs-lookup"><span data-stu-id="01266-210">The [Layout](xref:mvc/views/layout):</span></span>

* <span data-ttu-id="01266-211">각 페이지의 레이아웃을 제어합니다(페이지가 레이아웃에서 옵트아웃(opt out)되지 않는 경우).</span><span class="sxs-lookup"><span data-stu-id="01266-211">Controls the layout of each page (unless the page opts out of layout).</span></span>
* <span data-ttu-id="01266-212">JavaScript 및 스타일시트 같은 HTML 구조를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="01266-212">Imports HTML structures such as JavaScript and stylesheets.</span></span>

<span data-ttu-id="01266-213">자세한 내용은 [레이아웃 페이지](xref:mvc/views/layout)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="01266-213">See [layout page](xref:mvc/views/layout) for more information.</span></span>

<span data-ttu-id="01266-214">[Layout](xref:mvc/views/layout#specifying-a-layout) 속성은 *Pages/_ViewStart.cshtml*에서 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="01266-214">The [Layout](xref:mvc/views/layout#specifying-a-layout) property is set in *Pages/_ViewStart.cshtml*:</span></span>

[!code-cshtml[main](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

<span data-ttu-id="01266-215">**참고:** 레이아웃은 *Pages* 폴더에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="01266-215">**Note:** The layout is in the *Pages* folder.</span></span> <span data-ttu-id="01266-216">페이지는 현재 페이지와 동일한 폴더에서 시작하여 다른 뷰(레이아웃, 템플릿, 부분)를 계층 구조로 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="01266-216">Pages look for other views (layouts, templates, partials) hierarchically, starting in the same folder as the current page.</span></span> <span data-ttu-id="01266-217">*Pages* 폴더의 레이아웃은 *Pages* 폴더 아래의 Razor 페이지에서 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="01266-217">A layout in the *Pages* folder can be used from any Razor page under the *Pages* folder.</span></span>

<span data-ttu-id="01266-218">레이아웃 파일은 *Views/Shared* 폴더에 두지 **않는** 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="01266-218">We recommend you **not** put the layout file in the *Views/Shared* folder.</span></span> <span data-ttu-id="01266-219">*Views/Shared*는 MVC 뷰 패턴입니다.</span><span class="sxs-lookup"><span data-stu-id="01266-219">*Views/Shared* is an MVC views pattern.</span></span> <span data-ttu-id="01266-220">Razor 페이지는 경로 규칙이 아니라 폴더 계층 구조를 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="01266-220">Razor Pages are meant to rely on folder hierarchy, not path conventions.</span></span>

<span data-ttu-id="01266-221">Razor 페이지의 뷰 검색에는 *Pages* 폴더가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="01266-221">View search from a Razor Page includes the *Pages* folder.</span></span> <span data-ttu-id="01266-222">MVC 컨트롤러 및 기존 Razor 뷰에서 사용 중인 레이아웃, 템플릿 및 부분이 *제대로 작동*합니다.</span><span class="sxs-lookup"><span data-stu-id="01266-222">The layouts, templates, and partials you're using with MVC controllers and conventional Razor views *just work*.</span></span>

<span data-ttu-id="01266-223">*Pages/_ViewImports.cshtml* 파일을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="01266-223">Add a *Pages/_ViewImports.cshtml* file:</span></span>

[!code-cshtml[main](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

<span data-ttu-id="01266-224">`@namespace`는 자습서의 뒷부분에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="01266-224">`@namespace` is explained later in the tutorial.</span></span> <span data-ttu-id="01266-225">`@addTagHelper` 지시문은 [기본 제공 태그 도우미](xref:mvc/views/tag-helpers/builtin-th/Index)를 *Pages* 폴더의 모든 페이지에 도입합니다.</span><span class="sxs-lookup"><span data-stu-id="01266-225">The `@addTagHelper` directive brings in the [built-in Tag Helpers](xref:mvc/views/tag-helpers/builtin-th/Index) to all the pages in the *Pages* folder.</span></span>

<a name="namespace"></a>

<span data-ttu-id="01266-226">`@namespace` 지시문은 페이지에서 명시적으로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="01266-226">When the `@namespace` directive is used explicitly on a page:</span></span>

[!code-cshtml[main](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

<span data-ttu-id="01266-227">이 지시문은 페이지에 대한 네임스페이스를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="01266-227">The directive sets the namespace for the page.</span></span> <span data-ttu-id="01266-228">`@model` 지시문에는 네임스페이스가 포함될 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="01266-228">The `@model` directive doesn't need to include the namespace.</span></span>

<span data-ttu-id="01266-229">`@namespace` 지시문이 *_ViewImports.cshtml*에 포함되면 지정된 네임스페이스는 `@namespace` 지시문을 가져오는 페이지의 생성된 네임스페이스에 대한 접두사를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="01266-229">When the `@namespace` directive is contained in *_ViewImports.cshtml*, the specified namespace supplies the prefix for the generated namespace in the Page that imports the `@namespace` directive.</span></span> <span data-ttu-id="01266-230">생성된 네임스페이스의 나머지 부분(접미사 부분)은 *_ViewImports.cshtml*이 포함된 폴더와 페이지가 포함된 폴더 사이의 점으로 구분된 상대 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="01266-230">The rest of the generated namespace (the suffix portion) is the dot-separated relative path between the folder containing *_ViewImports.cshtml* and the folder containing the page.</span></span>

<span data-ttu-id="01266-231">예를 들어 코드 숨김 파일 *Pages/Customers/Edit.cshtml.cs*는 네임스페이스를 명시적으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="01266-231">For example, the code behind file *Pages/Customers/Edit.cshtml.cs* explicitly sets the namespace:</span></span>

[!code-cs[main](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

<span data-ttu-id="01266-232">*Pages/_ViewImports.cshtml* 파일은 다음 네임스페이스를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="01266-232">The *Pages/_ViewImports.cshtml* file sets the following namespace:</span></span>

[!code-cshtml[main](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

<span data-ttu-id="01266-233">*Pages/Customers/Edit.cshtml* Razor 페이지에 대한 생성된 네임스페이스는 코드 숨김 파일과 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="01266-233">The generated namespace for the *Pages/Customers/Edit.cshtml* Razor Page is the same as the code behind file.</span></span> <span data-ttu-id="01266-234">`@namespace` 지시문은 코드 숨김 파일에 대한 `@using` 지시문을 추가할 필요 없이 프로젝트 및 페이지 생성 코드에 추가된 C# 클래스가 *제대로 작동*하도록 고안되었습니다.</span><span class="sxs-lookup"><span data-stu-id="01266-234">The `@namespace` directive was designed so the C# classes added to a project and pages-generated code *just work* without having to add an `@using` directive for the code behind file.</span></span>

<span data-ttu-id="01266-235">**참고:** `@namespace`는 기존 Razor 뷰에서도 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="01266-235">**Note:** `@namespace` also works with conventional Razor views.</span></span>

<span data-ttu-id="01266-236">원래 *Pages/Create.cshtml* 뷰 파일:</span><span class="sxs-lookup"><span data-stu-id="01266-236">The original *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[main](index/sample/RazorPagesContacts/Pages/Create.cshtml?highlight=2)]

<span data-ttu-id="01266-237">업데이트된 *Pages/Create.cshtml* 뷰 파일:</span><span class="sxs-lookup"><span data-stu-id="01266-237">The updated *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[main](index/sample/RazorPagesContacts2/Pages/Customers/Create.cshtml?highlight=2)]

<span data-ttu-id="01266-238">[Razor 페이지 시작 프로젝트](#rpvs17)에는 클라이언트 쪽 유효성 검사를 연결하는 *Pages/_ValidationScriptsPartial.cshtml*이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="01266-238">The [Razor Pages starter project](#rpvs17) contains the *Pages/_ValidationScriptsPartial.cshtml*, which hooks up client-side validation.</span></span>

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a><span data-ttu-id="01266-239">페이지에 대한 URL 생성</span><span class="sxs-lookup"><span data-stu-id="01266-239">URL generation for Pages</span></span>

<span data-ttu-id="01266-240">이전에 표시된 `Create` 페이지에서는 `RedirectToPage`를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="01266-240">The `Create` page, shown previously, uses `RedirectToPage`:</span></span>

[!code-cs[main](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=10)]

<span data-ttu-id="01266-241">앱에는 다음 파일/폴더 구조가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="01266-241">The app has the following file/folder structure:</span></span>

* <span data-ttu-id="01266-242">*/Pages*</span><span class="sxs-lookup"><span data-stu-id="01266-242">*/Pages*</span></span>

  * <span data-ttu-id="01266-243">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="01266-243">*Index.cshtml*</span></span>
  * <span data-ttu-id="01266-244">*/Customer*</span><span class="sxs-lookup"><span data-stu-id="01266-244">*/Customer*</span></span>

    * <span data-ttu-id="01266-245">*Create.cshtml*</span><span class="sxs-lookup"><span data-stu-id="01266-245">*Create.cshtml*</span></span>
    * <span data-ttu-id="01266-246">*Edit.cshtml*</span><span class="sxs-lookup"><span data-stu-id="01266-246">*Edit.cshtml*</span></span>
    * <span data-ttu-id="01266-247">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="01266-247">*Index.cshtml*</span></span>

<span data-ttu-id="01266-248">*Pages/Customers/Create.cshtml* 및 *Pages/Customers/Edit.cshtml* 페이지는 성공 후에 *Pages/Index.cshtml*로 리디렉션됩니다.</span><span class="sxs-lookup"><span data-stu-id="01266-248">The *Pages/Customers/Create.cshtml* and *Pages/Customers/Edit.cshtml* pages redirect to *Pages/Index.cshtml* after success.</span></span> <span data-ttu-id="01266-249">문자열 `/Index`는 이전 페이지에 액세스하기 위한 URI 부분입니다.</span><span class="sxs-lookup"><span data-stu-id="01266-249">The string `/Index` is part of the URI to access the preceding page.</span></span> <span data-ttu-id="01266-250">문자열 `/Index`는 *Pages/Index.cshtml* 페이지의 URI를 생성하는 데 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="01266-250">The string `/Index` can be used to generate URIs to the *Pages/Index.cshtml* page.</span></span> <span data-ttu-id="01266-251">예:</span><span class="sxs-lookup"><span data-stu-id="01266-251">For example:</span></span>

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">My Index Page</a>`
* `RedirectToPage("/Index")`

<span data-ttu-id="01266-252">페이지 이름은 루트 */Pages* 폴더에서 시작되는 페이지 경로입니다(선행 `/` 포함, 예: `/Index`).</span><span class="sxs-lookup"><span data-stu-id="01266-252">The page name is the path to the page from the root */Pages* folder (including a leading `/`, for example `/Index`).</span></span> <span data-ttu-id="01266-253">이전 URL 생성 샘플은 URL 하드 코딩보다 훨씬 더 기능이 다양합니다.</span><span class="sxs-lookup"><span data-stu-id="01266-253">The preceding URL generation samples are much more feature rich than just hardcoding a URL.</span></span> <span data-ttu-id="01266-254">URL 생성은 [라우팅](xref:mvc/controllers/routing)을 사용하며 경로가 대상 경로에서 정의되는 방식에 따라 매개 변수를 생성하고 인코딩할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="01266-254">URL generation uses [routing](xref:mvc/controllers/routing) and can generate and encode parameters according to how the route is defined in the destination path.</span></span>

<span data-ttu-id="01266-255">페이지에 대한 URL 생성은 상대적 이름을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="01266-255">URL generation for pages supports relative names.</span></span> <span data-ttu-id="01266-256">다음 표에서는 *Pages/Customers/Create.cshtml*에서 다른 `RedirectToPage` 매개 변수를 통해 선택되는 인덱스 페이지를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="01266-256">The following table shows which Index page is selected with different `RedirectToPage` parameters from *Pages/Customers/Create.cshtml*:</span></span>

| <span data-ttu-id="01266-257">RedirectToPage(x)</span><span class="sxs-lookup"><span data-stu-id="01266-257">RedirectToPage(x)</span></span>| <span data-ttu-id="01266-258">페이지</span><span class="sxs-lookup"><span data-stu-id="01266-258">Page</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="01266-259">RedirectToPage("/Index")</span><span class="sxs-lookup"><span data-stu-id="01266-259">RedirectToPage("/Index")</span></span> | <span data-ttu-id="01266-260">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="01266-260">*Pages/Index*</span></span> |
| <span data-ttu-id="01266-261">RedirectToPage("./Index");</span><span class="sxs-lookup"><span data-stu-id="01266-261">RedirectToPage("./Index");</span></span> | <span data-ttu-id="01266-262">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="01266-262">*Pages/Customers/Index*</span></span> |
| <span data-ttu-id="01266-263">RedirectToPage("../Index")</span><span class="sxs-lookup"><span data-stu-id="01266-263">RedirectToPage("../Index")</span></span> | <span data-ttu-id="01266-264">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="01266-264">*Pages/Index*</span></span> |
| <span data-ttu-id="01266-265">RedirectToPage("Index")</span><span class="sxs-lookup"><span data-stu-id="01266-265">RedirectToPage("Index")</span></span>  | <span data-ttu-id="01266-266">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="01266-266">*Pages/Customers/Index*</span></span> |

<span data-ttu-id="01266-267">`RedirectToPage("Index")`, `RedirectToPage("./Index")` 및 `RedirectToPage("../Index")`는 *상대적 이름*입니다.</span><span class="sxs-lookup"><span data-stu-id="01266-267">`RedirectToPage("Index")`, `RedirectToPage("./Index")`, and `RedirectToPage("../Index")`  are *relative names*.</span></span> <span data-ttu-id="01266-268">`RedirectToPage` 매개 변수는 현재 페이지의 경로와 *결합*되어 대상 페이지의 이름을 계산합니다.</span><span class="sxs-lookup"><span data-stu-id="01266-268">The `RedirectToPage` parameter is *combined* with the path of the current page to compute the name of the destination page.</span></span>  <!-- Review: Original had The provided string is combined with the page name of the current page to compute the name of the destination page. -- page name, not page path -->

<span data-ttu-id="01266-269">상대적 이름 연결은 구조가 복잡한 사이트를 빌드할 때 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="01266-269">Relative name linking is useful when building sites with a complex structure.</span></span> <span data-ttu-id="01266-270">상대적 이름을 사용하여 한 폴더의 여러 페이지 간을 연결하는 경우 해당 폴더의 이름을 바꿀 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="01266-270">If you use relative names to link between pages in a folder, you can rename that folder.</span></span> <span data-ttu-id="01266-271">그래도 모든 링크가 작동합니다(폴더 이름을 포함하지 않기 때문).</span><span class="sxs-lookup"><span data-stu-id="01266-271">All the links still work (because they didn't include the folder name).</span></span>

## <a name="tempdata"></a><span data-ttu-id="01266-272">TempData</span><span class="sxs-lookup"><span data-stu-id="01266-272">TempData</span></span>

<span data-ttu-id="01266-273">ASP.NET Core는 [TempData](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.controller#Microsoft_AspNetCore_Mvc_Controller_TempData) 속성을 [컨트롤러](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.controller)에서 노출합니다.</span><span class="sxs-lookup"><span data-stu-id="01266-273">ASP.NET Core exposes the [TempData](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.controller#Microsoft_AspNetCore_Mvc_Controller_TempData) property on a [controller](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.controller).</span></span> <span data-ttu-id="01266-274">이 속성은 판독될 때까지 데이터를 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="01266-274">This property stores data until it is read.</span></span> <span data-ttu-id="01266-275">`Keep` 및 `Peek` 메서드를 사용하여 삭제 없이 데이터를 검사할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="01266-275">The `Keep` and `Peek` methods can be used to examine the data without deletion.</span></span> <span data-ttu-id="01266-276">`TempData`는 두 개 이상의 요청에 대한 데이터가 필요할 경우 리디렉션에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="01266-276">`TempData` is  useful for redirection, when data is needed for more than a single request.</span></span>

<span data-ttu-id="01266-277">`[TempData]` 특성은 ASP.NET Core 2.0의 새로운 기능이고 컨트롤러 및 페이지에서 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="01266-277">The `[TempData]` attribute is new in ASP.NET Core 2.0 and is supported on controllers and pages.</span></span>

<span data-ttu-id="01266-278">다음 코드는 `TempData`를 사용하여 `Message` 값을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="01266-278">The following code sets the value of `Message` using `TempData`:</span></span>

[!code-cs[main](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

<span data-ttu-id="01266-279">*Pages/Customers/Index.cshtml* 파일의 다음 태그는 `TempData`를 사용하여 `Message` 값을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="01266-279">The following markup in the *Pages/Customers/Index.cshtml* file displays the value of `Message` using `TempData`.</span></span>

```cshtml
<h3>Msg: @Model.Message</h3>
```

<span data-ttu-id="01266-280">*Pages/Customers/Index.cshtml.cs* 코드 숨김 파일은 `[TempData]` 특성을 `Message` 속성에 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="01266-280">The *Pages/Customers/Index.cshtml.cs* code-behind file applies the `[TempData]` attribute to the `Message` property.</span></span>

```cs
[TempData]
public string Message { get; set; }
```

<span data-ttu-id="01266-281">자세한 내용은 [TempData](xref:fundamentals/app-state#temp)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="01266-281">See [TempData](xref:fundamentals/app-state#temp) for more information.</span></span>

<a name="mhpp"></a>
## <a name="multiple-handlers-per-page"></a><span data-ttu-id="01266-282">페이지당 여러 처리기</span><span class="sxs-lookup"><span data-stu-id="01266-282">Multiple handlers per page</span></span>

<span data-ttu-id="01266-283">다음 페이지는 `asp-page-handler` 태그 도우미를 사용하여 두 개의 페이지 처리기에 대한 태그를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="01266-283">The following page generates markup for two page handlers using the `asp-page-handler` Tag Helper:</span></span>

[!code-cshtml[main](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<!-- Review: the FormActionTagHelper applies to all <form /> elements on a Razor page, even when there is no `asp-` attribute   -->

<span data-ttu-id="01266-284">이전 예제의 폼에는 두 개의 제출 단추가 있고, 각 단추는 `FormActionTagHelper`를 사용하여 다른 URL에 제출됩니다.</span><span class="sxs-lookup"><span data-stu-id="01266-284">The form in the preceding example has two submit buttons, each using the `FormActionTagHelper` to submit to a different URL.</span></span> <span data-ttu-id="01266-285">`asp-page-handler` 특성은 `asp-page`와 함께 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="01266-285">The `asp-page-handler` attribute is a companion to `asp-page`.</span></span> <span data-ttu-id="01266-286">`asp-page-handler`는 페이지에서 정의된 각 처리기 메서드에 제출되는 URL을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="01266-286">`asp-page-handler` generates URLs that submit to each of the handler methods defined by a page.</span></span> <span data-ttu-id="01266-287">샘플이 현재 페이지에 연결되어 있으므로 `asp-page`가 지정되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="01266-287">`asp-page` is not specified because the sample is linking to the current page.</span></span>

<span data-ttu-id="01266-288">코드 숨김 파일:</span><span class="sxs-lookup"><span data-stu-id="01266-288">The code-behind file:</span></span>

[!code-cs[main](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

<span data-ttu-id="01266-289">이전 코드는 *명명된 처리기 메서드*를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="01266-289">The preceding code uses *named handler methods*.</span></span> <span data-ttu-id="01266-290">명명된 처리기 메서드를 만들려면 `On<HTTP Verb>` 뒤와 `Async`(있는 경우) 앞에 이름의 텍스트를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="01266-290">Named handler methods are created by taking the text in the name after `On<HTTP Verb>` and before `Async` (if present).</span></span> <span data-ttu-id="01266-291">이전 예제에서 페이지 메서드는 OnPost**JoinList**Async 및 OnPost**JoinListUC**Async입니다.</span><span class="sxs-lookup"><span data-stu-id="01266-291">In the preceding example, the page methods are OnPost**JoinList**Async and OnPost**JoinListUC**Async.</span></span> <span data-ttu-id="01266-292">*OnPost* 및 *Async*가 제거된 처리기 이름은 `JoinList` 및 `JoinListUC`입니다.</span><span class="sxs-lookup"><span data-stu-id="01266-292">With *OnPost* and *Async* removed, the handler names are `JoinList` and `JoinListUC`.</span></span>

[!code-cshtml[main](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

<span data-ttu-id="01266-293">이전 코드를 사용할 경우 `OnPostJoinListAsync`에 제출되는 URL 경로는 `http://localhost:5000/Customers/CreateFATH?handler=JoinList`입니다.</span><span class="sxs-lookup"><span data-stu-id="01266-293">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `http://localhost:5000/Customers/CreateFATH?handler=JoinList`.</span></span> <span data-ttu-id="01266-294">`OnPostJoinListUCAsync`에 제출되는 URL 경로는 `http://localhost:5000/Customers/CreateFATH?handler=JoinListUC`입니다.</span><span class="sxs-lookup"><span data-stu-id="01266-294">The URL path that submits to `OnPostJoinListUCAsync` is `http://localhost:5000/Customers/CreateFATH?handler=JoinListUC`.</span></span>

## <a name="customizing-routing"></a><span data-ttu-id="01266-295">라우팅 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="01266-295">Customizing Routing</span></span>

<span data-ttu-id="01266-296">URL에서 쿼리 문자열 `?handler=JoinList`를 사용하지 않으려면 경로를 변경하여 처리기 이름을 URL의 경로 부분에 넣습니다.</span><span class="sxs-lookup"><span data-stu-id="01266-296">If you don't like the query string `?handler=JoinList` in the URL, you can change the route to put the handler name in the path portion of the URL.</span></span> <span data-ttu-id="01266-297">`@page` 지시문 뒤에 큰따옴표로 묶은 경로 템플릿을 추가하여 경로를 사용자 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="01266-297">You can customize the route by adding a route template enclosed in double quotes after the `@page` directive.</span></span>

[!code-cshtml[main](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

<span data-ttu-id="01266-298">이전 경로는 쿼리 문자열 대신 URL 경로에 처리기 이름을 넣습니다.</span><span class="sxs-lookup"><span data-stu-id="01266-298">The preceding route puts the handler name in the URL path instead of the query string.</span></span> <span data-ttu-id="01266-299">`handler` 뒤의 `?`는 경로 매개 변수가 선택 사항임을 의미합니다.</span><span class="sxs-lookup"><span data-stu-id="01266-299">The `?` following `handler` means the route parameter is optional.</span></span>

<span data-ttu-id="01266-300">`@page`를 사용하여 페이지 경로에 다른 세그먼트 및 매개 변수를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="01266-300">You can use `@page` to add additional segments and parameters to a page's route.</span></span> <span data-ttu-id="01266-301">무엇이든 페이지의 기본 경로에 **추가**됩니다.</span><span class="sxs-lookup"><span data-stu-id="01266-301">Whatever's there is **appended** to the default route of the page.</span></span> <span data-ttu-id="01266-302">절대 또는 가상 경로를 사용하여 페이지 경로를 변경하는 기능(예: `"~/Some/Other/Path"`)은 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="01266-302">Using an absolute or virtual path to change the page's route (like `"~/Some/Other/Path"`) is not supported.</span></span>

## <a name="configuration-and-settings"></a><span data-ttu-id="01266-303">구성 및 설정</span><span class="sxs-lookup"><span data-stu-id="01266-303">Configuration and settings</span></span>

<span data-ttu-id="01266-304">고급 옵션을 구성하려면 MVC 빌더에서 확장 메서드 `AddRazorPagesOptions`를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="01266-304">To configure advanced options, use the extension method `AddRazorPagesOptions` on the MVC builder:</span></span>

[!code-cs[main](index/sample/RazorPagesContacts/StartupAdvanced.cs?name=snippet_1)]

<span data-ttu-id="01266-305">현재 `RazorPagesOptions`를 사용하여 페이지의 루트 디렉터리를 설정하거나 페이지에 대한 응용 프로그램 모델 규칙을 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="01266-305">Currently you can use the `RazorPagesOptions` to set the root directory for pages, or add application model conventions for pages.</span></span> <span data-ttu-id="01266-306">나중에 이 방법으로 더 큰 확장성을 사용할 수 있기를 기대합니다.</span><span class="sxs-lookup"><span data-stu-id="01266-306">We hope to enable more extensibility this way in the future.</span></span>

<span data-ttu-id="01266-307">뷰를 미리 컴파일하려면 [Razor 뷰 컴파일](xref:mvc/views/view-compilation)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="01266-307">To precompile views, see [Razor view compilation](xref:mvc/views/view-compilation) .</span></span>

<span data-ttu-id="01266-308">[샘플 코드를 다운로드하거나 봅니다](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/razor-pages/index/sample).</span><span class="sxs-lookup"><span data-stu-id="01266-308">[Download or view sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/razor-pages/index/sample).</span></span>

<span data-ttu-id="01266-309">이 소개에 따라 빌드되는 [ASP.NET Core의 Razor 페이지 시작](xref:tutorials/razor-pages/razor-pages-start)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="01266-309">See [Getting started with Razor Pages in ASP.NET Core](xref:tutorials/razor-pages/razor-pages-start), which builds on this introduction.</span></span>
