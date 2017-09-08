---
title: "HTTP 처리기 및 ASP.NET Core 미들웨어 모듈을 마이그레이션하는 방법"
author: rick-anderson
description: 
keywords: ASP.NET Core
ms.author: tdykstra
manager: wpickett
ms.date: 12/07/2016
ms.topic: article
ms.assetid: 9c826a76-fbd2-46b5-978d-6ca6df53531a
ms.technology: aspnet
ms.prod: asp.net-core
uid: migration/http-modules
ms.openlocfilehash: f99c2751138ac789e7105ff256ce7254e280463e
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/11/2017
---
# <a name="migrating-http-handlers-and-modules-to-aspnet-core-middleware"></a><span data-ttu-id="e8ee6-103">HTTP 처리기 및 ASP.NET Core 미들웨어 모듈을 마이그레이션하는 방법</span><span class="sxs-lookup"><span data-stu-id="e8ee6-103">Migrating HTTP handlers and modules to ASP.NET Core middleware</span></span> 

<span data-ttu-id="e8ee6-104">으로 [Matt Perdeck](https://www.linkedin.com/in/mattperdeck)</span><span class="sxs-lookup"><span data-stu-id="e8ee6-104">By [Matt Perdeck](https://www.linkedin.com/in/mattperdeck)</span></span>

<span data-ttu-id="e8ee6-105">이 문서에서는 기존 ASP.NET 마이그레이션하는 방법을 보여 줍니다. [HTTP 모듈 및 처리기](https://msdn.microsoft.com/library/bb398986.aspx) ASP.NET Core로 [미들웨어](../fundamentals/middleware.md)합니다.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-105">This article shows how to migrate existing ASP.NET [HTTP modules and handlers](https://msdn.microsoft.com/library/bb398986.aspx) to ASP.NET Core [middleware](../fundamentals/middleware.md).</span></span>

## <a name="modules-and-handlers-revisited"></a><span data-ttu-id="e8ee6-106">모듈 및 revisited 처리기</span><span class="sxs-lookup"><span data-stu-id="e8ee6-106">Modules and handlers revisited</span></span>

<span data-ttu-id="e8ee6-107">ASP.NET Core 미들웨어를 계속 하기 전에 먼저 요약해 보면 HTTP 모듈 및 처리기의 작동 방식:</span><span class="sxs-lookup"><span data-stu-id="e8ee6-107">Before proceeding to ASP.NET Core middleware, let's first recap how HTTP modules and handlers work:</span></span>

![모듈 처리기](http-modules/_static/moduleshandlers.png)

<span data-ttu-id="e8ee6-109">**처리기는:**</span><span class="sxs-lookup"><span data-stu-id="e8ee6-109">**Handlers are:**</span></span>

   * <span data-ttu-id="e8ee6-110">구현 하는 클래스 [IHttpHandler](https://msdn.microsoft.com/library/system.web.ihttphandler.aspx)</span><span class="sxs-lookup"><span data-stu-id="e8ee6-110">Classes that implement [IHttpHandler](https://msdn.microsoft.com/library/system.web.ihttphandler.aspx)</span></span>

   * <span data-ttu-id="e8ee6-111">와 같은 지정 된 파일 이름이 나 확장명을 사용 하 여 요청을 처리 하는 데 사용 *보고서*</span><span class="sxs-lookup"><span data-stu-id="e8ee6-111">Used to handle requests with a given file name or extension, such as *.report*</span></span>

   * <span data-ttu-id="e8ee6-112">[구성 된](https://msdn.microsoft.com/library/46c5ddfy.aspx) 에 *Web.config*</span><span class="sxs-lookup"><span data-stu-id="e8ee6-112">[Configured](https://msdn.microsoft.com/library/46c5ddfy.aspx) in *Web.config*</span></span>

<span data-ttu-id="e8ee6-113">**모듈은:**</span><span class="sxs-lookup"><span data-stu-id="e8ee6-113">**Modules are:**</span></span>

   * <span data-ttu-id="e8ee6-114">구현 하는 클래스 [IHttpModule](https://msdn.microsoft.com/library/system.web.ihttpmodule.aspx)</span><span class="sxs-lookup"><span data-stu-id="e8ee6-114">Classes that implement [IHttpModule](https://msdn.microsoft.com/library/system.web.ihttpmodule.aspx)</span></span>

   * <span data-ttu-id="e8ee6-115">모든 요청에 대해 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-115">Invoked for every request</span></span>

   * <span data-ttu-id="e8ee6-116">(이후 처리를 중지 요청) 단락 (short-circuit) 수</span><span class="sxs-lookup"><span data-stu-id="e8ee6-116">Able to short-circuit (stop further processing of a request)</span></span>

   * <span data-ttu-id="e8ee6-117">HTTP 응답에 추가 하거나 직접 만들 수</span><span class="sxs-lookup"><span data-stu-id="e8ee6-117">Able to add to the HTTP response, or create their own</span></span>

   * <span data-ttu-id="e8ee6-118">[구성 된](https://msdn.microsoft.com/library/ms227673.aspx) 에 *Web.config*</span><span class="sxs-lookup"><span data-stu-id="e8ee6-118">[Configured](https://msdn.microsoft.com/library/ms227673.aspx) in *Web.config*</span></span>

<span data-ttu-id="e8ee6-119">**모듈에는 들어오는 요청을 처리 하는 순서는 의해 결정 됩니다.**</span><span class="sxs-lookup"><span data-stu-id="e8ee6-119">**The order in which modules process incoming requests is determined by:**</span></span>

   1. <span data-ttu-id="e8ee6-120">[응용 프로그램 수명 주기](https://msdn.microsoft.com/library/ms227673.aspx), ASP.NET에서 발생 하는 계열 이벤트 변수인: [BeginRequest](https://msdn.microsoft.com/library/system.web.httpapplication.beginrequest.aspx), [AuthenticateRequest](https://msdn.microsoft.com/library/system.web.httpapplication.authenticaterequest.aspx)등입니다. 각 모듈 하나 이상의 이벤트에 대 한 처리기를 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-120">The [application life cycle](https://msdn.microsoft.com/library/ms227673.aspx), which is a series events fired by ASP.NET: [BeginRequest](https://msdn.microsoft.com/library/system.web.httpapplication.beginrequest.aspx), [AuthenticateRequest](https://msdn.microsoft.com/library/system.web.httpapplication.authenticaterequest.aspx), etc. Each module can create a handler for one or more events.</span></span>

   2. <span data-ttu-id="e8ee6-121">같은 이벤트에 구성 된 순서에 대 한 *Web.config*합니다.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-121">For the same event, the order in which they are configured in *Web.config*.</span></span>

<span data-ttu-id="e8ee6-122">모듈을 외에도 수명 주기 이벤트에 대 한 처리기를 추가할 수 있습니다 프로그램 *Global.asax.cs* 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-122">In addition to modules, you can add handlers for the life cycle events to your *Global.asax.cs* file.</span></span> <span data-ttu-id="e8ee6-123">이러한 처리기는 구성 된 모듈의 처리기 후 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-123">These handlers run after the handlers in the configured modules.</span></span>

## <a name="from-handlers-and-modules-to-middleware"></a><span data-ttu-id="e8ee6-124">처리기와 미들웨어 모듈에서</span><span class="sxs-lookup"><span data-stu-id="e8ee6-124">From handlers and modules to middleware</span></span>

<span data-ttu-id="e8ee6-125">**미들웨어는 HTTP 모듈 및 처리기 보다 간단 합니다.**</span><span class="sxs-lookup"><span data-stu-id="e8ee6-125">**Middleware are simpler than HTTP modules and handlers:**</span></span>

   * <span data-ttu-id="e8ee6-126">모듈, 처리기, *Global.asax.cs*, *Web.config* (제외 IIS 구성) 및 응용 프로그램 수명 주기 사라지게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-126">Modules, handlers, *Global.asax.cs*, *Web.config* (except for IIS configuration) and the application life cycle are gone</span></span>

   * <span data-ttu-id="e8ee6-127">미들웨어를 하나씩 수행 된 모듈과 처리기의 역할</span><span class="sxs-lookup"><span data-stu-id="e8ee6-127">The roles of both modules and handlers have been taken over by middleware</span></span>

   * <span data-ttu-id="e8ee6-128">코드를 사용 하 여 미들웨어 구성 대신 *Web.config*</span><span class="sxs-lookup"><span data-stu-id="e8ee6-128">Middleware are configured using code rather than in *Web.config*</span></span>

   * <span data-ttu-id="e8ee6-129">[파이프라인 분기](../fundamentals/middleware.md#middleware-run-map-use) 뿐만 아니라 요청 헤더, 쿼리 문자열 등에서 뿐만 아니라 URL을 기반으로 하는 특정 미들웨어에 요청을 보낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-129">[Pipeline branching](../fundamentals/middleware.md#middleware-run-map-use) lets you send requests to specific middleware, based on not only the URL but also on request headers, query strings, etc.</span></span>

<span data-ttu-id="e8ee6-130">**미들웨어 모듈 매우 비슷합니다.**</span><span class="sxs-lookup"><span data-stu-id="e8ee6-130">**Middleware are very similar to modules:**</span></span>

   * <span data-ttu-id="e8ee6-131">모든 요청에 대해 원칙적으로 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-131">Invoked in principle for every request</span></span>

   * <span data-ttu-id="e8ee6-132">단락 (short-circuit)는 요청으로 할 [다음 미들웨어에서 요청을 전달 하지 않을 경우](#http-modules-shortcircuiting-middleware)</span><span class="sxs-lookup"><span data-stu-id="e8ee6-132">Able to short-circuit a request, by [not passing the request to the next middleware](#http-modules-shortcircuiting-middleware)</span></span>

   * <span data-ttu-id="e8ee6-133">HTTP 응답을 만들 수</span><span class="sxs-lookup"><span data-stu-id="e8ee6-133">Able to create their own HTTP response</span></span>

<span data-ttu-id="e8ee6-134">**미들웨어와 모듈은 다른 순서로 처리 됩니다.**</span><span class="sxs-lookup"><span data-stu-id="e8ee6-134">**Middleware and modules are processed in a different order:**</span></span>

   * <span data-ttu-id="e8ee6-135">미들웨어의 순서는는 삽입할 요청 파이프라인, 모듈의 순서는 주로 기반으로 하는 동안 순서에 따라 [응용 프로그램 수명 주기](https://msdn.microsoft.com/library/ms227673.aspx) 이벤트</span><span class="sxs-lookup"><span data-stu-id="e8ee6-135">Order of middleware is based on the order in which they are inserted into the request pipeline, while order of modules is mainly based on [application life cycle](https://msdn.microsoft.com/library/ms227673.aspx) events</span></span>

   * <span data-ttu-id="e8ee6-136">응답에 대 한 미들웨어의 순서는 모듈의 순서는 요청 및 응답에 대해 동일 하 게 하는 동안 요청에 대 한는 반대</span><span class="sxs-lookup"><span data-stu-id="e8ee6-136">Order of middleware for responses is the reverse from that for requests, while order of modules is the same for requests and responses</span></span>

   * <span data-ttu-id="e8ee6-137">참조 [IApplicationBuilder 미들웨어 파이프라인 만들기](../fundamentals/middleware.md#creating-a-middleware-pipeline-with-iapplicationbuilder)</span><span class="sxs-lookup"><span data-stu-id="e8ee6-137">See [Creating a middleware pipeline with IApplicationBuilder](../fundamentals/middleware.md#creating-a-middleware-pipeline-with-iapplicationbuilder)</span></span>

![미들웨어](http-modules/_static/middleware.png)

<span data-ttu-id="e8ee6-139">어떻게 요청 short-circuited 인증 미들웨어 위의 그림에서 note 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-139">Note how in the image above, the authentication middleware short-circuited the request.</span></span>

## <a name="migrating-module-code-to-middleware"></a><span data-ttu-id="e8ee6-140">미들웨어 모듈 코드 마이그레이션</span><span class="sxs-lookup"><span data-stu-id="e8ee6-140">Migrating module code to middleware</span></span>

<span data-ttu-id="e8ee6-141">기존 HTTP 모듈은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-141">An existing HTTP module will look similar to this:</span></span>

<span data-ttu-id="e8ee6-142">[!code-csharp[Main](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Modules/MyModule.cs?highlight=6,8,24,31)]</span><span class="sxs-lookup"><span data-stu-id="e8ee6-142">[!code-csharp[Main](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Modules/MyModule.cs?highlight=6,8,24,31)]</span></span>

<span data-ttu-id="e8ee6-143">에 나와 있는 것 처럼는 [미들웨어](../fundamentals/middleware.md) 페이지, ASP.NET Core 미들웨어는 노출 하는 클래스는 `Invoke` 메서드 만들기는 `HttpContext` 반환 하 고는 `Task`합니다.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-143">As shown in the [Middleware](../fundamentals/middleware.md) page, an ASP.NET Core middleware is a class that exposes an `Invoke` method taking an `HttpContext` and returning a `Task`.</span></span> <span data-ttu-id="e8ee6-144">새 미들웨어는 다음과 같이 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-144">Your new middleware will look like this:</span></span>

<a name=http-modules-usemiddleware></a>

<span data-ttu-id="e8ee6-145">[!code-csharp[Main](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddleware.cs?highlight=9,13,20,24,28,30,32)]</span><span class="sxs-lookup"><span data-stu-id="e8ee6-145">[!code-csharp[Main](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddleware.cs?highlight=9,13,20,24,28,30,32)]</span></span>

<span data-ttu-id="e8ee6-146">위의 미들웨어 서식 파일에서 섹션에서 만들어졌다면 [미들웨어를 작성](../fundamentals/middleware.md#middleware-writing-middleware)합니다.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-146">The above middleware template was taken from the section on [writing middleware](../fundamentals/middleware.md#middleware-writing-middleware).</span></span>

<span data-ttu-id="e8ee6-147">*MyMiddlewareExtensions* 도우미 클래스를 사용 하면 쉽게에 미들웨어를 구성 하 여 `Startup` 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-147">The *MyMiddlewareExtensions* helper class makes it easier to configure your middleware in your `Startup` class.</span></span> <span data-ttu-id="e8ee6-148">`UseMyMiddleware` 메서드 요청 파이프라인에 미들웨어 클래스를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-148">The `UseMyMiddleware` method adds your middleware class to the request pipeline.</span></span> <span data-ttu-id="e8ee6-149">미들웨어에서 필요한 서비스 미들웨어의 생성자에 지정 된 가져오기.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-149">Services required by the middleware get injected in the middleware's constructor.</span></span>

<a name=http-modules-shortcircuiting-middleware></a>

<span data-ttu-id="e8ee6-150">모듈에는 예를 들어 사용자는 권한이 없는 경우 요청을 종료 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-150">Your module might terminate a request, for example if the user is not authorized:</span></span>

<span data-ttu-id="e8ee6-151">[!code-csharp[Main](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Modules/MyTerminatingModule.cs?highlight=9,10,11,12,13&name=snippet_Terminate)]</span><span class="sxs-lookup"><span data-stu-id="e8ee6-151">[!code-csharp[Main](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Modules/MyTerminatingModule.cs?highlight=9,10,11,12,13&name=snippet_Terminate)]</span></span>

<span data-ttu-id="e8ee6-152">미들웨어는이 호출 하지 않음으로써 처리 `Invoke` 파이프라인의 다음 미들웨어에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-152">A middleware handles this by not calling `Invoke` on the next middleware in the pipeline.</span></span> <span data-ttu-id="e8ee6-153">이전 middlewares 응답 파이프라인을 통해 다시 때에 호출 될 때문에이 완벽 하 게을 종료 하지 않는 요청을 염두에서에 둬야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-153">Keep in mind that this does not fully terminate the request, because previous middlewares will still be invoked when the response makes its way back through the pipeline.</span></span>

<span data-ttu-id="e8ee6-154">[!code-csharp[Main](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyTerminatingMiddleware.cs?highlight=7,8&name=snippet_Terminate)]</span><span class="sxs-lookup"><span data-stu-id="e8ee6-154">[!code-csharp[Main](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyTerminatingMiddleware.cs?highlight=7,8&name=snippet_Terminate)]</span></span>

<span data-ttu-id="e8ee6-155">새 미들웨어를 모듈의 기능을 마이그레이션할 때 때문에 코드가 컴파일되지 않습니다 알 수 있습니다는 `HttpContext` 클래스 ASP.NET Core에서 크게 변경 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-155">When you migrate your module's functionality to your new middleware, you may find that your code doesn't compile because the `HttpContext` class has significantly changed in ASP.NET Core.</span></span> <span data-ttu-id="e8ee6-156">[나중에](#migrating-to-the-new-httpcontext), 새 ASP.NET Core HttpContext를 마이그레이션하는 방법에 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-156">[Later on](#migrating-to-the-new-httpcontext), you'll see how to migrate to the new ASP.NET Core HttpContext.</span></span>

## <a name="migrating-module-insertion-into-the-request-pipeline"></a><span data-ttu-id="e8ee6-157">마이그레이션 모듈 삽입 요청 파이프라인</span><span class="sxs-lookup"><span data-stu-id="e8ee6-157">Migrating module insertion into the request pipeline</span></span>

<span data-ttu-id="e8ee6-158">HTTP 모듈은 일반적으로 사용 하 여 요청 파이프라인에 추가 됩니다 *Web.config*:</span><span class="sxs-lookup"><span data-stu-id="e8ee6-158">HTTP modules are typically added to the request pipeline using *Web.config*:</span></span>

<span data-ttu-id="e8ee6-159">[!code-xml[Main](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Web.config?highlight=6&range=1-3,32-33,36,43,50,101)]</span><span class="sxs-lookup"><span data-stu-id="e8ee6-159">[!code-xml[Main](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Web.config?highlight=6&range=1-3,32-33,36,43,50,101)]</span></span>

<span data-ttu-id="e8ee6-160">이 변환 [새 미들웨어를 추가](../fundamentals/middleware.md#creating-a-middleware-pipeline-with-iapplicationbuilder) 요청 파이프라인에 프로그램 `Startup` 클래스:</span><span class="sxs-lookup"><span data-stu-id="e8ee6-160">Convert this by [adding your new middleware](../fundamentals/middleware.md#creating-a-middleware-pipeline-with-iapplicationbuilder) to the request pipeline in your `Startup` class:</span></span>

<span data-ttu-id="e8ee6-161">[!code-csharp[Main](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=16)]</span><span class="sxs-lookup"><span data-stu-id="e8ee6-161">[!code-csharp[Main](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=16)]</span></span>

<span data-ttu-id="e8ee6-162">새 미들웨어를 삽입 하면 파이프라인에서 정확한 지점 모듈로 처리 하는 이벤트에 따라 달라 집니다 (`BeginRequest`, `EndRequest`등)의 모듈 목록에서 해당 순서 및 *Web.config*합니다.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-162">The exact spot in the pipeline where you insert your new middleware depends on the event that it handled as a module (`BeginRequest`, `EndRequest`, etc.) and its order in your list of modules in *Web.config*.</span></span>

<span data-ttu-id="e8ee6-163">이전에 ASP.NET Core에 없는 응용 프로그램 수명 주기는 명시 하는 미들웨어에서 응답을 처리 하는 순서 모듈에 의해 사용 되는 순서와에서 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-163">As previously stated, there is no application life cycle in ASP.NET Core and the order in which responses are processed by middleware differs from the order used by modules.</span></span> <span data-ttu-id="e8ee6-164">이 정렬 결정 좀 더 어렵습니다를 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-164">This could make your ordering decision more challenging.</span></span>

<span data-ttu-id="e8ee6-165">순서 지정 문제가 되 면 독립적으로 주문할 수 있습니다. 여러 미들웨어 구성 요소에 모듈을 분할할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-165">If ordering becomes a problem, you could split your module into multiple middleware components that can be ordered independently.</span></span>

## <a name="migrating-handler-code-to-middleware"></a><span data-ttu-id="e8ee6-166">미들웨어 처리기 코드 마이그레이션</span><span class="sxs-lookup"><span data-stu-id="e8ee6-166">Migrating handler code to middleware</span></span>

<span data-ttu-id="e8ee6-167">HTTP 처리기는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-167">An HTTP handler looks something like this:</span></span>

<span data-ttu-id="e8ee6-168">[!code-csharp[Main](../migration/http-modules/sample/Asp.Net4/Asp.Net4/HttpHandlers/ReportHandler.cs?highlight=5,7,13,14,15,16)]</span><span class="sxs-lookup"><span data-stu-id="e8ee6-168">[!code-csharp[Main](../migration/http-modules/sample/Asp.Net4/Asp.Net4/HttpHandlers/ReportHandler.cs?highlight=5,7,13,14,15,16)]</span></span>

<span data-ttu-id="e8ee6-169">ASP.NET Core 프로젝트에는 번역할이 다음과 유사 하 게 되는 미들웨어:</span><span class="sxs-lookup"><span data-stu-id="e8ee6-169">In your ASP.NET Core project, you would translate this to a middleware similar to this:</span></span>

<span data-ttu-id="e8ee6-170">[!code-csharp[Main](../migration/http-modules/sample/Asp.Net.Core/Middleware/ReportHandlerMiddleware.cs?highlight=7,9,13,20,21,22,23,40,42,44)]</span><span class="sxs-lookup"><span data-stu-id="e8ee6-170">[!code-csharp[Main](../migration/http-modules/sample/Asp.Net.Core/Middleware/ReportHandlerMiddleware.cs?highlight=7,9,13,20,21,22,23,40,42,44)]</span></span>

<span data-ttu-id="e8ee6-171">이 미들웨어는 모듈에 해당 하는 미들웨어와 매우 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-171">This middleware is very similar to the middleware corresponding to modules.</span></span> <span data-ttu-id="e8ee6-172">유일한 차이점은 다음과 같습니다에 대 한 호출이 `_next.Invoke(context)`합니다.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-172">The only real difference is that here there is no call to `_next.Invoke(context)`.</span></span> <span data-ttu-id="e8ee6-173">지도록 관점에서 처리기가 요청 파이프라인의 끝에가 있으므로 호출할 다음 미들웨어 없습니다.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-173">That makes sense, because the handler is at the end of the request pipeline, so there will be no next middleware to invoke.</span></span>

## <a name="migrating-handler-insertion-into-the-request-pipeline"></a><span data-ttu-id="e8ee6-174">마이그레이션 처리기 삽입 요청 파이프라인</span><span class="sxs-lookup"><span data-stu-id="e8ee6-174">Migrating handler insertion into the request pipeline</span></span>

<span data-ttu-id="e8ee6-175">HTTP 처리기 구성 의해 이루어진다는 *Web.config* 다음과 같은 및:</span><span class="sxs-lookup"><span data-stu-id="e8ee6-175">Configuring an HTTP handler is done in *Web.config* and looks something like this:</span></span>

<span data-ttu-id="e8ee6-176">[!code-xml[Main](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Web.config?highlight=6&range=1-3,32,46-48,50,101)]</span><span class="sxs-lookup"><span data-stu-id="e8ee6-176">[!code-xml[Main](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Web.config?highlight=6&range=1-3,32,46-48,50,101)]</span></span>

<span data-ttu-id="e8ee6-177">새 처리기 미들웨어에서 요청 파이프라인에 추가 하 여 변환할 수 있습니다 프로그램 `Startup` 클래스, 모듈에서 변환 하는 미들웨어와 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-177">You could convert this by adding your new handler middleware to the request pipeline in your `Startup` class, similar to middleware converted from modules.</span></span> <span data-ttu-id="e8ee6-178">이 방법의 문제 새 처리기 미들웨어에 대 한 모든 요청을 보내는 것과 것입니다.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-178">The problem with that approach is that it would send all requests to your new handler middleware.</span></span> <span data-ttu-id="e8ee6-179">그러나만 원하는 미들웨어 연결할 지정된 된 확장 프로그램으로 요청 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-179">However, you only want requests with a given extension to reach your middleware.</span></span> <span data-ttu-id="e8ee6-180">HTTP 처리기로 사용 했던 동일한 기능을 가질 것입니다.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-180">That would give you the same functionality you had with your HTTP handler.</span></span>

<span data-ttu-id="e8ee6-181">한 가지 해결 방법은 지정된 된 확장 프로그램을 사용 하 여 요청에 대 한 파이프라인 분기를 사용 하 여는 `MapWhen` 확장 메서드.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-181">One solution is to branch the pipeline for requests with a given extension, using the `MapWhen` extension method.</span></span> <span data-ttu-id="e8ee6-182">이렇게 하면 동일한 `Configure` 다른 미들웨어를 추가 하는 메서드:</span><span class="sxs-lookup"><span data-stu-id="e8ee6-182">You do this in the same `Configure` method where you add the other middleware:</span></span>

<span data-ttu-id="e8ee6-183">[!code-csharp[Main](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=27-34)]</span><span class="sxs-lookup"><span data-stu-id="e8ee6-183">[!code-csharp[Main](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=27-34)]</span></span>

<span data-ttu-id="e8ee6-184">`MapWhen`이러한 매개 변수를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-184">`MapWhen` takes these parameters:</span></span>

1. <span data-ttu-id="e8ee6-185">람다는는 `HttpContext` 반환 `true` 여 분기 요청이 전달 해야 하는 경우.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-185">A lambda that takes the `HttpContext` and returns `true` if the request should go down the branch.</span></span> <span data-ttu-id="e8ee6-186">즉, 뿐 아니라 해당 확장명에 있지만 요청 헤더, 쿼리 문자열 매개 변수 등을 따라 요청을 분기할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-186">This means you can branch requests not just based on their extension, but also on request headers, query string parameters, etc.</span></span>

2. <span data-ttu-id="e8ee6-187">사용 하는 람다는 `IApplicationBuilder` 분기에 대 한 모든 미들웨어를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-187">A lambda that takes an `IApplicationBuilder` and adds all the middleware for the branch.</span></span> <span data-ttu-id="e8ee6-188">즉, 처리기 미들웨어 앞에 추가 미들웨어 분기에 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-188">This means you can add additional middleware to the branch in front of your handler middleware.</span></span>

<span data-ttu-id="e8ee6-189">분기는 모든 요청;에서 호출 됩니다. 전에 파이프라인에 미들웨어 추가 분기에 영향을 주어 갖습니다.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-189">Middleware added to the pipeline before the branch will be invoked on all requests; the branch will have no impact on them.</span></span>

## <a name="loading-middleware-options-using-the-options-pattern"></a><span data-ttu-id="e8ee6-190">로드 옵션 패턴을 사용 하 여 미들웨어 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-190">Loading middleware options using the options pattern</span></span>

<span data-ttu-id="e8ee6-191">일부 모듈과 처리기에 저장 되어 있는 구성 옵션을 사용할 *Web.config*합니다.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-191">Some modules and handlers have configuration options that are stored in *Web.config*.</span></span> <span data-ttu-id="e8ee6-192">그러나 ASP.NET Core에는 새 구성 모델이 사용 됩니다 대신 *Web.config*합니다.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-192">However, in ASP.NET Core a new configuration model is used in place of *Web.config*.</span></span>

<span data-ttu-id="e8ee6-193">새 [구성 시스템](../fundamentals/configuration.md) 이 문제를 해결 하려면 다음이 옵션을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-193">The new [configuration system](../fundamentals/configuration.md) gives you these options to solve this:</span></span>

* <span data-ttu-id="e8ee6-194">에 표시 된 대로 미들웨어에 대 한 옵션을 직접 삽입는 [절로](#loading-middleware-options-through-direct-injection)합니다.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-194">Directly inject the options into the middleware, as shown in the [next section](#loading-middleware-options-through-direct-injection).</span></span>

* <span data-ttu-id="e8ee6-195">사용 하 여 [옵션 패턴](../fundamentals/configuration.md#options-config-objects):</span><span class="sxs-lookup"><span data-stu-id="e8ee6-195">Use the [options pattern](../fundamentals/configuration.md#options-config-objects):</span></span>

1.  <span data-ttu-id="e8ee6-196">예를 들어 미들웨어 옵션을 저장 하는 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-196">Create a class to hold your middleware options, for example:</span></span>

    <span data-ttu-id="e8ee6-197">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_Options)]</span><span class="sxs-lookup"><span data-stu-id="e8ee6-197">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_Options)]</span></span>

2.  <span data-ttu-id="e8ee6-198">옵션 값을 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-198">Store the option values</span></span>

    <span data-ttu-id="e8ee6-199">구성 시스템을 사용 하면 옵션 아무 곳 이나 원하는 값을 저장할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-199">The configuration system allows you to store option values anywhere you want.</span></span> <span data-ttu-id="e8ee6-200">그러나 사용 하 여 가장 사이트 *appsettings.json*이므로 해당 접근 이동 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-200">However, most sites use *appsettings.json*, so we'll take that approach:</span></span>

    <span data-ttu-id="e8ee6-201">[!code-json[Main](http-modules/sample/Asp.Net.Core/appsettings.json?range=1,14-18)]</span><span class="sxs-lookup"><span data-stu-id="e8ee6-201">[!code-json[Main](http-modules/sample/Asp.Net.Core/appsettings.json?range=1,14-18)]</span></span>

    <span data-ttu-id="e8ee6-202">*MyMiddlewareOptionsSection* 섹션 이름에는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-202">*MyMiddlewareOptionsSection* here is a section name.</span></span> <span data-ttu-id="e8ee6-203">옵션 클래스의 이름이 같이 필요는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-203">It doesn't have to be the same as the name of your options class.</span></span>

3. <span data-ttu-id="e8ee6-204">옵션 클래스 옵션 값에 연결</span><span class="sxs-lookup"><span data-stu-id="e8ee6-204">Associate the option values with the options class</span></span>

    <span data-ttu-id="e8ee6-205">옵션 패턴 ASP.NET Core 종속성 주입 프레임 워크를 사용 하 여 연결 옵션의 유형을 (같은 `MyMiddlewareOptions`)와 `MyMiddlewareOptions` 실제 옵션이 있는 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-205">The options pattern uses ASP.NET Core's dependency injection framework to associate the options type (such as `MyMiddlewareOptions`) with a `MyMiddlewareOptions` object that has the actual options.</span></span>

    <span data-ttu-id="e8ee6-206">업데이트 프로그램 `Startup` 클래스:</span><span class="sxs-lookup"><span data-stu-id="e8ee6-206">Update your `Startup` class:</span></span>

    1.  <span data-ttu-id="e8ee6-207">사용 중인 경우 *appsettings.json*의 구성 작성기에 추가 `Startup` 생성자:</span><span class="sxs-lookup"><span data-stu-id="e8ee6-207">If you're using *appsettings.json*, add it to the configuration builder in the `Startup` constructor:</span></span>

      <span data-ttu-id="e8ee6-208">[!code-csharp[Main](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Ctor&highlight=5-6)]</span><span class="sxs-lookup"><span data-stu-id="e8ee6-208">[!code-csharp[Main](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Ctor&highlight=5-6)]</span></span>

    2.  <span data-ttu-id="e8ee6-209">옵션 서비스를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-209">Configure the options service:</span></span>

      <span data-ttu-id="e8ee6-210">[!code-csharp[Main](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_ConfigureServices&highlight=4)]</span><span class="sxs-lookup"><span data-stu-id="e8ee6-210">[!code-csharp[Main](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_ConfigureServices&highlight=4)]</span></span>

    3.  <span data-ttu-id="e8ee6-211">옵션 클래스 옵션을 연결 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-211">Associate your options with your options class:</span></span>

      <span data-ttu-id="e8ee6-212">[!code-csharp[Main](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_ConfigureServices&highlight=6-8)]</span><span class="sxs-lookup"><span data-stu-id="e8ee6-212">[!code-csharp[Main](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_ConfigureServices&highlight=6-8)]</span></span>

4.  <span data-ttu-id="e8ee6-213">미들웨어 생성자에 대 한 옵션을 삽입 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-213">Inject the options into your middleware constructor.</span></span> <span data-ttu-id="e8ee6-214">컨트롤러에 대 한 옵션을 삽입 하는 것과 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-214">This is similar to injecting options into a controller.</span></span>

  <span data-ttu-id="e8ee6-215">[!code-csharp[Main](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_MiddlewareWithParams&highlight=4,7,10,15-16)]</span><span class="sxs-lookup"><span data-stu-id="e8ee6-215">[!code-csharp[Main](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_MiddlewareWithParams&highlight=4,7,10,15-16)]</span></span>

  <span data-ttu-id="e8ee6-216">[UseMiddleware](#http-modules-usemiddleware) 미들웨어를 추가 하는 확장 메서드는 `IApplicationBuilder` 종속성 주입 담당 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-216">The [UseMiddleware](#http-modules-usemiddleware) extension method that adds your middleware to the `IApplicationBuilder` takes care of dependency injection.</span></span>

  <span data-ttu-id="e8ee6-217">에 국한 되지 않음 `IOptions` 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-217">This is not limited to `IOptions` objects.</span></span> <span data-ttu-id="e8ee6-218">미들웨어 해야 하는 다른 개체에는 이러한 방식으로 삽입 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-218">Any other object that your middleware requires can be injected this way.</span></span>

## <a name="loading-middleware-options-through-direct-injection"></a><span data-ttu-id="e8ee6-219">로드를 통해 직접 삽입 미들웨어 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-219">Loading middleware options through direct injection</span></span>

<span data-ttu-id="e8ee6-220">옵션 패턴에 낮은 옵션 값와 소비자 사이 결합은 생성 되는 이점이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-220">The options pattern has the advantage that it creates loose coupling between options values and their consumers.</span></span> <span data-ttu-id="e8ee6-221">실제 옵션 값에는 옵션 클래스 연결한, 되 면 다른 클래스 종속성 주입 프레임 워크를 통해 옵션에 대 한 액세스를 얻을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-221">Once you've associated an options class with the actual options values, any other class can get access to the options through the dependency injection framework.</span></span> <span data-ttu-id="e8ee6-222">옵션 값 전달 않아도가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-222">There is no need to pass around options values.</span></span>

<span data-ttu-id="e8ee6-223">그러면 분할 하지만 여러 가지 옵션을 같은 미들웨어를 두 번 사용 하려는 경우.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-223">This breaks down though if you want to use the same middleware twice, with different options.</span></span> <span data-ttu-id="e8ee6-224">예를 들어는 권한 부여 미들웨어 다양 한 역할을 허용 하는 서로 다른 분기에서 사용 되는입니다.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-224">For example an authorization middleware used in different branches allowing different roles.</span></span> <span data-ttu-id="e8ee6-225">하나의 옵션 클래스와 두 개의 서로 다른 옵션 개체를 연결할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-225">You can't associate two different options objects with the one options class.</span></span>

<span data-ttu-id="e8ee6-226">실제 옵션 값과 옵션 개체를 가져오는 데 솔루션은 프로그램 `Startup` 클래스 미들웨어의 각 인스턴스에 확인란을 직접 전달 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-226">The solution is to get the options objects with the actual options values in your `Startup` class and pass those directly to each instance of your middleware.</span></span>

1.  <span data-ttu-id="e8ee6-227">두 번째 키를 추가 *appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="e8ee6-227">Add a second key to *appsettings.json*</span></span>

    <span data-ttu-id="e8ee6-228">옵션 중 두 번째 집합을 추가 하려면는 *appsettings.json* 파일을 고유 하 게 식별 하는 데 새 키를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-228">To add a second set of options to the *appsettings.json* file, use a new key to uniquely identify it:</span></span>

    <span data-ttu-id="e8ee6-229">[!code-json[Main](http-modules/sample/Asp.Net.Core/appsettings.json?range=1,10-18&highlight=2-5)]</span><span class="sxs-lookup"><span data-stu-id="e8ee6-229">[!code-json[Main](http-modules/sample/Asp.Net.Core/appsettings.json?range=1,10-18&highlight=2-5)]</span></span>

2.  <span data-ttu-id="e8ee6-230">옵션 값을 검색 하 고 미들웨어에 전달 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-230">Retrieve options values and pass them to middleware.</span></span> <span data-ttu-id="e8ee6-231">`Use...` (파이프라인에 미들웨어를 추가)는 확장 메서드는 옵션 값을 전달할 수 있는 논리적 위치:</span><span class="sxs-lookup"><span data-stu-id="e8ee6-231">The `Use...` extension method (which adds your middleware to the pipeline) is a logical place to pass in the option values:</span></span> 

    <span data-ttu-id="e8ee6-232">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=20-23)]</span><span class="sxs-lookup"><span data-stu-id="e8ee6-232">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=20-23)]</span></span>

4.  <span data-ttu-id="e8ee6-233">미들웨어 옵션 매개 변수를 사용 하도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-233">Enable middleware to take an options parameter.</span></span> <span data-ttu-id="e8ee6-234">오버 로드를 제공 된 `Use...` 확장 메서드 (옵션 매개 변수를 사용 하 고로 전달 `UseMiddleware`).</span><span class="sxs-lookup"><span data-stu-id="e8ee6-234">Provide an overload of the `Use...` extension method (that takes the options parameter and passes it to `UseMiddleware`).</span></span> <span data-ttu-id="e8ee6-235">때 `UseMiddleware` 라고 매개 변수와 함께 전달 매개 변수 미들웨어 생성자에 미들웨어 개체를 인스턴스화할 때.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-235">When `UseMiddleware` is called with parameters, it passes the parameters to your middleware constructor when it instantiates the middleware object.</span></span>

    <span data-ttu-id="e8ee6-236">[!code-csharp[Main](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_Extensions&highlight=9-14)]</span><span class="sxs-lookup"><span data-stu-id="e8ee6-236">[!code-csharp[Main](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_Extensions&highlight=9-14)]</span></span>

    <span data-ttu-id="e8ee6-237">이 옵션 개체에 배치 되는 방법을 확인 프로그램 `OptionsWrapper` 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-237">Note how this wraps the options object in an `OptionsWrapper` object.</span></span> <span data-ttu-id="e8ee6-238">이 구현 `IOptions`미들웨어 생성자가 예상 대로, 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-238">This implements `IOptions`, as expected by the middleware constructor.</span></span>

## <a name="migrating-to-the-new-httpcontext"></a><span data-ttu-id="e8ee6-239">새 HttpContext로 마이그레이션</span><span class="sxs-lookup"><span data-stu-id="e8ee6-239">Migrating to the new HttpContext</span></span>

<span data-ttu-id="e8ee6-240">앞에서 본 하는 `Invoke` 형식의 매개 변수를 사용 하는 미들웨어에서 메서드 `HttpContext`:</span><span class="sxs-lookup"><span data-stu-id="e8ee6-240">You saw earlier that the `Invoke` method in your middleware takes a parameter of type `HttpContext`:</span></span>

```csharp
public async Task Invoke(HttpContext context)
```

<span data-ttu-id="e8ee6-241">`HttpContext`ASP.NET Core 크게 변경 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-241">`HttpContext` has significantly changed in ASP.NET Core.</span></span> <span data-ttu-id="e8ee6-242">이 섹션에서는의 가장 일반적으로 사용 되는 속성으로 변환 하는 방법을 보여 줍니다. [System.Web.HttpContext](https://msdn.microsoft.com/library/system.web.httpcontext.aspx) 새 `Microsoft.AspNetCore.Http.HttpContext`합니다.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-242">This section shows how to translate the most commonly used properties of [System.Web.HttpContext](https://msdn.microsoft.com/library/system.web.httpcontext.aspx) to the new `Microsoft.AspNetCore.Http.HttpContext`.</span></span>

### <a name="httpcontext"></a><span data-ttu-id="e8ee6-243">HttpContext</span><span class="sxs-lookup"><span data-stu-id="e8ee6-243">HttpContext</span></span>

<span data-ttu-id="e8ee6-244">**HttpContext.Items** 로 변환 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-244">**HttpContext.Items** translates to:</span></span>

<span data-ttu-id="e8ee6-245">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Items)]</span><span class="sxs-lookup"><span data-stu-id="e8ee6-245">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Items)]</span></span>

<span data-ttu-id="e8ee6-246">**고유한 요청 ID (System.Web.HttpContext 해당)**</span><span class="sxs-lookup"><span data-stu-id="e8ee6-246">**Unique request ID (no System.Web.HttpContext counterpart)**</span></span>

<span data-ttu-id="e8ee6-247">각 요청에 대 한 고유 id을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-247">Gives you a unique id for each request.</span></span> <span data-ttu-id="e8ee6-248">로그에 포함 하는 데 매우 유용 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-248">Very useful to include in your logs.</span></span>

<span data-ttu-id="e8ee6-249">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Trace)]</span><span class="sxs-lookup"><span data-stu-id="e8ee6-249">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Trace)]</span></span>

### <a name="httpcontextrequest"></a><span data-ttu-id="e8ee6-250">HttpContext.Request</span><span class="sxs-lookup"><span data-stu-id="e8ee6-250">HttpContext.Request</span></span>

<span data-ttu-id="e8ee6-251">**HttpContext.Request.HttpMethod** 로 변환 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-251">**HttpContext.Request.HttpMethod** translates to:</span></span>

<span data-ttu-id="e8ee6-252">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Method)]</span><span class="sxs-lookup"><span data-stu-id="e8ee6-252">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Method)]</span></span>

