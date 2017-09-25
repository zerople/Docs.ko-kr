---
title: "URL의 ASP.NET Core 미들웨어를 다시 작성"
author: guardrex
description: "다시 작성 및 ASP.NET Core 응용 프로그램에서 URL 다시 쓰기 미들웨어를 사용 하는 방법에 대 한 지침이 포함 된 리디렉션 URL 소개 합니다."
keywords: "ASP.NET Core URL 재작성, URL 재작성, URL 리디렉션, 미들웨어, apache_mod 리디렉션 URL"
ms.author: riande
manager: wpickett
ms.date: 08/17/2017
ms.topic: article
ms.assetid: e6130638-c410-4161-9921-b658ce988bd1
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/url-rewriting
ms.openlocfilehash: 44c78a6304eacc70cdee9bb0d9407376017abcac
ms.sourcegitcommit: 8005eb4051e568d88ee58d48424f39916052e6e2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2017
---
# <a name="url-rewriting-middleware-in-aspnet-core"></a><span data-ttu-id="e8c6d-104">URL의 ASP.NET Core 미들웨어를 다시 작성</span><span class="sxs-lookup"><span data-stu-id="e8c6d-104">URL Rewriting Middleware in ASP.NET Core</span></span>

<span data-ttu-id="e8c6d-105">여 [Luke Latham](https://github.com/guardrex) 및 [Mikael Mengistu](https://github.com/mikaelm12)</span><span class="sxs-lookup"><span data-stu-id="e8c6d-105">By [Luke Latham](https://github.com/guardrex) and [Mikael Mengistu](https://github.com/mikaelm12)</span></span>

[<span data-ttu-id="e8c6d-106">샘플 코드 보기 또는 다운로드</span><span class="sxs-lookup"><span data-stu-id="e8c6d-106">View or download sample code</span></span>](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/)

<span data-ttu-id="e8c6d-107">URL 다시 쓰기는 하나 이상의 미리 정의 된 규칙을 기반으로 하는 Url 요청을 수정 하는 작업입니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-107">URL rewriting is the act of modifying request URLs based on one or more predefined rules.</span></span> <span data-ttu-id="e8c6d-108">URL 다시 작성 하는 위치 및 주소 밀접 하 게 연결 되지 않은 리소스 위치 및 해당 주소 간의 추상화를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-108">URL rewriting creates an abstraction between resource locations and their addresses so that the locations and addresses are not tightly linked.</span></span> <span data-ttu-id="e8c6d-109">URL 다시 쓰기는 중요 한 몇 가지 시나리오가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-109">There are several scenarios where URL rewriting is valuable:</span></span>
* <span data-ttu-id="e8c6d-110">이동 또는 이러한 리소스에 대 한 안정적인 로케이터를 유지 관리 하는 동안 서버 리소스를 일시적 또는 영구적으로 대체</span><span class="sxs-lookup"><span data-stu-id="e8c6d-110">Moving or replacing server resources temporarily or permanently while maintaining stable locators for those resources</span></span>
* <span data-ttu-id="e8c6d-111">하나의 응용 프로그램의 영역에서 또는 다른 앱에서 처리 중인 요청과 분</span><span class="sxs-lookup"><span data-stu-id="e8c6d-111">Splitting request processing across different apps or across areas of one app</span></span>
* <span data-ttu-id="e8c6d-112">제거, 추가 또는 들어오는 요청에 URL 세그먼트를 다시 구성</span><span class="sxs-lookup"><span data-stu-id="e8c6d-112">Removing, adding, or reorganizing URL segments on incoming requests</span></span>
* <span data-ttu-id="e8c6d-113">공용 Url에 대 한 검색 엔진 최적화 SEO () 최적화</span><span class="sxs-lookup"><span data-stu-id="e8c6d-113">Optimizing public URLs for Search Engine Optimization (SEO)</span></span>
* <span data-ttu-id="e8c6d-114">링크를 클릭 하면 경우가 콘텐츠를 예측 하는 데 도움이 공용 친화적 Url의 사용 허용</span><span class="sxs-lookup"><span data-stu-id="e8c6d-114">Permitting the use of friendly public URLs to help people predict the content they will find by following a link</span></span>
* <span data-ttu-id="e8c6d-115">끝점을 보호 하는 안전 하지 않은 요청 리디렉션</span><span class="sxs-lookup"><span data-stu-id="e8c6d-115">Redirecting insecure requests to secure endpoints</span></span>
* <span data-ttu-id="e8c6d-116">이미지 hotlinking 방지</span><span class="sxs-lookup"><span data-stu-id="e8c6d-116">Preventing image hotlinking</span></span>

<span data-ttu-id="e8c6d-117">여러 가지 방법으로 URL을 변경 하 고, regex, Apache mod_rewrite 모듈 규칙, IIS를 다시 작성 모듈 규칙을 포함 하 고, 사용자 지정 규칙 논리를 사용 하 여에 대 한 규칙을 정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-117">You can define rules for changing the URL in several ways, including regex, Apache mod_rewrite module rules, IIS Rewrite Module rules, and using custom rule logic.</span></span> <span data-ttu-id="e8c6d-118">이 문서는 ASP.NET Core 응용 프로그램에서 URL 다시 쓰기 미들웨어를 사용 하는 방법에 대 한 지침이 포함 된 URL 다시 쓰기를 소개 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-118">This document introduces URL rewriting with instructions on how to use URL Rewriting Middleware in ASP.NET Core apps.</span></span>

> [!NOTE]
> <span data-ttu-id="e8c6d-119">URL 다시 쓰기 응용 프로그램의 성능이 저하 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-119">URL rewriting can reduce the performance of an app.</span></span> <span data-ttu-id="e8c6d-120">가능한 경우 규칙의 복잡성와 수를 제한 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-120">Where feasible, you should limit the number and complexity of rules.</span></span>

## <a name="url-redirect-and-url-rewrite"></a><span data-ttu-id="e8c6d-121">URL 리디렉션 및 URL 다시 쓰기</span><span class="sxs-lookup"><span data-stu-id="e8c6d-121">URL redirect and URL rewrite</span></span>
<span data-ttu-id="e8c6d-122">단어 사이의 차이 *URL 리디렉션* 및 *URL 재작성* 에 미묘한 보일 수 있지만 첫 번째 요소가 있 클라이언트에 리소스를 제공 하는 데 중요 한 이점이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-122">The difference in wording between *URL redirect* and *URL rewrite* may seem subtle at first but has important implications for providing resources to clients.</span></span> <span data-ttu-id="e8c6d-123">ASP.NET Core URL 다시 쓰기 미들웨어는 모두에 대 한 필요성을 충족 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-123">ASP.NET Core's URL Rewriting Middleware is capable of meeting the need for both.</span></span>

<span data-ttu-id="e8c6d-124">A *URL 리디렉션* 는 클라이언트 쪽 작업으로, 다른 주소에서 리소스에 액세스 하려면 클라이언트에 명령입니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-124">A *URL redirect* is a client-side operation, where the client is instructed to access a resource at another address.</span></span> <span data-ttu-id="e8c6d-125">서버에 대 한 왕복 걸리며 클라이언트는 리소스에 대 한 새 요청을 클라이언트로 반환 된 리디렉션 URL 브라우저의 주소 표시줄에 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-125">This requires a round-trip to the server, and the redirect URL returned to the client appears in the browser's address bar when the client makes a new request for the resource.</span></span> <span data-ttu-id="e8c6d-126">경우 `/resource` 은 *리디렉션* 를 `/different-resource`, 클라이언트 요청 `/resource`, 서버 클라이언트에서 리소스를 가져와야 하는 응답 `/different-resource` 리디렉션 임을 나타내는 상태 코드와 함께 임시 또는 영구 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-126">If `/resource` is *redirected* to `/different-resource`, the client requests `/resource`, and the server responds that the client should obtain the resource at `/different-resource` with a status code indicating that the redirect is either temporary or permanent.</span></span> <span data-ttu-id="e8c6d-127">클라이언트에 리디렉션 URL에 대 한 리소스에 대 한 새 요청을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-127">The client executes a new request for the resource at the redirect URL.</span></span>

![WebAPI 서비스 끝점 v2 (버전 2) 서버를 일시적으로 변경 버전 (v1) 1에서에서 되었습니다.](url-rewriting/_static/url_redirect.png)

<span data-ttu-id="e8c6d-133">를 다른 URL로 요청을 리디렉션하는 경우 리디렉션 영구 또는 임시 인지 여부를 나타낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-133">When redirecting requests to a different URL, you indicate whether the redirect is permanent or temporary.</span></span> <span data-ttu-id="e8c6d-134">301 (영구적 이동) 상태 코드는 리소스에 대 한 모든 향후 요청 새 URL을 사용 하도록 클라이언트에 게 지시 하 시겠습니까 고 있는 리소스에 영구 새 URL 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-134">The 301 (Moved Permanently) status code is used where the resource has a new, permanent URL and you wish to instruct the client that all future requests for the resource should use the new URL.</span></span> <span data-ttu-id="e8c6d-135">*301 상태 코드를 받을 때 클라이언트는 응답을 캐시할 수 있습니다.*</span><span class="sxs-lookup"><span data-stu-id="e8c6d-135">*The client may cache the response when a 301 status code is received.*</span></span> <span data-ttu-id="e8c6d-136">302 (있음) 상태 코드 변경, 클라이언트를 저장 하 고 리디렉션 URL을 나중에 다시 사용 하지 않아야 하 여기서 리디렉션은 임시 또는 제목 일반적으로 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-136">The 302 (Found) status code is used where the redirection is temporary or generally subject to change, such that the client shouldn't store and reuse the redirect URL in the future.</span></span> <span data-ttu-id="e8c6d-137">자세한 내용은 참조 [RFC 2616: 상태 코드 정의](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)합니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-137">For more information, see [RFC 2616: Status Code Definitions](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>

<span data-ttu-id="e8c6d-138">A *URL 재작성* 다른 리소스 주소에서 리소스를 제공 하는 서버 쪽 작업입니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-138">A *URL rewrite* is a server-side operation to provide a resource from a different resource address.</span></span> <span data-ttu-id="e8c6d-139">URL 다시 쓰기 서버에 대 한 라운드트립이 필요 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-139">Rewriting a URL doesn't require a round-trip to the server.</span></span> <span data-ttu-id="e8c6d-140">다시 쓴된 URL 클라이언트에 반환 되지 않습니다 및 브라우저의 주소 표시줄에 표시 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-140">The rewritten URL isn't returned to the client and won't appear in the browser's address bar.</span></span> <span data-ttu-id="e8c6d-141">때 `/resource` 은 *다시 작성* 를 `/different-resource`, 클라이언트 요청 `/resource`와 서버 *내부적으로* 인출에서 리소스 `/different-resource`합니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-141">When `/resource` is *rewritten* to `/different-resource`, the client requests `/resource`, and the server *internally* fetches the resource at `/different-resource`.</span></span> <span data-ttu-id="e8c6d-142">클라이언트를 다시 작성된 URL에 리소스를 검색할 수 있지만 해당 요청을 수행 하 고 응답을 받는 리소스 다시 작성된 URL에 있는지 클라이언트 정보 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-142">Although the client might be able to retrieve the resource at the rewritten URL, the client won't be informed that the resource exists at the rewritten URL when it makes its request and receives the response.</span></span>

![WebAPI 서비스 끝점 (v1) 버전 1에서에서 v2 (버전 2) 서버에서 변경 되었습니다.](url-rewriting/_static/url_rewrite.png)

## <a name="url-rewriting-sample-app"></a><span data-ttu-id="e8c6d-147">URL 다시 쓰기 샘플 응용 프로그램</span><span class="sxs-lookup"><span data-stu-id="e8c6d-147">URL rewriting sample app</span></span>
<span data-ttu-id="e8c6d-148">URL 다시 쓰기 사용 하 여 미들웨어의 기능을 탐색할 수 있습니다는 [URL 다시 쓰기 샘플 응용 프로그램](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/)합니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-148">You can explore the features of the URL Rewriting Middleware with the [URL rewriting sample app](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/).</span></span> <span data-ttu-id="e8c6d-149">응용 프로그램 다시 쓰기를 적용 하 고 리디렉션 규칙 및 다시 작성 또는 리디렉션 URL을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-149">The app applies rewrite and redirect rules and shows the rewritten or redirected URL.</span></span>

## <a name="when-to-use-url-rewriting-middleware"></a><span data-ttu-id="e8c6d-150">URL 다시 쓰기 미들웨어를 사용 하는 경우</span><span class="sxs-lookup"><span data-stu-id="e8c6d-150">When to use URL Rewriting Middleware</span></span>
<span data-ttu-id="e8c6d-151">URL 다시 쓰기 미들웨어를 사용 하 여 사용할 수 없는 경우는 [URL 재작성 모듈](https://www.iis.net/downloads/microsoft/url-rewrite) Windows 서버에서 iis는 [Apache mod_rewrite 모듈](https://httpd.apache.org/docs/2.4/rewrite/) Apache 서버의 [Nginx에URL다시쓰기](https://www.nginx.com/blog/creating-nginx-rewrite-rules/), 응용 프로그램에서 호스팅되는 또는 [HTTP.sys 서버](xref:fundamentals/servers/httpsys) (이전의 [WebListener](xref:fundamentals/servers/weblistener)).</span><span class="sxs-lookup"><span data-stu-id="e8c6d-151">Use URL Rewriting Middleware when you are unable to use the [URL Rewrite module](https://www.iis.net/downloads/microsoft/url-rewrite) with IIS on Windows Server, the [Apache mod_rewrite module](https://httpd.apache.org/docs/2.4/rewrite/) on Apache Server, [URL rewriting on Nginx](https://www.nginx.com/blog/creating-nginx-rewrite-rules/), or your app is hosted on [HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called [WebListener](xref:fundamentals/servers/weblistener)).</span></span> <span data-ttu-id="e8c6d-152">기술을 사용 하는 서버 기반 URL 다시 쓰기 IIS, Apache 또는 Nginx 주된 이유는 있는지 미들웨어 이러한 모듈의 전체 기능을 지원 하지 않습니다 및 미들웨어의 성능을 때문일 수와 일치 하지 않습니다는 모듈입니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-152">The main reasons to use the server-based URL rewriting technologies in IIS, Apache, or Nginx are that the middleware doesn't support the full features of these modules and the performance of the middleware probably won't match that of the modules.</span></span> <span data-ttu-id="e8c6d-153">그러나 일부의 기능은 사용 하지 않는 ASP.NET Core 프로젝트와 같은 서버 모듈의 `IsFile` 및 `IsDirectory` IIS 재작성 모듈의 제약 조건입니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-153">However, there are some features of the server modules that don't work with ASP.NET Core projects, such as the `IsFile` and `IsDirectory` constraints of the IIS Rewrite module.</span></span> <span data-ttu-id="e8c6d-154">이러한 시나리오에서 미들웨어를 대신 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-154">In these scenarios, use the middleware instead.</span></span>

## <a name="package"></a><span data-ttu-id="e8c6d-155">패키지</span><span class="sxs-lookup"><span data-stu-id="e8c6d-155">Package</span></span>
<span data-ttu-id="e8c6d-156">미들웨어를 프로젝트에 포함 하려면 추가에 대 한 참조는 [ `Microsoft.AspNetCore.Rewrite` ](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite/) 패키지 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-156">To include the middleware in your project, add a reference to the [`Microsoft.AspNetCore.Rewrite`](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite/) package.</span></span> <span data-ttu-id="e8c6d-157">이 기능은 이상 ASP.NET Core 1.1을 대상으로 하는 응용 프로그램에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-157">This feature is available for apps that target ASP.NET Core 1.1 or later.</span></span>

## <a name="extension-and-options"></a><span data-ttu-id="e8c6d-158">확장 및 옵션</span><span class="sxs-lookup"><span data-stu-id="e8c6d-158">Extension and options</span></span>
<span data-ttu-id="e8c6d-159">URL 재작성을 설정 하 고 규칙의 인스턴스를 만들어 리디렉션하는 `RewriteOptions` 각 규칙에 대 한 확장 메서드를 사용 하 여 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-159">Establish your URL rewrite and redirect rules by creating an instance of the `RewriteOptions` class with extension methods for each of your rules.</span></span> <span data-ttu-id="e8c6d-160">여러 규칙 싶다는 의사를 처리 하는 순서를 연결 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-160">Chain multiple rules in the order that you would like them processed.</span></span> <span data-ttu-id="e8c6d-161">`RewriteOptions` 으로 요청 파이프라인에 추가 되는 URL 다시 쓰기 미들웨어로 전달 되 `app.UseRewriter(options);`합니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-161">The `RewriteOptions` are passed into the URL Rewriting Middleware as it's added to the request pipeline with `app.UseRewriter(options);`.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="e8c6d-162">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="e8c6d-162">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

[!code-csharp[Main](url-rewriting/samples/2.x/Program.cs?name=snippet1)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="e8c6d-163">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="e8c6d-163">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

[!code-csharp[Main](url-rewriting/samples/1.x/Startup.cs?name=snippet1)]

---

### <a name="url-redirect"></a><span data-ttu-id="e8c6d-164">URL 리디렉션</span><span class="sxs-lookup"><span data-stu-id="e8c6d-164">URL redirect</span></span>
<span data-ttu-id="e8c6d-165">사용 하 여 `AddRedirect` 요청을 리디렉션할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-165">Use `AddRedirect` to redirect requests.</span></span> <span data-ttu-id="e8c6d-166">첫 번째 매개 변수는 받는 URL의 경로에 일치 하는 regex의 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-166">The first parameter contains your regex for matching on the path of the incoming URL.</span></span> <span data-ttu-id="e8c6d-167">두 번째 매개 변수 대체 문자열입니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-167">The second parameter is the replacement string.</span></span> <span data-ttu-id="e8c6d-168">세 번째 매개 변수가 있는 경우 상태 코드를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-168">The third parameter, if present, specifies the status code.</span></span> <span data-ttu-id="e8c6d-169">상태 코드를 지정 하지 않으면 기본값으로 302 (있음)을 나타내는 리소스는 일시적으로 이동 또는 대체 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-169">If you don't specify the status code, it defaults to 302 (Found), which indicates that the resource is temporarily moved or replaced.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="e8c6d-170">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="e8c6d-170">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

[!code-csharp[Main](url-rewriting/samples/2.x/Program.cs?name=snippet1&highlight=5)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="e8c6d-171">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="e8c6d-171">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

[!code-csharp[Main](url-rewriting/samples/1.x/Startup.cs?name=snippet1&highlight=2)]

---

<span data-ttu-id="e8c6d-172">개발자 도구를 사용 하도록 설정 된 브라우저에서 요청 경로로 샘플 응용 프로그램을 할 `/redirect-rule/1234/5678`합니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-172">In a browser with developer tools enabled, make a request to the sample app with the path `/redirect-rule/1234/5678`.</span></span> <span data-ttu-id="e8c6d-173">요청 경로 일치 하는 regex `redirect-rule/(.*)`, 및 경로 아래 템플릿으로 바뀝니다 `/redirected/1234/5678`합니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-173">The regex matches the request path on `redirect-rule/(.*)`, and the path is replaced with `/redirected/1234/5678`.</span></span> <span data-ttu-id="e8c6d-174">리디렉션 URL 302 (있음) 상태 코드를 사용 하 여 클라이언트에 다시 전송 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-174">The redirect URL is sent back to the client with a 302 (Found) status code.</span></span> <span data-ttu-id="e8c6d-175">브라우저에서 브라우저의 주소 표시줄에 표시 되는 리디렉션 URL에서 새 요청 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-175">The browser makes a new request at the redirect URL, which appears in the browser's address bar.</span></span> <span data-ttu-id="e8c6d-176">리디렉션 URL에 일치 하는 샘플 앱에 없는 규칙이, 하므로 두 번째 요청 응용 프로그램에서 보낸 200 (정상) 응답을 수신 및 응답의 본문으로 리디렉션 URL을 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-176">Since no rules in the sample app match on the redirect URL, the second request receives a 200 (OK) response from the app and the body of the response shows the redirect URL.</span></span> <span data-ttu-id="e8c6d-177">URL은 서버로 왕복이 수행 됩니다 *리디렉션*합니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-177">A roundtrip is made to the server when a URL is *redirected*.</span></span>

> [!WARNING]
> <span data-ttu-id="e8c6d-178">리디렉션 규칙을 설정할 때 주의 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-178">Be cautious when establishing your redirect rules.</span></span> <span data-ttu-id="e8c6d-179">리디렉션 규칙 리디렉션 이후에 포함 하 여 응용 프로그램에 각 요청에 대해 평가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-179">Your redirect rules are evaluated on each request to the app, including after a redirect.</span></span> <span data-ttu-id="e8c6d-180">가 실수로 쉽게 무한 리디렉션 루프가 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-180">It's easy to accidently create a loop of infinite redirects.</span></span>

<span data-ttu-id="e8c6d-181">원래 요청:`/redirect-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="e8c6d-181">Original Request: `/redirect-rule/1234/5678`</span></span>

![브라우저 창을 요청 및 응답을 추적 하는 개발자 도구](url-rewriting/_static/add_redirect.png)

<span data-ttu-id="e8c6d-183">괄호 안에 포함 된 식의 일부 라고는 *캡처 그룹*합니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-183">The part of the expression contained within parentheses is called a *capture group*.</span></span> <span data-ttu-id="e8c6d-184">점 (`.`) 식의 의미 *임의의 문자*합니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-184">The dot (`.`) of the expression means *match any character*.</span></span> <span data-ttu-id="e8c6d-185">별표 (`*`) 나타냅니다 *앞에 오는 문자 0 회 이상 일치*합니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-185">The asterisk (`*`) indicates *match the preceding character zero or more times*.</span></span> <span data-ttu-id="e8c6d-186">따라서 URL의 마지막 두 개의 경로 세그먼트 `1234/5678`, 캡처 그룹에 의해 캡처된 `(.*)`합니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-186">Therefore, the last two path segments of the URL, `1234/5678`, are captured by capture group `(.*)`.</span></span> <span data-ttu-id="e8c6d-187">후 요청 URL에서 제공한 값 `redirect-rule/` 이 단일 캡처 그룹에 의해 캡처됩니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-187">Any value you provide in the request URL after `redirect-rule/` is captured by this single capture group.</span></span>

<span data-ttu-id="e8c6d-188">대체 문자열에 캡처된 그룹은 달러 기호를 사용 하 여 문자열에 삽입 (`$`) 다음 캡처의 시퀀스 번호입니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-188">In the replacement string, captured groups are injected into the string with the dollar sign (`$`) followed by the sequence number of the capture.</span></span> <span data-ttu-id="e8c6d-189">첫 번째 캡처 그룹 값에 따라 획득 된은 `$1`, 두 번째 `$2`, 하며 프로그램 정규식에서 캡처 그룹에 대 한 시퀀스에서 지속 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-189">The first capture group value is obtained with `$1`, the second with `$2`, and they continue in sequence for the capture groups in your regex.</span></span> <span data-ttu-id="e8c6d-190">캡처된 그룹이 하나만 있는에서 경우 리디렉션 규칙 regex 샘플 응용 프로그램을 인 대체 문자열에 삽입 된 그룹이 하나만 있으므로 `$1`합니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-190">There's only one captured group in the redirect rule regex in the sample app, so there's only one injected group in the replacement string, which is `$1`.</span></span> <span data-ttu-id="e8c6d-191">URL은 규칙을 적용 하면 `/redirected/1234/5678`합니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-191">When the rule is applied, the URL becomes `/redirected/1234/5678`.</span></span>

<a name=url-redirect-to-secure-endpoint></a>
### <a name="url-redirect-to-a-secure-endpoint"></a><span data-ttu-id="e8c6d-192">보안 끝점을 URL 리디렉션</span><span class="sxs-lookup"><span data-stu-id="e8c6d-192">URL redirect to a secure endpoint</span></span>
<span data-ttu-id="e8c6d-193">사용 하 여 `AddRedirectToHttps` 동일한 호스트 및 HTTPS를 사용 하 여 경로에 HTTP 요청을 리디렉션할 수 (`https://`).</span><span class="sxs-lookup"><span data-stu-id="e8c6d-193">Use `AddRedirectToHttps` to redirect HTTP requests to the same host and path using HTTPS (`https://`).</span></span> <span data-ttu-id="e8c6d-194">상태 코드는 제공 되지 않는 경우 미들웨어 302 (있음) 기본값이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-194">If the status code isn't supplied, the middleware defaults to 302 (Found).</span></span> <span data-ttu-id="e8c6d-195">포트를 제공 하지 않으면, 미들웨어 기본적으로 `null`를로 변경 하는 프로토콜을 의미 하는 `https://` 클라이언트 포트 443에서 리소스에 액세스 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-195">If the port isn't supplied, the middleware defaults to `null`, which means the protocol changes to `https://` and the client accesses the resource on port 443.</span></span> <span data-ttu-id="e8c6d-196">상태 코드 301 (영구적 이동)로 설정 하 고 5001이 고 다른 포트로 변경 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-196">The example shows how to set the status code to 301 (Moved Permanently) and change the port to 5001.</span></span>
```csharp
var options = new RewriteOptions()
    .AddRedirectToHttps(301, 5001);

app.UseRewriter(options);
```
<span data-ttu-id="e8c6d-197">사용 하 여 `AddRedirectToHttpsPermanent` 안전 하지 않은 요청을 동일한 호스트와 보안 HTTPS 프로토콜을 사용 하 여 경로 리디렉션하도록 (`https://` 포트 443).</span><span class="sxs-lookup"><span data-stu-id="e8c6d-197">Use `AddRedirectToHttpsPermanent` to redirect insecure requests to the same host and path with secure HTTPS protocol (`https://` on port 443).</span></span> <span data-ttu-id="e8c6d-198">상태 코드 301 (영구적 이동)를 설정 하는 미들웨어입니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-198">The middleware sets the status code to 301 (Moved Permanently).</span></span>

<span data-ttu-id="e8c6d-199">샘플 응용 프로그램은 사용 하는 방법을 보여 주는 수 `AddRedirectToHttps` 또는 `AddRedirectToHttpsPermanent`합니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-199">The sample app is capable of demonstrating how to use `AddRedirectToHttps` or `AddRedirectToHttpsPermanent`.</span></span> <span data-ttu-id="e8c6d-200">확장 메서드를 추가 `RewriteOptions`합니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-200">Add the extension method to the `RewriteOptions`.</span></span> <span data-ttu-id="e8c6d-201">모든 URL에서 응용 프로그램에 안전 하지 않은 요청을 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-201">Make an insecure request to the app at any URL.</span></span> <span data-ttu-id="e8c6d-202">자체 서명 된 인증서를 신뢰할 수 있는지 경고 브라우저 보안을 해제 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-202">Dismiss the browser security warning that the self-signed certificate is untrusted.</span></span>

<span data-ttu-id="e8c6d-203">사용 하 여 원래 요청 `AddRedirectToHttps(301, 5001)`:`/secure`</span><span class="sxs-lookup"><span data-stu-id="e8c6d-203">Original Request using `AddRedirectToHttps(301, 5001)`: `/secure`</span></span>

![브라우저 창을 요청 및 응답을 추적 하는 개발자 도구](url-rewriting/_static/add_redirect_to_https.png)

<span data-ttu-id="e8c6d-205">사용 하 여 원래 요청 `AddRedirectToHttpsPermanent`:`/secure`</span><span class="sxs-lookup"><span data-stu-id="e8c6d-205">Original Request using `AddRedirectToHttpsPermanent`: `/secure`</span></span>

![브라우저 창을 요청 및 응답을 추적 하는 개발자 도구](url-rewriting/_static/add_redirect_to_https_permanent.png)

### <a name="url-rewrite"></a><span data-ttu-id="e8c6d-207">URL 다시 쓰기</span><span class="sxs-lookup"><span data-stu-id="e8c6d-207">URL rewrite</span></span>
<span data-ttu-id="e8c6d-208">사용 하 여 `AddRewrite` Url을 다시 작성 하기 위한 규칙을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-208">Use `AddRewrite` to create a rules for rewriting URLs.</span></span> <span data-ttu-id="e8c6d-209">첫 번째 매개 변수는 들어오는 URL 경로에 일치 하는 regex의 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-209">The first parameter contains your regex for matching on the incoming URL path.</span></span> <span data-ttu-id="e8c6d-210">두 번째 매개 변수 대체 문자열입니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-210">The second parameter is the replacement string.</span></span> <span data-ttu-id="e8c6d-211">세 번째 매개 변수 `skipRemainingRules: {true|false}`, 현재 규칙이 적용 되는 경우에 추가 재작성 규칙 건너뛸 것인지 여부는 미들웨어를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-211">The third parameter, `skipRemainingRules: {true|false}`, indicates to the middleware whether or not to skip additional rewrite rules if the current rule is applied.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="e8c6d-212">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="e8c6d-212">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

[!code-csharp[Main](url-rewriting/samples/2.x/Program.cs?name=snippet1&highlight=6)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="e8c6d-213">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="e8c6d-213">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

[!code-csharp[Main](url-rewriting/samples/1.x/Startup.cs?name=snippet1&highlight=3)]

---

<span data-ttu-id="e8c6d-214">원래 요청:`/rewrite-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="e8c6d-214">Original Request: `/rewrite-rule/1234/5678`</span></span>

![브라우저 창을 요청 및 응답을 추적 하는 개발자 도구](url-rewriting/_static/add_rewrite.png)

<span data-ttu-id="e8c6d-216">정규식에서 첫 번째 점은 캐럿 (`^`) 식의 시작 부분에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-216">The first thing you notice in the regex is the carat (`^`) at the beginning of the expression.</span></span> <span data-ttu-id="e8c6d-217">이 URL 경로의 시작 부분에서 일치 하는 시작 됨을 의미 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-217">This means that matching starts at the beginning of the URL path.</span></span>

<span data-ttu-id="e8c6d-218">리디렉션 규칙을 사용 하면 앞의 예제에서 `redirect-rule/(.*)`, 정규식의 시작 부분에 없는 캐럿; 따라서 모든 수 앞에 있는 문자 `redirect-rule/` 성공한 일치에 대 한 경로에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-218">In the earlier example with the redirect rule, `redirect-rule/(.*)`, there's no carat at the start of the regex; therefore, any characters may precede `redirect-rule/` in the path for a successful match.</span></span>

| <span data-ttu-id="e8c6d-219">Path</span><span class="sxs-lookup"><span data-stu-id="e8c6d-219">Path</span></span>                               | <span data-ttu-id="e8c6d-220">일치</span><span class="sxs-lookup"><span data-stu-id="e8c6d-220">Match</span></span> |
| ---------------------------------- | :---: |
| `/redirect-rule/1234/5678`         | <span data-ttu-id="e8c6d-221">예</span><span class="sxs-lookup"><span data-stu-id="e8c6d-221">Yes</span></span>   |
| `/my-cool-redirect-rule/1234/5678` | <span data-ttu-id="e8c6d-222">예</span><span class="sxs-lookup"><span data-stu-id="e8c6d-222">Yes</span></span>   |
| `/anotherredirect-rule/1234/5678`  | <span data-ttu-id="e8c6d-223">예</span><span class="sxs-lookup"><span data-stu-id="e8c6d-223">Yes</span></span>   |

<span data-ttu-id="e8c6d-224">다시 쓰기 규칙 `^rewrite-rule/(\d+)/(\d+)`, 경로로 시작 하는 경우에 일치 `rewrite-rule/`합니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-224">The rewrite rule, `^rewrite-rule/(\d+)/(\d+)`, only matches paths if they start with `rewrite-rule/`.</span></span> <span data-ttu-id="e8c6d-225">다시 쓰기 규칙와 위의 리디렉션 규칙 간에 일치 하는 차이 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-225">Notice the difference in matching between the rewrite rule below and the redirect rule above.</span></span>

| <span data-ttu-id="e8c6d-226">Path</span><span class="sxs-lookup"><span data-stu-id="e8c6d-226">Path</span></span>                              | <span data-ttu-id="e8c6d-227">일치</span><span class="sxs-lookup"><span data-stu-id="e8c6d-227">Match</span></span> |
| --------------------------------- | :---: |
| `/rewrite-rule/1234/5678`         | <span data-ttu-id="e8c6d-228">예</span><span class="sxs-lookup"><span data-stu-id="e8c6d-228">Yes</span></span>   |
| `/my-cool-rewrite-rule/1234/5678` | <span data-ttu-id="e8c6d-229">아니요</span><span class="sxs-lookup"><span data-stu-id="e8c6d-229">No</span></span>    |
| `/anotherrewrite-rule/1234/5678`  | <span data-ttu-id="e8c6d-230">아니요</span><span class="sxs-lookup"><span data-stu-id="e8c6d-230">No</span></span>    |

<span data-ttu-id="e8c6d-231">다음은 `^rewrite-rule/` 부분 식의은 두 개의 캡처 그룹 `(\d+)/(\d+)`합니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-231">Following the `^rewrite-rule/` portion of the expression, there are two capture groups, `(\d+)/(\d+)`.</span></span> <span data-ttu-id="e8c6d-232">`\d` 의미 *숫자 (number)*합니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-232">The `\d` signifies *match a digit (number)*.</span></span> <span data-ttu-id="e8c6d-233">더하기 기호 (`+`) 의미 *앞에 오는 문자를 하나 이상의 일치*합니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-233">The plus sign (`+`) means *match one or more of the preceding character*.</span></span> <span data-ttu-id="e8c6d-234">따라서 URL에는 슬래시 다음에 다른 숫자와 앞에 숫자가 포함 되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-234">Therefore, the URL must contain a number followed by a forward-slash followed by another number.</span></span> <span data-ttu-id="e8c6d-235">그룹은 다시 작성된 URL에 삽입이 캡처 `$1` 및 `$2`합니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-235">These capture groups are injected into the rewritten URL as `$1` and `$2`.</span></span> <span data-ttu-id="e8c6d-236">다시 쓰기 규칙 대체 문자열은 문자열에 캡처된 그룹을 배치합니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-236">The rewrite rule replacement string places the captured groups into the querystring.</span></span> <span data-ttu-id="e8c6d-237">요청 된 경로 `/rewrite-rule/1234/5678` 에서 리소스를 가져올 수는 다시 작성 `/rewritten?var1=1234&var2=5678`합니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-237">The requested path of `/rewrite-rule/1234/5678` is rewritten to obtain the resource at `/rewritten?var1=1234&var2=5678`.</span></span> <span data-ttu-id="e8c6d-238">쿼리 문자열은 원래 요청에 있는 경우 해당 데이터가 보존 됩니다 때 URL을 다시 작성 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-238">If a querystring is present on the original request, it's preserved when the URL is rewritten.</span></span>

<span data-ttu-id="e8c6d-239">서버 리소스를 가져올 수 없는 왕복이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-239">There's no roundtrip to the server to obtain the resource.</span></span> <span data-ttu-id="e8c6d-240">리소스가 존재 인출 개이고 200 (OK) 상태 코드를 사용 하 여 클라이언트에 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-240">If the resource exists, it's fetched and returned to the client with a 200 (OK) status code.</span></span> <span data-ttu-id="e8c6d-241">클라이언트가 리디렉션 없는 때문에 브라우저 주소 표시줄에 URL이 변경 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-241">Because the client isn't redirected, the URL in the browser address bar doesn't change.</span></span> <span data-ttu-id="e8c6d-242">클라이언트의 경우, 관련해 서 URL 다시 쓰기 작업을 하지 발생 했습니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-242">As far as the client is concerned, the URL rewrite operation never occurred.</span></span>

> [!NOTE]
> <span data-ttu-id="e8c6d-243">사용 하 여 `skipRemainingRules: true` 가능 하면 항상, 비용이 많이 드는 프로세스 일치 규칙을 사용 하면 응용 프로그램 응답 시간이 단축 하기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-243">Use `skipRemainingRules: true` whenever possible, because matching rules is an expensive process and reduces app response time.</span></span> <span data-ttu-id="e8c6d-244">가장 빠른 응용 프로그램 응답:</span><span class="sxs-lookup"><span data-stu-id="e8c6d-244">For the fastest app response:</span></span>
> * <span data-ttu-id="e8c6d-245">가장 자주 일치 하는 규칙에 가장 자주 일치 하는 규칙에서 다시 쓰기 규칙을 정렬 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-245">Order your rewrite rules from the most frequently matched rule to the least frequently matched rule.</span></span>
> * <span data-ttu-id="e8c6d-246">일치 하는 항목이 없는 추가 규칙 처리가 필요한 경우 나머지 규칙의 처리를 건너뜁니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-246">Skip the processing of the remaining rules when a match occurs and no additional rule processing is required.</span></span>

### <a name="apache-modrewrite"></a><span data-ttu-id="e8c6d-247">Apache mod_rewrite</span><span class="sxs-lookup"><span data-stu-id="e8c6d-247">Apache mod_rewrite</span></span>
<span data-ttu-id="e8c6d-248">사용 하 여 Apache mod_rewrite 규칙 적용 `AddApacheModRewrite`합니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-248">Apply Apache mod_rewrite rules with `AddApacheModRewrite`.</span></span> <span data-ttu-id="e8c6d-249">규칙 파일 앱 배포 되 고 있는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-249">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="e8c6d-250">자세한 내용과 mod_rewrite 규칙의 예에 대 한 참조 [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/)합니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-250">For more information and examples of mod_rewrite rules, see [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/).</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="e8c6d-251">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="e8c6d-251">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="e8c6d-252">A `StreamReader` 에서 규칙을 읽는 데 사용 되는 *ApacheModRewrite.txt* 규칙 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-252">A `StreamReader` is used to read the rules from the *ApacheModRewrite.txt* rules file.</span></span>

[!code-csharp[Main](url-rewriting/samples/2.x/Program.cs?name=snippet1&highlight=1,7)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="e8c6d-253">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="e8c6d-253">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="e8c6d-254">첫 번째 매개 변수를 사용는 `IFileProvider`를 통해 제공 하는 [종속성 주입](dependency-injection.md)합니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-254">The first parameter takes an `IFileProvider`, which is provided via [Dependency Injection](dependency-injection.md).</span></span> <span data-ttu-id="e8c6d-255">`IHostingEnvironment` 제공 하는 삽입 된 여 `ContentRootFileProvider`합니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-255">The `IHostingEnvironment` is injected to provide the `ContentRootFileProvider`.</span></span> <span data-ttu-id="e8c6d-256">두 번째 매개 변수는이 규칙 파일을 경로 *ApacheModRewrite.txt* 샘플 응용 프로그램에서입니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-256">The second parameter is the path to your rules file, which is *ApacheModRewrite.txt* in the sample app.</span></span>

[!code-csharp[Main](url-rewriting/samples/1.x/Startup.cs?name=snippet1&highlight=4)]

---

<span data-ttu-id="e8c6d-257">샘플 응용 프로그램에서 요청을 리디렉션합니다 `/apache-mod-rules-redirect/(.\*)` 를 `/redirected?id=$1`합니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-257">The sample app redirects requests from `/apache-mod-rules-redirect/(.\*)` to `/redirected?id=$1`.</span></span> <span data-ttu-id="e8c6d-258">응답 상태 코드 302 (있음)입니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-258">The response status code is 302 (Found).</span></span>

[!code[Main](url-rewriting/samples/2.x/ApacheModRewrite.txt)]

<span data-ttu-id="e8c6d-259">원래 요청:`/apache-mod-rules-redirect/1234`</span><span class="sxs-lookup"><span data-stu-id="e8c6d-259">Original Request: `/apache-mod-rules-redirect/1234`</span></span>

![브라우저 창을 요청 및 응답을 추적 하는 개발자 도구](url-rewriting/_static/add_apache_mod_redirect.png)

##### <a name="supported-server-variables"></a><span data-ttu-id="e8c6d-261">지원 되는 서버 변수</span><span class="sxs-lookup"><span data-stu-id="e8c6d-261">Supported server variables</span></span>
<span data-ttu-id="e8c6d-262">미들웨어는 다음 Apache mod_rewrite 서버 변수를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-262">The middleware supports the following Apache mod_rewrite server variables:</span></span>
* <span data-ttu-id="e8c6d-263">CONN_REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="e8c6d-263">CONN_REMOTE_ADDR</span></span>
* <span data-ttu-id="e8c6d-264">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="e8c6d-264">HTTP_ACCEPT</span></span>
* <span data-ttu-id="e8c6d-265">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="e8c6d-265">HTTP_CONNECTION</span></span>
* <span data-ttu-id="e8c6d-266">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="e8c6d-266">HTTP_COOKIE</span></span>
* <span data-ttu-id="e8c6d-267">HTTP_FORWARDED</span><span class="sxs-lookup"><span data-stu-id="e8c6d-267">HTTP_FORWARDED</span></span>
* <span data-ttu-id="e8c6d-268">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="e8c6d-268">HTTP_HOST</span></span>
* <span data-ttu-id="e8c6d-269">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="e8c6d-269">HTTP_REFERER</span></span>
* <span data-ttu-id="e8c6d-270">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="e8c6d-270">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="e8c6d-271">HTTPS</span><span class="sxs-lookup"><span data-stu-id="e8c6d-271">HTTPS</span></span>
* <span data-ttu-id="e8c6d-272">I P V 6</span><span class="sxs-lookup"><span data-stu-id="e8c6d-272">IPV6</span></span>
* <span data-ttu-id="e8c6d-273">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="e8c6d-273">QUERY_STRING</span></span>
* <span data-ttu-id="e8c6d-274">REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="e8c6d-274">REMOTE_ADDR</span></span>
* <span data-ttu-id="e8c6d-275">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="e8c6d-275">REMOTE_PORT</span></span>
* <span data-ttu-id="e8c6d-276">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="e8c6d-276">REQUEST_FILENAME</span></span>
* <span data-ttu-id="e8c6d-277">REQUEST_METHOD</span><span class="sxs-lookup"><span data-stu-id="e8c6d-277">REQUEST_METHOD</span></span>
* <span data-ttu-id="e8c6d-278">REQUEST_SCHEME</span><span class="sxs-lookup"><span data-stu-id="e8c6d-278">REQUEST_SCHEME</span></span>
* <span data-ttu-id="e8c6d-279">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="e8c6d-279">REQUEST_URI</span></span>
* <span data-ttu-id="e8c6d-280">SCRIPT_FILENAME</span><span class="sxs-lookup"><span data-stu-id="e8c6d-280">SCRIPT_FILENAME</span></span>
* <span data-ttu-id="e8c6d-281">SERVER_ADDR</span><span class="sxs-lookup"><span data-stu-id="e8c6d-281">SERVER_ADDR</span></span>
* <span data-ttu-id="e8c6d-282">서버 _ 포트</span><span class="sxs-lookup"><span data-stu-id="e8c6d-282">SERVER_PORT</span></span>
* <span data-ttu-id="e8c6d-283">SERVER_PROTOCOL</span><span class="sxs-lookup"><span data-stu-id="e8c6d-283">SERVER_PROTOCOL</span></span>
* <span data-ttu-id="e8c6d-284">시간</span><span class="sxs-lookup"><span data-stu-id="e8c6d-284">TIME</span></span>
* <span data-ttu-id="e8c6d-285">TIME_DAY</span><span class="sxs-lookup"><span data-stu-id="e8c6d-285">TIME_DAY</span></span>
* <span data-ttu-id="e8c6d-286">TIME_HOUR</span><span class="sxs-lookup"><span data-stu-id="e8c6d-286">TIME_HOUR</span></span>
* <span data-ttu-id="e8c6d-287">TIME_MIN</span><span class="sxs-lookup"><span data-stu-id="e8c6d-287">TIME_MIN</span></span>
* <span data-ttu-id="e8c6d-288">TIME_MON</span><span class="sxs-lookup"><span data-stu-id="e8c6d-288">TIME_MON</span></span>
* <span data-ttu-id="e8c6d-289">TIME_SEC</span><span class="sxs-lookup"><span data-stu-id="e8c6d-289">TIME_SEC</span></span>
* <span data-ttu-id="e8c6d-290">TIME_WDAY</span><span class="sxs-lookup"><span data-stu-id="e8c6d-290">TIME_WDAY</span></span>
* <span data-ttu-id="e8c6d-291">TIME_YEAR</span><span class="sxs-lookup"><span data-stu-id="e8c6d-291">TIME_YEAR</span></span>

### <a name="iis-url-rewrite-module-rules"></a><span data-ttu-id="e8c6d-292">IIS URL 재작성 모듈 규칙</span><span class="sxs-lookup"><span data-stu-id="e8c6d-292">IIS URL Rewrite Module rules</span></span>
<span data-ttu-id="e8c6d-293">IIS URL 재작성 모듈에 적용 되는 규칙을 사용 하려면 사용 `AddIISUrlRewrite`합니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-293">To use rules that apply to the IIS URL Rewrite Module, use `AddIISUrlRewrite`.</span></span> <span data-ttu-id="e8c6d-294">규칙 파일 앱 배포 되 고 있는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-294">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="e8c6d-295">사용할 미들웨어 지시 하지 않는 프로그램 *web.config* 파일을 Windows Server IIS에서 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-295">Don't direct the middleware to use your *web.config* file when running on Windows Server IIS.</span></span> <span data-ttu-id="e8c6d-296">Iis에서 이러한 규칙 외부에 저장 해야 하면 *web.config* IIS 재작성 모듈와 충돌 하지 않도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-296">With IIS, these rules should be stored outside of your *web.config* to avoid conflicts with the IIS Rewrite module.</span></span> <span data-ttu-id="e8c6d-297">자세한 내용과 예는 IIS URL 재작성 모듈 규칙에 대 한 참조 [를 사용 하 여 Url 재작성 모듈 2.0](https://docs.microsoft.com/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) 및 [URL 재작성 모듈 구성 참조](https://docs.microsoft.com/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference)합니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-297">For more information and examples of IIS URL Rewrite Module rules, see [Using Url Rewrite Module 2.0](https://docs.microsoft.com/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) and [URL Rewrite Module Configuration Reference](https://docs.microsoft.com/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference).</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="e8c6d-298">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="e8c6d-298">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="e8c6d-299">A `StreamReader` 에서 규칙을 읽는 데 사용 되는 *IISUrlRewrite.xml* 규칙 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-299">A `StreamReader` is used to read the rules from the *IISUrlRewrite.xml* rules file.</span></span>

[!code-csharp[Main](url-rewriting/samples/2.x/Program.cs?name=snippet1&highlight=2,8)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="e8c6d-300">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="e8c6d-300">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="e8c6d-301">첫 번째 매개 변수를 사용는 `IFileProvider`, 두 번째 매개 변수, 즉 XML 규칙 파일에 경로 반면, *IISUrlRewrite.xml* 샘플 응용 프로그램에서입니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-301">The first parameter takes an `IFileProvider`, while the second parameter is the path to your XML rules file, which is *IISUrlRewrite.xml* in the sample app.</span></span>

[!code-csharp[Main](url-rewriting/samples/1.x/Startup.cs?name=snippet1&highlight=5)]

---

<span data-ttu-id="e8c6d-302">샘플 응용 프로그램의 요청을 다시 작성 `/iis-rules-rewrite/(.*)` 를 `/rewritten?id=$1`합니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-302">The sample app rewrites requests from `/iis-rules-rewrite/(.*)` to `/rewritten?id=$1`.</span></span> <span data-ttu-id="e8c6d-303">응답은 200 (정상)이 상태 코드를 사용 하 여 클라이언트에 전송 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-303">The response is sent to the client with a 200 (OK) status code.</span></span>

[!code-xml[Main](url-rewriting/samples/2.x/IISUrlRewrite.xml)]

<span data-ttu-id="e8c6d-304">원래 요청:`/iis-rules-rewrite/1234`</span><span class="sxs-lookup"><span data-stu-id="e8c6d-304">Original Request: `/iis-rules-rewrite/1234`</span></span>

![브라우저 창을 요청 및 응답을 추적 하는 개발자 도구](url-rewriting/_static/add_iis_url_rewrite.png)

<span data-ttu-id="e8c6d-306">영향을 미치는 방식 앱 바람직하지 않은 방법으로 구성 된 서버 수준 규칙 활성 IIS를 다시 작성 모듈 있는 경우 응용 프로그램에 대 한 IIS 재작성 모듈을 해제할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-306">If you have an active IIS Rewrite Module with server-level rules configured that would impact your app in undesirable ways, you can disable the IIS Rewrite Module for an app.</span></span> <span data-ttu-id="e8c6d-307">자세한 내용은 참조 [비활성화 IIS 모듈](xref:hosting/iis-modules#disabling-iis-modules)합니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-307">For more information, see [Disabling IIS modules](xref:hosting/iis-modules#disabling-iis-modules).</span></span>

#### <a name="unsupported-features"></a><span data-ttu-id="e8c6d-308">지원 되지 않는 기능</span><span class="sxs-lookup"><span data-stu-id="e8c6d-308">Unsupported features</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="e8c6d-309">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="e8c6d-309">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="e8c6d-310">출시 미들웨어 2.x ASP.NET 코어는 다음과 같은 IIS URL 재작성 모듈 기능을 지원 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-310">The middleware released with ASP.NET Core 2.x doesn't support the following IIS URL Rewrite Module features:</span></span>
* <span data-ttu-id="e8c6d-311">아웃 바운드 규칙</span><span class="sxs-lookup"><span data-stu-id="e8c6d-311">Outbound Rules</span></span>
* <span data-ttu-id="e8c6d-312">사용자 지정 서버 변수</span><span class="sxs-lookup"><span data-stu-id="e8c6d-312">Custom Server Variables</span></span>
* <span data-ttu-id="e8c6d-313">와일드카드</span><span class="sxs-lookup"><span data-stu-id="e8c6d-313">Wildcards</span></span>
* <span data-ttu-id="e8c6d-314">LogRewrittenUrl</span><span class="sxs-lookup"><span data-stu-id="e8c6d-314">LogRewrittenUrl</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="e8c6d-315">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="e8c6d-315">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="e8c6d-316">출시 미들웨어 1.x ASP.NET 코어는 다음과 같은 IIS URL 재작성 모듈 기능을 지원 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-316">The middleware released with ASP.NET Core 1.x doesn't support the following IIS URL Rewrite Module features:</span></span>
* <span data-ttu-id="e8c6d-317">전역 규칙</span><span class="sxs-lookup"><span data-stu-id="e8c6d-317">Global Rules</span></span>
* <span data-ttu-id="e8c6d-318">아웃 바운드 규칙</span><span class="sxs-lookup"><span data-stu-id="e8c6d-318">Outbound Rules</span></span>
* <span data-ttu-id="e8c6d-319">지도 다시 작성</span><span class="sxs-lookup"><span data-stu-id="e8c6d-319">Rewrite Maps</span></span>
* <span data-ttu-id="e8c6d-320">CustomResponse 동작</span><span class="sxs-lookup"><span data-stu-id="e8c6d-320">CustomResponse Action</span></span>
* <span data-ttu-id="e8c6d-321">사용자 지정 서버 변수</span><span class="sxs-lookup"><span data-stu-id="e8c6d-321">Custom Server Variables</span></span>
* <span data-ttu-id="e8c6d-322">와일드카드</span><span class="sxs-lookup"><span data-stu-id="e8c6d-322">Wildcards</span></span>
* <span data-ttu-id="e8c6d-323">동작: CustomResponse</span><span class="sxs-lookup"><span data-stu-id="e8c6d-323">Action:CustomResponse</span></span>
* <span data-ttu-id="e8c6d-324">LogRewrittenUrl</span><span class="sxs-lookup"><span data-stu-id="e8c6d-324">LogRewrittenUrl</span></span>

---

#### <a name="supported-server-variables"></a><span data-ttu-id="e8c6d-325">지원 되는 서버 변수</span><span class="sxs-lookup"><span data-stu-id="e8c6d-325">Supported server variables</span></span>
<span data-ttu-id="e8c6d-326">미들웨어는 다음 IIS URL 재작성 모듈 서버 변수를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-326">The middleware supports the following IIS URL Rewrite Module server variables:</span></span>
* <span data-ttu-id="e8c6d-327">CONTENT_LENGTH</span><span class="sxs-lookup"><span data-stu-id="e8c6d-327">CONTENT_LENGTH</span></span>
* <span data-ttu-id="e8c6d-328">CONTENT_TYPE</span><span class="sxs-lookup"><span data-stu-id="e8c6d-328">CONTENT_TYPE</span></span>
* <span data-ttu-id="e8c6d-329">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="e8c6d-329">HTTP_ACCEPT</span></span>
* <span data-ttu-id="e8c6d-330">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="e8c6d-330">HTTP_CONNECTION</span></span>
* <span data-ttu-id="e8c6d-331">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="e8c6d-331">HTTP_COOKIE</span></span>
* <span data-ttu-id="e8c6d-332">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="e8c6d-332">HTTP_HOST</span></span>
* <span data-ttu-id="e8c6d-333">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="e8c6d-333">HTTP_REFERER</span></span>
* <span data-ttu-id="e8c6d-334">HTTP_URL</span><span class="sxs-lookup"><span data-stu-id="e8c6d-334">HTTP_URL</span></span>
* <span data-ttu-id="e8c6d-335">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="e8c6d-335">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="e8c6d-336">HTTPS</span><span class="sxs-lookup"><span data-stu-id="e8c6d-336">HTTPS</span></span>
* <span data-ttu-id="e8c6d-337">LOCAL_ADDR</span><span class="sxs-lookup"><span data-stu-id="e8c6d-337">LOCAL_ADDR</span></span>
* <span data-ttu-id="e8c6d-338">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="e8c6d-338">QUERY_STRING</span></span>
* <span data-ttu-id="e8c6d-339">REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="e8c6d-339">REMOTE_ADDR</span></span>
* <span data-ttu-id="e8c6d-340">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="e8c6d-340">REMOTE_PORT</span></span>
* <span data-ttu-id="e8c6d-341">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="e8c6d-341">REQUEST_FILENAME</span></span>
* <span data-ttu-id="e8c6d-342">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="e8c6d-342">REQUEST_URI</span></span>

> [!NOTE]
> <span data-ttu-id="e8c6d-343">가져올 수도 있습니다는 `IFileProvider` 통해는 `PhysicalFileProvider`합니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-343">You can also obtain an `IFileProvider` via a `PhysicalFileProvider`.</span></span> <span data-ttu-id="e8c6d-344">이 방법은 규칙 파일 프로그램 재작성의 위치에 대 한 유연성을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-344">This approach may provide greater flexibility for the location of your rewrite rules files.</span></span> <span data-ttu-id="e8c6d-345">다시 쓰기 규칙 파일 제공 된 경로에서 서버에 배포 되 고 있는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-345">Make sure that your rewrite rules files are deployed to the server at the path you provide.</span></span>
> ```csharp
> PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
> ```

### <a name="method-based-rule"></a><span data-ttu-id="e8c6d-346">메서드 기반 규칙</span><span class="sxs-lookup"><span data-stu-id="e8c6d-346">Method-based rule</span></span>
<span data-ttu-id="e8c6d-347">사용 하 여 `Add(Action<RewriteContext> applyRule)` 메서드에서 사용자 고유의 규칙 논리를 구현할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-347">Use `Add(Action<RewriteContext> applyRule)` to implement your own rule logic in a method.</span></span> <span data-ttu-id="e8c6d-348">`RewriteContext` 노출는 `HttpContext` 메서드에서 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-348">The `RewriteContext` exposes the `HttpContext` for use in your method.</span></span> <span data-ttu-id="e8c6d-349">`context.Result` 확인 방법을 추가 파이프라인 처리 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-349">The `context.Result` determines how additional pipeline processing is handled.</span></span>

| <span data-ttu-id="e8c6d-350">컨텍스트입니다. 결과</span><span class="sxs-lookup"><span data-stu-id="e8c6d-350">context.Result</span></span>                       | <span data-ttu-id="e8c6d-351">작업</span><span class="sxs-lookup"><span data-stu-id="e8c6d-351">Action</span></span>                                                          |
| ------------------------------------ | --------------------------------------------------------------- |
| <span data-ttu-id="e8c6d-352">`RuleResult.ContinueRules`(기본값)</span><span class="sxs-lookup"><span data-stu-id="e8c6d-352">`RuleResult.ContinueRules` (default)</span></span> | <span data-ttu-id="e8c6d-353">계속 규칙 적용</span><span class="sxs-lookup"><span data-stu-id="e8c6d-353">Continue applying rules</span></span>                                         |
| `RuleResult.EndResponse`             | <span data-ttu-id="e8c6d-354">규칙 적용을 중지 하 고 응답 보내기</span><span class="sxs-lookup"><span data-stu-id="e8c6d-354">Stop applying rules and send the response</span></span>                       |
| `RuleResult.SkipRemainingRules`      | <span data-ttu-id="e8c6d-355">규칙 적용을 중지 하 고 미들웨어 컨텍스트 보내기</span><span class="sxs-lookup"><span data-stu-id="e8c6d-355">Stop applying rules and send the context to the next middleware</span></span> |

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="e8c6d-356">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="e8c6d-356">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

[!code-csharp[Main](url-rewriting/samples/2.x/Program.cs?name=snippet1&highlight=9)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="e8c6d-357">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="e8c6d-357">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

[!code-csharp[Main](url-rewriting/samples/1.x/Startup.cs?name=snippet1&highlight=6)]

---

<span data-ttu-id="e8c6d-358">샘플 응용 프로그램으로 끝나는 경로 대 한 요청을 리디렉션하는 방법을 보여 줍니다. *.xml*합니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-358">The sample app demonstrates a method that redirects requests for paths that end with *.xml*.</span></span> <span data-ttu-id="e8c6d-359">에 대 한 요청을 수행 하는 경우 `/file.xml`로 리디렉션되도록 `/xmlfiles/file.xml`합니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-359">If you make a request for `/file.xml`, it's redirected to `/xmlfiles/file.xml`.</span></span> <span data-ttu-id="e8c6d-360">상태 코드 301 (영구적 이동)로 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-360">The status code is set to 301 (Moved Permanently).</span></span> <span data-ttu-id="e8c6d-361">리디렉션에 대 한 명시적으로 설정 해야 응답;의 상태 코드 그렇지 않은 경우 200 (OK) 상태 코드가 반환 되 고 클라이언트에 리디렉션도 발생 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-361">For a redirect, you must explicitly set the status code of the response; otherwise, a 200 (OK) status code is returned and the redirect won't occur on the client.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="e8c6d-362">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="e8c6d-362">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

[!code-csharp[Main](url-rewriting/samples/2.x/RewriteRules.cs?name=snippet1)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="e8c6d-363">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="e8c6d-363">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

[!code-csharp[Main](url-rewriting/samples/1.x/Startup.cs?name=snippet2)]

---

<span data-ttu-id="e8c6d-364">원래 요청:`/file.xml`</span><span class="sxs-lookup"><span data-stu-id="e8c6d-364">Original Request: `/file.xml`</span></span>

![브라우저 창을 요청 및 file.xml에 대 한 응답을 추적 하는 개발자 도구](url-rewriting/_static/add_redirect_xml_requests.png)

### <a name="irule-based-rule"></a><span data-ttu-id="e8c6d-366">IRule 기반 규칙</span><span class="sxs-lookup"><span data-stu-id="e8c6d-366">IRule-based rule</span></span>
<span data-ttu-id="e8c6d-367">사용 하 여 `Add(IRule)` 에서 파생 된 클래스에서 사용자 고유의 규칙 논리를 구현 하려면 `IRule`합니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-367">Use `Add(IRule)` to implement your own rule logic in a class that derives from `IRule`.</span></span> <span data-ttu-id="e8c6d-368">사용 하는 `IRule` 규칙 메서드 기반 방식을 사용 하 여 보다 큰 유연성을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-368">Using an `IRule` provides greater flexibility over using the method-based rule approach.</span></span> <span data-ttu-id="e8c6d-369">파생 된 클래스에 대 한 매개 변수에서 전달 될 수 있는 생성자를 포함 될 수 있습니다는 `ApplyRule` 메서드.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-369">Your derived class may include a constructor, where you can pass in parameters for the `ApplyRule` method.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="e8c6d-370">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="e8c6d-370">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

[!code-csharp[Main](url-rewriting/samples/2.x/Program.cs?name=snippet1&highlight=10-11)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="e8c6d-371">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="e8c6d-371">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

[!code-csharp[Main](url-rewriting/samples/1.x/Startup.cs?name=snippet1&highlight=7-8)]

---

<span data-ttu-id="e8c6d-372">샘플 응용 프로그램에 대 한 매개 변수 값의 `extension` 및 `newPath` 여러 조건을 모두 충족 하도록 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-372">The values of the parameters in the sample app for the `extension` and the `newPath` are checked to meet several conditions.</span></span> <span data-ttu-id="e8c6d-373">`extension` 값을 포함 해야 하며 값 이어야 합니다 *.png*, *.jpg*, 또는 *.gif*합니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-373">The `extension` must contain a value, and the value must be *.png*, *.jpg*, or *.gif*.</span></span> <span data-ttu-id="e8c6d-374">경우는 `newPath` 유효 하지는 `ArgumentException` throw 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-374">If the `newPath` isn't valid, an `ArgumentException` is thrown.</span></span> <span data-ttu-id="e8c6d-375">에 대 한 요청을 수행 하는 경우 *image.png*로 리디렉션되도록 `/png-images/image.png`합니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-375">If you make a request for *image.png*, it's redirected to `/png-images/image.png`.</span></span> <span data-ttu-id="e8c6d-376">에 대 한 요청을 수행 하는 경우 *image.jpg*로 리디렉션되도록 `/jpg-images/image.jpg`합니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-376">If you make a request for *image.jpg*, it's redirected to `/jpg-images/image.jpg`.</span></span> <span data-ttu-id="e8c6d-377">상태 코드가 301 (영구적 이동)을으로 설정 되어 및 `context.Result` 처리 규칙을 중지 하 고 응답을 보내기로 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-377">The status code is set to 301 (Moved Permanently), and the `context.Result` is set to stop processing rules and send the response.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="e8c6d-378">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="e8c6d-378">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

[!code-csharp[Main](url-rewriting/samples/2.x/RewriteRules.cs?name=snippet2)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="e8c6d-379">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="e8c6d-379">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

[!code-csharp[Main](url-rewriting/samples/1.x/RewriteRule.cs?name=snippet1)]

---

<span data-ttu-id="e8c6d-380">원래 요청:`/image.png`</span><span class="sxs-lookup"><span data-stu-id="e8c6d-380">Original Request: `/image.png`</span></span>

![브라우저 창을 요청 및 image.png에 대 한 응답을 추적 하는 개발자 도구](url-rewriting/_static/add_redirect_png_requests.png)

<span data-ttu-id="e8c6d-382">원래 요청:`/image.jpg`</span><span class="sxs-lookup"><span data-stu-id="e8c6d-382">Original Request: `/image.jpg`</span></span>

![브라우저 창을 요청 및 image.jpg에 대 한 응답을 추적 하는 개발자 도구](url-rewriting/_static/add_redirect_jpg_requests.png)

## <a name="regex-examples"></a><span data-ttu-id="e8c6d-384">정규식 예제</span><span class="sxs-lookup"><span data-stu-id="e8c6d-384">Regex examples</span></span>

| <span data-ttu-id="e8c6d-385">Goal</span><span class="sxs-lookup"><span data-stu-id="e8c6d-385">Goal</span></span> | <span data-ttu-id="e8c6d-386">Regex 문자열 &</span><span class="sxs-lookup"><span data-stu-id="e8c6d-386">Regex String &</span></span><br><span data-ttu-id="e8c6d-387">일치 예</span><span class="sxs-lookup"><span data-stu-id="e8c6d-387">Match Example</span></span> | <span data-ttu-id="e8c6d-388">대체 문자열 &</span><span class="sxs-lookup"><span data-stu-id="e8c6d-388">Replacement String &</span></span><br><span data-ttu-id="e8c6d-389">출력 예</span><span class="sxs-lookup"><span data-stu-id="e8c6d-389">Output Example</span></span> |
| ---- | :-----------------------------: | :------------------------------------: |
| <span data-ttu-id="e8c6d-390">쿼리 문자열에 경로 다시 작성</span><span class="sxs-lookup"><span data-stu-id="e8c6d-390">Rewrite path into querystring</span></span> | `^path/(.*)/(.*)`<br>`/path/abc/123` | `path?var1=$1&var2=$2`<br>`/path?var1=abc&var2=123` |
| <span data-ttu-id="e8c6d-391">후행 슬래시를 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-391">Strip trailing slash</span></span> | `(.*)/$`<br>`/path/` | `$1`<br>`/path` |
| <span data-ttu-id="e8c6d-392">후행 슬래시를 적용 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-392">Enforce trailing slash</span></span> | `(.*[^/])$`<br>`/path` | `$1/`<br>`/path/` |
| <span data-ttu-id="e8c6d-393">특정 요청을 다시 작성 하지 마십시오.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-393">Avoid rewriting specific requests</span></span> | `(.*[^(\.axd)])$`<br><span data-ttu-id="e8c6d-394">예:`/resource.htm`</span><span class="sxs-lookup"><span data-stu-id="e8c6d-394">Yes: `/resource.htm`</span></span><br><span data-ttu-id="e8c6d-395">아니요:`/resource.axd`</span><span class="sxs-lookup"><span data-stu-id="e8c6d-395">No: `/resource.axd`</span></span> | `rewritten/$1`<br>`/rewritten/resource.htm`<br>`/resource.axd` |
| <span data-ttu-id="e8c6d-396">URL 세그먼트를 다시 정렬</span><span class="sxs-lookup"><span data-stu-id="e8c6d-396">Rearrange URL segments</span></span> | `path/(.*)/(.*)/(.*)`<br>`path/1/2/3` | `path/$3/$2/$1`<br>`path/3/2/1` |
| <span data-ttu-id="e8c6d-397">URL 세그먼트를 대체 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-397">Replace a URL segment</span></span> | `^(.*)/segment2/(.*)`<br>`/segment1/segment2/segment3` | `$1/replaced/$2`<br>`/segment1/replaced/segment3` |

## <a name="additional-resources"></a><span data-ttu-id="e8c6d-398">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="e8c6d-398">Additional resources</span></span>
* [<span data-ttu-id="e8c6d-399">응용 프로그램 시작</span><span class="sxs-lookup"><span data-stu-id="e8c6d-399">Application Startup</span></span>](startup.md)
* [<span data-ttu-id="e8c6d-400">미들웨어</span><span class="sxs-lookup"><span data-stu-id="e8c6d-400">Middleware</span></span>](middleware.md)
* [<span data-ttu-id="e8c6d-401">.NET에서의 정규식</span><span class="sxs-lookup"><span data-stu-id="e8c6d-401">Regular expressions in .NET</span></span>](/dotnet/articles/standard/base-types/regular-expressions)
* [<span data-ttu-id="e8c6d-402">정규식 언어 - 빠른 참조</span><span class="sxs-lookup"><span data-stu-id="e8c6d-402">Regular expression language - quick reference</span></span>](/dotnet/articles/standard/base-types/quick-ref)
* [<span data-ttu-id="e8c6d-403">Apache mod_rewrite</span><span class="sxs-lookup"><span data-stu-id="e8c6d-403">Apache mod_rewrite</span></span>](https://httpd.apache.org/docs/2.4/rewrite/)
* [<span data-ttu-id="e8c6d-404">(IIS) 용 Url 재작성 모듈 2.0을 사용 하 여</span><span class="sxs-lookup"><span data-stu-id="e8c6d-404">Using Url Rewrite Module 2.0 (for IIS)</span></span>](https://docs.microsoft.com/iis/extensions/url-rewrite-module/using-url-rewrite-module-20)
* [<span data-ttu-id="e8c6d-405">URL 재작성 모듈 구성 참조</span><span class="sxs-lookup"><span data-stu-id="e8c6d-405">URL Rewrite Module Configuration Reference</span></span>](https://docs.microsoft.com/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference)
* [<span data-ttu-id="e8c6d-406">IIS URL 재작성 모듈 포럼</span><span class="sxs-lookup"><span data-stu-id="e8c6d-406">IIS URL Rewrite Module Forum</span></span>](https://forums.iis.net/1152.aspx)
* [<span data-ttu-id="e8c6d-407">간단한 URL 구조를 유지 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8c6d-407">Keep a simple URL structure</span></span>](https://support.google.com/webmasters/answer/76329?hl=en)
* [<span data-ttu-id="e8c6d-408">팁과 트릭 10 URL 다시 쓰기</span><span class="sxs-lookup"><span data-stu-id="e8c6d-408">10 URL Rewriting Tips and Tricks</span></span>](http://ruslany.net/2009/04/10-url-rewriting-tips-and-tricks/)
* [<span data-ttu-id="e8c6d-409">슬래시 또는 슬래시</span><span class="sxs-lookup"><span data-stu-id="e8c6d-409">To slash or not to slash</span></span>](https://webmasters.googleblog.com/2010/04/to-slash-or-not-to-slash.html)
