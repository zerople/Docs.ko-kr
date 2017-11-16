---
title: "Mac, Linux 또는 Windows에서 ASP.NET Core MVC 소개"
author: rick-anderson
description: "Mac, Linux 또는 Windows에서 ASP.NET Core MVC 및 Visual Studio Code 시작"
keywords: ASP.NET Core,MVC,VS Code,Visual Studio Code,Mac,Linux,Windows
ms.author: riande
manager: wpickett
ms.date: 07/07/2017
ms.topic: get-started-article
ms.assetid: 1d18b589-1638-4dc6-1638-fb0f41998d78
ms.technology: aspnet
ms.prod: asp.net-core
uid: tutorials/first-mvc-app-xplat/start-mvc
ms.openlocfilehash: 87ce5dca011a7bc37d34799db159d933c158cba1
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2017
---
# <a name="getting-started-with-aspnet-core-mvc--on-mac-linux-or-windows"></a>Mac, Linux 또는 Windows에서 ASP.NET Core MVC 시작

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)

이 자습서에서는 [Visual Studio Code](https://code.visualstudio.com)(VS Code)를 사용하여 ASP.NET Core MVC 웹앱을 빌드하는 기본 사항에 대해 알아봅니다. 이 자습서에서는 VS 코드를 잘 알고 있다고 가정합니다. 자세한 내용은 [VS 코드 시작](https://code.visualstudio.com/docs) 및 [Visual Studio Code 도움말](#visual-studio-code-help)을 참조하세요. [!INCLUDE[consider RP](../../includes/razor.md)]

이 자습서는 세 가지 버전이 있습니다.

* macOS: [Mac용 Visual Studio를 사용하여 ASP.NET Core MVC 앱 만들기](xref:tutorials/first-mvc-app-mac/start-mvc)
* Windows: [Visual Studio를 사용하여 ASP.NET Core MVC 앱 만들기](xref:tutorials/first-mvc-app/start-mvc)
* macOS, Linux 및 Windows: [Visual Studio Code를 사용하여 ASP.NET Core MVC 앱 만들기](xref:tutorials/first-mvc-app-xplat/start-mvc) 

## <a name="install-vs-code-and-net-core"></a>VS Code 및 .NET Core 설치 

이 자습서에서는 [.NET Core 2.0.0 SDK](https://www.microsoft.com/net/core) 이상이 필요합니다. ASP.NET Core 1.1 버전에 대해서는 [PDF](https://github.com/aspnet/Docs/blob/master/aspnetcore/tutorials/first-mvc-app-mac/start-mvc/8-23-17.pdf)를 참조하세요.

다음을 설치합니다.

* [.NET Core 2.0.0 SDK](https://www.microsoft.com/net/core) 이상.
* [Visual Studio Code](https://code.visualstudio.com)
* VS Code [C# 확장](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) 

## <a name="create-a-web-app-with-dotnet"></a>Dotnet와 웹앱 만들기

터미널에서 다음 명령을 실행합니다.

```console
mkdir MvcMovie
cd MvcMovie
dotnet new mvc
```

VS Code(Visual Studio Code)에서 *MvcMovie* 폴더를 열고 *Startup.cs* 파일을 선택합니다.

- 다음 **경고** 메시지에 대해 **예**를 선택합니다. “빌드 및 디버그에 필요한 자산이 'MvcMovie'에서 누락되었습니다. 추가할까요?”
- 다음 **정보** 메시지에 대해 **복원**을 선택합니다. “확인되지 않은 종속성이 있습니다.”

![VS Code, 경고: 빌드 및 디버그에 필요한 자산이 'MvcMovie'에서 누락되었습니다.  추가할까요?” 다시 표시 안 함, 지금 아님, 예 및 정보 표시 - 확인되지 않은 종속성이 있음 - 복원 - 닫기](../web-api-vsc/_static/vsc_restore.png)

**디버그**(F5) 키를 눌러 프로그램을 빌드하고 실행합니다.

![앱 실행](../first-mvc-app/start-mvc/_static/1.png)

VS Code가 [Kestrel](xref:fundamentals/servers/kestrel) 웹 서버를 시작하고 앱을 실행합니다. 주소 표시줄에 `localhost:5000`이 표시되고 `example.com` 등은 표시되지 않음을 알 수 있습니다. 그 이유는 `localhost`가 로컬 컴퓨터의 표준 이름이기 때문입니다.

기본 템플릿은 작동하는 **홈, 정보** 및 **연락처** 링크를 제공합니다. 위의 브라우저 이미지에는 이러한 링크가 표시되지 않습니다. 브라우저 크기에 따라 탐색 아이콘을 클릭하여 링크를 표시해야 할 수 있습니다.

![오른쪽 위의 탐색 아이콘](../first-mvc-app/start-mvc/_static/2.png)

이 자습서의 다음 부분에서는 MVC에 대해 알아보고 일부 코드 작성을 시작합니다.

## <a name="visual-studio-code-help"></a>Visual Studio Code 도움말

- [시작](https://code.visualstudio.com/docs)
- [디버깅](https://code.visualstudio.com/docs/editor/debugging)
- [통합 터미널](https://code.visualstudio.com/docs/editor/integrated-terminal)
- [바로 가기 키](https://code.visualstudio.com/docs/getstarted/keybindings#_keyboard-shortcuts-reference)

  - [Mac 바로 가기 키](https://code.visualstudio.com/shortcuts/keyboard-shortcuts-macos.pdf)
  - [Linux 바로 가기 키](https://code.visualstudio.com/shortcuts/keyboard-shortcuts-linux.pdf)
  - [Windows 바로 가기 키](https://code.visualstudio.com/shortcuts/keyboard-shortcuts-windows.pdf)

>[!div class="step-by-step"]
[다음 - 컨트롤러 추가](adding-controller.md)
