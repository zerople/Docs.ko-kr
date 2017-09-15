---
title: "기본 제공 ASP.NET Core 태그 도우미"
author: pkellner
description: "기본 제공 ASP.NET Core 태그 도우미"
keywords: "ASP.NET Core, 태그 도우미"
ms.author: riande
manager: wpickett
ms.date: 7/11/2017
ms.topic: article
ms.technology: aspnet
ms.prod: aspnet-core
uid: mvc/views/tag-helpers/builtin-th/Index
ms.openlocfilehash: 3f47cc571eff0c522aaf6543de58f158835384d4
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/11/2017
---
# <a name="aspnet-core-built-in-tag-helpers"></a><span data-ttu-id="11c09-104">기본 제공 ASP.NET Core 태그 도우미</span><span class="sxs-lookup"><span data-stu-id="11c09-104">ASP.NET Core built-in Tag Helpers</span></span>

<span data-ttu-id="11c09-105">작성자: [Peter Kellner](http://peterkellner.net)</span><span class="sxs-lookup"><span data-stu-id="11c09-105">By [Peter Kellner](http://peterkellner.net)</span></span> 

<span data-ttu-id="11c09-106">ASP.NET Core 프레임워크에는 생산적으로 강력한 코드를 작성할 수 있는 많은 태그 도우미가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="11c09-106">The ASP.NET Core framework includes many Tag Helpers that can help you be more productive in writing robust code.</span></span> <span data-ttu-id="11c09-107">이 섹션에서는 모든 기본 제공 태그 도우미의 개요를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="11c09-107">This section provides an overview of all the built-in Tag Helpers.</span></span>

> [!NOTE]
> <span data-ttu-id="11c09-108">[Razor](xref:mvc/views/razor) 뷰 엔진에서 내부적으로 사용하기 때문에 다루지 않는 기본 제공 태그 도우미도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="11c09-108">There are built-in Tag Helpers which are not discussed, since they are used internally by the [Razor](xref:mvc/views/razor) view engine.</span></span> <span data-ttu-id="11c09-109">여기에는 웹 사이트의 루트 경로로 확장되는 ~ 문자의 태그 도우미가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="11c09-109">This includes a Tag Helper for the ~ character which expands to the root path of the web site.</span></span>

## <a name="built-in-aspnet-core-tag-helpers"></a><span data-ttu-id="11c09-110">기본 제공 ASP.NET Core 태그 도우미</span><span class="sxs-lookup"><span data-stu-id="11c09-110">Built-in ASP.NET Core Tag Helpers</span></span>

<span data-ttu-id="11c09-111">**[앵커 태그 도우미](xref:mvc/views/tag-helpers/builtin-th/AnchorTagHelper)**</span><span class="sxs-lookup"><span data-stu-id="11c09-111">**[Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/AnchorTagHelper)**</span></span>

<span data-ttu-id="11c09-112">**[캐시 태그 도우미](xref:mvc/views/tag-helpers/builtin-th/CacheTagHelper)**</span><span class="sxs-lookup"><span data-stu-id="11c09-112">**[Cache Tag Helper](xref:mvc/views/tag-helpers/builtin-th/CacheTagHelper)**</span></span>

<span data-ttu-id="11c09-113">**[분산 캐시 태그 도우미](xref:mvc/views/tag-helpers/builtin-th/DistributedCacheTagHelper)**</span><span class="sxs-lookup"><span data-stu-id="11c09-113">**[Distributed Cache Tag Helper](xref:mvc/views/tag-helpers/builtin-th/DistributedCacheTagHelper)**</span></span>

<span data-ttu-id="11c09-114">**[환경 태그 도우미](xref:mvc/views/tag-helpers/builtin-th/EnvironmentTagHelper)**</span><span class="sxs-lookup"><span data-stu-id="11c09-114">**[Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/EnvironmentTagHelper)**</span></span>

[comment]: **[FormActionTagHelper](xref:mvc/views/tag-helpers/builtin-th/FormActionTagHelper)**

[comment]: **[FormTagTagHelper](xref:mvc/views/tag-helpers/builtin-th/FormTagHelper)**

<span data-ttu-id="11c09-115">**[이미지 태그 도우미](xref:mvc/views/tag-helpers/builtin-th/ImageTagHelper)**</span><span class="sxs-lookup"><span data-stu-id="11c09-115">**[Image Tag Helper](xref:mvc/views/tag-helpers/builtin-th/ImageTagHelper)**</span></span>

[comment]: **[InputTagHelper](xref:mvc/views/tag-helpers/builtin-th/InputTagHelper)**

[comment]: **[LabelTagHelper](xref:mvc/views/tag-helpers/builtin-th/LabelTagHelper)**

[comment]: **[LinkTagHelper](xref:mvc/views/tag-helpers/builtin-th/LinkTagHelper)**

[comment]: **[OptionTagHelper](xref:mvc/views/tag-helpers/builtin-th/OptionTagHelper)**

[comment]: **[ScriptTagHelper](xref:mvc/views/tag-helpers/builtin-th/ScriptTagTagHelper)**

[comment]: **[SelectTagHelper](xref:mvc/views/tag-helpers/builtin-th/SelectTagTagHelper)**

[comment]: **[TextAreaTagHelper](xref:mvc/views/tag-helpers/builtin-th/TextAreaTagHelper)**

[comment]: **[ValidationMessageTagHelper](xref:mvc/views/tag-helpers/builtin-th/ValidationMessageTagHelper)**

[comment]: **[ValidationSummaryTagHelper](xref:mvc/views/tag-helpers/builtin-th/ValidationSummaryTagHelper)**  
  
  
<!--

## Additional Resources

REQUIRED These must be xref links, not relative, that is ../../
* [Client-Side Development](../../../client-side/index.md)

* [Tag Helpers](xref:mvc/views/tag-helpers/intro)
-->
