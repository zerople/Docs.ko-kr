---
title: "새 필드 추가 | Microsoft 문서"
author: rick-anderson
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 16efbacf-fe7b-4b41-84b0-06a1574b95c2
ms.technology: aspnet
ms.prod: asp.net-core
uid: tutorials/first-mvc-app/new-field
translationtype: Machine Translation
ms.sourcegitcommit: f93c93002fec0088a7040cd53f31fd5b5a62fea7
ms.openlocfilehash: 8feabdd7d05f1f63d8d1e0d6fd6480b4ef371662
ms.lasthandoff: 03/23/2017

---
# <a name="adding-a-new-field"></a>새 필드 추가

[Rick Anderson](https://twitter.com/RickAndMSFT)

이 섹션에서는 [Entity Framework](http://docs.efproject.net/en/latest/platforms/aspnetcore/new-db.html) 모델에 새 필드를 추가 하 고 마이그레이션하는 하도록 Code First 마이그레이션을 데이터베이스에 변경 합니다.

EF Code First를 자동으로 데이터베이스를 만들려면 사용할 때 Code First 테이블 데이터베이스에 추가 데이터베이스의 스키마에서 생성 된 모델 클래스와 동기화 인지 추적할 수 있습니다. 동기화 그렇지 않으면 EF 예외가 throw 됩니다. 이렇게 하면 보다 쉽게 일치 하지 않는 데이터베이스/코드 문제를 찾을 수 있습니다.

## <a name="adding-a-rating-property-to-the-movie-model"></a>영화 모델에 등급 속성 추가

열기는 *Models/Movie.cs* 파일 하 고 추가 `Rating` 속성:

[!code-csharp[주](start-mvc/sample/MvcMovie/Models/MovieDateRating.cs?highlight=11&range=7-18)]

(Ctrl + Shift + B) 앱을 빌드하십시오.

새 필드를 추가 하기 때문에 `Movie` 클래스 해야이 새로운 속성이 포함 됩니다. 바인딩 허용 목록을 업데이트 합니다. *MoviesController.cs*, 업데이트는 `[Bind]` 모두에 대 한 특성은 `Create` 및 `Edit` 포함할 작업 메서드는 `Rating` 속성:

```csharp
[Bind("ID,Title,ReleaseDate,Genre,Price,Rating")]
   ```

표시를 만들고 새 편집 하기 위해 템플릿 보기를 업데이트 해야 `Rating` 브라우저 보기에서 속성입니다.

편집 된 */Views/Movies/Index.cshtml* 파일 하 고 추가 `Rating` 필드:

[!code-HTML[주](start-mvc/sample/MvcMovie/Views/Movies/IndexGenreRating.cshtml?highlight=17,39&range=24-64)]

업데이트는 */Views/Movies/Create.cshtml* 와 `Rating` 필드입니다. 이전 "폼 그룹" 복사/붙여넣기를 필드를 업데이트 하면 intelliSense 도움말을 사용할 수 있도록 합니다. IntelliSense 작동 [태그 도우미](xref:mvc/views/tag-helpers/intro)합니다.

![개발자가 asp의 특성 값에 대 한 R 문자 입력-에 대 한 보기의 두 번째 레이블 요소에 있습니다. 자동으로 목록에서 강조 표시 되는 등급을 포함 하 여 사용 가능한 필드를 보여 주는 Intellisense 상황에 맞는 메뉴가 표시 됩니다. 개발자는 필드를 클릭 하거나 키보드에서 Enter 키를 누르면, 값 등급에 설정 됩니다.](new-field/_static/cr.png)

응용 프로그램에는 새 필드를 포함 하도록 DB 업데이트 될 때까지 작동 하지 않습니다. 지금 실행 하는 경우에 다음을 이용할 수 있습니다 `SqlException`:

`SqlException: Invalid column name 'Rating'.`

업데이트 된 영화 모델 클래스는 기존 데이터베이스의 Movie 테이블의 스키마와 다르기 때문에이 오류가 표시 됩니다. (데이터베이스 테이블에 없는 등급 열이.)

오류를 해결 하는 방법은 몇 가지가 있습니다.

1. 자동으로 삭제 하 고 새 모델 클래스 스키마에 따라 데이터베이스를 다시 만드는 Entity Framework가 있습니다. 이 방법은 테스트 데이터베이스;에서 현재 개발을 수행할 때 개발 주기의 초반에 매우 편리 하 게 신속 하 게 모델 및 데이터베이스 스키마를 함께 발전 시킬 수 있습니다. 단점도 있지만, 필요 하다는 데이터베이스에서 기존 데이터를 잃게-프로덕션 데이터베이스에서이 방법을 사용 하 여 좋습니다! 자동으로 테스트 데이터와 함께 데이터베이스를 시드하려면 이니셜라이저를 사용 하는 것이 응용 프로그램을 개발 하는 생산성 방법은 경우가 많습니다.

2. 모델 클래스와 일치 하도록 명시적으로 기존 데이터베이스의 스키마를 수정 합니다. 이 방법의 장점은 데이터를 유지 하는 점입니다. 이 하거나 수동으로 변경 하거나 데이터베이스를 만들고 스크립트를 변경할 수 있습니다.

3. Code First 마이그레이션을 사용 하 여 데이터베이스 스키마를 업데이트 합니다.

이 자습서에서는 Code First 마이그레이션을 사용 합니다.

업데이트는 `SeedData` 클래스를 새 열에 대 한 값을 제공 합니다. 샘플 변경 아래에 표시 되어 있지만 각각에 대해이 변경을 싶을 `new Movie`합니다.

[!code-csharp[주](start-mvc/sample/MvcMovie/Models/SeedDataRating.cs?name=snippet1&highlight=6)]

명령 프롬프트를 열고 솔루션을 빌드하십시오. 다음 명령을 입력 합니다.

```console
dotnet ef migrations add Rating
dotnet ef database update
```

`migrations add` 명령은 현재를 검사 하는 마이그레이션 프레임 워크를 지시 `Movie` 현재 있는 모델 `Movie` DB 스키마 DB 새 모델로 마이그레이션하는 데 필요한 코드를 만듭니다. 이름에 "등급"는 임의 이며 마이그레이션 파일 이름을 지정 하는 데 사용 됩니다. 마이그레이션 단계에 대 한 의미 있는 이름을 사용 하는 것이 좋습니다.

Initialize DB 시드 되며 포함 DB에 있는 모든 레코드를 삭제 하는 경우는 `Rating` 필드입니다. 브라우저 또는 SSOX에서 삭제 링크를 통해이 수행할 수 있습니다.

응용 프로그램을 실행 하 고 사용 하 여 동영상 만들기/편집/표시를 할 수 있습니다 확인는 `Rating` 필드입니다. 도 추가 해야는 `Rating` 필드는 `Edit`, `Details`, 및 `Delete` 템플릿을 보고 합니다.

>[!div class="step-by-step"]
[이전](search.md)
[다음](validation.md)  

