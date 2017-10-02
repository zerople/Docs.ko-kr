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
# <a name="configure-portable-object-localization-with-orchard-core"></a><span data-ttu-id="907cb-104">휴대용 개체 지역화 Orchard 코어를 사용 하 여 구성</span><span class="sxs-lookup"><span data-stu-id="907cb-104">Configure portable object localization with Orchard Core</span></span>

<span data-ttu-id="907cb-105">여 [Sébastien Ros](https://github.com/sebastienros) 및 [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="907cb-105">By [Sébastien Ros](https://github.com/sebastienros) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="907cb-106">이 문서에서는 ASP.NET Core 응용 프로그램을 이식 가능한 개체 (PO) 파일을 사용 하는 단계를 안내는 [Orchard 코어](https://github.com/OrchardCMS/OrchardCore) 프레임 워크입니다.</span><span class="sxs-lookup"><span data-stu-id="907cb-106">This article walks through the steps for using Portable Object (PO) files in an ASP.NET Core application with the [Orchard Core](https://github.com/OrchardCMS/OrchardCore) framework.</span></span>

<span data-ttu-id="907cb-107">**참고:** Orchard 핵심 Microsoft 제품이 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="907cb-107">**Note:** Orchard Core is not a Microsoft product.</span></span> <span data-ttu-id="907cb-108">따라서 Microsoft는이 기능에 대 한 지원 되지 않습니다을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="907cb-108">Consequently, Microsoft provides no support for this feature.</span></span>

<span data-ttu-id="907cb-109">[보거나 다운로드 샘플 코드](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/localization/sample/POLocalization) ([다운로드 하는 방법을](xref:tutorials/index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="907cb-109">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/localization/sample/POLocalization) ([how to download](xref:tutorials/index#how-to-download-a-sample))</span></span>

## <a name="what-is-a-po-file"></a><span data-ttu-id="907cb-110">PO 파일 이란 무엇 인가요?</span><span class="sxs-lookup"><span data-stu-id="907cb-110">What is a PO file?</span></span>

<span data-ttu-id="907cb-111">PO 파일은 지정된 된 언어에 대 한 번역 된 문자열을 포함 하는 텍스트 파일로 배포 됩니다.</span><span class="sxs-lookup"><span data-stu-id="907cb-111">PO files are distributed as text files containing the translated strings for a given language.</span></span> <span data-ttu-id="907cb-112">PO 파일을 대신 사용의 몇 가지 이점을 *.resx* 파일 포함:</span><span class="sxs-lookup"><span data-stu-id="907cb-112">Some advantages of using PO files instead *.resx* files include:</span></span>
- <span data-ttu-id="907cb-113">PO 파일 지원 복수 적용 합니다. *.resx* 파일 복수 적용을 지원 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="907cb-113">PO files support pluralization; *.resx* files don't support pluralization.</span></span>
- <span data-ttu-id="907cb-114">PO 파일 처럼 컴파일되지 않습니다 *.resx* 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="907cb-114">PO files aren't compiled like *.resx* files.</span></span> <span data-ttu-id="907cb-115">이와 같이 특수 한 도구 및 빌드 단계 사용할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="907cb-115">As such, specialized tooling and build steps aren't required.</span></span>
- <span data-ttu-id="907cb-116">PO 파일 공동 온라인 편집 도구와 잘 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="907cb-116">PO files work well with collaborative online editing tools.</span></span>

### <a name="example"></a><span data-ttu-id="907cb-117">예제</span><span class="sxs-lookup"><span data-stu-id="907cb-117">Example</span></span>

<span data-ttu-id="907cb-118">복수 형태의으로 하나를 포함 하 여 프랑스어로 두 개의 문자열에 대 한 번역을 포함 하는 샘플 PO 파일 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="907cb-118">Here is a sample PO file containing the translation for two strings in French, including one with its plural form:</span></span>

<span data-ttu-id="907cb-119">*fr.po*</span><span class="sxs-lookup"><span data-stu-id="907cb-119">*fr.po*</span></span>

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

<span data-ttu-id="907cb-120">이 예제에서는 다음 구문을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="907cb-120">This example uses the following syntax:</span></span>

- <span data-ttu-id="907cb-121">`#:`: 변환 해야 하는 문자열의 컨텍스트를 나타내는 설명입니다.</span><span class="sxs-lookup"><span data-stu-id="907cb-121">`#:`: A comment indicating the context of the string to be translated.</span></span> <span data-ttu-id="907cb-122">같은 문자열이 사용 되는 위치에 따라 다르게 변환 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="907cb-122">The same string might be translated differently depending on where it is being used.</span></span>
- <span data-ttu-id="907cb-123">`msgid`: 변환 되지 않은 문자열입니다.</span><span class="sxs-lookup"><span data-stu-id="907cb-123">`msgid`: The untranslated string.</span></span>
- <span data-ttu-id="907cb-124">`msgstr`: 번역 된 문자열입니다.</span><span class="sxs-lookup"><span data-stu-id="907cb-124">`msgstr`: The translated string.</span></span>

<span data-ttu-id="907cb-125">복수 적용 지원의 경우 더 많은 항목을 정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="907cb-125">In the case of pluralization support, more entries can be defined.</span></span>

- <span data-ttu-id="907cb-126">`msgid_plural`변환 되지 않음된: 복수형 문자열입니다.</span><span class="sxs-lookup"><span data-stu-id="907cb-126">`msgid_plural`: The untranslated plural string.</span></span>
- <span data-ttu-id="907cb-127">`msgstr[0]`0의 경우: 번역 된 문자열입니다.</span><span class="sxs-lookup"><span data-stu-id="907cb-127">`msgstr[0]`: The translated string for the case 0.</span></span>
- <span data-ttu-id="907cb-128">`msgstr[N]`Case 명사에 대 한 번역 된 문자열:</span><span class="sxs-lookup"><span data-stu-id="907cb-128">`msgstr[N]`: The translated string for the case N.</span></span>

<span data-ttu-id="907cb-129">PO 파일 사양이 있습니다 [여기](https://www.gnu.org/savannah-checkouts/gnu/gettext/manual/html_node/PO-Files.html)합니다.</span><span class="sxs-lookup"><span data-stu-id="907cb-129">The PO file specification can be found [here](https://www.gnu.org/savannah-checkouts/gnu/gettext/manual/html_node/PO-Files.html).</span></span>

## <a name="configuring-po-file-support-in-aspnet-core"></a><span data-ttu-id="907cb-130">ASP.NET Core에서 PO 파일 지원 구성</span><span class="sxs-lookup"><span data-stu-id="907cb-130">Configuring PO file support in ASP.NET Core</span></span>

<span data-ttu-id="907cb-131">이 예제에서는 Visual Studio 2017 프로젝트 템플릿에서 생성 된 ASP.NET Core MVC 응용 프로그램을 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="907cb-131">This example is based on an ASP.NET Core MVC application generated from a Visual Studio 2017 project template.</span></span>

### <a name="referencing-the-package"></a><span data-ttu-id="907cb-132">패키지를 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="907cb-132">Referencing the package</span></span>

<span data-ttu-id="907cb-133">에 대 한 참조 추가 `OrchardCore.Localization.Core` NuGet 패키지 합니다.</span><span class="sxs-lookup"><span data-stu-id="907cb-133">Add a reference to the `OrchardCore.Localization.Core` NuGet package.</span></span> <span data-ttu-id="907cb-134">에 있는 [MyGet](https://www.myget.org/) 다음 패키지 소스에서: https://www.myget.org/F/orchardcore-preview/api/v3/index.json</span><span class="sxs-lookup"><span data-stu-id="907cb-134">It is available on [MyGet](https://www.myget.org/) at the following package source: https://www.myget.org/F/orchardcore-preview/api/v3/index.json</span></span>

<span data-ttu-id="907cb-135">*.csproj* 이제 다음과 비슷한 줄이 포함 파일 (버전 번호가 달라질 수 있음):</span><span class="sxs-lookup"><span data-stu-id="907cb-135">The *.csproj* file now contains a line similar to the following (version number may vary):</span></span>

[!code-xml[Main](localization/sample/POLocalization/POLocalization.csproj?range=9)]

### <a name="registering-the-service"></a><span data-ttu-id="907cb-136">서비스를 등록 하는 중</span><span class="sxs-lookup"><span data-stu-id="907cb-136">Registering the service</span></span>

<span data-ttu-id="907cb-137">필요한 서비스를 추가 `ConfigureServices` 방식의 *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="907cb-137">Add the required services to the `ConfigureServices` method of *Startup.cs*:</span></span>

[!code-csharp[Main](localization/sample/POLocalization/Startup.cs?name=snippet_ConfigureServices&highlight=4-21)]

<span data-ttu-id="907cb-138">필요한 미들웨어를 추가 `Configure` 방식의 *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="907cb-138">Add the required middleware to the `Configure` method of *Startup.cs*:</span></span>

[!code-csharp[Main](localization/sample/POLocalization/Startup.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="907cb-139">Razor 뷰를 선택 하려면 다음 코드를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="907cb-139">Add the following code to your Razor view of choice.</span></span> <span data-ttu-id="907cb-140">*About.cshtml* 이 예에서 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="907cb-140">*About.cshtml* is used in this example.</span></span>

[!code-cshtml[Main](localization/sample/POLocalization/Views/Home/About.cshtml)]

<span data-ttu-id="907cb-141">`IViewLocalizer` 인스턴스가 삽입 되 고 "Hello world!" 텍스트를 번역 하는 데 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="907cb-141">An `IViewLocalizer` instance is injected and used to translate the text "Hello world!".</span></span>

### <a name="creating-a-po-file"></a><span data-ttu-id="907cb-142">PO 파일 만들기</span><span class="sxs-lookup"><span data-stu-id="907cb-142">Creating a PO file</span></span>

<span data-ttu-id="907cb-143">라는 파일을 만들어  *<culture code>.po* 응용 프로그램 루트 폴더에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="907cb-143">Create a file named *<culture code>.po* in your application root folder.</span></span> <span data-ttu-id="907cb-144">이 예제에서는 파일 이름이 *fr.po* 프랑스어 사용 되기 때문에:</span><span class="sxs-lookup"><span data-stu-id="907cb-144">In this example, the file name is *fr.po* because the French language is used:</span></span>

[!code-text[Main](localization/sample/POLocalization/fr.po)]

<span data-ttu-id="907cb-145">이 파일에는 변환할 문자열 및 프랑스어 번역 된 문자열을 모두 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="907cb-145">This file stores both the string to translate and the French-translated string.</span></span> <span data-ttu-id="907cb-146">번역 필요한 경우 해당 부모 문화권으로 되돌립니다.</span><span class="sxs-lookup"><span data-stu-id="907cb-146">Translations revert to their parent culture, if necessary.</span></span> <span data-ttu-id="907cb-147">이 예제는 *fr.po* 파일은 요청 된 문화권이 사용 `fr-FR` 또는 `fr-CA`합니다.</span><span class="sxs-lookup"><span data-stu-id="907cb-147">In this example, the *fr.po* file is used if the requested culture is `fr-FR` or `fr-CA`.</span></span>

### <a name="testing-the-application"></a><span data-ttu-id="907cb-148">응용 프로그램 테스트</span><span class="sxs-lookup"><span data-stu-id="907cb-148">Testing the application</span></span>

<span data-ttu-id="907cb-149">응용 프로그램을 실행 하 고 URL로 이동 `/Home/About`합니다.</span><span class="sxs-lookup"><span data-stu-id="907cb-149">Run your application, and navigate to the URL `/Home/About`.</span></span> <span data-ttu-id="907cb-150">텍스트 **Hello world!**</span><span class="sxs-lookup"><span data-stu-id="907cb-150">The text **Hello world!**</span></span> <span data-ttu-id="907cb-151">´ ù.</span><span class="sxs-lookup"><span data-stu-id="907cb-151">is displayed.</span></span>

<span data-ttu-id="907cb-152">URL로 이동 `/Home/About?culture=fr-FR`합니다.</span><span class="sxs-lookup"><span data-stu-id="907cb-152">Navigate to the URL `/Home/About?culture=fr-FR`.</span></span> <span data-ttu-id="907cb-153">텍스트 **Bonjour le monde!**</span><span class="sxs-lookup"><span data-stu-id="907cb-153">The text **Bonjour le monde!**</span></span> <span data-ttu-id="907cb-154">´ ù.</span><span class="sxs-lookup"><span data-stu-id="907cb-154">is displayed.</span></span>

## <a name="pluralization"></a><span data-ttu-id="907cb-155">복수 적용</span><span class="sxs-lookup"><span data-stu-id="907cb-155">Pluralization</span></span>

<span data-ttu-id="907cb-156">PO 파일 카디널리티에 따라 다르게 변환 해야 하는 같은 문자열이 필요한 경우 유용 복수 적용 폼을 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="907cb-156">PO files support pluralization forms, which is useful when the same string needs to be translated differently based on a cardinality.</span></span> <span data-ttu-id="907cb-157">이 작업은 각 언어 문자열을 사용 하 여 카디널리티에 따라 선택 하는 사용자 지정 규칙을 정의 한다는 점에서 복잡 한 이루어집니다.</span><span class="sxs-lookup"><span data-stu-id="907cb-157">This task is made complicated by the fact that each language defines custom rules to select which string to use based on the cardinality.</span></span>

<span data-ttu-id="907cb-158">Orchard 지역화 패키지는 이러한 다른 복수 형태를 자동으로 호출 하는 API를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="907cb-158">The Orchard Localization package provides an API to invoke these different plural forms automatically.</span></span>

### <a name="creating-pluralization-po-files"></a><span data-ttu-id="907cb-159">복수 적용 PO 파일 만들기</span><span class="sxs-lookup"><span data-stu-id="907cb-159">Creating pluralization PO files</span></span>

<span data-ttu-id="907cb-160">다음 콘텐츠를 앞에서 언급 한 추가 *fr.po* 파일:</span><span class="sxs-lookup"><span data-stu-id="907cb-160">Add the following content to the previously mentioned *fr.po* file:</span></span>

```text
msgid "There is one item."
msgid_plural "There are {0} items."
msgstr[0] "Il y a un élément."
msgstr[1] "Il y a {0} éléments."
```

<span data-ttu-id="907cb-161">참조 [PO 파일 이란 무엇 인가요?](#what-is-a-po-file) 에 대 한 설명은이 예에서 각 항목의 의미 합니다.</span><span class="sxs-lookup"><span data-stu-id="907cb-161">See [What is a PO file?](#what-is-a-po-file) for an explanation of what each entry in this example represents.</span></span>

### <a name="adding-a-language-using-different-pluralization-forms"></a><span data-ttu-id="907cb-162">다른 복수 적용 양식을 사용 하 여 언어를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="907cb-162">Adding a language using different pluralization forms</span></span>

<span data-ttu-id="907cb-163">영어와 프랑스어 문자열 앞의 예제에서 사용 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="907cb-163">English and French strings were used in the previous example.</span></span> <span data-ttu-id="907cb-164">영어와 프랑스어 두 복수 적용 형식에만 있고 동일한 폼 규칙을 공유 하므로 하나의 카디널리티는 첫 번째 복수 형태의에 매핑되어 있는지 합니다.</span><span class="sxs-lookup"><span data-stu-id="907cb-164">English and French have only two pluralization forms and share the same form rules, which is that a cardinality of one is mapped to the first plural form.</span></span> <span data-ttu-id="907cb-165">다른 카디널리티는 두 번째 복수 형태의에 매핑됩니다.</span><span class="sxs-lookup"><span data-stu-id="907cb-165">Any other cardinality is mapped to the second plural form.</span></span>

<span data-ttu-id="907cb-166">일부 언어는 동일한 규칙을 공유합니다.</span><span class="sxs-lookup"><span data-stu-id="907cb-166">Not all languages share the same rules.</span></span> <span data-ttu-id="907cb-167">이 세 가지 복수 형태는 체코어 언어를 가진 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="907cb-167">This is illustrated with the Czech language, which has three plural forms.</span></span>

<span data-ttu-id="907cb-168">만들기는 `cs.po` 다음과 같이 파일 및 복수 적용은 세 가지 다른 번역을 요구 하는 방법을 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="907cb-168">Create the `cs.po` file as follows, and note how the pluralization needs three different translations:</span></span>

[!code-text[Main](localization/sample/POLocalization/cs.po)]

<span data-ttu-id="907cb-169">체코어 지역화를 허용 하려면 추가 `"cs"` 에서 지원 되는 culture의 목록에는 `ConfigureServices` 메서드:</span><span class="sxs-lookup"><span data-stu-id="907cb-169">To accept Czech localizations, add `"cs"` to the list of supported cultures in the `ConfigureServices` method:</span></span>

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

<span data-ttu-id="907cb-170">편집 된 *Views/Home/About.cshtml* 여러 카디널리티에 대 한 복수, 지역화 된 문자열을 렌더링 하는 파일:</span><span class="sxs-lookup"><span data-stu-id="907cb-170">Edit the *Views/Home/About.cshtml* file to render localized, plural strings for several cardinalities:</span></span>

```cshtml
<p>@Localizer.Plural(1, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(2, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(5, "There is one item.", "There are {0} items.")</p>
```

<span data-ttu-id="907cb-171">**참고:** 실제 시나리오에서는 변수 수를 나타내는 데 사용할 수는 있습니다.</span><span class="sxs-lookup"><span data-stu-id="907cb-171">**Note:** In a real world scenario, a variable would be used to represent the count.</span></span> <span data-ttu-id="907cb-172">여기에서 매우 특정 한 경우를 노출 하는 3 가지 값을 사용 하 여 동일한 코드를 반복 합니다.</span><span class="sxs-lookup"><span data-stu-id="907cb-172">Here, we repeat the same code with three different values to expose a very specific case.</span></span>

<span data-ttu-id="907cb-173">문화권을 전환 되 면 다음 화면이 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="907cb-173">Upon switching cultures, you see the following:</span></span>

<span data-ttu-id="907cb-174">`/Home/About`의 경우:</span><span class="sxs-lookup"><span data-stu-id="907cb-174">For `/Home/About`:</span></span>

```html
There is one item.
There are 2 items.
There are 5 items.
```

<span data-ttu-id="907cb-175">`/Home/About?culture=fr`의 경우:</span><span class="sxs-lookup"><span data-stu-id="907cb-175">For `/Home/About?culture=fr`:</span></span>

```html
Il y a un élément.
Il y a 2 éléments.
Il y a 5 éléments.
```

<span data-ttu-id="907cb-176">`/Home/About?culture=cs`의 경우:</span><span class="sxs-lookup"><span data-stu-id="907cb-176">For `/Home/About?culture=cs`:</span></span>

```html
Existuje jedna položka.
Existují 2 položky.
Existuje 5 položek.
```

<span data-ttu-id="907cb-177">참고 체코어 문화권에 대 한 세 가지 번역 다르기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="907cb-177">Note that for the Czech culture, the three translations are different.</span></span> <span data-ttu-id="907cb-178">프랑스어 및 영어 문화권 마지막 두 개의 번역 된 문자열에 대 한 동일한 구조를 공유합니다.</span><span class="sxs-lookup"><span data-stu-id="907cb-178">The French and English cultures share the same construction for the two last translated strings.</span></span>

## <a name="advanced-tasks"></a><span data-ttu-id="907cb-179">고급 작업</span><span class="sxs-lookup"><span data-stu-id="907cb-179">Advanced tasks</span></span>

### <a name="contextualizing-strings"></a><span data-ttu-id="907cb-180">Contextualizing 문자열</span><span class="sxs-lookup"><span data-stu-id="907cb-180">Contextualizing strings</span></span>

<span data-ttu-id="907cb-181">응용 프로그램에는 종종 여러 위치에서 변환 하는 문자열이 들어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="907cb-181">Applications often contain the strings to be translated in several places.</span></span> <span data-ttu-id="907cb-182">동일한 문자열 (Razor 뷰 또는 클래스 파일)는 응용 프로그램 내의 특정 위치에서 다른 변환이 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="907cb-182">The same string may have a different translation in certain locations within an app (Razor views or class files).</span></span> <span data-ttu-id="907cb-183">PO 파일 분류 되 고 표시 되는 문자열을 사용할 수 있는 한 파일 컨텍스트의 개념을 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="907cb-183">A PO file supports the notion of a file context, which can be used to categorize the string being represented.</span></span> <span data-ttu-id="907cb-184">파일 컨텍스트를 사용 하는 문자열 번역할 수 있습니다 다르게 파일 컨텍스트 (또는 파일 컨텍스트 부족)에 따라.</span><span class="sxs-lookup"><span data-stu-id="907cb-184">Using a file context, a string can be translated differently, depending on the file context (or lack of a file context).</span></span>

<span data-ttu-id="907cb-185">PO 지역화 서비스에는 전체 클래스 또는 문자열로 변환할 때 사용 되는 뷰 이름을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="907cb-185">The PO localization services use the name of the full class or the view that is used when translating a string.</span></span> <span data-ttu-id="907cb-186">에 값을 설정 하 여 이렇게는 `msgctxt` 항목입니다.</span><span class="sxs-lookup"><span data-stu-id="907cb-186">This is accomplished by setting the value on the `msgctxt` entry.</span></span>

<span data-ttu-id="907cb-187">이전에 사소한 추가 고려 *fr.po* 예제입니다.</span><span class="sxs-lookup"><span data-stu-id="907cb-187">Consider a minor addition to the previous *fr.po* example.</span></span> <span data-ttu-id="907cb-188">Razor 보기에 있는 *Views/Home/About.cshtml* 예약을 설정 하 여 파일 컨텍스트도 정의할 수 있습니다 `msgctxt` 항목의 값:</span><span class="sxs-lookup"><span data-stu-id="907cb-188">A Razor view located at *Views/Home/About.cshtml* can be defined as the file context by setting the reserved `msgctxt` entry's value:</span></span>

```text
msgctxt "Views.Home.About"
msgid "Hello world!"
msgstr "Bonjour le monde!"
```

<span data-ttu-id="907cb-189">와 `msgctxt` 탐색 하는 경우 텍스트 변환이 발생 따라서 설정, `/Home/About?culture=fr-FR`합니다.</span><span class="sxs-lookup"><span data-stu-id="907cb-189">With the `msgctxt` set as such, text translation occurs when navigating to `/Home/About?culture=fr-FR`.</span></span> <span data-ttu-id="907cb-190">탐색 하는 경우 변환이 발생 하지 않습니다 `/Home/Contact?culture=fr-FR`합니다.</span><span class="sxs-lookup"><span data-stu-id="907cb-190">The translation won't occur when navigating to `/Home/Contact?culture=fr-FR`.</span></span>

<span data-ttu-id="907cb-191">특정 항목이 지정 된 파일 컨텍스트와 일치 하면 Orchard 코어 대체 메커니즘이 컨텍스트 없이 적절 한 PO 파일을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="907cb-191">When no specific entry is matched with a given file context, Orchard Core's fallback mechanism looks for an appropriate PO file without a context.</span></span> <span data-ttu-id="907cb-192">가정 하는 경우 컨텍스트가 특정 파일에 대해 정의 된 *Views/Home/Contact.cshtml*탐색 하려면, `/Home/Contact?culture=fr-FR` 같은 PO 파일을 로드 합니다.</span><span class="sxs-lookup"><span data-stu-id="907cb-192">Assuming there is no specific file context defined for *Views/Home/Contact.cshtml*, navigating to `/Home/Contact?culture=fr-FR` loads a PO file such as:</span></span>

[!code-text[Main](localization/sample/POLocalization/fr.po)]

### <a name="changing-the-location-of-po-files"></a><span data-ttu-id="907cb-193">PO 파일의 위치를 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="907cb-193">Changing the location of PO files</span></span>

<span data-ttu-id="907cb-194">PO 파일의 기본 위치에서 변경할 수 있습니다 `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="907cb-194">The default location of PO files can be changed in `ConfigureServices`:</span></span>

```csharp
services.AddPortableObjectLocalization(options => options.ResourcesPath = "Localization");
```

<span data-ttu-id="907cb-195">이 예제에서는 PO 파일에서 로드 되는 *지역화* 폴더입니다.</span><span class="sxs-lookup"><span data-stu-id="907cb-195">In this example, the PO files are loaded from the *Localization* folder.</span></span>

### <a name="implementing-a-custom-logic-for-finding-localization-files"></a><span data-ttu-id="907cb-196">지역화 파일을 찾기 위한 사용자 지정 논리를 구현 합니다.</span><span class="sxs-lookup"><span data-stu-id="907cb-196">Implementing a custom logic for finding localization files</span></span>

<span data-ttu-id="907cb-197">PO 파일을 배치할 필요는 더 복잡 한 논리는 `OrchardCore.Localization.PortableObject.ILocalizationFileLocationProvider` 인터페이스를 구현 하 고 서비스로 등록 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="907cb-197">When more complex logic is needed to locate PO files, the `OrchardCore.Localization.PortableObject.ILocalizationFileLocationProvider` interface can be implemented and registered as a service.</span></span> <span data-ttu-id="907cb-198">다양 한 위치에 PO 파일을 저장할 수 있습니다 또는 파일 폴더의 계층 구조 내에서 찾을 수 하는 경우에 유용 합니다.</span><span class="sxs-lookup"><span data-stu-id="907cb-198">This is useful when PO files can be stored in varying locations or when the files have to be found within a hierarchy of folders.</span></span>

### <a name="using-a-different-default-pluralized-language"></a><span data-ttu-id="907cb-199">복수화 다른 기본 언어를 사용 하 여</span><span class="sxs-lookup"><span data-stu-id="907cb-199">Using a different default pluralized language</span></span>

<span data-ttu-id="907cb-200">패키지에 포함 된 `Plural` 두 복수 형태에 관련 된 확장 메서드.</span><span class="sxs-lookup"><span data-stu-id="907cb-200">The package includes a `Plural` extension method that is specific to two plural forms.</span></span> <span data-ttu-id="907cb-201">더 많은 복수 형태를 요구 하는 언어에 대 한 확장 메서드를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="907cb-201">For languages requiring more plural forms, create an extension method.</span></span> <span data-ttu-id="907cb-202">확장 방식으로 기본 언어에 대 한 모든 지역화 파일을 제공 하도록 필요 하지는 않습니다 &mdash; 원본 문자열은 이미 코드에서 직접 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="907cb-202">With an extension method, you won't need to provide any localization file for the default language &mdash; the original strings are already available directly in the code.</span></span>

<span data-ttu-id="907cb-203">보다 일반적인을 사용할 수 있습니다 `Plural(int count, string[] pluralForms, params object[] arguments)` 번역의 문자열 배열을 허용 하는 오버 로드 합니다.</span><span class="sxs-lookup"><span data-stu-id="907cb-203">You can use the more generic `Plural(int count, string[] pluralForms, params object[] arguments)` overload which accepts a string array of translations.</span></span>