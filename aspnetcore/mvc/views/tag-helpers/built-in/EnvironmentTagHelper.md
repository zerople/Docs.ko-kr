---
title: "ASP.NET Core에서 환경 태그 도우미"
author: pkellner
description: "ASP.Net Core 환경 태그 도우미 정의 된 모든 속성을 포함 하 여"
keywords: "ASP.NET Core, 태그 도우미"
ms.author: riande
manager: wpickett
ms.date: 07/14/2017
ms.topic: article
ms.technology: aspnet
ms.prod: aspnet-core
uid: mvc/views/tag-helpers/builtin-th/EnvironmentTagHelper
ms.openlocfilehash: 5870d9ebd02becf29f892c91310022d3b9a6b7af
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/11/2017
---
# <a name="environment-tag-helper-in-aspnet-core"></a><span data-ttu-id="f96e9-104">ASP.NET Core에서 환경 태그 도우미</span><span class="sxs-lookup"><span data-stu-id="f96e9-104">Environment Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="f96e9-105">여 [Peter Kellner](http://peterkellner.net) 및 [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span><span class="sxs-lookup"><span data-stu-id="f96e9-105">By [Peter Kellner](http://peterkellner.net) and [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span></span>

<span data-ttu-id="f96e9-106">환경 태그 도우미 조건에 따라 현재 호스팅 환경에 따라 포함된 된 콘텐츠에 렌더링 합니다.</span><span class="sxs-lookup"><span data-stu-id="f96e9-106">The Environment Tag Helper conditionally renders its enclosed content based on the current hosting environment.</span></span> <span data-ttu-id="f96e9-107">단일 특성 `names` 쉼표로 구분 된 목록이 환경 이름, 하는 모든 포함 된 콘텐츠를 렌더링할 수를 트리거할 현재 환경에 일치 하는 경우.</span><span class="sxs-lookup"><span data-stu-id="f96e9-107">Its single attribute `names` is a comma separated list of environment names, that if any match to the current environment, will trigger the enclosed content to be rendered.</span></span>

## <a name="environment-tag-helper-attributes"></a><span data-ttu-id="f96e9-108">환경 태그 도우미 특성</span><span class="sxs-lookup"><span data-stu-id="f96e9-108">Environment Tag Helper Attributes</span></span>

### <a name="names"></a><span data-ttu-id="f96e9-109">이름</span><span class="sxs-lookup"><span data-stu-id="f96e9-109">names</span></span>

<span data-ttu-id="f96e9-110">단일 호스팅 환경 이름 또는 호스팅 환경 이름의 포함 된 콘텐츠를 렌더링을 트리거하는 쉼표로 구분 된 목록을 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="f96e9-110">Accepts a single hosting environment name or a comma-separated list of hosting environment names that trigger the rendering of the enclosed content.</span></span>

<span data-ttu-id="f96e9-111">이러한 값은 ASP.NET Core 정적 속성에서 반환 된 현재 값과 비교할 `HostingEnvironment.EnvironmentName`합니다.</span><span class="sxs-lookup"><span data-stu-id="f96e9-111">These value(s) are compared to the current value returned from the ASP.NET Core static property `HostingEnvironment.EnvironmentName`.</span></span>  <span data-ttu-id="f96e9-112">이 값은 다음 중 하나: **준비**; **개발** 또는 **프로덕션**합니다.</span><span class="sxs-lookup"><span data-stu-id="f96e9-112">This value is one of the following: **Staging**; **Development** or **Production**.</span></span> <span data-ttu-id="f96e9-113">비교는 대/소문자를 무시합니다.</span><span class="sxs-lookup"><span data-stu-id="f96e9-113">The comparison ignores case.</span></span>

<span data-ttu-id="f96e9-114">유효한의 예로 `environment` 태그 도우미는:</span><span class="sxs-lookup"><span data-stu-id="f96e9-114">An example of a valid `environment` tag helper is:</span></span>

```cshtml
<environment names="Staging,Production">
  <strong>HostingEnvironment.EnvironmentName is Staging or Production</strong>
</environment>
```

## <a name="include-and-exclude-attributes"></a><span data-ttu-id="f96e9-115">include 및 exclude 특성</span><span class="sxs-lookup"><span data-stu-id="f96e9-115">include and exclude attributes</span></span>

<span data-ttu-id="f96e9-116">ASP.NET Core 2.x 추가 `include`  &  `exclude` 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="f96e9-116">ASP.NET Core 2.x adds the `include` & `exclude` attributes.</span></span> <span data-ttu-id="f96e9-117">포함 또는 제외 호스팅 환경 이름을 기반으로 포함 된 콘텐츠를 렌더링 하 이러한 특성 제어 합니다.</span><span class="sxs-lookup"><span data-stu-id="f96e9-117">These attributes control rendering the enclosed content based on the included or excluded hosting environment names.</span></span>

### <a name="include-aspnet-core-20-and-later"></a><span data-ttu-id="f96e9-118">2.0 이상 ASP.NET Core를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="f96e9-118">include ASP.NET Core 2.0 and later</span></span>

<span data-ttu-id="f96e9-119">`include` 의 비슷한 동작을 포함 하는 속성은 `names` ASP.NET Core 1.0의 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="f96e9-119">The `include` property has a similar behavior of the `names` attribute in ASP.NET Core 1.0.</span></span>

```cshtml
<environment include="Staging,Production">
  <strong>HostingEnvironment.EnvironmentName is Staging or Production</strong>
</environment>
```

### <a name="exclude-aspnet-core-20-and-later"></a><span data-ttu-id="f96e9-120">ASP.NET Core 2.0 이상 제외</span><span class="sxs-lookup"><span data-stu-id="f96e9-120">exclude ASP.NET Core 2.0 and later</span></span>

<span data-ttu-id="f96e9-121">반면,는 `exclude` 속성을 사용은 `EnvironmentTagHelper` 지정한 유형 제외한 모든 호스팅 환경 이름에 대해 포함 된 콘텐츠를 렌더링 합니다.</span><span class="sxs-lookup"><span data-stu-id="f96e9-121">In contrast, the `exclude` property lets the `EnvironmentTagHelper` render the enclosed content for all hosting environment names except the one(s) that you specified.</span></span>

```cshtml
<environment exclude="Development">
  <strong>HostingEnvironment.EnvironmentName is Staging or Production</strong>
</environment>
```

## <a name="additional-resources"></a><span data-ttu-id="f96e9-122">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="f96e9-122">Additional resources</span></span>

* <xref:fundamentals/environments>
* <xref:fundamentals/dependency-injection#service-lifetimes-and-registration-options>