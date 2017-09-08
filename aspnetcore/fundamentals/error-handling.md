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
ms.openlocfilehash: 5898892c63e978adfabf9939394fef4ea1848d49
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/11/2017
---
# <a name="introduction-to-error-handling-in-aspnet-core"></a><span data-ttu-id="2486a-104">ASP.NET Core의 오류 처리 소개</span><span class="sxs-lookup"><span data-stu-id="2486a-104">Introduction to Error Handling in ASP.NET Core</span></span>

<span data-ttu-id="2486a-105">여 [Steve Smith](http://ardalis.com) 및 [Tom Dykstra](https://github.com/tdykstra/)</span><span class="sxs-lookup"><span data-stu-id="2486a-105">By [Steve Smith](http://ardalis.com) and [Tom Dykstra](https://github.com/tdykstra/)</span></span>

<span data-ttu-id="2486a-106">이 문서에서는 일반적인 appoaches ASP.NET Core 응용 프로그램의 오류 처리를 다룹니다.</span><span class="sxs-lookup"><span data-stu-id="2486a-106">This article covers common appoaches to handling errors in ASP.NET Core apps.</span></span>

[<span data-ttu-id="2486a-107">샘플 코드 보기 또는 다운로드</span><span class="sxs-lookup"><span data-stu-id="2486a-107">View or download sample code</span></span>](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/error-handling/sample)

## <a name="the-developer-exception-page"></a><span data-ttu-id="2486a-108">개발자 예외 페이지</span><span class="sxs-lookup"><span data-stu-id="2486a-108">The developer exception page</span></span>

<span data-ttu-id="2486a-109">예외에 대 한 자세한 정보를 표시 하는 페이지를 표시 하는 응용 프로그램을 구성 하려면 설치는 `Microsoft.AspNetCore.Diagnostics` NuGet 패키지 하 고 행을 추가 하는 [시작 클래스에서 메서드를 구성](startup.md):</span><span class="sxs-lookup"><span data-stu-id="2486a-109">To configure an app to display a page that shows detailed information about exceptions, install the `Microsoft.AspNetCore.Diagnostics` NuGet package and add a line to the [Configure method in the Startup class](startup.md):</span></span>

<span data-ttu-id="2486a-110">[!code-csharp[Main](error-handling/sample/Startup.cs?name=snippet_DevExceptionPage&highlight=7)]</span><span class="sxs-lookup"><span data-stu-id="2486a-110">[!code-csharp[Main](error-handling/sample/Startup.cs?name=snippet_DevExceptionPage&highlight=7)]</span></span>

<span data-ttu-id="2486a-111">배치 `UseDeveloperExceptionPage` 와 같은 예외를 catch 하려는 모든 미들웨어 전에 `app.UseMvc`합니다.</span><span class="sxs-lookup"><span data-stu-id="2486a-111">Put `UseDeveloperExceptionPage` before any middleware you want to catch exceptions in, such as `app.UseMvc`.</span></span>

>[!WARNING]
> <span data-ttu-id="2486a-112">개발자 예외 페이지를 사용 하도록 설정 **응용 프로그램 개발 환경에서 실행 중인 경우에**합니다.</span><span class="sxs-lookup"><span data-stu-id="2486a-112">Enable the developer exception page **only when the app is running in the Development environment**.</span></span> <span data-ttu-id="2486a-113">프로덕션 환경에서 앱을 실행할 때 자세한 예외 정보를 공개적으로 공유 하는 것이 않으려는 합니다.</span><span class="sxs-lookup"><span data-stu-id="2486a-113">You don't want to share detailed exception information publicly when the app runs in production.</span></span> <span data-ttu-id="2486a-114">[환경 구성에 대 한 자세한](environments.md)합니다.</span><span class="sxs-lookup"><span data-stu-id="2486a-114">[Learn more about configuring environments](environments.md).</span></span>

<span data-ttu-id="2486a-115">개발자 예외 페이지를 보려면 샘플 응용 프로그램 설정 된 환경으로 실행 `Development`, 추가 `?throw=true` 응용 프로그램의 기본 URL에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2486a-115">To see the developer exception page, run the sample application with the environment set to `Development`, and add `?throw=true` to the base URL of the app.</span></span> <span data-ttu-id="2486a-116">예외 및 요청에 대 한 정보로 여러 탭 페이지에 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2486a-116">The page includes several tabs with information about the exception and the request.</span></span> <span data-ttu-id="2486a-117">첫 번째 탭에는 스택 추적에 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2486a-117">The first tab includes a stack trace.</span></span> 

![스택 추적](error-handling/_static/developer-exception-page.png)

<span data-ttu-id="2486a-119">다음 탭 있는 경우는 쿼리 문자열 매개 변수를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="2486a-119">The next tab shows the query string parameters, if any.</span></span>

![쿼리 문자열 매개 변수](error-handling/_static/developer-exception-page-query.png)

<span data-ttu-id="2486a-121">이 요청 된 쿠키를 포함 하지 않은 있지만에 표시 되는 경우는 **쿠키** 탭 합니다.</span><span class="sxs-lookup"><span data-stu-id="2486a-121">This request didn't have any cookies, but if it did, they would appear on the **Cookies** tab.</span></span> <span data-ttu-id="2486a-122">마지막으로 탭에 전달 된 헤더를 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2486a-122">You can see the headers that were passed in the last tab.</span></span>

![헤더](error-handling/_static/developer-exception-page-headers.png)

## <a name="configuring-a-custom-exception-handling-page"></a><span data-ttu-id="2486a-124">페이지를 처리 하는 사용자 지정 예외를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="2486a-124">Configuring a custom exception handling page</span></span>

<span data-ttu-id="2486a-125">앱 실행 중이지 않을 때 사용 하는 예외 처리기 페이지를 구성 하는 것이 좋습니다는 `Development` 환경입니다.</span><span class="sxs-lookup"><span data-stu-id="2486a-125">It's a good idea to configure an exception handler page to use when the app is not running in the `Development` environment.</span></span>

<span data-ttu-id="2486a-126">[!code-csharp[Main](error-handling/sample/Startup.cs?name=snippet_DevExceptionPage&highlight=11)]</span><span class="sxs-lookup"><span data-stu-id="2486a-126">[!code-csharp[Main](error-handling/sample/Startup.cs?name=snippet_DevExceptionPage&highlight=11)]</span></span>

<span data-ttu-id="2486a-127">MVC 응용 프로그램의 안 함 명시적으로 데코 레이트 HTTP 메서드 특성을 사용 하 여 오류 처리기 작업 메서드 같은 `HttpGet`합니다.</span><span class="sxs-lookup"><span data-stu-id="2486a-127">In an MVC app, don't explicitly decorate the error handler action method with HTTP method attributes, such as `HttpGet`.</span></span> <span data-ttu-id="2486a-128">명시적 동사를 사용 하 여 메서드에서 일부 요청 하지 못하도록 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="2486a-128">Using explicit verbs could prevent some requests from reaching the method.</span></span>

```csharp
[Route("/Error")]
public IActionResult Index()
{
    // Handle error here
}
```

## <a name="configuring-status-code-pages"></a><span data-ttu-id="2486a-129">상태 코드 페이지를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="2486a-129">Configuring status code pages</span></span>

<span data-ttu-id="2486a-130">기본적으로 응용 프로그램에 대 한 HTTP 상태 코드 500 (내부 서버 오류) 또는 404 (찾을 수 없음)와 같은 다양 한 상태 코드 페이지를 제공 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2486a-130">By default, your app will not provide a rich status code page for HTTP status codes such as 500 (Internal Server Error) or 404 (Not Found).</span></span> <span data-ttu-id="2486a-131">구성할 수는 `StatusCodePagesMiddleware` 줄을 추가 하 여는 `Configure` 메서드:</span><span class="sxs-lookup"><span data-stu-id="2486a-131">You can configure the `StatusCodePagesMiddleware` by adding a line to the `Configure` method:</span></span>

```csharp
app.UseStatusCodePages();
```

<span data-ttu-id="2486a-132">기본적으로이 미들웨어 404 등의 일반적인 상태 코드에 대 한 간단 하 고 텍스트 전용 처리기를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2486a-132">By default, this middleware adds simple, text-only handlers for common status codes, such as 404:</span></span>

![404 페이지](error-handling/_static/default-404-status-code.png)

<span data-ttu-id="2486a-134">미들웨어는 일부의 확장 메서드를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="2486a-134">The middleware supports several different extension methods.</span></span> <span data-ttu-id="2486a-135">람다 식에서 바라볼, 다른 콘텐츠 형식 및 형식 문자열을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="2486a-135">One takes a lambda expression, another takes a content type and format string.</span></span>

<span data-ttu-id="2486a-136">[!code-csharp[Main](error-handling/sample/Startup.cs?name=snippet_StatusCodePages)]</span><span class="sxs-lookup"><span data-stu-id="2486a-136">[!code-csharp[Main](error-handling/sample/Startup.cs?name=snippet_StatusCodePages)]</span></span>

```csharp
app.UseStatusCodePages("text/plain", "Status code page, status code: {0}");
```

<span data-ttu-id="2486a-137">리디렉션 확장 메서드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2486a-137">There are also redirect extension methods.</span></span> <span data-ttu-id="2486a-138">하나 302 상태 코드를 클라이언트에 보내고 하나 클라이언트에는 원래 상태 코드를 반환 하지만 또한 리디렉션 URL에 대 한 처리기를 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="2486a-138">One sends a 302 status code to the client, and one returns the original status code to the client but also executes the handler for the redirect URL.</span></span>

<span data-ttu-id="2486a-139">[!code-csharp[Main](error-handling/sample/Startup.cs?name=snippet_StatusCodePagesWithRedirect)]</span><span class="sxs-lookup"><span data-stu-id="2486a-139">[!code-csharp[Main](error-handling/sample/Startup.cs?name=snippet_StatusCodePagesWithRedirect)]</span></span>

```csharp
app.UseStatusCodePagesWithReExecute("/error/{0}");
```

<span data-ttu-id="2486a-140">특정 요청에 대 한 상태 코드 페이지를 사용 하지 않도록 설정 해야 할 경우이 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2486a-140">If you need to disable status code pages for certain requests, you can do so:</span></span>

```csharp
var statusCodePagesFeature = context.Features.Get<IStatusCodePagesFeature>();
if (statusCodePagesFeature != null)
{
  statusCodePagesFeature.Enabled = false;
}
```

## <a name="exception-handling-code"></a><span data-ttu-id="2486a-141">예외 처리 코드</span><span class="sxs-lookup"><span data-stu-id="2486a-141">Exception-handling code</span></span>

<span data-ttu-id="2486a-142">예외 처리 페이지의에서 코드는 예외가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2486a-142">Code in exception handling pages can throw exceptions.</span></span> <span data-ttu-id="2486a-143">종종 완전 한 정적 콘텐츠의 구성 프로덕션 오류 페이지에 대 한는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="2486a-143">It's often a good idea for production error pages to consist of purely static content.</span></span>

<span data-ttu-id="2486a-144">또한 유의 하십시오를 응답에 대 한 헤더를 보낸 응답의 상태 코드를 변경할 수 없습니다 및 예외 페이지 또는 처리기 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2486a-144">Also, be aware that once the headers for a response have been sent, you can't change the response's status code, nor can any exception pages or handlers run.</span></span> <span data-ttu-id="2486a-145">응답을 완료 해야 하거나 연결이 중단 합니다.</span><span class="sxs-lookup"><span data-stu-id="2486a-145">The response must be completed or the connection aborted.</span></span>

## <a name="server-exception-handling"></a><span data-ttu-id="2486a-146">서버 예외 처리</span><span class="sxs-lookup"><span data-stu-id="2486a-146">Server exception handling</span></span>

<span data-ttu-id="2486a-147">응용 프로그램에서 논리를 처리 하는 예외 뿐 아니라는 [서버](servers/index.md) 응용 프로그램을 호스팅하는 몇 가지 예외 처리를 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="2486a-147">In addition to the exception handling logic in your app, the [server](servers/index.md) hosting your app will perform some exception handling.</span></span> <span data-ttu-id="2486a-148">서버에서 헤더를 보낸 전에 된 예외를 catch 하는 경우 본문이 없는 500 내부 서버 오류 응답을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="2486a-148">If the server catches an exception before the headers have been sent it sends a 500 Internal Server Error response with no body.</span></span> <span data-ttu-id="2486a-149">헤더를 보낸 후에 예외를 catch, 연결을 닫습니다.</span><span class="sxs-lookup"><span data-stu-id="2486a-149">If it catches an exception after the headers have been sent, it closes the connection.</span></span> <span data-ttu-id="2486a-150">응용 프로그램에서 처리 되지 않은 요청을 서버에서 처리 되는 서버의 예외에서 발생 하는 예외를 모두 처리 됩니다 및 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="2486a-150">Requests that are not handled by your app will be handled by the server, and any exception that occurs will be handled by the server's exception handling.</span></span> <span data-ttu-id="2486a-151">모든 사용자 지정 오류 페이지나 예외 미들웨어 또는 응용 프로그램에 대해 구성한 필터를 처리 합니다.이 동작은 영향을 주지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2486a-151">Any custom error pages or exception handling middleware or filters you have configured for your app will not affect this behavior.</span></span>

## <a name="startup-exception-handling"></a><span data-ttu-id="2486a-152">시작 예외 처리</span><span class="sxs-lookup"><span data-stu-id="2486a-152">Startup exception handling</span></span>

<span data-ttu-id="2486a-153">호스팅 계층에만 응용 프로그램 시작 시 수행 되는 예외를 처리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2486a-153">Only the hosting layer can handle exceptions that take place during app startup.</span></span> <span data-ttu-id="2486a-154">응용 프로그램 시작 하는 동안 발생 하는 예외 서버 동작에 영향을 줄 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2486a-154">Exceptions that occur during app startup can impact server behavior.</span></span> <span data-ttu-id="2486a-155">예를 들어, 예외가 발생 하면 호출 하기 전에 `KestrelServerOptions.UseHttps`, 호스팅 계층 예외를 catch는 서버를 시작 하 고 비 SSL 포트에서 오류 페이지를 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="2486a-155">For example, if an exception happens before you call `KestrelServerOptions.UseHttps`, the hosting layer catches the exception, starts the server, and displays an error page on the non-SSL port.</span></span> <span data-ttu-id="2486a-156">예외가 발생 하면 해당 줄을 실행 한 후, 오류 페이지 대신 HTTPS를 통해 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2486a-156">If an exception happens after that line executes, the error page is served over HTTPS instead.</span></span>

<span data-ttu-id="2486a-157">할 수 있습니다 [시작 하는 동안 호스트 오류에 대 한 응답으로 동작 하는 방법을 구성](hosting.md#configuring-a-host) 를 사용 하 여 `CaptureStartupErrors` 및 `detailedErrors` 키입니다.</span><span class="sxs-lookup"><span data-stu-id="2486a-157">You can [configure how the host will behave in response to errors during startup](hosting.md#configuring-a-host) using `CaptureStartupErrors` and the `detailedErrors` key.</span></span>

## <a name="aspnet-mvc-error-handling"></a><span data-ttu-id="2486a-158">ASP.NET MVC 오류 처리</span><span class="sxs-lookup"><span data-stu-id="2486a-158">ASP.NET MVC error handling</span></span>

<span data-ttu-id="2486a-159">[MVC](../mvc/index.md) 앱은 예외 필터를 구성 하 고 모델 유효성 검사를 수행 하는 등의 오류를 처리 하기 위한 몇 가지 추가 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2486a-159">[MVC](../mvc/index.md) apps have some additional options for handling errors, such as configuring exception filters and performing model validation.</span></span>

### <a name="exception-filters"></a><span data-ttu-id="2486a-160">예외 필터</span><span class="sxs-lookup"><span data-stu-id="2486a-160">Exception Filters</span></span>

<span data-ttu-id="2486a-161">전역으로 또는 MVC 응용 프로그램에서-컨트롤러 또는 동작 당 기준 예외 필터를 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2486a-161">Exception filters can be configured globally or on a per-controller or per-action basis in an MVC app.</span></span> <span data-ttu-id="2486a-162">이러한 필터 컨트롤러 작업 또는 다른 필터를 실행 하는 동안 발생 하는 처리 되지 않은 예외를 처리 하며 그렇지 않으면 호출 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2486a-162">These filters handle any unhandled exception that occurs during the execution of a controller action or another filter, and are not called otherwise.</span></span> <span data-ttu-id="2486a-163">예외 필터에 대 한 자세한 [필터](../mvc/controllers/filters.md)합니다.</span><span class="sxs-lookup"><span data-stu-id="2486a-163">Learn more about exception filters in [Filters](../mvc/controllers/filters.md).</span></span>

>[!TIP]
> <span data-ttu-id="2486a-164">예외 필터는 MVC 동작 내에서 발생 하는 예외를 트래핑 하는 데 유용 하지만 유연 하 게 오류 미들웨어를 처리 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2486a-164">Exception filters are good for trapping exceptions that occur within MVC actions, but they're not as flexible as error handling middleware.</span></span> <span data-ttu-id="2486a-165">일반적인 경우에 대 한 미들웨어를 선호 하 고만 해야 하는 필터를 사용 하 여 오류 처리 작업을 수행할 *다르게* MVC 작업 선택에 따라 합니다.</span><span class="sxs-lookup"><span data-stu-id="2486a-165">Prefer middleware for the general case, and use filters only where you need to do error handling *differently* based on which MVC action was chosen.</span></span>

### <a name="handling-model-state-errors"></a><span data-ttu-id="2486a-166">처리 모델 상태 오류</span><span class="sxs-lookup"><span data-stu-id="2486a-166">Handling Model State Errors</span></span>

<span data-ttu-id="2486a-167">[유효성 검사 모델](../mvc/models/validation.md) 호출 되는 각 컨트롤러 동작 하기 전에 발생 하 고 검사 해야 하는 작업 메서드의 `ModelState.IsValid` 하 고 적절 하 게 대처 합니다.</span><span class="sxs-lookup"><span data-stu-id="2486a-167">[Model validation](../mvc/models/validation.md) occurs prior to each controller action being invoked, and it is the action method’s responsibility to inspect `ModelState.IsValid` and react appropriately.</span></span>

<span data-ttu-id="2486a-168">일부 응용 프로그램 모델 유효성 검사 오류를 처리 하기 위한 표준 규칙에는 쿼리에서 수행 하도록 선택 하는 [필터](../mvc/controllers/filters.md) 이러한 정책을 구현 하는 적절 한 위치 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2486a-168">Some apps will choose to follow a standard convention for dealing with model validation errors, in which case a [filter](../mvc/controllers/filters.md) may be an appropriate place to implement such a policy.</span></span> <span data-ttu-id="2486a-169">잘못 된 모델 상태와 작업을 수행할 때 동작 하는 방법을 테스트 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2486a-169">You should test how your actions behave with invalid model states.</span></span> <span data-ttu-id="2486a-170">자세한 내용을 알아보세요 [컨트롤러 논리를 테스트](../mvc/controllers/testing.md)합니다.</span><span class="sxs-lookup"><span data-stu-id="2486a-170">Learn more in [Testing controller logic](../mvc/controllers/testing.md).</span></span>



