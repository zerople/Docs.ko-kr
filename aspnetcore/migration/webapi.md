---
title: "ASP.NET Web API에서에서 마이그레이션 | Microsoft 문서"
author: ardalis
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 4f0564b4-ed4e-4e1e-9755-c1144d21a0ef
ms.technology: aspnet
ms.prod: asp.net-core
uid: migration/webapi
translationtype: Machine Translation
ms.sourcegitcommit: 010b730d2716f9f536fef889bc2f767afb648ef4
ms.openlocfilehash: 4efbd5ce957189c09b4501192c1c0ffebf336b7a
ms.lasthandoff: 03/23/2017

---
# <a name="migrating-from-aspnet-web-api"></a>ASP.NET Web API에서 마이그레이션

여 [Steve Smith](http://ardalis.com) 및 [Scott Addie](https://scottaddie.com)

웹 Api는 광범위 한 브라우저 및 모바일 장치를 비롯 한 클라이언트에 도달 하는 HTTP 서비스입니다. ASP.NET Core MVC 웹 응용 프로그램을 구축 하는 단일 하 고 일관 된 방법을 제공 하는 웹 Api를 구축 하는 것에 대 한 지원이 포함 되어 있습니다. 이 문서에서는 웹 API 구현 ASP.NET Web API에서 핵심 ASP.NET MVC로 마이그레이션하는 데 필요한 단계 설명 합니다.

[샘플 코드 보기 또는 다운로드](https://github.com/aspnet/Docs/tree/master/aspnetcore/migration/webapi/sample)

## <a name="review-aspnet-web-api-project"></a>ASP.NET Web API 프로젝트 검토

이 문서에서는 샘플 프로젝트의 *ProductsApp*문서에서 만든 [ASP.NET Web API 시작 하기](http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api) 기준으로 합니다. 해당 프로젝트에서 간단한 ASP.NET 웹 API 프로젝트는 다음과 같이 구성 됩니다.

*Global.asax.cs*를 호출할 `WebApiConfig.Register`:

[!code-csharp[주](../migration/webapi/sample/ProductsApp/Global.asax.cs?highlight=14)]

`WebApiConfig`에 정의 된 *App_Start*, 하나의 정적 있으며 `Register` 메서드:

[!code-csharp[주](../migration/webapi/sample/ProductsApp/App_Start/WebApiConfig.cs?highlight=15,16,17,18,19,20)]


이 클래스를 구성 [특성 라우팅](http://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2)이지만 실제로 프로젝트에 사용 되 고 있습니다. 또한 ASP.NET Web API에서 사용 되는 라우팅 테이블을 구성 합니다. 이 경우 ASP.NET Web API는 것을 예상 형식과 일치 하는 Url */api/ {컨트롤러} / {id}*와 *{id}* 선택 사항입니다.

*ProductsApp* 에서 상속 되는 하나의 간단한 컨트롤러를 포함 하는 프로젝트 `ApiController` 고 두 개의 메서드를 노출 합니다.

[!code-csharp[주](../migration/webapi/sample/ProductsApp/Controllers/ProductsController.cs?highlight=19,24)]

모델을 마지막으로, *제품*를 사용 하 여는 *ProductsApp*, 간단한 클래스입니다.

[!code-csharp[주](webapi/sample/ProductsApp/Models/Product.cs)]

간단한 프로젝트를 시작할 되었으므로, 이제는이 웹 API 프로젝트 핵심 ASP.NET MVC로 마이그레이션하는 방법을 보여 줍니다 수 있습니다.

## <a name="create-the-destination-project"></a>대상 프로젝트 만들기

새로운 빈 솔루션을 만들고 이름을 Visual Studio를 사용 하 여 *WebAPIMigration*합니다. 기존 추가 *ProductsApp* 프로젝트를 한 다음, 새 ASP.NET 핵심 웹 응용 프로그램 프로젝트를 솔루션에 추가 합니다. 새 프로젝트의 이름을 *ProductsCore*합니다.

![웹 서식 파일을 열고 새 프로젝트 대화 상자](webapi/_static/add-web-project.png)

다음으로, 웹 API 프로젝트 템플릿을 선택 합니다. 우리는 마이그레이션합니다는 *ProductsApp* 내용을이 새 프로젝트에 있습니다.

![ASP.NET Core 템플릿 목록에서 선택 된 웹 API 프로젝트 템플릿 사용 하 여 새 웹 응용 프로그램 대화 상자](webapi/_static/aspnet-5-webapi.png)

삭제 된 `Project_Readme.html` 새 프로젝트에서 파일입니다. 솔루션은 이제 다음과 같이 표시 됩니다.

![파일 및 폴더의 폴더를 보여 주는 솔루션 탐색기에서 응용 프로그램 솔루션을 엽니다는 ProductsApp 및 ProductsCore 프로젝트](webapi/_static/webapimigration-solution.png)

## <a name="migrate-configuration"></a>구성 마이그레이션

ASP.NET Core 더 이상 사용 하 여 *Global.asax*, *web.config*, 또는 *App_Start* 폴더입니다. 모든 시작 작업을 수행 하는 대신, *Startup.cs* 프로젝트의 루트에 (참조 [응용 프로그램 시작](../fundamentals/startup.md)). ASP.NET 핵심 MVC의 특성 기반 라우팅 이제 기본적으로 포함 된 경우 `UseMvc()` 가 호출 되 고 이것은 웹 API 라우팅을 구성 하는 권장된 방법은 (이며 라우팅 웹 API 시작 프로젝트를 처리 하는 방법).

[!code-none[주](../migration/webapi/sample/ProductsCore/Startup.cs?highlight=40)]

앞으로 프로젝트의 특성 라우팅을 사용 하 려 한다고 가정 하므로, 추가 구성이 필요 하지 않습니다. 샘플에서 하는 것 처럼 컨트롤러 및 작업을 하는 데 필요한 특성을 적용 하기만 하면 `ValuesController` 웹 API 시작 프로젝트에 포함 된 클래스:

[!code-csharp[주](../migration/webapi/sample/ProductsCore/Controllers/ValuesController.cs?highlight=9,13,20,27,33,39)]

있다는 사실에 주의 *[controller]* 8입니다. 이제 특성 기반 라우팅과 같은 특정 토큰 지원 *[controller]* 및 *[작업]*합니다. 이러한 토큰 런타임 시 대체 되는 컨트롤러 또는 작업의 이름으로 각각 특성이 적용 되었습니다. 이 처리 되지 않은 프로젝트에서 매직 문자열의 수를 줄일 수 있으며 경로 유지 작업 및 해당 컨트롤러와 함께 동기화 된 자동 이름 바꾸기 리팩터링을 적용 된 경우.

제품 API 컨트롤러를 마이그레이션하려면에서는 먼저 복사 *ProductsController* 새 프로젝트에 있습니다. 그런 다음 단순히 다음과 같은 컨트롤러에서 route 특성

```csharp
[Route("api/[controller]")]
```

추가 해야는 `[HttpGet]` 둘 모두를 HTTP Get을 통해 호출 해야 하므로 두 개의 메서드 특성입니다. 에 대 한 특성에는 기대 하는 "id" 매개 변수는 포함 `GetProduct()`:

```csharp
// /api/products
[HttpGet]
...

// /api/products/1
[HttpGet("{id}")]
```

이 시점에서 제대로 구성 되어 라우팅 그러나 것 테스트 아직 없습니다. 추가 변경 해야 하기 전에 *ProductsController* 컴파일됩니다.

## <a name="migrate-models-and-controllers"></a>모델 및 컨트롤러 마이그레이션

이 간단한 Web API 프로젝트에 대 한 마이그레이션 프로세스의 마지막 단계는 컨트롤러와 모든 모델 사용을 통해 복사 하는 것입니다. 이 경우 단순히 복사 *Controllers/ProductsController.cs* 새 원래 프로젝트에서. 그런 다음 새 원래 프로젝트에서 모델 폴더 전체를 복사 합니다. 새 프로젝트 이름과 일치 하도록 네임 스페이스 조정 (*ProductsCore*).  이 시점에서 응용 프로그램을 빌드하고 컴파일 오류 수를 찾을 수 있습니다. 일반적으로 다음과 같은 범주로 분류 해야이 됩니다.

* *ApiController* 존재 하지 않는

* *System.Web.Http* 네임 스페이스가 없습니다.

* *IHttpActionResult* 존재 하지 않는

다행 스럽게도 이들은 모두 매우 쉽게 수정할 수 있습니다.

* 변경 *ApiController* 를 *컨트롤러* (추가 해야 할 수도 *Microsoft.AspNetCore.Mvc를 사용 하 여*)

* 삭제를 참조 하는 문을 사용 하 여 *System.Web.Http*

* 반환 하는 모든 메서드에 변경 *IHttpActionResult* 반환 하는 *IActionResult*

이러한 변경 내용을 수행 하 고 사용 하지 않는 된 문을 사용 하 여 면 제거 마이그레이션된 *ProductsController* 클래스는 다음과 같습니다.

[!code-csharp[주](../migration/webapi/sample/ProductsCore/Controllers/ProductsController.cs?highlight=1,2,6,8,9,27)]

마이그레이션된 프로젝트를 실행 하 고를 찾는 이제 있어야 */api/제품*; 3 제품의 전체 목록은 보여야 합니다. 찾아 */api/products/1* 첫 번째 제품 표시 되어야 합니다.

## <a name="summary"></a>요약

간단한 ASP.NET 웹 API 프로젝트 핵심 ASP.NET MVC로 마이그레이션할를 간단 하 덕분 ASP.NET 핵심 MVC의 웹 Api에 대 한 기본 제공 지원 합니다. 모든 ASP.NET Web API 프로젝트로 마이그레이션할 필요는 주요 작업은 경로, 컨트롤러 및 컨트롤러 및 작업에 사용 되는 형식에 대 한 업데이트와 함께 모델입니다.

