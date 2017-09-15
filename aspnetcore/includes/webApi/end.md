## <a name="implement-the-other-crud-operations"></a>기타 CRUD 작업 구현

`Create`, `Update` 및 `Delete` 메서드를 컨트롤러에 추가합니다. 이러한 메서드는 테마에 대한 변형이므로 코드를 표시하고 주요 차이점을 강조 표시합니다. 코드를 추가하거나 변경한 후 프로젝트를 빌드합니다.

### <a name="create"></a>만들기

[!code-csharp[Main](../../tutorials/first-web-api/sample/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

[`[HttpPost]`](https://docs.asp.net/projects/api/en/latest/autoapi/Microsoft/AspNetCore/Mvc/HttpPostAttribute/index.html) 특성으로 나타내는 HTTP POST 메서드입니다. [`[FromBody]`](https://docs.asp.net/projects/api/en/latest/autoapi/Microsoft/AspNetCore/Mvc/FromBodyAttribute/index.html) 특성은 HTTP 요청 본문에서 할 일 항목 값을 가져오도록 MVC에 지시합니다.

`CreatedAtRoute` 메서드는 서버에서 새 리소스를 만드는 HTTP POST 메서드의 표준 응답인 201 응답을 반환합니다. `CreatedAtRoute`는 응답에 대한 위치 헤더도 추가합니다. 위치 헤더는 새로 만들어진 할 일 항목의 URI를 지정합니다. [10.2.2 201 Created](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)(10.2.2 201 생성됨)를 참조하세요.

### <a name="use-postman-to-send-a-create-request"></a>Postman을 사용하여 만들기 요청 보내기

![Postman 콘솔](../../tutorials/first-web-api/_static/pmc.png)

* HTTP 메서드를 `POST`로 설정
* **본문** 라디오 단추 선택
* **원시** 라디오 단추 선택
* 형식을 JSON으로 설정
* 키 값 편집기에서 다음과 같이 Todo 항목 입력 

```json
{
    "name":"walk dog",
    "isComplete":true
}
```

* **보내기** 선택

* 아래쪽 창에서 [헤더] 탭을 선택하고 **위치** 헤더를 복사합니다.

![Postman 콘솔의 헤더 탭](../../tutorials/first-web-api/_static/pmget.png)

위치 헤더 URI를 사용하여 방금 만든 리소스에 액세스합니다. `GetById` 메서드에서 만들어진 `"GetTodo"` 경로를 회수합니다.

```csharp
[HttpGet("{id}", Name = "GetTodo")]
public IActionResult GetById(long id)
```

### <a name="update"></a>업데이트

[!code-csharp[Main](../../tutorials/first-web-api/sample/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

`Update`는 `Create`와 비슷하지만 HTTP PUT을 사용합니다. 응답은 [204(콘텐츠 없음)](http://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)입니다. HTTP 사양에 따라 PUT 요청의 경우 클라이언트는 델타만이 아니라 전체 업데이트된 엔터티를 보내야 합니다. 부분 업데이트를 지원하려면 HTTP PATCH를 사용합니다.

![204(콘텐츠 없음) 응답을 보여주는 Postman 콘솔](../../tutorials/first-web-api/_static/pmcput.png)

### <a name="delete"></a>삭제

[!code-csharp[Main](../../tutorials/first-web-api/sample/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

응답은 [204(콘텐츠 없음)](http://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)입니다.

![204(콘텐츠 없음) 응답을 보여주는 Postman 콘솔](../../tutorials/first-web-api/_static/pmd.png)
