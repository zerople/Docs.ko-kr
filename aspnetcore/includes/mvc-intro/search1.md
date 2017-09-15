# <a name="adding-search-to-an-aspnet-core-mvc-app"></a>ASP.NET Core MVC 앱에 검색 추가

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)

이 섹션에서는 *장르* 또는 *이름*별로 영화를 검색할 수 있는 `Index` 작업 메서드에 검색 기능을 추가합니다.

`Index` 메서드를 다음 코드로 업데이트합니다.
<!--
[!code-html[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Shared/_Layout.cshtml?highlight=7,31)]
-->

[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_1stSearch)]

`Index` 작업 메서드의 첫 번째 줄은 영화를 선택하는 [LINQ](http://msdn.microsoft.com/library/bb397926.aspx) 쿼리를 만듭니다.

```csharp
var movies = from m in _context.Movie
             select m;
```

쿼리는 이 시점에서*만* 정의되며 데이터베이스에 대해 실행되지 **않았습니다**.

`searchString` 매개 변수에 문자열이 포함되는 경우 영화 쿼리는 검색 문자열의 값에 대해 필터링하도록 수정됩니다.

[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_SearchNull)]

위의 `s => s.Title.Contains()` 코드는 [람다 식](http://msdn.microsoft.com/library/bb397687.aspx)입니다. 람다 식은 메서드 기반 [LINQ](http://msdn.microsoft.com/library/bb397926.aspx) 쿼리에서 [Where](http://msdn.microsoft.com/library/system.linq.enumerable.where.aspx) 메서드 또는 `Contains`(위의 코드에서 사용됨)와 같은 표준 쿼리 연산자 메서드의 인수로 사용됩니다. LINQ 쿼리는 정의될 때 또는 메서드(예: `Where`, `Contains` 또는 `OrderBy`)를 호출하여 수정될 때 실행되지 않습니다. 대신 쿼리 실행이 지연됩니다.  즉, 실제로 실현된 값이 반복되거나 `ToListAsync` 메서드가 호출될 때까지 식의 계산이 지연되는 것을 의미합니다. 지연된 쿼리 실행에 대한 자세한 내용은 [쿼리 실행](http://msdn.microsoft.com/library/bb738633.aspx)을 참조하세요.

참고: [Contains](http://msdn.microsoft.com/library/bb155125.aspx) 메서드는 위에 표시된 C# 코드에서가 아닌 데이터베이스에서 실행됩니다. 쿼리에 대한 대/소문자 구분은 데이터베이스 및 데이터 정렬에 따라 달라집니다. SQL Server에서 [Contains](http://msdn.microsoft.com/library/bb155125.aspx)는 대/소문자를 구분하는 [SQL LIKE](http://msdn.microsoft.com/library/ms179859.aspx)로 매핑됩니다. SQLlite에서 기본 데이터 정렬과 함께 대/소문자를 구분합니다.

`/Movies/Index`로 이동합니다. 쿼리 문자열(예: `?searchString=Ghost`)을 URL에 추가합니다. 필터링된 영화가 표시됩니다.

![인덱스 보기](../../tutorials/first-mvc-app/search/_static/ghost.png)

`id`라는 매개 변수를 포함하도록 `Index` 메서드의 서명을 변경하는 경우 `id` 매개 변수는 *Startup.cs*에서 설정된 기본 경로의 선택적 `{id}` 자리 표시자와 일치합니다.

[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?highlight=5&name=snippet_1)]