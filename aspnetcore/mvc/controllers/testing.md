---
title: "ASP.NET Core에서 컨트롤러 논리를 테스트합니다."
author: ardalis
description: "ASP.NET Core Moq, xUnit와에서 컨트롤러 논리를 테스트 하는 방법에 알아봅니다."
keywords: "ASP.NET Core, 컨트롤러, 테스트, 테스트, 단위 테스트, 단위 테스트, 통합 테스트, 통합 테스트, xUnit"
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: dd4135ec-2b15-410c-b3fb-3d12eed4a1ac
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/controllers/testing
ms.openlocfilehash: 5d81e0193fb042993452ed314e70fb63573e615c
ms.sourcegitcommit: 6e83c55eb0450a3073ef2b95fa5f5bcb20dbbf89
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/28/2017
---
# <a name="testing-controller-logic-in-aspnet-core"></a>ASP.NET Core에서 컨트롤러 논리를 테스트합니다.

으로 [Steve Smith](https://ardalis.com/)

ASP.NET MVC 응용 프로그램의 컨트롤러는 작고 사용자 인터페이스 문제에 초점을 맞추고 이어야 합니다. UI가 아닌 문제를 처리 하는 큰 컨트롤러는 테스트 및 유지 관리 하기가 더 어렵습니다.

[GitHub에서 보거나 다운로드 샘플](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/controllers/testing/sample)

## <a name="testing-controllers"></a>테스트 컨트롤러

컨트롤러는 모든 ASP.NET Core MVC 응용 프로그램의 핵심 요소로입니다. 따라서 응용 프로그램에 대 한 의도 한 대로 동작 하므로 신뢰도 있어야 합니다. 자동화 된 테스트가이 신뢰도 제공할 수 있습니다 및 프로덕션에 도달 하기 전에 오류를 검색할 수 있습니다. 컨트롤러 내에서 불필요 한 책임을 배치 하지 컨트롤러 책임에만 테스트 포커스를 확인 하는 것이 유용 합니다.

컨트롤러 논리 최소화 해야 및 비즈니스 논리 나 인프라 문제나 (예를 들어 데이터 액세스)에 대해 수행 되지 않습니다. 테스트 컨트롤러 논리를 프레임 워크 없습니다. 테스트 방법 컨트롤러 *동작* 잘못 되었거나 잘못 된 입력에 기반 합니다. 수행 하는 비즈니스 작업의 결과에 따라 컨트롤러 응답을 테스트 합니다.

일반적인 컨트롤러 책임:

* 확인 `ModelState.IsValid`합니다.
* 경우에 대 한 오류 응답을 반환 `ModelState` 올바르지 않습니다.
* 지 속성 저장소에서 비즈니스 엔터티를 검색 합니다.
* 비즈니스 엔터티에서 작업을 수행 합니다.
* 지 속성을 비즈니스 엔터티를 저장 합니다.
* 적절 한 반환 `IActionResult`합니다.

## <a name="unit-testing"></a>유닛 테스트

[단위 테스트](https://docs.microsoft.com/dotnet/articles/core/testing/unit-testing-with-dotnet-test) 는 인프라와 종속성에서 격리 된 상태에서 응용 프로그램의 일부를 테스트 해야 합니다. 단위 테스트 컨트롤러 논리를 단일 작업의 내용만 검사 될 때, 프레임 워크 자체 또는 해당 종속 항목의 동작 하지 않습니다. 테스트 컨트롤러 작업 하면 단위로 동작에만 집중 있는지 확인 합니다. 등을 방지 하는 컨트롤러 단위 테스트 [필터](filters.md), [라우팅](../../fundamentals/routing.md), 또는 [모델 바인딩](../models/model-binding.md)합니다. 테스트 한 것에 초점을 맞추면, 단위 테스트는 일반적으로 쓰려고 쉽고 빠르게 실행 하는. 많은 오버 헤드 없이 자주 잘 작성 된 일련의 단위 테스트를 실행할 수 있습니다. 그러나 단위 테스트 문제 검색 하지 않는 구성 요소 간의 상호 작용의 용도 [통합 테스트](xref:mvc/controllers/testing#integration-testing)합니다.

단위 테스트를 사용자 지정 필터, 경로 등을 작성 하는 경우, 되지만 특정 컨트롤러 작업에서 테스트의 일부로 되지는 않습니다. 격리 된 상태에서 테스트 해야 합니다.

> [!TIP]
> [만들기 및 Visual Studio와 함께 단위 테스트 실행](https://www.visualstudio.com/docs/code/create-and-run-unit-tests-vs)합니다.

단위 테스트를 보여 주기 위해 다음 컨트롤러를 검토 합니다. 브레인스토밍 세션의 목록을 표시 하 고 새로운 브레인스토밍 POST를 사용 하 여 만들어야 하는 세션을 허용 합니다.

[!code-csharp[Main](testing/sample/TestingControllersSample/src/TestingControllersSample/Controllers/HomeController.cs?highlight=12,16,21,42,43)]

컨트롤러는 다음의 [명시적 종속 관계 원칙](http://deviq.com/explicit-dependencies-principle/)의 인스턴스를 제공 하는 종속성 주입 예상 `IBrainstormSessionRepository`합니다. 이렇게 하면 like는 모의 개체 프레임 워크를 사용 하 여 테스트를 매우 쉽게 [Moq](https://www.nuget.org/packages/Moq/)합니다. `HTTP GET Index` 메서드는 루프 또는 분기 및 유일한 호출 한 메서드가 없습니다. 이 테스트 하려면 `Index` 메서드를 여부를 확인 해야 하는 `ViewResult` 반환 되 면와 `ViewModel` 저장소의에서 `List` 메서드.

[!code-csharp[Main](testing/sample/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/HomeControllerTests.cs?highlight=17-18&range=1-33,76-95)]

`HomeController` `HTTP POST Index` 메서드 (위에 표시)을 확인 해야 합니다.

* 동작 메서드는 잘못 된 요청 반환 `ViewResult` 적절 한 데이터와 함께 때 `ModelState.IsValid` 은`false`

* `Add` 리포지토리의 메서드는 및 `RedirectToActionResult` 올바른 인수와 함께 반환 됩니다 때 `ModelState.IsValid` 가 true입니다.

잘못 된 모델 상태를 사용 하 여 오류를 추가 하 여 테스트할 수 `AddModelError` 아래 첫 번째 테스트와 같이 합니다.

[!code-csharp[Main](testing/sample/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/HomeControllerTests.cs?highlight=8,15-16,37-39&range=35-75)]

첫 번째 테스트를 확인 하는 경우 `ModelState` 유효 하지 않을 경우 동일한 `ViewResult` 에 대 한 반환 되는 `GET` 요청 합니다. 테스트 하지 않는 잘못 된 모델에 전달 하려고 하는 참고 합니다. 작동 하지 않습니다 모델 바인딩 실행 되 고 있지 않습니다 (경우에 [통합 테스트](xref:mvc/controllers/testing#integration-testing) 연습 모델 바인딩을 사용). 이 경우 모델 바인딩을 테스트 중입니다. 이러한 단위 테스트 작업 메서드의 코드에서에서 수행 하는 작업 테스트만 합니다.

두 번째 테스트 확인 때 `ModelState` 올바른지, 새 `BrainstormSession` (리포지토리)를 통해 추가 메서드에서 반환는 `RedirectToActionResult` 예상된 속성을 사용 합니다. 모의 호출 되지 호출은 일반적으로 무시 되었지만 호출 `Verifiable` 설치 프로그램의 끝에 호출 수 있도록 테스트에서 확인할 수 있습니다. 호출 하 여 그렇게 `mockRepo.Verify`, 예상 되는 메서드를 호출 하지 않았습니다 테스트가 실패 합니다.

> [!NOTE]
> 이 샘플에 사용 된 Moq 라이브러리를 사용 비안정형 모의 개체 ("느슨한" mocks 또는 스텁 라고도 함)를 사용한 확인할 또는 "strict" mocks 혼합할 수 있습니다. 에 대 한 자세한 내용은 [Moq 모의 동작을 사용자 지정](https://github.com/Moq/moq4/wiki/Quickstart#customizing-mock-behavior)합니다.

응용 프로그램의 다른 컨트롤러 브레인스토밍 특정 세션에 관련 된 정보를 표시 합니다. 잘못 된 id 값으로 처리 하는 일부 논리를 포함 합니다.

[!code-csharp[Main](./testing/sample/TestingControllersSample/src/TestingControllersSample/Controllers/SessionController.cs?highlight=19,20,21,22,25,26,27,28)]

컨트롤러 동작을 테스트 하려면 각각에 대해 하나씩 세 개의 사례에 `return` 문:

[!code-csharp[Main](testing/sample/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/SessionControllerTests.cs?highlight=27,28,29,46,47,64,65,66,67,68)]

웹 API (브레인스토밍 세션 및 세션에 새로운 아이디어를 추가 하는 방법을 관련 된 아이디어 목록) 기능을 노출 하는 응용 프로그램:

<a name=ideas-controller></a>

[!code-csharp[Main](testing/sample/TestingControllersSample/src/TestingControllersSample/Api/IdeasController.cs?highlight=21,22,27,30,31,32,33,34,35,36,41,42,46,52,65)]

`ForSession` 메서드 목록을 반환 `IdeaDTO` 형식입니다. API 호출을 통해 직접 비즈니스 도메인 엔터티를 반환 하지 않도록, 자주 이후 API 클라이언트 걸리며 외부적으로 노출 하는 API와 응용 프로그램의 내부 도메인 모델 불필요 하 게 결합 것 보다 많은 데이터 변경 내용이 해당 됩니다. 도메인 엔터티 및 연결을 통해 돌아갑니다 형식 간의 매핑을 수동으로 수행할 수 있습니다 (LINQ를 사용 하 여 `Select` 다음과 같이)와 같은 라이브러리를 사용 하 여 또는 [AutoMapper](https://github.com/AutoMapper/AutoMapper)

에 대 한 단위 테스트는 `Create` 및 `ForSession` API 메서드:

[!code-csharp[Main](testing/sample/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?highlight=18,23,29,33,38-39,43,50,58-59,68-70,76-78&range=1-83,121-135)]

메서드의 동작을 테스트 하려면 이전에 설명한 것 처럼 때 `ModelState` 유효 하지 않을 경우 컨트롤러에는 테스트의 일부로 모델 오류를 추가 합니다. 모델 유효성 검사 또는 모델 바인딩 단위 테스트에서 테스트-방금 특정으로 하는 경우 작업 메서드의 동작을 테스트 하지 마십시오 `ModelState` 값입니다.

두 번째 테스트는 모의 리포지토리에서 null을 반환 하도록 구성 되어 있으므로 null을 반환 하는 저장소에 따라 달라 집니다. 테스트 데이터베이스를 만들 필요가 없습니다 (메모리에 또는 그렇지 않은 경우)이이 결과 반환 하는 쿼리를 작성 및-표시 된 것 처럼 단일 문으로 수행할 수 있습니다.

마지막 테스트를 확인 하는 저장소의 `Update` 메서드를 호출 합니다. 모의로 호출 되 고 이전에 수행한 것 처럼 `Verifiable` 와 다음 모의 리포지토리의 `Verify` 메서드는 안정형 메서드가 실행 되었음을 확인 합니다. 단위 테스트 과제는에 없으면는 `Update` 메서드 데이터를 저장, 통합 테스트와 함께 수행할 수 있습니다.

## <a name="integration-testing"></a>통합 테스트

[통합 테스트](../../testing/integration-testing.md) 올바르게 함께 응용 프로그램 작업 내에서 별도 모듈을 보장 하기 위해 수행 됩니다. 일반적으로 모든 단위 테스트와 함께 테스트할 수 있습니다를 테스트할 수도 있습니다는 통합 테스트와 함께 하지만 반대의 제한이 적용 되지 않습니다. 그러나 통합 테스트에 단위 테스트 보다 느릴 수는 있습니다. 따라서 모든 단위 테스트 하는 여러 공동 작업자와 관련 된 시나리오에 대 한 통합 테스트를 사용 하 여 테스트 하는 것이 좋습니다.

여전히 유용 하지만, 모의 개체는 통합 테스트에서 거의 사용 됩니다. 단위 테스트를 모의 개체는 테스트의 용도 대 한 테스트 하는 단위 외부 공동 작업자 해야 작동 하는 방식을 제어 하는 효과적인 방법. 통합 테스트에서 실제 공동 작업자 전체 하위 시스템 작동 함께 제대로 확인 하는 데 사용 됩니다.

### <a name="application-state"></a>응용 프로그램 상태

한 중요 한 고려 사항은 통합 테스트를 수행 하는 경우에 응용 프로그램 상태를 설정 하는 방법입니다. 각 테스트에서 알려진된 상태로 응용 프로그램으로 시작 해야 하므로 테스트를 서로 독립적으로 실행 해야 합니다. 응용 프로그램 데이터베이스를 사용 하지 못하거나 모든 지 속성은 갖지, 문제가 아닐 수 있습니다. 그러나 대부분의 실제 응용 프로그램 데이터 저장소를 다시 설정 하지 않는 한 변경 내용은 모두 하나의 테스트 하 여 다른 테스트 영향을 줄 수 있으므로 상태 일부 종류의 데이터 저장소로 유지 합니다. 기본 제공을 사용 하 여 `TestServer`, 작업은 통합이 테스트에서는 내에서 호스트 ASP.NET Core 응용 프로그램에 매우 간단 하지만 반드시 사용 데이터에 대 한 액세스를 부여 하지 하 합니다. 실제 데이터베이스를 사용 하는 경우 한 가지 방법은 테스트 데이터베이스에 연결 하는 앱을 각 테스트를 실행 하기 전에 알려진 상태로 다시 설정 됩니다 테스트에 액세스 하 고 확인 수 있습니다.

이 샘플 응용 프로그램에서 사용 하 고 Entity Framework Core InMemoryDatabase 지원, 테스트 프로젝트 내에서 것에 연결할 수 있도록 합니다. 노출 대신는 `InitializeDatabase` 응용 프로그램의 메서드에서 `Startup` 에 있으면 응용 프로그램 시작 될 때 호출 하는 클래스는 `Development` 환경입니다. 통합 테스트에서이 환경으로 설정 것으로 자동으로 활용 `Development`합니다. InMemoryDatabase은 앱이 다시 시작 될 때마다 다시 설정한 후 데이터베이스를 다시 설정에 대해 걱정 하지 않아도 됩니다.

`Startup` 클래스:

[!code-csharp[Main](testing/sample/TestingControllersSample/src/TestingControllersSample/Startup.cs?highlight=19,20,34,35,43,52)]

표시는 `GetTestSession` 아래 통합 테스트에서 자주 사용 하는 방법입니다.

### <a name="accessing-views"></a>보기에 액세스

각 통합 테스트 클래스에 구성 된 `TestServer` ASP.NET Core 응용 프로그램 실행 합니다. 기본적으로 `TestServer` 실행 중인 위치-이 경우 테스트 프로젝트 폴더는 폴더에 웹 응용 프로그램 호스트 합니다. 따라서 하려고 하면 테스트 컨트롤러 작업을 반환 하는 `ViewResult`,이 오류가 표시 될 수 있습니다.

```
The view 'Index' was not found. The following locations were searched:
(list of locations)
```

이 문제를 해결 하려면 테스트 중인 프로젝트에 대 한 보기를 찾을 수 있도록 서버의 콘텐츠 루트를 구성 해야 합니다. 호출 하 여 이렇게 `UseContentRoot` 에 `TestFixture` 아래에 표시 된 클래스:

[!code-csharp[Main](testing/sample/TestingControllersSample/tests/TestingControllersSample.Tests/IntegrationTests/TestFixture.cs?highlight=30,33)]

`TestFixture` 클래스는 구성 및 만들기는 `TestServer`, 설정 하는 `HttpClient` 와 통신 하는 `TestServer`합니다. 사용 하 여 각 통합 테스트는 `Client` 속성을 테스트 서버에 연결 하 고 요청을 만드세요.

[!code-csharp[Main](testing/sample/TestingControllersSample/tests/TestingControllersSample.Tests/IntegrationTests/HomeControllerTests.cs?highlight=20,26,29,30,31,35,38,39,40,41,44,47,48)]

위의 첫 번째 테스트에는 `responseString` 실제 예상된 결과 포함 되어 있는지 확인 하는 감사 될 수 있는 뷰에서 HTML 렌더링을 보유 합니다.

두 번째 테스트 고유한 세션 이름 포함 하는 폼 POST를 생성 하 고, 앱에 게시 다음 예상된 리디렉션이 반환 되는지 확인 합니다.

### <a name="api-methods"></a>API 메서드

응용 프로그램 웹 것 것 테스트 자동화 하는 것을 예상 대로 실행 확인 Api를 노출할 경우. 기본 제공 `TestServer` 웹 Api를 테스트 하기가 쉬워졌습니다. 항상 확인 해야 API 메서드를 바인딩하는 모델을 사용 하는 경우 `ModelState.IsValid`, 이며 통합 테스트는 적절 한 위치 하 여 모델 유효성 검사가 제대로 작동 하는지 확인 합니다.

다음과 같은 테스트 대상 집합이 `Create` 에서 메서드는 [IdeasController](xref:mvc/controllers/testing#ideas-controller) 위에 표시 된 클래스:

[!code-csharp[Main](testing/sample/TestingControllersSample/tests/TestingControllersSample.Tests/IntegrationTests/ApiIdeasControllerTests.cs)]

HTML 뷰를 반환 하는 동작의 통합 테스트를 달리 API 웹 메서드 결과 반환 하는 일반적으로 deserialize 할 수 강력한 형식의 개체로 위의 마지막 테스트와 같이 합니다. 테스트 결과를 역직렬화 하는 경우에 `BrainstormSession` 인스턴스를 선택한 아이디어 아이디어의 해당 컬렉션에 올바르게 추가 되었는지 확인 합니다.

이 문서에서 통합 테스트의 추가 예제를 찾을 수 [샘플 프로젝트](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/controllers/testing/sample)합니다.
