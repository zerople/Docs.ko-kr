---
title: "ASP.NET Core MVC EF 코어-업데이트와 관련 데이터-7 / 10"
author: tdykstra
description: "이 자습서에서는 외래 키 필드와 탐색 속성을 업데이트 하 여 관련된 데이터를 업데이트 합니다."
keywords: "ASP.NET Core, Entity Framework Core 관련된 데이터를 조인"
ms.author: tdykstra
manager: wpickett
ms.date: 03/15/2017
ms.topic: get-started-article
ms.assetid: 67bd162b-bfb7-4750-9e7f-705228b5288c
ms.technology: aspnet
ms.prod: asp.net-core
uid: data/ef-mvc/update-related-data
ms.openlocfilehash: 655fefc0f9d884300bea670795c39a7a9aa10bb8
ms.sourcegitcommit: 5355c96a1768e5a1d5698a98c190e7addcc4ded5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/05/2017
---
# <a name="updating-related-data---ef-core-with-aspnet-core-mvc-tutorial-7-of-10"></a><span data-ttu-id="e0628-104">관련된 데이터-ASP.NET Core MVC 자습서 (7 / 10)와 함께 EF 코어 업데이트</span><span class="sxs-lookup"><span data-stu-id="e0628-104">Updating related data - EF Core with ASP.NET Core MVC tutorial (7 of 10)</span></span>

