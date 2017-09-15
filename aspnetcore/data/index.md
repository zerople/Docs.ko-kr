---
title: "ASP.NET Core에서 데이터 작업"
author: rick-anderson
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: a8fb7eb7-e0e5-4394-84f3-1f1dbe0ba2ef
ms.technology: aspnet
ms.prod: asp.net-core
ms.openlocfilehash: 3566127476289ae085a9161132b103638bc9b068
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/11/2017
---
# <a name="working-with-data-in-aspnet-core"></a><span data-ttu-id="6679f-103">ASP.NET Core에서 데이터 작업</span><span class="sxs-lookup"><span data-stu-id="6679f-103">Working with Data in ASP.NET Core</span></span> 

*   [<span data-ttu-id="6679f-104">Visual Studio를 사용하여 ASP.NET Core 및 Entity Framework Core 시작</span><span class="sxs-lookup"><span data-stu-id="6679f-104">Getting started with ASP.NET Core and Entity Framework Core using Visual Studio</span></span>](ef-mvc/index.md)
    *   [<span data-ttu-id="6679f-105">시작</span><span class="sxs-lookup"><span data-stu-id="6679f-105">Getting started</span></span>](ef-mvc/intro.md)
    *   [<span data-ttu-id="6679f-106">만들기, 읽기, 업데이트 및 삭제 작업</span><span class="sxs-lookup"><span data-stu-id="6679f-106">Create, Read, Update, and Delete operations</span></span>](ef-mvc/crud.md)
    *   [<span data-ttu-id="6679f-107">정렬, 필터링, 페이징 및 그룹화</span><span class="sxs-lookup"><span data-stu-id="6679f-107">Sorting, filtering, paging, and grouping</span></span>](ef-mvc/sort-filter-page.md)
    *   [<span data-ttu-id="6679f-108">마이그레이션</span><span class="sxs-lookup"><span data-stu-id="6679f-108">Migrations</span></span>](ef-mvc/migrations.md)
    *   [<span data-ttu-id="6679f-109">복합 데이터 모델 만들기</span><span class="sxs-lookup"><span data-stu-id="6679f-109">Creating a complex data model</span></span>](ef-mvc/complex-data-model.md)
    *   [<span data-ttu-id="6679f-110">관련 데이터 읽기</span><span class="sxs-lookup"><span data-stu-id="6679f-110">Reading related data</span></span>](ef-mvc/read-related-data.md)
    *   [<span data-ttu-id="6679f-111">관련 데이터 업데이트</span><span class="sxs-lookup"><span data-stu-id="6679f-111">Updating related data</span></span>](ef-mvc/update-related-data.md)
    *   [<span data-ttu-id="6679f-112">동시성 충돌 처리</span><span class="sxs-lookup"><span data-stu-id="6679f-112">Handling concurrency conflicts</span></span>](ef-mvc/concurrency.md)
    *   [<span data-ttu-id="6679f-113">상속</span><span class="sxs-lookup"><span data-stu-id="6679f-113">Inheritance</span></span>](ef-mvc/inheritance.md)
    *   [<span data-ttu-id="6679f-114">고급 항목</span><span class="sxs-lookup"><span data-stu-id="6679f-114">Advanced topics</span></span>](ef-mvc/advanced.md)
* <span data-ttu-id="6679f-115">[ASP.NET Core 및 EF Core - 새로운 데이터베이스](https://docs.microsoft.com/ef/core/get-started/aspnetcore/new-db)(Entity Framework Core 설명서 사이트)</span><span class="sxs-lookup"><span data-stu-id="6679f-115">[ASP.NET Core with EF Core - new database](https://docs.microsoft.com/ef/core/get-started/aspnetcore/new-db) (Entity Framework Core documentation site)</span></span>
* <span data-ttu-id="6679f-116">[ASP.NET Core 및 EF Core - 기존 데이터베이스](https://docs.microsoft.com/ef/core/get-started/aspnetcore/existing-db)(Entity Framework Core 설명서 사이트)</span><span class="sxs-lookup"><span data-stu-id="6679f-116">[ASP.NET Core with EF Core - existing database](https://docs.microsoft.com/ef/core/get-started/aspnetcore/existing-db) (Entity Framework Core documentation site)</span></span>
*   [<span data-ttu-id="6679f-117">ASP.NET Core 및 Entity Framework 6 시작</span><span class="sxs-lookup"><span data-stu-id="6679f-117">Getting Started with ASP.NET Core and Entity Framework 6</span></span>](entity-framework-6.md)
*   [<span data-ttu-id="6679f-118">Azure Storage</span><span class="sxs-lookup"><span data-stu-id="6679f-118">Azure Storage</span></span>](azure-storage/index.md)
    *   [<span data-ttu-id="6679f-119">Visual Studio 연결된 서비스를 사용하여 Azure Storage 추가</span><span class="sxs-lookup"><span data-stu-id="6679f-119">Adding Azure Storage by Using Visual Studio Connected Services</span></span>](https://azure.microsoft.com/documentation/articles/vs-azure-tools-connected-services-storage/)
    *   [<span data-ttu-id="6679f-120">Azure Blob Storage 및 Visual Studio 연결된 서비스 시작</span><span class="sxs-lookup"><span data-stu-id="6679f-120">Get Started with Azure Blob storage and Visual Studio Connected Services</span></span>](https://azure.microsoft.com/documentation/articles/vs-storage-aspnet5-getting-started-blobs/)
    *   [<span data-ttu-id="6679f-121">Queue Storage 및 Visual Studio 연결된 서비스 시작</span><span class="sxs-lookup"><span data-stu-id="6679f-121">Get Started with Queue Storage and Visual Studio Connected Services</span></span>](https://azure.microsoft.com/documentation/articles/vs-storage-aspnet5-getting-started-queues/)
    *   [<span data-ttu-id="6679f-122">Azure Table Storage 및 Visual Studio 연결된 서비스를 시작하는 방법</span><span class="sxs-lookup"><span data-stu-id="6679f-122">How to Get Started with Azure Table Storage and Visual Studio Connected Services</span></span>](https://azure.microsoft.com/documentation/articles/vs-storage-aspnet5-getting-started-tables/)
