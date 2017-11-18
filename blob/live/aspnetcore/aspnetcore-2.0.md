---
title: "ASP.NET Core 2.0의 새로운 기능"
author: rick-anderson
description: "ASP.NET Core 2.0의 새로운 기능"
keywords: "ASP.NET Core, 릴리스 정보, 새로운 기능"
ms.author: riande
manager: wpickett
ms.date: 07/10/2017
ms.topic: article
ms.assetid: 08c9f457-9c24-40f9-a08b-47dc251e4cec
ms.technology: aspnet
ms.prod: aspnet-core
uid: aspnetcore-2.0
ms.openlocfilehash: 98af3788652e87f6222551cb4a8e5427b312660c
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2017
---
# <a name="whats-new-in-aspnet-core-20"></a><span data-ttu-id="f3bc6-104">ASP.NET Core 2.0의 새로운 기능</span><span class="sxs-lookup"><span data-stu-id="f3bc6-104">What's new in ASP.NET Core 2.0</span></span>

<span data-ttu-id="f3bc6-105">이 문서에서는 ASP.NET Core 2.0의 가장 큰 변경 내용을 중점적으로 설명하고 관련 문서의 링크를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="f3bc6-105">This article highlights the most significant changes in ASP.NET Core 2.0, with links to relevant documentation.</span></span>

## <a name="razor-pages"></a><span data-ttu-id="f3bc6-106">Razor 페이지</span><span class="sxs-lookup"><span data-stu-id="f3bc6-106">Razor Pages</span></span>

<span data-ttu-id="f3bc6-107">Razor 페이지는 더 쉽고 더 생산적으로 코딩 페이지에 초점을 맞춘 시나리오를 만드는 ASP.NET Core MVC의 새로운 기능입니다.</span><span class="sxs-lookup"><span data-stu-id="f3bc6-107">Razor Pages is a new feature of ASP.NET Core MVC that makes coding page-focused scenarios easier and more productive.</span></span>

<span data-ttu-id="f3bc6-108">자세한 내용은 소개 및 자습서를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f3bc6-108">For more information, see the introduction and tutorial:</span></span>

* [<span data-ttu-id="f3bc6-109">Razor 페이지 소개</span><span class="sxs-lookup"><span data-stu-id="f3bc6-109">Introduction to Razor Pages</span></span>](xref:mvc/razor-pages/index)
* [<span data-ttu-id="f3bc6-110">Razor 페이지 시작</span><span class="sxs-lookup"><span data-stu-id="f3bc6-110">Getting started with Razor Pages</span></span>](xref:tutorials/razor-pages/razor-pages-start)

## <a name="aspnet-core-metapackage"></a><span data-ttu-id="f3bc6-111">ASP.NET Core 메타패키지</span><span class="sxs-lookup"><span data-stu-id="f3bc6-111">ASP.NET Core metapackage</span></span>

<span data-ttu-id="f3bc6-112">새로운 ASP.NET Core 메타패키지에는 ASP.NET Core 및 Entity Framework Core 팀에서 만들고 지원하는 모든 패키지와 함께 해당하는 내부 및 타사 종속성이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="f3bc6-112">A new ASP.NET Core metapackage includes all of the packages made and supported by the ASP.NET Core and Entity Framework Core teams, along with their internal and 3rd-party dependencies.</span></span> <span data-ttu-id="f3bc6-113">더 이상 패키지별로 개별 ASP.NET Core 기능을 선택할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="f3bc6-113">You no longer need to choose individual ASP.NET Core features by package.</span></span> <span data-ttu-id="f3bc6-114">모든 기능은 [Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All) 패키지에 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f3bc6-114">All features are included in the [Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All) package.</span></span> <span data-ttu-id="f3bc6-115">기본 템플릿은 이 패키지를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="f3bc6-115">The default templates use this package.</span></span>

