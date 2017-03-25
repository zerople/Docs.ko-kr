---
title: "필터 | Microsoft 문서"
author: ardalis
description: "자세한 방법을 *필터* 작업 및 ASP.NET 핵심 MVC에서 사용 하는 방법입니다."
keywords: "ASP.NET Core, 필터, mvc 필터, 작업 필터, 권한 부여 필터, 자원 필터, 결과 필터, 예외 필터"
ms.author: tdykstra
manager: wpickett
ms.date: 12/12/2016
ms.topic: article
ms.assetid: 531bda08-aa5b-4471-8f08-96add22c8683
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/controllers/filters
translationtype: Machine Translation
ms.sourcegitcommit: 010b730d2716f9f536fef889bc2f767afb648ef4
ms.openlocfilehash: 9672d56d69dbb13ab502d0a722a6d436b5a125e4
ms.lasthandoff: 03/23/2017

---

# <a name="filters"></a>필터

여 [Tom Dykstra](https://github.com/tdykstra/) 및 [Steve Smith](http://ardalis.com)

*필터* ASP.NET 핵심 MVC의 요청 처리 파이프라인의 특정 단계 전후에 코드를 실행할 수 있도록 합니다.

 모든 요청이 HTTPS 및 응답 캐싱 (캐시 된 응답을 반환 하려면 요청 파이프라인 단락 (short-circuiting))를 사용 하는 확인 하는 권한 부여 (사용자에 대 한 권한이 없는 리소스에 대 한 액세스를 방지)와 같은 기본 제공 필터 핸들 작업입니다. 

응용 프로그램에 대 한 문제를 처리 하는 사용자 지정 필터를 만들 수 있습니다. 코드 작업에서 중복 되지 않도록 하려면 언제 든 지 필터는 솔루션입니다. 예를 들어 예외 필터에서 오류 처리 코드를 통합할 수 있습니다.

[보기 또는 GitHub에서 샘플을 다운로드](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/controllers/filters/sample)합니다.

## <a name="how-do-filters-work"></a>필터는 어떻게 작동 하는가?

내에서 실행 되는 필터는 *MVC 동작 호출 파이프라인*때로는 라고 하는 *필터 파이프라인*합니다.  필터 파이프라인에는 MVC가 실행할 작업을 선택한 후 실행 합니다.

![요청은 다른 미들웨어, 라우팅 미들웨어, 작업 선택 및 MVC 동작 호출 파이프라인을 통해 처리 됩니다. 작업 선택, 라우팅 미들웨어 및 기타 다양 한 미들웨어를 통해 다시 응답이 클라이언트로 전송 되기 전에 요청 처리를 계속 합니다.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a>필터 형식

각 필터 유형 필터 파이프라인의 다른 단계에서 실행 됩니다.

* [권한 부여 필터](#authorization-filters) 처음으로 실행 하 고 현재 사용자가 현재 요청에 대 한 권한이 있는지 여부를 결정 하는 데 사용 됩니다. 요청은 권한이 없을 경우 파이프라인 효율적으로 진행할 수 있습니다. 

* [자원 필터](#resource-filters) 권한 부여 후 요청을 처리 하기부터 됩니다.  필터 파이프라인 및 파이프라인의 나머지 완료 된 후 나머지 앞에 코드를 실행할 수 있습니다. 캐싱을 구현 또는 그렇지 않은 경우 성능상의 이유로 필터 파이프라인 단락 (short-circuit)에 유용 합니다. 모델 바인딩 전에 실행 하기 때문에 모델 바인딩에 영향을 주는 필요한 부분을 유용 합니다.

* [작업 필터](#action-filters) 하기 직전 및 개별 작업 메서드를 호출한 후에 코드를 실행할 수 있습니다. 동작으로 전달 된 인수 및 작업에서 반환 된 결과 조작에 사용할 수 있습니다.

* [예외 필터](#exception-filters) 아무것도 응답 본문에 쓰여지기 전에 발생 하는 처리 되지 않은 예외에 전역 정책을 적용 하는 데 사용 됩니다.

* [필터 결과](#result-filters) 직전 및 개별 작업 결과 실행 한 후에 코드를 실행할 수 있습니다. 작업 메서드가 성공적으로 실행 하는 경우에 실행 하며 뷰 또는 포맷터 실행 묶어야 하는 논리에 유용 합니다.

다음 다이어그램에서는 이러한 종류의 필터는 필터 파이프라인에서 상호 작용 하는 방법을 보여 줍니다.

![요청 권한 부여 필터, 자원 필터, 모델 바인딩, 동작 필터, 작업이 실행 되 면 작업 결과가 변환, 예외 필터, 결과 및 필터 결과 실행을 통해 처리 됩니다. Out 길에 요청은만 결과 필터 및 리소스 필터에서 응답이 클라이언트로 전송 되기 전에 처리 됩니다.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a>구현

필터에는 다른 인터페이스 정의 통해 동기 및 비동기 구현을 지원 합니다. 수행 해야 하는 작업의 종류에 따라 동기화 또는 비동기 variant를 선택 합니다. 

전후에 정의 된 해당 파이프라인 단계를 실행할 수 있는 동기 필터 코드 모두*단계*Executing 및*단계*메서드를 실행 합니다. 예를 들어 `OnActionExecuting` 작업 메서드를 호출 하기 전에 호출 됩니다 및 `OnActionExecuted` 작업 메서드가 반환 된 후 호출 됩니다.

[!code-csharp[주](./filters/sample/src/FiltersSample/Filters/SampleActionFilter.cs?highlight=6,8,13)]

비동기 필터에는 단일 정의*단계*ExecutionAsync 메서드. 이 메서드는 *FilterType*ExecutionDelegate 대리자 필터의 파이프라인 단계를 실행 합니다. 예를 들어 `ActionExecutionDelegate` 호출 작업 메서드를 실행할 수 있습니다 코드 전후 프로시저를 호출 합니다.

[!code-csharp[주](./filters/sample/src/FiltersSample/Filters/SampleAsyncActionFilter.cs?highlight=6,8-10,13)]

단일 클래스에 여러 필터 단계에 대 한 인터페이스를 구현할 수 있습니다. 예를 들어는 [ActionFilterAttribute](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.filters.actionfilterattribute) 모두 추상 클래스 구현 `IActionFilter` 및 `IResultFilter`와 비동기가에 해당 합니다.

> [!NOTE]
> 구현 **어느** 동기 또는 비동기 버전을 둘 다는 필터 인터페이스입니다. 프레임 워크를 필터 비동기 인터페이스를 구현 하 고, 호출 하는 경우 먼저 확인 합니다. 그렇지 않으면 동기 인터페이스의 메서드를 호출 합니다. 하나의 클래스에 두 인터페이스를 구현 하는 경우, 비동기 메서드만 호출 됩니다. 와 같은 추상 클래스를 사용 하는 경우 [ActionFilterAttribute](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.filters.actionfilterattribute) 동기 메서드만 또는 각 필터 형식에 대 한 비동기 메서드를 재정의 합니다.

### <a name="ifilterfactory"></a>IFilterFactory

`IFilterFactory`는 `IFilter`를 구현합니다. 따라서는 `IFilterFactory` 으로 인스턴스를 사용할 수는 `IFilter` 필터 파이프라인의 모든 위치에서 인스턴스. 으로 캐스팅 하려고 시도 하는 필터를 호출 하는 프레임 워크를 준비 하는 경우는 `IFilterFactory`합니다. 해당 캐스트에 성공 하면는 `CreateInstance` 메서드는 만들기는 `IFilter` 호출 되는 인스턴스. 정확한 필터 파이프라인 응용 프로그램이 시작 될 때 명시적으로 설정 하지 않아도 되므로 매우 유연한 디자인을 제공 합니다.

구현할 수 있습니다 `IFilterFactory` 필터를 만드는 다른 방법으로 사용자 고유의 특성 구현 합니다.

[!code-csharp[주](./filters/sample/src/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

### <a name="built-in-filter-attributes"></a>기본 제공 필터 특성

프레임 워크 하위 클래스를 할 수 있는 기본 제공 특성 기반 필터를 포함 하 고 사용자 지정 합니다. 예를 들어, 다음과 같은 결과 필터 응답에 대 한 헤더를 추가 합니다.

<a name=add-header-attribute></a>

[!code-csharp[주](./filters/sample/src/FiltersSample/Filters/AddHeaderAttribute.cs?highlight=5,16)]

위의 예제와 같이 인수를 수락 하는 필터를 허용 하는 특성입니다. 컨트롤러 또는 작업 메서드에이 특성을 추가 하는 이름 및 HTTP 헤더의 값을 지정 합니다.

[!code-csharp[주](./filters/sample/src/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

결과 `Index` 작업 아래에 표시 되어-응답 헤더의 오른쪽 아래에 표시 됩니다.

![개발자 도구의 Microsoft Edge 작성자 Steve Smith를 포함 하 여 응답 헤더를 표시 합니다.@ardalis](filters/_static/add-header.png)

여러 필터 인터페이스 사용자 지정 구현을 위한 기본 클래스로 사용할 수 있는 해당 특성을 가집니다.

특성을 필터링 합니다.

* `ActionFilterAttribute`
* `ExceptionFilterAttribute`
* `ResultFilterAttribute`
* `FormatFilterAttribute`
* `ServiceFilterAttribute`
* `TypeFilterAttribute`

`TypeFilterAttribute`및 `ServiceFilterAttribute` 설명 [이 문서의 뒷부분에 나오는](#dependency-injection)합니다.

## <a name="filter-scopes-and-order-of-execution"></a>필터 범위와 실행 순서

세 가지 중 하나에서 파이프라인에 필터를 추가할 수 있습니다 *범위*합니다. 특성을 사용 하 여 컨트롤러 클래스 또는 특정 작업 메서드에 필터를 추가할 수 있습니다. 하거나 추가 하 여 전역적으로 (에 대 한 모든 컨트롤러와 작업) 필터를 등록할 수는 `MvcOptions.Filters` 컬렉션에는 `ConfigureServices` 에서 메서드는 `Startup` 클래스:

[!code-csharp[주](./filters/sample/src/FiltersSample/Startup.cs?name=snippet_ConfigureServices&highlight=5-8)]

### <a name="default-order-of-execution"></a>기본 실행 순서

파이프라인의 특정 단계에 대 한 여러 개의 필터가 있을 경우 범위는 기본 필터 실행 순서를 결정 합니다.  전역 필터를 차례로 메서드 필터를 포함 하는 클래스 필터를 묶습니다. 이 라고도으로 "러시아어 인형" 중첩 범위에 하나씩 늘어날 때마다 주위에 래핑된 이전 범위와 같은 한 [중첩 인형](https://en.wikipedia.org/wiki/Matryoshka_doll)합니다. 일반적으로 명시적으로 순서를 결정 하지 않고도 원하는 재정의 동작을 얻습니다.

이 중첩 Asa 결과 *후* 의 역순으로 실행 되는 필터의 코드는 *전에* 코드입니다. 시퀀스는 다음과 같습니다.

* *전에* 전체적으로 적용 된 필터의 코드
  * *전에* 컨트롤러에 적용 된 필터의 코드
    * *전에* 작업 메서드에 적용 된 필터의 코드
    * *후* 작업 메서드에 적용 된 필터의 코드
  * *후* 컨트롤러에 적용 된 필터의 코드
* *후* 전체적으로 적용 된 필터의 코드
  
동기 작업 필터에 대 한 필터 메서드를 호출 하는 순서를 보여 주는 예제는 다음과 같습니다.

| Sequence | 필터 범위 | Filter 메서드 |
|:--------:|:------------:|:-------------:|
| 1 | Global | `OnActionExecuting` |
| 2 | 컨트롤러 | `OnActionExecuting` |
| 3 | 메서드 | `OnActionExecuting` |
| 4 | 메서드 | `OnActionExecuted` |
| 5 | 컨트롤러 | `OnActionExecuted` |
| 6 | Global | `OnActionExecuted` |

이 시퀀스 메서드 필터 컨트롤러 필터 내에 중첩 된 컨트롤러 필터 전역 필터 내에 중첩 되어 표시 됩니다. 배치 하 라는 다른 방식으로 async 필터 내부 라면의 온*단계*ExecutionAsync 메서드를 더 엄격 하 게 범위를 사용 하 여 필터의 모든 코드를 실행 스택에 합니다.

> [!NOTE]
> 상속 하는 모든 컨트롤러는 `Controller` 기본 클래스에 포함 되어 `OnActionExecuting` 및 `OnActionExecuted` 메서드. 이러한 메서드를 실행 하는 지정된 된 동작에 대 한 필터를 래핑할: `OnActionExecuting` 있는 필터 보다 먼저 호출 됩니다 및 `OnActionExecuted` 후 필터를 모두 호출 됩니다.

### <a name="overriding-the-default-order"></a>기본 순서를 재정의합니다.

구현 하 여 실행의 기본 시퀀스를 재정의할 수 있습니다 `IOrderedFilter`합니다. 이 인터페이스를 노출 한 `Order` 실행의 순서를 결정 하는 범위에 대해 우선 순위를 사용 하는 속성입니다. 낮은 필터 `Order` 값을 갖습니다 해당 *전에* 코드를 더 높은 값으로 필터 보다 먼저 실행 `Order`합니다. 낮은 필터 `Order` 값을 갖습니다 해당 *후* 그 필터를 더 높은 실행 코드 `Order` 값입니다. 설정할 수는 `Order` 생성자 매개 변수를 사용 하 여 속성:

```csharp
[MyFilter(Name = "Controller Level Attribute", Order=1)]
```

있는 경우 동일한 첫 번째 집합 하지만 위의 예제에 표시 된 3 작업 필터는 `Order` 컨트롤러 및 글로벌 속성 각각 1과 2에 필터링, 실행의 순서를 바꿀 수는 있습니다.

| Sequence | 필터 범위 | `Order` 속성 | Filter 메서드 |
|:--------:|:------------:|:-----------------:|:-------------:|
| 1 | 메서드 | 0 | `OnActionExecuting` |
| 2 | 컨트롤러 | 1  | `OnActionExecuting` |
| 3 | Global | 2  | `OnActionExecuting` |
| 4 | Global | 2  | `OnActionExecuted` |
| 5 | 컨트롤러 | 1  | `OnActionExecuted` |
| 6 | 메서드 | 0  | `OnActionExecuted` |

`Order` 속성 필터를 실행할 순서를 결정할 때 범위 이기는 합니다. 필터 순서에 따라 먼저 정렬 됩니다 다음 범위는 결속을 끊을 하는 데 사용 됩니다. 기본 제공 필터를 모두 구현 `IOrderedFilter` 기본 설정 및 `Order` 값을 0으로 설정 하지 않으면 범위 순서를 결정 하므로 `Order` 0이 아닌 값으로.

## <a name="cancellation-and-short-circuiting"></a>취소 및 짧은 circuiting

설정 하 여 언제 든 지 필터 파이프라인 효율적으로 진행할 수 있습니다는 `Result` 속성에는 `context` 필터 메서드에 제공 된 매개 변수입니다. 예를 들어, 다음과 같은 리소스 필터 실행 파이프라인의 나머지를 방지합니다.

<a name=short-circuiting-resource-filter></a>

[!code-csharp[주](./filters/sample/src/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?highlight=12,13,14,15)]

다음 코드에서 모두는 `ShortCircuitingResourceFilter` 및 `AddHeader` 필터 대상의 `SomeResource` 작업 메서드. 그러나 때문에 `ShortCircuitingResourceFilter` 첫 번째 실행 하 고 파이프라인의 나머지 short-circuits는 `AddHeader` 필터에 대 한 실행 되지는 `SomeResource` 동작 합니다. 이 문제는 동일 필터에 모두 제공 하는 작업 메서드 수준에서 적용 된 경우는 `ShortCircuitingResourceFilter` 첫 번째 실행 합니다.

[!code-csharp[주](./filters/sample/src/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1,9)]

## <a name="dependency-injection"></a>종속성 주입

유형 또는 인스턴스가 필터를 추가할 수 있습니다. 인스턴스를 추가 하는 경우 해당 인스턴스의 모든 요청에 대해 사용 됩니다. 형식에 추가 하면 됩니다 형식 활성화, 즉 각 요청에 대 한 인스턴스를 만들 수는 고 생성자 종속성 채워집니다 [종속성 주입](../../fundamentals/dependency-injection.md) (DI). 해당 하는 형식으로 필터를 추가 `filters.Add(new TypeFilterAttribute(typeof(MyFilter)))`합니다.

특성으로 구현 되며 컨트롤러 클래스 또는 작업 메서드를 직접 추가 하는 필터에서 제공 하는 생성자 종속성을 가질 수 없습니다 [종속성 주입](../../fundamentals/dependency-injection.md) (DI). 특성에는 적용 되는 위치를 제공 하는 해당 생성자 매개 변수를 사용 해야 합니다. 때문입니다. 특성이 작동 하는 방법의 제한 사항입니다.

필터에 DI에서 액세스 해야 하는 종속성이 있으면 방법은 여러 가지 지원 합니다. 다음 중 하나를 사용 하 여 클래스 또는 동작 메서드에 필터를 적용할 수 있습니다.

* `ServiceFilterAttribute`
* `TypeFilterAttribute`
* `IFilterFactory`특성에 구현

> [!NOTE]
> 한 종속성 DI에서 가져오려는 수로 거입니다. 그러나 만들거나 사용 하지 않도록 필터 로깅 용도로 이후에 [기본 제공 프레임 워크 로깅 기능이](../../fundamentals/logging.md) 필요한 이미 제공 될 수 있습니다. 필터에 로깅을 추가 하려는 경우 비즈니스 도메인 문제나 또는 필터를 보다는 MVC 작업이 나 다른 프레임 워크 이벤트에 대 한 동작에 집중 해야 합니다.

### <a name="servicefilterattribute"></a>ServiceFilterAttribute

A `ServiceFilter` DI에서 필터의 인스턴스를 검색 합니다. 컨테이너에 필터를 추가 `ConfigureServices`에서 참조 하는 `ServiceFilter` 특성

[!code-csharp[주](./filters/sample/src/FiltersSample/Startup.cs?name=snippet_ConfigureServices&highlight=11)]

[!code-csharp[주](../../mvc/controllers/filters/sample/src/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

사용 하 여 `ServiceFilter` 예외에서 필터 형식 결과 등록 하지 않고 있습니다.

```
System.InvalidOperationException: No service for type
'FiltersSample.Filters.AddHeaderFilterWithDI' has been registered.
```

`ServiceFilterAttribute`구현 `IFilterFactory`, 단일를 만들기 위한 메서드를 노출 하는 `IFilter` 인스턴스. 경우 `ServiceFilterAttribute`, `IFilterFactory` 인터페이스의 `CreateInstance` 서비스 컨테이너 (DI)에서 지정된 된 형식을 로드 하도록 메서드를 구현 합니다.

### <a name="typefilterattribute"></a>TypeFilterAttribute

`TypeFilterAttribute`매우 비슷합니다 `ServiceFilterAttribute` (도 구현 하 고 `IFilterFactory`), 하지만 DI 컨테이너에서 직접 해당 형식을 확인할 수 없습니다. 사용 하 여 형식을 인스턴스화하여 대신 `Microsoft.Extensions.DependencyInjection.ObjectFactory`합니다.

형식 사용 하 여 참조 되는 이러한 차이로 인해는 `TypeFilterAttribute` 먼저 컨테이너를 사용 하 여 등록할 필요가 없습니다 (하지만 계속 해 서 컨테이너에서 해당 종속성). 또한 `TypeFilterAttribute` 생성자 인수를 형식에 대 한 필요에 따라 허용할 수 있습니다. 다음 예제에는 인수를 사용 하 여 형식을 전달 하는 방법을 보여 줍니다 `TypeFilterAttribute`:

[!code-csharp[주](../../mvc/controllers/filters/sample/src/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]

경우 필터는 인수가 필요 하지 않은 하지만 DI로 채워지도록 해야 하는 생성자 종속성,이 사용할 수 있습니다 명명 된 사용자 지정 특성 클래스 및 메서드 대신에 `[TypeFilter(typeof(FilterType))]`). 다음과 같은 필터가 구현할 수 있는 방법을 보여 줍니다.

[!code-csharp[주](./filters/sample/src/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

이 필터를 사용 하 여 메서드나 클래스에 적용할 수는 `[SampleActionFilter]` 구문을 사용 하는 대신 `[TypeFilter]` 또는 `[ServiceFilter]`합니다.

## <a name="authorization-filters"></a>권한 부여 필터

*권한 부여 필터* 작업 메서드에 대 한 액세스를 제어 하 고 첫 번째 필터를 필터 파이프라인 내에서 실행 됩니다. 만 있는지는 메서드를 지 원하는 메서드 전후 대부분 필터와 달리 앞입니다. 만 작성 해야 사용자 지정 권한 부여 필터 고유한 권한 부여 프레임 워크를 작성 하는 경우. 권한 부여 정책을 구성 또는 사용자 지정 필터를 작성 하는 대신 사용자 지정 권한 부여 정책 작성을 선호 합니다. 권한 부여 시스템 호출에 대 한 기본 제공 필터를 구현 하면 방금 담당 합니다.

예외를 처리 합니다 nothing 때문에 권한 부여 필터 내에서 예외 throw 하지 않아야 참고 (예외 필터 처리할 수 없는 해당). 대신, 챌린지를 실행 하거나 다른 해결 방법의 찾을 합니다.

에 대 한 자세한 내용은 [권한 부여](../../security/authorization/index.md)합니다.

## <a name="resource-filters"></a>자원 필터

*자원 필터* 구현 중 하나는 `IResourceFilter` 또는 `IAsyncResourceFilter` 인터페이스 및 해당 실행 필터 파이프라인의 대부분을 래핑합니다. (만 [권한 필터](#authorization-filters) 전에 실행 됩니다.) 리소스 필터는 요청을 수행 하는 작업의 대부분을 단락 (short-circuit) 하는 경우에 특히 유용 합니다. 예를 들어 응답은 캐시에 이미 캐싱 필터 파이프라인의 나머지를 피할 수 있습니다.

[짧은 circuiting 리소스 필터](#short-circuiting-resource-filter) 리소스 필터의 예로 앞서 살펴본 합니다. 또 다른 예로 [DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/dev/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs), 모델 바인딩 양식 데이터에 액세스할 수 없습니다. 대형 파일 업로드를 받고 메모리로 읽어 폼 않을 것을 알고 있는 경우에는 것이 유용 합니다.

## <a name="action-filters"></a>작업 필터

*작업 필터* 구현 중 하나는 `IActionFilter` 또는 `IAsyncActionFilter` 인터페이스 및 해당 실행 주위의 작업 메서드를 실행 합니다.

샘플 작업 필터는 다음과 같습니다.

[!code-csharp[주](./filters/sample/src/FiltersSample/Filters/SampleActionFilter.cs?name=snippet_ActionFilter)]

[ActionExecutingContext](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.filters.actionexecutingcontext) 다음 속성을 제공 합니다.

* `ActionArguments`-작업에 대 한 입력을 조작할 수 있습니다.
* `Controller`-컨트롤러 인스턴스를 조작할 수 있습니다. 
* `Result`-작업 메서드 및 후속 작업 필터의 실행을 short-circuits이 설정 합니다. 작업 메서드 및 후속 필터의 실행 수 없지만 대신 성공적인 결과 실패로 처리도 예외를 throw 됩니다.

[ActionExecutedContext](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.filters.actionexecutedcontext) 제공 `Controller` 및 `Result` 다음 속성도 있습니다.

* `Canceled`-작업 실행 된 처리가 단축 다른 필터에 의해 경우 true가 됩니다.
* `Exception`-작업 또는 후속 작업 필터는 예외가 발생 하는 경우 null이 아닌 됩니다. 이 속성을 효과적으로 null로 설정 합니다. '' 예외 처리, 및 `Result` 작업 메서드를 정상적으로 반환 된 경우 처럼 실행 됩니다.

에 대 한는 `IAsyncActionFilter`에 대 한 호출에서 `ActionExecutionDelegate` 모든 후속 작업 필터를 실행 하는 반환 된 작업 메서드는 `ActionExecutedContext`합니다. 단락 (short-circuit), 할당 `ActionExecutingContext.Result` 일부 결과에 인스턴스를 호출 하지 않으면는 `ActionExecutionDelegate`합니다.

프레임 워크는 추상 제공 `ActionFilterAttribute` 하위 클래스를 수 있습니다. 

## <a name="exception-filters"></a>예외 필터

*예외 필터* 구현 중 하나는 `IExceptionFilter` 또는 `IAsyncExceptionFilter` 인터페이스입니다. 이러한 일반적인 오류 처리 응용 프로그램에 대 한 정책을 구현 하는 데 사용 될 수 있습니다. 

다음 샘플 예외 필터 뷰는 사용자 지정 개발자 오류를 사용 하 여 응용 프로그램을 개발 하는 경우 발생 하는 예외에 대 한 세부 정보를 표시 합니다.

[!code-csharp[주](./filters/sample/src/FiltersSample/Filters/CustomExceptionFilterAttribute.cs?name=snippet_ExceptionFilter&highlight=1,14)]

예외 필터 없는 두 개의 이벤트 (이전에 대 한 후)-만 구현 `OnException` (또는 `OnExceptionAsync`). 

컨트롤러 만들기에서 발생 하는 처리 되지 않은 예외를 처리 하는 예외 필터 [모델 바인딩](../models/model-binding.md), 작업 필터 또는 작업 메서드에 있습니다. 리소스 필터, 결과 필터 또는 MVC 결과 실행에서 발생 하는 예외를 포착할 수 있게 하지 않습니다.

예외를 처리 하려면 설정의 `ExceptionContext.ExceptionHandled` 속성을 true 또는 응답을 작성 합니다. 그러면 예외 전파 되지 않습니다. 참고 예외 필터 "성공"으로 예외를 해제할 수 없습니다. 작업 필터만 할 수 있습니다.

> [!NOTE]
> ASP.NET 1.1에서 응답 전송 되지 않습니다 설정 하는 경우 `ExceptionHandled` true로 **및** 응답을 작성 합니다. 이 시나리오에서는 ASP.NET 핵심 1.0는 응답에 전송 하 고 ASP.NET 핵심 1.1.2 1.0 동작으로 반환 됩니다. 자세한 내용은 참조 [#5594 발급](https://github.com/aspnet/Mvc/issues/5594) GitHub 리포지토리에 있습니다. 

예외 필터는 MVC 동작 내에서 발생 하는 예외를 트래핑 하는 데 유용 하지만 오류 처리 미들웨어 것 만큼 유연 하지 않습니다. 미들웨어 일반적인 경우에만 해야 하는 필터를 사용 하 여 오류를 처리 및 *다르게* 기반으로 하는 MVC 동작을 선택 했습니다. 예를 들어 앱 API 끝점에 대해 및 보기/HTML에 대 한 작업 메서드를 가질 수 있습니다. 보기 기반 작업 오류 페이지를 HTML로 반환 하는 동안 API 끝점에서 JSON으로 오류 정보를 반환할 수 있습니다.

프레임 워크는 추상 제공 `ExceptionFilterAttribute` 하위 클래스를 수 있습니다. 

자동으로 상태 모델의 유효성을 검사 하 고 상태가 올바르지 않으면 오류를 반환 하는 작업 필터를 사용할 수 있습니다.

[!code-csharp[주](./filters/sample/src/FiltersSample/Filters/ValidateModelAttribute.cs)]

`OnActionExecuted` 메서드가 실행 후 동작 메서드와 수 보고를 통해 작업의 결과 조작는 `ActionExecutedContext.Result` 속성입니다. `ActionExecutedContext.Canceled`작업 실행 된 처리가 단축 다른 필터에 의해 하는 경우 true로 설정 됩니다. `ActionExecutedContext.Exception`작업 또는 후속 작업 필터는 예외가 발생 하는 경우 null이 아닌 값으로 설정 됩니다. 설정 `ActionExecutedContext.Exception` 효과적으로 null로 '' 예외 처리 및 `ActionExectedContext.Result` 작업 메서드를 정상적으로 반환 된 경우 처럼 실행할 수 있습니다.

## <a name="result-filters"></a>결과 필터

*필터 결과* 구현 중 하나는 `IResultFilter` 또는 `IAsyncResultFilter` 인터페이스 및 해당 실행 주위의 작업 결과 실행 합니다. 

HTTP 헤더를 추가 하는 결과 필터의 예를 들면 다음과 같습니다.

[!code-csharp[주](./filters/sample/src/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

실행 되 고 결과의 종류에 동작에 따라 달라 집니다. 뷰를 반환 하는 MVC 동작의 일부로 처리 하는 모든 razor 포함는 `ViewResult` 실행 되 고 있습니다. API 메서드는 결과의 실행의 일부로 일부 serialization을 수행할 수 있습니다. 에 대 한 자세한 내용은 [작업 결과](actions.md)

결과 필터 동작 또는 작업 필터는 작업 결과 생성할 때에 성공적인 결과 얻으려면-실행 됩니다. 예외 필터는 예외를 처리 하는 경우에 결과 필터 실행 되지 않습니다.

`OnResultExecuting` 메서드 효율적으로 진행할 수는 작업 결과 및 후속 결과 필터의 실행을 설정 하 여 `ResultExecutingContext.Cancel` true입니다. 일반적으로 빈 응답을 생성 하지 않도록 단락 (short-circuiting) 하는 경우 응답 개체를 작성 해야 합니다. 예외를 throw 하는 작업 결과 및 후속 필터 실행 되지 것입니다 있지만 대신 성공적인 결과 실패로 처리 됩니다.

경우는 `OnResultExecuted` 메서드 실행, 응답이 클라이언트에 전송 되어 가능성이 하 고 (아닌 경우 예외가 발생 했습니다) 더 이상 변경할 수 없습니다. `ResultExecutedContext.Canceled`작업 결과 실행 된 처리가 단축 다른 필터에 의해 하는 경우 true로 설정 됩니다.

`ResultExecutedContext.Exception`작업 결과 또는 후속 결과 필터 예외가 발생 하는 경우 null이 아닌 값으로 설정 됩니다. 설정 `Exception` 에 null 효과적으로 ''는 예외를 처리 하 고 MVC 파이프라인의 뒷부분에 나오는 예외가 다시 throw 되 고에서 예외를 방지 합니다. 결과 필터에서 예외를 처리 하는 경우 응답에 모든 데이터를 쓸 수 없습니다. 작업 결과 해당 실행을 통해 충족할 throw 하는 경우 헤더 이미 플러시될 때 클라이언트에 오류 코드를 전송 하는 신뢰할 수 있는 메커니즘이 있습니다.

에 대 한는 `IAsyncResultFilter` 에 대 한 호출 `await next()` 에 `ResultExecutionDelegate` 모든 후속 결과 필터를 실행 하는 작업 결과입니다. 단락 (short-circuit)을 설정한 `ResultExecutingContext.Cancel` 에 true 및 호출 하지 않으면는 `ResultExectionDelegate`합니다.

프레임 워크는 추상 제공 `ReesultFilterAttribute` 하위 클래스를 수 있습니다. [AddHeaderAttribute](#add-header-attribute) 앞에 표시 된 클래스는 결과 필터 특성의 예입니다.

## <a name="using-middleware-in-the-filter-pipeline"></a>미들웨어를 사용 하 여 필터 파이프라인

자원 필터 처럼 작동 [미들웨어](../../fundamentals/middleware.md) 는 파이프라인의 뒷부분에 제공 되는 모든 실행을 묶습니다. 하지만 필터 미들웨어 한다는 점에서 다릅니다 MVC 컨텍스트 및 구문에 액세스할 수 있는 즉 MVC의 일부입니다.

ASP.NET Core 1.1에서는 필터 파이프라인에서 미들웨어를 사용할 수 있습니다. 특정 컨트롤러 또는 작업 실행 되는 하나 또는 MVC 경로 데이터에 액세스 해야 하는 미들웨어 구성 요소를 설정한 경우 하는 작업을 수행 하는 것이 좋습니다.

미들웨어를 필터로 사용 하려면 형식 만들기는 `Configure` 필터 파이프라인에 주입 하려는 미들웨어를 지정 하는 메서드. 요청에 대 한 현재 문화권을 설정 하는 지역화 미들웨어를 사용 하는 예제는 다음과 같습니다.

[!code-csharp[주](./filters/sample/src/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,21)]

사용할 수는 `MiddlewareFilterAttribute` 선택한 컨트롤러나 동작에 대 한 미들웨어를 실행 하려면 또는 전역으로:

[!code-csharp[주](./filters/sample/src/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

모델 바인딩 전후 파이프라인의 나머지 리소스 필터는 필터 파이프라인의 단계에서 실행 되는 미들웨어 필터입니다.

## <a name="next-actions"></a>다음 작업

필터를 실험 하려면 [다운로드, 테스트 및 샘플을 수정](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/controllers/filters/sample)합니다.

