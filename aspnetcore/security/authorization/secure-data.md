---
title: "권한 부여에 의해 보호 되는 사용자 데이터와 ASP.NET Core 응용 프로그램 만들기"
author: rick-anderson
keywords: "ASP.NET Core, MVC, 권한 부여, 역할, 보안, 관리자"
ms.author: riande
manager: wpickett
ms.date: 05/22/2017
ms.topic: article
ms.assetid: abeb2f8e-dfbf-4398-a04c-338a613a65bc
ms.technology: aspnet
ms.prod: aspnet-core
uid: security/authorization/secure-data
ms.openlocfilehash: 036ff9a682dc17ead991c85a9d5dd9c4b6a7d0c7
ms.sourcegitcommit: 6e83c55eb0450a3073ef2b95fa5f5bcb20dbbf89
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/28/2017
---
# <a name="create-an-aspnet-core-app-with-user-data-protected-by-authorization"></a>권한 부여에 의해 보호 되는 사용자 데이터와 ASP.NET Core 응용 프로그램 만들기

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [Joe Audette](https://twitter.com/joeaudette)

이 자습서에는 권한 부여에 의해 보호 되는 사용자 데이터와 웹 응용 프로그램을 만드는 방법을 보여 줍니다. 인증 된 (등록 된) 사용자 연락처 목록을 표시를 만들었습니다. 세 가지 보안 그룹이 있습니다.

* 등록 된 사용자의 승인 된 모든 연락처 데이터를 볼 수 있습니다.
* 등록 된 사용자 수 편집/삭제 자신의 데이터입니다. 
* 관리자 승인 하거나 연락 데이터를 거부할 수 있습니다. 승인 된 연락처에만 사용자에 게 표시 됩니다.
* 관리자 승인/거부 있으며 편집/삭제 된 데이터.

다음 이미지에서는 Rick 사용자 (`rick@example.com`)에 로그인 합니다. 사용자 Rick 수 있는 유일한 보기 승인 연락처 및 그의 연락처 편집/삭제. 마지막 레코드만 Rick에서 만든, 편집 표시 및 링크 삭제

![위에서 설명한 이미지](secure-data/_static/rick.png)

다음 그림에 `manager@contoso.com` 관리자 역할에서 서명 합니다. 

![위에서 설명한 이미지](secure-data/_static/manager1.png)

다음 이미지는 관리자의 연락처 세부 정보 보기를 보여 줍니다.

![위에서 설명한 이미지](secure-data/_static/manager.png)

관리자 및 관리자의 승인 있고 단추를 거부 합니다.

다음 그림에 `admin@contoso.com` 관리자의 역할에서 서명 합니다. 

![위에서 설명한 이미지](secure-data/_static/admin.png)

관리자는 모든 권한을 갖습니다. 그녀는 모든 연락처 읽기/편집/삭제 수 한 연락처의 상태를 변경 했습니다.

응용 프로그램에서 만들어진 [스 캐 폴딩](xref:tutorials/first-mvc-app-xplat/adding-model#scaffold-the-moviecontroller) 다음 `Contact` 모델:

[!code-csharp[Main](secure-data/samples/starter/Models/Contact.cs?name=snippet1)]

A `ContactIsOwnerAuthorizationHandler` 인증 처리기를 사용 하면 사용자가 데이터를 편집할 수만 있습니다. A `ContactManagerAuthorizationHandler` 처리기 권한 부여 관리자 승인 또는 거부 연락처를 허용 합니다.  A `ContactAdministratorsAuthorizationHandler` 을 승인 하거나 거부할 연락처 연락처 편집/삭제 하는 관리자를 사용 하는 인증 처리기입니다. 

## <a name="prerequisites"></a>필수 구성 요소

시작 자습서 아닙니다. 에 대해 잘 알고 있어야 합니다.

* [ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc)
* [Entity Framework Core](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a>시작 및 완료 된 앱

[다운로드](xref:tutorials/index#how-to-download-a-sample) 는 [완료](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/final) 응용 프로그램입니다. [테스트](#test-the-completed-app) 해당 보안 기능에 잘 알고 있으므로 완료 된 앱입니다. 

### <a name="the-starter-app"></a>시작 응용 프로그램

완성 된 샘플을 사용 하 여 코드를 비교 하는 것이 좋습니다.

[다운로드](xref:tutorials/index#how-to-download-a-sample) 는 [스타터](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter) 응용 프로그램입니다. 

참조 [스타터 앱 만들기](#create-the-starter-app) 원하는 처음부터 만들 경우.

데이터베이스를 업데이트 합니다.

```none
   dotnet ef database update
```

응용 프로그램 실행을 탭의 **ContactManager** 링크를 선택한 만들기, 편집 및 연락처를 삭제를 확인 합니다.

이 자습서에는 안전한 사용자 데이터 응용 프로그램을 만드는 모든 주요 단계입니다. 완료 된 프로젝트를 참조 하는 것이 유용할 수 있습니다.

## <a name="modify-the-app-to-secure-user-data"></a>사용자 데이터 보호를 위해 응용 프로그램 수정

다음 섹션에서는 보안 사용자 데이터 응용 프로그램을 만드는 모든 주요 단계 있어야 합니다. 완료 된 프로젝트를 참조 하는 것이 유용할 수 있습니다.

### <a name="tie-the-contact-data-to-the-user"></a>사용자에 게 연락 데이터를 연결 합니다.

ASP.NET을 사용 하 여 [Identity](xref:security/authentication/identity) 가 데이터를 하지만 다른 사용자가 데이터가 아닌 사용자 ID 사용자를 편집할 수 있습니다. 추가 `OwnerID` 에 `Contact` 모델:

[!code-csharp[Main](secure-data/samples/final/Models/Contact.cs?name=snippet1&highlight=5-6,16-)]

`OwnerID`사용자의 id는 `AspNetUser` 테이블에 [Identity](xref:security/authentication/identity) 데이터베이스입니다. `Status` 필드 연락처 일반 사용자가 볼 수 있는지 여부를 결정 합니다. 

새 마이그레이션을 스 캐 폴딩 하 고 데이터베이스를 업데이트 합니다.

```console
dotnet ef migrations add userID_Status
dotnet ef database update
 ```

### <a name="require-ssl-and-authenticated-users"></a>SSL 및 인증 된 사용자가 필요 합니다.

에 `ConfigureServices` 의 메서드는 *Startup.cs* 파일에서 추가 된 [RequireHttpsAttribute](/aspnet/core/api/microsoft.aspnetcore.mvc.requirehttpsattribute) 권한 부여 필터:

[!code-csharp[Main](secure-data/samples/final/Startup.cs?name=snippet_SSL&highlight=1)]

참조를 HTTPS로 HTTP 요청을 리디렉션할 [URL 다시 쓰기 미들웨어](xref:fundamentals/url-rewriting)합니다. Visual Studio 코드를 사용 하 여 테스트 인증서를 SSL에 대 한 포함 되지 않은 로컬 플랫폼에 대 한 테스트 하거나 경우:

- 설정 `"LocalTest:skipSSL": true` 에 *appsettings.json* 파일입니다.

### <a name="require-authenticated-users"></a>인증 된 사용자가 필요 합니다.

사용자를 인증 하도록 요구 하는 기본 인증 정책을 설정 합니다. 인증을 사용 하 여 컨트롤러 또는 동작 메서드에서 옵트아웃을 선택할 수 있습니다는 `[AllowAnonymous]` 특성입니다. 이 접근 방식 추가 된 새 컨트롤러로 자동으로 내용의 포함 하도록 새 컨트롤러에 의존 하는 보다 더 안전 하 게 하는 인증 된 `[Authorize]` 특성입니다. 다음을 추가 `ConfigureServices` 의 메서드는 *Startup.cs* 파일:

[!code-csharp[Main](secure-data/samples/final/Startup.cs?name=snippet_defaultPolicy)]

추가 `[AllowAnonymous]` 익명 사용자가 등록 하기 전에 사이트에 대 한 정보를 가져올 수 있도록 홈 컨트롤러에 있습니다.

[!code-csharp[Main](secure-data/samples/final/Controllers/HomeController.cs?name=snippet1&highlight=2,6)]

### <a name="configure-the-test-account"></a>테스트 계정 구성

`SeedData` 두 계정, 관리자 및 관리자 클래스를 만듭니다. 사용 하 여는 [암호 관리자 도구](xref:security/app-secrets) 이러한 계정에 대 한 암호를 설정 합니다. 프로젝트 디렉터리에서이 작업을 수행 (포함 된 디렉터리 *Program.cs*).

```console
dotnet user-secrets set SeedUserPW <PW>
```

업데이트 `Configure` 테스트 암호를 사용 합니다.

[!code-csharp[Main](secure-data/samples/final/Startup.cs?name=Configure&highlight=19-21)]

관리자 사용자 ID를 추가 하 고 `Status = ContactStatus.Approved` 연락처에 있습니다. 사용자 ID 모든 연락처를 추가 하나만 표시 됩니다.

[!code-csharp[Main](secure-data/samples/final/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a>소유자, 관리자 및 관리자 권한 부여 처리기 만들기

만들기는 `ContactIsOwnerAuthorizationHandler` 클래스에 *권한 부여* 폴더입니다. `ContactIsOwnerAuthorizationHandler` 리소스를 소유 하는 리소스에 대해 작동 하는 사용자를 확인 합니다.

[!code-csharp[Main](secure-data/samples/final/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

`ContactIsOwnerAuthorizationHandler` 호출 `context.Succeed` 현재 인증 된 사용자가 연락처 소유자 인 경우. 권한 부여 처리기는 일반적으로 반환 `context.Succeed` 에서 요구 사항이 충족 합니다. 반환 `Task.FromResult(0)` 요구 사항이 충족 되지 않습니다. `Task.FromResult(0)`성공 또는 실패를 모두는 다른 권한 부여 핸들러를 실행 하도록 허용 합니다. 명시적으로 실패 해야 할 경우 반환 `context.Fail()`합니다.

연락처 소유자가 데이터를 편집/삭제 자신의 고유 데이터 확인 요구 사항 매개 변수에 전달 된 작업을 필요가 없습니다 허용 합니다.

### <a name="create-a-manager-authorization-handler"></a>관리자 권한 부여 처리기 만들기

만들기는 `ContactManagerAuthorizationHandler` 클래스에 *권한 부여* 폴더입니다. `ContactManagerAuthorizationHandler` 리소스에 대해 작동 하는 사용자는 관리자를 확인 합니다. 관리자만 승인 하거나 (새롭거나 변경 된) 콘텐츠 변경 내용을 거부할 수 있습니다.

[!code-csharp[Main](secure-data/samples/final/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a>관리자 권한 부여 처리기 만들기

만들기는 `ContactAdministratorsAuthorizationHandler` 클래스에 *권한 부여* 폴더입니다. `ContactAdministratorsAuthorizationHandler` 리소스에 대해 작동 하는 사용자가 관리자를 확인 합니다. 관리자는 모든 작업을 수행할 수 있습니다.

[!code-csharp[Main](secure-data/samples/final/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a>인증 처리기를 등록 합니다.

Entity Framework Core를 사용 하 여 서비스를 위해 등록 되어야 [종속성 주입](xref:fundamentals/dependency-injection) 를 사용 하 여 [AddScoped](/aspnet/core/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions)합니다. `ContactIsOwnerAuthorizationHandler` ASP.NET Core를 사용 하 여 [Identity](xref:security/authentication/identity), Entity Framework Core 기반입니다. 사용할 수 있도록 서비스 컬렉션에는 처리기를 등록 된 `ContactsController` 통해 [종속성 주입](xref:fundamentals/dependency-injection)합니다. 다음 코드의 끝에 추가 `ConfigureServices`:

[!code-csharp[Main](secure-data/samples/final/Startup.cs?name=AuthorizationHandlers)]

`ContactAdministratorsAuthorizationHandler`및 `ContactManagerAuthorizationHandler` 단일 항목으로 추가 됩니다. Singleton 이들은 하므로 EF를 사용 하지 않아 이며 필요한 모든 정보에는 `Context` 의 매개 변수는 `HandleRequirementAsync` 메서드.

전체 `ConfigureServices`:

[!code-csharp[Main](secure-data/samples/final/Startup.cs?name=ConfigureServices)]

## <a name="update-the-code-to-support-authorization"></a>권한 부여를 지원 하기 위한 코드를 업데이트 합니다.

이 섹션에서는 컨트롤러와 뷰 업데이트 및 작업 요구 사항 클래스를 추가 합니다.

### <a name="update-the-contacts-controller"></a>연락처 컨트롤러를 업데이트 합니다.

업데이트는 `ContactsController` 생성자:

* 추가 `IAuthorizationService` 서비스가 권한 부여 처리기에 액세스할 수 있습니다. 
* 추가 `Identity` `UserManager` 서비스:

[!code-csharp[Main](secure-data/samples/final/Controllers/ContactsController.cs?name=snippet_ContactsControllerCtor)]

### <a name="add-a-contact-operations-requirements-class"></a>연락처 작업 요구 사항 클래스를 추가 합니다.

추가 `ContactOperationsRequirements` 클래스는 *권한 부여* 폴더입니다. 이 클래스는 요구 사항이 응용 프로그램이 지 원하는:

[!code-csharp[Main](secure-data/samples/final/Authorization/ContactOperations.cs)]

### <a name="update-create"></a>업데이트 만들기

업데이트는 `HTTP POST Create` 메서드:

* 사용자 ID를 추가 `Contact` 모델입니다.
* 연락처를 소유 하 고 확인 하려면 권한 부여 처리기를 호출 합니다.

[!code-csharp[Main](secure-data/samples/final/Controllers/ContactsController.cs?name=snippet_Create)]

### <a name="update-edit"></a>업데이트 편집

모두 업데이트 `Edit` 연락처를 담당 하는 사용자를 확인 하 여 권한 부여 처리기를 사용 하는 메서드. 리소스 권한 부여를 수행 하는 것 때문에 사용할 수 없습니다는 `[Authorize]` 특성입니다. 특성 평가 되는 경우 리소스에 대 한 액세스를 아직 합니다. 리소스 기반 권한 부여는 명령적 이어야 합니다. 컨트롤러에서 로드 하거나 자체 처리기 내에서 로드 하 여 리소스에 액세스할 수 있는 검사를 수행 해야 합니다. 자주 리소스의 리소스 키를 전달 하 여 액세스 합니다.

[!code-csharp[Main](secure-data/samples/final/Controllers/ContactsController.cs?name=snippet_Edit)]

### <a name="update-the-delete-method"></a>Delete 메서드를 업데이트 합니다.

모두 업데이트 `Delete` 연락처를 담당 하는 사용자를 확인 하 여 권한 부여 처리기를 사용 하는 메서드.

[!code-csharp[Main](secure-data/samples/final/Controllers/ContactsController.cs?name=snippet_Delete)]

## <a name="inject-the-authorization-service-into-the-views"></a>인증 서비스는 뷰에 삽입

현재 UI에 표시 된 편집한 사용자가을 수정할 수 없는 데이터에 대 한 링크를 삭제 합니다. 권한 부여 처리기 보기에 적용 하 여이 문제를 해결 합니다 했습니다.

권한 부여 서비스를 삽입할는 *Views/_ViewImports.cshtml* 파일 되므로 모든 보기에 사용할 수 있습니다.

[!code-html[Main](secure-data/samples/final/Views/_ViewImports.cshtml)]

업데이트는 *Views/Contacts/Index.cshtml* Razor 뷰를만 편집을 표시 하 고 사용자에 게 수 편집/삭제 연락처에 대 한 링크를 삭제 합니다.

추가`@using ContactManager.Authorization;`

업데이트는 `Edit` 및 `Delete` 하므로 편집 및 연락처를 삭제할 수 있는 권한 가진 사용자에 대 한 렌더링 되는 링크입니다.

[!code-html[Main](secure-data/samples/final/Views/Contacts/Index.cshtml?range=63-84)]

경고: 데이터를 편집 또는 삭제 권한이 없는 사용자 로부터 링크 숨기기 응용 프로그램을 보호 하지 못합니다. 링크 숨기기 사용 하면 응용 프로그램 사용자 친숙 한 유효한 링크를 표시 하 여 있습니다. 사용자가 편집을 호출 하 고 자신이 소유 하지 않는 데이터에 대 한 작업을 삭제 하도록 생성 된 Url 해킹 수 있습니다.  컨트롤러는 액세스 보안 검사를 반복 해야 합니다.

### <a name="update-the-details-view"></a>업데이트 세부 정보 보기

관리자 승인 또는 연락처를 거부할 수 있도록 자세히 보기를 업데이트 합니다.

[!code-html[Main](secure-data/samples/final/Views/Contacts/Details.cshtml?range=53-)]

## <a name="test-the-completed-app"></a>완성 된 응용 프로그램 테스트

Visual Studio 코드를 사용 하 여 테스트 인증서를 SSL에 대 한 포함 되지 않은 로컬 플랫폼에 대 한 테스트 하거나 경우:

- 설정 `"LocalTest:skipSSL": true` 에 *appsettings.json* 파일입니다.

응용 프로그램을 실행 한 경우 연락처가 삭제의 모든 레코드는 `Contact` 테이블 및 데이터베이스를 시드하고에 앱을 다시 시작 합니다. Visual Studio를 사용 하는 경우 끝내 고 시드 데이터베이스에 IIS Express를 다시 시작 해야 합니다.

연락처를 검색 하는 사용자를 등록 합니다.

완성 된 앱을 테스트 하는 쉬운 방법은 세 가지 서로 다른 브라우저 (또는 incognito/InPrivate 버전)를 시작 하려면입니다. 하나의 브라우저에서 새 사용자를 등록, 예를 들어 `test@example.com`합니다. 각 브라우저에 다른 사용자로 로그인 합니다. 다음 사항을 확인합니다.

* 등록 된 사용자의 승인 된 모든 연락처 데이터를 볼 수 있습니다.
* 등록 된 사용자 수 편집/삭제 자신의 데이터입니다. 
* 관리자 승인 하거나 연락 데이터를 거부할 수 있습니다. `Details` 보기 보여줍니다 **승인** 및 **거부** 단추입니다. 
* 관리자 승인/거부 있으며 편집/삭제 된 데이터.

| 사용자| 옵션 |
| ------------ | ---------|
| test@example.com | 수 편집/삭제 자체 데이터 |
| manager@contoso.com | 승인/거부 및 편집/삭제를 데이터 소유할 수 있습니까  |
| admin@contoso.com | 편집/삭제 있고 모든 데이터를 승인/거부 합니다.|

관리자가 브라우저에서 연락처를 만듭니다. Delete에 대 한 URL을 복사 하 고 관리자 연락처에서 편집 합니다. 테스트 사용자는 이러한 작업을 수행할 수를 확인 하려면 테스트 사용자의 브라우저에 다음이 링크를 붙여 넣습니다.

## <a name="create-the-starter-app"></a>시작 응용 프로그램 만들기

시작 응용 프로그램을 만드는 다음이 지침을 따릅니다.

* 만들기는 **ASP.NET Core 웹 응용 프로그램** 를 사용 하 여 [Visual Studio 2017](https://www.visualstudio.com/) "ContactManager" 라는

  * 응용 프로그램을 만들 **개별 사용자 계정**합니다.
  * 네임 스페이스는 샘플에는 네임 스페이스를 사용 하 여 일치 하므로 "ContactManager" 이름을 지정 합니다.

* 다음 추가 `Contact` 모델:

  [!code-csharp[Main](secure-data/samples/starter/Models/Contact.cs?name=snippet1)]

* 스 캐 폴드 된 `Contact` Entity Framework Core를 사용 하 여 모델 및 `ApplicationDbContext` 데이터 컨텍스트. 모든 스 캐 폴딩 기본값을 적용 합니다. 사용 하 여 `ApplicationDbContext` contact 테이블에 클래스 데이터 컨텍스트에 대 한 배치는 [Identity](xref:security/authentication/identity) 데이터베이스입니다. 참조 [모델 추가](xref:tutorials/first-mvc-app/adding-model) 자세한 정보에 대 한 합니다.

* 업데이트는 **ContactManager** 고정는 *Views/Shared/_Layout.cshtml* 에서 파일을 `asp-controller="Home"` 를 `asp-controller="Contacts"` 하므로 눌러는 **ContactManager** 링크 연락처 컨트롤러를 호출 합니다. 원래 태그:

```html
   <a asp-area="" asp-controller="Home" asp-action="Index" class="navbar-brand">ContactManager</a>
   ```

업데이트 된 태그:

```html
   <a asp-area="" asp-controller="Contacts" asp-action="Index" class="navbar-brand">ContactManager</a>
   ```

* 초기 마이그레이션을 스 캐 폴드 하 고 데이터베이스를 업데이트 합니다.

```none
   dotnet ef migrations add initial
   dotnet ef database update
   ```

* 만들기, 편집 및 연락처를 삭제 하 여 앱을 테스트 합니다.

### <a name="seed-the-database"></a>데이터베이스 시드

추가 `SeedData` 클래스는 *데이터* 폴더입니다. 샘플을 다운로드 하는 경우 복사할 수 있습니다는 *SeedData.cs* 파일을 여 *데이터* 시작 프로젝트의 폴더입니다.

[!code-csharp[Main](secure-data/samples/starter/Data/SeedData.cs)]

강조 표시 된 코드의 끝에 추가 된 `Configure` 에서 메서드는 *Startup.cs* 파일:

[!code-csharp[Main](secure-data/samples/starter/Startup.cs?name=Configure&highlight=28-)]

응용 프로그램 데이터베이스 시드 있는지 테스트 합니다. Seed 메서드 DB 연락처의 모든 행이 없는 경우 실행 되지 않습니다.

### <a name="create-a-class-used-in-the-tutorial"></a>자습서에서 사용 되는 클래스 만들기

* 라는 폴더를 만듭니다 *권한 부여*합니다.
* 복사는 *Authorization\ContactOperations.cs* 완료 된 프로젝트 다운로드에서 파일 또는 다음 코드를 복사 합니다.

[!code-csharp[Main](secure-data/samples/final/Authorization/ContactOperations.cs)]

<a name=secure-data-add-resources-label></a>

### <a name="additional-resources"></a>추가 리소스

* [ASP.NET Core 권한 부여 랩](https://github.com/blowdart/AspNetAuthorizationWorkshop)합니다. 이 자습서에 도입 된 보안 기능에이 랩 자세히 알아봅니다.
* [ASP.NET Core에서 권한 부여: 단순, 역할, 클레임 기반 및 사용자 지정](index.md)
* [사용자 지정 정책 기반 권한 부여](policies.md)
