---
title: "전역화 및 지역화 ASP.NET Core"
author: rick-anderson
description: "ASP.NET Core 콘텐츠를 다른 언어와 문화권 지역화할에 대 한 미들웨어 및 서비스를 제공 방법에 대해 알아봅니다."
keywords: "ASP.NET Core, 지역화, 문화권, 언어, 리소스 파일, 전역화, 국제화, 로캘"
ms.author: riande
manager: wpickett
ms.date: 01/14/2017
ms.topic: article
ms.assetid: 7f275a09-f118-41c9-88d1-8de52d6a5aa1
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/localization
ms.openlocfilehash: 85a192bf0b2eb245ecdaaa8ffa1c8dd2f43b45b0
ms.sourcegitcommit: 6e83c55eb0450a3073ef2b95fa5f5bcb20dbbf89
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/28/2017
---
# <a name="globalization-and-localization-in-aspnet-core"></a>전역화 및 지역화 ASP.NET Core

여 [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [구재석 Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://twitter.com/NadeemAfana), 및 [Hisham Bin Ateya](https://twitter.com/hishambinateya)

ASP.NET Core를 사용 하 여 다국어 웹 사이트 만들기 하도록 사이트를 더 광범위 한에 도달 하면 수 있습니다. ASP.NET Core 다른 언어와 문화권에 지역화를 위한 서비스 및 미들웨어를 제공 합니다.

국제화 포함 [전역화](https://docs.microsoft.com/dotnet/api/system.globalization) 및 [지역화](https://docs.microsoft.com/dotnet/standard/globalization-localization/localization)합니다. 세계화는 서로 다른 culture를 지 원하는 응용 프로그램을 설계 하는 과정입니다. 세계화에는 입력, 표시 및 정의 된 집합이 특정 지역과 관련 된 언어 스크립트의 출력에 대 한 지원을 추가 합니다.

지역화는 이미 특정 문화권/로캘로 지역화 가능성에 대 한 처리 전역화 된 응용 프로그램을 위해 조정 하는 과정입니다.  자세한 내용은 참조 **전역화 및 지역화 용어** 이 문서의 끝 부분입니다.

응용 프로그램 지역화 과정은 다음과 같습니다.

1. 응용 프로그램의 콘텐츠를 지역화할 수 있도록

2. 언어 및 지원 되는 문화권에 대 한 지역화 된 리소스를 제공 합니다.

3. 각 요청에 대 한 언어/문화권을 선택 하는 전략을 구현 합니다.

## <a name="make-the-apps-content-localizable"></a>응용 프로그램의 콘텐츠를 지역화할 수 있도록

ASP.NET Core에 도입 된 `IStringLocalizer` 및 `IStringLocalizer<T>` 지역화 된 응용 프로그램을 개발할 때 생산성을 향상 하도록 설계 되었습니다. `IStringLocalizer`사용 하 여는 [ResourceManager](https://docs.microsoft.com/dotnet/api/system.resources.resourcemanager) 및 [ResourceReader](https://docs.microsoft.com/dotnet/api/system.resources.resourcereader) 런타임 시 문화권별 리소스를 제공 합니다. 간단한 인터페이스에는 인덱서 및 `IEnumerable` 지역화 된 문자열을 반환 합니다. `IStringLocalizer`리소스 파일에는 기본 언어 문자열을 저장 하도록 요구 하지 않습니다. 지역화를 위한 대상으로 하는 응용 프로그램을 개발할 수 있으며 초기 개발에에서 리소스 파일을 만들 필요가 없습니다. 아래 코드 지역화에 대 한 "에 대 한 Title" 문자열을 래핑하는 방법을 보여 줍니다.

[!code-csharp[Main](localization/sample/Localization/Controllers/AboutController.cs)]

위의 코드에는 `IStringLocalizer<T>` 구현에서 가져온 [종속성 주입](dependency-injection.md)합니다. "에 대 한 Title"의 지역화 된 값을 찾을 수 없습니다는 경우 인덱서 키 반환 되 면, 즉 "에 대 한 Title" 문자열입니다. 앱의 언어 리터럴 문자열 기본값을 그대로 수 있으며 응용 프로그램 개발에 집중할 수 있도록 로컬라이저의에서 래핑할 수 있습니다. 기본 언어를 사용 하 여 앱을 개발 하 고이 정보를 먼저 기본 리소스 파일을 만들지 않고 지역화 단계에 대 한 준비 합니다. 또는 기존의 접근 방식을 사용 하 고 기본 언어 문자열을 검색 하 여 키를 입력 수 있습니다. 대부분의 개발자 하지 않는 경우 기본 언어의 새 워크플로 *.resx* 파일과 단순히 문자열 리터럴은 래핑 응용 프로그램을 지역화 하는 오버 헤드를 줄일 수 있습니다. 다른 개발자가 쉽게 하 긴 문자열 리터럴과 함께 작동 하 고 지역화 된 문자열을 업데이트 하려면 쉽게 만들 수 것 처럼 일반적인 작업 흐름을 선호 합니다.

사용 하 여는 `IHtmlLocalizer<T>` HTML을 포함 하는 리소스에 대 한 구현 합니다. `IHtmlLocalizer`HTML은 리소스 문자열이 아닌 리소스 문자열에 서식이 지정 된 인수를 인코딩합니다. 값만 아래 샘플에서 강조 표시 `name` 매개 변수는 HTML로 인코딩하지 합니다.

[!code-csharp[Main](../fundamentals/localization/sample/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

참고: 일반적으로 원하는 텍스트와 HTML이 아닌 필드를 지역화 합니다.

가장 낮은 수준에서 얻을 수 있습니다 `IStringLocalizerFactory` 부재 중 [종속성 주입](dependency-injection.md):

[!code-csharp[Main](localization/sample/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

위의 코드를 보여 줍니다 각각 두 개의 팩터리 메서드를 만듭니다.

영역에서 컨트롤러로 지역화 된 문자열을 분할 하거나 방금 하나 이상 포함 수 있습니다. 샘플 응용 프로그램을 더미 클래스 이름은 `SharedResource` 공유 리소스에 사용 됩니다.

[!code-csharp[Main](localization/sample/Localization/Resources/SharedResource.cs)]

사용 하 여 일부 개발자는 `Startup` 전역 또는 공유 문자열을 포함 하는 클래스입니다.  아래 샘플에는 `InfoController` 및 `SharedResource` 지역화 담당자에 게 사용 됩니다.

[!code-csharp[Main](localization/sample/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a>보기 지역화

`IViewLocalizer` 서비스 제공에 대 한 지역화 된 문자열을 [보기](https://docs.microsoft.com/aspnet/core)합니다. `ViewLocalizer` 클래스는이 인터페이스를 구현 하 고 보기 파일 경로에서 리소스는 위치를 찾습니다. 다음 코드의 기본 구현을 사용 하는 방법을 보여 줍니다 `IViewLocalizer`:

[!code-HTML[Main](localization/sample/Localization/Views/Home/About.cshtml)]

기본 구현은 `IViewLocalizer` 보기의 파일 이름에 따라 리소스 파일을 찾습니다. 공유 전역 리소스 파일을 사용할 수 있는 옵션이 있습니다. `ViewLocalizer`사용 하 여 지역화 구현 `IHtmlLocalizer`Razor HTML 하지 않으므로 지역화 된 문자열을 인코딩합니다. 리소스 문자열을 매개 변수화 할 수 및 `IViewLocalizer` 는 매개 변수가 있지만 리소스 문자열이 아니라 HTML 인코드 합니다. Razor에 다음 태그를 고려 합니다.

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

프랑스어 리소스 파일은 다음 포함할 수 있습니다.

| Key | 값 |
| ----- | ------ |
| `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b> ` |

렌더링된 된 보기에는 리소스 파일에서 HTML 태그를 포함 됩니다.

메모:
- 보기 지역화 "Localization.AspNetCore.TagHelpers" NuGet 패키지가 필요합니다.
- 일반적으로 텍스트와 HTML이 아닌 필드를 지역화 하려고 합니다.

보기에서 공유 리소스 파일을 사용 하려면 주입 `IHtmlLocalizer<T>`:

[!code-HTML[Main](../fundamentals/localization/sample/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a>DataAnnotations 지역화

DataAnnotations 오류 메시지와 지역화 되므로 `IStringLocalizer<T>`합니다. 옵션을 사용 하 여 `ResourcesPath = "Resources"`는 오류 메시지를 `RegisterViewModel` 다음 경로 중 하나에 저장할 수 있습니다.

* Resources/ViewModels.Account.RegisterViewModel.fr.resx
* Resources/ViewModels/Account/RegisterViewModel.fr.resx

[!code-csharp[Main](localization/sample/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

ASP.NET Core MVC 1.1.0 및 더 높은, 비-유효성 검사 특성의 지역화 됩니다. ASP.NET Core 1.0 MVC는 **하지** 유효성 검사 아닌 특성에 대 한 지역화 된 문자열을 조회 합니다.

<a name=one-resource-string-multiple-classes></a>
### <a name="using-one-resource-string-for-multiple-classes"></a>하나의 리소스 문자열을 사용 하 여 다중 클래스

다음 코드에는 여러 클래스를 사용 하 여 유효성 검사 특성에 대 한 리소스 문자열을 사용 하는 방법을 보여 줍니다.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddDataAnnotationsLocalization(options => {
            options.DataAnnotationLocalizerProvider = (type, factory) =>
                factory.Create(typeof(SharedResource));
        });
}
```

위의 코드 `SharedResource` 유효성 검사 메시지 저장 되는 위치에 해당 하는 resx 클래스입니다. 이 접근 방식 DataAnnotations만 사용할 `SharedResource`, 각 클래스에 대 한 리소스 대신 합니다. 

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a>언어 및 지원 되는 문화권에 대 한 지역화 된 리소스를 제공 합니다.  

### <a name="supportedcultures-and-supporteduicultures"></a>SupportedCultures 및 SupportedUICultures

ASP.NET Core를 사용 하면 두 문화권 값을 지정할 수 있습니다 `SupportedCultures` 및 `SupportedUICultures`합니다. [CultureInfo](https://docs.microsoft.com/dotnet/api/system.globalization.cultureinfo) 개체에 대 한 `SupportedCultures` 날짜, 시간, 숫자 및 통화 형식 같은 culture에 종속 된 함수의 결과 결정 합니다. `SupportedCultures`또한 텍스트, 대/소문자 규칙 및 문자열 비교의 정렬 순서를 결정합니다. 참조 [CultureInfo.CurrentCulture](https://docs.microsoft.com/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) 에 대 한 자세한 정보는 서버가 문화권을 가져오는 방식입니다. `SupportedUICultures` 이것은 문자열 해석 결정 (에서 *.resx* 파일)을 조회 하 여는 [ResourceManager](https://docs.microsoft.com/dotnet/api/system.resources.resourcemanager)합니다. `ResourceManager` 의해 결정 되는 culture 관련 문자열을 단순히 조회 `CurrentUICulture`합니다. .NET의 모든 스레드는 `CurrentCulture` 및 `CurrentUICulture` 개체입니다. ASP.NET Core 문화권 종속 기능을 렌더링 하는 경우 이러한 값을 검사 합니다. 예를 들어, 현재 스레드 문화권 "EN-US" (영어, United States)로 설정 되어 있으면 `DateTime.Now.ToLongDateString()` 이지만 if "목요일, 2016 년 2 월 18,", 표시 `CurrentCulture` 설정 된 "ES-ES" 스페인어 (스페인)에 출력 됩니다 "jueves, 18 febrero de-de 2016" 입니다.

## <a name="working-with-resource-files"></a>리소스 파일 작업

리소스 파일은 코드에서 지역화 가능한 문자열을 구분 하는 데 유용한 메커니즘이 있습니다. 기본이 아닌 언어에 대 한 번역 된 문자열은 격리 된 *.resx* 리소스 파일입니다. 예를 들어 라는 스페인어 리소스 파일을 만드는 경우가 *Welcome.es.resx* 포함 된 문자열을 변환 합니다. "es" 스페인어 언어 코드입니다. Visual Studio에서이 리소스 파일을 만들려면:

1. **솔루션 탐색기**, 리소스 파일을 포함 하는 폴더를 마우스 오른쪽 단추로 클릭 > **추가** > **새 항목**합니다.

    ![중첩 된 상황에 맞는 메뉴: 솔루션 탐색기에서 상황에 맞는 메뉴는 리소스에 대 한 열려 있습니다. 두 번째 상황에 맞는 메뉴 강조 표시 된 새 항목 명령을 보여 주는 추가 대 한 쉽습니다.](localization/_static/newi.png)

2. 에 **설치 된 템플릿 검색** 상자 "resource"를 입력 하 고 파일 이름을 지정 합니다.

    ![새 항목 추가 대화 상자](localization/_static/res.png)

3. 에 키 값 (네이티브 문자열)을 입력의 **이름** 열과의 번역된 된 문자열의 **값** 열입니다.

    ![이름 열에 hello 및 값 열에 있는 단어 Hola (스페인어에서 Hello) Welcome.es.resx 파일 (스페인어 시작 리소스 파일)](localization/_static/hola.png)

    Visual Studio는 표시 된 *Welcome.es.resx* 파일입니다.

    ![시작 스페인어 (es) 리소스 파일을 나타내는 솔루션 탐색기](localization/_static/se.png)

<a name="error"></a>

Visual Studio 2017 미리 보기 버전 15.3를 사용 하는 경우에 리소스 편집기에서 오류 표시기를 볼 수 있습니다. 제거는 *ResXFileCodeGenerator* 에서 값의 *사용자 지정 도구* 속성 표에서이 오류 메시지를 방지 하려면:

![Resx 편집기](localization/_static/err.png)

또한이 오류를 무시할 수 있습니다. 다음 릴리스에서이 문제를 해결 하기 위해 노력 합니다.

## <a name="resource-file-naming"></a>리소스 파일 이름 지정

어셈블리 이름에서 뺀 해당 클래스의 전체 유형 이름에 대 한 리소스 이름이 지정 됩니다. 인 주 어셈블리는 프로젝트에서 예를 들어 프랑스 리소스 `LocalizationWebsite.Web.dll` 클래스에 대 한 `LocalizationWebsite.Web.Startup` 이름이 *Startup.fr.resx*합니다. 클래스에 대 한 리소스 `LocalizationWebsite.Web.Controllers.HomeController` 이름이 *Controllers.HomeController.fr.resx*합니다. 대상된 클래스의 네임 스페이스는 어셈블리 이름과 동일 하지는 경우 전체 유형 이름입니다. 예를 들어 샘플에서 프로젝트 형식에 대 한 리소스 `ExtraNamespace.Tools` 이름이 *ExtraNamespace.Tools.fr.resx*합니다.

샘플 프로젝트에는 `ConfigureServices` 메서드 집합은 `ResourcesPath` 홈 컨트롤러의 프랑스어 리소스 파일에 대 한 프로젝트 상대 경로 하므로 "리소스" *Resources/Controllers.HomeController.fr.resx*합니다. 또는 리소스 파일을 구성 하려면 폴더를 사용할 수 있습니다. 홈 컨트롤러에 대 한 경로 것 *Resources/Controllers/HomeController.fr.resx*합니다. 사용 하지 않는 경우는 `ResourcesPath` 옵션을는 *.resx* 프로젝트 기본 디렉터리에 파일을 거쳐야 합니다. 리소스 파일에 대 한 `HomeController` 이름이 *Controllers.HomeController.fr.resx*합니다. 리소스 파일을 구성 하는 방법에 따라 점 또는 경로 명명 규칙을 사용 하 여 선택 합니다.

| 리소스 이름 | 점 또는 경로 이름 지정 |
| ------------   | ------------- |
| Resources/Controllers.HomeController.fr.resx | 점  |
| Resources/Controllers/HomeController.fr.resx  | Path |
|    |     |

리소스 파일을 사용 하 여 `@inject IViewLocalizer` Razor 뷰는 유사한 패턴을 따릅니다. 점을 지정 하거나 경로 이름 지정을 사용 하 여 보기에 대 한 리소스 파일을 이름은 수 있습니다. Razor 뷰 리소스 파일의 관련된 보기 파일의 경로 모방 합니다. 설정 가정는 `ResourcesPath` "리소스"와 연결 된 프랑스어 리소스 파일의 *Views/Home/About.cshtml* 보기에서 다음 중 하나가 될 수 있습니다.

* Resources/Views/Home/About.fr.resx

* Resources/Views.Home.About.fr.resx

사용 하지 않는 경우는 `ResourcesPath` 옵션을는 *.resx* 보기는 보기와 같은 폴더에에 대 한 파일입니다.

".Fr" 문화권 지정자를 제거 하 고 쿠키 또는 기타 메커니즘) (통해 프랑스어로 설정 하는 문화권, 기본 리소스 파일을 읽고 문자열이 지역화 됩니다. 리소스 관리자는 culture 지정자 없이 *.resx 파일을 처리 하는 사용자의 요청 된 문화권 아무 것도 충족 하는 경우 기본 또는 대체 (fallback) 리소스를 지정 합니다. 요청 된 문화권의 리소스를 없습니다 기본 리소스 파일을가 필요한 경우 키에만 반환 하려면.

### <a name="generating-resource-files-with-visual-studio"></a>Visual Studio를 사용 하 여 리소스 파일을 생성합니다.

파일 이름에는 문화권 하지 않고 Visual Studio에서 리소스 파일을 만드는 경우 (예를 들어 *Welcome.resx*), Visual Studio는 각 문자열에 대 한 속성을 갖는 C# 클래스를 만듭니다. 이 결과 원하지 ASP.NET 코어; 일반적으로 기본값이 없는 *.resx* 리소스 파일 (A *.resx* 문화권 이름 없이 파일). 만들어야 하는 것이 좋습니다는 *.resx* 문화권 이름으로 파일 (예를 들어 *Welcome.fr.resx*). 만들 때 한 *.resx* 문화권 이름을 사용할 경우 Visual Studio 파일 클래스 파일을 생성 하지 것입니다. 예상 많은 개발자 됩니다 **하지** 기본 언어 리소스 파일을 만듭니다.

### <a name="adding-other-cultures"></a>다른 문화권에 맞게 추가

각 언어 및 culture 조합 (기본 언어)와 다른 고유한 리소스 파일이 필요합니다. ISO 언어 코드를 파일 이름에 있는 새 리소스 파일을 만들어 서로 다른 문화권 및 로캘에 대 한 리소스 파일을 만듭니다 (예를 들어 **en-우리**, **fr ca**, 및  **en gb**). 파일 이름 사이 이러한 ISO 코드 위치 및 *.resx* 에서 같이 파일 이름 확장명을 *Welcome.es MX.resx* (스페인어/멕시코). 중립 문화권에 따라 언어를 지정 하려면 국가 코드를 제거 (`MX` 앞의 예제에서). 스페인어 중립 문화권 리소스 파일 이름이 *Welcome.es.resx*합니다.

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a>각 요청에 대 한 언어/문화권을 선택 하는 전략을 구현 합니다.  

### <a name="configuring-localization"></a>지역화를 구성합니다.

지역화에 구성 된 `ConfigureServices` 메서드:

[!code-csharp[Main](localization/sample/Localization/Program.cs?name=snippet1)]

* `AddLocalization`서비스 컨테이너를 지역화 서비스를 추가합니다. 위의 코드는 또한 "리소스"에 대 한 리소스 경로 설정합니다.

* `AddViewLocalization`지역화 된 보기 파일에 대 한 지원을 추가합니다. 이 샘플 보기에서 지역화 보기 파일 접미사를 기반으로 합니다. 예를 들어 "fr"에 *Index.fr.cshtml* 파일입니다.

* `AddDataAnnotationsLocalization`각 언어에 대 한 지원을 추가 `DataAnnotations` 유효성 검사를 통해 메시지 `IStringLocalizer` 추상화 합니다.

### <a name="localization-middleware"></a>지역화 미들웨어

현재 요청에 설정 된 지역화에 [미들웨어](middleware.md)합니다. 지역화 미들웨어에서 사용 되는 `Configure` 방식의 *Program.cs* 파일입니다. 확인 요청 문화권을 확인할 수 있는 모든 미들웨어 하기 전에 지역화 미들웨어를 구성 해야 합니다 (예를 들어 `app.UseMvcWithDefaultRoute()`).

[!code-csharp[Main](localization/sample/Localization/Program.cs?name=snippet2)]

`UseRequestLocalization`초기화 한 `RequestLocalizationOptions` 개체입니다. 모든 요청 목록에서의 `RequestCultureProvider` 에 `RequestLocalizationOptions` 열거 및 요청 culture를 성공적으로 결정할 수 있는 첫 번째 공급자가 사용 됩니다. 기본 공급자에서 제공 된 `RequestLocalizationOptions` 클래스:

1. `QueryStringRequestCultureProvider`
2. `CookieRequestCultureProvider`
3. `AcceptLanguageHeaderRequestCultureProvider`

기본 목록 가장 구체적인 정보부터 가장 구체적으로 이동합니다. 문서의 뒷부분에 순서를 변경도 사용자 지정 문화권 공급자를 추가 하는 방법을 살펴보겠습니다. 어떤 공급자가 요청 문화권을 확인할 수 있으면는 `DefaultRequestCulture` 사용 됩니다.

### <a name="querystringrequestcultureprovider"></a>QueryStringRequestCultureProvider

일부 응용 프로그램 쿼리 문자열을 사용 하 여 설정 하는 [culture 및 UL culture](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx)합니다. 쿠키 또는 Accept-language 헤더 접근 방식을 사용 하는 응용 프로그램의 경우 URL에 쿼리 문자열을 추가 코드를 디버깅 및 테스트에 유용 합니다. 기본적으로는 `QueryStringRequestCultureProvider` 에서 첫 번째 지역화 공급자로 등록는 `RequestCultureProvider` 목록입니다. 쿼리 문자열 매개 변수를 전달 `culture` 및 `ui-culture`합니다. 다음 예제에서는 특정 문화권 (언어 및 지역) 스페인어/멕시코 설정합니다.

   `http://localhost:5000/?culture=es-MX&ui-culture=es-MX`

둘 중 하나에 전달 하는 경우 (`culture` 또는 `ui-culture`), 쿼리 문자열 공급자에 전달 된 것을 사용 하 여 두 값을 설정 합니다. 예를 들어 문화권만 설정 됩니다 모두 설정 된 `Culture` 및 `UICulture`:

   `http://localhost:5000/?culture=es-MX`

### <a name="cookierequestcultureprovider"></a>CookieRequestCultureProvider

프로덕션 앱에서 ASP.NET Core 문화권 쿠키와 함께 culture를 설정 하는 메커니즘을 제공 경우가 많습니다. 사용 하 여는 `MakeCookieValue` 메서드를 쿠키를 만듭니다.

`CookieRequestCultureProvider` `DefaultCookieName` 문화권 정보를 사용자를 추적 하는 데 기본 쿠키 이름인의 기본 설정으로 반환 합니다. 기본 쿠키 이름인 "입니다. AspNetCore.Culture "입니다.

쿠키 형식은 `c=%LANGCODE%|uic=%LANGCODE%`여기서 `c` 은 `Culture` 및 `uic` 은 `UICulture`, 예:

    c=en-UK|uic=en-US

문화권 정보 및 UI 문화권 중 하나만 지정 하면 지정된 된 문화권 한 문화권 정보 및 UI 문화권을 모두 사용 됩니다.

### <a name="the-accept-language-http-header"></a>Accept-language HTTP 헤더

[Accept-language 헤더](https://www.w3.org/International/questions/qa-accept-lang-locales) 대부분의 브라우저에서 설정할 수는 않았고 원래 사용자의 언어를 지정 합니다. 이 설정은 브라우저 전송 하도록 설정 된 또는 기본 운영 체제에서 상속한 것을 나타냅니다. Accept-language HTTP 헤더의 브라우저 요청 사용자의 기본 언어를 검색 하는 infallible 있는 것은 아닙니다 (참조 [브라우저의 언어 기본 설정](https://www.w3.org/International/questions/qa-lang-priorities.en.php)). 프로덕션 응용 프로그램 사용자는 선택한 culture 지정할 수 있는 방법을 포함 해야 합니다.

### <a name="setting-the-accept-language-http-header-in-ie"></a>IE에서 Accept-language HTTP 헤더 설정

1. 기어 아이콘에서을 누릅니다 **인터넷 옵션**합니다.

2. 탭 **언어**합니다.

    ![인터넷 옵션](localization/_static/lang.png)

3. 탭 **언어 기본 설정 설정**합니다.

4. 탭 **언어 추가**합니다.

5. 언어를 추가 합니다.

6. 언어를 차례로 누릅니다 **위로 이동**합니다.

### <a name="using-a-custom-provider"></a>사용자 지정 공급자를 사용 하 여

데이터베이스에 해당 언어 및 culture를 저장 하 여 소비자에 게 알리는 한다고 가정 합니다. 사용자에 대 한 이러한 값을 조회 하는 공급자를 작성할 수 있습니다. 다음 코드에는 사용자 지정 공급자를 추가 하는 방법을 보여 줍니다.

```csharp
private const string enUSCulture = "en-US";

services.Configure<RequestLocalizationOptions>(options =>
{
    var supportedCultures = new[]
    {
        new CultureInfo(enUSCulture),
        new CultureInfo("fr")
    };

    options.DefaultRequestCulture = new RequestCulture(culture: enUSCulture, uiCulture: enUSCulture);
    options.SupportedCultures = supportedCultures;
    options.SupportedUICultures = supportedCultures;

    options.RequestCultureProviders.Insert(0, new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
});
```

사용 하 여 `RequestLocalizationOptions` 지역화 공급자를 추가 하거나 제거 합니다.

### <a name="setting-the-culture-programmatically"></a>프로그래밍 방식으로 문화권 설정

이 샘플 **Localization.StarterWeb** 프로젝션 할 [GitHub](https://github.com/aspnet/entropy) 설정 하는 UI가 포함 된 `Culture`합니다. *Views/Shared/_SelectLanguagePartial.cshtml* 파일을 사용 하면 지원 되는 culture의 목록에서 culture를 지정할 수 있습니다.

[!code-HTML[Main](localization/sample/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

*Views/Shared/_SelectLanguagePartial.cshtml* 파일이에 추가 되는 `footer` 모든 보기에 사용할 수 있도록 레이아웃 파일의 섹션:

[!code-HTML[Main](localization/sample/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

`SetLanguage` 메서드 문화권 쿠키를 설정 합니다.

[!code-csharp[Main](localization/sample/Localization/Controllers/HomeController.cs?range=57-67)]

연결할 수 없습니다는 *_SelectLanguagePartial.cshtml* 이 프로젝트에 대 한 샘플 코드에 있습니다. **Localization.StarterWeb** 프로젝션 할 [GitHub](https://github.com/aspnet/entropy) 흐름 하는 코드가 `RequestLocalizationOptions` 는 Razor 통해 부분에 [종속성 주입](dependency-injection.md) 컨테이너입니다.

## <a name="globalization-and-localization-terms"></a>전역화 및 지역화 용어

응용 프로그램을 지역화 하는 프로세스는 또한 일반적으로 최신 소프트웨어 개발에 사용 되는 관련 문자 집합에 대 한 기본적인 이해 및 관련 된 문제에 대 한 이해가 필요 합니다. 숫자 (코드)로 텍스트를 저장 하는 모든 컴퓨터, 있지만 다른 시스템 다른 번호를 사용 하 여 동일한 텍스트를 저장 합니다. 지역화 과정 번역 된 특정 문화권/로캘에 대 한 응용 프로그램 사용자 인터페이스 (UI)를 참조 합니다.

[지역화 가능성](https://docs.microsoft.com/dotnet/standard/globalization-localization/localizability-review) 는 세계화 된 응용 프로그램 지역화 하는 데 사용할 준비가 되어 있는지 확인 하기 위한 중간 프로세스입니다.

[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) 문화권 이름에 대 한 서식을 "<languagecode2>-< country/regioncode2 >", 여기서 <languagecode2> 언어 코드 이며 < country/regioncode2 > 하위 문화권 코드입니다. 예를 들어 `es-CL` 스페인어 (칠레)에 대 한 `en-US` 영어 (미국) 및 `en-AU` 영어 (오스트레일리아)에 대 한 합니다. [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) 는 언어와 관련 된 두 문자의 소문자 문화권 코드는 ISO 639 및는 ISO 3166 국가 또는 지역와 관련 된 두 문자의 대문자 하위 문화권 코드의 조합입니다.  참조 [언어 문화권 이름](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx)합니다.

국제화 "I18N" 종종 약어로 됩니다. 약어는 첫 번째 및 마지막 문자를 사용 하 고 있으므로 18 둘 간의 문자의 수 첫째 문자의 수에 대 한 "I"와 "N" 마지막 합니다. (G11N) 전역화 및 지역화 (L10N)에 마찬가지입니다.

조건:

* 세계화 (G11N): 다른 언어와 지역 지원 앱을 만드는 프로세스입니다.
* 지역화 (L10N): 지정 된 언어 및 지역에 대 한 앱을 사용자 지정 프로세스입니다.
* 국제화 (I18N): 전역화 및 지역화를 모두 설명합니다.
* 언어 및 필요에 따라 영역 문화권: 않습니다.
* 중립 문화권: 지정한 언어에 있지만 지역에 있지는 문화권입니다. (예를 들어 "en", "es")
* 특정 문화권:는 지정한 언어 및 지역 하는 문화권입니다. (예를 들어 "EN-US", "EN-GB", "es CL")
* 로캘: 로캘의 문화권으로 동일 합니다.

## <a name="additional-resources"></a>추가 리소스

* [Localization.StarterWeb 프로젝트](https://github.com/aspnet/entropy) 문서에 사용 합니다.
* [Visual Studio의 리소스 파일](https://docs.microsoft.com/cpp/windows/resource-files-visual-studio)
* [.Resx 파일의 리소스](https://docs.microsoft.com/dotnet/framework/resources/working-with-resx-files-programmatically)