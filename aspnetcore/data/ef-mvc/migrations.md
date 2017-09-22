---
title: "ASP.NET Core MVC EF 코어 10-마이그레이션-4"
author: tdykstra
description: "이 자습서에서는 ASP.NET Core MVC 응용 프로그램에서 데이터 모델 변경 내용을 관리 하기 위한 EF 코어 마이그레이션 기능을 사용 하 여 시작 합니다."
keywords: "ASP.NET Core, Entity Framework Core, 마이그레이션"
ms.author: tdykstra
manager: wpickett
ms.date: 03/15/2017
ms.topic: get-started-article
ms.assetid: 81f6c9c2-a819-4f3a-97a4-4b0503b56c26
ms.technology: aspnet
ms.prod: asp.net-core
uid: data/ef-mvc/migrations
ms.openlocfilehash: 638bef0cda14f53a326c66c6a5da3f3c1bb762c6
ms.sourcegitcommit: 78d28178345a0eea91556e4cd1adad98b1446db8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2017
---
# <a name="migrations---ef-core-with-aspnet-core-mvc-tutorial-4-of-10"></a><span data-ttu-id="96441-104">마이그레이션-EF 코어 ASP.NET Core MVC 자습서 (4 / 10)</span><span class="sxs-lookup"><span data-stu-id="96441-104">Migrations - EF Core with ASP.NET Core MVC tutorial (4 of 10)</span></span>

