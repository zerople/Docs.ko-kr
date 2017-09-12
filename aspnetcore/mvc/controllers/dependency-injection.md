---
title: "컨트롤러에 대 한 종속성 주입"
author: ardalis
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: bc8b4ba3-e9ba-48fd-b1eb-cd48ff6bc7a1
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/controllers/dependency-injection
ms.openlocfilehash: b83bd4a24ccf7e90e9df06d6a8e229a2d5c6699a
ms.sourcegitcommit: 9cdbfd0d670d70b9c354216aabee260c52dad5ee
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/12/2017
---
# <a name="dependency-injection-into-controllers"></a>컨트롤러에 대 한 종속성 주입

<a name=dependency-injection-controllers></a>

으로 [Steve Smith](https://ardalis.com/)

ASP.NET Core MVC 컨트롤러의 생성자를 통해 명시적으로 해당 종속성을 요청 해야 합니다. 경우에 따라 개별 컨트롤러 작업에는 서비스 필요할 수 있습니다 및 컨트롤러 수준에서 요청할 수 있는 적합할 수 있습니다. 이 경우에 작업 메서드의 매개 변수로 서비스를 삽입할 수도 있습니다.

[샘플 코드 보기 또는 다운로드](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/controllers/dependency-injection/sample)

## <a name="dependency-injection"></a>종속성 주입

종속성 주입은 뒤에 기술에서 [종속성 반전 원칙](http://deviq.com/dependency-inversion-principle/), 응용 프로그램을 느슨하게 결합 된 모듈의 구성 될 수 있도록 합니다. ASP.NET Core에서 기본적으로 지원 [종속성 주입](../../fundamentals/dependency-injection.md), 그러면 응용 프로그램 쉽게 테스트 하 고 유지 관리 합니다.

## <a name="constructor-injection"></a>생성자 삽입

ASP.NET Core 기본적으로 지원 되는 생성자 기반 종속성 주입 MVC 컨트롤러까지 확장 됩니다. 서비스 종류를 생성자 매개 변수로 컨트롤러에 단순히 추가 ASP.NET Core 서비스 컨테이너에서 기본 제공를 사용 하 여 해당 형식을 확인 하려고 합니다. 서비스는 항상 그렇지는 않지만 일반적으로 정의 된 인터페이스를 사용 하 여 합니다. 예를 들어 현재 시간에 따라 달라 지는 비즈니스 논리가 응용 프로그램에 설정된 된 시간을 사용 하는 구현에 전달 하 여 테스트 수 있는 시간 (아니라 하드 코딩 것)를 검색 하는 서비스를 삽입할 수 있습니다.

[!code-csharp[Main](dependency-injection/sample/src/ControllerDI/Interfaces/IDateTime.cs)]


런타임 시 시스템 클록을 사용 하는 이와 같은 인터페이스를 구현 하는 매우 간단 합니다.

[!code-csharp[Main](dependency-injection/sample/src/ControllerDI/Services/SystemDateTime.cs)]


이 위치에 컨트롤러에서 서비스를 사용할 수 있습니다. 이 경우 일부 논리를 추가 했습니다는 `HomeController` `Index` 하루 중 시간을 기반으로 하는 메서드를 사용자에 게 인사말을 표시 합니다.

[!code-csharp[Main](./dependency-injection/sample/src/ControllerDI/Controllers/HomeController.cs?highlight=8,10,12,17,18,19,20,21,22,23,24,25,26,27,28,29,30&range=1-31,51-52)]

지금 응용 프로그램을 실행 하는 경우 오류를 발생 가능성이 합니다.

<!-- literal_block {"ids": [], "xml:space": "preserve"} -->

```
An unhandled exception occurred while processing the request.

InvalidOperationException: Unable to resolve service for type 'ControllerDI.Interfaces.IDateTime' while attempting to activate 'ControllerDI.Controllers.HomeController'.
Microsoft.Extensions.DependencyInjection.ActivatorUtilities.GetService(IServiceProvider sp, Type type, Type requiredBy, Boolean isDefaultParameterRequired)
```

म에서 서비스를 구성 하지 않은 경우이 오류가 발생는 `ConfigureServices` 메서드에서 우리의 `Startup` 클래스입니다. 에 대 한 요청을 지정 하려면 `IDateTime` 의 인스턴스를 사용 하 여 해결 해야 `SystemDateTime`, 하려면 아래 목록에서 강조 표시 된 줄을 추가 하면 `ConfigureServices` 메서드:

[!code-csharp[Main](./dependency-injection/sample/src/ControllerDI/Startup.cs?highlight=4&range=26-27,42-44)]

> [!NOTE]
> 여러 가지 다른 수명 옵션 중 하나를 사용 하 여이 특정 서비스를 구현할 수 있습니다 (`Transient`, `Scoped`, 또는 `Singleton`). 참조 [종속성 주입](../../fundamentals/dependency-injection.md) 각 범위 옵션 내용이 미치는 영향을 서비스의 동작을 이해할 수 있습니다.

서비스를 구성한 후 응용 프로그램을 실행 하 고 홈 페이지로 이동 해야 시간 기반 메시지 예상 대로 표시 합니다.

![서버 인사말](dependency-injection/_static/server-greeting.png)

>[!TIP]
> 참조 [테스트 컨트롤러 논리](testing.md) 종속성을 명시적으로 요청 하는 방법에 알아보려면 [http://deviq.com/explicit-dependencies-principle/](http://deviq.com/explicit-dependencies-principle/) 컨트롤러에 더 쉽게 코드를 테스트 합니다.

ASP.NET Core 기본 종속성 주입 서비스를 요청 하는 클래스에 대 한 단일 생성자만 지원 합니다. 둘 이상의 생성자가 있으면 내용의 된 예외가 나타날 수 있습니다.

<!-- literal_block {"ids": [], "xml:space": "preserve"} -->

```
An unhandled exception occurred while processing the request.

InvalidOperationException: Multiple constructors accepting all given argument types have been found in type 'ControllerDI.Controllers.HomeController'. There should only be one applicable constructor.
Microsoft.Extensions.DependencyInjection.ActivatorUtilities.FindApplicableConstructor(Type instanceType, Type[] argumentTypes, ConstructorInfo& matchingConstructor, Nullable`1[]& parameterMap)
```

오류 메시지에서 설명 하는 대로 단일 생성자만이 문제를 해결할 수 있습니다. 수도 있습니다 [제 3 자 구현은 기본 종속성 주입 지원 바꿉니다](../../fundamentals/dependency-injection.md#replacing-the-default-services-container)지 원하는 여러 명의 생성자의 여러 합니다.

## <a name="action-injection-with-fromservices"></a>FromServices와 삽입 작업

경우에 따라 컨트롤러 내에서 둘 이상의 작업에 대 한 서비스를 필요 하지 않습니다. 이 경우 서비스 작업 메서드에 매개 변수로 삽입을 하면 됩니다. 특성으로 매개 변수를 표시 하 여 이렇게 `[FromServices]` 다음과 같이 합니다.

[!code-csharp[Main](./dependency-injection/sample/src/ControllerDI/Controllers/HomeController.cs?highlight=1&range=33-38)]

## <a name="accessing-settings-from-a-controller"></a>컨트롤러에서 설정 액세스

컨트롤러 내에서 응용 프로그램 또는 구성 설정에 액세스 하는 일반적인 패턴입니다. 이 액세스에 설명 된 옵션 패턴을 사용 해야 [구성](../../fundamentals/configuration.md)합니다. 일반적으로 하지 요청 해야 설정을 종속성 주입을 사용 하 여 컨트롤러에서 직접 합니다. 하는 것이 좋습니다 요청은 `IOptions<T>` 인스턴스, 여기서 `T` 필요한 구성 클래스입니다.

옵션 패턴을 사용 하려면이 다음과 같은 옵션을 나타내는 클래스를 만들려면:

[!code-csharp[Main](dependency-injection/sample/src/ControllerDI/Model/SampleWebSettings.cs)]

옵션 모델을 사용 하며 구성 클래스는 서비스 컬렉션에 추가 응용 프로그램을 구성 해야 `ConfigureServices`:

[!code-csharp[Main](./dependency-injection/sample/src/ControllerDI/Startup.cs?highlight=3,4,5,6,9,16,19&range=14-44)]

> [!NOTE]
> 위의 목록에서 구성 하는 중 응용 프로그램을 JSON 형식 파일에서 설정을 읽습니다. 또한 위의 주석 처리 된 코드에 표시 된 대로 코드에서 완전히 설정을 구성할 수 있습니다. 참조 [구성](../../fundamentals/configuration.md) 추가 구성 옵션에 대 한 합니다.

강력한 형식의 구성 개체를 지정한 후 (이 경우 `SampleWebSettings`) 추가할 서비스 컬렉션 요청할 수 있습니다이 모든 컨트롤러 또는 동작 메서드에서 인스턴스를 요청 하 여 `IOptions<T>` (이 경우 `IOptions<SampleWebSettings>`) . 다음 코드는 컨트롤러에서 설정을 요청 것 하나를 보여 줍니다.

[!code-csharp[Main](./dependency-injection/sample/src/ControllerDI/Controllers/SettingsController.cs?highlight=3,5,7&range=7-22)]

옵션 패턴을 설정 및 구성을 서로 분리할 수를 허용 하 고 되도록 컨트롤러 팔 로우 [문제의 분리](http://deviq.com/separation-of-concerns/)방법 또는 위치를 알 필요가 없으므로 않아야 하므로 설정을 찾을 수 정보입니다. 하기가 컨트롤러 단위 테스트를 쉽게 [테스트 컨트롤러 논리](testing.md)있기 때문에, 없습니다 [정적 문에 붙이는](http://deviq.com/static-cling/) 또는 컨트롤러 클래스 내에서 설정 클래스의 직접 인스턴스화입니다.
