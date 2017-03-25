---
title: "ASP.NET Core에서 Yeoman를 사용 하 여 프로젝트를 빌드 | Microsoft 문서"
author: spboyer
description: "이 문서에서는 ASP.NET 핵심은 Yeoman를 사용 하 여 웹 응용 프로그램 구축을 안내 macOS의 생성기입니다."
keywords: "ASP.NET Core, Yeoman, 크로스 플랫폼 yo aspnet"
ms.author: spboyer
manager: wpickett
ms.date: 02/21/2017
ms.topic: article
ms.assetid: fda0c2a8-1743-4505-be1a-7f8ceeef8647
ms.technology: aspnet
ms.prod: asp.net-core
uid: client-side/yeoman
translationtype: Machine Translation
ms.sourcegitcommit: 0ebb9b63931ccb26126740de08270eda9b1ea486
ms.openlocfilehash: 2970612b138d7a79c26bf52939418d6ee317b042
ms.lasthandoff: 03/23/2017

---
# <a name="introduction-to-building-projects-with-yeoman-in-aspnet-core"></a>ASP.NET Core에서 Yeoman를 사용 하 여 프로젝트를 빌드 소개

[Yeoman](http://yeoman.io/) 은 많은 종류의 응용 프로그램을 만들기 위한 프로젝트 스 캐 폴딩 시스템입니다. Yeoman 생성기 ASP.NET 핵심에 대 한 다양 한 새로운 웹, MVC 또는 콘솔 응용 프로그램을 시작 하기 위한 프로젝트 템플릿 포함 합니다.

## <a name="install-nodejs-npm-and-yeoman"></a>Node.js, npm 및 Yeoman 설치

### <a name="prerequisites"></a>필수 조건

Node.js 및 npm은 Yeoman에 필요 합니다. 다운로드 [Node.js](https://nodejs.org/en/)합니다. 설치 관리자 포함 [Node.js](https://nodejs.org/en/) 및 [npm](https://www.npmjs.com/)합니다. Bower 역시 스타일 시트와 같은 UI 구성 요소를 설치 하는 데 필요 합니다.

Yeoman 및 Bower는를 설치 하려면 다음 명령을 실행 합니다.

```console
npm install -g yo bower
```

>[!Note]
>오류가 발생 하는 경우 `npm ERR! Please try running this command again as root/Administrator.` macOS, 실행을 사용 하 여 다음 명령을 [sudo](https://developer.apple.com/library/mac/documentation/Darwin/Reference/ManPages/man8/sudo.8.html):`sudo npm install -g yo bower`

명령 프롬프트에서 ASP.NET 생성기를 설치 합니다.

```console
npm install -g generator-aspnet@0.2.6
```

> [!NOTE]
> 아래 명령을 실행 하는 사용 권한 오류를 받게 되 면 `sudo` 위에 설명 된 대로 합니다.

`–g` 플래그 생성기를 설치 전체적으로 모든 경로에서 사용할 수 있도록 합니다.

## <a name="create-an-aspnet-app"></a>ASP.NET 응용 프로그램 만들기

에 대 한 ASP.NET 생성기를 실행 합니다.`yo`

```console
yo aspnet
```

생성기는 메뉴를 표시합니다. 아래쪽 화살표는 **멤버 자격 및 권한 부여] [없이 웹 응용 프로그램 기본** 프로젝트를 탭 한 다음 **Enter**:

![명령 창: 원하는 어떤 유형의 응용 프로그램? 응용 프로그램 종류의 메뉴](yeoman/_static/yeoman-yo-aspnet.png)

UI 프레임 워크로 Bootstrap을 선택 하 고 탭 **Enter**합니다.

사용 하 여 "**확인**" 응용 프로그램 이름 및 누르십시오 **Enter**합니다.

Yeoman은 프로젝트와 해당 지원 파일을 스 캐 폴딩 합니다. 제안된 된 다음 단계 명령의 형태로 제공 됩니다.

![명령 창: ASP.NET 응용 프로그램의 이름은 무엇입니까? 명령 프롬프트](yeoman/_static/yeoman-yo-aspnet-created.png)

[ASP.NET 생성기](https://www.npmjs.com/package/generator-aspnet) ASP.NET 핵심 Visual Studio 코드를 Visual Studio에 로드 하거나 명령줄에서 실행할 수 있는 프로젝트를 만듭니다.

## <a name="restore-build-and-run"></a>복원, 빌드 및 실행

디렉터리를 변경 하 여 제안 된 명령에 따라는 `MyWebApp` 디렉터리입니다. 그러고 나 서 `dotnet restore`합니다.

![명령 창](yeoman/_static/dotnet-restore.png)

빌드 및 사용 하 여 앱을 실행 `dotnet build` 및 `dotnet run`:

![명령 창](yeoman/_static/dotnet-build-run.png)

이 시점에서 새로 만든된 ASP.NET 핵심 앱을 테스트 하려면 표시 된 URL을 탐색할 수 있습니다.

## <a name="client-side-packages"></a>클라이언트 쪽 패키지

프런트 엔드 리소스를 사용 하 여 yeoman 생성기의 템플릿을 제공는 [Bower](bower.md) 클라이언트측 패키지 관리자를 추가 *bower.json* 및 *.bowerrc* 사용 하 여 클라이언트 쪽 패키지를 복원할 파일의 [Bower](bower.md) 클라이언트측 패키지 관리자입니다.

[BundlerMinifier](https://github.com/madskristensen/BundlerMinifier/wiki) 구성 요소 (묶음) 연결의 용이성 및 CSS, JavaScript 및 HTML의 축소에 대해 기본적으로도 포함 됩니다.

## <a name="building-and-running-from-visual-studio"></a>Visual Studio에서 빌드하고 실행

생성 된 ASP.NET 핵심 웹 프로젝트를 Visual Studio를 직접 로드 하 고 빌드 하 고, 해당 위치에서 프로젝트를 실행 수 있습니다. Yeoman를 사용 하 여 새 ASP.NET 핵심 앱을 스 캐 폴드 하려면 위의 지침을 따릅니다. 이 시간 선택 **웹 응용 프로그램** 응용 프로그램 이름을 확인 하 고 메뉴에서 `MyWebApp`합니다.

Visual Studio를 엽니다. 파일 메뉴에서 열기 ‣ 프로젝트/솔루션을 선택 합니다.

프로젝트 열기 대화 상자에서 이동 하는 *project.json* 파일를 선택 하 고 클릭는 **열려** 단추입니다. 솔루션 탐색기에서 프로젝트 아래 스크린샷과 같이 표시 됩니다.

![파일 및 폴더의 솔루션 탐색기에서 새 프로젝트](yeoman/_static/yeoman-solution.png)

Yeoman scaffolds MVC 웹 응용 프로그램을 모두 사용 하 여 전체 서버 및 클라이언트 쪽 빌드한 지원 합니다. 서버 쪽 종속성 아래에 나열 된는 **참조** 노드와의 클라이언트 쪽 종속성은 **종속성** 솔루션 탐색기의 노드. 프로젝트가 로드 될 때 종속성이 자동으로 복원 됩니다.

![솔루션 탐색기 트리 뷰에서 종속성 노드에서 Bower 폴더는 열려 해당 종속성을 나열 합니다.](yeoman/_static/yeoman-loading-dependencies.png)

모든 종속성을 복원할 때 키를 눌러 **F5** 프로젝트를 실행 합니다. 기본 홈 페이지가 브라우저에 표시 됩니다.

![Microsoft Edge에 열려 있는 웹 응용 프로그램](yeoman/_static/yeoman-home-page.png)

## <a name="restoring-building-and-hosting-from-a-command-line"></a>복원, 빌드 및 명령줄에서 호스팅

준비 하 고 사용 하 여 웹 응용 프로그램을 호스팅하는 [.NET Core](https://microsoft.com/net/core) 명령줄 인터페이스입니다.

명령 프롬프트에서 현재 디렉터리는 프로젝트가 포함 된 폴더를 변경 합니다 (즉, 포함 된 폴더로 *project.json* 파일):

```console
cd src\MyWebApp
```

프로젝트의 NuGet 패키지 종속성을 복원 합니다.

```console
dotnet restore
```

응용 프로그램을 실행 합니다.

```console
dotnet run
```

플랫폼 간 [Kestrel](../fundamentals/servers/kestrel.md) 웹 서버에서 5000 포트에서 수신 대기를 시작 합니다.

웹 브라우저를 열고 이동 `http://localhost:5000`합니다.

![Microsoft Edge에 열려 있는 웹 응용 프로그램](yeoman/_static/yeoman-home-page_5000.png)

## <a name="adding-to-your-project-with-sub-generators"></a>프로젝트에 추가 된 하위 생성기

프로젝트가 만들어진 후에 Yeoman를 사용 하 여 새로 생성 된 파일을 추가할 수 있습니다. 사용 하 여 [생성기 하위](https://www.github.com/omnisharp/generator-aspnet#sub-generators) 프로젝트를 구성 하는 파일 형식 중 하나를 추가 합니다. 예를 들어, 프로젝트에 새 클래스를 추가 하려면 입력에서 `yo aspnet:Class` 명령 클래스의 이름이 차례로 나옵니다. 파일 만들지 디렉터리에서 다음 명령을 실행 합니다.

```console
yo aspnet:Class Person
```

결과 Person.cs 라는 클래스 라는 파일 `Person`:

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;

namespace MyWebApp
{
    public class Person
    {
        public Person()
        {
        }
    }
}
```

## <a name="additional-resources"></a>추가 리소스

* [서버 (Kestrel 및 WebListener)](../fundamentals/servers/index.md)

* [Visual Studio 코드를 사용 하 여 Mac에서 첫 번째 ASP.NET 핵심 응용 프로그램](../tutorials/your-first-mac-aspnet.md)

* [기본 사항](../fundamentals/index.md)

