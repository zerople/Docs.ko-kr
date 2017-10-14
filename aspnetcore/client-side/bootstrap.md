---
title: "부트스트랩을 사용한 beautiful 및 응답성이 뛰어난 사이트 구축"
author: ardalis
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: bd27832c-2877-4b7b-9337-e009361d845f
ms.technology: aspnet
ms.prod: asp.net-core
uid: client-side/bootstrap
ms.openlocfilehash: f89ad584600c3f12a936599de27f931aff0cd4b5
ms.sourcegitcommit: 8f4d4fad1ca27adf9e396f5c205c9875a3963664
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/13/2017
---
# <a name="building-beautiful-responsive-sites-with-bootstrap"></a><span data-ttu-id="6eea8-103">부트스트랩을 사용한 beautiful 및 응답성이 뛰어난 사이트 구축</span><span class="sxs-lookup"><span data-stu-id="6eea8-103">Building beautiful, responsive sites with Bootstrap</span></span>

<a name="bootstrap-index"></a>

<span data-ttu-id="6eea8-104">으로 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="6eea8-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="6eea8-105">부트스트랩 현재 응답성이 뛰어난 웹 응용 프로그램을 개발 하기 위한 가장 인기 있는 웹 프레임 워크입니다.</span><span class="sxs-lookup"><span data-stu-id="6eea8-105">Bootstrap is currently the most popular web framework for developing responsive web applications.</span></span> <span data-ttu-id="6eea8-106">프런트 엔드 디자인 및 개발 또는 전문가 초보자에 관계 없이 다양 한 기능 및 웹 사이트와 사용자의 경험을 향상 시킬 수 있는 혜택을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="6eea8-106">It offers a number of features and benefits that can improve your users' experience with your web site, whether you're a novice at front-end design and development or an expert.</span></span> <span data-ttu-id="6eea8-107">부트스트랩은 CSS 및 JavaScript 파일의 집합으로 배포 되 고 태블릿에 데스크톱을 효율적으로 휴대폰에서 웹 사이트 또는 응용 프로그램 눈금을 도움이 되도록 설계 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="6eea8-107">Bootstrap is deployed as a set of CSS and JavaScript files, and is designed to help your website or application scale efficiently from phones to tablets to desktops.</span></span>

## <a name="getting-started"></a><span data-ttu-id="6eea8-108">시작</span><span class="sxs-lookup"><span data-stu-id="6eea8-108">Getting started</span></span>

<span data-ttu-id="6eea8-109">부트스트랩을 시작 하는 방법은 여러 가지가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6eea8-109">There are several ways to get started with Bootstrap.</span></span> <span data-ttu-id="6eea8-110">Visual Studio에서 새 웹 응용 프로그램을 시작 하는 경우는 case 부트스트랩을 미리 설치 되어, ASP.NET Core에 대 한 기본 시작 템플릿을 선택할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6eea8-110">If you're starting a new web application in Visual Studio, you can choose the default starter template for ASP.NET Core, in which case Bootstrap will come pre-installed:</span></span>

![스타터 서식 파일 솔루션 뷰를 부트스트랩 합니다.](bootstrap/_static/bootstrap-in-starter-template.png)

<span data-ttu-id="6eea8-112">ASP.NET Core를 부트스트랩 추가 프로젝트는 단순히 추가 하는 방법의 *bower.json* 종속성으로:</span><span class="sxs-lookup"><span data-stu-id="6eea8-112">Adding Bootstrap to an ASP.NET Core project is simply a matter of adding it to *bower.json* as a dependency:</span></span>

[!code-json[Main](../common/samples/WebApplication1/bower.json?highlight=5)]

<span data-ttu-id="6eea8-113">이 부트스트랩 ASP.NET Core 프로젝트에 추가 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="6eea8-113">This is the recommended way to add Bootstrap to an ASP.NET Core project.</span></span>

<span data-ttu-id="6eea8-114">또한 부트스트랩 Bower, npm NuGet 등의 여러 패키지 관리자 중 하나를 사용 하 여 설치할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6eea8-114">You can also install bootstrap using one of several package managers, such as Bower, npm, or NuGet.</span></span> <span data-ttu-id="6eea8-115">각각의 경우에는 프로세스는 기본적으로 동일 합니다.</span><span class="sxs-lookup"><span data-stu-id="6eea8-115">In each case, the process is essentially the same:</span></span>

### <a name="bower"></a><span data-ttu-id="6eea8-116">Bower</span><span class="sxs-lookup"><span data-stu-id="6eea8-116">Bower</span></span>

```console
bower install bootstrap
```

### <a name="npm"></a><span data-ttu-id="6eea8-117">npm</span><span class="sxs-lookup"><span data-stu-id="6eea8-117">npm</span></span>

```console
npm install bootstrap
```

### <a name="nuget"></a><span data-ttu-id="6eea8-118">NuGet</span><span class="sxs-lookup"><span data-stu-id="6eea8-118">NuGet</span></span>

```console
Install-Package bootstrap
```

> [!NOTE]
> <span data-ttu-id="6eea8-119">ASP.NET Core에서 부트스트랩 Bower 경유 하는 것과 같은 클라이언트 쪽 종속성을 설치 하는 것이 좋습니다 (사용 하 여 *bower.json*위에 표시 된 것 처럼).</span><span class="sxs-lookup"><span data-stu-id="6eea8-119">The recommended way to install client-side dependencies like Bootstrap in ASP.NET Core is via Bower (using *bower.json*, as shown above).</span></span> <span data-ttu-id="6eea8-120">Npm/NuGet을 사용 하는 다른 종류의 이전 버전의 ASP.NET 포함 하 여 웹 응용 프로그램에 추가할 수 부트스트랩 얼마나 쉽게 설명 하기 위해 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6eea8-120">The use of npm/NuGet are shown to demonstrate how easily Bootstrap can be added to other kinds of web applications, including earlier versions of ASP.NET.</span></span>

