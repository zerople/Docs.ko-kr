<!--
[!code-html[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Shared/_Layout.cshtml?highlight=7,31)]


[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_1stSearch)]

[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_SearchNull)]

![Index view](../../tutorials/first-mvc-app/search/_static/ghost.png)


[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?highlight=5&name=snippet_1)]

--> 

이전 `Index` 메서드:

[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1,8&name=snippet_1stSearch)]

`id` 매개 변수를 포함한 업데이트된 `Index` 메서드:

[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1,8&name=snippet_SearchID)]

이제 쿼리 문자열 값이 아닌 경로 데이터(URL 세그먼트)로 검색 제목을 전달할 수 있습니다.

![URL에 추가된 단어 ghost와 Ghostbusters 및 Ghostbusters 2라는 두 개의 반환된 영화 목록이 있는 인덱스 보기](../../tutorials/first-mvc-app/search/_static/g2.png)

그러나 사용자가 영화를 검색하려고 할 때마다 URL을 수정하지는 않습니다. 따라서 이제 영화를 필터링하는 데 도움이 되는 UI를 추가합니다. `Index` 메서드의 서명을 변경하여 경로 바인딩 `ID` 매개 변수 전달 방법을 테스트하는 경우 `searchString`이라는 매개 변수를 사용하도록 다시 변경합니다.

[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1&name=snippet_1stSearch)]

*Views/Movies/Index.cshtml* 파일을 열고 아래에서 강조 표시된 `<form>` 표시를 추가합니다.

[!code-HTML[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Movies/IndexForm1.cshtml?highlight=10-16&range=4-21)]

HTML `<form>` 태그는 [양식 태그 도우미](../../mvc/views/working-with-forms.md)를 사용하므로 양식을 제출하는 경우 필터 문자열은 영화 컨트롤러의 `Index` 작업에 게시됩니다. 변경 내용을 저장하고 필터를 테스트합니다.

![제목 필터 텍스트 상자에 단어 ghost를 입력하여 인덱스 보기](../../tutorials/first-mvc-app/search/_static/filter.png)

예상할 수 있는 대로 `Index` 메서드의 `[HttpPost]` 오버로드가 없습니다. 메서드가 앱의 상태를 변경하지 않고 데이터를 필터링하기 때문에 필요하지 않습니다.

다음 `[HttpPost] Index` 메서드를 추가할 수 있습니다.

[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1&name=snippet_SearchPost)]

`notUsed` 매개 변수는 `Index` 메서드의 오버로드를 만드는 데 사용됩니다. 자습서의 뒷부분에서 이에 대해 알아봅니다.

이 메서드를 추가하는 경우 작업 호출자는 `[HttpPost] Index` 메서드와 일치하고 `[HttpPost] Index` 메서드는 아래 이미지에 나와 있는 대로 실행됩니다.

![HttpPost 인덱스에서의 응용 프로그램 응답을 포함한 브라우저 창: ghost에 대한 필터](../../tutorials/first-mvc-app/search/_static/fo.png)

그러나 이 `[HttpPost]` 버전의 `Index` 메서드를 추가하는 경우에도 이를 모두 구현하는 방법은 제한됩니다. 특정 검색을 책갈피로 설정하거나 동일하게 필터링된 영화 목록을 보기 위해 클릭할 수 있는 링크를 친구에게 보내려고 한다고 가정합니다. HTTP POST 요청의 URL은 GET 요청의 URL(localhost:xxxxx/Movies/Index)과 동일합니다. URL에는 검색 정보가 없습니다. 검색 문자열 정보는 [양식 필드 값](https://developer.mozilla.org/docs/Web/Guide/HTML/Forms/Sending_and_retrieving_form_data)으로 서버에 전송됩니다. 브라우저 개발자 도구 또는 뛰어난 [Fiddler 도구](http://www.telerik.com/fiddler)에서 확인할 수 있습니다. 아래 이미지에서는 Chrome 브라우저 개발자 도구를 보여줍니다.

![Microsoft Edge에 있는 개발자 도구의 네트워크 탭은 ghost의 searchString 값을 가진 요청 본문을 보여줍니다.](../../tutorials/first-mvc-app/search/_static/f12_rb.png)

요청 본문에서 검색 매개 변수 및 [XSRF](../../security/anti-request-forgery.md) 토큰을 볼 수 있습니다. 이전 자습서에서 설명했듯이 [양식 태그 도우미](../../mvc/views/working-with-forms.md)는 [XSRF](../../security/anti-request-forgery.md) 위조 방지 토큰을 생성합니다. 컨트롤러 메서드에서 토큰 유효성을 검사할 필요가 없도록 데이터를 수정하지 않습니다.

검색 매개 변수가 URL이 아닌 요청 본문에 있기 때문에 책갈피에 해당 검색 정보를 캡처하거나 다른 사용자와 공유할 수 없습니다. 요청을 `HTTP GET`로 지정하여 이 문제를 해결합니다.