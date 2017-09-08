---
title: "ASP.NET Core Knockout.js MVVM 프레임 워크"
author: ardalis
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: b20e3b23-1c51-47bf-adac-91b5048567e0
ms.technology: aspnet
ms.prod: asp.net-core
uid: client-side/knockout
ms.openlocfilehash: 87b4fdc86f6bb870ae0a8cc85688a549fd0740ac
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/11/2017
---
# <a name="knockoutjs-mvvm-framework-in-aspnet-core"></a><span data-ttu-id="a4dfc-103">ASP.NET Core Knockout.js MVVM 프레임 워크</span><span class="sxs-lookup"><span data-stu-id="a4dfc-103">Knockout.js MVVM Framework in ASP.NET Core</span></span>

<span data-ttu-id="a4dfc-104">으로 [Steve Smith](http://ardalis.com)</span><span class="sxs-lookup"><span data-stu-id="a4dfc-104">By [Steve Smith](http://ardalis.com)</span></span>

<span data-ttu-id="a4dfc-105">Knockout는 복잡 한 데이터 기반 사용자 인터페이스의 생성을 간소화 하는 인기 있는 JavaScript 라이브러리입니다.</span><span class="sxs-lookup"><span data-stu-id="a4dfc-105">Knockout is a popular JavaScript library that simplifies the creation of complex data-based user interfaces.</span></span> <span data-ttu-id="a4dfc-106">단독으로 또는 함께 jQuery 등의 다른 라이브러리에는 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a4dfc-106">It can be used alone or with other libraries, such as jQuery.</span></span> <span data-ttu-id="a4dfc-107">기본 목적은 모델 업데이트 되는 UI를 변경 하면 해당 UI 요소를 JavaScript 개체로 정의 하는 기본 데이터 모델에 바인딩할 하며 그 반대 과정도 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="a4dfc-107">Its primary purpose is to bind UI elements to an underlying data model defined as a JavaScript object, such that when changes are made to the UI, the model is updated, and vice versa.</span></span> <span data-ttu-id="a4dfc-108">Knockout 지원 웹 응용 프로그램의 클라이언트 쪽 동작이 모델-뷰-MVVM () 패턴을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="a4dfc-108">Knockout facilitates the use of a Model-View-ViewModel (MVVM) pattern in a web application's client-side behavior.</span></span> <span data-ttu-id="a4dfc-109">관찰 가능 개체 및 바인딩 Knockout의 MVVM 구현 작업할 때 배워야 하나 두 가지 주요 개념을 알아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a4dfc-109">The two main concepts one must learn when working with Knockout's MVVM implementation are Observables and Bindings.</span></span>

## <a name="getting-started"></a><span data-ttu-id="a4dfc-110">시작</span><span class="sxs-lookup"><span data-stu-id="a4dfc-110">Getting started</span></span>

<span data-ttu-id="a4dfc-111">Knockout를 하나의 JavaScript 파일로 설치 되므로 배포 되 고 사용 하는 매우 간단 하 고 사용 하 여 [bower](bower.md)합니다.</span><span class="sxs-lookup"><span data-stu-id="a4dfc-111">Knockout is deployed as a single JavaScript file, so installing and using it is very straightforward using [bower](bower.md).</span></span> <span data-ttu-id="a4dfc-112">이미가지고 [bower](bower.md) 및 [gulp](using-gulp.md) 열고 구성 *bower.json* 다음과 같이 프로젝트에서 ASP.NET Core 더하고 knockout 종속성:</span><span class="sxs-lookup"><span data-stu-id="a4dfc-112">Assuming you already have [bower](bower.md) and [gulp](using-gulp.md) configured, open *bower.json* in your ASP.NET Core project and add the knockout dependency as shown here:</span></span>

```json
{
  "name": "KnockoutDemo",
  "private": true,
  "dependencies": {
    "knockout" : "^3.3.0"
  },
  "exportsOverride": {
  }
}
```

<span data-ttu-id="a4dfc-113">이 위치를 수동으로 bower (보기 ‣ 다른 창 ‣ Task Runner 탐색기)에서 작업 러너 탐색기를 열어 실행 한 다음 작업에서 마우스 오른쪽 단추로 클릭 bower에 실행을 선택 하는 다음을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a4dfc-113">With this in place, you can then manually run bower by opening the Task Runner Explorer (under View ‣ Other Windows ‣ Task Runner Explorer) and then under Tasks, right-click on bower and select Run.</span></span> <span data-ttu-id="a4dfc-114">결과 다음과 유사 하 게 같아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a4dfc-114">The result should appear similar to this:</span></span>

![작업 러너 탐색기에서 실행 중인 knockout bower](knockout/_static/bower-knockout.png)

<span data-ttu-id="a4dfc-116">이제 프로젝트의 보면 `wwwroot` 폴더 knockout lib 폴더 아래에 설치 되어 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a4dfc-116">Now if you look in your project's `wwwroot` folder, you should see knockout installed under the lib folder.</span></span>

![knockout lib 폴더에 설치](knockout/_static/wwwroot-knockout.png)

<span data-ttu-id="a4dfc-118">사용자가 파일의 캐시 된 복사본이 이미 있을 것을 따라서 전혀 다운로드 않아도 됩니다 가능성 증가 하면이 프로덕션 환경에서 콘텐츠 배달 네트워크 또는 CDN을 통해 knockout 참조할 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="a4dfc-118">It's recommended that in your production environment you reference knockout via a Content Delivery Network, or CDN, as this increases the likelihood that your users will already have a cached copy of the file and thus will not need to download it at all.</span></span> <span data-ttu-id="a4dfc-119">Knockout은 Microsoft Ajax CDN에서 다음을 포함 하는 여러 가지 Cdn에서 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a4dfc-119">Knockout is available on several CDNs, including the Microsoft Ajax CDN, here:</span></span>

[<span data-ttu-id="a4dfc-120">http://ajax.aspnetcdn.com/ajax/knockout/knockout-3.3.0.js</span><span class="sxs-lookup"><span data-stu-id="a4dfc-120">http://ajax.aspnetcdn.com/ajax/knockout/knockout-3.3.0.js</span></span>](http://ajax.aspnetcdn.com/ajax/knockout/knockout-3.3.0.js)

<span data-ttu-id="a4dfc-121">Knockout는 사용 된 페이지를 포함 하려면 추가 `<script>` 아무 곳에 나 호스트할 것 (응용 프로그램을 또는 CDN을 통해)에서 파일을 참조 하는 요소:</span><span class="sxs-lookup"><span data-stu-id="a4dfc-121">To include Knockout on a page that will use it, simply add a `<script>` element referencing the file from wherever you will be hosting it (with your application, or via a CDN):</span></span>

```html
<script type="text/javascript" src="knockout-3.3.0.js"></script>
```

## <a name="observables-viewmodels-and-simple-binding"></a><span data-ttu-id="a4dfc-122">관찰 가능 개체, Viewmodel, 및 단순 바인딩</span><span class="sxs-lookup"><span data-stu-id="a4dfc-122">Observables, ViewModels, and simple binding</span></span>

<span data-ttu-id="a4dfc-123">이미 JavaScript를 사용 하 여 DOM에 직접 액세스를 통해 웹 페이지에서 요소를 조작 하거나 jQuery와 같은 라이브러리를 사용 하 여에 익숙한 것 같습니다.</span><span class="sxs-lookup"><span data-stu-id="a4dfc-123">You may already be familiar with using JavaScript to manipulate elements on a web page, either via direct access to the DOM or using a library like jQuery.</span></span> <span data-ttu-id="a4dfc-124">일반적으로 직접 특정 사용자 작업에 대 한 응답으로 요소 값을 설정 하는 코드를 작성 하 여 이러한 유형의 동작이 수행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a4dfc-124">Typically this kind of behavior is achieved by writing code to directly set element values in response to certain user actions.</span></span> <span data-ttu-id="a4dfc-125">Knockout와 선언적 방법 만들어지며 대신는 페이지에서 요소 속성에 바인딩된 개체에.</span><span class="sxs-lookup"><span data-stu-id="a4dfc-125">With Knockout, a declarative approach is taken instead, through which elements on the page are bound to properties on an object.</span></span> <span data-ttu-id="a4dfc-126">DOM 요소를 조작 하는 코드를 작성 하는 대신 사용자 작업은 단순히 ViewModel 개체 상호 작용 및 Knockout 사항을 페이지 요소는 동기화 상태 유지 관리 합니다.</span><span class="sxs-lookup"><span data-stu-id="a4dfc-126">Instead of writing code to manipulate DOM elements, user actions simply interact with the ViewModel object, and Knockout takes care of ensuring the page elements are synchronized.</span></span>

<span data-ttu-id="a4dfc-127">간단한 예로 아래 페이지 목록을 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="a4dfc-127">As a simple example, consider the page list below.</span></span> <span data-ttu-id="a4dfc-128">포함 한 `<span>` 인 요소는 `data-bind` authorName에 각각 텍스트 콘텐츠를 바인딩해야 있는지를 나타내는 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="a4dfc-128">It includes a `<span>` element with a `data-bind` attribute indicating that the text content should be bound to authorName.</span></span> <span data-ttu-id="a4dfc-129">다음 변수 viewModel 단일 속성으로 정의 된 JavaScript 블록에서 `authorName`, 일부 값으로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="a4dfc-129">Next, in a JavaScript block a variable viewModel is defined with a single property, `authorName`, set to some value.</span></span> <span data-ttu-id="a4dfc-130">에 대 한 호출 마지막으로, `ko.applyBindings` 설정 되 면이 viewModel 변수를 전달 합니다.</span><span class="sxs-lookup"><span data-stu-id="a4dfc-130">Finally, a call to `ko.applyBindings` is made, passing in this viewModel variable.</span></span>

```html
<html>
<head>
    <script type="text/javascript" src="lib/knockout/knockout.js"></script>
</head>
<body>
    <h1>Some Article</h1>
    <p>
        By <span data-bind="text: authorName"></span>
    </p>
    <script type="text/javascript">
      var viewModel = {
        authorName: 'Steve Smith'
      };
      ko.applyBindings(viewModel);
    </script>
</body>
</html>
```

<span data-ttu-id="a4dfc-131">내용 브라우저에서 볼 때는 <span> 요소 viewModel 변수에 값으로 대체 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a4dfc-131">When viewed in the browser, the content of the <span> element is replaced with the value in the viewModel variable:</span></span>

![knockout 단순 바인딩](knockout/_static/simple-binding-screenshot.png)

<span data-ttu-id="a4dfc-133">단방향 바인딩 간단한 작업을 했습니다.</span><span class="sxs-lookup"><span data-stu-id="a4dfc-133">We now have simple one-way binding working.</span></span> <span data-ttu-id="a4dfc-134">곳은 코드에서 우리 작성 않은 범위의 내용에 대 한 값을 할당 하는 JavaScript를 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="a4dfc-134">Notice that nowhere in the code did we write JavaScript to assign a value to the span's contents.</span></span> <span data-ttu-id="a4dfc-135">ViewModel를 조작 하려면 म म 수이 단계를 추가 하 고 HTML 입력된 textbox를 추가 거 같은 해당 값에 바인딩할 하므로:</span><span class="sxs-lookup"><span data-stu-id="a4dfc-135">If we want to manipulate the ViewModel, we can take this a step further and add an HTML input textbox, and bind to its value, like so:</span></span>

```html
<p>
    Author Name: <input type="text" data-bind="value: authorName" />
</p>
```

<span data-ttu-id="a4dfc-136">페이지를 다시 로드이 값이 실제로 입력된 상자에 바인딩되어 있음을 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="a4dfc-136">Reloading the page, we see that this value is indeed bound to the input box:</span></span>

![knockout 입력된 바인딩](knockout/_static/input-binding-screenshot.png)

<span data-ttu-id="a4dfc-138">그러나 텍스트 상자에 값을 변경 하면 해당 값은 `<span>` 요소는 변경 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a4dfc-138">However, if we change the value in the textbox, the corresponding value in the `<span>` element doesn't change.</span></span> <span data-ttu-id="a4dfc-139">그 이유는 무엇입니까?</span><span class="sxs-lookup"><span data-stu-id="a4dfc-139">Why not?</span></span>

<span data-ttu-id="a4dfc-140">문제는 아무것도 통보는 `<span>` 업데이트 하는 데 필요한 합니다.</span><span class="sxs-lookup"><span data-stu-id="a4dfc-140">The issue is that nothing notified the `<span>` that it needed to be updated.</span></span> <span data-ttu-id="a4dfc-141">간단히 ViewModel 업데이트 ViewModel의 속성 특수 형식에 래핑되는 경우가 아니면 충분 자체로 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="a4dfc-141">Simply updating the ViewModel isn't by itself sufficient, unless the ViewModel's properties are wrapped in a special type.</span></span> <span data-ttu-id="a4dfc-142">사용 해야 **관찰 가능 개체** 에서 변경한 내용으로 나타나는 순서 대로 자동으로 업데이트 해야 하는 모든 속성에 대해 ViewModel입니다.</span><span class="sxs-lookup"><span data-stu-id="a4dfc-142">We need to use **observables** in the ViewModel for any properties that need to have changes automatically updated as they occur.</span></span> <span data-ttu-id="a4dfc-143">ViewModel 사용 하도록 변경 하 여 `ko.observable("value")` 정당한 "value" 대신 ViewModel 변경 될 때마다 해당 값에 바인딩된 모든 HTML 요소가 업데이트 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a4dfc-143">By changing the ViewModel to use `ko.observable("value")` instead of just "value", the ViewModel will update any HTML elements that are bound to its value whenever a change occurs.</span></span> <span data-ttu-id="a4dfc-144">변경 내용이 바인딩된 요소가 입력할 때 표시 되지 않습니다 하므로 포커스를 잃게 될 때까지 입력된 상자가 값을 기준를 업데이트 하지 않으면 note 합니다.</span><span class="sxs-lookup"><span data-stu-id="a4dfc-144">Note that input boxes don't update their value until they lose focus, so you won't see changes to bound elements as you type.</span></span>

> [!NOTE]
> <span data-ttu-id="a4dfc-145">키를 누를 때마다은 단순히 추가 후에 실시간 업데이트에 대 한 지원 추가 `valueUpdate: "afterkeydown"` 에 `data-bind` 특성의 내용입니다.</span><span class="sxs-lookup"><span data-stu-id="a4dfc-145">Adding support for live updating after each keypress is simply a matter of adding `valueUpdate: "afterkeydown"` to the `data-bind` attribute's contents.</span></span> <span data-ttu-id="a4dfc-146">사용 하 여이 동작을 얻을 수도 있습니다 `data-bind="textInput: authorName"` 값의 즉시 업데이트를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="a4dfc-146">You can also get this behavior by using `data-bind="textInput: authorName"` to get instant updates of values.</span></span> 

<span data-ttu-id="a4dfc-147">우리의 viewModel, ko.observable 사용 하도록 업데이트 한 후:</span><span class="sxs-lookup"><span data-stu-id="a4dfc-147">Our viewModel, after updating it to use ko.observable:</span></span>

```javascript
var viewModel = {
  authorName: ko.observable('Steve Smith')
};
ko.applyBindings(viewModel);
```

<span data-ttu-id="a4dfc-148">Knockout 바인딩의 다른 종류의 숫자를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="a4dfc-148">Knockout supports a number of different kinds of bindings.</span></span> <span data-ttu-id="a4dfc-149">에 바인딩하는 방법을 지금까지 살펴본 `text` 및 `value`합니다.</span><span class="sxs-lookup"><span data-stu-id="a4dfc-149">So far we've seen how to bind to `text` and to `value`.</span></span> <span data-ttu-id="a4dfc-150">지정 된 모든 특성을 바인딩할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a4dfc-150">You can also bind to any given attribute.</span></span> <span data-ttu-id="a4dfc-151">예를 들어, 앵커 태그를 사용 하 여 하이퍼링크를 만들려면는 `src` viewModel에 특성을 바인딩할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a4dfc-151">For instance, to create a hyperlink with an anchor tag, the `src` attribute can be bound to the viewModel.</span></span> <span data-ttu-id="a4dfc-152">Knockout 함수에 대 한 바인딩을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="a4dfc-152">Knockout also supports binding to functions.</span></span> <span data-ttu-id="a4dfc-153">이것을 보여주기 위해 viewModel 작성자의 twitter 핸들을 포함 하도록 업데이트 하 고 작성자의 twitter 페이지에 대 한 링크로 twitter 핸들을 표시 해 보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="a4dfc-153">To demonstrate this, let's update the viewModel to include the author's twitter handle, and display the twitter handle as a link to the author's twitter page.</span></span> <span data-ttu-id="a4dfc-154">다음 세 단계로 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="a4dfc-154">We'll do this in three stages.</span></span>

<span data-ttu-id="a4dfc-155">먼저, 작성자의 이름 뒤에 오는 괄호로 보여 주 겠지만 하이퍼링크를 표시 하는 HTML을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="a4dfc-155">First, add the HTML to display the hyperlink, which we'll show in parentheses after the author's name:</span></span>

```html
<h1>Some Article</h1>
<p>
    By <span data-bind="text: authorName"></span>
    (<a data-bind="attr: { href: twitterUrl}, text: twitterAlias" ></a>)
</p>
```

<span data-ttu-id="a4dfc-156">다음으로 viewModel twitterUrl 및 twitterAlias 속성을 포함 하도록 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="a4dfc-156">Next, update the viewModel to include the twitterUrl and twitterAlias properties:</span></span>

```javascript
var viewModel = {
  authorName: ko.observable('Steve Smith'),
  twitterAlias: ko.observable('@ardalis'),
  twitterUrl: ko.computed(function() {
    return "https://twitter.com/";
  }, this)
};
ko.applyBindings(viewModel);
```

<span data-ttu-id="a4dfc-157">이 시점에서 우리 업데이트 하지 않은 아직이 twitter 별칭에 대 한 올바른 URL로 이동 하려면 twitterUrl – twitter.com를 가리키고 바로 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="a4dfc-157">Notice that at this point we haven't yet updated the twitterUrl to go to the correct URL for this twitter alias – it's just pointing at twitter.com.</span></span> <span data-ttu-id="a4dfc-158">또한 새 Knockout 함수를 사용 하는 것을 볼 `computed`, twitterUrl에 대 한 합니다.</span><span class="sxs-lookup"><span data-stu-id="a4dfc-158">Also notice that we're using a new Knockout function, `computed`, for twitterUrl.</span></span> <span data-ttu-id="a4dfc-159">이 변경 하는 경우 UI 요소를 알려 주는 observable 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="a4dfc-159">This is an observable function that will notify any UI elements if it changes.</span></span> <span data-ttu-id="a4dfc-160">그러나 viewModel의 다른 속성에 대 한 액세스 권한이 있으면 변경 해야는 viewModel 생성 방법을 각 속성은 자체 문 수 있도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="a4dfc-160">However, for it to have access to other properties in the viewModel, we need to change how we are creating the viewModel, so that each property is its own statement.</span></span>

<span data-ttu-id="a4dfc-161">수정 된 viewModel 선언은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="a4dfc-161">The revised viewModel declaration is shown below.</span></span> <span data-ttu-id="a4dfc-162">이제 함수로 선언 합니다.</span><span class="sxs-lookup"><span data-stu-id="a4dfc-162">It is now declared as a function.</span></span> <span data-ttu-id="a4dfc-163">각 속성이 자체 이제 문은, 세미콜론으로 끝나는 인지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="a4dfc-163">Notice that each property is its own statement now, ending with a semicolon.</span></span> <span data-ttu-id="a4dfc-164">또한 확인 twitterAlias 속성 값에 액세스 하려면 해당 참조 ()를 포함 하므로 실행 해야 했습니다.</span><span class="sxs-lookup"><span data-stu-id="a4dfc-164">Also notice that to access the twitterAlias property value, we need to execute it, so its reference includes ().</span></span>

```javascript
function viewModel() {
  this.authorName = ko.observable('Steve Smith');
  this.twitterAlias = ko.observable('@ardalis');

  this.twitterUrl = ko.computed(function() {
    return "https://twitter.com/" + this.twitterAlias().replace('@','');
  }, this)
};
ko.applyBindings(viewModel);
```

<span data-ttu-id="a4dfc-165">결과 브라우저에서 예상 대로 작동 합니다.</span><span class="sxs-lookup"><span data-stu-id="a4dfc-165">The result works as expected in the browser:</span></span>

![knockout 하이퍼링크](knockout/_static/hyperlink-screenshot.png)

<span data-ttu-id="a4dfc-167">Knockout은 click 이벤트와 같은 특정 UI 요소 이벤트에 대 한 바인딩을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="a4dfc-167">Knockout also supports binding to certain UI element events, such as the click event.</span></span> <span data-ttu-id="a4dfc-168">이 옵션을 사용 하면 쉽고 선언적으로 UI 요소를 응용 프로그램의 viewModel 내에서 함수를 바인딩할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a4dfc-168">This allows you to easily and declaratively bind UI elements to functions within the application's viewModel.</span></span> <span data-ttu-id="a4dfc-169">단추를 추가할 수 있는 간단한 예로,를 클릭 하면 저자의 twitterAlias 모두 대문자로 되도록 수정 합니다.</span><span class="sxs-lookup"><span data-stu-id="a4dfc-169">As a simple example, we can add a button that, when clicked, modifies the author's twitterAlias to be all caps.</span></span>

<span data-ttu-id="a4dfc-170">첫째, 단추 추가, 단추의 바인딩을 클릭 이벤트와 viewModel에 추가 하 여 함수 이름을 참조 합니다.</span><span class="sxs-lookup"><span data-stu-id="a4dfc-170">First, we add the button, binding to the button's click event, and referencing the function name we're going to add to the viewModel:</span></span>

```html
<p>
    <button data-bind="click: capitalizeTwitterAlias">Capitalize</button>
</p>
```

<span data-ttu-id="a4dfc-171">그런 다음, viewModel에 함수를 추가 하 고 viewModel의 상태를 수정할를 연결 합니다.</span><span class="sxs-lookup"><span data-stu-id="a4dfc-171">Then, add the function to the viewModel, and wire it up to modify the viewModel's state.</span></span> <span data-ttu-id="a4dfc-172">고 해당 데이터베이스 twitterAlias 속성을 새 값을 설정 하려면 우리 메서드로 호출할 새 값을 전달 합니다.</span><span class="sxs-lookup"><span data-stu-id="a4dfc-172">Notice that to set a new value to the twitterAlias property, we call it as a method and pass in the new value.</span></span>

```javascript
function viewModel() {
  this.authorName = ko.observable('Steve Smith');
  this.twitterAlias = ko.observable('@ardalis');

  this.twitterUrl = ko.computed(function() {
    return "https://twitter.com/" + this.twitterAlias().replace('@','');
  }, this);

  this.capitalizeTwitterAlias = function() {
    var currentValue = this.twitterAlias();
    this.twitterAlias(currentValue.toUpperCase());
  }
};
ko.applyBindings(viewModel);
```

<span data-ttu-id="a4dfc-173">코드를 실행 하 고 단추를 클릭 하면 예상 대로 표시 되는 링크를 수정 합니다.</span><span class="sxs-lookup"><span data-stu-id="a4dfc-173">Running the code and clicking the button modifies the displayed link as expected:</span></span>

![하이퍼링크를 대문자로](knockout/_static/hyperlink-caps-screenshot.png)

## <a name="control-flow"></a><span data-ttu-id="a4dfc-175">제어 흐름</span><span class="sxs-lookup"><span data-stu-id="a4dfc-175">Control flow</span></span>

<span data-ttu-id="a4dfc-176">Knockout 조건부 및 반복 작업을 수행할 수 있는 바인딩이 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a4dfc-176">Knockout includes bindings that can perform conditional and looping operations.</span></span> <span data-ttu-id="a4dfc-177">반복 작업은 UI 목록, 메뉴 및 표 또는 테이블에 데이터의 목록을 바인딩에 특히 유용 합니다.</span><span class="sxs-lookup"><span data-stu-id="a4dfc-177">Looping operations are especially useful for binding lists of data to UI lists, menus, and grids or tables.</span></span> <span data-ttu-id="a4dfc-178">Foreach 바인딩에서 배열에 대해 반복 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a4dfc-178">The foreach binding will iterate over an array.</span></span> <span data-ttu-id="a4dfc-179">Observable 배열과 함께 사용 하는 경우 항목을 추가 하거나 UI 트리의 모든 요소를 다시 만들지 않고는 배열에서 제거할 때 UI 요소는 자동 업데이트 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a4dfc-179">When used with an observable array, it will automatically update the UI elements when items are added or removed from the array, without re-creating every element in the UI tree.</span></span> <span data-ttu-id="a4dfc-180">다음 예제에서는 게임 결과의 observable 배열을 포함 하는 새 viewModel를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="a4dfc-180">The following example uses a new viewModel which includes an observable array of game results.</span></span> <span data-ttu-id="a4dfc-181">사용 하 여 두 개의 열이 있는 간단한 테이블에 바인딩되어는 `foreach` 에 있는 바인딩는 `<tbody>` 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="a4dfc-181">It is bound to a simple table with two columns using a `foreach` binding on the `<tbody>` element.</span></span> <span data-ttu-id="a4dfc-182">각 `<tr>` 요소 내에서 `<tbody>` gameResults 컬렉션의 요소에 바인딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="a4dfc-182">Each `<tr>` element within `<tbody>` will be bound to an element of the gameResults collection.</span></span>

```html
<h1>Record</h1>
<table>
    <thead>
        <tr>
            <th>Opponent</th>
            <th>Result</th>
        </tr>
    </thead>
    <tbody data-bind="foreach: gameResults">
        <tr>
            <td data-bind="text:opponent"></td>
            <td data-bind="text:result"></td>
        </tr>
    </tbody>
</table>
<script type="text/javascript">
  function GameResult(opponent, result) {
    var self = this;
    self.opponent = opponent;
    self.result = ko.observable(result);
  }

  function ViewModel() {
    var self = this;

    self.resultChoices = ["Win", "Loss", "Tie"];

    self.gameResults = ko.observableArray([
      new GameResult("Brendan", self.resultChoices[0]),
      new GameResult("Brendan", self.resultChoices[0]),
      new GameResult("Michelle", self.resultChoices[1])
    ]);
  };
  ko.applyBindings(new ViewModel);
</script>
```

<span data-ttu-id="a4dfc-183">이 시간을 사용 하는지 ViewModel 대문자 "V"로 (applyBindings 호출)에 "new"를 사용 하 여 생성 하는 것으로 예상 하기 때문에 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a4dfc-183">Notice that this time we're using ViewModel with a capital “V" because we expect to construct it using “new" (in the applyBindings call).</span></span> <span data-ttu-id="a4dfc-184">를 실행 하면 페이지 결과 다음과 같은 출력 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a4dfc-184">When executed, the page results in the following output:</span></span>

![knockout 레코드 뷰 모델](knockout/_static/record-screenshot.png)

<span data-ttu-id="a4dfc-186">Observable 컬렉션 작동을 보여 주기 위해 좀 더 많은 기능을 추가 해 보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="a4dfc-186">To demonstrate that the observable collection is working, let's add a bit more functionality.</span></span> <span data-ttu-id="a4dfc-187">다른 ViewModel, 게임 결과 기록 하는 기능이 포함 하 고 단추를이 새로운 함수를 작성 하려면 몇 가지 UI를 추가 합니다 수 했습니다.</span><span class="sxs-lookup"><span data-stu-id="a4dfc-187">We can include the ability to record the results of another game to the ViewModel, and then add a button and some UI to work with this new function.</span></span>  <span data-ttu-id="a4dfc-188">첫째, addResult 메서드를 만들어 보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="a4dfc-188">First, let's create the addResult method:</span></span>

```javascript
// add this to ViewModel()
self.addResult = function() {
  self.gameResults.push(new GameResult("", self.resultChoices[0]));
}
```

<span data-ttu-id="a4dfc-189">단추를 사용 하 여이 메서드를 바인딩하는 `click` 바인딩:</span><span class="sxs-lookup"><span data-stu-id="a4dfc-189">Bind this method to a button using the `click` binding:</span></span>

```html
<button data-bind="click: addResult">Add New Result</button>
```

<span data-ttu-id="a4dfc-190">브라우저에서 페이지를 열고 단추를 두 번 클릭 하 여 각 번의 클릭으로 새 테이블 행의 결과:</span><span class="sxs-lookup"><span data-stu-id="a4dfc-190">Open the page in the browser and click the button a couple of times, resulting in a new table row with each click:</span></span>

![결과 추가 합니다.](knockout/_static/record-addresult-screenshot.png)

<span data-ttu-id="a4dfc-192">별도 양식 또는 인라인 일반적으로 UI에서 새 레코드를 추가 하도록 지 원하는 몇 가지가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a4dfc-192">There are a few ways to support adding new records in the UI, typically either inline or in a separate form.</span></span> <span data-ttu-id="a4dfc-193">전체로 편집할 수 있도록 텍스트 상자 및 dropdownlist 활용을 사용 하는 테이블 쉽게 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a4dfc-193">We can easily modify the table to use textboxes and dropdownlists so that the whole thing is editable.</span></span> <span data-ttu-id="a4dfc-194">변경 된 `<tr>` 요소와 같이:</span><span class="sxs-lookup"><span data-stu-id="a4dfc-194">Just change the `<tr>` element as shown:</span></span>

```html
<tbody data-bind="foreach: gameResults">
    <tr>
      <td><input data-bind="value:opponent" /></td>
      <td><select data-bind="options: $root.resultChoices, value:result, optionsText: $data"></select></td>
    </tr>
</tbody>
```

<span data-ttu-id="a4dfc-195">`$root` 가능한 선택 항목 노출 되는 ViewModel, 루트를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="a4dfc-195">Note that `$root` refers to the root ViewModel, which is where the possible choices are exposed.</span></span> <span data-ttu-id="a4dfc-196">`$data`각각은 단순 문자열 resultChoices 배열의 개별 요소를 참조 하는이 경우에서-지정된 된 컨텍스트 내에서 모든 현재 모델은를 참조 합니다.</span><span class="sxs-lookup"><span data-stu-id="a4dfc-196">`$data` refers to whatever the current model is within a given context - in this case it refers to an individual element of the resultChoices array, each of which is a simple string.</span></span>

<span data-ttu-id="a4dfc-197">이러한 변경으로 인해 전체 표 편집 가능한 상태가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a4dfc-197">With this change, the entire grid becomes editable:</span></span>

![편집 가능한 표](knockout/_static/editable-grid-screenshot.png)

<span data-ttu-id="a4dfc-199">Knockout 사용 하지 않는 것을 우리 얻을 수이 모든 jQuery를 사용 하 여 되지만 하지는 대개 거의 효율적입니다.</span><span class="sxs-lookup"><span data-stu-id="a4dfc-199">If we weren't using Knockout, we could achieve all of this using jQuery, but most likely it would not be nearly as efficient.</span></span> <span data-ttu-id="a4dfc-200">Knockout는 UI 요소에 바인딩된 ViewModel 항목에 해당 데이터를 추적 하 고만 추가, 제거 또는 업데이트 하는 이러한 요소를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="a4dfc-200">Knockout tracks which bound data items in the ViewModel correspond to which UI elements, and only updates those elements that need to be added, removed, or updated.</span></span> <span data-ttu-id="a4dfc-201">JQuery 또는 직접 DOM 조작을 사용 하 여 직접이 위해 많은 노력을 걸리는 이며 경우에 다음 (예:-업체 레코드)은 테이블의 데이터를 기반으로 집계 결과 표시 하 려 했습니다는 해야 한 번 더 반복 하 고이 통해 고 구문 분석은 HTML 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="a4dfc-201">It would take significant effort to achieve this ourselves using jQuery or direct DOM manipulation, and even then if we then wanted to display aggregate results (such as a win-loss record) based on the table's data, we would need to once more loop through it and parse the HTML elements.</span></span>  <span data-ttu-id="a4dfc-202">Knockout와-업체 레코드를 표시 하기가 쉽습니다.</span><span class="sxs-lookup"><span data-stu-id="a4dfc-202">With Knockout, displaying the win-loss record is trivial.</span></span> <span data-ttu-id="a4dfc-203">ViewModel 자체 내에서 계산을 수행 하 고 간단한 텍스트 바인딩과 함께 표시할 수 있습니다 및 `<span>`합니다.</span><span class="sxs-lookup"><span data-stu-id="a4dfc-203">We can perform the calculations within the ViewModel itself, and then display it with a simple text binding and a `<span>`.</span></span>

<span data-ttu-id="a4dfc-204">업체 레코드 문자열을 작성 하려면 계산된 관찰 가능 개체를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a4dfc-204">To build the win-loss record string, we can use a computed observable.</span></span> <span data-ttu-id="a4dfc-205">ViewModel 내에서 예측 가능한 속성에 대 한 참조는 함수 호출, 그렇지 않으면은 검색 하지 않지만 관찰 가능 개체의 값 (예: `gameResults()` 하지 `gameResults` 표시 된 코드에서):</span><span class="sxs-lookup"><span data-stu-id="a4dfc-205">Note that references to observable properties within the ViewModel must be function calls, otherwise they will not retrieve the value of the observable (i.e. `gameResults()` not `gameResults` in the code shown):</span></span>

```javascript
self.displayRecord = ko.computed(function () {
  var wins = self.gameResults().filter(function (value) { return value.result() == "Win"; }).length;
  var losses = self.gameResults().filter(function (value) { return value.result() == "Loss"; }).length;
  var ties = self.gameResults().filter(function (value) { return value.result() == "Tie"; }).length;
  return wins + " - " + losses + " - " + ties;
}, this);
```

<span data-ttu-id="a4dfc-206">이 함수는 범위 내에서 바인딩할는 `<h1>` 페이지의 위쪽에 요소:</span><span class="sxs-lookup"><span data-stu-id="a4dfc-206">Bind this function to a span within the `<h1>` element at the top of the page:</span></span>

```html
<h1>Record <span data-bind="text: displayRecord"></span></h1>
```

<span data-ttu-id="a4dfc-207">결과:</span><span class="sxs-lookup"><span data-stu-id="a4dfc-207">The result:</span></span>

![업체](knockout/_static/record-winloss-screenshot.png)

<span data-ttu-id="a4dfc-209">행 추가 또는 모든 행의 결과 열에서 선택된 된 요소를 수정 하는 창의 상단 부분에 표시 된 레코드가 업데이트 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a4dfc-209">Adding rows or modifying the selected element in any row's Result column will update the record shown at the top of the window.</span></span>

<span data-ttu-id="a4dfc-210">값에 바인딩 외에도 거의 모든 올바른 JavaScript 식 바인딩에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a4dfc-210">In addition to binding to values, you can also use almost any legal JavaScript expression within a binding.</span></span> <span data-ttu-id="a4dfc-211">예를 들어, UI 요소에만 값을 특정 임계값을 초과 하는 경우와 같은 특정 조건에서 나타나야 하는 경우 지정할 수 있습니다이 논리적 바인딩 식 내에서.</span><span class="sxs-lookup"><span data-stu-id="a4dfc-211">For example, if a UI element should only appear under certain conditions, such as when a value exceeds a certain threshold, you can specify this logically within the binding expression:</span></span>

```html
<div data-bind="visible: customerValue > 100"></div>
```

<span data-ttu-id="a4dfc-212">이 `<div>` 만 표시 됩니다는 customerValue은 100을 초과 하는 경우.</span><span class="sxs-lookup"><span data-stu-id="a4dfc-212">This `<div>` will only be visible when the customerValue is over 100.</span></span>

## <a name="templates"></a><span data-ttu-id="a4dfc-213">템플릿</span><span class="sxs-lookup"><span data-stu-id="a4dfc-213">Templates</span></span>

<span data-ttu-id="a4dfc-214">Knockout는 쉽게 사용자 동작에서 UI를 분리 하거나 증분식으로 UI 요소를 필요에 따라 대형 응용 프로그램 로드 수 있도록 서식 파일을 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="a4dfc-214">Knockout has support for templates, so that you can easily separate your UI from your behavior, or incrementally load UI elements into a large application on demand.</span></span> <span data-ttu-id="a4dfc-215">단순히 HTML 축소 것으로 밀어 넣는 서식 파일에 데이터 바인딩 호출에서 이름으로 서식 파일을 지정 하 여 각 행 자체 서식 파일을 만들려면 이전 예제의 업데이트 하면 `<tbody>`합니다.</span><span class="sxs-lookup"><span data-stu-id="a4dfc-215">We can update our previous example to make each row its own template by simply pulling the HTML out into a template and specifying the template by name in the data-bind call on `<tbody>`.</span></span>

```html
<tbody data-bind="template: { name: 'rowTemplate', foreach: gameResults }">
</tbody>
<script type="text/html" id="rowTemplate">
  <tr>
      <td><input data-bind="value:opponent" /></td>
      <td><select data-bind="options: $root.resultChoices, value:result, optionsText: $data"></select></td>
  </tr>
</script>
```

<span data-ttu-id="a4dfc-216">Knockout은 jQuery.tmpl 라이브러리와 같은 다른 템플릿 엔진와 Underscore.js의 템플릿 엔진을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="a4dfc-216">Knockout also supports other templating engines, such as the jQuery.tmpl library and Underscore.js's templating engine.</span></span>

## <a name="components"></a><span data-ttu-id="a4dfc-217">구성 요소</span><span class="sxs-lookup"><span data-stu-id="a4dfc-217">Components</span></span>

<span data-ttu-id="a4dfc-218">구성 요소를 사용 하 여 구성 하 고 UI 코드가 종속 되어 있는 ViewModel 데이터와 함께 일반적으로 UI 코드를 다시 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a4dfc-218">Components allow you to organize and reuse UI code, usually along with the ViewModel data on which the UI code depends.</span></span> <span data-ttu-id="a4dfc-219">구성 요소를 만들려면 단순히 하려면 해당 템플릿 및 해당 viewModel 지정 하 고 이름을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="a4dfc-219">To create a component, you simply need to specify its template and its viewModel, and give it a name.</span></span> <span data-ttu-id="a4dfc-220">이를 위해 `ko.components.register()`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="a4dfc-220">This is done by calling `ko.components.register()`.</span></span> <span data-ttu-id="a4dfc-221">서식 파일 및 viewmodel 인라인을 정의 하는 것 외에도 로드 될 수와 같은 라이브러리를 사용 하 여 외부 파일에서 *require.js*, 매우 명확 하 고 효율적인 코드의 결과입니다.</span><span class="sxs-lookup"><span data-stu-id="a4dfc-221">In addition to defining the templates and viewmodel inline, they can be loaded from external files using a library like *require.js*, resulting in very clean and efficient code.</span></span>

## <a name="communicating-with-apis"></a><span data-ttu-id="a4dfc-222">Api와의 통신</span><span class="sxs-lookup"><span data-stu-id="a4dfc-222">Communicating with APIs</span></span>

<span data-ttu-id="a4dfc-223">Knockout은 JSON 형식의 데이터를 작업할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a4dfc-223">Knockout can work with any data in JSON format.</span></span> <span data-ttu-id="a4dfc-224">검색 Knockout를 사용 하 여 데이터를 저장 하는 일반적인 방법은 수 있도록 지 원하는 jQuery 인는 `$.getJSON()` 데이터를 검색 하는 함수 및 `$.post()` API 끝점에는 브라우저에서 데이터를 보내는 방법을 합니다.</span><span class="sxs-lookup"><span data-stu-id="a4dfc-224">A common way to retrieve and save data using Knockout is with jQuery, which supports the `$.getJSON()` function to retrieve data, and the `$.post()` method to send data from the browser to an API endpoint.</span></span> <span data-ttu-id="a4dfc-225">물론, JSON 데이터를 주고받을 수를 다른 방식으로 원하는 경우 Knockout 작동할지 것도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a4dfc-225">Of course, if you prefer a different way to send and receive JSON data, Knockout will work with it as well.</span></span>

## <a name="summary"></a><span data-ttu-id="a4dfc-226">요약</span><span class="sxs-lookup"><span data-stu-id="a4dfc-226">Summary</span></span>

<span data-ttu-id="a4dfc-227">Knockout UI 요소는 ViewModel에 정의 된 클라이언트 응용 프로그램의 현재 상태에 바인딩하는 간단 하 고 세련 된 방법을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="a4dfc-227">Knockout provides a simple, elegant way to bind UI elements to the current state of the client application, defined in a ViewModel.</span></span> <span data-ttu-id="a4dfc-228">Knockout의 바인딩 구문을 처리 해야 하는 HTML 요소에 적용 된 데이터 바인딩 특성을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="a4dfc-228">Knockout's binding syntax uses the data-bind attribute, applied to HTML elements that are to be processed.</span></span> <span data-ttu-id="a4dfc-229">Knockout를 효율적으로 렌더링 및 UI 요소를 추적 하 여 큰 데이터 집합을 업데이트할 수 이며 요소에 영향을 받는 변경 사항을 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="a4dfc-229">Knockout is able to efficiently render and update large data sets by tracking UI elements and only processing changes to affected elements.</span></span> <span data-ttu-id="a4dfc-230">대규모 응용 프로그램 템플릿 및 외부 파일에서 필요에 따라 로드할 수 있는 구성 요소를 사용 하 여 UI 논리 분리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a4dfc-230">Large applications can break up UI logic using templates and components, which can be loaded on demand from external files.</span></span> <span data-ttu-id="a4dfc-231">현재 버전 3 Knockout 리치 클라이언트 상호 작용을 필요로 하는 웹 응용 프로그램을 향상 시킬 수 있는 안정적인 JavaScript 라이브러리입니다.</span><span class="sxs-lookup"><span data-stu-id="a4dfc-231">Currently version 3, Knockout is a stable JavaScript library that can improve web applications that require rich client interactivity.</span></span>
