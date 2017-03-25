---
title: "모델 추가 | Microsoft 문서"
author: rick-anderson
description: "간단한 ASP.NET 핵심 응용 프로그램에 모델을 추가 합니다."
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 03/14/2017
ms.topic: article
ms.assetid: 8dc28498-00ee-4d66-b903-b593059e9f39
ms.technology: aspnet
ms.prod: asp.net-core
uid: tutorials/first-mvc-app/adding-model
translationtype: Machine Translation
ms.sourcegitcommit: 07af9db54db32298cb9de5e9baf033303afa0bc5
ms.openlocfilehash: ca3a607d2590c32e37bdd9ac36e19691227d44f6
ms.lasthandoff: 03/23/2017

---
# <a name="adding-a-model"></a>모델 추가

[Rick Anderson](https://twitter.com/RickAndMSFT)

이 섹션에서는 데이터베이스에는 영화를 관리 하기 위한 몇 가지 클래스를 추가 합니다. 이러한 클래스 됩니다는 "**M**odel"의 일부는 **M**VC 응용 프로그램입니다.

.NET Framework 데이터 액세스 기술을 사용 합니다는 [Entity Framework Core](https://docs.microsoft.com/en-us/ef/core) 정의 하 고 이러한 데이터 모델 클래스를 사용 합니다. Entity Framework Core (라고도 **EF** 코어) 기능을 호출 하는 개발 패러다임 *Code First*합니다. 먼저, 코드를 작성 하 고이 코드는 데이터베이스 테이블이 만들어집니다. 먼저 코드를 사용 하면 간단한 클래스를 작성 하 여 데이터 모델 개체를 만들 수 있습니다. (이 경우 "plain old CLR object"에서 POCO 클래스 라고도) 데이터베이스는 클래스에서 생성 됩니다. 데이터베이스를 만들려면 먼저 해야 할 경우 MVC 및 EF 응용 프로그램 개발에 대해 자세히 알아보려면이 자습서를 계속 따를 수 있습니다.

## <a name="adding-data-model-classes"></a>데이터 모델 클래스 추가

솔루션 탐색기에서 마우스 오른쪽 단추로 클릭 하 고 **MvcMovie** 프로젝트 > **추가** > **새 폴더**합니다. 폴더 이름을 *모델*합니다.

솔루션 탐색기에서 마우스 오른쪽 단추로 클릭 하 고 *모델* 폴더 > **추가** > **클래스**합니다. 클래스의 이름을 **영화** 하 고 다음 속성을 추가 합니다.

[!code-csharp[주](start-mvc/sample/MvcMovie/Models/MovieNoEF.cs?name=snippet_1&highlight=7)]

동영상을 모델링 하는 속성 외에 `ID` 기본 키에 대 한 데이터베이스에 필드가 필요 합니다. 프로젝트를 빌드합니다. 응용 프로그램을 작성 하지 않아도 다음 섹션에서 오류를 얻게 됩니다. 마지막으로 추가 했습니다는 **M**에 odel 우리의 **M**VC 응용 프로그램입니다.

## <a name="scaffolding-a-controller"></a>컨트롤러를 스 캐 폴딩

**솔루션 탐색기**를 마우스 오른쪽 단추로 클릭는 *컨트롤러* 폴더 **> 추가 > 컨트롤러**합니다.

![단계 위에 보기](adding-model/_static/add_controller.png)

에 **MVC 종속성 추가** 대화 상자에서 **최소한의 종속성**를 선택 하 고 **추가**합니다.

![단계 위에 보기](adding-model/_static/add_depend.png)

Visual Studio는 컨트롤러를 스 캐 폴딩 하는 데 필요한 종속성을 추가 합니다. A *ScaffoldingReadMe.txt* 삭제할 수 있는 파일이 생성 됩니다.  

**솔루션 탐색기**를 마우스 오른쪽 단추로 클릭는 *컨트롤러* 폴더 **> 추가 > 컨트롤러**합니다.

![단계 위에 보기](adding-model/_static/add_controller.png)

에 **스 캐 폴드 추가** 대화 상자에서 tap **Entity Framework를 사용 하 여 MVC 컨트롤러 (뷰 포함), > 추가**합니다.

![추가 스 캐 폴드 대화 상자](adding-model/_static/add_scaffold2.png)

완료 된 **컨트롤러 추가** 대화 상자:

* **모델 클래스:** *동영상 (MvcMovie.Models)*
* **데이터 컨텍스트 클래스:** 선택은 **+** 아이콘 및 기본 추가 **MvcMovie.Models.MvcMovieContext**

![데이터 컨텍스트를 추가 합니다.](adding-model/_static/dc.png)

* **보기:** 체크 각 옵션의 기본값 유지
* **컨트롤러 이름:** 기본 유지 *MoviesController*
* 누르기 **추가**

![컨트롤러 추가 대화 상자](adding-model/_static/add_controller2.png)

Visual Studio 스 캐 폴딩 엔진에서는 다음을 만듭니다.

* 영화 컨트롤러 (*Controllers/MoviesController.cs*)
* 만들기, 삭제, 세부 정보, 편집 및 인덱스 Razor 파일 보기 (*보기/영화*)

Visual Studio에서 자동으로 작성 된 [CRUD](https://en.wikipedia.org/wiki/Create,_read,_update_and_delete) (만들기, 읽기, 업데이트 및 삭제) 작업 메서드 및 보기에 있습니다. CRUD 작업 메서드 및 보기의 자동 만들기 라고 *스 캐 폴딩*합니다. 만들기, 목록, 편집 및 동영상 항목을 삭제할 수 있는 모든 기능을 갖춘 웹 응용 프로그램을 곧 해야 합니다.

앱을 실행 하 고 클릭 하는 경우는 **Mvc 동영상** 링크를 얻게 오류가 다음과 유사 하 게 합니다.

```text
An unhandled exception occurred while processing the request.
SqlException: Cannot open database "MvcMovieContext-<GUID removed>" 
requested by the login. The login failed.
Login failed for user Rick
```

## <a name="add-ef-tooling"></a>EF 도구를 추가 합니다.

- 솔루션 탐색기에서 마우스 오른쪽 단추로 클릭 하 고 **MvcMovie** 프로젝트 > **편집 MvcMovie.csproj**합니다.

   ![SE meu 편집 MvcMovie.csproj 표시](adding-model/_static/edit_csproj.png)

- 추가 된 `"Microsoft.EntityFrameworkCore.Tools.DotNet"` NuGet 패키지:

[!code-xml[주](start-mvc/sample/MvcMovie/MvcMovie.csproj?range=22-25&highlight=3)] 

참고: 위에 표시 된 버전 번호 있었습니다 작성 시점에 올바른.

## <a name="update-the-database"></a>데이터베이스 업데이트

* 명령 프롬프트를 열고 프로젝트 디렉터리로 이동 합니다. 선택 하 여 경로 찾을 수는 *Startup.cs* 솔루션 탐색기에서 파일 및 보기는 `Full Path` 속성에는 **속성** 창입니다.

* 명령 프롬프트에서 다음 명령을 실행합니다.

  ```console
  dotnet restore
  dotnet ef migrations add Initial
  dotnet ef database update
  ```
  
## <a name="dotnet-ef-commands"></a>dotnet ef 명령

* `dotnet`(.NET core)은.NET의 플랫폼 간 구현 합니다. 알아볼 수 있습니다 [여기](http://go.microsoft.com/fwlink/?LinkID=517853)합니다.
* `dotnet restore`:에 지정 된 NuGet 패키지 다운로드는 *.csproj* 파일입니다.
* `dotnet ef migrations add Initial`Entity Framework.NET Core CLI 마이그레이션 명령을 실행 하 고 초기 마이그레이션을 만듭니다. "초기" 매개 변수는 임의적 이지만 첫 번째에 대 한 일반적인 (*초기*) 마이그레이션 데이터베이스입니다. 이 작업을 만듭니다는 *데이터/마이그레이션을/\<날짜 및 시간 > _Initial.cs* 파일 추가 (또는 삭제) 마이그레이션 명령을 포함 하는 *영화* 데이터베이스에 테이블
* `dotnet ef database update`방금 만든 마이그레이션을 사용 하 여 데이터베이스를 업데이트 합니다.

## <a name="test-the-app"></a>앱 테스트

메모: 

* 응용 프로그램을 실행 하 고 탭은 **Mvc 동영상** 링크 합니다.
* 탭은 **새로 만들기** 에 연결 하 고 영화를 만듭니다.

  ![장르, 가격, 릴리스 날짜 및 제목에 대 한 필드와 뷰 만들기](adding-model/_static/movies.png)

* 소수점이 하 또는에 쉼표를 입력할 수는 `Price` 필드입니다. 지원 하기 위해 [jQuery 유효성 검사](http://jqueryvalidation.org/) 쉼표를 사용 하는 영어가 아닌 로캘의 (","), 소수점 및 미국 영어 이외의 날짜 형식, 응용 프로그램을 전역화를 취해야 합니다. 참조 [추가 리소스](#additional-resources) 에 대 한 자세한 내용은 합니다. 이제 방금 10 같은 정수를 입력 합니다.

<a name=displayformatdatelocal></a>

* 일부 로캘에서 날짜 형식을 지정 해야 합니다. 아래에 강조 표시 된 코드를 참조 하십시오.

[!code-csharp[주](start-mvc/sample/MvcMovie/Models/MovieDateFormat.cs?name=snippet_1&highlight=2,10)]

에 대해 알아보겠습니다 `DataAnnotations` 자습서의 뒷부분에 나오는 합니다.

누르기 **만들기** 하면 폼이 영화 정보를 데이터베이스에 저장 된 위치를 서버에 게시할 수 있습니다. 다음으로 리디렉션됩니다는 */Movies* URL을 목록에서 새로 만든된 영화를 볼 수 있습니다.

![영화 새로 만든 보여 주는 뷰 영화 목록](adding-model/_static/h.png)

영화 항목 두 개 더 만듭니다. 시도 **편집**, **세부 정보**, 및 **삭제** 링크는 모두 작동 합니다.

## <a name="examining-the-generated-code"></a>생성된 된 코드를 검사합니다.

열기는 *Startup.cs* 파일을 검사 `ConfigureServices`:

[!code-csharp[주](start-mvc/sample/MvcMovie/Startup.cs?name=snippet_cs&highlight=7-8)]

위의 코드에 추가 되는 영화 데이터베이스 컨텍스트를 보여 줍니다.는 [종속성 주입](xref:fundamentals/dependency-injection) 컨테이너입니다.

열기는 *Controllers/MoviesController.cs* 파일을 생성자에 검사 합니다.

<!-- l.. Make copy of Movies controller because we comment out the initial index method and update it later  -->

[!code-csharp[주](start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_1)] 

생성자를 사용 하 여 [종속성 주입](xref:fundamentals/dependency-injection) 데이터베이스 컨텍스트를 주입 하 (`MvcMovieContext `) 컨트롤러에 있습니다. 각각의 데이터베이스 컨텍스트를 사용 하는 [CRUD](https://en.wikipedia.org/wiki/Create,_read,_update_and_delete) 는 컨트롤러의 메서드에 있습니다.

<a name=strongly-typed-models-keyword-label></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a>강력한 형식의 모델 및 @model 키워드

이 자습서의 앞부분에서 컨트롤러를 전달할 수 있는 방법을 데이터 나 개체를 사용 하 여 보기를 확인은 `ViewData` 사전입니다. `ViewData` 사전이 정보 보기를 전달 하는 편리한 런타임에 바인딩된 방법을 제공 하는 동적 개체입니다.

MVC는 또한 형식의 모델 개체를 보기를 강력 하 게 전달 하는 기능을 제공 합니다. 이 강력한 형식의 방법을 통해 더 나은 컴파일 타임의 코드 검사 하 고 풍부한 수 [IntelliSense](https://msdn.microsoft.com/en-us/library/hcw1s69b.aspx) Visual Studio (VS). VS에서 스 캐 폴딩 메커니즘 (즉, 전달 하는 경우 강력한 형식의 모델)이 접근이 방식을 사용한는 `MoviesController` 클래스와 메서드 및 뷰를 만들 때 보기.

생성 된 검사 `Details` 에서 메서드는 *Controllers/MoviesController.cs* 파일:

[!code-csharp[주](start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_details)]

`id` 매개 변수는 일반적으로 전달 경로 데이터로 예를 들어 `http://localhost:1234/movies/details/1` 설정:

* 컨트롤러에는 `movies` 컨트롤러 (첫 번째 URL 세그먼트).
* 동작을 `details` (두 번째 URL 세그먼트).
* 1 (마지막 URL 세그먼트) id입니다.

에 전달할 수 있습니다는 `id` 다음과 같이 쿼리 문자열:

`http://localhost:1234/movies/details?id=1`

인스턴스는 영화를 발견 하는 경우는 `Movie` 모델에 전달 되는 `Details` 보기:

```csharp
return View(movie);
   ```

내용을 검사는 *Views/Movies/Details.cshtml* 파일:

[!code-html[주](start-mvc/sample/MvcMovie/Views/Movies/DetailsOriginal.cshtml)]

포함 하 여 한 `@model` 보기 파일 맨 위에 있는 문을 보기를 필요로 하는 개체의 형식을 지정할 수 있습니다. 다음에 Visual Studio는 영화 컨트롤러를 만들 때 자동으로 포함 `@model` 맨 위에 있는 문에 *Details.cshtml* 파일:

```HTML
@model MvcMovie.Models.Movie
   ```

이 `@model` 지시문을 사용 하면 동영상을 사용 하 여 보기에 전달 된 컨트롤러에 액세스 하는 `Model` 개체는 강력한 형식입니다. 예를 들어,는 *Details.cshtml* 보기, 코드는 전달 각 영화 필드는 `DisplayNameFor` 및 `DisplayFor` HTML 도우미와 강력한 형식의 `Model` 개체입니다. `Create` 및 `Edit` 메서드 및 보기도 전달는 `Movie` 모델 개체입니다.

검사는 *Index.cshtml* 보기 및 `Index` 영화 컨트롤러의 메서드에서 합니다. 코드를 만드는 방법을 알 수는 `List` 호출 되 면 개체는 `View` 메서드. 이 코드는 전달 `Movies` 에서 목록는 `Index` 보기에 대 한 작업 메서드:

[!code-csharp[주](start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_index)]

다음에 Visual Studio는 영화 컨트롤러를 만들 때 자동으로 포함 `@model` 맨 위에 있는 문에 *Index.cshtml* 파일:

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-html[주](start-mvc/sample/MvcMovie/Views/Movies/IndexOriginal.cshtml?range=1)]

`@model` 지시문을 사용 하면 컨트롤러를 사용 하 여 뷰로 전달 되는 동영상 목록을 액세스할 수는 `Model` 개체는 강력한 형식입니다. 예를 들어는 *Index.cshtml* 보기를 사용 하 여 동영상을 통해 루프 코드를 사용 하는 한 `foreach` 문을 통해 강력한 형식의 `Model` 개체:

[!code-html[주](start-mvc/sample/MvcMovie/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

때문에 `Model` 개체는 강력한 형식 (으로 `IEnumerable<Movie>` 개체), 루프의 각 항목으로 형식화 된 `Movie`합니다. 여러 가지 이점을 얻을 즉, 컴파일 타임의 코드 검사 및 전체를 얻을 수 있다는 [IntelliSense](https://msdn.microsoft.com/en-us/library/hcw1s69b.aspx) 코드 편집기에서 지원 합니다.

![ID, 가격, 릴리스 날짜 및 제목에 대 한 사용 가능한 속성을 나열 하는 모델 항목에 Intellisense 상황에 맞는 메뉴](adding-model/_static/ints.png)

이제 데이터베이스와 페이지를 표시, 편집, 업데이트 및 데이터 삭제 되었습니다. 다음 자습서에서는 데이터베이스 협력 합니다.

## <a name="additional-resources"></a>추가 리소스

* [태그 도우미](../../mvc/views/tag-helpers/index.md)

* [전역화 및 지역화](../../fundamentals/localization.md)

>[!div class="step-by-step"]
[이전](adding-view.md)
[다음](working-with-sql.md)  

