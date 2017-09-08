---
title: "네이티브 모바일 응용 프로그램에 대 한 백 엔드 서비스 만들기"
author: ardalis
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 3b6a32f2-5af9-4ede-9b7f-17ab300526d0
ms.technology: aspnet
ms.prod: asp.net-core
uid: mobile/native-mobile-backend
ms.openlocfilehash: f2b74d6739112909ee05e036e904d5e30868fdbe
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/11/2017
---
# <a name="creating-backend-services-for-native-mobile-applications"></a>네이티브 모바일 응용 프로그램에 대 한 백 엔드 서비스 만들기

으로 [Steve Smith](http://ardalis.com)

모바일 앱 ASP.NET Core 백 엔드 서비스와 쉽게 통신할 수 있습니다.

[보기 또는 샘플 백 엔드 서비스 코드를 다운로드 합니다.](https://github.com/aspnet/Docs/tree/master/aspnetcore/mobile/native-mobile-backend/sample)

## <a name="the-sample-native-mobile-app"></a>샘플 기본 모바일 앱

이 자습서에는 네이티브 모바일 앱을 지원 하기 위해 ASP.NET Core MVC를 사용 하 여 백 엔드 서비스를 만드는 방법을 보여 줍니다. 사용 하 여는 [Xamarin Forms ToDoRest 앱](https://developer.xamarin.com/guides/xamarin-forms/web-services/consuming/rest/) Android, iOS, Windows 유니버설 및 Window Phone 장치에 대 한 별도 네이티브 클라이언트를 포함 하는 native client로 합니다. Xamarin 샘플 솔루션 다운로드 수 있을 뿐만 아니라 네이티브 응용 프로그램 (필요한 무료 Xamarin 도구가 설치)를 만들고 연결 된 자습서에 따라 수 있습니다. Xamarin 샘플에는 ASP.NET Web API 2 서비스 프로젝트에서이 문서의 ASP.NET Core 응용 프로그램으로 바꿉니다 (클라이언트에 필요한 변경 내용이 없습니다) 포함 되어 있습니다.

![Android smartphone에서 실행 되는 않는 Rest 응용 프로그램을](native-mobile-backend/_static/todo-android.png)

### <a name="features"></a>기능

ToDoRest 앱 나열 하 고, 추가, 삭제, 할 일 항목을 업데이트를 지원 합니다. 각 항목에는 ID, 이름, 메모, 및 아직 완료 되었는지 여부를 나타내는 속성에 있습니다.

항목의 주 보기 위에 표시 된 것 처럼 각 항목의 이름을 나열 하 고 확인 표시가 사용을 완료 하는 경우를 나타냅니다.

탭의 `+` 아이콘은 추가 항목 대화 상자를 엽니다.

![항목 추가 대화 상자](native-mobile-backend/_static/todo-android-new-item.png)

기본 목록 화면에서 항목을 누르면 항목의 이름, 메모 및 완료 설정을 수정할 수 있습니다 또는 항목을 삭제할 수 있는 편집 대화 상자를 엽니다.

![항목 대화 상자 편집](native-mobile-backend/_static/todo-android-edit-item.png)

이 샘플은 기본적으로 읽기 전용 작업을 허용 하는 developer.xamarin.com에서 호스트 되는 백 엔드 서비스를 사용 하도록 구성 됩니다. 샘플을 테스트 하려면 직접 순서도 컴퓨터에서 실행 되는 다음 섹션에서 만든 ASP.NET Core 응용 프로그램에 대 한 응용 프로그램의 업데이트 해야 `RestUrl` 상수입니다. 로 이동 된 `ToDoREST` 연 프로젝트는 *Constants.cs* 파일입니다. 대체는 `RestUrl` 컴퓨터의 IP를 포함 하는 url 주소 (localhost 또는 127.0.0.1,이 주소는 컴퓨터에서가 아니라 장치 에뮬레이터에서 사용 하므로). 포트 번호 (5000)을 포함 합니다. 장치 작업할 서비스를 테스트 하기 위해이 포트에 대 한 액세스를 차단 하는 활성 방화벽 없는 확인 합니다.

<!-- literal_block {"ids": [], "names": [], "highlight_args": {}, "backrefs": [], "dupnames": [], "linenos": false, "classes": [], "xml:space": "preserve", "language": "csharp"} -->

```csharp
// URL of REST service (Xamarin ReadOnly Service)
//public static string RestUrl = "http://developer.xamarin.com:8081/api/todoitems{0}";

// use your machine's IP address
public static string RestUrl = "http://192.168.1.207:5000/api/todoitems/{0}";
```

## <a name="creating-the-aspnet-core-project"></a>ASP.NET Core 프로젝트 만들기

Visual Studio에서 새 ASP.NET Core 웹 응용 프로그램을 만듭니다. 웹 API 템플릿과 인증 안 함을 선택 합니다. 프로젝트 이름을 *ToDoApi*합니다.

![선택한 웹 API 프로젝트 템플릿 사용 하 여 새 ASP.NET 웹 응용 프로그램 대화 상자](native-mobile-backend/_static/web-api-template.png)

응용 프로그램은 5000 포트에 대 한 모든 요청에 응답 해야 합니다. 업데이트 *Program.cs* 포함 하도록 `.UseUrls("http://*:5000")` 이 작업을 수행할:

[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Program.cs?range=10-16&highlight=3)]

> [!NOTE]
> 뒤에 기본적으로 로컬이 아닌 요청을 무시 합니다. IIS Express가 아닌을 직접 응용 프로그램을 실행 해야 합니다. 실행 `dotnet run` 명령 프롬프트에서 또는 Visual Studio 도구 모음에서 디버그 대상 드롭다운 목록에서 응용 프로그램 이름 프로필을 선택 합니다.

할 일 항목을 나타내기 위해 모델 클래스를 추가 합니다. 표시 필수 필드를 사용 하 여 `[Required]` 특성:

[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Models/ToDoItem.cs)]

API 메서드 데이터로 작업 하는 방법이 필요 합니다. 동일한를 사용 하 여 `IToDoRepository` 원래 Xamarin 샘플에서는 인터페이스:

[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Interfaces/IToDoRepository.cs)]

