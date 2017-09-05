---
title: "ASP.NET Core MVC EF 코어-고급-10 / 10"
author: tdykstra
description: "이 자습서에서는 고급의 Entity Framework Core를 사용 하는 ASP.NET 웹 응용 프로그램 개발 기능 수행 하는 경우 고려해 야 하는 데 유용 하는 여러 항목을 소개 합니다."
keywords: "ASP.NET Core, Entity Framework Core 원시 sql 검사 sql, 저장소 패턴, 작업 패턴을 자동 변경 내용 검색 단위 기존 데이터베이스"
ms.author: tdykstra
manager: wpickett
ms.date: 03/15/2017
ms.topic: get-started-article
ms.assetid: 92a2986a-d005-4ff6-9559-6657fd466bb7
ms.technology: aspnet
ms.prod: asp.net-core
uid: data/ef-mvc/advanced
ms.openlocfilehash: fa5e0a66f22cc14f34d05481ce2e4381085d122d
ms.sourcegitcommit: 4e84d8bf5f404bb77f3d41665cf7e7374fc39142
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/05/2017
---
# <a name="advanced-topics---ef-core-with-aspnet-core-mvc-tutorial-10-of-10"></a>고급 항목-EF 코어 ASP.NET Core MVC 자습서 (10 / 10)

