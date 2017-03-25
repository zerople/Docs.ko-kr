---
title: "응용 프로그램 부분을 | Microsoft 문서"
author: ardalis
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 1/4/2017
ms.topic: article
ms.assetid: b355a48e-a15c-4d58-b69c-899963613a98
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/extensibility/app-parts
translationtype: Machine Translation
ms.sourcegitcommit: 010b730d2716f9f536fef889bc2f767afb648ef4
ms.openlocfilehash: 3d4c778fb21a6be611d47fd1f08894b076bd9f3b
ms.lasthandoff: 03/23/2017

---
# <a name="application-parts"></a>응용 프로그램 부분

[Steve Smith](http://ardalis)

[샘플 코드 보기 또는 다운로드](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample)

*응용 프로그램 부분* 있는 MVC 컨트롤러, 구성 요소 보기와 같은 기능을 응용 프로그램의 리소스에 대 한 추상화 또는 태그 도우미를 검색할 수 있습니다. 한 가지 예는 응용 프로그램 부분 어셈블리 참조 및 표시 유형 및 컴파일 참조를 캡슐화 하는 AssemblyPart입니다. *공급자 기능* 핵심 ASP.NET MVC 응용 프로그램의 기능을 채우는 데 응용 프로그램 부분을와 함께 작동 합니다. 검색 (또는 로드를 방지)에 앱을 구성할 수 있도록 응용 프로그램 부분에 대 한 주요 사용 사례는 어셈블리에서 MVC 기능입니다.

## <a name="introducing-application-parts"></a>응용 프로그램 부분을 소개합니다.

MVC 응용 프로그램에서 해당 기능을 로드 [응용 프로그램 부분을](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.applicationparts.applicationpart)합니다. 특히,는 [AssemblyPart](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart#Microsoft_AspNetCore_Mvc_ApplicationParts_AssemblyPart) 클래스는 어셈블리에 의해 지원 되는 응용 프로그램 부분을 나타냅니다. 검색 하 고 컨트롤러, 구성 요소 보기, 태그 도우미 및 razor 컴파일이 소스 등의 MVC 기능을 로드할 이러한 클래스를 사용할 수 있습니다. [ApplicationPartManager](https://docs.microsoft.com/en-us/aspnet/core/api/microsoft.aspnetcore.mvc.applicationparts.applicationpartmanager) 는 MVC 응용 프로그램에 사용할 수 있는 기능 공급자와 응용 프로그램 부분을 추적 합니다. 와 상호 작용할 수는 `ApplicationPartManager` 에서 `Startup` MVC를 구성 하는 경우:

<!-- literal_block {"ids": [], "linenos": true, "xml:space": "preserve", "language": "csharp"} -->

```csharp
// create an assembly part from a class's assembly
var assembly = typeof(Startup).GetTypeInfo().Assembly;
services
    .AddMvc()
    .AddApplicationPart(assembly);

// OR
var assembly = typeof(Startup).GetTypeInfo().Assembly;
var part = new AssemblyPart(assembly);
services
    .AddMvc()
    .ConfigureApplicationPartManager(apm => p.ApplicationParts.Add(part));
```

기본적으로 MVC 종속성 트리를 검색 하 고 (다른 어셈블리)에 컨트롤러를 찾이 됩니다. (예를 들어, 컴파일 타임에 참조 되지 않으면 플러그 인)에서 임의 어셈블리를 로드 하려면 응용 프로그램 부분을 사용할 수 있습니다.

응용 프로그램 파트를 사용할 수 있습니다 *방지* 컨트롤러는 특정 어셈블리 또는 위치에 대 한 보고 합니다. 어떤 부분 (어셈블리)은 응용 프로그램에 사용할 수 있는 수정 하 여 제어할 수는 `ApplicationParts` 의 컬렉션은 `ApplicationPartManager`합니다. 에 있는 항목의 순서는 `ApplicationParts` 컬렉션 중요 하지 않습니다. 완벽 하 게 구성 해야는 `ApplicationPartManager` 전에 컨테이너에서 서비스를 구성 하는 데 사용 합니다. 완벽 하 게 구성 해야 예를 들어는 `ApplicationPartManager` 호출 하기 전에 `AddControllersAsServices`합니다. 이렇게 하려면 실패는 것을 의미 응용 프로그램 부분에 있는 컨트롤러 후 추가 되었는지 메서드 호출 적용 되지 것입니다 (서비스로 등록 가져오기는) 응용 프로그램의 잘못 된 bevavior 될 수 있습니다.

사용 하지 않으려는 경우 컨트롤러를 포함 하는 어셈블리에 있으면 제거는 `ApplicationPartManager`:

<!-- literal_block {"ids": [], "linenos": true, "xml:space": "preserve", "language": "csharp"} -->

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

프로젝트의 어셈블리 및 해당 종속 어셈블리 외에 `ApplicationPartManager` 부분에 대 한 포함 됩니다 `Microsoft.AspNetCore.Mvc.TagHelpers` 및 `Microsoft.AspNetCore.Mvc.Razor` 기본적으로 합니다.

## <a name="application-feature-providers"></a>응용 프로그램 기능 공급자

응용 프로그램 기능 공급자 응용 프로그램 부분을 검사 하 고 해당 부분에 대 한 기능을 제공 합니다. 다음 MVC 기능에 대 한 기본 제공 기능 공급자 가지가 있습니다.

- [컨트롤러](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.controllers.controllerfeatureprovider)
- [메타 데이터 참조](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.razor.compilation.metadatareferencefeatureprovider)
- [태그 도우미](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.razor.taghelpers.taghelperfeatureprovider)
- [구성 요소 보기](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.viewcomponents.viewcomponentfeatureprovider)

상속 하는 기능 공급자 `IApplicationFeatureProvider<T>`여기서 `T` 기능의 형식입니다. 위에 나열 된 모든 MVC의 기능 형식에 대 한 공급자 고유의 기능을 구현할 수 있습니다. 기능 공급자의 순서는 `ApplicationPartManager.FeatureProviders` 이후 공급자 이전 공급자에 의해 수행 된 작업에 반응할 수 있으므로 컬렉션 중요할 수 있습니다.

### <a name="sample-generic-controller-feature"></a>샘플: 일반 컨트롤러 기능

기본적으로 ASP.NET 핵심 MVC 무시 제네릭 컨트롤러 (예를 들어 `SomeController<T>`). 이 샘플에서는 기본 공급자 후 실행 되 고 형식의 지정된 된 목록에 대 한 일반 컨트롤러 인스턴스를 추가 하는 컨트롤러 기능 공급자 (에 정의 된 `EntityTypes.Types`):

[!code-csharp[주](./app-parts/sample/src/AppPartSample/GenericControllerFeatureProvider.cs?highlight=13&range=18-36)]

엔터티 유형:

[!code-csharp[주](./app-parts/sample/src/AppPartSample/Model/EntityTypes.cs?range=6-16)]

기능 공급자에 추가 된 `Startup`:

<!-- literal_block {"ids": [], "linenos": true, "xml:space": "preserve", "language": "csharp"} -->

```csharp
services.AddMvc()
        .ConfigureApplicationPartManager(p => 
            p.FeatureProviders.Add(new GenericControllerFeatureProvider()));
```

기본적으로 폼의 라우팅에 사용 되는 제네릭 컨트롤러 이름과 것 *GenericController'1 [위젯]* 대신 *위젯*합니다. 다음 특성을 사용 하 여 컨트롤러에 의해 사용 되는 제네릭 형식에 해당 이름을 수정 합니다.

[!code-csharp[주](./app-parts/sample/src/AppPartSample/GenericControllerNameConvention.cs)]

`GenericController` 클래스:

[!code-csharp[주](./app-parts/sample/src/AppPartSample/GenericController.cs?highlight=5-6)]

일치 하는 경로 요청 될 때 결과:

![이미지](app-parts/_static/generic-controller.png)

### <a name="sample-display-available-features"></a>사용할 수 있는 기능을 표시 하는 샘플:

요청 하 여 앱에 사용할 수 있는 지정된 된 기능을 반복할 수는 `ApplicationPartManager` 통해 [종속성 주입](../../fundamentals/dependency-injection.md) 및 적절 한 기능의 인스턴스를 채우는 데 사용 합니다.

 [!code-csharp[주](./app-parts/sample/src/AppPartSample/Controllers/FeaturesController.cs?highlight=16,25-27)]

예제 출력:

![이미지](app-parts/_static/available-features.png)



