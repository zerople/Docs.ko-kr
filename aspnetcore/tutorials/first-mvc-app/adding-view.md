---
title: "뷰 추가 | Microsoft 문서"
author: rick-anderson
description: "간단한 ASP.NET 핵심 MVC 응용 프로그램에 뷰 추가"
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 03/04/2017
ms.topic: article
ms.assetid: 6942432b-bac0-41e6-9ad7-cad313abf4db
ms.technology: aspnet
ms.prod: asp.net-core
uid: tutorials/first-mvc-app/adding-view
translationtype: Machine Translation
ms.sourcegitcommit: f93c93002fec0088a7040cd53f31fd5b5a62fea7
ms.openlocfilehash: a6d5e9424d32abd1d285a1c1a3b1fbb5262b4ee5
ms.lasthandoff: 03/23/2017

---
# <a name="adding-a-view"></a>뷰 추가

[Rick Anderson](https://twitter.com/RickAndMSFT)

이 섹션에서는 한다는 것을 수정 하는 `HelloWorldController` 템플릿 파일을 명확 하 게 캡슐화 하는 프로세스를 클라이언트에 HTML 응답을 생성 하는 Razor 뷰를 사용 하는 클래스입니다.

Razor를 사용 하 여 보기 템플릿 파일을 만들어야 합니다. 서식 파일 razor 기반 보기는 *.cshtml* 파일 확장명입니다. 만들 C#을 사용 하 여 HTML 출력 하는 세련 된 방법을 제공 합니다.

현재는 `Index` 메서드는이 메시지에 컨트롤러 클래스에 하드 코드 된 문자열을 반환 합니다. 에 `HelloWorldController` 클래스를 대체는 `Index` 메서드를 다음 코드로 바꿉니다.

[!code-csharp[주](start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_4)]

`Index` 위의 메서드는 뷰 개체를 반환 합니다. 브라우저에 HTML 응답을 생성 하려면 보기 서식 파일을 사용 합니다. 와 같은 컨트롤러 메서드 (작업 메서드에 라고도 함)는 `Index` 위의 메서드는 일반적으로 반환는 `IActionResult` (또는 클래스에서 파생 된 `ActionResult`)와 같은 문자열 하지 기본 형식입니다.

* 마우스 오른쪽 단추로 클릭는 *뷰* 폴더를 차례로 **추가 > 새 폴더** 폴더 이름을 *HelloWorld*합니다.

* 마우스 오른쪽 단추로 클릭는 *보기/HelloWorld* 폴더를 차례로 **추가 > 새 항목**합니다.

* 에 **새 항목 추가-MvcMovie** 대화 상자

  * 오른쪽 위에 검색 상자에 입력 *보기*

  * 누르기 **MVC 페이지 보기**

  * 에 **이름** 상자에서 하는 데 필요한 경우 이름을 변경할 *Index.cshtml*합니다.

  * 누르기 **추가**

![새 항목 추가 대화 상자](adding-view/_static/add_view.png)

내용을 대체는 *Views/HelloWorld/Index.cshtml* 다음과 같은 Razor 뷰 파일:

[!code-HTML[주](start-mvc/sample/MvcMovie/Views/HelloWorld/Index.cshtml)]

이동 `http://localhost:xxxx/HelloWorld`합니다. `Index` 에서 메서드는 `HelloWorldController` 많은 작업을 수행 하지 않은; 문은 단순히 실행 `return View();`, 메서드가 브라우저에 대 한 응답을 렌더링 하 보기 템플릿 파일을 사용 해야 함을 지정 합니다. MVC를 사용 하 여 기본값으로 사용 하 여 보기 템플릿 파일의 이름에 명시적으로 지정 하지 않은 때문에 *Index.cshtml* 에서 파일 보기는 */뷰/HelloWorld* 폴더입니다. 아래 그림에서는 문자열 "우리의 보기 템플릿에서 Hello!" 하드 코드 된 보기에 있습니다.

![브라우저 창](adding-view/_static/hell_template.png)

Tap ()를 설정/해제 해야 할 수 있습니다 (예: 모바일 장치)에서 브라우저 창을 작으면는 [부트스트랩 탐색 단추](http://getbootstrap.com/components/#navbar) 보려면 오른쪽 위에 있는에 **홈**, **에 대 한**, 및 **연락처** 링크 합니다.

![부트스트랩 탐색 단추를 강조 표시 하는 브라우저 창](adding-view/_static/1.png)

## <a name="changing-views-and-layout-pages"></a>보기 및 레이아웃 페이지를 변경합니다.

메뉴 링크를 누릅니다 (**MvcMovie**, **홈**, **에 대 한**). 각 페이지 같은 메뉴 레이아웃을 보여 줍니다. 메뉴 레이아웃에서 구현 되는 *Views/Shared/_Layout.cshtml* 파일입니다. 열기는 *Views/Shared/_Layout.cshtml* 파일입니다.

[레이아웃](xref:mvc/views/layout) 템플릿을 사용 하면 한 곳에서 사이트의 HTML 컨테이너 레이아웃을 지정 하 고 다음 사이트의 여러 페이지에 걸쳐 적용 수 있습니다. 찾기는 `@RenderBody()` 선입니다. `RenderBody`모든 보기 전용 페이지 하면 자리 표시자 쇼 만들기, *래핑된* 레이아웃 페이지에 있습니다. 예를 들어, 선택 하는 경우는 **에 대 한** 링크를는 **Views/Home/About.cshtml** 보기 내에서 렌더링 되는 `RenderBody` 메서드.

## <a name="change-the-title-and-menu-link-in-the-layout-file"></a>레이아웃 파일의 제목 및 메뉴 링크 변경

Title 요소 내용을 변경 합니다. 레이아웃 서식 파일에 고정 텍스트 "영화 App"에서 컨트롤러를 변경 `Home` 를 `Movies` 아래와 같이 합니다.

[!code-html[주](start-mvc/sample/MvcMovie/Views/Shared/_Layout.cshtml?highlight=7,31)]

>[!WARNING]
> 구현 되지 않은 `Movies` 이 링크를 클릭 하 아직 컨트롤러, 따라서, 404 (찾을 수 없음) 오류 메시지가 나타납니다.

변경 내용을 저장 하 고 탭은 **에 대 한** 링크 합니다. 어떻게 브라우저 탭에 제목 표시 **앱에 대 한-영화** 대신 **-Mvc 동영상 정보**합니다. 탭은 **연락처** 에 연결 하 고도 표시 되는지 확인 **동영상 앱**합니다. 레이아웃 템플릿을 한 번만 변경 사항을 적용할 수 및 새 링크 텍스트 및 새 제목을 사이트에 있는 모든 페이지 반영가 합니다.

검사는 *Views/_ViewStart.cshtml* 파일:


```HTML
@{
    Layout = "_Layout";
}
```

*Views/_ViewStart.cshtml* 파일에서 제공 된 *Views/Shared/_Layout.cshtml* 각 보기에는 파일입니다. 사용할 수는 `Layout` 속성을 다른 레이아웃 뷰를 설정 하거나 설정 `null` 없는 레이아웃 파일을 사용 하도록 합니다.

제목을 변경는 `Index` 보기.

열기 *Views/HelloWorld/Index.cshtml*합니다. 변경 하는 위치가 두 군데 있습니다.

   * 브라우저의 제목에 표시 되는 텍스트입니다.
   * 보조 헤더 (`<h2>` 요소).

할을 약간 다른 응용 프로그램의 어느 부분을 변경 하는 어떤 약간의 코드를 볼 수 있습니다.


```HTML
@{
    ViewData["Title"] = "Movie List";
}

<h2>My Movie List</h2>

<p>Hello from our View Template!</p>
```

`ViewData["Title"] = "Movie List";`집합 위의 코드에는 `Title` 의 속성은 `ViewData` 사전 "영화 목록"입니다. `Title` 속성에 사용 되는 `<title>` 레이아웃 페이지의 HTML 요소:


```HTML
<title>@ViewData["Title"] - Movie App</title>
   ```

변경 내용을 저장 하 고 이동 `http://localhost:xxxx/HelloWorld`합니다. 브라우저의 제목, 기본 제목 및 보조 제목 변경 되었다는 것을 확인 합니다. (브라우저에서 변경 내용이 보이지 않으면 표시 될 수 있습니다 캐시 된 콘텐츠입니다. Ctrl + f5 강제로 로드할 서버 로부터 응답을 브라우저에서.) 브라우저의 제목으로 만들어집니다. `ViewData["Title"]` 설정는 *Index.cshtml* 템플릿 및 추가 보기 "-동영상 앱" 레이아웃 파일에 추가 합니다.

또한 방법을의 콘텐츠는 *Index.cshtml* 보기 템플릿 병합 된는 *Views/Shared/_Layout.cshtml* 템플릿 보기와 단일 HTML 응답 브라우저에 게 보냈습니다. 레이아웃 템플릿을 쉽게 실제로 모든 응용 프로그램에서 페이지에 걸쳐 적용 되는 변경할 수 있습니다. 자세한 참조에 알아보려면 [레이아웃](../../mvc/views/layout.md)합니다.

![영화 목록 보기](adding-view/_static/hell3.png)

우리의 약간의 "데이터" (이 경우는 "Hello 우리의 보기 템플릿에서!" 메시지)는 하드 코드 하지만입니다. MVC 응용 프로그램에는 "V" (뷰) 및 "C" (컨트롤러) 하지만 없습니다 "M" (모델) 아직 완성 합니다.

## <a name="passing-data-from-the-controller-to-the-view"></a>데이터를 전달 하는 컨트롤러에서 보기

컨트롤러 작업은 들어오는 URL 요청에 대 한 응답으로 호출 됩니다. 컨트롤러 클래스는 들어오는 브라우저 요청을 처리 하는 코드를 작성 하는 위치입니다. 컨트롤러 데이터 원본에서 데이터를 검색 하 고 브라우저에 다시 보낼 응답의 유형을 결정 합니다. 템플릿 보기를 생성 하 여 HTML 응답을 브라우저에 서식을 지정 하는 컨트롤러에서 사용할 수 있습니다.

컨트롤러는 응답을 렌더링 하는 보기 템플릿 하기 위해 필요한 데이터를 제공 하는 일을 담당 합니다. 가장 좋은 방법은: 템플릿 보기 해야 **하지** 비즈니스 논리를 수행 하거나 데이터베이스와 직접 상호 작용 합니다. 대신, 보기 서식 파일에 컨트롤러에 의해 제공 되는 데이터 에서만 작동 해야 합니다. "중요 한 부분의 분리"이 유지 관리 정리, 테스트 및 유지 관리 가능한 코드를 유지할 수 있습니다.

현재는 `Welcome` 에서 메서드는 `HelloWorldController` 는 클래스는 `name` 및 `ID` 매개 변수 및 브라우저에 직접 값을 출력 합니다. 대신이 응답을 문자열로 렌더링 하는 컨트롤러, 보기 서식 파일을 대신 사용 하는 컨트롤러를 변경 하겠습니다. 템플릿 보기 컨트롤러에서 응답을 생성 하기 위해 보기에 적절 한 비트 데이터를 전달 해야 한다는 뜻 동적 응답을 생성 합니다. 컨트롤러 템플릿 보기에는 동적 데이터 (매개 변수)를 배치 하 여이 수행할 수는 `ViewData` 사전 템플릿 보기에 액세스할 수 있습니다.

으로 돌아가서는 *HelloWorldController.cs* 파일을 변경의 `Welcome` 메서드를 추가 `Message` 및 `NumTimes` 값을 `ViewData` 사전입니다. `ViewData` 사전이 동적 개체를 추가 하 여 제어에 원하는 것, 즉는 `ViewData` 내부 요소를 넣으면 될 때까지 개체에 정의 된 속성이 없습니다. [MVC 모델 바인딩 시스템](xref:mvc/models/model-binding) 자동으로 명명 된 매개 변수를 매핑합니다 (`name` 및 `numTimes`)를 매개 변수로 메서드에 주소 표시줄에 쿼리 문자열에서 합니다. 전체 *HelloWorldController.cs* 파일은 다음과 같습니다.

[!code-csharp[주](start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_5)]

`ViewData` 보기에 전달 되는 데이터를 포함 하는 사전 개체입니다. 다음으로 시작 보기 서식 파일이 필요 합니다.

* 마우스 오른쪽 단추로 클릭는 *보기/HelloWorld* 폴더를 차례로 **추가 > 새 항목**합니다.
* 에 **새 항목 추가-MvcMovie** 대화 상자:

  * 오른쪽 위에 검색 상자에 입력 *보기*합니다.
  * 누르기 **MVC 페이지 보기**합니다.
  * 에 **이름** 상자에 입력 합니다 *Welcome.cshtml*
  * Tap **Add**.

루프를 만드는 것은 *Welcome.cshtml* "Hello"를 표시 하는 템플릿 보기 `NumTimes`합니다. 그런 후 *Views/HelloWorld/Welcome.cshtml* 다음으로:

[!code-html[주](start-mvc/sample/MvcMovie/Views/HelloWorld/Welcome.cshtml)]

변경 내용을 저장 하 고 다음 URL로 이동 합니다.

`http://localhost:xxxx/HelloWorld/Welcome?name=Rick&numtimes=4`

URL에서 가져온 데이터를 사용 하 여 컨트롤러에 전달 되는 [MVC 모델 바인더가](xref:mvc/models/model-binding) 합니다. 컨트롤러에 데이터를 패키지는 `ViewData` 사전 및 보기에 해당 개체를 전달 합니다. 뷰는 다음 브라우저에 HTML로 데이터를 렌더링합니다.

![보기에 대 한 시작 레이블과 Hello Rick&4; 번 표시 된 구문을 보여 주는](adding-view/_static/rick.png)

위의 예제에서 사용 된 `ViewData` 를 보기 컨트롤러에서 데이터를 전달 하는 사전입니다. 자습서의 뒷부분에 나오는 뷰 모델 데이터를 전달 하는 컨트롤러에서 보기에 사용 합니다. 데이터를 전달 하는 뷰 모델 접근 방식은 일반적으로 보다 훨씬 선호 되는 `ViewData` 사전 접근 합니다.

모델, 하지만 하지 데이터베이스 종류에 대 한 일종의 "M" 했습니다. 배운 및 기능 데이터베이스 영화를 만들어 보겠습니다.

>[!div class="step-by-step"]
[이전](adding-controller.md)
[다음](adding-model.md)
