---
title: "VSTS를 사용 하 여 Azure에 ASP.NET 핵심 응용 프로그램을 게시 | Microsoft 문서"
author: rick-anderson
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 3bfc8a31-c187-45d0-aa29-8942a5df6292
ms.technology: aspnet
ms.prod: asp.net-core
uid: publishing/vsts-continuous-deployment
translationtype: Machine Translation
ms.sourcegitcommit: f42a2e1e42a2231c3fc4898fe98f61c89176b555
ms.openlocfilehash: dcb7965551c197bfdf7898a3d1b79ed9caf66507
ms.lasthandoff: 03/23/2017

---
# <a name="use-vsts-to-build-and-publish-to-an-azure-web-app-with-continuous-deployment"></a>VSTS를 사용 하 여 연속 배포를 사용 하 여 Azure 웹 앱을 빌드하고 게시 하려면

[Damien Pontifex](https://github.com/DamienPontifex)

이 자습서에서는 Visual Studio를 사용 하 여 ASP.NET 핵심 웹 응용 프로그램을 만들고 Azure 앱 서비스에 Visual Studio에서 배포 하는 방법을 지속적으로 배포를 사용 합니다.
   
> [!NOTE]
> 이 자습서를 완료 하려면 Microsoft Azure 계정이 필요 합니다. 계정이 없으면 다음을 할 수 있습니다 [MSDN 구독자 혜택을 활성화](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F) 또는 [무료 평가판에 등록할](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F)합니다. Visual Studio Team Services 계정도 필요 합니다. 계정이 없으면 다음을 할 수 있습니다 [무료 평가판에 등록할](https://www.visualstudio.com/products/visual-studio-team-services-vs)합니다.

## <a name="prerequisites"></a>필수 조건

이 자습서에서는 이미 다음 항목이 있다고 가정 합니다.

* [ASP.NET Core](https://www.microsoft.com/net/core) (런타임 및 도구). VSTS의 호스트 그룹 구성 서버에 이미 RC2 도구가 설치 되어 있습니다.

* [Git](http://git-scm.com/downloads)

* [Trackyon 장점은](https://marketplace.visualstudio.com/items?itemName=Trackyon.trackyonadvantage) 팀 서비스 계정에 설치 된 확장 합니다. 그러면 이후 단계에 대 한 사용 가능한 zip 임무를 추가 됩니다.

## <a name="setup-vsts-build"></a>VSTS 빌드 설정

1.  이후 단계 보다 명확 하 고 빌드 단계 중에 일관 된 경로 유지 하기가 수 있도록 몇 가지 빌드 변수를 설정 합니다.

    변수를 만들어 **PublishOutput** 원하는 경로를 설정 합니다. 사용 했습니다.`$(Build.StagingDirectory)/WebApplication`

    변수를 만들어 **사용 하 여 DeployPackage** 에 압축 된 웹 패키지 경로를 설정 합니다. 사용 했습니다 `$(Build.StagingDirectory)/WebApplication.zip` 게시 출력 함께 있어야 합니다.

    ![변수 탭](vsts-continuous-deployment/_static/setup-build-variables.png)

    > [!NOTE]
    > 호스팅된 빌드 에이전트를 사용 하는 ASP.NET 핵심 응용 프로그램을 빌드하고, 호스트 캐시 패키지를 시도 합니다. 호스팅된 서버 캐시를 유지 하지 않습니다이 단계를 건너뛸 나타내며 여기에 다른 변수를 추가 하 여 복원 시간을 줄일 수 있습니다.  
   
    *  이름:`DOTNET_SKIP_FIRST_TIME_EXPERIENCE`  
    *  값:`true`  

2.  명령줄 빌드 단계를 사용 하 여 패키지를 복원 하도록 합니다.

    * 클릭 **추가 빌드 단계...** and choose **Utility** > **Command Line** > **Add**

    * 빌드 단계에 대 한 인수를 설정 합니다.

        * 도구:`dotnet`

        * 인수: `restore`

    ![빌드 탭](vsts-continuous-deployment/_static/dotnet-restore.png)

3.  또 다른 명령줄 빌드 단계를 사용 하 여 프로젝트를 게시할 수 있습니다.

    * 클릭 **추가 빌드 단계...** and choose **Utility** > **Command Line** > **Add**

    * 빌드 단계에 대 한 인수를 설정 합니다.

        * 도구:`dotnet`

        * 인수: `publish src/WebApplication --configuration $(BuildConfiguration) --output $(PublishOutput)`

    * Src/웹 응용 프로그램을 적절 하 게 배포할 수 있는 응용 프로그램의 경로로 바꿉니다.

    ![빌드 탭](vsts-continuous-deployment/_static/dotnet-publish.png)

4.  Azure 앱 서비스에 배포할 수 있도록 게시 된 출력을 압축 합니다. 사용 하 여는 [Trackyon 장점은](https://marketplace.visualstudio.com/items?itemName=Trackyon.trackyonadvantage) 설치 배포에 대 한 게시 된 출력의 내용을 압축 하는 작업입니다.

    * 클릭 **추가 빌드 단계...** and choose **Utility** > **Trackyon Zip** > **Add**

    * 으로 zip 빌드 단계에 대 한 인수를 설정 합니다.

        * Zip 폴더:`$(PublishOutput)`

        * 최종 Zip 파일의 경로:`$(DeployPackage)`

    ![빌드 탭](vsts-continuous-deployment/_static/compress-publish-output.png)

5.  Azure 웹 응용 프로그램 배포 빌드 단계를 사용 하 여 Azure 웹 앱에 압축 된 게시 출력을 게시할 수 있습니다. 웹 배포 패키지의 4 단계에서 압축 된 내용이 출력 됩니다. 이 경우에서는 다시 사용 경로 이전 설정에 대 한 변수입니다.

    * 클릭 **추가 빌드 단계...** 선택한 **배포** > **Azure 웹 응용 프로그램 배포** > **추가**

    * 로 배포 단계에 대 한 인수를 설정 합니다.

        * Azure 구독:*<your configured azure connection>*

        * 웹 앱 위치:*<desired region>*

        * 웹 응용 프로그램 이름:*<desired app service name>*

        * 웹 배포 패키지:`$(DeployPackage)`

![빌드 탭](vsts-continuous-deployment/_static/web-app-deployment.png)

## <a name="use-vsts-release"></a>VSTS 릴리스를 사용 하 여

참조 [연속 배포를 사용 하 여 Azure 웹 앱을 빌드하고 게시를 사용 하 여 VSTS](https://www.visualstudio.com/en-us/docs/build/get-started/aspnet-4-ci-cd-azure-automatic)에 대 한 연속 배달 (CD) 워크플로 구성 하는 방법을 보여 주는 [Azure 앱 서비스](https://azure.microsoft.com/en-us/documentation/articles/app-service-changes-existing-services/) Visual Studio Team Services를 사용 합니다. Azure 연속 배달 Team Services에서 Azure 앱 서비스에 응용 프로그램에 대 한 업데이트를 게시 하는 강력한 배포 파이프라인 설정을 간소화 합니다. 파이프라인을 빌드, 테스트 실행, 스테이징 슬롯에 배포 및 다음 프로덕션에 배포 하는 Azure 포털에서 구성할 수 있습니다.

VSTS 릴리스 관리는 VSTS 빌드에서 릴리스 파이프라인을 관리 또는 사용할 수 있습니다. 빌드 파이프라인 및 릴리스 프로세스의 설정을 약간 변경 해야 했습니다.

1.  를 구성 하는 경우 이전 섹션에서 VSTS 빌드 설치 프로그램에서 Azure 웹 응용 프로그램 배포 단계를 제거 합니다.

2.  빌드 파이프라인에 복사 하 고 빌드 아티팩트 게시 단계 추가

    *  클릭 **추가 빌드 단계...** 선택한 **유틸리티** > **복사 및 빌드 아티팩트 게시** > **추가**

    *  복사에 대 한 인수를 설정 하 고 단계를 게시 합니다.

        *  내용:`$(DeployPackage)`

        *  아티팩트 이름:`DeployPackage`

        *  아티팩트 유형:`Server`

3.  릴리스 정 및 빌드 정의에 대 한 링크를 만들고 여기에 대해 2 단계 게시에서에서 복사 된 아티팩트가 단계를 수 있습니다.

## <a name="additional-resources"></a>추가 리소스

* [게시 및 배포](index.md)

* [팀 서비스 빌드](https://www.visualstudio.com/docs/build/overview)

* [팀 서비스 릴리스](https://www.visualstudio.com/en-us/docs/release/overview)