<span data-ttu-id="e8ee6-253">**HttpContext.Request.QueryString** 로 변환 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-253">**HttpContext.Request.QueryString** translates to:</span></span>

<span data-ttu-id="e8ee6-254">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Query)]</span><span class="sxs-lookup"><span data-stu-id="e8ee6-254">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Query)]</span></span>

<span data-ttu-id="e8ee6-255">**HttpContext.Request.Url** 및 **HttpContext.Request.RawUrl** 로 변환 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-255">**HttpContext.Request.Url** and **HttpContext.Request.RawUrl** translate to:</span></span>

<span data-ttu-id="e8ee6-256">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Url)]</span><span class="sxs-lookup"><span data-stu-id="e8ee6-256">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Url)]</span></span>

<span data-ttu-id="e8ee6-257">**HttpContext.Request.IsSecureConnection** 로 변환 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-257">**HttpContext.Request.IsSecureConnection** translates to:</span></span>

<span data-ttu-id="e8ee6-258">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Secure)]</span><span class="sxs-lookup"><span data-stu-id="e8ee6-258">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Secure)]</span></span>

<span data-ttu-id="e8ee6-259">**HttpContext.Request.UserHostAddress** 로 변환 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-259">**HttpContext.Request.UserHostAddress** translates to:</span></span>

