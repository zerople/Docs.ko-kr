---
title: "ASP.NET Core에서 태그 도우미를 작성합니다."
author: rick-anderson
description: "ASP.NET Core에서 태그 도우미를 작성 하는 방법을 알아봅니다."
keywords: "ASP.NET Core, 태그 도우미"
ms.author: riande
manager: wpickett
ms.date: 06/14/2017
ms.topic: article
ms.assetid: 4f16d978-5695-4abf-a785-fdaabf3bbcb9
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/views/tag-helpers/authoring
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1a5222da1380c2fe768b287bfa1a49b300c02f2b
ms.sourcegitcommit: 67f54fabbfa4e3942f5bfe1f8a7fdfe4a7a75358
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/19/2017
---
# <a name="authoring-tag-helpers-in-aspnet-core-a-walkthrough-with-samples"></a>ASP.NET Core, 샘플을 연습에서에서 태그 도우미를 작성합니다.

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)

[샘플 코드 보기 또는 다운로드](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/views/tag-helpers/authoring/sample)

## <a name="getting-started-with-tag-helpers"></a>태그 도우미를 시작 하기

이 자습서는 프로그래밍 태그 도우미에 대 한 소개를 제공합니다. [태그 도우미 소개](intro.md) 태그 도우미를 제공 하는 이점에 설명 합니다.

태그 도우미 클래스를 구현 하는 모든 클래스는는 `ITagHelper` 인터페이스입니다. 그러나 태그 도우미를 작성할 때 일반적으로에서 파생 시키고 `TagHelper`, 따라서 제공에 대 한 액세스를 수행 하는 `Process` 메서드.

1. 라는 새 ASP.NET Core 프로젝트 만들기 **AuthoringTagHelpers**합니다. 이 프로젝트에 대 한 인증에 필요 하지는 않습니다.

2. 태그 도우미 호출을 보관할 폴더를 만들 *TagHelpers*합니다. *TagHelpers* 폴더는 *하지* 필요한 합리적인 규칙 이지만 합니다. 이제 시작 하겠습니다 몇 가지 간단한 태그 도우미를 작성 합니다.

## <a name="a-minimal-tag-helper"></a>최소한의 태그 도우미

이 섹션에서는 전자 메일 태그를 업데이트 하는 태그 도우미를 작성할 수 있습니다. 예:

```html
<email>Support</email>
   ```

서버 해당 태그를 다음으로 변환 하는 전자 메일 태그 도우미를 사용 합니다.

```html
<a href="mailto:Support@contoso.com">Support@contoso.com</a>
   ```

즉, 앵커 태그는 하면 전자 메일 링크 합니다. 블로그 엔진을 작성 하는 경우 동일한 도메인에 모든 마케팅, 지원 및 다른 연락처에 대 한 전자 메일을 보낼 수 필요 사용자에 게 이렇게 할 수 있습니다.

1.  다음 추가 `EmailTagHelper` 클래스는 *TagHelpers* 폴더입니다.

    [!code-csharp[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1EmailTagHelperCopy.cs)]
    
    **참고:**
    
    * 루트 클래스 이름의 요소를 대상으로 하는 명명 규칙을 사용 하 여 태그 도우미 (빼기는 *TagHelper* 클래스 이름의 부분)입니다. 이 예제에서는 루트 이름 **전자 메일**TagHelper은 *전자 메일*이므로 `<email>` 태그 대상이 됩니다. 이 명명 규칙은 대부분 태그 도우미에 대 한 작동 해야, 나중에 재정의 하는 방법을 보여 드리 려 합니다.
    
    * `EmailTagHelper` 클래스는 `TagHelper`에서 파생됩니다. `TagHelper` 클래스 태그 도우미를 작성 하기 위한 메서드 및 속성을 제공 합니다.
    
    * 재정의 된 `Process` 방법은 태그 도우미 수행 하는 작업 실행을 제어 합니다. `TagHelper` 클래스는 비동기 버전을 제공 (`ProcessAsync`) 동일한 매개 변수를 사용 합니다.
    
    * 컨텍스트 매개 변수를 `Process` (및 `ProcessAsync`) 현재 HTML 태그의 실행과 관련 된 정보를 포함 합니다.
    
    * 출력 매개 변수를 `Process` (및 `ProcessAsync`)는 HTML 태그 및 콘텐츠를 생성 하는 데 원본 소스를 반영 하는 상태 저장 HTML 요소를 포함 합니다.
    
    * 통해 클래스 이름 접미사가 **TagHelper**, 즉 *하지* 필수, 모범 사례 규칙 것으로 간주 되지만 합니다. 클래스를 선언할 수 있습니다.
    
    ```csharp
    public class Email : TagHelper
    ```

