---
title: "부트스트랩을 사용한 beautiful 및 응답성이 뛰어난 사이트 구축"
author: ardalis
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: bd27832c-2877-4b7b-9337-e009361d845f
ms.technology: aspnet
ms.prod: asp.net-core
uid: client-side/bootstrap
ms.openlocfilehash: fc00bcce24d586865bf6a1d3f2f7e782a2f1c703
ms.sourcegitcommit: 9cdbfd0d670d70b9c354216aabee260c52dad5ee
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/12/2017
---
# <a name="building-beautiful-responsive-sites-with-bootstrap"></a>부트스트랩을 사용한 beautiful 및 응답성이 뛰어난 사이트 구축

<a name=bootstrap-index></a>

으로 [Steve Smith](https://ardalis.com/)

부트스트랩 현재 응답성이 뛰어난 웹 응용 프로그램을 개발 하기 위한 가장 인기 있는 웹 프레임 워크입니다. 프런트 엔드 디자인 및 개발 또는 전문가 초보자에 관계 없이 다양 한 기능 및 웹 사이트와 사용자의 경험을 향상 시킬 수 있는 혜택을 제공 합니다. 부트스트랩은 CSS 및 JavaScript 파일의 집합으로 배포 되 고 태블릿에 데스크톱을 효율적으로 휴대폰에서 웹 사이트 또는 응용 프로그램 눈금을 도움이 되도록 설계 되었습니다.

## <a name="getting-started"></a>시작

부트스트랩을 시작 하는 방법은 여러 가지가 있습니다. Visual Studio에서 새 웹 응용 프로그램을 시작 하는 경우는 case 부트스트랩을 미리 설치 되어, ASP.NET Core에 대 한 기본 시작 템플릿을 선택할 수 있습니다.

![스타터 서식 파일 솔루션 뷰를 부트스트랩 합니다.](bootstrap/_static/bootstrap-in-starter-template.png)

ASP.NET Core를 부트스트랩 추가 프로젝트는 단순히 추가 하는 방법의 *bower.json* 종속성으로:

[!code-json[Main](../common/samples/WebApplication1/bower.json?highlight=5)]

이 부트스트랩 ASP.NET Core 프로젝트에 추가 하는 것이 좋습니다.

또한 부트스트랩 Bower, npm NuGet 등의 여러 패키지 관리자 중 하나를 사용 하 여 설치할 수 있습니다. 각각의 경우에는 프로세스는 기본적으로 동일 합니다.

### <a name="bower"></a>Bower

```console
bower install bootstrap
```

### <a name="npm"></a>npm

```console
npm install bootstrap
```

### <a name="nuget"></a>NuGet

```console
Install-Package bootstrap
```

> [!NOTE]
> ASP.NET Core에서 부트스트랩 Bower 경유 하는 것과 같은 클라이언트 쪽 종속성을 설치 하는 것이 좋습니다 (사용 하 여 *bower.json*위에 표시 된 것 처럼). Npm/NuGet을 사용 하는 다른 종류의 이전 버전의 ASP.NET 포함 하 여 웹 응용 프로그램에 추가할 수 부트스트랩 얼마나 쉽게 설명 하기 위해 표시 됩니다.

부트스트랩의 고유한 로컬 버전을 참조 하는 경우 사용 하는 모든 페이지에서이 참조 해야 합니다. 프로덕션 환경에서 부트스트랩 CDN을 사용 하 여 참조 해야 합니다. 기본 ASP.NET 사이트 서식 파일에는 *_Layout.cshtml* 파일 다음과 같이 되므로 않습니다:

[!code-html[Main](../common/samples/WebApplication1/Views/Shared/_Layout.cshtml?highlight=9,13,51,59)]

> [!NOTE]
> 부트스트랩의 jQuery 플러그 사용 하려는 경우에 jQuery 참조 해야 합니다.

## <a name="basic-templates-and-features"></a>기본 서식 파일 및 기능

가장 기본적인 부트스트랩 서식 파일은와 매우 비슷하며는 *_Layout.cshtml* 파일 표시 위, 하 고 간단 하 게 탐색 및 페이지의 나머지 부분을 렌더링 하는 곳에 대 한 기본 메뉴가 포함 됩니다.

### <a name="basic-navigation"></a>기본 탐색

기본 서식 파일의 집합을 사용 하 여 `<div>` 위쪽 탐색 모음 및 페이지의 본문을 렌더링 하는 요소입니다. HTML5를 사용 하는 경우에 첫 번째를 바꿀 수 있습니다 `<div>` 사용 하 여 태그는 `<nav>` 태그를 동일한 결과 얻을 하지만 정확한 의미 합니다.  이 첫 번째 내 `<div>` 다른 몇 가지를 볼 수 있습니다. 첫째, 한 `<div>` "container" 한 후, 두 개 내에서 클래스와 함께 `<div>` 요소: "탐색 모음 헤더" 및 "navbar 축소"입니다.  화면 특정 최소 너비를 3 가로선을 보여 주는 이하인 경우 표시 되는 단추를 포함 하는 탐색 모음 헤더 div (소위는 "햄버거 아이콘"). 아이콘은 순수 HTML과 CSS;를 사용 하 여 렌더링은 이미지가 없는 필요 합니다. 이 각각의 아이콘을 표시 하는 코드는 <span> 태그 렌더링 흰색 막대 중 하나:

```html
<button type="button" class="navbar-toggle" data-toggle="collapse" data-target=".navbar-collapse">
    <span class="icon-bar"></span>
    <span class="icon-bar"></span>
    <span class="icon-bar"></span>
</button>
```

맨 왼쪽에 표시 되는 응용 프로그램 이름을 포함 됩니다.  기본 탐색 메뉴에서 렌더링 되는 `<ul>` 내의 두 번째 div 요소 홈, 정보 및 연락처에 대 한 링크를 포함 합니다. 등록 및 로그인에 대 한 추가 링크는 29 선에 _LoginPartial 선으로 추가 됩니다. 다른 탐색, 아래 각 페이지의 본문은 렌더링 되 `<div>`"container" 및 "본문 콘텐츠" 클래스와는 별도로 표시 합니다. 여기에 표시 된 간단한 기본 _Layout 파일에서 페이지의 내용은 페이지 및 간단한와 관련 된 특정 보기에서 렌더링 `<footer>` 의 끝에 추가 되는 `<div>` 요소입니다.  페이지에 대 한 기본 제공 표시 되는 방식을 사용 하 여 확인할 수 있습니다이 템플릿에서:

![페이지 정보](bootstrap/_static/about-page-wide.png)

창의 특정 너비 아래로 떨어질 경우 오른쪽, 위에서 단추 "햄버거"로 축소 된 탐색 모음에 표시 됩니다.

![햄버거 메뉴와 페이지에 대 한](bootstrap/_static/about-page-hamburger.png)

아이콘을 클릭 하면 페이지의 맨 위에서 아래로 내려갑니다 세로 서랍에서 메뉴 항목 표시:

![햄버거 메뉴와 페이지에 대 한 확장](bootstrap/_static/about-page-hamburger-open.png)

### <a name="typography-and-links"></a>입력 체계 및 링크

부트스트랩은 사이트의 기본 입력 체계, 색 및 해당 CSS 파일에 서식 지정 하는 링크를 설정 합니다. 이 CSS 파일에 테이블, 단추, 폼 요소, 이미지, 등에 대 한 기본 스타일 ([자세한](http://getbootstrap.com/css/)). 다음 표 레이아웃 시스템 기능은 특히 유용 합니다.

### <a name="grids"></a>표

부트스트랩의 가장 인기 있는 기능 중 하나는 모눈 레이아웃 시스템입니다. 최신 웹 응용 프로그램 사용 안는 `<table>` 태그 레이아웃의 경우 대신 실제 테이블 형식 데이터에이 요소의 사용을 제한 합니다. 대신 열과 행 레이아웃할 수는 일련의를 사용 하 여 `<div>` 요소 및 해당 CSS 클래스입니다. 세로로에 표시할 좁은 화면와 같은 휴대폰 격자 레이아웃을 조정 하는 기능을 포함 하 여이 방식의 장점은 다음과 같습니다.

[부트스트랩의 모눈 레이아웃 시스템](http://getbootstrap.com/css/#grid) 는 12 개의 열을 기반으로 합니다. 이 번호는 1, 2, 3 또는 4 개의 열,으로 균등 하 게 나눌 수 있습니다 및 열 너비는 1 내에 다 수 있습니다 때문에 선택 된 화면의 세로 너비가 12 일/합니다. 격자 레이아웃 시스템을 사용 하 여을 시작 하려면 먼저 검토 해야 컨테이너 `<div>` 한 행을 추가 하 고 `<div>`여기 표시 된 것 처럼:

```html
<div class="container">
    <div class="row">
        ...
    </div>
</div>
```

다음으로 더 추가 `<div>` 각 열에 대 한 요소 하 고 열 수를 지정 하는 `<div>` "col-md-"로 시작 하는 CSS 클래스의 일부로 (12) 중 차지 하 합니다. 예를 들어, 단순히 동일한 크기의 두 열이 포함 하려는 각각에 대 한 "col md 6"의 클래스를 사용 합니다. 이 경우 "md"는 짧은 "보통"과 표준 크기의 데스크톱 컴퓨터 디스플레이 크기를 나타냅니다. 옵션에는 네에서 선택할 수 있으며 각각에 사용할 더 높은 너비 (이므로 레이아웃 화면 너비에 관계 없이 수정 하려는 경우에 xs 클래스를 지정할 수 있습니다) 재정의 되지 않는 경우.

CSS 클래스 접두사 | 장치 계층 | 너비
:---: | :---: | :---:
열-xs- | 휴대폰 | < 768px
열-sm- | 태블릿 | > = 768px
열-md- | 데스크톱 | > = 992px
열-lg- | 더 큰 데스크톱 표시 | > = 1200px

두 열 모두 "col-md-6" 결과 레이아웃 데스크톱 해상도에서 두 개의 열 수 있지만 되어 소형 장치 (또는 데스크톱에 보다 좁은 브라우저 창), 사용자가을 쉽게 볼 수 있도록에 렌더링 될 때이 두 열 컨트롤이 세로로 쌓입니다 됩니다 지정 하는 경우 콘텐츠를 가로로 스크롤할 필요 없이입니다.

부트스트랩은 기본적으로 항상 단일 열 레이아웃을 둘 이상의 열을 사용할 때는 열을 지정 해야 합니다. 에 명시적으로 지정 하는 원하는 `<div>` 모든 12 열 차지 더 큰 장치 계층의 동작을 재정의 하는 것입니다. 여러 장치 계층 클래스를 지정할 때는 특정 지점에서 열 렌더링을 다시 설정 해야 합니다. 특정 뷰포트 내부 표시만 되는 clearfix div를 추가 합니다.이 수행할 수, 다음과 같이 합니다.

![좁은 문자열과 와이드 뷰포트 표](bootstrap/_static/narrow-and-wide-viewport-grid.png)

위의 예제에서는 1, 2 공유 "md" 레이아웃에 행 2와 3 공유 "xs" 레이아웃에 행입니다. clearfix 없이 `<div>`, 2와 3 제대로 표시 되지 않는 "xs" 보기 (하나에만, 4 및 5 개 표시 되는 참고)에서:

![clearfix 사용 하지 않고 표](bootstrap/_static/grid-without-clearfix.png)

이 예제에서는 단일 행만 `<div>` 사용 된 부트스트랩 된 상태는 주로 레이아웃 및 탄력적인 열에 대해 올바른 작업 않았습니다. 행을 지정 해야 하는 일반적으로 `<div>` 가로 각 행에 대 한 레이아웃을 차지 하며 물론 내부에 다른 부트스트랩 표를 중첩할 수 있습니다. 이렇게 하면 각 중첩 된 표 열 클래스를 사용 하 여 다음으로 세분화할 수 있습니다 하 배치 되는 요소 너비의 100%를 차지 합니다.

### <a name="jumbotron"></a>Jumbotron

Visual Studio 2012 또는 2013에서 기본 ASP.NET MVC 템플릿을 사용한 경우 실행에서 Jumbotron 보았을 것입니다. 쿼리, rotator, 쿼리, 유사한 요소에 대 한 호출 큰 배경 이미지를 표시 하는 데 사용할 수 있는 페이지의 큰 전자 섹션을 나타냅니다. jumbotron에 페이지를 추가 하려면 추가 `<div>` "jumbotron"의 클래스를 지정 하 고 다음에 컨테이너를 배치 `<div>` 안에 콘텐츠를 추가 합니다.  주 제목을 표시에 대 한 한 jumbotron를 사용 하는 페이지에 대 한 표준 사용자가 쉽게 조정할 수 있습니다.:

![jumbotron 예제](bootstrap/_static/jumbotron.png)

### <a name="buttons"></a>단추

기본 단추 클래스 및 해당 색은 아래 그림에 표시 됩니다.

![테마가 지정 된 단추](bootstrap/_static/theme-buttons.png)

### <a name="badges"></a>배지

배지 탐색 항목 옆에 있는 작은, 일반적으로 숫자로 이루어지며 설명선을 참조 하십시오. 다양 한 메시지 또는 알림, 대기 또는 업데이트의 존재 여부를 나타낼 있습니다. 이러한 배지를 지정 하는 작업은 추가 하는 것을 <span> "배지"의 클래스와 텍스트가 포함 된:

![테마가 지정 된 배지](bootstrap/_static/theme-badges.png)

### <a name="alerts"></a>경고

응용 프로그램의 사용자에 게 일종의 알림, 경고 또는 오류 메시지를 표시 해야 합니다. 즉, 표준 경고 클래스는 유용 합니다.  연결 된 색 구성표를 4 개의 다른 심각도 수준이 있습니다.

![테마가 지정 된 경고](bootstrap/_static/theme-alerts.png)

### <a name="navbars-and-menus"></a>탐색 모음과 메뉴

표준 navbar 우리의 레이아웃에 이미 포함 되어 있지만 부트스트랩 테마 스타일을 추가 옵션을 지원 합니다. 도 쉽게 가로로 있는 기본 설정에 같이 추가 하위 탐색 항목을 팝업 창 메뉴에 아니라 세로로 탐색 모음을 표시 하도록 선택할 수 있습니다. 탭 스트립와 같은 간단한 탐색 메뉴의 맨 위에 빌드됩니다. <ul> 요소입니다. 이 방금 CSS 클래스 "이동" 및 "탭으로 이동" 하 여 매우 간단 하 게 만들 수 있습니다.

![테마가 지정 된 탭](bootstrap/_static/theme-tabstrips.png)

탐색 모음 마찬가지로, 되지만 좀 더 복잡 합니다.  로 시작는 `<nav>` 또는 `<div>` 컨테이너 div는 나머지 요소가 고 "navbar"의 클래스와 함께 합니다. 가격 페이지 헤더에 있는 이미는 탐색 모음을 포함 – 지켜볼 단순히 확장 됩니다.이, 드롭다운 메뉴에 대 한 지원을 추가 합니다.

![테마가 지정 된 탐색 모음](bootstrap/_static/theme-navbars.png)

### <a name="additional-elements"></a>추가 요소

스트라이프 뷰에 대 한 지원을 비롯 하 여 올바른 형식의 스타일의 HTML 테이블을 표시 하는 기본 테마를 사용할 수도 수 있습니다. 단추와 유사한 스타일으로 레이블이 있습니다. 표준 HTML 외 추가 스타일 옵션을 지 원하는 사용자 지정 드롭다운 메뉴를 만들 수 있습니다 `<select>` 우리의 기본 시작 사이트에서 이미 사용 하는 것과 같은 탐색 모음과 함께 요소입니다. 진행률 표시줄을 해야 하는 경우 몇 가지 스타일에 제목 및 콘텐츠를 포함 하는 패널 및 그룹 목록을 선택할 수 있습니다.  표준 부트스트랩 테마를 여기에서 추가 옵션을 탐색 합니다.

[http://getbootstrap.com/examples/theme/](http://getbootstrap.com/examples/theme/)

## <a name="more-themes"></a>추가 테마

해당 CSS의 일부 또는 전부를 재정의 하 여 색 및 스타일을 사용자 고유의 응용 프로그램의 요구 사항에 맞게 조정 표준 부트스트랩 테마를 확장할 수 있습니다. 즉시 사용 가능한 테마에서 시작 하려는 경우 몇 가지 테마 갤러리 WrapBootstrap.com (들어 있는 다양 한 상업적 테마) 및 (제공 하는 사용 가능한 테마) Bootswatch.com 같은 부트스트랩 테마, 온라인 전문를 사용할 수 있습니다.  일부 유료 서식 파일 사용 가능한 다양 한 차트 및 계기와 많은 기능을 관리 메뉴 및 대시보드에 대 한 다양 한 지원 같은 기본 부트스트랩 테마를 제공합니다. 인기 유료 서식 파일의 예는 Inspinia, AngularJS 및 정적 HTML 버전 외에 ASP.NET MVC5 서식 파일을 포함 하는 $18에 대 한 판매에 대 한 현재입니다. 샘플 스크린 샷은 다음과 같습니다.

![테마 inspinia 예제](bootstrap/_static/theme-inspinia.png)

부트스트랩 테마 변경 하려는 경우 배치의 *bootstrap.css* 에서 원하는 테마에 대 한 파일의 **wwwroot/css** 폴더에서 참조를 변경 하 고 *_Layout.cshtml* 가리킵니다.  모든 환경에 대 한 링크를 변경 합니다.

```html
<environment names="Development">
    <link rel="stylesheet" href="~/css/bootstrap.css" />
```

```html
<environment names="Staging,Production">
    <link rel="stylesheet" href="~/css/bootstrap.min.css" />
```

사용자 고유의 대시보드 작성 하려는 경우에서 시작할 수 있습니다 사용할 수 있는 무료 예제의 여기: [http://getbootstrap.com/examples/dashboard/](http://getbootstrap.com/examples/dashboard/)합니다.

## <a name="components"></a>구성 요소

앞에서 설명한 이러한 요소 외에도 부트스트랩은 다양 한 지원 [UI 구성 요소를 기본 제공](http://getbootstrap.com/components/)합니다.

### <a name="glyphicons"></a>Glyphicons

부트스트랩 Glyphicons에서 아이콘 집합에 포함 됩니다 ([http://glyphicons.com](http://glyphicons.com)), 부트스트랩 사용 웹 응용 프로그램 내에서 사용 하기 위해 무료로 사용할 수는 200 개가 넘는 아이콘을 표시 합니다. 방금 일부 예제는 다음과 같습니다.

![Glyphicons](bootstrap/_static/theme-glyphicons.png)

### <a name="input-groups"></a>입력된 그룹

직관적인 환경 사용 하 여 사용자를 제공 하는 추가 텍스트 또는 입력된 요소를 사용 하는 단추의 번들 입력된 그룹을 사용:

![입력된 그룹](bootstrap/_static/input-groups.png)

### <a name="breadcrumbs"></a>이동 경로

이동 경로 해당 최근 기록 또는 깊이 사이트의 탐색 계층 구조 내에서 사용자를 표시 하는 데 공통 UI 구성 요소입니다. "이동" 클래스를 적용 하 여 쉽게 추가 `<ol>` 목록 요소입니다. 에 "페이지 매김" 클래스를 사용 하 여 페이지 매김에 대 한 기본 제공 지원을 포함 한 `<ul>` 내의 요소는 `<nav>`합니다. 포함 된 응답 슬라이드 쇼 및 비디오를 사용 하 여 추가 `<iframe>`, `<embed>`, `<video>`, 또는 `<object>` 부트스트랩은 자동으로 스타일 지정 하는 요소입니다. "포함-응답-16by9"와 같은 특정 클래스를 사용 하 여 특정 가로 세로 비율을 지정 합니다.

## <a name="javascript-support"></a>JavaScript가 지원

부트스트랩의 JavaScript 라이브러리에는 응용 프로그램에서 프로그래밍 방식으로 동작을 제어 하는 포함 된 구성 요소에 대 한 API 지원을 포함 합니다. 또한 *bootstrap.js* 수십 개의 포함 전환, 모달 대화 상자와 같은 추가 기능을 제공 하는 사용자 지정 jQuery 플러그 인 (스타일 문서에 사용자 스크롤 위치에 따라 업데이트)를 감지, 스크롤 화면 밖 스크롤되지 있도록 창에 동작과 컨베이어 벨트, 추가 메뉴를 축소 합니다. 부트스트랩 – 방문 하십시오 자세히 알아보려면에 기본 제공 JavaScript 추가 기능의 모든 처리 하려면 충분 한 공간이 없을 [http://getbootstrap.com/javascript/](http://getbootstrap.com/javascript/)합니다.

## <a name="summary"></a>요약

부트스트랩 신속 하 고 생산성을 배치 하 고 다양 한 웹 사이트 및 응용 프로그램 스타일 지정에 사용할 수 있는 웹 프레임 워크를 제공 합니다. 기본 입력 체계 및 스타일 수동 하거나 상업적으로 구입할 수 있는 사용자 지정 테마 지원을 통해 쉽게 조작할 수 있는 한 쾌적 한 모양 및 느낌을 제공 합니다. 다양 한 웹 구성 요소 이전에 비용이 많이 드는 타사 컨트롤 현대적이 고 열린 웹 표준을 지 원하는 동안 수행 하기 위해이 요구를 지원 합니다.
