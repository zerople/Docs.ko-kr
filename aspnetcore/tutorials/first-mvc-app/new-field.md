---
title: "새 필드 추가"
author: rick-anderson
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: get-started-article
ms.assetid: 16efbacf-fe7b-4b41-84b0-06a1574b95c2
ms.technology: aspnet
ms.prod: asp.net-core
uid: tutorials/first-mvc-app/new-field
ms.openlocfilehash: 7d7e7055dd6dc0a2aefd8f4a0a170483b8504267
ms.sourcegitcommit: 9cdbfd0d670d70b9c354216aabee260c52dad5ee
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/12/2017
---
# <a name="adding-a-new-field"></a><span data-ttu-id="ed00d-103">새 필드 추가</span><span class="sxs-lookup"><span data-stu-id="ed00d-103">Adding a New Field</span></span>

<span data-ttu-id="ed00d-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="ed00d-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="ed00d-105">이 섹션에서는 [Entity Framework](https://docs.microsoft.com/ef/core/get-started/aspnetcore/new-db) Code First 마이그레이션을 사용하여 모델에 새 필드를 추가하고 해당 변경 내용을 데이터베이스로 마이그레이션합니다.</span><span class="sxs-lookup"><span data-stu-id="ed00d-105">In this section you'll use [Entity Framework](https://docs.microsoft.com/ef/core/get-started/aspnetcore/new-db) Code First Migrations to add a new field to the model and migrate that change to the database.</span></span>

<span data-ttu-id="ed00d-106">EF Code First를 사용하여 자동으로 데이터베이스를 만들 때 Code First는 데이터베이스에 테이블을 추가하여 데이터베이스의 스키마가 생성된 모델 클래스와 동기화되어 있는지 여부를 추적합니다.</span><span class="sxs-lookup"><span data-stu-id="ed00d-106">When you use EF Code First to automatically create a database, Code First adds a table to the database to help track whether the schema of the database is in sync with the model classes it was generated from.</span></span> <span data-ttu-id="ed00d-107">동기화되어 있지 않은 경우 EF에서 예외를 throw합니다.</span><span class="sxs-lookup"><span data-stu-id="ed00d-107">If they aren't in sync, EF throws an exception.</span></span> <span data-ttu-id="ed00d-108">이렇게 하면 더 쉽게 일관성이 없는 데이터베이스/코드 문제를 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ed00d-108">This makes it easier to find inconsistent database/code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="ed00d-109">동영상 모델에 등급 속성 추가</span><span class="sxs-lookup"><span data-stu-id="ed00d-109">Adding a Rating Property to the Movie Model</span></span>

<span data-ttu-id="ed00d-110">*Models/Movie.cs* 파일을 열고 `Rating` 속성을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="ed00d-110">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

<span data-ttu-id="ed00d-111">[!code-csharp[Main](start-mvc/sample/MvcMovie/Models/MovieDateRating.cs?highlight=11&range=7-18)]</span><span class="sxs-lookup"><span data-stu-id="ed00d-111">[!code-csharp[Main](start-mvc/sample/MvcMovie/Models/MovieDateRating.cs?highlight=11&range=7-18)]</span></span>

<span data-ttu-id="ed00d-112">앱을 빌드합니다(Ctrl+Shift+B).</span><span class="sxs-lookup"><span data-stu-id="ed00d-112">Build the app (Ctrl+Shift+B).</span></span>

<span data-ttu-id="ed00d-113">`Movie` 클래스에 새 필드를 추가했으므로 이 새 속성이 포함되도록 바인딩 허용 목록도 업데이트해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ed00d-113">Because you've added a new field to the `Movie` class, you also need to update the binding white list so this new property will be included.</span></span> <span data-ttu-id="ed00d-114">*MoviesController.cs*에서 `Rating` 속성을 포함하도록 `Create` 및 `Edit` 동작 메서드에 대해 `[Bind]` 속성을 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="ed00d-114">In *MoviesController.cs*, update the `[Bind]` attribute for both the `Create` and `Edit` action methods to include the `Rating` property:</span></span>

```csharp
[Bind("ID,Title,ReleaseDate,Genre,Price,Rating")]
   ```

<span data-ttu-id="ed00d-115">브라우저 보기에서 새 `Rating` 속성을 표시, 작성 및 편집하기 위해 보기 템플릿도 업데이트해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ed00d-115">You also need to update the view templates in order to display, create and edit the new `Rating` property in the browser view.</span></span>

<span data-ttu-id="ed00d-116">*/Views/Movies/Index.cshtml* 파일을 편집하고 `Rating` 필드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="ed00d-116">Edit the */Views/Movies/Index.cshtml* file and add a `Rating` field:</span></span>

<span data-ttu-id="ed00d-117">[!code-HTML[Main](start-mvc/sample/MvcMovie/Views/Movies/IndexGenreRating.cshtml?highlight=17,39&range=24-64)]</span><span class="sxs-lookup"><span data-stu-id="ed00d-117">[!code-HTML[Main](start-mvc/sample/MvcMovie/Views/Movies/IndexGenreRating.cshtml?highlight=17,39&range=24-64)]</span></span>

<span data-ttu-id="ed00d-118">`Rating` 필드로 */Views/Movies/Create.cshtml*을 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="ed00d-118">Update the */Views/Movies/Create.cshtml* with a `Rating` field.</span></span> <span data-ttu-id="ed00d-119">이전 "양식 그룹"을 복사/붙여넣기하고 intelliSense에서 필드를 업데이트하도록 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ed00d-119">You can copy/paste the previous "form group" and let intelliSense help you update the fields.</span></span> <span data-ttu-id="ed00d-120">IntelliSense는 [태그 도우미](xref:mvc/views/tag-helpers/intro)와 함께 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="ed00d-120">IntelliSense works with [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="ed00d-121">참고: Visual Studio 2017의 RTM 버전에서 Razor intelliSense에 대해 [Razor 언어 서비스](https://marketplace.visualstudio.com/items?itemName=ms-madsk.RazorLanguageServices)를 설치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ed00d-121">Note: In the RTM verison of Visual Studio 2017 you need to install the [Razor Language Services](https://marketplace.visualstudio.com/items?itemName=ms-madsk.RazorLanguageServices) for Razor intelliSense.</span></span> <span data-ttu-id="ed00d-122">그러면 다음 릴리스에서 수정됩니다.</span><span class="sxs-lookup"><span data-stu-id="ed00d-122">This will be fixed in the next release.</span></span>

![개발자가 보기의 두 번째 레이블 요소에서 asp-for의 특성 값에 대해 문자 R을 입력했습니다.](new-field/_static/cr.png)

<span data-ttu-id="ed00d-126">앱은 새 필드를 포함하도록 DB를 업데이트할 때까지 작동하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ed00d-126">The app won't work until we update the DB to include the new field.</span></span> <span data-ttu-id="ed00d-127">지금 실행하는 경우 다음 `SqlException`이 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="ed00d-127">If you run it now, you'll get the following `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="ed00d-128">업데이트된 Movie 모델 클래스는 기존 데이터베이스의 Movie 테이블의 스키마와 다르기 때문에 이 오류가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="ed00d-128">You're seeing this error because the updated Movie model class is different than the schema of the Movie table of the existing database.</span></span> <span data-ttu-id="ed00d-129">(데이터베이스 테이블에 Rating 열이 없습니다.)</span><span class="sxs-lookup"><span data-stu-id="ed00d-129">(There's no Rating column in the database table.)</span></span>

<span data-ttu-id="ed00d-130">오류를 해결하는 몇 가지 방법이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ed00d-130">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="ed00d-131">Entity Framework에서 새 모델 클래스 스키마에 따라 데이터베이스를 자동으로 삭제하고 다시 만들도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="ed00d-131">Have the Entity Framework automatically drop and re-create the database based on the new model class schema.</span></span> <span data-ttu-id="ed00d-132">이 방법은 테스트 데이터베이스에서 활발한 개발을 수행할 때 개발 주기의 초기 단계에서 매우 편리하며 신속하게 모델 및 데이터베이스 스키마를 함께 개발할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ed00d-132">This approach is very convenient early in the development cycle when you are doing active development on a test database; it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="ed00d-133">그러나 단점은 데이터베이스에서 기존 데이터를 손실한다는 것입니다. 따라서 프로덕션 데이터베이스에서 이 방법을 사용하지 않으려 합니다.</span><span class="sxs-lookup"><span data-stu-id="ed00d-133">The downside, though, is that you lose existing data in the database — so you don't want to use this approach on a production database!</span></span> <span data-ttu-id="ed00d-134">테스트 데이터로 데이터베이스를 자동으로 시드하는 데 이니셜라이저를 사용하는 것은 종종 응용 프로그램을 개발하는 효율적인 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="ed00d-134">Using an initializer to automatically seed a database with test data is often a productive way to develop an application.</span></span>

2. <span data-ttu-id="ed00d-135">모델 클래스와 일치하도록 기존 데이터베이스의 스키마를 명시적으로 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="ed00d-135">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="ed00d-136">이 방법의 장점은 데이터를 유지한다는 점입니다.</span><span class="sxs-lookup"><span data-stu-id="ed00d-136">The advantage of this approach is that you keep your data.</span></span> <span data-ttu-id="ed00d-137">이러한 변경을 수동으로 수행하거나 데이터베이스 변경 스크립트를 만들어 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ed00d-137">You can make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="ed00d-138">Code First 마이그레이션을 사용하여 데이터베이스 스키마를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="ed00d-138">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="ed00d-139">이 자습서의 경우 Code First 마이그레이션을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="ed00d-139">For this tutorial, we'll use Code First Migrations.</span></span>

<span data-ttu-id="ed00d-140">새 열에 대해 값을 제공하도록 `SeedData` 클래스를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="ed00d-140">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="ed00d-141">샘플 변경은 아래에 표시되지만 각 `new Movie`에 대해 이 변경을 수행하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="ed00d-141">A sample change is shown below, but you'll want to make this change for each `new Movie`.</span></span>

<span data-ttu-id="ed00d-142">[!code-csharp[Main](start-mvc/sample/MvcMovie/Models/SeedDataRating.cs?name=snippet1&highlight=6)]</span><span class="sxs-lookup"><span data-stu-id="ed00d-142">[!code-csharp[Main](start-mvc/sample/MvcMovie/Models/SeedDataRating.cs?name=snippet1&highlight=6)]</span></span>

<span data-ttu-id="ed00d-143">솔루션을 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="ed00d-143">Build the solution.</span></span>

<span data-ttu-id="ed00d-144">**도구** 메뉴에서 **NuGet 패키지 관리자 > 패키지 관리자 콘솔**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="ed00d-144">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>

  ![PMC 메뉴](adding-model/_static/pmc.png)

<span data-ttu-id="ed00d-146">PMC에서 다음 명령을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="ed00d-146">In the PMC, enter the following commands:</span></span>

```PMC
Add-Migration Rating
Update-Database
```

<span data-ttu-id="ed00d-147">`Add-Migration` 명령은 마이그레이션 프레임워크에서 현재 `Movie` DB 스키마로 현재 `Movie` 모델을 검사하고 DB를 새 모델로 마이그레이션하는 데 필요한 코드를 만들도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="ed00d-147">The `Add-Migration` command tells the migration framework to examine the current `Movie` model with the current `Movie` DB schema and create the necessary code to migrate the DB to the new model.</span></span> <span data-ttu-id="ed00d-148">"Rating" 이름은 임의로 지정되며 마이그레이션 파일의 이름을 지정하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="ed00d-148">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="ed00d-149">마이그레이션 파일에 의미 있는 이름을 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="ed00d-149">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="ed00d-150">DB의 모든 레코드를 삭제하는 경우 이니셜라이저에서 DB를 시드하고 `Rating` 필드를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="ed00d-150">If you delete all the records in the DB, the initialize will seed the DB and include the `Rating` field.</span></span> <span data-ttu-id="ed00d-151">브라우저 또는 SSOX에서 삭제 링크를 사용하여 이를 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ed00d-151">You can do this with the delete links in the browser or from SSOX.</span></span>

<span data-ttu-id="ed00d-152">앱을 실행하고 `Rating` 필드를 사용하여 동영상을 만들고/편집/표시할 수 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="ed00d-152">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="ed00d-153">또한 `Edit`, `Details` 및 `Delete` 보기 템플릿에 `Rating` 필드를 추가해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ed00d-153">You should also add the `Rating` field to the `Edit`, `Details`, and `Delete` view templates.</span></span>

>[!div class="step-by-step"]
<span data-ttu-id="ed00d-154">[이전](search.md)
[다음](validation.md)</span><span class="sxs-lookup"><span data-stu-id="ed00d-154">[Previous](search.md)
[Next](validation.md)</span></span>  
