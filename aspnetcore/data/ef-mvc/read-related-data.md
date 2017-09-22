---
title: "ASP.NET Core MVC EF 코어-와 관련 된 데이터 읽기-6 / 10"
author: tdykstra
description: "이 자습서에서는 읽기 및 관련된 데이터-Entity Framework 탐색 속성에 로드 하는 데이터를 표시 합니다."
keywords: "ASP.NET Core, Entity Framework Core 관련된 데이터를 조인"
ms.author: tdykstra
manager: wpickett
ms.date: 03/15/2017
ms.topic: get-started-article
ms.assetid: 71fec30f-8ea7-4ca8-96e3-d2e26c5be44e
ms.technology: aspnet
ms.prod: asp.net-core
uid: data/ef-mvc/read-related-data
ms.openlocfilehash: 37613d974fdf1766b187cdd05efc926ecc6a351b
ms.sourcegitcommit: 78d28178345a0eea91556e4cd1adad98b1446db8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2017
---
# <a name="reading-related-data---ef-core-with-aspnet-core-mvc-tutorial-6-of-10"></a><span data-ttu-id="02d7e-104">읽기 관련 데이터-EF 코어 ASP.NET Core MVC 자습서 (6 / 10)</span><span class="sxs-lookup"><span data-stu-id="02d7e-104">Reading related data - EF Core with ASP.NET Core MVC tutorial (6 of 10)</span></span>

