---
title: "ASP.NET Core 및 Mac용 Visual Studio를 사용하여 Web API 만들기"
description: "ASP.NET Core MVC 및 Mac용 Visual Studio를 사용하여 Web API 만들기"
author: rick-anderson
ms.author: riande
ms.date: 09/15/2017
ms.topic: get-started-article
ms.prod: asp.net-core
uid: tutorials/first-web-api-mac
helpviewer_heywords: ASP.NET Core, WebAPI, Web API, REST, mac, macOS, HTTP, Service, HTTP Service
ms.technology: aspnet
keywords: "ASP.NET Core, WebAPI, Web API, REST, mac, macOS, HTTP, 서비스, HTTP 서비스"
manager: wpickett
ms.openlocfilehash: 6835cdefcc001452a3ffc8f4fd6a2f55f7274692
ms.sourcegitcommit: 78d28178345a0eea91556e4cd1adad98b1446db8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2017
---
# <a name="create-a-web-api-with-aspnet-core-mvc-and-visual-studio-for-mac"></a>ASP.NET Core MVC 및 Mac용 Visual Studio를 사용하여 Web API 만들기

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [Mike Wasson](https://github.com/mikewasson)

이 자습서에서는 “할 일” 항목 목록을 관리하기 위한 웹 API를 빌드합니다. UI는 빌드하지 않습니다.

이 자습서는 세 가지 버전이 있습니다.

* macOS: Mac용 Visual Studio를 사용한 Web API(이 자습서)
* Windows: [Windows용 Visual Studio를 사용한 Web API](xref:tutorials/first-web-api)
* macOS, Linux, Windows: [Visual Studio Code를 사용한 Web API](xref:tutorials/web-api-vsc)

<!-- WARNING: The code AND images in this doc are used by uid: tutorials/web-api-vsc, tutorials/first-web-api-mac and tutorials/first-web-api. If you change any code/images in this tutorial, update uid: tutorials/web-api-vsc -->

[!INCLUDE[template files](../includes/webApi/intro.md)]

* 영구 데이터베이스를 사용하는 예제를 보려면 [Mac 또는 Linux의 ASP.NET Core MVC 소개](xref:tutorials/first-mvc-app-xplat/index)를 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

다음을 설치합니다.

- [.NET Core SDK](https://www.microsoft.com/net/core#macos)  
- [Visual Studio for Mac](https://www.visualstudio.com/vs/visual-studio-mac/)

## <a name="create-the-project"></a>프로젝트를 만듭니다.

Visual Studio에서 **파일 > 새 솔루션**을 선택합니다.

![macOS 새 솔루션](first-web-api-mac/_static/sln.png)

**.NET Core App > ASP.NET Core Web API > 다음**을 선택합니다.

![macOS 새 프로젝트 대화 상자](first-web-api-mac/_static/1.png)

**프로젝트 이름**으로 **TodoApi**를 입력하고 [만들기]를 선택합니다.

![구성 대화 상자](first-web-api-mac/_static/2.png)

### <a name="launch-the-app"></a>앱 시작

Visual Studio에서 **실행 > 디버깅 시작**을 선택하여 앱을 시작합니다. Visual Studio가 브라우저를 시작하고 `http://localhost:5000`으로 이동합니다. HTTP 404(찾을 수 없음) 오류가 표시됩니다.  URL을 `http://localhost:port/api/values`로 변경합니다. `ValuesController` 데이터가 표시됩니다.

```
["value1","value2"]
```

### <a name="add-support-for-entity-framework-core"></a>Entity Framework Core에 대한 지원 추가

[Entity Framework Core InMemory](https://docs.microsoft.com/ef/core/providers/in-memory/) 데이터베이스 공급자를 설치합니다. 이 데이터베이스 공급자를 설치하면 Entity Framework Core를 메모리 내 데이터베이스에서 사용할 수 있습니다.

* **프로젝트** 메뉴에서 **NuGet 패키지 추가**를 선택합니다. 

  *  또는 **종속성**을 마우스 오른쪽 단추로 클릭하고 **패키지 추가**를 선택합니다.

* 검색 상자에 `EntityFrameworkCore.InMemory`를 입력합니다.
* `Microsoft.EntityFrameworkCore.InMemory`를 선택하고 **패키지 추가**를 선택합니다.

### <a name="add-a-model-class"></a>모델 클래스 추가

모델은 응용 프로그램에서 데이터를 나타내는 개체입니다. 이 경우 유일한 모델은 할 일 항목입니다.

*Models* 폴더를 추가합니다. 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭합니다. **추가** > **새 폴더**를 선택합니다. 폴더 이름을 *Models*로 지정합니다.

![새 폴더](first-web-api-mac/_static/folder.png)

참고: 프로젝트의 아무 곳에나 모델 클래스를 넣을 수 있지만 일반적으로 *Models* 폴더를 사용합니다.

`TodoItem` 클래스를 추가합니다. *Models* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가 > 새 파일 > 일반 > 빈 클래스**를 선택합니다. 클래스 이름을 `TodoItem`으로 지정하고 **새로 만들기**를 선택합니다.

생성된 코드를 다음으로 바꿉니다.

[!code-csharp[Main](first-web-api/sample/TodoApi/Models/TodoItem.cs)]

`TodoItem`이 만들어질 때 데이터베이스가 `Id`를 생성합니다.

### <a name="create-the-database-context"></a>데이터베이스 컨텍스트 만들기

*데이터베이스 컨텍스트*는 특정 데이터 모델에 맞게 Entity Framework 기능을 조정하는 주 클래스입니다. `Microsoft.EntityFrameworkCore.DbContext` 클래스에서 파생시키는 방식으로 이 클래스를 만듭니다.

`TodoContext` 클래스를 *Models* 폴더에 추가합니다.

[!code-csharp[Main](first-web-api/sample/TodoApi/Models/TodoContext.cs)]

[!INCLUDE[Register the database context](../includes/webApi/register_dbContext.md)]

## <a name="add-a-controller"></a>컨트롤러 추가

솔루션 탐색기의 *Controllers* 폴더에서 `TodoController` 클래스를 추가합니다.

생성된 코드를 다음으로 바꾸고 닫는 중괄호를 추가합니다.

[!INCLUDE[code and get todo items](../includes/webApi/getTodoItems.md)]

### <a name="launch-the-app"></a>앱 시작

Visual Studio에서 **실행 > 디버깅 시작**을 선택하여 앱을 시작합니다. Visual Studio가 브라우저를 시작하고 `http://localhost:port`로 이동합니다. 여기서 *port*는 임의로 선택된 포트 번호입니다. HTTP 404(찾을 수 없음) 오류가 표시됩니다.  URL을 `http://localhost:port/api/values`로 변경합니다. `ValuesController` 데이터가 표시됩니다.

```
["value1","value2"]
```

`Todo` 컨트롤러(`http://localhost:port/api/todo`)로 이동합니다.

```
[{"key":1,"name":"Item1","isComplete":false}]
```

## <a name="implement-the-other-crud-operations"></a>기타 CRUD 작업 구현

`Create`, `Update` 및 `Delete` 메서드를 컨트롤러에 추가합니다. 이러한 메서드는 테마에 대한 변형이므로 코드를 표시하고 주요 차이점을 강조 표시합니다. 코드를 추가하거나 변경한 후 프로젝트를 빌드합니다.

### <a name="create"></a>만들기

[!code-csharp[Main](first-web-api/sample/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

[`[HttpPost]`](/aspnet/core/api/microsoft.aspnetcore.mvc.httppostattribute) 특성으로 나타내는 HTTP POST 메서드입니다. [`[FromBody]`](/aspnet/core/api/microsoft.aspnetcore.mvc.frombodyattribute) 특성은 HTTP 요청 본문에서 할 일 항목 값을 가져오도록 MVC에 지시합니다.

`CreatedAtRoute` 메서드는 서버에서 새 리소스를 만드는 HTTP POST 메서드의 표준 응답인 201 응답을 반환합니다. `CreatedAtRoute`는 응답에 대한 위치 헤더도 추가합니다. 위치 헤더는 새로 만들어진 할 일 항목의 URI를 지정합니다. [10.2.2 201 Created](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)(10.2.2 201 생성됨)를 참조하세요.

### <a name="use-postman-to-send-a-create-request"></a>Postman을 사용하여 만들기 요청 보내기

* 앱을 시작합니다(**실행 > 디버깅 시작**).
* Postman을 시작합니다.

![Postman 콘솔](first-web-api/_static/pmc.png)

* HTTP 메서드를 `POST`로 설정
* **본문** 라디오 단추 선택
* **원시** 라디오 단추 선택
* 형식을 JSON으로 설정
* 키 값 편집기에서 다음과 같이 Todo 항목 입력

```json
{
    "name":"walk dog",
    "isComplete":true
}
```

* **보내기** 선택

* 아래쪽 창에서 [헤더] 탭을 선택하고 **위치** 헤더를 복사합니다.

![Postman 콘솔의 헤더 탭](first-web-api/_static/pmget.png)

위치 헤더 URI를 사용하여 방금 만든 리소스에 액세스합니다. `GetById` 메서드에서 만들어진 `"GetTodo"` 경로를 회수합니다.

```csharp
[HttpGet("{id}", Name = "GetTodo")]
public IActionResult GetById(string id)
```

### <a name="update"></a>업데이트

[!code-csharp[Main](first-web-api/sample/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

`Update`는 `Create`와 비슷하지만 HTTP PUT을 사용합니다. 응답은 [204(콘텐츠 없음)](http://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)입니다. HTTP 사양에 따라 PUT 요청의 경우 클라이언트는 델타만이 아니라 전체 업데이트된 엔터티를 보내야 합니다. 부분 업데이트를 지원하려면 HTTP PATCH를 사용합니다.

```json
{
  "key": 1,
  "name": "walk dog",
  "isComplete": true
}
```

![204(콘텐츠 없음) 응답을 보여주는 Postman 콘솔](first-web-api/_static/pmcput.png)

### <a name="delete"></a>삭제

[!code-csharp[Main](first-web-api/sample/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

응답은 [204(콘텐츠 없음)](http://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)입니다.

![204(콘텐츠 없음) 응답을 보여주는 Postman 콘솔](first-web-api/_static/pmd.png)

## <a name="next-steps"></a>다음 단계

* [컨트롤러 작업에 라우팅](xref:mvc/controllers/routing)
* API 배포에 대한 자세한 내용은 [게시 및 배포](../publishing/index.md)를 참조하세요.
* [샘플 코드 보기 또는 다운로드](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/first-web-api/sample)
* [Postman](https://www.getpostman.com/)
* [Fiddler](https://www.telerik.com/download/fiddler)
