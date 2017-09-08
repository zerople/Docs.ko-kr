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
# <a name="updating-related-data---ef-core-with-aspnet-core-mvc-tutorial-7-of-10"></a>관련된 데이터-ASP.NET Core MVC 자습서 (7 / 10)와 함께 EF 코어 업데이트

여 [Tom Dykstra](https://github.com/tdykstra) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)

Contoso 대학 샘플 웹 응용 프로그램에는 Entity Framework Core 및 Visual Studio를 사용 하 여 ASP.NET Core MVC 웹 응용 프로그램을 만드는 방법을 보여 줍니다. 자습서 시리즈에 대 한 정보를 참조 하십시오. [시리즈의 첫 번째 자습서](intro.md)합니다.

관련된 데이터; 이전 자습서에서 표시 이 자습서에서는 외래 키 필드와 탐색 속성을 업데이트 하 여 관련된 데이터를 업데이트 합니다.

다음 그림은 사용 하는 페이지의 일부를 보여 줍니다.

![과정 편집 페이지](update-related-data/_static/course-edit.png)

![강사 편집 페이지](update-related-data/_static/instructor-edit-courses.png)

## <a name="customize-the-create-and-edit-pages-for-courses"></a>교육 과정에 대 한 만들기 및 편집 페이지를 사용자 지정

새 과목 엔터티를 만들 때 기존 부서에는 관계가 있어야 합니다. 이 작업을 위해 스 캐 폴드 코드에는 컨트롤러 메서드 및 부서를 선택 하기 위한 드롭 다운 목록을 포함 하는 뷰 만들기 및 편집 포함 됩니다. 드롭다운 목록에서 집합은 `Course.DepartmentID` 외래 키 속성 및 Entity Framework를 로드 하는 데 필요한 모든는 `Department` 적절 한 부서 엔터티 된 탐색 속성입니다. 스 캐 폴드 코드를 사용 하지만 오류 처리를 추가 하 고 드롭 다운 목록을 정렬 하도록 약간 변경 됩니다.

*CoursesController.cs*, 4 개의 만들기 및 편집 메서드를 삭제 하 고 다음 코드로 바꿉니다.

[!code-csharp[Main](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_CreateGet)]

[!code-csharp[Main](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_CreatePost)]

[!code-csharp[Main](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_EditGet)]

[!code-csharp[Main](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_EditPost)]

이후에 `Edit` HttpPost 메서드 드롭 다운 목록에 대 한 부서 정보를 로드 하는 새 메서드를 만듭니다.

[!code-csharp[Main](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_Departments)]

`PopulateDepartmentsDropDownList` 메서드 이름을 기준으로 정렬 하는 모든 부서의 목록을 가져옵니다, 만듭니다는 `SelectList` 드롭 다운 목록에 대 한 컬렉션 뷰 컬렉션을 전달 하 고 `ViewBag`합니다. 메서드에 선택적 `selectedDepartment` 드롭 다운 목록에서 렌더링 될 때 선택 항목을 지정 하는 호출 코드를 허용 하는 매개 변수입니다. 보기에 이름이 "DepartmentID"를 전달 하는 `<select>` 태그 도우미와 도우미를 찾는 다음 알고는 `ViewBag` 개체에 대 한는 `SelectList` "DepartmentID" 라는 합니다.

HttpGet `Create` 메서드 호출의 `PopulateDepartmentsDropDownList` 새 과정에 대 한 부서 설정 되지 않으며 아직 때문에 선택한 항목을 설정 하지 않고 메서드:

[!code-csharp[Main](intro/samples/cu/Controllers/CoursesController.cs?highlight=3&name=snippet_CreateGet)]

HttpGet `Edit` 메서드 편집 중인 과정에 이미 할당 되어 있는 분야의 ID에 따라 선택한 항목을 설정 합니다.

[!code-csharp[Main](intro/samples/cu/Controllers/CoursesController.cs?highlight=15&name=snippet_EditGet)]

둘 다에 대해 HttpPost 메서드 `Create` 및 `Edit` 도 오류가 발생 한 후 페이지를 다시 표시 될 때 선택한 항목을 설정 하는 코드를 포함 합니다. 이렇게 하면는 오류 메시지를 표시 하기 페이지 다시 표시 됩니다 때 어떤 부서 선택한 선택 된 상태로 유지 합니다.

### <a name="add-asnotracking-to-details-and-delete-methods"></a>추가 합니다. 세부 정보를 AsNoTracking 및 Delete 메서드

강좌 세부 정보 및 Delete 페이지의 성능을 최적화 하기 위해 추가 `AsNoTracking` 에서 호출 된 `Details` 및 HttpGet `Delete` 메서드.

[!code-csharp[Main](intro/samples/cu/Controllers/CoursesController.cs?highlight=10&name=snippet_Details)]

[!code-csharp[Main](intro/samples/cu/Controllers/CoursesController.cs?highlight=10&name=snippet_DeleteGet)]

### <a name="modify-the-course-views"></a>과정 뷰 수정

*Views/Courses/Create.cshtml*, "부서 선택" 옵션을 추가 하는 **부서** 드롭 다운 목록에서에서 캡션은 변경할 **DepartmentID** 를  **부서**, 유효성 검사 메시지를 추가 합니다.

[!code-html[Main](intro/samples/cu/Views/Courses/Create.cshtml?highlight=2-6&range=29-34)]

*Views/Courses/Edit.cshtml*, 방금에서 수행한 부서 필드에 대 한 동일한 변경 내용을 *Create.cshtml*합니다.

또한 *Views/Courses/Edit.cshtml*, 추가 하기 전에 과정 숫자 필드는 **제목** 필드입니다. 과정 수의 기본 키 이기 때문에 표시 될 있지만 변경할 수 없습니다.

[!code-html[Main](intro/samples/cu/Views/Courses/Edit.cshtml?range=15-18)]

이미 숨겨진된 필드 (`<input type="hidden">`) 편집 보기에서 과정 번호에 대 한 합니다. 추가 `<label>` 태그 도우미를 클릭할 때 게시 된 데이터에 포함 되도록 과정 번호를 발생 하지 않는 되었기 때문에 숨겨진된 필드에 대 한 필요성을 제거 하지 않습니다 **저장** 에 **편집** 페이지.

*Views/Courses/Delete.cshtml*, 위쪽에서 과정 숫자 필드를 추가 하 고 부서 ID 부서 이름으로 변경 합니다.

[!code-html[Main](intro/samples/cu/Views/Courses/Delete.cshtml?highlight=14-19,36)]

*Views/Courses/Details.cshtml*, 수행한에 대 한 동일한 변경 내용을 *Delete.cshtml*합니다.

### <a name="test-the-course-pages"></a>테스트 과정 페이지

실행의 **만들기** 페이지 (과정 인덱스 페이지를 표시 하 고 클릭 **새로 만들기**) 새 과정에 대 한 데이터를 입력 합니다.

![코스 만들기 페이지](update-related-data/_static/course-create.png)


              **만들기**를 클릭합니다. Courses 인덱스 페이지가 목록에 추가 된 새 과정으로 표시 됩니다. 인덱스 페이지 목록에 있는 부서 이름과 관계가 올바르게 설정 되었는지 표시 하는 탐색 속성에서 제공 됩니다.

실행 된 **편집** 페이지 (클릭 **편집** 과정 인덱스 페이지에는 과정에).

![과정 편집 페이지](update-related-data/_static/course-edit.png)

페이지에서 데이터를 변경 하 고 클릭 **저장**합니다. Courses 인덱스 페이지가 업데이트 과정 데이터와 함께 표시 됩니다.

## <a name="add-an-edit-page-for-instructors"></a>강사에 대 한 편집 페이지를 추가 합니다.

강사 레코드를 편집할 때 강의 사무실 할당을 업데이트할 수 하려고 합니다. Instructor 엔터티 즉, 사용자 코드에 다음과 같은 경우 처리는 OfficeAssignment 엔터티와 0 또는 1을 한 관계에 있습니다.

* 사용자 선택을 사무실 할당을 취소 하는 경우 원래 값을이 OfficeAssignment 엔터티를 삭제 합니다.

* 사용자가 office 할당 값을 입력 하 고 원래 비어 있던 경우 새 OfficeAssignment 엔터티를 만듭니다.

* 사용자가 사무실 할당의 값을 변경 해도 기존 OfficeAssignment 엔터티 값을 변경 합니다.

### <a name="update-the-instructors-controller"></a>강사 컨트롤러를 업데이트 합니다.

*InstructorsController.cs*는 HttpGet의 코드 변경 `Edit` Instructor 엔터티를 로드 한다는 메서드 `OfficeAssignment` 탐색 속성과 호출 `AsNoTracking`:

[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?highlight=9,10&name=snippet_EditGetOA)]

