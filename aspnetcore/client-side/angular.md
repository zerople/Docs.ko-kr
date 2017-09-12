---
title: "AngularJS를 사용 하 여 단일 페이지 응용 프로그램 (SPAs)에 대 한"
author: rick-anderson
description: "AngularJS를 사용 하 여 SPA 스타일 ASP.NET 응용 프로그램을 빌드하는 방법에 알아봅니다"
keywords: "ASP.NET Core, 예: AngularJS, SPA"
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 4b30576b-2718-4c39-9253-a59966747893
ms.technology: aspnet
ms.prod: asp.net-core
uid: client-side/angular
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2c7929976f0c9f8284ab397b1a87d576bcdd15b0
ms.sourcegitcommit: 9cdbfd0d670d70b9c354216aabee260c52dad5ee
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/12/2017
---
# <a name="using-angularjs-for-single-page-applications-spas-with-aspnet-core"></a><span data-ttu-id="f7bfa-104">AngularJS를 사용 하 여 ASP.NET Core 있는 단일 페이지 응용 프로그램 (SPAs)</span><span class="sxs-lookup"><span data-stu-id="f7bfa-104">Using AngularJS for Single Page Applications (SPAs) with ASP.NET Core</span></span>


<span data-ttu-id="f7bfa-105">여 [Venkata Koppaka](https://blog.falafel.com/falafel-software-recognized-sitefinity-website-year/) 및 [Scott Addie](https://scottaddie.com)</span><span class="sxs-lookup"><span data-stu-id="f7bfa-105">By [Venkata Koppaka](https://blog.falafel.com/falafel-software-recognized-sitefinity-website-year/) and [Scott Addie](https://scottaddie.com)</span></span>

<span data-ttu-id="f7bfa-106">이 문서에서는 AngularJS를 사용 하 여 SPA 스타일 ASP.NET 응용 프로그램을 빌드하는 방법에 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-106">In this article, you will learn how to build a SPA-style ASP.NET application using AngularJS.</span></span>

[<span data-ttu-id="f7bfa-107">샘플 코드 보기 또는 다운로드</span><span class="sxs-lookup"><span data-stu-id="f7bfa-107">View or download sample code</span></span>](https://github.com/aspnet/Docs/tree/master/aspnetcore/client-side/angular/sample)

## <a name="what-is-angularjs"></a><span data-ttu-id="f7bfa-108">AngularJS 란?</span><span class="sxs-lookup"><span data-stu-id="f7bfa-108">What is AngularJS?</span></span>

<span data-ttu-id="f7bfa-109">[AngularJS](https://angularjs.org/) 는 일반적으로 단일 페이지 응용 프로그램 (SPAs) 작업에 사용 되는 Google에서 최신 JavaScript 프레임 워크입니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-109">[AngularJS](https://angularjs.org/) is a modern JavaScript framework from Google commonly used to work with Single Page Applications (SPAs).</span></span> <span data-ttu-id="f7bfa-110">AngularJS는 MIT 라이선스에 따라을 기반으로 개방형이 고 AngularJS의 개발 진행 상황을 줄 수 [해당 GitHub 리포지토리에](https://github.com/angular/angular.js)합니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-110">AngularJS is open sourced under MIT license, and the development progress of AngularJS can be followed on [its GitHub repository](https://github.com/angular/angular.js).</span></span> <span data-ttu-id="f7bfa-111">라이브러리는 HTML 각도 모양의 대괄호를 사용 하기 때문에 각 라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-111">The library is called Angular because HTML uses angular-shaped brackets.</span></span>

<span data-ttu-id="f7bfa-112">AngularJS jQuery, 같은 DOM 조작 라이브러리가 아닙니다. 하지만 jQuery jQLite 호출의 일부를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-112">AngularJS is not a DOM manipulation library like jQuery, but it uses a subset of jQuery called jQLite.</span></span> <span data-ttu-id="f7bfa-113">AngularJS은 주로 HTML 태그에 추가할 수 있는 선언적 HTML 특성 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-113">AngularJS is primarily based on declarative HTML attributes that you can add to your HTML tags.</span></span> <span data-ttu-id="f7bfa-114">AngularJS를 사용 하 여 브라우저에서 볼 수 있습니다는 [코드 학교 웹 사이트](https://www.codeschool.com/courses/shaping-up-with-angularjs) 또는 [W3Schools 웹 사이트](https://www.w3schools.com/angular/)합니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-114">You can try AngularJS in your browser using the [Code School website](https://www.codeschool.com/courses/shaping-up-with-angularjs) or  [W3Schools website](https://www.w3schools.com/angular/).</span></span>

<span data-ttu-id="f7bfa-115">이 문서에서는 AngularJS 각 머리글은 대 한 몇 가지 메모와 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-115">This article focuses on AngularJS with some notes on where Angular is heading.</span></span>

## <a name="getting-started"></a><span data-ttu-id="f7bfa-116">시작</span><span class="sxs-lookup"><span data-stu-id="f7bfa-116">Getting started</span></span>

<span data-ttu-id="f7bfa-117">AngularJS를 사용 하 여 ASP.NET 응용 프로그램에서 시작 하려면 프로젝트의 일부로 설치 하거나 (CDN) 콘텐츠 배달 네트워크에서 참조 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-117">To start using AngularJS in your ASP.NET application, you must either install it as part of your project, or reference it from a content delivery network (CDN).</span></span>

### <a name="installation"></a><span data-ttu-id="f7bfa-118">설치</span><span class="sxs-lookup"><span data-stu-id="f7bfa-118">Installation</span></span>

<span data-ttu-id="f7bfa-119">AngularJS 응용 프로그램에 추가 하는 방법은 여러 가지가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-119">There are several ways to add AngularJS to your application.</span></span> <span data-ttu-id="f7bfa-120">Visual Studio에서 새 ASP.NET Core 웹 응용 프로그램 시작 하는 경우에 기본 제공을 사용 하 여 AngularJS을 추가할 수 있습니다 [Bower](bower.md) 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-120">If you’re starting a new ASP.NET Core web application in Visual Studio, you can add AngularJS using the built-in [Bower](bower.md) support.</span></span> <span data-ttu-id="f7bfa-121">열기 *bower.json*, 항목을 추가 하 고는 `dependencies` 속성:</span><span class="sxs-lookup"><span data-stu-id="f7bfa-121">Open *bower.json*, and add an entry to the `dependencies` property:</span></span>

<a name=angular-bower-json></a>

<span data-ttu-id="f7bfa-122">[!code-json[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/bower.json?highlight=9)]</span><span class="sxs-lookup"><span data-stu-id="f7bfa-122">[!code-json[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/bower.json?highlight=9)]</span></span>

<span data-ttu-id="f7bfa-123">저장 중에 *bower.json* 를 프로젝트의 각 파일을 설치할 *wwwroot/lib* 폴더입니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-123">Upon saving the *bower.json* file, Angular will be installed in your project's *wwwroot/lib* folder.</span></span> <span data-ttu-id="f7bfa-124">또한 내에서 표시 됩니다는 `Dependencies/Bower` 폴더입니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-124">Additionally, it will be listed within the `Dependencies/Bower` folder.</span></span> <span data-ttu-id="f7bfa-125">아래 스크린샷을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-125">See the screenshot below.</span></span>

![AngularJS 프로젝트와 솔루션 탐색기](angular/_static/angular-solution-explorer.png)

<span data-ttu-id="f7bfa-127">다음에 추가 `<script>` 의 맨 아래에 대 한 참조는 `<body>` HTML 페이지의 섹션 또는 *_Layout.cshtml* 파일을 다음과 같이 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-127">Next, add a `<script>` reference to the bottom of the `<body>` section of your HTML page or *_Layout.cshtml* file, as shown here:</span></span>

<span data-ttu-id="f7bfa-128">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Shared/_Layout.cshtml?highlight=4&range=48-52)]</span><span class="sxs-lookup"><span data-stu-id="f7bfa-128">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Shared/_Layout.cshtml?highlight=4&range=48-52)]</span></span>

