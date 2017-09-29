---
title: "ASP.NET Core에서 브라우저 링크"
author: ncarandini
description: "어떻게 브라우저 링크는 하나 이상의 웹 브라우저와 함께 개발 환경에 연결 하는 Visual Studio 기능에 알아봅니다."
keywords: "ASP.NET Core, 브라우저 링크 CSS 동기화"
ms.author: riande
manager: wpickett
ms.date: 09/22/2017
ms.topic: article
ms.assetid: 11813d4c-3f8a-445a-b23b-e4a57d001abc
ms.technology: aspnet
ms.prod: asp.net-core
uid: client-side/using-browserlink
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 67ddc58e38962bd876050739a2a1447be4f589bb
ms.sourcegitcommit: 6e83c55eb0450a3073ef2b95fa5f5bcb20dbbf89
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/28/2017
---
# <a name="browser-link-in-aspnet-core"></a>ASP.NET Core에서 브라우저 링크 

여 [Nicolò Carandini](https://github.com/ncarandini), [Mike Wasson](https://github.com/MikeWasson), 및 [Tom Dykstra](https://github.com/tdykstra)

브라우저 링크는 Visual studio 개발 환경 및 하나 이상의 웹 브라우저 간에 통신 채널을 만드는 기능입니다. 다중 브라우저 테스트에 대 한 유용한 여러 브라우저에서 웹 응용 프로그램 한 번에 새로 고치려면 브라우저 링크를 사용할 수 있습니다.

## <a name="browser-link-setup"></a>브라우저 링크 설치

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

ASP.NET Core 2.x **웹 응용 프로그램**, **빈**, 및 **웹 API** 템플릿을 사용 하 여 프로젝트의 [Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All/) 메타 패키지에 대 한 패키지 참조가 포함 된 [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/)합니다. 따라서 사용 하는 `Microsoft.AspNetCore.All` 메타 패키지 브라우저 링크를 사용 하기 위해 사용할 수 있도록 하려면 추가 작업이 없으므로 필요 합니다.

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

ASP.NET Core 1.x **웹 응용 프로그램** 프로젝트 템플릿에에 대 한 패키지 참조는 [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) 패키지 합니다. **빈** 또는 **웹 API** 서식 파일 프로젝트에 패키지 참조를 추가 해야 `Microsoft.VisualStudio.Web.BrowserLink`합니다.

이 Visual Studio 기능을 가장 쉬운 방법은 패키지를 추가 하는 **빈** 또는 **웹 API** 서식 파일 프로젝트를 여는 것은 **패키지 관리자 콘솔** (**보기** > **다른 창** > **패키지 관리자 콘솔**) 다음 명령을 실행 합니다.

```console
install-package Microsoft.VisualStudio.Web.BrowserLink
```

사용할 수 있습니다 **NuGet 패키지 관리자**합니다. 프로젝트 이름을 마우스 오른쪽 단추로 클릭 **솔루션 탐색기** 선택 **NuGet 패키지 관리**:

![열기 NuGet 패키지 관리자](using-browserlink/_static/open-nuget-package-manager.png)

찾기 및 패키지를 설치 합니다.

![NuGet 패키지 관리자를 사용 하 여 패키지를 추가 합니다.](using-browserlink/_static/add-package-with-nuget-package-manager.png)

---

### <a name="configuration"></a>구성

에 `Configure` 의 메서드는 *Startup.cs* 파일:

```csharp
app.UseBrowserLink();
```

내 코드는 일반적으로 `if` 아래 그림과 같이 개발 환경에서 브라우저 링크의 사용 여부만 블록:

```csharp
if (env.IsDevelopment())
{
    app.UseDeveloperExceptionPage();
    app.UseBrowserLink();
}
```

자세한 내용은 [여러 환경 사용](xref:fundamentals/environments)을 참조하세요.

## <a name="how-to-use-browser-link"></a>브라우저 링크를 사용 하는 방법

Visual Studio 브라우저 링크 도구 모음 컨트롤 옆에 표시 ASP.NET Core 프로젝트가 열려 있는 경우는 **디버그 대상** 도구 모음 컨트롤:

![브라우저 링크 드롭 다운 메뉴](using-browserlink/_static/browserLink-dropdown-menu.png)

브라우저 링크 도구 모음 컨트롤에서 다음을 수행할 수 있습니다.

* 여러 브라우저에서 웹 응용 프로그램을 한 번에 새로 고칩니다.
* 열기는 **브라우저 링크 대시보드에**합니다.
* 또는 사용 안 함 **브라우저 링크**합니다. 참고: 브라우저 링크는 Visual Studio 2017 (15.3)에서 기본적으로 비활성화 됩니다.
* CSS 자동 동기화를 사용 하지 않도록 설정 하거나 사용 합니다.

> [!NOTE]
> 일부 Visual Studio 플러그 인, 특히 *웹 확장 팩 2015* 및 *웹 확장 팩 2017*, 브라우저 링크에 대 한 확장 된 기능을 제공 하지만 추가 기능 중 일부는 asp 작동 하지 않습니다. NET 핵심 프로젝트입니다.

## <a name="refresh-the-web-application-in-several-browsers-at-once"></a>여러 브라우저에서 웹 응용 프로그램 한 번에 새로 고침

에 있는 드롭 다운 메뉴를 사용 하 여 프로젝트를 시작할 때 시작 하려면 단일 웹 브라우저를 선택 하 고 **디버그 대상** 도구 모음 컨트롤:

![F 5를 눌러 드롭다운 메뉴](using-browserlink/_static/debug-target-dropdown-menu.png)

한 번에 여러 브라우저를 열려면 선택 **브라우저 선택...**  동일한 드롭다운 목록에서 합니다. 원하는 브라우저를 선택 하려면 CTRL 키를 누른 채 클릭 **찾아보기**:

![한 번에 다양 한 브라우저를 열으십시오](using-browserlink/_static/open-many-browsers-at-once.png)

인덱스 보기가 표시 된 Visual Studio를 열고 보여 주는 스크린샷 및 두 개의 열린 브라우저 다음과 같습니다.

![브라우저는 예제와 동기화](using-browserlink/_static/sync-with-two-browsers-example.png)

프로젝트에 연결 된 브라우저를 보려면 브라우저 링크 도구 모음 컨트롤을 마우스로:

![Hover 팁](using-browserlink/_static/hoover-tip.png)

인덱스 뷰를 변경 하 고 연결 된 모든 브라우저 브라우저 링크 새로 고침 단추를 클릭할 때 업데이트 됩니다.

![변경 하는 브라우저 동기화](using-browserlink/_static/browsers-sync-to-changes.png)

브라우저 링크는 Visual Studio 외부에서 시작 하 고 응용 프로그램 URL로 이동 하는 브라우저 에서도 작동 합니다.

### <a name="the-browser-link-dashboard"></a>브라우저 링크 대시보드

브라우저 링크에 대 한 드롭다운 메뉴를 열고 브라우저와 연결을 관리에서 브라우저 링크 대시보드를 엽니다.

![오픈 browserslink 대시보드](using-browserlink/_static/open-browserlink-dashboard.png)

브라우저가 없으므로 연결 되어 있는 경우 선택 하 여 비-디버깅 세션을 시작할 수 있습니다는 *브라우저에서 보기* 링크:

![browserlink 대시보드-no 연결](using-browserlink/_static/browserlink-dashboard-no-connections.png)

그렇지 않으면 연결 된 브라우저와 각 브라우저 표시 하는 페이지의 경로를 표시 됩니다.

![browserlink 대시보드-2 연결](using-browserlink/_static/browserlink-dashboard-two-connections.png)

원하는 경우 해당 단일 브라우저를 새로 고치려면 나열 된 브라우저 이름을 클릭할 수 있습니다.

### <a name="enable-or-disable-browser-link"></a>브라우저 링크를 사용할지 설정 합니다.

사용 중지 한 후 브라우저 링크를 다시 활성화 하면 브라우저에 다시 연결을 새로 고쳐야 합니다.

### <a name="enable-or-disable-css-auto-sync"></a>CSS 자동 동기화를 사용할지 설정 합니다.

CSS 자동 동기화를 사용 하는 CSS 파일에 변경 내용을 확인 하는 경우 자동으로 연결 된 브라우저가 새로 고쳐지지 합니다.

## <a name="how-does-it-work"></a>작동 방식

브라우저 링크 SignalR를 사용 하 여 Visual Studio 및 브라우저 간 통신 채널을 만듭니다. 브라우저 링크를 사용 하는 Visual Studio는 여러 명의 클라이언트 (브라우저)에 연결할 수 있는 SignalR 서버로 작동 합니다. 또한 브라우저 링크는 ASP.NET 요청 파이프라인에 미들웨어 구성 요소를 등록합니다. 이 구성 요소를 특수 삽입 `<script>` 서버에서 모든 페이지 요청에 대 한 참조입니다. 선택 하 여 스크립트 참조를 볼 수 **소스 보기** 브라우저와의 끝으로 스크롤하면에 `<body>` 콘텐츠에 태그:

```javascript
    <!-- Visual Studio Browser Link -->
    <script type="application/json" id="__browserLink_initializationData">
        {"requestId":"a717d5a07c1741949a7cefd6fa2bad08","requestMappingFromServer":false}
    </script>
    <script type="text/javascript" src="http://localhost:54139/b6e36e429d034f578ebccd6a79bf19bf/browserLink" async="async"></script>
    <!-- End Browser Link -->
</body>
```

소스 파일이 수정 되지 않습니다. 미들웨어 구성 요소는 스크립트 참조를 동적으로 삽입합니다. 

브라우저 쪽 코드는 모든 JavaScript 이기 때문에 브라우저 플러그 인을 없이 SignalR를 지 원하는 모든 브라우저에서 작동 합니다.
