---
title: "ASP.NET Core MVC에서 응답 데이터 서식 지정"
author: ardalis
description: "ASP.NET Core MVC에서 응답 데이터 형식을 지정 하는 방법에 알아봅니다."
keywords: "ASP.NET Core, IOutputFormatter, IActionResult 응답 데이터"
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: c056df45-d013-4826-91a1-4a092bae1ea5
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/models/formatting
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a7fd1ecb61c7b1559f8bd304c30f7201864bd448
ms.sourcegitcommit: 9cdbfd0d670d70b9c354216aabee260c52dad5ee
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/12/2017
---
# <a name="introduction-to-formatting-response-data-in-aspnet-core-mvc"></a><span data-ttu-id="ef6fa-104">ASP.NET Core MVC의 서식 지정 응답 데이터 소개</span><span class="sxs-lookup"><span data-stu-id="ef6fa-104">Introduction to formatting response data in ASP.NET Core MVC</span></span>

<span data-ttu-id="ef6fa-105">으로 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="ef6fa-105">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="ef6fa-106">ASP.NET Core MVC에서 응답 데이터를 고정된 형식을 사용 하 여 서식을 지정 하기 위한 또는 클라이언트 사양에 대 한 응답에서 기본적으로 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef6fa-106">ASP.NET Core MVC has built-in support for formatting response data, using fixed formats or in response to client specifications.</span></span>

<span data-ttu-id="ef6fa-107">[보기 또는 GitHub에서 샘플을 다운로드](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/models/formatting/sample)합니다.</span><span class="sxs-lookup"><span data-stu-id="ef6fa-107">[View or download sample from GitHub](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/models/formatting/sample).</span></span>

## <a name="format-specific-action-results"></a><span data-ttu-id="ef6fa-108">형식에 따른 작업 결과</span><span class="sxs-lookup"><span data-stu-id="ef6fa-108">Format-Specific Action Results</span></span>

<span data-ttu-id="ef6fa-109">일부 작업 결과 유형은 특정 형식에 고유한와 같은 `JsonResult` 및 `ContentResult`합니다.</span><span class="sxs-lookup"><span data-stu-id="ef6fa-109">Some action result types are specific to a particular format, such as `JsonResult` and `ContentResult`.</span></span> <span data-ttu-id="ef6fa-110">작업은 항상 특정 한 방식으로 형식이 지정 된 특정 결과 반환할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ef6fa-110">Actions can return specific results that are always formatted in a particular manner.</span></span> <span data-ttu-id="ef6fa-111">예를 들어 반환 된 `JsonResult` 클라이언트 기본 설정에 관계 없이 JSON 형식의 데이터를 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef6fa-111">For example, returning a `JsonResult` will return JSON-formatted data, regardless of client preferences.</span></span> <span data-ttu-id="ef6fa-112">마찬가지로, 반환 된 `ContentResult` (단순히 문자열을 반환 합니다)으로 일반 텍스트 형식 문자열 데이터를 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef6fa-112">Likewise, returning a `ContentResult` will return plain-text-formatted string data (as will simply returning a string).</span></span>

> [!NOTE]
> <span data-ttu-id="ef6fa-113">특정 형식 제한; 반환 하는 작업 필요 하지 않습니다. MVC는 모든 개체 반환 값을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="ef6fa-113">An action isn't required to return any particular type; MVC supports any object return value.</span></span> <span data-ttu-id="ef6fa-114">작업에서 반환 하는 경우는 `IActionResult` 구현과 컨트롤러에서 상속 `Controller`, 개발자는 선택 사항 중 많은 부분에 해당 하는 많은 도우미 메서드.</span><span class="sxs-lookup"><span data-stu-id="ef6fa-114">If an action returns an `IActionResult` implementation and the controller inherits from `Controller`, developers have many helper methods corresponding to many of the choices.</span></span> <span data-ttu-id="ef6fa-115">개체를 반환 하는 작업의 결과는 없는 `IActionResult` 적절 한 사용 하 여 형식을 serialize 할 `IOutputFormatter` 구현 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef6fa-115">Results from actions that return objects that are not `IActionResult` types will be serialized using the appropriate `IOutputFormatter` implementation.</span></span>

<span data-ttu-id="ef6fa-116">상속 되는 컨트롤러에서 특정 형식의 데이터를 반환 하는 `Controller` 기본 클래스, 기본 제공 도우미 메서드를 사용 `Json` JSON을 반환 하 고 `Content` 일반 텍스트에 대 한 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef6fa-116">To return data in a specific format from a controller that inherits from the `Controller` base class, use the built-in helper method `Json` to return JSON and `Content` for plain text.</span></span> <span data-ttu-id="ef6fa-117">동작 메서드에서 특정 결과 형식을 반환 해야 (예를 들어, `JsonResult`) 또는 `IActionResult`합니다.</span><span class="sxs-lookup"><span data-stu-id="ef6fa-117">Your action method should return either the specific result type (for instance, `JsonResult`) or `IActionResult`.</span></span>

