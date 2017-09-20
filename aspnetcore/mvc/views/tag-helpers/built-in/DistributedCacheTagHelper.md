---
title: "분산 캐시 태그 도우미 | Microsoft Docs"
author: pkellner
description: "캐시 태그 도우미를 사용 하는 방법을 보여 줍니다."
keywords: "ASP.NET Core, 태그 도우미"
ms.author: riande
manager: wpickett
ms.date: 02/14/2017
ms.topic: article
ms.assetid: c045d485-d1dc-4cea-a675-46be83b7a022
ms.technology: aspnet
ms.prod: aspnet-core
uid: mvc/views/tag-helpers/builtin-th/DistributedCacheTagHelper
ms.openlocfilehash: 2b260624fb2d85ab1a2625511397bcb4a85b6e77
ms.sourcegitcommit: 74a8ad9c1ba5c155d7c4303e67632a0922c38e86
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/20/2017
---
# <a name="distributed-cache-tag-helper"></a><span data-ttu-id="89c93-104">분산된 캐시 태그 도우미</span><span class="sxs-lookup"><span data-stu-id="89c93-104">Distributed Cache Tag Helper</span></span>

<span data-ttu-id="89c93-105">작성자: [Peter Kellner](http://peterkellner.net)</span><span class="sxs-lookup"><span data-stu-id="89c93-105">By [Peter Kellner](http://peterkellner.net)</span></span> 


<span data-ttu-id="89c93-106">분산 캐시 태그 도우미 분산된 캐시 원본에 해당 콘텐츠를 캐시 하 여 ASP.NET Core 응용 프로그램의 성능이 크게 향상 하는 기능을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="89c93-106">The Distributed Cache Tag Helper provides the ability to dramatically improve the performance of your ASP.NET Core app by caching its content to a distributed cache source.</span></span>

<span data-ttu-id="89c93-107">분산 캐시 태그 도우미 캐시 태그 도우미와 같은 기본 클래스에서 상속합니다.</span><span class="sxs-lookup"><span data-stu-id="89c93-107">The Distributed Cache Tag Helper inherits from the same base class as the Cache Tag Helper.</span></span>  <span data-ttu-id="89c93-108">캐시 태그 도우미에 연결 된 모든 특성은 Distributed 태그 도우미 에서도 작동 합니다.</span><span class="sxs-lookup"><span data-stu-id="89c93-108">All attributes associated with the Cache Tag Helper will also work on the Distributed Tag Helper.</span></span>


<span data-ttu-id="89c93-109">분산 캐시 태그 도우미 뒤에 오는 **명시적 종속성 원칙** 라고 **생성자 삽입**합니다.</span><span class="sxs-lookup"><span data-stu-id="89c93-109">The Distributed Cache Tag Helper follows the **Explicit Dependencies Principle** known as **Constructor Injection**.</span></span>  <span data-ttu-id="89c93-110">특히,는 `IDistributedCache` 인터페이스 컨테이너 분산 캐시 태그 도우미의 생성자에 전달 됩니다.</span><span class="sxs-lookup"><span data-stu-id="89c93-110">Specifically, the `IDistributedCache` interface container is passed into the Distributed Cache Tag Helper's constructor.</span></span>  <span data-ttu-id="89c93-111">구체적 구현 없는 경우 `IDistributedCache` 에 생성 되었음을 `ConfigureServices`startup.cs에서 일반적으로 발견 차례로 분산 캐시 태그 도우미 기본 캐시 태그 도우미와 캐시 된 데이터를 저장 하기 위한 동일한 메모리 내 공급자를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="89c93-111">If no specific concrete implementation of `IDistributedCache` has been created in `ConfigureServices`, usually found in startup.cs, then the Distributed Cache Tag Helper will use the same in-memory provider for storing cached data as the basic Cache Tag Helper.</span></span>

## <a name="distributed-cache-tag-helper-attributes"></a><span data-ttu-id="89c93-112">분산 캐시 태그 도우미 특성</span><span class="sxs-lookup"><span data-stu-id="89c93-112">Distributed Cache Tag Helper Attributes</span></span>

- - -

### <a name="enabled-expires-on-expires-after-expires-sliding-vary-by-header-vary-by-query-vary-by-route-vary-by-cookie-vary-by-user-vary-by-priority"></a><span data-ttu-id="89c93-113">만료 된 이후에 만료 만료-슬라이딩 활성화 vary 헤더 다 쿼리 다 경로 다 쿠키 다 사용자 다 by 우선 순위</span><span class="sxs-lookup"><span data-stu-id="89c93-113">enabled expires-on expires-after expires-sliding vary-by-header vary-by-query vary-by-route vary-by-cookie vary-by-user vary-by priority</span></span>

<span data-ttu-id="89c93-114">정의 대 한 캐시 태그 도우미를 참조 하십시오.</span><span class="sxs-lookup"><span data-stu-id="89c93-114">See Cache Tag Helper for definitions.</span></span> <span data-ttu-id="89c93-115">분산된 캐시 태그 도우미 있으므로 이러한 모든 특성은 캐시 태그 도우미에서 일반적인 캐시 태그 도우미와 같은 클래스에서 상속 합니다.</span><span class="sxs-lookup"><span data-stu-id="89c93-115">Distributed Cache Tag Helper inherits from the same class as Cache Tag Helper so all these attributes are common from Cache Tag Helper.</span></span>

- - -

### <a name="name-required"></a><span data-ttu-id="89c93-116">이름 (필수)</span><span class="sxs-lookup"><span data-stu-id="89c93-116">name (required)</span></span>

| <span data-ttu-id="89c93-117">특성 유형</span><span class="sxs-lookup"><span data-stu-id="89c93-117">Attribute Type</span></span>    | <span data-ttu-id="89c93-118">예제 값</span><span class="sxs-lookup"><span data-stu-id="89c93-118">Example Value</span></span>     |
|----------------   |----------------   |
| <span data-ttu-id="89c93-119">string</span><span class="sxs-lookup"><span data-stu-id="89c93-119">string</span></span>    | <span data-ttu-id="89c93-120">"my-distributed-cache-unique-key-101"</span><span class="sxs-lookup"><span data-stu-id="89c93-120">"my-distributed-cache-unique-key-101"</span></span>     |

<span data-ttu-id="89c93-121">필요한 `name` 특성은 분산 캐시 태그 도우미의 각 인스턴스에 대해 저장 된 캐시에 키로 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="89c93-121">The required `name` attribute is used as a key to that cache stored for each instance of a Distributed Cache Tag Helper.</span></span>  <span data-ttu-id="89c93-122">기본 캐시 태그 도우미 Razor 페이지 이름 및 razor 페이지의 태그 도우미의 위치에 따라 각 캐시 태그 도우미 인스턴스 키를 할당 하는와 달리 Distributed 캐시 태그 도우미만 기반 키 특성`name`</span><span class="sxs-lookup"><span data-stu-id="89c93-122">Unlike the basic Cache Tag Helper that assigns a key to each Cache Tag Helper instance based on the Razor page name and location of the tag helper in the razor page, the Distributed Cache Tag Helper only bases it's key on the attribute `name`</span></span>

<span data-ttu-id="89c93-123">사용 예제를 보려면:</span><span class="sxs-lookup"><span data-stu-id="89c93-123">Usage Example:</span></span>

```cshtml
<distributed-cache name="my-distributed-cache-unique-key-101">
    Time Inside Cache Tag Helper: @DateTime.Now
</distributed-cache>
```

## <a name="distributed-cache-tag-helper-idistributedcache-implementations"></a><span data-ttu-id="89c93-124">분산 캐시 태그 도우미 IDistributedCache 구현</span><span class="sxs-lookup"><span data-stu-id="89c93-124">Distributed Cache Tag Helper IDistributedCache Implementations</span></span>

<span data-ttu-id="89c93-125">두 개의 구현이 없는 `IDistributedCache` ASP.NET Core에서 기본적으로 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="89c93-125">There are two implementations of `IDistributedCache` built in to ASP.NET Core.</span></span>  <span data-ttu-id="89c93-126">에 따라 하나 **Sql Server** 기반 다른 **Redis**합니다.</span><span class="sxs-lookup"><span data-stu-id="89c93-126">One is based on **Sql Server** and the other is based on **Redis**.</span></span> <span data-ttu-id="89c93-127">명명 된 "작업 분산된 캐시" 아래에 참조 된 리소스의이 구현은 세부 정보를 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="89c93-127">Details of these implementations can be found at the resource referenced below named "Working with a distributed cache".</span></span> <span data-ttu-id="89c93-128">두 구현 모두의 인스턴스를 설정할 포함 `IDistributedCache` 에서 ASP.NET Core **startup.cs**합니다.</span><span class="sxs-lookup"><span data-stu-id="89c93-128">Both implementations involve setting an instance of `IDistributedCache` in ASP.NET Core's **startup.cs**.</span></span>

<span data-ttu-id="89c93-129">특정 구현을 사용과 특별히 관련 된 태그 특성이 없으면 `IDistributedCache`합니다.</span><span class="sxs-lookup"><span data-stu-id="89c93-129">There are no tag attributes specifically associated with using any specific implementation of `IDistributedCache`.</span></span>



- - -



## <a name="additional-resources"></a><span data-ttu-id="89c93-130">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="89c93-130">Additional resources</span></span>

* <xref:mvc/views/tag-helpers/builtin-th/CacheTagHelper>
* <xref:fundamentals/dependency-injection#service-lifetimes-and-registration-options>
* <xref:performance/caching/distributed>
* <xref:performance/caching/memory>
* <xref:security/authentication/identity>
