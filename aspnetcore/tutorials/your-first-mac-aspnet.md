---
title: "Mac 또는 Visual Studio 코드를 사용 하 여 Linux에서 ASP.NET 핵심 응용 프로그램을 구축 | Microsoft 문서"
author: spboyer
description: "이 문서에서 안내해 dotnet CLI를 사용 하 여 ASP.NET 코어 및 Visual Studio Code에 대 한 Mac에서 웹 응용 프로그램 처음 만들기"
keywords: "ASP.NET Core, macOS, Yeoman, 생성기 aspnet Visual Studio 코드, Linux, VS 코드"
ms.author: riande
manager: wpickett
ms.date: 03/09/2017
ms.topic: article
ms.assetid: dcc08e09-e73e-4feb-84ce-8219b7e544ef
ms.technology: aspnet
ms.prod: asp.net-core
uid: tutorials/your-first-mac-aspnet
translationtype: Machine Translation
ms.sourcegitcommit: bff0c6a3f481fa4c6ec73c98d5048fb82e4eacc3
ms.openlocfilehash: 4d47c0dbb4bca14912ea4d95f61e4aebe9cd83cc
ms.lasthandoff: 03/23/2017

---
# <a name="build-an-aspnet-core-app-on-a-mac-or-linux-using-visual-studio-code"></a>Mac 또는 Visual Studio 코드를 사용 하 여 Linux에서 ASP.NET 핵심 응용 프로그램을 구축 합니다.

이 문서에서는 Linux 또는 macOS에서 첫 번째 ASP.NET 핵심 응용 프로그램을 작성 하는 방법을 보여 줍니다.

## <a name="setting-up-your-development-environment"></a>개발 환경 설정

