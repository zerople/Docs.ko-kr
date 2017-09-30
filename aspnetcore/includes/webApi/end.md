## <a name="implement-the-other-crud-operations"></a><span data-ttu-id="74a7d-101">기타 CRUD 작업 구현</span><span class="sxs-lookup"><span data-stu-id="74a7d-101">Implement the other CRUD operations</span></span>

<span data-ttu-id="74a7d-102">`Create`, `Update` 및 `Delete` 메서드를 컨트롤러에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="74a7d-102">We'll add `Create`, `Update`, and `Delete` methods to the controller.</span></span> <span data-ttu-id="74a7d-103">이러한 메서드는 테마에 대한 변형이므로 코드를 표시하고 주요 차이점을 강조 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="74a7d-103">These are variations on a theme, so I'll just show the code and highlight the main differences.</span></span> <span data-ttu-id="74a7d-104">코드를 추가하거나 변경한 후 프로젝트를 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="74a7d-104">Build the project after adding or changing code.</span></span>

### <a name="create"></a><span data-ttu-id="74a7d-105">만들기</span><span class="sxs-lookup"><span data-stu-id="74a7d-105">Create</span></span>

[!code-csharp[Main](../../tutorials/first-web-api/sample/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="74a7d-106">[`[HttpPost]`](/aspnet/core/api/microsoft.aspnetcore.mvc.httppostattribute) 특성으로 나타내는 HTTP POST 메서드입니다.</span><span class="sxs-lookup"><span data-stu-id="74a7d-106">This is an HTTP POST method, indicated by the [`[HttpPost]`](/aspnet/core/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="74a7d-107">[`[FromBody]`](/aspnet/core/api/microsoft.aspnetcore.mvc.frombodyattribute) 특성은 HTTP 요청 본문에서 할 일 항목 값을 가져오도록 MVC에 지시합니다.</span><span class="sxs-lookup"><span data-stu-id="74a7d-107">The [`[FromBody]`](/aspnet/core/api/microsoft.aspnetcore.mvc.frombodyattribute) attribute tells MVC to get the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="74a7d-108">`CreatedAtRoute` 메서드는 서버에서 새 리소스를 만드는 HTTP POST 메서드의 표준 응답인 201 응답을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="74a7d-108">The `CreatedAtRoute` method returns a 201 response, which is the standard response for an HTTP POST method that creates a new resource on the server.</span></span> <span data-ttu-id="74a7d-109">`CreatedAtRoute`는 응답에 대한 위치 헤더도 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="74a7d-109">`CreatedAtRoute` also adds a Location header to the response.</span></span> <span data-ttu-id="74a7d-110">위치 헤더는 새로 만들어진 할 일 항목의 URI를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="74a7d-110">The Location header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="74a7d-111">[10.2.2 201 Created](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)(10.2.2 201 생성됨)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="74a7d-111">See [10.2.2 201 Created](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>

### <a name="use-postman-to-send-a-create-request"></a><span data-ttu-id="74a7d-112">Postman을 사용하여 만들기 요청 보내기</span><span class="sxs-lookup"><span data-stu-id="74a7d-112">Use Postman to send a Create request</span></span>

![Postman 콘솔](../../tutorials/first-web-api/_static/pmc.png)

* <span data-ttu-id="74a7d-114">HTTP 메서드를 `POST`로 설정</span><span class="sxs-lookup"><span data-stu-id="74a7d-114">Set the HTTP method to `POST`</span></span>
* <span data-ttu-id="74a7d-115">**본문** 라디오 단추 선택</span><span class="sxs-lookup"><span data-stu-id="74a7d-115">Select the **Body** radio button</span></span>
* <span data-ttu-id="74a7d-116">**원시** 라디오 단추 선택</span><span class="sxs-lookup"><span data-stu-id="74a7d-116">Select the **raw** radio button</span></span>
* <span data-ttu-id="74a7d-117">형식을 JSON으로 설정</span><span class="sxs-lookup"><span data-stu-id="74a7d-117">Set the type to JSON</span></span>
* <span data-ttu-id="74a7d-118">키 값 편집기에서 다음과 같이 Todo 항목 입력</span><span class="sxs-lookup"><span data-stu-id="74a7d-118">In the key-value editor, enter a Todo item such as</span></span> 

```json
{
    "name":"walk dog",
    "isComplete":true
}
```

* <span data-ttu-id="74a7d-119">**보내기** 선택</span><span class="sxs-lookup"><span data-stu-id="74a7d-119">Select **Send**</span></span>

* <span data-ttu-id="74a7d-120">아래쪽 창에서 [헤더] 탭을 선택하고 **위치** 헤더를 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="74a7d-120">Select the Headers tab in the lower pane and copy the **Location** header:</span></span>

![Postman 콘솔의 헤더 탭](../../tutorials/first-web-api/_static/pmget.png)

<span data-ttu-id="74a7d-122">위치 헤더 URI를 사용하여 방금 만든 리소스에 액세스합니다.</span><span class="sxs-lookup"><span data-stu-id="74a7d-122">You can use the Location header URI to access the resource you just created.</span></span> <span data-ttu-id="74a7d-123">`GetById` 메서드에서 만들어진 `"GetTodo"` 경로를 회수합니다.</span><span class="sxs-lookup"><span data-stu-id="74a7d-123">Recall the `GetById` method created the `"GetTodo"` named route:</span></span>

```csharp
[HttpGet("{id}", Name = "GetTodo")]
public IActionResult GetById(long id)
```

### <a name="update"></a><span data-ttu-id="74a7d-124">업데이트</span><span class="sxs-lookup"><span data-stu-id="74a7d-124">Update</span></span>

[!code-csharp[Main](../../tutorials/first-web-api/sample/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="74a7d-125">`Update`는 `Create`와 비슷하지만 HTTP PUT을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="74a7d-125">`Update` is similar to `Create`, but uses HTTP PUT.</span></span> <span data-ttu-id="74a7d-126">응답은 [204(콘텐츠 없음)](http://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)입니다.</span><span class="sxs-lookup"><span data-stu-id="74a7d-126">The response is [204 (No Content)](http://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="74a7d-127">HTTP 사양에 따라 PUT 요청의 경우 클라이언트는 델타만이 아니라 전체 업데이트된 엔터티를 보내야 합니다.</span><span class="sxs-lookup"><span data-stu-id="74a7d-127">According to the HTTP spec, a PUT request requires the client to send the entire updated entity, not just the deltas.</span></span> <span data-ttu-id="74a7d-128">부분 업데이트를 지원하려면 HTTP PATCH를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="74a7d-128">To support partial updates, use HTTP PATCH.</span></span>

![204(콘텐츠 없음) 응답을 보여주는 Postman 콘솔](../../tutorials/first-web-api/_static/pmcput.png)

### <a name="delete"></a><span data-ttu-id="74a7d-130">삭제</span><span class="sxs-lookup"><span data-stu-id="74a7d-130">Delete</span></span>

[!code-csharp[Main](../../tutorials/first-web-api/sample/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="74a7d-131">응답은 [204(콘텐츠 없음)](http://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)입니다.</span><span class="sxs-lookup"><span data-stu-id="74a7d-131">The response is [204 (No Content)](http://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

![204(콘텐츠 없음) 응답을 보여주는 Postman 콘솔](../../tutorials/first-web-api/_static/pmd.png)
