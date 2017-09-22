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
# <a name="working-with-the-application-model"></a><span data-ttu-id="7c24e-103">응용 프로그램 모델 작업</span><span class="sxs-lookup"><span data-stu-id="7c24e-103">Working with the Application Model</span></span>

<span data-ttu-id="7c24e-104">으로 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="7c24e-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="7c24e-105">ASP.NET Core MVC 정의 *응용 프로그램 모델* MVC 응용 프로그램의 구성 요소를 나타내는입니다.</span><span class="sxs-lookup"><span data-stu-id="7c24e-105">ASP.NET Core MVC defines an *application model* representing the components of an MVC app.</span></span> <span data-ttu-id="7c24e-106">읽을 수 있으며 MVC 요소의 작동 방식을 수정 하려면이 모델을 조작할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7c24e-106">You can read and manipulate this model to modify how MVC elements behave.</span></span> <span data-ttu-id="7c24e-107">기본적으로 MVC는 클래스를 컨트롤러로 간주 되며, 이러한 클래스는 메서드는 다음 작업을 매개 변수 및 라우팅 동작 하는 방법을 결정 하는 특정 규칙을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="7c24e-107">By default, MVC follows certain conventions to determine which classes are considered to be controllers, which methods on those classes are actions, and how parameters and routing behave.</span></span> <span data-ttu-id="7c24e-108">이 동작을 사용자 지정 규칙을 만들고 전역으로 또는 특성으로 적용 하 여 응용 프로그램의 요구 사항에 맞게 사용자 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7c24e-108">You can customize this behavior to suit your app's needs by creating your own conventions and applying them globally or as attributes.</span></span>

## <a name="models-and-providers"></a><span data-ttu-id="7c24e-109">모델 및 공급자</span><span class="sxs-lookup"><span data-stu-id="7c24e-109">Models and Providers</span></span>

<span data-ttu-id="7c24e-110">ASP.NET Core MVC 응용 프로그램 모델에는 추상 인터페이스 및 MVC 응용 프로그램을 설명 하는 구체적인 구현인 클래스가 모두 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7c24e-110">The ASP.NET Core MVC application model include both abstract interfaces and concrete implementation classes that describe an MVC application.</span></span> <span data-ttu-id="7c24e-111">이 모델은 응용 프로그램의 컨트롤러, 작업, 작업 매개 변수, 경로 및 기본 규칙에 따라 필터를 검색 하는 MVC의 결과입니다.</span><span class="sxs-lookup"><span data-stu-id="7c24e-111">This model is the result of MVC discovering the app's controllers, actions, action parameters, routes, and filters according to default conventions.</span></span> <span data-ttu-id="7c24e-112">응용 프로그램 모델을 사용 하 여 기본 MVC 동작에서 다른 규칙을 따라야 응용 프로그램을 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7c24e-112">By working with the application model, you can modify your app to follow different conventions from the default MVC behavior.</span></span> <span data-ttu-id="7c24e-113">매개 변수, 이름, 경로 및 필터는 모두 데이터로 사용 구성 작업 및 컨트롤러에 대 한 합니다.</span><span class="sxs-lookup"><span data-stu-id="7c24e-113">The parameters, names, routes, and filters are all used as configuration data for actions and controllers.</span></span>

<span data-ttu-id="7c24e-114">ASP.NET Core MVC 응용 프로그램 모델의 구조는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="7c24e-114">The ASP.NET Core MVC Application Model has the following structure:</span></span>

* <span data-ttu-id="7c24e-115">ApplicationModel</span><span class="sxs-lookup"><span data-stu-id="7c24e-115">ApplicationModel</span></span>
    * <span data-ttu-id="7c24e-116">컨트롤러 (ControllerModel)</span><span class="sxs-lookup"><span data-stu-id="7c24e-116">Controllers (ControllerModel)</span></span>
        * <span data-ttu-id="7c24e-117">작업 (ActionModel)</span><span class="sxs-lookup"><span data-stu-id="7c24e-117">Actions (ActionModel)</span></span>
            * <span data-ttu-id="7c24e-118">매개 변수 (ParameterModel)</span><span class="sxs-lookup"><span data-stu-id="7c24e-118">Parameters (ParameterModel)</span></span>

<span data-ttu-id="7c24e-119">모델의 각 수준에는 공통 권한이 `Properties` 컬렉션 및 하위 수준 액세스 하 고 계층의 상위 수준에서 설정 된 속성 값을 덮어쓸 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7c24e-119">Each level of the model has access to a common `Properties` collection, and lower levels can access and overwrite property values set by higher levels in the hierarchy.</span></span> <span data-ttu-id="7c24e-120">속성에 저장 됩니다는 `ActionDescriptor.Properties` 작업을 만드는 시기입니다.</span><span class="sxs-lookup"><span data-stu-id="7c24e-120">The properties are persisted to the `ActionDescriptor.Properties` when the actions are created.</span></span> <span data-ttu-id="7c24e-121">다음 요청을 처리 중인 경우 모든 속성 추가 또는 수정 된 규칙을 통해 액세스할 수 `ActionContext.ActionDescriptor.Properties`합니다.</span><span class="sxs-lookup"><span data-stu-id="7c24e-121">Then when a request is being handled, any properties a convention added or modified can be accessed through `ActionContext.ActionDescriptor.Properties`.</span></span> <span data-ttu-id="7c24e-122">속성을 사용 하는 필터, 모델 바인더, 등-액션에 구성할 수 있는 좋은 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="7c24e-122">Using properties is a great way to configure your filters, model binders, etc. on a per-action basis.</span></span>

