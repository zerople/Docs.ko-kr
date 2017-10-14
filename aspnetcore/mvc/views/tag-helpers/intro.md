---
title: "ASP.NET Core에서 태그 도우미"
author: rick-anderson
description: "태그 도우미 정의 및 ASP.NET 코어에서 사용 하는 방법에 알아봅니다."
keywords: "ASP.NET Core, 태그 도우미"
ms.author: riande
manager: wpickett
ms.date: 7/14/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/views/tag-helpers/intro
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 78004aa370cac8b297fd7ede534260c83965ae79
ms.sourcegitcommit: 8f4d4fad1ca27adf9e396f5c205c9875a3963664
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/13/2017
---
# <a name="introduction-to-tag-helpers-in-aspnet-core"></a><span data-ttu-id="6b5ff-104">ASP.NET Core에서 태그 도우미 소개</span><span class="sxs-lookup"><span data-stu-id="6b5ff-104">Introduction to Tag Helpers in ASP.NET Core</span></span> 

<span data-ttu-id="6b5ff-105">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="6b5ff-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

## <a name="what-are-tag-helpers"></a><span data-ttu-id="6b5ff-106">태그 도우미 이란?</span><span class="sxs-lookup"><span data-stu-id="6b5ff-106">What are Tag Helpers?</span></span>

<span data-ttu-id="6b5ff-107">태그 도우미 만들기 및 Razor 파일에서 HTML 요소 렌더링에 참여 하도록 서버 쪽 코드를 사용 하도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-107">Tag Helpers enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span> <span data-ttu-id="6b5ff-108">예를 들어 기본 제공 `ImageTagHelper` 버전 번호를 이미지 이름에 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-108">For example, the built-in `ImageTagHelper` can append a version number to the image name.</span></span> <span data-ttu-id="6b5ff-109">이미지 변경 될 때마다 서버 없으므로 클라이언트 (오래 된 캐시 된 이미지) 하는 대신 현재 이미지를 가져올 보장 되는 이미지에 대 한 새로운 고유 버전을 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-109">Whenever the image changes, the server generates a new unique version for the image, so clients are guaranteed to get the current image (instead of a stale cached image).</span></span> <span data-ttu-id="6b5ff-110">-양식, 링크, 로드 자산 및 점점-공용 GitHub 리포지토리에 및 NuGet로 훨씬 더 사용할 수 있는 패키지 만들기와 같은 일반적인 작업에 대 한 기본 제공 태그 도우미 여러 가지가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-110">There are many built-in Tag Helpers for common tasks - such as creating forms, links, loading assets and more - and even more available in public GitHub repositories and as NuGet packages.</span></span> <span data-ttu-id="6b5ff-111">태그 도우미 C#에서 작성 하 고 요소 이름, 특성 이름 또는 부모 태그를 기반으로 하는 HTML 요소를 대상입니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-111">Tag Helpers are authored in C#, and they target HTML elements based on element name, attribute name, or parent tag.</span></span> <span data-ttu-id="6b5ff-112">예를 들어 기본 제공 `LabelTagHelper` HTML 대상으로 지정할 수 `<label>` 요소 때는 `LabelTagHelper` 특성이 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-112">For example, the built-in `LabelTagHelper` can target the HTML `<label>` element when the `LabelTagHelper` attributes are applied.</span></span> <span data-ttu-id="6b5ff-113">익숙한 경우 [HTML 도우미](http://stephenwalther.com/archive/2009/03/03/chapter-6-understanding-html-helpers), 태그 도우미 Razor 뷰에서 HTML 및 C# 전환을 명시적 줄입니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-113">If you're familiar with [HTML Helpers](http://stephenwalther.com/archive/2009/03/03/chapter-6-understanding-html-helpers), Tag Helpers reduce the explicit transitions between HTML and C# in Razor views.</span></span> <span data-ttu-id="6b5ff-114">대부분의 경우에서 HTML 도우미 특정 태그 도우미에는 다른 방법은 제공 하지만 태그 도우미는 HTML 도우미를 대체 하지 않고 각 HTML 도우미에 대 한 태그 도우미 없기 인식 하는 것이 중요 합니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-114">In many cases, HTML Helpers provide an alternative approach to a specific Tag Helper, but it's important to recognize that Tag Helpers do not replace HTML Helpers and there is not a Tag Helper for each HTML Helper.</span></span> <span data-ttu-id="6b5ff-115">[태그 도우미 HTML 도우미에 비해](#tag-helpers-compared-to-html-helpers) 자세히 차이점에 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-115">[Tag Helpers compared to HTML Helpers](#tag-helpers-compared-to-html-helpers) explains the differences in more detail.</span></span>

## <a name="what-tag-helpers-provide"></a><span data-ttu-id="6b5ff-116">태그 도우미의 제공</span><span class="sxs-lookup"><span data-stu-id="6b5ff-116">What Tag Helpers provide</span></span>

<span data-ttu-id="6b5ff-117">**HTML에 적합 한 개발 경험이** 가장 부분 태그 도우미를 사용 하 여 Razor 태그와 동일 하 게 하는 표준 HTML입니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-117">**An HTML-friendly development experience** For the most part, Razor markup using Tag Helpers looks like standard HTML.</span></span> <span data-ttu-id="6b5ff-118">HTML/CSS/JavaScript 관련 지식이 필요 프런트 엔드 디자이너 C# Razor 구문을 몰라도 Razor를 편집할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-118">Front-end designers conversant with HTML/CSS/JavaScript can edit Razor without learning C# Razor syntax.</span></span>

