---
title: "ASP.NET Core 로그인"
author: ardalis
description: "로깅 프레임 워크에서 ASP.NET Core를 소개합니다. 각 기본 제공 로깅 공급자 및 인기 있는 일부 타사 공급자에 대 한 링크에 대 한 섹션이 포함 되어 있습니다."
keywords: "ASP.NET Core, 로깅, 로깅 공급자 Microsoft.Extensions.Logging, ILogger, ILoggerFactory, LogLevel, WithFilter, TraceSource, 이벤트 로그, EventSource를 범위로 지정"
ms.author: tdykstra
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: ac27ac68-d76a-4f8e-b8ab-ea045803e5f2
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/logging
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 30e00e2a442225bbe04be0d343f7048efe484477
ms.sourcegitcommit: 4e84d8bf5f404bb77f3d41665cf7e7374fc39142
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/05/2017
---
# <a name="introduction-to-logging-in-aspnet-core"></a><span data-ttu-id="a2e4f-105">ASP.NET Core 로그인 소개</span><span class="sxs-lookup"><span data-stu-id="a2e4f-105">Introduction to Logging in ASP.NET Core</span></span>

<span data-ttu-id="a2e4f-106">여 [Steve Smith](http://ardalis.com) 및 [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="a2e4f-106">By [Steve Smith](http://ardalis.com) and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="a2e4f-107">ASP.NET Core 다양 한 로깅 공급자와 작동 하는 로깅 API를 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-107">ASP.NET Core supports a logging API that works with a variety of logging providers.</span></span> <span data-ttu-id="a2e4f-108">하나 이상의 대상에 로그를 보낼 기본 제공 공급자 수 있으며, 타사 로깅 프레임 워크에 연결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-108">Built-in providers let you send logs to one or more destinations, and you can plug in a third-party logging framework.</span></span> <span data-ttu-id="a2e4f-109">이 문서에는 코드에는 기본 제공 로깅 API 및 공급자를 사용 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-109">This article shows how to use the built-in logging API and providers in your code.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="a2e4f-110">ASP.NET 2.x 핵심</span><span class="sxs-lookup"><span data-stu-id="a2e4f-110">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

[<span data-ttu-id="a2e4f-111">샘플 코드 보기 또는 다운로드</span><span class="sxs-lookup"><span data-stu-id="a2e4f-111">View or download sample code</span></span>](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/logging/sample2)

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="a2e4f-112">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="a2e4f-112">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

[<span data-ttu-id="a2e4f-113">샘플 코드 보기 또는 다운로드</span><span class="sxs-lookup"><span data-stu-id="a2e4f-113">View or download sample code</span></span>](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/logging/sample)

---

## <a name="how-to-create-logs"></a><span data-ttu-id="a2e4f-114">로그를 만드는 방법</span><span class="sxs-lookup"><span data-stu-id="a2e4f-114">How to create logs</span></span>

<span data-ttu-id="a2e4f-115">로그를 만들려면 가져오기는 `ILogger` 에서 개체는 [종속성 주입](dependency-injection.md) 컨테이너:</span><span class="sxs-lookup"><span data-stu-id="a2e4f-115">To create logs, get an `ILogger` object from the [dependency injection](dependency-injection.md) container:</span></span>

[!code-csharp[](logging/sample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

<span data-ttu-id="a2e4f-116">그런 다음 해당로 거 개체에 대해 로깅 메서드를 호출할:</span><span class="sxs-lookup"><span data-stu-id="a2e4f-116">Then call logging methods on that logger object:</span></span>

[!code-csharp[](logging/sample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="a2e4f-117">사용 하 여 로그를 만드는이 예제는 `TodoController` 으로 클래스는 *범주*합니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-117">This example creates logs with the `TodoController` class as the *category*.</span></span>  <span data-ttu-id="a2e4f-118">범주 설명 [이 문서의 뒷부분에 나오는](#log-category)합니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-118">Categories are explained [later in this article](#log-category).</span></span>

<span data-ttu-id="a2e4f-119">ASP.NET Core에서는 비동기로 거 메서드 로깅 async 사용의 비용 보다 중요 없음을 빠른 이어야 하기 때문에 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-119">ASP.NET Core does not provide async logger methods because logging should be so fast that it isn't worth the cost of using async.</span></span> <span data-ttu-id="a2e4f-120">true가 아니면 하는 상황에 있는 경우에 로그온 하는 방식을 변경 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-120">If you're in a situation where that's not true, consider changing the way you log.</span></span>  <span data-ttu-id="a2e4f-121">데이터 저장소 느린 경우 먼저, 빠른 저장소 로그 메시지가 작성 한 다음 나중에 느린 스토어로 이동 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-121">If your data store is slow, write the log messages to a fast store first, then move them to a slow store later.</span></span> <span data-ttu-id="a2e4f-122">예를 들어 읽고 다른 프로세스에 의해 속도가 느린 저장소에 유지 하는 메시지 큐에 기록 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-122">For example, log to a message queue that is read and persisted to slow storage by another process.</span></span>

## <a name="how-to-add-providers"></a><span data-ttu-id="a2e4f-123">공급자를 추가 하는 방법</span><span class="sxs-lookup"><span data-stu-id="a2e4f-123">How to add providers</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="a2e4f-124">ASP.NET 2.x 핵심</span><span class="sxs-lookup"><span data-stu-id="a2e4f-124">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="a2e4f-125">로깅 공급자를 사용 하 여 만든 메시지를 가져와 `ILogger` 개체 및 표시 하거나 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-125">A logging provider takes the messages that you create with an `ILogger` object and displays or stores them.</span></span> <span data-ttu-id="a2e4f-126">예를 들어 콘솔에서 메시지를 표시 하는 콘솔 공급자 및 Azure 앱 서비스 공급자를 사용 하는 Azure blob 저장소에 저장할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-126">For example, the Console provider displays messages on the console, and the Azure App Service provider can store them in Azure blob storage.</span></span>

<span data-ttu-id="a2e4f-127">공급자를 사용 하려면 공급자의 `Add<ProviderName>` 에 확장 메서드 *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="a2e4f-127">To use a provider, call the provider's `Add<ProviderName>` extension method in *Program.cs*:</span></span>

[!code-csharp[](logging/sample2/Program.cs?name=snippet_ExpandDefault&highlight=16,17)]

<span data-ttu-id="a2e4f-128">기본 프로젝트 템플릿은 위의 코드에서 참조 방식으로 로깅 설정 되지만 `ConfigureLogging` 호출 하면 됩니다는 `CreateDefaultBuilder` 메서드.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-128">The default project template sets up logging the way you see it in the preceding code, but the `ConfigureLogging` call is done by the `CreateDefaultBuilder` method.</span></span> <span data-ttu-id="a2e4f-129">다음은 코드 *Program.cs* 프로젝트 템플릿으로 만들어진:</span><span class="sxs-lookup"><span data-stu-id="a2e4f-129">Here's the code in *Program.cs* that is created by project templates:</span></span>

[!code-csharp[](logging/sample2/Program.cs?name=snippet_TemplateCode&highlight=7)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="a2e4f-130">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="a2e4f-130">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="a2e4f-131">로깅 공급자를 사용 하 여 만든 메시지를 가져와 `ILogger` 개체 및 표시 하거나 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-131">A logging provider takes the messages that you create with an `ILogger` object and displays or stores them.</span></span> <span data-ttu-id="a2e4f-132">예를 들어 콘솔에서 메시지를 표시 하는 콘솔 공급자 및 Azure 앱 서비스 공급자를 사용 하는 Azure blob 저장소에 저장할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-132">For example, the Console provider displays messages on the console, and the Azure App Service provider can store them in Azure blob storage.</span></span>

<span data-ttu-id="a2e4f-133">공급자를 사용 하려면 NuGet 패키지를 설치 하 고 인스턴스에 공급자의 확장 메서드를 호출 `ILoggerFactory`다음 예제에 나온 것 처럼 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-133">To use a provider, install its NuGet package and call the provider's extension method on an instance of `ILoggerFactory`, as shown in the following example.</span></span>

[!code-csharp[](logging/sample//Startup.cs?name=snippet_AddConsoleAndDebug&highlight=3,5-7)]

<span data-ttu-id="a2e4f-134">ASP.NET Core [종속성 주입](dependency-injection.md) DI ()를 제공 된 `ILoggerFactory` 인스턴스.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-134">ASP.NET Core [dependency injection](dependency-injection.md) (DI) provides the `ILoggerFactory` instance.</span></span> <span data-ttu-id="a2e4f-135">`AddConsole` 및 `AddDebug` 에 정의 된 확장 메서드는 [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/) 및 [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug/) 패키지 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-135">The `AddConsole` and `AddDebug` extension methods are defined in the [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/) and [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug/) packages.</span></span> <span data-ttu-id="a2e4f-136">각 확장 메서드 호출의 `ILoggerFactory.AddProvider` 공급자의 인스턴스에 전달 하는 메서드.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-136">Each extension method calls the `ILoggerFactory.AddProvider` method, passing in an instance of the provider.</span></span> 

> [!NOTE]
> <span data-ttu-id="a2e4f-137">이 문서에 대 한 샘플 응용 프로그램 추가에서 로깅 공급자는 `Configure` 의 메서드는 `Startup` 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-137">The sample application for this article adds logging providers in the `Configure` method of the `Startup` class.</span></span> <span data-ttu-id="a2e4f-138">이전에 실행 되는 코드에서 로그 출력을 가져올 하려는 경우 추가에 대 한 로깅 공급자는 `Startup` 클래스 생성자를 대신 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-138">If you want to get log output from code that executes earlier, add logging providers in the `Startup` class constructor instead.</span></span> 

---

<span data-ttu-id="a2e4f-139">각각에 대 한 정보를 찾을 수 [기본 제공 로깅 공급자](#built-in-logging-providers) 에 연결 하 고 [제 3 자 로깅 공급자](#third-party-logging-providers) 는 문서의 뒷부분에 나오는 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-139">You'll find information about each [built-in logging provider](#built-in-logging-providers) and links to [third-party logging providers](#third-party-logging-providers) later in the article.</span></span>

## <a name="sample-logging-output"></a><span data-ttu-id="a2e4f-140">예제 로깅 출력</span><span class="sxs-lookup"><span data-stu-id="a2e4f-140">Sample logging output</span></span>

<span data-ttu-id="a2e4f-141">이전 섹션에 나와 있는 샘플 코드를 있는 명령줄에서 실행할 때 콘솔에 있는 로그를 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-141">With the sample code shown in the preceding section, you'll see logs in the console when you run from the command line.</span></span> <span data-ttu-id="a2e4f-142">콘솔 출력의 예는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-142">Here's an example of console output:</span></span>

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
 
<span data-ttu-id="a2e4f-143">이러한 로그를 이동 하 여 생성 된 `http://localhost:5000/api/todo/0`, 둘 다의 실행을 트리거하는 `ILogger` 이전 섹션에서 볼 수 있는 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-143">These logs were created by going to `http://localhost:5000/api/todo/0`, which triggers execution of both `ILogger` calls shown in the preceding section.</span></span>

<span data-ttu-id="a2e4f-144">다음은 Visual Studio에서 예제 응용 프로그램을 실행 하는 경우 디버그 창에 표시 된 대로 동일한 로그의 예가입니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-144">Here's an example of the same logs as they appear in the Debug window when you run the sample application in Visual Studio:</span></span>

```console
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request starting HTTP/1.1 GET http://localhost:53104/api/todo/0  
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
TodoApi.Controllers.TodoController:Information: Getting item 0
TodoApi.Controllers.TodoController:Warning: GetById(0) NOT FOUND
Microsoft.AspNetCore.Mvc.StatusCodeResult:Information: Executing HttpStatusCodeResult, setting HTTP status code 404
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 152.5657ms
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request finished in 316.3195ms 404 
```

<span data-ttu-id="a2e4f-145">생성 된 로그는 `ILogger` 이전 섹션에서 볼 수 있는 호출 "TodoApi.Controllers.TodoController"로 시작 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-145">The logs that were created by the `ILogger` calls shown in the preceding section begin with "TodoApi.Controllers.TodoController".</span></span> <span data-ttu-id="a2e4f-146">ASP.NET Core에서 "Microsoft" 범주로 시작 하는 로그는 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-146">The logs that begin with "Microsoft" categories are from ASP.NET Core.</span></span> <span data-ttu-id="a2e4f-147">ASP.NET Core 자체 및 응용 프로그램 코드와 동일한 로깅 API와 동일한 로깅 공급자 사용 하는 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-147">ASP.NET Core itself and your application code are using the same logging API and the same logging providers.</span></span>

<span data-ttu-id="a2e4f-148">이 문서의 나머지 부분에서는 일부 세부 정보 및 로깅에 대 한 옵션을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-148">The remainder of this article explains some details and options for logging.</span></span>

## <a name="nuget-packages"></a><span data-ttu-id="a2e4f-149">NuGet 패키지</span><span class="sxs-lookup"><span data-stu-id="a2e4f-149">NuGet packages</span></span>

<span data-ttu-id="a2e4f-150">`ILogger` 및 `ILoggerFactory` 인터페이스에는 [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), 있으며에 대 한 기본 구현을에서 [Microsoft.Extensions.Logging](https://www.nuget.org/packages/Microsoft.Extensions.Logging/)합니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-150">The `ILogger` and `ILoggerFactory` interfaces are in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), and default implementations for them are in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/Microsoft.Extensions.Logging/).</span></span>

## <a name="log-category"></a><span data-ttu-id="a2e4f-151">로그 범주</span><span class="sxs-lookup"><span data-stu-id="a2e4f-151">Log category</span></span>

<span data-ttu-id="a2e4f-152">A *범주* 를 만들면 각 로그에 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-152">A *category* is included with each log that you create.</span></span>  <span data-ttu-id="a2e4f-153">만들 때 범주를 지정 된 `ILogger` 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-153">You specify the category when you create an `ILogger` object.</span></span> <span data-ttu-id="a2e4f-154">범주에는 모든 문자열을 사용할 수 있지만 로그는 작성 된 클래스의 정규화 된 이름을 사용 하는 규칙은.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-154">The category may be any string, but a convention is to use the fully qualified name of the class from which the logs are written.</span></span>  <span data-ttu-id="a2e4f-155">예: "TodoApi.Controllers.TodoController"입니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-155">For example: "TodoApi.Controllers.TodoController".</span></span>

<span data-ttu-id="a2e4f-156">범주를 지정 하는 문자열로 하거나 범주 형식에서 파생 하는 확장 메서드를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-156">You can specify the category as a string or use an extension method that derives the category from the type.</span></span> <span data-ttu-id="a2e4f-157">범주 문자열을 지정 하려면 호출 `CreateLogger` 에 `ILoggerFactory` 인스턴스를 다음과 같이 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-157">To specify the category as a string, call `CreateLogger` on an `ILoggerFactory` instance, as shown below.</span></span>

[!code-csharp[](logging/sample//Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

<span data-ttu-id="a2e4f-158">대부분의 경우는 것이를 사용 하기 쉽게 `ILogger<T>`다음 예제와 같이, 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-158">Most of the time, it will be easier to use  `ILogger<T>`, as in the following example.</span></span>

[!code-csharp[](logging/sample//Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

<span data-ttu-id="a2e4f-159">이 호출에 해당 하는 `CreateLogger` 의 정규화 된 형식 이름으로 `T`합니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-159">This is equivalent to calling `CreateLogger` with the fully qualified type name of `T`.</span></span>

## <a name="log-level"></a><span data-ttu-id="a2e4f-160">로그 수준</span><span class="sxs-lookup"><span data-stu-id="a2e4f-160">Log level</span></span>

<span data-ttu-id="a2e4f-161">때마다 지정할에 로그를 쓸 해당 [LogLevel](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.loglevel)합니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-161">Each time you write a log, you specify its [LogLevel](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.loglevel).</span></span> <span data-ttu-id="a2e4f-162">로그 수준 심각도 또는 중요도 수준을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-162">The log level indicates the degree of severity or importance.</span></span>  <span data-ttu-id="a2e4f-163">예를 들어 작성할 수 있습니다는 `Information` 메서드가 정상적으로 종료 될 때 로그는 `Warning` 로그 메서드가 반환 하는 404가 반환 코드를 반환 하는 경우 및는 `Error` 예기치 않은 예외를 catch 하는 때를 기록 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-163">For example, you might write an `Information` log when a method ends normally, a `Warning` log when a method returns a 404 return code, and an `Error` log when you catch an unexpected exception.</span></span>

<span data-ttu-id="a2e4f-164">다음 코드 예제에서는 메서드의 이름 (예를 들어 `LogWarning`) 로그 수준을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-164">In the following code example, the names of the methods (for example, `LogWarning`) specify the log level.</span></span>  <span data-ttu-id="a2e4f-165">첫 번째 매개 변수는 [이벤트 ID를 로그](#log-event-id) (이 문서의 뒷부분에 설명).</span><span class="sxs-lookup"><span data-stu-id="a2e4f-165">The first parameter is the [Log event ID](#log-event-id) (explained later in this article).</span></span>  <span data-ttu-id="a2e4f-166">나머지 매개 변수는 로그 메시지 문자열을 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-166">The remaining parameters construct a log message string.</span></span>

[!code-csharp[](logging/sample//Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="a2e4f-167">수준은 메서드 이름에 포함 된 로그 방법은 [ILogger 확장 메서드가](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.loggerextensions)합니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-167">Log methods that include the level in the method name are [extension methods for ILogger](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.loggerextensions).</span></span> <span data-ttu-id="a2e4f-168">이러한 메서드 호출 내부적으로 `Log` 를 받는 메서드에 `LogLevel` 매개 변수입니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-168">Behind the scenes, these methods call a `Log` method that takes a `LogLevel` parameter.</span></span> <span data-ttu-id="a2e4f-169">호출할 수 있습니다는 `Log` 구문을 하지만 이러한 확장 메서드를 실행 하는 것이 아니라 메서드를 직접은 상대적으로 복잡 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-169">You can call the `Log` method directly rather than one of these extension methods, but the syntax is relatively complicated.</span></span> <span data-ttu-id="a2e4f-170">자세한 내용은 참조는 [ILogger 인터페이스](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.ilogger) 및 [로 거 확장 소스 코드](https://github.com/aspnet/Logging/blob/master/src/Microsoft.Extensions.Logging.Abstractions/LoggerExtensions.cs)합니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-170">For more information, see the [ILogger interface](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.ilogger) and the [logger extensions source code](https://github.com/aspnet/Logging/blob/master/src/Microsoft.Extensions.Logging.Abstractions/LoggerExtensions.cs).</span></span>

<span data-ttu-id="a2e4f-171">다음을 정의 하는 ASP.NET Core [로그 수준](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.loglevel), 최소에 가장 높은 심각도에서 여기 순으로 정렬 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-171">ASP.NET Core defines the following [log levels](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.loglevel), ordered here from least to highest severity.</span></span>

* <span data-ttu-id="a2e4f-172">추적 = 0</span><span class="sxs-lookup"><span data-stu-id="a2e4f-172">Trace = 0</span></span>

  <span data-ttu-id="a2e4f-173">개발자 에게만 중요 정보에 대 한 문제를 디버깅 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-173">For information that is valuable only to a developer debugging an issue.</span></span> <span data-ttu-id="a2e4f-174">이러한 메시지는 중요 한 응용 프로그램 데이터 포함 하 고 있으므로 사용 하지 않아야 프로덕션 환경에서.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-174">These messages may contain sensitive application data and so should not be enabled in a production environment.</span></span> <span data-ttu-id="a2e4f-175">*기본적으로 사용 하지 않습니다.*</span><span class="sxs-lookup"><span data-stu-id="a2e4f-175">*Disabled by default.*</span></span> <span data-ttu-id="a2e4f-176">예: `Credentials: {"User":"someuser", "Password":"P@ssword"}`</span><span class="sxs-lookup"><span data-stu-id="a2e4f-176">Example: `Credentials: {"User":"someuser", "Password":"P@ssword"}`</span></span>

* <span data-ttu-id="a2e4f-177">디버그 = 1</span><span class="sxs-lookup"><span data-stu-id="a2e4f-177">Debug = 1</span></span>

  <span data-ttu-id="a2e4f-178">에 대 한 단기 유용성 개발 및 디버깅 하는 동안에 정보입니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-178">For information that has short-term usefulness during development and debugging.</span></span> <span data-ttu-id="a2e4f-179">예: `Entering method Configure with flag set to true.` 일반적으로 설정 하지는 `Debug` 수준은 로그 양이 많기 때문에 해결 된 경우가 아니면 프로덕션 환경에 기록 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-179">Example: `Entering method Configure with flag set to true.` You typically would not enable `Debug` level logs in production unless you are troubleshooting, due to the high volume of logs.</span></span>

* <span data-ttu-id="a2e4f-180">정보 2 =</span><span class="sxs-lookup"><span data-stu-id="a2e4f-180">Information = 2</span></span>

  <span data-ttu-id="a2e4f-181">응용 프로그램의 일반적인 흐름을 추적 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-181">For tracking the general flow of the application.</span></span> <span data-ttu-id="a2e4f-182">이러한 로그는 일반적으로 일부 장기 값을 가집니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-182">These logs typically have some long-term value.</span></span> <span data-ttu-id="a2e4f-183">예: `Request received for path /api/todo`</span><span class="sxs-lookup"><span data-stu-id="a2e4f-183">Example: `Request received for path /api/todo`</span></span>

* <span data-ttu-id="a2e4f-184">경고 = 3</span><span class="sxs-lookup"><span data-stu-id="a2e4f-184">Warning = 3</span></span>

  <span data-ttu-id="a2e4f-185">응용 프로그램 흐름에 비정상적인 또는 예기치 않은 이벤트에 대 한</span><span class="sxs-lookup"><span data-stu-id="a2e4f-185">For abnormal or unexpected events in the application flow.</span></span> <span data-ttu-id="a2e4f-186">여기에 오류 또는 기타 조건을 중지 하려면 응용 프로그램을 일으키지 않습니다 이지만 조사 해야 할 수 있는 포함 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-186">These may include errors or other conditions that do not cause the application to stop, but which may need to be investigated.</span></span> <span data-ttu-id="a2e4f-187">처리 된 예외는 사용할 수 있는 일반적인 위치는 `Warning` 로그 수준입니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-187">Handled exceptions are a common place to use the `Warning` log level.</span></span> <span data-ttu-id="a2e4f-188">예: `FileNotFoundException for file quotes.txt.`</span><span class="sxs-lookup"><span data-stu-id="a2e4f-188">Example: `FileNotFoundException for file quotes.txt.`</span></span>

* <span data-ttu-id="a2e4f-189">오류 = 4</span><span class="sxs-lookup"><span data-stu-id="a2e4f-189">Error = 4</span></span>

  <span data-ttu-id="a2e4f-190">오류 및 예외를 처리할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-190">For errors and exceptions that cannot be handled.</span></span> <span data-ttu-id="a2e4f-191">이 메시지는 현재 작업 또는 작업 (예: 현재 HTTP 요청) 오류가 발생할 경우 전체 응용 프로그램 오류가 발생 하지 않습니다 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-191">These messages indicate a failure in the current activity or operation (such as the current HTTP request), not an application-wide failure.</span></span> <span data-ttu-id="a2e4f-192">예제 로그 메시지:`Cannot insert record due to duplicate key violation.`</span><span class="sxs-lookup"><span data-stu-id="a2e4f-192">Example log message: `Cannot insert record due to duplicate key violation.`</span></span>

* <span data-ttu-id="a2e4f-193">중요 한 = 5</span><span class="sxs-lookup"><span data-stu-id="a2e4f-193">Critical = 5</span></span>

  <span data-ttu-id="a2e4f-194">오류에 대 한 즉각적인 주목이 필요한입니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-194">For failures that require immediate attention.</span></span> <span data-ttu-id="a2e4f-195">예: 데이터 손실 시나리오를 디스크 공간이 부족 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-195">Examples: data loss scenarios, out of disk space.</span></span>

<span data-ttu-id="a2e4f-196">로그 수준의을 특정 저장 매체에 얼마나 많은 로그 출력은 기록 제어 하거나 창을 표시에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-196">You can use the log level to control how much log output is written to a particular storage medium or display window.</span></span> <span data-ttu-id="a2e4f-197">예를 들어 프로덕션 환경에서 수 원하는의 모든 로그 `Information` 수준 하 고 볼륨 데이터 저장소와의 모든 로그를 이동할 수를 줄이려면 `Warning` 수준 이상 값 데이터 저장소로 이동 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-197">For example, in production you might want all logs of `Information` level and lower to go to a volume data store, and all logs of `Warning` level and higher to go to a value data store.</span></span> <span data-ttu-id="a2e4f-198">개발 하는 동안 일반적으로 보내는 경우가의 로그 `Warning` 또는 콘솔에 더 높은 심각도입니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-198">During development, you might normally send logs of `Warning` or higher severity to the console.</span></span> <span data-ttu-id="a2e4f-199">문제를 해결 해야 할 때 추가 하 여 `Debug` 수준입니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-199">Then when you need to troubleshoot, you can add `Debug` level.</span></span> <span data-ttu-id="a2e4f-200">[로그 필터링](#log-filtering) 이 문서의 뒷부분에 나오는 섹션에서는 공급자가 처리 하는 로그 수준을 제어 하는 방법에 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-200">The [Log filtering](#log-filtering) section later in this article explains how to control which log levels a provider handles.</span></span>

<span data-ttu-id="a2e4f-201">ASP.NET Core framework 씁니다 `Debug` 프레임 워크 이벤트에 대 한 로그 수준입니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-201">The ASP.NET Core framework writes `Debug` level logs for framework events.</span></span> <span data-ttu-id="a2e4f-202">로그 예제 앞부분이 문서에서 제외 아래 로그 `Information` 수준 하지 않게 `Debug` 로그 수준으로 표시 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-202">The log examples earlier in this article excluded logs below `Information` level, so no `Debug` level logs were shown.</span></span> <span data-ttu-id="a2e4f-203">한 예로 콘솔 로그를 표시 하도록 구성 하는 샘플 응용 프로그램을 실행 하는 경우 `Debug` 및 콘솔 공급자에 대 한 높은 로그입니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-203">Here's an example of console logs if you run the sample application configured to show `Debug` and higher logs for the console provider.</span></span>

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

## <a name="log-event-id"></a><span data-ttu-id="a2e4f-204">로그 이벤트 ID</span><span class="sxs-lookup"><span data-stu-id="a2e4f-204">Log event ID</span></span>

<span data-ttu-id="a2e4f-205">각 시간에 로그를 쓸 지정할 수 있습니다는 *이벤트 ID*합니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-205">Each time you write a log, you can specify an *event ID*.</span></span> <span data-ttu-id="a2e4f-206">샘플 앱은이 작업을 로컬로 정의 사용 하 여 수행 `LoggingEvents` 클래스:</span><span class="sxs-lookup"><span data-stu-id="a2e4f-206">The sample app does this by using a locally-defined `LoggingEvents` class:</span></span>

[!code-csharp[](logging/sample//Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](logging/sample//Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

<span data-ttu-id="a2e4f-207">이벤트 ID는 기록 된 이벤트 집합이 서로 상호 연결 하는 데 사용할 수 있는 정수 값입니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-207">An event ID is an integer value that you can use to associate a set of logged events with one another.</span></span> <span data-ttu-id="a2e4f-208">예를 들어, 장바구니에 항목을 추가 대 한 로그에 이벤트 ID 1000 수 및 구매를 완료에 대 한 로그에 이벤트 ID 1001 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-208">For instance, a log for adding an item to a shopping cart could be event ID 1000 and a log for completing a purchase could be event ID 1001.</span></span>

<span data-ttu-id="a2e4f-209">로깅 출력의 이벤트 ID 필드에 저장 되었거나 공급자에 따라 텍스트 메시지에 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-209">In logging output, the event ID may be stored in a field or included in the text message, depending on the provider.</span></span>  <span data-ttu-id="a2e4f-210">디버그 공급자에서 이벤트 Id를 표시 하지 않지만 콘솔 공급자 찍고 대괄호로 항목 뒤:</span><span class="sxs-lookup"><span data-stu-id="a2e4f-210">The Debug provider doesn't show event IDs, but the console provider shows them in brackets after the category:</span></span>

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-format-string"></a><span data-ttu-id="a2e4f-211">로그 메시지 형식 문자열</span><span class="sxs-lookup"><span data-stu-id="a2e4f-211">Log message format string</span></span>

<span data-ttu-id="a2e4f-212">로그에 쓸 때마다 텍스트 메시지를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-212">Each time you write a log, you provide a text message.</span></span> <span data-ttu-id="a2e4f-213">메시지 문자열 인수에 값을 배치 하는 다음 예제 에서처럼 명명 된 자리 표시자를 포함할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-213">The message string can contain named placeholders into which argument values are placed, as in the following example:</span></span>

[!code-csharp[](logging/sample//Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="a2e4f-214">자리 표시자, 해당 이름이 아니라 순서 어떤 매개 변수는 사용을 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-214">The order of placeholders, not their names, determines which parameters are used for them.</span></span> <span data-ttu-id="a2e4f-215">예를 들어 다음과 같은 코드를 가정해 봅니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-215">For example, if you have the following code:</span></span>

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

<span data-ttu-id="a2e4f-216">이 결과 로그 메시지는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-216">The resulting log message would look like this:</span></span>

```
Parameter values: parm1, parm2
```

<span data-ttu-id="a2e4f-217">로깅 공급자가 구현 하는 수 있도록이 방법으로 서식을 로깅 프레임 워크는 메시지 [구조적된 로깅 라고도 하는 의미 체계 로깅](http://programmers.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)합니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-217">The logging framework does message formatting in this way to make it possible for logging providers to implement [semantic logging, also known as structured logging](http://programmers.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span> <span data-ttu-id="a2e4f-218">인수 자체 뿐 아니라 서식이 지정 된 메시지 문자열, 로깅 시스템으로 전달 되기 때문에 로깅 공급자 메시지 문자열 외에도 필드로 매개 변수 값을 저장할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-218">Because the arguments themselves are passed to the logging system, not just the formatted message string, logging providers can store the parameter values as fields in addition to the message string.</span></span> <span data-ttu-id="a2e4f-219">예를 들어 옮기는 경우 Azure 테이블 저장소에 로그를 출력 하 고로 거 메서드 호출은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-219">For example, if you are directing your log output to Azure Table Storage, and your logger method call looks like this:</span></span>

```csharp
_logger.LogInformation("Getting item {ID} at {RequestTime}", id, DateTime.Now);
```

<span data-ttu-id="a2e4f-220">각 Azure 테이블 엔터티를 가지기 `ID` 및 `RequestTime` 속성으로, 로그 데이터에 대 한 쿼리를 단순화 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-220">Each Azure Table entity could have `ID` and `RequestTime` properties, which would simplify queries on log data.</span></span> <span data-ttu-id="a2e4f-221">특정 내에서 모든 로그를 찾을 수 `RequestTime` 문자 메시지의 시간 초과 구문 분석할 필요 없이 범위입니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-221">You could find all logs within a particular `RequestTime` range, without having to parse the time out of the text message.</span></span>

## <a name="logging-exceptions"></a><span data-ttu-id="a2e4f-222">예외 기록</span><span class="sxs-lookup"><span data-stu-id="a2e4f-222">Logging exceptions</span></span>

<span data-ttu-id="a2e4f-223">로 거 메서드 다음 예제와 같이 예외를 전달할 수 있는 오버 로드를 갖고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-223">The logger methods have overloads that let you pass in an exception, as in the following example:</span></span>

[!code-csharp[](logging/sample//Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

<span data-ttu-id="a2e4f-224">다른 공급자는 다양 한 방식에서 예외 정보를 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-224">Different providers handle the exception information in different ways.</span></span> <span data-ttu-id="a2e4f-225">위의 코드에서 디버그 공급자 출력의 예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-225">Here's an example of Debug provider output from the code shown above.</span></span>

```
TodoApi.Controllers.TodoController:Warning: GetById(036dd898-fb01-47e8-9a65-f92eb73cf924) NOT FOUND

System.Exception: Item not found exception.
 at TodoApi.Controllers.TodoController.GetById(String id) in C:\logging\sample\src\TodoApi\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a><span data-ttu-id="a2e4f-226">로그 필터링</span><span class="sxs-lookup"><span data-stu-id="a2e4f-226">Log filtering</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="a2e4f-227">ASP.NET 2.x 핵심</span><span class="sxs-lookup"><span data-stu-id="a2e4f-227">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="a2e4f-228">특정 공급자 및 범주 또는 모든 공급자 또는 모든 범주에 대 한 최소 로그 수준을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-228">You can specify a minimum log level for a specific provider and category or for all providers or all categories.</span></span>  <span data-ttu-id="a2e4f-229">최소 수준 미만인 모든 로그 있으므로 표시 가져오거나 저장 하지 않는 것 해당 공급자에 전달 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-229">Any logs below the minimum level aren't passed to that provider, so they don't get displayed or stored.</span></span> 

<span data-ttu-id="a2e4f-230">모든 로그를 표시 하려는 경우 지정할 수 있습니다 `LogLevel.None` 최소 로그 수준으로 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-230">If you want to suppress all logs, you can specify `LogLevel.None` as the minimum log level.</span></span> <span data-ttu-id="a2e4f-231">정수 값 `LogLevel.None` 은 6 이며 보다 높은 `LogLevel.Critical` (5).</span><span class="sxs-lookup"><span data-stu-id="a2e4f-231">The integer value of `LogLevel.None` is 6, which is higher than `LogLevel.Critical` (5).</span></span>

<span data-ttu-id="a2e4f-232">**구성에서 필터 규칙 만들기**</span><span class="sxs-lookup"><span data-stu-id="a2e4f-232">**Create filter rules in configuration**</span></span>

<span data-ttu-id="a2e4f-233">프로젝트 템플릿 만들기 호출 하는 코드 `CreateDefaultBuilder` 콘솔 및 디버그 공급자에 대 한 로깅을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-233">The project templates create code that calls `CreateDefaultBuilder` to set up logging for the Console and Debug providers.</span></span> <span data-ttu-id="a2e4f-234">`CreateDefaultBuilder` 메서드 설정 로깅을 구성에 대 한 조회 하는 `Logging` 섹션에서 다음과 같은 코드를 사용 하 여:</span><span class="sxs-lookup"><span data-stu-id="a2e4f-234">The `CreateDefaultBuilder` method also sets up logging to look for configuration in a `Logging` section, using code like the following:</span></span>

[!code-csharp[](logging/sample2/Program.cs?name=snippet_ExpandDefault&highlight=15)]

<span data-ttu-id="a2e4f-235">구성 데이터 공급자 및 다음 예제와 같이 범주에서 최소 로그 수준을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-235">The configuration data specifies minimum log levels by provider and category, as in the following example:</span></span>

[!code-json[](logging/sample2/appsettings.json)]

<span data-ttu-id="a2e4f-236">이 JSON 디버그 공급자에 대해 하나씩, 모든 공급자에 적용 되는 개와 콘솔 공급자에 대 한 4 6 개의 필터 규칙을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-236">This JSON creates six filter rules, one for the Debug provider, four for the Console provider, and one that applies to all providers.</span></span> <span data-ttu-id="a2e4f-237">각 공급자에 대 한 이러한 규칙 중 하나만 어떻게 선택 표시 때는 `ILogger` 개체가 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-237">You'll see later how just one of these rules is chosen for each provider when an `ILogger` object is created.</span></span>

<span data-ttu-id="a2e4f-238">**코드에서 필터 규칙**</span><span class="sxs-lookup"><span data-stu-id="a2e4f-238">**Filter rules in code**</span></span>

<span data-ttu-id="a2e4f-239">다음 예제와 같이 코드에서 필터 규칙을 등록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-239">You can register filter rules in code, as shown in the following example:</span></span>

[!code-csharp[](logging/sample2/Program.cs?name=snippet_FilterInCode&highlight=4-5)]

<span data-ttu-id="a2e4f-240">두 번째 `AddFilter` 형식 이름을 사용 하 여 디버그 공급자를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-240">The second `AddFilter` specifies the Debug provider by using its type name.</span></span> <span data-ttu-id="a2e4f-241">첫 번째 `AddFilter` 공급자 형식을 지정 하지 않습니다 때문에 모든 공급자에 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-241">The first `AddFilter` applies to all providers because it doesn't specify a provider type.</span></span>

<span data-ttu-id="a2e4f-242">**필터링 규칙 방식 적용**</span><span class="sxs-lookup"><span data-stu-id="a2e4f-242">**How filtering rules are applied**</span></span>

<span data-ttu-id="a2e4f-243">구성 데이터 및 `AddFilter` 앞의 예제에 표시 된 코드는 다음 표에 표시 된 규칙을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-243">The configuration data and the `AddFilter` code shown in the preceding examples create the rules shown in the following table.</span></span> <span data-ttu-id="a2e4f-244">처음 여섯 개 구성 예제에서 제공 하 고 마지막 두 코드 예제에서 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-244">The first six come from the configuration example and the last two come from the code example.</span></span>

<span data-ttu-id="a2e4f-245">수</span><span class="sxs-lookup"><span data-stu-id="a2e4f-245">Number</span></span>|<span data-ttu-id="a2e4f-246">공급자</span><span class="sxs-lookup"><span data-stu-id="a2e4f-246">Provider</span></span>|<span data-ttu-id="a2e4f-247">로 시작 하는 범주</span><span class="sxs-lookup"><span data-stu-id="a2e4f-247">Categories that begin with</span></span>|<span data-ttu-id="a2e4f-248">최소 로그 수준</span><span class="sxs-lookup"><span data-stu-id="a2e4f-248">Minimum log level</span></span>|
------|--------|--------------------------|-----------------|
<span data-ttu-id="a2e4f-249">1</span><span class="sxs-lookup"><span data-stu-id="a2e4f-249">1</span></span>|<span data-ttu-id="a2e4f-250">디버그</span><span class="sxs-lookup"><span data-stu-id="a2e4f-250">Debug</span></span>|<span data-ttu-id="a2e4f-251">모든 범주</span><span class="sxs-lookup"><span data-stu-id="a2e4f-251">All categories</span></span>|<span data-ttu-id="a2e4f-252">정보</span><span class="sxs-lookup"><span data-stu-id="a2e4f-252">Information</span></span>|
<span data-ttu-id="a2e4f-253">2</span><span class="sxs-lookup"><span data-stu-id="a2e4f-253">2</span></span>|<span data-ttu-id="a2e4f-254">콘솔</span><span class="sxs-lookup"><span data-stu-id="a2e4f-254">Console</span></span>|<span data-ttu-id="a2e4f-255">Microsoft.AspNetCore.Mvc.Razor.Internal</span><span class="sxs-lookup"><span data-stu-id="a2e4f-255">Microsoft.AspNetCore.Mvc.Razor.Internal</span></span>|<span data-ttu-id="a2e4f-256">경고</span><span class="sxs-lookup"><span data-stu-id="a2e4f-256">Warning</span></span>|
<span data-ttu-id="a2e4f-257">3</span><span class="sxs-lookup"><span data-stu-id="a2e4f-257">3</span></span>|<span data-ttu-id="a2e4f-258">콘솔</span><span class="sxs-lookup"><span data-stu-id="a2e4f-258">Console</span></span>|<span data-ttu-id="a2e4f-259">Microsoft.AspNetCore.Mvc.Razor.Razor</span><span class="sxs-lookup"><span data-stu-id="a2e4f-259">Microsoft.AspNetCore.Mvc.Razor.Razor</span></span>|<span data-ttu-id="a2e4f-260">디버그</span><span class="sxs-lookup"><span data-stu-id="a2e4f-260">Debug</span></span>|
<span data-ttu-id="a2e4f-261">4</span><span class="sxs-lookup"><span data-stu-id="a2e4f-261">4</span></span>|<span data-ttu-id="a2e4f-262">콘솔</span><span class="sxs-lookup"><span data-stu-id="a2e4f-262">Console</span></span>|<span data-ttu-id="a2e4f-263">Microsoft.AspNetCore.Mvc.Razor</span><span class="sxs-lookup"><span data-stu-id="a2e4f-263">Microsoft.AspNetCore.Mvc.Razor</span></span>|<span data-ttu-id="a2e4f-264">오류</span><span class="sxs-lookup"><span data-stu-id="a2e4f-264">Error</span></span>|
<span data-ttu-id="a2e4f-265">5</span><span class="sxs-lookup"><span data-stu-id="a2e4f-265">5</span></span>|<span data-ttu-id="a2e4f-266">콘솔</span><span class="sxs-lookup"><span data-stu-id="a2e4f-266">Console</span></span>|<span data-ttu-id="a2e4f-267">모든 범주</span><span class="sxs-lookup"><span data-stu-id="a2e4f-267">All categories</span></span>|<span data-ttu-id="a2e4f-268">정보</span><span class="sxs-lookup"><span data-stu-id="a2e4f-268">Information</span></span>|
<span data-ttu-id="a2e4f-269">6</span><span class="sxs-lookup"><span data-stu-id="a2e4f-269">6</span></span>|<span data-ttu-id="a2e4f-270">모든 공급자</span><span class="sxs-lookup"><span data-stu-id="a2e4f-270">All providers</span></span>|<span data-ttu-id="a2e4f-271">모든 범주</span><span class="sxs-lookup"><span data-stu-id="a2e4f-271">All categories</span></span>|<span data-ttu-id="a2e4f-272">경고</span><span class="sxs-lookup"><span data-stu-id="a2e4f-272">Warning</span></span>
<span data-ttu-id="a2e4f-273">7</span><span class="sxs-lookup"><span data-stu-id="a2e4f-273">7</span></span>|<span data-ttu-id="a2e4f-274">모든 공급자</span><span class="sxs-lookup"><span data-stu-id="a2e4f-274">All providers</span></span>|<span data-ttu-id="a2e4f-275">시스템</span><span class="sxs-lookup"><span data-stu-id="a2e4f-275">System</span></span>|<span data-ttu-id="a2e4f-276">디버그</span><span class="sxs-lookup"><span data-stu-id="a2e4f-276">Debug</span></span>
<span data-ttu-id="a2e4f-277">9</span><span class="sxs-lookup"><span data-stu-id="a2e4f-277">8</span></span>|<span data-ttu-id="a2e4f-278">디버그</span><span class="sxs-lookup"><span data-stu-id="a2e4f-278">Debug</span></span>|<span data-ttu-id="a2e4f-279">Microsoft</span><span class="sxs-lookup"><span data-stu-id="a2e4f-279">Microsoft</span></span>|<span data-ttu-id="a2e4f-280">추적</span><span class="sxs-lookup"><span data-stu-id="a2e4f-280">Trace</span></span>

<span data-ttu-id="a2e4f-281">만들 때는 `ILogger` 를 사용 하 여 로그를 작성 하는 개체는 `ILoggerFactory` 개체에는 해당로 거에 적용할 공급자 마다 단일 규칙을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-281">When you create an `ILogger` object to write logs with, the `ILoggerFactory` object selects a single rule per provider to apply to that logger.</span></span> <span data-ttu-id="a2e4f-282">모든 메시지를 기록한 `ILogger` 개체 선택한 규칙에 따라 필터링 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-282">All messages written by that `ILogger` object are filtered based on the selected rules.</span></span> <span data-ttu-id="a2e4f-283">각 공급자 및 범주 쌍에 대해 가능한 가장 구체적인 규칙은 사용 가능한 규칙에서 선택 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-283">The most specific rule possible for each provider and category pair is selected from the available rules.</span></span>

<span data-ttu-id="a2e4f-284">각 공급자에 대해 다음 알고리즘을 사용 하는 경우는 `ILogger` 지정된 된 범주에 대해 생성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-284">The following algorithm is used for each provider when an `ILogger` is created for a given category:</span></span>

* <span data-ttu-id="a2e4f-285">공급자 또는 해당 별칭을 일치 하는 모든 규칙을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-285">Select all rules that match the provider or its alias.</span></span>  <span data-ttu-id="a2e4f-286">검색 된 항목이 없는 경우 빈 공급자와 함께 모든 규칙을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-286">If none are found, select all rules with an empty provider.</span></span>
* <span data-ttu-id="a2e4f-287">앞 단계의 결과에서 가장 오래 된 하는 규칙 선택에 범주 접두사를 일치 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-287">From the result of the preceding step, select rules with longest matching category prefix.</span></span> <span data-ttu-id="a2e4f-288">발견 되 면 범주를 지정 하지 않은 모든 규칙을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-288">If none are found, select all rules that don't specify a category.</span></span>
* <span data-ttu-id="a2e4f-289">여러 규칙을 선택 하는 경우는 **마지막** 하나입니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-289">If multiple rules are selected take the **last** one.</span></span>
* <span data-ttu-id="a2e4f-290">규칙을 선택 하는 경우 사용 하 여 `MinimumLevel`합니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-290">If no rules are selected, use `MinimumLevel`.</span></span>
 
<span data-ttu-id="a2e4f-291">예를 들어 앞의 규칙 목록 있고 만들면는 `ILogger` "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine" 범주에 대 한 개체:</span><span class="sxs-lookup"><span data-stu-id="a2e4f-291">For example, suppose you have the preceding list of rules and you create an `ILogger` object for category "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span></span>

* <span data-ttu-id="a2e4f-292">디버그 공급자에 대 한 규칙 1, 6 및 8 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-292">For the Debug provider, rules 1, 6, and 8 apply.</span></span> <span data-ttu-id="a2e4f-293">규칙 8 가장 구체적인 이므로 선택한 것입니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-293">Rule 8 is most specific, so that's the one selected.</span></span>
* <span data-ttu-id="a2e4f-294">콘솔 공급자에 대 한 규칙 3, 4, 5 및 6이 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-294">For the Console provider, rules 3, 4, 5, and 6 apply.</span></span> <span data-ttu-id="a2e4f-295">규칙 3은 가장 구체적인입니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-295">Rule 3 is most specific.</span></span>

<span data-ttu-id="a2e4f-296">사용 하 여 로그를 만들 때는 `ILogger` "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine" 범주에 대 한 로그 `Trace` 수준 하 고 위에 디버그 공급자와의 로그를 `Debug` 수준 및 콘솔 공급자 위에 이동 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-296">When you create logs with an `ILogger` for category "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine", logs of `Trace` level and above will go to the Debug provider, and logs of `Debug` level and above will go to the Console provider.</span></span>

<span data-ttu-id="a2e4f-297">**별칭 공급자**</span><span class="sxs-lookup"><span data-stu-id="a2e4f-297">**Provider aliases**</span></span>

<span data-ttu-id="a2e4f-298">구성에서 공급자를 지정 하려면 형식 이름을 사용할 수 있지만 각 공급자 정의 더 짧은 *별칭* 하는 보다 쉽게 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-298">You can use the type name to specify a provider in configuration, but each provider defines a shorter *alias* that is easier to use.</span></span> <span data-ttu-id="a2e4f-299">기본 제공 공급자에 대 한 다음과 같은 별칭을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-299">For the built-in providers, use the following aliases:</span></span>

- <span data-ttu-id="a2e4f-300">콘솔</span><span class="sxs-lookup"><span data-stu-id="a2e4f-300">Console</span></span>
- <span data-ttu-id="a2e4f-301">디버그</span><span class="sxs-lookup"><span data-stu-id="a2e4f-301">Debug</span></span>
- <span data-ttu-id="a2e4f-302">이벤트 로그</span><span class="sxs-lookup"><span data-stu-id="a2e4f-302">EventLog</span></span>
- <span data-ttu-id="a2e4f-303">AzureAppServices</span><span class="sxs-lookup"><span data-stu-id="a2e4f-303">AzureAppServices</span></span>
- <span data-ttu-id="a2e4f-304">TraceSource</span><span class="sxs-lookup"><span data-stu-id="a2e4f-304">TraceSource</span></span>
- <span data-ttu-id="a2e4f-305">EventSource</span><span class="sxs-lookup"><span data-stu-id="a2e4f-305">EventSource</span></span>

<span data-ttu-id="a2e4f-306">**기본 최소 수준**</span><span class="sxs-lookup"><span data-stu-id="a2e4f-306">**Default minimum level**</span></span>

<span data-ttu-id="a2e4f-307">지정 된 공급자 및 범주에 대 한 구성 또는 코드에서 규칙이 적용 하는 경우에 적용 되는 최소 수준 설정이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-307">There is a minimum level setting that takes effect only if no rules from configuration or code apply for a given provider and category.</span></span> <span data-ttu-id="a2e4f-308">다음 예제에서는 최소 수준을 설정 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-308">The following example shows how to set the minimum level:</span></span>

[!code-csharp[](logging/sample2/Program.cs?name=snippet_MinLevel&highlight=3)]

<span data-ttu-id="a2e4f-309">기본값은 최소 수준을 명시적으로 설정 하지 않으면, `Information`, 즉 `Trace` 및 `Debug` 로그는 무시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-309">IF you don't explicitly set the minimum level, the default value is `Information`, which means that `Trace` and `Debug` logs are ignored.</span></span>

<span data-ttu-id="a2e4f-310">**필터 함수**</span><span class="sxs-lookup"><span data-stu-id="a2e4f-310">**Filter functions**</span></span>

<span data-ttu-id="a2e4f-311">필터링 규칙을 적용 하려면 필터 함수에 코드를 작성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-311">You can write code in a filter function to apply filtering rules.</span></span> <span data-ttu-id="a2e4f-312">Filter 함수는 모든 공급자 및 규칙 할당 하 여 구성 또는 코드를 갖지 않는 범주에 대해 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-312">A filter function is invoked for all providers and categories that do not have rules assigned to them by configuration or code.</span></span> <span data-ttu-id="a2e4f-313">함수에 코드 권한이 공급자 유형, 범주 및 로그 수준 메시지를 기록할지 여부를 결정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-313">Code in the function has access to the provider type, category, and log level to decide whether or not a message should be logged.</span></span> <span data-ttu-id="a2e4f-314">예:</span><span class="sxs-lookup"><span data-stu-id="a2e4f-314">For example:</span></span>

[!code-csharp[](logging/sample2/Program.cs?name=snippet_FilterFunction&highlight=5-13)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="a2e4f-315">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="a2e4f-315">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="a2e4f-316">일부 로깅 공급자 로그 수준 및 범주에 따라 로그 해야 저장소 미디어에 기록 되거나 무시 때 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-316">Some logging providers let you specify when logs should be written to a storage medium or ignored based on log level and category.</span></span>

<span data-ttu-id="a2e4f-317">`AddConsole` 및 `AddDebug` 확장 메서드 필터링 조건에 전달할 수 있는 오버 로드를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-317">The `AddConsole` and `AddDebug` extension methods provide overloads that let you pass in filtering criteria.</span></span> <span data-ttu-id="a2e4f-318">다음 샘플 코드 때문에 아래 로그를 무시 하려면 콘솔 공급자 `Warning` 디버그 공급자 프레임 워크에서 만들어지는 로그를 무시 하는 동안 수준입니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-318">The following sample code causes the console provider to ignore logs below `Warning` level, while the Debug provider ignores logs that the framework creates.</span></span>

[!code-csharp[](logging/sample/Startup.cs?name=snippet_AddConsoleAndDebugWithFilter&highlight=6-7)]

<span data-ttu-id="a2e4f-319">`AddEventLog` 메서드는 사용 하는 오버 로드는 `EventLogSettings` 에 필터링 함수를 포함할 수 있는 인스턴스를 해당 `Filter` 속성입니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-319">The `AddEventLog` method has an overload that takes an `EventLogSettings` instance, which may contain a filtering function in its `Filter` property.</span></span> <span data-ttu-id="a2e4f-320">TraceSource 공급자에서는 이러한 오버 로드 중 하나는 로깅 수준 및 기타 매개 변수 기반으로 하기 때문는 `SourceSwitch` 및 `TraceListener` 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-320">The TraceSource provider does not provide any of those overloads, since its logging level and other parameters are based on the  `SourceSwitch` and `TraceListener` it uses.</span></span>

<span data-ttu-id="a2e4f-321">등록 된 모든 공급자에 대 한 필터링 규칙을 설정할 수 있습니다는 `ILoggerFactory` 를 사용 하 여 인스턴스는 `WithFilter` 확장 메서드.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-321">You can set filtering rules for all providers that are registered with an `ILoggerFactory` instance by using the `WithFilter` extension method.</span></span> <span data-ttu-id="a2e4f-322">다음 예제에서는 디버그 수준에서 응용 프로그램 로그 하는 동안 경고를 프레임 워크 로그 (범주 "Microsoft" 또는 "시스템"으로 시작 함)을 제한 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-322">The example below limits framework logs (category begins with "Microsoft" or "System") to warnings while letting the app log at debug level.</span></span>

[!code-csharp[](logging/sample/Startup.cs?name=snippet_FactoryFilter&highlight=6-11)]

<span data-ttu-id="a2e4f-323">특정 범주에 대 한 기록에서 모든 로그를 방지 하기 위해 필터링을 사용 하려는 경우 지정할 수 있습니다 `LogLevel.None` 해당 범주에 대 한 최소 로그 수준으로 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-323">If you want to use filtering to prevent all logs from being written for a particular category, you can specify `LogLevel.None` as the minimum log level for that category.</span></span> <span data-ttu-id="a2e4f-324">정수 값 `LogLevel.None` 은 6 이며 보다 높은 `LogLevel.Critical` (5).</span><span class="sxs-lookup"><span data-stu-id="a2e4f-324">The integer value of `LogLevel.None` is 6, which is higher than `LogLevel.Critical` (5).</span></span>

<span data-ttu-id="a2e4f-325">`WithFilter` 제공한 확장 메서드는 [Microsoft.Extensions.Logging.Filter](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Filter) NuGet 패키지 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-325">The `WithFilter` extension method is provided by the [Microsoft.Extensions.Logging.Filter](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Filter) NuGet package.</span></span> <span data-ttu-id="a2e4f-326">메서드가 새 `ILoggerFactory` 함께 모든로 거 공급자에 전달 된 로그 메시지를 필터링 하는 인스턴스를 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-326">The method returns a new `ILoggerFactory` instance that will filter the log messages passed to all logger providers registered with it.</span></span> <span data-ttu-id="a2e4f-327">다른 모든 변경 되지 않습니다 `ILoggerFactory` 인스턴스를 원래 `ILoggerFactory` 인스턴스.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-327">It does not affect any other `ILoggerFactory` instances, including the original `ILoggerFactory` instance.</span></span>

---

## <a name="log-scopes"></a><span data-ttu-id="a2e4f-328">로그 범위</span><span class="sxs-lookup"><span data-stu-id="a2e4f-328">Log scopes</span></span>

<span data-ttu-id="a2e4f-329">내에서 논리적 작업의 집합을 그룹화 수는 *범위* 해당 집합의 일부로 만들어진 각 로그에 동일한 데이터를 연결 하기 위해 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-329">You can group a set of logical operations within a *scope* in order to attach the same data to each log that is created as part of that set.</span></span>  <span data-ttu-id="a2e4f-330">예를 들어 트랜잭션 ID를 포함 하도록 트랜잭션을 처리의 일부로 만들어진 모든 로그를 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-330">For example, you might want every log created as part of processing a transaction to include the transaction ID.</span></span>

<span data-ttu-id="a2e4f-331">범위는는 `IDisposable` 에서 반환 되는 형식에서 `ILogger.BeginScope<TState>` 메서드와 삭제 될 때까지 유지 되는 기간.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-331">A scope is an `IDisposable` type that is returned by the `ILogger.BeginScope<TState>` method and lasts until it is disposed.</span></span> <span data-ttu-id="a2e4f-332">범위를 사용 하 여 배치의 사용자로 거를 호출 하 여 한 `using` 다음과 같이 차단:</span><span class="sxs-lookup"><span data-stu-id="a2e4f-332">You use a scope by wrapping your logger calls in a `using` block, as shown here:</span></span>

[!code-csharp[](logging/sample//Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

<span data-ttu-id="a2e4f-333">다음 코드를 통해 콘솔 공급자에 대 한 범위:</span><span class="sxs-lookup"><span data-stu-id="a2e4f-333">The following code enables scopes for the console provider:</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="a2e4f-334">ASP.NET 2.x 핵심</span><span class="sxs-lookup"><span data-stu-id="a2e4f-334">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="a2e4f-335">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="a2e4f-335">In *Program.cs*:</span></span>

[!code-csharp[](logging/sample2/Program.cs?name=snippet_Scopes&highlight=4)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="a2e4f-336">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="a2e4f-336">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="a2e4f-337">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="a2e4f-337">In *Startup.cs*:</span></span>

[!code-csharp[](logging/sample/Startup.cs?name=snippet_Scopes&highlight=6)]

---

<span data-ttu-id="a2e4f-338">각 로그 메시지에는 범위가 지정 된 정보가 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-338">Each log message includes the scoped information:</span></span>

```
info: TodoApi.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApi.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApi.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApi.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a><span data-ttu-id="a2e4f-339">기본 제공 로깅 공급자</span><span class="sxs-lookup"><span data-stu-id="a2e4f-339">Built-in logging providers</span></span>

<span data-ttu-id="a2e4f-340">ASP.NET Core 다음 공급자를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-340">ASP.NET Core ships the following providers:</span></span>

* [<span data-ttu-id="a2e4f-341">콘솔</span><span class="sxs-lookup"><span data-stu-id="a2e4f-341">Console</span></span>](#console)
* [<span data-ttu-id="a2e4f-342">디버그</span><span class="sxs-lookup"><span data-stu-id="a2e4f-342">Debug</span></span>](#debug)
* [<span data-ttu-id="a2e4f-343">EventSource</span><span class="sxs-lookup"><span data-stu-id="a2e4f-343">EventSource</span></span>](#eventsource)
* [<span data-ttu-id="a2e4f-344">EventLog</span><span class="sxs-lookup"><span data-stu-id="a2e4f-344">EventLog</span></span>](#eventlog)
* [<span data-ttu-id="a2e4f-345">TraceSource</span><span class="sxs-lookup"><span data-stu-id="a2e4f-345">TraceSource</span></span>](#tracesource)
* [<span data-ttu-id="a2e4f-346">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="a2e4f-346">Azure App Service</span></span>](#appservice)

<a id="console"></a>
### <a name="the-console-provider"></a><span data-ttu-id="a2e4f-347">콘솔 공급자</span><span class="sxs-lookup"><span data-stu-id="a2e4f-347">The console provider</span></span>

<span data-ttu-id="a2e4f-348">[Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) 공급자 패키지 로그 출력을 콘솔에 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-348">The [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) provider package sends log output to the console.</span></span> 

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="a2e4f-349">ASP.NET 2.x 핵심</span><span class="sxs-lookup"><span data-stu-id="a2e4f-349">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```csharp
logging.AddConsole()
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="a2e4f-350">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="a2e4f-350">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

```csharp
loggerFactory.AddConsole()
```

<span data-ttu-id="a2e4f-351">[AddConsole 오버 로드](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.consoleloggerextensions) 에 전달할 수는 최소 로그 수준, 필터 함수 및 범위를 지원 하는지 여부를 나타내는 부울입니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-351">[AddConsole overloads](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.consoleloggerextensions) let you pass in an a minimum log level, a filter function, and a boolean that indicates whether scopes are supported.</span></span>  <span data-ttu-id="a2e4f-352">에 전달 하도록 또 다른 옵션은 프로그램 `IConfiguration` 범위 지원 및 로깅 수준을 지정할 수 있는 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-352">Another option is to pass in an `IConfiguration` object, which can specify scopes support and logging levels.</span></span> 

<span data-ttu-id="a2e4f-353">프로덕션 환경에서 사용 하기 위해 콘솔 공급자를 고려 하는 경우에 성능에 상당한 영향에 주의 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-353">If you are considering the console provider for use in production, be aware that it has a significant impact on performance.</span></span>

<span data-ttu-id="a2e4f-354">Visual Studio에서 새 프로젝트를 만들 때의 `AddConsole` 메서드는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-354">When you create a new project in Visual Studio, the `AddConsole` method looks like this:</span></span>

```csharp
loggerFactory.AddConsole(Configuration.GetSection("Logging"));
```

<span data-ttu-id="a2e4f-355">이 코드에서 참조 하는 `Logging` 의 섹션은 *appSettings.json* 파일:</span><span class="sxs-lookup"><span data-stu-id="a2e4f-355">This code refers to the `Logging` section of the *appSettings.json* file:</span></span>

[!code-json[](logging/sample//appsettings.json)]

<span data-ttu-id="a2e4f-356">에 설명 된 대로 디버그 수준에서 기록 하도록 응용 프로그램을 허용 하는 동안 제한 프레임 워크 로그 경고를 표시 설정을 [로그 필터링](#log-filtering) 섹션.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-356">The settings shown limit framework logs to warnings while allowing the app to log at debug level, as explained in the [Log filtering](#log-filtering) section.</span></span> <span data-ttu-id="a2e4f-357">자세한 내용은 참조 [구성](configuration.md)합니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-357">For more information, see [Configuration](configuration.md).</span></span>

---

<a id="debug"></a>
### <a name="the-debug-provider"></a><span data-ttu-id="a2e4f-358">디버그 공급자</span><span class="sxs-lookup"><span data-stu-id="a2e4f-358">The Debug provider</span></span>

<span data-ttu-id="a2e4f-359">[Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) 공급자 패키지 로그 출력을 사용 하 여 씁니다는 [System.Diagnostics.Debug](https://docs.microsoft.com/dotnet/core/api/system.diagnostics.debug#System_Diagnostics_Debug) 클래스 (`Debug.WriteLine` 메서드 호출).</span><span class="sxs-lookup"><span data-stu-id="a2e4f-359">The [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) provider package writes log output by using the [System.Diagnostics.Debug](https://docs.microsoft.com/dotnet/core/api/system.diagnostics.debug#System_Diagnostics_Debug) class (`Debug.WriteLine` method calls).</span></span>

<span data-ttu-id="a2e4f-360">Linux에서이 공급자는 로그를 기록 */var/log/message*합니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-360">On Linux, this provider writes logs to */var/log/message*.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="a2e4f-361">ASP.NET 2.x 핵심</span><span class="sxs-lookup"><span data-stu-id="a2e4f-361">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```csharp
logging.AddDebug()
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="a2e4f-362">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="a2e4f-362">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

```csharp
loggerFactory.AddDebug()
```

<span data-ttu-id="a2e4f-363">[오버 로드 AddDebug](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.debugloggerfactoryextensions) let 최소 로그 수준 또는 필터 함수에 전달 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-363">[AddDebug overloads](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.debugloggerfactoryextensions) let you pass in a minimum log level or a filter function.</span></span>

---

<a id="eventsource"></a>
### <a name="the-eventsource-provider"></a><span data-ttu-id="a2e4f-364">EventSource 공급자</span><span class="sxs-lookup"><span data-stu-id="a2e4f-364">The EventSource provider</span></span>

<span data-ttu-id="a2e4f-365">ASP.NET Core 1.1.0을 대상으로 하는 앱에 대 한 또는 그 이상으로 [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) 공급자 패키지 이벤트 추적을 구현할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-365">For apps that target ASP.NET Core 1.1.0 or higher, the [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) provider package can implement event tracing.</span></span> <span data-ttu-id="a2e4f-366">Windows에서 사용 하 여 [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803)합니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-366">On Windows, it uses [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span></span> <span data-ttu-id="a2e4f-367">공급자는 플랫폼 간 않으며 하는 이벤트가 수집 하 고 표시 도구가 아직 Linux 또는 macOS입니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-367">The provider is cross-platform, but there are no event collection and display tools yet for Linux or macOS.</span></span> 

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="a2e4f-368">ASP.NET 2.x 핵심</span><span class="sxs-lookup"><span data-stu-id="a2e4f-368">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```csharp
logging.AddEventSourceLogger()
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="a2e4f-369">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="a2e4f-369">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

```csharp
loggerFactory.AddEventSourceLogger()
```

---

<span data-ttu-id="a2e4f-370">수집 하 고 로그를 표시 하는 좋은 방법을 사용 하는 것은 [PerfView 유틸리티](https://www.microsoft.com/download/details.aspx?id=28567)합니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-370">A good way to collect and view logs is to use the [PerfView utility](https://www.microsoft.com/download/details.aspx?id=28567).</span></span> <span data-ttu-id="a2e4f-371">ETW 로그를 보기 위한 다른 도구 있지만 PerfView ASP.NET에서 내보내는 ETW 이벤트를 사용 하기 위한 최상의 환경을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-371">There are other tools for viewing ETW logs, but PerfView provides the best experience for working with the ETW events emitted by ASP.NET.</span></span> 

<span data-ttu-id="a2e4f-372">이 공급자에 의해 기록 된 이벤트를 수집 하기 위한 PerfView를 구성 하려면 문자열을 추가 `*Microsoft-Extensions-Logging` 에 **추가 공급자** 목록입니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-372">To configure PerfView for collecting events logged by this provider, add the string `*Microsoft-Extensions-Logging` to the **Additional Providers** list.</span></span> <span data-ttu-id="a2e4f-373">(문자열의 시작 부분에 별표를 누락 하지 마십시오.)</span><span class="sxs-lookup"><span data-stu-id="a2e4f-373">(Don't miss the asterisk at the start of the string.)</span></span>

![Perfview 추가 공급자](logging/_static/perfview-additional-providers.png)

<span data-ttu-id="a2e4f-375">Nano Server에서 이벤트 캡처 몇 가지 추가 설정이 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-375">Capturing events on Nano Server requires some additional setup:</span></span>

* <span data-ttu-id="a2e4f-376">PowerShell remoting Nano 서버에 연결 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-376">Connect PowerShell remoting to the Nano Server:</span></span>

  ```powershell
  Enter-PSSession [name]
  ```

* <span data-ttu-id="a2e4f-377">ETW 세션을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-377">Create an ETW session:</span></span>

  ```powershell
  New-EtwTraceSession -Name "MyAppTrace" -LocalFilePath C:\trace.etl
  ```

* <span data-ttu-id="a2e4f-378">ETW 공급자에 대 한 추가 [CLR](https://msdn.microsoft.com/library/ff357718), ASP.NET Core 및 필요에 따라 다른 사용자입니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-378">Add ETW providers for [CLR](https://msdn.microsoft.com/library/ff357718), ASP.NET Core, and others as needed.</span></span> <span data-ttu-id="a2e4f-379">GUID는 ASP.NET Core 공급자 `3ac73b97-af73-50e9-0822-5da4367920d0`합니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-379">The ASP.NET Core provider GUID is `3ac73b97-af73-50e9-0822-5da4367920d0`.</span></span> 

  ```powershell
  Add-EtwTraceProvider -Guid "{e13c0d23-ccbc-4e12-931b-d9cc2eee27e4}" -SessionName MyAppTrace
  Add-EtwTraceProvider -Guid "{3ac73b97-af73-50e9-0822-5da4367920d0}" -SessionName MyAppTrace
  ```

* <span data-ttu-id="a2e4f-380">사이트를 실행 하 고 추적 정보에 대 한 원하는 작업을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-380">Run the site and do whatever actions you want tracing information for.</span></span>

* <span data-ttu-id="a2e4f-381">마치면 추적 세션을 중지 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-381">Stop the tracing session when you're finished:</span></span>

  ```powershell
  Stop-EtwTraceSession -Name "MyAppTrace"
  ```

<span data-ttu-id="a2e4f-382">그 결과 *C:\trace.etl* 다른 Windows 버전에서와 같이 PerfView와 파일을 분석할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-382">The resulting *C:\trace.etl* file can be analyzed with PerfView as on other editions of Windows.</span></span>

<a id="eventlog"></a>
### <a name="the-windows-eventlog-provider"></a><span data-ttu-id="a2e4f-383">Windows 이벤트 로그 공급자</span><span class="sxs-lookup"><span data-stu-id="a2e4f-383">The Windows EventLog provider</span></span>

<span data-ttu-id="a2e4f-384">[Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) 공급자 패키지는 Windows 이벤트 로그에 로그 출력을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-384">The [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) provider package sends log output to the Windows Event Log.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="a2e4f-385">ASP.NET 2.x 핵심</span><span class="sxs-lookup"><span data-stu-id="a2e4f-385">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```csharp
logging.AddEventLog()
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="a2e4f-386">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="a2e4f-386">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

```csharp
loggerFactory.AddEventLog()
```

<span data-ttu-id="a2e4f-387">[AddEventLog 오버 로드](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.eventloggerfactoryextensions) 전달 let `EventLogSettings` 또는 최소 로그 수준입니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-387">[AddEventLog overloads](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.eventloggerfactoryextensions) let you pass in `EventLogSettings` or a minimum log level.</span></span>

---

<a id="tracesource"></a>
### <a name="the-tracesource-provider"></a><span data-ttu-id="a2e4f-388">TraceSource 공급자</span><span class="sxs-lookup"><span data-stu-id="a2e4f-388">The TraceSource provider</span></span>

<span data-ttu-id="a2e4f-389">[Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) 공급자 패키지가 사용 하는 [System.Diagnostics.TraceSource](https://msdn.microsoft.com/library/system.diagnostics.tracesource.aspx) 라이브러리 및 공급자입니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-389">The [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) provider package uses the [System.Diagnostics.TraceSource](https://msdn.microsoft.com/library/system.diagnostics.tracesource.aspx) libraries and providers.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="a2e4f-390">ASP.NET 2.x 핵심</span><span class="sxs-lookup"><span data-stu-id="a2e4f-390">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```csharp
logging.AddTraceSource(sourceSwitchName);
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="a2e4f-391">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="a2e4f-391">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

```csharp
loggerFactory.AddTraceSource(sourceSwitchName);
```

---

<span data-ttu-id="a2e4f-392">[오버 로드 AddTraceSource](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.tracesourcefactoryextensions) let 소스 스위치와 추적 수신기에 전달 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-392">[AddTraceSource overloads](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.tracesourcefactoryextensions) let you pass in a source switch and a trace listener.</span></span>

<span data-ttu-id="a2e4f-393">이 공급자를 사용 하려면는 응용 프로그램에서.NET Framework (아닌에서.NET Core)를 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-393">To use this provider, an application has to run on the .NET Framework (rather than .NET Core).</span></span> <span data-ttu-id="a2e4f-394">다양 한 메시지를 라우팅하는 공급자를 사용 하면 [수신기](https://msdn.microsoft.com/library/4y5y10s7)와 같은 [TextWriterTraceListener](https://msdn.microsoft.com/library/system.diagnostics.textwritertracelistener) 샘플 응용 프로그램에 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-394">The provider lets you route messages to a variety of [listeners](https://msdn.microsoft.com/library/4y5y10s7), such as the [TextWriterTraceListener](https://msdn.microsoft.com/library/system.diagnostics.textwritertracelistener) used in the sample application.</span></span>

<span data-ttu-id="a2e4f-395">다음 예제에서는 구성는 `TraceSource` 기록 하는 공급자 `Warning` 및 콘솔 창에 더 높은 메시지입니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-395">The following example configures a `TraceSource` provider that logs `Warning` and higher messages to the console window.</span></span>

[!code-csharp[](logging/sample/Startup.cs?name=snippet_TraceSource&highlight=9-12)]

<a id="appservice"></a>
### <a name="the-azure-app-service-provider"></a><span data-ttu-id="a2e4f-396">Azure 앱 서비스 공급자</span><span class="sxs-lookup"><span data-stu-id="a2e4f-396">The Azure App Service provider</span></span>

<span data-ttu-id="a2e4f-397">[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) 공급자 패키지 로그에는 Azure 앱 서비스 앱의 파일 시스템 및 텍스트 파일에 기록 [blob 저장소](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) 는 Azure 저장소 계정에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-397">The [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) provider package writes logs to text files in an Azure App Service app's file system and to [blob storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in an Azure Storage account.</span></span> <span data-ttu-id="a2e4f-398">공급자는 ASP.NET Core 1.1.0을 대상으로 하는 앱에 대해서만 사용할 수 있는 이후인 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-398">The provider is available only for apps that target ASP.NET Core 1.1.0 or higher.</span></span> 

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="a2e4f-399">ASP.NET 2.x 핵심</span><span class="sxs-lookup"><span data-stu-id="a2e4f-399">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

> [!NOTE]
> <span data-ttu-id="a2e4f-400">ASP.NET Core 2.0 미리 보기에는입니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-400">ASP.NET Core 2.0 is in preview.</span></span>  <span data-ttu-id="a2e4f-401">Azure 앱 서비스에 배포 될 때 최신 미리 보기 릴리스를 사용 하 여 만든 앱 실행 되지 않을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-401">Apps created with the latest preview release may not run when deployed to Azure App Service.</span></span> <span data-ttu-id="a2e4f-402">Azure 앱 서비스 2.0을 실행은 ASP.NET 코어 2.0 출시 되 면 앱 및 Azure 앱 서비스 공급자는 여기에 설명 된 대로 작동 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-402">When ASP.NET Core 2.0 is released, Azure App Service will run 2.0 apps, and the Azure App Service provider will work as indicated here.</span></span>

<span data-ttu-id="a2e4f-403">공급자 패키지 또는 호출 설치할 필요가 없습니다는 `AddAzureWebAppDiagnostics` 확장 메서드.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-403">You don't have to install the provider package or call the `AddAzureWebAppDiagnostics` extension method.</span></span>  <span data-ttu-id="a2e4f-404">공급자가 Azure 앱 서비스에 앱을 배포할 때 응용 프로그램에 자동으로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-404">The provider is automatically available to your app when you deploy the app to Azure App Service.</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="a2e4f-405">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="a2e4f-405">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

```csharp
loggerFactory.AddAzureWebAppDiagnostics();
```

<span data-ttu-id="a2e4f-406">`AddAzureWebAppDiagnostics` 에 전달할 수 있습니다를 오버 로드 [AzureAppServicesDiagnosticsSettings](https://github.com/aspnet/Logging/blob/c7d0b1b88668ff4ef8a86ea7d2ebb5ca7f88d3e0/src/Microsoft.Extensions.Logging.AzureAppServices/AzureAppServicesDiagnosticsSettings.cs)는 로깅 출력 서식 파일, blob 이름 및 파일 크기 제한을 같은 기본 설정을 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-406">An `AddAzureWebAppDiagnostics` overload lets you pass in [AzureAppServicesDiagnosticsSettings](https://github.com/aspnet/Logging/blob/c7d0b1b88668ff4ef8a86ea7d2ebb5ca7f88d3e0/src/Microsoft.Extensions.Logging.AzureAppServices/AzureAppServicesDiagnosticsSettings.cs), with which you can override default settings such as the logging output template, blob name, and file size limit.</span></span> <span data-ttu-id="a2e4f-407">(*출력 템플릿을* 는 위에 호출할 때 제공 하는 모든 로그에 적용 되는 메시지 형식 문자열은 `ILogger` 메서드.)</span><span class="sxs-lookup"><span data-stu-id="a2e4f-407">(*Output template* is a message format string that is applied to all logs, on top of the one that you provide when you call an `ILogger` method.)</span></span>  

---

<span data-ttu-id="a2e4f-408">응용 프로그램의 설정을 인식 앱 서비스 앱에 배포할 때의 [진단 로그](https://azure.microsoft.com/documentation/articles/web-sites-enable-diagnostic-log/#enablediag) 의 섹션은 **앱 서비스** Azure 포털의 페이지입니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-408">When you deploy to an App Service app, your application honors the settings in the [Diagnostic Logs](https://azure.microsoft.com/documentation/articles/web-sites-enable-diagnostic-log/#enablediag) section of the **App Service** page of the Azure portal.</span></span> <span data-ttu-id="a2e4f-409">이러한 설정을 변경 하면 변경 내용이 즉시 적용 앱을 다시 시작 하거나 코드를 다시 배포 하지 않아도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-409">When you change those settings, the changes take effect immediately without requiring that you restart the app or redeploy code to it.</span></span> 

![Azure 로깅 설정](logging/_static/azure-logging-settings.png)

<span data-ttu-id="a2e4f-411">로그 파일에 대 한 기본 위치는는 *d:\\홈\\LogFiles\\응용 프로그램* 폴더가 고 기본 파일 이름은 *진단 yyyymmdd.txt*합니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-411">The default location for log files is in the *D:\\home\\LogFiles\\Application* folder, and the default file name is *diagnostics-yyyymmdd.txt*.</span></span> <span data-ttu-id="a2e4f-412">기본 파일 크기 제한을 10MB 이하로 및 유지 하는 파일의 기본 최대 수는 2입니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-412">The default file size limit is 10 MB, and the default maximum number of files retained is 2.</span></span> <span data-ttu-id="a2e4f-413">기본 blob 이름은 *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*합니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-413">The default blob name is *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span></span> <span data-ttu-id="a2e4f-414">기본 동작에 대 한 자세한 내용은 참조 [AzureAppServicesDiagnosticsSettings](https://github.com/aspnet/Logging/blob/c7d0b1b88668ff4ef8a86ea7d2ebb5ca7f88d3e0/src/Microsoft.Extensions.Logging.AzureAppServices/AzureAppServicesDiagnosticsSettings.cs)합니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-414">For more information about default behavior, see [AzureAppServicesDiagnosticsSettings](https://github.com/aspnet/Logging/blob/c7d0b1b88668ff4ef8a86ea7d2ebb5ca7f88d3e0/src/Microsoft.Extensions.Logging.AzureAppServices/AzureAppServicesDiagnosticsSettings.cs).</span></span>

<span data-ttu-id="a2e4f-415">공급자는 프로젝트는 Azure 환경에서 실행 하는 경우에 작동 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-415">The provider only works when your project runs in the Azure environment.</span></span>  <span data-ttu-id="a2e4f-416">영향을 주지 않습니다 로컬로 실행할 때 &mdash; 로컬 파일 또는 blob에 대 한 로컬 개발 저장소에 기록 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-416">It has no effect when you run locally &mdash; it does not write to local files or local development storage for blobs.</span></span>

## <a name="third-party-logging-providers"></a><span data-ttu-id="a2e4f-417">제 3 자 로깅 공급자</span><span class="sxs-lookup"><span data-stu-id="a2e4f-417">Third-party logging providers</span></span>

<span data-ttu-id="a2e4f-418">ASP.NET Core를 사용 하는 몇 가지 타사 로깅 프레임 워크는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-418">Here are some third-party logging frameworks that work with ASP.NET Core:</span></span>

* <span data-ttu-id="a2e4f-419">[elmah.io](https://github.com/elmahio/Elmah.Io.Extensions.Logging) -Elmah.Io 서비스 공급자</span><span class="sxs-lookup"><span data-stu-id="a2e4f-419">[elmah.io](https://github.com/elmahio/Elmah.Io.Extensions.Logging) - provider for the Elmah.Io service</span></span>

* <span data-ttu-id="a2e4f-420">[JSNLog](http://jsnlog.com) -서버 쪽 로그에 JavaScript 예외 및 기타 클라이언트 쪽 이벤트를 기록 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-420">[JSNLog](http://jsnlog.com) - logs JavaScript exceptions and other client-side events in your server-side log.</span></span>

* <span data-ttu-id="a2e4f-421">[Loggr](https://github.com/imobile3/Loggr.Extensions.Logging) -Loggr 서비스 공급자</span><span class="sxs-lookup"><span data-stu-id="a2e4f-421">[Loggr](https://github.com/imobile3/Loggr.Extensions.Logging) - provider for the Loggr service</span></span>

* <span data-ttu-id="a2e4f-422">[NLog](https://github.com/NLog/NLog.Extensions.Logging) -NLog 라이브러리에 대 한 공급자</span><span class="sxs-lookup"><span data-stu-id="a2e4f-422">[NLog](https://github.com/NLog/NLog.Extensions.Logging) - provider for the NLog library</span></span>

* <span data-ttu-id="a2e4f-423">[Serilog](https://github.com/serilog/serilog-framework-logging) -Serilog 라이브러리에 대 한 공급자</span><span class="sxs-lookup"><span data-stu-id="a2e4f-423">[Serilog](https://github.com/serilog/serilog-framework-logging) - provider for the Serilog library</span></span>

<span data-ttu-id="a2e4f-424">일부 타사 프레임 워크에서는 작업을 수행할 수 [구조적된 로깅 라고도 하는 의미 체계 로깅](http://programmers.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)합니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-424">Some third-party frameworks can do [semantic logging, also known as structured logging](http://programmers.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="a2e4f-425">타사 프레임 워크를 사용 하는 것은 기본 제공 공급자 중 하나를 사용 하 여 비슷합니다: 프로젝트에 NuGet 패키지를 추가 하 고에 확장 메서드를 호출 `ILoggerFactory`합니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-425">Using a third-party framework is similar to using one of the built-in providers: add a NuGet package to your project and call an extension method on `ILoggerFactory`.</span></span> <span data-ttu-id="a2e4f-426">자세한 내용은 각 프레임 워크의 설명서를 참조 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-426">For more information, see each framework's documentation.</span></span>

<span data-ttu-id="a2e4f-427">다른 로깅 프레임 워크 또는 사용자의 로깅 요구 사항을 지원 하기 위해 사용자 고유의 사용자 지정 공급자도 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a2e4f-427">You can create your own custom providers as well, to support other logging frameworks or your own logging requirements.</span></span>