> [!NOTE]
> <span data-ttu-id="7c24e-123">`ActionDescriptor.Properties` 컬렉션 스레드로부터 안전 하지 않습니다 (쓰기)에 대 한 응용 프로그램 시작을 완료 했습니다.</span><span class="sxs-lookup"><span data-stu-id="7c24e-123">The `ActionDescriptor.Properties` collection is not thread safe (for writes) once app startup has finished.</span></span> <span data-ttu-id="7c24e-124">규칙은 안전 하 게 데이터를이 컬렉션에 추가 하는 가장 좋은 방법은 합니다.</span><span class="sxs-lookup"><span data-stu-id="7c24e-124">Conventions are the best way to safely add data to this collection.</span></span>

### <a name="iapplicationmodelprovider"></a><span data-ttu-id="7c24e-125">IApplicationModelProvider</span><span class="sxs-lookup"><span data-stu-id="7c24e-125">IApplicationModelProvider</span></span>

<span data-ttu-id="7c24e-126">정의한 공급자 패턴을 사용 하는 응용 프로그램 모델을 로드 하는 ASP.NET Core MVC는 [IApplicationModelProvider](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.applicationmodels.iapplicationmodelprovider) 인터페이스입니다.</span><span class="sxs-lookup"><span data-stu-id="7c24e-126">ASP.NET Core MVC loads the application model using a provider pattern, defined by the [IApplicationModelProvider](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.applicationmodels.iapplicationmodelprovider) interface.</span></span> <span data-ttu-id="7c24e-127">이 섹션에서는 일부의 방법의 내부 구현 세부 정보를 다룹니다이 공급자 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="7c24e-127">This section covers some of the internal implementation details of how this provider functions.</span></span> <span data-ttu-id="7c24e-128">이 고급 항목-응용 프로그램 모델을 활용 하는 대부분의 응용 프로그램 규칙을 사용 하 여 수행 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7c24e-128">This is an advanced topic - most apps that leverage the application model should do so by working with conventions.</span></span>

<span data-ttu-id="7c24e-129">구현에서 `IApplicationModelProvider` 인터페이스 "" 서로로 래핑합니다 각 구현 호출 `OnProvidersExecuting` 오름차순으로 정렬 순서에 따라 해당 `Order` 속성.</span><span class="sxs-lookup"><span data-stu-id="7c24e-129">Implementations of the `IApplicationModelProvider` interface "wrap" one another, with each implementation calling `OnProvidersExecuting` in ascending order based on its `Order` property.</span></span> <span data-ttu-id="7c24e-130">`OnProvidersExecuted` 메서드 반대 순서로 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7c24e-130">The `OnProvidersExecuted` method is then called in reverse order.</span></span> <span data-ttu-id="7c24e-131">프레임 워크 여러 공급자를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="7c24e-131">The framework defines several providers:</span></span>

<span data-ttu-id="7c24e-132">첫 번째 (`Order=-1000`):</span><span class="sxs-lookup"><span data-stu-id="7c24e-132">First (`Order=-1000`):</span></span>

* [`DefaultApplicationModelProvider`](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.internal.defaultapplicationmodelprovider)

<span data-ttu-id="7c24e-133">다음 (`Order=-990`):</span><span class="sxs-lookup"><span data-stu-id="7c24e-133">Then (`Order=-990`):</span></span>

* [`AuthorizationApplicationModelProvider`](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.internal.authorizationapplicationmodelprovider)
* [`CorsApplicationModelProvider`](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.cors.internal.corsapplicationmodelprovider)

> [!NOTE]
> <span data-ttu-id="7c24e-134">순서에 대해 같은 값으로는 두 공급자 `Order` 라고 따라서 해야에 의존 하 고이 정의 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7c24e-134">The order in which two providers with the same value for `Order` are called is undefined, and therefore should not be relied upon.</span></span>

> [!NOTE]
> <span data-ttu-id="7c24e-135">`IApplicationModelProvider`프레임 워크를 확장 하는 작성자를 위한 고급 개념이입니다.</span><span class="sxs-lookup"><span data-stu-id="7c24e-135">`IApplicationModelProvider` is an advanced concept for framework authors to extend.</span></span> <span data-ttu-id="7c24e-136">일반적으로 앱 규칙을 사용 해야 하 고 프레임 워크 공급자를 사용 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7c24e-136">In general, apps should use conventions and frameworks should use providers.</span></span> <span data-ttu-id="7c24e-137">중요 한 차이점은 규칙 하기 전에 공급자는 항상 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="7c24e-137">The key distinction is that providers always run before conventions.</span></span>