<span data-ttu-id="f7bfa-129">프로덕션 응용 프로그램 AngularJS과 같은 공용 라이브러리에 Cdn을 사용 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-129">It's recommended that production applications utilize CDNs for common libraries like AngularJS.</span></span> <span data-ttu-id="f7bfa-130">이 이와 같은 여러 가지 Cdn 중 하나에서 AngularJS를 참조할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-130">You can reference AngularJS from one of several CDNs, such as this one:</span></span>

<span data-ttu-id="f7bfa-131">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Shared/_Layout.cshtml?highlight=10&range=53-67)]</span><span class="sxs-lookup"><span data-stu-id="f7bfa-131">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Shared/_Layout.cshtml?highlight=10&range=53-67)]</span></span>

<span data-ttu-id="f7bfa-132">에 대 한 참조가 있으면는 *angular.js* 스크립트 파일을 준비가 AngularJS를 사용 하 여 웹 페이지에서 시작 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-132">Once you have a reference to the *angular.js* script file, you're ready to begin using AngularJS in your web pages.</span></span>

## <a name="key-components"></a><span data-ttu-id="f7bfa-133">주요 구성 요소</span><span class="sxs-lookup"><span data-stu-id="f7bfa-133">Key components</span></span>

<span data-ttu-id="f7bfa-134">AngularJS와 같은 다양 한 주요 구성 요소에 포함 *지시문*, *템플릿*, *반복기*, *모듈*, * 컨트롤러*, *구성 요소*, *구성 요소 라우터* 등입니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-134">AngularJS includes a number of major components, such as *directives*, *templates*, *repeaters*, *modules*, *controllers*, *components*, *component router* and more.</span></span> <span data-ttu-id="f7bfa-135">이러한 구성 요소가 함께 작동 하 여 웹 페이지에 동작을 추가 하는 방법을 살펴보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-135">Let's examine how these components work together to add behavior to your web pages.</span></span>

### <a name="directives"></a><span data-ttu-id="f7bfa-136">지시문</span><span class="sxs-lookup"><span data-stu-id="f7bfa-136">Directives</span></span>

<span data-ttu-id="f7bfa-137">AngularJS를 사용 하 여 [지시문](https://docs.angularjs.org/guide/directive) 사용자 지정 특성 및 요소가 함께 HTML을 확장 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-137">AngularJS uses [directives](https://docs.angularjs.org/guide/directive) to extend HTML with custom attributes and elements.</span></span> <span data-ttu-id="f7bfa-138">AngularJS 지시문을 통해 정의 된 `data-ng-*` 또는 `ng-*` 접두사 (`ng` 짧은 각도).</span><span class="sxs-lookup"><span data-stu-id="f7bfa-138">AngularJS directives are defined via `data-ng-*` or `ng-*` prefixes (`ng` is short for angular).</span></span> <span data-ttu-id="f7bfa-139">AngularJS 지시문의는 다음과 같은 두 종류가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-139">There are two types of AngularJS directives:</span></span>

   1. <span data-ttu-id="f7bfa-140">**기본 지시문**: 이러한 미리 정의 된 각 팀 및 AngularJS 프레임 워크의 일부가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-140">**Primitive Directives**: These are predefined by the Angular team and are part of the AngularJS framework.</span></span>

   2. <span data-ttu-id="f7bfa-141">**사용자 지정 지시문**: 정의할 수 있는 사용자 지정 지시문 이들은 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-141">**Custom Directives**: These are custom directives that you can define.</span></span>

<span data-ttu-id="f7bfa-142">모든 AngularJS 응용 프로그램에서 사용 되는 기본 지시문 중 하나는 `ng-app` 지시어가 AngularJS 응용 프로그램 시작 되도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-142">One of the primitive directives used in all AngularJS applications is the `ng-app` directive, which bootstraps the AngularJS application.</span></span> <span data-ttu-id="f7bfa-143">이 지시문에 적용할 수는 `<body>` 태그 또는 본문의 자식 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-143">This directive can be applied to the `<body>` tag or to a child element of the body.</span></span> <span data-ttu-id="f7bfa-144">작업의 예제를 살펴보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-144">Let's see an example in action.</span></span> <span data-ttu-id="f7bfa-145">ASP.NET 프로젝트에 있을 경우 추가 하거나 HTML 파일에는 `wwwroot` 폴더 또는 새 컨트롤러 동작 및 관련 된 보기를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-145">Assuming you're in an ASP.NET project, you can either add an HTML file to the `wwwroot` folder, or add a new controller action and an associated view.</span></span> <span data-ttu-id="f7bfa-146">이 예에서 추가한 새 `Directives` 작업 메서드를 `HomeController.cs`합니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-146">In this case, I've added a new `Directives` action method to `HomeController.cs`.</span></span> <span data-ttu-id="f7bfa-147">관련된 보기에는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-147">The associated view is shown here:</span></span>

<span data-ttu-id="f7bfa-148">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Home/Directives.cshtml?highlight=5,7)]</span><span class="sxs-lookup"><span data-stu-id="f7bfa-148">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Home/Directives.cshtml?highlight=5,7)]</span></span>

<span data-ttu-id="f7bfa-149">이 샘플을 서로 독립적으로 유지 하기 위해 공유 레이아웃 파일을 사용 하지 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-149">To keep these samples independent of one another, I'm not using the shared layout file.</span></span> <span data-ttu-id="f7bfa-150">와 body 태그를 데코레이팅 것을 볼 수 있습니다는 `ng-app` 는 AngularJS 응용 프로그램 지시문이이 페이지를 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-150">You can see that we decorated the body tag with the `ng-app` directive to indicate this page is an AngularJS application.</span></span> <span data-ttu-id="f7bfa-151">`{{2+2}}` 배울 수 있는 더에 대 한 잠시 후에 각 데이터 바인딩 식입니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-151">The `{{2+2}}` is an Angular data binding expression that you will learn more about in a moment.</span></span> <span data-ttu-id="f7bfa-152">이 응용 프로그램을 실행 하는 경우 결과 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-152">Here is the result if you run this application:</span></span>

![간단한 각도 지시문](angular/_static/simple-directive.png)

<span data-ttu-id="f7bfa-154">다른 기본 AngularJS 지시문은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-154">Other primitive directives in AngularJS include:</span></span>

<span data-ttu-id="f7bfa-155">`ng-controller`어떤 보기에는 JavaScript 컨트롤러 연결을 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-155">`ng-controller` Determines which JavaScript controller is bound to which view.</span></span>

<span data-ttu-id="f7bfa-156">`ng-model`HTML 요소 속성의 값을 바인딩할 모델을 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-156">`ng-model` Determines the model to which the values of an HTML element's properties are bound.</span></span>

<span data-ttu-id="f7bfa-157">`ng-init`현재 범위에 대 한 식 형식에서 응용 프로그램 데이터를 초기화 하는 데 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-157">`ng-init` Used to initialize the application data in the form of an expression for the current scope.</span></span>

<span data-ttu-id="f7bfa-158">`ng-if`제거 하거나 제공 된 식이 truthiness에 따라 DOM에 지정된 된 HTML 요소를 다시 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-158">`ng-if` Removes or recreates the given HTML element in the DOM based on the truthiness of the expression provided.</span></span>

<span data-ttu-id="f7bfa-159">`ng-repeat`데이터 집합에 대해 HTML 블록을 반복합니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-159">`ng-repeat` Repeats a given block of HTML over a set of data.</span></span>

<span data-ttu-id="f7bfa-160">`ng-show`표시 하거나 제공 된 식에 따라 지정된 된 HTML 요소를 숨깁니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-160">`ng-show` Shows or hides the given HTML element based on the expression provided.</span></span>

