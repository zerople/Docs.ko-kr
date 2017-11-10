---
title: "ASP.NET Core에서 Razor 페이지 시작"
author: rick-anderson
description: "ASP.NET Core에서 Razor 페이지 시작"
keywords: "ASP.NET Core, Razor 페이지, Razor, MVC"
ms.author: riande
manager: wpickett
ms.date: 08/27/2017
ms.topic: get-started-article
ms.technology: aspnet
ms.prod: aspnet-core
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: 9d647657f21dd7e952808e5fe020f7a9e8767cd8
ms.sourcegitcommit: 3ba32b2b6425ed94604cb0f681db0d5bb5f8ad58
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/28/2017
---
# <a name="getting-started-with-razor-pages-in-aspnet-core"></a>ASP.NET Core에서 Razor 페이지 시작

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)

이 자습서에서는 ASP.NET Core Razor 페이지 웹앱을 빌드하는 작업의 기본 사항을 설명합니다. 이 자습서를 시작하기 전에 [Razor 페이지 소개](xref:mvc/razor-pages/index)를 완료하는 것이 좋습니다. Razor 페이지는 ASP.NET Core에서 웹 응용 프로그램 UI를 빌드하는 좋은 방법입니다.

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE[install 2.0](../../includes/install2.0.md)]

## <a name="create-a-razor-web-app"></a>Razor 웹앱 만들기

* Visual Studio **파일** 메뉴에서 **새로 만들기** > **프로젝트**를 선택합니다.
* 새 ASP.NET Core 웹 응용 프로그램을 만듭니다. 프로젝트 이름을 **RazorPagesMovie**로 지정합니다. 코드를 복사 후 붙여넣을 때 네임스페이스가 일치하도록 프로젝트 이름을 *RazorPagesMovie*로 지정해야 합니다.
  ![새 ASP.NET Core 웹 응용 프로그램](../../mvc/razor-pages/index/_static/np.png)
* 드롭다운에서 **ASP.NET Core 2.0**을 선택하고 **웹 응용 프로그램**을 선택합니다.
  ![웹 응용 프로그램(Razor 페이지)](../../mvc/razor-pages/index/_static/np2.png)

Visual Studio 템플릿은 시작 프로젝트를 만듭니다.

![솔루션 탐색기](razor-pages-start/_static/se.png)

**F5** 키를 눌러 디버그 모드에서 앱을 실행하거나 **Ctrl-F5**를 눌러 디버거를 연결하지 않고 실행합니다.

![홈 또는 인덱스 페이지](razor-pages-start/_static/home.png)

* Visual Studio가 [IIS Express](https://docs.microsoft.com/iis/extensions/introduction-to-iis-express/iis-express-overview)를 시작하고 앱을 실행합니다. 주소 표시줄에 `localhost:port#`이 표시되고 `example.com` 등은 표시되지 않습니다. 그 이유는 `localhost`가 로컬 컴퓨터의 표준 이름이기 때문입니다. Localhost는 로컬 컴퓨터의 웹 요청만 지원합니다. Visual Studio에서 웹 프로젝트를 만들 경우 웹 서버에는 임의 포트가 사용됩니다. 이전 이미지에서 포트 번호는 5000입니다. 앱을 실행할 경우 다른 포트 번호가 표시됩니다.
* **Ctrl+F5**(디버그 이외 모드)를 사용하여 앱을 시작하면 코드를 변경하고, 파일을 저장하고, 브라우저를 새로 고치고, 코드 변경 내용을 확인할 수 있습니다. 대부분의 개발자는 앱을 빠르게 시작하고 변경 내용을 확인하기 위해 디버그 이외 모드를 사용하려고 합니다.

[!INCLUDE[razor-pages-start](../../includes/RP/razor-pages-start.md)]

>[!div class="step-by-step"]
[다음: 모델 추가](xref:tutorials/razor-pages/model)
