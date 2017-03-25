---
title: "ASP.NET Core에서 브라우저 링크 | Microsoft 문서"
author: ncarandini
description: "하나 이상의 웹 브라우저를 사용 하 여 개발 환경에 연결 하는 Visual Studio 기능"
keywords: "ASP.NET Core, 브라우저 링크 CSS 동기화"
ms.author: riande
manager: wpickett
ms.date: 12/28/2016
ms.topic: article
ms.assetid: 11813d4c-3f8a-445a-b23b-e4a57d001abc
ms.technology: aspnet
ms.prod: asp.net-core
uid: client-side/using-browserlink
ms.custom: H1Hack27Feb2017
translationtype: Machine Translation
ms.sourcegitcommit: 010b730d2716f9f536fef889bc2f767afb648ef4
ms.openlocfilehash: 1f9495bb4d32308255f6d6211404e20b6251501e
ms.lasthandoff: 03/23/2017

---
# <a name="introduction-to-browser-link-in-aspnet-core"></a>ASP.NET Core에서 브라우저 링크 소개 

여 [Nicolò Carandini](https://github.com/ncarandini), [Mike Wasson](https://github.com/MikeWasson), 및 [Tom Dykstra](https://github.com/tdykstra)

브라우저 링크는 개발 환경 및 하나 이상의 웹 브라우저 간의 통신 채널을 만들어 Visual Studio의 기능입니다. 브라우저 간 테스트에 유용한 여러 브라우저에서 웹 응용 프로그램 한 번에 새로 고치려면 브라우저 링크를 사용할 수 있습니다.

## <a name="browser-link-setup"></a>브라우저 링크 설치

ASP.NET Core **웹 응용 프로그램** 프로젝트 템플릿이 Visual Studio 2015에 포함 하 고 나중에 브라우저 링크에 필요한 모든 항목을 포함 합니다.

브라우저 링크 ASP.NET 코어를 사용 하 여 만든 프로젝트를 추가 하려면 **빈** 또는 **웹 API** 서식 파일을 다음이 단계를 수행 합니다.

1. 추가 된 *Microsoft.VisualStudio.Web.BrowserLink.Loader* 패키지 
2. 구성 코드에 추가 된 *Startup.cs* 파일입니다.

### <a name="add-the-package"></a>패키지 추가

이 기능은 Visual Studio 패키지를 추가 하는 가장 쉬운 방법은 이므로를 열려면는 **패키지 관리자 콘솔** (**보기 > 다른 창 > 패키지 관리자 콘솔**) 다음 명령을 실행 합니다.

```console
install-package Microsoft.VisualStudio.Web.BrowserLink.Loader
```

사용할 수 있습니다 **NuGet 패키지 관리자**합니다.  프로젝트 이름을 마우스 오른쪽 단추로 클릭 **솔루션 탐색기**, 선택한 **NuGet 패키지 관리**합니다. 

![열기 NuGet 패키지 관리자](using-browserlink/_static/open-nuget-package-manager.png)

다음 찾기 및 패키지를 설치 합니다.

![NuGet 패키지 관리자를 사용 하 여 패키지를 추가 합니다.](using-browserlink/_static/add-package-with-nuget-package-manager.png)

### <a name="add-configuration-code"></a>구성 코드를 추가 합니다.

열기는 *Startup.cs* 파일 및는 `Configure` 메서드에 다음 코드를 추가 합니다.

```csharp
app.UseBrowserLink();
```

내 코드는 일반적으로 `if` 다음과 같이 개발 환경에만 브라우저 링크 수 있는 블록:

[!code-csharp[주](./using-browserlink/sample/BrowserlinkSample/src/BrowserlinkSample/Startup.cs?highlight=1,4&range=40-44)]

자세한 내용은 참조 [여러 환경 작업](../fundamentals/environments.md)합니다.

## <a name="how-to-use-browser-link"></a>브라우저 링크를 사용 하는 방법

ASP.NET Core 프로젝트가 열려 있으면 Visual Studio를 보여 줍니다 브라우저 링크 도구 모음 컨트롤 옆에 **디버그 대상** 도구 모음 컨트롤:

![브라우저 링크 드롭다운 메뉴](using-browserlink/_static/browserLink-dropdown-menu.png)

브라우저 링크 도구 모음 컨트롤에서 다음을 수행할 수 있습니다.

- 여러 브라우저에서 웹 응용 프로그램 동시 새로 고침
- 열기는 **브라우저 링크 대시보드**
- 또는 사용 안 함 **브라우저 링크**
- CSS 자동 동기화를 사용 하지 않도록 설정 하거나 사용

> [!NOTE]
> 일부 Visual Studio 플러그 인, 특히 *웹 확장 팩 2015* 및 *웹 확장 팩 2017*, 브라우저 링크에 대 한 확장 된 기능을 제공 하지만 추가 기능의 일부 ASP.NET 핵심 프로젝트와 함께 작동 하지 않습니다.

## <a name="refresh-the-web-application-in-several-browsers-at-once"></a>여러 브라우저에서 웹 응용 프로그램 동시 새로 고침

드롭다운 메뉴를 사용 하 여 프로젝트를 시작할 때 실행 하는 단일 웹 브라우저를 선택 하려면는 **디버그 대상** 도구 모음 컨트롤:

![F&5;를 눌러 드롭다운 메뉴](using-browserlink/_static/debug-target-dropdown-menu.png)

한 번에 여러 개의 브라우저를 열려면 선택 **브라우저 선택...** 동일한 드롭다운 목록에서.  원하는 브라우저를 선택 하려면 CTRL 키를 누른 채 클릭 한 다음 **찾아보기**:

![한 번에 여러 브라우저를 열으십시오](using-browserlink/_static/open-many-browsers-at-once.png)

인덱스 보기가 포함 된 Visual Studio를 열고 보여 주는 샘플 스크린 샷 및 열린 브라우저는 다음과 같습니다.

![브라우저는 예제와 동기화](using-browserlink/_static/sync-with-two-browsers-example.png)

프로젝트에 연결 된 브라우저를 보려면 브라우저 링크 도구 모음 컨트롤 위로 마우스를 이동 합니다.

![호버 팁](using-browserlink/_static/hoover-tip.png)

인덱스 뷰를 변경 하 고 연결 된 모든 브라우저는 브라우저 링크 새로 고침 단추를 클릭할 때 업데이트 됩니다.

![변경 하려면 브라우저 동기화](using-browserlink/_static/browsers-sync-to-changes.png)

브라우저 링크 Visual Studio 외부에서 시작 하 고 응용 프로그램 URL로 이동 하는 브라우저 에서도 작동 합니다.

### <a name="the-browser-link-dashboard"></a>브라우저 링크 대시보드

브라우저 링크에 대 한 드롭다운 메뉴를 열고 브라우저와의 연결을 관리 하에서 브라우저 링크 대시보드를 엽니다.

![오픈 browserslink 대시보드](using-browserlink/_static/open-browserlink-dashboard.png)

클릭 하면 비 디버깅 세션 시작할 수 없는 브라우저 연결 되어 있는 경우는 _브라우저에서 보기_ 링크:

![browserlink 대시보드-아니요 연결](using-browserlink/_static/browserlink-dashboard-no-connections.png)

그렇지 않으면 각 브라우저에 표시 되는 페이지에 대 한 경로와 연결 된 브라우저 표시 됩니다.

![browserlink 대시보드-2 연결](using-browserlink/_static/browserlink-dashboard-two-connections.png)

원하는 경우 해당 단일 브라우저를 새로 고치려면 나열 된 브라우저 이름을 클릭할 수 있습니다.

### <a name="enable-or-disable-browser-link"></a>브라우저 링크를 사용 하지 않도록 설정 하거나 사용

기능을 비활성화 한 후 브라우저 링크를 다시 활성화 하면 다시 연결 하는 브라우저를 새로 고쳐야 할 수 있습니다.

### <a name="enable-or-disable-css-auto-sync"></a>CSS 자동 동기화를 사용 하지 않도록 설정 하거나 사용

CSS 자동 동기화를 사용 하는 경우 연결 된 브라우저는 자동으로 새로 고쳐집니다 CSS 파일을 변경 하는 경우.

## <a name="how-does-it-work"></a>작동 방식

브라우저 링크 SignalR을 사용 하 여 Visual Studio와 브라우저 간의 통신 채널을 만듭니다. 브라우저 링크 사용 하는 경우 Visual Studio는 여러 클라이언트 (브라우저)에 연결할 수 있는 SignalR 서버로 작동 합니다. 브라우저 링크는 또한 ASP.NET 요청 파이프라인에서 미들웨어 구성 요소를 등록합니다. 이 구성 요소는 특별 한 삽입 `<script>` 서버에서 모든 페이지 요청에 대 한 참조입니다. 선택 하 여 스크립트 참조를 볼 수 있습니다 **소스 보기** 브라우저 및 끝으로 스크롤 하는 `<body>` 콘텐츠에 태그:

```javascript
    <!-- Visual Studio Browser Link -->
    <script type="application/json" id="__browserLink_initializationData">
        {"requestId":"a717d5a07c1741949a7cefd6fa2bad08","requestMappingFromServer":false}
    </script>
    <script type="text/javascript" src="http://localhost:54139/b6e36e429d034f578ebccd6a79bf19bf/browserLink" async="async"></script>
    <!-- End Browser Link -->
</body>
```

소스 파일을 수정 되지 않습니다. 미들웨어 구성 요소는 스크립트 참조를 동적으로 삽입합니다. 

브라우저 쪽 코드에는 모든 JavaScript는 이기 때문에 SignalR을 지 원하는 모든 브라우저 플러그 인을 요구 하지 않고 모든 브라우저에서 작동 합니다.

