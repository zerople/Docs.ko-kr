---
title: "모델 추가"
author: rick-anderson
description: "간단한 ASP.NET Core 앱에 모델을 추가합니다."
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 03/30/2017
ms.topic: get-started-article
ms.assetid: 8dc28498-eeee-4666-b903-b593059e9f39
ms.technology: aspnet
ms.prod: asp.net-core
uid: tutorials/first-mvc-app-xplat/adding-model
ms.openlocfilehash: c68466a645687b6fe0193e9deec2f32632e6f0e7
ms.sourcegitcommit: 9cdbfd0d670d70b9c354216aabee260c52dad5ee
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/12/2017
---
[!INCLUDE[adding-model1](../../includes/mvc-intro/adding-model1.md)]

* <span data-ttu-id="a137d-104">*Models* 폴더에 *Movie.cs* 클래스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="a137d-104">Add a class to the *Models* folder named *Movie.cs*.</span></span>
* <span data-ttu-id="a137d-105">*Models/Movie.cs* 파일에 다음 코드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="a137d-105">Add the following code to the *Models/Movie.cs* file:</span></span>

<span data-ttu-id="a137d-106">[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Models/MovieNoEF.cs?name=snippet_1)]</span><span class="sxs-lookup"><span data-stu-id="a137d-106">[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Models/MovieNoEF.cs?name=snippet_1)]</span></span>

<span data-ttu-id="a137d-107">`ID` 필드는 데이터베이스에서 기본 키 대신 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="a137d-107">The `ID` field is required by the database for the primary key.</span></span> 

<span data-ttu-id="a137d-108">앱을 빌드하여 오류가 없고 마지막으로 **모**델을 **M**VC 앱에 추가했는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="a137d-108">Build the app to verify you don't have any errors, and you've finally added a **M**odel to your **M**VC app.</span></span>

## <a name="prepare-the-project-for-scaffolding"></a><span data-ttu-id="a137d-109">프로젝트에서 스캐폴딩을 준비합니다.</span><span class="sxs-lookup"><span data-stu-id="a137d-109">Prepare the project for scaffolding</span></span>

- <span data-ttu-id="a137d-110">다음 강조 표시된 NuGet 패키지를 *MvcMovie.csproj* 파일에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="a137d-110">Add the following highlighted NuGet packages to the *MvcMovie.csproj* file:</span></span>
             
   <span data-ttu-id="a137d-111">[!code-csharp[Main](start-mvc/sample/MvcMovie/MvcMovie.csproj?highlight=7,10)]</span><span class="sxs-lookup"><span data-stu-id="a137d-111">[!code-csharp[Main](start-mvc/sample/MvcMovie/MvcMovie.csproj?highlight=7,10)]</span></span>

- <span data-ttu-id="a137d-112">파일을 저장하고 다음 **정보** 메시지에 대해 **복원**을 선택합니다. “확인되지 않은 종속성이 있습니다.”</span><span class="sxs-lookup"><span data-stu-id="a137d-112">Save the file and select **Restore** to the **Info** message "There are unresolved dependencies".</span></span>
- <span data-ttu-id="a137d-113">*Models/MvcMovieContext.cs* 파일을 만들고 다음 `MvcMovieContext` 클래스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="a137d-113">Create a *Models/MvcMovieContext.cs* file and add the following `MvcMovieContext` class:</span></span>

   <span data-ttu-id="a137d-114">[!code-csharp[Main](start-mvc/sample/MvcMovie/Models/MvcMovieContext.cs)]</span><span class="sxs-lookup"><span data-stu-id="a137d-114">[!code-csharp[Main](start-mvc/sample/MvcMovie/Models/MvcMovieContext.cs)]</span></span>
   
- <span data-ttu-id="a137d-115">*Startup.cs* 파일을 열고 두 개의 using을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="a137d-115">Open the *Startup.cs* file and add two usings:</span></span>

   <span data-ttu-id="a137d-116">[!code-csharp[Main](start-mvc/sample/MvcMovie/Startup.cs?name=snippet1&highlight=1,2)]</span><span class="sxs-lookup"><span data-stu-id="a137d-116">[!code-csharp[Main](start-mvc/sample/MvcMovie/Startup.cs?name=snippet1&highlight=1,2)]</span></span>

