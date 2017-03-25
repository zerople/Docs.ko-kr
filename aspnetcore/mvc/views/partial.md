---
title: "이 기능은 부분 뷰 | Microsoft 문서"
author: ardalis
description: "ASP.NET 핵심 MVC의 부분 뷰를 사용 하 여"
keywords: "ASP.NET Core, 부분 뷰 부분"
ms.author: riande
manager: wpickett
ms.date: 03/14/2017
ms.topic: article
ms.assetid: 4be1b12c-b74e-44ff-826b-99ce86e8d464
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/views/partial
translationtype: Machine Translation
ms.sourcegitcommit: aabc57571f2ba286a31e645930b04fcd94099210
ms.openlocfilehash: 51792e6482a0fd573c5a9af7d2ae78e5e614faf8
ms.lasthandoff: 03/23/2017

---
# <a name="partial-views"></a>이 기능은 부분 뷰

여 [Steve Smith](http://ardalis.com) [Maher JENDOUBI](https://twitter.com/maherjend) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core MVC는 서로 다른 뷰 간에 공유 하려는 웹 페이지의 재사용 가능한 부분을 사용 하는 경우 유용한 부분 뷰를 지원 합니다.

[샘플 코드 보기 또는 다운로드](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/views/partial/sample)

## <a name="what-are-partial-views"></a>이 기능은 부분 뷰는 무엇입니까?

부분 뷰는 다른 보기 내에서 렌더링 되는 뷰입니다. 부분 뷰를 실행 하 여 생성 된 HTML 출력 호출 (또는 부모) 보기에 렌더링 됩니다. 이 기능은 부분 뷰 보기 등을 사용 하 여는 *.cshtml* 파일 확장명입니다.

## <a name="when-should-i-use-partial-views"></a>이 기능은 부분 뷰를 언제 사용 합니까?

부분 뷰는 큰 뷰를 중단 하기 더 작은 구성 요소로 효과적인 방법입니다. 내용 보기의 중복을 줄일 수 있으며 보기 요소를 다시 사용할 수 있습니다. 일반적인 레이아웃 요소를 지정 해야 [_Layout.cshtml](layout.md)합니다. 비 레이아웃 다시 사용할 수 있는 콘텐츠 부분 뷰 개체로 캡슐화 될 수 있습니다.

여러 가지 논리적 부분으로 구성 하는 복잡 한 페이지를 사용 하는 경우에 고유한 부분 보기와 각 부분에 맞게 유용할 수 있습니다. 격리 된 상태는 페이지의 나머지 부분에서 페이지의 각 부분을 볼 수 있습니다 되 고 전체 페이지 구조 및 부분 뷰를 렌더링 하는 호출만 포함 하 고 있으므로 페이지 자체에 대 한 보기 훨씬 간단 하 게 됩니다.

팁: 따라는 [안 함 반복 직접 원칙](http://deviq.com/don-t-repeat-yourself/) 보기에 있습니다.

## <a name="declaring-partial-views"></a>이 기능은 부분 뷰를 선언합니다.

다른 뷰와 마찬가지로 부분 뷰가 만들어집니다: 만들면는 *.cshtml* 내에서 파일의 *뷰* 폴더입니다. 부분 뷰와 일반 보기 사이 의미 체계 차이가-다르게 렌더링만 합니다. 컨트롤러에서 직접 반환 되는 보기를 사용할 수 있습니다 `ViewResult`, 부분 뷰로 같은 보기를 사용할 수 있습니다. 뷰 및 부분 뷰 렌더링 되는 방법에 대 한 주요 차이점은 부분 뷰 실행 되지 않습니다 *_ViewStart.cshtml* (뷰 수행-자세한 정보에 대 한 동안 *_ViewStart.cshtml* 에서 [레이아웃](layout.md)).

## <a name="referencing-a-partial-view"></a>참조 하는 부분 뷰

페이지 보기 내에서 여러 가지 방법을 통해 부분 뷰를 렌더링할 수 있습니다. 사용 하는 가장 간단한 것 `Html.Partial`를 반환 하는 `IHtmlString` 및 사용 하 여 호출을 접두사로 사용 하 여 참조할 수 있는 `@`:

[!code-html[주](partial/sample/src/PartialViewsSample/Views/Home/About.cshtml?range=9)]

`PartialAsync` 부분 (코드 보기에서 일반적으로 권장 되지 않습니다)는 없지만 비동기 코드가 포함 된 보기에 대 한 메서드를 사용할 수 있습니다.

[!code-html[주](partial/sample/src/PartialViewsSample/Views/Home/About.cshtml?range=8)]

부분 뷰를 렌더링할 수 `RenderPartial`합니다. 이 메서드는 결과 반환 하지 않습니다. 응답에 직접 렌더링된 된 출력을 스트리밍합니다. 결과 반환 하지 않습니다, Razor 코드 블록 내에서 호출 되어야 합니다 (호출할 수도 있습니다 `RenderPartialAsync` 필요한 경우):

[!code-html[주](partial/sample/src/PartialViewsSample/Views/Home/About.cshtml?range=10-12)]

결과 직접 스트리밍합니다 때문에 `RenderPartial` 및 `RenderPartialAsync` 일부 시나리오에서 더 잘 수행 될 수 있습니다. 그러나 대부분의 경우 하는 것이 좋습니다 사용 하 여 `Partial` 및 `PartialAsync`합니다.

> [!NOTE]
> 권장 방법은 사용 하 여 보기, 코드를 실행 하는 경우는 [뷰 구성 요소](view-components.md) 부분 뷰 대신 합니다.

### <a name="partial-view-discovery"></a>부분 뷰 검색

부분 뷰를 참조할 때에 여러 가지 방법으로 해당 위치를 참조할 수 있습니다.

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

다른 보기 폴더에 이름이 같은 다른 부분 뷰를 사용할 수 있습니다. 파일 확장명 없이 이름으로 뷰를 참조할 때 각 폴더에 보기와 같은 폴더에는 부분 뷰를 사용 합니다. 또한 넣어서 기본 부분 뷰를 사용 하 여 지정할 수 있습니다는 *공유* 폴더입니다. 공유 부분 보기 부분 보기의 고유 버전을 갖지 않는 모든 보기에서 사용 됩니다. 기본 부분 뷰를 사용할 수 있습니다 (에서 *공유*), 부모 보기와 같은 폴더에 같은 이름의 부분 보기에 의해 재정의 되어입니다.

이 기능은 부분 뷰 수 *연결*합니다. 즉, 부분 뷰 (가능한 경우 루프를 만들지 않으면) 다른 부분 뷰를 호출할 수 있습니다. 각 뷰나 부분 뷰 내에서 상대 경로 항상 해당 보기, 하지 루트 또는 부모 보기를 기준으로 합니다.

> [!NOTE]
> 선언 하는 경우는 [Razor](razor.md) `section` 부분 뷰로 제한 됩니다; 부분 뷰에서 되지 것입니다 때 그에 게 표시 합니다.

## <a name="accessing-data-from-partial-views"></a>이 기능은 부분 뷰에서 데이터에 액세스

부모 보기의 복사본을 가져와서 부분 뷰를 인스턴스화할 때 `ViewData` 사전입니다. 부모 보기에는 부분 뷰 내에서 데이터에 대 한 업데이트 유지 되지 않습니다. `ViewData`부분에서 변경 된 뷰는 부분 뷰를 반환 하는 경우 손실 됩니다.

인스턴스를 전달할 수 `ViewDataDictionary` 부분 보기:

```csharp
@Html.Partial("PartialName", customViewData)
   ```

또한 부분 보기에는 모델을 전달할 수 있습니다. 이 페이지의 뷰 모델의 일부 또는 사용자 지정 개체 수 있습니다. 모델을 전달할 수 있습니다 `Partial`,`PartialAsync`, `RenderPartial`, 또는 `RenderPartialAsync`:

```csharp
@Html.Partial("PartialName", viewModel)
   ```

인스턴스를 전달할 수 `ViewDataDictionary` 및 부분 뷰를 뷰 모델:

[!code-html[주](partial/sample/src/PartialViewsSample/Views/Articles/Read.cshtml?range=14-15)]

아래에서는 태그는 *Views/Articles/Read.cshtml* 두 부분 뷰를 포함 하는 뷰. 두 번째 부분 뷰 모델에 전달 하 고 `ViewData` 부분 보기에 있습니다. 새로운 전달할 수 `ViewData` 기존 그대로 유지 하면서 사전 `ViewData` 생성자 오버 로드를 사용 하는 경우는 `ViewDataDictionary` 아래의 강조 표시 된:

[!code-html[주](partial/sample/src/PartialViewsSample/Views/Articles/Read.cshtml)]

*뷰/공유/AuthorPartial*:

[!code-html[주](partial/sample/src/PartialViewsSample/Views/Shared/AuthorPartial.cshtml)]

*ArticleSection* 부분:

[!code-html[주](partial/sample/src/PartialViewsSample/Views/Articles/ArticleSection.cshtml)]

런타임에 부분 렌더링 되는 부모 보기로 렌더링 되는 공유 내 *_Layout.cshtml*

![출력 부분 보기](partial/_static/output.png)
