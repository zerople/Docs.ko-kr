---
title: "ASP.NET Core에서 태그 도우미"
author: rick-anderson
description: "태그 도우미 정의 및 ASP.NET 코어에서 사용 하는 방법에 알아봅니다."
keywords: "ASP.NET Core, 태그 도우미"
ms.author: riande
manager: wpickett
ms.date: 7/14/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/views/tag-helpers/intro
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 78004aa370cac8b297fd7ede534260c83965ae79
ms.sourcegitcommit: 8f4d4fad1ca27adf9e396f5c205c9875a3963664
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/13/2017
---
# <a name="introduction-to-tag-helpers-in-aspnet-core"></a>ASP.NET Core에서 태그 도우미 소개 

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)

## <a name="what-are-tag-helpers"></a>태그 도우미 이란?

태그 도우미 만들기 및 Razor 파일에서 HTML 요소 렌더링에 참여 하도록 서버 쪽 코드를 사용 하도록 설정 합니다. 예를 들어 기본 제공 `ImageTagHelper` 버전 번호를 이미지 이름에 추가할 수 있습니다. 이미지 변경 될 때마다 서버 없으므로 클라이언트 (오래 된 캐시 된 이미지) 하는 대신 현재 이미지를 가져올 보장 되는 이미지에 대 한 새로운 고유 버전을 생성 합니다. -양식, 링크, 로드 자산 및 점점-공용 GitHub 리포지토리에 및 NuGet로 훨씬 더 사용할 수 있는 패키지 만들기와 같은 일반적인 작업에 대 한 기본 제공 태그 도우미 여러 가지가 있습니다. 태그 도우미 C#에서 작성 하 고 요소 이름, 특성 이름 또는 부모 태그를 기반으로 하는 HTML 요소를 대상입니다. 예를 들어 기본 제공 `LabelTagHelper` HTML 대상으로 지정할 수 `<label>` 요소 때는 `LabelTagHelper` 특성이 적용 됩니다. 익숙한 경우 [HTML 도우미](http://stephenwalther.com/archive/2009/03/03/chapter-6-understanding-html-helpers), 태그 도우미 Razor 뷰에서 HTML 및 C# 전환을 명시적 줄입니다. 대부분의 경우에서 HTML 도우미 특정 태그 도우미에는 다른 방법은 제공 하지만 태그 도우미는 HTML 도우미를 대체 하지 않고 각 HTML 도우미에 대 한 태그 도우미 없기 인식 하는 것이 중요 합니다. [태그 도우미 HTML 도우미에 비해](#tag-helpers-compared-to-html-helpers) 자세히 차이점에 설명 합니다.

## <a name="what-tag-helpers-provide"></a>태그 도우미의 제공

**HTML에 적합 한 개발 경험이** 가장 부분 태그 도우미를 사용 하 여 Razor 태그와 동일 하 게 하는 표준 HTML입니다. HTML/CSS/JavaScript 관련 지식이 필요 프런트 엔드 디자이너 C# Razor 구문을 몰라도 Razor를 편집할 수 있습니다.

