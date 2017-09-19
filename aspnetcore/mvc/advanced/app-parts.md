---
title: "ASP.NET Core 응용 프로그램 파트"
author: ardalis
description: "검색 하거나 어셈블리에서 기능을 로드 하지 않도록 앱을 구성 하는 응용 프로그램의 리소스에 대해 abstrations 인 응용 프로그램 파트를 사용 하는 방법을 알아봅니다."
keywords: "ASP.NET Core 응용 프로그램 부분, 부분 응용 프로그램"
ms.author: riande
manager: wpickett
ms.date: 01/04/2017
ms.topic: article
ms.assetid: b355a48e-a15c-4d58-b69c-899963613a98
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/extensibility/app-parts
ms.openlocfilehash: 77d3a58d58493bf1b0b760ab9037d2778ba23441
ms.sourcegitcommit: 67f54fabbfa4e3942f5bfe1f8a7fdfe4a7a75358
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/19/2017
---
# <a name="application-parts-in-aspnet-core"></a><span data-ttu-id="9630f-104">ASP.NET Core 응용 프로그램 파트</span><span class="sxs-lookup"><span data-stu-id="9630f-104">Application Parts in ASP.NET Core</span></span>

[<span data-ttu-id="9630f-105">샘플 코드 보기 또는 다운로드</span><span class="sxs-lookup"><span data-stu-id="9630f-105">View or download sample code</span></span>](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample)

<span data-ttu-id="9630f-106">*응용 프로그램 부분* 있는 MVC 컨트롤러를 뷰 구성 요소와 같은 기능을 응용 프로그램의 리소스에 대 한 추상화 되었거나 태그 도우미를 검색할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9630f-106">An *Application Part* is an abstraction over the resources of an application, from which MVC features like controllers, view components, or tag helpers may be discovered.</span></span> <span data-ttu-id="9630f-107">응용 프로그램 파트의 한 가지 예는 AssemblyPart 어셈블리 참조 및 표시 유형 및 컴파일 참조를 캡슐화 하는입니다.</span><span class="sxs-lookup"><span data-stu-id="9630f-107">One example of an application part is an AssemblyPart, which encapsulates an assembly reference and exposes types and compilation references.</span></span> <span data-ttu-id="9630f-108">*공급자 기능* 응용 프로그램 부분을 ASP.NET Core MVC 응용 프로그램의 기능을 채우는 데 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="9630f-108">*Feature providers* work with application parts to populate the features of an ASP.NET Core MVC app.</span></span> <span data-ttu-id="9630f-109">응용 프로그램 부분에 대 한 주요 사용 사례 검색 (또는 로드를 방지) 응용 프로그램을 구성할 수 있도록 하는 어셈블리에서 MVC 기능입니다.</span><span class="sxs-lookup"><span data-stu-id="9630f-109">The main use case for application parts is to allow you to configure your app to discover (or avoid loading) MVC features from an assembly.</span></span>

## <a name="introducing-application-parts"></a><span data-ttu-id="9630f-110">응용 프로그램 부분을 소개합니다.</span><span class="sxs-lookup"><span data-stu-id="9630f-110">Introducing Application Parts</span></span>

