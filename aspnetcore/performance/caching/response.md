---
title: "ASP.NET Core의 응답 캐싱"
author: rick-anderson
description: "응답을 대역폭을 줄이고 성능을 향상 하려면 캐시를 사용 하는 방법에 알아봅니다."
keywords: "ASP.NET Core, 캐싱, HTTP 헤더 응답"
ms.author: riande
manager: wpickett
ms.date: 09/20/2017
ms.topic: article
ms.assetid: cb42035a-60b0-472e-a614-cb79f443f654
ms.prod: asp.net-core
uid: performance/caching/response
ms.openlocfilehash: 957bdf5fe24216fa3459ac7ecee0464a45226828
ms.sourcegitcommit: 6e83c55eb0450a3073ef2b95fa5f5bcb20dbbf89
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/28/2017
---
# <a name="response-caching-in-aspnet-core"></a><span data-ttu-id="db492-104">ASP.NET Core의 응답 캐싱</span><span class="sxs-lookup"><span data-stu-id="db492-104">Response caching in ASP.NET Core</span></span>

<span data-ttu-id="db492-105">여 [John Luo](https://github.com/JunTaoLuo), [Rick Anderson](https://twitter.com/RickAndMSFT), [Steve Smith](https://ardalis.com/), 및 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="db492-105">By [John Luo](https://github.com/JunTaoLuo), [Rick Anderson](https://twitter.com/RickAndMSFT), [Steve Smith](https://ardalis.com/), and [Luke Latham](https://github.com/guardrex)</span></span>

[<span data-ttu-id="db492-106">샘플 코드 보기 또는 다운로드</span><span class="sxs-lookup"><span data-stu-id="db492-106">View or download sample code</span></span>](https://github.com/aspnet/Docs/tree/master/aspnetcore/performance/caching/response/sample)

<span data-ttu-id="db492-107">응답 캐시 클라이언트 또는 프록시 웹 서버에 수행 된 요청 수가 줄어듭니다.</span><span class="sxs-lookup"><span data-stu-id="db492-107">Response caching reduces the number of requests a client or proxy makes to a web server.</span></span> <span data-ttu-id="db492-108">응답 캐시도 줄일 수 작업의 응답을 생성 하는 웹 서버 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="db492-108">Response caching also reduces the amount of work the web server performs to generate a response.</span></span> <span data-ttu-id="db492-109">응답 캐시 클라이언트, 프록시 및 응답을 캐시 미들웨어 용도 지정 하는 헤더에 의해 제어 됩니다.</span><span class="sxs-lookup"><span data-stu-id="db492-109">Response caching is controlled by headers that specify how you want client, proxy, and middleware to cache responses.</span></span>

<span data-ttu-id="db492-110">추가 하면 웹 서버 응답을 캐시할 수 [응답 캐싱 미들웨어](xref:performance/caching/middleware)합니다.</span><span class="sxs-lookup"><span data-stu-id="db492-110">The web server can cache responses when you add [Response Caching Middleware](xref:performance/caching/middleware).</span></span>

## <a name="http-based-response-caching"></a><span data-ttu-id="db492-111">응답 HTTP 기반 캐싱</span><span class="sxs-lookup"><span data-stu-id="db492-111">HTTP-based response caching</span></span>

<span data-ttu-id="db492-112">[HTTP 1.1 캐싱 사양](https://tools.ietf.org/html/rfc7234) 인터넷 캐시 동작 방식에 대해 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="db492-112">The [HTTP 1.1 Caching specification](https://tools.ietf.org/html/rfc7234) describes how Internet caches should behave.</span></span> <span data-ttu-id="db492-113">캐시에 사용 되는 기본 HTTP 헤더는 [캐시 제어](https://tools.ietf.org/html/rfc7234#section-5.2), 캐시를 지정 하는 데 사용 되는 *지시문*합니다.</span><span class="sxs-lookup"><span data-stu-id="db492-113">The primary HTTP header used for caching is [Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2), which is used to specify cache *directives*.</span></span> <span data-ttu-id="db492-114">지시문은 요청 하 게 클라이언트에서 서버로 하면서 되 고 응답 하 게 서버에서 클라이언트로 다시의 캐싱 동작을 제어 합니다.</span><span class="sxs-lookup"><span data-stu-id="db492-114">The directives control caching behavior as requests make their way from clients to servers and as reponses make their way from servers back to clients.</span></span> <span data-ttu-id="db492-115">프록시 서버를 통해 이동 하는 요청 및 응답 하 고 프록시 서버 또한 HTTP 1.1 캐싱 사양을 준수 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="db492-115">Requests and responses move through proxy servers, and proxy servers must also conform to the HTTP 1.1 Caching specification.</span></span>

<span data-ttu-id="db492-116">일반적인 `Cache-Control` 지시문 다음 표에 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="db492-116">Common `Cache-Control` directives are shown in the following table.</span></span>

| <span data-ttu-id="db492-117">지시문</span><span class="sxs-lookup"><span data-stu-id="db492-117">Directive</span></span>                                                       | <span data-ttu-id="db492-118">작업</span><span class="sxs-lookup"><span data-stu-id="db492-118">Action</span></span> |
| --------------------------------------------------------------- | ------ |
| [<span data-ttu-id="db492-119">public</span><span class="sxs-lookup"><span data-stu-id="db492-119">public</span></span>](https://tools.ietf.org/html/rfc7234#section-5.2.2.5)   | <span data-ttu-id="db492-120">캐시 된 응답을 저장할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="db492-120">A cache may store the response.</span></span> |
| [<span data-ttu-id="db492-121">private</span><span class="sxs-lookup"><span data-stu-id="db492-121">private</span></span>](https://tools.ietf.org/html/rfc7234#section-5.2.2.6)  | <span data-ttu-id="db492-122">공유 캐시에서 응답을 저장 되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="db492-122">The response must not be stored by a shared cache.</span></span> <span data-ttu-id="db492-123">개인 캐시는 저장 하 고 응답을 다시 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="db492-123">A private cache may store and reuse the response.</span></span> |
| [<span data-ttu-id="db492-124">최대 처리 기간</span><span class="sxs-lookup"><span data-stu-id="db492-124">max-age</span></span>](https://tools.ietf.org/html/rfc7234#section-5.2.1.1)  | <span data-ttu-id="db492-125">클라이언트는 오래 된 시간 (초) 지정 된 숫자 보다 큰 경우 응답을 수락 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="db492-125">The client won't accept a response whose age is greater than the specified number of seconds.</span></span> <span data-ttu-id="db492-126">예: `max-age=60` (60 초) `max-age=2592000` (1 월)</span><span class="sxs-lookup"><span data-stu-id="db492-126">Examples: `max-age=60` (60 seconds), `max-age=2592000` (1 month)</span></span> |
| [<span data-ttu-id="db492-127">캐시 없음</span><span class="sxs-lookup"><span data-stu-id="db492-127">no-cache</span></span>](https://tools.ietf.org/html/rfc7234#section-5.2.1.4) | <span data-ttu-id="db492-128">**요청에서**: 요청을 충족 하도록 저장된 응답 캐시를 사용 하지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="db492-128">**On requests**: A cache must not use a stored response to satisfy the request.</span></span> <span data-ttu-id="db492-129">참고:는 원본 서버는 클라이언트에 대 한 응답을 다시 생성 하 고 미들웨어 캐시에 저장 된 응답을 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="db492-129">Note: The origin server re-generates the response for the client, and the middleware updates the stored response in its cache.</span></span><br><br><span data-ttu-id="db492-130">**응답에 대해**: 원본 서버에서 유효성을 검사 하지 않고 후속 요청에 대 한 응답을 사용할 수 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="db492-130">**On responses**: The response must not be used for a subsequent request without validation on the origin server.</span></span> |
| [<span data-ttu-id="db492-131">저장소 아니요</span><span class="sxs-lookup"><span data-stu-id="db492-131">no-store</span></span>](https://tools.ietf.org/html/rfc7234#section-5.2.1.5) | <span data-ttu-id="db492-132">**요청에서**: 캐시 요청을 저장 하지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="db492-132">**On requests**: A cache must not store the request.</span></span><br><br><span data-ttu-id="db492-133">**응답에 대해**: 캐시 응답의 모든 부분을 저장 하지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="db492-133">**On responses**: A cache must not store any part of the response.</span></span> |

<span data-ttu-id="db492-134">캐시의 역할을 하는 다른 캐시 헤더는 다음 표에 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="db492-134">Other cache headers that play a role in caching are shown in the following table.</span></span>

| <span data-ttu-id="db492-135">Header</span><span class="sxs-lookup"><span data-stu-id="db492-135">Header</span></span>                                                     | <span data-ttu-id="db492-136">함수</span><span class="sxs-lookup"><span data-stu-id="db492-136">Function</span></span> |
| ---------------------------------------------------------- | -------- |
| [<span data-ttu-id="db492-137">보존 기간</span><span class="sxs-lookup"><span data-stu-id="db492-137">Age</span></span>](https://tools.ietf.org/html/rfc7234#section-5.1)     | <span data-ttu-id="db492-138">예상 응답 이후의 초 단위 시간의 양 생성 되었거나 원본 서버에서 성공적으로 유효성을 검사 합니다.</span><span class="sxs-lookup"><span data-stu-id="db492-138">An estimate of the amount of time in seconds since the response was generated or successfully validated at the origin server.</span></span> |
| [<span data-ttu-id="db492-139">만료</span><span class="sxs-lookup"><span data-stu-id="db492-139">Expires</span></span>](https://tools.ietf.org/html/rfc7234#section-5.3) | <span data-ttu-id="db492-140">날짜/시간 응답 초과가 부실 코드가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="db492-140">The date/time after which the response is considered stale.</span></span> |
| [<span data-ttu-id="db492-141">Pragma</span><span class="sxs-lookup"><span data-stu-id="db492-141">Pragma</span></span>](https://tools.ietf.org/html/rfc7234#section-5.4)  | <span data-ttu-id="db492-142">HTTP/1.0와의 호환성 설정에 대 한 캐시 하는 이전 버전과 대 한 존재 `no-cache` 동작 합니다.</span><span class="sxs-lookup"><span data-stu-id="db492-142">Exists for backwards compatibility with HTTP/1.0 caches for setting `no-cache` behavior.</span></span> <span data-ttu-id="db492-143">경우는 `Cache-Control` 헤더가 있으면는 `Pragma` 헤더는 무시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="db492-143">If the `Cache-Control` header is present, the `Pragma` header is ignored.</span></span> |
| [<span data-ttu-id="db492-144">변경</span><span class="sxs-lookup"><span data-stu-id="db492-144">Vary</span></span>](https://tools.ietf.org/html/rfc7231#section-7.1.4)  | <span data-ttu-id="db492-145">지정 된 캐시 된 응답 해야 보내지 않도록 하지 않는 한 모든의 `Vary` 헤더 필드에 캐시 된 응답의 원래 요청 하 고 새 요청이 모두 일치 합니다.</span><span class="sxs-lookup"><span data-stu-id="db492-145">Specifies that a cached response must not be sent unless all of the `Vary` header fields match in both the cached response's original request and the new request.</span></span> |

## <a name="http-based-caching-respects-request-cache-control-directives"></a><span data-ttu-id="db492-146">캐싱 측면 HTTP 기반 요청 캐시 제어 지시문</span><span class="sxs-lookup"><span data-stu-id="db492-146">HTTP-based caching respects request Cache-Control directives</span></span>

<span data-ttu-id="db492-147">[캐시 컨트롤 헤더에 대 한 HTTP 1.1 캐싱 사양이](https://tools.ietf.org/html/rfc7234#section-5.2) (를) 처리할 유효한 캐시 필요 `Cache-Control` 클라이언트에서 보낸 헤더입니다.</span><span class="sxs-lookup"><span data-stu-id="db492-147">The [HTTP 1.1 Caching specification for the Cache-Control header](https://tools.ietf.org/html/rfc7234#section-5.2) requires a cache to honor a valid `Cache-Control` header sent by the client.</span></span> <span data-ttu-id="db492-148">클라이언트가 사용 하 여 요청을 할 수는 `no-cache` 헤더 값과 강제로 서버에 대 한 모든 요청에 대 한 새 응답을 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="db492-148">A client can make requests with a `no-cache` header value and force the server to generate a new response for every request.</span></span>

<span data-ttu-id="db492-149">클라이언트를 구분 하지 않고 항상 `Cache-Control` HTTP 캐싱의 목표를 고려 하는 경우 요청 헤더는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="db492-149">Always honoring client `Cache-Control` request headers makes sense if you consider the goal of HTTP caching.</span></span> <span data-ttu-id="db492-150">공식 사양에서 캐싱 클라이언트, 프록시, 및 서버 네트워크를 통해 요청을 만족의 대기 시간 및 네트워크 오버 헤드를 줄이기 위해 위한 것입니다.</span><span class="sxs-lookup"><span data-stu-id="db492-150">Under the official specification, caching is meant to reduce the latency and network overhead of satisfying requests across a network of clients, proxies, and servers.</span></span> <span data-ttu-id="db492-151">원본 서버의 부하를 제어 하는 방법 반드시 합니다.</span><span class="sxs-lookup"><span data-stu-id="db492-151">It isn't necessarily a way to control the load on an origin server.</span></span>

<span data-ttu-id="db492-152">사용 하는 경우이 캐싱 동작을 통해 현재 개발자 컨트롤이 없는 [응답 캐싱 미들웨어](xref:performance/caching/middleware) 미들웨어 사양 캐싱 공식 준수 때문에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="db492-152">There's no current developer control over this caching behavior when using the [Response Caching Middleware](xref:performance/caching/middleware) because the middleware adheres to the official caching specification.</span></span> <span data-ttu-id="db492-153">[미들웨어 향상 될](https://github.com/aspnet/ResponseCaching/issues/96) 는 요청을 무시할 미들웨어 구성 허용 `Cache-Control` 된 캐시 된 응답을 제공 하도록 결정할 때 헤더입니다.</span><span class="sxs-lookup"><span data-stu-id="db492-153">[Future enhancements to the middleware](https://github.com/aspnet/ResponseCaching/issues/96) will permit configuring the middleware to ignore a request's `Cache-Control` header when deciding to serve a cached response.</span></span> <span data-ttu-id="db492-154">이 제공 하면 기회 서버에서 보다 효율적으로 제어할 부하를 미들웨어를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="db492-154">This will offer you an opportunity to better control the load on your server when you use the middleware.</span></span>

## <a name="other-caching-technology-in-aspnet-core"></a><span data-ttu-id="db492-155">ASP.NET Core의 기타 캐싱 기술</span><span class="sxs-lookup"><span data-stu-id="db492-155">Other caching technology in ASP.NET Core</span></span>

### <a name="in-memory-caching"></a><span data-ttu-id="db492-156">메모리 내 캐싱</span><span class="sxs-lookup"><span data-stu-id="db492-156">In-memory caching</span></span>

<span data-ttu-id="db492-157">메모리 내 캐싱 서버 메모리를 사용 하 여 캐시 된 데이터를 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="db492-157">In-memory caching uses server memory to store cached data.</span></span> <span data-ttu-id="db492-158">이러한 유형의 캐싱은 단일 서버 또는 여러 서버를 사용 하 여에 적합 한 *고정 세션*합니다.</span><span class="sxs-lookup"><span data-stu-id="db492-158">This type of caching is suitable for a single server or multiple servers using *sticky sessions*.</span></span> <span data-ttu-id="db492-159">고정 세션은 클라이언트에서 요청 처리를 위해 동일한 서버에 라우팅되 항상 의미 합니다.</span><span class="sxs-lookup"><span data-stu-id="db492-159">Sticky sessions means that the requests from a client are always routed to the same server for processing.</span></span>

<span data-ttu-id="db492-160">자세한 내용은 참조 [ASP.NET Core의 메모리 내 캐싱 소개](xref:performance/caching/memory)합니다.</span><span class="sxs-lookup"><span data-stu-id="db492-160">For more information, see [Introduction to in-memory caching in ASP.NET Core](xref:performance/caching/memory).</span></span>

### <a name="distributed-cache"></a><span data-ttu-id="db492-161">분산된 캐시</span><span class="sxs-lookup"><span data-stu-id="db492-161">Distributed Cache</span></span>

<span data-ttu-id="db492-162">분산된 캐시를 사용 하 여 응용 프로그램 클라우드 또는 서버 팜에서 호스트 될 때 메모리에 데이터를 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="db492-162">Use a distributed cache to store data in memory when the app is hosted in a cloud or server farm.</span></span> <span data-ttu-id="db492-163">캐시 요청을 처리 하는 서버 간에 공유 됩니다.</span><span class="sxs-lookup"><span data-stu-id="db492-163">The cache is shared across the servers that process requests.</span></span> <span data-ttu-id="db492-164">클라이언트는 그룹의 모든 서버에서 처리 하는 요청을 제출할 수 및 클라이언트에 대 한 캐시 된 데이터를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="db492-164">A client can submit a request that is handled by any server in the group and cached data for the client is available.</span></span> <span data-ttu-id="db492-165">ASP.NET Core에서는 SQL Server 및 분산 Redis 캐시를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="db492-165">ASP.NET Core offers SQL Server and Redis distributed caches.</span></span>

<span data-ttu-id="db492-166">자세한 내용은 참조 [분산된 캐시 작업](xref:performance/caching/distributed)합니다.</span><span class="sxs-lookup"><span data-stu-id="db492-166">For more information, see [Working with a distributed cache](xref:performance/caching/distributed).</span></span>

### <a name="cache-tag-helper"></a><span data-ttu-id="db492-167">캐시 태그 도우미</span><span class="sxs-lookup"><span data-stu-id="db492-167">Cache Tag Helper</span></span>

<span data-ttu-id="db492-168">캐시 태그 도우미와 MVC 뷰 또는 Razor 페이지 콘텐츠를 캐시할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="db492-168">You can cache the content from an MVC view or Razor Page with the Cache Tag Helper.</span></span> <span data-ttu-id="db492-169">캐시 태그 도우미에서는 데이터를 저장할 메모리 내 캐시를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="db492-169">The Cache Tag Helper uses in-memory caching to store data.</span></span>

<span data-ttu-id="db492-170">자세한 내용은 참조 [캐시 태그 도우미 ASP.NET Core mvc에서](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper)합니다.</span><span class="sxs-lookup"><span data-stu-id="db492-170">For more information, see [Cache Tag Helper in ASP.NET Core MVC](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper).</span></span>

### <a name="distributed-cache-tag-helper"></a><span data-ttu-id="db492-171">분산된 캐시 태그 도우미</span><span class="sxs-lookup"><span data-stu-id="db492-171">Distributed Cache Tag Helper</span></span>

<span data-ttu-id="db492-172">분산 캐시 태그 도우미와 MVC 뷰 또는 분산된 클라우드 또는 웹 팜 시나리오에 대 한 Razor 페이지 콘텐츠를 캐시할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="db492-172">You can cache the content from an MVC view or Razor Page in distributed cloud or web farm scenarios with the Distributed Cache Tag Helper.</span></span> <span data-ttu-id="db492-173">분산 캐시 태그 도우미를 사용 하 여 SQL Server 또는 Redis 데이터를 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="db492-173">The Distributed Cache Tag Helper uses SQL Server or Redis to store data.</span></span>

<span data-ttu-id="db492-174">자세한 내용은 참조 [분산 캐시 태그 도우미](xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper)합니다.</span><span class="sxs-lookup"><span data-stu-id="db492-174">For more information, see [Distributed Cache Tag Helper](xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper).</span></span>

## <a name="responsecache-attribute"></a><span data-ttu-id="db492-175">ResponseCache 특성</span><span class="sxs-lookup"><span data-stu-id="db492-175">ResponseCache attribute</span></span>

<span data-ttu-id="db492-176">`ResponseCacheAttribute` 응답 캐시에서 적절 한 헤더를 설정 하는 데 필요한 매개 변수를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="db492-176">The `ResponseCacheAttribute` specifies the parameters necessary for setting appropriate headers in response caching.</span></span> <span data-ttu-id="db492-177">참조 [ResponseCacheAttribute](/aspnet/core/api/microsoft.aspnetcore.mvc.responsecacheattribute) 에 대 한 설명은 매개 변수입니다.</span><span class="sxs-lookup"><span data-stu-id="db492-177">See [ResponseCacheAttribute](/aspnet/core/api/microsoft.aspnetcore.mvc.responsecacheattribute) for a description of the parameters.</span></span>

> [!WARNING]
> <span data-ttu-id="db492-178">인증 된 클라이언트에 대 한 정보를 포함 하는 콘텐츠에 대 한 캐싱을 사용 하지 않도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="db492-178">Disable caching for content that contains information for authenticated clients.</span></span> <span data-ttu-id="db492-179">사용자의 id 또는 사용자의 로그인 여부에 따라 변경 되지 않는 콘텐츠에 대 한 캐싱만 설정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="db492-179">Caching should only be enabled for content that doesn't change based on a user's identity or whether a user is logged in.</span></span>

<span data-ttu-id="db492-180">`VaryByQueryKeys string[]`(ASP.NET Core 1.1 이상 필요):으로 설정 하면 응답 캐싱 미들웨어 저장된 응답이 변경 쿼리 키의 지정 된 목록 값에 의해 합니다.</span><span class="sxs-lookup"><span data-stu-id="db492-180">`VaryByQueryKeys string[]` (requires ASP.NET Core 1.1 and higher): When set, the Response Caching Middleware varies the stored response by the values of the given list of query keys.</span></span> <span data-ttu-id="db492-181">응답의 캐싱 미들웨어를 설정 하려면 설정 해야 합니다는 `VaryByQueryKeys` 속성; 그렇지 않으면 런타임 예외가 throw 됩니다.</span><span class="sxs-lookup"><span data-stu-id="db492-181">The Response Caching Middleware must be enabled to set the `VaryByQueryKeys` property; otherwise, a runtime exception is thrown.</span></span> <span data-ttu-id="db492-182">에 대 한 해당 HTTP 헤더가 없으면는 `VaryByQueryKeys` 속성입니다.</span><span class="sxs-lookup"><span data-stu-id="db492-182">There's no corresponding HTTP header for the `VaryByQueryKeys` property.</span></span> <span data-ttu-id="db492-183">이 속성은 응답 캐싱 미들웨어가 처리 하는 HTTP 기능.</span><span class="sxs-lookup"><span data-stu-id="db492-183">This property is an HTTP feature handled by the Response Caching Middleware.</span></span> <span data-ttu-id="db492-184">캐시 된 응답을 제공 하도록 미들웨어에 대 한 쿼리 문자열 및 쿼리 문자열 값을 이전 요청을 일치 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="db492-184">For the middleware to serve a cached response, the query string and query string value must match a previous request.</span></span> <span data-ttu-id="db492-185">요청 및 결과 다음 표에 표시 된 순서를 예로 들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="db492-185">For example, consider the sequence of requests and results shown in the following table.</span></span>

| <span data-ttu-id="db492-186">요청</span><span class="sxs-lookup"><span data-stu-id="db492-186">Request</span></span>                          | <span data-ttu-id="db492-187">결과</span><span class="sxs-lookup"><span data-stu-id="db492-187">Result</span></span>                   |
| -------------------------------- | ------------------------ |
| `http://example.com?key1=value1` | <span data-ttu-id="db492-188">서버에서 반환 된</span><span class="sxs-lookup"><span data-stu-id="db492-188">Returned from server</span></span>     |
| `http://example.com?key1=value1` | <span data-ttu-id="db492-189">미들웨어에서 반환</span><span class="sxs-lookup"><span data-stu-id="db492-189">Returned from middleware</span></span> |
| `http://example.com?key1=value2` | <span data-ttu-id="db492-190">서버에서 반환 된</span><span class="sxs-lookup"><span data-stu-id="db492-190">Returned from server</span></span>     |

<span data-ttu-id="db492-191">첫 번째 요청이 서버에서 반환 하 고 미들웨어에 캐시 합니다.</span><span class="sxs-lookup"><span data-stu-id="db492-191">The first request is returned by the server and cached in middleware.</span></span> <span data-ttu-id="db492-192">이전 요청과 일치 하는 쿼리 문자열 때문에 두 번째 요청 미들웨어에서 반환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="db492-192">The second request is returned by middleware because the query string matches the previous request.</span></span> <span data-ttu-id="db492-193">세 번째 요청에에서 없는 경우 미들웨어 캐시에서 쿼리 문자열 값인 이전 요청이 일치 하지 않으므로</span><span class="sxs-lookup"><span data-stu-id="db492-193">The third request is not in the middleware cache because the query string value doesn't match a previous request.</span></span> 

<span data-ttu-id="db492-194">`ResponseCacheAttribute` 는 구성 하 고 만드는 데 사용 됩니다 (통해 `IFilterFactory`)는 `ResponseCacheFilter`합니다.</span><span class="sxs-lookup"><span data-stu-id="db492-194">The `ResponseCacheAttribute` is used to configure and create (via `IFilterFactory`) a `ResponseCacheFilter`.</span></span> <span data-ttu-id="db492-195">`ResponseCacheFilter` 적절 한 HTTP 헤더 및 응답의 기능을 업데이트 하는 작업을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="db492-195">The `ResponseCacheFilter` performs the work of updating the appropriate HTTP headers and features of the response.</span></span> <span data-ttu-id="db492-196">필터:</span><span class="sxs-lookup"><span data-stu-id="db492-196">The filter:</span></span>

* <span data-ttu-id="db492-197">에 대 한 모든 기존 헤더를 제거 `Vary`, `Cache-Control`, 및 `Pragma`합니다.</span><span class="sxs-lookup"><span data-stu-id="db492-197">Removes any existing headers for `Vary`, `Cache-Control`, and `Pragma`.</span></span> 
* <span data-ttu-id="db492-198">에 설정 된 속성에 따라 적절 한 헤더를 작성은 `ResponseCacheAttribute`합니다.</span><span class="sxs-lookup"><span data-stu-id="db492-198">Writes out the appropriate headers based on the properties set in the `ResponseCacheAttribute`.</span></span> 
* <span data-ttu-id="db492-199">업데이트 하는 경우에 캐싱 HTTP 기능 응답 `VaryByQueryKeys` 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="db492-199">Updates the response caching HTTP feature if `VaryByQueryKeys` is set.</span></span>

### <a name="vary"></a><span data-ttu-id="db492-200">변경</span><span class="sxs-lookup"><span data-stu-id="db492-200">Vary</span></span>

<span data-ttu-id="db492-201">이 헤더가 작성만 되 고 `VaryByHeader` 속성을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="db492-201">This header is only written when the `VaryByHeader` property is set.</span></span> <span data-ttu-id="db492-202">로 설정 되 고 `Vary` 속성의 값입니다.</span><span class="sxs-lookup"><span data-stu-id="db492-202">It's set to the `Vary` property's value.</span></span> <span data-ttu-id="db492-203">다음 샘플에서는 `VaryByHeader` 속성:</span><span class="sxs-lookup"><span data-stu-id="db492-203">The following sample uses the `VaryByHeader` property:</span></span>

[!code-csharp[Main](response/sample/Controllers/HomeController.cs?name=snippet_VaryByHeader&highlight=1)]

<span data-ttu-id="db492-204">브라우저의 네트워크 도구를 사용 하 여 응답 헤더를 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="db492-204">You can view the response headers with your browser's network tools.</span></span> <span data-ttu-id="db492-205">다음 이미지에서 출력 가장자리 f12 키를 보여 줍니다.는 **네트워크** 때 탭는 `About2` 동작 메서드가 새로 고쳐집니다.</span><span class="sxs-lookup"><span data-stu-id="db492-205">The following image shows the Edge F12 output on the **Network** tab when the `About2` action method is refreshed:</span></span>

![About2 동작 메서드가 호출 될 때 네트워크 탭에서 F12 출력 가장자리](response/_static/vary.png)

### <a name="nostore-and-locationnone"></a><span data-ttu-id="db492-207">NoStore 및 Location.None</span><span class="sxs-lookup"><span data-stu-id="db492-207">NoStore and Location.None</span></span>

<span data-ttu-id="db492-208">`NoStore`대부분의 다른 속성을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="db492-208">`NoStore` overrides most of the other properties.</span></span> <span data-ttu-id="db492-209">이 속성이로 설정 된 경우 `true`, `Cache-Control` 헤더가로 설정 되 `no-store`합니다.</span><span class="sxs-lookup"><span data-stu-id="db492-209">When this property is set to `true`, the `Cache-Control` header is set to `no-store`.</span></span> <span data-ttu-id="db492-210">경우 `Location` 로 설정 된 `None`:</span><span class="sxs-lookup"><span data-stu-id="db492-210">If `Location` is set to `None`:</span></span>

* <span data-ttu-id="db492-211">`Cache-Control`이 `no-store,no-cache`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="db492-211">`Cache-Control` is set to `no-store,no-cache`.</span></span>
* <span data-ttu-id="db492-212">`Pragma`이 `no-cache`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="db492-212">`Pragma` is set to `no-cache`.</span></span>

<span data-ttu-id="db492-213">경우 `NoStore` 은 `false` 및 `Location` 은 `None`, `Cache-Control` 및 `Pragma` 로 설정 `no-cache`합니다.</span><span class="sxs-lookup"><span data-stu-id="db492-213">If `NoStore` is `false` and `Location` is `None`, `Cache-Control` and `Pragma` are set to `no-cache`.</span></span>

<span data-ttu-id="db492-214">일반적으로 설정 `NoStore` 를 `true` 오류 페이지에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="db492-214">You typically set `NoStore` to `true` on error pages.</span></span> <span data-ttu-id="db492-215">예:</span><span class="sxs-lookup"><span data-stu-id="db492-215">For example:</span></span>

[!code-csharp[Main](response/sample/Controllers/HomeController.cs?name=snippet1&highlight=1)]

<span data-ttu-id="db492-216">그 결과 다음과 같은 헤더가 같습니다.</span><span class="sxs-lookup"><span data-stu-id="db492-216">This results in the following headers:</span></span>

```
Cache-Control: no-store,no-cache
Pragma: no-cache
```

### <a name="location-and-duration"></a><span data-ttu-id="db492-217">위치 및 기간</span><span class="sxs-lookup"><span data-stu-id="db492-217">Location and Duration</span></span>

<span data-ttu-id="db492-218">캐싱을 사용 하도록 설정 하려면 `Duration` 양수 값으로 설정 되어 있어야 하 고 `Location` 납입이 되어야 `Any` (기본값) 또는 `Client`합니다.</span><span class="sxs-lookup"><span data-stu-id="db492-218">To enable caching, `Duration` must be set to a positive value and `Location` must be either `Any` (the default) or `Client`.</span></span> <span data-ttu-id="db492-219">이 경우에 `Cache-Control` 헤더 뒤 위치 값으로 설정 됩니다는 `max-age` 응답 합니다.</span><span class="sxs-lookup"><span data-stu-id="db492-219">In this case, the `Cache-Control` header is set to the location value followed by the `max-age` of the response.</span></span>

> [!NOTE]
> <span data-ttu-id="db492-220">`Location`옵션의 `Any` 및 `Client` 번역할 `Cache-Control` 의 헤더 값 `public` 및 `private`각각.</span><span class="sxs-lookup"><span data-stu-id="db492-220">`Location`'s options of `Any` and `Client` translate into `Cache-Control` header values of `public` and `private`, respectively.</span></span> <span data-ttu-id="db492-221">앞에서 설명한 대로 설정 `Location` 를 `None` 두 설정 `Cache-Control` 및 `Pragma` 헤더를 `no-cache`합니다.</span><span class="sxs-lookup"><span data-stu-id="db492-221">As noted previously, setting `Location` to `None` sets both `Cache-Control` and `Pragma` headers to `no-cache`.</span></span>

<span data-ttu-id="db492-222">다음을 설정 하 여 생성 된 헤더를 보여 주는 예제는 `Duration` 기본 들어오거나 `Location` 값:</span><span class="sxs-lookup"><span data-stu-id="db492-222">Below is an example showing the headers produced by setting `Duration` and leaving the default `Location` value:</span></span>

[!code-csharp[Main](response/sample/Controllers/HomeController.cs?name=snippet_duration&highlight=1)]

<span data-ttu-id="db492-223">다음 헤더를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="db492-223">This produces the following header:</span></span>

```
Cache-Control: public,max-age=60
```

### <a name="cache-profiles"></a><span data-ttu-id="db492-224">캐시 프로필</span><span class="sxs-lookup"><span data-stu-id="db492-224">Cache profiles</span></span>

<span data-ttu-id="db492-225">복제 하는 대신 `ResponseCache` 에 MVC를 설정할 때 옵션으로 많은 컨트롤러 동작 특성에 캐시 프로필에서 설정을 구성할 수 있습니다는 `ConfigureServices` 메서드에서 `Startup`합니다.</span><span class="sxs-lookup"><span data-stu-id="db492-225">Instead of duplicating `ResponseCache` settings on many controller action attributes, cache profiles can be configured as options when setting up MVC in the `ConfigureServices` method in `Startup`.</span></span> <span data-ttu-id="db492-226">참조 된 캐시 프로필에서 찾은 값에서 기본값으로 사용 되는 `ResponseCache` 특성 및 특성에 지정 된 모든 속성에 의해 무시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="db492-226">Values found in a referenced cache profile are used as the defaults by the `ResponseCache` attribute and are overridden by any properties specified on the attribute.</span></span>

<span data-ttu-id="db492-227">캐시 프로필을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="db492-227">Setting up a cache profile:</span></span>

[!code-csharp[Main](response/sample/Startup.cs?name=snippet1)] 

<span data-ttu-id="db492-228">캐시 프로필을 참조 합니다.</span><span class="sxs-lookup"><span data-stu-id="db492-228">Referencing a cache profile:</span></span>

[!code-csharp[Main](response/sample/Controllers/HomeController.cs?name=snippet_controller&highlight=1,4)]

<span data-ttu-id="db492-229">`ResponseCache` 모두 작업 (메서드) 및 컨트롤러 (클래스)에 특성을 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="db492-229">The `ResponseCache` attribute can be applied both to actions (methods) and controllers (classes).</span></span> <span data-ttu-id="db492-230">메서드 수준 특성 클래스 수준 특성에 지정 된 설정을 무시 합니다.</span><span class="sxs-lookup"><span data-stu-id="db492-230">Method-level attributes override the settings specified in class-level attributes.</span></span>

<span data-ttu-id="db492-231">위의 예제에서는 클래스 수준 특성 메서드 수준의 특성 기간이 60 초로 설정 캐시 프로필을 참조 하는 동안 30 초의 지속 시간을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="db492-231">In the above example, a class-level attribute specifies a duration of 30 seconds, while a method-level attribute references a cache profile with a duration set to 60 seconds.</span></span>

<span data-ttu-id="db492-232">결과 헤더:</span><span class="sxs-lookup"><span data-stu-id="db492-232">The resulting header:</span></span>

```
Cache-Control: public,max-age=60
```

## <a name="additional-resources"></a><span data-ttu-id="db492-233">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="db492-233">Additional resources</span></span>

* [<span data-ttu-id="db492-234">사양에서 HTTP에서 캐싱</span><span class="sxs-lookup"><span data-stu-id="db492-234">Caching in HTTP from the specification</span></span>](https://tools.ietf.org/html/rfc7234#section-3)
* [<span data-ttu-id="db492-235">캐시 제어</span><span class="sxs-lookup"><span data-stu-id="db492-235">Cache-Control</span></span>](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9)
* [<span data-ttu-id="db492-236">응답 캐싱 미들웨어</span><span class="sxs-lookup"><span data-stu-id="db492-236">Response Caching Middleware</span></span>](xref:performance/caching/middleware)