<span data-ttu-id="02d7e-105">여 [Tom Dykstra](https://github.com/tdykstra) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="02d7e-105">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="02d7e-106">Contoso 대학 샘플 웹 응용 프로그램에는 Entity Framework Core 및 Visual Studio를 사용 하 여 ASP.NET Core MVC 웹 응용 프로그램을 만드는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-106">The Contoso University sample web application demonstrates how to create ASP.NET Core MVC web applications using Entity Framework Core and Visual Studio.</span></span> <span data-ttu-id="02d7e-107">자습서 시리즈에 대 한 정보를 참조 하십시오. [시리즈의 첫 번째 자습서](intro.md)합니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-107">For information about the tutorial series, see [the first tutorial in the series](intro.md).</span></span>

<span data-ttu-id="02d7e-108">이전 자습서에서 School 데이터 모델을 완료 합니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-108">In the previous tutorial, you completed the School data model.</span></span> <span data-ttu-id="02d7e-109">이 자습서에서는 읽기 및 관련된 데이터-Entity Framework 탐색 속성에 로드 하는 데이터를 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-109">In this tutorial, you'll read and display related data -- that is, data that the Entity Framework loads into navigation properties.</span></span>

<span data-ttu-id="02d7e-110">다음 그림에서는 보여 페이지와 협력 합니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-110">The following illustrations show the pages that you'll work with.</span></span>

![Courses 인덱스 페이지](read-related-data/_static/courses-index.png)

![강사 인덱스 페이지](read-related-data/_static/instructors-index.png)

## <a name="eager-explicit-and-lazy-loading-of-related-data"></a><span data-ttu-id="02d7e-113">관련된 데이터를 신속 하 게, 명시적, 및 지연 로드</span><span class="sxs-lookup"><span data-stu-id="02d7e-113">Eager, explicit, and lazy Loading of related data</span></span>

<span data-ttu-id="02d7e-114">여러 가지 방법으로 해당 개체-관계형 매핑 (ORM) 소프트웨어 같이 가지 Entity Framework를 엔터티의 탐색 속성에 관련된 데이터를 로드할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-114">There are several ways that Object-Relational Mapping (ORM) software such as Entity Framework can load related data into the navigation properties of an entity:</span></span>

* <span data-ttu-id="02d7e-115">즉시 로드 합니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-115">Eager loading.</span></span> <span data-ttu-id="02d7e-116">엔터티를 읽을 때 함께 관련된 데이터가 검색 됩니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-116">When the entity is read, related data is retrieved along with it.</span></span> <span data-ttu-id="02d7e-117">일반적으로 필요한 데이터를 모두 검색 하는 단일 조인 쿼리에서 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-117">This typically results in a single join query that retrieves all of the data that's needed.</span></span> <span data-ttu-id="02d7e-118">즉시 로드를 사용 하 여 지정 Entity Framework Core에는 `Include` 및 `ThenInclude` 메서드.</span><span class="sxs-lookup"><span data-stu-id="02d7e-118">You specify eager loading in Entity Framework Core by using the `Include` and `ThenInclude` methods.</span></span>

  ![즉시 로드 예제](read-related-data/_static/eager-loading.png)

  <span data-ttu-id="02d7e-120">몇 가지 별도 쿼리에서 데이터를 검색할 수 있습니다 및 EF "픽스" 탐색 속성입니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-120">You can retrieve some of the data in separate queries, and EF "fixes up" the navigation properties.</span></span>  <span data-ttu-id="02d7e-121">즉, EF 이전에 검색 된 엔터티의 탐색 속성에 속해 있는 개별적으로 검색 된 엔터티를 자동으로 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-121">That is, EF automatically adds the separately retrieved entities where they belong in navigation properties of previously retrieved entities.</span></span> <span data-ttu-id="02d7e-122">관련된 데이터를 검색 하는 쿼리를 사용할 수 있습니다는 `Load` 메서드 등의 목록이 나 개체를 반환 하는 메서드 대신 `ToList` 또는 `Single`합니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-122">For the query that retrieves related data, you can use the `Load` method instead of a method that returns a list or object, such as `ToList` or `Single`.</span></span>

  ![별도 쿼리 예제](read-related-data/_static/separate-queries.png)

* <span data-ttu-id="02d7e-124">명시적 로드 합니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-124">Explicit loading.</span></span> <span data-ttu-id="02d7e-125">먼저 엔터티를 읽으면 관련된 데이터가 검색 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-125">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="02d7e-126">필요한 경우 관련된 데이터를 검색 하는 코드를 작성 합니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-126">You write code that retrieves the related data if it's needed.</span></span> <span data-ttu-id="02d7e-127">Eager 별도 쿼리 로드의 경우 처럼 여러 쿼리에서 결과 로드 명시적 데이터베이스에 전송 합니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-127">As in the case of eager loading with separate queries, explicit loading results in multiple queries sent to the database.</span></span> <span data-ttu-id="02d7e-128">명시적 로드 코드 로드 되도록 탐색 속성을 지정 하는 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-128">The difference is that with explicit loading, the code specifies the navigation properties to be loaded.</span></span> <span data-ttu-id="02d7e-129">Entity Framework Core 1.1에서 사용할 수 있습니다는 `Load` 명시적 로딩 작업을 수행 하는 메서드.</span><span class="sxs-lookup"><span data-stu-id="02d7e-129">In Entity Framework Core 1.1 you can use the `Load` method to do explicit loading.</span></span> <span data-ttu-id="02d7e-130">예:</span><span class="sxs-lookup"><span data-stu-id="02d7e-130">For example:</span></span>

  ![명시적 로드 예제](read-related-data/_static/explicit-loading.png)

* <span data-ttu-id="02d7e-132">지연 로드 합니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-132">Lazy loading.</span></span> <span data-ttu-id="02d7e-133">먼저 엔터티를 읽으면 관련된 데이터가 검색 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-133">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="02d7e-134">그러나는 탐색 속성에 액세스 하려고 처음으로 해당 탐색 속성에 필요한 데이터가 자동으로 검색 됩니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-134">However, the first time you attempt to access a navigation property, the data required for that navigation property is automatically retrieved.</span></span> <span data-ttu-id="02d7e-135">쿼리는 탐색 속성에서 처음으로 데이터를 가져올 하려고 할 때마다 데이터베이스에 전송 됩니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-135">A query is sent to the database each time you try to get data from a navigation property for the first time.</span></span> <span data-ttu-id="02d7e-136">Entity Framework Core 1.0 지연 로드를 지원 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-136">Entity Framework Core 1.0 does not support lazy loading.</span></span>

### <a name="performance-considerations"></a><span data-ttu-id="02d7e-137">성능 고려 사항</span><span class="sxs-lookup"><span data-stu-id="02d7e-137">Performance considerations</span></span>

<span data-ttu-id="02d7e-138">검색 된 모든 엔터티 관련된 데이터가 필요할 경우, 데이터베이스에 전송 하는 단일 쿼리를 검색할 각 엔터티에 대 한 별개의 쿼리와 보다 일반적으로 더 효율적 이므로 최상의 성능을 제공 종종 즉시 로드 합니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-138">If you know you need related data for every entity retrieved, eager loading often offers the best performance, because a single query sent to the database is typically more efficient than separate queries for each entity retrieved.</span></span> <span data-ttu-id="02d7e-139">예를 들어, 각 부서에는 10 개의 관련된 과정입니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-139">For example, suppose that each department has ten related courses.</span></span> <span data-ttu-id="02d7e-140">모든 관련된 데이터를 즉시 로드는 데이터베이스에 왕복만 단일 (조인) 쿼리 및 단일으로 반환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-140">Eager loading of all related data would result in just a single (join) query and a single round trip to the database.</span></span> <span data-ttu-id="02d7e-141">각 부서에 대 한 교육 과정에 대 한 별도 쿼리는 데이터베이스에 11 개 왕복으로 반환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-141">A separate query for courses for each department would result in eleven round trips to the database.</span></span> <span data-ttu-id="02d7e-142">데이터베이스에 추가 왕복 성능이 저하 때는 대기 시간이 깁니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-142">The extra round trips to the database are especially detrimental to performance when latency is high.</span></span>

<span data-ttu-id="02d7e-143">반면에 일부 시나리오에서는 별도 쿼리는 더 효율적입니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-143">On the other hand, in some scenarios separate queries is more efficient.</span></span> <span data-ttu-id="02d7e-144">한 쿼리에서 모든 관련된 데이터를 즉시 로드에는 SQL Server 없습니다 효율적으로 처리 하는 생성 될 매우 복잡 한 조인이 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-144">Eager loading of all related data in one query might cause a very complex join to be generated, which SQL Server can't process efficiently.</span></span> <span data-ttu-id="02d7e-145">또는 즉시 로드 하는 모든 들겠지만 필요한 것 보다 더 많은 데이터를 검색 하기 때문에 별도 쿼리 향상 될 수도 있습니다를 처리 하는 엔터티 집합의 하위 집합에 대 한 엔터티의 탐색 속성에 액세스 해야 하는 경우.</span><span class="sxs-lookup"><span data-stu-id="02d7e-145">Or if you need to access an entity's navigation properties only for a subset of a set of the entities you're processing, separate queries might perform better because eager loading of everything up front would retrieve more data than you need.</span></span> <span data-ttu-id="02d7e-146">성능이 중요 한 경우에 최상의 선택을 하기 위해 두 가지 방식으로 성능을 테스트 하는 가장 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-146">If performance is critical, it's best to test performance both ways in order to make the best choice.</span></span>

## <a name="create-a-courses-page-that-displays-department-name"></a><span data-ttu-id="02d7e-147">부서 이름을 표시 하는 과정 페이지 만들기</span><span class="sxs-lookup"><span data-stu-id="02d7e-147">Create a Courses page that displays Department name</span></span>

<span data-ttu-id="02d7e-148">과정 엔터티 과정에 할당 된 부서 부서 엔터티를 포함 하는 탐색 속성을 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-148">The Course entity includes a navigation property that contains the Department entity of the department that the course is assigned to.</span></span> <span data-ttu-id="02d7e-149">에 있는 부서 엔터티에서 이름 속성을 가져올 과정의 목록에 할당 된 부서 이름을 표시 하려면 해야는 `Course.Department` 탐색 속성입니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-149">To display the name of the assigned department in a list of courses, you need to get the Name property from the Department entity that is in the `Course.Department` navigation property.</span></span>

<span data-ttu-id="02d7e-150">에 대해 동일한 옵션을 사용 하 여 Course 엔터티 형식에 대 한 CoursesController 라는 컨트롤러를 만들고는 **Entity Framework를 사용 하 여 뷰를 포함 된 MVC 컨트롤러** 하 던 이전 학생 컨트롤러에 표시 된 대로 scaffolder는 다음 그림의 경우:</span><span class="sxs-lookup"><span data-stu-id="02d7e-150">Create a controller named CoursesController for the Course entity type, using the same options for the **MVC Controller with views, using Entity Framework** scaffolder that you did earlier for the Students controller, as shown in the following illustration:</span></span>

![Courses 컨트롤러 추가](read-related-data/_static/add-courses-controller.png)

<span data-ttu-id="02d7e-152">열기 *CoursesController.cs* 검사는 `Index` 메서드.</span><span class="sxs-lookup"><span data-stu-id="02d7e-152">Open *CoursesController.cs* and examine the `Index` method.</span></span> <span data-ttu-id="02d7e-153">자동 스 캐 폴딩이에 대 한 즉시 로드 지정는 `Department` 를 사용 하 여 탐색 속성에서 `Include` 메서드.</span><span class="sxs-lookup"><span data-stu-id="02d7e-153">The automatic scaffolding has specified eager loading for the `Department` navigation property by using the `Include` method.</span></span>

<span data-ttu-id="02d7e-154">대체는 `Index` 메서드를 다음 코드로 대 한 보다 적절 한 이름을 사용 하는 `IQueryable` 과정 엔터티를 반환 하는 (`courses` 대신 `schoolContext`):</span><span class="sxs-lookup"><span data-stu-id="02d7e-154">Replace the `Index` method with the following code that uses a more appropriate name for the `IQueryable` that returns Course entities (`courses` instead of `schoolContext`):</span></span>

[!code-csharp[Main](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_RevisedIndexMethod)]

<span data-ttu-id="02d7e-155">열기 *Views/Courses/Index.cshtml* 템플릿 코드를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-155">Open *Views/Courses/Index.cshtml* and replace the template code with the following code.</span></span> <span data-ttu-id="02d7e-156">변경 내용은 강조 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-156">The changes are highlighted:</span></span>

[!code-html[](intro/samples/cu/Views/Courses/Index.cshtml?highlight=4,7,15-17,34-36,44)]

<span data-ttu-id="02d7e-157">스 캐 폴드 코드를 다음과 같이 변경을 했습니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-157">You've made the following changes to the scaffolded code:</span></span>

* <span data-ttu-id="02d7e-158">인덱스에서 제목 Courses로 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-158">Changed the heading from Index to Courses.</span></span>

* <span data-ttu-id="02d7e-159">추가 **번호** 보여 주는 열은 `CourseID` 속성 값입니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-159">Added a **Number** column that shows the `CourseID` property value.</span></span> <span data-ttu-id="02d7e-160">기본적으로 기본 키 이므로 일반적으로 최종 사용자에 게 의미가 스 캐 폴드 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-160">By default, primary keys aren't scaffolded because normally they are meaningless to end users.</span></span> <span data-ttu-id="02d7e-161">그러나이 경우 기본 키 의미 이며 하려는 표시할지를 기준으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-161">However, in this case the primary key is meaningful and you want to show it.</span></span>

* <span data-ttu-id="02d7e-162">변경 된 **부서** 부서 이름을 표시 하는 열입니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-162">Changed the **Department** column to display the department name.</span></span> <span data-ttu-id="02d7e-163">코드 표시는 `Name` 에 로드 되는 부서 엔터티의 속성은 `Department` 탐색 속성:</span><span class="sxs-lookup"><span data-stu-id="02d7e-163">The code displays the `Name` property of the Department entity that's loaded into the `Department` navigation property:</span></span>

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

<span data-ttu-id="02d7e-164">응용 프로그램을 실행 하 고 선택 된 **Courses** 부서 이름의 목록을 보려면 탭 합니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-164">Run the app and select the **Courses** tab to see the list with department names.</span></span>

![Courses 인덱스 페이지](read-related-data/_static/courses-index.png)

## <a name="create-an-instructors-page-that-shows-courses-and-enrollments"></a><span data-ttu-id="02d7e-166">만들기 과정 및 등록을 보여 주는 강사 페이지</span><span class="sxs-lookup"><span data-stu-id="02d7e-166">Create an Instructors page that shows Courses and Enrollments</span></span>

<span data-ttu-id="02d7e-167">이 섹션에서는 강사 페이지를 표시 하기 위해 컨트롤러와 강사 엔터티에 대 한 보기를 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-167">In this section, you'll create a controller and view for the Instructor entity in order to display the Instructors page:</span></span>

![강사 인덱스 페이지](read-related-data/_static/instructors-index.png)

<span data-ttu-id="02d7e-169">이 페이지를 읽고 다음과 같이 관련된 데이터를 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-169">This page reads and displays related data in the following ways:</span></span>

* <span data-ttu-id="02d7e-170">강사 목록은 OfficeAssignment 엔터티에서 관련된 데이터를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-170">The list of instructors displays related data from the OfficeAssignment entity.</span></span> <span data-ttu-id="02d7e-171">강사 및 OfficeAssignment 엔터티는 0 또는 1을 한 관계에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-171">The Instructor and OfficeAssignment entities are in a one-to-zero-or-one relationship.</span></span> <span data-ttu-id="02d7e-172">OfficeAssignment 엔터티에 대 한 즉시 로드를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-172">You'll use eager loading for the OfficeAssignment entities.</span></span> <span data-ttu-id="02d7e-173">이전에 설명한 대로 기본 테이블의 모든 검색 된 행에 관련된 데이터를 필요로 하는 경우 즉시 로드 일반적으로 더 효율적입니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-173">As explained earlier, eager loading is typically more efficient when you need the related data for all retrieved rows of the primary table.</span></span> <span data-ttu-id="02d7e-174">이 경우에 표시 된 모든 강사에 게 사무실 할당을 표시 하려면.</span><span class="sxs-lookup"><span data-stu-id="02d7e-174">In this case, you want to display office assignments for all displayed instructors.</span></span>

* <span data-ttu-id="02d7e-175">사용자가 강사를 선택 하면 관련된 과정 엔터티 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-175">When the user selects an instructor, related Course entities are displayed.</span></span> <span data-ttu-id="02d7e-176">강사 및 과정 엔터티 다 대 다 관계에는입니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-176">The Instructor and Course entities are in a many-to-many relationship.</span></span> <span data-ttu-id="02d7e-177">과정 엔터티 및 관련된 해당 부서 엔터티에 대 한 즉시 로드를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-177">You'll use eager loading for the Course entities and their related Department entities.</span></span> <span data-ttu-id="02d7e-178">이 경우 선택한 강사에 대해서만 courses 필요 하기 때문에 별도 쿼리 더 효율적일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-178">In this case, separate queries might be more efficient because you need courses only for the selected instructor.</span></span> <span data-ttu-id="02d7e-179">그러나이 예제에는 자체 탐색 속성에 있는 엔터티 내에서 탐색 속성에 대 한 즉시 로드를 사용 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-179">However, this example shows how to use eager loading for navigation properties within entities that are themselves in navigation properties.</span></span>

* <span data-ttu-id="02d7e-180">과정을 선택 하면 등록 엔터티 집합에서 관련된 데이터 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-180">When the user selects a course, related data from the Enrollments entity set is displayed.</span></span> <span data-ttu-id="02d7e-181">Course 및 등록 엔터티는 일 대 다 관계에는입니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-181">The Course and Enrollment entities are in a one-to-many relationship.</span></span> <span data-ttu-id="02d7e-182">등록 엔터티 및 해당 관련된 학생 엔터티에 대 한 별도 쿼리를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-182">You'll use separate queries for Enrollment entities and their related Student entities.</span></span>

### <a name="create-a-view-model-for-the-instructor-index-view"></a><span data-ttu-id="02d7e-183">강사 인덱스 보기에 대 한 뷰 모델 만들기</span><span class="sxs-lookup"><span data-stu-id="02d7e-183">Create a view model for the Instructor Index view</span></span>

<span data-ttu-id="02d7e-184">강사 페이지는 서로 다른 세 테이블에서 데이터를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-184">The Instructors page shows data from three different tables.</span></span> <span data-ttu-id="02d7e-185">따라서 각 테이블 중 하나에 대 한 데이터를 보유으로 세 가지 속성을 포함 하는 보기 모델을 만들어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-185">Therefore, you'll create a view model that includes three properties, each holding the data for one of the tables.</span></span>

<span data-ttu-id="02d7e-186">에 *SchoolViewModels* 폴더를 만들 *InstructorIndexData.cs* 기존 코드를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-186">In the *SchoolViewModels* folder, create *InstructorIndexData.cs* and replace the existing code with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="create-the-instructor-controller-and-views"></a><span data-ttu-id="02d7e-187">강사 컨트롤러 및 뷰 만들기</span><span class="sxs-lookup"><span data-stu-id="02d7e-187">Create the Instructor controller and views</span></span>

<span data-ttu-id="02d7e-188">다음 그림에 나와 있는 것 처럼 EF 읽기/쓰기 동작이 포함 된 Instructors 컨트롤러를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-188">Create an Instructors controller with EF read/write actions as shown in the following illustration:</span></span>

![강사 컨트롤러 추가](read-related-data/_static/add-instructors-controller.png)

<span data-ttu-id="02d7e-190">열기 *InstructorsController.cs* using 추가 Viewmodel 네임 스페이스에 대 한 문:</span><span class="sxs-lookup"><span data-stu-id="02d7e-190">Open *InstructorsController.cs* and add a using statement for the ViewModels namespace:</span></span>

[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_Using)]