<span data-ttu-id="f7bfa-161">AngularJS에서 지원 되는 모든 기본 지시문의 전체 목록은을 참조 하십시오는 [AngularJS 설명서 웹 사이트에서 지시문 설명서 섹션](https://docs.angularjs.org/api/ng/directive)합니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-161">For a full list of all primitive directives supported in AngularJS, please refer to the [directive documentation section on the AngularJS documentation website](https://docs.angularjs.org/api/ng/directive).</span></span>

### <a name="data-binding"></a><span data-ttu-id="f7bfa-162">데이터 바인딩</span><span class="sxs-lookup"><span data-stu-id="f7bfa-162">Data binding</span></span>

<span data-ttu-id="f7bfa-163">AngularJS 제공 [데이터 바인딩](https://docs.angularjs.org/guide/databinding) 지원의-즉시 하나를 사용 하는 `ng-bind` 지시문 또는 바인딩 식 구문와 같은 데이터 `{{expression}}`합니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-163">AngularJS provides [data binding](https://docs.angularjs.org/guide/databinding) support out-of-the-box using either the `ng-bind` directive or a data binding expression syntax such as `{{expression}}`.</span></span> <span data-ttu-id="f7bfa-164">AngularJS 항상 뷰 템플릿 사용 하 여 동기화에 모델의 데이터를에서 보관할 양방향 데이터 바인딩을 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-164">AngularJS supports two-way data binding where data from a model is kept in synchronization with a view template at all times.</span></span> <span data-ttu-id="f7bfa-165">보기에 변경 내용을 모델에 자동으로 반영 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-165">Any changes to the view are automatically reflected in the model.</span></span> <span data-ttu-id="f7bfa-166">마찬가지로, 보기에는 모델의 모든 변경 내용은 반영 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-166">Likewise, any changes in the model are reflected in the view.</span></span>

<span data-ttu-id="f7bfa-167">HTML 파일 또는 컨트롤러 작업이 라는 해당 보기와 만들기 `Databinding`합니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-167">Create either an HTML file or a controller action with an accompanying view named `Databinding`.</span></span> <span data-ttu-id="f7bfa-168">보기에서 다음을 포함:</span><span class="sxs-lookup"><span data-stu-id="f7bfa-168">Include the following in the view:</span></span>

<span data-ttu-id="f7bfa-169">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Home/Databinding.cshtml?highlight=8,9,10)]</span><span class="sxs-lookup"><span data-stu-id="f7bfa-169">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Home/Databinding.cshtml?highlight=8,9,10)]</span></span>

<span data-ttu-id="f7bfa-170">공지 지시문 또는 데이터 바인딩을 사용 하 여 모델 값을 표시할 수 있습니다 (`ng-bind`).</span><span class="sxs-lookup"><span data-stu-id="f7bfa-170">Notice that you can display model values using either directives or data binding (`ng-bind`).</span></span> <span data-ttu-id="f7bfa-171">결과 페이지는 다음과 같이 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-171">The resulting page should look like this:</span></span>

![단순 데이터 바인딩](angular/_static/simple-databinding.png)

### <a name="templates"></a><span data-ttu-id="f7bfa-173">템플릿</span><span class="sxs-lookup"><span data-stu-id="f7bfa-173">Templates</span></span>

<span data-ttu-id="f7bfa-174">[템플릿](https://docs.angularjs.org/guide/templates) AngularJS에은 셨 겠지만 HTML 페이지로 데코레이팅 AngularJS 지시문 및 아티팩트입니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-174">[Templates](https://docs.angularjs.org/guide/templates) in AngularJS are just plain HTML pages decorated with AngularJS directives and artifacts.</span></span> <span data-ttu-id="f7bfa-175">AngularJS에서 서식 파일은 지시문, 식, 필터 및 보기를 구성 하기 위해 HTML와 결합 하는 컨트롤의 혼합 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-175">A template in AngularJS is a mixture of directives, expressions, filters, and controls that combine with HTML to form the view.</span></span>

<span data-ttu-id="f7bfa-176">서식 파일을 설명 하기 위해 다른 보기를 추가 하 고 다음 것을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-176">Add another view to demonstrate templates, and add the following to it:</span></span>

<span data-ttu-id="f7bfa-177">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Home/Templates.cshtml?highlight=8,9,10)]</span><span class="sxs-lookup"><span data-stu-id="f7bfa-177">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Home/Templates.cshtml?highlight=8,9,10)]</span></span>

<span data-ttu-id="f7bfa-178">서식 파일에 같은 AngularJS 지시문 `ng-app`, `ng-init`, `ng-model` 및 데이터 바인딩 식 구문에 바인딩하는 `personName` 속성입니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-178">The template has AngularJS directives like `ng-app`, `ng-init`, `ng-model` and data binding expression syntax to bind the `personName` property.</span></span> <span data-ttu-id="f7bfa-179">아래 스크린샷에서 같이 보기 표시 브라우저에서 실행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-179">Running in the browser, the view looks like the screenshot below:</span></span>

![단순 템플릿 예 1](angular/_static/simple-templates-1.png)

<span data-ttu-id="f7bfa-181">입력된 필드 옆에 있는 텍스트를 동적으로 표시 됩니다 입력된 필드에 입력 하 여 이름을 변경한 경우 각도 양방향 데이터 바인딩을 동작을 보여 주는 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-181">If you change the name by typing in the input field, you will see the text next to the input field dynamically update, showing Angular two-way data binding in action.</span></span>

![단순 템플릿 예제 2](angular/_static/simple-templates-2.png)

### <a name="expressions"></a><span data-ttu-id="f7bfa-183">식</span><span class="sxs-lookup"><span data-stu-id="f7bfa-183">Expressions</span></span>

<span data-ttu-id="f7bfa-184">[식](https://docs.angularjs.org/guide/expression) AngularJS JavaScript와 유사한 코드 조각 내에 기록 되는 `{{ expression }}` 구문입니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-184">[Expressions](https://docs.angularjs.org/guide/expression) in AngularJS are JavaScript-like code snippets that are written inside the `{{ expression }}` syntax.</span></span> <span data-ttu-id="f7bfa-185">이 식의 데이터에 바인딩된 HTML과 마찬가지로 `ng-bind` 지시문입니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-185">The data from these expressions is bound to HTML the same way as `ng-bind` directives.</span></span> <span data-ttu-id="f7bfa-186">AngularJS 식 및 JavaScript 정규식 간의 주요 차이점은 식에 대해 평가 되는 AngularJS는 `$scope` AngularJS의 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-186">The main difference between AngularJS expressions and regular JavaScript expressions is that AngularJS expressions are evaluated against the `$scope` object in AngularJS.</span></span>

<span data-ttu-id="f7bfa-187">Bind 아래 샘플에서 AngularJS 식 `personName` 간단한 JavaScript 식 계산:</span><span class="sxs-lookup"><span data-stu-id="f7bfa-187">The AngularJS expressions in the sample below bind `personName` and a simple JavaScript calculated expression:</span></span>

<span data-ttu-id="f7bfa-188">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Home/Expressions.cshtml?highlight=8,9,10)]</span><span class="sxs-lookup"><span data-stu-id="f7bfa-188">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Home/Expressions.cshtml?highlight=8,9,10)]</span></span>

<span data-ttu-id="f7bfa-189">이 예제에서는 표시 되는 경우 브라우저에서 실행 되는 `personName` 데이터 및 계산의 결과:</span><span class="sxs-lookup"><span data-stu-id="f7bfa-189">The example running in the browser displays the `personName` data and the results of the calculation:</span></span>

![단순 식](angular/_static/simple-expressions.png)

### <a name="repeaters"></a><span data-ttu-id="f7bfa-191">반복기</span><span class="sxs-lookup"><span data-stu-id="f7bfa-191">Repeaters</span></span>

<span data-ttu-id="f7bfa-192">호출 하는 기본 지시문을 통해 수행 됩니다 AngularJS에 반복 `ng-repeat`합니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-192">Repeating in AngularJS is done via a primitive directive called `ng-repeat`.</span></span> <span data-ttu-id="f7bfa-193">`ng-repeat` 지시문 반복 데이터 배열의 길이 대해이 보기에서 지정된 된 HTML 요소를 반복 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-193">The `ng-repeat` directive repeats a given HTML element in a view over the length of a repeating data array.</span></span> <span data-ttu-id="f7bfa-194">AngularJS에서 반복기 문자열 또는 개체의 배열에 대해 반복할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-194">Repeaters in AngularJS can repeat over an array of strings or objects.</span></span> <span data-ttu-id="f7bfa-195">문자열 배열을 통해 반복 되는 샘플 사용 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-195">Here is a sample usage of repeating over an array of strings:</span></span>