<span data-ttu-id="e0628-105">여 [Tom Dykstra](https://github.com/tdykstra) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="e0628-105">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="e0628-106">Contoso 대학 샘플 웹 응용 프로그램에는 Entity Framework Core 및 Visual Studio를 사용 하 여 ASP.NET Core MVC 웹 응용 프로그램을 만드는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-106">The Contoso University sample web application demonstrates how to create ASP.NET Core MVC web applications using Entity Framework Core and Visual Studio.</span></span> <span data-ttu-id="e0628-107">자습서 시리즈에 대 한 정보를 참조 하십시오. [시리즈의 첫 번째 자습서](intro.md)합니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-107">For information about the tutorial series, see [the first tutorial in the series](intro.md).</span></span>

<span data-ttu-id="e0628-108">관련된 데이터; 이전 자습서에서 표시 이 자습서에서는 외래 키 필드와 탐색 속성을 업데이트 하 여 관련된 데이터를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-108">In the previous tutorial you displayed related data; in this tutorial you'll update related data by updating foreign key fields and navigation properties.</span></span>

<span data-ttu-id="e0628-109">다음 그림은 사용 하는 페이지의 일부를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-109">The following illustrations show some of the pages that you'll work with.</span></span>

![과정 편집 페이지](update-related-data/_static/course-edit.png)

![강사 편집 페이지](update-related-data/_static/instructor-edit-courses.png)

## <a name="customize-the-create-and-edit-pages-for-courses"></a><span data-ttu-id="e0628-112">교육 과정에 대 한 만들기 및 편집 페이지를 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="e0628-112">Customize the Create and Edit Pages for Courses</span></span>

<span data-ttu-id="e0628-113">새 과목 엔터티를 만들 때 기존 부서에는 관계가 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-113">When a new course entity is created, it must have a relationship to an existing department.</span></span> <span data-ttu-id="e0628-114">이 작업을 위해 스 캐 폴드 코드에는 컨트롤러 메서드 및 부서를 선택 하기 위한 드롭 다운 목록을 포함 하는 뷰 만들기 및 편집 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-114">To facilitate this, the scaffolded code includes controller methods and Create and Edit views that include a drop-down list for selecting the department.</span></span> <span data-ttu-id="e0628-115">드롭다운 목록에서 집합은 `Course.DepartmentID` 외래 키 속성 및 Entity Framework를 로드 하는 데 필요한 모든는 `Department` 적절 한 부서 엔터티 된 탐색 속성입니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-115">The drop-down list sets the `Course.DepartmentID` foreign key property, and that's all the Entity Framework needs in order to load the `Department` navigation property with the appropriate Department entity.</span></span> <span data-ttu-id="e0628-116">스 캐 폴드 코드를 사용 하지만 오류 처리를 추가 하 고 드롭 다운 목록을 정렬 하도록 약간 변경 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-116">You'll use the scaffolded code, but change it slightly to add error handling and sort the drop-down list.</span></span>

<span data-ttu-id="e0628-117">*CoursesController.cs*, 4 개의 만들기 및 편집 메서드를 삭제 하 고 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-117">In *CoursesController.cs*, delete the four Create and Edit methods and replace them with the following code:</span></span>

<span data-ttu-id="e0628-118">[!code-csharp[Main](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_CreateGet)]</span><span class="sxs-lookup"><span data-stu-id="e0628-118">[!code-csharp[Main](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_CreateGet)]</span></span>

<span data-ttu-id="e0628-119">[!code-csharp[Main](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_CreatePost)]</span><span class="sxs-lookup"><span data-stu-id="e0628-119">[!code-csharp[Main](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_CreatePost)]</span></span>

<span data-ttu-id="e0628-120">[!code-csharp[Main](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_EditGet)]</span><span class="sxs-lookup"><span data-stu-id="e0628-120">[!code-csharp[Main](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_EditGet)]</span></span>

<span data-ttu-id="e0628-121">[!code-csharp[Main](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_EditPost)]</span><span class="sxs-lookup"><span data-stu-id="e0628-121">[!code-csharp[Main](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_EditPost)]</span></span>

<span data-ttu-id="e0628-122">이후에 `Edit` HttpPost 메서드 드롭 다운 목록에 대 한 부서 정보를 로드 하는 새 메서드를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-122">After the `Edit` HttpPost method, create a new method that loads department info for the drop-down list.</span></span>

<span data-ttu-id="e0628-123">[!code-csharp[Main](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_Departments)]</span><span class="sxs-lookup"><span data-stu-id="e0628-123">[!code-csharp[Main](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_Departments)]</span></span>

<span data-ttu-id="e0628-124">`PopulateDepartmentsDropDownList` 메서드 이름을 기준으로 정렬 하는 모든 부서의 목록을 가져옵니다, 만듭니다는 `SelectList` 드롭 다운 목록에 대 한 컬렉션 뷰 컬렉션을 전달 하 고 `ViewBag`합니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-124">The `PopulateDepartmentsDropDownList` method gets a list of all departments sorted by name, creates a `SelectList` collection for a drop-down list, and passes the collection to the view in `ViewBag`.</span></span> <span data-ttu-id="e0628-125">메서드에 선택적 `selectedDepartment` 드롭 다운 목록에서 렌더링 될 때 선택 항목을 지정 하는 호출 코드를 허용 하는 매개 변수입니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-125">The method accepts the optional `selectedDepartment` parameter that allows the calling code to specify the item that will be selected when the drop-down list is rendered.</span></span> <span data-ttu-id="e0628-126">보기에 이름이 "DepartmentID"를 전달 하는 `<select>` 태그 도우미와 도우미를 찾는 다음 알고는 `ViewBag` 개체에 대 한는 `SelectList` "DepartmentID" 라는 합니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-126">The view will pass the name "DepartmentID" to the `<select>` tag helper, and the helper then knows to look in the `ViewBag` object for a `SelectList` named "DepartmentID".</span></span>

<span data-ttu-id="e0628-127">HttpGet `Create` 메서드 호출의 `PopulateDepartmentsDropDownList` 새 과정에 대 한 부서 설정 되지 않으며 아직 때문에 선택한 항목을 설정 하지 않고 메서드:</span><span class="sxs-lookup"><span data-stu-id="e0628-127">The HttpGet `Create` method calls the `PopulateDepartmentsDropDownList` method without setting the selected item, because for a new course the department is not established yet:</span></span>

<span data-ttu-id="e0628-128">[!code-csharp[Main](intro/samples/cu/Controllers/CoursesController.cs?highlight=3&name=snippet_CreateGet)]</span><span class="sxs-lookup"><span data-stu-id="e0628-128">[!code-csharp[Main](intro/samples/cu/Controllers/CoursesController.cs?highlight=3&name=snippet_CreateGet)]</span></span>

<span data-ttu-id="e0628-129">HttpGet `Edit` 메서드 편집 중인 과정에 이미 할당 되어 있는 분야의 ID에 따라 선택한 항목을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-129">The HttpGet `Edit` method sets the selected item, based on the ID of the department that is already assigned to the course being edited:</span></span>

<span data-ttu-id="e0628-130">[!code-csharp[Main](intro/samples/cu/Controllers/CoursesController.cs?highlight=15&name=snippet_EditGet)]</span><span class="sxs-lookup"><span data-stu-id="e0628-130">[!code-csharp[Main](intro/samples/cu/Controllers/CoursesController.cs?highlight=15&name=snippet_EditGet)]</span></span>

<span data-ttu-id="e0628-131">둘 다에 대해 HttpPost 메서드 `Create` 및 `Edit` 도 오류가 발생 한 후 페이지를 다시 표시 될 때 선택한 항목을 설정 하는 코드를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-131">The HttpPost methods for both `Create` and `Edit` also include code that sets the selected item when they redisplay the page after an error.</span></span> <span data-ttu-id="e0628-132">이렇게 하면는 오류 메시지를 표시 하기 페이지 다시 표시 됩니다 때 어떤 부서 선택한 선택 된 상태로 유지 합니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-132">This ensures that when the page is redisplayed to show the error message, whatever department was selected stays selected.</span></span>

### <a name="add-asnotracking-to-details-and-delete-methods"></a><span data-ttu-id="e0628-133">추가 합니다. 세부 정보를 AsNoTracking 및 Delete 메서드</span><span class="sxs-lookup"><span data-stu-id="e0628-133">Add .AsNoTracking to Details and Delete methods</span></span>

<span data-ttu-id="e0628-134">강좌 세부 정보 및 Delete 페이지의 성능을 최적화 하기 위해 추가 `AsNoTracking` 에서 호출 된 `Details` 및 HttpGet `Delete` 메서드.</span><span class="sxs-lookup"><span data-stu-id="e0628-134">To optimize performance of the Course Details and Delete pages, add `AsNoTracking` calls in the `Details` and HttpGet `Delete` methods.</span></span>

<span data-ttu-id="e0628-135">[!code-csharp[Main](intro/samples/cu/Controllers/CoursesController.cs?highlight=10&name=snippet_Details)]</span><span class="sxs-lookup"><span data-stu-id="e0628-135">[!code-csharp[Main](intro/samples/cu/Controllers/CoursesController.cs?highlight=10&name=snippet_Details)]</span></span>

<span data-ttu-id="e0628-136">[!code-csharp[Main](intro/samples/cu/Controllers/CoursesController.cs?highlight=10&name=snippet_DeleteGet)]</span><span class="sxs-lookup"><span data-stu-id="e0628-136">[!code-csharp[Main](intro/samples/cu/Controllers/CoursesController.cs?highlight=10&name=snippet_DeleteGet)]</span></span>

### <a name="modify-the-course-views"></a><span data-ttu-id="e0628-137">과정 뷰 수정</span><span class="sxs-lookup"><span data-stu-id="e0628-137">Modify the Course views</span></span>

<span data-ttu-id="e0628-138">*Views/Courses/Create.cshtml*, "부서 선택" 옵션을 추가 하는 **부서** 드롭 다운 목록에서에서 캡션은 변경할 **DepartmentID** 를  **부서**, 유효성 검사 메시지를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-138">In *Views/Courses/Create.cshtml*, add a "Select Department" option to the **Department** drop-down list, change the caption from **DepartmentID** to **Department**, and add a validation message.</span></span>

<span data-ttu-id="e0628-139">[!code-html[Main](intro/samples/cu/Views/Courses/Create.cshtml?highlight=2-6&range=29-34)]</span><span class="sxs-lookup"><span data-stu-id="e0628-139">[!code-html[Main](intro/samples/cu/Views/Courses/Create.cshtml?highlight=2-6&range=29-34)]</span></span>

<span data-ttu-id="e0628-140">*Views/Courses/Edit.cshtml*, 방금에서 수행한 부서 필드에 대 한 동일한 변경 내용을 *Create.cshtml*합니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-140">In *Views/Courses/Edit.cshtml*, make the same change for the Department field that you just did in *Create.cshtml*.</span></span>

<span data-ttu-id="e0628-141">또한 *Views/Courses/Edit.cshtml*, 추가 하기 전에 과정 숫자 필드는 **제목** 필드입니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-141">Also in *Views/Courses/Edit.cshtml*, add a course number field before the **Title** field.</span></span> <span data-ttu-id="e0628-142">과정 수의 기본 키 이기 때문에 표시 될 있지만 변경할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-142">Because the course number is the primary key, it's displayed, but it can't be changed.</span></span>

<span data-ttu-id="e0628-143">[!code-html[Main](intro/samples/cu/Views/Courses/Edit.cshtml?range=15-18)]</span><span class="sxs-lookup"><span data-stu-id="e0628-143">[!code-html[Main](intro/samples/cu/Views/Courses/Edit.cshtml?range=15-18)]</span></span>

<span data-ttu-id="e0628-144">이미 숨겨진된 필드 (`<input type="hidden">`) 편집 보기에서 과정 번호에 대 한 합니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-144">There's already a hidden field (`<input type="hidden">`) for the course number in the Edit view.</span></span> <span data-ttu-id="e0628-145">추가 `<label>` 태그 도우미를 클릭할 때 게시 된 데이터에 포함 되도록 과정 번호를 발생 하지 않는 되었기 때문에 숨겨진된 필드에 대 한 필요성을 제거 하지 않습니다 **저장** 에 **편집** 페이지.</span><span class="sxs-lookup"><span data-stu-id="e0628-145">Adding a `<label>` tag helper doesn't eliminate the need for the hidden field because it doesn't cause the course number to be included in the posted data when the user clicks **Save** on the **Edit** page.</span></span>

<span data-ttu-id="e0628-146">*Views/Courses/Delete.cshtml*, 위쪽에서 과정 숫자 필드를 추가 하 고 부서 ID 부서 이름으로 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-146">In *Views/Courses/Delete.cshtml*, add a course number field at the top and change department ID to department name.</span></span>

<span data-ttu-id="e0628-147">[!code-html[Main](intro/samples/cu/Views/Courses/Delete.cshtml?highlight=14-19,36)]</span><span class="sxs-lookup"><span data-stu-id="e0628-147">[!code-html[Main](intro/samples/cu/Views/Courses/Delete.cshtml?highlight=14-19,36)]</span></span>

<span data-ttu-id="e0628-148">*Views/Courses/Details.cshtml*, 수행한에 대 한 동일한 변경 내용을 *Delete.cshtml*합니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-148">In *Views/Courses/Details.cshtml*, make the same change that you just did for *Delete.cshtml*.</span></span>

### <a name="test-the-course-pages"></a><span data-ttu-id="e0628-149">테스트 과정 페이지</span><span class="sxs-lookup"><span data-stu-id="e0628-149">Test the Course pages</span></span>

<span data-ttu-id="e0628-150">실행의 **만들기** 페이지 (과정 인덱스 페이지를 표시 하 고 클릭 **새로 만들기**) 새 과정에 대 한 데이터를 입력 합니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-150">Run the **Create** page (display the Course Index page and click **Create New**) and enter data for a new course:</span></span>

![코스 만들기 페이지](update-related-data/_static/course-create.png)

<span data-ttu-id="e0628-152">
              **만들기**를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-152">Click **Create**.</span></span> <span data-ttu-id="e0628-153">Courses 인덱스 페이지가 목록에 추가 된 새 과정으로 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-153">The Courses Index page is displayed with the new course added to the list.</span></span> <span data-ttu-id="e0628-154">인덱스 페이지 목록에 있는 부서 이름과 관계가 올바르게 설정 되었는지 표시 하는 탐색 속성에서 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-154">The department name in the Index page list comes from the navigation property, showing that the relationship was established correctly.</span></span>

<span data-ttu-id="e0628-155">실행 된 **편집** 페이지 (클릭 **편집** 과정 인덱스 페이지에는 과정에).</span><span class="sxs-lookup"><span data-stu-id="e0628-155">Run the **Edit** page (click **Edit** on a course in the Course Index page ).</span></span>

![과정 편집 페이지](update-related-data/_static/course-edit.png)

<span data-ttu-id="e0628-157">페이지에서 데이터를 변경 하 고 클릭 **저장**합니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-157">Change data on the page and click **Save**.</span></span> <span data-ttu-id="e0628-158">Courses 인덱스 페이지가 업데이트 과정 데이터와 함께 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-158">The Courses Index page is displayed with the updated course data.</span></span>

## <a name="add-an-edit-page-for-instructors"></a><span data-ttu-id="e0628-159">강사에 대 한 편집 페이지를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-159">Add an Edit Page for Instructors</span></span>

<span data-ttu-id="e0628-160">강사 레코드를 편집할 때 강의 사무실 할당을 업데이트할 수 하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-160">When you edit an instructor record, you want to be able to update the instructor's office assignment.</span></span> <span data-ttu-id="e0628-161">Instructor 엔터티 즉, 사용자 코드에 다음과 같은 경우 처리는 OfficeAssignment 엔터티와 0 또는 1을 한 관계에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-161">The Instructor entity has a one-to-zero-or-one relationship with the OfficeAssignment entity, which means your code has to handle the following situations:</span></span>

* <span data-ttu-id="e0628-162">사용자 선택을 사무실 할당을 취소 하는 경우 원래 값을이 OfficeAssignment 엔터티를 삭제 합니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-162">If the user clears the office assignment and it originally had a value, delete the OfficeAssignment entity.</span></span>

* <span data-ttu-id="e0628-163">사용자가 office 할당 값을 입력 하 고 원래 비어 있던 경우 새 OfficeAssignment 엔터티를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-163">If the user enters an office assignment value and it originally was empty, create a new OfficeAssignment entity.</span></span>

* <span data-ttu-id="e0628-164">사용자가 사무실 할당의 값을 변경 해도 기존 OfficeAssignment 엔터티 값을 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-164">If the user changes the value of an office assignment, change the value in an existing OfficeAssignment entity.</span></span>

### <a name="update-the-instructors-controller"></a><span data-ttu-id="e0628-165">강사 컨트롤러를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-165">Update the Instructors controller</span></span>

<span data-ttu-id="e0628-166">*InstructorsController.cs*는 HttpGet의 코드 변경 `Edit` Instructor 엔터티를 로드 한다는 메서드 `OfficeAssignment` 탐색 속성과 호출 `AsNoTracking`:</span><span class="sxs-lookup"><span data-stu-id="e0628-166">In *InstructorsController.cs*, change the code in the HttpGet `Edit` method so that it loads the Instructor entity's `OfficeAssignment` navigation property and calls `AsNoTracking`:</span></span>

<span data-ttu-id="e0628-167">[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?highlight=9,10&name=snippet_EditGetOA)]</span><span class="sxs-lookup"><span data-stu-id="e0628-167">[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?highlight=9,10&name=snippet_EditGetOA)]</span></span>

