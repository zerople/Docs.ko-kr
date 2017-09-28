---
title: "유효성 검사 추가"
author: rick-anderson
description: "Razor 페이지에 유효성 검사를 추가하는 방법"
keywords: "ASP.NET Core, 유효성 검사, DataAnnotations, Razor, Razor 페이지"
ms.author: riande
manager: wpickett
ms.date: 08/07/2017
ms.topic: get-started-article
ms.technology: aspnet
ms.prod: aspnet-core
uid: tutorials/razor-pages/validation
ms.openlocfilehash: f8d86b48363675adaf27e2bed39f835f117e624a
ms.sourcegitcommit: 67f54fabbfa4e3942f5bfe1f8a7fdfe4a7a75358
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/19/2017
---
# <a name="adding-validation-to-a-razor-page"></a><span data-ttu-id="e87d3-104">Razor 페이지에 유효성 검사 추가</span><span class="sxs-lookup"><span data-stu-id="e87d3-104">Adding validation to a Razor Page</span></span>

<span data-ttu-id="e87d3-105">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="e87d3-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="e87d3-106">이 섹션에서 유효성 검사 논리는 `Movie` 모델에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="e87d3-106">In this section validation logic is added to the `Movie` model.</span></span> <span data-ttu-id="e87d3-107">유효성 검사 규칙은 사용자가 동영상을 만들거나 편집할 때 언제든지 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="e87d3-107">The validation rules are enforced any time a user creates or edits a movie.</span></span>

## <a name="validation"></a><span data-ttu-id="e87d3-108">유효성 검사</span><span class="sxs-lookup"><span data-stu-id="e87d3-108">Validation</span></span>

