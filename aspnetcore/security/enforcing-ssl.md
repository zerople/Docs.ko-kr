---
title: "ASP.NET Core 응용 프로그램에서 SSL을 강제 적용"
author: rick-anderson
description: "웹 응용 프로그램에서 ASP.NET Core SSL을 요구 하는 방법을 보여 줍니다."
keywords: ASP.NET Core, SSL, HTTPS, RequireHttpsAttribute, IIS Express
ms.author: riande
manager: wpickett
ms.date: 07/19/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/enforcing-ssl
ms.openlocfilehash: 35554939bd574b2826053004ed437bee46154c2b
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/11/2017
---
# <a name="enforcing-ssl-in-an-aspnet-core-app"></a><span data-ttu-id="e6308-104">ASP.NET Core 응용 프로그램에서 SSL을 강제 적용</span><span class="sxs-lookup"><span data-stu-id="e6308-104">Enforcing SSL in an ASP.NET Core app</span></span>

<span data-ttu-id="e6308-105">으로 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="e6308-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="e6308-106">이 문서에서는 표시 하는 방법:</span><span class="sxs-lookup"><span data-stu-id="e6308-106">This document shows how to:</span></span>

- <span data-ttu-id="e6308-107">모든 요청 (HTTPS 요청에만 해당)에 대 한 SSL이 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="e6308-107">Require SSL for all requests (HTTPS requests only).</span></span>
- <span data-ttu-id="e6308-108">HTTPS에 대 한 모든 HTTP 요청을 리디렉션하십시오.</span><span class="sxs-lookup"><span data-stu-id="e6308-108">Redirect all HTTP requests to HTTPS.</span></span>

## <a name="require-ssl"></a><span data-ttu-id="e6308-109">SSL 필요</span><span class="sxs-lookup"><span data-stu-id="e6308-109">Require SSL</span></span>

<span data-ttu-id="e6308-110">[RequireHttpsAttribute](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.requirehttpsattribute) ssl을 사용 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e6308-110">The [RequireHttpsAttribute](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.requirehttpsattribute) is used to require SSL.</span></span> <span data-ttu-id="e6308-111">컨트롤러 또는이 특성을 사용 하 여 메서드를 데코레이팅 할 수 있습니다 하거나 아래와 같이 전체적으로 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e6308-111">You can decorate controllers or methods with this attribute or you can apply it globally as shown below:</span></span>

<span data-ttu-id="e6308-112">다음 코드를 추가 `ConfigureServices` 에 `Startup`:</span><span class="sxs-lookup"><span data-stu-id="e6308-112">Add the following code to `ConfigureServices` in `Startup`:</span></span>

<span data-ttu-id="e6308-113">[!code-csharp[Main](authentication/accconfirm/sample/WebApp1/Startup.cs?name=snippet2&highlight=4-)]</span><span class="sxs-lookup"><span data-stu-id="e6308-113">[!code-csharp[Main](authentication/accconfirm/sample/WebApp1/Startup.cs?name=snippet2&highlight=4-)]</span></span>

<span data-ttu-id="e6308-114">위의 강조 표시 된 코드에서는 모든 요청 사용 `HTTPS`, 따라서 HTTP 요청은 무시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e6308-114">The highlighted code above requires all requests use `HTTPS`, therefore HTTP requests are ignored.</span></span> <span data-ttu-id="e6308-115">다음 강조 표시 된 코드를 HTTPS로 모든 HTTP 요청을 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="e6308-115">The following highlighted code redirects all HTTP requests to HTTPS:</span></span>

<span data-ttu-id="e6308-116">[!code-csharp[Main](authentication/accconfirm/sample/WebApp1/Startup.cs?name=snippet_AddRedirectToHttps&highlight=7-)]</span><span class="sxs-lookup"><span data-stu-id="e6308-116">[!code-csharp[Main](authentication/accconfirm/sample/WebApp1/Startup.cs?name=snippet_AddRedirectToHttps&highlight=7-)]</span></span>

<span data-ttu-id="e6308-117">참조 [URL 다시 쓰기 미들웨어](xref:fundamentals/url-rewriting) 자세한 정보에 대 한 합니다.</span><span class="sxs-lookup"><span data-stu-id="e6308-117">See [URL Rewriting Middleware](xref:fundamentals/url-rewriting) for more information.</span></span>

<span data-ttu-id="e6308-118">HTTPS를 전역적으로 요구 (`options.Filters.Add(new RequireHttpsAttribute());`) 보안 모범 사례입니다.</span><span class="sxs-lookup"><span data-stu-id="e6308-118">Requiring HTTPS globally (`options.Filters.Add(new RequireHttpsAttribute());`) is a security best practice.</span></span> <span data-ttu-id="e6308-119">적용 된 `[RequireHttps]` 모든 컨트롤러에는 특성을 전역으로 HTTPS를 요구 하는 것 만큼 안전 간주 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e6308-119">Applying the `[RequireHttps]` attribute to all controller is not considered as secure as requiring HTTPS globally.</span></span> <span data-ttu-id="e6308-120">보장할 수 없습니다 적용할 저장 되므로 응용 프로그램에 추가 하는 새로운 컨트롤러는 `[RequireHttps]` 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="e6308-120">You can't guarantee new controllers added to your app will remember to apply the `[RequireHttps]` attribute.</span></span>

## <a name="set-up-iis-express-for-sslhttps"></a><span data-ttu-id="e6308-121">IIS Express SSL/HTTPS에 대 한 설정</span><span class="sxs-lookup"><span data-stu-id="e6308-121">Set up IIS Express for SSL/HTTPS</span></span>

<span data-ttu-id="e6308-122">참조 [ASP.NET Core에서 개발을 위한 HTTPS 설정](xref:security/https#iisxpress)합니다.</span><span class="sxs-lookup"><span data-stu-id="e6308-122">See [Setting up HTTPS for development in ASP.NET Core](xref:security/https#iisxpress).</span></span>
