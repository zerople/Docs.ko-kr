<span data-ttu-id="10dad-101">위의 강조 표시된 코드는 [종속성 주입](xref:fundamentals/dependency-injection) 컨테이너에 추가된 영화 데이터베이스 컨텍스트를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="10dad-101">The highlighted code above shows the movie database context being added to the [Dependency Injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="10dad-102">다음 `services.AddDbContext<MvcMovieContext>(options =>` 줄이 표시되지 않습니다(코드 참조).</span><span class="sxs-lookup"><span data-stu-id="10dad-102">The line following `services.AddDbContext<MvcMovieContext>(options =>` is not shown (see your code).</span></span> <span data-ttu-id="10dad-103">데이터베이스가 연결 문자열을 사용하도록 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="10dad-103">It specifies the database to use and the connection string.</span></span> <span data-ttu-id="10dad-104">`=>`은 [람다 연산자](https://docs.microsoft.com/dotnet/articles/csharp/language-reference/operators/lambda-operator)입니다.</span><span class="sxs-lookup"><span data-stu-id="10dad-104">`=>` is a [lambda operator](https://docs.microsoft.com/dotnet/articles/csharp/language-reference/operators/lambda-operator).</span></span>

<span data-ttu-id="10dad-105">*Controllers/MoviesController.cs* 파일을 열고 생성자를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="10dad-105">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller because we comment out the initial index method and update it later  -->

<span data-ttu-id="10dad-106">[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_1)]</span><span class="sxs-lookup"><span data-stu-id="10dad-106">[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_1)]</span></span> 

<span data-ttu-id="10dad-107">생성자는 [종속성 주입](xref:fundamentals/dependency-injection)을 사용하여 컨트롤러에 데이터베이스 컨텍스트(`MvcMovieContext `)를 삽입할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="10dad-107">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext `) into the controller.</span></span> <span data-ttu-id="10dad-108">컨트롤러의 [CRUD](https://en.wikipedia.org/wiki/Create,_read,_update_and_delete) 메서드 각각에서 데이터베이스 컨텍스트를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="10dad-108">The database context is used in each of the [CRUD](https://en.wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<a name=strongly-typed-models-keyword-label></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="10dad-109">강력한 형식의 모델 및 @model 키워드</span><span class="sxs-lookup"><span data-stu-id="10dad-109">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="10dad-110">이 자습서의 앞부분에서 언급했듯이 컨트롤러가 `ViewData` 사전을 사용하여 데이터 또는 개체를 뷰에 전달할 수 있는 방법이 표시되었습니다.</span><span class="sxs-lookup"><span data-stu-id="10dad-110">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="10dad-111">`ViewData` 사전은 확인할 정보를 전달하는 편리한 런타임에 바인딩된 방법을 제공하는 동적 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="10dad-111">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="10dad-112">또한 MVC는 확인할 강력한 형식의 모델 개체를 전달하는 기능을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="10dad-112">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="10dad-113">이 방법을 사용하면 코드를 검사하는 컴파일 시간을 개선할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="10dad-113">This strongly typed approach enables better compile-time checking of your code.</span></span> <span data-ttu-id="10dad-114">스캐폴딩 메커니즘은 메서드 및 뷰를 만든 경우 `MoviesController` 클래스 및 뷰에서 이 방법(즉, 강력한 형식의 모델 전달)을 사용했습니다.</span><span class="sxs-lookup"><span data-stu-id="10dad-114">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views when it created the methods and views.</span></span>

<span data-ttu-id="10dad-115">*Controllers/MoviesController.cs* 파일에서 생성된 `Details` 메서드를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="10dad-115">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

<span data-ttu-id="10dad-116">[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_details)]</span><span class="sxs-lookup"><span data-stu-id="10dad-116">[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_details)]</span></span>

<span data-ttu-id="10dad-117">`id` 매개 변수는 일반적으로 경로 데이터 변수로 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="10dad-117">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="10dad-118">예를 들어 `http://localhost:5000/movies/details/1`은 다음을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="10dad-118">For example `http://localhost:5000/movies/details/1` sets:</span></span>

* <span data-ttu-id="10dad-119">`movies` 컨트롤러에 대한 컨트롤러(첫 번째 URL 세그먼트)</span><span class="sxs-lookup"><span data-stu-id="10dad-119">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="10dad-120">`details`에 대한 작업(두 번째 URL 세그먼트)</span><span class="sxs-lookup"><span data-stu-id="10dad-120">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="10dad-121">1에 대한 ID(마지막 URL 세그먼트)</span><span class="sxs-lookup"><span data-stu-id="10dad-121">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="10dad-122">다음과 같이 쿼리 문자열을 사용하여 `id`에 전달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="10dad-122">You can also pass in the `id` with a query string as follows:</span></span>

`http://localhost:1234/movies/details?id=1`

