---
title: "Razor 구문 참조 | Microsoft 문서"
author: rick-anderson
description: "Razor 구문에 자세히 설명"
keywords: ASP.NET Core, Razor
ms.author: riande
manager: wpickett
ms.date: 01/14/2017
ms.topic: article
ms.assetid: a89a8433-8b0e-4795-a73a-82114d27e233
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/views/razor
translationtype: Machine Translation
ms.sourcegitcommit: 4151c0561303eb2fae6d29ee3aa4019da365c86e
ms.openlocfilehash: 10f571b2e7434e03c8e476dfe96d7c88fcf4cc98
ms.lasthandoff: 03/23/2017

---
# <a name="razor-syntax"></a>Razor 구문

여 [Taylor Mullen](https://twitter.com/ntaylormullen) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)

## <a name="what-is-razor"></a>Razor는 무엇입니까?

Razor 구문을 기반으로 서버 코드를 웹 페이지에 포함 하는 데는 합니다. Razor 구문이 C# 및 HTML Razor 태그 이루어져 있습니다. 일반적으로 Razor 들어 있는 파일을 *.cshtml* 파일 확장명입니다.

## <a name="rendering-html"></a>HTML 렌더링

기본 Razor 언어 HTML입니다. Razor에서 HTML을 렌더링는 HTML 파일에 차이가 없습니다. 다음 태그로 Razor 파일:

```html
<p>Hello World</p>
   ```

가 변경 하지로 렌더링 `<p>Hello World</p>` 서버입니다.

## <a name="razor-syntax"></a>Razor 구문

C# 지원 하 고 사용 하는 razor는 `@` HTML에 C#에서 전환 기호입니다. Razor는 C# 식을 계산 하 고 HTML 출력에 렌더링 합니다. Razor에 C# 또는 Razor 특정 태그에 HTML을 전환할 수 있습니다. 때는 `@` 기호 뒤는 [Razor 예약 키워드](#razor-reserved-keywords) Razor 특정 태그에 전환, 그렇지 않으면 일반 C# 식으로 전환 합니다.

<a name=escape-at-label></a>

포함 된 HTML `@` 기호 초를 사용 하 여 이스케이프 해야 할 수 `@` 기호입니다. 예:

```html
<p>@@Username</p>
   ```

다음 HTML을 렌더링 합니다.

```html
<p>@Username</p>
   ```

<a name=razor-email-ref></a>

HTML 특성 및 전자 메일 주소를 포함 하는 콘텐츠 취급 하지 말라는 `@` 전환 문자로 기호입니다.

   `<a href="mailto:Support@contoso.com">Support@contoso.com</a>`

## <a name="implicit-razor-expressions"></a>암시적 Razor 식

암시적 Razor 식은로 시작 `@` 나옵니다. C# 코드입니다. 예:

```html
<p>@DateTime.Now</p>
<p>@DateTime.IsLeapYear(2016)</p>
```

C# 제외 하 고 `await` 키워드 암시적 식에 공백이 포함 되지 않아야 합니다. 예를 들어 C# 문에 명확한 끝으로 공간 intermingle 수 있습니다.

```html
<p>@await DoSomething("hello", "world")</p>
```

## <a name="explicit-razor-expressions"></a>명시적 Razor 식

명시적 Razor 식 이루어져는 @ 기호에 분산 된 괄호입니다. 예를 들어, 렌더링 시간을 지난 주:

```html
<p>Last week this time: @(DateTime.Now - TimeSpan.FromDays(7))</p>
```

내에서 모든 콘텐츠는 @ () 괄호 평가 되 고 출력에 렌더링 합니다.

일반적으로 암시적 식에 공백을 포함할 수 없습니다. 예를 들어 아래 코드에서&1; 주일은 현재 시간에서 차감 되지 않습니다.

[!code-html[주](razor/sample/Views/Home/Contact.cshtml?range=20)]

다음 HTML을 렌더링합니다.

```html
<p>Last week: 7/7/2016 4:39:52 PM - TimeSpan.FromDays(7)</p>
   ```

텍스트 식 결과와 연결할 명시적 식을 사용할 수 있습니다.

<!-- literal_block {"ids": [], "linenos": false, "xml:space": "preserve", "language": "none", "highlight_args": {"hl_lines": [5]}} -->

```none
@{
    var joe = new Person("Joe", 33);
 }

<p>Age@(joe.Age)</p>
```

명시적 식 없이 `<p>Age@joe.Age</p>` 전자 메일 주소로 간주 됩니다 및 `<p>Age@joe.Age</p>` 렌더링 됩니다. 명시적 식으로 쓸 때 `<p>Age33</p>` 렌더링 됩니다.

<a name=expression-encoding-label></a>

## <a name="expression-encoding"></a>식 인코딩

문자열에 평가 하는 C# 식을 인코딩된 HTML입니다. C# 식을 계산 하는 `IHtmlContent` 통해 직접 렌더링 되는 *IHtmlContent.WriteTo*합니다. 하지는 C# 식 *IHtmlContent* 문자열로 변환 됩니다 (여 *ToString*) 하 고 렌더링 되기 전에 인코딩해야 합니다. 예를 들어, 다음 Razor 태그:

```html
@("<span>Hello World</span>")
   ```

이 HTML을 렌더링합니다.

```html
&lt;span&gt;Hello World&lt;/span&gt;
   ```

브라우저에서 렌더링 합니다.

`<span>Hello World</span>`

`HtmlHelper``Raw` 출력은 인코딩된 아니라 HTML 태그로 렌더링 됩니다.

>[!WARNING]
> 사용 하 여 `HtmlHelper.Raw` 정리 되지 않은 사용자 입력은 보안상 위험 합니다. 사용자 입력은 악성 JavaScript 또는 다른 악용 기법 포함 될 수 있습니다. 사용자 입력을 정리 하는 것은 어렵습니다, 사용 하지 않도록 `HtmlHelper.Raw` 사용자 입력에 있습니다.

다음과 같은 Razor 태그:

```html
@Html.Raw("<span>Hello World</span>")
   ```

이 HTML을 렌더링합니다.

```html
<span>Hello World</span>
   ```

<a name=razor-code-blocks-label></a>

## <a name="razor-code-blocks"></a>Razor 코드 블록

Razor 코드 블록 시작 `@` 묶여 및 `{}`합니다. 정규식과 달리 C# 코드 블록 내부에서 코드 렌더링 되지 않습니다. 동일한 범위를 공유 하 고 순서에 정의 된 코드 블록 및 Razor 페이지의 식 (즉, 코드 블록에 선언 됩니다 뒷부분에 나오는 코드 블록 및 식에 대 한 범위에서).

```none
@{
    var output = "Hello World";
}

<p>The rendered result: @output</p>
```

렌더링 합니다.

```html
<p>The rendered result: Hello World</p>
   ```

<a name=implicit-transitions-label></a>

### <a name="implicit-transitions"></a>암시적 변환

코드 블록에 기본 언어는 C#, 하지만 HTML로 다시 전환할 수 있습니다. HTML 코드 블록 내에서 HTML 렌더링으로 다시 전환 됩니다.

```none
@{
    var inCSharp = true;
    <p>Now in HTML, was in C# @inCSharp</p>
}
```

<a name=explicit-delimited-transition-label></a>

### <a name="explicit-delimited-transition"></a>명시적 구분 기호로 분리 된 전환

HTML을 렌더링 해야 하는 코드 블록의 하위 섹션을 정의 하려면 Razor로 렌더링 하는 문자를 묶습니다 `<text>` 태그:

<!-- literal_block {"ids": [], "linenos": false, "xml:space": "preserve", "language": "none", "highlight_args": {"hl_lines": [4]}} -->

```none
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    <text>Name: @person.Name</text>
}
```

일반적으로 HTML 태그 둘러싸인 HTML을 렌더링 하려면이 방법의 사용 합니다. HTML 또는 Razor 태그가 없는 Razor 런타임 오류가 발생 합니다.

<a name=explicit-line-transition-with-label></a>

### <a name="explicit-line-transition-with-"></a>사용 하 여 명시적 선 전환`@:`

코드 블록 안에 줄의 나머지 부분을 HTML로 렌더링을를 사용 하 여는 `@:` 구문:

<!-- literal_block {"ids": [], "linenos": false, "xml:space": "preserve", "language": "none", "highlight_args": {"hl_lines": [4]}} -->

```none
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    @:Name: @person.Name
}
```

없이 `@:` 위 코드에서 런타임 오류가 발생 하는 Razor 가져와야 합니다.

<a name=control-structures-razor-label></a>

## <a name="control-structures"></a>제어 구조

제어 구조는 코드 블록의 확장입니다. 코드 블록 (태그, 인라인 C#으로 전환)도의 모든 측면은 다음 구조체에 적용 됩니다.

### <a name="conditionals-if-else-if-else-and-switch"></a>조건 `@if`, `else if`, `else` 및`@switch`

`@if` 제품군 제어 코드를 실행할 때:

```none
@if (value % 2 == 0)
{
    <p>The value was even</p>
}
```

`else`및 `else if` 필요 하지는 `@` 기호:

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

다음과 같이 switch 문을 사용할 수 있습니다.

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

### <a name="looping-for-foreach-while-and-do-while"></a>Looping `@for`, `@foreach`, `@while`, and`@do while`

템플릿 기반 HTML 제어 문을 반복을 렌더링할 수 있습니다. 예를 들어, 직원의 목록을 렌더링 합니다.

```none
@{
    var people = new Person[]
    {
          new Person("John", 33),
          new Person("Doe", 41),
    };
}
```

다음 반복 문 중 하나를 사용할 수 있습니다.

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

### <a name="compound-using"></a>복합`@using`

C#의 using 문을 사용 하는 개체가 삭제 되는지 확인 합니다. Razor에서 동일한이 메커니즘 수 만드는 데 사용할 [HTML 도우미](html-helpers.md) 추가 콘텐츠를 포함 하는 합니다. 예를 들어, 이용 하면 [ 🔧 HTML 도우미](html-helpers.md) form 태그를 렌더링 하는 `@using` 문:

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

범위와 위와 같은 수준 작업을 수행할 수도 있습니다 [태그 도우미](tag-helpers/index.md)합니다.

### <a name="try-catch-finally"></a>`@try`, `catch`, `finally`

예외 처리는 C# 유사 합니다.

[!code-html[주](razor/sample/Views/Home/Contact7.cshtml)]

### `@lock`

Razor에 잠금 문으로 중요 섹션을 보호 하는 기능이 있습니다.

```none
@lock (SomeLock)
{
    // Do critical section work
}
```

### <a name="comments"></a>설명

Razor에는 C# 및 HTML 주석을 지원 합니다. 다음 태그로 바꿉니다.

```none
@{
    /* C# comment. */
    // Another C# comment.
}
<!-- HTML comment -->
```

서버에서 렌더링 됩니다.

```none
<!-- HTML comment -->
```

페이지를 렌더링 하기 전에 서버에서 razor 주석이 제거 됩니다. Razor를 사용 하 여 `@*  *@` 을 메모를 구분 합니다. 다음 코드는 주석 처리 하므로 서버 태그를 렌더링 하지 것입니다.

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

## <a name="directives"></a>지시문

Razor 지시문 예약 된 키워드 다음으로 암시적 식으로 표현 됩니다는 `@` 기호입니다. 일반적으로 지시문 페이지 구문 분석 되는 방식을 변경 되거나 Razor 페이지 내에서 서로 다른 기능을 사용 하도록 설정 됩니다.

Razor 뷰의 코드를 생성 하는 방법을 이해 쉬워집니다 것 지시문의 작동 방식을 이해 하려면. Razor 페이지는 C# 파일을 생성 하는 데 사용 됩니다. 이 Razor 페이지:

[!code-html[주](razor/sample/Views/Home/Contact8.cshtml)]

다음과 유사 하 게 하는 클래스를 생성합니다.

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

[보기에 대해 생성 되는 Razor C# 클래스 보기](#razor-customcompilationservice-label) 이렇게 생성 된 클래스를 확인 하는 방법에 설명 합니다.

### `@using`

`@using` 지시문을 추가 합니다 c# `using` 생성 된 razor 페이지 지시문:

[!code-html[주](razor/sample/Views/Home/Contact9.cshtml)]

### `@model`

`@model` 지시문을 사용 하면 Razor 페이지에 전달 하는 모델의 형식을 지정할 수 있습니다. 이 도구는 다음 구문을 사용합니다.

```none
@model TypeNameOfModel
   ```

예를 들어, 개별 사용자 계정으로 ASP.NET 핵심 MVC 응용 프로그램을 만들면는 *Views/Account/Login.cshtml* Razor 뷰 모델 선언이 있습니다.

```csharp
@model LoginViewModel
   ```

클래스 예제에서 생성 된 클래스에서 상속 `RazorPage<dynamic>`합니다. 추가 하 여는 `@model` 상속 된 기능을 제어 합니다. 예

```csharp
@model LoginViewModel
   ```

다음 클래스를 생성합니다.

```csharp
public class _Views_Account_Login_cshtml : RazorPage<LoginViewModel>
   ```

Razor 페이지에 노출 된 `Model` 모델에 액세스 하는 것에 대 한 속성 페이지에 전달 합니다.

```html
<div>The Login Email: @Model.Email</div>
   ```

`@model` 지시문에는이 속성의 형식을 지정 된 (지정 하 여는 `T` 에서 `RazorPage<T>` 페이지에 대 한 생성 된 클래스에서 파생 된). 지정 하지 않으면는 `@model` 지시문은 `Model` 속성 형식이 됩니다 `dynamic`합니다. 모델의 값은 보기에는 컨트롤러에서 전달 됩니다. 참조 [강력한 모델 형식 및 @model 키워드](../../tutorials/first-mvc-app/adding-model.md#strongly-typed-models-keyword-label) 에 대 한 자세한 내용은 합니다.

### `@inherits`

`@inherits` 지시문 Razor 페이지 상속 된 클래스에 대해 모든 권한을 제공 합니다.

```none
@inherits TypeNameOfClassToInheritFrom
   ```

예를 들어, 다음과 같은 사용자 지정 Razor 페이지 유형 했습니다 가정해 보겠습니다.

[!code-csharp[주](razor/sample/Classes/CustomRazorPage.cs)]

다음과 같은 Razor 생성 `<div>Custom text: Hello World</div>`합니다.

[!code-html[주](razor/sample/Views/Home/Contact10.cshtml)]

사용할 수 없는 `@model` 및 `@inherits` 같은 페이지에 있습니다. 사용할 수 있습니다 `@inherits` 에 *_ViewImports.cshtml* Razor 페이지 가져오는 파일입니다. 예를 들어, Razor 보기 가져온 다음 *_ViewImports.cshtml* 파일:

[!code-html[주](razor/sample/Views/_ViewImportsModel.cshtml)]

강력한 형식의 Razor 페이지

[!code-html[주](razor/sample/Views/Home/Login1.cshtml)]

이 HTML 태그를 생성합니다.

```none
<div>The Login Email: Rick@contoso.com</div>
<div>Custom text: Hello World</div>
```

전달 될 때 "[Rick@contoso.com](mailto:Rick@contoso.com)" 모델에서:

   참조 [레이아웃](layout.md) 에 대 한 자세한 내용은 합니다.

### `@inject`

`@inject` 지시문을 사용 하면 서비스에서 삽입 하 여 [서비스 컨테이너](../../fundamentals/dependency-injection.md) 사용 하기 위해 Razor 페이지에 있습니다. 참조 [뷰로 종속성 주입](dependency-injection.md)합니다.

### `@functions`

`@functions` 지시문 Razor 페이지를 함수 수준 콘텐츠를 추가할 수 있습니다. 사용되는 구문은 다음과 같습니다.

```none
@functions { // C# Code }
   ```

예:

[!code-html[주](razor/sample/Views/Home/Contact6.cshtml)]

다음 HTML 태그를 생성합니다.

```none
<div>From method: Hello</div>
   ```

생성 된 Razor C#는 다음과 같습니다.

[!code-csharp[주](razor/sample/Classes/Views_Home_Test_cshtml.cs?range=1-19)]

### `@section`

`@section` 지시문과 함께에서 사용 하는 [레이아웃 페이지](layout.md) 렌더링된 된 HTML 페이지의 다른 부분에 콘텐츠를 렌더링할 뷰를 사용할 수 있도록 합니다. 참조 [섹션](layout.md#layout-sections-label) 에 대 한 자세한 내용은 합니다.

## <a name="taghelpers"></a>TagHelpers

다음 [태그 도우미](tag-helpers/index.md) 지시문이 제공 된 링크에 자세히 설명 되어 있습니다.

* [@addTagHelper](tag-helpers/intro.md#add-helper-label)
* [@removeTagHelper](tag-helpers/intro.md#remove-razor-directives-label)
* [@tagHelperPrefix](tag-helpers/intro.md#prefix-razor-directives-label)

<a name=razor-reserved-keywords-label></a>

## <a name="razor-reserved-keywords"></a>Razor 예약 키워드

### <a name="razor-keywords"></a>Razor 키워드

* 함수
* 상속
* 모델
* section
* 도우미 (에서 지원 되지 ASP.NET 핵심.)

Razor 키워드를 사용 하 여 이스케이프 수 `@(Razor Keyword)`, 예를 들어 `@(functions)`합니다. 아래 전체 샘플을 참조 하십시오.

### <a name="c-razor-keywords"></a>C# Razor 키워드

* case
* do
* default
* for
* foreach
* if
* 잠금
* switch
* try
* using
* while

C# Razor 키워드 해야 double로 이스케이프 `@(@C# Razor Keyword)`, 예를 들어 `@(@case)`합니다. 첫 번째 `@` Razor parser 이스케이프 두 번째 `@` C# 파서는 이스케이프 합니다. 아래 전체 샘플을 참조 하십시오.

### <a name="reserved-keywords-not-used-by-razor"></a>Razor에서 사용 하지 않는 예약 된 키워드

* namespace
* 클래스

<a name=razor-customcompilationservice-label></a>

## <a name="viewing-the-razor-c-class-generated-for-a-view"></a>보기에 대해 생성 되는 Razor C# 클래스 보기

ASP.NET Core MVC 프로젝트에 다음 클래스를 추가 합니다.

[!code-csharp[주](razor/sample/Services/CustomCompilationService.cs)]

재정의 `ICompilationService` 위의 클래스;와 MVC 하 여 추가 합니다.

[!code-csharp[주](razor/sample/Startup.cs?highlight=4&range=29-33)]

에 중단점을 설정 된 `Compile` 메서드의 `CustomCompilationService` 및 보기 `compilationContent`합니다.

![텍스트 시각화 도우미 뷰의 compilationContent](razor/_static/tvr.png)

> [!NOTE]
> 보기 조회 대/소문자를 구분 합니다. 명명 된 뷰 검색 (seek) 프로그램 컨트롤러 라우팅 경우 `Index` (*Index.cshtml*) 보기 파일 이름을 지정한 하지만 `index` (*index.cshtml*), 예외를 수신 합니다:`InvalidOperationException: The view 'Index' was not found.`

