---
title: "모델 바인딩 | Microsoft 문서"
author: rick-anderson
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: b355a48e-a15c-4d58-b69c-899763613a97
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/models/model-binding
translationtype: Machine Translation
ms.sourcegitcommit: 010b730d2716f9f536fef889bc2f767afb648ef4
ms.openlocfilehash: 853b00c611f8f3017a3e4c9d280a252abc4697b5
ms.lasthandoff: 03/23/2017

---
# <a name="model-binding"></a>모델 바인딩

[Rachel Appel](http://github.com/rachelappel)

## <a name="introduction-to-model-binding"></a>모델 바인딩 소개

ASP.NET 핵심 MVC의 모델 바인딩과 작업 메서드 매개 변수를 HTTP 요청에서 데이터를 매핑합니다. 매개 변수 문자열, 정수 또는 float, 같은 단순 형식일 수 또는 복합 형식에 있을 수 있습니다. 데이터의 복잡도 관계 없이 자주 반복 되는 시나리오는 들어오는 데이터를 해당 하는 도구에 매핑 때문에 이것이 MVC의 매우 유용한 기능입니다. MVC 있으므로 개발자는 약간 다른 버전의 모든 응용 프로그램에서 같은 코드를 재작성 유지 않아도 바인딩을 추상화 함으로써이 문제를 해결 합니다. 형식 변환기 코드를 직접 텍스트를 작성 하는 것은 지루한 작업, 오류가 발생 하기 쉽습니다.

## <a name="how-model-binding-works"></a>모델 바인딩의 작동 원리

MVC는 HTTP 요청을 받으면 컨트롤러의 특정 동작 메서드에 라우팅합니다 것입니다. 경로 데이터의 기능에 따라 실행 하려면 작업 메서드를 결정 한 다음 해당 작업 메서드의 매개 변수를 HTTP 요청에서 값에 바인딩합니다. 예를 들어 다음 URL:

`http://contoso.com/movies/edit/2`

경로 템플릿 다음과 같은, 이후 `{controller=Home}/{action=Index}/{id?}`, `movies/edit/2` 라우팅하는 `Movies` 컨트롤러와 해당 `Edit` 작업 메서드. 선택적 매개 변수 라는 받습니다 `id`합니다. 작업 메서드에 대 한 코드는 코드는 다음과 같아야 합니다.

<!-- literal_block {"ids": [], "linenos": true, "xml:space": "preserve", "language": "csharp"} -->

```csharp
public IActionResult Edit(int? id)
   ```

참고: URL 경로에 있는 문자열 대/소문자 구분 되지 않습니다.

MVC는 이름으로 요청 데이터를 작업 매개 변수를 바인딩할 하려고 합니다. MVC는 매개 변수 이름 및 설정 가능한 공용 속성의 이름을 사용 하 여 각 매개 변수에 값을 찾습니다. 위의 예에서 유일한 action 매개 변수 이름은 `id`, MVC가 경로 값에 같은 이름의 값에 바인딩됩니다. 경로 값 외에도 MVC는 데이터 요청의 다양 한 부분에서 바인딩한 집합 순서에 따라 수행 합니다. 다음은 모델 바인딩을 통해 표시 되는 순서 대로 데이터 원본의 목록이입니다.

1. `Form values`: 들은 POST 메서드를 사용 하 여 HTTP 요청에서 이동 하는 형식 값입니다. (jQuery POST 요청을 포함).

2. `Route values`:에서 제공 하는 경로 값 집합이 [라우팅](../../fundamentals/routing.md)

3. `Query strings`쿼리 문자열: URI의 부분입니다.

<!-- DocFX BUG
The link works but generates an error when building with DocFX
@fundamentals/routing
[Routing](xref:fundamentals/routing)
-->

참고: 폼 값, 경로 데이터 및 쿼리 문자열은 모두 이름 값 쌍으로 저장 됩니다.

모델 바인딩 라는 키를 요청 하므로 `id` 아무것도 및 명명 된 `id` 폼 값의 이동 해당 키를 찾고 경로 값입니다. 이 예제에서는 일치 하는 이기 합니다. 바인딩은 발생 하 고 값은 정수 2로 변환 됩니다. 편집 (string id)를 사용 하 여 동일한 요청 "2" 문자열로 변환 합니다.

지금까지 예제는 단순 형식을 사용합니다. MVC에서 단순 유형은 모든.NET 기본 형식 또는 문자열 형식 변환기가 있는 형식입니다. 마치 작업 메서드의 매개 변수는 클래스와 같은 `Movie` MVC의 모델 바인딩이 속성을 처리 하는 보기 좋게 단순 및 복합 유형이 포함 되어 있는 형식입니다. 일치 항목을 찾습니다 복합 형식의 속성을 통과 하 리플렉션 및 재귀를 사용 합니다. 모델 바인딩 패턴을 찾고 *parameter_name.property_name* 속성에 값을 바인딩할 합니다. 이 폼의 일치 하는 값을 찾지 못하면 속성 이름만 사용 하 여 바인딩하려면 시도 합니다. 와 같은 이러한 형식에 대 한 `Collection` 형식 일치 항목을 찾고 모델 바인딩 *parameter_name [index]* 또는 그냥 *[index]*합니다. 바인딩 처리 모델 `Dictionary` 요청 마찬가지로, 형식 *parameter_name [키]* 또는 그냥 *[키]*로 키가 단순 형식입니다. 지원 되는 키가 HTML 필드 이름 및 동일한 모델 유형에 대해 생성 된 태그 도우미 일치 합니다. 이렇게 하면 양식 필드에 계속의 편의 위해 사용자의 입력으로 채워진 예를 들어 만들기 또는 편집에서 바인딩된 데이터 유효성 검사를 통과 하지 못한 경우 라운드트립 값.

바인딩 동작을 위해 클래스 공용 기본 생성자가 있어야 하 고 바인딩할 멤버에는 쓰기 가능한 공용 속성 이어야 합니다. 클래스는 인스턴스화할 공용 기본 생성자를 사용 하 여 모델 바인딩에 경우에 속성을 설정할 수 있습니다.

매개 변수가 바인딩되면 모델 바인딩 해당 이름의 값을 중지 하 고에 대해 다음 매개 변수를 바인딩합니다. 바인딩 실패, MVC 오류를 throw 하지 않습니다. 확인 하 여 모델 상태 오류에 대해 쿼리할 수는 `ModelState.IsValid` 속성입니다.

참고: 각 항목에는 컨트롤러의 `ModelState` 속성은 한 `ModelStateEntry` 를 포함 하는 `Errors property`합니다. 것은이 컬렉션을 직접 쿼리할 필요가 거의 없습니다. 대신 `ModelState.IsValid` 를 사용하세요.

또한는 MVC 모델 바인딩을 수행할 때 고려해 야 하는 몇 가지 특수 한 데이터 유형이 있습니다.

* `IFormFile``IEnumerable<IFormFile>`: HTTP 요청에 포함 된 하나 이상의 업로드 된 파일입니다.

* `CancelationToken`: 비동기 컨트롤러에서 활동을 취소 하는 데 사용 합니다.

이러한 형식은 클래스 형식에 액션 매개 변수 또는 속성에 바인딩할 수 있습니다.

모델 바인딩 완료 되 면 [유효성 검사](validation.md) 발생 합니다. 기본 모델 바인딩 대부분의 개발 시나리오에 대해 작동 합니다. 또한 확장도 가능 하므로 기본 동작을 사용자 지정할 수 있는 고유한 요구 하는 경우.

## <a name="customize-model-binding-behavior-with-attributes"></a>특성으로 모델 바인딩 동작 사용자 지정

MVC는 서로 다른 소스를 해당 기본 모델 바인딩 동작을 안내 하는 데 사용할 수 있는 몇 가지 특성이 포함 되어 있습니다. 예를 들어, 바인딩이 속성에 대 한 필요한 지 여부 또는 것 전혀 사용 하 여 발생 해서는 안을 지정할 수 있습니다는 `[BindRequired]` 또는 `[BindNever]` 특성입니다. 또는 기본 데이터 원본을 무시 하 고 모델 바인더 데이터 원본을 지정할 수도 있습니다. 다음은 모델 바인딩 특성 목록입니다.

* `[BindRequired]`:이 특성 바인딩이 발생할 수 없는 경우 모델 상태 오류를 추가 합니다.

* `[BindNever]`:이 매개 변수 하지 바인딩할 모델 바인더를 지시 합니다.

* `[FromHeader]``[FromQuery]`, `[FromRoute]`, `[FromForm]`:이 사용 하 여 적용 하려는 정확한 바인딩 소스를 지정 합니다.

* `[FromServices]`:이 특성에 사용 하 여 [종속성 주입](../../fundamentals/dependency-injection.md) 서비스에서 매개 변수를 바인딩합니다.

* `[FromBody]`: 요청 본문에서 데이터를 바인딩하는 구성 된 포맷터를 사용 합니다. 포맷터는 요청의 콘텐츠 형식에 따라 선택 됩니다.

* `[ModelBinder]`: 기본 모델 바인더, 바인딩 소스 및 이름을 재정의 하는 데 사용 합니다.

특성을 매우 유용한 도구를 바인딩하는 모델의 기본 동작을 재정의 해야 합니다.

## <a name="binding-formatted-data-from-the-request-body"></a>요청 본문에서 데이터를 포맷 하는 바인딩

요청 데이터는 다양 한 JSON, XML 및 다른 많은 사용자를 포함 하는 형식으로에서 가져올 수 있습니다. [FromBody] 특성을 사용 하 여 매개 변수는 요청 본문의 데이터에 바인딩할 나타내려면 MVC 해당 콘텐츠 형식에 따라 요청 데이터를 처리할 포맷터의 구성된 된 집합을 사용 합니다. 기본적으로 MVC 포함 한 `JsonInputFormatter` XML 및 기타 사용자 지정 형식의 처리 하기 위한 추가 포맷터 추가할 수 있지만 JSON 데이터 처리에 대 한 클래스입니다.

> [!NOTE]
> 있을 수 최대 하나의 매개 변수는 작업으로 데코 레이트 된 각 `[FromBody]`합니다. ASP.NET Core MVC 런타임 포맷터에 요청 스트림의 읽을 책임을 위임 합니다. 매개 변수를 요청 스트림의 읽이 되 면 일반적으로 수 없으면 다른 바인딩에 대 한 다시 요청 스트림을 읽는 데 `[FromBody]` 매개 변수입니다.

> [!NOTE]
> `JsonInputFormatter` 기본 포맷터 이며 기반 [Json.NET](http://www.newtonsoft.com/json)합니다.

ASP.NET 선택에 따라 입력된 포맷터는 [콘텐츠 형식](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html) 헤더와 유형의 매개 변수를 그렇지 않으면 지정 하 여 적용 되는 속성이 없는 경우. 다른 형식을 구성 해야에서 또는 XML을 사용 하려는 경우는 *Startup.cs* 수 있지만 파일을 먼저 해야 할에 대 한 참조를 가져올 `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet을 사용 하 여 합니다. 시작 코드는 코드는 다음과 같아야 합니다.

<!-- literal_block {"ids": [], "linenos": true, "xml:space": "preserve", "language": "csharp"} -->

```csharp
public void ConfigureServices(IServiceCollection services)
   {
       services.AddMvc()
          .AddXmlSerializerFormatters();
   }
   ```

코드에 *Startup.cs* 파일에 포함 되어는 `ConfigureServices` 메서드는 `services` 인수 ASP.NET 앱에 대 한 서비스를 빌드하는 데 사용할 수 있습니다. 이 샘플에서는 XML 포맷터 MVC이 응용이 프로그램에 제공 되는 서비스로 추가 하 고 있습니다. `options` 로 전달 된 인수는 `AddMvc` 메서드 추가 하 고 응용 프로그램 시작 시 MVC에서 필터, 포맷터 및 기타 시스템 옵션을 관리할 수 있습니다. 그런 다음 적용할는 `Consumes` 특성을 컨트롤러 클래스 또는 작업 메서드를 원하는 형식으로 작동 합니다.

