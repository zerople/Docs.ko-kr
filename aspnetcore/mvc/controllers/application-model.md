---
title: "응용 프로그램 모델 작업"
author: ardalis
description: 
keywords: "ASP.NET Core,ASP.NET 코어 MVC 응용 프로그램 모델"
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 4eb7e52f-5665-41a4-a3e3-e348d07337f2
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/controllers/application-model
ms.openlocfilehash: 3c35184921dbe26cde100fd3d5124e38ea0d06cf
ms.sourcegitcommit: 78d28178345a0eea91556e4cd1adad98b1446db8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2017
---
# <a name="working-with-the-application-model"></a>응용 프로그램 모델 작업

으로 [Steve Smith](https://ardalis.com/)

ASP.NET Core MVC 정의 *응용 프로그램 모델* MVC 응용 프로그램의 구성 요소를 나타내는입니다. 읽을 수 있으며 MVC 요소의 작동 방식을 수정 하려면이 모델을 조작할 수 있습니다. 기본적으로 MVC는 클래스를 컨트롤러로 간주 되며, 이러한 클래스는 메서드는 다음 작업을 매개 변수 및 라우팅 동작 하는 방법을 결정 하는 특정 규칙을 따릅니다. 이 동작을 사용자 지정 규칙을 만들고 전역으로 또는 특성으로 적용 하 여 응용 프로그램의 요구 사항에 맞게 사용자 지정할 수 있습니다.

## <a name="models-and-providers"></a>모델 및 공급자

ASP.NET Core MVC 응용 프로그램 모델에는 추상 인터페이스 및 MVC 응용 프로그램을 설명 하는 구체적인 구현인 클래스가 모두 포함 됩니다. 이 모델은 응용 프로그램의 컨트롤러, 작업, 작업 매개 변수, 경로 및 기본 규칙에 따라 필터를 검색 하는 MVC의 결과입니다. 응용 프로그램 모델을 사용 하 여 기본 MVC 동작에서 다른 규칙을 따라야 응용 프로그램을 수정할 수 있습니다. 매개 변수, 이름, 경로 및 필터는 모두 데이터로 사용 구성 작업 및 컨트롤러에 대 한 합니다.

ASP.NET Core MVC 응용 프로그램 모델의 구조는 다음과 같습니다.

* ApplicationModel
    * 컨트롤러 (ControllerModel)
        * 작업 (ActionModel)
            * 매개 변수 (ParameterModel)

모델의 각 수준에는 공통 권한이 `Properties` 컬렉션 및 하위 수준 액세스 하 고 계층의 상위 수준에서 설정 된 속성 값을 덮어쓸 수 있습니다. 속성에 저장 됩니다는 `ActionDescriptor.Properties` 작업을 만드는 시기입니다. 다음 요청을 처리 중인 경우 모든 속성 추가 또는 수정 된 규칙을 통해 액세스할 수 `ActionContext.ActionDescriptor.Properties`합니다. 속성을 사용 하는 필터, 모델 바인더, 등-액션에 구성할 수 있는 좋은 방법입니다.

> [!NOTE]
> `ActionDescriptor.Properties` 컬렉션 스레드로부터 안전 하지 않습니다 (쓰기)에 대 한 응용 프로그램 시작을 완료 했습니다. 규칙은 안전 하 게 데이터를이 컬렉션에 추가 하는 가장 좋은 방법은 합니다.

### <a name="iapplicationmodelprovider"></a>IApplicationModelProvider

정의한 공급자 패턴을 사용 하는 응용 프로그램 모델을 로드 하는 ASP.NET Core MVC는 [IApplicationModelProvider](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.applicationmodels.iapplicationmodelprovider) 인터페이스입니다. 이 섹션에서는 일부의 방법의 내부 구현 세부 정보를 다룹니다이 공급자 함수입니다. 이 고급 항목-응용 프로그램 모델을 활용 하는 대부분의 응용 프로그램 규칙을 사용 하 여 수행 해야 합니다.

구현에서 `IApplicationModelProvider` 인터페이스 "" 서로로 래핑합니다 각 구현 호출 `OnProvidersExecuting` 오름차순으로 정렬 순서에 따라 해당 `Order` 속성. `OnProvidersExecuted` 메서드 반대 순서로 호출 됩니다. 프레임 워크 여러 공급자를 정의합니다.

첫 번째 (`Order=-1000`):

* [`DefaultApplicationModelProvider`](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.internal.defaultapplicationmodelprovider)

다음 (`Order=-990`):

* [`AuthorizationApplicationModelProvider`](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.internal.authorizationapplicationmodelprovider)
* [`CorsApplicationModelProvider`](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.cors.internal.corsapplicationmodelprovider)

> [!NOTE]
> 순서에 대해 같은 값으로는 두 공급자 `Order` 라고 따라서 해야에 의존 하 고이 정의 되지 않습니다.

> [!NOTE]
> `IApplicationModelProvider`프레임 워크를 확장 하는 작성자를 위한 고급 개념이입니다. 일반적으로 앱 규칙을 사용 해야 하 고 프레임 워크 공급자를 사용 해야 합니다. 중요 한 차이점은 규칙 하기 전에 공급자는 항상 실행 합니다.

`DefaultApplicationModelProvider` ASP.NET Core MVC에서 사용 하는 기본 동작을 설정 합니다. 책임은 다음과 같습니다.

* 컨텍스트를 전역 필터를 추가합니다.
* 컨트롤러는 컨텍스트를 추가
* 동작으로 공용 컨트롤러 메서드를 추가합니다.
* 컨텍스트에 동작 메서드 매개 변수 추가
* 경로 및 기타 특성 적용

일부 기본 제공 동작에 의해 구현 되는 `DefaultApplicationModelProvider`합니다. 이 공급자는 생성 해야는 [ `ControllerModel` ](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.applicationmodels.controllermodel)를 차례로 참조 하는 [ `ActionModel` ](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.applicationmodels.actionmodel#Microsoft_AspNetCore_Mvc_ApplicationModels_ActionModel), [ `PropertyModel` ](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.applicationmodels.propertymodel), 및 [ `ParameterModel` ](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.applicationmodels.parametermodel#Microsoft_AspNetCore_Mvc_ApplicationModels_ParameterModel) 인스턴스. `DefaultApplicationModelProvider` 클래스는 하 고 나중에 변경 될 수 있는 프레임 워크 내부 구현 세부 정보. 

`AuthorizationApplicationModelProvider` 와 연결 된 동작을 적용 합니다는 `AuthorizeFilter` 및 `AllowAnonymousFilter` 특성입니다. [이러한 특성에 대 한 자세한](xref:security/authorization/simple)합니다.

`CorsApplicationModelProvider` 와 연결 된 동작을 구현 하는 `IEnableCorsAttribute` 및 `IDisableCorsAttribute`, 및 `DisableCorsAuthorizationFilter`합니다. [CORS에 대 한 자세한](xref:security/cors)합니다.

## <a name="conventions"></a>규칙

응용 프로그램 모델 전체 모델 또는 공급자를 재정의 하는 보다는 모델의 동작을 사용자 지정 하는 간단한 방법을 제공 하는 규칙 추상화를 정의 합니다. 이러한 추상화는 앱의 동작을 수정 하는 것이 좋습니다. 규칙에는 사용자 지정에 동적으로 적용 되는 코드를 작성할 수 있는 방법을 제공 합니다. 반면 [필터](xref:mvc/controllers/filters) 프레임 워크의 동작을 수정 하 게, 사용자 지정 제어할 수 있도록 전체 응용 프로그램 함께 연결 합니다.

다음과 같은 규칙을 사용할 수 있습니다.

* [`IApplicationModelConvention`](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.applicationmodels.iapplicationmodelconvention)
* [`IControllerModelConvention`](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.applicationmodels.icontrollermodelconvention)
* [`IActionModelConvention`](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.applicationmodels.iactionmodelconvention)
* [`IParameterModelConvention`](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.applicationmodels.iparametermodelconvention)

MVC 옵션을 추가 하거나 구현 하 여 규칙을 적용할 `Attribute`s 및 컨트롤러, 동작 또는 액션 매개 변수를에 적용 (비슷합니다 [ `Filters` ](xref:mvc/controllers/filters)). 필터와 달리 각 요청의 일부가 아니라 응용 프로그램을 시작 하는 경우 규칙 실행만 됩니다.

### <a name="sample-modifying-the-applicationmodel"></a>샘플:는 ApplicationModel 수정

다음 규칙은 응용 프로그램 모델에 속성을 추가 하는 데 사용 됩니다. 

[!code-csharp[Main](./application-model/sample/src/AppModelSample/Conventions/ApplicationDescription.cs)]

MVC에 추가 될 때 옵션으로 응용 프로그램 모델 규칙을 적용할 `ConfigureServices` 에서 `Startup`합니다.

[!code-csharp[Main](./application-model/sample/src/AppModelSample/Startup.cs?name=ConfigureServices&highlight=5)]

속성은에서 액세스할 수는 `ActionDescriptor` 컨트롤러 작업 내에서 properties 컬렉션:

[!code-csharp[Main](./application-model/sample/src/AppModelSample/Controllers/AppModelController.cs?name=AppModelController)]

### <a name="sample-modifying-the-controllermodel-description"></a>샘플: 수정 ControllerModel 파일에 대 한 설명

앞의 예와 컨트롤러 모델 사용자 지정 속성을 포함 하려면 수정할 수도 있습니다. 이러한 응용 프로그램 모델에 지정 된 동일한 이름의 기존 속성을 덮어씁니다. 컨트롤러 수준에서 다음 규칙 특성에 대 한 설명을 추가합니다.

[!code-csharp[Main](./application-model/sample/src/AppModelSample/Conventions/ControllerDescriptionAttribute.cs)]

이 규칙은 컨트롤러에 대 한 특성으로 적용 됩니다.

[!code-csharp[Main](./application-model/sample/src/AppModelSample/Controllers/DescriptionAttributesController.cs?name=ControllerDescription&highlight=1)]

"설명" 속성이 이전 예제와 같이 동일한 방식으로 액세스 합니다.

### <a name="sample-modifying-the-actionmodel-description"></a>샘플: 수정 ActionModel 파일에 대 한 설명

이미 응용 프로그램 또는 컨트롤러 수준에서 적용 되는 동작을 재정의 하는 개별 작업에는 별도 특성 규칙을 적용할 수 있습니다.

[!code-csharp[Main](./application-model/sample/src/AppModelSample/Conventions/ActionDescriptionAttribute.cs)]

이전 예제에서는 컨트롤러 내 작업에 적용 하는이 컨트롤러 수준 규칙 재정의 방법을 보여 줍니다.

[!code-csharp[Main](./application-model/sample/src/AppModelSample/Controllers/DescriptionAttributesController.cs?name=DescriptionAttributesController&highlight=9)]

### <a name="sample-modifying-the-parametermodel"></a>샘플:는 ParameterModel 수정

수정할 작업 매개 변수에는 다음 규칙을 적용할 수 있습니다는 `BindingInfo`합니다. 다음과 같은 규칙 해야 하는 매개 변수 경로 매개 변수입니다. 다른 잠재적인 바인딩 소스 (예: 쿼리 문자열 값)은 무시 됩니다.

[!code-csharp[Main](./application-model/sample/src/AppModelSample/Conventions/MustBeInRouteParameterModelConvention.cs)]

모든 작업 매개 변수에 특성이 적용 될 수 있습니다.:

[!code-csharp[Main](./application-model/sample/src/AppModelSample/Controllers/ParameterModelController.cs?name=ParameterModelController&highlight=5)]

### <a name="sample-modifying-the-actionmodel-name"></a>샘플: ActionModel 이름 수정

다음 규칙을 수정는 `ActionModel` 업데이트 하는 *이름* 적용 되는 동작의 합니다. 새 이름 특성에 대 한 매개 변수로 제공 됩니다. 이 새 이름이 있으므로이 동작 메서드에 연결 하는 데 사용 된 경로 영향을 줍니다, 라우팅에 의해 사용 됩니다.

[!code-csharp[Main](./application-model/sample/src/AppModelSample/Conventions/CustomActionNameAttribute.cs)]

이 특성에는 동작 메서드에 적용 되는 `HomeController`:

[!code-csharp[Main](./application-model/sample/src/AppModelSample/Controllers/HomeController.cs?name=ActionModelConvention&highlight=2)]

메서드 이름이 `SomeName`, 메서드 이름을 사용 하 여 MVC 규칙을 재정의 하 고 사용 하 여 작업 이름을 대체 하는 특성 `MyCoolAction`합니다. 따라서이 작업에 도달 하는 데 사용 된 경로 `/Home/MyCoolAction`합니다.

> [!NOTE]
> 이 예제는 기본적으로 기본 제공을 사용할 때와 같은 [ActionName](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.actionnameattribute) 특성입니다.

### <a name="sample-custom-routing-convention"></a>예제: 사용자 지정 라우팅 규칙

사용할 수는 `IApplicationModelConvention` 라우팅의 작동 원리를 사용자 지정할 수 있습니다. 예를 들어 다음 규칙은 통합할 컨트롤러의 네임 스페이스 대체 해당 경로 `.` 포함 된 네임 스페이스에서 `/` 경로의:

[!code-csharp[Main](./application-model/sample/src/AppModelSample/Conventions/NamespaceRoutingConvention.cs)]

규칙에 따라 시작에 옵션으로 추가 됩니다.

[!code-csharp[Main](./application-model/sample/src/AppModelSample/Startup.cs?name=ConfigureServices&highlight=6)]

> [!TIP]
> 규칙을 추가할 수 있습니다 프로그램 [미들웨어](xref:fundamentals/middleware) 에 액세스 하 여 `MvcOptions` 를 사용 하 여`services.Configure<MvcOptions>(c => c.Conventions.Add(YOURCONVENTION));`

이 샘플에서 컨트롤러의 이름에 "Namespace"에 있는 특성 라우팅을 사용 하지 않는 경로에이 규칙을 적용 합니다. 다음 컨트롤러는이 규칙을 보여 줍니다.

[!code-csharp[Main](./application-model/sample/src/AppModelSample/Controllers/NamespaceRoutingController.cs?highlight=7-8)]

## <a name="application-model-usage-in-webapicompatshim"></a>WebApiCompatShim에서 응용 프로그램 모델 사용

ASP.NET Core MVC는 ASP.NET Web API 2에서 다른 규칙 집합을 사용합니다. 사용자 지정 규칙을 사용 하 여 Web API 응용 프로그램의 해당 값과 일치 되도록 ASP.NET Core MVC 응용 프로그램의 동작을 수정할 수 있습니다. 함께 제공 되며 Microsoft는 [WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim/) 이 용도로 합니다.

> [!NOTE]
> 에 대 한 자세한 내용은 [ASP.NET Web API에서 마이그레이션](xref:migration/webapi)합니다.

웹 API 호환성 Shim을 사용 하려면 프로젝트에 패키지를 추가 하 고 다음 호출 하 여 mvc의 규칙을 추가할 `AddWebApiConventions` 에 `Startup`:

```c#
services.AddMvc().AddWebApiConventions();
```

Shim에서 제공 하는 규칙을 특정 특성에 적용 된 적이 있는 응용 프로그램의 부분에만 적용 됩니다. 다음 네 가지 특성이 제어 컨트롤러 shim의 규칙에 의해 수정 하는 규칙을 포함 해야 하는 데 사용 됩니다.

* [UseWebApiActionConventionsAttribute](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.webapicompatshim.usewebapiactionconventionsattribute)
* [UseWebApiOverloadingAttribute](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.webapicompatshim.usewebapioverloadingattribute)
* [UseWebApiParameterConventionsAttribute](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.webapicompatshim.usewebapiparameterconventionsattribute)
* [UseWebApiRoutesAttribute](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.webapicompatshim.usewebapiroutesattribute)

### <a name="action-conventions"></a>작업 규칙

`UseWebApiActionConventionsAttribute` HTTP 메서드 이름에 따라 작업에 매핑할 때 사용 됩니다 (예를 들어, `Get` 을 매핑합니다 `HttpGet`). 특성 라우팅을 사용 하지 않는 작업에만 적용 됩니다.

### <a name="overloading"></a>오버로딩

`UseWebApiOverloadingAttribute` 적용 하는 데 사용 되는 `WebApiOverloadingApplicationModelConvention` 규칙입니다. 이 규칙에 추가 `OverloadActionConstraint` 후보 작업 요청을 모든 필수 매개 변수를 충족 하는 것을 제한 하는 동작 선택 프로세스를 합니다.

### <a name="parameter-conventions"></a>매개 변수 규칙

`UseWebApiParameterConventionsAttribute` 적용 하는 데 사용 되는 `WebApiParameterConventionsApplicationModelConvention` 동작 규칙입니다. 작업 매개 변수로 사용 되는 단순 형식에 바인딩됨을 URI에서 기본적으로 요청 본문에서 복합 형식은 바인딩된 하는 동안이 규칙을 지정 합니다.

### <a name="routes"></a>경로

`UseWebApiRoutesAttribute` 컨트롤 여부는 `WebApiApplicationModelConvention` 컨트롤러 규칙이 적용 됩니다. 이 규칙에 대 한 지원을 추가 하는 데 사용 사용 하도록 설정 하면 [영역](xref:mvc/controllers/areas) 경로에 있습니다.

규칙 집합을 외에도 호환성 패키지에는 `System.Web.Http.ApiController` 기본 클래스를 대체 하는 Web API에서 제공 합니다. 이렇게 하면 웹 API와 상속 용으로 작성 하 여 컨트롤러에서 해당 `ApiController` ASP.NET Core MVC에서 실행 되는 동안 디자인에 작동 합니다. 이 기본 컨트롤러 클래스의 모든로 데코레이팅된는 `UseWebApi*` 위에 나열 된 특성입니다. `ApiController` 속성, 메서드 및 웹 API에 있는 것과 호환 되는 결과 형식을 노출 합니다.

## <a name="using-apiexplorer-to-document-your-app"></a>ApiExplorer를 사용 하 여 앱을 문서화

응용 프로그램 모델을 노출 한 [ `ApiExplorer` ](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.applicationmodels.apiexplorermodel) 는 응용 프로그램의 구조를 전달 하는 데 사용할 수 있는 각 수준에서 속성입니다. 에 사용할 수 있습니다이 [Swagger와 같은 도구를 사용 하 여 Web Api에 대 한 도움말 페이지를 생성](https://docs.microsoft.com/aspnet/core/tutorials/web-api-help-pages-using-swagger)합니다. `ApiExplorer` 속성 노출은 `IsVisible` 노출 해야 하는 응용 프로그램의 모델의 부분을 지정 하려면 설정할 수 있는 속성입니다. 규칙을 사용 하 여이 설정을 구성할 수 있습니다.

[!code-csharp[Main](./application-model/sample/src/AppModelSample/Conventions/EnableApiExplorerApplicationConvention.cs)]

이 접근 방식 (및 필요한 경우 추가 규칙)를 사용 하거나 설정할 수 있습니다 앱 내에서 모든 수준에서 가시성 API를 사용 하지 않도록 설정 합니다. 
