---
title: "ASP.NET Core MVC 및 Mac용 Visual Studio 시작"
author: rick-anderson
description: "ASP.NET Core MVC 및 Visual Studio 시작"
keywords: "ASP.NET Core, MVC, Mac용 Visual Studio, Entity Framework"
ms.author: riande
manager: wpickett
ms.date: 8/23/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: tutorials/first-mvc-app-mac/start-mvc
ms.openlocfilehash: 028d6d3246908a9cd44a6834449d2fdbc9cae0b8
ms.sourcegitcommit: 9cdbfd0d670d70b9c354216aabee260c52dad5ee
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/12/2017
---
# <a name="getting-started-with-aspnet-core-mvc-and-visual-studio-for-mac"></a>ASP.NET Core MVC 및 Mac용 Visual Studio 시작

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)

이 자습서에서는 [Mac용 Visual Studio](https://www.visualstudio.com/vs/visual-studio-mac/)를 사용하여 ASP.NET Core MVC 웹앱을 빌드하는 기본 사항에 대해 알아봅니다. [!INCLUDE[consider RP](../../includes/razor.md)]

이 자습서는 세 가지 버전이 있습니다.

* macOS: [Mac용 Visual Studio을 사용하여 ASP.NET Core MVC 앱 빌드](xref:tutorials/first-mvc-app-mac/start-mvc)
* Windows: [Visual Studio를 사용하여 ASP.NET Core MVC 앱 빌드](xref:tutorials/first-mvc-app/start-mvc)
* Linux, macOS 및 Windows: [Visual Studio Code를 사용하여 ASP.NET Core MVC 앱 빌드](xref:tutorials/first-mvc-app-xplat/start-mvc)

## <a name="prerequisites"></a>필수 구성 요소

이 자습서에서는 [.NET Core 2.0.0 SDK](https://www.microsoft.com/net/core) 이상이 필요합니다. ASP.NET Core 1.1 버전에 대해서는 [PDF](https://github.com/aspnet/Docs/blob/master/aspnetcore/tutorials/first-mvc-app-mac/start-mvc/8-23-17.pdf)를 참조하세요.

다음을 설치합니다.

- [.NET Core 2.0.0 SDK](https://www.microsoft.com/net/core) 이상
- [Visual Studio for Mac](https://www.visualstudio.com/vs/visual-studio-mac/)

## <a name="create-a-web-app"></a>웹앱 만들기

Visual Studio에서 **파일 > 새 솔루션**을 선택합니다.

![macOS 새 솔루션](../first-web-api-mac/_static/sln.png)

**.NET Core App > ASP.NET Core > 웹앱 > 다음**을 선택합니다.

![macOS 새 프로젝트 대화 상자](start-mvc/1.png)

프로젝트 이름을 **MvcMovie**로 지정하고 **만들기**를 선택합니다.

![macOS 새 프로젝트 대화 상자](start-mvc/2.png)

### <a name="launch-the-app"></a>앱 시작

Visual Studio에서 **실행 > 디버깅하지 않고 시작**을 선택하여 앱을 시작합니다. Visual Studio가 [IIS Express](https://docs.microsoft.com/iis/extensions/introduction-to-iis-express/iis-express-overview)를 시작하고 브라우저를 실행하며 `http://localhost:port`로 이동합니다. 여기서 *포트*는 임의로 선택된 포트 번호입니다.

![새 프로젝트가 있는 브라우저](start-mvc/b1.png)

* 주소 표시줄에 `localhost:port#`이 표시되고 `example.com` 등은 표시되지 않습니다. 그 이유는 `localhost`가 로컬 컴퓨터의 표준 이름이기 때문입니다. Visual Studio에서 웹 프로젝트를 만들 경우 웹 서버에는 임의 포트가 사용됩니다. 앱을 실행할 경우 다른 포트 번호가 표시됩니다.
* **실행** 메뉴 항목에서 앱을 디버그 또는 디버그 이외 모드로 시작할 수 있습니다.

기본 템플릿은 **홈, 정보** 및 **연락처** 링크를 제공합니다. 위의 브라우저 이미지에는 이러한 링크가 표시되지 않습니다. 브라우저 크기에 따라 탐색 아이콘을 클릭하여 링크를 표시해야 할 수 있습니다.

![새 프로젝트가 있는 브라우저](start-mvc/b2.png)

이 자습서의 다음 부분에서는 MVC에 대해 알아보고 일부 코드 작성을 시작합니다.

>[!div class="step-by-step"]
[다음](adding-controller.md)  
