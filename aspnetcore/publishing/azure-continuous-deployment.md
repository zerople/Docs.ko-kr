---
title: "연속 배포를 사용 하 여 Azure 웹 앱에 게시 | Microsoft 문서"
author: rick-anderson
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 2707c7a8-2350-4304-9856-fda58e5c0a16
ms.technology: aspnet
ms.prod: asp.net-core
uid: publishing/azure-continuous-deployment
translationtype: Machine Translation
ms.sourcegitcommit: f42a2e1e42a2231c3fc4898fe98f61c89176b555
ms.openlocfilehash: 87fd0bfd706acb4329808ef82dd76b947763ddc5
ms.lasthandoff: 03/23/2017

---
# <a name="publishing-to-an-azure-web-app-with-continuous-deployment"></a>연속 배포를 사용 하 여 Azure 웹 앱에 게시

[Erik Reitan](https://github.com/Erikre)

이 자습서에서는 Visual Studio를 사용 하 여 ASP.NET 핵심 웹 응용 프로그램을 만들고 Azure 앱 서비스에 Visual Studio에서 배포 하는 방법을 지속적으로 배포를 사용 합니다. 

참고 항목 [연속 배포를 사용 하 여 Azure 웹 앱을 빌드하고 게시를 사용 하 여 VSTS](https://www.visualstudio.com/en-us/docs/build/get-started/aspnet-4-ci-cd-azure-automatic)에 대 한 연속 배달 (CD) 워크플로 구성 하는 방법을 보여 주는 [Azure 앱 서비스](https://azure.microsoft.com/en-us/documentation/articles/app-service-changes-existing-services/) Visual Studio Team Services를 사용 합니다. Azure 연속 배달 Team Services에서 Azure 앱 서비스에 응용 프로그램에 대 한 업데이트를 게시 하는 강력한 배포 파이프라인 설정을 간소화 합니다. 파이프라인을 빌드, 테스트 실행, 스테이징 슬롯에 배포 및 다음 프로덕션에 배포 하는 Azure 포털에서 구성할 수 있습니다.

> [!NOTE]
> 이 자습서를 완료 하려면 Microsoft Azure 계정이 필요 합니다. 계정이 없으면 다음을 할 수 있습니다 [MSDN 구독자 혜택을 활성화](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F) 또는 [무료 평가판에 등록할](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F)합니다.

## <a name="prerequisites"></a>필수 조건

이 자습서에서는 다음 이미 설치 했다고 가정 합니다.

* [Visual Studio](https://www.visualstudio.com)

* [ASP.NET Core](http://go.microsoft.com/fwlink/?LinkId=627627) (런타임 및 도구)

* [Git](http://git-scm.com/downloads) Windows 용

## <a name="create-an-aspnet-core-web-app"></a>ASP.NET Core 웹 응용 프로그램 만들기

1. Visual Studio를 시작합니다.

2. **파일** 메뉴 **새로** > **프로젝트**합니다.

3. 선택 된 **ASP.NET 웹 응용 프로그램** 프로젝트 템플릿. It appears under **Installed** > **Templates** > **Visual C#** > **Web**. 프로젝트 이름을 `SampleWebAppDemo`로 지정합니다. 선택 된 **소스 제어에 추가** 옵션을 클릭 **확인**합니다.

   ![새 프로젝트 대화 상자](azure-continuous-deployment/_static/01-new-project.png)

4. 에 **새 ASP.NET 프로젝트** 대화 상자에서 ASP.NET 핵심 선택 **빈** 서식 파일을 클릭 한 다음 **확인**합니다.

   ![새 ASP.NET 프로젝트 대화 상자](azure-continuous-deployment/_static/02-web-site-template.png)

5. **소스 제어 선택** 대화 상자에서 **Git** 새 프로젝트에 대 한 소스 제어 시스템으로 합니다.

   ![소스 제어 대화 상자를 선택 합니다.](azure-continuous-deployment/_static/03-source-control.png)

### <a name="running-the-web-app-locally"></a>웹 응용 프로그램을 로컬로 실행

1. Visual Studio에서 앱 만들기 완료 되 면 선택 하 여 앱을 실행 **디버그** -> **디버깅 시작**합니다. 대 안으로, 누르면 **F5**합니다.

   Visual Studio와 새 응용 프로그램을 초기화 하는 데 시간이 걸릴 수 있습니다. 완료 되 면 실행 중인 응용 프로그램은 브라우저에 표시 됩니다.

   ![' Hello World!'을 표시합니다 하는 응용 프로그램을 실행 하는 브라우저 창 표시](azure-continuous-deployment/_static/04-browser-runapp.png)

2. 실행 중인 웹 응용 프로그램을 검토 한 후 브라우저를 닫고 응용 프로그램을 중지 하도록 Visual Studio의 도구 모음에서 "디버깅 중지" 아이콘을 클릭 합니다.

## <a name="create-a-web-app-in-the-azure-portal"></a>Azure 포털에서 웹 앱 만들기

다음 단계는 Azure 포털에서 웹 앱을 만드는 과정을 안내 합니다.

1. 에 로그인 하 여 [Azure 포털](https://portal.azure.com)
2. 누르기 **새로** 포털의 왼쪽 맨 위에 있는
3. 누르기 **웹 + 모바일** > **웹 앱**

    ![Microsoft Azure 포털: 새로 만들기 단추: 웹 + 모바일 시장에서:을 갖춘 응용 프로그램에서 웹 응용 프로그램 단추](azure-continuous-deployment/_static/05-azure-newwebapp.png)

4.  에 **웹 응용 프로그램** 블레이드에서 대 한 고유한 값을 입력에서 **응용 프로그램 서비스 이름**합니다.

    ![웹 앱 블레이드](azure-continuous-deployment/_static/06-azure-newappblade.png)

    >[!NOTE]
    >**응용 프로그램 서비스 이름** 이름은 고유 해야 합니다. 포털의 이름을 입력 하려고 하면이 규칙을 적용 합니다. 각 항목에 대 한 해당 값으로 대체 해야 다른 값을 입력 한 후 **SampleWebAppDemo** 는이 자습서에 표시 합니다.

    &nbsp;
    
    또한는 **웹 응용 프로그램** 블레이드에서 기존 선택 **앱 서비스 계획/위치** 하거나 새로 만듭니다. 새 계획을 만들면 가격 책정 계층, 위치 및 기타 옵션을 선택 합니다. 앱 서비스 계획에 대 한 자세한 내용은 [Azure 앱 서비스 계획의 포괄 개요](https://azure.microsoft.com/en-us/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/)합니다.

5.  
              **만들기**를 클릭합니다. Azure 프로 비전 하 고 웹 앱을 실행 합니다.

    ![Azure 포털: 샘플 웹 응용 프로그램 데모 01 Essentials 블레이드](azure-continuous-deployment/_static/07-azure-webappblade.png)

## <a name="enable-git-publishing-for-the-new-web-app"></a>새 웹 앱에 대 한 Git 게시 사용

Git는 Azure 앱 서비스 웹 앱을 배포 하는 데 사용할 수 있는 분산 된 버전 제어 시스템입니다. 로컬 Git 리포지토리에 웹 앱에 대 한 작성 하는 코드를 저장 하 고 원격 리포지토리로 푸시하여 Azure에 코드를 배포 합니다.

1. 로그인은 [Azure 포털](https://portal.azure.com)로그인 하지 않는 경우.

2. 클릭 **찾아보기**, 탐색 창 맨 아래에 있습니다.

3. 클릭 **웹 앱** Azure 구독과 연결 된 웹 앱의 목록을 볼 수 있습니다.

4. 이 자습서의 이전 섹션에서 만든 웹 앱을 선택 합니다.

5. 하는 경우는 **설정을** 블레이드가 표시 되지 않는 경우, 선택 **설정을** 에 **웹 응용 프로그램** 블레이드입니다.

6. 에 **설정을** 블레이드, **배포 원본** > **소스 선택** > **로컬 Git 리포지토리**합니다.

   ![설정 블레이드에서: 배포 소스 블레이드에서: 소스 블레이드를 선택 합니다.](azure-continuous-deployment/_static/08-azure-localrepository.png)

7. **확인**을 클릭합니다.

8. 이전에 게시 한 웹 응용 프로그램 또는 다른 앱 서비스 앱에 대 한 배포 자격 증명을 설정 하지 않은 경우 지금 설정 합니다.

   * 클릭 **설정을** > **배포 자격 증명**합니다. **배포 자격 증명 설정** 블레이드가 표시 됩니다.

   * 사용자 이름 및 암호를 만듭니다.  Git를 설정할 때이 암호를 나중에 필요 합니다.

   * **저장**을 클릭합니다.

9. 에 **웹 응용 프로그램** 블레이드를 클릭 하 여 **설정을** > **속성**합니다. 에 배포할 원격 Git 리포지토리의 URL 아래에 표시 **GIT URL**합니다.

10. 복사는 **GIT URL** 나중에 자습서에서 사용할 값입니다.

   ![Azure 포털: 응용 프로그램 속성 블레이드](azure-continuous-deployment/_static/09-azure-giturl.png)

## <a name="publish-your-web-app-to-azure-app-service"></a>Azure 앱 서비스 웹 앱을 게시 합니다.

이 섹션에서는 사용 하 여 Visual Studio 및 푸시 해당 리포지토리에서 Azure 웹 앱을 배포 하 여 로컬 Git 리포지토리를 만들게 됩니다. 관련 된 단계는 다음과 같습니다.

   * Azure에 로컬 리포지토리를 배포할 수 있도록 사용자가 GIT URL 값을 사용 하 여 원격 리포지토리에 설정을 추가 합니다.

   * 프로젝트 변경을 내용을 커밋하십시오.

   * Azure에서 로컬 리포지토리를 원격 리포지토리에 프로젝트 변경 내용을 푸시하십시오.

&nbsp;
   
1.  **솔루션 탐색기** 마우스 오른쪽 단추로 클릭 **솔루션 'SampleWebAppDemo'** 선택한 **커밋**합니다. **팀 탐색기** 표시 됩니다.

    ![팀 탐색기 연결 탭](azure-continuous-deployment/_static/10-team-explorer.png)

2.  **팀 탐색기**선택는 **홈** (홈 아이콘) > **설정을** > **리포지토리 설정**합니다.

3.  에 **원격** 의 섹션은 **리포지토리 설정** 선택 **추가**합니다. **원격 추가** 대화 상자가 표시 됩니다.

4.  설정의 **이름** 하는 원격 **Azure SampleApp**합니다.

5.  에 대 한 값을 설정 **인출** 에 **Git URL** 이 자습서의 앞부분에 나오는 Azure에서 복사한 합니다. 이 URL로 끝나는 **.git**합니다.

    ![원격 대화 상자 편집](azure-continuous-deployment/_static/11-add-remote.png)

    >[!NOTE]
    >대 안으로,에서 원격 저장소를 지정할 수 있습니다는 **명령 창** 열어는 **명령 창**, 프로젝트 디렉터리를 변경 하 고 명령을 입력 합니다. 예를 들면 다음과 같습니다.`git remote add Azure-SampleApp https://me@sampleapp.scm.azurewebsites.net:443/SampleApp.git`

6.  선택 된 **홈** (홈 아이콘) > **설정을** > **전역 설정**합니다. 사용자 이름 및 전자 메일 주소를 설정 했는지 확인 합니다. 선택 해야 **업데이트**합니다.

7.  선택 **홈** > **변경** 돌아가려면는 **변경** 보기.

8.  커밋 메시지를 같은 입력 **초기 푸시 #1** 클릭 **커밋**합니다. 이렇게 하면 작업을 *커밋* 로컬로 합니다. 다음을 해야 *동기화* azure입니다.

    ![팀 탐색기 연결 탭](azure-continuous-deployment/_static/12-initial-commit.png)

    >[!NOTE]
    >대 안으로,에서 변경 내용을 커밋할 수 있습니다는 **명령 창** 열어는 **명령 창**, 프로젝트 디렉터리를 변경 하 고 git 명령을 입력 합니다. 예:
    >
    >`git add .`
    >
    >`git commit -am "Initial Push #1"`

9.  Select **Home** > **Sync** > **Actions** > **Open Command Prompt**. 명령 프롬프트를 프로젝트 디렉터리로 열립니다.

10.  명령 창에서 다음 명령을 입력 합니다.

    `git push -u Azure-SampleApp master`

11.  Azure를 입력 **배포 자격 증명** Azure에서 앞서 만든 암호를 합니다.

    >[!NOTE]
    >사용자가 입력할 때 암호는 표시 되지 않습니다.

    이 명령은 로컬 프로젝트 파일을 Azure에 푸시하는 과정을 시작 합니다. 위 명령의 출력이 성공적으로 배포 하는 메시지와 함께 종료 됩니다.
        
    ```
    remote: Finished successfully.
    remote: Running post deployment command(s)...
    remote: Deployment successful.
    To https://username@samplewebappdemo01.scm.azurewebsites.net:443/SampleWebAppDemo01.git
    * [new branch]      master -> master
    Branch master set up to track remote branch master from Azure-SampleApp.
    ```
    > [!NOTE]
    > 프로젝트에서 공동 작업을 수행 해야 할 경우에 고려해 야 [GitHub](https://github.com) 사이 Azure에 푸시합니다.
 
### <a name="verify-the-active-deployment"></a>활성 배포를 확인 합니다.

성공적으로 전송 웹 응용 프로그램 로컬 환경에서 Azure에 확인할 수 있습니다. 나열 된 성공적인 배포에 표시 됩니다.

1. 에 [Azure 포털](https://portal.azure.com), 웹 앱을 선택 합니다. 그런 다음 선택 **설정을** > **연속 배포**합니다.

   ![Azure 포털: 설정 블레이드에서: 성공적인 배포를 보여 주는 배포 블레이드](azure-continuous-deployment/_static/13-verify-deployment.png)

## <a name="run-the-app-in-azure"></a>Azure에서 응용 프로그램을 실행 합니다.

웹 앱을 Azure에 배포 했으므로 응용 프로그램을 실행할 수 있습니다.

이 작업은 다음 두 가지 방법으로 수행할 수 있습니다.

* Azure 포털에서 웹 앱 블레이드에서 웹 앱 찾아 클릭 **찾아보기** 기본 브라우저에서 앱을 볼 수 있습니다.

* 브라우저를 열고 웹 앱에 대 한 URL을 입력 합니다. 예:

  `http://SampleWebAppDemo.azurewebsites.net`

## <a name="update-your-web-app-and-republish"></a>웹 앱을 업데이트 하 고 다시 게시

지역 코드를 변경한 후 다시 게시할 수 있습니다.

1.  **솔루션 탐색기** 의 Visual Studio를 열고는 *Startup.cs* 파일입니다.

2.  에 `Configure` 메서드를 수정는 `Response.WriteAsync` 메서드를 다음과 같이 표시 되도록 합니다.

    ```aspx-cs
    await context.Response.WriteAsync("Hello World! Deploy to Azure.");
    ```
3.  변경 내용을 저장 하 *Startup.cs*합니다.

4.  **솔루션 탐색기**를 마우스 오른쪽 단추로 클릭 **솔루션 'SampleWebAppDemo'** 선택한 **커밋**합니다. **팀 탐색기** 표시 됩니다.

5.  예: 커밋 메시지를 입력 합니다.

    ```none
    Update #2
    ```

6.  키를 눌러는 **커밋** 프로젝트 변경 내용을 커밋하는 단추입니다.

7.  Select **Home** > **Sync** > **Actions** > **Push**.

>[!NOTE]
>대 안으로,에서 변경 내용을 푸시할 수 있습니다는 **명령 창** 열어는 **명령 창**, 프로젝트 디렉터리를 변경 하 고 git 명령을 입력 합니다. 예:
>
>`git push -u Azure-SampleApp master`

## <a name="view-the-updated-web-app-in-azure"></a>Azure에서 업데이트 된 웹 응용 프로그램 보기

업데이트 된 웹 앱을 선택 하 여 볼 **찾아보기** Azure 포털에서 또는 브라우저를 열고 웹 앱에 대 한 URL을 입력 하 여 웹 앱 블레이드에서 합니다. 예:

   `http://SampleWebAppDemo.azurewebsites.net`

## <a name="additional-resources"></a>추가 리소스

* [게시 및 배포](index.md)

* [프로젝트 Kudu](https://github.com/projectkudu/kudu/wiki)

