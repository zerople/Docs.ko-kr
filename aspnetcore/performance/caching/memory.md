---
title: "ASP.NET Core의 메모리 내 캐싱"
author: rick-anderson
description: "ASP.NET Core 메모리에 데이터를 캐시 하는 방법을 보여 줍니다."
keywords: "ASP.NET Core, 캐시, 메모리 내 성능"
ms.author: riande
manager: wpickett
ms.date: 12/14/2016
ms.topic: article
ms.assetid: 819511cf-d33e-410a-b5a9-bef7fa64d2f3
ms.technology: aspnet
ms.prod: asp.net-core
uid: performance/caching/memory
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b5dca6a81a66ce2a8771f1a16e63834d6504d8b6
ms.sourcegitcommit: 78d28178345a0eea91556e4cd1adad98b1446db8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2017
---
# <a name="introduction-to-in-memory-caching-in-aspnet-core"></a><span data-ttu-id="82886-104">ASP.NET Core의 메모리 내 캐싱 소개</span><span class="sxs-lookup"><span data-stu-id="82886-104">Introduction to in-memory caching in ASP.NET Core</span></span>

<span data-ttu-id="82886-105">여 [Rick Anderson](https://twitter.com/RickAndMSFT), [John Luo](https://github.com/JunTaoLuo), 및 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="82886-105">By [Rick Anderson](https://twitter.com/RickAndMSFT), [John Luo](https://github.com/JunTaoLuo), and [Steve Smith](https://ardalis.com/)</span></span>

[<span data-ttu-id="82886-106">샘플 코드 보기 또는 다운로드</span><span class="sxs-lookup"><span data-stu-id="82886-106">View or download sample code</span></span>](https://github.com/aspnet/Docs/tree/master/aspnetcore/performance/caching/memory/sample)

## <a name="caching-basics"></a><span data-ttu-id="82886-107">캐싱 기본 사항</span><span class="sxs-lookup"><span data-stu-id="82886-107">Caching basics</span></span>

<span data-ttu-id="82886-108">캐싱 크게 향상 시킬 수 성능 및 확장성 응용 프로그램의 콘텐츠를 생성 하는 데 필요한 작업을 줄여 합니다.</span><span class="sxs-lookup"><span data-stu-id="82886-108">Caching can significantly improve the performance and scalability of an app by reducing the work required to generate content.</span></span> <span data-ttu-id="82886-109">캐싱 동작 자주 변경 되는 데이터에 가장 적합 합니다.</span><span class="sxs-lookup"><span data-stu-id="82886-109">Caching works best with data that changes infrequently.</span></span> <span data-ttu-id="82886-110">많은 반환 될 수 있는 데이터의 복사본을 만들어 캐시 원본에서 보다 더 빠릅니다.</span><span class="sxs-lookup"><span data-stu-id="82886-110">Caching makes a copy of data that can be returned much faster than from the original source.</span></span> <span data-ttu-id="82886-111">작성 하 고 캐시 된 데이터에 의존 하지를 응용 프로그램을 테스트 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="82886-111">You should write and test your app to never depend on cached data.</span></span>

<span data-ttu-id="82886-112">ASP.NET Core 몇 가지 다른 캐시를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="82886-112">ASP.NET Core supports several different caches.</span></span> <span data-ttu-id="82886-113">가장 간단한 캐시 기반는 [IMemoryCache](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.caching.memory.imemorycache)을 웹 서버의 메모리에 저장 된 캐시를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="82886-113">The simplest cache is based on the [IMemoryCache](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.caching.memory.imemorycache), which represents a cache stored in the memory of the web server.</span></span> <span data-ttu-id="82886-114">여러 서버의 서버 팜에서 실행 되는 응용 프로그램 메모리에 캐시를 사용 하는 경우 세션 스티커 인지 확인 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="82886-114">Apps which run on a server farm of multiple servers should ensure that sessions are sticky when using the in-memory cache.</span></span> <span data-ttu-id="82886-115">고정 세션 모든 클라이언트에서 후속 요청이 동일한 서버로 이동 있는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="82886-115">Sticky sessions ensure that subsequent requests from a client all go to the same server.</span></span> <span data-ttu-id="82886-116">예를 들어 Azure 웹 앱 사용 [응용 프로그램 요청 라우팅](https://www.iis.net/learn/extensions/planning-for-arr) (ARR) 동일한 서버에 모든 후속 요청을 라우팅할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="82886-116">For example, Azure Web apps use [Application Request Routing](https://www.iis.net/learn/extensions/planning-for-arr) (ARR) to route all subsequent requests to the same server.</span></span>

<span data-ttu-id="82886-117">웹 팜에서 아닌 고정 세션 필요는 [분산 캐시](distributed.md) 캐시 일관성 문제가 발생 하지 않도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="82886-117">Non-sticky sessions in a web farm require a [distributed cache](distributed.md) to avoid cache consistency problems.</span></span> <span data-ttu-id="82886-118">일부 응용 프로그램에 대 한 분산된 캐시 메모리에 캐시 보다 더 높은 규모 확장을 지원할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="82886-118">For some apps, a distributed cache can support higher scale out than an in-memory cache.</span></span> <span data-ttu-id="82886-119">분산된 캐시를 사용 하 여 외부 프로세스에 캐시 메모리 오프 로드 합니다.</span><span class="sxs-lookup"><span data-stu-id="82886-119">Using a distributed cache offloads the cache memory to an external process.</span></span> 

<span data-ttu-id="82886-120">`IMemoryCache` 하지 않는 한 캐시의 메모리 캐시 항목을 제거 하는 [우선 순위를 캐시](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.caching.memory.cacheitempriority) 로 설정 된 `CacheItemPriority.NeverRemove`합니다.</span><span class="sxs-lookup"><span data-stu-id="82886-120">The `IMemoryCache` cache will evict cache entries under memory pressure unless the [cache priority](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.caching.memory.cacheitempriority) is set to `CacheItemPriority.NeverRemove`.</span></span> <span data-ttu-id="82886-121">설정할 수 있습니다는 `CacheItemPriority` 캐시를 우선 순위를 조정 하려면 메모리가 중에서 항목을 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="82886-121">You can set the `CacheItemPriority` to adjust the priority the cache evicts items under memory pressure.</span></span>

<span data-ttu-id="82886-122">메모리 내 캐시는 모든 개체를 저장할 수 있습니다. 분산된 캐시 인터페이스는 제한 `byte[]`합니다.</span><span class="sxs-lookup"><span data-stu-id="82886-122">The in-memory cache can store any object; the distributed cache interface is limited to `byte[]`.</span></span>

## <a name="using-imemorycache"></a><span data-ttu-id="82886-123">IMemoryCache를 사용 하 여</span><span class="sxs-lookup"><span data-stu-id="82886-123">Using IMemoryCache</span></span>

<span data-ttu-id="82886-124">메모리 내 캐시는 *서비스* 사용 하 여 응용 프로그램에서 참조 되는 [종속성 주입](../../fundamentals/dependency-injection.md)합니다.</span><span class="sxs-lookup"><span data-stu-id="82886-124">In-memory caching is a *service* that is referenced from your app using [Dependency Injection](../../fundamentals/dependency-injection.md).</span></span> <span data-ttu-id="82886-125">호출 `AddMemoryCache` 에 `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="82886-125">Call `AddMemoryCache` in `ConfigureServices`:</span></span>

[!code-csharp[Main](memory/sample/WebCache/Startup.cs?highlight=8)] 

<span data-ttu-id="82886-126">요청 된 `IMemoryCache` 생성자에 대 한 인스턴스:</span><span class="sxs-lookup"><span data-stu-id="82886-126">Request the `IMemoryCache` instance in the constructor:</span></span>

[!code-csharp[Main](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_ctor&highlight=3,5-)] 

<span data-ttu-id="82886-127">`IMemoryCache`NuGet 패키지 "Microsoft.Extensions.Caching.Memory" 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="82886-127">`IMemoryCache` requires NuGet package "Microsoft.Extensions.Caching.Memory".</span></span>

<span data-ttu-id="82886-128">다음 코드에서는 [TryGetValue](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.caching.memory.imemorycache#Microsoft_Extensions_Caching_Memory_IMemoryCache_TryGetValue_System_Object_System_Object__) 을 현재 시간 캐시에 있는지 확인 하십시오.</span><span class="sxs-lookup"><span data-stu-id="82886-128">The following code uses [TryGetValue](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.caching.memory.imemorycache#Microsoft_Extensions_Caching_Memory_IMemoryCache_TryGetValue_System_Object_System_Object__) to check if the current time is in the cache.</span></span> <span data-ttu-id="82886-129">새 항목 만들어지고과 함께 캐시에 추가 된 항목, 캐시 되지 않으면 [설정](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.caching.memory.cacheextensions#Microsoft_Extensions_Caching_Memory_CacheExtensions_Set__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object___0_)합니다.</span><span class="sxs-lookup"><span data-stu-id="82886-129">If the item is not cached, a new entry is created and added to the cache with [Set](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.caching.memory.cacheextensions#Microsoft_Extensions_Caching_Memory_CacheExtensions_Set__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object___0_).</span></span>

[!code-csharp[Main](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet1)]

<span data-ttu-id="82886-130">캐시 된 시간과 현재 시간 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="82886-130">The current time and the cached time is displayed:</span></span>

[!code-html[Main](memory/sample/WebCache/Views/Home/Cache.cshtml)]

<span data-ttu-id="82886-131">캐시 된 `DateTime` 있을 때는 요청 제한 시간 (및 메모리 부족으로 인해 없는 제거) 내에서 값이 캐시에 유지 됩니다.</span><span class="sxs-lookup"><span data-stu-id="82886-131">The cached `DateTime` value will remain in the cache while there are requests within the timeout period (and no eviction due to memory pressure).</span></span> <span data-ttu-id="82886-132">다음 이미지는 현재 시간 및 캐시에서 검색 하는 이전 시간을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="82886-132">The image below shows the current time and an older time retrieved from cache:</span></span>

![인덱스 뷰를 표시 하는 두 개의 서로 다른 시간](memory/_static/time.png)

<span data-ttu-id="82886-134">다음 코드에서는 [GetOrCreate](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.caching.memory.cacheextensions#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreate__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry___0__) 및 [GetOrCreateAsync](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.caching.memory.cacheextensions#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreateAsync__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry_System_Threading_Tasks_Task___0___) 데이터를 캐시 합니다.</span><span class="sxs-lookup"><span data-stu-id="82886-134">The following code uses [GetOrCreate](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.caching.memory.cacheextensions#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreate__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry___0__) and [GetOrCreateAsync](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.caching.memory.cacheextensions#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreateAsync__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry_System_Threading_Tasks_Task___0___) to cache data.</span></span> 

[!code-csharp[Main](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet2&highlight=3-7,14-19)]

<span data-ttu-id="82886-135">다음 호출 코드 [가져오기](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.caching.memory.cacheextensions#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_) 를 캐시 된 시간을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="82886-135">The following code calls [Get](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.caching.memory.cacheextensions#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_) to fetch the cached time:</span></span>

[!code-csharp[Main](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_gct)]

<span data-ttu-id="82886-136">참조 [IMemoryCache 메서드](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.caching.memory.imemorycache) 및 [CacheExtensions 메서드](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.caching.memory.cacheextensions) 에 대 한 설명은 캐시 메서드.</span><span class="sxs-lookup"><span data-stu-id="82886-136">See [IMemoryCache methods](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.caching.memory.imemorycache) and [CacheExtensions methods](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.caching.memory.cacheextensions) for a description of the cache methods.</span></span>

## <a name="using-memorycacheentryoptions"></a><span data-ttu-id="82886-137">MemoryCacheEntryOptions를 사용 하 여</span><span class="sxs-lookup"><span data-stu-id="82886-137">Using MemoryCacheEntryOptions</span></span>

<span data-ttu-id="82886-138">다음 샘플:</span><span class="sxs-lookup"><span data-stu-id="82886-138">The following sample:</span></span>

- <span data-ttu-id="82886-139">절대 만료 시간을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="82886-139">Sets the absolute expiration time.</span></span> <span data-ttu-id="82886-140">이 항목을 캐시 될 수 있는 최대 시간 고 슬라이딩 만료 지속적으로 갱신 되 면 너무 부실 항목 방지 합니다.</span><span class="sxs-lookup"><span data-stu-id="82886-140">This is the maximum time the entry can be cached and prevents the item from becoming too stale when the sliding expiration is continuously renewed.</span></span>
- <span data-ttu-id="82886-141">슬라이딩 만료 시간을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="82886-141">Sets a sliding expiration time.</span></span> <span data-ttu-id="82886-142">이 캐시 된 항목에 액세스 하는 요청에는 상대 (sliding) 만료 시계가 다시 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="82886-142">Requests that access this cached item will reset the sliding expiration clock.</span></span>
- <span data-ttu-id="82886-143">캐시 우선 순위 설정 `CacheItemPriority.NeverRemove`합니다.</span><span class="sxs-lookup"><span data-stu-id="82886-143">Sets the cache priority to `CacheItemPriority.NeverRemove`.</span></span> 
- <span data-ttu-id="82886-144">집합은 [PostEvictionDelegate](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.caching.memory.postevictiondelegate) 하는 후에 호출 됩니다는 엔트리를 캐시에서 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="82886-144">Sets a [PostEvictionDelegate](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.caching.memory.postevictiondelegate) that will be called after the entry is evicted from the cache.</span></span> <span data-ttu-id="82886-145">콜백 캐시에서 항목을 제거 하는 코드에서 다른 스레드에서 실행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="82886-145">The callback is run on a different thread from the code that removes the item from the cache.</span></span>

[!code-csharp[Main](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_et&highlight=14-20)]

## <a name="cache-dependencies"></a><span data-ttu-id="82886-146">캐시 종속성</span><span class="sxs-lookup"><span data-stu-id="82886-146">Cache dependencies</span></span>

<span data-ttu-id="82886-147">다음 예제에는 종속 항목을 만료 하는 경우 캐시 항목을 만료 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="82886-147">The following sample shows how to expire a cache entry if a dependent entry expires.</span></span> <span data-ttu-id="82886-148">A `CancellationChangeToken` 캐시 된 항목에 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="82886-148">A `CancellationChangeToken` is added to the cached item.</span></span> <span data-ttu-id="82886-149">때 `Cancel` 라고 하는 `CancellationTokenSource`, 캐시 항목이 모두 제거 됩니다.</span><span class="sxs-lookup"><span data-stu-id="82886-149">When `Cancel` is called on the `CancellationTokenSource`, both cache entries are evicted.</span></span> 

[!code-csharp[Main](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_ed)]

<span data-ttu-id="82886-150">사용 하는 `CancellationTokenSource` 여러 캐시 항목이 그룹으로 제거할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="82886-150">Using a `CancellationTokenSource` allows multiple cache entries to be evicted as a group.</span></span> <span data-ttu-id="82886-151">와 `using` 내에 만들어진 위의 코드에서 패턴, 캐시 항목의 `using` 블록 트리거 및 만료 설정을 상속 합니다.</span><span class="sxs-lookup"><span data-stu-id="82886-151">With the `using` pattern in the code above, cache entries created inside the `using` block will inherit triggers and expiration settings.</span></span>

### <a name="additional-notes"></a><span data-ttu-id="82886-152">추가 참고 사항</span><span class="sxs-lookup"><span data-stu-id="82886-152">Additional notes</span></span>

- <span data-ttu-id="82886-153">콜백을 사용 하 여 캐시 항목을 다시 채우기:</span><span class="sxs-lookup"><span data-stu-id="82886-153">When using a callback to repopulate a cache item:</span></span>

  - <span data-ttu-id="82886-154">여러 개의 요청 찾을 수 있는 캐시 된 키 값 빈 콜백을 완료 되지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="82886-154">Multiple requests can find the cached key value empty because the callback hasn't completed.</span></span> 
  - <span data-ttu-id="82886-155">이 캐시 된 항목을 채우는 여러 스레드 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="82886-155">This can result in several threads repopulating the cached item.</span></span>

- <span data-ttu-id="82886-156">하나의 캐시 항목을 사용 하 여 다른 만들을 부모 항목의 만료 토큰 및 시간 기반 만료 설정을 자식 복사 합니다.</span><span class="sxs-lookup"><span data-stu-id="82886-156">When one cache entry is used to create another, the child copies the parent entry's expiration tokens and time-based expiration settings.</span></span> <span data-ttu-id="82886-157">자식 수동 제거 하 여 만료 된 또는 부모 항목의 업데이트 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="82886-157">The child is not expired by manual removal or updating of the parent entry.</span></span>

### <a name="other-resources"></a><span data-ttu-id="82886-158">기타 리소스</span><span class="sxs-lookup"><span data-stu-id="82886-158">Other Resources</span></span>

* [<span data-ttu-id="82886-159">분산 캐시 사용</span><span class="sxs-lookup"><span data-stu-id="82886-159">Working with a Distributed Cache</span></span>](distributed.md)
* [<span data-ttu-id="82886-160">응답 캐싱 미들웨어</span><span class="sxs-lookup"><span data-stu-id="82886-160">Response caching middleware</span></span>](middleware.md)
