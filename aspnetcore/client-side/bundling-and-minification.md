---
title: "묶음 및 축소에서 ASP.NET Core"
author: spboyer
description: 
keywords: "ASP.NET 코어 묶음 및 축소, CSS, JavaScript, 축소할 BuildBundlerMinifier"
ms.author: riande
manager: wpickett
ms.date: 02/28/2017
ms.topic: article
ms.assetid: d54230f9-8e5f-4861-a29c-1d3a14e0b0d9
ms.technology: aspnet
ms.prod: aspnet-core
uid: client-side/bundling-and-minification
ms.openlocfilehash: 11528cb2067ced79a09845f9ff78d897da033438
ms.sourcegitcommit: 78d28178345a0eea91556e4cd1adad98b1446db8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2017
---
# <a name="bundling-and-minification-in-aspnet-core"></a><span data-ttu-id="31979-103">묶음 및 축소에서 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="31979-103">Bundling and minification in ASP.NET Core</span></span>

<span data-ttu-id="31979-104">묶음 및 축소는 다음 두 가지 기술 웹 응용 프로그램에 대 한 페이지 로드 성능 향상을 위해 ASP.NET에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="31979-104">Bundling and minification are two techniques you can use in ASP.NET to improve page load performance for your web application.</span></span> <span data-ttu-id="31979-105">번들로 단일 파일에 여러 파일을 결합합니다.</span><span class="sxs-lookup"><span data-stu-id="31979-105">Bundling combines multiple files into a single file.</span></span> <span data-ttu-id="31979-106">다양 한 스크립트와 페이로드가 작을수록에 CSS를 서로 다른 코드 최적화를 수행 하는 축소 합니다.</span><span class="sxs-lookup"><span data-stu-id="31979-106">Minification performs a variety of different code optimizations to scripts and CSS, which results in smaller payloads.</span></span> <span data-ttu-id="31979-107">함께 사용할 묶음 및 축소 부하 시간 여 성능을 향상 시킵니다 서버에 요청 수를 줄이고 CSS 및 JavaScript 파일과 같은 요청 된 자산의 크기를 줄이십시오.</span><span class="sxs-lookup"><span data-stu-id="31979-107">Used together, bundling and minification improves load time performance by reducing the number of requests to the server and reducing the size of the requested assets (such as CSS and JavaScript files).</span></span>

<span data-ttu-id="31979-108">이 문서에서는 묶음 및 축소, ASP.NET Core 응용 프로그램과 함께 이러한 기능은 사용할 수 있는 방법을 포함 하 여 사용의 이점에 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="31979-108">This article explains the benefits of using bundling and minification, including how these features can be used with ASP.NET Core applications.</span></span>

## <a name="overview"></a><span data-ttu-id="31979-109">개요</span><span class="sxs-lookup"><span data-stu-id="31979-109">Overview</span></span>

<span data-ttu-id="31979-110">ASP.NET Core 응용 프로그램에서 묶음 및 축소 클라이언트 리소스에 대 한 여러 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="31979-110">In ASP.NET Core apps, there are multiple options for bundling and minifying client-side resources.</span></span> <span data-ttu-id="31979-111">MVC에 대 한 핵심 템플릿 구성 파일 및 BuildBundlerMinifier NuGet 패키지를 사용 하 여 기본적으로 솔루션을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="31979-111">The core templates for MVC provide an out-of-the-box solution using a configuration file and BuildBundlerMinifier NuGet package.</span></span> <span data-ttu-id="31979-112">와 같은 타사 도구 [Gulp](using-gulp.md) 및 [Grunt](using-grunt.md) 추가 워크플로 또는 복잡 한 프로세스 필요한 해야 동일한 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="31979-112">Third party tools, such as [Gulp](using-gulp.md) and [Grunt](using-grunt.md) are also available to accomplish the same tasks should your processes require additional workflow or complexities.</span></span> <span data-ttu-id="31979-113">디자인 타임 묶음 및 축소를 사용 하 여 응용 프로그램의 배포 하기 전에 축소 된 파일이 생성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="31979-113">By using design-time bundling and minification, the minified files are created prior to the application’s deployment.</span></span> <span data-ttu-id="31979-114">묶음 및 축소 배포 하기 전에 서버 부하 감소의 이점이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="31979-114">Bundling and minifying before deployment provides the advantage of reduced server load.</span></span> <span data-ttu-id="31979-115">그러나 해당 디자인 타임 번들로 인식 해야 하 고 축소 빌드 복잡성을 증가 하 고 정적 파일 에서만 작동 합니다.</span><span class="sxs-lookup"><span data-stu-id="31979-115">However, it’s important to recognize that design-time bundling and minification increases build complexity and only works with static files.</span></span>

