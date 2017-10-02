---
title: "구성 요소 보기"
author: rick-anderson
description: "뷰 구성 요소는 다시 사용할 수 있는 렌더링 논리는 아무 곳 이나 사용 됩니다."
keywords: "ASP.NET Core, 구성 요소 보기, 부분 뷰"
ms.author: riande
manager: wpickett
ms.date: 02/14/2017
ms.topic: article
ms.assetid: ab4705b7-59d7-4f31-bc97-ea7f292fe926
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/views/view-components
ms.openlocfilehash: 3bc6d3f85d8ea7fb9b72b18cfd9c5ec2d07293b0
ms.sourcegitcommit: 732cd2684246e49e796836596643a8d37e20c46d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2017
---
# <a name="view-components"></a><span data-ttu-id="ed739-104">구성 요소 보기</span><span class="sxs-lookup"><span data-stu-id="ed739-104">View components</span></span>

<span data-ttu-id="ed739-105">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="ed739-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="ed739-106">[보거나 다운로드 샘플 코드](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/views/view-components/sample) ([다운로드 하는 방법을](xref:tutorials/index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ed739-106">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/views/view-components/sample) ([how to download](xref:tutorials/index#how-to-download-a-sample))</span></span>

## <a name="introducing-view-components"></a><span data-ttu-id="ed739-107">뷰 구성 요소 소개</span><span class="sxs-lookup"><span data-stu-id="ed739-107">Introducing view components</span></span>

<span data-ttu-id="ed739-108">새 ASP.NET Core mvc 뷰 구성 요소는 부분 뷰로 유사 하지만 훨씬 더 강력 합니다.</span><span class="sxs-lookup"><span data-stu-id="ed739-108">New to ASP.NET Core MVC, view components are similar to partial views, but they are much more powerful.</span></span> <span data-ttu-id="ed739-109">보기 구성 요소 하지 않는 모델 바인딩을 사용 하 고만를 호출할 때 제공 된 데이터에 따라 달라 집니다.</span><span class="sxs-lookup"><span data-stu-id="ed739-109">View components don’t use model binding, and only depend on the data you provide when calling into it.</span></span> <span data-ttu-id="ed739-110">뷰 구성 요소:</span><span class="sxs-lookup"><span data-stu-id="ed739-110">A view component:</span></span>

* <span data-ttu-id="ed739-111">전체 응답 하지 않고 청크를 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="ed739-111">Renders a chunk rather than a whole response</span></span>
* <span data-ttu-id="ed739-112">컨트롤러와 뷰 사이 있는 테스트 용이성 이점과 분리-문제의 동일한 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ed739-112">Includes the same separation-of-concerns and testability benefits found between a controller and view</span></span>
* <span data-ttu-id="ed739-113">매개 변수 및 비즈니스 논리가 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ed739-113">Can have parameters and business logic</span></span>
* <span data-ttu-id="ed739-114">일반적으로 페이지 레이아웃에서에서 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ed739-114">Is typically invoked from a layout page</span></span>

<span data-ttu-id="ed739-115">와 같은 너무 복잡해 서 부분 뷰를 다시 사용할 수 있는 렌더링 논리는 아무 곳 이나 구성 요소 보기 사항은:</span><span class="sxs-lookup"><span data-stu-id="ed739-115">View components are intended anywhere you have reusable rendering logic that is too complex for a partial view, such as:</span></span>

* <span data-ttu-id="ed739-116">동적 탐색 메뉴</span><span class="sxs-lookup"><span data-stu-id="ed739-116">Dynamic navigation menus</span></span>
* <span data-ttu-id="ed739-117">태그 클라우드 (여기서는 데이터베이스를 쿼리)</span><span class="sxs-lookup"><span data-stu-id="ed739-117">Tag cloud (where it queries the database)</span></span>
* <span data-ttu-id="ed739-118">로그인 패널</span><span class="sxs-lookup"><span data-stu-id="ed739-118">Login panel</span></span>
* <span data-ttu-id="ed739-119">쇼핑 카트</span><span class="sxs-lookup"><span data-stu-id="ed739-119">Shopping cart</span></span>
* <span data-ttu-id="ed739-120">최근에 게시 된 문서</span><span class="sxs-lookup"><span data-stu-id="ed739-120">Recently published articles</span></span>
* <span data-ttu-id="ed739-121">일반적인 블로그에서 사이드바 콘텐츠</span><span class="sxs-lookup"><span data-stu-id="ed739-121">Sidebar content on a typical blog</span></span>
* <span data-ttu-id="ed739-122">모든 페이지에 렌더링 됩니다 하 고 로그 아웃 하거나 사용자의 상태에 있는 로그에 따라 로그인에 대 한 링크를 보여 주는 로그인 패널</span><span class="sxs-lookup"><span data-stu-id="ed739-122">A login panel that would be rendered on every page and show either the links to log out or log in, depending on the log in state of the user</span></span>

<span data-ttu-id="ed739-123">뷰 구성 요소는 두 부분으로 구성 됩니다: 클래스 (대개에서 파생 [ViewComponent](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.viewcomponent)) 하 고 결과 (일반적으로 보기)을 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="ed739-123">A view component consists of two parts: the class (typically derived from [ViewComponent](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.viewcomponent)) and the result it returns (typically a view).</span></span> <span data-ttu-id="ed739-124">컨트롤러와 같은 뷰 구성 요소는 POCO 수는 있지만 대부분의 개발자는에서 파생 된 메서드 및 사용할 수 있는 속성의 활용 하려고 `ViewComponent`합니다.</span><span class="sxs-lookup"><span data-stu-id="ed739-124">Like controllers, a view component can be a POCO, but most developers will want to take advantage of the methods and properties available by deriving from `ViewComponent`.</span></span>

## <a name="creating-a-view-component"></a><span data-ttu-id="ed739-125">뷰 구성 요소 만들기</span><span class="sxs-lookup"><span data-stu-id="ed739-125">Creating a view component</span></span>

<span data-ttu-id="ed739-126">이 섹션에서는 뷰 구성 요소를 만들려면 중요 한 요구 합니다.</span><span class="sxs-lookup"><span data-stu-id="ed739-126">This section contains the high-level requirements to create a view component.</span></span> <span data-ttu-id="ed739-127">이 문서의 뒷부분에 나오는 각 단계를 자세히 검사 알아보고 뷰 구성 요소를 만드는 하겠습니다.</span><span class="sxs-lookup"><span data-stu-id="ed739-127">Later in the article, we'll examine each step in detail and create a view component.</span></span>

### <a name="the-view-component-class"></a><span data-ttu-id="ed739-128">뷰 구성 요소 클래스</span><span class="sxs-lookup"><span data-stu-id="ed739-128">The view component class</span></span>

<span data-ttu-id="ed739-129">뷰 구성 요소 클래스에서 다음 중 하나를 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ed739-129">A view component class can be created by any of the following:</span></span>

* <span data-ttu-id="ed739-130">파생 된 *ViewComponent*</span><span class="sxs-lookup"><span data-stu-id="ed739-130">Deriving from *ViewComponent*</span></span>
* <span data-ttu-id="ed739-131">사용 하는 클래스를 데코레이팅하는 `[ViewComponent]` 특성 또는 포함 하는 클래스에서 파생 되는 `[ViewComponent]` 특성</span><span class="sxs-lookup"><span data-stu-id="ed739-131">Decorating a class with the `[ViewComponent]` attribute, or deriving from a class with the `[ViewComponent]` attribute</span></span>
* <span data-ttu-id="ed739-132">클래스는 이름이 접미사로 끝나는 만들기 *ViewComponent*</span><span class="sxs-lookup"><span data-stu-id="ed739-132">Creating a class where the name ends with the suffix *ViewComponent*</span></span>

<span data-ttu-id="ed739-133">컨트롤러와 같은 구성 요소 보기 공용, 비중첩 및 비추상 클래스 여야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ed739-133">Like controllers, view components must be public, non-nested, and non-abstract classes.</span></span> <span data-ttu-id="ed739-134">뷰 구성 요소 이름은 제거 "ViewComponent" 접미사와 함께 클래스 이름이입니다.</span><span class="sxs-lookup"><span data-stu-id="ed739-134">The view component name is the class name with the "ViewComponent" suffix removed.</span></span> <span data-ttu-id="ed739-135">또한 명시적으로 지정할 수를 사용 하는 `ViewComponentAttribute.Name` 속성입니다.</span><span class="sxs-lookup"><span data-stu-id="ed739-135">It can also be explicitly specified using the `ViewComponentAttribute.Name` property.</span></span>

<span data-ttu-id="ed739-136">뷰 구성 요소 클래스:</span><span class="sxs-lookup"><span data-stu-id="ed739-136">A view component class:</span></span>

* <span data-ttu-id="ed739-137">생성자를 완벽 하 게 지원 [종속성 주입](../../fundamentals/dependency-injection.md)</span><span class="sxs-lookup"><span data-stu-id="ed739-137">Fully supports constructor [dependency injection](../../fundamentals/dependency-injection.md)</span></span>

* <span data-ttu-id="ed739-138">즉, 사용할 수 없는 컨트롤러 수명 주기에서 파트를 사용 하지 않는 [필터](../controllers/filters.md) 뷰 구성 요소에</span><span class="sxs-lookup"><span data-stu-id="ed739-138">Does not take part in the controller lifecycle, which means you can't use [filters](../controllers/filters.md) in a view component</span></span>

### <a name="view-component-methods"></a><span data-ttu-id="ed739-139">구성 요소 메서드를 보기</span><span class="sxs-lookup"><span data-stu-id="ed739-139">View component methods</span></span>

<span data-ttu-id="ed739-140">에 해당 논리를 정의 하는 뷰 구성 요소는 `InvokeAsync` 반환 하는 메서드는 `IViewComponentResult`합니다.</span><span class="sxs-lookup"><span data-stu-id="ed739-140">A view component defines its logic in an `InvokeAsync` method that returns an `IViewComponentResult`.</span></span> <span data-ttu-id="ed739-141">매개 변수를 바인딩하는 모델에서가 아니라 뷰 구성 요소 호출에서 직접 가져오는 합니다.</span><span class="sxs-lookup"><span data-stu-id="ed739-141">Parameters come directly from invocation of the view component, not from model binding.</span></span> <span data-ttu-id="ed739-142">뷰 구성 요소 하지 직접 요청을 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="ed739-142">A view component never directly handles a request.</span></span> <span data-ttu-id="ed739-143">뷰 구성 요소는 모델을 초기화 하 고 호출 하 여 보기에 전달 하는 일반적으로 `View` 메서드.</span><span class="sxs-lookup"><span data-stu-id="ed739-143">Typically, a view component initializes a model and passes it to a view by calling the `View` method.</span></span> <span data-ttu-id="ed739-144">요약 하자면, 구성 요소 메서드를 보려면:</span><span class="sxs-lookup"><span data-stu-id="ed739-144">In summary, view component methods:</span></span>

* <span data-ttu-id="ed739-145">정의 `InvokeAsync` 반환 하는 메서드는`IViewComponentResult`</span><span class="sxs-lookup"><span data-stu-id="ed739-145">Define an `InvokeAsync` method that returns an `IViewComponentResult`</span></span>
* <span data-ttu-id="ed739-146">일반적으로 모델을 초기화 하 고 호출 하 여 보기에 전달 된 `ViewComponent` `View` 메서드</span><span class="sxs-lookup"><span data-stu-id="ed739-146">Typically initializes a model and passes it to a view by calling the `ViewComponent` `View` method</span></span>
* <span data-ttu-id="ed739-147">매개 변수에서 호출 하는 메서드의 HTTP가 아닌 야 하며, 모델 바인딩되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ed739-147">Parameters come from the calling method, not HTTP, there is no model binding</span></span>
* <span data-ttu-id="ed739-148">됩니다 (일반적으로 보기)에서 사용자 코드에서 호출 HTTP 끝점으로 직접 연결할 수 없음, 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ed739-148">Are not reachable directly as an HTTP endpoint, they are invoked from your code (usually in a view).</span></span> <span data-ttu-id="ed739-149">뷰 구성 요소는 요청을 처리 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ed739-149">A view component never handles a request</span></span>
* <span data-ttu-id="ed739-150">현재 HTTP 요청에서 세부 정보 보다는 서명을에 오버 로드</span><span class="sxs-lookup"><span data-stu-id="ed739-150">Are overloaded on the signature rather than any details from the current HTTP request</span></span>

### <a name="view-search-path"></a><span data-ttu-id="ed739-151">보기 검색 경로</span><span class="sxs-lookup"><span data-stu-id="ed739-151">View search path</span></span>

<span data-ttu-id="ed739-152">런타임에서 다음 경로에서 보기에 대 한 검색:</span><span class="sxs-lookup"><span data-stu-id="ed739-152">The runtime searches for the view in the following paths:</span></span>

   * <span data-ttu-id="ed739-153">뷰 /\<controller_name > /Components/\<view_component_name > /\<뷰 _ 이름 ></span><span class="sxs-lookup"><span data-stu-id="ed739-153">Views/\<controller_name>/Components/\<view_component_name>/\<view_name></span></span>
   * <span data-ttu-id="ed739-154">뷰/공유/구성 요소/\<view_component_name > /\<뷰 _ 이름 ></span><span class="sxs-lookup"><span data-stu-id="ed739-154">Views/Shared/Components/\<view_component_name>/\<view_name></span></span>

<span data-ttu-id="ed739-155">뷰 구성 요소에 대 한 기본 뷰 이름은 *기본*, 뷰 파일을 의미 하는 일반적으로 이름이 지정 됩니다 *Default.cshtml*합니다.</span><span class="sxs-lookup"><span data-stu-id="ed739-155">The default view name for a view component is *Default*, which means your view file will typically be named *Default.cshtml*.</span></span> <span data-ttu-id="ed739-156">뷰 구성 요소 결과 만들 때 또는 호출 하는 경우 서로 다른 뷰 이름을 지정할 수 있습니다는 `View` 메서드.</span><span class="sxs-lookup"><span data-stu-id="ed739-156">You can specify a different view name when creating the view component result or when calling the `View` method.</span></span>

<span data-ttu-id="ed739-157">보기 파일 이름을 좋습니다 *Default.cshtml* 사용 하는 *뷰/공유/구성 요소/\<view_component_name > /\<view_name >* 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="ed739-157">We recommend you name the view file *Default.cshtml* and use the *Views/Shared/Components/\<view_component_name>/\<view_name>* path.</span></span> <span data-ttu-id="ed739-158">`PriorityList` 이 샘플에 사용 되는 뷰 구성 요소를 사용 하 여 *Views/Shared/Components/PriorityList/Default.cshtml* 뷰 구성 요소에 대 한 합니다.</span><span class="sxs-lookup"><span data-stu-id="ed739-158">The `PriorityList` view component used in this sample uses *Views/Shared/Components/PriorityList/Default.cshtml* for the view component view.</span></span>

## <a name="invoking-a-view-component"></a><span data-ttu-id="ed739-159">뷰 구성 요소 호출</span><span class="sxs-lookup"><span data-stu-id="ed739-159">Invoking a view component</span></span>

<span data-ttu-id="ed739-160">뷰 구성 요소를 사용 하려면 다음을 호출 뷰 내:</span><span class="sxs-lookup"><span data-stu-id="ed739-160">To use the view component, call the following inside a view:</span></span>

```html
@Component.InvokeAsync("Name of view component", <anonymous type containing parameters>)
```

<span data-ttu-id="ed739-161">에 전달 될 매개 변수는 `InvokeAsync` 메서드.</span><span class="sxs-lookup"><span data-stu-id="ed739-161">The parameters will be passed to the `InvokeAsync` method.</span></span> <span data-ttu-id="ed739-162">`PriorityList` 문서에서 개발한 뷰 구성 요소에서 호출 되는 *Views/Todo/Index.cshtml* 파일 보기.</span><span class="sxs-lookup"><span data-stu-id="ed739-162">The `PriorityList` view component developed in the article is invoked from the *Views/Todo/Index.cshtml* view file.</span></span> <span data-ttu-id="ed739-163">다음 예제에서 `InvokeAsync` 메서드는 두 개의 매개 변수를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="ed739-163">In the following, the `InvokeAsync` method is called with two parameters:</span></span>

[!code-html[Main](view-components/sample/ViewCompFinal/Views/Todo/IndexFinal.cshtml?range=35)]

## <a name="invoking-a-view-component-as-a-tag-helper"></a><span data-ttu-id="ed739-164">태그 도우미로 뷰 구성 요소를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="ed739-164">Invoking a view component as a Tag Helper</span></span>

<span data-ttu-id="ed739-165">ASP.NET Core 1.1 이상에서는 뷰 구성 요소도 호출할 수 있습니다는 [태그 도우미](xref:mvc/views/tag-helpers/intro):</span><span class="sxs-lookup"><span data-stu-id="ed739-165">For ASP.NET Core 1.1 and higher, you can invoke a view component as a [Tag Helper](xref:mvc/views/tag-helpers/intro):</span></span>

[!code-html[Main](view-components/sample/ViewCompFinal/Views/Todo/IndexTagHelper.cshtml?range=37-38)]

<span data-ttu-id="ed739-166">파스칼식 대/소문자 클래스 및 메서드 매개 변수 태그 도우미에 대 한 변환 자신의 [kebab 대/소문자를 줄이려면](https://stackoverflow.com/questions/11273282/whats-the-name-for-dash-separated-case/12273101)합니다.</span><span class="sxs-lookup"><span data-stu-id="ed739-166">Pascal-cased class and method parameters for Tag Helpers are translated into their [lower kebab case](https://stackoverflow.com/questions/11273282/whats-the-name-for-dash-separated-case/12273101).</span></span> <span data-ttu-id="ed739-167">뷰 구성 요소를 호출 하는 태그 도우미를 사용 하 여는 `<vc></vc>` 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="ed739-167">The Tag Helper to invoke a view component uses the `<vc></vc>` element.</span></span> <span data-ttu-id="ed739-168">뷰 구성 요소는 다음과 같이 지정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ed739-168">The view component is specified as follows:</span></span>

```html
<vc:[view-component-name]
  parameter1="parameter1 value"
  parameter2="parameter2 value">
</vc:[view-component-name]>
```

<span data-ttu-id="ed739-169">참고: 태그 도우미로 뷰 구성 요소를 사용 하려면 등록 해야 사용 하 여 뷰 구성 요소를 포함 하는 어셈블리는 `@addTagHelper` 지시문입니다.</span><span class="sxs-lookup"><span data-stu-id="ed739-169">Note: In order to use a View Component as a Tag Helper, you must register the assembly containing the View Component using the `@addTagHelper` directive.</span></span> <span data-ttu-id="ed739-170">뷰 구성 요소 "MyWebApp" 라는 어셈블리에 있으면, 예를 들어에 다음 지시문을 추가 `_ViewImports.cshtml` 파일:</span><span class="sxs-lookup"><span data-stu-id="ed739-170">For example, if your View Component is in an assembly called "MyWebApp", add the following directive to the `_ViewImports.cshtml` file:</span></span>

```csharp
@addTagHelper *, MyWebApp
```

<span data-ttu-id="ed739-171">뷰 구성 요소 뷰 구성 요소를 참조 하는 모든 파일에 태그 도우미로 등록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ed739-171">You can register a View Component as a Tag Helper to any file that references the View Component.</span></span> <span data-ttu-id="ed739-172">참조 [태그 도우미 범위 관리](xref:mvc/views/tag-helpers/intro#managing-tag-helper-scope) 태그 도우미를 등록 하는 방법에 대 한 자세한 내용은 합니다.</span><span class="sxs-lookup"><span data-stu-id="ed739-172">See [Managing Tag Helper Scope](xref:mvc/views/tag-helpers/intro#managing-tag-helper-scope) for more information on how to register Tag Helpers.</span></span>

<span data-ttu-id="ed739-173">`InvokeAsync` 이 자습서에 사용 된 방법:</span><span class="sxs-lookup"><span data-stu-id="ed739-173">The `InvokeAsync` method used in this tutorial:</span></span>

[!code-html[Main](view-components/sample/ViewCompFinal/Views/Todo/IndexFinal.cshtml?range=35)]

<span data-ttu-id="ed739-174">태그 도우미 태그:</span><span class="sxs-lookup"><span data-stu-id="ed739-174">In Tag Helper markup:</span></span>

[!code-html[Main](view-components/sample/ViewCompFinal/Views/Todo/IndexTagHelper.cshtml?range=37-38)]

<span data-ttu-id="ed739-175">위의 샘플에서는 `PriorityList` 뷰 구성 요소는 `priority-list`합니다.</span><span class="sxs-lookup"><span data-stu-id="ed739-175">In the sample above, the `PriorityList` view component becomes `priority-list`.</span></span> <span data-ttu-id="ed739-176">뷰 구성 요소에는 매개 변수는 kebab 소문자로에 특성으로 전달 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ed739-176">The parameters to the view component are passed as attributes in lower kebab case.</span></span>

### <a name="invoking-a-view-component-directly-from-a-controller"></a><span data-ttu-id="ed739-177">컨트롤러에서 직접 뷰 구성 요소 호출</span><span class="sxs-lookup"><span data-stu-id="ed739-177">Invoking a view component directly from a controller</span></span>

<span data-ttu-id="ed739-178">뷰 구성 요소는 일반적으로 보기에서 호출 하지만 컨트롤러 메서드에에서 직접 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ed739-178">View components are typically invoked from a view, but you can invoke them directly from a controller method.</span></span> <span data-ttu-id="ed739-179">콘텐츠를 반환 하는 컨트롤러 동작 구성 요소 보기 컨트롤러와 같은 끝점을 정의 하지 않는 동안 쉽게 구현할 수는 `ViewComponentResult`합니다.</span><span class="sxs-lookup"><span data-stu-id="ed739-179">While view components do not define endpoints like controllers, you can easily implement a controller action that returns the content of a `ViewComponentResult`.</span></span>

<span data-ttu-id="ed739-180">이 예제에서는 뷰 구성 요소는 컨트롤러에서 직접 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ed739-180">In this example, the view component is called directly from the controller:</span></span>

[!code-csharp[Main](view-components/sample/ViewCompFinal/Controllers/ToDoController.cs?name=snippet_IndexVC)]

## <a name="walkthrough-creating-a-simple-view-component"></a><span data-ttu-id="ed739-181">연습: 간단한 뷰 구성 요소 만들기</span><span class="sxs-lookup"><span data-stu-id="ed739-181">Walkthrough: Creating a simple view component</span></span>

<span data-ttu-id="ed739-182">[다운로드](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/views/view-components/sample), 빌드 및 시작 코드를 테스트 합니다.</span><span class="sxs-lookup"><span data-stu-id="ed739-182">[Download](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/views/view-components/sample), build and test the starter code.</span></span> <span data-ttu-id="ed739-183">이 간단한 프로젝트와는 `Todo` 의 목록을 표시 하는 컨트롤러 *Todo* 항목입니다.</span><span class="sxs-lookup"><span data-stu-id="ed739-183">It's a simple project with a `Todo` controller that displays a list of *Todo* items.</span></span>

![ToDos의 목록](view-components/_static/2dos.png)

### <a name="add-a-viewcomponent-class"></a><span data-ttu-id="ed739-185">ViewComponent 클래스 추가</span><span class="sxs-lookup"><span data-stu-id="ed739-185">Add a ViewComponent class</span></span>

<span data-ttu-id="ed739-186">만들기는 *ViewComponents* 폴더 다음 추가 `PriorityListViewComponent` 클래스:</span><span class="sxs-lookup"><span data-stu-id="ed739-186">Create a *ViewComponents* folder and add the following `PriorityListViewComponent` class:</span></span>

[!code-csharp[Main](view-components/sample/ViewCompFinal/ViewComponents/PriorityListViewComponent1.cs?name=snippet1)]

<span data-ttu-id="ed739-187">코드에 대 한 참고 사항:</span><span class="sxs-lookup"><span data-stu-id="ed739-187">Notes on the code:</span></span>

* <span data-ttu-id="ed739-188">뷰 구성 요소 클래스에 포함 될 수 있는 **모든** 프로젝트의 폴더입니다.</span><span class="sxs-lookup"><span data-stu-id="ed739-188">View component classes can be contained in **any** folder in the project.</span></span>
* <span data-ttu-id="ed739-189">클래스 이름 PriorityList**ViewComponent** 된 접미사로 끝나는지 **ViewComponent**, 클래스 구성 요소는 뷰에서 참조 하는 경우 런타임이 "PriorityList" 문자열을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="ed739-189">Because the class name PriorityList**ViewComponent** ends with the suffix **ViewComponent**, the runtime will use the string "PriorityList" when referencing the class component from a view.</span></span> <span data-ttu-id="ed739-190">설명 하겠습니다를 자세히 나중에.</span><span class="sxs-lookup"><span data-stu-id="ed739-190">I'll explain that in more detail later.</span></span>
* <span data-ttu-id="ed739-191">`[ViewComponent]` 특성 뷰 구성 요소를 참조 하는 데 이름을 변경할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ed739-191">The `[ViewComponent]` attribute can change the name used to reference a view component.</span></span> <span data-ttu-id="ed739-192">예를 들어 우리 수에 명명 된 클래스 `XYZ` 적용 하 고는 `ViewComponent` 특성:</span><span class="sxs-lookup"><span data-stu-id="ed739-192">For example, we could have named the class `XYZ` and applied the `ViewComponent` attribute:</span></span>

  ```csharp
  [ViewComponent(Name = "PriorityList")]
     public class XYZ : ViewComponent
     ```

* <span data-ttu-id="ed739-193">`[ViewComponent]` 위의 특성은 이름을 사용 하 여 보기 구성 요소 선택기를 지시 `PriorityList` 클래스 구성 요소는 뷰에서 참조 하는 경우 "PriorityList" 문자열을 사용 하는 구성 요소와 관련 된 보기를 찾을 때.</span><span class="sxs-lookup"><span data-stu-id="ed739-193">The `[ViewComponent]` attribute above tells the view component selector to use the name `PriorityList` when looking for the views associated with the component, and to use the string "PriorityList" when referencing the class component from a view.</span></span> <span data-ttu-id="ed739-194">설명 하겠습니다를 자세히 나중에.</span><span class="sxs-lookup"><span data-stu-id="ed739-194">I'll explain that in more detail later.</span></span>
* <span data-ttu-id="ed739-195">이 구성 요소에서는 [종속성 주입](../../fundamentals/dependency-injection.md) 데이터 컨텍스트를 사용할 수 있도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="ed739-195">The component uses [dependency injection](../../fundamentals/dependency-injection.md) to make the data context available.</span></span>
* <span data-ttu-id="ed739-196">`InvokeAsync`노출 된 보기에서 호출 될 수 있는 메서드는 임의 개수의 인수 걸릴 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ed739-196">`InvokeAsync` exposes a method which can be called from a view, and it can take an arbitrary number of arguments.</span></span>
* <span data-ttu-id="ed739-197">`InvokeAsync` 메서드 집합을 반환 `ToDo` 만족 하는 항목의 `isDone` 및 `maxPriority` 매개 변수입니다.</span><span class="sxs-lookup"><span data-stu-id="ed739-197">The `InvokeAsync` method returns the set of `ToDo` items that satisfy the `isDone` and `maxPriority` parameters.</span></span>

### <a name="create-the-view-component-razor-view"></a><span data-ttu-id="ed739-198">구성 요소 Razor 뷰 만들기</span><span class="sxs-lookup"><span data-stu-id="ed739-198">Create the view component Razor view</span></span>

* <span data-ttu-id="ed739-199">만들기는 *구성 요소뷰/공유/* 폴더입니다.</span><span class="sxs-lookup"><span data-stu-id="ed739-199">Create the *Views/Shared/Components* folder.</span></span> <span data-ttu-id="ed739-200">이 폴더 **해야** 이름은 *구성 요소*합니다.</span><span class="sxs-lookup"><span data-stu-id="ed739-200">This folder **must** be named *Components*.</span></span>

* <span data-ttu-id="ed739-201">만들기는 *뷰/공유/구성 요소/PriorityList* 폴더입니다.</span><span class="sxs-lookup"><span data-stu-id="ed739-201">Create the *Views/Shared/Components/PriorityList* folder.</span></span> <span data-ttu-id="ed739-202">이 폴더 이름은 뷰 구성 요소 클래스의 이름 또는 접미사 뺀 클래스의 이름과 일치 해야 합니다 (규칙 따른 하 고 사용 되는 경우는 *ViewComponent* 클래스 이름에서 접미사).</span><span class="sxs-lookup"><span data-stu-id="ed739-202">This folder name must match the name of the view component class, or the name of the class minus the suffix (if we followed convention and used the *ViewComponent* suffix in the class name).</span></span> <span data-ttu-id="ed739-203">사용 하는 경우는 `ViewComponent` 특성을 클래스 이름 특성 지정은 일치 하도록 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ed739-203">If you used the `ViewComponent` attribute, the class name would need to match the attribute designation.</span></span>

* <span data-ttu-id="ed739-204">만들기는 *Views/Shared/Components/PriorityList/Default.cshtml* Razor 보기:[!code-html[Main](view-components/sample/ViewCompFinal/Views/Shared/Components/PriorityList/Default1.cshtml)]</span><span class="sxs-lookup"><span data-stu-id="ed739-204">Create a *Views/Shared/Components/PriorityList/Default.cshtml* Razor view: [!code-html[Main](view-components/sample/ViewCompFinal/Views/Shared/Components/PriorityList/Default1.cshtml)]</span></span>
    
   <span data-ttu-id="ed739-205">Razor 뷰 목록을 가져와서 `TodoItem` 하 고 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="ed739-205">The Razor view takes a list of `TodoItem` and displays them.</span></span> <span data-ttu-id="ed739-206">경우 뷰 구성 요소 `InvokeAsync` 메서드 (예: 우리의 샘플) 보기의 이름을 전달 하지 않습니다 *기본* 규칙에 따라 뷰 이름에 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ed739-206">If the view component `InvokeAsync` method doesn't pass the name of the view (as in our sample), *Default* is used for the view name by convention.</span></span> <span data-ttu-id="ed739-207">자습서의 뒷부분에 나오는 I 보여줍니다 보기의 이름을 전달 하는 방법.</span><span class="sxs-lookup"><span data-stu-id="ed739-207">Later in the tutorial, I'll show you how to pass the name of the view.</span></span> <span data-ttu-id="ed739-208">특정 컨트롤러에 대 한 기본 스타일을 재정의 하려면 보기 컨트롤러 관련 보기 폴더를 추가 합니다 (예를 들어 *Views/Todo/Components/PriorityList/Default.cshtml)*합니다.</span><span class="sxs-lookup"><span data-stu-id="ed739-208">To override the default styling for a specific controller, add a view to the controller-specific view folder (for example *Views/Todo/Components/PriorityList/Default.cshtml)*.</span></span>
    
    <span data-ttu-id="ed739-209">뷰 구성 요소 관련 컨트롤러 이면 컨트롤러 관련 폴더에 추가할 수 있습니다 (*Views/Todo/Components/PriorityList/Default.cshtml*).</span><span class="sxs-lookup"><span data-stu-id="ed739-209">If the view component is controller-specific, you can add it to the controller-specific folder (*Views/Todo/Components/PriorityList/Default.cshtml*).</span></span>

* <span data-ttu-id="ed739-210">추가 `div` 맨 아래에 우선 순위 목록 구성 요소에 대 한 호출을 포함 하는 *Views/Todo/index.cshtml* 파일:</span><span class="sxs-lookup"><span data-stu-id="ed739-210">Add a `div` containing a call to the priority list component to the bottom of the *Views/Todo/index.cshtml* file:</span></span>

    [!code-html[Main](view-components/sample/ViewCompFinal/Views/Todo/IndexFirst.cshtml?range=34-38)]

<span data-ttu-id="ed739-211">태그 `@await Component.InvokeAsync` 뷰 구성 요소를 호출 하는 구문을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="ed739-211">The markup `@await Component.InvokeAsync` shows the syntax for calling view components.</span></span> <span data-ttu-id="ed739-212">첫 번째 인수에는 구성 요소를 호출 하거나 호출 하려는의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="ed739-212">The first argument is the name of the component we want to invoke or call.</span></span> <span data-ttu-id="ed739-213">후속 매개 변수는 구성 요소에 전달 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ed739-213">Subsequent parameters are passed to the component.</span></span> <span data-ttu-id="ed739-214">`InvokeAsync`임의 개수의 인수를 걸릴 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ed739-214">`InvokeAsync` can take an arbitrary number of arguments.</span></span>

<span data-ttu-id="ed739-215">응용 프로그램을 테스트 합니다.</span><span class="sxs-lookup"><span data-stu-id="ed739-215">Test the app.</span></span> <span data-ttu-id="ed739-216">다음 이미지는 할 일 목록 및 우선 순위 항목을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="ed739-216">The following image shows the ToDo list and the priority items:</span></span>

![할 일 목록 및 우선 순위 항목](view-components/_static/pi.png)

<span data-ttu-id="ed739-218">또한 컨트롤러에서 직접 뷰 구성 요소를 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ed739-218">You can also call the view component directly from the controller:</span></span>

[!code-csharp[Main](view-components/sample/ViewCompFinal/Controllers/ToDoController.cs?name=snippet_IndexVC)]

![IndexVC 동작에서 우선 순위 항목](view-components/_static/indexvc.png)

### <a name="specifying-a-view-name"></a><span data-ttu-id="ed739-220">뷰 이름 지정</span><span class="sxs-lookup"><span data-stu-id="ed739-220">Specifying a view name</span></span>

<span data-ttu-id="ed739-221">복잡 한 뷰 구성 요소를 조건에 따라 기본이 아닌 보기를 지정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ed739-221">A complex view component might need to specify a non-default view under some conditions.</span></span> <span data-ttu-id="ed739-222">다음 코드에서 "PVC" 뷰를 지정 하는 방법을 보여 줍니다는 `InvokeAsync` 메서드.</span><span class="sxs-lookup"><span data-stu-id="ed739-222">The following code shows how to specify the "PVC" view  from the `InvokeAsync` method.</span></span> <span data-ttu-id="ed739-223">업데이트는 `InvokeAsync` 에서 메서드는 `PriorityListViewComponent` 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="ed739-223">Update the `InvokeAsync` method in the `PriorityListViewComponent` class.</span></span>

[!code-csharp[Main](../../mvc/views/view-components/sample/ViewCompFinal/ViewComponents/PriorityListViewComponentFinal.cs?highlight=4,5,6,7,8,9&range=28-39)]

<span data-ttu-id="ed739-224">복사는 *Views/Shared/Components/PriorityList/Default.cshtml* 파일 라는 이름의 뷰가을 *Views/Shared/Components/PriorityList/PVC.cshtml*합니다.</span><span class="sxs-lookup"><span data-stu-id="ed739-224">Copy the *Views/Shared/Components/PriorityList/Default.cshtml* file to a view named *Views/Shared/Components/PriorityList/PVC.cshtml*.</span></span> <span data-ttu-id="ed739-225">PVC 뷰를 사용 하는 것을 나타내려면 제목을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="ed739-225">Add a heading to indicate the PVC view is being used.</span></span>

[!code-html[Main](../../mvc/views/view-components/sample/ViewCompFinal/Views/Shared/Components/PriorityList/PVC.cshtml?highlight=3)]

<span data-ttu-id="ed739-226">업데이트 *Views/TodoList/Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="ed739-226">Update *Views/TodoList/Index.cshtml*:</span></span>

<!-- Views/TodoList/Index.cshtml is never imported, so change to test tutorial -->

[!code-html[Main](view-components/sample/ViewCompFinal/Views/Todo/IndexFinal.cshtml?range=35)]

<span data-ttu-id="ed739-227">응용 프로그램을 실행 하 고 PVC 보기를 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="ed739-227">Run the app and verify PVC view.</span></span>

![우선 순위 뷰 구성 요소](view-components/_static/pvc.png)

<span data-ttu-id="ed739-229">PVC 뷰 렌더링 되지 않습니다 4 이상을의 우선 순위를 가진 뷰 구성 요소를 호출 하는 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="ed739-229">If the PVC view is not rendered, verify you are calling the view component with a priority of 4 or higher.</span></span>

### <a name="examine-the-view-path"></a><span data-ttu-id="ed739-230">뷰 경로 검사</span><span class="sxs-lookup"><span data-stu-id="ed739-230">Examine the view path</span></span>

* <span data-ttu-id="ed739-231">변경 priority 매개 변수를 3 개 이하의 우선 순위 보기 반환 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ed739-231">Change the priority parameter to three or less so the priority view is not returned.</span></span>
* <span data-ttu-id="ed739-232">일시적으로 이름 바꾸기는 *Views/Todo/Components/PriorityList/Default.cshtml* 를 *1Default.cshtml*합니다.</span><span class="sxs-lookup"><span data-stu-id="ed739-232">Temporarily rename the *Views/Todo/Components/PriorityList/Default.cshtml* to *1Default.cshtml*.</span></span>
* <span data-ttu-id="ed739-233">앱을 테스트, 다음과 같은 오류 메시지가 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="ed739-233">Test the app, you'll get the following error:</span></span>

   ```
   An unhandled exception occurred while processing the request.
   InvalidOperationException: The view 'Components/PriorityList/Default' was not found. The following locations were searched:
   /Views/ToDo/Components/PriorityList/Default.cshtml
   /Views/Shared/Components/PriorityList/Default.cshtml
   EnsureSuccessful
   ```

* <span data-ttu-id="ed739-234">복사 *Views/Todo/Components/PriorityList/1Default.cshtml* 를 *Views/Shared/Components/PriorityList/Default.cshtml*합니다.</span><span class="sxs-lookup"><span data-stu-id="ed739-234">Copy *Views/Todo/Components/PriorityList/1Default.cshtml* to *Views/Shared/Components/PriorityList/Default.cshtml*.</span></span>
* <span data-ttu-id="ed739-235">일부 태그를 추가 *Shared* 에서 보기를 나타내기 위해 Todo 뷰 구성 요소 뷰를는 *Shared* 폴더입니다.</span><span class="sxs-lookup"><span data-stu-id="ed739-235">Add some markup to the *Shared* Todo view component view to indicate the view is from the *Shared* folder.</span></span>
* <span data-ttu-id="ed739-236">테스트는 **Shared** 구성 요소 뷰.</span><span class="sxs-lookup"><span data-stu-id="ed739-236">Test the **Shared** component view.</span></span>

![공유 구성 요소 뷰를 사용 하 여 할 일 출력](view-components/_static/shared.png)

### <a name="avoiding-magic-strings"></a><span data-ttu-id="ed739-238">매직 문자열 방지</span><span class="sxs-lookup"><span data-stu-id="ed739-238">Avoiding magic strings</span></span>

<span data-ttu-id="ed739-239">컴파일 시 안전성을 하려는 경우 클래스 이름의 보기 하드 코드 된 구성 요소 이름을 바꿀 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ed739-239">If you want compile time safety, you can replace the hard-coded view component name with the class name.</span></span> <span data-ttu-id="ed739-240">"ViewComponent" 접미사 없이 뷰 구성 요소를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="ed739-240">Create the view component without the "ViewComponent" suffix:</span></span>

[!code-csharp[Main](../../mvc/views/view-components/sample/ViewCompFinal/ViewComponents/PriorityList.cs?highlight=10&range=5-35)]

<span data-ttu-id="ed739-241">추가 `using` 파일을 확인 하 고 사용 하는 프로그램 Razor 문을 `nameof` 연산자:</span><span class="sxs-lookup"><span data-stu-id="ed739-241">Add a `using` statement to your Razor view file, and use the `nameof` operator:</span></span>

[!code-html[Main](view-components/sample/ViewCompFinal/Views/Todo/IndexNameof.cshtml?range=1-6,33-)]

## <a name="additional-resources"></a><span data-ttu-id="ed739-242">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="ed739-242">Additional Resources</span></span>

* [<span data-ttu-id="ed739-243">뷰에 종속성 주입</span><span class="sxs-lookup"><span data-stu-id="ed739-243">Dependency injection into views</span></span>](dependency-injection.md)
