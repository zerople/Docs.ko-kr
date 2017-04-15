---
title: "ASP.NET Core MVC EF 코어-데이터 모델-5/10 | Microsoft 문서"
author: tdykstra
description: "이 자습서에서는 더 많은 엔터티 및 관계를 추가 하 고 서식 지정, 유효성 검사 및 데이터베이스 매핑 규칙을 지정 하 여 데이터 모델을 사용자 지정 합니다."
keywords: "ASP.NET Core, Entity Framework Core 데이터 주석"
ms.author: tdykstra
manager: wpickett
ms.date: 03/15/2017
ms.topic: article
ms.assetid: 0dd63913-a041-48b6-96a4-3aeaedbdf5d0
ms.technology: aspnet
ms.prod: asp.net-core
uid: data/ef-mvc/complex-data-model
translationtype: Machine Translation
ms.sourcegitcommit: c9d2009574592b4eca9cb82b7d6e752da512fe9a
ms.openlocfilehash: 8ca3102d5c1c0255ea90f7fb9989ed173d0dbc5b
ms.lasthandoff: 03/23/2017

---

# <a name="creating-a-complex-data-model---ef-core-with-aspnet-core-mvc-tutorial-5-of-10"></a>EF 코어 ASP.NET 핵심 MVC 자습서 (5/10)는 복잡 한 데이터 모델 만들기