<span data-ttu-id="f7bfa-196">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Home/Repeaters.cshtml?highlight=8,10,11)]</span><span class="sxs-lookup"><span data-stu-id="f7bfa-196">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Home/Repeaters.cshtml?highlight=8,10,11)]</span></span>

<span data-ttu-id="f7bfa-197">[repeat 지시문](https://docs.angularjs.org/api/ng/directive/ngRepeat) 이 스크린 샷에 표시 된 개발자 도구에서 볼 수 있듯이 일련의 순서가 없는 목록에 목록 항목을 출력 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-197">The [repeat directive](https://docs.angularjs.org/api/ng/directive/ngRepeat) outputs a series of list items in an unordered list, as you can see in the developer tools shown in this screenshot:</span></span>

![반복기 예제](angular/_static/repeater.png)

<span data-ttu-id="f7bfa-199">개체의 배열을 통해 반복 하는 예제는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-199">Here is an example that repeats over an array of objects.</span></span> <span data-ttu-id="f7bfa-200">`ng-init` 지시문 설정는 `names` 배열에 있는 각 요소 먼저 포함 된 개체 이름과 성을 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-200">The `ng-init` directive establishes a `names` array, where each element is an object containing first and last names.</span></span> <span data-ttu-id="f7bfa-201">`ng-repeat` 할당 `name in names`, 모든 배열 요소에 대해 목록 항목을 출력 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-201">The `ng-repeat` assignment, `name in names`, outputs a list item for every array element.</span></span>

<span data-ttu-id="f7bfa-202">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Home/Repeaters2.cshtml?highlight=8,9,10,11,13,14)]</span><span class="sxs-lookup"><span data-stu-id="f7bfa-202">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Home/Repeaters2.cshtml?highlight=8,9,10,11,13,14)]</span></span>

<span data-ttu-id="f7bfa-203">이 경우의 출력은 앞의 예와 동일 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-203">The output in this case is the same as in the previous example.</span></span>

<span data-ttu-id="f7bfa-204">각 루프를 실행 중에 위치에 따라 동작을 제공 하는 데 도움이 되는 일부 추가 지시문을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-204">Angular provides some additional directives that can help provide behavior based on where the loop is in its execution.</span></span>

`$index`

<span data-ttu-id="f7bfa-205">사용 하 여 `$index` 에 `ng-repeat` 인덱스 루프를 현재 위치를 결정 하기 위해 루프 켜져 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-205">Use `$index` in the `ng-repeat` loop to determine which index position your loop currently is on.</span></span>

<span data-ttu-id="f7bfa-206">`$even` 및 `$odd`</span><span class="sxs-lookup"><span data-stu-id="f7bfa-206">`$even` and `$odd`</span></span>

<span data-ttu-id="f7bfa-207">사용 하 여 `$even` 에 `ng-repeat` 루프 루프에서 현재 인덱스에도 인덱싱된 행 인지 여부를 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-207">Use `$even` in the `ng-repeat` loop to determine whether the current index in your loop is an even indexed row.</span></span> <span data-ttu-id="f7bfa-208">마찬가지로 사용 하 여 `$odd` 홀수 인덱스 행을 현재 인덱스 인지 확인 하려면.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-208">Similarly, use `$odd` to determine if the current index is an odd indexed row.</span></span>

<span data-ttu-id="f7bfa-209">`$first` 및 `$last`</span><span class="sxs-lookup"><span data-stu-id="f7bfa-209">`$first` and `$last`</span></span>

<span data-ttu-id="f7bfa-210">사용 하 여 `$first` 에 `ng-repeat` 루프 루프에서 현재 인덱스의 첫 번째 행 인지 여부를 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-210">Use `$first` in the `ng-repeat` loop to determine whether the current index in your loop is the first row.</span></span> <span data-ttu-id="f7bfa-211">마찬가지로 사용 하 여 `$last` 현재 인덱스의 마지막 행 인지 확인 하려면.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-211">Similarly, use `$last` to determine if the current index is the last row.</span></span>

<span data-ttu-id="f7bfa-212">다음은 보여 주는 샘플 `$index`, `$even`, `$odd`, `$first`, 및 `$last` 실행에서:</span><span class="sxs-lookup"><span data-stu-id="f7bfa-212">Below is a sample that shows `$index`, `$even`, `$odd`, `$first`, and `$last` in action:</span></span>

<span data-ttu-id="f7bfa-213">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Home/Repeaters3.cshtml?highlight=14,15,16,17,18)]</span><span class="sxs-lookup"><span data-stu-id="f7bfa-213">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Home/Repeaters3.cshtml?highlight=14,15,16,17,18)]</span></span>

<span data-ttu-id="f7bfa-214">출력 결과 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-214">Here is the resulting output:</span></span>

![반복기 예제 2](angular/_static/repeaters2.png)

### <a name="scope"></a><span data-ttu-id="f7bfa-216">$scope</span><span class="sxs-lookup"><span data-stu-id="f7bfa-216">$scope</span></span>

<span data-ttu-id="f7bfa-217">`$scope`역할을 연결 합니다 (아래에 설명) 컨트롤러 및 보기 (템플릿)을 하는 JavaScript 개체가입니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-217">`$scope` is a JavaScript object that acts as glue between the view (template) and the controller (explained below).</span></span> <span data-ttu-id="f7bfa-218">AngularJS에서 보기 서식 파일에 연결 된 값만 인식는 `$scope` 컨트롤러에는 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-218">A view template in AngularJS only knows about the values attached to the `$scope` object in the controller.</span></span>

> [!NOTE]
> <span data-ttu-id="f7bfa-219">MVVM 전 세계에서의 `$scope` AngularJS의 개체는 종종 ViewModel로 정의 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-219">In the MVVM world, the `$scope` object in AngularJS is often defined as the ViewModel.</span></span> <span data-ttu-id="f7bfa-220">AngularJS 팀 참조 하는 `$scope` 데이터 모델로 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-220">The AngularJS team refers to the `$scope` object as the Data-Model.</span></span> <span data-ttu-id="f7bfa-221">[AngularJS의 범위에 대 한 자세한 정보](https://docs.angularjs.org/guide/scope)합니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-221">[Learn more about Scopes in AngularJS](https://docs.angularjs.org/guide/scope).</span></span>

<span data-ttu-id="f7bfa-222">다음 속성을 설정 하는 방법을 보여 주는 간단한 예를은 `$scope` 별도 JavaScript 파일 *scope.js*:</span><span class="sxs-lookup"><span data-stu-id="f7bfa-222">Below is a simple example showing how to set properties on `$scope` within a separate JavaScript file, *scope.js*:</span></span>

<span data-ttu-id="f7bfa-223">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/scope.js?highlight=2,3)]</span><span class="sxs-lookup"><span data-stu-id="f7bfa-223">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/scope.js?highlight=2,3)]</span></span>

<span data-ttu-id="f7bfa-224">관찰 된 `$scope` 매개 변수는 2 번 줄에는 컨트롤러에 전달 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-224">Observe the `$scope` parameter passed to the controller on line 2.</span></span> <span data-ttu-id="f7bfa-225">이 개체에 대해 알고 보기는.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-225">This object is what the view knows about.</span></span> <span data-ttu-id="f7bfa-226">3 줄에 "Mary Jane"을 "name" 이라는 속성이 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-226">On line 3, we are setting a property called "name" to "Mary Jane".</span></span>

<span data-ttu-id="f7bfa-227">특정 속성은 보기에서 찾을 수 없는 경우 어떻게 되나요?</span><span class="sxs-lookup"><span data-stu-id="f7bfa-227">What happens when a particular property is not found by the view?</span></span> <span data-ttu-id="f7bfa-228">아래에 정의 된 보기 "name" 및 "age" 속성은 참조 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-228">The view defined below refers to "name" and "age" properties:</span></span>

<span data-ttu-id="f7bfa-229">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Home/Scope.cshtml?highlight=9,10,14)]</span><span class="sxs-lookup"><span data-stu-id="f7bfa-229">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Home/Scope.cshtml?highlight=9,10,14)]</span></span>