대체는 HttpPost `Edit` 를 다음 코드로 office 할당 업데이트를 처리 하는 메서드:

[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_EditPostOA)]

코드는 다음을 수행합니다.

-  에 메서드 이름을 변경 하는 `EditPost` 서명이 이제는 HttpGet와 동일 하기 때문에 `Edit` 메서드 (는 `ActionName` 특성을 지정 하는 `/Edit/` URL은 여전히 사용).

-  에 대 한 로드 신속 하 게 사용 하 여 데이터베이스에서 현재 Instructor 엔터티를 가져옵니다는 `OfficeAssignment` 탐색 속성입니다. 이는 HttpGet에서 수행한 것과 동일 `Edit` 메서드.

-  모델 바인더의 값으로 검색 된 Instructor 엔터티를 업데이트합니다. `TryUpdateModel` 오버 로드를 사용 하면 허용 목록에 포함 하려는 속성입니다. 이렇게 하면 과도 하 게 게시에 설명 된 대로 [두 번째 자습서](crud.md)합니다.

    <!-- Snippets do not play well with <ul> [!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?range=241-244)] -->

    ```csharp
    if (await TryUpdateModelAsync<Instructor>(
        instructorToUpdate,
        "",
        i => i.FirstMidName, i => i.LastName, i => i.HireDate, i => i.OfficeAssignment))
    ```
    
