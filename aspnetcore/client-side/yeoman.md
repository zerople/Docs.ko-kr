---
title: "Yeoman ASP.NET 코어에서 사용한 프로젝트 빌드"
author: spboyer
description: "이 문서에서 ASP.NET Core는 Yeoman를 사용 하 여 웹 응용 프로그램을 구축 안내 macOS에서 생성기입니다."
keywords: "ASP.NET Core, Yeoman, 플랫폼 간 yo aspnet"
ms.author: spboyer
manager: wpickett
ms.date: 07/05/2017
ms.topic: article
ms.assetid: fda0c2a8-1743-4505-be1a-7f8ceeef8647
ms.technology: aspnet
ms.prod: asp.net-core
uid: client-side/yeoman
ms.openlocfilehash: d7411c1635e9fef2857f9a03e7310224ee8d7344
ms.sourcegitcommit: 78d28178345a0eea91556e4cd1adad98b1446db8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2017
---
# <a name="introduction-to-building-projects-with-yeoman-in-aspnet-core"></a>프로젝트에서 ASP.NET Core Yeoman와 빌드 소개

[Yeoman](http://yeoman.io/) 는 많은 종류의 응용 프로그램을 만들기 위한 프로젝트 스 캐 폴딩 시스템입니다. Yeoman ASP.NET Core에 대 한 생성기에 다양 한 새 웹, MVC 또는 콘솔 응용 프로그램을 시작 하기 위한 프로젝트 템플릿이 포함 되어 있습니다.

## <a name="install-nodejs-npm-and-yeoman"></a>Node.js, npm 및 Yeoman 설치

### <a name="prerequisites"></a>필수 구성 요소

Node.js 및 npm Yeoman 필요 않습니다. 다운로드 [Node.js](https://nodejs.org/)합니다. 설치 관리자 포함 [Node.js](https://nodejs.org/) 및 [npm](https://www.npmjs.com/)합니다. Bower 역시 부트스트랩와 같은 UI 프레임 워크를 설치 하는 데 필요 합니다.

Yeoman 및 Bower를 설치 하려면 다음 명령을 실행 합니다.

```console
npm install -g yo bower
```

>[!Note]
>오류가 발생 하는 경우 `npm ERR! Please try running this command again as root/Administrator.` macOS 등에서 사용 하 여 다음 명령을 실행 [sudo](https://developer.apple.com/library/mac/documentation/Darwin/Reference/ManPages/man8/sudo.8.html):`sudo npm install -g yo bower`

명령 프롬프트에서 ASP.NET 생성기를 설치 합니다.

```console
npm install -g generator-aspnet
```

> [!NOTE]
> 명령을 실행 하는 사용 권한 오류가 발생할 경우 `sudo` 위에서 설명한 것 처럼 합니다.

`–g` 플래그 설치 생성기 전체적으로 하므로 모든 경로에서 사용할 수 있습니다.

## <a name="create-an-aspnet-app"></a>ASP.NET 응용 프로그램 만들기

ASP.NET Yeoman 기반 생성기를 실행 합니다.

```console
yo aspnet
```

생성자에 메뉴가 표시 됩니다. 아래쪽 화살표는 **멤버 자격 및 권한 부여] [없이 웹 응용 프로그램 기본** 프로젝트 및 탭 **Enter**:

![명령 창: 원하는 어떤 유형의 응용 프로그램? 응용 프로그램 종류의 메뉴](yeoman/_static/yeoman-yo-aspnet.png)

UI 프레임 워크도 부트스트랩을 선택 하 고 탭 **Enter**합니다.

사용 하 여 "**MyWebApp**" 응용 프로그램 이름 및 다음 tap에 대 한 **Enter**합니다.

Yeoman은 프로젝트와 해당 지원 파일 스 캐 폴드 할 됩니다. 권장 되는 다음 단계 명령의 형태로 제공 됩니다.

![명령 창: ASP.NET 응용 프로그램의 이름은 무엇입니까? 명령 프롬프트](yeoman/_static/yeoman-yo-aspnet-created.png)

[ASP.NET 생성기](https://www.npmjs.com/package/generator-aspnet) ASP.NET Core Visual Studio 코드를 Visual Studio에 로드 하거나 명령줄에서 실행할 수 있는 프로젝트를 만듭니다.

## <a name="restore-build-and-run"></a>복원, 빌드 및 실행

디렉터리를 변경 하 여 제안 된 명령에 따라는 `MyWebApp` 디렉터리입니다. 그러고 나 서 `dotnet restore`합니다.

![명령 창](yeoman/_static/dotnet-restore.png)

빌드 및 사용 하 여 앱 실행 `dotnet build` 및 `dotnet run`:

![명령 창](yeoman/_static/dotnet-build-run.png)

이 시점에서 새로 만든 ASP.NET Core 응용 프로그램을 테스트 하려면 표시 된 URL로 이동할 수 있습니다.

## <a name="client-side-packages"></a>클라이언트 패키지

프런트 엔드 리소스는 Yeoman에서 템플릿에 의해 제공 되며 생성기를 사용 하는 [Bower](xref:client-side/bower) , 추가 클라이언트 쪽 패키지 관리자 *bower.json* 및 *.bowerrc* Bower를 사용 하 여 클라이언트 패키지를 복원 하는 파일입니다.

[BundlerMinifier](xref:client-side/bundling-and-minification) 구성 요소 (묶음) 연결의 용이성 및 CSS, JavaScript 및 HTML의 축소에 대해 기본적으로도 포함 되어 있습니다.

## <a name="building-and-running-from-visual-studio"></a>Visual Studio에서 빌드하고 실행

생성 된 ASP.NET Core 웹 프로젝트를 Visual Studio를 직접 로드 지정 합니다 빌드 및에서 프로젝트를 실행 합니다. 새 ASP.NET Core 응용 프로그램 Yeoman를 사용 하 여 스 캐 폴드 하려면 위의 지침을 따릅니다. 이 시간 선택 **웹 응용 프로그램** 응용 프로그램 이름을 확인 하 고 메뉴 `MyWebApp`합니다.

Visual Studio를 엽니다. 파일 메뉴에서 열기 ‣ 프로젝트/솔루션을 선택 합니다.

프로젝트 열기 대화 상자에서로 이동 된 *.csproj* 파일을 선택 하 고 클릭는 **열려** 단추입니다. 솔루션 탐색기에서 프로젝트 아래 스크린샷과 같이 다음과 같아야 합니다.

![파일 및 폴더의 솔루션 탐색기에서 새 프로젝트](yeoman/_static/yeoman-solution.png)

Yeoman scaffolds MVC 웹 응용 프로그램, 완벽 하 게 둘 다 서버 및 클라이언트 쪽 빌드 지원입니다. 서버 쪽 종속성 아래에 나열 됩니다는 **종속성/NuGet** 노드와의 클라이언트 쪽 종속성의 **종속성/Bower** 솔루션 탐색기의 노드. 종속성은 프로젝트가 로드 되 면 자동으로 복원 됩니다.

![솔루션 탐색기 트리 뷰에서 종속성 노드에서 Bower 폴더는 해당 종속성을 나열 열려 있습니다.](yeoman/_static/yeoman-loading-dependencies.png)

모든 종속 관계를 복원할 때 키를 눌러 **F5** 프로젝트를 실행 합니다. 기본 홈 페이지가 브라우저에 표시 됩니다.

![웹 응용 프로그램에서 Microsoft Edge 열기](yeoman/_static/yeoman-home-page.png)

## <a name="restoring-building-and-hosting-from-a-command-line"></a>복원, 건물, 및 명령줄에서 호스팅

준비 하 고.NET Core CLI를 사용 하 여 웹 응용 프로그램을 호스팅할 수 있습니다.

명령 프롬프트에서 프로젝트가 포함 된 폴더에 현재 디렉터리를 변경 (즉, 포함 된 폴더로 *.csproj* 파일):

```console
cd src\MyWebApp
```

프로젝트의 NuGet 패키지 종속 파일을 복원 합니다.

```console
dotnet restore
```

응용 프로그램을 실행 합니다.

```console
dotnet run
```

플랫폼 간 [Kestrel](xref:fundamentals/servers/kestrel) 웹 서버에서 5000 포트에서 수신 대기를 시작 됩니다.

웹 브라우저를 열고 이동 `http://localhost:5000`합니다.

![웹 응용 프로그램에서 Microsoft Edge 열기](yeoman/_static/yeoman-home-page_5000.png)

## <a name="adding-to-your-project-with-sub-generators"></a>Sub 생성기에 프로젝트에 추가합니다.

Yeoman를 사용 하 여 [생성기 하위](https://github.com/omnisharp/generator-aspnet)를 추가할 수 있습니다는 `nuget.config` 또는 `web.config` 프로젝트가 만들어진 후 합니다. 예를 들어 파일 만들지 디렉터리에서 다음 명령을 실행 합니다.

```console
yo aspnet:nugetconfig
```

결과 라는 NuGet 구성 파일 `nuget.config` 다음 내용을 사용 하 여:

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
 <packageSources>
    <!--To inherit the global NuGet package sources remove the <clear/> line below -->
    <clear />
 </packageSources>
</configuration>
```

## <a name="additional-resources"></a>추가 리소스

* [서버 (Kestrel 및 WebListener)](xref:fundamentals/servers/index)
* [기본 사항](xref:fundamentals/index)
