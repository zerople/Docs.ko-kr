---
title: "ASP.NET Core 미들웨어"
author: rick-anderson
description: "ASP.NET Core 미들웨어 및 요청 파이프라인에 알아봅니다."
keywords: "ASP.NET Core, 미들웨어, 파이프라인, 대리자"
ms.author: riande
manager: wpickett
ms.date: 08/14/2017
ms.topic: article
ms.assetid: db9a86ab-46c2-40e0-baed-86e38c16af1f
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/middleware
ms.openlocfilehash: 3cd15c7e8ed4956e1d451f3bd5935fc175999d1f
ms.sourcegitcommit: 732cd2684246e49e796836596643a8d37e20c46d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2017
---
# <a name="aspnet-core-middleware-fundamentals"></a><span data-ttu-id="230b1-104">ASP.NET Core 미들웨어 기본 사항</span><span class="sxs-lookup"><span data-stu-id="230b1-104">ASP.NET Core Middleware Fundamentals</span></span>

<a name=fundamentals-middleware></a>

<span data-ttu-id="230b1-105">여 [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="230b1-105">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="230b1-106">[보거나 다운로드 샘플 코드](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/middleware/sample) ([다운로드 하는 방법을](xref:tutorials/index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="230b1-106">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/middleware/sample) ([how to download](xref:tutorials/index#how-to-download-a-sample))</span></span>

## <a name="what-is-middleware"></a><span data-ttu-id="230b1-107">미들웨어는 무엇입니까</span><span class="sxs-lookup"><span data-stu-id="230b1-107">What is middleware</span></span>

<span data-ttu-id="230b1-108">미들웨어는 소프트웨어 요청 및 응답을 처리 하는 응용 프로그램 파이프라인에 넣을입니다.</span><span class="sxs-lookup"><span data-stu-id="230b1-108">Middleware is software that is assembled into an application pipeline to handle requests and responses.</span></span> <span data-ttu-id="230b1-109">각 구성 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="230b1-109">Each component:</span></span>

* <span data-ttu-id="230b1-110">다음 구성 요소는 파이프라인에 요청을 통과 것인지를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="230b1-110">Chooses whether to pass the request to the next component in the pipeline.</span></span>
* <span data-ttu-id="230b1-111">파이프라인의 다음 구성 요소로 호출 된 후 전과 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="230b1-111">Can perform work before and after the next component in the pipeline is invoked.</span></span> 

<span data-ttu-id="230b1-112">요청 대리자 요청 파이프라인을 구축 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="230b1-112">Request delegates are used to build the request pipeline.</span></span> <span data-ttu-id="230b1-113">요청 대리자는 각 HTTP 요청을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="230b1-113">The request delegates handle each HTTP request.</span></span>

<span data-ttu-id="230b1-114">대리자를 사용 하 여 구성 된 요청 [실행](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.runextensions), [지도](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.mapextensions), 및 [사용](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.useextensions) 확장 메서드입니다.</span><span class="sxs-lookup"><span data-stu-id="230b1-114">Request delegates are configured using [Run](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.runextensions), [Map](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.mapextensions), and [Use](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.useextensions) extension methods.</span></span> <span data-ttu-id="230b1-115">개별 요청 대리자 (인라인 미들웨어 라고 함)는 무명 메서드의 인라인으로 지정된 될 수 또는 다시 사용할 수 있는 클래스에서 정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="230b1-115">An individual request delegate can be specified in-line as an anonymous method (called in-line middleware), or it can be defined in a reusable class.</span></span> <span data-ttu-id="230b1-116">이러한 다시 사용할 수 있는 클래스와 인라인 무명 메서드는 *미들웨어*, 또는 *미들웨어 구성 요소*합니다.</span><span class="sxs-lookup"><span data-stu-id="230b1-116">These reusable classes and in-line anonymous methods are *middleware*, or *middleware components*.</span></span> <span data-ttu-id="230b1-117">요청 파이프라인의 각 미들웨어 구성 요소는 파이프라인의 다음 구성 요소를 호출 하거나 해당 하는 경우 체인 단락 (short-circuiting) 하는 일을 담당 합니다.</span><span class="sxs-lookup"><span data-stu-id="230b1-117">Each middleware component in the request pipeline is responsible for invoking the next component in the pipeline, or short-circuiting the chain if appropriate.</span></span>

<span data-ttu-id="230b1-118">[미들웨어 마이그레이션 HTTP 모듈](../migration/http-modules.md) ASP.NET Core의 요청 파이프라인 및 이전 버전 간의 차이 설명 하 고 많은 미들웨어 샘플을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="230b1-118">[Migrating HTTP Modules to Middleware](../migration/http-modules.md) explains the difference between request pipelines in ASP.NET Core and the previous versions and provides more middleware samples.</span></span>

## <a name="creating-a-middleware-pipeline-with-iapplicationbuilder"></a><span data-ttu-id="230b1-119">IApplicationBuilder 미들웨어 파이프라인 만들기</span><span class="sxs-lookup"><span data-stu-id="230b1-119">Creating a middleware pipeline with IApplicationBuilder</span></span>

<span data-ttu-id="230b1-120">ASP.NET Core 요청 파이프라인의 요청 대리자, 즉,이 다이어그램 (실행 다음과 검은색 화살표의 스레드)와 같이 호출 시퀀스로 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="230b1-120">The ASP.NET Core request pipeline consists of a sequence of request delegates, called one after the other, as this diagram shows (the thread of execution follows the black arrows):</span></span>

![요청 처리 패턴 도착, 세 가지 middlewares 및 응용 프로그램이 응답을 통해 처리 요청을 표시 합니다.](middleware/_static/request-delegate-pipeline.png)

<span data-ttu-id="230b1-124">각 대리자 이전 및 다음 대리자 이후 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="230b1-124">Each delegate can perform operations before and after the next delegate.</span></span> <span data-ttu-id="230b1-125">또한 대리자 요청 파이프라인을 단락 (short-circuiting) 호출 하는 다음 대리자에는 요청을 통과 하지 결정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="230b1-125">A delegate can also decide to not pass a request to the next delegate, which is called short-circuiting the request pipeline.</span></span> <span data-ttu-id="230b1-126">불필요 한 작업을 방지 하기 때문에 단락 (short-circuiting)은 바람직한 경우가 많습니다.</span><span class="sxs-lookup"><span data-stu-id="230b1-126">Short-circuiting is often desirable because it avoids unnecessary work.</span></span> <span data-ttu-id="230b1-127">예를 들어 정적 파일 미들웨어 정적 파일에 대 한 요청을 반환 하 고 나머지 파이프라인 단락 (short-circuit) 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="230b1-127">For example, the static file middleware can return a request for a static file and short-circuit the rest of the pipeline.</span></span> <span data-ttu-id="230b1-128">예외 처리 대리자 파이프라인의 이후 단계에서 발생 하는 예외를 catch 할 수 있으므로 파이프라인, 초기에 호출 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="230b1-128">Exception-handling delegates need to be called early in the pipeline, so they can catch exceptions that occur in later stages of the pipeline.</span></span>

<span data-ttu-id="230b1-129">가장 간단한 가능한 ASP.NET Core 응용 프로그램은 모든 요청을 처리 하는 단일 요청 대리자를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="230b1-129">The simplest possible ASP.NET Core app sets up a single request delegate that handles all requests.</span></span> <span data-ttu-id="230b1-130">이 경우 실제 요청 파이프라인에 포함 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="230b1-130">This case doesn't include an actual request pipeline.</span></span> <span data-ttu-id="230b1-131">대신, 단일 익명 함수는 모든 HTTP 요청에 대 한 응답으로 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="230b1-131">Instead, a single anonymous function is called in response to every HTTP request.</span></span>

[!code-csharp[Main](middleware/sample/Middleware/Startup.cs)]

<span data-ttu-id="230b1-132">첫 번째 [응용 프로그램입니다. 실행](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.runextensions) 대리자 파이프라인을 종료 합니다.</span><span class="sxs-lookup"><span data-stu-id="230b1-132">The first [app.Run](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.runextensions) delegate terminates the pipeline.</span></span>

<span data-ttu-id="230b1-133">연결할 수 있습니다. 여러 요청 대리자와 함께 [응용 프로그램입니다. 사용 하 여](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.useextensions)합니다.</span><span class="sxs-lookup"><span data-stu-id="230b1-133">You can chain multiple request delegates together with [app.Use](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.useextensions).</span></span> <span data-ttu-id="230b1-134">`next` 매개 변수는 파이프라인의 다음 대리자를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="230b1-134">The `next` parameter represents the next delegate in the pipeline.</span></span> <span data-ttu-id="230b1-135">(하 여 파이프라인을 단락 수 한다는 기억 *하지* 호출는 *다음* 매개 변수.) 일반적으로 작업을 수행할 수는 다음 대리자 앞뒤 모두이 예제에서 보여 주듯이:</span><span class="sxs-lookup"><span data-stu-id="230b1-135">(Remember that you can short-circuit the pipeline by *not* calling the *next* parameter.) You can typically perform actions both before and after the next delegate, as this example demonstrates:</span></span>

[!code-csharp[Main](middleware/sample/Chain/Startup.cs?name=snippet1)]

>[!WARNING]
> <span data-ttu-id="230b1-136">호출 하지 마십시오 `next.Invoke` 클라이언트에 응답을 보낸 후 합니다.</span><span class="sxs-lookup"><span data-stu-id="230b1-136">Do not call `next.Invoke` after the response has been sent to the client.</span></span> <span data-ttu-id="230b1-137">변경 내용 `HttpResponse` 응답이 시작 된 후 예외가 throw 됩니다.</span><span class="sxs-lookup"><span data-stu-id="230b1-137">Changes to `HttpResponse` after the response has started will throw an exception.</span></span> <span data-ttu-id="230b1-138">예를 들어 헤더, 상태 코드 등을 설정 하는 등의 변경에는 예외가 throw 됩니다.</span><span class="sxs-lookup"><span data-stu-id="230b1-138">For example, changes such as setting headers, status code, etc,  will throw an exception.</span></span> <span data-ttu-id="230b1-139">호출한 후 응답 본문에 쓰기 `next`:</span><span class="sxs-lookup"><span data-stu-id="230b1-139">Writing to the response body after calling `next`:</span></span>
> - <span data-ttu-id="230b1-140">프로토콜 위반이 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="230b1-140">May cause a protocol violation.</span></span> <span data-ttu-id="230b1-141">예를 들어 쓰기는 언급 된 것 보다 많은 `content-length`합니다.</span><span class="sxs-lookup"><span data-stu-id="230b1-141">For example, writing more than the stated `content-length`.</span></span>
> - <span data-ttu-id="230b1-142">본문 형식을 손상 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="230b1-142">May corrupt the body format.</span></span> <span data-ttu-id="230b1-143">예를 들어 HTML 바닥글 CSS 파일에 기록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="230b1-143">For example, writing an HTML footer to a CSS file.</span></span>
>
> <span data-ttu-id="230b1-144">[HttpResponse.HasStarted](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.http.features.httpresponsefeature#Microsoft_AspNetCore_Http_Features_HttpResponseFeature_HasStarted) 경우 헤더를 보낸 및/또는 본문에 기록 되었는지 나타내는에 유용한 정보를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="230b1-144">[HttpResponse.HasStarted](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.http.features.httpresponsefeature#Microsoft_AspNetCore_Http_Features_HttpResponseFeature_HasStarted) is a useful hint to indicate if headers have been sent and/or the body has been written to.</span></span>

## <a name="ordering"></a><span data-ttu-id="230b1-145">정렬</span><span class="sxs-lookup"><span data-stu-id="230b1-145">Ordering</span></span>

<span data-ttu-id="230b1-146">미들웨어 구성 요소에 추가 되는 순서는 `Configure` 요청에서 호출 되는 순서와 역순은 응답에 대 한 메서드를 정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="230b1-146">The order that middleware components are added in the `Configure` method defines the order in which they are invoked on requests, and the reverse order for the response.</span></span> <span data-ttu-id="230b1-147">이 순서 지정 하는 것은 보안, 성능 및 기능에 대 한 중요 합니다.</span><span class="sxs-lookup"><span data-stu-id="230b1-147">This ordering is critical for security, performance, and functionality.</span></span>

<span data-ttu-id="230b1-148">Configure 메서드 (아래 참조) 다음 미들웨어 구성 요소를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="230b1-148">The Configure method (shown below) adds the following middleware components:</span></span>

1. <span data-ttu-id="230b1-149">예외/오류 처리</span><span class="sxs-lookup"><span data-stu-id="230b1-149">Exception/error handling</span></span>
2. <span data-ttu-id="230b1-150">정적 파일 서버</span><span class="sxs-lookup"><span data-stu-id="230b1-150">Static file server</span></span>
3. <span data-ttu-id="230b1-151">인증</span><span class="sxs-lookup"><span data-stu-id="230b1-151">Authentication</span></span>
4. <span data-ttu-id="230b1-152">MVC</span><span class="sxs-lookup"><span data-stu-id="230b1-152">MVC</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseExceptionHandler("/Home/Error"); // Call first to catch exceptions
                                            // thrown in the following middleware.

    app.UseStaticFiles();                   // Return static files and end pipeline.

    app.UseIdentity();                     // Authenticate before you access
                                           // secure resources.

    app.UseMvcWithDefaultRoute();          // Add MVC to the request pipeline.
}
```

<span data-ttu-id="230b1-153">위의 코드에서 `UseExceptionHandler` 파이프라인에 추가 하는 첫 번째 미들웨어 구성 요소는-따라서 대 한 후속 호출에서 발생 하는 모든 예외를 catch 합니다.</span><span class="sxs-lookup"><span data-stu-id="230b1-153">In the code above, `UseExceptionHandler` is the first middleware component added to the pipeline—therefore, it catches any exceptions that occur in later calls.</span></span>

<span data-ttu-id="230b1-154">요청을 처리 하 고 나머지 구성 요소를 통과 하지 않고 단락 (short-circuit) 수 있도록 정적 파일 미들웨어 파이프라인 초기에 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="230b1-154">The static file middleware is called early in the pipeline so it can handle requests and short-circuit without going through the remaining components.</span></span> <span data-ttu-id="230b1-155">정적 파일 미들웨어 제공 **없는** 권한 부여 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="230b1-155">The static file middleware provides **no** authorization checks.</span></span> <span data-ttu-id="230b1-156">모든 파일에서 제공 비롯 *wwwroot*, 공개적으로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="230b1-156">Any files served by it, including those under *wwwroot*, are publicly available.</span></span> <span data-ttu-id="230b1-157">참조 [정적 파일 작업](xref:fundamentals/static-files) 정적 파일을 보호 하는 방법에 대 한 합니다.</span><span class="sxs-lookup"><span data-stu-id="230b1-157">See [Working with static files](xref:fundamentals/static-files) for an approach to secure static files.</span></span>

<span data-ttu-id="230b1-158">요청이 정적 파일 미들웨어에서 처리 되지 않으면 Identity 미들웨어 전달 됩니다 (`app.UseIdentity`), 인증을 수행 하는 합니다.</span><span class="sxs-lookup"><span data-stu-id="230b1-158">If the request is not handled by the static file middleware, it's passed on to the Identity middleware (`app.UseIdentity`), which performs authentication.</span></span> <span data-ttu-id="230b1-159">Identity 인증 되지 않은 요청을 단락 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="230b1-159">Identity does not short-circuit unauthenticated requests.</span></span> <span data-ttu-id="230b1-160">Identity 요청을 인증 하는 있지만 권한 부여 (및 거부) MVC가 특정 컨트롤러와 작업을 선택한 후에 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="230b1-160">Although Identity authenticates requests,  authorization (and rejection) occurs only after MVC selects a specific controller and action.</span></span>

<span data-ttu-id="230b1-161">다음 예제는 정적 파일에 대 한 요청 응답 압축 미들웨어 하기 전에 정적 파일 미들웨어에서 처리 되는 곳 순서 미들웨어입니다.</span><span class="sxs-lookup"><span data-stu-id="230b1-161">The following example demonstrates a middleware ordering where requests for static files are handled by the static file middleware before the response compression middleware.</span></span> <span data-ttu-id="230b1-162">정적 파일 미들웨어의이 순서는 압축 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="230b1-162">Static files are not compressed with this ordering of the middleware.</span></span> <span data-ttu-id="230b1-163">MVC 응답을 [UseMvcWithDefaultRoute](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.mvcapplicationbuilderextensions#Microsoft_AspNetCore_Builder_MvcApplicationBuilderExtensions_UseMvcWithDefaultRoute_Microsoft_AspNetCore_Builder_IApplicationBuilder_) 압축할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="230b1-163">The MVC responses from [UseMvcWithDefaultRoute](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.mvcapplicationbuilderextensions#Microsoft_AspNetCore_Builder_MvcApplicationBuilderExtensions_UseMvcWithDefaultRoute_Microsoft_AspNetCore_Builder_IApplicationBuilder_) can be compressed.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseStaticFiles();         // Static files not compressed
                                  // by middleware.
    app.UseResponseCompression();
    app.UseMvcWithDefaultRoute();
}
```

<a name=middleware-run-map-use></a>

### <a name="use-run-and-map"></a><span data-ttu-id="230b1-164">사용 하 여, 실행 및 매핑</span><span class="sxs-lookup"><span data-stu-id="230b1-164">Use, Run, and Map</span></span>

<span data-ttu-id="230b1-165">사용 하 여 HTTP 파이프라인을 구성 `Use`, `Run`, 및 `Map`합니다.</span><span class="sxs-lookup"><span data-stu-id="230b1-165">You configure the HTTP pipeline using `Use`, `Run`, and `Map`.</span></span> <span data-ttu-id="230b1-166">`Use` 메서드 수 단락 (short-circuit) 파이프라인 (즉, 호출 하지 않는 경우는 `next` 요청 대리자).</span><span class="sxs-lookup"><span data-stu-id="230b1-166">The `Use` method can short-circuit the pipeline (that is, if it does not call a `next` request delegate).</span></span> <span data-ttu-id="230b1-167">`Run`규칙은 일부 미들웨어 구성 요소 노출 될 수 있습니다 및 `Run[Middleware]` 파이프라인의 끝에 실행 되는 메서드.</span><span class="sxs-lookup"><span data-stu-id="230b1-167">`Run` is a convention, and some middleware components may expose `Run[Middleware]` methods that run at the end of the pipeline.</span></span>

<span data-ttu-id="230b1-168">`Map*`확장이는 파이프라인 분기에 규칙으로 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="230b1-168">`Map*` extensions are used as a convention for branching the pipeline.</span></span> <span data-ttu-id="230b1-169">[지도](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.mapextensions) 지정된 된 요청 경로 일치를 기반으로 요청 파이프라인 분기 합니다.</span><span class="sxs-lookup"><span data-stu-id="230b1-169">[Map](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.mapextensions) branches the request pipeline based on matches of the given request path.</span></span> <span data-ttu-id="230b1-170">요청 경로 지정 된 경로로 시작 되 면 분기가 실행 되 고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="230b1-170">If the request path starts with the given path, the branch is executed.</span></span>

[!code-csharp[Main](middleware/sample/Chain/StartupMap.cs?name=snippet1)]

<span data-ttu-id="230b1-171">다음 표에서 요청 및 응답을 보여 줍니다. `http://localhost:1234` 앞의 코드를 사용 하 여:</span><span class="sxs-lookup"><span data-stu-id="230b1-171">The following table shows the requests and responses from `http://localhost:1234` using the previous code:</span></span>

| <span data-ttu-id="230b1-172">요청</span><span class="sxs-lookup"><span data-stu-id="230b1-172">Request</span></span> | <span data-ttu-id="230b1-173">응답</span><span class="sxs-lookup"><span data-stu-id="230b1-173">Response</span></span> |
| --- | --- |
| <span data-ttu-id="230b1-174">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="230b1-174">localhost:1234</span></span> | <span data-ttu-id="230b1-175">맵 비 대리자에서 번호입니다.</span><span class="sxs-lookup"><span data-stu-id="230b1-175">Hello from non-Map delegate.</span></span>  |
| <span data-ttu-id="230b1-176">localhost:1234 / map1</span><span class="sxs-lookup"><span data-stu-id="230b1-176">localhost:1234/map1</span></span> | <span data-ttu-id="230b1-177">맵 테스트 1</span><span class="sxs-lookup"><span data-stu-id="230b1-177">Map Test 1</span></span> |
| <span data-ttu-id="230b1-178">localhost:1234 / map2</span><span class="sxs-lookup"><span data-stu-id="230b1-178">localhost:1234/map2</span></span> | <span data-ttu-id="230b1-179">맵 테스트 2</span><span class="sxs-lookup"><span data-stu-id="230b1-179">Map Test 2</span></span> |
| <span data-ttu-id="230b1-180">localhost:1234 / map3</span><span class="sxs-lookup"><span data-stu-id="230b1-180">localhost:1234/map3</span></span> | <span data-ttu-id="230b1-181">맵 비 대리자에서 번호입니다.</span><span class="sxs-lookup"><span data-stu-id="230b1-181">Hello from non-Map delegate.</span></span>  |

<span data-ttu-id="230b1-182">때 `Map` 는 사용 하는 일치 하는 경로 세그먼트에서 제거 됩니다 `HttpRequest.Path` 에 추가 되 고 `HttpRequest.PathBase` 각 요청에 대 한 합니다.</span><span class="sxs-lookup"><span data-stu-id="230b1-182">When `Map` is used, the matched path segment(s) are removed from `HttpRequest.Path` and appended to `HttpRequest.PathBase` for each request.</span></span>

<span data-ttu-id="230b1-183">[MapWhen](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.mapwhenextensions) 지정된 된 조건자의 결과 기반으로 요청 파이프라인 분기 합니다.</span><span class="sxs-lookup"><span data-stu-id="230b1-183">[MapWhen](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.mapwhenextensions) branches the request pipeline based on the result of the given predicate.</span></span> <span data-ttu-id="230b1-184">형식 조건자를 임의의 조건자 `Func<HttpContext, bool>` 요청 파이프라인의 새 분기에 매핑하기 위해 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="230b1-184">Any predicate of type `Func<HttpContext, bool>` can be used to map requests to a new branch of the pipeline.</span></span> <span data-ttu-id="230b1-185">쿼리 문자열 변수의 현재 상태를 찾는 데 사용 되는 조건자 다음 예에서 `branch`:</span><span class="sxs-lookup"><span data-stu-id="230b1-185">In the following example, a predicate is used to detect the presence of a query string variable `branch`:</span></span>

[!code-csharp[Main](middleware/sample/Chain/StartupMapWhen.cs?name=snippet1)]

<span data-ttu-id="230b1-186">다음 표에서 요청 및 응답을 보여 줍니다. `http://localhost:1234` 앞의 코드를 사용 하 여:</span><span class="sxs-lookup"><span data-stu-id="230b1-186">The following table shows the requests and responses from `http://localhost:1234` using the previous code:</span></span>

| <span data-ttu-id="230b1-187">요청</span><span class="sxs-lookup"><span data-stu-id="230b1-187">Request</span></span> | <span data-ttu-id="230b1-188">응답</span><span class="sxs-lookup"><span data-stu-id="230b1-188">Response</span></span> |
| --- | --- |
| <span data-ttu-id="230b1-189">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="230b1-189">localhost:1234</span></span> | <span data-ttu-id="230b1-190">맵 비 대리자에서 번호입니다.</span><span class="sxs-lookup"><span data-stu-id="230b1-190">Hello from non-Map delegate.</span></span>  |
| <span data-ttu-id="230b1-191">localhost:1234 /? 분기 마스터 =</span><span class="sxs-lookup"><span data-stu-id="230b1-191">localhost:1234/?branch=master</span></span> | <span data-ttu-id="230b1-192">분기 사용 되는 마스터 =</span><span class="sxs-lookup"><span data-stu-id="230b1-192">Branch used = master</span></span>|

<span data-ttu-id="230b1-193">`Map`예를 들어 중첩 지원:</span><span class="sxs-lookup"><span data-stu-id="230b1-193">`Map` supports nesting, for example:</span></span>

```csharp
app.Map("/level1", level1App => {
       level1App.Map("/level2a", level2AApp => {
           // "/level1/level2a"
           //...
       });
       level1App.Map("/level2b", level2BApp => {
           // "/level1/level2b"
           //...
       });
   });
   ```

<span data-ttu-id="230b1-194">`Map`또한 일치 시킬 수 여러 세그먼트를 한 번에 예:</span><span class="sxs-lookup"><span data-stu-id="230b1-194">`Map` can also match multiple segments at once, for example:</span></span>

 ```csharp
app.Map("/level1/level2", HandleMultiSeg);
```

## <a name="built-in-middleware"></a><span data-ttu-id="230b1-195">기본 제공 미들웨어</span><span class="sxs-lookup"><span data-stu-id="230b1-195">Built-in middleware</span></span>

<span data-ttu-id="230b1-196">ASP.NET Core 다음 미들웨어 구성 요소와 함께 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="230b1-196">ASP.NET Core ships with the following middleware components:</span></span>

| <span data-ttu-id="230b1-197">미들웨어</span><span class="sxs-lookup"><span data-stu-id="230b1-197">Middleware</span></span> | <span data-ttu-id="230b1-198">설명</span><span class="sxs-lookup"><span data-stu-id="230b1-198">Description</span></span> |
| ----- | ------- |
| [<span data-ttu-id="230b1-199">인증</span><span class="sxs-lookup"><span data-stu-id="230b1-199">Authentication</span></span>](xref:security/authentication/identity) | <span data-ttu-id="230b1-200">인증 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="230b1-200">Provides authentication support.</span></span> |
| [<span data-ttu-id="230b1-201">CORS</span><span class="sxs-lookup"><span data-stu-id="230b1-201">CORS</span></span>](xref:security/cors) | <span data-ttu-id="230b1-202">크로스-원본 자원 공유를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="230b1-202">Configures Cross-Origin Resource Sharing.</span></span> |
| [<span data-ttu-id="230b1-203">응답 캐싱</span><span class="sxs-lookup"><span data-stu-id="230b1-203">Response Caching</span></span>](xref:performance/caching/middleware) | <span data-ttu-id="230b1-204">응답을 캐시에 대 한 지원을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="230b1-204">Provides support for caching responses.</span></span> |
| [<span data-ttu-id="230b1-205">응답 압축</span><span class="sxs-lookup"><span data-stu-id="230b1-205">Response Compression</span></span>](xref:performance/response-compression) | <span data-ttu-id="230b1-206">응답을 압축 하는 것에 대 한 지원을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="230b1-206">Provides support for compressing responses.</span></span> |
| [<span data-ttu-id="230b1-207">라우팅</span><span class="sxs-lookup"><span data-stu-id="230b1-207">Routing</span></span>](xref:fundamentals/routing) | <span data-ttu-id="230b1-208">정의 하 고 요청 경로 제한 합니다.</span><span class="sxs-lookup"><span data-stu-id="230b1-208">Defines and constrains request routes.</span></span> |
| [<span data-ttu-id="230b1-209">세션</span><span class="sxs-lookup"><span data-stu-id="230b1-209">Session</span></span>](xref:fundamentals/app-state) | <span data-ttu-id="230b1-210">사용자 세션을 관리 하기 위한 지원을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="230b1-210">Provides support for managing user sessions.</span></span> |
| [<span data-ttu-id="230b1-211">정적 파일</span><span class="sxs-lookup"><span data-stu-id="230b1-211">Static Files</span></span>](xref:fundamentals/static-files) | <span data-ttu-id="230b1-212">정적 파일 및 디렉터리 검색을 처리 하기 위한 지원을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="230b1-212">Provides support for serving static files and directory browsing.</span></span> |
| [<span data-ttu-id="230b1-213">URL 재작성 미들웨어</span><span class="sxs-lookup"><span data-stu-id="230b1-213">URL Rewriting Middleware</span></span>](xref:fundamentals/url-rewriting) | <span data-ttu-id="230b1-214">Url 다시 쓰기 및 요청 리디렉션에 대 한 지원을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="230b1-214">Provides support for rewriting URLs and redirecting requests.</span></span> |

<a name=middleware-writing-middleware></a>

## <a name="writing-middleware"></a><span data-ttu-id="230b1-215">쓰기 미들웨어</span><span class="sxs-lookup"><span data-stu-id="230b1-215">Writing middleware</span></span>

<span data-ttu-id="230b1-216">일반적으로 미들웨어 클래스에 캡슐화 하 고 확장 메서드로 노출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="230b1-216">Middleware is generally encapsulated in a class and exposed with an extension method.</span></span> <span data-ttu-id="230b1-217">쿼리 문자열에서 현재 요청에 대 한 문화권을 설정 하는 다음 미들웨어를 고려 합니다.</span><span class="sxs-lookup"><span data-stu-id="230b1-217">Consider the following middleware, which sets the culture for the current request from the query string:</span></span>

[!code-csharp[Main](middleware/sample/Culture/StartupCulture.cs?name=snippet1)]

<span data-ttu-id="230b1-218">참고: 위의 샘플 코드 미들웨어 구성 요소 만들기를 보여 주기 위해 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="230b1-218">Note: The sample code above is used to demonstrate creating a middleware component.</span></span> <span data-ttu-id="230b1-219">참조 [ 전역화 및 지역화](xref:fundamentals/localization) ASP.NET Core 기본 제공 지역화 지원에 대 한 합니다.</span><span class="sxs-lookup"><span data-stu-id="230b1-219">See [ Globalization and localization](xref:fundamentals/localization) for ASP.NET Core's built-in localization support.</span></span>

<span data-ttu-id="230b1-220">예를 들어 culture에 전달 하 여 미들웨어를 테스트할 수 있습니다 `http://localhost:7997/?culture=no`합니다.</span><span class="sxs-lookup"><span data-stu-id="230b1-220">You can test the middleware by passing in the culture, for example `http://localhost:7997/?culture=no`.</span></span>

<span data-ttu-id="230b1-221">다음 코드는 미들웨어 대리자 클래스에 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="230b1-221">The following code moves the middleware delegate to a class:</span></span>

[!code-csharp[Main](middleware/sample/Culture/RequestCultureMiddleware.cs)]

<span data-ttu-id="230b1-222">다음 확장 메서드는 미들웨어를 통해 노출 [IApplicationBuilder](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.iapplicationbuilder):</span><span class="sxs-lookup"><span data-stu-id="230b1-222">The following extension method exposes the middleware through [IApplicationBuilder](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.iapplicationbuilder):</span></span>

[!code-csharp[Main](middleware/sample/Culture/RequestCultureMiddlewareExtensions.cs)]

<span data-ttu-id="230b1-223">다음 코드에서 미들웨어를 호출 `Configure`:</span><span class="sxs-lookup"><span data-stu-id="230b1-223">The following code calls the middleware from `Configure`:</span></span>

[!code-csharp[Main](middleware/sample/Culture/Startup.cs?name=snippet1&highlight=5)]

<span data-ttu-id="230b1-224">미들웨어 따라야는 [명시적 종속성 원칙](http://deviq.com/explicit-dependencies-principle/) 생성자에서 해당 종속성을 노출 하 여 합니다.</span><span class="sxs-lookup"><span data-stu-id="230b1-224">Middleware should follow the [Explicit Dependencies Principle](http://deviq.com/explicit-dependencies-principle/) by exposing its dependencies in its constructor.</span></span> <span data-ttu-id="230b1-225">미들웨어 당 한 번 생성 *응용 프로그램 수명*합니다.</span><span class="sxs-lookup"><span data-stu-id="230b1-225">Middleware is constructed once per *application lifetime*.</span></span> <span data-ttu-id="230b1-226">참조 *요청별 종속성* 경우에는 아래 서비스 요청 내의 미들웨어를 공유 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="230b1-226">See *Per-request dependencies* below if you need to share services with middleware within a request.</span></span>

<span data-ttu-id="230b1-227">미들웨어 구성 요소 생성자 매개 변수를 통해 종속성 주입에서 해당 종속성을 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="230b1-227">Middleware components can resolve their dependencies from dependency injection through constructor parameters.</span></span> <span data-ttu-id="230b1-228">[`UseMiddleware<T>`](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.usemiddlewareextensions#methods_summary)추가 매개 변수를 직접 적용할 수도.</span><span class="sxs-lookup"><span data-stu-id="230b1-228">[`UseMiddleware<T>`](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.usemiddlewareextensions#methods_summary) can also accept additional parameters directly.</span></span>

### <a name="per-request-dependencies"></a><span data-ttu-id="230b1-229">요청별 종속성</span><span class="sxs-lookup"><span data-stu-id="230b1-229">Per-request dependencies</span></span>

<span data-ttu-id="230b1-230">미들웨어 하지 요청별, 응용 프로그램 시작 시 구성 된 때문에 *범위* 미들웨어 생성자에 의해 사용 되는 수명을 서비스 요청 될 때마다 다른 종속성 주입 종류와 공유 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="230b1-230">Because middleware is constructed at app startup, not per-request, *scoped* lifetime services used by middleware constructors are not  shared with other dependency-injected types during each request.</span></span> <span data-ttu-id="230b1-231">공유 해야 하는 경우는 *범위* 미들웨어와 다른 형식 간의 서비스, 이러한 서비스를 추가 `Invoke` 메서드의 서명입니다.</span><span class="sxs-lookup"><span data-stu-id="230b1-231">If you must share a *scoped* service between your middleware and other types, add these services to the `Invoke` method's signature.</span></span> <span data-ttu-id="230b1-232">`Invoke` 메서드 종속성 주입으로 채우는 추가 매개 변수를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="230b1-232">The `Invoke` method can accept additional parameters that are populated by dependency injection.</span></span> <span data-ttu-id="230b1-233">예:</span><span class="sxs-lookup"><span data-stu-id="230b1-233">For example:</span></span>

```c#
public class MyMiddleware
{
    private readonly RequestDelegate _next;

    public MyMiddleware(RequestDelegate next)
    {
        _next = next;
    }

    public async Task Invoke(HttpContext httpContext, IMyScopedService svc)
    {
        svc.MyProperty = 1000;
        await _next(httpContext);
    }
}
```

## <a name="resources"></a><span data-ttu-id="230b1-234">리소스</span><span class="sxs-lookup"><span data-stu-id="230b1-234">Resources</span></span>

* [<span data-ttu-id="230b1-235">이 문서에 사용 되는 샘플 코드</span><span class="sxs-lookup"><span data-stu-id="230b1-235">Sample code used in this doc</span></span>](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/middleware/sample)
* [<span data-ttu-id="230b1-236">HTTP 모듈을 미들웨어로 마이그레이션</span><span class="sxs-lookup"><span data-stu-id="230b1-236">Migrating HTTP Modules to Middleware</span></span>](../migration/http-modules.md)
* [<span data-ttu-id="230b1-237">응용 프로그램 시작</span><span class="sxs-lookup"><span data-stu-id="230b1-237">Application Startup</span></span>](startup.md)
* [<span data-ttu-id="230b1-238">기능 요청</span><span class="sxs-lookup"><span data-stu-id="230b1-238">Request Features</span></span>](request-features.md)
