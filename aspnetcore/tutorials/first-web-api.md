---
title: "ASP.NET Core 및 Windows용 Visual Studio를 사용하여 Web API 만들기"
author: rick-anderson
description: "ASP.NET Core MVC 및 Windows용 Visual Studio를 사용하여 Web API 빌드"
keywords: "ASP.NET Core, WebAPI, Web API, REST, HTTP, 서비스, HTTP 서비스"
ms.author: riande
manager: wpickett
ms.date: 8/15/2017
ms.topic: get-started-article
ms.technology: aspnet
ms.prod: asp.net-core
uid: tutorials/first-web-api
ms.openlocfilehash: c57c73c6f9c60874ef88749b838ed1cc1d353ead
ms.sourcegitcommit: 7fef13045e98d716c589a2982613dad261694a65
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2017
---
#<a name="create-a-web-api-with-aspnet-core-and-visual-studio-for-windows"></a>ASP.NET Core 및 Windows용 Visual Studio를 사용하여 Web API 만들기

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [Mike Wasson](https://github.com/mikewasson)

이 자습서에서는 “할 일” 항목 모음을 관리하기 위한 Web API를 빌드합니다. UI는 빌드하지 않습니다.

이 자습서는 세 가지 버전이 있습니다.

* Windows: Windows용 Visual Studio를 사용한 Web API(이 자습서)
* macOS: [Mac용 Visual Studio를 사용한 Web API](xref:tutorials/first-web-api-mac)
* macOS, Linux, Windows: [Visual Studio Code를 사용한 Web API](xref:tutorials/web-api-vsc)

<!-- WARNING: The code AND images in this doc are used by uid: tutorials/web-api-vsc, tutorials/first-web-api-mac and tutorials/first-web-api. If you change any code/images in this tutorial, update uid: tutorials/web-api-vsc -->

[!INCLUDE[intro to web API](../includes/webApi/intro.md)]

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE[install 2.0](../includes/install2.0.md)]

ASP.NET Core 1.1 버전에 대해서는 [이 PDF](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/first-web-api/_static/_webAPI.pdf)를 참조하세요.

## <a name="create-the-project"></a>프로젝트를 만듭니다.

Visual Studio에서 **파일** 메뉴 > **새로 만들기** > **프로젝트**를 선택합니다.

**ASP.NET Core 웹 응용 프로그램(.NET Core)** 프로젝트 템플릿을 선택합니다. 프로젝트 이름을 `TodoApi`로 지정하고 **확인**을 선택합니다.

![새 프로젝트 대화 상자](first-web-api/_static/new-project.png)

**새 ASP.NET Core 웹 응용 프로그램 - TodoApi** 대화 상자에서 **Web API** 템플릿을 선택합니다. **확인**을 선택합니다. **Docker 지원 사용**을 선택하지 **마세요**.

![ASP.NET Core 템플릿에서 Web API 프로젝트 템플릿이 선택된 새 ASP.NET 웹 응용 프로그램 대화 상자](first-web-api/_static/web-api-project.png)

### <a name="launch-the-app"></a>앱 시작

Visual Studio에서 CTRL+F5를 눌러 앱을 시작합니다. Visual Studio가 브라우저를 시작하고 `http://localhost:port/api/values`로 이동합니다. 여기서 *port*는 임의로 선택된 포트 번호입니다. Chrome, Edge 및 Firefox에는 다음 내용이 표시됩니다.

```
["value1","value2"]
``` 

### <a name="add-a-model-class"></a>모델 클래스 추가

모델은 응용 프로그램에서 데이터를 나타내는 개체입니다. 이 경우 유일한 모델은 할 일 항목입니다.

“Models” 폴더를 추가합니다. 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭합니다. **추가** > **새 폴더**를 선택합니다. 폴더 이름을 *Models*로 지정합니다.

참고: 모델 클래스는 프로젝트의 아무 곳에나 이동할 수 있지만 일반적으로 *Models* 폴더를 사용합니다.

`TodoItem` 클래스를 추가합니다. *Models* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **클래스**를 선택합니다. 클래스 이름을 `TodoItem`으로 지정하고 **추가**를 선택합니다.

생성된 코드를 다음으로 바꿉니다.

[!code-csharp[Main](first-web-api/sample/TodoApi/Models/TodoItem.cs)]

`TodoItem`이 만들어질 때 데이터베이스가 `Id`를 생성합니다.

### <a name="create-the-database-context"></a>데이터베이스 컨텍스트 만들기

*데이터베이스 컨텍스트*는 특정 데이터 모델에 맞게 Entity Framework 기능을 조정하는 주 클래스입니다. `Microsoft.EntityFrameworkCore.DbContext` 클래스에서 파생시키는 방식으로 이 클래스를 만듭니다.

`TodoContext` 클래스를 추가합니다. *Models* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **클래스**를 선택합니다. 클래스 이름을 `TodoContext`로 지정하고 **추가**를 선택합니다.

생성된 코드를 다음으로 바꿉니다.

[!code-csharp[Main](first-web-api/sample/TodoApi/Models/TodoContext.cs)]

[!INCLUDE[Register the database context](../includes/webApi/register_dbContext.md)]

### <a name="add-a-controller"></a>컨트롤러 추가

솔루션 탐색기에서 *Controllers* 폴더를 마우스 오른쪽 단추로 클릭합니다. **추가** > **새 항목**을 선택합니다. **새 항목 추가** 대화 상자에서 **Web API 컨트롤러 클래스** 템플릿을 선택합니다. 클래스 이름을 `TodoController`로 지정합니다.

![검색 상자의 컨트롤러 및 Web API 컨트롤러가 선택된 새 항목 추가 대화 상자](first-web-api/_static/new_controller.png)

생성된 코드를 다음으로 바꿉니다.

[!INCLUDE[code and get todo items](../includes/webApi/getTodoItems.md)]
  
### <a name="launch-the-app"></a>앱 시작

Visual Studio에서 CTRL+F5를 눌러 앱을 시작합니다. Visual Studio가 브라우저를 시작하고 `http://localhost:port/api/values`로 이동합니다. 여기서 *port*는 임의로 선택된 포트 번호입니다. Chrome, Edge 또는 Firefox를 사용할 경우 데이터가 표시됩니다. IE를 사용할 경우 IE에서 *values.json* 파일을 열거나 저장할지 묻는 메시지를 표시합니다. 방금 만든 `Todo` 컨트롤러(`http://localhost:port/api/todo`)로 이동합니다.

[!INCLUDE[last part of web API](../includes/webApi/end.md)]

[!INCLUDE[next steps](../includes/webApi/next.md)]

