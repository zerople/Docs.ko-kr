---
title: "ASP.NET Core에서 Razor 페이지 앱에 모델 추가"
author: rick-anderson
description: "ASP.NET Core에서 Razor 페이지 앱에 모델 추가"
keywords: "ASP.NET Core, Razor 페이지, Razor, MVC"
ms.author: riande
manager: wpickett
ms.date: 7/27/2017
ms.topic: get-started-article
ms.technology: aspnet
ms.prod: aspnet-core
uid: tutorials/razor-pages/modelz
ms.openlocfilehash: 1a08ecf1ee12fa0860cb6a18c1a63eaff2ddfbed
ms.sourcegitcommit: d9ec19e5452af83648074db5d96c0a0f4f9e7f9a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/29/2017
---
# <a name="adding-a-model-to-a-razor-pages-app"></a><span data-ttu-id="dea46-104">Razor 페이지 앱에 모델 추가</span><span class="sxs-lookup"><span data-stu-id="dea46-104">Adding a model to a Razor Pages app</span></span>

[!INCLUDE[model1](../../includes/RP/model1.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="dea46-105">데이터 모델 추가</span><span class="sxs-lookup"><span data-stu-id="dea46-105">Add a data model</span></span>

<span data-ttu-id="dea46-106">솔루션 탐색기에서 **RazorPagesMovie** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 폴더**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="dea46-106">In Solution Explorer, right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="dea46-107">폴더 이름을 *Models*로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="dea46-107">Name the folder *Models*.</span></span>

<span data-ttu-id="dea46-108">*Models* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **클래스**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="dea46-108">Right click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="dea46-109">클래스 이름을 **Movie**로 지정하고 다음 속성을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="dea46-109">Name the class **Movie** and add the following properties:</span></span>

[!INCLUDE[model 2](../../includes/RP/model2.md)]

<a name="cs"></a>
### <a name="add-a-database-connection-string"></a><span data-ttu-id="dea46-110">데이터베이스 연결 문자열 추가</span><span class="sxs-lookup"><span data-stu-id="dea46-110">Add a database connection string</span></span>

<span data-ttu-id="dea46-111">*appsettings.json* 파일에 연결 문자열을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="dea46-111">Add a connection string to the *appsettings.json* file.</span></span>

<span data-ttu-id="dea46-112">[!code-json[Main](../../tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings.json?highlight=8-10)]</span><span class="sxs-lookup"><span data-stu-id="dea46-112">[!code-json[Main](../../tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings.json?highlight=8-10)]</span></span>

<a name="reg"></a>
###  <a name="register-the-database-context"></a><span data-ttu-id="dea46-113">데이터베이스 컨텍스트 등록</span><span class="sxs-lookup"><span data-stu-id="dea46-113">Register the database context</span></span>

<span data-ttu-id="dea46-114">*Startup.cs* 파일에서 [종속성 주입](xref:fundamentals/dependency-injection) 컨테이너에 데이터베이스 컨텍스트를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="dea46-114">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in the *Startup.cs* file.</span></span>

<span data-ttu-id="dea46-115">[!code-csharp[Main](../../tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/Startup.cs?name=snippet_ConfigureServices&highlight=3-6)]</span><span class="sxs-lookup"><span data-stu-id="dea46-115">[!code-csharp[Main](../../tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/Startup.cs?name=snippet_ConfigureServices&highlight=3-6)]</span></span>

<span data-ttu-id="dea46-116">프로젝트를 빌드하여 오류가 없는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="dea46-116">Build the project to verify you don't have any errors.</span></span>

<a name="pmc"></a>
## <a name="add-scaffold-tooling-and-perform-initial-migration"></a><span data-ttu-id="dea46-117">스캐폴드 도구 추가 및 초기 마이그레이션 수행</span><span class="sxs-lookup"><span data-stu-id="dea46-117">Add scaffold tooling and perform initial migration</span></span>

<span data-ttu-id="dea46-118">이 섹션에서는 PMC(패키지 관리자 콘솔)를 사용하여 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="dea46-118">In this section, you use the Package Manager Console (PMC) to:</span></span>

* <span data-ttu-id="dea46-119">Visual Studio 웹 코드 생성 패키지를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="dea46-119">Add the Visual Studio web code generation package.</span></span> <span data-ttu-id="dea46-120">스캐폴딩 엔진을 실행하려면 이 패키지가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="dea46-120">This package is required to run the scaffolding engine.</span></span>
* <span data-ttu-id="dea46-121">초기 마이그레이션을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="dea46-121">Add an initial migration.</span></span>
* <span data-ttu-id="dea46-122">초기 마이그레이션을 사용하여 데이터베이스를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="dea46-122">Update the database with the initial migration.</span></span>

<span data-ttu-id="dea46-123">**도구** 메뉴에서 **NuGet 패키지 관리자 > 패키지 관리자 콘솔**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="dea46-123">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>

  ![PMC 메뉴](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="dea46-125">PMC에서 다음 명령을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="dea46-125">In the PMC, enter the following commands:</span></span>

```powershell
Install-Package Microsoft.VisualStudio.Web.CodeGeneration.Design -Version 2.0.0
Add-Migration Initial
Update-Database
```

<span data-ttu-id="dea46-126">`Install-Package` 명령은 스캐폴딩 엔진을 실행하는 데 필요한 도구를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="dea46-126">The `Install-Package` command installs the tooling required to run the scaffolding engine.</span></span>

<span data-ttu-id="dea46-127">`Add-Migration` 명령은 초기 데이터베이스 스키마를 만드는 코드를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="dea46-127">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="dea46-128">스키마는 `DbContext`에 지정된 모델을 기반으로 합니다(*Models/MovieContext.cs* 파일에서).</span><span class="sxs-lookup"><span data-stu-id="dea46-128">The schema is based on the model specified in the `DbContext` (In the *Models/MovieContext.cs* file).</span></span> <span data-ttu-id="dea46-129">`Initial` 인수는 마이그레이션 이름을 지정하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="dea46-129">The `Initial` argument is used to name the migrations.</span></span> <span data-ttu-id="dea46-130">모든 이름을 사용할 수 있지만 일반적으로 마이그레이션을 설명하는 이름을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="dea46-130">You can use any name, but by convention you choose a name that describes the migration.</span></span> <span data-ttu-id="dea46-131">자세한 내용은 [마이그레이션 소개](xref:data/ef-mvc/migrations#introduction-to-migrations)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="dea46-131">See [Introduction to migrations](xref:data/ef-mvc/migrations#introduction-to-migrations) for more information.</span></span>

<span data-ttu-id="dea46-132">`Update-Database` 명령은 데이터베이스를 만드는 *Migrations/\<time-stamp>_InitialCreate.cs* 파일에서 `Up` 메서드를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="dea46-132">The `Update-Database` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

[!INCLUDE[model 4windows](../../includes/RP/model4Win.md)]

[!INCLUDE[model 4](../../includes/RP/model4.md)]

<span data-ttu-id="dea46-133">다음 자습서에서는 스캐폴딩을 통해 만들어진 파일을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="dea46-133">The next tutorial explains the files created by scaffolding.</span></span>

>[!div class="step-by-step"]
<span data-ttu-id="dea46-134">[이전: 시작](xref:tutorials/razor-pages/razor-pages-start)
[다음: 스캐폴드된 Razor 페이지](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="dea46-134">[Previous: Getting Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>    