<span data-ttu-id="e8ee6-260">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Host)]</span><span class="sxs-lookup"><span data-stu-id="e8ee6-260">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Host)]</span></span>

<span data-ttu-id="e8ee6-261">**HttpContext.Request.Cookies** 로 변환 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-261">**HttpContext.Request.Cookies** translates to:</span></span>

<span data-ttu-id="e8ee6-262">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Cookies)]</span><span class="sxs-lookup"><span data-stu-id="e8ee6-262">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Cookies)]</span></span>

<span data-ttu-id="e8ee6-263">**HttpContext.Request.RequestContext.RouteData** 로 변환 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-263">**HttpContext.Request.RequestContext.RouteData** translates to:</span></span>

<span data-ttu-id="e8ee6-264">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Route)]</span><span class="sxs-lookup"><span data-stu-id="e8ee6-264">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Route)]</span></span>

<span data-ttu-id="e8ee6-265">**HttpContext.Request.Headers** 로 변환 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-265">**HttpContext.Request.Headers** translates to:</span></span>

<span data-ttu-id="e8ee6-266">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Headers)]</span><span class="sxs-lookup"><span data-stu-id="e8ee6-266">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Headers)]</span></span>

<span data-ttu-id="e8ee6-267">**HttpContext.Request.UserAgent** 로 변환 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-267">**HttpContext.Request.UserAgent** translates to:</span></span>

