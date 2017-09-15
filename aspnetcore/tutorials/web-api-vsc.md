---
title: "ASP.NET Core 및 VS Code를 사용하여 Web API 만들기"
author: rick-anderson
description: "ASP.NET Core MVC 및 Visual Studio Code를 사용하여 macOS, Linux 또는 Windows에서 웹 API 빌드"
keywords: "ASP.NET Core, WebAPI, Web API, REST, Mac, Linux, HTTP, 서비스, HTTP 서비스, VS Code"
ms.author: riande
manager: wpickett
ms.date: 5/24/2017
ms.topic: get-started-article
ms.assetid: 830b4bf5-dd14-423e-9f59-764a6f13a8f6
ms.technology: aspnet
ms.prod: asp.net-core
uid: tutorials/web-api-vsc
ms.openlocfilehash: abe088f2c9df94135209ce71540e6b345186ee70
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/11/2017
---
# <a name="create-a-web-api-with-aspnet-core-mvc-and-visual-studio-code-on-linux-macos-and-windows"></a>Linux, macOS 및 Windows에서 ASP.NET Core MVC 및 Visual Studio Code를 사용하여 Web API 만들기

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [Mike Wasson](https://github.com/mikewasson)

이 자습서에서는 “할 일” 항목 목록을 관리하기 위한 웹 API를 빌드합니다. UI는 빌드하지 않습니다.

이 자습서는 세 가지 버전이 있습니다.

* macOS, Linux, Windows: Visual Studio Code를 사용한 Web API(이 자습서)
* macOS: [Mac용 Visual Studio를 사용한 Web API](xref:tutorials/first-web-api-mac)
* Windows: [Windows용 Visual Studio를 사용한 Web API](xref:tutorials/first-web-api)

<!-- WARNING: The code AND images in this doc are used by uid: tutorials/web-api-vsc, tutorials/first-web-api-mac and tutorials/first-web-api. If you change any code/images in this tutorial, update uid: tutorials/web-api-vsc -->

[!INCLUDE[template files](../includes/webApi/intro.md)]

## <a name="set-up-your-development-environment"></a>개발 환경 설정

다운로드 및 설치:
- [.NET Core](https://microsoft.com/net/core)
- [Visual Studio Code](https://code.visualstudio.com)
- Visual Studio Code [C# 확장](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)

## <a name="create-the-project"></a>프로젝트를 만듭니다.

콘솔에서 다음 명령을 실행합니다.

```console
mkdir TodoApi
cd TodoApi
dotnet new webapi
```

VS Code(Visual Studio Code)에서 *TodoApi* 폴더를 열고 *Startup.cs* 파일을 선택합니다.

- 다음 **경고** 메시지에 대해 **예**를 선택합니다. “빌드 및 디버그에 필요한 자산이 ‘TodoApi’에서 누락되었습니다. 추가할까요?”
- 다음 **정보** 메시지에 대해 **복원**을 선택합니다. “확인되지 않은 종속성이 있습니다.”

<!-- uid: tutorials/first-mvc-app-xplat/start-mvc uses the pic below. If you change it, make sure it's consistent -->

![빌드 및 디버그에 필요한 자산이 ‘TodoApi’에서 누락되었습니다. 경고가 있는 VS Code 추가할까요? 다시 표시 안 함, 지금 아님, 예 및 정보 표시 - 확인되지 않은 종속성이 있음 - 복원 - 닫기](web-api-vsc/_static/vsc_restore.png)

**디버그**(F5) 키를 눌러 프로그램을 빌드하고 실행합니다. 브라우저에서 http://localhost:5000/api/값으로 이동합니다. 다음이 표시됩니다.

`["value1","value2"]`

VS Code 사용에 대한 팁은 [Visual Studio Code 도움말](#visual-studio-code-help)을 참조하세요.

## <a name="add-support-for-entity-framework-core"></a>Entity Framework Core에 대한 지원 추가

*TodoApi.csproj* 파일을 편집하여 [Entity Framework Core InMemory](https://docs.microsoft.com/ef/core/providers/in-memory/) 데이터베이스 공급자를 설치합니다. 이 데이터베이스 공급자를 설치하면 Entity Framework Core를 메모리 내 데이터베이스에서 사용할 수 있습니다.

[!code-xml[Main](web-api-vsc/sample/TodoApi/TodoApi.csproj?highlight=12)]

`dotnet restore`를 실행하여 다운로드하고 EF Core InMemory DB 공급자를 설치합니다. 터미널에서 `dotnet restore`를 실행하거나 VS Code에 `⌘⇧P`(macOS) 또는 `Ctrl+Shift+P`(Linux)를 입력한 다음 **.NET**을 입력합니다. **.NET: 복원 패키지**를 선택합니다.

## <a name="add-a-model-class"></a>모델 클래스 추가

모델은 응용 프로그램에서 데이터를 나타내는 개체입니다. 이 경우 유일한 모델은 할 일 항목입니다.

*Models* 폴더를 추가합니다. 프로젝트의 아무 곳에나 모델 클래스를 넣을 수 있지만 일반적으로 *Models* 폴더를 사용합니다.

`TodoItem` 클래스를 다음 코드로 추가합니다.

[!code-csharp[Main](first-web-api/sample/TodoApi/Models/TodoItem.cs)]

`TodoItem`이 만들어질 때 데이터베이스가 `Id`를 생성합니다.

## <a name="create-the-database-context"></a>데이터베이스 컨텍스트 만들기

*데이터베이스 컨텍스트*는 특정 데이터 모델에 맞게 Entity Framework 기능을 조정하는 주 클래스입니다. `Microsoft.EntityFrameworkCore.DbContext` 클래스에서 파생시키는 방식으로 이 클래스를 만듭니다.

*Models* 폴더에 `TodoContext` 클래스를 추가합니다.

[!code-csharp[Main](first-web-api/sample/TodoApi/Models/TodoContext.cs)]

[!INCLUDE[Register the database context](../includes/webApi/register_dbContext.md)]

## <a name="add-a-controller"></a>컨트롤러 추가

*Controllers* 폴더에 `TodoController`라는 클래스를 만듭니다. 다음 코드를 추가합니다.

[!INCLUDE[code and get todo items](../includes/webApi/getTodoItems.md)]

### <a name="launch-the-app"></a>앱 시작

VS Code에서 F5 키를 눌러 앱을 시작합니다. http://localhost:5000/api/할 일로 이동합니다(방금 만든 `Todo` 컨트롤러).

[!INCLUDE[last part of web API](../includes/webApi/end.md)]

## <a name="visual-studio-code-help"></a>Visual Studio Code 도움말

- [시작](https://code.visualstudio.com/docs)
- [디버깅](https://code.visualstudio.com/docs/editor/debugging)
- [통합 터미널](https://code.visualstudio.com/docs/editor/integrated-terminal)
- [바로 가기 키](https://code.visualstudio.com/docs/getstarted/keybindings#_keyboard-shortcuts-reference)

  - [Mac 바로 가기 키](https://go.microsoft.com/fwlink/?linkid=832143)
  - [Linux 바로 가기 키](https://go.microsoft.com/fwlink/?linkid=832144)
  - [Windows 바로 가기 키](https://go.microsoft.com/fwlink/?linkid=832145)

[!INCLUDE[next steps](../includes/webApi/next.md)]


