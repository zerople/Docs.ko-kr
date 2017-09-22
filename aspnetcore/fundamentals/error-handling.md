---
title: "ASP.NET Core의 오류 처리"
author: ardalis
description: "ASP.NET Core 응용 프로그램의 오류를 처리 하는 방법을 설명 합니다."
keywords: "ASP.NET Core, 오류 처리, 예외 처리"
ms.author: tdykstra
manager: wpickett
ms.date: 11/30/2016
ms.topic: article
ms.assetid: 4db51023-c8a6-4119-bbbe-3917e272c260
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/error-handling
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 93f0724dbe98316e2b5a0af0ac1760c3aac2f1d0
ms.sourcegitcommit: 78d28178345a0eea91556e4cd1adad98b1446db8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2017
---
# <a name="introduction-to-error-handling-in-aspnet-core"></a><span data-ttu-id="43455-104">ASP.NET Core의 오류 처리 소개</span><span class="sxs-lookup"><span data-stu-id="43455-104">Introduction to Error Handling in ASP.NET Core</span></span>

<span data-ttu-id="43455-105">여 [Steve Smith](https://ardalis.com/) 및 [Tom Dykstra](https://github.com/tdykstra/)</span><span class="sxs-lookup"><span data-stu-id="43455-105">By [Steve Smith](https://ardalis.com/) and [Tom Dykstra](https://github.com/tdykstra/)</span></span>

<span data-ttu-id="43455-106">이 문서에서는 일반적인 appoaches ASP.NET Core 응용 프로그램의 오류 처리를 다룹니다.</span><span class="sxs-lookup"><span data-stu-id="43455-106">This article covers common appoaches to handling errors in ASP.NET Core apps.</span></span>

[<span data-ttu-id="43455-107">샘플 코드 보기 또는 다운로드</span><span class="sxs-lookup"><span data-stu-id="43455-107">View or download sample code</span></span>](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/error-handling/sample)

## <a name="the-developer-exception-page"></a><span data-ttu-id="43455-108">개발자 예외 페이지</span><span class="sxs-lookup"><span data-stu-id="43455-108">The developer exception page</span></span>

<span data-ttu-id="43455-109">예외에 대 한 자세한 정보를 표시 하는 페이지를 표시 하는 응용 프로그램을 구성 하려면 설치는 `Microsoft.AspNetCore.Diagnostics` NuGet 패키지 하 고 행을 추가 하는 [시작 클래스에서 메서드를 구성](startup.md):</span><span class="sxs-lookup"><span data-stu-id="43455-109">To configure an app to display a page that shows detailed information about exceptions, install the `Microsoft.AspNetCore.Diagnostics` NuGet package and add a line to the [Configure method in the Startup class](startup.md):</span></span>

[!code-csharp[Main](error-handling/sample/Startup.cs?name=snippet_DevExceptionPage&highlight=7)]

<span data-ttu-id="43455-110">배치 `UseDeveloperExceptionPage` 와 같은 예외를 catch 하려는 모든 미들웨어 전에 `app.UseMvc`합니다.</span><span class="sxs-lookup"><span data-stu-id="43455-110">Put `UseDeveloperExceptionPage` before any middleware you want to catch exceptions in, such as `app.UseMvc`.</span></span>

>[!WARNING]
> <span data-ttu-id="43455-111">개발자 예외 페이지를 사용 하도록 설정 **응용 프로그램 개발 환경에서 실행 중인 경우에**합니다.</span><span class="sxs-lookup"><span data-stu-id="43455-111">Enable the developer exception page **only when the app is running in the Development environment**.</span></span> <span data-ttu-id="43455-112">프로덕션 환경에서 앱을 실행할 때 자세한 예외 정보를 공개적으로 공유 하는 것이 않으려는 합니다.</span><span class="sxs-lookup"><span data-stu-id="43455-112">You don't want to share detailed exception information publicly when the app runs in production.</span></span> <span data-ttu-id="43455-113">[환경 구성에 대 한 자세한](environments.md)합니다.</span><span class="sxs-lookup"><span data-stu-id="43455-113">[Learn more about configuring environments](environments.md).</span></span>

<span data-ttu-id="43455-114">개발자 예외 페이지를 보려면 샘플 응용 프로그램 설정 된 환경으로 실행 `Development`, 추가 `?throw=true` 응용 프로그램의 기본 URL에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="43455-114">To see the developer exception page, run the sample application with the environment set to `Development`, and add `?throw=true` to the base URL of the app.</span></span> <span data-ttu-id="43455-115">예외 및 요청에 대 한 정보로 여러 탭 페이지에 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="43455-115">The page includes several tabs with information about the exception and the request.</span></span> <span data-ttu-id="43455-116">첫 번째 탭에는 스택 추적에 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="43455-116">The first tab includes a stack trace.</span></span> 

![스택 추적](error-handling/_static/developer-exception-page.png)

<span data-ttu-id="43455-118">다음 탭 있는 경우는 쿼리 문자열 매개 변수를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="43455-118">The next tab shows the query string parameters, if any.</span></span>

![쿼리 문자열 매개 변수](error-handling/_static/developer-exception-page-query.png)

<span data-ttu-id="43455-120">이 요청 된 쿠키를 포함 하지 않은 있지만에 표시 되는 경우는 **쿠키** 탭 합니다. 마지막으로 탭에 전달 된 헤더를 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="43455-120">This request didn't have any cookies, but if it did, they would appear on the **Cookies** tab. You can see the headers that were passed in the last tab.</span></span>

![헤더](error-handling/_static/developer-exception-page-headers.png)

## <a name="configuring-a-custom-exception-handling-page"></a><span data-ttu-id="43455-122">페이지를 처리 하는 사용자 지정 예외를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="43455-122">Configuring a custom exception handling page</span></span>

<span data-ttu-id="43455-123">앱 실행 중이지 않을 때 사용 하는 예외 처리기 페이지를 구성 하는 것이 좋습니다는 `Development` 환경입니다.</span><span class="sxs-lookup"><span data-stu-id="43455-123">It's a good idea to configure an exception handler page to use when the app is not running in the `Development` environment.</span></span>

[!code-csharp[Main](error-handling/sample/Startup.cs?name=snippet_DevExceptionPage&highlight=11)]

<span data-ttu-id="43455-124">MVC 응용 프로그램의 안 함 명시적으로 데코 레이트 HTTP 메서드 특성을 사용 하 여 오류 처리기 작업 메서드 같은 `HttpGet`합니다.</span><span class="sxs-lookup"><span data-stu-id="43455-124">In an MVC app, don't explicitly decorate the error handler action method with HTTP method attributes, such as `HttpGet`.</span></span> <span data-ttu-id="43455-125">명시적 동사를 사용 하 여 메서드에서 일부 요청 하지 못하도록 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="43455-125">Using explicit verbs could prevent some requests from reaching the method.</span></span>

```csharp
[Route("/Error")]
public IActionResult Index()
{
    // Handle error here
}
```

## <a name="configuring-status-code-pages"></a><span data-ttu-id="43455-126">상태 코드 페이지를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="43455-126">Configuring status code pages</span></span>

<span data-ttu-id="43455-127">기본적으로 응용 프로그램에 대 한 HTTP 상태 코드 500 (내부 서버 오류) 또는 404 (찾을 수 없음)와 같은 다양 한 상태 코드 페이지를 제공 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="43455-127">By default, your app will not provide a rich status code page for HTTP status codes such as 500 (Internal Server Error) or 404 (Not Found).</span></span> <span data-ttu-id="43455-128">구성할 수는 `StatusCodePagesMiddleware` 줄을 추가 하 여는 `Configure` 메서드:</span><span class="sxs-lookup"><span data-stu-id="43455-128">You can configure the `StatusCodePagesMiddleware` by adding a line to the `Configure` method:</span></span>

```csharp
app.UseStatusCodePages();
```

<span data-ttu-id="43455-129">기본적으로이 미들웨어 404 등의 일반적인 상태 코드에 대 한 간단 하 고 텍스트 전용 처리기를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="43455-129">By default, this middleware adds simple, text-only handlers for common status codes, such as 404:</span></span>

![404 페이지](error-handling/_static/default-404-status-code.png)

<span data-ttu-id="43455-131">미들웨어는 일부의 확장 메서드를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="43455-131">The middleware supports several different extension methods.</span></span> <span data-ttu-id="43455-132">람다 식에서 바라볼, 다른 콘텐츠 형식 및 형식 문자열을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="43455-132">One takes a lambda expression, another takes a content type and format string.</span></span>

[!code-csharp[Main](error-handling/sample/Startup.cs?name=snippet_StatusCodePages)]

```csharp
app.UseStatusCodePages("text/plain", "Status code page, status code: {0}");
```

<span data-ttu-id="43455-133">리디렉션 확장 메서드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="43455-133">There are also redirect extension methods.</span></span> <span data-ttu-id="43455-134">하나 302 상태 코드를 클라이언트에 보내고 하나 클라이언트에는 원래 상태 코드를 반환 하지만 또한 리디렉션 URL에 대 한 처리기를 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="43455-134">One sends a 302 status code to the client, and one returns the original status code to the client but also executes the handler for the redirect URL.</span></span>

[!code-csharp[Main](error-handling/sample/Startup.cs?name=snippet_StatusCodePagesWithRedirect)]

```csharp
app.UseStatusCodePagesWithReExecute("/error/{0}");
```

<span data-ttu-id="43455-135">특정 요청에 대 한 상태 코드 페이지를 사용 하지 않도록 설정 해야 할 경우이 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="43455-135">If you need to disable status code pages for certain requests, you can do so:</span></span>

```csharp
var statusCodePagesFeature = context.Features.Get<IStatusCodePagesFeature>();
if (statusCodePagesFeature != null)
{
  statusCodePagesFeature.Enabled = false;
}
```

## <a name="exception-handling-code"></a><span data-ttu-id="43455-136">예외 처리 코드</span><span class="sxs-lookup"><span data-stu-id="43455-136">Exception-handling code</span></span>

<span data-ttu-id="43455-137">예외 처리 페이지의에서 코드는 예외가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="43455-137">Code in exception handling pages can throw exceptions.</span></span> <span data-ttu-id="43455-138">종종 완전 한 정적 콘텐츠의 구성 프로덕션 오류 페이지에 대 한는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="43455-138">It's often a good idea for production error pages to consist of purely static content.</span></span>

<span data-ttu-id="43455-139">또한 유의 하십시오를 응답에 대 한 헤더를 보낸 응답의 상태 코드를 변경할 수 없습니다 및 예외 페이지 또는 처리기 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="43455-139">Also, be aware that once the headers for a response have been sent, you can't change the response's status code, nor can any exception pages or handlers run.</span></span> <span data-ttu-id="43455-140">응답을 완료 해야 하거나 연결이 중단 합니다.</span><span class="sxs-lookup"><span data-stu-id="43455-140">The response must be completed or the connection aborted.</span></span>

## <a name="server-exception-handling"></a><span data-ttu-id="43455-141">서버 예외 처리</span><span class="sxs-lookup"><span data-stu-id="43455-141">Server exception handling</span></span>

<span data-ttu-id="43455-142">응용 프로그램에서 논리를 처리 하는 예외 뿐 아니라는 [서버](servers/index.md) 응용 프로그램 호스팅 몇 가지 예외 처리를 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="43455-142">In addition to the exception handling logic in your app, the [server](servers/index.md) hosting your app performs some exception handling.</span></span> <span data-ttu-id="43455-143">헤더를 보내기 전에 된 예외를 catch 하는 서버를 하는 경우 서버 본문이 없는 500 내부 서버 오류 응답을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="43455-143">If the server catches an exception before the headers are sent, the server sends a 500 Internal Server Error response with no body.</span></span> <span data-ttu-id="43455-144">헤더를 보낸 후에 예외를 catch 하는 서버를 서버 연결을 닫습니다.</span><span class="sxs-lookup"><span data-stu-id="43455-144">If the server catches an exception after the headers have been sent, the server closes the connection.</span></span> <span data-ttu-id="43455-145">응용 프로그램으로 처리 되지 요청은 서버에서 처리 됩니다.</span><span class="sxs-lookup"><span data-stu-id="43455-145">Requests that aren't handled by your app are handled by the server.</span></span> <span data-ttu-id="43455-146">서버의 예외 발생 하는 모든 예외를 처리를 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="43455-146">Any exception that occurs is handled by the server's exception handling.</span></span> <span data-ttu-id="43455-147">구성 된 모든 사용자 지정 오류 페이지 또는 예외 처리 미들웨어 또는 필터 안 함이 동작에 영향을이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="43455-147">Any configured custom error pages or exception handling middleware or filters don't affect this behavior.</span></span>

## <a name="startup-exception-handling"></a><span data-ttu-id="43455-148">시작 예외 처리</span><span class="sxs-lookup"><span data-stu-id="43455-148">Startup exception handling</span></span>

<span data-ttu-id="43455-149">호스팅 계층에만 응용 프로그램 시작 시 수행 되는 예외를 처리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="43455-149">Only the hosting layer can handle exceptions that take place during app startup.</span></span> <span data-ttu-id="43455-150">할 수 있습니다 [호스트 오류에 대 한 응답으로 시작 하는 동안 작동 하는 방법을 구성할](hosting.md#detailed-errors) 를 사용 하 여 `captureStartupErrors` 및 `detailedErrors` 키입니다.</span><span class="sxs-lookup"><span data-stu-id="43455-150">You can [configure how the host behaves in response to errors during startup](hosting.md#detailed-errors) using `captureStartupErrors` and the `detailedErrors` key.</span></span>

<span data-ttu-id="43455-151">호스트 주소/포트 바인딩 후에 오류가 발생 하는 경우 호스팅 캡처된 시작 오류에 대 한 오류 페이지만 표시할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="43455-151">Hosting can only show an error page for a captured startup error if the error occurs after host address/port binding.</span></span> <span data-ttu-id="43455-152">어떤 이유로 든 실패 하면 모든 바인딩에 호스팅 계층 dotnet 프로세스가 충돌할 중요 한 예외를 기록 하 고 없음 오류 페이지가 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="43455-152">If any binding fails for any reason, the hosting layer logs a critical exception, the dotnet process crashes, and no error page is displayed.</span></span>

## <a name="aspnet-mvc-error-handling"></a><span data-ttu-id="43455-153">ASP.NET MVC 오류 처리</span><span class="sxs-lookup"><span data-stu-id="43455-153">ASP.NET MVC error handling</span></span>

<span data-ttu-id="43455-154">[MVC](../mvc/index.md) 앱은 예외 필터를 구성 하 고 모델 유효성 검사를 수행 하는 등의 오류를 처리 하기 위한 몇 가지 추가 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="43455-154">[MVC](../mvc/index.md) apps have some additional options for handling errors, such as configuring exception filters and performing model validation.</span></span>

### <a name="exception-filters"></a><span data-ttu-id="43455-155">예외 필터</span><span class="sxs-lookup"><span data-stu-id="43455-155">Exception Filters</span></span>

<span data-ttu-id="43455-156">전역으로 또는 MVC 응용 프로그램에서-컨트롤러 또는 동작 당 기준 예외 필터를 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="43455-156">Exception filters can be configured globally or on a per-controller or per-action basis in an MVC app.</span></span> <span data-ttu-id="43455-157">이러한 필터 컨트롤러 작업 또는 다른 필터를 실행 하는 동안 발생 하는 처리 되지 않은 예외를 처리 하며 그렇지 않으면 호출 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="43455-157">These filters handle any unhandled exception that occurs during the execution of a controller action or another filter, and are not called otherwise.</span></span> <span data-ttu-id="43455-158">예외 필터에 대 한 자세한 [필터](../mvc/controllers/filters.md)합니다.</span><span class="sxs-lookup"><span data-stu-id="43455-158">Learn more about exception filters in [Filters](../mvc/controllers/filters.md).</span></span>

>[!TIP]
> <span data-ttu-id="43455-159">예외 필터는 MVC 동작 내에서 발생 하는 예외를 트래핑 하는 데 유용 하지만 유연 하 게 오류 미들웨어를 처리 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="43455-159">Exception filters are good for trapping exceptions that occur within MVC actions, but they're not as flexible as error handling middleware.</span></span> <span data-ttu-id="43455-160">일반적인 경우에 대 한 미들웨어를 선호 하 고만 해야 하는 필터를 사용 하 여 오류 처리 작업을 수행할 *다르게* MVC 작업 선택에 따라 합니다.</span><span class="sxs-lookup"><span data-stu-id="43455-160">Prefer middleware for the general case, and use filters only where you need to do error handling *differently* based on which MVC action was chosen.</span></span>

### <a name="handling-model-state-errors"></a><span data-ttu-id="43455-161">처리 모델 상태 오류</span><span class="sxs-lookup"><span data-stu-id="43455-161">Handling Model State Errors</span></span>

<span data-ttu-id="43455-162">[유효성 검사 모델](../mvc/models/validation.md) 호출 되는 각 컨트롤러 동작 하기 전에 발생 하 고 검사 해야 하는 작업 메서드의 `ModelState.IsValid` 하 고 적절 하 게 대처 합니다.</span><span class="sxs-lookup"><span data-stu-id="43455-162">[Model validation](../mvc/models/validation.md) occurs prior to each controller action being invoked, and it is the action method’s responsibility to inspect `ModelState.IsValid` and react appropriately.</span></span>

<span data-ttu-id="43455-163">일부 응용 프로그램 모델 유효성 검사 오류를 처리 하기 위한 표준 규칙에는 쿼리에서 수행 하도록 선택 하는 [필터](../mvc/controllers/filters.md) 이러한 정책을 구현 하는 적절 한 위치 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="43455-163">Some apps will choose to follow a standard convention for dealing with model validation errors, in which case a [filter](../mvc/controllers/filters.md) may be an appropriate place to implement such a policy.</span></span> <span data-ttu-id="43455-164">잘못 된 모델 상태와 작업을 수행할 때 동작 하는 방법을 테스트 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="43455-164">You should test how your actions behave with invalid model states.</span></span> <span data-ttu-id="43455-165">자세한 내용을 알아보세요 [컨트롤러 논리를 테스트](../mvc/controllers/testing.md)합니다.</span><span class="sxs-lookup"><span data-stu-id="43455-165">Learn more in [Testing controller logic](../mvc/controllers/testing.md).</span></span>



