---
title: "다른 인증 공급자의 설문 조사 합니다."
author: rick-anderson
ms.author: riande
manager: wpickett
ms.date: 11/3/2016
ms.topic: article
ms.assetid: BC36CA84-3DE8-496E-9AA2-2F1B74AE8309
ms.prod: asp.net-core
uid: security/authentication/otherlogins
ms.openlocfilehash: 421db8c89e01cebba0c1f98cc9286288a75777f2
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/11/2017
---
# <a name="short-survey-of-other-authentication-providers"></a><span data-ttu-id="5523f-102">다른 인증 공급자의 설문 조사</span><span class="sxs-lookup"><span data-stu-id="5523f-102">Short survey of other authentication providers</span></span>

<a name=security-authentication-other-logins></a>

<span data-ttu-id="5523f-103">여 [Rick Anderson](https://twitter.com/RickAndMSFT), [Pranav Rastogi](https://github.com/rustd), 및 [Valeriy Novytskyy](https://github.com/01binary)</span><span class="sxs-lookup"><span data-stu-id="5523f-103">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Pranav Rastogi](https://github.com/rustd), and [Valeriy Novytskyy](https://github.com/01binary)</span></span>

<span data-ttu-id="5523f-104">다음은 몇 가지 다른 일반 OAuth 공급자에 대 한 지침을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="5523f-104">Here are set up instructions for some other common OAuth providers.</span></span> <span data-ttu-id="5523f-105">제 3 자 NuGet 패키지에서 유지 관리 하는 것과 같은 [aspnet contrib](https://www.nuget.org/packages?q=owners%3Aaspnet-contrib+title%3AOAuth) ASP.NET Core 팀에서 구현 된 인증 공급자를 보완 하기 위해 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5523f-105">Third-party NuGet packages such as the ones maintained by [aspnet-contrib](https://www.nuget.org/packages?q=owners%3Aaspnet-contrib+title%3AOAuth) can be used to complement authentication providers implemented by the ASP.NET Core team.</span></span>

* <span data-ttu-id="5523f-106">설정 **LinkedIn** 로그인: [https://developer.linkedin.com/my-apps](https://developer.linkedin.com/my-apps)합니다.</span><span class="sxs-lookup"><span data-stu-id="5523f-106">Set up **LinkedIn** sign in: [https://developer.linkedin.com/my-apps](https://developer.linkedin.com/my-apps).</span></span> <span data-ttu-id="5523f-107">참조 [공식 단계](https://developer.linkedin.com/docs/oauth2)합니다.</span><span class="sxs-lookup"><span data-stu-id="5523f-107">See [official steps](https://developer.linkedin.com/docs/oauth2).</span></span>

* <span data-ttu-id="5523f-108">설정 **Instagram** 로그인: [https://www.instagram.com/developer/clients/manage](https://www.instagram.com/developer/clients/manage)합니다.</span><span class="sxs-lookup"><span data-stu-id="5523f-108">Set up **Instagram** sign in: [https://www.instagram.com/developer/clients/manage](https://www.instagram.com/developer/clients/manage).</span></span> <span data-ttu-id="5523f-109">참조 [공식 단계](https://www.instagram.com/developer/authentication/)합니다.</span><span class="sxs-lookup"><span data-stu-id="5523f-109">See [official steps](https://www.instagram.com/developer/authentication/).</span></span>

* <span data-ttu-id="5523f-110">설정 **Reddit** 로그인: [https://www.reddit.com/prefs/apps](https://www.reddit.com/prefs/apps)합니다.</span><span class="sxs-lookup"><span data-stu-id="5523f-110">Set up **Reddit** sign in: [https://www.reddit.com/prefs/apps](https://www.reddit.com/prefs/apps).</span></span> <span data-ttu-id="5523f-111">참조 [공식 단계](https://github.com/reddit/reddit/wiki/OAuth2-Quick-Start-Example)합니다.</span><span class="sxs-lookup"><span data-stu-id="5523f-111">See [official steps](https://github.com/reddit/reddit/wiki/OAuth2-Quick-Start-Example).</span></span>

* <span data-ttu-id="5523f-112">설정 **Github** 로그인: [https://github.com/settings/applications/new](https://github.com/settings/applications/new)합니다.</span><span class="sxs-lookup"><span data-stu-id="5523f-112">Set up **Github** sign in: [https://github.com/settings/applications/new](https://github.com/settings/applications/new).</span></span> <span data-ttu-id="5523f-113">참조 [공식 단계](https://developer.github.com/v3/oauth/)합니다.</span><span class="sxs-lookup"><span data-stu-id="5523f-113">See [official steps](https://developer.github.com/v3/oauth/).</span></span>

* <span data-ttu-id="5523f-114">설정 **Yahoo** 로그인: [https://developer.yahoo.com/apps/create/](https://developer.yahoo.com/apps/create/)합니다.</span><span class="sxs-lookup"><span data-stu-id="5523f-114">Set up **Yahoo** sign in: [https://developer.yahoo.com/apps/create/](https://developer.yahoo.com/apps/create/).</span></span> <span data-ttu-id="5523f-115">참조 [공식 단계](https://developer.yahoo.com/bbauth/user.html)합니다.</span><span class="sxs-lookup"><span data-stu-id="5523f-115">See [official steps](https://developer.yahoo.com/bbauth/user.html).</span></span>

* <span data-ttu-id="5523f-116">설정 **Tumblr** 로그인: [https://www.tumblr.com/oauth/apps](https://www.tumblr.com/oauth/apps)합니다.</span><span class="sxs-lookup"><span data-stu-id="5523f-116">Set up **Tumblr** sign in: [https://www.tumblr.com/oauth/apps](https://www.tumblr.com/oauth/apps).</span></span> <span data-ttu-id="5523f-117">참조 [공식 단계](https://www.tumblr.com/docs/en/api/v2#auth)합니다.</span><span class="sxs-lookup"><span data-stu-id="5523f-117">See [official steps](https://www.tumblr.com/docs/en/api/v2#auth).</span></span>

* <span data-ttu-id="5523f-118">설정 **Pinterest** 로그인: [https://developers.pinterest.com/apps](https://developers.pinterest.com/apps)합니다.</span><span class="sxs-lookup"><span data-stu-id="5523f-118">Set up **Pinterest** sign in: [https://developers.pinterest.com/apps](https://developers.pinterest.com/apps).</span></span> <span data-ttu-id="5523f-119">참조 [공식 단계](https://developers.pinterest.com/docs/api/overview/?)합니다.</span><span class="sxs-lookup"><span data-stu-id="5523f-119">See [official steps](https://developers.pinterest.com/docs/api/overview/?).</span></span>

* <span data-ttu-id="5523f-120">설정 **Pocket** 로그인: [http://getpocket.com/developer/apps/new](http://getpocket.com/developer/apps/new)합니다.</span><span class="sxs-lookup"><span data-stu-id="5523f-120">Set up **Pocket** sign in: [http://getpocket.com/developer/apps/new](http://getpocket.com/developer/apps/new).</span></span> <span data-ttu-id="5523f-121">참조 [공식 단계](https://getpocket.com/developer/docs/authentication)합니다.</span><span class="sxs-lookup"><span data-stu-id="5523f-121">See [official steps](https://getpocket.com/developer/docs/authentication).</span></span>

* <span data-ttu-id="5523f-122">설정 **Flickr** 로그인: [https://www.flickr.com/services/apps/create](https://www.flickr.com/services/apps/create)합니다.</span><span class="sxs-lookup"><span data-stu-id="5523f-122">Set up **Flickr** sign in: [https://www.flickr.com/services/apps/create](https://www.flickr.com/services/apps/create).</span></span> <span data-ttu-id="5523f-123">참조 [공식 단계](https://www.flickr.com/services/api/auth.oauth.html)합니다.</span><span class="sxs-lookup"><span data-stu-id="5523f-123">See [official steps](https://www.flickr.com/services/api/auth.oauth.html).</span></span>

* <span data-ttu-id="5523f-124">설정 **Dribble** 로그인: [https://dribbble.com/signup](https://dribbble.com/signup)합니다.</span><span class="sxs-lookup"><span data-stu-id="5523f-124">Set up **Dribble** sign in: [https://dribbble.com/signup](https://dribbble.com/signup).</span></span> <span data-ttu-id="5523f-125">참조 [공식 단계](http://developer.dribbble.com/v1/oauth/)합니다.</span><span class="sxs-lookup"><span data-stu-id="5523f-125">See [official steps](http://developer.dribbble.com/v1/oauth/).</span></span>

* <span data-ttu-id="5523f-126">설정 **Vimeo** 로그인: [https://developer.vimeo.com/apps](https://developer.vimeo.com/apps)합니다.</span><span class="sxs-lookup"><span data-stu-id="5523f-126">Set up **Vimeo** sign in: [https://developer.vimeo.com/apps](https://developer.vimeo.com/apps).</span></span> <span data-ttu-id="5523f-127">참조 [공식 단계](https://developer.vimeo.com/api/authentication)합니다.</span><span class="sxs-lookup"><span data-stu-id="5523f-127">See [official steps](https://developer.vimeo.com/api/authentication).</span></span>

* <span data-ttu-id="5523f-128">설정 **SoundCloud** 로그인: [http://soundcloud.com/you/apps/new](http://soundcloud.com/you/apps/new)합니다.</span><span class="sxs-lookup"><span data-stu-id="5523f-128">Set up **SoundCloud** sign in: [http://soundcloud.com/you/apps/new](http://soundcloud.com/you/apps/new).</span></span> <span data-ttu-id="5523f-129">참조 [공식 단계](https://developers.soundcloud.com/blog/we-love-oauth-2)합니다.</span><span class="sxs-lookup"><span data-stu-id="5523f-129">See [official steps](https://developers.soundcloud.com/blog/we-love-oauth-2).</span></span>

* <span data-ttu-id="5523f-130">설정 **VK** 로그인: [https://vk.com/apps?act=manage](https://vk.com/apps?act=manage)합니다.</span><span class="sxs-lookup"><span data-stu-id="5523f-130">Set up **VK** sign in: [https://vk.com/apps?act=manage](https://vk.com/apps?act=manage).</span></span> <span data-ttu-id="5523f-131">참조 [공식 단계](https://vk.com/pages?oid=-17680044&p=Authorizing_Sites)합니다.</span><span class="sxs-lookup"><span data-stu-id="5523f-131">See [official steps](https://vk.com/pages?oid=-17680044&p=Authorizing_Sites).</span></span>
