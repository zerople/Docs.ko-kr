---
title: "컨트롤러에 종속성 주입 | Microsoft 문서"
author: ardalis
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: bc8b4ba3-e9ba-48fd-b1eb-cd48ff6bc7a1
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/controllers/dependency-injection
translationtype: Machine Translation
ms.sourcegitcommit: 010b730d2716f9f536fef889bc2f767afb648ef4
ms.openlocfilehash: c01c5915a8cacbbc779d9a187e9e28dc95cc4710
ms.lasthandoff: 03/23/2017

---
# <a name="dependency-injection-into-controllers"></a>컨트롤러에 종속성 주입

<a name=dependency-injection-controllers></a>

[Steve Smith](http://ardalis.com)

ASP.NET Core MVC 컨트롤러의 생성자를 통해 명시적으로 해당 종속성을 요청 해야 합니다. 경우에 따라 개별 컨트롤러 작업을 서비스 해야 할 수 있습니다 및 컨트롤러 수준에서 요청을 하면 안 됩니다. 이 경우에 서비스 작업 메서드에 대 한 매개 변수로 삽입할 수도 있습니다.

[샘플 코드 보기 또는 다운로드](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/controllers/dependency-injection/sample)

## <a name="dependency-injection"></a>종속성 주입

다음에 나오는 기술은 종속성 주입은 [종속성 반전 원칙](http://deviq.com/dependency-inversion-principle)시켜 응용 프로그램을 느슨하게 연결 된 모듈로 구성 됩니다. ASP.NET Core에서 기본적으로 지원 [종속성 주입](../../fundamentals/dependency-injection.md)는 쉽게 응용 프로그램 테스트 및 유지 관리 합니다.

## <a name="constructor-injection"></a>생성자 주입

ASP.NET Core 기본적으로 지원 되는 생성자 기반 종속성 주입 MVC 컨트롤러를 확장합니다. 생성자 매개 변수로 컨트롤러에 서비스 종류를 추가 하기만 하면, ASP.NET 핵심 서비스 컨테이너에는 기본 제공를 사용 하 여 해당 형식을 확인 하려고 합니다. 서비스는 항상 그렇지는 않지만 일반적으로 정의 인터페이스를 사용 합니다. 예를 들어, 응용 프로그램에 비즈니스 논리는 현재 시간에 의존 하는 경우에 테스트 설정된 된 시간을 사용 하는 구현에 전달할 수 있는 시간 (아니라 하드 코딩)를 검색 하는 서비스를 삽입할 수 있습니다.

[!code-csharp[주](dependency-injection/sample/src/ControllerDI/Interfaces/IDateTime.cs)]


런타임 시 시스템 클록을 사용 하는 이와 같은 인터페이스를 구현 하는 매우 간단 합니다.

[!code-csharp[주](dependency-injection/sample/src/ControllerDI/Services/SystemDateTime.cs)]


이 위치를 컨트롤러에 서비스를 사용할 수 있습니다. 이 경우 일부 논리를 추가 했습니다는 `HomeController` `Index` 하루 중 시간을 기반으로 하는 메서드를 사용자에 게 인사말을 표시 합니다.

[!code-csharp[주](./dependency-injection/sample/src/ControllerDI/Controllers/HomeController.cs?highlight=8,10,12,17,18,19,20,21,22,23,24,25,26,27,28,29,30&range=1-31,51-52)]

이제 응용 프로그램을 실행 하는 경우 오류가 발생할 가능성이 높습니다 됩니다.

<!-- literal_block {"ids": [], "xml:space": "preserve"} -->

```
An unhandled exception occurred while processing the request.

InvalidOperationException: Unable to resolve service for type 'ControllerDI.Interfaces.IDateTime' while attempting to activate 'ControllerDI.Controllers.HomeController'.
Microsoft.Extensions.DependencyInjection.ActivatorUtilities.GetService(IServiceProvider sp, Type type, Type requiredBy, Boolean isDefaultParameterRequired)
```

우리에 서비스를 구성 하지 않은 경우이 오류가 발생는 `ConfigureServices` 메서드에서 우리의 `Startup` 클래스입니다. 에 대 한 요청을 지정 하려면 `IDateTime` 의 인스턴스를 사용 하 여 해결 해야 `SystemDateTime`, 강조 표시 된 줄을 아래 목록에 추가 하면 `ConfigureServices` 메서드:

[!code-csharp[주](./dependency-injection/sample/src/ControllerDI/Startup.cs?highlight=4&range=26-27,42-44)]

> [!NOTE]
> 몇 가지 다른 수명 옵션 중 하나를 사용 하 여이 특정 서비스를 구현할 수 있습니다 (`Transient`, `Scoped`, 또는 `Singleton`). 참조 [종속성 주입](../../fundamentals/dependency-injection.md) 각각이 범위 옵션의 내용이 미치는 영향을 서비스의 동작을 이해 하 합니다.

서비스 구성 되 면 응용 프로그램을 실행 하 고 홈 페이지로 이동 해야 시간 기반 메시지 예상 대로 표시:

![서버 인사말](dependency-injection/_static/server-greeting.png)

>[!TIP]
> 참조 [컨트롤러 논리 테스트](testing.md) 종속성을 명시적으로 요청 하는 방법에 알아보려면 [http://deviq.com/explicit-dependencies-principle](http://deviq.com/explicit-dependencies-principle) 컨트롤러에 더 쉽게 코드를 테스트 합니다.

기본 제공 종속성 주입 ASP.NET 핵심 서비스를 요청 하는 클래스에 대 한 단일 생성자만 지원 합니다. 둘 이상의 생성자가 있으면 내용의 된 예외가 발생할 수 있습니다.

<!-- literal_block {"ids": [], "xml:space": "preserve"} -->

```
An unhandled exception occurred while processing the request.

InvalidOperationException: Multiple constructors accepting all given argument types have been found in type 'ControllerDI.Controllers.HomeController'. There should only be one applicable constructor.
Microsoft.Extensions.DependencyInjection.ActivatorUtilities.FindApplicableConstructor(Type instanceType, Type[] argumentTypes, ConstructorInfo& matchingConstructor, Nullable`1[]& parameterMap)
```

오류 메시지의 내용과 같이 단일 생성자만이 문제를 해결할 수 있습니다. 수도 있습니다 [제&3; 자 구현 기본 종속성 주입 지원을 바꿉니다](../../fundamentals/dependency-injection.md#replacing-the-default-services-container)지 원하는 여러 생성자는 대부분이 있습니다.

## <a name="action-injection-with-fromservices"></a>FromServices 사용한 작업 주입

경우에 따라 컨트롤러 내에서 둘 이상의 작업에 대 한 서비스를 않아도 됩니다. 이 경우 서비스 작업 메서드에 매개 변수로 삽입 하는 감지를 하면 됩니다. 특성으로 매개 변수를 표시 하 여 이렇게 `[FromServices]` 다음과 같이 합니다.

[!code-csharp[주](./dependency-injection/sample/src/ControllerDI/Controllers/HomeController.cs?highlight=1&range=33-38)]

## <a name="accessing-settings-from-a-controller"></a>컨트롤러에서 설정에 액세스

컨트롤러 내에서 응용 프로그램 또는 구성 설정에 액세스 하는 일반적인 패턴입니다. 이 액세스에 설명 된 옵션 패턴을 사용 해야 [구성](../../fundamentals/configuration.md)합니다. 일반적으로 하지 요청 해야 설정을 종속성 주입을 사용 하 여 컨트롤러에서 직접. 하는 것이 좋습니다 요청은 `IOptions<T>` 인스턴스, 여기서 `T` 필요한 구성 클래스입니다.

옵션 패턴을 사용 하려면이 다음과 같은 옵션을 나타내는 클래스를 만드는 필요 합니다.

[!code-csharp[주](dependency-injection/sample/src/ControllerDI/Model/SampleWebSettings.cs)]

구성 클래스는 서비스 컬렉션에 추가 하 고 옵션 모델을 사용 하도록 응용 프로그램을 구성 해야 하는 다음 `ConfigureServices`:

[!code-csharp[주](./dependency-injection/sample/src/ControllerDI/Startup.cs?highlight=3,4,5,6,9,16,19&range=14-44)]

> [!NOTE]
> 위의 목록에서 JSON 형식 파일에서 설정을 읽지 응용 프로그램을 구성 하는 것입니다. 또한 위의 주석으로 처리 된 코드에 나와 있듯이 코드에서 완전히 설정을 구성할 수 있습니다. 참조 [구성](../../fundamentals/configuration.md) 구성 옵션에 추가 합니다.

강력한 형식의 구성 개체를 지정한 후 (이 경우 `SampleWebSettings`) 추가 하 고 서비스 컬렉션에 요청할 수 있습니다 것 모든 컨트롤러 또는 작업 메서드에서 인스턴스를 요청 하 여 `IOptions<T>` (이 경우 `IOptions<SampleWebSettings>`). 다음 코드는 컨트롤러에서 설정을 요청 것 하나를 보여 줍니다.

[!code-csharp[주](./dependency-injection/sample/src/ControllerDI/Controllers/SettingsController.cs?highlight=3,5,7&range=7-22)]

설정 및 구성, 서로 분리할 수를 허용 하 고 컨트롤러 팔 로우 하면 옵션 패턴을 [중요 한 부분의 분리](http://deviq.com/separation-of-concerns/)이므로 방법 또는 위치를 알 필요가 없는 설정 정보를 찾을 수 있습니다. 있기 컨트롤러 단위 테스트를 쉽게 [컨트롤러 논리 테스트](testing.md)있기 때문에, 없습니다 [정적 부착](http://deviq.com/static-cling/) 또는 컨트롤러 클래스 내에서 설정 클래스의 직접 인스턴스화입니다.

