*Views/HelloWorld/Index.cshtml* Razor 보기 파일의 콘텐츠를 다음으로 대체합니다.

[!code-HTML[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Index.cshtml)]

`http://localhost:xxxx/HelloWorld`로 이동합니다. `HelloWorldController`에서 `Index` 메서드는 많은 작업을 수행하지 않았습니다. `return View();` 문을 실행했습니다. 해당 문은 메서드가 브라우저에 대한 응답을 렌더링하기 위해 보기 템플릿 파일을 사용해야 함을 지정했습니다. 보기 템플릿 파일의 이름을 명시적으로 지정하지 않았기 때문에 MVC는 */Views/HelloWorld* 폴더에서 *Index.cshtml* 보기 파일을 사용하도록 기본 설정되었습니다. 아래 이미지는 보기에서 하드 코드된 “Hello from our View Template!” 문자열을 보여줍니다.

![브라우저 창](../../tutorials/first-mvc-app/adding-view/_static/hell_template.png)

브라우저 창이 작은 경우(예: 모바일 장치) **홈**, **정보** 및 **연락처** 링크를 보기 위해 오른쪽 위에 있는 [부트스트랩 탐색 단추](http://getbootstrap.com/components/#navbar)를 설정/해제(탭)해야 할 수도 있습니다.

![부트스트랩 탐색 단추가 강조 표시된 브라우저 창](../../tutorials/first-mvc-app/adding-view/_static/1.png)

## <a name="changing-views-and-layout-pages"></a>보기 및 레이아웃 페이지 변경

메뉴 링크를 누릅니다(**MvcMovie**, **홈**, **정보**). 각 페이지는 동일한 메뉴 레이아웃을 표시합니다. 메뉴 레이아웃은 *Views/Shared/_Layout.cshtml* 파일에서 구현됩니다. *Views/Shared/_Layout.cshtml* 파일을 엽니다.

[레이아웃](xref:mvc/views/layout) 템플릿을 사용하면 한 곳에서 사이트의 HTML 컨테이너 레이아웃을 지정한 다음 사이트에서 여러 페이지에 걸쳐 적용할 수 있습니다. `@RenderBody()` 줄을 찾습니다. `RenderBody`는 사용자가 만드는 모든 보기 전용 페이지가 표시되는 자리 표시자이며 레이아웃 페이지에서 *래핑됩니다*. 예를 들어 **정보** 링크를 선택하는 경우 **Views/Home/About.cshtml** 보기는 `RenderBody` 메서드 내에서 렌더링됩니다.

## <a name="change-the-title-and-menu-link-in-the-layout-file"></a>레이아웃 파일의 제목 및 메뉴 링크 변경

제목 요소의 콘텐츠를 변경합니다. 레이아웃 템플릿에서 앵커 텍스트를 "동영상 앱"으로, 컨트롤러를 `Home`에서 아래 강조 표시된 `Movies`로 변경합니다.

참고: ASP.NET Core 2.0 버전은 약간 다릅니다. `@inject ApplicationInsights` 및 `@Html.Raw(JavaScriptSnippet.FullScript)`는 포함하지 않습니다.

[!code-html[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Shared/_Layout.cshtml?highlight=7,31)]

>[!WARNING]
> `Movies` 컨트롤러를 아직 구현하지 않았으므로 해당 링크를 클릭하면 404(찾을 수 없음) 오류가 나타납니다.

변경 내용을 저장하고 **정보** 링크를 누릅니다. 브라우저 탭의 제목에서 **정보 - Mvc 동영상** 대신 **정보 - 동영상 앱**을 표시하는 방법을 확인합니다. **연락처** 링크를 누르고 **동영상 앱**도 표시하는 것을 확인합니다. 레이아웃 템플릿에 변경 사항을 한 번 적용할 수 있었으며 사이트의 모든 페이지에 새 링크 텍스트 및 새 제목을 반영했습니다.

*Views/_ViewStart.cshtml* 파일을 검사합니다.


```HTML
@{
    Layout = "_Layout";
}
```

*Views/_ViewStart.cshtml* 파일에서 각 보기에 대한 *Views/Shared/_Layout.cshtml* 파일을 가져 옵니다. `Layout` 속성을 사용하여 다른 레이아웃 보기를 설정하거나 레이아웃 파일을 사용하지 않도록 `null`로 설정할 수 있습니다.

`Index` 보기의 제목을 변경합니다.

*Views/HelloWorld/Index.cshtml*을 엽니다. 변경을 적용하는 두 군데의 위치가 있습니다.

   * 브라우저의 제목에 표시되는 텍스트
   * 보조 헤더(`<h2>` 요소)

어떤 코드에서 어떤 앱의 부분을 변경하는지 볼 수 있도록 약간 다르게 할 수 있습니다.


```HTML
@{
    ViewData["Title"] = "Movie List";
}

<h2>My Movie List</h2>

<p>Hello from our View Template!</p>
```

위의 코드에서 `ViewData["Title"] = "Movie List";`는 `ViewData` 사전의 `Title` 속성을 "Movie List"로 설정합니다. `Title` 속성은 레이아웃 페이지의 `<title>` HTML 요소에서 사용됩니다.


```HTML
<title>@ViewData["Title"] - Movie App</title>
   ```

변경 내용을 저장하고 `http://localhost:xxxx/HelloWorld`로 이동합니다. 브라우저 제목, 기본 제목 및 작은 제목이 변경된 것을 확인합니다. (브라우저에서 변경 내용을 확인할 수 없는 경우 캐시된 콘텐츠를 보고 있을 수도 있습니다. 브라우저에서 Ctrl+F5 키를 눌러 로드될 서버에서 응답을 강제로 적용합니다.) 브라우저 제목은 *Index.cshtml* 보기 템플릿에서 설정한 `ViewData["Title"]` 및 레이아웃 파일에 추가된 추가 "- Movie App"으로 만들어집니다.

또한 *Index.cshtml* 보기 템플릿에서 콘텐츠가 *Views/Shared/_Layout.cshtml* 보기 템플릿으로 병합되고 단일 HTML 응답이 브라우저로 전송된 방식을 확인합니다. 레이아웃 템플릿을 사용하면 응용 프로그램의 모든 페이지에 걸쳐 적용되는 변경 내용을 쉽게 만들 수 있습니다. 자세한 내용은 [레이아웃](../../mvc/views/layout.md)을 참조하세요.

![동영상 목록 보기](../../tutorials/first-mvc-app/adding-view/_static/hell3.png)

그렇지만 일부 “데이터”(이 경우 “Hello from our View Template!” 메시지)는 하드 코드되었습니다. MVC 응용 프로그램에는 "V"(보기)가 있으며 "C"(컨트롤러)가 있지만 "M"(모델)은 아직 없습니다.

## <a name="passing-data-from-the-controller-to-the-view"></a>컨트롤러에서 보기로 데이터 전달

컨트롤러 작업은 들어오는 URL 요청에 대한 응답으로 호출됩니다. 컨트롤러 클래스는 들어오는 브라우저 요청을 처리하는 코드를 작성하는 위치입니다. 컨트롤러는 데이터 원본에서 데이터를 검색하고 브라우저에 다시 전송할 응답의 유형을 결정합니다. 보기 템플릿은 브라우저에 대한 HTML 응답을 만들고 서식 지정하도록 컨트롤러에서 사용될 수 있습니다.

컨트롤러는 보기 템플릿에서 응답을 렌더링하기 위해 필요한 데이터를 제공할 책임이 있습니다. 모범 사례: 보기 템플릿은 비즈니스 논리를 수행하거나 데이터베이스와 직접 상호 작용하지 **않아야** 합니다. 대신 보기 템플릿은 컨트롤러에 의해 제공되는 데이터와만 작동해야 합니다. 이 "문제의 분리"를 유지 관리하면 코드를 깨끗하고, 테스트 가능하고 유지 관리 가능하게 유지할 수 있습니다.

현재 `HelloWorldController` 클래스에서 `Welcome` 메서드는 `name` 및 `ID` 매개 변수를 가져온 다음 값을 브라우저로 직접 출력합니다. 문자열로 이 응답을 렌더링하는 컨트롤러를 갖는 대신 보기 템플릿을 대신 사용하도록 컨트롤러를 변경해 보겠습니다. 보기 템플릿은 동적 응답을 생성합니다. 즉, 응답을 생성하기 위해 컨트롤러에서 보기로 일부 적절한 데이터를 전달해야 합니다. 보기 템플릿에서 액세스할 수 있도록 보기 템플릿이 `ViewData` 사전에서 필요한 동적 데이터(매개 변수)를 컨트롤러에서 배치하도록 하여 이를 수행할 수 있습니다.

*HelloWorldController.cs* 파일로 돌아가서 `Message` 및 `NumTimes` 값을 `ViewData` 사전에 추가하도록 `Welcome` 메서드를 변경합니다. `ViewData` 사전은 동적 개체입니다. 즉, 원하는 것을 넣을 수 있습니다. 사용자가 어떤 것을 넣을 때까지 `ViewData` 개체에는 정의된 속성이 없습니다. [MVC 모델 바인딩 시스템](xref:mvc/models/model-binding)은 주소 표시줄의 쿼리 문자열에서 메서드의 매개 변수로 명명된 매개 변수(`name` 및 `numTimes`)를 자동으로 매핑합니다. 전체 *HelloWorldController.cs* 파일은 다음과 같습니다.

[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_5)]

