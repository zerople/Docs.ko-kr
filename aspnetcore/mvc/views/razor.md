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
ms.openlocfilehash: 0e65f0e9f672f9f93256ebc039ea0db2e4ef5ae0
ms.sourcegitcommit: 6e83c55eb0450a3073ef2b95fa5f5bcb20dbbf89
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/28/2017
---
# <a name="razor-syntax-for-aspnet-core"></a>ASP.NET Core에 대 한 razor 구문

여 [Taylor Mullen](https://twitter.com/ntaylormullen) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)

## <a name="what-is-razor"></a>Razor 란?

Razor은 웹 페이지에 서버 기반 코드를 포함 하기 위한 태그 구문입니다. C# 및 HTML Razor 태그는 Razor 구문을 구성 됩니다. Razor를 일반적으로 들어 있는 파일을 *.cshtml* 파일 확장명입니다.

## <a name="rendering-html"></a>HTML 렌더링

기본 Razor 언어 HTML입니다. Razor의 HTML 렌더링는 HTML 파일에 차이가 없습니다. 다음 태그로 Razor 파일:

```html
<p>Hello World</p>
```

가 변경 하지로 렌더링 `<p>Hello World</p>` 서버입니다.

## <a name="razor-syntax"></a>Razor 구문

Razor C# 지원 및 사용 하 여는 `@` C#으로 HTML에서 전환 기호입니다. Razor C# 식을 계산 하 고 HTML 출력에이 렌더링 합니다. Razor는 HTML에서 C# 또는 Razor 관련 태그로 전환될 수 있습니다. 경우는 `@` 기호 뒤는 [Razor 예약 키워드](#razor-reserved-keywords) Razor 특정 태그에 전환, 그렇지 않으면 일반 C# 식으로 전환 합니다.

<a name=escape-at-label></a>

포함 된 HTML `@` 기호는 두 번째 이스케이프 되어야 할 수 있습니다 `@` 기호입니다. 예:

```cshtml
<p>@@Username</p>
```

다음 HTML을 렌더링 합니다.

```cshtml
<p>@Username</p>
```

<a name=razor-email-ref></a>

HTML 특성 및 전자 메일 주소를 포함 하는 내용을 처리 하지 않습니다는 `@` 전환 문자로 기호입니다.

   `<a href="mailto:Support@contoso.com">Support@contoso.com</a>`

## <a name="implicit-razor-expressions"></a>암시적 Razor 식

암시적 Razor 식은로 시작 `@` C# 코드에 나옵니다. 예:

```html
<p>@DateTime.Now</p>
<p>@DateTime.IsLeapYear(2016)</p>
```

C#을 제외한 `await` 키워드 암시적 식 공백을 포함 해서는 안 됩니다. 예를 들어 C# 문에 명확한 끝으로 공간 intermingle 수 있습니다.

```html
<p>@await DoSomething("hello", "world")</p>
```

## <a name="explicit-razor-expressions"></a>명시적 Razor 식

명시적 Razor 식은 이루어져는 @ 균형 잡힌 괄호 기호입니다. 예를 들어, 렌더링 시간을 지난 주:

```html
<p>Last week this time: @(DateTime.Now - TimeSpan.FromDays(7))</p>
```

내에서 모든 콘텐츠는 @ () 괄호 평가 되 고 출력에 렌더링 합니다.

일반적으로 암시적 식 공백을 포함할 수 없습니다. 예를 들어 아래 코드에서 1 주일은 현재 시간에서 차감 되지 않습니다.

[!code-html[Main](razor/sample/Views/Home/Contact.cshtml?range=20)]

다음과 같은 HTML을 렌더링 됩니다.

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

명시적 식 없이 `<p>Age@joe.Age</p>` 전자 메일 주소로 간주 됩니다 및 `<p>Age@joe.Age</p>` 는 렌더링 됩니다. 명시적 식을으로 쓸 때 `<p>Age33</p>` 렌더링 됩니다.

<a name=expression-encoding-label></a>

## <a name="expression-encoding"></a>식 인코딩

C# 식을 문자열로 평가 하는 인코딩된 HTML입니다. C# 식을 계산 하는 `IHtmlContent` 통해 직접 렌더링 되는 *IHtmlContent.WriteTo*합니다. C# 식으로 계산 하지는 *IHtmlContent* 문자열로 변환 됩니다 (여 *ToString*) 되 고 렌더링 하기 전에 인코딩된 합니다. 예를 들어, 다음 Razor 태그:

```cshtml
@("<span>Hello World</span>")
```

이 HTML을 렌더링합니다.

```html
&lt;span&gt;Hello World&lt;/span&gt;
```

브라우저에서 렌더링는:

`<span>Hello World</span>`

`HtmlHelper``Raw` 출력은 인코딩된 아니라 HTML 태그로 렌더링 합니다.

>[!WARNING]
> 사용 하 여 `HtmlHelper.Raw` unsanitized 사용자 입력은 보안상 위험 합니다. 사용자 입력에는 악의적인 JavaScript 또는 다른 악용 기법 포함 될 수 있습니다. 사용자 입력 처리에 사용 되는 어려운 문제, 사용 하지 않도록 `HtmlHelper.Raw` 사용자 입력에 있습니다.

다음 Razor 태그:

```cshtml
@Html.Raw("<span>Hello World</span>")
```

이 HTML을 렌더링합니다.

```html
<span>Hello World</span>
```

<a name=razor-code-blocks-label></a>

## <a name="razor-code-blocks"></a>Razor 코드 블록

Razor 코드 블록 시작 `@` 묶여 및 `{}`합니다. 식에서와 달리 C# 코드 코드 블록 안에 렌더링 되지 않습니다. 동일한 범위를 공유 하 고 순서에 정의 된 코드 블록 및 Razor 페이지의 식 (즉, 코드 블록의 선언 수는 나머지 코드 블록 및 식에 대 한 범위에).

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

코드 블록에 기본 언어는 C#, 하지만 HTML로 다시 전환할 수 있습니다. 코드 블록 내에서 HTML HTML 렌더링으로 다시 전환 됩니다.

```cshtml
@{
    var inCSharp = true;
    <p>Now in HTML, was in C# @inCSharp</p>
}
```

<a name=explicit-delimited-transition-label></a>

### <a name="explicit-delimited-transition"></a>명시적 구분 기호로 분리 된 전환

HTML을 렌더링 해야 하는 코드 블록의 하위 섹션을 정의 하려면 Razor와 함께 렌더링 사이의 문자를 둘러싸고 `<text>` 태그:

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    <text>Name: @person.Name</text>
}
```

일반적으로 HTML 태그 둘러싸인 HTML을 렌더링 하려는 경우이 방법의 사용 합니다. HTML 또는 Razor 태그가 없는 Razor 런타임 오류가 발생할 수 있습니다.

<a name=explicit-line-transition-with-label></a>

### <a name="explicit-line-transition-with-"></a>전환을 명시적 줄`@:`

코드 블록 안에 줄의 나머지 부분을 HTML로 렌더링, 사용 된 `@:` 구문:

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    @:Name: @person.Name
}
```

