---
title: "ASP.NET Core MVC 개요"
author: ardalis
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 89af38d1-52e0-4db7-b791-dbce909b0714
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/overview
ms.openlocfilehash: 67394b066c18a149a97b957d6478ba8301ea8147
ms.sourcegitcommit: 9cdbfd0d670d70b9c354216aabee260c52dad5ee
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/12/2017
---
# <a name="overview-of-aspnet-core-mvc"></a><span data-ttu-id="140c6-103">ASP.NET Core MVC 개요</span><span class="sxs-lookup"><span data-stu-id="140c6-103">Overview of ASP.NET Core MVC</span></span>

<span data-ttu-id="140c6-104">으로 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="140c6-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="140c6-105">ASP.NET Core MVC는 웹 앱을 빌드하기 위한 풍부한 프레임 워크 및 Api 모델-뷰-컨트롤러를 사용 하 여 패턴을 디자인 합니다.</span><span class="sxs-lookup"><span data-stu-id="140c6-105">ASP.NET Core MVC is a rich framework for building web apps and APIs using the Model-View-Controller design pattern.</span></span>

## <a name="what-is-the-mvc-pattern"></a><span data-ttu-id="140c6-106">MVC 패턴 무엇입니까?</span><span class="sxs-lookup"><span data-stu-id="140c6-106">What is the MVC pattern?</span></span>