<span data-ttu-id="f7bfa-230">줄 9 식 구문을 사용 하 여 "name" 속성을 표시 하는 각 요청 하는 것에 주의 하십시오.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-230">Notice on line 9 that we are asking Angular to show the "name" property using expression syntax.</span></span> <span data-ttu-id="f7bfa-231">다음 줄 10 "age", 존재 하지 않는 속성 참조 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-231">Line 10 then refers to "age", a property that does not exist.</span></span> <span data-ttu-id="f7bfa-232">실행 중인 예제에는 나이 대 한 "Mary Jane" 및 nothing으로 설정 이름을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-232">The running example shows the name set to "Mary Jane" and nothing for age.</span></span> <span data-ttu-id="f7bfa-233">누락 된 속성은 무시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-233">Missing properties are ignored.</span></span>

![범위 예](angular/_static/scope.png)

### <a name="modules"></a><span data-ttu-id="f7bfa-235">모듈</span><span class="sxs-lookup"><span data-stu-id="f7bfa-235">Modules</span></span>

<span data-ttu-id="f7bfa-236">A [모듈](https://docs.angularjs.org/guide/module) AngularJS 컨트롤러, 서비스, 지시문 등의 컬렉션이 있습니다. `angular.module()` 함수 호출은 생성, 등록 및에 AngularJS 모듈을 검색 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-236">A [module](https://docs.angularjs.org/guide/module) in AngularJS is a collection of controllers, services, directives, etc. The `angular.module()` function call is used to create, register, and retrieve modules in AngularJS.</span></span> <span data-ttu-id="f7bfa-237">사용 하 여 모든 모듈에 포함 하는 AngularJS 팀 및 타사 라이브러리에서 제공 하 여 등록 해야는 `angular.module()` 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-237">All modules, including those shipped by the AngularJS team and third party libraries, should be registered using the `angular.module()` function.</span></span>

<span data-ttu-id="f7bfa-238">다음은 AngularJS에 새 모듈을 만들어야 하는 방법을 보여 주는 코드 조각입니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-238">Below is a snippet of code that shows how to create a new module in AngularJS.</span></span> <span data-ttu-id="f7bfa-239">첫 번째 매개 변수는 모듈의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-239">The first parameter is the name of the module.</span></span> <span data-ttu-id="f7bfa-240">두 번째 매개 변수는 다른 모듈에 종속성을 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-240">The second parameter defines dependencies on other modules.</span></span> <span data-ttu-id="f7bfa-241">이 문서의 뒷부분에 나오는 우리가 보여 줄 이러한 종속성을 전달 하는 방법을 `angular.module()` 메서드를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-241">Later in this article, we will be showing how to pass these dependencies to an `angular.module()` method call.</span></span>

```javascript
var personApp = angular.module('personApp', []);
```

<span data-ttu-id="f7bfa-242">사용 하 여는 `ng-app` 지시문을 페이지에는 AngularJS 모듈을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-242">Use the `ng-app` directive to represent an AngularJS module on the page.</span></span> <span data-ttu-id="f7bfa-243">모듈을 사용 하려면 모듈의 이름을 지정 `personApp` 이 예제에 `ng-app` 우리의 템플릿에서 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-243">To use a module, assign the name of the module, `personApp` in this example, to the `ng-app` directive in our template.</span></span>

```html
<body ng-app="personApp">
```

### <a name="controllers"></a><span data-ttu-id="f7bfa-244">컨트롤러</span><span class="sxs-lookup"><span data-stu-id="f7bfa-244">Controllers</span></span>

<span data-ttu-id="f7bfa-245">[컨트롤러](https://docs.angularjs.org/guide/controller) AngularJS는 코드에 대 한 항목의 첫 번째 지점입니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-245">[Controllers](https://docs.angularjs.org/guide/controller) in AngularJS are the first point of entry for your code.</span></span> <span data-ttu-id="f7bfa-246">`<module name>.controller()` 함수 호출은 만들고에서 AngularJS 컨트롤러를 등록 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-246">The `<module name>.controller()` function call is used to create and register controllers in AngularJS.</span></span> <span data-ttu-id="f7bfa-247">`ng-controller` 지시문 HTML 페이지에는 AngularJS 컨트롤러를 나타내는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-247">The `ng-controller` directive is used to represent an AngularJS controller on the HTML page.</span></span> <span data-ttu-id="f7bfa-248">각 컨트롤러의 역할 상태 및 데이터 모델의 동작을 설정 하는 (`$scope`).</span><span class="sxs-lookup"><span data-stu-id="f7bfa-248">The role of the controller in Angular is to set state and behavior of the data model (`$scope`).</span></span> <span data-ttu-id="f7bfa-249">컨트롤러는 DOM를 직접 조작 하 쓰일 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-249">Controllers should not be used to manipulate the DOM directly.</span></span>

<span data-ttu-id="f7bfa-250">새 컨트롤러를 등록 하는 코드의 코드 조각은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-250">Below is a snippet of code that registers a new controller.</span></span> <span data-ttu-id="f7bfa-251">`personApp` 조각에서 변수가 참조 하는 각도 모듈 2 번 줄에 정의 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-251">The `personApp` variable in the snippet references an Angular module, which is defined on line 2.</span></span>

<span data-ttu-id="f7bfa-252">[!code-javascript[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/controllers.js?highlight=2,5)]</span><span class="sxs-lookup"><span data-stu-id="f7bfa-252">[!code-javascript[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/controllers.js?highlight=2,5)]</span></span>

<span data-ttu-id="f7bfa-253">사용 하 여 뷰는 `ng-controller` 지시문에는 컨트롤러 이름을 할당 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-253">The view using the `ng-controller` directive assigns the controller name:</span></span>

<span data-ttu-id="f7bfa-254">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Home/Controllers.cshtml?highlight=8,14)]</span><span class="sxs-lookup"><span data-stu-id="f7bfa-254">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Home/Controllers.cshtml?highlight=8,14)]</span></span>

<span data-ttu-id="f7bfa-255">에 해당 하는 "Jane" 및 "Mary"의 페이지에 표시는 `firstName` 및 `lastName` 에 연결 된 속성은 `$scope` 개체:</span><span class="sxs-lookup"><span data-stu-id="f7bfa-255">The page shows "Mary" and "Jane" that correspond to the `firstName` and `lastName` properties attached to the `$scope` object:</span></span>

![컨트롤러 예](angular/_static/controllers.png)

### <a name="components"></a><span data-ttu-id="f7bfa-257">구성 요소</span><span class="sxs-lookup"><span data-stu-id="f7bfa-257">Components</span></span>

<span data-ttu-id="f7bfa-258">[구성 요소](https://docs.angularjs.org/guide/component) 각 1.5. x는 캡슐화 및 개별 HTML 요소를 만드는 기능에 대 한 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-258">[Components](https://docs.angularjs.org/guide/component) in Angular 1.5.x allow for the encapsulation and capability of creating individual HTML elements.</span></span> <span data-ttu-id="f7bfa-259">각도 1.4.x.directive() 메서드를 사용 하 여 동일한 기능을 얻을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-259">In Angular 1.4.x you could achieve the same feature using the .directive() method.</span></span>

<span data-ttu-id="f7bfa-260">.Component() 메서드를 사용 하 여 개발 지시문 및 컨트롤러의 기능을 얻는 간소화 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-260">By using the .component() method, development is simplified gaining the functionality of the directive and the controller.</span></span> <span data-ttu-id="f7bfa-261">다른 이점은 다음과 같습니다. 범위 격리 모범 사례는 고유, 및 각도 2로 마이그레이션 훨씬 쉬워집니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-261">Other benefits include; scope isolation, best practices are inherent, and migration to Angular 2 becomes an easier task.</span></span> <span data-ttu-id="f7bfa-262">`<module name>.component()` 함수 호출은 만들고 AngularJS의 구성 요소를 등록 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-262">The `<module name>.component()` function call is used to create and register components in AngularJS.</span></span>

<span data-ttu-id="f7bfa-263">다음은 새 구성 요소를 등록 하는 코드 조각입니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-263">Below is a snippet of code that registers a new component.</span></span> <span data-ttu-id="f7bfa-264">`personApp` 조각에서 변수가 참조 하는 각도 모듈 2 번 줄에 정의 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-264">The `personApp` variable in the snippet references an Angular module, which is defined on line 2.</span></span>

<span data-ttu-id="f7bfa-265">[!code-javascript[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/components.js?highlight=2,5,13)]</span><span class="sxs-lookup"><span data-stu-id="f7bfa-265">[!code-javascript[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/components.js?highlight=2,5,13)]</span></span>

<span data-ttu-id="f7bfa-266">사용자 지정 HTML 요소를 표시 하에서는 보기입니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-266">The view where we are displaying the custom HTML element.</span></span>

<span data-ttu-id="f7bfa-267">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Home/Components.cshtml?highlight=8)]</span><span class="sxs-lookup"><span data-stu-id="f7bfa-267">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Home/Components.cshtml?highlight=8)]</span></span>

