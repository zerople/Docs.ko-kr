---
title: "기본 제공 ASP.NET Core 태그 도우미"
author: pkellner
description: "기본 제공 ASP.NET Core 태그 도우미"
keywords: "ASP.NET Core, 태그 도우미"
ms.author: riande
manager: wpickett
ms.date: 09/13/2017
ms.topic: article
ms.technology: aspnet
ms.prod: aspnet-core
uid: mvc/views/tag-helpers/builtin-th/Index
ms.openlocfilehash: e7c8c64283ca3740698300689b10497f984cfd3e
ms.sourcegitcommit: d022d4b96795ee473fa3847a1d8a8c7430423a86
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2017
---
# <a name="aspnet-core-built-in-tag-helpers"></a><span data-ttu-id="7bb09-104">기본 제공 ASP.NET Core 태그 도우미</span><span class="sxs-lookup"><span data-stu-id="7bb09-104">ASP.NET Core built-in Tag Helpers</span></span>

<span data-ttu-id="7bb09-105">작성자: [Peter Kellner](http://peterkellner.net)</span><span class="sxs-lookup"><span data-stu-id="7bb09-105">By [Peter Kellner](http://peterkellner.net)</span></span> 

<span data-ttu-id="7bb09-106">ASP.NET Core에는 생산성을 향상하기 위해 다양한 기본 제공 태그 도우미가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bb09-106">ASP.NET Core includes many built-in Tag Helpers to boost your productivity.</span></span> <span data-ttu-id="7bb09-107">이 섹션에서는 기본 제공 태그 도우미의 개요를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7bb09-107">This section provides an overview of the built-in Tag Helpers.</span></span>

> [!NOTE]
> <span data-ttu-id="7bb09-108">[Razor](xref:mvc/views/razor) 뷰 엔진에서 내부적으로 사용하기 때문에 다루지 않는 기본 제공 태그 도우미도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bb09-108">There are built-in Tag Helpers which aren't discussed, since they're used internally by the [Razor](xref:mvc/views/razor) view engine.</span></span> <span data-ttu-id="7bb09-109">여기에는 웹 사이트의 루트 경로로 확장되는 ~ 문자의 태그 도우미가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bb09-109">This includes a Tag Helper for the ~ character, which expands to the root path of the website.</span></span>

## <a name="built-in-aspnet-core-tag-helpers"></a><span data-ttu-id="7bb09-110">기본 제공 ASP.NET Core 태그 도우미</span><span class="sxs-lookup"><span data-stu-id="7bb09-110">Built-in ASP.NET Core Tag Helpers</span></span>

<span data-ttu-id="7bb09-111">**[앵커 태그 도우미](xref:mvc/views/tag-helpers/builtin-th/AnchorTagHelper)**</span><span class="sxs-lookup"><span data-stu-id="7bb09-111">**[Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/AnchorTagHelper)**</span></span>

<span data-ttu-id="7bb09-112">**[캐시 태그 도우미](xref:mvc/views/tag-helpers/builtin-th/CacheTagHelper)**</span><span class="sxs-lookup"><span data-stu-id="7bb09-112">**[Cache Tag Helper](xref:mvc/views/tag-helpers/builtin-th/CacheTagHelper)**</span></span>

<span data-ttu-id="7bb09-113">**[분산 캐시 태그 도우미](xref:mvc/views/tag-helpers/builtin-th/DistributedCacheTagHelper)**</span><span class="sxs-lookup"><span data-stu-id="7bb09-113">**[Distributed Cache Tag Helper](xref:mvc/views/tag-helpers/builtin-th/DistributedCacheTagHelper)**</span></span>

<span data-ttu-id="7bb09-114">**[환경 태그 도우미](xref:mvc/views/tag-helpers/builtin-th/EnvironmentTagHelper)**</span><span class="sxs-lookup"><span data-stu-id="7bb09-114">**[Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/EnvironmentTagHelper)**</span></span>

[comment]: **[FormActionTagHelper](xref:mvc/views/tag-helpers/builtin-th/FormActionTagHelper)**

<span data-ttu-id="7bb09-115">**[폼 태그 도우미](xref:mvc/views/working-with-forms#the-form-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="7bb09-115">**[Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper)**</span></span>

<span data-ttu-id="7bb09-116">**[이미지 태그 도우미](xref:mvc/views/tag-helpers/builtin-th/ImageTagHelper)**</span><span class="sxs-lookup"><span data-stu-id="7bb09-116">**[Image Tag Helper](xref:mvc/views/tag-helpers/builtin-th/ImageTagHelper)**</span></span>

<span data-ttu-id="7bb09-117">**[입력 태그 도우미](xref:mvc/views/working-with-forms#the-input-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="7bb09-117">**[Input Tag Helper](xref:mvc/views/working-with-forms#the-input-tag-helper)**</span></span>

<span data-ttu-id="7bb09-118">**[레이블 태그 도우미](xref:mvc/views/working-with-forms#the-label-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="7bb09-118">**[Label Tag Helper](xref:mvc/views/working-with-forms#the-label-tag-helper)**</span></span>

[comment]: **[LinkTagHelper](xref:mvc/views/tag-helpers/builtin-th/LinkTagHelper)**

[comment]: **[OptionTagHelper](xref:mvc/views/tag-helpers/builtin-th/OptionTagHelper)**

[comment]: **[ScriptTagHelper](xref:mvc/views/tag-helpers/builtin-th/ScriptTagTagHelper)**

<span data-ttu-id="7bb09-119">**[선택 태그 도우미](xref:mvc/views/working-with-forms#the-select-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="7bb09-119">**[Select Tag Helper](xref:mvc/views/working-with-forms#the-select-tag-helper)**</span></span>

<span data-ttu-id="7bb09-120">**[텍스트 영역 태그 도우미](xref:mvc/views/working-with-forms#the-textarea-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="7bb09-120">**[Textarea Tag Helper](xref:mvc/views/working-with-forms#the-textarea-tag-helper)**</span></span>

<span data-ttu-id="7bb09-121">**[유효성 검사 메시지 태그 도우미](xref:mvc/views/working-with-forms#the-validation-message-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="7bb09-121">**[Validation Message Tag Helper](xref:mvc/views/working-with-forms#the-validation-message-tag-helper)**</span></span>

<span data-ttu-id="7bb09-122">**[유효성 검사 요약 태그 도우미](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="7bb09-122">**[Validation Summary Tag Helper](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper)**</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7bb09-123">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="7bb09-123">Additional resources</span></span>

* [<span data-ttu-id="7bb09-124">클라이언트 쪽 개발</span><span class="sxs-lookup"><span data-stu-id="7bb09-124">Client-Side Development</span></span>](xref:client-side/index)
* [<span data-ttu-id="7bb09-125">태그 도우미</span><span class="sxs-lookup"><span data-stu-id="7bb09-125">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
