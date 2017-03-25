---
title: "컨트롤러 추가 | Microsoft 문서"
author: rick-anderson
description: "간단한 ASP.NET 핵심 MVC 응용 프로그램에 컨트롤러를 추가 하는 방법"
keywords: ASP.NET Core, MVC
ms.author: riande
manager: wpickett
ms.date: 02/28/2017
ms.topic: article
ms.assetid: e04b6665-d0de-4d99-b78f-d6a0c4634a87
ms.technology: aspnet
ms.prod: asp.net-core
uid: tutorials/first-mvc-app/adding-controller
translationtype: Machine Translation
ms.sourcegitcommit: d2b516e454571992156ca0154bd5db73b3fe5892
ms.openlocfilehash: 19cc342fb97e5bbbc508c6d7cb54eebf94a56f7b
ms.lasthandoff: 03/23/2017

---
# <a name="adding-a-controller"></a>컨트롤러 추가

[Rick Anderson](https://twitter.com/RickAndMSFT)

모델-뷰-컨트롤러 (MVC) 아키텍처 패턴 세 가지 주요 구성 요소를 응용 프로그램을 분리: **M**odel, **V**, 뷰 및 **C**ontroller 합니다. MVC 패턴에는 테스트 가능 하 고 기존의 구식 응용 프로그램 보다 쉽게 업데이트할 수 있는 앱을 만들 수 있습니다. MVC 기반 앱에는 다음이 포함 되어 있습니다.

* **M**odels: 응용 프로그램의 데이터를 나타내는 클래스입니다. 유효성 검사 논리를 사용 하 여 해당 데이터에 대 한 비즈니스 규칙을 적용 하는 모델 클래스. 일반적으로 모델 개체를 검색 하 고 모델 상태를 데이터베이스에 저장 합니다. 이 자습서에서는 한 `Movie` 모델 데이터베이스에서 영화 데이터를 검색, 보기에 제공 하거나 업데이트 합니다. 업데이트 된 데이터는 SQL Server 데이터베이스에 기록 됩니다.

* **V**iews: 뷰는 응용 프로그램의 사용자 인터페이스 (UI)를 표시 하는 구성 요소입니다. 일반적으로이 UI에는 모델 데이터가 표시 됩니다.

* **C**ontrollers: 브라우저 요청을 처리 하는 클래스입니다. 모델 데이터를 검색 하며 응답을 반환 하는 템플릿 보기를 호출 합니다. MVC 응용 프로그램에서 뷰 정보만 표시 됩니다. 컨트롤러는 처리 하 고 사용자 입력 및 상호 작용에 응답 합니다. 예를 들어 컨트롤러에서 경로 데이터 및 쿼리 문자열 값을 처리 모델에 이러한 값을 전달 합니다. 모델은 데이터베이스를 쿼리하려면 다음이 값을 사용할 수 있습니다. 예를 들어 `http://localhost:1234/Home/About` 의 데이터 경로 `Home` (컨트롤러) 및 `About` (home 컨트롤러에 대해 호출할 작업 메서드). `http://localhost:1234/Movies/Edit/5`ID 가진 동영상 편집에 대 한 요청은 동영상 컨트롤러를 사용 하 여&5; =.  이 자습서의 뒷부분에 나오는 데이터 경로 대 한 이야기 하겠습니다.

MVC 패턴에는 이러한 요소 간의 느슨한 결합을 제공 하는 동안 (입력된 논리, 비즈니스 논리 및 UI 논리) 응용 프로그램의 다양 한 측면을 분리 하는 응용 프로그램을 만들 수 있습니다. 패턴 각 종류의 논리 앱에 있는 위치를 지정 합니다. UI 논리 보기에 속해 있습니다. 입력된 논리는 컨트롤러에 속합니다. 비즈니스 논리는 모델에 속합니다. 이렇게이 분리 하면 다른 코드에 영향을 주지 않고 구현 시 한 번의 한 측면을 작업할 수 있기 때문에 응용 프로그램을 빌드할 때 복잡성을 관리할 수 있습니다. 예를 들어 비즈니스 논리 코드에 따라 보기 코드 없이 작업할 수 있습니다.

이 자습서에서는 이러한 모든 개념을 살펴볼을 하는 간단한 영화 응용 프로그램을 구축 하는 데 사용 하는 방법을 설명 합니다. MVC 프로젝트에 대 한 폴더를 현재 포함 된 *컨트롤러* 및 *뷰*합니다. A *모델* 이후 단계에서 폴더를 추가 합니다.

* **솔루션 탐색기**를 마우스 오른쪽 단추로 클릭 **컨트롤러 > 추가 > 새 항목**

![상황에 맞는 메뉴](adding-controller/_static/add_controller.png)

* 선택 **MVC 컨트롤러 클래스**
* 에 **새 항목 추가** 대화 상자에서 입력 **HelloWorldController**합니다.

![MVC 컨트롤러를 추가 하 고 이름을](adding-controller/_static/ac.png)

그런 후 *Controllers/HelloWorldController.cs* 다음으로:

[!code-csharp[주](start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_1)]

모든 `public` 컨트롤러의 메서드는 HTTP 끝점으로 호출할 수 있습니다. 위의 예제에서 두 메서드는 문자열을 반환합니다.  각 메서드 앞에 주석을 note 합니다.

이것이 첫 번째 메모 상태는 [HTTP GET](http://www.w3schools.com/tags/ref_httpmethods.asp) 의 기준 URL에 "/HelloWorld/"를 추가 하 여 호출 되는 메서드입니다. 두 번째 주석 지정는 [HTTP GET](http://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) 메서드를 추가 하 여 호출 되는 "/ HelloWorld/시작/"를 URL에 있습니다. 나중에 자습서에서 사용 하 여 하겠습니다 스 캐 폴딩 엔진 생성 `HTTP POST` 메서드.

비-디버그 모드 (Ctrl + F5 누름)에서 앱을 실행 하 고 주소 표시줄에 경로에 "HelloWorld"를 추가 합니다. (아래 이미지에서 `http://localhost:1234/HelloWorld` 사용 됩니다 교체 해야 하지만 *1234* 응용 프로그램의 포트 번호입니다.) `Index` 메서드는 문자열을 반환 합니다. 일부 HTML을 반환 하는 시스템에서 설정한 및 것!

![이 응용 프로그램 응답에 표시 된 브라우저 창 내 기본 작업은](adding-controller/_static/hell1.png)

MVC는 컨트롤러 클래스 (및 내 작업 메서드)은 들어오는 URL에 따라 호출합니다. 기본 [URL 라우팅 논리](../../mvc/controllers/routing.md) MVC 사용 하 여 다음과 같은 형식을 확인 하는 데 코드를 호출 합니다.

`/[Controller]/[ActionName]/[Parameters]`

라우팅에 형식을 설정 하면는 *Startup.cs* 파일입니다.

[!code-csharp[주](start-mvc/sample/MvcMovie/Startup.cs?name=snippet_1&highlight=5)]

앱을 실행 하 고 모든 URL 세그먼트를 지정 하지 "홈" 컨트롤러에 기본적으로 고 "Index" 메서드는 위의 강조 표시 하는 서식 파일 줄에서 지정 합니다.

첫 번째 URL 세그먼트는 실행 하는 컨트롤러 클래스를 결정 합니다. 따라서 `localhost:xxxx/HelloWorld` 매핑되는 `HelloWorldController` 클래스입니다. URL 세그먼트의 두 번째 부분 클래스에 작업 메서드를 결정합니다. 따라서 `localhost:xxxx/HelloWorld/Index` 로 인해는 `Index` 의 메서드는 `HelloWorldController` 클래스를 실행 합니다. 로 이동 했습니다 사라졌는지 `localhost:xxxx/HelloWorld` 및 `Index` 메서드가 기본적으로 합니다. 즉, `Index` 메서드 이름을 명시적으로 지정 하지 않으면 컨트롤러에서 호출 하는 기본 메서드입니다. URL 세그먼트의 세 번째 부분 ( `id`) 데이터 경로입니다. 이 자습서에서는 경로 데이터를 나중에 표시 됩니다 했습니다.

찾아 `http://localhost:xxxx/HelloWorld/Welcome`합니다. `Welcome` 메서드가 실행 되 고 "이것이 시작 작업 방법..." 문자열을 반환 합니다. 이 URL에 대 한 컨트롤러는 `HelloWorld` 및 `Welcome` 작업 메서드는 합니다. 사용 하지 않은 우리는 `[Parameters]` 아직 URL의 일부입니다.

![브라우저 창에 표시 된 응용 프로그램 응답이는 시작 작업 방법](adding-controller/_static/welcome.png)

수정 해 보겠습니다 예제 약간 컨트롤러에 URL에서 일부 매개 변수 정보를 전달할 수 있도록 (예를 들어 `/HelloWorld/Welcome?name=Scott&numtimes=4`).  변경 된 `Welcome` 메서드를 아래와 같이 두 개의 매개 변수를 포함 합니다. 코드를 나타내기 위해 C# 선택적 매개 변수 기능을 사용 하는 `numTimes` 없는 값은 해당 매개 변수에 대해 전달 된 매개 변수의 기본값은 1입니다.

[!code-csharp[주](start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_2)]

코드를 사용 하 여 위에 `HtmlEncoder.Default.Encode` 악의적인 입력 (즉, JavaScript)에서 응용 프로그램을 보호 하기 위해. 또한 사용 하 여 [문자열 보간](https://docs.microsoft.com/dotnet/articles/csharp/language-reference/keywords/interpolated-strings)합니다.

Visual Studio에서 비-디버그 모드 (Ctrl + F5)에서 필요가 없습니다 코드를 변경한 다음 응용 프로그램을 작성 합니다. 파일을 저장할 것 브라우저 새로 고치고 변경 내용을 볼 수 있습니다.

응용 프로그램을 실행 하 고 이동 합니다.

   `http://localhost:xxxx/HelloWorld/Welcome?name=Rick&numtimes=4`

(Xxxx를 포트 번호로 바꿉니다.) 에 대해 다른 값을 사용해 볼 수 있습니다 `name` 및 `numtimes` URL에 있습니다. MVC [모델 바인딩](../../mvc/models/model-binding.md) 시스템 메서드에 매개 변수로 주소 표시줄에는 쿼리 문자열에서 명명 된 매개 변수를 자동으로 매핑합니다. 참조 [모델 바인딩](../../mvc/models/model-binding.md) 에 대 한 자세한 내용은 합니다.

![브라우저 창에 표시 된 응용 프로그램 응답 Hello Rick의 NumTimes는: 4](adding-controller/_static/rick4.png)

URL 세그먼트 위의 샘플 (`Parameters`)을 사용 하지 않으면는 `name` 및 `numTimes` 매개 변수가 전달 [쿼리 문자열](http://en.wikipedia.org/wiki/Query_string)합니다. `?` (물음표)는 구분 기호 이며 쿼리 문자열에 따라 위 URL에서. `&` 문자 쿼리 문자열을 구분 합니다.

`Welcome` 메서드를 다음 코드로 바꿉니다.

[!code-csharp[주](start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_3)]

응용 프로그램을 실행 하 고 다음 URL을 입력 합니다.`http://localhost:xxx/HelloWorld/Welcome/3?name=Rick`

![브라우저 창에 표시 된 응용 프로그램 응답의 Hello Rick, ID: 3](adding-controller/_static/rick_routedata.png)

이 이번에는 세 번째 URL 세그먼트 일치 하는 경로 매개 변수 `id`합니다. `Welcome` 메서드의 매개 변수에 `id` URL 서식 파일에서 일치 하는 `MapRoute` 메서드. 후행 `?` (에서 `id?`) 나타냅니다는 `id` 매개 변수는 선택적입니다.

[!code-csharp[주](start-mvc/sample/MvcMovie/Startup.cs?name=snippet_1&highlight=5)]

이 예에서 컨트롤러 사업을 해 왔습니다 MVC의 "VC" 부분-즉, 뷰 및 컨트롤러 작업 합니다. 컨트롤러 HTML을 직접 반환 됩니다. 일반적으로 매우 복잡 하 고 유지 관리 되므로 HTML을 직접 반환 하는 컨트롤러 바람직하지 않습니다. 대신 일반적으로에서는 Razor 보기 템플릿 파일을 별도 HTML 응답을 생성 하기. 다음 자습서에서 그렇게 할 수 있습니다.

>[!div class="step-by-step"]
[이전](start-mvc.md)
[다음](adding-view.md)  

