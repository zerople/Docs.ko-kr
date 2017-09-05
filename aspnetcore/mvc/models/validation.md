---
title: "ASP.NET Core MVC에서 모델 유효성 검사"
author: rick-anderson
description: "ASP.NET Core MVC에서 모델 유효성 검사를 소개합니다."
keywords: "ASP.NET Core, MVC에서 유효성 검사"
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 3a8676dd-7ed8-4a05-bca2-44e288ab99ee
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/models/validation
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 514c7770b7f508893a059c1adcf56204160aceda
ms.sourcegitcommit: 275a5381b6172b4f0b5fcd1d252aff03d3dae166
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2017
---
# <a name="introduction-to-model-validation-in-aspnet-core-mvc"></a><span data-ttu-id="25740-104">ASP.NET Core MVC에서 모델 유효성 검사 소개</span><span class="sxs-lookup"><span data-stu-id="25740-104">Introduction to model validation in ASP.NET Core MVC</span></span>

<span data-ttu-id="25740-105">으로 [Rachel Appel](http://github.com/rachelappel)</span><span class="sxs-lookup"><span data-stu-id="25740-105">By [Rachel Appel](http://github.com/rachelappel)</span></span>

## <a name="introduction-to-model-validation"></a><span data-ttu-id="25740-106">모델 유효성 검사 소개</span><span class="sxs-lookup"><span data-stu-id="25740-106">Introduction to model validation</span></span>

<span data-ttu-id="25740-107">응용 프로그램 데이터베이스에 데이터를 저장 하기 전에 응용 프로그램 데이터를 확인 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="25740-107">Before an app stores data in a database, the app must validate the data.</span></span> <span data-ttu-id="25740-108">데이터 유형 및 크기로 적절 하 게 서식이 지정을 여 규칙을 준수 해야 하는 잠재적 보안 위협을 확인 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="25740-108">Data must be checked for potential security threats, verified that it is appropriately formatted by type and size, and it must conform to your rules.</span></span> <span data-ttu-id="25740-109">중복 되 고 구현 하기 번거로운 울 수 있지만 유효성 검사는 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="25740-109">Validation is necessary although it can be redundant and tedious to implement.</span></span> <span data-ttu-id="25740-110">Mvc에서 유효성 검사는 클라이언트와 서버 모두에서 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="25740-110">In MVC, validation happens on both the client and server.</span></span>

<span data-ttu-id="25740-111">다행히.NET에 유효성 검사 특성에 대 한 유효성 검사를 추상화 합니다.</span><span class="sxs-lookup"><span data-stu-id="25740-111">Fortunately, .NET has abstracted validation into validation attributes.</span></span> <span data-ttu-id="25740-112">이러한 특성에는 유효성 검사 코드를 작성 해야 하는 코드의 양을 줄이는 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="25740-112">These attributes contain validation code, thereby reducing the amount of code you must write.</span></span>

<span data-ttu-id="25740-113">[보기 또는 GitHub에서 샘플을 다운로드](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/models/validation/sample)합니다.</span><span class="sxs-lookup"><span data-stu-id="25740-113">[View or download sample from GitHub](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/models/validation/sample).</span></span>

## <a name="validation-attributes"></a><span data-ttu-id="25740-114">유효성 검사 특성</span><span class="sxs-lookup"><span data-stu-id="25740-114">Validation Attributes</span></span>

<span data-ttu-id="25740-115">유효성 검사 특성은 데이터베이스 테이블의 필드에 유효성 검사를 개념적으로 유사 하므로 모델 유효성 검사를 구성 하는 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="25740-115">Validation attributes are a way to configure model validation so it's similar conceptually to validation on fields in database tables.</span></span> <span data-ttu-id="25740-116">데이터 형식이 나 필수 필드를 할당 하는 등 제약 조건을 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="25740-116">This includes constraints such as assigning data types or required fields.</span></span> <span data-ttu-id="25740-117">다른 종류의 유효성 검사 전자 메일 주소 또는 전화 번호, 신용 카드와 같은 비즈니스 규칙을 적용 하는 데이터 패턴을 적용 하는 합니다.</span><span class="sxs-lookup"><span data-stu-id="25740-117">Other types of validation include applying patterns to data to enforce business rules, such as a credit card, phone number, or email address.</span></span> <span data-ttu-id="25740-118">유효성 검사 특성 확인 훨씬 간단 하 고 사용 하기 쉽게 이러한 요구 사항을 적용 합니다.</span><span class="sxs-lookup"><span data-stu-id="25740-118">Validation attributes make enforcing these requirements much simpler and easier to use.</span></span>

<span data-ttu-id="25740-119">다음은 이러한는 주석이 추가 된 `Movie` 동영상과 TV 프로그램에 대 한 정보를 저장 하는 응용 프로그램에서 모델입니다.</span><span class="sxs-lookup"><span data-stu-id="25740-119">Below is an annotated `Movie` model from an app that stores information about movies and TV shows.</span></span> <span data-ttu-id="25740-120">대부분의 속성 필요 하며 여러 문자열 속성에는 길이 요구 사항이입니다.</span><span class="sxs-lookup"><span data-stu-id="25740-120">Most of the properties are required and several string properties have length requirements.</span></span> <span data-ttu-id="25740-121">또한는에 대 한 숫자 범위 제한이 `Price` 속성을 0에서 $999.99, 사용자 지정 유효성 검사 특성을 합니다.</span><span class="sxs-lookup"><span data-stu-id="25740-121">Additionally, there is a numeric range restriction in place for the `Price` property from 0 to $999.99, along with a custom validation attribute.</span></span>

<span data-ttu-id="25740-122">[!code-csharp[Main](validation/sample/Movie.cs?range=6-31)]</span><span class="sxs-lookup"><span data-stu-id="25740-122">[!code-csharp[Main](validation/sample/Movie.cs?range=6-31)]</span></span>

<span data-ttu-id="25740-123">단순히 모델을 통해 읽을 쉽게 코드를 유지 하기 위해이 앱에 대 한 데이터에 대 한 규칙이 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="25740-123">Simply reading through the model reveals the rules about data for this app, making it easier to maintain the code.</span></span> <span data-ttu-id="25740-124">다음은 여러 인기 있는 기본 제공 유효성 검사 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="25740-124">Below are several popular built-in validation attributes:</span></span>

* <span data-ttu-id="25740-125">`[CreditCard]`: 유효성을 검사 속성에는 신용 카드 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="25740-125">`[CreditCard]`: Validates the property has a credit card format.</span></span>

* <span data-ttu-id="25740-126">`[Compare]`: 일치 하는 모델 항목에 두 속성의 유효성을 검사 합니다.</span><span class="sxs-lookup"><span data-stu-id="25740-126">`[Compare]`: Validates two properties in a model match.</span></span>

* <span data-ttu-id="25740-127">`[EmailAddress]`: 유효성을 검사 속성에는 전자 메일 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="25740-127">`[EmailAddress]`: Validates the property has an email format.</span></span>

* <span data-ttu-id="25740-128">`[Phone]`: 유효성을 검사 속성에는 전화 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="25740-128">`[Phone]`: Validates the property has a telephone format.</span></span>

* <span data-ttu-id="25740-129">`[Range]`: 지정된 된 범위 내에서 속성 값 변경의 유효성을 검사 합니다.</span><span class="sxs-lookup"><span data-stu-id="25740-129">`[Range]`: Validates the property value falls within the given range.</span></span>

* <span data-ttu-id="25740-130">`[RegularExpression]`: 데이터에 지정된 된 정규식과 일치 하는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="25740-130">`[RegularExpression]`: Validates that the data matches the specified regular expression.</span></span>

* <span data-ttu-id="25740-131">`[Required]`: 필수 속성으로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="25740-131">`[Required]`: Makes a property required.</span></span>

* <span data-ttu-id="25740-132">`[StringLength]`: 문자열 속성에 지정 된 최대 길이 최대 있는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="25740-132">`[StringLength]`: Validates that a string property has at most the given maximum length.</span></span>

* <span data-ttu-id="25740-133">`[Url]`: 유효성을 검사 속성이 URL 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="25740-133">`[Url]`: Validates the property has a URL format.</span></span>

<span data-ttu-id="25740-134">MVC에서 파생 된 모든 특성을 지원 `ValidationAttribute` 유효성 검사를 위해서입니다.</span><span class="sxs-lookup"><span data-stu-id="25740-134">MVC supports any attribute that derives from `ValidationAttribute` for validation purposes.</span></span> <span data-ttu-id="25740-135">많은 유용한 유효성 검사 특성에서 확인할 수 있습니다는 [System.ComponentModel.DataAnnotations](https://msdn.microsoft.com/library/system.componentmodel.dataannotations(v=vs.110).aspx) 네임 스페이스입니다.</span><span class="sxs-lookup"><span data-stu-id="25740-135">Many useful validation attributes can be found in the [System.ComponentModel.DataAnnotations](https://msdn.microsoft.com/library/system.componentmodel.dataannotations(v=vs.110).aspx) namespace.</span></span>

<span data-ttu-id="25740-136">제공 하는 기본 제공 특성 보다 더 많은 기능을 할 수 있는 인스턴스가 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="25740-136">There may be instances where you need more features than built-in attributes provide.</span></span> <span data-ttu-id="25740-137">해당 시간에 대 한 사용자 지정 유효성 검사 특성에서 파생 하 여 만들 수 있습니다 `ValidationAttribute` 구현 하 여 모델을 변경 하거나 `IValidatableObject`합니다.</span><span class="sxs-lookup"><span data-stu-id="25740-137">For those times, you can create custom validation attributes by deriving from `ValidationAttribute` or changing your model to implement `IValidatableObject`.</span></span>

## <a name="model-state"></a><span data-ttu-id="25740-138">모델 상태</span><span class="sxs-lookup"><span data-stu-id="25740-138">Model State</span></span>

<span data-ttu-id="25740-139">모델 상태 제출 된 HTML 폼 값의 유효성 검사 오류를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="25740-139">Model state represents validation errors in submitted HTML form values.</span></span>

<span data-ttu-id="25740-140">MVC는 계속 필드에 도달할 때까지 유효성 검사 오류 (기본적으로 200)의 최대 수입니다.</span><span class="sxs-lookup"><span data-stu-id="25740-140">MVC will continue validating fields until reaches the maximum number of errors (200 by default).</span></span> <span data-ttu-id="25740-141">다음 코드를 삽입 하 여이 번호를 구성할 수 있습니다는 `ConfigureServices` 에서 메서드는 *Startup.cs* 파일:</span><span class="sxs-lookup"><span data-stu-id="25740-141">You can configure this number by inserting the following code into the `ConfigureServices` method in the *Startup.cs* file:</span></span>

<span data-ttu-id="25740-142">[!code-csharp[Main](validation/sample/Startup.cs?range=27)]</span><span class="sxs-lookup"><span data-stu-id="25740-142">[!code-csharp[Main](validation/sample/Startup.cs?range=27)]</span></span>

## <a name="handling-model-state-errors"></a><span data-ttu-id="25740-143">처리 모델 상태 오류</span><span class="sxs-lookup"><span data-stu-id="25740-143">Handling Model State Errors</span></span>

<span data-ttu-id="25740-144">모델 유효성 검사가 호출 되는 각 컨트롤러 동작 하기 전에 발생 하 고 검사 해야 하는 작업 메서드의 `ModelState.IsValid` 하 고 적절 하 게 대처 합니다.</span><span class="sxs-lookup"><span data-stu-id="25740-144">Model validation occurs prior to each controller action being invoked, and it is the action method’s responsibility to inspect `ModelState.IsValid` and react appropriately.</span></span> <span data-ttu-id="25740-145">대부분의 경우에서 적합 한 반응 이상적 모델 유효성 검사에 실패 한 이유를 자세히 설명 하는 오류 응답의 몇 가지 종류를 반환 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="25740-145">In many cases, the appropriate reaction is to return some kind of error response, ideally detailing the reason why model validation failed.</span></span>

<span data-ttu-id="25740-146">모델 유효성 검사 오류,이 경우 필터 수 이어야 이러한 정책을 구현 하는 적절 한 위치를 처리 하기 위한 표준 규칙을 따르는 일부 응용 프로그램을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="25740-146">Some apps will choose to follow a standard convention for dealing with model validation errors, in which case a filter may be an appropriate place to implement such a policy.</span></span> <span data-ttu-id="25740-147">유효 하지 않은 모델 상태와 작업을 수행할 때 동작 하는 방법을 테스트 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="25740-147">You should test how your actions behave with valid and invalid model states.</span></span>

## <a name="manual-validation"></a><span data-ttu-id="25740-148">수동 유효성 검사</span><span class="sxs-lookup"><span data-stu-id="25740-148">Manual validation</span></span>

<span data-ttu-id="25740-149">모델 바인딩 및 유효성 검사 완료 되 면 일부를 반복 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="25740-149">After model binding and validation are complete, you may want to repeat parts of it.</span></span> <span data-ttu-id="25740-150">예를 들어, 사용자, 정수가 필요한 필드에 텍스트 입력 한 수 또는 모델의 속성에 대 한 값을 계산 해야 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="25740-150">For example, a user may have entered text in a field expecting an integer, or you may need to compute a value for a model's property.</span></span>

<span data-ttu-id="25740-151">유효성 검사를 수동으로 실행 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="25740-151">You may need to run validation manually.</span></span> <span data-ttu-id="25740-152">이렇게 하려면 호출 된 `TryValidateModel` 메서드를 다음과 같이 합니다.</span><span class="sxs-lookup"><span data-stu-id="25740-152">To do so, call the `TryValidateModel` method, as shown here:</span></span>

<span data-ttu-id="25740-153">[!code-csharp[Main](validation/sample/MoviesController.cs?range=52)]</span><span class="sxs-lookup"><span data-stu-id="25740-153">[!code-csharp[Main](validation/sample/MoviesController.cs?range=52)]</span></span>

## <a name="custom-validation"></a><span data-ttu-id="25740-154">사용자 지정 유효성 검사</span><span class="sxs-lookup"><span data-stu-id="25740-154">Custom validation</span></span>

<span data-ttu-id="25740-155">유효성 검사 특성에 대 한 대부분의 유효성 검사 요구 사항을 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="25740-155">Validation attributes work for most validation needs.</span></span> <span data-ttu-id="25740-156">그러나 일부 유효성 검사 규칙은 있지 않는 방금 필드를 확인 하는 등 일반 데이터 유효성 검사를 수행할지 또는 값의 범위를 따르는지으로 비즈니스에 관련이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="25740-156">However, some validation rules are specific to your business, as they're not just generic data validation such as ensuring a field is required or that it conforms to a range of values.</span></span> <span data-ttu-id="25740-157">이러한 시나리오에 대 한 사용자 지정 유효성 검사 특성은이 가장 좋은 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="25740-157">For these scenarios, custom validation attributes are a great solution.</span></span> <span data-ttu-id="25740-158">MVC에서 사용자 고유의 사용자 지정 유효성 검사 특성을 만들기 쉽습니다.</span><span class="sxs-lookup"><span data-stu-id="25740-158">Creating your own custom validation attributes in MVC is easy.</span></span> <span data-ttu-id="25740-159">상속 하는 것은 `ValidationAttribute`, 재정의 `IsValid` 메서드.</span><span class="sxs-lookup"><span data-stu-id="25740-159">Just inherit from the `ValidationAttribute`, and override the `IsValid` method.</span></span> <span data-ttu-id="25740-160">`IsValid` 메서드는 두 개의 매개 변수를 첫 번째는 라는 개체 *값* 고 두 번째는 `ValidationContext` 라는 개체 *validationContext*합니다.</span><span class="sxs-lookup"><span data-stu-id="25740-160">The `IsValid` method accepts two parameters, the first is an object named *value* and the second is a `ValidationContext` object named *validationContext*.</span></span> <span data-ttu-id="25740-161">*값* 지정 유효성 검사기의 유효성을 검사할 필드의 실제 값을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="25740-161">*Value* refers to the actual value from the field that your custom validator is validating.</span></span>

<span data-ttu-id="25740-162">다음 샘플에서는 비즈니스 규칙에 따르면 사용자가 장르를 설정 하지 않을 수 있습니다 *클래식* 1960 이후에 출시 동영상에 대 한 합니다.</span><span class="sxs-lookup"><span data-stu-id="25740-162">In the following sample, a business rule states that users may not set the genre to *Classic* for a movie released after 1960.</span></span> <span data-ttu-id="25740-163">`[ClassicMovie]` 특성 장르를 먼저 확인 하 고 기존 인 경우 다음 검사 릴리스 날짜 이후 1960 중인지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="25740-163">The `[ClassicMovie]` attribute checks the genre first, and if it is a classic, then it checks the release date to see that it is later than 1960.</span></span> <span data-ttu-id="25740-164">1960 후 해제 되 면 유효성 검사가 실패 합니다.</span><span class="sxs-lookup"><span data-stu-id="25740-164">If it is released after 1960, validation fails.</span></span> <span data-ttu-id="25740-165">특성은 데이터 유효성을 검사 하는 데 사용할 수 있는 연도 나타내는 정수 매개 변수를 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="25740-165">The attribute accepts an integer parameter representing the year that you can use to validate data.</span></span> <span data-ttu-id="25740-166">다음과 같이 특성의 생성자에서 매개 변수의 값을 캡처할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="25740-166">You can capture the value of the parameter in the attribute's constructor, as shown here:</span></span>

<span data-ttu-id="25740-167">[!code-csharp[Main](validation/sample/ClassicMovieAttribute.cs?range=9-28)]</span><span class="sxs-lookup"><span data-stu-id="25740-167">[!code-csharp[Main](validation/sample/ClassicMovieAttribute.cs?range=9-28)]</span></span>

<span data-ttu-id="25740-168">`movie` 나타냅니다 위에 변수는 `Movie` 유효성을 검사할 양식 전송에서 데이터를 포함 하는 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="25740-168">The `movie` variable above represents a `Movie` object that contains the data from the form submission to validate.</span></span> <span data-ttu-id="25740-169">이 경우 유효성 검사를 확인 하는 날짜에 장르는 `IsValid` 의 메서드는 `ClassicMovieAttribute` 규칙에 따라 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="25740-169">In this case, the validation code checks the date and genre in the `IsValid` method of the `ClassicMovieAttribute` class as per the rules.</span></span> <span data-ttu-id="25740-170">유효성을 검사 `IsValid` 반환는 `ValidationResult.Success` 코드 시점과 유효성 검사에 실패 한 `ValidationResult` 오류 메시지와 함께 합니다.</span><span class="sxs-lookup"><span data-stu-id="25740-170">Upon successful validation `IsValid` returns a `ValidationResult.Success` code, and when validation fails, a `ValidationResult` with an error message.</span></span> <span data-ttu-id="25740-171">사용자가 수정 하는 경우는 `Genre` 필드에서 폼을 전송 하 고는 `IsValid` 의 메서드는 `ClassicMovieAttribute` 영화 기존 인지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="25740-171">When a user modifies the `Genre` field and submits the form, the `IsValid` method of the `ClassicMovieAttribute` will verify whether the movie is a classic.</span></span> <span data-ttu-id="25740-172">기본 제공 특성과 마찬가지로 적용 된 `ClassicMovieAttribute` 와 같은 속성에 `ReleaseDate` 앞의 코드 예제에 표시 된 대로 발생 하는 유효성 검사를 진행 하려면.</span><span class="sxs-lookup"><span data-stu-id="25740-172">Like any built-in attribute, apply the `ClassicMovieAttribute` to a property such as `ReleaseDate` to ensure validation happens, as shown in the previous code sample.</span></span> <span data-ttu-id="25740-173">이 예제 에서만 작동 하므로 `Movie` 형식, 더 나은 옵션은 사용 하 `IValidatableObject` 다음 단락에서 표시 된 것 처럼 합니다.</span><span class="sxs-lookup"><span data-stu-id="25740-173">Since the example works only with `Movie` types, a better option is to use `IValidatableObject` as shown in the following paragraph.</span></span>

<span data-ttu-id="25740-174">구현 하 여 모델에이 동일한 코드에 배치 수는 또는 `Validate` 에서 메서드는 `IValidatableObject` 인터페이스입니다.</span><span class="sxs-lookup"><span data-stu-id="25740-174">Alternatively, this same code could be placed in the model by implementing the `Validate` method on the `IValidatableObject` interface.</span></span> <span data-ttu-id="25740-175">사용자 지정 유효성 검사 특성을 개별 속성 유효성 검사 잘 작동 하는 동안 구현 `IValidatableObject` 는 다음 그림과 같이 클래스 수준 유효성 검사를 구현 하는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="25740-175">While custom validation attributes work well for validating individual properties, implementing `IValidatableObject` can be used to implement class-level validation as seen here.</span></span>

<span data-ttu-id="25740-176">[!code-csharp[Main](validation/sample/MovieIValidatable.cs?range=33-41)]</span><span class="sxs-lookup"><span data-stu-id="25740-176">[!code-csharp[Main](validation/sample/MovieIValidatable.cs?range=33-41)]</span></span>

## <a name="client-side-validation"></a><span data-ttu-id="25740-177">클라이언트 쪽 유효성 검사</span><span class="sxs-lookup"><span data-stu-id="25740-177">Client side validation</span></span>

<span data-ttu-id="25740-178">클라이언트 쪽 유효성 검사는 사용자에 대 한 매우 편리 합니다.</span><span class="sxs-lookup"><span data-stu-id="25740-178">Client side validation is a great convenience for users.</span></span> <span data-ttu-id="25740-179">시간을 절약 그렇지 않은 경우 많은 비용이 들어가는지는 왕복에 대 한 대기 중인 서버.</span><span class="sxs-lookup"><span data-stu-id="25740-179">It saves time they would otherwise spend waiting for a round trip to the server.</span></span> <span data-ttu-id="25740-180">비즈니스 관계에서 몇 초 부분을 곱한 수백 번 매일 추가 최대 많은 시간과 비용, 노력 합니다.</span><span class="sxs-lookup"><span data-stu-id="25740-180">In business terms, even a few fractions of seconds multiplied hundreds of times each day adds up to be a lot of time, expense, and frustration.</span></span> <span data-ttu-id="25740-181">간단 하 고 즉시 유효성 검사를 사용 하면 보다 효율적으로 작동 하 고 더 좋은 품질 입력 및 출력을 생성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="25740-181">Straightforward and immediate validation enables users to work more efficiently and produce better quality input and output.</span></span>

<span data-ttu-id="25740-182">여기에 표시 된 대로 작동 하도록 클라이언트 쪽 유효성 검사에 대 한 위치에서 적절 한 JavaScript 스크립트 참조 하 여 뷰에 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="25740-182">You must have a view with the proper JavaScript script references in place for client side validation to work as you see here.</span></span>

<span data-ttu-id="25740-183">[!code-html[Main](validation/sample/Views/Shared/_Layout.cshtml?range=37)]</span><span class="sxs-lookup"><span data-stu-id="25740-183">[!code-html[Main](validation/sample/Views/Shared/_Layout.cshtml?range=37)]</span></span>

<span data-ttu-id="25740-184">[!code-html[Main](validation/sample/Views/Shared/_ValidationScriptsPartial.cshtml)]</span><span class="sxs-lookup"><span data-stu-id="25740-184">[!code-html[Main](validation/sample/Views/Shared/_ValidationScriptsPartial.cshtml)]</span></span>

<span data-ttu-id="25740-185">MVC는 데이터 유효성 검사 및 JavaScript를 사용 하 여 오류 메시지를 표시 하려면 모델 속성의 형식 메타 데이터 외에도 유효성 검사 특성을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="25740-185">MVC uses validation attributes in addition to type metadata from model properties to validate data and display any error messages using JavaScript.</span></span> <span data-ttu-id="25740-186">MVC 폼 요소에서 사용 하 여 모델을 렌더링을 사용 하는 경우 [태그 도우미](xref:mvc/views/tag-helpers/intro) 또는 [HTML 도우미](xref:mvc/views/overview) HTML 5를 추가 합니다 [데이터 특성](http://w3c.github.io/html/dom.html#embedding-custom-non-visible-data-with-the-data-attributes) 로 유효성 검사를 필요로 하는 폼 요소에서 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="25740-186">When you use MVC to render form elements from a model using [Tag Helpers](xref:mvc/views/tag-helpers/intro) or [HTML helpers](xref:mvc/views/overview) it will add HTML 5 [data- attributes](http://w3c.github.io/html/dom.html#embedding-custom-non-visible-data-with-the-data-attributes) in the form elements that need validation, as shown below.</span></span> <span data-ttu-id="25740-187">MVC에서는 오류가 발생 하는 `data-` 기본 제공 및 사용자 지정 특성에 대 한 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="25740-187">MVC generates the `data-` attributes for both built-in and custom attributes.</span></span> <span data-ttu-id="25740-188">다음과 같이 관련 태그 도우미를 사용 하 여 클라이언트에서 유효성 검사 오류를 표시할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="25740-188">You can display validation errors on the client using the relevant tag helpers as shown here:</span></span>

<span data-ttu-id="25740-189">[!code-html[Main](validation/sample/Views/Movies/Create.cshtml?highlight=4,5&range=19-25)]</span><span class="sxs-lookup"><span data-stu-id="25740-189">[!code-html[Main](validation/sample/Views/Movies/Create.cshtml?highlight=4,5&range=19-25)]</span></span>

<span data-ttu-id="25740-190">위의 태그 도우미 아래 HTML을 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="25740-190">The tag helpers above render the HTML below.</span></span> <span data-ttu-id="25740-191">다음에 유의 `data-` HTML의 특성에 대 한 유효성 검사 특성에 해당 출력은 `ReleaseDate` 속성입니다.</span><span class="sxs-lookup"><span data-stu-id="25740-191">Notice that the `data-` attributes in the HTML output correspond to the validation attributes for the `ReleaseDate` property.</span></span> <span data-ttu-id="25740-192">`data-val-required` 릴리스 날짜 필드에 사용자 이름을 입력 하지 않는 경우에 함께 제공 되는 메시지가 표시 됩니다. 표시할 오류 메시지를 포함 하는 특성을 아래 `<span>` 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="25740-192">The `data-val-required` attribute below contains an error message to display if the user doesn't fill in the release date field, and that message displays in the accompanying `<span>` element.</span></span>

```html
<form action="/Movies/Create" method="post">
    <div class="form-horizontal">
        <h4>Movie</h4>
        <div class="text-danger"></div>
        <div class="form-group">
            <label class="col-md-2 control-label" for="ReleaseDate">ReleaseDate</label>
            <div class="col-md-10">
                <input class="form-control" type="datetime"
                data-val="true" data-val-required="The ReleaseDate field is required."
                id="ReleaseDate" name="ReleaseDate" value="" />
                <span class="text-danger field-validation-valid"
                data-valmsg-for="ReleaseDate" data-valmsg-replace="true"></span>
            </div>
        </div>
    </div>
</form>
```

<span data-ttu-id="25740-193">폼이 유효한 될 때까지 전송을 방지 하는 클라이언트 쪽 유효성 검사 합니다.</span><span class="sxs-lookup"><span data-stu-id="25740-193">Client-side validation prevents submission until the form is valid.</span></span> <span data-ttu-id="25740-194">제출 단추가 양식을 전송 또는 오류 메시지를 표시 하는 JavaScript를 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="25740-194">The Submit button runs JavaScript that either submits the form or displays error messages.</span></span>

<span data-ttu-id="25740-195">MVC 유형 특성 값을 사용 하 여를 재정의할 수 있는 속성의.NET 데이터 형식을 기반으로 결정 `[DataType]` 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="25740-195">MVC determines type attribute values based on the .NET data type of a property, possibly overridden using `[DataType]` attributes.</span></span> <span data-ttu-id="25740-196">기본 `[DataType]` 특성 실제 서버 쪽 유효성 검사가 수행 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="25740-196">The base `[DataType]` attribute does no real server-side validation.</span></span> <span data-ttu-id="25740-197">그러나 브라우저 자신의 오류 메시지를 선택 하 고 만들려는, jQuery 유효성 검사 비간섭 패키지는 메시지를 무시 하 고 다른 사용자와 일관 되 게 표시 수도 있지만 이러한 오류를 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="25740-197">Browsers choose their own error messages and display those errors however they wish, however the jQuery Validation Unobtrusive package can override the messages and display them consistently with others.</span></span> <span data-ttu-id="25740-198">사용자를 적용할 때 가장 분명 이런 `[DataType]` 하위 클래스와 같은 `[EmailAddress]`합니다.</span><span class="sxs-lookup"><span data-stu-id="25740-198">This happens most obviously when users apply `[DataType]` subclasses such as `[EmailAddress]`.</span></span>

## <a name="iclientmodelvalidator"></a><span data-ttu-id="25740-199">IClientModelValidator</span><span class="sxs-lookup"><span data-stu-id="25740-199">IClientModelValidator</span></span>

<span data-ttu-id="25740-200">사용자 지정 특성에 대 한 클라이언트 쪽 논리를 만들 수 있습니다 및 [비간섭 유효성 검사](http://jqueryvalidation.org/documentation/) 클라이언트에 유효성 검사의 일부로 자동으로 실행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="25740-200">You may create client side logic for your custom attribute, and [unobtrusive validation](http://jqueryvalidation.org/documentation/) will execute it on the client for you automatically as part of validation.</span></span> <span data-ttu-id="25740-201">첫 번째 단계를 구현 하 여 어떤 데이터 특성 추가 제어 하는 것은 `IClientModelValidator` 다음과 같이 인터페이스:</span><span class="sxs-lookup"><span data-stu-id="25740-201">The first step is to control what data- attributes are added by implementing the `IClientModelValidator` interface as shown here:</span></span>

<span data-ttu-id="25740-202">[!code-csharp[Main](validation/sample/ClassicMovieAttribute.cs?range=30-42)]</span><span class="sxs-lookup"><span data-stu-id="25740-202">[!code-csharp[Main](validation/sample/ClassicMovieAttribute.cs?range=30-42)]</span></span>

<span data-ttu-id="25740-203">이 인터페이스를 구현 하는 특성이 생성 된 필드를 HTML 특성을 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="25740-203">Attributes that implement this interface can add HTML attributes to generated fields.</span></span> <span data-ttu-id="25740-204">에 대 한 출력을 검사 하는 `ReleaseDate` 요소는 이전 예제와 비슷하지만 이제는 HTML을 표시는 `data-val-classicmovie` 에 정의 된 특성의 `AddValidation` 방식의 `IClientModelValidator`합니다.</span><span class="sxs-lookup"><span data-stu-id="25740-204">Examining the output for the `ReleaseDate` element reveals HTML that is similar to the previous example, except now there is a `data-val-classicmovie` attribute that was defined in the `AddValidation` method of `IClientModelValidator`.</span></span>

```html
<input class="form-control" type="datetime"
data-val="true"
data-val-classicmovie="Classic movies must have a release year earlier than 1960."
data-val-classicmovie-year="1960"
data-val-required="The ReleaseDate field is required."
id="ReleaseDate" name="ReleaseDate" value="" />
```

<span data-ttu-id="25740-205">비 가시적인 유효성 검사에서 데이터를 사용 하는 `data-` 특성 오류 메시지를 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="25740-205">Unobtrusive validation uses the data in the `data-` attributes to display error messages.</span></span> <span data-ttu-id="25740-206">그러나 jQuery 규칙을 인식 하지 못합니다 또는 jQuery의에 추가 될 때까지 메시지 `validator` 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="25740-206">However, jQuery doesn't know about rules or messages until you add them to jQuery's `validator` object.</span></span> <span data-ttu-id="25740-207">라는 메서드를 추가 하는 아래 예제에서이 확인할 `classicmovie` jQuery로 사용자 지정 클라이언트 유효성 검사 코드가 포함 된 `validator` 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="25740-207">This is shown in the example below that adds a method named `classicmovie` containing custom client validation code to the jQuery `validator` object.</span></span>

<span data-ttu-id="25740-208">[!code-javascript[Main](validation/sample/Views/Movies/Create.cshtml?range=71-93)]</span><span class="sxs-lookup"><span data-stu-id="25740-208">[!code-javascript[Main](validation/sample/Views/Movies/Create.cshtml?range=71-93)]</span></span>

<span data-ttu-id="25740-209">이제 jQuery 유효성 검사 코드는 false를 반환 하는 경우 표시할 오류 메시지 뿐 아니라 사용자 지정 JavaScript 유효성 검사를 실행 하는 정보를 있습니다.</span><span class="sxs-lookup"><span data-stu-id="25740-209">Now jQuery has the information to execute the custom JavaScript validation as well as the error message to display if that validation code returns false.</span></span>

## <a name="remote-validation"></a><span data-ttu-id="25740-210">원격 유효성 검사</span><span class="sxs-lookup"><span data-stu-id="25740-210">Remote validation</span></span>

<span data-ttu-id="25740-211">원격 유효성 검사는 서버에서 데이터에 대 한 클라이언트에서 데이터의 유효성을 검사 해야 할 때 사용할 수 있는 유용한 기능입니다.</span><span class="sxs-lookup"><span data-stu-id="25740-211">Remote validation is a great feature to use when you need to validate data on the client against data on the server.</span></span> <span data-ttu-id="25740-212">예를 들어 앱 여부는 전자 메일 또는 사용자 이름이 이미 사용에서 되 고 많은 양의 데이터를 쿼리해야 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="25740-212">For example, your app may need to verify whether an email or user name is already in use, and it must query a large amount of data to do so.</span></span> <span data-ttu-id="25740-213">하나는 유효성 검사에 대 한 데이터 집합 다운로드 큰 또는 할 필드가 단 몇 너무 많은 리소스를 소비 합니다.</span><span class="sxs-lookup"><span data-stu-id="25740-213">Downloading large sets of data for validating one or a few fields consumes too many resources.</span></span> <span data-ttu-id="25740-214">중요 한 정보를 노출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="25740-214">It may also expose sensitive information.</span></span> <span data-ttu-id="25740-215">필드의 유효성을 검사 하는 왕복 요청 하는 대신이입니다.</span><span class="sxs-lookup"><span data-stu-id="25740-215">An alternative is to make a round-trip request to validate a field.</span></span>

<span data-ttu-id="25740-216">2 단계 프로세스에서 원격 유효성 검사를 구현할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="25740-216">You can implement remote validation in a two step process.</span></span> <span data-ttu-id="25740-217">첫째, 주석으로 처리 해야 사용 하 여 모델의 `[Remote]` 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="25740-217">First, you must annotate your model with the `[Remote]` attribute.</span></span> <span data-ttu-id="25740-218">`[Remote]` 특성 직접 클라이언트 쪽 JavaScript는 해당 코드를 호출 하는 데 사용할 수 있는 여러 오버 로드를 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="25740-218">The `[Remote]` attribute accepts multiple overloads you can use to direct client side JavaScript to the appropriate code to call.</span></span> <span data-ttu-id="25740-219">이 예제에서는 가리키는 `VerifyEmail` 의 동작 메서드는 `Users` 컨트롤러입니다.</span><span class="sxs-lookup"><span data-stu-id="25740-219">The example points to the `VerifyEmail` action method of the `Users` controller.</span></span>

<span data-ttu-id="25740-220">[!code-csharp[Main](validation/sample/User.cs?range=5-9)]</span><span class="sxs-lookup"><span data-stu-id="25740-220">[!code-csharp[Main](validation/sample/User.cs?range=5-9)]</span></span>

<span data-ttu-id="25740-221">두 번째 단계는 지정 하는 것 유효성 검사 코드는 해당 작업 메서드에서 정의 된 대로 `[Remote]` 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="25740-221">The second step is putting the validation code in the corresponding action method as defined in the `[Remote]` attribute.</span></span> <span data-ttu-id="25740-222">반환 된 `JsonResult` 클라이언트 쪽 계속 일시 중지 하 고 필요한 경우 오류를 표시 하는 데 사용할 수 있는 합니다.</span><span class="sxs-lookup"><span data-stu-id="25740-222">It returns a `JsonResult` that the client side can use to proceed or pause and display an error if needed.</span></span>

<span data-ttu-id="25740-223">[!code-none[Main](validation/sample/UsersController.cs?range=19-28)]</span><span class="sxs-lookup"><span data-stu-id="25740-223">[!code-none[Main](validation/sample/UsersController.cs?range=19-28)]</span></span>

<span data-ttu-id="25740-224">그러면 사용자가 전자 메일을 입력 하면 보기에는 JavaScript 메일 수행 된 경우 참조에 대 한 원격 호출에서는 이제 및 오류 메시지가 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="25740-224">Now when users enter an email, JavaScript in the view makes a remote call to see if that email has been taken, and if so, then displays the error message.</span></span> <span data-ttu-id="25740-225">그렇지 않으면 사용자는 일반적으로 폼을 제출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="25740-225">Otherwise, the user can submit the form as usual.</span></span>