<span data-ttu-id="f3bc6-116">자세한 내용은 [ASP.NET Core 2.0에 대한 Microsoft.AspNetCore.All 메타패키지](xref:fundamentals/metapackage)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f3bc6-116">For more information, see [Microsoft.AspNetCore.All metapackage for ASP.NET Core 2.0](xref:fundamentals/metapackage).</span></span>

## <a name="runtime-store"></a><span data-ttu-id="f3bc6-117">런타임 저장소</span><span class="sxs-lookup"><span data-stu-id="f3bc6-117">Runtime Store</span></span>

<span data-ttu-id="f3bc6-118">`Microsoft.AspNetCore.All` 메타패키지를 사용하는 응용 프로그램은 새로운 .NET Core 런타임 저장소를 자동으로 활용합니다.</span><span class="sxs-lookup"><span data-stu-id="f3bc6-118">Applications that use the `Microsoft.AspNetCore.All` metapackage automatically take advantage of the new .NET Core Runtime Store.</span></span> <span data-ttu-id="f3bc6-119">저장소에는 ASP.NET Core 2.0 응용 프로그램을 실행하는 데 필요한 모든 런타임 자산이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="f3bc6-119">The Store contains all the runtime assets needed to run ASP.NET Core 2.0 applications.</span></span> <span data-ttu-id="f3bc6-120">`Microsoft.AspNetCore.All` 메타패키지를 사용할 경우 참조되는 ASP.NET Core NuGet 패키지의 자산은 대상 시스템에 있기 때문에 응용 프로그램과 함께 배포되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f3bc6-120">When you use the `Microsoft.AspNetCore.All` metapackage, no assets from the referenced ASP.NET Core NuGet packages are deployed with the application because they already reside on the target system.</span></span> <span data-ttu-id="f3bc6-121">또한 응용 프로그램 시작 시간을 개선하기 위해 런타임 저장소의 자산은 미리 컴파일됩니다.</span><span class="sxs-lookup"><span data-stu-id="f3bc6-121">The assets in the Runtime Store are also precompiled to improve application startup time.</span></span>