`ViewData` 사전 개체는 보기에 전달되는 데이터를 포함합니다. 

*Views/HelloWorld/Welcome.cshtml*이라는 시작 보기 템플릿을 만듭니다.

"Hello" `NumTimes`를 표시하는 *Welcome.cshtml* 보기 템플릿에 반복을 만듭니다. *Views/HelloWorld/Welcome.cshtml*의 콘텐츠를 다음으로 대체합니다.

[!code-html[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Welcome.cshtml)]

변경 내용을 저장하고 다음 URL로 이동합니다.

`http://localhost:xxxx/HelloWorld/Welcome?name=Rick&numtimes=4`

URL에서 데이터를 가져오고 [MVC 모델 바인더](xref:mvc/models/model-binding)를 사용하여 컨트롤러에 전달됩니다. 컨트롤러는 데이터를 `ViewData` 사전으로 패키징하고 해당 개체를 보기에 전달합니다. 그런 다음 보기는 데이터를 HTML로 브라우저에 렌더링합니다.

![시작 레이블 및 네 번 표시되는 구 Hello Rick을 보여 주는 정보 보기](../../tutorials/first-mvc-app/adding-view/_static/rick.png)

위의 샘플에서 `ViewData` 사전을 사용하여 컨트롤러에서 보기로 데이터를 전달했습니다. 자습서의 뒷부분에서 보기 모델을 사용하여 컨트롤러에서 보기로 데이터를 전달합니다. 데이터를 전달하는 보기 모델 방법은 일반적으로 `ViewData` 사전 접근 방법보다 훨씬 많이 사용됩니다. 자세한 내용은 [MVC에서 ViewModel vs ViewData vs ViewBag vs TempData vs Session](http://www.mytecbits.com/microsoft/dot-net/viewmodel-viewdata-viewbag-tempdata-mvc)을 참조하세요.

이는 모델에 대한 일종의 "M"이었지만 데이터베이스 종류는 아니었습니다. 지금까지 학습한 것을 살펴보고 동영상의 데이터베이스를 만들어 보겠습니다.