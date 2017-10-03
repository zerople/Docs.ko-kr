---
title: "ASP.NET Core MVC에서 뷰"
author: ardalis
description: "보기에서 응용 프로그램의 데이터 표시 및 ASP.NET Core MVC에서 사용자 상호 작용을 처리 하는 방법을 알아봅니다."
keywords: "ASP.NET Core MVC, razor, viewmodel, viewdata, viewbag을 보려면"
ms.author: riande
manager: wpickett
ms.date: 09/26/2017
ms.topic: article
ms.assetid: 668c320d-c050-45e3-8161-2f460dc93b2f
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/views/overview
ms.openlocfilehash: d3fbdecaed87b3432f0532748a0833c833c65129
ms.sourcegitcommit: a60a99104fe7a29e271667cead6a06b6d8258d03
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/03/2017
---
# <a name="views-in-aspnet-core-mvc"></a>ASP.NET Core MVC에서 뷰

여 [Steve Smith](https://ardalis.com/) 및 [Luke Latham](https://github.com/guardrex)

에 **M**odel-**V**뷰-**C**ontroller (MVC) 패턴의 *보기* 응용 프로그램의 데이터 프레젠테이션 및 사용자 상호 작용을 처리 합니다. 뷰의 HTML 서식 파일은 포함 된 [Razor 태그](xref:mvc/views/razor)합니다. Razor 태그는 HTML 태그를 클라이언트에 전송 되는 웹 페이지를 생성 하기 위해 상호 작용 하는 코드입니다.

ASP.NET Core MVC 뷰는 *.cshtml* 사용 하는 파일의 [C# 프로그래밍 언어](/dotnet/csharp/) Razor 태그에 있습니다. 파일 보기 각 응용 프로그램의 이름으로 그룹화 됩니다는 일반적으로 [컨트롤러](xref:mvc/controllers/actions)합니다. 폴더에 저장 됩니다는에 *뷰* 응용 프로그램의 루트 폴더:

![Visual Studio의 솔루션 탐색기에서 뷰 폴더를 열고 About.cshtml, Contact.cshtml, 및 Index.cshtml 파일 표시에 대해 홈 폴더와](overview/_static/views_solution_explorer.png)

*홈* 컨트롤러로 표시 됩니다는 *홈* 폴더는 *뷰* 폴더입니다. *홈* 폴더에 대 한 뷰는 *에 대 한*, *연락처*, 및 *인덱스* (홈페이지) 웹 페이지입니다. 사용자는 이러한 세 가지 웹 페이지에서 컨트롤러 작업 중 하나를 요청할 때는 *홈* 빌드하고 웹 페이지를 사용자에 게 반환 하는 데는 세 개의 뷰로 있는 컨트롤러 결정 합니다.

사용 하 여 [레이아웃](xref:mvc/views/layout) 코드 반복을 줄이고 제공 일관 된 웹 페이지 섹션도 록 합니다. 레이아웃은 머리글, 탐색 및 메뉴 요소 및 바닥글에 종종 포함 됩니다. 머리글 및 바닥글에는 일반적으로 다양 한 메타 데이터 요소와 스크립트 및 스타일 자산에 대 한 링크에 대 한 상용구 태그를 포함 합니다. 레이아웃 보기에서이 상용구 태그를 방지 하는 데 도움이 됩니다.

[부분 뷰](xref:mvc/views/partial) 재사용 가능한 부분 뷰를 관리 하 여 코드 중복을 줄입니다. 예를 들어, 부분 뷰 여러 뷰에 표시 되는 블로그 웹 사이트에는 만든이 약력에 유용 합니다. 저자 약력 일반 보기 콘텐츠 이며 웹 페이지에 대 한 콘텐츠를 생성 하기 위해 실행할 코드가 필요 하지 않습니다. 저자 약력 콘텐츠는 부분 뷰를 사용 하 여이 콘텐츠 유형에 대 한 이상적인 이므로 단독으로 모델 바인딩에서 보기를 사용할 수 있습니다.

[구성 요소 확인](xref:mvc/views/view-components) 를 부분적으로 유사한 뷰는 반복 되는 코드를 줄일 수 있습니다 이러한 트래픽은 매우 웹 페이지를 렌더링 하는 데 서버에서 실행 하는 코드를 필요로 하는 콘텐츠 보기에 적합 합니다. 뷰 구성 요소는 렌더링 된 콘텐츠에 쇼핑 카트 웹 사이트와 같은 데이터베이스 상호 작용 해야 하는 경우에 유용 합니다. 뷰 구성 요소를 웹 페이지 출력을 생성 하기 위해 바인딩 모델링 제한 되지 않습니다.

## <a name="benefits-of-using-views"></a>뷰를 사용할 때의 이점

뷰를 설정 하는 데는 [ **S**eparation **o**f **C**oncerns (SoC) 디자인](http://deviq.com/separation-of-concerns/) 사용자 인터페이스 태그를 구분 하 여 MVC 응용 프로그램 내에서 응용 프로그램의 다른 부분입니다. SoC 디자인에서는 여러 가지 이점을 제공 하는 응용 프로그램 모듈식, 합니다.

* 앱이 하므로 더 잘 구성 된 유지 관리 하기가 더 쉽습니다. 뷰는 일반적으로 응용 프로그램 기능에 의해 그룹화 됩니다. 이렇게 하면 보다 쉽게 기능에 대해 작업할 때 관련 된 뷰를 찾을 수 있습니다.
* 앱의 일부는 느슨하게 결합 되어 있습니다. 작성 하 고 비즈니스 논리와 데이터 액세스 구성 요소가 별도로 응용 프로그램의 뷰를 업데이트할 수 있습니다. 반드시 응용 프로그램의 다른 부분을 업데이트 하지 않고 응용 프로그램의 뷰를 수정할 수 있습니다.
* 응용 프로그램의 사용자 인터페이스 부분 뷰는 별도 단위 테스트 하는 것이 쉽습니다.
* 더 나은 조직으로 인해 확률이 실수로 반복 부분은 사용자 인터페이스를 합니다.

## <a name="creating-a-view"></a>보기를 만드는 방법

에 컨트롤러에만 적용 되는 뷰가 생성 됩니다는 *뷰 / [ControllerName]* 폴더입니다. 컨트롤러 간에 공유 되는 뷰에 배치 되는 *뷰/공유* 폴더입니다. 뷰를 만들려면 새 파일을 추가 하 고 동일한 이름으로 연결 된 컨트롤러 작업으로 지정 된 *.cshtml* 파일 확장명입니다. 와 일치 하는 보기를 만들려면는 *에 대 한* 작업에는 *홈* 컨트롤러를 만듭니다는 *About.cshtml* 파일에 *뷰/홈*폴더:

[!code-cshtml[Main](../../common/samples/WebApplication1/Views/Home/About.cshtml)]

*Razor* 로 시작 태그는 `@` 기호입니다. C#을 배치 하 여 실행된 C# 문 내에서 코드 [Razor 코드 블록](xref:mvc/views/razor#razor-code-blocks) 중괄호을 off로 설정 (`{ ... }`). 예를 들어 "정보"의 할당을 참조 `ViewData["Title"]` 위에 표시 된 합니다. 단순히 값과 참조 하 여 HTML 내에서 값을 표시할 수 있습니다는 `@` 기호입니다. 내용을 참조는 `<h2>` 및 `<h3>` 위의 요소입니다.

위에 표시 된 콘텐츠 보기에는 사용자에 게 렌더링 되는 전체 웹 페이지의 일부일 뿐입니다. 페이지의 레이아웃의 나머지 및 다른 일반적인 보기의 요소에는 다른 보기 파일에서 지정 됩니다. 자세한 내용은 참조는 [레이아웃 항목](xref:mvc/views/layout)합니다.

## <a name="how-controllers-specify-views"></a>컨트롤러 뷰를 지정 하는 방법

뷰는으로 작업에서 일반적으로 반환 되는 [ViewResult](/aspnet/core/api/microsoft.aspnetcore.mvc.viewresult)의 형식인 [ActionResult](/aspnet/core/api/microsoft.aspnetcore.mvc.actionresult)합니다. 동작 메서드에서 만들고 반환할 수는 `ViewResult` 를 직접 하지만 일반적으로 수행 되지 않습니다. 대부분의 컨트롤러에서 상속 하므로 [컨트롤러](/aspnet/core/api/microsoft.aspnetcore.mvc.controller)를 그대로 사용 하면는 `View` 반환 하는 도우미 메서드는 `ViewResult`:

*HomeController.cs*

[!code-csharp[Main](../../common/samples/WebApplication1/Controllers/HomeController.cs?highlight=5&range=16-21)]

이 작업을 반환 하는 경우는 *About.cshtml* 마지막 섹션에 표시 된 보기는 다음과 같은 웹 페이지로 렌더링 됩니다.

![Edge 브라우저에서 렌더링 된 페이지에 대 한](overview/_static/about-page.png)

`View` 도우미 메서드에 몇 가지 오버 로드 합니다. 필요에 따라 다음을 지정할 수 있습니다.

* 반환할 명시적 보기:

  ```csharp
  return View("Orders");
  ```
* A [모델](xref:mvc/models/model-binding) 에 전달 하는 보기:

  ```csharp
  return View(Orders);
  ```
* 뷰를 모두 모델:

  ```csharp
  return View("Orders", Orders);
  ```

### <a name="view-discovery"></a>검색 보기

작업 뷰를 반환 하는 경우 프로세스를 호출할 *보기 검색* 이루어집니다. 이 프로세스 보기 이름을 기반으로 어떤 보기 파일을 사용 하는 것을 결정 합니다. 

기본적으로는 `View` 메서드 (`return View();`) 호출 될 작업 메서드와 같은 이름의 뷰를 반환 하는 것입니다. 예를 들어는 *에 대 한* `ActionResult` 메서드 컨트롤러의 이름은 뷰 파일에 대 한 검색 *About.cshtml*합니다. 런타임은 먼저는 *뷰 / [ControllerName]* 뷰에 대 한 폴더입니다. 일치 하는 뷰를 찾지 못하는 경우 검색 된 *Shared* 폴더 뷰에 대 한 합니다.

암시적으로 반환 하는 경우 문제가 되지 않습니다는 `ViewResult` 와 `return View();` 에 보기 이름을 명시적으로 전달 하거나는 `View` 메서드 `return View("<ViewName>");`합니다. 두 경우 모두 뷰의 검색이이 순서에서 일치 하는 파일의 보기에 대 한 검색합니다.

   1. *뷰 /\[ControllerName]\[ViewName].cshtml*
   1. *뷰/공유/\[ViewName].cshtml*

뷰 이름 대신 뷰 파일 경로 제공할 수 있습니다. 응용 프로그램 루트에서 시작 하는 절대 경로 사용 하는 경우 (필요에 따라부터 "/" 또는 "~ /"), *.cshtml* 확장을 지정 해야 합니다.

```csharp
return View("Views/Home/About.cshtml");
```

없이 서로 다른 디렉터리에 뷰를 지정 하는 상대 경로 사용할 수도 있습니다는 *.cshtml* 확장 합니다. 내에서 `HomeController`를 반환할 수 있습니다는 *인덱스* 볼 수 프로그램 *관리* 상대 경로 사용 하 여 뷰:

```csharp
return View("../Manage/Index");
```

마찬가지로, 현재 컨트롤러 관련 디렉터리를 나타낼 수 있습니다는 ". /" 접두사:

```csharp
return View("./About");
```

[부분 뷰](xref:mvc/views/partial) 및 [구성 요소 확인](xref:mvc/views/view-components) (비슷하지만 동일 하지 않은) 검색 메커니즘을 사용 합니다.

사용자 지정을 사용 하 여 응용 프로그램 내에 있는 뷰는 방법에 대 한 기본 규칙을 사용자 지정할 수 있습니다 [IViewLocationExpander](/aspnet/core/api/microsoft.aspnetcore.mvc.razor.iviewlocationexpander)합니다.

검색 보기는 파일 이름으로 뷰 파일을 찾는 데 사용 합니다. 기본 파일 시스템은 대/소문자 구분, 하는 경우 뷰 이름이 아마도 대/소문자 구분 됩니다. 운영 체제에서 호환성을 위해 대/소문자 컨트롤러 및 작업 이름 및 관련된 보기 폴더와 파일 이름 사이입니다. 대/소문자 구분 파일 시스템으로 작업 하는 동안 파일 보기를 찾을 수 없는 오류가 발생 하는 경우 요청 된 뷰 파일과 실제 보기 파일 이름 간의 대/소문자와 일치 하는지 확인 합니다.

컨트롤러, 작업 및 유지 관리 및 명확성에 대 한 뷰 간의 관계를 반영 하기 위해 보기에 대 한 파일 구조를 구성 하는 모범 사례를 따릅니다.

## <a name="passing-data-to-views"></a>보기에 데이터를 전달합니다.

여러 가지 방법으로 사용 하 여 보기에 데이터를 전달할 수 있습니다. 지정 하는 가장 강력한 방법입니다는 [모델](xref:mvc/models/model-binding) 보기에는 형식입니다. 이 모델은 일반적으로 라고는 *viewmodel*합니다. 동작에서 보기를 viewmodel 형식의 인스턴스로 전달 합니다.

활용 하기 위해 뷰를 사용 하면 데이터 보기를 전달 하는 viewmodel를 사용 하 여 *강력한* 형식 검사 합니다. *강력한 형식화* (또는 *강력한 형식의*) 모든 변수 및 상수에 명시적으로 정의 된 유형을 의미 (예를 들어 `string`, `int`, 또는 `DateTime`). 컴파일 타임에 보기에서 사용 되는 형식의 유효성이 검사 됩니다.

[Visual Studio](https://www.visualstudio.com/vs/) 및 [Visual Studio Code](https://code.visualstudio.com/) 이라는 기능을 사용 하 여 강력한 형식의 클래스 멤버의 목록을 [IntelliSense](/visualstudio/ide/using-intellisense)합니다. viewmodel 속성을 확인 하려는 경우 다음에 마침표 viewmodel에 대 한 변수 이름을 입력 (`.`). 이 오류를 줄일 수 코드를 더 빠르게 작성할 수 있습니다.

사용 하 여 모델을 지정 된 `@model` 지시문입니다. 사용 하 여 모델을 사용 하 여 `@Model`:

```cshtml
@model WebApplication1.ViewModels.Address

<h2>Contact</h2>
<address>
    @Model.Street<br>
    @Model.City, @Model.State @Model.PostalCode<br>
    <abbr title="Phone">P:</abbr> 425.555.0100
</address>
```

보기에 모델을 제공 하려면 컨트롤러 매개 변수로 전달 합니다.

```csharp
public IActionResult Contact()
{
    ViewData["Message"] = "Your contact page.";

    var viewModel = new Address()
    {
        Name = "Microsoft",
        Street = "One Microsoft Way",
        City = "Redmond",
        State = "WA",
        PostalCode = "98052-6399"
    };

    return View(viewModel);
}
```

보기를 제공할 수 있는 모델 유형에 대해 제한은 없습니다. 사용 하는 것이 좋습니다 **P**텍스트만 **O**ld **C**LR **O**되며 개체 (POCO) viewmodel 거의 또는 전혀 (메서드) 정의 된 동작입니다. Viewmodel 클래스에 저장 되거나는 일반적으로 *모델* 폴더 또는 별도 *Viewmodel* 응용 프로그램의 루트 폴더에 있습니다. *주소* viewmodel 위의 예제에서 사용 되는 라는 파일에 저장 된 POCO viewmodel *Address.cs*:

```csharp
namespace WebApplication1.ViewModels
{
    public class Address
    {
        public string Name { get; set; }
        public string Street { get; set; }
        public string City { get; set; }
        public string State { get; set; }
        public string PostalCode { get; set; }
    }
}
```

> [!NOTE]
> 아무 것도 동일한 클래스를 사용 하 여 프로그램 viewmodel 유형과 비즈니스 모델 유형에 대 한 수 없습니다. 그러나 별도 모델을 사용 하 여 다 하지 독립적으로 비즈니스 논리 및 데이터 액세스 부분 응용 프로그램의 사용자 보기 수 있습니다. 모델 및 viewmodel 분리도 이점을 보안 모델이 사용 하면 [모델 바인딩](xref:mvc/models/model-binding) 및 [유효성 검사](xref:mvc/models/validation) 사용자가 응용 프로그램에 전송 되는 데이터에 대 한 합니다.

### <a name="weakly-typed-data-viewdata-and-viewbag"></a>약한 형식의 데이터 (ViewData 및 ViewBag)

강력한 형식의 뷰 외에도 뷰는에 대 한 액세스는 *약한 형식의* (호출 또한 *자유로운 형식의*) 데이터의 컬렉션입니다. 강력한 종류와 달리 *약한 형식* (또는 *형식 느슨한*)를 사용 하는 데이터 형식에 있는 명시적으로 선언 하지 않는 것을 의미 합니다. 적은 양의 컨트롤러와 뷰 간에 데이터를 전달 하기 위한 약한 형식의 데이터의 컬렉션을 사용할 수 있습니다.

| 데이터 전달는 중...                        | 예제                                                                        |
| ------------------------------------------------- | ------------------------------------------------------------------------------ |
| 컨트롤러와 뷰                             | 드롭다운 목록을 데이터를 채웁니다.                                          |
| 보기 및 [레이아웃 보기](xref:mvc/views/layout)   | 설정의  **\<제목 >** 보기 파일에서는 레이아웃 보기의 요소 내용입니다.  |
| [부분 뷰](xref:mvc/views/partial) 및 보기 | 사용자 요청 하는 웹 페이지에 따라 데이터를 표시 하는 위젯입니다.      |

이 컬렉션을 통해 참조 될 수 있습니다는 `ViewData` 또는 `ViewBag` 컨트롤러와 뷰는 속성입니다. `ViewData` 속성은 약한 형식의 개체의 사전입니다. `ViewBag` 속성은 한 래퍼 `ViewData` 기본에 대 한 동적 속성을 제공 하는 `ViewData` 컬렉션입니다.

`ViewData`및 `ViewBag` 런타임에 동적으로 확인 됩니다. 컴파일 타임 형식 검사를 제공 하지 않는 둘 다은 일반적으로 더 오류가 viewmodel를 사용 하는 것입니다. 이러한 이유로, 일부 개발자가 최소한 또는 never를 사용 하려는 `ViewData` 및 `ViewBag`합니다.

**ViewData**

`ViewData`이 [ViewDataDictionary](/aspnet/core/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) 개체를 통해 액세스 `string` 키입니다. 문자열 데이터를 저장 하 고 캐스트에 대 한 필요 없이 직접 사용할 수 있지만 다른 캐스팅 해야 `ViewData` 추출 하면 특정 형식에 대 한 값 개체입니다. 사용할 수 있습니다 `ViewData` 보기 및 보기를 포함 하 여 내에서 컨트롤러에서 데이터를 전달 하 [부분 뷰](xref:mvc/views/partial) 및 [레이아웃](xref:mvc/views/layout)합니다.

다음은 인사말 및 사용 하 여 주소에 대 한 값을 설정 하는 예제 `ViewData` 동작에서:

```csharp
public IActionResult SomeAction()
{
    ViewData["Greeting"] = "Hello";
    ViewData["Address"]  = new Address()
    {
        Name = "Steve",
        Street = "123 Main St",
        City = "Hudson",
        State = "OH",
        PostalCode = "44236"
    };

    return View();
}
```

보기에서 데이터와 함께 작동 합니다.

```cshtml
@{
    // Since Address isn't a string, it requires a cast.
    var address = ViewData["Address"] as Address;
}

@ViewData["Greeting"] World!

<address>
    @address.Name<br>
    @address.Street<br>
    @address.City, @address.State @address.PostalCode
</address>
```

**ViewBag**

`ViewBag`이 [DynamicViewData](/aspnet/core/api/microsoft.aspnetcore.mvc.viewfeatures.internal.dynamicviewdata) 에 저장 된 개체에 대 한 동적 액세스를 제공 하는 개체 `ViewData`합니다. `ViewBag`캐스팅 필요 하지 않으므로 작업할 더 편리할 수 있습니다. 다음 예제에서는 사용 하는 방법을 보여 줍니다. `ViewBag` 사용 하 여 동일한 결과 함께 `ViewData` 위에:

```csharp
public IActionResult SomeAction()
{
    ViewBag.Greeting = "Hello";
    ViewBag.Address  = new Address()
    {
        Name = "Steve",
        Street = "123 Main St",
        City = "Hudson",
        State = "OH",
        PostalCode = "44236"
    };

    return View();
}
```

```cshtml
@ViewBag.Greeting World!

<address>
    @ViewBag.Address.Name<br>
    @ViewBag.Address.Street<br>
    @ViewBag.Address.City, @ViewBag.Address.State @ViewBag.Address.PostalCode
</address>
```

**ViewData 및 ViewBag를 동시에 사용 하 여**

이후 `ViewData` 및 `ViewBag` 동일한 기본 참조 `ViewData` 컬렉션을 모두 사용할 수 있습니다 `ViewData` 및 `ViewBag` 혼합 및 경우에 읽기 및 쓰기 값 사이 일치 합니다.

사용 하 여 제목을 설정 `ViewBag` 사용 하 여 설명 및 `ViewData` 맨 위에 있는 *About.cshtml* 보기:

```cshtml
@{
    Layout = "/Views/Shared/_Layout.cshtml";
    ViewBag.Title = "About Contoso";
    ViewData["Description"] = "Let us tell you about Contoso's philosophy and mission.";
}
```

속성을 읽지만 사용 역방향 `ViewData` 및 `ViewBag`합니다. 에 *_Layout.cshtml* 파일을 사용 하 여 제목 가져오는 `ViewData` 사용 하 여 설명을 가져오는 및 `ViewBag`:

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"]</title>
    <meta name="description" content="@ViewBag.Description">
    ...
```

문자열에 대 한 캐스트 필요 하지 않는 `ViewData`합니다. 사용할 수 있습니다 `@ViewData["Title"]` 캐스팅 하지 않은 채 합니다.

모두 사용 하 여 `ViewData` 및 `ViewBag` 에 혼합 및 읽기 및 쓰기 속성 일치와 같은 시간 작동 합니다. 다음 태그 렌더링 됩니다.

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <title>About Contoso</title>
    <meta name="description" content="Let us tell you about Contoso's philosophy and mission.">
    ...
```

**ViewBag ViewData 사이의 차이 요약**

* `ViewData`
  * 파생 [ViewDataDictionary](/aspnet/core/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary)는와 같은 유용할 수 있는 사전 속성이 있으므로 `ContainsKey`, `Add`, `Remove`, 및 `Clear`합니다.
  * 사전에 키는 문자열이 공백을 사용할 수 있도록 합니다. 예: `ViewData["Some Key With Whitespace"]`
  * 모든 형식 이외의 다른는 `string` 뷰를 인덱싱하지에 캐스팅 해야 `ViewData`합니다.
* `ViewBag`
  * 파생 [DynamicViewData](/aspnet/core/api/microsoft.aspnetcore.mvc.viewfeatures.internal.dynamicviewdata)점 표기법을 사용 하 여 동적 속성 만들기를 허용 하므로 (`@ViewBag.SomeKey = <value or object>`), 및 캐스트는 필요 없습니다. 구문은 `ViewBag` 사용 하면 신속 하 게 컨트롤러와 뷰를 추가할 수 있습니다.
  * 간단 하 게 null 값을 확인 합니다. 예: `@ViewBag.Person?.Name`

**ViewData 또는 ViewBag을 사용 하는 경우**

둘 다 `ViewData` 및 `ViewBag` 적은 양의 컨트롤러와 뷰 간에 데이터를 전달 하는 올바른 방법을 동등 하 게 합니다. 하나를 사용 하 여 (또는 둘 다)을 보였습니다 개인의 기본 설정 또는 해당 조직의 기본 설정을 선택 합니다. 혼합 일치 수 있지만 `ViewData` 및 `ViewBag` 개체 코드를 읽고 하나만 선택 하 고 일관 되 게 사용 하는 경우 유지 관리 하기가 더 쉽습니다. 둘 다 런타임에 동적으로 해결 되 고 따라서 런타임 오류가 발생 하기 쉽습니다 되므로 주의 해 서 사용 합니다. 일부 개발자가 완전히 방지할 합니다.

### <a name="dynamic-views"></a>동적 뷰

모델 선언 하지 마십시오 하는 뷰를 사용 하 여 입력 `@model` 있지만 이러한 메서드에 전달 된 모델 인스턴스를 갖는 (예를 들어 `return View(Address);`) 인스턴스 속성을 동적으로 참조할 수 있습니다.

```cshtml
<address>
    @Model.Street<br>
    @Model.City, @Model.State @Model.PostalCode<br>
    <abbr title="Phone">P:</abbr> 425.555.0100
</address>
```

이 기능은 유연성을 제공 되지만 컴파일 보호 지정 또는 IntelliSense 기능이 제공 하지 않습니다. 속성이 존재 하지 않으면 런타임 시 웹 페이지 생성이 실패 합니다.

## <a name="more-view-features"></a>더 많은 보기 기능

[태그 도우미](xref:mvc/views/tag-helpers/intro) 쉽게 기존 HTML 태그에 서버 쪽 동작을 추가 합니다. 태그 도우미를 사용 하 여 사용자 지정 코드 또는 보기 내에서 도우미를 작성할 필요가 방지할 수 있습니다. 태그 도우미 HTML 요소에 특성으로 적용 되 고 처리할 수 있는 편집기에서 무시 됩니다. 이 옵션을 사용 하면 편집 하 고 다양 한 도구에서에서 뷰 태그를 렌더링할 수 있습니다.

사용자 지정 HTML 태그를 생성 하는 많은 기본 제공 HTML 도우미와 수행할 수 있습니다. 더 복잡 한 사용자 인터페이스 논리에서 처리 될 수 [구성 요소 보기](xref:mvc/views/view-components)합니다. 뷰 구성 요소는 동일한 SoC 해당 컨트롤러를 제공 하 고 뷰 제공 됩니다. 동작 및 뷰가 공통 사용자 인터페이스 요소에 의해 사용 되는 데이터를 처리 하는 대 한 필요성을 제거할 수 있습니다.

ASP.NET Core의 다른 많은 요소와 마찬가지로 뷰 지원 [종속성 주입](xref:fundamentals/dependency-injection), 서비스 수를 허용 [뷰에 주입 된](xref:mvc/views/dependency-injection)합니다.
