---
title: "휴대용 개체 지역화를 구성 합니다."
author: sebastienros
description: "이 문서는 휴대용 개체 파일을 소개 하 고 Orchard 핵심 프레임 워크와 ASP.NET Core 응용 프로그램에서 사용 하 여 단계를 간략하게 설명 합니다."
keywords: "ASP.NET Core, 지역화, 문화권, 언어, 휴대용 개체"
ms.author: scaddie
manager: wpickett
ms.date: 09/26/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/portable-object-localization
ms.openlocfilehash: 4fa73ae08b10217de657681a27f6097fc3443737
ms.sourcegitcommit: 732cd2684246e49e796836596643a8d37e20c46d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2017
---
# <a name="configure-portable-object-localization-with-orchard-core"></a>휴대용 개체 지역화 Orchard 코어를 사용 하 여 구성

여 [Sébastien Ros](https://github.com/sebastienros) 및 [Scott Addie](https://twitter.com/Scott_Addie)

이 문서에서는 ASP.NET Core 응용 프로그램을 이식 가능한 개체 (PO) 파일을 사용 하는 단계를 안내는 [Orchard 코어](https://github.com/OrchardCMS/OrchardCore) 프레임 워크입니다.

**참고:** Orchard 핵심 Microsoft 제품이 아닙니다. 따라서 Microsoft는이 기능에 대 한 지원 되지 않습니다을 제공합니다.

[보거나 다운로드 샘플 코드](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/localization/sample/POLocalization) ([다운로드 하는 방법을](xref:tutorials/index#how-to-download-a-sample))

## <a name="what-is-a-po-file"></a>PO 파일 이란 무엇 인가요?

PO 파일은 지정된 된 언어에 대 한 번역 된 문자열을 포함 하는 텍스트 파일로 배포 됩니다. PO 파일을 대신 사용의 몇 가지 이점을 *.resx* 파일 포함:
- PO 파일 지원 복수 적용 합니다. *.resx* 파일 복수 적용을 지원 하지 않습니다.
- PO 파일 처럼 컴파일되지 않습니다 *.resx* 파일입니다. 이와 같이 특수 한 도구 및 빌드 단계 사용할 필요가 없습니다.
- PO 파일 공동 온라인 편집 도구와 잘 작동합니다.

### <a name="example"></a>예제

복수 형태의으로 하나를 포함 하 여 프랑스어로 두 개의 문자열에 대 한 번역을 포함 하는 샘플 PO 파일 다음과 같습니다.

*fr.po*

```text
#: Services/EmailService.cs:29
msgid "Enter a comma separated list of email addresses."
msgstr "Entrez une liste d'emails séparés par une virgule."

#: Views/Email.cshtml:112
msgid "The email address is \"{0}\"."
msgid_plural "The email addresses are \"{0}\"."
msgstr[0] "L'adresse email est \"{0}\"."
msgstr[1] "Les adresses email sont \"{0}\""
```

이 예제에서는 다음 구문을 사용합니다.

- `#:`: 변환 해야 하는 문자열의 컨텍스트를 나타내는 설명입니다. 같은 문자열이 사용 되는 위치에 따라 다르게 변환 될 수 있습니다.
- `msgid`: 변환 되지 않은 문자열입니다.
- `msgstr`: 번역 된 문자열입니다.

복수 적용 지원의 경우 더 많은 항목을 정의할 수 있습니다.

- `msgid_plural`변환 되지 않음된: 복수형 문자열입니다.
- `msgstr[0]`0의 경우: 번역 된 문자열입니다.
- `msgstr[N]`Case 명사에 대 한 번역 된 문자열:

PO 파일 사양이 있습니다 [여기](https://www.gnu.org/savannah-checkouts/gnu/gettext/manual/html_node/PO-Files.html)합니다.

## <a name="configuring-po-file-support-in-aspnet-core"></a>ASP.NET Core에서 PO 파일 지원 구성

이 예제에서는 Visual Studio 2017 프로젝트 템플릿에서 생성 된 ASP.NET Core MVC 응용 프로그램을 기반으로 합니다.

### <a name="referencing-the-package"></a>패키지를 참조합니다.

에 대 한 참조 추가 `OrchardCore.Localization.Core` NuGet 패키지 합니다. 에 있는 [MyGet](https://www.myget.org/) 다음 패키지 소스에서: https://www.myget.org/F/orchardcore-preview/api/v3/index.json

*.csproj* 이제 다음과 비슷한 줄이 포함 파일 (버전 번호가 달라질 수 있음):

[!code-xml[Main](localization/sample/POLocalization/POLocalization.csproj?range=9)]

### <a name="registering-the-service"></a>서비스를 등록 하는 중

필요한 서비스를 추가 `ConfigureServices` 방식의 *Startup.cs*:

[!code-csharp[Main](localization/sample/POLocalization/Startup.cs?name=snippet_ConfigureServices&highlight=4-21)]

필요한 미들웨어를 추가 `Configure` 방식의 *Startup.cs*:

[!code-csharp[Main](localization/sample/POLocalization/Startup.cs?name=snippet_Configure&highlight=15)]

Razor 뷰를 선택 하려면 다음 코드를 추가 합니다. *About.cshtml* 이 예에서 사용 됩니다.

[!code-cshtml[Main](localization/sample/POLocalization/Views/Home/About.cshtml)]

`IViewLocalizer` 인스턴스가 삽입 되 고 "Hello world!" 텍스트를 번역 하는 데 사용 합니다.

### <a name="creating-a-po-file"></a>PO 파일 만들기

라는 파일을 만들어  *<culture code>.po* 응용 프로그램 루트 폴더에 있습니다. 이 예제에서는 파일 이름이 *fr.po* 프랑스어 사용 되기 때문에:

[!code-text[Main](localization/sample/POLocalization/fr.po)]

이 파일에는 변환할 문자열 및 프랑스어 번역 된 문자열을 모두 저장 합니다. 번역 필요한 경우 해당 부모 문화권으로 되돌립니다. 이 예제는 *fr.po* 파일은 요청 된 문화권이 사용 `fr-FR` 또는 `fr-CA`합니다.

### <a name="testing-the-application"></a>응용 프로그램 테스트

응용 프로그램을 실행 하 고 URL로 이동 `/Home/About`합니다. 텍스트 **Hello world!** ´ ù.

URL로 이동 `/Home/About?culture=fr-FR`합니다. 텍스트 **Bonjour le monde!** ´ ù.

## <a name="pluralization"></a>복수 적용

PO 파일 카디널리티에 따라 다르게 변환 해야 하는 같은 문자열이 필요한 경우 유용 복수 적용 폼을 지원 합니다. 이 작업은 각 언어 문자열을 사용 하 여 카디널리티에 따라 선택 하는 사용자 지정 규칙을 정의 한다는 점에서 복잡 한 이루어집니다.

Orchard 지역화 패키지는 이러한 다른 복수 형태를 자동으로 호출 하는 API를 제공 합니다.

### <a name="creating-pluralization-po-files"></a>복수 적용 PO 파일 만들기

다음 콘텐츠를 앞에서 언급 한 추가 *fr.po* 파일:

```text
msgid "There is one item."
msgid_plural "There are {0} items."
msgstr[0] "Il y a un élément."
msgstr[1] "Il y a {0} éléments."
```

참조 [PO 파일 이란 무엇 인가요?](#what-is-a-po-file) 에 대 한 설명은이 예에서 각 항목의 의미 합니다.

### <a name="adding-a-language-using-different-pluralization-forms"></a>다른 복수 적용 양식을 사용 하 여 언어를 추가 합니다.

영어와 프랑스어 문자열 앞의 예제에서 사용 되었습니다. 영어와 프랑스어 두 복수 적용 형식에만 있고 동일한 폼 규칙을 공유 하므로 하나의 카디널리티는 첫 번째 복수 형태의에 매핑되어 있는지 합니다. 다른 카디널리티는 두 번째 복수 형태의에 매핑됩니다.

일부 언어는 동일한 규칙을 공유합니다. 이 세 가지 복수 형태는 체코어 언어를 가진 나와 있습니다.

만들기는 `cs.po` 다음과 같이 파일 및 복수 적용은 세 가지 다른 번역을 요구 하는 방법을 확인 합니다.

[!code-text[Main](localization/sample/POLocalization/cs.po)]

체코어 지역화를 허용 하려면 추가 `"cs"` 에서 지원 되는 culture의 목록에는 `ConfigureServices` 메서드:

```csharp
var supportedCultures = new List<CultureInfo>
{
    new CultureInfo("en-US"),
    new CultureInfo("en"),
    new CultureInfo("fr-FR"),
    new CultureInfo("fr"),
    new CultureInfo("cs")
};
```

편집 된 *Views/Home/About.cshtml* 여러 카디널리티에 대 한 복수, 지역화 된 문자열을 렌더링 하는 파일:

```cshtml
<p>@Localizer.Plural(1, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(2, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(5, "There is one item.", "There are {0} items.")</p>
```

**참고:** 실제 시나리오에서는 변수 수를 나타내는 데 사용할 수는 있습니다. 여기에서 매우 특정 한 경우를 노출 하는 3 가지 값을 사용 하 여 동일한 코드를 반복 합니다.

문화권을 전환 되 면 다음 화면이 나타납니다.

`/Home/About`의 경우:

```html
There is one item.
There are 2 items.
There are 5 items.
```

`/Home/About?culture=fr`의 경우:

```html
Il y a un élément.
Il y a 2 éléments.
Il y a 5 éléments.
```

`/Home/About?culture=cs`의 경우:

```html
Existuje jedna položka.
Existují 2 položky.
Existuje 5 položek.
```

참고 체코어 문화권에 대 한 세 가지 번역 다르기 때문입니다. 프랑스어 및 영어 문화권 마지막 두 개의 번역 된 문자열에 대 한 동일한 구조를 공유합니다.

## <a name="advanced-tasks"></a>고급 작업

### <a name="contextualizing-strings"></a>Contextualizing 문자열

응용 프로그램에는 종종 여러 위치에서 변환 하는 문자열이 들어 있습니다. 동일한 문자열 (Razor 뷰 또는 클래스 파일)는 응용 프로그램 내의 특정 위치에서 다른 변환이 있을 수 있습니다. PO 파일 분류 되 고 표시 되는 문자열을 사용할 수 있는 한 파일 컨텍스트의 개념을 지원 합니다. 파일 컨텍스트를 사용 하는 문자열 번역할 수 있습니다 다르게 파일 컨텍스트 (또는 파일 컨텍스트 부족)에 따라.

PO 지역화 서비스에는 전체 클래스 또는 문자열로 변환할 때 사용 되는 뷰 이름을 사용 합니다. 에 값을 설정 하 여 이렇게는 `msgctxt` 항목입니다.

이전에 사소한 추가 고려 *fr.po* 예제입니다. Razor 보기에 있는 *Views/Home/About.cshtml* 예약을 설정 하 여 파일 컨텍스트도 정의할 수 있습니다 `msgctxt` 항목의 값:

```text
msgctxt "Views.Home.About"
msgid "Hello world!"
msgstr "Bonjour le monde!"
```

와 `msgctxt` 탐색 하는 경우 텍스트 변환이 발생 따라서 설정, `/Home/About?culture=fr-FR`합니다. 탐색 하는 경우 변환이 발생 하지 않습니다 `/Home/Contact?culture=fr-FR`합니다.

특정 항목이 지정 된 파일 컨텍스트와 일치 하면 Orchard 코어 대체 메커니즘이 컨텍스트 없이 적절 한 PO 파일을 찾습니다. 가정 하는 경우 컨텍스트가 특정 파일에 대해 정의 된 *Views/Home/Contact.cshtml*탐색 하려면, `/Home/Contact?culture=fr-FR` 같은 PO 파일을 로드 합니다.

[!code-text[Main](localization/sample/POLocalization/fr.po)]

### <a name="changing-the-location-of-po-files"></a>PO 파일의 위치를 변경합니다.

PO 파일의 기본 위치에서 변경할 수 있습니다 `ConfigureServices`:

```csharp
services.AddPortableObjectLocalization(options => options.ResourcesPath = "Localization");
```

이 예제에서는 PO 파일에서 로드 되는 *지역화* 폴더입니다.

### <a name="implementing-a-custom-logic-for-finding-localization-files"></a>지역화 파일을 찾기 위한 사용자 지정 논리를 구현 합니다.

PO 파일을 배치할 필요는 더 복잡 한 논리는 `OrchardCore.Localization.PortableObject.ILocalizationFileLocationProvider` 인터페이스를 구현 하 고 서비스로 등록 될 수 있습니다. 다양 한 위치에 PO 파일을 저장할 수 있습니다 또는 파일 폴더의 계층 구조 내에서 찾을 수 하는 경우에 유용 합니다.

### <a name="using-a-different-default-pluralized-language"></a>복수화 다른 기본 언어를 사용 하 여

패키지에 포함 된 `Plural` 두 복수 형태에 관련 된 확장 메서드. 더 많은 복수 형태를 요구 하는 언어에 대 한 확장 메서드를 만듭니다. 확장 방식으로 기본 언어에 대 한 모든 지역화 파일을 제공 하도록 필요 하지는 않습니다 &mdash; 원본 문자열은 이미 코드에서 직접 사용할 수 있습니다.

보다 일반적인을 사용할 수 있습니다 `Plural(int count, string[] pluralForms, params object[] arguments)` 번역의 문자열 배열을 허용 하는 오버 로드 합니다.