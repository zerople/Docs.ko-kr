---
title: "ASP.NET Core MVC EF 코어-관련된 데이터-7/10 업데이트 | Microsoft 문서"
author: tdykstra
description: "이 자습서에서는 외래 키 필드와 탐색 속성을 업데이트 하 여 관련된 데이터를 업데이트 합니다."
keywords: "ASP.NET Core, Entity Framework Core 관련된 데이터를 조인"
ms.author: tdykstra
manager: wpickett
ms.date: 03/15/2017
ms.topic: article
ms.assetid: 67bd162b-bfb7-4750-9e7f-705228b5288c
ms.technology: aspnet
ms.prod: asp.net-core
uid: data/ef-mvc/update-related-data
translationtype: Machine Translation
ms.sourcegitcommit: 115a74c97de6052ec707ee164641f9c41224b9b3
ms.openlocfilehash: 59419e701eff667f91a523e4a6a87d723de0c3ba
ms.lasthandoff: 03/23/2017

---

# <a name="updating-related-data---ef-core-with-aspnet-core-mvc-tutorial-7-of-10"></a>관련된 데이터-ASP.NET 핵심 MVC 자습서 (7 ~ 10 개)를 사용 하는 EF 핵심 업데이트

여 [Tom Dykstra](https://github.com/tdykstra) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)

Contoso University 샘플 웹 응용 프로그램에는 Entity Framework 코어 1.1 및 Visual Studio 2017를 사용 하 여 ASP.NET 핵심 1.1 MVC 웹 응용 프로그램을 만드는 방법을 보여 줍니다. 자습서 시리즈에 대 한 정보를 참조 하십시오. [시리즈의 첫 번째 자습서](intro.md)합니다.

이전 자습서에서 있습니다 표시 되는 관련된 데이터입니다. 이 자습서에서는 외래 키 필드와 탐색 속성을 업데이트 하 여 관련된 데이터를 업데이트 합니다.

다음 그림은 사용 하는 페이지의 일부를 보여 줍니다.

![코스 편집 페이지](update-related-data/_static/course-edit.png)

![강사 편집 페이지](update-related-data/_static/instructor-edit-courses.png)

## <a name="customize-the-create-and-edit-pages-for-courses"></a>코스에 대 한 만들기 및 편집 페이지를 사용자 지정

새 코스 엔터티를 만들 때 기존는 부서에 대 한 관계를 있어야 합니다. 이 작업을 위해 스 캐 폴드 된 코드에는 컨트롤러 메서드 및 부서를 선택 하기 위한 드롭 다운 목록을 포함 하는 뷰 만들기 및 편집 포함 됩니다. 드롭다운 목록에서 집합의 `Course.DepartmentID` 외래 키 속성 및 Entity Framework를 로드 하는 데 필요한 모든는 `Department` 탐색 속성을 적절 한 부서 엔터티. 스 캐 폴드 된 코드를 사용 하지만 오류 처리를 추가 하 고 드롭 다운 목록을 정렬 하도록 약간 변경할 수 있습니다.

*CoursesController.cs*, 만들기 및 편집 메서드&4; 개를 삭제 하 고 다음 코드로 바꿉니다.

[!code-csharp[주](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_CreateGet)]

[!code-csharp[주](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_CreatePost)]

[!code-csharp[주](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_EditGet)]

[!code-csharp[주](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_EditPost)]

이후에 `Edit` HttpPost 메서드의 드롭 다운 목록에 대 한 부서 정보를 로드 하는 새 메서드를 만듭니다.

[!code-csharp[주](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_Departments)]

`PopulateDepartmentsDropDownList` 메서드 이름을 기준으로 정렬 하는 모든 부서의 목록을 가져옵니다, 만듭니다는 `SelectList` 드롭 다운 목록에 대 한 컬렉션 보기에 컬렉션을 전달 하 고 `ViewBag`합니다. 메서드에 선택적 `selectedDepartment` 드롭 다운 목록에서 렌더링 될 때 선택 항목을 지정 하려면 호출 코드를 허용 하는 매개 변수입니다. 보기는 이름을 전달 하는 "DepartmentID"는 `<select>` 태그 도우미와 도우미 다음 찾는 위치를 알고는 `ViewBag` 개체에 대 한는 `SelectList` "DepartmentID" 라는 합니다.

HttpGet `Create` 메서드 호출의 `PopulateDepartmentsDropDownList` 새 강의 대 한 부서에서 설정 되지 않으며 아직 때문에 선택한 항목을 설정 하지 않고 메서드:

[!code-csharp[주](intro/samples/cu/Controllers/CoursesController.cs?highlight=3&name=snippet_CreateGet)]

HttpGet `Edit` 메서드를 편집 하 고이 과정에 이미 할당 되어 있는 부서 ID에 따라 선택한 항목을 설정 합니다.

[!code-csharp[주](intro/samples/cu/Controllers/CoursesController.cs?highlight=15&name=snippet_EditGet)]

HttpPost 메서드 모두에 대 한 `Create` 및 `Edit` 오류가 발생 한 후 페이지를 다시 표시 될 때 선택한 항목을 설정 하는 코드를 포함할 수도 있습니다. 이렇게 하면는 오류 메시지를 표시 하는 페이지를 다시 표시 됩니다 때 어떤 부서에서 선택 된 선택 된 상태로 유지 합니다.

### <a name="add-asnotracking-to-details-and-delete-methods"></a>추가 합니다. 세부 정보를 AsNoTracking 및 Delete 메서드

과정 정보 및 삭제 페이지의 성능을 최적화 하기 위해 추가 `AsNoTracking` 에서 호출 된 `Details` 및 HttpGet `Delete` 메서드.

[!code-csharp[주](intro/samples/cu/Controllers/CoursesController.cs?highlight=10&name=snippet_Details)]

[!code-csharp[주](intro/samples/cu/Controllers/CoursesController.cs?highlight=10&name=snippet_DeleteGet)]

### <a name="modify-the-course-views"></a>코스 뷰 수정

*Views/Courses/Create.cshtml*, "부서 선택" 옵션을 추가 하는 **부서** 드롭 다운 목록에서 필드에 대 한 캡션을 변경 및 **DepartmentID** 를 **부서**합니다.

[!code-html[주](intro/samples/cu/Views/Courses/Create.cshtml?highlight=2,4,5,6&range=35-43)]

*Views/Courses/Edit.cshtml*를 동일 하 게 변경 하 던 것에서 Department 필드에 대 한 *Create.cshtml*합니다.

또한 *Views/Courses/Edit.cshtml*, 크레딧 필드 앞 코스 숫자 필드를 추가 합니다. 기본 키 이기 때문에 표시 되지만 변경할 수 없습니다.

[!code-html[주](intro/samples/cu/Views/Courses/Edit.cshtml?range=15-20)]

숨겨진된 필드는 이미 (`<input type="hidden">`) 편집 보기의 강좌 번호입니다. 추가 `<label>` 태그 도우미 하지 않아도 숨겨진된 필드에 대 한 사용자가 게시 된 데이터에 포함 될 강좌 번호를 발생 시 키 하지 않습니다 **저장** 에 **편집** 페이지입니다.

*Views/Courses/Delete.cshtml*, 맨 위에 있는 코스 숫자 필드를 추가 하 고 부서 ID 부서 이름으로 변경 합니다.

[!code-html[주](intro/samples/cu/Views/Courses/Delete.cshtml?highlight=14-19,36)]

*Views/Course/Details.cshtml*를 동일 하 게 변경 하 던 것에 대 한 *Delete.cshtml*합니다.

### <a name="test-the-course-pages"></a>테스트 과정 페이지

실행은 **만들기** 페이지 (코스 인덱스 페이지를 표시 하 고 클릭 **새로 만들기**) 새 강의 대 한 데이터를 입력 합니다.

![강좌 만들기 페이지](update-related-data/_static/course-create.png)


              **만들기**를 클릭합니다. 목록에 추가 하는 새 코스 과정 인덱스 페이지가 표시 됩니다. 인덱스 페이지 목록에서 부서 이름을 관계가 올바르게 설정 되었는지 표시 하는 탐색 속성에서 가져옵니다.

실행 된 **편집** 페이지 (클릭 **편집** 코스 인덱스 페이지에 제공 하는 과정에서).

![코스 편집 페이지](update-related-data/_static/course-edit.png)

페이지에서 데이터를 변경 하 고 클릭 **저장**합니다. 업데이트 된 과정 데이터와 함께 코스 인덱스 페이지가 표시 됩니다.

## <a name="add-an-edit-page-for-instructors"></a>강사에 대 한 편집 페이지를 추가 합니다.

강사 레코드를 편집할 때 강사의 사무실 할당을 업데이트할 수 하려고 합니다. Instructor 엔터티 즉, 코드는 다음과 같은 경우를 처리 하기에 OfficeAssignment 엔터티&0; 또는&1;에 한 관계가:

* 사용자 선택을 사무실 할당을 취소 하는 경우 원래 값 OfficeAssignment 엔터티를 삭제 합니다.

* 사용자가 office 할당 값을 입력 하 고 원래 비어 있던 경우 새 OfficeAssignment 엔터티를 만듭니다.

* 사용자의 사무실 할당 값을 변경 해도 기존 OfficeAssignment 엔터티 값을 변경 합니다.

### <a name="update-the-instructors-controller"></a>강사 컨트롤러 업데이트

*InstructorsController.cs*는 HttpGet의 코드 변경 `Edit` Instructor 엔터티를 로드 한다는 메서드 `OfficeAssignment` 탐색 속성 및 호출 `AsNoTracking`:

[!code-csharp[주](intro/samples/cu/Controllers/InstructorsController.cs?highlight=9,10&name=snippet_EditGetOA)]

HttpPost 바꿉니다 `Edit` 메서드를 다음 코드로 office 할당 업데이트를 처리 하도록 합니다.

[!code-csharp[주](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_EditPostOA)]

코드는 다음을 수행 합니다.

-  에 메서드 이름을 변경 하는 `EditPost` 서명을 이제는 HttpGet와 동일 하기 때문에 `Edit` 메서드 (의 `ActionName` 특성을 지정 하는 `/Edit/` URL은 여전히 사용 됩니다).

-  선행 로딩에 대 한 사용 하 여 데이터베이스에서 현재 Instructor 엔터티를 가져옵니다는 `OfficeAssignment` 탐색 속성입니다. 이는 HttpGet에서 수행한 것과 동일 `Edit` 메서드.

-  모델 바인더를 값으로 검색 된 Instructor 엔터티를 업데이트합니다. `TryUpdateModel` 오버 로드를 사용 하면 허용 목록에 포함 하려는 속성입니다. 이렇게 하면 과도 하 게 게시에 설명 된 대로 [두 번째 자습서](crud.md)합니다.

    <!-- Snippets do not play well with <ul> [!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?range=241-244)] -->

    ```csharp
    if (await TryUpdateModelAsync<Instructor>(
        instructorToUpdate,
        "",
        i => i.FirstMidName, i => i.LastName, i => i.HireDate, i => i.OfficeAssignment))
    ```
    