<span data-ttu-id="e0628-168">대체는 HttpPost `Edit` 를 다음 코드로 office 할당 업데이트를 처리 하는 메서드:</span><span class="sxs-lookup"><span data-stu-id="e0628-168">Replace the HttpPost `Edit` method with the following code to handle office assignment updates:</span></span>

<span data-ttu-id="e0628-169">[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_EditPostOA)]</span><span class="sxs-lookup"><span data-stu-id="e0628-169">[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_EditPostOA)]</span></span>

<span data-ttu-id="e0628-170">코드는 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-170">The code does the following:</span></span>

-  <span data-ttu-id="e0628-171">에 메서드 이름을 변경 하는 `EditPost` 서명이 이제는 HttpGet와 동일 하기 때문에 `Edit` 메서드 (는 `ActionName` 특성을 지정 하는 `/Edit/` URL은 여전히 사용).</span><span class="sxs-lookup"><span data-stu-id="e0628-171">Changes the method name to `EditPost` because the signature is now the same as the HttpGet `Edit` method (the `ActionName` attribute specifies that the `/Edit/` URL is still used).</span></span>

-  <span data-ttu-id="e0628-172">에 대 한 로드 신속 하 게 사용 하 여 데이터베이스에서 현재 Instructor 엔터티를 가져옵니다는 `OfficeAssignment` 탐색 속성입니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-172">Gets the current Instructor entity from the database using eager loading for the `OfficeAssignment` navigation property.</span></span> <span data-ttu-id="e0628-173">이는 HttpGet에서 수행한 것과 동일 `Edit` 메서드.</span><span class="sxs-lookup"><span data-stu-id="e0628-173">This is the same as what you did in the HttpGet `Edit` method.</span></span>

