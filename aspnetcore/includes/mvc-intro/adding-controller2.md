*Controllers/HelloWorldController.cs*의 내용을 다음으로 바꿉니다.

[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_1)]

컨트롤러의 모든 `public` 메서드는 HTTP 끝점으로 호출할 수 있습니다. 위의 샘플에서 메서드는 모두 문자열을 반환합니다.  각 메서드 앞의 주석을 적어둡니다.

HTTP 끝점은 웹 응용 프로그램에서 대상으로 지정 가능한 URL(예: `http://localhost:1234/HelloWorld`)으로써 사용되는 프로토콜(`HTTP`), 웹 서버(TCP 포트 포함)의 네트워크 위치(`localhost:1234`) 및 대상 URI `HelloWorld`을 결합합니다.

첫 번째 주석은 이 항목을 기본 URL에 "/HelloWorld/"를 추가하여 호출되는 [HTTP GET](http://www.w3schools.com/tags/ref_httpmethods.asp) 메서드라고 설명합니다. 두 번째 주석은 이 항목을 URL에 "/HelloWorld/Welcome/"를 추가하여 호출되는 [HTTP GET](http://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) 메서드를 지정합니다. 나중에 자습서에서 스캐폴딩 엔진을 사용하여 `HTTP POST` 메서드를 생성합니다.

디버그가 아닌 모드로 앱을 실행하고 주소 표시줄의 경로에 "HelloWorld"를 추가합니다. `Index` 메서드가 문자열을 반환합니다.

![브라우저 창은 내 기본 작업입니다.라는 응용 프로그램 응답을 표시합니다.](../../tutorials/first-mvc-app/adding-controller/_static/hell1.png)

MVC는 들어오는 URL에 따라 컨트롤러 클래스(및 그 안에 작업 메서드)를 호출합니다. MVC에서 사용하는 기본 [URL 라우팅 논리](../../mvc/controllers/routing.md)는 다음과 같은 형식을 사용하여 호출할 코드를 결정합니다.

`/[Controller]/[ActionName]/[Parameters]`

*Startup.cs* 파일의 라우팅에 대한 형식을 지정합니다.

[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?name=snippet_1&highlight=5)]

앱을 실행하고 URL 세그먼트를 제공하지 않는 경우 "Home" 컨트롤러에 기본값으로 지정되고 위에 강조 표시된 템플릿 줄에 "Index" 메서드가 지정됩니다.

첫 번째 URL 세그먼트는 실행할 컨트롤러 클래스를 결정합니다. 따라서 `localhost:xxxx/HelloWorld`은 `HelloWorldController` 클래스에 매핑됩니다. URL 세그먼트의 두 번째 부분은 클래스에 대한 작업 메서드를 결정합니다. 따라서 `localhost:xxxx/HelloWorld/Index`는 실행할 `HelloWorldController` 클래스의 `Index` 메서드를 발생시킵니다. `localhost:xxxx/HelloWorld`로 이동하고 기본적으로 `Index` 메서드를 호출해야 합니다. 메서드 이름이 명시적으로 지정되지 않으면 `Index`가 컨트롤러에서 호출되는 기본 메서드이기 때문입니다. URL 세그먼트(`id`)의 세 번째 부분은 경로 데이터입니다. 이 자습서에서는 경로 데이터가 나중에 표시됩니다.

`http://localhost:xxxx/HelloWorld/Welcome`으로 이동합니다. `Welcome` 메서드는 "시작 작업 메서드입니다..."라는 문자열을 실행하고 반환합니다. 이 URL의 경우 컨트롤러는 `HelloWorld`이고 `Welcome`은 작업 메서드입니다. 아직 URL의 `[Parameters]` 부분을 사용하지 않았습니다.

![브라우저 창은 시작 작업 메서드입니다.라는 응용 프로그램 응답을 표시합니다.](../../tutorials/first-mvc-app/adding-controller/_static/welcome.png)

URL의 일부 매개 변수 정보를 컨트롤러에 전달하도록 코드를 수정합니다. 예를 들어, `/HelloWorld/Welcome?name=Rick&numtimes=4`을 입력합니다. `Welcome` 메서드가 다음 코드와 같이 두 개의 매개 변수를 포함하도록 변경합니다. 

[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_2)]

위의 코드:

* C# 선택적 매개 변수 기능을 사용하여 `numTimes` 매개 변수에 대해 전달된 값이 없는 경우 해당 매개 변수의 기본값이 1임을 나타냅니다.
* `HtmlEncoder.Default.Encode`를 사용하여 악의적인 입력(예: JavaScript)에서 응용 프로그램을 보호합니다. 
* [보간된 문자열](https://docs.microsoft.com/dotnet/articles/csharp/language-reference/keywords/interpolated-strings)을 사용합니다.

앱을 실행하고 다음으로 이동합니다.

   `http://localhost:xxxx/HelloWorld/Welcome?name=Rick&numtimes=4`

(xxxx를 포트 번호로 바꿉니다.) URL에서 `name` 및 `numtimes`에 다른 값을 사용할 수 있습니다. MVC [모델 바인딩](../../mvc/models/model-binding.md) 시스템은 주소 표시줄의 쿼리 문자열에서 메서드의 매개 변수로 명명된 매개 변수를 자동으로 매핑합니다. 자세한 정보는 [모델 바인딩](../../mvc/models/model-binding.md)을 참조하세요.

![브라우저 창은 안녕하세요, Rick. NumTimes은 4입니다.라는 응용 프로그램 응답을 표시합니다.](../../tutorials/first-mvc-app/adding-controller/_static/rick4.png)

위의 이미지에서 URL 세그먼트(`Parameters`)를 사용하지 않고 `name` 및 `numTimes` 매개 변수가 [쿼리 문자열](http://en.wikipedia.org/wiki/Query_string)로 전달됩니다. 위 URL에서 `?`(물음표)는 구분 기호이고 쿼리 문자열이 이어집니다. `&` 문자는 쿼리 문자열을 구분합니다.

`Welcome` 메서드를 다음 코드로 바꿉니다.

[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_3)]

앱을 실행하고 다음 URL을 입력합니다.`http://localhost:xxx/HelloWorld/Welcome/3?name=Rick`

![브라우저 창은 안녕하세요, Rick. ID는 3입니다.라는 응용 프로그램 응답을 표시합니다.](../../tutorials/first-mvc-app/adding-controller/_static/rick_routedata.png)

이번에 세 번째 URL 세그먼트는 `id` 경로 매개 변수와 일치합니다. `Welcome` 메서드는 `MapRoute` 메서드에서 URL 템플릿과 일치하는 `id` 매개 변수를 포함합니다. 후행 `?`(`id?`에 위치)는 `id` 매개 변수가 선택 사항임을 나타냅니다.

[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?name=snippet_1&highlight=5)]

이러한 예제에서 컨트롤러는 MVC의 "VC" 부분을 사용했습니다. 즉, 뷰 및 컨트롤러 작업입니다. 컨트롤러는 HTML을 직접 반환합니다. 일반적으로 매우 코딩하고 유지하기에 복잡해지므로 컨트롤러에서 HTML을 직접 반환하지 않으려고 합니다. 대신 일반적으로 별도의 Razor 뷰 템플릿 파일을 사용하여 HTML 응답을 생성하는 데 도움이 됩니다. 다음 자습서에서 해당 작업을 수행합니다.
