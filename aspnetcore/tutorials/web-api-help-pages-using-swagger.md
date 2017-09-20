---
title: "Swagger를 사용한 ASP.NET Core Web API 도움말 페이지"
author: spboyer
description: "이 자습서에서는 Swagger를 추가하여 Web API 응용 프로그램에 대한 설명서 및 도움말 페이지를 생성하는 연습을 제공합니다."
keywords: "ASP.NET Core, Swagger, Swashbuckle, 도움말 페이지, Web API"
ms.author: spboyer
manager: wpickett
ms.date: 09/01/2017
ms.topic: article
ms.assetid: 54bb961d-29d9-4dee-8e2c-a93fc33c16f2
ms.technology: aspnet
ms.prod: asp.net-core
uid: tutorials/web-api-help-pages-using-swagger
ms.openlocfilehash: 647ab48fb83c5e2c79b5de371173bc644c65d831
ms.sourcegitcommit: 98ecb0f1bae4886507b090c84ecd99ff1e5c46ed
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/17/2017
---
# <a name="aspnet-web-api-help-pages-using-swagger"></a><span data-ttu-id="4d87b-104">Swagger를 사용한 ASP.NET Web API 도움말 페이지</span><span class="sxs-lookup"><span data-stu-id="4d87b-104">ASP.NET Web API Help Pages using Swagger</span></span>

<a name=web-api-help-pages-using-swagger></a>

