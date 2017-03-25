---
title: "ASP.NET MVC 코어의 컨트롤러와 요청을 처리 | Microsoft 문서"
author: ardalis
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 9da9eb52-8583-4069-af91-155ba3529d7f
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/controllers/actions
translationtype: Machine Translation
ms.sourcegitcommit: 010b730d2716f9f536fef889bc2f767afb648ef4
ms.openlocfilehash: f90f6b877d924d9ade89527e63cbb98b72c45ea9
ms.lasthandoff: 03/23/2017

---
# <a name="handling-requests-with-controllers-in-aspnet-mvc-core"></a>ASP.NET MVC 코어의 컨트롤러와 요청 처리

[Steve Smith](http://ardalis.com)

컨트롤러, 작업 및 작업 결과 개발자가 ASP.NET MVC 코어를 사용 하 여 앱을 작성 하는 방법의 핵심 부분입니다.

## <a name="what-is-a-controller"></a>컨트롤러는 무엇입니까

ASP.NET mvc에서는 *컨트롤러* 정의 하 고 일련의 작업을 그룹화 하는 데 사용 됩니다. *작업* (또는 *작업 메서드*)는 들어오는 요청을 처리 하는 컨트롤러에 메서드가 만들어집니다. 컨트롤러 수단을 제공 논리적 그룹화 이와 유사한 작업 함께 전체적으로 적용할 규칙 (예: 라우팅, 캐싱, 권한 부여)의 공통 집합을 허용 합니다. 들어오는 요청을 통해 작업에 매핑됩니다. [라우팅](routing.md)합니다.

ASP.NET Core MVC 컨트롤러 "컨트롤러"로 끝나는 또는 "컨트롤러"로 끝나는 클래스에서 상속 되는 모든 인스턴스화할 수 있는 클래스 될 수 있습니다. 컨트롤러 따라야는 [명시적 종속성 원칙](http://deviq.com/explicit-dependencies-principle) 해당 동작을 사용 하 여 생성자를 통해 수행 하는 모든 종속성을 요청 하 고 [종속성 주입](dependency-injection.md)합니다.

규칙에 따라 컨트롤러 클래스:

* 루트 수준 "컨트롤러" 폴더에 있습니다

* Microsoft.AspNetCore.Mvc.Controller에서 상속

이러한 두 규칙 필요 하지 않습니다.

모델-뷰-컨트롤러 패턴 내에서 컨트롤러는 초기 요청 처리와 모델의 인스턴스화 하는 일을 담당 합니다. 일반적으로 비즈니스 의사 결정 모델 내에서 수행 되어야 합니다.

> [!NOTE]
> 모델 이어야 합니다는 *CLR 개체 POCO (Plain Old)*이 아니라는 `DbContext` 또는 데이터베이스와 관련 된 형식입니다.

컨트롤러는 모델의 처리 (있는 경우)의 결과 사용해, 관련된 뷰 데이터와 함께 적절 한 뷰를 반환 합니다. 자세한 정보: [ASP.NET 핵심 MVC 개요](../overview.md) 및 [ASP.NET 핵심 MVC 및 Visual Studio 시작](../../tutorials/first-mvc-app/start-mvc.md)합니다.

>[!TIP]
> 컨트롤러는 한 *UI 수준* 추상화 합니다. 책임에는 들어오는 요청 데이터가 올바른지 확인 하 고 어떤 보기 (또는 API에 대 한 결과)를 반환할 선택할 수입니다. 잘 구성 된 응용 프로그램에서 직접 데이터 액세스 또는 비즈니스 논리를 포함 하지 않습니다 하지만 대신 이러한 책임을 처리 하는 서비스에 위임 합니다.

## <a name="defining-actions"></a>동작 정의

컨트롤러 종류 모든 공용 메서드는 작업입니다. 작업에 매개 변수는 데이터를 요청에 바인딩되고를 사용 하 여 [모델 바인딩](../models/model-binding.md)합니다.

>[!WARNING]
> 매개 변수를 허용 하는 작업 메서드에 확인 해야는 `ModelState.IsValid` 속성은 true입니다.

작업 메서드는 비즈니스 문제에 들어오는 요청을 매핑하기 위한 논리를 포함 해야 합니다. 일반적으로 비즈니스 문제나 컨트롤러를 통해 액세스 하는 서비스로 표시 되어야 [종속성 주입](dependency-injection.md)합니다. 그런 다음 작업 응용 프로그램 상태를 비즈니스 작업의 결과를 매핑합니다.
작업 아무 것도 반환할 수 있지만 자주 반환 하는 인스턴스의 `IActionResult` (또는 `Task<IActionResult>` 비동기 메서드의) 응답을 생성 하는 합니다. 선택에 대 한 작업 메서드는 *어떤 유형의 응답*; 작업 결과 *는 응답 하지 않는*합니다.

### <a name="controller-helper-methods"></a>컨트롤러 도우미 메서드

대부분의 개발자가 해당 컨트롤러에서 기본 상속 표시할 수 필요 하지는 않지만 `Controller` 클래스입니다. 이렇게 많은 속성 및 다양 한 응답을 반환 하는 데 도움이 되도록 다음과 같은 도우미 메서드를 포함 하는 유용한 방법을에 액세스 하 여 컨트롤러를 제공 합니다.

**[보기](../views/index.md)**

HTML을 렌더링 하는 모델을 사용 하는 뷰를 반환 합니다. 예: `return View(customer);`

**HTTP 상태 코드**

HTTP 상태 코드를 반환 합니다. 예: `return BadRequest();`

**서식이 지정 된 응답**

반환할 `Json` 또는 지정 된 방식으로 개체의 서식을 지정 하려면 유사 합니다. 예: `return Json(customer);`

**콘텐츠 협상 된 응답**

작업 개체를 직접 반환 하는 대신 콘텐츠 협상 된 응답을 반환할 수 있습니다 (사용 하 여 `Ok`, `Created`, `CreatedAtRoute` 또는 `CreatedAtAction`). 예를 들어 `return Ok();` 또는`return CreatedAtRoute("routename",values,newobject);`

**리디렉션**

다른 작업이 나 대상에는 리디렉션을 반환 (사용 하 여 `Redirect`, `LocalRedirect`, `RedirectToAction` 또는 `RedirectToRoute`). 예: `return RedirectToAction("Complete", new {id = 123});`

위의 방법 외에도 작업 놓는 개체를 반환할 수 있습니다. 이 경우 개체는 클라이언트의 요청에 따라 포맷 됩니다. 에 대 한 자세한 내용은 [응답 데이터 서식 지정](../models/formatting.md)

### <a name="cross-cutting-concerns"></a>문제

대부분의 앱에서 많은 작업의 워크플로 부분을 공유 합니다. 예를 들어, 대부분 응용 프로그램의 인증 된 사용자만 사용할 수 있습니다 또는 캐싱에서 장점을 활용할 수 있습니다. 하기 전에 일부 논리를 수행 하려면 하거나 사용할 수 있습니다 작업 메서드가 실행 된 후 때는 *필터*합니다. 너무 커지지를 사용 하 여 사용자의 작업을 유지할 수 [필터](filters.md) 이러한 문제를 처리할 수 있습니다. 작업을 수행 하도록 허용 내 중복을 방지할 수이 고 [안 함 반복 직접 (드라이) 원칙](http://deviq.com/don-t-repeat-yourself/)합니다.

권한 부여 및 인증의 경우 적용할 수는 `Authorize` 특성 필요로 하는 행위. 컨트롤러에 추가 하는 것이 컨트롤러 내에서 모든 작업에 적용 됩니다. 이 특성은 적절 한 필터 확인을 추가 합니다.이 작업에 대 한 모든 요청에 적용 됩니다. 필터 동작을 보다 세부적으로 제어를 제공 하기 위해 컨트롤러와 작업 수준에서 일부 특성을 적용할 수 있습니다. 자세한 정보: [필터](filters.md)합니다.

MVC 응용 프로그램의 문제는 다른 예 포함 될 수 있습니다.
   * [오류 처리](filters.md#exception-filters)

   * [응답 캐싱](../../performance/caching/response.md)

> [!NOTE]
> 필터를 사용 하 여 MVC 응용 프로그램에서 많은 문제를 처리할 수 있습니다. 모든 ASP.NET 핵심 응용 프로그램에 사용할 수 있는 염두에 또 다른 옵션은 사용자 지정 [미들웨어](../../fundamentals/middleware.md)합니다.