<span data-ttu-id="02d7e-191">Index 메서드가 관련된 데이터를 즉시 로드의 보기 모델에 저장 하 고 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-191">Replace the Index method with the following code to do eager loading of related data and put it in the view model.</span></span>

[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_EagerLoading)]

<span data-ttu-id="02d7e-192">메서드에 선택적 경로 데이터 (`id`) 및 쿼리 문자열 매개 변수 (`courseID`) 선택한 강사 및 선택한 과정의 ID 값을 제공 하는 합니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-192">The method accepts optional route data (`id`) and a query string parameter (`courseID`) that provide the ID values of the selected instructor and selected course.</span></span> <span data-ttu-id="02d7e-193">매개 변수에서 제공 되는 **선택** 페이지의 하이퍼링크입니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-193">The parameters are provided by the **Select** hyperlinks on the page.</span></span>

<span data-ttu-id="02d7e-194">코드의 보기 모델의 인스턴스를 만들고 강사 목록에 배치 하 여 시작 합니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-194">The code begins by creating an instance of the view model and putting in it the list of instructors.</span></span> <span data-ttu-id="02d7e-195">에 대 한 즉시 로드를 지정 하는 코드는 `Instructor.OfficeAssignment` 및 `Instructor.CourseAssignments` 탐색 속성입니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-195">The code specifies eager loading for the `Instructor.OfficeAssignment` and the `Instructor.CourseAssignments` navigation properties.</span></span> <span data-ttu-id="02d7e-196">내에서 `CourseAssignments` 속성을는 `Course` 속성은 로드 하 고 내에, `Enrollments` 및 `Department` 로드 및 각 속성은 `Enrollment` 엔터티는 `Student` 속성이 로드 합니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-196">Within the `CourseAssignments` property, the `Course` property is loaded, and within that, the `Enrollments` and `Department` properties are loaded, and within each `Enrollment` entity the `Student` property is loaded.</span></span>

