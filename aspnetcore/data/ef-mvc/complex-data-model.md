---
title: "ASP.NET Core MVC EF 코어-5/10-데이터 모델"
author: tdykstra
description: "이 자습서에서는 더 많은 엔터티 및 관계를 추가 및 서식 지정, 유효성 검사 및 데이터베이스 매핑 규칙을 지정 하 여 데이터 모델을 사용자 지정 합니다."
keywords: "ASP.NET Core, Entity Framework Core 데이터 주석"
ms.author: tdykstra
manager: wpickett
ms.date: 03/15/2017
ms.topic: get-started-article
ms.assetid: 0dd63913-a041-48b6-96a4-3aeaedbdf5d0
ms.technology: aspnet
ms.prod: asp.net-core
uid: data/ef-mvc/complex-data-model
ms.openlocfilehash: a9e255040c300bc5ce55a356e17e6912dbaeaf88
ms.sourcegitcommit: 9cdbfd0d670d70b9c354216aabee260c52dad5ee
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/12/2017
---
# <a name="creating-a-complex-data-model---ef-core-with-aspnet-core-mvc-tutorial-5-of-10"></a><span data-ttu-id="59475-104">복잡 한 데이터 모델-EF 코어 ASP.NET Core MVC 자습서 (5/10) 만들기</span><span class="sxs-lookup"><span data-stu-id="59475-104">Creating a complex data model - EF Core with ASP.NET Core MVC tutorial (5 of 10)</span></span>

