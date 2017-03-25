---
title: "ASP.NET Core MVC EF와 핵심-고급-10 / 10 | Microsoft 문서"
author: tdykstra
description: "이 자습서에서는 Entity Framework 코어를 사용 하는 ASP.NET 웹 응용 프로그램 개발의 기본 사항을 초과할 때 알고 있어야 하는 유용한 몇 가지 항목을 소개 합니다."
keywords: "ASP.NET Core, Entity Framework 코어, 원시 sql 검사 sql, 리포지토리 패턴, 작업 패턴을 기본적으로 자동 변경 내용 검색 단위 기존 데이터베이스"
ms.author: tdykstra
manager: wpickett
ms.date: 03/15/2017
ms.topic: article
ms.assetid: 92a2986a-d005-4ff6-9559-6657fd466bb7
ms.technology: aspnet
ms.prod: asp.net-core
uid: data/ef-mvc/advanced
translationtype: Machine Translation
ms.sourcegitcommit: 115a74c97de6052ec707ee164641f9c41224b9b3
ms.openlocfilehash: b59e75bdf16d6bb0bb18cd90c78294b527d15680
ms.lasthandoff: 03/23/2017

---

# <a name="advanced-topics---ef-core-with-aspnet-core-mvc-tutorial-10-of-10"></a>고급 항목-EF 코어 ASP.NET 핵심 MVC 자습서 (10 / 10)

