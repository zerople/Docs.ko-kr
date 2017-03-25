---
title: "ASP.NET Core MVC EF 코어 10-마이그레이션-4 | Microsoft 문서"
author: tdykstra
description: "이 자습서에서는 ASP.NET 핵심 MVC 응용 프로그램에서 데이터 모델 변경을 관리 하기 위한 EF 코어 마이그레이션 기능을 사용 하 여 시작 합니다."
keywords: "ASP.NET Core, Entity Framework Core 마이그레이션"
ms.author: tdykstra
manager: wpickett
ms.date: 03/15/2017
ms.topic: article
ms.assetid: 81f6c9c2-a819-4f3a-97a4-4b0503b56c26
ms.technology: aspnet
ms.prod: asp.net-core
uid: data/ef-mvc/migrations
translationtype: Machine Translation
ms.sourcegitcommit: 115a74c97de6052ec707ee164641f9c41224b9b3
ms.openlocfilehash: 2c906d90a45da9d2572230729c3ecc83caaa6a8f
ms.lasthandoff: 03/23/2017

---

# <a name="migrations---ef-core-with-aspnet-core-mvc-tutorial-4-of-10"></a>마이그레이션-EF 코어 ASP.NET 핵심 MVC 자습서 (4 / 10)

여 [Tom Dykstra](https://github.com/tdykstra) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)

Contoso University 샘플 웹 응용 프로그램에는 Entity Framework 코어 1.1 및 Visual Studio 2017를 사용 하 여 ASP.NET 핵심 1.1 MVC 웹 응용 프로그램을 만드는 방법을 보여 줍니다. 자습서 시리즈에 대 한 정보를 참조 하십시오. [시리즈의 첫 번째 자습서](intro.md)합니다.

이 자습서에서는 데이터 모델 변경을 관리 하기 위한 EF 코어 마이그레이션 기능을 사용 하 여 시작 합니다. 이후의 자습서에서는 데이터 모델을 변경 하면 더 많은 마이그레이션을 추가 합니다.

## <a name="introduction-to-migrations"></a>마이그레이션 소개

새 응용 프로그램을 개발할 때 데이터 모델에 자주 및 변경 될 때마다 모델 변경, 데이터베이스와 동기화를 가져옵니다. 데이터베이스를 만들려면 존재 하지 않는 경우 Entity Framework를 구성 하 여이 자습서를 시작 합니다. 그런 다음 하면 데이터 모델 변경-추가, 제거 또는 엔터티 클래스를 변경 또는 DbContext 클래스-변경 될 때마다 데이터베이스를 삭제할 수 고 EF 테스트 데이터로 시드합니다와 모델을 일치 하는 새 브러시를 만듭니다.

이 방법은 데이터베이스를 데이터 모델과 동기화 시키는 프로덕션으로 응용 프로그램을 배포 하기 전 까지는 잘 작동 합니다. 응용 프로그램을 유지 하기 위해 때마다 모든 내용을 손실 하지 않으려면 입력 한 데이터를 저장 하는 일반적으로 프로덕션에서 실행 중인 경우에 새 열을 추가 하는 등 변경을 합니다. 새 데이터베이스를 만드는 대신 데이터베이스 스키마를 업데이트 하는 EF를 사용 하 여이 문제를 해결 하는 EF 코어 마이그레이션을 기능입니다.

## <a name="entity-framework-core-nuget-packages-for-migrations"></a>마이그레이션에 대 한 entity Framework 코어 NuGet 패키지

마이그레이션에서 작동 하기 위해 사용할 수는 **패키지 관리자 콘솔** (PMC) 또는 명령줄 인터페이스 (CLI).  이 자습서에는 CLI 명령을 사용 하는 방법을 보여 줍니다. PMC에 대 한 정보는 [이 자습서의 후반부](#pmc)합니다.

명령줄 인터페이스 (CLI)에 대 한 EF 도구에 제공 된 [Microsoft.EntityFrameworkCore.Tools.DotNet](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools.DotNet)합니다. 이 패키지를 설치 하려면 추가 하는 `DotNetCliToolReference` 컬렉션에는 *.csproj* 파일을 표시 합니다. (이 예제의 버전 번호에 자습서가 작성 하는 경우 현재 있었습니다.)

[!code-xml[](intro/samples/cu/ContosoUniversity.csproj?range=23-26&highlight=3)]
  
(편집할 수는 *.csproj* 파일에서 프로젝트 이름을 마우스 오른쪽 단추로 클릭 하 여 **솔루션 탐색기** 선택 하 고 **편집 ContosoUniversity.csproj**.)

## <a name="change-the-connection-string"></a>연결 문자열 변경

에 *appsettings.json* 파일, ContosoUniversity2 또는 다른 이름을 사용 하는 컴퓨터에 사용 하지 않은 연결 문자열에 데이터베이스의 이름을 변경 합니다.

[!code-json[주](intro/samples/cu/appsettings2.json?range=1-4)]

이 변경 내용을 첫 번째 마이그레이션을 새 데이터베이스를 만들 수 있도록 프로젝트를 설정 합니다. 마이그레이션을 시작 하기 위한 필요 하지 않습니다. 하지만 알아봅니다 나중 것이 좋습니다.

> [!NOTE]
> 데이터베이스 이름을 변경 하는 대신, 데이터베이스를 삭제할 수 있습니다. 사용 하 여 **SQL Server 개체 탐색기** (SSOX) 또는 `database drop` CLI 명령:
> ```console
> dotnet ef database drop
> ```
> 다음 섹션에는 CLI 명령을 실행 하는 방법을 설명 합니다.

## <a name="create-an-initial-migration"></a>초기 마이그레이션 만들기

변경 내용을 저장 하 고 프로젝트를 빌드하십시오. 명령 창을 열고 프로젝트 폴더로 이동 합니다. 작업을 수행 하는 빠른 방법을 다음과 같습니다.

* **솔루션 탐색기**프로젝트를 마우스 오른쪽 단추로 클릭 하 고 선택 **파일 탐색기에서 열기** 상황에 맞는 메뉴에서 합니다.

  ![파일 탐색기 메뉴 항목에서 열기](migrations/_static/open-in-file-explorer.png)

* 주소 표시줄에 "cmd"를 입력 하 고 Enter 키를 누릅니다.

  ![명령 창 열기](migrations/_static/open-command-window.png)

명령 창에서 다음 명령을 입력 합니다.

```console
dotnet ef migrations add InitialCreate
```

명령 창에 다음과 같은 출력이 표시.

```console
Build succeeded.
    0 Warning(s)
    0 Error(s)

Time Elapsed 00:00:15.63
Done. To undo this action, use 'ef migrations remove'
```

오류 메시지가 표시 되 면 *실행 파일이 없는 일치 하는 명령 dotnet-ef 발견*, 참조 [이 블로그 게시물](http://thedatafarm.com/data-access/no-executable-found-matching-command-dotnet-ef/) 도움말 문제 해결에 대 한 합니다.

오류 메시지가 표시 되 면 "*... 파일에 액세스할 수 없습니다 ContosoUniversity.dll 다른 프로세스에서 사용 되 고 있으므로 합니다.*", Windows 시스템 트레이에서 IIS Express 아이콘을 찾을 하 고 마우스 오른쪽 단추로 클릭 한 다음 클릭 **ContosoUniversity > 중지 사이트**합니다.

## <a name="examine-the-up-and-down-methods"></a>위쪽을 검토 하 고 아래쪽 메서드

실행 하는 시기는 `migrations add` 명령, EF를 처음부터 데이터베이스를 만드는 코드를 생성 합니다. 이 코드는는 *마이그레이션을* 라는 파일에 저장 폴더 *<timestamp>_InitialCreate.cs*합니다. `Up` 의 메서드는 `InitialCreate` 클래스는 데이터 모델 엔터티 집합에 해당 하는 데이터베이스 테이블을 만듭니다 및 `Down` 다음 예와에서 같이, 메서드 삭제 합니다.

[!code-csharp[주](intro/samples/cu/Migrations/20170215220724_InitialCreate.cs?range=92-120)]

마이그레이션 호출의 `Up` 마이그레이션에 대 한 데이터 모델 변경 내용을 구현 하려면 메서드. 업데이트, 마이그레이션 호출 롤백할 명령을 입력 하는 경우는 `Down` 메서드.

이 코드는 입력 했을 때 만든 초기 마이그레이션에 대 한는 `migrations add InitialCreate` 명령입니다. 마이그레이션 name 매개 변수 (예제의 "InitialCreate") 파일 이름에 사용 되 고 원하는 대로 될 수 있습니다. 단어 또는 마이그레이션에 수행 되는 요약 문구를 선택 하는 것이 좋습니다. 예를 들어 나중에 마이그레이션할 "AddDepartmentTable" 이름을 지정할 수 있습니다.

데이터베이스가 이미 존재 하는 경우 초기 마이그레이션을 만든 경우 데이터베이스 생성 코드가 생성 되지만 데이터베이스는 이미 데이터 모델과 일치 하기 때문에 실행할 필요는 없습니다. 여기서는 데이터베이스가 존재 하지 않으면 아직 데이터베이스를 만드는이 코드가 실행 됩니다 다른 환경에는 응용 프로그램을 배포 하므로 것이 좋습니다는 먼저 테스트 합니다. 그건 마이그레이션을 처음부터 다시 만들 수 있도록 이전-연결 문자열에 데이터베이스의 이름을 변경 합니다.

## <a name="examine-the-data-model-snapshot"></a>데이터 모델 스냅숏을 검사합니다

마이그레이션도 만듭니다. 한 *스냅숏* 에 현재 데이터베이스 스키마의 *Migrations/SchoolContextModelSnapshot.cs*합니다. 해당 코드의 모양은 다음과 같습니다.

[!code-csharp[주](intro/samples/cu/Migrations/SchoolContextModelSnapshot1.cs?name=snippet_Truncate)]

현재 데이터베이스 스키마는 코드를 표현 되므로 EF 코어 마이그레이션을 만들려는 데이터베이스와 상호 작용할 필요가 없습니다. 마이그레이션에 추가 하면 EF 스냅숏 파일에 데이터 모델을 비교 하 여 변경 내용을 결정 합니다. EF는 데이터베이스를 업데이트 해야 하는 경우에 데이터베이스와 상호 작용 합니다. 

마이그레이션 라는 파일을 삭제 하 여 제거할 수 없습니다, 만들 하는 마이그레이션 동기화 되어야 하는 스냅숏 파일에 *<timestamp>_<migrationname>.cs*합니다. 해당 파일을 삭제 하면 나머지 마이그레이션은 데이터베이스 스냅숏 파일와 동기화 됩니다. 추가 된 마지막 마이그레이션을 삭제 하려면 사용 된 [dotnet ef 마이그레이션을 제거](https://docs.microsoft.com/en-us/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove) 명령 합니다.

## <a name="apply-the-migration-to-the-database"></a>마이그레이션을 데이터베이스에 적용

명령 창에서 데이터베이스 및 테이블을 만듭니다 하려면 다음 명령을 입력 합니다.

```console
dotnet ef database update
```

명령의 출력은는 `migrations add` 명령입니다.

```text
Build succeeded.
    0 Warning(s)
    0 Error(s)

Time Elapsed 00:00:17.34
Done.
```

사용 하 여 **SQL Server 개체 탐색기** 첫 번째 자습서에서 수행한 것 처럼 데이터베이스를 검사 합니다.  추적 하는 데이터베이스에 적용 된 마이그레이션이 어떤 __EFMigrationsHistory 테이블의 추가 알 수 있습니다. 해당 테이블의 데이터를 표시 하 고 첫 번째 마이그레이션에 대 한 하나의 항목만 표시 됩니다.

![SSOX에서 마이그레이션 기록](migrations/_static/migrations-table.png)

모든 항목이 여전히 제대로 작동 하는지 확인 이전과 동일 응용 프로그램을 실행 합니다.

![학생 인덱스 페이지](migrations/_static/students-index.png)

<a id="pmc"></a>
## <a name="command-line-interface-cli-vs-package-manager-console-pmc"></a>명령줄 인터페이스 (CLI) vs입니다. 패키지 관리자 콘솔 (PMC)

Visual Studio에서 PowerShell cmdlet 또는.NET Core CLI 명령에서 사용할 수 없으면 마이그레이션 관리 EF 도구 **패키지 관리자 콘솔** (PMC) 창입니다. 이 자습서에서는 CLI를 사용 하는 방법을 설명 하지만 원하는 경우 PMC를 사용할 수 있습니다.

PMC 명령을 사용 하 여 하려는 경우 설치는 [Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools) 패키지 합니다. CLI 도구와는 달리 편집할 필요가 없습니다는 *.csproj* 파일;를 사용 하 여 설치할 수는 **패키지 관리자 콘솔** 또는 **NuGet 패키지 관리자** GUI입니다.

CLI 명령에 대 한 자세한 내용은 참조 [.NET Core CLI](https://docs.microsoft.com/en-us/ef/core/miscellaneous/cli/dotnet)합니다. 

PMC 명령에 대 한 자세한 내용은 참조 [패키지 관리자 콘솔 (Visual Studio)](https://docs.microsoft.com/en-us/ef/core/miscellaneous/cli/powershell)합니다.

## <a name="summary"></a>요약

이 자습서에서 만든 첫 번째 마이그레이션을 적용 하는 방법을 살펴보았습니다. 다음 자습서 인 데이터 모델을 확장 하 여 보다 수준 높은 주제를 보면 먼저 합니다. 그 과정에서 사용자를 만들고 적용 하 여 추가 마이그레이션으로 합니다.

>[!div class="step-by-step"]
[이전](sort-filter-page.md)
[다음](complex-data-model.md)  

