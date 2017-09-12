---
title: "ASP.NET Core MVC Entity Framework 코어 자습서 1 / 10 인"
author: tdykstra
description: 
keywords: "ASP.NET Core, Entity Framework Core 자습서"
ms.author: tdykstra
manager: wpickett
ms.date: 03/15/2017
ms.topic: get-started-article
ms.assetid: b67c3d4a-f2bf-4132-a48b-4b0d599d7981
ms.technology: aspnet
ms.prod: asp.net-core
uid: data/ef-mvc/intro
ms.openlocfilehash: 949733119b4e3a4b8716f2bcc1f631949d5049bc
ms.sourcegitcommit: 9cdbfd0d670d70b9c354216aabee260c52dad5ee
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/12/2017
---
# <a name="getting-started-with-aspnet-core-mvc-and-entity-framework-core-using-visual-studio-1-of-10"></a>Visual Studio (1 / 10)을 사용 하 여 Entity Framework Core 및 ASP.NET Core MVC 시작

여 [Tom Dykstra](https://github.com/tdykstra) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)

Contoso 대학 샘플 웹 응용 프로그램에는 Entity Framework (EF) 코어 2.0 및 Visual Studio 2017을 사용 하 여 ASP.NET 코어 2.0 MVC 웹 응용 프로그램을 만드는 방법을 보여 줍니다.

샘플 응용 프로그램은 웹 사이트 가상 Contoso 대학입니다. 학생 진입, 과정 만들기 및 강사 할당 등의 기능을 포함합니다. 처음부터 Contoso 대학 샘플 응용 프로그램을 빌드하는 방법을 설명 하는 자습서 시리즈의 첫 번째입니다.

[다운로드 하거나 완성 된 응용 프로그램을 보고 합니다.](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

EF 코어 2.0 EF의 최신 버전은 있지만 아직 되지 않은 EF의 모든 기능 6.x 합니다. EF 선택 하는 방법에 대 한 내용은 6.x 및 EF 코어 참조 [EF 코어 vs. EF6.x](https://docs.microsoft.com/ef/efcore-and-ef6/)합니다. EF를 선택 하는 경우 6.x 참조 [이 자습서 시리즈의 이전 버전](https://docs.microsoft.com/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application)합니다.

> [!NOTE]
> * 이 자습서의 ASP.NET Core 1.1 버전에 대 한 참조는 [PDF 형식으로이 자습서의 VS 2017 업데이트 2 버전](https://github.com/aspnet/Docs/blob/master/aspnetcore/data/efmvc/intro/_static/efmvc1.1.pdf)합니다.
> * 이 자습서의 Visual Studio 2015 버전을 보려면 [VS 2015 version of ASP.NET Core documentation in PDF format](https://github.com/aspnet/Docs/blob/master/aspnetcore/common/_static/aspnet-core-project-json.pdf)(ASP.NET Core의 VS 2015 버전 설명서(PDF 형식))을 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE[install 2.0](../../includes/install2.0.md)]

## <a name="troubleshooting"></a>문제 해결

솔루션에 코드를 비교 하 여 일반적으로 찾을 수 문제를 해결할 수 없는 실행 하는 경우는 [완료 된 프로젝트](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)합니다. 일반적인 오류 및 해결 방법을 목록은 참조 하십시오. [계열의 마지막 자습서의 문제 해결 섹션](advanced.md#common-errors)합니다. 에 대 한 StackOverflow.com에 질문을 게시할 수 필요한 있습니다을 찾지 못한 경우 [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) 또는 [EF 코어](https://stackoverflow.com/questions/tagged/entity-framework-core)합니다.

> [!TIP] 
> 각각 이전 자습서에서 수행 되는 동작과 기반으로 하는 일련의 10 자습서입니다.  각 자습서 완료 후 프로젝트의 복사본을 저장 하는 것이 좋습니다.  문제를 실행 하는 경우에 전체 계열의 시작 부분으로 다시 이동 하지 않고도 이전 자습서에서를 통해 시작할 수 있습니다.

## <a name="the-contoso-university-web-application"></a>Contoso 대학 웹 응용 프로그램

이 자습서에를 작성 하는 응용 프로그램은 간단한 대학 웹 사이트.

사용자가 볼 수 있으며 학생과에서는 강사 정보 업데이트 됩니다. 다음은 몇 화면을 만듭니다.

![학생 인덱스 페이지](intro/_static/students-index.png)

![학생 편집 페이지](intro/_static/student-edit.png)

이 자습서는 Entity Framework를 사용 하는 방법에 주로 초점을 맞출 수 있도록이 사이트의 사용자 인터페이스 스타일은 기본 제공 템플릿에서 생성 내용을 가까운 유지 되었습니다.

## <a name="create-an-aspnet-core-mvc-web-application"></a>ASP.NET Core MVC 웹 응용 프로그램 만들기

Visual Studio를 열고 ASP.NET Core C# 웹 라는 새 프로젝트 "ContosoUniversity"를 만듭니다.

* **파일** 메뉴 선택 **새로 만들기 > 프로젝트**합니다.

* 왼쪽된 창에서 선택 **설치 > Visual C# > 웹**합니다.

* 선택 된 **ASP.NET Core 웹 응용 프로그램** 프로젝트 템플릿을 합니다.

* 입력 **ContosoUniversity** 이름과 클릭으로 **확인**합니다.

  ![새 프로젝트 대화 상자](intro/_static/new-project.png)

* 에 대 한 대기는 **새 ASP.NET Core 웹 응용 프로그램 (.NET Core)** 대화 상자를 표시

* 선택 **ASP.NET 코어 2.0** 및 **웹 응용 프로그램 (모델-뷰-컨트롤러)** 서식 파일입니다.

  **참고:** ASP.NET 코어 2.0 및 EF 코어 2.0 이상-있는지 확인 하는이 자습서에서는 **ASP.NET Core 1.1** 선택 하지 않으면 합니다.

* 확인 **인증** 로 설정 된 **인증 안 함**합니다.

* **확인**을 클릭합니다.

  ![새 ASP.NET 프로젝트 대화 상자](intro/_static/new-aspnet.png)

## <a name="set-up-the-site-style"></a>사이트 스타일 설정

몇 가지 간단한 변경 사항이 사이트 메뉴, 레이아웃 및 홈 페이지를 설정 합니다.

열기 *Views/Shared/_Layout.cshtml* 다음과 같이 변경 하 고 있습니다.

* "ContosoUniversity"의 "Contoso 대학"으로 변경 합니다. 다음과 같은 세 가지 항목이 없습니다.

* 메뉴 항목에 대 한 추가 **학생**, **Courses**, **강사**, 및 **부서**, 및 삭제는 **연락처** 메뉴 항목입니다.

변경 내용은 강조 표시 됩니다.

[!code-html[](intro/samples/cu/Views/Shared/_Layout.cshtml?highlight=6,30,36-39,48)]

*Views/Home/Index.cshtml*, 파일의 내용을이 응용 프로그램에 대 한 텍스트도 ASP.NET MVC에 대 한 텍스트를 바꾸려면 다음 코드로 바꿉니다.

[!code-html[](intro/samples/cu/Views/Home/Index.cshtml)]

CTRL + f5 키를 눌러 프로젝트를 실행 하거나 선택할 **디버그 > 디버깅 하지 않고 시작** 메뉴에서 합니다. 이 자습서에서 만드는 페이지에 대 한 탭이 포함 된 홈 페이지가 나타납니다.

![Contoso 대학 홈 페이지](intro/_static/home-page.png)

## <a name="entity-framework-core-nuget-packages"></a>Entity Framework Core NuGet 패키지

EF 핵심 지원에는 프로젝트를 추가 하려면 대상으로 지정할 데이터베이스 공급자를 설치 합니다. 이 자습서에서는 SQL Server, 사용 및 공급자 패키지는 [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/)합니다. 이 패키지에 포함 되어는 [Microsoft.AspNetCore.All](xref:fundamentals/metapackage) metapackage, 하므로 설치할 필요가 없습니다.

이 패키지 및 해당 종속성 (`Microsoft.EntityFrameworkCore` 및 `Microsoft.EntityFrameworkCore.Relational`) EF에 대 한 런타임 지원을 제공 합니다. 도구 패키지를 추가 합니다의 뒷부분에 나오는 [마이그레이션](migrations.md) 자습서입니다. 

Entity Framework Core에 사용할 수 있는 다른 데이터베이스 공급자에 대 한 정보를 참조 하십시오. [데이터베이스 공급자](https://docs.microsoft.com/ef/core/providers/)합니다.

## <a name="create-the-data-model"></a>데이터 모델 만들기

다음 Contoso 대학교 응용 프로그램에 대 한 엔터티 클래스 만듭니다. 다음 세 가지 엔터티부터 시작 합니다.

![학생-등록 과정-데이터 모델 다이어그램](intro/_static/data-model-diagram.png)

간의 일 대 다 관계가 `Student` 및 `Enrollment` 엔터티 간의 일 대 다 관계 이며 `Course` 및 `Enrollment` 엔터티. 즉, 학생 과정을 개수에 관계 없이 등록 될 수 있습니다 및 과정에 학생에 등록 여러 개가 있을 수 있습니다.

다음 섹션에서는 각각에 대 한 이러한 엔터티의 클래스를 만듭니다.

### <a name="the-student-entity"></a>학생 엔터티

![학생 엔터티 다이어그램](intro/_static/student-entity.png)

에 *모델* 폴더를 라는 클래스 파일을 만들어 *Student.cs* 템플릿 코드를 다음 코드로 바꿉니다.

[!code-csharp[Main](intro/samples/cu/Models/Student.cs?name=snippet_Intro)]

`ID` 속성이이 클래스에 해당 하는 데이터베이스 테이블의 기본 키 열이 됩니다. Entity Framework를 기본적으로 명명 된 속성을 해석 `ID` 또는 `classnameID` 기본 키로 합니다.

`Enrollments` 속성은 탐색 속성입니다. 이 엔터티와 관련 된 다른 엔터티와 탐색 속성을 보유 합니다. 이 경우는 `Enrollments` 속성의는 `Student entity` 를 모두 보유할는 `Enrollment` 엔터티는 관련 된 `Student` 엔터티. 즉, 하는 경우 데이터베이스의 지정된 된 학생 행에 두 개의 관련 된 등록 행 (해당 StudentID 외래 키 열에 해당 학생의 기본 키 값을 포함 하는 행)는 `Student` 엔터티의 `Enrollments` 탐색 속성은 포함 하는 것 두 개의 `Enrollment` 엔터티.

해당 형식은 항목 추가, 삭제 및 업데이트와 같은 될 수 있는 목록 이어야 합니다는 탐색 속성 (예: 다 대 다 또는 일 대 다 관계) 여러 엔터티를 보유할 수, 하는 경우 `ICollection<T>`합니다.  지정할 수 있습니다 `ICollection<T>` 또는 형식으로 `List<T>` 또는 `HashSet<T>`합니다. 지정 하는 경우 `ICollection<T>`, EF 만듭니다는 `HashSet<T>` 기본적으로 컬렉션입니다.

### <a name="the-enrollment-entity"></a>등록 엔터티

![등록 엔터티 다이어그램](intro/_static/enrollment-entity.png)

에 *모델* 폴더를 만들 *Enrollment.cs* 기존 코드를 다음 코드로 바꿉니다.

[!code-csharp[Main](intro/samples/cu/Models/Enrollment.cs?name=snippet_Intro)]

`EnrollmentID` 속성은 기본 키를 됩니다;이 엔터티 사용는 `classnameID` 대신 패턴 `ID` 에서 본 것 처럼 자체적으로 `Student` 엔터티. 일반적으로 한 패턴을 선택 하는 데이터 모델 전체에서 사용 합니다. 여기서는 변형 패턴 중 하나를 사용할 수 있는 보여 줍니다. 에 [이후의 자습서](inheritance.md), 어떻게 ID를 사용 하 여 응용 프로그램 이름 없이 쉽게 상속을 구현 하려면 데이터 모델에 표시 됩니다.

`Grade` 속성은 한 `enum`합니다. 다음 매개 변수는 `Grade` 형식 선언을 나타냅니다는 `Grade` 속성은 null을 허용 합니다. Null이 등급은 0 개 등급 다릅니다-null 의미는 등급 알려진 또는 아직 할당 되지 않았습니다.

`StudentID` 속성은 외래 키 및 해당 탐색 속성은 `Student`합니다. `Enrollment` 엔터티는 하 나와 연결 `Student` 엔터티, 속성에는 단일만 포함할 수 있으므로 `Student` 엔터티 (달리는 `Student.Enrollments` 탐색 속성 했 듯이, 여러을 보유할 수 있는 `Enrollment` 엔터티)입니다.

`CourseID` 속성은 외래 키 및 해당 탐색 속성은 `Course`합니다. `Enrollment` 엔터티는 하 나와 연결 `Course` 엔터티.

Entity Framework 라고 하는 경우 외래 키 속성으로 속성을 해석 `<navigation property name><primary key property name>` (예를 들어 `StudentID` 에 대 한는 `Student` 이후 탐색 속성에서 `Student` 엔터티의 기본 키가 `ID`). 외래 키 속성은 단순히 또한 이름은 수 `<primary key property name>` (예를 들어 `CourseID` 이후는 `Course` 엔터티의 기본 키가 `CourseID`).

### <a name="the-course-entity"></a>과정 엔터티

![과정 엔터티 다이어그램](intro/_static/course-entity.png)

에 *모델* 폴더를 만들 *Course.cs* 기존 코드를 다음 코드로 바꿉니다.

[!code-csharp[Main](intro/samples/cu/Models/Course.cs?name=snippet_Intro)]

`Enrollments` 속성은 탐색 속성입니다. A `Course` 개수에 관계 없이 관련 될 수 있는 엔터티 `Enrollment` 엔터티.

에 대 한 내용은 라고는 `DatabaseGenerated` 특성에 [이후의 자습서](complex-data-model.md) 이 시리즈의 합니다. 기본적으로,이 특성 과정 대신 생성 하는 데이터베이스에 대 한 기본 키를 입력할 수 있습니다.

## <a name="create-the-database-context"></a>데이터베이스 컨텍스트 만들기

지정된 된 데이터 모델에 대 한 Entity Framework 기능을 조정 하는 기본 클래스는 데이터베이스 컨텍스트 클래스입니다. `Microsoft.EntityFrameworkCore.DbContext` 클래스에서 파생시키는 방식으로 이 클래스를 만듭니다. 코드에서 데이터 모델에 포함 된 엔터티 지정 합니다. 특정 Entity Framework 동작을 사용자 지정할 수 있습니다. 이 프로젝트에 클래스 이름은 `SchoolContext`합니다.

프로젝트 폴더에 라는 폴더를 만듭니다 *데이터*합니다.

에 *데이터* 폴더 라는 새 클래스 파일을 만들 *SchoolContext.cs*, 템플릿 코드를 다음 코드로 바꿉니다.

[!code-csharp[Main](intro/samples/cu/Data/SchoolContext.cs?name=snippet_Intro)]

이 코드에서는 `DbSet` 각 엔터티 집합에 대 한 속성입니다. Entity Framework 용어에서 엔터티 집합은 일반적으로 데이터베이스 테이블에 해당하고 엔터티는 테이블의 행에 해당합니다.

생략 했습니다 수는 `DbSet<Enrollment>` 및 `DbSet<Course>` 문과 것은 동일 하 게 작동 합니다. Entity Framework 하기 때문에 암시적으로 해당 포함 됩니다는 `Student` 엔터티 참조는 `Enrollment` 엔터티 및 `Enrollment` 엔터티 참조는 `Course` 엔터티.

EF는 데이터베이스를 만들 때 같은 이름을 갖는 테이블을 만듭니다는 `DbSet` 속성 이름입니다. 컬렉션에 대 한 속성 이름에는 일반적으로 (학생을 하지 않고 학생) 복수형 하지만 개발자 또는 테이블 이름을 복수화 여부에 대 한 동의. 이 자습서에 대 한 DbContext 단 수 테이블 이름을 지정 하 여 기본 동작을 재정의 합니다. 이렇게 하려면 마지막 DbSet 속성 뒤 다음 강조 표시 된 코드를 추가 합니다.

[!code-csharp[Main](intro/samples/cu/Data/SchoolContext.cs?name=snippet_TableNames&highlight=16-21)]

## <a name="register-the-context-with-dependency-injection"></a>종속성 주입 컨텍스트 등록

ASP.NET Core 구현 [종속성 주입](../../fundamentals/dependency-injection.md) 기본적으로 합니다. 서비스 (예: EF 데이터베이스 컨텍스트) 종속성 주입 응용 프로그램 시작 중에 등록 됩니다. 이러한 서비스 (예: MVC 컨트롤러)에 필요한 구성 요소를 생성자 매개 변수를 통해 이러한 서비스 제공 됩니다. 이 자습서의 뒷부분에 나오는 컨텍스트 인스턴스를 가져오는 컨트롤러 생성자 코드를 볼 수 있습니다.

등록 하려면 `SchoolContext` 서비스를 열고 *Startup.cs*, 강조 표시 된 줄을 추가 하 고는 `ConfigureServices` 메서드.

[!code-csharp[Main](intro/samples/cu/Startup.cs?name=snippet_SchoolContext&highlight=3-4)]

연결 문자열의 이름에는 메서드를 호출 하 여 컨텍스트에 전달 됩니다는 `DbContextOptionsBuilder` 개체입니다. 로컬 개발에 대 한는 [ASP.NET Core 구성 시스템](../../fundamentals/configuration.md) 는 연결 문자열에서 *appsettings.json* 파일입니다.

추가 `using` 에 대 한 문을 `ContosoUniversity.Data` 및 `Microsoft.EntityFrameworkCore` 네임 스페이스에 다음 프로젝트를 빌드합니다.

[!code-csharp[Main](intro/samples/cu/Startup.cs?name=snippet_Usings)]

열기는 *appsettings.json* 파일을 다음 예제와 같이 연결 문자열을 추가 합니다.

[!code-json[](./intro/samples/cu/appsettings1.json?highlight=2-4)]

### <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

연결 문자열은 SQL Server LocalDB 데이터베이스를 지정 합니다. LocalDB는 SQL Server Express 데이터베이스 엔진의 경량 버전 하며 응용 프로그램 개발의 경우 프로덕션 환경에서 사용 되지 않습니다. LocalDB 요청 시 시작 하 고 사용자 모드에서 실행 되므로 복잡 한 구성이 없습니다. 기본적으로 LocalDB 만듭니다 *.mdf* 데이터베이스 파일에는 `C:/Users/<user>` 디렉터리입니다.

## <a name="add-code-to-initialize-the-database-with-test-data"></a>테스트 데이터로 데이터베이스를 초기화 하는 코드를 추가 합니다.

Entity Framework에서 빈 데이터베이스를 만듭니다.  이 섹션에서는 테스트 데이터로 채우기 위해 데이터베이스를 만든 후 호출 되는 메서드를 작성할 수 있습니다.

사용 하 여 여기에서 `EnsureCreated` 방법을 자동으로 데이터베이스를 만들 수 있습니다. 에 [이후의 자습서](migrations.md) 삭제 하 고 데이터베이스를 다시 작성 하는 대신 데이터베이스 스키마를 변경 하려면 Code First 마이그레이션을 사용 하 여 모델 변경 내용을 처리 하는 방법을 배웁니다.

에 *데이터* 폴더 라는 새 클래스 파일을 만들 *DbInitializer.cs* 템플릿 코드 필요할 때 만들 데이터베이스를 다음 코드로 대체 하 고 부하 테스트 새 데이터 데이터베이스입니다.

[!code-csharp[Main](intro/samples/cu/Data/DbInitializer.cs?name=snippet_Intro)]

코드는 데이터베이스의 어떤 학생 되는 경우 데이터베이스는 새로운 클래스 이며 테스트 데이터로 시드할 해야 하는 경우를 가정 그렇지 않은 경우를 확인 합니다.  배열에 테스트 데이터를 로드 하지 않고 `List<T>` 성능을 최적화 하는 컬렉션입니다.

*Program.cs*, 수정 된 `Main` 응용 프로그램 시작 시에 다음을 수행 하는 메서드:

* 종속성 주입 컨테이너에서 데이터베이스 컨텍스트 인스턴스를 가져옵니다.
* 컨텍스트를 전달 하는 초기값 메서드를 호출 합니다.
* Seed 메서드 수행 되는 경우 컨텍스트를 삭제 합니다.

[!code-csharp[Main](intro/samples/cu/Program.cs?name=snippet_Seed&highlight=3-20)]

추가 `using` 문:

[!code-csharp[Main](intro/samples/cu/Program.cs?name=snippet_Usings)]

이전 자습서에서 유사한 코드를 볼 수 있습니다는 `Configure` 메서드에서 *Startup.cs*합니다. 사용 하는 것이 좋습니다는 `Configure` 요청 파이프라인을 설정할 때만 메서드. 응용 프로그램 시작 코드에 속하는 `Main` 메서드.

이제 처음 응용 프로그램을 실행 하면 데이터베이스 생성 되며 테스트 데이터를 사용 하 여 시드할 합니다. 데이터 모델을 변경할 때마다 데이터베이스를 삭제 하 고, 시드 메서드를 업데이트 하 고, 동일한 방식으로 새 데이터베이스로 후 새로 시작할 수 있습니다. 이후의 자습서에서는 데이터 모델이 변경, 삭제 및 다시 만들기 없이 때 데이터베이스를 수정 하는 방법을 볼 수 있습니다.

## <a name="create-a-controller-and-views"></a>컨트롤러와 뷰 만들기

다음으로, MVC 컨트롤러 및 EF 데이터를 저장 하 고 쿼리를 사용 하 여 뷰를 추가 하려면 Visual Studio의 스 캐 폴딩 엔진을 사용 합니다.

자동 생성의 CRUD 작업 메서드와 뷰를 스 캐 폴딩 라고 합니다. 스 캐 폴딩 스 캐 폴드 코드는 일반적으로 생성 된 코드를 수정 하지 마십시오 하는 반면 사용자 고유의 요구 사항에 맞게 수정할 수 있는 시작 지점을 점에서 코드 생성와 다릅니다. 생성 된 코드를 사용자 지정 해야 하는 경우 partial 클래스를 사용 하거나 항목이 변경 될 때 코드를 다시 생성 합니다.

* 마우스 오른쪽 단추로 클릭는 **컨트롤러** 폴더에 **솔루션 탐색기** 선택 **추가 > 스 캐 폴드 된 새 항목**합니다.

* 에 **MVC 종속성 추가** 대화 상자에서 **최소 종속성**를 선택 하 고 **추가**합니다.

  ![종속성 추가](intro/_static/add-depend.png)

  Visual Studio는 컨트롤러를 스 캐 폴딩 하는 데 필요한 종속성을 추가 합니다. 프로젝트 파일에만 변경 내용이 추가 `Microsoft.VisualStudio.Web.CodeGeneration.Design` 패키지 합니다.

  A *ScaffoldingReadMe.txt* 삭제할 수 있는 파일이 생성 됩니다.

* 다시 한 번, 마우스 오른쪽 단추로 클릭는 **컨트롤러** 폴더에 **솔루션 탐색기** 선택 **추가 > 스 캐 폴드 된 새 항목**합니다.

* 에 **추가 스 캐 폴드** 대화 상자:

  * 선택 **뷰, Entity Framework를 사용 하 여 포함 된 MVC 컨트롤러**합니다.

  * **추가**를 클릭합니다.

* 에 **컨트롤러 추가** 대화 상자:

  * **모델 클래스** 선택 **학생**합니다.

  * **데이터 컨텍스트 클래스가** 선택 **SchoolContext**합니다.

  * 기본값을 적용 **StudentsController** 이름으로 합니다.

  * **추가**를 클릭합니다.

  ![스 캐 폴드 학생](intro/_static/scaffold-student.png)

  클릭할 때 **추가**, Visual Studio 스 캐 폴딩 엔진 만듭니다는 *StudentsController.cs* 파일과 뷰 집합 (*.cshtml* 파일)는 컨트롤러와 작동 하는 합니다.

(스 캐 폴딩 엔진 만들 수도 데이터베이스 컨텍스트를 수동으로 만들지 않는 경우 먼저이 자습서에 대 한 예전 합니다. 새 컨텍스트 클래스를 지정할 수는 **컨트롤러 추가** 상자 오른쪽에 있는 더하기 기호를 클릭 하 여 **데이터 컨텍스트 클래스가**합니다.  그런 다음 visual Studio를 만듭니다 프로그램 `DbContext` 클래스 뿐만 아니라 컨트롤러와 뷰.)

컨트롤러를 사용 한다고 보면는 `SchoolContext` 를 생성자 매개 변수로 합니다.

[!code-csharp[Main](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Context&highlight=5,7,9)]

인스턴스를 전달 하는 ASP.NET 종속성 주입 하므로 `SchoolContext` 컨트롤러에 있습니다. 구성에서 *Startup.cs* 이전 파일.

컨트롤러를 포함 한 `Index` 데이터베이스의 모든 학생을 표시 하는 작업 메서드. 메서드 읽어 설정 학생 엔터티에서 학생의 목록을 가져오고는 `Students` 데이터베이스 컨텍스트 인스턴스의 속성:

[!code-csharp[Main](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex&highlight=3)]

이 자습서의 뒷부분에 나오는이 코드에서 비동기 프로그래밍 요소에 대해 알아봅니다.

*Views/Students/Index.cshtml* 보기 테이블에이 목록에 표시 됩니다.

[!code-html[](intro/samples/cu/Views/Students/Index1.cshtml)]

CTRL + f5 키를 눌러 프로젝트를 실행 하거나 선택할 **디버그 > 디버깅 하지 않고 시작** 메뉴에서 합니다.

테스트 데이터를 보려면 학생 탭을 클릭 하 고 `DbInitializer.Initialize` 삽입 메서드. 브라우저 창이 되 면 표시 최소에 따라는 `Student` 탭 페이지의 맨 위에 링크의 링크를 보려면 오른쪽 맨 위에 탐색 아이콘을 클릭 해야 합니다.

![좁은 Contoso 대학 홈 페이지](intro/_static/home-page-narrow.png)

![학생 인덱스 페이지](intro/_static/students-index.png)

## <a name="view-the-database"></a>데이터베이스 보기

응용 프로그램을 시작 했을 때의 `DbInitializer.Initialize` 메서드 호출 `EnsureCreated`합니다. EF 보여 준다는 사실을 알았습니다 데이터베이스가 없습니다. 했습니다 및 없으므로, 다음의 나머지 부분에서는 생성 된 `Initialize` 메서드 코드는 데이터베이스 데이터로 채워집니다. 사용할 수 있습니다 **SQL Server 개체 탐색기** (SSOX) Visual Studio에서 데이터베이스를 볼 수 있습니다.

브라우저를 닫습니다.

SSOX 창이 열려 있지 않은 경우 선택에서 **보기** Visual Studio에서 메뉴.

SSOX, 클릭 **(localdb) \MSSQLLocalDB > 데이터베이스**, 다음의 연결 문자열에 있는 데이터베이스 이름에 대 한 항목을 클릭 하 고 프로그램 *appsettings.json* 파일입니다.

확장은 **테이블** 노드에서 데이터베이스의 테이블을 볼 수 있습니다.

![SSOX의 테이블](intro/_static/ssox-tables.png)

마우스 오른쪽 단추로 클릭는 **학생** 테이블 마우스 클릭 **데이터 보기** 생성 된 열과 테이블에 삽입 된 행을 볼 수 있습니다.

![학생 표에 SSOX](intro/_static/ssox-student-table.png)

*.mdf* 및 *.ldf* 데이터베이스 파일은는 *C:\Users\<yourusername >* 폴더입니다.

호출 하는 것 때문에 `EnsureCreated` 이니셜라이저 메서드의 백그라운드에서 작업에서 실행 하면 이제 만들 수에 대 한 변경의 `Student` 클래스, 데이터베이스를 삭제 하 고, 응용 프로그램을 다시 실행 하 고 데이터베이스는 자동으로 변경 내용을 맞게 다시 만들어질 수 있습니다. 예를 들어, 추가 하는 경우는 `EmailAddress` 속성을는 `Student` 클래스 표시 새 `EmailAddress` 다시 만든된 테이블의 열입니다.

## <a name="conventions"></a>규칙

규칙 또는 Entity Framework에서는 가정을 사용 되기 때문에 전체 데이터베이스를 만들 수도 수 있게 되기를 Entity Framework에 대 한 순서 대로 작성 해야 했습니다 코드의 양을 최소화 됩니다.

* 이름을 `DbSet` 속성 테이블 이름으로 사용 됩니다. 엔터티에 의해 참조 되지 않는 한 `DbSet` 속성, 엔터티 클래스 이름이 테이블 이름으로 사용 됩니다.

* 엔터티 속성 이름은 열 이름에 사용 됩니다.

* ID 또는 classnameID 명명 된 엔터티 속성은 기본 키 속성으로 인식 됩니다.

* 라고 하는 경우 외래 키 속성으로는 속성을 해석 * <navigation property name> <primary key property name> * (예를 들어 `StudentID` 에 대 한는 `Student` 이후 탐색 속성은 `Student` 엔터티의 기본 키가 `ID`). 외래 키 속성은 단순히 또한 이름은 수 * <primary key property name> * (예를 들어 `EnrollmentID` 이후는 `Enrollment` 엔터티의 기본 키가 `EnrollmentID`).

기본 동작을 재정의할 수 있습니다. 예를 들어이 자습서의 앞부분에서 본 것 처럼 테이블 이름에 지정할 수 명시적으로. 열 이름을 설정할 수 있으며에서 볼 수 있듯이 기본 키 또는 외래 키 속성을 설정 하 고는 [이후의 자습서](complex-data-model.md) 이 시리즈의 합니다.

## <a name="asynchronous-code"></a>비동기 코드

비동기 프로그래밍은 ASP.NET 코어 및 EF 코어에 대 한 기본 모드입니다.

웹 서버를 사용할 수 있는 스레드 수가 제한에 및 로드 양이 많으면 상황에서 모든 사용 가능한 스레드 수 사용. 이 경우 서버를 스레드가 해제 될 때까지 새 요청을 처리할 수 없습니다. I/O가 완료를 대기 하 고 있으므로 작업을 실제로 수행 되지 않습니다은 동안 많은 스레드가 동기 코드와 함께 하느라 정체 될 수 있습니다. 비동기 코드로 i/o가 완료를 대기 하는 프로세스 스레드에서 다른 요청을 처리에 사용할 서버에 대해 해제 됩니다. 따라서 비동기 코드 서버 리소스를 보다 효율적으로 사용할 수 있으며 특정가 지연 없이 더 많은 트래픽을 처리 하도록 서버를 설정 합니다.

비동기 코드는 런타임 시 약간의 오버 헤드를 도입 하지만 성능에 미치는 영향은 매우 적습니다, 하는 동안 트래픽이 많은 상황에 대 한 트래픽이 적은 상황에 맞는 잠재적 성능 향상 됩니다.

다음 코드에서는 `async` 키워드를 `Task<T>` 값을 반환 `await` 키워드 및 `ToListAsync` 메서드를 비동기적으로 실행 하는 코드를 확인 합니다.

[!code-csharp[Main](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex)]

* `async` 키워드가 있으면 컴파일러 메서드 본문의 부분에 대 한 콜백을 생성 하 고 자동으로 만들 수는 `Task<IActionResult>` 반환 되는 개체입니다.

* 반환 형식은 `Task<IActionResult>` 형식의 결과 함께 진행 중인 작업을 나타내는 `IActionResult`합니다.

* `await` 키워드는 컴파일러에 메서드가 두 부분으로 분할 합니다. 첫 번째 부분은 비동기적으로 시작 된 작업을 종료 합니다. 두 번째 부분은 작업이 완료 될 때 호출 되는 콜백 메서드에 배치 됩니다.

* `ToListAsync`비동기 버전의 `ToList` 확장 메서드.

Entity Framework를 사용 하는 비동기 코드를 작성할 때 알아두어야 할 일부의 원인:

* 쿼리 또는 데이터베이스에 보낼 명령을 발생 하는 명령문만 비동기적으로 실행 됩니다. 예를 들어 포함 된 `ToListAsync`, `SingleOrDefaultAsync`, 및 `SaveChangesAsync`합니다.  포함 하지 않습니다, 예를 들어 변경 하는 문에 `IQueryable`와 같은 `var students = context.Students.Where(s => s.LastName == "Davolio")`합니다.

* EF 컨텍스트 스레드로부터 안전 하지 않습니다: 동시에 여러 작업을 수행 하지 마세요. 비동기 EF 메서드를 호출 하는 경우 항상 사용는 `await` 키워드입니다.

* 페이징 등 사용 중인 비동기 코드의 성능 이점을 활용, 라이브러리는 패키지에 있는지 확인 하려는 경우, 또한 비동기 때문에 쿼리가 데이터베이스에 전송 하는 Entity Framework 메서드를 호출 하는 경우 메서드를 사용 합니다.

.NET의 비동기 프로그래밍에 대 한 자세한 내용은 참조 [비동기 개요](https://docs.microsoft.com/dotnet/articles/standard/async)합니다.

## <a name="summary"></a>요약

이제 Entity Framework Core 및 SQL Server Express LocalDB를 사용 하 여 저장 하 고 데이터를 표시 하는 간단한 응용 프로그램을 만들었습니다. 다음 자습서에서는 기본 CRUD 수행 하는 방법을 배우게 됩니다 (만들기, 읽기, 업데이트, 삭제) 작업입니다.

>[!div class="step-by-step"]
[다음](crud.md)  
