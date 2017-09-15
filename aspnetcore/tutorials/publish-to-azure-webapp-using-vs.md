---
title: "Visual Studio를 사용하여 Azure에 ASP.NET Core 앱 게시"
author: rick-anderson
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: get-started-article
ms.assetid: 78571e4a-a143-452d-9cf2-0860f85972e6
ms.technology: aspnet
ms.prod: asp.net-core
uid: tutorials/publish-to-azure-webapp-using-vs
ms.openlocfilehash: c4df5f4551760fbc4ed4b11362d249b24f186e00
ms.sourcegitcommit: 8f5277871eff86134ebf68d3737196cfd4a62c2c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2017
---
# <a name="publish-an-aspnet-core-web-app-to-azure-app-service-using-visual-studio"></a>Visual Studio를 사용하여 Azure App Service에 ASP.NET Core 웹앱 게시

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [Cesar Blum Silveira](https://github.com/cesarbs)

## <a name="set-up-the-development-environment"></a>개발 환경 설정

* 최신 [Azure SDK for Visual Studio](https://www.visualstudio.com/features/azure-tools-vs)를 설치합니다. SDK는 아직 없는 경우 Visual Studio를 설치합니다.

> [!NOTE]
> SDK 설치는 컴퓨터에 많은 종속성이 없는 경우 30분 이상 걸릴 수 있습니다.

* [.NET Core + Visual Studio 도구](http://go.microsoft.com/fwlink/?LinkID=798306) 설치

* [Azure 계정](https://portal.azure.com/)을 확인합니다. [Azure 계정을 열](https://azure.microsoft.com/pricing/free-trial/)거나 [Visual Studio 구독자 혜택을 활성화](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)할 수 있습니다.

## <a name="create-a-web-app"></a>웹앱 만들기

Visual Studio 시작 페이지에서 **새 프로젝트...**를 누릅니다.

![시작 페이지](publish-to-azure-webapp-using-vs/_static/new_project.png)

또는 메뉴를 사용하여 새 프로젝트를 만들 수 있습니다. **파일 > 새로 만들기 > 프로젝트...**를 누릅니다.

![파일 메뉴](publish-to-azure-webapp-using-vs/_static/alt_new_project.png)

**새 프로젝트** 대화 상자를 완료합니다.

* 왼쪽 창에서 **웹**를 누릅니다.

* 가운데 창에서 **ASP.NET Core 웹 응용 프로그램(.NET Core)**을 누릅니다.

* **확인**을 누릅니다.

![새 프로젝트 대화 상자](publish-to-azure-webapp-using-vs/_static/new_prj.png)

**새 ASP.NET Core 웹 응용 프로그램(.NET Core)** 대화 상자에서:

* **웹 응용 프로그램**을 누릅니다.

* **인증**이 **개별 사용자 계정**으로 설정되었는지 확인합니다.

* **클라우드에서 호스트**가 선택되지 **않았는지** 확인합니다.

* **확인**을 누릅니다.

![새 ASP.NET Core 웹 응용 프로그램(.NET Core) 대화 상자](publish-to-azure-webapp-using-vs/_static/noath.png)

## <a name="test-the-app-locally"></a>로컬로 앱 테스트

* **Ctrl+F5** 키를 눌러 로컬로 앱을 실행합니다.

* **정보** 및 **연락처** 링크를 누릅니다. 장치 크기에 따라 탐색 아이콘을 눌러 링크를 표시해야 할 수 있습니다.

![localhost의 Microsoft Edge에서 열린 웹 응용 프로그램](publish-to-azure-webapp-using-vs/_static/show.png)

* **등록**을 누르고 새 사용자를 등록합니다. 가상의 전자 메일 주소를 사용할 수 있습니다. 제출하면 다음과 같은 오류가 발생합니다.

![내부 서버 오류: 요청을 처리하는 동안 데이터베이스 작업이 실패했습니다. SQL 예외: 데이터베이스를 열 수 없습니다. 응용 프로그램 DB 컨텍스트에 대한 기존 마이그레이션 적용으로 이 문제를 해결할 수 있습니다.](publish-to-azure-webapp-using-vs/_static/mig.png)

두 가지 방법으로 문제를 해결할 수 있습니다.

* **마이그레이션 적용**을 누르고 페이지 업데이트를 누르고, 페이지를 새로 고칩니다. 또는

* 프로젝트 디렉터리의 명령 프롬프트에서 다음을 실행합니다.

  <!-- literal_block {"ids": [], "xml:space": "preserve"} -->

  ```
  dotnet ef database update
     ```

앱은 새 사용자를 등록하는 데 사용한 전자 메일 및 **로그오프** 링크를 표시합니다.

![Microsoft Edge에서 열린 웹 응용 프로그램. 레지스터 링크는 텍스트 Hello abc@example.com으로 교체됩니다.](publish-to-azure-webapp-using-vs/_static/hello.png)

## <a name="deploy-the-app-to-azure"></a>Azure에 앱 배포

배포할 게시된 앱이 실행되고 있지 않은지 확인합니다. 앱이 실행 중이면 *publish* 폴더의 파일이 잠겨 있습니다. 잠긴 파일을 복사할 수 없으므로 배포를 수행할 수 없습니다.

솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시...**를 선택합니다.

![강조 표시된 게시 링크로 열린 바로 가기 메뉴](publish-to-azure-webapp-using-vs/_static/pub.png)

**게시** 대화 상자에서 **Microsoft Azure App Service**를 누릅니다.

![게시 대화 상자](publish-to-azure-webapp-using-vs/_static/maas1.png)

**새로 만들기...**를 눌러 새 리소스 그룹을 만듭니다. 새 리소스 그룹 만들기를 통해 이 자습서에서 만드는 모든 Azure 리소스를 더욱 쉽게 삭제할 수 있습니다.

![App Service 대화 상자](publish-to-azure-webapp-using-vs/_static/newrg1.png)

새 리소스 그룹 및 앱 서비스 계획을 만듭니다.

* 리소스 그룹에 대해 **새로 만들기...**를 누르고 새 리소스 그룹에 대한 이름을 입력합니다.

* 앱 서비스 계획에 대해 **새로 만들기...**를 누르고 가까운 위치를 선택합니다. 생성된 기본 이름을 유지할 수 있습니다.

* **추가 Azure 서비스 탐색**을 눌러 새 데이터베이스를 만듭니다.

![새 리소스 그룹 대화 상자: 호스팅 패널](publish-to-azure-webapp-using-vs/_static/cas.png)

* 녹색 **+** 아이콘을 눌러 새 SQL Database를 만듭니다.

![새 리소스 그룹 대화 상자: 서비스 패널](publish-to-azure-webapp-using-vs/_static/sql.png)

* **SQL Database 구성** 대화 상자에서 **새로 만들기...**를 눌러 새 데이터베이스 서버를 만듭니다.

![SQL Database 구성 대화 상자](publish-to-azure-webapp-using-vs/_static/conf.png)

* 관리자 사용자 이름 및 암호를 입력한 다음 **확인**을 누릅니다. 이 단계에서 만드는 사용자 이름 및 암호를 잊지 마십시오. 기본 **서버 이름**을 유지할 수 있습니다.

![SQL Server 구성 대화 상자](publish-to-azure-webapp-using-vs/_static/conf_servername.png)

> [!NOTE]
> "관리자"는 관리자 사용자 이름으로 사용할 수 없습니다.

* **SQL Database 구성** 대화 상자에서 **확인**을 누릅니다.

![SQL Database 구성 대화 상자](publish-to-azure-webapp-using-vs/_static/conf_final.png)

* **App Service 만들기** 대화 상자에서 **만들기**를 누릅니다.

![App Service 만들기 대화 상자](publish-to-azure-webapp-using-vs/_static/create_as.png)

* **게시** 대화 상자에서 **다음**을 누릅니다.

![게시 대화 상자: 연결 패널](publish-to-azure-webapp-using-vs/_static/pubc.png)

* **게시** 대화 상자의 **설정** 단계에서:

  * **데이터베이스**를 확장하고 **런타임 시 이 연결 문자열 사용**을 선택합니다.

  * **Entity Framework 마이그레이션**을 확장하고 **게시에 이 마이그레이션 적용**을 선택합니다.

* **게시**를 누르고 Visual Studio에서 앱 게시를 완료할 때까지 기다립니다.

![게시 대화 상자: 설정 패널](publish-to-azure-webapp-using-vs/_static/pubs.png)

Visual Studio는 앱을 Azure에 게시하고 브라우저에서 클라우드 앱을 시작합니다.

### <a name="test-your-app-in-azure"></a>Azure에서 앱 테스트

* **정보** 및 **연락처** 링크 테스트

* 새 사용자 등록

![Azure App Service의 Microsoft Edge에서 열린 웹 응용 프로그램](publish-to-azure-webapp-using-vs/_static/final.png)

### <a name="update-the-app"></a>앱 업데이트

* `Views/Home/About.cshtml` Razor 보기 파일을 편집하고 해당 내용을 변경합니다. 예:

<!-- literal_block {"ids": [], "linenos": false, "xml:space": "preserve", "language": "html", "highlight_args": {"hl_lines": [7]}} -->

```html
@{
       ViewData["Title"] = "About";
   }
   <h2>@ViewData["Title"].</h2>
   <h3>@ViewData["Message"]</h3>

   <p>My updated about page.</p>
   ```

* 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시...**를 누릅니다.

![강조 표시된 게시 링크로 열린 바로 가기 메뉴](publish-to-azure-webapp-using-vs/_static/pub.png)

* 앱이 게시된 후 변경 내용이 Azure에서 제공되는지 확인합니다.

### <a name="clean-up"></a>정리

앱 테스트를 완료하면 [Azure Portal](https://portal.azure.com/)로 이동하고 앱을 삭제합니다.

* **리소스 그룹**을 선택한 다음 만든 리소스 그룹을 누릅니다.

![Azure Portal: 사이드바 메뉴에 있는 리소스 그룹](publish-to-azure-webapp-using-vs/_static/portalrg.png)

* **리소스 그룹** 블레이드에서 **삭제**를 누릅니다.

![Azure Portal: 리소스 그룹 블레이드](publish-to-azure-webapp-using-vs/_static/rgd.png)

* 리소스 그룹의 이름을 입력하고 **삭제**를 누릅니다. 이 자습서에서 만든 앱과 다른 모든 리소스는 이제 Azure에서 삭제됩니다.

### <a name="next-steps"></a>다음 단계

* [ASP.NET Core MVC 및 Visual Studio 시작](first-mvc-app/start-mvc.md)

* [ASP.NET Core 소개](../index.md)

* [기본 사항](../fundamentals/index.md)
