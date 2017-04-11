---
title: "ASP.NET Core 및 Entity Framework 6 시작 | Microsoft 문서"
author: tdykstra
description: "이 문서에서는 ASP.NET 핵심 응용 프로그램에서 Entity Framework 6을 사용 하는 방법을 보여줍니다."
keywords: ASP.NET Core, Entity Framework EF 6
ms.author: tdykstra
manager: wpickett
ms.date: 02/24/2017
ms.topic: article
ms.assetid: 016cc836-4c43-45a4-b9a7-9efaf53350df
ms.technology: aspnet
ms.prod: asp.net-core
uid: data/entity-framework-6
translationtype: Machine Translation
ms.sourcegitcommit: 0ebb9b63931ccb26126740de08270eda9b1ea486
ms.openlocfilehash: d6a863173e4b49bd7ee4a284859acc844db1e27b
ms.lasthandoff: 03/23/2017

---
# <a name="getting-started-with-aspnet-core-and-entity-framework-6"></a>ASP.NET Core과 Entity Framework 6을 시작 하기

여 [Paweł Grudzień](https://github.com/pgrudzien12), [Damien Pontifex](https://github.com/DamienPontifex), 및 [Tom Dykstra](https://github.com/tdykstra)

이 문서에서는 ASP.NET 핵심 응용 프로그램에서 Entity Framework 6을 사용 하는 방법을 보여줍니다.

## <a name="overview"></a>개요

Entity Framework 6을 사용 하려면 Entity Framework 6에서는.NET Core를 지원 하지 않으므로 전체.NET Framework에 대해 컴파일할 수 프로젝트에 있습니다. 로 업그레이드 해야 플랫폼 기능이 필요한 경우 [Entity Framework Core](https://docs.efproject.net)합니다.

ASP.NET 핵심 응용 프로그램에서 Entity Framework 6을 사용 하는 권장된 방법은 EF6 컨텍스트를 적용할 이며 모델 클래스에서 클래스 라이브러리 프로젝트 대상으로 하는 전체 프레임 워크. ASP.NET Core 프로젝트에서 클래스 라이브러리에 대 한 참조를 추가 합니다. 샘플을 참조 하십시오 [EF6 및 ASP.NET 핵심 프로젝트가 있는 Visual Studio 솔루션](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/entity-framework-6/sample/)합니다.

.NET Core 프로젝트의 모든 EF6 명령와 같은 기능을 지원 하지 않으므로 ASP.NET Core 프로젝트에는 e f&6; 컨텍스트를 넣을 수 없습니다 *Enable-migrations* 필요 합니다.

E f&6; 컨텍스트를 찾았으면 프로젝트 형식에 관계 없이 EF6 명령줄 도구만 EF6 컨텍스트와 함께 작동 합니다. 예를 들어 `Scaffold-DbContext` 은 Entity Framework 코어에서 사용할 수 있습니다. 리버스 엔지니어링 데이터베이스의 이름을 EF6 모델에 수행 해야 할 경우 참조 [Code First는 기존 데이터베이스에](https://msdn.microsoft.com/en-us/jj200620)합니다.

## <a name="reference-full-framework-and-ef6-in-the-aspnet-core-project"></a>참조 전체 프레임 워크 및 ASP.NET 핵심 프로젝트에서 e f&6;

ASP.NET Core 프로젝트 전체.NET framework와 e f&6;을 참조 해야 합니다. 예를 들어는 *.csproj* ASP.NET 핵심 프로젝트의 파일 다음 예와 유사 합니다 (파일의 관련 부분에만 표시 됨).

[!code-xml[](entity-framework-6/sample/MVCCore/MVCCore.csproj?range=3-9&highlight=2)]

새 프로젝트를 만드는 경우 사용 하 여는 **ASP.NET 핵심 웹 응용 프로그램 (.NET Framework)** 템플릿.

## <a name="handle-connection-strings"></a>연결 문자열을 처리 합니다.

E f&6; 클래스 라이브러리 프로젝트에서 사용 하는 e f&6; 명령줄 도구는 컨텍스트를 인스턴스화할 수 있도록 기본 생성자가 필요 합니다. 하지만 생성자를 사용 하는 경우 ASP.NET Core 프로젝트에 사용자 컨텍스트 연결 문자열에는 연결 문자열에 전달할 수 있도록 하는 매개 변수 있어야 하도록 지정 하 고 싶을 것입니다. 예를 들면 다음과 같습니다.

[!code-csharp[](entity-framework-6/sample/EF6/SchoolContext.cs?name=snippet_Constructor)]

E f&6; 프로젝트의 구현을 제공 하는 EF6 컨텍스트 매개 변수가 없는 생성자가 없으므로 [IDbContextFactory](https://msdn.microsoft.com/library/hh506876)합니다. E f&6; 명령줄 도구는 찾아 해당 구현을 사용 하 여 컨텍스트를 인스턴스화할 수 있도록 합니다. 예를 들면 다음과 같습니다.

[!code-csharp[](entity-framework-6/sample/EF6/SchoolContextFactory.cs?name=snippet_IDbContextFactory)]

이 샘플 코드는 `IDbContextFactory` 구현을 하드 코드 된 연결 문자열을 전달 합니다. 명령줄 도구에서 사용할 연결 문자열입니다. 클래스 라이브러리를 호출 응용 프로그램을 사용 하는 동일한 연결 문자열을 사용 하는지 확인 하는 전략을 구현 합니다. 예를 들어 두 프로젝트 모두에서 환경 변수에서 값을 구할 수 있습니다.

## <a name="set-up-dependency-injection-in-the-aspnet-core-project"></a>ASP.NET Core 프로젝트에 종속성 주입 설정

핵심 프로젝트의 *Startup.cs* DI (종속성 주입)에 대 한 EF6 컨텍스트를 설정 하는 파일 `ConfigureServices`합니다. EF 컨텍스트 개체는 요청 수명에 대 한 범위가 지정 됩니다.

[!code-csharp[](entity-framework-6/sample/MVCCore/Startup.cs?name=snippet_ConfigureServices&highlight=5)]

그런 다음 DI를 사용 하 여 컨트롤러에서 컨텍스트 인스턴스를 가져올 수 있습니다. 코드는 EF 코어 컨텍스트에 대 한 쓰기는 비슷합니다.

[!code-csharp[](entity-framework-6/sample/MVCCore/Controllers/StudentsController.cs?name=snippet_ContextInController)]

## <a name="sample-application"></a>샘플 응용 프로그램

샘플 응용 프로그램에 대 한 참조는 [샘플 Visual Studio 솔루션](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/entity-framework-6/sample/) 이 기사에 포함 합니다.

이 샘플은 Visual Studio에서 다음 단계에 따라 처음부터 새로 만들 수 있습니다.

* 솔루션을 만듭니다.

* **새 프로젝트 추가 > 웹 > ASP.NET 핵심 웹 응용 프로그램 (.NET Framework)**

* **새 프로젝트 추가 > 기존 Windows 데스크톱 > 클래스 라이브러리 (.NET Framework)**

* **패키지 관리자 콘솔** (PMC) 두 프로젝트 모두에 대 한 명령을 실행 `Install-Package Entityframework`합니다.

* 클래스 라이브러리 프로젝트에서 만드는 데이터 모델 클래스 및는 컨텍스트 클래스의 구현을 `IDbContextFactory`합니다.

* 클래스 라이브러리 프로젝트에 대 한 PMC에서 명령을 실행 `Enable-Migrations` 및 `Add-Migration Initial`합니다. ASP.NET Core 프로젝트를 시작 프로젝트로 설정한 경우 추가 `-StartupProjectName EF6` 이 명령에 있습니다.

* 핵심 프로젝트에서 클래스 라이브러리 프로젝트에 대 한 프로젝트 참조를 추가 합니다.

* 핵심 프로젝트에서의 *Startup.cs*, DI에 대 한 컨텍스트를 등록 합니다.

* 핵심 프로젝트에서의 *appsettings.json*, 연결 문자열을 추가 합니다.

* 핵심 프로젝트의 컨트롤러와 읽기 및 데이터를 쓸 수 있는지 확인 하는 뷰를 추가 합니다. (ASP.NET 핵심 MVC 스 캐 폴딩 클래스 라이브러리에서 참조 된 EF6 컨텍스트와 함께 작동 하지 않는 참고 합니다.)

## <a name="summary"></a>요약

이 문서에서는 ASP.NET 핵심 응용 프로그램에서 Entity Framework 6을 사용 하는 것에 대 한 기본 지침을 제공 합니다.

## <a name="additional-resources"></a>추가 리소스

* [Entity Framework-코드 기반 구성](https://msdn.microsoft.com/en-us/data/jj680699.aspx)

