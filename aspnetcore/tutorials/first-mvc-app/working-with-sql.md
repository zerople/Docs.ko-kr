---
title: "SQL Server LocalDB 사용"
author: rick-anderson
description: "간단한 MVC 앱으로 SQL Server LocalDB 사용"
keywords: ASP.NET Core, SQL Server LocalDB, SQL Server, LocalDB
ms.author: riande
manager: wpickett
ms.date: 03/07/2017
ms.topic: get-started-article
ms.assetid: ff8fd9b8-7c98-424d-8641-7524e23bf541
ms.technology: aspnet
ms.prod: asp.net-core
uid: tutorials/first-mvc-app/working-with-sql
ms.openlocfilehash: dd8b8603d8444c95f086fd593aabe86d60f93ad4
ms.sourcegitcommit: eb025f2166023e1c394a0213c7ed8a9ca7190da5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2017
---
# <a name="working-with-sql-server-localdb"></a><span data-ttu-id="3eef5-104">SQL Server LocalDB 사용</span><span class="sxs-lookup"><span data-stu-id="3eef5-104">Working with SQL Server LocalDB</span></span>

<span data-ttu-id="3eef5-105">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="3eef5-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="3eef5-106">`MvcMovieContext` 개체는 데이터베이스에 연결하고 데이터베이스 레코드에 `Movie` 개체를 매핑하는 작업을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="3eef5-106">The `MvcMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="3eef5-107">데이터베이스 컨텍스트는 *Startup.cs* 파일의 `ConfigureServices` 메서드에서 [종속성 주입](xref:fundamentals/dependency-injection) 컨테이너에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="3eef5-107">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in the *Startup.cs* file:</span></span>

<span data-ttu-id="3eef5-108">[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?name=ConfigureServices&highlight=6-7)]</span><span class="sxs-lookup"><span data-stu-id="3eef5-108">[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?name=ConfigureServices&highlight=6-7)]</span></span>

<span data-ttu-id="3eef5-109">ASP.NET Core [구성](xref:fundamentals/configuration) 시스템은 `ConnectionString`을 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="3eef5-109">The ASP.NET Core [Configuration](xref:fundamentals/configuration) system reads the `ConnectionString`.</span></span> <span data-ttu-id="3eef5-110">로컬 개발의 경우 *appsettings.json* 파일에서 연결 문자열을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="3eef5-110">For local development, it gets the connection string from the *appsettings.json* file:</span></span>

<span data-ttu-id="3eef5-111">[!code-javascript[Main](start-mvc/sample/MvcMovie/appsettings.json?highlight=2&range=8-10)]</span><span class="sxs-lookup"><span data-stu-id="3eef5-111">[!code-javascript[Main](start-mvc/sample/MvcMovie/appsettings.json?highlight=2&range=8-10)]</span></span>

<span data-ttu-id="3eef5-112">테스트 또는 프로덕션 서버에 앱을 배포할 때 환경 변수 또는 다른 방법을 사용하여 실제 SQL Server에 연결 문자열을 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3eef5-112">When you deploy the app to a test or production server, you can use an environment variable or another approach to set the connection string to a real SQL Server.</span></span> <span data-ttu-id="3eef5-113">자세한 내용은 [구성](xref:fundamentals/configuration)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="3eef5-113">See [Configuration](xref:fundamentals/configuration) for more information.</span></span>

## <a name="sql-server-express-localdb"></a><span data-ttu-id="3eef5-114">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="3eef5-114">SQL Server Express LocalDB</span></span>

<span data-ttu-id="3eef5-115">LocalDB는 프로그램 개발용으로 대상이 지정된 SQL Server Express 데이터베이스 엔진의 간단 버전입니다.</span><span class="sxs-lookup"><span data-stu-id="3eef5-115">LocalDB is a lightweight version of the SQL Server Express Database Engine that is targeted for program development.</span></span> <span data-ttu-id="3eef5-116">LocalDB는 요청 시 시작하고 사용자 모드에서 실행되므로 복잡한 구성이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="3eef5-116">LocalDB starts on demand and runs in user mode, so there is no complex configuration.</span></span> <span data-ttu-id="3eef5-117">기본적으로 LocalDB 데이터베이스는 *C:/사용자/\<user\>* 디렉터리에 "\*.mdf" 파일을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="3eef5-117">By default, LocalDB database creates "\*.mdf" files in the *C:/Users/\<user\>* directory.</span></span>

* <span data-ttu-id="3eef5-118">**보기** 메뉴에서 SSOX(**SQL Server 개체 탐색기**)를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="3eef5-118">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![보기 메뉴](working-with-sql/_static/ssox.png)

* <span data-ttu-id="3eef5-120">마우스 오른쪽 단추로 `Movie` 테이블 **> 디자이너 보기**를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="3eef5-120">Right click on the `Movie` table **> View Designer**</span></span>

  ![동영상 테이블에서 열린 바로 가기 메뉴](working-with-sql/_static/design.png)

  ![디자이너에 열린 동영상 테이블](working-with-sql/_static/dv.png)

<span data-ttu-id="3eef5-123">`ID` 옆의 키 아이콘을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="3eef5-123">Note the key icon next to `ID`.</span></span> <span data-ttu-id="3eef5-124">기본적으로 EF는 `ID`라는 속성을 기본 키로 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="3eef5-124">By default, EF will make a property named `ID` the primary key.</span></span>

