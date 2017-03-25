---
title: "ASP.NET Core MVC EF 코어 10-동시성-8 | Microsoft 문서"
author: tdykstra
description: "이 자습서에서는 여러 사용자가 동시에 같은 엔터티를 업데이트할 때 충돌을 처리 하는 방법을 보여 줍니다."
keywords: "ASP.NET Core, Entity Framework Core 동시성"
ms.author: tdykstra
manager: wpickett
ms.date: 03/15/2017
ms.topic: article
ms.assetid: 15e79e15-bda5-441d-80c7-8032a2628605
ms.technology: aspnet
ms.prod: asp.net-core
uid: data/ef-mvc/concurrency
translationtype: Machine Translation
ms.sourcegitcommit: a7d6980dab89b42cb63bb6840202719e802da4f3
ms.openlocfilehash: e9feacbe61293499b9755cb45159bd921ed41f58
ms.lasthandoff: 03/23/2017

---

# <a name="handling-concurrency-conflicts---ef-core-with-aspnet-core-mvc-tutorial-8-of-10"></a>동시성 충돌-ASP.NET 핵심 MVC 자습서 (8 ~ 10 개)를 사용 하는 EF 핵심 처리

여 [Tom Dykstra](https://github.com/tdykstra) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)

Contoso University 샘플 웹 응용 프로그램에는 Entity Framework 코어 1.1 및 Visual Studio 2017를 사용 하 여 ASP.NET 핵심 1.1 MVC 웹 응용 프로그램을 만드는 방법을 보여 줍니다. 자습서 시리즈에 대 한 정보를 참조 하십시오. [시리즈의 첫 번째 자습서](intro.md)합니다.

이전 자습서에서는 데이터를 업데이트 하는 방법을 알아보았습니다. 이 자습서에서는 여러 사용자가 동시에 같은 엔터티를 업데이트할 때 충돌을 처리 하는 방법을 보여 줍니다.

동시성 오류를 처리 및 부서 엔터티를 사용 하는 웹 페이지를 만들어야 합니다. 다음 그림은 동시성 충돌이 발생 하는 경우 표시 되는 일부 메시지를 포함 하 여 편집 및 삭제 페이지를 보여 줍니다.

![부서 편집 페이지](concurrency/_static/edit-error.png)

![부서 삭제 페이지](concurrency/_static/delete-error.png)

## <a name="concurrency-conflicts"></a>동시성 충돌

한 명의 사용자를 편집 하려면 엔터티 데이터를 표시 하 고 첫 번째 사용자의 변경 내용을 데이터베이스에 기록 되기 전에 다른 사용자는 동일한 엔터티의 데이터를 업데이트 하는 다음 동시성 충돌이 발생 합니다. 이러한 충돌 감지를 사용 하지 않는 경우 마지막 다른 사용자의 변경 내용을 덮어씁니다는 모든 사용자 데이터베이스를 업데이트 합니다. 많은 응용 프로그램에서이 위험을 감수할: 경우 또는 사용자가 거의 또는 몇 가지 업데이트가 없는 경우 일부 변경 내용이 덮어쓰면 동시성에 대 한 프로그래밍의 비용 이점을 얻는 정말 중요 하지 않은 합니다. 이 경우 동시성 충돌을 처리 하려면 응용 프로그램을 구성할 필요가 없습니다.

### <a name="pessimistic-concurrency-locking"></a>비관적 동시성 (잠금)

응용 프로그램에서 동시성 시나리오에서 실수로 인 한 데이터 손실을 방지할 필요가 경우 작업을 수행 하는 한 가지 방법은 데이터베이스 잠금을 사용 하는 합니다. 비관적 동시성을 호출 됩니다. 예를 들어 데이터베이스에서 행을 읽기 전에 요청에 대 한 잠금을 읽기 전용 또는 update 액세스 합니다. 다른 사용자가 중 하나에 대 한 행을 잠글 수 업데이트 액세스에 대 한 행을 잠그는 경우 읽기 전용으로 설정 하거나 변경 중인 데이터의 복사본을 쓰던 때문에 대 한 액세스를 업데이트 합니다. 읽기 전용 액세스에 대 한 행을 잠그는 경우 다른 사용자도 잠글 수 업데이트 하지 않는 읽기 전용 액세스 합니다.

잠금 관리에 단점이 있습니다. 프로그램에 복잡할 수 있습니다. 중요 한 데이터베이스 관리 리소스가 필요 하 고 응용 프로그램의 사용자 수와 성능 문제가 생길 수 증가 합니다. 이러한 이유로, 모든 데이터베이스 관리 시스템 비관적 동시성을 지원 합니다. Entity Framework Core,에 대 한 지원을 제공 하지 않습니다 하며이 자습서를 구현 하는 방법을 보여 주지 않습니다.

### <a name="optimistic-concurrency"></a>낙관적 동시성

비관적 동시성에 대 한 대안에는 낙관적 동시성입니다. 낙관적 동시성 했을 동시성 충돌을 허용 하 고 그럴 경우 적절 하 게 반응 의미 합니다. 예를 들어 Jane 부서 편집 페이지를 방문 하 고 $350,000.00에서 $0.00 영어 부서에 대 한 예산 금액을 변경 합니다.

![예산 0으로 변경](concurrency/_static/change-budget.png)

Jane은 클릭 하기 전에 **저장**, John 같은 페이지를 방문 하 고 2007 년 9 월 1에서에서 2013 년 8 월 8 일에 시작 날짜 필드를 변경 합니다.

![2013 시작 날짜를 변경합니다.](concurrency/_static/change-date.png)

Jane은 클릭 **저장** 첫 번째 인덱스 페이지에는 브라우저는 반환 될 때 변경 그녀에 게 표시 하 고 있습니다.

![0으로 변경 하는 예산](concurrency/_static/budget-zero.png)

John 클릭 한 다음 **저장** $350,000.00의 예산에 여전히 표시 하는 편집 페이지에 있습니다. 다음은 동시성 충돌을 처리 하는 방법으로 결정 됩니다.

일부의 옵션은 다음과 같습니다.

* 한 사용자가 수정 되는 속성을 추적 하 고 데이터베이스의 해당 열만 업데이트할 수 있습니다.

     예제 시나리오에서 데이터가 손실 될, 두 사용자가 서로 다른 속성 업데이트 되었기 때문에 합니다. 다음에 영어 부서 이동 Jane의와 John의 변경 내용을-2013 년 8 월 8 일의 시작 날짜와 0 달러의 예산 볼 수 있습니다. 업데이트는이 메서드의 데이터 손실을 야기 하는 충돌의 수를 줄일 수 있지만 경쟁 변경 될 경우 엔터티의 동일한 속성에 데이터 손실을 방지할 수 없습니다. Entity Framework이 방식이으로 작동 하는지 여부를 업데이트 코드를 구현 하는 방법에 따라 달라 집니다. 엔터티에 대 한 모든 원래 속성 값 및 새 값의 추적을 유지 하기 위해 많은 양의 상태를 유지 해야 하므로 웹 응용 프로그램에서는 경우가 유용 합니다. 많은 양의 상태를 유지 관리 성능이 떨어질 수 있습니다 응용 프로그램 서버 리소스를 필요로 하거나 (예를 들어 숨겨진된 필드)에 웹 페이지 자체에 포함 해야 하기 때문에 또는 쿠키에 있습니다.

* Jane의 내용으로 덮어쓰게 이한일의 변경 하도록 할 수 있습니다.

     다음에 영어 부서 이동, 2013 년 8 월 8 일 아티스트와 복원 된 $350,000.00 값입니다. 이 이라고는 *클라이언트 우선* 또는 *Wins의 마지막* 시나리오입니다. (클라이언트의 모든 값 보다 우선 데이터 저장소의 기능입니다.) 동시성 처리에 대 한 코딩을 이렇게 하지 않으면이 섹션의 소개 부분에서 언급 했 듯이이 자동으로 수행 됩니다.

* John의 변경 되 고 데이터베이스에서 업데이트를 방지할 수 있습니다.

     일반적으로 오류 메시지를 표시, 그 데이터의 현재 상태를 표시 하는 여전히 있도록 원한다 면 자신의 변경 내용을 다시 적용할 수 있도록 허용 합니다. 이 이라고는 *저장소 Wins* 시나리오입니다. (데이터 저장소 값 보다 우선 클라이언트에서 전송한 값입니다.) 이 자습서에서는 저장소 Wins 시나리오를 구현 합니다. 이 메서드는 변경 하지 않고 상황 하 라는 메시지가 표시 되 고 사용자 없이 덮어씀을 않도록 합니다.

### <a name="detecting-concurrency-conflicts"></a>동시성 충돌 감지

처리 하 여 충돌을 해결할 수 `DbConcurrencyException` Entity Framework throw 하는 예외입니다. 이러한 예외를 throw 하는 시기를 확인 하기 위해 Entity Framework에서 충돌을 검색할 수 있어야 합니다. 따라서 구성 해야 데이터베이스와 데이터 모델 적절 하 게 합니다. 충돌 감지를 사용 하기 위한 몇 가지 옵션은 다음과 같습니다.

* 데이터베이스 테이블에 행이 변경 된 시기를 결정 하는 데 사용할 수 있는 추적 열을 포함 합니다. Entity Framework where에서 해당 열을 포함 하도록 구성할 수 있습니다 SQL Update 또는 Delete 명령을 절.

     추적 열의 데이터 형식이 일반적으로 `rowversion`합니다. `rowversion` 값은 행이 업데이트 될 때마다가 증가 하는 일련 번호입니다. Update 또는 Delete 명령에서 Where 절 (원래 행 버전이) 추적 열의 원래 값이 포함 됩니다. 업데이트 되는 행의 값을 다른 사용자에 의해 변경 된 경우는 `rowversion` 열은 원래 값 보다 다르므로 Update 또는 Delete 문의 Where 때문에 업데이트할 행을 찾을 수 없습니다 절. 행이 없는 업데이트 또는 삭제 하 여 업데이트 Entity Framework 찾습니다 (즉 때 영향을 받는 행 수가&0;) 명령을 동시성 충돌로 해석 합니다.

* Where에서 테이블의 모든 열의 원래 값을 포함 하도록 Entity Framework 구성 업데이트 및 삭제 명령 절.

     행 처음 읽은 후에 변경 행에서 첫 번째 옵션에서와 같이 Where 절 반환 되지 않습니다 행을 업데이트 하려면 Entity Framework를 동시성 충돌로 해석 합니다. 이 방법은 많은 열이 있는 데이터베이스 테이블에 대 한 매우 큰 Where 발생할 수 있습니다 절을 많은 양의 상태를 유지 관리 하는 필요할 수 있습니다. 앞에서 설명한 대로 많은 양의 상태를 유지 관리 응용 프로그램 성능 영향을 줄 수 있습니다. 따라서이 방법은 일반적으로 권장 되지 않으며,이 자습서에서 사용한 방법은 아닙니다.

     동시성을 추가 하 여 추적 하려는 엔터티의 모든 아닌 기본 키 속성을 표시 해야 하는이 동시성을 구현 하지 않으려는 경우는 `ConcurrencyCheck` 해당 하는 특성입니다. 변경 하는 Entity Framework는 Update 및 Delete 문 SQL Where 절의 모든 열을 포함 하도록 합니다.

이 자습서의 나머지 부분에서 추가 하는 `rowversion` 부서 엔터티를 추적 속성, 컨트롤러와 뷰를 만들고 테스트 모두 올바르게 작동 하는지 확인 합니다.

## <a name="add-a-tracking-property-to-the-department-entity"></a>부서 엔터티를 추적 속성을 추가 합니다.

*Models/Department.cs*, RowVersion 라는 추적 속성을 추가 합니다.

[!code-csharp[주](intro/samples/cu/Models/Department.cs?name=snippet_Final&highlight=26,27)]

`Timestamp` 특성이 열이 포함 되도록 지정 where에서 절을 업데이트 및 삭제 명령 데이터베이스에 전송 합니다. 특성 이라고 `Timestamp` 이전 버전의 SQL Server는 SQL을 사용 하기 때문에 `timestamp` SQL 전에 데이터 형식 `rowversion` 대체 했습니다. .NET 유형을 `rowversion` 바이트 배열입니다.

Fluent API를 사용 하는 것을 선호 하는 경우 사용할 수 있습니다는 `IsConcurrencyToken` 메서드 (에서 *Data/SchoolContext.cs*) 다음 예제와 같이 추적 속성을 지정 하려면:

```csharp
modelBuilder.Entity<Department>()
    .Property(p => p.RowVersion).IsConcurrencyToken();
```

속성을 추가 하 여 다른 마이그레이션을 수행 해야 하므로 데이터베이스 모델을 변경 했습니다.

변경 내용을 저장 하 고 프로젝트를 빌드하려면 명령 창에서 다음 명령을 입력 합니다.

```console
dotnet ef migrations add RowVersion
dotnet ef database update
```

## <a name="create-a-departments-controller-and-views"></a>부서 컨트롤러와 뷰 만들기

학생, 교육 코스 및 강사 이전과 마찬가지로 부서 컨트롤러와 뷰 스 캐 폴딩 합니다.

![스 캐 폴드 부서](concurrency/_static/add-departments-controller.png)

에 *DepartmentsController.cs* 파일, 부서 관리자 드롭 다운 목록을 강사의 전체 이름 대신 마지막 이름만 포함 될 수 있도록 "fullname" "FirstMidName"의 모든&4; 개의 항목을 변경 합니다.

[!code-csharp[주](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_Dropdown)]

## <a name="update-the-departments-index-view"></a>부서 인덱스 뷰를 업데이트 합니다.

스 캐 폴딩 엔진 인덱스 보기에서 RowVersion 열 만들어지지만 해당 필드를 표시 하지 않아야 합니다.

코드 *Views/Departments/Index.cshtml* 다음 코드를 사용 합니다.

[!code-html[주](intro/samples/cu/Views/Departments/Index.cshtml?highlight=4,7,44)]

"부서" 삭제 RowVersion 열 제목을 변경 하 고 관리자에 대 한 첫 번째 이름 대신 전체 이름을 표시 합니다.

## <a name="update-the-edit-methods-in-the-departments-controller"></a>부서 컨트롤러의 편집 메서드를 업데이트 합니다.

에 두 HttpGet `Edit` 메서드 및 `Details` 메서드를 추가 `AsNoTracking`합니다.

[!code-csharp[주](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_EagerLoading&highlight=3)]

Httppost 기존 코드를 바꿉니다 `Edit` 메서드를 다음 코드로 바꿉니다.

[!code-csharp[주](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_EditPost)]

코드는 업데이트할 부서를 읽으려고 하 여 시작 합니다. 하는 경우는 `SingleOrDefaultAsync` 메서드가 null을 반환, 부서에서 다른 사용자가 삭제 되었습니다. 이 경우 코드는 오류 메시지와 함께 편집 페이지를 다시 표시 될 수 있도록 부서 엔터티를 만들 수 게시 된 양식 값을 사용 합니다. 대 안으로, department 필드를 다시 표시 하지 않고 오류 메시지를 표시 하는 경우 부서 엔터티를 다시 만드는 없다는 것입니다.

뷰 저장 원래 `RowVersion` 숨겨진된 필드에이 메서드는 값에 해당 값을 받고는 `rowVersion` 매개 변수입니다. 호출 하기 전에 `SaveChanges`, 원래에 넣이 필요가 `RowVersion` 속성 값에는 `OriginalValues` 엔터티의 컬렉션입니다.

```csharp
_context.Entry(departmentToUpdate).Property("RowVersion").OriginalValue = rowVersion;
```

Entity Framework에는 SQL UPDATE 명령을 만들고, 해당 명령을 포함 합니다 원래가 있는 행을 검색 하는 WHERE 절 다음 `RowVersion` 값입니다. UPDATE 명령을 영향을 받는 행이 없는 경우 (행이 없는 한 원래 `RowVersion` 값), Entity Framework를 throw 한 `DbUpdateConcurrencyException` 예외입니다.

해당 예외에 대 한 catch 블록의 코드에서 업데이트 된 값을 포함 하는 영향을 받는 부서 엔터티를 가져옵니다는 `Entries` 예외 개체의 속성입니다.

[!code-csharp[주](intro/samples/cu/Controllers/DepartmentsController.cs?range=164)]

`Entries` 컬렉션 하나만 갖습니다 `EntityEntry` 개체입니다.  사용자가 입력 한 새 값 및 현재 데이터베이스 값을 가져오는 해당 개체를 사용할 수 있습니다.

[!code-csharp[주](intro/samples/cu/Controllers/DepartmentsController.cs?range=165-166)]

코드 페이지의 편집에 입력 한 사용자에서 다른 데이터베이스 값이 포함 된 각 열에 대 한 사용자 지정 오류 메시지를 추가 하 여 (필드가 하나만 여기에 표시 된 됨).

[!code-csharp[주](intro/samples/cu/Controllers/DepartmentsController.cs?range=174-178)]

코드에서는 설정 하는 마지막으로,는 `RowVersion` 값은 `departmentToUpdate` 데이터베이스에서 새 값을 검색 합니다. 이 새로운 `RowVersion` 페이지는 다시 표시 하 고 편집 하 고 다음 사용자가 클릭 시간 값 숨겨진된 필드에 저장 됩니다 **저장**, 편집 페이지를 다시 표시는 발생 때문에 발생 하는 동시성 오류만 합니다.

[!code-csharp[주](intro/samples/cu/Controllers/DepartmentsController.cs?range=199-200)]

`ModelState.Remove` 문을 하지 않으므로 필요 `ModelState` 이전에 `RowVersion` 값입니다. 뷰에서 `ModelState` 필드 우선 모델 속성 값은 둘 다 있는 경우에 대 한 값입니다.

## <a name="update-the-department-edit-view"></a>부서 편집 뷰 업데이트

*Views/Departments/Edit.cshtml*를 다음과 같이 변경 합니다.

* 제거는 `<div>` 에 대 한 스 캐 폴드 된 요소는 `RowVersion` 필드입니다.

* 저장 하는 숨겨진된 필드를 추가 하는 `RowVersion` 바로 다음에 숨겨진된 필드에 대 한 속성 값을는 `DepartmentID` 속성입니다.

* 드롭다운 목록에는 "관리자 권한 선택" 옵션을 추가 합니다.

[!code-html[주](intro/samples/cu/Views/Departments/Edit.cshtml?highlight=15,41-43)]

## <a name="test-concurrency-conflicts-in-the-edit-page"></a>편집 페이지에서 동시성 충돌 테스트

사이트를 실행 하 고 부서 인덱스 페이지로 이동 하는 부서를 클릭 합니다.

마우스 오른쪽 단추로 클릭는 **편집** 영어 부서 및 선택에 대 한 하이퍼링크 **새 탭에서 열기**를 클릭 한 다음는 **편집** 영어 부서에 대 한 하이퍼링크입니다. 두 개의 브라우저 탭에는 이제 동일한 정보가 표시 됩니다.

첫 번째 브라우저 탭에 필드를 변경 하 고 클릭 **저장**합니다.

![변경 후 1 페이지 부서 편집](concurrency/_static/edit-after-change-1.png)

브라우저는 변경 된 값이 있는 인덱스 페이지를 표시 합니다.

두 번째 브라우저 탭에 필드를 변경 합니다.

![변경 후 2 페이지 부서 편집](concurrency/_static/edit-after-change-2.png)

**저장**을 클릭합니다. 오류 메시지가 표시 됩니다.

![부서 편집 페이지 오류 메시지](concurrency/_static/edit-error.png)

클릭 **저장** 다시 합니다. 두 번째 브라우저 탭에 입력 한 값이 저장 됩니다. 인덱스 페이지가 표시 되 면 저장된 된 값을 참조 합니다.

## <a name="update-the-delete-page"></a>삭제 페이지를 업데이트 합니다.

삭제 페이지에 대 한 Entity Framework는 다른 부서에서 비슷한 방식으로 편집 사용자에 의해 발생 한 동시성 충돌을 감지 합니다. 때는 HttpGet `Delete` 확인 보기를 표시 하는 메서드, 보기에는 원래 `RowVersion` 숨겨진된 필드의 값입니다. HttpPost를 사용할 수 있는 값 이면 `Delete` 메서드를 사용자가 삭제 확인 될 때 호출 됩니다. Entity Framework에는 SQL DELETE 명령을 만듭니다 때 원래 사용 하는 WHERE 절 포함 `RowVersion` 값입니다. 명령 결과가&0; 인 행에 영향 (행이 삭제 확인 페이지가 표시 된 후 변경 의미), 동시성 예외가 throw 되는 HttpGet 및 `Delete` 메서드는 오류 플래그가 오류 메시지와 확인 페이지가 다시 표시 하려면 true로 설정 합니다. 행이&0; 개인 경우에서 오류 메시지가 없으면 보고서가 표시 되도록 행 다른 사용자가 삭제 되었기 때문에 영향을 받은 가능한 이기도 합니다.

### <a name="update-the-delete-methods-in-the-departments-controller"></a>부서 컨트롤러의 Delete 메서드를 업데이트 합니다.

*DepartmentController.cs*는 HttpGet 바꿉니다 `Delete` 메서드를 다음 코드로 바꿉니다.

[!code-csharp[주](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_DeleteGet&highlight=1,14-17,21-29)]

메서드는 동시성 오류가 발생 한 후 페이지는 다시 표시 되 고 있는지 여부를 나타내는 선택적 매개 변수를 수락 합니다. 이 플래그는 true를 더 이상 지정 하는 부서 존재 하는 경우 다른 사용자가 삭제 되었습니다. 이 경우 코드 인덱스 페이지로 리디렉션합니다.  이 플래그는 true이 고 학부가 다른 사용자가 변경 되었습니다. 이 경우 코드 보냅니다 사용 하 여 보기에 오류 메시지를 보내 `ViewData`합니다.  

HttpPost의 코드를 대체 `Delete` 메서드 (라는 `DeleteConfirmed`) 다음 코드를 사용 합니다.

[!code-csharp[주](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_DeletePost&highlight=3,7,14,15,16,17,18)]

대체 했습니까 스 캐 폴드 된 코드에서이 메서드는 레코드 ID만 수락 됨:


```csharp
public async Task<IActionResult> DeleteConfirmed(int id)
```

모델 바인더에서 만든 부서 엔터티 인스턴스에이 매개 변수를 변경 했습니다. 레코드 키 외에도 RowVersion 속성 값에 EF 액세스할을 수 있습니다.

```csharp
public async Task<IActionResult> Delete(Department department)
```

작업 메서드 이름을 변경한 `DeleteConfirmed` 를 `Delete`합니다. 이름을 사용 하는 스 캐 폴드 된 코드 `DeleteConfirmed` 는 고유의 시그니처가 HttpPost 메서드를 제공 합니다. (다른 메서드 매개 변수를 가지도록 오버 로드 된 메서드는 CLR에 필요 합니다.) 서명 되는 고유 했으므로 MVC 관례를 계속 하 고 HttpPost 및 HttpGet delete 메서드에 대 한 동일한 이름을 사용 하 여 수 있습니다.

부서에서 이미 삭제 된 경우는 `AnyAsync` 메서드에서 false가 반환 하 고 응용 프로그램에서 Index 메서드에 다시 바로 이동 합니다.

동시성 오류 포착 되는 경우 코드는 삭제 확인 페이지를 다시 표시 하 고 것을 나타내는 플래그 동시성 오류 메시지가 표시를 제공 합니다.

### <a name="update-the-delete-view"></a>삭제 뷰를 업데이트 합니다.

*Views/Department/Delete.cshtml*, 스 캐 폴드 된 코드는 오류 메시지 필드와 DepartmentID 및 RowVersion 속성에 대 한 숨겨진된 필드를 추가 하는 다음 코드로 바꿉니다. 변경 내용은 강조 표시 됩니다.

[!code-html[주](intro/samples/cu/Views/Departments/Delete.cshtml?highlight=9,38,43-44)]

이렇게 하면 다음과 같이 변경 합니다.

* 사이 오류 메시지가 추가 `h2` 및 `h3` 머리글입니다.

* 에 FullName LastName 바꿉니다는 **관리자** 필드입니다.

* RowVersion 필드를 제거합니다.

* 숨겨진된 필드에 대 한 추가 `DepartmentID` 및 `RowVersion` 속성입니다.

부서 인덱스 페이지를 실행 합니다. 마우스 오른쪽 단추로 클릭는 **삭제** 영어 부서 및 선택에 대 한 하이퍼링크 **새 탭에서 열기**, 첫 번째 탭에서 클릭는 **편집** 영어 부서에 대 한 하이퍼링크입니다.

첫 번째 창에서 값 중 하나를 변경 하 고 클릭 **저장**:

![삭제 하기 전에 변경 후 부서 편집 페이지](concurrency/_static/edit-after-change-for-delete.png)

두 번째 탭에서 클릭 **삭제**합니다. 동시성 오류 메시지가 나타나고 부서 값은 현재 데이터베이스와 새로 고쳐집니다.

![동시성 오류와 함께 부서 삭제 확인 페이지](concurrency/_static/delete-error.png)

클릭 하면 **삭제** 부서에서 삭제 했을 보여 주는 인덱스 페이지로 리디렉션되 며 하 다시 합니다.

## <a name="update-details-and-create-views"></a>세부 정보를 업데이트 하 고 뷰 만들기

필요에 따라 세부 정보에서 스 캐 폴드 된 코드를 정리 하 고 뷰를 만들 수 있습니다.

코드를 대체 *Views/Departments/Details.cshtml* RowVersion 열을 삭제 하는 관리자의 전체 이름을 표시 합니다.

[!code-html[주](intro/samples/cu/Views/Departments/Details.cshtml?highlight=35)]

코드를 대체 *Views/Departments/Create.cshtml* 옵션 선택 드롭다운 목록에 추가 합니다.

[!code-html[주](intro/samples/cu/Views/Departments/Create.cshtml?highlight=38-40)]

## <a name="summary"></a>요약

동시성 충돌을 처리에 대 한 소개를 마쳤습니다. EF 코어의 동시성을 처리 하는 방법에 대 한 자세한 내용은 참조 [동시성 충돌](https://docs.microsoft.com/en-us/ef/core/saving/concurrency)합니다. 다음 자습서에는 Instructor 및 Student 엔터티를 사용 하 여 계층당 하나의 테이블 상속을 구현 하는 방법을 보여 줍니다.

>[!div class="step-by-step"]
[이전](update-related-data.md)
[다음](inheritance.md)  