-  <span data-ttu-id="e0628-174">모델 바인더의 값으로 검색 된 Instructor 엔터티를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-174">Updates the retrieved Instructor entity with values from the model binder.</span></span> <span data-ttu-id="e0628-175">`TryUpdateModel` 오버 로드를 사용 하면 허용 목록에 포함 하려는 속성입니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-175">The `TryUpdateModel` overload enables you to whitelist the properties you want to include.</span></span> <span data-ttu-id="e0628-176">이렇게 하면 과도 하 게 게시에 설명 된 대로 [두 번째 자습서](crud.md)합니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-176">This prevents over-posting, as explained in the [second tutorial](crud.md).</span></span>

    <!-- Snippets do not play well with <ul> [!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?range=241-244)] -->

    ```csharp
    if (await TryUpdateModelAsync<Instructor>(
        instructorToUpdate,
        "",
        i => i.FirstMidName, i => i.LastName, i => i.HireDate, i => i.OfficeAssignment))
    ```
    
-   <span data-ttu-id="e0628-177">사무실 위치 비어 있으면 OfficeAssignment 테이블의 관련된 행이 삭제 됩니다 되도록 null로 Instructor.OfficeAssignment 속성을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-177">If the office location is blank, sets the Instructor.OfficeAssignment property to null so that the related row in the OfficeAssignment table will be deleted.</span></span>

    <!-- Snippets do not play well with <ul>  "intro/samples/cu/Controllers/InstructorsController.cs"} -->

    ```csharp
    if (String.IsNullOrWhiteSpace(instructorToUpdate.OfficeAssignment?.Location))
    {
        instructorToUpdate.OfficeAssignment = null;
    }
    ```

- <span data-ttu-id="e0628-178">데이터베이스에 변경 내용을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-178">Saves the changes to the database.</span></span>

### <a name="update-the-instructor-edit-view"></a><span data-ttu-id="e0628-179">강사 편집 뷰 업데이트</span><span class="sxs-lookup"><span data-stu-id="e0628-179">Update the Instructor Edit view</span></span>

<span data-ttu-id="e0628-180">*Views/Instructors/Edit.cshtml*, 사무실 위치 하기 전에 끝에 편집 하기 위해 새 필드를 추가 하는 **저장** 단추:</span><span class="sxs-lookup"><span data-stu-id="e0628-180">In *Views/Instructors/Edit.cshtml*, add a new field for editing the office location, at the end before the **Save** button:</span></span>

<span data-ttu-id="e0628-181">[!code-html[Main](intro/samples/cu/Views/Instructors/Edit.cshtml?range=30-34)]</span><span class="sxs-lookup"><span data-stu-id="e0628-181">[!code-html[Main](intro/samples/cu/Views/Instructors/Edit.cshtml?range=30-34)]</span></span>

<span data-ttu-id="e0628-182">페이지 실행 (선택 된 **강사** 탭을 클릭 한 다음 **편집** 강사에).</span><span class="sxs-lookup"><span data-stu-id="e0628-182">Run the page (select the **Instructors** tab and then click **Edit** on an instructor).</span></span> <span data-ttu-id="e0628-183">변경 된 **사무실 위치** 클릭 **저장**합니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-183">Change the **Office Location** and click **Save**.</span></span>

![강사 편집 페이지](update-related-data/_static/instructor-edit-office.png)

## <a name="add-course-assignments-to-the-instructor-edit-page"></a><span data-ttu-id="e0628-185">강사 편집 페이지를 과정 할당을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-185">Add Course assignments to the Instructor Edit page</span></span>

<span data-ttu-id="e0628-186">강사는 courses 개수에 관계 없이 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-186">Instructors may teach any number of courses.</span></span> <span data-ttu-id="e0628-187">이제 다음 스크린 샷에 표시 된 것 처럼 확인란 그룹을 사용 하는 과정 할당을 변경 하는 기능을 추가 하 여 강사 편집 페이지를 향상 시켜:</span><span class="sxs-lookup"><span data-stu-id="e0628-187">Now you'll enhance the Instructor Edit page by adding the ability to change course assignments using a group of check boxes, as shown in the following screen shot:</span></span>

