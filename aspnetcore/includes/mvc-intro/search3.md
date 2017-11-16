<!--
[!code-html[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Shared/_Layout.cshtml?highlight=7,31)]


[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_1stSearch)]

[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_SearchNull)]

![Index view](../../tutorials/first-mvc-app/search/_static/ghost.png)


[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?highlight=5&name=snippet_1)]

--> 

<span data-ttu-id="0b449-101">이제 검색을 제출할 때 URL에는 검색 쿼리 문자열이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="0b449-101">Now when you submit a search, the URL contains the search query string.</span></span> <span data-ttu-id="0b449-102">`HttpPost Index` 메서드가 있는 경우에도 검색은 `HttpGet Index` 작업 메서드로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="0b449-102">Searching will also go to the `HttpGet Index` action method, even if you have a `HttpPost Index` method.</span></span>

![URL에서 searchString=ghost 및 Ghostbusters 및 Ghostbusters 2라는 두 개의 반환된 영화를 표시하는 브라우저 창은 단어 ghost를 포함합니다.](../../tutorials/first-mvc-app/search/_static/search_get.png)

<span data-ttu-id="0b449-104">다음 표시는 `form` 태그에 대한 변경 내용을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="0b449-104">The following markup shows the change to the `form` tag:</span></span>

```html
<form asp-controller="Movies" asp-action="Index" method="get">
   ```

## <a name="adding-search-by-genre"></a><span data-ttu-id="0b449-105">장르별 검색 추가</span><span class="sxs-lookup"><span data-stu-id="0b449-105">Adding Search by genre</span></span>

<span data-ttu-id="0b449-106">다음 `MovieGenreViewModel` 클래스를 *Models* 폴더에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="0b449-106">Add the following `MovieGenreViewModel` class to the *Models* folder:</span></span>

[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Models/MovieGenreViewModel.cs)]

<span data-ttu-id="0b449-107">영화 장르 보기 모델은 다음을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="0b449-107">The movie-genre view model will contain:</span></span>

   * <span data-ttu-id="0b449-108">영화 목록</span><span class="sxs-lookup"><span data-stu-id="0b449-108">A list of movies.</span></span>
   * <span data-ttu-id="0b449-109">장르 목록을 포함한 `SelectList`</span><span class="sxs-lookup"><span data-stu-id="0b449-109">A `SelectList` containing the list of genres.</span></span> <span data-ttu-id="0b449-110">이를 통해 사용자는 목록에서 장르를 선택할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0b449-110">This will allow the user to select a genre from the list.</span></span>
   * <span data-ttu-id="0b449-111">선택한 장르가 포함된 `movieGenre`</span><span class="sxs-lookup"><span data-stu-id="0b449-111">`movieGenre`, which contains the selected genre.</span></span>

<span data-ttu-id="0b449-112">`MoviesController.cs`에서 `Index` 메서드를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="0b449-112">Replace the `Index` method in `MoviesController.cs` with the following code:</span></span>

[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_SearchGenre)]

<span data-ttu-id="0b449-113">다음 코드는 데이터베이스에서 모든 장르를 검색하는 `LINQ` 쿼리입니다.</span><span class="sxs-lookup"><span data-stu-id="0b449-113">The following code is a `LINQ` query that retrieves all the genres from the database.</span></span>

[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_LINQ)]

<span data-ttu-id="0b449-114">특정 장르를 프로젝트하여 장르의 `SelectList`를 생성합니다(선택 목록에 중복 장르가 없도록 함).</span><span class="sxs-lookup"><span data-stu-id="0b449-114">The `SelectList` of genres is created by projecting the distinct genres (we don't want our select list to have duplicate genres).</span></span>

```csharp
movieGenreVM.genres = new SelectList(await genreQuery.Distinct().ToListAsync())
   ```

## <a name="adding-search-by-genre-to-the-index-view"></a><span data-ttu-id="0b449-115">인덱스 보기에 장르별 검색 추가</span><span class="sxs-lookup"><span data-stu-id="0b449-115">Adding search by genre to the Index view</span></span>

<span data-ttu-id="0b449-116">다음과 같이 `Index.cshtml`을 업그레이드합니다.</span><span class="sxs-lookup"><span data-stu-id="0b449-116">Update `Index.cshtml` as follows:</span></span>

[!code-HTML[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Movies/IndexFormGenreNoRating.cshtml?highlight=1,15,16,17,28,31,34,37,43)]

<span data-ttu-id="0b449-117">다음 HTML 도우미에서 사용되는 람다 식을 살펴봅니다.</span><span class="sxs-lookup"><span data-stu-id="0b449-117">Examine the lambda expression used in the following HTML Helper:</span></span>

`@Html.DisplayNameFor(model => model.movies[0].Title)`
 
<span data-ttu-id="0b449-118">이전 코드에서 `DisplayNameFor` HTML 도우미는 람다 식에서 참조되는 `Title` 속성을 검사하여 표시 이름을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="0b449-118">In the preceding code, the `DisplayNameFor` HTML Helper inspects the `Title` property referenced in the lambda expression to determine the display name.</span></span> <span data-ttu-id="0b449-119">람다 식을 평가하지 않고 검사하기 때문에 `model`, `model.movies` 또는 `model.movies[0]`가 `null`이거나 비어 있을 경우 액세스 위반을 수신하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="0b449-119">Since the lambda expression is inspected rather than evaluated, you don't receive an access violation when `model`, `model.movies`, or `model.movies[0]` are `null` or empty.</span></span> <span data-ttu-id="0b449-120">람다 식이 계산될 경우(예: `@Html.DisplayFor(modelItem => item.Title)`) 모델의 속성 값이 계산됩니다.</span><span class="sxs-lookup"><span data-stu-id="0b449-120">When the lambda expression is evaluated (for example, `@Html.DisplayFor(modelItem => item.Title)`), the model's property values are evaluated.</span></span>

<span data-ttu-id="0b449-121">장르별, 영화 제목별 및 둘 다로 검색하여 앱을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="0b449-121">Test the app by searching by genre, by movie title, and by both.</span></span>
