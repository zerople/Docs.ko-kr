---
title: "검색 추가 | Microsoft 문서"
author: rick-anderson
description: "간단한 ASP.NET 핵심 MVC 응용 프로그램에 검색을 추가 하는 방법을 보여 줍니다."
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 03/07/2017
ms.topic: article
ms.assetid: d69e5529-8ef6-4628-855d-200206d962b9
ms.technology: aspnet
ms.prod: asp.net-core
uid: tutorials/first-mvc-app/search
translationtype: Machine Translation
ms.sourcegitcommit: fe4c3e800bac875f271cce86df52d1d79784d4d3
ms.openlocfilehash: d716874554fe140fa259ed500a56f447c3fc9449
ms.lasthandoff: 03/23/2017

---
# <a name="adding-search"></a>검색 추가

[Rick Anderson](https://twitter.com/RickAndMSFT)

이 섹션에서는 검색 기능을 추가 합니다는 `Index` 작업 메서드를 사용 하면 검색 하 여 영화 *장르* 또는 *이름*합니다.

업데이트는 `Index` 메서드를 다음 코드로 바꿉니다.

[!code-csharp[주](start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_1stSearch)]

첫 번째 줄은 `Index` 작업 메서드를 만듭니다를 [LINQ](http://msdn.microsoft.com/en-us/library/bb397926.aspx) 영화를 선택 하는 쿼리:

```csharp
var movies = from m in _context.Movie
             select m;
```

쿼리는 *만* 가 시점에서 정의 된 **하지** 데이터베이스에 대해 실행 되었습니다.

하는 경우는 `searchString` 매개 변수는 문자열이 포함 된, 영화 쿼리 검색 문자열의 값으로 필터링 하도록 수정 됩니다.

[!code-csharp[주](start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_SearchNull)]

`s => s.Title.Contains()` 위의 코드는 한 [람다 식을](http://msdn.microsoft.com/en-us/library/bb397687.aspx)합니다. 람다 식은 메서드 기반에 사용 되 [LINQ](http://msdn.microsoft.com/en-us/library/bb397926.aspx) 와 같은 표준 쿼리 연산자 메서드의 인수로 쿼리는 [여기서](http://msdn.microsoft.com/en-us/library/system.linq.enumerable.where.aspx) 메서드 또는 `Contains` (위 코드에서 사용 됨). 정의 될 때 또는 같은 메서드를 호출 하 여 수정 된 경우에 LINQ 쿼리 실행 되지 않습니다 `Where`, `Contains` 또는 `OrderBy`합니다. 대신, 쿼리 실행이 지연 됩니다.  즉, 해당 실현된 값 실제로 반복 될 때까지 식의 계산을 지연 또는 `ToListAsync` 메서드를 호출 합니다. 지연 된 쿼리 실행에 대 한 자세한 내용은 참조 [쿼리 실행](http://msdn.microsoft.com/en-us/library/bb738633.aspx)합니다.

참고:는 [Contains](http://msdn.microsoft.com/en-us/library/bb155125.aspx) 위에 표시 된 c# 코드에는 없는 데이터베이스에서 메서드를 실행 합니다. 데이터베이스를 [Contains](http://msdn.microsoft.com/en-us/library/bb155125.aspx) 매핑됩니다 [SQL 같은](http://msdn.microsoft.com/en-us/library/ms179859.aspx), 대/소문자 구분입니다.

이동 `/Movies/Index`합니다. 와 같은 쿼리 문자열을 추가 `?searchString=ghost` url입니다. 필터링 된 영화 표시 됩니다.

![인덱스 보기](search/_static/ghost.png)

서명을 변경 하는 경우는 `Index` 메서드 라는 매개 변수를 사용 하려면 `id`, `id` 매개 변수는 선택적 일치 `{id}` 기본값에 대 한 자리 표시자 경로 집합에 *Startup.cs*합니다.

[!code-csharp[주](start-mvc/sample/MvcMovie/Startup.cs?highlight=5&name=snippet_1)]

신속 하 게 이름을 바꿀 수는 `searchString` 매개 변수를 `id` 와 **이름 바꾸기** 명령입니다. 마우스 오른쪽 단추로 클릭 `searchString` **> 이름 바꾸기**합니다.

![상황에 맞는 메뉴](search/_static/rename.png)

이름 바꾸기 대상은 강조 표시 됩니다.

![인덱스 ActionResult 메서드 전체에서 강조 표시 된 변수를 보여 주는 코드 편집기](search/_static/rename2.png)

매개 변수를 변경 `id` 의 모든 항목 및 `searchString` 변경 `id`합니다.

![코드 편집기에서 변수를 보여 주는 id로 변경 되었습니다.](search/_static/rename3.png)

이전 `Index` 메서드:

[!code-csharp[주](start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1,8&name=snippet_1stSearch)]

업데이트 된 `Index` 메서드:

[!code-csharp[주](start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1,8&name=snippet_SearchID)]

이제 제목 검색을 데이터로 경로 (URL 세그먼트) 대신 쿼리 문자열 값으로 전달할 수 있습니다.

![Url과 두 개의 동영상 Ghostbusters 및 Ghostbusters 2의 반환 된 영화 목록에 추가 된 단어 유령 인덱스 보기](search/_static/g2.png)

그러나 사용자가 동영상을 검색할 때마다 URL을 수정할 수 것으로 기대할 수 없습니다. 이제 영화를 필터링 하는 데 도움이 되는 UI를 추가 합니다. 시그니처를 변경한 경우에 `Index` 바인딩 경로 전달 하는 방법을 테스트 하려면 `ID` 매개 변수를 명명 된 매개 변수를 다시 변경해 `searchString`:

[!code-csharp[주](start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1&name=snippet_1stSearch)]

열기는 *Views/Movies/Index.cshtml* 파일을 열고 추가 `<form>` 태그 아래에 강조 표시 합니다.

[!code-HTML[주](start-mvc/sample/MvcMovie/Views/Movies/IndexForm1.cshtml?highlight=11,12,13,14,15,16&range=4-21)]

HTML `<form>` 태그를 사용 하 여는 [Form 태그 도우미](../../mvc/views/working-with-forms.md)이므로 폼을 제출 하면 필터 문자열에 게시 되는 `Index` 영화 컨트롤러의 동작입니다. 변경 내용을 저장 하 고 필터를 테스트 합니다.

![제목 필터 텍스트 상자에 입력 한 단어 유령 인덱스 보기](search/_static/filter.png)

없는 `[HttpPost]` 오버 로드는 `Index` 메서드를 예상 합니다. 필요 하지 않은, 메서드는 앱의 상태를 변경 하지 때문에 데이터를 필터링 하는 것입니다.

다음을 추가할 수 있습니다 `[HttpPost] Index` 메서드.

[!code-csharp[주](start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1&name=snippet_SearchPost)]

`notUsed` 매개 변수 오버 로드를 만드는 데 사용 되는 `Index` 메서드. 이 자습서의 뒷부분에 나오는 하는 방법에 대 한 이야기 하겠습니다.

작업 호출자 일치를 추가한 경우이 메서드는 `[HttpPost] Index` 메서드 및 `[HttpPost] Index` 메서드는 아래 그림에 표시 된 대로 실행 됩니다.

![브라우저 창에 HttpPost Index에서의 응용 프로그램 응답: 삭제할 레코드에 대 한 필터](search/_static/fo.png)

그러나이 추가 하는 경우에 `[HttpPost]` 버전의는 `Index` 메서드를 어떻게이 모든 구현 되었는지에 대 한 제한이 있습니다. 책갈피는 특정 검색 하거나 보내려는 링크 친구에 게 영화 동일한 필터링 된 목록을 보려면 클릭 하면 가정해 보십시오. HTTP POST에 대 한 URL 요청 하는 GET 요청 (localhost:xxxxx/영화/인덱스)에 대 한 URL과 동일-URL에는 검색 정보가 없습니다. 검색 문자열 정보로 서버에 전송 되는 [필드 값을 형성](https://developer.mozilla.org/en-US/docs/Web/Guide/HTML/Forms/Sending_and_retrieving_form_data)합니다. 사용 중인지를 확인할 수는 [F12 개발자 도구](https://dev.windows.com/en-us/microsoft-edge/platform/documentation/f12-devtools-guide/) 는 유용한 또는 [Fiddler 도구](http://www.telerik.com/fiddler)합니다. 시작 된 [F12 도구](https://dev.windows.com/en-us/microsoft-edge/platform/documentation/f12-devtools-guide/):

탭은 **http://localhost:xxx/영화 HTTP POST 200** 줄을 누른 다음 **본문이 > 요청 본문**합니다.

![삭제할 레코드의 searchString 값으로는 요청 본문을 보여 주는 Microsoft Edge에 대 한 개발자 도구의 네트워크 탭](search/_static/f12_rb.png)

검색 매개 변수를 볼 수 및 [XSRF](../../security/anti-request-forgery.md) 요청 본문에 토큰입니다. 이전 자습서에서 설명 했 듯이 참고는 [Form 태그 도우미](../../mvc/views/working-with-forms.md) 생성는 [XSRF](../../security/anti-request-forgery.md) 위조 방지 토큰입니다. 우리는 수정 하지 데이터 컨트롤러 메서드에서 토큰 유효성을 검사할 필요가 없습니다.

검색 매개 변수는 요청 본문 및 URL이 아닌는 이기 때문에 책갈피를 검색 정보 캡처 방법이 나 다른 사용자와 공유할 수 없습니다. 이 수정할 차례 요청 수를 지정 하 여 `HTTP GET`합니다. 어떻게 intelliSense를 사용 하면 태그를 업데이트 하는 데 알 수 있습니다.

![Form 요소에 대 한 특성의 목록에서 선택한 메서드로 Intellisense 상황에 맞는 메뉴](search/_static/int_m.png)

![Intellisense 메서드 특성 값의 목록에서 선택 된 상황에 맞는 메뉴](search/_static/int_get.png)

고유한 글꼴을 알 수는 `<form>` 태그입니다. 구별 되는 글꼴 태그에서 지원 되는지 나타냅니다 [태그 도우미](../../mvc/views/tag-helpers/intro.md)합니다.

![자주색 텍스트와 함께 form 태그](search/_static/th_font.png)

이제 검색을 제출할 때 URL 검색 쿼리 문자열을 포함 합니다. 검색도 이동 되는 `HttpGet Index` 있는 경우에 작업 메서드는 `HttpPost Index` 메서드.

![브라우저 창에 표시 된 searchString 고스트 = Url과 Ghostbusters과 Ghostbusters 2를 반환 하 고, 영화에서 단어 삭제할 레코드를 포함 합니다.](search/_static/search_get.png)

다음 태그 표시 하 여 변경 내용을 `form` 태그:

```html
<form asp-controller="Movies" asp-action="Index" method="get">
   ```

## <a name="adding-search-by-genre"></a>장르별로 검색 추가

다음 코드를 추가 `MovieGenreViewModel` 클래스는 *모델* 폴더:

[!code-csharp[주](start-mvc/sample/MvcMovie/Models/MovieGenreViewModel.cs)]

영화 장르 보기 모델에 포함 됩니다.

   * 동영상의 목록이 됩니다.
   * A `SelectList` 장르 목록을 포함 합니다. 이렇게 하면 사용자가 목록에서 장르를 선택할 수 있습니다.
   * `movieGenre`선택된 하는 장르를 포함 하는 합니다.

대체는 `Index` 메서드에서 `MoviesController.cs` 를 다음 코드로:

[!code-csharp[주](start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_SearchGenre)]

다음 코드는 한 `LINQ` 데이터베이스에서 모든 장르를 검색 하는 쿼리입니다.

[!code-csharp[주](start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_LINQ)]

`SelectList` 장르 (않으려고 중복 장르를 select 목록) 고유 장르를 투영 하 여 생성 됩니다.

```csharp
movieGenreVM.genres = new SelectList(await genreQuery.Distinct().ToListAsync())
   ```

## <a name="adding-search-by-genre-to-the-index-view"></a>인덱스 뷰에 장르별로 검색 추가

업데이트 `Index.cshtml` 다음과 같습니다.

[!code-HTML[주](start-mvc/sample/MvcMovie/Views/Movies/IndexFormGenreNoRating.cshtml?highlight=1,15,16,17,28,31,34,37)]

장르, 영화 제목 및 모두 사용 하 여 검색 하 여 앱을 테스트 합니다.

>[!div class="step-by-step"]
[이전](controller-methods-views.md)
[다음](new-field.md)  