<span data-ttu-id="f7bfa-268">구성 요소에서 사용 하는 연결 된 템플릿:</span><span class="sxs-lookup"><span data-stu-id="f7bfa-268">The associated template used by component:</span></span>

<span data-ttu-id="f7bfa-269">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/partials/personcomponent.html?highlight=2,3)]</span><span class="sxs-lookup"><span data-stu-id="f7bfa-269">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/partials/personcomponent.html?highlight=2,3)]</span></span>

<span data-ttu-id="f7bfa-270">"Aftab" 및 "Ansari"에 해당 하는 페이지에 표시 된 `firstName` 및 `lastName` 에 연결 된 속성은 `vm` 개체:</span><span class="sxs-lookup"><span data-stu-id="f7bfa-270">The page shows "Aftab" and "Ansari" that correspond to the `firstName` and `lastName` properties attached to the `vm` object:</span></span>

![구성 요소 예제](angular/_static/components.png)

### <a name="services"></a><span data-ttu-id="f7bfa-272">서비스</span><span class="sxs-lookup"><span data-stu-id="f7bfa-272">Services</span></span>

<span data-ttu-id="f7bfa-273">[서비스](https://docs.angularjs.org/guide/services) AngularJS에 일반적으로 사용 되는 각 응용 프로그램의 수명 내내 사용할 수 있는 파일에 추출 된 하는 공유 코드에 대 한 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-273">[Services](https://docs.angularjs.org/guide/services) in AngularJS are commonly used for shared code that is abstracted away into a file which can be used throughout the lifetime of an Angular application.</span></span> <span data-ttu-id="f7bfa-274">서비스 지연 초기화는 없습니다 것 서비스의 인스턴스는 서비스에 의존 하는 구성 요소를 가져옵니다 사용 하지 않는 경우를 의미 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-274">Services are lazily instantiated, meaning that there will not be an instance of a service unless a component that depends on the service gets used.</span></span> <span data-ttu-id="f7bfa-275">팩터리는 AngularJS 응용 프로그램에서 사용 하는 서비스의 예입니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-275">Factories are an example of a service used in AngularJS applications.</span></span> <span data-ttu-id="f7bfa-276">팩터리를 사용 하 여 만들어집니다는 `myApp.factory()` 함수 호출, 여기서 `myApp` 은 모듈입니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-276">Factories are created using the `myApp.factory()` function call, where `myApp` is the module.</span></span>

<span data-ttu-id="f7bfa-277">다음은에서 AngularJS 팩터리를 사용 하는 방법을 보여 주는 예:</span><span class="sxs-lookup"><span data-stu-id="f7bfa-277">Below is an example that shows how to use factories in AngularJS:</span></span>

<span data-ttu-id="f7bfa-278">[!code-javascript[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/simpleFactory.js?highlight=1)]</span><span class="sxs-lookup"><span data-stu-id="f7bfa-278">[!code-javascript[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/simpleFactory.js?highlight=1)]</span></span>

<span data-ttu-id="f7bfa-279">이 팩터리가 컨트롤러에서를 호출 하려면 전달 `personFactory` 매개 변수로 `controller` 함수:</span><span class="sxs-lookup"><span data-stu-id="f7bfa-279">To call this factory from the controller, pass `personFactory` as a parameter to the `controller` function:</span></span>

```javascript
personApp.controller('personController', function($scope,personFactory) {
  $scope.name = personFactory.getName();
});
```

### <a name="using-services-to-talk-to-a-rest-endpoint"></a><span data-ttu-id="f7bfa-280">REST 끝점에 설명 하는 서비스를 사용 하 여</span><span class="sxs-lookup"><span data-stu-id="f7bfa-280">Using services to talk to a REST endpoint</span></span>

<span data-ttu-id="f7bfa-281">다음은 종단 간 예제에서에서 서비스 사용 AngularJS ASP.NET Core 웹 API 끝점 상호 작용할 수입니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-281">Below is an end-to-end example using services in AngularJS to interact with an ASP.NET Core Web API endpoint.</span></span> <span data-ttu-id="f7bfa-282">이 예제에서는 웹 API에서 데이터를 가져오고 보기 서식 파일의 데이터를 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-282">The example gets data from the Web API and displays the data in a view template.</span></span> <span data-ttu-id="f7bfa-283">부터 시작 하겠습니다 보기 먼저:</span><span class="sxs-lookup"><span data-stu-id="f7bfa-283">Let's start with the view first:</span></span>

<span data-ttu-id="f7bfa-284">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/People/Index.cshtml?highlight=5,8,10,17,18,19)]</span><span class="sxs-lookup"><span data-stu-id="f7bfa-284">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/People/Index.cshtml?highlight=5,8,10,17,18,19)]</span></span>

<span data-ttu-id="f7bfa-285">Angular 모듈 호출에이 보기에는 `PersonsApp` 컨트롤러를 호출 하 고 `personController`합니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-285">In this view, we have an Angular module called `PersonsApp` and a controller called `personController`.</span></span> <span data-ttu-id="f7bfa-286">사용 하 여 `ng-repeat` 사람 목록에 대 한 반복 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-286">We are using `ng-repeat` to iterate over the list of persons.</span></span> <span data-ttu-id="f7bfa-287">줄 17-19에 세 개의 사용자 지정 JavaScript 파일을 참조 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-287">We are referencing three custom JavaScript files on lines 17-19.</span></span>

<span data-ttu-id="f7bfa-288">*personApp.js* 파일 등록을 사용 하는 `PersonsApp` 모듈 및 구문을 이전 예제와 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-288">The *personApp.js* file is used to register the `PersonsApp` module; and, the syntax is similar to previous examples.</span></span> <span data-ttu-id="f7bfa-289">사용 하는 `angular.module` 여기서 작업 하는 모듈의 새 인스턴스를 만들 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-289">We are using the `angular.module` function to create a new instance of the module that we will be working with.</span></span>

<span data-ttu-id="f7bfa-290">[!code-javascript[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/personApp.js?highlight=3)]</span><span class="sxs-lookup"><span data-stu-id="f7bfa-290">[!code-javascript[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/personApp.js?highlight=3)]</span></span>

<span data-ttu-id="f7bfa-291">에 대해 살펴보겠습니다 *personFactory.js*아래, 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-291">Let's take a look at *personFactory.js*, below.</span></span> <span data-ttu-id="f7bfa-292">모듈의 호출 `factory` 메서드 팩터리를 만들어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-292">We are calling the module’s `factory` method to create a factory.</span></span> <span data-ttu-id="f7bfa-293">줄 12 표시 기본 제공 각 `$http` 서비스 웹 서비스에서 사용자 정보를 검색 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-293">Line 12 shows the built-in Angular `$http` service retrieving people information from a web service.</span></span>

<span data-ttu-id="f7bfa-294">[!code-javascript[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/personFactory.js?highlight=6,7,12)]</span><span class="sxs-lookup"><span data-stu-id="f7bfa-294">[!code-javascript[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/personFactory.js?highlight=6,7,12)]</span></span>

<span data-ttu-id="f7bfa-295">*personController.js*, 모듈의 호출 `controller` 컨트롤러를 만드는 메서드를 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-295">In *personController.js*, we are calling the module’s `controller` method to create the controller.</span></span> <span data-ttu-id="f7bfa-296">`$scope` 개체의 `people` 속성 personFactory (라인 13)에서 반환 된 데이터에 할당 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-296">The `$scope` object's `people` property is assigned the data returned from the personFactory (line 13).</span></span>

