---
title: "테스트 컨트롤러 논리 | Microsoft 문서"
author: ardalis
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: dd4135ec-2b15-410c-b3fb-3d12eed4a1ac
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/controllers/testing
translationtype: Machine Translation
ms.sourcegitcommit: 38d4c0e385aa8d68440118a12d6dd1e94aa7bfd2
ms.openlocfilehash: ed39b4b563f1da0761f120a6a1ec5bcc33737b50
ms.lasthandoff: 03/23/2017

---
# <a name="testing-controllers"></a>테스트 컨트롤러

[Steve Smith](http://ardalis.com)

ASP.NET MVC 앱에서 컨트롤러 작고 사용자 인터페이스 문제에 집중 해야 합니다. 비 UI 문제를 처리 하는 큰 컨트롤러는 테스트 및 유지 관리 하기가 더 어렵습니다.

[보기 또는 GitHub에서 샘플을 다운로드 합니다.](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/controllers/testing/sample)

## <a name="why-test-controllers"></a>테스트 컨트롤러 이유

컨트롤러는 모든 핵심 ASP.NET MVC 응용 프로그램의 중앙 부분입니다. 따라서 동작 하는 앱에 대 한 의도 한 대로 신뢰도 있어야 합니다. 자동화 된 테스트가이 신뢰도 제공할 수 및 프로덕션에 도달 하기 전에 오류를 검색할 수 있습니다. 컨트롤러 내에서 불필요 한 책임을 배치 하지 하 고 컨트롤러 책임에만 테스트 포커스 확인을 고려해 야 합니다.

컨트롤러 논리 최소화 해야 하 고 비즈니스 논리 또는 인프라 문제나 (예를 들어 데이터 액세스)에 대해 수행 되지 않습니다. 프레임 워크 하지 컨트롤러 논리를 테스트 합니다. 테스트 방법을 컨트롤러 *동작* 잘못 되었거나 잘못 된 입력에 기반 합니다. 수행 하는 비즈니스 작업의 결과에 따라 컨트롤러 응답을 테스트 합니다.

일반적인 컨트롤러 책임.

* 확인`ModelState.IsValid`

* 오류 응답을 반환 하는 경우 `ModelState` 올바르지 않습니다

* 지 속성 저장소에서 비즈니스 엔터티를 검색 합니다.

* 비즈니스 엔터티 작업을 수행

* 지 속성을 비즈니스 엔터티를 저장 합니다.

* 적절 한 반환`IActionResult`

## <a name="unit-testing"></a>단위 테스트

[단위 테스트](https://docs.microsoft.com/en-us/dotnet/articles/core/testing/unit-testing-with-dotnet-test) 는 인프라와 종속성에서 격리 된 상태에서 응용 프로그램의 일부를 테스트 해야 합니다. 단위 테스트 컨트롤러 논리를 한 번의 작업의 내용만 검사 될 때, 프레임 워크 자체 또는 해당 종속성의 동작 하지 않습니다. 사용자 단위 테스트 컨트롤러 작업의 동작에만 집중할 수 있는지 확인 합니다. 컨트롤러 단위 테스트 방지 등 [필터](filters.md), [라우팅](../../fundamentals/routing.md), 또는 [모델 바인딩](../models/model-binding.md)합니다. 한 가지 테스트에 초점을 맞춤 으로써 단위 테스트는 일반적으로 쉽게 작성할 수 및 신속 하 게 실행 합니다. 많은 오버 헤드 없이 자주 잘 작성 된 일련의 단위 테스트를 실행할 수 있습니다. 그러나 단위 테스트 문제 검색 되지 않는 구성 요소 간의 상호 작용의 용도 [통합 테스트](xref:mvc/controllers/testing#integration-testing)합니다.

단위 테스트를 해야 하는 사용자 지정 필터, 경로 등을 작성 하는 경우, 있지만 테스트 특정 컨트롤러 작업에 포함 되지 않습니다. 격리 된 상태에서 테스트 해야 합니다.

> [!TIP]
> [만들고 Visual Studio와 함께 단위 테스트를 실행할](https://www.visualstudio.com/en-us/get-started/code/create-and-run-unit-tests-vs)합니다.

단위 테스트를 보여 주기 위해 다음 컨트롤러를 검토 합니다. 브레인스토밍 세션의 목록을 표시 하 고 새로운 브레인스토밍를 게시물을 만들 수는 세션을 허용 합니다.

[!code-csharp[주](testing/sample/TestingControllersSample/src/TestingControllersSample/Controllers/HomeController.cs?highlight=12,16,21,42,43)]

컨트롤러는 다음의 [명시적 종속성 원칙](http://deviq.com/explicit-dependencies-principle/), 종속성 주입의 인스턴스로 제공 하기를 기대 `IBrainstormSessionRepository`합니다. 이렇게 하면 원하는 모의 개체 프레임 워크를 사용 하 여 테스트를 비교적 쉽게 [Moq](https://www.nuget.org/packages/Moq/)합니다. `HTTP GET Index` 메서드에 반복 또는 분기 및만 호출 한 방법은 없습니다. 이 테스트 `Index` 메서드를 확인 해야 하는 `ViewResult` 반환 되 면와 `ViewModel` 리포지토리에서 `List` 메서드.

[!code-csharp[주](testing/sample/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/HomeControllerTests.cs?highlight=17-18&range=1-33,75-95)]

`HomeController` `HTTP POST Index` 메서드 (위에 표시 됨) 확인 해야 합니다.

* 작업 메서드는 잘못 된 요청 `ViewResult` 적절 한 데이터와 함께 때 `ModelState.IsValid` 는`false`

* `Add` 저장소에서 메서드를 호출 및 `RedirectToActionResult` 올바른 인수를 함께 반환 됩니다 때 `ModelState.IsValid` 그렇습니다.

잘못 된 모델 상태를 사용 하 여 오류를 추가 하 여 테스트할 수 `AddModelError` 아래 첫 번째 테스트와 같이 합니다.

[!code-csharp[주](testing/sample/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/HomeControllerTests.cs?highlight=8,15-16,37-39&range=35-75)]

첫 번째 테스트를 확인 하는 경우 `ModelState` 유효 하지 동일한 `ViewResult` 와 반환 되는 `GET` 요청 합니다. 테스트는 잘못 된 모델에 전달 하려고 시도 하지는 참고 사항 작동 하지 않습니다 모델 바인딩 실행이 중지 되므로 (경우에 [통합 테스트](xref:mvc/controllers/testing#integration-testing) 실습 모델 바인딩을 사용 합니다). 이 경우 모델 바인딩을 테스트 중입니다. 이러한 단위 테스트의 작업 메서드에 코드 수행 작업 테스트할만 합니다.

두 번째 테스트는 확인 `ModelState` 올바른지, 새 `BrainstormSession` (리포지토리)를 통해 추가 됩니다 메서드가 반환 하 고는 `RedirectToActionResult` 예상된 속성을 사용 합니다. 모의 호출 되지 호출은 일반적으로 무시 하지만 호출 `Verifiable` 설치 프로그램의 끝에 호출 수 있도록 테스트에서 확인할 수 있습니다. 호출 하 여 이렇게 `mockRepo.Verify`, 예상 된 메서드가 호출 되지 않았다는 테스트 실패 합니다.

> [!NOTE]
> 이 샘플에 사용 되는 Moq 라이브러리 쉽게 비안정형 모의 개체 ("느슨한" mock 또는 스텁 라고도 함)를 사용한 검증할 수 있는, 또는 "strict" mock를 혼합할 수 있습니다. 에 대 한 자세한 내용은 [Moq Mock 동작을 사용자 지정](https://github.com/Moq/moq4/wiki/Quickstart#customizing-mock-behavior)합니다.

응용 프로그램의 다른 컨트롤러에는 특정 브레인스토밍 세션에 관련 된 정보가 표시 됩니다. 잘못 된 id 값으로 처리 하는 일부 논리를 포함 합니다.

[!code-csharp[주](./testing/sample/TestingControllersSample/src/TestingControllersSample/Controllers/SessionController.cs?highlight=19,20,21,22,25,26,27,28)]

컨트롤러 작업에 세 개의 사례를 테스트에 대해 각각 하나씩 `return` 문:

[!code-csharp[주](testing/sample/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/SessionControllerTests.cs?highlight=27,28,29,46,47,64,65,66,67,68)]

웹 API (브레인스토밍 세션 및 세션에 새로운 아이디어를 추가 하는 방법을 관련 된 아이디어 목록)과 기능을 제공 하는 응용 프로그램:

<a name=ideas-controller></a>

[!code-csharp[주](testing/sample/TestingControllersSample/src/TestingControllersSample/Api/IdeasController.cs?highlight=21,22,27,30,31,32,33,34,35,36,41,42,46,52,65)]

`ForSession` 메서드 목록을 반환 `IdeaDTO` 형식입니다. API 호출을 통해 직접 비즈니스 도메인 엔터티를 반환 하지 않도록, 자주 이후 API 클라이언트에 필요 하 고 응용 프로그램의 내부 도메인 모델 외부에 노출 하는 API와 함께 불필요 하 게 결합은 보다 많은 데이터가 포함 됩니다. 도메인 엔터티 및 연결을 통해 반환 됩니다 형식 간의 매핑을 수동으로 수행할 수 있습니다 (LINQ를 사용 하 여 `Select` 다음과 같이) 또는 같은 라이브러리를 사용 하 여 [AutoMapper](https://github.com/AutoMapper/AutoMapper)

에 대 한 단위 테스트는 `Create` 및 `ForSession` API 메서드:

[!code-csharp[주](testing/sample/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?highlight=18,23,29,33,38-39,43,50,58-59,68-70,76-78&range=1-83,121-135)]

메서드의 동작을 테스트 하려면 이전에 설명한 것 처럼 때 `ModelState` 유효 하지 않거나 모델 오류에는 테스트의 일환으로 컨트롤러에 추가 합니다. 모델 유효성 검사 또는 모델 바인딩 단위 테스트에서 테스트-방금 특정 발생할 경우 작업 메서드의 동작을 테스트 하지 마십시오 `ModelState` 값입니다.

두 번째 테스트는 모의 리포지토리는 null을 반환 하도록 구성 되어 있으므로, null을 반환 하는 저장소에 따라 다릅니다. 테스트 데이터베이스를 만들 필요가 없습니다 (메모리에서 또는 기타 방식으로) 하 고이 결과 반환 하는 쿼리를 생성할-표시 된 것 처럼 단일 문으로 수행할 수 있습니다.

마지막 테스트를 확인 하는 저장소의 `Update` 메서드를 호출 합니다. Mock를 호출 이전에 수행한 것과 `Verifiable` 및 다음 모의 리포지토리의 `Verify` 메서드는 안정형 메서드가 실행 되었음을 확인 합니다. 단위 테스트 확인 해야 하는 이기는 `Update` 데이터를 저장 하는 메서드, 통합 테스트를 수행할 수 있습니다.

## <a name="integration-testing"></a>통합 테스트

[통합 테스트](../../testing/integration-testing.md) 응용 프로그램 작업 내에서 별도 모듈을 함께 제대로 확인 하는 실행 합니다. 일반적으로 단위 테스트와 함께 테스트할 수 작업을 수행을 테스트할 수도 있습니다는 통합 테스트와 하지만 반대로 제한이 적용 되지 않습니다. 그러나 통합 테스트는 단위 테스트 보다 속도가 훨씬 경향이 있습니다. 따라서 모든 단위 테스트와 함께 하는 통합 테스트를 사용 하 여 여러 공동 작업자와 관련 된 시나리오에 대 한 테스트 하는 것이 좋습니다.

여전히 유용 하지만, 모의 개체는 통합 테스트에서 거의 사용 됩니다. 단위 테스트, 모의 개체는 테스트의 목적에 대 한 테스트 하는 단위 외부에서 회사 들은 해야 작동 하는 방식을 제어 하는 효과적인 방법. 통합 테스트에서 실제 공동 작업자 전체 하위 시스템 작동 함께 제대로 확인 하는 데 사용 됩니다.

### <a name="application-state"></a>응용 프로그램 상태

하나의 중요 한 고려 사항은 통합 테스트를 수행 하는 경우에 응용 프로그램 상태를 설정 하는 방법입니다. 따라서 각 테스트는 알려진된 상태에서 응용 프로그램부터 시작 해야와 테스트를 서로 독립적으로 실행 해야 합니다. 응용 프로그램 데이터베이스를 사용 하지 못하거나 모든 지 속성에 문제 되지 않을 수 있습니다. 그러나 대부분의 실제 응용 프로그램 데이터 저장소를 다시 설정 하지 않는 한 변경 내용은 모두 하나의 테스트 하 여 다른 테스트 영향을 줄 수 있으므로 몇 가지 종류의 데이터 저장소에 상태를 유지 합니다. 기본 제공을 사용 하 여 `TestServer`, 우리의 통합 테스트 내에서 호스트 ASP.NET 핵심 응용 프로그램에 매우 간단 하지만 사용 데이터에 대 한 액세스 권한을 부여 필요 하지 않습니다. 실제 데이터베이스를 사용 하는 경우 한 가지 방법은 테스트 데이터베이스에 연결 하는 앱을 각 테스트를 실행 하기 전에 알려진 상태로 다시 설정 됩니다 테스트에서 액세스 하 고 확인 수입니다.

이 샘플 응용 프로그램에서 사용 하 고 Entity Framework Core InMemoryDatabase 지원, 테스트 프로젝트 내에서 것에 연결할 수 있도록 합니다. 노출 대신는 `InitializeDatabase` 응용 프로그램의 메서드에서 `Startup` 에 있으면 응용 프로그램 시작 될 때 호출 하는 클래스는 `Development` 환경입니다. 통합 테스트에서이 환경을으로 설정 하는 것으로 자동으로 활용 `Development`합니다. InMemoryDatabase는 앱이 다시 시작 될 때마다 다시 설정한 후 데이터베이스를 다시 설정에 대해 걱정 하지 않아도 됩니다.

`Startup` 클래스:

[!code-csharp[주](testing/sample/TestingControllersSample/src/TestingControllersSample/Startup.cs?highlight=19,20,38,39,47,56)]

표시는 `GetTestSession` 메서드 아래 통합 테스트에 자주 사용 합니다.

### <a name="accessing-views"></a>보기에 액세스

각 통합 테스트 클래스를 구성 된 `TestServer` ASP.NET 핵심 앱을 실행 하는 합니다. 기본적으로 `TestServer` 실행 위치-이 경우 테스트 프로젝트 폴더는 폴더에 웹 응용 프로그램 호스트입니다. 따라서 하려고 할 때 테스트 컨트롤러 작업을 반환 하는 `ViewResult`,이 오류가 표시 될 수 있습니다.

<!-- literal_block {"ids": [], "names": [], "highlight_args": {}, "backrefs": [], "dupnames": [], "linenos": false, "classes": [], "xml:space": "preserve", "language": "none"} -->

```none
The view 'Index' was not found. The following locations were searched:
(list of locations)
```

이 문제를 해결 하려면 테스트 중인 프로젝트에 대 한 보기를 찾을 수 있도록 서버의 콘텐츠 루트를 구성 해야 합니다. 호출 하 여 이렇게 `UseContentRoot` 에 `TestFixture` 아래에 표시 된 클래스:

[!code-csharp[주](testing/sample/TestingControllersSample/tests/TestingControllersSample.Tests/IntegrationTests/TestFixture.cs?highlight=32,35)]

`TestFixture` 클래스는 구성 및 만들기는 `TestServer`설정는 `HttpClient` 와 통신 하는 `TestServer`합니다. 사용 하 여 통합의 각 테스트는 `Client` 속성을 테스트 서버에 연결 하 고 요청을 확인 합니다.

[!code-csharp[주](testing/sample/TestingControllersSample/tests/TestingControllersSample.Tests/IntegrationTests/HomeControllerTests.cs?highlight=20,26,29,30,31,35,38,39,40,41,44,47,48)]

위의 첫 번째 테스트는 `responseString` 실제 렌더링 된 HTML을 검사 하 여 예상된 결과 포함 되어 있는지 확인 하 여 수 뷰에서 보유 합니다.

두 번째 테스트 고유한 세션 이름 포함 하는 폼 POST를 생성 하 고, 앱에 게시 다음 예상된 리디렉션이 반환 되는지 확인 합니다.

### <a name="api-methods"></a>API 메서드

응용 프로그램 웹 Api, it의 자동화 된 테스트는 것이 좋습니다 예상 대로 실행 확인을 노출 하는 경우. 기본 제공 `TestServer` 쉽게 웹 Api를 테스트 합니다. 항상 확인 해야 API 메서드에 바인딩하는 모델을 사용 하는 경우 `ModelState.IsValid`, 및 통합 테스트는 모델 유효성 검사를 제대로 작동 하는지 확인 하는 곳입니다.

테스트 대상의 다음 집합은 `Create` 에서 메서드는 [IdeasController](xref:mvc/controllers/testing#ideas-controller) 위에 표시 된 클래스:

[!code-csharp[주](testing/sample/TestingControllersSample/tests/TestingControllersSample.Tests/IntegrationTests/ApiIdeasControllerTests.cs)]

달리 HTML 뷰를 반환 하는 동작의 통합 테스트를 결과 반환 하는 웹 API 메서드에 될 수 있습니다 일반적으로 강력한 형식의 개체로 deserialize 된 위의 마지막 테스트와 같이 합니다. 테스트 결과를 역직렬화 하는 경우에 `BrainstormSession` 인스턴스로 이동한 아이디어를 해당 컬렉션의 아이디어에 올바르게 추가 되었는지 확인 합니다.

이 문서에서 통합 테스트의 추가 예제를 찾을 수 있을 [샘플 프로젝트](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/controllers/testing/sample)합니다.

