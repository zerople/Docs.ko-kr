---
title: "개요 보기 | Microsoft 문서"
author: ardalis
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 668c320d-c050-45e3-8161-2f460dc93b2f
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/views/overview
translationtype: Machine Translation
ms.sourcegitcommit: 010b730d2716f9f536fef889bc2f767afb648ef4
ms.openlocfilehash: 32ded2e3a3b15c9ad7450703205f3728fcb63272
ms.lasthandoff: 03/23/2017

---
# <a name="rendering-html-with-views-in-aspnet-core-mvc"></a>ASP.NET 핵심 mvc에서 뷰가 포함 된 HTML 렌더링

[Steve Smith](http://ardalis.com)

ASP.NET MVC 코어 컨트롤러를 사용 하 여 결과 서식이 지정 된 반환할 수 있습니다 *뷰*합니다.

## <a name="what-are-views"></a>뷰는 무엇입니까?

모델-뷰-컨트롤러 (MVC) 패턴에는 *보기* 응용 프로그램 사용자의 상호 작용의 프레젠테이션 세부 정보를 캡슐화 합니다. 뷰는 클라이언트에 보내도록 콘텐츠를 생성 하는 포함 된 코드를 사용 하 여 HTML 템플릿입니다. 사용 하 여 뷰 [Razor 구문](razor.md), 코드를 최소한의 코드 또는 의례 HTML 상호 작용할 수 있습니다.

ASP.NET 핵심 MVC 뷰는 *.cshtml* 에서 기본적으로 저장 된 파일을 *뷰* 응용 프로그램 내의 폴더입니다. 일반적으로 각 컨트롤러에 있는 특정 컨트롤러 작업에 대 한 뷰는 고유한 폴더에 포함 됩니다.

![솔루션 탐색기에서 views 폴더](overview/_static/views_solution_explorer.png)

작업별 뷰 외에도 [부분 뷰](partial.md), [레이아웃 및 기타 특수 보기 파일](layout.md) 반복을 절감 하 고 응용 프로그램의 보기 내에서 다시 사용할 수 있으며 데 사용할 수 있습니다.

## <a name="benefits-of-using-views"></a>뷰를 사용할 경우의 이점

뷰를 제공 [중요 한 부분의 분리](http://deviq.com/separation-of-concerns/) 내 MVC 응용 프로그램 사용자 인터페이스 수준 태그가을 비즈니스 논리를 캡슐화 합니다. ASP.NET MVC 뷰 사용 [Razor 구문을](razor.md) HTML 태그 및 서버 쪽 논리 간편한 전환할 수 있습니다. 응용 프로그램의 사용자 인터페이스의 반복 되는 일반적인 요소를 사용 하 여 뷰 간에 쉽게 다시 사용할 수 [레이아웃 및 공유 지시문](layout.md) 또는 [부분 뷰](partial.md)합니다.

## <a name="creating-a-view"></a>뷰 만들기

컨트롤러에 관련 된에서 생성 하는 *보기 / [ControllerName]* 폴더입니다. 컨트롤러 간에 공유 되는 뷰에 배치 되는 */뷰/공유* 폴더입니다. 뷰 파일을 연결 된 컨트롤러 작업을 같은 이름을 지정 하 고 추가 된 *.cshtml* 파일 확장명입니다. 예를 들어에 대 한 보기를 만들 수는 *에 대 한* 작업에는 *홈* 컨트롤러를 만들 수 있습니다는 *About.cshtml* 파일에 */뷰/홈* 폴더.

샘플 보기 파일 (*About.cshtml*):

[!code-html[주](../../common/samples/WebApplication1/Views/Home/About.cshtml)]

*Razor* 코드도 표시 되는 `@` 기호입니다. C# 문을 Razor가 중괄호 안의 해제 코드 블록 내에서 실행 됩니다 (`{` `}`), About 할당과 같은 하는 `ViewData["Title"]` 위에 표시 된 요소입니다. Razor HTML 내에 있는 값을 사용 하 여 값을 참조 하 여 표시를 사용할 수는 `@` 내에서 표시 된 것 처럼 기호는 `<h2>` 및 `<h3>` 위의 요소.

이 보기를 담당 하 고 출력의 부분에만 집중 합니다. 나머지 페이지의 레이아웃 및 기타 일반적인 측면 보기의 다른 위치에 지정 됩니다. 에 대 한 자세한 내용은 [레이아웃 및 공유 보기 논리](layout.md)합니다.

## <a name="how-do-controllers-specify-views"></a>보기를 지정 하는 컨트롤러를 작업 하는 방법

뷰 작업으로 인해 일반적으로 반환 되는 `ViewResult`합니다. 작업 메서드를 만들어 반환는 `ViewResult` 직접, 하지만 일반적으로 컨트롤러에서 상속 하는 경우 `Controller`, 사용 하 여 단순히는 `View` 도우미 메서드를이 예제로 보여 줍니다.

*HomeController.cs*

[!code-csharp[주](../../common/samples/WebApplication1/Controllers/HomeController.cs?highlight=5&range=16-21)]

`View` 도우미 메서드는 앱 개발자를 위한 반환 보기 쉽게 수행할 수 있도록 몇 가지 오버 로드가 있습니다. 필요에 따라 반환할 보기 뿐만 아니라 보기를 전달 하는 모델 개체를 지정할 수 있습니다.

이 작업을 반환 하는 경우는 *About.cshtml* 위에 표시 된 보기에서 렌더링 됩니다.

![페이지 정보](overview/_static/about-page.png)

### <a name="view-discovery"></a>보기 검색

뷰를 반환 하는 작업, 프로세스 호출 *보기 검색* 이루어집니다. 이 프로세스는 뷰 파일을 사용할 결정 합니다. 런타임은 컨트롤러 전용 뷰, 다음을 먼저 찾아보고 일치 하는 뷰 이름에 대 한 특정 뷰 파일을 지정 하지 않으면는 *공유* 폴더입니다.

작업 반환 하는 경우는 `View` 메서드를 다음과 같이 `return View();`, 작업 이름이 뷰 이름으로 사용 됩니다. 예를 들어이 "Index" 라는 작업 메서드에서 호출 된 것에 해당 뷰 이름 "Index"를 전달 하입니다. 뷰 이름을 명시적으로 메서드에 전달 될 수 있습니다 (`return View("SomeView");`). 이러한 경우 모두 보기 검색에서 일치 하는 파일의 보기를 검색합니다.

   1. Views/<ControllerName>/<ViewName>.cshtml

   2. 뷰/공유/<ViewName>.cshtml

>[!TIP]
> 다음의 단순히 반환 규칙을 권장 `View()` 수행 되므로 더 유연 하 고 코드를 리팩터링하면 쉽게 가능한 경우 작업에서.

뷰 이름 대신 뷰 파일 경로 제공할 수 있습니다. 이 경우에 *.cshtml* 확장 파일 경로의 일부로 지정 해야 합니다. 응용 프로그램 루트에 상대적인 경로 여야 합니다 (및 필요에 따라 시작할 수 있습니다 "/" 또는 "~ /"). 예를 들면 `return View("Views/Home/About.cshtml");` 같은 형식입니다.

> [!NOTE]
> [이 기능은 부분 뷰](partial.md) 및 [구성 요소를 볼](view-components.md) (비슷하지만 동일 하지는 않습니다) 검색 메커니즘을 사용 합니다.

> [!NOTE]
> 사용자 지정을 사용 하 여 뷰는 응용 프로그램 내에 위치 하는 것에 대 한 기본 규칙을 사용자 지정할 수 있습니다 `IViewLocationExpander`합니다.

>[!TIP]
> 뷰 이름은 대/소문자 구분 내부 파일 시스템에 따라 수 있습니다. 운영 체제 간에 호환성을 위해 컨트롤러 및 작업 이름 및 관련된 보기 폴더와 파일 이름 간의 경우 항상 일치 합니다.

## <a name="passing-data-to-views"></a>보기에 데이터 전달

몇 가지 메커니즘을 사용 하 여 뷰로 데이터를 전달할 수 있습니다. 지정 하는 가장 강력한 방법입니다는 *모델* 보기에는 형식 (일반적으로 *viewmodel*, 비즈니스 도메인 모델 형식을 구분 하기 위해), 동작에서 보기에이 형식의 인스턴스를 전달 합니다. 데이터 보기를 전달 하는 모델 또는 뷰 모델을 사용 하는 것이 좋습니다. 따라서 강력한 형식 검사를 활용 하기 위해 보기 수 있습니다. 모델을 사용 하 여 뷰를 지정할 수는 `@model` 지시문:

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

모델 보기에 대해 지정 된 뷰에 전송 인스턴스가 사용 하 여 강력한 형식을 액세스할 수 있습니다 `@Model` 위와 같이 합니다. 뷰와 모델 형식의 인스턴스를 제공 하려면 컨트롤러를 매개 변수로 전달 합니다.

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

모델로 보기를 제공할 수 있는 형식에 대해 제한은 없습니다. 비즈니스 논리 앱에 다른 곳에서 캡슐화 될 수 있도록 거의 없거나 전혀 없이 동작을 사용 하 여 CLR 개체 POCO (Plain Old) 보기 모델 전달 것이 좋습니다. 이 방법의 예로 *주소* 위의 예제에서 사용 하는 viewmodel:

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
> 아무 것도 동일한 클래스를 사용 하 여 비즈니스 모델 형식과 디스플레이 모델 형식으로 수 없습니다. 그러나 게 별도 상태를 유지 도메인 또는 지 속성 모델에서 독립적으로 변경 하 여 뷰를 허용 하 고 몇 가지 보안 이점을 제공할 수 있습니다 (사용자가 사용 하 여 응용 프로그램으로 전송할 모델에 대 한 [모델 바인딩](../models/model-binding.md)).

### <a name="loosely-typed-data"></a>느슨한 형식의 데이터

강력한 형식의 뷰 외에도 모든 보기에 액세스를 권한이 느슨한 형식의 데이터 컬렉션입니다. 이 컬렉션을 통해 참조 될 수 있습니다는 `ViewData` 또는 `ViewBag` 컨트롤러 및 보기에는 속성입니다. `ViewBag` 속성은 래퍼 `ViewData` 해당 컬렉션에 대 한 동적 뷰를 제공 하는 합니다. 별도 컬렉션을 아닙니다.

`ViewData`사전 개체를 통해 액세스 `string` 키입니다. 저장 하 고, 개체를 검색할 수를 추출 하면 특정 형식으로 캐스팅 해야 합니다. 사용할 수 있습니다 `ViewData` 데이터를 전달 하는 컨트롤러 및 보기 (및 부분 뷰 및 레이아웃) 내에서 보기에 있습니다. 문자열 데이터를 저장 하 고 캐스트에 대 한 필요 없이 직접 사용할 수 있습니다.

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

뷰에서 데이터를 사용 합니다.

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

`ViewBag` 개체에 저장 된 개체에 대 한 동적 액세스를 제공 합니다. `ViewData`합니다. 이 캐스팅 있지 않아도 되므로 사용 하는 편리한 방법을 수 있습니다. 동일한 예제를 사용 하 여 위에서 `ViewBag` 대신 강력한 형식의 `address` 보기에서 인스턴스:

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
> 모두 동일한 기본를 참조 하므로 `ViewData` 컬렉션 수를 섞어 간의 `ViewData` 및 `ViewBag` 읽고 편리 하 게 하는 경우 값을 쓸 때.

### <a name="dynamic-views"></a>동적 뷰

모델 유형을 선언 하지 마십시오. 하지만 모델 인스턴스를 전달 하는 뷰는이 인스턴스를 동적으로 참조할 수 있습니다. 예를 들어 인스턴스의 `Address` 는 선언 하지 않습니다 뷰로 전달는 `@model`, 보기는 여전히 표시 된 것 처럼 동적으로 인스턴스 속성을 참조할 수 있습니다.

<!-- literal_block {"ids": [], "linenos": false, "xml:space": "preserve", "language": "html", "highlight_args": {"hl_lines": [13, 16, 17, 18]}} -->

```html
<address>
       @Model.Street<br />
       @Model.City, @Model.State @Model.PostalCode<br />
       <abbr title="Phone">P:</abbr>
       425.555.0100
   </address>
   ```

이 기능은 일부 유연성을 제공할 수 있지만 모든 컴파일 보호 또는 IntelliSense를 제공 하지 않습니다. 속성이 존재 하지 않으면 페이지는 런타임에 실패 합니다.

## <a name="more-view-features"></a>더 많은 보기 기능

[태그 도우미](tag-helpers/intro.md) 쉽게 서버 쪽 동작을 사용자 지정 코드 또는 보기 내에서 도우미를 사용 하도록 할 필요가 없으므로 기존 HTML 태그를 추가할 수 있습니다. 태그 도우미 특성으로 뷰 태그를 편집 하 고 다양 한 도구에서에서 렌더링 될 수 있도록, 익숙하지 편집기에서 무시 되는 HTML 요소에 적용 됩니다. 태그 도우미 여러 용도로 사용 하 고 특히 수 [양식 작업](working-with-forms.md) 훨씬 쉽습니다.

사용자 지정 HTML 태그 생성 얻을 수 있는 많은 기본 제공 [HTML 도우미](html-helpers.md), 더 복잡 한 UI 논리 (공존할 수 있는 자체 데이터 요구 사항)에 캡슐화 할 수 및 [뷰 구성 요소](view-components.md)합니다. 뷰 구성 요소는 컨트롤러와 뷰에서 제공 하는 중요 한 부분의 동일한 분리를 제공 하 고 필요가 동작 및 뷰에 대 한 일반적인 UI 요소에 의해 사용 되는 데이터 처리를 제거할 수 있습니다.

ASP.NET 코어의 다른 여러 측면과 마찬가지로 뷰 지원 [종속성 주입](../../fundamentals/dependency-injection.md), 서비스를 사용할 수 있도록 [보기에 주입](dependency-injection.md)합니다.

