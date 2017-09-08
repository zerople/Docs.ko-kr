---
title: "ASP.NET Core에 정적 파일 작업"
author: rick-anderson
description: "정적 파일 작업"
keywords: "ASP.NET Core, 정적 파일, 정적 자산, HTML, CSS, JavaScript"
ms.author: riande
manager: wpickett
ms.date: 4/07/2017
ms.topic: article
ms.assetid: e32245c7-4eee-4831-bd2e-915dbf9f5f70
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/static-files
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ea6c180332dd5ab3a7238dcd73a4a1c8534c6243
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/11/2017
---
# <a name="introduction-to-working-with-static-files-in-aspnet-core"></a><span data-ttu-id="19784-104">ASP.NET Core에서 정적 파일로 작업 소개</span><span class="sxs-lookup"><span data-stu-id="19784-104">Introduction to working with static files in ASP.NET Core</span></span>

<a name=fundamentals-static-files></a>

<span data-ttu-id="19784-105">으로 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="19784-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="19784-106">HTML, CSS, 이미지 및 JavaScript와 같은 정적 파일은 ASP.NET Core 응용 프로그램을 클라이언트에 직접 제공할 수 있는 자산입니다.</span><span class="sxs-lookup"><span data-stu-id="19784-106">Static files, such as HTML, CSS, image, and JavaScript, are assets that an ASP.NET Core app can serve directly to clients.</span></span>

[<span data-ttu-id="19784-107">샘플 코드 보기 또는 다운로드</span><span class="sxs-lookup"><span data-stu-id="19784-107">View or download sample code</span></span>](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/static-files/sample)

## <a name="serving-static-files"></a><span data-ttu-id="19784-108">정적 파일 처리</span><span class="sxs-lookup"><span data-stu-id="19784-108">Serving static files</span></span>

