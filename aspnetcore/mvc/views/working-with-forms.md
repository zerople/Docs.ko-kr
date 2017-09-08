---
title: "폼에 ASP.NET Core 태그 도우미"
author: rick-anderson
description: "태그 도우미 폼과 함께 사용 하는 기본 제공을 설명 합니다."
keywords: "ASP.NET Core, 태그 도우미 TagHelper를 HTML 양식 Forms"
ms.author: riande
manager: wpickett
ms.date: 02/14/2017
ms.topic: article
ms.assetid: 25595059-4fac-4785-8152-f88590e3169b
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/views/working-with-forms
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: bd69e008a81abc4f6785d93b89823c03e1a7df83
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/11/2017
---
# <a name="introduction-to-using-tag-helpers-in-forms-in-aspnet-core"></a>태그 도우미를 사용 하 여 폼에 ASP.NET Core 소개

여 [Rick Anderson](https://twitter.com/RickAndMSFT), [Dave Paquette](https://twitter.com/Dave_Paquette), 및 [Jerrie Pelser](https://github.com/jerriep)

이 문서에 폼 및 폼에 일반적으로 사용 되는 HTML 요소 작업을 보여 줍니다. HTML [양식](https://www.w3.org/TR/html401/interact/forms.html) 요소는 서버에 데이터를 다시 게시 하는 기본 메커니즘 웹 앱 사용을 제공 합니다. 이 문서의 대부분 설명 [태그 도우미](tag-helpers/intro.md) 있고 어떻게 수 생산적으로 강력한 HTML 폼을 만들 수 있습니다. 참조 [태그 도우미 소개](tag-helpers/intro.md) 이 문서를 읽기 전에 합니다.

대부분의 경우에서 HTML 도우미 특정 태그 도우미에는 다른 방법은 제공 하지만 태그 도우미는 HTML 도우미를 대체 하지 않고 각 HTML 도우미에 대 한 태그 도우미 없기 인식 하는 것이 중요 합니다. 존재 하는 HTML 도우미 대신 경우 다룹니다.

<a name=my-asp-route-param-ref-label></a>

## <a name="the-form-tag-helper"></a>Form 태그 도우미

[양식](https://www.w3.org/TR/html401/interact/forms.html) 도우미 태그:

* HTML을 생성 [ \<양식 >](https://www.w3.org/TR/html401/interact/forms.html) `action` MVC 컨트롤러 동작 또는 명명 된 경로 대 한 특성 값

* 에서는 오류가 발생 하는 숨겨진 [요청 확인 토큰](http://www.asp.net/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages) 교차 사이트 요청 위조를 방지 하기 위해 (함께 사용할 경우의 `[ValidateAntiForgeryToken]` HTTP Post 작업 메서드에 특성)

* 제공 된 `asp-route-<Parameter Name>` 특성, 여기서 `<Parameter Name>` 경로 값에 추가 됩니다. `routeValues` 매개 변수를 `Html.BeginForm` 및 `Html.BeginRouteForm` 유사한 기능을 제공 합니다.

* 에 HTML 도우미 대신 `Html.BeginForm` 및`Html.BeginRouteForm`

예제:

[!code-HTML[Main](working-with-forms/sample/final/Views/Demo/RegisterFormOnly.cshtml)]

위의 Form 태그 도우미에서는 다음 HTML을 생성합니다.

```HTML
<form method="post" action="/Demo/Register">
     <!-- Input and Submit elements -->
     <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>" />
    </form>
   ```

MVC 런타임에서 생성 된 `action` Form 태그 도우미 특성에서 특성 값 `asp-controller` 및 `asp-action`합니다. Form 태그 도우미도 숨겨진 생성 [요청 확인 토큰](http://www.asp.net/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages) 교차 사이트 요청 위조를 방지 하기 위해 (함께 사용할 경우의 `[ValidateAntiForgeryToken]` HTTP Post 작업 메서드에 특성). 순수 HTML 폼 교차 사이트 요청 위조 로부터 보호 하는 것은 어려운, Form 태그 도우미를이 서비스를 제공 합니다.

### <a name="using-a-named-route"></a>명명된 된 경로 사용 하 여

`asp-route` 태그 도우미 특성에 대 한 HTML 태그를 생성할 수도 `action` 특성입니다. 사용 하 여 앱은 [경로](../../fundamentals/routing.md) 라는 `register` 등록 페이지에 다음 태그를 사용할 수 있습니다.

[!code-HTML[Main](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterRoute.cshtml)]

보기 중 많은 *뷰/계정* 폴더 (새 웹 앱을 만들 때 생성 *개별 사용자 계정*) 포함 된 [asp-경로-returnurl](http://docs.asp.net/en/latest/mvc/views/working-with-forms.html#the-form-tag-helper) 특성:

<!-- literal_block {"ids": [], "linenos": false, "xml:space": "preserve", "language": "none", "highlight_args": {"hl_lines": [2]}} -->

```none
<form asp-controller="Account" asp-action="Login"
     asp-route-returnurl="@ViewData["ReturnUrl"]"
     method="post" class="form-horizontal" role="form">
   ```

>[!NOTE]
>기본 제공된 템플릿과 함께 `returnUrl` 만 자동으로 채워집니다 권한 있는 리소스에 액세스 하려고 하지만 되지 인증 하거나 때 권한을 부여 합니다. 무단된 액세스를 시도 하면 보안 미들웨어 리디렉션됩니다 사용 하 여 로그인 페이지에는 `returnUrl` 설정 합니다.

## <a name="the-input-tag-helper"></a>입력된 태그 도우미

입력 태그 도우미 바인딩하는 HTML [ \<입력 >](https://www.w3.org/wiki/HTML/Elements/input) 요소 razor 뷰의 모델 식입니다.

구문:

```HTML
<input asp-for="<Expression Name>" />
   ```

입력된 태그 도우미:

* 생성 된 `id` 및 `name` HTML 특성에 지정 된 식 이름에 대 한는 `asp-for` 특성입니다. `asp-for="Property1.Property2"`는 `m => m.Property1.Property2`와 같습니다. 식의 이름에 사용 되는 것은 `asp-for` 특성 값입니다. 참조는 [식 이름은](#expression-names) 추가 정보에 대 한 섹션.

* HTML 설정 `type` 특성 값에는 모델 유형에 따라 및 [데이터 주석을](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.aspx) 모델 속성에 적용 된 특성

* HTML을 덮어쓰지 것입니다 `type` 특성 값이 지정 된 경우

* 생성 [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5) 에서 유효성 검사 특성 [데이터 주석을](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.aspx) 모델 속성에 적용 되는 특성

* 에 HTML 도우미 기능이와 겹칠 `Html.TextBoxFor` 및 `Html.EditorFor`합니다. 참조는 **입력 태그 도우미에 대 한 HTML 도우미 대안** 자세한 내용은 섹션.

* 강력한 형식 지정을 제공합니다. 속성 변경의 이름과 태그 도우미를 업데이트 하지에 오류를 다음과 같이 얻게 됩니다.

```HTML
An error occurred during the compilation of a resource required to process
this request. Please review the following specific error details and modify
your source code appropriately.

Type expected
 'RegisterViewModel' does not contain a definition for 'Email' and no
 extension method 'Email' accepting a first argument of type 'RegisterViewModel'
 could be found (are you missing a using directive or an assembly reference?)
```

`Input` 설정 하는 HTML 태그 도우미 `type` 특성의.NET 유형을 기반으로 합니다. 다음 표에서 몇 가지 일반적인.NET 형식과 생성 되는 HTML 형식 (일부.NET 유형 설명 되어 있음)를 나열 합니다.

|.NET 형식|입력된 형식|
|---|---|
|Bool|형식이 "checkbox" =|
|문자열|형식 = "text"|
|DateTime|형식 = "datetime"|
|Byte|형식 = "number"|
|Int|형식 = "number"|
|Single, Double|형식 = "number"|


다음 표에서 몇 가지 일반적인 [데이터 주석](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.aspx) 입력된 태그 도우미 (모든 유효성 검사 특성 설명 되어 있음)는 특정 입력된 형식에 매핑되는 특성:


|특성|입력된 형식|
|---|---|
|[EmailAddress]|형식 = "email"|
|[Url]|형식 = "url"|
|[HiddenInput]|형식 = "hidden"|
|[Phone]|형식 = "전화"|
|[DataType(DataType.Password)]| 형식 = "password"|
|[DataType(DataType.Date)]| 형식 = "date"|
|[DataType(DataType.Time)]| 형식 = "시간"|


예제:

[!code-csharp[Main](working-with-forms/sample/final/ViewModels/RegisterViewModel.cs)]

[!code-HTML[Main](working-with-forms/sample/final/Views/Demo/RegisterInput.cshtml)]

위의 코드에서는 다음 HTML을 생성합니다.

```HTML
  <form method="post" action="/Demo/RegisterInput">
       Email:
       <input type="email" data-val="true"
              data-val-email="The Email Address field is not a valid e-mail address."
              data-val-required="The Email Address field is required."
              id="Email" name="Email" value="" /> <br>
       Password:
       <input type="password" data-val="true"
              data-val-required="The Password field is required."
              id="Password" name="Password" /><br>
       <button type="submit">Register</button>
     <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>" />
   </form>
   ```

에 적용할 데이터 주석은 `Email` 및 `Password` 속성은 모델에서 메타 데이터를 생성 합니다. 모델 메타 데이터를 사용 하 고 생성 하는 입력 태그 도우미 [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5) `data-val-*` 특성 (참조 [모델 유효성 검사](../models/validation.md)). 이러한 특성에는 입력된 필드를 연결할 유효성 검사기에 설명 합니다. 이렇게 하면 비 가시적인 HTML5 및 [jQuery](https://jquery.com/) 유효성 검사 합니다. 비 가시적인 특성 형식에는 `data-val-rule="Error Message"`, 여기서 규칙은 유효성 검사 규칙의 이름 (예: `data-val-required`, `data-val-email`, `data-val-maxlength`등.) 오류 메시지가 특성에서 제공 하는 경우에 대 한 값으로 표시 됩니다는 `data-val-rule` 특성입니다. 폼의 특성은 또한 `data-val-ruleName-argumentName="argumentValue"` 예를 들어,는 규칙에 대 한 추가 세부 정보를 제공 하는 `data-val-maxlength-max="1024"` 합니다.

### <a name="html-helper-alternatives-to-input-tag-helper"></a>입력 태그 도우미에 대 한 HTML 도우미 대안

`Html.TextBox``Html.TextBoxFor`, `Html.Editor` 및 `Html.EditorFor` 겹치는 입력 태그 도우미와 기능입니다. 입력 태그 도우미에서 자동으로 설정 된 `type` ; 특성 `Html.TextBox` 및 `Html.TextBoxFor` 되지 것입니다. `Html.Editor`및 `Html.EditorFor` 컬렉션, 복잡 한 개체 및 템플릿; 처리 된 입력 태그 도우미는 그렇지 않습니다. 입력 태그 도우미 `Html.EditorFor` 및 `Html.TextBoxFor` 은 강력한 형식 (람다 식 사용). `Html.TextBox` 및 `Html.Editor` 없습니다 (식 이름 사용).

### <a name="htmlattributes"></a>HtmlAttributes

`@Html.Editor()`및 `@Html.EditorFor()` 특수 `ViewDataDictionary` 라는 항목 `htmlAttributes` 자신의 기본 서식 파일을 실행 합니다. 이 문제는 사용 하 여 확장 필요에 따라 `additionalViewData` 매개 변수입니다. "HtmlAttributes" 키는 대 소문자를 구분 합니다. 키 "htmlAttributes" 유사 하 게 처리 되는 `htmlAttributes` 같은 도우미 입력에 전달 된 개체 `@Html.TextBox()`합니다.

```HTML
@Html.EditorFor(model => model.YourProperty, 
  new { htmlAttributes = new { @class="myCssClass", style="Width:100px" } })
```

### <a name="expression-names"></a>식 이름

`asp-for` 특성 값은 한 `ModelExpression` 및 람다 식의 오른쪽입니다. 따라서 `asp-for="Property1"` 됩니다 `m => m.Property1` 생성된 된 코드 의미 하기 때문에에서 접두사로 사용 하 여 필요가 없습니다 `Model`합니다. 사용할 수는 "@" 문자는 인라인 식을 시작 하 고 앞으로 이동 하는 `m.`:

```HTML
@{
       var joe = "Joe";
   }
   <input asp-for="@joe" />
```

다음을 생성합니다.

```HTML
<input type="text" id="joe" name="joe" value="Joe" />
```

컬렉션 속성을 가진 `asp-for="CollectionProperty[23].Member"` 동일한 이름으로 생성 `asp-for="CollectionProperty[i].Member"` 때 `i` 값 `23`합니다.

### <a name="navigating-child-properties"></a>자식 속성을 탐색

뷰 모델의 속성 경로 사용 하 여 자식 속성을 탐색할 수 있습니다. 자식을 포함 하는 보다 복잡 한 모델 클래스를 고려 `Address` 속성입니다.

[!code-csharp[Main](../../mvc/views/working-with-forms/sample/final/ViewModels/AddressViewModel.cs?highlight=1,2,3,4&range=5-8)]

[!code-csharp[Main](../../mvc/views/working-with-forms/sample/final/ViewModels/RegisterAddressViewModel.cs?highlight=8&range=5-13)]

보기에서에 바인딩한 `Address.AddressLine1`:

[!code-HTML[Main](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterAddress.cshtml?highlight=6)]

다음 HTML이 생성 `Address.AddressLine1`:

```HTML
<input type="text" id="Address_AddressLine1" name="Address.AddressLine1" value="" />
   ```

### <a name="expression-names-and-collections"></a>식 이름 및 컬렉션

샘플의 배열을 포함 하는 모델 `Colors`:

[!code-csharp[Main](../../mvc/views/working-with-forms/sample/final/ViewModels/Person.cs?highlight=3&range=5-10)]

작업 방법:

```csharp
public IActionResult Edit(int id, int colorIndex)
   {
       ViewData["Index"] = colorIndex;
       return View(GetPerson(id));
   }
   ```

다음 Razor 특정 액세스 하는 방법을 보여 줍니다. `Color` 요소:

[!code-HTML[Main](working-with-forms/sample/final/Views/Demo/EditColor.cshtml)]

*Views/Shared/EditorTemplates/String.cshtml* 템플릿:

[!code-HTML[Main](working-with-forms/sample/final/Views/Shared/EditorTemplates/String.cshtml)]

사용 하 여 샘플 `List<T>`:

[!code-csharp[Main](working-with-forms/sample/final/ViewModels/ToDoItem.cs?range=3-8)]

다음 Razor 컬렉션을 반복 하는 방법을 보여 줍니다.

[!code-HTML[Main](working-with-forms/sample/final/Views/Demo/Edit.cshtml)]

*Views/Shared/EditorTemplates/ToDoItem.cshtml* 템플릿:

[!code-HTML[Main](working-with-forms/sample/final/Views/Shared/EditorTemplates/ToDoItem.cshtml)]


>[!NOTE]
>항상 사용 `for` (및 *하지* `foreach`) 목록에 대 한 반복 합니다. 평가 인덱서는 LINQ의 식은 비용이 많이 들 수를 최소화 해야 합니다.

&nbsp;

>[!NOTE]
>위의 주석 처리 된 예제 코드는 람다 식을 바꿀 방법을 보여 줍니다.는 `@` 연산자에 액세스할 각 `ToDoItem` 목록에 있습니다.

## <a name="the-textarea-tag-helper"></a>Textarea 태그 도우미

`Textarea Tag Helper` 태그 도우미 입력 태그 도우미를 것과 비슷합니다.

* 생성 된 `id` 및 `name` 특성 및에 대 한 모델에서 데이터 유효성 검사 특성은 [ \<textarea >](http://www.w3.org/wiki/HTML/Elements/textarea) 요소입니다.

* 강력한 형식 지정을 제공합니다.

* HTML 도우미 대체:`Html.TextAreaFor`

예제:

[!code-csharp[Main](working-with-forms/sample/final/ViewModels/DescriptionViewModel.cs)]

[!code-HTML[Main](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterTextArea.cshtml?highlight=4)]

다음 HTML 생성 됩니다.

<!-- literal_block {"ids": [], "linenos": false, "xml:space": "preserve", "language": "HTML", "highlight_args": {"hl_lines": [2, 3, 4, 5, 6, 7, 8]}} -->

```HTML
<form method="post" action="/Demo/RegisterTextArea">
  <textarea data-val="true"
   data-val-maxlength="The field Description must be a string or array type with a maximum length of &#x27;1024&#x27;."
   data-val-maxlength-max="1024"
   data-val-minlength="The field Description must be a string or array type with a minimum length of &#x27;5&#x27;."
   data-val-minlength-min="5"
   id="Description" name="Description">
  </textarea>
  <button type="submit">Test</button>
  <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>" />
</form>
```

## <a name="the-label-tag-helper"></a>레이블 태그 도우미

* 레이블 캡션 생성 및 `for` 특성에 [ <label> ](https://www.w3.org/wiki/HTML/Elements/label) 식 이름에 대 한 요소

* HTML 도우미 대체: `Html.LabelFor`합니다.

`Label Tag Helper` 순수 HTML label 요소를 통해 다음과 같은 이점을 제공 합니다.

* 설명 레이블을 값을 자동으로 가져오기는 `Display` 특성입니다. 의도 한 표시 이름을 조합해 서 고 시간에 따라 변경 될 수 있습니다 `Display` 특성 및 레이블 태그 도우미 적용 됩니다는 `Display` 모든 곳에서 사용 됩니다.

* 소스 코드에서 덜 태그

* 강력한 모델 속성을 입력 합니다.

예제:

[!code-csharp[Main](working-with-forms/sample/final/ViewModels/SimpleViewModel.cs)]

[!code-HTML[Main](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterLabel.cshtml?highlight=4)]

다음 HTML에 대해 생성 되는 `<label>` 요소:

```HTML
<label for="Email">Email Address</label>
   ```

생성 된 레이블 태그 도우미의 `for` 와 연결 된 id는 "Email"의 특성 값의 `<input>` 요소입니다. 태그 도우미 생성 일관 된 `id` 및 `for` 요소 제대로 연결 될 수 있도록 합니다. 이 샘플의 캡션을에서 제공 되는 `Display` 특성입니다. 모델에 포함 하지 않은 경우는 `Display` 특성 캡션 식의 속성 이름 것입니다.

## <a name="the-validation-tag-helpers"></a>유효성 검사 태그 도우미

유효성 검사 태그 도우미 두 가지가 있습니다. `Validation Message Tag Helper` (표시할 단일 속성에 대 한 유효성 검사 메시지를 모델에), 및 `Validation Summary Tag Helper` (유효성 검사 오류의 요약 표시 입니다). `Input Tag Helper` HTML5 모델 클래스에 주석 특성 데이터에 따라 요소 입력에 클라이언트 쪽 유효성 검사 특성을 추가 합니다. 서버에서 유효성 검사도 수행 됩니다. 유효성 검사 태그 도우미 유효성 검사 오류가 발생 하는 경우 이러한 오류 메시지를 표시 합니다.

### <a name="the-validation-message-tag-helper"></a>유효성 검사 메시지 태그 도우미

* 추가 [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5) `data-valmsg-for="property"` 특성을 [에 걸쳐](https://developer.mozilla.org/docs/Web/HTML/Element/span) 지정된 모델 속성의 입력된 필드에 유효성 검사 오류 메시지를 연결 하는 요소입니다.   클라이언트 쪽 유효성 검사 오류가 발생할 때 [jQuery](https://jquery.com/) 의 오류 메시지를 표시는 `<span>` 요소입니다.

* 유효성 검사는 또한 서버에서 사용합니다. 클라이언트는 사용 하지 않도록 설정 하는 JavaScript 있을 수 있습니다 및 서버 쪽에서 일부 유효성 검사에만 수행할 수 있습니다.

* HTML 도우미 대체:`Html.ValidationMessageFor`

`Validation Message Tag Helper` 와 함께 사용 되는 `asp-validation-for` 특성에는 HTML [걸쳐](https://developer.mozilla.org/docs/Web/HTML/Element/span) 요소입니다.

```HTML
<span asp-validation-for="Email"></span>
   ```

유효성 검사 메시지 태그 도우미 다음 HTML을 생성 합니다.

```HTML
<span class="field-validation-valid"
  data-valmsg-for="Email"
  data-valmsg-replace="true"></span>
```

일반적으로 사용 된 `Validation Message Tag Helper` 후는 `Input` 동일한 속성에 대 한 태그 도우미입니다. 이렇게 하면 근처 오류가 발생 하는 입력 유효성 검사 오류 메시지가 표시 됩니다.

> [!NOTE]
> 올바른 javascript 보기 및 [jQuery](https://jquery.com/) 스크립트 클라이언트 쪽 유효성 검사에 대 한 위치에서 참조 합니다. 참조 [모델 유효성 검사](../models/validation.md) 자세한 정보에 대 한 합니다.

서버 쪽 유효성 검사 오류가 발생 하는 (예를 들어 사용자 지정 서버 쪽 유효성 검사 또는 경우 클라이언트 쪽 유효성 검사가 해제 된) MVC 배치 해당 오류 메시지의 본문으로는 `<span>` 요소입니다.

```HTML
<span class="field-validation-error" data-valmsg-for="Email"
            data-valmsg-replace="true">
   The Email Address field is required.
</span>
```

### <a name="the-validation-summary-tag-helper"></a>유효성 검사 요약 태그 도우미

* 대상 `<div>` 사용 하 여 요소는 `asp-validation-summary` 특성

* HTML 도우미 대체:`@Html.ValidationSummary`

`Validation Summary Tag Helper` 유효성 검사 메시지의 요약 정보를 표시 하는 데 사용 됩니다. `asp-validation-summary` 특성 값은 다음 중 하나일 수 있습니다.

|asp 유효성 검사 요약|표시 되는 유효성 검사 메시지|
|--- |--- |
|ValidationSummary.All|속성 및 모델 수준|
|ValidationSummary.ModelOnly|모델|
|ValidationSummary.None|없음|

### <a name="sample"></a>샘플

다음 예제에서는 데이터 모델으로 데코레이팅된 `DataAnnotation` 특성에 유효성 검사 오류 메시지를 생성 하는 메시지는 `<input>` 요소입니다.  유효성 검사 오류가 발생 하면 오류 메시지가 유효성 검사 태그 도우미에 표시 됩니다.

[!code-csharp[Main](working-with-forms/sample/final/ViewModels/RegisterViewModel.cs)]

[!code-HTML[Main](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterValidation.cshtml?highlight=4,6,8&range=1-10)]

생성 된 HTML (모델은 유효) 하는 경우:

<!-- literal_block {"ids": [], "linenos": false, "xml:space": "preserve", "language": "HTML", "highlight_args": {"hl_lines": [2, 3, 8, 9, 12, 13]}} -->

```HTML
<form action="/DemoReg/Register" method="post">
  <div class="validation-summary-valid" data-valmsg-summary="true">
  <ul><li style="display:none"></li></ul></div>
  Email:  <input name="Email" id="Email" type="email" value=""
   data-val-required="The Email field is required."
   data-val-email="The Email field is not a valid e-mail address."
   data-val="true"> <br>
  <span class="field-validation-valid" data-valmsg-replace="true"
   data-valmsg-for="Email"></span><br>
  Password: <input name="Password" id="Password" type="password"
   data-val-required="The Password field is required." data-val="true"><br>
  <span class="field-validation-valid" data-valmsg-replace="true"
   data-valmsg-for="Password"></span><br>
  <button type="submit">Register</button>
  <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>" />
</form>
```

## <a name="the-select-tag-helper"></a>Select 태그 도우미

* 생성 [선택](https://www.w3.org/wiki/HTML/Elements/select) 및 관련 된 [옵션](https://www.w3.org/wiki/HTML/Elements/option) 모델의 속성에 대 한 요소입니다.

* 에 HTML 도우미 대신 `Html.DropDownListFor` 및`Html.ListBoxFor`

`Select Tag Helper` `asp-for` 에 대 한 모델 속성 이름에서 [선택](https://www.w3.org/wiki/HTML/Elements/select) 요소 및 `asp-items` 지정는 [옵션](https://www.w3.org/wiki/HTML/Elements/option) 요소입니다.  예:

[!code-HTML[Main](working-with-forms/sample/final/Views/Home/Index.cshtml?range=4)]

예제:

[!code-csharp[Main](working-with-forms/sample/final/ViewModels/CountryViewModel.cs)]

`Index` 메서드를 초기화 하는 `CountryViewModel`, 선택한 국가별 설정 및에 전달 된 `Index` 보기.

[!code-csharp[Main](working-with-forms/sample/final/Controllers/HomeController.cs?range=114-119)]

HTTP POST `Index` 메서드 선택 항목을 표시 합니다.

[!code-csharp[Main](working-with-forms/sample/final/Controllers/HomeController.cs?range=15-27)]

`Index` 보기:

[!code-HTML[Main](working-with-forms/sample/final/Views/Home/Index.cshtml?highlight=4)]

("CA" 선택)로 다음 HTML을 생성 합니다.

<!-- literal_block {"ids": [], "linenos": false, "xml:space": "preserve", "language": "HTML", "highlight_args": {"hl_lines": [2, 3, 4, 5, 6]}} -->

```HTML
<form method="post" action="/">
     <select id="Country" name="Country">
       <option value="MX">Mexico</option>
       <option selected="selected" value="CA">Canada</option>
       <option value="US">USA</option>
     </select>
       <br /><button type="submit">Register</button>
     <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>" />
   </form>
   ```

> [!NOTE]
> 사용 하지 않는 것이 좋습니다 `ViewBag` 또는 `ViewData` 선택 태그 도우미와 합니다. 뷰 모델은 MVC 메타 데이터를 제공할에 더 강력 하 고 문제를 줄일 일반적으로 합니다.

`asp-for` 특성 값은 특별 한 경우 있으며 필요 하지 않습니다는 `Model` 접두사, 다른 태그 도우미 특성 안 함 (예: `asp-items`)

[!code-HTML[Main](working-with-forms/sample/final/Views/Home/Index.cshtml?range=4)]

### <a name="enum-binding"></a>Enum 바인딩

사용 하기 어려운 방식은 `<select>` 와 `enum` 속성을 생성 하 고는 `SelectListItem` 에서 요소를는 `enum` 값.

예제:

[!code-csharp[Main](working-with-forms/sample/final/ViewModels/CountryEnumViewModel.cs?range=3-7)]

[!code-csharp[Main](working-with-forms/sample/final/ViewModels/CountryEnum.cs)]

`GetEnumSelectList` 메서드를 생성 한 `SelectList` 열거형에 대 한 개체입니다.

[!code-HTML[Main](../../mvc/views/working-with-forms/sample/final/Views/Home/IndexEnum.cshtml?highlight=5)]

으로 목록에 열거자를 데코레이팅 할 수 있습니다는 `Display` 보다 풍부한 UI 가져올 특성:

[!code-csharp[Main](working-with-forms/sample/final/ViewModels/CountryEnum.cs?highlight=5,7)]

다음 HTML 생성 됩니다.

<!-- literal_block {"ids": [], "linenos": false, "xml:space": "preserve", "language": "HTML", "highlight_args": {"hl_lines": [4, 5]}} -->

```HTML
  <form method="post" action="/Home/IndexEnum">
         <select data-val="true" data-val-required="The EnumCountry field is required."
                 id="EnumCountry" name="EnumCountry">
             <option value="0">United Mexican States</option>
             <option value="1">United States of America</option>
             <option value="2">Canada</option>
             <option value="3">France</option>
             <option value="4">Germany</option>
             <option selected="selected" value="5">Spain</option>
         </select>
         <br /><button type="submit">Register</button>
         <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>" />
    </form>
   ```

### <a name="option-group"></a>옵션 그룹

HTML [ \<optgroup >](https://www.w3.org/wiki/HTML/Elements/optgroup) 뷰 모델 하나 이상 포함 하는 경우 요소가 생성 `SelectListGroup` 개체입니다.

`CountryViewModelGroup` 그룹은 `SelectListItem` "North America" 및 "Europe" 그룹으로 요소:

[!code-csharp[Main](../../mvc/views/working-with-forms/sample/final/ViewModels/CountryViewModelGroup.cs?highlight=5,6,14,20,26,32,38,44&range=6-56)]

두 개의 그룹은 다음과 같습니다.

![옵션 그룹 예제](working-with-forms/_static/grp.png)

생성 된 HTML:

<!-- literal_block {"ids": [], "linenos": false, "xml:space": "preserve", "language": "HTML", "highlight_args": {"hl_lines": [3, 4, 5, 6, 7, 8, 9, 10, 11, 12]}} -->

```HTML
 <form method="post" action="/Home/IndexGroup">
      <select id="Country" name="Country">
          <optgroup label="North America">
              <option value="MEX">Mexico</option>
              <option value="CAN">Canada</option>
              <option value="US">USA</option>
          </optgroup>
          <optgroup label="Europe">
              <option value="FR">France</option>
              <option value="ES">Spain</option>
              <option value="DE">Germany</option>
          </optgroup>
      </select>
      <br /><button type="submit">Register</button>
      <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>" />
 </form>
```

### <a name="multiple-select"></a>다중 선택

태그 선택 도우미 자동으로 생성 됩니다는 [여러 = "여러"](http://w3c.github.io/html-reference/select.html) 특성 속성에 지정 된 경우는 `asp-for` 특성은 한 `IEnumerable`합니다. 예를 들어 다음 모델을 가정합니다.

[!code-csharp[Main](../../mvc/views/working-with-forms/sample/final/ViewModels/CountryViewModelIEnumerable.cs?highlight=6)]

다음 보기:

[!code-HTML[Main](../../mvc/views/working-with-forms/sample/final/Views/Home/IndexMultiSelect.cshtml?highlight=4)]

다음 HTML을 생성합니다.

<!-- literal_block {"ids": [], "linenos": false, "xml:space": "preserve", "language": "HTML", "highlight_args": {"hl_lines": [3]}} -->

```HTML
<form method="post" action="/Home/IndexMultiSelect">
    <select id="CountryCodes"
    multiple="multiple"
    name="CountryCodes"><option value="MX">Mexico</option>
<option value="CA">Canada</option>
<option value="US">USA</option>
<option value="FR">France</option>
<option value="ES">Spain</option>
<option value="DE">Germany</option>
</select>
    <br /><button type="submit">Register</button>
  <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>" />
</form>
```

### <a name="no-selection"></a>선택 영역이 없는

여러 페이지에 "지정 안 됨된" 옵션을 사용 하 여 생각 되 면 HTML 반복을 제거 하는 템플릿을 만들 수 있습니다.

[!code-HTML[Main](../../mvc/views/working-with-forms/sample/final/Views/Home/IndexEmptyTemplate.cshtml?highlight=5)]

*Views/Shared/EditorTemplates/CountryViewModel.cshtml* 템플릿:

[!code-HTML[Main](working-with-forms/sample/final/Views/Shared/EditorTemplates/CountryViewModel.cshtml)]

추가 HTML [ \<옵션 >](https://www.w3.org/wiki/HTML/Elements/option) 요소에 국한 되지 않음는 *선택 영역이 없는* 대/소문자입니다. 예를 들어 다음과 같은 보기 및 작업 메서드는 위의 코드와 유사한 HTML을 생성 합니다.

[!code-csharp[Main](working-with-forms/sample/final/Controllers/HomeController.cs?range=114-119)]

[!code-HTML[Main](working-with-forms/sample/final/Views/Home/IndexOption.cshtml)]

올바른 `<option>` 요소가 선택 됩니다 (포함 된 `selected="selected"` 특성) 현재에 따라 `Country` 값입니다.

<!-- literal_block {"ids": [], "linenos": false, "xml:space": "preserve", "language": "HTML", "highlight_args": {"hl_lines": [5]}} -->

```HTML
 <form method="post" action="/Home/IndexEmpty">
      <select id="Country" name="Country">
          <option value="">&lt;none&gt;</option>
          <option value="MX">Mexico</option>
          <option value="CA" selected="selected">Canada</option>
          <option value="US">USA</option>
      </select>
      <br /><button type="submit">Register</button>
   <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>" />
 </form>
 ```

## <a name="additional-resources"></a>추가 리소스

* [태그 도우미](tag-helpers/intro.md)

* [HTML 폼 요소](https://www.w3.org/TR/html401/interact/forms.html)

* [확인 토큰을 요청 합니다.](http://www.asp.net/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages)

* [모델 바인딩](../models/model-binding.md)

* [모델 유효성 검사](../models/validation.md)

* [데이터 주석](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.aspx)

* [코드 조각이이 문서에 대 한](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/views/forms/sample)합니다.