![Courses와 강사 편집 페이지](update-related-data/_static/instructor-edit-courses.png)

<span data-ttu-id="e0628-189">Course 및 Instructor 엔터티 간의 다 대 다입니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-189">The relationship between the Course and Instructor entities is many-to-many.</span></span> <span data-ttu-id="e0628-190">추가 하 고 관계를 추가 및 CourseAssignments 조인 엔터티 집합에서 엔터티를 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-190">To add and remove relationships, you add and remove entities to and from the CourseAssignments join entity set.</span></span>

<span data-ttu-id="e0628-191">과정을 변경할 수 있도록 하는 UI 강사는은 확인란의 그룹에 할당 합니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-191">The UI that enables you to change which courses an instructor is assigned to is a group of check boxes.</span></span> <span data-ttu-id="e0628-192">데이터베이스의 모든 과정에 대 한 확인란이 표시 되 고 강사에 현재 할당 되어 있는 것을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-192">A check box for every course in the database is displayed, and the ones that the instructor is currently assigned to are selected.</span></span> <span data-ttu-id="e0628-193">사용자가 선택 하거나 과정 할당을 변경 하려면 확인란의 선택을 취소 합니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-193">The user can select or clear check boxes to change course assignments.</span></span> <span data-ttu-id="e0628-194">Courses 수가 된 훨씬 큰을 뷰에서 데이터를 제공 합니다. 다른 방법을 사용 하 고 싶을 것 하지만 관계 만들기 또는 삭제 하려면 조인 엔터티를 조작 하는 동일한 메서드를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-194">If the number of courses were much greater, you would probably want to use a different method of presenting the data in the view, but you'd use the same method of manipulating a join entity to create or delete relationships.</span></span>

### <a name="update-the-instructors-controller"></a><span data-ttu-id="e0628-195">강사 컨트롤러를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-195">Update the Instructors controller</span></span>

<span data-ttu-id="e0628-196">확인란 목록 보기에는 데이터를 제공 하려면 보기 모델 클래스를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-196">To provide data to the view for the list of check boxes, you'll use a view model class.</span></span>

<span data-ttu-id="e0628-197">만들 *AssignedCourseData.cs* 에 *SchoolViewModels* 폴더와 기존 코드를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-197">Create *AssignedCourseData.cs* in the *SchoolViewModels* folder and replace the existing code with the following code:</span></span>

<span data-ttu-id="e0628-198">[!code-csharp[Main](intro/samples/cu/Models/SchoolViewModels/AssignedCourseData.cs)]</span><span class="sxs-lookup"><span data-stu-id="e0628-198">[!code-csharp[Main](intro/samples/cu/Models/SchoolViewModels/AssignedCourseData.cs)]</span></span>

<span data-ttu-id="e0628-199">*InstructorsController.cs*, 대체는 HttpGet `Edit` 메서드를 다음 코드로 합니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-199">In *InstructorsController.cs*, replace the HttpGet `Edit` method with the following code.</span></span> <span data-ttu-id="e0628-200">변경 내용은 강조 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-200">The changes are highlighted.</span></span>

<span data-ttu-id="e0628-201">[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?highlight=10,17,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36&name=snippet_EditGetCourses)]</span><span class="sxs-lookup"><span data-stu-id="e0628-201">[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?highlight=10,17,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36&name=snippet_EditGetCourses)]</span></span>

<span data-ttu-id="e0628-202">에 대 한 즉시 로드를 추가 하는 코드는 `Courses` 탐색 속성 새 호출 `PopulateAssignedCourseData` 메서드를 사용 하 여 확인란 배열에 대 한 정보를 제공는 `AssignedCourseData` 모델 클래스를 보고 합니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-202">The code adds eager loading for the `Courses` navigation property and calls the new `PopulateAssignedCourseData` method to provide information for the check box array using the `AssignedCourseData` view model class.</span></span>

<span data-ttu-id="e0628-203">코드는 `PopulateAssignedCourseData` 보기 모델 클래스를 사용 하는 과정의 목록을 로드 하기 위해 모든 과정 엔터티를 통해 메서드를 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-203">The code in the `PopulateAssignedCourseData` method reads through all Course entities in order to load a list of courses using the view model class.</span></span> <span data-ttu-id="e0628-204">각 과정에 대 한 코드 과정 강의에 있는지 여부를 확인 `Courses` 탐색 속성입니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-204">For each course, the code checks whether the course exists in the instructor's `Courses` navigation property.</span></span> <span data-ttu-id="e0628-205">효율적인 조회를 만들려면 과정에서 강사에 할당 되었는지 여부를 확인할 때 강사에 할당 하는 과정에 저장 됩니다는 `HashSet` 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-205">To create efficient lookup when checking whether a course is assigned to the instructor, the courses assigned to the instructor are put into a `HashSet` collection.</span></span> <span data-ttu-id="e0628-206">`Assigned` 속성이로 설정 된 과정에 대해 true 강사에 할당 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-206">The `Assigned` property  is set to true for courses the instructor is assigned to.</span></span> <span data-ttu-id="e0628-207">보기 선택 상자로 표시 되어야 하는 확인을 확인 하려면이 속성을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-207">The view will use this property to determine which check boxes must be displayed as selected.</span></span> <span data-ttu-id="e0628-208">목록 보기에 전달 되는 마지막으로, `ViewData`합니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-208">Finally, the list is passed to the view in `ViewData`.</span></span>

<span data-ttu-id="e0628-209">다음으로, 사용자가 클릭할 때 실행 되는 코드를 추가 **저장**합니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-209">Next, add the code that's executed when the user clicks **Save**.</span></span> <span data-ttu-id="e0628-210">대체는 `EditPost` 메서드를 다음 코드를 복사해 업데이트 하는 새 메서드 추가 `Courses` Instructor 엔터티의 탐색 속성입니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-210">Replace the `EditPost` method with the following code, and add a new method that updates the `Courses` navigation property of the Instructor entity.</span></span>

<span data-ttu-id="e0628-211">[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?highlight=1,3,12,13,25,39-40&name=snippet_EditPostCourses)]</span><span class="sxs-lookup"><span data-stu-id="e0628-211">[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?highlight=1,3,12,13,25,39-40&name=snippet_EditPostCourses)]</span></span>