없이 `@:` 위 코드에서 런타임 오류 Razor 얻을 수 있습니다.

<a name=control-structures-razor-label></a>

## <a name="control-structures"></a>제어 구조

제어 구조는 코드 블록의 확장입니다. 코드 블록 (인라인 C# 태그로 전환)도의 모든 측면은 다음 구조에 적용 됩니다.

### <a name="conditionals-if-else-if-else-and-switch"></a>조건부 `@if`, `else if`, `else` 및`@switch`

`@if` 제품군 제어 코드 실행 하는 경우:

```cshtml
@if (value % 2 == 0)
{
    <p>The value was even</p>
}
```

`else`및 `else if` 필요 하지 않습니다는 `@` 기호:

```cshtml
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
        <p>Your number was not 1 or 1337.</p>
        break;
}
```

### <a name="looping-for-foreach-while-and-do-while"></a>반복 `@for`, `@foreach`, `@while`, 및`@do while`

템플릿 기반 HTML 제어 문을 반복을 렌더링할 수 있습니다. 예를 들어, 사람 목록이 렌더링 하려면:

```cshtml
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

### <a name="compound-using"></a>복합`@using`

C#의 using 문을 사용 하는 개체가 삭제 되도록 합니다. Razor의 동일한이 메커니즘 수 만드는 데 사용할 추가 콘텐츠를 포함 하는 HTML 도우미입니다. 예를 들어, HTML 도우미와 폼 태그를 렌더링 하 이용 하면는 `@using` 문:

```cshtml
@using (Html.BeginForm())
{
    <div>
        email:
        <input type="email" id="Email" name="Email" value="" />
        <button type="submit"> Register </button>
    </div>
}
```

위의와 같은 범위 수준 작업을 수행할 수 있습니다 [태그 도우미](tag-helpers/index.md)합니다.

### <a name="try-catch-finally"></a>`@try`, `catch`, `finally`

예외 처리는 C# 유사 합니다.

[!code-html[Main](razor/sample/Views/Home/Contact7.cshtml)]

### `@lock`

Razor에 임계 섹션 lock 문을 보호 하는 기능이 있습니다.

```cshtml
@lock (SomeLock)
{
    // Do critical section work
}
```

### <a name="comments"></a>설명

Razor 주석 C# 및 HTML을 지원합니다. 다음 태그:

```cshtml
@{
    /* C# comment. */
    // Another C# comment.
}
<!-- HTML comment -->
```

서버도 렌더링 됩니다.

```cshtml
<!-- HTML comment -->
```

Razor 주석 페이지를 렌더링 하기 전에 서버에서 제거 됩니다. Razor를 사용 하 여 `@*  *@` 를 주석을 구분 합니다. 다음 코드 주석 처리 되어, 있으므로 서버는 모든 태그를 렌더링 하지 않습니다.

```cshtml
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

Razor 지시문 다음 예약 된 키워드를 사용 하 여 암시적 식으로 표시 됩니다는 `@` 기호입니다. 지시문을 일반적으로 페이지를 구문 분석 되는 방식을 변경 되거나 Razor 페이지 내에서 서로 다른 기능을 사용 하도록 설정 됩니다.

Razor 뷰의 코드를 생성 하는 방법을 이해는 간편한 방법이 지시문의 작동 방식을 이해 하려면 있습니다. Razor 페이지는 C# 파일을 생성 하는 데 사용 됩니다. 이 Razor 페이지:

[!code-html[Main](razor/sample/Views/Home/Contact8.cshtml)]

다음과 유사한 클래스를 생성 합니다.

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

[보기에 대해 생성 된 Razor C# 클래스 보기](#razor-customcompilationservice-label) 이렇게 생성 된 클래스를 확인 하는 방법에 설명 합니다.

### `@using`

`@using` 지시문을 추가 합니다 c# `using` 생성 된 razor 페이지 지시문:

[!code-html[Main](razor/sample/Views/Home/Contact9.cshtml)]

### `@model`

`@model` 지시문 Razor 페이지에 전달 하는 모델의 유형을 지정 합니다. 이 도구는 다음 구문을 사용합니다.

```cshtml
@model TypeNameOfModel
```

예를 들어, 개별 사용자 계정으로 ASP.NET Core MVC 응용 프로그램을 만들면는 *Views/Account/Login.cshtml* Razor 뷰 모델 선언이 있습니다.

```cshtml
@model LoginViewModel
```

앞의 클래스 예제에서 생성 된 클래스에서 상속 `RazorPage<dynamic>`합니다. 추가 하 여는 `@model` 상속 된 기능을 제어 합니다. 예

```cshtml
@model LoginViewModel
```

다음 클래스를 생성합니다.

```csharp
public class _Views_Account_Login_cshtml : RazorPage<LoginViewModel>
```

Razor 페이지에 노출 한 `Model` 모델 액세스에 대 한 속성 페이지에 전달 합니다.

```cshtml
<div>The Login Email: @Model.Email</div>
```

`@model` 지시문이이 속성의 형식을 지정 된 (지정 하 여는 `T` 에 `RazorPage<T>` 페이지에 대 한 생성 된 클래스에서 파생 된). 지정 하지 않으면는 `@model` 지시문은 `Model` 속성 유형 중 하나가 됩니다 `dynamic`합니다. 모델의 값은 컨트롤러에서 뷰에 전달 됩니다. 참조 [강력한 형식 모델 및 @model 키워드](../../tutorials/first-mvc-app/adding-model.md#strongly-typed-models-keyword-label) 자세한 정보에 대 한 합니다.

### `@inherits`

`@inherits` 지시문 Razor 페이지 상속 된 클래스의 모든 권한을 제공 합니다.

```cshtml
@inherits TypeNameOfClassToInheritFrom
```

예를 들어, 다음과 같은 사용자 지정 Razor 페이지 유형 몇 가정해 보겠습니다.

[!code-csharp[Main](razor/sample/Classes/CustomRazorPage.cs)]

다음 Razor 생성 `<div>Custom text: Hello World</div>`합니다.

[!code-html[Main](razor/sample/Views/Home/Contact10.cshtml)]

사용할 수 없는 `@model` 및 `@inherits` 동일한 페이지에 있습니다. 사용할 수 있습니다 `@inherits` 에 *_ViewImports.cshtml* Razor 페이지 파일입니다. 예를 들어, Razor 뷰 가져온 다음 *_ViewImports.cshtml* 파일:

[!code-html[Main](razor/sample/Views/_ViewImportsModel.cshtml)]

강력한 형식의 Razor 페이지

[!code-html[Main](razor/sample/Views/Home/Login1.cshtml)]

이 HTML 태그를 생성 합니다.

```cshtml
<div>The Login Email: Rick@contoso.com</div>
<div>Custom text: Hello World</div>
```

전달 될 때 "[Rick@contoso.com](mailto:Rick@contoso.com)" 모델에서:

   자세한 내용은 [레이아웃](layout.md)을 참조하세요.

### `@inject`

`@inject` 지시문을 사용 하면에서 서비스를 삽입할 수 있습니다 프로그램 [서비스 컨테이너](../../fundamentals/dependency-injection.md) 사용 하기 위해 Razor 페이지에 있습니다. 참조 [뷰로 종속성 주입](dependency-injection.md)합니다.

<a name="functions"></a>

### `@functions`

`@functions` 지시문 Razor 페이지를 함수 수준 콘텐츠를 추가할 수 있습니다. 사용되는 구문은 다음과 같습니다.

```cshtml
@functions { // C# Code }
```

예:

[!code-html[Main](razor/sample/Views/Home/Contact6.cshtml)]

다음 HTML 태그를 생성 합니다.

```cshtml
<div>From method: Hello</div>
```

생성 된 Razor C#는 다음과 같습니다.

[!code-csharp[Main](razor/sample/Classes/Views_Home_Test_cshtml.cs?range=1-19)]

### `@section`

`@section` 지시어와 함께 사용 되는 [레이아웃 페이지](layout.md) 렌더링된 된 HTML 페이지의 서로 다른 부분에 콘텐츠를 렌더링할 뷰를 사용할 수 있도록 합니다. 참조 [섹션](layout.md#layout-sections-label) 자세한 정보에 대 한 합니다.

## <a name="tag-helpers"></a>태그 도우미

다음 [태그 도우미](tag-helpers/index.md) 지시문 제공 된 링크에서 자세히 설명 합니다.

* [@addTagHelper](tag-helpers/intro.md#add-helper-label)
* [@removeTagHelper](tag-helpers/intro.md#remove-razor-directives-label)
* [@tagHelperPrefix](tag-helpers/intro.md#prefix-razor-directives-label)

<a name=razor-reserved-keywords-label></a>

## <a name="razor-reserved-keywords"></a>Razor 예약 키워드

### <a name="razor-keywords"></a>Razor 키워드

* 페이지 (ASP.NET Core 2.0 이상 필요)
* 함수
* 상속
* 모델
* section
* 도우미 (지원 되지 않음 ASP.NET Core 여.)

Razor 키워드와 이스케이프 될 수 있습니다 `@(Razor Keyword)`, 예를 들어 `@(functions)`합니다. 아래 전체 샘플을 참조 하십시오.

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

C# Razor 키워드 해야 이중 이스케이프 `@(@C# Razor Keyword)`, 예를 들어 `@(@case)`합니다. 첫 번째 `@` Razor 파서를 이스케이프 합니다. 두 번째 `@` C# 파서를 이스케이프 합니다. 아래 전체 샘플을 참조 하십시오.

### <a name="reserved-keywords-not-used-by-razor"></a>Razor에서 사용 하지 않는 예약 된 키워드

* 네임스페이스(namespace)
* 클래스

<a name=razor-customcompilationservice-label></a>

## <a name="viewing-the-razor-c-class-generated-for-a-view"></a>보기에 대해 생성 된 Razor C# 클래스 보기

ASP.NET Core MVC 프로젝트에 다음 클래스를 추가 합니다.

[!code-csharp[Main](razor/sample/Services/CustomCompilationService.cs)]

재정의 `ICompilationService` 위의 클래스;와 MVC에서 추가

[!code-csharp[Main](razor/sample/Startup.cs?highlight=4&range=29-33)]

중단점을 설정는 `Compile` 방식의 `CustomCompilationService` 뷰와 `compilationContent`합니다.

![CompilationContent의 텍스트 시각화 도우미 보기](razor/_static/tvr.png)

<a name="case"></a>
## <a name="view-lookups-and-case-sensitivity"></a>보기 조회 및 대/소문자 구분

Razor 뷰 엔진 뷰에 대 한 대/소문자 구분 조회를 수행 합니다. 그러나 실제 조회 기본 원본에 의해 결정 됩니다.

* 소스를 기반으로 하는 파일: 

    * 대/소문자 구분 파일 시스템 (예: Windows), 운영 체제에서 실제 파일 공급자 조회는 대/소문자 구분 합니다. 예를 들어 `return View("Test")` 초래 `/Views/Home/Test.cshtml`, `/Views/home/test.cshtml` 다른 대/소문자 변형이 모두 발견 되 고 있습니다.
    * 대/소문자 구분 파일 시스템에 Linux OSX을 포함 하 고 `EmbeddedFileProvider`, 조회는 대/소문자 구분 합니다. 예를 들어 `return View("Test")` 구체적으로 찾습니다 `/Views/Home/Test.cshtml`합니다.
        
* 미리 컴파일된 뷰:

   * ASP.Net Core 2.0 이상에서는 대/소문자 구분 모든 운영 체제에는 미리 컴파일된 뷰를 찾는 합니다. 동작은 Windows에서 물리적 파일 공급자의 동작과 동일 합니다. 
   참고: 미리 컴파일된 뷰를 두 가지 경우에만 다른 경우 조회 명확 하지 않습니다.

개발자는 영역, 컨트롤러 및 작업 이름의 대/소문자를 파일 및 디렉터리 이름의 대/소문자와 일치 하는 것이 좋습니다. 이렇게 하면 배포에 기본 파일 시스템의 알 수 없는 상태로 유지 합니다.
