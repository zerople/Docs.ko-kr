`Movie` 클래스에 다음 속성을 추가합니다.

[!code-csharp[Main](../../tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/Models/MovieNoEF.cs?name=snippet_MovieNoEF)]

`ID` 필드는 데이터베이스에서 기본 키 대신 필요합니다.

<a name="dc"></a>
### <a name="add-a-database-context-class"></a>데이터베이스 컨텍스트 클래스 추가

*MovieContext.cs*라는 `DbContext` 파생 클래스를 *Models* 폴더에 추가합니다.

[!code-csharp[Main](../../tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/Models/MovieContext.cs)]

이전 코드에서는 엔터티 집합에 대한 `DbSet` 속성을 만듭니다. Entity Framework 용어에서 엔터티 집합은 일반적으로 데이터베이스 테이블에 해당하고 엔터티는 테이블의 행에 해당합니다.