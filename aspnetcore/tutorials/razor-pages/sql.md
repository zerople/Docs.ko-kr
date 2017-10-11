---
title: "SQL Server LocalDB 및 ASP.NET Core 사용"
author: rick-anderson
description: "SQL Server LocalDB 및 ASP.NET Core 사용을 설명합니다."
keywords: "ASP.NET Core, Razor 페이지, Razor, MVC, SQL, LocalDB"
ms.author: riande
manager: wpickett
ms.date: 08/07/2017
ms.topic: get-started-article
ms.technology: aspnet
ms.prod: aspnet-core
uid: tutorials/razor-pages/sql
ms.openlocfilehash: 374111dfcf92132bbcf956eafb98a75f833ce2bb
ms.sourcegitcommit: 94b7e0f95b92c98b182a93d2b3dc0287e5f97976
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/04/2017
---
# <a name="working-with-sql-server-localdb-and-aspnet-core"></a><span data-ttu-id="df50b-104">SQL Server LocalDB 및 ASP.NET Core 사용</span><span class="sxs-lookup"><span data-stu-id="df50b-104">Working with SQL Server LocalDB and ASP.NET Core</span></span>

<span data-ttu-id="df50b-105">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="df50b-105">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span> 

<span data-ttu-id="df50b-106">`MovieContext` 개체는 데이터베이스에 연결하고 데이터베이스 레코드에 `Movie` 개체를 매핑하는 작업을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="df50b-106">The `MovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="df50b-107">데이터베이스 컨텍스트는 *Startup.cs* 파일의 `ConfigureServices` 메서드에서 [종속성 주입](xref:fundamentals/dependency-injection) 컨테이너에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="df50b-107">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in the *Startup.cs* file:</span></span>

[!code-csharp[Main](razor-pages-start/sample/RazorPagesMovie/Startup.cs?name=snippet_ConfigureServices&highlight=6-7)]

<span data-ttu-id="df50b-108">ASP.NET Core [구성](xref:fundamentals/configuration) 시스템은 `ConnectionString`을 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="df50b-108">The ASP.NET Core [Configuration](xref:fundamentals/configuration) system reads the `ConnectionString`.</span></span> <span data-ttu-id="df50b-109">로컬 개발의 경우 *appsettings.json* 파일에서 연결 문자열을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="df50b-109">For local development, it gets the connection string from the *appsettings.json* file:</span></span>

[!code-json[Main](razor-pages-start/sample/RazorPagesMovie/appsettings.json?highlight=2&range=8-10)]

<span data-ttu-id="df50b-110">테스트 또는 프로덕션 서버에 앱을 배포할 때 환경 변수 또는 다른 방법을 사용하여 실제 SQL Server에 연결 문자열을 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="df50b-110">When you deploy the app to a test or production server, you can use an environment variable or another approach to set the connection string to a real SQL Server.</span></span> <span data-ttu-id="df50b-111">자세한 내용은 [구성](xref:fundamentals/configuration)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="df50b-111">See [Configuration](xref:fundamentals/configuration) for more information.</span></span>

## <a name="sql-server-express-localdb"></a><span data-ttu-id="df50b-112">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="df50b-112">SQL Server Express LocalDB</span></span>

<span data-ttu-id="df50b-113">LocalDB는 프로그램 개발용으로 대상이 지정된 SQL Server Express 데이터베이스 엔진의 간단 버전입니다.</span><span class="sxs-lookup"><span data-stu-id="df50b-113">LocalDB is a lightweight version of the SQL Server Express Database Engine that is targeted for program development.</span></span> <span data-ttu-id="df50b-114">LocalDB는 요청 시 시작하고 사용자 모드에서 실행되므로 복잡한 구성이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="df50b-114">LocalDB starts on demand and runs in user mode, so there is no complex configuration.</span></span> <span data-ttu-id="df50b-115">기본적으로 LocalDB 데이터베이스는 *C:/사용자/\<user\>* 디렉터리에 "\*.mdf" 파일을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="df50b-115">By default, LocalDB database creates "\*.mdf" files in the *C:/Users/\<user\>* directory.</span></span>

<a name="ssox"></a>
* <span data-ttu-id="df50b-116">**보기** 메뉴에서 SSOX(**SQL Server 개체 탐색기**)를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="df50b-116">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![보기 메뉴](sql/_static/ssox.png)

* <span data-ttu-id="df50b-118">`Movie` 테이블을 마우스 오른쪽 단추로 클릭하고 **디자이너 보기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="df50b-118">Right click on the `Movie` table and select **View Designer**:</span></span>

  ![동영상 테이블에서 열린 바로 가기 메뉴](sql/_static/design.png)

  ![디자이너에 열린 동영상 테이블](sql/_static/dv.png)

<span data-ttu-id="df50b-121">`ID` 옆의 키 아이콘을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="df50b-121">Note the key icon next to `ID`.</span></span> <span data-ttu-id="df50b-122">기본적으로 EF는 기본 키에 대해 `ID`라는 속성을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="df50b-122">By default, EF creates a property named `ID` for the primary key.</span></span>

* <span data-ttu-id="df50b-123">`Movie` 테이블을 마우스 오른쪽 단추로 클릭하고 **데이터 보기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="df50b-123">Right click on the `Movie` table and select **View Data**:</span></span>

  ![테이블 데이터를 보여 주는 열린 동영상 테이블](sql/_static/vd22.png)

## <a name="seed-the-database"></a><span data-ttu-id="df50b-125">데이터베이스 시드</span><span class="sxs-lookup"><span data-stu-id="df50b-125">Seed the database</span></span>

<span data-ttu-id="df50b-126">*Models* 폴더에 `SeedData`라는 새 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="df50b-126">Create a new class named `SeedData` in the *Models* folder.</span></span> <span data-ttu-id="df50b-127">생성된 코드를 다음으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="df50b-127">Replace the generated code with the following:</span></span>

[!code-csharp[Main](razor-pages-start/sample/RazorPagesMovie/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="df50b-128">DB에 동영상이 있는 경우 시드 이니셜라이저가 반환되고 동영상이 추가되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="df50b-128">If there are any movies in the DB, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```
<a name="si"></a>
### <a name="add-the-seed-initializer"></a><span data-ttu-id="df50b-129">시드 이니셜라이저 추가</span><span class="sxs-lookup"><span data-stu-id="df50b-129">Add the seed initializer</span></span>

