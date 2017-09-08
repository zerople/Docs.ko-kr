---
title: "전역화 및 지역화"
author: rick-anderson
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 01/14/2017
ms.topic: article
ms.assetid: 7f275a09-f118-41c9-88d1-8de52d6a5aa1
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/localization
ms.openlocfilehash: 70f11cc9de8e885745e7d08cb98ac68e3cc8ef95
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/11/2017
---
# <a name="globalization-and-localization"></a><span data-ttu-id="82ba3-103">전역화 및 지역화</span><span class="sxs-lookup"><span data-stu-id="82ba3-103">Globalization and localization</span></span>

<span data-ttu-id="82ba3-104">여 [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [구재석 Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://twitter.com/NadeemAfana), 및 [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span><span class="sxs-lookup"><span data-stu-id="82ba3-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://twitter.com/NadeemAfana), and [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span></span>

<span data-ttu-id="82ba3-105">ASP.NET Core를 사용 하 여 다국어 웹 사이트 만들기 하도록 사이트를 더 광범위 한에 도달 하면 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-105">Creating a multilingual website with ASP.NET Core will allow your site to reach a wider audience.</span></span> <span data-ttu-id="82ba3-106">ASP.NET Core 다른 언어와 문화권에 지역화를 위한 서비스 및 미들웨어를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-106">ASP.NET Core provides services and middleware for localizing into different languages and cultures.</span></span>

<span data-ttu-id="82ba3-107">국제화 포함 [전역화](https://msdn.microsoft.com/library/aa292081(v=vs.71).aspx) 및 [지역화](https://msdn.microsoft.com/library/aa292137(v=vs.71).aspx)합니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-107">Internationalization involves [Globalization](https://msdn.microsoft.com/library/aa292081(v=vs.71).aspx) and [Localization](https://msdn.microsoft.com/library/aa292137(v=vs.71).aspx).</span></span> <span data-ttu-id="82ba3-108">세계화는 서로 다른 culture를 지 원하는 응용 프로그램을 설계 하는 과정입니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-108">Globalization is the process of designing apps that support different cultures.</span></span> <span data-ttu-id="82ba3-109">세계화에는 입력, 표시 및 정의 된 집합이 특정 지역과 관련 된 언어 스크립트의 출력에 대 한 지원을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-109">Globalization adds support for input, display, and output of a defined set of language scripts that relate to specific geographic areas.</span></span>

<span data-ttu-id="82ba3-110">지역화는 이미 특정 문화권/로캘로 지역화 가능성에 대 한 처리 전역화 된 응용 프로그램을 위해 조정 하는 과정입니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-110">Localization is the process of adapting a globalized app, which you have already processed for localizability, to a particular culture/locale.</span></span>  <span data-ttu-id="82ba3-111">자세한 내용은 참조 **전역화 및 지역화 용어** 이 문서의 끝 부분입니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-111">For more information see **Globalization and localization terms** near the end of this document.</span></span>

<span data-ttu-id="82ba3-112">응용 프로그램 지역화 과정은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-112">App localization involves the following:</span></span>

1. <span data-ttu-id="82ba3-113">응용 프로그램의 콘텐츠를 지역화할 수 있도록</span><span class="sxs-lookup"><span data-stu-id="82ba3-113">Make the app's content localizable</span></span>

2. <span data-ttu-id="82ba3-114">언어 및 지원 되는 문화권에 대 한 지역화 된 리소스를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-114">Provide localized resources for the languages and cultures you support</span></span>

3. <span data-ttu-id="82ba3-115">각 요청에 대 한 언어/문화권을 선택 하는 전략을 구현 합니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-115">Implement a strategy to select the language/culture for each request</span></span>

## <a name="make-the-apps-content-localizable"></a><span data-ttu-id="82ba3-116">응용 프로그램의 콘텐츠를 지역화할 수 있도록</span><span class="sxs-lookup"><span data-stu-id="82ba3-116">Make the app's content localizable</span></span>

<span data-ttu-id="82ba3-117">ASP.NET Core에 도입 된 `IStringLocalizer` 및 `IStringLocalizer<T>` 지역화 된 응용 프로그램을 개발할 때 생산성을 향상 하도록 설계 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-117">Introduced in ASP.NET Core, `IStringLocalizer` and `IStringLocalizer<T>` were architected to improve productivity when developing localized apps.</span></span> <span data-ttu-id="82ba3-118">`IStringLocalizer`사용 하 여는 [ResourceManager](https://msdn.microsoft.com/library/system.resources.resourcemanager(v=vs.110).aspx) 및 [ResourceReader](https://msdn.microsoft.com/library/system.resources.resourcereader(v=vs.110).aspx) 런타임 시 문화권별 리소스를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-118">`IStringLocalizer` uses the [ResourceManager](https://msdn.microsoft.com/library/system.resources.resourcemanager(v=vs.110).aspx) and [ResourceReader](https://msdn.microsoft.com/library/system.resources.resourcereader(v=vs.110).aspx) to provide culture-specific resources at run time.</span></span> <span data-ttu-id="82ba3-119">간단한 인터페이스에는 인덱서 및 `IEnumerable` 지역화 된 문자열을 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-119">The simple interface has an indexer and an `IEnumerable` for returning localized strings.</span></span> <span data-ttu-id="82ba3-120">`IStringLocalizer`리소스 파일에는 기본 언어 문자열을 저장 하도록 요구 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-120">`IStringLocalizer` doesn't require you to store the default language strings in a resource file.</span></span> <span data-ttu-id="82ba3-121">지역화를 위한 대상으로 하는 응용 프로그램을 개발할 수 있으며 초기 개발에에서 리소스 파일을 만들 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-121">You can develop an app targeted for localization and not need to create resource files early in development.</span></span> <span data-ttu-id="82ba3-122">아래 코드 지역화에 대 한 "에 대 한 Title" 문자열을 래핑하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-122">The code below shows how to wrap the string "About Title" for localization.</span></span>

<span data-ttu-id="82ba3-123">[!code-csharp[Main](localization/sample/Controllers/AboutController.cs)]</span><span class="sxs-lookup"><span data-stu-id="82ba3-123">[!code-csharp[Main](localization/sample/Controllers/AboutController.cs)]</span></span>

<span data-ttu-id="82ba3-124">위의 코드에는 `IStringLocalizer<T>` 구현에서 가져온 [종속성 주입](dependency-injection.md)합니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-124">In the code above, the `IStringLocalizer<T>` implementation comes from [Dependency Injection](dependency-injection.md).</span></span> <span data-ttu-id="82ba3-125">"에 대 한 Title"의 지역화 된 값을 찾을 수 없습니다는 경우 인덱서 키 반환 되 면, 즉 "에 대 한 Title" 문자열입니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-125">If the localized value of "About Title" is not found, then the indexer key is returned, that is, the string "About Title".</span></span> <span data-ttu-id="82ba3-126">앱의 언어 리터럴 문자열 기본값을 그대로 수 있으며 응용 프로그램 개발에 집중할 수 있도록 로컬라이저의에서 래핑할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-126">You can leave the default language literal strings in the app and wrap them in the localizer, so that you can focus on developing the app.</span></span> <span data-ttu-id="82ba3-127">기본 언어를 사용 하 여 앱을 개발 하 고이 정보를 먼저 기본 리소스 파일을 만들지 않고 지역화 단계에 대 한 준비 합니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-127">You develop your app with your default language and prepare it for the localization step without first creating a default resource file.</span></span> <span data-ttu-id="82ba3-128">또는 기존의 접근 방식을 사용 하 고 기본 언어 문자열을 검색 하 여 키를 입력 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-128">Alternatively, you can use the traditional approach and provide a key to retrieve the default language string.</span></span> <span data-ttu-id="82ba3-129">대부분의 개발자 하지 않는 경우 기본 언어의 새 워크플로 *.resx* 파일과 단순히 문자열 리터럴은 래핑 응용 프로그램을 지역화 하는 오버 헤드를 줄일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-129">For many developers the new workflow of not having a default language *.resx* file and simply wrapping the string literals can reduce the overhead of localizing an app.</span></span> <span data-ttu-id="82ba3-130">다른 개발자가 쉽게 하 긴 문자열 리터럴과 함께 작동 하 고 지역화 된 문자열을 업데이트 하려면 쉽게 만들 수 것 처럼 일반적인 작업 흐름을 선호 합니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-130">Other developers will prefer the traditional work flow as it can make it easier to work with longer string literals and make it easier to update localized strings.</span></span>

<span data-ttu-id="82ba3-131">사용 하 여는 `IHtmlLocalizer<T>` HTML을 포함 하는 리소스에 대 한 구현 합니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-131">Use the `IHtmlLocalizer<T>` implementation for resources that contain HTML.</span></span> <span data-ttu-id="82ba3-132">`IHtmlLocalizer`HTML은 리소스 문자열이 아닌 리소스 문자열에 서식이 지정 된 인수를 인코딩합니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-132">`IHtmlLocalizer` HTML encodes arguments that are formatted in the resource string, but not the resource string.</span></span> <span data-ttu-id="82ba3-133">값만 아래 샘플에서 강조 표시 `name` 매개 변수는 HTML로 인코딩하지 합니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-133">In the sample highlighted below, only the value of `name` parameter is HTML encoded.</span></span>

<span data-ttu-id="82ba3-134">[!code-csharp[Main](../fundamentals/localization/sample/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]</span><span class="sxs-lookup"><span data-stu-id="82ba3-134">[!code-csharp[Main](../fundamentals/localization/sample/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]</span></span>

<span data-ttu-id="82ba3-135">참고: 일반적으로 원하는 텍스트와 HTML이 아닌 필드를 지역화 합니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-135">Note: You generally want to only localize text and not HTML.</span></span>

<span data-ttu-id="82ba3-136">가장 낮은 수준에서 얻을 수 있습니다 `IStringLocalizerFactory` 부재 중 [종속성 주입](dependency-injection.md):</span><span class="sxs-lookup"><span data-stu-id="82ba3-136">At the lowest level, you can get `IStringLocalizerFactory` out of [Dependency Injection](dependency-injection.md):</span></span>

<span data-ttu-id="82ba3-137">[!code-csharp[Main](localization/sample/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]</span><span class="sxs-lookup"><span data-stu-id="82ba3-137">[!code-csharp[Main](localization/sample/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]</span></span>

<span data-ttu-id="82ba3-138">위의 코드를 보여 줍니다 각각 두 개의 팩터리 메서드를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-138">The code above demonstrates each of the two factory create methods.</span></span>

<span data-ttu-id="82ba3-139">영역에서 컨트롤러로 지역화 된 문자열을 분할 하거나 방금 하나 이상 포함 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-139">You can partition your localized strings by controller, area, or have just one container.</span></span> <span data-ttu-id="82ba3-140">샘플 응용 프로그램을 더미 클래스 이름은 `SharedResource` 공유 리소스에 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-140">In the sample app, a dummy class named `SharedResource` is used for shared resources.</span></span>

<span data-ttu-id="82ba3-141">[!code-csharp[Main](localization/sample/Resources/SharedResource.cs)]</span><span class="sxs-lookup"><span data-stu-id="82ba3-141">[!code-csharp[Main](localization/sample/Resources/SharedResource.cs)]</span></span>

<span data-ttu-id="82ba3-142">사용 하 여 일부 개발자는 `Startup` 전역 또는 공유 문자열을 포함 하는 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-142">Some developers use the `Startup` class to contain global or shared strings.</span></span>  <span data-ttu-id="82ba3-143">아래 샘플에는 `InfoController` 및 `SharedResource` 지역화 담당자에 게 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-143">In the sample below, the `InfoController` and the `SharedResource` localizers are used:</span></span>

<span data-ttu-id="82ba3-144">[!code-csharp[Main](localization/sample/Controllers/InfoController.cs?range=9-26)]</span><span class="sxs-lookup"><span data-stu-id="82ba3-144">[!code-csharp[Main](localization/sample/Controllers/InfoController.cs?range=9-26)]</span></span>

## <a name="view-localization"></a><span data-ttu-id="82ba3-145">보기 지역화</span><span class="sxs-lookup"><span data-stu-id="82ba3-145">View localization</span></span>

<span data-ttu-id="82ba3-146">`IViewLocalizer` 서비스 제공에 대 한 지역화 된 문자열을 [보기](http://docs.asp.net/projects/mvc/en/latest/views/index.html)합니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-146">The `IViewLocalizer` service provides localized strings for a [view](http://docs.asp.net/projects/mvc/en/latest/views/index.html).</span></span> <span data-ttu-id="82ba3-147">`ViewLocalizer` 클래스는이 인터페이스를 구현 하 고 보기 파일 경로에서 리소스는 위치를 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-147">The `ViewLocalizer` class implements this interface and finds the resource location from the view file path.</span></span> <span data-ttu-id="82ba3-148">다음 코드의 기본 구현을 사용 하는 방법을 보여 줍니다 `IViewLocalizer`:</span><span class="sxs-lookup"><span data-stu-id="82ba3-148">The following code shows how to use the default implementation of `IViewLocalizer`:</span></span>

<span data-ttu-id="82ba3-149">[!code-HTML[Main](localization/sample/Views/Home/About.cshtml)]</span><span class="sxs-lookup"><span data-stu-id="82ba3-149">[!code-HTML[Main](localization/sample/Views/Home/About.cshtml)]</span></span>

<span data-ttu-id="82ba3-150">기본 구현은 `IViewLocalizer` 보기의 파일 이름에 따라 리소스 파일을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-150">The default implementation of `IViewLocalizer` finds the resource file based on the view's file name.</span></span> <span data-ttu-id="82ba3-151">공유 전역 리소스 파일을 사용할 수 있는 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-151">There is no option to use a global shared resource file.</span></span> <span data-ttu-id="82ba3-152">`ViewLocalizer`사용 하 여 지역화 구현 `IHtmlLocalizer`Razor HTML 하지 않으므로 지역화 된 문자열을 인코딩합니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-152">`ViewLocalizer` implements the localizer using `IHtmlLocalizer`, so Razor doesn't HTML encode the localized string.</span></span> <span data-ttu-id="82ba3-153">리소스 문자열을 매개 변수화 할 수 및 `IViewLocalizer` 는 매개 변수가 있지만 리소스 문자열이 아니라 HTML 인코드 합니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-153">You can parameterize resource strings and `IViewLocalizer` will HTML encode the parameters, but not the resource string.</span></span> <span data-ttu-id="82ba3-154">Razor에 다음 태그를 고려 합니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-154">Consider the following Razor markup:</span></span>

```HTML
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
   ```

<span data-ttu-id="82ba3-155">프랑스어 리소스 파일은 다음 포함할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-155">A French resource file could contain the following:</span></span>

| <span data-ttu-id="82ba3-156">Key</span><span class="sxs-lookup"><span data-stu-id="82ba3-156">Key</span></span> | <span data-ttu-id="82ba3-157">값</span><span class="sxs-lookup"><span data-stu-id="82ba3-157">Value</span></span> |
| ----- | ------ |
| `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b> ` |

<span data-ttu-id="82ba3-158">렌더링된 된 보기에는 리소스 파일에서 HTML 태그를 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-158">The rendered view would contain the HTML markup from the resource file.</span></span>

<span data-ttu-id="82ba3-159">메모:</span><span class="sxs-lookup"><span data-stu-id="82ba3-159">Notes:</span></span>
- <span data-ttu-id="82ba3-160">보기 지역화 "Localization.AspNetCore.TagHelpers" NuGet 패키지가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-160">View localization requires the "Localization.AspNetCore.TagHelpers" NuGet package.</span></span>
- <span data-ttu-id="82ba3-161">일반적으로 텍스트와 HTML이 아닌 필드를 지역화 하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-161">You generally want to only localize text and not HTML.</span></span>

<span data-ttu-id="82ba3-162">보기에서 공유 리소스 파일을 사용 하려면 주입 `IHtmlLocalizer<T>`:</span><span class="sxs-lookup"><span data-stu-id="82ba3-162">To use a shared resource file in a view, inject `IHtmlLocalizer<T>`:</span></span>

<span data-ttu-id="82ba3-163">[!code-HTML[Main](../fundamentals/localization/sample/Views/Test/About.cshtml?highlight=5,12)]</span><span class="sxs-lookup"><span data-stu-id="82ba3-163">[!code-HTML[Main](../fundamentals/localization/sample/Views/Test/About.cshtml?highlight=5,12)]</span></span>

## <a name="dataannotations-localization"></a><span data-ttu-id="82ba3-164">DataAnnotations 지역화</span><span class="sxs-lookup"><span data-stu-id="82ba3-164">DataAnnotations localization</span></span>

<span data-ttu-id="82ba3-165">DataAnnotations 오류 메시지와 지역화 되므로 `IStringLocalizer<T>`합니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-165">DataAnnotations error messages are localized with `IStringLocalizer<T>`.</span></span> <span data-ttu-id="82ba3-166">옵션을 사용 하 여 `ResourcesPath = "Resources"`는 오류 메시지를 `RegisterViewModel` 다음 경로 중 하나에 저장할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-166">Using the option `ResourcesPath = "Resources"`, the error messages in `RegisterViewModel` can be stored in either of the following paths:</span></span>

* <span data-ttu-id="82ba3-167">Resources/ViewModels.Account.RegisterViewModel.fr.resx</span><span class="sxs-lookup"><span data-stu-id="82ba3-167">Resources/ViewModels.Account.RegisterViewModel.fr.resx</span></span>
* <span data-ttu-id="82ba3-168">Resources/ViewModels/Account/RegisterViewModel.fr.resx</span><span class="sxs-lookup"><span data-stu-id="82ba3-168">Resources/ViewModels/Account/RegisterViewModel.fr.resx</span></span>

<span data-ttu-id="82ba3-169">[!code-csharp[Main](localization/sample/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]</span><span class="sxs-lookup"><span data-stu-id="82ba3-169">[!code-csharp[Main](localization/sample/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]</span></span>

<span data-ttu-id="82ba3-170">ASP.NET Core MVC 1.1.0 및 더 높은, 비-유효성 검사 특성의 지역화 됩니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-170">In ASP.NET Core MVC 1.1.0 and higher, non-validation attributes are localized.</span></span> <span data-ttu-id="82ba3-171">ASP.NET Core 1.0 MVC는 **하지** 유효성 검사 아닌 특성에 대 한 지역화 된 문자열을 조회 합니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-171">ASP.NET Core MVC 1.0 does **not** look up localized strings for non-validation attributes.</span></span>

<a name=one-resource-string-multiple-classes></a>
### <a name="using-one-resource-string-for-multiple-classes"></a><span data-ttu-id="82ba3-172">하나의 리소스 문자열을 사용 하 여 다중 클래스</span><span class="sxs-lookup"><span data-stu-id="82ba3-172">Using one resource string for multiple classes</span></span>

<span data-ttu-id="82ba3-173">다음 코드에는 여러 클래스를 사용 하 여 유효성 검사 특성에 대 한 리소스 문자열을 사용 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-173">The following code shows how to use one resource string for validation attributes with multiple classes:</span></span>

```
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddDataAnnotationsLocalization(options => {
            options.DataAnnotationLocalizerProvider = (type, factory) =>
                factory.Create(typeof(SharedResource));
        });
}
```

<span data-ttu-id="82ba3-174">위의 코드 `SharedResource` 유효성 검사 메시지 저장 되는 위치에 해당 하는 resx 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-174">In the preceeding code, `SharedResource` is the class corresponding to the resx where your validation messages are stored.</span></span> <span data-ttu-id="82ba3-175">이 접근 방식 DataAnnotations만 사용할 `SharedResource`, 각 클래스에 대 한 리소스 대신 합니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-175">With this approach,  DataAnnotations will only use `SharedResource`, rather than the resource for each class.</span></span> 

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a><span data-ttu-id="82ba3-176">언어 및 지원 되는 문화권에 대 한 지역화 된 리소스를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-176">Provide localized resources for the languages and cultures you support</span></span>  

### <a name="supportedcultures-and-supporteduicultures"></a><span data-ttu-id="82ba3-177">SupportedCultures 및 SupportedUICultures</span><span class="sxs-lookup"><span data-stu-id="82ba3-177">SupportedCultures and SupportedUICultures</span></span>

<span data-ttu-id="82ba3-178">ASP.NET Core를 사용 하면 두 문화권 값을 지정할 수 있습니다 `SupportedCultures` 및 `SupportedUICultures`합니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-178">ASP.NET Core allows you to specify two culture values, `SupportedCultures` and `SupportedUICultures`.</span></span> <span data-ttu-id="82ba3-179">[CultureInfo](https://msdn.microsoft.com/library/system.globalization.cultureinfo(v=vs.110).aspx) 개체에 대 한 `SupportedCultures` 날짜, 시간, 숫자 및 통화 형식 같은 culture에 종속 된 함수의 결과 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-179">The [CultureInfo](https://msdn.microsoft.com/library/system.globalization.cultureinfo(v=vs.110).aspx) object for `SupportedCultures` determines the results of culture-dependent functions, such as date, time, number, and currency formatting.</span></span> <span data-ttu-id="82ba3-180">`SupportedCultures`또한 텍스트, 대/소문자 규칙 및 문자열 비교의 정렬 순서를 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-180">`SupportedCultures` also determines the sorting order of text, casing conventions, and string comparisons.</span></span> <span data-ttu-id="82ba3-181">참조 [CultureInfo.CurrentCulture](https://msdn.microsoft.com/library/system.globalization.cultureinfo.currentculture%28v=vs.110%29.aspx) 에 대 한 자세한 정보는 서버가 문화권을 가져오는 방식입니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-181">See [CultureInfo.CurrentCulture](https://msdn.microsoft.com/library/system.globalization.cultureinfo.currentculture%28v=vs.110%29.aspx) for more info on how the server gets the Culture.</span></span> <span data-ttu-id="82ba3-182">`SupportedUICultures` 이것은 문자열 해석 결정 (에서 *.resx* 파일)을 조회 하 여는 [ResourceManager](https://msdn.microsoft.com/library/system.resources.resourcemanager(v=vs.110).aspx)합니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-182">The `SupportedUICultures` determines which translates strings (from *.resx* files) are looked up by the [ResourceManager](https://msdn.microsoft.com/library/system.resources.resourcemanager(v=vs.110).aspx).</span></span> <span data-ttu-id="82ba3-183">`ResourceManager` 의해 결정 되는 culture 관련 문자열을 단순히 조회 `CurrentUICulture`합니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-183">The `ResourceManager` simply looks up culture-specific strings that is determined by `CurrentUICulture`.</span></span> <span data-ttu-id="82ba3-184">.NET의 모든 스레드는 `CurrentCulture` 및 `CurrentUICulture` 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-184">Every thread in .NET has `CurrentCulture` and `CurrentUICulture` objects.</span></span> <span data-ttu-id="82ba3-185">ASP.NET Core 문화권 종속 기능을 렌더링 하는 경우 이러한 값을 검사 합니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-185">ASP.NET Core inspects these values when rendering culture-dependent functions.</span></span> <span data-ttu-id="82ba3-186">예를 들어, 현재 스레드 문화권 "EN-US" (영어, United States)로 설정 되어 있으면 `DateTime.Now.ToLongDateString()` 이지만 if "목요일, 2016 년 2 월 18,", 표시 `CurrentCulture` 설정 된 "ES-ES" 스페인어 (스페인)에 출력 됩니다 "jueves, 18 febrero de-de 2016" 입니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-186">For example, if the current thread's culture is set to "en-US" (English, United States), `DateTime.Now.ToLongDateString()` displays "Thursday, February 18, 2016", but if `CurrentCulture` is set to "es-ES" (Spanish, Spain) the output will be "jueves, 18 de febrero de 2016".</span></span>

## <a name="working-with-resource-files"></a><span data-ttu-id="82ba3-187">리소스 파일 작업</span><span class="sxs-lookup"><span data-stu-id="82ba3-187">Working with resource files</span></span>

<span data-ttu-id="82ba3-188">리소스 파일은 코드에서 지역화 가능한 문자열을 구분 하는 데 유용한 메커니즘이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-188">A resource file is a useful mechanism for separating localizable strings from code.</span></span> <span data-ttu-id="82ba3-189">기본이 아닌 언어에 대 한 번역 된 문자열은 격리 된 *.resx* 리소스 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-189">Translated strings for the non-default language are isolated *.resx* resource files.</span></span> <span data-ttu-id="82ba3-190">예를 들어 라는 스페인어 리소스 파일을 만드는 경우가 *Welcome.es.resx* 포함 된 문자열을 변환 합니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-190">For example, you might want to create Spanish resource file named *Welcome.es.resx* containing translated strings.</span></span> <span data-ttu-id="82ba3-191">"es" 스페인어 언어 코드입니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-191">"es" is the language code for Spanish.</span></span> <span data-ttu-id="82ba3-192">Visual Studio에서이 리소스 파일을 만들려면:</span><span class="sxs-lookup"><span data-stu-id="82ba3-192">To create this resource file in Visual Studio:</span></span>

1. <span data-ttu-id="82ba3-193">**솔루션 탐색기**, 리소스 파일을 포함 하는 폴더를 마우스 오른쪽 단추로 클릭 > **추가** > **새 항목**합니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-193">In **Solution Explorer**, right click on the folder which will contain the resource file > **Add** > **New Item**.</span></span>

    ![중첩 된 상황에 맞는 메뉴: 솔루션 탐색기에서 상황에 맞는 메뉴는 리소스에 대 한 열려 있습니다.](localization/_static/newi.png)

2. <span data-ttu-id="82ba3-196">에 **설치 된 템플릿 검색** 상자 "resource"를 입력 하 고 파일 이름을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-196">In the **Search installed templates** box, enter "resource" and name the file.</span></span>

    ![새 항목 추가 대화 상자](localization/_static/res.png)

3. <span data-ttu-id="82ba3-198">에 키 값 (네이티브 문자열)을 입력의 **이름** 열과의 번역된 된 문자열의 **값** 열입니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-198">Enter the key value (native string) in the **Name** column and the translated string in the **Value** column.</span></span>

    ![이름 열에 hello 및 값 열에 있는 단어 Hola (스페인어에서 Hello) Welcome.es.resx 파일 (스페인어 시작 리소스 파일)](localization/_static/hola.png)

    <span data-ttu-id="82ba3-200">Visual Studio는 표시 된 *Welcome.es.resx* 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-200">Visual Studio shows the *Welcome.es.resx* file.</span></span>

    ![시작 스페인어 (es) 리소스 파일을 나타내는 솔루션 탐색기](localization/_static/se.png)

<a name="error"></a>

<span data-ttu-id="82ba3-202">Visual Studio 2017 미리 보기 버전 15.3를 사용 하는 경우에 리소스 편집기에서 오류 표시기를 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-202">If you are using Visual Studio 2017 Preview version 15.3, you'll get an error indicator in the resource editor.</span></span> <span data-ttu-id="82ba3-203">제거는 *ResXFileCodeGenerator* 에서 값의 *사용자 지정 도구* 속성 표에서이 오류 메시지를 방지 하려면:</span><span class="sxs-lookup"><span data-stu-id="82ba3-203">Remove the *ResXFileCodeGenerator*  value from the *Custom Tool*  properties grid to prevent this error message:</span></span>

![Resx 편집기](localization/_static/err.png)

<span data-ttu-id="82ba3-205">또한이 오류를 무시할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-205">Alternatively, you can ignore this error.</span></span> <span data-ttu-id="82ba3-206">다음 릴리스에서이 문제를 해결 하기 위해 노력 합니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-206">We hope to fix this in the next release.</span></span>

## <a name="resource-file-naming"></a><span data-ttu-id="82ba3-207">리소스 파일 이름 지정</span><span class="sxs-lookup"><span data-stu-id="82ba3-207">Resource file naming</span></span>

<span data-ttu-id="82ba3-208">어셈블리 이름에서 뺀 해당 클래스의 전체 유형 이름에 대 한 리소스 이름이 지정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-208">Resources are named for the full type name of their class minus the assembly name.</span></span> <span data-ttu-id="82ba3-209">인 주 어셈블리는 프로젝트에서 예를 들어 프랑스 리소스 `LocalizationWebsite.Web.dll` 클래스에 대 한 `LocalizationWebsite.Web.Startup` 이름이 *Startup.fr.resx*합니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-209">For example, a French resource in a project whose main assembly is `LocalizationWebsite.Web.dll` for the class `LocalizationWebsite.Web.Startup` would be named *Startup.fr.resx*.</span></span> <span data-ttu-id="82ba3-210">클래스에 대 한 리소스 `LocalizationWebsite.Web.Controllers.HomeController` 이름이 *Controllers.HomeController.fr.resx*합니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-210">A resource for the class `LocalizationWebsite.Web.Controllers.HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="82ba3-211">대상된 클래스의 네임 스페이스는 어셈블리 이름과 동일 하지는 경우 전체 유형 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-211">If your targeted class's namespace is not the same as the assembly name you will need the full type name.</span></span> <span data-ttu-id="82ba3-212">예를 들어 샘플에서 프로젝트 형식에 대 한 리소스 `ExtraNamespace.Tools` 이름이 *ExtraNamespace.Tools.fr.resx*합니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-212">For example, in the sample project a resource for the type `ExtraNamespace.Tools` would be named *ExtraNamespace.Tools.fr.resx*.</span></span>

<span data-ttu-id="82ba3-213">샘플 프로젝트에는 `ConfigureServices` 메서드 집합은 `ResourcesPath` 홈 컨트롤러의 프랑스어 리소스 파일에 대 한 프로젝트 상대 경로 하므로 "리소스" *Resources/Controllers.HomeController.fr.resx*합니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-213">In the sample project, the `ConfigureServices` method sets the `ResourcesPath` to "Resources", so the project relative path for the home controller's French resource file is *Resources/Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="82ba3-214">또는 리소스 파일을 구성 하려면 폴더를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-214">Alternatively, you can use folders to organize resource files.</span></span> <span data-ttu-id="82ba3-215">홈 컨트롤러에 대 한 경로 것 *Resources/Controllers/HomeController.fr.resx*합니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-215">For the home controller, the path would be *Resources/Controllers/HomeController.fr.resx*.</span></span> <span data-ttu-id="82ba3-216">사용 하지 않는 경우는 `ResourcesPath` 옵션을는 *.resx* 프로젝트 기본 디렉터리에 파일을 거쳐야 합니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-216">If you don't use the `ResourcesPath` option, the *.resx* file would go in the project base directory.</span></span> <span data-ttu-id="82ba3-217">리소스 파일에 대 한 `HomeController` 이름이 *Controllers.HomeController.fr.resx*합니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-217">The resource file for `HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="82ba3-218">리소스 파일을 구성 하는 방법에 따라 점 또는 경로 명명 규칙을 사용 하 여 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-218">The choice of using the dot or path naming convention depends on how you want to organize your resource files.</span></span>

| <span data-ttu-id="82ba3-219">리소스 이름</span><span class="sxs-lookup"><span data-stu-id="82ba3-219">Resource name</span></span> | <span data-ttu-id="82ba3-220">점 또는 경로 이름 지정</span><span class="sxs-lookup"><span data-stu-id="82ba3-220">Dot or path naming</span></span> |
| ------------   | ------------- |
| <span data-ttu-id="82ba3-221">Resources/Controllers.HomeController.fr.resx</span><span class="sxs-lookup"><span data-stu-id="82ba3-221">Resources/Controllers.HomeController.fr.resx</span></span> | <span data-ttu-id="82ba3-222">점</span><span class="sxs-lookup"><span data-stu-id="82ba3-222">Dot</span></span>  |
| <span data-ttu-id="82ba3-223">Resources/Controllers/HomeController.fr.resx</span><span class="sxs-lookup"><span data-stu-id="82ba3-223">Resources/Controllers/HomeController.fr.resx</span></span>  | <span data-ttu-id="82ba3-224">Path</span><span class="sxs-lookup"><span data-stu-id="82ba3-224">Path</span></span> |
|    |     |

<span data-ttu-id="82ba3-225">리소스 파일을 사용 하 여 `@inject IViewLocalizer` Razor 뷰는 유사한 패턴을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-225">Resource files using `@inject IViewLocalizer` in Razor views follow a similar pattern.</span></span> <span data-ttu-id="82ba3-226">점을 지정 하거나 경로 이름 지정을 사용 하 여 보기에 대 한 리소스 파일을 이름은 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-226">The resource file for a view can be named using either dot naming or path naming.</span></span> <span data-ttu-id="82ba3-227">Razor 뷰 리소스 파일의 관련된 보기 파일의 경로 모방 합니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-227">Razor view resource files mimic the path of their associated view file.</span></span> <span data-ttu-id="82ba3-228">설정 가정는 `ResourcesPath` "리소스"와 연결 된 프랑스어 리소스 파일의 *Views/Home/About.cshtml* 보기에서 다음 중 하나가 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-228">Assuming we set the `ResourcesPath` to "Resources", the French resource file associated with the *Views/Home/About.cshtml* view could be either of the following:</span></span>

* <span data-ttu-id="82ba3-229">Resources/Views/Home/About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="82ba3-229">Resources/Views/Home/About.fr.resx</span></span>

* <span data-ttu-id="82ba3-230">Resources/Views.Home.About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="82ba3-230">Resources/Views.Home.About.fr.resx</span></span>

<span data-ttu-id="82ba3-231">사용 하지 않는 경우는 `ResourcesPath` 옵션을는 *.resx* 보기는 보기와 같은 폴더에에 대 한 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-231">If you don't use the `ResourcesPath` option, the *.resx* file for a view would be located in the same folder as the view.</span></span>

<span data-ttu-id="82ba3-232">".Fr" 문화권 지정자를 제거 하 고 쿠키 또는 기타 메커니즘) (통해 프랑스어로 설정 하는 문화권, 기본 리소스 파일을 읽고 문자열이 지역화 됩니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-232">If you remove the ".fr" culture designator AND you have the culture set to French (via cookie or other mechanism), the default resource file is read and strings are localized.</span></span> <span data-ttu-id="82ba3-233">리소스 관리자는 culture 지정자 없이 *.resx 파일을 처리 하는 사용자의 요청 된 문화권 아무 것도 충족 하는 경우 기본 또는 대체 (fallback) 리소스를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-233">The Resource manager designates a default or fallback resource, when nothing meets your requested culture you're served the *.resx file without a culture designator.</span></span> <span data-ttu-id="82ba3-234">요청 된 문화권의 리소스를 없습니다 기본 리소스 파일을가 필요한 경우 키에만 반환 하려면.</span><span class="sxs-lookup"><span data-stu-id="82ba3-234">If you want to just return the key when missing a resource for the requested culture you must not have a default resource file.</span></span>

### <a name="generating-resource-files-with-visual-studio"></a><span data-ttu-id="82ba3-235">Visual Studio를 사용 하 여 리소스 파일을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-235">Generating resource files with Visual Studio</span></span>

<span data-ttu-id="82ba3-236">파일 이름에는 문화권 하지 않고 Visual Studio에서 리소스 파일을 만드는 경우 (예를 들어 *Welcome.resx*), Visual Studio는 각 문자열에 대 한 속성을 갖는 C# 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-236">If you create a resource file in Visual Studio without a culture in the file name (for example, *Welcome.resx*), Visual Studio will create a C# class with a property for each string.</span></span> <span data-ttu-id="82ba3-237">이 결과 원하지 ASP.NET 코어; 일반적으로 기본값이 없는 *.resx* 리소스 파일 (A *.resx* 문화권 이름 없이 파일).</span><span class="sxs-lookup"><span data-stu-id="82ba3-237">That's usually not what you want with ASP.NET Core; you typically don't have a default *.resx* resource file (A *.resx* file without the culture name).</span></span> <span data-ttu-id="82ba3-238">만들어야 하는 것이 좋습니다는 *.resx* 문화권 이름으로 파일 (예를 들어 *Welcome.fr.resx*).</span><span class="sxs-lookup"><span data-stu-id="82ba3-238">We suggest you create the *.resx* file with a culture name (for example *Welcome.fr.resx*).</span></span> <span data-ttu-id="82ba3-239">만들 때 한 *.resx* 문화권 이름을 사용할 경우 Visual Studio 파일 클래스 파일을 생성 하지 것입니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-239">When you create a *.resx* file with a culture name, Visual Studio will not generate the class file.</span></span> <span data-ttu-id="82ba3-240">예상 많은 개발자 됩니다 **하지** 기본 언어 리소스 파일을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-240">We anticipate that many developers will **not** create a default language resource file.</span></span>

### <a name="adding-other-cultures"></a><span data-ttu-id="82ba3-241">다른 문화권에 맞게 추가</span><span class="sxs-lookup"><span data-stu-id="82ba3-241">Adding Other Cultures</span></span>

<span data-ttu-id="82ba3-242">각 언어 및 culture 조합 (기본 언어)와 다른 고유한 리소스 파일이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-242">Each language and culture combination (other than the default language) requires a unique resource file.</span></span> <span data-ttu-id="82ba3-243">ISO 언어 코드를 파일 이름에 있는 새 리소스 파일을 만들어 서로 다른 문화권 및 로캘에 대 한 리소스 파일을 만듭니다 (예를 들어 **en-우리**, **fr ca**, 및  **en gb**).</span><span class="sxs-lookup"><span data-stu-id="82ba3-243">You create resource files for different cultures and locales by creating new resource files in which the ISO language codes are part of the file name (for example, **en-us**, **fr-ca**, and **en-gb**).</span></span> <span data-ttu-id="82ba3-244">파일 이름 사이 이러한 ISO 코드 위치 및 *.resx* 에서 같이 파일 이름 확장명을 *Welcome.es MX.resx* (스페인어/멕시코).</span><span class="sxs-lookup"><span data-stu-id="82ba3-244">These ISO codes are placed between the file name and the *.resx* file name extension, as in *Welcome.es-MX.resx* (Spanish/Mexico).</span></span> <span data-ttu-id="82ba3-245">중립 문화권에 따라 언어를 지정 하려면 국가 코드를 제거 (`MX` 앞의 예제에서).</span><span class="sxs-lookup"><span data-stu-id="82ba3-245">To specify a culturally neutral language, remove the country code (`MX` in the preceding example).</span></span> <span data-ttu-id="82ba3-246">스페인어 중립 문화권 리소스 파일 이름이 *Welcome.es.resx*합니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-246">The culturally neutral Spanish resource file name is *Welcome.es.resx*.</span></span>

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a><span data-ttu-id="82ba3-247">각 요청에 대 한 언어/문화권을 선택 하는 전략을 구현 합니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-247">Implement a strategy to select the language/culture for each request</span></span>  

### <a name="configuring-localization"></a><span data-ttu-id="82ba3-248">지역화를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-248">Configuring localization</span></span>

<span data-ttu-id="82ba3-249">지역화에 구성 된 `ConfigureServices` 메서드:</span><span class="sxs-lookup"><span data-stu-id="82ba3-249">Localization is configured in the `ConfigureServices` method:</span></span>

<span data-ttu-id="82ba3-250">[!code-csharp[Main](localization/sample/Startup.cs?range=45-49)]</span><span class="sxs-lookup"><span data-stu-id="82ba3-250">[!code-csharp[Main](localization/sample/Startup.cs?range=45-49)]</span></span>

* <span data-ttu-id="82ba3-251">`AddLocalization`서비스 컨테이너를 지역화 서비스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-251">`AddLocalization` Adds the localization services to the services container.</span></span> <span data-ttu-id="82ba3-252">위의 코드는 또한 "리소스"에 대 한 리소스 경로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-252">The code above also sets the resources path to "Resources".</span></span>

* <span data-ttu-id="82ba3-253">`AddViewLocalization`지역화 된 보기 파일에 대 한 지원을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-253">`AddViewLocalization` Adds support for localized view files.</span></span> <span data-ttu-id="82ba3-254">이 샘플 보기에서 지역화 보기 파일 접미사를 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-254">In this sample view localization is based on the view file suffix.</span></span> <span data-ttu-id="82ba3-255">예를 들어 "fr"에 *Index.fr.cshtml* 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-255">For example "fr" in the *Index.fr.cshtml* file.</span></span>

* <span data-ttu-id="82ba3-256">`AddDataAnnotationsLocalization`각 언어에 대 한 지원을 추가 `DataAnnotations` 유효성 검사를 통해 메시지 `IStringLocalizer` 추상화 합니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-256">`AddDataAnnotationsLocalization` Adds support for localized `DataAnnotations` validation messages through `IStringLocalizer` abstractions.</span></span>

### <a name="localization-middleware"></a><span data-ttu-id="82ba3-257">지역화 미들웨어</span><span class="sxs-lookup"><span data-stu-id="82ba3-257">Localization middleware</span></span>

<span data-ttu-id="82ba3-258">현재 요청에 설정 된 지역화에 [미들웨어](middleware.md)합니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-258">The current culture on a request is set in the localization [Middleware](middleware.md).</span></span> <span data-ttu-id="82ba3-259">지역화 미들웨어에서 사용 되는 `Configure` 방식의 *Startup.cs* 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-259">The localization middleware is enabled in the `Configure` method of *Startup.cs* file.</span></span> <span data-ttu-id="82ba3-260">확인 요청 문화권을 확인할 수 있는 모든 미들웨어 하기 전에 지역화 미들웨어를 구성 해야 합니다 (예를 들어 `app.UseMvc()`).</span><span class="sxs-lookup"><span data-stu-id="82ba3-260">Note,  the localization middleware must be configured before any middleware which might check the request culture (for example, `app.UseMvc()`).</span></span>

<span data-ttu-id="82ba3-261">[!code-csharp[Main](localization/sample/Startup.cs?highlight=13-35&range=123-159)]</span><span class="sxs-lookup"><span data-stu-id="82ba3-261">[!code-csharp[Main](localization/sample/Startup.cs?highlight=13-35&range=123-159)]</span></span>

<span data-ttu-id="82ba3-262">`UseRequestLocalization`초기화 한 `RequestLocalizationOptions` 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-262">`UseRequestLocalization` initializes a `RequestLocalizationOptions` object.</span></span> <span data-ttu-id="82ba3-263">모든 요청 목록에서의 `RequestCultureProvider` 에 `RequestLocalizationOptions` 열거 및 요청 culture를 성공적으로 결정할 수 있는 첫 번째 공급자가 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-263">On every request the list of `RequestCultureProvider` in the `RequestLocalizationOptions` is enumerated and the first provider that can successfully determine the request culture is used.</span></span> <span data-ttu-id="82ba3-264">기본 공급자에서 제공 된 `RequestLocalizationOptions` 클래스:</span><span class="sxs-lookup"><span data-stu-id="82ba3-264">The default providers come from the `RequestLocalizationOptions` class:</span></span>

1. `QueryStringRequestCultureProvider`
2. `CookieRequestCultureProvider`
3. `AcceptLanguageHeaderRequestCultureProvider`

<span data-ttu-id="82ba3-265">기본 목록 가장 구체적인 정보부터 가장 구체적으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-265">The default list goes from most specific to least specific.</span></span> <span data-ttu-id="82ba3-266">문서의 뒷부분에 순서를 변경도 사용자 지정 문화권 공급자를 추가 하는 방법을 살펴보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-266">Later in the article we'll see how you can change the order and even add a custom culture provider.</span></span> <span data-ttu-id="82ba3-267">어떤 공급자가 요청 문화권을 확인할 수 있으면는 `DefaultRequestCulture` 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-267">If none of the providers can determine the request culture, the `DefaultRequestCulture` is used.</span></span>

### <a name="querystringrequestcultureprovider"></a><span data-ttu-id="82ba3-268">QueryStringRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="82ba3-268">QueryStringRequestCultureProvider</span></span>

<span data-ttu-id="82ba3-269">일부 응용 프로그램 쿼리 문자열을 사용 하 여 설정 하는 [culture 및 UL culture](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx#Current)합니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-269">Some apps will use a query string to set the [culture and UI culture](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx#Current).</span></span> <span data-ttu-id="82ba3-270">쿠키 또는 Accept-language 헤더 접근 방식을 사용 하는 응용 프로그램의 경우 URL에 쿼리 문자열을 추가 코드를 디버깅 및 테스트에 유용 합니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-270">For apps that use the cookie or Accept-Language header approach, adding a query string to the URL is useful for debugging and testing code.</span></span> <span data-ttu-id="82ba3-271">기본적으로는 `QueryStringRequestCultureProvider` 에서 첫 번째 지역화 공급자로 등록는 `RequestCultureProvider` 목록입니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-271">By default, the `QueryStringRequestCultureProvider` is registered as the first localization provider in the `RequestCultureProvider` list.</span></span> <span data-ttu-id="82ba3-272">쿼리 문자열 매개 변수를 전달 `culture` 및 `ui-culture`합니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-272">You pass the query string parameters `culture` and `ui-culture`.</span></span> <span data-ttu-id="82ba3-273">다음 예제에서는 특정 문화권 (언어 및 지역) 스페인어/멕시코 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-273">The following example sets the specific culture (language and region) to Spanish/Mexico:</span></span>

   `http://localhost:5000/?culture=es-MX&ui-culture=es-MX`

<span data-ttu-id="82ba3-274">둘 중 하나에 전달 하는 경우 (`culture` 또는 `ui-culture`), 쿼리 문자열 공급자에 전달 된 것을 사용 하 여 두 값을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-274">If you only pass in one of the two (`culture` or `ui-culture`), the query string provider will set both values using the one you passed in.</span></span> <span data-ttu-id="82ba3-275">예를 들어 문화권만 설정 됩니다 모두 설정 된 `Culture` 및 `UICulture`:</span><span class="sxs-lookup"><span data-stu-id="82ba3-275">For example, setting just the culture will set both the `Culture` and the `UICulture`:</span></span>

   `http://localhost:5000/?culture=es-MX`

### <a name="cookierequestcultureprovider"></a><span data-ttu-id="82ba3-276">CookieRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="82ba3-276">CookieRequestCultureProvider</span></span>

<span data-ttu-id="82ba3-277">프로덕션 앱에서 ASP.NET Core 문화권 쿠키와 함께 culture를 설정 하는 메커니즘을 제공 경우가 많습니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-277">Production apps will often provide a mechanism to set the culture with the ASP.NET Core culture cookie.</span></span> <span data-ttu-id="82ba3-278">사용 하 여는 `MakeCookieValue` 메서드를 쿠키를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-278">Use the `MakeCookieValue`  method to create a cookie.</span></span>

<span data-ttu-id="82ba3-279">`CookieRequestCultureProvider` `DefaultCookieName` 문화권 정보를 사용자를 추적 하는 데 기본 쿠키 이름인의 기본 설정으로 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-279">The `CookieRequestCultureProvider` `DefaultCookieName`  returns the default cookie name used to track the user’s preferred culture information.</span></span> <span data-ttu-id="82ba3-280">기본 쿠키 이름인 "입니다. AspNetCore.Culture "입니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-280">The default cookie  name is ".AspNetCore.Culture".</span></span>

<span data-ttu-id="82ba3-281">쿠키 형식은 `c=%LANGCODE%|uic=%LANGCODE%`여기서 `c` 은 `Culture` 및 `uic` 은 `UICulture`, 예:</span><span class="sxs-lookup"><span data-stu-id="82ba3-281">The cookie format is `c=%LANGCODE%|uic=%LANGCODE%`, where `c` is `Culture` and `uic` is `UICulture`, for example:</span></span>

    c=en-UK|uic=en-US

<span data-ttu-id="82ba3-282">문화권 정보 및 UI 문화권 중 하나만 지정 하면 지정된 된 문화권 한 문화권 정보 및 UI 문화권을 모두 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-282">If you only specify one of culture info and UI culture, the specified culture will be used for both culture info and UI culture.</span></span>

### <a name="the-accept-language-http-header"></a><span data-ttu-id="82ba3-283">Accept-language HTTP 헤더</span><span class="sxs-lookup"><span data-stu-id="82ba3-283">The Accept-Language HTTP header</span></span>

<span data-ttu-id="82ba3-284">[Accept-language 헤더](https://www.w3.org/International/questions/qa-accept-lang-locales) 대부분의 브라우저에서 설정할 수는 않았고 원래 사용자의 언어를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-284">The [Accept-Language header](https://www.w3.org/International/questions/qa-accept-lang-locales) is settable in most browsers and was originally intended to specify the user's language.</span></span> <span data-ttu-id="82ba3-285">이 설정은 브라우저 전송 하도록 설정 된 또는 기본 운영 체제에서 상속한 것을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-285">This setting indicates what the browser has been set to send or has inherited from the underlying operating system.</span></span> <span data-ttu-id="82ba3-286">Accept-language HTTP 헤더의 브라우저 요청 사용자의 기본 언어를 검색 하는 infallible 있는 것은 아닙니다 (참조 [브라우저의 언어 기본 설정](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).</span><span class="sxs-lookup"><span data-stu-id="82ba3-286">The Accept-Language HTTP header from a browser request is not an infallible way to detect the user's preferred language (see [Setting language preferences in a browser](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).</span></span> <span data-ttu-id="82ba3-287">프로덕션 응용 프로그램 사용자는 선택한 culture 지정할 수 있는 방법을 포함 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-287">A production app should include a way for a user to customize their choice of culture.</span></span>

### <a name="setting-the-accept-language-http-header-in-ie"></a><span data-ttu-id="82ba3-288">IE에서 Accept-language HTTP 헤더 설정</span><span class="sxs-lookup"><span data-stu-id="82ba3-288">Setting the Accept-Language HTTP header in IE</span></span>

1. <span data-ttu-id="82ba3-289">기어 아이콘에서을 누릅니다 **인터넷 옵션**합니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-289">From the gear icon, tap **Internet Options**.</span></span>

2. <span data-ttu-id="82ba3-290">탭 **언어**합니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-290">Tap **Languages**.</span></span>

    ![인터넷 옵션](localization/_static/lang.png)

3. <span data-ttu-id="82ba3-292">탭 **언어 기본 설정 설정**합니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-292">Tap **Set Language Preferences**.</span></span>

4. <span data-ttu-id="82ba3-293">탭 **언어 추가**합니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-293">Tap **Add a language**.</span></span>

5. <span data-ttu-id="82ba3-294">언어를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-294">Add the language.</span></span>

6. <span data-ttu-id="82ba3-295">언어를 차례로 누릅니다 **위로 이동**합니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-295">Tap the language, then tap **Move Up**.</span></span>

### <a name="using-a-custom-provider"></a><span data-ttu-id="82ba3-296">사용자 지정 공급자를 사용 하 여</span><span class="sxs-lookup"><span data-stu-id="82ba3-296">Using a custom provider</span></span>

<span data-ttu-id="82ba3-297">데이터베이스에 해당 언어 및 culture를 저장 하 여 소비자에 게 알리는 한다고 가정 합니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-297">Suppose you want to let your customers store their language and culture in your databases.</span></span> <span data-ttu-id="82ba3-298">사용자에 대 한 이러한 값을 조회 하는 공급자를 작성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-298">You could write a provider to look up these values for the user.</span></span> <span data-ttu-id="82ba3-299">다음 코드에는 사용자 지정 공급자를 추가 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-299">The following code shows how to add a custom provider:</span></span>

```csharp
services.Configure<RequestLocalizationOptions>(options =>
   {
       var supportedCultures = new[]
       {
           new CultureInfo("en-US"),
           new CultureInfo("fr")
       };

       options.DefaultRequestCulture = new RequestCulture(culture: "en-US", uiCulture: "en-US");
       options.SupportedCultures = supportedCultures;
       options.SupportedUICultures = supportedCultures;

       options.RequestCultureProviders.Insert(0, new CustomRequestCultureProvider(async context =>
       {
         // My custom request culture logic
         return new ProviderCultureResult("en");
       }));
   });
   ```

<span data-ttu-id="82ba3-300">사용 하 여 `RequestLocalizationOptions` 지역화 공급자를 추가 하거나 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-300">Use `RequestLocalizationOptions` to add or remove localization providers.</span></span>

### <a name="setting-the-culture-programmatically"></a><span data-ttu-id="82ba3-301">프로그래밍 방식으로 문화권 설정</span><span class="sxs-lookup"><span data-stu-id="82ba3-301">Setting the culture programmatically</span></span>

<span data-ttu-id="82ba3-302">이 샘플 **Localization.StarterWeb** 프로젝션 할 [GitHub](https://github.com/aspnet/entropy) 설정 하는 UI가 포함 된 `Culture`합니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-302">This sample **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) contains UI to set the `Culture`.</span></span> <span data-ttu-id="82ba3-303">*Views/Shared/_SelectLanguagePartial.cshtml* 파일을 사용 하면 지원 되는 culture의 목록에서 culture를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-303">The *Views/Shared/_SelectLanguagePartial.cshtml* file allows you to select the culture from the list of supported cultures:</span></span>

<span data-ttu-id="82ba3-304">[!code-HTML[Main](localization/sample/Views/Shared/_SelectLanguagePartial.cshtml)]</span><span class="sxs-lookup"><span data-stu-id="82ba3-304">[!code-HTML[Main](localization/sample/Views/Shared/_SelectLanguagePartial.cshtml)]</span></span>

<span data-ttu-id="82ba3-305">*Views/Shared/_SelectLanguagePartial.cshtml* 파일이에 추가 되는 `footer` 모든 보기에 사용할 수 있도록 레이아웃 파일의 섹션:</span><span class="sxs-lookup"><span data-stu-id="82ba3-305">The *Views/Shared/_SelectLanguagePartial.cshtml* file is added to the `footer` section of the layout file so it will be available to all views:</span></span>

<span data-ttu-id="82ba3-306">[!code-HTML[Main](localization/sample/Views/Shared/_Layout.cshtml?range=48-61&highlight=10)]</span><span class="sxs-lookup"><span data-stu-id="82ba3-306">[!code-HTML[Main](localization/sample/Views/Shared/_Layout.cshtml?range=48-61&highlight=10)]</span></span>

<span data-ttu-id="82ba3-307">`SetLanguage` 메서드 문화권 쿠키를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-307">The `SetLanguage` method sets the culture cookie.</span></span>

<span data-ttu-id="82ba3-308">[!code-csharp[Main](localization/sample/Controllers/HomeController.cs?range=57-67)]</span><span class="sxs-lookup"><span data-stu-id="82ba3-308">[!code-csharp[Main](localization/sample/Controllers/HomeController.cs?range=57-67)]</span></span>

<span data-ttu-id="82ba3-309">연결할 수 없습니다는 *_SelectLanguagePartial.cshtml* 이 프로젝트에 대 한 샘플 코드에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-309">You can't plug in the *_SelectLanguagePartial.cshtml* to sample code for this project.</span></span> <span data-ttu-id="82ba3-310">**Localization.StarterWeb** 프로젝션 할 [GitHub](https://github.com/aspnet/entropy) 흐름 하는 코드가 `RequestLocalizationOptions` 는 Razor 통해 부분에 [종속성 주입](dependency-injection.md) 컨테이너입니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-310">The **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) has code to flow the `RequestLocalizationOptions` to a Razor partial through the [Dependency Injection](dependency-injection.md) container.</span></span>

## <a name="globalization-and-localization-terms"></a><span data-ttu-id="82ba3-311">전역화 및 지역화 용어</span><span class="sxs-lookup"><span data-stu-id="82ba3-311">Globalization and localization terms</span></span>

<span data-ttu-id="82ba3-312">응용 프로그램을 지역화 하는 프로세스는 또한 일반적으로 최신 소프트웨어 개발에 사용 되는 관련 문자 집합에 대 한 기본적인 이해 및 관련 된 문제에 대 한 이해가 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-312">The process of localizing your app also requires a basic understanding of relevant character sets commonly used in modern software development and an understanding of the issues associated with them.</span></span> <span data-ttu-id="82ba3-313">숫자 (코드)로 텍스트를 저장 하는 모든 컴퓨터, 있지만 다른 시스템 다른 번호를 사용 하 여 동일한 텍스트를 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-313">Although all computers store text as numbers (codes), different systems store the same text using different numbers.</span></span> <span data-ttu-id="82ba3-314">지역화 과정 번역 된 특정 문화권/로캘에 대 한 응용 프로그램 사용자 인터페이스 (UI)를 참조 합니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-314">The localization process refers to translating the app user interface (UI) for a specific culture/locale.</span></span>

<span data-ttu-id="82ba3-315">[지역화 가능성](https://msdn.microsoft.com/library/aa292135(v=vs.71).aspx) 는 세계화 된 응용 프로그램 지역화 하는 데 사용할 준비가 되어 있는지 확인 하기 위한 중간 프로세스입니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-315">[Localizability](https://msdn.microsoft.com/library/aa292135(v=vs.71).aspx) is an intermediate process for verifying that a globalized app is ready for localization.</span></span>

<span data-ttu-id="82ba3-316">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) 문화권 이름에 대 한 서식을 "<languagecode2>-< country/regioncode2 >", 여기서 <languagecode2> 언어 코드 이며 < country/regioncode2 > 하위 문화권 코드입니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-316">The [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) format for the culture name is "<languagecode2>-<country/regioncode2>", where <languagecode2> is the language code and <country/regioncode2> is the subculture code.</span></span> <span data-ttu-id="82ba3-317">예를 들어 `es-CL` 스페인어 (칠레)에 대 한 `en-US` 영어 (미국) 및 `en-AU` 영어 (오스트레일리아)에 대 한 합니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-317">For example, `es-CL` for Spanish (Chile), `en-US` for English (United States), and `en-AU` for English (Australia).</span></span> <span data-ttu-id="82ba3-318">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) 는 언어와 관련 된 두 문자의 소문자 문화권 코드는 ISO 639 및는 ISO 3166 국가 또는 지역와 관련 된 두 문자의 대문자 하위 문화권 코드의 조합입니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-318">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) is a combination of an ISO 639 two-letter lowercase culture code associated with a language and an ISO 3166 two-letter uppercase subculture code associated with a country or region.</span></span>  <span data-ttu-id="82ba3-319">참조 [언어 문화권 이름](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx)합니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-319">See [Language Culture Name](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).</span></span>

<span data-ttu-id="82ba3-320">국제화 "I18N" 종종 약어로 됩니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-320">Internationalization is often abbreviated to "I18N".</span></span> <span data-ttu-id="82ba3-321">약어는 첫 번째 및 마지막 문자를 사용 하 고 있으므로 18 둘 간의 문자의 수 첫째 문자의 수에 대 한 "I"와 "N" 마지막 합니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-321">The abbreviation takes the first and last letters and the number of letters between them, so 18 stands for the number of letters between the first "I" and the last "N".</span></span> <span data-ttu-id="82ba3-322">(G11N) 전역화 및 지역화 (L10N)에 마찬가지입니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-322">The same applies to Globalization (G11N), and Localization (L10N).</span></span>

<span data-ttu-id="82ba3-323">조건:</span><span class="sxs-lookup"><span data-stu-id="82ba3-323">Terms:</span></span>

* <span data-ttu-id="82ba3-324">세계화 (G11N): 다른 언어와 지역 지원 앱을 만드는 프로세스입니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-324">Globalization (G11N): The process of making an app support different languages and regions.</span></span>
* <span data-ttu-id="82ba3-325">지역화 (L10N): 지정 된 언어 및 지역에 대 한 앱을 사용자 지정 프로세스입니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-325">Localization (L10N): The process of customizing an app for a given language and region.</span></span>
* <span data-ttu-id="82ba3-326">국제화 (I18N): 전역화 및 지역화를 모두 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-326">Internationalization (I18N): Describes both globalization and localization.</span></span>
* <span data-ttu-id="82ba3-327">언어 및 필요에 따라 영역 문화권: 않습니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-327">Culture: It is a language and, optionally, a region.</span></span>
* <span data-ttu-id="82ba3-328">중립 문화권: 지정한 언어에 있지만 지역에 있지는 문화권입니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-328">Neutral culture: A culture that has a specified language, but not a region.</span></span> <span data-ttu-id="82ba3-329">(예를 들어 "en", "es")</span><span class="sxs-lookup"><span data-stu-id="82ba3-329">(for example "en", "es")</span></span>
* <span data-ttu-id="82ba3-330">특정 문화권:는 지정한 언어 및 지역 하는 문화권입니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-330">Specific culture: A culture that has a specified language and region.</span></span> <span data-ttu-id="82ba3-331">(예를 들어 "EN-US", "EN-GB", "es CL")</span><span class="sxs-lookup"><span data-stu-id="82ba3-331">(for example "en-US", "en-GB", "es-CL")</span></span>
* <span data-ttu-id="82ba3-332">로캘: 로캘의 문화권으로 동일 합니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-332">Locale: A locale is the same as a culture.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="82ba3-333">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="82ba3-333">Additional Resources</span></span>

* <span data-ttu-id="82ba3-334">[Localization.StarterWeb 프로젝트](https://github.com/aspnet/entropy) 문서에 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="82ba3-334">[Localization.StarterWeb project](https://github.com/aspnet/entropy) used in the article.</span></span>
* <span data-ttu-id="82ba3-335">[Visual Studio의 리소스 파일](https://msdn.microsoft.com/library/xbx3z216(v=vs.110).aspx#VSResFiles)</span><span class="sxs-lookup"><span data-stu-id="82ba3-335">[Resource Files in Visual Studio](https://msdn.microsoft.com/library/xbx3z216(v=vs.110).aspx#VSResFiles)</span></span>
* <span data-ttu-id="82ba3-336">[.Resx 파일의 리소스](https://msdn.microsoft.com/library/xbx3z216(v=vs.110).aspx#ResourcesFiles)</span><span class="sxs-lookup"><span data-stu-id="82ba3-336">[Resources in .resx Files](https://msdn.microsoft.com/library/xbx3z216(v=vs.110).aspx#ResourcesFiles)</span></span>