[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ThenInclude)]

<span data-ttu-id="02d7e-197">보기에는 항상 OfficeAssignment 엔터티 필요, 하므로을 같은 쿼리에서 인출 하는 더 효율적입니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-197">Since the view always requires the OfficeAssignment entity, it's more efficient to fetch that in the same query.</span></span> <span data-ttu-id="02d7e-198">과정 엔터티는 하지 않고 보다 선택 하는 과정에 페이지를 더 자주 표시할 경우에 단일 쿼리에 여러 개의 쿼리 보다 더 나은 이므로 웹 페이지에서 강사를 선택한 경우 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-198">Course entities are required when an instructor is selected in the web page, so a single query is better than multiple queries only if the page is displayed more often with a course selected than without.</span></span>

<span data-ttu-id="02d7e-199">코드에서 반복 `CourseAssignments` 및 `Course` 의 두 속성만 필요 하기 때문에 `Course`합니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-199">The code repeats `CourseAssignments` and `Course` because you need two properties from `Course`.</span></span> <span data-ttu-id="02d7e-200">첫 번째 문자열 `ThenInclude` 가져옵니다 호출 `CourseAssignment.Course`, `Course.Enrollments`, 및 `Enrollment.Student`합니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-200">The first string of `ThenInclude` calls gets `CourseAssignment.Course`, `Course.Enrollments`, and `Enrollment.Student`.</span></span>

