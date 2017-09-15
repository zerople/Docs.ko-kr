---
title: "ASP.NET Core MVC 앱에 모델 추가"
author: rick-anderson
description: "간단한 ASP.NET Core 앱에 모델을 추가합니다."
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 03/30/2017
ms.topic: get-started-article
ms.assetid: 8dc28498-00ee-4d66-b903-b593059e9f39
ms.technology: aspnet
ms.prod: asp.net-core
uid: tutorials/first-mvc-app/adding-model
ms.openlocfilehash: 8d0bebc22e1cfdc6d9b213d0c3159a7dab988020
ms.sourcegitcommit: 0bd3f6ec577c648dd777877e97572ec2da1b36c4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2017
---
[!INCLUDE[adding-model](../../includes/mvc-intro/adding-model1.md)]

참고: ASP.NET Core 2.0 템플릿은 *Models* 폴더를 포함합니다.

솔루션 탐색기에서 **MvcMovie** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 폴더**를 선택합니다. 폴더 이름을 *Models*로 지정합니다.

*Models* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **클래스**를 선택합니다. 클래스 이름을 **Movie**로 지정하고 다음 속성을 추가합니다.

[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Models/MovieNoEF.cs?name=snippet_1)]

`ID` 필드는 데이터베이스에서 기본 키 대신 필요합니다. 

프로젝트를 빌드하여 오류가 없는지 확인합니다. 이제 **M**VC 앱에 **모**델이 있습니다.

## <a name="scaffolding-a-controller"></a>컨트롤러 스캐폴딩

**솔루션 탐색기**에서 *Controllers* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가 > 컨트롤러**를 선택합니다.

![위의 단계 보기](adding-model/_static/add_controller.png)

**MVC 종속성 추가** 대화 상자에서 **최소 종속성**을 선택하고 **추가**를 선택합니다.

![위의 단계 보기](adding-model/_static/add_depend.png)

Visual Studio가 컨트롤러를 스캐폴드하는 데 필요한 종속성을 추가하지만 컨트롤러 자체는 생성되지 않습니다. 다음 **> 추가 > 컨트롤러** 호출에서 컨트롤러를 만듭니다. 

**솔루션 탐색기**에서 *Controllers* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가 > 컨트롤러**를 선택합니다.

![위의 단계 보기](adding-model/_static/add_controller.png)

**스캐폴드 추가** 대화 상자에서 **보기 포함 MVC 컨트롤러, Entity Framework > 추가 사용**을 누릅니다.

![스캐폴드 추가 대화 상자](adding-model/_static/add_scaffold2.png)

**컨트롤러 추가** 대화 상자를 입력합니다.

* **모델 클래스:** *Movie (MvcMovie.Models)*
* **데이터 컨텍스트 클래스**: **+** 아이콘을 선택하고 기본값 **MvcMovie.Models.MvcMovieContext**를 추가합니다.

![데이터 컨텍스트 추가](adding-model/_static/dc.png)

* **보기:** 각 옵션의 기본값 선택 유지
* **컨트롤러 이름:** 기본값 *MoviesController* 유지
* **추가** 누르기

![컨트롤러 추가 대화 상자](adding-model/_static/add_controller2.png)

Visual Studio에서 다음을 만듭니다.

