---
title: "유효성 검사 추가 | Microsoft 문서"
author: rick-anderson
description: "간단한 ASP.NET 핵심 응용 프로그램에 유효성 검사를 추가 하는 방법."
keywords: "ASP.NET Core, 유효성 검사 DataAnnotations"
ms.author: riande
manager: wpickett
ms.date: 03/07/2017
ms.topic: article
ms.assetid: edbed483-6858-4f19-8082-3ac7d2752986
ms.technology: aspnet
ms.prod: asp.net-core
uid: tutorials/first-mvc-app/validation
translationtype: Machine Translation
ms.sourcegitcommit: f93c93002fec0088a7040cd53f31fd5b5a62fea7
ms.openlocfilehash: 93fa45b87d312ba62d976421bba0b141368d3ba6
ms.lasthandoff: 03/23/2017

---
# <a name="adding-validation"></a>유효성 검사 추가

[Rick Anderson](https://twitter.com/RickAndMSFT)

이 섹션에서는 유효성 검사 논리를 추가 합니다는 `Movie` 모델과 있습니다 알게 될 유효성 검사 규칙 언제 든 지 사용자가 만들거나 편집 하는 동영상을 적용 됩니다.

## <a name="keeping-things-dry"></a>작업 상태를 유지합니다.

MVC의 디자인 원칙 중 하나는 [마른](http://en.wikipedia.org/wiki/Don%27t_repeat_yourself) ("안 함 반복 직접"). ASP.NET MVC 기능이 나 동작을 한 번만 지정 한 다음 응용 프로그램에 모든 위치에서 반영 하도록 장려 합니다. 작성 해야 하는 코드의 양을 줄어들고 오류가 발생 하기 쉬운을 쉽게 테스트 하 고 쉽게 유지 관리를 작성 하는 코드를 만듭니다.

MVC 및 Entity Framework 코어 Code First에서 제공 하는 유효성 검사 지원은 작업에는 마른 원칙 좋은 예입니다. (모델 클래스)의 한 부분에서 유효성 검사 규칙을 선언적으로 지정할 수 및 규칙은 앱의 모든 위치에서 적용 됩니다.

## <a name="adding-validation-rules-to-the-movie-model"></a>영화 모델에 유효성 검사 규칙 추가

열기는 *Movie.cs* 파일입니다. DataAnnotations 기본 제공 클래스 또는 속성에 선언적으로 적용 하는 유효성 검사 특성 집합을 제공 합니다. (같은 서식 특성을 포함 `DataType` 서식 지정에 대 한 도움말을 유효성 검사를 제공 하지 않습니다.)

업데이트는 `Movie` 클래스는 기본 제공 기능을 활용 하려면 `Required`, `StringLength`, `RegularExpression`, 및 `Range` 유효성 검사 특성입니다.

[!code-csharp[주](start-mvc/sample/MvcMovie/Models/MovieDateRatingDA.cs?name=snippet1)]

유효성 검사 특성에 적용 되는 모델 속성에 적용할 동작을 지정 합니다. `Required` 및 `MinimumLength` 특성 속성 값이 있어야 하지만 사용자가 사용할 수 없음이 유효성 검사를 충족 하기 위해 공백 방해가 전혀 나타냅니다. `RegularExpression` 특성을 사용 제한할 수 있는 문자를 입력 합니다. 위의 코드에서 `Genre` 및 `Rating` (공백, 숫자 및 특수 문자는 허용 되지 않음) 문자만 사용 해야 합니다. `Range` 특성에 지정된 된 범위 내의 값을 제한 합니다. `StringLength` 특성을 사용 하는 문자열 속성의 최대 길이 및 필요에 따라 최소 길이 설정할 수 있습니다. 값 형식 (예: `decimal`, `int`, `float`, `DateTime`)이 기본적으로 필요 하 고 필요 하지 않습니다는 `[Required]` 특성입니다.

유효성 검사 규칙을 자동으로 만드는 것에 의해 적용 ASP.NET 하 게 만드는 기능은 더 강력한 응용 프로그램. 또한 항목의 유효성을 검사 하 여 실수로 잘못 된 데이터를 데이터베이스에 사용 잊어서는 안을 보장 합니다.

## <a name="validation-error-ui-in-mvc"></a>Mvc에서 UI 유효성 검사 오류

앱을 실행 하 고 영화 컨트롤러 이동 합니다.

탭은 **새로 만들기** 새 동영상을 추가 하는 링크입니다. 일부 잘못 된 값으로 양식을 작성 합니다. JQuery 클라이언트 쪽 유효성 검사의 오류를 검색 하는 즉시 오류 메시지가 표시 됩니다.

![여러 jQuery 클라이언트 쪽 유효성 검사 오류가 있는 동영상 보기 양식](validation/_static/val.png)

> [!NOTE]
> 소수점이 하 또는에 쉼표를 입력할 수는 `Price` 필드입니다. 지원 하기 위해 [jQuery 유효성 검사](http://jqueryvalidation.org/) 쉼표를 사용 하는 영어가 아닌 로캘의 (","), 소수점 및 미국 영어 이외의 날짜 형식, 응용 프로그램을 전역화를 취해야 합니다. 참조 [추가 리소스](#additional-resources) 에 대 한 자세한 내용은 합니다. 이제 방금 10 같은 정수를 입력 합니다.

폼에 잘못 된 값을 포함 하는 각 필드에 적절 한 유효성 검사 오류 메시지에 자동으로 렌더링 하는 방법을 확인 합니다. 오류 (경우에 사용자가 사용 하지 않도록 설정 하는 JavaScript) (JavaScript 및 jQuery를 사용 하 여) 하는 클라이언트 쪽 및 서버 쪽 모두에 적용 됩니다.

가장 큰 이점은 한 줄의 코드를 변경 해야 하지는 `MoviesController` 클래스 또는 *Create.cshtml* UI이 유효성이 검사를 사용 하도록 설정 하려면 보기. 컨트롤러 및 자동으로이 자습서의 앞부분에서 만든 뷰 선택의 속성에 유효성 검사 특성을 사용 하 여 지정 된 유효성 검사 규칙은 `Movie` 모델 클래스입니다. 사용 하 여 테스트 유효성 검사는 `Edit` 동작 메서드와 동일한 유효성 검사가 적용 됩니다.

클라이언트 쪽 유효성 검사 오류가 없을 때까지 양식 데이터를 서버에 보내지지 않아. 중단점을 삽입 하 여이 확인할 수는 `HTTP Post` 메서드를 사용 하 여는 [Fiddler 도구](http://www.telerik.com/fiddler) , 또는 [F12 개발자 도구](https://dev.windows.com/en-us/microsoft-edge/platform/documentation/f12-devtools-guide/)합니다.

## <a name="how-validation-occurs-in-the-create-view-and-create-action-method"></a>만들기 보기 및 작업 메서드를 만들어 유효성 검사에서 발생 하는 방법

UI 유효성 검사 뷰나 컨트롤러의 코드에 대 한 업데이트 없이 생성 된 방법을 궁금할 것입니다. 다음 코드는 두 가지를 보여 줍니다. `Create` 메서드.

[!code-csharp[주](start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippetCreate)]

(HTTP GET) 첫 번째 `Create` 작업 메서드는 초기 만들기 폼을 표시 합니다. 두 번째 (`[HttpPost]`) 버전 폼 게시를 처리 합니다. 두 번째 `Create` 메서드 (의 `[HttpPost]` 버전) 호출 `ModelState.IsValid` 동영상에 유효성 검사 오류가 있는지 여부를 확인 합니다. 이 메서드를 호출 하는 개체에 적용 된 유효성 검사 특성을 평가 합니다. 개체에 유효성 검사 오류는 `Create` 메서드는 폼이 다시 표시 됩니다. 오류가 없는 경우 메서드는 데이터베이스에 새 동영상을 저장 합니다. 동영상 예제에서는 폼은 게시 되지 서버에 클라이언트 쪽;에서 검색 된 유효성 검사 오류가 있을 경우 두 번째 `Create` 메서드는 클라이언트 쪽 유효성 검사 오류가 있는 경우 호출 되지 않습니다. 브라우저에서 JavaScript를 사용 하지 않으면 클라이언트 유효성 검사를 사용 하지 않도록 설정 하 고 HTTP POST를 테스트할 수 있습니다 `Create` 메서드 `ModelState.IsValid` 유효성 검사 오류를 검색 합니다.

중단점을 설정할 수는 `[HttpPost] Create` 메서드 확인 메서드가 호출 되지 않습니다, 유효성 검사 오류가 발견 된 경우 클라이언트 쪽 유효성 검사 양식 데이터를 전송 하지 것입니다. 브라우저에서 JavaScript를 사용 하지 않도록 설정 하면 오류가 발생 하 고 양식을 제출 하는 경우 중단점이 적중 됩니다. 전체 유효성 검사 없이 얻게 됩니다. 다음 이미지는 Internet Explorer에서 JavaScript를 사용 하지 않도록 설정 하는 방법을 보여 줍니다.

![Internet Explorer: 보안 탭 인터넷 옵션의 사용자 지정 수준 단추를 클릭 합니다. 보안 설정-로컬 인트라넷 영역 대화 상자에서 스크립팅 섹션의 액티브 스크립팅 하는 사용 하지 않도록 설정 합니다.](validation/_static/p8_IE9_disableJavaScript.png)

다음 이미지는 FireFox 브라우저에서 JavaScript를 사용 하지 않도록 설정 하는 방법을 보여 줍니다.

![Firefox: 옵션의 콘텐츠 탭에서 확인란의 선택을 취소 Javascript를 사용 하도록 설정 합니다.](validation/_static/ff.png)

다음 그림에서는 Chrome 브라우저에서 JavaScript를 사용 하지 않도록 설정 하는 방법을 보여 줍니다.

![Google Chrome: 콘텐츠 설정의 Javascript에서 섹션을 선택 하지 못하게 JavaScript를 실행 하는 모든 사이트입니다.](validation/_static/chrome.png)

JavaScript을 해제 하면 잘못 된 데이터와 디버거를 통해 단계를 게시 합니다.

![잘못 된 데이터의 게시물에 디버깅 하는 동안 Intellisense ModelState.IsValid에 값이 false 보여 줍니다.](validation/_static/ms.png)

다음은 부분은 *Create.cshtml* 자습서 앞부분의 스 캐 폴드 템플릿 보기. 초기 폼을 표시 하 고 오류가 발생 한 경우 다시 표시를 모두 위에 표시 된 작업 방법으로 사용 됩니다.

[!code-HTML[주](start-mvc/sample/MvcMovie/Views/Movies/CreateRatingBrevity.cshtml)]

[입력 태그 도우미](xref:mvc/views/working-with-forms) 소비는 [DataAnnotations](http://msdn.microsoft.com/en-us/library/system.componentmodel.dataannotations.aspx) 특성 및 클라이언트 쪽에서 jQuery 유효성 검사에 필요한 HTML 특성을 생성 합니다. [유효성 검사 태그 도우미](xref:mvc/views/working-with-forms#the-validation-tag-helpers) 유효성 검사 오류를 표시 합니다. 참조 [유효성 검사](xref:mvc/models/validation) 에 대 한 자세한 내용은 합니다.

이 방법을 사용 하는 방법에 대 한 훌륭한 이란은 두 컨트롤러 또는 `Create` 보기 템플릿 알고 있는 아무 것도 적용 되는 실제 유효성 검사 규칙에 대 한 표시 되는 특정 오류 메시지에 대 한 합니다. 유효성 검사 규칙과 오류 문자열 에서만 지정 되며는 `Movie` 클래스입니다. 이러한 유효성 검사 규칙이 자동으로 적용 됩니다는 `Edit` 보기 및 모든 다른 뷰 템플릿을 만들 수 있습니다 모델을 편집 합니다.

유효성 검사 논리를 변경 해야 할 때 해도 정확 하 게 한 곳에서 모델에 유효성 검사 특성을 추가 하 여 (이 예제는 `Movie` 클래스). 되 고 규칙은 어떻게 적용과 일치 하는 응용 프로그램의 다른 부분에 걱정 하지 않아도 됩니다.-모든 유효성 검사 논리가 한 곳에서 정의 되 고 모든 곳에서 사용 합니다. 이 코드를 아주 간결 유지 하 고 쉽게 유지 관리 하 고 발전 시킬 수 있습니다. 거 야 함을 의미 하 고 완전히 마른 원칙을 적용 합니다.

## <a name="using-datatype-attributes"></a>데이터 형식 특성을 사용 하 여

열기는 *Movie.cs* 파일을 검사는 `Movie` 클래스입니다. `System.ComponentModel.DataAnnotations` 네임 스페이스 외에도 기본 제공 유효성 검사 특성의 서식 특성을 제공 합니다. 이미 적용 한 `DataType` 릴리스 날짜를 price 필드를 열거형 값입니다. 다음 코드는 `ReleaseDate` 및 `Price` 는 적절 한 속성 `DataType` 특성입니다.

[!code-csharp[주](start-mvc/sample/MvcMovie/Models/MovieDateRatingDA.cs?highlight=2,6&name=snippet2)]

`DataType` 특성만 데이터의 서식을 지정 하는 뷰 엔진에 대 한 힌트를 제공 (같은 특성을 제공 하 고 `<a>` URL에 대 한 및 `<a href="mailto:EmailAddress.com">` 전자 메일입니다. 사용할 수는 `RegularExpression` 형식의 데이터를 유효성을 검사 하는 특성입니다. `DataType` 는 유효성 검사 특성, 특성 데이터베이스 내장 형식 보다 구체적인 데이터 형식을 지정 하는 데 사용 됩니다. 이 경우에 원하는 시간이 아니라 날짜를 추적 하기 위해. `DataType` 열거형은 제공 많은 등의 데이터 형식, 날짜, 시간, 통화, 전자 메일 주소 및 전화 번호입니다. `DataType` 특성이 자동으로 특정 형식의 기능을 제공 하도록 응용 프로그램을 사용할 수도 있습니다. 예를 들어 한 `mailto:` 에 대 한 링크를 만들 수 `DataType.EmailAddress`에 대 한 날짜 선택기를 제공할 수 있습니다 `DataType.Date` HTML5 지원 브라우저에 합니다. `DataType` 특성 내보냅니다 HTML 5 `data-` (샵된 데이터 대시) 특성을 HTML 5 브라우저 이해할 수 있습니다. `DataType` 특성 **하지** 유효성을 검사 합니다.

`DataType.Date`표시 되는 날짜의 형식을 지정 하지 않습니다. 기본적으로 데이터 필드가 표시에 서버에 따라 기본 형식에 따라 `CultureInfo`합니다.

`DisplayFormat` 특성 날짜 형식을 명시적으로 지정 하는 데 사용 됩니다.

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
public DateTime ReleaseDate { get; set; }
```

`ApplyFormatInEditMode` 설정은 서식을 적용 해야 하는지도 값 편집을 위해 텍스트 상자에 표시 될 때 지정 합니다. (않을 수 있는 일부 필드에 대 한-예를 들어 통화 값에 대 한 것 원하지 않는 텍스트 상자에 통화 기호 편집 합니다.)

사용할 수는 `DisplayFormat` 자체를 만들었지만 특성은 일반적으로 사용 하는 것이 좋습니다는 `DataType` 특성입니다. `DataType` 특성을 화면에 렌더링 하는 방법이 아니라 데이터의 의미 체계를 전달 하 고 DisplayFormat으로 얻을 수 없는 다음과 같은 이점을 제공 합니다.

* 브라우저는 HTML5 기능 (예: calendar 컨트롤, 로캘에 적합 한 통화 기호, 전자 메일 링크, 등을 표시 합니다.)을 사용할 수 있습니다.

* 기본적으로 브라우저에 따라 올바른 형식을 사용 하 여 데이터를 렌더링 합니다 프로그램 [로캘](http://msdn.microsoft.com/en-us/library/vstudio/wyzd2bce.aspx)

* `DataType` 특성 수 데이터를 렌더링 하는 오른쪽 필드 템플릿을 선택 하는 MVC를 사용 하도록 설정 (의 `DisplayFormat` 자체 문자열 서식 파일을 사용 하 여 사용 하는 경우).

> [!NOTE]
> jQuery 유효성 검사는 사용 하지는 `Range` 특성 및 `DateTime`합니다. 예를 들어 다음 코드는 날짜가 지정된 된 범위에 있을 경우에 클라이언트 쪽 유효성 검사 오류 시을 항상 표시 됩니다.

```csharp
[Range(typeof(DateTime), "1/1/1966", "1/1/2020")]
   ```

사용 하 여 jQuery 날짜 유효성 검사를 사용 하지 않도록 할 수는 `Range` 특성을 `DateTime`합니다. 하지를 하드 날짜를 사용 하 여 모델을 컴파일하는 것이 좋습니다는 일반적으로 `Range` 특성 및 `DateTime` 사용 하지 않고 있습니다.

다음 코드 한 줄에 결합 특성을 보여 줍니다.

[!code-csharp[주](start-mvc/sample/MvcMovie/Models/MovieDateRatingDAmult.cs?name=snippet1)]

시리즈의 다음 부분에서는 응용 프로그램을 검토 합니다를 자동으로 생성 된 일부 향상 시키는 `Details` 및 `Delete` 메서드.

## <a name="additional-resources"></a>추가 리소스

* [양식 사용](xref:mvc/views/working-with-forms)
* [전역화 및 지역화](xref:fundamentals/localization)
* [태그 도우미 소개](xref:mvc/views/tag-helpers/intro)
* [태그 도우미를 작성합니다.](xref:mvc/views/tag-helpers/authoring)

>[!div class="step-by-step"]
[이전](new-field.md)
[다음](details.md)  