<span data-ttu-id="140c6-107">모델-뷰-컨트롤러 (MVC) 아키텍처 패턴에서는 응용 프로그램을 3 개의 주요 구성 요소 그룹이: 모델, 뷰 및 컨트롤러입니다.</span><span class="sxs-lookup"><span data-stu-id="140c6-107">The Model-View-Controller (MVC) architectural pattern separates an application into three main groups of components: Models, Views, and Controllers.</span></span> <span data-ttu-id="140c6-108">이 패턴을 달성 하기 위해 사용 하면 [문제의 분리](http://deviq.com/separation-of-concerns/)합니다.</span><span class="sxs-lookup"><span data-stu-id="140c6-108">This pattern helps to achieve [separation of concerns](http://deviq.com/separation-of-concerns/).</span></span> <span data-ttu-id="140c6-109">이 패턴을 사용 하 여 사용자 요청은 사용자 작업을 수행할 및/또는 쿼리 결과 검색할 모델 작업을 담당 하는 컨트롤러에 라우팅됩니다.</span><span class="sxs-lookup"><span data-stu-id="140c6-109">Using this pattern, user requests are routed to a Controller which is responsible for working with the Model to perform user actions and/or retrieve results of queries.</span></span> <span data-ttu-id="140c6-110">컨트롤러는 사용자에 게 표시 하려면 보기를 선택 하 고 필요한 모든 모델 데이터를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="140c6-110">The Controller chooses the View to display to the user, and provides it with any Model data it requires.</span></span>

<span data-ttu-id="140c6-111">다음 그림에 세 가지 주요 구성 요소 및 다른 알림을 참조:</span><span class="sxs-lookup"><span data-stu-id="140c6-111">The following diagram shows the three main components and which ones reference the others:</span></span>

![MVC 패턴](overview/_static/mvc.png)

<span data-ttu-id="140c6-113">책임의이 경계를 사용 하면 보다 쉽게 코딩, 디버깅, 및가 소유한 항목 (모델, 뷰 또는 컨트롤러)을 단일 작업을 테스트할 수 있기 때문에 복잡성 측면에서 응용 프로그램의 크기를 조정 (따르는 [단일 책임 원칙 ](http://deviq.com/single-responsibility-principle/)).</span><span class="sxs-lookup"><span data-stu-id="140c6-113">This delineation of responsibilities helps you scale the application in terms of complexity because it’s easier to code, debug, and test something (model, view, or controller) that has a single job (and follows the [Single Responsibility Principle](http://deviq.com/single-responsibility-principle/)).</span></span> <span data-ttu-id="140c6-114">업데이트, 테스트 및 디버그 종속성이 코드를 두 개 이상의 이러한 세 가지 영역에 분산 하려면 더 어렵습니다.</span><span class="sxs-lookup"><span data-stu-id="140c6-114">It's more difficult to update, test, and debug code that has dependencies spread across two or more of these three areas.</span></span> <span data-ttu-id="140c6-115">예를 들어, 사용자 인터페이스 논리 비즈니스 논리 보다 더 자주 변경 하는 경향이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="140c6-115">For example, user interface logic tends to change more frequently than business logic.</span></span> <span data-ttu-id="140c6-116">단일 개체에 프레젠테이션 코드 및 비즈니스 논리를 결합 하는 경우 사용자 인터페이스를 변경할 때마다 비즈니스 논리를 포함 하는 개체를 수정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="140c6-116">If presentation code and business logic are combined in a single object, you have to modify an object containing business logic every time you change the user interface.</span></span> <span data-ttu-id="140c6-117">이 오류가 및 인증의 재 테스트 모든 비즈니스 논리의 모든 최소한의 사용자 인터페이스를 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="140c6-117">This is likely to introduce errors and require the retesting of all business logic after every minimal user interface change.</span></span>

> [!NOTE]
> <span data-ttu-id="140c6-118">뷰와 컨트롤러 모두 모델에 따라 달라 집니다.</span><span class="sxs-lookup"><span data-stu-id="140c6-118">Both the view and the controller depend on the model.</span></span> <span data-ttu-id="140c6-119">그러나 모델 보기도 아니고 컨트롤러에 따라 달라 집니다.</span><span class="sxs-lookup"><span data-stu-id="140c6-119">However, the model depends on neither the view nor the controller.</span></span> <span data-ttu-id="140c6-120">분리의 주요 이점 중 하나입니다.</span><span class="sxs-lookup"><span data-stu-id="140c6-120">This is one of the key benefits of the separation.</span></span> <span data-ttu-id="140c6-121">이 분리를 통해 작성 하 고 테스트할 모델이 화면 표시와 무관 합니다.</span><span class="sxs-lookup"><span data-stu-id="140c6-121">This separation allows the model to be built and tested independent of the visual presentation.</span></span>

### <a name="model-responsibilities"></a><span data-ttu-id="140c6-122">모델의 책임</span><span class="sxs-lookup"><span data-stu-id="140c6-122">Model Responsibilities</span></span>

<span data-ttu-id="140c6-123">MVC 응용 프로그램에서 모델은 응용 프로그램 및 모든 비즈니스 논리 또는 것을 통해 수행 해야 하는 작업의 상태를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="140c6-123">The Model in an MVC application represents the state of the application and any business logic or operations that should be performed by it.</span></span> <span data-ttu-id="140c6-124">응용 프로그램의 상태를 유지 하기 위한 모든 구현 논리와 함께 모델에 비즈니스 논리를 캡슐화 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="140c6-124">Business logic should be encapsulated in the model, along with any implementation logic for persisting the state of the application.</span></span> <span data-ttu-id="140c6-125">강력한 형식의 뷰 데이터를 포함 하도록 특별히 설계 된 ViewModel 형식; 해당 보기에 표시 하려면 일반적으로 사용 합니다. 컨트롤러를 만들고 모델에서 이러한 ViewModel 인스턴스를 채웁니다.</span><span class="sxs-lookup"><span data-stu-id="140c6-125">Strongly-typed views will typically use ViewModel types specifically designed to contain the data to display on that view; the controller will create and populate these ViewModel instances from the model.</span></span>

> [!NOTE]
> <span data-ttu-id="140c6-126">MVC 아키텍처 패턴을 사용 하는 응용 프로그램에서 모델을 구성 하는 방법은 여러 가지가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="140c6-126">There are many ways to organize the model in an app that uses the MVC architectural pattern.</span></span> <span data-ttu-id="140c6-127">일부에 대 한 자세한 [여러 가지 모델 유형에](http://deviq.com/kinds-of-models/)합니다.</span><span class="sxs-lookup"><span data-stu-id="140c6-127">Learn more about some [different kinds of model types](http://deviq.com/kinds-of-models/).</span></span>

### <a name="view-responsibilities"></a><span data-ttu-id="140c6-128">보기의 책임</span><span class="sxs-lookup"><span data-stu-id="140c6-128">View Responsibilities</span></span>

<span data-ttu-id="140c6-129">뷰는 사용자 인터페이스를 통해 콘텐츠를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="140c6-129">Views are responsible for presenting content through the user interface.</span></span> <span data-ttu-id="140c6-130">사용 하는 [Razor 뷰 엔진](#razor-view-engine) HTML 태그에.NET 코드를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="140c6-130">They use the [Razor view engine](#razor-view-engine) to embed .NET code in HTML markup.</span></span> <span data-ttu-id="140c6-131">뷰 내에서 논리를 최소화 해야 하며 모든 논리 관련 콘텐츠를 제공 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="140c6-131">There should be minimal logic within views, and any logic in them should relate to presenting content.</span></span> <span data-ttu-id="140c6-132">사용 하 여 보기에 있는 다양 한 논리를 수행할 필요가 복잡 한 모델의 데이터를에서 표시 하기 위해 파일을 찾으면 고려는 [뷰 구성 요소](views/view-components.md), ViewModel, 또는 보기를 간소화 하기 위해 템플릿 보기.</span><span class="sxs-lookup"><span data-stu-id="140c6-132">If you find the need to perform a great deal of logic in view files in order to display data from a complex model, consider using a [View Component](views/view-components.md), ViewModel, or view template to simplify the view.</span></span>

### <a name="controller-responsibilities"></a><span data-ttu-id="140c6-133">컨트롤러의 책임</span><span class="sxs-lookup"><span data-stu-id="140c6-133">Controller Responsibilities</span></span>

<span data-ttu-id="140c6-134">컨트롤러는 사용자 상호 작용 처리, 모델로 작업 하 고, 궁극적으로에 렌더링할 뷰를 선택 하는 구성 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="140c6-134">Controllers are the components that handle user interaction, work with the model, and ultimately select a view to render.</span></span> <span data-ttu-id="140c6-135">MVC 응용 프로그램에서 뷰 정보만 표시 됩니다. 컨트롤러 처리 하며 사용자 입력 및 상호 작용에 응답 합니다.</span><span class="sxs-lookup"><span data-stu-id="140c6-135">In an MVC application, the view only displays information; the controller handles and responds to user input and interaction.</span></span> <span data-ttu-id="140c6-136">MVC 패턴에서 컨트롤러는 초기 진입점 하 고 모델을 사용 하려면 형식 및 렌더링 하는 보기를 선택 (따라서 이름-제어 응용 프로그램 요청에 응답 하는 방법을).</span><span class="sxs-lookup"><span data-stu-id="140c6-136">In the MVC pattern, the controller is the initial entry point, and is responsible for selecting which model types to work with and which view to render (hence its name - it controls how the app responds to a given request).</span></span>

> [!NOTE]
> <span data-ttu-id="140c6-137">너무 많은 책임으로 컨트롤러를 지나치게 복잡 수 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="140c6-137">Controllers should not be overly complicated by too many responsibilities.</span></span> <span data-ttu-id="140c6-138">컨트롤러 논리를 지나치게 복잡 되는 것을 유지 하기 위해 사용 하 여는 [단일 책임 원칙](http://deviq.com/single-responsibility-principle/) 푸시 비즈니스 논리는 컨트롤러를 도메인 모델입니다.</span><span class="sxs-lookup"><span data-stu-id="140c6-138">To keep controller logic from becoming overly complex, use the [Single Responsibility Principle](http://deviq.com/single-responsibility-principle/) to push business logic out of the controller and into the domain model.</span></span>

>[!TIP]
> <span data-ttu-id="140c6-139">컨트롤러 작업 같은 종류의 작업을 자주 수행를 찾을 경우 참고할 수는 [직접 원칙 반복 안 함](http://deviq.com/don-t-repeat-yourself/) 이러한 일반적인 작업을 이동 하 여 [필터](#filters)합니다.</span><span class="sxs-lookup"><span data-stu-id="140c6-139">If you find that your controller actions frequently perform the same kinds of actions, you can follow the [Don't Repeat Yourself principle](http://deviq.com/don-t-repeat-yourself/) by moving these common actions into [filters](#filters).</span></span>

## <a name="what-is-aspnet-core-mvc"></a><span data-ttu-id="140c6-140">ASP.NET Core MVC 이란</span><span class="sxs-lookup"><span data-stu-id="140c6-140">What is ASP.NET Core MVC</span></span>

<span data-ttu-id="140c6-141">ASP.NET Core MVC 프레임 워크는는 간단한 오픈 소스로 테스트 하기 편리한 프레젠테이션 프레임 워크를 ASP.NET Core에 맞게 최적화 합니다.</span><span class="sxs-lookup"><span data-stu-id="140c6-141">The ASP.NET Core MVC framework is a lightweight, open source, highly testable presentation framework optimized for use with ASP.NET Core.</span></span>

<span data-ttu-id="140c6-142">ASP.NET Core MVC 문제의 확실히 구분할 수 있도록 하는 동적 웹 사이트를 구축 하는 패턴 기반의 방법을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="140c6-142">ASP.NET Core MVC provides a patterns-based way to build dynamic websites that enables a clean separation of concerns.</span></span> <span data-ttu-id="140c6-143">가능 하 게 지원 하며 최신 웹 표준을 사용 하 여 태그에 대 한 모든 권한을 제공 것입니다.</span><span class="sxs-lookup"><span data-stu-id="140c6-143">It gives you full control over markup, supports TDD-friendly development and uses the latest web standards.</span></span>

## <a name="features"></a><span data-ttu-id="140c6-144">기능</span><span class="sxs-lookup"><span data-stu-id="140c6-144">Features</span></span>

<span data-ttu-id="140c6-145">ASP.NET Core MVC는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="140c6-145">ASP.NET Core MVC includes the following:</span></span>

* [<span data-ttu-id="140c6-146">라우팅</span><span class="sxs-lookup"><span data-stu-id="140c6-146">Routing</span></span>](#routing)
* [<span data-ttu-id="140c6-147">모델 바인딩</span><span class="sxs-lookup"><span data-stu-id="140c6-147">Model binding</span></span>](#model-binding)
* [<span data-ttu-id="140c6-148">모델 유효성 검사</span><span class="sxs-lookup"><span data-stu-id="140c6-148">Model validation</span></span>](#model-validation)
* [<span data-ttu-id="140c6-149">종속성 주입</span><span class="sxs-lookup"><span data-stu-id="140c6-149">Dependency injection</span></span>](../fundamentals/dependency-injection.md)
* [<span data-ttu-id="140c6-150">필터</span><span class="sxs-lookup"><span data-stu-id="140c6-150">Filters</span></span>](#filters)
* [<span data-ttu-id="140c6-151">영역</span><span class="sxs-lookup"><span data-stu-id="140c6-151">Areas</span></span>](#areas)
* [<span data-ttu-id="140c6-152">웹 Api</span><span class="sxs-lookup"><span data-stu-id="140c6-152">Web APIs</span></span>](#web-apis)
* [<span data-ttu-id="140c6-153">테스트 가능성</span><span class="sxs-lookup"><span data-stu-id="140c6-153">Testability</span></span>](#testability)
* [<span data-ttu-id="140c6-154">Razor 뷰 엔진</span><span class="sxs-lookup"><span data-stu-id="140c6-154">Razor view engine</span></span>](#razor-view-engine)
* [<span data-ttu-id="140c6-155">강력한 형식의 뷰</span><span class="sxs-lookup"><span data-stu-id="140c6-155">Strongly typed views</span></span>](#strongly-typed-views)
* [<span data-ttu-id="140c6-156">태그 도우미</span><span class="sxs-lookup"><span data-stu-id="140c6-156">Tag Helpers</span></span>](#tag-helpers)
* [<span data-ttu-id="140c6-157">구성 요소 보기</span><span class="sxs-lookup"><span data-stu-id="140c6-157">View Components</span></span>](#view-components)

### <a name="routing"></a><span data-ttu-id="140c6-158">라우팅</span><span class="sxs-lookup"><span data-stu-id="140c6-158">Routing</span></span>

<span data-ttu-id="140c6-159">ASP.NET Core MVC의 맨 위에 빌드됩니다 [ASP.NET Core 라우팅](../fundamentals/routing.md), 수 있는 강력한 URL 매핑 구성 요소 알기 쉽고 검색 가능한 Url이 있는 응용 프로그램을 작성 합니다.</span><span class="sxs-lookup"><span data-stu-id="140c6-159">ASP.NET Core MVC is built on top of [ASP.NET Core's routing](../fundamentals/routing.md), a powerful URL-mapping component that lets you build applications that have comprehensible and searchable URLs.</span></span> <span data-ttu-id="140c6-160">이렇게 하면 웹 서버에 있는 파일의 구성 방법에 관계 없이 링크 생성 및 검색 엔진 최적화 (SEO)에 대 한 잘 작동 하는 응용 프로그램의 URL 이름 지정 패턴을 정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="140c6-160">This enables you to define your application's URL naming patterns that work well for search engine optimization (SEO) and for link generation, without regard for how the files on your web server are organized.</span></span> <span data-ttu-id="140c6-161">경로 값 제약 조건, 기본값 및 선택적 값을 지원 하는 편리한 경로 템플릿 구문을 사용 하 여 프로그램 경로 정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="140c6-161">You can define your routes using a convenient route template syntax that supports route value constraints, defaults and optional values.</span></span>

<span data-ttu-id="140c6-162">*규칙 기반 라우팅* 사용 되는 형식 전체적으로 URL을 정의할 수 있습니다 응용 프로그램 허용 하 고 지정한 컨트롤러에 특정 동작 메서드에 매핑됩니다 이들의 각 형식을 지정 하는 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="140c6-162">*Convention-based routing* enables you to globally define the URL formats that your application accepts and how each of those formats maps to a specific action method on given controller.</span></span> <span data-ttu-id="140c6-163">들어오는 요청을 받으면 라우팅 엔진 URL을 구문 분석 및 정의 된 URL 형식 중 하나로 일치 하 고 연결된 된 컨트롤러 동작 메서드를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="140c6-163">When an incoming request is received, the routing engine parses the URL and matches it to one of the defined URL formats, and then calls the associated controller's action method.</span></span>

```csharp
routes.MapRoute(name: "Default", template: "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="140c6-164">*라우팅 특성* 컨트롤러와 응용 프로그램의 경로 정의 하는 특성을 사용 하 여 동작 데코레이팅하여 라우팅 정보를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="140c6-164">*Attribute routing* enables you to specify routing information by decorating your controllers and actions with attributes that define your application's routes.</span></span> <span data-ttu-id="140c6-165">즉, route 정의 컨트롤러 및 작업 자신이 연결 옆에 배치 됩니다.</span><span class="sxs-lookup"><span data-stu-id="140c6-165">This means that your route definitions are placed next to the controller and action with which they're associated.</span></span>

<!-- literal_block {"ids": [], "linenos": false, "xml:space": "preserve", "language": "csharp", "highlight_args": {"hl_lines": [1, 4]}} -->

```csharp
[Route("api/[controller]")]
public class ProductsController : Controller
{
  [HttpGet("{id}")]
  public IActionResult GetProduct(int id)
  {
    ...
  }
}
```

### <a name="model-binding"></a><span data-ttu-id="140c6-166">모델 바인딩</span><span class="sxs-lookup"><span data-stu-id="140c6-166">Model binding</span></span>

<span data-ttu-id="140c6-167">ASP.NET Core MVC [모델 바인딩](models/model-binding.md) 클라이언트 요청 데이터 (폼 값, 경로 데이터, 쿼리 문자열 매개 변수, HTTP 헤더)는 컨트롤러 처리할 수 있는 개체를 변환 합니다.</span><span class="sxs-lookup"><span data-stu-id="140c6-167">ASP.NET Core MVC [model binding](models/model-binding.md) converts client request data  (form values, route data, query string parameters, HTTP headers) into objects that the controller can handle.</span></span> <span data-ttu-id="140c6-168">컨트롤러 논리에서 들어오는 요청 데이터는 사항을 알아내는의 작업을 수행할 수 없는 결과적으로, 단순히 해당 작업 메서드에 매개 변수로 데이터를 갖습니다.</span><span class="sxs-lookup"><span data-stu-id="140c6-168">As a result, your controller logic doesn't have to do the work of figuring out the incoming request data; it simply has the data as parameters to its action methods.</span></span>

```csharp
public async Task<IActionResult> Login(LoginViewModel model, string returnUrl = null) { ... }
   ```

### <a name="model-validation"></a><span data-ttu-id="140c6-169">모델 유효성 검사</span><span class="sxs-lookup"><span data-stu-id="140c6-169">Model validation</span></span>

<span data-ttu-id="140c6-170">ASP.NET Core MVC 지원 [유효성 검사](models/validation.md) 데이터 주석 유효성 검사 특성으로 모델 개체를 데코레이팅하여 합니다.</span><span class="sxs-lookup"><span data-stu-id="140c6-170">ASP.NET Core MVC supports [validation](models/validation.md) by decorating your model object with data annotation validation attributes.</span></span> <span data-ttu-id="140c6-171">유효성 검사 특성 값은 서버에 게시 전에 클라이언트 쪽에서 확인으로 컨트롤러 작업 전에 서버에 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="140c6-171">The validation attributes are checked on the client side before values are posted to the server, as well as on the server before the controller action is called.</span></span>

<!-- literal_block {"ids": [], "linenos": false, "xml:space": "preserve", "language": "csharp", "highlight_args": {"hl_lines": [4, 5, 8, 9]}} -->

```csharp
using System.ComponentModel.DataAnnotations;
public class LoginViewModel
{
    [Required]
    [EmailAddress]
    public string Email { get; set; }

    [Required]
    [DataType(DataType.Password)]
    public string Password { get; set; }

    [Display(Name = "Remember me?")]
    public bool RememberMe { get; set; }
}
```

<span data-ttu-id="140c6-172">컨트롤러 동작:</span><span class="sxs-lookup"><span data-stu-id="140c6-172">A controller action:</span></span>

<!-- literal_block {"ids": [], "linenos": false, "xml:space": "preserve", "language": "csharp", "highlight_args": {"hl_lines": [3]}} -->

```csharp
public async Task<IActionResult> Login(LoginViewModel model, string returnUrl = null)
{
    if (ModelState.IsValid)
    {
      // work with the model
    }
    // If we got this far, something failed, redisplay form
    return View(model);
}
```

<span data-ttu-id="140c6-173">프레임 워크 클라이언트 및 서버에서 요청 데이터 유효성 검사를 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="140c6-173">The framework will handle validating request data both on the client and on the server.</span></span> <span data-ttu-id="140c6-174">모델 유형에 대해 지정 된 유효성 검사 논리 비간섭 주석으로 렌더링 된 보기에 추가 되 고 사용 하 여 브라우저에 적용 됩니다 [jQuery 유효성 검사](https://jqueryvalidation.org/)합니다.</span><span class="sxs-lookup"><span data-stu-id="140c6-174">Validation logic specified on model types is added to the rendered views as unobtrusive annotations and is enforced in the browser with [jQuery Validation](https://jqueryvalidation.org/).</span></span>

### <a name="dependency-injection"></a><span data-ttu-id="140c6-175">종속성 주입</span><span class="sxs-lookup"><span data-stu-id="140c6-175">Dependency injection</span></span>

<span data-ttu-id="140c6-176">ASP.NET Core에서 기본적으로 지원 [종속성 주입 (DI)](../fundamentals/dependency-injection.md)합니다.</span><span class="sxs-lookup"><span data-stu-id="140c6-176">ASP.NET Core has built-in support for [dependency injection (DI)](../fundamentals/dependency-injection.md).</span></span> <span data-ttu-id="140c6-177">ASP.NET Core MVC에서 [컨트롤러](controllers/dependency-injection.md) 요청 수를 수행 하도록 허용 하는 생성자를 통해 서비스 필요는 [명시적 종속성 원칙](http://deviq.com/explicit-dependencies-principle/)합니다.</span><span class="sxs-lookup"><span data-stu-id="140c6-177">In ASP.NET Core MVC, [controllers](controllers/dependency-injection.md) can request needed services through their constructors, allowing them to follow the [Explicit Dependencies Principle](http://deviq.com/explicit-dependencies-principle/).</span></span>

<span data-ttu-id="140c6-178">앱 ´ ï ´ [종속성 주입 보이는 파일](views/dependency-injection.md)를 사용 하 여는 `@inject` 지시문:</span><span class="sxs-lookup"><span data-stu-id="140c6-178">Your app can also use [dependency injection in view files](views/dependency-injection.md), using the `@inject` directive:</span></span>

<!-- literal_block {"ids": [], "linenos": false, "xml:space": "preserve", "language": "html", "highlight_args": {"hl_lines": [1]}} -->

```html
@inject SomeService ServiceName
<!DOCTYPE html>
<html>
<head>
  <title>@ServiceName.GetTitle</title>
</head>
<body>
  <h1>@ServiceName.GetTitle</h1>
</body>
</html>
```

### <a name="filters"></a><span data-ttu-id="140c6-179">필터</span><span class="sxs-lookup"><span data-stu-id="140c6-179">Filters</span></span>

<span data-ttu-id="140c6-180">[필터](controllers/filters.md) 개발자가 예외 처리 또는 권한 부여와 같은 일반적인 문제를 캡슐화 합니다.</span><span class="sxs-lookup"><span data-stu-id="140c6-180">[Filters](controllers/filters.md) help developers encapsulate cross-cutting concerns, like exception handling or authorization.</span></span> <span data-ttu-id="140c6-181">필터 작업 메서드에 대 한 전처리 및 후 처리 논리를 실행 중인 사용자 지정을 사용 하도록 설정 하며 지정된 된 요청에 대 한 실행 파이프라인 내에서 특정 지점에서 실행 되도록 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="140c6-181">Filters enable running custom pre- and post-processing logic for action methods, and can be configured to run at certain points within the execution pipeline for a given request.</span></span> <span data-ttu-id="140c6-182">컨트롤러 또는 특성으로 작업에 적용할 수 있습니다 (필터나 전체적으로 실행 될 수 있습니다).</span><span class="sxs-lookup"><span data-stu-id="140c6-182">Filters can be applied to controllers or actions as attributes (or can be run globally).</span></span> <span data-ttu-id="140c6-183">여러 필터 (예: `Authorize`) 프레임 워크에 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="140c6-183">Several filters (such as `Authorize`) are included in the framework.</span></span>


```csharp
[Authorize]
   public class AccountController : Controller
   {

```

### <a name="areas"></a><span data-ttu-id="140c6-184">영역</span><span class="sxs-lookup"><span data-stu-id="140c6-184">Areas</span></span>

<span data-ttu-id="140c6-185">[영역](controllers/areas.md) 큰 ASP.NET Core MVC 웹 응용 프로그램 여러 개의 작은 기능 그룹으로 분할 하는 방법을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="140c6-185">[Areas](controllers/areas.md) provide a way to partition a large ASP.NET Core MVC Web app into smaller functional groupings.</span></span> <span data-ttu-id="140c6-186">실질적으로 영역은 응용 프로그램 내의 MVC 구조입니다.</span><span class="sxs-lookup"><span data-stu-id="140c6-186">An area is effectively an MVC structure inside an application.</span></span> <span data-ttu-id="140c6-187">MVC 프로젝트에서 모델, 컨트롤러 및 보기와 같은 논리적 구성 요소는 서로 다른 폴더에 저장 하는 및 MVC 명명 규칙을 사용 하 여 이러한 구성 요소 간의 관계를 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="140c6-187">In an MVC project, logical components like Model, Controller, and View are kept in different folders, and MVC uses naming conventions to create the relationship between these components.</span></span> <span data-ttu-id="140c6-188">규모가 큰 앱에 대 한 별도 높은 수준의 기능 영역을를 응용 프로그램을 분할 하는 것이 도움이 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="140c6-188">For a large app, it may be advantageous to partition the app into separate high level areas of functionality.</span></span> <span data-ttu-id="140c6-189">예를 들어, 체크 아웃, 청구 및 검색 등과 같은 여러 비즈니스 단위를 사용 하 여 전자 상거래 앱입니다. 각 이러한 단위가 있는 자신의 논리적 구성 요소 뷰, 컨트롤러 및 모델입니다.</span><span class="sxs-lookup"><span data-stu-id="140c6-189">For instance, an e-commerce app with multiple business units, such as checkout, billing, and search etc. Each of these units have their own logical component views, controllers, and models.</span></span>

### <a name="web-apis"></a><span data-ttu-id="140c6-190">웹 Api</span><span class="sxs-lookup"><span data-stu-id="140c6-190">Web APIs</span></span>

<span data-ttu-id="140c6-191">웹 사이트를 구축 하기 위한 훌륭한 플랫폼 않도록, ASP.NET Core MVC는 웹 Api를 구축 하기 위한 훌륭한 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="140c6-191">In addition to being a great platform for building web sites, ASP.NET Core MVC has great support for building Web APIs.</span></span> <span data-ttu-id="140c6-192">다양 한 브라우저 및 모바일 장치를 포함 하 여 클라이언트를 연결할 수 있는 서비스를 작성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="140c6-192">You can build services that can reach a broad range of clients including browsers and mobile devices.</span></span>

<span data-ttu-id="140c6-193">프레임 워크에서는 기본적으로 지 원하는 HTTP 콘텐츠 협상을 지원 [데이터 서식 지정](models/formatting.md) JSON 또는 XML입니다.</span><span class="sxs-lookup"><span data-stu-id="140c6-193">The framework includes support for HTTP content-negotiation with built-in support for [formatting data](models/formatting.md) as JSON or XML.</span></span> <span data-ttu-id="140c6-194">쓰기 [사용자 지정 포맷터](advanced/custom-formatters.md) 사용자의 고유 형식에 대 한 지원을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="140c6-194">Write [custom formatters](advanced/custom-formatters.md) to add support for your own formats.</span></span>

<span data-ttu-id="140c6-195">링크 생성을 사용 하 여 하이퍼미디어에 대 한 지원을 사용 하도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="140c6-195">Use link generation to enable support for hypermedia.</span></span> <span data-ttu-id="140c6-196">쉽게에 대 한 지원을 사용 하도록 설정할 [크로스-원본 자원 공유 (CORS)](http://www.w3.org/TR/cors/) 여러 웹 응용 프로그램에서 Web Api를 공유할 수 있도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="140c6-196">Easily enable support for [cross-origin resource sharing (CORS)](http://www.w3.org/TR/cors/) so that your Web APIs can be shared across multiple Web applications.</span></span>

### <a name="testability"></a><span data-ttu-id="140c6-197">테스트 가능성</span><span class="sxs-lookup"><span data-stu-id="140c6-197">Testability</span></span>

<span data-ttu-id="140c6-198">인터페이스 및 종속성 주입의 프레임 워크의 사용 더 적합 한 단위 테스트를 지정 하 고 구성 하는 기능 (예: Entity Framework 용 TestHost 및 InMemory 공급자)를 포함 하는 프레임 워크 [통합 테스트](../testing/integration-testing.md) 빠른 쉽고도 합니다.</span><span class="sxs-lookup"><span data-stu-id="140c6-198">The framework's use of interfaces and dependency injection make it well-suited to unit testing, and the framework includes features (like a TestHost and InMemory provider for Entity Framework) that make [integration testing](../testing/integration-testing.md) quick and easy as well.</span></span> <span data-ttu-id="140c6-199">에 대 한 자세한 내용은 [컨트롤러 논리를 테스트](controllers/testing.md)합니다.</span><span class="sxs-lookup"><span data-stu-id="140c6-199">Learn more about [testing controller logic](controllers/testing.md).</span></span>

### <a name="razor-view-engine"></a><span data-ttu-id="140c6-200">Razor 뷰 엔진</span><span class="sxs-lookup"><span data-stu-id="140c6-200">Razor view engine</span></span>

<span data-ttu-id="140c6-201">[ASP.NET Core MVC 뷰](views/overview.md) 사용는 [Razor 뷰 엔진](views/razor.md) 뷰를 렌더링 합니다.</span><span class="sxs-lookup"><span data-stu-id="140c6-201">[ASP.NET Core MVC views](views/overview.md) use the [Razor view engine](views/razor.md) to render views.</span></span> <span data-ttu-id="140c6-202">Razor은 포함 된 C# 코드를 사용 하 여 뷰를 정의 하기 위한 compact 표현이 다양 하 고 유연한 템플릿 태그 언어입니다.</span><span class="sxs-lookup"><span data-stu-id="140c6-202">Razor is a compact, expressive and fluid template markup language for defining views using embedded C# code.</span></span> <span data-ttu-id="140c6-203">Razor 웹 콘텐츠 서버에서 동적으로 생성 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="140c6-203">Razor is used to dynamically generate web content on the server.</span></span> <span data-ttu-id="140c6-204">클라이언트 쪽 내용 및 코드와 서버 코드를 완전히 혼합할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="140c6-204">You can cleanly mix server code with client side content and code.</span></span>

```text
<ul>
  @for (int i = 0; i < 5; i++) {
    <li>List item @i</li>
  }
</ul>
```

<span data-ttu-id="140c6-205">Razor 뷰 엔진을 정의할 수 있습니다를 사용 하 여 [레이아웃](views/layout.md), [부분 뷰](views/partial.md) 및 대체 가능 섹션입니다.</span><span class="sxs-lookup"><span data-stu-id="140c6-205">Using the Razor view engine you can define [layouts](views/layout.md), [partial views](views/partial.md) and replaceable sections.</span></span>

### <a name="strongly-typed-views"></a><span data-ttu-id="140c6-206">강력한 형식의 뷰</span><span class="sxs-lookup"><span data-stu-id="140c6-206">Strongly typed views</span></span>

<span data-ttu-id="140c6-207">Mvc에서 razor 뷰 수 강력한 형식 모델에 따라 합니다.</span><span class="sxs-lookup"><span data-stu-id="140c6-207">Razor views in MVC can be strongly typed based on your model.</span></span> <span data-ttu-id="140c6-208">컨트롤러 형식 검사 및 IntelliSense 지원을 보기를 사용 하도록 설정 하는 보기에는 강력한 형식의 모델을 전달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="140c6-208">Controllers can pass a strongly typed model to views enabling your views to have type checking and IntelliSense support.</span></span>

<span data-ttu-id="140c6-209">다음 보기 형식의 모델을 정의 하는 예를 들어 `IEnumerable<Product>`:</span><span class="sxs-lookup"><span data-stu-id="140c6-209">For example, the following view defines a model of type `IEnumerable<Product>`:</span></span>

```html
@model IEnumerable<Product>
<ul>
    @foreach (Product p in Model)
    {
        <li>@p.Name</li>
    }
</ul>
```

### <a name="tag-helpers"></a><span data-ttu-id="140c6-210">태그 도우미</span><span class="sxs-lookup"><span data-stu-id="140c6-210">Tag Helpers</span></span>

<span data-ttu-id="140c6-211">[태그 도우미](views/tag-helpers/intro.md) 만들기 및 Razor 파일에서 HTML 요소 렌더링에 참여 하도록 서버 쪽 코드를 사용 하도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="140c6-211">[Tag Helpers](views/tag-helpers/intro.md) enable server side code to participate in creating and rendering HTML elements in Razor files.</span></span> <span data-ttu-id="140c6-212">사용자 지정 태그를 정의 하려면 태그 도우미를 사용할 수 있습니다 (예를 들어 `<environment>`) 또는 기존 태그의 동작을 수정 (예를 들어 `<label>`).</span><span class="sxs-lookup"><span data-stu-id="140c6-212">You can use tag helpers to define custom tags (for example, `<environment>`) or to modify the behavior of existing tags (for example, `<label>`).</span></span> <span data-ttu-id="140c6-213">태그 도우미 요소 이름 및 해당 특성에 따라 특정 요소에 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="140c6-213">Tag Helpers bind to specific elements based on the element name and its attributes.</span></span> <span data-ttu-id="140c6-214">서버 쪽 렌더링 편집 환경을 제공 하는 HTML을 유지 하면서의 이점을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="140c6-214">They provide the benefits of server-side rendering while still preserving an HTML editing experience.</span></span>

<span data-ttu-id="140c6-215">-양식, 링크, 로드 자산 및 점점-공용 GitHub 리포지토리에 및 NuGet로 훨씬 더 사용할 수 있는 패키지 만들기와 같은 일반적인 작업에 대 한 기본 제공 태그 도우미 여러 가지가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="140c6-215">There are many built-in Tag Helpers for common tasks - such as creating forms, links, loading assets and more - and even more available in public GitHub repositories and as NuGet packages.</span></span> <span data-ttu-id="140c6-216">태그 도우미 C#에서 작성 하 고 요소 이름, 특성 이름 또는 부모 태그를 기반으로 하는 HTML 요소를 대상입니다.</span><span class="sxs-lookup"><span data-stu-id="140c6-216">Tag Helpers are authored in C#, and they target HTML elements based on element name, attribute name, or parent tag.</span></span> <span data-ttu-id="140c6-217">예를 들어 기본적으로 제공 된 LinkTagHelper에 대 한 링크를 만드는 데 사용할 수는 `Login` 의 작업은 `AccountsController`:</span><span class="sxs-lookup"><span data-stu-id="140c6-217">For example, the built-in LinkTagHelper can be used to create a link to the `Login` action of the `AccountsController`:</span></span>

<!-- literal_block {"ids": [], "linenos": false, "xml:space": "preserve", "language": "html", "highlight_args": {"hl_lines": [3]}} -->

```html
<p>
    Thank you for confirming your email.
    Please <a asp-controller="Account" asp-action="Login">Click here to Log in</a>.
</p>
```

<span data-ttu-id="140c6-218">`EnvironmentTagHelper` 는 개발, 스테이징 또는 프로덕션 같은 런타임 환경에 따라 (예: 원시 또는 최소화 된) 보기에서 다른 스크립트를 포함 하는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="140c6-218">The `EnvironmentTagHelper` can be used to include different scripts in your views (for example, raw or minified) based on the runtime environment, such as Development, Staging, or Production:</span></span>

<!-- literal_block {"ids": [], "linenos": false, "xml:space": "preserve", "language": "html", "highlight_args": {"hl_lines": [1, 3, 4, 9]}} -->

```html
<environment names="Development">
    <script src="~/lib/jquery/dist/jquery.js"></script>
</environment>
<environment names="Staging,Production">
    <script src="https://ajax.aspnetcdn.com/ajax/jquery/jquery-2.1.4.min.js"
            asp-fallback-src="~/lib/jquery/dist/jquery.min.js"
            asp-fallback-test="window.jQuery">
    </script>
</environment>
```

<span data-ttu-id="140c6-219">태그 도우미 HTML 기반의 개발 환경 및 HTML 및 Razor 태그를 만들기 위한 다양 한 IntelliSense 환경을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="140c6-219">Tag Helpers provide an HTML-friendly development experience and a rich IntelliSense environment for creating HTML and Razor markup.</span></span> <span data-ttu-id="140c6-220">대부분의 기본 제공 태그 도우미 기존 HTML 요소를 대상 하 고 요소에 대 한 서버 쪽 특성을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="140c6-220">Most of the built-in Tag Helpers target existing HTML elements and provide server-side attributes for the element.</span></span>

### <a name="view-components"></a><span data-ttu-id="140c6-221">구성 요소 보기</span><span class="sxs-lookup"><span data-stu-id="140c6-221">View Components</span></span>

<span data-ttu-id="140c6-222">[구성 요소 확인](views/view-components.md) 렌더링 논리를 패키지 하 고 응용 프로그램에 걸쳐 다시 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="140c6-222">[View Components](views/view-components.md) allow you to package rendering logic and reuse it throughout the application.</span></span> <span data-ttu-id="140c6-223">유사 [부분 뷰](views/partial.md), 서로 관련된 논리입니다.</span><span class="sxs-lookup"><span data-stu-id="140c6-223">They're similar to [partial views](views/partial.md), but with associated logic.</span></span>