<span data-ttu-id="ef6fa-118">JSON 형식의 데이터를 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef6fa-118">Returning JSON-formatted data:</span></span>

<span data-ttu-id="ef6fa-119">[!code-csharp[Main](./formatting/sample/Controllers/Api/AuthorsController.cs?highlight=3,5&range=21-26)]</span><span class="sxs-lookup"><span data-stu-id="ef6fa-119">[!code-csharp[Main](./formatting/sample/Controllers/Api/AuthorsController.cs?highlight=3,5&range=21-26)]</span></span>

<span data-ttu-id="ef6fa-120">이 작업에서 샘플 응답:</span><span class="sxs-lookup"><span data-stu-id="ef6fa-120">Sample response from this action:</span></span>

![네트워크 탭에서 Microsoft Edge 응답의 콘텐츠 형식을 보여 주는 개발자 도구의 application/json](formatting/_static/json-response.png)

<span data-ttu-id="ef6fa-122">응답의 콘텐츠 형식을 `application/json`와 응답 헤더 섹션 모두의 네트워크 요청 목록에 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef6fa-122">Note that the content type of the response is `application/json`, shown both in the list of network requests and in the Response Headers section.</span></span> <span data-ttu-id="ef6fa-123">또한 note 요청 헤더 섹션에서 Accept 헤더에서에 (이 경우, Microsoft Edge) 브라우저에서 제공 되는 옵션 목록입니다.</span><span class="sxs-lookup"><span data-stu-id="ef6fa-123">Also note the list of options presented by the browser (in this case, Microsoft Edge) in the Accept header in the Request Headers section.</span></span> <span data-ttu-id="ef6fa-124">기술을이 헤더를 무시 하 엑세스 그 아래에 설명 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ef6fa-124">The current technique is ignoring this header; obeying it is discussed below.</span></span>

<span data-ttu-id="ef6fa-125">사용 하 여 서식이 지정 된 일반 텍스트 데이터를 반환 하려면 `ContentResult` 및 `Content` 도우미:</span><span class="sxs-lookup"><span data-stu-id="ef6fa-125">To return plain text formatted data, use `ContentResult` and the `Content` helper:</span></span>

<span data-ttu-id="ef6fa-126">[!code-csharp[Main](./formatting/sample/Controllers/Api/AuthorsController.cs?highlight=3,5&range=47-52)]</span><span class="sxs-lookup"><span data-stu-id="ef6fa-126">[!code-csharp[Main](./formatting/sample/Controllers/Api/AuthorsController.cs?highlight=3,5&range=47-52)]</span></span>

<span data-ttu-id="ef6fa-127">이 작업의 응답:</span><span class="sxs-lookup"><span data-stu-id="ef6fa-127">A response from this action:</span></span>

![Microsoft Edge 응답의 콘텐츠 형식을 보여 주는 개발자 도구의 네트워크 탭은 텍스트/일반](formatting/_static/text-response.png)

<span data-ttu-id="ef6fa-129">이 경우는 `Content-Type` 반환 된 `text/plain`합니다.</span><span class="sxs-lookup"><span data-stu-id="ef6fa-129">Note in this case the `Content-Type` returned is `text/plain`.</span></span> <span data-ttu-id="ef6fa-130">또한는 문자열 응답 유형을 사용 하 여이 동작을 얻을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ef6fa-130">You can also achieve this same behavior using just a string response type:</span></span>

<span data-ttu-id="ef6fa-131">[!code-csharp[Main](./formatting/sample/Controllers/Api/AuthorsController.cs?highlight=3,5&range=54-59)]</span><span class="sxs-lookup"><span data-stu-id="ef6fa-131">[!code-csharp[Main](./formatting/sample/Controllers/Api/AuthorsController.cs?highlight=3,5&range=54-59)]</span></span>

>[!TIP]
> <span data-ttu-id="ef6fa-132">여러 개 포함 된 특정 작업에 대 한 반환 형식 또는 옵션 (예: 수행 되는 작업의 결과에 따라 다른 HTTP 상태 코드), 원하는 `IActionResult` 반환 형식으로.</span><span class="sxs-lookup"><span data-stu-id="ef6fa-132">For non-trivial actions with multiple return types or options (for example, different HTTP status codes based on the result of operations performed), prefer `IActionResult` as the return type.</span></span>

