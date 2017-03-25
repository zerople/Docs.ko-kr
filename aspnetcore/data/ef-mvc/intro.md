---
title: "ASP.NET Core MVC 자습서 1 / 10-Entity Framework 코어 | Microsoft 문서"
author: tdykstra
description: 
keywords: "ASP.NET Core, Entity Framework 코어, 자습서"
ms.author: tdykstra
manager: wpickett
ms.date: 03/15/2017
ms.topic: article
ms.assetid: b67c3d4a-f2bf-4132-a48b-4b0d599d7981
ms.technology: aspnet
ms.prod: asp.net-core
uid: data/ef-mvc/intro
translationtype: Machine Translation
ms.sourcegitcommit: 115a74c97de6052ec707ee164641f9c41224b9b3
ms.openlocfilehash: 172032f9a3433f49da8a2898539be81b0ce06176
ms.lasthandoff: 03/23/2017

---
# <a name="getting-started-with-aspnet-core-mvc-and-entity-framework-core-using-visual-studio-1-of-10"></a>ASP.NET Core MVC 및 Visual Studio (1 / 10)를 사용 하 여 Entity Framework 핵심 시작

여 [Tom Dykstra](https://github.com/tdykstra) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)

Contoso University 샘플 웹 응용 프로그램에는 Entity Framework 코어 1.1 및 Visual Studio 2017를 사용 하 여 ASP.NET 핵심 1.1 MVC 웹 응용 프로그램을 만드는 방법을 보여 줍니다.

샘플 응용 프로그램은 가상의 Contoso University에 대 한 웹 사이트. 학생 허용, 강좌 만들기 및 강사 할당 등의 기능을 포함합니다. 처음부터 Contoso University 샘플 응용 프로그램을 빌드하는 방법을 설명 하는 자습서 시리즈의 첫 번째 항목입니다.

[완성된 된 응용 프로그램을 보거나 다운로드 합니다.](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

EF 코어 1.1 EF의 최신 버전 이지만 EF의 기능을 모두 아직 없는 6.x 합니다. EF 중에서 선택 하는 방법에 대 한 내용은 6.x에서 EF 코어 1.0 참조 [EF 코어 vs. EF6.x](https://docs.microsoft.com/ef/efcore-and-ef6/). EF를 선택 하면 6.x, 참조 [이 자습서 시리즈의 이전 버전](https://docs.microsoft.com/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application)합니다.

> [!NOTE]
> 이 자습서의 Visual Studio 2015 버전에 대 한 참조는 [VS 2015 버전의 ASP.NET 핵심 설명서 PDF 형식으로](https://github.com/aspnet/Docs/blob/master/aspnetcore/common/_static/aspnet-core-project-json.pdf)합니다.

## <a name="prerequisites"></a>필수 조건

[Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/install-visual-studio) 와 **ASP.NET 및 웹 개발** 및 **.NET Core 크로스 플랫폼 개발 작업** 설치 합니다.

## <a name="troubleshooting"></a>문제 해결

솔루션에 코드를 비교 하 여 일반적으로 찾을 수를 해결할 수 없는 문제가 발생 하는 경우는 [완성 된 프로젝트](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)합니다. 일반적인 오류 및 해결 하는 방법의 목록이 참조 [시리즈의 마지막 자습서의 문제 해결 섹션](advanced.md#common-errors)합니다. 에 대 한 StackOverflow.com에 질문을 게시할 수 필요한 있습니다 찾을 수 없는 경우 [ASP.NET 핵심](http://stackoverflow.com/questions/tagged/asp.net-core) 또는 [EF 코어](http://stackoverflow.com/questions/tagged/entity-framework-core)합니다.

> [!TIP] 
> 이전 자습서에서 수행 되는 기반으로 하는 일련의 10 자습서입니다.  각 자습서 완료 후 프로젝트의 복사본을 저장 하는 것이 좋습니다.  다음 문제가 발생 하면 전체 시리즈의 시작 부분으로 다시 이동 하는 대신 이전 자습서에서을 통해 시작할 수 있습니다.

## <a name="the-contoso-university-web-application"></a>Contoso University 웹 응용 프로그램

이러한 자습서를 작성 하는 응용 프로그램은 간단한 대학 웹 사이트.

사용자가 볼 수 있으며, 학생, 과정 및 강사 정보 업데이트. 만들게 스크린의 몇 가지 있습니다.

![학생 인덱스 페이지](intro/_static/students-index.png)

![학생 편집 페이지](intro/_static/student-edit.png)

이 자습서는 Entity Framework를 사용 하는 방법에 주로 초점을 맞출 수 있도록이 사이트의 UI 스타일은 기본 제공 템플릿에 의해 생성 되는 기능에 가깝게 유지 되었습니다.

## <a name="create-an-aspnet-core-mvc-web-application"></a>ASP.NET Core MVC 웹 응용 프로그램 만들기

Visual Studio를 열고 "ContosoUniversity" 라는 하는 새 ASP.NET 핵심 C# 웹 프로젝트를 만듭니다.

* **파일** 메뉴 **새로 만들기 > 프로젝트**합니다.

* 왼쪽된 창에서 선택 **템플릿 > Visual C# > 웹**합니다.

* 선택 된 **ASP.NET 핵심 웹 응용 프로그램 (.NET Core)** 프로젝트 템플릿.

* 입력 **ContosoUniversity** 이름과 클릭으로 **확인**합니다.

  ![새 프로젝트 대화 상자](intro/_static/new-project.png)

* 대기는 **새 ASP.NET 핵심 웹 응용 프로그램 (.NET Core)** 대화 상자를 표시

* 선택 **ASP.NET 1.1** 및 **웹 응용 프로그램** 템플릿.

* 확인 **인증** 로 설정 된 **인증 안 함**합니다.

* **확인**을 클릭합니다.

  ![새 ASP.NET 프로젝트 대화 상자](intro/_static/new-aspnet.png)

## <a name="set-up-the-site-style"></a>사이트 스타일 설정

약간만 변경 하면 사이트 메뉴, 레이아웃 및 홈 페이지를 설정 합니다.

열기 *Views/Shared/_Layout.cshtml* 를 다음과 같이 변경 합니다.

* "Contoso University"를 "ContosoUniversity"의 각 항목을 변경 합니다. 다음과 같은 세 개의 항목이 없습니다.

* 메뉴 항목에 대 한 추가 **학생**, **과정**, **강사**, 및 **부서**, 및 삭제는 **연락처** 메뉴 항목입니다.

변경 내용은 강조 표시 됩니다.

[!code-html[](intro/samples/cu/Views/Shared/_Layout.cshtml?highlight=7,31,37-40,49)]

*Views/Home/Index.cshtml*,이 응용 프로그램에 대 한 텍스트로 ASP.NET MVC에 대 한 텍스트를 대체 하는 다음 코드는 파일의 내용을 바꿉니다.

[!code-html[](intro/samples/cu/Views/Home/Index.cshtml)]

CTRL + F5 키를 눌러 프로젝트를 실행 하거나 선택할 **디버그 > 디버깅 하지 않고 시작** 메뉴에서 합니다. 이 자습서에서 만드는 페이지에 대 한 탭이 포함 된 홈 페이지가 표시 됩니다.

![Contoso University 홈 페이지](intro/_static/home-page.png)

## <a name="entity-framework-core-nuget-packages"></a>Entity Framework 코어 NuGet 패키지

EF 핵심 지원에는 프로젝트를 추가 하려면 대상으로 할 데이터베이스 공급자를 설치 합니다. 이 자습서에서는 SQL Server 공급자를 설치 합니다.: [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/)합니다. 

```
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```
  
이 패키지와 해당 종속성 (`Microsoft.EntityFrameworkCore` 및 `Microsoft.EntityFrameworkCore.Relational`) EF에 대 한 런타임 지원을 제공 합니다. 도구 패키지를 추가 합니다의 뒷부분에 나오는 [마이그레이션을](migrations.md) 자습서입니다. 

## <a name="create-the-data-model"></a>데이터 모델 만들기

그런 다음 엔터티 클래스는 Contoso University 응용 프로그램을 만들어야 합니다. 다음 세 가지 엔터티부터 시작 합니다.

![과정 등록 학생 데이터 모델 다이어그램](intro/_static/data-model-diagram.png)

사이 일 대 다 관계가 `Student` 및 `Enrollment` 엔터티 간의 일 대 다 관계 이며 `Course` 및 `Enrollment` 엔터티. 즉, 학생 과정, 여러에 등록할 수 및 제공 하는 과정에 등록 하는 학생의 모든 수를 가질 수 있습니다.

다음 섹션에서는 이러한 엔터티 중 각각에 대 한 클래스를 만듭니다.

### <a name="the-student-entity"></a>학생 엔터티

![학생 엔터티 다이어그램](intro/_static/student-entity.png)

프로젝트 폴더에서 라는 폴더를 만듭니다 *모델*합니다.

에 *모델* 폴더에서 라는 클래스 파일을 만듭니다 *Student.cs* 템플릿 코드를 다음 코드로 바꿉니다.

[!code-csharp[주](intro/samples/cu/Models/Student.cs?name=snippet_Intro)]

`ID` 속성에는이 클래스에 해당 하는 데이터베이스 테이블의 기본 키 열 수 있습니다. 기본적으로 Entity Framework 해석 하는 라는 속성이 `ID` 또는 `classnameID` 기본 키로 합니다.

`Enrollments` 속성은 탐색 속성입니다. 탐색 속성에는이 엔터티와 관련 된 다른 엔터티와 보관 합니다. 이 경우에 `Enrollments` 속성은 `Student entity` 모두 보유는 `Enrollment` 엔터티는 관련 된 `Student` 엔터티. 즉, 데이터베이스에 지정 된 학생 행에 두 개의 관련 된 등록 행 (해당 StudentID 외래 키 열에 해당 학생의 기본 키 값을 포함 하는 행)는 `Student` 엔터티의 `Enrollments` 탐색 속성은이 두 포함 `Enrollment` 엔터티.

탐색 속성 (다 대 다 또는 일 대 다 관계)에서 같이 여러 엔터티를 보유할 수, 해당 형식은 항목 추가, 삭제 및 업데이트와 같은 될 수 있는 목록 이어야 합니다 `ICollection<T>`합니다.  지정할 수 있습니다 `ICollection<T>` 또는과 같은 형식 `List<T>` 또는 `HashSet<T>`합니다. 지정 하는 경우 `ICollection<T>`, EF 만듭니다는 `HashSet<T>` 기본적으로 컬렉션입니다.

### <a name="the-enrollment-entity"></a>등록 엔터티

![등록 엔터티 다이어그램](intro/_static/enrollment-entity.png)

에 *모델* 폴더를 만들 *Enrollment.cs* 기존 코드를 다음 코드로 바꿉니다.

[!code-csharp[주](intro/samples/cu/Models/Enrollment.cs?name=snippet_Intro)]

`EnrollmentID` 속성의 기본 키가 됩니다;이 엔터티 사용의 `classnameID` 대신 패턴 `ID` 에서 본 것 처럼 자체는 `Student` 엔터티. 일반적으로 패턴 중 하나를 선택 하는 데이터 모델 전체에서 사용 합니다. 여기서는 변형을 패턴 중 하나를 사용할 수 있는 보여 줍니다. 에 [자습서 뒷부분](inheritance.md), 어떻게 classname 없이 ID를 사용 하 여 쉽게 데이터 모델에서 상속을 구현 하 표시 됩니다.

`Grade` 속성은 한 `enum`합니다. 후 물음표는 `Grade` 형식 선언을 나타내는 `Grade` 속성은 null을 허용 합니다. Null이 등급은&0; 개 등급을 다릅니다-null 의미는 등급에 알지 또는 아직 할당 되지 않은 합니다.

`StudentID` 속성이 외래 키를이 고 해당 탐색 속성은 `Student`합니다. `Enrollment` 엔터티는 하 나와 연결 `Student` 엔터티를 단일 속성을 저장할 수 있으므로 `Student` 엔터티 (달리는 `Student.Enrollments` 탐색 속성 했 듯이, 여러을 보유할 수 있는 `Enrollment` 엔터티).

`CourseID` 속성이 외래 키를이 고 해당 탐색 속성은 `Course`합니다. `Enrollment` 엔터티는 하 나와 연결 `Course` 엔터티.

Entity Framework 라고 하는 경우 외래 키 속성으로 속성을 해석 `<navigation property name><primary key property name>` (예를 들어 `StudentID` 에 대 한는 `Student` 이후 탐색 속성은 `Student` 엔터티의 기본 키가 `ID`). 외래 키 속성은 단순히 또한 이름은 수 `<primary key property name>` (예를 들어 `CourseID` 이후는 `Course` 엔터티의 기본 키가 `CourseID`).

### <a name="the-course-entity"></a>Course 엔터티

![Course 엔터티 다이어그램](intro/_static/course-entity.png)

에 *모델* 폴더를 만들 *Course.cs* 기존 코드를 다음 코드로 바꿉니다.

[!code-csharp[주](intro/samples/cu/Models/Course.cs?name=snippet_Intro)]

`Enrollments` 속성은 탐색 속성입니다. A `Course` 원하는 수의 엔터티를 관련 될 수 있습니다 `Enrollment` 엔터티.

에 대 한 내용은 라고는 `DatabaseGenerated` 특성에 [자습서 뒷부분](complex-data-model.md) 이 시리즈의 합니다. 기본적으로,이 특성 사용 코스는 대신 생성 하는 데이터베이스에 대 한 기본 키를 입력할 수 있습니다.

## <a name="create-the-database-context"></a>데이터베이스 컨텍스트 만들기

지정된 된 데이터 모델에 대 한 Entity Framework 기능을 조정 하는 기본 클래스는 데이터베이스 컨텍스트 클래스입니다. 이 클래스에서 파생 시켜 만들는 `System.Data.Entity.DbContext` 클래스입니다. 코드에는 엔터티 데이터 모델에 포함 된 지정 합니다. 특정 엔터티 프레임 워크 동작을 사용자 지정할 수 있습니다. 이 프로젝트의 클래스 이름은 `SchoolContext`합니다.

프로젝트 폴더에서 라는 폴더를 만듭니다 *데이터*합니다.

에 *데이터* 폴더에 라는 새 클래스 파일을 만들 *SchoolContext.cs*, 템플릿 코드를 다음 코드로 바꿉니다.

[!code-csharp[주](intro/samples/cu/Data/SchoolContext.cs?name=snippet_Intro)]

이 코드에서는 한 `DbSet` 각 엔터티 집합에 대 한 속성입니다. Entity Framework 용어에서 엔터티 집합을 일반적으로 데이터베이스 테이블에 해당 하 고 엔터티 테이블의 행에 해당 합니다.

생략 하면 수의 `DbSet<Enrollment>` 및 `DbSet<Course>` 문과 것은 동일 하 게 작동 합니다. Entity Framework 때문에 암시적으로 포함 됩니다는 `Student` 엔터티 참조는 `Enrollment` 엔터티 및 `Enrollment` 엔터티 참조는 `Course` 엔터티.

데이터베이스를 만들면 EF 만듭니다 동일 이름을 가진 테이블의 `DbSet` 속성 이름입니다. 컬렉션에 대 한 속성 이름에는 일반적으로 또는 테이블 이름을 복수화 여부에 대 한 동의 안 함 (학생 들에 게 아니라 학생), 복수형 하지만 개발자입니다. 이 자습서에 대 한 DbContext 단 수 테이블 이름을 지정 하 여 기본 동작을 재정의 합니다. 이렇게 하려면 마지막 DbSet 속성 뒤 다음 강조 표시 된 코드를 추가 합니다.

[!code-csharp[주](intro/samples/cu/Data/SchoolContext.cs?name=snippet_TableNames&highlight=16-21)]

## <a name="register-the-context-with-dependency-injection"></a>종속성 주입을 컨텍스트 등록

ASP.NET Core 구현 [종속성 주입](../../fundamentals/dependency-injection.md) 기본적으로 합니다. 서비스 (예: EF 데이터베이스 컨텍스트) 종속성 주입 응용 프로그램 시작 중에 등록 됩니다. 이러한 서비스 (예: MVC 컨트롤러)에 필요한 구성 요소를 생성자 매개 변수를 통해 이러한 서비스 제공 됩니다. 컨트롤러 생성자 되는 코드를 컨텍스트 인스턴스를이 자습서의 뒷부분에 표시 됩니다.

등록 하려면 `SchoolContext` 서비스를 열고 *Startup.cs*, 강조 표시 된 줄을 추가 하 고는 `ConfigureServices` 메서드.

[!code-csharp[주](intro/samples/cu/Startup.cs?name=snippet_SchoolContext&highlight=4-5)]

연결 문자열의 이름에는 메서드를 호출 하 여 컨텍스트에 전달 됩니다는 `DbContextOptionsBuilder` 개체입니다. 로컬 개발을 위해는 [ASP.NET 핵심 구성 시스템](../../fundamentals/configuration.md) 에서 연결 문자열을 읽고는 *appsettings.json* 파일입니다.

추가 `using` 에 대 한 문을 `ContosoUniversity.Data` 및 `Microsoft.EntityFrameworkCore` 네임 스페이스는 프로젝트를 빌드합니다.

[!code-csharp[주](intro/samples/cu/Startup.cs?name=snippet_Usings&highlight=1,4)]

열기는 *appsettings.json* 파일을 다음 예제에 표시 된 대로 연결 문자열을 추가 합니다.

[!code-json[](./intro/samples/cu/appsettings1.json?highlight=2-4)]

### <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

연결 문자열에는 SQL Server LocalDB 데이터베이스를 지정합니다. LocalDB는 SQL Server Express 데이터베이스 엔진의 경량 버전 및 프로덕션을 사용 하지 않는 응용 프로그램 개발을 위한 것입니다. LocalDB는 요청에서 시작 하 고 복잡 한 구성은 없으며 사용자 모드에서 실행 합니다. 기본적으로 LocalDB 만듭니다 *.mdf* 데이터베이스 파일에는 `C:/Users/<user>` 디렉터리입니다.

## <a name="add-code-to-initialize-the-database-with-test-data"></a>테스트 데이터를 사용 하 여 데이터베이스를 초기화 하는 코드를 추가 합니다.

Entity Framework에서 빈 데이터베이스를 만듭니다.  이 섹션에서는 테스트 데이터로 채우려면는 데이터베이스를 만든 후 호출 되는 메서드를 작성할 수 있습니다.

여기서 사용 하 여는 `EnsureCreated` 메서드를 자동으로 데이터베이스를 만듭니다. 에 [자습서 뒷부분](migrations.md) 삭제 하 고 데이터베이스를 다시 작성 하는 대신 데이터베이스 스키마를 변경 하려면 Code First 마이그레이션을 사용 하 여 모델 변경 내용을 처리 하는 방법을 확인할 수 있습니다.

에 *데이터* 폴더 라는 새 클래스 파일을 만듭니다 *DbInitializer.cs* 하 고 템플릿 코드 필요할 때 만들 데이터베이스를 다음 코드로 바꾸고 새 데이터베이스에 테스트 데이터를 로드 합니다.

[!code-csharp[주](intro/samples/cu/Data/DbInitializer.cs?name=snippet_Intro)]

코드는 모든 학생 들에 게는 데이터베이스는이 고 그렇지 않은 경우 것으로 간주 되어 데이터베이스 새로운 테스트 데이터로 시드할 수 해야 하는 경우를 확인 합니다.  배열에 테스트 데이터를 로드 하지 않고 `List<T>` 성능을 최적화 하는 컬렉션입니다.

*Startup.cs*, 수정는 `Configure` 메서드를 응용 프로그램 시작 시이 seed 메서드를 호출 하도록 합니다. 첫째, ASP.NET 종속성 주입 되 게 제공할 수 있도록 컨텍스트 메서드 시그니처를 추가 하면 `DbInitializer` 클래스입니다.

[!code-csharp[주](intro/samples/cu/Startup.cs?name=snippet_ConfigureSignature&highlight=1)]

그런 다음 호출 프로그램 `DbInitializer.Initialize` 메서드 끝에 `Configure` 메서드.

[!code-csharp[주](intro/samples/cu/Startup.cs?name=snippet_RouteAndSeed&highlight=8)]

이제 데이터베이스 응용 프로그램을 실행 하는 처음으로 생성 되며 테스트 데이터를 사용 하 여 시드할. 데이터 모델 변경 될 때마다 삭제 데이터베이스, seed 메서드를 사용 하 여 업데이트 하 고 동일한 방식으로 새 데이터베이스와 새로 시작 합니다. 이후의 자습서는 데이터 모델이 변경, 삭제를 다시 생성 하지 않고 때 데이터베이스를 수정 하는 방법을 알아보겠습니다.

## <a name="create-a-controller-and-views"></a>컨트롤러와 뷰 만들기

다음으로, Visual Studio에서 스 캐 폴딩 엔진을 사용 하 여 MVC 컨트롤러 및 뷰를 쿼리하고 데이터를 저장 하기 위해 EF 사용할 추가 합니다.

CRUD 작업 메서드 및 보기의 자동 만들기 스 캐 폴딩으로 알려져 있습니다. 스 캐 폴딩 스 캐 폴드 된 코드는 일반적으로 생성 된 코드를 수정 하지 않는 반면 사용자 고유의 요구 사항에 맞게 수정할 수 있는 시작 지점을 점에서 코드 생성와 다릅니다. 생성 된 코드를 사용자 지정 해야 할 경우 partial 클래스를 사용 하거나 항목이 변경 될 때 코드를 다시 생성 합니다.

* 마우스 오른쪽 단추로 클릭는 **컨트롤러** 폴더에 **솔루션 탐색기** 선택한 **추가 > 새 스 캐 폴드 항목**합니다.

* 에 **MVC 종속성 추가** 대화 상자에서 **최소한의 종속성**를 선택 하 고 **추가**합니다.

  ![종속성 추가](intro/_static/add-depend.png)

  패키지를 포함 하 여 디자인 타임 EF 기능으로는 컨트롤러를 스 캐 폴딩 하는 데 필요한 종속성을 추가 하는 visual Studio (`Microsoft.EntityFrameworkCore.Design`). 기존 데이터베이스에서 DbContext 스 캐 폴딩에 필요한 패키지에 포함 됩니다 (`Microsoft.EntityFrameworkCore.SqlServer.Design`). A *ScaffoldingReadMe.txt* 삭제할 수 있는 파일이 생성 됩니다.

* 다시 한 번 단추로 클릭 하 고는 **컨트롤러** 폴더에 **솔루션 탐색기** 선택 하 고 **추가 > 새 스 캐 폴드 항목**.

* 에 **스 캐 폴드 추가** 대화 상자:

  * 선택 **MVC 컨트롤러 (뷰 포함), Entity Framework를 사용 하 여**합니다.

  * **추가**를 클릭합니다.

* 에 **컨트롤러 추가** 대화 상자:

  * **모델 클래스** 선택 **학생**합니다.

  * **데이터 컨텍스트 클래스** 선택 **SchoolContext**합니다.

  * 기본값을 적용 **StudentsController** 이름으로 합니다.

  * **추가**를 클릭합니다.

  ![스 캐 폴드 학생](intro/_static/scaffold-student.png)

  클릭할 때 **추가**, Visual Studio 스 캐 폴딩 엔진 만듭니다는 *StudentsController.cs* 파일 및 다양 한 보기 (*.cshtml* 파일) 컨트롤러와 함께 작동 하 합니다.

(스 캐 폴딩 엔진 만들 수도 데이터베이스 컨텍스트를 수동으로 만들지 않는 경우이 자습서를 위해 이전과 마찬가지로 먼저 합니다. 새 컨텍스트 클래스를 지정할 수는 **컨트롤러 추가** 상자 오른쪽에 있는 더하기 기호를 클릭 하 여 **데이터 컨텍스트 클래스**합니다.  그런 다음 visual Studio를 만듭니다 프로그램 `DbContext` 클래스 뿐만 아니라 컨트롤러와 뷰.)

컨트롤러를 사용 한다고 보면는 `SchoolContext` 를 생성자 매개 변수로 합니다.

[!code-csharp[주](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Context&highlight=5,7,9)]

ASP.NET 종속성 주입의 인스턴스를 전달 해 줄 것 `SchoolContext` 컨트롤러에 있습니다. 구성 된 *Startup.cs* 이전 파일입니다.

컨트롤러를 포함 한 `Index` 데이터베이스의 모든 학생 들에 게 표시 하는 작업 메서드에 있습니다. 메서드는 학생 들에 게 엔터티 참조 하 여 집합에서 학생 들의 목록을 가져옵니다는 `Students` 데이터베이스 컨텍스트 인스턴스의 속성:

[!code-csharp[주](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex&highlight=3)]

이 자습서 뒷부분에서이 코드에서는 비동기 프로그래밍 요소에 대해 알아봅니다.

*Views/Students/Index.cshtml* 보기 테이블에이 목록에 표시 됩니다.

[!code-html[](intro/samples/cu/Views/Students/Index1.cshtml)]

CTRL + F5 키를 눌러 프로젝트를 실행 하거나 선택할 **디버그 > 디버깅 하지 않고 시작** 메뉴에서 합니다.

테스트 데이터를 보려면 학생 탭을 클릭 하는 `DbInitializer.Initialize` 메서드를 삽입 합니다. 에 따라 어떻게 좁은 브라우저 창, 볼 수는 `Student` 탭 페이지의 맨 위에 링크 링크를 보려면 오른쪽 위 모서리에 있는 탐색 아이콘을 클릭 해야 합니다.

![좁은 Contoso University 홈 페이지](intro/_static/home-page-narrow.png)

![학생 인덱스 페이지](intro/_static/students-index.png)

## <a name="view-the-database"></a>데이터베이스 보기

응용 프로그램을 시작 했을 때의 `DbInitializer.Initialize` 메서드 호출 `EnsureCreated`합니다. EF 본 데이터베이스가 없습니다. 했습니다 하 고 없으므로 생성 한 다음 나머지는 `Initialize` 메서드 코드는 데이터베이스를 데이터로 채워집니다. 사용할 수 있습니다 **SQL Server 개체 탐색기** (SSOX) Visual Studio에서 데이터베이스를 볼 수 있습니다.

브라우저를 닫습니다.

SSOX 창이 열려 있지 않으면 이미에서 선택 된 **보기** Visual Studio의 메뉴입니다.

SSOX, 클릭 **(localdb) \MSSQLLocalDB > 데이터베이스**, 다음의 연결 문자열에 있는 데이터베이스 이름에 대 한 항목을 클릭 하 고 프로그램 *appsettings.json* 파일입니다.

확장 된 **테이블** 노드에서 데이터베이스에서 테이블을 볼 수 있습니다.

![SSOX에서 테이블](intro/_static/ssox-tables.png)

마우스 오른쪽 단추로 클릭는 **학생** 테이블을 클릭 **데이터 보기** 생성 된 열 및 테이블에 삽입 된 행을 볼 수 있습니다.

![SSOX에서 student 테이블](intro/_static/ssox-student-table.png)

*.mdf* 및 *.ldf* 데이터베이스 파일은는 *C:Users<yourusername>* 폴더입니다.

호출 하는 것 때문에 `EnsureCreated` 앱 시작 시 실행 되는 이니셜라이저 메서드에서 만들 수 있습니다 이제에 대 한 변경의 `Student class`, 데이터베이스를 삭제, 응용 프로그램을 다시 실행 및 데이터베이스는 자동으로 변경 내용을 일치 하도록 다시 만들 수 있습니다. 예를 들어, 추가 하는 경우는 `EmailAddress` 속성을는 `Student` 클래스를 보면 새 `EmailAddress` 다시 만들어진된 테이블의 열입니다.

## <a name="conventions"></a>규칙

규칙, 또는 Entity Framework를 사용 하면 가정을 사용 되기 때문에 전체 데이터베이스를 만들 수 있으려면 Entity Framework에 대 한 순서 대로 작성 해야 하는 코드의 양을 최소화 됩니다.

* 이름을 `DbSet` 속성은 테이블 이름으로 사용 됩니다. 참조 하지 엔터티의 `DbSet` 속성, 엔터티 클래스 이름이 테이블 이름으로 사용 됩니다.

* 엔터티 속성 이름은 열 이름에 사용 됩니다.

* ID 또는 classnameID 명명 된 엔터티 속성은 기본 키 속성으로 인식 됩니다.

* 라고 하는 경우 속성이 외래 키 속성으로 해석 됩니다 *<navigation property name> <primary key property name>* (예를 들어 `StudentID` 에 대 한는 `Student` 이후 탐색 속성은 `Student` 엔터티의 기본 키가 `ID`). 외래 키 속성은 단순히 또한 이름은 수 *<primary key property name>* (예를 들어 `EnrollmentID` 이후는 `Enrollment` 엔터티의 기본 키가 `EnrollmentID`).

기본 동작을 재정의할 수 있습니다. 예를 들어이 자습서의 앞부분에서 보았던 것 처럼 테이블 이름에 지정할 수 명시적으로. 열 이름을 설정할 수 있으며에서 볼 수 있듯이 기본 키 또는 외래 키 속성을 설정 하 고는 [자습서 뒷부분](complex-data-model.md) 이 시리즈의 합니다.

## <a name="asynchronous-code"></a>비동기 코드

비동기 프로그래밍은 ASP.NET 코어 및 EF 코어에 대 한 기본 모드입니다.

웹 서버 제한 된 수의 스레드를 사용할 수 있으며 높은 로드 상황에서 사용 가능한 스레드 모두에서 사용 중일 수 있습니다. 이 경우 서버는 스레드를 해제 될 때까지 새 요청을 처리할 수 없습니다. 아닌 실제로 작업 하는 동안 모든 i/o가 완료 기다리는 때문에 많은 스레드가 동기 코드와 함께 하느라 정체 될 수 있습니다. 비동기 코드와 함께 I/O가 완료에 대 한 대기 하는 프로세스 스레드에서 다른 요청을 처리 하기 위해 사용 하 여 서버에 대해 해제 됩니다. 결과적으로, 비동기 코드 보다 효율적으로 사용 되며 지연 없이 더 많은 트래픽을 처리할 서버가 설정 되어 서버 리소스를 사용 합니다.

비동기 코드는 런타임 시 적은 양의 오버 헤드를 도입 하지만 잠재적인 성능 향상은 상당한는 성능에 미치는 영향은 매우 적습니다, 동안 트래픽이 높은 상황을 트래픽이 적은 경우.

다음 코드에서는 `async` 키워드를 `Task<T>` 값을 반환 `await` 키워드를 및 `ToListAsync` 메서드를 비동기적으로 실행 하는 코드를 확인 합니다.

[!code-csharp[주](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex)]

* `async` 키워드가 있으면 컴파일러 메서드 본문의 부분에 대 한 콜백을 생성 하 고 자동으로 만들 수는 `Task<IActionResult>` 반환 되는 개체입니다.

* 반환 형식은 `Task<IActionResult>` 종류의 결과 함께 진행 중인 작업을 나타내는 `IActionResult`합니다.

* `await` 키워드 하면 컴파일러에서 메서드를 두 부분으로 분할 합니다. 첫 번째 부분은 비동기적으로 시작 된 작업을 종료 합니다. 두 번째 부분은 작업이 완료 될 때 호출 되는 콜백 메서드에 배치 됩니다.

* `ToListAsync`비동기 버전의 `ToList` 확장 메서드.

Entity Framework를 사용 하는 비동기 코드를 작성 하는 알아야 할 몇 가지 있습니다.

* 쿼리 또는 데이터베이스에 보낼 명령을 발생 하는 문만 비동기적으로 실행 됩니다. 예를 들어 포함 된 `ToListAsync`, `SingleOrDefaultAsync`, 및 `SaveChangesAsync`합니다.  포함 되지 않습니다, 예를 들어 변경 하는 문에 `IQueryable`와 같은 `var students = context.Students.Where(s => s.LastName == "Davolio")`합니다.

* 스레드로부터 안전 하지 않은 EF 컨텍스트: 동시에 여러 작업을 수행 하지 마십시오. 비동기 EF 메서드를 호출 하는 경우 항상 사용은 `await` 키워드입니다.

* 페이징 등 사용 중인 비동기 코드의 성능 이점 활용 하기 위해, 라이브러리 패키지를 확인 하려는 경우, 또한 비동기 때문에 쿼리가 데이터베이스로 전송 하는 Entity Framework 메서드를 호출 하는 경우 메서드를 사용 합니다.

.NET의 비동기 프로그래밍에 대 한 자세한 내용은 참조 [비동기 개요](https://docs.microsoft.com/en-us/dotnet/articles/standard/async)합니다.

## <a name="summary"></a>요약

이제 Entity Framework 코어 및 SQL Server Express LocalDB를 사용 하 여 저장 하 고 데이터를 표시 하는 간단한 응용 프로그램을 만들었습니다. 다음 자습서에서는 기본 CRUD를 수행 하는 방법을 배우게 됩니다 (만들기, 읽기, 업데이트, 삭제) 작업입니다.

>[!div class="step-by-step"]
[다음](crud.md)  