<span data-ttu-id="e8ee6-268">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Agent)]</span><span class="sxs-lookup"><span data-stu-id="e8ee6-268">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Agent)]</span></span>

<span data-ttu-id="e8ee6-269">**HttpContext.Request.UrlReferrer** 로 변환 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-269">**HttpContext.Request.UrlReferrer** translates to:</span></span>

<span data-ttu-id="e8ee6-270">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Referrer)]</span><span class="sxs-lookup"><span data-stu-id="e8ee6-270">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Referrer)]</span></span>

<span data-ttu-id="e8ee6-271">**HttpContext.Request.ContentType** 로 변환 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-271">**HttpContext.Request.ContentType** translates to:</span></span>

<span data-ttu-id="e8ee6-272">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Type)]</span><span class="sxs-lookup"><span data-stu-id="e8ee6-272">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Type)]</span></span>

<span data-ttu-id="e8ee6-273">**HttpContext.Request.Form** 로 변환 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-273">**HttpContext.Request.Form** translates to:</span></span>

<span data-ttu-id="e8ee6-274">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Form)]</span><span class="sxs-lookup"><span data-stu-id="e8ee6-274">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Form)]</span></span>

> [!WARNING]
> <span data-ttu-id="e8ee6-275">콘텐츠 하위 형식이 경우 양식 값을 읽기 *x-www-형식-urlencoded* 또는 *양식 데이터*합니다.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-275">Read form values only if the content sub type is *x-www-form-urlencoded* or *form-data*.</span></span>

