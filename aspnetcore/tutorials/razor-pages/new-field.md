---
title: "Razor 페이지에 새 필드 추가"
author: rick-anderson
description: "Entity Framework Core를 사용하여 Razor 페이지에 새 필드를 추가하는 방법을 보여 줍니다."
keywords: "ASP.NET Core, Entity Framework Core, 마이그레이션"
ms.author: riande
manager: wpickett
ms.date: 8/7/2017
ms.topic: get-started-article
ms.technology: aspnet
ms.prod: aspnet-core
uid: tutorials/razor-pages/new-field
ms.openlocfilehash: bda00f290043251ad308192c5b1a873ae7cd0d85
ms.sourcegitcommit: e832a9b9f41a8b26a8c88edfd8fc35b8bfd97d5d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2017
---
# <a name="adding-a-new-field-to-a-razor-page"></a>Razor 페이지에 새 필드 추가

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)

이 섹션에서는 [Entity Framework](http://docs.efproject.net/en/latest/platforms/aspnetcore/new-db.html) Code First 마이그레이션을 사용하여 모델에 새 필드를 추가하고 해당 변경 내용을 데이터베이스로 마이그레이션합니다.

EF Code First를 사용하여 자동으로 데이터베이스를 만들 때 Code First는 데이터베이스에 테이블을 추가하여 데이터베이스의 스키마가 생성된 모델 클래스와 동기화되어 있는지 여부를 추적합니다. 동기화되어 있지 않은 경우 EF에서 예외를 throw합니다. 이렇게 하면 더 쉽게 일관성이 없는 데이터베이스/코드 문제를 찾을 수 있습니다.

## <a name="adding-a-rating-property-to-the-movie-model"></a>동영상 모델에 등급 속성 추가

*Models/Movie.cs* 파일을 열고 `Rating` 속성을 추가합니다.

[!code-csharp[Main](razor-pages-start/sample/RazorPagesMovie/Models/MovieDateRating.cs?highlight=11&range=7-18)]

앱을 빌드합니다(Ctrl+Shift+B).

*Pages/Movies/Index.cshtml*를 편집하고 `Rating` 필드를 추가합니다.

[!code-cshtml[Main](razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Index.cshtml?highlight=40-42,61-63)]

삭제 및 세부 정보 페이지에 `Rating` 필드를 추가합니다.

*Create.cshtml*을 `Rating` 필드로 업데이트합니다. 이전 `<div>` 요소를 복사/붙여넣기하고 intelliSense에서 필드를 업데이트하도록 할 수 있습니다. IntelliSense는 [태그 도우미](xref:mvc/views/tag-helpers/intro)와 함께 작동합니다.

![개발자가 보기의 두 번째 레이블 요소에서 asp-for의 특성 값에 대해 문자 R을 입력했습니다. 목록에서 자동으로 강조 표시되는 Rating을 포함하여 사용 가능한 필드를 보여 주는 Intellisense 바로 가기 메뉴가 표시되었습니다. 개발자가 필드를 클릭하거나 키보드에서 Enter 키를 누르면 값은 Rating으로 설정됩니다.](new-field/_static/cr.png)

다음 코드는 `Rating` 필드와 함께 *Create.cshtml*을 보여 줍니다.

[!code-cshtml[Main](razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Create.cshtml?highlight=31-35)]

편집 페이지에 `Rating` 필드를 추가합니다.

앱은 새 필드를 포함하도록 DB가 업데이트될 때까지 작동하지 않습니다. 지금 실행하면 앱은 `SqlException`을 throw합니다.

`SqlException: Invalid column name 'Rating'.`

이 오류는 데이터베이스의 동영상 테이블의 스키마와 다른 업데이트된 동영상 모델 클래스로 인해 발생됩니다. (데이터베이스 테이블에 `Rating` 열이 없습니다.)

오류를 해결하는 몇 가지 방법이 있습니다.

1. Entity Framework에서 새 모델 클래스 스키마를 사용하여 데이터베이스를 자동으로 삭제하고 다시 만들도록 합니다. 이 방법은 개발 주기의 초기 단계에서 편리하며 신속하게 모델 및 데이터베이스 스키마를 함께 개발할 수 있습니다. 단점은 데이터베이스의 기존 데이터를 잃게 된다는 것입니다. 프로덕션 데이터베이스에서 이러한 방법을 사용하지 않으려 합니다. 테스트 데이터로 데이터베이스를 자동으로 시드하도록 스키마에서 DB를 삭제하고 이니셜라이저를 사용하는 것은 종종 앱을 개발하는 효율적인 방법입니다.

2. 모델 클래스와 일치하도록 기존 데이터베이스의 스키마를 명시적으로 수정합니다. 이 방법의 장점은 데이터를 유지한다는 점입니다. 이러한 변경을 수동으로 수행하거나 데이터베이스 변경 스크립트를 만들어 수행할 수 있습니다.

3. Code First 마이그레이션을 사용하여 데이터베이스 스키마를 업데이트합니다.

이 자습서의 경우 Code First 마이그레이션을 사용합니다.

새 열에 대해 값을 제공하도록 `SeedData` 클래스를 업데이트합니다. 샘플 변경은 아래에 표시되지만 각 `new Movie` 블록에 대해 이 변경을 수행하려고 합니다.

[!code-csharp[Main](razor-pages-start/sample/RazorPagesMovie/Models/SeedDataRating.cs?name=snippet1&highlight=6)]

[완료된 SeedData.cs 파일](https://github.com/aspnet/Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/Models/SeedDataRating.cs)을 참조하세요.

솔루션을 빌드합니다.

<a name="pmc"></a>

**도구** 메뉴에서 **NuGet 패키지 관리자 > 패키지 관리자 콘솔**을 선택합니다.
PMC에서 다음 명령을 입력합니다.

```PMC
Add-Migration Rating
Update-Database
```

`Add-Migration` 명령은 프레임워크에 다음 작업을 지시합니다.

* `Movie` 모델을 `Movie` DB 스키마와 비교합니다.
* DB 스키마를 새 모델로 마이그레이션하도록 코드를 만듭니다.

"Rating" 이름은 임의로 지정되며 마이그레이션 파일의 이름을 지정하는 데 사용됩니다. 마이그레이션 파일에 의미 있는 이름을 사용하는 것이 좋습니다.

<a name="ssox"></a> DB의 모든 레코드를 삭제하는 경우 이니셜라이저에서 DB를 시드하고 `Rating` 필드를 포함합니다. 브라우저 또는 [SSOX](xref:tutorials/razor-pages/sql#ssox)(Sql Server 개체 탐색기)에서 삭제 링크를 사용하여 이를 수행할 수 있습니다. SSOX에서 데이터베이스를 삭제하려면:

* SSOX에서 데이터베이스를 선택합니다.
* 데이터베이스를 마우스 오른쪽 단추로 클릭하고 *삭제*를 선택합니다.
* **기존 연결 닫기*를 선택합니다.
* **확인**을 선택합니다.
* [PMC](xref:tutorials/razor-pages/new-field#pmc)에서 데이터베이스를 업데이트합니다. 

    ```PMC
    Update-Database
    ```

앱을 실행하고 `Rating` 필드를 사용하여 동영상을 만들고/편집/표시할 수 있는지 확인합니다. 데이터베이스가 시드되지 않은 경우 IIS Express를 중지한 다음 앱을 실행합니다.

>[!div class="step-by-step"]
[이전: 검색 추가](xref:tutorials/razor-pages/search)
[다음: 새 필드 추가](xref:tutorials/razor-pages/new-field)