-   사무실 위치 비어 있으면 Instructor.OfficeAssignment 속성을 OfficeAssignment 테이블의 관련된 행이 삭제 됩니다 있도록를 null로 설정 합니다.

    <!-- Snippets do not play well with <ul>  "intro/samples/cu/Controllers/InstructorsController.cs"} -->

    ```csharp
    if (String.IsNullOrWhiteSpace(instructorToUpdate.OfficeAssignment?.Location))
    {
        instructorToUpdate.OfficeAssignment = null;
    }
    ```

- 데이터베이스에 변경 내용을 저장 합니다.

### <a name="update-the-instructor-edit-view"></a>강사 편집 뷰 업데이트

*Views/Instructors/Edit.cshtml*, 사무실 위치 하기 전에 끝에 편집을 위해 새 필드를 추가 하는 **저장** 단추:

[!code-html[주](intro/samples/cu/Views/Instructors/Edit.cshtml?range=36-42)]

페이지를 실행 (선택 된 **강사** 탭을 클릭 한 다음 **편집** 강사에). 변경 된 **사무실 위치** 클릭 **저장**합니다.

![강사 편집 페이지](update-related-data/_static/instructor-edit-office.png)

## <a name="add-course-assignments-to-the-instructor-edit-page"></a>강사 편집 페이지를 코스 할당을 추가 합니다.

