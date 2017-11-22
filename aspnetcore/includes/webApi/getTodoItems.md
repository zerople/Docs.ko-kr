[!code-csharp[Main](../../tutorials/first-web-api/sample/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="c21f3-101">위의 코드:</span><span class="sxs-lookup"><span data-stu-id="c21f3-101">The preceding code:</span></span>

* <span data-ttu-id="c21f3-102">빈 컨트롤러 클래스를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="c21f3-102">Defines an empty controller class.</span></span> <span data-ttu-id="c21f3-103">다음 섹션에서는 API를 구현하기 위해 메서드가 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="c21f3-103">In the next sections, methods are added to implement the API.</span></span>
* <span data-ttu-id="c21f3-104">생성자는 [종속성 주입](xref:fundamentals/dependency-injection)을 사용하여 컨트롤러에 데이터베이스 컨텍스트(`TodoContext `)를 삽입할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c21f3-104">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`TodoContext `) into the controller.</span></span> <span data-ttu-id="c21f3-105">컨트롤러의 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 메서드 각각에서 데이터베이스 컨텍스트를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c21f3-105">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="c21f3-106">항목이 없는 경우 생성자는 메모리 내 데이터베이스에 항목을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="c21f3-106">The constructor adds an item to the in-memory database if one doesn't exist.</span></span>

## <a name="getting-to-do-items"></a><span data-ttu-id="c21f3-107">할 일 항목 가져오기</span><span class="sxs-lookup"><span data-stu-id="c21f3-107">Getting to-do items</span></span>

<span data-ttu-id="c21f3-108">할 일 항목을 가져오려면 다음 메서드를 `TodoController` 클래스에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="c21f3-108">To get to-do items, add the following methods to the `TodoController` class.</span></span>

[!code-csharp[Main](../../tutorials/first-web-api/sample/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="c21f3-109">다음과 같은 메서드는 두 개의 GET 메서드를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="c21f3-109">These methods implement the two GET methods:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="c21f3-110">`GetAll` 메서드에 대한 예제 HTTP 응답은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="c21f3-110">Here is an example HTTP response for the `GetAll` method:</span></span>

```
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
   ```

<span data-ttu-id="c21f3-111">자습서의 뒷부분에서는 HTTP 응답을 [Postman](https://www.getpostman.com/) 또는 [curl](https://developer.apple.com/legacy/library/documentation/Darwin/Reference/ManPages/man1/curl.1.html)로 볼 수 있는 방법을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="c21f3-111">Later in the tutorial I'll show how the HTTP response can be viewed with [Postman](https://www.getpostman.com/) or [curl](https://developer.apple.com/legacy/library/documentation/Darwin/Reference/ManPages/man1/curl.1.html).</span></span>

### <a name="routing-and-url-paths"></a><span data-ttu-id="c21f3-112">라우팅 및 URL 경로</span><span class="sxs-lookup"><span data-stu-id="c21f3-112">Routing and URL paths</span></span>

<span data-ttu-id="c21f3-113">`[HttpGet]` 특성은 HTTP GET 메서드를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="c21f3-113">The `[HttpGet]` attribute specifies an HTTP GET method.</span></span> <span data-ttu-id="c21f3-114">각 방법에 대한 URL 경로는 다음과 같이 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="c21f3-114">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="c21f3-115">컨트롤러의 `Route` 특성에서 템플릿 문자열을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c21f3-115">Take the template string in the controller’s `Route` attribute:</span></span>

[!code-csharp[Main](../../tutorials/first-web-api/sample/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="c21f3-116">"[컨트롤러]"를 컨트롤러의 이름으로 바꿉니다. 즉, 컨트롤러 클래스 이름에서 "Controller" 접미사를 뺀 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="c21f3-116">Replace "[Controller]" with the name of the controller, which is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="c21f3-117">이 샘플의 경우 컨트롤러 클래스 이름은 **Todo**Controller이고 루트 이름은 "todo"입니다.</span><span class="sxs-lookup"><span data-stu-id="c21f3-117">For this sample, the controller class name is **Todo**Controller and the root name is "todo".</span></span> <span data-ttu-id="c21f3-118">ASP.NET Core [라우팅](xref:mvc/controllers/routing)은 대/소문자를 구분하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c21f3-118">ASP.NET Core [routing](xref:mvc/controllers/routing) is not case sensitive.</span></span>
* <span data-ttu-id="c21f3-119">`[HttpGet]` 특성에 경로 템플릿(예: `[HttpGet("/products")]`)이 있는 경우 경로에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="c21f3-119">If the `[HttpGet]` attribute has a route template (such as `[HttpGet("/products")]`, append that to the path.</span></span> <span data-ttu-id="c21f3-120">이 샘플은 템플릿을 사용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c21f3-120">This sample doesn't use a template.</span></span> <span data-ttu-id="c21f3-121">자세한 내용은 [Http[동사] 특성을 포함한 라우팅 특성](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c21f3-121">See [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes) for more information.</span></span>

<span data-ttu-id="c21f3-122">`GetById` 메서드에서:</span><span class="sxs-lookup"><span data-stu-id="c21f3-122">In the `GetById` method:</span></span>

[!code-csharp[Main](../../tutorials/first-web-api/sample/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

<span data-ttu-id="c21f3-123">`"{id}"`는 `todo` 항목의 ID에 대한 자리 표시자 변수입니다.</span><span class="sxs-lookup"><span data-stu-id="c21f3-123">`"{id}"` is a placeholder variable for the ID of the `todo` item.</span></span> <span data-ttu-id="c21f3-124">`GetById`가 호출되면 URL의 "{id}" 값을 메서드의 `id` 매개 변수에 할당합니다.</span><span class="sxs-lookup"><span data-stu-id="c21f3-124">When `GetById` is invoked, it assigns the value of "{id}" in the URL to the method's `id` parameter.</span></span>

<span data-ttu-id="c21f3-125">`Name = "GetTodo"`는 명명된 경로를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="c21f3-125">`Name = "GetTodo"` creates a named route.</span></span> <span data-ttu-id="c21f3-126">명명된 경로:</span><span class="sxs-lookup"><span data-stu-id="c21f3-126">Named routes:</span></span>

* <span data-ttu-id="c21f3-127">앱에서 해당 경로 이름을 사용하여 HTTP 링크를 만들도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="c21f3-127">Enable the app to create an HTTP link using the route name.</span></span>
* <span data-ttu-id="c21f3-128">자습서의 뒷부분에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="c21f3-128">Are explained later in the tutorial.</span></span>

### <a name="return-values"></a><span data-ttu-id="c21f3-129">반환 값</span><span class="sxs-lookup"><span data-stu-id="c21f3-129">Return values</span></span>

<span data-ttu-id="c21f3-130">`GetAll` 메서드는 `IEnumerable`을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="c21f3-130">The `GetAll` method returns an `IEnumerable`.</span></span> <span data-ttu-id="c21f3-131">MVC는 자동으로 [JSON](http://www.json.org/)에 개체를 직렬화하고 JSON을 응답 메시지의 본문에 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="c21f3-131">MVC automatically serializes the object to [JSON](http://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="c21f3-132">이 메서드의 응답 코드는 200이며 처리되지 않은 예외가 없다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="c21f3-132">The response code for this method is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="c21f3-133">(처리되지 않은 예외는 5xx 오류로 변환됩니다.)</span><span class="sxs-lookup"><span data-stu-id="c21f3-133">(Unhandled exceptions are translated into 5xx errors.)</span></span>

<span data-ttu-id="c21f3-134">반면, `GetById` 메서드는 다양한 반환 형식을 나타내는 보다 일반적인 `IActionResult` 형식을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="c21f3-134">In contrast, the `GetById` method returns the more general `IActionResult` type, which represents a wide range of return types.</span></span> <span data-ttu-id="c21f3-135">`GetById`에는 두 개의 다른 반환 형식이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c21f3-135">`GetById` has two different return types:</span></span>

* <span data-ttu-id="c21f3-136">요청된 ID와 일치하는 항목이 없는 경우 메서드가 404 오류를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="c21f3-136">If no item matches the requested ID, the method returns a 404 error.</span></span> <span data-ttu-id="c21f3-137">`NotFound`를 반환하면 HTTP 404 응답이 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="c21f3-137">Returning `NotFound` returns an HTTP 404 response.</span></span>

* <span data-ttu-id="c21f3-138">그렇지 않으면 메서드가 JSON 응답 본문에서 200을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="c21f3-138">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="c21f3-139">`ObjectResult`를 반환하면 HTTP 200 응답이 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="c21f3-139">Returning `ObjectResult` returns an HTTP 200 response.</span></span>
