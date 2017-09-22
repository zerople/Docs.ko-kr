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
ms.openlocfilehash: e8e7d4a69fd681534fb313ff113805bfd6a6d44e
ms.sourcegitcommit: 78d28178345a0eea91556e4cd1adad98b1446db8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2017
---
# <a name="enforcing-ssl-in-an-aspnet-core-app"></a><span data-ttu-id="2f2f6-104">ASP.NET Core 응용 프로그램에서 SSL을 강제 적용</span><span class="sxs-lookup"><span data-stu-id="2f2f6-104">Enforcing SSL in an ASP.NET Core app</span></span>

<span data-ttu-id="2f2f6-105">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="2f2f6-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="2f2f6-106">이 문서에서는 표시 하는 방법:</span><span class="sxs-lookup"><span data-stu-id="2f2f6-106">This document shows how to:</span></span>

- <span data-ttu-id="2f2f6-107">모든 요청 (HTTPS 요청에만 해당)에 대 한 SSL이 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="2f2f6-107">Require SSL for all requests (HTTPS requests only).</span></span>
- <span data-ttu-id="2f2f6-108">HTTPS에 대 한 모든 HTTP 요청을 리디렉션하십시오.</span><span class="sxs-lookup"><span data-stu-id="2f2f6-108">Redirect all HTTP requests to HTTPS.</span></span>

## <a name="require-ssl"></a><span data-ttu-id="2f2f6-109">SSL 필요</span><span class="sxs-lookup"><span data-stu-id="2f2f6-109">Require SSL</span></span>

<span data-ttu-id="2f2f6-110">[RequireHttpsAttribute](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.requirehttpsattribute) ssl을 사용 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f2f6-110">The [RequireHttpsAttribute](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.requirehttpsattribute) is used to require SSL.</span></span> <span data-ttu-id="2f2f6-111">컨트롤러 또는이 특성을 사용 하 여 메서드를 데코레이팅 할 수 있습니다 하거나 아래와 같이 전체적으로 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2f2f6-111">You can decorate controllers or methods with this attribute or you can apply it globally as shown below:</span></span>

<span data-ttu-id="2f2f6-112">다음 코드를 추가 `ConfigureServices` 에 `Startup`:</span><span class="sxs-lookup"><span data-stu-id="2f2f6-112">Add the following code to `ConfigureServices` in `Startup`:</span></span>

[!code-csharp[Main](authentication/accconfirm/sample/WebApp1/Startup.cs?name=snippet2&highlight=4-)]

<span data-ttu-id="2f2f6-113">위의 강조 표시 된 코드에서는 모든 요청 사용 `HTTPS`, 따라서 HTTP 요청은 무시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f2f6-113">The highlighted code above requires all requests use `HTTPS`, therefore HTTP requests are ignored.</span></span> <span data-ttu-id="2f2f6-114">다음 강조 표시 된 코드를 HTTPS로 모든 HTTP 요청을 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="2f2f6-114">The following highlighted code redirects all HTTP requests to HTTPS:</span></span>

[!code-csharp[Main](authentication/accconfirm/sample/WebApp1/Startup.cs?name=snippet_AddRedirectToHttps&highlight=7-)]

<span data-ttu-id="2f2f6-115">참조 [URL 다시 쓰기 미들웨어](xref:fundamentals/url-rewriting) 자세한 정보에 대 한 합니다.</span><span class="sxs-lookup"><span data-stu-id="2f2f6-115">See [URL Rewriting Middleware](xref:fundamentals/url-rewriting) for more information.</span></span>

<span data-ttu-id="2f2f6-116">HTTPS를 전역적으로 요구 (`options.Filters.Add(new RequireHttpsAttribute());`) 보안 모범 사례입니다.</span><span class="sxs-lookup"><span data-stu-id="2f2f6-116">Requiring HTTPS globally (`options.Filters.Add(new RequireHttpsAttribute());`) is a security best practice.</span></span> <span data-ttu-id="2f2f6-117">적용 된 `[RequireHttps]` 모든 컨트롤러에는 특성을 전역으로 HTTPS를 요구 하는 것 만큼 안전 간주 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2f2f6-117">Applying the `[RequireHttps]` attribute to all controller is not considered as secure as requiring HTTPS globally.</span></span> <span data-ttu-id="2f2f6-118">보장할 수 없습니다 적용할 저장 되므로 응용 프로그램에 추가 하는 새로운 컨트롤러는 `[RequireHttps]` 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="2f2f6-118">You can't guarantee new controllers added to your app will remember to apply the `[RequireHttps]` attribute.</span></span>

## <a name="set-up-iis-express-for-sslhttps"></a><span data-ttu-id="2f2f6-119">IIS Express SSL/HTTPS에 대 한 설정</span><span class="sxs-lookup"><span data-stu-id="2f2f6-119">Set up IIS Express for SSL/HTTPS</span></span>

<span data-ttu-id="2f2f6-120">참조 [ASP.NET Core에서 개발을 위한 HTTPS 설정](xref:security/https#iisxpress)합니다.</span><span class="sxs-lookup"><span data-stu-id="2f2f6-120">See [Setting up HTTPS for development in ASP.NET Core](xref:security/https#iisxpress).</span></span>