<span data-ttu-id="e0628-212">[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_UpdateCourses&highlight=1-31)]</span><span class="sxs-lookup"><span data-stu-id="e0628-212">[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_UpdateCourses&highlight=1-31)]</span></span>

<span data-ttu-id="e0628-213">메서드 시그니처는 HttpGet와에서 다르면 이제 `Edit` 에서 메서드 이름을 변경 하므로 `EditPost` 다시 `Edit`합니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-213">The method signature is now different from the HttpGet `Edit` method, so the method name changes from `EditPost` back to `Edit`.</span></span>

<span data-ttu-id="e0628-214">모델 바인더를 자동으로 업데이트할 수 없습니다 보기 과정 엔터티 컬렉션에 한는 `CourseAssignments` 탐색 속성입니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-214">Since the view doesn't have a collection of Course entities, the model binder can't automatically update the `CourseAssignments` navigation property.</span></span> <span data-ttu-id="e0628-215">모델 바인더를 사용 하 여 업데이트 하는 대신는 `CourseAssignments` 탐색 속성을 새 그렇게 `UpdateInstructorCourses` 메서드.</span><span class="sxs-lookup"><span data-stu-id="e0628-215">Instead of using the model binder to update the `CourseAssignments` navigation property, you do that in the new `UpdateInstructorCourses` method.</span></span> <span data-ttu-id="e0628-216">따라서 제외 해야는 `CourseAssignments` 모델 바인딩에서 속성입니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-216">Therefore you need to exclude the `CourseAssignments` property from model binding.</span></span> <span data-ttu-id="e0628-217">호출 하는 코드를 변경 하지 않아도이 `TryUpdateModel` 허용 목록이 오버 로드를 사용 중 이므로 및 `CourseAssignments` include 목록에 있지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-217">This doesn't require any change to the code that calls `TryUpdateModel` because you're using the whitelisting overload and `CourseAssignments` isn't in the include list.</span></span>

<span data-ttu-id="e0628-218">경우 없는 확인란이 선택 된을의 코드 `UpdateInstructorCourses` 초기화는 `CourseAssignments` 빈 컬렉션 및 반환 된 탐색 속성:</span><span class="sxs-lookup"><span data-stu-id="e0628-218">If no check boxes were selected, the code in `UpdateInstructorCourses` initializes the `CourseAssignments` navigation property with an empty collection and returns:</span></span>

<span data-ttu-id="e0628-219">[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_UpdateCourses&highlight=3-7)]</span><span class="sxs-lookup"><span data-stu-id="e0628-219">[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_UpdateCourses&highlight=3-7)]</span></span>

<span data-ttu-id="e0628-220">코드 다음 데이터베이스의 모든 과정을 반복 하 고 각 과목와 보기에서 선택 된 강사에 현재 할당 된 것에 대해 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-220">The code then loops through all courses in the database and checks each course against the ones currently assigned to the instructor versus the ones that were selected in the view.</span></span> <span data-ttu-id="e0628-221">두 번째 컬렉션에 저장 된 효율적인 조회를 쉽게 `HashSet` 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-221">To facilitate efficient lookups, the latter two collections are stored in `HashSet` objects.</span></span>

<span data-ttu-id="e0628-222">과정에 대 한 확인란을 선택 했지만 과정에 없는 경우는 `Instructor.CourseAssignments` 탐색 속성 과정 탐색 속성의 컬렉션에 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-222">If the check box for a course was selected but the course isn't in the `Instructor.CourseAssignments` navigation property, the course is added to the collection in the navigation property.</span></span>

<span data-ttu-id="e0628-223">[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?highlight=14-20&name=snippet_UpdateCourses)]</span><span class="sxs-lookup"><span data-stu-id="e0628-223">[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?highlight=14-20&name=snippet_UpdateCourses)]</span></span>

<span data-ttu-id="e0628-224">과정에 대 한 확인란 선택 되지 않은 과정에 속하지만 `Instructor.CourseAssignments` 과정 탐색 속성이 탐색 속성에서 제거 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-224">If the check box for a course wasn't selected, but the course is in the `Instructor.CourseAssignments` navigation property, the course is removed from the navigation property.</span></span>

<span data-ttu-id="e0628-225">[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?highlight=21-29&name=snippet_UpdateCourses)]</span><span class="sxs-lookup"><span data-stu-id="e0628-225">[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?highlight=21-29&name=snippet_UpdateCourses)]</span></span>

### <a name="update-the-instructor-views"></a><span data-ttu-id="e0628-226">강사 뷰 업데이트</span><span class="sxs-lookup"><span data-stu-id="e0628-226">Update the Instructor views</span></span>

<span data-ttu-id="e0628-227">*Views/Instructors/Edit.cshtml*, 추가 **Courses** 을 추가 하 여 확인란의 배열로 필드 바로 다음 코드는 `div` 에 대 한 요소는 **Office**  필드 및 하기 전에 `div` 에 대 한 요소는 **저장** 단추입니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-227">In *Views/Instructors/Edit.cshtml*, add a **Courses** field with an array of check boxes by adding the following code immediately after the `div` elements for the **Office** field and before the `div` element for the **Save** button.</span></span>

<a id="notepad"></a>
> [!NOTE] 
> <span data-ttu-id="e0628-228">Visual Studio에서 코드를 붙여 넣을 때 줄 바꿈 코드를 중단 하는 방식으로 변경 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-228">When you paste the code in Visual Studio, line breaks will be changed in a way that breaks the code.</span></span>  <span data-ttu-id="e0628-229">자동 서식 지정을 실행 취소 하려면 Ctrl + Z를 한 번 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-229">Press Ctrl+Z one time to undo the automatic formatting.</span></span>  <span data-ttu-id="e0628-230">여기 같이 있도록 줄 바꿈이 수정 합니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-230">This will fix the line breaks so that they look like what you see here.</span></span> <span data-ttu-id="e0628-231">들여쓰기 완벽 하지 않아도 되지만 `@</tr><tr>`, `@:<td>`, `@:</td>`, 및 `@:</tr>` 줄 각각 한 줄에 표시 된 것 처럼 이거나 런타임 오류 메시지가 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-231">The indentation doesn't have to be perfect, but the `@</tr><tr>`, `@:<td>`, `@:</td>`, and `@:</tr>` lines must each be on a single line as shown or you'll get a runtime error.</span></span> <span data-ttu-id="e0628-232">선택 된 새 코드 블록과 Tab 세 번 키를 눌러 줄 기존 코드와 함께 새 코드를 합니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-232">With the block of new code selected, press Tab three times to line up the new code with the existing code.</span></span>

