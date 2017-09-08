---
title: "부분 뷰"
author: ardalis
description: "ASP.NET Core mvc에서 부분 뷰를 사용 하 여"
keywords: "ASP.NET Core, 부분 뷰, 부분"
ms.author: riande
manager: wpickett
ms.date: 03/14/2017
ms.topic: article
ms.assetid: 4be1b12c-b74e-44ff-826b-99ce86e8d464
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/views/partial
ms.openlocfilehash: 53df0c153b68c74250034e5b02f4a951b676eb3a
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/11/2017
---
# <a name="partial-views"></a>부분 뷰

여 [Steve Smith](http://ardalis.com), [Maher JENDOUBI](https://twitter.com/maherjend), 및 [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core MVC는 재사용 가능한 파트의 서로 다른 뷰 간에 공유 하려는 웹 페이지에 있는 경우에 유용 부분 뷰를 지원 합니다.

[샘플 코드 보기 또는 다운로드](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/views/partial/sample)

## <a name="what-are-partial-views"></a>부분 뷰는 무엇입니까?

부분 뷰는 다른 뷰 내에서 렌더링 되는 뷰입니다. 부분 뷰를 실행 하 여 생성 된 HTML 출력을 호출 (또는 부모) 보기에 렌더링 됩니다. 부분 뷰 같이 뷰를 사용 하 여는 *.cshtml* 파일 확장명입니다.

## <a name="when-should-i-use-partial-views"></a>부분 뷰는 경우 사용 해야 합니까?

부분 뷰는 효과적으로 큰 뷰를 더 작은 구성 요소로 분리 합니다. 콘텐츠 보기의 중복을 줄일 수 있으며 뷰 요소를 다시 사용할 수 있습니다. 일반 레이아웃 요소에 지정 해야 [_Layout.cshtml](layout.md)합니다. 비 레이아웃 다시 사용할 수 있는 콘텐츠 부분 뷰 개체로 캡슐화 될 수 있습니다.

논리 조각으로 구성 하는 복잡 한 페이지를 사용 하도록 설정한 경우에 자체 부분 뷰로 각 부분에 맞게 유용할 수 있습니다. 페이지의 나머지 부분과 분리에서 페이지의 각 부분을 볼 수 있습니다만 포함 되어 있으므로 전체 페이지 구조와를 부분 뷰를 렌더링 하기 페이지 자체에 대 한 보기 매핑되며 훨씬 더 간단 합니다.

팁: 따라는 [하지 반복 직접 원칙](http://deviq.com/don-t-repeat-yourself/) 보기에 있습니다.

## <a name="declaring-partial-views"></a>부분 뷰를 선언합니다.

부분 뷰를 다른 뷰와 마찬가지로 만들어집니다: 만들면는 *.cshtml* 내에 파일이 *뷰* 폴더입니다. 부분 뷰 및 일반 보기 간의 의미 체계 차이점이-다르게 렌더링만 합니다. 컨트롤러의 직접에서 반환 되는 뷰를 사용할 수 있습니다 `ViewResult`, 부분 뷰로 같은 보기를 사용할 수 있습니다. 부분 뷰 실행 하지 않는 뷰와 부분 뷰 렌더링 되는 방식 간의 주요 차이점은 *_viewstart.vbhtml* (뷰 수행-자세한 정보에 대 한 동안 *_viewstart.vbhtml* 에 [레이아웃 ](layout.md)).

## <a name="referencing-a-partial-view"></a>부분 뷰를 참조합니다.

페이지 보기 내에서 여러 가지가 부분 뷰를 렌더링할 수 있습니다. 사용 하는 가장 간단한 것 `Html.Partial`를 반환 하는 `IHtmlString` 접두사로 사용 하 여 호출 하 여 참조 될 수 있습니다 및 `@`:

[!code-html[Main](partial/sample/src/PartialViewsSample/Views/Home/About.cshtml?range=9)]

`PartialAsync` 메서드는 부분 (보기에는 코드는 일반적으로 권장 되지 않습니다)는 없지만 비동기 코드가 포함 된 뷰를 사용할 수 있습니다.

[!code-html[Main](partial/sample/src/PartialViewsSample/Views/Home/About.cshtml?range=8)]

부분 뷰를 렌더링할 수도 있고 `RenderPartial`합니다. 이 메서드는 결과 반환 하지 않습니다. 렌더링된 된 출력을 응답에 직접를 스트리밍합니다. Razor 코드 블록 내에서 호출할 수 해야 결과 반환 하지 않습니다 (호출할 수도 있습니다 `RenderPartialAsync` 필요한 경우):

[!code-html[Main](partial/sample/src/PartialViewsSample/Views/Home/About.cshtml?range=10-12)]

결과 직접 스트리밍합니다 때문에 `RenderPartial` 및 `RenderPartialAsync` 일부 시나리오에서 더 잘 수행 될 수 있습니다. 그러나 사용 하는 것이 좋습니다 대부분의 경우 `Partial` 및 `PartialAsync`합니다.

> [!NOTE]
> 보기를 코드를 실행 해야 할 경우 권장된 패턴을 사용 하는 [뷰 구성 요소](view-components.md) 부분 뷰를 대신 합니다.

### <a name="partial-view-discovery"></a>부분 뷰 검색

부분 뷰를 참조 하는 경우 여러 가지 방법으로 해당 위치를 참조할 수 있습니다.

```text
// Uses a view in current folder with this name
// If none is found, searches the Shared folder
@Html.Partial("ViewName")

// A view with this name must be in the same folder
@Html.Partial("ViewName.cshtml")

// Locate the view based on the application root
// Paths that start with "/" or "~/" refer to the application root
@Html.Partial("~/Views/Folder/ViewName.cshtml")
@Html.Partial("/Views/Folder/ViewName.cshtml")

// Locate the view using relative paths
@Html.Partial("../Account/LoginPartial.cshtml")
```

서로 다른 뷰 폴더에 이름이 같은 다른 부분 뷰를 사용할 수 있습니다. 확장명을 제외한 파일 이름으로 뷰를 참조할 때 각 폴더의 보기와 같은 폴더에 부분 뷰를 사용 합니다. 또한에 넣어서 기본 부분 뷰를 사용 하려면 지정할 수 있습니다는 *Shared* 폴더입니다. 공유 부분 뷰의 부분 뷰의의 고유 버전을 갖지 않는 모든 보기에서 사용 됩니다. 기본 부분 뷰를 사용할 수 있습니다 (에서 *Shared*)는 부모 보기와 같은 폴더에 같은 이름의 부분 뷰에 의해 재정의 됩니다.

부분 뷰 수 *연결*합니다. 즉, 부분 뷰 (으로 루프를 만들지 않으면) 다른 부분 뷰를 호출할 수 있습니다. 각 뷰나 부분 뷰 내에서 상대 경로 항상 해당 보기, 하지 루트 또는 부모 보기를 기준으로 합니다.

> [!NOTE]
> 선언 하는 경우는 [Razor](razor.md) `section` 부분 뷰에서 않을 해당 부모에 게 표시; 부분 뷰로 제한 됩니다.

## <a name="accessing-data-from-partial-views"></a>부분 뷰에서 데이터에 액세스

부모 뷰의 복사본을 가져와서 부분 뷰가 인스턴스화되면 `ViewData` 사전입니다. 부분 뷰 내에서 데이터에 대 한 업데이트는 부모 뷰에 유지 되지 않습니다. `ViewData`부분에서 변경 된 부분 뷰는 반환 될 때 보기 손실 됩니다.

인스턴스를 전달할 수 `ViewDataDictionary` 부분 뷰로:

```csharp
@Html.Partial("PartialName", customViewData)
   ```

부분 뷰를 모델을 전달할 수도 있습니다. 이 페이지의 보기 모델 또는 일부 일부를 사용자 지정 개체 수 있습니다. 모델을 전달할 수 있습니다 `Partial`,`PartialAsync`, `RenderPartial`, 또는 `RenderPartialAsync`:

```csharp
@Html.Partial("PartialName", viewModel)
   ```

인스턴스를 전달할 수 `ViewDataDictionary` 및 부분 뷰를 뷰 모델:

[!code-html[Main](partial/sample/src/PartialViewsSample/Views/Articles/Read.cshtml?range=15-16)]

다음 태그는 *Views/Articles/Read.cshtml* 두 부분 뷰를 포함 하는 뷰. 두 번째 부분 뷰는 모델에 전달 하 고 `ViewData` 부분 뷰를 합니다. 새로운 전달할 수 `ViewData` 기존 유지 하면서 사전 `ViewData` 생성자 오버 로드를 사용 하는 경우는 `ViewDataDictionary` 아래의 강조 표시 된:

[!code-html[Main](partial/sample/src/PartialViewsSample/Views/Articles/Read.cshtml)]

*뷰/공유/AuthorPartial*:

[!code-html[Main](partial/sample/src/PartialViewsSample/Views/Shared/AuthorPartial.cshtml)]

*ArticleSection* 부분:

[!code-html[Main](partial/sample/src/PartialViewsSample/Views/Articles/ArticleSection.cshtml)]

런타임에 부분 렌더링 되는 부모 보기로 렌더링 되는 자체 공유 내 *_Layout.cshtml*

![부분 뷰 출력](partial/_static/output.png)