-   사무실 위치 비어 있으면 OfficeAssignment 테이블의 관련된 행이 삭제 됩니다 되도록 null로 Instructor.OfficeAssignment 속성을 설정 합니다.

    <!-- Snippets do not play well with <ul>  "intro/samples/cu/Controllers/InstructorsController.cs"} -->

    ```csharp
    if (String.IsNullOrWhiteSpace(instructorToUpdate.OfficeAssignment?.Location))
    {
        instructorToUpdate.OfficeAssignment = null;
    }
    ```

- 데이터베이스에 변경 내용을 저장합니다.

### <a name="update-the-instructor-edit-view"></a>강사 편집 뷰 업데이트

*Views/Instructors/Edit.cshtml*, 사무실 위치 하기 전에 끝에 편집 하기 위해 새 필드를 추가 하는 **저장** 단추:

[!code-html[Main](intro/samples/cu/Views/Instructors/Edit.cshtml?range=30-34)]

페이지 실행 (선택 된 **강사** 탭을 클릭 한 다음 **편집** 강사에). 변경 된 **사무실 위치** 클릭 **저장**합니다.

![강사 편집 페이지](update-related-data/_static/instructor-edit-office.png)

## <a name="add-course-assignments-to-the-instructor-edit-page"></a>강사 편집 페이지를 과정 할당을 추가 합니다.

강사는 courses 개수에 관계 없이 지정할 수 있습니다. 이제 다음 스크린 샷에 표시 된 것 처럼 확인란 그룹을 사용 하는 과정 할당을 변경 하는 기능을 추가 하 여 강사 편집 페이지를 향상 시켜:

![Courses와 강사 편집 페이지](update-related-data/_static/instructor-edit-courses.png)

Course 및 Instructor 엔터티 간의 다 대 다입니다. 추가 하 고 관계를 추가 및 CourseAssignments 조인 엔터티 집합에서 엔터티를 제거 합니다.

과정을 변경할 수 있도록 하는 UI 강사는은 확인란의 그룹에 할당 합니다. 데이터베이스의 모든 과정에 대 한 확인란이 표시 되 고 강사에 현재 할당 되어 있는 것을 선택 합니다. 사용자가 선택 하거나 과정 할당을 변경 하려면 확인란의 선택을 취소 합니다. Courses 수가 된 훨씬 큰을 뷰에서 데이터를 제공 합니다. 다른 방법을 사용 하 고 싶을 것 하지만 관계 만들기 또는 삭제 하려면 조인 엔터티를 조작 하는 동일한 메서드를 사용 합니다.

### <a name="update-the-instructors-controller"></a>강사 컨트롤러를 업데이트 합니다.

확인란 목록 보기에는 데이터를 제공 하려면 보기 모델 클래스를 사용 합니다.

만들 *AssignedCourseData.cs* 에 *SchoolViewModels* 폴더와 기존 코드를 다음 코드로 바꿉니다.

[!code-csharp[Main](intro/samples/cu/Models/SchoolViewModels/AssignedCourseData.cs)]

*InstructorsController.cs*, 대체는 HttpGet `Edit` 메서드를 다음 코드로 합니다. 변경 내용은 강조 표시 됩니다.

[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?highlight=10,17,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36&name=snippet_EditGetCourses)]

에 대 한 즉시 로드를 추가 하는 코드는 `Courses` 탐색 속성 새 호출 `PopulateAssignedCourseData` 메서드를 사용 하 여 확인란 배열에 대 한 정보를 제공는 `AssignedCourseData` 모델 클래스를 보고 합니다.

코드는 `PopulateAssignedCourseData` 보기 모델 클래스를 사용 하는 과정의 목록을 로드 하기 위해 모든 과정 엔터티를 통해 메서드를 읽습니다. 각 과정에 대 한 코드 과정 강의에 있는지 여부를 확인 `Courses` 탐색 속성입니다. 효율적인 조회를 만들려면 과정에서 강사에 할당 되었는지 여부를 확인할 때 강사에 할당 하는 과정에 저장 됩니다는 `HashSet` 컬렉션입니다. `Assigned` 속성이로 설정 된 과정에 대해 true 강사에 할당 됩니다. 보기 선택 상자로 표시 되어야 하는 확인을 확인 하려면이 속성을 사용 합니다. 목록 보기에 전달 되는 마지막으로, `ViewData`합니다.

다음으로, 사용자가 클릭할 때 실행 되는 코드를 추가 **저장**합니다. 대체는 `EditPost` 메서드를 다음 코드를 복사해 업데이트 하는 새 메서드 추가 `Courses` Instructor 엔터티의 탐색 속성입니다.

[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?highlight=1,3,12,13,25,39-40&name=snippet_EditPostCourses)]

[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_UpdateCourses&highlight=1-31)]

메서드 시그니처는 HttpGet와에서 다르면 이제 `Edit` 에서 메서드 이름을 변경 하므로 `EditPost` 다시 `Edit`합니다.

모델 바인더를 자동으로 업데이트할 수 없습니다 보기 과정 엔터티 컬렉션에 한는 `CourseAssignments` 탐색 속성입니다. 모델 바인더를 사용 하 여 업데이트 하는 대신는 `CourseAssignments` 탐색 속성을 새 그렇게 `UpdateInstructorCourses` 메서드. 따라서 제외 해야는 `CourseAssignments` 모델 바인딩에서 속성입니다. 호출 하는 코드를 변경 하지 않아도이 `TryUpdateModel` 허용 목록이 오버 로드를 사용 중 이므로 및 `CourseAssignments` include 목록에 있지 않습니다.

