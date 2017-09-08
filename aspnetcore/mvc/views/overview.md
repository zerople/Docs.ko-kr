---
title: "뷰 개요"
author: ardalis
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 668c320d-c050-45e3-8161-2f460dc93b2f
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/views/overview
ms.openlocfilehash: a93ee8165be52e33c2e7da4d3fee2c8225864db9
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/11/2017
---
# <a name="rendering-html-with-views-in-aspnet-core-mvc"></a><span data-ttu-id="3e541-103">ASP.NET Core MVC에서 뷰가 포함 된 HTML 렌더링</span><span class="sxs-lookup"><span data-stu-id="3e541-103">Rendering HTML with views in ASP.NET Core MVC</span></span>

<span data-ttu-id="3e541-104">으로 [Steve Smith](http://ardalis.com)</span><span class="sxs-lookup"><span data-stu-id="3e541-104">By [Steve Smith](http://ardalis.com)</span></span>

<span data-ttu-id="3e541-105">ASP.NET Core MVC 컨트롤러 사용 하 여 서식이 지정 된 결과 반환할 수 *뷰*합니다.</span><span class="sxs-lookup"><span data-stu-id="3e541-105">ASP.NET Core MVC controllers can return formatted results using *views*.</span></span>

## <a name="what-are-views"></a><span data-ttu-id="3e541-106">뷰는 무엇입니까?</span><span class="sxs-lookup"><span data-stu-id="3e541-106">What are Views?</span></span>

<span data-ttu-id="3e541-107">모델-뷰-컨트롤러 (MVC) 패턴에는 *보기* 앱과 사용자의 상호 작용의 프레젠테이션 세부 정보를 캡슐화 합니다.</span><span class="sxs-lookup"><span data-stu-id="3e541-107">In the Model-View-Controller (MVC) pattern, the *view* encapsulates the presentation details of the user's interaction with the app.</span></span> <span data-ttu-id="3e541-108">뷰는 클라이언트로 전송할 콘텐츠를 생성 하는 포함 된 코드를 사용 하 여 HTML 템플릿을입니다.</span><span class="sxs-lookup"><span data-stu-id="3e541-108">Views are HTML templates with embedded code that generate content to send to the client.</span></span> <span data-ttu-id="3e541-109">사용 하 여 뷰 [Razor 구문을](razor.md), 코드가 최소한의 코드 또는 공식 절차와 HTML 상호 작용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3e541-109">Views use [Razor syntax](razor.md), which allows code to interact with HTML with minimal code or ceremony.</span></span>

<span data-ttu-id="3e541-110">ASP.NET Core MVC 뷰는 *.cshtml* 에서 기본적으로 저장 된 파일을 *뷰* 응용 프로그램 내에서 폴더입니다.</span><span class="sxs-lookup"><span data-stu-id="3e541-110">ASP.NET Core MVC views are *.cshtml* files stored by default in a *Views* folder within the application.</span></span> <span data-ttu-id="3e541-111">일반적으로 각 컨트롤러에는 특정 컨트롤러 작업에 대 한 보기 된 고유한 폴더에 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="3e541-111">Typically, each controller will have its own folder, in which are views for specific controller actions.</span></span>

![솔루션 탐색기에서 views 폴더](overview/_static/views_solution_explorer.png)

<span data-ttu-id="3e541-113">특정 작업 보기 외에도 [부분 뷰](partial.md), [레이아웃 및 기타 특수 보기 파일](layout.md) 는 반복을 절감 하 고 응용 프로그램의 보기 내에서 다시 사용할 수 있도록 허용 하는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3e541-113">In addition to action-specific views, [partial views](partial.md), [layouts, and other special view files](layout.md) can be used to help reduce repetition and allow for reuse within the app's views.</span></span>

## <a name="benefits-of-using-views"></a><span data-ttu-id="3e541-114">뷰를 사용할 때의 이점</span><span class="sxs-lookup"><span data-stu-id="3e541-114">Benefits of Using Views</span></span>

<span data-ttu-id="3e541-115">뷰를 제공 [문제의 분리](http://deviq.com/separation-of-concerns/) 별도로 비즈니스 논리에서 사용자 인터페이스 수준 태그를 캡슐화 하는 MVC 응용 프로그램 내에서.</span><span class="sxs-lookup"><span data-stu-id="3e541-115">Views provide [separation of concerns](http://deviq.com/separation-of-concerns/) within an MVC app, encapsulating user interface level markup separately from business logic.</span></span> <span data-ttu-id="3e541-116">ASP.NET MVC 뷰 사용 [Razor 구문을](razor.md) HTML 태그 및 서버 쪽 논리 단계를 전환할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3e541-116">ASP.NET MVC views use [Razor syntax](razor.md) to make switching between HTML markup and server side logic painless.</span></span> <span data-ttu-id="3e541-117">일반적으로 반복적인 측면의 응용 프로그램의 사용자 인터페이스를 사용 하 여 뷰 간에 쉽게 다시 사용할 수 [레이아웃 및 공유 지시문](layout.md) 또는 [부분 뷰](partial.md)합니다.</span><span class="sxs-lookup"><span data-stu-id="3e541-117">Common, repetitive aspects of the app's user interface can easily be reused between views using [layout and shared directives](layout.md) or [partial views](partial.md).</span></span>

## <a name="creating-a-view"></a><span data-ttu-id="3e541-118">보기를 만드는 방법</span><span class="sxs-lookup"><span data-stu-id="3e541-118">Creating a View</span></span>

<span data-ttu-id="3e541-119">에 컨트롤러에만 적용 되는 뷰가 생성 됩니다는 *뷰 / [ControllerName]* 폴더입니다.</span><span class="sxs-lookup"><span data-stu-id="3e541-119">Views that are specific to a controller are created in the *Views/[ControllerName]* folder.</span></span> <span data-ttu-id="3e541-120">컨트롤러 간에 공유 되는 뷰에 배치 되는 */뷰/공유* 폴더입니다.</span><span class="sxs-lookup"><span data-stu-id="3e541-120">Views that are shared among controllers are placed in the */Views/Shared* folder.</span></span> <span data-ttu-id="3e541-121">연결 된 컨트롤러 작업을 동일 파일 보기 이름을 지정 하 고 추가 된 *.cshtml* 파일 확장명입니다.</span><span class="sxs-lookup"><span data-stu-id="3e541-121">Name the view file the same as its associated controller action, and add the *.cshtml* file extension.</span></span> <span data-ttu-id="3e541-122">에 대 한 보기를 만드는 예는 *에 대 한* 작업에는 *홈* 컨트롤러를 만들면 됩니다는 *About.cshtml* 파일에   */뷰/홈*폴더입니다.</span><span class="sxs-lookup"><span data-stu-id="3e541-122">For example, to create a view for the *About* action on the *Home* controller, you would create the *About.cshtml* file in the */Views/Home* folder.</span></span>

<span data-ttu-id="3e541-123">샘플 보기 파일 (*About.cshtml*):</span><span class="sxs-lookup"><span data-stu-id="3e541-123">A sample view file (*About.cshtml*):</span></span>

<span data-ttu-id="3e541-124">[!code-html[Main](../../common/samples/WebApplication1/Views/Home/About.cshtml)]</span><span class="sxs-lookup"><span data-stu-id="3e541-124">[!code-html[Main](../../common/samples/WebApplication1/Views/Home/About.cshtml)]</span></span>

<span data-ttu-id="3e541-125">*Razor* 코드도 표시 되는 `@` 기호입니다.</span><span class="sxs-lookup"><span data-stu-id="3e541-125">*Razor* code is denoted by the `@` symbol.</span></span> <span data-ttu-id="3e541-126">C# 문에 Razor가 중괄호 코드 블록을 off로 설정 내에서 실행 됩니다 (`{` `}`), "정보" 할당과 같은 하는 `ViewData["Title"]` 위에 표시 된 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="3e541-126">C# statements are run within Razor code blocks set off by curly braces (`{` `}`), such as the assignment of "About" to the `ViewData["Title"]` element shown above.</span></span> <span data-ttu-id="3e541-127">Razor를 단순히 값과 참조 하 여 HTML 내에서 값을 표시 하려면 사용할 수는 `@` 기호, 내에서 표시 된 것 처럼는 `<h2>` 및 `<h3>` 위의 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="3e541-127">Razor can be used to display values within HTML by simply referencing the value with the `@` symbol, as shown within the `<h2>` and `<h3>` elements above.</span></span>

<span data-ttu-id="3e541-128">이 뷰를 담당 하 고 출력의 부분에만 집중 합니다.</span><span class="sxs-lookup"><span data-stu-id="3e541-128">This view focuses on just the portion of the output for which it is responsible.</span></span> <span data-ttu-id="3e541-129">페이지의 레이아웃의 나머지와 다른 공용 보기의 요소를 다른 곳에서 지정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="3e541-129">The rest of the page's layout, and other common aspects of the view, are specified elsewhere.</span></span> <span data-ttu-id="3e541-130">에 대 한 자세한 내용은 [레이아웃 및 공유 뷰 논리](layout.md)합니다.</span><span class="sxs-lookup"><span data-stu-id="3e541-130">Learn more about [layout and shared view logic](layout.md).</span></span>

## <a name="how-do-controllers-specify-views"></a><span data-ttu-id="3e541-131">보기를 지정 하는 컨트롤러 어떻게 해야 하나요?</span><span class="sxs-lookup"><span data-stu-id="3e541-131">How do Controllers Specify Views?</span></span>

<span data-ttu-id="3e541-132">뷰는으로 작업에서 일반적으로 반환 되는 `ViewResult`합니다.</span><span class="sxs-lookup"><span data-stu-id="3e541-132">Views are typically returned from actions as a `ViewResult`.</span></span> <span data-ttu-id="3e541-133">동작 메서드에서 만들고 반환할 수는 `ViewResult` 를 직접 하지만 일반적으로 컨트롤러에서 상속 하는 경우 `Controller`, 단순히 사용할는 `View` 도우미 메서드를이 예제로 보여 줍니다:</span><span class="sxs-lookup"><span data-stu-id="3e541-133">Your action method can create and return a `ViewResult` directly, but more commonly if your controller inherits from `Controller`, you'll simply use the `View` helper method, as this example demonstrates:</span></span>

<span data-ttu-id="3e541-134">*HomeController.cs*</span><span class="sxs-lookup"><span data-stu-id="3e541-134">*HomeController.cs*</span></span>

<span data-ttu-id="3e541-135">[!code-csharp[Main](../../common/samples/WebApplication1/Controllers/HomeController.cs?highlight=5&range=16-21)]</span><span class="sxs-lookup"><span data-stu-id="3e541-135">[!code-csharp[Main](../../common/samples/WebApplication1/Controllers/HomeController.cs?highlight=5&range=16-21)]</span></span>

<span data-ttu-id="3e541-136">`View` 도우미 메서드에 응용 프로그램 개발자에 대 한 반환 보기 쉽게 수행할 수 있도록 몇 가지 오버 로드 합니다.</span><span class="sxs-lookup"><span data-stu-id="3e541-136">The `View` helper method has several overloads to make returning views easier for app developers.</span></span> <span data-ttu-id="3e541-137">뷰에 전달할를 모델 개체와 함께 뷰를 반환 하려면 선택적으로 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3e541-137">You can optionally specify a view to return, as well as a model object to pass to the view.</span></span>

<span data-ttu-id="3e541-138">이 작업을 반환 하는 경우는 *About.cshtml* 위에 표시 된 뷰를 렌더링할:</span><span class="sxs-lookup"><span data-stu-id="3e541-138">When this action returns, the *About.cshtml* view shown above is rendered:</span></span>

![페이지 정보](overview/_static/about-page.png)

### <a name="view-discovery"></a><span data-ttu-id="3e541-140">검색 보기</span><span class="sxs-lookup"><span data-stu-id="3e541-140">View Discovery</span></span>

<span data-ttu-id="3e541-141">작업 뷰를 반환 하는 경우 프로세스를 호출할 *보기 검색* 이루어집니다.</span><span class="sxs-lookup"><span data-stu-id="3e541-141">When an action returns a view, a process called *view discovery* takes place.</span></span> <span data-ttu-id="3e541-142">이 프로세스는 뷰 파일을 사용할 것을 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="3e541-142">This process determines which view file will be used.</span></span> <span data-ttu-id="3e541-143">런타임은 컨트롤러 관련 보기 먼저 찾습니다 일치 하는 뷰 이름에 대 한 특정 보기 파일을 지정 하지 않으면는 *Shared* 폴더입니다.</span><span class="sxs-lookup"><span data-stu-id="3e541-143">Unless a specific view file is specified, the runtime looks for a controller-specific view first, then looks for matching view name in the *Shared* folder.</span></span>

<span data-ttu-id="3e541-144">작업 반환 하는 경우는 `View` 메서드를 다음과 같이 `return View();`, 뷰 이름으로 작업 이름이 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="3e541-144">When an action returns the `View` method, like so `return View();`, the action name is used as the view name.</span></span> <span data-ttu-id="3e541-145">예를 들어이 값이 "Index" 라는 작업 메서드에서 호출 된 경우 "Index"의 뷰 이름 전달 같을 수 합니다.</span><span class="sxs-lookup"><span data-stu-id="3e541-145">For example, if this were called from an action method named "Index", it would be equivalent to passing in a view name of "Index".</span></span> <span data-ttu-id="3e541-146">뷰 이름을 명시적으로 메서드에 전달 될 수 있습니다 (`return View("SomeView");`).</span><span class="sxs-lookup"><span data-stu-id="3e541-146">A view name can be explicitly passed to the method (`return View("SomeView");`).</span></span> <span data-ttu-id="3e541-147">이러한 경우 모두 보기 검색에서 일치 하는 파일의 보기를 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="3e541-147">In both of these cases, view discovery searches for a matching view file in:</span></span>

   1. <span data-ttu-id="3e541-148">뷰 /<ControllerName>/<ViewName>.cshtml</span><span class="sxs-lookup"><span data-stu-id="3e541-148">Views/<ControllerName>/<ViewName>.cshtml</span></span>

   2. <span data-ttu-id="3e541-149">뷰/공유/<ViewName>.cshtml</span><span class="sxs-lookup"><span data-stu-id="3e541-149">Views/Shared/<ViewName>.cshtml</span></span>

>[!TIP]
> <span data-ttu-id="3e541-150">단순히 반환 규칙 다음과 좋습니다 `View()` 수행 되므로 더 유연 하 고 코드를 리팩터링하면 쉽게 가능한 경우 작업에서 합니다.</span><span class="sxs-lookup"><span data-stu-id="3e541-150">We recommend following the convention of simply returning `View()` from actions when possible, as it results in more flexible, easier to refactor code.</span></span>

<span data-ttu-id="3e541-151">뷰 이름 대신 뷰 파일 경로 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3e541-151">A view file path can be provided, instead of a view name.</span></span> <span data-ttu-id="3e541-152">이 경우에 *.cshtml* 확장 파일 경로의 일부로 지정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="3e541-152">In this case, the *.cshtml* extension must be specified as part of the file path.</span></span> <span data-ttu-id="3e541-153">응용 프로그램 루트에 상대적인 경로 여야 합니다 (필요에 따라 시작할 수 있습니다 및 "/" 또는 "~ /").</span><span class="sxs-lookup"><span data-stu-id="3e541-153">The path should be relative to the application root (and can optionally start with "/" or "~/").</span></span> <span data-ttu-id="3e541-154">예를 들면 `return View("Views/Home/About.cshtml");` 같은 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="3e541-154">For example: `return View("Views/Home/About.cshtml");`</span></span>

> [!NOTE]
> <span data-ttu-id="3e541-155">[부분 뷰](partial.md) 및 [구성 요소 확인](view-components.md) (비슷하지만 동일 하지 않은) 검색 메커니즘을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="3e541-155">[Partial views](partial.md) and [view components](view-components.md) use similar (but not identical) discovery mechanisms.</span></span>

> [!NOTE]
> <span data-ttu-id="3e541-156">사용자 지정을 사용 하 여 뷰 응용 프로그램 내에에 대 한 기본 규칙을 사용자 지정할 수 있습니다 `IViewLocationExpander`합니다.</span><span class="sxs-lookup"><span data-stu-id="3e541-156">You can customize the default convention regarding where views are located within the app by using a custom `IViewLocationExpander`.</span></span>

>[!TIP]
> <span data-ttu-id="3e541-157">뷰 이름은 대/소문자 구분 내부 파일 시스템에 따라 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3e541-157">View names may be case sensitive depending on the underlying file system.</span></span> <span data-ttu-id="3e541-158">운영 체제에서 호환성을 위해 컨트롤러 및 작업 이름 및 관련된 보기 폴더와 파일 이름을 간의 대/소문자를 항상 일치 합니다.</span><span class="sxs-lookup"><span data-stu-id="3e541-158">For compatibility across operating systems, always match case between controller and action names and associated view folders and filenames.</span></span>

## <a name="passing-data-to-views"></a><span data-ttu-id="3e541-159">데이터 뷰를 전달</span><span class="sxs-lookup"><span data-stu-id="3e541-159">Passing Data to Views</span></span>

<span data-ttu-id="3e541-160">여러 가지 메커니즘을 사용 하 여 보기에 데이터를 전달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3e541-160">You can pass data to views using several mechanisms.</span></span> <span data-ttu-id="3e541-161">지정 하는 가장 강력한 방법입니다는 *모델* 보기에서 유형 (라고 하는 *viewmodel*, 비즈니스 도메인 모델 형식 구별 하기 위해), 이러한 형식의 인스턴스 보기에 전달 된 후 동작입니다.</span><span class="sxs-lookup"><span data-stu-id="3e541-161">The most robust approach is to specify a *model* type in the view (commonly referred to as a *viewmodel*, to distinguish it from business domain model types), and then pass an instance of this type to the view from the action.</span></span> <span data-ttu-id="3e541-162">데이터 보기를 전달 하는 모델 또는 모델 보기를 사용 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="3e541-162">We recommend you use a model or view model to pass data to a view.</span></span> <span data-ttu-id="3e541-163">따라서 강력한 형식 검사를 활용 하기 위해 보기 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3e541-163">This allows the view to take advantage of strong type checking.</span></span> <span data-ttu-id="3e541-164">사용 하 여 보기에 대 한 모델을 지정할 수는 `@model` 지시문:</span><span class="sxs-lookup"><span data-stu-id="3e541-164">You can specify a model for a view using the `@model` directive:</span></span>

<!-- literal_block {"ids": [], "linenos": false, "xml:space": "preserve", "language": "html", "highlight_args": {"hl_lines": [1]}} -->

```html
@model WebApplication1.ViewModels.Address
   <h2>Contact</h2>
   <address>
       @Model.Street<br />
       @Model.City, @Model.State @Model.PostalCode<br />
       <abbr title="Phone">P:</abbr>
       425.555.0100
   </address>
   ```

<span data-ttu-id="3e541-165">모델 보기에 대해 지정 된 뷰에 전송 인스턴스가 사용 하 여 강력한 형식의 방식으로 액세스할 수 있습니다 `@Model` 위와 같이 합니다.</span><span class="sxs-lookup"><span data-stu-id="3e541-165">Once a model has been specified for a view, the instance sent to the view can be accessed in a strongly-typed manner using `@Model` as shown above.</span></span> <span data-ttu-id="3e541-166">뷰에 모델 형식의 인스턴스를 제공 하려면 컨트롤러 매개 변수로 전달 합니다.</span><span class="sxs-lookup"><span data-stu-id="3e541-166">To provide an instance of the model type to the view, the controller passes it as a parameter:</span></span>

<!-- literal_block {"ids": [], "linenos": false, "xml:space": "preserve", "language": "csharp", "highlight_args": {"hl_lines": [13]}} -->

```csharp
public IActionResult Contact()
   {
       ViewData["Message"] = "Your contact page.";

       var viewModel = new Address()
       {
           Name = "Microsoft",
           Street = "One Microsoft Way",
           City = "Redmond",
           State = "WA",
           PostalCode = "98052-6399"
       };
       return View(viewModel);
   }
   ```

<span data-ttu-id="3e541-167">모델로 보기에 제공 될 수 있는 형식에 제한은 없습니다.</span><span class="sxs-lookup"><span data-stu-id="3e541-167">There are no restrictions on the types that can be provided to a view as a model.</span></span> <span data-ttu-id="3e541-168">응용 프로그램에 비즈니스 논리를 다른 곳에서 캡슐화 될 수 있도록 거의 또는 전혀 동작을 사용 하 여 CLR 개체 POCO (Plain Old) 보기 모델을 전달 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="3e541-168">We recommend passing Plain Old CLR Object (POCO) view models with little or no behavior, so that business logic can be encapsulated elsewhere in the app.</span></span> <span data-ttu-id="3e541-169">이 방법의 예로 *주소* viewmodel 위의 예에서 사용:</span><span class="sxs-lookup"><span data-stu-id="3e541-169">An example of this approach is the *Address* viewmodel used in the example above:</span></span>

<!-- literal_block {"ids": [], "linenos": false, "xml:space": "preserve", "language": "csharp", "highlight_args": {"hl_lines": [13]}} -->

```csharp
namespace WebApplication1.ViewModels
   {
       public class Address
       {
           public string Name { get; set; }
           public string Street { get; set; }
           public string City { get; set; }
           public string State { get; set; }
           public string PostalCode { get; set; }
       }
   }
   ```

> [!NOTE]
> <span data-ttu-id="3e541-170">아무 것도 동일한 클래스를 사용 하 여 비즈니스 모델 유형 및 디스플레이 모델 유형에 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="3e541-170">Nothing prevents you from using the same classes as your business model types and your display model types.</span></span> <span data-ttu-id="3e541-171">그러나 이러한 및 별도로 유지 하거나 도메인 또는 지 속성이 모델에서 독립적으로 변경 하 여 뷰를 사용 하면 몇 가지 보안 이점을 제공할 수 있습니다 (사용자가 사용 하 여 응용 프로그램에 보내는 모델에 대 한 [모델 바인딩](../models/model-binding.md)).</span><span class="sxs-lookup"><span data-stu-id="3e541-171">However, keeping them separate allows your views to vary independently from your domain or persistence model, and can offer some security benefits as well (for models that users will send to the app using [model binding](../models/model-binding.md)).</span></span>

### <a name="loosely-typed-data"></a><span data-ttu-id="3e541-172">잘못 입력 한 데이터</span><span class="sxs-lookup"><span data-stu-id="3e541-172">Loosely Typed Data</span></span>

<span data-ttu-id="3e541-173">강력한 형식의 뷰 외에도 모든 보기를 사용 하면 데이터의 느슨한 형식의 컬렉션에 권한이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3e541-173">In addition to strongly typed views, all views have access to a loosely typed collection of data.</span></span> <span data-ttu-id="3e541-174">이 컬렉션을 통해 참조 될 수 있습니다는 `ViewData` 또는 `ViewBag` 컨트롤러와 뷰는 속성입니다.</span><span class="sxs-lookup"><span data-stu-id="3e541-174">This same collection can be referenced through either the `ViewData` or `ViewBag` properties on controllers and views.</span></span> <span data-ttu-id="3e541-175">`ViewBag` 속성은 한 래퍼 `ViewData` 해당 컬렉션에 대 한 동적 뷰를 제공 하는입니다.</span><span class="sxs-lookup"><span data-stu-id="3e541-175">The `ViewBag` property is a wrapper around `ViewData` that provides a dynamic view over that collection.</span></span> <span data-ttu-id="3e541-176">별도 컬렉션 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="3e541-176">It is not a separate collection.</span></span>

<span data-ttu-id="3e541-177">`ViewData`통해 액세스 하는 사전 개체 `string` 키입니다.</span><span class="sxs-lookup"><span data-stu-id="3e541-177">`ViewData` is a dictionary object accessed through `string` keys.</span></span> <span data-ttu-id="3e541-178">저장 하 고, 개체를 검색할 수 있습니다 및 추출 하는 경우 특정 형식으로 캐스팅 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="3e541-178">You can store and retrieve objects in it, and you'll need to cast them to a specific type when you extract them.</span></span> <span data-ttu-id="3e541-179">사용할 수 있습니다 `ViewData` 뷰 (및 부분 뷰 및 레이아웃) 내에서 뿐 아니라, 보기에는 컨트롤러에서 데이터를 전달 하 합니다.</span><span class="sxs-lookup"><span data-stu-id="3e541-179">You can use `ViewData` to pass data from a controller to views, as well as within views (and partial views and layouts).</span></span> <span data-ttu-id="3e541-180">문자열 데이터를 저장 하 고 캐스트에 대 한 필요 없이 직접 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3e541-180">String data can be stored and used directly, without the need for a cast.</span></span>

<span data-ttu-id="3e541-181">에 대 한 일부 값을 설정 `ViewData` 동작에서:</span><span class="sxs-lookup"><span data-stu-id="3e541-181">Set some values for `ViewData` in an action:</span></span>

```csharp
public IActionResult SomeAction()
   {
       ViewData["Greeting"] = "Hello";
       ViewData["Address"]  = new Address()
       {
           Name = "Steve",
           Street = "123 Main St",
           City = "Hudson",
           State = "OH",
           PostalCode = "44236"
       };

       return View();
   }
   ```

<span data-ttu-id="3e541-182">보기에서 데이터와 함께 작동 합니다.</span><span class="sxs-lookup"><span data-stu-id="3e541-182">Work with the data in a view:</span></span>

<!-- literal_block {"ids": [], "linenos": false, "xml:space": "preserve", "language": "html", "highlight_args": {"hl_lines": [3, 6]}} -->

```html
@{
       // Requires cast
       var address = ViewData["Address"] as Address;
   }

   @ViewData["Greeting"] World!

   <address>
       @address.Name<br />
       @address.Street<br />
       @address.City, @address.State @address.PostalCode
   </address>
   ```

<span data-ttu-id="3e541-183">`ViewBag` 개체에 저장 된 개체에 대 한 동적 액세스를 제공 `ViewData`합니다.</span><span class="sxs-lookup"><span data-stu-id="3e541-183">The `ViewBag` objects provides dynamic access to the objects stored in `ViewData`.</span></span> <span data-ttu-id="3e541-184">이 더 편리 캐스팅이 필요 하지 않으므로 작업할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3e541-184">This can be more convenient to work with, since it doesn't require casting.</span></span> <span data-ttu-id="3e541-185">사용 하 여 위와 동일한 예제에서는 `ViewBag` 대신 강력한 형식의 `address` 보기에서 인스턴스:</span><span class="sxs-lookup"><span data-stu-id="3e541-185">The same example as above, using `ViewBag` instead of a strongly typed `address` instance in the view:</span></span>

<!-- literal_block {"ids": [], "linenos": false, "xml:space": "preserve", "language": "html", "highlight_args": {"hl_lines": [1, 4, 5, 6]}} -->

```html
@ViewBag.Greeting World!

   <address>
       @ViewBag.Address.Name<br />
       @ViewBag.Address.Street<br />
       @ViewBag.Address.City, @ViewBag.Address.State @ViewBag.Address.PostalCode
   </address>
   ```

> [!NOTE]
> <span data-ttu-id="3e541-186">둘 다 동일한 기본를 참조 하므로 `ViewData` 컬렉션을 혼합 하는 사이 일치 `ViewData` 및 `ViewBag` 읽고 편리 하 게 하는 경우 값을 쓸 때.</span><span class="sxs-lookup"><span data-stu-id="3e541-186">Since both refer to the same underlying `ViewData` collection, you can mix and match between `ViewData` and `ViewBag` when reading and writing values, if convenient.</span></span>

### <a name="dynamic-views"></a><span data-ttu-id="3e541-187">동적 뷰</span><span class="sxs-lookup"><span data-stu-id="3e541-187">Dynamic Views</span></span>

<span data-ttu-id="3e541-188">뷰는 형식에 전달 된 모델 인스턴스를 설치 했지만 모델 형식을 선언 하지 마십시오.이 인스턴스를 동적으로 참조할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3e541-188">Views that do not declare a model type but have a model instance passed to them can reference this instance dynamically.</span></span> <span data-ttu-id="3e541-189">예를 들어 인스턴스의 `Address` 를 선언 하지 않습니다 하는 보기에 전달 되는 `@model`, 보기는 여전히 표시 된 것 처럼 동적으로 인스턴스 속성을 참조할 수:</span><span class="sxs-lookup"><span data-stu-id="3e541-189">For example, if an instance of `Address` is passed to a view that doesn't declare an `@model`, the view would still be able to refer to the instance's properties dynamically as shown:</span></span>

<!-- literal_block {"ids": [], "linenos": false, "xml:space": "preserve", "language": "html", "highlight_args": {"hl_lines": [13, 16, 17, 18]}} -->

```html
<address>
       @Model.Street<br />
       @Model.City, @Model.State @Model.PostalCode<br />
       <abbr title="Phone">P:</abbr>
       425.555.0100
   </address>
   ```

<span data-ttu-id="3e541-190">이 기능은 일부 유연성을 제공할 수 있지만 모든 컴파일 보호 또는 IntelliSense를 제공 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3e541-190">This feature can offer some flexibility, but does not offer any compilation protection or IntelliSense.</span></span> <span data-ttu-id="3e541-191">속성이 존재 하지 않으면 페이지는 런타임 시 실패 합니다.</span><span class="sxs-lookup"><span data-stu-id="3e541-191">If the property doesn't exist, the page will fail at runtime.</span></span>

## <a name="more-view-features"></a><span data-ttu-id="3e541-192">더 많은 보기 기능</span><span class="sxs-lookup"><span data-stu-id="3e541-192">More View Features</span></span>

<span data-ttu-id="3e541-193">[태그 도우미](tag-helpers/intro.md) 쉽게 서버 쪽 동작을 사용자 지정 코드 또는 도우미 보기 내에서 사용 하 여 필요 없이 기존 HTML 태그를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3e541-193">[Tag helpers](tag-helpers/intro.md) make it easy to add server-side behavior to existing HTML tags, avoiding the need to use custom code or helpers within views.</span></span> <span data-ttu-id="3e541-194">태그 도우미 특성으로 뷰 태그를 편집 하 고 다양 한 도구에서에서 렌더링할 수 있도록,에 익숙하지 않은 편집기에서 무시 되는 HTML 요소에 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="3e541-194">Tag helpers are applied as attributes to HTML elements, which are ignored by editors that aren't familiar with them, allowing view markup to be edited and rendered in a variety of tools.</span></span> <span data-ttu-id="3e541-195">태그 도우미는 여러 가지 그리고 허용 되는 특히 [양식 작업](working-with-forms.md) 훨씬 쉽습니다.</span><span class="sxs-lookup"><span data-stu-id="3e541-195">Tag helpers have many uses, and in particular can make [working with forms](working-with-forms.md) much easier.</span></span>

<span data-ttu-id="3e541-196">사용자 지정 HTML 태그를 생성 얻을 수 있는 여러 기본 제공 HTML 도우미 및 (잠재적으로 자체 데이터 요구 사항 있음)의 더 복잡 한 UI 논리에 캡슐화 할 수 [구성 요소 보기](view-components.md)합니다.</span><span class="sxs-lookup"><span data-stu-id="3e541-196">Generating custom HTML markup can be achieved with many built-in HTML Helpers, and more complex UI logic (potentially with its own data requirements) can be encapsulated in [View Components](view-components.md).</span></span> <span data-ttu-id="3e541-197">뷰 구성 요소는 컨트롤러와 뷰 제공 하는 문제의 동일한 분리를 제공 하 고 일반적인 UI 요소에서 사용 하는 데이터를 처리 하는 동작 및 뷰에 대 한 필요성 제거할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3e541-197">View components provide the same separation of concerns that controllers and views offer, and can eliminate the need for actions and views to deal with data used by common UI elements.</span></span>

<span data-ttu-id="3e541-198">ASP.NET Core의 다른 많은 요소와 마찬가지로 뷰 지원 [종속성 주입](../../fundamentals/dependency-injection.md), 서비스 수를 허용 [뷰에 주입 된](dependency-injection.md)합니다.</span><span class="sxs-lookup"><span data-stu-id="3e541-198">Like many other aspects of ASP.NET Core, views support [dependency injection](../../fundamentals/dependency-injection.md), allowing services to be [injected into views](dependency-injection.md).</span></span>