<span data-ttu-id="7c24e-138">`DefaultApplicationModelProvider` ASP.NET Core MVC에서 사용 하는 기본 동작을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7c24e-138">The `DefaultApplicationModelProvider` establishes many of the default behaviors used by ASP.NET Core MVC.</span></span> <span data-ttu-id="7c24e-139">책임은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="7c24e-139">Its responsibilities include:</span></span>

* <span data-ttu-id="7c24e-140">컨텍스트를 전역 필터를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7c24e-140">Adding global filters to the context</span></span>
* <span data-ttu-id="7c24e-141">컨트롤러는 컨텍스트를 추가</span><span class="sxs-lookup"><span data-stu-id="7c24e-141">Adding controllers to the context</span></span>
* <span data-ttu-id="7c24e-142">동작으로 공용 컨트롤러 메서드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7c24e-142">Adding public controller methods as actions</span></span>
* <span data-ttu-id="7c24e-143">컨텍스트에 동작 메서드 매개 변수 추가</span><span class="sxs-lookup"><span data-stu-id="7c24e-143">Adding action method parameters to the context</span></span>
* <span data-ttu-id="7c24e-144">경로 및 기타 특성 적용</span><span class="sxs-lookup"><span data-stu-id="7c24e-144">Applying route and other attributes</span></span>