2.  확인 하는 `EmailTagHelper` 우리의 모든 Razor 뷰를 사용할 수 있는 클래스, 추가 `addTagHelper` 지시문을 *Views/_ViewImports.cshtml* 파일:[!code-html[Main](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImportsCopyEmail.cshtml?highlight=2,3)]
    
    위의 코드에서는 와일드 카드 구문을 사용할 수 있는 모든 태그 도우미 어셈블리에서 지정 하 여 뒤의 첫 번째 문자열 `@addTagHelper` 로드할 태그 도우미를 지정 합니다 (사용 하 여 "*" 모든 태그 도우미에 대 한), 및 두 번째 문자열 "AuthoringTagHelpers" 태그 도우미에는 어셈블리를 지정 합니다. 또한 와일드 카드 구문을 사용 하 여 ASP.NET Core MVC 태그 도우미의 두 번째 줄을 제공 하는 확인 (해당 도우미에 대해서는 설명 [태그 도우미 소개](intro.md).) `@addTagHelper` 지시문 태그 도우미 Razor 뷰를 사용할 수 있도록 합니다. 또는 다음과 같이 태그 도우미의 정규화 된 이름 (FQN)를 제공할 수 있습니다.
    
    [!code-html[Main](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImports.cshtml?highlight=3&range=1-3)]
    
    태그 도우미는 FQN를 사용 하 여 뷰를 추가 하려면 먼저는 FQN가 추가 (`AuthoringTagHelpers.TagHelpers.EmailTagHelper`), 및 다음 어셈블리 이름 (*AuthoringTagHelpers*). 와일드 카드 구문을 사용 하 여 대부분의 개발자를 선호 합니다. [태그 도우미 소개](intro.md) 태그 도우미 추가, 제거, 계층 및 와일드 카드 구문을 세부적으로 이동 합니다.
    
3.  태그가 업데이트는 *Views/Home/Contact.cshtml* 이러한 변경 내용으로 파일:

    [!code-html[Main](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Contact.cshtml?highlight=15,16&range=1-17)]

4.  응용 프로그램을 실행 하 고 선호 하는 브라우저를 사용 하 여 전자 메일 태그 앵커 태그도 바뀌는 것을 확인할 수 있도록 HTML 소스를 봅니다 (예를 들어 `<a>Support</a>`). *지원* 및 *마케팅* 는 링크로 렌더링 됩니다 있지만 없습니다는 `href` 특성을 작동할 수 있도록 합니다. 다음 섹션에서이 문제를 해결 합니다 했습니다.

참고: HTML 태그 및 특성, 태그, 클래스 이름 및 Razor, 및 C#의 특성을 같은 않습니다 대/소문자 구분.

## <a name="setattribute-and-setcontent"></a>SetAttribute 및 SetContent

이 섹션에서는 업데이트 하는 `EmailTagHelper` 하 여 전자 메일에 대 한 유효한 앵커 태그를 만듭니다. 업데이트를 가져오도록 Razor 보기의 정보 (형식으로 `mail-to` 특성)에서 앵커를 생성 합니다.이 사용 합니다.

업데이트는 `EmailTagHelper` 다음을 사용 하 여 클래스:

[!code-csharp[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelperMailTo.cs?range=6-22)]

**참고:**

