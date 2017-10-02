---
title: "구성 요소 보기"
author: rick-anderson
description: "뷰 구성 요소는 다시 사용할 수 있는 렌더링 논리는 아무 곳 이나 사용 됩니다."
keywords: "ASP.NET Core, 구성 요소 보기, 부분 뷰"
ms.author: riande
manager: wpickett
ms.date: 02/14/2017
ms.topic: article
ms.assetid: ab4705b7-59d7-4f31-bc97-ea7f292fe926
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/views/view-components
ms.openlocfilehash: 3bc6d3f85d8ea7fb9b72b18cfd9c5ec2d07293b0
ms.sourcegitcommit: 732cd2684246e49e796836596643a8d37e20c46d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2017
---
# <a name="view-components"></a>구성 요소 보기

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)

[보거나 다운로드 샘플 코드](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/views/view-components/sample) ([다운로드 하는 방법을](xref:tutorials/index#how-to-download-a-sample))

## <a name="introducing-view-components"></a>뷰 구성 요소 소개

새 ASP.NET Core mvc 뷰 구성 요소는 부분 뷰로 유사 하지만 훨씬 더 강력 합니다. 보기 구성 요소 하지 않는 모델 바인딩을 사용 하 고만를 호출할 때 제공 된 데이터에 따라 달라 집니다. 뷰 구성 요소:

* 전체 응답 하지 않고 청크를 렌더링합니다.
* 컨트롤러와 뷰 사이 있는 테스트 용이성 이점과 분리-문제의 동일한 포함 됩니다.
* 매개 변수 및 비즈니스 논리가 있을 수 있습니다.
* 일반적으로 페이지 레이아웃에서에서 호출 됩니다.

와 같은 너무 복잡해 서 부분 뷰를 다시 사용할 수 있는 렌더링 논리는 아무 곳 이나 구성 요소 보기 사항은:

* 동적 탐색 메뉴
* 태그 클라우드 (여기서는 데이터베이스를 쿼리)
* 로그인 패널
* 쇼핑 카트
* 최근에 게시 된 문서
* 일반적인 블로그에서 사이드바 콘텐츠
* 모든 페이지에 렌더링 됩니다 하 고 로그 아웃 하거나 사용자의 상태에 있는 로그에 따라 로그인에 대 한 링크를 보여 주는 로그인 패널

뷰 구성 요소는 두 부분으로 구성 됩니다: 클래스 (대개에서 파생 [ViewComponent](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.viewcomponent)) 하 고 결과 (일반적으로 보기)을 반환 합니다. 컨트롤러와 같은 뷰 구성 요소는 POCO 수는 있지만 대부분의 개발자는에서 파생 된 메서드 및 사용할 수 있는 속성의 활용 하려고 `ViewComponent`합니다.

## <a name="creating-a-view-component"></a>뷰 구성 요소 만들기

이 섹션에서는 뷰 구성 요소를 만들려면 중요 한 요구 합니다. 이 문서의 뒷부분에 나오는 각 단계를 자세히 검사 알아보고 뷰 구성 요소를 만드는 하겠습니다.

### <a name="the-view-component-class"></a>뷰 구성 요소 클래스

뷰 구성 요소 클래스에서 다음 중 하나를 만들 수 있습니다.

* 파생 된 *ViewComponent*
* 사용 하는 클래스를 데코레이팅하는 `[ViewComponent]` 특성 또는 포함 하는 클래스에서 파생 되는 `[ViewComponent]` 특성
* 클래스는 이름이 접미사로 끝나는 만들기 *ViewComponent*

컨트롤러와 같은 구성 요소 보기 공용, 비중첩 및 비추상 클래스 여야 합니다. 뷰 구성 요소 이름은 제거 "ViewComponent" 접미사와 함께 클래스 이름이입니다. 또한 명시적으로 지정할 수를 사용 하는 `ViewComponentAttribute.Name` 속성입니다.

뷰 구성 요소 클래스:

* 생성자를 완벽 하 게 지원 [종속성 주입](../../fundamentals/dependency-injection.md)

* 즉, 사용할 수 없는 컨트롤러 수명 주기에서 파트를 사용 하지 않는 [필터](../controllers/filters.md) 뷰 구성 요소에

### <a name="view-component-methods"></a>구성 요소 메서드를 보기

에 해당 논리를 정의 하는 뷰 구성 요소는 `InvokeAsync` 반환 하는 메서드는 `IViewComponentResult`합니다. 매개 변수를 바인딩하는 모델에서가 아니라 뷰 구성 요소 호출에서 직접 가져오는 합니다. 뷰 구성 요소 하지 직접 요청을 처리 합니다. 뷰 구성 요소는 모델을 초기화 하 고 호출 하 여 보기에 전달 하는 일반적으로 `View` 메서드. 요약 하자면, 구성 요소 메서드를 보려면:

* 정의 `InvokeAsync` 반환 하는 메서드는`IViewComponentResult`
* 일반적으로 모델을 초기화 하 고 호출 하 여 보기에 전달 된 `ViewComponent` `View` 메서드
* 매개 변수에서 호출 하는 메서드의 HTTP가 아닌 야 하며, 모델 바인딩되지 않습니다.
* 됩니다 (일반적으로 보기)에서 사용자 코드에서 호출 HTTP 끝점으로 직접 연결할 수 없음, 됩니다. 뷰 구성 요소는 요청을 처리 하지 않습니다.
* 현재 HTTP 요청에서 세부 정보 보다는 서명을에 오버 로드

### <a name="view-search-path"></a>보기 검색 경로

런타임에서 다음 경로에서 보기에 대 한 검색:

   * 뷰 /\<controller_name > /Components/\<view_component_name > /\<뷰 _ 이름 >
   * 뷰/공유/구성 요소/\<view_component_name > /\<뷰 _ 이름 >

뷰 구성 요소에 대 한 기본 뷰 이름은 *기본*, 뷰 파일을 의미 하는 일반적으로 이름이 지정 됩니다 *Default.cshtml*합니다. 뷰 구성 요소 결과 만들 때 또는 호출 하는 경우 서로 다른 뷰 이름을 지정할 수 있습니다는 `View` 메서드.

보기 파일 이름을 좋습니다 *Default.cshtml* 사용 하는 *뷰/공유/구성 요소/\<view_component_name > /\<view_name >* 경로입니다. `PriorityList` 이 샘플에 사용 되는 뷰 구성 요소를 사용 하 여 *Views/Shared/Components/PriorityList/Default.cshtml* 뷰 구성 요소에 대 한 합니다.

## <a name="invoking-a-view-component"></a>뷰 구성 요소 호출

뷰 구성 요소를 사용 하려면 다음을 호출 뷰 내:

```html
@Component.InvokeAsync("Name of view component", <anonymous type containing parameters>)
```

에 전달 될 매개 변수는 `InvokeAsync` 메서드. `PriorityList` 문서에서 개발한 뷰 구성 요소에서 호출 되는 *Views/Todo/Index.cshtml* 파일 보기. 다음 예제에서 `InvokeAsync` 메서드는 두 개의 매개 변수를 사용 합니다.

[!code-html[Main](view-components/sample/ViewCompFinal/Views/Todo/IndexFinal.cshtml?range=35)]

## <a name="invoking-a-view-component-as-a-tag-helper"></a>태그 도우미로 뷰 구성 요소를 호출합니다.

ASP.NET Core 1.1 이상에서는 뷰 구성 요소도 호출할 수 있습니다는 [태그 도우미](xref:mvc/views/tag-helpers/intro):

[!code-html[Main](view-components/sample/ViewCompFinal/Views/Todo/IndexTagHelper.cshtml?range=37-38)]

파스칼식 대/소문자 클래스 및 메서드 매개 변수 태그 도우미에 대 한 변환 자신의 [kebab 대/소문자를 줄이려면](https://stackoverflow.com/questions/11273282/whats-the-name-for-dash-separated-case/12273101)합니다. 뷰 구성 요소를 호출 하는 태그 도우미를 사용 하 여는 `<vc></vc>` 요소입니다. 뷰 구성 요소는 다음과 같이 지정 됩니다.

```html
<vc:[view-component-name]
  parameter1="parameter1 value"
  parameter2="parameter2 value">
</vc:[view-component-name]>
```

참고: 태그 도우미로 뷰 구성 요소를 사용 하려면 등록 해야 사용 하 여 뷰 구성 요소를 포함 하는 어셈블리는 `@addTagHelper` 지시문입니다. 뷰 구성 요소 "MyWebApp" 라는 어셈블리에 있으면, 예를 들어에 다음 지시문을 추가 `_ViewImports.cshtml` 파일:

```csharp
@addTagHelper *, MyWebApp
```

뷰 구성 요소 뷰 구성 요소를 참조 하는 모든 파일에 태그 도우미로 등록할 수 있습니다. 참조 [태그 도우미 범위 관리](xref:mvc/views/tag-helpers/intro#managing-tag-helper-scope) 태그 도우미를 등록 하는 방법에 대 한 자세한 내용은 합니다.

`InvokeAsync` 이 자습서에 사용 된 방법:

[!code-html[Main](view-components/sample/ViewCompFinal/Views/Todo/IndexFinal.cshtml?range=35)]

태그 도우미 태그:

[!code-html[Main](view-components/sample/ViewCompFinal/Views/Todo/IndexTagHelper.cshtml?range=37-38)]

위의 샘플에서는 `PriorityList` 뷰 구성 요소는 `priority-list`합니다. 뷰 구성 요소에는 매개 변수는 kebab 소문자로에 특성으로 전달 됩니다.

### <a name="invoking-a-view-component-directly-from-a-controller"></a>컨트롤러에서 직접 뷰 구성 요소 호출

뷰 구성 요소는 일반적으로 보기에서 호출 하지만 컨트롤러 메서드에에서 직접 호출할 수 있습니다. 콘텐츠를 반환 하는 컨트롤러 동작 구성 요소 보기 컨트롤러와 같은 끝점을 정의 하지 않는 동안 쉽게 구현할 수는 `ViewComponentResult`합니다.

이 예제에서는 뷰 구성 요소는 컨트롤러에서 직접 호출 됩니다.

[!code-csharp[Main](view-components/sample/ViewCompFinal/Controllers/ToDoController.cs?name=snippet_IndexVC)]

## <a name="walkthrough-creating-a-simple-view-component"></a>연습: 간단한 뷰 구성 요소 만들기

[다운로드](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/views/view-components/sample), 빌드 및 시작 코드를 테스트 합니다. 이 간단한 프로젝트와는 `Todo` 의 목록을 표시 하는 컨트롤러 *Todo* 항목입니다.

![ToDos의 목록](view-components/_static/2dos.png)

### <a name="add-a-viewcomponent-class"></a>ViewComponent 클래스 추가

만들기는 *ViewComponents* 폴더 다음 추가 `PriorityListViewComponent` 클래스:

[!code-csharp[Main](view-components/sample/ViewCompFinal/ViewComponents/PriorityListViewComponent1.cs?name=snippet1)]

코드에 대 한 참고 사항:

* 뷰 구성 요소 클래스에 포함 될 수 있는 **모든** 프로젝트의 폴더입니다.
* 클래스 이름 PriorityList**ViewComponent** 된 접미사로 끝나는지 **ViewComponent**, 클래스 구성 요소는 뷰에서 참조 하는 경우 런타임이 "PriorityList" 문자열을 사용 합니다. 설명 하겠습니다를 자세히 나중에.
* `[ViewComponent]` 특성 뷰 구성 요소를 참조 하는 데 이름을 변경할 수 있습니다. 예를 들어 우리 수에 명명 된 클래스 `XYZ` 적용 하 고는 `ViewComponent` 특성:

  ```csharp
  [ViewComponent(Name = "PriorityList")]
     public class XYZ : ViewComponent
     ```

* `[ViewComponent]` 위의 특성은 이름을 사용 하 여 보기 구성 요소 선택기를 지시 `PriorityList` 클래스 구성 요소는 뷰에서 참조 하는 경우 "PriorityList" 문자열을 사용 하는 구성 요소와 관련 된 보기를 찾을 때. 설명 하겠습니다를 자세히 나중에.
* 이 구성 요소에서는 [종속성 주입](../../fundamentals/dependency-injection.md) 데이터 컨텍스트를 사용할 수 있도록 합니다.
* `InvokeAsync`노출 된 보기에서 호출 될 수 있는 메서드는 임의 개수의 인수 걸릴 수 있습니다.
* `InvokeAsync` 메서드 집합을 반환 `ToDo` 만족 하는 항목의 `isDone` 및 `maxPriority` 매개 변수입니다.

### <a name="create-the-view-component-razor-view"></a>구성 요소 Razor 뷰 만들기

* 만들기는 *구성 요소뷰/공유/* 폴더입니다. 이 폴더 **해야** 이름은 *구성 요소*합니다.

* 만들기는 *뷰/공유/구성 요소/PriorityList* 폴더입니다. 이 폴더 이름은 뷰 구성 요소 클래스의 이름 또는 접미사 뺀 클래스의 이름과 일치 해야 합니다 (규칙 따른 하 고 사용 되는 경우는 *ViewComponent* 클래스 이름에서 접미사). 사용 하는 경우는 `ViewComponent` 특성을 클래스 이름 특성 지정은 일치 하도록 해야 합니다.

* 만들기는 *Views/Shared/Components/PriorityList/Default.cshtml* Razor 보기:[!code-html[Main](view-components/sample/ViewCompFinal/Views/Shared/Components/PriorityList/Default1.cshtml)]
    
   Razor 뷰 목록을 가져와서 `TodoItem` 하 고 표시 합니다. 경우 뷰 구성 요소 `InvokeAsync` 메서드 (예: 우리의 샘플) 보기의 이름을 전달 하지 않습니다 *기본* 규칙에 따라 뷰 이름에 사용 됩니다. 자습서의 뒷부분에 나오는 I 보여줍니다 보기의 이름을 전달 하는 방법. 특정 컨트롤러에 대 한 기본 스타일을 재정의 하려면 보기 컨트롤러 관련 보기 폴더를 추가 합니다 (예를 들어 *Views/Todo/Components/PriorityList/Default.cshtml)*합니다.
    
    뷰 구성 요소 관련 컨트롤러 이면 컨트롤러 관련 폴더에 추가할 수 있습니다 (*Views/Todo/Components/PriorityList/Default.cshtml*).

* 추가 `div` 맨 아래에 우선 순위 목록 구성 요소에 대 한 호출을 포함 하는 *Views/Todo/index.cshtml* 파일:

    [!code-html[Main](view-components/sample/ViewCompFinal/Views/Todo/IndexFirst.cshtml?range=34-38)]

태그 `@await Component.InvokeAsync` 뷰 구성 요소를 호출 하는 구문을 보여 줍니다. 첫 번째 인수에는 구성 요소를 호출 하거나 호출 하려는의 이름입니다. 후속 매개 변수는 구성 요소에 전달 됩니다. `InvokeAsync`임의 개수의 인수를 걸릴 수 있습니다.

응용 프로그램을 테스트 합니다. 다음 이미지는 할 일 목록 및 우선 순위 항목을 보여 줍니다.

![할 일 목록 및 우선 순위 항목](view-components/_static/pi.png)

또한 컨트롤러에서 직접 뷰 구성 요소를 호출할 수 있습니다.

[!code-csharp[Main](view-components/sample/ViewCompFinal/Controllers/ToDoController.cs?name=snippet_IndexVC)]

![IndexVC 동작에서 우선 순위 항목](view-components/_static/indexvc.png)

### <a name="specifying-a-view-name"></a>뷰 이름 지정

복잡 한 뷰 구성 요소를 조건에 따라 기본이 아닌 보기를 지정 해야 합니다. 다음 코드에서 "PVC" 뷰를 지정 하는 방법을 보여 줍니다는 `InvokeAsync` 메서드. 업데이트는 `InvokeAsync` 에서 메서드는 `PriorityListViewComponent` 클래스입니다.

[!code-csharp[Main](../../mvc/views/view-components/sample/ViewCompFinal/ViewComponents/PriorityListViewComponentFinal.cs?highlight=4,5,6,7,8,9&range=28-39)]

복사는 *Views/Shared/Components/PriorityList/Default.cshtml* 파일 라는 이름의 뷰가을 *Views/Shared/Components/PriorityList/PVC.cshtml*합니다. PVC 뷰를 사용 하는 것을 나타내려면 제목을 추가 합니다.

[!code-html[Main](../../mvc/views/view-components/sample/ViewCompFinal/Views/Shared/Components/PriorityList/PVC.cshtml?highlight=3)]

업데이트 *Views/TodoList/Index.cshtml*:

<!-- Views/TodoList/Index.cshtml is never imported, so change to test tutorial -->

[!code-html[Main](view-components/sample/ViewCompFinal/Views/Todo/IndexFinal.cshtml?range=35)]

응용 프로그램을 실행 하 고 PVC 보기를 확인 합니다.

![우선 순위 뷰 구성 요소](view-components/_static/pvc.png)

PVC 뷰 렌더링 되지 않습니다 4 이상을의 우선 순위를 가진 뷰 구성 요소를 호출 하는 확인 합니다.

### <a name="examine-the-view-path"></a>뷰 경로 검사

* 변경 priority 매개 변수를 3 개 이하의 우선 순위 보기 반환 되지 않습니다.
* 일시적으로 이름 바꾸기는 *Views/Todo/Components/PriorityList/Default.cshtml* 를 *1Default.cshtml*합니다.
* 앱을 테스트, 다음과 같은 오류 메시지가 나타납니다.

   ```
   An unhandled exception occurred while processing the request.
   InvalidOperationException: The view 'Components/PriorityList/Default' was not found. The following locations were searched:
   /Views/ToDo/Components/PriorityList/Default.cshtml
   /Views/Shared/Components/PriorityList/Default.cshtml
   EnsureSuccessful
   ```

* 복사 *Views/Todo/Components/PriorityList/1Default.cshtml* 를 *Views/Shared/Components/PriorityList/Default.cshtml*합니다.
* 일부 태그를 추가 *Shared* 에서 보기를 나타내기 위해 Todo 뷰 구성 요소 뷰를는 *Shared* 폴더입니다.
* 테스트는 **Shared** 구성 요소 뷰.

![공유 구성 요소 뷰를 사용 하 여 할 일 출력](view-components/_static/shared.png)

### <a name="avoiding-magic-strings"></a>매직 문자열 방지

컴파일 시 안전성을 하려는 경우 클래스 이름의 보기 하드 코드 된 구성 요소 이름을 바꿀 수 있습니다. "ViewComponent" 접미사 없이 뷰 구성 요소를 만듭니다.

[!code-csharp[Main](../../mvc/views/view-components/sample/ViewCompFinal/ViewComponents/PriorityList.cs?highlight=10&range=5-35)]

추가 `using` 파일을 확인 하 고 사용 하는 프로그램 Razor 문을 `nameof` 연산자:

[!code-html[Main](view-components/sample/ViewCompFinal/Views/Todo/IndexNameof.cshtml?range=1-6,33-)]

## <a name="additional-resources"></a>추가 리소스

* [뷰에 종속성 주입](dependency-injection.md)