<span data-ttu-id="10dad-123">ID 값이 제공되지 않는 경우에 `id` 매개 변수가 [nullable 형식](https://docs.microsoft.com/dotnet/csharp/programming-guide/nullable-types/index)(`int?`)으로 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="10dad-123">The `id` parameter is defined as a [nullable type](https://docs.microsoft.com/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value is not provided.</span></span>

<span data-ttu-id="10dad-124">[람다 식](https://docs.microsoft.com/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions)을 `SingleOrDefaultAsync`에 전달하여 경로 데이터 또는 쿼리 문자열 값과 일치하는 영화 엔터티를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="10dad-124">A [lambda expression](https://docs.microsoft.com/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `SingleOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .SingleOrDefaultAsync(m => m.ID == id);
```

<span data-ttu-id="10dad-125">영화가 있으면 `Movie` 모델의 인스턴스를 `Details` 뷰에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="10dad-125">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
   ```

<span data-ttu-id="10dad-126">*Views/Movies/Details.cshtml* 파일의 내용을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="10dad-126">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

<span data-ttu-id="10dad-127">[!code-html[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Movies/DetailsOriginal.cshtml)]</span><span class="sxs-lookup"><span data-stu-id="10dad-127">[!code-html[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Movies/DetailsOriginal.cshtml)]</span></span>

<span data-ttu-id="10dad-128">뷰 파일 맨 위에 있는 `@model` 문을 포함하여 뷰에서 필요로 하는 개체의 유형을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="10dad-128">By including a `@model` statement at the top of the view file, you can specify the type of object that the view expects.</span></span> <span data-ttu-id="10dad-129">영화 컨트롤러를 만들 때 Visual Studio에서는 *Details.cshtml* 파일의 맨 위에 다음 `@model` 문을 자동으로 포함했습니다.</span><span class="sxs-lookup"><span data-stu-id="10dad-129">When you created the movie controller, Visual Studio automatically included the following `@model` statement at the top of the *Details.cshtml* file:</span></span>

```HTML
@model MvcMovie.Models.Movie
   ```

<span data-ttu-id="10dad-130">이 `@model` 지시문을 사용하면 강력한 형식인 `Model` 개체를 사용하여 컨트롤러가 뷰에 전달된 영화에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="10dad-130">This `@model` directive allows you to access the movie that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="10dad-131">예를 들어 *Details.cshtml* 뷰에서 코드는 각 영화 필드를 강력한 형식의 `Model` 개체를 사용하여 `DisplayNameFor` 및 `DisplayFor` HTML 도우미에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="10dad-131">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="10dad-132">또한 `Create` 및 `Edit` 메서드 및 뷰는 `Movie` 모델 개체를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="10dad-132">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="10dad-133">영화 컨트롤러에서 *Index.cshtml* 뷰 및 `Index` 메서드를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="10dad-133">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="10dad-134">코드가 `View` 메서드를 호출하는 경우 `List` 개체를 생성하는 방법을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="10dad-134">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="10dad-135">이 코드는 `Index` 작업 메서드의 `Movies` 목록을 뷰에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="10dad-135">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

<span data-ttu-id="10dad-136">[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_index)]</span><span class="sxs-lookup"><span data-stu-id="10dad-136">[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_index)]</span></span>

<span data-ttu-id="10dad-137">영화 컨트롤러를 만들 때 스캐폴딩에서는 *Index.cshtml* 파일의 맨 위에 다음 `@model` 문을 자동으로 포함했습니다.</span><span class="sxs-lookup"><span data-stu-id="10dad-137">When you created the movies controller, scaffolding automatically included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

<span data-ttu-id="10dad-138">[!code-html[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Movies/IndexOriginal.cshtml?range=1)]</span><span class="sxs-lookup"><span data-stu-id="10dad-138">[!code-html[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Movies/IndexOriginal.cshtml?range=1)]</span></span>

<span data-ttu-id="10dad-139">`@model` 지시문을 사용하면 강력한 형식인 `Model` 개체를 사용하여 컨트롤러가 뷰에 전달된 영화 목록에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="10dad-139">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="10dad-140">예를 들어 *Index.cshtml* 뷰에서 코드는 강력한 형식의 `Model` 개체에 `foreach` 문을 포함한 영화를 반복합니다.</span><span class="sxs-lookup"><span data-stu-id="10dad-140">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

<span data-ttu-id="10dad-141">[!code-html[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]</span><span class="sxs-lookup"><span data-stu-id="10dad-141">[!code-html[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]</span></span>

<span data-ttu-id="10dad-142">`Model` 개체가 강력한 형식이기 때문에(`IEnumerable<Movie>` 개체처럼) 루프의 각 항목은 `Movie`으로 입력됩니다.</span><span class="sxs-lookup"><span data-stu-id="10dad-142">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="10dad-143">즉, 여러 가지 이점 중에서 코드를 검사하는 컴파일 시간을 가져올 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="10dad-143">Among other benefits, this means that you get compile-time checking of the code:</span></span>