<span data-ttu-id="4d87b-105">작성자: [Shayne Boyer](https://twitter.com/spboyer) 및 [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="4d87b-105">By [Shayne Boyer](https://twitter.com/spboyer) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="4d87b-106">소비 응용 프로그램을 빌드할 경우 개발자가 API의 다양한 메서드를 이해하기 어려울 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4d87b-106">Understanding the various methods of an API can be a challenge for a developer when building a consuming application.</span></span>

<span data-ttu-id="4d87b-107">[Swagger](https://swagger.io/)와 .NET Core 구현 [Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore)를 함께 사용하여 Web API에 대한 유용한 설명서와 도움말 페이지를 생성하는 것은 두서너 개의 NuGet 패키지를 추가하고 *Startup.cs*를 수정하는 것만큼 쉽습니다.</span><span class="sxs-lookup"><span data-stu-id="4d87b-107">Generating good documentation and help pages for your Web API, using [Swagger](https://swagger.io/) with the .NET Core implementation [Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore), is as easy as adding a couple of NuGet packages and modifying the *Startup.cs*.</span></span>

* <span data-ttu-id="4d87b-108">[Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore)는 ASP.NET Core Web API에 대한 Swagger 문서를 생성하기 위한 오픈 소스 프로젝트입니다.</span><span class="sxs-lookup"><span data-stu-id="4d87b-108">[Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) is an open source project for generating Swagger documents for ASP.NET Core Web APIs.</span></span>

* <span data-ttu-id="4d87b-109">[Swagger](https://swagger.io/)는 대화형 설명서, 클라이언트 SDK 생성 및 검색 기능에 대한 지원을 가능하게 하는 RESTful API의 컴퓨터에서 읽을 수 있는 표현입니다.</span><span class="sxs-lookup"><span data-stu-id="4d87b-109">[Swagger](https://swagger.io/) is a machine-readable representation of a RESTful API that enables support for interactive documentation, client SDK generation, and discoverability.</span></span>

<span data-ttu-id="4d87b-110">이 자습서는 [ASP.NET Core MVC 및 Visual Studio를 사용하여 첫 번째 Web API 빌드](xref:tutorials/first-web-api)에 대한 샘플을 기반으로 빌드됩니다.</span><span class="sxs-lookup"><span data-stu-id="4d87b-110">This tutorial builds on the sample on [Building Your First Web API with ASP.NET Core MVC and Visual Studio](xref:tutorials/first-web-api).</span></span> <span data-ttu-id="4d87b-111">함께 수행해 보려면 [https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/first-web-api/sample](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/first-web-api/sample)에서 샘플을 다운로드합니다.</span><span class="sxs-lookup"><span data-stu-id="4d87b-111">If you'd like to follow along, download the sample at [https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/first-web-api/sample](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/first-web-api/sample).</span></span>

## <a name="getting-started"></a><span data-ttu-id="4d87b-112">시작</span><span class="sxs-lookup"><span data-stu-id="4d87b-112">Getting Started</span></span>

<span data-ttu-id="4d87b-113">Swashbuckle에 대한 세 가지 주 구성 요소는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="4d87b-113">There are three main components to Swashbuckle:</span></span>

* <span data-ttu-id="4d87b-114">`Swashbuckle.AspNetCore.Swagger`: `SwaggerDocument` 개체를 JSON 끝점으로 노출하기 위한 Swagger 개체 모델 및 미들웨어.</span><span class="sxs-lookup"><span data-stu-id="4d87b-114">`Swashbuckle.AspNetCore.Swagger`: a Swagger object model and middleware to expose `SwaggerDocument` objects as JSON endpoints.</span></span>

* <span data-ttu-id="4d87b-115">`Swashbuckle.AspNetCore.SwaggerGen`: 경로, 컨트롤러 및 모델에서 직접 `SwaggerDocument` 개체를 빌드하는 Swagger 생성기.</span><span class="sxs-lookup"><span data-stu-id="4d87b-115">`Swashbuckle.AspNetCore.SwaggerGen`: a Swagger generator that builds `SwaggerDocument` objects directly from your routes, controllers, and models.</span></span> <span data-ttu-id="4d87b-116">일반적으로 Swagger 끝점 미들웨어와 결합되어 자동으로 Swagger JSON을 노출합니다.</span><span class="sxs-lookup"><span data-stu-id="4d87b-116">It's typically combined with the Swagger endpoint middleware to automatically expose Swagger JSON.</span></span>

* <span data-ttu-id="4d87b-117">`Swashbuckle.AspNetCore.SwaggerUI`: Web API 기능을 설명할 수 있는 다양한 사용자 지정 가능한 환경을 빌드하기 위해 Swagger JSON을 해석하는 Swagger UI 도구의 포함된 버전.</span><span class="sxs-lookup"><span data-stu-id="4d87b-117">`Swashbuckle.AspNetCore.SwaggerUI`: an embedded version of the Swagger UI tool which interprets Swagger JSON to build a rich, customizable experience for describing the Web API functionality.</span></span> <span data-ttu-id="4d87b-118">여기에는 공용 메서드에 대한 기본 제공 테스트 도구가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="4d87b-118">It includes built-in test harnesses for the public methods.</span></span>

## <a name="nuget-packages"></a><span data-ttu-id="4d87b-119">NuGet 패키지</span><span class="sxs-lookup"><span data-stu-id="4d87b-119">NuGet Packages</span></span>

<span data-ttu-id="4d87b-120">다음 방법으로 Swashbuckle을 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4d87b-120">Swashbuckle can be added with the following approaches:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4d87b-121">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4d87b-121">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4d87b-122">**패키지 관리자 콘솔** 창에서:</span><span class="sxs-lookup"><span data-stu-id="4d87b-122">From the **Package Manager Console** window:</span></span>

    ```powershell
    Install-Package Swashbuckle.AspNetCore
    ```

* <span data-ttu-id="4d87b-123">**NuGet 패키지 관리** 대화 상자에서:</span><span class="sxs-lookup"><span data-stu-id="4d87b-123">From the **Manage NuGet Packages** dialog:</span></span>

     * <span data-ttu-id="4d87b-124">**솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭 > **NuGet 패키지 관리** 선택</span><span class="sxs-lookup"><span data-stu-id="4d87b-124">Right-click your project in **Solution Explorer** > **Manage NuGet Packages**</span></span>
     * <span data-ttu-id="4d87b-125">**패키지 소스**를 “nuget.org”로 설정</span><span class="sxs-lookup"><span data-stu-id="4d87b-125">Set the **Package source** to "nuget.org"</span></span>
     * <span data-ttu-id="4d87b-126">검색 상자에 “Swashbuckle.AspNetCore” 입력</span><span class="sxs-lookup"><span data-stu-id="4d87b-126">Enter "Swashbuckle.AspNetCore" in the search box</span></span>
     * <span data-ttu-id="4d87b-127">**찾아보기** 탭에서 “Swashbuckle.AspNetCore” 패키지를 선택하고 **설치** 클릭</span><span class="sxs-lookup"><span data-stu-id="4d87b-127">Select the "Swashbuckle.AspNetCore" package from the **Browse** tab and click **Install**</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="4d87b-128">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="4d87b-128">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="4d87b-129">**Solution Pad**에서 *Packages* 폴더를 마우스 오른쪽 단추로 클릭 > **패키지 추가...** 선택</span><span class="sxs-lookup"><span data-stu-id="4d87b-129">Right-click the *Packages* folder in **Solution Pad** > **Add Packages...**</span></span>
* <span data-ttu-id="4d87b-130">**패키지 추가** 창의 **소스** 드롭다운을 “nuget.org”로 설정</span><span class="sxs-lookup"><span data-stu-id="4d87b-130">Set the **Add Packages** window's **Source** drop-down to "nuget.org"</span></span>
* <span data-ttu-id="4d87b-131">검색 상자에 Swashbuckle.AspNetCore 입력</span><span class="sxs-lookup"><span data-stu-id="4d87b-131">Enter Swashbuckle.AspNetCore in the search box</span></span>
* <span data-ttu-id="4d87b-132">결과 창에서 Swashbuckle.AspNetCore 패키지를 선택하고 **패키지 추가** 클릭</span><span class="sxs-lookup"><span data-stu-id="4d87b-132">Select the Swashbuckle.AspNetCore package from the results pane and click **Add Package**</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="4d87b-133">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4d87b-133">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="4d87b-134">**통합 터미널**에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="4d87b-134">Run the following command from the **Integrated Terminal**:</span></span>

```console
dotnet add TodoApi.csproj package Swashbuckle.AspNetCore
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="4d87b-135">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="4d87b-135">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="4d87b-136">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="4d87b-136">Run the following command:</span></span>

```console
dotnet add TodoApi.csproj package Swashbuckle.AspNetCore
```

---

## <a name="add-and-configure-swagger-to-the-middleware"></a><span data-ttu-id="4d87b-137">미들웨어에 Swagger 추가 및 구성</span><span class="sxs-lookup"><span data-stu-id="4d87b-137">Add and configure Swagger to the middleware</span></span>

<span data-ttu-id="4d87b-138">*Startup.cs*의 `ConfigureServices` 메서드에서 서비스 컬렉션에 Swagger 생성기를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="4d87b-138">Add the Swagger generator to the services collection in the `ConfigureServices` method of *Startup.cs*:</span></span>

[!code-csharp[Main](../tutorials/web-api-help-pages-using-swagger/sample/TodoApi/Startup2.cs?name=snippet_ConfigureServices&highlight=7-10)]

<span data-ttu-id="4d87b-139">다음 using 문을 `Info` 클래스에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="4d87b-139">Add the following using statement for the `Info` class:</span></span>

```csharp
using Swashbuckle.AspNetCore.Swagger;
```

<span data-ttu-id="4d87b-140">*Startup.cs*의 `Configure` 메서드에서 생성된 JSON 문서 및 SwaggerUI를 지원하기 위해 미들웨어를 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="4d87b-140">In the `Configure` method of *Startup.cs*, enable the middleware for serving the generated JSON document and the SwaggerUI:</span></span>

[!code-csharp[Main](../tutorials/web-api-help-pages-using-swagger/sample/TodoApi/Startup2.cs?name=snippet_Configure&highlight=4,7-10)]

<span data-ttu-id="4d87b-141">앱을 시작하고 `http://localhost:<random_port>/swagger/v1/swagger.json`으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="4d87b-141">Launch the app, and navigate to `http://localhost:<random_port>/swagger/v1/swagger.json`.</span></span> <span data-ttu-id="4d87b-142">끝점을 설명하는 생성된 문서가 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="4d87b-142">The generated document describing the endpoints appears.</span></span>

<span data-ttu-id="4d87b-143">**참고:** Microsoft Edge, Google Chrome 및 Firefox에는 JSON 문서가 기본적으로 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="4d87b-143">**Note:** Microsoft Edge, Google Chrome, and Firefox display JSON documents natively.</span></span> <span data-ttu-id="4d87b-144">더 읽기 쉽도록 문서 서식을 지정하는 Chrome용 확장이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4d87b-144">There are extensions for Chrome that format the document for easier reading.</span></span> <span data-ttu-id="4d87b-145">*간략하게 표현하기 위해 다음 예제는 간소화되어 있습니다.*</span><span class="sxs-lookup"><span data-stu-id="4d87b-145">*The following example is reduced for brevity.*</span></span>

```json
{
   "swagger": "2.0",
   "info": {
       "version": "v1",
       "title": "API V1"
   },
   "basePath": "/",
   "paths": {
       "/api/Todo": {
           "get": {
               "tags": [
                   "Todo"
               ],
               "operationId": "ApiTodoGet",
               "consumes": [],
               "produces": [
                   "text/plain",
                   "application/json",
                   "text/json"
               ],
               "responses": {
                   "200": {
                       "description": "Success",
                       "schema": {
                           "type": "array",
                           "items": {
                               "$ref": "#/definitions/TodoItem"
                           }
                       }
                   }
                }
           },
           "post": {
               ...
           }
       },
       "/api/Todo/{id}": {
           "get": {
               ...
           },
           "put": {
               ...
           },
           "delete": {
               ...
   },
   "definitions": {
       "TodoItem": {
           "type": "object",
            "properties": {
                "id": {
                    "format": "int64",
                    "type": "integer"
                },
                "name": {
                    "type": "string"
                },
                "isComplete": {
                    "default": false,
                    "type": "boolean"
                }
            }
       }
   },
   "securityDefinitions": {}
}
```

<span data-ttu-id="4d87b-146">이 문서에서는 `http://localhost:<random_port>/swagger`로 이동하여 볼 수 있는 Swagger UI를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="4d87b-146">This document drives the Swagger UI, which can be viewed by navigating to `http://localhost:<random_port>/swagger`:</span></span>

![Swagger UI](web-api-help-pages-using-swagger/_static/swagger-ui.png)

<span data-ttu-id="4d87b-148">`TodoController`의 각 공용 작업 메서드를 UI에서 테스트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4d87b-148">Each public action method in `TodoController` can be tested from the UI.</span></span> <span data-ttu-id="4d87b-149">메서드 이름을 클릭하여 섹션을 확장합니다.</span><span class="sxs-lookup"><span data-stu-id="4d87b-149">Click a method name to expand the section.</span></span> <span data-ttu-id="4d87b-150">필요한 매개 변수를 추가하고 “직접 시험해 보세요.”를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="4d87b-150">Add any necessary parameters, and click "Try it out!".</span></span>

![예제 Swagger GET 테스트](web-api-help-pages-using-swagger/_static/get-try-it-out.png)

## <a name="customization--extensibility"></a><span data-ttu-id="4d87b-152">사용자 지정 및 확장성</span><span class="sxs-lookup"><span data-stu-id="4d87b-152">Customization & Extensibility</span></span>

<span data-ttu-id="4d87b-153">Swagger는 개체 모델을 설명하고 테마와 일치하도록 UI를 사용자 지정하는 옵션을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="4d87b-153">Swagger provides options for documenting the object model and customizing the UI to match your theme.</span></span>

### <a name="api-info-and-description"></a><span data-ttu-id="4d87b-154">API 정보 및 설명</span><span class="sxs-lookup"><span data-stu-id="4d87b-154">API Info and Description</span></span>

<span data-ttu-id="4d87b-155">`AddSwaggerGen` 메서드에 전달되는 구성 작업은 작성자, 라이선스 및 설명과 같은 정보를 추가하는 데 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4d87b-155">The configuration action passed to the `AddSwaggerGen` method can be used to add information such as the author, license, and description:</span></span>

[!code-csharp[Main](../tutorials/web-api-help-pages-using-swagger/sample/TodoApi/Startup.cs?range=20-30,36)]

<span data-ttu-id="4d87b-156">다음 이미지에서는 버전 정보를 표시하는 Swagger UI를 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="4d87b-156">The following image depicts the Swagger UI displaying the version information:</span></span>

![버전 정보가 포함된 Swagger UI: 설명, 작성자 및 추가 링크 확인](web-api-help-pages-using-swagger/_static/custom-info.png)

### <a name="xml-comments"></a><span data-ttu-id="4d87b-158">XML 주석</span><span class="sxs-lookup"><span data-stu-id="4d87b-158">XML Comments</span></span>

<span data-ttu-id="4d87b-159">XML 주석은 다음 방법으로 사용하도록 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4d87b-159">XML comments can be enabled with the following approaches:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4d87b-160">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4d87b-160">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4d87b-161">**솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **속성** 선택</span><span class="sxs-lookup"><span data-stu-id="4d87b-161">Right-click the project in **Solution Explorer** and select **Properties**</span></span>
* <span data-ttu-id="4d87b-162">**빌드** 탭의 **출력** 섹션에서 **XML 문서 파일** 상자 선택:</span><span class="sxs-lookup"><span data-stu-id="4d87b-162">Check the **XML documentation file** box under the **Output** section of the **Build** tab:</span></span>

![프로젝트 속성의 빌드 탭](web-api-help-pages-using-swagger/_static/swagger-xml-comments.png)

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="4d87b-164">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="4d87b-164">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="4d87b-165">**프로젝트 옵션** 대화 상자 열기 > **빌드** > **컴파일러** 선택</span><span class="sxs-lookup"><span data-stu-id="4d87b-165">Open the **Project Options** dialog > **Build** > **Compiler**</span></span>
* <span data-ttu-id="4d87b-166">**일반 옵션** 섹션에서 **XML 문서 생성** 상자 선택:</span><span class="sxs-lookup"><span data-stu-id="4d87b-166">Check the **Generate xml documentation** box under the **General Options** section:</span></span>

![프로젝트 옵션의 일반 옵션 섹션](web-api-help-pages-using-swagger/_static/swagger-xml-comments-mac.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="4d87b-168">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4d87b-168">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="4d87b-169">*.csproj* 파일에 다음 코드 조각을 수동으로 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="4d87b-169">Manually add the following snippet to the *.csproj* file:</span></span>

[!code-xml[Main](../tutorials/web-api-help-pages-using-swagger/sample/TodoApi/TodoApi.csproj?range=7-9)]

---

<span data-ttu-id="4d87b-170">생성된 XML 파일을 사용하도록 Swagger를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="4d87b-170">Configure Swagger to use the generated XML file.</span></span> <span data-ttu-id="4d87b-171">Linux 또는 Windows가 아닌 운영 체제의 경우 파일 이름 및 경로는 대/소문자를 구분할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4d87b-171">For Linux or non-Windows operating systems, file names and paths can be case sensitive.</span></span> <span data-ttu-id="4d87b-172">예를 들어 *ToDoApi.XML* 파일은 Windows에는 있지만 CentOS에는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="4d87b-172">For example, a *ToDoApi.XML* file would be found on Windows but not CentOS.</span></span>

[!code-csharp[Main](../tutorials/web-api-help-pages-using-swagger/sample/TodoApi/Startup.cs?name=snippet_ConfigureServices&highlight=20-22)]

<span data-ttu-id="4d87b-173">이전 코드에서 `ApplicationBasePath`는 앱의 기본 경로를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="4d87b-173">In the preceding code, `ApplicationBasePath` gets the base path of the app.</span></span> <span data-ttu-id="4d87b-174">기본 경로는 XML 주석 파일을 찾는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="4d87b-174">The base path is used to locate the XML comments file.</span></span> <span data-ttu-id="4d87b-175">*TodoApi.xml*은 이 예제에서만 작동합니다. 생성된 XML 주석 파일의 이름이 응용 프로그램 이름을 기준으로 하기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="4d87b-175">*TodoApi.xml* only works for this example, since the name of the generated XML comments file is based on the application name.</span></span>

<span data-ttu-id="4d87b-176">메서드에 3중 슬래시 주석을 추가하면 섹션 헤더에 설명이 추가되어 Swagger UI가 개선됩니다.</span><span class="sxs-lookup"><span data-stu-id="4d87b-176">Adding the triple-slash comments to the method enhances the Swagger UI by adding the description to the section header:</span></span>

[!code-csharp[Main](../tutorials/web-api-help-pages-using-swagger/sample/TodoApi/Controllers/TodoController.cs?name=snippet_Delete&highlight=2)]

![DELETE 메서드에 대한 XML 주석 ‘특정 TodoItem을 삭제합니다.’를 보여 주는](web-api-help-pages-using-swagger/_static/triple-slash-comments.png)

<span data-ttu-id="4d87b-179">UI는 생성된 JSON 파일을 통해 실행되므로 다음 주석을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="4d87b-179">The UI is driven by the generated JSON file, which also contains these comments:</span></span>

```json
"delete": {
    "tags": [
        "Todo"
    ],
    "summary": "Deletes a specific TodoItem.",
    "operationId": "ApiTodoByIdDelete",
    "consumes": [],
    "produces": [],
    "parameters": [
        {
            "name": "id",
            "in": "path",
            "description": "",
            "required": true,
            "type": "integer",
            "format": "int64"
        }
    ],
    "responses": {
        "200": {
            "description": "Success"
        }
    }
}
```

<span data-ttu-id="4d87b-180">[<remarks>](https://docs.microsoft.com/dotnet/csharp/programming-guide/xmldoc/remarks) 태그를 `Create` 작업 메서드 문서에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="4d87b-180">Add a [<remarks>](https://docs.microsoft.com/dotnet/csharp/programming-guide/xmldoc/remarks) tag to the `Create` action method documentation.</span></span> <span data-ttu-id="4d87b-181">이 태그는 `<summary>` 태그에 지정된 정보를 보충하고 더 강력한 Swagger UI를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="4d87b-181">It supplements information specified in the `<summary>` tag and provides a more robust Swagger UI.</span></span> <span data-ttu-id="4d87b-182">`<remarks>` 태그 콘텐츠는 텍스트, JSON 또는 XML로 구성될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4d87b-182">The `<remarks>` tag content can consist of text, JSON, or XML.</span></span>

[!code-csharp[Main](../tutorials/web-api-help-pages-using-swagger/sample/TodoApi/Controllers/TodoController.cs?name=snippet_Create&highlight=4-14)]

<span data-ttu-id="4d87b-183">이러한 추가 주석이 포함된 향상된 UI 기능을 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4d87b-183">Notice the UI enhancements with these additional comments.</span></span>

![추가 주석이 표시된 Swagger UI](web-api-help-pages-using-swagger/_static/xml-comments-extended.png)

### <a name="data-annotations"></a><span data-ttu-id="4d87b-185">데이터 주석</span><span class="sxs-lookup"><span data-stu-id="4d87b-185">Data Annotations</span></span>

<span data-ttu-id="4d87b-186">`System.ComponentModel.DataAnnotations`에 있는 특성으로 모델을 데코레이트하면 Swagger UI 구성 요소를 실행하는 데 도움이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4d87b-186">Decorate the model with attributes, found in `System.ComponentModel.DataAnnotations`, to help drive the Swagger UI components.</span></span>

<span data-ttu-id="4d87b-187">`[Required]` 특성을 `TodoItem` 클래스의 `Name` 속성에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="4d87b-187">Add the `[Required]` attribute to the `Name` property of the `TodoItem` class:</span></span>

[!code-csharp[Main](../tutorials/web-api-help-pages-using-swagger/sample/TodoApi/Models/TodoItem.cs?highlight=10)]

<span data-ttu-id="4d87b-188">이 특성이 있으면 UI 동작이 변경되고 기본 JSON 스키마가 변경됩니다.</span><span class="sxs-lookup"><span data-stu-id="4d87b-188">The presence of this attribute changes the UI behavior and alters the underlying JSON schema:</span></span>

```json
"definitions": {
    "TodoItem": {
        "required": [
            "name"
        ],
        "type": "object",
        "properties": {
            "id": {
                "format": "int64",
                "type": "integer"
            },
            "name": {
                "type": "string"
            },
            "isComplete": {
                "default": false,
                "type": "boolean"
            }
        }
    }
},
```

<span data-ttu-id="4d87b-189">API 컨트롤러에 `[Produces("application/json")]` 특성을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="4d87b-189">Add the `[Produces("application/json")]` attribute to the API controller.</span></span> <span data-ttu-id="4d87b-190">이 특성은 컨트롤러 동작이 *application/json* 콘텐츠 형식의 반환을 지원하도록 선언하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="4d87b-190">Its purpose is to declare that the controller's actions support a return a content type of *application/json*:</span></span>

[!code-csharp[Main](../tutorials/web-api-help-pages-using-swagger/sample/TodoApi/Controllers/TodoController.cs?name=snippet_TodoController&highlight=3)]

<span data-ttu-id="4d87b-191">컨트롤러 GET 작업의 경우 **응답 콘텐츠 형식** 드롭다운에서 이 콘텐츠 형식이 기본값으로 선택됩니다.</span><span class="sxs-lookup"><span data-stu-id="4d87b-191">The **Response Content Type** drop-down selects this content type as the default for the controller's GET actions:</span></span>

![기본 응답 콘텐츠 형식이 포함된 Swagger UI](web-api-help-pages-using-swagger/_static/json-response-content-type.png)

<span data-ttu-id="4d87b-193">Web API에서 데이터 주석 사용이 증가하면 UI 및 API 도움말 페이지에는 더 자세한 설명과 유용한 정보가 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="4d87b-193">As the usage of data annotations in the Web API increases, the UI and API help pages become more descriptive and useful.</span></span>

### <a name="describing-response-types"></a><span data-ttu-id="4d87b-194">응답 형식 설명</span><span class="sxs-lookup"><span data-stu-id="4d87b-194">Describing Response Types</span></span>

<span data-ttu-id="4d87b-195">소비 개발자는 반환된 항목 특히, 응답 형식 및 오류 코드(표준이 아닌 경우)를 가장 중요하게 생각합니다.&mdash;</span><span class="sxs-lookup"><span data-stu-id="4d87b-195">Consuming developers are most concerned with what is returned &mdash; specifically response types and error codes (if not standard).</span></span> <span data-ttu-id="4d87b-196">이러한 항목은 XML 주석 및 데이터 주석에서 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="4d87b-196">These are handled in the XML comments and data annotations.</span></span>

<span data-ttu-id="4d87b-197">`Create` 작업은 성공 시 `201 Created`를 반환하고, 게시된 요청 본문이 null일 경우 `400 Bad Request`를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="4d87b-197">The `Create` action returns `201 Created` on success or `400 Bad Request` when the posted request body is null.</span></span> <span data-ttu-id="4d87b-198">Swagger UI에 적절한 문서가 없으면 소비자는 이러한 예상 결과에 대한 정보를 알 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="4d87b-198">Without proper documentation in the Swagger UI, the consumer lacks knowledge of these expected outcomes.</span></span> <span data-ttu-id="4d87b-199">다음 예제에서 강조 표시된 줄을 추가하면 이 문제가 해결됩니다.</span><span class="sxs-lookup"><span data-stu-id="4d87b-199">That problem is fixed by adding the highlighted lines in the following example:</span></span>

[!code-csharp[Main](../tutorials/web-api-help-pages-using-swagger/sample/TodoApi/Controllers/TodoController.cs?name=snippet_Create&highlight=17,18,20,21)]

<span data-ttu-id="4d87b-200">이제 Swagger UI에서는 예상 HTTP 응답 코드를 분명히 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="4d87b-200">The Swagger UI now clearly documents the expected HTTP response codes:</span></span>

![POST 응답 클래스 설명 ‘새로 만들어진 Todo 항목 반환’ 및 ‘400 - 응답 메시지에서 항목의 상태 코드 및 이유가 null인 경우’를 보여 주는 Swagger UI](web-api-help-pages-using-swagger/_static/data-annotations-response-types.png)

### <a name="customizing-the-ui"></a><span data-ttu-id="4d87b-202">UI 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="4d87b-202">Customizing the UI</span></span>

<span data-ttu-id="4d87b-203">스톡 UI는 기능적인 동시에 표현 가능하지만 API에 대한 문서 페이지를 빌드할 경우 스톡 UI로 브랜드나 테마를 표현하고자 합니다.</span><span class="sxs-lookup"><span data-stu-id="4d87b-203">The stock UI is both functional and presentable; however, when building documentation pages for your API, you want it to represent your brand or theme.</span></span> <span data-ttu-id="4d87b-204">Swashbuckle 구성 요소를 사용하여 해당 작업을 수행하려면 리소스를 추가하여 정적 파일을 지원하고 폴더 구조를 빌드하여 해당 파일을 호스트합니다.</span><span class="sxs-lookup"><span data-stu-id="4d87b-204">Accomplishing that task with the Swashbuckle components requires adding the resources to serve static files and then building the folder structure to host those files.</span></span>

<span data-ttu-id="4d87b-205">.NET Framework를 대상으로 지정할 경우 `Microsoft.AspNetCore.StaticFiles` NuGet 패키지를 프로젝트에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="4d87b-205">If targeting .NET Framework, add the `Microsoft.AspNetCore.StaticFiles` NuGet package to the project:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.StaticFiles" Version="2.0.0" />
```

<span data-ttu-id="4d87b-206">정적 파일 미들웨어를 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="4d87b-206">Enable the static files middleware:</span></span>

[!code-csharp[Main](../tutorials/web-api-help-pages-using-swagger/sample/TodoApi/Startup.cs?name=snippet_Configure&highlight=3)]

<span data-ttu-id="4d87b-207">[Swagger UI GitHub 리포지토리](https://github.com/swagger-api/swagger-ui/tree/2.x/dist)에서 *dist* 폴더의 콘텐츠를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="4d87b-207">Acquire the contents of the *dist* folder from the [Swagger UI GitHub repository](https://github.com/swagger-api/swagger-ui/tree/2.x/dist).</span></span> <span data-ttu-id="4d87b-208">이 폴더에는 Swagger UI 페이지에 필요한 자산이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="4d87b-208">This folder contains the necessary assets for the Swagger UI page.</span></span> <span data-ttu-id="4d87b-209">해당 폴더의 콘텐츠를 *wwwroot/swagger/ui* 폴더로 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="4d87b-209">Copy the contents of that folder into the *wwwroot/swagger/ui* folder.</span></span>

<span data-ttu-id="4d87b-210">다음 CSS가 포함된 *wwwroot/swagger/ui/css/custom.css* 파일을 만들어 페이지 헤더를 사용자 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="4d87b-210">Create a *wwwroot/swagger/ui/css/custom.css* file with the following CSS to customize the page header:</span></span>

[!code-css[Main](../tutorials/web-api-help-pages-using-swagger/sample/TodoApi/wwwroot/swagger/ui/css/custom.css)]

<span data-ttu-id="4d87b-211">*index.html* 파일에서 *custom.css*를 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="4d87b-211">Reference *custom.css* in the *index.html* file:</span></span>

[!code-html[Main](../tutorials/web-api-help-pages-using-swagger/sample/TodoApi/wwwroot/swagger/ui/index.html?range=14)]

<span data-ttu-id="4d87b-212">`http://localhost:<random_port>/swagger/ui/index.html`의 *index.html* 페이지로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="4d87b-212">Browse to the *index.html* page at `http://localhost:<random_port>/swagger/ui/index.html`.</span></span> <span data-ttu-id="4d87b-213">헤더의 텍스트 상자에 `http://localhost:<random_port>/swagger/v1/swagger.json`을 입력하고 **탐색** 단추를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="4d87b-213">Enter `http://localhost:<random_port>/swagger/v1/swagger.json` in the header's textbox, and click the **Explore** button.</span></span> <span data-ttu-id="4d87b-214">결과 페이지가 다음과 같이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="4d87b-214">The resulting page looks as follows:</span></span>

![사용자 지정 헤더 제목이 포함된 Swagger UI](web-api-help-pages-using-swagger/_static/custom-header.png)

<span data-ttu-id="4d87b-216">페이지를 사용하여 훨씬 더 많은 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4d87b-216">There is much more you can do with the page.</span></span> <span data-ttu-id="4d87b-217">[Swagger UI GitHub 리포지토리](https://github.com/swagger-api/swagger-ui)에서 UI 리소스에 대한 전체 기능을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="4d87b-217">See the full capabilities for the UI resources at the [Swagger UI GitHub repository](https://github.com/swagger-api/swagger-ui).</span></span>