이 샘플에 대 한 구현만 개인 항목 컬렉션을 사용합니다.

[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Services/ToDoRepository.cs)]

구성에서 구현을 *Startup.cs*:

[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Startup.cs?highlight=6&range=29-35)]

이 시점에서 준비가 만들기는 *ToDoItemsController*합니다.

> [!TIP]
> 만들기에 대 한 웹 Api에 알아보기 [건물 Your 첫 번째 Web API Core MVC ASP.NET 및 Visual Studio](../tutorials/first-web-api.md)합니다.

## <a name="creating-the-controller"></a>컨트롤러 만들기

프로젝트에 새 컨트롤러 추가 *ToDoItemsController*합니다. 그 Microsoft.AspNetCore.Mvc.Controller에서 상속 해야 합니다. 추가 `Route` 컨트롤러를 시작 하는 경로에 대 한 요청을 처리 합니다 나타내려면 특성 `api/todoitems`합니다. `[controller]` 경로의 토큰은 컨트롤러의 이름으로 대체 (생략 하는 것은 `Controller` 접미사), 되며가 글로벌 경로 대 한 특히 유용 합니다. 에 대 한 자세한 내용은 [라우팅](../fundamentals/routing.md)합니다.

컨트롤러의 요구 사항은 `IToDoRepository` 를 작동; 컨트롤러의 생성자를 통해이 형식의 인스턴스를 요청 합니다. 런타임 시이 인스턴스가 제공 됩니다에 대 한 프레임 워크의 지원을 사용 하 여 [종속성 주입](../fundamentals/dependency-injection.md)합니다.

[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=1-17&highlight=9,14)]

이 API는 데이터 원본에서 CRUD (만들기, 읽기, 업데이트, 삭제) 작업을 수행 4 개의 다른 HTTP 동사를 지원 합니다. 이 중 가장 간단한 HTTP GET 요청에 해당 하는 읽기 작업입니다.

### <a name="reading-items"></a>항목을 읽는

GET 요청에 의해 이루어진다는 항목 목록을 요청 하는 `List` 메서드. `[HttpGet]` 특성에 `List` 메서드는이 작업은 GET 요청을 처리만 나타냅니다. 이 작업에 대 한 경로 컨트롤러에 지정 된 경로입니다. 반드시 작업 이름을 경로의 일부로 사용할 필요가 없습니다. 각 작업에는 고유 하 고 명확한 경로 확인 하기만 하면 됩니다. 컨트롤러와 특정 경로를 작성 하는 메서드 수준에서 라우팅 특성을 적용할 수 있습니다.

[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=19-23)]

`List` 메서드 200 OK 응답 코드와 JSON으로 serialize 할 일 항목을 모두 반환 합니다.