<span data-ttu-id="6eea8-121">부트스트랩의 고유한 로컬 버전을 참조 하는 경우 사용 하는 모든 페이지에서이 참조 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="6eea8-121">If you're referencing your own local versions of Bootstrap, you'll need to reference them in any pages that will use it.</span></span> <span data-ttu-id="6eea8-122">프로덕션 환경에서 부트스트랩 CDN을 사용 하 여 참조 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="6eea8-122">In production you should reference bootstrap using a CDN.</span></span> <span data-ttu-id="6eea8-123">기본 ASP.NET 사이트 서식 파일에는 *_Layout.cshtml* 파일 다음과 같이 되므로 않습니다:</span><span class="sxs-lookup"><span data-stu-id="6eea8-123">In the default ASP.NET site template, the *_Layout.cshtml* file does so like this:</span></span>

[!code-html[Main](../common/samples/WebApplication1/Views/Shared/_Layout.cshtml?highlight=9,13,51,59)]

> [!NOTE]
> <span data-ttu-id="6eea8-124">부트스트랩의 jQuery 플러그 사용 하려는 경우에 jQuery 참조 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="6eea8-124">If you're going to be using any of Bootstrap's jQuery plugins, you will also need to reference jQuery.</span></span>

## <a name="basic-templates-and-features"></a><span data-ttu-id="6eea8-125">기본 서식 파일 및 기능</span><span class="sxs-lookup"><span data-stu-id="6eea8-125">Basic templates and features</span></span>

<span data-ttu-id="6eea8-126">가장 기본적인 부트스트랩 서식 파일은와 매우 비슷하며는 *_Layout.cshtml* 파일 표시 위, 하 고 간단 하 게 탐색 및 페이지의 나머지 부분을 렌더링 하는 곳에 대 한 기본 메뉴가 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6eea8-126">The most basic Bootstrap template looks very much like the *_Layout.cshtml* file shown above, and simply includes a basic menu for navigation and a place to render the rest of the page.</span></span>

### <a name="basic-navigation"></a><span data-ttu-id="6eea8-127">기본 탐색</span><span class="sxs-lookup"><span data-stu-id="6eea8-127">Basic navigation</span></span>

<span data-ttu-id="6eea8-128">기본 서식 파일의 집합을 사용 하 여 `<div>` 위쪽 탐색 모음 및 페이지의 본문을 렌더링 하는 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="6eea8-128">The default template uses a set of `<div>` elements to render a top navbar and the main body of the page.</span></span> <span data-ttu-id="6eea8-129">HTML5를 사용 하는 경우에 첫 번째를 바꿀 수 있습니다 `<div>` 사용 하 여 태그는 `<nav>` 태그를 동일한 결과 얻을 하지만 정확한 의미 합니다.</span><span class="sxs-lookup"><span data-stu-id="6eea8-129">If you're using HTML5, you can replace the first `<div>` tag with a `<nav>` tag to get the same effect, but with more precise semantics.</span></span>  <span data-ttu-id="6eea8-130">이 첫 번째 내 `<div>` 다른 몇 가지를 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6eea8-130">Within this first `<div>` you can see there are several others.</span></span> <span data-ttu-id="6eea8-131">첫째, 한 `<div>` "container" 한 후, 두 개 내에서 클래스와 함께 `<div>` 요소: "탐색 모음 헤더" 및 "navbar 축소"입니다.</span><span class="sxs-lookup"><span data-stu-id="6eea8-131">First, a `<div>` with a class of "container", and then within that, two more `<div>` elements: "navbar-header" and "navbar-collapse".</span></span>  <span data-ttu-id="6eea8-132">화면 특정 최소 너비를 3 가로선을 보여 주는 이하인 경우 표시 되는 단추를 포함 하는 탐색 모음 헤더 div (소위는 "햄버거 아이콘").</span><span class="sxs-lookup"><span data-stu-id="6eea8-132">The navbar-header div includes a button that will appear when the screen is below a certain minimum width, showing 3 horizontal lines (a so-called "hamburger icon").</span></span> <span data-ttu-id="6eea8-133">아이콘은 순수 HTML과 CSS;를 사용 하 여 렌더링은 이미지가 없는 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="6eea8-133">The icon is rendered using pure HTML and CSS; no image is required.</span></span> <span data-ttu-id="6eea8-134">이 각각의 아이콘을 표시 하는 코드는 <span> 태그 렌더링 흰색 막대 중 하나:</span><span class="sxs-lookup"><span data-stu-id="6eea8-134">This is the code that displays the icon, with each of the <span> tags rendering one of the white bars:</span></span>

```html
<button type="button" class="navbar-toggle" data-toggle="collapse" data-target=".navbar-collapse">
    <span class="icon-bar"></span>
    <span class="icon-bar"></span>
    <span class="icon-bar"></span>
</button>
```

