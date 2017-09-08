---
title: "ASP.NET Core MVC 컨트롤러와의 요청 처리"
author: ardalis
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 07/03/2017
ms.topic: article
ms.assetid: 9da9eb52-8583-4069-af91-155ba3529d7f
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/controllers/actions
ms.openlocfilehash: b7d6341c0312b3f5f122acfb2ee01210151b33bb
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/11/2017
---
# <a name="handling-requests-with-controllers-in-aspnet-core-mvc"></a>ASP.NET Core MVC 컨트롤러와의 요청 처리

여 [Steve Smith](http://ardalis.com) 및 [Scott Addie](https://github.com/scottaddie)

컨트롤러, 작업 및 작업 결과 개발자가 ASP.NET Core MVC를 사용 하 여 앱을 구축 하는 방법의 핵심 부분입니다.

## <a name="what-is-a-controller"></a>컨트롤러는 무엇입니까?

컨트롤러를 정의 하 고 일련의 작업을 그룹화에 사용 됩니다. 작업 (또는 *동작 메서드가*) 요청을 처리 하는 컨트롤러에 메서드가 만들어집니다. 컨트롤러를 논리적으로 그룹화와 유사한 동작입니다. 이 집계 작업의 라우팅, 캐싱 및 권한 부여를 전체적으로 적용할 수 등의 규칙의 공통 집합을 허용 합니다. 요청을 통해 작업에 매핑된 [라우팅](xref:mvc/controllers/routing)합니다.

규칙에 따라 컨트롤러 클래스:
* 프로젝트의 루트 수준에 있는 *컨트롤러* 폴더
* 상속`Microsoft.AspNetCore.Mvc.Controller`

컨트롤러는 다음 조건 중 하나 이상 true는 인스턴스화할 수 있는 클래스입니다.
* 클래스 이름은 "Controller"로 그 뒤에
* 이름이 "Controller" 접미사는 클래스에서 클래스 상속
* 로 데코레이팅된 클래스는 `[Controller]` 특성

컨트롤러 클래스는 연결 된 있어야 `[NonController]` 특성입니다.

컨트롤러 따라야는 [명시적 종속성 원칙](http://deviq.com/explicit-dependencies-principle)합니다. 이 원칙을 구현 하는 방법은 몇 가지가 있습니다. 동일한 서비스를 필요로 하는 여러 컨트롤러 작업을 하는 경우를 사용해 [생성자 삽입](xref:mvc/controllers/dependency-injection#constructor-injection) 해당 종속성을 요청할 수 있습니다. 서비스가 단일 동작 메서드에서 필요한 경우 사용 하십시오 [작업 삽입](xref:mvc/controllers/dependency-injection#action-injection-with-fromservices) 종속성을 요청할 수 있습니다.

내에서 **M**odel-**V**뷰-**C**ontroller 패턴은 컨트롤러에 대 한 책임이 초기 요청 처리와 모델의 인스턴스화입니다. 일반적으로 비즈니스 의사 결정 모델 내에서 수행 되어야 합니다.

컨트롤러는 모델의 처리 (있는 경우)의 결과 사용해 하 고 적절 한 보기와 관련 된 보기 데이터 또는 API 호출의 결과 반환 합니다. 자세한 내용 [ASP.NET Core MVC 개요](xref:mvc/overview) 및 [ASP.NET Core MVC 및 Visual Studio 시작](xref:tutorials/first-mvc-app/start-mvc)합니다.

컨트롤러는는 *UI 수준* 추상화 합니다. 해당 사항이 요청 데이터가 올바른지 확인 하 고 어떤 보기 (또는 API에 대 한 결과)를 반환 해야 선택 합니다. 잘 구성 된 앱에서이 포함 되지 않습니다 직접 데이터 액세스 또는 비즈니스 논리. 대신, 컨트롤러 이러한 책임을 처리 하는 서비스에 위임 합니다.

## <a name="defining-actions"></a>동작 정의

Public 메서드를 컨트롤러에서로 데코레이팅 된 문을 제외한는 `[NonAction]` 특성을 가지 작업입니다. 동작에 매개 변수는 데이터를 요청에 바인딩되고를 사용 하 여 [모델 바인딩](xref:mvc/models/model-binding)합니다. 모델 유효성 검사는 모델 바인딩 있는 모든 작업에 대해 발생 합니다. `ModelState.IsValid` 속성 값을 바인딩하는 모델 및 유효성 검사의 성공 여부를 나타냅니다.

작업 메서드는 비즈니스 문제에 요청을 매핑하는 것에 대 한 논리를 포함 해야 합니다. 비즈니스 문제나 일반적으로 컨트롤러를 통해 액세스 하는 서비스로 표시 되어야 [종속성 주입](xref:mvc/controllers/dependency-injection)합니다. 작업은 비즈니스 작업의 결과 응용 프로그램 상태에 매핑됩니다.

작업, 모든 항목을 반환할 수 있지만 자주의 인스턴스를 반환 `IActionResult` (또는 `Task<IActionResult>` 비동기 메서드에 대 한) 응답을 생성 하는 합니다. 동작 메서드는 선택에 대 한 *응답의 종류*합니다. 작업 결과 *는 응답 하지 않는*합니다.

### <a name="controller-helper-methods"></a>컨트롤러 도우미 메서드

컨트롤러에서 일반적으로 상속 [컨트롤러](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.controller)않더라도이 필요 하지 않습니다. 파생 된 `Controller` 도우미 메서드의 세 가지 범주에 대 한 액세스를 제공 합니다.

#### <a name="1-methods-resulting-in-an-empty-response-body"></a>1. 빈 응답 본문에으로 인해 발생 하는 방법

더 `Content-Type` HTTP 응답 헤더는 응답 본문을 설명 하는 콘텐츠 없으므로 포함 되어 있습니다.

이 범주 내의 결과 형식은 두 가지가: 리디렉션 및 HTTP 상태 코드입니다.

* **HTTP 상태 코드**

    이 형식은 HTTP 상태 코드를 반환합니다. 이러한 종류의 몇 가지 도우미 메서드는 `BadRequest`, `NotFound`, 및 `Ok`합니다. 예를 들어 `return BadRequest();` 400 상태 코드가 실행 될 때을 생성 합니다. 때와 같은 메서드 `BadRequest`, `NotFound`, 및 `Ok` 는 오버 로드를 더 이상 자격이 HTTP 상태 코드 응답자도 콘텐츠 협상 수행 되 고 있으므로 합니다.

* **리디렉션**

    이 이와 같은 작업이 나 대상에 리디렉션이 반환 (사용 하 여 `Redirect`, `LocalRedirect`, `RedirectToAction`, 또는 `RedirectToRoute`). 예를 들어 `return RedirectToAction("Complete", new {id = 123});` 리디렉션합니다 `Complete`, 익명 개체를 전달 합니다.

    리디렉션 결과 형식이 주로 추가 HTTP 상태 코드 형식에서 다른 한 `Location` HTTP 응답 헤더입니다.

#### <a name="2-methods-resulting-in-a-non-empty-response-body-with-a-predefined-content-type"></a>2. 미리 정의 된 콘텐츠 형식과 비어 있지 않은 응답 본문에으로 인해 발생 하는 방법

이 항목의 대부분의 도우미 메서드를 포함 한 `ContentType` 속성을 설정할 수는 `Content-Type` 응답 본문을 설명 하기 위해 응답 헤더입니다.

이 범주 내의 결과 형식은 두 가지가: [보기](xref:mvc/views/overview) 및 [서식이 지정 된 응답](xref:mvc/models/formatting)합니다.

* **보기**

    이 형식은 HTML을 렌더링 하는 모델을 사용 하는 뷰를 반환 합니다. 예를 들어 `return View(customer);` 보기에 데이터 바인딩 모델을 전달 합니다.

* **서식이 지정 된 응답**

    이 형식은 JSON 또는 비슷한 데이터 교환 형식인 개체를 나타내는 지정 된 방식으로 반환 합니다. 예를 들어 `return Json(customer);` JSON 형식에 제공된 된 개체를 serialize 합니다.
    
    이 형식의 다른 일반적인 방법 `File`, `PhysicalFile`, 및 `VirtualFile`합니다. 예를 들어 `return PhysicalFile(customerFilePath, "text/xml");` 에서 설명 하는 XML 파일을 반환 합니다.는 `Content-Type` 응답 헤더 값에 "text/xml"입니다.

#### <a name="3-methods-resulting-in-a-non-empty-response-body-formatted-in-a-content-type-negotiated-with-the-client"></a>3. 클라이언트와 협상 콘텐츠 형식으로 서식이 지정 된 비어 있지 않은 응답 본문에으로 인해 발생 하는 방법

이 범주는 더 잘 알려져 **콘텐츠 협상**합니다. [콘텐츠 협상](xref:mvc/models/formatting#content-negotiation) 동작 반환 될 때마다 적용 되는 [ObjectResult](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.objectresult) 형식 또는 이외의 이와 [IActionResult](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.iactionresult) 구현 합니다. 비-반환 하는 동작`IActionResult` 구현 (예를 들어 `object`)도 서식이 지정 된 응답을 반환 합니다.

이러한 종류의 일부 도우미 메서드를 포함 `BadRequest`, `CreatedAtRoute`, 및 `Ok`합니다. 이러한 방법의 예로 `return BadRequest(modelState);`, `return CreatedAtRoute("routename", values, newobject);`, 및 `return Ok(value);`각각. `BadRequest` 및 `Ok` 값으로 전달 하는 경우에 콘텐츠 협상을 수행 합니다; 값을 전달 하지 않고 대신으로 사용 HTTP 상태 코드의 결과 형식입니다. `CreatedAtRoute` 메서드, 반면에 항상 콘텐츠 협상을 수행 이후의 메서드의 오버 로드 된 값을 전달할 수 모두 필요 합니다.

### <a name="cross-cutting-concerns"></a>일반적인 문제

일반적으로 응용 프로그램의 워크플로 부분을 공유합니다. 쇼핑 카트를 액세스할 때 인증을 요구 하는 응용 프로그램 또는 일부 페이지에 데이터를 캐시 하는 응용 프로그램을 예로 들 수 있습니다. 를 앞 이나 뒤 동작 메서드의 논리를 수행 하려면 사용 하 여 한 *필터*합니다. 사용 하 여 [필터](xref:mvc/controllers/filters) 일반적인 문제에 따라 수 있으므로 중복 줄일 수는 [하지 반복 직접 (드라이) 원칙](http://deviq.com/don-t-repeat-yourself/)합니다.

필터 가장와 같은 특성을 `[Authorize]`, 원하는 수준의 세분성에 따라 컨트롤러 또는 동작 수준에 적용할 수 있습니다.

오류 처리 및 응답 캐시는 종종 일반적인 문제:
   * [오류 처리](xref:mvc/controllers/filters#exception-filters)
   * [응답 캐시](xref:performance/caching/response)

필터 또는 사용자 지정을 사용 하 여 많은 일반적인 문제를 처리할 수 있습니다 [미들웨어](xref:fundamentals/middleware)합니다.