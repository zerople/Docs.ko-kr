---
title: "ASP.NET MVC Core 앱에 모델 추가"
author: rick-anderson
description: "간단한 ASP.NET Core 앱에 모델을 추가합니다."
keywords: "ASP.NET Core,MVC,스캐폴드,스캐폴딩"
ms.author: riande
manager: wpickett
ms.devlang: csharp
ms.date: 09/22/2017
ms.topic: get-started-article
ms.assetid: 8dc28498-eeee-1638-b903-b593059e9f39
ms.technology: aspnet
ms.prod: .net-core
uid: tutorials/first-mvc-app-mac/adding-model
ms.openlocfilehash: 0e2c47c7d9903d6d300fa293dd0530a2f65f77d8
ms.sourcegitcommit: e6bcd56a4b11e20ff55df004971f9ed384937342
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2017
---
[!INCLUDE[adding-model](../../includes/mvc-intro/adding-model1.md)]

* *Models* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 파일**을 선택합니다. 
* **새 파일** 대화 상자에서:

  * 왼쪽 창에서 **일반**을 선택합니다.
  * 가운데 창에서 **빈 클래스**를 선택합니다.
  * 클래스 이름을 **Movie**로 지정하고 **새로 만들기**를 선택합니다.

`Movie` 클래스에 다음 속성을 추가합니다.

[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Models/MovieNoEF.cs?name=snippet_1)]

`ID` 필드는 데이터베이스에서 기본 키 대신 필요합니다.

프로젝트를 빌드하여 오류가 없는지 확인합니다. 이제 **M**VC 앱에 **모**델이 있습니다.

## <a name="prepare-the-project-for-scaffolding"></a>프로젝트에서 스캐폴딩을 준비합니다.

- 프로젝트 파일을 마우스 오른쪽 단추로 클릭하고 **도구 > 파일 편집**을 선택합니다.

  ![위의 단계 보기](adding-model/_static/1.png)

- 다음 강조 표시된 NuGet 패키지를 *MvcMovie.csproj* 파일에 추가합니다.
             
  [!code-csharp[Main](../first-mvc-app-xplat/start-mvc/sample/MvcMovie/MvcMovie.csproj?highlight=7,10)]

- 파일을 저장합니다.

- *Models/MvcMovieContext.cs* 파일을 만들고 다음 `MvcMovieContext` 클래스를 추가합니다. [!code-csharp[Main](../../tutorials/first-mvc-app-xplat/start-mvc/sample/MvcMovie/Models/MvcMovieContext.cs)]
   
- *Startup.cs* 파일을 열고 두 개의 using을 추가합니다. [!code-csharp[Main](../../tutorials/first-mvc-app-xplat/start-mvc/sample/MvcMovie/Startup.cs?name=snippet1&highlight=1,2)]

- 데이터베이스 컨텍스트를 *Startup.cs* 파일에 추가합니다.

   [!code-csharp[Main](../../tutorials/first-mvc-app-xplat/start-mvc/sample/MvcMovie/Startup.cs?name=snippet2&highlight=6-7)]

  이 코드는 데이터 모델에 포함되는 모델 클래스를 Entity Framework에 알립니다. 데이터베이스에서 Movie 테이블을 표현할 Movie 개체의 *엔터티 집합* 하나를 정의하고 있습니다.

- 프로젝트를 빌드하여 오류가 없는지 확인합니다.

## <a name="scaffold-the-moviecontroller"></a>MovieController 스캐폴드

프로젝트 폴더에서 터미널 창을 열고 다음 명령을 실행합니다.

```
dotnet restore
dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries 
```
오류 `No executable found matching command "dotnet-aspnet-codegenerator", verify`가 표시될 경우:

 * 현재 위치가 프로젝트 디렉터리입니다. 프로젝트 디렉터리에는 *Program.cs*, *Startup.cs* 및 *.csproj* 파일이 있습니다.
 * dotnet 버전이 1.1 이상입니다. `dotnet`을 실행하여 버전을 확인합니다.
 * `<DotNetCliToolReference>` 요소를 [MvcMovie.csproj 파일](#prepare-the-project-for-scaffolding)에 추가했습니다.
 
<!--
> [!NOTE]
> If you get an error when the scaffolding command runs, see [issue 444 in the scaffolding repository](https://github.com/aspnet/scaffolding/issues/444) for a workaround.
-->

스캐폴딩 엔진은 다음을 만듭니다.

* 동영상 컨트롤러(*Controllers/MoviesController.cs*)
* 만들기, 삭제, 세부 정보, 편집 및 인덱스 페이지에 대한 Razor 뷰 파일(*Views/Movies/\*.cshtml*)

[CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete)(만들기, 읽기, 업데이트 및 삭제) 작업 메서드와 뷰를 자동으로 만드는 작업을 *스캐폴딩*이라고 합니다. 동영상 데이터베이스를 관리할 수 있는 완벽하게 작동하는 웹 응용 프로그램이 곧 제공됩니다.

### <a name="add-the-files-to-visual-studio"></a>Visual Studio에 파일 추가

* *MovieController.cs* 파일을 Visual Studio 프로젝트에 추가합니다.

  * *Controllers* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가 > 파일 추가**를 선택합니다.
  * *MovieController.cs* 파일을 선택합니다.

* *Movies* 폴더 및 뷰를 추가합니다.

  * *Views* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가 > 기존 폴더 추가**를 선택합니다.
  * *Views* 폴더로 이동하고, *Views\Movies*를 선택하고 나서, **열기**를 선택합니다.
  * **Movies에서 추가할 파일 선택** 대화 상자에서 **모두 포함**, **확인**을 차례로 선택합니다.

[!INCLUDE[adding-model 2x](../../includes/mvc-intro/adding-model2xp.md)]

[!INCLUDE[adding-model](../../includes/mvc-intro/adding-model3.md)]

이제 데이터를 표시, 편집, 업데이트 및 삭제할 데이터베이스 및 페이지가 제공됩니다. 다음 자습서에서는 데이터베이스 작업을 수행합니다.

## <a name="additional-resources"></a>추가 리소스

* [태그 도우미](xref:mvc/views/tag-helpers/intro)
* [전역화 및 지역화](xref:fundamentals/localization)

>[!div class="step-by-step"]
[이전 뷰 추가](adding-view.md)
[다음 SQL 사용](working-with-sql.md)  
