---
title: "ASP.NET Core에서 세션 및 응용 프로그램 상태"
author: rick-anderson
description: "요청 간에 유지 응용 프로그램 및 사용자 (세션) 상태에 접근 합니다."
keywords: "ASP.NET Core 응용 프로그램 상태, 세션 상태, 쿼리 문자열, 게시"
ms.author: riande
manager: wpickett
ms.date: 06/08/2017
ms.topic: article
ms.assetid: 18cda488-0769-4cb9-82f6-4c6685f2045d
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/app-state
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 409444e99cfa49f30812c6130120391a8f477839
ms.sourcegitcommit: 50608ec8ae49897d8bf11d5f6dc511da30862bfa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/15/2017
---
# <a name="introduction-to-session-and-application-state-in-aspnet-core"></a><span data-ttu-id="23dbe-104">ASP.NET Core에서 세션 및 응용 프로그램 상태 소개</span><span class="sxs-lookup"><span data-stu-id="23dbe-104">Introduction to session and application state in ASP.NET Core</span></span>

<span data-ttu-id="23dbe-105">여 [Rick Anderson](https://twitter.com/RickAndMSFT), [Steve Smith](https://ardalis.com/), 및 [Diana LaRose](https://github.com/DianaLaRose)</span><span class="sxs-lookup"><span data-stu-id="23dbe-105">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Steve Smith](https://ardalis.com/), and [Diana LaRose](https://github.com/DianaLaRose)</span></span>

<span data-ttu-id="23dbe-106">HTTP는 상태 비저장 프로토콜입니다.</span><span class="sxs-lookup"><span data-stu-id="23dbe-106">HTTP is a stateless protocol.</span></span> <span data-ttu-id="23dbe-107">웹 서버 독립적인 요청으로 각 HTTP 요청을 처리 하 고 이전 요청에서 사용자 값을 유지 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="23dbe-107">A  web server treats each HTTP request as an independent request and does not retain user values from previous requests.</span></span> <span data-ttu-id="23dbe-108">이 문서에서는 응용 프로그램 및 요청 간에 세션 상태를 유지 하기 위해 다양 한 방법 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="23dbe-108">This article discusses different ways to preserve application and session state between requests.</span></span> 

## <a name="session-state"></a><span data-ttu-id="23dbe-109">세션 상태</span><span class="sxs-lookup"><span data-stu-id="23dbe-109">Session state</span></span>

<span data-ttu-id="23dbe-110">세션 상태는 사용자가 웹 앱을 방문 하는 동안 사용자 데이터를 저장 하는 데 사용할 수 있는 ASP.NET Core의 기능입니다.</span><span class="sxs-lookup"><span data-stu-id="23dbe-110">Session state is a feature in ASP.NET Core that you can use to save and store user data while the user browses your web app.</span></span> <span data-ttu-id="23dbe-111">세션 상태 서버에 사전 또는 해시 테이블, 구성 된 브라우저에서 요청을 통해 데이터를 유지 합니다.</span><span class="sxs-lookup"><span data-stu-id="23dbe-111">Consisting of a dictionary or hash table on the server, session state persists data across requests from a browser.</span></span> <span data-ttu-id="23dbe-112">세션 데이터는 캐시에 의해 지원 됩니다.</span><span class="sxs-lookup"><span data-stu-id="23dbe-112">The session data is backed by a cache.</span></span>

<span data-ttu-id="23dbe-113">ASP.NET Core 클라이언트 각 요청과 함께 서버에 전송 된 세션 ID를 포함 하는 쿠키를 제공 하 여 세션 상태를 유지 합니다.</span><span class="sxs-lookup"><span data-stu-id="23dbe-113">ASP.NET Core maintains session state by giving the client a cookie that contains the session ID, which is sent to the server with each request.</span></span> <span data-ttu-id="23dbe-114">서버 세션 ID를 사용 하 여 세션 데이터를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="23dbe-114">The server uses the session ID to fetch the session data.</span></span> <span data-ttu-id="23dbe-115">세션 쿠키의 브라우저 관련 이기 때문에 브라우저 세션을 공유할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="23dbe-115">Because the session cookie is specific to the browser, you cannot share sessions across browsers.</span></span> <span data-ttu-id="23dbe-116">세션 쿠키는 브라우저 세션이 끝나면 삭제 됩니다.</span><span class="sxs-lookup"><span data-stu-id="23dbe-116">Session cookies are deleted only when the browser session ends.</span></span> <span data-ttu-id="23dbe-117">쿠키가 만료 된 세션에 대 한 수신 되 면 같은 세션 쿠키를 사용 하는 새 세션 생성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="23dbe-117">If a cookie is received for an expired session, a new session that uses the same session cookie  is created.</span></span> 

<span data-ttu-id="23dbe-118">서버는 마지막 요청 이후 제한 된 시간에 대 한 세션을 유지합니다.</span><span class="sxs-lookup"><span data-stu-id="23dbe-118">The server retains a session for a limited time after the last request.</span></span> <span data-ttu-id="23dbe-119">세션 제한 시간을 설정 하거나 기본값인 20 분을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="23dbe-119">You can either set the session timeout or use the default value of 20 minutes.</span></span> <span data-ttu-id="23dbe-120">세션 상태는 영구적으로 유지 될 필요가 없습니다 있지만 특정 세션에 고유한 사용자 데이터를 저장 하는 데 이상적입니다.</span><span class="sxs-lookup"><span data-stu-id="23dbe-120">Session state is ideal for storing user data that is specific to a particular session but doesn’t need to be persisted permanently.</span></span> <span data-ttu-id="23dbe-121">데이터에서에서 삭제 됩니다 백업 저장소 중 하나를 호출할 때 `Session.Clear` 세션 데이터 저장소에서 만료 될 때 또는 합니다.</span><span class="sxs-lookup"><span data-stu-id="23dbe-121">Data is deleted from the backing store either when you call `Session.Clear` or when the session expires in the data store.</span></span> <span data-ttu-id="23dbe-122">브라우저를 닫을 때 세션 쿠키를 삭제 하는 경우 또는 서버를 알지 못합니다.</span><span class="sxs-lookup"><span data-stu-id="23dbe-122">The server does not know when the browser is closed or when the session cookie is deleted.</span></span>

> [!WARNING]
> <span data-ttu-id="23dbe-123">세션에 중요 한 데이터를 저장 하지 마십시오.</span><span class="sxs-lookup"><span data-stu-id="23dbe-123">Do not store sensitive data in session.</span></span> <span data-ttu-id="23dbe-124">클라이언트 수 하지 브라우저를 닫고 세션 쿠키의 선택을 취소 (및 일부 브라우저 세션 쿠키 활성 상태를 유지할 전체 windows).</span><span class="sxs-lookup"><span data-stu-id="23dbe-124">The client might not close the browser and clear the session cookie (and some browsers keep session cookies alive across windows).</span></span> <span data-ttu-id="23dbe-125">또한 세션; 단일 사용자로 제한 될 수 있습니다. 다음 사용자는 동일한 세션 계속 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="23dbe-125">Also, a session might not be restricted to a single user; the next user might continue with the same session.</span></span>

<span data-ttu-id="23dbe-126">메모리 내 세션 공급자는 로컬 서버에 세션 데이터를 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="23dbe-126">The in-memory session provider stores session data on the local server.</span></span> <span data-ttu-id="23dbe-127">서버 팜에서 웹 앱을 실행 하려는 경우 특정 서버에 각 세션에 연결할 고정 세션을 사용 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="23dbe-127">If you plan to run your web app on a server farm, you must use sticky sessions to tie each session to a specific server.</span></span> <span data-ttu-id="23dbe-128">Windows Azure 웹 사이트 플랫폼 고정 세션 (응용 프로그램 요청 라우팅 또는 ARR) 기본값으로 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="23dbe-128">The Windows Azure Web Sites platform defaults to sticky sessions (Application Request Routing or ARR).</span></span> <span data-ttu-id="23dbe-129">그러나는 고정 세션 확장성에 영향을 줄 수 있으며 웹 응용 프로그램 업데이트 복잡 하 게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="23dbe-129">However, sticky sessions can affect scalability and complicate web app updates.</span></span> <span data-ttu-id="23dbe-130">Redis를 사용 하는 편이 더 나은지 되었거나 SQL Server 분산 캐시는 고정 세션이 필요 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="23dbe-130">A better option is to use the Redis or SQL Server distributed caches, which don't require sticky sessions.</span></span> <span data-ttu-id="23dbe-131">자세한 내용은 참조 [분산 캐시 작업](xref:performance/caching/distributed)합니다.</span><span class="sxs-lookup"><span data-stu-id="23dbe-131">For more information, see [Working with a Distributed Cache](xref:performance/caching/distributed).</span></span> <span data-ttu-id="23dbe-132">서비스 공급자를 설정에 대 한 자세한 내용은 참조 하십시오. [세션 구성](#configuring-session) 이 문서의 뒷부분에 나오는 합니다.</span><span class="sxs-lookup"><span data-stu-id="23dbe-132">For details on setting up service providers, see [Configuring Session](#configuring-session) later in this article.</span></span>

<span data-ttu-id="23dbe-133">이 섹션의 나머지 부분에서는 사용자 데이터를 저장 하기 위한 옵션을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="23dbe-133">The remainder of this section describes the options for storing user data.</span></span>

<a name="temp"></a>
### <a name="tempdata"></a><span data-ttu-id="23dbe-134">TempData</span><span class="sxs-lookup"><span data-stu-id="23dbe-134">TempData</span></span>

<span data-ttu-id="23dbe-135">ASP.NET Core MVC 노출 된 [TempData](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.controller#Microsoft_AspNetCore_Mvc_Controller_TempData) 속성에는 [컨트롤러](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.controller)합니다.</span><span class="sxs-lookup"><span data-stu-id="23dbe-135">ASP.NET Core MVC exposes the [TempData](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.controller#Microsoft_AspNetCore_Mvc_Controller_TempData) property on a [controller](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.controller).</span></span> <span data-ttu-id="23dbe-136">이 속성은 판독될 때까지 데이터를 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="23dbe-136">This property stores data until it is read.</span></span> <span data-ttu-id="23dbe-137">`Keep` 및 `Peek` 메서드를 사용하여 삭제 없이 데이터를 검사할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="23dbe-137">The `Keep` and `Peek` methods can be used to examine the data without deletion.</span></span> <span data-ttu-id="23dbe-138">`TempData`단일 요청 보다 많이 필요한 데이터의 경우 리디렉션, 데 특히 유용 합니다.</span><span class="sxs-lookup"><span data-stu-id="23dbe-138">`TempData` is particularly useful for redirection, when data is needed for more than a single request.</span></span> <span data-ttu-id="23dbe-139">`TempData`세션 상태 위에 빌드됩니다.</span><span class="sxs-lookup"><span data-stu-id="23dbe-139">`TempData` is built on top of session state.</span></span> 

## <a name="cookie-based-tempdata-provider"></a><span data-ttu-id="23dbe-140">쿠키 기반 TempData 공급자</span><span class="sxs-lookup"><span data-stu-id="23dbe-140">Cookie-based TempData provider</span></span> 

<span data-ttu-id="23dbe-141">ASP.NET Core 1.1 이상 버전에서는 사용자의 TempData를 쿠키에 저장 하려면 TempData 쿠키 기반 공급자를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="23dbe-141">In ASP.NET Core 1.1 and higher, you can use the cookie-based TempData provider to store a user's TempData in a cookie.</span></span> <span data-ttu-id="23dbe-142">쿠키 기반 TempData 공급자를 사용 하려면 등록 된 `CookieTempDataProvider` 서비스로 `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="23dbe-142">To enable the  cookie-based TempData provider, register the `CookieTempDataProvider` service in `ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();
    // Add CookieTempDataProvider after AddMvc and include ViewFeatures.
    // using Microsoft.AspNetCore.Mvc.ViewFeatures;
    services.AddSingleton<ITempDataProvider, CookieTempDataProvider>();
}
```

<span data-ttu-id="23dbe-143">쿠키 데이터가 인코딩된는 [Base64UrlTextEncoder](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.authentication.base64urltextencoder)합니다.</span><span class="sxs-lookup"><span data-stu-id="23dbe-143">The cookie data is encoded with the [Base64UrlTextEncoder](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.authentication.base64urltextencoder).</span></span> <span data-ttu-id="23dbe-144">쿠키 암호화 되 고 청크를 때문에 단일 쿠키 크기 제한이 적용 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="23dbe-144">Because the cookie is encrypted and chunked, the single cookie size limit does not apply.</span></span> <span data-ttu-id="23dbe-145">쿠키 데이터가 압축 되지 않으며와 같은 보안 문제를 일으킬 수 encryped 데이터 압축 때문에 [범죄](https://wikipedia.org/wiki/CRIME_(security_exploit)) 및 [위반](https://wikipedia.org/wiki/BREACH_(security_exploit)) 공격입니다.</span><span class="sxs-lookup"><span data-stu-id="23dbe-145">The cookie data is not compressed, because compressing encryped data can lead to security problems such as the [CRIME](https://wikipedia.org/wiki/CRIME_(security_exploit)) and [BREACH](https://wikipedia.org/wiki/BREACH_(security_exploit)) attacks.</span></span> <span data-ttu-id="23dbe-146">쿠키 기반 TempData 공급자에 대 한 자세한 내용은 참조 하십시오. [CookieTempDataProvider](https://github.com/aspnet/Mvc/blob/dev/src/Microsoft.AspNetCore.Mvc.ViewFeatures/ViewFeatures/CookieTempDataProvider.cs)합니다.</span><span class="sxs-lookup"><span data-stu-id="23dbe-146">For more information on the cookie-based TempData provider, see [CookieTempDataProvider](https://github.com/aspnet/Mvc/blob/dev/src/Microsoft.AspNetCore.Mvc.ViewFeatures/ViewFeatures/CookieTempDataProvider.cs).</span></span>

### <a name="query-strings"></a><span data-ttu-id="23dbe-147">쿼리 문자열</span><span class="sxs-lookup"><span data-stu-id="23dbe-147">Query strings</span></span>

<span data-ttu-id="23dbe-148">새 요청 쿼리 문자열에 추가 하 여 다른 한 요청에서 제한 된 양의 데이터를 전달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="23dbe-148">You can pass a limited amount of data from one request to another by adding it to the new request’s query string.</span></span> <span data-ttu-id="23dbe-149">이에 포함 된 상태를 전자 메일 또는 소셜 네트워크를 통해 공유할 수 있는 링크를 허용 하는 영구적인 방식으로 상태를 캡처하는 데 유용 합니다.</span><span class="sxs-lookup"><span data-stu-id="23dbe-149">This is useful for capturing state in a persistent manner that allows links with embedded state to be shared through email or social networks.</span></span> <span data-ttu-id="23dbe-150">그러나 이러한 이유로 해야 사용 하지 쿼리 문자열을 중요 한 데이터에 대 한 합니다.</span><span class="sxs-lookup"><span data-stu-id="23dbe-150">However, for this reason,  you should never use query strings for sensitive data.</span></span> <span data-ttu-id="23dbe-151">뿐 아니라 쉽게 공유 되 고 쿼리 문자열에 데이터를 포함 하 여 만들 수에 대 한 기회 [교차 사이트 요청 위조 (CSRF)](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) 공격으로, 사용자가 인증 하는 동안 악성 사이트를 방문 하도록 유도할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="23dbe-151">In addition to being easily shared, including data in query strings can create opportunities for [Cross-Site Request Forgery (CSRF)](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) attacks, which can trick users into visiting malicious sites while authenticated.</span></span> <span data-ttu-id="23dbe-152">공격자가 다음 앱에서 사용자 데이터를 도용 하거나 사용자를 대신 하 여 악의적인 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="23dbe-152">Attackers can then steal user data from your app or take malicious actions on behalf of the user.</span></span> <span data-ttu-id="23dbe-153">유지 된 모든 응용 프로그램이 나 세션 상태는 CSRF 공격 으로부터 보호 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="23dbe-153">Any preserved application or session state must protect against CSRF attacks.</span></span> <span data-ttu-id="23dbe-154">CSRF 공격에 대 한 자세한 내용은 참조 하십시오. [방지 교차 사이트 요청 위조 (XSRF/CSRF) 공격에 ASP.NET Core](../security/anti-request-forgery.md)합니다.</span><span class="sxs-lookup"><span data-stu-id="23dbe-154">For more information on CSRF attacks, see [Preventing Cross-Site Request Forgery (XSRF/CSRF) Attacks in ASP.NET Core](../security/anti-request-forgery.md).</span></span>

### <a name="post-data-and-hidden-fields"></a><span data-ttu-id="23dbe-155">게시 데이터 및 숨김된 필드</span><span class="sxs-lookup"><span data-stu-id="23dbe-155">Post data and hidden fields</span></span>

<span data-ttu-id="23dbe-156">데이터 숨겨진된 양식 필드에 저장 되며 다음 요청에 다시 게시 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="23dbe-156">Data can be saved in hidden form fields and posted back on the next request.</span></span> <span data-ttu-id="23dbe-157">여러 페이지로 구성 된 양식에서 일반적입니다.</span><span class="sxs-lookup"><span data-stu-id="23dbe-157">This is common in multipage forms.</span></span> <span data-ttu-id="23dbe-158">그러나 클라이언트 잠재적으로 데이터를 변조할 수 있습니다, 때문에 서버 해야 항상 유효성 합니다.</span><span class="sxs-lookup"><span data-stu-id="23dbe-158">However, because the  client can potentially tamper with the data, the server must always revalidate it.</span></span> 

### <a name="cookies"></a><span data-ttu-id="23dbe-159">쿠키</span><span class="sxs-lookup"><span data-stu-id="23dbe-159">Cookies</span></span>

<span data-ttu-id="23dbe-160">쿠키는 웹 응용 프로그램에서 사용자 관련 데이터를 저장 하는 방법을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="23dbe-160">Cookies provide a way to store user-specific data in web applications.</span></span> <span data-ttu-id="23dbe-161">쿠키는 모든 요청과 함께 전송 되므로, 해당 크기를 최소로 유지 되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="23dbe-161">Because cookies are sent with every request, their size should be kept to a minimum.</span></span> <span data-ttu-id="23dbe-162">이상적으로 서버에 저장 된 실제 데이터와 식별자만 쿠키에 저장 되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="23dbe-162">Ideally, only an identifier should be stored in a cookie with the actual data stored on the server.</span></span> <span data-ttu-id="23dbe-163">대부분의 브라우저는 4096 바이트에 쿠키를 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="23dbe-163">Most browsers restrict cookies to 4096 bytes.</span></span> <span data-ttu-id="23dbe-164">또한 제한 된 개수의 쿠키는 각 도메인에 대해 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="23dbe-164">In addition, only a limited number of cookies are available for each domain.</span></span>  

<span data-ttu-id="23dbe-165">쿠키는 변조 될 이기 때문에 서버에서 이러한 유효성을 검사 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="23dbe-165">Because cookies are subject to tampering, they must be validated on the server.</span></span> <span data-ttu-id="23dbe-166">클라이언트에 쿠키의 지 속성 사용자 작업 및 만료 될 수 있는 경우에 클라이언트에서 데이터 지 속성 형식의 가장 영 속은 일반적으로.</span><span class="sxs-lookup"><span data-stu-id="23dbe-166">Although the durability of the cookie on a client is subject to user intervention and expiration, they are generally the most durable form of data persistence on the client.</span></span>

<span data-ttu-id="23dbe-167">쿠키는 자주 사용 개인 설정에 대 한 콘텐츠 알려진된 사용자에 대 한 사용자 지정한 합니다.</span><span class="sxs-lookup"><span data-stu-id="23dbe-167">Cookies are often used for personalization, where content is customized for a known user.</span></span> <span data-ttu-id="23dbe-168">사용자만 파악 하 고 대부분의 경우에서 인증 되지 않은, 때문에 사용자 이름, 계정 이름 또는 고유한 사용자 ID (예: GUID)를 쿠키에 저장 하 여 쿠키를 일반적으로 보호할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="23dbe-168">Because the user is only identified and not authenticated in most cases, you can typically secure a cookie by storing the user name, account name, or a unique user ID (such as a GUID) in the cookie.</span></span> <span data-ttu-id="23dbe-169">그런 다음 사이트의 사용자 개인 설정 인프라에 액세스할 쿠키를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="23dbe-169">You can then use the cookie to access the user personalization infrastructure of a site.</span></span>

### <a name="httpcontextitems"></a><span data-ttu-id="23dbe-170">HttpContext.Items</span><span class="sxs-lookup"><span data-stu-id="23dbe-170">HttpContext.Items</span></span>

<span data-ttu-id="23dbe-171">`Items` 컬렉션은 데이터를 저장 하는 좋은 위치 하나의 특정 요청을 처리 동안에 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="23dbe-171">The `Items` collection is a good location to store data that is needed only while processing one particular request.</span></span> <span data-ttu-id="23dbe-172">컬렉션의 각 요청 후 삭제 됩니다.</span><span class="sxs-lookup"><span data-stu-id="23dbe-172">The collection's contents are discarded after each request.</span></span> <span data-ttu-id="23dbe-173">`Items` 컬렉션은 가장 요청 하는 중에 다른 시점에서 작동 하 고 매개 변수를 전달할 방법이 없습니다 직접 때 통신 하도록 구성 요소 또는 미들웨어에 대 한 방법으로 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="23dbe-173">The `Items` collection is best used as a way for components or middleware to communicate when they operate at different points in time during a request and have no direct way to pass parameters.</span></span> <span data-ttu-id="23dbe-174">자세한 내용은 참조 [HttpContext.Items 작업](#working-with-httpcontextitems)이 문서의 뒷부분에 나오는 합니다.</span><span class="sxs-lookup"><span data-stu-id="23dbe-174">For more information, see [Working with HttpContext.Items](#working-with-httpcontextitems), later in this article.</span></span>

### <a name="cache"></a><span data-ttu-id="23dbe-175">캐시</span><span class="sxs-lookup"><span data-stu-id="23dbe-175">Cache</span></span>

<span data-ttu-id="23dbe-176">캐싱은 데이터 저장 및 검색 하는 효율적인 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="23dbe-176">Caching is an efficient way to store and retrieve data.</span></span> <span data-ttu-id="23dbe-177">시간 및 기타 고려 사항에 따라 캐시 된 항목의 수명을 제어할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="23dbe-177">You can control the lifetime of cached items based on time and other considerations.</span></span> <span data-ttu-id="23dbe-178">에 대 한 자세한 내용은 [캐싱](../performance/caching/index.md)합니다.</span><span class="sxs-lookup"><span data-stu-id="23dbe-178">Learn more about [Caching](../performance/caching/index.md).</span></span>

<a name=session></a>

## <a name="configuring-session"></a><span data-ttu-id="23dbe-179">세션 구성</span><span class="sxs-lookup"><span data-stu-id="23dbe-179">Configuring Session</span></span>

<span data-ttu-id="23dbe-180">`Microsoft.AspNetCore.Session` 패키지 세션 상태를 관리 하기 위한 미들웨어를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="23dbe-180">The `Microsoft.AspNetCore.Session` package provides middleware for managing session state.</span></span> <span data-ttu-id="23dbe-181">세션 미들웨어를 사용 하도록 설정 하려면 `Startup`포함 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="23dbe-181">To enable the session middleware, `Startup`must contain:</span></span>

- <span data-ttu-id="23dbe-182">중 하나는 [IDistributedCache](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.caching.distributed.idistributedcache) 메모리 캐시 합니다.</span><span class="sxs-lookup"><span data-stu-id="23dbe-182">Any of the [IDistributedCache](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.caching.distributed.idistributedcache) memory caches.</span></span> <span data-ttu-id="23dbe-183">`IDistributedCache` 구현이 세션에 대 한 백업 저장소로 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="23dbe-183">The `IDistributedCache` implementation is used as a backing store for session.</span></span>
- <span data-ttu-id="23dbe-184">[AddSession](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.dependencyinjection.sessionservicecollectionextensions#Microsoft_Extensions_DependencyInjection_SessionServiceCollectionExtensions_AddSession_Microsoft_Extensions_DependencyInjection_IServiceCollection_) 필요 "Microsoft.AspNetCore.Session" NuGet 패키지를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="23dbe-184">[AddSession](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.dependencyinjection.sessionservicecollectionextensions#Microsoft_Extensions_DependencyInjection_SessionServiceCollectionExtensions_AddSession_Microsoft_Extensions_DependencyInjection_IServiceCollection_) call, which requires NuGet package "Microsoft.AspNetCore.Session".</span></span>
- <span data-ttu-id="23dbe-185">[UseSession](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.sessionmiddlewareextensions#methods_) 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="23dbe-185">[UseSession](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.sessionmiddlewareextensions#methods_) call.</span></span>

<span data-ttu-id="23dbe-186">다음 코드에서는 메모리 내 세션 공급자를 설정 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="23dbe-186">The following code shows how to set up the in-memory session provider.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="23dbe-187">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="23dbe-187">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

[!code-csharp[Main](app-state/sample/src/WebAppSessionDotNetCore2.0App/Startup.cs?highlight=11-19,24)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="23dbe-188">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="23dbe-188">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

[!code-csharp[Main](app-state/sample/src/WebAppSession/Startup.cs?highlight=11-19,24)]

---

<span data-ttu-id="23dbe-189">세션을 참조할 수 있습니다 `HttpContext` 일단 설치 되 고 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="23dbe-189">You can reference Session from `HttpContext` once it is installed and configured.</span></span>

<span data-ttu-id="23dbe-190">액세스 하려는 경우 `Session` 전에 `UseSession` 예외 호출 된 `InvalidOperationException: Session has not been configured for this application or request` throw 됩니다.</span><span class="sxs-lookup"><span data-stu-id="23dbe-190">If you try to access `Session` before `UseSession` has been called, the exception `InvalidOperationException: Session has not been configured for this application or request` is thrown.</span></span>

<span data-ttu-id="23dbe-191">새 하려고 하면 `Session` (즉, 세션 쿠키가 없는 만든)에 쓰기를 이미 시작 된 후의 `Response` 스트림, 예외 `InvalidOperationException: The session cannot be established after the response has started` throw 됩니다.</span><span class="sxs-lookup"><span data-stu-id="23dbe-191">If you try to create a new `Session` (that is, no session cookie has been created) after you have already begun writing to the `Response` stream, the exception `InvalidOperationException: The session cannot be established after the response has started` is thrown.</span></span> <span data-ttu-id="23dbe-192">웹 서버 로그에서 예외를 확인할 수 있습니다. 브라우저에 표시 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="23dbe-192">The exception can be found in the web server log; it will not be displayed in the browser.</span></span>

### <a name="loading-session-asynchronously"></a><span data-ttu-id="23dbe-193">세션을 비동기적으로 로드</span><span class="sxs-lookup"><span data-stu-id="23dbe-193">Loading Session asynchronously</span></span> 

<span data-ttu-id="23dbe-194">기본 세션 레코드를 로드 하는 기본 세션 공급자에서 ASP.NET Core [IDistributedCache](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.caching.distributed.idistributedcache) 경우에만 비동기적으로 저장소는 [ISession.LoadAsync](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.http.isession#Microsoft_AspNetCore_Http_ISession_LoadAsync) 명시적으로 메서드는 하기 전에  `TryGetValue`, `Set`, 또는 `Remove` 메서드.</span><span class="sxs-lookup"><span data-stu-id="23dbe-194">The default session provider in ASP.NET Core loads the session record from the underlying [IDistributedCache](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.caching.distributed.idistributedcache) store asynchronously only if the [ISession.LoadAsync](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.http.isession#Microsoft_AspNetCore_Http_ISession_LoadAsync) method is explicitly called before  the `TryGetValue`, `Set`, or `Remove` methods.</span></span> <span data-ttu-id="23dbe-195">경우 `LoadAsync` 내부 먼저 호출 되지 않습니다는 영향을 줄 수 크기를 조정 하는 앱의 기능 세션 레코드를 동기적으로 로드 합니다.</span><span class="sxs-lookup"><span data-stu-id="23dbe-195">If `LoadAsync` is not called first, the underlying session record is loaded synchronously, which could potentially impact the ability of the app to scale.</span></span>

<span data-ttu-id="23dbe-196">이 패턴을 적용 하는 응용 프로그램을 래핑하는 [DistributedSessionStore](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.session.distributedsessionstore) 및 [DistributedSession](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.session.distributedsession) 경우 예외를 throw 하는 버전와 구현 된 `LoadAsync` 메서드는 없습니다 이전에 호출 `TryGetValue`, `Set`, 또는 `Remove`합니다.</span><span class="sxs-lookup"><span data-stu-id="23dbe-196">To have applications enforce this pattern, wrap the [DistributedSessionStore](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.session.distributedsessionstore) and [DistributedSession](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.session.distributedsession) implementations with versions that throw an exception if the `LoadAsync` method is not called before `TryGetValue`, `Set`, or `Remove`.</span></span> <span data-ttu-id="23dbe-197">서비스 컨테이너에 래핑된 버전을 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="23dbe-197">Register the wrapped versions in the services container.</span></span>

### <a name="implementation-details"></a><span data-ttu-id="23dbe-198">구현 세부 정보</span><span class="sxs-lookup"><span data-stu-id="23dbe-198">Implementation Details</span></span>

<span data-ttu-id="23dbe-199">세션 쿠키를 사용 하 여 추적 하 고 단일 브라우저에서 요청을 식별 합니다.</span><span class="sxs-lookup"><span data-stu-id="23dbe-199">Session uses a cookie to track and identify requests from a single browser.</span></span> <span data-ttu-id="23dbe-200">기본적으로이 쿠키 이름은 "입니다. 경로 사용 하 여 AspNet.Session"하며"/"입니다.</span><span class="sxs-lookup"><span data-stu-id="23dbe-200">By default, this cookie is named ".AspNet.Session", and it uses a path of "/".</span></span> <span data-ttu-id="23dbe-201">쿠키 기본 도메인을 지정 하지 않으므로, 것 사용할 수 없어 클라이언트 쪽 스크립트를 페이지에서 (때문에 `CookieHttpOnly` 기본값으로 `true`).</span><span class="sxs-lookup"><span data-stu-id="23dbe-201">Because the cookie default does not specify a domain, it is not made available to the client-side script on the page (because `CookieHttpOnly` defaults to `true`).</span></span>

<span data-ttu-id="23dbe-202">사용 하 여 세션 기본값을 재정의 하려면 `SessionOptions`:</span><span class="sxs-lookup"><span data-stu-id="23dbe-202">To override session defaults, use `SessionOptions`:</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="23dbe-203">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="23dbe-203">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

[!code-csharp[Main](app-state/sample/src/WebAppSessionDotNetCore2.0App/StartupCopy.cs?name=snippet1&highlight=8-12)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="23dbe-204">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="23dbe-204">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

[!code-csharp[Main](app-state/sample/src/WebAppSession/StartupCopy.cs?name=snippet1&highlight=8-12)]

---

<span data-ttu-id="23dbe-205">서버에서 사용 하는 `IdleTimeout` 기간 세션 유휴 상태일 수 있는 내용이 중단 하기 전에 확인 하는 속성입니다.</span><span class="sxs-lookup"><span data-stu-id="23dbe-205">The server uses the `IdleTimeout` property to determine how long a session can be idle before its contents are abandoned.</span></span> <span data-ttu-id="23dbe-206">이 속성은 쿠키 만료 기한 무관 합니다.</span><span class="sxs-lookup"><span data-stu-id="23dbe-206">This property is independent of the cookie expiration.</span></span> <span data-ttu-id="23dbe-207">(에서 읽기 또는 쓰기) 세션 미들웨어를 통해 전달 되는 각 요청은 제한 시간이 다시 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="23dbe-207">Each request that passes through the Session middleware (read from or written to) resets the timeout.</span></span>

<span data-ttu-id="23dbe-208">때문에 `Session` 은 *잠기지 않은*, 마지막 세션의 내용을 수정 하려고 하는 둘 다 두 요청 첫 번째 재정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="23dbe-208">Because `Session` is *non-locking*, if two requests both attempt to modify the contents of session, the last one overrides the first.</span></span> <span data-ttu-id="23dbe-209">`Session`로 구현 됩니다는 *일관 된 세션*, 즉, 모든 내용을 함께 저장 됩니다.</span><span class="sxs-lookup"><span data-stu-id="23dbe-209">`Session` is implemented as a *coherent session*, which means that all the contents are stored together.</span></span> <span data-ttu-id="23dbe-210">세션 (서로 다른 키)의 다른 부분을 수정 하는 두 요청 서로 저하 계속 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="23dbe-210">Two requests that are modifying different parts of the session (different keys) might still impact each other.</span></span>

## <a name="setting-and-getting-session-values"></a><span data-ttu-id="23dbe-211">설정 및 세션 값 가져오기</span><span class="sxs-lookup"><span data-stu-id="23dbe-211">Setting and getting Session values</span></span>

<span data-ttu-id="23dbe-212">세션을 통해 액세스는 `Session` 속성 `HttpContext`합니다.</span><span class="sxs-lookup"><span data-stu-id="23dbe-212">Session is accessed through the `Session` property on `HttpContext`.</span></span> <span data-ttu-id="23dbe-213">이 속성은 한 [ISession](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.http.isession) 구현 합니다.</span><span class="sxs-lookup"><span data-stu-id="23dbe-213">This property is an [ISession](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.http.isession) implementation.</span></span>

<span data-ttu-id="23dbe-214">다음 예제에서는 설정 및 가져오기는 int와 문자열을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="23dbe-214">The following example shows setting and getting an int and a string:</span></span>

[!code-csharp[Main](app-state/sample/src/WebAppSession/Controllers/HomeController.cs?name=snippet1)]

<span data-ttu-id="23dbe-215">다음 확장 메서드를 추가 하는 경우 설정할 수 있으며 세션에 직렬화 가능 개체를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="23dbe-215">If you add the following extension methods, you can set and get serializable objects to Session:</span></span>

[!code-csharp[Main](app-state/sample/src/WebAppSession/Extensions/SessionExtensions.cs)]

<span data-ttu-id="23dbe-216">다음 샘플에는 설정 하 고 직렬화 가능 개체를 가져오는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="23dbe-216">The following sample shows how to set and get a serializable object:</span></span>

[!code-csharp[Main](app-state/sample/src/WebAppSession/Controllers/HomeController.cs?name=snippet2)]


## <a name="working-with-httpcontextitems"></a><span data-ttu-id="23dbe-217">HttpContext.Items 작업</span><span class="sxs-lookup"><span data-stu-id="23dbe-217">Working with HttpContext.Items</span></span>

<span data-ttu-id="23dbe-218">`HttpContext` 추상화 형식의 사전 컬렉션에 대 한 지원을 제공 `IDictionary<object, object>`라는 `Items`합니다.</span><span class="sxs-lookup"><span data-stu-id="23dbe-218">The `HttpContext` abstraction provides support for a dictionary collection of type `IDictionary<object, object>`, called `Items`.</span></span> <span data-ttu-id="23dbe-219">이 컬렉션은의 시작 부분부터 사용할 수는 *HttpRequest* 은 각 요청의 끝에 삭제 됩니다.</span><span class="sxs-lookup"><span data-stu-id="23dbe-219">This collection is available from the start of an *HttpRequest* and is discarded at the end of each request.</span></span> <span data-ttu-id="23dbe-220">키가 지정 된 항목에 값을 지정 하거나 특정 키에 대 한 값을 요청 하 여 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="23dbe-220">You can access it by  assigning a value to a keyed entry, or by requesting the value for a particular key.</span></span>

<span data-ttu-id="23dbe-221">아래 샘플에서 [미들웨어](middleware.md) 추가 `isVerified` 에 `Items` 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="23dbe-221">In the sample below, [Middleware](middleware.md) adds `isVerified` to the `Items` collection.</span></span>

```csharp
app.Use(async (context, next) =>
{
    // perform some verification
    context.Items["isVerified"] = true;
    await next.Invoke();
});
```

<span data-ttu-id="23dbe-222">파이프라인의 뒷부분에 나오는 다른 미들웨어를 액세스 하기:</span><span class="sxs-lookup"><span data-stu-id="23dbe-222">Later in the pipeline, another middleware could access it:</span></span>

```csharp
app.Run(async (context) =>
{
    await context.Response.WriteAsync("Verified request? " + 
        context.Items["isVerified"]);
});
```

<span data-ttu-id="23dbe-223">단일 앱에서 사용할만 미들웨어에 대 한 `string` 키 허용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="23dbe-223">For middleware that will only be used by a single app, `string` keys are acceptable.</span></span> <span data-ttu-id="23dbe-224">그러나 응용 프로그램 간에 공유 되는 미들웨어 키가 충돌 될 가능성을 방지 하기 위해 고유한 개체 키를 사용 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="23dbe-224">However, middleware that will be shared between applications should use unique object keys to avoid any chance of key collisions.</span></span> <span data-ttu-id="23dbe-225">여러 응용 프로그램에서 사용 해야 하는 미들웨어를 개발 하는 경우 아래와 같이 미들웨어 클래스에 정의 된 개체를 고유 키를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="23dbe-225">If you are developing middleware that must work across multiple applications, use a unique object key defined in your middleware class as shown below:</span></span>

```csharp
public class SampleMiddleware
{
    public static readonly object SampleKey = new Object();

    public async Task Invoke(HttpContext httpContext)
    {
        httpContext.Items[SampleKey] = "some value";
        // additional code omitted
    }
}
```

<span data-ttu-id="23dbe-226">다른 코드에 저장 된 값에 액세스할 수 `HttpContext.Items` 미들웨어 클래스에 의해 노출 되는 키를 사용 하 여:</span><span class="sxs-lookup"><span data-stu-id="23dbe-226">Other code can access the value stored in `HttpContext.Items` using the key exposed by the middleware class:</span></span>

```csharp
public class HomeController : Controller
{
    public IActionResult Index()
    {
        string value = HttpContext.Items[SampleMiddleware.SampleKey];
    }
}
```

<span data-ttu-id="23dbe-227">이 방법은 또한 반복 되는 코드에서 여러 위치에서 "매직 문자열"를 제거 하 여 이점이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="23dbe-227">This approach also has the advantage of eliminating repetition of "magic strings" in multiple places in the code.</span></span>

<a name=appstate-errors></a>

## <a name="application-state-data"></a><span data-ttu-id="23dbe-228">응용 프로그램 상태 데이터</span><span class="sxs-lookup"><span data-stu-id="23dbe-228">Application state data</span></span>

<span data-ttu-id="23dbe-229">사용 하 여 [종속성 주입](xref:fundamentals/dependency-injection) 모든 사용자에 게 데이터를 사용할 수 있도록 하려면:</span><span class="sxs-lookup"><span data-stu-id="23dbe-229">Use [Dependency Injection](xref:fundamentals/dependency-injection) to make data available to all users:</span></span>

1. <span data-ttu-id="23dbe-230">데이터를 포함 하는 서비스 정의 (예를 들어 라는 클래스 `MyAppData`).</span><span class="sxs-lookup"><span data-stu-id="23dbe-230">Define a service containing the data (for example, a class named `MyAppData`).</span></span>

```csharp
public class MyAppData
{
    // Declare properties/methods/etc.
} 
```
2. <span data-ttu-id="23dbe-231">에 대 한 서비스 클래스를 추가 `ConfigureServices` (예를 들어 `services.AddSingleton<MyAppData>();`).</span><span class="sxs-lookup"><span data-stu-id="23dbe-231">Add the service class to `ConfigureServices` (for example `services.AddSingleton<MyAppData>();`).</span></span>
3. <span data-ttu-id="23dbe-232">각 컨트롤러에서 데이터 서비스 클래스를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="23dbe-232">Consume the data service class in each controller:</span></span>

```csharp
public class MyController : Controller
{
    public MyController(MyAppData myService)
    {
        // Do something with the service (read some data from it, 
        // store it in a private field/property, etc.)
    }
} 
```

### <a name="common-errors-when-working-with-session"></a><span data-ttu-id="23dbe-233">세션을 작업할 때 일반적인 오류</span><span class="sxs-lookup"><span data-stu-id="23dbe-233">Common errors when working with session</span></span>

* <span data-ttu-id="23dbe-234">"'Microsoft.AspNetCore.Session.DistributedSessionStore' 활성화 하는 동안 'Microsoft.Extensions.Caching.Distributed.IDistributedCache' 형식에 대 한 서비스를 확인할 수 없습니다."</span><span class="sxs-lookup"><span data-stu-id="23dbe-234">"Unable to resolve service for type 'Microsoft.Extensions.Caching.Distributed.IDistributedCache' while attempting to activate 'Microsoft.AspNetCore.Session.DistributedSessionStore'."</span></span>

  <span data-ttu-id="23dbe-235">일반적으로는 하나 이상의 구성에 실패 `IDistributedCache` 구현 합니다.</span><span class="sxs-lookup"><span data-stu-id="23dbe-235">This is usually caused by failing to configure at least one `IDistributedCache` implementation.</span></span> <span data-ttu-id="23dbe-236">자세한 내용은 참조 [분산 캐시 작업](xref:performance/caching/distributed) 및 [메모리 캐싱에](xref:performance/caching/memory)합니다.</span><span class="sxs-lookup"><span data-stu-id="23dbe-236">For more information, see [Working with a Distributed Cache](xref:performance/caching/distributed) and [In memory caching](xref:performance/caching/memory).</span></span>

### <a name="additional-resources"></a><span data-ttu-id="23dbe-237">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="23dbe-237">Additional Resources</span></span>


* [<span data-ttu-id="23dbe-238">ASP.NET Core 1.x:이 문서에 사용 되는 코드 샘플</span><span class="sxs-lookup"><span data-stu-id="23dbe-238">ASP.NET Core 1.x: Sample code used in this document</span></span>](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/app-state/sample/src/WebAppSession)
* [<span data-ttu-id="23dbe-239">ASP.NET Core 2.x:이 문서에 사용 되는 코드 샘플</span><span class="sxs-lookup"><span data-stu-id="23dbe-239">ASP.NET Core 2.x: Sample code used in this document</span></span>](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/app-state/sample/src/WebAppSessionDotNetCore2.0App)