여 [Tom Dykstra](https://github.com/tdykstra) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)

Contoso University 샘플 웹 응용 프로그램에는 Entity Framework 코어 1.1 및 Visual Studio 2017를 사용 하 여 ASP.NET 핵심 1.1 MVC 웹 응용 프로그램을 만드는 방법을 보여 줍니다. 자습서 시리즈에 대 한 정보를 참조 하십시오. [시리즈의 첫 번째 자습서](intro.md)합니다.

이전 자습서의 세 가지 엔터티 작성 된 간단한 데이터 모델을 사용 했습니다. 이 자습서에서는 더 많은 엔터티 및 관계를 추가 합니다 및 서식 지정, 유효성 검사 및 데이터베이스 매핑 규칙을 지정 하 여 데이터 모델을 사용자 지정 합니다.

완료 되 면 엔터티 클래스를 다음 그림에 표시 되는 완료 된 데이터 모델을 수행 합니다.

![엔터티 다이어그램](complex-data-model/_static/diagram.png)

## <a name="customize-the-data-model-by-using-attributes"></a>특성을 사용 하 여 데이터 모델을 사용자 지정

이 섹션의 서식, 유효성 검사 및 데이터베이스 매핑 규칙을 지정 하는 특성을 사용 하 여 데이터 모델을 사용자 지정 하는 방법을 알아보겠습니다. 그런 다음 몇 개를 만들어야 하는 다음과 같은 섹션에 추가 하 여 전체 학교 데이터 모델 특성 클래스에 이미 생성 하 고 모델에는 나머지 엔터티 형식에 대 한 새 클래스를 만들어입니다.

### <a name="the-datatype-attribute"></a>데이터 형식 특성

학생 등록 날짜에 대 한 모든 웹 페이지의 현재 시간을 표시, 날짜와 함께 날짜는이 필드에 대 한 중요 한 모든 합니다. 데이터 주석 특성을 사용 하 여 수 하나를 수행 하면 코드에서 데이터를 표시 하는 모든 보기의 표시 형식을 해결 하는 변경 합니다. 특성을 추가 합니다, 작업을 수행 하는 방법의 예제를 보려면는 `EnrollmentDate` 속성에는 `Student` 클래스입니다.

*Models/Student.cs*, 추가 `using` 에 대 한 정보는 `System.ComponentModel.DataAnnotations` 네임 스페이스를 추가 하 고 `DataType` 및 `DisplayFormat` 특성을 `EnrollmentDate` 속성을 다음 예와에서 같이:

[!code-csharp[주](intro/samples/cu/Models/Student.cs?name=snippet_DataType&highlight=3,12-13)]

`DataType` 특성 데이터베이스 내장 형식 보다 구체적인 데이터 형식을 지정 하는 데 사용 됩니다. 이 경우에 원하는 추적 하기 위해 날짜, 날짜 및 시간입니다. `DataType` 날짜, 시간, 전화 번호, 통화, 전자 우편 주소 등과 같은 많은 데이터 형식에 대 한 열거형을 제공 합니다. `DataType` 특성이 자동으로 특정 형식의 기능을 제공 하도록 응용 프로그램을 사용할 수도 있습니다. 예를 들어 한 `mailto:` 에 대 한 링크를 만들 수 `DataType.EmailAddress`에 대 한 날짜 선택기를 제공할 수 있습니다 `DataType.Date` HTML5 지원 브라우저에 합니다. `DataType` 특성 내보냅니다 HTML 5 `data-` (샵된 데이터 대시) 특성을 HTML 5 브라우저 이해할 수 있습니다. `DataType` 특성 유효성 검사를 제공 하지 않습니다.

`DataType.Date`표시 되는 날짜의 형식을 지정 하지 않습니다. 데이터 필드는 기본적으로 서버의 CultureInfo에 따라 기본 형식에 따라 표시 됩니다.

`DisplayFormat` 특성 날짜 형식을 명시적으로 지정 하는 데 사용 됩니다.

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

`ApplyFormatInEditMode` 설정은 서식을 적용 해야 하는지도 값 편집을 위해 텍스트 상자에 표시 될 때 지정 합니다. (않을 수도 있습니다-일부 필드에 대 한 예를 들어 통화 값에 대 한 수 원하지 텍스트 상자에 통화 기호 편집 합니다.)

사용할 수는 `DisplayFormat` 자체를 만들었지만 특성은 일반적으로 사용 하는 것이 좋습니다는 `DataType` 도 특성입니다. `DataType` 특성을 화면에 렌더링 하는 방법이 아니라 데이터의 의미 체계를 전달 하 고으로 얻을 수 없는 다음과 같은 이점을 제공 `DisplayFormat`:

* 브라우저는 HTML5 기능을 활성화할 수 (calendar 컨트롤, 로캘에 적합 한 통화 기호, 전자 메일 링크를 표시 하는 등 일부 클라이언트 쪽 입력 유효성 검사 등입니다.).

* 기본적으로 브라우저는 사용자의 로캘에 따른 올바른 형식을 사용 하 여 데이터를 렌더링 합니다.

자세한 내용은 참조는 [ \<입력 > 태그 도우미 설명서](../../mvc/views/working-with-forms.md#the-input-tag-helper)합니다.

학생 들에 게 인덱스 페이지를 다시 실행 하 고 등록 날짜에 대 한 번 더 이상 표시 됩니다. 동일한 학생 모델을 사용 하는 보기에 적용 됩니다.

![시간 없이 날짜를 표시 하는 학생 인덱스 페이지](complex-data-model/_static/dates-no-times.png)

### <a name="the-stringlength-attribute"></a>StringLength 특성

데이터 유효성 검사 규칙 및 특성을 사용 하 여 유효성 검사 오류 메시지를 지정할 수도 있습니다. `StringLength` 특성 데이터베이스에서 최대 길이 설정 하 고 클라이언트 쪽과 서버 쪽 제공 ASP.NET MVC에 대 한 유효성을 검사 합니다. 이 특성에 최소 문자열 길이 지정할 수도 있습니다 수 있지만 최소값 데이터베이스 스키마에 대 한 영향을 주지 않습니다.

사용자가 이름에 50 개 이상의 문자를 입력 하지 않도록 하려는 경우를 가정 합니다. 이러한 제한에 추가 하려면 추가 `StringLength` 특성을 `LastName` 및 `FirstMidName` 속성을 다음 예와에서 같이 합니다.

[!code-csharp[주](intro/samples/cu/Models/Student.cs?name=snippet_StringLength&highlight=10,12)]

`StringLength` 특성 이름에 공백을 입력에서 사용자를 방해가 되지 않습니다. 사용할 수는 `RegularExpression` 입력에 제한을 적용 하는 특성입니다. 예를 들어 다음 코드는 첫 번째 문자를 대문자로 하 고 나머지 문자를 사전순으로 필요 합니다.

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z''-'\s]*$")]
```

`MaxLength` 에 유사한 기능을 제공 하는 특성의 `StringLength` 특성 하지만 클라이언트 쪽을 제공 하지 않습니다 유효성 검사 합니다.

이제 데이터베이스 모델 데이터베이스 스키마의 내용을 변경 해야 하는 방식으로 변경 되었습니다. 스키마를 업데이트 하는 응용 프로그램 UI를 사용 하 여 데이터베이스에 추가 될 수 있습니다 하면 데이터 손실 없이 마이그레이션을 사용 합니다.

변경 내용을 저장 하 고 프로젝트를 빌드하십시오. 프로젝트 폴더에서 명령 창을 열고 다음 명령을 입력 하 고 합니다.

```console
dotnet ef migrations add MaxLengthOnNames
dotnet ef database update
```

`migrations add` 명령 데이터 손실이 발생할 수 있는 두 개의 열에 대 한 짧은 최대 길이 사용 하면 변경 하기 때문에 경고 합니다.  마이그레이션 라는 파일을 만듭니다 *<timeStamp>_MaxLengthOnNames.cs*합니다. 이 파일의 코드를 포함 된 `Up` 현재 데이터 모델과 일치 하도록 데이터베이스를 업데이트 하는 방법입니다. `database update` 명령이 해당 코드를 실행 합니다.

마이그레이션 파일 이름 앞에 추가 하는 타임 스탬프를 마이그레이션 요청 Entity Framework에서 사용 됩니다. 데이터베이스 업데이트 명령을 실행 하기 전에 마이그레이션을 여러 건 만들고 그런 다음 생성 된 순서에 적용 됩니다 모든 마이그레이션 합니다.

만들기 페이지를 실행 하 고 50 자 보다 긴 이름 중 하나를 입력 합니다. 만들기를 클릭 하면 클라이언트 쪽 유효성 검사 오류 메시지가 표시 됩니다.

![학생 인덱스 문자열 길이 오류를 보여주는 페이지](complex-data-model/_static/string-length-errors.png)

### <a name="the-column-attribute"></a>열 특성

또한 클래스 및 속성에 매핑하는 방식을 데이터베이스를 제어 하려면 특성을 사용할 수 있습니다. 이름을 사용한 가정 `FirstMidName` 첫 번째 이름에 대 한 중간 이름 필드가 포함 될 수도 있으므로 필드입니다. 이름을 지정 하는 데이터베이스 열이 있지만 `FirstName`이므로 데이터베이스에 대 한 임시 쿼리를 작성 하는 사용자는 해당 이름에 익숙해져 있습니다. 이 매핑은 위해 사용할 수는 `Column` 특성입니다.

`Column` 특성을 지정 하는 데이터베이스를 만들 때의 열은 `Student` 테이블에 매핑되는 `FirstMidName` 속성 이름이 지정 됩니다 `FirstName`합니다. 즉 때 코드 참조 `Student.FirstMidName`, 데이터에서 옵니다 또는에서 업데이트할 수는 `FirstName` 의 열은 `Student` 테이블입니다. 열 이름을 지정 하지 않으면, 속성 이름으로 동일한 이름을 제공 됩니다.

에 *Student.cs* 파일에서 추가 `using` 문을 `System.ComponentModel.DataAnnotations.Schema` 열 이름 특성을 추가 하 고는 `FirstMidName` 속성을 다음 강조 표시 된 코드에 나와 있는 것 처럼:

[!code-csharp[주](intro/samples/cu/Models/Student.cs?name=snippet_Column&highlight=4,14)]

변경 내용을 저장 하 고 프로젝트를 빌드하십시오.

추가 `Column` 특성이 모델이 변경 된 `SchoolContext`이므로 데이터베이스를 일치 하지 않습니다.

변경 내용을 저장 하 고 프로젝트를 빌드하십시오. 프로젝트 폴더에서 명령 창을 열고 하 고 다른 마이그레이션 만들려면 다음 명령을 입력 합니다.

```console
dotnet ef migrations add ColumnFirstName
dotnet ef database update
```

**SQL Server 개체 탐색기**를 두 번 클릭 하 여 학생 테이블 디자이너를 열고는 **학생** 테이블입니다.

![마이그레이션 후 SSOX에서 학생 들에 게 테이블](complex-data-model/_static/ssox-after-migration.png)

처음 두 마이그레이션을 적용 하기 전에 이름 열 형식 nvarchar (max) 되었습니다. 이제 nvarchar (50) 않으며 FirstMidName에서 FirstName 열 이름이 변경 되었습니다.

> [!Note]
> 다음 섹션의 모든 엔터티 클래스를 만든 후 전에 컴파일 하려고 하면 컴파일러 오류가 발생할 수 있습니다.

## <a name="final-changes-to-the-student-entity"></a>최종 학생 엔터티 변경

![학생 엔터티](complex-data-model/_static/student-entity.png)

*Models/Student.cs*, 추가한 코드를 이전를 다음 코드로 바꿉니다. 변경 내용은 강조 표시 됩니다.

[!code-csharp[주](intro/samples/cu/Models/Student.cs?name=snippet_BeforeInheritance&highlight=11,13,15,18,22,25-31)]

### <a name="the-required-attribute"></a>필수 특성

`Required` 이 특성은 이름 속성 필수 필드입니다. `Required` 같은 값 형식 nullable이 아닌 형식에 대 한 특성이 필요 하지 않습니다 (DateTime, int, 두 번, float, 등입니다.). Null 일 수 없는 형식 필수 필드로 자동으로 처리 됩니다.

제거할 수는 `Required` 특성 및에 대 한 최소 길이 매개 변수로 대체할는 `StringLength` 특성:

```csharp
[Display(Name = "Last Name")]
[StringLength(50, MinimumLength=1)]
public string LastName { get; set; }
```

### <a name="the-display-attribute"></a>표시 특성

`Display` 특성 지정 캡션 텍스트 상자에 대 한 각 인스턴스 (에 단어를 분할 하는 공간이 없습니다) "이름", "Last Name", "전체 이름", 및 속성 이름 대신 "등록 날짜" 이어야 합니다.

### <a name="the-fullname-calculated-property"></a>계산 하는 FullName 속성

`FullName`다른 두 개의 속성을 연결 하 여 생성 되는 값을 반환 하는 계산 된 속성이입니다. 따라서에 get 접근자만 및 no `FullName` 열이 데이터베이스에 생성 됩니다.

## <a name="create-the-instructor-entity"></a>Instructor 엔터티 만들기

![Instructor 엔터티](complex-data-model/_static/instructor-entity.png)

만들 *Models/Instructor.cs*, 템플릿 코드를 다음 코드로 바꿉니다.

[!code-csharp[주](intro/samples/cu/Models/Instructor.cs?name=snippet_BeforeInheritance)]

몇 가지 속성은 학생 및 강사 엔터티의 동일한 지 확인 합니다. 에 [상속 구현](inheritance.md) 이 시리즈의 뒷부분에 나오는 자습서에서는 중복을 제거 하기 위해이 코드를 리팩터링 합니다.

쓸 수 있도록 한 줄에 여러 특성을 삽입할 수는 `HireDate` 특성을 다음과 같이 합니다.

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="the-courseassignments-and-officeassignment-navigation-properties"></a>CourseAssignments와 OfficeAssignment 탐색 속성

`CourseAssignments` 및 `OfficeAssignment` 속성은 탐색 속성입니다.

강사 임의 개수의 과정을 가르칠 수도 하므로 `CourseAssignments` 컬렉션으로 정의 됩니다.

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

탐색 속성 수 여러 엔터티를 유지 하는 형식과 항목 추가, 삭제 및 업데이트 될 수 있는 목록 이어야 합니다.  지정할 수 있습니다 `ICollection<T>` 또는과 같은 형식 `List<T>` 또는 `HashSet<T>`합니다. 지정 하는 경우 `ICollection<T>`, EF 만듭니다는 `HashSet<T>` 기본적으로 컬렉션입니다.

이유는 이러한 이유 `CourseAssignment` 엔터티 아래 다 대 다 관계에 대 한 섹션에서 설명 됩니다.

Contoso University 비즈니스 규칙 상태 강사 많아야 한 office를 하나만 사용할 수 있으므로 `OfficeAssignment` (없는 office 할당 된 경우 null 일 수 있음)는 단일 OfficeAssignment 엔터티 속성에 저장 합니다.

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="create-the-officeassignment-entity"></a>OfficeAssignment 엔터티 만들기

![OfficeAssignment 엔터티](complex-data-model/_static/officeassignment-entity.png)

만들 *Models/OfficeAssignment.cs* 다음 코드를 사용 합니다.

[!code-csharp[주](intro/samples/cu/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a>키 특성

Instructor와 OfficeAssignment 엔터티 간의&0; 또는&1;에 한 관계가 있습니다. 기본 키 역시 해당 외래 키 Instructor 엔터티를 따라서을 사무실 할당을 기준으로 할당 된 강사만 존재 합니다. 하지만 해당 이름을 ID 또는 classnameID 명명 규칙을 따르지 않는 때문에 Entity Framework이 엔터티의 기본 키로 자동으로 InstructorID 인식할 수 없습니다. 따라서는 `Key` 특성 키로 식별 하는 데 사용 됩니다.

```csharp
[Key]
public int InstructorID { get; set; }
```

사용할 수도 있습니다는 `Key` 특성 엔터티의 기본 키 있지만 classnameID 또는 id입니다. 이외의 항목 속성의 이름을 지정 하는 경우

기본적으로 EF 식별 관계에 대 한 열이 때문에 데이터베이스 생성 된로 키를 처리 합니다.

### <a name="the-instructor-navigation-property"></a>강사 탐색 속성

Instructor 엔터티는 null을 허용 `OfficeAssignment` 탐색 속성 (강사 하지 않을 수 있으므로 사무실 할당), OfficeAssignment 엔터티는 nullable이 아닌 있으며 `Instructor` 탐색 속성 (사무실 할당 강사-없으면 불가능 하기 때문에 `InstructorID` 은 nullable이 아닌). Instructor 엔터티에 관련된 OfficeAssignment 엔터티, 각 엔터티에 탐색 속성에 다른 하나에 대 한 참조가 있습니다.

배치할 수는 `[Required]` 특성을 지정 하는 관련된 강사 해야 하지만 때문에 그럴 필요가 없습니다 강사 탐색 속성에는 `InstructorID` 외래 키 (이이 테이블에 키)는 null을 허용 하지.

## <a name="modify-the-course-entity"></a>Course 엔터티를 수정 합니다.

![Course 엔터티](complex-data-model/_static/course-entity.png)

*Models/Course.cs*, 추가한 코드를 이전를 다음 코드로 바꿉니다. 변경 내용은 강조 표시 됩니다.

[!code-csharp[주](intro/samples/cu/Models/Course.cs?name=snippet_Final&highlight=2,10,13,16,21,23)]

Course 엔터티에 외래 키 속성이 `DepartmentID` 에 관련 된 부서 엔터티를 가리키는 `Department` 탐색 속성입니다.

Entity Framework 관련된 엔터티에 대 한 탐색 속성을 사용 하는 경우 데이터 모델에 외래 키 속성을 추가 하 여 필요 하지 않습니다.  EF 자동으로 외래 키에 데이터베이스는 필요할 때마다 만들고 만듭니다 [그림자 속성이](https://docs.microsoft.com/ef/core/modeling/shadow-properties) 해당 합니다. 하지만 데이터 모델에서 외래 키가 있는 업데이트를 수행할 수 간단 하 고 더 효율적입니다. 예를 들어 편집 하려면 course 엔터티를 가져오는 경우 부서 엔터티와 null을 로드 하지 따라서 course 엔터티를 업데이트할 때 해야 부서 엔터티를 먼저 가져온 합니다. 때 외래 키 속성 `DepartmentID` 포함 된 데이터 모델의 부서 엔터티를 업데이트 하기 전에 가져온 필요가 없습니다.

### <a name="the-databasegenerated-attribute"></a>DatabaseGenerated 특성

`DatabaseGenerated` 특성을 `None` 에 매개 변수는 `CourseID` 기본 키 값은 사용자가 제공한 것이 아니라 있음을 데이터베이스에 의해 생성 된 속성을 지정 합니다.

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

기본적으로 Entity Framework에서는 기본 키 값은 데이터베이스에서 생성 되어을 가정 합니다. 대부분의 시나리오에서 원하는 것이입니다. 그러나 코스 엔터티에 대 한 1000 계열 등의 숫자를 사용자 지정 하는 과정을 사용 하 여 한 부서, 다른 부서에 대 한 2000 계열에 대 한 고 합니다 등.

`DatabaseGenerated` 날짜를 기록 하는 데 사용 되는 데이터베이스 열의 경우 행 작성 되거나 업데이트 된 대로 기본 값을 생성 하려면 특성 에서도 사용할 수 있습니다.  자세한 내용은 참조 [생성 된 속성](https://docs.microsoft.com/ef/core/modeling/generated-properties)합니다.

### <a name="foreign-key-and-navigation-properties"></a>외래 키 및 탐색 속성입니다.

외래 키 속성과 탐색 속성을 Course 엔터티 다음과 같은 관계를 반영 합니다.

코스 한 부서에 할당 되므로는 `DepartmentID` 외래 키와 `Department` 위에서 언급 한 이유 때문에 탐색 속성입니다.

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

제공 하는 과정을 등록 하는 학생의 원하는 수를 사용할 수 있으므로 `Enrollments` 탐색 속성은 컬렉션:

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

여러 강사 하 여 제공 하는 과정을 유도할 수 있습니다 않으므로 `CourseAssignments` 탐색 속성은 컬렉션 (형식 `CourseAssignment` 설명 [나중](#many-to-many-relationships):

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

## <a name="create-the-department-entity"></a>부서 엔터티 만들기

![부서 엔터티](complex-data-model/_static/department-entity.png)


만들 *Models/Department.cs* 다음 코드를 사용 합니다.

[!code-csharp[주](intro/samples/cu/Models/Department.cs?name=snippet_Begin)]

### <a name="the-column-attribute"></a>열 특성

사용 하는 이전는 `Column` 열 이름 매핑을 변경할 특성입니다. 부서 엔터티에 대 한 코드는 `Column` SQL 데이터 형식 매핑 데이터베이스에 SQL Server 통화 형식을 사용 하 여 열 정의 됩니다 되도록 변경 하려면 특성의 사용:

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

열 매핑 일반적으로 필요 하지 않은, Entity Framework 속성에 대해 정의 하는 CLR 형식에 따라 적절 한 SQL Server 데이터 형식을 선택 합니다. CLR `decimal` 형식이 매핑되는 SQL Server에 `decimal` 유형입니다. 하지만 경우 파악 한 열은 유지 하는 통화 금액 money 데이터 형식에 방법이 더 적합 하 합니다.

### <a name="foreign-key-and-navigation-properties"></a>외래 키 및 탐색 속성입니다.

다음과 같은 관계를 반영 하는 외래 키 및 탐색 속성:

부서 수 있거나 없는 관리자 및 관리자는 항상 강사입니다. 따라서는 `InstructorID` 속성은 강사 엔터티에 외래 키로 포함 되어 있으며 뒤에 물음표 추가 됩니다는 `int` null 허용으로 속성을 표시 하는 지정을 입력 합니다. 탐색 속성의 이름은 `Administrator` Instructor 엔터티를 보유 하지만:

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

부서 과정 탐색 속성 이므로 많은 과목을 가질 수 있습니다.

```csharp
public ICollection<Course> Courses { get; set; }
```

> [!NOTE]
> 규칙에 Entity Framework에서는 다 대 다 관계 및 nullable이 아닌 외래 키에 대 한 하위 삭제. 이 마이그레이션을 추가 하려고 하면 예외가 발생 하는 순환 하위 삭제 규칙 발생할 수 있습니다. 예를 들어, null 허용으로 Department.InstructorID 속성을 정의 하지 않은 EF 부서, 그렇지 않은 사용자를 삭제 하면 강의 삭제 하는 하위 삭제 규칙을 구성 합니다. 비즈니스 규칙이 필요한 경우는 `InstructorID` 속성을 nullable이 아닌, 다음 fluent API 문을 사용 하 여 관계에 하위 삭제를 사용 하지 않도록 설정 해야 합니다.
> ```csharp
> modelBuilder.Entity<Department>()
>    .HasOne(d => d.Administrator)
>    .WithMany()
>    .OnDelete(DeleteBehavior.Restrict)
> ```

## <a name="modify-the-enrollment-entity"></a>등록 엔터티를 수정 합니다.

![등록 엔터티](complex-data-model/_static/enrollment-entity.png)

*Models/Enrollment.cs*, 추가한 코드를 이전를 다음 코드로 바꿉니다.

[!code-csharp[주](intro/samples/cu/Models/Enrollment.cs?name=snippet_Final&highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a>외래 키 및 탐색 속성입니다.

외래 키 속성과 탐색 속성은 다음과 같은 관계 반영 됩니다.

이므로 단일 과목에 대 한 등록 레코드는 한 `CourseID` 외래 키 속성 및 `Course` 탐색 속성:

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

등록 레코드 이므로 단일 학생의 않습니다는 `StudentID` 외래 키 속성 및 `Student` 탐색 속성:

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a>다 대 다 관계

학생 및 Course 엔터티 간에 다 대 다 관계가 하 고 등록 엔터티 역할 다 대 다 조인 테이블 *페이로드와 함께* 데이터베이스에 있습니다. "Payload" with Enrollment 테이블에 추가 데이터 (이 경우, 기본 키 및 등급 속성)에서 조인 된 테이블에 대 한 외래 키 외에도 포함 되어 있는지를 의미 합니다.

다음 그림 엔터티 다이어그램에서 이러한 관계 모양을 보여 줍니다. (이 다이어그램은 EF에 대 한 Entity Framework Power Tools를 사용 하 여 생성 된 6.x; 자습서의 일부가 아닌 다이어그램을 만들기만 사용 되는 물체 여기.)

![학생 과정 많은 일 대 다 관계](complex-data-model/_static/student-course.png)

각 관계 선을에, 일 대 다 관계를 나타내는 별표 (*) 및 한쪽 끝에 있는 1에 있습니다.

등록 테이블 등급 정보를 포함 하지 않은 경우 CourseID 및 StudentID 두 개의 외래 키를 포함 해야만 합니다. 이 경우 것 페이로드 없이 다 대 다 조인 테이블 (또는 pure join table) 데이터베이스에서입니다. 강사 및 코스 엔터티 종류 다 대 다 관계의 있고 페이로드 없이 조인 테이블으로 작동 하도록 엔터티 클래스를 만들려면 다음 단계는 합니다.

(EF 6.x 지원 다 대 다 관계를 지원 하지만 EF 코어에 대 한 암시적 조인 테이블에는 없습니다. 자세한 내용은 참조는 [EF 코어 GitHub 리포지토리의 토론](https://github.com/aspnet/EntityFramework/issues/1368).) 

## <a name="the-courseassignment-entity"></a>CourseAssignment 엔터티

![CourseAssignment 엔터티](complex-data-model/_static/courseassignment-entity.png)

만들 *Models/CourseAssignment.cs* 다음 코드를 사용 합니다.

[!code-csharp[주](intro/samples/cu/Models/CourseAssignment.cs)]

### <a name="join-entity-names"></a>엔터티 이름 가입

조인 테이블 강사-코스 다 대 다 관계에 대 한 데이터베이스에 필요 하며 엔터티 집합으로 표현할 수 있습니다. 일반적으로 조인 엔터티 이름을 `EntityName1EntityName2`는 경우 그렇게 할 수 `CourseInstructor`합니다. 그러나 관계를 설명 하는 이름을 선택 하는 것이 좋습니다. 데이터 모델은 간단한 시작 및 자주 페이로드를 나중에 가져오기 no 페이로드 조인을 사용 하 여 증가 합니다. 설명이 포함 된 엔터티 이름으로 시작 하는 경우에 나중에 이름을 변경할 필요가 없습니다. 이상적으로 조인 엔터티 자연 (가능한 한 단어) 이름을 자체 비즈니스 도메인에 포함 합니다. 예를 들어, 책과 고객 등급을 통해 연결할 수 없습니다. 이 관계에 대 한 `CourseAssignment` 보다 더 유용 `CourseInstructor`합니다.

### <a name="composite-key"></a>복합 키

외래 키가 null을 허용 하며 고유 하 게 되므로 테이블의 각 행을 식별, 별도 기본 키에 대 한 필요가 없습니다. *InstructorID* 및 *CourseID* 속성은 복합 기본 키로 작동 해야 합니다. EF 복합 기본 키를 식별 하는 유일한 방법은 사용 하는 것은 *fluent API* (수행할 수 없습니다 특성을 사용 하 여). 다음 섹션에서 복합 기본 키를 구성 하는 방법에 표시 됩니다.

복합 키 하면 한 코스 및 하나의 강사에 대 한 여러 행에 대 한 여러 행을 포함할 수 있지만 동일한 강사 및 과정에 대 한 여러 행 사용할 수 없습니다. `Enrollment` 조인 엔터티는 이러한 종류의 중복 될 수 있으므로 고유한 기본 키를 정의 합니다. 이러한 중복을 방지 하려면 외래 키 필드에 고유 인덱스를 추가 하거나 구성할 수 `Enrollment` 비슷합니다 복합 기본 키가 있는 `CourseAssignment`합니다. 자세한 내용은 참조 [인덱스](https://docs.efproject.net/en/latest/modeling/indexes.html)합니다.

## <a name="update-the-database-context"></a>데이터베이스 컨텍스트를 업데이트 합니다.

다음과 같은 강조 표시 된 코드를 추가 하는 *Data/SchoolContext.cs* 파일:

[!code-csharp[주](intro/samples/cu/Data/SchoolContext.cs?name=snippet_BeforeInheritance&highlight=15-18,25-31)]

이 코드는 새 엔터티를 추가 하 고 CourseAssignment 엔터티의 복합 기본 키를 구성 합니다.

## <a name="fluent-api-alternative-to-attributes"></a>특성에 대 한 fluent API 대안

코드는 `OnModelCreating` 의 메서드는 `DbContext` 클래스에서 사용 하는 *fluent API* EF 동작을 구성 하려면. 이 예제에서와 같이 단일 문으로 일련의 메서드 호출을 함께 연결 하 여 자주 사용 되므로 API "fluent" 라고는 [EF 핵심 설명서](https://docs.microsoft.com/en-us/ef/core/modeling/#methods-of-configuration):

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

이 자습서에서는 특성으로 작업할 수 없는 데이터베이스 매핑을에 fluent API를 사용 합니다. 그러나 대부분의 서식이, 유효성 검사 및 특성을 사용 하 여 수행할 수 있는 매핑 규칙을 지정 하려면 fluent API를 사용할 수도 있습니다. 와 같은 일부 특성 `MinimumLength` fluent API와 함께 적용할 수 없습니다. 앞서 언급 했 듯이 `MinimumLength` 스키마를 변경 되지 않습니다 클라이언트 및 서버 쪽 유효성 검사 규칙에만 적용 됩니다.

일부 개발자 들은 보관할 수 있는 엔터티 클래스 "정상". 있도록 단독으로 fluent API를 사용 하는 것을 선호합니다 원하는 많고만 fluent API를 사용 하 여 수행할 수 있는 몇 가지 사용자 지정 특성 및 fluent API를 혼합할 수 있지만 권장은이 두 가지 방법 중 하나를 선택 하 고 가능한 한를 일관 되 게 사용 하 여 일반적입니다. 둘 다를 사용 하는 경우에 충돌이 있을 때마다 Fluent API 재정의 특성 note 합니다.

Fluent API와 비교 하는 특성에 대 한 자세한 내용은 참조 [구성 방법이](https://ef.readthedocs.io/en/latest/modeling/index.html#methods-of-configuration)합니다.

## <a name="entity-diagram-showing-relationships"></a>관계를 보여 주는 엔터티 다이어그램

다음 그림에는 완성된 된 School 모델에 대 한 Entity Framework 파워 도구 만들기 다이어그램을 보여줍니다.

![엔터티 다이어그램](complex-data-model/_static/diagram.png)

일 대 다 관계 선이 외에도 (1로 \*), Instructor와 OfficeAssignment 엔터티 및 0-또는--다 관계 선 사이 0 또는 1을 한 관계 선을 (1 0..1)를 여기서 볼 수 (0..1으로 *) 강사 및 부서 엔터티 사이입니다.

## <a name="seed-the-database-with-test-data"></a>테스트 데이터를 사용 하 여 데이터베이스를 시드하십시오

코드는 *Data/DbInitializer.cs* 만든 새 엔터티에 대 한 시드 데이터를 제공 하기 위해 다음 코드가 포함 된 파일입니다.

[!code-csharp[주](intro/samples/cu/Data/DbInitializer.cs?name=snippet_Final)]

첫 번째 자습서에서 본 것 처럼이 코드의 대부분 단순히 새 엔터티 개체를 만들고 테스트를 위해 필요에 따라 속성에 샘플 데이터를 로드 합니다. 다 대 다 관계의 처리 방법을 확인: 코드에서 엔터티를 만들어 관계를 만드는 `Enrollments` 및 `CourseAssignment` 엔터티 집합을 조인 합니다.

## <a name="add-a-migration"></a>추가 마이그레이션

변경 내용을 저장 하 고 프로젝트를 빌드하십시오. 그런 다음 프로젝트 폴더에서 명령 창을 열고 하 고 입력의 `migrations add` 명령 (수행 하지 않도록 데이터베이스 업데이트 명령을 아직):

```console
dotnet ef migrations add ComplexDataModel
```

가능한 데이터 손실에 대 한 경고가 발생합니다.

```text
Build succeeded.
    0 Warning(s)
    0 Error(s)

Time Elapsed 00:00:11.58
An operation was scaffolded that may result in the loss of data. Please review the migration for accuracy.
Done. To undo this action, use 'ef migrations remove'
```

실행 하려는 경우는 `database update` 이 시점에서 명령 (그렇게 하지 마십시오 아직), 다음과 같은 오류가 발생 합니다.

> ALTER TABLE 문을 충돌 FOREIGN KEY 제약 조건 "FK_dbo 합니다. Course_dbo 합니다. Department_DepartmentID "로 설정 합니다. 데이터베이스 "ContosoUniversity", "dbo 테이블에에서 충돌이 발생 했습니다. 부서 ", 열 'DepartmentID'입니다.

기존 데이터의 마이그레이션을 실행할 때 외래 키 제약 조건을 충족 하기 위해 데이터베이스에 스텁 데이터를 삽입 해야 합니다. 생성된 된 코드에는 `Up` 메서드 Course 테이블에 nullable이 아닌 DepartmentID 외래 키를 추가 합니다. 있는 경우 이미 행은 Course 테이블에는 코드를 실행 하는 경우는 `AddColumn` SQL Server null 일 수 없는 열에 저장할 값을 알 수 없는 때문에 작업이 실패 합니다. 이 자습서에 대 한 새 데이터베이스에 마이그레이션을 실행 합니다 하지만 프로덕션 응용 프로그램에서 다음과 같이 작업을 수행 하는 방법의 예를 나타내도록 기존 데이터를 처리 하는 마이그레이션 수행 해야 합니다.

이 마이그레이션 작업 기존 데이터를 새 열에 기본값을 제공 하는 코드를 변경 해야 하 고 스텁 부서를 만들 수 있도록 라는 "Temp" 기본 부서 역할을 합니다. 결과적으로, 코스 행을 기존는 모두 관련이 있을 후 "Temp" 부서는 `Up` 메서드가 실행 됩니다.

* 열기는 *<timestamp>_ComplexDataModel.cs* 파일입니다. 

* DepartmentID 열 Course 테이블을 추가 하는 코드 줄을 주석 처리 합니다.

  [!code-csharp[주](intro/samples/cu/Migrations/20170215234014_ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

* Department 테이블을 만드는 코드를 뒤 다음 강조 표시 된 코드를 추가 합니다.

  [!code-csharp[주](intro/samples/cu/Migrations/20170215234014_ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=22-32)]

프로덕션 응용 프로그램에서 코드 또는 스크립트를 부서 행 추가 및 새 부서 행 코스 행 관련이 쓰듯이 합니다. "Temp" 부서 또는 Course.DepartmentID 열에 기본값을 다음 더 이상 해야 합니다.

변경 내용을 저장 하 고 프로젝트를 빌드하십시오.

## <a name="change-the-connection-string-and-update-the-database"></a>연결 문자열을 변경 하 고 데이터베이스를 업데이트 합니다.

이제 새 코드에 있어야는 `DbInitializer` 빈 데이터베이스를 새 엔터티에 대 한 시드 데이터를 추가 하는 클래스입니다. 비어 있는 새 데이터베이스를 만드는 하려면 연결 문자열에 데이터베이스의 이름을 변경 *appsettings.json* ContosoUniversity3 또는 사용 중인 컴퓨터에서 사용 하지 않은 몇 가지 다른 이름으로 합니다.

```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=(localdb)\\mssqllocaldb;Database=ContosoUniversity3;Trusted_Connection=True;MultipleActiveResultSets=true"
  },
```

에 대 한 변경 내용을 저장 *appsettings.json*합니다.

> [!NOTE]
> 데이터베이스 이름을 변경 하는 대신, 데이터베이스를 삭제할 수 있습니다. 사용 하 여 **SQL Server 개체 탐색기** (SSOX) 또는 `database drop` CLI 명령:
> ```console
> dotnet ef database drop
> ```

데이터베이스 이름을 변경 했거나 데이터베이스를 삭제 한 후 실행 하는 `database update` 마이그레이션을 실행 하려면 명령 창에 명령 합니다.

```console
dotnet ef database update
```

실행 하는 앱에는 `DbInitializer.Initialize` 메서드를 실행 하 고 새 데이터베이스를 채웁니다.

앞에서 수행한 것 처럼 데이터베이스 SSOX에서 열기 및 확장의 **테이블** 노드에서 모든 테이블 생성을 볼 수 있습니다. (이전 시점에서 열 SSOX 여전히 있으면 새로 고침 단추 클릭 합니다.)

![SSOX에서 테이블](complex-data-model/_static/ssox-tables.png)

데이터베이스를 시드하 이니셜라이저 코드를 트리거할 응용 프로그램을 실행 합니다.

마우스 오른쪽 단추로 클릭는 **CourseAssignment** 선택한 테이블 **데이터 보기** 에 데이터가 있는지 확인 합니다.

![SSOX에서 CourseAssignment 데이터](complex-data-model/_static/ssox-ci-data.png)

## <a name="summary"></a>요약

이제 좀 더 복잡 한 데이터 모델 및 해당 데이터베이스를 되었습니다. 다음 자습서 관련된 데이터에 액세스 하는 방법에 대해 자세히 배웁니다.

>[!div class="step-by-step"]
[이전](migrations.md)
[다음](read-related-data.md)  

