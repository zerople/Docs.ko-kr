---
title: "Visual Studio 및 Git을 사용하여 Azure에 연속 배포"
author: rick-anderson
description: "Visual Studio를 사용하여 ASP.NET Core 웹앱을 만들고 연속 배포를 위한 Git를 사용하여 Azure App Service에 배포하는 방법을 알아봅니다."
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 2707c7a8-2350-4304-9856-fda58e5c0a16
ms.technology: aspnet
ms.prod: asp.net-core
uid: publishing/azure-continuous-deployment
ms.openlocfilehash: f7ea2e76fdee19a3d964e42053f0060a0a505e5b
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2017
---
# <a name="continuous-deployment-to-azure-for-aspnet-core-with-visual-studio-and-git"></a>Visual Studio 및 Git을 사용하여 Azure에 ASP.NET Core 연속 배포

작성자: [Erik Reitan](https://github.com/Erikre)

이 자습서에서는 Visual Studio를 사용하여 ASP.NET Core 웹앱을 만들고 연속 배포를 사용하여 Visual Studio에서 Azure App Service에 배포하는 방법을 알아봅니다. 

또한 [연속 배포를 사용하여 Azure Web App을 빌드하고 게시하기 위해 VSTS 사용](https://www.visualstudio.com/docs/build/get-started/aspnet-4-ci-cd-azure-automatic)을 참조하세요. 여기서는 Visual Studio Team Services를 사용하여 [Azure App Service](https://azure.microsoft.com/documentation/articles/app-service-changes-existing-services/)에 대한 CD(지속적인 업데이트) 워크플로를 구성하는 방법을 보여줍니다. Team Services의 Azure 지속적인 업데이트는 Azure App Service에 앱의 업데이트를 게시하는 강력한 배포 파이프라인을 간단하게 설정합니다. 파이프라인을 빌드하고, 테스트를 실행하고, 스테이징 슬롯에 배포하고, 프로덕션에 배포하도록 Azure Portal에서 구성할 수 있습니다.

> [!NOTE]
> 이 자습서를 완료하려면 Microsoft Azure 계정이 필요합니다. 계정이 없으면 [MSDN 구독자 혜택을 활성화](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)하거나 [평가판에 등록](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F)할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

이 자습서에서는 이미 다음 항목을 설치했다고 가정합니다.

* [Visual Studio](https://www.visualstudio.com)

* [ASP.NET Core](https://www.microsoft.com/net/download/core)(런타임 및 도구)

* Windows용 [Git](https://git-scm.com/downloads)

## <a name="create-an-aspnet-core-web-app"></a>ASP.NET Core 웹앱 만들기

1. Visual Studio를 시작합니다.

2. **파일** 메뉴에서 **새로 만들기** > **프로젝트**를 선택합니다.

3. **ASP.NET Core 웹 응용 프로그램** 프로젝트 템플릿을 선택합니다. **설치됨** > **템플릿** > **Visual C#** > **.NET Core** 아래에 표시됩니다. 프로젝트 이름을 `SampleWebAppDemo`로 지정합니다. **새 Git 리포지토리 만들기** 옵션을 선택하고 **확인**을 클릭합니다.

   ![새 프로젝트 대화 상자](azure-continuous-deployment/_static/01-new-project.png)

4. **새 ASP.NET Core 프로젝트** 대화 상자에서 ASP.NET Core **빈** 템플릿을 선택한 후 **확인**을 클릭합니다.

   ![새 ASP.NET 프로젝트 대화 상자](azure-continuous-deployment/_static/02-web-site-template.png)

>[!NOTE]
    >최신 .NET Core 릴리스는 2.0입니다.

### <a name="running-the-web-app-locally"></a>로컬로 웹앱 실행

1. Visual Studio에서 앱 만들기를 완료하면 **디버그** -> **디버깅 시작**을 선택하여 앱을 실행합니다. 대안으로 **F5** 키를 눌러도 됩니다.

   Visual Studio 및 새 앱을 초기화하는 데 시간이 걸릴 수 있습니다. 작업이 완료되면 브라우저는 실행 중인 앱을 표시합니다.

   ![브라우저 창에서는 'Hello World!'을 표시하는 응용 프로그램이 실행 중이라고 표시합니다.](azure-continuous-deployment/_static/04-browser-runapp.png)

2. 실행 중인 웹앱을 검토한 후 브라우저를 닫고 Visual Studio의 도구 모음에서 "디버깅 중지" 아이콘을 클릭하여 앱을 중지합니다.

## <a name="create-a-web-app-in-the-azure-portal"></a>Azure Portal에서 웹앱 만들기

다음 단계에서는 Azure Portal에서 웹앱을 만드는 과정을 설명합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 포털의 왼쪽 맨 위에 있는 **새로 만들기**를 누릅니다.
3. **웹 + 모바일** > **Web App**을 누릅니다.

    ![Microsoft Azure Portal: 새 단추: Marketplace 아래에서 웹 + 모바일: 주요 앱 아래에서 Web App 단추](azure-continuous-deployment/_static/05-azure-newwebapp.png)

4.  **Web App** 블레이드에서 **App Service 이름**에 고유한 값을 입력합니다.

    ![Web App 블레이드](azure-continuous-deployment/_static/06-azure-newappblade.png)

    >[!NOTE]
    >**App Service 이름** 이름은 고유해야 합니다. 이름을 입력하려는 경우 포털은 이 규칙을 적용합니다. 다른 값을 입력한 후 이 자습서에서 표시되는 **SampleWebAppDemo**의 각 항목에 해당 값을 대체해야 합니다.

    &nbsp;
    
    또한 **Web App** 블레이드에서 기존 **App Service 계획/위치**를 선택하거나 새로 만듭니다. 새 계획을 만들면 가격 책정 계층, 위치 및 기타 옵션을 선택합니다. App Service 계획에 대한 자세한 내용은 [Azure App Service 계획 세부 개요](https://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/)를 참조하세요.

5.  
              **만들기**를 클릭합니다. Azure에서는 웹앱을 프로비전하고 시작합니다.

    ![Azure Portal: 샘플 Web App 데모 01 Essentials 블레이드](azure-continuous-deployment/_static/07-azure-webappblade.png)

## <a name="enable-git-publishing-for-the-new-web-app"></a>새 웹앱에 대한 Git 게시 사용

Git는 Azure App Service 웹앱을 배포하는 데 사용할 수 있는 분산된 버전 제어 시스템입니다. 로컬 Git 리포지토리에서 웹앱에 작성하는 코드를 저장하고 원격 리포지토리에 푸시하여 Azure에 코드를 배포합니다.

1. 아직 로그인하지 않은 경우 [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. **App Services**를 클릭하여 Azure 구독과 연결된 앱 서비스 목록을 표시합니다.

3. 이 자습서의 이전 섹션에서 만든 웹앱을 선택합니다.

4. **배포** 블레이드에서 **배포 옵션** > **원본 선택** > **로컬 Git 리포지토리**를 선택합니다.

   ![설정 블레이드: 배포 원본 블레이드: 원본 블레이드 선택](azure-continuous-deployment/_static/deployment-options.png)

5. **확인**을 클릭합니다.

6. 이전에 웹앱 또는 다른 App Service 앱을 게시하기 위해 배포 자격 증명을 설정하지 않은 경우 지금 설정합니다.

   * **설정** > **배포 자격 증명**을 클릭합니다. **배포 자격 증명 설정** 블레이드가 표시됩니다.

   * 사용자 이름 및 암호를 만듭니다.  나중에 Git를 설정할 때 이 암호가 필요합니다.

   * **저장**을 클릭합니다.

7. **Web App** 블레이드에서 **설정** > **속성**을 클릭합니다. 배포할 원격 Git 리포지토리 URL이 **GIT URL** 아래에 표시됩니다.

8. 나중에 자습서에서 사용할 **GIT URL** 값을 복사합니다.

   ![Azure Portal: 응용 프로그램 속성 블레이드](azure-continuous-deployment/_static/09-azure-giturl.png)

## <a name="publish-your-web-app-to-azure-app-service"></a>Azure App Service에 웹앱 게시

이 섹션에서는 Visual Studio를 사용하여 로컬 Git 리포지토리를 만들고 해당 리포지토리로부터 Azure에 푸시하여 웹앱을 배포합니다. 관련 단계에는 다음이 포함됩니다.

   * Azure에 로컬 리포지토리를 배포할 수 있도록 GIT URL 값을 사용하여 원격 리포지토리 설정을 추가합니다.

   * 프로젝트에 변경 내용이 커밋됩니다.

   * 로컬 리포지토리로부터 Azure의 원격 리포지토리에 프로젝트 변경 내용이 푸시됩니다.

&nbsp;
   
1.  **솔루션 탐색기**에서 **솔루션 'SampleWebAppDemo'**를 마우스 오른쪽 단추로 클릭하고 **커밋**을 선택합니다. **팀 탐색기**가 표시됩니다.

    ![팀 탐색기 연결 탭](azure-continuous-deployment/_static/10-team-explorer.png)

2.  **팀 탐색기**에서 **홈**(홈 아이콘) > **설정** > **리포지토리 설정**을 선택합니다.

3.  **리포지토리 설정**의 **원격** 섹션에서 **추가**를 선택합니다. **원격 추가** 대화 상자가 표시됩니다.

4.  원격 리포지토리의 **이름**을 **Azure SampleApp**으로 설정합니다.

5.  **인출**의 값을 이 자습서의 앞부분에 나오는 Azure에서 복사한 **Git URL**로 설정합니다. 이 URL은 **.git**로 끝납니다.

    ![원격 대화 상자 편집](azure-continuous-deployment/_static/11-add-remote.png)

    >[!NOTE]
    >대안으로 **명령 창**을 열고,프로젝트 디렉터리를 변경하고, 명령을 입력하여 **명령 창**의 원격 저장소를 지정할 수 있습니다. 예를 들면 다음과 같습니다.`git remote add Azure-SampleApp https://me@sampleapp.scm.azurewebsites.net:443/SampleApp.git`

6.  **홈**(홈 아이콘) > **설정** > **전역 설정**을 선택합니다. 사용자 이름 및 전자 메일 주소를 설정했는지 확인합니다. **업데이트**를 선택해야 합니다.

7.  **홈** > **변경 내용**을 선택하여 **변경 내용** 보기로 돌아갑니다.

8.  **초기 푸시 #1**과 같은 커밋 메시지를 입력하고 **커밋**을 클릭합니다. 이렇게 하면 로컬로 *커밋*을 생성합니다. 다음으로 Azure와 *동기화*해야 합니다.

    ![팀 탐색기 연결 탭](azure-continuous-deployment/_static/12-initial-commit.png)

    >[!NOTE]
    >대안으로 **명령 창**을 열고,프로젝트 디렉터리를 변경하고, git 명령을 입력하여 **명령 창**의 변경 내용을 커밋할 수 있습니다. 예:
    >
    >`git add .`
    >
    >`git commit -am "Initial Push #1"`

9.  **홈** > **동기화** > **동작** > **명령 프롬프트 열기**를 선택합니다. 프로젝트 디렉터리에 명령 프롬프트가 열립니다.

10.  명령 창에서 다음 명령을 입력합니다.

    `git push -u Azure-SampleApp master`

11.  Azure에서 만든 Azure **배포 자격 증명** 암호를 입력합니다.

    >[!NOTE]
    >사용자가 입력할 때 암호는 표시되지 않습니다.

    이 명령은 로컬 프로젝트 파일을 Azure로 푸시하는 프로세스를 시작합니다. 위 명령의 출력은 성공적으로 배포했다는 메시지와 함께 종료됩니다.
        
    ```
    remote: Finished successfully.
    remote: Running post deployment command(s)...
    remote: Deployment successful.
    To https://username@samplewebappdemo01.scm.azurewebsites.net:443/SampleWebAppDemo01.git
    * [new branch]      master -> master
    Branch master set up to track remote branch master from Azure-SampleApp.
    ```
    > [!NOTE]
    > 프로젝트에서 공동 작업해야 하는 경우 Azure에 푸시 푸시하는 동안 [GitHub](https://github.com)에 푸시할지를 고려해야 합니다.
 
### <a name="verify-the-active-deployment"></a>활성 배포 확인

로컬 환경으로부터 Azure로 웹앱을 성공적으로 전송했는지 확인할 수 있습니다. 배포 성공이 나열됩니다.

1. [Azure Portal](https://portal.azure.com)에서 웹앱을 선택합니다. 그런 다음 **배포** > **배포 옵션**을 선택합니다.

   ![Azure Portal: 설정 블레이드: 성공적인 배포를 보여주는 배포 블레이드](azure-continuous-deployment/_static/13-verify-deployment.png)

## <a name="run-the-app-in-azure"></a>Azure에서 앱 실행

이제 Azure에 웹앱을 배포했으므로 앱을 실행할 수 있습니다.

이 작업은 다음 두 가지 방법으로 수행할 수 있습니다.

* Azure Portal에서 웹앱의 웹앱 블레이드를 찾고 **찾아보기**를 클릭하여 기본 브라우저에서 앱을 볼 수 있습니다.

* 브라우저를 열고 웹앱의 URL을 입력합니다. 예:

  `http://SampleWebAppDemo.azurewebsites.net`

## <a name="update-your-web-app-and-republish"></a>웹앱 업데이트 및 다시 게시

로컬 코드를 변경한 후에 다시 게시할 수 있습니다.

1.  Visual Studio의 **솔루션 탐색기**에서 *Startup.cs* 파일을 엽니다.

2.  `Configure` 메서드에서 `Response.WriteAsync` 메서드를 수정하여 다음과 같이 표시되도록 합니다.

    ```aspx-cs
    await context.Response.WriteAsync("Hello World! Deploy to Azure.");
    ```
3.  변경 내용을 *Startup.cs*에 저장합니다.

4.  **솔루션 탐색기**에서 **솔루션 'SampleWebAppDemo'**를 마우스 오른쪽 단추로 클릭하고 **커밋**을 선택합니다. **팀 탐색기**가 표시됩니다.

5.  다음과 같이 커밋 메시지를 입력하세요.

    ```none
    Update #2
    ```

6.  **커밋** 단추를 눌러 프로젝트 변경 내용을 커밋합니다.

7.  **홈** > **동기화** > **작업** > **푸시**를 선택합니다.

>[!NOTE]
>대안으로 **명령 창**을 열고,프로젝트 디렉터리를 변경하고, git 명령을 입력하여 **명령 창**의 변경 내용을 푸시할 수 있습니다. 예:
>
>`git push -u Azure-SampleApp master`

## <a name="view-the-updated-web-app-in-azure"></a>Azure에서 업데이트된 웹앱 보기

Azure Portal에서 웹앱 블레이드의 **찾아보기**를 선택하거나 브라우저를 열고 웹앱의 URL을 입력하여 업데이트된 웹앱을 봅니다. 예:

   `http://SampleWebAppDemo.azurewebsites.net`

## <a name="additional-resources"></a>추가 리소스

* [게시 및 배포](index.md)

* [프로젝트 Kudu](https://github.com/projectkudu/kudu/wiki)
