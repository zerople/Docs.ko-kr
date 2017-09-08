---
title: "ASP.NET Core MVC EF 코어-정렬, 필터, 페이징-3 / 10 인"
author: tdykstra
description: "이 자습서에서는 정렬, 필터링 및 페이징을 ASP.NET 코어 및 Entity Framework 코어를 사용 하 여 페이지 기능을 추가 합니다."
keywords: "ASP.NET Core, Entity Framework Core, 정렬, 필터, 페이징, 그룹화"
ms.author: tdykstra
ms.date: 03/15/2017
ms.topic: get-started-article
ms.assetid: e6c1ff3c-5673-43bf-9c2d-077f6ada1f29
ms.technology: aspnet
ms.prod: asp.net-core
uid: data/ef-mvc/sort-filter-page
ms.openlocfilehash: bc2896d0eeda7e84cef06ee3f235e637bfe04318
ms.sourcegitcommit: 5355c96a1768e5a1d5698a98c190e7addcc4ded5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/05/2017
---
# <a name="sorting-filtering-paging-and-grouping---ef-core-with-aspnet-core-mvc-tutorial-3-of-10"></a>정렬, 필터링, 페이징 및 그룹화-EF 코어 ASP.NET Core MVC 자습서 (3 / 10)

여 [Tom Dykstra](https://github.com/tdykstra) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)

Contoso 대학 샘플 웹 응용 프로그램에는 Entity Framework Core 및 Visual Studio를 사용 하 여 ASP.NET Core MVC 웹 응용 프로그램을 만드는 방법을 보여 줍니다. 자습서 시리즈에 대 한 정보를 참조 하십시오. [시리즈의 첫 번째 자습서](intro.md)합니다.

이전 자습서 인 학생 엔터티에 대 한 기본적인 CRUD 작업에 대 한 웹 페이지의 집합을 구현합니다. 이 자습서에서는 정렬, 필터링 및 페이징 기능 학생 인덱스 페이지에 추가 합니다. 단순 그룹화를 수행 하는 페이지를 만들 수 있습니다.

다음은 페이지 모양은 완료 되 면입니다. 열 머리글은 해당 열으로 정렬 하기 위해 클릭할 수 있는 링크입니다. 열 머리글을 반복 해 서 클릭 하면 오름차순 및 내림차순 정렬 순서 사이 토글합니다.

![학생 인덱스 페이지](sort-filter-page/_static/paging.png)

## <a name="add-column-sort-links-to-the-students-index-page"></a>학생 인덱스 페이지에 열 정렬 링크 추가

학생 인덱스 페이지에 정렬를 추가 하려면 변경 된 `Index` 학생 컨트롤러의 메서드 코드 학생 인덱스 뷰를 추가 합니다.

### <a name="add-sorting-functionality-to-the-index-method"></a>인덱스 메서드에 정렬 기능 추가

*StudentsController.cs*, 대체 된 `Index` 메서드를 다음 코드로:

[!code-csharp[Main](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortOnly)]

이 코드는 수신 된 `sortOrder` URL에 쿼리 문자열에서 매개 변수입니다. 쿼리 문자열 값은 작업 메서드에 매개 변수로 ASP.NET Core MVC에서 제공 됩니다. 매개 변수는 "Name" 또는 "날짜" 밑줄과 문자열 "desc" 내림차순을 지정 하 여 필요에 따라 문자열로 됩니다. 기본 정렬 순서는 오름차순입니다.

인덱스 페이지를 요청 하 고, 처음으로 쿼리 문자열이 없는 합니다. 학생의 fall 통해 사례에서 설정한의 기본 설정인 성을 기준으로 오름차순에 표시 되는 `switch` 문. 적절 한 열 머리글 하이퍼링크를 클릭 하면 `sortOrder` 쿼리 문자열에 값을 제공 합니다.

두 `ViewData` 요소 (NameSortParm 및 DateSortParm)를 사용 하는 보기를 열 머리글 하이퍼링크 적절 한 쿼리 문자열 값으로 구성 합니다.

[!code-csharp[Main](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortOnly&highlight=3-4)]

이들은 삼항 문입니다. 첫 번째 지정 되는 경우는 `sortOrder` 매개 변수는 null 또는 비어 있는 경우 NameSortParm로 설정 해야 "name_desc", 그렇지 않으면 빈 문자열로 설정 해야 합니다. 열 머리글 하이퍼링크를 다음과 같이 설정 하려면 보기를 사용 하는이 두 개의 문:

|  현재 정렬 순서  | 마지막 이름 하이퍼링크 | 날짜 하이퍼링크 |
|:--------------------:|:-------------------:|:--------------:|
| 이름 오름차순 마지막  | descending          | ascending      |
| 이름 내림차순 마지막 | ascending           | ascending      |
| 오름차순 날짜       | ascending           | descending     |
| 내림차순 날짜      | ascending           | ascending      |

메서드가 LINQ to Entities 사용 하 여 기준으로 정렬 하려면 열을 지정 합니다. 코드 만듭니다는 `IQueryable` switch 문의 하기 전에 변수 switch 문과 호출에서 수정는 `ToListAsync` 후 메서드는 `switch` 문. 생성 및 수정할 `IQueryable` 변수 쿼리가 없는 데이터베이스에 전송 됩니다. 변환 될 때까지 쿼리가 실행 되지 않습니다는 `IQueryable` 개체와 같은 메서드를 호출 하 여 컬렉션에 `ToListAsync`합니다. 따라서이 코드 발생 되어야 실행 하는 단일 쿼리는 `return View` 문.

이 코드 열 다 수 포함 된 자세한 정보를 가져올 수 있습니다. [이 시리즈의 마지막 자습서](advanced.md#dynamic-linq) 의 이름을 전달할 수 있도록 하는 코드를 작성 하는 방법을 보여 줍니다.는 `OrderBy` 문자열 변수에 열입니다.

### <a name="add-column-heading-hyperlinks-to-the-student-index-view"></a>열 머리글 하이퍼링크 학생 인덱스 뷰를 추가

코드 *Views/Students/Index.cshtml*, 열 머리글 하이퍼링크를 추가 하려면 다음 코드와 함께 합니다. 변경 된 줄이 강조 표시 됩니다.

[!code-html[](intro/samples/cu/Views/Students/Index2.cshtml?highlight=16,22)]

이 코드의 정보를 사용 하 여 `ViewData` 적절 한 쿼리 된 하이퍼링크를 설정 하는 속성 문자열 값입니다.

페이지를 실행 하 고 클릭는 **성** 및 **등록 날짜** 확인 해당 정렬 하려면 열 머리글 작동 합니다.

![이름 순서로 학생 인덱스 페이지](sort-filter-page/_static/name-order.png)

## <a name="add-a-search-box-to-the-students-index-page"></a>검색 상자 학생 인덱스 페이지에 추가

학생 인덱스 페이지에 필터링을 추가 하려면 추가 텍스트 상자 및 전송 단추가 보기로 및에 해당 변경는 `Index` 메서드. 입력란 이름 및 성 필드에서 검색할 문자열을 입력할 수 있습니다.

### <a name="add-filtering-functionality-to-the-index-method"></a>Index 메서드에 필터링 기능 추가

*StudentsController.cs*, 대체는 `Index` 메서드를 다음 코드로 (변경 내용을 강조 표시).

[!code-csharp[Main](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortFilter&highlight=1,5,9-13)]

사용자가 추가한는 `searchString` 매개 변수는 `Index` 메서드. 검색 문자열 값이 인덱스 뷰를 추가 하는 텍스트 상자가에서 수신 합니다. 또한를 추가 했으므로 LINQ 명령문 where 해당 이름 또는 성을 검색 문자열을 포함 하는 학생만을 선택 하는 절. Where를 추가 하는 문에 절이 검색 하려면 값이 있는 경우에 실행 됩니다.

> [!NOTE]
> 호출 하는 여기에서 `Where` 메서드는 `IQueryable` 개체 및 필터는 서버에서 처리 됩니다. 일부 시나리오에서는 있습니다를 호출할 수는 `Where` 메모리 내 컬렉션에 대 한 확장 메서드이기 메서드. (에 대 한 참조를 변경 한다고 가정 예를 들어 `_context.Students` 는 EF의 즉 대신 `DbSet` 반환 하는 저장소 메서드를 참조 하는 `IEnumerable` 컬렉션입니다.) 결과 일반적으로 동일 하지만 경우에 따라 다를 수 있습니다.
>
>예를 들어의.NET Framework 구현은 `Contains` 메서드는 기본적으로 대/소문자 구분 비교를 수행 하지만 SQL Server에서 SQL Server 인스턴스의 데이터 정렬 설정에 따라 결정 됩니다. 해당 설정은 기본적으로 대/소문자 구분 됩니다. 호출할 수는 `ToUpper` 명시적으로 대/소문자 구분 테스트를 만드는 메서드와 알림이: *여기서 (s = > s.LastName.ToUpper() 합니다. Contains(searchString.ToUpper())*합니다. 결과 그대로 반환 하는 리포지토리를 사용 하도록 나중에 코드를 변경 하는 경우 않게 하는 `IEnumerable` 컬렉션 대신 한 `IQueryable` 개체입니다. (호출 하는 경우는 `Contains` 에서 메서드는 `IEnumerable` .NET Framework 구현은 가져올 컬렉션을; 호출 하는 경우 그에 `IQueryable` 개체, 데이터베이스 공급자 구현을 가져옵니다.) 그러나이 솔루션에 대 한 성능 저하가 됩니다. `ToUpper` 코드 TSQL SELECT 문의 WHERE 절에 함수를 추가 합니다. 인덱스를 사용 하 여 최적화 프로그램이 수 없게 됩니다. 않는 것이 좋습니다은 대/소문자 SQL이 설치 되어 대부분, 있다고 가정은 `ToUpper` 대/소문자 구분 데이터 저장소로 마이그레이션하기 전 까지는 코드입니다.

### <a name="add-a-search-box-to-the-student-index-view"></a>검색 상자 학생 인덱스 뷰를 추가

*Views/Student/Index.cshtml*, 캡션, 텍스트 상자를 만들려면 테이블 태그를 열기 전에 강조 표시 된 코드를 추가 및 **검색** 단추입니다.

[!code-html[](intro/samples/cu/Views/Students/Index3.cshtml?range=9-23&highlight=5-13)]

이 코드에서는 사용는 `<form>` [태그 도우미](https://docs.asp.net/en/latest/mvc/views/tag-helpers/intro.html) 검색 텍스트 상자 및 단추를 추가 합니다. 기본적으로는 `<form>` 태그 도우미 즉, 매개 변수가 URL 아니라 HTTP 메시지 본문에 쿼리 문자열로 전달 하는 POST로 양식 데이터를 전송 합니다. HTTP GET을 지정 하면 양식 데이터 변수로 전달 됩니다 URL에 쿼리 문자열, URL에 책갈피를 있습니다. 작업 업데이트 되지 않습니다 때 전달 되는 사용 해야 하는 W3C 지침 좋습니다.

페이지를 실행 하 고 검색 문자열을 입력 한 다음 필터링이 작동 하는지 확인 하는 검색을 클릭 합니다.

![필터링 된 인덱스 페이지 학생](sort-filter-page/_static/filtering.png)

검색 문자열은 URL에 포함 되어 있는지 확인 합니다.

```html
http://localhost:5813/Students?SearchString=an
```

이 페이지에 책갈피는 책갈피를 사용 하는 경우 필터링된 된 목록을 봅니다. 추가 `method="get"` 에 `form` 태그는 원인을 쿼리 문자열을 생성할 수 있습니다.

이 단계에서 열 머리글 정렬 링크를 클릭 하면 손실 됩니다에 입력 된 필터 값은 **검색** 상자입니다. 다음 섹션에서 수정 하 게 합니다.

## <a name="add-paging-functionality-to-the-students-index-page"></a>학생 인덱스 페이지에 페이징 기능을 추가 합니다.

페이징에 학생 인덱스 페이지를 추가 하려면 만들어는 `PaginatedList` 클래스를 사용 하 `Skip` 및 `Take` 문은 항상 테이블의 모든 행을 검색 하는 대신 서버에서 데이터를 필터링 합니다. 다음에 추가로 변경할 수는 `Index` 메서드 페이징 단추를 추가 하 고는 `Index` 보기. 다음 그림에는 페이징 단추가 나와 있습니다.

![학생 인덱스 페이징 링크가 있는 페이지](sort-filter-page/_static/paging.png)

프로젝트 폴더에서 만들 `PaginatedList.cs`, 다음 템플릿 코드를 다음 코드로 바꿉니다.

[!code-csharp[Main](intro/samples/cu/PaginatedList.cs)]

`CreateAsync` 이 코드에서 메서드는 페이지 크기 및 페이지 번호를 사용 하 고 적절 한 적용 `Skip` 및 `Take` 문을 `IQueryable`합니다. 때 `ToListAsync` 라고 하는 `IQueryable`, 요청된 된 페이지를 포함 하는 목록을 반환 합니다. 속성 `HasPreviousPage` 및 `HasNextPage` 활성화 하거나 비활성화 하는 데 사용 될 **이전** 및 **다음** 단추 페이징 합니다.

A `CreateAsync` 메서드는 생성자를 대신 만드는 데 사용 되는 `PaginatedList<T>` 개체 생성자는 비동기 코드를 실행할 수 없습니다.

## <a name="add-paging-functionality-to-the-index-method"></a>인덱스 메서드에 페이징 기능을 추가 합니다.

*StudentsController.cs*, 대체 된 `Index` 메서드를 다음 코드로 합니다.

[!code-csharp[Main](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortFilterPage&highlight=1-5,7,11-18,45-46)]

이 코드 페이지 번호 매개 변수, 현재 정렬 순서 매개 변수, 및 현재 필터 매개 변수 메서드 시그니처를 추가합니다.

```csharp
public async Task<IActionResult> Index(
    string sortOrder,
    string currentFilter,
    string searchString,
    int? page)
```

처음으로 페이지 표시 되거나, 사용자 하지 않은 한 페이징 또는 정렬 링크를 클릭 한 경우 모든 매개 변수가 null이 됩니다.  페이징 링크를 클릭 하는 경우 페이지 변수 표시할 페이지 번호를 포함 됩니다.

`ViewData` CurrentSort 라는 요소를이 페이징 하는 동안 동일한 정렬 순서를 유지 하기 위해 페이징 파일에 대 한 링크에 포함 되어야 하기 때문에 현재 정렬 순서를 사용 하 여 뷰를 제공 합니다.

`ViewData` 현재 필터 라는 요소를 현재 필터 문자열을 사용 하 여 뷰를 제공 합니다. 이 값, 페이징 하는 동안 필터 설정을 유지 하기 위해 페이징 링크에 포함 되어야 하며 페이지 다시 표시 하는 경우 텍스트 상자에 복원 해야 합니다.

검색 문자열 페이징 하는 동안 변경 되 면 표시할 다른 데이터를 새 필터 발생할 수 있기 때문 1로 다시 설정 되는 페이지에 있습니다. 검색 문자열 값이 텍스트 상자에 입력 하 고 전송 단추를 누를 때 변경 됩니다. 이 경우에 `searchString` 매개 변수는 null입니다.

```csharp
if (searchString != null)
{
    page = 1;
}
else
{
    searchString = currentFilter;
}
```

끝에는 `Index` 메서드는 `PaginatedList.CreateAsync` 메서드 학생 페이징을 지원 되는 컬렉션 형식에는 단일 페이지로 학생 쿼리를 변환 합니다. 그러면 학생 들의 해당 단일 페이지 보기에 전달 됩니다.

```csharp
return View(await PaginatedList<Student>.CreateAsync(students.AsNoTracking(), page ?? 1, pageSize));
```

`PaginatedList.CreateAsync` 메서드는 페이지 번호를 사용 합니다. 두 물음표 null 병합 연산자를 나타냅니다. Null 병합 연산자는 null 허용 형식에 대 한 기본값을 정의 식 `(page ?? 1)` 의 값을 반환 하는 수단 `page` 값을 가진 또는 이면 1을 반환 하는 경우 `page` null입니다.

## <a name="add-paging-links-to-the-student-index-view"></a>학생 인덱스 뷰를 페이징 링크 추가

*Views/Students/Index.cshtml*, 기존 코드를 다음 코드로 바꿉니다. 변경 내용은 강조 표시 됩니다.

[!code-html[](intro/samples/cu/Views/Students/Index.cshtml?highlight=1,27,30,33,61-79)]

`@model` 페이지 맨 위에 있는 문은 보기 이제 가져옴을 지정는 `PaginatedList<T>` 개체가 아니라는 `List<T>` 개체입니다.

열 머리글 링크 필터 결과 내에서 사용자를 정렬할 수 있도록 컨트롤러에 현재 검색 문자열을 전달 하는 쿼리 문자열을 사용 합니다.

```html
<a asp-action="Index" asp-route-sortOrder="@ViewData["DateSortParm"]" asp-route-currentFilter ="@ViewData["CurrentFilter"]">Enrollment Date</a>
```

태그 도우미에서 페이징 단추가 표시 됩니다.

```html
<a asp-action="Index"
   asp-route-sortOrder="@ViewData["CurrentSort"]"
   asp-route-page="@(Model.PageIndex - 1)"
   asp-route-currentFilter="@ViewData["CurrentFilter"]"
   class="btn btn-default @prevDisabled">
   Previous
</a>
```

페이지를 실행 합니다.

![학생 인덱스 페이징 링크가 있는 페이지](sort-filter-page/_static/paging.png)

페이징 작동 하는지 확인 하려면 서로 다른 정렬 순서에서 페이징 링크를 클릭 합니다. 검색 문자열을 입력 하 고 페이징 페이징도 제대로 작동 하는지 확인 된 정렬 및 필터링을 다시 시도 하십시오.

## <a name="create-an-about-page-that-shows-student-statistics"></a>학생 통계를 보여 주는 정보 페이지 만들기

Contoso 대학 웹 사이트에 대 한 **에 대 한** 페이지에서 각 등록 날짜에 대해 등록 한 학생 수를 표시 합니다. 이 그룹에 그룹화 및 간단한 계산 해야합니다. 이렇게 하려면 다음을 수행 합니다.

* 보기를 전달 해야 하는 데이터에 대 한 보기 모델 클래스를 만듭니다.

* Home 컨트롤러에서 정보 메서드를 수정 합니다.

* 정보 보기를 수정 합니다.

### <a name="create-the-view-model"></a>뷰 모델 만들기

만들기는 *SchoolViewModels* 폴더에는 *모델* 폴더입니다.

새 폴더에 클래스 파일 추가 *EnrollmentDateGroup.cs* 템플릿 코드를 다음 코드로 바꿉니다.

[!code-csharp[Main](intro/samples/cu/Models/SchoolViewModels/EnrollmentDateGroup.cs)]

### <a name="modify-the-home-controller"></a>Home 컨트롤러를 수정 합니다.

*HomeController.cs*, 다음 추가 파일 맨 위에 있는 문을 사용 하 여:

[!code-csharp[Main](intro/samples/cu/Controllers/HomeController.cs?name=snippet_Usings1)]

여는 중괄호는 클래스에 대 한 직후 데이터베이스 컨텍스트에 대 한 클래스 변수를 추가 하 고에서 ASP.NET Core DI 컨텍스트의 인스턴스를 가져옵니다.

[!code-csharp[Main](intro/samples/cu/Controllers/HomeController.cs?name=snippet_AddContext&highlight=3,5,7)]

`About` 메서드를 다음 코드로 바꿉니다.

[!code-csharp[Main](intro/samples/cu/Controllers/HomeController.cs?name=snippet_UseDbSet)]

LINQ 명령문 학생 엔터티 등록 날짜별으로 그룹화 각 그룹에 있는 엔터티 수를 계산 하 고 컬렉션의 결과 저장할지 `EnrollmentDateGroup` 모델 개체를 보고 합니다.
> [!NOTE] 
> Entity Framework Core 1.0 버전의 클라이언트에는 전체 결과 집합이 반환 됩니다 및 그룹화 클라이언트에서 수행 됩니다. 일부 시나리오에서 성능 문제가 발생할 수 있습니다. 필요한 경우 원시 SQL을 사용 하 여 서버에서 그룹화 작업을 수행 하 고 프로덕션 볼륨 데이터의 성능을 테스트 해야 합니다. 원시 SQL을 사용 하는 방법에 대 한 정보를 참조 하십시오. [이 시리즈의 마지막 자습서](advanced.md)합니다.

### <a name="modify-the-about-view"></a>수정 된 보기에 대 한

코드는 *Views/Home/About.cshtml* 를 다음 코드로 파일:

[!code-html[](intro/samples/cu/Views/Home/About.cshtml)]

응용 프로그램을 실행 하 고 클릭는 **에 대 한** 링크 합니다. 각 등록 날짜에 대 한 학생 수는 테이블에 표시 됩니다.

![페이지 정보](sort-filter-page/_static/about.png)

## <a name="summary"></a>요약

이 자습서에서는 정렬, 필터링, 페이징, 및 그룹화를 수행 하는 방법을 살펴보았습니다. 다음 자습서에서 마이그레이션을 사용 하 여 데이터 모델 변경 내용을 처리 하는 방법을 설명 합니다.

>[!div class="step-by-step"]
[이전](crud.md)
[다음](migrations.md)  
