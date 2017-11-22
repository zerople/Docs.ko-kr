## <a name="implement-the-other-crud-operations"></a><span data-ttu-id="82666-101">기타 CRUD 작업 구현</span><span class="sxs-lookup"><span data-stu-id="82666-101">Implement the other CRUD operations</span></span>

<span data-ttu-id="82666-102">다음 섹션에서 `Create`, `Update` 및 `Delete` 메서드가 컨트롤러에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="82666-102">In the following sections, `Create`, `Update`, and `Delete` methods are added to the controller.</span></span>

### <a name="create"></a><span data-ttu-id="82666-103">만들기</span><span class="sxs-lookup"><span data-stu-id="82666-103">Create</span></span>

<span data-ttu-id="82666-104">다음 `Create` 메서드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="82666-104">Add the following `Create` method.</span></span>

[!code-csharp[Main](../../tutorials/first-web-api/sample/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="82666-105">이전 코드는 [`[HttpPost]`](/aspnet/core/api/microsoft.aspnetcore.mvc.httppostattribute) 특성으로 표시되는 HTTP POST 메서드입니다.</span><span class="sxs-lookup"><span data-stu-id="82666-105">The preceding code is an HTTP POST method, indicated by the [`[HttpPost]`](/aspnet/core/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="82666-106">[`[FromBody]`](/aspnet/core/api/microsoft.aspnetcore.mvc.frombodyattribute) 특성은 HTTP 요청 본문에서 할 일 항목 값을 가져오도록 MVC에 지시합니다.</span><span class="sxs-lookup"><span data-stu-id="82666-106">The [`[FromBody]`](/aspnet/core/api/microsoft.aspnetcore.mvc.frombodyattribute) attribute tells MVC to get the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="82666-107">`CreatedAtRoute` 메서드는 다음과 같은 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="82666-107">The `CreatedAtRoute` method:</span></span>

* <span data-ttu-id="82666-108">201 응답을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="82666-108">Returns a 201 response.</span></span> <span data-ttu-id="82666-109">HTTP 201은 서버에서 새 리소스를 만드는 HTTP POST 메서드의 표준 응답입니다.</span><span class="sxs-lookup"><span data-stu-id="82666-109">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="82666-110">응답에 대한 위치 헤더를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="82666-110">Adds a Location header to the response.</span></span> <span data-ttu-id="82666-111">위치 헤더는 새로 만들어진 할 일 항목의 URI를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="82666-111">The Location header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="82666-112">[10.2.2 201 Created](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)(10.2.2 201 생성됨)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="82666-112">See [10.2.2 201 Created](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="82666-113">“GetTodo”라는 경로를 사용하여 URL을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="82666-113">Uses the "GetTodo" named route to create the URL.</span></span> <span data-ttu-id="82666-114">“GetTodo”라는 경로는 `GetById`에 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="82666-114">The "GetTodo" named route is defined in `GetById`:</span></span>

[!code-csharp[Main](../../tutorials/first-web-api/sample/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="use-postman-to-send-a-create-request"></a><span data-ttu-id="82666-115">Postman을 사용하여 만들기 요청 보내기</span><span class="sxs-lookup"><span data-stu-id="82666-115">Use Postman to send a Create request</span></span>

![Postman 콘솔](../../tutorials/first-web-api/_static/pmc.png)

* <span data-ttu-id="82666-117">HTTP 메서드를 `POST`로 설정</span><span class="sxs-lookup"><span data-stu-id="82666-117">Set the HTTP method to `POST`</span></span>
* <span data-ttu-id="82666-118">**본문** 라디오 단추 선택</span><span class="sxs-lookup"><span data-stu-id="82666-118">Select the **Body** radio button</span></span>
* <span data-ttu-id="82666-119">**원시** 라디오 단추 선택</span><span class="sxs-lookup"><span data-stu-id="82666-119">Select the **raw** radio button</span></span>
* <span data-ttu-id="82666-120">형식을 JSON으로 설정</span><span class="sxs-lookup"><span data-stu-id="82666-120">Set the type to JSON</span></span>
* <span data-ttu-id="82666-121">키 값 편집기에서 다음과 같이 Todo 항목 입력</span><span class="sxs-lookup"><span data-stu-id="82666-121">In the key-value editor, enter a Todo item such as</span></span>

```json
{
    "name":"walk dog",
    "isComplete":true
}
```

* <span data-ttu-id="82666-122">**보내기** 선택</span><span class="sxs-lookup"><span data-stu-id="82666-122">Select **Send**</span></span>
* <span data-ttu-id="82666-123">아래쪽 창에서 [헤더] 탭을 선택하고 **위치** 헤더를 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="82666-123">Select the Headers tab in the lower pane and copy the **Location** header:</span></span>

![Postman 콘솔의 헤더 탭](../../tutorials/first-web-api/_static/pmget.png)

<span data-ttu-id="82666-125">위치 헤더 URI를 사용하여 새 항목에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="82666-125">The Location header URI can be used to access the new item.</span></span>

### <a name="update"></a><span data-ttu-id="82666-126">업데이트</span><span class="sxs-lookup"><span data-stu-id="82666-126">Update</span></span>

<span data-ttu-id="82666-127">다음 `Update` 메서드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="82666-127">Add the following `Update` method:</span></span>

[!code-csharp[Main](../../tutorials/first-web-api/sample/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="82666-128">`Update`는 `Create`와 비슷하지만 HTTP PUT을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="82666-128">`Update` is similar to `Create`, but uses HTTP PUT.</span></span> <span data-ttu-id="82666-129">응답은 [204(콘텐츠 없음)](http://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)입니다.</span><span class="sxs-lookup"><span data-stu-id="82666-129">The response is [204 (No Content)](http://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="82666-130">HTTP 사양에 따라 PUT 요청의 경우 클라이언트는 델타만이 아니라 전체 업데이트된 엔터티를 보내야 합니다.</span><span class="sxs-lookup"><span data-stu-id="82666-130">According to the HTTP spec, a PUT request requires the client to send the entire updated entity, not just the deltas.</span></span> <span data-ttu-id="82666-131">부분 업데이트를 지원하려면 HTTP PATCH를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="82666-131">To support partial updates, use HTTP PATCH.</span></span>

![204(콘텐츠 없음) 응답을 보여주는 Postman 콘솔](../../tutorials/first-web-api/_static/pmcput.png)

### <a name="delete"></a><span data-ttu-id="82666-133">삭제</span><span class="sxs-lookup"><span data-stu-id="82666-133">Delete</span></span>

<span data-ttu-id="82666-134">다음 `Delete` 메서드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="82666-134">Add the following `Delete` method:</span></span>

[!code-csharp[Main](../../tutorials/first-web-api/sample/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="82666-135">`Delete` 응답은 [204(콘텐츠 없음)](http://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)입니다.</span><span class="sxs-lookup"><span data-stu-id="82666-135">The `Delete` response is [204 (No Content)](http://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

<span data-ttu-id="82666-136">`Delete` 테스트:</span><span class="sxs-lookup"><span data-stu-id="82666-136">Test `Delete`:</span></span> 

![204(콘텐츠 없음) 응답을 보여주는 Postman 콘솔](../../tutorials/first-web-api/_static/pmd.png)