<span data-ttu-id="31979-116">묶음 및 축소는 주로 첫 번째 페이지 요청 부하 시간을 개선 합니다.</span><span class="sxs-lookup"><span data-stu-id="31979-116">Bundling and minification primarily improve the first page request load time.</span></span> <span data-ttu-id="31979-117">브라우저 캐시 자산 (JavaScript, CSS 및 이미지) 웹 페이지를 요청 되 면 페이지는 동일한 사이트 자산과 동일한 요청 하거나 묶음 및 축소 같은 페이지를 요청할 때 모든 성능 향상을 제공 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="31979-117">Once a web page has been requested, the browser caches the assets (JavaScript, CSS and images) so bundling and minification won’t provide any performance boost when requesting the same page, or pages on the same site requesting the same assets.</span></span> <span data-ttu-id="31979-118">설정 하지 않으면는 사용자의 자산에서 올바르게 헤더 만료 묶음 및 축소를 사용 하지 않는, 브라우저의 새로 고침 추론 됩니다 자산 부실 몇 일 후 약관을 표시할 브라우저 각 자산에 대 한 유효성 검사 요청을 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="31979-118">If you don’t set the expires header correctly on your assets, and you don’t use bundling and minification, the browser's freshness heuristics will mark the assets stale after a few days and the browser will require a validation request for each asset.</span></span> <span data-ttu-id="31979-119">이 경우 묶음 및 축소 첫 번째 페이지 요청 후에 성능 향상을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="31979-119">In this case, bundling and minification provide a performance increase even after the first page request.</span></span>

### <a name="bundling"></a><span data-ttu-id="31979-120">번들</span><span class="sxs-lookup"><span data-stu-id="31979-120">Bundling</span></span>

<span data-ttu-id="31979-121">번들로 결합 하거나 단일 파일에 여러 파일을 번들을 활용할 수 있는 기능입니다.</span><span class="sxs-lookup"><span data-stu-id="31979-121">Bundling is a feature that makes it easy to combine or bundle multiple files into a single file.</span></span> <span data-ttu-id="31979-122">번들 파일 결합 하 여 여러 단일 파일로, 되므로 검색 하 고 웹 페이지 등의 웹 자산을 표시 하는 데 필요한 서버에 요청 수 감소 시킵니다.</span><span class="sxs-lookup"><span data-stu-id="31979-122">Because bundling combines multiple files into a single file, it reduces the number of requests to the server that are required to retrieve and display a web asset, such as a web page.</span></span> <span data-ttu-id="31979-123">CSS, JavaScript 및 다른 번들을 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="31979-123">You can create CSS, JavaScript and other bundles.</span></span> <span data-ttu-id="31979-124">적은 파일 서버에 브라우저 또는 응용 프로그램을 제공 하는 서비스에서 더 적은 수의 HTTP 요청을 의미 합니다.</span><span class="sxs-lookup"><span data-stu-id="31979-124">Fewer files means fewer HTTP requests from your browser to the server or from the service providing your application.</span></span> <span data-ttu-id="31979-125">이 결과에서 첫 번째 페이지 부하 성능이 향상 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="31979-125">This results in improved first page load performance.</span></span>

### <a name="minification"></a><span data-ttu-id="31979-126">축소</span><span class="sxs-lookup"><span data-stu-id="31979-126">Minification</span></span>

