---
title: "ASP.NET Core 로그인 | Microsoft 문서"
author: ardalis
description: "ASP.NET Core에는 로깅 프레임 워크를 소개합니다. 각 기본 제공 된 로깅 공급자 및 일부 인기 있는 타사 공급자에 대 한 링크에 대 한 섹션을 포함 합니다."
keywords: "ASP.NET Core, 로깅, 로깅 공급자, Microsoft.Extensions.Logging, ILogger, ILoggerFactory, LogLevel, WithFilter, TraceSource, 이벤트 로그, EventSource, 범위 지정"
ms.author: tdykstra
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: ac27ac68-d76a-4f8e-b8ab-ea045803e5f2
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/logging
ms.custom: H1Hack27Feb2017
translationtype: Machine Translation
ms.sourcegitcommit: 010b730d2716f9f536fef889bc2f767afb648ef4
ms.openlocfilehash: 86d4cb23cef6ad5982508883ec6361073e2900d3
ms.lasthandoff: 03/23/2017

---
# <a name="introduction-to-logging-in-aspnet-core"></a>ASP.NET Core 로그인 소개

여 [Steve Smith](http://ardalis.com) 및 [Tom Dykstra](https://github.com/tdykstra)

ASP.NET Core 다양 한 로깅 공급자와 함께 작동 하는 로깅 API를 지원 합니다. 기본 제공 공급자를 사용 하면 하나 이상의 대상 로그 보내기 및 제&3; 자 로깅 프레임 워크에 연결할 수 있습니다. 이 문서에는 코드에서 기본 제공 된 로깅 API 및 공급자를 사용 하는 방법을 보여 줍니다.

[샘플 코드 보기 또는 다운로드](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/logging/sample)

## <a name="how-to-add-providers"></a>공급자를 추가 하는 방법

로그 데이터에 몇 가지 작업을 수행 하는 로깅 공급자, 예: 콘솔에 표시 또는 Azure blob 저장소에 저장 합니다. 공급자를 사용 하려면 해당 NuGet 패키지를 설치 하 고 인스턴스의 공급자의 확장 메서드를 호출 `ILoggerFactory`다음 예제와 같이 합니다.

[!code-csharp[](logging/sample/src/TodoApi/Startup.cs?name=snippet_AddConsoleAndDebug&highlight=3,5-7)]

ASP.NET Core [종속성 주입](dependency-injection.md) DI ()를 제공 된 `ILoggerFactory` 인스턴스. `AddConsole` 및 `AddDebug` 에 정의 된 확장 메서드가 [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/) 및 [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug/) 패키지 합니다. 확장 메서드는 각각는 `ILoggerFactory.AddProvider` 메서드를 공급자의 인스턴스를 전달 합니다. 

> [!NOTE]
> 이 문서에 대 한 샘플 응용 프로그램에 대 한 로깅 공급자 추가 `Configure` 의 메서드는 `Startup` 클래스입니다. 이전에 실행 되는 코드에서 로그 출력을 가져오려는 경우 추가 로깅 공급자에는 `Startup` 클래스 생성자를 대신 합니다. 

각에 대 한 정보를 찾아볼 [기본 제공 된 로깅 공급자](#built-in-logging-providers) 연결 하 고를 [타사 로깅 공급자](#third-party-logging-providers) 는 문서의 뒷부분에 나오는 합니다.

## <a name="how-to-create-logs"></a>로그를 만드는 방법

로그를 만들려면 가져오기는 `ILogger` DI에서 개체 하는 필드에 저장 한 다음 해당로 거 개체의 로깅 메서드를 호출 합니다.

[!code-csharp[](logging/sample/src/TodoApi/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=4,7,10)]

[!code-csharp[](logging/sample/src/TodoApi/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

이 예제에서는 요청 `ILogger<TodoController>` 지정 하려면 DI에서는 `TodoController` 으로 클래스는 *범주* 의 거를 사용 하 여 만든 로그 합니다.  범주 설명 [이 문서의 뒷부분에 나오는](#log-category)합니다.

## <a name="sample-logging-output"></a>예제 로깅 출력

위에 나와 있는 샘플 코드와 Visual Studio에서 디버그 모드에서 실행 하면 로그는 명령줄에서 실행할 때 콘솔에서 및 디버그 창에 표시 됩니다. 

여기에 표시 하는 콘솔 명령줄에서 샘플 응용 프로그램을 실행 하 고 URL로 이동 하는 경우의 예는 `http://localhost:5000/api/todo/0`:

```console
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:5000/api/todo/invalidid
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (invalidid) - ModelState is Valid
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 243.2636ms
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[2]
      Request finished in 628.9188ms 404
```

다음은 표시의 디버그 창에서 디버그 모드에서 Visual Studio에서 샘플 응용 프로그램을 실행 하 고 URL로 이동 하는 경우의 예 `http://localhost:55070/api/todo/0`:

```
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request starting HTTP/1.1 GET http://localhost:55070/api/todo/invalidid
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (invalidid) - ModelState is Valid
TodoApi.Controllers.TodoController:Information: Getting item invalidid
TodoApi.Controllers.TodoController:Warning: GetById(invalidid) NOT FOUND
Microsoft.AspNetCore.Mvc.StatusCodeResult:Information: Executing HttpStatusCodeResult, setting HTTP status code 404
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 12.5003ms
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request finished in 19.0913ms 404
```

이러한 예제에서 동일한 로깅 API와 같은 로깅 공급자 자체 ASP.NET 핵심 및 응용 프로그램 코드 사용 되 볼 수 있습니다.

이 문서의 나머지 부분에서는 몇 가지 세부 정보 및 로깅에 대 한 옵션을 설명합니다.

## <a name="nuget-packages"></a>NuGet 패키지

`ILogger` 및 `ILoggerFactory` 인터페이스에는 [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), 해당 기본 구현이 있으며 [Microsoft.Extensions.Logging](https://www.nuget.org/packages/Microsoft.Extensions.Logging/)합니다.

## <a name="log-category"></a>로그 범주

A *범주* 가 지정 된 각 로그를 만들어야 합니다. 범주는 임의의 문자열이 될 수 있지만 로그는 기록 된 클래스의 정규화 된 이름을 사용 하는 규칙은.  예: "TodoApi.Controllers.TodoController"입니다.

DI를에서 거 개체 또는 하나의 요청을 만들면 및 범주는 해당로 거에 의해 작성 된 모든 로그를 자동으로 포함 하는 경우에 범주를 지정 합니다. 범주를 명시적으로 지정할 수 또는 범주 형식에서 파생 하는 확장 메서드를 사용할 수 있습니다. 범주를 명시적으로 지정 하려면 호출 `CreateLogger` 에 *ILoggerFactory* 인스턴스를 다음과 같이 합니다.

[!code-csharp[](logging/sample/src/TodoApi/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

대부분의 경우는 것이를 사용 하기 쉽게 `ILogger<T>`다음 예제와 같이 합니다.

[!code-csharp[](logging/sample/src/TodoApi/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7,10)]

이 호출에 해당 하는 `CreateLogger` 의 정규화 된 형식 이름으로 `T`합니다.

## <a name="log-level"></a>로그 수준

때마다 지정 로그를 작성 하면 해당 [LogLevel](https://docs.asp.net/projects/api/en/latest/autoapi/Microsoft/Extensions/Logging/LogLevel/index.html)합니다. 로그 수준 심각도 또는 중요도 수준을 나타냅니다.  예를 들어, 작성할 수는 `Information` 메서드가 정상적으로 종료 될 때 로그는 `Warning` 메서드가 반환 하는 404 코드를 반환 하는 경우와 로그 `Error` 예기치 않은 예외를 catch 할 때 로그온 합니다.

다음 코드 예제에서는 메서드의 이름과 로그 수준을 지정합니다.

[!code-csharp[](logging/sample/src/TodoApi/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

메서드 이름에서 수준을 포함 하는 로그 메서드는 [ILogger 하기 위한 확장 메서드](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.loggerextensions) 하는 *Microsoft.Extensions.Logging* 패키지를 제공 합니다.  백그라운드에서 이러한 메서드 호출을 `Log` 를 받는 메서드에 `LogLevel` 매개 변수입니다. 호출할 수는 `Log` 구문을 이러한 확장 메서드를 실행 하는 것이 아니라 메서드를 직접은 비교적 복잡 합니다. 자세한 내용은 참조는 [ILogger 인터페이스](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.ilogger) 및 [로 거 확장 소스 코드](https://github.com/aspnet/Logging/blob/master/src/Microsoft.Extensions.Logging.Abstractions/LoggerExtensions.cs)합니다.

다음을 정의 하는 ASP.NET 핵심 [로그 수준](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.loglevel), 최소에 가장 높은 심각도에서 여기 주문 합니다.

* 추적 = 0

  개발자 에게만 중요 정보에 대 한 문제를 디버깅 합니다. 이러한 메시지는 중요 한 응용 프로그램 데이터 포함 하 고 있으므로 사용 하지 않아야 프로덕션 환경에서. *기본적으로 사용할 수 없습니다.* 예: `Credentials: {"User":"someuser", "Password":"P@ssword"}`

* 디버그 = 1

  개발 및 디버깅 하는 동안 단기 유용성에는 정보입니다. 예: `Entering method Configure with flag set to true.`

* 정보 = 2

  응용 프로그램의 일반적인 흐름을 추적 합니다. 이러한 로그는 일반적으로 장기 일부 값을 가집니다. 예: `Request received for path /api/todo`

* 경고 = 3

  응용 프로그램 흐름에 비정상적인 또는 예기치 않은 이벤트입니다. 이러한 오류 또는 응용 프로그램을 중지 하지 않게 하는 하지만 조사 해야 할 수도 있는 다른 조건이 포함 될 수 있습니다. 처리 된 예외는 사용 하 여 공통 장소는 `Warning` 로그 수준입니다. 예: `FileNotFoundException for file quotes.txt.`

* 오류 = 4

  오류 및 예외를 처리할 수 없습니다. 이 메시지는 현재 활동 또는 작업 (예: 현재 HTTP 요청)의 오류, 응용 프로그램 수준 오류가 발생 하지 나타냅니다. 예제 로그 메시지:`Cannot insert record due to duplicate key violation.`

* 중요 한 = 5

  즉각적인 주의가 필요한 오류입니다. 예를 들어 데이터 손실 시나리오, 디스크 공간이 부족 합니다.

얼마나 많은 로그 출력은 특정 저장소 매체에 기록 제어 하거나 창을 표시 하는 로그 수준을 사용할 수 있습니다. 예를 들어 프로덕션 환경에서 보아야의 모든 로그 `Information` 는 대용량 데이터 저장소와의 모든 로그로 돌아가려면 수준 이상 `Warning` 수준 및 더 높은 우선 순위가 높은 데이터 저장소로 이동 하 합니다. 개발 하는 동안 하면 일반적으로 지침을 제공의 로그만 `Warning` 를 콘솔에 더 높은 심각도 추가 또는 `Debug` 문제를 조사 해야 할 때 수준입니다. [로그 필터링](#log-filtering) 이 문서의 뒷부분에 나오는 섹션에서는 공급자를 처리 하는 로그 수준을 제어 하는 방법에 설명 합니다.

ASP.NET Core 프레임 워크 씁니다 `Debug` framework 이벤트에 대 한 로그입니다. 다음은 나타나는 콘솔 공급자에서 최소 로그 수준을 설정 샘플 응용 프로그램을 실행 하는 경우의 예 `Debug` URL로 이동 하 고 `http://localhost:5000/api/todo/0`:

```console
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:5000/api/todo/0
dbug: Microsoft.AspNetCore.StaticFiles.StaticFileMiddleware[4]
      The request path /api/todo/0 does not match a supported file type
dbug: Microsoft.AspNetCore.Routing.Tree.TreeRouter[1]
      Request successfully matched the route with name 'GetTodo' and template 'api/Todo/{id}'.
dbug: Microsoft.AspNetCore.Mvc.Internal.ActionSelector[2]
      Action 'TodoApi.Controllers.TodoController.Update (TodoApi)' with id '6cada879-f7a8-4152-b244-7b41831791cc' did not match the constraint 'Microsoft.AspNetCore.Mvc.Internal.HttpMethodActionConstraint'
dbug: Microsoft.AspNetCore.Mvc.Internal.ActionSelector[2]
      Action 'TodoApi.Controllers.TodoController.Delete (TodoApi)' with id '529c0e82-aea6-466c-bbe2-e77ded858853' did not match the constraint 'Microsoft.AspNetCore.Mvc.Internal.HttpMethodActionConstraint'
dbug: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action TodoApi.Controllers.TodoController.GetById (TodoApi)
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
info: TodoApi.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApi.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
dbug: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action method TodoApi.Controllers.TodoController.GetById (TodoApi), returned result Microsoft.AspNetCore.Mvc.NotFoundResult.
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 198.8402ms
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[2]
      Request finished in 550.6837ms 404
dbug: Microsoft.AspNetCore.Server.Kestrel[9]
      Connection id "0HKV8M5ARIH5P" completed keep alive response.
```

## <a name="log-event-id"></a>로그 이벤트 ID

때마다 로그를 작성 하면 지정할 수는 *이벤트 ID*합니다. 샘플 응용 프로그램 로컬 정의 사용 하 여 이것이 `LoggingEvents` 클래스:

[!code-csharp[](logging/sample/src/TodoApi/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](logging/sample/src/TodoApi/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

이벤트 ID는 기록 된 이벤트 집합을 서로 연결 하는 데 사용할 수 있는 정수 값입니다. 예를 들어, 장바구니에 항목 추가 대 한 로그에 이벤트 ID 1000 수 및 구매를 완료 하는 것에 대 한 로그에 이벤트 ID 1001 수 있습니다.

로깅 출력의 이벤트 ID 필드에 저장 되었거나 공급자에 따라 텍스트 메시지에 포함 합니다.  디버그 공급자는 이벤트 Id를 표시 하지는 않습니다 하지만 콘솔 공급자 항목 뒤에 대괄호 표시:

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-format-string"></a>로그 메시지 형식 문자열

로그에 쓸 때마다 텍스트 메시지를 제공 합니다. 메시지 문자열 인수에 값을 배치 하는 다음 예제 에서처럼 명명 된 자리 표시자를 포함할 수 있습니다.

[!code-csharp[](logging/sample/src/TodoApi/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

자리 표시자를 해당 이름이 아니라 순서는 매개 변수는 사용을 결정 합니다. 예를 들어 다음과 같은 코드를 가정해 봅니다.

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

결과 로그 메시지는 다음과 같습니다.

```
Parameter values: parm1, parm2
```

로깅 프레임 워크를 구현 하는 로깅 공급자에 대 한 수 있도록이 방식으로 서식 지정 메시지는 [구조화 된 로깅을 라고도 하는 의미 체계 로깅](http://programmers.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)합니다. 인수 자체 뿐만 아니라 형식이 지정 된 메시지 문자열, 로깅 시스템으로 전달 되므로 로깅 공급자 메시지 문자열 외에도 필드와 매개 변수 값을 저장할 수 있습니다. 예를 들어 옮기는 프로그램 로그 출력을 Azure 테이블 저장소 및 메서드 호출으로 거는 다음과 같습니다.

```csharp
_logger.LogInformation("Getting item {ID} at {RequestTime}", id, DateTime.Now);
```

각 Azure 테이블 엔터티를 가지기 `ID` 및 `RequestTime` 로그 데이터에 대 한 쿼리를 간소화 하는 속성입니다. 특정 내에서 모든 로그를 찾을 수 `RequestTime` 문자 메시지의 시간 초과 구문 분석할 필요 없이 범위입니다.

## <a name="logging-exceptions"></a>예외 기록

로 거 메서드 다음 예제와 같이 예외를 전달할 수 있도록 하는 오버 로드를 갖고 있습니다.

[!code-csharp[](logging/sample/src/TodoApi/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

다른 공급자는 다른 방법으로 예외 정보를 처리합니다. 위의 코드에서 디버그 공급자 출력의 예는 다음과 같습니다.

```
TodoApi.Controllers.TodoController:Warning: GetById(036dd898-fb01-47e8-9a65-f92eb73cf924) NOT FOUND

System.Exception: Item not found exception.
 at TodoApi.Controllers.TodoController.GetById(String id) in C:\logging\sample\src\TodoApi\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a>로그 필터링

일부 로깅 공급자 로그 수준 및 범주에 따라 로그를 저장 매체에 기록 또는 무시 시기를 지정할 수 있습니다.

`AddConsole` 및 `AddDebug` 확장 메서드는 필터링 기준에 전달할 수 있는 오버 로드를 제공 합니다. 다음 샘플 코드 때문에 무시 로그 아래 콘솔 공급자 `Warning` 디버그 공급자 프레임 워크를 만들고 로그를 무시 하는 동안 조정 합니다.

[!code-csharp[](logging/sample/src/TodoApi/Startup.cs?name=snippet_AddConsoleAndDebugWithFilter&highlight=6-7)]

`AddEventLog` 메서드는 오버 로드를는 `EventLogSettings` 필터링 함수에 포함 될 수 있는 인스턴스를 해당 `Filter` 속성입니다. TraceSource 공급자에서는 이러한 오버 로드 중 하나는 로깅 수준 및 다른 매개 변수 기반으로 하므로 `SourceSwitch` 및 `TraceListener` 사용 합니다.

등록 된 모든 공급자에 대 한 필터링 규칙을 설정할 수는 `ILoggerFactory` 를 사용 하 여 인스턴스는 `WithFilter` 확장 메서드. 아래 예제에서는 디버그 수준에서 응용 프로그램 로그 하는 동안 경고를 프레임 워크 로그 (범주는 "Microsoft" 또는 "System"로 시작)를 제한 합니다.

[!code-csharp[](logging/sample/src/TodoApi/Startup.cs?name=snippet_FactoryFilter&highlight=6-11)]

특정 범주에 대 한 기록에서 모든 로그를 방지 하기 위해 필터링을 사용 하려는 경우를 지정할 수 있습니다 `LogLevel.None` 해당 범주에 대 한 최소 로그 수준으로 합니다. 정수 값 `LogLevel.None` 이 6 이면 보다 높은 `LogLevel.Critical` (5).

`WithFilter` 제공한 확장 메서드는 [Microsoft.Extensions.Logging.Filter](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Filter) NuGet 패키지입니다. 메서드는 새 `ILoggerFactory` 모든로 거 공급자에 전달 되는 로그 메시지를 필터링 하는 인스턴스 등록 된 것입니다. 다른 모든 변경 되지 않습니다 `ILoggerFactory` 인스턴스를 원래 `ILoggerFactory` 인스턴스.

## <a name="log-scopes"></a>로그 범위

내에서 논리적 작업 집합을 그룹화 할 수는 *범위* 해당 집합의 일부로 생성 된 각 로그를 동일한 데이터를 연결 하려면.  예를 들어, 트랜잭션 ID를 포함 하도록 트랜잭션을 처리의 일부로 만들어진 모든 로그 경우가

범위는는 `IDisposable` 에서 반환 되는 형식에서 `ILogger.BeginScope<TState>` 메서드와 삭제 될 때까지 유지 됩니다. 범위를 사용 하 여 배치의 여로 거를 호출 하 여 한 `using` 블록을 다음과 같이 합니다.

[!code-csharp[](logging/sample/src/TodoApi/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

다음 코드를 사용 하면 콘솔 공급자에 대 한 범위:

[!code-csharp[](logging/sample/src/TodoApi/Startup.cs?name=snippet_Scopes&highlight=6)]

각 로그 메시지에는 범위가 지정 된 정보가 포함 됩니다.

```
info: TodoApi.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApi.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApi.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApi.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a>기본 제공 된 로깅 공급자

ASP.NET Core 다음 공급자를 제공 합니다.

* [콘솔](#console)
* [디버그](#debug)
* [EventSource](#eventsource)
* [EventLog](#eventlog)
* [TraceSource](#tracesource)
* [Azure App Service](#appservice)

<a id="console"></a>
### <a name="the-console-provider"></a>콘솔 공급자

[Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) 공급자 패키지 로그 출력을 콘솔에 보냅니다. 

```csharp
loggerFactory.AddConsole()
```

[AddConsole 오버 로드](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.consoleloggerextensions) 에 전달할 수는 최소 로그 수준, 필터 함수 및 범위를 지원 하는지 여부를 나타내는 부울입니다.  다른 옵션은 전달 하는 `IConfiguration` 범위 지원 및 로깅 수준을 지정할 수 있는 개체입니다. 

프로덕션에 사용할 콘솔 공급자를 고려 하는 경우 성능에 큰 영향에 주의 합니다.

Visual Studio에서 새 프로젝트를 만들 때의 `AddConsole` 메서드는 다음과 같습니다.

```csharp
loggerFactory.AddConsole(Configuration.GetSection("Logging"));
```

이 코드에서 참조 하는 `Logging` 의 섹션은 *appSettings.json* 파일:

[!code-json[](logging/sample/src/TodoApi/appsettings.json)]

에 설명 된 대로 디버그 수준에서 기록 하는 앱 수 있도록 하는 동안 제한 프레임 워크 로그 경고를 표시 설정을 [로그 필터링](#log-filtering) 섹션입니다. 자세한 내용은 참조 [구성](configuration.md)합니다.

<a id="debug"></a>
### <a name="the-debug-provider"></a>디버그 공급자

[Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) 공급자 패키지 로그 출력을 사용 하 여 작성 된 [System.Diagnostics.Debug](https://docs.microsoft.com/dotnet/core/api/system.diagnostics.debug#System_Diagnostics_Debug) 클래스.

```csharp
loggerFactory.AddDebug()
```

[AddDebug 오버 로드](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.debugloggerfactoryextensions) 최소 로그 수준 또는 필터 함수에 전달할 수 있도록 합니다.

<a id="eventsource"></a>
### <a name="the-eventsource-provider"></a>EventSource 공급자

ASP.NET Core 1.1.0을 대상으로 하는 앱에 대 한 이상는 [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) 공급자 패키지 이벤트 추적을 구현할 수 있습니다. Windows에서 사용 하 여 [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803)합니다. 공급자가 여러 플랫폼 간에 있습니다 이벤트가 Linux 또는 macOS 용 아직 수집 및 표시 도구. 

```csharp
loggerFactory.AddEventSourceLogger()
```

수집 하 고 로그를 표시 하는 가장 좋은 방법은 사용 하는 것은 [PerfView 유틸리티](https://www.microsoft.com/en-us/download/details.aspx?id=28567)합니다. ETW 로그를 확인 하기 위한 기타 도구 있지만 PerfView ASP.NET에서 내보낸 ETW 이벤트를 사용 하기 위한 최상의 환경을 제공 합니다. 

이 공급자에 의해 기록 된 이벤트를 수집 하기 위한 PerfView를 구성 하려면 문자열을 추가 `*Microsoft-Extensions-Logging` 에 **추가 공급자** 목록입니다. (문자열의 시작 부분에 별표를 놓치지 마십시오.)

![Perfview 추가 공급자](logging/_static/perfview-additional-providers.png)

Nano 서버에 데이터를 캡처하고 사항이 몇 가지 추가 설정이 필요 합니다.

* PowerShell 원격 Nano 서버에 연결 합니다.

  ```powershell
  Enter-PSSession [name]
  ```

* ETW 세션을 만듭니다.

  ```powershell
  New-EtwTraceSession -Name "MyAppTrace" -LocalFilePath C:\trace.etl
  ```

* ETW 공급자에 대 한 추가 [CLR](https://msdn.microsoft.com/library/ff357718), ASP.NET 핵심 및 필요에 따라 다른 사용자입니다. GUID는 ASP.NET 핵심 공급자 `3ac73b97-af73-50e9-0822-5da4367920d0`합니다. 

  ```powershell
  Add-EtwTraceProvider -Guid "{e13c0d23-ccbc-4e12-931b-d9cc2eee27e4}" -SessionName MyAppTrace
  Add-EtwTraceProvider -Guid "{3ac73b97-af73-50e9-0822-5da4367920d0}" -SessionName MyAppTrace
  ```

* 사이트를 실행 하 고 추적 정보를 원하는 작업을 수행 합니다.

* 마치면 추적 세션을 중지 합니다.

  ```powershell
  Remove-EtwTraceSession MyAppTrace
  ```

그 결과 *C:\trace.etl* 다른 Windows 버전에서와 같이 PerfView와 파일을 분석할 수 있습니다.

<a id="eventlog"></a>
### <a name="the-windows-eventlog-provider"></a>Windows 이벤트 로그 공급자

[Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) 공급자 패키지는 Windows 이벤트 로그에 로그 출력을 보냅니다.

```csharp
loggerFactory.AddEventLog()
```

[AddEventLog 오버 로드](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.eventloggerfactoryextensions) 에 전달할 수 있도록 `EventLogSettings` 또는 최소 로그 수준입니다.

<a id="tracesource"></a>
### <a name="the-tracesource-provider"></a>TraceSource 공급자

[Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) 공급자 패키지에서 사용 하 여 [System.Diagnostics.TraceSource](https://msdn.microsoft.com/library/system.diagnostics.tracesource.aspx) 라이브러리 및 공급자입니다.

```csharp
loggerFactory.AddTraceSource(sourceSwitchName);
```

[AddTraceSource 오버 로드](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.tracesourcefactoryextensions) 소스 스위치와 추적 수신기에 전달 되도록 합니다.

이 공급자를 사용 하려면 응용 프로그램은.NET Framework (아니라.NET Core)에서 실행 수 있습니다. 다양 한 메시지를 라우팅하는 공급자를 사용 하면 [수신기](https://msdn.microsoft.com/library/4y5y10s7)와 같은 [TextWriterTraceListener](https://msdn.microsoft.com/library/system.diagnostics.textwritertracelistener) 샘플 응용 프로그램에 사용 합니다.

다음 예제에서는 구성 된 `TraceSource` 로그 공급자 `Warning` 및 더 높은 메시지를 콘솔 창.

[!code-csharp[](logging/sample/src/TodoApi/Startup.cs?name=snippet_TraceSource&highlight=8-12)]

<a id="appservice"></a>
### <a name="the-azure-app-service-provider"></a>Azure 앱 서비스 공급자

[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) 공급자 패키지는 Azure 앱 서비스 응용 프로그램의 파일 시스템에 텍스트 파일 로그에 기록 [blob 저장소](https://azure.microsoft.com/en-us/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) 는 Azure 저장소 계정에 있습니다. 공급자는 ASP.NET 핵심 1.1.0을 대상으로 하는 응용 프로그램에만 사용할 수는 있습니다. 

```csharp
loggerFactory.AddAzureWebAppDiagnostics();
```

`AddAzureWebAppDiagnostics` 에 전달할 수 있습니다 오버 로드 [AzureAppServicesDiagnosticsSettings](https://github.com/aspnet/Logging/blob/c7d0b1b88668ff4ef8a86ea7d2ebb5ca7f88d3e0/src/Microsoft.Extensions.Logging.AzureAppServices/AzureAppServicesDiagnosticsSettings.cs)는 로깅 출력 서식 파일, blob 이름 및 파일 크기 제한을 같은 기본 설정을 재정의할 수 있습니다. (*출력 템플릿을* 는 위에 호출 하는 경우를 제공 하는 모든 로그에 적용 되는 메시지 형식 문자열은 `ILogger` 메서드.)  

응용 프로그램의 설정을 인식 앱 서비스 앱에 배포 하는 경우는 [진단 로그](https://azure.microsoft.com/en-us/documentation/articles/web-sites-enable-diagnostic-log/#enablediag) 섹션은 **앱 서비스** Azure 포털의 블레이드입니다. 이러한 설정을 변경 하면 변경 내용이 즉시 적용 앱을 다시 시작 하거나 코드를 다시 배포 하는 요구 하지 않고 됩니다. 

![Azure 로깅 설정](logging/_static/azure-logging-settings.png)

로그 파일에 대 한 기본 위치는는 *d:\\홈\\LogFiles\\응용 프로그램* 폴더 및 기본 파일 이름은 *진단 yyyymmdd.txt*합니다. 기본 파일 크기 제한을 10 MB 이며 유지 하는 파일의 기본 최대 수는 2입니다. 기본 blob 이름은 *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*합니다. 기본 동작에 대 한 자세한 내용은 참조 [AzureAppServicesDiagnosticsSettings](https://github.com/aspnet/Logging/blob/c7d0b1b88668ff4ef8a86ea7d2ebb5ca7f88d3e0/src/Microsoft.Extensions.Logging.AzureAppServices/AzureAppServicesDiagnosticsSettings.cs)합니다.

공급자는 Azure 환경에서 프로젝트를 실행 하는 경우에 작동 합니다.  영향을 주지 않습니다-로컬로 실행할 때 그 쓰지 않습니다 blob에 대 한 로컬 개발 저장소 또는 로컬 파일.

> [!NOTE]
> 응용 프로그램에 로그인 하는 응용 프로그램 서비스를 설정 하는 또 다른 방법은 있는지 공급자 기본 설정을 변경할 필요가 없습니다. 설치 [Microsoft.AspNetCore.AzureAppServicesIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServicesIntegration/) (종속성으로 로깅 패키지 포함), 해당 확장 메서드를 호출 하 고 `WebHostBuilder` 에 프로그램 `Main` 메서드.
>
> ```csharp
> var host = new WebHostBuilder()
>     .UseKestrel()    
>     .UseAzureAppServices()    
>     .UseStartup<Startup>()    
>     .Build();
> ```
>
>  내부적으로 `UseAzureAppServices` 호출 `UseIISIntegration` 와 로깅 공급자 확장 메서드의 `AddAzureWebAppDiagnostics`합니다.

## <a name="third-party-logging-providers"></a>타사 로깅 공급자

ASP.NET Core를 사용 하는 일부 타사 로깅 프레임 워크는 다음과 같습니다.

   * [elmah.io](https://github.com/elmahio/Elmah.Io.Extensions.Logging) -Elmah.Io 서비스 공급자

   * [Loggr](https://github.com/imobile3/Loggr.Extensions.Logging) -Loggr 서비스 공급자

   * [NLog](https://github.com/NLog/NLog.Extensions.Logging) -NLog 라이브러리에 대 한 공급자

   * [Serilog](https://github.com/serilog/serilog-framework-logging) -Serilog 라이브러리에 대 한 공급자

일부 타사 프레임 워크 작업을 수행할 수 [구조화 된 로깅을 라고도 하는 의미 체계 로깅](http://programmers.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)합니다.

타사 프레임 워크를 사용 하 여 기본 제공 공급자 중 하나를 사용 하 여 비슷합니다: 프로젝트에 NuGet 패키지를 추가 하 고에 확장 메서드를 호출 `ILoggerFactory`합니다. 자세한 내용은 각 프레임 워크 설명서를 참조 합니다.

다른 로깅 프레임 워크 또는 사용자의 로깅 요구 사항을 지원 하기 위해, 사용자 고유의 사용자 지정 공급자를 만들 수 있습니다.

