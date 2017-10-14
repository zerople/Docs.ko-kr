---
title: "크로스-원본 요청 (CORS)를 사용 하도록 설정"
author: rick-anderson
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 05/17/2017
ms.topic: article
ms.assetid: f9d95e88-4d7e-4d0c-a8e1-47de1128d505
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/cors
ms.openlocfilehash: 0d1c34f5c82fe1a28a405617ea77084db899139b
ms.sourcegitcommit: 8f4d4fad1ca27adf9e396f5c205c9875a3963664
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/13/2017
---
# <a name="enabling-cross-origin-requests-cors"></a><span data-ttu-id="eff02-103">크로스-원본 요청 (CORS)를 사용 하도록 설정</span><span class="sxs-lookup"><span data-stu-id="eff02-103">Enabling Cross-Origin Requests (CORS)</span></span>

<span data-ttu-id="eff02-104">여 [Mike Wasson](https://github.com/mikewasson), [Shayne 보이 어](https://twitter.com/spboyer), 및 [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="eff02-104">By [Mike Wasson](https://github.com/mikewasson), [Shayne Boyer](https://twitter.com/spboyer), and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="eff02-105">브라우저 보안 다른 도메인에 대 한 AJAX 요청에서 웹 페이지를 방지 합니다.</span><span class="sxs-lookup"><span data-stu-id="eff02-105">Browser security prevents a web page from making AJAX requests to another domain.</span></span> <span data-ttu-id="eff02-106">이 제한은 라고는 *동일 원본 정책*, 악성 사이트를 다른 사이트에서 중요 한 데이터를 읽을 수 없도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="eff02-106">This restriction is called the *same-origin policy*, and prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="eff02-107">하지만, 경우에 따라 다른 사이트 web API 대 크로스-원본 요청을 만들 수 있도록 필요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="eff02-107">However, sometimes you might want to let other sites make cross-origin requests to your web API.</span></span>

<span data-ttu-id="eff02-108">[교차 원본 자원 공유](http://www.w3.org/TR/cors/) (CORS)는 동일 원본 정책을 완화 하도록 서버를 허용 하는 W3C 표준입니다.</span><span class="sxs-lookup"><span data-stu-id="eff02-108">[Cross Origin Resource Sharing](http://www.w3.org/TR/cors/) (CORS) is a W3C standard that allows a server to relax the same-origin policy.</span></span> <span data-ttu-id="eff02-109">CORS를 사용 하는 서버는 다른 사용자를 거부 하는 동안 일부 크로스-원본 요청을 명시적으로 허용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="eff02-109">Using CORS, a server can explicitly allow some cross-origin requests while rejecting others.</span></span> <span data-ttu-id="eff02-110">과 같은 CORS를 안전 하 고 이전 기술을 보다 좀 더 융통적 [JSONP](https://wikipedia.org/wiki/JSONP)합니다.</span><span class="sxs-lookup"><span data-stu-id="eff02-110">CORS is safer and more flexible than earlier techniques such as [JSONP](https://wikipedia.org/wiki/JSONP).</span></span> <span data-ttu-id="eff02-111">이 항목에서는 ASP.NET Core 응용 프로그램에서 CORS를 사용 하도록 설정 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="eff02-111">This topic shows how to enable CORS in an ASP.NET Core application.</span></span>

## <a name="what-is-same-origin"></a><span data-ttu-id="eff02-112">"같은 출처" 이란?</span><span class="sxs-lookup"><span data-stu-id="eff02-112">What is "same origin"?</span></span>

<span data-ttu-id="eff02-113">두 Url에는 동일한 구성표, 호스트 및 포트 있는 경우 동일한 원본이 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="eff02-113">Two URLs have the same origin if they have identical schemes, hosts, and ports.</span></span> <span data-ttu-id="eff02-114">([RFC 6454](http://tools.ietf.org/html/rfc6454))</span><span class="sxs-lookup"><span data-stu-id="eff02-114">([RFC 6454](http://tools.ietf.org/html/rfc6454))</span></span>

<span data-ttu-id="eff02-115">이러한 두 Url에는 동일한 원본이 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="eff02-115">These two URLs have the same origin:</span></span>

* `http://example.com/foo.html`

* `http://example.com/bar.html`

<span data-ttu-id="eff02-116">이러한 Url 있는 두 이전 보다 다른 출처:</span><span class="sxs-lookup"><span data-stu-id="eff02-116">These URLs have different origins than the previous two:</span></span>

* <span data-ttu-id="eff02-117">`http://example.net`-다른 도메인</span><span class="sxs-lookup"><span data-stu-id="eff02-117">`http://example.net` - Different domain</span></span>

* <span data-ttu-id="eff02-118">`http://www.example.com/foo.html`-다른 하위 도메인</span><span class="sxs-lookup"><span data-stu-id="eff02-118">`http://www.example.com/foo.html` - Different subdomain</span></span>

* <span data-ttu-id="eff02-119">`https://example.com/foo.html`-다른 구성표</span><span class="sxs-lookup"><span data-stu-id="eff02-119">`https://example.com/foo.html` - Different scheme</span></span>

* <span data-ttu-id="eff02-120">`http://example.com:9000/foo.html`-다른 포트</span><span class="sxs-lookup"><span data-stu-id="eff02-120">`http://example.com:9000/foo.html` - Different port</span></span>

> [!NOTE]
> <span data-ttu-id="eff02-121">Internet Explorer origin을 비교할 때 포트를 고려 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="eff02-121">Internet Explorer does not consider the port when comparing origins.</span></span>

## <a name="setting-up-cors"></a><span data-ttu-id="eff02-122">CORS 설정</span><span class="sxs-lookup"><span data-stu-id="eff02-122">Setting up CORS</span></span>

<span data-ttu-id="eff02-123">응용 프로그램에 대 한 CORS 추가를 설정 하는 `Microsoft.AspNetCore.Cors` 프로젝트에 패키지 합니다.</span><span class="sxs-lookup"><span data-stu-id="eff02-123">To set up CORS for your application add the `Microsoft.AspNetCore.Cors` package to your project.</span></span>

<span data-ttu-id="eff02-124">Startup.cs에 CORS 서비스를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="eff02-124">Add the CORS services in Startup.cs:</span></span>

[!code-csharp[Main](cors/sample/CorsExample1/Startup.cs?name=snippet_addcors)]

## <a name="enabling-cors-with-middleware"></a><span data-ttu-id="eff02-125">CORS 미들웨어와 사용</span><span class="sxs-lookup"><span data-stu-id="eff02-125">Enabling CORS with middleware</span></span>

<span data-ttu-id="eff02-126">사용 하려면 전체 응용 프로그램에 대 한 CORS 사용 하 여 요청 파이프라인에 CORS 미들웨어를 추가 `UseCors` 확장 메서드.</span><span class="sxs-lookup"><span data-stu-id="eff02-126">To enable CORS for your entire application add the CORS middleware to your request pipeline using the `UseCors` extension method.</span></span> <span data-ttu-id="eff02-127">참고는 CORS 미들웨어 앞에 야 정의 된 끝점 (예: 교차 원본 요청을 지원 하 고 응용 프로그램에서.</span><span class="sxs-lookup"><span data-stu-id="eff02-127">Note that the CORS middleware must precede any defined endpoints in your app that you want to support cross-origin requests (ex.</span></span> <span data-ttu-id="eff02-128">호출 하기 전에 `UseMvc`).</span><span class="sxs-lookup"><span data-stu-id="eff02-128">before any call to `UseMvc`).</span></span>

<span data-ttu-id="eff02-129">사용 하 여 CORS 미들웨어를 추가 하는 경우 크로스-원본 정책을 지정할 수 있습니다는 `CorsPolicyBuilder` 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="eff02-129">You can specify a cross-origin policy when adding the CORS middleware using the `CorsPolicyBuilder` class.</span></span> <span data-ttu-id="eff02-130">이렇게 하는 데는 두 가지 방법이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="eff02-130">There are two ways to do this.</span></span> <span data-ttu-id="eff02-131">첫 번째 람다를 사용 하 여 UseCors 호출입니다.</span><span class="sxs-lookup"><span data-stu-id="eff02-131">The first is to call UseCors with a lambda:</span></span>

[!code-csharp[Main](cors/sample/CorsExample1/Startup.cs?highlight=11,12&range=22-38)]

<span data-ttu-id="eff02-132">**참고:** 후행 슬래시가 없는 URL을 지정 해야 합니다 (`/`).</span><span class="sxs-lookup"><span data-stu-id="eff02-132">**Note:** The URL must be specified without a trailing slash (`/`).</span></span> <span data-ttu-id="eff02-133">URL로 종료 하는 경우 `/`가 반환 됩니다 `false` 헤더가 없으면 반환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="eff02-133">If the URL terminates with `/`, the comparison will return `false` and no header will be returned.</span></span>

<span data-ttu-id="eff02-134">람다는는 `CorsPolicyBuilder` 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="eff02-134">The lambda takes a `CorsPolicyBuilder` object.</span></span> <span data-ttu-id="eff02-135">목록을 찾을 수 있습니다는 [구성 옵션](#cors-policy-options) 이 항목의 뒷부분에 나오는 합니다.</span><span class="sxs-lookup"><span data-stu-id="eff02-135">You'll find a list of the [configuration options](#cors-policy-options) later in this topic.</span></span> <span data-ttu-id="eff02-136">이 예제에서는 정책을 통해 크로스-원본 요청을 `http://example.com` 및 다른 원본이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="eff02-136">In this example, the policy allows cross-origin requests from `http://example.com` and no other origins.</span></span>

<span data-ttu-id="eff02-137">메서드 호출을 체인화할 수 있도록 CorsPolicyBuilder fluent API에 있는지 참고:</span><span class="sxs-lookup"><span data-stu-id="eff02-137">Note that CorsPolicyBuilder has a fluent API, so you can chain method calls:</span></span>

[!code-csharp[Main](../security/cors/sample/CorsExample3/Startup.cs?highlight=3&range=29-32)]

<span data-ttu-id="eff02-138">하나 이상의 명명 된 CORS 정책을 정의 하 고 런타임 시 이름별 정책을 선택 하는 두 번째 방법이입니다.</span><span class="sxs-lookup"><span data-stu-id="eff02-138">The second approach is to define one or more named CORS policies, and then select the policy by name at run time.</span></span>

[!code-csharp[Main](cors/sample/CorsExample2/Startup.cs?name=snippet_begin)]

<span data-ttu-id="eff02-139">이 예제에서는 "AllowSpecificOrigin" 라는 CORS 정책을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="eff02-139">This example adds a CORS policy named "AllowSpecificOrigin".</span></span> <span data-ttu-id="eff02-140">정책을 선택 하려면에 이름을 전달 `UseCors`합니다.</span><span class="sxs-lookup"><span data-stu-id="eff02-140">To select the policy, pass the name to `UseCors`.</span></span>

## <a name="enabling-cors-in-mvc"></a><span data-ttu-id="eff02-141">MVC에 CORS 사용</span><span class="sxs-lookup"><span data-stu-id="eff02-141">Enabling CORS in MVC</span></span>

<span data-ttu-id="eff02-142">또는 MVC를 사용 하 여 특정 CORS 작업당: 컨트롤러 당 또는 모든 컨트롤러에 대해 전역으로 적용 합니다.</span><span class="sxs-lookup"><span data-stu-id="eff02-142">You can alternatively use MVC to apply specific CORS per action, per controller, or globally for all controllers.</span></span> <span data-ttu-id="eff02-143">CORS를 사용 하도록 MVC를 사용 하는 경우 동일한 CORS 서비스는 사용 되지 않지만 CORS 미들웨어 않습니다.</span><span class="sxs-lookup"><span data-stu-id="eff02-143">When using MVC to enable CORS the same CORS services are used, but the CORS middleware is not.</span></span>

### <a name="per-action"></a><span data-ttu-id="eff02-144">작업 마다</span><span class="sxs-lookup"><span data-stu-id="eff02-144">Per action</span></span>

<span data-ttu-id="eff02-145">특정 작업에 대 한 CORS 정책을 지정 하려면 추가 `[EnableCors]` 특성 작업을 합니다.</span><span class="sxs-lookup"><span data-stu-id="eff02-145">To specify a CORS policy for a specific action add the `[EnableCors]` attribute to the action.</span></span> <span data-ttu-id="eff02-146">정책 이름을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="eff02-146">Specify the policy name.</span></span>

[!code-csharp[Main](cors/sample/CorsMVC/Controllers/ValuesController.cs?name=EnableOnAction)]

### <a name="per-controller"></a><span data-ttu-id="eff02-147">컨트롤러당</span><span class="sxs-lookup"><span data-stu-id="eff02-147">Per controller</span></span>

<span data-ttu-id="eff02-148">특정 컨트롤러에 대 한 CORS 정책을 지정 하려면 추가 `[EnableCors]` 특성을 컨트롤러 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="eff02-148">To specify the CORS policy for a specific controller add the `[EnableCors]` attribute to the controller class.</span></span> <span data-ttu-id="eff02-149">정책 이름을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="eff02-149">Specify the policy name.</span></span>

[!code-csharp[Main](cors/sample/CorsMVC/Controllers/ValuesController.cs?name=EnableOnController)]

### <a name="globally"></a><span data-ttu-id="eff02-150">전역으로</span><span class="sxs-lookup"><span data-stu-id="eff02-150">Globally</span></span>

<span data-ttu-id="eff02-151">추가 하 여 위에 모든 컨트롤러에 대해 CORS를 전체적으로 사용할 수 있습니다는 `CorsAuthorizationFilterFactory` 필터를 전역 필터 컬렉션:</span><span class="sxs-lookup"><span data-stu-id="eff02-151">You can enable CORS globally for all controllers by adding the `CorsAuthorizationFilterFactory` filter to the global filter collection:</span></span>

[!code-csharp[Main](cors/sample/CorsMVC/Startup2.cs?name=snippet_configureservices)]

<span data-ttu-id="eff02-152">우선 순위는: 작업, 컨트롤러, 전역 합니다.</span><span class="sxs-lookup"><span data-stu-id="eff02-152">The precedence order is: Action, controller, global.</span></span> <span data-ttu-id="eff02-153">작업 수준 정책 컨트롤러 수준 정책 보다 우선 적용 하 고 컨트롤러 수준 정책 글로벌 정책 보다 우선 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="eff02-153">Action-level policies take precedence over controller-level policies, and controller-level policies take precedence over global policies.</span></span>

### <a name="disable-cors"></a><span data-ttu-id="eff02-154">CORS를 사용 하지 않도록 설정</span><span class="sxs-lookup"><span data-stu-id="eff02-154">Disable CORS</span></span>

<span data-ttu-id="eff02-155">컨트롤러 또는 동작에 대 한 CORS를 해제 하려면 사용 하 여는 `[DisableCors]` 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="eff02-155">To disable CORS for a controller or action, use the `[DisableCors]` attribute.</span></span>

[!code-csharp[Main](cors/sample/CorsMVC/Controllers/ValuesController.cs?name=DisableOnAction)]

## <a name="cors-policy-options"></a><span data-ttu-id="eff02-156">CORS 정책 옵션</span><span class="sxs-lookup"><span data-stu-id="eff02-156">CORS policy options</span></span>

<span data-ttu-id="eff02-157">이 섹션에서는 CORS 정책에서 설정할 수 있는 다양 한 옵션을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="eff02-157">This section describes the various options that you can set in a CORS policy.</span></span>

* [<span data-ttu-id="eff02-158">허용 되는 원본을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="eff02-158">Set the allowed origins</span></span>](#set-the-allowed-origins)

* [<span data-ttu-id="eff02-159">허용 된 HTTP 메서드 설정</span><span class="sxs-lookup"><span data-stu-id="eff02-159">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)

* [<span data-ttu-id="eff02-160">허용 된 요청 헤더 설정</span><span class="sxs-lookup"><span data-stu-id="eff02-160">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)

* [<span data-ttu-id="eff02-161">노출 된 응답 헤더 설정</span><span class="sxs-lookup"><span data-stu-id="eff02-161">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)

* [<span data-ttu-id="eff02-162">크로스-원본 요청에 자격 증명</span><span class="sxs-lookup"><span data-stu-id="eff02-162">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)

* [<span data-ttu-id="eff02-163">실행 전 만료 시간 설정</span><span class="sxs-lookup"><span data-stu-id="eff02-163">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="eff02-164">일부 옵션에 대 한 읽기 유용할 수 있습니다 [작동 하는 방법을 CORS](#how-cors-works) 첫 번째입니다.</span><span class="sxs-lookup"><span data-stu-id="eff02-164">For some options it may be helpful to read [How CORS works](#how-cors-works) first.</span></span>

### <a name="set-the-allowed-origins"></a><span data-ttu-id="eff02-165">허용 되는 원본을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="eff02-165">Set the allowed origins</span></span>

<span data-ttu-id="eff02-166">하나 이상의 특정 origin을 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="eff02-166">To allow one or more specific origins:</span></span>

[!code-csharp[Main](cors/sample/CorsExample4/Startup.cs?range=19-23)]

<span data-ttu-id="eff02-167">모든 원본을 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="eff02-167">To allow all origins:</span></span>

[!code-csharp[Main](cors/sample/CorsExample4/Startup.cs??range=27-31)]

<span data-ttu-id="eff02-168">모든 원본에서 요청을 허용 하기 전에 신중히 고려해 야 합니다.</span><span class="sxs-lookup"><span data-stu-id="eff02-168">Consider carefully before allowing requests from any origin.</span></span> <span data-ttu-id="eff02-169">모든 웹 사이트 API에 대 한 AJAX 호출을 만들 수 있음을 의미 합니다.</span><span class="sxs-lookup"><span data-stu-id="eff02-169">It means that literally any website can make AJAX calls to your API.</span></span>

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="eff02-170">허용 된 HTTP 메서드 설정</span><span class="sxs-lookup"><span data-stu-id="eff02-170">Set the allowed HTTP methods</span></span>

<span data-ttu-id="eff02-171">모든 HTTP 메서드를 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="eff02-171">To allow all HTTP methods:</span></span>

[!code-csharp[Main](cors/sample/CorsExample4/Startup.cs?range=44-49)]

<span data-ttu-id="eff02-172">전 요청 및 액세스 제어-허용-방법 헤더에 영향을 줍니다.</span><span class="sxs-lookup"><span data-stu-id="eff02-172">This affects pre-flight requests and Access-Control-Allow-Methods header.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="eff02-173">허용 된 요청 헤더 설정</span><span class="sxs-lookup"><span data-stu-id="eff02-173">Set the allowed request headers</span></span>

<span data-ttu-id="eff02-174">CORS 실행 전 요청에는 응용 프로그램에 의해 설정 된 HTTP 헤더를 나열 하는 액세스 제어-요청 헤더 헤더가 포함 될 수 있습니다 (이라는 "요청 헤더 author").</span><span class="sxs-lookup"><span data-stu-id="eff02-174">A CORS preflight request might include an Access-Control-Request-Headers header, listing the HTTP headers set by the application (the so-called "author request headers").</span></span>

<span data-ttu-id="eff02-175">특정 헤더를 허용 목록:</span><span class="sxs-lookup"><span data-stu-id="eff02-175">To whitelist specific headers:</span></span>

[!code-csharp[Main](cors/sample/CorsExample4/Startup.cs?range=53-58)]

<span data-ttu-id="eff02-176">허용 하려면 요청 헤더를 작성 모든:</span><span class="sxs-lookup"><span data-stu-id="eff02-176">To allow all author request headers:</span></span>

[!code-csharp[Main](cors/sample/CorsExample4/Startup.cs?range=62-67)]

<span data-ttu-id="eff02-177">브라우저에서 액세스 제어-요청 헤더를 설정 하는 방법을 완전히 일치 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="eff02-177">Browsers are not entirely consistent in how they set Access-Control-Request-Headers.</span></span> <span data-ttu-id="eff02-178">아닌 다른 헤더를 값으로 설정 하면 "*"를 포함 해야 이상 "동의", "콘텐츠 형식은" 및 "출처" + 지원 하 고 모든 사용자 지정 헤더입니다.</span><span class="sxs-lookup"><span data-stu-id="eff02-178">If you set headers to anything other than "*", you should include at least "accept", "content-type", and "origin", plus any custom headers that you want to support.</span></span>

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="eff02-179">노출 된 응답 헤더 설정</span><span class="sxs-lookup"><span data-stu-id="eff02-179">Set the exposed response headers</span></span>

<span data-ttu-id="eff02-180">기본적으로 브라우저 노출 하지 않습니다 모든 응용 프로그램에 대 한 응답 헤더입니다.</span><span class="sxs-lookup"><span data-stu-id="eff02-180">By default, the browser does not expose all of the response headers to the application.</span></span> <span data-ttu-id="eff02-181">(참조 [http://www.w3.org/TR/cors/#simple-response-header](http://www.w3.org/TR/cors/#simple-response-header).) 기본적으로 사용할 수 있는 응답 헤더에는</span><span class="sxs-lookup"><span data-stu-id="eff02-181">(See [http://www.w3.org/TR/cors/#simple-response-header](http://www.w3.org/TR/cors/#simple-response-header).) The response headers that are available by default are:</span></span>

* <span data-ttu-id="eff02-182">캐시 제어</span><span class="sxs-lookup"><span data-stu-id="eff02-182">Cache-Control</span></span>

* <span data-ttu-id="eff02-183">Content-language</span><span class="sxs-lookup"><span data-stu-id="eff02-183">Content-Language</span></span>

* <span data-ttu-id="eff02-184">콘텐츠-유형</span><span class="sxs-lookup"><span data-stu-id="eff02-184">Content-Type</span></span>

* <span data-ttu-id="eff02-185">만료</span><span class="sxs-lookup"><span data-stu-id="eff02-185">Expires</span></span>

* <span data-ttu-id="eff02-186">마지막 수정</span><span class="sxs-lookup"><span data-stu-id="eff02-186">Last-Modified</span></span>

* <span data-ttu-id="eff02-187">Pragma</span><span class="sxs-lookup"><span data-stu-id="eff02-187">Pragma</span></span>

<span data-ttu-id="eff02-188">이러한 호출 하는 CORS 사양 *간단한 응답 헤더*합니다.</span><span class="sxs-lookup"><span data-stu-id="eff02-188">The CORS spec calls these *simple response headers*.</span></span> <span data-ttu-id="eff02-189">다른 헤더를 사용할 수 있도록 응용 프로그램:</span><span class="sxs-lookup"><span data-stu-id="eff02-189">To make other headers available to the application:</span></span>

[!code-csharp[Main](cors/sample/CorsExample4/Startup.cs?range=71-76)]

### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="eff02-190">크로스-원본 요청에 자격 증명</span><span class="sxs-lookup"><span data-stu-id="eff02-190">Credentials in cross-origin requests</span></span>

<span data-ttu-id="eff02-191">자격 증명에는 CORS 요청에 대 한 특별 한 처리가 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="eff02-191">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="eff02-192">기본적으로 브라우저 크로스-원본 요청에 자격 증명을 보내지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="eff02-192">By default, the browser does not send any credentials with a cross-origin request.</span></span> <span data-ttu-id="eff02-193">자격 증명 쿠키 뿐만 아니라 HTTP 인증 스키마를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="eff02-193">Credentials include cookies as well as HTTP authentication schemes.</span></span> <span data-ttu-id="eff02-194">클라이언트는 크로스-원본 요청에 자격 증명을 보내려면 true로 XMLHttpRequest.withCredentials 설정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="eff02-194">To send credentials with a cross-origin request, the client must set XMLHttpRequest.withCredentials to true.</span></span>

<span data-ttu-id="eff02-195">직접 XMLHttpRequest를 사용 하 여:</span><span class="sxs-lookup"><span data-stu-id="eff02-195">Using XMLHttpRequest directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'http://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="eff02-196">jQuery:</span><span class="sxs-lookup"><span data-stu-id="eff02-196">In jQuery:</span></span>

```jQuery
$.ajax({
  type: 'get',
  url: 'http://www.example.com/home',
  xhrFields: {
    withCredentials: true
}
```

<span data-ttu-id="eff02-197">또한 서버 자격 증명을 허용 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="eff02-197">In addition, the server must allow the credentials.</span></span> <span data-ttu-id="eff02-198">크로스-원본 자격 증명을 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="eff02-198">To allow cross-origin credentials:</span></span>

[!code-csharp[Main](cors/sample/CorsExample4/Startup.cs?range=80-85)]

<span data-ttu-id="eff02-199">이제 HTTP 응답에는 브라우저에 알려 서버 크로스-원본 요청에 대 한 자격 증명을 허용 하는 액세스-컨트롤--자격 증명 허용 헤더가 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="eff02-199">Now the HTTP response will include an Access-Control-Allow-Credentials header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="eff02-200">브라우저에서 자격 증명을 보내는 응답에는 올바른 액세스-컨트롤--자격 증명 허용 헤더 표시 되지만 브라우저 응용 프로그램에 대 한 응답을 노출 되지는 않습니다 및 AJAX 요청이 실패 합니다.</span><span class="sxs-lookup"><span data-stu-id="eff02-200">If the browser sends credentials, but the response does not include a valid Access-Control-Allow-Credentials header, the browser will not expose the response to the application, and the AJAX request fails.</span></span>

<span data-ttu-id="eff02-201">크로스-원본 자격 증명을 허용 하는 방법에 대 한 때문에 신중을 기해야 다른 도메인에서 웹 사이트 사용자가 인식 하지 않고 사용자를 대신 하 여, 앱에 로그인 한 사용자의 자격 증명 보낼 수 있음을 의미 합니다.</span><span class="sxs-lookup"><span data-stu-id="eff02-201">Be very careful about allowing cross-origin credentials, because it means a website at another domain can send a logged-in user’s credentials to your app on the user’s behalf, without the user being aware.</span></span> <span data-ttu-id="eff02-202">CORS 사양도 상태 해당 설정을 원본이 "*" (모든 원본을) 액세스-컨트롤--자격 증명 허용 헤더가 있으면 올바르지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="eff02-202">The CORS spec also states that setting origins to "*" (all origins) is invalid if the Access-Control-Allow-Credentials header is present.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="eff02-203">실행 전 만료 시간 설정</span><span class="sxs-lookup"><span data-stu-id="eff02-203">Set the preflight expiration time</span></span>

<span data-ttu-id="eff02-204">액세스 제어-최대 기간 헤더 실행 전 요청에 대 한 응답을 캐시할 수 시간을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="eff02-204">The Access-Control-Max-Age header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="eff02-205">다음을 설정 하려면이 헤더</span><span class="sxs-lookup"><span data-stu-id="eff02-205">To set this header:</span></span>

[!code-csharp[Main](cors/sample/CorsExample4/Startup.cs?range=89-94)]

<a name="cors-how-cors-works"></a>

## <a name="how-cors-works"></a><span data-ttu-id="eff02-206">CORS가 작동 하는 방법</span><span class="sxs-lookup"><span data-stu-id="eff02-206">How CORS works</span></span>

<span data-ttu-id="eff02-207">이 섹션에서는 CORS 요청은 HTTP 메시지 수준에서 수행 되는 작업을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="eff02-207">This section describes what happens in a CORS request, at the level of the HTTP messages.</span></span> <span data-ttu-id="eff02-208">작동 방식을 이해 CORS CORS 정책을 올바르게 구성 하 고 예상 대로 작동 하지 않는 경우 문제를 해결할 수 있도록 하는 것이 유용 합니다.</span><span class="sxs-lookup"><span data-stu-id="eff02-208">It’s important to understand how CORS works, so that you can configure your CORS policy correctly, and troubleshoot if things don’t work as you expect.</span></span>

<span data-ttu-id="eff02-209">CORS 사양 교차 원본 요청을 사용 하도록 설정 하는 몇 가지 새 HTTP 헤더를 소개 합니다.</span><span class="sxs-lookup"><span data-stu-id="eff02-209">The CORS specification introduces several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="eff02-210">크로스-원본 요청;에 대 한 자동으로 이러한 헤더를 설정 브라우저 CORS를 지 원하는 경우 JavaScript 코드에 특수 한 어떤 작업도 수행할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="eff02-210">If a browser supports CORS, it sets these headers automatically for cross-origin requests; you don’t need to do anything special in your JavaScript code.</span></span>

<span data-ttu-id="eff02-211">크로스-원본 요청의 예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="eff02-211">Here is an example of a cross-origin request.</span></span> <span data-ttu-id="eff02-212">"원본" 헤더는 요청을 수행 하는 사이트의 도메인을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="eff02-212">The "Origin" header gives the domain of the site that is making the request:</span></span>

```
GET http://myservice.azurewebsites.net/api/test HTTP/1.1
Referer: http://myclient.azurewebsites.net/
Accept: */*
Accept-Language: en-US
Origin: http://myclient.azurewebsites.net
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.2; WOW64; Trident/6.0)
Host: myservice.azurewebsites.net
```

<span data-ttu-id="eff02-213">서버 요청을 허용 하는 경우 액세스 제어-허용-원본 헤더를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="eff02-213">If the server allows the request, it sets the Access-Control-Allow-Origin header.</span></span> <span data-ttu-id="eff02-214">이 헤더의 값 원본 헤더 또는 와일드 카드 값 "*", 모든 원본을 허용 되는 의미 합니다.:</span><span class="sxs-lookup"><span data-stu-id="eff02-214">The value of this header either matches the Origin header, or is the wildcard value "*", meaning that any origin is allowed.:</span></span>

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Type: text/plain; charset=utf-8
Access-Control-Allow-Origin: http://myclient.azurewebsites.net
Date: Wed, 20 May 2015 06:27:30 GMT
Content-Length: 12

Test message
```

<span data-ttu-id="eff02-215">응답에 대 한 액세스 제어-허용-원본 헤더 포함 되어 있지 않으면, AJAX 요청이 실패 합니다.</span><span class="sxs-lookup"><span data-stu-id="eff02-215">If the response does not include the Access-Control-Allow-Origin header, the AJAX request fails.</span></span> <span data-ttu-id="eff02-216">특히, 브라우저 요청을 허용 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="eff02-216">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="eff02-217">성공적인 응답을 반환 하는 서버, 경우에 브라우저 수 없습니다 응답 클라이언트 응용 프로그램에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="eff02-217">Even if the server returns a successful response, the browser does not make the response available to the client application.</span></span>

### <a name="preflight-requests"></a><span data-ttu-id="eff02-218">실행 전 요청</span><span class="sxs-lookup"><span data-stu-id="eff02-218">Preflight Requests</span></span>

<span data-ttu-id="eff02-219">일부 CORS 요청에 대 한 브라우저의 리소스에 대 한 실제 요청을 보내기 전에 "실행 전 요청" 라는 추가 요청을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="eff02-219">For some CORS requests, the browser sends an additional request, called a "preflight request", before it sends the actual request for the resource.</span></span> <span data-ttu-id="eff02-220">다음 조건에 해당할 경우 브라우저에서 실행 전 요청을 건너뛸 수 있습니다.:</span><span class="sxs-lookup"><span data-stu-id="eff02-220">The browser can skip the preflight request if the following conditions are true:</span></span>

* <span data-ttu-id="eff02-221">요청 메서드는 GET, HEAD 또는 POST 및</span><span class="sxs-lookup"><span data-stu-id="eff02-221">The request method is GET, HEAD, or POST, and</span></span>

* <span data-ttu-id="eff02-222">응용 프로그램 Accept, Accept-language, Content-language 이외의 모든 요청 헤더를 설정 하지 않는 콘텐츠 형식 또는 마지막-이벤트-ID 및</span><span class="sxs-lookup"><span data-stu-id="eff02-222">The application does not set any request headers other than Accept, Accept-Language, Content-Language, Content-Type, or Last-Event-ID, and</span></span>

* <span data-ttu-id="eff02-223">콘텐츠 형식 헤더 (하는 경우 설정) 다음 중 하나입니다.</span><span class="sxs-lookup"><span data-stu-id="eff02-223">The Content-Type header (if set) is one of the following:</span></span>

  * <span data-ttu-id="eff02-224">application/x-www-form-urlencoded</span><span class="sxs-lookup"><span data-stu-id="eff02-224">application/x-www-form-urlencoded</span></span>

  * <span data-ttu-id="eff02-225">multipart/폼 데이터</span><span class="sxs-lookup"><span data-stu-id="eff02-225">multipart/form-data</span></span>

  * <span data-ttu-id="eff02-226">텍스트/일반</span><span class="sxs-lookup"><span data-stu-id="eff02-226">text/plain</span></span>

<span data-ttu-id="eff02-227">요청 헤더에 대 한 규칙 setRequestHeader XMLHttpRequest 개체에서 호출 하 여 응용 프로그램을 설정 하는 헤더에 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="eff02-227">The rule about request headers applies to headers that the application sets by calling setRequestHeader on the XMLHttpRequest object.</span></span> <span data-ttu-id="eff02-228">(이러한 작성자 요청 "헤더" CORS 사양을 호출합니다.) 규칙은 브라우저 등을 설정할 수, 사용자 에이전트, 호스트 또는 Content-length 헤더에 적용 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="eff02-228">(The CORS specification calls these "author request headers".) The rule does not apply to headers the browser can set, such as User-Agent, Host, or Content-Length.</span></span>

<span data-ttu-id="eff02-229">실행 전 요청의 예는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="eff02-229">Here is an example of a preflight request:</span></span>

```
OPTIONS http://myservice.azurewebsites.net/api/test HTTP/1.1
Accept: */*
Origin: http://myclient.azurewebsites.net
Access-Control-Request-Method: PUT
Access-Control-Request-Headers: accept, x-my-custom-header
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.2; WOW64; Trident/6.0)
Host: myservice.azurewebsites.net
Content-Length: 0
```

<span data-ttu-id="eff02-230">전 요청의 HTTP OPTIONS 메서드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="eff02-230">The pre-flight request uses the HTTP OPTIONS method.</span></span> <span data-ttu-id="eff02-231">두 개의 특수 헤더를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="eff02-231">It includes two special headers:</span></span>

* <span data-ttu-id="eff02-232">액세스-컨트롤-요청-방법: 실제 요청에 사용할 HTTP 메서드.</span><span class="sxs-lookup"><span data-stu-id="eff02-232">Access-Control-Request-Method: The HTTP method that will be used for the actual request.</span></span>

* <span data-ttu-id="eff02-233">액세스 제어-요청-헤더만: 응용 프로그램은 실제 요청에 설정 하는 요청 헤더의 목록.</span><span class="sxs-lookup"><span data-stu-id="eff02-233">Access-Control-Request-Headers: A list of request headers that the application set on the actual request.</span></span> <span data-ttu-id="eff02-234">(다시이 헤더가 포함 되지 않습니다 브라우저 설정입니다.)</span><span class="sxs-lookup"><span data-stu-id="eff02-234">(Again, this does not include headers that the browser sets.)</span></span>

<span data-ttu-id="eff02-235">다음은 서버에서 요청을 허용 하는 것으로 가정 하는 예제 응답이입니다.</span><span class="sxs-lookup"><span data-stu-id="eff02-235">Here is an example response, assuming that the server allows the request:</span></span>

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 0
Access-Control-Allow-Origin: http://myclient.azurewebsites.net
Access-Control-Allow-Headers: x-my-custom-header
Access-Control-Allow-Methods: PUT
Date: Wed, 20 May 2015 06:33:22 GMT
```

<span data-ttu-id="eff02-236">허용 되는 메서드를 나열 하는 액세스 제어-허용-방법 헤더 및 필요에 따라 허용된 된 헤더를 나열 하는 액세스 제어-허용-헤더만 헤더가 응답에 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="eff02-236">The response includes an Access-Control-Allow-Methods header that lists the allowed methods, and optionally an Access-Control-Allow-Headers header, which lists the allowed headers.</span></span> <span data-ttu-id="eff02-237">실행 전 요청이 성공 하면 앞에서 설명한 대로 브라우저 실제 요청을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="eff02-237">If the preflight request succeeds, the browser sends the actual request, as described earlier.</span></span>