<span data-ttu-id="e8ee6-276">**HttpContext.Request.InputStream** 로 변환 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-276">**HttpContext.Request.InputStream** translates to:</span></span>

<span data-ttu-id="e8ee6-277">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Input)]</span><span class="sxs-lookup"><span data-stu-id="e8ee6-277">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Input)]</span></span>

> [!WARNING]
> <span data-ttu-id="e8ee6-278">이 코드는 파이프라인의 끝에 처리기 형식 미들웨어, 에서만 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-278">Use this code only in a handler type middleware, at the end of a pipeline.</span></span>
>
><span data-ttu-id="e8ee6-279">요청당 한 번만 위와 같이 원시 본문을 읽을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-279">You can read the raw body as shown above only once per request.</span></span> <span data-ttu-id="e8ee6-280">미들웨어를 첫 번째 읽기 후 본문을 읽는 동안에 빈 본문이 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-280">Middleware trying to read the body after the first read will read an empty body.</span></span>
>
><span data-ttu-id="e8ee6-281">이 버퍼에서 수행 되기 때문에 앞에서 표시 된 것 처럼 폼 읽기에 적용 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-281">This does not apply to reading a form as shown earlier, because that is done from a buffer.</span></span>

### <a name="httpcontextresponse"></a><span data-ttu-id="e8ee6-282">HttpContext.Response</span><span class="sxs-lookup"><span data-stu-id="e8ee6-282">HttpContext.Response</span></span>