* 파스칼식 대/소문자 클래스 및 속성 이름 태그 도우미에 대 한 변환 자신의 [kebab 대/소문자를 줄이려면](https://stackoverflow.com/questions/11273282/whats-the-name-for-dash-separated-case/12273101)합니다. 따라서 사용 하는 `MailTo` 특성을 사용 하 여 `<email mail-to="value"/>` 와 동일 합니다.

* 마지막 줄이 최소 기능 태그 도우미에 대 한 완료 된 콘텐츠를 설정합니다.

* 강조 표시 된 줄에는 특성을 추가 하기 위한 구문을 보여 줍니다.

[!code-csharp[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelperMailTo.cs?highlight=6&range=14-21)]

이 방법으로 속성 컬렉션에 현재 존재 하지 않는 "href" 특성에 대 한 작동 합니다. 사용할 수도 있습니다는 `output.Attributes.Add` 메서드를 태그 도우미 특성의 태그 특성 컬렉션의 끝에 추가 합니다.

1.  태그가 업데이트는 *Views/Home/Contact.cshtml* 이러한 변경 내용으로 파일:[!code-html[Main](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/ContactCopy.cshtml?highlight=15,16)]

2.  응용 프로그램을 실행 하 고 올바른 링크를 생성 하는지 확인 합니다.
    
    > [!NOTE]
    >전자 메일 태그 자체 닫는 작성 하는 경우 (`<email mail-to="Rick" />`), 최종 출력도 자체적으로 닫는 것입니다. 시작 태그를 사용 하 여 태그를 작성 하는 기능을 사용 하도록 설정 하려면 (`<email mail-to="Rick">`) 다음을 사용 하 여 클래스를 데코레이팅 해야 합니다.
    >
    > [!code-csharp[Main](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelperMailVoid.cs?highlight=1&range=6-10)]
    
    자체적으로 닫는 전자 메일 태그 도우미 출력 사용할 때 `<a href="mailto:Rick@contoso.com" />`합니다. 자체적으로 닫는 앵커 태그 되지 않으므로 유효한 HTML을 만들라는, 원하지 하지만 자체적으로 닫는 태그 도우미를 만들려고 할 수 있습니다. 태그 도우미의 유형을 설정는 `TagMode` 태그를 읽은 후 속성입니다.
    
### <a name="processasync"></a>ProcessAsync

이 섹션에서는 비동기 전자 메일 도우미를 작성 합니다.

1.  대체는 `EmailTagHelper` 를 다음 코드로 클래스:

    [!code-csharp[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelper.cs?range=6-17)]

    **참고:**

    * 이 버전은 비동기 사용 `ProcessAsync` 메서드. 비동기 `GetChildContentAsync` 반환는 `Task` 포함 하는 `TagHelperContent`합니다.

    * 사용 하 여는 `output` HTML 요소의 콘텐츠를 가져오지 매개 변수입니다.

2.  다음과 같이 변경 하는 *Views/Home/Contact.cshtml* 태그 도우미 대상 전자 메일을 받을 수 있도록 파일입니다.

    [!code-html[Main](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Contact.cshtml?highlight=15,16&range=1-17)]

3.  응용 프로그램을 실행 하 고 유효한 전자 메일 링크를 생성 하는지 확인 합니다.

### <a name="removeall-precontentsethtmlcontent-and-postcontentsethtmlcontent"></a>RemoveAll, PreContent.SetHtmlContent 및 PostContent.SetHtmlContent

1.  다음 추가 `BoldTagHelper` 클래스는 *TagHelpers* 폴더입니다.

    [!code-csharp[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/BoldTagHelper.cs)]

    **참고:**
    
    * `[HtmlTargetElement]` "bold" 이름이 HTML 특성을 포함 하는 HTML 요소를 지정 하는 특성 매개 변수는 일치 하는 전달 특성 및 `Process` 재정의 메서드는 클래스에서 실행 됩니다. 이 예제는 `Process` 메서드 "bold" 특성을 제거 하 고 사용 하 여 포함 된 태그를 둘러싸는 `<strong></strong>`합니다.
    
    * 열기를 작성 해야 콘텐츠 기존 태그를 대체 하지 않으려면 때문에 `<strong>` 사용 하 여 태그는 `PreContent.SetHtmlContent` 메서드와 닫는 `</strong>` 사용 하 여 태그는 `PostContent.SetHtmlContent` 메서드.
    
2.  수정 된 *About.cshtml* 포함할 뷰를는 `bold` 특성 값입니다. 완성 된 코드는 다음과 같습니다.

    [!code-html[Main](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/AboutBoldOnly.cshtml?highlight=7)]

3.  앱을 실행합니다. 소스를 검사 하는 태그를 확인 하십시오. 즐겨 찾는 브라우저를 사용할 수 있습니다.

    `[HtmlTargetElement]` 위의 특성에만 "bold"의 특성 이름을 제공 하는 HTML 태그를 대상으로 합니다. `<bold>` 요소 태그 도우미에서 수정 되지 않았습니다.

4. 주석으로 처리는 `[HtmlTargetElement]` 고 특성 줄을 대상으로 기본값은 `<bold>` 폼의 HTML 태그, 태그 `<bold>`합니다. 기억, 기본 명명 규칙 클래스 이름과 일치 합니다 **b o l d**TagHelper를 `<bold>` 태그입니다.

5. 응용 프로그램을 실행 하 고 있는지 확인은 `<bold>` 태그 태그 도우미에 의해 처리 됩니다.

여러 개의 클래스를 데코레이팅하 `[HtmlTargetElement]` 특성 대상의 논리 OR 결과입니다. 예를 들어 아래 코드를 사용 하 여 굵게 태그 또는 특성 일치 합니다.

[!code-csharp[Main](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/zBoldTagHelperCopy.cs?highlight=1,2&range=5-15)]

동일한 문에서 특성이 여러 개를 추가 하는 경우 런타임에으로 처리할 수 있으며 논리적 AND 예를 들어 아래 코드에는 HTML 요소 이름을 지정 해야 "bold" 라는 특성을 가진 "bold" (`<bold bold />`)와 일치 하도록 합니다.

```csharp
[HtmlTargetElement("bold", Attributes = "bold")]
   ```

사용할 수도 있습니다는 `[HtmlTargetElement]` 대상 요소의 이름을 변경할 수 있습니다. 예를 들어는 `BoldTagHelper` 대상 `<MyBold>` 태그를 다음 특성을 사용 합니다.

```csharp
[HtmlTargetElement("MyBold")]
   ```

## <a name="passing-a-model-to-a-tag-helper"></a>태그 도우미에는 모델을 전달합니다.

1.  추가 *모델* 폴더입니다.

2.  다음 `WebsiteContext` 클래스를 *Models* 폴더에 추가합니다.

    [!code-csharp[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Models/WebsiteContext.cs)]

3.  다음 추가 `WebsiteInformationTagHelper` 클래스는 *TagHelpers* 폴더입니다.

    [!code-csharp[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/WebsiteInformationTagHelper.cs)]
    
    **참고:**
    
    * 태그 도우미 파스칼식 대/소문자 C# 클래스 이름 및 속성에 대 한 태그 도우미에 대 한 변환에서 설명한 대로 [kebab 대/소문자를 줄이려면](http://wiki.c2.com/?KebabCase)합니다. 따라서 사용 하는 `WebsiteInformationTagHelper` Razor에서 작성 `<website-information />`합니다.
    
    * 사용 하 여 대상 요소를 명시적으로 식별 되지 됩니다는 `[HtmlTargetElement]` 특성의 기본값을 `website-information` 대상이 됩니다. 다음 특성 (참고 kebab 사례 되지만 클래스 이름과 일치) 적용 한 경우:
    
    ```csharp
    [HtmlTargetElement("WebsiteInformation")]
    ```
    
    더 낮은 kebab 사례 태그 `<website-information />` 일치 하지 않습니다. 사용 하려는 경우는 `[HtmlTargetElement]` 특성을 아래와 같이 kebab 대/소문자를 사용 합니다.
    
    ```csharp
    [HtmlTargetElement("Website-Information")]
    ```
    
    * 자체 닫는 요소에는 내용이 없는 합니다. 이 예제에 대 한 Razor 태그를 사용 하 여 자체적으로 닫는 태그 이지만 태그 도우미를 만들는 [섹션](http://www.w3.org/TR/html5/sections.html#the-section-element) 요소 (자체적으로 닫는 되 고 작성 하는 내 콘텐츠는 `section` 요소). 따라서 설정 해야 `TagMode` 를 `StartTagAndEndTag` 출력을 작성 합니다. 또는 주석으로 처리 설정 행 `TagMode` 닫는 태그를 사용 하 여 태그를 작성 합니다. (이 자습서의 뒷부분에 나오는 예제 태그 제공 됩니다.)
    
    * `$` (달러 기호) 사용 하 여 다음 줄에서 한 [문자열 보간](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/interpolated-strings):
    
    ```cshtml
    $@"<ul><li><strong>Version:</strong> {Info.Version}</li>
    ```

4.  다음 태그를 추가 *About.cshtml* 보기. 강조 표시 된 태그는 웹 사이트 정보를 표시합니다.
    
    [!code-html[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/About.cshtml?highlight=1,12-)]
    
    >[!NOTE]
    > 아래에 표시 된 Razor 태그:
    >
    >[!code-html[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/About.cshtml?range=13-17)]
    > 
    >Razor 알고는 `info` 특성은 문자열이 아닌 클래스 및 C# 코드를 작성 하려면. 모든 문자열이 아닌 태그 도우미 특성 없이 쓸지는 `@` 문자입니다.
    
5.  응용 프로그램을 실행 하 고 웹 사이트 정보를 보려면 정보 보기로 이동 합니다.

    >[!NOTE]
    >닫는 태그에 다음 태그를 사용 하 고 있는 줄을 제거할 수 있습니다 `TagMode.StartTagAndEndTag` 태그 도우미에서:
    >
    >[!code-html[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/AboutNotSelfClosing.cshtml?range=13-18)]

## <a name="condition-tag-helper"></a>조건 태그 도우미

조건 태그 도우미 true 값을 전달 하는 경우의 출력을 렌더링 합니다.

1.  다음 추가 `ConditionTagHelper` 클래스는 *TagHelpers* 폴더입니다.

    [!code-csharp[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/ConditionTagHelper.cs)]

2.  내용을 대체는 *Views/Home/Index.cshtml* 다음 태그로 파일:

    <!-- literal_block {"xml:space": "preserve", "source": "mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Index.cshtml", "ids": [], "linenos": false, "highlight_args": {"linenostart": 1}} -->
    
    ```cshtml
    @using AuthoringTagHelpers.Models
    @model WebsiteContext
    
    @{
        ViewData["Title"] = "Home Page";
    }
    
    <div>
        <h3>Information about our website (outdated):</h3>
        <website-information info=Model />
        <div condition="Model.Approved">
            <p>
                This website has <strong surround="em"> @Model.Approved </strong> been approved yet.
                Visit www.contoso.com for more information.
            </p>
        </div>
    </div>
    ```
    
3.  대체는 `Index` 에서 메서드는 `Home` 를 다음 코드로 컨트롤러:

    [!code-csharp[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Controllers/HomeController.cs?range=9-18)]

4.  응용 프로그램을 실행 하 고 홈 페이지로 이동 합니다. 조건부의 태그를 `div` 렌더링 되지 것입니다. 쿼리 문자열 추가 `?approved=true` url (예를 들어 `http://localhost:1235/Home/Index?approved=true`). `approved`가 true로 설정 되었고 조건부 태그 표시 됩니다.

>[!NOTE]
>사용 하 여는 [nameof](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/nameof) 연산자를 굵게 표시 된 태그 도우미에서와 마찬가지로 문자열을 지정 하지 않고 대상에 특성을 지정 합니다.
>
>[!code-csharp[Main](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/zConditionTagHelperCopy.cs?highlight=1,2,5&range=5-18)]
>
>[nameof](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/nameof) 연산자는 보호 코드 해야 것도 리팩터링할 (하도록 이름을 변경 하려는 `RedCondition`) 합니다.

### <a name="avoiding-tag-helper-conflicts"></a>태그 도우미 충돌 방지

이 섹션에서는 한 쌍 자동 링크 태그 도우미의 작성할 수 있습니다. 첫 번째는 HTML 앵커 태그 동일한 URL이 포함 된 (및 따라서 URL에 대 한 링크를 생성)를 HTTP로 시작 하는 URL을 포함 하는 태그를 바뀝니다. 두 번째는 동일한 작업을 수행 URL에 대 한 WWW로 시작 합니다.

이러한 두 도우미 밀접 한 관련이 있으므로 나중에 리팩터링할 수 있습니다에 같은 파일에 유지할 수 있습니다.

1.  다음 추가 `AutoLinkerHttpTagHelper` 클래스는 *TagHelpers* 폴더입니다.

    [!code-csharp[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinker.cs?range=7-19)]

    >[!NOTE]
    >`AutoLinkerHttpTagHelper` 대상 클래스 `p` 요소 및 사용 하 여 [Regex](https://docs.microsoft.com/dotnet/standard/base-types/regular-expression-language-quick-reference) 앵커를 만들려고 합니다.

2.  끝에 다음 태그를 추가 *Views/Home/Contact.cshtml* 파일:

    [!code-html[Main](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Contact.cshtml?highlight=19)]

3.  응용 프로그램을 실행 하 고 태그 도우미가 앵커를 제대로 렌더링 되는지 확인 합니다.

4.  업데이트는 `AutoLinker` 포함 하도록 클래스는 `AutoLinkerWwwTagHelper` www 텍스트도 원래 www 텍스트를 포함 하는 앵커 태그를 변환 됩니다입니다. 업데이트 된 코드는 아래 강조 표시 됩니다.

    [!code-csharp[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinker.cs?highlight=15-34&range=7-34)]

5.  앱을 실행합니다. 링크로 www 텍스트를 렌더링 하지 않으면이 HTTP 텍스트를 확인 합니다. 두 클래스에 중단점을 배치 하면 HTTP 태그 도우미 클래스 첫 번째 실행 되는지 확인할 수 있습니다. 문제는 태그 도우미 출력에 캐시 되며 HTTP 태그 도우미의 캐시 된 출력 덮어씁니다 WWW 태그 도우미를 실행 하는 경우입니다. 이 자습서의 뒷부분에 나오는 태그 도우미에서 실행 되는 순서를 제어 하는 방법을 살펴보겠습니다. 다음 코드를 수정 하겠습니다.

    [!code-csharp[Main](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinkerCopy.cs?highlight=5,6,10,21,22,26&range=8-37)]

    >[!NOTE]
    >자동 링크 태그 도우미의 첫 번째 버전을 다음 코드로 대상의 내용을 가져온:
    >
    >[!code-csharp[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinker.cs?range=12)]
    >
    >즉, 호출 `GetChildContentAsync` 를 사용 하는 `TagHelperOutput` 에 전달 되는 `ProcessAsync` 메서드. 언급 한 것 처럼 이전에 출력 캐시 되므로 마지막 태그 wins를 실행 하는 도우미입니다. 다음 코드와 함께 해당 문제가 해결:
    >
    >[!code-csharp[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z2AutoLinkerCopy.cs?range=34-35)]
    >
    >위의 코드 콘텐츠가 수정 된 출력 버퍼에서 콘텐츠를 가져옵니다 적 하는 경우를 확인 합니다.

6.  응용 프로그램을 실행 하 고 두 개의 링크가 예상 대로 작동 하는지 확인 합니다. 우리의 자동 링커 태그 도우미는 완벽 한 상태가 나타날 수 있습니다, 미묘한 문제가 이었지만 합니다. WWW 태그 도우미 실행 될 경우 첫 번째, www 링크 올바른 되지 않습니다. 코드를 추가 하 여 업데이트 된 `Order` 태그에서 실행 되는 순서를 제어 하는 오버 로드 합니다. `Order` 속성 같은 요소를 대상으로 하는 다른 태그 도우미를 기준으로 실행 순서를 결정 합니다. 기본 순서 값은 0이 고 더 낮은 값이 있는 인스턴스를 먼저 실행 됩니다.

    [!code-csharp[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z2AutoLinkerCopy.cs?highlight=5,6,7,8&range=8-15)]
    
    위의 코드는 HTTP 태그 도우미 WWW 태그 도우미 하기 전에 실행 되도록 보장 됩니다. 변경 `Order` 를 `MaxValue` WWW 태그에 대 한 생성 된 태그 올바른지 확인 합니다.

## <a name="inspecting-and-retrieving-child-content"></a>검사 하 고 자식 콘텐츠를 검색 합니다.

태그 도우미 콘텐츠를 검색 하도록 여러 가지 속성을 제공 합니다.

-  결과 `GetChildContentAsync` 추가할 수 있는 `output.Content`합니다.
-  결과 검사할 수 `GetChildContentAsync` 와 `GetContent`합니다.
-  수정 하는 경우 `output.Content`, TagHelper 본문을 실행 되거나 호출 하지 않으면 렌더링 되지 것입니다 `GetChildContentAsync` 자동 링커 샘플에서와 같이:

[!code-csharp[Main](../../views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinkerCopy.cs?highlight=5,6,10&range=8-21)]

-  여러 번 호출 `GetChildContentAsync` 은 동일한 값을 반환 하 고 다시 실행 되지 것입니다는 `TagHelper` 캐시 된 결과 사용 하지 않으므로 나타내는 false 매개 변수에서 전달 하지 않으면 본문입니다.