* Entity Framework Core [데이터베이스 컨텍스트 클래스](xref:data/ef-mvc/intro#create-the-database-context)(*Data/MvcMovieContext.cs*)
* 동영상 컨트롤러(*Controllers/MoviesController.cs*)
* 만들기, 삭제, 세부 정보, 편집 및 인덱스 페이지에 대한 Razor 뷰 파일(*Views/Movies/&ast;.cshtml*)

[CRUD](https://en.wikipedia.org/wiki/Create,_read,_update_and_delete)(만들기, 읽기, 업데이트 및 삭제) 작업 메서드와 뷰 및 데이터베이스 컨텍스트의 자동 생성을 *스캐폴딩*이라고 합니다. 동영상 데이터베이스를 관리할 수 있는 완벽하게 작동하는 웹 응용 프로그램이 곧 제공됩니다.

응용 프로그램을 실행하고 **Mvc Movie** 링크를 클릭하면 다음과 유사한 오류가 표시됩니다.

```
An unhandled exception occurred while processing the request.

SqlException: Cannot open database "MvcMovieContext-<GUID removed>" requested by the login. The login failed.
Login failed for user 'Rick'.

System.Data.SqlClient.SqlInternalConnectionTds..ctor(DbConnectionPoolIdentity identity, SqlConnectionString 
```

데이터베이스를 만들어야 하며 여기에 EF Core [마이그레이션](xref:data/ef-mvc/migrations) 기능을 사용할 것입니다. 마이그레이션을 통해 데이터 모델과 일치하는 데이터베이스를 만들고 데이터 모델 변경 시 데이터베이스 스키마를 업데이트할 수 있습니다.

## <a name="add-ef-tooling-and-perform-initial-migration"></a>EF 도구 추가 및 초기 마이그레이션 수행

이 섹션에서는 PMC(패키지 관리자 콘솔)를 사용하여 다음을 수행합니다.

* Entity Framework Core 도구 패키지 추가 이 패키지는 마이그레이션을 추가하고 데이터베이스를 업데이트하는 데 필요합니다.
* 초기 마이그레이션을 추가합니다.
* 초기 마이그레이션을 사용하여 데이터베이스를 업데이트합니다.

**도구** 메뉴에서 **NuGet 패키지 관리자 > 패키지 관리자 콘솔**을 선택합니다.

<!-- following image shared with uid: tutorials/razor-pages/model -->
  ![PMC 메뉴](adding-model/_static/pmc.png)

PMC에서 다음 명령을 입력합니다.

``` PMC
Install-Package Microsoft.EntityFrameworkCore.Tools
Add-Migration Initial
Update-Database
```

참고: PMC 문제가 있는 경우 [CLI 방식](#cli)을 참조하세요.

`Add-Migration` 명령은 초기 데이터베이스 스키마를 만드는 코드를 생성합니다. 스키마는 `DbContext`에 지정된 모델을 기반으로 합니다(*Data/MvcMovieContext.cs* 파일). `Initial` 인수는 마이그레이션 이름을 지정하는 데 사용됩니다. 모든 이름을 사용할 수 있지만 일반적으로 마이그레이션을 설명하는 이름을 선택합니다. 자세한 내용은 [마이그레이션 소개](xref:data/ef-mvc/migrations#introduction-to-migrations)를 참조하세요.

`Update-Database` 명령은 데이터베이스를 만드는 *Migrations/\<time-stamp>_InitialCreate.cs* 파일에서 `Up` 메서드를 실행합니다.

<a name="cli"></a>PMC 대신 명령줄 인터페이스(CLI)를 사용하여 앞의 단계를 수행할 수 있습니다.

* *.csproj* 파일에 [EF Core 도구](xref:data/ef-mvc/migrations#entity-framework-core-nuget-packages-for-migrations)를 추가합니다.
* 콘솔(프로젝트 디렉터리)에서 다음 명령을 실행합니다.

  ```console
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```     
  

[!INCLUDE[adding-model](../../includes/mvc-intro/adding-model3.md)]

[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?name=ConfigureServices&highlight=6-7)]

[!INCLUDE[adding-model](../../includes/mvc-intro/adding-model4.md)]

![ID, 가격, 출시일, 제목에 대해 사용 가능한 속성을 열거하는 모델 항목의 Intellisense 상황별 메뉴](adding-model/_static/ints.png)

## <a name="additional-resources"></a>추가 리소스

* [태그 도우미](xref:mvc/views/tag-helpers/intro)
* [전역화 및 지역화](xref:fundamentals/localization)

>[!div class="step-by-step"]
[이전 뷰 추가](adding-view.md)
[다음 SQL 사용](working-with-sql.md)  
