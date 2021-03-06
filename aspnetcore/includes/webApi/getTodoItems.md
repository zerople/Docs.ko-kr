[!code-csharp[Main](../../tutorials/first-web-api/sample/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

위의 코드:

* 빈 컨트롤러 클래스를 정의합니다. 다음 섹션에서는 API를 구현하기 위해 메서드가 추가됩니다.
* 생성자는 [종속성 주입](xref:fundamentals/dependency-injection)을 사용하여 컨트롤러에 데이터베이스 컨텍스트(`TodoContext `)를 삽입할 수 있습니다. 컨트롤러의 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 메서드 각각에서 데이터베이스 컨텍스트를 사용합니다.
* 항목이 없는 경우 생성자는 메모리 내 데이터베이스에 항목을 추가합니다.

## <a name="getting-to-do-items"></a>할 일 항목 가져오기

할 일 항목을 가져오려면 다음 메서드를 `TodoController` 클래스에 추가합니다.

[!code-csharp[Main](../../tutorials/first-web-api/sample/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

다음과 같은 메서드는 두 개의 GET 메서드를 구현합니다.

* `GET /api/todo`
* `GET /api/todo/{id}`

`GetAll` 메서드에 대한 예제 HTTP 응답은 다음과 같습니다.

```
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
   ```

자습서의 뒷부분에서는 HTTP 응답을 [Postman](https://www.getpostman.com/) 또는 [curl](https://developer.apple.com/legacy/library/documentation/Darwin/Reference/ManPages/man1/curl.1.html)로 볼 수 있는 방법을 설명합니다.

### <a name="routing-and-url-paths"></a>라우팅 및 URL 경로

`[HttpGet]` 특성은 HTTP GET 메서드를 지정합니다. 각 방법에 대한 URL 경로는 다음과 같이 구성됩니다.

* 컨트롤러의 `Route` 특성에서 템플릿 문자열을 사용합니다.

[!code-csharp[Main](../../tutorials/first-web-api/sample/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* "[컨트롤러]"를 컨트롤러의 이름으로 바꿉니다. 즉, 컨트롤러 클래스 이름에서 "Controller" 접미사를 뺀 이름입니다. 이 샘플의 경우 컨트롤러 클래스 이름은 **Todo**Controller이고 루트 이름은 "todo"입니다. ASP.NET Core [라우팅](xref:mvc/controllers/routing)은 대/소문자를 구분하지 않습니다.
* `[HttpGet]` 특성에 경로 템플릿(예: `[HttpGet("/products")]`)이 있는 경우 경로에 추가합니다. 이 샘플은 템플릿을 사용하지 않습니다. 자세한 내용은 [Http[동사] 특성을 포함한 라우팅 특성](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)을 참조하세요.

`GetById` 메서드에서:

[!code-csharp[Main](../../tutorials/first-web-api/sample/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

`"{id}"`는 `todo` 항목의 ID에 대한 자리 표시자 변수입니다. `GetById`가 호출되면 URL의 "{id}" 값을 메서드의 `id` 매개 변수에 할당합니다.

`Name = "GetTodo"`는 명명된 경로를 만듭니다. 명명된 경로:

* 앱에서 해당 경로 이름을 사용하여 HTTP 링크를 만들도록 설정합니다.
* 자습서의 뒷부분에서 설명합니다.

### <a name="return-values"></a>반환 값

`GetAll` 메서드는 `IEnumerable`을 반환합니다. MVC는 자동으로 [JSON](http://www.json.org/)에 개체를 직렬화하고 JSON을 응답 메시지의 본문에 기록합니다. 이 메서드의 응답 코드는 200이며 처리되지 않은 예외가 없다고 가정합니다. (처리되지 않은 예외는 5xx 오류로 변환됩니다.)

반면, `GetById` 메서드는 다양한 반환 형식을 나타내는 보다 일반적인 `IActionResult` 형식을 반환합니다. `GetById`에는 두 개의 다른 반환 형식이 있습니다.

* 요청된 ID와 일치하는 항목이 없는 경우 메서드가 404 오류를 반환합니다. `NotFound`를 반환하면 HTTP 404 응답이 반환됩니다.

* 그렇지 않으면 메서드가 JSON 응답 본문에서 200을 반환합니다. `ObjectResult`를 반환하면 HTTP 200 응답이 반환됩니다.