<span data-ttu-id="e0628-233">[!code-html[Main](intro/samples/cu/Views/Instructors/Edit.cshtml?range=35-61)]</span><span class="sxs-lookup"><span data-stu-id="e0628-233">[!code-html[Main](intro/samples/cu/Views/Instructors/Edit.cshtml?range=35-61)]</span></span>

<span data-ttu-id="e0628-234">이 코드는 세 개의 열이 있는 HTML 테이블을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-234">This code creates an HTML table that has three columns.</span></span> <span data-ttu-id="e0628-235">각 열에는 확인란이 과정 번호 및 제목으로 구성 된 캡션 뒤 합니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-235">In each column is a check box followed by a caption that consists of the course number and title.</span></span> <span data-ttu-id="e0628-236">모든 확인란 있어야 그룹으로 처리 해야 하는 모델 바인더에 게 동일한 이름 ("selectedCourses").</span><span class="sxs-lookup"><span data-stu-id="e0628-236">The check boxes all have the same name ("selectedCourses"), which informs the model binder that they are to be treated as a group.</span></span> <span data-ttu-id="e0628-237">각 확인란의 value 속성의 값으로 설정 되어 `CourseID`합니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-237">The value attribute of each check box is set to the value of `CourseID`.</span></span> <span data-ttu-id="e0628-238">모델 바인더 구성 된 컨트롤러에 배열을 전달 페이지가 게시 되는 경우는 `CourseID` 확인란만 선택 되에 대 한 값입니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-238">When the page is posted, the model binder passes an array to the controller that consists of the `CourseID` values for only the check boxes which are selected.</span></span>

<span data-ttu-id="e0628-239">확인란은 처음 렌더링 됩니다 때 (에 확인 표시)을 선택 하는 특성을 확인가 강사에 할당 하는 과정에 사용 되는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-239">When the check boxes are initially rendered, those that are for courses assigned to the instructor have checked attributes, which selects them (displays them checked).</span></span>

<span data-ttu-id="e0628-240">강사 인덱스 페이지를 실행 하 고 클릭 **편집** 보려면 강사에는 **편집** 페이지.</span><span class="sxs-lookup"><span data-stu-id="e0628-240">Run the Instructor Index page, and click **Edit** on an instructor to see the **Edit** page.</span></span>

![Courses와 강사 편집 페이지](update-related-data/_static/instructor-edit-courses.png)

<span data-ttu-id="e0628-242">일부 과정 할당을 변경 하 고 저장을 클릭 합니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-242">Change some course assignments and click Save.</span></span> <span data-ttu-id="e0628-243">인덱스 페이지에 변경 내용은 반영 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-243">The changes you make are reflected on the Index page.</span></span>

> [!NOTE] 
> <span data-ttu-id="e0628-244">강사 과정 데이터를 편집 하려면 여기에 적용 되는 방법을 제한 된 수의 과목 필요한 경우에 작동 합니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-244">The approach taken here to edit instructor course data works well when there is a limited number of courses.</span></span> <span data-ttu-id="e0628-245">훨씬 큰 경우에 컬렉션의 경우 다른 UI와 다른 업데이트 방법을 필요한 것입니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-245">For collections that are much larger, a different UI and a different updating method would be required.</span></span>

## <a name="update-the-delete-page"></a><span data-ttu-id="e0628-246">업데이트 페이지 삭제</span><span class="sxs-lookup"><span data-stu-id="e0628-246">Update the Delete page</span></span>

<span data-ttu-id="e0628-247">*InstructorsController.cs*, 삭제는 `DeleteConfirmed` 다음 해당 위치에 코드 메서드 및 삽입 합니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-247">In *InstructorsController.cs*, delete the `DeleteConfirmed` method and insert the following code in its place.</span></span>

<span data-ttu-id="e0628-248">[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?highlight=5-7,9-12&name=snippet_DeleteConfirmed)]</span><span class="sxs-lookup"><span data-stu-id="e0628-248">[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?highlight=5-7,9-12&name=snippet_DeleteConfirmed)]</span></span>

<span data-ttu-id="e0628-249">이 코드에서는 다음과 같이 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-249">This code makes the following changes:</span></span>

* <span data-ttu-id="e0628-250">에 대 한 로드는 eager는 `CourseAssignments` 탐색 속성입니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-250">Does eager loading for the `CourseAssignments` navigation property.</span></span>  <span data-ttu-id="e0628-251">이 포함 하거나 관련에 대 한 알 수 없고 EF `CourseAssignment` 엔터티 하 고 삭제 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-251">You have to include this or EF won't know about related `CourseAssignment` entities and won't delete them.</span></span>  <span data-ttu-id="e0628-252">읽이 필요가 없도록 하려면 여기를 구성할 수 있습니다 하위 삭제를 데이터베이스입니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-252">To avoid needing to read them here you could configure cascade delete in the database.</span></span>

* <span data-ttu-id="e0628-253">삭제할 강사 부서의 관리자로 할당 된 경우 해당 부서에서 강사 할당을 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-253">If the instructor to be deleted is assigned as administrator of any departments, removes the instructor assignment from those departments.</span></span>

## <a name="add-office-location-and-courses-to-the-create-page"></a><span data-ttu-id="e0628-254">사무실 위치 및 courses 만들기 페이지에 추가</span><span class="sxs-lookup"><span data-stu-id="e0628-254">Add office location and courses to the Create page</span></span>

<span data-ttu-id="e0628-255">*InstructorsController.cs*, HttpGet 및 HttpPost 삭제 `Create` 메서드를 그 자리에 다음 코드를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-255">In *InstructorsController.cs*, delete the HttpGet and HttpPost `Create` methods, and then add the following code in their place:</span></span>

<span data-ttu-id="e0628-256">[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_Create&highlight=3-5,12,14-22,29)]</span><span class="sxs-lookup"><span data-stu-id="e0628-256">[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_Create&highlight=3-5,12,14-22,29)]</span></span>