<span data-ttu-id="e8ee6-283">**HttpContext.Response.Status** 및 **HttpContext.Response.StatusDescription** 로 변환 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-283">**HttpContext.Response.Status** and **HttpContext.Response.StatusDescription** translate to:</span></span>

<span data-ttu-id="e8ee6-284">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Status)]</span><span class="sxs-lookup"><span data-stu-id="e8ee6-284">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Status)]</span></span>

<span data-ttu-id="e8ee6-285">**HttpContext.Response.ContentEncoding** 및 **HttpContext.Response.ContentType** 로 변환 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-285">**HttpContext.Response.ContentEncoding** and **HttpContext.Response.ContentType** translate to:</span></span>

<span data-ttu-id="e8ee6-286">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_RespType)]</span><span class="sxs-lookup"><span data-stu-id="e8ee6-286">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_RespType)]</span></span>

<span data-ttu-id="e8ee6-287">**HttpContext.Response.ContentType** 에 자체도로 변환 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-287">**HttpContext.Response.ContentType** on its own also translates to:</span></span>

<span data-ttu-id="e8ee6-288">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_RespTypeOnly)]</span><span class="sxs-lookup"><span data-stu-id="e8ee6-288">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_RespTypeOnly)]</span></span>

<span data-ttu-id="e8ee6-289">**HttpContext.Response.Output** 로 변환 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-289">**HttpContext.Response.Output** translates to:</span></span>