[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ThenInclude&highlight=3-6)]

<span data-ttu-id="02d7e-201">코드의 해당 지점에서 다른 `ThenInclude` 의 탐색 속성에 대 한 것 `Student`는 필요 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-201">At that point in the code, another `ThenInclude` would be for navigation properties of `Student`, which you don't need.</span></span> <span data-ttu-id="02d7e-202">하지만 호출 `Include` 으로 다시 시작 `Instructor` 속성을 지정 하는이 시간 다시 체인을 통해 이동 해야 하므로 `Course.Department` 대신 `Course.Enrollments`합니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-202">But calling `Include` starts over with `Instructor` properties, so you have to go through the chain again, this time specifying `Course.Department` instead of `Course.Enrollments`.</span></span>

[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ThenInclude&highlight=7-9)]

<span data-ttu-id="02d7e-203">다음 코드는 강사가 선택 될 때 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-203">The following code executes when an instructor was selected.</span></span> <span data-ttu-id="02d7e-204">선택한 강사 강사 보기 모델에 있는 목록에서 검색 됩니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-204">The selected instructor is retrieved from the list of instructors in the view model.</span></span> <span data-ttu-id="02d7e-205">뷰 모델 `Courses` 속성에서 해당 강사 과정 엔터티와 로드 `CourseAssignments` 탐색 속성입니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-205">The view model's `Courses` property is then loaded with the Course entities from that instructor's `CourseAssignments` navigation property.</span></span>

