위의 강조 표시된 코드는 [종속성 주입](xref:fundamentals/dependency-injection) 컨테이너에 추가된 영화 데이터베이스 컨텍스트를 표시합니다(*Startup.cs* 파일). `services.AddDbContext<MvcMovieContext>(options =>`는 사용할 데이터베이스 및 연결 문자열을 지정합니다. `=>`은 [람다 연산자](https://docs.microsoft.com/dotnet/articles/csharp/language-reference/operators/lambda-operator)입니다.

*Controllers/MoviesController.cs* 파일을 열고 생성자를 검사합니다.

<!-- l.. Make copy of Movies controller because we comment out the initial index method and update it later  -->

[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_1)] 

생성자는 [종속성 주입](xref:fundamentals/dependency-injection)을 사용하여 컨트롤러에 데이터베이스 컨텍스트(`MvcMovieContext `)를 삽입할 수 있습니다. 컨트롤러의 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 메서드 각각에서 데이터베이스 컨텍스트를 사용합니다.

<a name="strongly-typed-models-keyword-label"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a>강력한 형식의 모델 및 @model 키워드

이 자습서의 앞부분에서 언급했듯이 컨트롤러가 `ViewData` 사전을 사용하여 데이터 또는 개체를 뷰에 전달할 수 있는 방법이 표시되었습니다. `ViewData` 사전은 확인할 정보를 전달하는 편리한 런타임에 바인딩된 방법을 제공하는 동적 개체입니다.

또한 MVC는 확인할 강력한 형식의 모델 개체를 전달하는 기능을 제공합니다. 이 방법을 사용하면 코드를 검사하는 컴파일 시간을 개선할 수 있습니다. 스캐폴딩 메커니즘은 메서드 및 뷰를 만든 경우 `MoviesController` 클래스 및 뷰에서 이 방법(즉, 강력한 형식의 모델 전달)을 사용했습니다.

*Controllers/MoviesController.cs* 파일에서 생성된 `Details` 메서드를 검사합니다.

[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_details)]

`id` 매개 변수는 일반적으로 경로 데이터 변수로 전달됩니다. 예를 들어 `http://localhost:5000/movies/details/1`은 다음을 설정합니다.

* `movies` 컨트롤러에 대한 컨트롤러(첫 번째 URL 세그먼트)
* `details`에 대한 작업(두 번째 URL 세그먼트)
* 1에 대한 ID(마지막 URL 세그먼트)

다음과 같이 쿼리 문자열을 사용하여 `id`에 전달할 수 있습니다.

`http://localhost:1234/movies/details?id=1`

ID 값이 제공되지 않는 경우에 `id` 매개 변수가 [nullable 형식](https://docs.microsoft.com/dotnet/csharp/programming-guide/nullable-types/index)(`int?`)으로 정의됩니다.

[람다 식](https://docs.microsoft.com/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions)을 `SingleOrDefaultAsync`에 전달하여 경로 데이터 또는 쿼리 문자열 값과 일치하는 영화 엔터티를 선택합니다.

```csharp
var movie = await _context.Movie
    .SingleOrDefaultAsync(m => m.ID == id);
```

영화가 있으면 `Movie` 모델의 인스턴스를 `Details` 뷰에 전달합니다.

```csharp
return View(movie);
   ```

*Views/Movies/Details.cshtml* 파일의 내용을 검사합니다.

[!code-html[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Movies/DetailsOriginal.cshtml)]

뷰 파일 맨 위에 있는 `@model` 문을 포함하여 뷰에서 필요로 하는 개체의 유형을 지정할 수 있습니다. 영화 컨트롤러를 만들 때 Visual Studio에서는 *Details.cshtml* 파일의 맨 위에 다음 `@model` 문을 자동으로 포함했습니다.

```HTML
@model MvcMovie.Models.Movie
   ```

이 `@model` 지시문을 사용하면 강력한 형식인 `Model` 개체를 사용하여 컨트롤러가 뷰에 전달된 영화에 액세스할 수 있습니다. 예를 들어 *Details.cshtml* 뷰에서 코드는 각 영화 필드를 강력한 형식의 `Model` 개체를 사용하여 `DisplayNameFor` 및 `DisplayFor` HTML 도우미에 전달합니다. 또한 `Create` 및 `Edit` 메서드 및 뷰는 `Movie` 모델 개체를 전달합니다.

영화 컨트롤러에서 *Index.cshtml* 뷰 및 `Index` 메서드를 검사합니다. 코드가 `View` 메서드를 호출하는 경우 `List` 개체를 생성하는 방법을 확인합니다. 이 코드는 `Index` 작업 메서드의 `Movies` 목록을 뷰에 전달합니다.

[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_index)]

영화 컨트롤러를 만들 때 스캐폴딩에서는 *Index.cshtml* 파일의 맨 위에 다음 `@model` 문을 자동으로 포함했습니다.

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-html[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Movies/IndexOriginal.cshtml?range=1)]

`@model` 지시문을 사용하면 강력한 형식인 `Model` 개체를 사용하여 컨트롤러가 뷰에 전달된 영화 목록에 액세스할 수 있습니다. 예를 들어 *Index.cshtml* 뷰에서 코드는 강력한 형식의 `Model` 개체에 `foreach` 문을 포함한 영화를 반복합니다.

[!code-html[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

`Model` 개체가 강력한 형식이기 때문에(`IEnumerable<Movie>` 개체처럼) 루프의 각 항목은 `Movie`으로 입력됩니다. 즉, 여러 가지 이점 중에서 코드를 검사하는 컴파일 시간을 가져올 수 있습니다.
