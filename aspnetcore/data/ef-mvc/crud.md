---
title: "ASP.NET Core MVC EF 코어 CRUD-2 / 10 | Microsoft 문서"
author: tdykstra
description: 
keywords: "ASP.NET Core, Entity Framework 코어, CRUD, 만들기, 읽기, 업데이트, 삭제"
ms.author: tdykstra
manager: wpickett
ms.date: 03/15/2017
ms.topic: article
ms.assetid: 6e1cd570-40f1-4b24-8b6e-7d2d27758f18
ms.technology: aspnet
ms.prod: asp.net-core
uid: data/ef-mvc/crud
translationtype: Machine Translation
ms.sourcegitcommit: c597a3af1af25408bea76729bc9d968f998aeece
ms.openlocfilehash: a6a9382065dd9e5bf6e5211d469a897688481ca8
ms.lasthandoff: 03/23/2017

---
# <a name="create-read-update-and-delete---ef-core-with-aspnet-core-mvc-tutorial-2-of-10"></a>만들기, 읽기, 업데이트 및 삭제-EF 코어 ASP.NET 핵심 MVC 자습서 (2 / 10)

여 [Tom Dykstra](https://github.com/tdykstra) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)

Contoso University 샘플 웹 응용 프로그램에는 Entity Framework 코어 1.1 및 Visual Studio 2017를 사용 하 여 ASP.NET 핵심 1.1 MVC 웹 응용 프로그램을 만드는 방법을 보여 줍니다. 자습서 시리즈에 대 한 정보를 참조 하십시오. [시리즈의 첫 번째 자습서](intro.md)합니다.

이전 자습서에서 저장 하 고 Entity Framework 및 SQL Server LocalDB를 사용 하 여 데이터를 표시 하는 MVC 응용 프로그램을 만들었습니다. 이 자습서에서는 검토 하 고는 CRUD를 사용자 지정 합니다 (만들기, 읽기, 업데이트, 삭제) MVC 스 캐 폴딩 자동으로 만들어 드립니다 컨트롤러 및 뷰에서 코드입니다.

> [!NOTE] 
> 컨트롤러 및 데이터 액세스 계층 사이의 추상화 계층을 만들기 위해 리포지토리 패턴을 구현 하는 일반적인이 좋습니다. 이 자습서를 간단 하 고 자체 Entity Framework를 사용 하는 방법을 가르치는 중점을 유지 하려면 저장소를 사용 하지 않아. EF의 저장소에 대 한 정보를 참조 하십시오. [이 시리즈의 마지막 자습서](advanced.md)합니다.

이 자습서에서는 다음 웹 페이지를 사용 합니다.

![학생 정보 페이지](crud/_static/student-details.png)

![학생 만들기 페이지](crud/_static/student-create.png)

![학생 편집 페이지](crud/_static/student-edit.png)

![학생 삭제 페이지](crud/_static/student-delete.png)

## <a name="customize-the-details-page"></a>세부 정보 페이지를 사용자 지정

학생 들에 게 인덱스 페이지에 대 한 스 캐 폴드 된 코드는 제외는 `Enrollments` 속성을 해당 속성 컬렉션을 보유 하기 때문에 있습니다. 에 **세부 정보** 페이지 HTML 표에 컬렉션의 내용을 표시 합니다.

*Controllers/StudentsController.cs*, 세부 정보에 대 한 작업 메서드를 사용 하 여 볼는 `SingleOrDefaultAsync` 메서드는 단일을 `Student` 엔터티. 호출 하는 코드를 추가 `Include`합니다. `ThenInclude`및 `AsNoTracking` 메서드를 다음 강조 표시 된 코드와 같이 합니다.

[!code-csharp[주](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Details&highlight=8-12)]

`Include` 및 `ThenInclude` 메서드 컨텍스트를 로드 하면는 `Student.Enrollments` 탐색 속성 및 각 등록 내는 `Enrollment.Course` 탐색 속성입니다.  이러한 방법에 대해 자세히 배웁니다는 [관련된 데이터를 읽는](read-related-data.md) 자습서입니다.

`AsNoTracking` 메서드는 반환 된 엔터티를 현재 컨텍스트의 수명에서 업데이트 되지 것입니다 없는 시나리오에서 성능을 향상 합니다. 에 대 한 더 알아봅니다 `AsNoTracking` 이 자습서의 끝에 있습니다.

### <a name="route-data"></a>경로 데이터

에 전달 되는 키 값은 `Details` 에서 제공 되는 메서드 *데이터를 라우팅할*합니다. 경로 데이터는 데이터 모델 바인더는 URL의 세그먼트에서 찾을 수입니다. 예를 들어, 기본 경로 컨트롤러, 작업 및 id 세그먼트를 지정합니다.

[!code-csharp[주](intro/samples/cu/Startup.cs?name=snippet_RouteAndSeed&highlight=5)]

다음 URL에 기본 경로 인덱스의 동작으로 그리고 id; 1 컨트롤러로 강사를 매핑합니다. 이들은 경로 데이터 값입니다.

```
http://localhost:1230/Instructor/Index/1?courseID=2021
```

URL의 마지막 부분 ("? courseID&2021; =") 쿼리 문자열 값입니다. 모델 바인더는 ID 값을 전달할 수도 `Details` 메서드 `id` 매개 변수는 쿼리 문자열 값으로 전달 하는 경우:

```
http://localhost:1230/Instructor/Index?id=1&CourseID=2021
```

인덱스 페이지에 하이퍼링크 Url은 Razor 뷰에서 태그 도우미 문에 의해 생성 됩니다. 다음과 같은 Razor 코드에서의 `id` 매개 변수가 기본 경로 따라서 일치 `id` 경로 데이터에 추가 됩니다.

```html
<a asp-action="Edit" asp-route-id="@item.ID">Edit</a>
```

다음 HTML이 생성 때 `item.ID` 6:

```html
<a href="/Students/Edit/6">Edit</a>
```

다음과 같은 Razor 코드에서 `studentID` 쿼리 문자열로 추가 기본 경로에 대 한 매개 변수를 일치 하지 않습니다.

```html
<a asp-action="Edit" asp-route-studentID="@item.ID">Edit</a>
```

다음 HTML이 생성 때 `item.ID` 6:

```html
<a href="/Students/Edit?studentID=6">Edit</a>
```

태그 도우미에 대 한 자세한 내용은 참조 [태그 도우미 ASP.NET 핵심에](xref:mvc/views/tag-helpers/intro)합니다.

### <a name="add-enrollments-to-the-details-view"></a>자세히 보기를 등록을 추가 합니다.

열기 *Views/Students/Details.cshtml*합니다. 각 필드를 사용 하 여 표시 됩니다 `DisplayNameFor` 및 `DisplayFor` 도우미에는 다음 예와 같이 합니다.

[!code-html[](intro/samples/cu/Views/Students/Details.cshtml?range=13-18&highlight=2,5)]

마지막 필드 뒤와 닫기 직전 `</dl>` 태그를 사용한 등록의 목록을 표시 하려면 다음 코드를 추가 합니다.

[!code-html[](intro/samples/cu/Views/Students/Details.cshtml?range=31-52)]

코드 들여쓰기 인 경우 잘못 된 코드를 붙여 넣은 후 CTRL-K-D 키를 눌러 수정 합니다.

이 코드의 엔터티를 반복 하는 `Enrollments` 탐색 속성입니다. 각 등록 과정 제목와 등급 표시합니다. 과정 제목에 저장 된 Course 엔터티에서 검색 되는 `Course` 등록 엔터티의 탐색 속성입니다.

응용 프로그램을 실행, 선택은 **학생 들에 게** 탭을 클릭는 **세부 정보** 학생에 대 한 링크입니다. 선택한 학생에 대 한 코스 목록과 등급 표시.

![학생 정보 페이지](crud/_static/student-details.png)

## <a name="update-the-create-page"></a>만들기 페이지를 업데이트 합니다.

*StudentsController.cs*, HttpPost 수정 `Create` try catch 블록을 추가 하 고 ID를 제거 하 여 메서드는 `Bind` 특성입니다.

[!code-csharp[주](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Create&highlight=4,6-7,14-21)]

이 코드는 학생 들에 게 엔터티를 ASP.NET MVC 모델 바인더가에서 만든 학생 엔터티의 설정 하 고 데이터베이스의 변경 내용을 저장 한 다음 추가 합니다. (모델 바인더는 ASP.NET MVC 기능을 참조 양식을 통해 제출 된 데이터에 맞게 쉽게, 모델 바인더는 CLR 형식에 게시 된 양식 값을 변환 하 고 작업 메서드에 매개 변수에서 전달 됩니다. 이 경우 모델 바인더를 폼 컬렉션의 속성 값을 사용 하 여 학생 엔터티를 인스턴스화합니다.)

제거 하면 `ID` 에서 `Bind` 특성 ID가 SQL Server는 자동으로 설정 된 행이 삽입 될 때 기본 키 값입니다. 사용자의 입력 ID 값을 설정 하지 않습니다.

이외의 다른는 `Bind` 특성 try catch 블록은 스 캐 폴드 된 코드에 대 한 유일한 변경 합니다. 예외에서 파생 되는 경우 `DbUpdateException` 는 변경 내용을 저장 하는 동안 발견 되었습니다, 일반 오류 메시지가 표시 됩니다. `DbUpdateException`예외는 다시 시도 한다고 사용자 하므로 외부 프로그래밍 오류를 보다는 응용 프로그램에 의해 발생 합니다. 이 샘플에서 구현 되지 않지만 프로덕션 품질 응용 프로그램 예외를 기록 합니다. 자세한 내용은 참조는 **파악을 위한 로그** 섹션 [모니터링 및 원격 분석 (실제 클라우드 앱 빌드 Azure)](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry)합니다.

`ValidateAntiForgeryToken` 특성 (CSRF) 교차 사이트 요청 위조 공격을 방지 합니다. 보기를 삽입 하 고 자동으로 되어 토큰은 [FormTagHelper](https://github.com/aspnet/Mvc/blob/dev/src/Microsoft.AspNetCore.Mvc.TagHelpers/FormTagHelper.cs) 폼은 사용자가 제출 될 때 포함 됩니다. 토큰에서 유효성을 검사는 `ValidateAntiForgeryToken` 특성입니다. CSRF에 대 한 자세한 내용은 참조 [ 🔧 백신 요청 위조](../../security/anti-request-forgery.md)합니다.

<a id="overpost"></a>
### <a name="security-note-about-overposting"></a>Overposting에 대 한 보안 정보

`Bind` 스 캐 폴드 된 코드에 포함 된 특성의 `Create` 메서드는 한 가지 방법은 overposting 으로부터 보호 하기 위해에서 시나리오 만들기. 예를 들어, 학생 엔터티를 포함 한 `Secret` 속성을 설정 하려면이 웹 페이지를 표시 하지 않으려면입니다.

```csharp
public class Student
{
    public int ID { get; set; }
    public string LastName { get; set; }
    public string FirstMidName { get; set; }
    public DateTime EnrollmentDate { get; set; }
    public string Secret { get; set; }
}
```

없는 경우에는 `Secret` 웹 페이지, 해커의 필드 Fiddler와 같은 도구를 사용 하거나 게시 하려면 JavaScript를 작성 한 `Secret` 값을 형성 합니다. 없이 `Bind` 모델 바인더가 모델 바인더 학생 인스턴스를 만들 때 사용 되는 필드를 제한 하는 특성 모두를 선택 합니다 `Secret` 값을 만들어 사용 하 여 학생 엔터티 인스턴스를 만들 수 있습니다. 어떤 해커에 대해 지정 된 값이 다음는 `Secret` 양식 필드 데이터베이스에서 업데이트 됩니다. 다음 이미지는 Fiddler 도구 추가 표시는 `Secret` 게시 된 양식 값을 필드 (값 "OverPost")을 사용 합니다.

![암호 필드를 추가 하는 fiddler](crud/_static/fiddler.png)

값 "OverPost" 성공적으로 추가 됩니다에 `Secret` 속성 삽입 하면 의도 하지 않은 웹 페이지를 해당 속성을 설정할 수 있지만 행입니다.

사용 하 여 보안 모범 사례는는 `Include` 화이트 리스트 필드에 바인딩 특성을 가진 매개 변수입니다. 사용 하 여도 가능는 `Exclude` 매개 변수를 제외 하려면 블랙 리스트 필드로 합니다. 이유 `Include` 보안이 강화 되는 엔터티에 새 속성을 추가 하면 새 필드가 자동으로 보호 되지 않는 `Exclude` 목록입니다.

먼저 데이터베이스에서 엔터티를 읽은 다음 호출 하 여 편집 시나리오에서 overposting를 방지할 수 있습니다 `TryUpdateModel`명시적 허용 되는 속성 목록에 전달 합니다. 이 자습서에 사용 되는 방법입니다.

대부분의 개발자가 선호 하는 overposting 방지 하는 또 다른 방법은 모델 바인딩과 함께 엔터티 클래스가 아닌 모델 보기를 사용 하는 것입니다. 뷰 모델에서 업데이트 하려는 속성만 포함 됩니다. MVC 모델 바인더가 완료 되 면 필요에 따라 AutoMapper와 같은 도구를 사용 하 여 엔터티 인스턴스에 뷰 모델 속성을 복사 합니다. 사용 하 여 `_context.Entry` 해당 상태를 설정 하려면 엔터티 인스턴스에서 `Unchanged`로 설정한 `Property("PropertyName").IsModified` 뷰 모델에 포함 된 각 엔터티 속성에 대해 true로 합니다. 이 메서드는 모두에서 편집한 시나리오를 만들 합니다.

### <a name="test-the-create-page"></a>만들기 페이지를 테스트 합니다.

코드 *Views/Students/Create.cshtml* 사용 하 여 `label`, `input`, 및 `span` (유효성 검사 메시지의 경우)에 대 한 각 필드에 태그를 지정 합니다.

페이지를 선택 하 여 실행의 **학생** 탭을 클릭 하 고 **새로 만들기**합니다.

이름 및 잘못 된 날짜를 입력 하 고 클릭 **만들기** 오류 메시지를 확인 합니다.

![날짜 유효성 검사 오류](crud/_static/date-error.png)

이 기본적으로 얻을 수 있는 서버 쪽 유효성 검사 자습서의 뒷부분에 클라이언트 쪽 유효성 검사에 대 한 코드를 생성 하는 특성을 추가 하는 방법을 표시 됩니다. 다음과 같은 강조 표시 된 코드에서 모델 유효성 검사를 보여 줍니다.는 `Create` 메서드.

[!code-csharp[주](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Create&highlight=8)]

유효한 값으로 날짜를 변경 하 고 클릭 **만들기** 에 표시 하는 새 학생을 볼 수는 **인덱스** 페이지입니다.

## <a name="update-the-edit-page"></a>업데이트 편집 페이지

*StudentController.cs*는 HttpGet `Edit` 메서드 (없이 하나는 `HttpPost` 특성) 사용 하는 `SingleOrDefaultAsync` 에서 본 것 처럼 선택한 학생 엔터티를 검색 하는 메서드는 `Details` 메서드. 이 메서드를 변경할 필요가 없습니다.

### <a name="recommended-httppost-edit-code-read-and-update"></a>HttpPost Edit 코드 권장: 읽기 및 업데이트

HttpPost Edit 작업 메서드를 다음 코드로 바꿉니다.

[!code-csharp[주](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ReadFirst)]

이러한 변경 내용은 overposting 방지 하기 위해 보안 모범 사례를 구현 합니다. 생성 하 여 스 캐 폴더는 `Bind` 특성을 사용 하 여 설정 엔터티를 모델 바인더에서 만든 엔터티를 추가 `Modified` 플래그입니다. 있으므로 코드 대부분의 시나리오에 권장 되지 않습니다는 `Bind` 특성에 나열 되지 않은 필드에서 기존의 모든 데이터를 지웁니다는 `Include` 매개 변수입니다.

기존 엔터티 및 호출에서 새 코드를 읽고 `TryUpdateModel` 하는 게시 된 양식 데이터에서 사용자 입력에 따라 검색 된 엔터티의 필드를 업데이트 합니다. Entity Framework의 자동 변경 집합 추적의 `Modified` 양식 입력에 의해 변경 된 필드에 대 한 플래그입니다. 경우는 `SaveChanges` 메서드가 호출 되 면 Entity Framework 데이터베이스 행을 업데이트 하는 SQL 문을 만듭니다. 동시성 충돌 무시 되 고 데이터베이스에서 사용자가 업데이트 된 테이블 열만 업데이트 됩니다. (자습서의 뒷부분 동시성 충돌을 처리 하는 방법을 보여 줍니다.)

과도 한 게시 으로부터, 하 여 업데이트할 수 있는 필드를 방지 하기 위해 모범 사례로 **편집** 페이지는 허용 목록에는 `TryUpdateModel` 매개 변수입니다. (매개 변수 목록에서 필드의 목록 앞에 빈 문자열은 양식 필드 이름으로 사용 하 여 접두사에 대 한.) 현재는 보호 하려는 추가 필드가 없는 되더라도 바인딩할 모델 바인더를 원하는 필드를 나열 하는 데이터 모델을 나중에 필드 추가 하는 경우 하 자동으로 보호 될 때 명시적으로 추가 여기까지 있습니다.

HttpPost의 메서드 서명에 이러한 변경으로 인해 `Edit` 메서드는 HttpGet 같습니다 `Edit` 메서드도 있습니다; 따라서 있습니다 이름을 바꾸 었으 메서드 `EditPost`합니다.

### <a name="alternative-httppost-edit-code-create-and-attach"></a>대체 HttpPost Edit 코드: 만들기 및 연결

권장된 HttpPost 코드를 편집 하면 변경 된 열만 업데이트 되는 모델 바인딩에 대 한 포함 하지 않을 속성에서 데이터를 보존 합니다. 그러나 읽기 우선 접근 방식에는 추가 데이터베이스는 읽기 및 동시성 충돌을 처리 하는 것에 대 한 좀 더 복잡 한 코드에서 발생할 수 있습니다 필요 합니다. 대신 하는 EF 컨텍스트를 모델 바인더에서 만든 엔터티를 연결 하 고 수정 된 것으로 표시 합니다. (프로젝트를 업데이트 하지이 코드를 선택적 접근 방식을 설명 하기 위해 표시만 했습니다.) 

[!code-csharp[주](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_CreateAndAttach)]

웹 페이지 UI 필드의 모든 엔터티를 포함 하 고 그 중 하나를 업데이트할 수 있습니다이 방법을 사용할 수 있습니다.

스 캐 폴드 된 코드 만들기 / 연결 방법을 사용 하 여 하지만 catch `DbUpdateConcurrencyException` 404 오류 코드를 예외 및 반환 합니다.  데이터베이스 업데이트 예외를 catch 하 고 오류 메시지를 표시 하는 표시 된 예제입니다.

### <a name="entity-states"></a>엔터티 상태

메모리에서 엔터티는 데이터베이스의 해당 행과 동기화 하 고이 정보를 호출 하는 경우를 결정 하는 여부를 추적 데이터베이스 컨텍스트는 `SaveChanges` 메서드. 예를 들어 전달 하는 경우 새 엔터티를는 `Add` 엔터티의 상태를로 설정 하는 메서드 `Added`합니다. 호출 하면는 `SaveChanges` 메서드에서 데이터베이스 컨텍스트 SQL INSERT 명령을 발행 합니다.

엔터티의 다음 상태 중 하나일 수 있습니다.

* `Added`. 엔터티가는 데이터베이스에 아직 존재 하지 않습니다. `SaveChanges` 메서드는 INSERT 문을 실행 합니다.

* `Unchanged`. 이 엔터티와 작업을 수행 해야 하는 아무 것도 `SaveChanges` 메서드. 데이터베이스에서 엔터티를 읽을 때 엔터티가이 상태와 함께 시작 됩니다.

* `Modified`. 엔터티의 속성 값의 일부 또는 전부 수정 되었습니다. `SaveChanges` 메서드는 UPDATE 문을 실행 합니다.

* `Deleted`. 엔터티 삭제 표시 되었습니다. `SaveChanges` 메서드는 DELETE 문을 실행 합니다.

* `Detached`. 엔터티는 데이터베이스 컨텍스트에 의해 추적 되 고 되지 않습니다.

데스크톱 응용 프로그램 상태 변경 내용이 일반적으로 자동으로 설정 됩니다. 엔터티를 읽고 해당 속성 값의 일부 변경 해야 합니다. 이 인해 해당 엔터티 상태를 자동으로 변경 해야 `Modified`합니다. 호출 하면 `SaveChanges`, Entity Framework에는 실제 속성을 변경 하는 업데이트 하는 SQL UPDATE 문을 생성 합니다.

웹 응용 프로그램에서 `DbContext` 을 처음에 읽는 엔터티 및 해당 데이터를 편집할 수는 페이지가 렌더링 된 후 삭제 되는 표시 됩니다. 때 HttpPost `Edit` 작업 메서드를 호출 하 고 새 웹 요청이 이루어집니다의 새 인스턴스는 `DbContext`합니다. 새 이러한 맥락에서 엔터티를 다시 읽을 경우 데스크톱 처리를 시뮬레이션 합니다.

하지만 추가 읽기 작업을 수행 하지 않으려는 경우 모델 바인더에서 만든 엔터티 개체를 사용 하 여 해야 합니다.  이 작업을 수행 하는 가장 간단한 방법은 앞에 표시 된 대체 HttpPost Edit 코드 에서처럼 Modified에 엔터티 상태를 설정 하는 것입니다. 호출 하면 `SaveChanges`, Entity Framework 컨텍스트에 변경 하는 속성을 알 수 없기 때문에 데이터베이스 행의 모든 열을 업데이트 합니다.

읽기 중심 접근 방식을 문제를 방지 하려면 표시 되지만 사용자가 실제로 변경 된 필드를 업데이트 하는 SQL 업데이트 문을 원하는 코드가 복잡해 집니다. 어떤 방식으로든에서 원래 값을 저장 해야 (예: 숨겨진된 필드를 사용 하 여)를 사용할 수 있는 경우 HttpPost `Edit` 메서드를 호출 합니다. 원래 값이 호출을 사용 하 여 한 학생 엔터티를 만들 수는 `Attach` 메서드는 엔터티, 원래 버전과 함께 새 값을 엔터티의 값으로 업데이트 한 다음 호출 `SaveChanges`합니다.

### <a name="test-the-edit-page"></a>테스트 편집 페이지

응용 프로그램을 실행 하 고 선택 된 **학생** 탭을 클릭 한 다음는 **편집** 하이퍼링크입니다.

![학생 편집 페이지](crud/_static/student-edit.png)

선택한 데이터의 일부 변경 **저장**합니다. **인덱스** 페이지가 열리고 변경 된 데이터를 표시 합니다.

## <a name="update-the-delete-page"></a>삭제 페이지를 업데이트 합니다.

*StudentController.cs*, 템플릿 코드는 HttpGet `Delete` 메서드가 `SingleOrDefaultAsync` 에서 보았듯이 세부 정보 및 편집 방법을 선택한 학생 엔터티를 검색 하는 메서드입니다. 그러나 구현 하는 사용자 지정 오류 메시지에 대 한 호출 `SaveChanges` 실패 하면이 메서드와 해당 뷰를 몇 가지 기능을 추가 합니다.

업데이트에 대 한 확인 하 고 작업을 만들 때 두 개의 작업 메서드 삭제 작업에 필요 합니다. GET 요청에 대 한 응답에서 호출 되는 메서드는 승인 또는 삭제 작업을 취소 하는 사용자에 게 제공 하는 보기를 표시 합니다. 사용자가 승인할 경우이 POST 요청이 생성 됩니다. 이런 경우, HttpPost `Delete` 메서드가 호출 되 고 실제로 해당 메서드에 삭제 동작을 수행 합니다.

HttpPost를 try-catch 블록에 추가 합니다 `Delete` 데이터베이스를 업데이트할 때 발생할 수 있는 오류를 처리 하는 방법입니다. 오류가 발생 하면 HttpPost Delete 메서드는 오류가 발생 했음을 나타냅니다 하는 매개 변수를 전달 하는 HttpGet Delete 메서드를 호출 합니다. HttpGet Delete 메서드는 다음 기회를 취소 하거나 다시 시도 사용자에 게 부여 하는 오류 메시지와 함께 확인 페이지가 다시 표시 됩니다.

대체는 HttpGet `Delete` 오류 보고를 관리 하는 다음 코드로 작업 메서드.

[!code-csharp[주](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_DeleteGet&highlight=1,9,16-21)]

이 코드는 변경 내용을 저장 하려면 실패 한 후 메서드가 호출 된 있는지 여부를 나타내는 선택적 매개 변수를 수락 합니다. 이 매개 변수는 false는 HttpGet `Delete` 이전에 실패 한 없이 메서드를 호출 합니다. HttpPost에서 호출 될 때 `Delete` 데이터베이스 업데이트 오류에 대 한 응답에서 메서드를 매개 변수가 true이 고 보기에 오류 메시지가 전달 됩니다.

### <a name="the-read-first-approach-to-httppost-delete"></a>HttpPost Delete는 읽기 우선 접근 방식

HttpPost 바꿉니다 `Delete` 작업 메서드 (라는 `DeleteConfirmed`)를 다음 코드로 실제 삭제 작업을 수행 하 고이 데이터베이스 업데이트 오류를 catch 합니다.

[!code-csharp[주](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_DeleteWithReadFirst&highlight=6,8-11,13-14,18-23)]

이 코드는 선택한 엔터티를 검색 한 다음 호출는 `Remove` 엔터티의 상태를 설정 하는 메서드 `Deleted`합니다. 때 `SaveChanges` 이라고 SQL DELETE 명령이 생성 됩니다.

### <a name="the-create-and-attach-approach-to-httppost-delete"></a>HttpPost Delete는 만들기 / 연결 방법

대규모 응용 프로그램에서 성능 향상이 매우 중요 하는 경우 주를 사용 하 여 학생 엔터티를 인스턴스화하여 불필요 한 SQL 쿼리를 방지할 수 있습니다 키 값 및 엔터티 상태를 다음 설정 `Deleted`합니다. Entity Framework는 엔터티를 삭제 하는 데 필요한 모든입니다. (이 코드를 프로젝트에 추가 하지 마십시오; 대신 설명 하기 위해 여기는 합니다.)

[!code-csharp[주](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_DeleteWithoutReadFirst&highlight=7-8)]

엔터티도 삭제 해야 하는 데이터 관련 있는 경우 해당 하위 삭제는 데이터베이스에 구성 되어 있는지 확인 합니다. 엔터티 삭제를이 방식에서는 EF 모를 수 있습니다 관련된 엔터티를 삭제할 수 있습니다.

### <a name="update-the-delete-view"></a>삭제 뷰를 업데이트 합니다.

*Views/Student/Delete.cshtml*, 다음 예제와 같이 오류 메시지가 h2 머리글 사이의 h3 머리글을 추가 합니다.

[!code-html[](intro/samples/cu/Views/Students/Delete.cshtml?range=7-9&highlight=2)]

페이지를 선택 하 여 실행 된 **학생 들에 게** 탭을 클릭 하는 **삭제** 하이퍼링크:

![삭제 확인 페이지](crud/_static/student-delete.png)

클릭 **삭제**합니다. 인덱스 페이지가 삭제 된 학생 없이 표시 됩니다. (오류 코드의 동시성 자습서에 작동 처리의 예가 표시 됩니다.)

## <a name="closing-database-connections"></a>데이터베이스 연결 닫기

데이터베이스 연결을 보유 하는 리소스를 확보 하려면 상황에 맞는 인스턴스 삭제 되어야 합니다 최대한 빨리 완료 되는 경우. ASP.NET Core 기본 제공 [종속성 주입](../../fundamentals/dependency-injection.md) 해당 작업을 담당 합니다.

*Startup.cs* 호출의 [AddDbContext 확장 메서드](https://github.com/aspnet/EntityFramework/blob/03bcb5122e3f577a84498545fcf130ba79a3d987/src/Microsoft.EntityFrameworkCore/EntityFrameworkServiceCollectionExtensions.cs) 프로 비전 하는 `DbContext` ASP.NET DI 컨테이너의 클래스입니다. 메서드를 서비스 수명을 설정 `Scoped` 기본적으로 합니다. `Scoped`컨텍스트 개체 수명 웹 요청 수명이 일치 하는 방법 및 `Dispose` 메서드는 웹 요청의 끝에 자동으로 호출 됩니다.

## <a name="handling-transactions"></a>트랜잭션 처리

기본적으로 Entity Framework는 암시적으로 트랜잭션을 구현합니다. 여러 행 또는 테이블에 변경 하 고 다음 시나리오에서 `SaveChanges`, Entity Framework 하는지 자동으로 변경 내용을 모두 성공 하거나 모두 실패 합니다. 일부 변경 내용이 먼저 완료 되 고 오류가 발생 한 다음 해당 변경 내용은 자동으로 롤백됩니다. 여기서 필요한 세부적으로 제어할 수-예를 들어 트랜잭션에-Entity Framework 외부에서 수행 하는 작업을 포함 하도록 하려는 경우 시나리오 참조 [트랜잭션을](https://docs.microsoft.com/ef/core/saving/transactions)합니다.

## <a name="no-tracking-queries"></a>쿼리 추적 안 함

데이터베이스 컨텍스트 테이블 행을 검색 하 고이 표현 하는 엔터티 개체를 만드는 경우 기본적으로 그 여부를 추적 메모리의 엔터티를 데이터베이스에 포함 된 내용으로 동기화 됩니다. 데이터를 메모리에에서 한 캐시 역할을 하 고 엔터티를 업데이트 하는 경우에 사용 됩니다. 이 캐시는 필요 하지 않습니다 종종 웹 응용 프로그램에서 컨텍스트는 일반적으로 수명이 (새로운 만들어지고 삭제 한 각 요청에 대 한) 인스턴스와 컨텍스트를 읽는 엔터티는 해당 엔터티를 다시 사용 하기 전에 일반적으로 삭제 됩니다.

호출 하 여 메모리에 있는 엔터티 개체의 추적을 비활성화할 수는 `AsNoTracking` 메서드. 작업을 수행 해야 하는 일반적인 시나리오는 다음과 같습니다.

* 상황에 맞는 수명 기간 동안 모든 엔터티를 업데이트할 필요가 없는 있으며 EF 필요는 없습니다 [별도 쿼리에 의해 검색 된 엔터티를 탐색 속성을 자동으로 로드](read-related-data.md)합니다. 컨트롤러의 HttpGet 작업 메서드에에서 이러한 조건이 충족 되는 경우가 많습니다.

* 많은 양의 데이터를 검색 하는 쿼리를 실행 하는 하 고 반환된 된 데이터의 작은 부분만 업데이트 됩니다. 효율적으로 큰 쿼리에 대해 추적을 해제 하 고 나중에 업데이트 해야 하는 몇 가지 엔터티에 대 한 쿼리를 실행할 수 있습니다.

* 엔터티를 업데이트 하려면 연결 하려고 하지만 서로 다른 목적을 위해 동일한 엔터티를 검색 이전 합니다. 데이터베이스 컨텍스트 엔터티가 이미 추적 되 고, 변경 하려는 엔터티를 연결할 수 없습니다. 호출 하는 것이 상황을 처리 하는 한 가지 방법은 `AsNoTracking` 이전 쿼리 합니다.

자세한 내용은 참조 [vs를 추적 합니다. 추적 안 함이](https://docs.microsoft.com/en-us/ef/core/querying/tracking)합니다.

## <a name="summary"></a>요약

이제 학생 엔터티에 대 한 간단한 CRUD 작업을 수행 하는 페이지에 대 한 전체 집합이 있습니다. 다음 자습서에서의 기능을 확장할 수는 **인덱스** 정렬, 필터링 및 페이징을 추가 하 여 페이지입니다.

>[!div class="step-by-step"]
[이전](intro.md)
[다음](sort-filter-page.md)  

