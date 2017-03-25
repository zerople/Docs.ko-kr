---
title: "ASP.NET Core MVC 및 Visual Studio는 웹 API를 빌드 | Microsoft 문서"
author: rick-anderson
description: "ASP.NET 핵심 MVC 및 Visual Studio는 웹 API를 구축 합니다."
keywords: "ASP.NET Core, WebAPI, 웹 API REST"
ms.author: riande
manager: wpickett
ms.date: 03/14/2017
ms.topic: article
ms.assetid: 830b4af5-ed14-423e-9f59-764a6f13a8f6
ms.technology: aspnet
ms.prod: asp.net-core
uid: tutorials/first-web-api
translationtype: Machine Translation
ms.sourcegitcommit: 1894789727a7c3ae66f25040d1a70c4f1e64c3da
ms.openlocfilehash: a8cfabd84113ccd4d9b367f5394102983c031d04
ms.lasthandoff: 03/23/2017

---
# <a name="building-your-first-web-api-with-aspnet-core-mvc-and-visual-studio"></a>ASP.NET Core MVC 및 Visual Studio를 사용 하 여 첫 번째 웹 API를 빌드

여 [Mike Wasson](https://github.com/mikewasson) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)

HTTP를 단순히 웹 페이지를 제공 합니다. 서비스 및 데이터를 노출 하는 Api를 구축 하기 위한 강력한 플랫폼 이기도 합니다. HTTP는 간단 하 고 유연 하며 어디에서 나는입니다. 상상할 수 있는 거의 모든 플랫폼에는 HTTP 라이브러리 HTTP 서비스에는 다양 한 브라우저, 모바일 장치 및 기존 데스크톱 앱을 비롯 한 클라이언트에 연결할 수 있습니다.

이 자습서에서는 할 일 항목의 목록을 관리 하기 위한 간단한 웹 API를 만들어 봅니다. 이 자습서에서는 모든 UI를 빌드할 수 없습니다.

ASP.NET Core에서 웹 Api를 구축 하는 MVC 기본적으로 지원 합니다. 

참고: ASP.NET 코어로 기존 웹 API 앱에 이식 하는 경우 참조 [ASP.NET Web API에서 마이그레이션](xref:migration/webapi)

## <a name="overview"></a>개요

만들어야 하는 API는 다음과 같습니다.  


|API | 설명    | 요청 본문    | 응답 본문   |
|--- | ---- | ---- | ---- |
|/Api/todo 가져오기  | 모든 할 일 항목 가져오기 | 없음 | 할 일 항목의 배열|
|가져오기 /api/todo / {id}  | ID로 항목 가져오기 | 없음 | 할 일 항목|
|게시/api/todo | 새 항목을 추가합니다. | 할 일 항목  | 할 일 항목 |
|PUT /api/todo / {id} | 기존 항목을 업데이트 합니다.&nbsp;  | 할 일 항목 |
|/Api/todo / {id} 삭제&nbsp;  &nbsp; | 항목 삭제&nbsp;  &nbsp;  | 없음 없는 요청 본문-  | 없음|
     
<br>     
    
다음 다이어그램에서는 응용 프로그램의 기본 디자인을 보여 줍니다.

![클라이언트 요청을 제출 및 오른쪽에 그려진 상자 응용 프로그램에서 응답을 받고 왼쪽 상자에 표시 됩니다. 응용 프로그램 상자 내에서&3; 개의 상자에는 컨트롤러, 모델 및 데이터 액세스 계층 나타냅니다. 응용 프로그램의 컨트롤러에 요청 하는 및 컨트롤러와 데이터 액세스 계층 간의 읽기/쓰기 작업이 수행 됩니다. 모델은 직렬화 되며 응답에서 클라이언트로 반환 됩니다.](first-web-api/_static/architecture.png)

* 클라이언트는 어떤 웹 API (브라우저, 모바일 앱 등)를 사용 합니다. 이 자습서에서는 클라이언트를 작성 하지 했습니다. 사용 하 여 [Postman](https://www.getpostman.com/) 앱을 테스트 합니다.

* A *모델* 는 응용 프로그램에서 데이터를 나타내는 개체입니다. 이 경우 모델만 할 일 항목입니다. 모델은 간단한 C# 클래스 (Poco)으로 표시 됩니다.

* A *컨트롤러* HTTP를 처리 하는 개체를 요청 하 고 HTTP 응답을 만듭니다. 이 응용 프로그램에 단일 컨트롤러를 갖습니다.

* 이 자습서를 단순하게 유지 하기 위해 응용 프로그램에는 영구 데이터베이스를 사용 하지 않습니다. 대신, 메모리 내 데이터베이스에 할 일 항목을 저장합니다. 

### <a name="create-the-project"></a>프로젝트를 만듭니다.

Visual Studio에서 선택 **파일** 메뉴 > **새로** > **프로젝트**합니다.

선택 된 **ASP.NET 핵심 웹 응용 프로그램 (.NET Core)** 프로젝트 템플릿. 프로젝트 이름을 `TodoApi` 선택한 **확인**합니다.

![새 프로젝트 대화 상자](first-web-api/_static/new-project.png)

에 **새 ASP.NET 핵심 웹 응용 프로그램 (.NET Core)-TodoApi** 대화 상자에서는 **웹 API** 템플릿. 선택 **확인**합니다. 마십시오 **하지** 선택 **Docker 지원 활성화**합니다.

![ASP.NET 핵심 서식 파일에서 선택한 웹 API 프로젝트 템플릿 사용 하 여 새 ASP.NET 웹 응용 프로그램 대화 상자](first-web-api/_static/web-api-project.png)

### <a name="add-support-for-entity-framework-core"></a>Entity Framework 코어에 대 한 지원 추가

설치는 [Entity Framework 코어 InMemory](https://docs.microsoft.com/en-us/ef/core/providers/in-memory/) 데이터베이스 공급자입니다. 이 데이터베이스 공급자에는 Entity Framework Core 메모리 내 데이터베이스와 함께 사용 될 수 있습니다.

편집 된 *TodoApi.csproj* 파일입니다. 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭 합니다. 선택 **TodoApi.csproj 편집**합니다. 에 `ItemGroup` 요소를 강조 표시 된 추가 `PackageReference`:

[!code-xml[주](first-web-api/sample/TodoApi/TodoApi.csproj?highlight=1&range=14-15)]

### <a name="add-a-model-class"></a>모델 클래스 추가

모델은 응용 프로그램에서 데이터를 나타내는 개체입니다. 이 경우 모델만 할 일 항목입니다.

"Models" 라는 폴더를 추가 합니다. 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭 합니다. Select **Add** > **New Folder**. 폴더 이름을 *모델*합니다.

참고: 넣으면 모델 클래스 아무 곳 이나 프로젝트를 하지만 *모델* 폴더는 일반적으로 사용 합니다.

추가 `TodoItem` 클래스입니다. 마우스 오른쪽 단추로 클릭는 *모델* 폴더와 선택 **추가** > **클래스**합니다. 클래스의 이름을 `TodoItem` 선택한 **추가**합니다.

사용 하 여 생성 된 코드를 바꿉니다.

[!code-csharp[주](first-web-api/sample/TodoApi/Models/TodoItem.cs)]

* `[Key]` 데이터 주석 속성을 나타내는 `Key`, 고유 식별자입니다.
* `[DatabaseGenerated`키 (응용 프로그램 아님) 생성 하는 데이터베이스를 지정 합니다.
* `DatabaseGeneratedOption.Identity`행이 삽입 될 때 데이터베이스에서 정수 키를 생성할지를 지정 합니다. 

### <a name="create-the-database-context"></a>데이터베이스 컨텍스트 만들기

*데이터베이스 컨텍스트* 는 지정된 된 데이터 모델에 대 한 Entity Framework 기능을 조정 하는 주 클래스입니다. 이 클래스에서 파생 시켜 만들는 `Microsoft.EntityFrameworkCore.DbContext` 클래스입니다.

추가 `TodoContext` 클래스입니다. 마우스 오른쪽 단추로 클릭는 *모델* 폴더와 선택 **추가** > **클래스**합니다. 클래스의 이름을 `TodoContext` 선택한 **추가**합니다.

[!code-csharp[주](first-web-api/sample/TodoApi/Models/TodoContext.cs)]

## <a name="add-a-repository-class"></a>리포지토리 클래스를 추가 합니다.

A *저장소* 는 데이터 계층을 캡슐화 하는 개체입니다. *리포지토리* 를 검색 하 고 데이터를 엔터티 모델에 매핑 논리를 포함 합니다. 리포지토리 코드를 만들기는 *모델* 폴더입니다.

명명 된 저장소 인터페이스 정의 `ITodoRepository`합니다. 클래스 템플릿을 사용 하 여 (**새 항목 추가**  > **클래스**).

[!code-csharp[주](first-web-api/sample/TodoApi/Models/ITodoRepository.cs)]

이 인터페이스는 기본 CRUD 작업을 정의합니다.

추가 `TodoRepository` 클래스를 구현 하는 `ITodoRepository`:

[!code-csharp[주](first-web-api/sample/TodoApi/Models/TodoRepository.cs)]

컴파일러 오류 확인을 위해 앱을 빌드하십시오.

## <a name="register-the-repository"></a>저장소를 등록 합니다.

저장소 인터페이스를 정의 하 여 리포지토리 클래스를 사용 하는 MVC 컨트롤러에서 분리할 수 있습니다. 인스턴스화하는 대신 한 `TodoRepository` 것을 주입 하는 컨트롤러 내는 `ITodoRepository` 에 대 한 ASP.NET 코어의 기본 제공 지원을 사용 하 여 [종속성 주입](xref:fundamentals/dependency-injection)합니다.

이 방법을 사용 하면 보다 쉽게 단위에 컨트롤러를 테스트 합니다. 단위 테스트는 mock 또는 스텁 버전의 주입 해야 `ITodoRepository`합니다. 이런 방식으로 테스트 컨트롤러 논리와 데이터 액세스 계층에 구체적으로 대상이 됩니다.

저장소 컨트롤러에 넣기, 하려면 DI 컨테이너를 사용 하 여 등록 해야 합니다. 열기는 *Startup.cs* 파일입니다. 

에 `ConfigureServices` 메서드를 강조 표시 된 코드를 추가 합니다.

[!code-csharp[주](first-web-api/sample/TodoApi/Startup.cs?name=snippet_AddSingleton&highlight=11)]

## <a name="add-a-controller"></a>컨트롤러 추가

솔루션 탐색기에서 마우스 오른쪽 단추로 클릭는 *컨트롤러* 폴더입니다. Select **Add** > **New Item**. 에 **새 항목 추가** 대화 상자에서는 **웹 API 컨트롤러 클래스** 템플릿. 클래스의 이름을 `TodoController`합니다.

![새 항목 추가 대화 상자와 컨트롤러의 검색 상자 및 웹 API 컨트롤러를 선택](first-web-api/_static/new-project.png)


생성된 된 코드를 다음으로 바꿉니다 (및 닫는 중괄호를 추가 합니다.):

[!code-csharp[주](first-web-api/sample/TodoApi/Controllers/TodoController.cs?name=snippet_todo1)]

이 빈 컨트롤러 클래스를 정의 합니다. 다음 섹션에서 API를 구현 하는 메서드를 추가 합니다.

## <a name="getting-to-do-items"></a>할 일 항목 가져오기

할 일 항목을 가져오려면 다음 메서드를 추가 `TodoController` 클래스입니다.

[!code-csharp[주](first-web-api/sample/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

이러한 메서드는 두 가지 GET 메서드가 구현 합니다.

* `GET /api/todo`

* `GET /api/todo/{id}`

다음에 대 한 예제에서는 HTTP 응답은는 `GetAll` 메서드:

```
HTTP/1.1 200 OK
   Content-Type: application/json; charset=utf-8
   Server: Microsoft-IIS/10.0
   Date: Thu, 18 Jun 2015 20:51:10 GMT
   Content-Length: 82

   [{"Key":"4f67d7c5-a2a9-4aae-b030-16003dd829ae","Name":"Item1","IsComplete":false}]
   ```

이 자습서 뒷부분에서 설명할 것를 보는 방법을 사용 하 여 HTTP 응답 [Postman](https://www.getpostman.com/)합니다.

### <a name="routing-and-url-paths"></a>라우팅 및 URL 경로

`[HttpGet]` 특성은 HTTP GET 메서드를 지정 합니다. 각 방법에 대 한 URL 경로 다음과 같이 구성 됩니다.

* 템플릿 문자열을 컨트롤러의 route 특성 사용`[Route("api/[controller]")]`
* "[Controller]를"을 컨트롤러 클래스 이름 "컨트롤러" 접미사는 컨트롤러의 이름을 바꿉니다. 이 샘플에 대 한 컨트롤러 클래스 이름은 **Todo**은 "todo" 컨트롤러와 루트 이름입니다. ASP.NET Core [라우팅](xref:mvc/controllers/routing) 는 대 소문자를 구분 하지 않습니다.
* 하는 경우는 `[HttpGet]` 특성에 문자열, 경로에 추가 하는 서식 파일입니다. 이 샘플에는 템플릿 문자열을 사용 하지 않습니다.

에 `GetById` 메서드:

```csharp
[HttpGet("{id}", Name = "GetTodo")]
public IActionResult GetById(string id)
```

`"{id}"`ID에 대 한 자리 표시자 변수가 `todo` 항목입니다. 때 `GetById` 은 "{id}"의 값의 url을 메서드의 할당 호출 `id` 매개 변수입니다.

`Name = "GetTodo"`명명된 된 경로 만들고 HTTP 응답에이 경로를 연결할 수 있습니다. 필자 나중에 설명 것에 대 한 예입니다. 참조 [컨트롤러 작업에 대 한 라우팅을](xref:mvc/controllers/routing) 자세한 정보.

### <a name="return-values"></a>반환 값

`GetAll` 메서드가 반환 되는 `IEnumerable`합니다. MVC는 자동으로 개체를 serialize [JSON](http://www.json.org/) 하 고 응답 메시지의 본문에 JSON을 기록 합니다. 응답 코드는이 메서드는 200에 대 한 처리 되지 않은 예외가 되었다고 가정 합니다. (예외가 발생할 때마다 5xx 오류로 변환 됩니다.)

반면에 `GetById` 메서드는 보다 일반적인 `IActionResult` 광범위 한 반환 형식 나타내는 형식입니다. `GetById`서로 다른 두 반환 형식에 있습니다.

* 요청된 된 ID 일치 하는 항목이 없는 경우 404 오류가 반환 됩니다.  반환 하 여 이렇게 `NotFound`합니다.

* 메서드가는 JSON 응답 본문을 포함 하는 200이 반환 됩니다. 반환 하 여 이렇게는`ObjectResult`

  
### <a name="launch-the-app"></a>응용 프로그램을 실행

Visual Studio에서 응용 프로그램을 실행 하려면 CTRL + f&5;를 누릅니다. Visual Studio로 이동 하며 브라우저 시작 `http://localhost:port/api/values`여기서 *포트* 임의로 선택한 포트 번호입니다. Chrome, 모서리 또는 Firefox를 사용 하는 경우 데이터가 표시 됩니다. IE가 열기는 또는 저장 IE를 사용 하는 경우는 *values.json* 파일입니다. 탐색 하는 `Todo` 방금 만든 컨트롤러 `http://localhost:port/api/todo`합니다.

## <a name="implement-the-other-crud-operations"></a>다른 CRUD 작업을 구현 합니다.

추가한 `Create`, `Update`, 및 `Delete` 컨트롤러 메서드. 이러한 변형이 테마에만 코드를 보여 고 주요 차이점을 강조 표시 합니다. 추가 또는 코드를 변경한 후 프로젝트를 빌드하십시오.

### <a name="create"></a>만들기

[!code-csharp[주](first-web-api/sample/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

이으로 표시 하는 HTTP POST 메서드는 [ `[HttpPost]` ](https://docs.asp.net/projects/api/en/latest/autoapi/Microsoft/AspNetCore/Mvc/HttpPostAttribute/index.html) 특성입니다. [ `[FromBody]` ](https://docs.asp.net/projects/api/en/latest/autoapi/Microsoft/AspNetCore/Mvc/FromBodyAttribute/index.html) 특성은 MVC HTTP 요청의 본문에서 할 일 항목의 값을 가져올 수 있습니다.

`CreatedAtRoute` 메서드를 서버에 새 리소스를 만드는 HTTP POST 메서드에 대 한 표준 응답은 201 응답을 반환 합니다. `CreatedAtRoute`또한 위치 헤더를 응답에 추가합니다. Location 헤더는 새로 만든된 할 일 항목의 URI를 지정 합니다. 참조 [10.2.2 201 생성 됨](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)합니다.

### <a name="use-postman-to-send-a-create-request"></a>Postman을 사용 하 여 만들기 요청을 보내려면

![Postman 콘솔](first-web-api/_static/pmc.png)

* HTTP 메서드 설정`POST`
* 선택 된 **본문** 라디오 단추
* 선택 된 **원시** 라디오 단추
* 형식을 JSON으로 설정
* 키-값 편집기에서 할 일 항목을 같은 입력 
```JSON
{
    "name":"walk dog",
    "isComplete":true
}```

* Select **Send**

Select the Headers tab in the lower pane and copy the **Location** header:

![Headers tab of the Postman console](first-web-api/_static/pmget.png)

You can use the Location header URI to access the resource you just created. Recall the `GetById` method created the `"GetTodo"` named route:

```csharp
[HttpGet("{id}", Name = "GetTodo")]
public IActionResult GetById(string id)
```

### <a name="update"></a>업데이트

[!code-csharp[주](first-web-api/sample/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

`Update`유사한 `Create`, 하지만 HTTP PUT을 사용 합니다. 응답은 [204 (콘텐츠 없음)](http://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)합니다. HTTP 사양에 따라는 PUT 요청을 보내도록 델타 뿐 아니라 전체 업데이트 된 엔터티를 클라이언트에 필요 합니다. 부분 업데이트를 지원 하려면 HTTP PATCH를 사용 합니다.

![204 (콘텐츠 없음) 응답을 보여 주는 postman 콘솔](first-web-api/_static/pmcput.png)

### <a name="delete"></a>삭제

[!code-csharp[주](first-web-api/sample/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

응답은 [204 (콘텐츠 없음)](http://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)합니다.

![204 (콘텐츠 없음) 응답을 보여 주는 postman 콘솔](first-web-api/_static/pmd.png)

## <a name="next-steps"></a>다음 단계

* 에 네이티브 모바일 응용 프로그램용 백 엔드 만들기에 대 한 자세한 참조 [네이티브 모바일 응용 프로그램에 대 한 백 엔드 서비스를 만드는](xref:mobile/native-mobile-backend)합니다.

* [컨트롤러 작업에 대 한 라우팅을](xref:mvc/controllers/routing)

* API를 배포 하는 방법에 대 한 정보를 참조 하십시오. [게시 및 배포](../publishing/index.md)합니다.

* [샘플 코드 보기 또는 다운로드](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/first-web-api/sample)

* [Postman](https://www.getpostman.com/)

* [Fiddler](http://www.fiddler2.com/fiddler2/)

