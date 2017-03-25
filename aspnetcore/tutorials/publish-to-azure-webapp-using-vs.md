---
title: "Visual Studio를 사용 하 여 Azure에 ASP.NET 핵심 웹 앱 배포 | Microsoft 문서"
author: rick-anderson
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 78571e4a-a143-452d-9cf2-0860f85972e6
ms.technology: aspnet
ms.prod: asp.net-core
uid: tutorials/publish-to-azure-webapp-using-vs
translationtype: Machine Translation
ms.sourcegitcommit: 010b730d2716f9f536fef889bc2f767afb648ef4
ms.openlocfilehash: b90d31aa49e23aef3c045f3aeab0c4068117373a
ms.lasthandoff: 03/23/2017

---
# <a name="deploy-an-aspnet-core-web-app-to-azure-using-visual-studio"></a>Visual Studio를 사용 하 여 Azure에 ASP.NET 핵심 웹 응용 프로그램 배포

여 [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [경우 Blum Silveira](https://github.com/cesarbs)

## <a name="set-up-the-development-environment"></a>개발 환경 설정

* 최신 설치 [Visual Studio 용 Azure SDK](https://www.visualstudio.com/features/azure-tools-vs)합니다. 아직 없는 경우 SDK에서 Visual Studio를 설치 합니다.

> [!NOTE]
> 종속성의 여러 컴퓨터에 없는 경우 SDK 설치에서 30 분 이상 걸릴 수 있습니다.

* 설치 [.NET Core + Visual Studio 도구](http://go.microsoft.com/fwlink/?LinkID=798306)

* 확인 프로그램 [Azure 계정](https://portal.azure.com/)합니다. 할 수 있습니다 [무료 Azure 계정을](https://azure.microsoft.com/pricing/free-trial/) 또는 [구독자 혜택을 활성화 하는 Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)합니다.

## <a name="create-a-web-app"></a>웹 앱 만들기

Visual Studio 시작 페이지에서 누릅니다 **새 프로젝트...**.

![시작 페이지](publish-to-azure-webapp-using-vs/_static/new_project.png)

또는 새 프로젝트를 만들려면 메뉴를 사용할 수 있습니다. 누르기 **파일 > 새로 만들기 > 프로젝트...**.

![파일 메뉴](publish-to-azure-webapp-using-vs/_static/alt_new_project.png)

완료 된 **새 프로젝트** 대화 상자:

* 왼쪽된 창에서 **웹**

* 가운데 창에서 탭 **ASP.NET 핵심 웹 응용 프로그램 (.NET 코어)**

* 누르기 **확인**

![새 프로젝트 대화 상자](publish-to-azure-webapp-using-vs/_static/new_prj.png)

에 **새 ASP.NET 핵심 웹 응용 프로그램 (.NET Core)** 대화 상자:

* 누르기 **웹 응용 프로그램**

* 확인 **인증** 로 설정 된 **개별 사용자 계정**

* 확인 **클라우드에서 호스트** 는 **하지** 확인

* 누르기 **확인**

![새 ASP.NET 핵심 웹 응용 프로그램 (.NET Core) 대화 상자](publish-to-azure-webapp-using-vs/_static/noath.png)

## <a name="test-the-app-locally"></a>앱을 로컬로 테스트

* 키를 눌러 **Ctrl + f&5;** 로컬로 앱을 실행 하려면

* 탭은 **에 대 한** 및 **연락처** 링크 합니다. 장치의 크기에 따라는 링크를 표시 하려면 탐색 아이콘을 누르세요 해야 할 수 있습니다.

![Localhost에서 Microsoft Edge에서 웹 응용 프로그램을 엽니다.](publish-to-azure-webapp-using-vs/_static/show.png)

* 누르기 **등록** 하 고 새 사용자를 등록 합니다. 가상의 전자 메일 주소를 사용할 수 있습니다. 제출 하면 다음 오류가 됩니다.

![내부 서버 오류: 요청을 처리 하는 동안 데이터베이스 작업이 실패 했습니다. SQL 예외: 데이터베이스를 열 수 없습니다. 응용 프로그램 DB 컨텍스트에 대 한 기존 마이그레이션을 적용이 문제를 해결할 수 있습니다.](publish-to-azure-webapp-using-vs/_static/mig.png)

두 가지 방법으로 문제를 해결할 수 있습니다.

* 누르기 **마이그레이션을 적용** 페이지 업데이트; 페이지를 새로 고칩니다. 면 또는

* 프로젝트의 디렉터리에서 명령 프롬프트에서 다음을 실행 합니다.

  <!-- literal_block {"ids": [], "xml:space": "preserve"} -->

  ```
  dotnet ef database update
     ```

새 사용자를 등록 하는 데 사용 되는 전자 메일을 표시 하는 응용 프로그램 및 **로그 오프** 링크 합니다.

![Microsoft Edge에서 웹 응용 프로그램을 엽니다. 등록 링크 텍스트 Hello 대신 abc@example.com!](publish-to-azure-webapp-using-vs/_static/hello.png)

## <a name="deploy-the-app-to-azure"></a>Azure에 앱 배포

솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭 하 고 선택 **게시...**.

![상황에 맞는 메뉴를 강조 표시 하는 게시 링크에서 열기](publish-to-azure-webapp-using-vs/_static/pub.png)

에 **게시** 대화 상자에서 tap **Microsoft Azure 앱 서비스**합니다.

![게시 대화 상자](publish-to-azure-webapp-using-vs/_static/maas1.png)

누르기 **새로 만들기...** 새 리소스 그룹을 만듭니다. 새 리소스 그룹을 만드는 것 하기가 쉬워집니다이 자습서에서 만드는 모든 Azure 리소스를 삭제 합니다.

![앱 서비스 대화 상자](publish-to-azure-webapp-using-vs/_static/newrg1.png)

새 리소스 그룹 및 앱 서비스 계획을 만듭니다.

* 누르기 **새로 만들기...** 리소스에 대 한 그룹화 하 고 새 리소스 그룹에 대 한 이름을 입력 합니다.

* 누르기 **새로 만들기...** 앱 서비스 계획 및 가까운 위치를 선택 합니다. 생성 된 기본 이름을 유지할 수 있습니다.

* 누르기 **추가 Azure 서비스를 탐색** 새 데이터베이스를 만들려면

![새 리소스 그룹 대화 상자: 호스팅 패널](publish-to-azure-webapp-using-vs/_static/cas.png)

* 녹색 누릅니다 **+** 를 새 SQL 데이터베이스를 만드는 아이콘

![새 리소스 그룹 대화 상자: 서비스 패널](publish-to-azure-webapp-using-vs/_static/sql.png)

* 누르기 **새로 만들기...** 에 **SQL 데이터베이스 구성** 대화 상자를 새 데이터베이스 서버를 만듭니다.

![SQL 데이터베이스 대화 상자를 구성 합니다.](publish-to-azure-webapp-using-vs/_static/conf.png)

* 관리자 사용자 이름 및 암호를 입력 한 다음 누릅니다 **확인**합니다. 사용자 이름 및이 단계에서 만든 암호를 잊지 마십시오. 기본을 유지할 수 **서버 이름**

![SQL Server 대화 상자를 구성 합니다.](publish-to-azure-webapp-using-vs/_static/conf_servername.png)

> [!NOTE]
> 관리자 사용자 이름 "admin"를 사용할 수 없습니다.

* 누르기 **확인** 에 **SQL 데이터베이스 구성** 대화 상자

![SQL 데이터베이스 대화 상자를 구성 합니다.](publish-to-azure-webapp-using-vs/_static/conf_final.png)

* 누르기 **만들기** 에 **응용 프로그램 서비스 만들기** 대화 상자

![응용 프로그램 서비스 만들기 대화 상자](publish-to-azure-webapp-using-vs/_static/create_as.png)

* 누르기 **다음** 에 **게시** 대화 상자

![게시 대화 상자: 연결 패널](publish-to-azure-webapp-using-vs/_static/pubc.png)

* 에 **설정을** 의 단계는 **게시** 대화 상자:

  * 확장 **데이터베이스** 확인 **런타임 시이 연결 문자열을 사용 합니다.**

  * 확장 **Entity Framework 마이그레이션** 확인 **적용이이 마이그레이션에 게시**

* 누르기 **게시** 하 고 Visual Studio 앱 게시 완료 될 때까지 대기 합니다.

![게시 대화 상자: 설정 패널](publish-to-azure-webapp-using-vs/_static/pubs.png)

Visual Studio는 앱을 Azure에 게시 되며 브라우저에서 클라우드 응용 프로그램을 실행 합니다.

### <a name="test-your-app-in-azure"></a>Azure에서 응용 프로그램을 테스트 합니다.

* 테스트는 **에 대 한** 및 **연락처** 링크

* 새 사용자 등록

![웹 응용 프로그램에서 Azure 앱 서비스에서 Microsoft Edge 열](publish-to-azure-webapp-using-vs/_static/final.png)

### <a name="update-the-app"></a>앱 업데이트

* 편집 된 `Views/Home/About.cshtml` Razor 파일의 내용을 변경 하 고 있습니다. 예:

<!-- literal_block {"ids": [], "linenos": false, "xml:space": "preserve", "language": "html", "highlight_args": {"hl_lines": [7]}} -->

```html
@{
       ViewData["Title"] = "About";
   }
   <h2>@ViewData["Title"].</h2>
   <h3>@ViewData["Message"]</h3>

   <p>My updated about page.</p>
   ```

* 프로젝트를 마우스 오른쪽 단추로 클릭 하 고 탭 **게시...** 다시

![상황에 맞는 메뉴를 강조 표시 하는 게시 링크에서 열기](publish-to-azure-webapp-using-vs/_static/pub.png)

* 변경 내용을 Azure에서 사용할 수 있는 앱을 게시 한 후 확인

### <a name="clean-up"></a>정리

로 이동 하는 앱을 테스트 했으면는 [Azure 포털](https://portal.azure.com/) 및 응용 프로그램을 삭제 합니다.

* 선택 **리소스 그룹**를 누른 다음 만든 리소스 그룹

![사이드바 메뉴에 있는 azure 포털: 리소스 그룹](publish-to-azure-webapp-using-vs/_static/portalrg.png)

* 에 **리소스 그룹** 블레이드, tap **삭제**

![Azure 포털: 리소스 그룹 블레이드](publish-to-azure-webapp-using-vs/_static/rgd.png)

* 리소스 그룹의 이름을 입력 하 고 탭 **삭제**합니다. 응용 프로그램 및이 자습서에서 만든 다른 모든 리소스는 이제 Azure에서 삭제

### <a name="next-steps"></a>다음 단계

* [Visual Studio 및 ASP.NET 핵심 MVC 시작](first-mvc-app/start-mvc.md)

* [ASP.NET Core 소개](../index.md)

* [기본 사항](../fundamentals/index.md)

