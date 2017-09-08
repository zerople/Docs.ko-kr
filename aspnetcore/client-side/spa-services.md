---
title: "단일 페이지 응용 프로그램을 만들기 위한 JavaScriptServices를 사용 하 여"
author: scottaddie
description: "단일 페이지 응용 프로그램 (SPA) 뒷받침 되며 ASP.NET Core 만들려는 JavaScriptServices 사용의 이점에 알아봅니다."
keywords: "ASP.NET Core 각도, SPA, JavaScriptServices, SpaServices"
ms.author: scaddie
manager: wpickett
ms.date: 08/02/2017
ms.topic: article
ms.assetid: 4b30576b-2718-4c39-9253-a59966747893
ms.technology: aspnet
ms.prod: asp.net-core
uid: client-side/spa-services
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 300e90912a03980d1dcde2edaf34677d80cab136
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/11/2017
---
# <a name="using-javascriptservices-for-creating-single-page-applications-with-aspnet-core"></a><span data-ttu-id="26870-104">ASP.NET Core 사용 단일 페이지 응용 프로그램을 만들기 위한 JavaScriptServices를 사용 하 여</span><span class="sxs-lookup"><span data-stu-id="26870-104">Using JavaScriptServices for Creating Single Page Applications with ASP.NET Core</span></span>