<span data-ttu-id="19784-109">정적 파일은 일반적으로 %programfiles%\microsoft는 `web root` (*\<콘텐츠 루트 > / wwwroot*) 폴더입니다.</span><span class="sxs-lookup"><span data-stu-id="19784-109">Static files are typically located in the `web root` (*\<content-root>/wwwroot*) folder.</span></span> <span data-ttu-id="19784-110">참조 [콘텐츠 루트](xref:fundamentals/index#content-root) 및 [웹 루트](xref:fundamentals/index#web-root) 자세한 정보에 대 한 합니다.</span><span class="sxs-lookup"><span data-stu-id="19784-110">See [Content root](xref:fundamentals/index#content-root) and [Web root](xref:fundamentals/index#web-root) for more information.</span></span> <span data-ttu-id="19784-111">콘텐츠 루트 현재 디렉터리를 일반적으로 설정 되도록 프로젝트의 `web root` 개발에서 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="19784-111">You generally set the content root to be the current directory so that your project's `web root` will be found while in development.</span></span>

<span data-ttu-id="19784-112">[!code-csharp[Main](../common/samples/WebApplication1/Program.cs?highlight=5&start=12&end=22)]</span><span class="sxs-lookup"><span data-stu-id="19784-112">[!code-csharp[Main](../common/samples/WebApplication1/Program.cs?highlight=5&start=12&end=22)]</span></span>

<span data-ttu-id="19784-113">아래의 모든 폴더에 정적 파일을 저장할 수 있습니다는 `web root` 해당 루트의 상대 경로 사용 하 여 액세스 하 고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="19784-113">Static files can be stored in any folder under the `web root` and accessed with a relative path to that root.</span></span> <span data-ttu-id="19784-114">예를 들어 Visual Studio를 사용 하 여 기본 웹 응용 프로그램 프로젝트를 만들 때 여러 폴더가 있는 내에서 만든는 *wwwroot* 폴더- *css*, *이미지*, 및 *js*합니다.</span><span class="sxs-lookup"><span data-stu-id="19784-114">For example, when you create a default Web application project using Visual Studio, there are several folders created within the *wwwroot*  folder - *css*, *images*, and *js*.</span></span> <span data-ttu-id="19784-115">URI에 있는 이미지에 액세스 하기는 *이미지* 하위 폴더:</span><span class="sxs-lookup"><span data-stu-id="19784-115">The URI to access an image in the *images* subfolder:</span></span>

* `http://<app>/images/<imageFileName>`
* `http://localhost:9189/images/banner3.svg`

<span data-ttu-id="19784-116">정적 파일 처리에 대 한 순서 대로 구성 해야는 [미들웨어](middleware.md) 파이프라인에 정적 파일을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="19784-116">In order for static files to be served, you must configure the [Middleware](middleware.md) to add static files to the pipeline.</span></span> <span data-ttu-id="19784-117">정적 파일 미들웨어에서 종속성을 추가 하 여 구성할 수 있습니다는 *Microsoft.AspNetCore.StaticFiles* 패키지를 프로젝트와 다음 호출에서 `UseStaticFiles` 에서 확장 메서드 `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="19784-117">The static file middleware can be configured by adding a dependency on the *Microsoft.AspNetCore.StaticFiles* package to your project and then calling the `UseStaticFiles` extension method from `Startup.Configure`:</span></span>

<span data-ttu-id="19784-118">[!code-csharp[Main](../fundamentals/static-files/sample/StartupStaticFiles.cs?highlight=3&name=snippet1)]</span><span class="sxs-lookup"><span data-stu-id="19784-118">[!code-csharp[Main](../fundamentals/static-files/sample/StartupStaticFiles.cs?highlight=3&name=snippet1)]</span></span>

<span data-ttu-id="19784-119">`app.UseStaticFiles();`에 있는 파일을 사용 하면 `web root` (*wwwroot* 기본적으로) servable 합니다.</span><span class="sxs-lookup"><span data-stu-id="19784-119">`app.UseStaticFiles();` makes the files in `web root` (*wwwroot* by default) servable.</span></span> <span data-ttu-id="19784-120">나중에 다른 디렉터리 내용을와 servable 확인 하는 방법을 보여 드리 려 `UseStaticFiles`합니다.</span><span class="sxs-lookup"><span data-stu-id="19784-120">Later I'll show how to make other directory contents servable with `UseStaticFiles`.</span></span>

<span data-ttu-id="19784-121">"Microsoft.AspNetCore.StaticFiles" NuGet 패키지를 포함 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="19784-121">You must include the NuGet package "Microsoft.AspNetCore.StaticFiles".</span></span>

> [!NOTE]
> <span data-ttu-id="19784-122">`web root`기본적으로 *wwwroot* 있지만 디렉터리를 설정할 수는 `web root` ड ि र ॅ `UseWebRoot`합니다.</span><span class="sxs-lookup"><span data-stu-id="19784-122">`web root` defaults to the *wwwroot* directory, but you can set the `web root` directory with `UseWebRoot`.</span></span>

<span data-ttu-id="19784-123">있다고 가정 하면 외부 사용 하려는 정적 파일이 있는 프로젝트 계층 구조는 `web root`합니다.</span><span class="sxs-lookup"><span data-stu-id="19784-123">Suppose you have a project hierarchy where the static files you wish to serve are outside the `web root`.</span></span> <span data-ttu-id="19784-124">예:</span><span class="sxs-lookup"><span data-stu-id="19784-124">For example:</span></span>

* <span data-ttu-id="19784-125">wwwroot</span><span class="sxs-lookup"><span data-stu-id="19784-125">wwwroot</span></span>
  * <span data-ttu-id="19784-126">css</span><span class="sxs-lookup"><span data-stu-id="19784-126">css</span></span>
  * <span data-ttu-id="19784-127">이미지</span><span class="sxs-lookup"><span data-stu-id="19784-127">images</span></span>
  * <span data-ttu-id="19784-128">...</span><span class="sxs-lookup"><span data-stu-id="19784-128">...</span></span>
* <span data-ttu-id="19784-129">MyStaticFiles</span><span class="sxs-lookup"><span data-stu-id="19784-129">MyStaticFiles</span></span>
  * <span data-ttu-id="19784-130">test.png</span><span class="sxs-lookup"><span data-stu-id="19784-130">test.png</span></span>

<span data-ttu-id="19784-131">에 액세스 하려면 요청에 대 한 *test.png*, 정적 파일 미들웨어를 다음과 같이 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="19784-131">For a request to access *test.png*, configure the static files middleware as follows:</span></span>

<span data-ttu-id="19784-132">[!code-csharp[Main](../fundamentals/static-files/sample/StartupTwoStaticFiles.cs?highlight=5,6,7,8,9,10&name=snippet1)]</span><span class="sxs-lookup"><span data-stu-id="19784-132">[!code-csharp[Main](../fundamentals/static-files/sample/StartupTwoStaticFiles.cs?highlight=5,6,7,8,9,10&name=snippet1)]</span></span>

<span data-ttu-id="19784-133">요청을 `http://<app>/StaticFiles/test.png` 사용할는 *test.png* 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="19784-133">A request to `http://<app>/StaticFiles/test.png` will serve the *test.png* file.</span></span>

<span data-ttu-id="19784-134">`StaticFileOptions()`응답 헤더를 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="19784-134">`StaticFileOptions()` can set response headers.</span></span> <span data-ttu-id="19784-135">아래 코드 정적 파일에서 처리를 설정 하는 예를 들어는 *wwwroot* 폴더 및 집합은 `Cache-Control` 헤더를 10 분 (600 초) 동안 공개적으로 캐시할 수 있도록:</span><span class="sxs-lookup"><span data-stu-id="19784-135">For example, the code below sets up static file serving from the *wwwroot* folder and sets the `Cache-Control` header to make them publicly cacheable for 10 minutes (600 seconds):</span></span>

<span data-ttu-id="19784-136">[!code-csharp[Main](../fundamentals/static-files/sample/StartupAddHeader.cs?name=snippet1)]</span><span class="sxs-lookup"><span data-stu-id="19784-136">[!code-csharp[Main](../fundamentals/static-files/sample/StartupAddHeader.cs?name=snippet1)]</span></span>

![추가 된 캐시 제어 헤더를 보여 주는 응답 헤더](static-files/_static/add-header.png)

## <a name="static-file-authorization"></a><span data-ttu-id="19784-138">정적 파일 권한 부여</span><span class="sxs-lookup"><span data-stu-id="19784-138">Static file authorization</span></span>

<span data-ttu-id="19784-139">정적 파일 모듈에서는 **없는** 권한 부여 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="19784-139">The static file module provides **no** authorization checks.</span></span> <span data-ttu-id="19784-140">모든 파일에서 제공 비롯 *wwwroot* 공개적으로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="19784-140">Any files served by it, including those under *wwwroot* are publicly available.</span></span> <span data-ttu-id="19784-141">파일을 제공 하려면 권한 부여 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="19784-141">To serve files based on authorization:</span></span>

* <span data-ttu-id="19784-142">외부에 보관해 두면 *wwwroot* 및 정적 파일 미들웨어에 액세스할 수 있는 모든 디렉터리 **및**</span><span class="sxs-lookup"><span data-stu-id="19784-142">Store them outside of *wwwroot* and any directory accessible to the static file middleware **and**</span></span>

* <span data-ttu-id="19784-143">반환 되는 컨트롤러 동작을 통해 역할는 `FileResult` 권한 부여 적용 되는 위치</span><span class="sxs-lookup"><span data-stu-id="19784-143">Serve them through a controller action, returning a `FileResult` where authorization is applied</span></span>

## <a name="enabling-directory-browsing"></a><span data-ttu-id="19784-144">디렉터리 검색을 사용 하도록 설정</span><span class="sxs-lookup"><span data-stu-id="19784-144">Enabling directory browsing</span></span>

<span data-ttu-id="19784-145">디렉터리 검색 웹 응용 프로그램의 사용자가 지정된 된 디렉터리 내에서 파일과 디렉터리의 목록을 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="19784-145">Directory browsing allows the user of your web app to see a list of directories and files within a specified directory.</span></span> <span data-ttu-id="19784-146">보안상의 이유로 기본적으로 비활성화 되어 디렉터리 검색 (참조 [고려 사항](#considerations)).</span><span class="sxs-lookup"><span data-stu-id="19784-146">Directory browsing is disabled by default for security reasons (see [Considerations](#considerations)).</span></span> <span data-ttu-id="19784-147">디렉터리 검색을 사용 하려면 호출는 `UseDirectoryBrowser` 에서 확장 메서드 `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="19784-147">To enable directory browsing, call the `UseDirectoryBrowser` extension method from  `Startup.Configure`:</span></span>

<span data-ttu-id="19784-148">[!code-csharp[Main](static-files/sample/StartupBrowse.cs?name=snippet1)]</span><span class="sxs-lookup"><span data-stu-id="19784-148">[!code-csharp[Main](static-files/sample/StartupBrowse.cs?name=snippet1)]</span></span>

<span data-ttu-id="19784-149">호출 하 여 필요한 서비스를 추가 하 고 `AddDirectoryBrowser` 에서 확장 메서드 `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="19784-149">And add required services by calling `AddDirectoryBrowser` extension method from `Startup.ConfigureServices`:</span></span>

<span data-ttu-id="19784-150">[!code-csharp[Main](static-files/sample/StartupBrowse.cs?name=snippet2)]</span><span class="sxs-lookup"><span data-stu-id="19784-150">[!code-csharp[Main](static-files/sample/StartupBrowse.cs?name=snippet2)]</span></span>

<span data-ttu-id="19784-151">위의 코드 디렉터리를 검색할 수는 *wwwroot/이미지* URL http://를 사용 하 여 폴더\<앱 > / 각 파일 및 폴더에 대 한 링크와 MyImages:</span><span class="sxs-lookup"><span data-stu-id="19784-151">The code above allows directory browsing of the *wwwroot/images* folder using the URL http://\<app>/MyImages, with links to each file and folder:</span></span>

![디렉터리 검색](static-files/_static/dir-browse.png)

<span data-ttu-id="19784-153">참조 [고려 사항](#considerations) 검색을 사용 하도록 설정할 때는 보안 위험에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="19784-153">See [Considerations](#considerations) on the security risks when enabling browsing.</span></span>

<span data-ttu-id="19784-154">두 참고 `app.UseStaticFiles` 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="19784-154">Note the two `app.UseStaticFiles` calls.</span></span> <span data-ttu-id="19784-155">CSS, 이미지 및 JavaScript에서 제공 하는 데 필요한 첫 번째는 *wwwroot* 폴더 및 디렉터리의 검색에 대 한 두 번째 호출에서 *wwwroot/이미지* URL http://를 사용 하 여 폴더\<응용 프로그램 > / MyImages:</span><span class="sxs-lookup"><span data-stu-id="19784-155">The first one is required to serve the CSS, images and JavaScript in the *wwwroot* folder, and the second call for directory browsing of the *wwwroot/images* folder using the URL http://\<app>/MyImages:</span></span>

<span data-ttu-id="19784-156">[!code-csharp[Main](static-files/sample/StartupBrowse.cs?highlight=3,5&name=snippet1)]</span><span class="sxs-lookup"><span data-stu-id="19784-156">[!code-csharp[Main](static-files/sample/StartupBrowse.cs?highlight=3,5&name=snippet1)]</span></span>

## <a name="serving-a-default-document"></a><span data-ttu-id="19784-157">기본 문서를 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="19784-157">Serving a default document</span></span>

<span data-ttu-id="19784-158">기본 홈 페이지를 설정 하면 사이트 방문자 사이트를 방문할 때 시작 하는 곳입니다.</span><span class="sxs-lookup"><span data-stu-id="19784-158">Setting a default home page gives site visitors a place to start when visiting your site.</span></span> <span data-ttu-id="19784-159">사용자에 게 URI를 정규화 하지 않고 기본 페이지를 제공 하도록 웹 앱에 대 한 순서 대로 호출는 `UseDefaultFiles` 에서 확장 메서드 `Startup.Configure` 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="19784-159">In order for your Web app to serve a default page without the user having to fully qualify the URI, call the `UseDefaultFiles` extension method from `Startup.Configure` as follows.</span></span>

<span data-ttu-id="19784-160">[!code-csharp[Main](../fundamentals/static-files/sample/StartupEmpty.cs?highlight=3&name=snippet1)]</span><span class="sxs-lookup"><span data-stu-id="19784-160">[!code-csharp[Main](../fundamentals/static-files/sample/StartupEmpty.cs?highlight=3&name=snippet1)]</span></span>

> [!NOTE]
> <span data-ttu-id="19784-161">`UseDefaultFiles`먼저 호출 해야 `UseStaticFiles` 기본 파일 역할을 합니다.</span><span class="sxs-lookup"><span data-stu-id="19784-161">`UseDefaultFiles` must be called before `UseStaticFiles` to serve the default file.</span></span> <span data-ttu-id="19784-162">`UseDefaultFiles`파일을 처리 하지 않는 실제로 있는 URL 다시 작성기입니다.</span><span class="sxs-lookup"><span data-stu-id="19784-162">`UseDefaultFiles` is a URL re-writer that doesn't actually serve the file.</span></span> <span data-ttu-id="19784-163">정적 파일 미들웨어를 사용 하도록 설정 해야 합니다 (`UseStaticFiles`)에이 파일을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="19784-163">You must enable the static file middleware (`UseStaticFiles`) to serve the file.</span></span>

<span data-ttu-id="19784-164">와 `UseDefaultFiles`, 폴더에 대 한 요청에 대 한 검색 합니다.</span><span class="sxs-lookup"><span data-stu-id="19784-164">With `UseDefaultFiles`, requests to a folder will search for:</span></span>

* <span data-ttu-id="19784-165">default.htm</span><span class="sxs-lookup"><span data-stu-id="19784-165">default.htm</span></span>
* <span data-ttu-id="19784-166">default.html</span><span class="sxs-lookup"><span data-stu-id="19784-166">default.html</span></span>
* <span data-ttu-id="19784-167">index.htm</span><span class="sxs-lookup"><span data-stu-id="19784-167">index.htm</span></span>
* <span data-ttu-id="19784-168">index.html</span><span class="sxs-lookup"><span data-stu-id="19784-168">index.html</span></span>

<span data-ttu-id="19784-169">첫 번째 파일을 목록에서 찾을 수는 (하지만 요청 된 URI를 표시 하도록 브라우저 URL 계속)의 정규화 된 URI 요청이 경우 처럼 처리 됩니다.</span><span class="sxs-lookup"><span data-stu-id="19784-169">The first file found from the list will be served as if the request was the fully qualified URI (although the browser URL will continue to show the URI requested).</span></span>

<span data-ttu-id="19784-170">다음 코드에서는 기본 파일 이름을 변경 하는 방법을 보여 줍니다. *mydefault.html*합니다.</span><span class="sxs-lookup"><span data-stu-id="19784-170">The following code shows how to change the default file name to *mydefault.html*.</span></span>

<span data-ttu-id="19784-171">[!code-csharp[Main](static-files/sample/StartupDefault.cs?name=snippet1)]</span><span class="sxs-lookup"><span data-stu-id="19784-171">[!code-csharp[Main](static-files/sample/StartupDefault.cs?name=snippet1)]</span></span>

## <a name="usefileserver"></a><span data-ttu-id="19784-172">UseFileServer</span><span class="sxs-lookup"><span data-stu-id="19784-172">UseFileServer</span></span>

<span data-ttu-id="19784-173">`UseFileServer`기능을 결합 `UseStaticFiles`, `UseDefaultFiles`, 및 `UseDirectoryBrowser`합니다.</span><span class="sxs-lookup"><span data-stu-id="19784-173">`UseFileServer` combines the functionality of `UseStaticFiles`, `UseDefaultFiles`, and `UseDirectoryBrowser`.</span></span>

<span data-ttu-id="19784-174">다음 코드를 통해 정적 파일 및 기본 파일 서비스 되도록 하지만 디렉터리 검색을 허용 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="19784-174">The following code enables static files and the default file to be served, but does not allow directory browsing:</span></span>

```csharp
app.UseFileServer();
   ```

<span data-ttu-id="19784-175">다음 코드를 통해 정적 파일, 기본 파일 및 디렉터리 검색:</span><span class="sxs-lookup"><span data-stu-id="19784-175">The following code enables static files, default files and  directory browsing:</span></span>

```csharp
app.UseFileServer(enableDirectoryBrowsing: true);
   ```

<span data-ttu-id="19784-176">참조 [고려 사항](#considerations) 검색을 사용 하도록 설정할 때는 보안 위험에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="19784-176">See [Considerations](#considerations) on the security risks when enabling browsing.</span></span> <span data-ttu-id="19784-177">와 마찬가지로 `UseStaticFiles`, `UseDefaultFiles`, 및 `UseDirectoryBrowser`외부에 있는 파일을 제공 하려는 경우는 `web root`를 인스턴스화하고 구성는 `FileServerOptions` 매개 변수로 전달 하는 개체 `UseFileServer`합니다.</span><span class="sxs-lookup"><span data-stu-id="19784-177">As with `UseStaticFiles`, `UseDefaultFiles`, and `UseDirectoryBrowser`, if you wish to serve files that exist outside the `web root`, you instantiate and configure an `FileServerOptions` object that you pass as a parameter to `UseFileServer`.</span></span> <span data-ttu-id="19784-178">웹 앱의 다음 디렉터리 계층 구조를 예로 들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="19784-178">For example, given the following directory hierarchy in your Web app:</span></span>

* <span data-ttu-id="19784-179">wwwroot</span><span class="sxs-lookup"><span data-stu-id="19784-179">wwwroot</span></span>

  * <span data-ttu-id="19784-180">css</span><span class="sxs-lookup"><span data-stu-id="19784-180">css</span></span>

  * <span data-ttu-id="19784-181">이미지</span><span class="sxs-lookup"><span data-stu-id="19784-181">images</span></span>

  * <span data-ttu-id="19784-182">...</span><span class="sxs-lookup"><span data-stu-id="19784-182">...</span></span>

* <span data-ttu-id="19784-183">MyStaticFiles</span><span class="sxs-lookup"><span data-stu-id="19784-183">MyStaticFiles</span></span>

  * <span data-ttu-id="19784-184">test.png</span><span class="sxs-lookup"><span data-stu-id="19784-184">test.png</span></span>

  * <span data-ttu-id="19784-185">default.html</span><span class="sxs-lookup"><span data-stu-id="19784-185">default.html</span></span>

<span data-ttu-id="19784-186">위의 계층 예제를 사용 하려는 경우도 정적 파일, 기본 파일 및 검색을 사용 하도록 설정 된 `MyStaticFiles` 디렉터리입니다.</span><span class="sxs-lookup"><span data-stu-id="19784-186">Using the hierarchy example above, you might want to enable static files, default files, and browsing for the `MyStaticFiles` directory.</span></span> <span data-ttu-id="19784-187">다음 코드 조각에서 한 번 호출 하 여 수행 됩니다 하는 `FileServerOptions`합니다.</span><span class="sxs-lookup"><span data-stu-id="19784-187">In the following code snippet, that is accomplished with a single call to `FileServerOptions`.</span></span>

<span data-ttu-id="19784-188">[!code-csharp[Main](static-files/sample/StartupUseFileServer.cs?highlight=5,6,7,8,9,10,11&name=snippet1)]</span><span class="sxs-lookup"><span data-stu-id="19784-188">[!code-csharp[Main](static-files/sample/StartupUseFileServer.cs?highlight=5,6,7,8,9,10,11&name=snippet1)]</span></span>

<span data-ttu-id="19784-189">경우 `enableDirectoryBrowsing` 로 설정 된 `true` 호출에 필요한 `AddDirectoryBrowser` 에서 확장 메서드 `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="19784-189">If `enableDirectoryBrowsing` is set to `true` you are required to call `AddDirectoryBrowser` extension method from  `Startup.ConfigureServices`:</span></span>

<span data-ttu-id="19784-190">[!code-csharp[Main](static-files/sample/StartupUseFileServer.cs?name=snippet2)]</span><span class="sxs-lookup"><span data-stu-id="19784-190">[!code-csharp[Main](static-files/sample/StartupUseFileServer.cs?name=snippet2)]</span></span>

<span data-ttu-id="19784-191">파일 계층 구조 및 위의 코드를 사용 하 여:</span><span class="sxs-lookup"><span data-stu-id="19784-191">Using the file hierarchy and code above:</span></span>

| <span data-ttu-id="19784-192">URI</span><span class="sxs-lookup"><span data-stu-id="19784-192">URI</span></span>            |                             <span data-ttu-id="19784-193">응답</span><span class="sxs-lookup"><span data-stu-id="19784-193">Response</span></span>  |
| ------- | ------|
| `http://<app>/StaticFiles/test.png`    |      <span data-ttu-id="19784-194">MyStaticFiles/test.png</span><span class="sxs-lookup"><span data-stu-id="19784-194">MyStaticFiles/test.png</span></span> |
| `http://<app>/StaticFiles`              |     <span data-ttu-id="19784-195">MyStaticFiles/default.html</span><span class="sxs-lookup"><span data-stu-id="19784-195">MyStaticFiles/default.html</span></span> |

<span data-ttu-id="19784-196">명명 된 파일이 기본값이 있는 경우는 *MyStaticFiles* 디렉터리, http://\<앱 > StaticFiles 클릭 가능한 링크와 함께 나열 하는 디렉터리를 반환 합니다. /:</span><span class="sxs-lookup"><span data-stu-id="19784-196">If no default named files are in the *MyStaticFiles* directory, http://\<app>/StaticFiles returns the directory listing with clickable links:</span></span>

![정적 파일 목록](static-files/_static/db2.PNG)

> [!NOTE]
> <span data-ttu-id="19784-198">`UseDefaultFiles`및 `UseDirectoryBrowser` url http:// 걸립니다\<앱 >는 후행 슬래시 및 원인 없이 StaticFiles http:// 리디렉션됩니다 클라이언트 쪽 /\<응용 프로그램 > /StaticFiles/ (후행 슬래시 추가).</span><span class="sxs-lookup"><span data-stu-id="19784-198">`UseDefaultFiles` and `UseDirectoryBrowser` will take the url http://\<app>/StaticFiles without the trailing slash and cause a client side redirect to http://\<app>/StaticFiles/ (adding the trailing slash).</span></span> <span data-ttu-id="19784-199">문서 내에서 후행 슬래시 상대 Url 없이 올바르지 않게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="19784-199">Without the trailing slash relative URLs within the documents would be incorrect.</span></span>

## <a name="fileextensioncontenttypeprovider"></a><span data-ttu-id="19784-200">FileExtensionContentTypeProvider</span><span class="sxs-lookup"><span data-stu-id="19784-200">FileExtensionContentTypeProvider</span></span>

<span data-ttu-id="19784-201">`FileExtensionContentTypeProvider` 클래스 파일 확장명이 MIME 콘텐츠 형식이 매핑되는 컬렉션을 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="19784-201">The `FileExtensionContentTypeProvider` class contains a  collection that maps file extensions to MIME content types.</span></span> <span data-ttu-id="19784-202">다음 샘플에서는 여러 파일 확장명이 알려진된 MIME 형식에 등록 된, ".rtf" 대신 표시 되며 ".mp4" 제거 됩니다.</span><span class="sxs-lookup"><span data-stu-id="19784-202">In the following sample, several file extensions are registered to known MIME types, the ".rtf" is replaced, and ".mp4" is removed.</span></span>

<span data-ttu-id="19784-203">[!code-csharp[Main](../fundamentals/static-files/sample/StartupFileExtensionContentTypeProvider.cs?highlight=3,4,5,6,7,8,9,10,11,12,19&name=snippet1)]</span><span class="sxs-lookup"><span data-stu-id="19784-203">[!code-csharp[Main](../fundamentals/static-files/sample/StartupFileExtensionContentTypeProvider.cs?highlight=3,4,5,6,7,8,9,10,11,12,19&name=snippet1)]</span></span>

<span data-ttu-id="19784-204">참조 [MIME 콘텐츠 형식을](http://www.iana.org/assignments/media-types/media-types.xhtml)합니다.</span><span class="sxs-lookup"><span data-stu-id="19784-204">See   [MIME content types](http://www.iana.org/assignments/media-types/media-types.xhtml).</span></span>

## <a name="non-standard-content-types"></a><span data-ttu-id="19784-205">사용할 수 없는 콘텐츠 형식</span><span class="sxs-lookup"><span data-stu-id="19784-205">Non-standard content types</span></span>

<span data-ttu-id="19784-206">ASP.NET 정적 파일 미들웨어 거의 400 알려진된 파일 콘텐츠 형식을 이해합니다.</span><span class="sxs-lookup"><span data-stu-id="19784-206">The ASP.NET static file middleware understands almost 400 known file content types.</span></span> <span data-ttu-id="19784-207">사용자가 알 수 없는 파일 형식의 파일을 요청 하는 경우 정적 파일 미들웨어는 HTTP 404 (찾을 수 없음) 응답을 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="19784-207">If the user requests a file of an unknown file type, the static file middleware returns a HTTP 404 (Not found) response.</span></span> <span data-ttu-id="19784-208">디렉터리 검색을 사용 하는 경우 파일에 대 한 링크에는 표시 하지만 URI에서 HTTP 404 오류를 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="19784-208">If directory browsing is enabled, a link to the file will be displayed, but the URI will return an HTTP 404 error.</span></span>

<span data-ttu-id="19784-209">다음 코드에서는 처리 알 수 없는 형식을 사용 하면 고 알 수 없는 파일을 이미지로 렌더링 됩니다.</span><span class="sxs-lookup"><span data-stu-id="19784-209">The following code enables serving unknown types and will render the unknown file as an image.</span></span>

<span data-ttu-id="19784-210">[!code-csharp[Main](static-files/sample/StartupServeUnknownFileTypes.cs?name=snippet1)]</span><span class="sxs-lookup"><span data-stu-id="19784-210">[!code-csharp[Main](static-files/sample/StartupServeUnknownFileTypes.cs?name=snippet1)]</span></span>

<span data-ttu-id="19784-211">위의 코드와 함께 파일을 알 수 없는 콘텐츠 형식에 대 한 요청 이미지 형식으로 반환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="19784-211">With the code above, a request for a file with an unknown content type will be returned as an image.</span></span>

>[!WARNING]
> <span data-ttu-id="19784-212">사용 하도록 설정 `ServeUnknownFileTypes` 보안상 위험할 수 및 사용은 권장 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="19784-212">Enabling `ServeUnknownFileTypes` is a security risk and using it is discouraged.</span></span>  <span data-ttu-id="19784-213">`FileExtensionContentTypeProvider`(위에서 설명한) 비표준 확장명을 가진 파일을 처리 하는 보다 안전한 대체 방법을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="19784-213">`FileExtensionContentTypeProvider`  (explained above) provides a safer alternative to serving files with non-standard extensions.</span></span>

### <a name="considerations"></a><span data-ttu-id="19784-214">고려 사항</span><span class="sxs-lookup"><span data-stu-id="19784-214">Considerations</span></span>

>[!WARNING]
> <span data-ttu-id="19784-215">`UseDirectoryBrowser`및 `UseStaticFiles` 비밀 정보를 누출 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="19784-215">`UseDirectoryBrowser` and `UseStaticFiles` can leak secrets.</span></span> <span data-ttu-id="19784-216">하는 것이 좋습니다 있습니다 **하지** 사용 디렉터리 프로덕션 환경에서 검색 합니다.</span><span class="sxs-lookup"><span data-stu-id="19784-216">We recommend that you **not** enable directory browsing in production.</span></span> <span data-ttu-id="19784-217">주의를 사용 하는 디렉터리에 대 한 `UseStaticFiles` 또는 `UseDirectoryBrowser` 대로 전체 디렉터리와 모든 하위 디렉터리를 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="19784-217">Be careful about which directories you enable with `UseStaticFiles` or `UseDirectoryBrowser` as the entire directory and all sub-directories will be accessible.</span></span> <span data-ttu-id="19784-218">와 같은 자체 디렉터리에 공용 콘텐츠를 유지 하는 것이 좋습니다  *\<콘텐츠 루트 > / wwwroot*, 응용 프로그램 뷰, 구성 파일 등에서 떨어진 곳입니다.</span><span class="sxs-lookup"><span data-stu-id="19784-218">We recommend keeping public content in its own directory such as *\<content root>/wwwroot*, away from application views, configuration files, etc.</span></span>

* <span data-ttu-id="19784-219">노출 된 콘텐츠에 대 한 Url `UseDirectoryBrowser` 및 `UseStaticFiles` 는 대/소문자 구분 및 해당 내부 파일 시스템의 문자 제한이 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="19784-219">The URLs for content exposed with `UseDirectoryBrowser` and `UseStaticFiles` are subject to the case sensitivity and character restrictions of their underlying file system.</span></span> <span data-ttu-id="19784-220">예를 들어 Windows 대/소문자 구분, 이지만 Mac 및 Linux 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="19784-220">For example, Windows is case insensitive, but Mac and Linux are not.</span></span>

* <span data-ttu-id="19784-221">IIS에서 호스팅되는 ASP.NET Core 응용 프로그램의 경우 정적 파일에 대 한 요청을 포함 하 여 응용 프로그램에 대 한 모든 요청을 전달 하도록 ASP.NET 코어 모듈을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="19784-221">ASP.NET Core applications hosted in IIS use the ASP.NET Core Module to forward all requests to the application including requests for static files.</span></span> <span data-ttu-id="19784-222">ASP.NET Core 모듈에 의해 처리 되기 전에 요청을 처리할 수 있는 기회가 못하는 때문에 IIS 정적 파일 처리기 사용 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="19784-222">The IIS static file handler is not used because it doesn't get a chance to handle requests before they are handled by the ASP.NET Core Module.</span></span>

* <span data-ttu-id="19784-223">에 서버 또는 웹 사이트 수준에서 IIS 정적 파일 처리기를 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="19784-223">To remove the IIS static file handler (at the server or website level):</span></span>

     * <span data-ttu-id="19784-224">탐색 하 고 **모듈** 기능</span><span class="sxs-lookup"><span data-stu-id="19784-224">Navigate to the **Modules** feature</span></span>

     * <span data-ttu-id="19784-225">선택 **모듈은 staticfilemodule입니다** 목록에서</span><span class="sxs-lookup"><span data-stu-id="19784-225">Select **StaticFileModule** in the list</span></span>

     * <span data-ttu-id="19784-226">탭 **제거** 에 **동작** 사이드바</span><span class="sxs-lookup"><span data-stu-id="19784-226">Tap **Remove** in the **Actions** sidebar</span></span>

>[!WARNING]
> <span data-ttu-id="19784-227">IIS 정적 파일 처리기에서 사용 되는 경우 **및** ASP.NET Core 모듈 (ANCM) 제대로 구성 되지 않은 (예를 들어 경우 *web.config* 응용 프로그램이 배포 되지), 정적 파일 처리 됩니다.</span><span class="sxs-lookup"><span data-stu-id="19784-227">If the IIS static file handler is enabled **and** the ASP.NET Core Module (ANCM) is not correctly configured (for example if *web.config* was not deployed), static files will be served.</span></span>

* <span data-ttu-id="19784-228">응용 프로그램 프로젝트 외부에서 코드 파일 (c# 및 Razor 포함)를 배치할 `web root` (*wwwroot* 기본적으로).</span><span class="sxs-lookup"><span data-stu-id="19784-228">Code files (including c# and Razor) should be placed outside of the app project's `web root` (*wwwroot* by default).</span></span> <span data-ttu-id="19784-229">이렇게 하면 응용 프로그램의 클라이언트 쪽 콘텐츠 및 서버 쪽 코드 유출 되지 않도록 방지 서버 쪽 소스 코드를 명확히 구분을 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="19784-229">This creates a clean separation between your app's client side content and server side source code, which prevents server side code from being leaked.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="19784-230">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="19784-230">Additional Resources</span></span>

* [<span data-ttu-id="19784-231">미들웨어</span><span class="sxs-lookup"><span data-stu-id="19784-231">Middleware</span></span>](middleware.md)

* [<span data-ttu-id="19784-232">ASP.NET Core 소개</span><span class="sxs-lookup"><span data-stu-id="19784-232">Introduction to ASP.NET Core</span></span>](../index.md)