<span data-ttu-id="e0628-257">이 코드에 대 한 확인 했던 것과 비슷합니다는 `Edit` 방법이 없는 courses 처음에 제외 하 고 선택 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-257">This code is similar to what you saw for the `Edit` methods except that initially no courses are selected.</span></span> <span data-ttu-id="e0628-258">HttpGet `Create` 메서드 호출의 `PopulateAssignedCourseData` 에 빈 컬렉션을 제공 하기 위해 선택 하지만 courses 있을 수 있기 때문에 하지 메서드는 `foreach` (그렇지 않은 경우 코드 보기는는 null 참조 예외를 throw) 보기에서 루프입니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-258">The HttpGet `Create` method calls the `PopulateAssignedCourseData` method not because there might be courses selected but in order to provide an empty collection for the `foreach` loop in the view (otherwise the view code would throw a null reference exception).</span></span>

<span data-ttu-id="e0628-259">HttpPost `Create` 메서드 추가 각 선택한 과정에 참여 하는 `CourseAssignments` 유효성 검사 오류를 검사 하 고 데이터베이스에 새 강사를 추가 하기 전에 탐색 속성입니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-259">The HttpPost `Create` method adds each selected course to the `CourseAssignments` navigation property before it checks for validation errors and adds the new instructor to the database.</span></span> <span data-ttu-id="e0628-260">한 과정 선택 내용을 자동으로 복원 됩니다 (예:는 잘못 된 날짜는 키가 지정 된 사용자), 모델 오류가 때 페이지는 오류 메시지가 다시 표시 되도록 모델 오류가 있는 경우에 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-260">Courses are added even if there are model errors so that when there are model errors (for an example, the user keyed an invalid date), and the page is redisplayed with an error message, any course selections that were made are automatically restored.</span></span>

<span data-ttu-id="e0628-261">강좌를 추가 하려면 다음에 유의 `CourseAssignments` 탐색 속성은 빈 컬렉션으로 속성을 초기화 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-261">Notice that in order to be able to add courses to the `CourseAssignments` navigation property you have to initialize the property as an empty collection:</span></span>

```csharp
instructor.CourseAssignments = new List<CourseAssignment>();
```

<span data-ttu-id="e0628-262">컨트롤러 코드에서이 작업을 수행 하는 대신, 자동으로 컬렉션을 만드는 존재 하지 않는 경우 다음 예제와 같이 getter 속성을 변경 하 여 강사 모델에서 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-262">As an alternative to doing this in controller code, you could do it in the Instructor model by changing the property getter to automatically create the collection if it doesn't exist, as shown in the following example:</span></span>

```csharp
private ICollection<CourseAssignment> _courseAssignments;
public ICollection<CourseAssignment> CourseAssignments
{
    get
    {
        return _courseAssignments ?? (_courseAssignments = new List<CourseAssignment>());
    }
    set
    {
        _courseAssignments = value;
    }
}
```

<span data-ttu-id="e0628-263">수정 하는 경우는 `CourseAssignments` 속성 이러한 방식으로 컨트롤러에 명시적 속성이 초기화 코드를 제거할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-263">If you modify the `CourseAssignments` property in this way, you can remove the explicit property initialization code in the controller.</span></span>

<span data-ttu-id="e0628-264">*Views/Instructor/Create.cshtml*, 사무실 위치 입력란을 추가 하 고 전송 단추 하기 전에 교육 과정에 대 한 확인란을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-264">In *Views/Instructor/Create.cshtml*, add an office location text box and check boxes for courses before the Submit button.</span></span> <span data-ttu-id="e0628-265">편집 페이지의 경우 처럼 [Visual Studio 붙여넣을 때 코드 서식을 다시 지정 하는 경우 서식을 수정](#notepad)합니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-265">As in the case of the Edit page, [fix the formatting if Visual Studio reformats the code when you paste it](#notepad).</span></span>

<span data-ttu-id="e0628-266">[!code-html[Main](intro/samples/cu/Views/Instructors/Create.cshtml?range=29-61)]</span><span class="sxs-lookup"><span data-stu-id="e0628-266">[!code-html[Main](intro/samples/cu/Views/Instructors/Create.cshtml?range=29-61)]</span></span>

<span data-ttu-id="e0628-267">테스트를 실행 하 여는 **만들기** 페이지와 강사를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-267">Test by running the **Create** page and adding an instructor.</span></span> 

## <a name="handling-transactions"></a><span data-ttu-id="e0628-268">트랜잭션 처리</span><span class="sxs-lookup"><span data-stu-id="e0628-268">Handling Transactions</span></span>

<span data-ttu-id="e0628-269">에 설명 된 대로 [CRUD 자습서](crud.md), Entity Framework에는 암시적으로 트랜잭션을 구현 합니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-269">As explained in the [CRUD tutorial](crud.md), the Entity Framework implicitly implements transactions.</span></span> <span data-ttu-id="e0628-270">여기서 필요한 세부적으로 제어할 수-예를 들어 트랜잭션에서-Entity Framework 밖에 서 수행 하는 작업을 포함 하도록 하려는 경우 시나리오 참조 [트랜잭션을](https://docs.microsoft.com/ef/core/saving/transactions)합니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-270">For scenarios where you need more control -- for example, if you want to include operations done outside of Entity Framework in a transaction -- see [Transactions](https://docs.microsoft.com/ef/core/saving/transactions).</span></span>

## <a name="summary"></a><span data-ttu-id="e0628-271">요약</span><span class="sxs-lookup"><span data-stu-id="e0628-271">Summary</span></span>

<span data-ttu-id="e0628-272">이제 관련된 데이터 사용 소개를 완료 했습니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-272">You have now completed the introduction to working with related data.</span></span> <span data-ttu-id="e0628-273">다음 자습서에서는 동시성 충돌을 처리 하는 방법을 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e0628-273">In the next tutorial you'll see how to handle concurrency conflicts.</span></span>

>[!div class="step-by-step"]
<span data-ttu-id="e0628-274">[이전](read-related-data.md)
[다음](concurrency.md)</span><span class="sxs-lookup"><span data-stu-id="e0628-274">[Previous](read-related-data.md)
[Next](concurrency.md)</span></span>  
