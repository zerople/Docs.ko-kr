# <a name="adding-a-view-to-an-aspnet-core-mvc-app"></a>ASP.NET Core MVC 앱에 뷰 추가

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)

이 섹션에서는 Razor 뷰 템플릿 파일을 사용하도록 `HelloWorldController` 클래스를 수정하여 클라이언트에 HTML 응답을 생성하는 프로세스를 명확하게 캡슐화합니다.

Razor를 사용하여 뷰 템플릿 파일을 만듭니다. Razor 기반 뷰 템플릿에는 *.cshtml* 파일 확장이 있습니다. C#을 사용하여 HTML 출력을 만들 수 있는 세련된 방법을 제공합니다.

현재 `Index` 메서드는 컨트롤러 클래스에서 하드 코딩된 메시지 문자열을 반환합니다. `HelloWorldController` 클래스에서 `Index` 메서드를 다음 코드로 바꿉니다.

[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_4)]

앞의 코드는 `View` 개체를 반환합니다. 브라우저에 HTML 응답을 생성하기 위해 뷰 템플릿을 사용합니다. 위의 `Index` 메서드와 같은 컨트롤러 메서드(동작 메서드라고도 함)는 일반적으로 문자열과 같은 형식이 아닌 [IActionResult](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.iactionresult)(또는 `ActionResult`에서 파생된 클래스)를 반환합니다.
