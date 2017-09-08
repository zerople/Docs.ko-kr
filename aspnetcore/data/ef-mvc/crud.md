---
title: "ASP.NET Core MVC EF 코어 CRUD-2 / 10 인"
author: tdykstra
description: 
keywords: "ASP.NET Core, Entity Framework Core, CRUD, 만들기, 읽기, 업데이트, 삭제"
ms.author: tdykstra
manager: wpickett
ms.date: 03/15/2017
ms.topic: get-started-article
ms.assetid: 6e1cd570-40f1-4b24-8b6e-7d2d27758f18
ms.technology: aspnet
ms.prod: asp.net-core
uid: data/ef-mvc/crud
ms.openlocfilehash: 855f060a6404dedff310b288ada9738689069ceb
ms.sourcegitcommit: 5355c96a1768e5a1d5698a98c190e7addcc4ded5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/05/2017
---
# <a name="create-read-update-and-delete---ef-core-with-aspnet-core-mvc-tutorial-2-of-10"></a>만들기, 읽기, 업데이트 및 삭제-EF 코어 ASP.NET Core MVC 자습서 (2 / 10)

여 [Tom Dykstra](https://github.com/tdykstra) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)

Contoso 대학 샘플 웹 응용 프로그램에는 Entity Framework Core 및 Visual Studio를 사용 하 여 ASP.NET Core MVC 웹 응용 프로그램을 만드는 방법을 보여 줍니다. 자습서 시리즈에 대 한 정보를 참조 하십시오. [시리즈의 첫 번째 자습서](intro.md)합니다.

이전 자습서에서 저장 하 고 Entity Framework 및 SQL Server LocalDB를 사용 하 여 데이터를 표시 하는 MVC 응용 프로그램을 만들었습니다. 이 자습서를 검토 하는 CRUD 사용자 지정 (만들기, 읽기, 업데이트, 삭제)는 MVC 기반 구조는 자동으로에 만드는 코드를 사용자에 대 한 컨트롤러와 뷰.

> [!NOTE] 
> 컨트롤러와 데이터 액세스 계층 간에 추상화 계층을 만들기 위해 리포지토리 패턴을 구현 하는 일반적인이 좋습니다. 이 자습서를 간단 하 고 교육 자체 Entity Framework를 사용 하는 방법에 초점을 맞추고 유지 하려면 저장소를 사용 하지 않아 합니다. EF의 저장소에 대 한 정보를 참조 하십시오. [이 시리즈의 마지막 자습서](advanced.md)합니다.

이 자습서에서는 다음 웹 페이지를 사용 합니다.

![학생 세부 정보 페이지](crud/_static/student-details.png)

![학생 만들기 페이지](crud/_static/student-create.png)

![학생 편집 페이지](crud/_static/student-edit.png)

![학생 삭제 페이지](crud/_static/student-delete.png)

## <a name="customize-the-details-page"></a>사용자 지정 세부 정보 페이지

학생 인덱스 페이지에 대 한 스 캐 폴드 코드에서 제외 된 `Enrollments` 속성을 속성 컬렉션을 보유 하기 때문에 있습니다. 에 **세부 정보** 페이지에서 HTML 테이블에 컬렉션의 내용을 표시 합니다.

*Controllers/StudentsController.cs*, 세부 정보에 대 한 작업 메서드를 사용 하 여 볼는 `SingleOrDefaultAsync` 메서드는 단일 검색를 `Student` 엔터티. 호출 하는 코드를 추가 `Include`합니다. `ThenInclude`및 `AsNoTracking` 메서드를 다음 강조 표시 된 코드에 나와 있는 것 처럼 합니다.

[!code-csharp[Main](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Details&highlight=8-12)]

`Include` 및 `ThenInclude` 메서드 인해 로드 컨텍스트는 `Student.Enrollments` 탐색 속성 및 각 등록 내는 `Enrollment.Course` 탐색 속성입니다.  이러한 방법에 대해 자세히 알아봅니다는 [관련된 데이터를 읽는](read-related-data.md) 자습서입니다.

`AsNoTracking` 메서드는 반환 되는 엔터티를 현재 컨텍스트 수명에서 업데이트 되지 것입니다 없는 시나리오에서 성능을 향상 합니다. 에 대 한 자세히 알아보세요 `AsNoTracking` 이 자습서의 끝에 있습니다.

### <a name="route-data"></a>경로 데이터

에 전달 되는 키 값의 `Details` 메서드에서 가져온 *데이터 전달할*합니다. 경로 데이터는 모델 바인더는 URL의 세그먼트에 있는 데이터입니다. 예를 들어 기본 경로 세그먼트를 컨트롤러, 작업 및 id를 지정합니다.

[!code-csharp[Main](intro/samples/cu/Startup.cs?name=snippet_Route&highlight=5)]

기본 경로 작업으로 인덱스와 1; id로 컨트롤러로 강사를 매핑합니다 다음 url 이들은 경로 데이터 값입니다.

```
http://localhost:1230/Instructor/Index/1?courseID=2021
```

URL의 마지막 부분 ("? courseID 2021 =") 쿼리 문자열 값입니다. 모델 바인더는 ID 값을 전달할 수도 `Details` 메서드 `id` 매개 변수는 쿼리 문자열 값으로 전달 하는 경우:

```
http://localhost:1230/Instructor/Index?id=1&CourseID=2021
```

인덱스 페이지에 하이퍼링크 Url Razor 보기에서 태그 도우미 문에 의해 생성 됩니다. 다음 Razor 코드에서의 `id` 매개 변수 이므로 기본 경로 일치 `id` 경로 데이터에 추가 됩니다.

```html
<a asp-action="Edit" asp-route-id="@item.ID">Edit</a>
```

다음 HTML 생성 때 `item.ID` 은 6:

```html
<a href="/Students/Edit/6">Edit</a>
```

다음 Razor 코드에서 `studentID` 쿼리 문자열로 추가 기본 경로에 매개 변수가 일치 하지 않습니다.

```html
<a asp-action="Edit" asp-route-studentID="@item.ID">Edit</a>
```

다음 HTML 생성 때 `item.ID` 은 6:

```html
<a href="/Students/Edit?studentID=6">Edit</a>
```

태그 도우미에 대 한 자세한 내용은 참조 [태그 도우미에서 ASP.NET Core](xref:mvc/views/tag-helpers/intro)합니다.

### <a name="add-enrollments-to-the-details-view"></a>등록 세부 정보 보기에 추가

열기 *Views/Students/Details.cshtml*합니다. 사용 하 여 각 필드는 표시 `DisplayNameFor` 및 `DisplayFor` 다음 예제와 같이 도우미 클래스:

[!code-html[](intro/samples/cu/Views/Students/Details.cshtml?range=13-18&highlight=2,5)]

마지막 필드 뒤와 닫는 직전 `</dl>` 태그 등록의 목록을 표시 하려면 다음 코드를 추가 합니다.

[!code-html[](intro/samples/cu/Views/Students/Details.cshtml?range=31-52)]

코드 들여쓰기 위치는 코드를 붙여 넣은 후 잘못 된 경우 CTRL-K-D를 수정한 후에 키를 누릅니다.

이 코드의 엔터티는 반복의 `Enrollments` 탐색 속성입니다. 각 등록 과정 제목과 등급 표시합니다. 에 저장 된 과정 엔터티에서 과정 이름을 검색 하는 `Course` 등록 엔터티의 탐색 속성입니다.

응용 프로그램을 실행, 선택는 **학생** 탭을 클릭는 **세부 정보** 학생에 대 한 링크입니다. 선택한 학생 과정 및 등급의 목록이 표시:

![학생 세부 정보 페이지](crud/_static/student-details.png)

## <a name="update-the-create-page"></a>업데이트 만들기 페이지

*StudentsController.cs*, 수정 된 HttpPost `Create` try / catch 블록 추가 및 ID를 제거 하 여 메서드는 `Bind` 특성입니다.

[!code-csharp[Main](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Create&highlight=4,6-7,14-21)]

이 코드는 학생 엔터티를 ASP.NET MVC 모델 바인더를 통해 만든 학생 엔터티 설정 하 고 데이터베이스의 변경 내용을 저장 한 다음 추가 합니다. (모델 바인더는 ASP.NET MVC 기능을 참조 하면 쉽게 폼에서 전송한 데이터에 사용할 수 있습니다, 모델 바인더는 CLR 형식에 게시 된 양식 값을 변환 하 고 동작 메서드 매개 변수에서 전달 됩니다. 이 경우, 모델 바인더를 폼 컬렉션에서 속성 값을 사용 하 여 학생 엔터티를 인스턴스화합니다.)

제거 하면 `ID` 에서 `Bind` 특성 ID가 있는 행이 삽입 될 때 SQL 서버가 자동으로 설정 하는 기본 키 값입니다. 사용자의 입력 ID 값을 설정 하지 않습니다.

이외의 `Bind` 특성 try catch 블록은 스 캐 폴드 코드에 대 한 유일한 변경 합니다. 예외에서 파생 되는 경우 `DbUpdateException` 는 변경 내용을 저장 하는 동안 발견 되었습니다, 일반 오류 메시지가 표시 됩니다. `DbUpdateException`예외 경우에 따라 인해 외부 프로그래밍 오류가 아니라 응용 프로그램에 있는 하므로 사용자는 다시 시도 하는 데 권장 됩니다. 이 샘플에서 구현 되지 않지만 프로덕션 품질 응용 프로그램은 예외를 기록 합니다. 자세한 내용은 참조는 **통찰력에 대 한 로그** 섹션 [모니터링 및 원격 분석 (실제 클라우드로 응용 프로그램 빌딩 Azure)](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry)합니다.

`ValidateAntiForgeryToken` 특성 교차 사이트 요청 위조 (CSRF) 공격을 방지할 수 있습니다. 토큰이 자동으로 주입 하 여 보기에는 [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) 사용자가 폼이 전송 될 때 포함 됩니다. 토큰에 따라 유효성이 검사 된 `ValidateAntiForgeryToken` 특성입니다. CSRF에 대 한 자세한 내용은 참조 [바이러스 요청 위조](../../security/anti-request-forgery.md)합니다.

<a id="overpost"></a>
### <a name="security-note-about-overposting"></a>Overposting에 대 한 보안 정보

`Bind` 스 캐 폴드 코드에 포함 된 특성은 `Create` 메서드는 한 가지 방법은 overposting 으로부터 보호 하기 위해에서 시나리오 만들기. 예를 들어, 학생 엔터티를 포함 한 `Secret` 속성을 설정 하려면이 웹 페이지를 표시 하지 않으려면입니다.

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

없는 경우에 한 `Secret` 웹 페이지는 해커가 필드에서 Fiddler와 같은 도구를 사용 하거나 게시 하려면 일부 JavaScript 작성 한 `Secret` 값을 형성 합니다. 없이 `Bind` 하는 모델 바인더 모델 바인더 학생 인스턴스를 만들 때 사용 되는 필드를 제한 하는 특성은 선택 `Secret` 값 형태와 학생 엔터티 인스턴스를 만들려면 사용 합니다. 에 대해 지정 된 해커가 어떤 값이 다음는 `Secret` 양식 필드 데이터베이스에서 업데이트 됩니다. 다음 이미지에서는 Fiddler 도구 추가 보여 줍니다.는 `Secret` 게시 된 양식 값을 필드 (값 "OverPost")을 사용 합니다.

![암호 필드를 추가 하는 fiddler](crud/_static/fiddler.png)

값 "OverPost" 다음 성공적으로에 추가 되는 `Secret` 속성 삽입 행을 해당 속성을 설정 하는 웹 페이지 수 있도록 의도 하지 않은 있지만 합니다.

먼저 데이터베이스에서 엔터티를 읽은 한 다음 호출 하 여 편집 시나리오에서 overposting를 방지할 수 있습니다 `TryUpdateModel`명시적 허용 되는 속성 목록에 전달 합니다. 이 자습서에 사용 되는 방법입니다.

대부분의 개발자가 기본 overposting 방지 하는 다른 방법으로 모델 바인딩으로 엔터티 클래스를 사용 하지 않고 모델 보기를 사용 하는 것입니다. 뷰 모델에서 업데이트 하려는 속성만 포함 됩니다. MVC 모델 바인더 완료 되 면 필요에 따라 AutoMapper 같은 도구를 사용 하 여 엔터티 인스턴스에 보기 모델 속성을 복사 합니다. 사용 하 여 `_context.Entry` 해당 상태를 설정 하려면 엔터티 인스턴스에서 `Unchanged`를 설정한 `Property("PropertyName").IsModified` 의 보기 모델에 포함 된 각 엔터티 속성에서 true로 합니다. 이 메서드가 작동 모두에서 편집한 시나리오를 만들 합니다.

### <a name="test-the-create-page"></a>테스트 만들기 페이지

코드 *Views/Students/Create.cshtml* 사용 하 여 `label`, `input`, 및 `span` (유효성 검사 메시지의 경우)에 대 한 태그 각 필드에 대 한 도우미입니다.

페이지를 선택 하 여 실행 된 **학생** 탭을 클릭 하 고 **새로 만들기**합니다.

이름 및 날짜를 입력 합니다. 브라우저를 사용 하면 그렇게 하는 경우 잘못 된 날짜를 입력 하십시오. (일부 브라우저 강제로 날짜 선택을 사용할 수 있습니다.) 클릭 **만들기** 오류 메시지를 확인 합니다.

![날짜 유효성 검사 오류](crud/_static/date-error.png)

이 기본적으로 얻을 수 있는 서버 쪽 유효성 검사 이후의 자습서에서 또한 클라이언트 쪽 유효성 검사에 대 한 코드를 생성 하는 특성을 추가 하는 방법을 볼 수 있습니다. 다음 강조 표시 된 코드에서 모델 유효성 검사를 보여 줍니다.는 `Create` 메서드.

[!code-csharp[Main](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Create&highlight=8)]

유효한 값으로 날짜를 변경 하 고 클릭 **만들기** 새 학생에 표시를 볼 수는 **인덱스** 페이지.

## <a name="update-the-edit-page"></a>업데이트 편집 페이지

*StudentController.cs*는 HttpGet `Edit` 메서드 (없이 하나는 `HttpPost` 특성) 사용 하 여는 `SingleOrDefaultAsync` 에서 본 것 처럼 선택한 학생 엔터티를 검색 하는 메서드는 `Details` 메서드. 이 방법을 변경할 필요가 없습니다.

### <a name="recommended-httppost-edit-code-read-and-update"></a>코드 HttpPost 편집 권장: 읽기 및 업데이트

HttpPost 편집 동작 메서드를 다음 코드로 바꿉니다.

[!code-csharp[Main](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ReadFirst)]

이러한 변경 내용은 overposting 방지 하기 위해 보안 모범 사례를 구현 합니다. 생성 된 scaffolder는 `Bind` 특성을 사용 하 여 설정 엔터티를 모델 바인더에서 만든 엔터티를 추가 `Modified` 플래그입니다. 때문에 코드 대부분의 시나리오에 권장 되지 않는다고는 `Bind` 특성에 나열 되지 않은 필드에서 기존의 모든 데이터를 지웁니다는 `Include` 매개 변수입니다.

기존 엔터티 및 호출에서 새 코드를 읽고 `TryUpdateModel` 필드 검색 된 엔터티를 업데이트 하 [폼 게시 된 데이터에서 사용자 입력에 따라](xref:mvc/models/model-binding#how-model-binding-works)합니다. Entity Framework의 변경 내용 자동 추적 설정은 `Modified` 폼 입력에 의해 변경 된 필드에 대 한 플래그입니다. 경우는 `SaveChanges` 메서드가 호출 되 면 Entity Framework 데이터베이스 행을 업데이트 하는 SQL 문을 만듭니다. 동시성 충돌 무시 되 고 데이터베이스에서 사용자가 업데이트 된 테이블 열만 업데이트 됩니다. (이후의 자습서 동시성 충돌을 처리 하는 방법을 보여 줍니다.)

초과 게시 하 여 업데이트할 수 있는 필드를 방지 하기 위해 가장 좋은 방법은 **편집** 페이지는 허용 목록에는 `TryUpdateModel` 매개 변수입니다. (매개 변수 목록에서 필드 목록 앞에 빈 문자열은 양식 필드 이름으로 사용 하는 접두사입니다.) 현재는 보호 하는 추가 필드가 없습니다 되지만 바인딩할 모델 바인더를 원하는 필드를 나열 하 여 데이터 모델에 나중에 필드 추가 하는 경우 자동으로 보호 되는지 명시적으로 추가 해야만 여기 됩니다.

이와 같이 변경,는 HttpPost의 메서드 서명에 `Edit` 는 HttpGet와 같습니다 `Edit` 메서드; 메서드 이름이 변경 했으므로 따라서 `EditPost`합니다.

### <a name="alternative-httppost-edit-code-create-and-attach"></a>대체 HttpPost 편집 코드: 만들고 연결 하려면

권장된 HttpPost 코드를 편집 하면 변경 된 열만 업데이트 되는 모델 바인딩에 대 한 포함 된 의도 하지 않은 속성에 데이터 유지. 그러나 읽기 우선 접근 방식은 해야 추가 데이터베이스 읽고 동시성 충돌을 처리 하기 위한 더 복잡 한 코드에서 발생할 수 있습니다. 해도 EF 컨텍스트와 모델 바인더를 통해 만든 엔터티를 연결 하 고 수정 된 것으로 표시 됩니다. (프로젝트를 업데이트 하지 않으면이 코드로 선택적 접근 방식을 설명 하기 위해 표시만 했습니다.) 

[!code-csharp[Main](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_CreateAndAttach)]

웹 페이지 UI 필드의 모든 엔터티를 포함 하 고 그 중 하나를 업데이트할 수 있는 경우이 방법을 사용할 수 있습니다.

스 캐 폴드 코드 만들기 / 연결 방법을 사용 하 여 있지만 catch `DbUpdateConcurrencyException` 404 오류 코드를 예외 및 반환 합니다.  데이터베이스 업데이트 예외를 catch 하 고 오류 메시지를 표시 하는 표시 된 예제입니다.

### <a name="entity-states"></a>엔터티 상태

메모리에 엔터티는 해당 행에는 데이터베이스와 동기화 하 고이 정보를 호출할 때 수행 되는 작업을 결정 하는 여부를 추적 데이터베이스 컨텍스트는 `SaveChanges` 메서드. 예를 들어 전달 하는 경우에 새 엔터티는 `Add` 메서드 엔터티의 상태로 설정 된 `Added`합니다. 호출 하면는 `SaveChanges` 메서드, 데이터베이스 컨텍스트 SQL INSERT 명령을 실행 합니다.

엔터티 상태는 다음 중 하나일 수 있습니다.

* `Added`. 엔터티는 데이터베이스에 아직 존재 하지 않습니다. `SaveChanges` 메서드는 INSERT 문을 실행 합니다.

* `Unchanged`. 하 여이 엔터티를 사용 하 여 수행 해야 하는 아무 것도 `SaveChanges` 메서드. 데이터베이스에서 엔터티를 읽을 때 엔터티가이 상태에 있는 시작 합니다.

* `Modified`. 엔터티의 속성 값의 일부 또는 모든 수정 되었습니다. `SaveChanges` 메서드는 UPDATE 문을 실행 합니다.

* `Deleted`. 엔터티 삭제 하도록 표시 되었습니다. `SaveChanges` 메서드 DELETE 문을 실행 합니다.

* `Detached`. 엔터티는 데이터베이스 컨텍스트에서 추적 되 고 있지 않습니다.

데스크톱 응용 프로그램 상태 변경 내용이 일반적으로 자동으로 설정 됩니다. 엔터티를 읽고 해당 속성 값의 일부 변경 해야 합니다. 이렇게 하면 해당 엔터티 상태를 자동으로 변경 해야 `Modified`합니다. 호출 하면 `SaveChanges`, Entity Framework를 변경 하는 실제 속성만 업데이트 하는 SQL UPDATE 문을 생성 합니다.

웹 앱의 경우에 `DbContext` 엔터티와 페이지를 렌더링 한 다음 해당 데이터를 편집할 수에 삭제 표시 처음에 읽는 합니다. 경우는 HttpPost `Edit` 동작 메서드는, 새 웹 요청이 만들어지면 및의 새 인스턴스는 `DbContext`합니다. 새 해당 컨텍스트 내에서 엔터티를 다시 읽을 경우 데스크톱 처리를 시뮬레이션 합니다.

하지만 추가 읽기 작업을 수행 하지 않으려는 경우, 모델 바인더를 통해 생성 된 엔터티 개체를 사용 해야 합니다.  이 작업을 수행 하는 가장 간단한 방법은 앞에서 살펴본 대체 HttpPost 편집 코드 에서처럼 엔터티 상태를 수정한 날짜를 설정 하려면 됩니다. 호출 하면 `SaveChanges`, Entity Framework는 컨텍스트는 변경 되는 속성을 알 수 없기 때문에 데이터베이스 행의 모든 열을 업데이트 합니다.

읽기 중심 접근 방식을 방지 하려면 표시 되지만 사용자가 실제로 변경 된 필드에만 업데이트 하도록 SQL UPDATE 문을 시겠습니까 코드는 복잡 합니다. 에 특정 한 방식으로 원래 값을 저장 해야 (같은 숨겨진된 필드를 사용 하 여)를 사용할 수 있는 경우는 HttpPost `Edit` 메서드를 호출 합니다. 그러면 원래 값이 호출을 사용 하 여 학생 엔터티를 만들 수 있습니다는 `Attach` 메서드는 해당 원래 버전은 엔터티의 엔터티의 값을 새 값으로 업데이트 한 다음 호출 `SaveChanges`합니다.

### <a name="test-the-edit-page"></a>편집 페이지를 테스트 합니다.

응용 프로그램을 실행 하 고 선택 된 **학생** 탭을 클릭 한 다음는 **편집** 하이퍼링크입니다.

![학생 편집 페이지](crud/_static/student-edit.png)

클릭 하 여 확인 하 고 데이터의 일부 변경 **저장**합니다. **인덱스** 페이지가 열리고 변경 된 데이터를 표시 합니다.

## <a name="update-the-delete-page"></a>업데이트 페이지 삭제

*StudentController.cs*, 템플릿 코드는 HttpGet `Delete` 메서드는 `SingleOrDefaultAsync` 메서드 세부 정보 및 편집에서 볼 수 있듯이 선택한 학생 엔터티를 검색 하는 메서드입니다. 그러나을 구현 하는 사용자 지정 오류 메시지에 대 한 호출 `SaveChanges` 실패 하면이 메서드를 해당 보기의 일부 기능을 추가 합니다.

업데이트에 대 한 준다는 사실을 알았습니다 하 고 작업을 만드는 두 개의 작업 메서드 삭제 작업에 필요 합니다. GET 요청에 대 한 응답에서 호출 되는 메서드를 승인 하거나 삭제 작업을 취소 하는 사용자에 게 제공 하는 보기를 표시 합니다. 사용자가 승인 하는 경우 POST 요청 생성 됩니다. 이런 경우, 고 HttpPost `Delete` 메서드가 호출 되 고 실제로 해당 메서드에 삭제 동작을 수행 합니다.

Try catch 블록에서 HttpPost에 추가할 `Delete` 데이터베이스를 업데이트할 때 발생할 수 있는 오류를 처리 하는 메서드. 오류가 발생 하면 HttpPost Delete 메서드는 오류가 발생 했음을 나타내는 매개 변수 전달 HttpGet Delete 메서드를 호출 합니다. 그런 다음 HttpGet Delete 메서드는 기회를 취소 하거나 다시 시도 하십시오. 사용자에 게 제공 하는 오류 메시지와 함께 확인 페이지 다시 표시 됩니다.

교체는 HttpGet `Delete` 동작 메서드를 다음 코드로, 오류 보고를 관리 하는 합니다.

[!code-csharp[Main](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_DeleteGet&highlight=1,9,16-21)]

이 코드는 변경 내용을 저장 하려면 오류 발생 후 메서드가 호출 된 있는지 여부를 나타내는 선택적 매개 변수를 허용 합니다. 이 매개 변수는 false는 HttpGet `Delete` 이전에 실패 한 없이 메서드를 호출 합니다. HttpPost에서 호출 될 때 `Delete` 데이터베이스 업데이트 오류에 대 한 응답에서 메서드 매개 변수는 true 및 보기에 오류 메시지가 전달 됩니다.

### <a name="the-read-first-approach-to-httppost-delete"></a>HttpPost 삭제에 대 한 읽기 중심 접근

대체는 HttpPost `Delete` 동작 메서드 (라는 `DeleteConfirmed`)를 다음 코드로 실제 삭제 동작을 수행 하 고이 한 데이터베이스 업데이트 오류를 catch 합니다.

[!code-csharp[Main](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_DeleteWithReadFirst&highlight=6,8-11,13-14,18-23)]

이 코드에서는 선택된 된 엔터티를 검색 한 다음 호출는 `Remove` 엔터티의 상태 설정 하는 방법은 `Deleted`합니다. 때 `SaveChanges` 호출, SQL DELETE 명령이 생성 합니다.

### <a name="the-create-and-attach-approach-to-httppost-delete"></a>HttpPost 삭제 하는 만들기 / 연결 방법

주를 사용 하 여 학생 엔터티를 인스턴스화하여 불필요 한 SQL 쿼리를 방지할 수는 대규모 응용 프로그램의 성능을 개선 우선 순위 이면 키 값 및 다음 엔터티 상태를 설정 하는 것 `Deleted`합니다. Entity Framework는 엔터티를 삭제 하는 데 필요한 모든입니다. (프로젝트에이 코드를 배치 하지 마십시오; 대신을 보여 주기 위해 여기는 합니다.)

[!code-csharp[Main](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_DeleteWithoutReadFirst&highlight=7-8)]

엔터티도 삭제 해야 하는 데이터 관련에 cascade delete 해당 데이터베이스에 구성 되어 있는지 확인 합니다. 엔터티 삭제에이 접근 방식 EF 사실을 모를 수 관련 엔터티가 삭제 됩니다.

### <a name="update-the-delete-view"></a>삭제 뷰 업데이트

*Views/Student/Delete.cshtml*, 다음 예제와 같이 h2 제목 및 h3 머리글 사이 오류 메시지가 추가:

[!code-html[](intro/samples/cu/Views/Students/Delete.cshtml?range=7-9&highlight=2)]

페이지를 선택 하 여 실행 된 **학생** 탭을 클릭 하 고는 **삭제** 하이퍼링크:

![삭제 확인 페이지](crud/_static/student-delete.png)

클릭 **삭제**합니다. 인덱스 페이지 삭제 된 학생 없이 표시 됩니다. (오류 처리 코드 동시성 자습서에서 실제 동작에서의 예를 볼 수 있습니다.)

## <a name="closing-database-connections"></a>데이터베이스 연결 닫기

데이터베이스 연결을 보유 하는 리소스를 확보 하려면 컨텍스트 인스턴스가 삭제 해야 최대한 빨리을 마쳤습니다. ASP.NET Core 기본 제공 [종속성 주입](../../fundamentals/dependency-injection.md) 해당 작업을 담당 합니다.

*Startup.cs*, 호출 하는 [AddDbContext 확장 메서드](https://github.com/aspnet/EntityFramework/blob/03bcb5122e3f577a84498545fcf130ba79a3d987/src/Microsoft.EntityFrameworkCore/EntityFrameworkServiceCollectionExtensions.cs) 프로 비전 하는 `DbContext` ASP.NET DI 컨테이너에는 클래스입니다. 메서드를 서비스 수명을 설정 `Scoped` 기본적으로 합니다. `Scoped`컨텍스트 개체 수명 웹 요청 라이프 시간과 일치 하는 방법 및 `Dispose` 메서드가 웹 요청이 끝날 때 자동으로 호출 됩니다.

## <a name="handling-transactions"></a>트랜잭션 처리

기본적으로 Entity Framework는 트랜잭션을 암시적으로 구현합니다. 여러 행 이나 테이블을 변경 하 고 호출 하는 다음 시나리오에서 `SaveChanges`, Entity Framework 하는지 자동으로 확인 중 하나 변경 내용을 모두 성공 하거나 모두 실패 합니다. 일부 변경 내용이 먼저 완료 되는 경우 오류가 발생 하는 다음 해당 변경 내용이 자동으로 롤백됩니다. 여기서 필요한 세부적으로 제어할 수-예를 들어 트랜잭션에서-Entity Framework 밖에 서 수행 하는 작업을 포함 하도록 하려는 경우 시나리오 참조 [트랜잭션을](https://docs.microsoft.com/ef/core/saving/transactions)합니다.

## <a name="no-tracking-queries"></a>No 추적 쿼리

데이터베이스 컨텍스트 테이블 행을 검색 하 고을 나타내는 엔터티 개체를 만드는 때 기본적으로를 추적 데이터베이스에 포함 된 내용으로 메모리에 엔터티 동기화 되었는지 여부입니다. 데이터를 메모리에에서 한 캐시 역할을 하 고 엔터티를 업데이트할 때 사용 됩니다. 이 캐싱은 종종에 필요 하지 않은 웹 응용 프로그램 컨텍스트는 일반적으로 수명이 짧은 (새로운 삭제을 만들어 각 요청에 대 한) 인스턴스와 컨텍스트 때문에 읽는 엔터티는 해당 엔터티를 다시 사용 하기 전에 일반적으로 삭제 됩니다.

호출 하 여 메모리에 엔터티 개체의 추적을 해제할 수 있습니다는 `AsNoTracking` 메서드. 다음과 같은 일반적인 시나리오를 사용할 수 있습니다.

* 컨텍스트 수명 동안 않아도 모든 엔터티를 업데이트 하 고 EF에 필요 하지 않습니다 [별도 쿼리에 의해 검색 된 엔터티가 포함 된 탐색 속성을 자동으로 로드](read-related-data.md)합니다. 자주 HttpGet 동작 메서드에서 컨트롤러의 이러한 조건이 충족 합니다.

* 많은 양의 데이터를 검색 하는 쿼리를 실행 하는 및 반환된 된 데이터의 작은 부분만 업데이트 됩니다. 보다 효과적으로 큰 쿼리에 대해 추적을 해제 하 고 나중에 업데이트 해야 하는 몇 가지 엔터티에 대 한 쿼리를 실행할 수 있습니다.

* 업데이트 하려면 엔터티를 연결 하려고 하지만 서로 다른 목적을 위해 동일한 엔터티를 검색할 이전 합니다. 엔터티는 데이터베이스 컨텍스트에서 이미 추적 중인를 변경 하려면 해당 하는 엔터티를 연결할 수 없습니다. 호출 하는 것이 상황을 처리 하는 한 가지 방법은 `AsNoTracking` 이전 쿼리 합니다.

자세한 내용은 참조 [vs를 추적 합니다. No 추적](https://docs.microsoft.com/ef/core/querying/tracking)합니다.

## <a name="summary"></a>요약

학생 엔터티에 대 한 간단한 CRUD 작업을 수행 하는 페이지에 대 한 전체 집합이 생깁니다. 다음 자습서에서의 기능을 확장 합니다는 **인덱스** 정렬, 필터링 및 페이징을 추가 하 여 페이지입니다.

>[!div class="step-by-step"]
[이전](intro.md)
[다음](sort-filter-page.md)  