<span data-ttu-id="e8ee6-290">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Output)]</span><span class="sxs-lookup"><span data-stu-id="e8ee6-290">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Output)]</span></span>

<span data-ttu-id="e8ee6-291">**HttpContext.Response.TransmitFile**</span><span class="sxs-lookup"><span data-stu-id="e8ee6-291">**HttpContext.Response.TransmitFile**</span></span>

<span data-ttu-id="e8ee6-292">파일을 서비스 설명 [여기](../fundamentals/request-features.md#middleware-and-request-features)합니다.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-292">Serving up a file is discussed [here](../fundamentals/request-features.md#middleware-and-request-features).</span></span>

<span data-ttu-id="e8ee6-293">**HttpContext.Response.Headers**</span><span class="sxs-lookup"><span data-stu-id="e8ee6-293">**HttpContext.Response.Headers**</span></span>

<span data-ttu-id="e8ee6-294">때문에 복잡는 보내는 응답 헤더를 설정할 경우 응답 본문에 아무 것도 기록 된 후은 전송 되지 것입니다.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-294">Sending response headers is complicated by the fact that if you set them after anything has been written to the response body, they will not be sent.</span></span>

<span data-ttu-id="e8ee6-295">솔루션 응답 시작에 쓰기 전에 오른쪽 호출 될 콜백 메서드를 설정 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-295">The solution is to set a callback method that will be called right before writing to the response starts.</span></span> <span data-ttu-id="e8ee6-296">시작 부분에 가장 잘 이렇게는 `Invoke` 미들웨어에서 메서드.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-296">This is best done at the start of the `Invoke` method in your middleware.</span></span> <span data-ttu-id="e8ee6-297">응답 헤더를 설정 하는이 콜백 메서드는</span><span class="sxs-lookup"><span data-stu-id="e8ee6-297">It is this callback method that sets your response headers.</span></span>

<span data-ttu-id="e8ee6-298">다음 코드에서는 호출 하는 콜백 메서드 설정 `SetHeaders`:</span><span class="sxs-lookup"><span data-stu-id="e8ee6-298">The following code sets a callback method called `SetHeaders`:</span></span>

```csharp
public async Task Invoke(HttpContext httpContext)
{
    // ...
    httpContext.Response.OnStarting(SetHeaders, state: httpContext);
```

<span data-ttu-id="e8ee6-299">`SetHeaders` 콜백 메서드는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-299">The `SetHeaders` callback method would look like this:</span></span>

<span data-ttu-id="e8ee6-300">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_SetHeaders)]</span><span class="sxs-lookup"><span data-stu-id="e8ee6-300">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_SetHeaders)]</span></span>