여 [Tom Dykstra](https://github.com/tdykstra) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)

Contoso 대학 샘플 웹 응용 프로그램에는 Entity Framework Core 및 Visual Studio를 사용 하 여 ASP.NET Core MVC 웹 응용 프로그램을 만드는 방법을 보여 줍니다. 자습서 시리즈에 대 한 정보를 참조 하십시오. [시리즈의 첫 번째 자습서](intro.md)합니다.

이전 자습서에서 구현한 계층당 하나의 테이블 상속 합니다. 이 자습서에서는 고급의 Entity Framework Core를 사용 하는 ASP.NET Core 웹 응용 프로그램 개발 기능 수행 하는 경우 고려해 야 하는 데 유용 하는 여러 항목을 소개 합니다.

## <a name="raw-sql-queries"></a>원시 SQL 쿼리

Entity Framework 사용의 장점 중 하나를 방지할 수 제한 된 데이터를 저장 하는 특정 방법에 가깝게 너무 코드입니다. 생성 하 여 SQL 쿼리 및 명령, 직접 작성 하지 않아도 하므로 수행 합니다. 하지만 사용자가 직접 만든 특정 SQL 쿼리를 실행 해야 할 때 예외적인 경우도 있습니다. 이러한 시나리오에는 엔터티 프레임 워크 코드의 첫 번째 API SQL 명령을 데이터베이스에 직접 전달할 수 있도록 하는 메서드를 포함 합니다. EF Core 1.0에서 다음 옵션을 선택할:

* 사용 하 여 `DbSet.FromSql` 엔터티 형식을 반환 하는 쿼리에 대 한 메서드. 반환 된 개체에 필요한 형식 이어야 합니다는 `DbSet` 개체 및 이러한는 자동으로 데이터베이스 컨텍스트에서 추적 하지 않는 한 있습니다 [추적 해제](crud.md#no-tracking-queries)합니다.

* 사용 하 여 `Database.ExecuteSqlCommand` 쿼리가 아닌 명령에 대 한 합니다.

엔터티 지원 하지 않는 형식을 반환 하는 쿼리를 실행 해야 하는 경우 ADO.NET EF에서 제공 하는 데이터베이스 연결으로 사용할 수 있습니다. 반환 된 데이터 엔터티 형식을 검색 하려면이 메서드를 사용 하는 경우에 데이터베이스 컨텍스트에 의해 추적 되지 않습니다.

항상 true 인 웹 응용 프로그램에서 SQL 명령을 실행 하는 경우, 사이트 SQL 삽입 공격 으로부터 보호 하기 위해 예방 조치 수행 해야 합니다. 작업을 수행 하는 한 가지 방법은 웹 페이지를 제출한 문자열 SQL 명령으로 해석할 수 없는 되도록 매개 변수가 있는 쿼리를 사용 하는 것입니다. 이 자습서에서는 사용자 입력을 쿼리에 통합 하는 경우 매개 변수가 있는 쿼리를 사용 합니다.

## <a name="call-a-query-that-returns-entities"></a>엔터티를 반환 하는 쿼리를 호출 합니다.

`DbSet<TEntity>` 클래스 형식의 엔터티를 반환 하는 쿼리를 실행 하는 데 사용할 수 있는 메서드를 제공 `TEntity`합니다. 이 방법을 보려면 있습니다의 코드를 변경 합니다는 `Details` 부서 컨트롤러의 메서드.

*DepartmentsController.cs*에 `Details` 메서드를 사용 하는 부서를 검색 하는 코드를 대체 한 `FromSql` 다음 강조 표시 된 코드에 나와 있는 것 처럼 메서드 호출:

[!code-csharp[Main](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_RawSQL&highlight=8,9,10,13)]

새 코드는 올바르게 작동 하는지 확인 하려면 선택은 **부서** 탭 한 다음 **세부 정보** 부서 중 하나에 대 한 합니다.

![부서 세부 정보](advanced/_static/department-details.png)

## <a name="call-a-query-that-returns-other-types"></a>다른 형식을 반환 하는 쿼리를 호출 합니다.

각 등록 날짜에 대 한 학생 수를 보여 주는 정보 페이지에 대 한 학생 통계 표에서 이전 만들었습니다. 학생 엔터티 집합에서 데이터를 가져왔습니다 (`_context.Students`) LINQ의 목록으로 결과 프로젝션 하는 데 사용 하 고 `EnrollmentDateGroup` 모델 개체를 보고 합니다. LINQ를 사용 하지 않고 자체 sql 문을 작성 하려는 가정 합니다. SQL 쿼리를 실행 해야 작업을 수행 하는 이외의 형식을 반환 엔터티 개체입니다. EF Core 1.0에서 작업을 수행 하는 한 가지 방법은 ADO.NET 코드를 작성 하 고 EF에서 데이터베이스 연결을 가져옵니다.

*HomeController.cs*, 대체 된 `About` 메서드를 다음 코드로:

[!code-csharp[Main](intro/samples/cu/Controllers/HomeController.cs?name=snippet_UseRawSQL&highlight=3-32)]

사용 하 여 추가 문:

[!code-csharp[Main](intro/samples/cu/Controllers/HomeController.cs?name=snippet_Usings2)]

정보 페이지를 실행 합니다. 이전과 동일한 데이터를 표시 합니다.

![페이지 정보](advanced/_static/about.png)

## <a name="call-an-update-query"></a>업데이트 쿼리를 호출 합니다.

Contoso 대학 관리자가 모든 과정에 대 한 크레딧의 수를 변경 하는 등 데이터베이스의 전역 변경을 수행 하려는 가정 합니다. 대학교 많은 수의 과목 있으면 수 없기 엔터티로 모두 검색 하 고 개별적으로 변경 하 여 효율적입니다. 이 섹션의 사용자는 모든 과정에 대 한 크레딧의 수를 변경 하는 인수를 지정할 수 있게 하는 웹 페이지를 구현 하 고 SQL UPDATE 문을 실행 하 여 변경할 수 있습니다. 웹 페이지는 다음 그림과 같이 표시 됩니다.

![업데이트 과정 크레딧 페이지](advanced/_static/update-credits.png)

*CoursesContoller.cs*, HttpGet 및 HttpPost UpdateCourseCredits 메서드 추가:

[!code-csharp[Main](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_UpdateGet)]

[!code-csharp[Main](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_UpdatePost)]

컨트롤러 HttpGet 요청을 처리할 때 아무 것도 반환에 `ViewData["RowsAffected"]`, 앞의 그림에 나와 있는 것 처럼 뷰의 빈 텍스트 상자 및 전송 단추를 표시 합니다.

경우는 **업데이트** 단추를 클릭 하면 HttpPost 메서드를 호출 하 고 승수에 텍스트 상자에 입력 된 값입니다. 다음 코드를 실행 과정을 업데이트 하 고 보기에 영향을 받는 행 수를 반환 하는 SQL `ViewData`합니다. 뷰를 가져옵니다는 `RowsAffected` 값을 업데이트 된 행의 수를 표시 합니다.

**솔루션 탐색기**를 마우스 오른쪽 단추로 클릭는 *뷰/Courses* 폴더를 마우스 클릭 한 다음 **추가 > 새 항목**합니다.

에 **새 항목 추가** 대화 상자에서 클릭 **ASP.NET** 아래 **설치 됨** 왼쪽된 창에서 클릭 **MVC 뷰 페이지**, 새뷰이름을지정하고 *UpdateCourseCredits.cshtml*합니다.

*Views/Courses/UpdateCourseCredits.cshtml*, 템플릿 코드를 다음 코드로 바꿉니다.

[!code-html[Main](intro/samples/cu/Views/Courses/UpdateCourseCredits.cshtml)]

실행의 `UpdateCourseCredits` 메서드를 선택 하 여는 **Courses** 탭에서 다음 추가 "/ UpdateCourseCredits" 브라우저의 주소 표시줄에 URL의 끝에 (예: `http://localhost:5813/Courses/UpdateCourseCredits`). 텍스트 상자에 숫자를 입력 합니다.

![업데이트 과정 크레딧 페이지](advanced/_static/update-credits.png)

**업데이트**를 클릭합니다. 영향을 받는 행 수가 표시 됩니다.

![업데이트 과정 크레딧 영향을 받는 행을 페이지](advanced/_static/update-credits-rows-affected.png)

클릭 **목록으로 돌아가기** 크레딧의 수정 된 번호로 과정의 목록을 볼 수 있습니다.

Note 프로덕션 코드를 업데이트 하는 항상 결과 유효한 데이터를 확인 합니다. 여기에 표시 된 간소화 된 코드 수 크레딧 충분히 결과적으로 5 보다 큰 수를 곱합니다. (의 `Credits` 속성에는 `[Range(0, 5)]` 특성입니다.) 업데이트 쿼리 작동 하지만 잘못 된 데이터 크레딧 수는 5 개 가정 하는 시스템의 다른 부분에서 예기치 않은 결과가 발생할 수 있습니다.

원시 SQL 쿼리 하는 방법에 대 한 자세한 내용은 참조 [원시 SQL 쿼리](https://docs.microsoft.com/ef/core/querying/raw-sql)합니다.

## <a name="examine-sql-sent-to-the-database"></a>SQL 데이터베이스에 전송 검사

데이터베이스에 전송 되는 실제 SQL 쿼리를 볼 수 많은 도움이 됩니다. ASP.NET Core에 대 한 기본 제공 로깅 기능 쿼리 및 업데이트에 대 한 SQL을 포함 하는 로그를 쓰지 EF 코어에서 자동으로 사용 됩니다. 이 섹션에서는 SQL 로깅의 몇 가지 예를 볼 수 있습니다.

열기 *StudentsController.cs* 및는 `Details` 에 중단점을 설정 하는 메서드는 `if (student == null)` 문.

디버그 모드에서 응용 프로그램을 실행 하 고 학생에 대 한 세부 정보 페이지로 이동 합니다.

이동 하는 **출력** 디버그 표시 창 출력을 보고, 쿼리를 참조 하십시오.

```
Microsoft.EntityFrameworkCore.Database.Command:Information: Executed DbCommand (56ms) [Parameters=[@__id_0='?'], CommandType='Text', CommandTimeout='30']
SELECT TOP(2) [s].[ID], [s].[Discriminator], [s].[FirstName], [s].[LastName], [s].[EnrollmentDate]
FROM [Person] AS [s]
WHERE ([s].[Discriminator] = N'Student') AND ([s].[ID] = @__id_0)
ORDER BY [s].[ID]
Microsoft.EntityFrameworkCore.Database.Command:Information: Executed DbCommand (122ms) [Parameters=[@__id_0='?'], CommandType='Text', CommandTimeout='30']
SELECT [s.Enrollments].[EnrollmentID], [s.Enrollments].[CourseID], [s.Enrollments].[Grade], [s.Enrollments].[StudentID], [e.Course].[CourseID], [e.Course].[Credits], [e.Course].[DepartmentID], [e.Course].[Title]
FROM [Enrollment] AS [s.Enrollments]
INNER JOIN [Course] AS [e.Course] ON [s.Enrollments].[CourseID] = [e.Course].[CourseID]
INNER JOIN (
    SELECT TOP(1) [s0].[ID]
    FROM [Person] AS [s0]
    WHERE ([s0].[Discriminator] = N'Student') AND ([s0].[ID] = @__id_0)
    ORDER BY [s0].[ID]
) AS [t] ON [s.Enrollments].[StudentID] = [t].[ID]
ORDER BY [t].[ID]
```

알 수 여기 놀라운 수 있는: 최대 2 개의 행을 선택 하는 SQL (`TOP(2)`) Person 테이블 로부터 합니다. `SingleOrDefaultAsync` 메서드는 서버에서 1 개 행으로 확인 되지 않았습니다. 그 이유는 다음과 같습니다.

* 쿼리에서 여러 행을 반환 하는 경우이 메서드는 null을 반환 합니다.
* 쿼리에서 여러 행을 반환 여부를 확인 하려면 EF이 2 개 이상 반환 하는 경우를 확인 해야 합니다.

디버그 모드를 사용 하에 로깅 출력을 보려면 중단점에서 중지 없는 참고는 **출력** 창. 결과 확인 하려는 시점 로깅을 중지할 수 편리한 방법 뿐입니다. 로깅 계속 하는 경우 그럴 필요가, 하 한에 관심이 파트 찾을 돌아가기 스크롤해야 합니다.

## <a name="repository-and-unit-of-work-patterns"></a>작업 패턴의 단위 및 저장소

대부분의 개발자는 Entity Framework와 함께 사용할 수 있는 코드 주위에서 래퍼로 저장소와 단위 작업 패턴을 구현 하는 코드를 작성 합니다. 이러한 패턴은 데이터 액세스 계층 및 응용 프로그램의 비즈니스 논리 계층 간에 추상화 계층을 만드는 데 사용 됩니다. 이러한 패턴을 구현 하는 데이터 저장소의 변경 내용 으로부터 응용 프로그램을 분리 하는 데 도움이 및 자동화 된 단위 테스트 또는 테스트 기반 개발 (TDD)으로 기여할 수입니다. 그러나 이러한 패턴을 구현 하는 추가 코드를 작성은 여러 가지 이유로 EF를 사용 하는 응용 프로그램에 적합 합니다.

* EF 컨텍스트 클래스 자체 데이터 저장소 관련 코드에서 코드를 분리 합니다.

* EF 컨텍스트 클래스 EF를 사용 하 여 수행 하는 데이터베이스에 대 한 작업 단위가 클래스 업데이트 작동할 수 있습니다.

* EF는 리포지토리에 코드를 작성 하지 않고도 TDD를 구현 하기 위한 기능이 있습니다.

저장소와 단위 작업 패턴을 구현 하는 방법에 대 한 정보를 참조 하십시오. [이 자습서 시리즈의 Entity Framework 5 버전](https://docs.microsoft.com/aspnet/mvc/overview/older-versions/getting-started-with-ef-5-using-mvc-4/implementing-the-repository-and-unit-of-work-patterns-in-an-asp-net-mvc-application)합니다.

Entity Framework Core 테스트에 사용할 수 있는 메모리 내 데이터베이스 공급자를 구현 합니다. 자세한 내용은 참조 [with InMemory 테스트](https://docs.microsoft.com/ef/core/miscellaneous/testing/in-memory)합니다.

## <a name="automatic-change-detection"></a>자동 변경 내용 검색

Entity Framework 엔터티의 현재 값과 원래 값을 비교 하 여 엔터티의 변경 된 방식을 (및 따라서로 업데이트를 데이터베이스에 전송 해야 할)를 결정 합니다. 엔터티 쿼리 하거나 연결 하는 경우 원래 값 저장 됩니다. 자동 변경 내용 검색 발생 하는 메서드 중 일부는 다음과 같습니다.

* DbContext.SaveChanges

* DbContext.Entry

* ChangeTracker.Entries

많은 엔터티를 추적 하 고 있는 경우 루프에서 여러 번 다음이 방법 중 하나 호출 하면 변경 내용 자동 감지를 사용 하 여 일시적으로 해제 하 여 성능 향상을 발생할 수 있습니다는 `ChangeTracker.AutoDetectChangesEnabled` 속성입니다. 예:

```csharp
_context.ChangeTracker.AutoDetectChangesEnabled = false;
```

## <a name="entity-framework-core-source-code-and-development-plans"></a>Entity Framework Core 소스 코드 및 개발 계획

Entity Framework Core에 대 한 소스 코드에서 제공 됩니다. [https://github.com/aspnet/EntityFramework](https://github.com/aspnet/EntityFramework)합니다. 소스 코드 외에도 있습니다 수 야간 빌드 가져오기, 추적 실행, 기능 사양, 디자인 회의 노트 [이후 개발에 대 한 로드맵](https://github.com/aspnet/EntityFramework/wiki/Roadmap), 등입니다. 버그를 보고할 수 있습니다 및 EF 소스 코드에 고유한 향상 된 기능에 영향을 줄 수 있습니다.

소스 코드를 연 하지만 Entity Framework 코어는 Microsoft 제품으로 완전히 지원 됩니다. Microsoft Entity Framework 팀 기여 허용 되는 제어를 유지 하 고 각 릴리스의 품질을 보장할 수 있는 모든 코드 변경 내용을 테스트 합니다.

## <a name="reverse-engineer-from-existing-database"></a>기존 데이터베이스 리버스 엔지니어링

리버스 엔지니어링 하는 기존 데이터베이스에서 엔터티 클래스를 포함 하 여 데이터 모델을 사용 하 여는 [스 캐 폴드 dbcontext](https://docs.microsoft.com/ef/core/miscellaneous/cli/powershell#scaffold-dbcontext) 명령입니다. 참조는 [-시작 자습서](https://docs.microsoft.com/ef/core/get-started/aspnetcore/existing-db)합니다.

<a id="dynamic-linq">
## <a name="use-dynamic-linq-to-simplify-sort-selection-code"></a>동적 LINQ를 사용 하 여 정렬 선택 코드를 단순화 합니다.

[이 시리즈의 세 번째 자습서](sort-filter-page.md) 열 이름은 하드 코딩 하 여 LINQ 코드를 작성 하는 방법을 보여 줍니다는 `switch` 문. 중에서 선택 하려면 두 개의 열이 정상적으로 작동 하지만 코드 자세한 정보를 가져올 수 많은 열이 있는 경우. 이러한 문제를 해결 하기 위해 사용할 수 있습니다는 `EF.Property` 메서드를 문자열로 속성의 이름을 지정 합니다. 이 방법은 실행 하려면 대체는 `Index` 에서 메서드는 `StudentsController` 를 다음 코드로 합니다.

[!code-csharp[Main](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_DynamicLinq)]

## <a name="next-steps"></a>다음 단계

이 일련의 ASP.NET MVC 응용 프로그램에서 Entity Framework Core를 사용 하는 자습서를 완료 했습니다.

EF 코어에 대 한 자세한 내용은 참조는 [Entity Framework Core 설명서](https://docs.microsoft.com/ef/core)합니다. 책 제공 됩니다.: [동작에서 Entity Framework Core](https://www.manning.com/books/entity-framework-core-in-action)합니다.

작성 한 후 웹 응용 프로그램을 배포 하는 방법에 대 한 정보를 참조 하십시오. [게시 및 배포](../../publishing/index.md)합니다.

인증 및 권한, 같은 ASP.NET Core MVC와 관련 된 다른 항목에 대 한 정보에 대 한 참조는 [ASP.NET Core 설명서](https://docs.microsoft.com/aspnet/core/)합니다.

## <a name="acknowledgments"></a>승인

Tom Dykstra 및 Rick Anderson (twitter @RickAndMSFT)이이 자습서를 작성 합니다. Rowan, 디에고 Vega 및 Entity Framework 팀의 다른 멤버가 코드 검토와 보조 및 자습서에 대 한 코드 작성 했다면 하는 동안 발생 하는 문제를 디버그 하는 데 도움을 준 합니다.

## <a name="common-errors"></a>일반적인 오류  

### <a name="contosouniversitydll-used-by-another-process"></a>다른 프로세스에서 사용 하는 ContosoUniversity.dll

오류 메시지:

> 열 수 없습니다 '... bin\Debug\netcoreapp1.0\ContosoUniversity.dll' 쓰기용-' 프로세스는 파일에 액세스할 수 없습니다 '... \bin\Debug\netcoreapp1.0\ContosoUniversity.dll' 다른 프로세스에서 사용 되 고 있으므로 합니다.

해결책:

IIS express 사이트를 중지 합니다. 로 이동는 Windows 시스템 트레이 찾기 IIS Express 및 해당 아이콘을 마우스 오른쪽 단추로 클릭, Contoso 대학 사이트를 클릭 한 다음 **중지 사이트**합니다.

### <a name="migration-scaffolded-with-no-code-in-up-and-down-methods"></a>메서드 위쪽 및 아래쪽에서 코드가 없는 스 캐 폴드 된 마이그레이션

가능한 원인:

자동으로 EF CLI 명령을 닫고 코드 파일을 저장 하지 않습니다. 실행할 때 변경 하지 않은 경우는 `migrations add` 명령, EF 변경 내용을 찾을 수 없습니다.

해결책:

실행의 `migrations remove` 명령 코드 변경 내용을 저장 하 고 다시 실행의 `migrations add` 명령입니다.

### <a name="errors-while-running-database-update"></a>실행 중인 데이터베이스를 업데이트 하는 동안 오류

기존 데이터를 포함 하는 데이터베이스의 스키마를 변경 하는 경우 다른 오류를 얻을 수는 것이 불가능 합니다. 마이그레이션 오류를 해결할 수 없으면 발생 하는 경우 연결 문자열에 데이터베이스 이름을 변경 하거나 데이터베이스를 삭제 합니다. 새 데이터베이스와 함께 마이그레이션할 데이터가 더 않으며 update-database 명령을 작업이 오류 없이 완료 가능성이 훨씬 더 높습니다.

가장 간단한 방법은 데이터베이스의 이름을 바꾸려면 *appsettings.json*합니다. 다음에 실행할 때 `database update`, 새 데이터베이스가 생성 됩니다.

SSOX에 데이터베이스를 삭제 하려면 데이터베이스를 마우스 오른쪽 단추로 클릭 합니다 **삭제**, 한 다음는 **데이터베이스 삭제** 대화 상자 선택 **기존 연결 닫기** 클릭 **정상**합니다.

실행 CLI를 사용 하 여 데이터베이스를 삭제 하는 `database drop` CLI 명령을:

```console
dotnet ef database drop
```

### <a name="error-locating-sql-server-instance"></a>오류 찾기 SQL Server 인스턴스

오류 메시지:

> SQL Server에 연결을 설정 하는 동안 네트워크 관련 또는 인스턴스 관련 오류가 발생 했습니다. 서버를 찾을 수 없거나 액세스할 수 없습니다. 인스턴스 이름이 올바르고 SQL Server가 원격 연결을 허용하도록 구성되어 있는지 확인합니다. (공급자: SQL 네트워크 인터페이스, 오류: 26-서버/인스턴스 지정 된 찾기 오류)

해결책:

연결 문자열을 확인 합니다. 데이터베이스 파일을 수동으로 삭제 한 경우 새 데이터베이스를 다시 시작 하도록 생성 문자열에 데이터베이스의 이름을 변경 합니다.

>[!div class="step-by-step"]
[이전](inheritance.md)