<span data-ttu-id="6eea8-135">맨 왼쪽에 표시 되는 응용 프로그램 이름을 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6eea8-135">It also includes the application name, which appears in the top left.</span></span>  <span data-ttu-id="6eea8-136">기본 탐색 메뉴에서 렌더링 되는 `<ul>` 내의 두 번째 div 요소 홈, 정보 및 연락처에 대 한 링크를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="6eea8-136">The main navigation menu is rendered by the `<ul>` element within the second div, and includes links to Home, About, and Contact.</span></span> <span data-ttu-id="6eea8-137">등록 및 로그인에 대 한 추가 링크는 29 선에 _LoginPartial 선으로 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6eea8-137">Additional links for Register and Login are added by the _LoginPartial line on line 29.</span></span> <span data-ttu-id="6eea8-138">다른 탐색, 아래 각 페이지의 본문은 렌더링 되 `<div>`"container" 및 "본문 콘텐츠" 클래스와는 별도로 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="6eea8-138">Below the navigation, the main body of each page is rendered in another `<div>`, marked with the "container" and "body-content" classes.</span></span> <span data-ttu-id="6eea8-139">여기에 표시 된 간단한 기본 _Layout 파일에서 페이지의 내용은 페이지 및 간단한와 관련 된 특정 보기에서 렌더링 `<footer>` 의 끝에 추가 되는 `<div>` 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="6eea8-139">In the simple default _Layout file shown here, the contents of the page are rendered by the specific View associated with the page, and then a simple `<footer>` is added to the end of the `<div>` element.</span></span>  <span data-ttu-id="6eea8-140">페이지에 대 한 기본 제공 표시 되는 방식을 사용 하 여 확인할 수 있습니다이 템플릿에서:</span><span class="sxs-lookup"><span data-stu-id="6eea8-140">You can see how the built-in About page appears using this template:</span></span>

![페이지 정보](bootstrap/_static/about-page-wide.png)

<span data-ttu-id="6eea8-142">창의 특정 너비 아래로 떨어질 경우 오른쪽, 위에서 단추 "햄버거"로 축소 된 탐색 모음에 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6eea8-142">The collapsed navbar, with "hamburger" button in the top right, appears when the window drops below a certain width:</span></span>

![햄버거 메뉴와 페이지에 대 한](bootstrap/_static/about-page-hamburger.png)

<span data-ttu-id="6eea8-144">아이콘을 클릭 하면 페이지의 맨 위에서 아래로 내려갑니다 세로 서랍에서 메뉴 항목 표시:</span><span class="sxs-lookup"><span data-stu-id="6eea8-144">Clicking the icon reveals the menu items in a vertical drawer that slides down from the top of the page:</span></span>

![햄버거 메뉴와 페이지에 대 한 확장](bootstrap/_static/about-page-hamburger-open.png)

### <a name="typography-and-links"></a><span data-ttu-id="6eea8-146">입력 체계 및 링크</span><span class="sxs-lookup"><span data-stu-id="6eea8-146">Typography and links</span></span>

<span data-ttu-id="6eea8-147">부트스트랩은 사이트의 기본 입력 체계, 색 및 해당 CSS 파일에 서식 지정 하는 링크를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="6eea8-147">Bootstrap sets up the site's basic typography, colors, and link formatting in its CSS file.</span></span> <span data-ttu-id="6eea8-148">이 CSS 파일에 테이블, 단추, 폼 요소, 이미지, 등에 대 한 기본 스타일 ([자세한](http://getbootstrap.com/css/)).</span><span class="sxs-lookup"><span data-stu-id="6eea8-148">This CSS file includes default styles for tables, buttons, form elements, images, and more ([learn more](http://getbootstrap.com/css/)).</span></span> <span data-ttu-id="6eea8-149">다음 표 레이아웃 시스템 기능은 특히 유용 합니다.</span><span class="sxs-lookup"><span data-stu-id="6eea8-149">One particularly useful feature is the grid layout system, covered next.</span></span>

### <a name="grids"></a><span data-ttu-id="6eea8-150">표</span><span class="sxs-lookup"><span data-stu-id="6eea8-150">Grids</span></span>

<span data-ttu-id="6eea8-151">부트스트랩의 가장 인기 있는 기능 중 하나는 모눈 레이아웃 시스템입니다.</span><span class="sxs-lookup"><span data-stu-id="6eea8-151">One of the most popular features of Bootstrap is its grid layout system.</span></span> <span data-ttu-id="6eea8-152">최신 웹 응용 프로그램 사용 안는 `<table>` 태그 레이아웃의 경우 대신 실제 테이블 형식 데이터에이 요소의 사용을 제한 합니다.</span><span class="sxs-lookup"><span data-stu-id="6eea8-152">Modern web applications should avoid using the `<table>` tag for layout, instead restricting the use of this element to actual tabular data.</span></span> <span data-ttu-id="6eea8-153">대신 열과 행 레이아웃할 수는 일련의를 사용 하 여 `<div>` 요소 및 해당 CSS 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="6eea8-153">Instead, columns and rows can be laid out using a series of `<div>` elements and the appropriate CSS classes.</span></span> <span data-ttu-id="6eea8-154">세로로에 표시할 좁은 화면와 같은 휴대폰 격자 레이아웃을 조정 하는 기능을 포함 하 여이 방식의 장점은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="6eea8-154">There are several advantages to this approach, including the ability to adjust the layout of grids to display vertically on narrow screens, such as on phones.</span></span>