강사에 대해서는 과정을 개수에 관계 없이 설명 될 수 있습니다. 이제 다음 스크린 샷과 같이 확인란의 그룹을 사용 하 여 코스 할당을 변경 하는 기능을 추가 하 여 강사 편집 페이지를 개선 하 합니다.

![과정을 통해 강사 편집 페이지](update-related-data/_static/instructor-edit-courses.png)

코스 강사로 활동 하 고 엔터티 간의 관계는 다 대 다입니다. 추가 하 고 관계를 제거, 추가 및 CourseAssignments 조인 엔터티 집합에서 엔터티를 제거 합니다.

어떤 과정을 변경할 수 있도록 하는 UI 강사는은 확인란의 그룹에 할당 합니다. 데이터베이스의 모든 과정에 대 한 확인란이 표시 되 고, 강의에 현재 할당 되어 있는 것과 선택 됩니다. 사용자가 선택 하거나 코스 할당을 변경 하려면 확인란의 선택을 취소 합니다. 과정 수가 훨씬 더 클 경우 뷰에서 데이터를 표시 합니다. 다른 방법을 사용 하 여 원할 것 하지만 관계 만들기 또는 삭제 하는 조인 엔터티를 조작 하는 동일한 메서드를 사용 합니다.

### <a name="update-the-instructors-controller"></a>강사 컨트롤러 업데이트