**HTML 및 Razor 태그를 만들기 위한 다양 한 IntelliSense 환경을** 이것이 HTML 도우미, 서버 쪽에서 Razor 뷰 태그 만들기는 이전 접근 방식 크게 다릅니다. [태그 도우미 HTML 도우미에 비해](#tag-helpers-compared-to-html-helpers) 자세히 차이점에 설명 합니다. [태그 도우미에 대 한 IntelliSense 지원을](#intellisense-support-for-tag-helpers) IntelliSense 환경에 설명 합니다. Razor C# 구문 익숙한에 개발자는 생산성을 높일 태그 도우미를 사용 하 여 C# Razor 태그를 작성 하는 것입니다.

**서버에만 사용할 수 있는 정보를 사용 하 여 유지 관리 가능한 코드 및 보다 강력 하 고 안정적 이며 보다 생산적이 고 생성할 수 있도록 방법은** 예를 들어 지금까지 이미지를 업데이트 하는 방법에 대 한 리소스로 변경한 경우 이미지의 이름을 변경 하려면 이미지입니다. 성능상의 이유로 이미지를 적극적으로 캐시 합니다 및 유효 하지 않은 복사본 가져오기 클라이언트 이미지의 이름을 변경 하지 않는 한 될 위험이 있습니다. 지금까지 이미지 편집 된 후 이름은 변경할 수 해야 했으며 업데이트 하는 데 필요한 웹 응용 프로그램의 이미지에 대 한 각 참조. 뿐만 아니라이 매우 많이 소비 것도 오류가 발생할 (있습니다 수 참조를 누락, 잘못 된 문자열 등을 실수로 입력) 노동 무엇입니까 기본 제공 `ImageTagHelper` 수를 자동으로 수행 합니다. `ImageTagHelper` 는 버전을 추가할 수 이미지에 대 한 고유한 새 버전에 서버 이미지 변경 될 때마다 자동으로 생성 되므로를 이미지 이름 번호입니다. 클라이언트는 현재 이미지 될 보장 됩니다. 사용 하 여 견고성과 근로 절약이 기본적으로 제공 된 `ImageTagHelper`합니다.

대부분의 기본 제공 태그 도우미 기존 HTML 요소를 대상 하 고 요소에 대 한 서버 쪽 특성을 제공 합니다. 예를 들어는 `<input>` 에서 대부분의 보기 중에서 사용 하는 요소는 *뷰/계정* 폴더에는 `asp-for` 렌더링 된 HTML로 지정 된 모델 속성의 이름을 추출 하는 특성입니다. 다음 Razor 태그:

```html
<label asp-for="Email"></label>
```

다음 HTML을 생성합니다.

```html
<label for="Email">Email</label>
```

`asp-for` 특성으로 사용할 수는 `For` 속성에는 `LabelTagHelper`합니다. 참조 [태그 도우미 제작](authoring.md) 자세한 정보에 대 한 합니다.

## <a name="managing-tag-helper-scope"></a>태그 도우미의 범위를 관리합니다.

태그 도우미 범위가의 조합에 의해 제어 됩니다 `@addTagHelper`, `@removeTagHelper`, 및 "!" 옵트아웃 문자입니다.

<a name="add-helper-label"></a>

### <a name="addtaghelper-makes-tag-helpers-available"></a>`@addTagHelper`태그 도우미를 사용할 수 있도록 설정

라는 새 ASP.NET Core 웹 앱을 만들 경우 *AuthoringTagHelpers* (인증 안 함),으로 다음 *Views/_ViewImports.cshtml* 파일을 프로젝트에 추가 됩니다:

[!code-html[Main](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImportsCopy.cshtml?highlight=2&range=2-3)]

`@addTagHelper` 지시문 태그 도우미 보기를 사용할 수 있도록 합니다. 이 경우에 파일 보기 *Views/_ViewImports.cshtml*에 있는 모든 보기 파일에 상속 되는 기본적으로는 *뷰* 폴더 및 하위 디렉터리; 태그 도우미를 사용할 수 있도록 합니다. 위의 코드에 와일드 카드 구문을 사용 하 여 ("\*") 되도록 지정 하려면 지정된 된 어셈블리의 모든 태그 도우미 (*Microsoft.AspNetCore.Mvc.TagHelpers*)의 모든 뷰 파일을 사용할 수 있습니다는 *뷰* 디렉터리 또는 하위 디렉터리입니다. 후 첫 번째 매개 변수 `@addTagHelper` 로드할 태그 도우미를 지정 합니다 (사용 하 여 "\*" 모든 태그 도우미에 대 한), 두 번째 매개 변수 "Microsoft.AspNetCore.Mvc.TagHelpers" 태그 도우미를 포함 하는 어셈블리를 지정 합니다. *Microsoft.AspNetCore.Mvc.TagHelpers* 는 어셈블리의 기본 제공 ASP.NET 핵심 태그 도우미입니다.

이 프로젝트의 태그 도우미의 모든 (이라는 어셈블리를 만드는 *AuthoringTagHelpers*), 다음을 사용 합니다.

[!code-html[Main](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImportsCopy.cshtml?highlight=3)]

프로젝트에 포함 된 경우는 `EmailTagHelper` 기본 네임 스페이스를 가진 (`AuthoringTagHelpers.TagHelpers.EmailTagHelper`), 태그 도우미의 정규화 된 이름 (FQN)를 제공할 수 있습니다.

```cshtml
@using AuthoringTagHelpers
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@addTagHelper AuthoringTagHelpers.TagHelpers.EmailTagHelper, AuthoringTagHelpers
```

태그 도우미는 FQN를 사용 하 여 뷰를 추가 하려면 먼저는 FQN가 추가 (`AuthoringTagHelpers.TagHelpers.EmailTagHelper`), 및 다음 어셈블리 이름 (*AuthoringTagHelpers*). 대부분의 개발자 선호 하는 "\*" 와일드 카드 구문을 합니다. 와일드 카드 구문을 사용 하면 와일드 카드 문자를 삽입할 수 있습니다 "\*" 접미사는 FQN의으로 합니다. 예를 들어 다음 지시문를 가져오므로 `EmailTagHelper`:

```csharp
@addTagHelper AuthoringTagHelpers.TagHelpers.E*, AuthoringTagHelpers
@addTagHelper AuthoringTagHelpers.TagHelpers.Email*, AuthoringTagHelpers
```

앞에서 언급 한 바와 같이 추가 `@addTagHelper` 지시문을 *Views/_ViewImports.cshtml* 파일을 사용 하면 태그 도우미의 모든 뷰 파일에 사용할 수는 *뷰* 디렉터리 및 하위 디렉터리입니다. 사용할 수는 `@addTagHelper` 태그 도우미는 보기에만 노출 하는 데 옵트인 하려면 특정 보기 파일에 지시문입니다.

<a name="remove-razor-directives-label"></a>

### <a name="removetaghelper-removes-tag-helpers"></a>`@removeTagHelper`태그 도우미를 제거합니다.

`@removeTagHelper` 동일한 두 개의 매개 변수를 갖는 `@addTagHelper`, 이전에 추가 하는 태그 도우미를 해제 합니다. 예를 들어 `@removeTagHelper` 보기에서 특정 보기 제거 된 지정 된 태그 도우미에 적용 합니다. 사용 하 여 `@removeTagHelper` 에 *Views/Folder/_ViewImports.cshtml* 파일에서 뷰 모두에서 지정된 된 태그 도우미 제거 *폴더*합니다.

### <a name="controlling-tag-helper-scope-with-the-viewimportscshtml-file"></a>사용 하 여 태그 도우미 범위 제어는 *_ViewImports.cshtml* 파일

추가할 수는 *_ViewImports.cshtml* 모든 보기 폴더 및 보기에 엔진은 모두 해당 파일의 지시문을 적용 및 *Views/_ViewImports.cshtml* 파일입니다. 빈을 추가한 경우 *Views/Home/_ViewImports.cshtml* 에 대 한 파일의 *홈* 뷰, 때문에 변경 되지 것는 *_ViewImports.cshtml* 파일은 가산적입니다. 모든 `@addTagHelper` 지시문에 추가 된 *Views/Home/_ViewImports.cshtml* 파일 (기본값에 없는 *Views/_ViewImports.cshtml* 파일) 보기에 해당 태그 도우미 노출에는 *홈* 폴더입니다.

<a name="opt-out"></a>

### <a name="opting-out-of-individual-elements"></a>개별 요소를 옵트아웃

태그 도우미 옵트아웃 문자로 요소 수준에서 태그 도우미를 사용 하지 않도록 설정할 수 있습니다 ("!"). 예를 들어 `Email` 유효성 검사에 사용 되지 않는지는 `<span>` 태그 도우미 옵트아웃 문자로:

```csharp
<!span asp-validation-for="Email" class="text-danger"></!span>
```

여는 태그와 닫는 태그에 태그 도우미 옵트아웃 문자를 적용 해야 합니다. (Visual Studio 편집기 자동으로 추가 옵트아웃 문자 닫는 태그를 지정 하 여는 태그를 하나 추가) 합니다. 옵트아웃 문자를 추가 하면 요소 및 태그 도우미 특성 구별 되는 글꼴에 더 이상 표시 됩니다.

<a name="prefix-razor-directives-label"></a>

### <a name="using-taghelperprefix-to-make-tag-helper-usage-explicit"></a>사용 하 여 `@tagHelperPrefix` 명시적으로 만들어야 하 태그 도우미 사용

`@tagHelperPrefix` 지시문을 사용 하면 태그 도우미 지원 기능이 사용 하 고 명시적 태그 도우미 사용을 확인 하려면 태그 접두사 문자열을 지정할 수 있습니다. 예를 들어 다음 태그를 추가할 수 있습니다는 *Views/_ViewImports.cshtml* 파일:

```html
@tagHelperPrefix th:
```
아래 코드 이미지 태그 도우미 접두사로 설정 되어 `th:`, 접두사를 사용 하는 요소에만 `th:` 태그 도우미 (태그 도우미 지원 요소에서 보유 한 고유한 글꼴)를 지원 합니다. `<label>` 및 `<input>` 요소 태그 도우미 접두사 대 한 태그 도우미를 사용할 수는 동안는 `<span>` 요소는 그렇지 않습니다.

![이미지](intro/_static/thp.png)

에 적용 되는 동일한 계층 구조 규칙 `@addTagHelper` 에 적용 `@tagHelperPrefix`합니다.

## <a name="intellisense-support-for-tag-helpers"></a>태그 도우미에 대 한 IntelliSense 지원

Visual Studio에서 새 ASP.NET 웹 앱을 만들 때 "Microsoft.AspNetCore.Razor.Tools" NuGet 패키지를 추가 합니다. 태그 도우미 도구를 추가 하는 패키지입니다.

HTML을 작성 하는 것이 좋습니다. `<label>` 요소입니다. 입력으로 `<l` Visual Studio 편집기에서 IntelliSense는 일치 하는 요소를 표시 합니다.

![이미지](intro/_static/label.png)

뿐만 아니라 HTML 도움말 아이콘 하지만 가져올 수 있습니다 (의 "@" symbol with "<>" 그 아래).

![이미지](intro/_static/tagSym.png)

태그 도우미의 대상으로 요소를 식별 합니다. 순수 HTML 요소 (예:는 `fieldset`) "<>" 아이콘을 표시 합니다.

순수 HTML `<label>` 빨간색에서 특성 갈색 글꼴로 기본 Visual Studio 색 테마) (사용 하 여 HTML 태그를 표시 하는 태그와 파란색에서 특성 값입니다.

![이미지](intro/_static/LableHtmlTag.png)

입력 한 후 `<label`, IntelliSense 사용 가능한 HTML/CSS 특성 및 태그 도우미 대상 특성을 나열 합니다.

![이미지](intro/_static/labelattr.png)

IntelliSense 문 완성을 사용 하면 선택한 값을 사용 하 여 문을 완료 하려면 tab 키를 입력할 수 있습니다.

![이미지](intro/_static/stmtcomplete.png)

입력 된 태그 도우미 특성 값으로 태그 및 특성 글꼴이 변경 합니다. 기본 Visual Studio "Blue" 또는 "Light" 색 테마를 사용 하는 글꼴은 굵은 자주입니다. "어두운" 테마를 사용 하 여 글꼴을 굵게 청록색 합니다. 이 문서에 있는 이미지는 기본 테마를 사용 하 여 수행 되었습니다.

![이미지](intro/_static/labelaspfor2.png)

Visual Studio를 입력할 수 있는 *CompleteWord* 바로 가기 (Ctrl + 스페이스바는는 [기본](https://docs.microsoft.com/visualstudio/ide/default-keyboard-shortcuts-in-visual-studio) 큰따옴표 안에 (""), 현재 위치는 C#, C# 클래스에서 것 처럼 및 합니다. IntelliSense는 페이지 모델에서 모든 메서드 및 속성을 표시합니다. 메서드 및 속성은 사용할 수 있는 속성 형식이 `ModelExpression`합니다. 아래 그림에서는 편집 중인 I는 `Register` 보기 하므로 `RegisterViewModel` ´ ï ´ 합니다.

![이미지](intro/_static/intellemail.png)

IntelliSense에는 속성과 페이지에서 모델에 사용할 수 있는 방법을 보여 줍니다. 다양 한 IntelliSense 환경을 CSS 클래스를 선택할 수 있습니다.

![이미지](intro/_static/iclass.png)

![이미지](intro/_static/intel3.png)

## <a name="tag-helpers-compared-to-html-helpers"></a>HTML 도우미에 비해 태그 도우미

태그 도우미 Razor 뷰에서 HTML 요소에 연결 하는 동안 [HTML 도우미](http://stephenwalther.com/archive/2009/03/03/chapter-6-understanding-html-helpers) 메서드 Razor 뷰에서 html 섞인 상태로 호출 됩니다. "캡션" 하는 CSS 클래스를 사용 하 여 HTML 레이블을 만듭니다 다음 Razor 태그를 고려 합니다.

```html
@Html.Label("FirstName", "First Name:", new {@class="caption"})
```

에 (`@`) 기호 이라는 Razor 코드의 시작 됩니다. 다음 두 매개 변수 ("FirstName" 및 "이름:")은 문자열 이므로 [IntelliSense](https://docs.microsoft.com/visualstudio/ide/using-intellisense) 도울 수 없습니다. 마지막 인수:

```html
new {@class="caption"}
```

익명 개체 특성을 나타내는 데 사용 됩니다. 때문에 **클래스** 예약된 된 키워드는 C#을 사용 하 여는 `@` 강제로 해석 하는 모든 C# 기호 "@class=" 기호로 (속성 이름). 프런트 엔드 디자이너로 (다른 사람이 HTML/CSS/JavaScript 및 기타 클라이언트 기술에 잘 알고 있지만 C# 및 Razor 익숙하지), 줄의 대부분은 외래 합니다. IntelliSense 활용 하지 못하는 상태로 전체 줄을 작성 해야 합니다.

사용 하 여는 `LabelTagHelper`, 동일한 태그로 작성할 수 있습니다.

![이미지](intro/_static/label2.png)

입력으로 태그 도우미 버전과 `<l` Visual Studio 편집기에서 IntelliSense는 일치 하는 요소를 표시 합니다.

![이미지](intro/_static/label.png)

IntelliSense를 사용 하면 전체 줄을 쓸 수 있습니다. `LabelTagHelper` 의 내용을 설정도 기본적으로 `asp-for` 특성 값 ("FirstName")을 "이름"; 각 새 대문자 문자 발생 공백으로 속성 이름으로 구성 하는 문장 속성 카멜식 대/소문자를 변환 합니다. 다음 태그:

![이미지](intro/_static/label2.png)

생성합니다.

```html
<label class="caption" for="FirstName">First Name</label>
```

카멜식 대 문장 대/소문자를 사용 하는 콘텐츠를 콘텐츠를 추가 하는 경우 사용 되지 않습니다는 `<label>`합니다. 예:

![이미지](intro/_static/1stName.png)

생성합니다.

```html
<label class="caption" for="FirstName">Name First</label>
```

다음 코드 이미지 폼 부분을 보여 줍니다.는 *Views/Account/Register.cshtml* 템플릿에서 생성 된 레거시 ASP.NET 4.5.x MVC Visual Studio 2015에 포함 된 Razor 보기.

![이미지](intro/_static/regCS.png)

Visual Studio 편집기에는 C# 코드가 회색 배경과 함께 표시 됩니다. 예를 들어는 `AntiForgeryToken` HTML 도우미:

```html
@Html.AntiForgeryToken()
```

회색 배경과 함께 표시 됩니다. 대부분의 레지스터 보기에서 태그는 C#입니다. 비교 하 여 태그 도우미를 사용 하 여 해당 하는 방법:

![이미지](intro/_static/regTH.png)

태그를 훨씬 쉽고 명확한 읽기, 편집 및 HTML 도우미 방식에 비해 유지 관리 됩니다. C# 코드는 서버에 대해 알고 있어야 하는 최소 수준으로 줄어듭니다. Visual Studio 편집기에는 고유한 글꼴로 태그 도우미의 대상 태그가 표시 됩니다.

고려는 *전자 메일* 그룹:

[!code-csharp[Main](intro/sample/Register.cshtml?range=12-18)]

각 "asp-" 특성을 "Email"의 값 갖지만 "Email" 문자열이 아닙니다. 이 컨텍스트에서 "Email"는 C# 모델 식 속성에 대 한는 `RegisterViewModel`합니다.

Visual Studio 편집기를 사용 하면 작성 **모든** Visual Studio에서는 대부분의 HTML 도우미 접근 방식에서 코드에 대 한 도움말을 제공 하는 동안 레지스터 폼의 태그 도우미 접근 방식에서 변경 내용입니다. [태그 도우미에 대 한 IntelliSense 지원을](#intellisense-support-for-tag-helpers) Visual Studio 편집기에서 태그 도우미 작업 정보를 확인할 이동 합니다.

## <a name="tag-helpers-compared-to-web-server-controls"></a>웹 서버 컨트롤에 비해 태그 도우미

* 태그 도우미;와 연결 하는 요소를 소유 하지 않는 단순히 요소 및 콘텐츠 렌더링에 참여 합니다. ASP.NET [웹 서버 컨트롤](https://msdn.microsoft.com/library/7698y1f0.aspx) 선언 되 고 페이지에 호출 됩니다.

* [웹 서버 컨트롤의](https://msdn.microsoft.com/library/zsyt68f1.aspx) 특수 주기를 어렵게 만들 수 있는 개발 및 디버깅 합니다.

* 웹 서버 컨트롤을 사용 하는 클라이언트 컨트롤을 사용 하 여 클라이언트 문서 개체 모델 (DOM) 요소에 기능을 추가할 수 있습니다. 태그 도우미가 없는 DOM.

* 웹 서버 컨트롤 브라우저 자동 검색을 포함 합니다. 태그 도우미 브라우저에 모를 합니다.

* 같은 요소에 여러 태그 도우미 작동할 수 있습니다 (참조 [태그 도우미 방지 충돌](https://docs.microsoft.com/aspnet/core/mvc/views/tag-helpers/authoring#avoiding-tag-helper-conflicts) ) 동안 일반적으로 웹 서버 컨트롤을 구성할 수 없습니다.

* 태그 도우미의 태그와, 범위가 지정 하는 HTML 요소의 콘텐츠를 수정할 수 있지만 페이지에 다른 항목이 직접 수정 하지 마세요. 웹 서버 컨트롤은 덜 구체적인 범위가; 페이지의 다른 부분에 영향을 주는 작업을 수행할 수 있으며 의도 하지 않은 부작용을 사용 하도록 설정 합니다.

* 형식 변환기를 사용 하 여 문자열을 개체로 변환 하는 웹 서버 컨트롤. 태그 도우미와에서 작업 하면서 기본적으로 C#, 형식 변환 수행 필요가 없습니다.

* 웹 서버 사용을 제어 [System.ComponentModel](https://docs.microsoft.com/dotnet/api/system.componentmodel) 구성 요소와 컨트롤의 런타임 및 디자인 타임 동작을 구현 합니다. `System.ComponentModel`기본 클래스와 특성 및 형식 변환기 구현, 소스 데이터에 바인딩, 및 구성 요소 라이선스에 대 한 인터페이스를 포함 합니다. 일반적으로 파생 되는 태그 도우미 즉 `TagHelper`, 및 `TagHelper` 두 개의 메서드를 노출 하는 기본 클래스 `Process` 및 `ProcessAsync`합니다.

## <a name="customizing-the-tag-helper-element-font"></a>태그 도우미 요소 글꼴을 사용자 지정

글꼴 및 색 지정에서 사용자 지정할 수 있습니다 **도구** > **옵션** > **환경** > **글꼴 및 색**:

![이미지](intro/_static/fontoptions2.png)

## <a name="additional-resources"></a>추가 리소스

* [태그 도우미 작성](authoring.md)
* [양식 사용](../working-with-forms.md)
* [GitHub의 TagHelperSamples](https://github.com/dpaquette/TagHelperSamples) 사용 하기 위한 태그 도우미 샘플이 포함 되어 [부트스트랩](http://getbootstrap.com/)합니다.

<!--
* [Working with Forms ](xref:mvc/views/working-with-forms)
-->
