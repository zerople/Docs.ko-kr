---
title: "ASP.NET Core에 대 한 읽기 권한만 태그 | Microsoft 문서"
author: rick-anderson
description: "태그 도우미 정의 및 ASP.NET 코어에서 사용 하는 방법에 알아봅니다."
keywords: "ASP.NET Core, 태그 도우미"
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 7a47f0b9-dd1e-4d25-97ec-2346a5d3ce2c
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/views/tag-helpers/intro
ms.custom: H1Hack27Feb2017
translationtype: Machine Translation
ms.sourcegitcommit: 8ab055f5cfb259486949b5d8d01dafe07301398a
ms.openlocfilehash: 65dbb7fe2380550d1b60575b2d26cdce9b57260e
ms.lasthandoff: 03/23/2017

---
# <a name="introduction-to-tag-helpers-in-aspnet-core"></a>ASP.NET Core에서 태그 도우미 소개 

[Rick Anderson](https://twitter.com/RickAndMSFT)

## <a name="what-are-tag-helpers"></a>태그 도우미 무엇입니까?

태그 도우미 만들기 및 Razor 파일의 요소를 HTML 렌더링에 참여 하는 서버 쪽 코드를 사용 합니다. 예를 들어 기본 제공 `ImageTagHelper` 이미지 이름에 버전 번호를 추가할 수 있습니다. 이미지 변경 될 때마다 서버 없으므로 클라이언트는 오래 된 캐시 된 이미지 대신 현재 이미지를 가져올 보장 되는 이미지에 대 한 새 고유 버전을 생성 합니다. 양식, 링크, 로드 자산, 점점-더욱 NuGet와 공용 GitHub 리포지토리에 사용할 수 있는 패키지를 만드는 것과 같은 일반적인 작업-에 대 한 기본 제공 태그 도우미 여러 가지가 있습니다. 태그 도우미 C#으로 작성 되며 및 요소 이름, 특성 이름 또는 부모 태그를 기반으로 하는 HTML 요소를 대상입니다. 예를 들어 기본 제공 `LabelTagHelper` HTML을 대상으로 지정할 수 `<label>` 요소 때는 `LabelTagHelper` 특성이 적용 됩니다. 익숙한 경우 [HTML 도우미](http://stephenwalther.com/archive/2009/03/03/chapter-6-understanding-html-helpers), 태그 도우미 줄일 Razor 뷰에서 HTML 및 C# 간의 명시적 전환 합니다. [태그를 HTML 도우미에 비해 도우미](#tag-helpers-compared-to-html-helpers) 자세히 차이점에 설명 합니다.

## <a name="what-tag-helpers-provide"></a>태그 도우미에서 제공 하는 것

**HTML에 게 친숙 한 개발 환경을** 
 가장 많은 부분에 대 한 태그 도우미를 사용 하 여 Razor 태그 같습니다 표준 HTML입니다. 프런트 엔드 디자이너 HTML/CSS/JavaScript와 함께 관련 지식이 필요 C# Razor 구문을 몰라도 Razor를 편집할 수 있습니다.

**HTML 및 Razor 태그를 만들기 위한 다양 한 IntelliSense 환경을** 
 이것은 Razor 뷰에서 태그의 서버 쪽 만들기에 대 한 이전 접근 방법, HTML 도우미를 대조를 이룹니다. [태그를 HTML 도우미에 비해 도우미](#tag-helpers-compared-to-html-helpers) 자세히 차이점에 설명 합니다. [태그 도우미에 대 한 IntelliSense 지원](#intellisense-support-for-tag-helpers) IntelliSense 환경에 설명 합니다. Razor C# 구문을 사용 하 여 발생에 개발자는 생산성을 높일 태그 도우미를 사용 하 여 C# Razor 태그를 작성 하는 것입니다.

**서버에만 사용할 수 있는 정보를 사용 하 여 유지 관리 가능한 코드 및 보다 강력 하 고 안정적 이며 더욱 생산적이 고 생성할 수 있도록 방법은** 
 예를 들어 지금까지 자리잡았지만에서 이미지를 업데이트 하는 것 이었습니다 이미지를 변경 하는 경우 이미지의 이름을 변경 합니다. 성능상의 이유로 이미지를 적극적으로 캐시 해야 하 고 오래 된 복사본 가져오기 클라이언트 이미지의 이름을 변경 하지 않는 한 될 위험이 있습니다. 지금까지 이미지 편집 된 후 이름을 변경 해야 했으며 업데이트 하는 데 필요한 웹 응용 프로그램의 이미지에 대 한 각 참조. 뿐만 아니라 서 표시 되는 매우 노동 소모 것도 오류가 발생할 (있습니다 수 참조를 누락, 실수로 잘못 된 문자열 등을 입력 합니다.) 기본 제공 `ImageTagHelper` 수를 자동으로 수행 합니다. `ImageTagHelper` 버전을 추가할 수 이미지 변경 될 때마다 서버 이미지에 대 한 새 고유 버전을 자동으로 생성 되므로 이미지 이름에 숫자입니다. 클라이언트는 현재 이미지를 가져올 보장 됩니다. 사용 하 여 견고성 및 노동 절감이 기본적으로 무료 제공에서 `ImageTagHelper`합니다.

대부분의 기본 제공 태그 도우미 기존 HTML 요소를 대상으로 하 고 요소에 대 한 서버 쪽 특성을 제공 합니다. 예를 들어는 `<input>` 에서의 뷰 대부분에서 사용 하는 요소는 *Views/Account* 폴더에 포함 되어는 `asp-for` 렌더링 된 HTML로 지정 된 모델 속성의 이름을 추출 하는 특성입니다. 다음과 같은 Razor 태그:

```html
<label asp-for="Email"></label>
   ```

다음 HTML을 생성합니다.

```html
<label for="Email">Email</label>
   ```

`asp-for` 특성에서 사용할 수는 `For` 속성에는 `LabelTagHelper`합니다. 참조 [태그 도우미 제작](authoring.md) 에 대 한 자세한 내용은 합니다.

## <a name="managing-tag-helper-scope"></a>태그 도우미 범위를 관리합니다.

태그 도우미 범위가의 조합에 의해 제어 됩니다 `@addTagHelper`, `@removeTagHelper`, 및 "!" 옵트아웃 문자입니다.

<a name=add-helper-label></a>

### <a name="addtaghelper-makes-tag-helpers-available"></a>`@addTagHelper`태그 도우미를 사용할 수 있도록

이라는 새 ASP.NET 핵심 웹 앱을 만들 경우 *AuthoringTagHelpers* (인증 없음)와 다음 *Views/_ViewImports.cshtml* 파일이 프로젝트에 추가 됩니다.

[!code-html[주](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImportsCopy.cshtml?highlight=2&range=2-3)]

`@addTagHelper` 지시문 태그 도우미 보기를 사용할 수 있도록 합니다. 뷰 파일은이 경우 *Views/_ViewImports.cshtml*, 파일의 모든 보기에 상속 되는 기본적으로는 *뷰* 폴더 및 하위 디렉터리; 태그 도우미를 사용할 수 있도록 합니다. 위의 코드에는 와일드 카드 구문을 사용 하 여 ("\*") 되도록 지정 하려면 지정된 된 어셈블리의 모든 태그 도우미 (*Microsoft.AspNetCore.Mvc.TagHelpers*) 모든 보기 파일에 사용할 수는 *뷰* 디렉터리나 하위 디렉터리입니다. 후 첫 번째 매개 변수 `@addTagHelper` 로드할 태그 도우미를 지정 합니다 (사용 하 여 "\*" 모든 태그 도우미에 대 한), 두 번째 매개 변수 "Microsoft.AspNetCore.Mvc.TagHelpers" 태그 도우미를 포함 하는 어셈블리를 지정 합니다. *Microsoft.AspNetCore.Mvc.TagHelpers* 기본 제공 ASP.NET 핵심 태그 도우미에 대 한 어셈블리입니다.

이 프로젝트의 태그 도우미의 모든 (이라는 어셈블리 만드는 *AuthoringTagHelpers*), 다음을 사용 합니다.

[!code-html[주](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImportsCopy.cshtml?highlight=3)]

프로젝트에 포함 된 경우는 `EmailTagHelper` 기본 네임 스페이스 (`AuthoringTagHelpers.TagHelpers.EmailTagHelper`), 태그 도우미의 정규화 된 이름 (FQN)를 제공할 수 있습니다.

<!-- literal_block {"ids": [], "linenos": false, "xml:space": "preserve", "language": "html", "highlight_args": {"hl_lines": [3]}} -->

```html
@using AuthoringTagHelpers
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@addTagHelper "AuthoringTagHelpers.TagHelpers.EmailTagHelper, AuthoringTagHelpers"
```

태그 도우미에는 FQN를 사용 하 여 뷰를 추가 하려면 먼저는 FQN가 추가 (`AuthoringTagHelpers.TagHelpers.EmailTagHelper`), 어셈블리 이름을 (*AuthoringTagHelpers*). 대부분의 개발자가 선호 하는 "\*" 와일드 카드 구문입니다. 와일드 카드 구문을 삽입할 수 있도록 와일드 카드 문자 "\*" 접미사는 FQN의으로 합니다. 예를 들어 다음 지시문 중 하나를 가져오므로 `EmailTagHelper`:

```csharp
@addTagHelper "AuthoringTagHelpers.TagHelpers.E*, AuthoringTagHelpers"
@addTagHelper "AuthoringTagHelpers.TagHelpers.Email*, AuthoringTagHelpers"
```

앞에서 설명한 바와 같이 추가 `@addTagHelper` 지시문을 *Views/_ViewImports.cshtml* 파일을 사용 하면 태그 도우미의 모든 보기 파일에 사용할 수는 *뷰* 디렉터리 및 하위 디렉터리입니다. 사용할 수는 `@addTagHelper` 지시문에 참여 하는 보기에만 태그 도우미를 노출 하는 데 사용 하려는 경우에 파일을 특정 보기에 있습니다.

<a name=remove-razor-directives-label></a>

### <a name="removetaghelper-removes-tag-helpers"></a>`@removeTagHelper`태그 도우미를 제거합니다.

`@removeTagHelper` 동일한 두 개의 매개 변수를 갖는 `@addTagHelper`, 하 고 이전에 추가 된 태그 도우미를 제거 합니다. 예를 들어 `@removeTagHelper` 보기에서 특정 보기 제거 된 지정 된 태그 도우미에 적용 합니다. 사용 하 여 `@removeTagHelper` 에 *Views/Folder/_ViewImports.cshtml* 파일에서 뷰를 모두에서 지정된 된 태그 도우미를 제거 *폴더*합니다.

### <a name="controlling-tag-helper-scope-with-the-viewimportscshtml-file"></a>태그 도우미와 범위를 제어는 *_ViewImports.cshtml* 파일

추가할 수는 *_ViewImports.cshtml* 지시문 구현과 엔진이 모든 보기 폴더 및 보기에 추가 *_ViewImports.cshtml* 파일에 포함 된는 *Views/_ViewImports.cshtml* 파일. 빈을 추가한 경우 *Views/Home/_ViewImports.cshtml* 파일에 대 한는 *홈* 뷰, 때문에 변경 내용이 없음을 것는 *_ViewImports.cshtml* 파일은 누적 합니다. 모든 `@addTagHelper` 지시문에 추가 *Views/Home/_ViewImports.cshtml* 파일 (기본값에는 없는 *Views/_ViewImports.cshtml* 파일) 보기에 해당 태그 도우미를 노출 합니다 에서만 *홈* 폴더입니다.

### <a name="opting-out-of-individual-elements"></a>개별 요소를 옵트아웃

태그 도우미 옵트아웃 문자로 요소 수준에서 태그 도우미를 비활성화할 수 있습니다 ("!"). 예를 들어 `Email` 유효성 검사에 사용 되지 않는지는 `<span>` 태그 도우미 옵트아웃 문자:

```csharp
<!span asp-validation-for="Email" class="text-danger"></!span>
   ```

태그 도우미 옵트아웃 문자는 태그와 닫는 태그를 적용 해야 합니다. (Visual Studio 편집기 자동으로 추가 옵트아웃 문자 닫는 태그에 여는 태그를 추가 하는 경우). (Opt out) 문자를 추가 하면 요소 및 태그 도우미 특성 구별 되는 글꼴에 더 이상 표시 됩니다.

<a name=prefix-razor-directives-label></a>

### <a name="using-taghelperprefix-to-make-tag-helper-usage-explicit"></a>사용 하 여 `@tagHelperPrefix` 명시적으로 만들어야 하 태그 도우미 사용

`@tagHelperPrefix` 지시문을 사용 하면 태그 도우미 지원을 사용 하도록 설정 하 고 사용이 태그 도우미를 명시적으로 만들어야 하는 태그 접두사 문자열을 지정할 수 있습니다. 아래 코드 이미지 태그 도우미 접두사로 설정 됩니다 `th:`, 접두사를 사용 하는 요소에만 `th:` 태그 도우미 (태그 도우미 사용이 가능한 요소는 고유한 글꼴 있음)를 지원 합니다. `<label>` 및 `<input>` 요소 태그 도우미 접두사 및 태그 도우미를 사용할 수는 동안는 `<span>` 요소는 그렇지 않습니다.

![이미지](intro/_static/thp.png)

에 적용 되는 동일한 계층 구조 규칙 `@addTagHelper` 에 적용 `@tagHelperPrefix`합니다.

## <a name="intellisense-support-for-tag-helpers"></a>태그 도우미에 대 한 IntelliSense 지원

Visual Studio에서 새 ASP.NET 웹 응용 프로그램을 만들 때 "Microsoft.AspNetCore.Razor.Tools" NuGet 패키지를 추가 합니다. 태그 도우미 도구를 추가 하는 패키지입니다.

HTML을 직접 작성해 `<label>` 요소입니다. 입력 하는 즉시 `<l` Visual Studio 편집기에서 IntelliSense는 일치 하는 요소를 표시 합니다.

![이미지](intro/_static/label.png)

뿐만 아니라 HTML 도움말 아이콘 하지만 가져올 수 있습니다 (에서 "@" 기호를 "<>" 그 아래).

![이미지](intro/_static/tagSym.png)

태그 도우미의 대상으로 요소를 식별 합니다. 순수 HTML 요소 (예:는 `fieldset`) 표시는 "<>" 아이콘입니다.

순수 HTML `<label>` 빨간색으로 특성 갈색 글꼴로 기본 Visual Studio의 색 테마) (사용 하 여 HTML 태그를 표시 하는 태그와 파란색에서 특성 값입니다.

![이미지](intro/_static/LableHtmlTag.png)

입력 한 후 `<label`, IntelliSense에는 사용할 수 있는 HTML/CSS 특성 및 태그 도우미 대상 특성 목록을 표시 합니다.

![이미지](intro/_static/labelattr.png)

IntelliSense 문 완성을 사용 하면 선택한 값을 사용 하 여 문을 완료 하려면 tab 키를 입력할 수 있습니다.

![이미지](intro/_static/stmtcomplete.png)

태그 도우미 특성을 입력 하는 즉시 태그 및 특성 글꼴 변경 합니다. 기본 Visual Studio "Blue" 또는 "Light" 색 테마를 사용 하는 글꼴은 굵은 자주입니다. "어두운" 테마를 사용 하는 경우 글꼴에 굵게 표시 된 청록색입니다. 이 문서에 있는 이미지의 기본 테마를 사용 하 여 수행 되었습니다.

![이미지](intro/_static/labelaspfor2.png)

Visual Studio를 입력할 수 있는 *CompleteWord* 바로 가기 (Ctrl + 스페이스바는는 [기본](https://msdn.microsoft.com/en-us/library/da5kh0wa.aspx)) 큰따옴표 안에 (""), 그리고 사용자가 이제 C#, C# 클래스에서 것 처럼. IntelliSense는 페이지 모델에서 모든 메서드 및 속성을 표시합니다. 메서드 및 속성은 사용할 수 있는 속성 형식이 `ModelExpression`합니다. 아래 이미지에서 편집 중인 필자는 `Register` 보기 이므로 `RegisterViewModel` 를 사용할 수 있습니다.

![이미지](intro/_static/intellemail.png)

IntelliSense에는 속성과 페이지에서 모델에 사용할 수 있는 방법을 보여 줍니다. 풍부한 IntelliSense 환경을 CSS 클래스를 선택할 수 있습니다.

![이미지](intro/_static/iclass.png)

![이미지](intro/_static/intel3.png)

## <a name="tag-helpers-compared-to-html-helpers"></a>HTML 도우미에 비해 태그 도우미

태그 도우미 Razor 뷰에서 HTML 요소에 연결 하는 동안 [HTML 도우미](http://stephenwalther.com/archive/2009/03/03/chapter-6-understanding-html-helpers) Razor 뷰에서 HTML 섞여 메서드가 호출 됩니다. "캡션" CSS 클래스를 사용 하 여 HTML 레이블을 만듭니다 다음 Razor 태그에서 고려해 야 합니다.

```html
@Html.Label("FirstName", "First Name:", new {@class="caption"})
   ```

(`@`)에서는 기호 Razor 코드의 시작입니다. 다음 두 매개 변수 ("FirstName" 및 "이름:")는 문자열, 따라서 [IntelliSense](https://msdn.microsoft.com/en-us/library/hcw1s69b.aspx) 도와 드릴 수 없습니다. 마지막 인수:

```html
new {@class="caption"}
   ```

익명 개체 특성을 나타내는 데 사용 됩니다. 때문에 **클래스** 예약된 된 키워드는 C#을 사용 하 여는 `@` 강제로 해석 하는 모든 C# 기호 "@class=" 기호로 (속성 이름). 프런트 엔드 디자이너로 (누군가가 HTML/CSS/JavaScript 및 기타 클라이언트 기술에 잘 알고 있지만 C# 및 Razor에 익숙하지 않는), 줄의 대부분은 외래 합니다. IntelliSense 사용 하 여 활용 하지 못하는 상태로 전체 줄을 작성 해야 합니다.

사용 하는 `LabelTagHelper`로 동일한 태그를 작성할 수 있습니다.

![이미지](intro/_static/label2.png)

입력 하는 즉시 태그 도우미 버전과 `<l` Visual Studio 편집기에서 IntelliSense는 일치 하는 요소를 표시 합니다.

![이미지](intro/_static/label.png)

IntelliSense를 사용 하면 전체 줄을 작성 합니다. `LabelTagHelper` 의 내용을 설정도 기본적으로 `asp-for` 특성 값 ("FirstName") "First name"; 각 새 대문자 문자 발생 하는 위치에 공백이 있는 속성 이름으로 구성 하는 문장 속성 카멜식 대/소문자를 변환 합니다. 다음 태그:

![이미지](intro/_static/label2.png)

생성합니다.


   ```html
   <label class="caption" for="FirstName">First Name</label>
      ```

The  camel-cased to sentence-cased content is not used if you add content to the `<label>`. For example:

![image](intro/_static/1stName.png)

generates:


   ```html
   <label class="caption" for="FirstName">Name First</label>
      ```

The following code image shows the Form portion of the *Views/Account/Register.cshtml* Razor view generated from the legacy ASP.NET 4.5.x MVC template included with Visual Studio 2015.

![image](intro/_static/regCS.png)

The Visual Studio editor displays C# code with a grey background. For example, the `AntiForgeryToken` HTML Helper:

```html
@Html.AntiForgeryToken()
   ```

회색 배경과 함께 표시 됩니다. 대부분의 레지스터 보기에서 태그는 C#입니다. 비교 하 여 태그 도우미를 사용 하 여 해당 하는 방법:

![이미지](intro/_static/regTH.png)

태그를 훨씬 깔끔하고 읽기, 편집 및 HTML 도우미 방식에 비해 유지 관리 하기가 됩니다. C# 코드는 서버에 대해 알고 있어야 하는 최소로 축소 됩니다. Visual Studio 편집기에는 고유한 글꼴로 태그 도우미 대상인 태그가 표시 됩니다.

고려는 *전자 메일* 그룹:

[!code-csharp[주](intro/sample/Register.cshtml?range=12-18)]

각 "asp-" 특성을 "Email"의 값 갖지만 "Email" 문자열이 아닙니다. 이 컨텍스트에서 "Email"은 C# 모델 식 속성에 대 한는 `RegisterViewModel`합니다.

Visual Studio 편집기를 사용 하면 작성 **모든** Visual Studio에서는 HTML 도우미 접근 방식에서 코드의 대부분에 대 한 도움말을 제공 하는 동안 등록 폼의 태그 도우미 접근 방식에서 태그입니다. [태그 도우미에 대 한 IntelliSense 지원](#intellisense-support-for-tag-helpers) Visual Studio 편집기에서 태그 도우미 작업 세부 정보로 이동 합니다.

## <a name="tag-helpers-compared-to-web-server-controls"></a>웹 서버 컨트롤에 비해 태그 도우미

* 태그 도우미;에 연결 하는 요소를 소유 하지 않고 단순히 요소 및 콘텐츠 렌더링에 참여 합니다. ASP.NET [웹 서버 컨트롤](https://msdn.microsoft.com/en-us/library/7698y1f0.aspx) 선언 되 고 페이지에서 호출 합니다.

* [웹 서버 컨트롤의](https://msdn.microsoft.com/en-us/library/zsyt68f1.aspx) 특수 주기를 어렵게 만들 수 있는 개발 하 고 디버깅 합니다.

* 웹 서버 컨트롤을 사용 하는 클라이언트 컨트롤을 사용 하 여 클라이언트 문서 개체 모델 (DOM) 요소에 기능을 추가할 수 있습니다. 태그 도우미가 없는 DOM.

* 웹 서버 컨트롤 브라우저 자동 검색을 포함 합니다. 태그 도우미는 브라우저의 알 수 없습니다.

* 동일한 요소에 여러 태그 도우미 작동할 수 있습니다 (참조 [태그 도우미 방지 충돌](https://docs.microsoft.com/aspnet/core/mvc/views/tag-helpers/authoring#avoiding-tag-helper-conflicts) ) 동안 일반적으로 웹 서버 컨트롤을 구성할 수 없습니다.

* 태그 도우미 태그와 하 고 범위 지정 된 HTML 요소의 콘텐츠를 수정할 수 있지만 페이지의 다른 항목에 직접 수정 하지 마세요. 웹 서버 컨트롤 보다 덜 구체적인 범위를 가져야 하 고 페이지;의 다른 부분에 영향을 주는 작업을 수행할 수 있습니다. 의도 하지 않은 부작용을 사용 하도록 설정 합니다.

* 웹 서버 컨트롤의 형식 변환기를 사용 하 여 문자열을 개체로 변환 하 합니다. 태그 도우미와 작업할 기본적으로 C#의 형식 변환 수행 필요가 없습니다.

* 웹 서버 컨트롤 사용 [System.ComponentModel](https://msdn.microsoft.com/en-us/library/system.componentmodel%28v=vs.110%29.aspx) 구성 요소와 컨트롤의 런타임 및 디자인 타임 동작을 구현 합니다. `System.ComponentModel`기본 클래스와 특성 및 형식 변환기 구현, 소스 데이터에 바인딩, 및 라이센스 구성 요소에 대 한 인터페이스를 포함 합니다. 일반적으로 파생 되는 태그 도우미 즉 `TagHelper`, 및 `TagHelper` 기본 클래스는 두 개의 메서드만 노출 `Process` 및 `ProcessAsync`합니다.

## <a name="customizing-the-tag-helper-element-font"></a>태그 도우미 요소 글꼴을 사용자 지정

글꼴 및 색 지정에서 사용자 지정할 수 있습니다 **도구** > **옵션** > **환경** > **글꼴 및 색**:

![이미지](intro/_static/fontoptions2.png)

## <a name="additional-resources"></a>추가 리소스

* [태그 도우미를 작성합니다.](authoring.md)
* [양식 사용](../working-with-forms.md)
* [GitHub에서 TagHelperSamples](https://github.com/dpaquette/TagHelperSamples) 작업을 위한 태그 도우미 샘플이 포함 되어 [부트스트랩](http://getbootstrap.com/)합니다.

<!--
* [Working with Forms ](xref:mvc/views/working-with-forms)
-->

