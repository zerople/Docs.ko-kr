---
title: "Razor 뷰 컴파일 및 미리 컴파일"
author: rick-anderson
description: "MVC Razor 뷰 컴파일 및 미리 컴파일 ASP.NET Core 응용 프로그램에서 사용할 수 있도록 하는 방법을 설명 하는 참조 문서입니다."
keywords: "ASP.NET Core, Razor 컴파일, Razor 전 컴파일, Razor 미리 보기"
ms.author: riande
manager: wpickett
ms.date: 08/16/2017
ms.topic: article
ms.assetid: ab4705b7-1638-1638-bc97-ea7f292fe92a
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/views/view-compilation
ms.openlocfilehash: bfee2e5e8f71c99465be79589a77f0e173097b23
ms.sourcegitcommit: 8005eb4051e568d88ee58d48424f39916052e6e2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2017
---
# <a name="razor-view-compilation-and-precompilation-in-aspnet-core"></a><span data-ttu-id="ff8e6-104">Razor 뷰 컴파일 및 ASP.NET 코어에서 미리 컴파일</span><span class="sxs-lookup"><span data-stu-id="ff8e6-104">Razor view compilation and precompilation in ASP.NET Core</span></span>

<span data-ttu-id="ff8e6-105">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="ff8e6-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="ff8e6-106">Razor 뷰는 뷰를 호출 하면 런타임에 컴파일됩니다.</span><span class="sxs-lookup"><span data-stu-id="ff8e6-106">Razor views are compiled at runtime when the view is invoked.</span></span> <span data-ttu-id="ff8e6-107">ASP.NET 1.1.0 핵심 및 높을수록 수 필요에 따라 컴파일 Razor 뷰 및 응용 프로그램와 함께 배포할 &mdash; 프로세스 미리 컴파일 라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="ff8e6-107">ASP.NET Core 1.1.0 and higher can optionally compile Razor views and deploy them with the app &mdash; a process known as precompilation.</span></span> <span data-ttu-id="ff8e6-108">ASP.NET Core 2.x 프로젝트 템플릿 기본적으로 미리 컴파일을 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="ff8e6-108">The ASP.NET Core 2.x project templates enable precompilation by default.</span></span>

> [!NOTE]
> <span data-ttu-id="ff8e6-109">Razor 뷰 미리 컴파일 현재 사용할 수 없는 수행 하는 경우는 [자체 포함된 배포 (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) ASP.NET 코어 2.0에서.</span><span class="sxs-lookup"><span data-stu-id="ff8e6-109">Razor view precompilation is currently unavailable when performing a [self-contained deployment (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) in ASP.NET Core 2.0.</span></span> <span data-ttu-id="ff8e6-110">2.1를 놓을 때 기능 Scd에 대 한 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ff8e6-110">The feature will be available for SCDs when 2.1 releases.</span></span> <span data-ttu-id="ff8e6-111">자세한 내용은 참조 [Windows에서 Linux에 대 한 크로스 컴파일을 하는 보기 컴파일에 실패](https://github.com/aspnet/MvcPrecompilation/issues/102)합니다.</span><span class="sxs-lookup"><span data-stu-id="ff8e6-111">For more information, see [View compilation fails when cross-compiling for Linux on Windows](https://github.com/aspnet/MvcPrecompilation/issues/102).</span></span>

<span data-ttu-id="ff8e6-112">미리 컴파일 고려 사항:</span><span class="sxs-lookup"><span data-stu-id="ff8e6-112">Precompilation considerations:</span></span>

* <span data-ttu-id="ff8e6-113">뷰 미리 컴파일 보다 작은 게시 된 번들 및 더 빠른 시작 시간에 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="ff8e6-113">Precompiling views results in a smaller published bundle and faster startup time.</span></span>
* <span data-ttu-id="ff8e6-114">뷰를 미리 컴파일한 후에 Razor 파일을 편집할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="ff8e6-114">You can't edit Razor files after you precompile views.</span></span> <span data-ttu-id="ff8e6-115">편집 된 보기에는 게시 된 번들 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="ff8e6-115">The edited views won't be present in the published bundle.</span></span> 

<span data-ttu-id="ff8e6-116">배포 하려면 미리 컴파일된 뷰:</span><span class="sxs-lookup"><span data-stu-id="ff8e6-116">To deploy precompiled views:</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="ff8e6-117">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="ff8e6-117">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="ff8e6-118">프로젝트가.NET Framework를 대상으로 하는 경우 패키지 참조를 포함할 `Microsoft.AspNetCore.Mvc.Razor.ViewCompilation`:</span><span class="sxs-lookup"><span data-stu-id="ff8e6-118">If your project targets .NET Framework, include a package reference to `Microsoft.AspNetCore.Mvc.Razor.ViewCompilation`:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Mvc.Razor.ViewCompilation" Version="2.0.0" PrivateAssets="All" />
```

<span data-ttu-id="ff8e6-119">프로젝트 대상이.NET Core를 변경 하지 않아도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ff8e6-119">If your project targets .NET Core, no changes are necessary.</span></span>

<span data-ttu-id="ff8e6-120">ASP.NET Core 2.x 프로젝트 템플릿 암시적으로 설정 합니다. `MvcRazorCompileOnPublish` 를 `true` 에서이 노드를 안전 하 게 제거할 수 즉 기본적으로는 *.csproj* 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="ff8e6-120">The ASP.NET Core 2.x project templates implicitly set `MvcRazorCompileOnPublish` to `true` by default, which means this node can be safely removed from the *.csproj* file.</span></span> <span data-ttu-id="ff8e6-121">명시적으로 지정 해야 하는 것을 선호 있는지 설정 하더라도 피해는 `MvcRazorCompileOnPublish` 속성을 `true`합니다.</span><span class="sxs-lookup"><span data-stu-id="ff8e6-121">If you prefer to be explicit, there's no harm in setting the `MvcRazorCompileOnPublish` property to `true`.</span></span> <span data-ttu-id="ff8e6-122">다음 *.csproj* 샘플에서는이 설정을 강조 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="ff8e6-122">The following *.csproj* sample highlights this setting:</span></span>

[!code-xml[Main](view-compilation\sample\MvcRazorCompileOnPublish2.csproj?highlight=5)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="ff8e6-123">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="ff8e6-123">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="ff8e6-124">설정 `MvcRazorCompileOnPublish` 를 `true`, 패키지 참조를 포함 하 고 `Microsoft.AspNetCore.Mvc.Razor.ViewCompilation`합니다.</span><span class="sxs-lookup"><span data-stu-id="ff8e6-124">Set `MvcRazorCompileOnPublish` to `true`, and include a package reference to `Microsoft.AspNetCore.Mvc.Razor.ViewCompilation`.</span></span> <span data-ttu-id="ff8e6-125">다음 *.csproj* 샘플에서는 이러한 설정을 강조 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="ff8e6-125">The following *.csproj* sample highlights these settings:</span></span>

[!code-xml[Main](view-compilation\sample\MvcRazorCompileOnPublish.csproj?highlight=5,12)]

---
