---
title: "ASP.NET Core MVC EF 코어 10-마이그레이션-4"
author: tdykstra
description: "이 자습서에서는 ASP.NET Core MVC 응용 프로그램에서 데이터 모델 변경 내용을 관리 하기 위한 EF 코어 마이그레이션 기능을 사용 하 여 시작 합니다."
keywords: "ASP.NET Core, Entity Framework Core 마이그레이션"
ms.author: tdykstra
manager: wpickett
ms.date: 03/15/2017
ms.topic: get-started-article
ms.assetid: 81f6c9c2-a819-4f3a-97a4-4b0503b56c26
ms.technology: aspnet
ms.prod: asp.net-core
uid: data/ef-mvc/migrations
ms.openlocfilehash: 4d81099d1ab97a8a49d96657153a54aa96dd6bf8
ms.sourcegitcommit: 74e22e08e3b08cb576e5184d16f4af5656c13c0c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2017
---
# <a name="migrations---ef-core-with-aspnet-core-mvc-tutorial-4-of-10"></a>마이그레이션-EF 코어 ASP.NET Core MVC 자습서 (4 / 10)

여 [Tom Dykstra](https://github.com/tdykstra) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)

Contoso 대학 샘플 웹 응용 프로그램에는 Entity Framework Core 및 Visual Studio를 사용 하 여 ASP.NET Core MVC 웹 응용 프로그램을 만드는 방법을 보여 줍니다. 자습서 시리즈에 대 한 정보를 참조 하십시오. [시리즈의 첫 번째 자습서](intro.md)합니다.

이 자습서에서는 데이터 모델 변경 내용을 관리 하기 위한 EF 코어 마이그레이션 기능을 사용 하 여 시작 합니다. 이후의 자습서에서는 데이터 모델을 변경 하면 더 많은 마이그레이션을 추가 합니다.

## <a name="introduction-to-migrations"></a>로 마이그레이션에도 소개

새 응용 프로그램을 개발 하는 경우 데이터 모델에 자주 및 변경 될 때마다 모델 변경, 데이터베이스와 동기화를 가져옵니다. 존재 하지 않는 경우 데이터베이스를 만들려면를 Entity Framework를 구성 하 여이 자습서를 시작 합니다. 그런 다음 데이터 모델을 변경 등의 추가, 제거 또는 엔터티 클래스를 변경 또는 DbContext 클래스-변경 될 때마다 데이터베이스를 삭제할 수 고 EF 모델을 일치 시키고 테스트 데이터로 초기값을 설정 하는 새 브러시를 만듭니다.

이 방법은 데이터베이스를 데이터 모델을 통해 동기화 유지 프로덕션 환경에 응용 프로그램을 배포할 때까지 잘 작동 합니다. 응용 프로그램에서 데이터를 보관 하 고 모든 될 때마다 손실 하지 않으려면를 일반적으로 저장 하는 프로덕션에서 실행 중인 경우에 새 열을 추가 하는 등 변경을 합니다. EF 코어 마이그레이션 기능 새 데이터베이스를 만드는 대신 데이터베이스 스키마를 업데이트 하는 EF를 사용 하 여이 문제를 해결 합니다.

## <a name="entity-framework-core-nuget-packages-for-migrations"></a>마이그레이션에 대 한 entity Framework Core NuGet 패키지

마이그레이션을 사용 하려면 사용할 수 있습니다는 **패키지 관리자 콘솔** (PMC) 또는 명령줄 인터페이스 (CLI).  이 자습서에는 CLI 명령을 사용 하는 방법을 보여 줍니다. PMC에 대 한 정보는 [이 자습서의 최종](#pmc)합니다.

제공 되는 CLI (명령줄 인터페이스)에 대 한 EF 도구 [Microsoft.EntityFrameworkCore.Tools.DotNet](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools.DotNet)합니다. 이 패키지를 설치 하려면 추가 하는 `DotNetCliToolReference` 컬렉션에는 *.csproj* 표시 된 것 처럼 파일입니다. **참고:** 편집 하 여이 패키지를 설치 해야는 *.csproj* 파일; 사용할 수 없습니다는 `install-package` 패키지 관리자 GUI 나 명령입니다. 편집할 수는 *.csproj* 파일에서 프로젝트 이름을 마우스 오른쪽 단추로 클릭 하 여 **솔루션 탐색기** 선택 하 고 **편집 ContosoUniversity.csproj**합니다.

[!code-xml[](intro/samples/cu/ContosoUniversity.csproj?range=12-15&highlight=2)]
  
(이 예제의 버전 번호를 자습서 기록 될 때 현재 했습니다.) 

## <a name="change-the-connection-string"></a>연결 문자열 변경

에 *appsettings.json* 파일에서 ContosoUniversity2 또는 다른 이름으로 사용 중인 컴퓨터에서 사용 하지 않은 연결 문자열에 데이터베이스의 이름을 변경 합니다.

[!code-json[Main](intro/samples/cu/appsettings2.json?range=1-4)]

이러한 변경 하 여 첫 번째 마이그레이션 새 데이터베이스를 만드는 프로젝트를 설정 합니다. 마이그레이션 시작 하기 위한 필요 하지 않습니다 되지만 것이 좋습니다 나중에 표시 됩니다.

> [!NOTE]
> 데이터베이스 이름을 변경 하는 대신, 데이터베이스를 삭제할 수 있습니다. 사용 하 여 **SQL Server 개체 탐색기** (SSOX) 또는 `database drop` CLI 명령을:
> ```console
> dotnet ef database drop
> ```
> 다음 섹션에는 CLI 명령을 실행 하는 방법을 설명 합니다.

## <a name="create-an-initial-migration"></a>초기 마이그레이션 만들기

변경 내용을 저장 하 고 프로젝트를 빌드하십시오. 명령 창을 열고 프로젝트 폴더로 이동 합니다. 작업을 수행 하는 빠른 방법을 다음과 같습니다.

* **솔루션 탐색기**프로젝트를 마우스 오른쪽 단추로 클릭 하 고 선택 **파일 탐색기에서 열기** 상황에 맞는 메뉴입니다.

  ![파일 탐색기 메뉴 항목에서 열기](migrations/_static/open-in-file-explorer.png)

* 주소 표시줄에 "cmd"를 입력 하 고 Enter 키를 누릅니다.

  ![명령 창 열기](migrations/_static/open-command-window.png)

명령 창에서 다음 명령을 입력 합니다.

```console
dotnet ef migrations add InitialCreate
```

명령 창에 다음과 같은 출력이 표시 됩니다.

```console
info: Microsoft.AspNetCore.DataProtection.KeyManagement.XmlKeyManager[0]
      User profile is available. Using 'C:\Users\username\AppData\Local\ASP.NET\DataProtection-Keys' as key repository and Windows DPAPI to encrypt keys at rest.
info: Microsoft.EntityFrameworkCore.Infrastructure[100403]
      Entity Framework Core 2.0.0-rtm-26452 initialized 'SchoolContext' using provider 'Microsoft.EntityFrameworkCore.SqlServer' with options: None
Done. To undo this action, use 'ef migrations remove'
```

> [!NOTE]
> 오류 메시지가 표시 되 면 *없는 실행 파일을 일치 하는 명령 "dotnet-ef" 찾을*, 참조 [이 블로그 게시물](http://thedatafarm.com/data-access/no-executable-found-matching-command-dotnet-ef/) 문제 해결 도움말에 대 한 합니다.

오류 메시지가 표시 되 면 "*... 파일에 액세스할 수 없습니다 ContosoUniversity.dll 다른 프로세스에서 사용 되 고 있으므로 합니다.* "Windows 시스템 트레이에서 찾기 IIS Express 아이콘을 마우스 오른쪽 단추로 클릭 차례로 클릭 **ContosoUniversity > 중지 사이트**합니다.

## <a name="examine-the-up-and-down-methods"></a>위쪽을 검토 하 고 아래쪽 메서드

실행 하는 시기는 `migrations add` 명령, EF에서 생성 한 코드를 처음부터 데이터베이스 만들기. 이 코드는는 *마이그레이션* 라는 파일에 폴더  *\<타임 스탬프 > _InitialCreate.cs*합니다. `Up` 의 메서드는 `InitialCreate` 클래스는 데이터 모델 엔터티 집합에 해당 하는 데이터베이스 테이블을 만듭니다 및 `Down` 다음 예제와 같이, 메서드 삭제 합니다.

[!code-csharp[Main](intro/samples/cu/Migrations/20170215220724_InitialCreate.cs?range=92-118)]

마이그레이션 호출은 `Up` 마이그레이션에 대 한 데이터 모델 변경 내용을 구현 하려면 메서드. Update, 마이그레이션 호출을 롤백해야 하는 명령을 입력할 때는 `Down` 메서드.

이 코드는 입력 했을 때 만들어진 초기 마이그레이션에 대 한는 `migrations add InitialCreate` 명령입니다. 마이그레이션 name 매개 변수 (이 예제에서 "InitialCreate") 파일 이름에 대해 사용 되 고 원하는 작업이 무엇이 든 될 수 있습니다. 단어 또는 구를 마이그레이션에서 수행 되는 것을 요약 하는 선택 하는 것이 좋습니다. 예를 들어 나중에 마이그레이션할 "AddDepartmentTable" 이름을 지정할 수 있습니다.

데이터베이스가 이미 존재 하는 경우 초기 마이그레이션을 만든 경우 데이터베이스 만들기 코드 생성 되지만 데이터베이스는 이미 데이터 모델 일치 하기 때문에 실행할 필요는 없습니다. 여기서 데이터베이스가 아직 없는, 데이터베이스를 만들려면이 코드가 실행 되는 다른 환경에 앱을 배포할 때 하므로 이기 먼저 테스트 하는 것이 좋습니다. 바로 이러한 이유로 마이그레이션을 처음부터 다시 만들 수 있도록 이전-연결 문자열에 데이터베이스의 이름을 변경 합니다.

## <a name="examine-the-data-model-snapshot"></a>데이터 모델 스냅숏을 검사합니다

마이그레이션도 만듭니다.는 *스냅숏* 에 현재 데이터베이스 스키마의 *Migrations/SchoolContextModelSnapshot.cs*합니다. 해당 코드의 모양을 다음과 같습니다.

[!code-csharp[Main](intro/samples/cu/Migrations/SchoolContextModelSnapshot1.cs?name=snippet_Truncate)]

현재 데이터베이스 스키마, 코드에 표현 되므로 EF 코어 마이그레이션 만들려는 데이터베이스와 상호 작용할 필요가 없습니다. 마이그레이션에 추가 하면 EF 스냅숏 파일에 데이터 모델을 비교 하 여 변경 내용을 결정 합니다. EF는 데이터베이스를 업데이트 해야 하는 경우에 데이터베이스와 상호 작용 합니다. 

명명 된 파일을 삭제 하 여 마이그레이션의 제거할 수 없습니다 만들 하는 마이그레이션 동기화 되어야 하는 스냅숏 파일에  *\<타임 스탬프 > _\<migrationname >.cs*합니다. 해당 파일을 삭제 하면 나머지 마이그레이션은 데이터베이스 스냅숏 파일와 동기화 됩니다. 사용자가 추가한 마지막 마이그레이션을 삭제 하려면 사용 하 여는 [dotnet ef 마이그레이션 제거](https://docs.microsoft.com/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove) 명령입니다.

## <a name="apply-the-migration-to-the-database"></a>마이그레이션 데이터베이스에 적용

명령 창에서에 데이터베이스 및 테이블을 만들려면 다음 명령을 입력 합니다.

```console
dotnet ef database update
```

명령의 출력은는 `migrations add` 명령을 제외 하 고 데이터베이스를 설정 하는 SQL 명령에 대 한 로그를 참조 하십시오. 대부분의 로그는 다음 예제 출력에서 생략 됩니다. 이 수준의 로그 메시지에 대 한 세부 정보를 표시 하지 않으려는 경우의 로그 수준을 변경할 수 있습니다는 *appsettings 합니다. Development.json* 파일입니다. 자세한 내용은 참조 [로깅 소개](xref:fundamentals/logging)합니다.

```text
info: Microsoft.AspNetCore.DataProtection.KeyManagement.XmlKeyManager[0]
      User profile is available. Using 'C:\Users\username\AppData\Local\ASP.NET\DataProtection-Keys' as key repository and Windows DPAPI to encrypt keys at rest.
info: Microsoft.EntityFrameworkCore.Infrastructure[100403]
      Entity Framework Core 2.0.0-rtm-26452 initialized 'SchoolContext' using provider 'Microsoft.EntityFrameworkCore.SqlServer' with options: None
info: Microsoft.EntityFrameworkCore.Database.Command[200101]
      Executed DbCommand (467ms) [Parameters=[], CommandType='Text', CommandTimeout='60']
      CREATE DATABASE [ContosoUniversity2];
info: Microsoft.EntityFrameworkCore.Database.Command[200101]
      Executed DbCommand (20ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      CREATE TABLE [__EFMigrationsHistory] (
          [MigrationId] nvarchar(150) NOT NULL,
          [ProductVersion] nvarchar(32) NOT NULL,
          CONSTRAINT [PK___EFMigrationsHistory] PRIMARY KEY ([MigrationId])
      );

<logs omitted for brevity>

info: Microsoft.EntityFrameworkCore.Database.Command[200101]
      Executed DbCommand (3ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      INSERT INTO [__EFMigrationsHistory] ([MigrationId], [ProductVersion])
      VALUES (N'20170816151242_InitialCreate', N'2.0.0-rtm-26452');
Done.
```

사용 하 여 **SQL Server 개체 탐색기** 첫 번째 자습서에서와 같이 데이터베이스를 검사 합니다.  추적 하는 데이터베이스에 적용 된 마이그레이션이 어떤 __EFMigrationsHistory 테이블의 추가 확인할 수 있습니다. 해당 테이블에서 데이터를 확인 하 고 첫 번째 마이그레이션에 한 행씩 표시 됩니다. (마지막 로그 CLI 출력 앞의 예제에서이 행을 만들고 INSERT 문을 보여 줍니다.)

모든 항목이 여전히 제대로 작동 하는지 확인 이전과 동일 응용 프로그램을 실행 합니다.

![학생 인덱스 페이지](migrations/_static/students-index.png)

<a id="pmc"></a>
## <a name="command-line-interface-cli-vs-package-manager-console-pmc"></a>CLI (명령줄 인터페이스) vs입니다. 패키지 관리자 콘솔 (PMC)

Visual Studio에서 PowerShell cmdlet 또는.NET Core CLI 명령에서 사용할 수 없으면 마이그레이션 관리 EF tooling **패키지 관리자 콘솔** (PMC) 창. 이 자습서에서는 CLI를 사용 하는 방법을 보여 줍니다. 하지만 원하는 경우에 PMC를 사용할 수 있습니다.

PMC 명령에 대 한 EF 명령은 [Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools) 패키지 합니다. 이 패키지에 이미 포함 되어는 [Microsoft.AspNetCore.All](xref:fundamentals/metapackage) metapackage, 하므로 설치할 필요가 없습니다.

**중요:** 이 동일한 패키지를 편집 하 여 CLI를 설치 하는 것과는 *.csproj* 파일입니다. 이 개체의 이름이 끝나는 `Tools`, CLI 패키지 이름에는 달리 `Tools.DotNet`합니다.

CLI 명령에 대 한 자세한 내용은 참조 [.NET Core CLI](https://docs.microsoft.com/ef/core/miscellaneous/cli/dotnet)합니다. 

PMC 명령에 대 한 자세한 내용은 참조 [패키지 관리자 콘솔 (Visual Studio)](https://docs.microsoft.com/ef/core/miscellaneous/cli/powershell)합니다.

## <a name="summary"></a>요약

이 자습서에서 만든 첫 번째 마이그레이션을 적용 하는 방법을 살펴보았습니다. 다음 자습서에서는 데이터 모델을 확장 하 여 더 많은 고급 항목을 살펴보고 먼저 하겠습니다. 방식에 따라 만들 하 고 추가 마이그레이션을 적용 합니다.

>[!div class="step-by-step"]
[이전](sort-filter-page.md)
[다음](complex-data-model.md)  