<span data-ttu-id="31979-127">다양 한 요청 된 자산 (예: CSS, 이미지, JavaScript 파일)의 크기를 줄이기 위해 다른 코드 최적화를 수행 하는 축소 합니다.</span><span class="sxs-lookup"><span data-stu-id="31979-127">Minification performs a variety of different code optimizations to reduce the size of requested assets (such as CSS, images, JavaScript files).</span></span> <span data-ttu-id="31979-128">축소의 일반적인 결과 불필요 한 공백 및 메모를 제거 하 고 한 문자를 변수 이름을 단축 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="31979-128">Common results of minification include removing unnecessary white space and comments, and shortening variable names to one character.</span></span>

<span data-ttu-id="31979-129">다음 JavaScript 함수가 있다고 합시다.</span><span class="sxs-lookup"><span data-stu-id="31979-129">Consider the following JavaScript function:</span></span>

```javascript
AddAltToImg = function (imageTagAndImageID, imageContext) {
  ///<signature>
  ///<summary> Adds an alt tab to the image
  // </summary>
  //<param name="imgElement" type="String">The image selector.</param>
  //<param name="ContextForImage" type="String">The image context.</param>
  ///</signature>
  var imageElement = $(imageTagAndImageID, imageContext);
  imageElement.attr('alt', imageElement.attr('id').replace(/ID/, ''));
}
```

<span data-ttu-id="31979-130">축소 후 함수가 다음과 감소 됩니다.</span><span class="sxs-lookup"><span data-stu-id="31979-130">After minification, the function is reduced to the following:</span></span>

```javascript
AddAltToImg=function(t,a){var r=$(t,a);r.attr("alt",r.attr("id").replace(/ID/,""))};
```

<span data-ttu-id="31979-131">주석 및 불필요 한 공백을 제거 하는 것 외에도 다음 매개 변수 및 변수 이름은 열의 이름을 (축약 됨) 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="31979-131">In addition to removing the comments and unnecessary whitespace, the following parameters and variable names were renamed (shortened) as follows:</span></span>

<span data-ttu-id="31979-132">원래 색</span><span class="sxs-lookup"><span data-stu-id="31979-132">Original</span></span> | <span data-ttu-id="31979-133">이름이 바뀜</span><span class="sxs-lookup"><span data-stu-id="31979-133">Renamed</span></span>
--- | :---:
<span data-ttu-id="31979-134">imageTagAndImageID</span><span class="sxs-lookup"><span data-stu-id="31979-134">imageTagAndImageID</span></span> | <span data-ttu-id="31979-135">t</span><span class="sxs-lookup"><span data-stu-id="31979-135">t</span></span>
<span data-ttu-id="31979-136">이미지 컨텍스트</span><span class="sxs-lookup"><span data-stu-id="31979-136">imageContext</span></span> | <span data-ttu-id="31979-137">a</span><span class="sxs-lookup"><span data-stu-id="31979-137">a</span></span>
<span data-ttu-id="31979-138">imageElement</span><span class="sxs-lookup"><span data-stu-id="31979-138">imageElement</span></span> | <span data-ttu-id="31979-139">r</span><span class="sxs-lookup"><span data-stu-id="31979-139">r</span></span>

## <a name="impact-of-bundling-and-minification"></a><span data-ttu-id="31979-140">묶음 및 축소의 영향</span><span class="sxs-lookup"><span data-stu-id="31979-140">Impact of bundling and minification</span></span>

<span data-ttu-id="31979-141">다음 표에서 모든 자산을 개별적으로 나열 하 고 간단한 웹 페이지에서 묶음 및 축소를 사용 하 여 간의 몇 가지 중요 한 차이점을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="31979-141">The following table shows several important differences between listing all the assets individually and using bundling and minification on a simple web page:</span></span>