<span data-ttu-id="df50b-130">*Program.cs* 파일에서 `Main` 메서드의 끝에 시드 이니셜라이저를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="df50b-130">Add the seed initializer to the end of the `Main` method in the *Program.cs* file:</span></span>

[!code-csharp[Main](razor-pages-start/sample/RazorPagesMovie/Program.cs?highlight=6,17-32)]

<span data-ttu-id="df50b-131">앱 테스트</span><span class="sxs-lookup"><span data-stu-id="df50b-131">Test the app</span></span>

* <span data-ttu-id="df50b-132">DB의 모든 레코드를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="df50b-132">Delete all the records in the DB.</span></span> <span data-ttu-id="df50b-133">브라우저 또는 [SSOX](xref:tutorials/razor-pages/new-field#ssox)에서 삭제 링크를 사용하여 이를 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="df50b-133">You can do this with the delete links in the browser or from [SSOX](xref:tutorials/razor-pages/new-field#ssox)</span></span>
* <span data-ttu-id="df50b-134">시드 메서드가 실행되도록 앱에서 초기화를 적용하도록 합니다(`Startup` 클래스에서 메서드 호출).</span><span class="sxs-lookup"><span data-stu-id="df50b-134">Force the app to initialize (call the methods in the `Startup` class) so the seed method runs.</span></span> <span data-ttu-id="df50b-135">초기화를 적용하려면 IIS Express를 중지하고 다시 시작해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="df50b-135">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="df50b-136">다음 접근 방식 중 하나를 사용하여 이를 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="df50b-136">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="df50b-137">알림 영역에서 IIS Express 시스템 트레이 아이콘을 마우스 오른쪽 단추로 클릭하고 **종료** 또는 **사이트 중지**를 탭합니다.</span><span class="sxs-lookup"><span data-stu-id="df50b-137">Right click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**:</span></span>

    ![IIS Express 시스템 트레이 아이콘](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![바로 가기 메뉴](sql/_static/stopIIS.png)

   * <span data-ttu-id="df50b-140">비디버그 모드에서 VS를 실행했던 경우 F5 키를 눌러 디버그 모드에서 실행되도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="df50b-140">If you were running VS in non-debug mode, press F5 to run in debug mode.</span></span>
   * <span data-ttu-id="df50b-141">디버그 모드에서 VS를 실행했던 경우 디버거를 중지하고 F5 키를 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="df50b-141">If you were running VS in debug mode, stop the debugger and press F5.</span></span>
   
<span data-ttu-id="df50b-142">앱에서 시드된 데이터를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="df50b-142">The app shows the seeded data:</span></span>

![동영상 데이터를 표시하는 크롬에서 열린 동영상 응용 프로그램](sql/_static/m55.png)

<span data-ttu-id="df50b-144">다음 자습서는 데이터의 표현을 정리합니다.</span><span class="sxs-lookup"><span data-stu-id="df50b-144">The next tutorial will clean up the presentation of the data.</span></span>

>[!div class="step-by-step"]
<span data-ttu-id="df50b-145">[이전: 스캐폴드된 Razor 페이지](xref:tutorials/razor-pages/page)
[다음: 페이지 업데이트](xref:tutorials/razor-pages/da1)</span><span class="sxs-lookup"><span data-stu-id="df50b-145">[Previous: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)
[Next: Updating the pages](xref:tutorials/razor-pages/da1)</span></span>
