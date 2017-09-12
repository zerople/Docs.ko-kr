---
title: "ASP.NET Core에서 기능을 요청"
author: ardalis
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: d1fbd23c-2ff9-4216-b908-0201ff3afb7c
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/request-features
ms.openlocfilehash: a10aefe3819fb03019575c36274dd164faf7086c
ms.sourcegitcommit: 9cdbfd0d670d70b9c354216aabee260c52dad5ee
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/12/2017
---
# <a name="request-features-in-aspnet-core"></a><span data-ttu-id="8f5f4-103">ASP.NET Core에서 기능을 요청</span><span class="sxs-lookup"><span data-stu-id="8f5f4-103">Request Features in ASP.NET Core</span></span>

<span data-ttu-id="8f5f4-104">으로 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="8f5f4-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="8f5f4-105">웹 HTTP 요청 및 응답에 관련 된 세부 인터페이스에 정의 된 서버 구현입니다.</span><span class="sxs-lookup"><span data-stu-id="8f5f4-105">Web server implementation details related to HTTP requests and responses are defined in interfaces.</span></span> <span data-ttu-id="8f5f4-106">이러한 인터페이스를 생성 및 호스팅 응용 프로그램의 파이프라인을 수정할 서버 구현 및 미들웨어에서 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8f5f4-106">These interfaces are used by server implementations and middleware to create and modify the application's hosting pipeline.</span></span>

## <a name="feature-interfaces"></a><span data-ttu-id="8f5f4-107">기능 인터페이스</span><span class="sxs-lookup"><span data-stu-id="8f5f4-107">Feature interfaces</span></span>

<span data-ttu-id="8f5f4-108">HTTP 기능 인터페이스의 수를 정의 하는 ASP.NET Core `Microsoft.AspNetCore.Http.Features` 는 하는 데 서버에서 지 원하는 기능을 식별 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f5f4-108">ASP.NET Core defines a number of HTTP feature interfaces in `Microsoft.AspNetCore.Http.Features` which are used by servers to identify the features they support.</span></span> <span data-ttu-id="8f5f4-109">요청을 처리 하 고 응답을 반환 하는 다음과 같은 기능 인터페이스:</span><span class="sxs-lookup"><span data-stu-id="8f5f4-109">The following feature interfaces handle requests and return responses:</span></span>

<span data-ttu-id="8f5f4-110">`IHttpRequestFeature`프로토콜, 경로, 쿼리 문자열, 헤더 및 본문을 포함 하 여 HTTP 요청을의 구조를 정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f5f4-110">`IHttpRequestFeature` Defines the structure of an HTTP request, including the protocol, path, query string, headers, and body.</span></span>

<span data-ttu-id="8f5f4-111">`IHttpResponseFeature`상태 코드, 헤더 및 응답의 본문을 포함 하 여 HTTP 응답의 구조를 정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f5f4-111">`IHttpResponseFeature` Defines the structure of an HTTP response, including the status code, headers, and body of the response.</span></span>

<span data-ttu-id="8f5f4-112">`IHttpAuthenticationFeature`에 따라 사용자를 식별 하기 위한 지원을 정의 `ClaimsPrincipal` 인증 처리기를 지정 하 고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8f5f4-112">`IHttpAuthenticationFeature` Defines support for identifying users based on a `ClaimsPrincipal` and specifying an authentication handler.</span></span>