<span data-ttu-id="31979-142">작업</span><span class="sxs-lookup"><span data-stu-id="31979-142">Action</span></span> | <span data-ttu-id="31979-143">M B/으로</span><span class="sxs-lookup"><span data-stu-id="31979-143">With B/M</span></span> | <span data-ttu-id="31979-144">M B/없이</span><span class="sxs-lookup"><span data-stu-id="31979-144">Without B/M</span></span> | <span data-ttu-id="31979-145">변경</span><span class="sxs-lookup"><span data-stu-id="31979-145">Change</span></span>
--- | :---: | :---: | :---:
<span data-ttu-id="31979-146">파일 요청</span><span class="sxs-lookup"><span data-stu-id="31979-146">File Requests</span></span> |<span data-ttu-id="31979-147">7</span><span class="sxs-lookup"><span data-stu-id="31979-147">7</span></span> | <span data-ttu-id="31979-148">18</span><span class="sxs-lookup"><span data-stu-id="31979-148">18</span></span> | <span data-ttu-id="31979-149">157%</span><span class="sxs-lookup"><span data-stu-id="31979-149">157%</span></span>
<span data-ttu-id="31979-150">전송 KB</span><span class="sxs-lookup"><span data-stu-id="31979-150">KB Transferred</span></span> | <span data-ttu-id="31979-151">156</span><span class="sxs-lookup"><span data-stu-id="31979-151">156</span></span> | <span data-ttu-id="31979-152">264.68</span><span class="sxs-lookup"><span data-stu-id="31979-152">264.68</span></span> | <span data-ttu-id="31979-153">70%</span><span class="sxs-lookup"><span data-stu-id="31979-153">70%</span></span>
<span data-ttu-id="31979-154">로드 시간 (밀리초)</span><span class="sxs-lookup"><span data-stu-id="31979-154">Load Time (MS)</span></span> | <span data-ttu-id="31979-155">885</span><span class="sxs-lookup"><span data-stu-id="31979-155">885</span></span> | <span data-ttu-id="31979-156">2360</span><span class="sxs-lookup"><span data-stu-id="31979-156">2360</span></span> | <span data-ttu-id="31979-157">167%</span><span class="sxs-lookup"><span data-stu-id="31979-157">167%</span></span>

<span data-ttu-id="31979-158">보낸 바이트 브라우저 요청에 적용 되는 HTTP 헤더와 함께 매우 자세한 정보는 번들 소프트웨어 크게 감소를 했습니다.</span><span class="sxs-lookup"><span data-stu-id="31979-158">The bytes sent had a significant reduction with bundling as browsers are fairly verbose with the HTTP headers that they apply on requests.</span></span> <span data-ttu-id="31979-159">이 예제를 로컬로 실행 되지만 로드 시간이 상당히 개선을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="31979-159">The load time shows a big improvement, however this example was run locally.</span></span> <span data-ttu-id="31979-160">자산 묶음 및 축소를 사용 하 여 네트워크를 통해 전송 되 면 성능에 큰 향상 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="31979-160">You will get greater gains in performance when using bundling and minification with assets transferred over a network.</span></span>

## <a name="using-bundling-and-minification-in-a-project"></a><span data-ttu-id="31979-161">묶음 및 축소를 사용 하 여 프로젝트의</span><span class="sxs-lookup"><span data-stu-id="31979-161">Using bundling and minification in a project</span></span>

<span data-ttu-id="31979-162">MVC 프로젝트 템플릿은 제공는 `bundleconfig.json` 구성 파일을 각 번들에 대 한 옵션을 정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="31979-162">The MVC project template provides a `bundleconfig.json` configuration file which defines the options for each bundle.</span></span> <span data-ttu-id="31979-163">기본적으로 단일 번들 구성 사용자 지정 JavaScript에 대 한 정의 (`wwwroot/js/site.js`) 및 스타일 시트 (`wwwroot/css/site.css`) 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="31979-163">By default, a single bundle configuration is defined for the custom JavaScript (`wwwroot/js/site.js`) and Stylesheet (`wwwroot/css/site.css`) files.</span></span>

[!code-json[Main](../client-side/bundling-and-minification/samples/BuildBundlerMinifierExample/bundleconfig.json)]

<span data-ttu-id="31979-164">번들 옵션은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="31979-164">Bundle options include:</span></span>