<span data-ttu-id="7c24e-145">일부 기본 제공 동작에 의해 구현 되는 `DefaultApplicationModelProvider`합니다.</span><span class="sxs-lookup"><span data-stu-id="7c24e-145">Some built-in behaviors are implemented by the `DefaultApplicationModelProvider`.</span></span> <span data-ttu-id="7c24e-146">이 공급자는 생성 해야는 [ `ControllerModel` ](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.applicationmodels.controllermodel)를 차례로 참조 하는 [ `ActionModel` ](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.applicationmodels.actionmodel#Microsoft_AspNetCore_Mvc_ApplicationModels_ActionModel), [ `PropertyModel` ](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.applicationmodels.propertymodel), 및 [ `ParameterModel` ](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.applicationmodels.parametermodel#Microsoft_AspNetCore_Mvc_ApplicationModels_ParameterModel) 인스턴스.</span><span class="sxs-lookup"><span data-stu-id="7c24e-146">This provider is responsible for constructing the [`ControllerModel`](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.applicationmodels.controllermodel), which in turn references [`ActionModel`](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.applicationmodels.actionmodel#Microsoft_AspNetCore_Mvc_ApplicationModels_ActionModel), [`PropertyModel`](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.applicationmodels.propertymodel), and [`ParameterModel`](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.applicationmodels.parametermodel#Microsoft_AspNetCore_Mvc_ApplicationModels_ParameterModel) instances.</span></span> <span data-ttu-id="7c24e-147">`DefaultApplicationModelProvider` 클래스는 하 고 나중에 변경 될 수 있는 프레임 워크 내부 구현 세부 정보.</span><span class="sxs-lookup"><span data-stu-id="7c24e-147">The `DefaultApplicationModelProvider` class is an internal framework implementation detail that can and will change in the future.</span></span> 

<span data-ttu-id="7c24e-148">`AuthorizationApplicationModelProvider` 와 연결 된 동작을 적용 합니다는 `AuthorizeFilter` 및 `AllowAnonymousFilter` 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="7c24e-148">The `AuthorizationApplicationModelProvider` is responsible for applying the behavior associated with the `AuthorizeFilter` and `AllowAnonymousFilter` attributes.</span></span> <span data-ttu-id="7c24e-149">[이러한 특성에 대 한 자세한](xref:security/authorization/simple)합니다.</span><span class="sxs-lookup"><span data-stu-id="7c24e-149">[Learn more about these attributes](xref:security/authorization/simple).</span></span>

<span data-ttu-id="7c24e-150">`CorsApplicationModelProvider` 와 연결 된 동작을 구현 하는 `IEnableCorsAttribute` 및 `IDisableCorsAttribute`, 및 `DisableCorsAuthorizationFilter`합니다.</span><span class="sxs-lookup"><span data-stu-id="7c24e-150">The `CorsApplicationModelProvider` implements behavior associated with the `IEnableCorsAttribute` and `IDisableCorsAttribute`, and the `DisableCorsAuthorizationFilter`.</span></span> <span data-ttu-id="7c24e-151">[CORS에 대 한 자세한](xref:security/cors)합니다.</span><span class="sxs-lookup"><span data-stu-id="7c24e-151">[Learn more about CORS](xref:security/cors).</span></span>

## <a name="conventions"></a><span data-ttu-id="7c24e-152">규칙</span><span class="sxs-lookup"><span data-stu-id="7c24e-152">Conventions</span></span>

<span data-ttu-id="7c24e-153">응용 프로그램 모델 전체 모델 또는 공급자를 재정의 하는 보다는 모델의 동작을 사용자 지정 하는 간단한 방법을 제공 하는 규칙 추상화를 정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="7c24e-153">The application model defines convention abstractions that provide a simpler way to customize the behavior of the models than overriding the entire model or provider.</span></span> <span data-ttu-id="7c24e-154">이러한 추상화는 앱의 동작을 수정 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="7c24e-154">These abstractions are the recommended way to modify your app's behavior.</span></span> <span data-ttu-id="7c24e-155">규칙에는 사용자 지정에 동적으로 적용 되는 코드를 작성할 수 있는 방법을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="7c24e-155">Conventions provide a way for you to write code that will dynamically apply customizations.</span></span> <span data-ttu-id="7c24e-156">반면 [필터](xref:mvc/controllers/filters) 프레임 워크의 동작을 수정 하 게, 사용자 지정 제어할 수 있도록 전체 응용 프로그램 함께 연결 합니다.</span><span class="sxs-lookup"><span data-stu-id="7c24e-156">While [filters](xref:mvc/controllers/filters) provide a means of modifying the framework's behavior, customizations let you control how the whole app is wired together.</span></span>

<span data-ttu-id="7c24e-157">다음과 같은 규칙을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7c24e-157">The following conventions are available:</span></span>

* [`IApplicationModelConvention`](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.applicationmodels.iapplicationmodelconvention)
* [`IControllerModelConvention`](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.applicationmodels.icontrollermodelconvention)
* [`IActionModelConvention`](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.applicationmodels.iactionmodelconvention)
* [`IParameterModelConvention`](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.applicationmodels.iparametermodelconvention)

<span data-ttu-id="7c24e-158">MVC 옵션을 추가 하거나 구현 하 여 규칙을 적용할 `Attribute`s 및 컨트롤러, 동작 또는 액션 매개 변수를에 적용 (비슷합니다 [ `Filters` ](xref:mvc/controllers/filters)).</span><span class="sxs-lookup"><span data-stu-id="7c24e-158">Conventions are applied by adding them to MVC options or by implementing `Attribute`s and applying them to controllers, actions, or action parameters (similar to [`Filters`](xref:mvc/controllers/filters)).</span></span> <span data-ttu-id="7c24e-159">필터와 달리 각 요청의 일부가 아니라 응용 프로그램을 시작 하는 경우 규칙 실행만 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7c24e-159">Unlike filters, conventions are only executed when the app is starting, not as part of each request.</span></span>

### <a name="sample-modifying-the-applicationmodel"></a><span data-ttu-id="7c24e-160">샘플:는 ApplicationModel 수정</span><span class="sxs-lookup"><span data-stu-id="7c24e-160">Sample: Modifying the ApplicationModel</span></span>

<span data-ttu-id="7c24e-161">다음 규칙은 응용 프로그램 모델에 속성을 추가 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7c24e-161">The following convention is used to add a property to the application model.</span></span> 

[!code-csharp[Main](./application-model/sample/src/AppModelSample/Conventions/ApplicationDescription.cs)]

<span data-ttu-id="7c24e-162">MVC에 추가 될 때 옵션으로 응용 프로그램 모델 규칙을 적용할 `ConfigureServices` 에서 `Startup`합니다.</span><span class="sxs-lookup"><span data-stu-id="7c24e-162">Application model conventions are applied as options when MVC is added in `ConfigureServices` in `Startup`.</span></span>

[!code-csharp[Main](./application-model/sample/src/AppModelSample/Startup.cs?name=ConfigureServices&highlight=5)]

<span data-ttu-id="7c24e-163">속성은에서 액세스할 수는 `ActionDescriptor` 컨트롤러 작업 내에서 properties 컬렉션:</span><span class="sxs-lookup"><span data-stu-id="7c24e-163">Properties are accessible from the `ActionDescriptor` properties collection within controller actions:</span></span>

[!code-csharp[Main](./application-model/sample/src/AppModelSample/Controllers/AppModelController.cs?name=AppModelController)]

### <a name="sample-modifying-the-controllermodel-description"></a><span data-ttu-id="7c24e-164">샘플: 수정 ControllerModel 파일에 대 한 설명</span><span class="sxs-lookup"><span data-stu-id="7c24e-164">Sample: Modifying the ControllerModel Description</span></span>

<span data-ttu-id="7c24e-165">앞의 예와 컨트롤러 모델 사용자 지정 속성을 포함 하려면 수정할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7c24e-165">As in the previous example, the controller model can also be modified to include custom properties.</span></span> <span data-ttu-id="7c24e-166">이러한 응용 프로그램 모델에 지정 된 동일한 이름의 기존 속성을 덮어씁니다.</span><span class="sxs-lookup"><span data-stu-id="7c24e-166">These will override existing properties with the same name specified in the application model.</span></span> <span data-ttu-id="7c24e-167">컨트롤러 수준에서 다음 규칙 특성에 대 한 설명을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7c24e-167">The following convention attribute adds a description at the controller level:</span></span>

[!code-csharp[Main](./application-model/sample/src/AppModelSample/Conventions/ControllerDescriptionAttribute.cs)]

<span data-ttu-id="7c24e-168">이 규칙은 컨트롤러에 대 한 특성으로 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7c24e-168">This convention is applied as an attribute on a controller.</span></span>

[!code-csharp[Main](./application-model/sample/src/AppModelSample/Controllers/DescriptionAttributesController.cs?name=ControllerDescription&highlight=1)]

<span data-ttu-id="7c24e-169">"설명" 속성이 이전 예제와 같이 동일한 방식으로 액세스 합니다.</span><span class="sxs-lookup"><span data-stu-id="7c24e-169">The "description" property is accessed in the same manner as in previous examples.</span></span>

### <a name="sample-modifying-the-actionmodel-description"></a><span data-ttu-id="7c24e-170">샘플: 수정 ActionModel 파일에 대 한 설명</span><span class="sxs-lookup"><span data-stu-id="7c24e-170">Sample: Modifying the ActionModel Description</span></span>

<span data-ttu-id="7c24e-171">이미 응용 프로그램 또는 컨트롤러 수준에서 적용 되는 동작을 재정의 하는 개별 작업에는 별도 특성 규칙을 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7c24e-171">A separate attribute convention can be applied to individual actions, overriding behavior already applied at the application or controller level.</span></span>

[!code-csharp[Main](./application-model/sample/src/AppModelSample/Conventions/ActionDescriptionAttribute.cs)]

<span data-ttu-id="7c24e-172">이전 예제에서는 컨트롤러 내 작업에 적용 하는이 컨트롤러 수준 규칙 재정의 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="7c24e-172">Applying this to an action within the previous example's controller demonstrates how it overrides the controller-level convention:</span></span>

[!code-csharp[Main](./application-model/sample/src/AppModelSample/Controllers/DescriptionAttributesController.cs?name=DescriptionAttributesController&highlight=9)]

### <a name="sample-modifying-the-parametermodel"></a><span data-ttu-id="7c24e-173">샘플:는 ParameterModel 수정</span><span class="sxs-lookup"><span data-stu-id="7c24e-173">Sample: Modifying the ParameterModel</span></span>

<span data-ttu-id="7c24e-174">수정할 작업 매개 변수에는 다음 규칙을 적용할 수 있습니다는 `BindingInfo`합니다.</span><span class="sxs-lookup"><span data-stu-id="7c24e-174">The following convention can be applied to action parameters to modify their `BindingInfo`.</span></span> <span data-ttu-id="7c24e-175">다음과 같은 규칙 해야 하는 매개 변수 경로 매개 변수입니다. 다른 잠재적인 바인딩 소스 (예: 쿼리 문자열 값)은 무시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7c24e-175">The following convention requires that the parameter be a route parameter; other potential binding sources (such as query string values) are ignored.</span></span>

[!code-csharp[Main](./application-model/sample/src/AppModelSample/Conventions/MustBeInRouteParameterModelConvention.cs)]

<span data-ttu-id="7c24e-176">모든 작업 매개 변수에 특성이 적용 될 수 있습니다.:</span><span class="sxs-lookup"><span data-stu-id="7c24e-176">The attribute may be applied to any action parameter:</span></span>

[!code-csharp[Main](./application-model/sample/src/AppModelSample/Controllers/ParameterModelController.cs?name=ParameterModelController&highlight=5)]

### <a name="sample-modifying-the-actionmodel-name"></a><span data-ttu-id="7c24e-177">샘플: ActionModel 이름 수정</span><span class="sxs-lookup"><span data-stu-id="7c24e-177">Sample: Modifying the ActionModel Name</span></span>

<span data-ttu-id="7c24e-178">다음 규칙을 수정는 `ActionModel` 업데이트 하는 *이름* 적용 되는 동작의 합니다.</span><span class="sxs-lookup"><span data-stu-id="7c24e-178">The following convention modifies the `ActionModel` to update the *name* of the action to which it is applied.</span></span> <span data-ttu-id="7c24e-179">새 이름 특성에 대 한 매개 변수로 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7c24e-179">The new name is provided as a parameter to the attribute.</span></span> <span data-ttu-id="7c24e-180">이 새 이름이 있으므로이 동작 메서드에 연결 하는 데 사용 된 경로 영향을 줍니다, 라우팅에 의해 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7c24e-180">This new name is used by routing, so it will affect the route used to reach this action method.</span></span>

[!code-csharp[Main](./application-model/sample/src/AppModelSample/Conventions/CustomActionNameAttribute.cs)]

<span data-ttu-id="7c24e-181">이 특성에는 동작 메서드에 적용 되는 `HomeController`:</span><span class="sxs-lookup"><span data-stu-id="7c24e-181">This attribute is applied to an action method in the `HomeController`:</span></span>

[!code-csharp[Main](./application-model/sample/src/AppModelSample/Controllers/HomeController.cs?name=ActionModelConvention&highlight=2)]

<span data-ttu-id="7c24e-182">메서드 이름이 `SomeName`, 메서드 이름을 사용 하 여 MVC 규칙을 재정의 하 고 사용 하 여 작업 이름을 대체 하는 특성 `MyCoolAction`합니다.</span><span class="sxs-lookup"><span data-stu-id="7c24e-182">Even though the method name is `SomeName`, the attribute overrides the MVC convention of using the method name and replaces the action name with `MyCoolAction`.</span></span> <span data-ttu-id="7c24e-183">따라서이 작업에 도달 하는 데 사용 된 경로 `/Home/MyCoolAction`합니다.</span><span class="sxs-lookup"><span data-stu-id="7c24e-183">Thus, the route used to reach this action is `/Home/MyCoolAction`.</span></span>

> [!NOTE]
> <span data-ttu-id="7c24e-184">이 예제는 기본적으로 기본 제공을 사용할 때와 같은 [ActionName](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.actionnameattribute) 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="7c24e-184">This example is essentially the same as using the built-in [ActionName](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.actionnameattribute) attribute.</span></span>

### <a name="sample-custom-routing-convention"></a><span data-ttu-id="7c24e-185">예제: 사용자 지정 라우팅 규칙</span><span class="sxs-lookup"><span data-stu-id="7c24e-185">Sample: Custom Routing Convention</span></span>

<span data-ttu-id="7c24e-186">사용할 수는 `IApplicationModelConvention` 라우팅의 작동 원리를 사용자 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7c24e-186">You can use an `IApplicationModelConvention` to customize how routing works.</span></span> <span data-ttu-id="7c24e-187">예를 들어 다음 규칙은 통합할 컨트롤러의 네임 스페이스 대체 해당 경로 `.` 포함 된 네임 스페이스에서 `/` 경로의:</span><span class="sxs-lookup"><span data-stu-id="7c24e-187">For example, the following convention will incorporate Controllers' namespaces into their routes, replacing `.` in the namespace with `/` in the route:</span></span>

[!code-csharp[Main](./application-model/sample/src/AppModelSample/Conventions/NamespaceRoutingConvention.cs)]

<span data-ttu-id="7c24e-188">규칙에 따라 시작에 옵션으로 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7c24e-188">The convention is added as an option in Startup.</span></span>

[!code-csharp[Main](./application-model/sample/src/AppModelSample/Startup.cs?name=ConfigureServices&highlight=6)]

> [!TIP]
> <span data-ttu-id="7c24e-189">규칙을 추가할 수 있습니다 프로그램 [미들웨어](xref:fundamentals/middleware) 에 액세스 하 여 `MvcOptions` 를 사용 하 여`services.Configure<MvcOptions>(c => c.Conventions.Add(YOURCONVENTION));`</span><span class="sxs-lookup"><span data-stu-id="7c24e-189">You can add conventions to your [middleware](xref:fundamentals/middleware) by accessing `MvcOptions` using `services.Configure<MvcOptions>(c => c.Conventions.Add(YOURCONVENTION));`</span></span>

<span data-ttu-id="7c24e-190">이 샘플에서 컨트롤러의 이름에 "Namespace"에 있는 특성 라우팅을 사용 하지 않는 경로에이 규칙을 적용 합니다.</span><span class="sxs-lookup"><span data-stu-id="7c24e-190">This sample applies this convention to routes that are not using attribute routing where the controller has  "Namespace" in its name.</span></span> <span data-ttu-id="7c24e-191">다음 컨트롤러는이 규칙을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="7c24e-191">The following controller demonstrates this convention:</span></span>

[!code-csharp[Main](./application-model/sample/src/AppModelSample/Controllers/NamespaceRoutingController.cs?highlight=7-8)]

## <a name="application-model-usage-in-webapicompatshim"></a><span data-ttu-id="7c24e-192">WebApiCompatShim에서 응용 프로그램 모델 사용</span><span class="sxs-lookup"><span data-stu-id="7c24e-192">Application Model Usage in WebApiCompatShim</span></span>

<span data-ttu-id="7c24e-193">ASP.NET Core MVC는 ASP.NET Web API 2에서 다른 규칙 집합을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7c24e-193">ASP.NET Core MVC uses a different set of conventions from ASP.NET Web API 2.</span></span> <span data-ttu-id="7c24e-194">사용자 지정 규칙을 사용 하 여 Web API 응용 프로그램의 해당 값과 일치 되도록 ASP.NET Core MVC 응용 프로그램의 동작을 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7c24e-194">Using custom conventions, you can modify an ASP.NET Core MVC app's behavior to be consistent with that of a Web API app.</span></span> <span data-ttu-id="7c24e-195">함께 제공 되며 Microsoft는 [WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim/) 이 용도로 합니다.</span><span class="sxs-lookup"><span data-stu-id="7c24e-195">Microsoft ships the [WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim/) specifically for this purpose.</span></span>

> [!NOTE]
> <span data-ttu-id="7c24e-196">에 대 한 자세한 내용은 [ASP.NET Web API에서 마이그레이션](xref:migration/webapi)합니다.</span><span class="sxs-lookup"><span data-stu-id="7c24e-196">Learn more about [migrating from ASP.NET Web API](xref:migration/webapi).</span></span>

<span data-ttu-id="7c24e-197">웹 API 호환성 Shim을 사용 하려면 프로젝트에 패키지를 추가 하 고 다음 호출 하 여 mvc의 규칙을 추가할 `AddWebApiConventions` 에 `Startup`:</span><span class="sxs-lookup"><span data-stu-id="7c24e-197">To use the Web API Compatibility Shim, you need to add the package to your project and then add the conventions to MVC by calling `AddWebApiConventions` in `Startup`:</span></span>

```c#
services.AddMvc().AddWebApiConventions();
```

<span data-ttu-id="7c24e-198">Shim에서 제공 하는 규칙을 특정 특성에 적용 된 적이 있는 응용 프로그램의 부분에만 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7c24e-198">The conventions provided by the shim are only applied to parts of the app that have had certain attributes applied to them.</span></span> <span data-ttu-id="7c24e-199">다음 네 가지 특성이 제어 컨트롤러 shim의 규칙에 의해 수정 하는 규칙을 포함 해야 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7c24e-199">The following four attributes are used to control which controllers should have their conventions modified by the shim's conventions:</span></span>

* [<span data-ttu-id="7c24e-200">UseWebApiActionConventionsAttribute</span><span class="sxs-lookup"><span data-stu-id="7c24e-200">UseWebApiActionConventionsAttribute</span></span>](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.webapicompatshim.usewebapiactionconventionsattribute)
* [<span data-ttu-id="7c24e-201">UseWebApiOverloadingAttribute</span><span class="sxs-lookup"><span data-stu-id="7c24e-201">UseWebApiOverloadingAttribute</span></span>](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.webapicompatshim.usewebapioverloadingattribute)
* [<span data-ttu-id="7c24e-202">UseWebApiParameterConventionsAttribute</span><span class="sxs-lookup"><span data-stu-id="7c24e-202">UseWebApiParameterConventionsAttribute</span></span>](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.webapicompatshim.usewebapiparameterconventionsattribute)
* [<span data-ttu-id="7c24e-203">UseWebApiRoutesAttribute</span><span class="sxs-lookup"><span data-stu-id="7c24e-203">UseWebApiRoutesAttribute</span></span>](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.webapicompatshim.usewebapiroutesattribute)

### <a name="action-conventions"></a><span data-ttu-id="7c24e-204">작업 규칙</span><span class="sxs-lookup"><span data-stu-id="7c24e-204">Action Conventions</span></span>

<span data-ttu-id="7c24e-205">`UseWebApiActionConventionsAttribute` HTTP 메서드 이름에 따라 작업에 매핑할 때 사용 됩니다 (예를 들어, `Get` 을 매핑합니다 `HttpGet`).</span><span class="sxs-lookup"><span data-stu-id="7c24e-205">The `UseWebApiActionConventionsAttribute` is used to map the HTTP method to actions based on their name (for instance, `Get` would map to `HttpGet`).</span></span> <span data-ttu-id="7c24e-206">특성 라우팅을 사용 하지 않는 작업에만 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7c24e-206">It only applies to actions that do not use attribute routing.</span></span>

### <a name="overloading"></a><span data-ttu-id="7c24e-207">오버로딩</span><span class="sxs-lookup"><span data-stu-id="7c24e-207">Overloading</span></span>

<span data-ttu-id="7c24e-208">`UseWebApiOverloadingAttribute` 적용 하는 데 사용 되는 `WebApiOverloadingApplicationModelConvention` 규칙입니다.</span><span class="sxs-lookup"><span data-stu-id="7c24e-208">The `UseWebApiOverloadingAttribute` is used to apply the `WebApiOverloadingApplicationModelConvention` convention.</span></span> <span data-ttu-id="7c24e-209">이 규칙에 추가 `OverloadActionConstraint` 후보 작업 요청을 모든 필수 매개 변수를 충족 하는 것을 제한 하는 동작 선택 프로세스를 합니다.</span><span class="sxs-lookup"><span data-stu-id="7c24e-209">This convention adds an `OverloadActionConstraint` to the action selection process, which limits candidate actions to those for which the request satisfies all non-optional parameters.</span></span>

### <a name="parameter-conventions"></a><span data-ttu-id="7c24e-210">매개 변수 규칙</span><span class="sxs-lookup"><span data-stu-id="7c24e-210">Parameter Conventions</span></span>

<span data-ttu-id="7c24e-211">`UseWebApiParameterConventionsAttribute` 적용 하는 데 사용 되는 `WebApiParameterConventionsApplicationModelConvention` 동작 규칙입니다.</span><span class="sxs-lookup"><span data-stu-id="7c24e-211">The `UseWebApiParameterConventionsAttribute` is used to apply the `WebApiParameterConventionsApplicationModelConvention` action convention.</span></span> <span data-ttu-id="7c24e-212">작업 매개 변수로 사용 되는 단순 형식에 바인딩됨을 URI에서 기본적으로 요청 본문에서 복합 형식은 바인딩된 하는 동안이 규칙을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7c24e-212">This convention specifies that simple types used as action parameters are bound from the URI by default, while complex types are bound from the request body.</span></span>

### <a name="routes"></a><span data-ttu-id="7c24e-213">경로</span><span class="sxs-lookup"><span data-stu-id="7c24e-213">Routes</span></span>

<span data-ttu-id="7c24e-214">`UseWebApiRoutesAttribute` 컨트롤 여부는 `WebApiApplicationModelConvention` 컨트롤러 규칙이 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7c24e-214">The `UseWebApiRoutesAttribute` controls whether the `WebApiApplicationModelConvention` controller convention is applied.</span></span> <span data-ttu-id="7c24e-215">이 규칙에 대 한 지원을 추가 하는 데 사용 사용 하도록 설정 하면 [영역](xref:mvc/controllers/areas) 경로에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7c24e-215">When enabled, this convention is used to add support for [areas](xref:mvc/controllers/areas) to the route.</span></span>

<span data-ttu-id="7c24e-216">규칙 집합을 외에도 호환성 패키지에는 `System.Web.Http.ApiController` 기본 클래스를 대체 하는 Web API에서 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="7c24e-216">In addition to a set of conventions, the compatibility package includes a `System.Web.Http.ApiController` base class that replaces the one provided by Web API.</span></span> <span data-ttu-id="7c24e-217">이렇게 하면 웹 API와 상속 용으로 작성 하 여 컨트롤러에서 해당 `ApiController` ASP.NET Core MVC에서 실행 되는 동안 디자인에 작동 합니다.</span><span class="sxs-lookup"><span data-stu-id="7c24e-217">This allows your controllers written for Web API and inheriting from its `ApiController` to work as they were designed, while running on ASP.NET Core MVC.</span></span> <span data-ttu-id="7c24e-218">이 기본 컨트롤러 클래스의 모든로 데코레이팅된는 `UseWebApi*` 위에 나열 된 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="7c24e-218">This base controller class is decorated with all of the `UseWebApi*` attributes listed above.</span></span> <span data-ttu-id="7c24e-219">`ApiController` 속성, 메서드 및 웹 API에 있는 것과 호환 되는 결과 형식을 노출 합니다.</span><span class="sxs-lookup"><span data-stu-id="7c24e-219">The `ApiController` exposes properties, methods, and result types that are compatible with those found in Web API.</span></span>

## <a name="using-apiexplorer-to-document-your-app"></a><span data-ttu-id="7c24e-220">ApiExplorer를 사용 하 여 앱을 문서화</span><span class="sxs-lookup"><span data-stu-id="7c24e-220">Using ApiExplorer to Document Your App</span></span>

<span data-ttu-id="7c24e-221">응용 프로그램 모델을 노출 한 [ `ApiExplorer` ](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.applicationmodels.apiexplorermodel) 는 응용 프로그램의 구조를 전달 하는 데 사용할 수 있는 각 수준에서 속성입니다.</span><span class="sxs-lookup"><span data-stu-id="7c24e-221">The application model exposes an [`ApiExplorer`](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.applicationmodels.apiexplorermodel) property at each level that can be used to traverse the app's structure.</span></span> <span data-ttu-id="7c24e-222">에 사용할 수 있습니다이 [Swagger와 같은 도구를 사용 하 여 Web Api에 대 한 도움말 페이지를 생성](https://docs.microsoft.com/aspnet/core/tutorials/web-api-help-pages-using-swagger)합니다.</span><span class="sxs-lookup"><span data-stu-id="7c24e-222">This can be used to [generate help pages for your Web APIs using tools like Swagger](https://docs.microsoft.com/aspnet/core/tutorials/web-api-help-pages-using-swagger).</span></span> <span data-ttu-id="7c24e-223">`ApiExplorer` 속성 노출은 `IsVisible` 노출 해야 하는 응용 프로그램의 모델의 부분을 지정 하려면 설정할 수 있는 속성입니다.</span><span class="sxs-lookup"><span data-stu-id="7c24e-223">The `ApiExplorer` property exposes an `IsVisible` property that can be set to specify which parts of your app's model should be exposed.</span></span> <span data-ttu-id="7c24e-224">규칙을 사용 하 여이 설정을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7c24e-224">You can configure this setting using a convention:</span></span>

[!code-csharp[Main](./application-model/sample/src/AppModelSample/Conventions/EnableApiExplorerApplicationConvention.cs)]

<span data-ttu-id="7c24e-225">이 접근 방식 (및 필요한 경우 추가 규칙)를 사용 하거나 설정할 수 있습니다 앱 내에서 모든 수준에서 가시성 API를 사용 하지 않도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7c24e-225">Using this approach (and additional conventions if required), you can enable or disable API visibility at any level within your app.</span></span> 