확인란 목록 보기에는 데이터를 제공 하는 뷰 모델 클래스를 사용 합니다.

만들 *AssignedCourseData.cs* 에 *SchoolViewModels* 폴더와 기존 코드를 다음 코드로 바꿉니다.

[!code-csharp[주](intro/samples/cu/Models/SchoolViewModels/AssignedCourseData.cs)]

*InstructorsController.cs*, 교체는 HttpGet `Edit` 메서드를 다음 코드로 바꿉니다. 변경 내용은 강조 표시 됩니다.

[!code-csharp[주](intro/samples/cu/Controllers/InstructorsController.cs?highlight=10,17,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36&name=snippet_EditGetCourses)]

코드에 대 한 선행 로딩에 추가 하는 `Courses` 탐색 속성 새 호출 `PopulateAssignedCourseData` 메서드를 사용 하 여 확인란 배열에 대 한 정보를 제공는 `AssignedCourseData` 모델 클래스를 확인 합니다.

코드는 `PopulateAssignedCourseData` 뷰 모델 클래스를 사용 하는 과정의 목록을 로드 하기 위해 모든 코스 엔터티를 통해 메서드를 읽습니다. 각 코스에 대 한 코드 확인 과정을 강의에 있는지 여부를 `Courses` 탐색 속성입니다. 효율적인 조회를 제공 하는 과정에서 강사에 할당 되었는지 여부를 확인할 때 만들려면 강의에 할당 하는 과정에 포함 되는 `HashSet` 컬렉션입니다. `Assigned` 속성이 true 코스에 대 한 강의에 할당 됩니다. 보기 선택으로 상자를 표시 해야 하는 확인을 확인 하려면이 속성을 사용 합니다. 목록 보기에 전달 되는 마지막으로, `ViewData`합니다.

다음으로, 사용자가 클릭할 때 실행 되는 코드를 추가 **저장**합니다. 대체는 `EditPost` 메서드를 다음 코드를 업데이트 하는 새 메서드를 추가 하는 `Courses` 강사 엔터티의 탐색 속성입니다.

[!code-csharp[주](intro/samples/cu/Controllers/InstructorsController.cs?highlight=3,12,13,25&name=snippet_EditPostCourses)]

[!code-csharp[주](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_UpdateCourses&highlight=1-31)]

메서드 시그니처는 HttpGet와에서 다르면 이제 `Edit` 에서 메서드 이름을 변경 하므로 `EditPost` 다시 `Edit`합니다.

모델 바인더 보기 Course 엔터티 컬렉션을가 자동으로 업데이트할 수는 `CourseAssignments` 탐색 속성입니다. 모델 바인더를 사용 하 여 업데이트 대신는 `CourseAssignments` 탐색 속성이 새 그렇게 `UpdateInstructorCourses` 메서드. 따라서 제외 해야는 `CourseAssignments` 을 모델 바인딩에서 속성입니다. 호출 하는 코드를 변경 하지 않아도이 `TryUpdateModel` whitelisting 오버 로드를 사용 중 이므로 및 `CourseAssignments` 포함 목록에 있지 않습니다.

경우 확인 표시가 나타나지 상자가 선택 된 코드에서는 `UpdateInstructorCourses` 를 초기화는 `CourseAssignments` 탐색 속성을 빈 컬렉션을 반환 합니다.

