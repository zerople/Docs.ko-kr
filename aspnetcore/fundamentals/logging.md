---
title: "ASP.NET Core 로그인"
author: ardalis
description: "로깅 프레임 워크에서 ASP.NET Core를 소개합니다. 각 기본 제공 로깅 공급자 및 인기 있는 일부 타사 공급자에 대 한 링크에 대 한 섹션이 포함 되어 있습니다."
keywords: "ASP.NET Core, 로깅, 로깅 providers,Microsoft.Extensions.Logging,ILogger,ILoggerFactory,LogLevel,WithFilter,TraceSource,EventLog,EventSource,scopes"
ms.author: tdykstra
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: ac27ac68-d76a-4f8e-b8ab-ea045803e5f2
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/logging
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ca81f01fe1c5026514eafedf852b4bc8f3b6fd21
ms.sourcegitcommit: 78d28178345a0eea91556e4cd1adad98b1446db8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2017
---
# <a name="introduction-to-logging-in-aspnet-core"></a>ASP.NET Core 로그인 소개

여 [Steve Smith](https://ardalis.com/) 및 [Tom Dykstra](https://github.com/tdykstra)

ASP.NET Core 다양 한 로깅 공급자와 작동 하는 로깅 API를 지원 합니다. 하나 이상의 대상에 로그를 보낼 기본 제공 공급자 수 있으며, 타사 로깅 프레임 워크에 연결할 수 있습니다. 이 문서에는 코드에는 기본 제공 로깅 API 및 공급자를 사용 하는 방법을 보여 줍니다.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

[샘플 코드 보기 또는 다운로드](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/logging/sample2)

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

[샘플 코드 보기 또는 다운로드](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/logging/sample)

---

## <a name="how-to-create-logs"></a>로그를 만드는 방법

로그를 만들려면 가져오기는 `ILogger` 에서 개체는 [종속성 주입](dependency-injection.md) 컨테이너:

[!code-csharp[](logging/sample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

그런 다음 해당로 거 개체에 대해 로깅 메서드를 호출할:

[!code-csharp[](logging/sample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

사용 하 여 로그를 만드는이 예제는 `TodoController` 으로 클래스는 *범주*합니다.  범주 설명 [이 문서의 뒷부분에 나오는](#log-category)합니다.

ASP.NET Core에서는 비동기로 거 메서드 로깅 async 사용의 비용 보다 중요 없음을 빠른 이어야 하기 때문에 합니다. true가 아니면 하는 상황에 있는 경우에 로그온 하는 방식을 변경 하는 것이 좋습니다.  데이터 저장소 느린 경우 먼저, 빠른 저장소 로그 메시지가 작성 한 다음 나중에 느린 스토어로 이동 합니다. 예를 들어 읽고 다른 프로세스에 의해 속도가 느린 저장소에 유지 하는 메시지 큐에 기록 합니다.

## <a name="how-to-add-providers"></a>공급자를 추가 하는 방법

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

로깅 공급자를 사용 하 여 만든 메시지를 가져와 `ILogger` 개체 및 표시 하거나 저장 합니다. 예를 들어 콘솔에서 메시지를 표시 하는 콘솔 공급자 및 Azure 앱 서비스 공급자를 사용 하는 Azure blob 저장소에 저장할 수 있습니다.

공급자를 사용 하려면 공급자의 `Add<ProviderName>` 에 확장 메서드 *Program.cs*:

[!code-csharp[](logging/sample2/Program.cs?name=snippet_ExpandDefault&highlight=16,17)]

기본 프로젝트 템플릿은 위의 코드에서 참조 방식으로 로깅 설정 되지만 `ConfigureLogging` 호출 하면 됩니다는 `CreateDefaultBuilder` 메서드. 다음은 코드 *Program.cs* 프로젝트 템플릿으로 만들어진:

[!code-csharp[](logging/sample2/Program.cs?name=snippet_TemplateCode&highlight=7)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

로깅 공급자를 사용 하 여 만든 메시지를 가져와 `ILogger` 개체 및 표시 하거나 저장 합니다. 예를 들어 콘솔에서 메시지를 표시 하는 콘솔 공급자 및 Azure 앱 서비스 공급자를 사용 하는 Azure blob 저장소에 저장할 수 있습니다.

공급자를 사용 하려면 NuGet 패키지를 설치 하 고 인스턴스에 공급자의 확장 메서드를 호출 `ILoggerFactory`다음 예제에 나온 것 처럼 합니다.

[!code-csharp[](logging/sample//Startup.cs?name=snippet_AddConsoleAndDebug&highlight=3,5-7)]

ASP.NET Core [종속성 주입](dependency-injection.md) DI ()를 제공 된 `ILoggerFactory` 인스턴스. `AddConsole` 및 `AddDebug` 에 정의 된 확장 메서드는 [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/) 및 [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug/) 패키지 합니다. 각 확장 메서드 호출의 `ILoggerFactory.AddProvider` 공급자의 인스턴스에 전달 하는 메서드. 

> [!NOTE]
> 이 문서에 대 한 샘플 응용 프로그램 추가에서 로깅 공급자는 `Configure` 의 메서드는 `Startup` 클래스입니다. 이전에 실행 되는 코드에서 로그 출력을 가져올 하려는 경우 추가에 대 한 로깅 공급자는 `Startup` 클래스 생성자를 대신 합니다. 

---

각각에 대 한 정보를 찾을 수 [기본 제공 로깅 공급자](#built-in-logging-providers) 에 연결 하 고 [제 3 자 로깅 공급자](#third-party-logging-providers) 는 문서의 뒷부분에 나오는 합니다.

## <a name="sample-logging-output"></a>예제 로깅 출력

이전 섹션에 나와 있는 샘플 코드를 있는 명령줄에서 실행할 때 콘솔에 있는 로그를 표시 됩니다. 콘솔 출력의 예는 다음과 같습니다.

```console
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:5000/api/todo/0
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
info: TodoApi.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApi.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 42.9286ms
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[2]
      Request finished in 148.889ms 404
```
 
이러한 로그를 이동 하 여 생성 된 `http://localhost:5000/api/todo/0`, 둘 다의 실행을 트리거하는 `ILogger` 이전 섹션에서 볼 수 있는 호출 합니다.

다음은 Visual Studio에서 예제 응용 프로그램을 실행 하는 경우 디버그 창에 표시 된 대로 동일한 로그의 예가입니다.

```console
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request starting HTTP/1.1 GET http://localhost:53104/api/todo/0  
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
TodoApi.Controllers.TodoController:Information: Getting item 0
TodoApi.Controllers.TodoController:Warning: GetById(0) NOT FOUND
Microsoft.AspNetCore.Mvc.StatusCodeResult:Information: Executing HttpStatusCodeResult, setting HTTP status code 404
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 152.5657ms
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request finished in 316.3195ms 404 
```

생성 된 로그는 `ILogger` 이전 섹션에서 볼 수 있는 호출 "TodoApi.Controllers.TodoController"로 시작 합니다. ASP.NET Core에서 "Microsoft" 범주로 시작 하는 로그는 합니다. ASP.NET Core 자체 및 응용 프로그램 코드와 동일한 로깅 API와 동일한 로깅 공급자 사용 하는 합니다.

이 문서의 나머지 부분에서는 일부 세부 정보 및 로깅에 대 한 옵션을 설명합니다.

## <a name="nuget-packages"></a>NuGet 패키지

`ILogger` 및 `ILoggerFactory` 인터페이스에는 [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), 있으며에 대 한 기본 구현을에서 [Microsoft.Extensions.Logging](https://www.nuget.org/packages/Microsoft.Extensions.Logging/)합니다.

## <a name="log-category"></a>로그 범주

A *범주* 를 만들면 각 로그에 포함 되어 있습니다.  만들 때 범주를 지정 된 `ILogger` 개체입니다. 범주에는 모든 문자열을 사용할 수 있지만 로그는 작성 된 클래스의 정규화 된 이름을 사용 하는 규칙은.  예: "TodoApi.Controllers.TodoController"입니다.

범주를 지정 하는 문자열로 하거나 범주 형식에서 파생 하는 확장 메서드를 사용할 수 있습니다. 범주 문자열을 지정 하려면 호출 `CreateLogger` 에 `ILoggerFactory` 인스턴스를 다음과 같이 합니다.

[!code-csharp[](logging/sample//Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

대부분의 경우는 것이를 사용 하기 쉽게 `ILogger<T>`다음 예제와 같이, 합니다.

[!code-csharp[](logging/sample//Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

이 호출에 해당 하는 `CreateLogger` 의 정규화 된 형식 이름으로 `T`합니다.

## <a name="log-level"></a>로그 수준

때마다 지정할에 로그를 쓸 해당 [LogLevel](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.loglevel)합니다. 로그 수준 심각도 또는 중요도 수준을 나타냅니다.  예를 들어 작성할 수 있습니다는 `Information` 메서드가 정상적으로 종료 될 때 로그는 `Warning` 로그 메서드가 반환 하는 404가 반환 코드를 반환 하는 경우 및는 `Error` 예기치 않은 예외를 catch 하는 때를 기록 합니다.

다음 코드 예제에서는 메서드의 이름 (예를 들어 `LogWarning`) 로그 수준을 지정 합니다.  첫 번째 매개 변수는 [이벤트 ID를 로그](#log-event-id) (이 문서의 뒷부분에 설명).  나머지 매개 변수는 로그 메시지 문자열을 생성 합니다.

[!code-csharp[](logging/sample//Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

수준은 메서드 이름에 포함 된 로그 방법은 [ILogger 확장 메서드가](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.loggerextensions)합니다. 이러한 메서드 호출 내부적으로 `Log` 를 받는 메서드에 `LogLevel` 매개 변수입니다. 호출할 수 있습니다는 `Log` 구문을 하지만 이러한 확장 메서드를 실행 하는 것이 아니라 메서드를 직접은 상대적으로 복잡 합니다. 자세한 내용은 참조는 [ILogger 인터페이스](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.ilogger) 및 [로 거 확장 소스 코드](https://github.com/aspnet/Logging/blob/master/src/Microsoft.Extensions.Logging.Abstractions/LoggerExtensions.cs)합니다.

다음을 정의 하는 ASP.NET Core [로그 수준](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.loglevel), 최소에 가장 높은 심각도에서 여기 순으로 정렬 합니다.

* 추적 = 0

  개발자 에게만 중요 정보에 대 한 문제를 디버깅 합니다. 이러한 메시지는 중요 한 응용 프로그램 데이터 포함 하 고 있으므로 사용 하지 않아야 프로덕션 환경에서. *기본적으로 사용 하지 않습니다.* 예: `Credentials: {"User":"someuser", "Password":"P@ssword"}`

* 디버그 = 1

  에 대 한 단기 유용성 개발 및 디버깅 하는 동안에 정보입니다. 예: `Entering method Configure with flag set to true.` 일반적으로 설정 하지는 `Debug` 수준은 로그 양이 많기 때문에 해결 된 경우가 아니면 프로덕션 환경에 기록 합니다.

* 정보 2 =

  응용 프로그램의 일반적인 흐름을 추적 합니다. 이러한 로그는 일반적으로 일부 장기 값을 가집니다. 예: `Request received for path /api/todo`

* 경고 = 3

  응용 프로그램 흐름에 비정상적인 또는 예기치 않은 이벤트에 대 한 여기에 오류 또는 기타 조건을 중지 하려면 응용 프로그램을 일으키지 않습니다 이지만 조사 해야 할 수 있는 포함 될 수 있습니다. 처리 된 예외는 사용할 수 있는 일반적인 위치는 `Warning` 로그 수준입니다. 예: `FileNotFoundException for file quotes.txt.`

* 오류 = 4

  오류 및 예외를 처리할 수 없습니다. 이 메시지는 현재 작업 또는 작업 (예: 현재 HTTP 요청) 오류가 발생할 경우 전체 응용 프로그램 오류가 발생 하지 않습니다 나타냅니다. 예제 로그 메시지:`Cannot insert record due to duplicate key violation.`

* 중요 한 = 5

  오류에 대 한 즉각적인 주목이 필요한입니다. 예: 데이터 손실 시나리오를 디스크 공간이 부족 합니다.

로그 수준의을 특정 저장 매체에 얼마나 많은 로그 출력은 기록 제어 하거나 창을 표시에 사용할 수 있습니다. 예를 들어 프로덕션 환경에서 수 원하는의 모든 로그 `Information` 수준 하 고 볼륨 데이터 저장소와의 모든 로그를 이동할 수를 줄이려면 `Warning` 수준 이상 값 데이터 저장소로 이동 합니다. 개발 하는 동안 일반적으로 보내는 경우가의 로그 `Warning` 또는 콘솔에 더 높은 심각도입니다. 문제를 해결 해야 할 때 추가 하 여 `Debug` 수준입니다. [로그 필터링](#log-filtering) 이 문서의 뒷부분에 나오는 섹션에서는 공급자가 처리 하는 로그 수준을 제어 하는 방법에 설명 합니다.

ASP.NET Core framework 씁니다 `Debug` 프레임 워크 이벤트에 대 한 로그 수준입니다. 로그 예제 앞부분이 문서에서 제외 아래 로그 `Information` 수준 하지 않게 `Debug` 로그 수준으로 표시 되었습니다. 한 예로 콘솔 로그를 표시 하도록 구성 하는 샘플 응용 프로그램을 실행 하는 경우 `Debug` 및 콘솔 공급자에 대 한 높은 로그입니다.

```console
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:62555/api/todo/0
dbug: Microsoft.AspNetCore.Routing.Tree.TreeRouter[1]
      Request successfully matched the route with name 'GetTodo' and template 'api/Todo/{id}'.
dbug: Microsoft.AspNetCore.Mvc.Internal.ActionSelector[2]
      Action 'TodoApi.Controllers.TodoController.Update (TodoApi)' with id '089d59b6-92ec-472d-b552-cc613dfd625d' did not match the constraint 'Microsoft.AspNetCore.Mvc.Internal.HttpMethodActionConstraint'
dbug: Microsoft.AspNetCore.Mvc.Internal.ActionSelector[2]
      Action 'TodoApi.Controllers.TodoController.Delete (TodoApi)' with id 'f3476abe-4bd9-4ad3-9261-3ead09607366' did not match the constraint 'Microsoft.AspNetCore.Mvc.Internal.HttpMethodActionConstraint'
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
      Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 0.8788ms
dbug: Microsoft.AspNetCore.Server.Kestrel[9]
      Connection id "0HL6L7NEFF2QD" completed keep alive response.
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[2]
      Request finished in 2.7286ms 404
```

## <a name="log-event-id"></a>로그 이벤트 ID

각 시간에 로그를 쓸 지정할 수 있습니다는 *이벤트 ID*합니다. 샘플 앱은이 작업을 로컬로 정의 사용 하 여 수행 `LoggingEvents` 클래스:

[!code-csharp[](logging/sample//Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](logging/sample//Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

이벤트 ID는 기록 된 이벤트 집합이 서로 상호 연결 하는 데 사용할 수 있는 정수 값입니다. 예를 들어, 장바구니에 항목을 추가 대 한 로그에 이벤트 ID 1000 수 및 구매를 완료에 대 한 로그에 이벤트 ID 1001 수 있습니다.

로깅 출력의 이벤트 ID 필드에 저장 되었거나 공급자에 따라 텍스트 메시지에 포함 합니다.  디버그 공급자에서 이벤트 Id를 표시 하지 않지만 콘솔 공급자 찍고 대괄호로 항목 뒤:

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-format-string"></a>로그 메시지 형식 문자열

로그에 쓸 때마다 텍스트 메시지를 제공 합니다. 메시지 문자열 인수에 값을 배치 하는 다음 예제 에서처럼 명명 된 자리 표시자를 포함할 수 있습니다.

[!code-csharp[](logging/sample//Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

자리 표시자, 해당 이름이 아니라 순서 어떤 매개 변수는 사용을 결정 합니다. 예를 들어 다음과 같은 코드를 가정해 봅니다.

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

이 결과 로그 메시지는 다음과 같습니다.

```
Parameter values: parm1, parm2
```

로깅 공급자가 구현 하는 수 있도록이 방법으로 서식을 로깅 프레임 워크는 메시지 [구조적된 로깅 라고도 하는 의미 체계 로깅](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)합니다. 인수 자체 뿐 아니라 서식이 지정 된 메시지 문자열, 로깅 시스템으로 전달 되기 때문에 로깅 공급자 메시지 문자열 외에도 필드로 매개 변수 값을 저장할 수 있습니다. 예를 들어 옮기는 경우 Azure 테이블 저장소에 로그를 출력 하 고로 거 메서드 호출은 다음과 같습니다.

```csharp
_logger.LogInformation("Getting item {ID} at {RequestTime}", id, DateTime.Now);
```

각 Azure 테이블 엔터티를 가지기 `ID` 및 `RequestTime` 속성으로, 로그 데이터에 대 한 쿼리를 단순화 합니다. 특정 내에서 모든 로그를 찾을 수 `RequestTime` 문자 메시지의 시간 초과 구문 분석할 필요 없이 범위입니다.

## <a name="logging-exceptions"></a>예외 기록

로 거 메서드 다음 예제와 같이 예외를 전달할 수 있는 오버 로드를 갖고 있습니다.

[!code-csharp[](logging/sample//Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

다른 공급자는 다양 한 방식에서 예외 정보를 처리합니다. 위의 코드에서 디버그 공급자 출력의 예를 들면 다음과 같습니다.

```
TodoApi.Controllers.TodoController:Warning: GetById(036dd898-fb01-47e8-9a65-f92eb73cf924) NOT FOUND

System.Exception: Item not found exception.
 at TodoApi.Controllers.TodoController.GetById(String id) in C:\logging\sample\src\TodoApi\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a>로그 필터링

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

특정 공급자 및 범주 또는 모든 공급자 또는 모든 범주에 대 한 최소 로그 수준을 지정할 수 있습니다.  최소 수준 미만인 모든 로그 있으므로 표시 가져오거나 저장 하지 않는 것 해당 공급자에 전달 되지 않습니다. 

모든 로그를 표시 하려는 경우 지정할 수 있습니다 `LogLevel.None` 최소 로그 수준으로 됩니다. 정수 값 `LogLevel.None` 은 6 이며 보다 높은 `LogLevel.Critical` (5).

**구성에서 필터 규칙 만들기**

프로젝트 템플릿 만들기 호출 하는 코드 `CreateDefaultBuilder` 콘솔 및 디버그 공급자에 대 한 로깅을 설정 합니다. `CreateDefaultBuilder` 메서드 설정 로깅을 구성에 대 한 조회 하는 `Logging` 섹션에서 다음과 같은 코드를 사용 하 여:

[!code-csharp[](logging/sample2/Program.cs?name=snippet_ExpandDefault&highlight=15)]

구성 데이터 공급자 및 다음 예제와 같이 범주에서 최소 로그 수준을 지정합니다.

[!code-json[](logging/sample2/appsettings.json)]

이 JSON 디버그 공급자에 대해 하나씩, 모든 공급자에 적용 되는 개와 콘솔 공급자에 대 한 4 6 개의 필터 규칙을 만듭니다. 각 공급자에 대 한 이러한 규칙 중 하나만 어떻게 선택 표시 때는 `ILogger` 개체가 만들어집니다.

**코드에서 필터 규칙**

다음 예제와 같이 코드에서 필터 규칙을 등록할 수 있습니다.

[!code-csharp[](logging/sample2/Program.cs?name=snippet_FilterInCode&highlight=4-5)]

두 번째 `AddFilter` 형식 이름을 사용 하 여 디버그 공급자를 지정 합니다. 첫 번째 `AddFilter` 공급자 형식을 지정 하지 않습니다 때문에 모든 공급자에 적용 됩니다.

**필터링 규칙 방식 적용**

구성 데이터 및 `AddFilter` 앞의 예제에 표시 된 코드는 다음 표에 표시 된 규칙을 만듭니다. 처음 여섯 개 구성 예제에서 제공 하 고 마지막 두 코드 예제에서 제공 합니다.

수|공급자|로 시작 하는 범주|최소 로그 수준|
------|--------|--------------------------|-----------------|
1|디버그|모든 범주|정보|
2|콘솔|Microsoft.AspNetCore.Mvc.Razor.Internal|경고|
3|콘솔|Microsoft.AspNetCore.Mvc.Razor.Razor|디버그|
4|콘솔|Microsoft.AspNetCore.Mvc.Razor|오류|
5|콘솔|모든 범주|정보|
6|모든 공급자|모든 범주|디버그
7|모든 공급자|시스템|디버그
9|디버그|Microsoft|추적

만들 때는 `ILogger` 를 사용 하 여 로그를 작성 하는 개체는 `ILoggerFactory` 개체에는 해당로 거에 적용할 공급자 마다 단일 규칙을 선택 합니다. 모든 메시지를 기록한 `ILogger` 개체 선택한 규칙에 따라 필터링 됩니다. 각 공급자 및 범주 쌍에 대해 가능한 가장 구체적인 규칙은 사용 가능한 규칙에서 선택 됩니다.

각 공급자에 대해 다음 알고리즘을 사용 하는 경우는 `ILogger` 지정된 된 범주에 대해 생성 됩니다.

* 공급자 또는 해당 별칭을 일치 하는 모든 규칙을 선택 합니다.  검색 된 항목이 없는 경우 빈 공급자와 함께 모든 규칙을 선택 합니다.
* 앞 단계의 결과에서 가장 오래 된 하는 규칙 선택에 범주 접두사를 일치 됩니다. 발견 되 면 범주를 지정 하지 않은 모든 규칙을 선택 합니다.
* 여러 규칙을 선택 하는 경우는 **마지막** 하나입니다.
* 규칙을 선택 하는 경우 사용 하 여 `MinimumLevel`합니다.
 
예를 들어 앞의 규칙 목록 있고 만들면는 `ILogger` "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine" 범주에 대 한 개체:

* 디버그 공급자에 대 한 규칙 1, 6 및 8 적용 됩니다. 규칙 8 가장 구체적인 이므로 선택한 것입니다.
* 콘솔 공급자에 대 한 규칙 3, 4, 5 및 6이 적용 됩니다. 규칙 3은 가장 구체적인입니다.

사용 하 여 로그를 만들 때는 `ILogger` "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine" 범주에 대 한 로그 `Trace` 수준 하 고 위에 디버그 공급자와의 로그를 `Debug` 수준 및 콘솔 공급자 위에 이동 합니다.

**별칭 공급자**

구성에서 공급자를 지정 하려면 형식 이름을 사용할 수 있지만 각 공급자 정의 더 짧은 *별칭* 하는 보다 쉽게 사용할 수 있습니다. 기본 제공 공급자에 대 한 다음과 같은 별칭을 사용 합니다.

- 콘솔
- 디버그
- 이벤트 로그
- AzureAppServices
- TraceSource
- EventSource

**기본 최소 수준**

지정 된 공급자 및 범주에 대 한 구성 또는 코드에서 규칙이 적용 하는 경우에 적용 되는 최소 수준 설정이 있습니다. 다음 예제에서는 최소 수준을 설정 하는 방법을 보여 줍니다.

[!code-csharp[](logging/sample2/Program.cs?name=snippet_MinLevel&highlight=3)]

기본값은 최소 수준을 명시적으로 설정 하지 않으면, `Information`, 즉 `Trace` 및 `Debug` 로그는 무시 됩니다.

**필터 함수**

필터링 규칙을 적용 하려면 필터 함수에 코드를 작성할 수 있습니다. Filter 함수는 모든 공급자 및 규칙 할당 하 여 구성 또는 코드를 갖지 않는 범주에 대해 호출 됩니다. 함수에 코드 권한이 공급자 유형, 범주 및 로그 수준 메시지를 기록할지 여부를 결정할 수 있습니다. 예:

[!code-csharp[](logging/sample2/Program.cs?name=snippet_FilterFunction&highlight=5-13)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

일부 로깅 공급자 로그 수준 및 범주에 따라 로그 해야 저장소 미디어에 기록 되거나 무시 때 지정할 수 있습니다.

`AddConsole` 및 `AddDebug` 확장 메서드 필터링 조건에 전달할 수 있는 오버 로드를 제공 합니다. 다음 샘플 코드 때문에 아래 로그를 무시 하려면 콘솔 공급자 `Warning` 디버그 공급자 프레임 워크에서 만들어지는 로그를 무시 하는 동안 수준입니다.

[!code-csharp[](logging/sample/Startup.cs?name=snippet_AddConsoleAndDebugWithFilter&highlight=6-7)]

`AddEventLog` 메서드는 사용 하는 오버 로드는 `EventLogSettings` 에 필터링 함수를 포함할 수 있는 인스턴스를 해당 `Filter` 속성입니다. TraceSource 공급자에서는 이러한 오버 로드 중 하나는 로깅 수준 및 기타 매개 변수 기반으로 하기 때문는 `SourceSwitch` 및 `TraceListener` 사용 합니다.

등록 된 모든 공급자에 대 한 필터링 규칙을 설정할 수 있습니다는 `ILoggerFactory` 를 사용 하 여 인스턴스는 `WithFilter` 확장 메서드. 다음 예제에서는 디버그 수준에서 응용 프로그램 로그 하는 동안 경고를 프레임 워크 로그 (범주 "Microsoft" 또는 "시스템"으로 시작 함)을 제한 합니다.

[!code-csharp[](logging/sample/Startup.cs?name=snippet_FactoryFilter&highlight=6-11)]

특정 범주에 대 한 기록에서 모든 로그를 방지 하기 위해 필터링을 사용 하려는 경우 지정할 수 있습니다 `LogLevel.None` 해당 범주에 대 한 최소 로그 수준으로 됩니다. 정수 값 `LogLevel.None` 은 6 이며 보다 높은 `LogLevel.Critical` (5).

`WithFilter` 제공한 확장 메서드는 [Microsoft.Extensions.Logging.Filter](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Filter) NuGet 패키지 합니다. 메서드가 새 `ILoggerFactory` 함께 모든로 거 공급자에 전달 된 로그 메시지를 필터링 하는 인스턴스를 등록 합니다. 다른 모든 변경 되지 않습니다 `ILoggerFactory` 인스턴스를 원래 `ILoggerFactory` 인스턴스.

---

## <a name="log-scopes"></a>로그 범위

내에서 논리적 작업의 집합을 그룹화 수는 *범위* 해당 집합의 일부로 만들어진 각 로그에 동일한 데이터를 연결 하기 위해 합니다.  예를 들어 트랜잭션 ID를 포함 하도록 트랜잭션을 처리의 일부로 만들어진 모든 로그를 할 수 있습니다.

범위는는 `IDisposable` 에서 반환 되는 형식에서 `ILogger.BeginScope<TState>` 메서드와 삭제 될 때까지 유지 되는 기간. 범위를 사용 하 여 배치의 사용자로 거를 호출 하 여 한 `using` 다음과 같이 차단:

[!code-csharp[](logging/sample//Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

다음 코드를 통해 콘솔 공급자에 대 한 범위:

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

*Program.cs*:

[!code-csharp[](logging/sample2/Program.cs?name=snippet_Scopes&highlight=4)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

*Startup.cs*:

[!code-csharp[](logging/sample/Startup.cs?name=snippet_Scopes&highlight=6)]

---

각 로그 메시지에는 범위가 지정 된 정보가 포함 됩니다.

```
info: TodoApi.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApi.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApi.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApi.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a>기본 제공 로깅 공급자

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

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

```csharp
logging.AddConsole()
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

```csharp
loggerFactory.AddConsole()
```

[AddConsole 오버 로드](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.consoleloggerextensions) 에 전달할 수는 최소 로그 수준, 필터 함수 및 범위를 지원 하는지 여부를 나타내는 부울입니다.  에 전달 하도록 또 다른 옵션은 프로그램 `IConfiguration` 범위 지원 및 로깅 수준을 지정할 수 있는 개체입니다. 

프로덕션 환경에서 사용 하기 위해 콘솔 공급자를 고려 하는 경우에 성능에 상당한 영향에 주의 해야 합니다.

Visual Studio에서 새 프로젝트를 만들 때의 `AddConsole` 메서드는 다음과 같습니다.

```csharp
loggerFactory.AddConsole(Configuration.GetSection("Logging"));
```

이 코드에서 참조 하는 `Logging` 의 섹션은 *appSettings.json* 파일:

[!code-json[](logging/sample//appsettings.json)]

에 설명 된 대로 디버그 수준에서 기록 하도록 응용 프로그램을 허용 하는 동안 제한 프레임 워크 로그 경고를 표시 설정을 [로그 필터링](#log-filtering) 섹션. 자세한 내용은 [구성](configuration.md)을 참조하세요.

---

<a id="debug"></a>
### <a name="the-debug-provider"></a>디버그 공급자

[Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) 공급자 패키지 로그 출력을 사용 하 여 씁니다는 [System.Diagnostics.Debug](https://docs.microsoft.com/dotnet/core/api/system.diagnostics.debug#System_Diagnostics_Debug) 클래스 (`Debug.WriteLine` 메서드 호출).

Linux에서이 공급자는 로그를 기록 */var/log/message*합니다.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

```csharp
logging.AddDebug()
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

```csharp
loggerFactory.AddDebug()
```

[오버 로드 AddDebug](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.debugloggerfactoryextensions) let 최소 로그 수준 또는 필터 함수에 전달 합니다.

---

<a id="eventsource"></a>
### <a name="the-eventsource-provider"></a>EventSource 공급자

ASP.NET Core 1.1.0을 대상으로 하는 앱에 대 한 또는 그 이상으로 [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) 공급자 패키지 이벤트 추적을 구현할 수 있습니다. Windows에서 사용 하 여 [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803)합니다. 공급자는 플랫폼 간 않으며 하는 이벤트가 수집 하 고 표시 도구가 아직 Linux 또는 macOS입니다. 

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

```csharp
logging.AddEventSourceLogger()
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

```csharp
loggerFactory.AddEventSourceLogger()
```

---

수집 하 고 로그를 표시 하는 좋은 방법을 사용 하는 것은 [PerfView 유틸리티](https://www.microsoft.com/download/details.aspx?id=28567)합니다. ETW 로그를 보기 위한 다른 도구 있지만 PerfView ASP.NET에서 내보내는 ETW 이벤트를 사용 하기 위한 최상의 환경을 제공 합니다. 

이 공급자에 의해 기록 된 이벤트를 수집 하기 위한 PerfView를 구성 하려면 문자열을 추가 `*Microsoft-Extensions-Logging` 에 **추가 공급자** 목록입니다. (문자열의 시작 부분에 별표를 누락 하지 마십시오.)

![Perfview 추가 공급자](logging/_static/perfview-additional-providers.png)

Nano Server에서 이벤트 캡처 몇 가지 추가 설정이 필요 합니다.

* PowerShell remoting Nano 서버에 연결 합니다.

  ```powershell
  Enter-PSSession [name]
  ```

* ETW 세션을 만듭니다.

  ```powershell
  New-EtwTraceSession -Name "MyAppTrace" -LocalFilePath C:\trace.etl
  ```

* ETW 공급자에 대 한 추가 [CLR](https://docs.microsoft.com/dotnet/framework/performance/clr-etw-providers), ASP.NET Core 및 필요에 따라 다른 사용자입니다. GUID는 ASP.NET Core 공급자 `3ac73b97-af73-50e9-0822-5da4367920d0`합니다. 

  ```powershell
  Add-EtwTraceProvider -Guid "{e13c0d23-ccbc-4e12-931b-d9cc2eee27e4}" -SessionName MyAppTrace
  Add-EtwTraceProvider -Guid "{3ac73b97-af73-50e9-0822-5da4367920d0}" -SessionName MyAppTrace
  ```

* 사이트를 실행 하 고 추적 정보에 대 한 원하는 작업을 수행 합니다.

* 마치면 추적 세션을 중지 합니다.

  ```powershell
  Stop-EtwTraceSession -Name "MyAppTrace"
  ```

그 결과 *C:\trace.etl* 다른 Windows 버전에서와 같이 PerfView와 파일을 분석할 수 있습니다.

<a id="eventlog"></a>
### <a name="the-windows-eventlog-provider"></a>Windows 이벤트 로그 공급자

[Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) 공급자 패키지는 Windows 이벤트 로그에 로그 출력을 보냅니다.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

```csharp
logging.AddEventLog()
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

```csharp
loggerFactory.AddEventLog()
```

[AddEventLog 오버 로드](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.eventloggerfactoryextensions) 전달 let `EventLogSettings` 또는 최소 로그 수준입니다.

---

<a id="tracesource"></a>
### <a name="the-tracesource-provider"></a>TraceSource 공급자

[Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) 공급자 패키지가 사용 하는 [System.Diagnostics.TraceSource](https://docs.microsoft.com/dotnet/api/system.diagnostics.tracesource) 라이브러리 및 공급자입니다.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

```csharp
logging.AddTraceSource(sourceSwitchName);
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

```csharp
loggerFactory.AddTraceSource(sourceSwitchName);
```

---

[오버 로드 AddTraceSource](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.tracesourcefactoryextensions) let 소스 스위치와 추적 수신기에 전달 합니다.

이 공급자를 사용 하려면는 응용 프로그램에서.NET Framework (아닌에서.NET Core)를 실행 합니다. 다양 한 메시지를 라우팅하는 공급자를 사용 하면 [수신기](https://docs.microsoft.com/dotnet/framework/debug-trace-profile/trace-listeners)와 같은 [TextWriterTraceListener](https://docs.microsoft.com/dotnet/api/system.diagnostics.textwritertracelistenerr) 샘플 응용 프로그램에 사용 합니다.

다음 예제에서는 구성는 `TraceSource` 기록 하는 공급자 `Warning` 및 콘솔 창에 더 높은 메시지입니다.

[!code-csharp[](logging/sample/Startup.cs?name=snippet_TraceSource&highlight=9-12)]

<a id="appservice"></a>
### <a name="the-azure-app-service-provider"></a>Azure 앱 서비스 공급자

[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) 공급자 패키지 로그에는 Azure 앱 서비스 앱의 파일 시스템 및 텍스트 파일에 기록 [blob 저장소](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) 는 Azure 저장소 계정에 있습니다. 공급자는 ASP.NET Core 1.1.0을 대상으로 하는 앱에 대해서만 사용할 수 있는 이후인 있습니다. 

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

> [!NOTE]
> ASP.NET Core 2.0 미리 보기에는입니다.  Azure 앱 서비스에 배포 될 때 최신 미리 보기 릴리스를 사용 하 여 만든 앱 실행 되지 않을 수 있습니다. Azure 앱 서비스 2.0을 실행은 ASP.NET 코어 2.0 출시 되 면 앱 및 Azure 앱 서비스 공급자는 여기에 설명 된 대로 작동 합니다.

공급자 패키지 또는 호출 설치할 필요가 없습니다는 `AddAzureWebAppDiagnostics` 확장 메서드.  공급자가 Azure 앱 서비스에 앱을 배포할 때 응용 프로그램에 자동으로 사용할 수 있습니다.

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

```csharp
loggerFactory.AddAzureWebAppDiagnostics();
```

`AddAzureWebAppDiagnostics` 에 전달할 수 있습니다를 오버 로드 [AzureAppServicesDiagnosticsSettings](https://github.com/aspnet/Logging/blob/c7d0b1b88668ff4ef8a86ea7d2ebb5ca7f88d3e0/src/Microsoft.Extensions.Logging.AzureAppServices/AzureAppServicesDiagnosticsSettings.cs)는 로깅 출력 서식 파일, blob 이름 및 파일 크기 제한을 같은 기본 설정을 재정의할 수 있습니다. (*출력 템플릿을* 는 위에 호출할 때 제공 하는 모든 로그에 적용 되는 메시지 형식 문자열은 `ILogger` 메서드.)  

---

응용 프로그램의 설정을 인식 앱 서비스 앱에 배포할 때의 [진단 로그](https://azure.microsoft.com/documentation/articles/web-sites-enable-diagnostic-log/#enablediag) 의 섹션은 **앱 서비스** Azure 포털의 페이지입니다. 이러한 설정을 변경 하면 변경 내용이 즉시 적용 앱을 다시 시작 하거나 코드를 다시 배포 하지 않아도 됩니다. 

![Azure 로깅 설정](logging/_static/azure-logging-settings.png)

로그 파일에 대 한 기본 위치는는 *d:\\홈\\LogFiles\\응용 프로그램* 폴더가 고 기본 파일 이름은 *진단 yyyymmdd.txt*합니다. 기본 파일 크기 제한을 10MB 이하로 및 유지 하는 파일의 기본 최대 수는 2입니다. 기본 blob 이름은 *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*합니다. 기본 동작에 대 한 자세한 내용은 참조 [AzureAppServicesDiagnosticsSettings](https://github.com/aspnet/Logging/blob/c7d0b1b88668ff4ef8a86ea7d2ebb5ca7f88d3e0/src/Microsoft.Extensions.Logging.AzureAppServices/AzureAppServicesDiagnosticsSettings.cs)합니다.

공급자는 프로젝트는 Azure 환경에서 실행 하는 경우에 작동 합니다.  영향을 주지 않습니다 로컬로 실행할 때 &mdash; 로컬 파일 또는 blob에 대 한 로컬 개발 저장소에 기록 하지 않습니다.

## <a name="third-party-logging-providers"></a>제 3 자 로깅 공급자

ASP.NET Core를 사용 하는 몇 가지 타사 로깅 프레임 워크는 다음과 같습니다.

* [elmah.io](https://github.com/elmahio/Elmah.Io.Extensions.Logging) -Elmah.Io 서비스 공급자

* [JSNLog](http://jsnlog.com) -서버 쪽 로그에 JavaScript 예외 및 기타 클라이언트 쪽 이벤트를 기록 합니다.

* [Loggr](https://github.com/imobile3/Loggr.Extensions.Logging) -Loggr 서비스 공급자

* [NLog](https://github.com/NLog/NLog.Extensions.Logging) -NLog 라이브러리에 대 한 공급자

* [Serilog](https://github.com/serilog/serilog-extensions-logging) -Serilog 라이브러리에 대 한 공급자

일부 타사 프레임 워크에서는 작업을 수행할 수 [구조적된 로깅 라고도 하는 의미 체계 로깅](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)합니다.

타사 프레임 워크를 사용 하는 것은 기본 제공 공급자 중 하나를 사용 하 여 비슷합니다: 프로젝트에 NuGet 패키지를 추가 하 고에 확장 메서드를 호출 `ILoggerFactory`합니다. 자세한 내용은 각 프레임 워크의 설명서를 참조 합니다.

다른 로깅 프레임 워크 또는 사용자의 로깅 요구 사항을 지원 하기 위해 사용자 고유의 사용자 지정 공급자도 만들 수 있습니다.