- <span data-ttu-id="a137d-117">데이터베이스 컨텍스트를 *Startup.cs* 파일에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="a137d-117">Add the database context to the *Startup.cs* file:</span></span>

   <span data-ttu-id="a137d-118">[!code-csharp[Main](start-mvc/sample/MvcMovie/Startup.cs?name=snippet2&highlight=6-7)]</span><span class="sxs-lookup"><span data-stu-id="a137d-118">[!code-csharp[Main](start-mvc/sample/MvcMovie/Startup.cs?name=snippet2&highlight=6-7)]</span></span>

  <span data-ttu-id="a137d-119">이 코드는 데이터 모델에 포함되는 모델 클래스를 Entity Framework에 알립니다.</span><span class="sxs-lookup"><span data-stu-id="a137d-119">This tells Entity Framework which model classes are included in the data model.</span></span> <span data-ttu-id="a137d-120">데이터베이스에서 Movie 테이블을 표현할 Movie 개체의 *엔터티 집합* 하나를 정의하고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a137d-120">You're defining one *entity set* of Movie objects, which will be represented in the database as a Movie table.</span></span>

- <span data-ttu-id="a137d-121">프로젝트를 빌드하여 오류가 없는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="a137d-121">Build the project to verify there are no errors.</span></span>

## <a name="scaffold-the-moviecontroller"></a><span data-ttu-id="a137d-122">MovieController 스캐폴드</span><span class="sxs-lookup"><span data-stu-id="a137d-122">Scaffold the MovieController</span></span>

<span data-ttu-id="a137d-123">프로젝트 폴더에서 터미널 창을 열고 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="a137d-123">Open a terminal window in the project folder and run the following commands:</span></span>

```
dotnet restore
dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries 
```

> [!NOTE]
> <span data-ttu-id="a137d-124">스캐폴딩 명령이 실행될 때 오류가 발생할 경우 해결 방법은 [issue 444 in the scaffolding repository](https://github.com/aspnet/scaffolding/issues/444)(스캐폴딩 리포지토리의 문제 444)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a137d-124">If you get an error when the scaffolding command runs, see [issue 444 in the scaffolding repository](https://github.com/aspnet/scaffolding/issues/444) for a workaround.</span></span>

<span data-ttu-id="a137d-125">스캐폴딩 엔진은 다음을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="a137d-125">The scaffolding engine creates the following:</span></span>

* <span data-ttu-id="a137d-126">동영상 컨트롤러(*Controllers/MoviesController.cs*)</span><span class="sxs-lookup"><span data-stu-id="a137d-126">A movies controller (*Controllers/MoviesController.cs*)</span></span>
* <span data-ttu-id="a137d-127">만들기, 삭제, 세부 정보, 편집 및 인덱스 페이지에 대한 Razor 뷰 파일(*Views/Movies/\*.cshtml*)</span><span class="sxs-lookup"><span data-stu-id="a137d-127">Razor view files for Create, Delete, Details, Edit and Index pages (*Views/Movies/\*.cshtml*)</span></span>

<span data-ttu-id="a137d-128">[CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete)(만들기, 읽기, 업데이트 및 삭제) 작업 메서드와 뷰를 자동으로 만드는 작업을 *스캐폴딩*이라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="a137d-128">The automatic creation of [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (create, read, update, and delete) action methods and views is known as *scaffolding*.</span></span> <span data-ttu-id="a137d-129">동영상 데이터베이스를 관리할 수 있는 완벽하게 작동하는 웹 응용 프로그램이 곧 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="a137d-129">You'll soon have a fully functional web application that lets you manage a movie database.</span></span>

[!INCLUDE[adding-model 2x](../../includes/mvc-intro/adding-model2xp.md)]

[!INCLUDE[adding-model](../../includes/mvc-intro/adding-model3.md)]

<span data-ttu-id="a137d-130">이제 데이터를 표시, 편집, 업데이트 및 삭제할 데이터베이스 및 페이지가 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="a137d-130">You now have a database and pages to display, edit, update and delete data.</span></span> <span data-ttu-id="a137d-131">다음 자습서에서는 데이터베이스 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="a137d-131">In the next tutorial, we'll work with the database.</span></span>

### <a name="additional-resources"></a><span data-ttu-id="a137d-132">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="a137d-132">Additional resources</span></span>

* [<span data-ttu-id="a137d-133">태그 도우미</span><span class="sxs-lookup"><span data-stu-id="a137d-133">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="a137d-134">전역화 및 지역화</span><span class="sxs-lookup"><span data-stu-id="a137d-134">Globalization and localization</span></span>](xref:fundamentals/localization)

>[!div class="step-by-step"]
<span data-ttu-id="a137d-135">[이전 - 뷰 추가](adding-view.md)
[다음 - SQLite 사용](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="a137d-135">[Previous - Add a view](adding-view.md)
[Next - Working with SQLite](working-with-sql.md)</span></span>
