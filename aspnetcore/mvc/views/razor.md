---
title: "ASP.NET Core에 대 한 razor 구문 참조"
author: rick-anderson
description: "Razor 구문에 자세히 설명"
keywords: Razor, ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 07/04/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/views/razor
ms.openlocfilehash: fff2f98592473a9baf6a2d4e360fec3026b7210d
ms.sourcegitcommit: 67f54fabbfa4e3942f5bfe1f8a7fdfe4a7a75358
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/19/2017
---
# <a name="razor-syntax-for-aspnet-core"></a><span data-ttu-id="3c0f7-104">ASP.NET Core에 대 한 razor 구문</span><span class="sxs-lookup"><span data-stu-id="3c0f7-104">Razor syntax for ASP.NET Core</span></span>

<span data-ttu-id="3c0f7-105">여 [Taylor Mullen](https://twitter.com/ntaylormullen) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="3c0f7-105">By [Taylor Mullen](https://twitter.com/ntaylormullen) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

## <a name="what-is-razor"></a><span data-ttu-id="3c0f7-106">Razor 란?</span><span class="sxs-lookup"><span data-stu-id="3c0f7-106">What is Razor?</span></span>

<span data-ttu-id="3c0f7-107">Razor은 웹 페이지에 서버 기반 코드를 포함 하기 위한 태그 구문입니다.</span><span class="sxs-lookup"><span data-stu-id="3c0f7-107">Razor is a markup syntax for embedding server based code into web pages.</span></span> <span data-ttu-id="3c0f7-108">C# 및 HTML Razor 태그는 Razor 구문을 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="3c0f7-108">The Razor syntax consists of Razor markup, C# and HTML.</span></span> <span data-ttu-id="3c0f7-109">Razor를 일반적으로 들어 있는 파일을 *.cshtml* 파일 확장명입니다.</span><span class="sxs-lookup"><span data-stu-id="3c0f7-109">Files containing Razor generally have a *.cshtml* file extension.</span></span>

## <a name="rendering-html"></a><span data-ttu-id="3c0f7-110">HTML 렌더링</span><span class="sxs-lookup"><span data-stu-id="3c0f7-110">Rendering HTML</span></span>

<span data-ttu-id="3c0f7-111">기본 Razor 언어 HTML입니다.</span><span class="sxs-lookup"><span data-stu-id="3c0f7-111">The default Razor language is HTML.</span></span> <span data-ttu-id="3c0f7-112">Razor의 HTML 렌더링는 HTML 파일에 차이가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="3c0f7-112">Rendering HTML from Razor is no different than in an HTML file.</span></span> <span data-ttu-id="3c0f7-113">다음 태그로 Razor 파일:</span><span class="sxs-lookup"><span data-stu-id="3c0f7-113">A Razor file with the following markup:</span></span>

```html
<p>Hello World</p>
   ```

<span data-ttu-id="3c0f7-114">가 변경 하지로 렌더링 `<p>Hello World</p>` 서버입니다.</span><span class="sxs-lookup"><span data-stu-id="3c0f7-114">Is rendered unchanged as `<p>Hello World</p>` by the server.</span></span>

## <a name="razor-syntax"></a><span data-ttu-id="3c0f7-115">Razor 구문</span><span class="sxs-lookup"><span data-stu-id="3c0f7-115">Razor syntax</span></span>

<span data-ttu-id="3c0f7-116">Razor C# 지원 및 사용 하 여는 `@` C#으로 HTML에서 전환 기호입니다.</span><span class="sxs-lookup"><span data-stu-id="3c0f7-116">Razor supports C# and uses the `@` symbol to transition from HTML to C#.</span></span> <span data-ttu-id="3c0f7-117">Razor C# 식을 계산 하 고 HTML 출력에이 렌더링 합니다.</span><span class="sxs-lookup"><span data-stu-id="3c0f7-117">Razor evaluates C# expressions and renders them in the HTML output.</span></span> <span data-ttu-id="3c0f7-118">Razor는 HTML에서 C# 또는 Razor 관련 태그로 전환될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3c0f7-118">Razor can transition from HTML into C# or into Razor-specific markup.</span></span> <span data-ttu-id="3c0f7-119">경우는 `@` 기호 뒤는 [Razor 예약 키워드](#razor-reserved-keywords) Razor 특정 태그에 전환, 그렇지 않으면 일반 C# 식으로 전환 합니다.</span><span class="sxs-lookup"><span data-stu-id="3c0f7-119">When an `@` symbol is followed by a [Razor reserved keyword](#razor-reserved-keywords) it transitions into Razor-specific markup, otherwise it transitions into plain C#.</span></span>

<a name=escape-at-label></a>

<span data-ttu-id="3c0f7-120">포함 된 HTML `@` 기호는 두 번째 이스케이프 되어야 할 수 있습니다 `@` 기호입니다.</span><span class="sxs-lookup"><span data-stu-id="3c0f7-120">HTML containing `@` symbols may need to be escaped with a second `@` symbol.</span></span> <span data-ttu-id="3c0f7-121">예:</span><span class="sxs-lookup"><span data-stu-id="3c0f7-121">For example:</span></span>

```html
<p>@@Username</p>
   ```

<span data-ttu-id="3c0f7-122">다음 HTML을 렌더링 합니다.</span><span class="sxs-lookup"><span data-stu-id="3c0f7-122">would render the following HTML:</span></span>

```html
<p>@Username</p>
   ```

<a name=razor-email-ref></a>

<span data-ttu-id="3c0f7-123">HTML 특성 및 전자 메일 주소를 포함 하는 내용을 처리 하지 않습니다는 `@` 전환 문자로 기호입니다.</span><span class="sxs-lookup"><span data-stu-id="3c0f7-123">HTML attributes and content containing email addresses don’t treat the `@` symbol as a transition character.</span></span>

   `<a href="mailto:Support@contoso.com">Support@contoso.com</a>`

## <a name="implicit-razor-expressions"></a><span data-ttu-id="3c0f7-124">암시적 Razor 식</span><span class="sxs-lookup"><span data-stu-id="3c0f7-124">Implicit Razor expressions</span></span>

<span data-ttu-id="3c0f7-125">암시적 Razor 식은로 시작 `@` C# 코드에 나옵니다.</span><span class="sxs-lookup"><span data-stu-id="3c0f7-125">Implicit Razor expressions start with `@` followed by C# code.</span></span> <span data-ttu-id="3c0f7-126">예:</span><span class="sxs-lookup"><span data-stu-id="3c0f7-126">For example:</span></span>

```html
<p>@DateTime.Now</p>
<p>@DateTime.IsLeapYear(2016)</p>
```

<span data-ttu-id="3c0f7-127">C#을 제외한 `await` 키워드 암시적 식 공백을 포함 해서는 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="3c0f7-127">With the exception of the C# `await` keyword implicit expressions must not contain spaces.</span></span> <span data-ttu-id="3c0f7-128">예를 들어 C# 문에 명확한 끝으로 공간 intermingle 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3c0f7-128">For example, you can intermingle spaces as long as the C# statement has a clear ending:</span></span>

```html
<p>@await DoSomething("hello", "world")</p>
```

## <a name="explicit-razor-expressions"></a><span data-ttu-id="3c0f7-129">명시적 Razor 식</span><span class="sxs-lookup"><span data-stu-id="3c0f7-129">Explicit Razor expressions</span></span>

<span data-ttu-id="3c0f7-130">명시적 Razor 식은 이루어져는 @ 균형 잡힌 괄호 기호입니다.</span><span class="sxs-lookup"><span data-stu-id="3c0f7-130">Explicit Razor expressions consists of an @ symbol with balanced parenthesis.</span></span> <span data-ttu-id="3c0f7-131">예를 들어, 렌더링 시간을 지난 주:</span><span class="sxs-lookup"><span data-stu-id="3c0f7-131">For example, to render last week's time:</span></span>

```html
<p>Last week this time: @(DateTime.Now - TimeSpan.FromDays(7))</p>
```

<span data-ttu-id="3c0f7-132">내에서 모든 콘텐츠는 @ () 괄호 평가 되 고 출력에 렌더링 합니다.</span><span class="sxs-lookup"><span data-stu-id="3c0f7-132">Any content within the @() parenthesis is evaluated and rendered to the output.</span></span>

<span data-ttu-id="3c0f7-133">일반적으로 암시적 식 공백을 포함할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="3c0f7-133">Implicit expressions generally cannot contain spaces.</span></span> <span data-ttu-id="3c0f7-134">예를 들어 아래 코드에서 1 주일은 현재 시간에서 차감 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3c0f7-134">For example, in the code below, one week is not subtracted from the current time:</span></span>

[!code-html[Main](razor/sample/Views/Home/Contact.cshtml?range=20)]

<span data-ttu-id="3c0f7-135">다음과 같은 HTML을 렌더링 됩니다.</span><span class="sxs-lookup"><span data-stu-id="3c0f7-135">Which renders the following HTML:</span></span>

```html
<p>Last week: 7/7/2016 4:39:52 PM - TimeSpan.FromDays(7)</p>
   ```

<span data-ttu-id="3c0f7-136">연결 된 식 결과 텍스트에 명시적 식을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3c0f7-136">You can use an explicit expression to concatenate text with an expression result:</span></span>

<!-- literal_block {"ids": [], "linenos": false, "xml:space": "preserve", "language": "none", "highlight_args": {"hl_lines": [5]}} -->

```none
@{
    var joe = new Person("Joe", 33);
 }

<p>Age@(joe.Age)</p>
```

<span data-ttu-id="3c0f7-137">명시적 식 없이 `<p>Age@joe.Age</p>` 전자 메일 주소로 간주 됩니다 및 `<p>Age@joe.Age</p>` 는 렌더링 됩니다.</span><span class="sxs-lookup"><span data-stu-id="3c0f7-137">Without the explicit expression, `<p>Age@joe.Age</p>` would be treated as an email address and `<p>Age@joe.Age</p>` would be rendered.</span></span> <span data-ttu-id="3c0f7-138">명시적 식을으로 쓸 때 `<p>Age33</p>` 렌더링 됩니다.</span><span class="sxs-lookup"><span data-stu-id="3c0f7-138">When written as an explicit expression, `<p>Age33</p>` is rendered.</span></span>

<a name=expression-encoding-label></a>

## <a name="expression-encoding"></a><span data-ttu-id="3c0f7-139">식 인코딩</span><span class="sxs-lookup"><span data-stu-id="3c0f7-139">Expression encoding</span></span>

<span data-ttu-id="3c0f7-140">C# 식을 문자열로 평가 하는 인코딩된 HTML입니다.</span><span class="sxs-lookup"><span data-stu-id="3c0f7-140">C# expressions that evaluate to a string are HTML encoded.</span></span> <span data-ttu-id="3c0f7-141">C# 식을 계산 하는 `IHtmlContent` 통해 직접 렌더링 되는 *IHtmlContent.WriteTo*합니다.</span><span class="sxs-lookup"><span data-stu-id="3c0f7-141">C# expressions that evaluate to `IHtmlContent` are rendered directly through *IHtmlContent.WriteTo*.</span></span> <span data-ttu-id="3c0f7-142">C# 식으로 계산 하지는 *IHtmlContent* 문자열로 변환 됩니다 (여 *ToString*) 되 고 렌더링 하기 전에 인코딩된 합니다.</span><span class="sxs-lookup"><span data-stu-id="3c0f7-142">C# expressions that don't evaluate to *IHtmlContent* are converted to a string (by *ToString*) and encoded before they are rendered.</span></span> <span data-ttu-id="3c0f7-143">예를 들어, 다음 Razor 태그:</span><span class="sxs-lookup"><span data-stu-id="3c0f7-143">For example, the following Razor markup:</span></span>

```html
@("<span>Hello World</span>")
   ```

<span data-ttu-id="3c0f7-144">이 HTML을 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="3c0f7-144">Renders this HTML:</span></span>

```html
&lt;span&gt;Hello World&lt;/span&gt;
   ```

<span data-ttu-id="3c0f7-145">브라우저에서 렌더링는:</span><span class="sxs-lookup"><span data-stu-id="3c0f7-145">Which the browser renders as:</span></span>

`<span>Hello World</span>`

<span data-ttu-id="3c0f7-146">`HtmlHelper``Raw` 출력은 인코딩된 아니라 HTML 태그로 렌더링 합니다.</span><span class="sxs-lookup"><span data-stu-id="3c0f7-146">`HtmlHelper` `Raw` output is not encoded but rendered as HTML markup.</span></span>

>[!WARNING]
> <span data-ttu-id="3c0f7-147">사용 하 여 `HtmlHelper.Raw` unsanitized 사용자 입력은 보안상 위험 합니다.</span><span class="sxs-lookup"><span data-stu-id="3c0f7-147">Using `HtmlHelper.Raw` on unsanitized user input is a security risk.</span></span> <span data-ttu-id="3c0f7-148">사용자 입력에는 악의적인 JavaScript 또는 다른 악용 기법 포함 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3c0f7-148">User input might contain malicious JavaScript or other exploits.</span></span> <span data-ttu-id="3c0f7-149">사용자 입력 처리에 사용 되는 어려운 문제, 사용 하지 않도록 `HtmlHelper.Raw` 사용자 입력에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3c0f7-149">Sanitizing user input is difficult, avoid using `HtmlHelper.Raw` on user input.</span></span>

<span data-ttu-id="3c0f7-150">다음 Razor 태그:</span><span class="sxs-lookup"><span data-stu-id="3c0f7-150">The following Razor markup:</span></span>

```html
@Html.Raw("<span>Hello World</span>")
   ```

<span data-ttu-id="3c0f7-151">이 HTML을 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="3c0f7-151">Renders this HTML:</span></span>

```html
<span>Hello World</span>
   ```

<a name=razor-code-blocks-label></a>

## <a name="razor-code-blocks"></a><span data-ttu-id="3c0f7-152">Razor 코드 블록</span><span class="sxs-lookup"><span data-stu-id="3c0f7-152">Razor code blocks</span></span>

<span data-ttu-id="3c0f7-153">Razor 코드 블록 시작 `@` 묶여 및 `{}`합니다.</span><span class="sxs-lookup"><span data-stu-id="3c0f7-153">Razor code blocks start with `@` and are enclosed by `{}`.</span></span> <span data-ttu-id="3c0f7-154">식에서와 달리 C# 코드 코드 블록 안에 렌더링 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3c0f7-154">Unlike expressions, C# code inside code blocks is not rendered.</span></span> <span data-ttu-id="3c0f7-155">동일한 범위를 공유 하 고 순서에 정의 된 코드 블록 및 Razor 페이지의 식 (즉, 코드 블록의 선언 수는 나머지 코드 블록 및 식에 대 한 범위에).</span><span class="sxs-lookup"><span data-stu-id="3c0f7-155">Code blocks and expressions in a Razor page share the same scope and are defined in order (that is, declarations in a code block will be in scope for later code blocks and expressions).</span></span>

```none
@{
    var output = "Hello World";
}

<p>The rendered result: @output</p>
```

<span data-ttu-id="3c0f7-156">렌더링 합니다.</span><span class="sxs-lookup"><span data-stu-id="3c0f7-156">Would render:</span></span>

```html
<p>The rendered result: Hello World</p>
   ```

<a name=implicit-transitions-label></a>

### <a name="implicit-transitions"></a><span data-ttu-id="3c0f7-157">암시적 변환</span><span class="sxs-lookup"><span data-stu-id="3c0f7-157">Implicit transitions</span></span>

<span data-ttu-id="3c0f7-158">코드 블록에 기본 언어는 C#, 하지만 HTML로 다시 전환할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3c0f7-158">The default language in a code block is C#, but you can transition back to HTML.</span></span> <span data-ttu-id="3c0f7-159">코드 블록 내에서 HTML HTML 렌더링으로 다시 전환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="3c0f7-159">HTML within a code block will transition back into rendering HTML:</span></span>

```none
@{
    var inCSharp = true;
    <p>Now in HTML, was in C# @inCSharp</p>
}
```

<a name=explicit-delimited-transition-label></a>

### <a name="explicit-delimited-transition"></a><span data-ttu-id="3c0f7-160">명시적 구분 기호로 분리 된 전환</span><span class="sxs-lookup"><span data-stu-id="3c0f7-160">Explicit delimited transition</span></span>

<span data-ttu-id="3c0f7-161">HTML을 렌더링 해야 하는 코드 블록의 하위 섹션을 정의 하려면 Razor와 함께 렌더링 사이의 문자를 둘러싸고 `<text>` 태그:</span><span class="sxs-lookup"><span data-stu-id="3c0f7-161">To define a sub-section of a code block that should render HTML, surround the characters to be rendered with the Razor `<text>` tag:</span></span>

<!-- literal_block {"ids": [], "linenos": false, "xml:space": "preserve", "language": "none", "highlight_args": {"hl_lines": [4]}} -->

```none
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    <text>Name: @person.Name</text>
}
```

<span data-ttu-id="3c0f7-162">일반적으로 HTML 태그 둘러싸인 HTML을 렌더링 하려는 경우이 방법의 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="3c0f7-162">You generally use this approach when you want to render HTML that is not surrounded by an HTML tag.</span></span> <span data-ttu-id="3c0f7-163">HTML 또는 Razor 태그가 없는 Razor 런타임 오류가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3c0f7-163">Without an HTML or Razor tag, you get a Razor runtime error.</span></span>

<a name=explicit-line-transition-with-label></a>

### <a name="explicit-line-transition-with-"></a><span data-ttu-id="3c0f7-164">전환을 명시적 줄`@:`</span><span class="sxs-lookup"><span data-stu-id="3c0f7-164">Explicit Line Transition with `@:`</span></span>

<span data-ttu-id="3c0f7-165">코드 블록 안에 줄의 나머지 부분을 HTML로 렌더링, 사용 된 `@:` 구문:</span><span class="sxs-lookup"><span data-stu-id="3c0f7-165">To render the rest of an entire line as HTML inside a code block, use the `@:` syntax:</span></span>

<!-- literal_block {"ids": [], "linenos": false, "xml:space": "preserve", "language": "none", "highlight_args": {"hl_lines": [4]}} -->

```none
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    @:Name: @person.Name
}
```

<span data-ttu-id="3c0f7-166">없이 `@:` 위 코드에서 런타임 오류 Razor 얻을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3c0f7-166">Without the `@:` in the code above, you'd get a Razor run time error.</span></span>

<a name=control-structures-razor-label></a>

## <a name="control-structures"></a><span data-ttu-id="3c0f7-167">제어 구조</span><span class="sxs-lookup"><span data-stu-id="3c0f7-167">Control Structures</span></span>

<span data-ttu-id="3c0f7-168">제어 구조는 코드 블록의 확장입니다.</span><span class="sxs-lookup"><span data-stu-id="3c0f7-168">Control structures are an extension of code blocks.</span></span> <span data-ttu-id="3c0f7-169">코드 블록 (인라인 C# 태그로 전환)도의 모든 측면은 다음 구조에 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="3c0f7-169">All aspects of code blocks (transitioning to markup, inline C#) also apply to the following structures.</span></span>

### <a name="conditionals-if-else-if-else-and-switch"></a><span data-ttu-id="3c0f7-170">조건부 `@if`, `else if`, `else` 및`@switch`</span><span class="sxs-lookup"><span data-stu-id="3c0f7-170">Conditionals `@if`, `else if`, `else` and `@switch`</span></span>

<span data-ttu-id="3c0f7-171">`@if` 제품군 제어 코드 실행 하는 경우:</span><span class="sxs-lookup"><span data-stu-id="3c0f7-171">The `@if` family controls when code runs:</span></span>

```none
@if (value % 2 == 0)
{
    <p>The value was even</p>
}
```

<span data-ttu-id="3c0f7-172">`else`및 `else if` 필요 하지 않습니다는 `@` 기호:</span><span class="sxs-lookup"><span data-stu-id="3c0f7-172">`else` and `else if` don't require the `@` symbol:</span></span>

```none
@if (value % 2 == 0)
{
    <p>The value was even</p>
}
else if (value >= 1337)
{
    <p>The value is large.</p>
}
else
{
    <p>The value was not large and is odd.</p>
}
```

<span data-ttu-id="3c0f7-173">다음과 같이 switch 문을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3c0f7-173">You can use a switch statement like this:</span></span>

```none
@switch (value)
{
    case 1:
        <p>The value is 1!</p>
        break;
    case 1337:
        <p>Your number is 1337!</p>
        break;
    default:
        <p>Your number was not 1 or 1337.</p>
        break;
}
```

### <a name="looping-for-foreach-while-and-do-while"></a><span data-ttu-id="3c0f7-174">반복 `@for`, `@foreach`, `@while`, 및`@do while`</span><span class="sxs-lookup"><span data-stu-id="3c0f7-174">Looping `@for`, `@foreach`, `@while`, and `@do while`</span></span>

<span data-ttu-id="3c0f7-175">템플릿 기반 HTML 제어 문을 반복을 렌더링할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3c0f7-175">You can render templated HTML with looping control statements.</span></span> <span data-ttu-id="3c0f7-176">예를 들어, 사람 목록이 렌더링 하려면:</span><span class="sxs-lookup"><span data-stu-id="3c0f7-176">For example, to render a list of people:</span></span>

```none
@{
    var people = new Person[]
    {
          new Person("John", 33),
          new Person("Doe", 41),
    };
}
```

<span data-ttu-id="3c0f7-177">다음 반복 문 중 하나를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3c0f7-177">You can use any of the following looping statements:</span></span>

`@for`

```none
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>
}
```

`@foreach`

```none
@foreach (var person in people)
{
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>
}
```

`@while`

```none
@{ var i = 0; }
@while (i < people.Length)
{
    var person = people[i];
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>

    i++;
}
```

`@do while`

```none
@{ var i = 0; }
@do
{
    var person = people[i];
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>

    i++;
} while (i < people.Length);
```

### <a name="compound-using"></a><span data-ttu-id="3c0f7-178">복합`@using`</span><span class="sxs-lookup"><span data-stu-id="3c0f7-178">Compound `@using`</span></span>

<span data-ttu-id="3c0f7-179">C#의 using 문을 사용 하는 개체가 삭제 되도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="3c0f7-179">In C# a using statement is used to ensure an object is disposed.</span></span> <span data-ttu-id="3c0f7-180">Razor의 동일한이 메커니즘 수 만드는 데 사용할 추가 콘텐츠를 포함 하는 HTML 도우미입니다.</span><span class="sxs-lookup"><span data-stu-id="3c0f7-180">In Razor this same mechanism can be used to create HTML helpers that contain additional content.</span></span> <span data-ttu-id="3c0f7-181">예를 들어, HTML 도우미와 폼 태그를 렌더링 하 이용 하면는 `@using` 문:</span><span class="sxs-lookup"><span data-stu-id="3c0f7-181">For instance, we can utilize HTML Helpers to render a form tag with the `@using` statement:</span></span>

```none
@using (Html.BeginForm())
{
    <div>
        email:
        <input type="email" id="Email" name="Email" value="" />
        <button type="submit"> Register </button>
    </div>
}
```

<span data-ttu-id="3c0f7-182">위의와 같은 범위 수준 작업을 수행할 수 있습니다 [태그 도우미](tag-helpers/index.md)합니다.</span><span class="sxs-lookup"><span data-stu-id="3c0f7-182">You can also perform scope level actions like the above with [Tag Helpers](tag-helpers/index.md).</span></span>

### <a name="try-catch-finally"></a><span data-ttu-id="3c0f7-183">`@try`, `catch`, `finally`</span><span class="sxs-lookup"><span data-stu-id="3c0f7-183">`@try`, `catch`, `finally`</span></span>

<span data-ttu-id="3c0f7-184">예외 처리는 C# 유사 합니다.</span><span class="sxs-lookup"><span data-stu-id="3c0f7-184">Exception handling is similar to  C#:</span></span>

[!code-html[Main](razor/sample/Views/Home/Contact7.cshtml)]

### `@lock`

<span data-ttu-id="3c0f7-185">Razor에 임계 섹션 lock 문을 보호 하는 기능이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3c0f7-185">Razor has the capability to protect critical sections with lock statements:</span></span>

```none
@lock (SomeLock)
{
    // Do critical section work
}
```

### <a name="comments"></a><span data-ttu-id="3c0f7-186">설명</span><span class="sxs-lookup"><span data-stu-id="3c0f7-186">Comments</span></span>

<span data-ttu-id="3c0f7-187">Razor 주석 C# 및 HTML을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="3c0f7-187">Razor supports C# and HTML comments.</span></span> <span data-ttu-id="3c0f7-188">다음 태그:</span><span class="sxs-lookup"><span data-stu-id="3c0f7-188">The following markup:</span></span>

```none
@{
    /* C# comment. */
    // Another C# comment.
}
<!-- HTML comment -->
```

<span data-ttu-id="3c0f7-189">서버도 렌더링 됩니다.</span><span class="sxs-lookup"><span data-stu-id="3c0f7-189">Is rendered by the server as:</span></span>

```none
<!-- HTML comment -->
```

<span data-ttu-id="3c0f7-190">Razor 주석 페이지를 렌더링 하기 전에 서버에서 제거 됩니다.</span><span class="sxs-lookup"><span data-stu-id="3c0f7-190">Razor comments are removed by the server before the page is rendered.</span></span> <span data-ttu-id="3c0f7-191">Razor를 사용 하 여 `@*  *@` 를 주석을 구분 합니다.</span><span class="sxs-lookup"><span data-stu-id="3c0f7-191">Razor uses `@*  *@` to delimit comments.</span></span> <span data-ttu-id="3c0f7-192">다음 코드 주석 처리 되어, 있으므로 서버는 모든 태그를 렌더링 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3c0f7-192">The following code is commented out, so the server will not render any markup:</span></span>

```none
 @*
 @{
     /* C# comment. */
     // Another C# comment.
 }
 <!-- HTML comment -->
*@
```

<a name=razor-directives-label></a>

## <a name="directives"></a><span data-ttu-id="3c0f7-193">지시문</span><span class="sxs-lookup"><span data-stu-id="3c0f7-193">Directives</span></span>

<span data-ttu-id="3c0f7-194">Razor 지시문 다음 예약 된 키워드를 사용 하 여 암시적 식으로 표시 됩니다는 `@` 기호입니다.</span><span class="sxs-lookup"><span data-stu-id="3c0f7-194">Razor directives are represented by implicit expressions with reserved keywords following the `@` symbol.</span></span> <span data-ttu-id="3c0f7-195">지시문을 일반적으로 페이지를 구문 분석 되는 방식을 변경 되거나 Razor 페이지 내에서 서로 다른 기능을 사용 하도록 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="3c0f7-195">A directive will typically change the way a page is parsed or enable different functionality within your Razor page.</span></span>

<span data-ttu-id="3c0f7-196">Razor 뷰의 코드를 생성 하는 방법을 이해는 간편한 방법이 지시문의 작동 방식을 이해 하려면 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3c0f7-196">Understanding how Razor generates code for a view will make it easier to understand how directives work.</span></span> <span data-ttu-id="3c0f7-197">Razor 페이지는 C# 파일을 생성 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="3c0f7-197">A Razor page is used to generate a C# file.</span></span> <span data-ttu-id="3c0f7-198">이 Razor 페이지:</span><span class="sxs-lookup"><span data-stu-id="3c0f7-198">For example, this Razor page:</span></span>

[!code-html[Main](razor/sample/Views/Home/Contact8.cshtml)]

<span data-ttu-id="3c0f7-199">다음과 유사한 클래스를 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="3c0f7-199">Generates a class similar to the following:</span></span>

```csharp
public class _Views_Something_cshtml : RazorPage<dynamic>
{
    public override async Task ExecuteAsync()
    {
        var output = "Hello World";

        WriteLiteral("/r/n<div>Output: ");
        Write(output);
        WriteLiteral("</div>");
    }
}
```

<span data-ttu-id="3c0f7-200">[보기에 대해 생성 된 Razor C# 클래스 보기](#razor-customcompilationservice-label) 이렇게 생성 된 클래스를 확인 하는 방법에 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="3c0f7-200">[Viewing the Razor C# class generated for a view](#razor-customcompilationservice-label) explains how to view this generated class.</span></span>

### `@using`

<span data-ttu-id="3c0f7-201">`@using` 지시문을 추가 합니다 c# `using` 생성 된 razor 페이지 지시문:</span><span class="sxs-lookup"><span data-stu-id="3c0f7-201">The `@using` directive will add the c# `using` directive to the generated razor page:</span></span>

[!code-html[Main](razor/sample/Views/Home/Contact9.cshtml)]

### `@model`

<span data-ttu-id="3c0f7-202">`@model` 지시문 Razor 페이지에 전달 하는 모델의 유형을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="3c0f7-202">The `@model` directive specifies the type of the model passed to the Razor page.</span></span> <span data-ttu-id="3c0f7-203">이 도구는 다음 구문을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="3c0f7-203">It uses the following syntax:</span></span>

```none
@model TypeNameOfModel
   ```

<span data-ttu-id="3c0f7-204">예를 들어, 개별 사용자 계정으로 ASP.NET Core MVC 응용 프로그램을 만들면는 *Views/Account/Login.cshtml* Razor 뷰 모델 선언이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3c0f7-204">For example, if you create an ASP.NET Core MVC app with individual user accounts, the *Views/Account/Login.cshtml* Razor view contains the following model declaration:</span></span>

```csharp
@model LoginViewModel
   ```

<span data-ttu-id="3c0f7-205">앞의 클래스 예제에서 생성 된 클래스에서 상속 `RazorPage<dynamic>`합니다.</span><span class="sxs-lookup"><span data-stu-id="3c0f7-205">In the preceding class example, the class generated inherits from `RazorPage<dynamic>`.</span></span> <span data-ttu-id="3c0f7-206">추가 하 여는 `@model` 상속 된 기능을 제어 합니다.</span><span class="sxs-lookup"><span data-stu-id="3c0f7-206">By adding an `@model` you control what’s inherited.</span></span> <span data-ttu-id="3c0f7-207">예</span><span class="sxs-lookup"><span data-stu-id="3c0f7-207">For example</span></span>

```csharp
@model LoginViewModel
   ```

<span data-ttu-id="3c0f7-208">다음 클래스를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="3c0f7-208">Generates the following class</span></span>

```csharp
public class _Views_Account_Login_cshtml : RazorPage<LoginViewModel>
   ```

<span data-ttu-id="3c0f7-209">Razor 페이지에 노출 한 `Model` 모델 액세스에 대 한 속성 페이지에 전달 합니다.</span><span class="sxs-lookup"><span data-stu-id="3c0f7-209">Razor pages expose a `Model` property for accessing the model passed to the page.</span></span>

```html
<div>The Login Email: @Model.Email</div>
   ```

<span data-ttu-id="3c0f7-210">`@model` 지시문이이 속성의 형식을 지정 된 (지정 하 여는 `T` 에 `RazorPage<T>` 페이지에 대 한 생성 된 클래스에서 파생 된).</span><span class="sxs-lookup"><span data-stu-id="3c0f7-210">The `@model` directive specified the type of this property (by specifying the `T` in `RazorPage<T>` that the generated class for your page derives from).</span></span> <span data-ttu-id="3c0f7-211">지정 하지 않으면는 `@model` 지시문은 `Model` 속성 유형 중 하나가 됩니다 `dynamic`합니다.</span><span class="sxs-lookup"><span data-stu-id="3c0f7-211">If you don't specify the `@model` directive the `Model` property will be of type `dynamic`.</span></span> <span data-ttu-id="3c0f7-212">모델의 값은 컨트롤러에서 뷰에 전달 됩니다.</span><span class="sxs-lookup"><span data-stu-id="3c0f7-212">The value of the model is passed from the controller to the view.</span></span> <span data-ttu-id="3c0f7-213">참조 [강력한 형식 모델 및 @model 키워드](../../tutorials/first-mvc-app/adding-model.md#strongly-typed-models-keyword-label) 자세한 정보에 대 한 합니다.</span><span class="sxs-lookup"><span data-stu-id="3c0f7-213">See [Strongly typed models and the @model keyword](../../tutorials/first-mvc-app/adding-model.md#strongly-typed-models-keyword-label) for more information.</span></span>

### `@inherits`

<span data-ttu-id="3c0f7-214">`@inherits` 지시문 Razor 페이지 상속 된 클래스의 모든 권한을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="3c0f7-214">The `@inherits` directive gives you full control of the class your Razor page inherits:</span></span>

```none
@inherits TypeNameOfClassToInheritFrom
   ```

<span data-ttu-id="3c0f7-215">예를 들어, 다음과 같은 사용자 지정 Razor 페이지 유형 몇 가정해 보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="3c0f7-215">For instance, let’s say we had the following custom Razor page type:</span></span>

[!code-csharp[Main](razor/sample/Classes/CustomRazorPage.cs)]

<span data-ttu-id="3c0f7-216">다음 Razor 생성 `<div>Custom text: Hello World</div>`합니다.</span><span class="sxs-lookup"><span data-stu-id="3c0f7-216">The following Razor would generate `<div>Custom text: Hello World</div>`.</span></span>

[!code-html[Main](razor/sample/Views/Home/Contact10.cshtml)]

<span data-ttu-id="3c0f7-217">사용할 수 없는 `@model` 및 `@inherits` 동일한 페이지에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3c0f7-217">You can't use `@model` and `@inherits` on the same page.</span></span> <span data-ttu-id="3c0f7-218">사용할 수 있습니다 `@inherits` 에 *_ViewImports.cshtml* Razor 페이지 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="3c0f7-218">You can have `@inherits` in a *_ViewImports.cshtml* file that the Razor page imports.</span></span> <span data-ttu-id="3c0f7-219">예를 들어, Razor 뷰 가져온 다음 *_ViewImports.cshtml* 파일:</span><span class="sxs-lookup"><span data-stu-id="3c0f7-219">For example, if your Razor view imported the following *_ViewImports.cshtml* file:</span></span>

[!code-html[Main](razor/sample/Views/_ViewImportsModel.cshtml)]

<span data-ttu-id="3c0f7-220">강력한 형식의 Razor 페이지</span><span class="sxs-lookup"><span data-stu-id="3c0f7-220">The following strongly typed Razor page</span></span>

[!code-html[Main](razor/sample/Views/Home/Login1.cshtml)]

<span data-ttu-id="3c0f7-221">이 HTML 태그를 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="3c0f7-221">Generates this HTML markup:</span></span>

```none
<div>The Login Email: Rick@contoso.com</div>
<div>Custom text: Hello World</div>
```

<span data-ttu-id="3c0f7-222">전달 될 때 "[Rick@contoso.com](mailto:Rick@contoso.com)" 모델에서:</span><span class="sxs-lookup"><span data-stu-id="3c0f7-222">When passed "[Rick@contoso.com](mailto:Rick@contoso.com)" in the model:</span></span>

   <span data-ttu-id="3c0f7-223">자세한 내용은 [레이아웃](layout.md)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="3c0f7-223">See [Layout](layout.md) for more information.</span></span>

### `@inject`

<span data-ttu-id="3c0f7-224">`@inject` 지시문을 사용 하면에서 서비스를 삽입할 수 있습니다 프로그램 [서비스 컨테이너](../../fundamentals/dependency-injection.md) 사용 하기 위해 Razor 페이지에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3c0f7-224">The `@inject` directive enables you to inject a service from your [service container](../../fundamentals/dependency-injection.md)  into your Razor page for use.</span></span> <span data-ttu-id="3c0f7-225">참조 [뷰로 종속성 주입](dependency-injection.md)합니다.</span><span class="sxs-lookup"><span data-stu-id="3c0f7-225">See [Dependency injection into views](dependency-injection.md).</span></span>

<a name="functions"></a>

### `@functions`

<span data-ttu-id="3c0f7-226">`@functions` 지시문 Razor 페이지를 함수 수준 콘텐츠를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3c0f7-226">The `@functions` directive enables you to add function level content to your Razor page.</span></span> <span data-ttu-id="3c0f7-227">사용되는 구문은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="3c0f7-227">The syntax is:</span></span>

```none
@functions { // C# Code }
   ```

<span data-ttu-id="3c0f7-228">예:</span><span class="sxs-lookup"><span data-stu-id="3c0f7-228">For example:</span></span>

[!code-html[Main](razor/sample/Views/Home/Contact6.cshtml)]

<span data-ttu-id="3c0f7-229">다음 HTML 태그를 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="3c0f7-229">Generates the following HTML markup:</span></span>

```none
<div>From method: Hello</div>
   ```

<span data-ttu-id="3c0f7-230">생성 된 Razor C#는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="3c0f7-230">The generated Razor C# looks like:</span></span>

[!code-csharp[Main](razor/sample/Classes/Views_Home_Test_cshtml.cs?range=1-19)]

### `@section`

<span data-ttu-id="3c0f7-231">`@section` 지시어와 함께 사용 되는 [레이아웃 페이지](layout.md) 렌더링된 된 HTML 페이지의 서로 다른 부분에 콘텐츠를 렌더링할 뷰를 사용할 수 있도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="3c0f7-231">The `@section` directive is used in conjunction with the [layout page](layout.md) to enable views to render content in different parts of the rendered HTML page.</span></span> <span data-ttu-id="3c0f7-232">참조 [섹션](layout.md#layout-sections-label) 자세한 정보에 대 한 합니다.</span><span class="sxs-lookup"><span data-stu-id="3c0f7-232">See [Sections](layout.md#layout-sections-label) for more information.</span></span>

## <a name="tag-helpers"></a><span data-ttu-id="3c0f7-233">태그 도우미</span><span class="sxs-lookup"><span data-stu-id="3c0f7-233">Tag Helpers</span></span>

<span data-ttu-id="3c0f7-234">다음 [태그 도우미](tag-helpers/index.md) 지시문 제공 된 링크에서 자세히 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="3c0f7-234">The following [Tag Helpers](tag-helpers/index.md) directives are detailed in the links provided.</span></span>

* [@addTagHelper](tag-helpers/intro.md#add-helper-label)
* [@removeTagHelper](tag-helpers/intro.md#remove-razor-directives-label)
* [@tagHelperPrefix](tag-helpers/intro.md#prefix-razor-directives-label)

<a name=razor-reserved-keywords-label></a>

## <a name="razor-reserved-keywords"></a><span data-ttu-id="3c0f7-235">Razor 예약 키워드</span><span class="sxs-lookup"><span data-stu-id="3c0f7-235">Razor reserved keywords</span></span>

### <a name="razor-keywords"></a><span data-ttu-id="3c0f7-236">Razor 키워드</span><span class="sxs-lookup"><span data-stu-id="3c0f7-236">Razor keywords</span></span>

* <span data-ttu-id="3c0f7-237">페이지 (ASP.NET Core 2.0 이상 필요)</span><span class="sxs-lookup"><span data-stu-id="3c0f7-237">page (Requires ASP.NET Core 2.0 and later)</span></span>
* <span data-ttu-id="3c0f7-238">함수</span><span class="sxs-lookup"><span data-stu-id="3c0f7-238">functions</span></span>
* <span data-ttu-id="3c0f7-239">상속</span><span class="sxs-lookup"><span data-stu-id="3c0f7-239">inherits</span></span>
* <span data-ttu-id="3c0f7-240">모델</span><span class="sxs-lookup"><span data-stu-id="3c0f7-240">model</span></span>
* <span data-ttu-id="3c0f7-241">section</span><span class="sxs-lookup"><span data-stu-id="3c0f7-241">section</span></span>
* <span data-ttu-id="3c0f7-242">도우미 (지원 되지 않음 ASP.NET Core 여.)</span><span class="sxs-lookup"><span data-stu-id="3c0f7-242">helper   (Not supported by ASP.NET Core.)</span></span>

<span data-ttu-id="3c0f7-243">Razor 키워드와 이스케이프 될 수 있습니다 `@(Razor Keyword)`, 예를 들어 `@(functions)`합니다.</span><span class="sxs-lookup"><span data-stu-id="3c0f7-243">Razor keywords can be escaped with `@(Razor Keyword)`, for example `@(functions)`.</span></span> <span data-ttu-id="3c0f7-244">아래 전체 샘플을 참조 하십시오.</span><span class="sxs-lookup"><span data-stu-id="3c0f7-244">See the complete sample below.</span></span>

### <a name="c-razor-keywords"></a><span data-ttu-id="3c0f7-245">C# Razor 키워드</span><span class="sxs-lookup"><span data-stu-id="3c0f7-245">C# Razor keywords</span></span>

* <span data-ttu-id="3c0f7-246">case</span><span class="sxs-lookup"><span data-stu-id="3c0f7-246">case</span></span>
* <span data-ttu-id="3c0f7-247">do</span><span class="sxs-lookup"><span data-stu-id="3c0f7-247">do</span></span>
* <span data-ttu-id="3c0f7-248">default</span><span class="sxs-lookup"><span data-stu-id="3c0f7-248">default</span></span>
* <span data-ttu-id="3c0f7-249">for</span><span class="sxs-lookup"><span data-stu-id="3c0f7-249">for</span></span>
* <span data-ttu-id="3c0f7-250">foreach</span><span class="sxs-lookup"><span data-stu-id="3c0f7-250">foreach</span></span>
* <span data-ttu-id="3c0f7-251">if</span><span class="sxs-lookup"><span data-stu-id="3c0f7-251">if</span></span>
* <span data-ttu-id="3c0f7-252">else</span><span class="sxs-lookup"><span data-stu-id="3c0f7-252">else</span></span>
* <span data-ttu-id="3c0f7-253">잠금</span><span class="sxs-lookup"><span data-stu-id="3c0f7-253">lock</span></span>
* <span data-ttu-id="3c0f7-254">switch</span><span class="sxs-lookup"><span data-stu-id="3c0f7-254">switch</span></span>
* <span data-ttu-id="3c0f7-255">try</span><span class="sxs-lookup"><span data-stu-id="3c0f7-255">try</span></span>
* <span data-ttu-id="3c0f7-256">catch</span><span class="sxs-lookup"><span data-stu-id="3c0f7-256">catch</span></span>
* <span data-ttu-id="3c0f7-257">finally</span><span class="sxs-lookup"><span data-stu-id="3c0f7-257">finally</span></span>
* <span data-ttu-id="3c0f7-258">using</span><span class="sxs-lookup"><span data-stu-id="3c0f7-258">using</span></span>
* <span data-ttu-id="3c0f7-259">while</span><span class="sxs-lookup"><span data-stu-id="3c0f7-259">while</span></span>

<span data-ttu-id="3c0f7-260">C# Razor 키워드 해야 이중 이스케이프 `@(@C# Razor Keyword)`, 예를 들어 `@(@case)`합니다.</span><span class="sxs-lookup"><span data-stu-id="3c0f7-260">C# Razor keywords need to be double escaped with `@(@C# Razor Keyword)`, for example `@(@case)`.</span></span> <span data-ttu-id="3c0f7-261">첫 번째 `@` Razor 파서를 이스케이프 합니다. 두 번째 `@` C# 파서를 이스케이프 합니다.</span><span class="sxs-lookup"><span data-stu-id="3c0f7-261">The first `@` escapes the Razor parser, the second `@` escapes the C# parser.</span></span> <span data-ttu-id="3c0f7-262">아래 전체 샘플을 참조 하십시오.</span><span class="sxs-lookup"><span data-stu-id="3c0f7-262">See the complete sample below.</span></span>

### <a name="reserved-keywords-not-used-by-razor"></a><span data-ttu-id="3c0f7-263">Razor에서 사용 하지 않는 예약 된 키워드</span><span class="sxs-lookup"><span data-stu-id="3c0f7-263">Reserved keywords not used by Razor</span></span>

* <span data-ttu-id="3c0f7-264">네임스페이스(namespace)</span><span class="sxs-lookup"><span data-stu-id="3c0f7-264">namespace</span></span>
* <span data-ttu-id="3c0f7-265">클래스</span><span class="sxs-lookup"><span data-stu-id="3c0f7-265">class</span></span>

<a name=razor-customcompilationservice-label></a>

## <a name="viewing-the-razor-c-class-generated-for-a-view"></a><span data-ttu-id="3c0f7-266">보기에 대해 생성 된 Razor C# 클래스 보기</span><span class="sxs-lookup"><span data-stu-id="3c0f7-266">Viewing the Razor C# class generated for a view</span></span>

<span data-ttu-id="3c0f7-267">ASP.NET Core MVC 프로젝트에 다음 클래스를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="3c0f7-267">Add the following class to your ASP.NET Core MVC project:</span></span>

[!code-csharp[Main](razor/sample/Services/CustomCompilationService.cs)]

<span data-ttu-id="3c0f7-268">재정의 `ICompilationService` 위의 클래스;와 MVC에서 추가</span><span class="sxs-lookup"><span data-stu-id="3c0f7-268">Override the `ICompilationService` added by MVC with the above class;</span></span>

[!code-csharp[Main](razor/sample/Startup.cs?highlight=4&range=29-33)]

<span data-ttu-id="3c0f7-269">중단점을 설정는 `Compile` 방식의 `CustomCompilationService` 뷰와 `compilationContent`합니다.</span><span class="sxs-lookup"><span data-stu-id="3c0f7-269">Set a break point on the `Compile` method of `CustomCompilationService` and view `compilationContent`.</span></span>

![CompilationContent의 텍스트 시각화 도우미 보기](razor/_static/tvr.png)

<a name="case"></a>
## <a name="view-lookups-and-case-sensitivity"></a><span data-ttu-id="3c0f7-271">보기 조회 및 대/소문자 구분</span><span class="sxs-lookup"><span data-stu-id="3c0f7-271">View lookups and case sensitivity</span></span>

<span data-ttu-id="3c0f7-272">Razor 뷰 엔진 뷰에 대 한 대/소문자 구분 조회를 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="3c0f7-272">The Razor view engine performs case-sensitive lookups for views.</span></span> <span data-ttu-id="3c0f7-273">그러나 실제 조회 기본 원본에 의해 결정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="3c0f7-273">However, the actual lookup is determined by the underlying source:</span></span>

* <span data-ttu-id="3c0f7-274">소스를 기반으로 하는 파일:</span><span class="sxs-lookup"><span data-stu-id="3c0f7-274">File based source:</span></span> 

    * <span data-ttu-id="3c0f7-275">대/소문자 구분 파일 시스템 (예: Windows), 운영 체제에서 실제 파일 공급자 조회는 대/소문자 구분 합니다.</span><span class="sxs-lookup"><span data-stu-id="3c0f7-275">On operating systems with case insensitive file systems (like Windows), physical file provider lookups are case insensitive.</span></span> <span data-ttu-id="3c0f7-276">예를 들어 `return View("Test")` 초래 `/Views/Home/Test.cshtml`, `/Views/home/test.cshtml` 다른 대/소문자 변형이 모두 발견 되 고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3c0f7-276">For example `return View("Test")` would result in `/Views/Home/Test.cshtml`, `/Views/home/test.cshtml` and all other casing variants would be discovered.</span></span>
    * <span data-ttu-id="3c0f7-277">대/소문자 구분 파일 시스템에 Linux OSX을 포함 하 고 `EmbeddedFileProvider`, 조회는 대/소문자 구분 합니다.</span><span class="sxs-lookup"><span data-stu-id="3c0f7-277">On case sensitive file systems, which includes Linux, OSX and `EmbeddedFileProvider`, lookups are case sensitive.</span></span> <span data-ttu-id="3c0f7-278">예를 들어 `return View("Test")` 구체적으로 찾습니다 `/Views/Home/Test.cshtml`합니다.</span><span class="sxs-lookup"><span data-stu-id="3c0f7-278">For example, `return View("Test")` would specifically look for `/Views/Home/Test.cshtml`.</span></span>
        
* <span data-ttu-id="3c0f7-279">미리 컴파일된 뷰:</span><span class="sxs-lookup"><span data-stu-id="3c0f7-279">Precompiled views:</span></span>

   * <span data-ttu-id="3c0f7-280">ASP.Net Core 2.0 이상에서는 대/소문자 구분 모든 운영 체제에는 미리 컴파일된 뷰를 찾는 합니다.</span><span class="sxs-lookup"><span data-stu-id="3c0f7-280">With ASP.Net Core 2.0 and later, looking up precompiled views is case insensitive on all operating systems.</span></span> <span data-ttu-id="3c0f7-281">동작은 Windows에서 물리적 파일 공급자의 동작과 동일 합니다.</span><span class="sxs-lookup"><span data-stu-id="3c0f7-281">The behavior is identical to physical file provider's behavior on Windows.</span></span> 
   <span data-ttu-id="3c0f7-282">참고: 미리 컴파일된 뷰를 두 가지 경우에만 다른 경우 조회 명확 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3c0f7-282">Note: If two precompiled views differ only in case, the result of lookup is non-deterministic.</span></span>

<span data-ttu-id="3c0f7-283">개발자는 영역, 컨트롤러 및 작업 이름의 대/소문자를 파일 및 디렉터리 이름의 대/소문자와 일치 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="3c0f7-283">Developers are encouraged to match the casing of file and directory names to the casing of area, controller and action names.</span></span> <span data-ttu-id="3c0f7-284">이렇게 하면 배포에 기본 파일 시스템의 알 수 없는 상태로 유지 합니다.</span><span class="sxs-lookup"><span data-stu-id="3c0f7-284">This would ensure your deployments remain agnostic of the underlying file system.</span></span>