<span data-ttu-id="59475-105">여 [Tom Dykstra](https://github.com/tdykstra) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="59475-105">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="59475-106">Contoso 대학 샘플 웹 응용 프로그램에는 Entity Framework Core 및 Visual Studio를 사용 하 여 ASP.NET Core MVC 웹 응용 프로그램을 만드는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="59475-106">The Contoso University sample web application demonstrates how to create ASP.NET Core MVC web applications using Entity Framework Core and Visual Studio.</span></span> <span data-ttu-id="59475-107">자습서 시리즈에 대 한 정보를 참조 하십시오. [시리즈의 첫 번째 자습서](intro.md)합니다.</span><span class="sxs-lookup"><span data-stu-id="59475-107">For information about the tutorial series, see [the first tutorial in the series](intro.md).</span></span>

<span data-ttu-id="59475-108">이전 자습서에서 사용한 세 가지 엔터티 작성 된 간단한 데이터 모델입니다.</span><span class="sxs-lookup"><span data-stu-id="59475-108">In the previous tutorials, you worked with a simple data model that was composed of three entities.</span></span> <span data-ttu-id="59475-109">이 자습서에서는 더 많은 엔터티 및 관계를 추가 합니다 및 서식 지정, 유효성 검사 및 데이터베이스 매핑 규칙을 지정 하 여 데이터 모델을 사용자 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="59475-109">In this tutorial, you'll add more entities and relationships and you'll customize the data model by specifying formatting, validation, and database mapping rules.</span></span>

<span data-ttu-id="59475-110">완료 되 면, 엔터티 클래스는 다음 그림에 표시 되는 완성 된 데이터 모델을 구성 하는:</span><span class="sxs-lookup"><span data-stu-id="59475-110">When you're finished, the entity classes will make up the completed data model that's shown in the following illustration:</span></span>

![엔터티 다이어그램](complex-data-model/_static/diagram.png)

## <a name="customize-the-data-model-by-using-attributes"></a><span data-ttu-id="59475-112">특성을 사용 하 여 데이터 모델을 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="59475-112">Customize the Data Model by Using Attributes</span></span>

<span data-ttu-id="59475-113">이 섹션의 서식, 유효성 검사 및 데이터베이스 매핑 규칙을 지정 하는 특성을 사용 하 여 데이터 모델을 사용자 지정 하는 방법을 배웁니다.</span><span class="sxs-lookup"><span data-stu-id="59475-113">In this section you'll see how to customize the data model by using attributes that specify formatting, validation, and database mapping rules.</span></span> <span data-ttu-id="59475-114">다음 만들게 다음 섹션 중 몇 개에 추가 하 여 전체 학교 데이터 모델을 특성 클래스를 이미 만든 하 고 모델의 나머지 엔터티 형식에 대 한 새 클래스를 만들어입니다.</span><span class="sxs-lookup"><span data-stu-id="59475-114">Then in several of the following sections you'll create the complete School data model by adding attributes to the classes you already created and creating new classes for the remaining entity types in the model.</span></span>

### <a name="the-datatype-attribute"></a><span data-ttu-id="59475-115">데이터 형식 특성</span><span class="sxs-lookup"><span data-stu-id="59475-115">The DataType attribute</span></span>

<span data-ttu-id="59475-116">학생 등록 날짜에 대 한 모든 웹 페이지의 현재 시간을 표시, 날짜와 함께 날짜는이 필드에 대 한 필요 하므로.</span><span class="sxs-lookup"><span data-stu-id="59475-116">For student enrollment dates, all of the web pages currently display the time along with the date, although all you care about for this field is the date.</span></span> <span data-ttu-id="59475-117">데이터 주석 특성을 사용 하 여 하나 만들 수 있습니다 코드는 데이터를 표시 하는 모든 보기에서 표시 형식을 해결 하는 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="59475-117">By using data annotation attributes, you can make one code change that will fix the display format in every view that shows the data.</span></span> <span data-ttu-id="59475-118">특성을 추가 합니다 즉, 작업을 수행 하는 방법의 예를 보려면는 `EnrollmentDate` 속성에는 `Student` 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="59475-118">To see an example of how to do that, you'll add an attribute to the `EnrollmentDate` property in the `Student` class.</span></span>

<span data-ttu-id="59475-119">*Models/Student.cs*, 추가 `using` 문에 `System.ComponentModel.DataAnnotations` 네임 스페이스 추가 `DataType` 및 `DisplayFormat` 특성을 `EnrollmentDate` 속성을 다음 예제와 같이:</span><span class="sxs-lookup"><span data-stu-id="59475-119">In *Models/Student.cs*, add a `using` statement for the `System.ComponentModel.DataAnnotations` namespace and add `DataType` and `DisplayFormat` attributes to the `EnrollmentDate` property, as shown in the following example:</span></span>

<span data-ttu-id="59475-120">[!code-csharp[Main](intro/samples/cu/Models/Student.cs?name=snippet_DataType&highlight=3,12-13)]</span><span class="sxs-lookup"><span data-stu-id="59475-120">[!code-csharp[Main](intro/samples/cu/Models/Student.cs?name=snippet_DataType&highlight=3,12-13)]</span></span>

<span data-ttu-id="59475-121">`DataType` 특성 데이터베이스 내장 형식 보다 구체적인 데이터 형식을 지정 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="59475-121">The `DataType` attribute is used to specify a data type that is more specific than the database intrinsic type.</span></span> <span data-ttu-id="59475-122">이 경우에 포함 하려고 날짜와 시간에서 날짜를 추적 하 합니다.</span><span class="sxs-lookup"><span data-stu-id="59475-122">In this case we only want to keep track of the date, not the date and time.</span></span> <span data-ttu-id="59475-123">`DataType` 날짜, 시간, 전화 번호, 통화, EmailAddress, 등과 같은 많은 데이터 형식의 열거형을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="59475-123">The  `DataType` Enumeration provides for many data types, such as Date, Time, PhoneNumber, Currency, EmailAddress, and more.</span></span> <span data-ttu-id="59475-124">`DataType` 특성 유형별 기능을 자동으로 제공 하도록 응용 프로그램 사용 하도록 설정할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="59475-124">The `DataType` attribute can also enable the application to automatically provide type-specific features.</span></span> <span data-ttu-id="59475-125">예를 들어 한 `mailto:` 에 대 한 링크를 만들 수 `DataType.EmailAddress`, 날짜 선택 기가 제공 될 수 있습니다 및 `DataType.Date` HTML5 지원 브라우저에서.</span><span class="sxs-lookup"><span data-stu-id="59475-125">For example, a `mailto:` link can be created for `DataType.EmailAddress`, and a date selector can be provided for `DataType.Date` in browsers that support HTML5.</span></span> <span data-ttu-id="59475-126">`DataType` 특성 내보냅니다 HTML 5 `data-` (커맨드 데이터 대시) 특성을 HTML 5 브라우저 이해할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="59475-126">The `DataType` attribute emits HTML 5 `data-` (pronounced data dash) attributes that HTML 5 browsers can understand.</span></span> <span data-ttu-id="59475-127">`DataType` 특성 유효성을 검사 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="59475-127">The `DataType` attributes do not provide any validation.</span></span>

<span data-ttu-id="59475-128">`DataType.Date`표시 되는 날짜의 형식을 지정 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="59475-128">`DataType.Date` does not specify the format of the date that is displayed.</span></span> <span data-ttu-id="59475-129">기본적으로 서버의 CultureInfo에 따라 기본 형식에 따라 데이터 필드가 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="59475-129">By default, the data field is displayed according to the default formats based on the server's CultureInfo.</span></span>

<span data-ttu-id="59475-130">`DisplayFormat` 특성 날짜 형식을 명시적으로 지정 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="59475-130">The `DisplayFormat` attribute is used to explicitly specify the date format:</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

<span data-ttu-id="59475-131">`ApplyFormatInEditMode` 설정은 서식을 적용 해야 하는지도 값 편집을 위해 텍스트 상자에 표시 되 면을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="59475-131">The `ApplyFormatInEditMode` setting specifies that the formatting should also be applied when the value is displayed in a text box for editing.</span></span> <span data-ttu-id="59475-132">(하지 않을-일부 필드에 대 한 예를 들어 통화 값에 대 한 수 원하지 텍스트 상자에 통화 기호 편집 합니다.)</span><span class="sxs-lookup"><span data-stu-id="59475-132">(You might not want that for some fields -- for example, for currency values, you might not want the currency symbol in the text box for editing.)</span></span>

<span data-ttu-id="59475-133">사용할 수는 `DisplayFormat` 하지만 자체를 기준으로 특성은 일반적으로 사용 하는 것이 좋습니다는 `DataType` 도 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="59475-133">You can use the `DisplayFormat` attribute by itself, but it's generally a good idea to use the `DataType` attribute also.</span></span> <span data-ttu-id="59475-134">`DataType` 특성 데이터를 화면에 렌더링 하는 방법 전송할의 의미를 전달 하 고으로 볼 수 없는 다음과 같은 이점을 제공 `DisplayFormat`:</span><span class="sxs-lookup"><span data-stu-id="59475-134">The `DataType` attribute conveys the semantics of the data as opposed to how to render it on a screen, and provides the following benefits that you don't get with `DisplayFormat`:</span></span>

* <span data-ttu-id="59475-135">브라우저는 HTML5 기능을 활성화할 수 (calendar 컨트롤, 로캘에 적합 한 통화 기호, 전자 메일 링크를 표시 하는 등 일부 클라이언트 쪽 입력 유효성 검사, 등입니다.).</span><span class="sxs-lookup"><span data-stu-id="59475-135">The browser can enable HTML5 features (for example to show a calendar control, the locale-appropriate currency symbol, email links, some client-side input validation, etc.).</span></span>

* <span data-ttu-id="59475-136">기본적으로 브라우저는 사용자의 로캘에 따른 올바른 형식을 사용 하 여 데이터를 렌더링 합니다.</span><span class="sxs-lookup"><span data-stu-id="59475-136">By default, the browser will render data using the correct format based on your locale.</span></span>

<span data-ttu-id="59475-137">자세한 내용은 참조는 [ \<입력 > 도우미 설명서 태그](../../mvc/views/working-with-forms.md#the-input-tag-helper)합니다.</span><span class="sxs-lookup"><span data-stu-id="59475-137">For more information, see the [\<input> tag helper documentation](../../mvc/views/working-with-forms.md#the-input-tag-helper).</span></span>

<span data-ttu-id="59475-138">학생 인덱스 페이지를 다시 실행 하 고 등록 날짜에 대 한 번 더 이상 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="59475-138">Run the Students Index page again and notice that times are no longer displayed for the enrollment dates.</span></span> <span data-ttu-id="59475-139">동일한 학생 모델을 사용 하는 다른 보기에 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="59475-139">The same will be true for any view that uses the Student model.</span></span>

![학생 인덱스 페이지 시간 없이 날짜를 표시 합니다.](complex-data-model/_static/dates-no-times.png)

### <a name="the-stringlength-attribute"></a><span data-ttu-id="59475-141">StringLength 특성</span><span class="sxs-lookup"><span data-stu-id="59475-141">The StringLength attribute</span></span>

<span data-ttu-id="59475-142">데이터 유효성 검사 규칙 및 유효성 검사 오류 메시지 특성을 사용 하 여 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="59475-142">You can also specify data validation rules and validation error messages using attributes.</span></span> <span data-ttu-id="59475-143">`StringLength` 특성 데이터베이스의 최대 길이 설정 하 고 클라이언트측 및 서버측 제공 ASP.NET MVC에 대 한 유효성을 검사 합니다.</span><span class="sxs-lookup"><span data-stu-id="59475-143">The `StringLength` attribute sets the maximum length  in the database and provides client side and server side validation for ASP.NET MVC.</span></span> <span data-ttu-id="59475-144">이 특성에는 최소 문자열 길이 지정할 수도 있습니다 수 있지만 최소값 데이터베이스 스키마에 대 한 영향을 주지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="59475-144">You can also specify the minimum string length in this attribute, but the minimum value has no impact on the database schema.</span></span>

<span data-ttu-id="59475-145">사용자가을 이름에 대 한 50 개 이상의 문자를 입력 하지 마세요 되도록 한다고 가정 합니다.</span><span class="sxs-lookup"><span data-stu-id="59475-145">Suppose you want to ensure that users don't enter more than 50 characters for a name.</span></span> <span data-ttu-id="59475-146">이 제한 사항은 추가 하려면 추가 `StringLength` 특성에 `LastName` 및 `FirstMidName` 다음 예제와 같이 속성:</span><span class="sxs-lookup"><span data-stu-id="59475-146">To add this limitation, add `StringLength` attributes to the `LastName` and `FirstMidName` properties, as shown in the following example:</span></span>

<span data-ttu-id="59475-147">[!code-csharp[Main](intro/samples/cu/Models/Student.cs?name=snippet_StringLength&highlight=10,12)]</span><span class="sxs-lookup"><span data-stu-id="59475-147">[!code-csharp[Main](intro/samples/cu/Models/Student.cs?name=snippet_StringLength&highlight=10,12)]</span></span>

<span data-ttu-id="59475-148">`StringLength` 특성 이름에 공백을 입력에서 사용자를 금지 되지는 않습니다.</span><span class="sxs-lookup"><span data-stu-id="59475-148">The `StringLength` attribute won't prevent a user from entering white space for a name.</span></span> <span data-ttu-id="59475-149">사용할 수는 `RegularExpression` 특성을 입력에 제한을 적용 합니다.</span><span class="sxs-lookup"><span data-stu-id="59475-149">You can use the `RegularExpression` attribute to apply restrictions to the input.</span></span> <span data-ttu-id="59475-150">예를 들어 다음 코드는 첫 번째 문자를 대문자로 변환 하 고 나머지 문자를 사전순으로 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="59475-150">For example, the following code requires the first character to be upper case and the remaining characters to be alphabetical:</span></span>

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z''-'\s]*$")]
```

<span data-ttu-id="59475-151">`MaxLength` 특성 비슷한 기능을 제공는 `StringLength` 특성 하지만 클라이언트 쪽을 제공 하지 않는 유효성 검사 합니다.</span><span class="sxs-lookup"><span data-stu-id="59475-151">The `MaxLength` attribute provides functionality similar to the `StringLength` attribute but doesn't provide client side validation.</span></span>

<span data-ttu-id="59475-152">이제는 데이터베이스 모델 데이터베이스 스키마의 변경 해야 하는 방식으로 변경 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="59475-152">The database model has now changed in a way that requires a change in the database schema.</span></span> <span data-ttu-id="59475-153">마이그레이션은 응용 프로그램 UI를 사용 하 여 데이터베이스에 추가 될 수 있습니다 있습니다 데이터 손실 없이 스키마를 업데이트 하려면 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="59475-153">You'll use migrations to update the schema without losing any data that you may have added to the database by using the application UI.</span></span>

<span data-ttu-id="59475-154">변경 내용을 저장 하 고 프로젝트를 빌드하십시오.</span><span class="sxs-lookup"><span data-stu-id="59475-154">Save your changes and build the project.</span></span> <span data-ttu-id="59475-155">그런 다음 프로젝트 폴더에 명령 창을 열고 하 고 다음 명령을 입력 합니다.</span><span class="sxs-lookup"><span data-stu-id="59475-155">Then open the command window in the project folder and enter the following commands:</span></span>

```console
dotnet ef migrations add MaxLengthOnNames
```

```console
dotnet ef database update
```

<span data-ttu-id="59475-156">`migrations add` 명령 데이터 손실이 발생할 수 있습니다는 변경을 통해 두 개의 열에 대 한 짧은 최대 길이 때문에 경고 합니다.</span><span class="sxs-lookup"><span data-stu-id="59475-156">The `migrations add` command warns that data loss may occur, because the change makes the maximum length shorter for two columns.</span></span>  <span data-ttu-id="59475-157">마이그레이션 라는 파일을 만듭니다 * \<타임 스탬프 > _MaxLengthOnNames.cs*합니다.</span><span class="sxs-lookup"><span data-stu-id="59475-157">Migrations creates a file named *\<timeStamp>_MaxLengthOnNames.cs*.</span></span> <span data-ttu-id="59475-158">이 파일의 코드에 포함 된 `Up` 현재 데이터 모델과 일치 하도록 데이터베이스를 업데이트 하는 메서드.</span><span class="sxs-lookup"><span data-stu-id="59475-158">This file contains code in the `Up` method that will update the database to match the current data model.</span></span> <span data-ttu-id="59475-159">`database update` 명령이 해당 코드를 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="59475-159">The `database update` command ran that code.</span></span>

<span data-ttu-id="59475-160">마이그레이션 파일 이름에 접두사로 타임 스탬프를 마이그레이션 요청 Entity Framework에서 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="59475-160">The timestamp prefixed to the migrations file name is used by Entity Framework to order the migrations.</span></span> <span data-ttu-id="59475-161">마이그레이션을 여러 건 update-database 명령을 실행 하기 전에 만들고 생성 된 순서에 적용 됩니다는 마이그레이션의 모든 합니다.</span><span class="sxs-lookup"><span data-stu-id="59475-161">You can create multiple migrations before running the update-database command, and then all of the migrations are applied in the order in which they were created.</span></span>

<span data-ttu-id="59475-162">만들기 페이지를 실행 하 고 50 자 보다 긴 이름 중 하나를 입력 합니다.</span><span class="sxs-lookup"><span data-stu-id="59475-162">Run the Create page, and enter either name longer than 50 characters.</span></span> <span data-ttu-id="59475-163">만들기를 클릭 하면 클라이언트 쪽 유효성 검사 오류 메시지가 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="59475-163">When you click Create, client side validation shows an error message.</span></span>

![학생 인덱스 문자열 길이 오류를 보여주는 페이지](complex-data-model/_static/string-length-errors.png)

### <a name="the-column-attribute"></a><span data-ttu-id="59475-165">Column 특성</span><span class="sxs-lookup"><span data-stu-id="59475-165">The Column attribute</span></span>

<span data-ttu-id="59475-166">클래스 및 속성을 데이터베이스에 매핑되는 방법을 제어 하려면 특성을 사용할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="59475-166">You can also use attributes to control how your classes and properties are mapped to the database.</span></span> <span data-ttu-id="59475-167">이름을 사용한 경우를 가정해 볼 `FirstMidName` 첫 번째 이름에 대 한 필드 중간 이름 포함도 될 수 있으므로 필드입니다.</span><span class="sxs-lookup"><span data-stu-id="59475-167">Suppose you had used the name `FirstMidName` for the first-name field because the field might also contain a middle name.</span></span> <span data-ttu-id="59475-168">이름을 지정 하는 데이터베이스 열을 원하는 하지만 `FirstName`해당 이름에는 데이터베이스에 대 한 임시 쿼리를 작성 하는 사용자는 대개 때문에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="59475-168">But you want the database column to be named `FirstName`, because users who will be writing ad-hoc queries against the database are accustomed to that name.</span></span> <span data-ttu-id="59475-169">이 매핑은를 사용할 수 있습니다는 `Column` 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="59475-169">To make this mapping, you can use the `Column` attribute.</span></span>

<span data-ttu-id="59475-170">`Column` 특성을 지정 하는 데이터베이스를 만들 때의 열은 `Student` 테이블에 매핑되는 `FirstMidName` 속성 이름이 지정 됩니다 `FirstName`합니다.</span><span class="sxs-lookup"><span data-stu-id="59475-170">The `Column` attribute specifies that when the database is created, the column of the `Student` table that maps to the `FirstMidName` property will be named `FirstName`.</span></span> <span data-ttu-id="59475-171">즉 때 코드 참조 `Student.FirstMidName`, 데이터에서 옵니다 또는에서 업데이트할 수는 `FirstName` 의 열은 `Student` 테이블입니다.</span><span class="sxs-lookup"><span data-stu-id="59475-171">In other words, when your code refers to `Student.FirstMidName`, the data will come from or be updated in the `FirstName` column of the `Student` table.</span></span> <span data-ttu-id="59475-172">열 이름을 지정 하지 않으면, 속성 이름과 같은 이름이 주어 집니다.</span><span class="sxs-lookup"><span data-stu-id="59475-172">If you don't specify column names, they are given the same name as the property name.</span></span>

<span data-ttu-id="59475-173">에 *Student.cs* 파일에서 추가 `using` 문을 `System.ComponentModel.DataAnnotations.Schema` 열 이름 특성을 추가 하 고는 `FirstMidName` 속성을 다음 강조 표시 된 코드에 나와 있는 것 처럼:</span><span class="sxs-lookup"><span data-stu-id="59475-173">In the *Student.cs* file, add a `using` statement for `System.ComponentModel.DataAnnotations.Schema` and add the column name attribute to the `FirstMidName` property, as shown in the following highlighted code:</span></span>

<span data-ttu-id="59475-174">[!code-csharp[Main](intro/samples/cu/Models/Student.cs?name=snippet_Column&highlight=4,14)]</span><span class="sxs-lookup"><span data-stu-id="59475-174">[!code-csharp[Main](intro/samples/cu/Models/Student.cs?name=snippet_Column&highlight=4,14)]</span></span>

<span data-ttu-id="59475-175">추가 `Column` 특성이 변경 모델이 `SchoolContext`이므로 데이터베이스를 일치 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="59475-175">The addition of the `Column` attribute changes the model backing the `SchoolContext`, so it won't match the database.</span></span>

<span data-ttu-id="59475-176">변경 내용을 저장 하 고 프로젝트를 빌드하십시오.</span><span class="sxs-lookup"><span data-stu-id="59475-176">Save your changes and build the project.</span></span> <span data-ttu-id="59475-177">그런 다음 프로젝트 폴더에 명령 창을 열고 하 고 다른 마이그레이션 만들려면 다음 명령을 입력 합니다.</span><span class="sxs-lookup"><span data-stu-id="59475-177">Then open the command window in the project folder and enter the following commands to create another migration:</span></span>

```console
dotnet ef migrations add ColumnFirstName
```

```console
dotnet ef database update
```

<span data-ttu-id="59475-178">**SQL Server 개체 탐색기**를 두 번 클릭 하 여 학생 테이블 디자이너를 열고는 **학생** 테이블입니다.</span><span class="sxs-lookup"><span data-stu-id="59475-178">In **SQL Server Object Explorer**, open the Student table designer by double-clicking the **Student** table.</span></span>

![마이그레이션 후에 SSOX 학생 테이블](complex-data-model/_static/ssox-after-migration.png)

<span data-ttu-id="59475-180">처음 두 마이그레이션을 적용 하기 전에 이름 열 형식 nvarchar (max) 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="59475-180">Before you applied the first two migrations, the name columns were of type nvarchar(MAX).</span></span> <span data-ttu-id="59475-181">Nvarchar (50)은 이제 및 열 이름을 FirstName로 FirstMidName에서 변경 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="59475-181">They are now nvarchar(50) and the column name has changed from FirstMidName to FirstName.</span></span>

> [!Note]
> <span data-ttu-id="59475-182">다음 섹션의 모든 엔터티 클래스를 만든 후 전에 컴파일 하려고 하면 컴파일러 오류가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="59475-182">If you try to compile before you finish creating all of the entity classes in the following sections, you might get compiler errors.</span></span>

## <a name="final-changes-to-the-student-entity"></a><span data-ttu-id="59475-183">마지막 변경 내용이 학생 엔터티에</span><span class="sxs-lookup"><span data-stu-id="59475-183">Final changes to the Student entity</span></span>

![학생 엔터티](complex-data-model/_static/student-entity.png)

<span data-ttu-id="59475-185">*Models/Student.cs*, 추가한 코드를 이전를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="59475-185">In *Models/Student.cs*, replace the code you added earlier with the following code.</span></span> <span data-ttu-id="59475-186">변경 내용은 강조 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="59475-186">The changes are highlighted.</span></span>

<span data-ttu-id="59475-187">[!code-csharp[Main](intro/samples/cu/Models/Student.cs?name=snippet_BeforeInheritance&highlight=11,13,15,18,22,24-31)]</span><span class="sxs-lookup"><span data-stu-id="59475-187">[!code-csharp[Main](intro/samples/cu/Models/Student.cs?name=snippet_BeforeInheritance&highlight=11,13,15,18,22,24-31)]</span></span>

### <a name="the-required-attribute"></a><span data-ttu-id="59475-188">필수 특성</span><span class="sxs-lookup"><span data-stu-id="59475-188">The Required attribute</span></span>

<span data-ttu-id="59475-189">`Required` 특성 필드에 필수 이름 속성을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="59475-189">The `Required` attribute makes the name properties required fields.</span></span> <span data-ttu-id="59475-190">`Required` nullable이 아닌 형식 예: 값 형식에 대 한 특성은 필요 하지 않습니다 (DateTime, int, 두 번, float 등.).</span><span class="sxs-lookup"><span data-stu-id="59475-190">The `Required` attribute is not needed for non-nullable types such as value types (DateTime, int, double, float, etc.).</span></span> <span data-ttu-id="59475-191">Null 일 수 없는 형식 필수 필드로 자동으로 처리 됩니다.</span><span class="sxs-lookup"><span data-stu-id="59475-191">Types that can't be null are automatically treated as required fields.</span></span>

<span data-ttu-id="59475-192">제거할 수는 `Required` 특성 및에 대 한 최소 길이 매개 변수 바꾸기는 `StringLength` 특성:</span><span class="sxs-lookup"><span data-stu-id="59475-192">You could remove the `Required` attribute and replace it with a minimum length parameter for the `StringLength` attribute:</span></span>

```csharp
[Display(Name = "Last Name")]
[StringLength(50, MinimumLength=1)]
public string LastName { get; set; }
```

### <a name="the-display-attribute"></a><span data-ttu-id="59475-193">표시 특성</span><span class="sxs-lookup"><span data-stu-id="59475-193">The Display attribute</span></span>

<span data-ttu-id="59475-194">`Display` 특성 지정 캡션 텍스트 상자에 대 한 각 인스턴스 (에 단어를 분할 하는 공간이 없습니다) "이름", "Last Name", "전체" 이름과 속성 이름 대신 "등록 날짜" 이어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="59475-194">The `Display` attribute specifies that the caption for the text boxes should be "First Name", "Last Name", "Full Name", and "Enrollment Date" instead of the property name in each instance (which has no space dividing the words).</span></span>

### <a name="the-fullname-calculated-property"></a><span data-ttu-id="59475-195">계산 FullName 속성</span><span class="sxs-lookup"><span data-stu-id="59475-195">The FullName calculated property</span></span>

<span data-ttu-id="59475-196">`FullName`다른 두 개의 속성을 연결 하 여 생성 하는 값을 반환 하는 계산 된 속성이입니다.</span><span class="sxs-lookup"><span data-stu-id="59475-196">`FullName` is a calculated property that returns a value that's created by concatenating two other properties.</span></span> <span data-ttu-id="59475-197">따라서은 get 접근자만 하 고 `FullName` 열이 데이터베이스에 생성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="59475-197">Therefore it has only a get accessor, and no `FullName` column will be generated in the database.</span></span>

## <a name="create-the-instructor-entity"></a><span data-ttu-id="59475-198">Instructor 엔터티 만들기</span><span class="sxs-lookup"><span data-stu-id="59475-198">Create the Instructor Entity</span></span>

![Instructor 엔터티](complex-data-model/_static/instructor-entity.png)

<span data-ttu-id="59475-200">만들 *Models/Instructor.cs*, 템플릿 코드를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="59475-200">Create *Models/Instructor.cs*, replacing the template code with the following code:</span></span>

<span data-ttu-id="59475-201">[!code-csharp[Main](intro/samples/cu/Models/Instructor.cs?name=snippet_BeforeInheritance)]</span><span class="sxs-lookup"><span data-stu-id="59475-201">[!code-csharp[Main](intro/samples/cu/Models/Instructor.cs?name=snippet_BeforeInheritance)]</span></span>

<span data-ttu-id="59475-202">여러 가지 속성 학생과 Instructor 엔터티에서 동일한 지를 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="59475-202">Notice that several properties are the same in the Student and Instructor entities.</span></span> <span data-ttu-id="59475-203">에 [상속 구현](inheritance.md) 이 시리즈의 뒷부분에 나오는 자습서에서는이 코드는 중복 제거를 리팩터링 합니다.</span><span class="sxs-lookup"><span data-stu-id="59475-203">In the [Implementing Inheritance](inheritance.md) tutorial later in this series, you'll refactor this code to eliminate the redundancy.</span></span>

<span data-ttu-id="59475-204">쓸 수 있도록 한 줄에 여러 특성을 삽입할 수는 `HireDate` 특성을 다음과 같이 합니다.</span><span class="sxs-lookup"><span data-stu-id="59475-204">You can put multiple attributes on one line, so you could also write the `HireDate` attributes as follows:</span></span>

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="the-courseassignments-and-officeassignment-navigation-properties"></a><span data-ttu-id="59475-205">CourseAssignments 및 OfficeAssignment 탐색 속성</span><span class="sxs-lookup"><span data-stu-id="59475-205">The CourseAssignments and OfficeAssignment navigation properties</span></span>

<span data-ttu-id="59475-206">`CourseAssignments` 및 `OfficeAssignment` 속성은 탐색 속성입니다.</span><span class="sxs-lookup"><span data-stu-id="59475-206">The `CourseAssignments` and `OfficeAssignment` properties are navigation properties.</span></span>

<span data-ttu-id="59475-207">강사 courses 개수에 관계 없이 배울 수 하므로 `CourseAssignments` 컬렉션으로 정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="59475-207">An instructor can teach any number of courses, so `CourseAssignments` is defined as a collection.</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="59475-208">탐색 속성에는 여러 엔터티 보유할 수, 하는 경우 해당 형식은 항목 추가, 삭제 및 업데이트 될 수 있는 목록 이어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="59475-208">If a navigation property can hold multiple entities, its type must be a list in which entries can be added, deleted, and updated.</span></span>  <span data-ttu-id="59475-209">지정할 수 있습니다 `ICollection<T>` 또는 형식으로 `List<T>` 또는 `HashSet<T>`합니다.</span><span class="sxs-lookup"><span data-stu-id="59475-209">You can specify `ICollection<T>` or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="59475-210">지정 하는 경우 `ICollection<T>`, EF 만듭니다는 `HashSet<T>` 기본적으로 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="59475-210">If you specify `ICollection<T>`, EF creates a `HashSet<T>` collection by default.</span></span>

<span data-ttu-id="59475-211">이유는 이러한 이유 `CourseAssignment` 엔터티 아래 다 대 다 관계에 대 한 섹션에서 설명 됩니다.</span><span class="sxs-lookup"><span data-stu-id="59475-211">The reason why these are `CourseAssignment` entities is explained below in the section about many-to-many relationships.</span></span>

<span data-ttu-id="59475-212">비즈니스 규칙에 Contoso 대학 따르면 강사 한 office 최대 하나만 사용할 수 있습니다 하므로 `OfficeAssignment` 속성 (있음 없는 사무실 할당 된 경우에 null 일 수 있습니다) OfficeAssignment 엔터티 하나에 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="59475-212">Contoso University business rules state that an instructor can only have at most one office, so the `OfficeAssignment` property holds a single OfficeAssignment entity (which may be null if no office is assigned).</span></span>

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="create-the-officeassignment-entity"></a><span data-ttu-id="59475-213">OfficeAssignment 엔터티 만들기</span><span class="sxs-lookup"><span data-stu-id="59475-213">Create the OfficeAssignment entity</span></span>

![OfficeAssignment 엔터티](complex-data-model/_static/officeassignment-entity.png)

<span data-ttu-id="59475-215">만들 *Models/OfficeAssignment.cs* 를 다음 코드로:</span><span class="sxs-lookup"><span data-stu-id="59475-215">Create *Models/OfficeAssignment.cs* with the following code:</span></span>

<span data-ttu-id="59475-216">[!code-csharp[Main](intro/samples/cu/Models/OfficeAssignment.cs)]</span><span class="sxs-lookup"><span data-stu-id="59475-216">[!code-csharp[Main](intro/samples/cu/Models/OfficeAssignment.cs)]</span></span>

### <a name="the-key-attribute"></a><span data-ttu-id="59475-217">키 특성</span><span class="sxs-lookup"><span data-stu-id="59475-217">The Key attribute</span></span>

<span data-ttu-id="59475-218">강사와 OfficeAssignment 엔터티 간의 0 또는 1에 한 관계가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="59475-218">There's a one-to-zero-or-one relationship  between the Instructor and the OfficeAssignment entities.</span></span> <span data-ttu-id="59475-219">사무실 할당만 할당 된 강사를 기준으로 존재 하며 따라서 해당 기본 키도 해당 외래 키 Instructor 엔터티를 합니다.</span><span class="sxs-lookup"><span data-stu-id="59475-219">An office assignment only exists in relation to the instructor it's assigned to, and therefore its primary key is also its foreign key to the Instructor entity.</span></span> <span data-ttu-id="59475-220">하지만 해당 이름을 ID 또는 classnameID 명명 규칙을 준수 하지 않는 때문에 Entity Framework이 엔터티의 기본 키로 자동으로 InstructorID 인식할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="59475-220">But the Entity Framework can't automatically recognize InstructorID as the primary key of this entity because its name doesn't follow the ID or classnameID naming convention.</span></span> <span data-ttu-id="59475-221">따라서는 `Key` 특성 키로 식별 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="59475-221">Therefore, the `Key` attribute is used to identify it as the key:</span></span>

```csharp
[Key]
public int InstructorID { get; set; }
```

<span data-ttu-id="59475-222">사용할 수도 있습니다는 `Key` 특성 엔터티의 기본 키가 있지만 classnameID 또는 ID가 아닌 다른 항목 속성의 이름을 지정 하는 경우</span><span class="sxs-lookup"><span data-stu-id="59475-222">You can also use the `Key` attribute if the entity does have its own primary key but you want to name the property something other than classnameID or ID.</span></span>

<span data-ttu-id="59475-223">기본적으로 EF 확인 관계에 대 한 열이 때문에 데이터베이스 생성 된으로 키를 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="59475-223">By default, EF treats the key as non-database-generated because the column is for an identifying relationship.</span></span>

### <a name="the-instructor-navigation-property"></a><span data-ttu-id="59475-224">강사 탐색 속성</span><span class="sxs-lookup"><span data-stu-id="59475-224">The Instructor navigation property</span></span>

<span data-ttu-id="59475-225">Instructor 엔터티는 null을 허용 `OfficeAssignment` 탐색 속성 (강사 사무실 할당 되지 않았을) 이므로, OfficeAssignment 엔터티에 비 nullable 및 `Instructor` 탐색 속성 (이므로 사무실 할당 수 없습니다 강사-없이 존재할 `InstructorID` 은 nullable이 아닌).</span><span class="sxs-lookup"><span data-stu-id="59475-225">The Instructor entity has a nullable `OfficeAssignment` navigation property (because an instructor might not have an office assignment), and the OfficeAssignment entity has a non-nullable `Instructor` navigation property (because an office assignment can't exist without an instructor -- `InstructorID` is non-nullable).</span></span> <span data-ttu-id="59475-226">Instructor 엔터티 관련 된 OfficeAssignment 엔터티, 하면 각 엔터티에 탐색 속성에 다른 하나에 대 한 참조를 갖게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="59475-226">When an Instructor entity has a related OfficeAssignment entity, each entity will have a reference to the other one in its navigation property.</span></span>

<span data-ttu-id="59475-227">빠뜨릴 수는 `[Required]` 관련된 강사 있어야 하지만 때문에 작업을 수행 하는 것이 없는 지정 하려면 강사 탐색 속성에 대해 특성의 `InstructorID` 외래 키 (이 테이블에 키 이기도 함)은 nullable이 아닌 합니다.</span><span class="sxs-lookup"><span data-stu-id="59475-227">You could put a `[Required]` attribute on the Instructor navigation property to specify that there must be a related instructor, but you don't have to do that because the `InstructorID` foreign key (which is also the key to this table) is non-nullable.</span></span>

## <a name="modify-the-course-entity"></a><span data-ttu-id="59475-228">과정 엔터티 수정</span><span class="sxs-lookup"><span data-stu-id="59475-228">Modify the Course Entity</span></span>

![과정 엔터티](complex-data-model/_static/course-entity.png)

<span data-ttu-id="59475-230">*Models/Course.cs*, 추가한 코드를 이전를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="59475-230">In *Models/Course.cs*, replace the code you added earlier with the following code.</span></span> <span data-ttu-id="59475-231">변경 내용은 강조 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="59475-231">The changes are highlighted.</span></span>

<span data-ttu-id="59475-232">[!code-csharp[Main](intro/samples/cu/Models/Course.cs?name=snippet_Final&highlight=2,10,13,16,19,21,23)]</span><span class="sxs-lookup"><span data-stu-id="59475-232">[!code-csharp[Main](intro/samples/cu/Models/Course.cs?name=snippet_Final&highlight=2,10,13,16,19,21,23)]</span></span>

<span data-ttu-id="59475-233">과정 엔터티에 포함 된 외래 키 속성이 `DepartmentID` 가 관련된 부서 엔터티를 가리키는 `Department` 탐색 속성입니다.</span><span class="sxs-lookup"><span data-stu-id="59475-233">The course entity has a foreign key property `DepartmentID` which points to the related Department entity and it has a `Department` navigation property.</span></span>

<span data-ttu-id="59475-234">Entity Framework 관련된 엔터티에 대 한 탐색 속성을 사용 하는 경우 데이터 모델에 외래 키 속성을 추가할 수 필요 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="59475-234">The Entity Framework doesn't require you to add a foreign key property to your data model when you have a navigation property for a related entity.</span></span>  <span data-ttu-id="59475-235">EF에서 자동으로 필요할 때마다 데이터베이스에 외래 키를 생성 하 고 만듭니다 [숨기](https://docs.microsoft.com/ef/core/modeling/shadow-properties) 하 합니다.</span><span class="sxs-lookup"><span data-stu-id="59475-235">EF automatically creates foreign keys in the database wherever they are needed and creates [shadow properties](https://docs.microsoft.com/ef/core/modeling/shadow-properties) for them.</span></span> <span data-ttu-id="59475-236">하지만 데이터 모델에 외래 키가 있는 업데이트를 수행할 수 쉽고 효율적으로.</span><span class="sxs-lookup"><span data-stu-id="59475-236">But having the foreign key in the data model can make updates simpler and more efficient.</span></span> <span data-ttu-id="59475-237">예를 들어, 편집 하는 과정 엔터티를 가져올 때 Department 엔터티는 null을 로드 하지 않는 경우 하므로 과정 엔터티를 업데이트할 때 해야 먼저 Department 엔터티를 인출 합니다.</span><span class="sxs-lookup"><span data-stu-id="59475-237">For example, when you fetch a course entity to edit, the  Department entity is null if you don't load it, so when you update the course entity, you would have to first fetch the Department entity.</span></span> <span data-ttu-id="59475-238">때 외래 키 속성 `DepartmentID` 포함 된 데이터 모델에서 업데이트 하기 전에 Department 엔터티를 가져올 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="59475-238">When the foreign key property `DepartmentID` is included in the data model, you don't need to fetch the Department entity before you update.</span></span>

### <a name="the-databasegenerated-attribute"></a><span data-ttu-id="59475-239">DatabaseGenerated 특성</span><span class="sxs-lookup"><span data-stu-id="59475-239">The DatabaseGenerated attribute</span></span>

<span data-ttu-id="59475-240">`DatabaseGenerated` 특성이 `None` 에 매개 변수는 `CourseID` 기본 키 값은 사용자가 제공 하지 않고 하는 데이터베이스에서 생성 된 속성을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="59475-240">The `DatabaseGenerated` attribute with the `None` parameter on the `CourseID` property specifies that primary key values are provided by the user rather than generated by the database.</span></span>

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

<span data-ttu-id="59475-241">기본적으로 Entity Framework에서는 기본 키 값이 데이터베이스에서 생성 된을 가정 합니다.</span><span class="sxs-lookup"><span data-stu-id="59475-241">By default, Entity Framework assumes that primary key values are generated by the database.</span></span> <span data-ttu-id="59475-242">즉, 대부분의 시나리오에서 원하는 합니다.</span><span class="sxs-lookup"><span data-stu-id="59475-242">That's what you want in most scenarios.</span></span> <span data-ttu-id="59475-243">그러나 과정 엔터티에 대 한 한 부서를 다른 부서에 대 한 2000 계열에 대 한 1000 계열과 같이 사용자가 지정한 과정 번호를 사용 하 등입니다.</span><span class="sxs-lookup"><span data-stu-id="59475-243">However, for Course entities, you'll use a user-specified course number such as a 1000 series for one department, a 2000 series for another department, and so on.</span></span>

<span data-ttu-id="59475-244">`DatabaseGenerated` 특성 날짜를 기록 하는 데 사용 되는 데이터베이스 열의 경우 행을 만들거나 업데이트할 때 기본값을 생성에 사용할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="59475-244">The `DatabaseGenerated` attribute can also be used to generate default values, as in the case of database columns used to record the date a row was created or updated.</span></span>  <span data-ttu-id="59475-245">자세한 내용은 참조 [속성 생성](https://docs.microsoft.com/ef/core/modeling/generated-properties)합니다.</span><span class="sxs-lookup"><span data-stu-id="59475-245">For more information, see [Generated Properties](https://docs.microsoft.com/ef/core/modeling/generated-properties).</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="59475-246">외래 키와 탐색 속성입니다.</span><span class="sxs-lookup"><span data-stu-id="59475-246">Foreign key and navigation properties</span></span>

<span data-ttu-id="59475-247">외래 키 속성 및 과정 엔터티의 탐색 속성은 다음과 같은 관계 반영 됩니다.</span><span class="sxs-lookup"><span data-stu-id="59475-247">The foreign key properties and navigation properties in the Course entity reflect the following relationships:</span></span>

<span data-ttu-id="59475-248">이므로 과정 한 부서에 할당 된는 `DepartmentID` 외래 키와 `Department` 위에서 언급 한 이유는 탐색 속성입니다.</span><span class="sxs-lookup"><span data-stu-id="59475-248">A course is assigned to one department, so there's a `DepartmentID` foreign key and a `Department` navigation property for the reasons mentioned above.</span></span>

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

<span data-ttu-id="59475-249">과정을 학생을 등록 여러 개가 있을 수 있으므로 `Enrollments` 탐색 속성은 컬렉션:</span><span class="sxs-lookup"><span data-stu-id="59475-249">A course can have any number of students enrolled in it, so the `Enrollments` navigation property is a collection:</span></span>

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

<span data-ttu-id="59475-250">여러 강사 하 여 과정을 배울 수 있습니다 하므로 `CourseAssignments` 탐색 속성은 컬렉션 (유형을 `CourseAssignment` 설명 [나중](#many-to-many-relationships)):</span><span class="sxs-lookup"><span data-stu-id="59475-250">A course may be taught by multiple instructors, so the `CourseAssignments` navigation property is a collection (the type `CourseAssignment` is explained [later](#many-to-many-relationships)):</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

## <a name="create-the-department-entity"></a><span data-ttu-id="59475-251">부서 엔터티 만들기</span><span class="sxs-lookup"><span data-stu-id="59475-251">Create the Department entity</span></span>

![Department 엔터티](complex-data-model/_static/department-entity.png)


<span data-ttu-id="59475-253">만들 *Models/Department.cs* 를 다음 코드로:</span><span class="sxs-lookup"><span data-stu-id="59475-253">Create *Models/Department.cs* with the following code:</span></span>

<span data-ttu-id="59475-254">[!code-csharp[Main](intro/samples/cu/Models/Department.cs?name=snippet_Begin)]</span><span class="sxs-lookup"><span data-stu-id="59475-254">[!code-csharp[Main](intro/samples/cu/Models/Department.cs?name=snippet_Begin)]</span></span>

### <a name="the-column-attribute"></a><span data-ttu-id="59475-255">Column 특성</span><span class="sxs-lookup"><span data-stu-id="59475-255">The Column attribute</span></span>

<span data-ttu-id="59475-256">사용 하는 이전 버전의 `Column` 열 이름 매핑을 변경할 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="59475-256">Earlier you used the `Column` attribute to change column name mapping.</span></span> <span data-ttu-id="59475-257">부서 엔터티에 대 한 코드는 `Column` 특성은 SQL 데이터 형식 매핑 변경 데이터베이스의 SQL Server money 형식을 사용 하 여 열 정의할 수 있도록 하는 데 사용 되 고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="59475-257">In the code for the Department entity, the `Column` attribute is being used to change SQL data type mapping so that the column will be defined using the SQL Server money type in the database:</span></span>

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

<span data-ttu-id="59475-258">열 매핑 일반적으로 필요 하지 않은, Entity Framework 속성에 대해 정의 하는 CLR 형식에 따라 적절 한 SQL Server 데이터 형식을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="59475-258">Column mapping is generally not required, because the Entity Framework chooses the appropriate SQL Server data type based on the CLR type that you define for the property.</span></span> <span data-ttu-id="59475-259">CLR `decimal` SQL Server에 매핑됩니다 `decimal` 유형입니다.</span><span class="sxs-lookup"><span data-stu-id="59475-259">The CLR `decimal` type maps to a SQL Server `decimal` type.</span></span> <span data-ttu-id="59475-260">몰라도 경우 열은 유지 하는 통화 금액 및 money 데이터 형식이 더 적합 하 한입니다.</span><span class="sxs-lookup"><span data-stu-id="59475-260">But in this case you know that the column will be holding currency amounts, and the money data type is more appropriate for that.</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="59475-261">외래 키와 탐색 속성입니다.</span><span class="sxs-lookup"><span data-stu-id="59475-261">Foreign key and navigation properties</span></span>

<span data-ttu-id="59475-262">외래 키와 탐색 속성은 다음과 같은 관계 반영 됩니다.</span><span class="sxs-lookup"><span data-stu-id="59475-262">The foreign key and navigation properties reflect the following relationships:</span></span>

<span data-ttu-id="59475-263">부서 수도, 관리자가 없을 수 있습니다 및 관리자는 항상 강사 합니다.</span><span class="sxs-lookup"><span data-stu-id="59475-263">A department may or may not have an administrator, and an administrator is always an instructor.</span></span> <span data-ttu-id="59475-264">따라서는 `InstructorID` 속성 강사 엔터티에 외래 키로 포함 되며 매개 변수 뒤에 추가 됩니다는 `int` 지정 null 허용으로 속성을 표시 하려면를 입력 합니다.</span><span class="sxs-lookup"><span data-stu-id="59475-264">Therefore the `InstructorID` property is included as the foreign key to the Instructor entity, and a question mark is added after the `int` type designation to mark the property as nullable.</span></span> <span data-ttu-id="59475-265">탐색 속성이 명명 `Administrator` Instructor 엔터티를 보유 하지만:</span><span class="sxs-lookup"><span data-stu-id="59475-265">The navigation property is named `Administrator` but holds an Instructor entity:</span></span>

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

<span data-ttu-id="59475-266">부서는 Courses 탐색 속성 이므로 많은 과목을 포함할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="59475-266">A department may have many courses, so there's a Courses navigation property:</span></span>

```csharp
public ICollection<Course> Courses { get; set; }
```

> [!NOTE]
> <span data-ttu-id="59475-267">규칙에 따라 Entity Framework에는 null을 허용 하지는 외래 키 및 다 대 다 관계에 대 한 하위 삭제를 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="59475-267">By convention, the Entity Framework enables cascade delete for non-nullable foreign keys and for many-to-many relationships.</span></span> <span data-ttu-id="59475-268">이 마이그레이션을 추가 하려고 하면 예외가 발생 하는 순환 cascade delete 규칙을 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="59475-268">This can result in circular cascade delete rules, which will cause an exception when you try to add a migration.</span></span> <span data-ttu-id="59475-269">예를 들어, null 허용으로 Department.InstructorID 속성을 정의 하지 않은 경우 EF 부서, 그렇지 않은 사용자를 삭제 하면 강의 삭제 하려면 cascade delete 규칙을 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="59475-269">For example, if you didn't define the Department.InstructorID property as nullable, EF would configure a cascade delete rule to delete the instructor when you delete the department, which is not what you want to have happen.</span></span> <span data-ttu-id="59475-270">비즈니스 규칙에 필요한 경우는 `InstructorID` 비 nullable 속성 관계에 cascade delete를 사용 하지 않도록 설정 하려면 다음 fluent API 문을 사용 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="59475-270">If your business rules required the `InstructorID` property to be non-nullable, you would have to use the following fluent API statement to disable cascade delete on the relationship:</span></span>
> ```csharp
> modelBuilder.Entity<Department>()
>    .HasOne(d => d.Administrator)
>    .WithMany()
>    .OnDelete(DeleteBehavior.Restrict)
> ```

## <a name="modify-the-enrollment-entity"></a><span data-ttu-id="59475-271">등록 엔터티 수정</span><span class="sxs-lookup"><span data-stu-id="59475-271">Modify the Enrollment entity</span></span>

![등록 엔터티](complex-data-model/_static/enrollment-entity.png)

<span data-ttu-id="59475-273">*Models/Enrollment.cs*, 추가한 코드를 이전를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="59475-273">In *Models/Enrollment.cs*, replace the code you added earlier with the following code:</span></span>

<span data-ttu-id="59475-274">[!code-csharp[Main](intro/samples/cu/Models/Enrollment.cs?name=snippet_Final&highlight=1-2,16)]</span><span class="sxs-lookup"><span data-stu-id="59475-274">[!code-csharp[Main](intro/samples/cu/Models/Enrollment.cs?name=snippet_Final&highlight=1-2,16)]</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="59475-275">외래 키와 탐색 속성입니다.</span><span class="sxs-lookup"><span data-stu-id="59475-275">Foreign key and navigation properties</span></span>

<span data-ttu-id="59475-276">외래 키 속성 및 탐색 속성은 다음과 같은 관계 반영 됩니다.</span><span class="sxs-lookup"><span data-stu-id="59475-276">The foreign key properties and navigation properties reflect the following relationships:</span></span>

<span data-ttu-id="59475-277">이므로 단일 과정에 대 한 등록 레코드는 한 `CourseID` 외래 키 속성 및 `Course` 탐색 속성:</span><span class="sxs-lookup"><span data-stu-id="59475-277">An enrollment record is for a single course, so there's a `CourseID` foreign key property and a `Course` navigation property:</span></span>

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

<span data-ttu-id="59475-278">이므로 단일 학생에 대 한 등록 레코드는 한 `StudentID` 외래 키 속성 및 `Student` 탐색 속성:</span><span class="sxs-lookup"><span data-stu-id="59475-278">An enrollment record is for a single student, so there's a `StudentID` foreign key property and a `Student` navigation property:</span></span>

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a><span data-ttu-id="59475-279">다 대 다 관계</span><span class="sxs-lookup"><span data-stu-id="59475-279">Many-to-Many Relationships</span></span>

<span data-ttu-id="59475-280">학생과 과정 엔터티 간의 다 대 다 관계가 있는 및 다 대 다 조인 표 형식으로 등록 엔터티 함수의 *페이로드를 사용한* 데이터베이스에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="59475-280">There's a many-to-many relationship between the Student and Course entities, and the Enrollment entity functions as a many-to-many join table *with payload* in the database.</span></span> <span data-ttu-id="59475-281">페이로드"사용" (이 경우, 기본 키 및 등급 속성)에 조인 된 테이블에 대 한 외래 키 외에도 추가 데이터 Enrollment 테이블에 포함 되어 있음을 의미 합니다.</span><span class="sxs-lookup"><span data-stu-id="59475-281">"With payload" means that the Enrollment table contains additional data besides foreign keys for the joined tables (in this case, a primary key and a Grade property).</span></span>

<span data-ttu-id="59475-282">다음 그림은 엔터티 다이어그램에서 이러한 관계 모양을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="59475-282">The following illustration shows what these relationships look like in an entity diagram.</span></span> <span data-ttu-id="59475-283">(이 다이어그램 EF에 대 한 Entity Framework 파워 도구를 사용 하 여 생성 된 6.x; 자습서의 일부가 아닌는 다이어그램을 만들어만 사용 되 고로 보여 줍니다.)</span><span class="sxs-lookup"><span data-stu-id="59475-283">(This diagram was generated using the Entity Framework Power Tools for EF 6.x; creating the diagram isn't part of the tutorial, it's just being used here as an illustration.)</span></span>

![학생 과정 다 대 다 관계](complex-data-model/_static/student-course.png)

<span data-ttu-id="59475-285">각 관계 한쪽 끝에 별표 (*)부터 1에서 형식이 다른 경우 일 대 다 관계를 나타내는입니다.</span><span class="sxs-lookup"><span data-stu-id="59475-285">Each relationship line has a 1 at one end and an asterisk (*) at the other, indicating a one-to-many relationship.</span></span>

<span data-ttu-id="59475-286">Enrollment 테이블 등급 정보를 포함 하지 않은 경우 CourseID 및 StudentID 두 개의 외래 키를 포함 해야만 합니다.</span><span class="sxs-lookup"><span data-stu-id="59475-286">If the Enrollment table didn't include grade information, it would only need to contain the two foreign keys CourseID and StudentID.</span></span> <span data-ttu-id="59475-287">이 경우 것 처럼 페이로드 없이 다 대 다 조인 테이블 (또는 순수 조인 테이블)는 데이터베이스입니다.</span><span class="sxs-lookup"><span data-stu-id="59475-287">In that case, it would be a many-to-many join table without payload (or a pure join table) in the database.</span></span> <span data-ttu-id="59475-288">강사 및 과정 엔터티 종류 다 대 다 관계의 있고 페이로드 없이 조인 테이블으로 기능 하도록 엔터티 클래스를 만들려면 다음 단계는 합니다.</span><span class="sxs-lookup"><span data-stu-id="59475-288">The Instructor and Course entities have that kind of many-to-many relationship, and your next step is to create an entity class to function as a join table without payload.</span></span>

<span data-ttu-id="59475-289">(EF 6.x 지원 다 대 다 관계가 되지만 EF 코어에 대 한 암시적 조인 테이블은 그렇지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="59475-289">(EF 6.x supports implicit join tables for many-to-many relationships, but EF Core does not.</span></span> <span data-ttu-id="59475-290">자세한 내용은 참조는 [EF 코어 GitHub 리포지토리에 토론](https://github.com/aspnet/EntityFramework/issues/1368).)</span><span class="sxs-lookup"><span data-stu-id="59475-290">For more information, see the [discussion in the EF Core GitHub repository](https://github.com/aspnet/EntityFramework/issues/1368).)</span></span> 

## <a name="the-courseassignment-entity"></a><span data-ttu-id="59475-291">CourseAssignment 엔터티</span><span class="sxs-lookup"><span data-stu-id="59475-291">The CourseAssignment entity</span></span>

![CourseAssignment 엔터티](complex-data-model/_static/courseassignment-entity.png)

<span data-ttu-id="59475-293">만들 *Models/CourseAssignment.cs* 를 다음 코드로:</span><span class="sxs-lookup"><span data-stu-id="59475-293">Create *Models/CourseAssignment.cs* with the following code:</span></span>

<span data-ttu-id="59475-294">[!code-csharp[Main](intro/samples/cu/Models/CourseAssignment.cs)]</span><span class="sxs-lookup"><span data-stu-id="59475-294">[!code-csharp[Main](intro/samples/cu/Models/CourseAssignment.cs)]</span></span>

### <a name="join-entity-names"></a><span data-ttu-id="59475-295">엔터티 이름 가입</span><span class="sxs-lookup"><span data-stu-id="59475-295">Join entity names</span></span>

<span data-ttu-id="59475-296">조인 테이블 강사 교육 과정 다 대 다 관계에 대 한 데이터베이스에 필요 하며 엔터티 집합으로 표현할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="59475-296">A join table is required in the database for the Instructor-to-Courses many-to-many relationship, and it has to be represented by an entity set.</span></span> <span data-ttu-id="59475-297">일반적으로 조인 엔터티 이름을 `EntityName1EntityName2`는 경우 그렇게 할 수 `CourseInstructor`합니다.</span><span class="sxs-lookup"><span data-stu-id="59475-297">It's common to name a join entity `EntityName1EntityName2`, which in this case would be `CourseInstructor`.</span></span> <span data-ttu-id="59475-298">그러나 관계를 설명 하는 이름을 선택 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="59475-298">However, we recommend that you choose a name that describes the relationship.</span></span> <span data-ttu-id="59475-299">데이터 모델은 단순을 시작 및 자주 가져오는 페이로드 아니요 페이로드 조인을 사용 하 여 증가 합니다.</span><span class="sxs-lookup"><span data-stu-id="59475-299">Data models start out simple and grow, with no-payload joins frequently getting payloads later.</span></span> <span data-ttu-id="59475-300">설명이 포함 된 엔터티 이름으로 시작 하는 경우 나중에 이름을 변경할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="59475-300">If you start with a descriptive entity name, you won't have to change the name later.</span></span> <span data-ttu-id="59475-301">것이 가장 좋습니다, 조인 엔터티 자체 자연 (가능한 한 단어) 이름을 비즈니스 도메인에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="59475-301">Ideally, the join entity would have its own natural (possibly single word) name in the business domain.</span></span> <span data-ttu-id="59475-302">예를 들어 설명서 및 고객 등급을 통해 연결할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="59475-302">For example, Books and Customers could be linked through Ratings.</span></span> <span data-ttu-id="59475-303">이 관계에 대해 `CourseAssignment` 하는 것이 보다 `CourseInstructor`합니다.</span><span class="sxs-lookup"><span data-stu-id="59475-303">For this relationship, `CourseAssignment` is a better choice than `CourseInstructor`.</span></span>

### <a name="composite-key"></a><span data-ttu-id="59475-304">복합 키</span><span class="sxs-lookup"><span data-stu-id="59475-304">Composite key</span></span>

<span data-ttu-id="59475-305">외래 키가 null을 허용 하며 고유 하 게 테이블의 각 행을 식별, 별도 기본 키에 대 한 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="59475-305">Since the foreign keys are not nullable and together uniquely identify each row of the table, there is no need for a separate primary key.</span></span> <span data-ttu-id="59475-306">*InstructorID* 및 *CourseID* 속성은 복합 기본 키로 작동 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="59475-306">The *InstructorID* and *CourseID* properties should function as a composite primary key.</span></span> <span data-ttu-id="59475-307">사용 하 여는 EF에 복합 기본 키를 식별 하는 유일한 방법은 *fluent API* (것으로 수행할 수 없는 특성을 사용 하 여).</span><span class="sxs-lookup"><span data-stu-id="59475-307">The only way to identify composite primary keys to EF is by using the *fluent API* (it can't be done by using attributes).</span></span> <span data-ttu-id="59475-308">다음 섹션에 복합 기본 키를 구성 하는 방법을 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="59475-308">You'll see how to configure the composite primary key in the next section.</span></span>

<span data-ttu-id="59475-309">복합 키는 한 과정 및 하나의 강사에 대 한 여러 행에 대 한 여러 행 있을 수 있지만, 동일한 강사 및 과정에 대 한 여러 행 사용할 수 없습니다 것을 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="59475-309">The composite key ensures that while you can have multiple rows for one course, and multiple rows for one instructor, you can't have multiple rows for the same instructor and course.</span></span> <span data-ttu-id="59475-310">`Enrollment` 조인 엔터티는 이러한 종류의 중복이 가능 하므로 고유한 기본 키를 정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="59475-310">The `Enrollment` join entity defines its own primary key, so duplicates of this sort are possible.</span></span> <span data-ttu-id="59475-311">이러한 중복을 방지 하려면 외래 키 필드에 고유 인덱스를 추가 하거나 구성할 수 있습니다 `Enrollment` 비슷합니다 복합 기본 키가 있는 `CourseAssignment`합니다.</span><span class="sxs-lookup"><span data-stu-id="59475-311">To prevent such duplicates, you could add a unique index on the foreign key fields, or configure `Enrollment` with a primary composite key similar to `CourseAssignment`.</span></span> <span data-ttu-id="59475-312">자세한 내용은 참조 [인덱스](https://docs.microsoft.com/ef/core/modeling/indexes)합니다.</span><span class="sxs-lookup"><span data-stu-id="59475-312">For more information, see [Indexes](https://docs.microsoft.com/ef/core/modeling/indexes).</span></span>

## <a name="update-the-database-context"></a><span data-ttu-id="59475-313">데이터베이스 컨텍스트를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="59475-313">Update the database context</span></span>

<span data-ttu-id="59475-314">다음 강조 표시 된 코드를 추가 하는 *Data/SchoolContext.cs* 파일:</span><span class="sxs-lookup"><span data-stu-id="59475-314">Add the following highlighted code to the *Data/SchoolContext.cs* file:</span></span>

<span data-ttu-id="59475-315">[!code-csharp[Main](intro/samples/cu/Data/SchoolContext.cs?name=snippet_BeforeInheritance&highlight=15-18,25-31)]</span><span class="sxs-lookup"><span data-stu-id="59475-315">[!code-csharp[Main](intro/samples/cu/Data/SchoolContext.cs?name=snippet_BeforeInheritance&highlight=15-18,25-31)]</span></span>

<span data-ttu-id="59475-316">이 코드는 새 엔터티를 추가 하 고 CourseAssignment 엔터티의 복합 기본 키를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="59475-316">This code adds the new entities and configures the CourseAssignment entity's composite primary key.</span></span>

## <a name="fluent-api-alternative-to-attributes"></a><span data-ttu-id="59475-317">특성에 대 한 fluent API 대안</span><span class="sxs-lookup"><span data-stu-id="59475-317">Fluent API alternative to attributes</span></span>

<span data-ttu-id="59475-318">코드는 `OnModelCreating` 의 메서드는 `DbContext` 클래스에서 사용 하는 *fluent API* EF 동작을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="59475-318">The code in the `OnModelCreating` method of the `DbContext` class uses the *fluent API* to configure EF behavior.</span></span> <span data-ttu-id="59475-319">일련의 함께 메서드 호출에서이 예제에서와 같이 단일 문으로 가설에서 많이 사용 되므로 API "fluent" 이라고는 [EF 핵심 설명서](https://docs.microsoft.com/ef/core/modeling/#methods-of-configuration):</span><span class="sxs-lookup"><span data-stu-id="59475-319">The API is called "fluent" because it's often used by stringing a series of method calls together into a single statement, as in this example from the [EF Core documentation](https://docs.microsoft.com/ef/core/modeling/#methods-of-configuration):</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

<span data-ttu-id="59475-320">이 자습서에서는 특성으로 작업할 수 없는 데이터베이스 매핑을에 fluent API를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="59475-320">In this tutorial, you're using the fluent API only for database mapping that you can't do with attributes.</span></span> <span data-ttu-id="59475-321">그러나 대부분의 서식, 유효성 검사 및 특성을 사용 하 여 수행할 수 있습니다는 매핑 규칙을 지정 하려면 fluent API를 사용할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="59475-321">However, you can also use the fluent API to specify most of the formatting, validation, and mapping rules that you can do by using attributes.</span></span> <span data-ttu-id="59475-322">와 같은 일부 특성 `MinimumLength` fluent API를 통해 적용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="59475-322">Some attributes such as `MinimumLength` can't be applied with the fluent API.</span></span> <span data-ttu-id="59475-323">앞에서 설명한 대로 `MinimumLength` 는 스키마를 변경 하지 않는 클라이언트와 서버 쪽 유효성 검사 규칙에만 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="59475-323">As mentioned previously, `MinimumLength` doesn't change the schema, it only applies a client and server side validation rule.</span></span>

<span data-ttu-id="59475-324">일부 개발자가 단독으로 보관할 수 있는 엔터티 클래스 "정리 합니다." fluent API를 사용 하는 것을 선호합니다</span><span class="sxs-lookup"><span data-stu-id="59475-324">Some developers prefer to use the fluent API exclusively so that they can keep their entity classes "clean."</span></span> <span data-ttu-id="59475-325">원하는 되어만 fluent API를 사용 하 여 수행할 수 있는 몇 가지 사용자 지정 특성 및 fluent API를 함께 사용할 수 있습니다 하지만 일반적 방법이 권장된이 두 가지 방법 중 하나를 선택 하 고 가능한 한를 일관 되 게 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="59475-325">You can mix attributes and fluent API if you want, and there are a few customizations that can only be done by using fluent API, but in general the recommended practice is to choose one of these two approaches and use that consistently as much as possible.</span></span> <span data-ttu-id="59475-326">둘 다 사용 않으면, note 충돌이 있을 때마다 Fluent API 재정의 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="59475-326">If you do use both, note that wherever there is a conflict, Fluent API overrides attributes.</span></span>

<span data-ttu-id="59475-327">Fluent API와 비교 하는 특성에 대 한 자세한 내용은 참조 [구성 방법이](https://docs.microsoft.com/ef/core/modeling/#methods-of-configuration)합니다.</span><span class="sxs-lookup"><span data-stu-id="59475-327">For more information about attributes vs. fluent API, see [Methods of configuration](https://docs.microsoft.com/ef/core/modeling/#methods-of-configuration).</span></span>

## <a name="entity-diagram-showing-relationships"></a><span data-ttu-id="59475-328">관계를 보여 주는 엔터티 다이어그램</span><span class="sxs-lookup"><span data-stu-id="59475-328">Entity Diagram Showing Relationships</span></span>

<span data-ttu-id="59475-329">다음은 Entity Framework 파워 도구 만들기 완료 된 School 모델 다이어그램입니다.</span><span class="sxs-lookup"><span data-stu-id="59475-329">The following illustration shows the diagram that the Entity Framework Power Tools create for the completed School model.</span></span>

![엔터티 다이어그램](complex-data-model/_static/diagram.png)

<span data-ttu-id="59475-331">일 대 다 관계 선을 외에도 (1 ~ \*), 여기에서 볼 수 강사 및 OfficeAssignment 엔터티 및 0-또는---일대다 관계 선을 사이 0 또는 1을 한 관계 선을 (1를 0..1) (0..1 대 *) 사이 강사 및 부서 엔터티입니다.</span><span class="sxs-lookup"><span data-stu-id="59475-331">Besides the one-to-many relationship lines (1 to \*), you can see here the one-to-zero-or-one relationship line (1 to 0..1) between the Instructor and OfficeAssignment entities and the zero-or-one-to-many relationship line (0..1 to *) between the Instructor and Department entities.</span></span>

## <a name="seed-the-database-with-test-data"></a><span data-ttu-id="59475-332">테스트 데이터로 데이터베이스를 시드하십시오</span><span class="sxs-lookup"><span data-stu-id="59475-332">Seed the Database with Test Data</span></span>

<span data-ttu-id="59475-333">코드는 *Data/DbInitializer.cs* 만든 새 엔터티에 대 한 데이터를 제공 하기 위해 다음 코드가 포함 된 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="59475-333">Replace the code in the *Data/DbInitializer.cs* file with the following code in order to provide seed data for the new entities you've created.</span></span>

<span data-ttu-id="59475-334">[!code-csharp[Main](intro/samples/cu/Data/DbInitializer.cs?name=snippet_Final)]</span><span class="sxs-lookup"><span data-stu-id="59475-334">[!code-csharp[Main](intro/samples/cu/Data/DbInitializer.cs?name=snippet_Final)]</span></span>

<span data-ttu-id="59475-335">첫 번째 자습서에서 볼 수 있듯이이 코드의 대부분을 단순히 새 엔터티 개체 만들고 테스트를 위해 필요에 따라 속성에 샘플 데이터를 로드 합니다.</span><span class="sxs-lookup"><span data-stu-id="59475-335">As you saw in the first tutorial, most of this code simply creates new entity objects and loads sample data into properties as required for testing.</span></span> <span data-ttu-id="59475-336">다 대 다 관계의 처리 방법을 확인: 코드에서 엔터티를 만들어 관계를 만드는 `Enrollments` 및 `CourseAssignment` 엔터티 집합을 조인 합니다.</span><span class="sxs-lookup"><span data-stu-id="59475-336">Notice how the many-to-many relationships are handled: the code creates relationships by creating entities in the `Enrollments` and `CourseAssignment` join entity sets.</span></span>

## <a name="add-a-migration"></a><span data-ttu-id="59475-337">추가 마이그레이션</span><span class="sxs-lookup"><span data-stu-id="59475-337">Add a migration</span></span>

<span data-ttu-id="59475-338">변경 내용을 저장 하 고 프로젝트를 빌드하십시오.</span><span class="sxs-lookup"><span data-stu-id="59475-338">Save your changes and build the project.</span></span> <span data-ttu-id="59475-339">그런 다음 프로젝트 폴더에 명령 창을 열고 다음을 입력에서 `migrations add` 명령 (실행 하지 마십시오. update-database 명령을 아직):</span><span class="sxs-lookup"><span data-stu-id="59475-339">Then open the command window in the project folder and enter the `migrations add` command (don't do the update-database command yet):</span></span>

```console
dotnet ef migrations add ComplexDataModel
```

<span data-ttu-id="59475-340">가능한 데이터 손실에 대 한 경고가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="59475-340">You get a warning about possible data loss.</span></span>

```text
An operation was scaffolded that may result in the loss of data. Please review the migration for accuracy.
Done. To undo this action, use 'ef migrations remove'
```

<span data-ttu-id="59475-341">실행 하 려 한 경우는 `database update` 이 시점에서 명령 (마 아직), 다음 오류가 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="59475-341">If you tried to run the `database update` command at this point (don't do it yet), you would get the following error:</span></span>

> <span data-ttu-id="59475-342">ALTER TABLE 문을 충돌 FOREIGN KEY 제약 조건 "FK_dbo 합니다. Course_dbo 합니다. Department_DepartmentID "입니다.</span><span class="sxs-lookup"><span data-stu-id="59475-342">The ALTER TABLE statement conflicted with the FOREIGN KEY constraint "FK_dbo.Course_dbo.Department_DepartmentID".</span></span> <span data-ttu-id="59475-343">데이터베이스 "ContosoUniversity" 테이블 "dbo에에서 충돌이 발생 했습니다. 부서 ", 열 'DepartmentID'입니다.</span><span class="sxs-lookup"><span data-stu-id="59475-343">The conflict occurred in database "ContosoUniversity", table "dbo.Department", column 'DepartmentID'.</span></span>

<span data-ttu-id="59475-344">기존 데이터와 마이그레이션을 실행 하는 경우에 경우에 따라 foreign key 제약 조건을 만족 하려면 데이터베이스에 스텁 데이터를 삽입 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="59475-344">Sometimes when you execute migrations with existing data, you need to insert stub data into the database to satisfy foreign key constraints.</span></span> <span data-ttu-id="59475-345">생성 된 코드는 `Up` 메서드 Course 테이블에 null을 허용 하지 DepartmentID 외래 키를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="59475-345">The generated code in the `Up` method adds a non-nullable DepartmentID foreign key to the Course table.</span></span> <span data-ttu-id="59475-346">이미 있으면 행은 Course 테이블에는 코드를 실행 하는 경우는 `AddColumn` SQL Server null 일 수 없는 열에 저장 하는 값을 인식 하지 때문에 작업이 실패 합니다.</span><span class="sxs-lookup"><span data-stu-id="59475-346">If there are already rows in the Course table when the code runs, the `AddColumn` operation fails because SQL Server doesn't know what value to put in the column that can't be null.</span></span> <span data-ttu-id="59475-347">이 자습서에 대 한 실행 마이그레이션 새 데이터베이스에 있지만 프로덕션 응용 프로그램에서 다음과 같이 작업을 수행 하는 방법의 예를 보여 줍니다. 기존 데이터를 처리할 마이그레이션 수행 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="59475-347">For this tutorial you'll run the migration on a new database, but in a production application you'd have to make the migration handle existing data, so the following directions show an example of how to do that.</span></span>

<span data-ttu-id="59475-348">이 마이그레이션 작업 기존 데이터를 새 열에 기본값을 제공 하는 코드를 변경 해야 하 고 스텁 부서를 만들 수 있도록 기본 부서에서 역할을 "Temp" 라는 합니다.</span><span class="sxs-lookup"><span data-stu-id="59475-348">To make this migration work with existing data you have to change the code to give the new column a default value, and create a stub department named "Temp" to act as the default department.</span></span> <span data-ttu-id="59475-349">결과적으로, 기존 과정 행 모두에 연결 됩니다 후 "Temp" 부서는 `Up` 메서드를 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="59475-349">As a result, existing Course rows will all be related to the "Temp" department after the `Up` method runs.</span></span>

* <span data-ttu-id="59475-350">열기는 *{timestamp}_ComplexDataModel.cs* 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="59475-350">Open the *{timestamp}_ComplexDataModel.cs* file.</span></span> 

* <span data-ttu-id="59475-351">줄을 주석으로 DepartmentID 열 Course 테이블을 추가 하는 코드입니다.</span><span class="sxs-lookup"><span data-stu-id="59475-351">Comment out the line of code that adds the DepartmentID column to the Course table.</span></span>

  <span data-ttu-id="59475-352">[!code-csharp[Main](intro/samples/cu/Migrations/20170215234014_ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]</span><span class="sxs-lookup"><span data-stu-id="59475-352">[!code-csharp[Main](intro/samples/cu/Migrations/20170215234014_ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]</span></span>

* <span data-ttu-id="59475-353">Department 테이블을 만드는 코드 뒤 다음 강조 표시 된 코드를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="59475-353">Add the following highlighted code after the code that creates the Department table:</span></span>

  <span data-ttu-id="59475-354">[!code-csharp[Main](intro/samples/cu/Migrations/20170215234014_ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=22-32)]</span><span class="sxs-lookup"><span data-stu-id="59475-354">[!code-csharp[Main](intro/samples/cu/Migrations/20170215234014_ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=22-32)]</span></span>

<span data-ttu-id="59475-355">프로덕션 응용 프로그램에서 코드 또는 부서 행을 추가 하 고 새 부서 행 과정 행과 연관 스크립트를 작성할 것 있습니다.</span><span class="sxs-lookup"><span data-stu-id="59475-355">In a production application, you would write code or scripts to add Department rows and relate Course rows to the new Department rows.</span></span> <span data-ttu-id="59475-356">"Temp" 부서 또는 Course.DepartmentID 열에 기본값을 다음 더 이상 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="59475-356">You would then no longer need the "Temp" department or the default value on the Course.DepartmentID column.</span></span>

<span data-ttu-id="59475-357">변경 내용을 저장 하 고 프로젝트를 빌드하십시오.</span><span class="sxs-lookup"><span data-stu-id="59475-357">Save your changes and build the project.</span></span>

## <a name="change-the-connection-string-and-update-the-database"></a><span data-ttu-id="59475-358">연결 문자열을 변경 하 고 데이터베이스를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="59475-358">Change the connection string and update the database</span></span>

<span data-ttu-id="59475-359">이제 있는 새 코드는 `DbInitializer` 빈 데이터베이스를 새 엔터티에 대 한 데이터를 추가 하는 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="59475-359">You now have new code in the `DbInitializer` class that adds seed data for the new entities to an empty database.</span></span> <span data-ttu-id="59475-360">EF 비어 있는 새 데이터베이스 만들기, 변경의 연결 문자열에 데이터베이스의 이름을 *appsettings.json* ContosoUniversity3 또는 다른 이름으로 사용 중인 컴퓨터에서 사용 하지 않은 합니다.</span><span class="sxs-lookup"><span data-stu-id="59475-360">To make EF create a new empty database, change the name of the database in the connection string in *appsettings.json* to ContosoUniversity3 or some other name that you haven't used on the computer you're using.</span></span>

```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=(localdb)\\mssqllocaldb;Database=ContosoUniversity3;Trusted_Connection=True;MultipleActiveResultSets=true"
  },
```

<span data-ttu-id="59475-361">변경 내용을 저장 *appsettings.json*합니다.</span><span class="sxs-lookup"><span data-stu-id="59475-361">Save your change to *appsettings.json*.</span></span>

> [!NOTE]
> <span data-ttu-id="59475-362">데이터베이스 이름을 변경 하는 대신, 데이터베이스를 삭제할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="59475-362">As an alternative to changing the database name, you can delete the database.</span></span> <span data-ttu-id="59475-363">사용 하 여 **SQL Server 개체 탐색기** (SSOX) 또는 `database drop` CLI 명령을:</span><span class="sxs-lookup"><span data-stu-id="59475-363">Use **SQL Server Object Explorer** (SSOX) or the `database drop` CLI command:</span></span>
> ```console
> dotnet ef database drop
> ```

<span data-ttu-id="59475-364">데이터베이스 이름을 변경 했거나 데이터베이스를 삭제 한 후 실행 하는 `database update` 는 마이그레이션 실행 하려면 명령 창에서 명령을 합니다.</span><span class="sxs-lookup"><span data-stu-id="59475-364">After you have changed the database name or deleted the database, run the `database update` command in the command window to execute the migrations.</span></span>

```console
dotnet ef database update
```

<span data-ttu-id="59475-365">앱 발생을 실행 하는 `DbInitializer.Initialize` 메서드를 실행 하 고 새 데이터베이스를 채웁니다.</span><span class="sxs-lookup"><span data-stu-id="59475-365">Run the app to cause the `DbInitializer.Initialize` method to run and populate the new database.</span></span>

<span data-ttu-id="59475-366">데이터베이스에서 SSOX 앞에서 수행한 것 처럼를 열고 확장는 **테이블** 노드에서 모든 테이블 생성을 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="59475-366">Open the database in SSOX as you did earlier, and expand the **Tables** node to see that all of the tables have been created.</span></span> <span data-ttu-id="59475-367">(아직 SSOX 이전 시점에서 열 경우 새로 고침 단추 클릭.)</span><span class="sxs-lookup"><span data-stu-id="59475-367">(If you still have SSOX open from the earlier time, click the Refresh button.)</span></span>

![SSOX의 테이블](complex-data-model/_static/ssox-tables.png)

<span data-ttu-id="59475-369">데이터베이스를 시드하 이니셜라이저 코드를 트리거할 응용 프로그램을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="59475-369">Run the application to trigger the initializer code that seeds the database.</span></span>

<span data-ttu-id="59475-370">마우스 오른쪽 단추로 클릭는 **CourseAssignment** 테이블을 선택한 **데이터 보기** 에 데이터가 있는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="59475-370">Right-click the **CourseAssignment** table and select **View Data** to verify that it has data in it.</span></span>

![SSOX의 CourseAssignment 데이터](complex-data-model/_static/ssox-ci-data.png)

## <a name="summary"></a><span data-ttu-id="59475-372">요약</span><span class="sxs-lookup"><span data-stu-id="59475-372">Summary</span></span>

<span data-ttu-id="59475-373">이제 더 복잡 한 데이터 모델 및 해당 데이터베이스 만들어졌습니다.</span><span class="sxs-lookup"><span data-stu-id="59475-373">You now have a more complex data model and corresponding database.</span></span> <span data-ttu-id="59475-374">다음 자습서에서 관련된 데이터를 액세스 하는 방법에 대해 자세히 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="59475-374">In the following tutorial, you'll learn more about how to access related data.</span></span>

>[!div class="step-by-step"]
<span data-ttu-id="59475-375">[이전](migrations.md)
[다음](read-related-data.md)</span><span class="sxs-lookup"><span data-stu-id="59475-375">[Previous](migrations.md)
[Next](read-related-data.md)</span></span>  
