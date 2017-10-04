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
# <a name="razor-syntax-for-aspnet-core"></a>ASP.NET Core에 대 한 razor 구문

여 [Rick Anderson](https://twitter.com/RickAndMSFT), [Luke Latham](https://github.com/guardrex), 및 [Taylor Mullen](https://twitter.com/ntaylormullen)

Razor은 웹 페이지에 서버 기반 코드를 포함 하는 것에 대 한 태그 구문입니다. Razor 구문 Razor 태그, C#, 및 HTML로 이루어져 있습니다. Razor를 일반적으로 들어 있는 파일을 *.cshtml* 파일 확장명입니다.

## <a name="rendering-html"></a>HTML 렌더링

기본 Razor 언어 HTML입니다. 렌더링 HTML Razor 태그에서이 HTML 파일에서 HTML을 렌더링 하는 방법과 다르지 않습니다. HTML 태그에 배치 하는 경우는 *.cshtml* Razor 파일을 변경 하지 않고 서버에서 렌더링 됩니다.

## <a name="razor-syntax"></a>Razor 구문

Razor C# 지원 및 사용 하 여는 `@` C#으로 HTML에서 전환 기호입니다. Razor C# 식을 계산 하 고 HTML 출력에이 렌더링 합니다.

경우는 `@` 기호 뒤는 [Razor 예약 키워드](#razor-reserved-keywords), Razor 특정 태그에 전환 합니다. 그렇지 않은 경우 일반 C# 식으로 전환 됩니다.

이스케이프할는 `@` Razor 태그에서 기호, 초를 사용 하 여 `@` 기호:

```cshtml
<p>@@Username</p>
```

코드는 단일 HTML로 렌더링은 `@` 기호:

```html
<p>@Username</p>
```

HTML 특성 및 전자 메일 주소를 포함 하는 내용을 처리 하지 않습니다는 `@` 전환 문자로 기호입니다. 다음 예제에서 전자 메일 주소 Razor 구문 분석 하 여 그대로 유지 됩니다.

```cshtml
<a href="mailto:Support@contoso.com">Support@contoso.com</a>
```

## <a name="implicit-razor-expressions"></a>암시적 Razor 식

암시적 Razor 식은로 시작 `@` C# 코드가 뒤에 오는:

```cshtml
<p>@DateTime.Now</p>
<p>@DateTime.IsLeapYear(2016)</p>
```

C# 제외 하 고 `await` 키워드, 암시적 식 공백을 포함 해서는 안 됩니다. C# 문에 명확한 종료 하는 경우 공간 intermingle 수 있습니다.

```cshtml
<p>@await DoSomething("hello", "world")</p>
```

## <a name="explicit-razor-expressions"></a>명시적 Razor 식

Razor 식은 명시적으로 구성 될는 `@` 균형 잡힌 괄호 기호입니다. 지난 주 시간을 렌더링 하려면 다음 Razor 태그 사용 됩니다.

```cshtml
<p>Last week this time: @(DateTime.Now - TimeSpan.FromDays(7))</p>
```

내에서 모든 콘텐츠는 `@()` 괄호 평가 되 고 출력에 렌더링 합니다.

일반적으로 이전 섹션에 설명 된 암시적 식, 공백을 포함할 수 없습니다. 다음 코드에서 1 주일 현재 시간에서 차감 되지 않습니다.

[!code-cshtml[Main](razor/sample/Views/Home/Contact.cshtml?range=17)]

코드는 다음과 같은 HTML을 렌더링 됩니다.

```html
<p>Last week: 7/7/2016 4:39:52 PM - TimeSpan.FromDays(7)</p>
```

연결 된 식 결과 텍스트에 명시적 식을 사용할 수 있습니다.

```cshtml
@{
    var joe = new Person("Joe", 33);
}

<p>Age@(joe.Age)</p>
```

명시적 식 없이 `<p>Age@joe.Age</p>` 전자 메일 주소로 처리 및 `<p>Age@joe.Age</p>` 렌더링 됩니다. 명시적 식을으로 쓸 때 `<p>Age33</p>` 렌더링 됩니다.

## <a name="expression-encoding"></a>식 인코딩

C# 식을 문자열로 평가 하는 인코딩된 HTML입니다. C# 식을 계산 하는 `IHtmlContent` 통해 직접 렌더링 되는 `IHtmlContent.WriteTo`합니다. C# 식으로 계산 하지는 `IHtmlContent` 하 여 문자열로 변환 `ToString` 되 고 렌더링 하는 전에 인코딩된 합니다.

```cshtml
@("<span>Hello World</span>")
```

코드는 다음과 같은 HTML을 렌더링 됩니다.

```html
&lt;span&gt;Hello World&lt;/span&gt;
```

HTML은 브라우저에 표시 됩니다.

```
<span>Hello World</span>
```

`HtmlHelper.Raw`출력 인코딩된 않지만 HTML 태그로 렌더링 합니다.

> [!WARNING]
> 사용 하 여 `HtmlHelper.Raw` unsanitized 사용자 입력은 보안상 위험 합니다. 사용자 입력에는 악의적인 JavaScript 또는 다른 악용 기법 포함 될 수 있습니다. 사용자 입력을 정리 하는 것은 어렵습니다. 사용 하지 않도록 `HtmlHelper.Raw` 사용자 입력을 사용 합니다.

```cshtml
@Html.Raw("<span>Hello World</span>")
```

코드는 다음과 같은 HTML을 렌더링 됩니다.

```html
<span>Hello World</span>
```

## <a name="razor-code-blocks"></a>Razor 코드 블록

Razor 코드 블록 시작 `@` 묶여 및 `{}`합니다. 식에서와 달리 C# 코드 블록 내부에서 코드 렌더링 되지 않습니다. 코드 블록 및 뷰의 식에서에서 같은 범위를 공유 및 순서에 정의 됩니다.

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

코드는 다음과 같은 HTML을 렌더링 됩니다.

```html
<p>The future depends on what you do today. - Mahatma Gandhi</p>
<p>Hate cannot drive out hate, only love can do that. - Martin Luther King, Jr.</p>
```

### <a name="implicit-transitions"></a>암시적 변환

코드 블록에 기본 언어는 C#, 하지만 HTML로 다시 전환할 수 있습니다.

```cshtml
@{
    var inCSharp = true;
    <p>Now in HTML, was in C# @inCSharp</p>
}
```

### <a name="explicit-delimited-transition"></a>명시적 구분 기호로 분리 된 전환

HTML 렌더링 해야 하는 코드 블록의 하위 섹션을 정의 하려면 코드 감싸기 렌더링 하기 위한 문자 Razor  **\<텍스트 >** 태그:

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    <text>Name: @person.Name</text>
}
```

HTML 태그로 묶인 없는 HTML을 렌더링 하려면이 방법을 사용 합니다. HTML 또는 Razor 태그가 없는 Razor 런타임 오류가 발생 합니다.

 **\<텍스트 >** 태그에서 콘텐츠를 렌더링할 때 공백을 제어도 유용 합니다. 사이 있는 내용을는  **\<텍스트 >** 태그를 렌더링 하기 전이나 후에 공백이 없어야 하 고는  **\<텍스트 >** 태그의 HTML 출력에 나타납니다.

### <a name="explicit-line-transition-with-"></a>명시적 줄 전환을 @:

코드 블록 안에 줄의 나머지 부분을 HTML로 렌더링, 사용 된 `@:` 구문:

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    @:Name: @person.Name
}
```

없이 `@:` Razor 런타임 오류 코드에서 발생 합니다.

## <a name="control-structures"></a>제어 구조

제어 구조는 코드 블록의 확장입니다. 코드 블록 (인라인 C# 태그로 전환)도의 모든 측면은 다음 구조에 적용 됩니다.

### <a name="conditionals-if-else-if-else-and-switch"></a>조건부 @if, else if else, 및@switch

`@if`코드를 실행할 때 컨트롤:

```cshtml
@if (value % 2 == 0)
{
    <p>The value was even.</p>
}
```

`else`및 `else if` 필요 하지 않습니다는 `@` 기호:

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

다음과 같이 switch 문을 사용할 수 있습니다.

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

### <a name="looping-for-foreach-while-and-do-while"></a>반복 @for, @foreach, @while, 및 @do 동안

템플릿 기반 HTML 제어 문을 반복을 렌더링할 수 있습니다. 사람 목록이 렌더링 합니다.

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

다음 반복 문 중 하나를 사용할 수 있습니다.

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

### <a name="compound-using"></a>복합@using

C#에서는 `using` 문을 사용 하는 개체가 삭제 되도록 합니다. Razor, 동일한 메커니즘 추가 콘텐츠를 포함 하는 HTML 도우미 만들기에 사용 됩니다. 예를 들어, form 태그를 렌더링 하는 HTML 도우미를 사용할 수 있습니다는 `@using` 문:

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

범위 수준 작업을 수행할 수도 있습니다 [태그 도우미](xref:mvc/views/tag-helpers/intro)합니다.

### <a name="try-catch-finally"></a>@trycatch, finally

예외 처리는 C# 유사 합니다.

[!code-cshtml[Main](razor/sample/Views/Home/Contact7.cshtml)]

### <a name="lock"></a>@lock

Razor에 임계 섹션 lock 문을 보호 하는 기능이 있습니다.

```cshtml
@lock (SomeLock)
{
    // Do critical section work
}
```

### <a name="comments"></a>설명

Razor 주석 C# 및 HTML을 지원합니다.

```cshtml
@{
    /* C# comment */
    // Another C# comment
}
<!-- HTML comment -->
```

코드는 다음과 같은 HTML을 렌더링 됩니다.

```html
<!-- HTML comment -->
```

Razor 주석 웹 페이지를 렌더링 하기 전에 서버에서 제거 됩니다. Razor를 사용 하 여 `@*  *@` 를 주석을 구분 합니다. 다음 코드 주석 처리 되어, 서버에서 태그를 렌더링 하지 않습니다.

```cshtml
@*
    @{
        /* C# comment */
        // Another C# comment
    }
    <!-- HTML comment -->
*@
```

## <a name="directives"></a>지시문

Razor 지시문 다음 예약 된 키워드를 사용 하 여 암시적 식으로 표시 됩니다는 `@` 기호입니다. 지시문에는 일반적으로 보기를 구문 분석 또는 다른 기능을 활성화 하는 방법을 변경 합니다.

Razor 뷰의 코드를 생성 하는 방법을 이해 쉽게 지시문의 작동 방식을 이해 하려면 있습니다.

[!code-html[Main](razor/sample/Views/Home/Contact8.cshtml)]

코드에서는 클래스에는 다음과 유사한 오류가 생성 됩니다.

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

이 문서의 섹션의 뒷부분에 나오는 [보기에 대해 생성 된 Razor C# 클래스 보기](#viewing-the-razor-c-class-generated-for-a-view) 이렇게 생성 된 클래스를 확인 하는 방법에 설명 합니다.

### <a name="using"></a>@using

`@using` 지시문 추가 하는 C# `using` 지시문을 생성 된 보기에:

[!code-cshtml[Main](razor/sample/Views/Home/Contact9.cshtml)]

### <a name="model"></a>@model

`@model` 지시문 보기에 전달 된 모델의 유형을 지정 합니다.

```cshtml
@model TypeNameOfModel
```

개별 사용자 계정으로 ASP.NET Core MVC 응용 프로그램을 만들면는 *Views/Account/Login.cshtml* 보기 모델 선언이 있습니다.

```cshtml
@model LoginViewModel
```

생성 된 클래스에서 상속 `RazorPage<dynamic>`:

```csharp
public class _Views_Account_Login_cshtml : RazorPage<LoginViewModel>
```

Razor를 노출 한 `Model` 보기에 전달 된 모델에 액세스 하기 위한 속성:

```cshtml
<div>The Login Email: @Model.Email</div>
```

`@model` 지시문이이 속성의 유형을 지정 합니다. 지시문에 지정 된 `T` 에 `RazorPage<T>` 보기는 생성 된 클래스에서 파생 합니다. 지정 하지 않으면는 `@model` 지시문의 `Model` 속성은 형식이 `dynamic`합니다. 모델의 값은 컨트롤러에서 뷰에 전달 됩니다. 참조 [강력한 형식 모델 및 @model 키워드](xref:tutorials/first-mvc-app/adding-model#strongly-typed-models-keyword-label) 자세한 정보에 대 한 합니다.

### <a name="inherits"></a>@inherits

`@inherits` 지시문 보기 상속 된 클래스의 모든 권한을 제공 합니다.

```cshtml
@inherits TypeNameOfClassToInheritFrom
```

다음은 사용자 지정 Razor 페이지 유형:

[!code-csharp[Main](razor/sample/Classes/CustomRazorPage.cs)]

`CustomText` 는 보기에 표시 됩니다.

[!code-cshtml[Main](razor/sample/Views/Home/Contact10.cshtml)]

코드는 다음과 같은 HTML을 렌더링 됩니다.

```html
<div>Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping a slop bucket on the street below.</div>
```

사용할 수 없는 `@model` 및 `@inherits` 동일한 보기에 있습니다. 사용할 수 있습니다 `@inherits` 에 *_ViewImports.cshtml* 파일을 가져오므로 보기:

[!code-cshtml[Main](razor/sample/Views/_ViewImportsModel.cshtml)]

다음은 강력한 형식의 뷰의 예입니다.

[!code-cshtml[Main](razor/sample/Views/Home/Login1.cshtml)]

경우 "rick@contoso.com" 전달 보기 모델에서는 다음과 같은 HTML 태그를 생성 합니다.

```html
<div>The Login Email: rick@contoso.com</div>
<div>Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping a slop bucket on the street below.</div>
```

### <a name="inject"></a>@inject

`@inject` 지시문을 사용 하면 서비스에서 삽입 하 여 [서비스 컨테이너](xref:fundamentals/dependency-injection) 보기에 있습니다. 참조 [뷰로 종속성 주입](xref:mvc/views/dependency-injection) 자세한 정보에 대 한 합니다.

### <a name="functions"></a>@functions

`@functions` 지시문을 사용 하면 함수 수준 콘텐츠 뷰를 추가할 수 있습니다.

```cshtml
@functions { // C# Code }
```

예:

[!code-cshtml[Main](razor/sample/Views/Home/Contact6.cshtml)]

코드에서는 다음 HTML 태그를 생성합니다.

```html
<div>From method: Hello</div>
```

다음 코드는 생성된 된 Razor C# 클래스:

[!code-csharp[Main](razor/sample/Classes/Views_Home_Test_cshtml.cs?range=1-19)]

### <a name="section"></a>@section

`@section` 지시어와 함께 사용 되는 [레이아웃](xref:mvc/views/layout) HTML 페이지의 서로 다른 부분에 콘텐츠를 렌더링할 뷰를 사용할 수 있도록 합니다. 참조 [섹션](xref:mvc/views/layout#layout-sections-label) 자세한 정보에 대 한 합니다.

## <a name="tag-helpers"></a>태그 도우미

와 관련 된 세 가지 지시문 없는 [태그 도우미](xref:mvc/views/tag-helpers/intro)합니다.

| 지시문 | 함수 |
| --------- | -------- |
| [@addTagHelper](xref:mvc/views/tag-helpers/intro#add-helper-label) | 태그 도우미 보기를 사용할 수 있도록 합니다. |
| [@removeTagHelper](xref:mvc/views/tag-helpers/intro#remove-razor-directives-label) | 보기에서 이전에 추가 된 태그 도우미를 제거 합니다. |
| [@tagHelperPrefix](xref:mvc/views/tag-helpers/intro#prefix-razor-directives-label) | 태그 도우미 지원 기능이 사용 하 고 명시적 태그 도우미 사용을 확인 하려면 태그 접두사를 지정 합니다. |

## <a name="razor-reserved-keywords"></a>Razor 예약 키워드

### <a name="razor-keywords"></a>Razor 키워드

* 페이지 (ASP.NET Core 2.0 이상 필요)
* 함수
* 상속
* 모델
* section
* (현재 지원 하지 않는 ASP.NET Core) 도우미

Razor 키워드로 이스케이프 `@(Razor Keyword)` (예를 들어 `@(functions)`).

### <a name="c-razor-keywords"></a>C# Razor 키워드

* case
* do
* default
* for
* foreach
* if
* else
* 잠금
* switch
* try
* catch
* finally
* using
* while

C# Razor 키워드와 이중 이스케이프 해야 `@(@C# Razor Keyword)` (예를 들어 `@(@case)`). 첫 번째 `@` Razor 구문 분석기를 이스케이프 합니다. 두 번째 `@` C# 파서를 이스케이프 합니다.

### <a name="reserved-keywords-not-used-by-razor"></a>Razor에서 사용 하지 않는 예약 된 키워드

* 네임스페이스(namespace)
* 클래스

## <a name="viewing-the-razor-c-class-generated-for-a-view"></a>보기에 대해 생성 된 Razor C# 클래스 보기

ASP.NET Core MVC 프로젝트에 다음 클래스를 추가 합니다.

[!code-csharp[Main](razor/sample/Utilities/CustomTemplateEngine.cs)]

재정의 `RazorTemplateEngine` 와 MVC에서 추가 `CustomTemplateEngine` 클래스:

[!code-csharp[Main](razor/sample/Startup.cs?highlight=4&range=10-14)]

중단점을 설정한는 `return csharpDocument` 의 문은 `CustomTemplateEngine`합니다. 프로그램 실행이 중단점에서 중지 되는 경우의 값을 보려면 `generatedCode`합니다.

![GeneratedCode의 텍스트 시각화 도우미 보기](razor/_static/tvr.png)

## <a name="view-lookups-and-case-sensitivity"></a>보기 조회 및 대/소문자 구분

Razor 뷰 엔진 뷰에 대 한 대/소문자 구분 조회를 수행 합니다. 그러나 실제 조회는 기본 파일 시스템에 의해 결정 됩니다.

* 소스를 기반으로 하는 파일: 
  * 대/소문자 구분 파일 시스템 (예: Windows), 운영 체제에서 실제 파일 공급자 조회는 대/소문자 구분 합니다. 예를 들어 `return View("Test")` 일치 하는 항목으로 인해 */Views/Home/Test.cshtml*, */Views/home/test.cshtml*, 및 기타 대/소문자 구분 변형 합니다.
  * 대/소문자 구분 파일 시스템에 (예: Linux, OSX와 `EmbeddedFileProvider`), 조회는 대/소문자 구분 합니다. 예를 들어 `return View("Test")` 구체적으로 일치 하는 항목 */Views/Home/Test.cshtml*합니다.
* 뷰 미리 컴파일된: ASP.Net 2.0 이상 코어, 미리 컴파일된 뷰를 조회는 대/소문자 구분 모든 운영 체제에 있습니다. 동작은 Windows에서 물리적 파일 공급자의 동작과 동일 합니다. 미리 컴파일된 뷰를 두 가지 경우에만 다른 경우 조회 명확 하지 않습니다.

개발자는 영역, 컨트롤러 및 작업 이름의 대/소문자를 파일 및 디렉터리 이름의 대/소문자와 일치 하는 것이 좋습니다. 이렇게 하면 배포에 기본 파일 시스템에 관계 없이 해당 뷰를 찾을 수 있습니다.
