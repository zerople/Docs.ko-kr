---
title: "ASP.NET Core에 대 한 razor 구문 참조"
author: guardrex
description: "웹 페이지에 서버 기반 코드를 포함 하는 것에 대 한 Razor 태그 구문에 알아봅니다."
keywords: "ASP.NET Core, Razor, Razor 지시문"
ms.author: riande
manager: wpickett
ms.date: 09/29/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/views/razor
ms.openlocfilehash: 532e278597a0029b5bae93068af5b7b147c35688
ms.sourcegitcommit: e45f8912ce32b4071bf7e83b8f8315cd8bba3520
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/04/2017
---
# <a name="razor-syntax-for-aspnet-core"></a><span data-ttu-id="d7a03-104">ASP.NET Core에 대 한 razor 구문</span><span class="sxs-lookup"><span data-stu-id="d7a03-104">Razor syntax for ASP.NET Core</span></span>

<span data-ttu-id="d7a03-105">여 [Rick Anderson](https://twitter.com/RickAndMSFT), [Luke Latham](https://github.com/guardrex), 및 [Taylor Mullen](https://twitter.com/ntaylormullen)</span><span class="sxs-lookup"><span data-stu-id="d7a03-105">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Luke Latham](https://github.com/guardrex), and [Taylor Mullen](https://twitter.com/ntaylormullen)</span></span>

<span data-ttu-id="d7a03-106">Razor은 웹 페이지에 서버 기반 코드를 포함 하는 것에 대 한 태그 구문입니다.</span><span class="sxs-lookup"><span data-stu-id="d7a03-106">Razor is a markup syntax for embedding server-based code into webpages.</span></span> <span data-ttu-id="d7a03-107">Razor 구문 Razor 태그, C#, 및 HTML로 이루어져 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d7a03-107">The Razor syntax consists of Razor markup, C#, and HTML.</span></span> <span data-ttu-id="d7a03-108">Razor를 일반적으로 들어 있는 파일을 *.cshtml* 파일 확장명입니다.</span><span class="sxs-lookup"><span data-stu-id="d7a03-108">Files containing Razor generally have a *.cshtml* file extension.</span></span>

## <a name="rendering-html"></a><span data-ttu-id="d7a03-109">HTML 렌더링</span><span class="sxs-lookup"><span data-stu-id="d7a03-109">Rendering HTML</span></span>

<span data-ttu-id="d7a03-110">기본 Razor 언어 HTML입니다.</span><span class="sxs-lookup"><span data-stu-id="d7a03-110">The default Razor language is HTML.</span></span> <span data-ttu-id="d7a03-111">렌더링 HTML Razor 태그에서이 HTML 파일에서 HTML을 렌더링 하는 방법과 다르지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d7a03-111">Rendering HTML from Razor markup is no different than rendering HTML from an HTML file.</span></span> <span data-ttu-id="d7a03-112">HTML 태그에 배치 하는 경우는 *.cshtml* Razor 파일을 변경 하지 않고 서버에서 렌더링 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d7a03-112">If you place HTML markup into a *.cshtml* Razor file, it's rendered by the server unchanged.</span></span>

## <a name="razor-syntax"></a><span data-ttu-id="d7a03-113">Razor 구문</span><span class="sxs-lookup"><span data-stu-id="d7a03-113">Razor syntax</span></span>

<span data-ttu-id="d7a03-114">Razor C# 지원 및 사용 하 여는 `@` C#으로 HTML에서 전환 기호입니다.</span><span class="sxs-lookup"><span data-stu-id="d7a03-114">Razor supports C# and uses the `@` symbol to transition from HTML to C#.</span></span> <span data-ttu-id="d7a03-115">Razor C# 식을 계산 하 고 HTML 출력에이 렌더링 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7a03-115">Razor evaluates C# expressions and renders them in the HTML output.</span></span>

<span data-ttu-id="d7a03-116">경우는 `@` 기호 뒤는 [Razor 예약 키워드](#razor-reserved-keywords), Razor 특정 태그에 전환 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7a03-116">When an `@` symbol is followed by a [Razor reserved keyword](#razor-reserved-keywords), it transitions into Razor-specific markup.</span></span> <span data-ttu-id="d7a03-117">그렇지 않은 경우 일반 C# 식으로 전환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d7a03-117">Otherwise, it transitions into plain C#.</span></span>

<span data-ttu-id="d7a03-118">이스케이프할는 `@` Razor 태그에서 기호, 초를 사용 하 여 `@` 기호:</span><span class="sxs-lookup"><span data-stu-id="d7a03-118">To escape an `@` symbol in Razor markup, use a second `@` symbol:</span></span>

```cshtml
<p>@@Username</p>
```

<span data-ttu-id="d7a03-119">코드는 단일 HTML로 렌더링은 `@` 기호:</span><span class="sxs-lookup"><span data-stu-id="d7a03-119">The code is rendered in HTML with a single `@` symbol:</span></span>

```html
<p>@Username</p>
```

<span data-ttu-id="d7a03-120">HTML 특성 및 전자 메일 주소를 포함 하는 내용을 처리 하지 않습니다는 `@` 전환 문자로 기호입니다.</span><span class="sxs-lookup"><span data-stu-id="d7a03-120">HTML attributes and content containing email addresses don't treat the `@` symbol as a transition character.</span></span> <span data-ttu-id="d7a03-121">다음 예제에서 전자 메일 주소 Razor 구문 분석 하 여 그대로 유지 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d7a03-121">The email addresses in the following example are untouched by Razor parsing:</span></span>

```cshtml
<a href="mailto:Support@contoso.com">Support@contoso.com</a>
```

## <a name="implicit-razor-expressions"></a><span data-ttu-id="d7a03-122">암시적 Razor 식</span><span class="sxs-lookup"><span data-stu-id="d7a03-122">Implicit Razor expressions</span></span>

<span data-ttu-id="d7a03-123">암시적 Razor 식은로 시작 `@` C# 코드가 뒤에 오는:</span><span class="sxs-lookup"><span data-stu-id="d7a03-123">Implicit Razor expressions start with `@` followed by C# code:</span></span>

```cshtml
<p>@DateTime.Now</p>
<p>@DateTime.IsLeapYear(2016)</p>
```

<span data-ttu-id="d7a03-124">C# 제외 하 고 `await` 키워드, 암시적 식 공백을 포함 해서는 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d7a03-124">With the exception of the C# `await` keyword, implicit expressions must not contain spaces.</span></span> <span data-ttu-id="d7a03-125">C# 문에 명확한 종료 하는 경우 공간 intermingle 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d7a03-125">You can intermingle spaces if the C# statement has a clear ending:</span></span>

```cshtml
<p>@await DoSomething("hello", "world")</p>
```

## <a name="explicit-razor-expressions"></a><span data-ttu-id="d7a03-126">명시적 Razor 식</span><span class="sxs-lookup"><span data-stu-id="d7a03-126">Explicit Razor expressions</span></span>

<span data-ttu-id="d7a03-127">Razor 식은 명시적으로 구성 될는 `@` 균형 잡힌 괄호 기호입니다.</span><span class="sxs-lookup"><span data-stu-id="d7a03-127">Explicit Razor expressions consist of an `@` symbol with balanced parenthesis.</span></span> <span data-ttu-id="d7a03-128">지난 주 시간을 렌더링 하려면 다음 Razor 태그 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d7a03-128">To render last week's time, the following Razor markup is used:</span></span>

```cshtml
<p>Last week this time: @(DateTime.Now - TimeSpan.FromDays(7))</p>
```

<span data-ttu-id="d7a03-129">내에서 모든 콘텐츠는 `@()` 괄호 평가 되 고 출력에 렌더링 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7a03-129">Any content within the `@()` parenthesis is evaluated and rendered to the output.</span></span>

<span data-ttu-id="d7a03-130">일반적으로 이전 섹션에 설명 된 암시적 식, 공백을 포함할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="d7a03-130">Implicit expressions, described in the previous section, generally can't contain spaces.</span></span> <span data-ttu-id="d7a03-131">다음 코드에서 1 주일 현재 시간에서 차감 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d7a03-131">In the following code, one week isn't subtracted from the current time:</span></span>

[!code-cshtml[Main](razor/sample/Views/Home/Contact.cshtml?range=17)]

<span data-ttu-id="d7a03-132">코드는 다음과 같은 HTML을 렌더링 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d7a03-132">The code renders the following HTML:</span></span>

```html
<p>Last week: 7/7/2016 4:39:52 PM - TimeSpan.FromDays(7)</p>
```

<span data-ttu-id="d7a03-133">연결 된 식 결과 텍스트에 명시적 식을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d7a03-133">You can use an explicit expression to concatenate text with an expression result:</span></span>

```cshtml
@{
    var joe = new Person("Joe", 33);
}

<p>Age@(joe.Age)</p>
```

<span data-ttu-id="d7a03-134">명시적 식 없이 `<p>Age@joe.Age</p>` 전자 메일 주소로 처리 및 `<p>Age@joe.Age</p>` 렌더링 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d7a03-134">Without the explicit expression, `<p>Age@joe.Age</p>` is treated as an email address, and `<p>Age@joe.Age</p>` is rendered.</span></span> <span data-ttu-id="d7a03-135">명시적 식을으로 쓸 때 `<p>Age33</p>` 렌더링 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d7a03-135">When written as an explicit expression, `<p>Age33</p>` is rendered.</span></span>

## <a name="expression-encoding"></a><span data-ttu-id="d7a03-136">식 인코딩</span><span class="sxs-lookup"><span data-stu-id="d7a03-136">Expression encoding</span></span>

<span data-ttu-id="d7a03-137">C# 식을 문자열로 평가 하는 인코딩된 HTML입니다.</span><span class="sxs-lookup"><span data-stu-id="d7a03-137">C# expressions that evaluate to a string are HTML encoded.</span></span> <span data-ttu-id="d7a03-138">C# 식을 계산 하는 `IHtmlContent` 통해 직접 렌더링 되는 `IHtmlContent.WriteTo`합니다.</span><span class="sxs-lookup"><span data-stu-id="d7a03-138">C# expressions that evaluate to `IHtmlContent` are rendered directly through `IHtmlContent.WriteTo`.</span></span> <span data-ttu-id="d7a03-139">C# 식으로 계산 하지는 `IHtmlContent` 하 여 문자열로 변환 `ToString` 되 고 렌더링 하는 전에 인코딩된 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7a03-139">C# expressions that don't evaluate to `IHtmlContent` are converted to a string by `ToString` and encoded before they're rendered.</span></span>

```cshtml
@("<span>Hello World</span>")
```

<span data-ttu-id="d7a03-140">코드는 다음과 같은 HTML을 렌더링 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d7a03-140">The code renders the following HTML:</span></span>

```html
&lt;span&gt;Hello World&lt;/span&gt;
```

<span data-ttu-id="d7a03-141">HTML은 브라우저에 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d7a03-141">The HTML is shown in the browser as:</span></span>

```
<span>Hello World</span>
```

<span data-ttu-id="d7a03-142">`HtmlHelper.Raw`출력 인코딩된 않지만 HTML 태그로 렌더링 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7a03-142">`HtmlHelper.Raw` output isn't encoded but rendered as HTML markup.</span></span>

> [!WARNING]
> <span data-ttu-id="d7a03-143">사용 하 여 `HtmlHelper.Raw` unsanitized 사용자 입력은 보안상 위험 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7a03-143">Using `HtmlHelper.Raw` on unsanitized user input is a security risk.</span></span> <span data-ttu-id="d7a03-144">사용자 입력에는 악의적인 JavaScript 또는 다른 악용 기법 포함 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d7a03-144">User input might contain malicious JavaScript or other exploits.</span></span> <span data-ttu-id="d7a03-145">사용자 입력을 정리 하는 것은 어렵습니다.</span><span class="sxs-lookup"><span data-stu-id="d7a03-145">Sanitizing user input is difficult.</span></span> <span data-ttu-id="d7a03-146">사용 하지 않도록 `HtmlHelper.Raw` 사용자 입력을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7a03-146">Avoid using `HtmlHelper.Raw` with user input.</span></span>

```cshtml
@Html.Raw("<span>Hello World</span>")
```

<span data-ttu-id="d7a03-147">코드는 다음과 같은 HTML을 렌더링 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d7a03-147">The code renders the following HTML:</span></span>

```html
<span>Hello World</span>
```

## <a name="razor-code-blocks"></a><span data-ttu-id="d7a03-148">Razor 코드 블록</span><span class="sxs-lookup"><span data-stu-id="d7a03-148">Razor code blocks</span></span>

<span data-ttu-id="d7a03-149">Razor 코드 블록 시작 `@` 묶여 및 `{}`합니다.</span><span class="sxs-lookup"><span data-stu-id="d7a03-149">Razor code blocks start with `@` and are enclosed by `{}`.</span></span> <span data-ttu-id="d7a03-150">식에서와 달리 C# 코드 블록 내부에서 코드 렌더링 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d7a03-150">Unlike expressions, C# code inside code blocks isn't rendered.</span></span> <span data-ttu-id="d7a03-151">코드 블록 및 뷰의 식에서에서 같은 범위를 공유 및 순서에 정의 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d7a03-151">Code blocks and expressions in a view share the same scope and are defined in order:</span></span>

```cshtml
@{
    var quote = "The future depends on what you do today. - Mahatma Gandhi";
}

<p>@quote</p>

@{
    quote = "Hate cannot drive out hate, only love can do that. - Martin Luther King, Jr.";
}

<p>@quote</p>
```

<span data-ttu-id="d7a03-152">코드는 다음과 같은 HTML을 렌더링 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d7a03-152">The code renders the following HTML:</span></span>

```html
<p>The future depends on what you do today. - Mahatma Gandhi</p>
<p>Hate cannot drive out hate, only love can do that. - Martin Luther King, Jr.</p>
```

### <a name="implicit-transitions"></a><span data-ttu-id="d7a03-153">암시적 변환</span><span class="sxs-lookup"><span data-stu-id="d7a03-153">Implicit transitions</span></span>

<span data-ttu-id="d7a03-154">코드 블록에 기본 언어는 C#, 하지만 HTML로 다시 전환할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d7a03-154">The default language in a code block is C#, but you can transition back to HTML:</span></span>

```cshtml
@{
    var inCSharp = true;
    <p>Now in HTML, was in C# @inCSharp</p>
}
```

### <a name="explicit-delimited-transition"></a><span data-ttu-id="d7a03-155">명시적 구분 기호로 분리 된 전환</span><span class="sxs-lookup"><span data-stu-id="d7a03-155">Explicit delimited transition</span></span>

<span data-ttu-id="d7a03-156">HTML 렌더링 해야 하는 코드 블록의 하위 섹션을 정의 하려면 코드 감싸기 렌더링 하기 위한 문자 Razor  **\<텍스트 >** 태그:</span><span class="sxs-lookup"><span data-stu-id="d7a03-156">To define a sub-section of a code block that should render HTML, surround the characters for rendering with the Razor **\<text>** tag:</span></span>

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    <text>Name: @person.Name</text>
}
```

<span data-ttu-id="d7a03-157">HTML 태그로 묶인 없는 HTML을 렌더링 하려면이 방법을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7a03-157">Use this approach when you want to render HTML that isn't surrounded by an HTML tag.</span></span> <span data-ttu-id="d7a03-158">HTML 또는 Razor 태그가 없는 Razor 런타임 오류가 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7a03-158">Without an HTML or Razor tag, you receive a Razor runtime error.</span></span>

<span data-ttu-id="d7a03-159"> **\<텍스트 >** 태그에서 콘텐츠를 렌더링할 때 공백을 제어도 유용 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7a03-159">The **\<text>** tag is also useful to control whitespace when rendering content.</span></span> <span data-ttu-id="d7a03-160">사이 있는 내용을는  **\<텍스트 >** 태그를 렌더링 하기 전이나 후에 공백이 없어야 하 고는  **\<텍스트 >** 태그의 HTML 출력에 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="d7a03-160">Only the content between the **\<text>** tags is rendered, and no whitespace before or after the **\<text>** tags appears in the HTML output.</span></span>

### <a name="explicit-line-transition-with-"></a><span data-ttu-id="d7a03-161">명시적 줄 전환을 @:</span><span class="sxs-lookup"><span data-stu-id="d7a03-161">Explicit Line Transition with @:</span></span>

<span data-ttu-id="d7a03-162">코드 블록 안에 줄의 나머지 부분을 HTML로 렌더링, 사용 된 `@:` 구문:</span><span class="sxs-lookup"><span data-stu-id="d7a03-162">To render the rest of an entire line as HTML inside a code block, use the `@:` syntax:</span></span>

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    @:Name: @person.Name
}
```

<span data-ttu-id="d7a03-163">없이 `@:` Razor 런타임 오류 코드에서 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7a03-163">Without the `@:` in the code, you recieve a Razor runtime error.</span></span>

## <a name="control-structures"></a><span data-ttu-id="d7a03-164">제어 구조</span><span class="sxs-lookup"><span data-stu-id="d7a03-164">Control Structures</span></span>

<span data-ttu-id="d7a03-165">제어 구조는 코드 블록의 확장입니다.</span><span class="sxs-lookup"><span data-stu-id="d7a03-165">Control structures are an extension of code blocks.</span></span> <span data-ttu-id="d7a03-166">코드 블록 (인라인 C# 태그로 전환)도의 모든 측면은 다음 구조에 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d7a03-166">All aspects of code blocks (transitioning to markup, inline C#) also apply to the following structures.</span></span>

### <a name="conditionals-if-else-if-else-and-switch"></a><span data-ttu-id="d7a03-167">조건부 @if, else if else, 및@switch</span><span class="sxs-lookup"><span data-stu-id="d7a03-167">Conditionals @if, else if, else, and @switch</span></span>

<span data-ttu-id="d7a03-168">`@if`코드를 실행할 때 컨트롤:</span><span class="sxs-lookup"><span data-stu-id="d7a03-168">`@if` controls when code runs:</span></span>

```cshtml
@if (value % 2 == 0)
{
    <p>The value was even.</p>
}
```

<span data-ttu-id="d7a03-169">`else`및 `else if` 필요 하지 않습니다는 `@` 기호:</span><span class="sxs-lookup"><span data-stu-id="d7a03-169">`else` and `else if` don't require the `@` symbol:</span></span>

```cshtml
@if (value % 2 == 0)
{
    <p>The value was even.</p>
}
else if (value >= 1337)
{
    <p>The value is large.</p>
}
else
{
    <p>The value is odd and small.</p>
}
```

<span data-ttu-id="d7a03-170">다음과 같이 switch 문을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d7a03-170">You can use a switch statement like this:</span></span>

```cshtml
@switch (value)
{
    case 1:
        <p>The value is 1!</p>
        break;
    case 1337:
        <p>Your number is 1337!</p>
        break;
    default:
        <p>Your number wasn't 1 or 1337.</p>
        break;
}
```

### <a name="looping-for-foreach-while-and-do-while"></a><span data-ttu-id="d7a03-171">반복 @for, @foreach, @while, 및 @do 동안</span><span class="sxs-lookup"><span data-stu-id="d7a03-171">Looping @for, @foreach, @while, and @do while</span></span>

<span data-ttu-id="d7a03-172">템플릿 기반 HTML 제어 문을 반복을 렌더링할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d7a03-172">You can render templated HTML with looping control statements.</span></span> <span data-ttu-id="d7a03-173">사람 목록이 렌더링 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7a03-173">To render a list of people:</span></span>

```cshtml
@{
    var people = new Person[]
    {
          new Person("Weston", 33),
          new Person("Johnathon", 41),
          ...
    };
}
```

<span data-ttu-id="d7a03-174">다음 반복 문 중 하나를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d7a03-174">You can use any of the following looping statements:</span></span>

`@for`

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>
}
```

`@foreach`

```cshtml
@foreach (var person in people)
{
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>
}
```

`@while`

```cshtml
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

```cshtml
@{ var i = 0; }
@do
{
    var person = people[i];
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>

    i++;
} while (i < people.Length);
```

### <a name="compound-using"></a><span data-ttu-id="d7a03-175">복합@using</span><span class="sxs-lookup"><span data-stu-id="d7a03-175">Compound @using</span></span>

<span data-ttu-id="d7a03-176">C#에서는 `using` 문을 사용 하는 개체가 삭제 되도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7a03-176">In C#, a `using` statement is used to ensure an object is disposed.</span></span> <span data-ttu-id="d7a03-177">Razor, 동일한 메커니즘 추가 콘텐츠를 포함 하는 HTML 도우미 만들기에 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d7a03-177">In Razor, the same mechanism is used to create HTML Helpers that contain additional content.</span></span> <span data-ttu-id="d7a03-178">예를 들어, form 태그를 렌더링 하는 HTML 도우미를 사용할 수 있습니다는 `@using` 문:</span><span class="sxs-lookup"><span data-stu-id="d7a03-178">For instance, you can utilize HTML Helpers to render a form tag with the `@using` statement:</span></span>

```cshtml
@using (Html.BeginForm())
{
    <div>
        email:
        <input type="email" id="Email" value="">
        <button>Register</button>
    </div>
}
```

<span data-ttu-id="d7a03-179">범위 수준 작업을 수행할 수도 있습니다 [태그 도우미](xref:mvc/views/tag-helpers/intro)합니다.</span><span class="sxs-lookup"><span data-stu-id="d7a03-179">You can also perform scope-level actions with [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span>

### <a name="try-catch-finally"></a><span data-ttu-id="d7a03-180">@trycatch, finally</span><span class="sxs-lookup"><span data-stu-id="d7a03-180">@try, catch, finally</span></span>

<span data-ttu-id="d7a03-181">예외 처리는 C# 유사 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7a03-181">Exception handling is similar to C#:</span></span>

[!code-cshtml[Main](razor/sample/Views/Home/Contact7.cshtml)]

### <a name="lock"></a>@lock

<span data-ttu-id="d7a03-182">Razor에 임계 섹션 lock 문을 보호 하는 기능이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d7a03-182">Razor has the capability to protect critical sections with lock statements:</span></span>

```cshtml
@lock (SomeLock)
{
    // Do critical section work
}
```

### <a name="comments"></a><span data-ttu-id="d7a03-183">설명</span><span class="sxs-lookup"><span data-stu-id="d7a03-183">Comments</span></span>

<span data-ttu-id="d7a03-184">Razor 주석 C# 및 HTML을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="d7a03-184">Razor supports C# and HTML comments:</span></span>

```cshtml
@{
    /* C# comment */
    // Another C# comment
}
<!-- HTML comment -->
```

<span data-ttu-id="d7a03-185">코드는 다음과 같은 HTML을 렌더링 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d7a03-185">The code renders the following HTML:</span></span>

```html
<!-- HTML comment -->
```

<span data-ttu-id="d7a03-186">Razor 주석 웹 페이지를 렌더링 하기 전에 서버에서 제거 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d7a03-186">Razor comments are removed by the server before the webpage is rendered.</span></span> <span data-ttu-id="d7a03-187">Razor를 사용 하 여 `@*  *@` 를 주석을 구분 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7a03-187">Razor uses `@*  *@` to delimit comments.</span></span> <span data-ttu-id="d7a03-188">다음 코드 주석 처리 되어, 서버에서 태그를 렌더링 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d7a03-188">The following code is commented out, so the server doesn't render any markup:</span></span>

```cshtml
@*
    @{
        /* C# comment */
        // Another C# comment
    }
    <!-- HTML comment -->
*@
```

## <a name="directives"></a><span data-ttu-id="d7a03-189">지시문</span><span class="sxs-lookup"><span data-stu-id="d7a03-189">Directives</span></span>

<span data-ttu-id="d7a03-190">Razor 지시문 다음 예약 된 키워드를 사용 하 여 암시적 식으로 표시 됩니다는 `@` 기호입니다.</span><span class="sxs-lookup"><span data-stu-id="d7a03-190">Razor directives are represented by implicit expressions with reserved keywords following the `@` symbol.</span></span> <span data-ttu-id="d7a03-191">지시문에는 일반적으로 보기를 구문 분석 또는 다른 기능을 활성화 하는 방법을 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7a03-191">A directive typically changes the way a view is parsed or enables different functionality.</span></span>

<span data-ttu-id="d7a03-192">Razor 뷰의 코드를 생성 하는 방법을 이해 쉽게 지시문의 작동 방식을 이해 하려면 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d7a03-192">Understanding how Razor generates code for a view makes it easier to understand how directives work.</span></span>

[!code-html[Main](razor/sample/Views/Home/Contact8.cshtml)]

<span data-ttu-id="d7a03-193">코드에서는 클래스에는 다음과 유사한 오류가 생성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d7a03-193">The code generates a class similar to the following:</span></span>

```csharp
public class _Views_Something_cshtml : RazorPage<dynamic>
{
    public override async Task ExecuteAsync()
    {
        var output = "Getting old ain't for wimps! - Anonymous";

        WriteLiteral("/r/n<div>Quote of the Day: ");
        Write(output);
        WriteLiteral("</div>");
    }
}
```

<span data-ttu-id="d7a03-194">이 문서의 섹션의 뒷부분에 나오는 [보기에 대해 생성 된 Razor C# 클래스 보기](#viewing-the-razor-c-class-generated-for-a-view) 이렇게 생성 된 클래스를 확인 하는 방법에 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7a03-194">Later in this article, the section [Viewing the Razor C# class generated for a view](#viewing-the-razor-c-class-generated-for-a-view) explains how to view this generated class.</span></span>

### <a name="using"></a>@using

<span data-ttu-id="d7a03-195">`@using` 지시문 추가 하는 C# `using` 지시문을 생성 된 보기에:</span><span class="sxs-lookup"><span data-stu-id="d7a03-195">The `@using` directive adds the C# `using` directive to the generated view:</span></span>

[!code-cshtml[Main](razor/sample/Views/Home/Contact9.cshtml)]

### <a name="model"></a>@model

<span data-ttu-id="d7a03-196">`@model` 지시문 보기에 전달 된 모델의 유형을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7a03-196">The `@model` directive specifies the type of the model passed to a view:</span></span>

```cshtml
@model TypeNameOfModel
```

<span data-ttu-id="d7a03-197">개별 사용자 계정으로 ASP.NET Core MVC 응용 프로그램을 만들면는 *Views/Account/Login.cshtml* 보기 모델 선언이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d7a03-197">If you create an ASP.NET Core MVC app with individual user accounts, the *Views/Account/Login.cshtml* view contains the following model declaration:</span></span>

```cshtml
@model LoginViewModel
```

<span data-ttu-id="d7a03-198">생성 된 클래스에서 상속 `RazorPage<dynamic>`:</span><span class="sxs-lookup"><span data-stu-id="d7a03-198">The class generated inherits from `RazorPage<dynamic>`:</span></span>

```csharp
public class _Views_Account_Login_cshtml : RazorPage<LoginViewModel>
```

<span data-ttu-id="d7a03-199">Razor를 노출 한 `Model` 보기에 전달 된 모델에 액세스 하기 위한 속성:</span><span class="sxs-lookup"><span data-stu-id="d7a03-199">Razor exposes a `Model` property for accessing the model passed to the view:</span></span>

```cshtml
<div>The Login Email: @Model.Email</div>
```

<span data-ttu-id="d7a03-200">`@model` 지시문이이 속성의 유형을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7a03-200">The `@model` directive specifies the type of this property.</span></span> <span data-ttu-id="d7a03-201">지시문에 지정 된 `T` 에 `RazorPage<T>` 보기는 생성 된 클래스에서 파생 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7a03-201">The directive specifies the `T` in `RazorPage<T>` that the generated class that your view derives from.</span></span> <span data-ttu-id="d7a03-202">지정 하지 않으면는 `@model` 지시문의 `Model` 속성은 형식이 `dynamic`합니다.</span><span class="sxs-lookup"><span data-stu-id="d7a03-202">If you don't specify the `@model` directive, the `Model` property is of type `dynamic`.</span></span> <span data-ttu-id="d7a03-203">모델의 값은 컨트롤러에서 뷰에 전달 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d7a03-203">The value of the model is passed from the controller to the view.</span></span> <span data-ttu-id="d7a03-204">참조 [강력한 형식 모델 및 @model 키워드](xref:tutorials/first-mvc-app/adding-model#strongly-typed-models-keyword-label) 자세한 정보에 대 한 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7a03-204">See [Strongly typed models and the @model keyword](xref:tutorials/first-mvc-app/adding-model#strongly-typed-models-keyword-label) for more information.</span></span>

### <a name="inherits"></a>@inherits

<span data-ttu-id="d7a03-205">`@inherits` 지시문 보기 상속 된 클래스의 모든 권한을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7a03-205">The `@inherits` directive gives you full control of the class your view inherits:</span></span>

```cshtml
@inherits TypeNameOfClassToInheritFrom
```

<span data-ttu-id="d7a03-206">다음은 사용자 지정 Razor 페이지 유형:</span><span class="sxs-lookup"><span data-stu-id="d7a03-206">The following is a custom Razor page type:</span></span>

[!code-csharp[Main](razor/sample/Classes/CustomRazorPage.cs)]

<span data-ttu-id="d7a03-207">`CustomText` 는 보기에 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d7a03-207">The `CustomText` is displayed in a view:</span></span>

[!code-cshtml[Main](razor/sample/Views/Home/Contact10.cshtml)]

<span data-ttu-id="d7a03-208">코드는 다음과 같은 HTML을 렌더링 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d7a03-208">The code renders the following HTML:</span></span>

```html
<div>Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping a slop bucket on the street below.</div>
```

<span data-ttu-id="d7a03-209">사용할 수 없는 `@model` 및 `@inherits` 동일한 보기에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d7a03-209">You can't use `@model` and `@inherits` in the same view.</span></span> <span data-ttu-id="d7a03-210">사용할 수 있습니다 `@inherits` 에 *_ViewImports.cshtml* 파일을 가져오므로 보기:</span><span class="sxs-lookup"><span data-stu-id="d7a03-210">You can have `@inherits` in a *_ViewImports.cshtml* file that the view imports:</span></span>

[!code-cshtml[Main](razor/sample/Views/_ViewImportsModel.cshtml)]

<span data-ttu-id="d7a03-211">다음은 강력한 형식의 뷰의 예입니다.</span><span class="sxs-lookup"><span data-stu-id="d7a03-211">The following is an example of a strongly-typed view:</span></span>

[!code-cshtml[Main](razor/sample/Views/Home/Login1.cshtml)]

<span data-ttu-id="d7a03-212">경우 "rick@contoso.com" 전달 보기 모델에서는 다음과 같은 HTML 태그를 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7a03-212">If "rick@contoso.com" is passed in the model, the view generates the following HTML markup:</span></span>

```html
<div>The Login Email: rick@contoso.com</div>
<div>Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping a slop bucket on the street below.</div>
```

### <a name="inject"></a>@inject

<span data-ttu-id="d7a03-213">`@inject` 지시문을 사용 하면 서비스에서 삽입 하 여 [서비스 컨테이너](xref:fundamentals/dependency-injection) 보기에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d7a03-213">The `@inject` directive enables you to inject a service from your [service container](xref:fundamentals/dependency-injection) into your view.</span></span> <span data-ttu-id="d7a03-214">참조 [뷰로 종속성 주입](xref:mvc/views/dependency-injection) 자세한 정보에 대 한 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7a03-214">See [Dependency injection into views](xref:mvc/views/dependency-injection) for more information.</span></span>

### <a name="functions"></a>@functions

<span data-ttu-id="d7a03-215">`@functions` 지시문을 사용 하면 함수 수준 콘텐츠 뷰를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d7a03-215">The `@functions` directive enables you to add function-level content to a view:</span></span>

```cshtml
@functions { // C# Code }
```

<span data-ttu-id="d7a03-216">예:</span><span class="sxs-lookup"><span data-stu-id="d7a03-216">For example:</span></span>

[!code-cshtml[Main](razor/sample/Views/Home/Contact6.cshtml)]

<span data-ttu-id="d7a03-217">코드에서는 다음 HTML 태그를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="d7a03-217">The code generates the following HTML markup:</span></span>

```html
<div>From method: Hello</div>
```

<span data-ttu-id="d7a03-218">다음 코드는 생성된 된 Razor C# 클래스:</span><span class="sxs-lookup"><span data-stu-id="d7a03-218">The following code is the generated Razor C# class:</span></span>

[!code-csharp[Main](razor/sample/Classes/Views_Home_Test_cshtml.cs?range=1-19)]

### <a name="section"></a>@section

<span data-ttu-id="d7a03-219">`@section` 지시어와 함께 사용 되는 [레이아웃](xref:mvc/views/layout) HTML 페이지의 서로 다른 부분에 콘텐츠를 렌더링할 뷰를 사용할 수 있도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7a03-219">The `@section` directive is used in conjunction with the [layout](xref:mvc/views/layout) to enable views to render content in different parts of the HTML page.</span></span> <span data-ttu-id="d7a03-220">참조 [섹션](xref:mvc/views/layout#layout-sections-label) 자세한 정보에 대 한 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7a03-220">See [Sections](xref:mvc/views/layout#layout-sections-label) for more information.</span></span>

## <a name="tag-helpers"></a><span data-ttu-id="d7a03-221">태그 도우미</span><span class="sxs-lookup"><span data-stu-id="d7a03-221">Tag Helpers</span></span>

<span data-ttu-id="d7a03-222">와 관련 된 세 가지 지시문 없는 [태그 도우미](xref:mvc/views/tag-helpers/intro)합니다.</span><span class="sxs-lookup"><span data-stu-id="d7a03-222">There are three directives that pertain to [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span>

| <span data-ttu-id="d7a03-223">지시문</span><span class="sxs-lookup"><span data-stu-id="d7a03-223">Directive</span></span> | <span data-ttu-id="d7a03-224">함수</span><span class="sxs-lookup"><span data-stu-id="d7a03-224">Function</span></span> |
| --------- | -------- |
| [@addTagHelper](xref:mvc/views/tag-helpers/intro#add-helper-label) | <span data-ttu-id="d7a03-225">태그 도우미 보기를 사용할 수 있도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7a03-225">Makes Tag Helpers available to a view.</span></span> |
| [@removeTagHelper](xref:mvc/views/tag-helpers/intro#remove-razor-directives-label) | <span data-ttu-id="d7a03-226">보기에서 이전에 추가 된 태그 도우미를 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7a03-226">Removes Tag Helpers previously added from a view.</span></span> |
| [@tagHelperPrefix](xref:mvc/views/tag-helpers/intro#prefix-razor-directives-label) | <span data-ttu-id="d7a03-227">태그 도우미 지원 기능이 사용 하 고 명시적 태그 도우미 사용을 확인 하려면 태그 접두사를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7a03-227">Specifies a tag prefix to enable Tag Helper support and to make Tag Helper usage explicit.</span></span> |

## <a name="razor-reserved-keywords"></a><span data-ttu-id="d7a03-228">Razor 예약 키워드</span><span class="sxs-lookup"><span data-stu-id="d7a03-228">Razor reserved keywords</span></span>

### <a name="razor-keywords"></a><span data-ttu-id="d7a03-229">Razor 키워드</span><span class="sxs-lookup"><span data-stu-id="d7a03-229">Razor keywords</span></span>

* <span data-ttu-id="d7a03-230">페이지 (ASP.NET Core 2.0 이상 필요)</span><span class="sxs-lookup"><span data-stu-id="d7a03-230">page (Requires ASP.NET Core 2.0 and later)</span></span>
* <span data-ttu-id="d7a03-231">함수</span><span class="sxs-lookup"><span data-stu-id="d7a03-231">functions</span></span>
* <span data-ttu-id="d7a03-232">상속</span><span class="sxs-lookup"><span data-stu-id="d7a03-232">inherits</span></span>
* <span data-ttu-id="d7a03-233">모델</span><span class="sxs-lookup"><span data-stu-id="d7a03-233">model</span></span>
* <span data-ttu-id="d7a03-234">section</span><span class="sxs-lookup"><span data-stu-id="d7a03-234">section</span></span>
* <span data-ttu-id="d7a03-235">(현재 지원 하지 않는 ASP.NET Core) 도우미</span><span class="sxs-lookup"><span data-stu-id="d7a03-235">helper (Not currently supported by ASP.NET Core)</span></span>

<span data-ttu-id="d7a03-236">Razor 키워드로 이스케이프 `@(Razor Keyword)` (예를 들어 `@(functions)`).</span><span class="sxs-lookup"><span data-stu-id="d7a03-236">Razor keywords are escaped with `@(Razor Keyword)` (for example, `@(functions)`).</span></span>

### <a name="c-razor-keywords"></a><span data-ttu-id="d7a03-237">C# Razor 키워드</span><span class="sxs-lookup"><span data-stu-id="d7a03-237">C# Razor keywords</span></span>

* <span data-ttu-id="d7a03-238">case</span><span class="sxs-lookup"><span data-stu-id="d7a03-238">case</span></span>
* <span data-ttu-id="d7a03-239">do</span><span class="sxs-lookup"><span data-stu-id="d7a03-239">do</span></span>
* <span data-ttu-id="d7a03-240">default</span><span class="sxs-lookup"><span data-stu-id="d7a03-240">default</span></span>
* <span data-ttu-id="d7a03-241">for</span><span class="sxs-lookup"><span data-stu-id="d7a03-241">for</span></span>
* <span data-ttu-id="d7a03-242">foreach</span><span class="sxs-lookup"><span data-stu-id="d7a03-242">foreach</span></span>
* <span data-ttu-id="d7a03-243">if</span><span class="sxs-lookup"><span data-stu-id="d7a03-243">if</span></span>
* <span data-ttu-id="d7a03-244">else</span><span class="sxs-lookup"><span data-stu-id="d7a03-244">else</span></span>
* <span data-ttu-id="d7a03-245">잠금</span><span class="sxs-lookup"><span data-stu-id="d7a03-245">lock</span></span>
* <span data-ttu-id="d7a03-246">switch</span><span class="sxs-lookup"><span data-stu-id="d7a03-246">switch</span></span>
* <span data-ttu-id="d7a03-247">try</span><span class="sxs-lookup"><span data-stu-id="d7a03-247">try</span></span>
* <span data-ttu-id="d7a03-248">catch</span><span class="sxs-lookup"><span data-stu-id="d7a03-248">catch</span></span>
* <span data-ttu-id="d7a03-249">finally</span><span class="sxs-lookup"><span data-stu-id="d7a03-249">finally</span></span>
* <span data-ttu-id="d7a03-250">using</span><span class="sxs-lookup"><span data-stu-id="d7a03-250">using</span></span>
* <span data-ttu-id="d7a03-251">while</span><span class="sxs-lookup"><span data-stu-id="d7a03-251">while</span></span>

<span data-ttu-id="d7a03-252">C# Razor 키워드와 이중 이스케이프 해야 `@(@C# Razor Keyword)` (예를 들어 `@(@case)`).</span><span class="sxs-lookup"><span data-stu-id="d7a03-252">C# Razor keywords must be double-escaped with `@(@C# Razor Keyword)` (for example, `@(@case)`).</span></span> <span data-ttu-id="d7a03-253">첫 번째 `@` Razor 구문 분석기를 이스케이프 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7a03-253">The first `@` escapes the Razor parser.</span></span> <span data-ttu-id="d7a03-254">두 번째 `@` C# 파서를 이스케이프 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7a03-254">The second `@` escapes the C# parser.</span></span>

### <a name="reserved-keywords-not-used-by-razor"></a><span data-ttu-id="d7a03-255">Razor에서 사용 하지 않는 예약 된 키워드</span><span class="sxs-lookup"><span data-stu-id="d7a03-255">Reserved keywords not used by Razor</span></span>

* <span data-ttu-id="d7a03-256">네임스페이스(namespace)</span><span class="sxs-lookup"><span data-stu-id="d7a03-256">namespace</span></span>
* <span data-ttu-id="d7a03-257">클래스</span><span class="sxs-lookup"><span data-stu-id="d7a03-257">class</span></span>

## <a name="viewing-the-razor-c-class-generated-for-a-view"></a><span data-ttu-id="d7a03-258">보기에 대해 생성 된 Razor C# 클래스 보기</span><span class="sxs-lookup"><span data-stu-id="d7a03-258">Viewing the Razor C# class generated for a view</span></span>

<span data-ttu-id="d7a03-259">ASP.NET Core MVC 프로젝트에 다음 클래스를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7a03-259">Add the following class to your ASP.NET Core MVC project:</span></span>

[!code-csharp[Main](razor/sample/Utilities/CustomTemplateEngine.cs)]

<span data-ttu-id="d7a03-260">재정의 `RazorTemplateEngine` 와 MVC에서 추가 `CustomTemplateEngine` 클래스:</span><span class="sxs-lookup"><span data-stu-id="d7a03-260">Override the `RazorTemplateEngine` added by MVC with the `CustomTemplateEngine` class:</span></span>

[!code-csharp[Main](razor/sample/Startup.cs?highlight=4&range=10-14)]

<span data-ttu-id="d7a03-261">중단점을 설정한는 `return csharpDocument` 의 문은 `CustomTemplateEngine`합니다.</span><span class="sxs-lookup"><span data-stu-id="d7a03-261">Set a break point on the `return csharpDocument` statement of `CustomTemplateEngine`.</span></span> <span data-ttu-id="d7a03-262">프로그램 실행이 중단점에서 중지 되는 경우의 값을 보려면 `generatedCode`합니다.</span><span class="sxs-lookup"><span data-stu-id="d7a03-262">When program execution stops at the break point, view the value of `generatedCode`.</span></span>

![GeneratedCode의 텍스트 시각화 도우미 보기](razor/_static/tvr.png)

## <a name="view-lookups-and-case-sensitivity"></a><span data-ttu-id="d7a03-264">보기 조회 및 대/소문자 구분</span><span class="sxs-lookup"><span data-stu-id="d7a03-264">View lookups and case sensitivity</span></span>

<span data-ttu-id="d7a03-265">Razor 뷰 엔진 뷰에 대 한 대/소문자 구분 조회를 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7a03-265">The Razor view engine performs case-sensitive lookups for views.</span></span> <span data-ttu-id="d7a03-266">그러나 실제 조회는 기본 파일 시스템에 의해 결정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d7a03-266">However, the actual lookup is determined by the underlying file system:</span></span>

* <span data-ttu-id="d7a03-267">소스를 기반으로 하는 파일:</span><span class="sxs-lookup"><span data-stu-id="d7a03-267">File based source:</span></span> 
  * <span data-ttu-id="d7a03-268">대/소문자 구분 파일 시스템 (예: Windows), 운영 체제에서 실제 파일 공급자 조회는 대/소문자 구분 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7a03-268">On operating systems with case insensitive file systems (for example, Windows), physical file provider lookups are case insensitive.</span></span> <span data-ttu-id="d7a03-269">예를 들어 `return View("Test")` 일치 하는 항목으로 인해 */Views/Home/Test.cshtml*, */Views/home/test.cshtml*, 및 기타 대/소문자 구분 변형 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7a03-269">For example, `return View("Test")` results in matches for */Views/Home/Test.cshtml*, */Views/home/test.cshtml*, and any other casing variant.</span></span>
  * <span data-ttu-id="d7a03-270">대/소문자 구분 파일 시스템에 (예: Linux, OSX와 `EmbeddedFileProvider`), 조회는 대/소문자 구분 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7a03-270">On case sensitive file systems (for example, Linux, OSX, and with `EmbeddedFileProvider`), lookups are case sensitive.</span></span> <span data-ttu-id="d7a03-271">예를 들어 `return View("Test")` 구체적으로 일치 하는 항목 */Views/Home/Test.cshtml*합니다.</span><span class="sxs-lookup"><span data-stu-id="d7a03-271">For example, `return View("Test")` specifically matches */Views/Home/Test.cshtml*.</span></span>
* <span data-ttu-id="d7a03-272">뷰 미리 컴파일된: ASP.Net 2.0 이상 코어, 미리 컴파일된 뷰를 조회는 대/소문자 구분 모든 운영 체제에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d7a03-272">Precompiled views: With ASP.Net Core 2.0 and later, looking up precompiled views is case insensitive on all operating systems.</span></span> <span data-ttu-id="d7a03-273">동작은 Windows에서 물리적 파일 공급자의 동작과 동일 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7a03-273">The behavior is identical to physical file provider's behavior on Windows.</span></span> <span data-ttu-id="d7a03-274">미리 컴파일된 뷰를 두 가지 경우에만 다른 경우 조회 명확 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d7a03-274">If two precompiled views differ only in case, the result of lookup is non-deterministic.</span></span>

<span data-ttu-id="d7a03-275">개발자는 영역, 컨트롤러 및 작업 이름의 대/소문자를 파일 및 디렉터리 이름의 대/소문자와 일치 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="d7a03-275">Developers are encouraged to match the casing of file and directory names to the casing of area, controller, and action names.</span></span> <span data-ttu-id="d7a03-276">이렇게 하면 배포에 기본 파일 시스템에 관계 없이 해당 뷰를 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d7a03-276">This ensures your deployments will find their views regardless of the underlying file system.</span></span>