와 같은 다양 한 도구를 사용 하 여 새 API 메서드를 테스트할 수 [우체부](https://www.getpostman.com/docs/), 여기에 표시 합니다.

![Todoitems 및 반환 되는 세 가지 항목에 대 한 JSON을 보여 주는 응답의 본문에 대 한 GET 요청을 보여 주는 우체부 콘솔](native-mobile-backend/_static/postman-get.png)

### <a name="creating-items"></a>항목 만들기

일반적으로 HTTP POST 동사에 새 데이터 항목을 만드는 매핑됩니다. `Create` 메서드에 `[HttpPost]` 특성을 적용 하 고 수락는 `ToDoItem` 인스턴스. 이후는 `item` 은 게시물 본문에 전달할 인수,으로 데코레이팅되 어이 매개 변수는 `[FromBody]` 특성입니다.

메서드 내부를 유효성 및 데이터 저장소에서 이전 존재에 대 한 항목이 선택 되는 및을 없는 문제가 발생 한 경우 저장소를 사용 하 여 추가 됩니다. 검사 `ModelState.IsValid` 수행 [유효성 검사 모델](../mvc/models/validation.md), 사용자 입력을 허용 하는 모든 API 메서드에서 수행 해야 합니다.

[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=25-46)]

샘플에서는 모바일 클라이언트에 전달 되는 오류 코드를 포함 하는 열거형을 사용 합니다.

[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=91-99)]

우체부를 사용 하 여 JSON 형식의 요청 본문에 새 개체를 제공 하는 POST 동사를 선택 하 여 새 항목 추가 테스트 합니다. 지정 하는 요청 헤더를 추가할도 해야는 `Content-Type` 의 `application/json`합니다.

![POST 및 응답을 보여 주는 우체부 콘솔](native-mobile-backend/_static/postman-post.png)

메서드는 응답에서 새로 만든된 항목을 반환합니다.

### <a name="updating-items"></a>항목 업데이트

레코드를 수정 이루어진다는 HTTP PUT 요청을 사용 하 여 합니다. 이 변경 외의 `Edit` 메서드는 거의 동일 `Create`합니다. 레코드를 찾을 수 없을 경우, `Edit` 작업 반환 됩니다는 `NotFound` (404) 응답 합니다.

[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=48-69)]

우체부를 테스트 하려면 PUT를 동사를 변경 합니다. 요청 본문에서 업데이트 된 개체 데이터를 지정 합니다.

![PUT 및 응답을 보여 주는 우체부 콘솔](native-mobile-backend/_static/postman-put.png)

이 메서드는 반환 된 `NoContent` (204) 응답 기존 API와 일관성을 위해 로그인이 성공 합니다.

### <a name="deleting-items"></a>항목 삭제

레코드를 삭제 하려면 삭제할 항목의 ID를 전달 하는 서비스에 대 한 삭제 요청 합니다. 업데이트를 사용 하면 존재 하지 않는 항목에 대 한 요청은 수신으로 `NotFound` 응답 합니다. 그렇지 않으면, 요청이 성공 표시 됩니다는 `NoContent` (204) 응답 합니다.

[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=71-88)]

Note 삭제 기능을 테스트할 때 요청 본문에 필요한은 nothing입니다.

![삭제 및 응답을 보여 주는 우체부 콘솔](native-mobile-backend/_static/postman-delete.png)

## <a name="common-web-api-conventions"></a>공통 Web API 규칙

앱에 대 한 백 엔드 서비스를 개발 하는 경우 규칙 또는 처리 일반적인 문제에 대 한 일관 된 집합을 제공 합니다. 예를 들어 위에 표시 된 서비스에서 받은 특정 발견 되지 않은 레코드에 대 한 요청을 `NotFound` 응답 대신 `BadRequest` 응답 합니다. 항상 확인 하는 모델에 바인딩되어 형식에 전달 된이 서비스에 명령을 마찬가지로, `ModelState.IsValid` 반환 하 고는 `BadRequest` 잘못 된 모델 유형에 대 한 합니다.

Api에 대 한 일반적인 정책을 식별 했으면, 일반적으로 캡슐화 할 수 있습니다에 [필터](../mvc/controllers/filters.md)합니다. 에 대 한 자세한 내용은 [ASP.NET Core MVC 응용 프로그램에서 공통 API 정책을 캡슐화 하는 방법을](https://msdn.microsoft.com/magazine/mt767699.aspx)합니다.