경우 없는 확인란이 선택 된을의 코드 `UpdateInstructorCourses` 초기화는 `CourseAssignments` 빈 컬렉션 및 반환 된 탐색 속성:

[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_UpdateCourses&highlight=3-7)]

코드 다음 데이터베이스의 모든 과정을 반복 하 고 각 과목와 보기에서 선택 된 강사에 현재 할당 된 것에 대해 확인 합니다. 두 번째 컬렉션에 저장 된 효율적인 조회를 쉽게 `HashSet` 개체입니다.

과정에 대 한 확인란을 선택 했지만 과정에 없는 경우는 `Instructor.CourseAssignments` 탐색 속성 과정 탐색 속성의 컬렉션에 추가 됩니다.

[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?highlight=14-20&name=snippet_UpdateCourses)]

과정에 대 한 확인란 선택 되지 않은 과정에 속하지만 `Instructor.CourseAssignments` 과정 탐색 속성이 탐색 속성에서 제거 됩니다.

[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?highlight=21-29&name=snippet_UpdateCourses)]

### <a name="update-the-instructor-views"></a>강사 뷰 업데이트

*Views/Instructors/Edit.cshtml*, 추가 **Courses** 을 추가 하 여 확인란의 배열로 필드 바로 다음 코드는 `div` 에 대 한 요소는 **Office**  필드 및 하기 전에 `div` 에 대 한 요소는 **저장** 단추입니다.

<a id="notepad"></a>
> [!NOTE] 
> Visual Studio에서 코드를 붙여 넣을 때 줄 바꿈 코드를 중단 하는 방식으로 변경 됩니다.  자동 서식 지정을 실행 취소 하려면 Ctrl + Z를 한 번 누릅니다.  여기 같이 있도록 줄 바꿈이 수정 합니다. 들여쓰기 완벽 하지 않아도 되지만 `@</tr><tr>`, `@:<td>`, `@:</td>`, 및 `@:</tr>` 줄 각각 한 줄에 표시 된 것 처럼 이거나 런타임 오류 메시지가 나타납니다. 선택 된 새 코드 블록과 Tab 세 번 키를 눌러 줄 기존 코드와 함께 새 코드를 합니다.

[!code-html[Main](intro/samples/cu/Views/Instructors/Edit.cshtml?range=35-61)]

이 코드는 세 개의 열이 있는 HTML 테이블을 만듭니다. 각 열에는 확인란이 과정 번호 및 제목으로 구성 된 캡션 뒤 합니다. 모든 확인란 있어야 그룹으로 처리 해야 하는 모델 바인더에 게 동일한 이름 ("selectedCourses"). 각 확인란의 value 속성의 값으로 설정 되어 `CourseID`합니다. 모델 바인더 구성 된 컨트롤러에 배열을 전달 페이지가 게시 되는 경우는 `CourseID` 확인란만 선택 되에 대 한 값입니다.

확인란은 처음 렌더링 됩니다 때 (에 확인 표시)을 선택 하는 특성을 확인가 강사에 할당 하는 과정에 사용 되는 것입니다.

강사 인덱스 페이지를 실행 하 고 클릭 **편집** 보려면 강사에는 **편집** 페이지.

![Courses와 강사 편집 페이지](update-related-data/_static/instructor-edit-courses.png)

일부 과정 할당을 변경 하 고 저장을 클릭 합니다. 인덱스 페이지에 변경 내용은 반영 됩니다.

> [!NOTE] 
> 강사 과정 데이터를 편집 하려면 여기에 적용 되는 방법을 제한 된 수의 과목 필요한 경우에 작동 합니다. 훨씬 큰 경우에 컬렉션의 경우 다른 UI와 다른 업데이트 방법을 필요한 것입니다.

## <a name="update-the-delete-page"></a>업데이트 페이지 삭제

*InstructorsController.cs*, 삭제는 `DeleteConfirmed` 다음 해당 위치에 코드 메서드 및 삽입 합니다.