[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?range=56-62)]

<span data-ttu-id="02d7e-206">`Where` 메서드 컬렉션을 반환 하지만 조건을 단일 Instructor 엔터티 반환 되 고 해당 메서드 결과에 전달 되는 경우.</span><span class="sxs-lookup"><span data-stu-id="02d7e-206">The `Where` method returns a collection, but in this case the criteria passed to that method result in only a single Instructor entity being returned.</span></span> <span data-ttu-id="02d7e-207">`Single` 메서드가 해당 엔터티에 액세스할 수 있는 단일 Instructor 엔터티 컬렉션 변환 `CourseAssignments` 속성입니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-207">The `Single` method converts the collection into a single Instructor entity, which gives you access to that entity's `CourseAssignments` property.</span></span> <span data-ttu-id="02d7e-208">`CourseAssignments` 속성 포함 `CourseAssignment` 하려는 관련 된 엔터티 `Course` 엔터티.</span><span class="sxs-lookup"><span data-stu-id="02d7e-208">The `CourseAssignments` property contains `CourseAssignment` entities, from which you want only the related `Course` entities.</span></span>

<span data-ttu-id="02d7e-209">사용 된 `Single` 메서드 컬렉션을 알고 있는 경우 컬렉션에 항목을 하나만 갖습니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-209">You use the `Single` method on a collection when you know the collection will have only one item.</span></span> <span data-ttu-id="02d7e-210">단일 메서드는 전달 된 컬렉션은 비어 있거나 둘 이상의 항목이 없는 경우 예외를 throw 합니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-210">The Single method throws an exception if the collection passed to it is empty or if there's more than one item.</span></span> <span data-ttu-id="02d7e-211">대신 `SingleOrDefault`, 값을 반환 하는 기본값 (이 경우 null) 컬렉션이 비어 있는 경우.</span><span class="sxs-lookup"><span data-stu-id="02d7e-211">An alternative is `SingleOrDefault`, which returns a default value (null in this case) if the collection is empty.</span></span> <span data-ttu-id="02d7e-212">그러나이 경우에 여전히 초래 예외 (찾으려고 시도에서 `Courses` 속성에 null 참조), 예외 메시지는 문제의 원인을 477860 덜 명확 하 게 하 고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-212">However, in this case that would still result in an exception (from trying to find a `Courses` property on a null reference), and the exception message would less clearly indicate the cause of the problem.</span></span> <span data-ttu-id="02d7e-213">호출 하는 경우는 `Single` 메서드를 전달할 수도 있습니다는 where 조건 호출 하는 대신는 `Where` 메서드 별도로:</span><span class="sxs-lookup"><span data-stu-id="02d7e-213">When you call the `Single` method, you can also pass in the Where condition instead of calling the `Where` method separately:</span></span>

```csharp
.Single(i => i.ID == id.Value)
```

<span data-ttu-id="02d7e-214">위 코드를 아래 코드 대신 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-214">Instead of:</span></span>

```csharp
.Where(I => i.ID == id.Value).Single()
```

<span data-ttu-id="02d7e-215">다음으로 과정을 선택한 경우 선택한 과정의 보기 모델에는 과정의 목록에서 검색 됩니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-215">Next, if a course was selected, the selected course is retrieved from the list of courses in the view model.</span></span> <span data-ttu-id="02d7e-216">다음 뷰 모델의 `Enrollments` 속성이 해당 과정에서 등록 엔터티와 로드 `Enrollments` 탐색 속성입니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-216">Then the view model's `Enrollments` property is loaded with the Enrollment entities from that course's `Enrollments` navigation property.</span></span>

[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?range=64-69)]

### <a name="modify-the-instructor-index-view"></a><span data-ttu-id="02d7e-217">강사 인덱스 뷰를 수정 합니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-217">Modify the Instructor Index view</span></span>

<span data-ttu-id="02d7e-218">*Views/Instructors/Index.cshtml*, 템플릿 코드를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-218">In *Views/Instructors/Index.cshtml*, replace the template code with the following code.</span></span> <span data-ttu-id="02d7e-219">변경 내용은 강조 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-219">The changes are highlighted.</span></span>