<span data-ttu-id="f7bfa-297">[!code-javascript[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/personController.js?highlight=6,7,13)]</span><span class="sxs-lookup"><span data-stu-id="f7bfa-297">[!code-javascript[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/personController.js?highlight=6,7,13)]</span></span>

<span data-ttu-id="f7bfa-298">웹 API와 그 뒤에 있는 모델에 대해 간략히 살펴보고를 보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-298">Let's take a quick look at the Web API and the model behind it.</span></span> <span data-ttu-id="f7bfa-299">`Person` 모델와 POCO (Plain Old CLR Object)은 `Id`, `FirstName`, 및 `LastName` 속성:</span><span class="sxs-lookup"><span data-stu-id="f7bfa-299">The `Person` model is a POCO (Plain Old CLR Object) with `Id`, `FirstName`, and `LastName` properties:</span></span>

<span data-ttu-id="f7bfa-300">[!code-csharp[Main](angular/sample/AngularJSSample/src/AngularJSSample/Models/Person.cs)]</span><span class="sxs-lookup"><span data-stu-id="f7bfa-300">[!code-csharp[Main](angular/sample/AngularJSSample/src/AngularJSSample/Models/Person.cs)]</span></span>

<span data-ttu-id="f7bfa-301">`Person` 컨트롤러의 JSON 형식 목록을 반환 `Person` 개체:</span><span class="sxs-lookup"><span data-stu-id="f7bfa-301">The `Person` controller returns a JSON-formatted list of `Person` objects:</span></span>

<span data-ttu-id="f7bfa-302">[!code-csharp[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Controllers/Api/PersonController.cs?highlight=9,10,19)]</span><span class="sxs-lookup"><span data-stu-id="f7bfa-302">[!code-csharp[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Controllers/Api/PersonController.cs?highlight=9,10,19)]</span></span>

<span data-ttu-id="f7bfa-303">응용 프로그램을에서 살펴보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-303">Let's see the application in action:</span></span>

![나머지 결과 표시 하는 컨트롤러](angular/_static/rest-bound.png)

<span data-ttu-id="f7bfa-305">있습니다 수 [GitHub에서 응용 프로그램의 구조 보기](https://github.com/aspnet/Docs/tree/master/aspnetcore/client-side/angular/sample)합니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-305">You can [view the application's structure on GitHub](https://github.com/aspnet/Docs/tree/master/aspnetcore/client-side/angular/sample).</span></span>

> [!NOTE]
> <span data-ttu-id="f7bfa-306">에 대 한 자세한 AngularJS 응용 프로그램을 구성을 참조 하십시오. [John 예 각도 스타일 가이드](https://github.com/johnpapa/angular-styleguide)</span><span class="sxs-lookup"><span data-stu-id="f7bfa-306">For more on structuring AngularJS applications, see [John Papa's Angular Style Guide](https://github.com/johnpapa/angular-styleguide)</span></span>

&nbsp;

> [!NOTE]
> <span data-ttu-id="f7bfa-307">AngularJS 모듈, 컨트롤러 팩터리를 만들려면 지시문 및 보기 파일 쉽게 반드시 확인 Sayed Hashimi 아웃 [Visual Studio에 대 한 서식 파일 팩 SideWaffle](http://sidewaffle.com/)합니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-307">To create AngularJS module, controller, factory, directive and view files easily, be sure to check out Sayed Hashimi's [SideWaffle template pack for Visual Studio](http://sidewaffle.com/).</span></span> <span data-ttu-id="f7bfa-308">Sayed Hashimi microsoft Visual Studio 웹 팀의 수석 프로그램 관리자 이며 SideWaffle 템플릿 "gold" 표준 것으로 간주 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-308">Sayed Hashimi is a Senior Program Manager on the Visual Studio Web Team at Microsoft and SideWaffle templates are considered the gold standard.</span></span> <span data-ttu-id="f7bfa-309">이 문서 작성 당시의 SideWaffle는 Visual Studio 2012, 2013 및 2015 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-309">At the time of this writing, SideWaffle is available for Visual Studio 2012, 2013, and 2015.</span></span>

### <a name="routing-and-multiple-views"></a><span data-ttu-id="f7bfa-310">라우팅 및 다중 뷰</span><span class="sxs-lookup"><span data-stu-id="f7bfa-310">Routing and multiple views</span></span>

<span data-ttu-id="f7bfa-311">AngularJS 기반 SPA (단일 페이지 응용 프로그램) 탐색을 처리 하는 기본 제공 경로 공급자를 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-311">AngularJS has a built-in route provider to handle SPA (Single Page Application) based navigation.</span></span> <span data-ttu-id="f7bfa-312">AngularJS 라우팅을 사용 하려면 추가 해야 합니다는 `angular-route` Bower를 사용 하 여 라이브러리입니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-312">To work with routing in AngularJS, you must add the `angular-route` library using Bower.</span></span> <span data-ttu-id="f7bfa-313">볼 수는 [bower.json](#angular-bower-json) 는 우리는 이미 참조 프로젝트에이 문서의 시작 부분에 참조 된 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-313">You can see in the [bower.json](#angular-bower-json) file referenced at the start of this article that we are already referencing it in our project.</span></span>

<span data-ttu-id="f7bfa-314">패키지를 설치 하면 추가 스크립트 참조 (*각도 route.js*) 보기에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-314">After you install the package, add the script reference (*angular-route.js*) to your view.</span></span>

<span data-ttu-id="f7bfa-315">이제 작성 된 하 고 탐색 하 여 추가 사용자 앱을 살펴보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-315">Now let's take the Person App we have been building and add navigation to it.</span></span> <span data-ttu-id="f7bfa-316">첫째, 해 드립니다 응용 프로그램의 복사본을 만들어 새 `PeopleController` 라는 동작 `Spa` 및 해당 `Spa.cshtml` Index.cshtml 보기에 복사 하 여 보기는 `People` 폴더입니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-316">First, we will make a copy of the app by creating a new `PeopleController` action called `Spa` and a corresponding `Spa.cshtml` view by copying the Index.cshtml view in the `People` folder.</span></span> <span data-ttu-id="f7bfa-317">에 대 한 스크립트 참조를 추가 `angular-route` (11 줄 참조).</span><span class="sxs-lookup"><span data-stu-id="f7bfa-317">Add a script reference to `angular-route` (see line 11).</span></span> <span data-ttu-id="f7bfa-318">추가적으로 `div` 으로 표시는 `ng-view` 지시문 (6 행 참조)를 뷰에 배치할 자리 표시자로 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-318">Also add a `div` marked with the `ng-view` directive (see line 6) as a placeholder to place views in.</span></span> <span data-ttu-id="f7bfa-319">일부 추가 사용을 *.js* 13-16으로 줄에 참조 되는 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-319">We are going to be using several additional *.js* files which are referenced on lines 13-16.</span></span>

<span data-ttu-id="f7bfa-320">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/People/Spa.cshtml?highlight=6,11,12,13,14,15,16)]</span><span class="sxs-lookup"><span data-stu-id="f7bfa-320">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/People/Spa.cshtml?highlight=6,11,12,13,14,15,16)]</span></span>

<span data-ttu-id="f7bfa-321">에 대해 살펴보겠습니다 *personModule.js* 파일 म 라우팅을 사용 하 여 모듈을 인스턴스화하는 방법을 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-321">Let's take a look at *personModule.js* file to see how we are instantiating the module with routing.</span></span> <span data-ttu-id="f7bfa-322">전달 `ngRoute` 모듈로 라이브러리로 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-322">We are passing `ngRoute` as a library into the module.</span></span> <span data-ttu-id="f7bfa-323">이 모듈 응용 프로그램의 라우팅을 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-323">This module handles routing in our application.</span></span>

<span data-ttu-id="f7bfa-324">[!code-javascript[Main](angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/personModule.js)]</span><span class="sxs-lookup"><span data-stu-id="f7bfa-324">[!code-javascript[Main](angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/personModule.js)]</span></span>

