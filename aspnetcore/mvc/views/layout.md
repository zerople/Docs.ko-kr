---
title: "레이아웃 | Microsoft 문서"
author: ardalis
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 29f12d1f-9734-48bd-bf1a-cee53a8ab700
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/views/layout
translationtype: Machine Translation
ms.sourcegitcommit: 010b730d2716f9f536fef889bc2f767afb648ef4
ms.openlocfilehash: 861c2dfec00e1192dee49e85a0b94e9e68b075ba
ms.lasthandoff: 03/23/2017

---
# <a name="layout"></a>레이아웃

[Steve Smith](http://ardalis.com)

뷰는 자주 시각적 및 프로그래밍 방식으로 요소를 공유합니다. 이 문서에서는 일반적인 레이아웃을 사용 하 여 지시문, 공유, ASP.NET 응용 프로그램에서 뷰를 렌더링 하기 전에 일반적인 코드를 실행 하는 방법을 배웁니다.

## <a name="what-is-a-layout"></a>레이아웃은 무엇입니까

대부분의 웹 앱은 페이지를 탐색 하면서 사용자 일관 된 환경을 제공 하는 일반적인 레이아웃이 있습니다. 레이아웃에는 일반적으로 응용 프로그램, 탐색 또는 메뉴 요소 머리글과 바닥글 등 일반적인 사용자 인터페이스 요소에 포함 됩니다.

![페이지 레이아웃 예제](layout/_static/page-layout.png)

응용 프로그램 내에서 여러 페이지에서 스크립트 및 스타일 시트와 같은 일반적인 HTML 구조도 자주 사용 합니다. 이러한 모든 공유 요소 정의 되어 있을 수는 *레이아웃* 파일을 응용 프로그램 내에서 사용 되는 모든 보기에서 참조할 수 있습니다. 레이아웃 뷰에서 든 중복 코드를 따라 줄이기는 [안 함 반복 직접 (드라이) 원칙](http://deviq.com/don-t-repeat-yourself/)합니다.

ASP.NET 응용 프로그램에 대 한 기본 자판 배열의 이름은 규칙에 따라 `_Layout.cshtml`합니다. Visual Studio ASP.NET 핵심 MVC 프로젝트 템플릿에서이 레이아웃 파일에 포함 된 `Views/Shared` 폴더:

![솔루션 탐색기에서 views 폴더](layout/_static/web-project-views.png)

이 레이아웃 앱에서 보기에 대 한 최상위 수준 템플릿을 정의합니다. 앱 레이아웃을 필요 없습니다 및 앱 다른 레이아웃을 지정 하는 다른 뷰를 둘 이상의 레이아웃을 정의할 수 있습니다.

예로 `_Layout.cshtml`:

[!code-html[주](../../common/samples/WebApplication1/Views/Shared/_Layout.cshtml?highlight=42,66)]

## <a name="specifying-a-layout"></a>레이아웃을 지정합니다.

Razor 보기에 `Layout` 속성입니다. 이 속성을 설정 하 여 레이아웃을 지정 하는 개별 뷰:

[!code-html[주](../../common/samples/WebApplication1/Views/_ViewStart.cshtml?highlight=2)]

지정한 레이아웃 전체 경로 사용할 수 있습니다 (예: `/Views/Shared/_Layout.cshtml`) 또는 이름의 일부 (예: `_Layout`). 이름의 일부를 제공 하는 Razor 뷰 엔진 레이아웃 파일은 표준 검색 프로세스를 사용 하 여 검색 됩니다. 컨트롤러 관련 폴더를 먼저 검색, 이어서는 `Shared` 폴더입니다. 이러한 검색 프로세스는 검색에 사용 되는 것과 동일 [부분 뷰](partial.md)합니다.

기본적으로 모든 레이아웃 호출 해야 `RenderBody`합니다. 모든 곳에 대 한 호출 `RenderBody` 은 배치 하는 보기의 내용을 렌더링 됩니다.

<a name=layout-sections-label></a>

### <a name="sections"></a>섹션

하나 이상의 선택적으로 레이아웃을 참조할 수 *섹션*를 호출 하 여 `RenderSection`합니다. 섹션에는 특정 페이지 요소를 배치할 위치를 구성 하는 방법을 제공 합니다. 호출할 때마다 `RenderSection` 해당 섹션의 필수 또는 선택 여부를 지정할 수 있습니다. 필요한 부분이 없으면 예외가 throw 됩니다. 사용 하 여 섹션 내에서 렌더링할 콘텐츠를 지정 하는 개별 뷰는 `@section` Razor 구문입니다. 보기 섹션에 정의 하는 경우 렌더링 해야 하거나 오류가 발생 합니다.

예로 `@section` 보기에서 정의 합니다.

```html
@section Scripts {
     <script type="text/javascript" src="/scripts/main.js"></script>
   }
   ```

위 코드에서 유효성 검사 스크립트에 추가 됩니다는 `scripts` 폼을 포함 하는 보기에는 섹션입니다. 동일한 응용 프로그램의 다른 보기는 추가 스크립트를 필요 하지 않은 및 스크립트 섹션을 정의 하지 않고도 때문입니다.

보기에 정의 된 섹션은 그 즉시 레이아웃 페이지 에서만에서 사용할 수 있습니다. 부분, 구성 요소 보기 또는 보기 시스템의 다른 부분에서 참조할 수 없습니다.

### <a name="ignoring-sections"></a>섹션을 무시 하 고

기본적으로 본문 및 콘텐츠 페이지의 모든 섹션 모두 렌더링 해야 레이아웃 페이지. Razor 뷰 엔진 렌더링 된 본문 및 각 섹션 있는지 여부를 추적 하 여이 적용 합니다.

호출 하는 본문이 나 섹션을 무시 하는 뷰 엔진을 지시 하는 `IgnoreBody` 및 `IgnoreSection` 메서드.

본문 및 Razor 페이지에서 모든 섹션 렌더링 하거나 무시 됩니다.

<a name=viewimports></a>

## <a name="importing-shared-directives"></a>공유 지시문 가져오기

네임 스페이스 가져오기 또는 수행과 같은 여러 가지 방법으로 뷰 Razor 지시문 사용할 수 있습니다 [종속성 주입](dependency-injection.md)합니다. 일반적인 많은 보기에서 공유 하는 지시문을 지정할 수 있습니다 `_ViewImports.cshtml` 파일입니다. `_ViewImports` 파일은 다음과 같은 지시문을 지원 합니다.

* `@addTagHelper`

* `@removeTagHelper`

* `@tagHelperPrefix`

* `@using`

* `@model`

* `@inherits`

* `@inject`

파일에서 함수 및 섹션 정의 등의 기타 Razor 기능을 지원 하지 않습니다.

샘플 `_ViewImports.cshtml` 파일:

[!code-html[주](../../common/samples/WebApplication1/Views/_ViewImports.cshtml)]

`_ViewImports.cshtml` 파일을 ASP.NET 핵심 MVC 응용 프로그램에 일반적으로 배치 되는 `Views` 폴더입니다. A `_ViewImports.cshtml` 원하는 모든 폴더에 있는 경우에만 적용 됩니다 해당 폴더와 하위 폴더 내에서 뷰 내에서 파일을 배치할 수 있습니다. `_ViewImports`파일은 루트 수준에서 시작을 처리 되며 다음 최대 앞에 오는 각 폴더에 대 한 뷰 자체의 위치 하므로 루트 수준에서 지정 된 설정을 재정의할 수 있습니다 폴더 수준에서.

예를 들어 루트 수준 `_ViewImports.cshtml` 파일 지정 `@model` 및 `@addTagHelper`, 다른 `_ViewImports.cshtml` 보기의 컨트롤러 관련 폴더의 파일 지정 다른 `@model` 다른 추가 `@addTagHelper`, 두 태그 도우미에 액세스할 수 있는 뷰와 후자를 사용 합니다 `@model`합니다.

여러 개 있는 경우 `_ViewImports.cshtml` 파일 보기에 대해 실행 됩니다에 포함 된 지시문의 동작을 결합 된 `ViewImports.cshtml` 파일이 다음과 같이 됩니다.

* `@addTagHelper``@removeTagHelper`: 모든 실행 순서

* `@tagHelperPrefix`: 보기에 가장 근접 한 다른 어떤 재정의

* `@model`: 보기에 가장 근접 한 다른 어떤 재정의

* `@inherits`: 보기에 가장 근접 한 다른 어떤 재정의

* `@using`: 모든 포함 됩니다. 중복 항목은 무시 됩니다.

* `@inject`: 각 속성에 대 한 보기에 가장 가까운 스택을 속성 이름이 같은 다른 재정의

<a name=viewstart></a>

## <a name="running-code-before-each-view"></a>각 보기 전에 코드를 실행합니다.

모든 보기 전에 실행 해야 할에 배치 해야 코드가 있으면는 `_ViewStart.cshtml` 파일입니다. 규칙에 따라는 `_ViewStart.cshtml` 파일은 디렉터리에 `Views` 폴더입니다. 에 나열 된 문을 `_ViewStart.cshtml` 모든 전체 보기 (하지 레이아웃 및 부분 하지 뷰) 보다 먼저 실행 됩니다. 마찬가지로 [ViewImports.cshtml](xref:mvc/views/layout#viewimports), `_ViewStart.cshtml` 계층적입니다. 경우에 `_ViewStart.cshtml` 파일 보기 컨트롤러 관련 폴더에 정의 되 면의 루트에 정의 된 후 실행 됩니다는 `Views` 폴더 (있는 경우).

샘플 `_ViewStart.cshtml` 파일:

[!code-html[주](../../common/samples/WebApplication1/Views/_ViewStart.cshtml)]

위의 파일에서 모든 보기를 사용 하는 지정 된 `_Layout.cshtml` 레이아웃입니다.

> [!NOTE]
> 모두 `_ViewStart.cshtml` 나 `_ViewImports.cshtml` 에 일반적으로 배치 되는 `/Views/Shared` 폴더입니다. 이러한 파일의 버전을 응용 프로그램 수준에서 직접 배치 해야는 `/Views` 폴더입니다.