<span data-ttu-id="96441-105">여 [Tom Dykstra](https://github.com/tdykstra) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="96441-105">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="96441-106">Contoso 대학 샘플 웹 응용 프로그램에는 Entity Framework Core 및 Visual Studio를 사용 하 여 ASP.NET Core MVC 웹 응용 프로그램을 만드는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="96441-106">The Contoso University sample web application demonstrates how to create ASP.NET Core MVC web applications using Entity Framework Core and Visual Studio.</span></span> <span data-ttu-id="96441-107">자습서 시리즈에 대 한 정보를 참조 하십시오. [시리즈의 첫 번째 자습서](intro.md)합니다.</span><span class="sxs-lookup"><span data-stu-id="96441-107">For information about the tutorial series, see [the first tutorial in the series](intro.md).</span></span>

<span data-ttu-id="96441-108">이 자습서에서는 데이터 모델 변경 내용을 관리 하기 위한 EF 코어 마이그레이션 기능을 사용 하 여 시작 합니다.</span><span class="sxs-lookup"><span data-stu-id="96441-108">In this tutorial, you start using the EF Core migrations feature for managing data model changes.</span></span> <span data-ttu-id="96441-109">이후의 자습서에서는 데이터 모델을 변경 하면 더 많은 마이그레이션을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="96441-109">In later tutorials, you'll add more migrations as you change the data model.</span></span>

## <a name="introduction-to-migrations"></a><span data-ttu-id="96441-110">로 마이그레이션에도 소개</span><span class="sxs-lookup"><span data-stu-id="96441-110">Introduction to migrations</span></span>

<span data-ttu-id="96441-111">새 응용 프로그램을 개발 하는 경우 데이터 모델에 자주 및 변경 될 때마다 모델 변경, 데이터베이스와 동기화를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="96441-111">When you develop a new application, your data model changes frequently, and each time the model changes, it gets out of sync with the database.</span></span> <span data-ttu-id="96441-112">존재 하지 않는 경우 데이터베이스를 만들려면를 Entity Framework를 구성 하 여이 자습서를 시작 합니다.</span><span class="sxs-lookup"><span data-stu-id="96441-112">You started these tutorials by configuring the Entity Framework to create the database if it doesn't exist.</span></span> <span data-ttu-id="96441-113">그런 다음 데이터 모델을 변경 등의 추가, 제거 또는 엔터티 클래스를 변경 또는 DbContext 클래스-변경 될 때마다 데이터베이스를 삭제할 수 고 EF 모델을 일치 시키고 테스트 데이터로 초기값을 설정 하는 새 브러시를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="96441-113">Then each time you change the data model -- add, remove, or change entity classes or change your DbContext class -- you can delete the database and EF creates a new one that matches the model, and seeds it with test data.</span></span>

<span data-ttu-id="96441-114">이 방법은 데이터베이스를 데이터 모델을 통해 동기화 유지 프로덕션 환경에 응용 프로그램을 배포할 때까지 잘 작동 합니다.</span><span class="sxs-lookup"><span data-stu-id="96441-114">This method of keeping the database in sync with the data model works well until you deploy the application to production.</span></span> <span data-ttu-id="96441-115">응용 프로그램에서 데이터를 보관 하 고 모든 될 때마다 손실 하지 않으려면를 일반적으로 저장 하는 프로덕션에서 실행 중인 경우에 새 열을 추가 하는 등 변경을 합니다.</span><span class="sxs-lookup"><span data-stu-id="96441-115">When the application is running in production it is usually storing data that you want to keep, and you don't want to lose everything each time you make a change such as adding a new column.</span></span> <span data-ttu-id="96441-116">EF 코어 마이그레이션 기능 새 데이터베이스를 만드는 대신 데이터베이스 스키마를 업데이트 하는 EF를 사용 하 여이 문제를 해결 합니다.</span><span class="sxs-lookup"><span data-stu-id="96441-116">The EF Core Migrations feature solves this problem by enabling EF to update the database schema instead of creating  a new database.</span></span>

## <a name="entity-framework-core-nuget-packages-for-migrations"></a><span data-ttu-id="96441-117">마이그레이션을 위한 Entity Framework Core NuGet 패키지</span><span class="sxs-lookup"><span data-stu-id="96441-117">Entity Framework Core NuGet packages for migrations</span></span>

<span data-ttu-id="96441-118">마이그레이션을 사용 하려면 사용할 수 있습니다는 **패키지 관리자 콘솔** (PMC) 또는 명령줄 인터페이스 (CLI).</span><span class="sxs-lookup"><span data-stu-id="96441-118">To work with migrations, you can use the **Package Manager Console** (PMC) or the command-line interface (CLI).</span></span>  <span data-ttu-id="96441-119">이 자습서에는 CLI 명령을 사용 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="96441-119">These tutorials show how to use CLI commands.</span></span> <span data-ttu-id="96441-120">PMC에 대 한 정보는 [이 자습서의 최종](#pmc)합니다.</span><span class="sxs-lookup"><span data-stu-id="96441-120">Information about the PMC is at [the end of this tutorial](#pmc).</span></span>

<span data-ttu-id="96441-121">CLI(명령줄 인터페이스)용 EF 도구는 [Microsoft.EntityFrameworkCore.Tools.DotNet](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools.DotNet)에서 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="96441-121">The EF tools for the command-line interface (CLI) are provided in [Microsoft.EntityFrameworkCore.Tools.DotNet](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools.DotNet).</span></span> <span data-ttu-id="96441-122">이 패키지를 설치 하려면 추가 하는 `DotNetCliToolReference` 컬렉션에는 *.csproj* 표시 된 것 처럼 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="96441-122">To install this package, add it to the `DotNetCliToolReference` collection in the *.csproj* file, as shown.</span></span> <span data-ttu-id="96441-123">**참고:** *.csproj* 파일을 편집하여 이 패키지를 설치해야 합니다. `install-package` 명령이나 패키지 관리자 GUI를 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="96441-123">**Note:** You have to install this package by editing the *.csproj* file; you can't use the `install-package` command or the package manager GUI.</span></span> <span data-ttu-id="96441-124">편집할 수는 *.csproj* 파일에서 프로젝트 이름을 마우스 오른쪽 단추로 클릭 하 여 **솔루션 탐색기** 선택 하 고 **편집 ContosoUniversity.csproj**합니다.</span><span class="sxs-lookup"><span data-stu-id="96441-124">You can edit the *.csproj* file by right-clicking the project name in **Solution Explorer** and selecting **Edit ContosoUniversity.csproj**.</span></span>

[!code-xml[](intro/samples/cu/ContosoUniversity.csproj?range=12-15&highlight=2)]
  
<span data-ttu-id="96441-125">(이 예제의 버전 번호를 자습서 기록 될 때 현재 했습니다.)</span><span class="sxs-lookup"><span data-stu-id="96441-125">(The version numbers in this example were current when the tutorial was written.)</span></span> 

## <a name="change-the-connection-string"></a><span data-ttu-id="96441-126">연결 문자열 변경</span><span class="sxs-lookup"><span data-stu-id="96441-126">Change the connection string</span></span>

<span data-ttu-id="96441-127">에 *appsettings.json* 파일에서 ContosoUniversity2 또는 다른 이름으로 사용 중인 컴퓨터에서 사용 하지 않은 연결 문자열에 데이터베이스의 이름을 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="96441-127">In the *appsettings.json* file, change the name of the database in the connection string to ContosoUniversity2 or some other name that you haven't used on the computer you're using.</span></span>

[!code-json[Main](intro/samples/cu/appsettings2.json?range=1-4)]

<span data-ttu-id="96441-128">이러한 변경 하 여 첫 번째 마이그레이션 새 데이터베이스를 만드는 프로젝트를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="96441-128">This change sets up the project so that the first migration will create a new database.</span></span> <span data-ttu-id="96441-129">마이그레이션 시작 하기 위한 필요 하지 않습니다 되지만 것이 좋습니다 나중에 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="96441-129">This isn't required for getting started with migrations, but you'll see later why it's a good idea.</span></span>

> [!NOTE]
> <span data-ttu-id="96441-130">데이터베이스 이름을 변경 하는 대신, 데이터베이스를 삭제할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="96441-130">As an alternative to changing the database name, you can delete the database.</span></span> <span data-ttu-id="96441-131">사용 하 여 **SQL Server 개체 탐색기** (SSOX) 또는 `database drop` CLI 명령을:</span><span class="sxs-lookup"><span data-stu-id="96441-131">Use **SQL Server Object Explorer** (SSOX) or the `database drop` CLI command:</span></span>
> ```console
> dotnet ef database drop
> ```
> <span data-ttu-id="96441-132">다음 섹션에는 CLI 명령을 실행 하는 방법을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="96441-132">The following section explains how to run CLI commands.</span></span>

## <a name="create-an-initial-migration"></a><span data-ttu-id="96441-133">초기 마이그레이션 만들기</span><span class="sxs-lookup"><span data-stu-id="96441-133">Create an initial migration</span></span>

<span data-ttu-id="96441-134">변경 내용을 저장 하 고 프로젝트를 빌드하십시오.</span><span class="sxs-lookup"><span data-stu-id="96441-134">Save your changes and build the project.</span></span> <span data-ttu-id="96441-135">명령 창을 열고 프로젝트 폴더로 이동 합니다.</span><span class="sxs-lookup"><span data-stu-id="96441-135">Then open a command window and navigate to the project folder.</span></span> <span data-ttu-id="96441-136">작업을 수행 하는 빠른 방법을 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="96441-136">Here's a quick way to do that:</span></span>

* <span data-ttu-id="96441-137">**솔루션 탐색기**프로젝트를 마우스 오른쪽 단추로 클릭 하 고 선택 **파일 탐색기에서 열기** 상황에 맞는 메뉴입니다.</span><span class="sxs-lookup"><span data-stu-id="96441-137">In **Solution Explorer**, right-click the project and choose **Open in File Explorer** from the context menu.</span></span>

  ![파일 탐색기 메뉴 항목에서 열기](migrations/_static/open-in-file-explorer.png)

* <span data-ttu-id="96441-139">주소 표시줄에 "cmd"를 입력 하 고 Enter 키를 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="96441-139">Enter "cmd" in the address bar and press Enter.</span></span>

  ![명령 창 열기](migrations/_static/open-command-window.png)

<span data-ttu-id="96441-141">명령 창에서 다음 명령을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="96441-141">Enter the following command in the command window:</span></span>

```console
dotnet ef migrations add InitialCreate
```

<span data-ttu-id="96441-142">명령 창에 다음과 같은 출력이 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="96441-142">You see output like the following in the command window:</span></span>

```console
info: Microsoft.AspNetCore.DataProtection.KeyManagement.XmlKeyManager[0]
      User profile is available. Using 'C:\Users\username\AppData\Local\ASP.NET\DataProtection-Keys' as key repository and Windows DPAPI to encrypt keys at rest.
info: Microsoft.EntityFrameworkCore.Infrastructure[100403]
      Entity Framework Core 2.0.0-rtm-26452 initialized 'SchoolContext' using provider 'Microsoft.EntityFrameworkCore.SqlServer' with options: None
Done. To undo this action, use 'ef migrations remove'
```

> [!NOTE]
> <span data-ttu-id="96441-143">오류 메시지가 표시 되 면 *없는 실행 파일을 일치 하는 명령 "dotnet-ef" 찾을*, 참조 [이 블로그 게시물](http://thedatafarm.com/data-access/no-executable-found-matching-command-dotnet-ef/) 문제 해결 도움말에 대 한 합니다.</span><span class="sxs-lookup"><span data-stu-id="96441-143">If you see an error message *No executable found matching command "dotnet-ef"*, see [this blog post](http://thedatafarm.com/data-access/no-executable-found-matching-command-dotnet-ef/) for help troubleshooting.</span></span>

<span data-ttu-id="96441-144">오류 메시지가 표시 되 면 "*... 파일에 액세스할 수 없습니다 ContosoUniversity.dll 다른 프로세스에서 사용 되 고 있으므로 합니다. *"Windows 시스템 트레이에서 찾기 IIS Express 아이콘을 마우스 오른쪽 단추로 클릭 차례로 클릭 **ContosoUniversity > 중지 사이트**합니다.</span><span class="sxs-lookup"><span data-stu-id="96441-144">If you see an error message "*cannot access the file ... ContosoUniversity.dll because it is being used by another process.*", find the IIS Express icon in the Windows System Tray, and right-click it, then click **ContosoUniversity > Stop Site**.</span></span>

## <a name="examine-the-up-and-down-methods"></a><span data-ttu-id="96441-145">위쪽을 검토 하 고 아래쪽 메서드</span><span class="sxs-lookup"><span data-stu-id="96441-145">Examine the Up and Down methods</span></span>

<span data-ttu-id="96441-146">실행 하는 시기는 `migrations add` 명령, EF에서 생성 한 코드를 처음부터 데이터베이스 만들기.</span><span class="sxs-lookup"><span data-stu-id="96441-146">When you executed the `migrations add` command, EF generated the code that will create the database from scratch.</span></span> <span data-ttu-id="96441-147">이 코드는는 *마이그레이션* 라는 파일에 폴더 * \<타임 스탬프 > _InitialCreate.cs*합니다.</span><span class="sxs-lookup"><span data-stu-id="96441-147">This code is in the *Migrations* folder, in the file named *\<timestamp>_InitialCreate.cs*.</span></span> <span data-ttu-id="96441-148">`Up` 의 메서드는 `InitialCreate` 클래스는 데이터 모델 엔터티 집합에 해당 하는 데이터베이스 테이블을 만듭니다 및 `Down` 다음 예제와 같이, 메서드 삭제 합니다.</span><span class="sxs-lookup"><span data-stu-id="96441-148">The `Up` method of the `InitialCreate` class creates the database tables that correspond to the data model entity sets, and the `Down` method deletes them, as shown in the following example.</span></span>

[!code-csharp[Main](intro/samples/cu/Migrations/20170215220724_InitialCreate.cs?range=92-118)]

<span data-ttu-id="96441-149">마이그레이션 호출은 `Up` 마이그레이션에 대 한 데이터 모델 변경 내용을 구현 하려면 메서드.</span><span class="sxs-lookup"><span data-stu-id="96441-149">Migrations calls the `Up` method to implement the data model changes for a migration.</span></span> <span data-ttu-id="96441-150">Update, 마이그레이션 호출을 롤백해야 하는 명령을 입력할 때는 `Down` 메서드.</span><span class="sxs-lookup"><span data-stu-id="96441-150">When you enter a command to roll back the update, Migrations calls the `Down` method.</span></span>

<span data-ttu-id="96441-151">이 코드는 입력 했을 때 만들어진 초기 마이그레이션에 대 한는 `migrations add InitialCreate` 명령입니다.</span><span class="sxs-lookup"><span data-stu-id="96441-151">This code is for the initial migration that was created when you entered the `migrations add InitialCreate` command.</span></span> <span data-ttu-id="96441-152">마이그레이션 name 매개 변수 (이 예제에서 "InitialCreate") 파일 이름에 대해 사용 되 고 원하는 작업이 무엇이 든 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="96441-152">The migration name parameter ("InitialCreate" in the example) is used for the file name and can be whatever you want.</span></span> <span data-ttu-id="96441-153">단어 또는 구를 마이그레이션에서 수행 되는 것을 요약 하는 선택 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="96441-153">It's best to choose a word or phrase that summarizes what is being done in the migration.</span></span> <span data-ttu-id="96441-154">예를 들어 나중에 마이그레이션할 "AddDepartmentTable" 이름을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="96441-154">For example, you might name a later migration "AddDepartmentTable".</span></span>

<span data-ttu-id="96441-155">데이터베이스가 이미 존재 하는 경우 초기 마이그레이션을 만든 경우 데이터베이스 만들기 코드 생성 되지만 데이터베이스는 이미 데이터 모델 일치 하기 때문에 실행할 필요는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="96441-155">If you created the initial migration when the database already exists, the database creation code is generated but it doesn't have to run because the database already matches the data model.</span></span> <span data-ttu-id="96441-156">여기서 데이터베이스가 아직 없는, 데이터베이스를 만들려면이 코드가 실행 되는 다른 환경에 앱을 배포할 때 하므로 이기 먼저 테스트 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="96441-156">When you deploy the app to another environment where the database doesn't exist yet, this code will run to create your database, so it's a good idea to test it first.</span></span> <span data-ttu-id="96441-157">바로 이러한 이유로 마이그레이션을 처음부터 다시 만들 수 있도록 이전-연결 문자열에 데이터베이스의 이름을 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="96441-157">That's why you changed the name of the database in the connection string earlier -- so that migrations can create a new one from scratch.</span></span>

## <a name="examine-the-data-model-snapshot"></a><span data-ttu-id="96441-158">데이터 모델 스냅숏을 검사합니다</span><span class="sxs-lookup"><span data-stu-id="96441-158">Examine the data model snapshot</span></span>

<span data-ttu-id="96441-159">마이그레이션도 만듭니다.는 *스냅숏* 에 현재 데이터베이스 스키마의 *Migrations/SchoolContextModelSnapshot.cs*합니다.</span><span class="sxs-lookup"><span data-stu-id="96441-159">Migrations also creates a *snapshot* of the current database schema in *Migrations/SchoolContextModelSnapshot.cs*.</span></span> <span data-ttu-id="96441-160">해당 코드의 모양을 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="96441-160">Here's what that code looks like:</span></span>

[!code-csharp[Main](intro/samples/cu/Migrations/SchoolContextModelSnapshot1.cs?name=snippet_Truncate)]

<span data-ttu-id="96441-161">현재 데이터베이스 스키마, 코드에 표현 되므로 EF 코어 마이그레이션 만들려는 데이터베이스와 상호 작용할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="96441-161">Because the current database schema is represented in code, EF Core doesn't have to interact with the database to create migrations.</span></span> <span data-ttu-id="96441-162">마이그레이션에 추가 하면 EF 스냅숏 파일에 데이터 모델을 비교 하 여 변경 내용을 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="96441-162">When you add a migration, EF determines what changed by comparing the data model to the snapshot file.</span></span> <span data-ttu-id="96441-163">EF는 데이터베이스를 업데이트 해야 하는 경우에 데이터베이스와 상호 작용 합니다.</span><span class="sxs-lookup"><span data-stu-id="96441-163">EF interacts with the database only when it has to update the database.</span></span> 

<span data-ttu-id="96441-164">명명 된 파일을 삭제 하 여 마이그레이션의 제거할 수 없습니다 만들 하는 마이그레이션 동기화 되어야 하는 스냅숏 파일에 * \<타임 스탬프 > _\<migrationname >.cs*합니다.</span><span class="sxs-lookup"><span data-stu-id="96441-164">The snapshot file has to be kept in sync with the migrations that create it, so you can't remove a migration just by deleting the file named  *\<timestamp>_\<migrationname>.cs*.</span></span> <span data-ttu-id="96441-165">해당 파일을 삭제 하면 나머지 마이그레이션은 데이터베이스 스냅숏 파일와 동기화 됩니다.</span><span class="sxs-lookup"><span data-stu-id="96441-165">If you delete that file, the remaining migrations will be out of sync with the database snapshot file.</span></span> <span data-ttu-id="96441-166">사용자가 추가한 마지막 마이그레이션을 삭제 하려면 사용 하 여는 [dotnet ef 마이그레이션 제거](https://docs.microsoft.com/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove) 명령입니다.</span><span class="sxs-lookup"><span data-stu-id="96441-166">To delete the last migration that you added, use the [dotnet ef migrations remove](https://docs.microsoft.com/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove) command.</span></span>

## <a name="apply-the-migration-to-the-database"></a><span data-ttu-id="96441-167">마이그레이션 데이터베이스에 적용</span><span class="sxs-lookup"><span data-stu-id="96441-167">Apply the migration to the database</span></span>

<span data-ttu-id="96441-168">명령 창에서에 데이터베이스 및 테이블을 만들려면 다음 명령을 입력 합니다.</span><span class="sxs-lookup"><span data-stu-id="96441-168">In the command window, enter the following command to create the database and tables in it.</span></span>

```console
dotnet ef database update
```

<span data-ttu-id="96441-169">명령의 출력은는 `migrations add` 명령을 제외 하 고 데이터베이스를 설정 하는 SQL 명령에 대 한 로그를 참조 하십시오.</span><span class="sxs-lookup"><span data-stu-id="96441-169">The output from the command is similar to the `migrations add` command, except that you see logs for the SQL commands that set up the database.</span></span> <span data-ttu-id="96441-170">대부분의 로그는 다음 예제 출력에서 생략 됩니다.</span><span class="sxs-lookup"><span data-stu-id="96441-170">Most of the logs are omitted in the following sample output.</span></span> <span data-ttu-id="96441-171">이 수준의 로그 메시지에 대 한 세부 정보를 표시 하지 않으려는 경우의 로그 수준을 변경할 수 있습니다는 *appsettings 합니다. Development.json* 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="96441-171">If you prefer not to see this level of detail in log messages, you can change the log levels in the *appsettings.Development.json* file.</span></span> <span data-ttu-id="96441-172">자세한 내용은 참조 [로깅 소개](xref:fundamentals/logging)합니다.</span><span class="sxs-lookup"><span data-stu-id="96441-172">For more information, see [Introduction to logging](xref:fundamentals/logging).</span></span>

```text
info: Microsoft.AspNetCore.DataProtection.KeyManagement.XmlKeyManager[0]
      User profile is available. Using 'C:\Users\username\AppData\Local\ASP.NET\DataProtection-Keys' as key repository and Windows DPAPI to encrypt keys at rest.
info: Microsoft.EntityFrameworkCore.Infrastructure[100403]
      Entity Framework Core 2.0.0-rtm-26452 initialized 'SchoolContext' using provider 'Microsoft.EntityFrameworkCore.SqlServer' with options: None
info: Microsoft.EntityFrameworkCore.Database.Command[200101]
      Executed DbCommand (467ms) [Parameters=[], CommandType='Text', CommandTimeout='60']
      CREATE DATABASE [ContosoUniversity2];
info: Microsoft.EntityFrameworkCore.Database.Command[200101]
      Executed DbCommand (20ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      CREATE TABLE [__EFMigrationsHistory] (
          [MigrationId] nvarchar(150) NOT NULL,
          [ProductVersion] nvarchar(32) NOT NULL,
          CONSTRAINT [PK___EFMigrationsHistory] PRIMARY KEY ([MigrationId])
      );

<logs omitted for brevity>

info: Microsoft.EntityFrameworkCore.Database.Command[200101]
      Executed DbCommand (3ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      INSERT INTO [__EFMigrationsHistory] ([MigrationId], [ProductVersion])
      VALUES (N'20170816151242_InitialCreate', N'2.0.0-rtm-26452');
Done.
```

<span data-ttu-id="96441-173">사용 하 여 **SQL Server 개체 탐색기** 첫 번째 자습서에서와 같이 데이터베이스를 검사 합니다.</span><span class="sxs-lookup"><span data-stu-id="96441-173">Use **SQL Server Object Explorer** to inspect the database as you did in the first tutorial.</span></span>  <span data-ttu-id="96441-174">추적 하는 데이터베이스에 적용 된 마이그레이션이 어떤 __EFMigrationsHistory 테이블의 추가 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="96441-174">You'll notice the addition of an __EFMigrationsHistory table that keeps track of which migrations have been applied to the database.</span></span> <span data-ttu-id="96441-175">해당 테이블에서 데이터를 확인 하 고 첫 번째 마이그레이션에 한 행씩 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="96441-175">View the data in that table and you'll see one row for the first migration.</span></span> <span data-ttu-id="96441-176">(마지막 로그 CLI 출력 앞의 예제에서이 행을 만들고 INSERT 문을 보여 줍니다.)</span><span class="sxs-lookup"><span data-stu-id="96441-176">(The last log in the preceding CLI output example shows the INSERT statement that creates this row.)</span></span>

<span data-ttu-id="96441-177">모든 항목이 여전히 제대로 작동 하는지 확인 이전과 동일 응용 프로그램을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="96441-177">Run the application to verify that everything still works the same as before.</span></span>

![학생 인덱스 페이지](migrations/_static/students-index.png)

<a id="pmc"></a>
## <a name="command-line-interface-cli-vs-package-manager-console-pmc"></a><span data-ttu-id="96441-179">CLI (명령줄 인터페이스) vs입니다. 패키지 관리자 콘솔 (PMC)</span><span class="sxs-lookup"><span data-stu-id="96441-179">Command-line interface (CLI) vs. Package Manager Console (PMC)</span></span>

<span data-ttu-id="96441-180">Visual Studio에서 PowerShell cmdlet 또는.NET Core CLI 명령에서 사용할 수 없으면 마이그레이션 관리 EF tooling **패키지 관리자 콘솔** (PMC) 창.</span><span class="sxs-lookup"><span data-stu-id="96441-180">The EF tooling for managing migrations is available from .NET Core CLI commands or from PowerShell cmdlets in the Visual Studio **Package Manager Console** (PMC) window.</span></span> <span data-ttu-id="96441-181">이 자습서에서는 CLI를 사용 하는 방법을 보여 줍니다. 하지만 원하는 경우에 PMC를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="96441-181">This tutorial shows how to use the CLI, but you can use the PMC if you prefer.</span></span>

<span data-ttu-id="96441-182">PMC 명령에 대 한 EF 명령은 [Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools) 패키지 합니다.</span><span class="sxs-lookup"><span data-stu-id="96441-182">The EF commands for the PMC commands are in the [Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools) package.</span></span> <span data-ttu-id="96441-183">이 패키지에 이미 포함 되어는 [Microsoft.AspNetCore.All](xref:fundamentals/metapackage) metapackage, 하므로 설치할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="96441-183">This package is already included in the [Microsoft.AspNetCore.All](xref:fundamentals/metapackage) metapackage, so you don't have to install it.</span></span>

<span data-ttu-id="96441-184">**중요:** 이 동일한 패키지를 편집 하 여 CLI를 설치 하는 것과는 *.csproj* 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="96441-184">**Important:** This is not the same package as the one you install for the CLI by editing the *.csproj* file.</span></span> <span data-ttu-id="96441-185">이 개체의 이름이 끝나는 `Tools`, CLI 패키지 이름에는 달리 `Tools.DotNet`합니다.</span><span class="sxs-lookup"><span data-stu-id="96441-185">The name of this one ends in `Tools`, unlike the CLI package name which ends in `Tools.DotNet`.</span></span>

<span data-ttu-id="96441-186">CLI 명령에 대 한 자세한 내용은 참조 [.NET Core CLI](https://docs.microsoft.com/ef/core/miscellaneous/cli/dotnet)합니다.</span><span class="sxs-lookup"><span data-stu-id="96441-186">For more information about the CLI commands, see [.NET Core CLI](https://docs.microsoft.com/ef/core/miscellaneous/cli/dotnet).</span></span> 

<span data-ttu-id="96441-187">PMC 명령에 대 한 자세한 내용은 참조 [패키지 관리자 콘솔 (Visual Studio)](https://docs.microsoft.com/ef/core/miscellaneous/cli/powershell)합니다.</span><span class="sxs-lookup"><span data-stu-id="96441-187">For more information about the PMC commands, see [Package Manager Console (Visual Studio)](https://docs.microsoft.com/ef/core/miscellaneous/cli/powershell).</span></span>

## <a name="summary"></a><span data-ttu-id="96441-188">요약</span><span class="sxs-lookup"><span data-stu-id="96441-188">Summary</span></span>

<span data-ttu-id="96441-189">이 자습서에서 만든 첫 번째 마이그레이션을 적용 하는 방법을 살펴보았습니다.</span><span class="sxs-lookup"><span data-stu-id="96441-189">In this tutorial, you've seen how to create and apply your first migration.</span></span> <span data-ttu-id="96441-190">다음 자습서에서는 데이터 모델을 확장 하 여 더 많은 고급 항목을 살펴보고 먼저 하겠습니다.</span><span class="sxs-lookup"><span data-stu-id="96441-190">In the next tutorial, you'll begin looking at more advanced topics by expanding the data model.</span></span> <span data-ttu-id="96441-191">방식에 따라 만들 하 고 추가 마이그레이션을 적용 합니다.</span><span class="sxs-lookup"><span data-stu-id="96441-191">Along the way you'll create and apply additional migrations.</span></span>

>[!div class="step-by-step"]
<span data-ttu-id="96441-192">[이전](sort-filter-page.md)
[다음](complex-data-model.md)</span><span class="sxs-lookup"><span data-stu-id="96441-192">[Previous](sort-filter-page.md)
[Next](complex-data-model.md)</span></span>  