[!code-html[](intro/samples/cu/Views/Instructors/Index1.cshtml?range=1-64&highlight=1,3-7,15-19,24,26-31,41-54,56)]

<span data-ttu-id="02d7e-220">기존 코드를 다음과 같이 변경을 했습니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-220">You've made the following changes to the existing code:</span></span>

* <span data-ttu-id="02d7e-221">모델 클래스를 변경 `InstructorIndexData`합니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-221">Changed the model class to `InstructorIndexData`.</span></span>

* <span data-ttu-id="02d7e-222">페이지 제목을 변경 **인덱스** 를 **강사**합니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-222">Changed the page title from **Index** to **Instructors**.</span></span>

* <span data-ttu-id="02d7e-223">추가 **Office** 표시 하는 열 `item.OfficeAssignment.Location` 경우에만 `item.OfficeAssignment` null입니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-223">Added an **Office** column that displays `item.OfficeAssignment.Location` only if `item.OfficeAssignment` is not null.</span></span> <span data-ttu-id="02d7e-224">(0 또는 1을 한 관계 이기 때문에 있을 수 있습니다 하지 관련된 OfficeAssignment 엔터티.)</span><span class="sxs-lookup"><span data-stu-id="02d7e-224">(Because this is a one-to-zero-or-one relationship, there might not be a related OfficeAssignment entity.)</span></span>

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* <span data-ttu-id="02d7e-225">추가 **Courses** 과정을 표시 하는 열에서 각 강사 배웠습니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-225">Added a **Courses** column that displays courses taught by each instructor.</span></span> <span data-ttu-id="02d7e-226">참조 [명시적 줄 전환을 `@:` ](xref:mvc/views/razor#explicit-line-transition-with-label) 이 razor 구문에 대 한 자세한 합니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-226">See [Explicit Line Transition with `@:`](xref:mvc/views/razor#explicit-line-transition-with-label) for more about this razor syntax.</span></span>

* <span data-ttu-id="02d7e-227">동적으로 추가 하는 추가 된 코드 `class="success"` 에 `tr` 선택한 강사의 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-227">Added code that dynamically adds `class="success"` to the `tr` element of the selected instructor.</span></span> <span data-ttu-id="02d7e-228">부트스트랩 클래스를 사용 하 여 선택된 된 행에 대 한 배경색을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-228">This sets a background color for the selected row using a Bootstrap class.</span></span>

  ```html
  string selectedRow = "";
  if (item.ID == (int?)ViewData["InstructorID"])
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* <span data-ttu-id="02d7e-229">레이블이 지정 된 새 하이퍼링크 추가 **선택** 에에 보내도록 선택한 강의 ID 때문에 있는 다른 각 행에 링크 바로 앞의 `Index` 메서드.</span><span class="sxs-lookup"><span data-stu-id="02d7e-229">Added a new hyperlink labeled **Select** immediately before the other links in each row, which causes the selected instructor's ID to be sent to the `Index` method.</span></span>

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

<span data-ttu-id="02d7e-230">응용 프로그램을 실행 하 고 선택 된 **강사** 탭 합니다. 페이지 관련된 OfficeAssignment 엔터티가 없는 경우 관련된 OfficeAssignment 엔터티 및 빈 테이블 셀의 Location 속성을 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-230">Run the app and select the **Instructors** tab. The page displays the Location property of related OfficeAssignment entities and an empty table cell when there's no related OfficeAssignment entity.</span></span>

![강사 인덱스 페이지에는 아무 선택](read-related-data/_static/instructors-index-no-selection.png)

<span data-ttu-id="02d7e-232">에 *Views/Instructors/Index.cshtml* 파일을 닫는 요소 (끝날 때 파일), 테이블 후 다음 코드를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-232">In the *Views/Instructors/Index.cshtml* file, after the closing table element (at the end of the file), add the following code.</span></span> <span data-ttu-id="02d7e-233">이 코드는 강사가 강사 선택 된 경우와 관련 된 과정의 목록이 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-233">This code displays a list of courses related to an instructor when an instructor is selected.</span></span>

[!code-html[](intro/samples/cu/Views/Instructors/Index1.cshtml?range=66-101)]

<span data-ttu-id="02d7e-234">이 코드를 읽고는 `Courses` 과정의 목록을 표시 하려면 보기 모델의 속성입니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-234">This code reads the `Courses` property of the view model to display a list of courses.</span></span> <span data-ttu-id="02d7e-235">또한 제공는 **선택** 하이퍼링크에 선택한 과정의 ID를 전송 하는 `Index` 동작 메서드.</span><span class="sxs-lookup"><span data-stu-id="02d7e-235">It also provides a **Select** hyperlink that sends the ID of the selected course to the `Index` action method.</span></span>

<span data-ttu-id="02d7e-236">페이지를 새로 고치고 강사를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-236">Refresh the page and select an instructor.</span></span> <span data-ttu-id="02d7e-237">이제 선택한 강사에 할당 하는 과정을 표시 하는 표를 표시 및 각 과정에 대 한 할당 된 부서 이름을 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-237">Now you see a grid that displays courses assigned to the selected instructor, and for each course you see the name of the assigned department.</span></span>

![강사 인덱스 페이지 강사 선택](read-related-data/_static/instructors-index-instructor-selected.png)

<span data-ttu-id="02d7e-239">방금 추가한 코드 블록을 한 후 다음 코드를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-239">After the code block you just added, add the following code.</span></span> <span data-ttu-id="02d7e-240">해당 과정을 선택한 경우에 과정에서 등록 된 학생의 목록을 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-240">This displays a list of the students who are enrolled in a course when that course is selected.</span></span>

[!code-html[](intro/samples/cu/Views/Instructors/Index1.cshtml?range=103-125)]

<span data-ttu-id="02d7e-241">이 코드는 등록 과정에 학생의 목록을 표시 하기 위해 뷰 모델의 등록 속성을 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-241">This code reads the Enrollments property of the view model in order to display a list of students enrolled in the course.</span></span>

<span data-ttu-id="02d7e-242">페이지를 다시 새로 고치십시오 하 고 강사를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-242">Refresh the page again and select an instructor.</span></span> <span data-ttu-id="02d7e-243">그런 다음 등록 된 학생 자신의 등급의 목록을 보려면 과정을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-243">Then select a course to see the list of enrolled students and their grades.</span></span>

![강사 인덱스 페이지 강사 및 선택한 과정](read-related-data/_static/instructors-index.png)

## <a name="explicit-loading"></a><span data-ttu-id="02d7e-245">명시적 로드</span><span class="sxs-lookup"><span data-stu-id="02d7e-245">Explicit loading</span></span>

<span data-ttu-id="02d7e-246">강사 목록을 검색 하는 경우 *InstructorsController.cs*를 즉시 로드에 대 한 지정한는 `CourseAssignments` 탐색 속성입니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-246">When you retrieved the list of instructors in *InstructorsController.cs*, you specified eager loading for the `CourseAssignments` navigation property.</span></span>

<span data-ttu-id="02d7e-247">사용자가 거의 선택한 강사 및 과정에서 등록을 확인 하려면 예상 가정 합니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-247">Suppose you expected users to only rarely want to see enrollments in a selected instructor and course.</span></span> <span data-ttu-id="02d7e-248">이 경우 다음 요청 될 경우에 등록 데이터를 로드 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-248">In that case, you might want to load the enrollment data only if it's requested.</span></span> <span data-ttu-id="02d7e-249">명시적 로드 작업을 수행 하는 방법의 예를 보려면 대체는 `Index` 등록에 대 한 즉시 로드를 제거 하 고 해당 속성을 명시적으로 로드 하는 다음 코드를 사용 하 여 메서드.</span><span class="sxs-lookup"><span data-stu-id="02d7e-249">To see an example of how to do explicit loading, replace the `Index` method with the following code, which removes eager loading for Enrollments and loads that property explicitly.</span></span> <span data-ttu-id="02d7e-250">코드 변경 내용 강조 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-250">The code changes are highlighted.</span></span>

[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ExplicitLoading&highlight=23-29)]

<span data-ttu-id="02d7e-251">새 코드 삭제는 *ThenInclude* instructor 엔터티를 검색 하는 코드에서 등록 데이터에 대 한 메서드를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-251">The new code drops the *ThenInclude* method calls for enrollment data from the code that retrieves instructor entities.</span></span> <span data-ttu-id="02d7e-252">강사 및 과정을 선택 하는 경우 선택한 과정에 대 한 등록 엔터티 및 각 등록에 대해 학생 엔터티 강조 표시 된 코드를 검색 합니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-252">If an instructor and course are selected, the highlighted code retrieves Enrollment entities for the selected course, and Student entities for each Enrollment.</span></span>

<span data-ttu-id="02d7e-253">실행 데이터를 검색 하는 방법을 변경 하지만 이동 강사 인덱스 페이지를 지금 하는 응용 프로그램 페이지에 표시 되는 내용에 차이가 없어야 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-253">Run the app, go to the Instructors Index page now and you'll see no difference in what's displayed on the page, although you've changed how the data is retrieved.</span></span>

## <a name="summary"></a><span data-ttu-id="02d7e-254">요약</span><span class="sxs-lookup"><span data-stu-id="02d7e-254">Summary</span></span>

<span data-ttu-id="02d7e-255">이제 사용한 즉시 로드 한 쿼리 및 여러 개의 쿼리 탐색 속성에 관련된 데이터를 읽을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-255">You've now used eager loading with one query and with multiple queries to read related data into navigation properties.</span></span> <span data-ttu-id="02d7e-256">다음 자습서에서 관련된 데이터를 업데이트 하는 방법을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="02d7e-256">In the next tutorial you'll learn how to update related data.</span></span>

>[!div class="step-by-step"]
><span data-ttu-id="02d7e-257">[이전](complex-data-model.md)
>[다음](update-related-data.md)</span><span class="sxs-lookup"><span data-stu-id="02d7e-257">[Previous](complex-data-model.md)
[Next](update-related-data.md)</span></span>  
