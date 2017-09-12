---
title: "뷰 개요"
author: ardalis
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 668c320d-c050-45e3-8161-2f460dc93b2f
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/views/overview
ms.openlocfilehash: 3b33c13f2385d3b07ba9b6f0bc0fd560abc3735c
ms.sourcegitcommit: 9cdbfd0d670d70b9c354216aabee260c52dad5ee
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/12/2017
---
# <a name="rendering-html-with-views-in-aspnet-core-mvc"></a>ASP.NET Core MVC에서 뷰가 포함 된 HTML 렌더링

으로 [Steve Smith](https://ardalis.com/)

ASP.NET Core MVC 컨트롤러 사용 하 여 서식이 지정 된 결과 반환할 수 *뷰*합니다.

## <a name="what-are-views"></a>뷰는 무엇입니까?

모델-뷰-컨트롤러 (MVC) 패턴에는 *보기* 앱과 사용자의 상호 작용의 프레젠테이션 세부 정보를 캡슐화 합니다. 뷰는 클라이언트로 전송할 콘텐츠를 생성 하는 포함 된 코드를 사용 하 여 HTML 템플릿을입니다. 사용 하 여 뷰 [Razor 구문을](razor.md), 코드가 최소한의 코드 또는 공식 절차와 HTML 상호 작용할 수 있습니다.

ASP.NET Core MVC 뷰는 *.cshtml* 에서 기본적으로 저장 된 파일을 *뷰* 응용 프로그램 내에서 폴더입니다. 일반적으로 각 컨트롤러에는 특정 컨트롤러 작업에 대 한 보기 된 고유한 폴더에 포함 됩니다.

![솔루션 탐색기에서 views 폴더](overview/_static/views_solution_explorer.png)

특정 작업 보기 외에도 [부분 뷰](partial.md), [레이아웃 및 기타 특수 보기 파일](layout.md) 는 반복을 절감 하 고 응용 프로그램의 보기 내에서 다시 사용할 수 있도록 허용 하는 데 사용할 수 있습니다.

## <a name="benefits-of-using-views"></a>뷰를 사용할 때의 이점

뷰를 제공 [문제의 분리](http://deviq.com/separation-of-concerns/) 별도로 비즈니스 논리에서 사용자 인터페이스 수준 태그를 캡슐화 하는 MVC 응용 프로그램 내에서. ASP.NET MVC 뷰 사용 [Razor 구문을](razor.md) HTML 태그 및 서버 쪽 논리 단계를 전환할 수 있습니다. 일반적으로 반복적인 측면의 응용 프로그램의 사용자 인터페이스를 사용 하 여 뷰 간에 쉽게 다시 사용할 수 [레이아웃 및 공유 지시문](layout.md) 또는 [부분 뷰](partial.md)합니다.

## <a name="creating-a-view"></a>보기를 만드는 방법

에 컨트롤러에만 적용 되는 뷰가 생성 됩니다는 *뷰 / [ControllerName]* 폴더입니다. 컨트롤러 간에 공유 되는 뷰에 배치 되는 */뷰/공유* 폴더입니다. 연결 된 컨트롤러 작업을 동일 파일 보기 이름을 지정 하 고 추가 된 *.cshtml* 파일 확장명입니다. 에 대 한 보기를 만드는 예는 *에 대 한* 작업에는 *홈* 컨트롤러를 만들면 됩니다는 *About.cshtml* 파일에  * /뷰/홈*폴더입니다.

샘플 보기 파일 (*About.cshtml*):

[!code-html[Main](../../common/samples/WebApplication1/Views/Home/About.cshtml)]

*Razor* 코드도 표시 되는 `@` 기호입니다. C# 문에 Razor가 중괄호 코드 블록을 off로 설정 내에서 실행 됩니다 (`{` `}`), "정보" 할당과 같은 하는 `ViewData["Title"]` 위에 표시 된 요소입니다. Razor를 단순히 값과 참조 하 여 HTML 내에서 값을 표시 하려면 사용할 수는 `@` 기호, 내에서 표시 된 것 처럼는 `<h2>` 및 `<h3>` 위의 요소입니다.

이 뷰를 담당 하 고 출력의 부분에만 집중 합니다. 페이지의 레이아웃의 나머지와 다른 공용 보기의 요소를 다른 곳에서 지정 됩니다. 에 대 한 자세한 내용은 [레이아웃 및 공유 뷰 논리](layout.md)합니다.

## <a name="how-do-controllers-specify-views"></a>보기를 지정 하는 컨트롤러 어떻게 해야 하나요?

뷰는으로 작업에서 일반적으로 반환 되는 `ViewResult`합니다. 동작 메서드에서 만들고 반환할 수는 `ViewResult` 를 직접 하지만 일반적으로 컨트롤러에서 상속 하는 경우 `Controller`, 단순히 사용할는 `View` 도우미 메서드를이 예제로 보여 줍니다:

*HomeController.cs*

[!code-csharp[Main](../../common/samples/WebApplication1/Controllers/HomeController.cs?highlight=5&range=16-21)]

`View` 도우미 메서드에 응용 프로그램 개발자에 대 한 반환 보기 쉽게 수행할 수 있도록 몇 가지 오버 로드 합니다. 뷰에 전달할를 모델 개체와 함께 뷰를 반환 하려면 선택적으로 지정할 수 있습니다.

이 작업을 반환 하는 경우는 *About.cshtml* 위에 표시 된 뷰를 렌더링할:

![페이지 정보](overview/_static/about-page.png)

### <a name="view-discovery"></a>검색 보기

작업 뷰를 반환 하는 경우 프로세스를 호출할 *보기 검색* 이루어집니다. 이 프로세스는 뷰 파일을 사용할 것을 결정 합니다. 런타임은 컨트롤러 관련 보기 먼저 찾습니다 일치 하는 뷰 이름에 대 한 특정 보기 파일을 지정 하지 않으면는 *Shared* 폴더입니다.

작업 반환 하는 경우는 `View` 메서드를 다음과 같이 `return View();`, 뷰 이름으로 작업 이름이 사용 됩니다. 예를 들어이 값이 "Index" 라는 작업 메서드에서 호출 된 경우 "Index"의 뷰 이름 전달 같을 수 합니다. 뷰 이름을 명시적으로 메서드에 전달 될 수 있습니다 (`return View("SomeView");`). 이러한 경우 모두 보기 검색에서 일치 하는 파일의 보기를 검색합니다.

   1. 뷰 /\<ControllerName > /\<ViewName >.cshtml

   2. 뷰/공유/\<ViewName >.cshtml

>[!TIP]
> 단순히 반환 규칙 다음과 좋습니다 `View()` 수행 되므로 더 유연 하 고 코드를 리팩터링하면 쉽게 가능한 경우 작업에서 합니다.

뷰 이름 대신 뷰 파일 경로 제공할 수 있습니다. 응용 프로그램 루트에서 시작 하는 절대 경로 사용 하는 경우 (필요에 따라부터 "/" 또는 "~ /"), *.cshtml* 확장 파일 경로의 일부로 지정 해야 합니다 (예를 들어 `return View("Views/Home/About.cshtml");`). 또는 컨트롤러 해당 언어별 디렉터리 내에서 상대 경로 사용할 수는 *뷰* 서로 다른 디렉터리에 뷰를 지정 하는 디렉터리 (예를 들어 `return View("../Manage/Index");` 내는 `HomeController`). 마찬가지로, 현재 컨트롤러 관련 디렉터리를 이동할 수 있습니다 (예를 들어 `return View("./About");`). 상대 경로 사용 하지 않는 *.cshtml* 확장 합니다. 위에서 언급 한 대로 컨트롤러, 작업 및 유지 관리 및 명확성에 대 한 뷰 간의 관계를 반영 하도록 뷰에 대 한 파일 구조를 구성 하는 모범 사례를 따릅니다.

> [!NOTE]
> [부분 뷰](partial.md) 및 [구성 요소 확인](view-components.md) (비슷하지만 동일 하지 않은) 검색 메커니즘을 사용 합니다.

> [!NOTE]
> 사용자 지정을 사용 하 여 뷰 응용 프로그램 내에에 대 한 기본 규칙을 사용자 지정할 수 있습니다 `IViewLocationExpander`합니다.

>[!TIP]
> 뷰 이름은 대/소문자 구분 내부 파일 시스템에 따라 수 있습니다. 운영 체제에서 호환성을 위해 컨트롤러 및 작업 이름 및 관련된 보기 폴더와 파일 이름을 간의 대/소문자를 항상 일치 합니다.

## <a name="passing-data-to-views"></a>데이터 뷰를 전달

여러 가지 메커니즘을 사용 하 여 보기에 데이터를 전달할 수 있습니다. 지정 하는 가장 강력한 방법입니다는 *모델* 보기에서 유형 (라고 하는 *viewmodel*, 비즈니스 도메인 모델 형식 구별 하기 위해), 이러한 형식의 인스턴스 보기에 전달 된 후 동작입니다. 데이터 보기를 전달 하는 모델 또는 모델 보기를 사용 하는 것이 좋습니다. 따라서 강력한 형식 검사를 활용 하기 위해 보기 수 있습니다. 사용 하 여 보기에 대 한 모델을 지정할 수는 `@model` 지시문:

<!-- literal_block {"ids": [], "linenos": false, "xml:space": "preserve", "language": "html", "highlight_args": {"hl_lines": [1]}} -->

```html
@model WebApplication1.ViewModels.Address
   <h2>Contact</h2>
   <address>
       @Model.Street<br />
       @Model.City, @Model.State @Model.PostalCode<br />
       <abbr title="Phone">P:</abbr>
       425.555.0100
   </address>
   ```

모델 보기에 대해 지정 된 뷰에 전송 인스턴스가 사용 하 여 강력한 형식의 방식으로 액세스할 수 있습니다 `@Model` 위와 같이 합니다. 뷰에 모델 형식의 인스턴스를 제공 하려면 컨트롤러 매개 변수로 전달 합니다.

<!-- literal_block {"ids": [], "linenos": false, "xml:space": "preserve", "language": "csharp", "highlight_args": {"hl_lines": [13]}} -->

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

모델로 보기에 제공 될 수 있는 형식에 제한은 없습니다. 응용 프로그램에 비즈니스 논리를 다른 곳에서 캡슐화 될 수 있도록 거의 또는 전혀 동작을 사용 하 여 CLR 개체 POCO (Plain Old) 보기 모델을 전달 것이 좋습니다. 이 방법의 예로 *주소* viewmodel 위의 예에서 사용:

<!-- literal_block {"ids": [], "linenos": false, "xml:space": "preserve", "language": "csharp", "highlight_args": {"hl_lines": [13]}} -->

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
> 아무 것도 동일한 클래스를 사용 하 여 비즈니스 모델 유형 및 디스플레이 모델 유형에 수 없습니다. 그러나 이러한 및 별도로 유지 하거나 도메인 또는 지 속성이 모델에서 독립적으로 변경 하 여 뷰를 사용 하면 몇 가지 보안 이점을 제공할 수 있습니다 (사용자가 사용 하 여 응용 프로그램에 보내는 모델에 대 한 [모델 바인딩](../models/model-binding.md)).

### <a name="loosely-typed-data"></a>잘못 입력 한 데이터

강력한 형식의 뷰 외에도 모든 보기를 사용 하면 데이터의 느슨한 형식의 컬렉션에 권한이 있습니다. 이 컬렉션을 통해 참조 될 수 있습니다는 `ViewData` 또는 `ViewBag` 컨트롤러와 뷰는 속성입니다. `ViewBag` 속성은 한 래퍼 `ViewData` 해당 컬렉션에 대 한 동적 뷰를 제공 하는입니다. 별도 컬렉션 아닙니다.

`ViewData`통해 액세스 하는 사전 개체 `string` 키입니다. 저장 하 고, 개체를 검색할 수 있습니다 및 추출 하는 경우 특정 형식으로 캐스팅 해야 합니다. 사용할 수 있습니다 `ViewData` 뷰 (및 부분 뷰 및 레이아웃) 내에서 뿐 아니라, 보기에는 컨트롤러에서 데이터를 전달 하 합니다. 문자열 데이터를 저장 하 고 캐스트에 대 한 필요 없이 직접 사용할 수 있습니다.

에 대 한 일부 값을 설정 `ViewData` 동작에서:

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

<!-- literal_block {"ids": [], "linenos": false, "xml:space": "preserve", "language": "html", "highlight_args": {"hl_lines": [3, 6]}} -->

```html
@{
       // Requires cast
       var address = ViewData["Address"] as Address;
   }

   @ViewData["Greeting"] World!

   <address>
       @address.Name<br />
       @address.Street<br />
       @address.City, @address.State @address.PostalCode
   </address>
   ```

`ViewBag` 개체에 저장 된 개체에 대 한 동적 액세스를 제공 `ViewData`합니다. 이 더 편리 캐스팅이 필요 하지 않으므로 작업할 수 있습니다. 사용 하 여 위와 동일한 예제에서는 `ViewBag` 대신 강력한 형식의 `address` 보기에서 인스턴스:

<!-- literal_block {"ids": [], "linenos": false, "xml:space": "preserve", "language": "html", "highlight_args": {"hl_lines": [1, 4, 5, 6]}} -->

```html
@ViewBag.Greeting World!

   <address>
       @ViewBag.Address.Name<br />
       @ViewBag.Address.Street<br />
       @ViewBag.Address.City, @ViewBag.Address.State @ViewBag.Address.PostalCode
   </address>
   ```

> [!NOTE]
> 둘 다 동일한 기본를 참조 하므로 `ViewData` 컬렉션을 혼합 하는 사이 일치 `ViewData` 및 `ViewBag` 읽고 편리 하 게 하는 경우 값을 쓸 때.

### <a name="dynamic-views"></a>동적 뷰

뷰는 형식에 전달 된 모델 인스턴스를 설치 했지만 모델 형식을 선언 하지 마십시오.이 인스턴스를 동적으로 참조할 수 있습니다. 예를 들어 인스턴스의 `Address` 를 선언 하지 않습니다 하는 보기에 전달 되는 `@model`, 보기는 여전히 표시 된 것 처럼 동적으로 인스턴스 속성을 참조할 수:

<!-- literal_block {"ids": [], "linenos": false, "xml:space": "preserve", "language": "html", "highlight_args": {"hl_lines": [13, 16, 17, 18]}} -->

```html
<address>
       @Model.Street<br />
       @Model.City, @Model.State @Model.PostalCode<br />
       <abbr title="Phone">P:</abbr>
       425.555.0100
   </address>
   ```

이 기능은 일부 유연성을 제공할 수 있지만 모든 컴파일 보호 또는 IntelliSense를 제공 하지 않습니다. 속성이 존재 하지 않으면 페이지는 런타임 시 실패 합니다.

## <a name="more-view-features"></a>더 많은 보기 기능

[태그 도우미](tag-helpers/intro.md) 쉽게 서버 쪽 동작을 사용자 지정 코드 또는 도우미 보기 내에서 사용 하 여 필요 없이 기존 HTML 태그를 추가할 수 있습니다. 태그 도우미 특성으로 뷰 태그를 편집 하 고 다양 한 도구에서에서 렌더링할 수 있도록,에 익숙하지 않은 편집기에서 무시 되는 HTML 요소에 적용 됩니다. 태그 도우미는 여러 가지 그리고 허용 되는 특히 [양식 작업](working-with-forms.md) 훨씬 쉽습니다.

사용자 지정 HTML 태그를 생성 얻을 수 있는 여러 기본 제공 HTML 도우미 및 (잠재적으로 자체 데이터 요구 사항 있음)의 더 복잡 한 UI 논리에 캡슐화 할 수 [구성 요소 보기](view-components.md)합니다. 뷰 구성 요소는 컨트롤러와 뷰 제공 하는 문제의 동일한 분리를 제공 하 고 일반적인 UI 요소에서 사용 하는 데이터를 처리 하는 동작 및 뷰에 대 한 필요성 제거할 수 있습니다.

ASP.NET Core의 다른 많은 요소와 마찬가지로 뷰 지원 [종속성 주입](../../fundamentals/dependency-injection.md), 서비스 수를 허용 [뷰에 주입 된](dependency-injection.md)합니다.
