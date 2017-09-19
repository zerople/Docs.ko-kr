---
title: "ASP.NET Core에 Id 소개"
author: rick-anderson
description: "ASP.NET Core 응용 프로그램과 함께 Id를 사용 하 여"
keywords: "ASP.NET Core, Identity, 권한 부여, 보안"
ms.author: riande
manager: wpickett
ms.date: 07/07/2017
ms.topic: article
ms.assetid: cf119f21-1a2b-49a2-b052-547ccb66ee83
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/authentication/identity
ms.openlocfilehash: 0c17daa96bc69dc0b8393811a4dfe0e5dc4a1884
ms.sourcegitcommit: 67f54fabbfa4e3942f5bfe1f8a7fdfe4a7a75358
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/19/2017
---
# <a name="introduction-to-identity-on-aspnet-core"></a><span data-ttu-id="27b38-104">ASP.NET Core에 Id 소개</span><span class="sxs-lookup"><span data-stu-id="27b38-104">Introduction to Identity on ASP.NET Core</span></span>

<span data-ttu-id="27b38-105">여 [Pranav Rastogi](https://github.com/rustd), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra), Jon Galloway [Erik Reitan](https://github.com/Erikre), 및 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="27b38-105">By [Pranav Rastogi](https://github.com/rustd), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra), Jon Galloway, [Erik Reitan](https://github.com/Erikre), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="27b38-106">ASP.NET Core Id는 응용 프로그램에 로그인 기능을 추가할 수 있는 멤버 자격 시스템입니다.</span><span class="sxs-lookup"><span data-stu-id="27b38-106">ASP.NET Core Identity is a membership system which allows you to add login functionality to your application.</span></span> <span data-ttu-id="27b38-107">사용자 계정 및 로그인 사용자 이름으로 만들 수 있습니다 및 암호 또는 Facebook, Google, Microsoft 계정, Twitter 또는 다른 사용자와 같은 외부 로그인 공급자를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="27b38-107">Users can create an account and login with a user name and password or they can use an external login provider such as Facebook, Google, Microsoft Account, Twitter or others.</span></span>

<span data-ttu-id="27b38-108">ASP.NET Core Id 사용자 이름, 암호 및 프로필 데이터를 저장 하는 SQL Server 데이터베이스를 사용 하도록 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="27b38-108">You can configure ASP.NET Core Identity to use a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="27b38-109">또는 사용자 고유의 영구 저장소 예를 들어 Azure 테이블 저장소를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="27b38-109">Alternatively, you can use your own persistent store, for example Azure Table Storage.</span></span> <span data-ttu-id="27b38-110">이 문서에는 Visual Studio는 CLI를 사용 하 여 위한 지침이 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="27b38-110">This document contains instructions for Visual Studio and for using the CLI.</span></span>

## <a name="overview-of-identity"></a><span data-ttu-id="27b38-111">Id의 개요</span><span class="sxs-lookup"><span data-stu-id="27b38-111">Overview of Identity</span></span>

<span data-ttu-id="27b38-112">이 항목에서는 등록, 로그인 하는 기능을 추가 하려면 ASP.NET Core Id를 사용 하 고 로그 아웃 한 사용자 합니다.</span><span class="sxs-lookup"><span data-stu-id="27b38-112">In this topic, you'll learn how to use ASP.NET Core Identity to add functionality to register, log in, and log out a user.</span></span> <span data-ttu-id="27b38-113">ASP.NET Core Id를 사용 하 여 앱을 만드는 방법에 대해 더 자세한 내용은이 문서의 끝에 다음 단계 섹션을 참조 하십시오.</span><span class="sxs-lookup"><span data-stu-id="27b38-113">For more detailed instructions about creating apps using ASP.NET Core Identity, see the Next Steps section at the end of this article.</span></span>

1.  <span data-ttu-id="27b38-114">개별 사용자 계정을 가진 ASP.NET Core 웹 응용 프로그램 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="27b38-114">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

    # <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="27b38-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="27b38-115">Visual Studio</span></span>](#tab/visual-studio)
    <span data-ttu-id="27b38-116">Visual Studio에서 선택 **파일** -> **새로** -> **프로젝트**합니다.</span><span class="sxs-lookup"><span data-stu-id="27b38-116">In Visual Studio, select **File** -> **New** -> **Project**.</span></span> <span data-ttu-id="27b38-117">선택 된 **ASP.NET 웹 응용 프로그램** 에서 **새 프로젝트** 대화 상자.</span><span class="sxs-lookup"><span data-stu-id="27b38-117">Select the **ASP.NET Web Application** from the **New Project** dialog box.</span></span> <span data-ttu-id="27b38-118">ASP.NET Core를 선택 하면 **웹 응용 프로그램** 와 **개별 사용자 계정** 인증 방법으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="27b38-118">Selecting an ASP.NET Core **Web Application** with **Individual User Accounts** as the authentication method.</span></span>

    <span data-ttu-id="27b38-119">참고: 선택 해야 **개별 사용자 계정**합니다.</span><span class="sxs-lookup"><span data-stu-id="27b38-119">Note: You must select **Individual User Accounts**.</span></span>
 
    ![새 프로젝트 대화 상자](identity/_static/01-mvc.png)
    
    # <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="27b38-121">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="27b38-121">.NET Core CLI</span></span>](#tab/netcore-cli)
    <span data-ttu-id="27b38-122">.NET Core CLI를 사용 하는 경우 사용 하 여 새 프로젝트 만들기 ``dotnet new mvc --auth Individual``합니다.</span><span class="sxs-lookup"><span data-stu-id="27b38-122">If using the .NET Core CLI, create the new project using ``dotnet new mvc --auth Individual``.</span></span> <span data-ttu-id="27b38-123">Visual Studio 만듭니다 Identity 템플릿 코드는 새 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="27b38-123">This will create a new project with the same Identity template code Visual Studio creates.</span></span>
 
    <span data-ttu-id="27b38-124">만든된 프로젝트에 포함 되어는 `Microsoft.AspNetCore.Identity.EntityFrameworkCore` Id 데이터 및 스키마를 사용 하 여 SQL Server를 유지 하는 패키지 [Entity Framework Core](https://docs.microsoft.com/ef/)합니다.</span><span class="sxs-lookup"><span data-stu-id="27b38-124">The created project contains the `Microsoft.AspNetCore.Identity.EntityFrameworkCore` package, which will persist the Identity data and schema to SQL Server using [Entity Framework Core](https://docs.microsoft.com/ef/).</span></span>
    
    ---
 
2.  <span data-ttu-id="27b38-125">Id 서비스를 구성 하 고 미들웨어에서 추가 `Startup`합니다.</span><span class="sxs-lookup"><span data-stu-id="27b38-125">Configure Identity services and add middleware in `Startup`.</span></span>

    <span data-ttu-id="27b38-126">Id 서비스에서 응용 프로그램에 추가 되는 `ConfigureServices` 에서 메서드는 `Startup` 클래스:</span><span class="sxs-lookup"><span data-stu-id="27b38-126">The Identity services are added to the application in the `ConfigureServices` method in the `Startup` class:</span></span>

    # <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="27b38-127">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="27b38-127">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)
    
    [!code-csharp[Main](identity/sample/src/ASPNETv2-IdentityDemo/Startup.cs?name=snippet_configureservices&highlight=7-9,11-28,30-39)]
    
    <span data-ttu-id="27b38-128">이러한 서비스를 통해 응용 프로그램에서 사용할 수 [종속성 주입](xref:fundamentals/dependency-injection)합니다.</span><span class="sxs-lookup"><span data-stu-id="27b38-128">These services are made available to the application through [dependency injection](xref:fundamentals/dependency-injection).</span></span>
    
    <span data-ttu-id="27b38-129">Identity를 호출 하 여 응용 프로그램에 대해 사용할 `UseAuthentication` 에 `Configure` 메서드.</span><span class="sxs-lookup"><span data-stu-id="27b38-129">Identity is enabled for the application by calling `UseAuthentication` in the `Configure` method.</span></span> <span data-ttu-id="27b38-130">`UseAuthentication`인증 추가 [미들웨어](xref:fundamentals/middleware) 요청 파이프라인을 합니다.</span><span class="sxs-lookup"><span data-stu-id="27b38-130">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware) to the request pipeline.</span></span>
    
    [!code-csharp[Main](identity/sample/src/ASPNETv2-IdentityDemo/Startup.cs?name=snippet_configure&highlight=17)]
    
    # <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="27b38-131">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="27b38-131">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)
    
    [!code-csharp[Main](identity/sample/src/ASPNET-IdentityDemo/Startup.cs?name=snippet_configureservices&highlight=7-9,13-34)]
    
    <span data-ttu-id="27b38-132">이러한 서비스를 통해 응용 프로그램에서 사용할 수 [종속성 주입](xref:fundamentals/dependency-injection)합니다.</span><span class="sxs-lookup"><span data-stu-id="27b38-132">These services are made available to the application through [dependency injection](xref:fundamentals/dependency-injection).</span></span>
    
    <span data-ttu-id="27b38-133">Identity를 호출 하 여 응용 프로그램에 대해 사용할 `UseIdentity` 에 `Configure` 메서드.</span><span class="sxs-lookup"><span data-stu-id="27b38-133">Identity is enabled for the application by calling `UseIdentity` in the `Configure` method.</span></span> <span data-ttu-id="27b38-134">`UseIdentity`쿠키 기반 인증 추가 [미들웨어](xref:fundamentals/middleware) 요청 파이프라인을 합니다.</span><span class="sxs-lookup"><span data-stu-id="27b38-134">`UseIdentity` adds cookie-based authentication [middleware](xref:fundamentals/middleware) to the request pipeline.</span></span>
        
    [!code-csharp[Main](identity/sample/src/ASPNET-IdentityDemo/Startup.cs?name=snippet_configure&highlight=21)]
    
    ---
     
    <span data-ttu-id="27b38-135">프로세스를 응용 프로그램 시작에 대 한 자세한 내용은 참조 [응용 프로그램 시작](xref:fundamentals/startup)합니다.</span><span class="sxs-lookup"><span data-stu-id="27b38-135">For more information about the application start up process, see [Application Startup](xref:fundamentals/startup).</span></span>

3.  <span data-ttu-id="27b38-136">사용자를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="27b38-136">Create a user.</span></span>

    <span data-ttu-id="27b38-137">응용 프로그램을 시작 하 고을 클릭는 **등록** 링크 합니다.</span><span class="sxs-lookup"><span data-stu-id="27b38-137">Launch the application and then click on the **Register** link.</span></span>

    <span data-ttu-id="27b38-138">처음이이 작업을 수행 하는 경우 마이그레이션을 실행 하는 데 필요한 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="27b38-138">If this is the first time you're performing this action, you may be required to run migrations.</span></span> <span data-ttu-id="27b38-139">응용 프로그램 하 라는 메시지가 표시 **적용 마이그레이션**:</span><span class="sxs-lookup"><span data-stu-id="27b38-139">The application prompts you to **Apply Migrations**:</span></span>
    
    ![마이그레이션 웹 페이지를 적용 합니다.](identity/_static/apply-migrations.png)
    
    <span data-ttu-id="27b38-141">또는 영구 데이터베이스 없이 응용 프로그램과 함께 ASP.NET Core Id를 사용 하 여 메모리 내 데이터베이스를 사용 하 여 테스트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="27b38-141">Alternately, you can test using ASP.NET Core Identity with your app without a persistent database by using an in-memory database.</span></span> <span data-ttu-id="27b38-142">메모리 내 데이터베이스를 사용 하려면 추가 ``Microsoft.EntityFrameworkCore.InMemory`` 앱 패키지 및 응용 프로그램의 호출을 수정 ``AddDbContext`` 에 ``ConfigureServices`` 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="27b38-142">To use an in-memory database, add the ``Microsoft.EntityFrameworkCore.InMemory`` package to your app and modify your app's call to ``AddDbContext`` in ``ConfigureServices`` as follows:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseInMemoryDatabase(Guid.NewGuid().ToString()));
    ```
    
    <span data-ttu-id="27b38-143">사용자가 클릭할 때는 **등록** 링크는 ``Register`` 에서 동작이 호출 될 ``AccountController``합니다.</span><span class="sxs-lookup"><span data-stu-id="27b38-143">When the user clicks the **Register** link, the ``Register`` action is invoked on ``AccountController``.</span></span> <span data-ttu-id="27b38-144">``Register`` 동작 호출 하 여 사용자를 만듭니다 `CreateAsync` 에 `_userManager` 개체 (제공 된 ``AccountController`` 종속성 주입 하 여):</span><span class="sxs-lookup"><span data-stu-id="27b38-144">The ``Register`` action creates the user by calling `CreateAsync` on the  `_userManager` object (provided to ``AccountController`` by dependency injection):</span></span>
 
    [!code-csharp[Main](identity/sample/src/ASPNET-IdentityDemo/Controllers/AccountController.cs?name=snippet_register&highlight=11)]

    <span data-ttu-id="27b38-145">에 대 한 호출 사용자가 로그인 된 사용자가 성공적으로 만들어진 경우 ``_signInManager.SignInAsync``합니다.</span><span class="sxs-lookup"><span data-stu-id="27b38-145">If the user was created successfully, the user is logged in by the call to ``_signInManager.SignInAsync``.</span></span>

    <span data-ttu-id="27b38-146">**참고:** 참조 [계정 확인](xref:security/authentication/accconfirm#prevent-login-at-registration) 등록 시 즉시 로그인을 방지 하는 단계에 대 한 합니다.</span><span class="sxs-lookup"><span data-stu-id="27b38-146">**Note:** See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>
 
4.  <span data-ttu-id="27b38-147">로그인.</span><span class="sxs-lookup"><span data-stu-id="27b38-147">Log in.</span></span>
 
    <span data-ttu-id="27b38-148">사용자가 클릭 하 여 로그인 할 수는 **로그인** 사이트의 맨 위에 링크 될 수 있습니다 이동 하 게 될 로그인 페이지에 권한 부여를 요구 하는 사이트의 일부에 액세스 하려는 경우 또는 합니다.</span><span class="sxs-lookup"><span data-stu-id="27b38-148">Users can sign in by clicking the **Log in** link at the top of the site, or they may be navigated to the Login page if they attempt to access a part of the site that requires authorization.</span></span> <span data-ttu-id="27b38-149">사용자가 로그인 페이지에 폼을 제출는 ``AccountController`` ``Login`` 작업을 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="27b38-149">When the user submits the form on the Login page, the ``AccountController`` ``Login`` action is called.</span></span>

    [!code-csharp[Main](identity/sample/src/ASPNET-IdentityDemo/Controllers/AccountController.cs?name=snippet_login&highlight=13-14)]
 
    <span data-ttu-id="27b38-150">``Login`` 동작 호출 ``PasswordSignInAsync`` 에 ``_signInManager`` 개체 (제공 된 ``AccountController`` 종속성 주입 하 여).</span><span class="sxs-lookup"><span data-stu-id="27b38-150">The ``Login`` action calls ``PasswordSignInAsync`` on the ``_signInManager`` object (provided to ``AccountController`` by dependency injection).</span></span>
 
    <span data-ttu-id="27b38-151">기본 ``Controller`` 클래스가 노출 한 ``User`` 컨트롤러 메서드에서 액세스할 수 있는 속성입니다.</span><span class="sxs-lookup"><span data-stu-id="27b38-151">The base ``Controller`` class exposes a ``User`` property that you can access from controller methods.</span></span> <span data-ttu-id="27b38-152">예를 들어, 열거할 수 있습니다 `User.Claims` 을 권한 부여 결정을 내립니다.</span><span class="sxs-lookup"><span data-stu-id="27b38-152">For instance, you can enumerate `User.Claims` and make authorization decisions.</span></span> <span data-ttu-id="27b38-153">자세한 내용은 참조 [권한 부여](xref:security/authorization/index)합니다.</span><span class="sxs-lookup"><span data-stu-id="27b38-153">For more information, see [Authorization](xref:security/authorization/index).</span></span>
 
5.  <span data-ttu-id="27b38-154">로그 아웃 합니다.</span><span class="sxs-lookup"><span data-stu-id="27b38-154">Log out.</span></span>
 
    <span data-ttu-id="27b38-155">클릭 하 고 **로그 아웃** 호출 연결는 `LogOut` 동작 합니다.</span><span class="sxs-lookup"><span data-stu-id="27b38-155">Clicking the **Log out** link calls the `LogOut` action.</span></span>
 
    [!code-csharp[Main](identity/sample/src/ASPNET-IdentityDemo/Controllers/AccountController.cs?name=snippet_logout&highlight=7)]
 
    <span data-ttu-id="27b38-156">앞의 코드 호출 위에 `_signInManager.SignOutAsync` 메서드.</span><span class="sxs-lookup"><span data-stu-id="27b38-156">The preceding code above calls the `_signInManager.SignOutAsync` method.</span></span> <span data-ttu-id="27b38-157">`SignOutAsync` 메서드 쿠키에 저장 하는 사용자의 클레임을 지웁니다.</span><span class="sxs-lookup"><span data-stu-id="27b38-157">The `SignOutAsync` method clears the user's claims stored in a cookie.</span></span>
 
6.  <span data-ttu-id="27b38-158">구성입니다.</span><span class="sxs-lookup"><span data-stu-id="27b38-158">Configuration.</span></span>

    <span data-ttu-id="27b38-159">Id는 응용 프로그램의 시작 클래스에서 재정의할 수 있는 몇 가지 기본 동작에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="27b38-159">Identity has some default behaviors that you can override in your application's startup class.</span></span> <span data-ttu-id="27b38-160">구성 하지 않아도 ``IdentityOptions`` 기본 동작을 사용 하는 경우.</span><span class="sxs-lookup"><span data-stu-id="27b38-160">You do not need to configure ``IdentityOptions`` if you are using the default behaviors.</span></span>

    # <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="27b38-161">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="27b38-161">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)
    
    [!code-csharp[Main](identity/sample/src/ASPNETv2-IdentityDemo/Startup.cs?name=snippet_configureservices&highlight=7-9,11-28,30-39)]
    
    # <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="27b38-162">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="27b38-162">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)
    
    [!code-csharp[Main](identity/sample/src/ASPNET-IdentityDemo/Startup.cs?name=snippet_configureservices&highlight=13-34)]

    ---
    
    <span data-ttu-id="27b38-163">Id를 구성 하는 방법에 대 한 자세한 내용은 참조 [구성 Identity](xref:security/authentication/identity-configuration)합니다.</span><span class="sxs-lookup"><span data-stu-id="27b38-163">For more information about how to configure Identity, see [Configure Identity](xref:security/authentication/identity-configuration).</span></span>
    
    <span data-ttu-id="27b38-164">기본 키의 데이터 형식을 구성할 수 참조 [Id 구성 기본 키 데이터 형식이](xref:security/authentication/identity-primary-key-configuration)합니다.</span><span class="sxs-lookup"><span data-stu-id="27b38-164">You also can configure the data type of the primary key, see [Configure Identity primary keys data type](xref:security/authentication/identity-primary-key-configuration).</span></span>
 
7.  <span data-ttu-id="27b38-165">데이터베이스를 봅니다.</span><span class="sxs-lookup"><span data-stu-id="27b38-165">View the database.</span></span>

    <span data-ttu-id="27b38-166">앱 (Windows와 Visual Studio 사용자에 대 한 기본값)는 SQL Server 데이터베이스를 사용 하는 경우에 만든 응용 프로그램 데이터베이스를 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="27b38-166">If your app is using a SQL Server database (the default on Windows and for Visual Studio users), you can view the database the app created.</span></span> <span data-ttu-id="27b38-167">사용할 수 있습니다 **SQL Server Management Studio**합니다.</span><span class="sxs-lookup"><span data-stu-id="27b38-167">You can use **SQL Server Management Studio**.</span></span> <span data-ttu-id="27b38-168">또는 Visual Studio에서 선택 **보기** -> **SQL Server 개체 탐색기**합니다.</span><span class="sxs-lookup"><span data-stu-id="27b38-168">Alternatively, from Visual Studio, select **View** -> **SQL Server Object Explorer**.</span></span> <span data-ttu-id="27b38-169">연결할 **(localdb) \MSSQLLocalDB**합니다.</span><span class="sxs-lookup"><span data-stu-id="27b38-169">Connect to **(localdb)\MSSQLLocalDB**.</span></span> <span data-ttu-id="27b38-170">일치 하는 이름을 사용 하 여 데이터베이스 * *aspnet-<*프로젝트의 이름*>-<*날짜 문자열*> * * 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="27b38-170">The database with a name matching **aspnet-<*name of your project*>-<*date string*>** is displayed.</span></span>

    ![AspNetUsers 데이터베이스 테이블에 대 한 상황에 맞는 메뉴](identity/_static/04-db.png)
    
    <span data-ttu-id="27b38-172">데이터베이스 확장 및 해당 **테이블**를 마우스 오른쪽 단추로 클릭 한 다음는 **dbo입니다. AspNetUsers** 테이블을 선택한 **데이터 보기**합니다.</span><span class="sxs-lookup"><span data-stu-id="27b38-172">Expand the database and its **Tables**, then right-click the **dbo.AspNetUsers** table and select **View Data**.</span></span>

## <a name="identity-components"></a><span data-ttu-id="27b38-173">Identity 구성 요소</span><span class="sxs-lookup"><span data-stu-id="27b38-173">Identity Components</span></span>

<span data-ttu-id="27b38-174">Id 시스템에 대 한 기본 참조 어셈블리는 `Microsoft.AspNetCore.Identity`합니다.</span><span class="sxs-lookup"><span data-stu-id="27b38-174">The primary reference assembly for the Identity system is `Microsoft.AspNetCore.Identity`.</span></span> <span data-ttu-id="27b38-175">이 패키지 ASP.NET Core Id에 대 한 인터페이스의 핵심 집합으로 포함 되어 있으며 `Microsoft.AspNetCore.Identity.EntityFrameworkCore`합니다.</span><span class="sxs-lookup"><span data-stu-id="27b38-175">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

<span data-ttu-id="27b38-176">이러한 종속성 ASP.NET Core 응용 프로그램의 Id 시스템을 사용 하려면 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="27b38-176">These dependencies are needed to use the Identity system in ASP.NET Core applications:</span></span>

* <span data-ttu-id="27b38-177">`Microsoft.AspNetCore.Identity.EntityFrameworkCore`-Identity Entity Framework Core 사용 시 필요한 형식을 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="27b38-177">`Microsoft.AspNetCore.Identity.EntityFrameworkCore` - Contains the required types to use Identity with Entity Framework Core.</span></span>

* <span data-ttu-id="27b38-178">`Microsoft.EntityFrameworkCore.SqlServer`-Entity Framework Core는 SQL Server와 같은 관계형 데이터베이스에 대 한 Microsoft의 권장 되는 데이터 액세스 기술입니다.</span><span class="sxs-lookup"><span data-stu-id="27b38-178">`Microsoft.EntityFrameworkCore.SqlServer` - Entity Framework Core is Microsoft's recommended data access technology for relational databases like SQL Server.</span></span> <span data-ttu-id="27b38-179">테스트를 위해 사용할 수 있습니다 `Microsoft.EntityFrameworkCore.InMemory`합니다.</span><span class="sxs-lookup"><span data-stu-id="27b38-179">For testing, you can use `Microsoft.EntityFrameworkCore.InMemory`.</span></span>

* <span data-ttu-id="27b38-180">`Microsoft.AspNetCore.Authentication.Cookies`-앱이 쿠키 기반 인증을 사용할 수 있도록 하는 미들웨어입니다.</span><span class="sxs-lookup"><span data-stu-id="27b38-180">`Microsoft.AspNetCore.Authentication.Cookies` - Middleware that enables an app to use cookie-based authentication.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="27b38-181">ASP.NET Core Id로 마이그레이션</span><span class="sxs-lookup"><span data-stu-id="27b38-181">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="27b38-182">추가 정보 및 기존 본인 마이그레이션하는 방법에 대 한 지침을 참조 저장 [마이그레이션 인증 및 Id](xref:migration/identity)합니다.</span><span class="sxs-lookup"><span data-stu-id="27b38-182">For additional information and guidance on migrating your existing Identity store see [Migrating Authentication and Identity](xref:migration/identity).</span></span>

## <a name="next-steps"></a><span data-ttu-id="27b38-183">다음 단계</span><span class="sxs-lookup"><span data-stu-id="27b38-183">Next Steps</span></span>

* [<span data-ttu-id="27b38-184">인증 및 ID 마이그레이션</span><span class="sxs-lookup"><span data-stu-id="27b38-184">Migrating Authentication and Identity</span></span>](xref:migration/identity)
* [<span data-ttu-id="27b38-185">계정 확인 및 암호 복구</span><span class="sxs-lookup"><span data-stu-id="27b38-185">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="27b38-186">SMS를 사용한 2단계 인증</span><span class="sxs-lookup"><span data-stu-id="27b38-186">Two-factor authentication with SMS</span></span>](xref:security/authentication/2fa)
* [<span data-ttu-id="27b38-187">Facebook, Google 및 기타 외부 공급자를 통해 인증 사용</span><span class="sxs-lookup"><span data-stu-id="27b38-187">Enabling authentication using Facebook, Google and other external providers</span></span>](xref:security/authentication/social/index)
