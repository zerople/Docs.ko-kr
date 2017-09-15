<!--
[!code-html[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Shared/_Layout.cshtml?highlight=7,31)]


[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_1stSearch)]

[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_SearchNull)]

![Index view](../../tutorials/first-mvc-app/search/_static/ghost.png)


[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?highlight=5&name=snippet_1)]

--> 

이제 검색을 제출할 때 URL에는 검색 쿼리 문자열이 포함됩니다. `HttpPost Index` 메서드가 있는 경우에도 검색은 `HttpGet Index` 작업 메서드로 이동합니다.

![URL에서 searchString=ghost 및 Ghostbusters 및 Ghostbusters 2라는 두 개의 반환된 영화를 표시하는 브라우저 창은 단어 ghost를 포함합니다.](../../tutorials/first-mvc-app/search/_static/search_get.png)

다음 표시는 `form` 태그에 대한 변경 내용을 표시합니다.

```html
<form asp-controller="Movies" asp-action="Index" method="get">
   ```

## <a name="adding-search-by-genre"></a>장르별 검색 추가

다음 `MovieGenreViewModel` 클래스를 *Models* 폴더에 추가합니다.

[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Models/MovieGenreViewModel.cs)]

영화 장르 보기 모델은 다음을 포함합니다.

   * 영화 목록
   * 장르 목록을 포함한 `SelectList` 이를 통해 사용자는 목록에서 장르를 선택할 수 있습니다.
   * 선택한 장르가 포함된 `movieGenre`

`MoviesController.cs`에서 `Index` 메서드를 다음 코드로 바꿉니다.

[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_SearchGenre)]

다음 코드는 데이터베이스에서 모든 장르를 검색하는 `LINQ` 쿼리입니다.

[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_LINQ)]

특정 장르를 프로젝트하여 장르의 `SelectList`를 생성합니다(선택 목록에 중복 장르가 없도록 함).

```csharp
movieGenreVM.genres = new SelectList(await genreQuery.Distinct().ToListAsync())
   ```

## <a name="adding-search-by-genre-to-the-index-view"></a>인덱스 보기에 장르별 검색 추가

다음과 같이 `Index.cshtml`을 업그레이드합니다.

[!code-HTML[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Movies/IndexFormGenreNoRating.cshtml?highlight=1,15,16,17,28,31,34,37,43)]

다음 HTML 도우미에서 사용되는 람다 식을 살펴봅니다.

`@Html.DisplayNameFor(model => model.movies[0].Title)`
 
이전 코드에서 `DisplayNameFor` HTML 도우미는 람다 식에서 참조되는 `Title` 속성을 검사하여 표시 이름을 확인합니다. 람다 식을 평가하지 않고 검사하기 때문에 `model`, `model.movies` 또는 `model.movies[0]`가 `null`이거나 비어 있을 경우 액세스 위반을 수신하지 않습니다. 람다 식이 계산될 경우(예: `@Html.DisplayFor(modelItem => item.Title)`) 모델의 속성 값이 계산됩니다.

장르별, 영화 제목별 및 둘 다로 검색하여 앱을 테스트합니다.
