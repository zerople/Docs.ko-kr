---
title: "ASP.NET Core 1.1의 새로운 기능"
author: rick-anderson
description: "ASP.NET Core 1.1의 새로운 기능"
keywords: ASP.NET Core, bower
ms.author: riande
manager: wpickett
ms.date: 02/14/2017
ms.topic: article
ms.assetid: 062f8353-d1bc-4e99-a821-c1d1bb162c47
ms.technology: aspnet
ms.prod: aspnet-core
uid: aspnetcore-1.1
ms.openlocfilehash: 7fdb00bc64cb20bd0e658b3a81814059404476d2
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/11/2017
---
# <a name="whats-new-in-aspnet-core-11"></a><span data-ttu-id="c7b4c-104">ASP.NET Core 1.1의 새로운 기능</span><span class="sxs-lookup"><span data-stu-id="c7b4c-104">What's new in ASP.NET Core 1.1</span></span>

<span data-ttu-id="c7b4c-105">ASP.NET Core 1.1에는 다음과 같은 새로운 기능이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="c7b4c-105">ASP.NET Core 1.1 includes the following new features:</span></span>

- [<span data-ttu-id="c7b4c-106">URL 재작성 미들웨어</span><span class="sxs-lookup"><span data-stu-id="c7b4c-106">URL Rewriting Middleware</span></span>](https://docs.microsoft.com/aspnet/core/fundamentals/url-rewriting)
- [<span data-ttu-id="c7b4c-107">응답 캐싱 미들웨어</span><span class="sxs-lookup"><span data-stu-id="c7b4c-107">Response Caching Middleware</span></span>](https://docs.microsoft.com/aspnet/core/performance/caching/middleware)
- [<span data-ttu-id="c7b4c-108">태그 도우미인 구성 요소 보기</span><span class="sxs-lookup"><span data-stu-id="c7b4c-108">View Components as Tag Helpers</span></span>](xref:mvc/views/view-components#invoking-a-view-component-as-a-tag-helper)
- [<span data-ttu-id="c7b4c-109">MVC 필터인 미들웨어</span><span class="sxs-lookup"><span data-stu-id="c7b4c-109">Middleware as MVC filters</span></span>](xref:mvc/controllers/filters#using-middleware-in-the-filter-pipeline)
- [<span data-ttu-id="c7b4c-110">쿠키 기반 TempData 공급자</span><span class="sxs-lookup"><span data-stu-id="c7b4c-110">Cookie-based TempData provider</span></span>](xref:fundamentals/app-state#cookie-based-tempdata-provider )
- [<span data-ttu-id="c7b4c-111">Azure App Service 로깅 공급자</span><span class="sxs-lookup"><span data-stu-id="c7b4c-111">Azure App Service logging provider</span></span>](xref:fundamentals/logging#appservice)
- [<span data-ttu-id="c7b4c-112">Azure Key Vault 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="c7b4c-112">Azure Key Vault configuration provider</span></span>](xref:security/key-vault-configuration)
- [<span data-ttu-id="c7b4c-113">Azure 및 Redis 저장소 데이터 보호 키 리포지토리</span><span class="sxs-lookup"><span data-stu-id="c7b4c-113">Azure and Redis Storage Data Protection Key Repositories</span></span>](xref:security/data-protection/implementation/key-storage-providers#azure-and-redis)
- [<span data-ttu-id="c7b4c-114">Windows용 WebListener 서버</span><span class="sxs-lookup"><span data-stu-id="c7b4c-114">WebListener Server for Windows</span></span>](xref:fundamentals/servers/weblistener)
- [<span data-ttu-id="c7b4c-115">WebSockets 지원</span><span class="sxs-lookup"><span data-stu-id="c7b4c-115">WebSockets support</span></span>](xref:fundamentals/websockets)

## <a name="choosing-between-versions-10-and-11-of-aspnet-core"></a><span data-ttu-id="c7b4c-116">ASP.NET Core 버전 1.0과 1.1 중에서 선택</span><span class="sxs-lookup"><span data-stu-id="c7b4c-116">Choosing between versions 1.0 and 1.1 of ASP.NET Core</span></span>

<span data-ttu-id="c7b4c-117">ASP.NET Core 1.1에는 1.0보다 더 많은 기능이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c7b4c-117">ASP.NET Core 1.1 has more features than 1.0.</span></span> <span data-ttu-id="c7b4c-118">일반적으로 최신 버전을 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="c7b4c-118">In general, we recommend you use the latest version.</span></span>

## <a name="additional-information"></a><span data-ttu-id="c7b4c-119">추가 정보</span><span class="sxs-lookup"><span data-stu-id="c7b4c-119">Additional Information</span></span>

- [<span data-ttu-id="c7b4c-120">ASP.NET Core 1.1.0 릴리스 정보</span><span class="sxs-lookup"><span data-stu-id="c7b4c-120">ASP.NET Core 1.1.0 Release Notes</span></span>](https://github.com/aspnet/Home/releases/tag/1.1.0)
- <span data-ttu-id="c7b4c-121">ASP.NET Core 개발 팀의 진행 상황 및 계획과 연결하려면 매주 [ASP.NET 커뮤니티 스탠드업](https://live.asp.net/)을 시청하세요.</span><span class="sxs-lookup"><span data-stu-id="c7b4c-121">If you’d like to connect with the ASP.NET Core development team’s progress and plans, tune in to the weekly [ASP.NET Community Standup](https://live.asp.net/).</span></span>