<span data-ttu-id="f3bc6-122">자세한 내용은 [런타임 저장소](https://docs.microsoft.com/dotnet/core/deploying/runtime-store)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f3bc6-122">For more information, see [Runtime store](https://docs.microsoft.com/dotnet/core/deploying/runtime-store)</span></span>

## <a name="net-standard-20"></a><span data-ttu-id="f3bc6-123">.NET Standard 2.0</span><span class="sxs-lookup"><span data-stu-id="f3bc6-123">.NET Standard 2.0</span></span>

<span data-ttu-id="f3bc6-124">ASP.NET Core 2.0 패키지는 .NET Standard 2.0을 대상으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="f3bc6-124">The ASP.NET Core 2.0 packages target .NET Standard 2.0.</span></span> <span data-ttu-id="f3bc6-125">패키지는 다른 .NET Standard 2.0 라이브러리에서 참조될 수 있고 .NET Core 2.0, .NET Framework 4.6.1 등 .NET의 .NET Standard 2.0 규격 구현에서 실행될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f3bc6-125">The packages can be referenced by other .NET Standard 2.0 libraries, and they can run on .NET Standard 2.0-compliant implementations of .NET, including .NET Core 2.0 and .NET Framework 4.6.1.</span></span> 

<span data-ttu-id="f3bc6-126">`Microsoft.AspNetCore.All` 메타패키지는 .NET Core 2.0 런타임 저장소와 함께 사용되므로 .NET Core 2.0만 대상으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="f3bc6-126">The `Microsoft.AspNetCore.All` metapackage targets .NET Core 2.0 only, because it is intended to be used with the .NET Core 2.0 Runtime Store.</span></span>

## <a name="configuration-update"></a><span data-ttu-id="f3bc6-127">구성 업데이트</span><span class="sxs-lookup"><span data-stu-id="f3bc6-127">Configuration update</span></span>

<span data-ttu-id="f3bc6-128">`IConfiguration` 인스턴스는 기본적으로 ASP.NET Core 2.0의 서비스 컨테이너에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="f3bc6-128">An `IConfiguration` instance is added to the services container by default in ASP.NET Core 2.0.</span></span> <span data-ttu-id="f3bc6-129">서비스 컨테이너의 `IConfiguration`을 사용하면 응용 프로그램이 컨테이너에서 구성 값을 더 쉽게 검색할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f3bc6-129">`IConfiguration` in the services container makes it easier for applications to retrieve configuration values from the container.</span></span>

<span data-ttu-id="f3bc6-130">계획된 문서의 상태에 대한 자세한 내용은 [GitHub issue](https://github.com/aspnet/Docs/issues/3387)(GitHub 문제)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f3bc6-130">For information about the status of planned documentation, see the [GitHub issue](https://github.com/aspnet/Docs/issues/3387).</span></span>

## <a name="logging-update"></a><span data-ttu-id="f3bc6-131">로깅 업데이트</span><span class="sxs-lookup"><span data-stu-id="f3bc6-131">Logging update</span></span>

<span data-ttu-id="f3bc6-132">ASP.NET Core 2.0에서 로깅은 기본적으로 DI(종속성 주입) 시스템에 통합되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f3bc6-132">In ASP.NET Core 2.0, logging is incorporated into the dependency injection (DI) system by default.</span></span> <span data-ttu-id="f3bc6-133">*Startup.cs* 파일 대신에 *Program.cs* 파일에서 공급자를 추가하고 필터링을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="f3bc6-133">You add providers and configure filtering in the *Program.cs* file instead of in the *Startup.cs* file.</span></span> <span data-ttu-id="f3bc6-134">기본 `ILoggerFactory`는 전체 공급자 필터링 및 특정 공급자 필터링에 둘 다 하나의 유연한 방법을 사용하는 방식으로 필터링을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="f3bc6-134">And the default `ILoggerFactory` supports filtering in a way that lets you use one flexible approach for both cross-provider filtering and specific-provider filtering.</span></span>

<span data-ttu-id="f3bc6-135">자세한 내용은 [로깅 소개](xref:fundamentals/logging/index)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f3bc6-135">For more information, see [Introduction to Logging](xref:fundamentals/logging/index).</span></span>

## <a name="authentication-update"></a><span data-ttu-id="f3bc6-136">인증 업데이트</span><span class="sxs-lookup"><span data-stu-id="f3bc6-136">Authentication update</span></span>

<span data-ttu-id="f3bc6-137">새 인증 모델을 사용하면 DI를 사용하는 응용 프로그램에 대한 인증을 더 쉽게 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f3bc6-137">A new authentication model makes it easier to configure authentication for an application using DI.</span></span>

<span data-ttu-id="f3bc6-138">새 템플릿을 사용하여 [Azure AD B2C](https://azure.microsoft.com/services/active-directory-b2c/)를 통해 웹앱 및 Web API에 대한 인증을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f3bc6-138">New templates are available for configuring authentication for web apps and web APIs using [Azure AD B2C] (https://azure.microsoft.com/services/active-directory-b2c/).</span></span>

<span data-ttu-id="f3bc6-139">계획된 문서의 상태에 대한 자세한 내용은 [GitHub issue](https://github.com/aspnet/Docs/issues/3054)(GitHub 문제)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f3bc6-139">For information about the status of planned documentation, see the [GitHub issue](https://github.com/aspnet/Docs/issues/3054).</span></span>

## <a name="identity-update"></a><span data-ttu-id="f3bc6-140">ID 업데이트</span><span class="sxs-lookup"><span data-stu-id="f3bc6-140">Identity update</span></span>

<span data-ttu-id="f3bc6-141">ASP.NET Core 2.0에서는 ID를 사용하여 보안 Web API를 더 쉽게 빌드할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f3bc6-141">We've made it easier to build secure web APIs using Identity in ASP.NET Core 2.0.</span></span> <span data-ttu-id="f3bc6-142">[MSAL(Microsoft Authentication Library)](https://www.nuget.org/packages/Microsoft.Identity.Client)을 사용하여 Web API에 액세스하기 위해 액세스 토큰을 얻을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f3bc6-142">You can acquire access tokens for accessing your web APIs using the [Microsoft Authentication Library (MSAL)](https://www.nuget.org/packages/Microsoft.Identity.Client).</span></span>

<span data-ttu-id="f3bc6-143">2.0의 인증 변경에 대한 자세한 내용은 다음 리소스를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f3bc6-143">For more information on authentication changes in 2.0, see the following resources:</span></span>

* [<span data-ttu-id="f3bc6-144">ASP.NET Core의 계정 확인 및 암호 복구</span><span class="sxs-lookup"><span data-stu-id="f3bc6-144">Account confirmation and password recovery in ASP.NET Core</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="f3bc6-145">ASP.NET Core에서 인증자 앱에 QR 코드 생성 사용</span><span class="sxs-lookup"><span data-stu-id="f3bc6-145">Enabling QR Code generation for authenticator apps in ASP.NET Core</span></span>](xref:security/authentication/identity-enable-qrcodes)
* [<span data-ttu-id="f3bc6-146">ASP.NET Core 2.0으로 인증 및 ID 마이그레이션</span><span class="sxs-lookup"><span data-stu-id="f3bc6-146">Migrating Authentication and Identity to ASP.NET Core 2.0</span></span>](xref:migration/1x-to-2x/identity-2x)

## <a name="spa-templates"></a><span data-ttu-id="f3bc6-147">SPA 템플릿</span><span class="sxs-lookup"><span data-stu-id="f3bc6-147">SPA templates</span></span>

<span data-ttu-id="f3bc6-148">Angular, Aurelia, Knockout.js, React.js 및 React.js에 대한 SPA(단일 페이지 응용 프로그램) 프로젝트 템플릿을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f3bc6-148">Single Page Application (SPA) project templates for Angular, Aurelia, Knockout.js, React.js, and React.js with Redux are available.</span></span> <span data-ttu-id="f3bc6-149">Angular 템플릿은 Angular 4로 업데이트되었습니다.</span><span class="sxs-lookup"><span data-stu-id="f3bc6-149">The Angular template has been updated to Angular 4.</span></span> <span data-ttu-id="f3bc6-150">Angular 및 React 템플릿은 기본적으로 제공됩니다. 다른 템플릿을 가져오는 방법에 대한 자세한 내용은 [새 SPA 프로젝트 만들기](xref:client-side/spa-services#creating-a-new-project)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f3bc6-150">The Angular and React templates are available by default; for information about how to get the other templates, see [Creating a new SPA project](xref:client-side/spa-services#creating-a-new-project).</span></span> <span data-ttu-id="f3bc6-151">ASP.NET Core에서 SPA를 빌드하는 방법에 대한 자세한 내용은 [단일 페이지 응용 프로그램 만들기에 JavaScriptServices 사용](xref:client-side/spa-services)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f3bc6-151">For information about how to build a SPA in ASP.NET Core, see [Using JavaScriptServices for Creating Single Page Applications](xref:client-side/spa-services).</span></span>

## <a name="kestrel-improvements"></a><span data-ttu-id="f3bc6-152">Kestrel 기능 향상</span><span class="sxs-lookup"><span data-stu-id="f3bc6-152">Kestrel improvements</span></span>

<span data-ttu-id="f3bc6-153">Kestrel 웹 서버에는 인터넷 연결 서버로서 더 적합하도록 도와주는 새로운 기능이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f3bc6-153">The Kestrel web server has new features that make it more suitable as an Internet-facing server.</span></span> <span data-ttu-id="f3bc6-154">`KestrelServerOptions` 클래스의 새 `Limits` 속성에 다양한 서버 제약 조건 구성 옵션이 추가되었습니다.</span><span class="sxs-lookup"><span data-stu-id="f3bc6-154">We’ve added a number of server constraint configuration options in the `KestrelServerOptions` class’s new `Limits` property.</span></span> <span data-ttu-id="f3bc6-155">이제 다음에 대한 제한을 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f3bc6-155">You can now add limits for the following:</span></span>

- <span data-ttu-id="f3bc6-156">최대 클라이언트 연결</span><span class="sxs-lookup"><span data-stu-id="f3bc6-156">Maximum client connections</span></span>
- <span data-ttu-id="f3bc6-157">최대 요청 본문 크기</span><span class="sxs-lookup"><span data-stu-id="f3bc6-157">Maximum request body size</span></span>
- <span data-ttu-id="f3bc6-158">최소 요청 본문 데이터 속도</span><span class="sxs-lookup"><span data-stu-id="f3bc6-158">Minimum request body data rate</span></span>

<span data-ttu-id="f3bc6-159">자세한 내용은 [ASP.NET Core의 Kestrel 웹 서버 구현](xref:fundamentals/servers/kestrel)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f3bc6-159">For more information, see [Kestrel web server implementation in ASP.NET Core](xref:fundamentals/servers/kestrel).</span></span>

## <a name="weblistener-renamed-to-httpsys"></a><span data-ttu-id="f3bc6-160">WebListener 이름이 HTTP.sys로 바뀜</span><span class="sxs-lookup"><span data-stu-id="f3bc6-160">WebListener renamed to HTTP.sys</span></span>

<span data-ttu-id="f3bc6-161">패키지 `Microsoft.AspNetCore.Server.WebListener` 및 `Microsoft.Net.Http.Server`가 새 패키지 `Microsoft.AspNetCore.Server.HttpSys`로 병합되었습니다.</span><span class="sxs-lookup"><span data-stu-id="f3bc6-161">The packages `Microsoft.AspNetCore.Server.WebListener` and `Microsoft.Net.Http.Server` have been merged into a new package `Microsoft.AspNetCore.Server.HttpSys`.</span></span> <span data-ttu-id="f3bc6-162">네임스페이스가 일치하도록 업데이트되었습니다.</span><span class="sxs-lookup"><span data-stu-id="f3bc6-162">The namespaces have been updated to match.</span></span>

<span data-ttu-id="f3bc6-163">자세한 내용은 [ASP.NET Core의 HTTP.sys 웹 서버 구현](xref:fundamentals/servers/httpsys)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f3bc6-163">For more information, see [HTTP.sys web server implementation in ASP.NET Core](xref:fundamentals/servers/httpsys).</span></span>

## <a name="enhanced-http-header-support"></a><span data-ttu-id="f3bc6-164">향상된 HTTP 헤더 지원</span><span class="sxs-lookup"><span data-stu-id="f3bc6-164">Enhanced HTTP header support</span></span>

<span data-ttu-id="f3bc6-165">MVC를 사용하여 `FileStreamResult` 또는 `FileContentResult`를 전송할 경우 이제 전송하는 콘텐츠에서 `ETag` 또는 `LastModified` 날짜를 설정하는 옵션이 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="f3bc6-165">When using MVC to transmit a `FileStreamResult` or a `FileContentResult`, you now have the option to set an `ETag` or a `LastModified` date on the content you transmit.</span></span> <span data-ttu-id="f3bc6-166">다음과 비슷한 코드를 사용하여 반환된 콘텐츠에서 이러한 값을 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f3bc6-166">You can set these values on the returned content with code similar to the following:</span></span>

```csharp
var data = Encoding.UTF8.GetBytes("This is a sample text from a binary array");
var entityTag = new EntityTagHeaderValue("\"MyCalculatedEtagValue\"");
return File(data, "text/plain", "downloadName.txt", lastModified: DateTime.UtcNow.AddSeconds(-5), entityTag: entityTag);
```

<span data-ttu-id="f3bc6-167">방문자에게 반환된 파일이 `ETag` 및 `LastModified` 값에 해당하는 HTTP 헤더로 데코레이트됩니다.</span><span class="sxs-lookup"><span data-stu-id="f3bc6-167">The file returned to your visitors will be decorated with the appropriate HTTP headers for the `ETag` and `LastModified` values.</span></span>

<span data-ttu-id="f3bc6-168">응용 프로그램 방문자가 범위 요청 헤더가 포함된 콘텐츠를 요청하면 ASP.NET은 이 요청을 인식하고 해당 헤더를 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="f3bc6-168">If an application visitor requests content with a Range Request header, ASP.NET will recognize that and handle that header.</span></span> <span data-ttu-id="f3bc6-169">요청된 콘텐츠가 부분적으로 전달될 수 있으면 ASP.NET은 적절히 건너뛰고 요청된 바이트 집합만 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="f3bc6-169">If the requested content can be partially delivered, ASP.NET will appropriately skip and return just the requested set of bytes.</span></span>  <span data-ttu-id="f3bc6-170">이 기능에 맞게 조정하거나 이 기능을 처리하기 위해 메서드에 특수 처리기를 작성할 필요가 없습니다. 기능이 자동으로 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="f3bc6-170">You do not need to write any special handlers into your methods to adapt or handle this feature; it is automatically handled for you.</span></span>

## <a name="hosting-startup-and-application-insights"></a><span data-ttu-id="f3bc6-171">호스팅 시작 및 Application Insights</span><span class="sxs-lookup"><span data-stu-id="f3bc6-171">Hosting startup and Application Insights</span></span>

<span data-ttu-id="f3bc6-172">이제 호스팅 환경에서는 응용 프로그램이 명시적으로 종속성을 사용하거나 메서드를 호출할 필요 없이 추가 패키지 종속성을 삽입하고 응용 프로그램 시작 중에 코드를 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f3bc6-172">Hosting environments can now inject extra package dependencies and execute code during application startup, without the application needing to explicitly take a dependency or call any methods.</span></span> <span data-ttu-id="f3bc6-173">이 기능을 사용하면 응용 프로그램이 미리 인식할 필요 없이 특정 환경이 해당 환경에 고유한 기능을 “강화”할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f3bc6-173">This feature can be used to enable certain environments to "light-up" features unique to that environment without the application needing to know ahead of time.</span></span> 

<span data-ttu-id="f3bc6-174">ASP.NET Core 2.0에서 이 기능은 Visual Studio에서 디버그할 경우 및 Azure App Services에서 실행될 경우(옵트인(opt in) 후) 자동으로 Application Insights 진단을 사용하도록 설정하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="f3bc6-174">In ASP.NET Core 2.0, this feature is used to automatically enable Application Insights diagnostics when debugging in Visual Studio and (after opting in) when running in Azure App Services.</span></span> <span data-ttu-id="f3bc6-175">따라서 프로젝트 템플릿은 더 이상 기본적으로 Application Insights 패키지와 코드를 추가하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f3bc6-175">As a result, the project templates no longer add Application Insights packages and code by default.</span></span>

<span data-ttu-id="f3bc6-176">계획된 문서의 상태에 대한 자세한 내용은 [GitHub issue](https://github.com/aspnet/Docs/issues/3389)(GitHub 문제)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f3bc6-176">For information about the status of planned documentation, see the [GitHub issue](https://github.com/aspnet/Docs/issues/3389).</span></span>

## <a name="automatic-use-of-anti-forgery-tokens"></a><span data-ttu-id="f3bc6-177">위조 방지 토큰 자동 사용</span><span class="sxs-lookup"><span data-stu-id="f3bc6-177">Automatic use of anti-forgery tokens</span></span>

<span data-ttu-id="f3bc6-178">ASP.NET Core는 언제나 기본적으로 콘텐츠를 HTML 인코딩하는 기능을 제공했지만, 새 버전에서는 교차 사이트 요청 위조(XSRF) 공격을 방지할 수 있도록 추가 조치를 취합니다.</span><span class="sxs-lookup"><span data-stu-id="f3bc6-178">ASP.NET Core has always helped HTML-encode your content by default, but with the new version we’re taking an extra step to help prevent cross-site request forgery (XSRF) attacks.</span></span> <span data-ttu-id="f3bc6-179">이제 ASP.NET Core는 기본적으로 위조 방지 토큰을 내보내고 추가 구성없이 폼 POST 작업 및 페이지에서 토큰의 유효성을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="f3bc6-179">ASP.NET Core will now emit anti-forgery tokens by default and validate them on form POST actions and pages without extra configuration.</span></span>

<span data-ttu-id="f3bc6-180">자세한 내용은 [ASP.NET Core에서 교차 사이트 요청 위조(XSRF/CSRF) 공격 방지](xref:security/anti-request-forgery)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f3bc6-180">For more information, see [Preventing Cross-Site Request Forgery (XSRF/CSRF) Attacks in ASP.NET Core](xref:security/anti-request-forgery).</span></span>

## <a name="automatic-precompilation"></a><span data-ttu-id="f3bc6-181">자동으로 미리 컴파일</span><span class="sxs-lookup"><span data-stu-id="f3bc6-181">Automatic precompilation</span></span>

<span data-ttu-id="f3bc6-182">Razor 뷰 미리 컴파일이 기본적으로 게시 중에 사용하도록 설정되므로 게시 출력 크기와 응용 프로그램 시작 시간이 감소합니다.</span><span class="sxs-lookup"><span data-stu-id="f3bc6-182">Razor view pre-compilation is enabled during publish by default, reducing the publish output size and application startup time.</span></span>

## <a name="razor-support-for-c-71"></a><span data-ttu-id="f3bc6-183">C# 7.1에 대한 Razor 지원</span><span class="sxs-lookup"><span data-stu-id="f3bc6-183">Razor support for C# 7.1</span></span>

<span data-ttu-id="f3bc6-184">Razor 뷰 엔진이 새 Roslyn 컴파일러를 사용하도록 업데이트되었습니다.</span><span class="sxs-lookup"><span data-stu-id="f3bc6-184">The Razor view engine has been updated to work with the new Roslyn compiler.</span></span> <span data-ttu-id="f3bc6-185">여기에는 기본 식, 유추된 튜플 이름 및 제네릭 패턴 일치 같은 C# 7.1 기능에 대한 지원이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="f3bc6-185">That includes support for C# 7.1 features like Default Expressions, Inferred Tuple Names, and Pattern-Matching with Generics.</span></span> <span data-ttu-id="f3bc6-186">프로젝트에서 C# 7.1을 사용하려면 프로젝트 파일에 다음 속성을 추가하고 나서 솔루션을 다시 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="f3bc6-186">To use C# 7.1 in your project, add the following property in your project file and then reload the solution:</span></span>

```xml
<LangVersion>latest</LangVersion>
```

<span data-ttu-id="f3bc6-187">C# 7.1 기능 상태에 대한 자세한 내용은 [the Roslyn GitHub repository](https://github.com/dotnet/roslyn/blob/master/docs/Language%20Feature%20Status.md)(Roslyn GitHub 리포지토리)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f3bc6-187">For information about the status of C# 7.1 features, see [the Roslyn GitHub repository](https://github.com/dotnet/roslyn/blob/master/docs/Language%20Feature%20Status.md).</span></span>

## <a name="other-documentation-updates-for-20"></a><span data-ttu-id="f3bc6-188">2.0에 대한 기타 문서 업데이트</span><span class="sxs-lookup"><span data-stu-id="f3bc6-188">Other documentation updates for 2.0</span></span>

* [<span data-ttu-id="f3bc6-189">Visual Studio 및 MSBuild에 대한 게시 프로필을 만들어 ASP.NET Core 앱 배포</span><span class="sxs-lookup"><span data-stu-id="f3bc6-189">Create publish profiles for Visual Studio and MSBuild, to deploy ASP.NET Core apps</span></span>](xref:publishing/web-publishing-vs)
* [<span data-ttu-id="f3bc6-190">키 관리</span><span class="sxs-lookup"><span data-stu-id="f3bc6-190">Key Management</span></span>](xref:security/data-protection/implementation/key-management)
* [<span data-ttu-id="f3bc6-191">Facebook 인증 구성</span><span class="sxs-lookup"><span data-stu-id="f3bc6-191">Configuring Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="f3bc6-192">Twitter 인증 구성</span><span class="sxs-lookup"><span data-stu-id="f3bc6-192">Configuring Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="f3bc6-193">Google 인증 구성</span><span class="sxs-lookup"><span data-stu-id="f3bc6-193">Configuring Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="f3bc6-194">Microsoft 계정 인증 구성</span><span class="sxs-lookup"><span data-stu-id="f3bc6-194">Configuring Microsoft Account authentication</span></span>](xref:security/authentication/microsoft-logins)

## <a name="migration-guidance"></a><span data-ttu-id="f3bc6-195">마이그레이션 지침</span><span class="sxs-lookup"><span data-stu-id="f3bc6-195">Migration guidance</span></span>

<span data-ttu-id="f3bc6-196">ASP.NET Core 1.x 응용 프로그램을 ASP.NET Core 2.0으로 마이그레이션하는 방법에 대한 자세한 내용은 다음 리소스를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f3bc6-196">For guidance on how to migrate ASP.NET Core 1.x applications to ASP.NET Core 2.0, see the following resources:</span></span>

* [<span data-ttu-id="f3bc6-197">ASP.NET Core 1.x에서 ASP.NET Core 2.0으로 마이그레이션</span><span class="sxs-lookup"><span data-stu-id="f3bc6-197">Migrating from ASP.NET Core 1.x to ASP.NET Core 2.0</span></span>](xref:migration/1x-to-2x/index)
* [<span data-ttu-id="f3bc6-198">ASP.NET Core 2.0으로 인증 및 ID 마이그레이션</span><span class="sxs-lookup"><span data-stu-id="f3bc6-198">Migrating Authentication and Identity to ASP.NET Core 2.0</span></span>](xref:migration/1x-to-2x/identity-2x)

## <a name="additional-information"></a><span data-ttu-id="f3bc6-199">추가 정보</span><span class="sxs-lookup"><span data-stu-id="f3bc6-199">Additional Information</span></span>

<span data-ttu-id="f3bc6-200">전체 변경 내용 목록을 보려면 [ASP.NET Core 2.0 Release Notes](https://github.com/aspnet/Home/releases/tag/2.0.0)(ASP.NET Core 2.0 릴리스 정보)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f3bc6-200">For the complete list of changes, see the [ASP.NET Core 2.0 Release Notes](https://github.com/aspnet/Home/releases/tag/2.0.0).</span></span>

<span data-ttu-id="f3bc6-201">ASP.NET Core 개발 팀의 진행 상황 및 계획과 연결하려면 매주 [ASP.NET Community Standup](https://live.asp.net/)(ASP.NET 커뮤니티 스탠드업)을 시청하세요.</span><span class="sxs-lookup"><span data-stu-id="f3bc6-201">If you’d like to connect with the ASP.NET Core development team’s progress and plans, tune in to the weekly [ASP.NET Community Standup](https://live.asp.net/).</span></span>