개발 컴퓨터 다운로드를 설치 하 고 설정 하려면 [.NET Core](https://microsoft.com/net/core) 및 [Visual Studio 코드](https://code.visualstudio.com) 와 [C# 확장](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)합니다.

## <a name="scaffolding-applications-using-dotnet-new"></a>Dotnet new를 사용 하는 응용 프로그램 스 캐 폴딩

사용할 `dotnet new` "빈 웹 서식 파일"을 사용 하 여 새 웹 응용 프로그램을 생성 합니다. 라는 프로젝트에 대 한 작업 디렉터리를 만들고 *firstapp* 합니다. cd를 *firstapp*합니다.

Visual Studio 코드를 시작 하 고 엽니다는 *firstapp* 폴더입니다. Ctrl + '\`'(역따옴표)를 눌러 VS Code에서 포함된 터미널을 엽니다. 또는 별도 터미널 창을 사용 합니다.
실행의 `dotnet new` 명령을 만들려면 새 웹 응용 프로그램 전달는 `mvc` 서식 파일 형식으로 매개 변수입니다.

```console
dotnet new mvc
```

CLI 명령 완료 시; 다음과 같은 출력와 파일이 생성 됩니다.

```console
Content generation time: 79.6691 ms
The template "Empty ASP.NET Core Web Application" created successfully.
```

* Startup.cs: [시작 클래스](../fundamentals/startup.md) -응용 프로그램에 대 한 모든 요청을 처리 하는 요청 파이프라인을 구성 하는 클래스입니다.
* Program.cs: [Program 클래스](../fundamentals/index.md) 하는 응용 프로그램의 주 진입점을 포함 합니다.
* firstapp.csproj: [프로젝트 파일](https://docs.microsoft.com/en-us/dotnet/articles/core/preview3/tools/csproj) ASP.NET 핵심 응용 프로그램에 대 한 MSBuild 프로젝트 파일 형식입니다. 프로젝트 간 참조가 NuGet 참조 및 기타 관련된 항목을 프로젝트 합니다.
* appsettings.json / appsettings 합니다. Development.json: 환경 기본 응용 프로그램 설정 구성 파일입니다. [구성 참조](xref:fundamentals/configuration)합니다.
* bower.json: 프로젝트에 대 한 패키지 종속성 Bower 합니다.
* .bowerrc: Bower 자산을 다운로드할 때 구성 요소를 설치 하는 위치를 정의 하는 bower 구성 파일입니다.
* bundleconfig.json: 묶음 및 축소 프런트 엔드 JavaScript 및 CSS 자산에 사용 되는 구성 파일입니다.
* 뷰: Razor 뷰를 포함합니다. 뷰는 응용 프로그램의 사용자 인터페이스 (UI)를 표시 하는 구성 요소입니다. 일반적으로이 UI에는 모델 데이터가 표시 됩니다.
* 컨트롤러: 처음에 포함 된 MVC 컨트롤러 *HomeController.cs*합니다. 컨트롤러는 브라우저 요청을 처리 하는 클래스입니다.
* wwwroot: 웹 응용 프로그램 루트 폴더입니다.

자세한 내용은 참조 [The MVC 패턴](xref:mvc/overview)합니다.

## <a name="developing-aspnet-core-applications-on-a-mac-with-visual-studio-code"></a>Visual Studio 코드 Mac에서 ASP.NET 핵심 응용 프로그램 개발

Visual Studio 코드 (VS 코드)에서 프로젝트 폴더를 엽니다. VS Code는 필요한 프로젝트 종속성을 복원 하 고 빌드/디버그 종속성을 추가 하 라는 메시지가 나타납니다. 누르기 **예** 를 빌드 및 디버그 자산을 추가 하거나 누른 다음 **복원** 프로젝트 종속성을 복원 하려면.

![정보 메시지: 2. 2. 필요한 자산을 빌드하고 디버깅 프로젝트에서 누락 되었습니다. 추가할?](your-first-mac-aspnet/_static/debug-add-items-prompt.png)

에 대 한 **복원**, 또는 실행할 수 있습니다 `dotnet restore` 터미널에서 입력 또는 `⌘⇧P` 또는 `Ctrl+Shift+P` VS 코드에서 및를 입력 한 후 `.NET` 같이 합니다.

![명령 모음에 대 한 '점' 입력에 자동 완성 옵션을 보여 주는 ' dotnet: 복원 패키지 '](your-first-mac-aspnet/_static/dot-restore.png)

VS Code은 파일 및 생산성 코딩 enviromment 작업에 대 한 효율적인된 정리 인터페이스를 제공 합니다. 

왼쪽된 탐색 모음에서&5; 개의 아이콘을 나타내는&4; 개의 viewlets 있습니다.

* 탐색
* 검색
* Git
* 디버그
* 확장

탐색기 viewlet 폴더 탐색 및 열려 있는 파일의 보기를 제공 합니다. 저장 하지 않은 변경 내용으로 파일을 나타내기 위해 배지를 표시 합니다. viewlet에 새 폴더와 파일을 만들 수 있습니다. 선택할 수 있습니다 **모두 저장** 위로 마우스에 나타나는 메뉴 옵션입니다.

검색 viewlet를 사용 하면 열려 있는 파일의 폴더 트리를 검색할 수 있습니다. 파일 이름 및 파일 내용에 대 한 검색이 됩니다.

*VS Code* 시스템에 설치 된 경우 Git와 통합 합니다. 새 저장소를 초기화 하 고, 커밋 확인 하 고, Git viewlet에서 변경 사항을 푸시 수입니다.

![GIT 사이드바 '이 작업 영역 되지 않았으며 git 소스 제어에서 ' 'git 리포지토리 초기화' 단추를 나타내는](your-first-mac-aspnet/_static/vscode-git.png)

디버그 viewlet 응용 프로그램의 대화형 디버깅을 지원 합니다.

VS 코드 편집기에는 수많은 훌륭한 기능입니다. 사용 하 여 사용 하지 않는 보면 문을 밑줄이 표시 되 고 사용 하 여 자동으로 제거할 수 있습니다 `⌘ .` 또는 `Ctrl + .` 전구 모양 아이콘 표시 되 면 합니다. 클래스 및 메서드 얼마나 많은 참조를 프로젝트에 표시할 수도 있습니다.

편집기에 대 한 자세한 내용은 [Visual Studio 코드](https://code.visualstudio.com)합니다.

## <a name="using-the-vs-code-debugger"></a>VS Code 디버거를 사용 하 여

샘플 사용 하도록 구성 된 [Kestrel](../fundamentals/servers/kestrel.md) 웹 서버에 대 한 합니다.

디버거에서 앱을 실행 합니다.

* 왼쪽된 창에서 보기 표시줄에 있는 디버그 아이콘을 누르기

* 앱을 시작 하려면 "(F5) Play" 아이콘을 누르세요

![사이드바 삼각형 재생 단추를 보여 주는 디버그](your-first-mac-aspnet/_static/launch-debugger.png)

기본 브라우저가 자동으로 시작 하 고 이동`http://localhost:5000`

![브라우저 창](your-first-mac-aspnet/_static/myfirstapp.png)

* 응용 프로그램을 중지 하려면 브라우저를 닫고 디버그 표시줄에 "Stop" 아이콘을 누르면

![VS 코드 디버그 모음](your-first-mac-aspnet/_static/debugger.png)

### <a name="using-the-dotnet-commands"></a>Dotnet 명령을 사용 하 여

* 실행 `dotnet run` 터미널/bash에서 앱을 시작 하는 명령

* 로 이동`http://localhost:5000`

* 웹 서버를 중지 하려면 누릅니다 `⌃+C` 또는 `Ctrl+C`합니다.

## <a name="publishing-to-azure"></a>Azure에 게시

프로덕션 환경에서 호스트에 업데이트를 푸시할 Git 통합을 제공 하는 VS Code [Microsoft Azure](http://azure.microsoft.com)합니다.

### <a name="initialize-git"></a>Git 초기화

작업 하는 폴더에서 Git를 초기화 합니다. Git viewlet 푸시 알림을 탭 하 고 클릭 하 고 `Initialize Git repository` 단추입니다.

![GIT 사이드바](your-first-mac-aspnet/_static/vscode-git-commit.png)

커밋 메시지 및 tap를 입력 하거나 탭 준비 된 파일을 적용 하 고 확인 표시 아이콘을 추가 합니다.

![GIT 사이드바 보여 주는 파일 변경](your-first-mac-aspnet/_static/init-commit.png)

Git 변경 내용 추적는 Git viewlet 마지막 커밋 이후 변경 된 파일에 표시 되는 파일에 대 한 업데이트를 확인 하는 경우.

### <a name="initialize-azure-website"></a>Azure 웹 사이트 초기화

Git를 사용 하 여 직접 Azure 웹 앱에 배포할 수 있습니다.

* Azure 계정이 없으면 다음을 할 수 있습니다 [무료 평가판 만들](http://azure.microsoft.com/en-us/pricing/free-trial/)합니다.

새 응용 프로그램을 호스트 하는 Azure 포털에서 웹 앱을 만듭니다.

![Microsoft Azure 포털: 새로 만들기 단추: 웹 + 모바일 선택 마켓플레이스 목록에 주요 응용 프로그램에서 웹 응용 프로그램 단추 표시](your-first-mac-aspnet/_static/create-web-app.png)

웹 앱을 지원 하기 위해 Azure에서 구성 [Git를 사용 하 여 연속 배포](https://azure.microsoft.com/en-us/documentation/articles/app-service-deploy-local-git/)합니다.

Azure 포털에서 웹 앱에 대 한 Git URL을 기록 합니다.

![웹 응용 프로그램에 대 한 azure 포털: 요약 패널](your-first-mac-aspnet/_static/azure-portal.png)

터미널 창에서 명명 된 원격 추가 `azure` 이전에 기록한 Git url입니다.

`git remote add azure https://shayneboyer@myfirstappmac.scm.azurewebsites.net:443/MyFirstAppMac.git`

Master로 푸시하십시오.  `git push azure master`배포 합니다.

   ![성공적인 배포를 표시 하는 명령 창](your-first-mac-aspnet/_static/git-push-azure-master.png)

새로 배포 된 웹 앱으로 이동 합니다.

![브라우저 창](your-first-mac-aspnet/_static/azure.png)

Azure 포털에서 배포 세부 정보를 살펴보면 볼 수 있습니다 로그 및 단계 있을 때마다 분기에 커밋.

![웹 응용 프로그램에 대 한 azure 포털: 배포 세부 정보](your-first-mac-aspnet/_static/deployment.png)

## <a name="additional-resources"></a>추가 리소스

* [Visual Studio 코드](https://code.visualstudio.com)
* [기본 사항](../fundamentals/index.md)

