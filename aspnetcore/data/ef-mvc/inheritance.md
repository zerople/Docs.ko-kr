---
title: "ASP.NET Core MVC EF 코어 상속-9 / 10 인"
author: tdykstra
description: "이 자습서에서는 ASP.NET Core 응용 프로그램에서 Entity Framework Core를 사용 하 여 데이터 모델에서 상속을 구현 하는 방법을 보여줍니다."
keywords: "ASP.NET Core, Entity Framework Core 상속"
ms.author: tdykstra
manager: wpickett
ms.date: 03/15/2017
ms.topic: get-started-article
ms.assetid: 41dc0db7-6f17-453e-aba6-633430609c74
ms.technology: aspnet
ms.prod: asp.net-core
uid: data/ef-mvc/inheritance
ms.openlocfilehash: 3c86dea145d2d4dec10c77e008f511cfe67975f9
ms.sourcegitcommit: 4e84d8bf5f404bb77f3d41665cf7e7374fc39142
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/05/2017
---
# <a name="inheritance---ef-core-with-aspnet-core-mvc-tutorial-9-of-10"></a>상속-EF 코어 ASP.NET Core MVC 자습서 (9 / 10)

여 [Tom Dykstra](https://github.com/tdykstra) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)

Contoso 대학 샘플 웹 응용 프로그램에는 Entity Framework Core 및 Visual Studio를 사용 하 여 ASP.NET Core MVC 웹 응용 프로그램을 만드는 방법을 보여 줍니다. 자습서 시리즈에 대 한 정보를 참조 하십시오. [시리즈의 첫 번째 자습서](intro.md)합니다.

이전 자습서에서 동시성 예외를 처리합니다. 이 자습서에서는 데이터 모델에서 상속을 구현 하는 방법을 보여줍니다.

개체 지향 프로그래밍 코드 재사용을 용이 하 게 상속을 사용할 수 있습니다. 이 자습서에서는 변경 된 `Instructor` 및 `Student` 에서 파생 되도록 클래스는 `Person` 기본 클래스와 같은 속성이 포함 된 `LastName` 강사 및 학생 데이터 모두에 공통적인 합니다. 추가 하거나 모든 웹 페이지를 변경 하지 않습니다 되지만 일부 코드를 변경 합니다 및 이러한 변경 내용을 데이터베이스에 자동으로 반영 됩니다.

## <a name="options-for-mapping-inheritance-to-database-tables"></a>데이터베이스 테이블에 상속 매핑에 대 한 옵션

`Instructor` 및 `Student` 학교 데이터 모델의 클래스에는 동일한 여러 속성이 있습니다.

![학생과 강사 클래스](inheritance/_static/no-inheritance.png)

공유 하는 속성에 대 한 중복 코드를 제거 하려는 경우 다음과 같이 `Instructor` 및 `Student` 엔터티. 또는 이름 강사 또는 학생 반환 되었는지 여부를 고려 하지 않고 이름에 서식을 지정할 수 있는 서비스를 작성 하려는 합니다. 만들 수는 `Person` 확인 한 다음 기본 속성을 공유 하는 것만 포함 하는 클래스는 `Instructor` 및 `Student` 다음 그림에 나와 있는 것 처럼 해당 기본 클래스에서 상속 되는 클래스:

![사용자 클래스에서 파생 된 학생과 강사 클래스](inheritance/_static/inheritance.png)

여러 가지 방법으로 데이터베이스에이 상속 구조를 나타낼 수 있습니다. 학생과 단일 테이블에서 강사에 대 한 정보를 포함 하는 사용자 테이블에 있을 수 있습니다. 모두 (성, 이름) 하려면 [일부] 학생 (EnrollmentDate)에 일부 강사 (HireDate)에 적용할 수의 일부 열입니다. 일반적으로 판별자 열을 각 행이 나타내는 유형을 지정 해야 합니다. 예를 들어 판별자 열 학생용 강사 및 "학생"에 대 한 "강사"를 할 수 있습니다.

![한 계층에 테이블 예제](inheritance/_static/tph.png)

이 패턴의 단일 데이터베이스 테이블에서 엔터티 상속 구조를 생성 한 계층에 테이블 (TPH) 상속을 라고 합니다.

대신 좀 더 상속 구조 처럼 보이도록 하는 데이터베이스입니다. 예를 들어 Person 테이블의 이름 필드에만 있고 날짜 필드와 함께 별도 강사 및 학생 테이블 수 있습니다.

![형식당 하나의 테이블 상속](inheritance/_static/tpt.png)

이 패턴의 각 엔터티 클래스에 대 한 데이터베이스 테이블을 만드는 형식 (TPT) 상속 당 테이블을 이라고 합니다.

또 다른 옵션을 개별 테이블에 모든 추상이 아닌 형식을 매핑하는 것입니다. 상속 된 속성을 포함 하는 클래스의 모든 속성을 해당 테이블의 열에 매핑합니다. 이 패턴에는 테이블 구체적 클래스 (TPC) 상속을 라고 합니다. 앞에서 보았듯이 사람, 학생, 및 강사 클래스에 대 한 TPC 상속을 구현한 경우 학생과 강사 테이블 유사할 것 다르지 전과 상속 구현 후입니다.

TPC 및 TPH 상속 패턴 TPT 패턴 복잡 한 조인 쿼리 될 수 있으므로 일반적으로 TPT 상속 패턴 보다 더 나은 성능을 제공 합니다.

이 자습서에서는 TPH 상속 구현 하는 방법을 설명 합니다. TPH는 Entity Framework Core 지만 상속 패턴입니다.  만들기가 수행 합니다는 `Person` 클래스, 변경 된 `Instructor` 및 `Student` 클래스에서 상속할 수 `Person`에 새 클래스 추가 `DbContext`, 마이그레이션 만들기 및 합니다.

> [!TIP] 
> 다음과 같은 변경 하기 전에 프로젝트 복사본을 저장 하는 것이 좋습니다.  다음 문제 및 다시 시작 하는 필요성을 실행 하는 경우 전체 계열의 시작 부분에 다시 고 있거나이 자습서에 대해 수행 하는 단계 순서를 반대로 하는 대신 저장 된 프로젝트에서 시작 하는 보다 쉽게 수 됩니다.

## <a name="create-the-person-class"></a>Person 클래스 만들기

모델 폴더에서 Person.cs 만들고 템플릿 코드를 다음 코드로 바꿉니다.

[!code-csharp[Main](intro/samples/cu/Models/Person.cs)]

## <a name="make-student-and-instructor-classes-inherit-from-person"></a>사용자 로부터 상속 학생과 강사 클래스

*Instructor.cs*Person 클래스에서 강사 클래스를 파생 하 고 키 및 이름 필드를 제거 합니다. 코드는 다음 예제와 같습니다.

[!code-csharp[Main](intro/samples/cu/Models/Instructor.cs?name=snippet_AfterInheritance&highlight=8)]

변경 내용을 *Student.cs*합니다.

[!code-csharp[Main](intro/samples/cu/Models/Student.cs?name=snippet_AfterInheritance&highlight=8)]

## <a name="add-the-person-entity-type-to-the-data-model"></a>Person 엔터티 형식을 데이터 모델에 추가

Person 엔터티 형식을 추가 *SchoolContext.cs*합니다. 새 줄이 강조 표시 됩니다.

[!code-csharp[Main](intro/samples/cu/Data/SchoolContext.cs?name=snippet_AfterInheritance&highlight=19,30)]

Entity Framework 계층당 하나의 테이블 상속을 구성 하는 데 필요한 것입니다. 데이터베이스 업데이트 될 때, 볼 수 있듯이 학생과 강사 테이블 대신 개인 테이블은입니다.

## <a name="create-and-customize-migration-code"></a>만들기 및 마이그레이션 코드를 사용자 지정

변경 내용을 저장 하 고 프로젝트를 빌드하십시오. 그런 다음 프로젝트 폴더에 명령 창을 열고 하 고 다음 명령을 입력 합니다.

```console
dotnet ef migrations add Inheritance
```

실행 하지는 `database update` 아직 명령입니다. 해당 명령의 손실 된 데이터를 강사 테이블을 삭제 하 고 사용자에 게 Student 테이블 이름 바꾸기 때문에 발생 합니다. 기존 데이터를 유지 하려면 사용자 지정 코드를 제공 해야 합니다.

열기 *마이그레이션 /\<타임 스탬프 > _Inheritance.cs* 바꾸고는 `Up` 메서드를 다음 코드로:

[!code-csharp[Main](intro/samples/cu/Migrations/20170216215525_Inheritance.cs?name=snippet_Up)]

이 코드는 다음과 같은 데이터베이스 작업 업데이트를 처리할 수 있습니다:

* Foreign key 제약 조건 및 학생 테이블 인덱스를 제거 합니다.

* 사람으로 강사 테이블 이름을 바꾸고 학생 데이터를 저장 하는 데 필요한 변경:

* 학생용 nullable EnrollmentDate를 추가합니다.

* 학생 또는 강사에 대 한 행이 있는지 여부를 나타내기 위해 판별자 열을 추가 합니다.

* 학생 행 고용 날짜 되지 않았으므로 HireDate null을 허용 하면 있습니다.

* 학생을 가리키는 외래 키를 업데이트 하는 임시 필드를 추가 합니다. 학생 Person 테이블에 복사 하는 경우 새 기본 키 값을 볼 수 있습니다.

* Person 테이블에 학생 테이블에서 데이터를 복사 합니다. 이렇게 하면 새 기본 키 값도 할당에 대해 학습 합니다.

* 학생을 가리키는 외래 키 값을 수정 합니다.

* Foreign key 제약 조건 및 인덱스를 이제으로 지정 하기 Person 테이블을 다시 만듭니다.

(기본 키 유형으로 정수 대신 GUID를 사용 했다면, 학생 기본 키 값을 변경할 필요가 없게 및 이러한 단계 중 일부 수 누락 되었습니다.)

실행 된 `database update` 명령:

```console
dotnet ef database update
```

(프로덕션 시스템에서 해당 변경 내용을 확인 합니다는 `Down` 메서드는 이전 데이터베이스 버전으로 돌아가려면 사용 해야 하는 경우에서. 이 자습서를 사용 하지 않습니다는 `Down` 메서드.)

> [!NOTE] 
> 기존 데이터를 포함 하는 데이터베이스의 스키마를 변경 하는 경우 다른 오류를 얻을 수는 것이 불가능 합니다. 마이그레이션 오류를 해결할 수 없는 발생 하는 경우 연결 문자열에 데이터베이스 이름을 변경 하거나 데이터베이스를 삭제 합니다. 새 데이터베이스와 함께 마이그레이션할 데이터가 없는 및 update-database 명령을 작업이 오류 없이 완료 가능성이 높습니다. 데이터베이스를 삭제 하려면 SSOX 사용 하거나 실행 하는 `database drop` CLI 명령입니다.

## <a name="test-with-inheritance-implemented"></a>구현 상속으로 테스트

사이트를 실행 하 고 다양 한 페이지를 시도 하십시오. 모든 항목이 작동 하기 전과 동일 합니다.

**SQL Server 개체 탐색기**, 확장 **데이터 연결/SchoolContext** 차례로 **테이블**, 학생과 강사 테이블은 대체 하 여 참조 하 고는 사용자 테이블입니다. Person 테이블 디자이너를 열고 모두 사용은 Student 및 강사 테이블에 포함 되도록 열을 참조 하십시오.

![SSOX에 person 테이블](inheritance/_static/ssox-person-table.png)

Person 테이블을 마우스 오른쪽 단추로 누른 **테이블 데이터 표시** 판별자 열을 표시 합니다.

![SSOX-테이블 데이터에에서 개인 테이블](inheritance/_static/ssox-person-data.png)

## <a name="summary"></a>요약

에 대 한 계층당 하나의 테이블 상속을 구현한는 `Person`, `Student`, 및 `Instructor` 클래스입니다. Entity Framework Core의 상속에 대 한 자세한 내용은 참조 [상속](https://docs.microsoft.com/ef/core/modeling/inheritance)합니다. 다음 자습서에서는 다양 한 고급 비교적 Entity Framework 시나리오를 처리 하는 방법을 볼 수 있습니다.

>[!div class="step-by-step"]
[이전](concurrency.md)
[다음](advanced.md)  