<span data-ttu-id="f7bfa-325">*personRoutes.js* 파일을 아래의 경로 공급자에 따라 경로 정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-325">The *personRoutes.js* file, below, defines routes based on the route provider.</span></span> <span data-ttu-id="f7bfa-326">줄 4-7 효과적으로 한다는 것으로 때 사용 하 여 URL 탐색 정의 `/persons` 은 호출 템플릿을 사용 하 여 요청 `partials/personlist` 통해 작업을 수행 하 여 `personListController`합니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-326">Lines 4-7 define navigation by effectively saying, when a URL with `/persons` is requested, use a template called `partials/personlist` by working through `personListController`.</span></span> <span data-ttu-id="f7bfa-327">8-11 선은 나타냅니다는 경로 매개 변수를 사용 하는 세부 정보 페이지 `personId`합니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-327">Lines 8-11 indicate a detail page with a route parameter of `personId`.</span></span> <span data-ttu-id="f7bfa-328">URL 패턴 중 하 나와 일치 하지 않습니다, 각 기본적으로 `/persons` 보기.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-328">If the URL doesn't match one of the patterns, Angular defaults to the `/persons` view.</span></span>

<span data-ttu-id="f7bfa-329">[!code-javascript[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/personRoutes.js?highlight=4,5,6,7,8,9,10,11,13)]</span><span class="sxs-lookup"><span data-stu-id="f7bfa-329">[!code-javascript[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/personRoutes.js?highlight=4,5,6,7,8,9,10,11,13)]</span></span>

<span data-ttu-id="f7bfa-330">`personlist.html` 파일은 사용자 목록을 표시 하는 데 필요한 HTML만 포함 된 부분 뷰.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-330">The `personlist.html` file is a partial view containing only the HTML needed to display person list.</span></span>

<span data-ttu-id="f7bfa-331">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/partials/personlist.html?highlight=3)]</span><span class="sxs-lookup"><span data-stu-id="f7bfa-331">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/partials/personlist.html?highlight=3)]</span></span>

<span data-ttu-id="f7bfa-332">컨트롤러 모듈의를 사용 하 여 정의 된 `controller` 함수 *personListController.js*합니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-332">The controller is defined by using the module's `controller` function in *personListController.js*.</span></span>

<span data-ttu-id="f7bfa-333">[!code-javascript[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/personListController.js?highlight=1)]</span><span class="sxs-lookup"><span data-stu-id="f7bfa-333">[!code-javascript[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/personListController.js?highlight=1)]</span></span>

<span data-ttu-id="f7bfa-334">이 응용 프로그램을 실행 하 고 탐색 하는 `people/spa#/persons` 확인해 보겠습니다 URL:</span><span class="sxs-lookup"><span data-stu-id="f7bfa-334">If we run this application and navigate to the `people/spa#/persons` URL, we will see:</span></span>

![사용자 목록 보기](angular/_static/spa-persons.png)

<span data-ttu-id="f7bfa-336">하는 경우 이동할 세부 정보 페이지에 예를 들어 `people/spa#/persons/2`, 세부 정보 부분 뷰를 살펴봅니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-336">If we navigate to a detail page, for example `people/spa#/persons/2`, we will see the detail partial view:</span></span>

![사용자 세부 정보 보기](angular/_static/spa-persons-2.png)

<span data-ttu-id="f7bfa-338">전체 소스 및 모든 파일에이 문서에 표시 되지 않습니다 볼 수 있습니다 [GitHub](https://github.com/aspnet/Docs/tree/master/aspnetcore/client-side/angular/sample)합니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-338">You can view the full source and any files not shown in this article on [GitHub](https://github.com/aspnet/Docs/tree/master/aspnetcore/client-side/angular/sample).</span></span>

### <a name="event-handlers"></a><span data-ttu-id="f7bfa-339">이벤트 처리기</span><span class="sxs-lookup"><span data-stu-id="f7bfa-339">Event Handlers</span></span>

<span data-ttu-id="f7bfa-340">HTML dom에서 입력된 요소에 이벤트 처리 기능을 추가 하는 AngularJS 지시문의 여러 가지</span><span class="sxs-lookup"><span data-stu-id="f7bfa-340">There are a number of directives in AngularJS that add event-handling capabilities to the input elements in your HTML DOM.</span></span> <span data-ttu-id="f7bfa-341">다음은 AngularJS에 내장 되어 있는 이벤트의 목록입니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-341">Below is a list of the events that are built into AngularJS.</span></span>

   * `ng-click`

   * `ng-dbl-click`

   * `ng-mousedown`

   * `ng-mouseup`

   * `ng-mouseenter`

   * `ng-mouseleave`

   * `ng-mousemove`

   * `ng-keydown`

   * `ng-keyup`

   * `ng-keypress`

   * `ng-change`

> [!NOTE]
> <span data-ttu-id="f7bfa-342">사용 하 여 사용자 고유의 이벤트 처리기를 추가할 수는 [AngularJS 기능 사용자 지정 지시문](https://docs.angularjs.org/guide/directive)합니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-342">You can add your own event handlers using the [custom directives feature in AngularJS](https://docs.angularjs.org/guide/directive).</span></span>

<span data-ttu-id="f7bfa-343">방법을 살펴보겠습니다는 `ng-click` 이벤트 연결 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-343">Let's look at how the `ng-click` event is wired up.</span></span> <span data-ttu-id="f7bfa-344">라는 새 JavaScript 파일을 만듭니다 *eventHandlerController.js*, 다음을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-344">Create a new JavaScript file named *eventHandlerController.js*, and add the following to it:</span></span>

<span data-ttu-id="f7bfa-345">[!code-javascript[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/eventHandlerController.js?highlight=5,6,7)]</span><span class="sxs-lookup"><span data-stu-id="f7bfa-345">[!code-javascript[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/eventHandlerController.js?highlight=5,6,7)]</span></span>

<span data-ttu-id="f7bfa-346">새 확인 `sayName` 함수 `eventHandlerController` 위 5 줄에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-346">Notice the new `sayName` function in `eventHandlerController` on line 5 above.</span></span> <span data-ttu-id="f7bfa-347">모든 메서드를 수행 하는 작업에 이제는 환영 메시지를 통해 사용자에 게 JavaScript 경고를 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-347">All the method is doing for now is showing a JavaScript alert to the user with a welcome message.</span></span>

<span data-ttu-id="f7bfa-348">아래의 보기 AngularJS 이벤트에는 컨트롤러 함수를 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-348">The view below binds a controller function to an AngularJS event.</span></span> <span data-ttu-id="f7bfa-349">줄 9에 있는 단추에는 `ng-click` 각도 지시문 적용 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-349">Line 9 has a button on which the `ng-click` Angular directive has been applied.</span></span> <span data-ttu-id="f7bfa-350">호출 우리의 `sayName` 에 추가 된 함수는 `$scope` 개체는이 보기에 전달 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-350">It calls our `sayName` function, which is attached to the `$scope` object passed to this view.</span></span>

<span data-ttu-id="f7bfa-351">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/People/Events.cshtml?highlight=9)]</span><span class="sxs-lookup"><span data-stu-id="f7bfa-351">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/People/Events.cshtml?highlight=9)]</span></span>

<span data-ttu-id="f7bfa-352">실행 중인 예제에서는 컨트롤러의 `sayName` 함수 단추를 클릭할 때 자동으로 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-352">The running example demonstrates that the controller's `sayName` function is called automatically when the button is clicked.</span></span>

![Click 이벤트](angular/_static/events.png)

<span data-ttu-id="f7bfa-354">에 대 한 자세한 내용은 AngularJS 기본 제공 이벤트 처리기 지시문, 해야 h e a d에 [설명서 웹 사이트](https://docs.angularjs.org/api/ng/directive/ngClick) AngularJS의 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfa-354">For more detail on AngularJS built-in event handler directives, be sure to head to the [documentation website](https://docs.angularjs.org/api/ng/directive/ngClick) of AngularJS.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f7bfa-355">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="f7bfa-355">Additional resources</span></span>

* [<span data-ttu-id="f7bfa-356">Angular Docs</span><span class="sxs-lookup"><span data-stu-id="f7bfa-356">Angular Docs</span></span>](https://docs.angularjs.org)

* [<span data-ttu-id="f7bfa-357">Angular 2 정보</span><span class="sxs-lookup"><span data-stu-id="f7bfa-357">Angular 2 Info</span></span>](https://angular.io/)
