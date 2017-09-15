---
title: "ASP.NET Core 소개"
author: rick-anderson
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 08/03/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: index
ms.openlocfilehash: 10831719630bc638ce2f7424f53548518868d433
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/11/2017
---
# <a name="introduction-to-aspnet-core"></a><span data-ttu-id="1a3ea-103">ASP.NET Core 소개</span><span class="sxs-lookup"><span data-stu-id="1a3ea-103">Introduction to ASP.NET Core</span></span>

<span data-ttu-id="1a3ea-104">작성자: [Daniel Roth](https://github.com/danroth27), [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [Shaun Luttin](https://twitter.com/dicshaunary)</span><span class="sxs-lookup"><span data-stu-id="1a3ea-104">By [Daniel Roth](https://github.com/danroth27), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Shaun Luttin](https://twitter.com/dicshaunary)</span></span>

<span data-ttu-id="1a3ea-105">ASP.NET Core는 클라우드 기반 인터넷에 연결된 최신 응용 프로그램을 빌드하기 위한 플랫폼 간 고성능 [오픈 소스](https://github.com/aspnet/home) 프레임워크입니다.</span><span class="sxs-lookup"><span data-stu-id="1a3ea-105">ASP.NET Core is a cross-platform, high-performance, [open-source](https://github.com/aspnet/home) framework for building modern, cloud-based, Internet-connected applications.</span></span> <span data-ttu-id="1a3ea-106">ASP.NET Core를 사용하면 다음과 같은 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1a3ea-106">With ASP.NET Core, you can:</span></span>

* <span data-ttu-id="1a3ea-107">웹앱 및 서비스, IoT 앱 및 모바일 백 엔드를 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="1a3ea-107">Build web apps and services, IoT apps, and mobile backends.</span></span>
* <span data-ttu-id="1a3ea-108">Windows, macOS 및 Linux에서 즐겨 찾는 개발 도구를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="1a3ea-108">Use your favorite development tools on Windows, macOS, and Linux.</span></span>
* <span data-ttu-id="1a3ea-109">클라우드 또는 온-프레미스에 배포</span><span class="sxs-lookup"><span data-stu-id="1a3ea-109">Deploy to the cloud or on-premises</span></span>
* <span data-ttu-id="1a3ea-110">[.NET Core 또는.NET Framework](https://docs.microsoft.com/dotnet/articles/standard/choosing-core-framework-server)를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="1a3ea-110">Run on [.NET Core or .NET Framework](https://docs.microsoft.com/dotnet/articles/standard/choosing-core-framework-server).</span></span>

## <a name="why-use-aspnet-core"></a><span data-ttu-id="1a3ea-111">ASP.NET Core를 사용하는 이유는 무엇인가요?</span><span class="sxs-lookup"><span data-stu-id="1a3ea-111">Why use ASP.NET Core?</span></span>

<span data-ttu-id="1a3ea-112">수백만 명의 개발자가 ASP.NET을 사용하여 웹 앱을 만들었습니다(계속 사용 중).</span><span class="sxs-lookup"><span data-stu-id="1a3ea-112">Millions of developers have used ASP.NET (and continue to use it) to create web apps.</span></span> <span data-ttu-id="1a3ea-113">ASP.NET Core는 간결한 모듈식 프레임워크를 만드는 아키텍처 변경 내용을 포함한 ASP.NET의 새로운 디자인입니다.</span><span class="sxs-lookup"><span data-stu-id="1a3ea-113">ASP.NET Core is a redesign of ASP.NET, with architectural changes that result in a leaner and modular framework.</span></span>

<span data-ttu-id="1a3ea-114">ASP.NET Core는 다음과 같은 이점을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="1a3ea-114">ASP.NET Core provides the following benefits:</span></span>

* <span data-ttu-id="1a3ea-115">웹 UI 및 웹 API를 동일한 과정으로 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="1a3ea-115">A unified story for building web UI and web APIs.</span></span>
* <span data-ttu-id="1a3ea-116">[최신 클라이언트 쪽 프레임워크](xref:client-side/index) 및 워크플로 개발을 통합합니다.</span><span class="sxs-lookup"><span data-stu-id="1a3ea-116">Integration of [modern client-side frameworks](xref:client-side/index) and development workflows.</span></span>
* <span data-ttu-id="1a3ea-117">클라우드를 갖춘 환경 기반 [구성 시스템](xref:fundamentals/configuration)입니다.</span><span class="sxs-lookup"><span data-stu-id="1a3ea-117">A cloud-ready, environment-based [configuration system](xref:fundamentals/configuration).</span></span>
* <span data-ttu-id="1a3ea-118">[종속성 주입](xref:fundamentals/dependency-injection)이 기본 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="1a3ea-118">Built-in [dependency injection](xref:fundamentals/dependency-injection).</span></span>
* <span data-ttu-id="1a3ea-119">간단한 고성능 모듈식 HTTP 요청 파이프라인을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="1a3ea-119">A lightweight, high-performance, and modular HTTP request pipeline.</span></span>
* <span data-ttu-id="1a3ea-120">IIS에서 호스트하거나 고유한 프로세스에서 자체 호스팅하는 기능이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1a3ea-120">Ability to host on IIS or self-host in your own process.</span></span>
* <span data-ttu-id="1a3ea-121">[.NET Core](https://docs.microsoft.com/dotnet/articles/standard/choosing-core-framework-server)를 실행하여 진정한 병렬 응용 프로그램 버전 관리를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="1a3ea-121">Can run on [.NET Core](https://docs.microsoft.com/dotnet/articles/standard/choosing-core-framework-server), which supports true side-by-side app versioning.</span></span>
* <span data-ttu-id="1a3ea-122">최신 웹 개발을 간소화하는 도구를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="1a3ea-122">Tooling that simplifies modern web development.</span></span>
* <span data-ttu-id="1a3ea-123">Windows, macOS 및 Linux에서 빌드하고 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1a3ea-123">Ability to build and run on Windows, macOS, and Linux.</span></span>
* <span data-ttu-id="1a3ea-124">오픈 소스이며 커뮤니티에 중점을 둡니다.</span><span class="sxs-lookup"><span data-stu-id="1a3ea-124">Open-source and community-focused.</span></span>

<span data-ttu-id="1a3ea-125">ASP.NET Core는 완전히 [NuGet](https://nuget.org) 패키지로 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="1a3ea-125">ASP.NET Core ships entirely as [NuGet](https://nuget.org) packages.</span></span> <span data-ttu-id="1a3ea-126">그러면 필요한 NuGet 패키지를 포함하도록 앱을 최적화할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1a3ea-126">This allows you to optimize your app to include just the NuGet packages you need.</span></span> <span data-ttu-id="1a3ea-127">작은 앱 노출 영역의 혜택에는 보안 강화, 서비스 절감, 성능 향상이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="1a3ea-127">The benefits of a smaller app surface area include tighter security, reduced servicing, and improved performance.</span></span>

## <a name="build-web-apis-and-web-ui-using-aspnet-core-mvc"></a><span data-ttu-id="1a3ea-128">ASP.NET Core MVC를 사용하여 웹 API 및 웹 UI 빌드</span><span class="sxs-lookup"><span data-stu-id="1a3ea-128">Build web APIs and web UI using ASP.NET Core MVC</span></span>

<span data-ttu-id="1a3ea-129">ASP.NET Core MVC에서는 [웹 API](xref:tutorials/index#building-web-apis) 및 [웹앱](xref:tutorials/index#building-web-applications)을 빌드하는 데 유용한 기능을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="1a3ea-129">ASP.NET Core MVC provides features that help you build [web APIs](xref:tutorials/index#building-web-apis) and [web apps](xref:tutorials/index#building-web-applications):</span></span>

* <span data-ttu-id="1a3ea-130">[MVC(모델-뷰-컨트롤러) 패턴](xref:mvc/overview)을 통해 웹 API 및 웹앱을 [테스트 가능](testing/index.md)하게 합니다.</span><span class="sxs-lookup"><span data-stu-id="1a3ea-130">The [Model-View-Controller (MVC) pattern](xref:mvc/overview) helps make your web APIs and web apps [testable](testing/index.md).</span></span>
* <span data-ttu-id="1a3ea-131">[Razor 페이지](xref:mvc/razor-pages/index)(2.0의 새로운 기능)는 웹 UI를 쉽게 빌드하고 생산성을 높일 수 있는 페이지 기반 프로그래밍 모델합니다.</span><span class="sxs-lookup"><span data-stu-id="1a3ea-131">[Razor Pages](xref:mvc/razor-pages/index) (new in 2.0) is a page-based programming model that makes building web UI easier and more productive.</span></span>
* <span data-ttu-id="1a3ea-132">[Razor 구문](xref:mvc/views/razor)은 [Razor 페이지](xref:mvc/razor-pages/index) 및 [MVC 뷰](xref:mvc/views/overview)에 생산적인 언어를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="1a3ea-132">[Razor syntax](xref:mvc/views/razor) provides a productive language for [Razor Pages](xref:mvc/razor-pages/index) and [MVC Views](xref:mvc/views/overview).</span></span>
* <span data-ttu-id="1a3ea-133">[태그 도우미](xref:mvc/views/tag-helpers/intro)를 사용하면 서버 쪽 코드를 Razor 파일에서 HTML 요소를 만들고 렌더링하는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1a3ea-133">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span>
* <span data-ttu-id="1a3ea-134">[여러 데이터 형식 및 콘텐츠 협상](mvc/models/formatting.md)에 대한 기본 제공 지원을 통해 웹 API를 브라우저 및 모바일 장치를 포함한 다양한 클라이언트에 연결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1a3ea-134">Built-in support for [multiple data formats and content negotiation](mvc/models/formatting.md) lets your web APIs reach a broad range of clients, including browsers and mobile devices.</span></span>
* <span data-ttu-id="1a3ea-135">[모델 바인딩](xref:mvc/models/model-binding)은 작업 메서드 매개 변수에 HTTP 요청의 데이터를 자동으로 매핑합니다.</span><span class="sxs-lookup"><span data-stu-id="1a3ea-135">[Model Binding](xref:mvc/models/model-binding) automatically maps data from HTTP requests to action method parameters.</span></span>
* <span data-ttu-id="1a3ea-136">[유효성 검사 모델](xref:mvc/models/validation)은 자동으로 클라이언트와 서버 쪽 유효성 검사를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="1a3ea-136">[Model Validation](xref:mvc/models/validation) automatically performs client and server-side validation.</span></span>

## <a name="client-side-development"></a><span data-ttu-id="1a3ea-137">클라이언트 쪽 개발</span><span class="sxs-lookup"><span data-stu-id="1a3ea-137">Client-side development</span></span>

<span data-ttu-id="1a3ea-138">ASP.NET Core는 [AngularJS](xref:client-side/angular), [KnockoutJS](xref:client-side/knockout) 및 [부트스트랩](xref:client-side/bootstrap)을 비롯한 다양한 클라이언트 쪽 프레임워크와 원활하게 통합되도록 설계되었습니다.</span><span class="sxs-lookup"><span data-stu-id="1a3ea-138">ASP.NET Core is designed to integrate seamlessly with a variety of client-side frameworks, including [AngularJS](xref:client-side/angular), [KnockoutJS](xref:client-side/knockout), and [Bootstrap](xref:client-side/bootstrap).</span></span> <span data-ttu-id="1a3ea-139">자세한 내용은 [클라이언트 쪽 개발](client-side/index.md)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="1a3ea-139">See [Client-side development](client-side/index.md) for more details.</span></span>

## <a name="next-steps"></a><span data-ttu-id="1a3ea-140">다음 단계</span><span class="sxs-lookup"><span data-stu-id="1a3ea-140">Next steps</span></span>

<span data-ttu-id="1a3ea-141">자세한 내용은 다음 리소스를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="1a3ea-141">For more information, see the following resources:</span></span>

* [<span data-ttu-id="1a3ea-142">ASP.NET Core 자습서</span><span class="sxs-lookup"><span data-stu-id="1a3ea-142">ASP.NET Core tutorials</span></span>](xref:tutorials/index)
* [<span data-ttu-id="1a3ea-143">ASP.NET Core 기본 사항</span><span class="sxs-lookup"><span data-stu-id="1a3ea-143">ASP.NET Core fundamentals</span></span>](xref:fundamentals/index)
* <span data-ttu-id="1a3ea-144">[매주 ASP.NET 커뮤니티 스탠드업](https://live.asp.net/)은 팀의 진행률 및 계획을 다루고 새 블로그 및 타사 소프트웨어를 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="1a3ea-144">[The weekly ASP.NET community standup](https://live.asp.net/) covers the team's progress and plans and features new blogs and third-party software.</span></span>