<span data-ttu-id="8f5f4-113">`IHttpUpgradeFeature`에 대 한 지원을 정의 [HTTP 업그레이드](https://tools.ietf.org/html/rfc2616.html#section-14.42), 서버 프로토콜을 전환 하려는 경우 사용 하려는 클라이언트 프로토콜 추가 지정할 수 있도록 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f5f4-113">`IHttpUpgradeFeature` Defines support for [HTTP Upgrades](https://tools.ietf.org/html/rfc2616.html#section-14.42), which allow the client to specify which additional protocols it would like to use if the server wishes to switch protocols.</span></span>

<span data-ttu-id="8f5f4-114">`IHttpBufferingFeature`요청 및/또는 응답 버퍼링을 사용 하지 않도록 설정 하기 위한 메서드를 정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f5f4-114">`IHttpBufferingFeature` Defines methods for disabling buffering of requests and/or responses.</span></span>

<span data-ttu-id="8f5f4-115">`IHttpConnectionFeature`로컬 및 원격 주소 및 포트에 대 한 속성을 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="8f5f4-115">`IHttpConnectionFeature` Defines properties for local and remote addresses and ports.</span></span>

<span data-ttu-id="8f5f4-116">`IHttpRequestLifetimeFeature`연결을 중지 하거나 검색 하는 경우 요청이 갑자기 중단 등으로가 종료 클라이언트 연결 끊기에 대 한 지원을 정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f5f4-116">`IHttpRequestLifetimeFeature` Defines support for aborting connections, or detecting if a request has been terminated prematurely, such as by a client disconnect.</span></span>

<span data-ttu-id="8f5f4-117">`IHttpSendFileFeature`비동기적으로 파일을 보내는 방법을 정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f5f4-117">`IHttpSendFileFeature` Defines a method for sending files asynchronously.</span></span>

<span data-ttu-id="8f5f4-118">`IHttpWebSocketFeature`Websocket을 지원 하기 위한 API를 정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f5f4-118">`IHttpWebSocketFeature` Defines an API for supporting web sockets.</span></span>

<span data-ttu-id="8f5f4-119">`IHttpRequestIdentifierFeature`요청을 고유 하 게 식별 하는 구현 될 수 있는 속성을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f5f4-119">`IHttpRequestIdentifierFeature` Adds a property that can be implemented to uniquely identify requests.</span></span>

<span data-ttu-id="8f5f4-120">`ISessionFeature`정의 `ISessionFactory` 및 `ISession` 사용자 세션을 지원 하기 위한 추상화입니다.</span><span class="sxs-lookup"><span data-stu-id="8f5f4-120">`ISessionFeature` Defines `ISessionFactory` and `ISession` abstractions for supporting user sessions.</span></span>

<span data-ttu-id="8f5f4-121">`ITlsConnectionFeature`클라이언트 인증서를 검색 하기 위한 API를 정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f5f4-121">`ITlsConnectionFeature` Defines an API for retrieving client certificates.</span></span>

<span data-ttu-id="8f5f4-122">`ITlsTokenBindingFeature`TLS 토큰 바인딩 매개 변수를 사용 하기 위한 메서드를 정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f5f4-122">`ITlsTokenBindingFeature` Defines methods for working with TLS token binding parameters.</span></span>

> [!NOTE]
> <span data-ttu-id="8f5f4-123">`ISessionFeature`서버 기능이 아니지만 의해 구현 되는 `SessionMiddleware` (참조 [관리 응용 프로그램 상태](app-state.md)).</span><span class="sxs-lookup"><span data-stu-id="8f5f4-123">`ISessionFeature` is not a server feature, but is implemented by the `SessionMiddleware` (see [Managing Application State](app-state.md)).</span></span>

## <a name="feature-collections"></a><span data-ttu-id="8f5f4-124">컬렉션 기능</span><span class="sxs-lookup"><span data-stu-id="8f5f4-124">Feature collections</span></span>

<span data-ttu-id="8f5f4-125">`Features` 속성 `HttpContext` 가져오고 현재 요청에 대 한 사용 가능한 HTTP 기능을 설정 하기 위한 인터페이스를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f5f4-125">The `Features` property of `HttpContext` provides an interface for getting and setting the available HTTP features for the current request.</span></span> <span data-ttu-id="8f5f4-126">요청 컨텍스트 내 에서도 변경할 수 있는 기능 컬렉션 이므로 미들웨어 데 사용할 수는 컬렉션을 수정 하 고 추가 기능에 대 한 지원을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f5f4-126">Since the feature collection is mutable even within the context of a request, middleware can be used to modify the collection and add support for additional features.</span></span>

## <a name="middleware-and-request-features"></a><span data-ttu-id="8f5f4-127">미들웨어 및 요청 기능</span><span class="sxs-lookup"><span data-stu-id="8f5f4-127">Middleware and request features</span></span>

<span data-ttu-id="8f5f4-128">서버 기능 컬렉션을 만드는 책임이 상태인 미들웨어 수 모두이 컬렉션에 추가할 및 컬렉션에서 기능을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f5f4-128">While servers are responsible for creating the feature collection, middleware can both add to this collection and consume features from the collection.</span></span> <span data-ttu-id="8f5f4-129">예를 들어는 `StaticFileMiddleware` 액세스는 `IHttpSendFileFeature` 기능입니다.</span><span class="sxs-lookup"><span data-stu-id="8f5f4-129">For example, the `StaticFileMiddleware` accesses the `IHttpSendFileFeature` feature.</span></span> <span data-ttu-id="8f5f4-130">기능이 설치 된 경우, 실제 경로가에서 요청된 된 정적 파일을 보낼 수 ´ ù.</span><span class="sxs-lookup"><span data-stu-id="8f5f4-130">If the feature exists, it is used to send the requested static file from its physical path.</span></span> <span data-ttu-id="8f5f4-131">그렇지는 더 느린 대체 방법은 파일을 보내려면 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8f5f4-131">Otherwise, a slower alternative method is used to send the file.</span></span> <span data-ttu-id="8f5f4-132">사용 가능한 경우는 `IHttpSendFileFeature` 운영 시스템이 직접 커널 모드 네트워크 카드에 복사를 수행 하 고 파일을 열 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8f5f4-132">When available, the `IHttpSendFileFeature` allows the operating system to open the file and perform a direct kernel mode copy to the network card.</span></span>

<span data-ttu-id="8f5f4-133">또한 미들웨어는 서버에서 설정한 기능 컬렉션에 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8f5f4-133">Additionally, middleware can add to the feature collection established by the server.</span></span> <span data-ttu-id="8f5f4-134">도 서버의 기능을 보완할 미들웨어 허용 미들웨어에서 기존 기능을 바꿀 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8f5f4-134">Existing features can even be replaced by middleware, allowing the middleware to augment the functionality of the server.</span></span> <span data-ttu-id="8f5f4-135">컬렉션에 추가 기능은 다른 미들웨어 또는 내부 응용 프로그램 자체 요청 파이프라인의 뒷부분에 나오는에 즉시 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8f5f4-135">Features added to the collection are available immediately to other middleware or the underlying application itself later in the request pipeline.</span></span>

<span data-ttu-id="8f5f4-136">사용자 지정 서버 구현 및 특정 미들웨어의 향상 된 기능을 결합 함으로써 정확한 응용 프로그램에 필요한 기능 집합을 생성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8f5f4-136">By combining custom server implementations and specific middleware enhancements, the precise set of features an application requires can be constructed.</span></span> <span data-ttu-id="8f5f4-137">그러면 누락 된 서버에서 변경 하지 않고도 추가할 기능 및 보장 공격을 제한할 수는 최소한의 기능만 노출 되 영역 및 성능 향상을 노출 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f5f4-137">This allows missing features to be added without requiring a change in server, and ensures only the minimal amount of features are exposed, thus limiting attack surface area and improving performance.</span></span>

## <a name="summary"></a><span data-ttu-id="8f5f4-138">요약</span><span class="sxs-lookup"><span data-stu-id="8f5f4-138">Summary</span></span>

<span data-ttu-id="8f5f4-139">기능 인터페이스를 지 원하는 지정된 된 요청 특정 HTTP 기능 정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f5f4-139">Feature interfaces define specific HTTP features that a given request may support.</span></span> <span data-ttu-id="8f5f4-140">서버 기능, 컬렉션 및 해당 서버에서 지 원하는 기능의 초기 집합을 정의 하지만 미들웨어를 사용 하 여 이러한 기능을 향상 시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8f5f4-140">Servers define collections of features, and the initial set of features supported by that server, but middleware can be used to enhance these features.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8f5f4-141">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="8f5f4-141">Additional Resources</span></span>

* [<span data-ttu-id="8f5f4-142">서버</span><span class="sxs-lookup"><span data-stu-id="8f5f4-142">Servers</span></span>](servers/index.md)

* [<span data-ttu-id="8f5f4-143">미들웨어</span><span class="sxs-lookup"><span data-stu-id="8f5f4-143">Middleware</span></span>](middleware.md)

* [<span data-ttu-id="8f5f4-144">OWIN(Open Web Interface for .NET)</span><span class="sxs-lookup"><span data-stu-id="8f5f4-144">Open Web Interface for .NET (OWIN)</span></span>](owin.md)
