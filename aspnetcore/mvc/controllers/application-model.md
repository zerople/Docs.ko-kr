---
title: "응용 프로그램 모델에서 작업 | Microsoft 문서"
author: ardalis
description: 
keywords: "ASP.NET Core, ASP.NET 핵심 MVC 응용 프로그램 모델"
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 4eb7e52f-5665-41a4-a3e3-e348d07337f2
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/controllers/application-model
translationtype: Machine Translation
ms.sourcegitcommit: 010b730d2716f9f536fef889bc2f767afb648ef4
ms.openlocfilehash: 1b37ebbe8a637f682010621728de7b60c7417e4a
ms.lasthandoff: 03/23/2017

---
# <a name="working-with-the-application-model"></a>응용 프로그램 모델에서 작업

[Steve Smith](http://ardalis.com)

ASP.NET Core MVC 정의 *응용 프로그램 모델* MVC 응용 프로그램의 구성 요소를 나타내는 합니다. 읽을 수 있으며 MVC 요소의 작동 방식을 수정 하려면이 모델을 조작할 수 있습니다. 기본적으로 MVC는 클래스를 컨트롤러로 간주 되며, 이러한 클래스는 메서드는 다음 작업을 매개 변수 및 라우팅 동작 하는 방법을 결정 하려면 특정 규칙을 따릅니다. 사용자 고유의 규칙을 만들고 전역적으로 또는 특성으로 적용 하 여 응용 프로그램의 요구 사항에 맞게이 동작을 사용자 지정할 수 있습니다.

## <a name="models-and-providers"></a>모델 및 공급자

ASP.NET 핵심 MVC 응용 프로그램 모델에는 추상 인터페이스 및 MVC 응용 프로그램을 설명 하는 구체적인 구현 클래스를 모두 포함 됩니다. 이 모델은 응용 프로그램의 컨트롤러, 작업, 동작 매개 변수, 경로 및 기본 규칙에 따라 필터를 검색 하는 MVC의 결과입니다. 응용 프로그램 모델을 사용 하 여 기본 MVC 동작에서 서로 다른 규칙을 따르지 앱을 수정할 수 있습니다. 매개 변수, 이름, 경로 및 필터는 모두 사용 되는 구성 데이터 작업 및 컨트롤러입니다.

ASP.NET 핵심 MVC 응용 프로그램 모델의 구조는 다음과 같습니다.

* ApplicationModel
    * 컨트롤러 (ControllerModel)
        * 작업 (ActionModel)
            * 매개 변수 (ParameterModel)

모델의 각 수준에는 공통 액세스할 `Properties` 컬렉션 및 하위 수준 액세스 하 고 계층 구조에서 상위 수준에서 설정 된 속성 값을 덮어쓸 수 있습니다. 속성에 유지 되는지는 `ActionDescriptor.Properties` 작업을 만들 때. 요청이 처리 되는 경우 모든 속성 규칙을 추가 하거나 수정할 수를 통해 액세스할 수 `ActionContext.ActionDescriptor.Properties`합니다. 속성을 사용 하는-작업 별로 필터, 모델 바인더, 등을 구성할 수 있는 좋은 방법입니다.

> [!NOTE]
> `ActionDescriptor.Properties` 컬렉션 스레드로부터 안전 하지 않습니다 (쓰기)에 대 한 응용 프로그램 시작이 완료 되 면 합니다. 규칙은 안전 하 게 데이터를이 컬렉션에 추가 하는 가장 좋은 방법은입니다.

### <a name="iapplicationmodelprovider"></a>IApplicationModelProvider

에 정의 된 공급자 패턴을 사용 하 여 응용 프로그램 모델을 로드 하는 ASP.NET MVC 코어는 [IApplicationModelProvider](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.applicationmodels.iapplicationmodelprovider) 인터페이스입니다. 이 섹션에서는 일부의 방법의 내부 구현 세부 정보를 다룹니다이 공급자 함수입니다. 이 고급 항목-응용 프로그램 모델을 활용 하는 대부분의 응용 프로그램 규칙을 사용 하 여 수행 해야 합니다.

구현에서 `IApplicationModelProvider` 인터페이스 "wrap" 다른 각 구현이 호출 된 `OnProvidersExecuting` 오름차순에 따라 해당 `Order` 속성입니다. `OnProvidersExecuted` 메서드가 반대 순서로 호출 됩니다. 프레임 워크는 여러 공급자를 정의합니다.

첫 번째 (`Order=-1000`):

* [`DefaultApplicationModelProvider`](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.internal.defaultapplicationmodelprovider)

Then (`Order=-990`):

* [`AuthorizationApplicationModelProvider`](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.internal.authorizationapplicationmodelprovider)
* [`CorsApplicationModelProvider`](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.cors.internal.corsapplicationmodelprovider)

> [!NOTE]
> 어떤 두 공급자에 대 한 값이 같은 순서로 `Order` 이라고, 정의 되지 않으며 따라서 해야 사용할 수 없습니다.

> [!NOTE]
> `IApplicationModelProvider`확장 하는 프레임 워크 작성에 대 한 고급 개념이입니다. 일반적으로 규칙을 사용 해야 하는 응용 프로그램 및 프레임 워크는 공급자를 사용 해야 합니다. 중요 한 차이점은 공급자 규칙 항상 실행 합니다.

`DefaultApplicationModelProvider` 다양 한 핵심 ASP.NET MVC에서 사용 하는 기본 동작을 설정 합니다. 책임은 다음과 같습니다.

* 컨텍스트에 전역 필터를 추가합니다.
* 컨텍스트에 컨트롤러를 추가합니다.
* 작업으로 공용 컨트롤러 메서드를 추가합니다.
* 작업 메서드 매개 변수를 컨텍스트에 추가
* 경로 및 기타 특성을 적용합니다.

일부 기본 제공 동작에 의해 구현 되는 `DefaultApplicationModelProvider`합니다. 이 공급자가 생성 하는 일을 담당는 [ `ControllerModel` ](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.applicationmodels.controllermodel)를 다시 참조 하는 [ `ActionModel` ](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.applicationmodels.actionmodel#Microsoft_AspNetCore_Mvc_ApplicationModels_ActionModel), [ `PropertyModel` ](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.applicationmodels.propertymodel), 및 [ `ParameterModel` ](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.applicationmodels.parametermodel#Microsoft_AspNetCore_Mvc_ApplicationModels_ParameterModel) 인스턴스. `DefaultApplicationModelProvider` 클래스는 하 고 나중에 변경 될 수 있는 내부 프레임 워크 구현 정보입니다. 

`AuthorizationApplicationModelProvider` 와 연결 된 동작을 적용 합니다는 `AuthorizeFilter` 및 `AllowAnonymousFilter` 특성입니다. [이러한 특성에 대 한 자세한](https://docs.microsoft.com/aspnet/core/security/authorization/simple)합니다.

`CorsApplicationModelProvider` 와 연결 된 동작을 구현 하는 `IEnableCorsAttribute` 및 `IDisableCorsAttribute`, 및 `DisableCorsAuthorizationFilter`합니다. [CORS에 대 한 자세한 정보](https://docs.microsoft.com/aspnet/core/security/cors)합니다.

## <a name="conventions"></a>규칙

응용 프로그램 모델은 전체 모델 또는 공급자를 재정의 하는 보다 모델의 동작을 사용자 지정 하는 간단한 방법을 제공 하는 규칙 추상화를 정의 합니다. 이러한 추상화는 응용 프로그램의 동작을 수정 하는 것이 좋습니다. 규칙에는 사용자 지정 항목을 동적으로 적용 되는 코드를 작성할 수 있는 방법을 제공 합니다. 반면 [필터](https://docs.microsoft.com/aspnet/core/mvc/controllers/filters) 사용자 지정 하면 전체 응용 프로그램은 연결 되는 방식을 함께, 프레임 워크의 동작을 수정 하는 수단을 제공 합니다.

다음 규칙을 사용할 수 있습니다.

* [`IApplicationModelConvention`](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.applicationmodels.iapplicationmodelconvention)
* [`IControllerModelConvention`](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.applicationmodels.icontrollermodelconvention)
* [`IActionModelConvention`](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.applicationmodels.iactionmodelconvention)
* [`IParameterModelConvention`](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.applicationmodels.iparametermodelconvention)

MVC 옵션을 추가 하거나 구현 하 여 규칙을 적용할 `Attribute`s 및 컨트롤러, 동작 또는 동작 매개 변수를에 적용 (비슷합니다 [ `Filters` ](https://docs.microsoft.com/aspnet/core/mvc/controllers/filters)). 필터와 달리 각 요청의 일부가 아니라 응용 프로그램을 시작할 때 규칙 실행만 됩니다.

### <a name="sample-modifying-the-applicationmodel"></a>샘플:는 ApplicationModel 수정

다음 규칙은 응용 프로그램 모델에 속성을 추가 하는 데 사용 됩니다. 

[!code-csharp[주](./application-model/sample/src/AppModelSample/Conventions/ApplicationDescription.cs)]

MVC에 추가 되 면 옵션으로 응용 프로그램 모델 규칙을 적용할 `ConfigureServices` 에서 `Startup`합니다.

[!code-csharp[주](./application-model/sample/src/AppModelSample/Startup.cs?name=ConfigureServices&highlight=5)]

속성에서 액세스할 수 있는 `ActionDescriptor` 컨트롤러 작업 내에서 properties 컬렉션:

[!code-csharp[주](./application-model/sample/src/AppModelSample/Controllers/AppModelController.cs?name=AppModelController)]

### <a name="sample-modifying-the-controllermodel-description"></a>샘플: ControllerModel 설명을 수정

이전 예제와 같이 컨트롤러 모델 사용자 지정 속성을 포함 하도록 수정할 수도 있습니다. 이러한 응용 프로그램 모델에 지정 된 같은 이름의 기존 속성을 덮어씁니다. 컨트롤러 수준에 대 한 설명을 추가 하는 다음 규칙 특성:

[!code-csharp[주](./application-model/sample/src/AppModelSample/Conventions/ControllerDescriptionAttribute.cs)]

이 규칙은 컨트롤러에서 특성으로 적용 됩니다.

[!code-csharp[주](./application-model/sample/src/AppModelSample/Controllers/DescriptionAttributesController.cs?name=ControllerDescription&highlight=1)]

"설명" 속성이 이전 예제와 같이 동일한 방식으로 액세스 합니다.

### <a name="sample-modifying-the-actionmodel-description"></a>샘플: ActionModel 설명을 수정

컨트롤러 또는 응용 프로그램 수준에서 이미 적용 된 동작을 재정의 하는 개별 작업에는 별도 특성 규칙을 적용할 수 있습니다.

[!code-csharp[주](./application-model/sample/src/AppModelSample/Conventions/ActionDescriptionAttribute.cs)]

이전 예제에서는 컨트롤러 내에서 동작을 적용 하는이 방법을 컨트롤러 수준 규칙을 재정의 보여 줍니다.

[!code-csharp[주](./application-model/sample/src/AppModelSample/Controllers/DescriptionAttributesController.cs?name=DescriptionAttributesController&highlight=9)]

### <a name="sample-modifying-the-parametermodel"></a>샘플:는 ParameterModel 수정

다음 규칙 액션 매개 변수 수정에 적용할 수 있습니다 자신의 `BindingInfo`합니다. 다음 규칙 있어야 매개 변수는 경로 매개 변수입니다. 다른 잠재적인 바인딩 소스 (예: 쿼리 문자열 값)은 무시 됩니다.

[!code-csharp[주](./application-model/sample/src/AppModelSample/Conventions/MustBeInRouteParameterModelConvention.cs)]

특성 작업 매개 변수에 적용할 수 있습니다.

[!code-csharp[주](./application-model/sample/src/AppModelSample/Controllers/ParameterModelController.cs?name=ParameterModelController&highlight=5)]

### <a name="sample-modifying-the-actionmodel-name"></a>샘플: ActionModel 이름 수정

다음 규칙을 수정는 `ActionModel` 업데이트 하는 *이름* 적용 되는 동작의 합니다. 새 이름 특성에 대 한 매개 변수로 제공 됩니다. 이 새 이름이 있으므로이 작업 메서드에 도달 하는 데 사용 되는 경로 영향을 줍니다, 라우팅에 의해 사용 됩니다.

[!code-csharp[주](./application-model/sample/src/AppModelSample/Conventions/CustomActionNameAttribute.cs)]

작업 메서드에이 특성은 적용 되는 `HomeController`:

[!code-csharp[주](./application-model/sample/src/AppModelSample/Controllers/HomeController.cs?name=ActionModelConvention&highlight=2)]

메서드 이름이 `SomeName`, 메서드 이름을 사용 하 여 MVC 관례를 재정의 하 고 사용 하 여 작업 이름을 대체 하는 특성 `MyCoolAction`합니다. 따라서이 작업에 도달 하는 데 사용 되는 경로 `/Home/MyCoolAction`합니다.

> [!NOTE]
> 이 예제는 기본적으로 기본 제공을 사용할 때와 같은 [ActionName](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.actionnameattribute) 특성입니다.

### <a name="sample-custom-routing-convention"></a>샘플: 사용자 지정 라우팅 규칙

사용할 수는 `IApplicationModelConvention` 라우팅의 작동 원리를 사용자 지정할 수 있습니다. 예를 들어, 다음 규칙은 통합할 컨트롤러의 네임 스페이스 대체 하는 해당 경로 `.` 으로 네임 스페이스에 `/` 경로에 있습니다.

[!code-csharp[주](./application-model/sample/src/AppModelSample/Conventions/NamespaceRoutingConvention.cs)]

규칙에서 시작 옵션으로 추가 됩니다.

[!code-csharp[주](./application-model/sample/src/AppModelSample/Startup.cs?name=ConfigureServices&highlight=6)]

> [!TIP]
> 규칙을 추가할 수 여 [미들웨어](https://docs.microsoft.com/aspnet/core/fundamentals/middleware) 에 액세스 하 여 `MvcOptions` 를 사용 하 여`services.Configure<MvcOptions>(c => c.Conventions.Add(YOURCONVENTION));`

이 샘플에는 특성 라우팅 컨트롤러 이름에 "Namespace"에 있는 사용 하지 않는 경로에이 규칙에이 따라 적용 됩니다. 다음 컨트롤러는이 규칙을 보여 줍니다.

[!code-csharp[주](./application-model/sample/src/AppModelSample/Controllers/NamespaceRoutingController.cs?highlight=7-8)]

## <a name="application-model-usage-in-webapicompatshim"></a>WebApiCompatShim에서 응용 프로그램 모델 사용

ASP.NET MVC 핵심 ASP.NET Web API 2에서 더 다양 한 규칙을 사용합니다. 사용자 지정 규칙을 사용 하 여 웹 API 앱의 일치 하는 ASP.NET MVC 핵심 응용 프로그램의 동작을 수정할 수 있습니다. Microsoft 제공 되며가는 [WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim/) 이 용도로 합니다.

> [!NOTE]
> 에 대 한 자세한 내용은 [ASP.NET Web API에서 마이그레이션](https://docs.microsoft.com/aspnet/core/migration/webapi)).

호출 하 여 mvc 규칙을 추가한 다음 프로젝트에 패키지를 추가 해야 웹 API 호환성 Shim을 사용 하려면 `AddWebApiConventions` 에서 `Startup`:

```c#
services.AddMvc().AddWebApiConventions();
```

Shim은에서 제공 하는 규칙에 적용 된 특정 특성에 나와 있는 응용 프로그램의 일부에만 적용 됩니다. 다음 네 가지 특성을 제어 하는 컨트롤러 shim은 규칙에 의해 수정 된 해당 규칙이 있어야 합니다. 사용 됩니다.

* [UseWebApiActionConventionsAttribute](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.webapicompatshim.usewebapiactionconventionsattribute)
* [UseWebApiOverloadingAttribute](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.webapicompatshim.usewebapioverloadingattribute)
* [UseWebApiParameterConventionsAttribute](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.webapicompatshim.usewebapiparameterconventionsattribute)
* [UseWebApiRoutesAttribute](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.webapicompatshim.usewebapiroutesattribute)

### <a name="action-conventions"></a>작업 규칙

`UseWebApiActionConventionsAttribute` HTTP 메서드 이름에 따라 작업을 매핑할 때 사용 됩니다 (예를 들어, `Get` 매핑되는 `HttpGet`). 특성 라우팅을 사용 하지 않는 작업에만 적용 됩니다.

### <a name="overloading"></a>오버로딩

`UseWebApiOverloadingAttribute` 적용 하는 데 사용 되는 `WebApiOverloadingApplicationModelConvention` 규칙입니다. 이 규칙에이 따라 추가 `OverloadActionConstraint` 후보 작업 요청을 모든 필수 매개 변수를 충족 하는 것을 제한 하는 작업 선택 과정에 있습니다.

### <a name="parameter-conventions"></a>매개 변수 규칙

`UseWebApiParameterConventionsAttribute` 적용 하는 데 사용 되는 `WebApiParameterConventionsApplicationModelConvention` 작업 규칙입니다. 이 규칙의 작업 매개 변수로 사용 하는 단순 형식에 바인딩됨을 URI에서 기본적으로 복합 형식을 요청 본문에서 바인딩되는 동안 지정 합니다.

### <a name="routes"></a>경로

`UseWebApiRoutesAttribute` 컨트롤 여부는 `WebApiApplicationModelConvention` 컨트롤러 규칙이 적용 됩니다. 이 규칙에 대 한 지원을 추가 하는 사용 하도록 설정 하면 [영역](https://docs.microsoft.com/aspnet/core/mvc/controllers/areas) 는 경로에 있습니다.

호환성 패키지에는 규칙 집합을 외에도 다음을 포함 합니다.는 `System.Web.Http.ApiController` 기본 클래스 대체 하는 웹 API에서 제공 합니다. 이렇게 하면 웹 API 및 상속에 대 한 기록 하 여 컨트롤러에서 해당 `ApiController` Asp.net.mvc 코어에서 실행 되는 동안 디자인에 작동 해야 합니다. 이 기본 컨트롤러 클래스의 모든 데코 레이트 된는 `UseWebApi*` 위에 나열 된 특성입니다. `ApiController` 속성, 메서드 및 웹 API에 있는 해당와 호환 되는 결과 형식을 노출 합니다.

## <a name="using-apiexplorer-to-document-your-app"></a>ApiExplorer를 사용 하 여 응용 프로그램을 문서화

응용 프로그램 모델을 노출 한 [ `ApiExplorer` ](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.applicationmodels.apiexplorermodel) 응용 프로그램의 구조를 탐색 하는 데 사용할 수 있는 각 수준에서 속성입니다. 이를 사용할 수 있습니다 [Swagger 같은 도구를 사용 하 여 웹 Api에 대 한 도움말 페이지를 생성할](https://docs.microsoft.com/aspnet/core/tutorials/web-api-help-pages-using-swagger)합니다. `ApiExplorer` 속성 노출은 `IsVisible` 노출 해야 하는 응용 프로그램의 모델의 어떤 부분을 지정 하려면 설정할 수 있는 속성이 있습니다. 규칙을 사용 하 여이 설정을 구성할 수 있습니다.

[!code-csharp[주](./application-model/sample/src/AppModelSample/Conventions/EnableApiExplorerApplicationConvention.cs)]

이 접근 방식을 (및 필요한 경우 추가 규칙)를 사용 하거나 설정할 수 있습니다 앱 내에서 모든 수준에서 표시 유형 API를 사용 하지 않도록 설정 합니다. 

