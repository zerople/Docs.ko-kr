---
title: "ASP.NET Core MVC EF 코어-읽기-관련된 데이터 6 / 10 | Microsoft 문서"
author: tdykstra
description: "이 자습서에서는 읽기 하 고 관련된 데이터-즉, Entity Framework는 탐색 속성에 로드 하는 데이터를 표시 합니다."
keywords: "ASP.NET Core, Entity Framework Core 관련된 데이터를 조인"
ms.author: tdykstra
manager: wpickett
ms.date: 03/15/2017
ms.topic: article
ms.assetid: 71fec30f-8ea7-4ca8-96e3-d2e26c5be44e
ms.technology: aspnet
ms.prod: asp.net-core
uid: data/ef-mvc/read-related-data
translationtype: Machine Translation
ms.sourcegitcommit: 8ab055f5cfb259486949b5d8d01dafe07301398a
ms.openlocfilehash: 2d3c6f1c1b54c726c04d372ff8a36682ca9d151b
ms.lasthandoff: 03/23/2017

---

# <a name="reading-related-data---ef-core-with-aspnet-core-mvc-tutorial-6-of-10"></a>읽기 관련 데이터-EF 코어 ASP.NET 핵심 MVC 자습서 (6 / 10)

여 [Tom Dykstra](https://github.com/tdykstra) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)

Contoso University 샘플 웹 응용 프로그램에는 Entity Framework 코어 1.1 및 Visual Studio 2017를 사용 하 여 ASP.NET 핵심 1.1 MVC 웹 응용 프로그램을 만드는 방법을 보여 줍니다. 자습서 시리즈에 대 한 정보를 참조 하십시오. [시리즈의 첫 번째 자습서](intro.md)합니다.

이전 자습서에서 School 데이터 모델을 완료 합니다. 이 자습서에서는 읽기 하 고 관련된 데이터-즉, Entity Framework는 탐색 속성에 로드 하는 데이터를 표시 합니다.

다음 그림은 수 작업 하는 페이지를 보여 줍니다.

![코스 인덱스 페이지](read-related-data/_static/courses-index.png)

![강사 인덱스 페이지](read-related-data/_static/instructors-index.png)

## <a name="eager-explicit-and-lazy-loading-of-related-data"></a>관련된 데이터의 선행, 명시적 및 지연 로딩

여러 가지 방법을 통해 해당 개체-관계형 매핑 (ORM) 소프트웨어 Entity Framework를 엔터티의 탐색 속성에 관련된 데이터를 로드할 수와 같은:

* 즉시 로드 합니다. 엔터티를 읽을 때 그에 따라 관련된 데이터가 검색 됩니다. 일반적으로 그러면 모든 필요한 데이터를 검색 하는 단일 조인 쿼리 됩니다. 즉시 로드를 사용 하 여 지정 Entity Framework Core에는 `Include` 및 `ThenInclude` 메서드.

  ![선행 로딩 예제](read-related-data/_static/eager-loading.png)

  쿼리를 별도에 있는 데이터의 일부를 검색 한 EF "수정" 탐색 속성.  즉, EF 이전에 검색 된 엔터티의 탐색 속성에 속해 있는 개별적으로 검색 된 엔터티를 자동으로 추가 합니다. 관련된 데이터를 검색 하는 쿼리를 사용할 수 있습니다는 `Load` 메서드 등의 목록이 나 개체를 반환 하는 메서드 대신 `ToList` 또는 `Single`합니다.

  ![별도 쿼리 예제](read-related-data/_static/separate-queries.png)

* 명시적 로드 합니다. 먼저 엔터티를 읽으면 관련된 데이터가 검색 되지 않습니다. 필요한 경우 관련된 데이터를 가져오는 코드를 작성 합니다. 별도 쿼리를 사용한 선행 로딩의 경우와 같이 명시적으로 여러 쿼리에서 결과 로드 합니다. 데이터베이스에 전송 합니다. 차이점은 명시적 로드를 코드 지정 로드할 탐색 속성입니다. Entity Framework 코어 1.1에서 사용할 수는 `Load` 명시적 로드를 수행 하기 위해 메서드. 예:

  ![명시적 로딩 예제](read-related-data/_static/explicit-loading.png)

* 지연 로드 합니다. 먼저 엔터티를 읽으면 관련된 데이터가 검색 되지 않습니다. 그러나 처음에는 탐색 속성에 액세스 하려고 하면 해당 탐색 속성에 필요한 데이터 자동으로 검색 됩니다. 쿼리가 처음에 대 한 탐색 속성에서 데이터를 가져오려는 시도 될 때마다 데이터베이스에 전송 됩니다. Entity Framework Core 1.0 지연 로딩을 지원 하지 않습니다.

### <a name="performance-considerations"></a>성능 고려 사항

검색 된 모든 엔터티에 대 한 관련된 데이터를 필요한 알고 있는 경우 데이터베이스에 전송 하는 단일 쿼리 검색 된 각 엔터티에 대 한 별도 쿼리 보다 일반적으로 더 효율적 이므로 최상의 성능을 얻으려면 제공 종종 즉시 로드 합니다. 예를 들어, 각 부서에는&10; 개의 관련된 과정입니다. 모든 관련된 데이터의 선행 로딩 결과적으로 단일 (조인) 쿼리 및 단일 왕복 데이터베이스에 있습니다. 각 부서에 대 한 코스에 대 한 별도 쿼리는 데이터베이스에&11; 개 왕복으로 반환 됩니다. 데이터베이스에 추가적인 라운드 트립이 되며 특히 성능에 좋지 않은 대기 시간이 깁니다.

반면에 일부 시나리오에서는 별도 쿼리는 더 효율적입니다. 모든 관련된 데이터를 하나의 쿼리에서의 선행 로딩은 매우 복잡 한 조인 생성 될 이며 SQL Server를 효율적으로 처리할 수 없습니다 발생할 수 있습니다. 또는 선행 로딩 하는 모든 작업을 먼저 필요한 것 보다 더 많은 데이터를 검색 하기 때문에 별도 쿼리 향상 될 수도 있습니다를 처리 하는 엔터티 집합의 하위 집합에 대해서만 엔터티의 탐색 속성에 액세스 해야 하는 경우. 성능이 중요 한 경우 성능을 테스트 하는 최상의 옵션 선택 하기 위해 두 가지 방법으로 하는 것이 좋습니다.

## <a name="create-a-courses-page-that-displays-department-name"></a>부서 이름을 표시 하는 과정 페이지 만들기

Course 엔터티 과정에 할당 되는 부서의 부서 엔터티를 포함 하는 탐색 속성이 포함 됩니다. 에 있는 부서 엔터티의 Name 속성을 가져오는 과정의 목록에 할당 된 부서 이름을 표시 하려면 해야는 `Course.Department` 탐색 속성입니다.

만들기에 대 한 동일한 옵션을 사용 하 여 Course 엔터티 형식에 대 한 CoursesController 컨트롤러는 **Entity Framework를 사용 하 여 MVC 컨트롤러 (뷰 포함),** 하 던 이전 학생 컨트롤러에 대 한 다음 그림에 나와 있는 것 처럼 스 캐 폴더:

![코스 컨트롤러 추가](read-related-data/_static/add-courses-controller.png)

열기 *CoursesController.cs* 검사는 `Index` 메서드. 자동 스 캐 폴딩이에 대 한 선행 로딩 지정는 `Department` 를 사용 하 여 탐색 속성은 `Include` 메서드.

대체는 `Index` 에 적합 한 이름을 사용 하는 다음 코드를 사용 하 여 메서드는 `IQueryable` 코스 엔터티를 반환 하는 (`courses` 대신 `schoolContext`):

[!code-csharp[주](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_RevisedIndexMethod)]

열기 *Views/Courses/Index.cshtml* 템플릿 코드를 다음 코드로 바꿉니다. 변경 내용은 강조 표시 됩니다.

[!code-html[](intro/samples/cu/Views/Courses/Index.cshtml?highlight=4,7,15-17,34-36,44)]

스 캐 폴드 된 코드를 다음과 같이 변경을 했습니다.

* 코스에 인덱스에서 머리글을 변경 합니다.

* 추가 **번호** 보여 주는 열은 `CourseID` 속성 값입니다. 기본적으로 기본 키 이므로 일반적으로 최종 사용자에 게 의미가 스 캐 폴드 되지 않습니다. 그러나이 경우 기본 키가 의미 하 고으로 표시할지를 하려는 경우

* 변경 된 **부서** 부서 이름을 표시 하는 열입니다. 코드 표시는 `Name` 에 로드 된 부서 엔터티의 속성은 `Department` 탐색 속성:

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

부서 이름의 목록을 보려면 페이지 (Contoso University 홈 페이지에 있는 코스 탭을 선택 하는 경우)를 실행 합니다.

![코스 인덱스 페이지](read-related-data/_static/courses-index.png)

## <a name="create-an-instructors-page-that-shows-courses-and-enrollments"></a>과정 및 등록을 보여 주는 강사 페이지 만들기

이 섹션에서는 강사 페이지를 표시 하기 위해 컨트롤러 및 강사 엔터티에 대 한 보기를 생성 합니다.

![강사 인덱스 페이지](read-related-data/_static/instructors-index.png)

이 페이지를 읽고 다음과 같은 방법으로 관련된 데이터를 표시 합니다.

* 강사 목록이 OfficeAssignment 엔터티의 관련된 데이터를 표시합니다. Instructor와 OfficeAssignment 엔터티는&0; 또는&1;을 한 관계에 있습니다. OfficeAssignment 엔터티에 대 한 선행 로딩을 사용 합니다. 앞에서 설명한 대로 기본 테이블의 모든 검색 된 행에 관련된 데이터를 필요로 하는 경우 즉시 로드 일반적으로 더 효율적입니다. 이 경우 모든 표시 된 강사의 사무실 할당을 표시 하려면.

* 강사를 선택 하면 관련된 코스 엔터티가 표시 됩니다. 강사 및 코스 엔터티가 서로 다 대 다 관계입니다. Course 엔터티 및 관련된 부서 엔터티에 대 한 선행 로딩을 사용 합니다. 이 경우에 선택한 강사의 과정을 필요 하기 때문에 별도 쿼리 더 효율적일 수 있습니다. 그러나이 예제에서는 자체 탐색 속성에 있는 엔터티 내에서 탐색 속성에 대 한 선행 로딩을 사용 하는 방법을 보여 줍니다.

* 사용자가 제공 하는 과정을 선택 하면 등록 엔터티 집합의 관련된 데이터 표시 됩니다. 코스 및 등록 엔터티 일 대 다 관계에는. 등록 엔터티 및 해당 관련된 학생 엔터티에 대 한 별도 쿼리를 사용 합니다.

### <a name="create-a-view-model-for-the-instructor-index-view"></a>강사 인덱스 보기에 대 한 뷰 모델 만들기

강사 페이지에는 서로 다른 세 테이블에서 데이터 표시 됩니다. 따라서 각 테이블 중 하나에 대 한 데이터를 보유으로 세 가지 속성을 포함 하는 뷰 모델을 만들어야 합니다.

에 *SchoolViewModels* 폴더를 만들 *InstructorIndexData.cs* 기존 코드를 다음 코드로 바꿉니다.

[!code-csharp[주](intro/samples/cu/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="create-the-instructor-controller-and-views"></a>강사 컨트롤러와 뷰 만들기

다음 그림에 나와 있는 것 처럼 EF 읽기/쓰기 동작이 포함 된 강사 컨트롤러를 만듭니다.

![강사 컨트롤러 추가](read-related-data/_static/add-instructors-controller.png)

열기 *InstructorsController.cs* 추가 하 여 및 Viewmodel 네임 스페이스에 대 한 정보:

[!code-csharp[주](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_Using)]

Index 메서드에 관련 된 데이터의 선행 로딩을 수행 하는 뷰 모델에 다음 코드로 바꿉니다.

[!code-csharp[주](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_EagerLoading)]

메서드에 선택적 경로 데이터 (`id`) 및 쿼리 문자열 매개 변수 (`courseID`) 선택한 강사 및 선택한 과정의 ID 값을 제공 하는 합니다. 매개 변수에서 제공 되는 **선택** 페이지의 하이퍼링크입니다.

코드 뷰 모델의 인스턴스를 만들고 강사의 목록에 배치 하 여 시작 합니다. 코드에 대 한 선행 로딩을 지정 된 `Instructor.OfficeAssignment` 및 `Instructor.CourseAssignments` 탐색 속성입니다. 내는 `CourseAssignments` 속성에는 `Course` 로드 하 고 그 안에서 속성은, `Enrollments` 및 `Department` 로드 하 고 각 속성은 `Enrollment` 엔터티는 `Student` 속성이 로드 합니다.

[!code-csharp[주](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ThenInclude)]

보기는 항상 OfficeAssignment 엔터티를 사용 하므로를 같은 쿼리에서 인출 하는 더 효율적입니다. 코스 엔터티는 없이 보다 선택 과정에 페이지를 더 자주 표시 하는 경우에 단일 쿼리는 쿼리를 여러 개 보다 더 나은 웹 페이지에서 강사를 선택한 경우 필요 합니다.

코드를 반복 해 서 `CourseAssignments` 및 `Course` 의 두 속성만 필요 하기 때문에 `Course`합니다. 첫 번째 문자열 `ThenInclude` 가져옵니다 호출 `CourseAssignment.Course`, `Course.Enrollments`, 및 `Enrollment.Student`합니다.

[!code-csharp[주](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ThenInclude&highlight=3-6)]

코드의 해당 지점에서 다른 `ThenInclude` 의 탐색 속성에 대 한 것 `Student`, 필요 하지 않습니다. 하지만 호출 `Include` 으로 다시 시작 `Instructor` 속성을 지정 하는이 시간 다시 체인을 통해 이동 해야 하므로 `Course.Department` 대신 `Course.Enrollments`합니다.

[!code-csharp[주](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ThenInclude&highlight=7-9)]

다음 코드는 강사가 선택 될 때 실행 합니다. 선택한 강사 뷰 모델에서 강사의 목록에서 검색 됩니다. 뷰 모델의 `Courses` 속성에서 해당 강사의 코스 엔터티와 로드 `CourseAssignments` 탐색 속성입니다.

[!code-csharp[주](intro/samples/cu/Controllers/InstructorsController.cs?range=54-60)]

`Where` 메서드는 컬렉션을 반환 하지만 조건에서 단일 강사 엔터티만 반환 되는 메서드 결과에 전달 되는 경우. `Single` 메서드 컬렉션을 단일 엔터티로 강사, 해당 엔터티에 액세스할 수 있는 변환 `CourseAssignments` 속성입니다. `CourseAssignments` 속성 포함 `CourseAssignment` 하려는 관련 된 엔터티 `Course` 엔터티.

사용 된 `Single` 메서드 컬렉션을 알고 있는 경우 컬렉션에 항목을 하나만 갖습니다. 단일 메서드에 전달 된 컬렉션은 비어 있거나 둘 이상의 항목 없으면 예외가 throw 됩니다. 대신 `SingleOrDefault`를 반환 하는 기본값 (이 경우 null) 컬렉션이 비어 있는 경우. 그러나이 경우에는 여전히 예외가 발생 (찾으려고 시도에서 `Courses` 속성에 null 참조), 예외 메시지는 문제의 원인을 나타냅니다 덜 명확 하 게 하 고 있습니다. 호출 하는 경우는 `Single` 메서드를 전달할 수도 있습니다 where에서 조건 호출 하는 대신는 `Where` 메서드 별도로:

```csharp
.Single(i => i.ID == id.Value)
```

대신에:

```csharp
.Where(I => i.ID == id.Value).Single()
```

다음으로, 제공 하는 과정을 선택한 경우 선택한 과정 보기 모델에 제공 된 과정의 목록에서 검색 됩니다. 그런 다음 뷰 모델의 `Enrollments` 속성이 해당 과정에서 등록 엔터티와 로드 `Enrollments` 탐색 속성입니다.

[!code-csharp[주](intro/samples/cu/Controllers/InstructorsController.cs?range=62-67)]

### <a name="modify-the-instructor-index-view"></a>강사 인덱스 뷰 수정

*Views/Instructor/Index.cshtml*, 템플릿 코드를 다음 코드로 바꿉니다. 변경 내용은 강조 표시 됩니다.

[!code-html[](intro/samples/cu/Views/Instructors/Index1.cshtml?range=1-64&highlight=1,3-7,18-19,41-54,56)]

기존 코드를 다음과 같이 변경을 했습니다.

* 모델 클래스를 변경 `InstructorIndexData`합니다.

* 페이지 제목을 변경 **인덱스** 를 **강사**합니다.

* 추가 **Office** 표시 하는 열 `item.OfficeAssignment.Location` 경우에만 `item.OfficeAssignment` null이 됩니다. (0 또는&1;을 한 관계는 이기 때문에 있을 수 있습니다 하지 관련된 OfficeAssignment 엔터티.)

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* 추가 **과정** 과정을 표시 하는 열에서 각 강사 배웠습니다.

* 동적으로 추가 하는 추가 된 코드 `class="success"` 에 `tr` 선택한 강사의 요소입니다. 부트스트랩 클래스를 사용 하 여 선택한 행의 배경색을 설정 합니다.

  ```html
  string selectedRow = "";
  if (item.ID == (int?)ViewData["InstructorID"])
  {
      selectedRow = "success";
  }
  ```

* 레이블이 지정 된 새 하이퍼링크를 추가 **선택** 를 보내도록 선택한 강사의 ID에 이르게 각 행에 다른 링크를 바로 앞의 `Index` 메서드.

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

응용 프로그램을 실행 하 고 강사 탭을 선택 합니다. 페이지 관련 OfficeAssignment 엔터티가 때 관련된 OfficeAssignment 엔터티 및 빈 테이블 셀의 Location 속성을 표시 합니다.

![선택한 항목이 강사 인덱스 페이지](read-related-data/_static/instructors-index-no-selection.png)

에 *Views/Instructor/Index.cshtml* 파일을 닫는 테이블 (끝에 요소 파일의), 한 후 다음 코드를 추가 합니다. 이 코드는 강사가 선택 되 면 강사와 관련 된 과정의 목록이 표시 됩니다.

[!code-html[](intro/samples/cu/Views/Instructors/Index1.cshtml?range=66-101)]

이 코드를 읽고는 `Courses` 과정의 목록을 표시 하는 뷰 모델의 속성입니다. 또한 제공는 **선택** 하이퍼링크에 선택한 과정의 ID를 전송 하는 `Index` 작업 메서드.

페이지를 실행 하 고 강사를 선택 합니다. 선택한 강사에 할당 하는 과정을 표시 하는 표를 참조 하는 이제 및 각 과정에 대 한 할당 된 부서 이름을 표시 합니다.

![선택한 강사 인덱스 페이지 강사](read-related-data/_static/instructors-index-instructor-selected.png)

방금 추가한 코드 블록을 한 후 다음 코드를 추가 합니다. 과정을 선택한 경우에 제공 하는 과정에 등록 되어 학생의 목록을 표시 합니다.

[!code-html[](intro/samples/cu/Views/Instructors/Index1.cshtml?range=103-125)]

이 코드는 과정에 등록 하는 학생의 목록을 표시 하기 위해 뷰 모델의 등록 속성을 읽습니다.

페이지를 실행 하 고 강사를 선택 합니다. 등록 된 학생과 자신의 등급의 목록을 보려면 코스를 선택 합니다.

![강사 인덱스 페이지 강사 및 코스 선택](read-related-data/_static/instructors-index.png)

## <a name="explicit-loading"></a>명시적 로드

강사 목록이 검색 때 *InstructorsController.cs*, 선행 로딩에 대 한 지정한는 `CourseAssignments` 탐색 속성입니다.

선택한 강사 및 코스에 등록을 확인 하려는 경우에 사용자를 예상 가정 합니다. 이 경우 다음 요청 될 경우에 등록 데이터를 로드 하는 것이 좋습니다. 명시적 로드를 수행 하는 방법의 예를 보려면 대체는 `Index` 등록에 대 한 선행 로딩을 제거 하 고 해당 속성을 명시적으로 로드 하는 다음 코드를 사용 하 여 메서드. 코드 변경 내용은 강조 표시 됩니다.

[!code-csharp[주](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ExplicitLoading&highlight=25-31)]

새 코드는 *ThenInclude* instructor 엔터티를 검색 하는 코드에서 등록 데이터에 대 한 메서드를 호출 합니다. 있는 강사 이자 코스를 선택 하는 경우 강조 표시 된 코드는 선택한 과정에 대 한 등록 엔터티 및 각 등록에 대 한 학생 엔터티를 검색 합니다.

강사 인덱스 페이지를 지금 실행 하며 데이터를 검색 하는 방법을 변경 하지만 페이지에 표시 되는 내용을 차이가 없습니다 확인할 수 있습니다.

## <a name="summary"></a>요약

이제 사용한 선행 로딩 하나의 쿼리를 사용 하 고 여러 개의 쿼리가 있는 탐색 속성에 관련된 데이터를 읽을 수 있습니다. 다음 자습서 관련된 데이터를 업데이트 하는 방법에 알아봅니다.

>[!div class="step-by-step"]
>[이전](complex-data-model.md)
>[다음](update-related-data.md)  

