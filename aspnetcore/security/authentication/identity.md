---
title: "Id 소개 | Microsoft 문서"
author: rick-anderson
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: cf119f21-1a2b-49a2-b052-547ccb66ee83
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/authentication/identity
translationtype: Machine Translation
ms.sourcegitcommit: 010b730d2716f9f536fef889bc2f767afb648ef4
ms.openlocfilehash: d80ae98330af8ae0e959bdf013314b016db83289
ms.lasthandoff: 03/23/2017

---
# <a name="introduction-to-identity"></a>Id 소개

여 [Pranav Rastogi](https://github.com/rustd), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra), Jon Galloway 및 [Erik Reitan](https://github.com/Erikre)

ASP.NET Core Id는 응용 프로그램에 로그인 기능을 추가할 수 있는 멤버 자격 시스템입니다. 사용자 계정 및 로그인 사용자 이름으로 만들 수 있습니다 및 암호 또는 Facebook, Google, Microsoft 계정, Twitter 등는 외부 로그인 공급자를 사용할 수 있습니다.

사용자 이름, 암호 및 프로필 데이터를 저장 하는 SQL Server 데이터베이스를 사용 하 여 ASP.NET 핵심 Id를 구성할 수 있습니다. 또는 Azure 테이블 저장소와 같은 다른 영구 저장소에 영구 저장소에서 저장소 데이터를 사용할 수 있습니다.

## <a name="overview-of-identity"></a>Id의 개요

이 항목에서는 ASP.NET 핵심 Id를 사용 하 여 기능을 등록, 로그인을 추가 하는 방법을 설명 하 고 로그 아웃 한 사용자 합니다. 세부 정보를 단계별 되거나 읽기 함께 따를 수 있습니다. ASP.NET 핵심 Id를 사용 하 여 앱을 만드는 방법에 대 한 더 자세한 내용은이 문서의 끝에 다음 단계 섹션을 참조 하십시오.

1.  개별 사용자 계정으로 Visual Studio에서 ASP.NET 핵심 웹 응용 프로그램 프로젝트를 만듭니다.

    Visual Studio에서 선택 **파일** -> **새로** -> **프로젝트**합니다. 그런 다음 선택 하는 **ASP.NET 웹 응용 프로그램** 에서 **새 프로젝트** 대화 상자입니다. ASP.NET Core를 선택 하 여 계속 **웹 응용 프로그램** 와 **개별 사용자 계정** 인증 방법으로 합니다.
 
    ![새 프로젝트 대화 상자](identity/_static/01-mvc.png)
 
    만든된 프로젝트를 포함 된 `Microsoft.AspNetCore.Identity.EntityFrameworkCore` id 데이터 및 SQL Server를 사용 하 여 스키마를 유지 하는 패키지 [Entity Framework Core](https://docs.efproject.net)합니다.
 
    > [!NOTE]
    >Visual Studio에서 선택 하 여 NuGet 패키지 세부 정보를 볼 수 있습니다 **도구** -> **NuGet 패키지 관리자** -> **솔루션에 대 한 NuGet 패키지 관리**합니다.
 
    Id 서비스에서 응용 프로그램에 추가 되는 `ConfigureServices` 에서 메서드는 `Startup` 클래스:
 
    [!code-csharp[주](identity/sample/src/ASPNET-IdentityDemo/Startup.cs?highlight=10-12&range=38-56)]
    
    이러한 서비스를 통해 응용 프로그램에 제공 된 다음 [종속성 주입](../../fundamentals/dependency-injection.md)합니다.
 
    Identity를 호출 하 여 응용 프로그램에 대 한 사용 `UseIdentity` 에 `Configure` 의 메서드는 `Startup` 클래스입니다. 쿠키 기반 인증 요청 파이프라인에 추가 됩니다.
 
    [!code-csharp[주](identity/sample/src/ASPNET-IdentityDemo/Startup.cs?highlight=22&range=58-89)]
 
    프로세스는 응용 프로그램 시작에 대 한 자세한 내용은 참조 [응용 프로그램 시작](../../fundamentals/startup.md)합니다.

2.  사용자를 만듭니다.
 
    Visual Studio에서 응용 프로그램을 시작 (**디버그** -> **디버깅 시작**)를 클릭 하 고는 **등록** 사용자를 만들려면 브라우저의 링크입니다. 다음 이미지는 사용자 이름 및 암호를 수집 하는 등록 페이지를 보여 줍니다.
 
    ![전자 메일 (사용자 이름), 암호 및 암호 확인에 대 한 사용자 입력된 필드가 있는 등록 페이지](identity/_static/02-reg.png)
 
    사용자가는 **등록** 링크를는 `UserManager` 및 `SignInManager` 서비스 컨트롤러에 주입 됩니다.
 
    [!code-csharp[주](identity/sample/src/ASPNET-IdentityDemo/Controllers/AccountController.cs?highlight=3-4,11-12,17-18&range=19-43)]
 
    그런 다음, **등록** 작업은 호출 하 여 사용자를 만듭니다 `CreateAsync` 의 함수는 `UserManager` 아래와 같이 개체:
 
    [!code-csharp[주](identity/sample/src/ASPNET-IdentityDemo/Controllers/AccountController.cs?highlight=9&range=101-128)]
 
3.  로그인.
 
    사용자 생성 되었으면, 사용자가 로그인 하 여는 `SignInAsync` 에 포함 된 메서드는 `Register` 동작 합니다. 로그인 하 여는 `SignInAsync` 메서드는 사용자의 클레임으로 쿠키를 저장 합니다.
 
    [!code-csharp[주](identity/sample/src/ASPNET-IdentityDemo/Controllers/AccountController.cs?range=101-128&highlight=18)]
 
    위의 `SignInAsync` 메서드 호출에서 아래 `SignInAsync` 작업에 포함 되어 있는 `SignInManager` 클래스입니다.
 
    필요한 경우에 컨트롤러 작업을 내 사용자 id 세부 정보를 액세스할 수 있습니다. 예를 들어, 내에 중단점을 설정 하 여는 `HomeController.Index` 볼 수 작업 메서드는 `User.claims` 세부 정보입니다. 사용자 로그인, 권한 부여 결정을 내릴 수 있습니다. 자세한 내용은 참조 [권한 부여](../authorization/index.md)합니다.
 
    등록 된 사용자로 로그인 할 수 웹 응용 프로그램을 클릭 하 여는 **로그인** 링크 합니다.  등록 된 사용자가 로그인 할 때의 `Login` 의 동작에서 `AccountController` 호출 됩니다. 그런 다음, **로그인** 동작을 사용 하 여 사용자 로그인은 `PasswordSignInAsync` 에 포함 된 메서드는 `Login` 동작 합니다.
 
    [!code-csharp[주](identity/sample/src/ASPNET-IdentityDemo/Controllers/AccountController.cs?highlight=11&range=54-89)]
 
4.  로그 오프 합니다.
 
    클릭 하 고 **로그 오프** 호출 링크는 `LogOff` account 컨트롤러의 작업.
 
    [!code-csharp[주](identity/sample/src/ASPNET-IdentityDemo/Controllers/AccountController.cs?highlight=5&range=131-138)]
 
    위의 코드는 `SignInManager.SignOutAsync` 메서드. `SignOutAsync` 사용자 클레임을 쿠키에 저장 하는 메서드 지웁니다.
 
5.  구성 합니다.

    Id는 응용 프로그램의 시작 클래스에서 재정의할 수 있는 몇 가지 기본 동작에 있습니다.
 
    [!code-csharp[주](identity/sample/src/ASPNET-IdentityDemo/Startup.cs?highlight=5&range=57-78)]
    
    Id를 구성 하는 방법에 대 한 자세한 내용은 참조 [구성 Identity](identity-configuration.md)합니다.
    
    기본 키의 데이터 형식을 구성할 수 참조 [구성 Id 기본 키 데이터 형식](identity-primary-key-configuration.md)합니다.
 
6.  데이터베이스를 표시 합니다.

    응용 프로그램을 중지 한 후 선택 하 여 Visual Studio에서 사용자 데이터베이스를 볼 **보기** -> **SQL Server 개체 탐색기**합니다. 내에서 다음을 확장 한 다음,는 **SQL Server 개체 탐색기**:
    
    * (localdb) \MSSQLLocalDB
    
    * 데이터베이스
    
    * aspnet5-*응용 프로그램의 이름*>
    
    * Tables
    
    그런 다음 마우스 오른쪽 단추로 클릭는 **dbo. AspNetUsers** 선택한 테이블 **데이터 보기** 만든 사용자의 속성을 표시 합니다.
    
    ![AspNetUsers 데이터베이스 테이블에 대 한 상황에 맞는 메뉴](identity/_static/04-db.png)

## <a name="identity-components"></a>Id 구성 요소

Id 시스템에 대 한 기본 참조 어셈블리는 `Microsoft.AspNetCore.Identity`합니다. 이 패키지는 ASP.NET 핵심 Id에 대 한 인터페이스의 핵심 집합을 포함 합니다.

![ASP.NET 핵심 Id의 프로젝트 참조](identity/_static/05-dependencies.png)

ASP.NET 핵심 응용 프로그램에서 id 시스템을 사용 하 여 이러한 종속성 필요 합니다.

* `EntityFramework.SqlServer`-Entity Framework는 관계형 데이터베이스에 대 한 Microsoft의 권장 되는 데이터 액세스 기술입니다.

* `Microsoft.AspNetCore.Authentication.Cookies`미들웨어 쿠키를 사용 하 여 응용 프로그램 기반 인증, ASP와 비슷합니다. NET의 폼 인증입니다.

* `Microsoft.AspNetCore.Cryptography.KeyDerivation`-키 파생에 사용 하는 유틸리티입니다.

* `Microsoft.AspNetCore.Hosting.Abstractions`-호스팅 추상화 합니다.

## <a name="migrating-to-aspnet-core-identity"></a>ASP.NET Core Id로 마이그레이션

추가 정보 및 사용자의 기존 id 마이그레이션하는 방법에 대 한 지침 참조: 매장 [마이그레이션 인증 및 Id](../../migration/identity.md)

<!-- replace ../../ relative links
  [Authentication and Identity](../../migration/identity.md)
with xref links
 [Authentication and Identity](xref:migration/identity)
-->

## <a name="next-steps"></a>다음 단계

* [마이그레이션 인증 및 Id](xref:migration/identity)
* [계정 확인 및 암호 복구](accconfirm.md)
* [SMS와&2; 단계 인증](2fa.md)
* [Facebook, Google 및 다른 외부 공급자를 사용 하 여 인증을 사용 하도록 설정](social/index.md)