<span data-ttu-id="9630f-111">MVC 응용 프로그램에서 해당 기능을 로드 [응용 프로그램 부분을](/aspnet/core/api/microsoft.aspnetcore.mvc.applicationparts.applicationpart)합니다.</span><span class="sxs-lookup"><span data-stu-id="9630f-111">MVC apps load their features from [application parts](/aspnet/core/api/microsoft.aspnetcore.mvc.applicationparts.applicationpart).</span></span> <span data-ttu-id="9630f-112">특히는 [AssemblyPart](/aspnet/core/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart#Microsoft_AspNetCore_Mvc_ApplicationParts_AssemblyPart) 클래스는 어셈블리에 의해 지원 되는 응용 프로그램 부분을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="9630f-112">In particular, the [AssemblyPart](/aspnet/core/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart#Microsoft_AspNetCore_Mvc_ApplicationParts_AssemblyPart) class represents an application part that is backed by an assembly.</span></span> <span data-ttu-id="9630f-113">검색 하 고 컨트롤러, 구성 요소 보기, 태그 도우미 및 razor 컴파일 원본 등의 MVC 기능을 로드할 이러한 클래스를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9630f-113">You can use these classes to discover and load MVC features, such as controllers, view components, tag helpers, and razor compilation sources.</span></span> <span data-ttu-id="9630f-114">[ApplicationPartManager](/aspnet/core/api/microsoft.aspnetcore.mvc.applicationparts.applicationpartmanager) MVC 응용 프로그램에 응용 프로그램 파트 및 사용할 수 있는 기능 공급자 추적 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="9630f-114">The [ApplicationPartManager](/aspnet/core/api/microsoft.aspnetcore.mvc.applicationparts.applicationpartmanager) is responsible for tracking the application parts and feature providers available to the MVC app.</span></span> <span data-ttu-id="9630f-115">와 상호 작용할 수는 `ApplicationPartManager` 에 `Startup` MVC를 구성 하는 경우:</span><span class="sxs-lookup"><span data-stu-id="9630f-115">You can interact with the `ApplicationPartManager` in `Startup` when you configure MVC:</span></span>

```csharp
// create an assembly part from a class's assembly
var assembly = typeof(Startup).GetTypeInfo().Assembly;
services.AddMvc()
    .AddApplicationPart(assembly);

// OR
var assembly = typeof(Startup).GetTypeInfo().Assembly;
var part = new AssemblyPart(assembly);
services.AddMvc()
    .ConfigureApplicationPartManager(apm => p.ApplicationParts.Add(part));
```

<span data-ttu-id="9630f-116">기본적으로 MVC 종속성 트리를 검색 하 고 (다른 어셈블리)에 컨트롤러를 찾이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="9630f-116">By default MVC will search the dependency tree and find controllers (even in other assemblies).</span></span> <span data-ttu-id="9630f-117">(예를 들어, 컴파일 타임에 참조 되지 않으면 플러그 인)에서 임의의 어셈블리를 로드 하려면 응용 프로그램 부분을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9630f-117">To load an arbitrary assembly (for instance, from a plugin that isn't referenced at compile time), you can use an application part.</span></span>

<span data-ttu-id="9630f-118">응용 프로그램 파트를 사용할 수 있습니다 *방지* 특정 어셈블리 또는 위치에 컨트롤러를 찾고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9630f-118">You can use application parts to *avoid* looking for controllers in a particular assembly or location.</span></span> <span data-ttu-id="9630f-119">어떤 부분 또는 어셈블리는 응용 프로그램에 사용할 수 있는 수정 하 여 제어할 수 있습니다는 `ApplicationParts` 의 컬렉션은 `ApplicationPartManager`합니다.</span><span class="sxs-lookup"><span data-stu-id="9630f-119">You can control which parts (or assemblies) are available to the app by modifying the `ApplicationParts` collection of the `ApplicationPartManager`.</span></span> <span data-ttu-id="9630f-120">에 있는 항목의 순서는 `ApplicationParts` 컬렉션 중요 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="9630f-120">The order of the entries in the `ApplicationParts` collection is not important.</span></span> <span data-ttu-id="9630f-121">완전히 구성 해야는 `ApplicationPartManager` 컨테이너에 서비스를 구성 하는 데 사용 하기 전에.</span><span class="sxs-lookup"><span data-stu-id="9630f-121">It is important to fully configure the `ApplicationPartManager` before using it to configure services in the container.</span></span> <span data-ttu-id="9630f-122">완벽 하 게 구성 해야 예를 들어는 `ApplicationPartManager` 호출 하기 전에 `AddControllersAsServices`합니다.</span><span class="sxs-lookup"><span data-stu-id="9630f-122">For example, you should fully configure the `ApplicationPartManager` before invoking `AddControllersAsServices`.</span></span> <span data-ttu-id="9630f-123">이렇게 하려면 실패 한 것을 의미 합니다 응용 프로그램 부분에 있는 컨트롤러 후 추가 되었는지 메서드 호출 적용 되지 것입니다 (서비스로 등록 가져올 됩니다) 응용 프로그램의 잘못 된 bevavior 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9630f-123">Failing to do so, will mean that controllers in application parts added after that method call will not be affected (will not get registered as services) which might result in incorrect bevavior of your application.</span></span>

<span data-ttu-id="9630f-124">사용 하지 않으려는 경우 컨트롤러를 포함 하는 어셈블리를 설정한 경우 제거 된 `ApplicationPartManager`:</span><span class="sxs-lookup"><span data-stu-id="9630f-124">If you have an assembly that contains controllers you do not want to be used, remove it from the `ApplicationPartManager`:</span></span>

```csharp
services.AddMvc()
    .ConfigureApplicationPartManager(p =>
    {
        var dependentLibrary = p.ApplicationParts
            .FirstOrDefault(part => part.Name == "DependentLibrary");

        if (dependentLibrary != null)
        {
           p.ApplicationParts.Remove(dependentLibrary);
        }
    })
```

<span data-ttu-id="9630f-125">프로젝트의 어셈블리 및 해당 종속 어셈블리 이외에 `ApplicationPartManager` 부분에 대 한 포함 됩니다 `Microsoft.AspNetCore.Mvc.TagHelpers` 및 `Microsoft.AspNetCore.Mvc.Razor` 기본적으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="9630f-125">In addition to your project's assembly and its dependent assemblies, the `ApplicationPartManager` will include parts for `Microsoft.AspNetCore.Mvc.TagHelpers` and `Microsoft.AspNetCore.Mvc.Razor` by default.</span></span>

## <a name="application-feature-providers"></a><span data-ttu-id="9630f-126">응용 프로그램 기능 공급자</span><span class="sxs-lookup"><span data-stu-id="9630f-126">Application Feature Providers</span></span>

<span data-ttu-id="9630f-127">응용 프로그램 기능 공급자 응용 프로그램 부분을 검사 하 고 해당 부분에 대 한 기능을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="9630f-127">Application Feature Providers examine application parts and provide features for those parts.</span></span> <span data-ttu-id="9630f-128">다음 MVC 기능에 대 한 기본 제공 기능 공급자 가지가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9630f-128">There are built-in feature providers for the following MVC features:</span></span>

* [<span data-ttu-id="9630f-129">컨트롤러</span><span class="sxs-lookup"><span data-stu-id="9630f-129">Controllers</span></span>](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.controllers.controllerfeatureprovider)
* [<span data-ttu-id="9630f-130">메타 데이터 참조</span><span class="sxs-lookup"><span data-stu-id="9630f-130">Metadata Reference</span></span>](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.razor.compilation.metadatareferencefeatureprovider)
* [<span data-ttu-id="9630f-131">태그 도우미</span><span class="sxs-lookup"><span data-stu-id="9630f-131">Tag Helpers</span></span>](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.razor.taghelpers.taghelperfeatureprovider)
* [<span data-ttu-id="9630f-132">구성 요소 보기</span><span class="sxs-lookup"><span data-stu-id="9630f-132">View Components</span></span>](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.viewcomponents.viewcomponentfeatureprovider)

<span data-ttu-id="9630f-133">상속 하는 기능 공급자 `IApplicationFeatureProvider<T>`여기서 `T` 형식 기능입니다.</span><span class="sxs-lookup"><span data-stu-id="9630f-133">Feature providers inherit from `IApplicationFeatureProvider<T>`, where `T` is the type of the feature.</span></span> <span data-ttu-id="9630f-134">MVC의 기능 형식에 대 한 공급자 위에 나열 된 고유한 기능을 구현할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9630f-134">You can implement your own feature providers for any of MVC's feature types listed above.</span></span> <span data-ttu-id="9630f-135">기능 공급자에서의 순서는 `ApplicationPartManager.FeatureProviders` 이후 공급자 이전 공급자 수행한 작업에 반응할 수 있으므로 컬렉션 중요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9630f-135">The order of feature providers in the `ApplicationPartManager.FeatureProviders` collection can be important, since later providers can react to actions taken by previous providers.</span></span>

### <a name="sample-generic-controller-feature"></a><span data-ttu-id="9630f-136">예제: 일반 컨트롤러 기능</span><span class="sxs-lookup"><span data-stu-id="9630f-136">Sample: Generic controller feature</span></span>

<span data-ttu-id="9630f-137">기본적으로 ASP.NET Core MVC 무시 제네릭 컨트롤러 (예를 들어 `SomeController<T>`).</span><span class="sxs-lookup"><span data-stu-id="9630f-137">By default, ASP.NET Core MVC ignores generic controllers (for example, `SomeController<T>`).</span></span> <span data-ttu-id="9630f-138">이 샘플에서는 기본 공급자 후 실행 되 고 형식의 지정된 된 목록에 대 한 일반 컨트롤러 인스턴스를 추가 하는 컨트롤러 기능 공급자 (에 정의 된 `EntityTypes.Types`):</span><span class="sxs-lookup"><span data-stu-id="9630f-138">This sample uses a controller feature provider that runs after the default provider and adds generic controller instances for a specified list of types (defined in `EntityTypes.Types`):</span></span>

[!code-csharp[Main](./app-parts/sample/AppPartsSample/GenericControllerFeatureProvider.cs?highlight=13&range=18-36)]

<span data-ttu-id="9630f-139">엔터티 형식:</span><span class="sxs-lookup"><span data-stu-id="9630f-139">The entity types:</span></span>

[!code-csharp[Main](./app-parts/sample/AppPartsSample/Model/EntityTypes.cs?range=6-16)]

<span data-ttu-id="9630f-140">기능 공급자에 추가 됩니다 `Startup`:</span><span class="sxs-lookup"><span data-stu-id="9630f-140">The feature provider is added in `Startup`:</span></span>

```csharp
services.AddMvc()
    .ConfigureApplicationPartManager(p => 
        p.FeatureProviders.Add(new GenericControllerFeatureProvider()));
```

<span data-ttu-id="9630f-141">기본적으로 폼의 라우팅에 사용 되는 제네릭 컨트롤러 이름과 것 *GenericController'1 [위젯]* 대신 *위젯*합니다.</span><span class="sxs-lookup"><span data-stu-id="9630f-141">By default, the generic controller names used for routing would be of the form *GenericController\`1[Widget]* instead of *Widget*.</span></span> <span data-ttu-id="9630f-142">다음 특성을 사용 하 여 컨트롤러에서 사용 하는 제네릭 형식에 해당 이름을 수정 합니다.</span><span class="sxs-lookup"><span data-stu-id="9630f-142">The following attribute is used to modify the name to correspond to the generic type used by the controller:</span></span>

[!code-csharp[Main](./app-parts/sample/AppPartsSample/GenericControllerNameConvention.cs)]

<span data-ttu-id="9630f-143">`GenericController` 클래스:</span><span class="sxs-lookup"><span data-stu-id="9630f-143">The `GenericController` class:</span></span>

[!code-csharp[Main](./app-parts/sample/AppPartsSample/GenericController.cs?highlight=5-6)]

<span data-ttu-id="9630f-144">일치 하는 경로 요청 하는 경우 결과:</span><span class="sxs-lookup"><span data-stu-id="9630f-144">The result, when a matching route is requested:</span></span>

![샘플 응용 프로그램에서 출력 된 예에서는 읽기, '는 제네릭 Sproket 컨트롤러에서 안녕하세요.'](app-parts/_static/generic-controller.png)

### <a name="sample-display-available-features"></a><span data-ttu-id="9630f-146">샘플: 디스플레이 사용할 수 있는 기능</span><span class="sxs-lookup"><span data-stu-id="9630f-146">Sample: Display available features</span></span>

<span data-ttu-id="9630f-147">요청 하 여 응용 프로그램에 제공 되는 지정된 된 기능을 반복할 수는 `ApplicationPartManager` 통해 [종속성 주입](../../fundamentals/dependency-injection.md) 및 적절 한 기능의 인스턴스를 채우는 데 사용:</span><span class="sxs-lookup"><span data-stu-id="9630f-147">You can iterate through the populated features available to your app by requesting an `ApplicationPartManager` through [dependency injection](../../fundamentals/dependency-injection.md) and using it to populate instances of the appropriate features:</span></span>

[!code-csharp[Main](./app-parts/sample/AppPartsSample/Controllers/FeaturesController.cs?highlight=16,25-27)]

<span data-ttu-id="9630f-148">예제 출력:</span><span class="sxs-lookup"><span data-stu-id="9630f-148">Example output:</span></span>

![샘플 응용 프로그램의 출력 예](app-parts/_static/available-features.png)