<span data-ttu-id="6eea8-155">[부트스트랩의 모눈 레이아웃 시스템](http://getbootstrap.com/css/#grid) 는 12 개의 열을 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="6eea8-155">[Bootstrap's grid layout system](http://getbootstrap.com/css/#grid) is based on twelve columns.</span></span> <span data-ttu-id="6eea8-156">이 번호는 1, 2, 3 또는 4 개의 열,으로 균등 하 게 나눌 수 있습니다 및 열 너비는 1 내에 다 수 있습니다 때문에 선택 된 화면의 세로 너비가 12 일/합니다.</span><span class="sxs-lookup"><span data-stu-id="6eea8-156">This number was chosen because it can be divided evenly into 1, 2, 3, or 4 columns, and column widths can vary to within 1/12th of the vertical width of the screen.</span></span> <span data-ttu-id="6eea8-157">격자 레이아웃 시스템을 사용 하 여을 시작 하려면 먼저 검토 해야 컨테이너 `<div>` 한 행을 추가 하 고 `<div>`여기 표시 된 것 처럼:</span><span class="sxs-lookup"><span data-stu-id="6eea8-157">To start using the grid layout system, you should begin with a container `<div>` and then add a row `<div>`, as shown here:</span></span>

```html
<div class="container">
    <div class="row">
        ...
    </div>
</div>
```

<span data-ttu-id="6eea8-158">다음으로 더 추가 `<div>` 각 열에 대 한 요소 하 고 열 수를 지정 하는 `<div>` "col-md-"로 시작 하는 CSS 클래스의 일부로 (12) 중 차지 하 합니다.</span><span class="sxs-lookup"><span data-stu-id="6eea8-158">Next, add additional `<div>` elements for each column, and specify the number of columns that `<div>` should occupy (out of 12) as part of a CSS class starting with "col-md-".</span></span> <span data-ttu-id="6eea8-159">예를 들어, 단순히 동일한 크기의 두 열이 포함 하려는 각각에 대 한 "col md 6"의 클래스를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="6eea8-159">For instance, if you want to simply have two columns of equal size, you would use a class of "col-md-6" for each one.</span></span> <span data-ttu-id="6eea8-160">이 경우 "md"는 짧은 "보통"과 표준 크기의 데스크톱 컴퓨터 디스플레이 크기를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="6eea8-160">In this case "md" is short for "medium" and refers to standard-sized desktop computer display sizes.</span></span> <span data-ttu-id="6eea8-161">옵션에는 네에서 선택할 수 있으며 각각에 사용할 더 높은 너비 (이므로 레이아웃 화면 너비에 관계 없이 수정 하려는 경우에 xs 클래스를 지정할 수 있습니다) 재정의 되지 않는 경우.</span><span class="sxs-lookup"><span data-stu-id="6eea8-161">There are four different options you can choose from, and each will be used for higher widths unless overridden (so if you want the layout to be fixed regardless of screen width, you can just specify xs classes).</span></span>

<span data-ttu-id="6eea8-162">CSS 클래스 접두사</span><span class="sxs-lookup"><span data-stu-id="6eea8-162">CSS Class Prefix</span></span> | <span data-ttu-id="6eea8-163">장치 계층</span><span class="sxs-lookup"><span data-stu-id="6eea8-163">Device Tier</span></span> | <span data-ttu-id="6eea8-164">너비</span><span class="sxs-lookup"><span data-stu-id="6eea8-164">Width</span></span>
:---: | :---: | :---:
<span data-ttu-id="6eea8-165">열-xs-</span><span class="sxs-lookup"><span data-stu-id="6eea8-165">col-xs-</span></span> | <span data-ttu-id="6eea8-166">휴대폰</span><span class="sxs-lookup"><span data-stu-id="6eea8-166">Phones</span></span> | <span data-ttu-id="6eea8-167">< 768px</span><span class="sxs-lookup"><span data-stu-id="6eea8-167">< 768px</span></span>
<span data-ttu-id="6eea8-168">열-sm-</span><span class="sxs-lookup"><span data-stu-id="6eea8-168">col-sm-</span></span> | <span data-ttu-id="6eea8-169">태블릿</span><span class="sxs-lookup"><span data-stu-id="6eea8-169">Tablets</span></span> | <span data-ttu-id="6eea8-170">> = 768px</span><span class="sxs-lookup"><span data-stu-id="6eea8-170">>= 768px</span></span>
<span data-ttu-id="6eea8-171">열-md-</span><span class="sxs-lookup"><span data-stu-id="6eea8-171">col-md-</span></span> | <span data-ttu-id="6eea8-172">데스크톱</span><span class="sxs-lookup"><span data-stu-id="6eea8-172">Desktops</span></span> | <span data-ttu-id="6eea8-173">> = 992px</span><span class="sxs-lookup"><span data-stu-id="6eea8-173">>= 992px</span></span>
<span data-ttu-id="6eea8-174">열-lg-</span><span class="sxs-lookup"><span data-stu-id="6eea8-174">col-lg-</span></span> | <span data-ttu-id="6eea8-175">더 큰 데스크톱 표시</span><span class="sxs-lookup"><span data-stu-id="6eea8-175">Larger Desktop Displays</span></span> | <span data-ttu-id="6eea8-176">> = 1200px</span><span class="sxs-lookup"><span data-stu-id="6eea8-176">>= 1200px</span></span>

<span data-ttu-id="6eea8-177">두 열 모두 "col-md-6" 결과 레이아웃 데스크톱 해상도에서 두 개의 열 수 있지만 되어 소형 장치 (또는 데스크톱에 보다 좁은 브라우저 창), 사용자가을 쉽게 볼 수 있도록에 렌더링 될 때이 두 열 컨트롤이 세로로 쌓입니다 됩니다 지정 하는 경우 콘텐츠를 가로로 스크롤할 필요 없이입니다.</span><span class="sxs-lookup"><span data-stu-id="6eea8-177">When specifying two columns both with "col-md-6" the resulting layout will be two columns at desktop resolutions, but these two columns will stack vertically when rendered on smaller devices (or a narrower browser window on a desktop), allowing users to easily view content without the need to scroll horizontally.</span></span>

<span data-ttu-id="6eea8-178">부트스트랩은 기본적으로 항상 단일 열 레이아웃을 둘 이상의 열을 사용할 때는 열을 지정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="6eea8-178">Bootstrap will always default to a single-column layout, so you only need to specify columns when you want more than one column.</span></span> <span data-ttu-id="6eea8-179">에 명시적으로 지정 하는 원하는 `<div>` 모든 12 열 차지 더 큰 장치 계층의 동작을 재정의 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="6eea8-179">The only time you would want to explicitly specify that a `<div>` take up all 12 columns would be to override the behavior of a larger device tier.</span></span> <span data-ttu-id="6eea8-180">여러 장치 계층 클래스를 지정할 때는 특정 지점에서 열 렌더링을 다시 설정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="6eea8-180">When specifying multiple device tier classes, you may need to reset the column rendering at certain points.</span></span> <span data-ttu-id="6eea8-181">특정 뷰포트 내부 표시만 되는 clearfix div를 추가 합니다.이 수행할 수, 다음과 같이 합니다.</span><span class="sxs-lookup"><span data-stu-id="6eea8-181">Adding a clearfix div that is only visible within a certain viewport can achieve this, as shown here:</span></span>

![좁은 문자열과 와이드 뷰포트 표](bootstrap/_static/narrow-and-wide-viewport-grid.png)

<span data-ttu-id="6eea8-183">위의 예제에서는 1, 2 공유 "md" 레이아웃에 행 2와 3 공유 "xs" 레이아웃에 행입니다.</span><span class="sxs-lookup"><span data-stu-id="6eea8-183">In the above example, One and Two share a row in the "md" layout, while Two and Three share a row in the "xs" layout.</span></span> <span data-ttu-id="6eea8-184">clearfix 없이 `<div>`, 2와 3 제대로 표시 되지 않는 "xs" 보기 (하나에만, 4 및 5 개 표시 되는 참고)에서:</span><span class="sxs-lookup"><span data-stu-id="6eea8-184">Without the clearfix `<div>`, Two and Three are not shown correctly in the "xs" view (note that only One, Four, and Five are shown):</span></span>

![clearfix 사용 하지 않고 표](bootstrap/_static/grid-without-clearfix.png)

<span data-ttu-id="6eea8-186">이 예제에서는 단일 행만 `<div>` 사용 된 부트스트랩 된 상태는 주로 레이아웃 및 탄력적인 열에 대해 올바른 작업 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="6eea8-186">In this example, only a single row `<div>` was used, and Bootstrap still mostly did the right thing with regard to the layout and stacking of the columns.</span></span> <span data-ttu-id="6eea8-187">행을 지정 해야 하는 일반적으로 `<div>` 가로 각 행에 대 한 레이아웃을 차지 하며 물론 내부에 다른 부트스트랩 표를 중첩할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6eea8-187">Typically, you should specify a row `<div>` for each horizontal row your layout requires, and of course you can nest Bootstrap grids within one another.</span></span> <span data-ttu-id="6eea8-188">이렇게 하면 각 중첩 된 표 열 클래스를 사용 하 여 다음으로 세분화할 수 있습니다 하 배치 되는 요소 너비의 100%를 차지 합니다.</span><span class="sxs-lookup"><span data-stu-id="6eea8-188">When you do, each nested grid will occupy 100% of the width of the element in which it is placed, which can then be subdivided using column classes.</span></span>

### <a name="jumbotron"></a><span data-ttu-id="6eea8-189">Jumbotron</span><span class="sxs-lookup"><span data-stu-id="6eea8-189">Jumbotron</span></span>

<span data-ttu-id="6eea8-190">Visual Studio 2012 또는 2013에서 기본 ASP.NET MVC 템플릿을 사용한 경우 실행에서 Jumbotron 보았을 것입니다.</span><span class="sxs-lookup"><span data-stu-id="6eea8-190">If you've used the default ASP.NET MVC templates in Visual Studio 2012 or 2013, you've probably seen the Jumbotron in action.</span></span> <span data-ttu-id="6eea8-191">쿼리, rotator, 쿼리, 유사한 요소에 대 한 호출 큰 배경 이미지를 표시 하는 데 사용할 수 있는 페이지의 큰 전자 섹션을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="6eea8-191">It refers to a large full-width section of a page that can be used to display a large background image, a call to action, a rotator, or similar elements.</span></span> <span data-ttu-id="6eea8-192">jumbotron에 페이지를 추가 하려면 추가 `<div>` "jumbotron"의 클래스를 지정 하 고 다음에 컨테이너를 배치 `<div>` 안에 콘텐츠를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="6eea8-192">To add a jumbotron to a page, simply add a `<div>` and give it a class of "jumbotron", then place a container `<div>` inside and add your content.</span></span>  <span data-ttu-id="6eea8-193">주 제목을 표시에 대 한 한 jumbotron를 사용 하는 페이지에 대 한 표준 사용자가 쉽게 조정할 수 있습니다.:</span><span class="sxs-lookup"><span data-stu-id="6eea8-193">We can easily adjust the standard About page to use a jumbotron for the main headings it displays:</span></span>

![jumbotron 예제](bootstrap/_static/jumbotron.png)

### <a name="buttons"></a><span data-ttu-id="6eea8-195">단추</span><span class="sxs-lookup"><span data-stu-id="6eea8-195">Buttons</span></span>

<span data-ttu-id="6eea8-196">기본 단추 클래스 및 해당 색은 아래 그림에 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6eea8-196">The default button classes and their colors are shown in the figure below.</span></span>

![테마가 지정 된 단추](bootstrap/_static/theme-buttons.png)

### <a name="badges"></a><span data-ttu-id="6eea8-198">배지</span><span class="sxs-lookup"><span data-stu-id="6eea8-198">Badges</span></span>

<span data-ttu-id="6eea8-199">배지 탐색 항목 옆에 있는 작은, 일반적으로 숫자로 이루어지며 설명선을 참조 하십시오.</span><span class="sxs-lookup"><span data-stu-id="6eea8-199">Badges refer to small, usually numeric callouts next to a navigation item.</span></span> <span data-ttu-id="6eea8-200">다양 한 메시지 또는 알림, 대기 또는 업데이트의 존재 여부를 나타낼 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6eea8-200">They can indicate a number of messages or notifications waiting, or the presence of updates.</span></span> <span data-ttu-id="6eea8-201">이러한 배지를 지정 하는 작업은 추가 하는 것을 <span> "배지"의 클래스와 텍스트가 포함 된:</span><span class="sxs-lookup"><span data-stu-id="6eea8-201">Specifying such badges is as simple as adding a <span> containing the text, with a class of "badge":</span></span>

![테마가 지정 된 배지](bootstrap/_static/theme-badges.png)

### <a name="alerts"></a><span data-ttu-id="6eea8-203">경고</span><span class="sxs-lookup"><span data-stu-id="6eea8-203">Alerts</span></span>

<span data-ttu-id="6eea8-204">응용 프로그램의 사용자에 게 일종의 알림, 경고 또는 오류 메시지를 표시 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="6eea8-204">You may need to display some kind of notification, alert, or error message to your application's users.</span></span> <span data-ttu-id="6eea8-205">즉, 표준 경고 클래스는 유용 합니다.</span><span class="sxs-lookup"><span data-stu-id="6eea8-205">That's where the standard alert classes are useful.</span></span>  <span data-ttu-id="6eea8-206">연결 된 색 구성표를 4 개의 다른 심각도 수준이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6eea8-206">There are four different severity levels with associated color schemes:</span></span>

![테마가 지정 된 경고](bootstrap/_static/theme-alerts.png)

### <a name="navbars-and-menus"></a><span data-ttu-id="6eea8-208">탐색 모음과 메뉴</span><span class="sxs-lookup"><span data-stu-id="6eea8-208">Navbars and menus</span></span>

<span data-ttu-id="6eea8-209">표준 navbar 우리의 레이아웃에 이미 포함 되어 있지만 부트스트랩 테마 스타일을 추가 옵션을 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="6eea8-209">Our layout already includes a standard navbar, but the Bootstrap theme supports additional styling options.</span></span> <span data-ttu-id="6eea8-210">도 쉽게 가로로 있는 기본 설정에 같이 추가 하위 탐색 항목을 팝업 창 메뉴에 아니라 세로로 탐색 모음을 표시 하도록 선택할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6eea8-210">We can also easily opt to display the navbar vertically rather than horizontally if that's preferred, as well as adding sub-navigation items in flyout menus.</span></span> <span data-ttu-id="6eea8-211">탭 스트립와 같은 간단한 탐색 메뉴의 맨 위에 빌드됩니다.</span><span class="sxs-lookup"><span data-stu-id="6eea8-211">Simple navigation menus, like tab strips, are built on top of</span></span> <ul> <span data-ttu-id="6eea8-212">요소입니다.</span><span class="sxs-lookup"><span data-stu-id="6eea8-212">elements.</span></span> <span data-ttu-id="6eea8-213">이 방금 CSS 클래스 "이동" 및 "탭으로 이동" 하 여 매우 간단 하 게 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6eea8-213">These can be created very simply by just providing them with the CSS classes "nav" and "nav-tabs":</span></span>

![테마가 지정 된 탭](bootstrap/_static/theme-tabstrips.png)

<span data-ttu-id="6eea8-215">탐색 모음 마찬가지로, 되지만 좀 더 복잡 합니다.</span><span class="sxs-lookup"><span data-stu-id="6eea8-215">Navbars are built similarly, but are a bit more complex.</span></span>  <span data-ttu-id="6eea8-216">로 시작는 `<nav>` 또는 `<div>` 컨테이너 div는 나머지 요소가 고 "navbar"의 클래스와 함께 합니다.</span><span class="sxs-lookup"><span data-stu-id="6eea8-216">They start with a `<nav>` or `<div>` with a class of "navbar", within which a container div holds the rest of the elements.</span></span> <span data-ttu-id="6eea8-217">가격 페이지 헤더에 있는 이미는 탐색 모음을 포함 – 지켜볼 단순히 확장 됩니다.이, 드롭다운 메뉴에 대 한 지원을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="6eea8-217">Our page includes a navbar in its header already – the one shown below simply expands on this, adding support for a dropdown menu:</span></span>

![테마가 지정 된 탐색 모음](bootstrap/_static/theme-navbars.png)

### <a name="additional-elements"></a><span data-ttu-id="6eea8-219">추가 요소</span><span class="sxs-lookup"><span data-stu-id="6eea8-219">Additional elements</span></span>

<span data-ttu-id="6eea8-220">스트라이프 뷰에 대 한 지원을 비롯 하 여 올바른 형식의 스타일의 HTML 테이블을 표시 하는 기본 테마를 사용할 수도 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6eea8-220">The default theme can also be used to present HTML tables in a nicely formatted style, including support for striped views.</span></span> <span data-ttu-id="6eea8-221">단추와 유사한 스타일으로 레이블이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6eea8-221">There are labels with styles that are similar to those of the buttons.</span></span> <span data-ttu-id="6eea8-222">표준 HTML 외 추가 스타일 옵션을 지 원하는 사용자 지정 드롭다운 메뉴를 만들 수 있습니다 `<select>` 우리의 기본 시작 사이트에서 이미 사용 하는 것과 같은 탐색 모음과 함께 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="6eea8-222">You can create custom Dropdown menus that support additional styling options beyond the standard HTML `<select>` element, along with Navbars like the one our default starter site is already using.</span></span> <span data-ttu-id="6eea8-223">진행률 표시줄을 해야 하는 경우 몇 가지 스타일에 제목 및 콘텐츠를 포함 하는 패널 및 그룹 목록을 선택할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6eea8-223">If you need a progress bar, there are several styles to choose from, as well as List Groups and panels that include a title and content.</span></span>  <span data-ttu-id="6eea8-224">표준 부트스트랩 테마를 여기에서 추가 옵션을 탐색 합니다.</span><span class="sxs-lookup"><span data-stu-id="6eea8-224">Explore additional options within the standard Bootstrap Theme here:</span></span>

[<span data-ttu-id="6eea8-225">http://getbootstrap.com/examples/theme/</span><span class="sxs-lookup"><span data-stu-id="6eea8-225">http://getbootstrap.com/examples/theme/</span></span>](http://getbootstrap.com/examples/theme/)

## <a name="more-themes"></a><span data-ttu-id="6eea8-226">추가 테마</span><span class="sxs-lookup"><span data-stu-id="6eea8-226">More themes</span></span>

<span data-ttu-id="6eea8-227">해당 CSS의 일부 또는 전부를 재정의 하 여 색 및 스타일을 사용자 고유의 응용 프로그램의 요구 사항에 맞게 조정 표준 부트스트랩 테마를 확장할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6eea8-227">You can extend the standard Bootstrap theme by overriding some or all of its CSS, adjusting the colors and styles to suit your own application's needs.</span></span> <span data-ttu-id="6eea8-228">즉시 사용 가능한 테마에서 시작 하려는 경우 몇 가지 테마 갤러리 WrapBootstrap.com (들어 있는 다양 한 상업적 테마) 및 (제공 하는 사용 가능한 테마) Bootswatch.com 같은 부트스트랩 테마, 온라인 전문를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6eea8-228">If you'd like to start from a ready-made theme, there are several theme galleries available online that specialize in Bootstrap themes, such as WrapBootstrap.com (which has a variety of commercial themes) and Bootswatch.com (which offers free themes).</span></span>  <span data-ttu-id="6eea8-229">일부 유료 서식 파일 사용 가능한 다양 한 차트 및 계기와 많은 기능을 관리 메뉴 및 대시보드에 대 한 다양 한 지원 같은 기본 부트스트랩 테마를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="6eea8-229">Some of the paid templates available provide a great deal of functionality on top of the basic Bootstrap theme, such as rich support for administrative menus, and dashboards with rich charts and gauges.</span></span> <span data-ttu-id="6eea8-230">인기 유료 서식 파일의 예는 Inspinia, AngularJS 및 정적 HTML 버전 외에 ASP.NET MVC5 서식 파일을 포함 하는 $18에 대 한 판매에 대 한 현재입니다.</span><span class="sxs-lookup"><span data-stu-id="6eea8-230">An example of a popular paid template is Inspinia, currently for sale for $18, which includes an ASP.NET MVC5 template in addition to AngularJS and static HTML versions.</span></span> <span data-ttu-id="6eea8-231">샘플 스크린 샷은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="6eea8-231">A sample screenshot is shown below.</span></span>

![테마 inspinia 예제](bootstrap/_static/theme-inspinia.png)

<span data-ttu-id="6eea8-233">부트스트랩 테마 변경 하려는 경우 배치의 *bootstrap.css* 에서 원하는 테마에 대 한 파일의 **wwwroot/css** 폴더에서 참조를 변경 하 고 *_Layout.cshtml* 가리킵니다.</span><span class="sxs-lookup"><span data-stu-id="6eea8-233">If you want to change your Bootstrap theme, put the *bootstrap.css* file for the theme you want in the **wwwroot/css** folder and change the references in *_Layout.cshtml* to point it.</span></span>  <span data-ttu-id="6eea8-234">모든 환경에 대 한 링크를 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="6eea8-234">Change the links for all environments:</span></span>

```html
<environment names="Development">
    <link rel="stylesheet" href="~/css/bootstrap.css" />
```

```html
<environment names="Staging,Production">
    <link rel="stylesheet" href="~/css/bootstrap.min.css" />
```

<span data-ttu-id="6eea8-235">사용자 고유의 대시보드 작성 하려는 경우에서 시작할 수 있습니다 사용할 수 있는 무료 예제의 여기: [http://getbootstrap.com/examples/dashboard/](http://getbootstrap.com/examples/dashboard/)합니다.</span><span class="sxs-lookup"><span data-stu-id="6eea8-235">If you want to build your own dashboard, you can start from the free example available here: [http://getbootstrap.com/examples/dashboard/](http://getbootstrap.com/examples/dashboard/).</span></span>

## <a name="components"></a><span data-ttu-id="6eea8-236">구성 요소</span><span class="sxs-lookup"><span data-stu-id="6eea8-236">Components</span></span>

<span data-ttu-id="6eea8-237">앞에서 설명한 이러한 요소 외에도 부트스트랩은 다양 한 지원 [UI 구성 요소를 기본 제공](http://getbootstrap.com/components/)합니다.</span><span class="sxs-lookup"><span data-stu-id="6eea8-237">In addition to those elements already discussed, Bootstrap includes support for a variety of [built-in UI components](http://getbootstrap.com/components/).</span></span>

### <a name="glyphicons"></a><span data-ttu-id="6eea8-238">Glyphicons</span><span class="sxs-lookup"><span data-stu-id="6eea8-238">Glyphicons</span></span>

<span data-ttu-id="6eea8-239">부트스트랩 Glyphicons에서 아이콘 집합에 포함 됩니다 ([http://glyphicons.com](http://glyphicons.com)), 부트스트랩 사용 웹 응용 프로그램 내에서 사용 하기 위해 무료로 사용할 수는 200 개가 넘는 아이콘을 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="6eea8-239">Bootstrap includes icon sets from Glyphicons ([http://glyphicons.com](http://glyphicons.com)), with over 200 icons freely available for use within your Bootstrap-enabled web application.</span></span> <span data-ttu-id="6eea8-240">방금 일부 예제는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="6eea8-240">Here's just a small sample:</span></span>

![Glyphicons](bootstrap/_static/theme-glyphicons.png)

### <a name="input-groups"></a><span data-ttu-id="6eea8-242">입력된 그룹</span><span class="sxs-lookup"><span data-stu-id="6eea8-242">Input groups</span></span>

<span data-ttu-id="6eea8-243">직관적인 환경 사용 하 여 사용자를 제공 하는 추가 텍스트 또는 입력된 요소를 사용 하는 단추의 번들 입력된 그룹을 사용:</span><span class="sxs-lookup"><span data-stu-id="6eea8-243">Input groups allow bundling of additional text or buttons with an input element, providing the user with a more intuitive experience:</span></span>

![입력된 그룹](bootstrap/_static/input-groups.png)

### <a name="breadcrumbs"></a><span data-ttu-id="6eea8-245">이동 경로</span><span class="sxs-lookup"><span data-stu-id="6eea8-245">Breadcrumbs</span></span>

<span data-ttu-id="6eea8-246">이동 경로 해당 최근 기록 또는 깊이 사이트의 탐색 계층 구조 내에서 사용자를 표시 하는 데 공통 UI 구성 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="6eea8-246">Breadcrumbs are a common UI component used to show a user their recent history or depth within a site's navigation hierarchy.</span></span> <span data-ttu-id="6eea8-247">"이동" 클래스를 적용 하 여 쉽게 추가 `<ol>` 목록 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="6eea8-247">Add them easily by applying the "breadcrumb" class to any `<ol>` list element.</span></span> <span data-ttu-id="6eea8-248">에 "페이지 매김" 클래스를 사용 하 여 페이지 매김에 대 한 기본 제공 지원을 포함 한 `<ul>` 내의 요소는 `<nav>`합니다.</span><span class="sxs-lookup"><span data-stu-id="6eea8-248">Include built-in support for pagination by using the "pagination" class on a `<ul>` element within a `<nav>`.</span></span> <span data-ttu-id="6eea8-249">포함 된 응답 슬라이드 쇼 및 비디오를 사용 하 여 추가 `<iframe>`, `<embed>`, `<video>`, 또는 `<object>` 부트스트랩은 자동으로 스타일 지정 하는 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="6eea8-249">Add responsive embedded slideshows and video by using `<iframe>`, `<embed>`, `<video>`, or `<object>` elements, which Bootstrap will style automatically.</span></span> <span data-ttu-id="6eea8-250">"포함-응답-16by9"와 같은 특정 클래스를 사용 하 여 특정 가로 세로 비율을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="6eea8-250">Specify a particular aspect ratio by using specific classes like "embed-responsive-16by9".</span></span>

## <a name="javascript-support"></a><span data-ttu-id="6eea8-251">JavaScript가 지원</span><span class="sxs-lookup"><span data-stu-id="6eea8-251">JavaScript support</span></span>

<span data-ttu-id="6eea8-252">부트스트랩의 JavaScript 라이브러리에는 응용 프로그램에서 프로그래밍 방식으로 동작을 제어 하는 포함 된 구성 요소에 대 한 API 지원을 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="6eea8-252">Bootstrap's JavaScript library includes API support for the included components, allowing you to control their behavior programmatically within your application.</span></span> <span data-ttu-id="6eea8-253">또한 *bootstrap.js* 수십 개의 포함 전환, 모달 대화 상자와 같은 추가 기능을 제공 하는 사용자 지정 jQuery 플러그 인 (스타일 문서에 사용자 스크롤 위치에 따라 업데이트)를 감지, 스크롤 화면 밖 스크롤되지 있도록 창에 동작과 컨베이어 벨트, 추가 메뉴를 축소 합니다.</span><span class="sxs-lookup"><span data-stu-id="6eea8-253">In addition, *bootstrap.js* includes over a dozen custom jQuery plugins, providing additional features like transitions, modal dialogs, scroll detection (updating styles based on where the user has scrolled in the document), collapse behavior, carousels, and affixing menus to the window so they do not scroll off the screen.</span></span> <span data-ttu-id="6eea8-254">부트스트랩 – 방문 하십시오 자세히 알아보려면에 기본 제공 JavaScript 추가 기능의 모든 처리 하려면 충분 한 공간이 없을 [http://getbootstrap.com/javascript/](http://getbootstrap.com/javascript/)합니다.</span><span class="sxs-lookup"><span data-stu-id="6eea8-254">There's not sufficient room to cover all of the JavaScript add-ons built into Bootstrap – to learn more please visit [http://getbootstrap.com/javascript/](http://getbootstrap.com/javascript/).</span></span>

## <a name="summary"></a><span data-ttu-id="6eea8-255">요약</span><span class="sxs-lookup"><span data-stu-id="6eea8-255">Summary</span></span>

<span data-ttu-id="6eea8-256">부트스트랩 신속 하 고 생산성을 배치 하 고 다양 한 웹 사이트 및 응용 프로그램 스타일 지정에 사용할 수 있는 웹 프레임 워크를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="6eea8-256">Bootstrap provides a web framework that can be used to quickly and productively lay out and style a wide variety of websites and applications.</span></span> <span data-ttu-id="6eea8-257">기본 입력 체계 및 스타일 수동 하거나 상업적으로 구입할 수 있는 사용자 지정 테마 지원을 통해 쉽게 조작할 수 있는 한 쾌적 한 모양 및 느낌을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="6eea8-257">Its basic typography and styles provide a pleasant look and feel that can easily be manipulated through custom theme support, which can be hand-crafted or purchased commercially.</span></span> <span data-ttu-id="6eea8-258">다양 한 웹 구성 요소 이전에 비용이 많이 드는 타사 컨트롤 현대적이 고 열린 웹 표준을 지 원하는 동안 수행 하기 위해이 요구를 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="6eea8-258">It supports a host of web components that in the past would have required expensive third-party controls to accomplish, while supporting modern and open web standards.</span></span>