[!code-csharp[주](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_UpdateCourses&highlight=3-7)]

코드 다음 데이터베이스의 모든 과정을 반복 하 고 현재 보기에서 선택 된 것과 강사에 할당 하는 것에 대해 각 코스 확인. 효율적인 조회를 쉽게 후자의 두 컬렉션에 저장 된 `HashSet` 개체입니다.

제공 하는 과정에 대 한 확인란을 선택한 있지만 과정에 포함 되지 않은 경우는 `Instructor.CourseAssignments` 탐색 속성이 코스 컬렉션 탐색 속성에 추가 됩니다.

[!code-csharp[주](intro/samples/cu/Controllers/InstructorsController.cs?highlight=14-20&name=snippet_UpdateCourses)]

제공 하는 과정에 대 한 확인란이 선택 되지 않은 과정에서 하는 경우는 `Instructor.CourseAssignments` 코스 탐색 속성이 탐색 속성에서 제거 됩니다.

[!code-csharp[주](intro/samples/cu/Controllers/InstructorsController.cs?highlight=21-29&name=snippet_UpdateCourses)]

### <a name="update-the-instructor-views"></a>강사 뷰 업데이트

*Views/Instructors/Edit.cshtml*, 추가 **과정** 다음 추가 하 여 확인란의 배열을 사용 하 여 필드 바로 다음 코드는 `div` 요소에 대 한는 **Office** 필드 하기 전에 `div` 에 대 한 요소는 **저장** 단추 합니다.

> [!NOTE] 
> 이 이렇게 하려면 메모장과 같은 텍스트 편집기에서 파일을 엽니다.  Visual Studio를 사용 하는 경우 줄 바꿈 코드를 중단 하는 방식에서 변경 됩니다.  이런 경우, 여기 나오는 것과 같이 줄 바꿈을 수정 합니다. 들여쓰기 완벽 하지 않아도 되지만 `@</tr><tr>`, `@:<td>`, `@:</td>`, 및 `@:</tr>` 줄 각각 한 줄에 표시 된 것 처럼 이거나 런타임 오류가 발생 합니다. 텍스트 편집기에서 파일을 편집한 후 Visual Studio에서 열, 새 코드 블록을 강조 표시 및 tab 키를 눌러 기존 코드와 함께 새 코드 줄을 두 번 수 있습니다.

[!code-html[주](intro/samples/cu/Views/Instructors/Edit.cshtml?range=43-69)]

이 코드는 세 개의 열이 있는 HTML 테이블을 만듭니다. 각 열에는 확인란이 강좌 번호 및 제목으로 구성 된 캡션 옵니다. 모든 확인란 이름이 같은 ("selectedCourses")를 알리는 모델 바인더는 그룹으로 간주 됩니다. 각 확인란의 값 특성의 값으로 설정 되어 `CourseID`합니다. 모델 바인더 컨트롤러 구성 된 배열을 전달 페이지가 게시 되는 경우는 `CourseID` 확인란만 선택 된 값입니다.

확인란 처음 렌더링 되 면 (에 확인 표시)을 선택 하는 특성을 확인 한 강의에 할당 하는 과정에 사용 되는 것입니다.

강사 인덱스 페이지를 실행 하 고 클릭 **편집** 보려면 강사에는 **편집** 페이지입니다.

![과정을 통해 강사 편집 페이지](update-related-data/_static/instructor-edit-courses.png)

일부 코스 할당을 변경 하 고 저장을 클릭 합니다. 인덱스 페이지에서 수행한 변경 내용은 반영 됩니다.

> [!NOTE] 
> 강사 코스 데이터를 편집 하려면 여기를 수행 하는 방법은 코스 수가 제한 되는 경우에 작동 합니다. 훨씬 큰 경우에 컬렉션의 경우 서로 다른 UI와 다른 업데이트 메서드는 필요 하지 않습니다.

## <a name="update-the-delete-page"></a>삭제 페이지를 업데이트 합니다.

*InstructorsController.cs*, 삭제 된 `DeleteConfirmed` 그 자리에 다음 코드 메서드 및 삽입 합니다.

[!code-csharp[주](intro/samples/cu/Controllers/InstructorsController.cs?highlight=5-7,9-12&name=snippet_DeleteConfirmed)]