<span data-ttu-id="e8ee6-301">**HttpContext.Response.Cookies**</span><span class="sxs-lookup"><span data-stu-id="e8ee6-301">**HttpContext.Response.Cookies**</span></span>

<span data-ttu-id="e8ee6-302">브라우저에서 쿠키 여행는 *Set-cookie* 응답 헤더입니다.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-302">Cookies travel to the browser in a *Set-Cookie* response header.</span></span> <span data-ttu-id="e8ee6-303">결과적으로, 쿠키 보내기를 응답 헤더를 보내는 데 사용 된 것과 동일한 콜백 함수가 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-303">As a result, sending cookies requires the same callback as used for sending response headers:</span></span>

```csharp
public async Task Invoke(HttpContext httpContext)
{
    // ...
    httpContext.Response.OnStarting(SetCookies, state: httpContext);
    httpContext.Response.OnStarting(SetHeaders, state: httpContext);
```

<span data-ttu-id="e8ee6-304">`SetCookies` 콜백 메서드는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="e8ee6-304">The `SetCookies` callback method would look like the following:</span></span>

<span data-ttu-id="e8ee6-305">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_SetCookies)]</span><span class="sxs-lookup"><span data-stu-id="e8ee6-305">[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_SetCookies)]</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e8ee6-306">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="e8ee6-306">Additional Resources</span></span>

* [<span data-ttu-id="e8ee6-307">HTTP 처리기 및 HTTP 모듈 개요</span><span class="sxs-lookup"><span data-stu-id="e8ee6-307">HTTP Handlers and HTTP Modules Overview</span></span>](https://msdn.microsoft.com/library/bb398986.aspx)

* [<span data-ttu-id="e8ee6-308">구성</span><span class="sxs-lookup"><span data-stu-id="e8ee6-308">Configuration</span></span>](../fundamentals/configuration.md)

* [<span data-ttu-id="e8ee6-309">응용 프로그램 시작</span><span class="sxs-lookup"><span data-stu-id="e8ee6-309">Application Startup</span></span>](../fundamentals/startup.md)

* [<span data-ttu-id="e8ee6-310">미들웨어</span><span class="sxs-lookup"><span data-stu-id="e8ee6-310">Middleware</span></span>](../fundamentals/middleware.md)