<span data-ttu-id="6b5ff-119">**HTML 및 Razor 태그를 만들기 위한 다양 한 IntelliSense 환경을** 이것이 HTML 도우미, 서버 쪽에서 Razor 뷰 태그 만들기는 이전 접근 방식 크게 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-119">**A rich IntelliSense environment for creating HTML and Razor markup** This is in sharp contrast to HTML Helpers, the previous approach to server-side creation of markup in Razor views.</span></span> <span data-ttu-id="6b5ff-120">[태그 도우미 HTML 도우미에 비해](#tag-helpers-compared-to-html-helpers) 자세히 차이점에 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-120">[Tag Helpers compared to HTML Helpers](#tag-helpers-compared-to-html-helpers) explains the differences in more detail.</span></span> <span data-ttu-id="6b5ff-121">[태그 도우미에 대 한 IntelliSense 지원을](#intellisense-support-for-tag-helpers) IntelliSense 환경에 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-121">[IntelliSense support for Tag Helpers](#intellisense-support-for-tag-helpers) explains the IntelliSense environment.</span></span> <span data-ttu-id="6b5ff-122">Razor C# 구문 익숙한에 개발자는 생산성을 높일 태그 도우미를 사용 하 여 C# Razor 태그를 작성 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-122">Even developers experienced with Razor C# syntax are more productive using Tag Helpers than writing C# Razor markup.</span></span>

<span data-ttu-id="6b5ff-123">**서버에만 사용할 수 있는 정보를 사용 하 여 유지 관리 가능한 코드 및 보다 강력 하 고 안정적 이며 보다 생산적이 고 생성할 수 있도록 방법은** 예를 들어 지금까지 이미지를 업데이트 하는 방법에 대 한 리소스로 변경한 경우 이미지의 이름을 변경 하려면 이미지입니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-123">**A way to make you more productive and able to produce more robust, reliable, and maintainable code using information only available on the server** For example, historically the mantra on updating images was to change the name of the image when you change the image.</span></span> <span data-ttu-id="6b5ff-124">성능상의 이유로 이미지를 적극적으로 캐시 합니다 및 유효 하지 않은 복사본 가져오기 클라이언트 이미지의 이름을 변경 하지 않는 한 될 위험이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-124">Images should be aggressively cached for performance reasons, and unless you change the name of an image, you risk clients getting a stale copy.</span></span> <span data-ttu-id="6b5ff-125">지금까지 이미지 편집 된 후 이름은 변경할 수 해야 했으며 업데이트 하는 데 필요한 웹 응용 프로그램의 이미지에 대 한 각 참조.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-125">Historically, after an image was edited, the name had to be changed and each reference to the image in the web app needed to be updated.</span></span> <span data-ttu-id="6b5ff-126">뿐만 아니라이 매우 많이 소비 것도 오류가 발생할 (있습니다 수 참조를 누락, 잘못 된 문자열 등을 실수로 입력) 노동 무엇입니까 기본 제공 `ImageTagHelper` 수를 자동으로 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-126">Not only is this very labor intensive, it's also error prone (you could miss a reference, accidentally enter the wrong string, etc.) The built-in `ImageTagHelper` can do this for you automatically.</span></span> <span data-ttu-id="6b5ff-127">`ImageTagHelper` 는 버전을 추가할 수 이미지에 대 한 고유한 새 버전에 서버 이미지 변경 될 때마다 자동으로 생성 되므로를 이미지 이름 번호입니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-127">The `ImageTagHelper` can append a version number to the image name, so whenever the image changes, the server automatically generates a new unique version for the image.</span></span> <span data-ttu-id="6b5ff-128">클라이언트는 현재 이미지 될 보장 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-128">Clients are guaranteed to get the current image.</span></span> <span data-ttu-id="6b5ff-129">사용 하 여 견고성과 근로 절약이 기본적으로 제공 된 `ImageTagHelper`합니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-129">This robustness and labor savings comes essentially free by using the `ImageTagHelper`.</span></span>

<span data-ttu-id="6b5ff-130">대부분의 기본 제공 태그 도우미 기존 HTML 요소를 대상 하 고 요소에 대 한 서버 쪽 특성을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-130">Most of the built-in Tag Helpers target existing HTML elements and provide server-side attributes for the element.</span></span> <span data-ttu-id="6b5ff-131">예를 들어는 `<input>` 에서 대부분의 보기 중에서 사용 하는 요소는 *뷰/계정* 폴더에는 `asp-for` 렌더링 된 HTML로 지정 된 모델 속성의 이름을 추출 하는 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-131">For example, the `<input>` element used in many of the views in the *Views/Account* folder contains the `asp-for` attribute, which extracts the name of the specified model property into the rendered HTML.</span></span> <span data-ttu-id="6b5ff-132">다음 Razor 태그:</span><span class="sxs-lookup"><span data-stu-id="6b5ff-132">The following Razor markup:</span></span>

```html
<label asp-for="Email"></label>
```

<span data-ttu-id="6b5ff-133">다음 HTML을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-133">Generates the following HTML:</span></span>

```html
<label for="Email">Email</label>
```

<span data-ttu-id="6b5ff-134">`asp-for` 특성으로 사용할 수는 `For` 속성에는 `LabelTagHelper`합니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-134">The `asp-for` attribute is made available by the `For` property in the `LabelTagHelper`.</span></span> <span data-ttu-id="6b5ff-135">참조 [태그 도우미 제작](authoring.md) 자세한 정보에 대 한 합니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-135">See [Authoring Tag Helpers](authoring.md) for more information.</span></span>

## <a name="managing-tag-helper-scope"></a><span data-ttu-id="6b5ff-136">태그 도우미의 범위를 관리합니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-136">Managing Tag Helper scope</span></span>

<span data-ttu-id="6b5ff-137">태그 도우미 범위가의 조합에 의해 제어 됩니다 `@addTagHelper`, `@removeTagHelper`, 및 "!" 옵트아웃 문자입니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-137">Tag Helpers scope is controlled by a combination of `@addTagHelper`, `@removeTagHelper`, and the "!" opt-out character.</span></span>

<a name="add-helper-label"></a>

### <a name="addtaghelper-makes-tag-helpers-available"></a><span data-ttu-id="6b5ff-138">`@addTagHelper`태그 도우미를 사용할 수 있도록 설정</span><span class="sxs-lookup"><span data-stu-id="6b5ff-138">`@addTagHelper` makes Tag Helpers available</span></span>

<span data-ttu-id="6b5ff-139">라는 새 ASP.NET Core 웹 앱을 만들 경우 *AuthoringTagHelpers* (인증 안 함),으로 다음 *Views/_ViewImports.cshtml* 파일을 프로젝트에 추가 됩니다:</span><span class="sxs-lookup"><span data-stu-id="6b5ff-139">If you create a new ASP.NET Core web app named *AuthoringTagHelpers* (with no authentication), the following *Views/_ViewImports.cshtml* file will be added to your project:</span></span>

[!code-html[Main](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImportsCopy.cshtml?highlight=2&range=2-3)]

<span data-ttu-id="6b5ff-140">`@addTagHelper` 지시문 태그 도우미 보기를 사용할 수 있도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-140">The `@addTagHelper` directive makes Tag Helpers available to the view.</span></span> <span data-ttu-id="6b5ff-141">이 경우에 파일 보기 *Views/_ViewImports.cshtml*에 있는 모든 보기 파일에 상속 되는 기본적으로는 *뷰* 폴더 및 하위 디렉터리; 태그 도우미를 사용할 수 있도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-141">In this case, the view file is *Views/_ViewImports.cshtml*, which by default is inherited by all view files in the *Views* folder and sub-directories; making Tag Helpers available.</span></span> <span data-ttu-id="6b5ff-142">위의 코드에 와일드 카드 구문을 사용 하 여 ("\*") 되도록 지정 하려면 지정된 된 어셈블리의 모든 태그 도우미 (*Microsoft.AspNetCore.Mvc.TagHelpers*)의 모든 뷰 파일을 사용할 수 있습니다는 *뷰* 디렉터리 또는 하위 디렉터리입니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-142">The code above uses the wildcard syntax ("\*") to specify that all Tag Helpers in the specified assembly (*Microsoft.AspNetCore.Mvc.TagHelpers*) will be available to every view file in the *Views* directory or sub-directory.</span></span> <span data-ttu-id="6b5ff-143">후 첫 번째 매개 변수 `@addTagHelper` 로드할 태그 도우미를 지정 합니다 (사용 하 여 "\*" 모든 태그 도우미에 대 한), 두 번째 매개 변수 "Microsoft.AspNetCore.Mvc.TagHelpers" 태그 도우미를 포함 하는 어셈블리를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-143">The first parameter after `@addTagHelper` specifies the Tag Helpers to load (we are using "\*" for all Tag Helpers), and the second parameter "Microsoft.AspNetCore.Mvc.TagHelpers" specifies the assembly containing the Tag Helpers.</span></span> <span data-ttu-id="6b5ff-144">*Microsoft.AspNetCore.Mvc.TagHelpers* 는 어셈블리의 기본 제공 ASP.NET 핵심 태그 도우미입니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-144">*Microsoft.AspNetCore.Mvc.TagHelpers* is the assembly for the built-in ASP.NET Core Tag Helpers.</span></span>

<span data-ttu-id="6b5ff-145">이 프로젝트의 태그 도우미의 모든 (이라는 어셈블리를 만드는 *AuthoringTagHelpers*), 다음을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-145">To expose all of the Tag Helpers in this project (which creates an assembly named *AuthoringTagHelpers*), you would use the following:</span></span>

[!code-html[Main](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImportsCopy.cshtml?highlight=3)]

<span data-ttu-id="6b5ff-146">프로젝트에 포함 된 경우는 `EmailTagHelper` 기본 네임 스페이스를 가진 (`AuthoringTagHelpers.TagHelpers.EmailTagHelper`), 태그 도우미의 정규화 된 이름 (FQN)를 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-146">If your project contains an `EmailTagHelper` with the default namespace (`AuthoringTagHelpers.TagHelpers.EmailTagHelper`), you can provide the fully qualified name (FQN) of the Tag Helper:</span></span>

```cshtml
@using AuthoringTagHelpers
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@addTagHelper AuthoringTagHelpers.TagHelpers.EmailTagHelper, AuthoringTagHelpers
```

<span data-ttu-id="6b5ff-147">태그 도우미는 FQN를 사용 하 여 뷰를 추가 하려면 먼저는 FQN가 추가 (`AuthoringTagHelpers.TagHelpers.EmailTagHelper`), 및 다음 어셈블리 이름 (*AuthoringTagHelpers*).</span><span class="sxs-lookup"><span data-stu-id="6b5ff-147">To add a Tag Helper to a view using an FQN, you first add the FQN (`AuthoringTagHelpers.TagHelpers.EmailTagHelper`), and then the assembly name (*AuthoringTagHelpers*).</span></span> <span data-ttu-id="6b5ff-148">대부분의 개발자 선호 하는 "\*" 와일드 카드 구문을 합니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-148">Most developers prefer to use the  "\*" wildcard syntax.</span></span> <span data-ttu-id="6b5ff-149">와일드 카드 구문을 사용 하면 와일드 카드 문자를 삽입할 수 있습니다 "\*" 접미사는 FQN의으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-149">The wildcard syntax allows you to insert the wildcard character "\*" as the suffix in an FQN.</span></span> <span data-ttu-id="6b5ff-150">예를 들어 다음 지시문를 가져오므로 `EmailTagHelper`:</span><span class="sxs-lookup"><span data-stu-id="6b5ff-150">For example, any of the following directives will bring in the `EmailTagHelper`:</span></span>

```csharp
@addTagHelper AuthoringTagHelpers.TagHelpers.E*, AuthoringTagHelpers
@addTagHelper AuthoringTagHelpers.TagHelpers.Email*, AuthoringTagHelpers
```

<span data-ttu-id="6b5ff-151">앞에서 언급 한 바와 같이 추가 `@addTagHelper` 지시문을 *Views/_ViewImports.cshtml* 파일을 사용 하면 태그 도우미의 모든 뷰 파일에 사용할 수는 *뷰* 디렉터리 및 하위 디렉터리입니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-151">As mentioned previously, adding the `@addTagHelper` directive to the *Views/_ViewImports.cshtml* file makes the Tag Helper available to all view files in the *Views* directory and sub-directories.</span></span> <span data-ttu-id="6b5ff-152">사용할 수는 `@addTagHelper` 태그 도우미는 보기에만 노출 하는 데 옵트인 하려면 특정 보기 파일에 지시문입니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-152">You can use the `@addTagHelper` directive in specific view files if you want to opt-in to exposing the Tag Helper to only those views.</span></span>

<a name="remove-razor-directives-label"></a>

### <a name="removetaghelper-removes-tag-helpers"></a><span data-ttu-id="6b5ff-153">`@removeTagHelper`태그 도우미를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-153">`@removeTagHelper` removes Tag Helpers</span></span>

<span data-ttu-id="6b5ff-154">`@removeTagHelper` 동일한 두 개의 매개 변수를 갖는 `@addTagHelper`, 이전에 추가 하는 태그 도우미를 해제 합니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-154">The `@removeTagHelper` has the same two parameters as `@addTagHelper`, and it removes a Tag Helper that was previously added.</span></span> <span data-ttu-id="6b5ff-155">예를 들어 `@removeTagHelper` 보기에서 특정 보기 제거 된 지정 된 태그 도우미에 적용 합니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-155">For example, `@removeTagHelper` applied to a specific view removes the specified Tag Helper from the view.</span></span> <span data-ttu-id="6b5ff-156">사용 하 여 `@removeTagHelper` 에 *Views/Folder/_ViewImports.cshtml* 파일에서 뷰 모두에서 지정된 된 태그 도우미 제거 *폴더*합니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-156">Using `@removeTagHelper` in a *Views/Folder/_ViewImports.cshtml* file removes the specified Tag Helper from all of the views in *Folder*.</span></span>

### <a name="controlling-tag-helper-scope-with-the-viewimportscshtml-file"></a><span data-ttu-id="6b5ff-157">사용 하 여 태그 도우미 범위 제어는 *_ViewImports.cshtml* 파일</span><span class="sxs-lookup"><span data-stu-id="6b5ff-157">Controlling Tag Helper scope with the *_ViewImports.cshtml* file</span></span>

<span data-ttu-id="6b5ff-158">추가할 수는 *_ViewImports.cshtml* 모든 보기 폴더 및 보기에 엔진은 모두 해당 파일의 지시문을 적용 및 *Views/_ViewImports.cshtml* 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-158">You can add a *_ViewImports.cshtml* to any view folder, and the view engine applies the directives from both that file and the *Views/_ViewImports.cshtml* file.</span></span> <span data-ttu-id="6b5ff-159">빈을 추가한 경우 *Views/Home/_ViewImports.cshtml* 에 대 한 파일의 *홈* 뷰, 때문에 변경 되지 것는 *_ViewImports.cshtml* 파일은 가산적입니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-159">If you added an empty *Views/Home/_ViewImports.cshtml* file for the *Home* views, there would be no change because the *_ViewImports.cshtml* file is additive.</span></span> <span data-ttu-id="6b5ff-160">모든 `@addTagHelper` 지시문에 추가 된 *Views/Home/_ViewImports.cshtml* 파일 (기본값에 없는 *Views/_ViewImports.cshtml* 파일) 보기에 해당 태그 도우미 노출에는 *홈* 폴더입니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-160">Any `@addTagHelper` directives you add to the *Views/Home/_ViewImports.cshtml* file (that are not in the default *Views/_ViewImports.cshtml* file) would expose those Tag Helpers to views only in the *Home* folder.</span></span>

<a name="opt-out"></a>

### <a name="opting-out-of-individual-elements"></a><span data-ttu-id="6b5ff-161">개별 요소를 옵트아웃</span><span class="sxs-lookup"><span data-stu-id="6b5ff-161">Opting out of individual elements</span></span>

<span data-ttu-id="6b5ff-162">태그 도우미 옵트아웃 문자로 요소 수준에서 태그 도우미를 사용 하지 않도록 설정할 수 있습니다 ("!").</span><span class="sxs-lookup"><span data-stu-id="6b5ff-162">You can disable a Tag Helper at the element level with the Tag Helper opt-out character ("!").</span></span> <span data-ttu-id="6b5ff-163">예를 들어 `Email` 유효성 검사에 사용 되지 않는지는 `<span>` 태그 도우미 옵트아웃 문자로:</span><span class="sxs-lookup"><span data-stu-id="6b5ff-163">For example, `Email` validation is disabled in the `<span>` with the Tag Helper opt-out character:</span></span>

```csharp
<!span asp-validation-for="Email" class="text-danger"></!span>
```

<span data-ttu-id="6b5ff-164">여는 태그와 닫는 태그에 태그 도우미 옵트아웃 문자를 적용 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-164">You must apply the Tag Helper opt-out character to the opening and closing tag.</span></span> <span data-ttu-id="6b5ff-165">(Visual Studio 편집기 자동으로 추가 옵트아웃 문자 닫는 태그를 지정 하 여는 태그를 하나 추가) 합니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-165">(The Visual Studio editor automatically adds the opt-out character to the closing tag when you add one to the opening tag).</span></span> <span data-ttu-id="6b5ff-166">옵트아웃 문자를 추가 하면 요소 및 태그 도우미 특성 구별 되는 글꼴에 더 이상 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-166">After you add the opt-out character, the element and Tag Helper attributes are no longer displayed in a distinctive font.</span></span>

<a name="prefix-razor-directives-label"></a>

### <a name="using-taghelperprefix-to-make-tag-helper-usage-explicit"></a><span data-ttu-id="6b5ff-167">사용 하 여 `@tagHelperPrefix` 명시적으로 만들어야 하 태그 도우미 사용</span><span class="sxs-lookup"><span data-stu-id="6b5ff-167">Using `@tagHelperPrefix` to make Tag Helper usage explicit</span></span>

<span data-ttu-id="6b5ff-168">`@tagHelperPrefix` 지시문을 사용 하면 태그 도우미 지원 기능이 사용 하 고 명시적 태그 도우미 사용을 확인 하려면 태그 접두사 문자열을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-168">The `@tagHelperPrefix` directive allows you to specify a tag prefix string to enable Tag Helper support and to make Tag Helper usage explicit.</span></span> <span data-ttu-id="6b5ff-169">예를 들어 다음 태그를 추가할 수 있습니다는 *Views/_ViewImports.cshtml* 파일:</span><span class="sxs-lookup"><span data-stu-id="6b5ff-169">For example, you could add the following markup to the *Views/_ViewImports.cshtml* file:</span></span>

```html
@tagHelperPrefix th:
```
<span data-ttu-id="6b5ff-170">아래 코드 이미지 태그 도우미 접두사로 설정 되어 `th:`, 접두사를 사용 하는 요소에만 `th:` 태그 도우미 (태그 도우미 지원 요소에서 보유 한 고유한 글꼴)를 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-170">In the code image below, the Tag Helper prefix is set to `th:`, so only those elements using the prefix `th:` support Tag Helpers (Tag Helper-enabled elements have a distinctive font).</span></span> <span data-ttu-id="6b5ff-171">`<label>` 및 `<input>` 요소 태그 도우미 접두사 대 한 태그 도우미를 사용할 수는 동안는 `<span>` 요소는 그렇지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-171">The `<label>` and `<input>` elements have the Tag Helper prefix and are Tag Helper-enabled, while the `<span>` element does not.</span></span>

![이미지](intro/_static/thp.png)

<span data-ttu-id="6b5ff-173">에 적용 되는 동일한 계층 구조 규칙 `@addTagHelper` 에 적용 `@tagHelperPrefix`합니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-173">The same hierarchy rules that apply to `@addTagHelper` also apply to `@tagHelperPrefix`.</span></span>

## <a name="intellisense-support-for-tag-helpers"></a><span data-ttu-id="6b5ff-174">태그 도우미에 대 한 IntelliSense 지원</span><span class="sxs-lookup"><span data-stu-id="6b5ff-174">IntelliSense support for Tag Helpers</span></span>

<span data-ttu-id="6b5ff-175">Visual Studio에서 새 ASP.NET 웹 앱을 만들 때 "Microsoft.AspNetCore.Razor.Tools" NuGet 패키지를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-175">When you create a new ASP.NET web app in Visual Studio, it adds the NuGet package "Microsoft.AspNetCore.Razor.Tools".</span></span> <span data-ttu-id="6b5ff-176">태그 도우미 도구를 추가 하는 패키지입니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-176">This is the package that adds Tag Helper tooling.</span></span>

<span data-ttu-id="6b5ff-177">HTML을 작성 하는 것이 좋습니다. `<label>` 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-177">Consider writing an HTML `<label>` element.</span></span> <span data-ttu-id="6b5ff-178">입력으로 `<l` Visual Studio 편집기에서 IntelliSense는 일치 하는 요소를 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-178">As soon as you enter `<l` in the Visual Studio editor, IntelliSense displays matching elements:</span></span>

![이미지](intro/_static/label.png)

<span data-ttu-id="6b5ff-180">뿐만 아니라 HTML 도움말 아이콘 하지만 가져올 수 있습니다 (의 "@" symbol with "<>" 그 아래).</span><span class="sxs-lookup"><span data-stu-id="6b5ff-180">Not only do you get HTML help, but the icon (the "@" symbol with "<>" under it).</span></span>

![이미지](intro/_static/tagSym.png)

<span data-ttu-id="6b5ff-182">태그 도우미의 대상으로 요소를 식별 합니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-182">identifies the element as targeted by Tag Helpers.</span></span> <span data-ttu-id="6b5ff-183">순수 HTML 요소 (예:는 `fieldset`) "<>" 아이콘을 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-183">Pure HTML elements (such as the `fieldset`) display the "<>" icon.</span></span>

<span data-ttu-id="6b5ff-184">순수 HTML `<label>` 빨간색에서 특성 갈색 글꼴로 기본 Visual Studio 색 테마) (사용 하 여 HTML 태그를 표시 하는 태그와 파란색에서 특성 값입니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-184">A pure HTML `<label>` tag displays the HTML tag (with the default Visual Studio color theme) in a brown font, the attributes in red, and the attribute values in blue.</span></span>

![이미지](intro/_static/LableHtmlTag.png)

<span data-ttu-id="6b5ff-186">입력 한 후 `<label`, IntelliSense 사용 가능한 HTML/CSS 특성 및 태그 도우미 대상 특성을 나열 합니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-186">After you enter `<label`, IntelliSense lists the available HTML/CSS attributes and the Tag Helper-targeted attributes:</span></span>

![이미지](intro/_static/labelattr.png)

<span data-ttu-id="6b5ff-188">IntelliSense 문 완성을 사용 하면 선택한 값을 사용 하 여 문을 완료 하려면 tab 키를 입력할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-188">IntelliSense statement completion allows you to enter the tab key to complete the statement with the selected value:</span></span>

![이미지](intro/_static/stmtcomplete.png)

<span data-ttu-id="6b5ff-190">입력 된 태그 도우미 특성 값으로 태그 및 특성 글꼴이 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-190">As soon as a Tag Helper attribute is entered, the tag and attribute fonts change.</span></span> <span data-ttu-id="6b5ff-191">기본 Visual Studio "Blue" 또는 "Light" 색 테마를 사용 하는 글꼴은 굵은 자주입니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-191">Using the default Visual Studio "Blue" or "Light" color theme, the font is bold purple.</span></span> <span data-ttu-id="6b5ff-192">"어두운" 테마를 사용 하 여 글꼴을 굵게 청록색 합니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-192">If you're using the "Dark" theme the font is bold teal.</span></span> <span data-ttu-id="6b5ff-193">이 문서에 있는 이미지는 기본 테마를 사용 하 여 수행 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-193">The images in this document were taken using the default theme.</span></span>

![이미지](intro/_static/labelaspfor2.png)

<span data-ttu-id="6b5ff-195">Visual Studio를 입력할 수 있는 *CompleteWord* 바로 가기 (Ctrl + 스페이스바는는 [기본](https://docs.microsoft.com/visualstudio/ide/default-keyboard-shortcuts-in-visual-studio) 큰따옴표 안에 (""), 현재 위치는 C#, C# 클래스에서 것 처럼 및 합니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-195">You can enter the Visual Studio *CompleteWord* shortcut (Ctrl +spacebar is the [default](https://docs.microsoft.com/visualstudio/ide/default-keyboard-shortcuts-in-visual-studio) inside the double quotes (""), and you are now in C#, just like you would be in a C# class.</span></span> <span data-ttu-id="6b5ff-196">IntelliSense는 페이지 모델에서 모든 메서드 및 속성을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-196">IntelliSense displays all the methods and properties on the page model.</span></span> <span data-ttu-id="6b5ff-197">메서드 및 속성은 사용할 수 있는 속성 형식이 `ModelExpression`합니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-197">The methods and properties are available because the property type is `ModelExpression`.</span></span> <span data-ttu-id="6b5ff-198">아래 그림에서는 편집 중인 I는 `Register` 보기 하므로 `RegisterViewModel` ´ ï ´ 합니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-198">In the image below, I'm editing the `Register` view, so the `RegisterViewModel` is available.</span></span>

![이미지](intro/_static/intellemail.png)

<span data-ttu-id="6b5ff-200">IntelliSense에는 속성과 페이지에서 모델에 사용할 수 있는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-200">IntelliSense lists the properties and methods available to the model on the page.</span></span> <span data-ttu-id="6b5ff-201">다양 한 IntelliSense 환경을 CSS 클래스를 선택할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-201">The rich IntelliSense environment helps you select the CSS class:</span></span>

![이미지](intro/_static/iclass.png)

![이미지](intro/_static/intel3.png)

## <a name="tag-helpers-compared-to-html-helpers"></a><span data-ttu-id="6b5ff-204">HTML 도우미에 비해 태그 도우미</span><span class="sxs-lookup"><span data-stu-id="6b5ff-204">Tag Helpers compared to HTML Helpers</span></span>

<span data-ttu-id="6b5ff-205">태그 도우미 Razor 뷰에서 HTML 요소에 연결 하는 동안 [HTML 도우미](http://stephenwalther.com/archive/2009/03/03/chapter-6-understanding-html-helpers) 메서드 Razor 뷰에서 html 섞인 상태로 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-205">Tag Helpers attach to HTML elements in Razor views, while [HTML Helpers](http://stephenwalther.com/archive/2009/03/03/chapter-6-understanding-html-helpers) are invoked as methods interspersed with HTML in Razor views.</span></span> <span data-ttu-id="6b5ff-206">"캡션" 하는 CSS 클래스를 사용 하 여 HTML 레이블을 만듭니다 다음 Razor 태그를 고려 합니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-206">Consider the following Razor markup, which creates an HTML label with the CSS class "caption":</span></span>

```html
@Html.Label("FirstName", "First Name:", new {@class="caption"})
```

<span data-ttu-id="6b5ff-207">에 (`@`) 기호 이라는 Razor 코드의 시작 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-207">The at (`@`) symbol tells Razor this is the start of code.</span></span> <span data-ttu-id="6b5ff-208">다음 두 매개 변수 ("FirstName" 및 "이름:")은 문자열 이므로 [IntelliSense](https://docs.microsoft.com/visualstudio/ide/using-intellisense) 도울 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-208">The next two parameters ("FirstName" and "First Name:") are strings, so [IntelliSense](https://docs.microsoft.com/visualstudio/ide/using-intellisense) can't help.</span></span> <span data-ttu-id="6b5ff-209">마지막 인수:</span><span class="sxs-lookup"><span data-stu-id="6b5ff-209">The last argument:</span></span>

```html
new {@class="caption"}
```

<span data-ttu-id="6b5ff-210">익명 개체 특성을 나타내는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-210">Is an anonymous object used to represent attributes.</span></span> <span data-ttu-id="6b5ff-211">때문에 **클래스** 예약된 된 키워드는 C#을 사용 하 여는 `@` 강제로 해석 하는 모든 C# 기호 "@class=" 기호로 (속성 이름).</span><span class="sxs-lookup"><span data-stu-id="6b5ff-211">Because **class** is a reserved keyword in C#, you use the `@` symbol to force C# to interpret "@class=" as a symbol (property name).</span></span> <span data-ttu-id="6b5ff-212">프런트 엔드 디자이너로 (다른 사람이 HTML/CSS/JavaScript 및 기타 클라이언트 기술에 잘 알고 있지만 C# 및 Razor 익숙하지), 줄의 대부분은 외래 합니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-212">To a front-end designer (someone familiar with HTML/CSS/JavaScript and other client technologies but not familiar with C# and Razor), most of the line is foreign.</span></span> <span data-ttu-id="6b5ff-213">IntelliSense 활용 하지 못하는 상태로 전체 줄을 작성 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-213">The entire line must be authored with no help from IntelliSense.</span></span>

<span data-ttu-id="6b5ff-214">사용 하 여는 `LabelTagHelper`, 동일한 태그로 작성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-214">Using the `LabelTagHelper`, the same markup can be written as:</span></span>

![이미지](intro/_static/label2.png)

<span data-ttu-id="6b5ff-216">입력으로 태그 도우미 버전과 `<l` Visual Studio 편집기에서 IntelliSense는 일치 하는 요소를 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-216">With the Tag Helper version, as soon as you enter `<l` in the Visual Studio editor, IntelliSense displays matching elements:</span></span>

![이미지](intro/_static/label.png)

<span data-ttu-id="6b5ff-218">IntelliSense를 사용 하면 전체 줄을 쓸 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-218">IntelliSense helps you write the entire line.</span></span> <span data-ttu-id="6b5ff-219">`LabelTagHelper` 의 내용을 설정도 기본적으로 `asp-for` 특성 값 ("FirstName")을 "이름"; 각 새 대문자 문자 발생 공백으로 속성 이름으로 구성 하는 문장 속성 카멜식 대/소문자를 변환 합니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-219">The `LabelTagHelper` also defaults to setting the content of the `asp-for` attribute value ("FirstName") to "First Name"; It converts camel-cased properties to a sentence composed of the property name with a space where each new upper-case letter occurs.</span></span> <span data-ttu-id="6b5ff-220">다음 태그:</span><span class="sxs-lookup"><span data-stu-id="6b5ff-220">In the following markup:</span></span>

![이미지](intro/_static/label2.png)

<span data-ttu-id="6b5ff-222">생성합니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-222">generates:</span></span>

```html
<label class="caption" for="FirstName">First Name</label>
```

<span data-ttu-id="6b5ff-223">카멜식 대 문장 대/소문자를 사용 하는 콘텐츠를 콘텐츠를 추가 하는 경우 사용 되지 않습니다는 `<label>`합니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-223">The camel-cased to sentence-cased content is not used if you add content to the `<label>`.</span></span> <span data-ttu-id="6b5ff-224">예:</span><span class="sxs-lookup"><span data-stu-id="6b5ff-224">For example:</span></span>

![이미지](intro/_static/1stName.png)

<span data-ttu-id="6b5ff-226">생성합니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-226">generates:</span></span>

```html
<label class="caption" for="FirstName">Name First</label>
```

<span data-ttu-id="6b5ff-227">다음 코드 이미지 폼 부분을 보여 줍니다.는 *Views/Account/Register.cshtml* 템플릿에서 생성 된 레거시 ASP.NET 4.5.x MVC Visual Studio 2015에 포함 된 Razor 보기.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-227">The following code image shows the Form portion of the *Views/Account/Register.cshtml* Razor view generated from the legacy ASP.NET 4.5.x MVC template included with Visual Studio 2015.</span></span>

![이미지](intro/_static/regCS.png)

<span data-ttu-id="6b5ff-229">Visual Studio 편집기에는 C# 코드가 회색 배경과 함께 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-229">The Visual Studio editor displays C# code with a grey background.</span></span> <span data-ttu-id="6b5ff-230">예를 들어는 `AntiForgeryToken` HTML 도우미:</span><span class="sxs-lookup"><span data-stu-id="6b5ff-230">For example, the `AntiForgeryToken` HTML Helper:</span></span>

```html
@Html.AntiForgeryToken()
```

<span data-ttu-id="6b5ff-231">회색 배경과 함께 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-231">is displayed with a grey background.</span></span> <span data-ttu-id="6b5ff-232">대부분의 레지스터 보기에서 태그는 C#입니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-232">Most of the markup in the Register view is C#.</span></span> <span data-ttu-id="6b5ff-233">비교 하 여 태그 도우미를 사용 하 여 해당 하는 방법:</span><span class="sxs-lookup"><span data-stu-id="6b5ff-233">Compare that to the equivalent approach using Tag Helpers:</span></span>

![이미지](intro/_static/regTH.png)

<span data-ttu-id="6b5ff-235">태그를 훨씬 쉽고 명확한 읽기, 편집 및 HTML 도우미 방식에 비해 유지 관리 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-235">The markup is much cleaner and easier to read, edit, and maintain than the HTML Helpers approach.</span></span> <span data-ttu-id="6b5ff-236">C# 코드는 서버에 대해 알고 있어야 하는 최소 수준으로 줄어듭니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-236">The C# code is reduced to the minimum that the server needs to know about.</span></span> <span data-ttu-id="6b5ff-237">Visual Studio 편집기에는 고유한 글꼴로 태그 도우미의 대상 태그가 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-237">The Visual Studio editor displays markup targeted by a Tag Helper in a distinctive font.</span></span>

<span data-ttu-id="6b5ff-238">고려는 *전자 메일* 그룹:</span><span class="sxs-lookup"><span data-stu-id="6b5ff-238">Consider the *Email* group:</span></span>

[!code-csharp[Main](intro/sample/Register.cshtml?range=12-18)]

<span data-ttu-id="6b5ff-239">각 "asp-" 특성을 "Email"의 값 갖지만 "Email" 문자열이 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-239">Each of the "asp-" attributes has a value of "Email", but "Email" is not a string.</span></span> <span data-ttu-id="6b5ff-240">이 컨텍스트에서 "Email"는 C# 모델 식 속성에 대 한는 `RegisterViewModel`합니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-240">In this context, "Email" is the C# model expression property for the `RegisterViewModel`.</span></span>

<span data-ttu-id="6b5ff-241">Visual Studio 편집기를 사용 하면 작성 **모든** Visual Studio에서는 대부분의 HTML 도우미 접근 방식에서 코드에 대 한 도움말을 제공 하는 동안 레지스터 폼의 태그 도우미 접근 방식에서 변경 내용입니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-241">The Visual Studio editor helps you write **all** of the markup in the Tag Helper approach of the register form, while Visual Studio provides no help for most of the code in the HTML Helpers approach.</span></span> <span data-ttu-id="6b5ff-242">[태그 도우미에 대 한 IntelliSense 지원을](#intellisense-support-for-tag-helpers) Visual Studio 편집기에서 태그 도우미 작업 정보를 확인할 이동 합니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-242">[IntelliSense support for Tag Helpers](#intellisense-support-for-tag-helpers) goes into detail on working with Tag Helpers in the Visual Studio editor.</span></span>

## <a name="tag-helpers-compared-to-web-server-controls"></a><span data-ttu-id="6b5ff-243">웹 서버 컨트롤에 비해 태그 도우미</span><span class="sxs-lookup"><span data-stu-id="6b5ff-243">Tag Helpers compared to Web Server Controls</span></span>

* <span data-ttu-id="6b5ff-244">태그 도우미;와 연결 하는 요소를 소유 하지 않는 단순히 요소 및 콘텐츠 렌더링에 참여 합니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-244">Tag Helpers don't own the element they're associated with; they simply participate in the rendering of the element and content.</span></span> <span data-ttu-id="6b5ff-245">ASP.NET [웹 서버 컨트롤](https://msdn.microsoft.com/library/7698y1f0.aspx) 선언 되 고 페이지에 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-245">ASP.NET [Web Server controls](https://msdn.microsoft.com/library/7698y1f0.aspx) are declared and invoked on a page.</span></span>

* <span data-ttu-id="6b5ff-246">[웹 서버 컨트롤의](https://msdn.microsoft.com/library/zsyt68f1.aspx) 특수 주기를 어렵게 만들 수 있는 개발 및 디버깅 합니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-246">[Web Server controls](https://msdn.microsoft.com/library/zsyt68f1.aspx) have a non-trivial lifecycle that can make developing and debugging difficult.</span></span>

* <span data-ttu-id="6b5ff-247">웹 서버 컨트롤을 사용 하는 클라이언트 컨트롤을 사용 하 여 클라이언트 문서 개체 모델 (DOM) 요소에 기능을 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-247">Web Server controls allow you to add functionality to the client Document Object Model (DOM) elements by using a client control.</span></span> <span data-ttu-id="6b5ff-248">태그 도우미가 없는 DOM.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-248">Tag Helpers have no DOM.</span></span>

* <span data-ttu-id="6b5ff-249">웹 서버 컨트롤 브라우저 자동 검색을 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-249">Web Server controls include automatic browser detection.</span></span> <span data-ttu-id="6b5ff-250">태그 도우미 브라우저에 모를 합니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-250">Tag Helpers have no knowledge of the browser.</span></span>

* <span data-ttu-id="6b5ff-251">같은 요소에 여러 태그 도우미 작동할 수 있습니다 (참조 [태그 도우미 방지 충돌](https://docs.microsoft.com/aspnet/core/mvc/views/tag-helpers/authoring#avoiding-tag-helper-conflicts) ) 동안 일반적으로 웹 서버 컨트롤을 구성할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-251">Multiple Tag Helpers can act on the same element (see [Avoiding Tag Helper conflicts](https://docs.microsoft.com/aspnet/core/mvc/views/tag-helpers/authoring#avoiding-tag-helper-conflicts) ) while you typically can't compose Web Server controls.</span></span>

* <span data-ttu-id="6b5ff-252">태그 도우미의 태그와, 범위가 지정 하는 HTML 요소의 콘텐츠를 수정할 수 있지만 페이지에 다른 항목이 직접 수정 하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-252">Tag Helpers can modify the tag and content of HTML elements that they're scoped to, but don't directly modify anything else on a page.</span></span> <span data-ttu-id="6b5ff-253">웹 서버 컨트롤은 덜 구체적인 범위가; 페이지의 다른 부분에 영향을 주는 작업을 수행할 수 있으며 의도 하지 않은 부작용을 사용 하도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-253">Web Server controls have a less specific scope and can perform actions that affect other parts of your page; enabling unintended side effects.</span></span>

* <span data-ttu-id="6b5ff-254">형식 변환기를 사용 하 여 문자열을 개체로 변환 하는 웹 서버 컨트롤.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-254">Web Server controls use type converters to convert strings into objects.</span></span> <span data-ttu-id="6b5ff-255">태그 도우미와에서 작업 하면서 기본적으로 C#, 형식 변환 수행 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-255">With Tag Helpers, you work natively in C#, so you don't need to do type conversion.</span></span>

* <span data-ttu-id="6b5ff-256">웹 서버 사용을 제어 [System.ComponentModel](https://docs.microsoft.com/dotnet/api/system.componentmodel) 구성 요소와 컨트롤의 런타임 및 디자인 타임 동작을 구현 합니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-256">Web Server controls use [System.ComponentModel](https://docs.microsoft.com/dotnet/api/system.componentmodel) to implement the run-time and design-time behavior of components and controls.</span></span> <span data-ttu-id="6b5ff-257">`System.ComponentModel`기본 클래스와 특성 및 형식 변환기 구현, 소스 데이터에 바인딩, 및 구성 요소 라이선스에 대 한 인터페이스를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-257">`System.ComponentModel` includes the base classes and interfaces for implementing attributes and type converters, binding to data sources, and licensing components.</span></span> <span data-ttu-id="6b5ff-258">일반적으로 파생 되는 태그 도우미 즉 `TagHelper`, 및 `TagHelper` 두 개의 메서드를 노출 하는 기본 클래스 `Process` 및 `ProcessAsync`합니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-258">Contrast that to Tag Helpers, which typically derive from `TagHelper`, and the `TagHelper` base class exposes only two methods, `Process` and `ProcessAsync`.</span></span>

## <a name="customizing-the-tag-helper-element-font"></a><span data-ttu-id="6b5ff-259">태그 도우미 요소 글꼴을 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="6b5ff-259">Customizing the Tag Helper element font</span></span>

<span data-ttu-id="6b5ff-260">글꼴 및 색 지정에서 사용자 지정할 수 있습니다 **도구** > **옵션** > **환경** > **글꼴 및 색**:</span><span class="sxs-lookup"><span data-stu-id="6b5ff-260">You can customize the font and colorization from **Tools** > **Options** > **Environment** > **Fonts and Colors**:</span></span>

![이미지](intro/_static/fontoptions2.png)

## <a name="additional-resources"></a><span data-ttu-id="6b5ff-262">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="6b5ff-262">Additional Resources</span></span>

* [<span data-ttu-id="6b5ff-263">태그 도우미 작성</span><span class="sxs-lookup"><span data-stu-id="6b5ff-263">Authoring Tag Helpers</span></span>](authoring.md)
* [<span data-ttu-id="6b5ff-264">양식 사용</span><span class="sxs-lookup"><span data-stu-id="6b5ff-264">Working with Forms </span></span>](../working-with-forms.md)
* <span data-ttu-id="6b5ff-265">[GitHub의 TagHelperSamples](https://github.com/dpaquette/TagHelperSamples) 사용 하기 위한 태그 도우미 샘플이 포함 되어 [부트스트랩](http://getbootstrap.com/)합니다.</span><span class="sxs-lookup"><span data-stu-id="6b5ff-265">[TagHelperSamples on GitHub](https://github.com/dpaquette/TagHelperSamples) contains Tag Helper samples for working with [Bootstrap](http://getbootstrap.com/).</span></span>

<!--
* [Working with Forms ](xref:mvc/views/working-with-forms)
-->
