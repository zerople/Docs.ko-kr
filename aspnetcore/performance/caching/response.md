---
title: "응답 캐시"
author: rick-anderson
description: "응답을 대역폭을 줄이고 성능을 향상 하려면 캐시를 사용 하는 방법에 설명 합니다."
keywords: "ASP.NET Core, 캐싱, HTTP 헤더 응답"
ms.author: riande
manager: wpickett
ms.date: 07/10/2017
ms.topic: article
ms.assetid: cb42035a-60b0-472e-a614-cb79f443f654
ms.prod: asp.net-core
uid: performance/caching/response
ms.openlocfilehash: 97bc56a3cfe7383f207a4f621ab3fe8b8dc258ef
ms.sourcegitcommit: 67f54fabbfa4e3942f5bfe1f8a7fdfe4a7a75358
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/19/2017
---
# <a name="response-caching"></a><span data-ttu-id="78ab4-104">응답 캐시</span><span class="sxs-lookup"><span data-stu-id="78ab4-104">Response Caching</span></span>

<span data-ttu-id="78ab4-105">여 [John Luo](https://github.com/JunTaoLuo), [Rick Anderson](https://twitter.com/RickAndMSFT), 및 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="78ab4-105">By [John Luo](https://github.com/JunTaoLuo), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Steve Smith](https://ardalis.com/)</span></span>

[<span data-ttu-id="78ab4-106">샘플 코드 보기 또는 다운로드</span><span class="sxs-lookup"><span data-stu-id="78ab4-106">View or download sample code</span></span>](https://github.com/aspnet/Docs/tree/master/aspnetcore/performance/caching/response/sample)

## <a name="what-is-response-caching"></a><span data-ttu-id="78ab4-107">응답 캐시 하는 것이 무엇입니까</span><span class="sxs-lookup"><span data-stu-id="78ab4-107">What is Response Caching</span></span>

<span data-ttu-id="78ab4-108">*응답 캐시* 응답을 캐시 관련 헤더를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="78ab4-108">*Response caching* adds cache-related headers to responses.</span></span> <span data-ttu-id="78ab4-109">이러한 헤더 클라이언트, 프록시 및 응답을 캐시 미들웨어 용도 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="78ab4-109">These headers specify how you want client, proxy and middleware to cache responses.</span></span> <span data-ttu-id="78ab4-110">응답 캐시 클라이언트 또는 프록시 웹 서버에 수행 된 요청 수를 줄일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="78ab4-110">Response caching can reduce the number of requests a client or proxy makes to the web server.</span></span> <span data-ttu-id="78ab4-111">응답 캐시 양을 줄일 수도 작업의 응답을 생성 하는 웹 서버 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="78ab4-111">Response caching can also reduce the amount of work the web server performs to generate the response.</span></span> 

<span data-ttu-id="78ab4-112">캐시에 사용 되는 기본 HTTP 헤더는 `Cache-Control`합니다.</span><span class="sxs-lookup"><span data-stu-id="78ab4-112">The primary HTTP header used for caching is `Cache-Control`.</span></span> <span data-ttu-id="78ab4-113">참조는 [HTTP 1.1 캐싱](https://tools.ietf.org/html/rfc7234#section-5.2) 자세한 정보에 대 한 합니다.</span><span class="sxs-lookup"><span data-stu-id="78ab4-113">See the [HTTP 1.1 Caching](https://tools.ietf.org/html/rfc7234#section-5.2) for more information.</span></span> <span data-ttu-id="78ab4-114">같은 캐시 지시문:</span><span class="sxs-lookup"><span data-stu-id="78ab4-114">Common cache directives:</span></span>

* [<span data-ttu-id="78ab4-115">public</span><span class="sxs-lookup"><span data-stu-id="78ab4-115">public</span></span>](https://tools.ietf.org/html/rfc7234#section-5.2.2.5)
* [<span data-ttu-id="78ab4-116">private</span><span class="sxs-lookup"><span data-stu-id="78ab4-116">private</span></span>](https://tools.ietf.org/html/rfc7234#section-5.2.2.6)
* [<span data-ttu-id="78ab4-117">캐시 없음</span><span class="sxs-lookup"><span data-stu-id="78ab4-117">no-cache</span></span>](https://tools.ietf.org/html/rfc7234#section-5.2.1.4)
* [<span data-ttu-id="78ab4-118">Pragma</span><span class="sxs-lookup"><span data-stu-id="78ab4-118">Pragma</span></span>](https://tools.ietf.org/html/rfc7234#section-5.4)
* [<span data-ttu-id="78ab4-119">변경</span><span class="sxs-lookup"><span data-stu-id="78ab4-119">Vary</span></span>](https://tools.ietf.org/html/rfc7231#section-7.1.4)

<span data-ttu-id="78ab4-120">웹 서버 캐싱 미들웨어의 응답을 추가 하 여 응답을 캐시할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="78ab4-120">The web server can cache responses by adding the response caching middleware.</span></span> <span data-ttu-id="78ab4-121">참조 [미들웨어 캐싱 응답](middleware.md) 자세한 정보에 대 한 합니다.</span><span class="sxs-lookup"><span data-stu-id="78ab4-121">See [Response caching middleware](middleware.md) for more information.</span></span>

## <a name="distributed-cache-tag-helper"></a><span data-ttu-id="78ab4-122">분산된 캐시 태그 도우미</span><span class="sxs-lookup"><span data-stu-id="78ab4-122">Distributed Cache Tag Helper</span></span>

<span data-ttu-id="78ab4-123">[분산 캐시 태그 도우미](xref:mvc/views/tag-helpers/builtin-th/DistributedCacheTagHelper) 하면 분산 캐싱 합니다.</span><span class="sxs-lookup"><span data-stu-id="78ab4-123">The [Distributed Cache Tag Helper](xref:mvc/views/tag-helpers/builtin-th/DistributedCacheTagHelper) enables distributed caching.</span></span>


## <a name="responsecache-attribute"></a><span data-ttu-id="78ab4-124">ResponseCache 특성</span><span class="sxs-lookup"><span data-stu-id="78ab4-124">ResponseCache Attribute</span></span>

<span data-ttu-id="78ab4-125">`ResponseCacheAttribute` 응답 캐시에서 적절 한 헤더를 설정 하는 데 필요한 매개 변수를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="78ab4-125">The `ResponseCacheAttribute` specifies the parameters necessary for setting appropriate headers in response caching.</span></span> <span data-ttu-id="78ab4-126">참조 [ResponseCacheAttribute](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.responsecacheattribute) 에 대 한 설명은 매개 변수입니다.</span><span class="sxs-lookup"><span data-stu-id="78ab4-126">See [ResponseCacheAttribute](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.responsecacheattribute)  for a description of the parameters.</span></span>

>[!WARNING]
> <span data-ttu-id="78ab4-127">인증 된 클라이언트에 대 한 정보를 포함 하는 콘텐츠에 대 한 캐싱을 사용 하지 않도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="78ab4-127">Disable caching for content that contains information for authenticated clients.</span></span> <span data-ttu-id="78ab4-128">Caching에 설정 해야 여부나 사용자가 로그인에 사용자의 id를 기반으로 하는 내용이 변경 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="78ab4-128">Caching should only be enabled for content that does not change based on a user's identity, or whether a user is logged in.</span></span>

<span data-ttu-id="78ab4-129">`VaryByQueryKeys string[]`(ASP.NET Core 1.1.0 필요 이상):으로 설정 하면 미들웨어 캐싱 응답 저장된 응답에 따라 달라 집니다 쿼리 키의 지정 된 목록의 값입니다.</span><span class="sxs-lookup"><span data-stu-id="78ab4-129">`VaryByQueryKeys string[]` (requires ASP.NET Core 1.1.0 and higher): When set, the response caching middleware will vary the stored response by the values of the given list of query keys.</span></span> <span data-ttu-id="78ab4-130">캐싱 미들웨어의 응답을 설정 하려면 설정 해야 합니다는 `VaryByQueryKeys` 속성, 그렇지 않으면 런타임 예외가 throw 됩니다.</span><span class="sxs-lookup"><span data-stu-id="78ab4-130">The response caching middleware must be enabled to set the `VaryByQueryKeys` property, otherwise a runtime exception will be thrown.</span></span> <span data-ttu-id="78ab4-131">에 대 한 해당 HTTP 헤더가 없으면는 `VaryByQueryKeys` 속성입니다.</span><span class="sxs-lookup"><span data-stu-id="78ab4-131">There is no corresponding HTTP header for the `VaryByQueryKeys` property.</span></span> <span data-ttu-id="78ab4-132">이 속성은 캐싱 미들웨어의 응답에서 처리 하는 HTTP 기능.</span><span class="sxs-lookup"><span data-stu-id="78ab4-132">This property is an HTTP feature handled by the response caching middleware.</span></span> <span data-ttu-id="78ab4-133">캐시 된 응답을 제공 하도록 미들웨어에 대 한 쿼리 문자열 및 쿼리 문자열 값을 이전 요청을 일치 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="78ab4-133">For the middleware to serve a cached response, the query string and query string value must match a previous request.</span></span> <span data-ttu-id="78ab4-134">예를 들어 다음 시퀀스를 살펴봅니다.</span><span class="sxs-lookup"><span data-stu-id="78ab4-134">For example, consider the following sequence:</span></span>

| <span data-ttu-id="78ab4-135">요청</span><span class="sxs-lookup"><span data-stu-id="78ab4-135">Request</span></span>          | <span data-ttu-id="78ab4-136">결과</span><span class="sxs-lookup"><span data-stu-id="78ab4-136">Result</span></span> |
| ----------------- | ------------ | 
| `http://example.com?key1=value1` | <span data-ttu-id="78ab4-137">서버에서 반환 된</span><span class="sxs-lookup"><span data-stu-id="78ab4-137">returned from server</span></span> |
| `http://example.com?key1=value1` | <span data-ttu-id="78ab4-138">미들웨어에서 반환</span><span class="sxs-lookup"><span data-stu-id="78ab4-138">returned from middleware</span></span> |
| `http://example.com?key1=value2` | <span data-ttu-id="78ab4-139">서버에서 반환 된</span><span class="sxs-lookup"><span data-stu-id="78ab4-139">returned from server</span></span> |

<span data-ttu-id="78ab4-140">첫 번째 요청이 서버에서 반환 하 고 미들웨어에 캐시 합니다.</span><span class="sxs-lookup"><span data-stu-id="78ab4-140">The first request is returned by the server and cached in middleware.</span></span> <span data-ttu-id="78ab4-141">이전 요청과 일치 하는 쿼리 문자열 때문에 두 번째 요청 미들웨어에서 반환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="78ab4-141">The second request is returned by middleware because the query string matches the previous request.</span></span> <span data-ttu-id="78ab4-142">세 번째 요청에에서 없는 경우 미들웨어 캐시에서 쿼리 문자열 값인 이전 요청이 일치 하지 않으므로</span><span class="sxs-lookup"><span data-stu-id="78ab4-142">The third request is not in the middleware cache because the query string value doesn't match a previous request.</span></span> 

<span data-ttu-id="78ab4-143">`ResponseCacheAttribute` 는 구성 하 고 만드는 데 사용 됩니다 (통해 `IFilterFactory`)는 `ResponseCacheFilter`합니다.</span><span class="sxs-lookup"><span data-stu-id="78ab4-143">The `ResponseCacheAttribute` is used to configure and create (via `IFilterFactory`) a `ResponseCacheFilter`.</span></span> <span data-ttu-id="78ab4-144">`ResponseCacheFilter` 적절 한 HTTP 헤더 및 응답의 기능을 업데이트 하는 작업을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="78ab4-144">The `ResponseCacheFilter` performs the work of updating the appropriate HTTP headers and features of the response.</span></span> <span data-ttu-id="78ab4-145">필터:</span><span class="sxs-lookup"><span data-stu-id="78ab4-145">The filter:</span></span>

* <span data-ttu-id="78ab4-146">에 대 한 모든 기존 헤더를 제거 `Vary`, `Cache-Control`, 및 `Pragma`합니다.</span><span class="sxs-lookup"><span data-stu-id="78ab4-146">Removes any existing headers for `Vary`, `Cache-Control`, and `Pragma`.</span></span> 
* <span data-ttu-id="78ab4-147">에 설정 된 속성에 따라 적절 한 헤더를 작성은 `ResponseCacheAttribute`합니다.</span><span class="sxs-lookup"><span data-stu-id="78ab4-147">Writes out the appropriate headers based on the properties set in the `ResponseCacheAttribute`.</span></span> 
* <span data-ttu-id="78ab4-148">업데이트 하는 경우에 캐싱 HTTP 기능 응답 `VaryByQueryKeys` 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="78ab4-148">Updates the response caching HTTP feature if `VaryByQueryKeys` is set.</span></span>

### <a name="vary"></a><span data-ttu-id="78ab4-149">변경</span><span class="sxs-lookup"><span data-stu-id="78ab4-149">Vary</span></span>

<span data-ttu-id="78ab4-150">이 헤더가 작성만 되 고 `VaryByHeader` 속성을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="78ab4-150">This header is only written when the `VaryByHeader` property is set.</span></span> <span data-ttu-id="78ab4-151">로 설정 되 고 `Vary` 속성의 값입니다.</span><span class="sxs-lookup"><span data-stu-id="78ab4-151">It is set to the `Vary` property's value.</span></span> <span data-ttu-id="78ab4-152">다음 샘플에서는 `VaryByHeader` 속성입니다.</span><span class="sxs-lookup"><span data-stu-id="78ab4-152">The following sample uses the `VaryByHeader` property.</span></span>

[!code-csharp[Main](response/sample/Controllers/HomeController.cs?name=snippet_VaryByHeader&highlight=1)]

<span data-ttu-id="78ab4-153">브라우저 네트워크 도구와 함께 응답 헤더를 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="78ab4-153">You can view the response headers with your browsers network tools.</span></span> <span data-ttu-id="78ab4-154">다음 이미지에서 출력 가장자리 f12 키를 보여 줍니다.는 **네트워크** 때 탭의 `About2` 동작 메서드가 새로 고쳐집니다.</span><span class="sxs-lookup"><span data-stu-id="78ab4-154">The following image shows the Edge F12 output on the **Network** tab when the `About2` action method is refreshed.</span></span> 

![F12 출력에서 가장자리는 * * 네트워크 * * 'About2' 동작 메서드가 호출 될 때 탭](response/_static/vary.png)

### <a name="nostore-and-locationnone"></a><span data-ttu-id="78ab4-156">NoStore 및 Location.None</span><span class="sxs-lookup"><span data-stu-id="78ab4-156">NoStore and Location.None</span></span>

<span data-ttu-id="78ab4-157">`NoStore`대부분의 다른 속성을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="78ab4-157">`NoStore` overrides most of the other properties.</span></span> <span data-ttu-id="78ab4-158">이 속성이로 설정 된 경우 `true`, `Cache-Control` 헤더 "store"로 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="78ab4-158">When this property is set to `true`, the `Cache-Control` header will be set to "no-store".</span></span> <span data-ttu-id="78ab4-159">경우 `Location` 로 설정 된 `None`:</span><span class="sxs-lookup"><span data-stu-id="78ab4-159">If `Location` is set to `None`:</span></span>

* <span data-ttu-id="78ab4-160">`Cache-Control`이 `"no-store, no-cache"`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="78ab4-160">`Cache-Control` is set to `"no-store, no-cache"`.</span></span> 
* <span data-ttu-id="78ab4-161">`Pragma`이 `no-cache`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="78ab4-161">`Pragma` is set to `no-cache`.</span></span> 

<span data-ttu-id="78ab4-162">경우 `NoStore` 은 `false` 및 `Location` 은 `None`, `Cache-Control` 및 `Pragma` 로 설정 됩니다 `no-cache`합니다.</span><span class="sxs-lookup"><span data-stu-id="78ab4-162">If `NoStore` is `false` and `Location` is `None`,  `Cache-Control` and `Pragma` will be set to `no-cache`.</span></span>

<span data-ttu-id="78ab4-163">일반적으로 설정 `NoStore` 를 `true` 오류 페이지에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="78ab4-163">You typically set `NoStore` to `true` on error pages.</span></span> <span data-ttu-id="78ab4-164">예:</span><span class="sxs-lookup"><span data-stu-id="78ab4-164">For example:</span></span>

[!code-csharp[Main](response/sample/Controllers/HomeController.cs?name=snippet1&highlight=1)]

<span data-ttu-id="78ab4-165">이렇게 하면 다음과 같은 헤더가:</span><span class="sxs-lookup"><span data-stu-id="78ab4-165">This will result in the following headers:</span></span>

```javascript
Cache-Control: no-store,no-cache
Pragma: no-cache
```

### <a name="location-and-duration"></a><span data-ttu-id="78ab4-166">위치 및 기간</span><span class="sxs-lookup"><span data-stu-id="78ab4-166">Location and Duration</span></span>

<span data-ttu-id="78ab4-167">캐싱을 사용 하도록 설정 하려면 `Duration` 양수 값으로 설정 되어 있어야 하 고 `Location` 납입이 되어야 `Any` (기본값) 또는 `Client`합니다.</span><span class="sxs-lookup"><span data-stu-id="78ab4-167">To enable caching, `Duration` must be set to a positive value and `Location` must be either `Any` (the default) or `Client`.</span></span> <span data-ttu-id="78ab4-168">이 경우에 `Cache-Control` 헤더는 "최대-기간" 응답의 다음 위치 값으로 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="78ab4-168">In this case, the `Cache-Control` header will be set to the location value followed by the "max-age" of the response.</span></span>

> [!NOTE]
> <span data-ttu-id="78ab4-169">`Location`옵션의 `Any` 및 `Client` 번역할 `Cache-Control` 의 헤더 값 `public` 및 `private`각각.</span><span class="sxs-lookup"><span data-stu-id="78ab4-169">`Location`'s options of `Any` and `Client` translate into `Cache-Control` header values of `public` and `private`, respectively.</span></span> <span data-ttu-id="78ab4-170">앞에서 설명한 대로 설정 `Location` 를 `None` 는 둘 다 설정 `Cache-Control` 및 `Pragma` 헤더를 `no-cache`합니다.</span><span class="sxs-lookup"><span data-stu-id="78ab4-170">As noted previously, setting `Location` to `None` will set both `Cache-Control` and `Pragma` headers to `no-cache`.</span></span>

<span data-ttu-id="78ab4-171">다음을 설정 하 여 생성 된 헤더를 보여 주는 예제는 `Duration` 기본 들어오거나 `Location` 값입니다.</span><span class="sxs-lookup"><span data-stu-id="78ab4-171">Below is an example showing the headers produced by setting `Duration` and leaving the default `Location` value.</span></span>

[!code-csharp[Main](response/sample/Controllers/HomeController.cs?name=snippet_duration&highlight=1)]

<span data-ttu-id="78ab4-172">다음 헤더를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="78ab4-172">Produces the following headers:</span></span>

```javascript
Cache-Control: public,max-age=60
   ```

### <a name="cache-profiles"></a><span data-ttu-id="78ab4-173">캐시 프로필</span><span class="sxs-lookup"><span data-stu-id="78ab4-173">Cache Profiles</span></span>

<span data-ttu-id="78ab4-174">복제 하는 대신 `ResponseCache` 에 MVC를 설정할 때 옵션으로 많은 컨트롤러 동작 특성에 캐시 프로필에서 설정을 구성할 수 있습니다는 `ConfigureServices` 메서드에서 `Startup`합니다.</span><span class="sxs-lookup"><span data-stu-id="78ab4-174">Instead of duplicating `ResponseCache` settings on many controller action attributes, cache profiles can be configured as options when setting up MVC in the `ConfigureServices` method in `Startup`.</span></span> <span data-ttu-id="78ab4-175">기본적으로 참조 된 캐시 프로필의 값이 사용 됩니다는 `ResponseCache` 특성과 특성에 지정 된 임의 속성으로 재정의 됩니다.</span><span class="sxs-lookup"><span data-stu-id="78ab4-175">Values found in a referenced cache profile will be used as the defaults by the `ResponseCache` attribute, and will be overridden by any properties specified on the attribute.</span></span>

<span data-ttu-id="78ab4-176">캐시 프로필을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="78ab4-176">Setting up a cache profile:</span></span>

[!code-csharp[Main](response/sample/Startup.cs?name=snippet1)] 

<span data-ttu-id="78ab4-177">캐시 프로필을 참조 합니다.</span><span class="sxs-lookup"><span data-stu-id="78ab4-177">Referencing a cache profile:</span></span>

[!code-csharp[Main](response/sample/Controllers/HomeController.cs?name=snippet_controller&highlight=1,4)]

<span data-ttu-id="78ab4-178">`ResponseCache` 모두 작업 (메서드) 뿐만 아니라 컨트롤러 (클래스)에 특성을 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="78ab4-178">The `ResponseCache` attribute can be applied both to actions (methods) as well as controllers (classes).</span></span> <span data-ttu-id="78ab4-179">메서드 수준 특성 클래스 수준 특성에 지정 된 설정을 재정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="78ab4-179">Method-level attributes will override the settings specified in class-level attributes.</span></span>

<span data-ttu-id="78ab4-180">위의 예제에서는 클래스 수준 특성 메서드 수준의 특성 기간이 60 초로 설정 캐시 프로필을 참조 하는 동안 30 초의 지속 시간을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="78ab4-180">In the above example, a class-level attribute specifies a duration of 30 seconds while a method-level attributes references a cache profile with a duration set to 60 seconds.</span></span>

<span data-ttu-id="78ab4-181">결과 헤더:</span><span class="sxs-lookup"><span data-stu-id="78ab4-181">The resulting header:</span></span>

```
Cache-Control: public,max-age=60
   ```

  ### <a name="additional-resources"></a><span data-ttu-id="78ab4-182">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="78ab4-182">Additional Resources</span></span>

* [<span data-ttu-id="78ab4-183">사양에서 HTTP에서 캐싱</span><span class="sxs-lookup"><span data-stu-id="78ab4-183">Caching in HTTP from the specification</span></span>](https://tools.ietf.org/html/rfc7234#section-3)
* [<span data-ttu-id="78ab4-184">캐시 제어</span><span class="sxs-lookup"><span data-stu-id="78ab4-184">Cache-Control</span></span>](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9)
