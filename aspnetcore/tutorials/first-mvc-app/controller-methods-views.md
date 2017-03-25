---
title: "컨트롤러 메서드와 뷰 | Microsoft 문서"
author: rick-anderson
description: "컨트롤러 메서드를, 뷰 및 DataAnnotations 작업"
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 03/07/2017
ms.topic: article
ms.assetid: c7313211-b271-4adf-bab8-8e72603cc0ce
ms.technology: aspnet
ms.prod: asp.net-core
uid: tutorials/first-mvc-app/controller-methods-views
translationtype: Machine Translation
ms.sourcegitcommit: 6d6bda047da2436c97b6153fa62688946dafc62c
ms.openlocfilehash: 050cbfe2afe2913bc031820a4ebfdaa22d1204d5
ms.lasthandoff: 03/23/2017

---

# <a name="controller-methods-and-views"></a>컨트롤러 메서드와 뷰

[Rick Anderson](https://twitter.com/RickAndMSFT)

동영상 앱을 시작할 있지만 프레젠테이션을 바람직하지 않습니다. 시간 (오전&12;시:&00; 아래 이미지에서)를 참조 하지 않으려고 및 **ReleaseDate** 두 단어 이어야 합니다.

![Index 뷰: 출시 날짜는 한 단어 (공백 없음) 및 모든 동영상 릴리스 날짜의 오전 12 시간을 보여 줍니다.](working-with-sql/_static/m55.png)

열기는 *Models/Movie.cs* 파일을 강조 표시 된 아래에 표시 된 줄을 추가 합니다.

<!-- next update, create MovieDateWithExtraUsings that has all the using we remove
in the next step 
-->
[!code-csharp[주](start-mvc/sample/MvcMovie/Models/MovieDate.cs?name=snippet_1&highlight=11-12)]

빨간색의 구불구불한 선은 마우스 오른쪽 단추로 클릭 **> 빠른 작업 및 리팩터링을**합니다.

  ![상황에 맞는 메뉴에서는 * * > 빠른 작업 및 리팩터링을 * *.](controller-methods-views/_static/qa.png)


Tap`using System.ComponentModel.DataAnnotations;`

  ![System.ComponentModel.DataAnnotations를 사용 하 여 목록의 위쪽에](controller-methods-views/_static/da.png)

  Visual studio 추가 `using System.ComponentModel.DataAnnotations;`합니다.

제거는 `using` 필요 하지 않은 문입니다. 나타납니까 밝은 회색 글꼴로 기본적으로 합니다. 아무 곳 이나 클릭 마우스 오른쪽 단추로 *Movie.cs* 파일 **> 제거 및 정렬**합니다.

![제거 및 정렬](controller-methods-views/_static/rm.png)

업데이트 된 코드:

[!code-csharp[주](./start-mvc/sample/MvcMovie/Models/MovieDate.cs?name=snippet_1)]

다루며 [DataAnnotations](http://msdn.microsoft.com/library/system.componentmodel.dataannotations.aspx) 다음 자습서에서입니다. [표시](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.displayattribute.aspx) 특성 (이 경우 "ReleaseDate" 대신 "릴리스 날짜")에 있는 필드의 이름에 대해 표시할 대상을 지정 합니다. [DataType](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.datatypeattribute.aspx) 특성 필드에 저장 된 시간 정보가 표시 되지 (Date)와 데이터의 형식을 지정 합니다.

이동 하는 `Movies` 컨트롤러 위에 마우스 포인터와 **편집** 대상 URL을 보려면이 링크를 합니다.

![브라우저 창에 편집 링크 및 링크 위로 마우스를 가져갑니다 http://localhost:1234/영화/편집/5의 Url 표시](controller-methods-views/_static/edit7.png)

**편집**, **세부 정보**, 및 **삭제** 링크에서 MVC 코어 앵커 태그 도우미에서 생성 되는 *Views/Movies/Index.cshtml* 파일입니다.

[!code-HTML[주](start-mvc/sample/MvcMovie/Views/Movies/IndexOriginal.cshtml?highlight=1-3&range=46-50)]

[태그 도우미](xref:mvc/views/tag-helpers/intro) 만들기 및 Razor 파일의 요소를 HTML 렌더링에 참여 하는 서버 쪽 코드를 사용 하도록 설정 합니다. 위의 코드에는 `AnchorTagHelper` HTML을 동적으로 생성 `href` 컨트롤러 작업의 메서드 및 경로 id에서 특성 값입니다. 사용 하면 **소스 보기** 즐겨 찾는 브라우저 또는 사용 하 여 프로그램에서 **F12** 생성된 된 태그를 검사 하는 도구입니다. **F12** 도구는 다음과 같습니다.

![DOM 탐색기의 개발자 도구 Microsoft Edge 보여 주는 href의 특성 값을 편집, 세부 정보에 대 한 앵커 태그 도우미에 의해 생성 된 및 링크 삭제](controller-methods-views/_static/f12.png)

형식은 회수 [라우팅](xref:mvc/controllers/routing) 에 설정 된 *Startup.cs* 파일:

[!code-csharp[주](start-mvc/sample/MvcMovie/Startup.cs?name=snippet_1&highlight=5)]

ASP.NET Core 변환 `http://localhost:1234/Movies/Edit/4` 에 대 한 요청에는 `Edit` 작업 메서드는 `Movies` 매개 변수를 사용 하 여 컨트롤러 `Id` 4입니다. (컨트롤러 메서드 다 라고도 작업 메서드)

[태그 도우미](xref:mvc/views/tag-helpers/intro) 는 ASP.NET 코어의 가장 인기 있는 새로운 기능 중 하나입니다. 참조 [추가 리소스](#additional-resources) 에 대 한 자세한 내용은 합니다.

열기는 `Movies` 컨트롤러를 두 개의 검사 `Edit` 작업 메서드. 다음 코드는 `HTTP GET Edit` 메서드에서 어떤 fetchs 동영상 편집 양식에서 생성 된 정보를 표시 하 고는 *Edit.cshtml* Razor 파일입니다.

[!code-csharp[주](start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit1)]

다음 코드는 `HTTP POST Edit` 메서드에서 프로세스는 게시 된 동영상 값:

[!code-csharp[주](start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit2)]

`[Bind]` 특성은 으로부터 보호 하기 위해 한 가지 방법은 [과도 한 게시](https://docs.microsoft.com/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application#overpost)합니다. 속성에 포함 해야는 `[Bind]` 특성을 변경 합니다. 참조 [과도 하 게 게시에서 컨트롤러를 보호](http://www.asp.net/mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application#overpost) 에 대 한 자세한 내용은 합니다. [Viewmodel](http://rachelappel.com/use-viewmodels-to-manage-data-amp-organize-code-in-asp-net-mvc-applications/) 과도 하 게 게시를 방지 하기 위해 대체 방법을 제공 합니다.

두 번째 확인 `Edit` 작업 메서드는 앞에 `[HttpPost]` 특성입니다.

[!code-csharp[주](start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit2&highlight=4)]

`HttpPost` 특성을 지정 하는이 `Edit` 메서드를 호출할 수 *만* 에 대 한 `POST` 요청 합니다. 적용할 수 있습니다는 `[HttpGet]` 특성을 첫 번째 메서드를 편집 하지만 필요가 없기 때문에 `[HttpGet]` 값이 기본값입니다.

`ValidateAntiForgeryToken` 특성으로 사용 됩니다. [요청 위조 방지](xref:security/anti-request-forgery) 편집 뷰 파일에서 생성 된 위조 방지 토큰 이룹니다 및 (*Views/Movies/Edit.cshtml*). 파일 편집 보기를 사용 하 여 위조 방지 토큰 생성은 [Form 태그 도우미](xref:mvc/views/working-with-forms)합니다.

[!code-HTML[주](start-mvc/sample/MvcMovie/Views/Movies/Edit.cshtml?range=9)]

[Form 태그 도우미](xref:mvc/views/working-with-forms) 일치 해야 하는 숨겨진된 위조 방지 토큰을 생성 하는 `[ValidateAntiForgeryToken]` 에서 위조 방지 토큰 생성의 `Edit` 영화 컨트롤러의 메서드. 자세한 내용은 참조 [백신 요청 위조](xref:security/anti-request-forgery)합니다.

`HttpGet Edit` 메서드는 영화 `ID` 매개 변수를 Entity Framework를 사용 하 여 영화를 조회 `SingleOrDefaultAsync` 메서드를 편집 뷰로 선택한 동영상을 반환 합니다. 영화를 찾을 수 없는 경우 `NotFound` (HTTP 404)가 반환 됩니다.

[!code-csharp[주](start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit1)]

스 캐 폴딩 시스템 편집 뷰를 만들 때 검사는 `Movie` 클래스 및 생성된 된 렌더링 하는 코드 `<label>` 및 `<input>` 클래스의 각 속성에 대 한 요소입니다. 다음 예제에서는 Visual Studio 스 캐 폴딩 시스템에서 생성 된 편집 뷰를 보여 줍니다.

[!code-HTML[주](start-mvc/sample/MvcMovie/Views/Movies/EditCopy.cshtml?highlight=1)]

템플릿 보기에는 어떻게는 `@model MvcMovie.Models.Movie` 파일 맨 위에 있는 문. `@model MvcMovie.Models.Movie`보기 형식으로 템플릿 보기에 대 한 모델 필요 함을 지정 `Movie`합니다.

스 캐 폴드 된 코드는 HTML 태그를 간소화 하도록 몇 가지 태그 도우미 메서드를 사용 합니다. - [레이블 태그 도우미](xref:mvc/views/working-with-forms) 필드 (예: "Title", "ReleaseDate", "장르" 또는 "Price")의 이름을 표시 합니다. [입력 태그 도우미](xref:mvc/views/working-with-forms) 는 html `<input>` 요소입니다. [유효성 검사 태그 도우미](xref:mvc/views/working-with-forms) 해당 속성과 관련 된 모든 유효성 검사 메시지를 표시 합니다.

응용 프로그램을 실행 하 고 이동은 `/Movies` URL입니다. 클릭 된 **편집** 링크 합니다. 브라우저에서 페이지에 대 한 원본을 봅니다. 에 대 한 생성 된 HTML의 `<form>` 요소는 다음과 같습니다.

[!code-HTML[주](start-mvc/sample/MvcMovie/Views/Shared/edit_view_source.html?highlight=1,6,10,17,24,28)]

`<input>` 요소가 되는 `HTML <form>` 요소 인 `action` 특성에 게시 하도록 설정 되어는 `/Movies/Edit/id` URL입니다. 양식 데이터 서버에 게시 될 때의 `Save` 단추를 클릭 합니다. 닫는 앞의 마지막 줄 `</form>` 숨겨진 표시 된 요소 [XSRF](xref:security/anti-request-forgery) 에 의해 생성 된 토큰의 [Form 태그 도우미](xref:mvc/views/working-with-forms)합니다.

## <a name="processing-the-post-request"></a>POST 요청을 처리합니다.

에서는 다음 목록에서 `[HttpPost]` 버전의는 `Edit` 작업 메서드.

[!code-csharp[주](start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit2)]

`[ValidateAntiForgeryToken]` 특성의 숨겨진 유효성을 검사 [XSRF](xref:security/anti-request-forgery) 위조 방지 토큰 생성기에 의해 생성 된 토큰은 [Form 태그 도우미](xref:mvc/views/working-with-forms)

[모델 바인딩](xref:mvc/models/model-binding) 시스템에서는 게시 된 양식 값을 사용 하 고 만듭니다는 `Movie` 변수로 전달 되는 개체는 `movie` 매개 변수입니다. `ModelState.IsValid` 메서드 (편집 또는 업데이트)을 수정 하는 양식에서 제출 된 데이터를 사용할 수 있다는 확인 한 `Movie` 개체입니다. 데이터가 유효한 경우에 저장 됩니다. 업데이트 된 (편집된) 영화 데이터를 호출 하 여 데이터베이스에 저장 되는 `SaveChangesAsync` 데이터베이스 컨텍스트의 메서드. 데이터를 저장 한 후 코드는 사용자를 리디렉션하는 `Index` 작업 메서드는 `MoviesController` 방금 변경한을 포함 하 여 영화 컬렉션을 표시 하는 클래스입니다.

폼이 서버에 게시 하기 전에 클라이언트 쪽 유효성 검사는 필드에서 유효성 검사 규칙을 확인 합니다. 모든 유효성 검사 오류가 있는 경우 오류 메시지가 표시 되 고에 폼이 게시 되지 않습니다. JavaScript를 사용 하지 않도록 설정 하면 클라이언트 쪽 유효성 검사를 하지 않습니다 하지만 서버 유효 하지 않은 게시 된 값 확인 하 고 오류 메시지와 함께 폼 값이 다시 표시 됩니다. 자습서의 뒷부분에 나오는 검사 [모델 유효성 검사](xref:mvc/models/validation) 자세히 합니다. [유효성 검사 태그 도우미](xref:mvc/views/working-with-forms) 에 *Views/Movies/Edit.cshtml* 적절 한 오류 메시지를 표시 하는 템플릿을 담당 하는 보기입니다.

![보기 편집: abc의 잘못 된 가격 값에 대 한 예외 상태 필드 가격 숫자를 여야 합니다. Xyz 상태의 하십시오 잘못 된 출시 날짜 값에 대 한 예외는 유효한 날짜를 입력 합니다.](controller-methods-views/_static/val.png)

모든는 `HttpGet` 영화 컨트롤러의 메서드에 비슷한 패턴을 따릅니다. 동영상 개체를 가져옵니다 (또는 개체의 경우에서 목록을 `Index`), 고 보기에 개체 (모델)을 전달 합니다. `Create` 메서드는 빈 동영상 개체를 전달 된 `Create` 보기. 만들기, 편집, 삭제 또는 그렇지 않은 경우 데이터를 수정 하는 모든 메서드를 수행할는 `[HttpPost]` 메서드의 오버 로드 합니다. 데이터는 HTTP GET 메서드를 수정 하는 것은 보안상 위험 합니다. 데이터를 수정는 `HTTP GET` 메서드는 또한 HTTP에 대 한 유용한 정보 및 아키텍처는 위반 [REST](http://rest.elkstein.org/) 패턴에서는 GET 요청 응용 프로그램의 상태를 변경 하지 않아야 지정 합니다. 즉, GET 작업을 수행 해야 안전 하 게 보호 하는 작업을 의도 하지 않은 영구 데이터를 수정 하지 않습니다.

## <a name="additional-resources"></a>추가 리소스

* [전역화 및 지역화](xref:fundamentals/localization)
* [태그 도우미 소개](xref:mvc/views/tag-helpers/intro)
* [태그 도우미를 작성합니다.](xref:mvc/views/tag-helpers/authoring)
* [백신 요청 위조 방지](xref:security/anti-request-forgery)
* 보호에서 컨트롤러 [과도 한 게시](http://www.asp.net/mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application#overpost)
* [Viewmodel](http://rachelappel.com/use-viewmodels-to-manage-data-amp-organize-code-in-asp-net-mvc-applications/)
* [Form 태그 도우미](xref:mvc/views/working-with-forms)
* [입력된 태그 도우미](xref:mvc/views/working-with-forms)
* [레이블 태그 도우미](xref:mvc/views/working-with-forms)
* [태그 도우미를 선택 합니다.](xref:mvc/views/working-with-forms)
* [유효성 검사 태그 도우미](xref:mvc/views/working-with-forms)

>[!div class="step-by-step"]
[이전](working-with-sql.md)
[다음](search.md)  

