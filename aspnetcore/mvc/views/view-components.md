---
title: "구성 요소를 볼 | Microsoft 문서"
author: rick-anderson
description: "뷰 구성 요소는 다시 사용할 수 있는 렌더링 논리 있는 아무 곳 이나 사용 됩니다."
keywords: "ASP.NET Core, 구성 요소 보기 부분 뷰"
ms.author: riande
manager: wpickett
ms.date: 02/14/2017
ms.topic: article
ms.assetid: ab4705b7-59d7-4f31-bc97-ea7f292fe926
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/views/view-components
translationtype: Machine Translation
ms.sourcegitcommit: 46630f7764d0a4169cf0b3be910bb8f8f312bbda
ms.openlocfilehash: be27a8a13f0260b21b7527a5773496916a197047
ms.lasthandoff: 03/23/2017

---
# <a name="view-components"></a>구성 요소 보기

[Rick Anderson](https://twitter.com/RickAndMSFT)

[샘플 코드 보기 또는 다운로드](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/views/view-components/sample)

## <a name="introducing-view-components"></a>뷰 구성 요소를 소개합니다.

새로운 ASP.NET 핵심 mvc 뷰 구성 요소는 부분 뷰 비슷합니다 하지만 훨씬 더 강력 합니다. 뷰 구성 요소 모델 바인딩을 사용 하 여 하를 호출할 때 제공 하는 데이터에만 종속 마십시오. 뷰 구성 요소:

* 전체 응답 하지 않고 청크를 렌더링합니다.
* 동일한--중요 한 부분의 분리와 테스트 용이성 혜택 컨트롤러와 뷰 사이 포함 합니다.
* 매개 변수 및 비즈니스 논리를 가질 수 있습니다.
* 일반적으로 레이아웃 페이지에서 호출 되며

뷰 구성 요소와 같은 너무 복잡 하는 부분 뷰를 다시 사용할 수 있는 렌더링 논리 있는 아무 곳 이나 사용 됩니다.

* 동적 탐색 메뉴
* 태그 클라우드 (여기서는 데이터베이스 쿼리)
* 로그인 패널
* 쇼핑 카트
* 최근에 게시 된 문서
* 일반적인 블로그 사이드바 콘텐츠
* 모든 페이지에 렌더링 됩니다 및 로그 아웃 하거나 사용자의 상태에 있는 로그에 따라 로그인에 대 한 링크를 표시 하는 로그인 패널

A `view component` 클래스 두 부분으로 구성 됩니다 (일반적으로에서 파생 [ViewComponent](https://docs.microsoft.com/en-us/aspnet/core/api/microsoft.aspnetcore.mvc.viewcomponent)) 하 고 결과 (일반적으로 보기)을 반환 합니다. 컨트롤러와 같은 뷰 구성 요소에 POCO 수는 있지만 대부분의 개발자는에서 파생 하 여 메서드 및 속성의 이용 하려면 `ViewComponent`합니다.

## <a name="creating-a-view-component"></a>뷰 구성 요소 만들기

이 섹션에는 뷰 구성 요소를 만드는 높은 수준의 요구 사항을 포함 합니다. 기사 뒷부분에서 각 단계를에서 검토 하 고 뷰 구성 요소를 만들어야 합니다.

### <a name="the-view-component-class"></a>뷰 구성 요소 클래스

뷰 구성 요소 클래스에서 다음 중 하나를 만들 수 있습니다.

* 파생 된 *ViewComponent*
* 사용 하는 클래스를 데코레이팅하는 `[ViewComponent]` 특성 또는 사용 하는 클래스에서 파생 되는 `[ViewComponent]` 특성
* 이름 접미사와 함께 끝나는 클래스를 만드는 *ViewComponent*

컨트롤러와 같은 구성 요소 보기에는 공용, 중첩 되지 않은 추상 클래스 이어야 합니다. 뷰 구성 요소 이름은 "ViewComponent" 접미사를 제거 사용 하 여 클래스 이름이입니다. 또한 명시적으로 지정할 수를 사용 하는 `ViewComponentAttribute.Name` 속성입니다.

뷰 구성 요소 클래스:

* 생성자를 완벽 하 게 지원 [종속성 주입](../../fundamentals/dependency-injection.md)

* 즉, 사용할 수 없는 컨트롤러 수명 주기에서 파트를 사용 하지 않는 [필터](../controllers/filters.md) 뷰 구성 요소에서

### <a name="view-component-methods"></a>뷰 구성 요소 메서드

논리를 정의 하는 뷰 구성 요소는 `InvokeAsync` 메서드를 반환 하는 `IViewComponentResult`합니다. 매개 변수는 호출을 바인딩하는 모델에서가 아니라 뷰 구성 요소에서 직접 제공 합니다. 뷰 구성 요소 하지 직접 처리를 요청 합니다. 뷰 구성 요소는 모델을 초기화 하 고 호출 하 여 보기에 전달 하는 일반적으로 `View` 메서드. 요약 하자면, 구성 요소의 메서드를 보려면:

* 정의 *`InvokeAsync`* 반환 하는 메서드는`IViewComponentResult`
* 일반적으로 모델을 초기화 하 고 호출 하 여 보기에 전달 된 `ViewComponent` `View` 메서드  
* 매개 변수에서 호출 하는 메서드, HTTP가 아닌 어, 모델 바인딩되지 않습니다.
* (일반적으로 보기)에서 사용자 코드에서 호출 HTTP 끝점으로 직접 연결할 수 없음, 됩니다. 뷰 구성 요소는 요청을 처리 하지 않습니다.
* 현재 HTTP 요청에서 세부 정보 보다는 서명을에 오버 로드 되며

### <a name="view-search-path"></a>보기 검색 경로

다음 경로에 보기에 대 한 런타임 검색합니다.

   * 보기 /\<controller_name > /Components/\<view_component_name > /\<뷰 _ 이름 >
   * 보기/Shared/구성 요소/\<view_component_name > /\<뷰 _ 이름 >

뷰 구성 요소에 대 한 기본 뷰 이름은 *기본*, 즉 뷰 파일 일반적으로 이름이 지정 됩니다 *Default.cshtml*합니다. 뷰 구성 요소 결과 만들 때 또는 호출 하는 경우 서로 다른 뷰 이름을 지정할 수는 `View` 메서드.

보기 파일 이름을 하는 것이 좋습니다 *Default.cshtml* 를 사용 하 고는 *뷰/공유/구성 요소/\<view_component_name > /\<뷰 _ 이름 >* 경로입니다. `PriorityList` 이 샘플에 사용 되는 뷰 구성 요소를 사용 하 여 *Views/Shared/Components/PriorityList/Default.cshtml* 뷰 구성 요소에 대 한 합니다.

## <a name="invoking-a-view-component"></a>뷰 구성 요소를 호출합니다.

뷰 구성 요소를 사용 하려면 호출 `@Component.InvokeAsync("Name of view component", <anonymous type containing parameters>)` 보기에서. 에 전달 될 매개 변수는 `InvokeAsync` 메서드.  `PriorityList` 문서에서 개발한 뷰 구성 요소에서 호출 되는 *Views/Todo/Index.cshtml* 파일 보기. 다음 예제에서는 `InvokeAsync` 메서드는 두 개의 매개 변수를 사용 합니다.

[!code-html[주](view-components/sample/ViewCompFinal/Views/Todo/IndexFinal.cshtml?range=35)]

## <a name="invoking-a-view-component-as-a-tag-helper"></a>태그 도우미로 뷰 구성 요소를 호출합니다.

ASP.NET Core 1.1 이상에서는로 뷰 구성 요소를 호출할 수 있습니다는 [태그 도우미](xref:mvc/views/tag-helpers/intro):

[!code-html[주](view-components/sample/ViewCompFinal/Views/Todo/IndexTagHelper.cshtml?range=37-38)]

파스칼식 대/소문자 클래스 및 메서드 매개 변수를 태그 도우미로 변환 됩니다 자신의 [kebab 사례를 줄이려면](http://stackoverflow.com/questions/11273282/whats-the-name-for-dash-separated-case/12273101#12273101)합니다. 뷰 구성 요소를 호출 하는 태그 도우미를 사용 하 여 `<vc></vc>` 요소입니다. 뷰 구성 요소에 따라 다음과 같이 지정 됩니다.

```html
<vc:[view-component-name]>
  parameter1="parameter1 value"
  parameter2="parameter2 value"
</vc:[view-component-name]>
```

`InvokeAsync` 이 자습서에서 사용 하는 방법:

[!code-html[주](view-components/sample/ViewCompFinal/Views/Todo/IndexFinal.cshtml?range=35)]

태그 도우미 태그:

[!code-html[주](view-components/sample/ViewCompFinal/Views/Todo/IndexTagHelper.cshtml?range=37-38)]

위의 샘플에는 `PriorityList` 뷰 구성 요소는 `priority-list`합니다. 뷰 구성 요소에는 매개 변수는 더 낮은 kebab 경우 arributes 변수로 전달 됩니다.

### <a name="invoking-a-view-component-directly-from-a-controller"></a>컨트롤러에서 직접 뷰 구성 요소를 호출합니다.

뷰 구성 요소는 일반적으로 보기에서 호출 되어 있지만 컨트롤러 메서드에서 직접 호출할 수 있습니다. 구성 요소 보기 컨트롤러와 같은 끝점을 정의 하지 않는 동안의 콘텐츠를 반환 하는 컨트롤러 작업을 쉽게 구현할 수 있습니다는 `ViewComponentResult`합니다.

이 예제에서는 컨트롤러에서 직접 뷰 구성 요소 호출 됩니다.

[!code-csharp[주](view-components/sample/ViewCompFinal/Controllers/ToDoController.cs?name=snippet_IndexVC)]

## <a name="walkthrough-creating-a-simple-view-component"></a>연습: 간단한 뷰 구성 요소 만들기

[다운로드](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/views/view-components/sample), 빌드 및 시작 코드를 테스트 합니다. 간단한 프로젝트와는 `Todo` 의 목록을 표시 하는 컨트롤러 *Todo* 항목입니다.

![ToDos 목록](view-components/_static/2dos.png)

### <a name="add-a-viewcomponent-class"></a>ViewComponent 클래스 추가

만들기는 *ViewComponents* 폴더에 다음을 추가 하 고 `PriorityListViewComponent` 클래스입니다.

[!code-csharp[주](view-components/sample/ViewCompFinal/ViewComponents/PriorityListViewComponent1.cs?name=snippet1)]

코드에 대 한 참고 사항:

* 뷰 구성 요소 클래스에 포함 될 수 있는 **모든** 프로젝트의 폴더입니다.
* 클래스 이름을 PriorityList 되므로**ViewComponent** 된 접미사로 끝나는지 **ViewComponent**, 클래스 구성 요소는 뷰에서 참조 하는 경우 런타임에서 "PriorityList" 문자열을 사용 합니다. 설명 하겠습니다 하 자세히 나중.
* `[ViewComponent]` 특성 보기 구성 요소를 참조 하는 데 사용 하는 이름을 변경할 수 있습니다. 예를 들어, 여기서 이름을 사용할 수도 있습니다 클래스 `XYZ`를 적용 하 고는 `ViewComponent` 특성:

  ```csharp
  [ViewComponent(Name = "PriorityList")]
     public class XYZ : ViewComponent
     ```

* `[ViewComponent]` 위의 특성 이름을 사용 하 여 보기 구성 요소 선택기를은 `PriorityList` 클래스 구성 요소는 뷰에서 참조 하는 경우 "PriorityList" 문자열을 사용 하는 구성 요소와 관련 된 보기를 찾을 때. 설명 하겠습니다 하 자세히 나중.
* 구성 요소를 사용 하 여 [종속성 주입](../../fundamentals/dependency-injection.md) 데이터 컨텍스트를 사용할 수 있도록 합니다.
* `InvokeAsync`노출 된 보기에서 호출할 수 있는 메서드는 임의의 수의 인수 걸릴 수 있습니다.
* `InvokeAsync` 메서드 집합을 반환 `ToDo` 충족 하는 항목의 `isDone` 및 `maxPriority` 매개 변수입니다.

### <a name="create-the-view-component-razor-view"></a>구성 요소 Razor 뷰 만들기

* 만들기는 *구성 요소뷰/공유/* 폴더입니다. 이 폴더 **해야** 이름은 *구성 요소*합니다.

* 만들기는 *뷰/공유/구성 요소/PriorityList* 폴더입니다. 뷰 구성 요소 클래스의 이름 또는 접미사 뺀 클래스의 이름을이 폴더 이름은 일치 해야 합니다 (규칙 따른 하 고 사용 되는 경우는 *ViewComponent* 클래스 이름에 접미사). 사용 하는 경우는 `ViewComponent` 특성을 클래스 이름 특성 지정와 일치 하도록 해야 합니다.

* 만들기는 *Views/Shared/Components/PriorityList/Default.cshtml* Razor 보기: [!code-html [주](view-components/sample/ViewCompFinal/Views/Shared/Components/PriorityList/Default1.cshtml)]
    
   Razor 보기의 목록을 가져오고 `TodoItem` 하 고 표시 합니다. 경우 뷰 구성 요소 `InvokeAsync` 메서드 (예: 우리의 샘플) 보기의 이름을 전달 하지 않습니다 *기본* 규칙에 따라 뷰 이름에 사용 됩니다. 자습서의 뒷부분에 나오는 보기의 이름을 전달 하는 방법을 살펴보겠습니다. 특정 컨트롤러에 대 한 기본 스타일을 재정의 하려면 보기 폴더에 대 한 특정 보기 컨트롤러를 추가 합니다 (예를 들어 *Views/Todo/Components/PriorityList/Default.cshtml)*합니다.
    
    뷰 구성 요소 특정 컨트롤러 이면 컨트롤러 특정 폴더를 추가할 수 있습니다 (*Views/Todo/Components/PriorityList/Default.cshtml*).

* 추가 `div` 맨 아래에 우선 순위 목록 구성 요소에 대 한 호출을 포함 하는 *Views/Todo/index.cshtml* 파일:

    [!code-html[주](view-components/sample/ViewCompFinal/Views/Todo/IndexFirst.cshtml?range=34-38)]

태그 `@await Component.InvokeAsync` 뷰 구성 요소를 호출 하기 위한 구문을 보여 줍니다. 첫 번째 인수는를 호출 하거나 호출 하는 구성 요소의 이름. 후속 매개 변수는 구성 요소에 전달 됩니다. `InvokeAsync`임의 개수의 인수를 걸릴 수 있습니다.

앱을 테스트 합니다. 다음 이미지는 할 일 목록 및 우선 순위 항목을 보여 줍니다.

![할 일 목록 및 우선 순위 항목](view-components/_static/pi.png)

또한 컨트롤러에서 직접 뷰 구성 요소를 호출할 수 있습니다.

[!code-csharp[주](view-components/sample/ViewCompFinal/Controllers/ToDoController.cs?name=snippet_IndexVC)]

![IndexVC 작업의 우선 순위 항목](view-components/_static/indexvc.png)

### <a name="specifying-a-view-name"></a>뷰 이름을 지정합니다.

복잡 한 뷰 구성 요소를 조건에 따라 기본이 아닌 뷰를 지정 해야 합니다. 다음 코드에서는 "PVC" 뷰를 지정 하는 방법을 보여 줍니다.는 `InvokeAsync` 메서드. 업데이트는 `InvokeAsync` 에서 메서드는 `PriorityListViewComponent` 클래스입니다.

[!code-csharp[주](../../mvc/views/view-components/sample/ViewCompFinal/ViewComponents/PriorityListViewComponentFinal.cs?highlight=4,5,6,7,8,9&range=28-39)]

복사는 *Views/Shared/Components/PriorityList/Default.cshtml* 파일을 명명 된 뷰 *Views/Shared/Components/PriorityList/PVC.cshtml*합니다. PVC 보기는 사용 됨을 나타내는 머리글을 추가 합니다.

[!code-html[주](../../mvc/views/view-components/sample/ViewCompFinal/Views/Shared/Components/PriorityList/PVC.cshtml?highlight=3)]

업데이트 *Views/TodoList/Index.cshtml*

<!-- Views/TodoList/Index.cshtml is never imported, so change to test tutorial -->

[!code-html[주](view-components/sample/ViewCompFinal/Views/Todo/IndexFinal.cshtml?range=35)]

앱을 실행 하 고 PVC 보기를 확인 합니다.

![우선 순위 보기 구성](view-components/_static/pvc.png)

PVC 뷰가 렌더링 되지 4 이상인 우선 순위가 뷰 구성 요소를 호출 하는 확인 합니다.

### <a name="examine-the-view-path"></a>뷰 경로 검사 합니다.

* 변경 priority 매개 변수를&3; 개 이하로 우선 순위 보기는 반환 되지 않습니다.
* 이름을 일시적으로 *Views/Todo/Components/PriorityList/Default.cshtml* 를 *1Default.cshtml*합니다.
* 앱 테스트, 다음 오류 메시지가 나타납니다.

   <!-- literal_block {"ids": [], "xml:space": "preserve"} -->

   ```
    An unhandled exception occurred while processing the request.
    InvalidOperationException: The view 'Components/PriorityList/Default' was not found. The following locations were searched:
    /Views/ToDo/Components/PriorityList/Default.cshtml
    /Views/Shared/Components/PriorityList/Default.cshtml
    EnsureSuccessful
      ```

* Copy *Views/Todo/Components/PriorityList/1Default.cshtml* to *Views/Shared/Components/PriorityList/Default.cshtml.
* Add some markup to the *Shared* Todo view component view to indicate the view is from the *Shared* folder.
* Test the **Shared** component view.

![ToDo output with Shared component view](view-components/_static/shared.png)

### Avoiding magic strings

If you want compile time safety you can replace the hard coded view component name with the class name. Create the view component without the "ViewComponent" suffix:

[!code-csharp[Main](../../mvc/views/view-components/sample/ViewCompFinal/ViewComponents/PriorityList.cs?highlight=10,14&range=4-34)]

Add a `using` statement to your Razor view file and use the `nameof` operator:

[!code-html[Main](view-components/sample/ViewCompFinal/Views/Todo/IndexNameof.cshtml?range=1-6,33-)]

## Additional Resources

* [Dependency injection into views](dependency-injection.md)

* `ViewComponent`