이 코드에서는 다음과 같이 변경 합니다.

* 선행 로딩은 `CourseAssignments` 탐색 속성입니다.  이 포함 해야 또는 관련 작업에 대 한 EF 알지 `CourseAssignment` 엔터티 하 고 삭제 하지 않습니다.  읽이 필요가 없도록 하려면 여기를 구성할 수 있습니다 하위 삭제를 데이터베이스입니다.

* 삭제할 강사의 부서 관리자로 할당 되 면 해당 부서에서 강사 할당을 제거 합니다.

## <a name="add-office-location-and-courses-to-the-create-page"></a>사무실 위치 및 과정 만들기 페이지에 추가

*InstructorController.cs*, HttpGet 및 HttpPost 삭제 `Create` 메서드를 그 자리에 다음 코드를 추가 합니다.

[!code-csharp[주](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_Create&highlight=3-5,12,14-22)]

이 코드에 대 한 확인 했던 것과 비슷합니다는 `Edit` 방법이 없는 과정 처음에 제외 하 고 선택 됩니다. HttpGet `Create` 메서드 호출의 `PopulateAssignedCourseData` 에 빈 컬렉션을 제공 하기 위해 하지만 선택 과정 있을 수 있기 때문에 하지 메서드는 `foreach` (그렇지 않은 경우 보기 코드는 예외를 throw null 참조) 보기에는 루프입니다.

HttpPost `Create` 메서드를 추가 하는 각 선택한 과정의 `CourseAssignments` 탐색 속성 유효성 검사 오류를 확인 하 고 데이터베이스에 새 강의 추가 합니다. 코스를 수행한 코스 선택 내용을 자동으로 복원 됩니다 (예:는 잘못 된 날짜는 키가 지정 된 사용자), 모델 오류가 페이지는 오류 메시지가 다시 표시 하는 경우 모델 오류가 있는 경우에 추가 됩니다.

과정을 추가할 수 있도록 표시 되는 `CourseAssignments` 빈 컬렉션으로 속성을 초기화 해야 하는 탐색 속성:

```csharp
instructor.CourseAssignments = new List<CourseAssignment>();
```

컨트롤러 코드에이 작업을 수행 하는 대신, 자동으로 컬렉션을 만드는 존재 하지 않는 경우 다음 예제와 같이 getter 속성을 변경 하 여 강사 모델에서 수행할 수 있습니다.

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

수정 하는 경우는 `CourseAssignments` 속성 이러한 방식으로 컨트롤러에 명시적 속성 초기화 코드를 제거할 수 있습니다.

*Views/Instructor/Create.cshtml*, 사무실 위치 입력란을 추가 하 고 전송 단추 하기 전에 코스에 대 한 확인란을 선택 합니다. 편집 페이지의 경우와 같이이 효율성도 뛰어납니다 메모장과 같은 텍스트 편집기에서 수행 하는 경우.

[!code-html[주](intro/samples/cu/Views/Instructors/Create.cshtml?range=35-69)]

또한 *Create.cshtml*는 스 캐 폴더에 배치 하는 고용 날짜 필드 이름 및 성 필드 사이 알 수 있습니다. 원하는 경우 해당 필드를 논리적인 순서로 재정렬할 수 있습니다.  동일한 문제에 Edit, Details, 필드 순서와 적용 하 고 페이지를 삭제 합니다.

테스트를 실행 하 여는 **만들기** 페이지 및 강사를 추가 합니다. 

## <a name="handling-transactions"></a>트랜잭션 처리

에 설명 된 대로 [CRUD 자습서](crud.md), Entity Framework에는 암시적으로 트랜잭션을 구현 합니다. 여기서 필요한 세부적으로 제어할 수-예를 들어 트랜잭션에-Entity Framework 외부에서 수행 하는 작업을 포함 하도록 하려는 경우 시나리오 참조 [트랜잭션](https://docs.microsoft.com/ef/core/saving/transactions)합니다.

## <a name="summary"></a>요약

이제 데이터 관련된 작업에 대 한 소개를 완료 했습니다. 다음 자습서에서는 동시성 충돌을 처리 하는 방법을 알아보겠습니다.

>[!div class="step-by-step"]
[이전](read-related-data.md)
[다음](concurrency.md)  