* <span data-ttu-id="3eef5-125">마우스 오른쪽 단추로 `Movie` 테이블 **> 데이터 보기**를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="3eef5-125">Right click on the `Movie` table **> View Data**</span></span>

  ![동영상 테이블에서 열린 바로 가기 메뉴](working-with-sql/_static/ssox2.png)

  ![테이블 데이터를 보여 주는 열린 동영상 테이블](working-with-sql/_static/vd22.png)

## <a name="seed-the-database"></a><span data-ttu-id="3eef5-128">데이터베이스 시드</span><span class="sxs-lookup"><span data-stu-id="3eef5-128">Seed the database</span></span>

<span data-ttu-id="3eef5-129">*Models* 폴더에 `SeedData`라는 새 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="3eef5-129">Create a new class named `SeedData` in the *Models* folder.</span></span> <span data-ttu-id="3eef5-130">생성된 코드를 다음으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="3eef5-130">Replace the generated code with the following:</span></span>

<span data-ttu-id="3eef5-131">[!code-csharp[Main](start-mvc/sample/MvcMovie/Models/SeedData.cs?name=snippet_1)]</span><span class="sxs-lookup"><span data-stu-id="3eef5-131">[!code-csharp[Main](start-mvc/sample/MvcMovie/Models/SeedData.cs?name=snippet_1)]</span></span>

<span data-ttu-id="3eef5-132">DB에 동영상이 있는 경우 시드 이니셜라이저가 반환되고 동영상이 추가되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3eef5-132">If there are any movies in the DB, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>
### <a name="add-the-seed-initializer"></a><span data-ttu-id="3eef5-133">시드 이니셜라이저 추가</span><span class="sxs-lookup"><span data-stu-id="3eef5-133">Add the seed initializer</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="3eef5-134">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="3eef5-134">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="3eef5-135">*Program.cs* 파일에서 `Main` 메서드에 시드 이니셜라이저를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="3eef5-135">Add the seed initializer to the `Main` method in the *Program.cs* file:</span></span>

<span data-ttu-id="3eef5-136">[!code-csharp[Main](start-mvc/sample/MvcMovie/Program.cs?highlight=6,16-32)]</span><span class="sxs-lookup"><span data-stu-id="3eef5-136">[!code-csharp[Main](start-mvc/sample/MvcMovie/Program.cs?highlight=6,16-32)]</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="3eef5-137">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="3eef5-137">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="3eef5-138">*Startup.cs* 파일에서 `Configure` 메서드의 끝에 시드 이니셜라이저를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="3eef5-138">Add the seed initializer to the end of the `Configure` method in the *Startup.cs* file.</span></span>

<span data-ttu-id="3eef5-139">[!code-csharp[Main](start-mvc/sample/MvcMovie/Startup.cs?highlight=9&name=snippet_seed)]</span><span class="sxs-lookup"><span data-stu-id="3eef5-139">[!code-csharp[Main](start-mvc/sample/MvcMovie/Startup.cs?highlight=9&name=snippet_seed)]</span></span>

---

<span data-ttu-id="3eef5-140">앱 테스트</span><span class="sxs-lookup"><span data-stu-id="3eef5-140">Test the app</span></span>

* <span data-ttu-id="3eef5-141">DB의 모든 레코드를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="3eef5-141">Delete all the records in the DB.</span></span> <span data-ttu-id="3eef5-142">브라우저 또는 SSOX에서 삭제 링크를 사용하여 이를 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3eef5-142">You can do this with the delete links in the browser or from SSOX.</span></span>
* <span data-ttu-id="3eef5-143">시드 메서드가 실행되도록 앱에서 초기화를 적용하도록 합니다(`Startup` 클래스에서 메서드 호출).</span><span class="sxs-lookup"><span data-stu-id="3eef5-143">Force the app to initialize (call the methods in the `Startup` class) so the seed method runs.</span></span> <span data-ttu-id="3eef5-144">초기화를 적용하려면 IIS Express를 중지하고 다시 시작해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="3eef5-144">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="3eef5-145">다음 접근 방식 중 하나를 사용하여 이를 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3eef5-145">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="3eef5-146">알림 영역에서 IIS Express 시스템 트레이 아이콘을 마우스 오른쪽 단추로 클릭하고 **종료** 또는 **사이트 중지**를 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="3eef5-146">Right click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**</span></span>

    ![IIS Express 시스템 트레이 아이콘](working-with-sql/_static/iisExIcon.png)

    ![바로 가기 메뉴](working-with-sql/_static/stopIIS.png)

   * <span data-ttu-id="3eef5-149">비디버그 모드에서 VS를 실행했던 경우 F5 키를 눌러 디버그 모드에서 실행되도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="3eef5-149">If you were running VS in non-debug mode, press F5 to run in debug mode</span></span>
   * <span data-ttu-id="3eef5-150">디버그 모드에서 VS를 실행했던 경우 디버거를 중지하고 F5 키를 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="3eef5-150">If you were running VS in debug mode, stop the debugger and press F5</span></span>
   
<span data-ttu-id="3eef5-151">앱에서 시드된 데이터를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="3eef5-151">The app shows the seeded data.</span></span>

![동영상 데이터를 표시하는 Microsoft Edge에서 열린 MVC 동영상 응용 프로그램](working-with-sql/_static/m55.png)

>[!div class="step-by-step"]
<span data-ttu-id="3eef5-153">[이전](adding-model.md)
[다음](controller-methods-views.md)</span><span class="sxs-lookup"><span data-stu-id="3eef5-153">[Previous](adding-model.md)
[Next](controller-methods-views.md)</span></span>  
