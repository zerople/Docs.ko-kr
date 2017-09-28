# <a name="adding-a-new-field"></a>새 필드 추가

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)

이 자습서에서는 `Movies` 테이블에 새 필드를 추가합니다. 스키마를 변경하는 경우 데이터베이스를 삭제하고 새 데이터베이스를 만듭니다(새 필드 추가). 유지할 프로덕션 데이터가 없는 경우 이 워크플로는 개발 초기에 잘 작동합니다.

앱을 배포하고 유지해야 하는 데이터가 있으면 스키마를 변경해야 하는 경우 DB를 삭제할 수 없습니다. Entity Framework [Code First 마이그레이션](https://docs.microsoft.com/ef/core/get-started/aspnetcore/new-db)을 사용하면 스키마를 업데이트하고 데이터 손실 없이 데이터베이스를 마이그레이션할 수 있습니다. 마이그레이션은 SQL Server를 사용하는 경우 인기 있는 기능이지만 SQLlite는 다양한 마이그레이션 스키마 작업을 지원하지 않으므로 매우 간단하게 마이그레이션할 수 있습니다. 자세한 내용은 [SQLite 제한 사항](https://docs.microsoft.com/ef/core/providers/sqlite/limitations)을 참조하세요.

## <a name="adding-a-rating-property-to-the-movie-model"></a>영화 모델에 등급 속성 추가

*Models/Movie.cs* 파일을 열고 `Rating` 속성을 추가합니다.

[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Models/MovieDateRating.cs?highlight=11&range=7-18)]

`Movie` 클래스에 새 필드를 추가했으므로 이 새 속성이 포함되도록 바인딩 허용 목록도 업데이트해야 합니다. *MoviesController.cs*에서 `Rating` 속성을 포함하도록 `Create` 및 `Edit` 동작 메서드에 대해 `[Bind]` 속성을 업데이트합니다.

```csharp
[Bind("ID,Title,ReleaseDate,Genre,Price,Rating")]
   ```

브라우저 보기에서 새 `Rating` 속성을 표시, 작성 및 편집하기 위해 보기 템플릿도 업데이트해야 합니다.

*/Views/Movies/Index.cshtml* 파일을 편집하고 `Rating` 필드를 추가합니다.

[!code-HTML[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Movies/IndexGenreRating.cshtml?highlight=17,39&range=24-64)]

`Rating` 필드로 */Views/Movies/Create.cshtml*을 업데이트합니다.

앱은 새 필드를 포함하도록 DB를 업데이트할 때까지 작동하지 않습니다. 지금 실행하는 경우 다음 `SqliteException`이 발생합니다.

```
SqliteException: SQLite Error 1: 'no such column: m.Rating'.
```

업데이트된 영화 모델 클래스는 기존 데이터베이스의 영화 테이블의 스키마와 다르기 때문에 이 오류가 표시됩니다. (데이터베이스 테이블에 `Rating` 열이 없습니다.)

오류를 해결하는 몇 가지 방법이 있습니다.

1. 데이터베이스를 삭제하고 Entity Framework에서 새 모델 클래스 스키마에 따라 자동으로 다시 만듭니다. 이 방법으로 데이터베이스에서 기존 데이터를 손실하게 되므로 프로덕션 데이터베이스에서 수행할 수 없습니다. 테스트 데이터로 데이터베이스를 자동으로 시드하는 데 이니셜라이저를 사용하는 것은 종종 앱을 개발하는 효율적인 방법입니다.

2. 모델 클래스와 일치하도록 기존 데이터베이스의 스키마를 수동으로 수정합니다. 이 방법의 장점은 데이터를 유지한다는 점입니다. 이러한 변경을 수동으로 수행하거나 데이터베이스 변경 스크립트를 만들어 수행할 수 있습니다.

3. Code First 마이그레이션을 사용하여 데이터베이스 스키마를 업데이트합니다.

이 자습서의 경우 스키마가 변경될 때 데이터베이스를 삭제하고 다시 만듭니다. 터미널에서 다음 명령을 실행하여 DB를 삭제합니다.

`dotnet ef database drop`

새 열에 대해 값을 제공하도록 `SeedData` 클래스를 업데이트합니다. 샘플 변경은 아래에 표시되지만 각 `new Movie`에 대해 이 변경을 수행하려고 합니다.

[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Models/SeedDataRating.cs?name=snippet1&highlight=6)]

`Rating` 필드를 `Edit`, `Details` 및 `Delete` 뷰에 추가합니다.

앱을 실행하고 `Rating` 필드를 사용하여 동영상을 만들고/편집/표시할 수 있는지 확인합니다. 템플릿
