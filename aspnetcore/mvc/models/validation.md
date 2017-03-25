---
title: "모델 유효성 검사 ASP.NET 핵심 mvc에서 | Microsoft 문서"
author: rick-anderson
description: "ASP.NET Core MVC에서 모델 유효성 검사를 소개합니다."
keywords: "ASP.NET Core, MVC, 유효성 검사"
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 3a8676dd-7ed8-4a05-bca2-44e288ab99ee
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/models/validation
ms.custom: H1Hack27Feb2017
translationtype: Machine Translation
ms.sourcegitcommit: 010b730d2716f9f536fef889bc2f767afb648ef4
ms.openlocfilehash: 506276c2bcddd3cb7591ee927f4a2a918ddd3a76
ms.lasthandoff: 03/23/2017

---
# <a name="introduction-to-model-validation-in-aspnet-core-mvc"></a>ASP.NET 핵심 MVC에서 모델 유효성 검사 소개

[Rachel Appel](http://github.com/rachelappel)

## <a name="introduction-to-model-validation"></a>모델 유효성 검사 소개

데이터베이스에 데이터를 저장 하는 응용 프로그램을 하기 전에 응용 프로그램 데이터를 확인 해야 합니다. 확인 된 형식 및 크기를 적절 하 게 서식이 지정 하 여 규칙을 따라야 합니다 잠재적인 보안 위협에 대 한 데이터를 확인할 수 해야 합니다. 중복 되 고 구현은 지루한 작업 이기 사용할 수 있지만 유효성 검사는 필요 합니다. MVC에서는 유효성 검사는 클라이언트와 서버 모두에서 발생합니다.

다행 스럽게도.NET에 유효성 검사 특성에 대 한 유효성 검사를 추상화 합니다. 이러한 특성에는 유효성 검사 코드를 작성 해야 하는 코드의 양을 줄이는 포함 합니다.

## <a name="validation-attributes"></a>유효성 검사 특성

유효성 검사 특성은 데이터베이스 테이블의 필드에 대 한 유효성 검사와 개념적으로 비슷합니다 되기 때문에 모델 유효성 검사를 구성 하는 방법이 있습니다. 데이터 형식 또는 필수 필드를 할당 하는 등의 제약 조건이 포함 됩니다. 다른 종류의 유효성 검사 전자 메일 주소 또는 전화 번호, 신용 카드와 같은 비즈니스 규칙을 적용 하는 데이터 패턴을 적용 하는 합니다. 유효성 검사 특성 적용 훨씬 쉽고 간단 하 게 사용 하 여 이러한 요구 사항을 확인 합니다.

다음 주석이 첨부 된은 `Movie` 영화 또는 TV 프로그램에 대 한 정보를 저장 하는 응용 프로그램에서 모델입니다. 대부분의 속성 필수 필드 이며 여러 문자열 속성이 길이 요구 사항이 있습니다. 또한가 대 한 숫자 범위 제한의 `Price` 속성을 0에서 $999.99, 사용자 지정 유효성 검사 특성을 합니다.

[!code-csharp[주](validation/sample/Movie.cs?range=6-31)]

단순히 모델을 통해 읽을 쉽게 코드를 유지 하기 위해이 응용 프로그램에 대 한 데이터에 대 한 규칙을 보여 줍니다. 다음은 몇 가지 인기 있는 기본 제공 유효성 검사 특성입니다.

* `[CreditCard]`: 유효성 검사 속성에는 신용 카드 형식입니다.

* `[Compare]`: 일치 하는 모델 항목의 두 속성의 유효성을 검사 합니다.

* `[EmailAddress]`: 유효성 검사 속성에는 전자 메일 형식입니다.

* `[Phone]`: 유효성 검사 속성에는 전화 형식입니다.

* `[Range]`: 지정된 된 범위 내에서 속성 값 변경의 유효성을 검사 합니다.

* `[RegularExpression]`: 데이터가 지정된 된 정규식 일치 하는지 확인 합니다.

* `[Required]`: 필요한 속성을 만듭니다.

* `[StringLength]`: 문자열 속성에 지정 된 최대 길이 최대 있는지 확인 합니다.

* `[Url]`: 유효성 검사 속성은 URL 형식입니다.

MVC에서 파생 된 모든 특성을 지원 `ValidationAttribute` 유효성 검사를 위해. 많은 유용한 유효성 검사 특성을 찾을 수 있습니다는 [System.ComponentModel.DataAnnotations](https://msdn.microsoft.com/en-us/library/system.componentmodel.dataannotations(v=vs.110).aspx) 네임 스페이스입니다.

기본 제공 특성을 제공 하는 보다 더 많은 기능을 해야 하는 인스턴스가 있을 수 있습니다. 해당 시간에 대 한 사용자 지정 유효성 검사 특성에서 파생 하 여 만들 수 있습니다 `ValidationAttribute` 를 구현 하 여 모델을 변경 하거나 `IValidatableObject`합니다.

## <a name="model-state"></a>모델 상태

모델 상태에 전송 된 HTML 폼 값 유효성 검사 오류를 나타냅니다.

MVC는 계속 필드에 수신 될 때까지 유효성 검사 오류 (기본적으로&200;)의 최대 수입니다. 다음 코드를 삽입 하 여이 수를 구성할 수는 `ConfigureServices` 에서 메서드는 `Startup.cs` 파일:

[!code-csharp[주](validation/sample/Startup.cs?range=27)]

## <a name="handling-model-state-errors"></a>오류 처리 모델 상태

모델 유효성 검사 호출 되는 각 컨트롤러 작업 전에 발생 하 고는 검사 해야 하는 작업 메서드의 `ModelState.IsValid` 하 고 적절 하 게 대처 합니다. 대부분의 경우에서 적절 한 반응 일종의 이상적인 모델 유효성 검사 실패 한 이유를 자세히 설명 하는 오류 응답을 반환 하는 것입니다.

일부 앱 경우 필터는 이러한 정책을 구현 하기 위한 적절 한 위치를 수 있습니다 모델 유효성 검사 오류를 처리 하기 위한 표준 규칙을 따르는 선택 합니다. 사용자가 작업이 유효 하지 않은 모델 상태와 동작 하는 방법을 테스트 해야 합니다.

## <a name="manual-validation"></a>수동 유효성 검사

모델 바인딩 및 유효성 검사 완료 후에 일부를 반복 하는 것이 좋습니다. 예를 들어, 사용자, 정수가 필요한 필드에 텍스트를 입력 한 수 또는 모델의 속성에 대 한 값을 계산 해야 할 수 있습니다.

유효성 검사를 수동으로 실행 해야 할 수도 있습니다. 이렇게 하려면 호출의 `TryValidateModel` 메서드를 다음과 같이 합니다.

[!code-csharp[주](validation/sample/MoviesController.cs?range=52)]

## <a name="custom-validation"></a>사용자 지정 유효성 검사

유효성 검사 특성은 대부분의 유효성 검사 요구 사항을 사용 됩니다. 그러나 일부 유효성 검사 규칙은 비즈니스에 특정 달라도 방금 필드를 확인 하는 등 일반 데이터 유효성 검사를 수행할지 또는 값의 범위를 따르는지입니다. 이러한 시나리오에 대 한 사용자 지정 유효성 검사 특성은 훌륭한 솔루션입니다. MVC에서 사용자 고유의 사용자 지정 유효성 검사 특성을 만들기 쉽습니다. 상속 하는 것은 `ValidationAttribute`, 재정의 `IsValid` 메서드. `IsValid` 메서드는 두 개의 매개 변수는 첫 번째 라는 개체는 *값* 이 고 두 번째는 `ValidationContext` 라는 개체 *validationContext*. *값* 사용자 지정 유효성 검사기의 유효성을 검사 하는 필드에서 실제 값을 나타냅니다.

다음 샘플에서는 비즈니스 규칙을 따르면 사용자가는 장르를 설정 하지 않을 수 있습니다 *클래식* 1960 이후 출시 된 영화에 대 한 합니다. `[ClassicMovie]` 특성 장르를 먼저 확인 하 고 기존 인 경우 다음을 검사 1960 보다 이후 지 릴리스 날짜입니다. 1960 후 해제 되는 경우 유효성 검사에 실패 합니다. 특성은 데이터의 유효성을 검사 하는 데 사용할 수 있는 연도 나타내는 정수 매개 변수를 수락 합니다. 다음과 같이 특성의 생성자에서 매개 변수 값을 캡처할 수 있습니다.

[!code-csharp[주](validation/sample/ClassicMovieAttribute.cs?range=9-28)]

`movie` 나타냅니다 위에 변수는 `Movie` 유효성을 검사 하는 양식 전송 으로부터 데이터를 포함 하는 개체입니다. 이 경우 유효성 검사를 확인 하는 날짜 장르에는 `IsValid` 의 메서드는 `ClassicMovieAttribute` 규칙에 따라 클래스입니다. 유효성을 검사 `IsValid` 반환는 `ValidationResult.Success` 코드 유효성 검사에 실패할 때와 `ValidationResult` 오류 메시지와 함께 합니다. 사용자가 수정 하는 경우는 `Genre` 필드는 폼을 전송 하 고는 `IsValid` 의 메서드는 `ClassicMovieAttribute` 동영상 기존 인지 확인 합니다. 기본 제공 특성과 마찬가지로 적용는 `ClassicMovieAttribute` 와 같은 속성에 `ReleaseDate` 되도록 발생 하는 유효성 검사는 앞의 코드 예제와 같이 합니다. 예제에서는 에서만 작동 하므로 `Movie` 형식, 더 나은 옵션은 사용 `IValidatableObject` 다음 단락에서와 같이 합니다.

구현 하 여 모델에 같은 코드에 배치 수는 또는 `Validate` 메서드는 `IValidatableObject` 인터페이스입니다. 사용자 지정 유효성 검사 특성을 개별 속성의 유효성을 검사 잘 작동 하는 동안 구현 `IValidatableObject` 여기에 나오는 클래스 수준 유효성 검사를 구현 하는 것입니다.

[!code-csharp[주](validation/sample/MovieIValidatable.cs?range=33-41)]

## <a name="client-side-validation"></a>클라이언트 쪽 유효성 검사

클라이언트 쪽 유효성 검사는 사용자에 대 한 매우 편리 합니다. 시간이 절약 소비 그렇지 않은 경우 이러한 왕복에 대 한 대기 중인 서버. 비즈니스 관계에서 일부 소수 단위 초 수백 번 매일 곱한 추가 최대 많은 시간과 비용, 노력 합니다. 간단 하 고 즉시 유효성 검사를 사용 하면 보다 효율적으로 작업 하 고 더 나은 품질 입력 및 출력을 생성할 수 있습니다.

여기에 표시 된 대로 작동 하려면 클라이언트 쪽 유효성 검사에 대 한 적절 한 JavaScript 스크립트 참조 된 보기가 있어야 합니다.

[!code-html[주](validation/sample/Views/Shared/_Layout.cshtml?range=37)]

[!code-html[주](validation/sample/Views/Shared/_ValidationScriptsPartial.cshtml)]

MVC는 데이터의 유효성을 검사 하 고 JavaScript를 사용 하 여 오류 메시지를 표시 하려면 모델 속성에서 형식 메타 데이터 외에도 유효성 검사 특성을 사용 합니다. MVC 폼 요소에서 사용 하 여 모델을 렌더링을 사용 하는 경우 [태그 도우미](https://docs.asp.net/en/latest/mvc/views/tag-helpers/index.html) 또는 [HTML 도우미](https://docs.asp.net/en/latest/mvc/views/html-helpers.html) HTML 5를 추가 합니다 [데이터 특성](http://w3c.github.io/html/dom.html#embedding-custom-non-visible-data-with-the-data-attributes) 아래와 같이 유효성 검사를 하는 폼 요소에 있습니다. MVC에서는 오류가 발생 하는 `data-` 기본 제공 및 사용자 지정 특성에 대 한 특성입니다. 다음과 같이 관련 태그 도우미를 사용 하 여 클라이언트에서 유효성 검사 오류를 표시할 수 있습니다.

[!code-html[주](validation/sample/Views/Movies/Create.cshtml?highlight=4,5&range=19-25)]

위의 태그 도우미 아래 HTML을 렌더링합니다. 다음에 유의 `data-` HTML의 특성에 대 한 유효성 검사 특성에 해당 출력은 `ReleaseDate` 속성입니다. `data-val-required` 사용자 하지 릴리스 날짜 필드를 채우고 함께에 해당 메시지를 표시 하는 경우 표시할 오류 메시지를 포함 하는 특성 아래 `<span>` 요소입니다.

<!-- literal_block {"ids": [], "linenos": false, "xml:space": "preserve", "language": "html", "highlight_args": {"hl_lines": [8, 9, 10, 11, 12]}} -->

```html
<form action="/movies/Create" method="post">
  <div class="form-horizontal">
    <h4>Movie</h4>
    <div class="text-danger"></div>
    <div class="form-group">
      <label class="col-md-2 control-label" for="ReleaseDate">ReleaseDate</label>
      <div class="col-md-10">
        <input class="form-control" type="datetime"
        data-val="true" data-val-required="The ReleaseDate field is required."
        id="ReleaseDate" name="ReleaseDate" value="" />
        <span class="text-danger field-validation-valid"
        data-valmsg-for="ReleaseDate" data-valmsg-replace="true"></span>
      </div>
    </div>
    </div>
</form>
```

폼이 유효한 될 때까지 제출을 방지 하는 클라이언트 쪽 유효성 검사 합니다. 제출 단추 양식을 제출 또는 오류 메시지를 표시 하는 JavaScript를 실행 합니다.

MVC 형식 특성 값을 사용 하 여를 재정의할 수 있는 속성의.NET 데이터 형식에 따라 결정 `[DataType]` 특성입니다. 기본 `[DataType]` 특성에는 실제 서버 쪽 유효성을 검사 하지 않습니다. 하지만 그러나 브라우저 자체 오류 메시지를 선택 하 고 원하는, jQuery 비간섭 유효성 검사 패키지는 메시지를 무시 하 고 다른 사용자와 일관 되 게 표시할 수 해당 오류를 표시 합니다. 사용자는 적용할 때 가장 중요 한 사실은 이런 `[DataType]` 하위 클래스와 같은 `[EmailAddress]`합니다.

## <a name="iclientmodelvalidator"></a>IClientModelValidator

사용자 지정 특성에 대 한 클라이언트 쪽 논리를 만들 수 있습니다 및 [비간섭 유효성 검사](http://jqueryvalidation.org/documentation/) 유효성 검사의 일부로 자동으로 클라이언트에서 실행 됩니다. 첫 번째 단계를 구현 하 여 필요한 데이터 특성 추가 제어 하는 것은 `IClientModelValidator` 다음과 같이 인터페이스:

[!code-csharp[주](validation/sample/ClassicMovieAttribute.cs?range=30-42)]

이 인터페이스를 구현 하는 특성이 생성 된 필드에 HTML 특성을 추가할 수 있습니다. 에 대 한 출력을 검사 하는 `ReleaseDate` 요소는 앞의 예제와 유사 하지만 이제는 HTML을 표시는 `data-val-classicmovie` 에 정의 된 특성의 `AddValidation` 메서드의 `IClientModelValidator`합니다.

```html
<input class="form-control" type="datetime"
data-val="true"
data-val-classicmovie="Classic movies must have a release year earlier than 1960"
data-val-classicmovie-year="1960"
data-val-required="The ReleaseDate field is required."
id="ReleaseDate" name="ReleaseDate" value="" />
```

비간섭 유효성 검사의 데이터를 사용 하 여는 `data-` 속성에 오류 메시지를 표시 합니다. 그러나 jQuery 규칙에 대 한 알 수 없는 또는 jQuery에 추가할 때까지 메시지 `validator` 개체입니다. 라는 메서드를 추가 하는 아래 예제에서이 확인할 `classicmovie` jQuery 사용자 지정 클라이언트 유효성 검사 코드가 포함 된 `validator` 개체입니다.

[!code-javascript[주](validation/sample/Views/Movies/Create.cshtml?range=71-93)]

이제 jQuery에 유효성 검사 코드는 false를 반환 하는 경우 표시할 오류 메시지 뿐 아니라 사용자 지정 JavaScript 유효성 검사를 실행 하는 정보가 있습니다.

## <a name="remote-validation"></a>원격 유효성 검사

원격 유효성 검사는 서버에서 데이터에 대 한 클라이언트에서 데이터의 유효성을 검사 해야 할 때 사용 하는 훌륭한 기능입니다. 예를 들어 앱은 전자 메일 또는 사용자 이름이 이미 사용 중입니다. 및 많은 양의 작업을 수행 하는 데이터를 검색 해야 하는지 여부를 확인 해야 합니다. 하나는 유효성 검사에 대 한 데이터 집합이 큰 다운로드 또는 너무 많은 리소스를 사용 하는 몇 가지 필드입니다. 중요 한 정보를 노출할 수 있습니다. 필드의 유효성을 검사 하는 라운드트립 요청 하는 대신이입니다.

원격 유효성 검사는 두 단계에서 구현할 수 있습니다. 첫째, 사용 하 여 모델 주석을 `[Remote]` 특성입니다. `[Remote]` 특성에 사용할 여러 개의 오버 로드를 호출 하는 적절 한 코드를 클라이언트 쪽 JavaScript를 직접 사용할 수 있습니다. 이 예제를 가리키는 `VerifyEmail` 작업 메서드는 `Users` 컨트롤러입니다.

[!code-csharp[주](validation/sample/User.cs?range=5-9)]

두 번째 단계 지정 하는 것은 유효성 검사 코드의 해당 하는 작업 메서드에 정의 된 대로 `[Remote]` 특성입니다. 반환 된 `JsonResult` 클라이언트 쪽 또는 계속 진행 하 고, 일시 중지 하 고, 필요한 경우 오류를 표시 하는 데 사용할 수 있는 합니다.

[!code-none[주](validation/sample/UsersController.cs?range=19-28)]

보기에는 JavaScript에서 해당 전자 메일 생성 된 경우 참조를 원격 호출할 사용자가 전자 메일을 입력 하는 경우 이제 및 오류 메시지를 표시 합니다. 그렇지 않으면 사용자는 양식을 정상적으로 전송할 수 있습니다.