여 [Tom Dykstra](https://github.com/tdykstra) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)

Contoso University 샘플 웹 응용 프로그램에는 Entity Framework 코어 1.0 및 Visual Studio 2015를 사용 하 여 핵심 1.0 ASP.NET MVC 웹 응용 프로그램을 만드는 방법을 보여 줍니다. 자습서 시리즈에 대 한 정보를 참조 하십시오. [시리즈의 첫 번째 자습서](intro.md)합니다.

이전 자습서에서 계층당 하나의 테이블 상속을 구현 합니다. 이 자습서에서는 Entity Framework 코어를 사용 하는 ASP.NET 웹 응용 프로그램 개발의 기본 사항을 초과할 때 알고 있어야 하는 유용한 몇 가지 항목을 소개 합니다.

## <a name="raw-sql-queries"></a>원시 SQL 쿼리

Entity Framework의 장점 중 하나를 방지할 수 특정 메서드의 데이터를 저장 하기에 너무 밀접 하 게 코드를 연결입니다. 생성 하 여 SQL 쿼리 및 명령, 직접 작성 하지 않아도 하므로 수행 합니다. 하지만 사용자가 수동으로 만든 특정 SQL 쿼리를 실행 해야 할 때 예외적인 경우도 있습니다. 이러한 시나리오에서는 Entity Framework Code First API SQL 명령을 데이터베이스에 직접 전달할 수 있도록 하는 메서드를 포함 합니다. 다음과 같은 옵션이 있습니다 EF 코어 1.0에서:

* 사용 하 여 `DbSet.FromSql` 엔터티 형식을 반환 하는 쿼리에 대 한 메서드. 반환 된 개체에 필요한 형식 이어야 합니다는 `DbSet` 않으며 개체를 자동으로 추적할지 데이터베이스 컨텍스트에서 하지 않는 한 있습니다 [추적 해제](crud.md#no-tracking-queries)합니다.

* 사용 하 여 `Database.ExecuteSqlCommand` 쿼리가 아닌 명령에 대 한 합니다.

엔터티 지원 하지 않는 형식을 반환 하는 쿼리를 실행 해야 하는 경우에 EF에서 제공 하는 데이터베이스 연결 된 ADO.NET을 사용할 수 있습니다. 반환 된 데이터는 엔터티 형식을 검색 하려면이 메서드를 사용 하는 경우에 데이터베이스 컨텍스트에 의해 추적 되지 않습니다.

항상 true 인 웹 응용 프로그램에서 SQL 명령을 실행 하는 경우, 사이트 SQL 주입 공격 으로부터 보호 하기 위해 예방 조치를 수행 해야 합니다. 한 가지 방법으로 매개 변수가 있는 쿼리를 사용 하 여 SQL 명령으로 웹 페이지를 통해 제출 된 문자열을 해석할 수 없는 되도록 하는 것입니다. 이 자습서에서는 쿼리를 사용자 입력을 통합 하는 경우 매개 변수가 있는 쿼리를 사용 합니다.

## <a name="call-a-query-that-returns-entities"></a>엔터티를 반환 하는 쿼리를 호출 합니다.

`DbSet<TEntity>` 클래스 형식의 엔터티를 반환 하는 쿼리를 실행 하는 데 사용할 수 있는 메서드를 제공 `TEntity`합니다. 이 방법을 보려면 사용자의 코드를 변경 합니다는 `Details` 부서 컨트롤러의 메서드에 있습니다.

*DepartmentsController.cs*에 `Details` 메서드를 사용 하는 부서를 검색 하는 코드는 `FromSql` 메서드 호출에서 다음과 같은 강조 표시 된 코드에 나온 대로:

[!code-csharp[주](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_RawSQL&highlight=8,9,10,13)]

을 새 코드가 올바르게 작동 하는지 확인 하려면 선택은 **부서** 탭 차례로 **세부 정보** 부서 하나에 대 한 합니다.

![부서 세부 정보](advanced/_static/department-details.png)

## <a name="call-a-query-that-returns-other-types"></a>다른 형식을 반환 하는 쿼리를 호출 합니다.

앞서 각 등록 날짜에 대 한 학생 수를 표시 되는 정보 페이지에 대 한 학생 통계 표를 만들었습니다. 학생 들에 게 엔터티 집합에서 데이터를 가져온 (`_context.Students`)의 목록에 결과 투영 하는 LINQ를 사용 하 고 `EnrollmentDateGroup` 모델 개체를 보고 합니다. LINQ를 사용 하지 않고 자체 SQL을 작성 하려고 한다고 가정 합니다. SQL 쿼리를 실행 해야 할 작업을 수행 하는 반환 엔터티 개체 이외의 합니다. EF 코어 1.0에서 작업을 수행 하는 한 가지 방법은 ADO.NET 코드를 작성 하 고 EF에서 데이터베이스 연결을 가져옵니다.

*HomeController.cs*, 교체는 `About` 메서드를 다음 코드로 바꿉니다.

[!code-csharp[주](intro/samples/cu/Controllers/HomeController.cs?name=snippet_UseRawSQL&highlight=3-32)]

추가 하 여 문:

[!code-csharp[주](intro/samples/cu/Controllers/HomeController.cs?name=snippet_Usings2)]

정보 페이지를 실행 합니다. 이전과 동일한 데이터가 표시 됩니다.

![페이지 정보](advanced/_static/about.png)

## <a name="call-an-update-query"></a>업데이트 쿼리를 호출 합니다.

Contoso University 관리자가 모든 과정에 대 한 크레딧의 수를 변경 하는 등 데이터베이스의 전역 변경 작업을 수행 하려는 경우를 가정 합니다. 대학 과정 수가 많은 경우 것 하지 엔터티로 모두 검색 하 고 개별적으로 변경 하는 데 효율적입니다. 이 섹션에서는 모든 과정에 대 한 크레딧의 수를 변경 하는 인수를 지정 하는 데 사용할 수 있는 웹 페이지를 구현 하 고 변경 SQL UPDATE 문을 실행 하 여 원활 하 게 합니다. 웹 페이지는 다음 그림과 같이 표시 됩니다.

![업데이트 과정 학점이 페이지](advanced/_static/update-credits.png)

*CoursesContoller.cs*, HttpGet 및 HttpPost UpdateCourseCredits 메서드를 추가 합니다.

[!code-csharp[주](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_UpdateGet)]

[!code-csharp[주](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_UpdatePost)]

컨트롤러는 HttpGet 요청을 처리할 때 아무 것도 반환에 `ViewData["RowsAffected"]`, 앞의 그림에서와 같이 보기는 빈 텍스트 상자 및 전송 단추를 표시 합니다.

경우는 **업데이트** 단추를 클릭 하면 HttpPost 메서드를 호출 하 고 점수 텍스트 상자에 입력 된 값이 있습니다. 다음 코드를 실행 과정을 업데이트 하 고 보기에 영향을 받는 행 수를 반환 하는 SQL `ViewData`합니다. 뷰를 가져옵니다는 `RowsAffected` 값을 업데이트 된 행의 수를 표시 합니다.

**솔루션 탐색기**를 마우스 오른쪽 단추로 클릭는 *보기/과정* 폴더를 마우스 클릭 한 다음 **추가 > 새 항목**합니다.

에 **새 항목 추가** 대화 상자에서 클릭 **ASP.NET** 아래 **설치 됨** 왼쪽된 창에서 **MVC 뷰 페이지**, 하 고 새 뷰 이름을 *UpdateCourseCredits.cshtml*합니다.

*Views/Courses/UpdateCourseCredits.cshtml*, 템플릿 코드를 다음 코드로 바꿉니다.

[!code-html[주](intro/samples/cu/Views/Courses/UpdateCourseCredits.cshtml)]

실행은 `UpdateCourseCredits` 메서드를 선택 하 여는 **과정** 탭에서 다음을 추가 "/ UpdateCourseCredits" 브라우저의 주소 표시줄에서 URL의 끝에 (예: `http://localhost:5813/Course/UpdateCourseCredits)`합니다. 텍스트 상자에 숫자를 입력 합니다.

![업데이트 과정 학점이 페이지](advanced/_static/update-credits.png)

**업데이트**를 클릭합니다. 영향을 받는 행 수를 확인 합니다.

![업데이트 과정 학점이 영향을 받는 행을 페이지](advanced/_static/update-credits-rows-affected.png)

클릭 **목록으로 돌아가기** 크레딧의 수정 된 번호로 과정의 목록을 볼 수 있습니다.

원시 SQL 쿼리 하는 방법에 대 한 자세한 내용은 참조 [원시 SQL 쿼리](https://docs.microsoft.com/en-us/ef/core/querying/raw-sql)합니다.

## <a name="examine-sql-sent-to-the-database"></a>SQL 데이터베이스에 전송 검사

데이터베이스에 전송 되는 실제 SQL 쿼리를 볼 수 하는 데 도움이 됩니다. ASP.NET Core에 대 한 기본 제공 된 로깅 기능 쿼리 및 업데이트에 대 한 SQL을 포함 하는 로그를 작성 하 EF 코어에서 자동으로 사용 됩니다. 이 섹션에서는 SQL 로깅에 대 한 예가 표시 됩니다.

열기 *StudentsController.cs* 및는 `Details` 에 중단점을 설정 하는 메서드는 `if (student == null)` 문입니다.

디버그 모드에서 응용 프로그램을 실행 하 고 학생에 대 한 세부 정보 페이지로 이동 합니다.

이동의 **출력** 디버그를 보여 주는 창 출력을 보고, 쿼리를 참조 합니다.

```
Microsoft.EntityFrameworkCore.Storage.IRelationalCommandBuilderFactory:Information: Executed DbCommand (225ms) [Parameters=[@__id_0='?'], CommandType='Text', CommandTimeout='30']
SELECT [e].[EnrollmentID], [e].[CourseID], [e].[Grade], [e].[StudentID], [c].[CourseID], [c].[Credits], [c].[DepartmentID], [c].[Title]
FROM [Enrollment] AS [e]
INNER JOIN (
    SELECT DISTINCT TOP(2) [s].[ID]
    FROM [Person] AS [s]
    WHERE ([s].[Discriminator] = N'Student') AND ([s].[ID] = @__id_0)
    ORDER BY [s].[ID]
) AS [s0] ON [e].[StudentID] = [s0].[ID]
INNER JOIN [Course] AS [c] ON [e].[CourseID] = [c].[CourseID]
ORDER BY [s0].[ID]
```

여기 있는 것 놀라운 보면: 최대 2 개 행을 선택 하는 SQL (`TOP(2)`). `SingleOrDefaultAsync` 메서드는 서버에서 한 행으로 확인 되지 않았습니다. 경우 Where 절에서 일치 하는 여러 행 메서드 반환 해야 null 때문에 EF만 최대 2 행을 선택 하기 때문에 3 개 이상의 일치 Where 절, 결과 `SingleOrDefault` 2 개의 행과 일치 하는 경우 메서드는 동일 합니다.

디버그 모드를 사용 하 여 로깅 출력을 얻으려면 중단점에서 중지 없는 참고는 **출력** 창입니다. 로깅 출력을 확인 하려는 시점을 중지 하는 편리한 방법인 이며 로깅을 계속 경우 그럴 필요가, 하 고에 관심이 있다면 파트 찾기로 다시 스크롤할 필요가 있습니다.

## <a name="repository-and-unit-of-work-patterns"></a>리포지토리 및 작업 단위 패턴

많은 개발자는 Entity Framework와 함께 사용할 수 있는 코드 주위에서 래퍼로 리포지토리 및 작업 단위 패턴을 구현 하는 코드를 작성 합니다. 이러한 패턴은 데이터 액세스 계층 및 응용 프로그램의 비즈니스 논리 계층 사이의 추상화 계층을 만드는 데 사용 됩니다. 이러한 패턴을 구현 하는 데이터 저장소의 변경 내용 으로부터 응용 프로그램을 분리 하는 데 도움이 및 자동화 된 단위 테스트 또는 테스트 기반 개발 TDD ()를 쉽게 할 수 있습니다. 그러나 이러한 패턴을 구현 하는 추가 코드를 작성은 여러 가지 이유로 EF를 사용 하는 응용 프로그램에 적합 합니다.

* 자체는 EF 컨텍스트 클래스에는 데이터 저장소 특정 코드에서 코드를 분리합니다.

* 데이터베이스에 대 한 작업 단위가 클래스 업데이트 EF를 사용 하 여 수행 하는 EF 컨텍스트 클래스 작동할 수 있습니다.

* EF를 사용 하면 저장소 코드를 작성 하지 않고도 TDD를 구현 하기 위한 기능이 있습니다.

리포지토리 및 작업 단위 패턴을 구현 하는 방법에 대 한 정보를 참조 하십시오. [이 자습서 시리즈의 Entity Framework 5 버전](https://docs.microsoft.com/aspnet/mvc/overview/older-versions/getting-started-with-ef-5-using-mvc-4/implementing-the-repository-and-unit-of-work-patterns-in-an-asp-net-mvc-application)합니다.

Entity Framework Core 테스트에 사용할 수 있는 메모리 내 데이터베이스 공급자를 구현 합니다. 자세한 내용은 참조 [InMemory를 사용 하 여 테스트](https://docs.microsoft.com/ef/core/miscellaneous/testing/in-memory)합니다.

## <a name="automatic-change-detection"></a>자동 변경 내용 검색

Entity Framework 원래 값으로는 엔터티의 현재 값을 비교 하 여 엔터티 어떻게 변경 되었는지 (및 업데이트를 데이터베이스에 전송 해야 하는 따라서)를 결정 합니다. 엔터티 쿼리 하거나 연결 하는 경우 원래 값 저장 됩니다. 자동 변경 내용 검색 발생 하는 메서드 중 일부는 다음과 같습니다.

* DbContext.SaveChanges

* DbContext.Entry

* ChangeTracker.Entries

많은 수의 엔터티를 추적 하는 경우 루프에서 여러 번 이러한 방법 중 하나 호출 하면 성능이 크게 향상 된 기능을 사용 하 여 변경 내용을 자동 검색을 일시적으로 해제 하 여 발생할 수 있습니다는 `ChangeTracker.AutoDetectChangesEnabled` 속성입니다. 예:

```csharp
_context.ChangeTracker.AutoDetectChangesEnabled = false;
```

## <a name="entity-framework-core-source-code-and-development-plans"></a>Entity Framework Core 소스 코드 및 개발 계획

Entity Framework Core에 대 한 소스 코드에서 사용할 수는 [https://github.com/aspnet/EntityFramework](https://github.com/aspnet/EntityFramework)합니다. 소스 코드 외에도 수 있으며 야간 빌드 가져오기, 문제 추적, 기능 사양, 디자인 회의 노트 [향후 개발을 위한 로드맵](https://github.com/aspnet/EntityFramework/wiki/Roadmap), 등입니다. 버그를 보고할 수 및 EF 소스 코드에 고유한 향상 된 기능에 기여할 수 있습니다.

소스 코드를 연 하지만 Entity Framework 코어는 Microsoft 제품으로 완전히 지원 됩니다. Microsoft Entity Framework 팀 기 고물 허용 되는 제어를 유지 하 고 각 릴리스의 품질 보장을 위해 모든 코드 변경 내용을 테스트 합니다.

## <a name="reverse-engineer-from-existing-database"></a>기존 데이터베이스에서 리버스 엔지니어링

리버스 엔지니어링할 기존 데이터베이스에서 엔터티 클래스를 포함 하 여 데이터 모델을 사용 하 여는 [스 캐 폴드 dbcontext](https://docs.microsoft.com/ef/core/miscellaneous/cli/powershell#scaffold-dbcontext) 명령입니다. 참조는 [시작 자습서](https://docs.microsoft.com/ef/core/get-started/aspnetcore/existing-db)합니다.

## <a name="summary"></a>요약

이 시리즈를 Entity Framework 코어를 사용 하 여 ASP.NET MVC 응용 프로그램에 대 한 자습서를 마쳤습니다. Entity Framework 코어를 사용 하 여 데이터를 사용 하는 방법에 대 한 자세한 내용은 참조는 [EF 설명서](https://docs.microsoft.com/ef/core)합니다.

이 만든 후 웹 응용 프로그램을 배포 하는 방법에 대 한 정보를 참조 하십시오. [게시 및 배포](../../publishing/index.md)합니다.
인증 및 권한 부여 등 ASP.NET 핵심 MVC와 관련 된 다른 항목에 대 한 정보에 대 한 참조는 [ASP.NET 핵심 설명서](https://docs.microsoft.com/en-us/aspnet/core/)합니다.

## <a name="acknowledgments"></a>감사의 글

Tom Dykstra 및 Rick Anderson (twitter @RickAndMSFT)이이 자습서를 작성 합니다. Rowan Miller, Diego Vega 및 Entity Framework 팀의 다른 멤버 코드 검토를 지 원하는 및이 자습서에 대 한 코드 작성 하는 동안 발생 하는 문제를 디버깅 하는 데 도움이 됩니다.

## <a name="common-errors"></a>일반적인 오류  

### <a name="contosouniversitydll-used-by-another-process"></a>다른 프로세스에서 사용 하는 ContosoUniversity.dll

오류 메시지:

> 열 수 없습니다. '... bin\Debug\netcoreapp1.0\ContosoUniversity.dll' 쓰기용-' 프로세스는 파일에 액세스할 수 없습니다 '... \bin\Debug\netcoreapp1.0\ContosoUniversity.dll' 다른 프로세스에서 사용 되 고 있으므로 합니다.

해결책:

IIS Express에서 사이트를 중지 합니다. Windows 시스템 트레이를 IIS Express를 찾고 및 해당 아이콘을 마우스 오른쪽 단추로 클릭 하 고, Contoso University 사이트를 선택 하 고 클릭 한 다음 이동 **사이트 중지**합니다.

### <a name="migration-scaffolded-with-no-code-in-up-and-down-methods"></a>메서드 위쪽 및 아래쪽에 코드 없이 스 캐 폴드 마이그레이션

가능한 원인:

자동으로 EF CLI 명령을 닫고 코드 파일을 저장 하지 않습니다. 실행할 때 변경 하지 않은 경우는 `migrations add` 명령, EF 변경 내용을 찾이 없습니다.

해결책:

실행은 `migrations remove` 명령, 코드 변경 내용을 저장 및 다시는 `migrations add` 명령입니다.

### <a name="errors-while-running-database-update"></a>실행 중인 데이터베이스를 업데이트 하는 동안 오류

기존 데이터가 있는 데이터베이스의 스키마를 변경 하는 경우 다른 오류를 가져올 수는 것이 불가능 합니다. 마이그레이션 오류를 해결할 수 없는 경우, 연결 문자열에 데이터베이스 이름을 변경 하거나 데이터베이스를 삭제 합니다. 새 데이터베이스로 마이그레이션할 데이터가 없는 있으며 데이터베이스 업데이트 명령을 오류 없이 완료 가능성이 훨씬 더 높습니다.

가장 간단한 방법은 데이터베이스의 이름을 바꾸려면 *appsettings.json*합니다. 다음에 실행할 때 `database update`, 새 데이터베이스가 만들어집니다.

SSOX에서 데이터베이스를 삭제 하려면 데이터베이스를 마우스 오른쪽 단추로 클릭 합니다 **삭제**, 한 다음는 **데이터베이스 삭제** 대화 상자 선택 **기존 연결을 닫을** 클릭 **확인**합니다.

CLI를 사용 하 여 데이터베이스를 삭제 하려면 실행의 `database drop` CLI 명령:

```console
dotnet ef database drop
```

### <a name="error-locating-sql-server-instance"></a>SQL Server 인스턴스를 배치 하는 오류

오류 메시지:

> SQL Server에 대 한 연결을 설정 하는 동안 네트워크 관련 또는 인스턴스 관련 오류가 발생 했습니다. 서버를 찾을 수 없거나 액세스할 수 없습니다. 인스턴스 이름이 올바르고 SQL Server 원격 연결을 허용 하도록 구성 되어 있는지 확인 합니다. (공급자: SQL 네트워크 인터페이스, 오류: 26-오류를 찾는 서버/인스턴스 지정)

해결책:

연결 문자열을 확인 합니다. 데이터베이스 파일을 수동으로 삭제 한 경우 새 데이터베이스를 다시 시작 하도록 생성 문자열에 데이터베이스의 이름을 변경 합니다.

>[!div class="step-by-step"]
[이전](inheritance.md)

