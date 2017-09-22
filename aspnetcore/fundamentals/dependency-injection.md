---
title: "ASP.NET Core의 종속성 주입"
author: ardalis
description: "ASP.NET Core 종속성 주입을 구현 하는 방법 및 사용 하는 방법에 알아봅니다."
keywords: "종속성 주입, di ASP.NET Core"
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: fccd69be-7ad1-47fb-b203-b3633b6b9a9b
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/dependency-injection
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e5dfebeddb3a9394fd1faf798e029a9312201089
ms.sourcegitcommit: 78d28178345a0eea91556e4cd1adad98b1446db8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2017
---
# <a name="introduction-to-dependency-injection-in-aspnet-core"></a>ASP.NET Core의 종속성 주입 소개

<a name=fundamentals-dependency-injection></a>

여 [Steve Smith](https://ardalis.com/) 및 [Scott Addie](https://scottaddie.com)

ASP.NET Core는 지원 및 종속성 주입을 활용 하도록를 처음부터 다시 설계 되었습니다. ASP.NET Core 응용 프로그램 시작 클래스의 메서드를 삽입 하 여 기본 제공 프레임 워크 서비스를 활용할 수 있습니다 및 삽입도에 대 한 응용 프로그램 서비스를 구성할 수 있습니다. ASP.NET Core에서 제공 하는 기본 서비스 컨테이너에는 최소 기능 설정 및 다른 컨테이너를 대체 하기 위한 하지 제공 합니다.

[샘플 코드 보기 또는 다운로드](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/dependency-injection/sample)

## <a name="what-is-dependency-injection"></a>종속성 주입 이란?

종속성 주입 DI ()은 개체와 공동 작업자 또는 종속성 간의 느슨한 결합을 실현을 위한 기술입니다. 공동 작업자를 인스턴스화하거나 정적 참조를 사용 하 여 직접, 대신 클래스의 동작을 수행 하는 데 필요한 개체는 특정 방식으로 클래스에 제공 됩니다. 대부분의 경우 클래스 선언를 수행 하도록 허용 하는 생성자를 통해 해당 종속성은 [명시적 종속성 원칙](http://deviq.com/explicit-dependencies-principle/)합니다. 이 방법은 "생성자 삽입" 라고 합니다.

클래스 염두에서 DI 함께 디자인 된 때 자신의 공동 작업자에 직접적으로 하드 코드 된 종속성이 없는 때문에 느슨하게 결합 됩니다. 같은 [종속성 반전 원칙](http://deviq.com/dependency-inversion-principle/), 상태는 *"높은 수준의 모듈 낮은 수준 모듈에 종속; 둘 다 추상화에 의존 해야 합니다."* 특정 구현을 참조 하는 대신 클래스 요청 추상화 (일반적으로 `interfaces`) 클래스를 생성할 때에 제공 되는 합니다. 인터페이스에 대 한 종속성을 추출 하 고 매개 변수로 이러한 인터페이스의 구현을 제공 하는 또한의 예로 [전략 디자인 패턴](http://deviq.com/strategy-design-pattern/)합니다.

시스템 DI를 사용 하도록 설계 된, 자신의 생성자 (또는 속성)을 통해 해당 종속성을 요청 하는 여러 클래스가 때 관련된 종속성과 함께 이러한 클래스를 만드는 데 전용된 되는 클래스를 알면 큰 도움이 됩니다. 이러한 클래스 라고 *컨테이너*, 또는 보다 구체적으로, [Inversion of Control (IoC)](http://deviq.com/inversion-of-control/) DI (Dependency Injection) 컨테이너 또는 컨테이너입니다. 컨테이너에서 요청 된 형식의 인스턴스를 제공 해야 하는 팩터리 기본적으로. 지정된 된 형식에 선언에 종속성이 고 컨테이너 종속성 형식을 제공 하도록 구성 된 경우 요청한 인스턴스를 만드는 중 종속성 만들어집니다. 이러한 방식으로 모든 개체 하드 코드 된 생성의 도움 없이 클래스에 복잡 한 종속성 그래프를 제공할 수 있습니다. 종속성과 함께 개체를 만들 뿐만 아니라 컨테이너는 일반적으로 응용 프로그램 내에서 개체 수명을 관리 합니다.

간단한 기본 제공 컨테이너를 포함 하는 ASP.NET Core (나타내는 `IServiceProvider` 인터페이스) 기본적으로 생성자 삽입을 지원 하 고 ASP.NET을 사용 하면 특정 서비스 DI를 통해 사용할 수 있습니다. ASP입니다. 으로 관리 하는 형식에 NET의 컨테이너는 *서비스*합니다. 이 문서의 나머지 부분 전체에서 *서비스* ASP.NET Core IoC 컨테이너에 의해 관리 되는 유형을 참조 합니다. 기본 제공 컨테이너의 서비스 구성의 `ConfigureServices` 응용 프로그램에서 `Startup` 클래스입니다.

> [!NOTE]
> Martin Fowler가 광범위 한 문서에 기록 [Inversion의 컨트롤 컨테이너 및 종속성 주입 패턴](https://www.martinfowler.com/articles/injection.html)합니다. Microsoft Patterns and Practices도에 대 한 훌륭한 설명 [종속성 주입](https://msdn.microsoft.com/library/hh323705.aspx)합니다.

> [!NOTE]
> 이 문서에서는 모든 ASP.NET 응용 프로그램에 적용 될 때 종속성 주입을 설명 합니다. MVC 컨트롤러 내에서 종속성 주입에 대해서는 [종속성 주입 및 컨트롤러](../mvc/controllers/dependency-injection.md)합니다.

### <a name="constructor-injection-behavior"></a>생성자 삽입 동작

생성자 삽입 해야 하는 생성자에 *공용*합니다. 그렇지 않으면 앱을 throw 합니다는 `InvalidOperationException`:

> 'YourType' 형식에 대 한 적절 한 생성자를 찾을 수 없습니다. 형식은 구체적이 고 공용 생성자의 모든 매개 변수에 대 한 등록 서비스를 확인 합니다.


생성자 삽입 하려면 해당 적용 가능한 생성자가 하나만 있어야 합니다. 생성자 오버 로드를 지원 하지만 하나의 오버 로드는 해당 인수 모두이 행 될 수 종속성 주입 하 여 존재할 수 있습니다. 둘 이상이 있는 앱에서 throw 한 `InvalidOperationException`:

> 모든 지정 된 인수 형식을 수락 하는 여러 명의 생성자 형식을 'YourType'에서 발견 되었습니다. 적용 가능한 생성자가 하나 여야 합니다.

생성자는 종속성 주입으로 제공 되지 않은 인수를 사용할 수 있지만 이러한 기본값을 지원 해야 합니다. 예:

```csharp
// throws InvalidOperationException: Unable to resolve service for type 'System.String'...
public CharactersController(ICharacterRepository characterRepository, string title)
{
    _characterRepository = characterRepository;
    _title = title;
}

// runs without error
public CharactersController(ICharacterRepository characterRepository, string title = "Characters")
{
    _characterRepository = characterRepository;
    _title = title;
}
```

## <a name="using-framework-provided-services"></a>프레임 워크에서 제공한 서비스를 사용 하 여

`ConfigureServices` 에서 메서드는 `Startup` 클래스는 Entity Framework Core 및 ASP.NET Core MVC 같은 플랫폼 기능을 포함 하 여 응용 프로그램을 사용 하는 서비스를 정의 합니다. 처음에 `IServiceCollection` 에 제공 된 `ConfigureServices` 에 정의 된 다음 서비스가 (에 따라 [호스트 구성 방법](xref:fundamentals/hosting)):

| 서비스 종류 | 수명 |
| ----- | ------- |
| [Microsoft.AspNetCore.Hosting.IHostingEnvironment](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.hosting.ihostingenvironment) | 단일 항목 |
| [Microsoft.Extensions.Logging.ILoggerFactory](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.iloggerfactory) | 단일 항목 |
| [Microsoft.Extensions.Logging.ILogger&lt;T&gt;](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.ilogger) | 단일 항목 |
| [Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.hosting.builder.iapplicationbuilderfactory) | 일시적이 지 |
| [Microsoft.AspNetCore.Http.IHttpContextFactory](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.http.ihttpcontextfactory) | 일시적이 지 |
| [Microsoft.Extensions.Options.IOptions&lt;T&gt;](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.options.ioptions-1) | 단일 항목 |
| [System.Diagnostics.DiagnosticSource](https://docs.microsoft.com/dotnet/core/api/system.diagnostics.diagnosticsource) | 단일 항목 |
| [System.Diagnostics.DiagnosticListener](https://docs.microsoft.com/dotnet/core/api/system.diagnostics.diagnosticlistener) | 단일 항목 |
| [Microsoft.AspNetCore.Hosting.IStartupFilter](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.hosting.istartupfilter) | 일시적이 지 |
| [Microsoft.Extensions.ObjectPool.ObjectPoolProvider](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.objectpool.objectpoolprovider) | 단일 항목 |
| [Microsoft.Extensions.Options.IConfigureOptions&lt;T&gt;](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.options.iconfigureoptions-1) | 일시적이 지 |
| [Microsoft.AspNetCore.Hosting.Server.IServer](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.hosting.server.iserver) | 단일 항목 |
| [Microsoft.AspNetCore.Hosting.IStartup](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.hosting.istartup) | 단일 항목 |
| [Microsoft.AspNetCore.Hosting.IApplicationLifetime](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.hosting.iapplicationlifetime) | 단일 항목 |

다음은 다양 한와 같은 확장 메서드를 사용 하 여 컨테이너를 추가 서비스를 추가 하는 방법의 예 `AddDbContext`, `AddIdentity`, 및 `AddMvc`합니다.

[!code-csharp[Main](../common/samples/WebApplication1/Startup.cs?highlight=5-6,8-10,12&range=39-56)]

기능 및 ASP.NET MVC에서 같은 제공 하는 미들웨어 단일 추가 사용 하 여 규칙을 지키는*ServiceName* 확장 메서드를 모든 해당 기능에 필요한 서비스를 등록 합니다.

>[!TIP]
> 내에서 특정 프레임 워크에서 제공한 서비스를 요청할 수 `Startup` 매개 변수 목록은-통해 메서드를 참조 [응용 프로그램 시작](startup.md) 자세한 세부 정보에 대 한 합니다.

## <a name="registering-your-own-services"></a>사용자 고유의 서비스 등록

다음과 같이 사용자 고유의 응용 프로그램 서비스를 등록할 수 있습니다. 첫 번째 제네릭 형식은 형식을 (일반적으로 인터페이스)는 컨테이너에서 요청 될 것을 나타냅니다. 두 번째 제네릭 형식을는 컨테이너에 의해 인스턴스화될 되 고 이러한 요청을 처리 하는 데 사용 하는 구체적 유형은 나타냅니다.

[!code-csharp[Main](../common/samples/WebApplication1/Startup.cs?range=53-54)]

> [!NOTE]
> 각 `services.Add<ServiceName>` 확장 메서드 추가 (및 잠재적으로 구성) 서비스입니다. 예를 들어 `services.AddMvc()` MVC를 사용 하려면 서비스를 추가 합니다. 따르는이 규칙의 확장 메서드를 배치 하는 것이 좋습니다는 `Microsoft.Extensions.DependencyInjection` 네임 스페이스에 서비스 등록의 그룹을 캡슐화 합니다.

`AddTransient` 메서드가 인스턴스화되는 별도로 필요로 하는 모든 개체에 대 한 구체적인 서비스에 추상 형식을 매핑할 때 사용 됩니다. 서비스의로 알려져 *수명*, 및 추가 수명 옵션은 다음과 같습니다. 각 등록 하면 서비스에 대 한 적절 한 기간 동안 선택 하는 것이 유용 합니다. 서비스의 새 인스턴스를 요청한 각 클래스에 제공 해야 하나요? 하나의 인스턴스만 사용 되도록 지정 된 웹 요청? 단일 인스턴스를 사용할지 또는 응용 프로그램의 수명 동안?

이 문서에 대 한 샘플에는 호출 되는 문자 이름을 표시 하는 간단한 컨트롤러 `CharactersController`합니다. 해당 `Index` 메서드는 응용 프로그램에서 저장 된 문자의 현재 목록을 표시 하 고 없는 경우에 소수의 문자를 사용 하 여 컬렉션을 초기화 합니다. 이 응용 프로그램 엔터티 프레임 워크 코어를 사용 하지만 및 `ApplicationDbContext` 중 어떤 것도 컨트롤러에서 해당 지 속성에 대 한 클래스입니다. 인터페이스 뒤에 있는 특정 데이터 액세스 메커니즘 추출 된 대신 `ICharacterRepository`을 따라는 [리포지토리 패턴](http://deviq.com/repository-pattern/)합니다. 인스턴스 `ICharacterRepository` 생성자를 통해 요청 되 고 다음에 사용 되는 문자를 필요에 따라 액세스 전용 필드에 할당 합니다.

[!code-csharp[Main](../fundamentals/dependency-injection/sample/DependencyInjectionSample/Controllers/CharactersController.cs?highlight=3,5,6,7,8,14,21-27&range=8-36)]

`ICharacterRepository` 컨트롤러를 사용 해야 합니다. 두 메서드를 정의 `Character` 인스턴스.

[!code-csharp[Main](../fundamentals/dependency-injection/sample/DependencyInjectionSample/Interfaces/ICharacterRepository.cs?highlight=8,9)]

이 인터페이스는 구체적인 형식에서 구현에 `CharacterRepository`, 즉 사용 됩니다.

> [!NOTE]
> DI 방식으로 함께 사용 되는 `CharacterRepository` 클래스는 일반 모델 뿐 아니라 "저장소" 또는 데이터 액세스 클래스에서 응용 프로그램 서비스의 모든 따를 수 있습니다.

[!code-csharp[Main](../fundamentals/dependency-injection/sample/DependencyInjectionSample/Models/CharacterRepository.cs?highlight=9,11,12,13,14)]

`CharacterRepository` 요청은 `ApplicationDbContext` 생성자에서입니다. 에 연결 된 적절 하 게이 자체 종속성을 요청 하는 차례로 각 요청 된 종속성에 사용할 종속성 주입을 위한 비정상적인 아닙니다. 컨테이너의 모든 종속성 그래프에서를 확인 하 고 확인 된 서비스를 반환 해야 합니다.

> [!NOTE]
> 요청된 된 개체 및 모든 필요한 개체와의 모든 개체를 필요로 하는 것을 만드는 경우에 따라 라고는 *개체 그래프*합니다. 마찬가지로, 축적 집합 확인 해야 하는 종속성을 일반적으로 라고 하는 *종속성 트리* 또는 *종속성 그래프*합니다.

이 경우 둘 다 `ICharacterRepository` 하 고 다시 `ApplicationDbContext` 서비스 컨테이너에에 등록 해야 `ConfigureServices` 에서 `Startup`합니다. `ApplicationDbContext`구성 된 확장 메서드를 호출 `AddDbContext<T>`합니다. 다음 코드와의 등록 된 `CharacterRepository` 유형입니다.

[!code-csharp[Main](dependency-injection/sample/DependencyInjectionSample/Startup.cs?highlight=3-5,11&range=16-32)]

Entity Framework 컨텍스트를 사용 하 여 서비스 컨테이너에 추가 해야는 `Scoped` 수명입니다. 이 처리 되었음을 자동으로 도우미 메서드를 사용 하 여 위와 같이 합니다. 저장소의 Entity Framework를 사용할 수 있도록 동일한 수명 주기를 사용 해야 합니다.

>[!WARNING]
> 경계 해야 할 주요 위험을 확인 하는 한 `Scoped` 단일 서비스입니다. 이 경우 후속 요청을 처리할 때 서비스가 잘못 된 상태를 갖게 됩니다 가능성이 높습니다.

종속성이 있는 서비스 컨테이너에 등록 해야 합니다. 서비스의 생성자는 기본 형식인와 같은 필요한 경우는 `string`를 사용 하 여이 값이 삽입 될 수 있습니다는 [패턴 및 구성 옵션](configuration.md)합니다.

## <a name="service-lifetimes-and-registration-options"></a>서비스 수명 및 등록 옵션

ASP.NET 서비스 다음 수명을 구성할 수 있습니다.

**일시적이 지**

일시적인 수명 서비스 요청 될 때마다 생성 됩니다. 이 수명 간단한 상태 비저장 서비스에 가장 적합 합니다.

**범위 지정**

범위 지정된 수명 서비스는 요청당 한 번 생성 됩니다.

**단일 항목**

Singleton 수명 서비스 요청 될 때 처음으로 생성 됩니다 (또는 경우 `ConfigureServices` 있는 인스턴스를 지정 하는 경우 실행) 및 모든 후속 요청에서는 동일한 인스턴스를 사용 하는 다음 합니다. 응용 프로그램에 단일 동작 필요한 경우 단일 디자인 패턴을 구현 하 고 직접 클래스에서 개체의 수명을 관리 하는 대신 서비스 컨테이너 서비스의 수명을 관리할 수 있도록이 좋습니다.

여러 가지 방법으로 컨테이너와 서비스를 등록할 수 있습니다. 에 사용할 구체적인 형식을 지정 하 여 지정 된 유형의 서비스 구현을 등록 하는 방법을 이미 살펴보았습니다. 또한 팩터리 지정할 수 있습니다, 다음 인스턴스를 만드는 요청에 사용 되는. 세 번째 방법은 직접 없는 경우 컨테이너 인스턴스를 만들 하려고 시도 하지 것입니다 (또는 인스턴스 dispose 됩니다)를 사용 하려면 형식의 인스턴스를 지정 하는 것입니다.

이러한 수명 및 등록 옵션 간의 차이점을 보여 주기 위해 하나 이상의 작업을 나타내는 간단한 인터페이스를 고려는 *작업* 고유 식별자를 가진 `OperationId`합니다. 이 서비스에 대 한 수명을 구성 우리는 방법에 따라 컨테이너 요청 클래스에 서비스의 동일한 또는 다른 인스턴스를 제공 합니다. 나타내기 위해 어떤 수명을 요청 하 고, 수명 옵션 당 하나의 형식만 만들겠습니다.

[!code-csharp[Main](../fundamentals/dependency-injection/sample/DependencyInjectionSample/Interfaces/IOperation.cs?highlight=5-8)]

단일 클래스를 사용 하 여 이러한 인터페이스를 구현 `Operation`를 수락 하는 `Guid` 의 생성자 또는 사용 하 여 새 `Guid` 아무 것도 제공 하는 경우.

다음 `ConfigureServices`, 각 형식은 명명 된 수명에 따라 컨테이너에 추가 됩니다.

[!code-csharp[Main](dependency-injection/sample/DependencyInjectionSample/Startup.cs?range=26-32)]

`IOperationSingletonInstance` 서비스의 알려진된 ID로 특정 인스턴스를 사용 하는 `Guid.Empty` 수 있도록 명확한이 이와 같은 (해당 Guid를 모두 0 수 없음)는 사용 중인 경우. 또한 등록 한는 `OperationService` 에 각각의 다른 종속 된 `Operation` 형식, 해당 될 수 있도록 요청 내의 선택을 취소이 서비스는 컨트롤러 또는 새 대시보드를 각 작업 유형에 대해 동일한 인스턴스를 가져오는 여부. 이 서비스는 모든 보기에 표시할 수 있도록 해당 종속성 속성으로 노출입니다.

[!code-csharp[Main](dependency-injection/sample/DependencyInjectionSample/Services/OperationService.cs)]

샘플에 포함 되어 개체 수명 내와 응용 프로그램에 별도 개별 요청 간에 보여 주기 위해는 `OperationsController` 각 종류의 요청 `IOperation` 유형으로는 `OperationService`합니다. `Index` 그런 다음 모든 컨트롤러의와 서비스의 표시 `OperationId` 값입니다.

[!code-csharp[Main](dependency-injection/sample/DependencyInjectionSample/Controllers/OperationsController.cs)]

이제 두 개의 별도 요청이이 컨트롤러 작업으로 수행 됩니다.

![일시적이 지, Scoped, Singleton, 및 인스턴스 컨트롤러 및 첫 번째 요청에 작업을 서비스 작업에 대 한 작업 ID 값 (GUID의)을 표시 하는 Microsoft Edge에서 실행 되는 종속성 주입 샘플 웹 응용 프로그램의 작업 보기입니다.](dependency-injection/_static/lifetimes_request1.png)

![작업 보기 두 번째 요청에 대 한 작업 ID 값을 표시 합니다.](dependency-injection/_static/lifetimes_request2.png)

관찰 중 어떤는 `OperationId` 값 달라는 요청 내 간에, 그리고 요청 합니다.

* *일시적인* 개체가 다르면 항상 않으면 모든 컨트롤러와 모든 서비스에는 새 인스턴스를 제공 합니다.

* *범위* 개체가 다른 요청을 통해 다르지만 요청 내에서 동일한 지

* *Singleton* 개체는 모든 개체 및 모든 요청에 대해 같은 (에서 인스턴스 제공 여부에 관계 없이 `ConfigureServices`)

## <a name="request-services"></a>서비스 요청

ASP.NET에서 사용할 수 있는 서비스에서 요청을 `HttpContext` 을 통해 노출 되는 `RequestServices` 컬렉션입니다.

![HttpContext 요청 서비스 Intellisense 상황별 대화 상자는 요청 서비스를 가져오거나 요청의 서비스 컨테이너에 대 한 액세스를 제공 하는 IServiceProvider를 나타내는입니다.](dependency-injection/_static/request-services.png)

요청 서비스를 구성 하 고 응용 프로그램의 일부로 요청 서비스를 나타냅니다. 에 있는 형식에 의해 충족 하 여 개체 종속성을 지정 하는 경우 이러한 `RequestServices`이 아니라 `ApplicationServices`합니다.

일반적으로 사용 하는 이러한 속성을 직접 대신를 선호 형식 클래스의 생성자를 통해 필요한 클래스를 요청 하 고 이러한 종속성 주입 프레임 워크 수 있도록. 이 통해 쉽게 테스트할 수 있는 클래스를 (참조 [테스트](../testing/index.md))가 더 느슨하게 결합 하 고 있습니다.

> [!NOTE]
> 종속성에 액세스 하는 생성자 매개 변수로 요청 하는 것을 선호는 `RequestServices` 컬렉션입니다.

## <a name="designing-your-services-for-dependency-injection"></a>종속성 주입을 위한 서비스를 디자인합니다.

종속성 주입 자신의 공동 작업자를 사용 하 여 서비스를 디자인 해야 합니다. 즉, 상태 저장 정적 메서드 호출을 사용 하지 마십시오 (결과적으로 알려진 코드 냄새 [정적 문에 붙이는](http://deviq.com/static-cling/)) 및 서비스 내에서 종속 클래스의 직접 인스턴스화입니다. 고 구문을 기억 하는 것이 좋습니다. [새 작업은](https://ardalis.com/new-is-glue)를 인스턴스화하는 형식을 아니면 종속성 주입을 통해 요청를 선택할 때, 합니다. 수행 하 여는 [단색 원칙의 개체 지향 디자인](http://deviq.com/solid/), 클래스를 작은 하 고, 잘 구성 된 테스트를 쉽게 거친 간격과 자연스럽 게 됩니다.

경우에 어떻게 클래스 방식으로 삽입 되지 너무 많은 종속성이 있는 경향이 찾을 수 있습니까? 이 일반적으로 클래스를 너무 많이 수행 하 고 SRP-위반 아마도 표시는 [단일 책임 원칙](http://deviq.com/single-responsibility-principle/)합니다. 참조 하는 경우 해당 책임이 있는 몇 가지 새로운 클래스를 이동 하 여 클래스를 리팩터링할 수 있습니다. 프로그램 `Controller` 클래스 해야에 초점을 맞출 UI 우려 사항, 비즈니스 규칙 및 데이터 액세스 구현 세부 정보를 적절 한 클래스에 저장 해야 하므로 [문제를 분리](http://deviq.com/separation-of-concerns/)합니다.

데이터 액세스와 관련 하 여 구체적으로 주입할 수는 `DbContext` 컨트롤러에 (가정 EF 서비스 컨테이너를 추가 했으므로 `ConfigureServices`). 일부 개발자가 데이터베이스에 저장소 인터페이스를 사용 하는 것을 선호를 삽입 하는 대신는 `DbContext` 직접 합니다. 한 곳에 액세스 하는 논리 데이터를 캡슐화 하는 인터페이스를 사용 하 여 많은 데이터베이스에 변경 될 때 변경 해야 합니다를 최소화할 수 있습니다.

### <a name="disposing-of-services"></a>서비스 삭제

컨테이너는 호출 `Dispose` 에 대 한 `IDisposable` 형식을 만듭니다. 그러나 추가 하는 경우 인스턴스를 컨테이너에 직접, 것 삭제 되지 않습니다.

예제:

```csharp
// Services implement IDisposable:
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}
public class Service3 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    // container will create the instance(s) of these types and will dispose them
    services.AddScoped<Service1>();
    services.AddSingleton<Service2>();

    // container did not create instance so it will NOT dispose it
    services.AddSingleton<Service3>(new Service3());
    services.AddSingleton(new Service3());
}
```

> [!NOTE]
> 컨테이너에 대해 dispose 호출 버전 1.0에서에서 *모든* `IDisposable` 개체를 만들지 않은 포함 합니다.

## <a name="replacing-the-default-services-container"></a>기본 서비스 컨테이너를 대체합니다.

프레임 워크 및 대부분의 소비자 응용 프로그램에서 빌드된의 기본 요구를 수용 하는 기본 제공 서비스 컨테이너를 위한 것입니다. 그러나 개발자 기본 제공 컨테이너의 기본 컨테이너로 바꿀 수 있습니다. `ConfigureServices` 메서드는 일반적으로 반환 `void`, 시그니처에 반환할 변경 되 면 하지만 `IServiceProvider`, 서로 다른 컨테이너를 구성 하거나 반환할 수 있습니다. .NET에 대 한 사용 가능한 많은 IOC 컨테이너 있습니다. 이 예제는 [Autofac](https://autofac.org/) 패키지를 사용 합니다.

먼저, 적절 한 컨테이너 패키지를 설치 합니다.

* `Autofac`
* `Autofac.Extensions.DependencyInjection`

그런 다음 구성에서 컨테이너 `ConfigureServices` 다음 다시 돌아와 `IServiceProvider`:

```csharp
public IServiceProvider ConfigureServices(IServiceCollection services)
{
    services.AddMvc();
    // Add other framework services

    // Add Autofac
    var containerBuilder = new ContainerBuilder();
    containerBuilder.RegisterModule<DefaultModule>();
    containerBuilder.Populate(services);
    var container = containerBuilder.Build();
    return new AutofacServiceProvider(container);
}
```

> [!NOTE]
> 제 3 자 DI 컨테이너를 사용 하는 경우 변경 해야 `ConfigureServices` 반환 되도록 `IServiceProvider` 대신 `void`합니다.

마지막으로 구성 하 여 Autofac에 정상적으로 `DefaultModule`:

```csharp
public class DefaultModule : Module
{
    protected override void Load(ContainerBuilder builder)
    {
        builder.RegisterType<CharacterRepository>().As<ICharacterRepository>();
    }
}
```

런타임 시 Autofac 형식을 확인 하 고 종속성 주입을 사용 됩니다. [Autofac 및 ASP.NET Core를 사용 하는 방법에 대 한 자세한 정보](http://docs.autofac.org/en/latest/integration/aspnetcore.html)합니다.

### <a name="thread-safety"></a>스레드로부터의 안전성

Singleton 서비스는 스레드로부터 안전 해야 합니다. Singleton 서비스를 일시적으로 서비스에 종속 하는 경우 일시적으로 서비스는 스레드로부터 안전 단일 항목에서 사용 방법에 따라 해야 할 수도 있습니다.

## <a name="recommendations"></a>권장 사항

종속성 주입을 사용할 때는 다음 권장 사항을 염두에서에 둬야 합니다.

* DI은 복잡 한 종속성이 있는 개체입니다. 컨트롤러, 서비스, 어댑터 및 리포지토리는 DI에 추가할 수 있는 개체의 모든 예입니다.

* DI에서 직접 데이터 및 구성을 저장 하지 않습니다. 예를 들어 사용자의 쇼핑 카트 일반적으로 서비스 컨테이너에 추가 하지 않아야 합니다. 구성을 사용 해야는 [옵션 모델](configuration.md#options-config-objects)합니다. 다른 개체에 대 한 액세스를 허용 하기 위해서만 존재 하는 "데이터 소유자" 개체를 하지 마십시오. 가능한 경우 DI를 통해 필요 실제 항목을 요청 하는 것이 좋습니다.

* 서비스에 대 한 정적 액세스를 하지 마십시오.

* 응용 프로그램 코드에서 서비스 위치를 하지 마십시오.

* 에 대 한 정적 액세스를 방지 `HttpContext`합니다.

> [!NOTE]
> 권장 구성의 모든 집합 처럼 하나만 필요할된 때 발생할 수 있습니다. 프레임 워크 자체 내에서 특별 한 대부분의 경우-많지 않은 예외를 발견 했습니다.

종속성 주입은는 *대체* 정적/전역 개체 액세스 패턴을 합니다. 정적 개체 액세스와 함께 사용할 경우 DI의 이점을 실현 수 없습니다.

## <a name="additional-resources"></a>추가 리소스

* [응용 프로그램 시작](startup.md)

* [테스트](../testing/index.md)

* [종속성 주입 (MSDN)으로 ASP.NET Core에 정리 코드를 작성합니다.](https://msdn.microsoft.com/magazine/mt703433.aspx)

* [컨테이너 관리 응용 프로그램 디자인, 궁금할: 여기서 컨테이너 속해야 합니까?](https://blogs.msdn.microsoft.com/nblumhardt/2008/12/26/container-managed-application-design-prelude-where-does-the-container-belong/)

* [명시적 종속 관계 원칙](http://deviq.com/explicit-dependencies-principle/)

* [컨트롤 컨테이너 및 종속성 주입 패턴의 반전](https://www.martinfowler.com/articles/injection.html) (Fowler)
