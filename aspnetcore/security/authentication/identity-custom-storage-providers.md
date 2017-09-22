---
title: "ASP.NET Core Id에 대 한 사용자 지정 저장소 공급자 | Microsoft Docs"
author: ardalis
description: "ASP.NET Core Id에 대 한 사용자 지정 저장소 공급자를 구성 하는 방법입니다."
keywords: "ASP.NET Core, Identity, 사용자 지정 저장소 공급자"
ms.author: riande
manager: wpickett
ms.date: 05/24/2017
ms.topic: article
ms.assetid: b2ace545-ecf6-4664-b31e-b65bd4a6b025
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/authentication/identity-custom-storage-providers
ms.openlocfilehash: 214343c9b964baca3c436966d39caede1a58aba2
ms.sourcegitcommit: 78d28178345a0eea91556e4cd1adad98b1446db8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2017
---
# <a name="custom-storage-providers-for-aspnet-core-identity"></a>ASP.NET Core Id에 대 한 사용자 지정 저장소 공급자

으로 [Steve Smith](https://ardalis.com/)

ASP.NET Core Id는 사용자 지정 저장소 공급자를 만들고 응용 프로그램에 연결할 수 있도록 하는 확장 가능한 시스템입니다. 이 항목에서는 ASP.NET Core Id에 대 한 사용자 지정 된 저장소 공급자를 만드는 방법을 설명 합니다. 저장소 공급자를 만들기 위한 중요 한 개념을 다루지만 단계별 연습 않습니다.

[보기 또는 GitHub에서 샘플을 다운로드](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authentication/identity/sample)합니다.

## <a name="introduction"></a>소개

기본적으로 ASP.NET Core Id 시스템 Entity Framework Core를 사용 하 여 SQL Server 데이터베이스에 사용자 정보를 저장 합니다. 많은 응용 프로그램에 대 한이 방법은 잘 작동합니다. 그러나 데이터 스키마 또는 다른 지 속성 메커니즘을 사용 하도록 할 수도 있습니다. 예:

* 사용 하면 [Azure 테이블 저장소](https://docs.microsoft.com/azure/storage/) 또는 다른 데이터 저장소입니다.
* 데이터베이스 테이블에는 다른 구조가 있습니다. 
* 와 같은 다른 데이터 액세스 접근 방식을 사용 하려면 [Dapper](https://github.com/StackExchange/Dapper)합니다. 

각이 경우에서 저장 메커니즘에 대 한 사용자 지정된 공급자를 작성할 수 있으며 응용 프로그램에 해당 공급자를 연결 합니다. 키를 누릅니다.

ASP.NET Core Id는 "개별 사용자 계정" 옵션을 사용 하 여 Visual Studio에서 프로젝트 템플릿을에 포함 됩니다.

.NET Core CLI를 사용 하는 경우 추가 `-au Individual`:

```
dotnet new mvc -au Individual
dotnet new webapi -au Individual
```

## <a name="the-aspnet-core-identity-architecture"></a>ASP.NET Core Id 아키텍처

ASP.NET Core Id 관리자와 저장소를 호출 하는 클래스로 구성 됩니다. *관리자* 높은 수준의 하는 클래스를 응용 프로그램 개발자는 Id 사용자를 만드는 등의 작업을 수행 하기 위해 사용 합니다. *저장소* 는 사용자 및 역할을 하는 등의 엔터티는 유지 하는 방법을 지정 하는 하위 클래스입니다. 저장소에 따라는 [리포지토리 패턴](http://deviq.com/repository-pattern/) 고 밀접 하 게 지 속성 메커니즘으로 결합 되어 수행 됩니다. 관리자는 (구성)를 제외한 응용 프로그램 코드를 변경 하지 않고 지 속성 메커니즘을 바꿀 수 있습니다 의미 하는 저장소에서 분리 됩니다.

다음 다이어그램에서는 저장소 데이터 액세스 계층 상호 작용 하는 동안 웹 응용 프로그램 관리자, 상호 작용 방법을 보여 줍니다.

![ASP.NET Core 응용 프로그램 관리자 (예를 들어 'UserManager', 'RoleManager')와 함께 작동합니다. 관리자 저장소 사용 (예를 들어 ' UserStore') 통신 하는 Entity Framework Core와 같은 라이브러리를 사용 하 여 데이터 원본.](identity-custom-storage-providers/_static/identity-architecture-diagram.png)

사용자 지정 저장소 공급자를 만들려면 (위의 다이어그램에서 녹색, 회색 상자)이 데이터 액세스 계층으로 데이터 원본, 데이터 액세스 계층 및 상호 작용 하는 저장소 클래스를 만듭니다. 관리자 또는 그 (위의 파란색 상자는)와 상호 작용 하는 응용 프로그램 코드를 사용자 지정할 필요가 없습니다.

새 인스턴스를 만들 때 `UserManager` 또는 `RoleManager` 사용자 클래스의 형식을 제공 하 고 저장소 클래스의 인스턴스를 인수로 전달 합니다. 이 방법을 사용 하면 ASP.NET Core에 사용자 지정 된 클래스를 연결할 수 있습니다. 

[새 저장소 공급자를 사용 하도록 응용 프로그램을 다시 구성](#reconfigure-app-to-use-new-storage-provider) 인스턴스화하는 방법을 보여 줍니다. `UserManager` 및 `RoleManager` 사용자 지정 된 저장소와 함께 합니다.

## <a name="aspnet-core-identity-stores-data-types"></a>ASP.NET Core Identity 저장 데이터 형식

[ASP.NET Core Id](https://github.com/aspnet/identity) 데이터 형식은 다음 섹션에서 자세히 설명 합니다.

### <a name="users"></a>Users

웹 사이트의 등록 된 사용자입니다. [IdentityUser](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnet.identity.corecompat.identityuser) 형식을 확장 하거나 사용자 고유의 사용자 지정 형식에 대 한 예제로 사용 될 수 있습니다. 사용자 지정 id 저장소 솔루션을 구현 하는 특정 형식에서 상속할 필요가 없습니다.

### <a name="user-claims"></a>사용자 클레임

문 집합 (또는 [클레임](https://docs.microsoft.com//dotnet/api/system.security.claims.claim) 사용자의 id를 나타내는 사용자에 대 한 합니다. 더 나은 식의 역할을 통해 액세스가 가능 보다는 사용자의 id 사용 하도록 설정할 수 있습니다.

### <a name="user-logins"></a>사용자 로그인

외부 인증 공급자 (예: Facebook 또는 Microsoft 계정)에 대 한 정보는 사용자를 로그인 할 때 사용 하도록 합니다. [예제](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnet.identity.corecompat.identityuserlogin)

### <a name="roles"></a>역할

사이트에 대 한 권한 부여 그룹입니다. 역할 Id 및 역할 이름 (예: "Admin" 또는 "Employee")를 포함합니다. [예제](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnet.identity.corecompat.identityrole)

## <a name="the-data-access-layer"></a>데이터 액세스 계층

이 항목에서는 사용 하고자 하는 지 속성 메커니즘 및 해당 메커니즘에 대 한 엔터티를 만드는 방법에 익숙한 가정 합니다. 이 항목을 저장소 또는 데이터 액세스 클래스; 만드는 방법에 대 한 세부 정보를 제공 하지 않습니다. ASP.NET Core Id 작업할 때 디자인 결정에 대 한 몇 가지 제안 사항 제공 합니다.

사용자 지정 된 저장소 공급자에 대 한 데이터 액세스 계층을 디자인할 때 원하는 대로 많은 해야 합니다. 응용 프로그램에서 사용 하려는 기능에 대 한 지 속성 메커니즘을 만드는 하기만 하면 됩니다. 예를 들어 앱에서 역할을 사용 하지 않는 경우 않아도 역할 또는 사용자 역할 연결에 대 한 저장소를 만드는 합니다. 기술 및 기존 인프라에는 ASP.NET Core Id의 기본 구현에서 매우 다른 구조가 필요할 수 있습니다. 데이터 액세스 계층, 저장소 구현 구조와 함께 작동 하는 논리를 제공할 수 있습니다.

데이터 액세스 계층을 데이터 소스에서 ASP.NET Core Id 데이터를 저장 하는 논리를 제공 합니다. 사용자 지정 된 저장소 공급자에 대 한 데이터 액세스 계층에는 사용자 및 역할 정보를 저장 하는 다음 클래스가 포함 될 수 있습니다.

### <a name="context-class"></a>Context 클래스

지 속성 메커니즘에 연결 하 고 쿼리를 실행 하는 정보를 캡슐화 합니다. 종속성 주입을 통해 일반적으로 제공 되는이 클래스의 인스턴스를 필요로 하는 여러 데이터 클래스입니다. [예제](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnet.identity.corecompat.identitydbcontext-1)합니다.

### <a name="user-storage"></a>사용자 저장소

저장 하 고 사용자 정보 (예: 사용자 이름 및 암호 해시)를 검색 합니다. [예제](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnet.identity.corecompat.userstore-1)

### <a name="role-storage"></a>역할 저장소

저장 하 고 (예: 역할 이름) 역할 정보를 검색 합니다. [예제](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.identity.entityframeworkcore.rolestore-1)

### <a name="userclaims-storage"></a>UserClaims 저장소

저장 하 고 사용자 클레임 정보 (예: 클레임 유형 및 값)를 검색 합니다. [예제](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnet.identity.corecompat.userstore-1)

### <a name="userlogins-storage"></a>UserLogins 저장소

저장 하 고 (예: 외부 인증 공급자 사용 하 는) 사용자 로그인 정보를 검색 합니다. [예제](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnet.identity.corecompat.userstore-1)

### <a name="userrole-storage"></a>UserRole 저장소

저장 하 고 있는 사용자에 게 할당 된 역할을 검색 합니다. [예제](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnet.identity.corecompat.userstore-1)

**팁:** 만 응용 프로그램에서 사용 하려는 클래스를 구현 합니다.

데이터 액세스 클래스에서 지 속성 메커니즘에 대 한 데이터 작업을 수행 하는 코드를 제공 합니다. 예를 들어 사용자 지정 공급자 내에서 할 수 있습니다에 새 사용자를 만들려면 다음 코드는 *저장* 클래스:

[!code-csharp[Main](identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/CustomUserStore.cs?name=createuser&highlight=7)]

사용자 만들기에 대 한 구현 논리에는 ``_usersTable.CreateAsync`` 메서드를 아래에 표시 합니다.

## <a name="customize-the-user-class"></a>사용자 클래스를 사용자 지정

저장소 공급자를 구현할 때에 사용자 클래스를 만듭니다는 [ `IdentityUser` 클래스](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnet.identity.corecompat.identityuser)합니다.

여기에 최소한 사용자 클래스에 포함 해야 합니다는 `Id` 및 `UserName` 속성입니다.

`IdentityUser` 클래스 속성을 정의 하는 ``UserManager`` 호출을 수행할 때 요청 작업입니다. 기본 유형을 `Id` 속성은 문자열 이지만에서 상속할 수 있습니다 `IdentityUser<TKey, TUserClaim, TUserRole, TUserLogin, TUserToken>` 하 고 다른 형식을 지정 합니다. 프레임 워크는 데이터 형식 변환을 처리 하는 저장소 구현이 필요 합니다.

## <a name="customize-the-user-store"></a>사용자 저장소에 사용자 지정

만들기는 `UserStore` 사용자에 대 한 모든 데이터 작업에 대 한 메서드를 제공 하는 클래스입니다. 이 클래스는 해당 하는 [UserStore<TUser> ](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.identity.entityframeworkcore.userstore-1) 클래스입니다. 사용자 `UserStore` 클래스를 구현 `IUserStore<TUser>` 및 필요한 선택적 인터페이스입니다. 응용 프로그램에서 제공 하는 기능에 따라 구현 해야 하는 선택적 인터페이스를 선택 합니다.

### <a name="optional-interfaces"></a>선택적 인터페이스

- IUserRoleStore https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.identity.iuserrolestore-1
- IUserClaimStore https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.identity.iuserclaimstore-1
- S w https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.identity.iuserpasswordstore-1
- IUserSecurityStampStore<!-- make these all links and remove / -->
- O r e
- IPhoneNumberStore
- I q
- IUserLoginStore
- T w
- O r e

선택적 인터페이스에서 상속 하므로 `IUserStore`합니다. 저장 부분적으로 구현 된 샘플 사용자를 볼 수 있습니다 [여기](https://github.com/aspnet/Docs/blob/master/aspnetcore/security/authentication/identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/CustomUserStore.cs)합니다.

내에서 `UserStore` 작업을 수행 하기 위해 만든 데이터 액세스 클래스를 사용할 클래스입니다. 이러한 종속성 주입을 사용 하 여 전달 됩니다. 예를 들어 Dapper 구현으로 SQL Server에에서는 `UserStore` 클래스에는 `CreateAsync` 의 인스턴스를 사용 하는 메서드 `DapperUsersTable` 새 레코드를 삽입:

[!code-csharp[Main](identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/DapperUsersTable.cs?name=createuser&highlight=7)]

### <a name="interfaces-to-implement-when-customizing-user-store"></a>사용자 저장소에 사용자 지정 하는 경우 구현 하는 인터페이스

- **IUserStore**  
 [IUserStore&lt;s e r&gt; ](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.identity.iuserstore-1) 인터페이스는 인터페이스는 사용자 저장소에 구현 해야 합니다. 만들기, 업데이트, 삭제 및 사용자를 검색 하기 위한 메서드를 정의 합니다.
- **IUserClaimStore**  
 [IUserClaimStore&lt;s e r&gt; ](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.identity.iuserclaimstore-1) 인터페이스 사용자 클레임을 사용 하도록 설정 하기 위해 구현할 메서드를 정의 합니다. 추가, 제거 및 사용자 클레임을 검색 하기 위한 메서드를 포함 합니다.
- **IUserLoginStore**  
 [IUserLoginStore&lt;s e r&gt; ](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.identity.iuserloginstore-1) 외부 인증 공급자를 사용 하도록 설정 하기 위해 구현할 메서드를 정의 합니다. 추가, 제거 및 사용자 로그인 및 로그인 정보에 따라 사용자를 검색 하기 위한 메서드를 검색 하기 위한 메서드를 포함 합니다.
- **IUserRoleStore**  
 [IUserRoleStore&lt;s e r&gt; ](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.identity.iuserrolestore-1) 인터페이스 역할에 사용자에 매핑하기 위해 구현할 메서드를 정의 합니다. 추가, 제거 및 사용자의 역할 및 사용자를 역할에 할당 된 경우를 확인 하는 메서드를 검색 하는 메서드를 포함 합니다.
- **S w**  
 [s w&lt;s e r&gt; ](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.identity.iuserpasswordstore-1) 인터페이스 해시 된 암호를 유지 하기 위해 구현할 메서드를 정의 합니다. 가져오기 및 해시 된 암호와 사용자가 암호를 설정 하는지 여부를 나타내는 방법을 설정 하는 메서드를 포함 합니다.
- **IUserSecurityStampStore**  
 [IUserSecurityStampStore&lt;s e r&gt; ](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.identity.iusersecuritystampstore-1) 인터페이스를 사용자의 계정 정보가 변경 되었는지 여부를 나타내는 보안 스탬프를 사용 하기 위해 구현할 메서드를 정의 합니다. 사용자의 암호 변경 또는 추가 하거나 로그인을 제거 하는 경우이 스탬프 업데이트 됩니다. 가져오기 및 설정의 보안 스탬프에 대 한 메서드를 포함 합니다.
- **T w**  
 [t w&lt;s e r&gt; ](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.identity.iusertwofactorstore-1) 인터페이스 2 단계 인증을 지원 하기 위해 구현할 메서드를 정의 합니다. 가져오기 및 사용자에 대해 2 단계 인증이 사용 되는지 여부를 설정 하는 메서드를 포함 합니다.
- **E r p h**  
 [p h&lt;s e r&gt; ](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.identity.iuserphonenumberstore-1) 인터페이스 사용자 전화 번호를 저장 하기 위해 구현할 메서드를 정의 합니다. 가져오기 및 전화 번호와 전화 번호가 확인 되었는지 여부를 설정 하는 메서드를 포함 합니다.
- **O r e**  
 [o r e&lt;s e r&gt; ](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.identity.iuseremailstore-1) 인터페이스 사용자 전자 메일 주소를 저장 하기 위해 구현할 메서드를 정의 합니다. 가져오기 및 전자 메일 주소 및 전자 메일이 확인 여부를 설정 하는 메서드를 포함 합니다.
- **O r e**  
 [o r e&lt;s e r&gt; ](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.identity.iuserlockoutstore-1) 인터페이스 계정 잠금에 대 한 정보를 저장 하기 위해 구현할 메서드를 정의 합니다. 실패 한 액세스 시도 횟수와 잠금 추적을 위한 메서드를 포함 합니다.
- **I q**  
 [i q&lt;s e r&gt; ](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.identity.iqueryableuserstore-1) 인터페이스 멤버 구현 쿼리 가능한 사용자 저장소를 제공 하도록 정의 합니다.

앱에서 필요한는 인터페이스를 구현 합니다. 예:

```csharp
public class UserStore : IUserStore<IdentityUser>,
                         IUserClaimStore<IdentityUser>,
                         IUserLoginStore<IdentityUser>,
                         IUserRoleStore<IdentityUser>,
                         IUserPasswordStore<IdentityUser>,
                         IUserSecurityStampStore<IdentityUser>
{
    // interface implementations not shown
}
```

### <a name="identityuserclaim-identityuserlogin-and-identityuserrole"></a>IdentityUserClaim, IdentityUserLogin, 및 IdentityUserRole

``Microsoft.AspNet.Identity.EntityFramework`` 네임 스페이스에 구현 된 [IdentityUserClaim](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.identity.entityframeworkcore.identityuserclaim-1), [IdentityUserLogin](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnet.identity.corecompat.identityuserlogin), 및 [IdentityUserRole](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.identity.entityframeworkcore.identityuserrole-1) 클래스입니다. 이러한 기능을 사용 하는 경우에 이러한 클래스의 사용자가 자체 버전을 만들고 응용 프로그램에 대 한 속성을 정의 하는 것이 좋습니다. 그러나 때로는 것이 효율적 기본 작업 (예: 추가 또는 제거 하는 사용자의 클레임)를 수행할 때 이러한 엔터티를 메모리에 로드 하지 않도록 합니다. 대신, 백 엔드 저장소 클래스는 데이터 원본에 직접 이러한 작업을 실행할 수 있습니다. 예를 들어는 ``UserStore.GetClaimsAsync`` 메서드를 호출할 수는 ``userClaimTable.FindByUserId(user.Id)`` 에 대 한 쿼리를 실행 하는 메서드를 직접 테이블 클레임의 목록을 반환 합니다.

## <a name="customize-the-role-class"></a>Role 클래스를 사용자 지정

역할 저장소 공급자를 구현할 때 사용자 지정 역할 유형을 만들 수 있습니다. 특정 인터페이스를 구현 하지 필요 하지만 있어야는 `Id` 갖습니다 일반적으로 `Name` 속성입니다.

다음은 예제 역할 클래스입니다.

[!code-csharp[Main](identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/ApplicationRole.cs)]

## <a name="customize-the-role-store"></a>역할 저장소의 사용자 지정

만들 수는 ``RoleStore`` 역할에 대 한 모든 데이터 작업에 대 한 메서드를 제공 하는 클래스입니다. 이 클래스는 해당 하는 [RoleStore<TRole> ](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.identity.entityframeworkcore.rolestore-1) 클래스입니다. 에 `RoleStore` 구현 클래스는 ``IRoleStore<TRole>`` 하 고 선택적으로 ``IQueryableRoleStore<TRole>`` 인터페이스입니다.

- **IRoleStore&lt;TRole&gt;**  
 [IRoleStore](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.identity.irolestore-1) 인터페이스 역할 저장소 클래스에서 구현 하는 메서드를 정의 합니다. 만들기, 업데이트, 삭제 및 역할을 검색 하는 메서드를 포함 합니다.
- **RoleStore&lt;TRole&gt;**  
 사용자 지정 하려면 `RoleStore`를 구현 하는 클래스를 만들는 `IRoleStore` 인터페이스입니다. 

## <a name="reconfigure-app-to-use-new-storage-provider"></a>새 저장소 공급자를 사용 하도록 응용 프로그램을 다시 구성

저장소 공급자를 구현한 후에 앱 사용을 구성 합니다. 응용 프로그램의 기본 공급자를 사용 하는 경우 사용자 지정 공급자로 바꿉니다.

1. 제거는 `Microsoft.AspNetCore.EntityFramework.Identity` NuGet 패키지 합니다.
1. 저장소 공급자와는 별도 프로젝트 또는 패키지에 있는 경우에 대 한 참조를 추가 합니다.
1. 에 대 한 모든 참조 바꿉니다 `Microsoft.AspNetCore.EntityFramework.Identity` 을 사용 하 여 저장소 공급자의 네임 스페이스에 대 한 문입니다.
1. 에 ``ConfigureServices`` change 메서드에 `AddIdentity` 사용자 지정 형식을 사용 하는 메서드. 이 용도 대 한 확장 메서드를 만들 수 있습니다. 참조 [IdentityServiceCollectionExtensions](https://github.com/aspnet/Identity/blob/rel/1.1.0/src/Microsoft.AspNetCore.Identity/IdentityServiceCollectionExtensions.cs) 예에 대 한 합니다.
1. 역할을 사용 하는 업데이트는 `RoleManager` 사용 하 여 `RoleStore` 클래스입니다.
1. 응용 프로그램의 구성에는 연결 문자열 및 자격 증명을 업데이트 합니다.

예제:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add identity types
    services.AddIdentity<ApplicationUser, ApplicationRole>()
        .AddDefaultTokenProviders();

    // Identity Services
    services.AddTransient<IUserStore<ApplicationUser>, CustomUserStore>();
    services.AddTransient<IRoleStore<ApplicationRole>, CustomRoleStore>();
    string connectionString = Configuration.GetConnectionString("DefaultConnection");
    services.AddTransient<SqlConnection>(e => new SqlConnection(connectionString));
    services.AddTransient<DapperUsersTable>();

    // additional configuration
}
```

## <a name="references"></a>참조

- [ASP.NET Id에 대 한 사용자 지정 저장소 공급자](https://docs.microsoft.com/aspnet/identity/overview/extensibility/overview-of-custom-storage-providers-for-aspnet-identity)
- [ASP.NET Core Id](https://github.com/aspnet/identity) -이 저장소에 저장소 공급자를 유지 관리 하는 커뮤니티에 대 한 링크를 포함 합니다.