* <span data-ttu-id="31979-165">outputFileName-출력 번들 파일의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="31979-165">outputFileName - name of the bundle file to output.</span></span> <span data-ttu-id="31979-166">상대 경로 포함할 수 있습니다는 `bundleconfig.json` 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="31979-166">Can contain a relative path from the `bundleconfig.json` file.</span></span> <span data-ttu-id="31979-167">**필수**</span><span class="sxs-lookup"><span data-stu-id="31979-167">**required**</span></span>
* <span data-ttu-id="31979-168">inputFiles-함께 번들로 묶는 파일의 배열입니다.</span><span class="sxs-lookup"><span data-stu-id="31979-168">inputFiles - array of files to bundle together.</span></span> <span data-ttu-id="31979-169">이들은 구성 파일에 상대 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="31979-169">These are relative paths to the configuration file.</span></span> <span data-ttu-id="31979-170">**선택적**, * 빈 출력 파일에 빈 값이 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="31979-170">**optional**, *an empty value results in an empty output file.</span></span> <span data-ttu-id="31979-171">[와일드 카드 사용](http://www.tldp.org/LDP/abs/html/globbingref.html) 패턴이 지원 됩니다.</span><span class="sxs-lookup"><span data-stu-id="31979-171">[globbing](http://www.tldp.org/LDP/abs/html/globbingref.html) patterns are supported.</span></span>
* <span data-ttu-id="31979-172">축소할-출력에 대 한 축소 옵션을 입력 합니다.</span><span class="sxs-lookup"><span data-stu-id="31979-172">minify - minification options for the output type.</span></span> <span data-ttu-id="31979-173">**선택적**, *기본값-`minify: { enabled: true }`*</span><span class="sxs-lookup"><span data-stu-id="31979-173">**optional**, *default - `minify: { enabled: true }`*</span></span>
  * <span data-ttu-id="31979-174">구성 옵션은 출력 파일 형식을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="31979-174">Configuration options are available per output file type.</span></span>
    * [<span data-ttu-id="31979-175">CSS Minifier입니다.</span><span class="sxs-lookup"><span data-stu-id="31979-175">CSS Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki/cssminifier)
    * [<span data-ttu-id="31979-176">JavaScript Minifier입니다.</span><span class="sxs-lookup"><span data-stu-id="31979-176">JavaScript Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki)
    * [<span data-ttu-id="31979-177">HTML Minifier입니다.</span><span class="sxs-lookup"><span data-stu-id="31979-177">HTML Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki)
* <span data-ttu-id="31979-178">includeInProject-프로젝트 파일 생성 된 파일을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="31979-178">includeInProject - add generated files to project file.</span></span> <span data-ttu-id="31979-179">**선택적**, *기본값-false*</span><span class="sxs-lookup"><span data-stu-id="31979-179">**optional**, *default - false*</span></span>
* <span data-ttu-id="31979-180">-sourcemap을 내보내면 해당 번들된 파일에 대 한 소스 맵을 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="31979-180">sourceMaps - generate source maps for the bundled file.</span></span> <span data-ttu-id="31979-181">**선택적**, *기본값-false*</span><span class="sxs-lookup"><span data-stu-id="31979-181">**optional**, *default - false*</span></span>

### <a name="visual-studio-2015--2017"></a><span data-ttu-id="31979-182">Visual Studio 2015 / 2017</span><span class="sxs-lookup"><span data-stu-id="31979-182">Visual Studio 2015 / 2017</span></span>

<span data-ttu-id="31979-183">열기 `bundleconfig.json` Visual Studio에서 사용자 환경에 설치 해야 합니다; 확장명이 없는 묻는 메시지가 표시 됩니다 하나이 파일 형식을 지원할 수 있는지를 제안 합니다.</span><span class="sxs-lookup"><span data-stu-id="31979-183">Open `bundleconfig.json` in Visual Studio, if your environment does not have the extension installed; a prompt is presented suggesting that there is one that could assist with this file type.</span></span>

![BuildBundlerMinifier 확장 제안](../client-side/bundling-and-minification/_static/bundler-extension-suggestion.png)

<span data-ttu-id="31979-185">확장 보기를 선택 하 고 설치는 **번들러 & Minifier** 확장 (Visual Studio가 필요 다시 시작)입니다.</span><span class="sxs-lookup"><span data-stu-id="31979-185">Select View Extensions, and install the **Bundler & Minifier** extension (Requires Visual Studio restart).</span></span>

![BuildBundlerMinifier 확장 제안](../client-side/bundling-and-minification/_static/view-extension.png)

<span data-ttu-id="31979-187">다시 시작이 완료 되 면 축소 및 클라이언트 쪽 자산 번들의 프로세스를 실행 하 여 빌드를 구성 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="31979-187">When the restart is complete, you need to configure the build to run the processes of minifying and bundling the client-side assets.</span></span> <span data-ttu-id="31979-188">마우스 오른쪽 단추로 클릭는 `bundleconfig.json` 파일을 선택 *빌드에 사용 번들... *.</span><span class="sxs-lookup"><span data-stu-id="31979-188">Right-click the `bundleconfig.json` file and select *Enable bundle on build...*.</span></span>

<span data-ttu-id="31979-189">프로젝트를 빌드할 및 `bundleconfig.json` 구성에 따라 출력 파일을 생성 하기 위해 빌드 프로세스에 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="31979-189">Build the project, and the `bundleconfig.json` is included in the build process to produce the output files based on the configuration.</span></span>

```console
1>------ Build started: Project: BuildBundlerMinifierExample, Configuration: Debug Any CPU ------
1>
1>Bundler: Begin processing bundleconfig.json
1>Bundler: Done processing bundleconfig.json
1>BuildBundlerMinifierExample -> C:\BuildBundlerMinifierExample\bin\Debug\netcoreapp1.1\BuildBundlerMinifierExample.dll
========== Build: 1 succeeded or up-to-date, 0 failed, 0 skipped ==========
```

### <a name="visual-studio-code-or-command-line"></a><span data-ttu-id="31979-190">Visual Studio Code 또는 명령줄</span><span class="sxs-lookup"><span data-stu-id="31979-190">Visual Studio Code or Command Line</span></span>

<span data-ttu-id="31979-191">Visual Studio 및 확장; GUI 제스처를 사용 하 여 묶음 및 축소 프로세스를 드라이브 그러나 동일한 기능을 사용할 수는 `dotnet` CLI 및 BuildBundlerMinifier NuGet 패키지 합니다.</span><span class="sxs-lookup"><span data-stu-id="31979-191">Visual Studio and the extension drive the bundling and minification process using GUI gestures; however, the same capabilities are available with the `dotnet` CLI and BuildBundlerMinifier NuGet package.</span></span>

<span data-ttu-id="31979-192">프로젝트에 NuGet 패키지를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="31979-192">Add the NuGet package to your project:</span></span>

```console
dotnet add package BuildBundlerMinifier
```

<span data-ttu-id="31979-193">종속성을 복원 합니다.</span><span class="sxs-lookup"><span data-stu-id="31979-193">Restore the dependencies:</span></span>

```console
dotnet restore
```

<span data-ttu-id="31979-194">응용 프로그램을 빌드하십시오.</span><span class="sxs-lookup"><span data-stu-id="31979-194">Build the app:</span></span>

```console
dotnet build
```

<span data-ttu-id="31979-195">빌드 명령의 출력은 축소 및/또는 구성에 따라 번들의 결과 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="31979-195">The output from the build command shows the results of the minification and/or bundling according to what is configured.</span></span>

```console
Microsoft (R) Build Engine version 15.1.545.13942
Copyright (C) Microsoft Corporation. All rights reserved.


  Bundler: Begin processing bundleconfig.json
     Minified wwwroot/css/site.min.css
  Bundler: Done processing bundleconfig.json
  BuildBundlerMinifierExample -> /BuildBundlerMinifierExample/bin/Debug/netcoreapp1.0/BuildBundlerMinifierExample.dll
```

## <a name="adding-files"></a><span data-ttu-id="31979-196">파일 추가</span><span class="sxs-lookup"><span data-stu-id="31979-196">Adding files</span></span>

<span data-ttu-id="31979-197">이 예제에서는 CSS 파일을 추가로 호출 추가 됩니다 `custom.css` 묶음 및 축소를 위해 구성 및 `site.css`단일에서 결과, `site.min.css`합니다.</span><span class="sxs-lookup"><span data-stu-id="31979-197">In this example, an additional CSS file is added called `custom.css` and configured for bundling and minification with `site.css`, resulting in a single `site.min.css`.</span></span>

<span data-ttu-id="31979-198">custom.css</span><span class="sxs-lookup"><span data-stu-id="31979-198">custom.css</span></span>

```css
.about, [role=main], [role=complementary]
{
    margin-top: 60px;
}

footer
{
    margin-top: 10px;
}
```

<span data-ttu-id="31979-199">상대 경로를 추가할 `bundleconfig.json`합니다.</span><span class="sxs-lookup"><span data-stu-id="31979-199">Add the relative path to `bundleconfig.json`.</span></span>

[!code-json[Main](../client-side/bundling-and-minification/samples/BuildBundlerMinifierExample/bundleconfig2.json)]

> [!NOTE]
> <span data-ttu-id="31979-200">또는 와일드 카드 사용 패턴을 사용할 수 없습니다- `"inputFiles": ["wwwroot/**/*(*.css|!(*.min.css)"]` 는 모든 CSS 파일을 가져오고 제외 되어 축소 된 파일 패턴입니다.</span><span class="sxs-lookup"><span data-stu-id="31979-200">Alternatively, the globbing pattern could be used - `"inputFiles": ["wwwroot/**/*(*.css|!(*.min.css)"]` which gets all CSS files and excludes the minified file pattern.</span></span>

<span data-ttu-id="31979-201">응용 프로그램을 빌드합니다 열면 및 `site.min.css`는 이제 알 수의 내용을 `custom.css` 파일의 끝에 추가 된 합니다.</span><span class="sxs-lookup"><span data-stu-id="31979-201">Build the application and if you open `site.min.css`, you'll now notice that contents of `custom.css` has been appended to the end of the file.</span></span>

## <a name="controlling-bundling-and-minification"></a><span data-ttu-id="31979-202">묶음 및 축소를 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="31979-202">Controlling bundling and minification</span></span>

<span data-ttu-id="31979-203">일반적으로 프로덕션 환경에만 응용 프로그램의 번들로 묶은 및 축소 된 파일을 사용 하려면.</span><span class="sxs-lookup"><span data-stu-id="31979-203">In general, you want to use the bundled and minified files of your app only in a production environment.</span></span> <span data-ttu-id="31979-204">개발 하는 동안 앱을 쉽게 디버깅할 수 있도록 원본 파일을 사용 하려면.</span><span class="sxs-lookup"><span data-stu-id="31979-204">During development, you want to use your original files so your app is easier to debug.</span></span>

<span data-ttu-id="31979-205">스크립트와 레이아웃 페이지에서 환경 태그 도우미를 사용 하 여 페이지에 포함할 CSS 파일을 지정할 수 있습니다 (참조 [태그 도우미](../mvc/views/tag-helpers/index.md)).</span><span class="sxs-lookup"><span data-stu-id="31979-205">You can specify which scripts and CSS files to include in your pages using the environment tag helper in your layout pages (see [Tag Helpers](../mvc/views/tag-helpers/index.md)).</span></span> <span data-ttu-id="31979-206">환경 태그 도우미 특정 환경에서 실행 하는 경우 해당 콘텐츠를 렌더링만 합니다.</span><span class="sxs-lookup"><span data-stu-id="31979-206">The environment tag helper will only render its contents when running in specific environments.</span></span> <span data-ttu-id="31979-207">참조 [여러 환경 작업](../fundamentals/environments.md) 지정 현재 환경에 대 한 합니다.</span><span class="sxs-lookup"><span data-stu-id="31979-207">See [Working with Multiple Environments](../fundamentals/environments.md) for details on specifying the current environment.</span></span>

<span data-ttu-id="31979-208">실행할 때 다음과 같은 환경 태그 처리 되지 않은 CSS 파일을 렌더링 합니다는 `Development` 환경:</span><span class="sxs-lookup"><span data-stu-id="31979-208">The following environment tag will render the unprocessed CSS files when running in the `Development` environment:</span></span>

[!code-html[Main](../client-side/bundling-and-minification/samples/BuildBundlerMinifierExample/Views/Shared/_Layout.cshtml?highlight=3&range=9-12)]

<span data-ttu-id="31979-209">실행 하는 경우에이 환경 태그는 번들 및 축소 된 CSS 파일을 렌더링 합니다 `Production` 또는 `Staging`:</span><span class="sxs-lookup"><span data-stu-id="31979-209">This environment tag will render the bundled and minified CSS files only when running in `Production` or `Staging`:</span></span>

[!code-html[Main](../client-side/bundling-and-minification/samples/BuildBundlerMinifierExample/Views/Shared/_Layout.cshtml?highlight=5&range=13-18)]

## <a name="consuming-bundleconfigjson-from-gulp"></a><span data-ttu-id="31979-210">Bundleconfig.json Gulp에서 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="31979-210">Consuming bundleconfig.json from Gulp</span></span>

<span data-ttu-id="31979-211">예: 이미지 처리, 캐시 busting, CDN assest 처리 등의 추가 프로세스를 해야 하는 응용 프로그램 묶음 및 축소 과정 번들 및 Minify 프로세스 Gulp를 변환할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="31979-211">If your app bundling and minification workflow requires additional processes such as image processing, cache busting, CDN assest processing, etc., then you can convert the Bundle and Minify process to Gulp.</span></span>

> [!NOTE]
> <span data-ttu-id="31979-212">변환 옵션 Visual Studio 2015 및 2017 에서만 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="31979-212">Conversion option only available in Visual Studio 2015 and 2017.</span></span>

<span data-ttu-id="31979-213">마우스 오른쪽 단추로 클릭는 `bundleconfig.json` 선택 **Gulp 변환... **. 자동으로 생성 됩니다는 `gulpfile.js` 하 고 필요한 npm 패키지를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="31979-213">Right-click the `bundleconfig.json` and select **Convert to Gulp...**. This will generate the `gulpfile.js` and install the necessary npm packages.</span></span>

![Gulp를 변환](../client-side/bundling-and-minification/_static/convert-togulp.png)

<span data-ttu-id="31979-215">`gulpfile.js` 읽기 생성 되는 `bundleconfig.json` 파일 구성을 위해, 따라서를 계속 하려면는 입/출력 및 설정에 사용할 합니다.</span><span class="sxs-lookup"><span data-stu-id="31979-215">The `gulpfile.js` produced reads the `bundleconfig.json` file for the configuration, therefore it can continue to be used for the inputs/outputs and settings.</span></span>

[!code-javascript[Main](../client-side/bundling-and-minification/samples/BuildBundlerMinifierExample/gulpfile.js)]

<span data-ttu-id="31979-216">Gulp를 Visual Studio 2017에서 프로젝트가 빌드될 때 사용 하도록 설정 하려면 *.csproj 파일에 다음을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="31979-216">To enable Gulp when the project builds in Visual Studio 2017, add the following to the *.csproj file:</span></span>

```xml
<Target Name="MyPreCompileTarget" BeforeTargets="Build">
    <Exec Command="gulp min" />
</Target>
```

<span data-ttu-id="31979-217">Visual Studio 2015에서 프로젝트가 빌드될 때 Gulp를 사용 하려면 다음을 추가 `project.json` 파일:</span><span class="sxs-lookup"><span data-stu-id="31979-217">To enable Gulp when the project builds in Visual Studio 2015, add the following to the `project.json` file:</span></span>

```json
"scripts": {
    "precompile": "gulp min"
}
```

## <a name="additional-resources"></a><span data-ttu-id="31979-218">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="31979-218">Additional resources</span></span>

* [<span data-ttu-id="31979-219">Gulp 사용</span><span class="sxs-lookup"><span data-stu-id="31979-219">Using Gulp</span></span>](using-gulp.md)
* [<span data-ttu-id="31979-220">Grunt 사용</span><span class="sxs-lookup"><span data-stu-id="31979-220">Using Grunt</span></span>](using-grunt.md)
* [<span data-ttu-id="31979-221">여러 환경 사용</span><span class="sxs-lookup"><span data-stu-id="31979-221">Working with Multiple Environments</span></span>](../fundamentals/environments.md)
* [<span data-ttu-id="31979-222">태그 도우미</span><span class="sxs-lookup"><span data-stu-id="31979-222">Tag Helpers</span></span>](../mvc/views/tag-helpers/index.md)
