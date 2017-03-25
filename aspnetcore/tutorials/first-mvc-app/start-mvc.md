---
title: "ASP.NET Core MVC 및 Visual Studio 시작 | Microsoft 문서"
author: rick-anderson
description: "Visual Studio 및 ASP.NET 핵심 MVC 시작"
keywords: ASP.NET Core, MVC
ms.author: riande
manager: wpickett
ms.date: 03/07/2017
ms.topic: article
ms.assetid: 1d18b589-e3fd-4dc6-bde6-fb0f41998d78
ms.technology: aspnet
ms.prod: asp.net-core
uid: tutorials/first-mvc-app/start-mvc
translationtype: Machine Translation
ms.sourcegitcommit: 54ea0223213a9038a8548e4a01e8a2113ccb8498
ms.openlocfilehash: 403f42dc53322e4e589ab564b5d32caf227259f8
ms.lasthandoff: 03/23/2017

---
# <a name="getting-started-with-aspnet-core-mvc-and-visual-studio"></a>Visual Studio 및 ASP.NET 핵심 MVC 시작

[Rick Anderson](https://twitter.com/RickAndMSFT)

이 자습서에서는 ASP.NET 핵심 MVC 웹 응용 프로그램 사용 하 여 프로그램 빌드의 기초에 대해 설명 합니다 [Visual Studio 2017](https://www.visualstudio.com/)합니다.

> [!NOTE]
> 참조 [Mac를 사용 하 여 Visual Studio 코드에서 첫 번째 ASP.NET 핵심 응용 프로그램](../your-first-mac-aspnet.md) Mac 자습서입니다.

이 자습서의 Visual Studio 2015 버전에 대 한 참조는 [VS 2015 버전의 ASP.NET 핵심 설명서 PDF 형식으로](https://github.com/aspnet/Docs/blob/master/aspnetcore/common/_static/aspnet-core-project-json.pdf)합니다.

## <a name="install-visual-studio-and-net-core"></a>Visual Studio 및.NET Core를 설치 합니다.

Visual Studio 커뮤니티 2017를 설치 합니다. 커뮤니티 다운로드를 선택 합니다. Visual Studio 2017 설치 되어 있는 경우이 단계를 건너뜁니다.

  * [Visual Studio 2017 홈 페이지 설치 관리자](https://www.visualstudio.com/en-us/visual-studio-homepage-vs.aspx)

설치 관리자를 실행 하 고 다음 작업을 선택 합니다.
 - **ASP.NET 및 웹 개발** (아래 **웹 / 클라우드**)
 - **.NET core 크로스 플랫폼 개발** (아래 **기타 도구 집합**)

![**ASP.NET 및 웹 개발 * * (아래에서 * * 웹 / 클라우드 * *)](start-mvc/_static/web_workload.png)

![* *.NET core 크로스-크로스-플랫폼 개발 * * (아래에서 * * 다른 도구 집합 * *)](start-mvc/_static/x_plat_wl.png)


## <a name="create-a-web-app"></a>웹 앱 만들기

Visual Studio에서 선택 **파일 > 새로 만들기 > 프로젝트**합니다.

![파일 > 새로 만들기 > 프로젝트](start-mvc/_static/alt_new_project.png)

완료 된 **새 프로젝트** 대화 상자:

* 왼쪽된 창에서 **.NET Core**
* 가운데 창에서 탭 **ASP.NET 핵심 웹 응용 프로그램 (.NET 코어)**
* 프로젝트의 이름을 "MvcMovie" (것은 네임 스페이스는 일치 하는 코드를 복사 하는 경우 "MvcMovie" 프로젝트 이름을 지정 하는 것이 중요 합니다.)
* 누르기 **확인**

![새 프로젝트 대화 상자에서 ASP.NET 핵심 웹 왼쪽된 창에서.Net core ](start-mvc/_static/new_project2.png)

완료 된 **새 ASP.NET 핵심 웹 응용 프로그램 (.NET Core)-MvcMovie** 대화 상자:

* 버전 선택기 드롭다운 상자 tap에서 **ASP.NET 핵심 1.1**
* 누르기 **웹 응용 프로그램**
* 기본 유지 **인증 안 함**
* Tap **OK**.

![새 ASP.NET 핵심 웹 응용 프로그램](start-mvc/_static/p3.png)

Visual Studio는 방금 만든 MVC 프로젝트에 대 한 기본 서식 파일을 사용 합니다. 프로젝트 이름을 입력 하 고 몇 가지 옵션을 선택 하 여 작업 중인 앱을 지금 바로 있습니다. 이것은 간단한 시작 프로젝트를 이며를 시작 하려면 먼저

누르기 **F5** 디버그 모드에서 응용 프로그램을 실행 하려면 또는 **Ctrl + f&5;** 비디버그 모드에서.

![실행 중인 응용 프로그램](start-mvc/_static/1.png)

* Visual Studio는 시작 [IIS Express](http://www.iis.net/learn/extensions/introduction-to-iis-express/iis-express-overview) 응용 프로그램을 실행 하 고 있습니다. 주소 표시줄에 표시 된 `localhost:port#` 및 하지 것 처럼 `example.com`합니다. 이므로 `localhost` 로컬 컴퓨터에 대 한 표준 호스트 이름입니다. Visual Studio 웹 프로젝트를 만들 때 임의 포트를 웹 서버에 사용 됩니다. 위의 그림에서 포트 번호가 1234입니다. 응용 프로그램을 실행 하면 다른 포트 번호를 표시 됩니다.
* 응용 프로그램을 시작 **Ctrl + f&5;** (비-디버그 모드)을 사용 하면 코드를 변경, 파일을 저장, 브라우저 새로 고침 및 코드 변경 내용을 확인할 수 있습니다. 신속 하 게 응용 프로그램을 실행 하 고 변경 내용을 확인 하려면 비-디버그 모드를 사용 하는 개발자가 많습니다.
* 디버그 또는 비-디버그 모드에서 응용 프로그램을 실행할 수는 **디버그** 메뉴 항목:

![디버그 메뉴](start-mvc/_static/debug_menu.png)

* 기반으로 응용 프로그램을 디버깅할 수는 **IIS Express** 단추

![IIS Express](start-mvc/_static/iis_express.png)

기본 템플릿은 작업 수 **에 대 한 홈** 및 **연락처** 링크 합니다. 위 이미지 브라우저 다음이 링크를 표시 하지 않습니다. 브라우저의 크기에 따라 개체를 표시 하려면 탐색 아이콘을 클릭 하 여 할 수 있습니다.

![오른쪽 위에 있는 탐색 아이콘](start-mvc/_static/2.png)

디버그 모드에서 실행 되 고 누릅니다 **Shift + F5** 디버깅을 중지 합니다.

이 자습서의 다음 단계에서 MVC에 알아보기를 몇 가지 코드 작성을 시작 합니다.

>[!div class="step-by-step"]
[다음](adding-controller.md)  