## <a name="content-negotiation"></a><span data-ttu-id="ef6fa-133">콘텐츠 협상</span><span class="sxs-lookup"><span data-stu-id="ef6fa-133">Content Negotiation</span></span>

<span data-ttu-id="ef6fa-134">콘텐츠 협상 (*conneg* 줄여서) 클라이언트를 지정 하는 경우에 발생 한 [Accept 헤더](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html)합니다.</span><span class="sxs-lookup"><span data-stu-id="ef6fa-134">Content negotiation (*conneg* for short) occurs when the client specifies an [Accept header](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).</span></span> <span data-ttu-id="ef6fa-135">ASP.NET Core MVC에서 사용 하는 기본 형식은 JSON입니다.</span><span class="sxs-lookup"><span data-stu-id="ef6fa-135">The default format used by ASP.NET Core MVC is JSON.</span></span> <span data-ttu-id="ef6fa-136">콘텐츠 협상에 의해 구현 됩니다 `ObjectResult`합니다.</span><span class="sxs-lookup"><span data-stu-id="ef6fa-136">Content negotiation is implemented by `ObjectResult`.</span></span> <span data-ttu-id="ef6fa-137">또한 도우미 메서드에서 반환 된 특정 작업 결과 상태 코드에 기본적으로 (모두를 기반으로 하 `ObjectResult`).</span><span class="sxs-lookup"><span data-stu-id="ef6fa-137">It is also built into the status code specific action results returned from the helper methods (which are all based on `ObjectResult`).</span></span> <span data-ttu-id="ef6fa-138">모델 형식 (데이터 전송 형식으로 정의 된 클래스)을 반환할 수 있습니다 및 프레임 워크는 자동으로 줄 바꿈이는 `ObjectResult` 드립니다.</span><span class="sxs-lookup"><span data-stu-id="ef6fa-138">You can also return a model type (a class you've defined as your data transfer type) and the framework will automatically wrap it in an `ObjectResult` for you.</span></span>

<span data-ttu-id="ef6fa-139">다음 작업 메서드에서 사용 하는 `Ok` 및 `NotFound` 도우미 메서드.</span><span class="sxs-lookup"><span data-stu-id="ef6fa-139">The following action method uses the `Ok` and `NotFound` helper methods:</span></span>

<span data-ttu-id="ef6fa-140">[!code-csharp[Main](./formatting/sample/Controllers/Api/AuthorsController.cs?highlight=8,10&range=28-38)]</span><span class="sxs-lookup"><span data-stu-id="ef6fa-140">[!code-csharp[Main](./formatting/sample/Controllers/Api/AuthorsController.cs?highlight=8,10&range=28-38)]</span></span>

<span data-ttu-id="ef6fa-141">다른 형식 요청 하 고 서버에 요청된 된 형식을 반환 수 하지 않으면 JSON 형식의 응답 반환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ef6fa-141">A JSON-formatted response will be returned unless another format was requested and the server can return the requested format.</span></span> <span data-ttu-id="ef6fa-142">와 같은 도구를 사용할 수 있습니다 [Fiddler](http://www.telerik.com/fiddler) 를 Accept 헤더를 포함 하는 요청을 만들고 다른 형식을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef6fa-142">You can use a tool like [Fiddler](http://www.telerik.com/fiddler) to create a request that includes an Accept header and specify another format.</span></span> <span data-ttu-id="ef6fa-143">서버에 있는 경우 해당 경우에는 *포맷터* 요청 된 형식으로 응답을 생성할 수 있는, 결과에 반환 됩니다 클라이언트 기본 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="ef6fa-143">In that case, if the server has a *formatter* that can produce a response in the requested format, the result will be returned in the client-preferred format.</span></span>

![수동으로 만든를 보여 주는 fiddler 콘솔 GET 요청 Accept 헤더 값이 응용 프로그램/x m l](formatting/_static/fiddler-composer.png)

<span data-ttu-id="ef6fa-145">위의 스크린 샷에서 요청을 생성 하는 Fiddler 작성기 사용 되었습니다 지정 `Accept: application/xml`합니다.</span><span class="sxs-lookup"><span data-stu-id="ef6fa-145">In the above screenshot, the Fiddler Composer has been used to generate a request, specifying `Accept: application/xml`.</span></span> <span data-ttu-id="ef6fa-146">기본적으로 ASP.NET Core MVC만 지원 JSON, 하더라도 반환 결과 여전히 JSON 형식 수 다른 format을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef6fa-146">By default, ASP.NET Core MVC only supports JSON, so even when another format is specified, the result returned is still JSON-formatted.</span></span> <span data-ttu-id="ef6fa-147">다음 섹션에 추가 하는 포맷터를 추가 하는 방법을 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ef6fa-147">You'll see how to add additional formatters in the next section.</span></span>

<span data-ttu-id="ef6fa-148">컨트롤러 작업을 반환할 수 있습니다 (일반 이전 CLR 개체), POCOs ASP.NET MVC에서 자동으로 만듭니다는 쿼리에서 `ObjectResult` 을 해당 개체를 래핑합니다.</span><span class="sxs-lookup"><span data-stu-id="ef6fa-148">Controller actions can return POCOs (Plain Old CLR Objects), in which case ASP.NET MVC will automatically create an `ObjectResult` for you that wraps the object.</span></span> <span data-ttu-id="ef6fa-149">클라이언트는 서식이 지정 된 직렬화 된 개체를 발생 합니다 (JSON 형식은 기본, XML 또는 기타 형식을 구성할 수 있습니다).</span><span class="sxs-lookup"><span data-stu-id="ef6fa-149">The client will get the formatted serialized object (JSON format is the default; you can configure XML or other formats).</span></span> <span data-ttu-id="ef6fa-150">가 반환 되는 개체 `null`, 면 프레임 워크는 반환 된 `204 No Content` 응답 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef6fa-150">If the object being returned is `null`, then the framework will return a `204 No Content` response.</span></span>

<span data-ttu-id="ef6fa-151">개체 유형을 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef6fa-151">Returning an object type:</span></span>

<span data-ttu-id="ef6fa-152">[!code-csharp[Main](./formatting/sample/Controllers/Api/AuthorsController.cs?highlight=3&range=40-45)]</span><span class="sxs-lookup"><span data-stu-id="ef6fa-152">[!code-csharp[Main](./formatting/sample/Controllers/Api/AuthorsController.cs?highlight=3&range=40-45)]</span></span>

<span data-ttu-id="ef6fa-153">이 샘플에서는 유효한 작성자 별칭에 대 한 요청 작성자의 데이터로 200 정상 응답이 받게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ef6fa-153">In the sample, a request for a valid author alias will receive a 200 OK response with the author's data.</span></span> <span data-ttu-id="ef6fa-154">잘못 된 별칭에 대 한 요청 204 콘텐츠 없음 응답을 받게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ef6fa-154">A request for an invalid alias will receive a 204 No Content response.</span></span> <span data-ttu-id="ef6fa-155">XML 및 JSON 형식의 응답을 보여 주는 스크린샷 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="ef6fa-155">Screenshots showing the response in XML and JSON formats are shown below.</span></span>

### <a name="content-negotiation-process"></a><span data-ttu-id="ef6fa-156">콘텐츠 협상 프로세스</span><span class="sxs-lookup"><span data-stu-id="ef6fa-156">Content Negotiation Process</span></span>

<span data-ttu-id="ef6fa-157">콘텐츠 *협상* 만 수행한 경우는 `Accept` 요청에 헤더를 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef6fa-157">Content *negotiation* only takes place if an `Accept` header appears in the request.</span></span> <span data-ttu-id="ef6fa-158">요청에 accept 헤더가 포함 되어 있으면, 프레임 워크 기본 설정 순서 대로 accept 헤더의 미디어 유형을 열거 합니다 및 accept 헤더에 의해 지정 된 형식 중 하나에 대 한 응답을 생성할 수 있는 포맷터를 찾으려고 시도 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef6fa-158">When a request contains an accept header, the framework will enumerate the media types in the accept header in preference order and will try to find a formatter that can produce a response in one of the formats specified by the accept header.</span></span> <span data-ttu-id="ef6fa-159">프레임 워크 응답을 생성할 수 있는 첫 번째 포맷터를 찾으려고 시도할 경우 클라이언트의 요청을 충족 시킬 수 있는 포맷터를 발견 되 면 (개발자가에서 옵션을 구성 하지 않는 한 `MvcOptions` 406 반환할 승인 금지 대신).</span><span class="sxs-lookup"><span data-stu-id="ef6fa-159">In case no formatter is found that can satisfy the client's request, the framework will try to find the first formatter that can produce a response (unless the developer has configured the option on `MvcOptions` to return 406 Not Acceptable instead).</span></span> <span data-ttu-id="ef6fa-160">이 요청에는 XML을 지정 하는 경우 XML 포맷터 구성 되지 않았습니다. JSON 포맷터 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ef6fa-160">If the request specifies XML, but the XML formatter has not been configured, then the JSON formatter will be used.</span></span> <span data-ttu-id="ef6fa-161">보다 일반적으로 포맷터를 구성 된 경우 요청 된 형식으로 제공할 수 있는 다음 개체의 서식을 지정할 수 있는 보다 첫 번째 포맷터가 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef6fa-161">More generally, if no formatter is configured that can provide the requested format, then the first formatter than can format the object is used.</span></span> <span data-ttu-id="ef6fa-162">헤더가 없으면이 지정 되 면 반환 될 개체를 처리할 수 있는 첫 번째 포맷터는 응답을 직렬화 하는 데 사용될지 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef6fa-162">If no header is given, the first formatter that can handle the object to be returned will be used to serialize the response.</span></span> <span data-ttu-id="ef6fa-163">이 경우 모든 협상이 발생 하지 않으므로-서버 결정할 때 알고리즘 형식을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef6fa-163">In this case, there isn't any negotiation taking place - the server is determining what format it will use.</span></span>

> [!NOTE]
> <span data-ttu-id="ef6fa-164">Accept 헤더를 포함 하는 경우 `*/*`, 머리글은 무시 됩니다 `RespectBrowserAcceptHeader` 로 설정 하면 true이 고 `MvcOptions`합니다.</span><span class="sxs-lookup"><span data-stu-id="ef6fa-164">If the Accept header contains `*/*`, the Header will be ignored unless `RespectBrowserAcceptHeader` is set to true on `MvcOptions`.</span></span>

### <a name="browsers-and-content-negotiation"></a><span data-ttu-id="ef6fa-165">브라우저 및 콘텐츠 협상</span><span class="sxs-lookup"><span data-stu-id="ef6fa-165">Browsers and Content Negotiation</span></span>

<span data-ttu-id="ef6fa-166">일반적인 API 클라이언트와 달리 웹 브라우저에 제공 하는 하는 경향이 `Accept` 다양 한 종류의 와일드 카드를 포함 한 형식으로 포함 하는 헤더입니다.</span><span class="sxs-lookup"><span data-stu-id="ef6fa-166">Unlike typical API clients, web browsers tend to supply `Accept` headers that include a wide array of formats, including wildcards.</span></span> <span data-ttu-id="ef6fa-167">기본적으로 프레임 워크는 브라우저에서 요청 된을 감지할 때 무시 됩니다는 `Accept` 헤더 구성과 대신 반환 콘텐츠 응용 프로그램에서의 기본 형식 (JSON 달리 구성 하지 않는 한).</span><span class="sxs-lookup"><span data-stu-id="ef6fa-167">By default, when the framework detects that the request is coming from a browser, it will ignore the `Accept` header and instead return the content in the application's configured default format (JSON unless otherwise configured).</span></span> <span data-ttu-id="ef6fa-168">이 Api를 사용 하 여 다양 한 브라우저를 사용 하는 경우는 보다 일관 된 환경을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef6fa-168">This provides a more consistent experience when using different browsers to consume APIs.</span></span>

<span data-ttu-id="ef6fa-169">응용 프로그램 honor 브라우저 accept 헤더가 사용 하려는 경우 구성할 수 있습니다이 MVC의 구성의 일부분으로 설정 하 여 `RespectBrowserAcceptHeader` 를 `true` 에 `ConfigureServices` 메서드에서 *Startup.cs*합니다.</span><span class="sxs-lookup"><span data-stu-id="ef6fa-169">If you would prefer your application honor browser accept headers, you can configure this as part of MVC's configuration by setting `RespectBrowserAcceptHeader` to `true` in the `ConfigureServices` method in *Startup.cs*.</span></span>

```csharp
services.AddMvc(options =>
{
    options.RespectBrowserAcceptHeader = true; // false by default
}
```

## <a name="configuring-formatters"></a><span data-ttu-id="ef6fa-170">포맷터를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="ef6fa-170">Configuring Formatters</span></span>

<span data-ttu-id="ef6fa-171">JSON의 기본값을 초과 하는 추가 형식을 지원 하기 위해 응용 프로그램에 필요한 경우 NuGet 패키지 추가 구성 하는 MVC를 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef6fa-171">If your application needs to support additional formats beyond the default of JSON, you can add NuGet packages and configure MVC to support them.</span></span> <span data-ttu-id="ef6fa-172">입력 및 출력에 대 한 별도 포맷터 가지가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ef6fa-172">There are separate formatters for input and output.</span></span> <span data-ttu-id="ef6fa-173">입력된 포맷터에서 사용 [모델 바인딩](model-binding.md); 출력 포맷터는 응답 형식을 지정 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ef6fa-173">Input formatters are used by [Model Binding](model-binding.md); output formatters are used to format responses.</span></span> <span data-ttu-id="ef6fa-174">구성할 수 있습니다 [사용자 지정 포맷터](../advanced/custom-formatters.md)합니다.</span><span class="sxs-lookup"><span data-stu-id="ef6fa-174">You can also configure [Custom Formatters](../advanced/custom-formatters.md).</span></span>

### <a name="adding-xml-format-support"></a><span data-ttu-id="ef6fa-175">XML 형식 지원 추가</span><span class="sxs-lookup"><span data-stu-id="ef6fa-175">Adding XML Format Support</span></span>

<span data-ttu-id="ef6fa-176">XML 서식 지정에 대 한 지원을 추가할 설치는 `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet 패키지 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef6fa-176">To add support for XML formatting, install the `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet package.</span></span>

<span data-ttu-id="ef6fa-177">MVC의 구성에는 XmlSerializerFormatters 추가 *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="ef6fa-177">Add the XmlSerializerFormatters to MVC's configuration in *Startup.cs*:</span></span>

<span data-ttu-id="ef6fa-178">[!code-csharp[Main](./formatting/sample/Startup.cs?name=snippet1&highlight=2)]</span><span class="sxs-lookup"><span data-stu-id="ef6fa-178">[!code-csharp[Main](./formatting/sample/Startup.cs?name=snippet1&highlight=2)]</span></span>

<span data-ttu-id="ef6fa-179">또는 출력 포맷터만 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ef6fa-179">Alternately, you can add just the output formatter:</span></span>

```csharp
services.AddMvc(options =>
{
    options.OutputFormatters.Add(new XmlSerializerOutputFormatter());
});
```

<span data-ttu-id="ef6fa-180">이러한 두 방법을 사용 하 여 결과 serialize `System.Xml.Serialization.XmlSerializer`합니다.</span><span class="sxs-lookup"><span data-stu-id="ef6fa-180">These two approaches will serialize results using `System.Xml.Serialization.XmlSerializer`.</span></span> <span data-ttu-id="ef6fa-181">원하는 경우 사용할 수 있습니다는 `System.Runtime.Serialization.DataContractSerializer` 해당 연결 된 포맷터를 추가 하 여:</span><span class="sxs-lookup"><span data-stu-id="ef6fa-181">If you prefer, you can use the `System.Runtime.Serialization.DataContractSerializer` by adding its associated formatter:</span></span>

```csharp
services.AddMvc(options =>
{
    options.OutputFormatters.Add(new XmlDataContractSerializerOutputFormatter());
});
```

<span data-ttu-id="ef6fa-182">컨트롤러 메서드는 요청에 따라 적절 한 형식을 반환 해야 XML 서식 지정에 대 한 지원을 추가 하면 `Accept` 헤더 예제에서는이 Fiddler로:</span><span class="sxs-lookup"><span data-stu-id="ef6fa-182">Once you've added support for XML formatting, your controller methods should return the appropriate format based on the request's `Accept` header, as this Fiddler example demonstrates:</span></span>

![Fiddler 콘솔: 요청에 대 한 원시 The 탭 Accept 헤더 값은 application/xml에 표시 합니다.](formatting/_static/xml-response.png)

<span data-ttu-id="ef6fa-185">원시 GET 요청이 사용 하 고 검사기 탭에서 볼 수는 `Accept: application/xml` 헤더 집합입니다.</span><span class="sxs-lookup"><span data-stu-id="ef6fa-185">You can see in the Inspectors tab that the Raw GET request was made with an `Accept: application/xml` header set.</span></span> <span data-ttu-id="ef6fa-186">창에 표시 된 응답의 `Content-Type: application/xml` 헤더로, 및 `Author` XML로 serialize 된 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="ef6fa-186">The response pane shows the `Content-Type: application/xml` header, and the `Author` object has been serialized to XML.</span></span>

<span data-ttu-id="ef6fa-187">지정 하는 요청을 수정 하려면 Composer 탭을 사용 하 여 `application/json` 에서 `Accept` 헤더입니다.</span><span class="sxs-lookup"><span data-stu-id="ef6fa-187">Use the Composer tab to modify the request to specify `application/json` in the `Accept` header.</span></span> <span data-ttu-id="ef6fa-188">요청을 실행 하 고 응답 JSON으로 형식이 지정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ef6fa-188">Execute the request, and the response will be formatted as JSON:</span></span>

![Fiddler 콘솔: Accept 헤더 값은 application/json 요청에 대 한 원시의 탭에 표시 합니다.](formatting/_static/json-response-fiddler.png)

<span data-ttu-id="ef6fa-191">이 스크린 샷에 볼 수 있습니다의 헤더를 설정 하는 요청 `Accept: application/json` 응답와 동일 하 게 지정 하 고 해당 `Content-Type`합니다.</span><span class="sxs-lookup"><span data-stu-id="ef6fa-191">In this screenshot, you can see the request sets a header of `Accept: application/json` and the response specifies the same as its `Content-Type`.</span></span> <span data-ttu-id="ef6fa-192">`Author` 개체의 JSON 형식의 응답 본문에 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ef6fa-192">The `Author` object is shown in the body of the response, in JSON format.</span></span>

### <a name="forcing-a-particular-format"></a><span data-ttu-id="ef6fa-193">특정 형식 적용</span><span class="sxs-lookup"><span data-stu-id="ef6fa-193">Forcing a Particular Format</span></span>

<span data-ttu-id="ef6fa-194">특정 작업에 대 한 응답 형식을 제한 하려는 경우 할 수 있습니다, 적용할 수는 `[Produces]` 필터입니다.</span><span class="sxs-lookup"><span data-stu-id="ef6fa-194">If you would like to restrict the response formats for a specific action you can, you can apply the `[Produces]` filter.</span></span> <span data-ttu-id="ef6fa-195">`[Produces]` 필터는 특정 작업 (또는 컨트롤러)에 대 한 응답 형식을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef6fa-195">The `[Produces]` filter specifies the response formats for a specific action (or controller).</span></span> <span data-ttu-id="ef6fa-196">마찬가지로 대부분 [필터](../controllers/filters.md), 작업, 컨트롤러 또는 전역 범위에 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ef6fa-196">Like most [Filters](../controllers/filters.md), this can be applied at the action, controller, or global scope.</span></span>

```csharp
[Produces("application/json")]
public class AuthorsController
```

<span data-ttu-id="ef6fa-197">`[Produces]` 필터 내에서 모든 작업을 강제로 `AuthorsController` 다른 포맷터는 응용 프로그램 및 제공 된 클라이언트에 대해 구성 된 경우에 JSON 형식의 응답을 반환 하는 `Accept` 헤더를 요청 하는 다른 사용 가능한 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="ef6fa-197">The `[Produces]` filter will force all actions within the `AuthorsController` to return JSON-formatted responses, even if other formatters were configured for the application and the client provided an `Accept` header requesting a different, available format.</span></span> <span data-ttu-id="ef6fa-198">참조 [필터](../controllers/filters.md) 필터를 전역으로 적용 하는 방법을 비롯 하 여 자세한 내용을 보려면 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef6fa-198">See [Filters](../controllers/filters.md) to learn more, including how to apply filters globally.</span></span>

### <a name="special-case-formatters"></a><span data-ttu-id="ef6fa-199">특수 한 사례 포맷터</span><span class="sxs-lookup"><span data-stu-id="ef6fa-199">Special Case Formatters</span></span>

<span data-ttu-id="ef6fa-200">일부 특수 한 경우 기본 제공 포맷터를 사용 하 여 구현 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ef6fa-200">Some special cases are implemented using built-in formatters.</span></span> <span data-ttu-id="ef6fa-201">기본적으로 `string` 반환 형식으로 형식이 지정 됩니다 *텍스트/일반* (*텍스트/html* 통해 요청 된 경우 `Accept` 헤더).</span><span class="sxs-lookup"><span data-stu-id="ef6fa-201">By default, `string` return types will be formatted as *text/plain* (*text/html* if requested via `Accept` header).</span></span> <span data-ttu-id="ef6fa-202">이 문제를 제거 하 여 제거할 수는 `TextOutputFormatter`합니다.</span><span class="sxs-lookup"><span data-stu-id="ef6fa-202">This behavior can be removed by removing the `TextOutputFormatter`.</span></span> <span data-ttu-id="ef6fa-203">포맷터에서 제거는 `Configure` 에서 메서드 *Startup.cs* (아래 참조).</span><span class="sxs-lookup"><span data-stu-id="ef6fa-203">You remove formatters in the `Configure` method in *Startup.cs* (shown below).</span></span> <span data-ttu-id="ef6fa-204">모델 개체에 영향을 주는 작업 반환 형식을 반환 합니다는 204 콘텐츠 없음 응답 반환 하는 경우 `null`합니다.</span><span class="sxs-lookup"><span data-stu-id="ef6fa-204">Actions that have a model object return type will return a 204 No Content response when returning `null`.</span></span> <span data-ttu-id="ef6fa-205">이 문제를 제거 하 여 제거할 수는 `HttpNoContentOutputFormatter`합니다.</span><span class="sxs-lookup"><span data-stu-id="ef6fa-205">This behavior can be removed by removing the `HttpNoContentOutputFormatter`.</span></span> <span data-ttu-id="ef6fa-206">다음 코드를 제거는 `TextOutputFormatter` 및 `HttpNoContentOutputFormatter`합니다.</span><span class="sxs-lookup"><span data-stu-id="ef6fa-206">The following code removes the `TextOutputFormatter` and `HttpNoContentOutputFormatter`.</span></span>

```csharp
services.AddMvc(options =>
{
    options.OutputFormatters.RemoveType<TextOutputFormatter>();
    options.OutputFormatters.RemoveType<HttpNoContentOutputFormatter>();
});
```

<span data-ttu-id="ef6fa-207">없이 `TextOutputFormatter`, `string` 406를 반환 하는 형식 반환 합니다. 예를 들어 승인 금지 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef6fa-207">Without the `TextOutputFormatter`, `string` return types return 406 Not Acceptable, for example.</span></span> <span data-ttu-id="ef6fa-208">XML 포맷터 있으면 것은 서식을 참고 `string` 경우 반환 형식에서 `TextOutputFormatter` 제거 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ef6fa-208">Note that if an XML formatter exists, it will format `string` return types if the `TextOutputFormatter` is removed.</span></span>

<span data-ttu-id="ef6fa-209">없이 `HttpNoContentOutputFormatter`, null 개체는 구성 된 포맷터를 사용 하 여 서식이 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef6fa-209">Without the `HttpNoContentOutputFormatter`, null objects are formatted using the configured formatter.</span></span> <span data-ttu-id="ef6fa-210">예를 들어 JSON 포맷터는 반환 된 응답의 본문으로 `null`XML 포맷터는 특성으로 빈 XML 요소를 반환 하는 반면, `xsi:nil="true"` 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef6fa-210">For example, the JSON formatter will simply return a response with a body of `null`, while the XML formatter will return an empty XML element with the attribute `xsi:nil="true"` set.</span></span>

## <a name="response-format-url-mappings"></a><span data-ttu-id="ef6fa-211">응답 형식 URL 매핑</span><span class="sxs-lookup"><span data-stu-id="ef6fa-211">Response Format URL Mappings</span></span>

<span data-ttu-id="ef6fa-212">클라이언트 쿼리 문자열 또는 일부는 경로의 또는.xml 또는.json 등의 형식에 따른 파일 확장명을 사용 하 여 특정 형식을 URL의 일부로 같은 요청할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ef6fa-212">Clients can request a particular format as part of the URL, such as in the query string or part of the path, or by using a format-specific file extension such as .xml or .json.</span></span> <span data-ttu-id="ef6fa-213">요청 경로에서 매핑은 API를 사용 하 여 경로에 지정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef6fa-213">The mapping from request path should be specified in the route the API is using.</span></span> <span data-ttu-id="ef6fa-214">예:</span><span class="sxs-lookup"><span data-stu-id="ef6fa-214">For example:</span></span>

```csharp
[FormatFilter]
public class ProductsController
{
    [Route("[controller]/[action]/{id}.{format?}")]
    public Product GetById(int id)
```

<span data-ttu-id="ef6fa-215">이 경로 사용 하면 요청 된 형식 선택적 파일 확장명으로 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ef6fa-215">This route would allow the requested format to be specified as an optional file extension.</span></span> <span data-ttu-id="ef6fa-216">`[FormatFilter]` 특성에 형식 값의 존재 여부를 검사는 `RouteData` 응답 만들어질 때 응답 형식을 적절 한 포맷터에 매핑합니다.</span><span class="sxs-lookup"><span data-stu-id="ef6fa-216">The `[FormatFilter]` attribute checks for the existence of the format value in the `RouteData` and will map the response format to the appropriate formatter when the response is created.</span></span>

| <span data-ttu-id="ef6fa-217">경로</span><span class="sxs-lookup"><span data-stu-id="ef6fa-217">Route</span></span>                      | <span data-ttu-id="ef6fa-218">포맷터</span><span class="sxs-lookup"><span data-stu-id="ef6fa-218">Formatter</span></span>                          |
| -------------------------- | ---------------------------------- |
| `/products/GetById/5`      | <span data-ttu-id="ef6fa-219">기본 출력 포맷터</span><span class="sxs-lookup"><span data-stu-id="ef6fa-219">The default output formatter</span></span>       |
| `/products/GetById/5.json` | <span data-ttu-id="ef6fa-220">JSON 포맷터 (구성 된 경우)</span><span class="sxs-lookup"><span data-stu-id="ef6fa-220">The JSON formatter (if configured)</span></span> |
| `/products/GetById/5.xml`  | <span data-ttu-id="ef6fa-221">XML 포맷터 (구성 된 경우)</span><span class="sxs-lookup"><span data-stu-id="ef6fa-221">The XML formatter (if configured)</span></span>  |