[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?highlight=5-7,9-12&name=snippet_DeleteConfirmed)]

이 코드에서는 다음과 같이 변경 합니다.

* 에 대 한 로드는 eager는 `CourseAssignments` 탐색 속성입니다.  이 포함 하거나 관련에 대 한 알 수 없고 EF `CourseAssignment` 엔터티 하 고 삭제 하지 않습니다.  읽이 필요가 없도록 하려면 여기를 구성할 수 있습니다 하위 삭제를 데이터베이스입니다.

* 삭제할 강사 부서의 관리자로 할당 된 경우 해당 부서에서 강사 할당을 제거 합니다.

## <a name="add-office-location-and-courses-to-the-create-page"></a>사무실 위치 및 courses 만들기 페이지에 추가

*InstructorsController.cs*, HttpGet 및 HttpPost 삭제 `Create` 메서드를 그 자리에 다음 코드를 추가 합니다.

[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_Create&highlight=3-5,12,14-22,29)]

이 코드에 대 한 확인 했던 것과 비슷합니다는 `Edit` 방법이 없는 courses 처음에 제외 하 고 선택 됩니다. HttpGet `Create` 메서드 호출의 `PopulateAssignedCourseData` 에 빈 컬렉션을 제공 하기 위해 선택 하지만 courses 있을 수 있기 때문에 하지 메서드는 `foreach` (그렇지 않은 경우 코드 보기는는 null 참조 예외를 throw) 보기에서 루프입니다.

HttpPost `Create` 메서드 추가 각 선택한 과정에 참여 하는 `CourseAssignments` 유효성 검사 오류를 검사 하 고 데이터베이스에 새 강사를 추가 하기 전에 탐색 속성입니다. 한 과정 선택 내용을 자동으로 복원 됩니다 (예:는 잘못 된 날짜는 키가 지정 된 사용자), 모델 오류가 때 페이지는 오류 메시지가 다시 표시 되도록 모델 오류가 있는 경우에 추가 됩니다.

강좌를 추가 하려면 다음에 유의 `CourseAssignments` 탐색 속성은 빈 컬렉션으로 속성을 초기화 해야 합니다.

```csharp
instructor.CourseAssignments = new List<CourseAssignment>();
```

컨트롤러 코드에서이 작업을 수행 하는 대신, 자동으로 컬렉션을 만드는 존재 하지 않는 경우 다음 예제와 같이 getter 속성을 변경 하 여 강사 모델에서 수행할 수 있습니다.

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

수정 하는 경우는 `CourseAssignments` 속성 이러한 방식으로 컨트롤러에 명시적 속성이 초기화 코드를 제거할 수 있습니다.

*Views/Instructor/Create.cshtml*, 사무실 위치 입력란을 추가 하 고 전송 단추 하기 전에 교육 과정에 대 한 확인란을 선택 합니다. 편집 페이지의 경우 처럼 [Visual Studio 붙여넣을 때 코드 서식을 다시 지정 하는 경우 서식을 수정](#notepad)합니다.

[!code-html[Main](intro/samples/cu/Views/Instructors/Create.cshtml?range=29-61)]

테스트를 실행 하 여는 **만들기** 페이지와 강사를 추가 합니다. 

## <a name="handling-transactions"></a>트랜잭션 처리

에 설명 된 대로 [CRUD 자습서](crud.md), Entity Framework에는 암시적으로 트랜잭션을 구현 합니다. 여기서 필요한 세부적으로 제어할 수-예를 들어 트랜잭션에서-Entity Framework 밖에 서 수행 하는 작업을 포함 하도록 하려는 경우 시나리오 참조 [트랜잭션을](https://docs.microsoft.com/ef/core/saving/transactions)합니다.

## <a name="summary"></a>요약

이제 관련된 데이터 사용 소개를 완료 했습니다. 다음 자습서에서는 동시성 충돌을 처리 하는 방법을 볼 수 있습니다.

>[!div class="step-by-step"]
[이전](read-related-data.md)
[다음](concurrency.md)  