<span data-ttu-id="26870-105">여 [Scott Addie](https://github.com/scottaddie) 및 [Fiyaz Hasan](http://fiyazhasan.me/)</span><span class="sxs-lookup"><span data-stu-id="26870-105">By [Scott Addie](https://github.com/scottaddie) and [Fiyaz Hasan](http://fiyazhasan.me/)</span></span>

<span data-ttu-id="26870-106">단일 페이지 응용 프로그램 (SPA)에 내재 된 풍부한 사용자 환경이 때문에 웹 응용 프로그램의 인기 있는 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="26870-106">A Single Page Application (SPA) is a popular type of web application due to its inherent rich user experience.</span></span> <span data-ttu-id="26870-107">와 같은 클라이언트 쪽 SPA 프레임 워크 또는 라이브러리가, 통합 [각](https://angular.io/) 또는 [반응](https://facebook.github.io/react/), ASP.NET Core 것은 어려울 수와 같은 서버 쪽 프레임 워크입니다.</span><span class="sxs-lookup"><span data-stu-id="26870-107">Integrating client-side SPA frameworks or libraries, such as [Angular](https://angular.io/) or [React](https://facebook.github.io/react/), with server-side frameworks like ASP.NET Core can be difficult.</span></span> <span data-ttu-id="26870-108">[JavaScriptServices](https://github.com/aspnet/JavaScriptServices) 통합 프로세스에서 충돌을 줄이기 위해 개발 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="26870-108">[JavaScriptServices](https://github.com/aspnet/JavaScriptServices) was developed to reduce friction in the integration process.</span></span> <span data-ttu-id="26870-109">다른 클라이언트와 서버 기술 스택 간에 원활 하 게 작업 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="26870-109">It enables seamless operation between the different client and server technology stacks.</span></span>

[<span data-ttu-id="26870-110">샘플 코드 보기 또는 다운로드</span><span class="sxs-lookup"><span data-stu-id="26870-110">View or download sample code</span></span>](https://github.com/aspnet/Docs/tree/master/aspnetcore/client-side/spa-services/sample)

<a name="what-is-js-services"></a>

## <a name="what-is-javascriptservices"></a><span data-ttu-id="26870-111">JavaScriptServices 란?</span><span class="sxs-lookup"><span data-stu-id="26870-111">What is JavaScriptServices?</span></span>

<span data-ttu-id="26870-112">JavaScriptServices은 ASP.NET Core에 대 한 클라이언트 쪽 기술과의 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="26870-112">JavaScriptServices is a collection of client-side technologies for ASP.NET Core.</span></span> <span data-ttu-id="26870-113">목표는 ASP.NET Core SPAs를 구축 하기 위한 개발자의 기본 서버 쪽 플랫폼으로 위치를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="26870-113">Its goal is to position ASP.NET Core as developers' preferred server-side platform for building SPAs.</span></span>

<span data-ttu-id="26870-114">JavaScriptServices 세 가지 고유한 NuGet 패키지 이루어져 있습니다.</span><span class="sxs-lookup"><span data-stu-id="26870-114">JavaScriptServices consists of three distinct NuGet packages:</span></span>
* <span data-ttu-id="26870-115">[Microsoft.AspNetCore.NodeServices](http://www.nuget.org/packages/Microsoft.AspNetCore.NodeServices/) (NodeServices)</span><span class="sxs-lookup"><span data-stu-id="26870-115">[Microsoft.AspNetCore.NodeServices](http://www.nuget.org/packages/Microsoft.AspNetCore.NodeServices/) (NodeServices)</span></span>
* <span data-ttu-id="26870-116">[Microsoft.AspNetCore.SpaServices](http://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) (SpaServices)</span><span class="sxs-lookup"><span data-stu-id="26870-116">[Microsoft.AspNetCore.SpaServices](http://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) (SpaServices)</span></span>
* <span data-ttu-id="26870-117">[Microsoft.AspNetCore.SpaTemplates](http://www.nuget.org/packages/Microsoft.AspNetCore.SpaTemplates/) (SpaTemplates)</span><span class="sxs-lookup"><span data-stu-id="26870-117">[Microsoft.AspNetCore.SpaTemplates](http://www.nuget.org/packages/Microsoft.AspNetCore.SpaTemplates/) (SpaTemplates)</span></span>

<span data-ttu-id="26870-118">이러한 패키지는 유용한 경우 있습니다.</span><span class="sxs-lookup"><span data-stu-id="26870-118">These packages are useful if you:</span></span>
* <span data-ttu-id="26870-119">서버에서 JavaScript를 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="26870-119">Run JavaScript on the server</span></span>
* <span data-ttu-id="26870-120">SPA 프레임 워크 또는 라이브러리 사용</span><span class="sxs-lookup"><span data-stu-id="26870-120">Use a SPA framework or library</span></span>
* <span data-ttu-id="26870-121">시스템용을 사용 하 여 클라이언트 쪽 자산 빌드</span><span class="sxs-lookup"><span data-stu-id="26870-121">Build client-side assets with Webpack</span></span>

<span data-ttu-id="26870-122">이 문서에 포커스를 많이 SpaServices 패키지를 사용 하 여에 배치 됩니다.</span><span class="sxs-lookup"><span data-stu-id="26870-122">Much of the focus in this article is placed on using the SpaServices package.</span></span>

<a name="what-is-spa-services"></a>

## <a name="what-is-spaservices"></a><span data-ttu-id="26870-123">SpaServices 란?</span><span class="sxs-lookup"><span data-stu-id="26870-123">What is SpaServices?</span></span>

<span data-ttu-id="26870-124">ASP.NET Core SPAs를 구축 하기 위한 개발자의 기본 서버 쪽 플랫폼으로 배치 하려면 SpaServices 만들어졌습니다.</span><span class="sxs-lookup"><span data-stu-id="26870-124">SpaServices was created to position ASP.NET Core as developers' preferred server-side platform for building SPAs.</span></span> <span data-ttu-id="26870-125">SpaServices ASP.NET 코어 SPAs 개발할 필요가 없습니다 및 특정 클라이언트 프레임 워크에 고정 하지 않는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="26870-125">SpaServices is not required to develop SPAs with ASP.NET Core, and it doesn't lock you into a particular client framework.</span></span>

<span data-ttu-id="26870-126">SpaServices와 같은 유용한 인프라를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="26870-126">SpaServices provides useful infrastructure such as:</span></span>
* [<span data-ttu-id="26870-127">서버 쪽 사전 렌더링이</span><span class="sxs-lookup"><span data-stu-id="26870-127">Server-side prerendering</span></span>](#server-prerendering)
* [<span data-ttu-id="26870-128">시스템용 Dev 미들웨어입니다.</span><span class="sxs-lookup"><span data-stu-id="26870-128">Webpack Dev Middleware</span></span>](#webpack-dev-middleware)
* [<span data-ttu-id="26870-129">핫 모듈 교체</span><span class="sxs-lookup"><span data-stu-id="26870-129">Hot Module Replacement</span></span>](#hot-module-replacement)
* [<span data-ttu-id="26870-130">라우팅 도우미</span><span class="sxs-lookup"><span data-stu-id="26870-130">Routing helpers</span></span>](#routing-helpers)

<span data-ttu-id="26870-131">전체적으로, 이러한 인프라 구성 요소 개발 워크플로 및 런타임 환경을 모두 향상 합니다.</span><span class="sxs-lookup"><span data-stu-id="26870-131">Collectively, these infrastructure components enhance both the development workflow and the runtime experience.</span></span> <span data-ttu-id="26870-132">구성 요소를 개별적으로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="26870-132">The components can be adopted individually.</span></span>

<a name="spa-services-prereqs"></a>

## <a name="prerequisites-for-using-spaservices"></a><span data-ttu-id="26870-133">SpaServices 사용 하기 위한 필수 구성 요소</span><span class="sxs-lookup"><span data-stu-id="26870-133">Prerequisites for using SpaServices</span></span>

<span data-ttu-id="26870-134">SpaServices를 사용 하려면 다음을 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="26870-134">To work with SpaServices, install the following:</span></span>
* <span data-ttu-id="26870-135">[Node.js](https://nodejs.org/) (버전 6) npm와</span><span class="sxs-lookup"><span data-stu-id="26870-135">[Node.js](https://nodejs.org/) (version 6 or later) with npm</span></span>
    * <span data-ttu-id="26870-136">이러한 구성 요소 설치 되 고 있습니다를 확인 하려면 명령줄에서 다음을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="26870-136">To verify these components are installed and can be found, run the following from the command line:</span></span>

    ```console
    node -v && npm -v
    ```

<span data-ttu-id="26870-137">참고: Azure 웹 사이트를 배포 하는 경우 않아도 합니까 여기 &mdash; Node.js 설치 되어 서버 환경에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="26870-137">Note: If you're deploying to an Azure web site, you don't need to do anything here &mdash; Node.js is installed and available in the server environments.</span></span>

* <span data-ttu-id="26870-138">[.NET core SDK](https://www.microsoft.com/net/download/core) 1.0 (이상)</span><span class="sxs-lookup"><span data-stu-id="26870-138">[.NET Core SDK](https://www.microsoft.com/net/download/core) 1.0 (or later)</span></span>
    * <span data-ttu-id="26870-139">Windows를 사용 하는 경우 설치 Visual Studio 2017을 선택 하 여 **.NET Core 플랫폼 간 개발** 작업 합니다.</span><span class="sxs-lookup"><span data-stu-id="26870-139">If you're on Windows, this can be installed by selecting Visual Studio 2017's **.NET Core cross-platform development** workload.</span></span>

* <span data-ttu-id="26870-140">[Microsoft.AspNetCore.SpaServices](http://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) NuGet 패키지</span><span class="sxs-lookup"><span data-stu-id="26870-140">[Microsoft.AspNetCore.SpaServices](http://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) NuGet package</span></span>

<a name="server-prerendering"></a>

## <a name="server-side-prerendering"></a><span data-ttu-id="26870-141">서버 쪽 사전 렌더링이</span><span class="sxs-lookup"><span data-stu-id="26870-141">Server-side prerendering</span></span>

<span data-ttu-id="26870-142">범용 (해 라고도 함) 응용 프로그램은 JavaScript 응용 프로그램 서버와 클라이언트 둘 다 실행 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="26870-142">A universal (also known as isomorphic) application is a JavaScript application capable of running both on the server and the client.</span></span> <span data-ttu-id="26870-143">각, React, 및 기타 인기 있는 프레임 워크가 응용 프로그램 개발 스타일에 대 한 유니버설 플랫폼을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="26870-143">Angular, React, and other popular frameworks provide a universal platform for this application development style.</span></span> <span data-ttu-id="26870-144">개념은 먼저 Node.js 통해 서버에서 프레임 워크 구성 요소를 렌더링 하 고 클라이언트에 실행 한 다음 위임 추가 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="26870-144">The idea is to first render the framework components on the server via Node.js, and then delegate further execution to the client.</span></span>

<span data-ttu-id="26870-145">ASP.NET Core [태그 도우미](xref:mvc/views/tag-helpers/intro) 제공한 SpaServices 서버에서 JavaScript 함수를 호출 하 여 서버 쪽 사전 렌더링이의 구현을 단순화 합니다.</span><span class="sxs-lookup"><span data-stu-id="26870-145">ASP.NET Core [Tag Helpers](xref:mvc/views/tag-helpers/intro) provided by SpaServices simplify the implementation of server-side prerendering by invoking the JavaScript functions on the server.</span></span>

### <a name="prerequisites"></a><span data-ttu-id="26870-146">필수 구성 요소</span><span class="sxs-lookup"><span data-stu-id="26870-146">Prerequisites</span></span>

<span data-ttu-id="26870-147">다음을 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="26870-147">Install the following:</span></span>
* <span data-ttu-id="26870-148">[aspnet 사전 렌더링이](https://www.npmjs.com/package/aspnet-prerendering) npm 패키지:</span><span class="sxs-lookup"><span data-stu-id="26870-148">[aspnet-prerendering](https://www.npmjs.com/package/aspnet-prerendering) npm package:</span></span>

    ```console
    npm i -S aspnet-prerendering
    ```

### <a name="configuration"></a><span data-ttu-id="26870-149">구성</span><span class="sxs-lookup"><span data-stu-id="26870-149">Configuration</span></span>

<span data-ttu-id="26870-150">태그 도우미 프로젝트의 네임 스페이스 등록을 통해 검색 가능한 내용이 *_ViewImports.cshtml* 파일:</span><span class="sxs-lookup"><span data-stu-id="26870-150">The Tag Helpers are made discoverable via namespace registration in the project's *_ViewImports.cshtml* file:</span></span>

<span data-ttu-id="26870-151">[!code-csharp[Main](../client-side/spa-services/sample/SpaServicesSampleApp/Views/_ViewImports.cshtml?highlight=3)]</span><span class="sxs-lookup"><span data-stu-id="26870-151">[!code-csharp[Main](../client-side/spa-services/sample/SpaServicesSampleApp/Views/_ViewImports.cshtml?highlight=3)]</span></span>

<span data-ttu-id="26870-152">이러한 태그 도우미 Razor 뷰 내에 HTML 형식의 구문을 활용 하 여 하위 수준 Api와 직접 통신 하는 복잡 한을 추상화 합니다.</span><span class="sxs-lookup"><span data-stu-id="26870-152">These Tag Helpers abstract away the intricacies of communicating directly with low-level APIs by leveraging an HTML-like syntax inside the Razor view:</span></span>

<span data-ttu-id="26870-153">[!code-html[Main](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=5)]</span><span class="sxs-lookup"><span data-stu-id="26870-153">[!code-html[Main](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=5)]</span></span>

### <a name="the-asp-prerender-module-tag-helper"></a><span data-ttu-id="26870-154">`asp-prerender-module` 태그 도우미</span><span class="sxs-lookup"><span data-stu-id="26870-154">The `asp-prerender-module` Tag Helper</span></span>

<span data-ttu-id="26870-155">`asp-prerender-module` 태그 도우미, 앞의 코드 예제에 사용 된 실행 *ClientApp/dist/main-server.js* Node.js 통해 서버에서 합니다.</span><span class="sxs-lookup"><span data-stu-id="26870-155">The `asp-prerender-module` Tag Helper, used in the preceding code example, executes *ClientApp/dist/main-server.js* on the server via Node.js.</span></span> <span data-ttu-id="26870-156">확실히 하기 위해서 *main server.js* 파일은 JavaScript를 TypeScript transpilation 작업 아티팩트는 [시스템용](http://webpack.github.io/) 빌드 프로세스입니다.</span><span class="sxs-lookup"><span data-stu-id="26870-156">For clarity's sake, *main-server.js* file is an artifact of the TypeScript-to-JavaScript transpilation task in the [Webpack](http://webpack.github.io/) build process.</span></span> <span data-ttu-id="26870-157">한 항목이 지점 별칭을 정의 하는 시스템용 `main-server`;에서이 별칭에 대 한 종속성 그래프의 순회를 시작 및는 *ClientApp/부팅-server.ts* 파일:</span><span class="sxs-lookup"><span data-stu-id="26870-157">Webpack defines an entry point alias of `main-server`; and, traversal of the dependency graph for this alias begins at the *ClientApp/boot-server.ts* file:</span></span>

<span data-ttu-id="26870-158">[!code-javascript[Main](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=53)]</span><span class="sxs-lookup"><span data-stu-id="26870-158">[!code-javascript[Main](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=53)]</span></span>

<span data-ttu-id="26870-159">다음 각 예제에서는 *ClientApp/부팅-server.ts* 파일에서 사용는 `createServerRenderer` 함수 및 `RenderResult` 유형의 `aspnet-prerendering` npm 구성 하려면 패키지 Node.js 통해 서버 렌더링 합니다.</span><span class="sxs-lookup"><span data-stu-id="26870-159">In the following Angular example, the *ClientApp/boot-server.ts* file utilizes the `createServerRenderer` function and `RenderResult` type of the `aspnet-prerendering` npm package to configure server rendering via Node.js.</span></span> <span data-ttu-id="26870-160">보내는 서버 쪽 렌더링을 강력한 형식의 JavaScript에 겹쳐서 표시 하는 해결 함수 호출에 전달 되는 HTML 태그 `Promise` 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="26870-160">The HTML markup destined for server-side rendering is passed to a resolve function call, which is wrapped in a strongly-typed JavaScript `Promise` object.</span></span> <span data-ttu-id="26870-161">`Promise` 개체의 중요 한 이유는 DOM의 자리 표시자 요소에는 삽입에 대 한 페이지에는 HTML 태그를 비동기적으로 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="26870-161">The `Promise` object's significance is that it asynchronously supplies the HTML markup to the page for injection in the DOM's placeholder element.</span></span>

<span data-ttu-id="26870-162">[!code-typescript[Main](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-34,79-)]</span><span class="sxs-lookup"><span data-stu-id="26870-162">[!code-typescript[Main](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-34,79-)]</span></span>

### <a name="the-asp-prerender-data-tag-helper"></a><span data-ttu-id="26870-163">`asp-prerender-data` 태그 도우미</span><span class="sxs-lookup"><span data-stu-id="26870-163">The `asp-prerender-data` Tag Helper</span></span>

<span data-ttu-id="26870-164">함께 사용 하면는 `asp-prerender-module` 태그 도우미의 `asp-prerender-data` Razor 보기에서 서버 쪽 JavaScript를 컨텍스트 정보를 전달 하 태그 도우미를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="26870-164">When coupled with the `asp-prerender-module` Tag Helper, the `asp-prerender-data` Tag Helper can be used to pass contextual information from the Razor view to the server-side JavaScript.</span></span> <span data-ttu-id="26870-165">다음 태그를 사용자 데이터를 전달 하는 예를 들어는 `main-server` 모듈:</span><span class="sxs-lookup"><span data-stu-id="26870-165">For example, the following markup passes user data to the `main-server` module:</span></span>

<span data-ttu-id="26870-166">[!code-html[Main](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=9-12)]</span><span class="sxs-lookup"><span data-stu-id="26870-166">[!code-html[Main](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=9-12)]</span></span>

<span data-ttu-id="26870-167">받은 `UserName` 인수 기본 제공 JSON 직렬 변환기를 사용 하 여 직렬화 되 고에 저장 되는 `params.data` 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="26870-167">The received `UserName` argument is serialized using the built-in JSON serializer and is stored in the `params.data` object.</span></span> <span data-ttu-id="26870-168">다음 각 예제에서 데이터 내에서 개인 설정 된 인사말을 생성 하는 데 사용 된 `h1` 요소:</span><span class="sxs-lookup"><span data-stu-id="26870-168">In the following Angular example, the data is used to construct a personalized greeting within an `h1` element:</span></span>

<span data-ttu-id="26870-169">[!code-typescript[Main](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,38-52,79-)]</span><span class="sxs-lookup"><span data-stu-id="26870-169">[!code-typescript[Main](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,38-52,79-)]</span></span>

<span data-ttu-id="26870-170">참고: 태그 도우미에 전달 된 속성 이름으로 표시 됩니다 **표시 방법이 PascalCase** 표기법입니다.</span><span class="sxs-lookup"><span data-stu-id="26870-170">Note: Property names passed in Tag Helpers are represented with **PascalCase** notation.</span></span> <span data-ttu-id="26870-171">JavaScript에서 동일한 속성 이름으로 표시 됩니다는 여기서 즉 **camelCase**합니다.</span><span class="sxs-lookup"><span data-stu-id="26870-171">Contrast that to JavaScript, where the same property names are represented with **camelCase**.</span></span> <span data-ttu-id="26870-172">기본 JSON serialization 구성은 이러한 차이 담당 합니다.</span><span class="sxs-lookup"><span data-stu-id="26870-172">The default JSON serialization configuration is responsible for this difference.</span></span>

<span data-ttu-id="26870-173">연장 하 여 이전 코드 예제, 데이터 전달할 수 있습니다는 서버에서 보기에 hydrating 여는 `globals` 속성에 제공 되는 `resolve` 함수:</span><span class="sxs-lookup"><span data-stu-id="26870-173">To expand upon the preceding code example, data can be passed from the server to the view by hydrating the `globals` property provided to the `resolve` function:</span></span>

<span data-ttu-id="26870-174">[!code-typescript[Main](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,57-77,79-)]</span><span class="sxs-lookup"><span data-stu-id="26870-174">[!code-typescript[Main](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,57-77,79-)]</span></span>

<span data-ttu-id="26870-175">`postList` 배열 내에 정의 된는 `globals` 개체는 글로벌 브라우저의 연결 된 `window` 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="26870-175">The `postList` array defined inside the `globals` object is attached to the browser's global `window` object.</span></span> <span data-ttu-id="26870-176">전역 범위에이 변수 호이스팅와 특히 서버에서 한 번를 다시 클라이언트에 동일한 데이터를 로드 하 관련해 서 작업이, 중복을 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="26870-176">This variable hoisting to global scope eliminates duplication of effort, particularly as it pertains to loading the same data once on the server and again on the client.</span></span>

![창 개체에 연결 된 전역 postList 변수](spa-services/_static/global_variable.png)

<a name="webpack-dev-middleware"></a>

## <a name="webpack-dev-middleware"></a><span data-ttu-id="26870-178">시스템용 Dev 미들웨어입니다.</span><span class="sxs-lookup"><span data-stu-id="26870-178">Webpack Dev Middleware</span></span>

<span data-ttu-id="26870-179">[시스템용 Dev 미들웨어](https://webpack.github.io/docs/webpack-dev-middleware.html) 에서는 시스템용 필요에 따라 리소스를 작성 하는 그에 따라 효율적인된 개발 워크플로 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="26870-179">[Webpack Dev Middleware](https://webpack.github.io/docs/webpack-dev-middleware.html) introduces a streamlined development workflow whereby Webpack builds resources on demand.</span></span> <span data-ttu-id="26870-180">미들웨어가 자동으로 컴파일하고 브라우저에서 페이지를 다시 로드 하는 경우 클라이언트 쪽 리소스를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="26870-180">The middleware automatically compiles and serves client-side resources when a page is reloaded in the browser.</span></span> <span data-ttu-id="26870-181">타사 종속성 또는 사용자 지정 코드 변경 될 때 프로젝트의 npm 빌드 스크립트를 통해 시스템용을 수동으로 호출할 하는 대체 방법이입니다.</span><span class="sxs-lookup"><span data-stu-id="26870-181">The alternate approach is to manually invoke Webpack via the project's npm build script when a third-party dependency or the custom code changes.</span></span> <span data-ttu-id="26870-182">npm에서 스크립트를 작성은 *package.json* 파일은 다음 예제에 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="26870-182">An npm build script in the *package.json* file is shown in the following example:</span></span>

<span data-ttu-id="26870-183">[!code-json[Main](../client-side/spa-services/sample/SpaServicesSampleApp/package.json?range=5)]</span><span class="sxs-lookup"><span data-stu-id="26870-183">[!code-json[Main](../client-side/spa-services/sample/SpaServicesSampleApp/package.json?range=5)]</span></span>

### <a name="prerequisites"></a><span data-ttu-id="26870-184">필수 구성 요소</span><span class="sxs-lookup"><span data-stu-id="26870-184">Prerequisites</span></span>

<span data-ttu-id="26870-185">다음을 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="26870-185">Install the following:</span></span>
* <span data-ttu-id="26870-186">[aspnet 시스템용](https://www.npmjs.com/package/aspnet-webpack) npm 패키지:</span><span class="sxs-lookup"><span data-stu-id="26870-186">[aspnet-webpack](https://www.npmjs.com/package/aspnet-webpack) npm package:</span></span>

    ```console
    npm i -D aspnet-webpack
    ```

### <a name="configuration"></a><span data-ttu-id="26870-187">구성</span><span class="sxs-lookup"><span data-stu-id="26870-187">Configuration</span></span>

<span data-ttu-id="26870-188">시스템용 Dev 미들웨어에 다음 코드를 통해 HTTP 요청 파이프라인에 등록 되 고 *Startup.cs* 파일의 `Configure` 메서드:</span><span class="sxs-lookup"><span data-stu-id="26870-188">Webpack Dev Middleware is registered into the HTTP request pipeline via the following code in the *Startup.cs* file's `Configure` method:</span></span>

<span data-ttu-id="26870-189">[!code-csharp[Main](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=webpack-middleware-registration&highlight=4)]</span><span class="sxs-lookup"><span data-stu-id="26870-189">[!code-csharp[Main](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=webpack-middleware-registration&highlight=4)]</span></span>

<span data-ttu-id="26870-190">`UseWebpackDevMiddleware` 하기 전에 확장 메서드를 호출 해야 [정적 파일 호스팅 등록](xref:fundamentals/static-files) 통해는 `UseStaticFiles` 확장 메서드.</span><span class="sxs-lookup"><span data-stu-id="26870-190">The `UseWebpackDevMiddleware` extension method must be called before [registering static file hosting](xref:fundamentals/static-files) via the `UseStaticFiles` extension method.</span></span> <span data-ttu-id="26870-191">보안상의 이유로 응용 프로그램 개발 모드에서 실행 하는 경우에 미들웨어를 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="26870-191">For security reasons, register the middleware only when the app runs in development mode.</span></span>

<span data-ttu-id="26870-192">*webpack.config.js* 파일의 `output.publicPath` 속성이 있으면 조사할 미들웨어는 `dist` 변경에 대 한 폴더:</span><span class="sxs-lookup"><span data-stu-id="26870-192">The *webpack.config.js* file's `output.publicPath` property tells the middleware to watch the `dist` folder for changes:</span></span>

<span data-ttu-id="26870-193">[!code-javascript[Main](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,13-16)]</span><span class="sxs-lookup"><span data-stu-id="26870-193">[!code-javascript[Main](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,13-16)]</span></span>

<a name="hot-module-replacement"></a>

## <a name="hot-module-replacement"></a><span data-ttu-id="26870-194">핫 모듈 교체</span><span class="sxs-lookup"><span data-stu-id="26870-194">Hot Module Replacement</span></span>

<span data-ttu-id="26870-195">시스템용의 간주할 [핫 모듈 교체](https://webpack.github.io/docs/hot-module-replacement-with-webpack.html) 의 진화 (HMR) 기능 [시스템용 Dev 미들웨어](#webpack-dev-middleware)합니다.</span><span class="sxs-lookup"><span data-stu-id="26870-195">Think of Webpack's [Hot Module Replacement](https://webpack.github.io/docs/hot-module-replacement-with-webpack.html) (HMR) feature as an evolution of [Webpack Dev Middleware](#webpack-dev-middleware).</span></span> <span data-ttu-id="26870-196">HMR는 모두 같은 유용한 기능이 도입 되었지만 더 이상 자동으로 변경 내용을 컴파일한 후 페이지 콘텐츠를 업데이트 하 여 개발 워크플로 간소화 합니다.</span><span class="sxs-lookup"><span data-stu-id="26870-196">HMR introduces all the same benefits, but it further streamlines the development workflow by automatically updating page content after compiling the changes.</span></span> <span data-ttu-id="26870-197">현재 메모리 상태와는 SPA의 디버깅 세션을 방해 하 게 하는 브라우저의 새로 고침으로이 혼동 하지 마십시오.</span><span class="sxs-lookup"><span data-stu-id="26870-197">Don't confuse this with a refresh of the browser, which would interfere with the current in-memory state and debugging session of the SPA.</span></span> <span data-ttu-id="26870-198">시스템용 Dev 미들웨어 서비스와 변경이 즉 브라우저 간의 라이브 링크가 ~ 단순히 다른 금지 된 단어 ~ 브라우저에 전달 합니다.</span><span class="sxs-lookup"><span data-stu-id="26870-198">There is a live link between the Webpack Dev Middleware service and the browser, which means changes are ~simply another banned word~ pushed to the browser.</span></span>

### <a name="prerequisites"></a><span data-ttu-id="26870-199">필수 구성 요소</span><span class="sxs-lookup"><span data-stu-id="26870-199">Prerequisites</span></span>

<span data-ttu-id="26870-200">다음을 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="26870-200">Install the following:</span></span>
* <span data-ttu-id="26870-201">[시스템용 핫 미들웨어](https://www.npmjs.com/package/webpack-hot-middleware) npm 패키지:</span><span class="sxs-lookup"><span data-stu-id="26870-201">[webpack-hot-middleware](https://www.npmjs.com/package/webpack-hot-middleware) npm package:</span></span>

    ```console
    npm i -D webpack-hot-middleware
    ```

### <a name="configuration"></a><span data-ttu-id="26870-202">구성</span><span class="sxs-lookup"><span data-stu-id="26870-202">Configuration</span></span>

<span data-ttu-id="26870-203">HMR 구성 요소에서 MVC의 HTTP 요청 파이프라인으로 등록 되어야 합니다는 `Configure` 메서드:</span><span class="sxs-lookup"><span data-stu-id="26870-203">The HMR component must be registered into MVC's HTTP request pipeline in the `Configure` method:</span></span>

```csharp
app.UseWebpackDevMiddleware(new WebpackDevMiddlewareOptions {
    HotModuleReplacement = true
});
```

<span data-ttu-id="26870-204">마찬가지로 true와 [시스템용 Dev 미들웨어](#webpack-dev-middleware), `UseWebpackDevMiddleware` 하기 전에 확장 메서드를 호출 해야 합니다는 `UseStaticFiles` 확장 메서드.</span><span class="sxs-lookup"><span data-stu-id="26870-204">As was true with [Webpack Dev Middleware](#webpack-dev-middleware), the `UseWebpackDevMiddleware` extension method must be called before the `UseStaticFiles` extension method.</span></span> <span data-ttu-id="26870-205">보안상의 이유로 응용 프로그램 개발 모드에서 실행 하는 경우에 미들웨어를 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="26870-205">For security reasons, register the middleware only when the app runs in development mode.</span></span>

<span data-ttu-id="26870-206">*webpack.config.js* 파일 정의 해야 합니다는 `plugins` 빈 상태일 경우에 배열:</span><span class="sxs-lookup"><span data-stu-id="26870-206">The *webpack.config.js* file must define a `plugins` array, even if it's left empty:</span></span>

<span data-ttu-id="26870-207">[!code-javascript[Main](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,25)]</span><span class="sxs-lookup"><span data-stu-id="26870-207">[!code-javascript[Main](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,25)]</span></span>

<span data-ttu-id="26870-208">브라우저에서 응용 프로그램을 로드 한 후 개발자 도구 콘솔 탭 HMR 정품 인증에 대 한 확인을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="26870-208">After loading the app in the browser, the developer tools' Console tab provides confirmation of HMR activation:</span></span>

![핫 모듈 교체 연결 된 메시지](spa-services/_static/hmr_connected.png)

<a name="routing-helpers"></a>

## <a name="routing-helpers"></a><span data-ttu-id="26870-210">라우팅 도우미</span><span class="sxs-lookup"><span data-stu-id="26870-210">Routing helpers</span></span>

<span data-ttu-id="26870-211">대부분의 ASP.NET Core 기반 SPAs 서버 쪽 라우팅과 함께 클라이언트 쪽 라우팅 합니다.</span><span class="sxs-lookup"><span data-stu-id="26870-211">In most ASP.NET Core-based SPAs, you'll want client-side routing in addition to server-side routing.</span></span> <span data-ttu-id="26870-212">SPA 및 MVC 라우팅 시스템 방해를 받지 않고 독립적으로 작업할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="26870-212">The SPA and MVC routing systems can work independently without interference.</span></span> <span data-ttu-id="26870-213">그러나가 문제점을 게시 한 특별 한 경우: 404 HTTP 응답을 식별 합니다.</span><span class="sxs-lookup"><span data-stu-id="26870-213">There is, however, one edge case posing challenges: identifying 404 HTTP responses.</span></span>

<span data-ttu-id="26870-214">시나리오를 고려해 야의 확장명은 경로 `/some/page` 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="26870-214">Consider the scenario in which an extensionless route of `/some/page` is used.</span></span> <span data-ttu-id="26870-215">해당 패턴 일치는 클라이언트 쪽 경로 않지만 요청 하지 않는 패턴 일치 한 서버 쪽 경로 가정 합니다.</span><span class="sxs-lookup"><span data-stu-id="26870-215">Assume the request doesn't pattern-match a server-side route, but its pattern does match a client-side route.</span></span> <span data-ttu-id="26870-216">에 대 한 들어오는 요청에 알아보겠습니다 `/images/user-512.png`, 일반적으로 서버에서 이미지 파일을 찾으려고 시도 합니다.</span><span class="sxs-lookup"><span data-stu-id="26870-216">Now consider an incoming request for `/images/user-512.png`, which generally expects to find an image file on the server.</span></span> <span data-ttu-id="26870-217">클라이언트 쪽 응용 프로그램에서 처리할 것 있음을 그럴 가능성은 해당 요청 된 리소스 경로는 모든 서버 쪽 경로 또는 정적 파일와 일치 하지 않으면,-404 HTTP 상태 코드를 반환 하려면 일반적으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="26870-217">If that requested resource path doesn't match any server-side route or static file, it's unlikely that the client-side application would handle it — you generally want to return a 404 HTTP status code.</span></span>

### <a name="prerequisites"></a><span data-ttu-id="26870-218">필수 구성 요소</span><span class="sxs-lookup"><span data-stu-id="26870-218">Prerequisites</span></span>

<span data-ttu-id="26870-219">다음을 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="26870-219">Install the following:</span></span>
* <span data-ttu-id="26870-220">클라이언트 쪽 라우팅 npm 패키지입니다.</span><span class="sxs-lookup"><span data-stu-id="26870-220">The client-side routing npm package.</span></span> <span data-ttu-id="26870-221">예를 들어 각 사용:</span><span class="sxs-lookup"><span data-stu-id="26870-221">Using Angular as an example:</span></span>

    ```console
    npm i -S @angular/router
    ```

### <a name="configuration"></a><span data-ttu-id="26870-222">구성</span><span class="sxs-lookup"><span data-stu-id="26870-222">Configuration</span></span>

<span data-ttu-id="26870-223">라는 확장 메서드 `MapSpaFallbackRoute` 에 사용 되는 `Configure` 메서드:</span><span class="sxs-lookup"><span data-stu-id="26870-223">An extension method named `MapSpaFallbackRoute` is used in the `Configure` method:</span></span>

<span data-ttu-id="26870-224">[!code-csharp[Main](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=mvc-routing-table&highlight=7-9)]</span><span class="sxs-lookup"><span data-stu-id="26870-224">[!code-csharp[Main](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=mvc-routing-table&highlight=7-9)]</span></span>

<span data-ttu-id="26870-225">팁: 경로 구성 하는 순서로 평가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="26870-225">Tip: Routes are evaluated in the order in which they're configured.</span></span> <span data-ttu-id="26870-226">따라서는 `default` 패턴 일치를 위해 이전 코드 예제에서 경로 먼저 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="26870-226">Consequently, the `default` route in the preceding code example is used first for pattern matching.</span></span>

<a name="new-project-creation"></a>

## <a name="creating-a-new-project"></a><span data-ttu-id="26870-227">새 프로젝트 만들기</span><span class="sxs-lookup"><span data-stu-id="26870-227">Creating a new project</span></span>

<span data-ttu-id="26870-228">JavaScriptServices 미리 구성 된 응용 프로그램 템플릿을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="26870-228">JavaScriptServices provides pre-configured application templates.</span></span> <span data-ttu-id="26870-229">SpaServices 다양 한 프레임 워크 및 각, Aurelia, Knockout, React, 및 Vue 등의 라이브러리와 함께에서 이러한 서식 파일에 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="26870-229">SpaServices is used in these templates, in conjunction with different frameworks and libraries such as Angular, Aurelia, Knockout, React, and Vue.</span></span>

<span data-ttu-id="26870-230">다음 명령을 실행 하 여.NET Core CLI를 통해 이러한 서식 파일을 설치할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="26870-230">These templates can be installed via the .NET Core CLI by running the following command:</span></span>

```console
dotnet new --install Microsoft.AspNetCore.SpaTemplates::*
```

<span data-ttu-id="26870-231">사용 가능한 SPA 템플릿 목록이 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="26870-231">A list of available SPA templates is displayed:</span></span>

| <span data-ttu-id="26870-232">템플릿</span><span class="sxs-lookup"><span data-stu-id="26870-232">Templates</span></span>                                 | <span data-ttu-id="26870-233">짧은 이름</span><span class="sxs-lookup"><span data-stu-id="26870-233">Short Name</span></span> | <span data-ttu-id="26870-234">언어</span><span class="sxs-lookup"><span data-stu-id="26870-234">Language</span></span> | <span data-ttu-id="26870-235">Tags</span><span class="sxs-lookup"><span data-stu-id="26870-235">Tags</span></span>        |
|:------------------------------------------|:-----------|:---------|:------------|
| <span data-ttu-id="26870-236">각도와 MVC ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="26870-236">MVC ASP.NET Core with Angular</span></span>             | <span data-ttu-id="26870-237">angular</span><span class="sxs-lookup"><span data-stu-id="26870-237">angular</span></span>    | <span data-ttu-id="26870-238">[C#]</span><span class="sxs-lookup"><span data-stu-id="26870-238">[C#]</span></span>     | <span data-ttu-id="26870-239">웹/MVC/SPA</span><span class="sxs-lookup"><span data-stu-id="26870-239">Web/MVC/SPA</span></span> |
| <span data-ttu-id="26870-240">MVC ASP.NET Core Aurelia와</span><span class="sxs-lookup"><span data-stu-id="26870-240">MVC ASP.NET Core with Aurelia</span></span>             | <span data-ttu-id="26870-241">aurelia</span><span class="sxs-lookup"><span data-stu-id="26870-241">aurelia</span></span>    | <span data-ttu-id="26870-242">[C#]</span><span class="sxs-lookup"><span data-stu-id="26870-242">[C#]</span></span>     | <span data-ttu-id="26870-243">웹/MVC/SPA</span><span class="sxs-lookup"><span data-stu-id="26870-243">Web/MVC/SPA</span></span> |
| <span data-ttu-id="26870-244">Knockout.js와 MVC ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="26870-244">MVC ASP.NET Core with Knockout.js</span></span>         | <span data-ttu-id="26870-245">knockout</span><span class="sxs-lookup"><span data-stu-id="26870-245">knockout</span></span>   | <span data-ttu-id="26870-246">[C#]</span><span class="sxs-lookup"><span data-stu-id="26870-246">[C#]</span></span>     | <span data-ttu-id="26870-247">웹/MVC/SPA</span><span class="sxs-lookup"><span data-stu-id="26870-247">Web/MVC/SPA</span></span> |
| <span data-ttu-id="26870-248">MVC ASP.NET Core React.js와</span><span class="sxs-lookup"><span data-stu-id="26870-248">MVC ASP.NET Core with React.js</span></span>            | <span data-ttu-id="26870-249">react</span><span class="sxs-lookup"><span data-stu-id="26870-249">react</span></span>      | <span data-ttu-id="26870-250">[C#]</span><span class="sxs-lookup"><span data-stu-id="26870-250">[C#]</span></span>     | <span data-ttu-id="26870-251">웹/MVC/SPA</span><span class="sxs-lookup"><span data-stu-id="26870-251">Web/MVC/SPA</span></span> |
| <span data-ttu-id="26870-252">MVC ASP.NET Core React.js 및 Redux</span><span class="sxs-lookup"><span data-stu-id="26870-252">MVC ASP.NET Core with React.js and Redux</span></span>  | <span data-ttu-id="26870-253">reactredux</span><span class="sxs-lookup"><span data-stu-id="26870-253">reactredux</span></span> | <span data-ttu-id="26870-254">[C#]</span><span class="sxs-lookup"><span data-stu-id="26870-254">[C#]</span></span>     | <span data-ttu-id="26870-255">웹/MVC/SPA</span><span class="sxs-lookup"><span data-stu-id="26870-255">Web/MVC/SPA</span></span> |
| <span data-ttu-id="26870-256">MVC ASP.NET Core Vue.js와</span><span class="sxs-lookup"><span data-stu-id="26870-256">MVC ASP.NET Core with Vue.js</span></span>              | <span data-ttu-id="26870-257">vue</span><span class="sxs-lookup"><span data-stu-id="26870-257">vue</span></span>        | <span data-ttu-id="26870-258">[C#]</span><span class="sxs-lookup"><span data-stu-id="26870-258">[C#]</span></span>     | <span data-ttu-id="26870-259">웹/MVC/SPA</span><span class="sxs-lookup"><span data-stu-id="26870-259">Web/MVC/SPA</span></span> | 

<span data-ttu-id="26870-260">SPA 템플릿 중 하나를 사용 하 여 새 프로젝트를 만들려면 포함는 **약식 이름** 에 서식 파일의는 `dotnet new` 명령입니다.</span><span class="sxs-lookup"><span data-stu-id="26870-260">To create a new project using one of the SPA templates, include the **Short Name** of the template in the `dotnet new` command.</span></span> <span data-ttu-id="26870-261">다음 명령은 서버 측에 대해 구성 된 ASP.NET Core MVC와 함께 각 응용 프로그램를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="26870-261">The following command creates an Angular application with ASP.NET Core MVC configured for the server side:</span></span>

```console
dotnet new angular
```

<a name="runtime-config-mode"></a>

### <a name="set-the-runtime-configuration-mode"></a><span data-ttu-id="26870-262">런타임 구성 모드를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="26870-262">Set the runtime configuration mode</span></span>

<span data-ttu-id="26870-263">다음과 같은 두 가지 기본 런타임 구성 모드</span><span class="sxs-lookup"><span data-stu-id="26870-263">Two primary runtime configuration modes exist:</span></span>
* <span data-ttu-id="26870-264">**개발**:</span><span class="sxs-lookup"><span data-stu-id="26870-264">**Development**:</span></span>
    * <span data-ttu-id="26870-265">디버깅을 쉽게 수행 하려면 소스 맵이 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="26870-265">Includes source maps to ease debugging.</span></span>
    * <span data-ttu-id="26870-266">성능에 대 한 클라이언트 쪽 코드를 최적화 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="26870-266">Doesn't optimize the client-side code for performance.</span></span>
* <span data-ttu-id="26870-267">**프로덕션**:</span><span class="sxs-lookup"><span data-stu-id="26870-267">**Production**:</span></span>
    * <span data-ttu-id="26870-268">소스 맵이 제외 됩니다.</span><span class="sxs-lookup"><span data-stu-id="26870-268">Excludes source maps.</span></span>
    * <span data-ttu-id="26870-269">묶음 및 축소를 통해 클라이언트 코드를 최적화합니다.</span><span class="sxs-lookup"><span data-stu-id="26870-269">Optimizes the client-side code via bundling & minification.</span></span>

<span data-ttu-id="26870-270">ASP.NET Core 라는 환경 변수를 사용 하 여 `ASPNETCORE_ENVIRONMENT` 구성 모드를 저장할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="26870-270">ASP.NET Core uses an environment variable named `ASPNETCORE_ENVIRONMENT` to store the configuration mode.</span></span> <span data-ttu-id="26870-271">참조  **[환경 설정](xref:fundamentals/environments#setting-the-environment)**  자세한 정보에 대 한 합니다.</span><span class="sxs-lookup"><span data-stu-id="26870-271">See **[Setting the environment](xref:fundamentals/environments#setting-the-environment)** for more information.</span></span>

### <a name="running-with-net-core-cli"></a><span data-ttu-id="26870-272">.NET core CLI 실행</span><span class="sxs-lookup"><span data-stu-id="26870-272">Running with .NET Core CLI</span></span>

<span data-ttu-id="26870-273">프로젝트 루트에서 다음 명령을 실행 하 여 필요한 NuGet 및 npm 패키지를 복원 합니다.</span><span class="sxs-lookup"><span data-stu-id="26870-273">Restore the required NuGet and npm packages by running the following command at the project root:</span></span>

```console
dotnet restore && npm i
```

<span data-ttu-id="26870-274">빌드 및 응용 프로그램을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="26870-274">Build and run the application:</span></span>

```console
dotnet run
```

<span data-ttu-id="26870-275">응용 프로그램에 따라 로컬 호스트에서 시작 된 [런타임 구성 모드](#runtime-config-mode)합니다.</span><span class="sxs-lookup"><span data-stu-id="26870-275">The application starts on localhost according to the [runtime configuration mode](#runtime-config-mode).</span></span> <span data-ttu-id="26870-276">로 이동 `http://localhost:5000` 브라우저에 방문 페이지를 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="26870-276">Navigating to `http://localhost:5000` in the browser displays the landing page.</span></span>

### <a name="running-with-visual-studio-2017"></a><span data-ttu-id="26870-277">Visual Studio 2017 함께 실행</span><span class="sxs-lookup"><span data-stu-id="26870-277">Running with Visual Studio 2017</span></span>

<span data-ttu-id="26870-278">열기는 *.csproj* 에서 생성 된 파일은 `dotnet new` 명령입니다.</span><span class="sxs-lookup"><span data-stu-id="26870-278">Open the *.csproj* file generated by the `dotnet new` command.</span></span> <span data-ttu-id="26870-279">데 필요한 NuGet 및 npm 패키지는 프로젝트를 열고 시 자동으로 복원 됩니다.</span><span class="sxs-lookup"><span data-stu-id="26870-279">The required NuGet and npm packages are restored automatically upon project open.</span></span> <span data-ttu-id="26870-280">이 복원 프로세스 최대 몇 분 정도 걸릴 수 있습니다 및 응용 프로그램은 작업이 완료 될 때 실행 하도록 준비 합니다.</span><span class="sxs-lookup"><span data-stu-id="26870-280">This restoration process may take up to a few minutes, and the application is ready to run when it completes.</span></span> <span data-ttu-id="26870-281">녹색 실행된 단추 또는 키를 눌러 `Ctrl + F5`, 브라우저 응용 프로그램의 방문 페이지에 열립니다.</span><span class="sxs-lookup"><span data-stu-id="26870-281">Click the green run button or press `Ctrl + F5`, and the browser opens to the application's landing page.</span></span> <span data-ttu-id="26870-282">응용 프로그램에 따라 로컬 호스트에서 실행 되는 [런타임 구성 모드](#runtime-config-mode)합니다.</span><span class="sxs-lookup"><span data-stu-id="26870-282">The application runs on localhost according to the [runtime configuration mode](#runtime-config-mode).</span></span> 

<a name="app-testing"></a>

## <a name="testing-the-app"></a><span data-ttu-id="26870-283">응용 프로그램 테스트</span><span class="sxs-lookup"><span data-stu-id="26870-283">Testing the app</span></span>

<span data-ttu-id="26870-284">SpaServices 템플릿은 사용 하 여 클라이언트 쪽 테스트를 실행 하는 미리 구성 된 [Karma](https://karma-runner.github.io/1.0/index.html) 및 [Jasmine](https://jasmine.github.io/)합니다.</span><span class="sxs-lookup"><span data-stu-id="26870-284">SpaServices templates are pre-configured to run client-side tests using [Karma](https://karma-runner.github.io/1.0/index.html) and [Jasmine](https://jasmine.github.io/).</span></span> <span data-ttu-id="26870-285">Karma test runner를 해당 테스트에 대 한 반면 jasmine는 인기 있는 단위 테스트 프레임 워크에 JavaScript를입니다.</span><span class="sxs-lookup"><span data-stu-id="26870-285">Jasmine is a popular unit testing framework for JavaScript, whereas Karma is a test runner for those tests.</span></span> <span data-ttu-id="26870-286">Karma 함께 작동 하도록 구성 되는 [시스템용 Dev 미들웨어](#webpack-dev-middleware) 중지 하 고 변경 내용이 적용 될 때마다 테스트를 실행 하지 않아도 되도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="26870-286">Karma is configured to work with the [Webpack Dev Middleware](#webpack-dev-middleware) such that you don’t have to stop and run the test every time changes are made.</span></span> <span data-ttu-id="26870-287">테스트 사례 또는 테스트 사례 자체에 대해 실행 되는 코드 인지 테스트가 자동으로 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="26870-287">Whether it's the code running against the test case or the test case itself, the test runs automatically.</span></span>

<span data-ttu-id="26870-288">각 응용 프로그램을 사용 하 여 예를 들어, 두 개의 자스민 테스트 사례가 이미 제공에 대 한는 `CounterComponent` 에 *counter.component.spec.ts* 파일:</span><span class="sxs-lookup"><span data-stu-id="26870-288">Using the Angular application as an example, two Jasmine test cases are already provided for the `CounterComponent` in the *counter.component.spec.ts* file:</span></span>

<span data-ttu-id="26870-289">[!code-typescript[Main](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/app/components/counter/counter.component.spec.ts?range=15-28)]</span><span class="sxs-lookup"><span data-stu-id="26870-289">[!code-typescript[Main](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/app/components/counter/counter.component.spec.ts?range=15-28)]</span></span>

<span data-ttu-id="26870-290">프로젝트 루트에 명령 프롬프트를 열고 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="26870-290">Open the command prompt at the project root, and run the following command:</span></span>

```console
npm test
```

<span data-ttu-id="26870-291">스크립트에 정의 된 설정을 읽는 Karma test runner를 시작 합니다.는 *karma.conf.js* 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="26870-291">The script launches the Karma test runner, which reads the settings defined in the *karma.conf.js* file.</span></span> <span data-ttu-id="26870-292">다른 설정 보다는 *karma.conf.js* 를 통해 실행할 테스트 파일을 식별 하는 `files` 배열:</span><span class="sxs-lookup"><span data-stu-id="26870-292">Among other settings, the *karma.conf.js* identifies the test files to be executed via its `files` array:</span></span>

<span data-ttu-id="26870-293">[!code-javascript[Main](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/test/karma.conf.js?range=4-5,8-11)]</span><span class="sxs-lookup"><span data-stu-id="26870-293">[!code-javascript[Main](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/test/karma.conf.js?range=4-5,8-11)]</span></span>

<a name="app-publishing"></a>

## <a name="publishing-the-application"></a><span data-ttu-id="26870-294">응용 프로그램을 게시</span><span class="sxs-lookup"><span data-stu-id="26870-294">Publishing the application</span></span>

<span data-ttu-id="26870-295">생성 된 클라이언트 쪽 자산 및 게시 된 ASP.NET Core 아티팩트를 준비 하려면 배포 패키지에 결합은 복잡할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="26870-295">Combining the generated client-side assets and the published ASP.NET Core artifacts into a ready-to-deploy package can be cumbersome.</span></span> <span data-ttu-id="26870-296">다행히도 SpaServices 라는 사용자 지정 MSBuild 대상을 사용 하 여 해당 전체 게시 프로세스를 조정 `RunWebpack`:</span><span class="sxs-lookup"><span data-stu-id="26870-296">Thankfully, SpaServices orchestrates that entire publication process with a custom MSBuild target named `RunWebpack`:</span></span>

<span data-ttu-id="26870-297">[!code-xml[Main](../client-side/spa-services/sample/SpaServicesSampleApp/SpaServicesSampleApp.csproj?range=31-45)]</span><span class="sxs-lookup"><span data-stu-id="26870-297">[!code-xml[Main](../client-side/spa-services/sample/SpaServicesSampleApp/SpaServicesSampleApp.csproj?range=31-45)]</span></span>

<span data-ttu-id="26870-298">MSBuild 대상에는 다음 책임이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="26870-298">The MSBuild target has the following responsibilities:</span></span>
1. <span data-ttu-id="26870-299">Npm 패키지를 복원 합니다.</span><span class="sxs-lookup"><span data-stu-id="26870-299">Restore the npm packages</span></span>
1. <span data-ttu-id="26870-300">제 3 자, 클라이언트 쪽 자산 프로덕션 수준의 빌드를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="26870-300">Create a production-grade build of the third-party, client-side assets</span></span>
1. <span data-ttu-id="26870-301">사용자 지정 클라이언트 측 자산 프로덕션 수준의 빌드를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="26870-301">Create a production-grade build of the custom client-side assets</span></span>
1. <span data-ttu-id="26870-302">시스템용에서 생성 된 자산을 게시 폴더에 복사 합니다.</span><span class="sxs-lookup"><span data-stu-id="26870-302">Copy the Webpack-generated assets to the publish folder</span></span>

<span data-ttu-id="26870-303">MSBuild 대상 실행 하는 경우 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="26870-303">The MSBuild target is invoked when running:</span></span>

```console
dotnet publish -c Release
```

## <a name="additional-resources"></a><span data-ttu-id="26870-304">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="26870-304">Additional resources</span></span>

* [<span data-ttu-id="26870-305">Angular Docs</span><span class="sxs-lookup"><span data-stu-id="26870-305">Angular Docs</span></span>](https://angular.io/docs)