<span data-ttu-id="e87d3-109">소프트웨어 개발의 주요 개념은 [반복 금지](https://wikipedia.org/wiki/Don%27t_repeat_yourself)라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="e87d3-109">A key tenet of software development is called [DRY](https://wikipedia.org/wiki/Don%27t_repeat_yourself) ("**D**on't **R**epeat **Y**ourself").</span></span> <span data-ttu-id="e87d3-110">Razor 페이지는 기능이 한번 정의된 곳의 개발을 원활하게 하고 앱 전체에서 반영됩니다.</span><span class="sxs-lookup"><span data-stu-id="e87d3-110">Razor Pages encourages development where functionality is specified once, and it's reflected throughout the app.</span></span> <span data-ttu-id="e87d3-111">반복 금지를 통해 앱에서 코드의 양을 줄일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e87d3-111">DRY can help reduce the amount of code in an app.</span></span> <span data-ttu-id="e87d3-112">반복 금지를 사용하면 코드에 오류가 적게 되고 테스트 및 유지 관리하기 쉽습니다.</span><span class="sxs-lookup"><span data-stu-id="e87d3-112">DRY makes the code less error prone, and easier to test and maintain.</span></span>

<span data-ttu-id="e87d3-113">Razor 페이지에서 제공하는 유효성 검사 지원 및 Entity Framework는 반복 금지 원칙의 좋은 예제입니다.</span><span class="sxs-lookup"><span data-stu-id="e87d3-113">The validation support provided by Razor Pages and Entity Framework is a good example of the DRY principle.</span></span> <span data-ttu-id="e87d3-114">유효성 검사 규칙은 한 위치(모델 클래스에서)에서 선언적으로 지정되고 규칙은 앱의 모든 위치에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="e87d3-114">Validation rules are declaratively specified in one place (in the model class), and the rules are enforced everywhere in the app.</span></span>

### <a name="adding-validation-rules-to-the-movie-model"></a><span data-ttu-id="e87d3-115">동영상 모델에 유효성 검사 규칙 추가</span><span class="sxs-lookup"><span data-stu-id="e87d3-115">Adding validation rules to the movie model</span></span>

<span data-ttu-id="e87d3-116">*Movie.cs* 파일을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="e87d3-116">Open the *Movie.cs* file.</span></span> <span data-ttu-id="e87d3-117">[DataAnnotations](https://docs.microsoft.com/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6)는 클래스 또는 속성에 선언적으로 적용되는 유효성 검사 특성의 기본 제공 집합을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="e87d3-117">[DataAnnotations](https://docs.microsoft.com/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) provides a built-in set of validation attributes that are applied declaratively to a class or property.</span></span> <span data-ttu-id="e87d3-118">DataAnnotations는 또한 서식 지정을 돕는 `DataType`과 같은 서식 지정 특성을 포함하며 유효성 검사를 제공하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e87d3-118">DataAnnotations also contains formatting attributes like `DataType` that help with formatting and don't provide validation.</span></span>

<span data-ttu-id="e87d3-119">`Required`, `StringLength`, `RegularExpression` 및 `Range` 유효성 검사 특성을 활용하도록 `Movie` 클래스를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="e87d3-119">Update the `Movie` class to take advantage of the `Required`, `StringLength`, `RegularExpression`, and `Range` validation attributes.</span></span>

[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc//sample/MvcMovie/Models/MovieDateRatingDA.cs?name=snippet1)]

<span data-ttu-id="e87d3-120">유효성 검사 특성은 모델 속성에 적용되는 동작을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="e87d3-120">Validation attributes specify behavior that is enforced on model properties.</span></span> <span data-ttu-id="e87d3-121">`Required` 및 `MinimumLength` 특성은 속성에 값이 있어야 하지만 사용자가 유효성 검사 제약 조건을 만족하기 위해 공백을 입력하는 것을 예방할 수 없다는 것을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="e87d3-121">The `Required` and `MinimumLength` attributes indicates that a property must have a value; but nothing prevents a user from entering white space to satisfy the validation constraint.</span></span> <span data-ttu-id="e87d3-122">`RegularExpression` 특성은 입력될 수 있는 문자를 제한하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="e87d3-122">The `RegularExpression` attribute is used to limit what characters can be input.</span></span> <span data-ttu-id="e87d3-123">위의 코드에서 `Genre` 및 `Rating`은 문자만을 사용해야 합니다(공백, 숫자 및 특수 문자가 허용되지 않음).</span><span class="sxs-lookup"><span data-stu-id="e87d3-123">In the preceding code, `Genre` and `Rating` must use only letters (white space, numbers and special characters are not allowed).</span></span> <span data-ttu-id="e87d3-124">`Range` 특성은 지정된 범위 내의 값을 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="e87d3-124">The `Range` attribute constrains a value to within a specified range.</span></span> <span data-ttu-id="e87d3-125">`StringLength` 특성은 문자열의 최대 길이 및 필요에 따라 최소 길이를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e87d3-125">The `StringLength` attribute sets the maximum length of a string, and optionally the minimum length.</span></span> <span data-ttu-id="e87d3-126">[값 형식](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/value-types)(예: `decimal`, `int`, `float`, `DateTime`)은 기본적으로 필요하며 `[Required]` 특성은 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e87d3-126">[Value types](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/value-types) (such as `decimal`, `int`, `float`, `DateTime`) are inherently required and don't need the `[Required]` attribute.</span></span>

<span data-ttu-id="e87d3-127">ASP.NET Core에 의해 자동으로 적용되는 유효성 검사 규칙을 사용하면 앱을 보다 강력하게 합니다.</span><span class="sxs-lookup"><span data-stu-id="e87d3-127">Having validation rules automatically enforced by ASP.NET Core helps make an app more robust.</span></span> <span data-ttu-id="e87d3-128">모델에서 자동 유효성 검사는 새 코드가 추가될 때 적용하기 위해 기억할 필요가 없기 때문에 앱을 보호합니다.</span><span class="sxs-lookup"><span data-stu-id="e87d3-128">Automatic validation on models helps protect the app because you don't have to remember to apply them when new code is added.</span></span>

### <a name="validation-error-ui-in-razor-pages"></a><span data-ttu-id="e87d3-129">Razor 페이지에서 유효성 검사 오류 UI</span><span class="sxs-lookup"><span data-stu-id="e87d3-129">Validation Error UI in Razor Pages</span></span>

<span data-ttu-id="e87d3-130">앱을 실행하고 Pages/Movies로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="e87d3-130">Run the app and navigate to Pages/Movies.</span></span>

<span data-ttu-id="e87d3-131">**새로 만들기** 링크를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e87d3-131">Select the **Create New** link.</span></span> <span data-ttu-id="e87d3-132">일부 잘못된 값으로 양식을 완성합니다.</span><span class="sxs-lookup"><span data-stu-id="e87d3-132">Complete the form with some invalid values.</span></span> <span data-ttu-id="e87d3-133">jQuery 클라이언트 쪽 유효성 검사에서 오류를 발견하면 오류 메시지를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="e87d3-133">When jQuery client-side validation detects the error, it displays an error message.</span></span>

![여러 jQuery 클라이언트 쪽 유효성 검사 오류가 있는 동영상 보기 양식](validation/_static/val.png)

> [!NOTE]
> <span data-ttu-id="e87d3-135">`Price` 필드에는 소수점 또는 쉼표를 입력할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="e87d3-135">You may not be able to enter decimal points or commas in the `Price` field.</span></span> <span data-ttu-id="e87d3-136">소수점으로 쉼표(“,”)를 사용하는 영어가 아닌 로캘 및 미국 영어가 아닌 날짜 형식에 대해 [jQuery 유효성 검사](https://jqueryvalidation.org/)를 지원하려면 앱을 전역화하는 단계를 수행해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e87d3-136">To support [jQuery validation](https://jqueryvalidation.org/) in non-English locales that use a comma (",") for a decimal point, and non US-English date formats, you must take steps to globalize your app.</span></span> <span data-ttu-id="e87d3-137">자세한 내용은 [추가 리소스](#additional-resources)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e87d3-137">See [Additional resources](#additional-resources) for more information.</span></span> <span data-ttu-id="e87d3-138">이제 10 같은 정수를 입력하면 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e87d3-138">For now, just enter whole numbers like 10.</span></span>

<span data-ttu-id="e87d3-139">양식에서 잘못된 값을 포함하는 각 필드에 유효성 검사 오류 메시지를 자동으로 렌더링하는 방법을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="e87d3-139">Notice how the form has automatically rendered a validation error message in each field containing an invalid value.</span></span> <span data-ttu-id="e87d3-140">오류는 클라이언트 쪽(JavaScript 및 jQuery 사용) 및 서버 쪽(사용자가 JavaScript를 사용하지 않도록 설정한 경우) 모두 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="e87d3-140">The errors are enforced both client-side (using JavaScript and jQuery) and server-side (when a user has JavaScript disabled).</span></span>

<span data-ttu-id="e87d3-141">중요한 이점은 만들기 또는 편집 페이지에서 코드 변경이 필요하지 **않았다는** 점입니다.</span><span class="sxs-lookup"><span data-stu-id="e87d3-141">A significant benefit is that **no** code changes were necessary in the Create  or Edit pages.</span></span> <span data-ttu-id="e87d3-142">DataAnnotations가 모델에 적용된 후 유효성 검사 UI가 활성화되었습니다.</span><span class="sxs-lookup"><span data-stu-id="e87d3-142">Once DataAnnotations were applied to the model, the validation UI was enabled.</span></span> <span data-ttu-id="e87d3-143">이 자습서에서 만든 Razor 페이지는 자동으로 유효성 검사 규칙을 선택했습니다(`Movie` 모델 클래스의 속성에서 유효성 검사 특성 사용).</span><span class="sxs-lookup"><span data-stu-id="e87d3-143">The Razor Pages created in this tutorial automatically picked up the validation rules (using validation attributes on the properties of the `Movie` model class).</span></span> <span data-ttu-id="e87d3-144">편집 페이지를 사용하는 테스트 유효성 검사는 동일한 유효성 검사가 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="e87d3-144">Test validation using the Edit page, the same validation is applied.</span></span>

<span data-ttu-id="e87d3-145">양식 데이터는 클라이언트 쪽 유효성 검사 오류가 없을 때까지 서버에 게시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e87d3-145">The form data is not posted to the server until there are no client-side validation errors.</span></span> <span data-ttu-id="e87d3-146">양식 데이터가 다음 방법 중 하나 이상으로 게시되지 않았는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="e87d3-146">Verify form data is not posted by one or more of the following approaches:</span></span>

* <span data-ttu-id="e87d3-147">`OnPostAsync` 메서드에 중단점을 배치합니다.</span><span class="sxs-lookup"><span data-stu-id="e87d3-147">Put a break point in the `OnPostAsync` method.</span></span> <span data-ttu-id="e87d3-148">양식을 제출합니다(**만들기** 또는 **저장** 선택).</span><span class="sxs-lookup"><span data-stu-id="e87d3-148">Submit the form (select **Create** or **Save**).</span></span> <span data-ttu-id="e87d3-149">중단점은 적중되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e87d3-149">The break point is never hit.</span></span>
* <span data-ttu-id="e87d3-150">[Fiddler 도구](http://www.telerik.com/fiddler)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="e87d3-150">Use the [Fiddler tool](http://www.telerik.com/fiddler).</span></span>
* <span data-ttu-id="e87d3-151">브라우저 개발자 도구를 사용하여 네트워크 트래픽을 모니터링합니다.</span><span class="sxs-lookup"><span data-stu-id="e87d3-151">Use the browser developer tools to monitor network traffic.</span></span>

### <a name="server-side-validation"></a><span data-ttu-id="e87d3-152">서버 쪽 유효성 검사</span><span class="sxs-lookup"><span data-stu-id="e87d3-152">Server-side validation</span></span>

<span data-ttu-id="e87d3-153">브라우저에서 JavaScript가 비활성화된 경우 오류가 있는 양식 제출에서 서버에 게시합니다.</span><span class="sxs-lookup"><span data-stu-id="e87d3-153">When JavaScript is disabled in the browser, submitting the form with errors will post to the server.</span></span>

<span data-ttu-id="e87d3-154">선택 가능한 서버 쪽 유효성 검사 테스트:</span><span class="sxs-lookup"><span data-stu-id="e87d3-154">Optional, test server-side validation:</span></span>

* <span data-ttu-id="e87d3-155">브라우저에서 JavaScript를 비활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="e87d3-155">Disable JavaScript in the browser.</span></span> <span data-ttu-id="e87d3-156">브라우저에서 JavaScript를 비활성화할 수 없는 경우 다른 브라우저를 시도하세요.</span><span class="sxs-lookup"><span data-stu-id="e87d3-156">If you can't disable JavaScript in the browser, try another browser.</span></span>
* <span data-ttu-id="e87d3-157">만들기 또는 편집 페이지의 `OnPostAsync` 메서드에서 중단점을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e87d3-157">Set a break point in the `OnPostAsync` method of the Create or Edit page.</span></span>
* <span data-ttu-id="e87d3-158">유효성 검사 오류가 있는 양식을 제출합니다.</span><span class="sxs-lookup"><span data-stu-id="e87d3-158">Submit a form with validation errors.</span></span>
* <span data-ttu-id="e87d3-159">모델 상태가 유효하지 않은 것을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="e87d3-159">Verify the model state is invalid:</span></span>

  ```csharp
   if (!ModelState.IsValid)
   {
      return Page();
   }
  ```

<span data-ttu-id="e87d3-160">다음 코드는 자습서의 앞부분에서 스캐폴드한 *Create.cshtml* 페이지의 일부를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="e87d3-160">The following code shows a portion of the *Create.cshtml* page that you scaffolded earlier in the tutorial.</span></span> <span data-ttu-id="e87d3-161">만들기 및 편집 페이지에서 초기 양식을 표시하고 오류가 발생한 경우 양식을 다시 표시하기 위해 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="e87d3-161">It's used by the Create and Edit pages to display the initial form and to redisplay the form in the event of an error.</span></span>

[!code-cshtml[Main](razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Create.cshtml?range=14-20)]

<span data-ttu-id="e87d3-162">[입력 태그 도우미](xref:mvc/views/working-with-forms)는 [DataAnnotations](https://docs.microsoft.com/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) 특성을 사용하고 클라이언트 쪽의 jQuery 유효성 검사에 필요한 HTML 특성을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="e87d3-162">The [Input Tag Helper](xref:mvc/views/working-with-forms) uses the [DataAnnotations](https://docs.microsoft.com/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span> <span data-ttu-id="e87d3-163">[유효성 검사 태그 도우미](xref:mvc/views/working-with-forms#the-validation-tag-helpers)는 유효성 검사 오류를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="e87d3-163">The [Validation Tag Helper](xref:mvc/views/working-with-forms#the-validation-tag-helpers) displays validation errors.</span></span> <span data-ttu-id="e87d3-164">자세한 내용은 [유효성 검사](xref:mvc/models/validation)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e87d3-164">See [Validation](xref:mvc/models/validation) for more information.</span></span>

<span data-ttu-id="e87d3-165">만들기 및 편집 페이지에 유효성 검사 규칙이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="e87d3-165">The Create and Edit pages have no validation rules in them.</span></span> <span data-ttu-id="e87d3-166">유효성 검사 규칙 및 오류 문자열은 `Movie` 클래스에서만 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="e87d3-166">The validation rules and the error strings are specified only in the `Movie` class.</span></span> <span data-ttu-id="e87d3-167">이러한 유효성 검사 규칙은 `Movie` 모델을 편집하는 Razor 페이지에 자동으로 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="e87d3-167">These validation rules are automatically applied to Razor Pages that edit the `Movie` model.</span></span>

<span data-ttu-id="e87d3-168">유효성 검사 논리를 변경해야 하는 경우 모델에서만 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="e87d3-168">When validation logic needs to change, it's done only in the model.</span></span> <span data-ttu-id="e87d3-169">유효성 검사는 응용 프로그램에 걸쳐 일관되게 적용됩니다(유효성 검사 논리는 한 위치에서 정의됨).</span><span class="sxs-lookup"><span data-stu-id="e87d3-169">Validation is applied consistently throughout the application (validation logic is defined in one place).</span></span> <span data-ttu-id="e87d3-170">한 위치의 유효성 검사를 통해 코드를 깨끗이 유지하고 쉽게 유지 관리하고 업데이트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e87d3-170">Validation in one place helps keep the code clean, and makes it easier to maintain and update.</span></span>

## <a name="using-datatype-attributes"></a><span data-ttu-id="e87d3-171">데이터 형식 특성 사용</span><span class="sxs-lookup"><span data-stu-id="e87d3-171">Using DataType Attributes</span></span>

<span data-ttu-id="e87d3-172">`Movie` 클래스를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="e87d3-172">Examine the `Movie` class.</span></span> <span data-ttu-id="e87d3-173">`System.ComponentModel.DataAnnotations` 네임스페이스는 기본 제공 유효성 검사 특성의 집합 외에 서식 지정 특성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="e87d3-173">The `System.ComponentModel.DataAnnotations` namespace provides formatting attributes in addition to the built-in set of validation attributes.</span></span> <span data-ttu-id="e87d3-174">`DataType` 특성은 `ReleaseDate` 및 `Price` 속성에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="e87d3-174">The `DataType` attribute is applied to the `ReleaseDate` and `Price` properties.</span></span>

[!code-csharp[Main](razor-pages-start/sample/RazorPagesMovie/Models/MovieDateRatingDA.cs?highlight=2,6&name=snippet2)]

<span data-ttu-id="e87d3-175">`DataType` 특성은 데이터의 서식을 지정하도록 뷰 엔진에 대한 힌트만을 제공합니다(그리고 URL에 대한 `<a>` 및 전자 메일에 대한 `<a href="mailto:EmailAddress.com">`과 같은 특성 제공).</span><span class="sxs-lookup"><span data-stu-id="e87d3-175">The `DataType` attributes only provide hints for the view engine to format the data (and supplies attributes such as `<a>` for URL's and `<a href="mailto:EmailAddress.com">` for email).</span></span> <span data-ttu-id="e87d3-176">`RegularExpression` 특성을 사용하여 데이터 형식의 유효성을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="e87d3-176">Use the `RegularExpression` attribute to validate the format of the data.</span></span> <span data-ttu-id="e87d3-177">`DataType` 특성은 데이터베이스 내장 형식보다 구체적인 데이터 형식을 지정하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="e87d3-177">The `DataType` attribute is used to specify a data type that is more specific than the database intrinsic type.</span></span> <span data-ttu-id="e87d3-178">`DataType` 특성은 유효성 검사 특성이 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="e87d3-178">`DataType` attributes are not validation attributes.</span></span> <span data-ttu-id="e87d3-179">샘플 응용 프로그램에서 날짜는 시간 없이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="e87d3-179">In the sample application, only the date is displayed, without time.</span></span>

<span data-ttu-id="e87d3-180">`DataType` 열거형은 날짜, 시간, 전화 번호, 통화, 전자 메일 주소 등과 같은 많은 데이터 형식을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="e87d3-180">The `DataType` Enumeration provides for many data types, such as Date, Time, PhoneNumber, Currency, EmailAddress, and more.</span></span> <span data-ttu-id="e87d3-181">`DataType` 특성을 통해 응용 프로그램에서 자동으로 유형별 기능을 제공하도록 설정할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e87d3-181">The `DataType` attribute can also enable the application to automatically provide type-specific features.</span></span> <span data-ttu-id="e87d3-182">예를 들어 `DataType.EmailAddress`에 대해 `mailto:` 링크를 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e87d3-182">For example, a `mailto:` link can be created for `DataType.EmailAddress`.</span></span> <span data-ttu-id="e87d3-183">HTML5를 지원하는 브라우저에서 `DataType.Date`에 대해 날짜 선택기가 제공될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e87d3-183">A date selector can be provided for `DataType.Date` in browsers that support HTML5.</span></span> <span data-ttu-id="e87d3-184">`DataType` 특성은 HTML 5 브라우저에서 사용하는 HTML 5 `data-`(데이터 대시로 발음) 특성을 내보냅니다.</span><span class="sxs-lookup"><span data-stu-id="e87d3-184">The `DataType` attributes emits HTML 5 `data-` (pronounced data dash) attributes that HTML 5 browsers consume.</span></span> <span data-ttu-id="e87d3-185">`DataType` 특성은 유효성 검사를 제공하지 **않습니다**.</span><span class="sxs-lookup"><span data-stu-id="e87d3-185">The `DataType` attributes do **not** provide any validation.</span></span>

<span data-ttu-id="e87d3-186">`DataType.Date`는 표시되는 날짜의 서식을 지정하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e87d3-186">`DataType.Date` does not specify the format of the date that is displayed.</span></span> <span data-ttu-id="e87d3-187">기본적으로 데이터 필드는 서버 `CultureInfo`의 기본 형식에 따라 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="e87d3-187">By default, the data field is displayed according to the default formats based on the server's `CultureInfo`.</span></span>

<span data-ttu-id="e87d3-188">`DisplayFormat` 특성은 날짜 형식을 명시적으로 지정하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="e87d3-188">The `DisplayFormat` attribute is used to explicitly specify the date format:</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
public DateTime ReleaseDate { get; set; }
```

<span data-ttu-id="e87d3-189">`ApplyFormatInEditMode` 설정은 값이 편집을 위해 표시될 때 서식 지정이 적용되어야 함을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="e87d3-189">The `ApplyFormatInEditMode` setting specifies that the formatting should be applied when the value is displayed for editing.</span></span> <span data-ttu-id="e87d3-190">일부 필드에 대해 해당 동작을 원하지 않을 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e87d3-190">You might not want that behavior for some fields.</span></span> <span data-ttu-id="e87d3-191">예를 들어 통화 값의 편집 UI에서 통화 기호를 원하지 않을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e87d3-191">For example, in currency values, you probably do not want the currency symbol in the edit UI.</span></span>

<span data-ttu-id="e87d3-192">`DisplayFormat` 특성은 단독으로 사용될 수 있지만 일반적으로 `DataType` 특성을 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="e87d3-192">The `DisplayFormat` attribute can be used by itself, but it's generally a good idea to use the `DataType` attribute.</span></span> <span data-ttu-id="e87d3-193">`DataType` 특성은 데이터를 화면에 렌더링하는 방법과 반대로 데이터의 의미 체계를 전달하고 DisplayFormat으로 가져올 수 없는 다음과 같은 이점을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="e87d3-193">The `DataType` attribute conveys the semantics of the data as opposed to how to render it on a screen, and provides the following benefits that you don't get with DisplayFormat:</span></span>

* <span data-ttu-id="e87d3-194">브라우저는 HTML5 기능을 활성화할 수 있습니다(예: 달력 컨트롤, 로캘에 적합한 통화 기호, 전자 메일 링크 등을 표시하기 위해).</span><span class="sxs-lookup"><span data-stu-id="e87d3-194">The browser can enable HTML5 features (for example to show a calendar control, the locale-appropriate currency symbol, email links, etc.)</span></span>
* <span data-ttu-id="e87d3-195">기본적으로 브라우저는 사용자의 로캘에 따른 올바른 형식을 사용하여 데이터를 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="e87d3-195">By default, the browser will render data using the correct format based on your locale.</span></span>
* <span data-ttu-id="e87d3-196">`DataType` 특성은 ASP.NET Core 프레임워크를 활성화하여 데이터를 렌더링할 올바른 필드 템플릿을 선택할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e87d3-196">The `DataType` attribute can enable the ASP.NET Core framework to choose the right field template to render the data.</span></span> <span data-ttu-id="e87d3-197">자체적으로 사용되는 경우 `DisplayFormat`은 문자열 템플릿을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="e87d3-197">The `DisplayFormat` if used by itself uses the string template.</span></span>

<span data-ttu-id="e87d3-198">참고: jQuery 유효성 검사는 `Range` 특성 및 `DateTime`을 사용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e87d3-198">Note: jQuery validation does not work with the `Range` attribute and `DateTime`.</span></span> <span data-ttu-id="e87d3-199">예를 들어 다음 코드는 날짜가 지정된 범위에 있을 경우에도 클라이언트 쪽 유효성 검사 오류를 항상 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="e87d3-199">For example, the following code will always display a client-side validation error, even when the date is in the specified range:</span></span>

```csharp
[Range(typeof(DateTime), "1/1/1966", "1/1/2020")]
   ```

<span data-ttu-id="e87d3-200">일반적으로 모델에서 하드 날짜를 컴파일하는 것은 좋지 않으므로 `Range` 특성 및 `DateTime`을 사용하지 않는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="e87d3-200">It's generally not a good practice to compile hard dates in your models, so using the `Range` attribute and `DateTime` is discouraged.</span></span>

<span data-ttu-id="e87d3-201">다음 코드는 한 줄에 결합 특성을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="e87d3-201">The following code shows combining attributes on one line:</span></span>

[!code-csharp[Main](razor-pages-start/sample/RazorPagesMovie/Models/MovieDateRatingDAmult.cs?name=snippet1)]

<span data-ttu-id="e87d3-202">Razor 페이지에 대한 이 소개를 완료해 주셔서 감사합니다.</span><span class="sxs-lookup"><span data-stu-id="e87d3-202">Thanks for completing this introduction to Razor Pages.</span></span> <span data-ttu-id="e87d3-203">의견을 남겨 주세요.</span><span class="sxs-lookup"><span data-stu-id="e87d3-203">We appreciate any comments you leave.</span></span> <span data-ttu-id="e87d3-204">[MVC 및 EF Core 시작](xref:data/ef-mvc/intro)은 이 자습서에 대한 뛰어난 후속편입니다.</span><span class="sxs-lookup"><span data-stu-id="e87d3-204">[Getting started with MVC and EF Core](xref:data/ef-mvc/intro) is an excellent follow up to this tutorial.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e87d3-205">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="e87d3-205">Additional resources</span></span>

* [<span data-ttu-id="e87d3-206">양식 사용</span><span class="sxs-lookup"><span data-stu-id="e87d3-206">Working with Forms</span></span>](xref:mvc/views/working-with-forms)
* [<span data-ttu-id="e87d3-207">전역화 및 지역화</span><span class="sxs-lookup"><span data-stu-id="e87d3-207">Globalization and localization</span></span>](xref:fundamentals/localization)
* [<span data-ttu-id="e87d3-208">태그 도우미 소개</span><span class="sxs-lookup"><span data-stu-id="e87d3-208">Introduction to Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="e87d3-209">태그 도우미 작성</span><span class="sxs-lookup"><span data-stu-id="e87d3-209">Authoring Tag Helpers</span></span>](xref:mvc/views/tag-helpers/authoring)

>[!div class="step-by-step"]
[<span data-ttu-id="e87d3-210">이전: 새 필드 추가</span><span class="sxs-lookup"><span data-stu-id="e87d3-210">Previous: Adding a new field</span></span>](xref:tutorials/razor-pages/new-field)
