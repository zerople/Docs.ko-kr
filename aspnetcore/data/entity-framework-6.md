---
title: "ASP.NET Core 및 Entity Framework 6 시작"
author: tdykstra
description: "이 문서에는 ASP.NET Core 응용 프로그램에서 Entity Framework 6을 사용 하는 방법을 보여 줍니다."
keywords: ASP.NET Core, Entity Framework EF 6
ms.author: tdykstra
manager: wpickett
ms.date: 02/24/2017
ms.topic: article
ms.assetid: 016cc836-4c43-45a4-b9a7-9efaf53350df
ms.technology: aspnet
ms.prod: asp.net-core
uid: data/entity-framework-6
ms.openlocfilehash: e186568e27c067e29985b8a286e26b87c3186ac4
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/11/2017
---
# <a name="getting-started-with-aspnet-core-and-entity-framework-6"></a><span data-ttu-id="e27ff-104">ASP.NET Core 및 Entity Framework 6 시작</span><span class="sxs-lookup"><span data-stu-id="e27ff-104">Getting started with ASP.NET Core and Entity Framework 6</span></span>

<span data-ttu-id="e27ff-105">여 [Paweł Grudzień](https://github.com/pgrudzien12), [Damien Pontifex](https://github.com/DamienPontifex), 및 [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="e27ff-105">By [Paweł Grudzień](https://github.com/pgrudzien12), [Damien Pontifex](https://github.com/DamienPontifex), and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="e27ff-106">이 문서에는 ASP.NET Core 응용 프로그램에서 Entity Framework 6을 사용 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="e27ff-106">This article shows how to use Entity Framework 6 in an ASP.NET Core application.</span></span>

## <a name="overview"></a><span data-ttu-id="e27ff-107">개요</span><span class="sxs-lookup"><span data-stu-id="e27ff-107">Overview</span></span>

<span data-ttu-id="e27ff-108">Entity Framework 6을 사용 하려면 Entity Framework 6에서는.NET Core를 지원 하지 않으므로.NET Framework에 대해 컴파일할 수 프로젝트에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e27ff-108">To use Entity Framework 6, your project has to compile against .NET Framework, as Entity Framework 6 does not support .NET Core.</span></span> <span data-ttu-id="e27ff-109">로 업그레이드 해야 플랫폼 기능이 필요 하면 [Entity Framework Core](https://docs.efproject.net)합니다.</span><span class="sxs-lookup"><span data-stu-id="e27ff-109">If you need cross-platform features you will need to upgrade to [Entity Framework Core](https://docs.efproject.net).</span></span>

<span data-ttu-id="e27ff-110">ASP.NET Core 응용 프로그램에서 Entity Framework 6을 사용 하는 권장된 방법은 EF6 컨텍스트를 넣을 수 이며 클래스 라이브러리에 모델 클래스를 프로젝트 대상으로 하는 전체 프레임 워크.</span><span class="sxs-lookup"><span data-stu-id="e27ff-110">The recommended way to use Entity Framework 6 in an ASP.NET Core application is to put the EF6 context and model classes in a class library project that targets the full framework.</span></span> <span data-ttu-id="e27ff-111">ASP.NET Core 프로젝트에서 클래스 라이브러리에 대 한 참조를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="e27ff-111">Add a reference to the class library from the ASP.NET Core project.</span></span> <span data-ttu-id="e27ff-112">샘플을 참조 하십시오 [EF6 및 ASP.NET Core 프로젝트가 있는 Visual Studio 솔루션](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/entity-framework-6/sample/)합니다.</span><span class="sxs-lookup"><span data-stu-id="e27ff-112">See the sample [Visual Studio solution with EF6 and ASP.NET Core projects](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/entity-framework-6/sample/).</span></span>

<span data-ttu-id="e27ff-113">.NET Core 프로젝트 EF6와 같은 명령 하는 기능의 일부를 지원 하지 않으므로 EF6 컨텍스트 ASP.NET Core 프로젝트에 넣을 수 없습니다 *Enable-migrations* 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="e27ff-113">You can't put an EF6 context in an ASP.NET Core project because .NET Core projects don't support all of the functionality that EF6 commands such as *Enable-Migrations* require.</span></span>

<span data-ttu-id="e27ff-114">EF6 컨텍스트 찾았으면 프로젝트 형식에 관계 없이 EF6 명령줄 도구만는 EF6 컨텍스트를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="e27ff-114">Regardless of project type in which you locate your EF6 context, only EF6 command-line tools work with an EF6 context.</span></span> <span data-ttu-id="e27ff-115">예를 들어 `Scaffold-DbContext` 은 Entity Framework Core에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e27ff-115">For example, `Scaffold-DbContext` is only available in Entity Framework Core.</span></span> <span data-ttu-id="e27ff-116">리버스 엔지니어링 데이터베이스의 이름을 EF6 모델에 수행 해야 할 경우 참조 [Code First는 기존 데이터베이스에](https://msdn.microsoft.com/jj200620)합니다.</span><span class="sxs-lookup"><span data-stu-id="e27ff-116">If you need to do reverse engineering of a database into an EF6 model, see [Code First to an Existing Database](https://msdn.microsoft.com/jj200620).</span></span>

## <a name="reference-full-framework-and-ef6-in-the-aspnet-core-project"></a><span data-ttu-id="e27ff-117">전체 프레임 워크 참조 및 ASP.NET Core 프로젝트의 EF6</span><span class="sxs-lookup"><span data-stu-id="e27ff-117">Reference full framework and EF6 in the ASP.NET Core project</span></span>

<span data-ttu-id="e27ff-118">ASP.NET Core 프로젝트는.NET framework 및 EF6 참조 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e27ff-118">Your ASP.NET Core project needs to reference .NET framework and EF6.</span></span> <span data-ttu-id="e27ff-119">예를 들어는 *.csproj* ASP.NET Core 프로젝트의 파일은 다음 예제와 같이 표시 됩니다 (파일의 관련 부분에만 표시 됨).</span><span class="sxs-lookup"><span data-stu-id="e27ff-119">For example, the *.csproj* file of your ASP.NET Core project will look similar to the following example (only relevant parts of the file are shown).</span></span>

[!code-xml[](entity-framework-6/sample/MVCCore/MVCCore.csproj?range=3-9&highlight=2)]

<span data-ttu-id="e27ff-120">새 프로젝트를 만드는 경우 사용 하 여는 **ASP.NET Core 웹 응용 프로그램 (.NET Framework)** 서식 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="e27ff-120">If you’re creating a new project, use the **ASP.NET Core Web Application (.NET Framework)** template.</span></span>

## <a name="handle-connection-strings"></a><span data-ttu-id="e27ff-121">연결 문자열을 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="e27ff-121">Handle connection strings</span></span>

<span data-ttu-id="e27ff-122">EF6 명령줄 도구는 EF6 클래스 라이브러리 프로젝트에서 사용할 컨텍스트를 인스턴스화할 수 있도록 기본 생성자가 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="e27ff-122">The EF6 command-line tools that you'll use in the EF6 class library project require a default constructor so they can instantiate the context.</span></span> <span data-ttu-id="e27ff-123">하지만 생성자를 사용 하는 경우 ASP.NET Core 프로젝트에 사용자 컨텍스트 연결 문자열에는 연결 문자열에 전달할 수 있도록 하는 매개 변수 있어야 하도록 지정 하 고 싶을 것입니다.</span><span class="sxs-lookup"><span data-stu-id="e27ff-123">But you'll probably want to specify the connection string to use in the ASP.NET Core project, in which case your context constructor must have a parameter that lets you pass in the connection string.</span></span> <span data-ttu-id="e27ff-124">예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="e27ff-124">Here's an example.</span></span>

[!code-csharp[](entity-framework-6/sample/EF6/SchoolContext.cs?name=snippet_Constructor)]

<span data-ttu-id="e27ff-125">EF6 프로젝트의 구현을 제공 하기에 EF6 컨텍스트 매개 변수가 없는 생성자에 한 [IDbContextFactory](https://msdn.microsoft.com/library/hh506876)합니다.</span><span class="sxs-lookup"><span data-stu-id="e27ff-125">Since your EF6 context doesn't have a parameterless constructor, your EF6 project has to provide an implementation of [IDbContextFactory](https://msdn.microsoft.com/library/hh506876).</span></span> <span data-ttu-id="e27ff-126">EF6 명령줄 도구 찾기 및 컨텍스트를 인스턴스화할 수 있도록 해당 구현을 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e27ff-126">The EF6 command-line tools will find and use that implementation so they can instantiate the context.</span></span> <span data-ttu-id="e27ff-127">예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="e27ff-127">Here's an example.</span></span>

[!code-csharp[](entity-framework-6/sample/EF6/SchoolContextFactory.cs?name=snippet_IDbContextFactory)]

<span data-ttu-id="e27ff-128">이 샘플 코드는 `IDbContextFactory` 구현 하드 코드 된 연결 문자열에 전달 합니다.</span><span class="sxs-lookup"><span data-stu-id="e27ff-128">In this sample code, the `IDbContextFactory` implementation passes in a hard-coded connection string.</span></span> <span data-ttu-id="e27ff-129">명령줄 도구에서 사용할 연결 문자열입니다.</span><span class="sxs-lookup"><span data-stu-id="e27ff-129">This is the connection string that the command-line tools will use.</span></span> <span data-ttu-id="e27ff-130">클래스 라이브러리를 사용 하 여 호출 응용 프로그램이 동일한 연결 문자열을 사용 하도록 전략을 구현 합니다.</span><span class="sxs-lookup"><span data-stu-id="e27ff-130">You'll want to implement a strategy to ensure that the class library uses the same connection string that the calling application uses.</span></span> <span data-ttu-id="e27ff-131">예를 들어 두 프로젝트 모두에서 환경 변수에서 값을 얻을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e27ff-131">For example, you could get the value from an environment variable in both projects.</span></span>

## <a name="set-up-dependency-injection-in-the-aspnet-core-project"></a><span data-ttu-id="e27ff-132">ASP.NET Core 프로젝트의 종속성 주입 설정</span><span class="sxs-lookup"><span data-stu-id="e27ff-132">Set up dependency injection in the ASP.NET Core project</span></span>

<span data-ttu-id="e27ff-133">핵심 프로젝트에서 *Startup.cs* 종속성 주입 (DI)에 대 한 EF6 컨텍스트를 설정 하는 파일 `ConfigureServices`합니다.</span><span class="sxs-lookup"><span data-stu-id="e27ff-133">In the Core project's *Startup.cs* file, set up the EF6 context for dependency injection (DI) in `ConfigureServices`.</span></span> <span data-ttu-id="e27ff-134">EF 컨텍스트 개체는 요청 수명에 대 한 범위 지정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e27ff-134">EF context objects should be scoped for a per-request lifetime.</span></span>

[!code-csharp[](entity-framework-6/sample/MVCCore/Startup.cs?name=snippet_ConfigureServices&highlight=5)]

<span data-ttu-id="e27ff-135">그런 다음 DI를 사용 하 여 컨트롤러에 인스턴스 컨텍스트를 가져올 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e27ff-135">You can then get an instance of the context in your controllers by using DI.</span></span> <span data-ttu-id="e27ff-136">코드는 EF 코어 컨텍스트에 대 한 쓰기는 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="e27ff-136">The code is similar to what you'd write for an EF Core context:</span></span>

[!code-csharp[](entity-framework-6/sample/MVCCore/Controllers/StudentsController.cs?name=snippet_ContextInController)]

## <a name="sample-application"></a><span data-ttu-id="e27ff-137">샘플 응용 프로그램</span><span class="sxs-lookup"><span data-stu-id="e27ff-137">Sample application</span></span>

<span data-ttu-id="e27ff-138">작업 샘플 응용 프로그램에 대 한 참조는 [샘플 Visual Studio 솔루션](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/entity-framework-6/sample/) 이 문서와 함께 제공 되는 합니다.</span><span class="sxs-lookup"><span data-stu-id="e27ff-138">For a working sample application, see the [sample Visual Studio solution](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/entity-framework-6/sample/) that accompanies this article.</span></span>

<span data-ttu-id="e27ff-139">이 샘플은 Visual Studio에서 다음 단계에 따라 처음부터 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e27ff-139">This sample can be created from scratch by the following steps in Visual Studio:</span></span>

* <span data-ttu-id="e27ff-140">솔루션을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="e27ff-140">Create a solution.</span></span>

* <span data-ttu-id="e27ff-141">**새 프로젝트 추가 > 웹 > ASP.NET Core 웹 응용 프로그램 (.NET Framework)**</span><span class="sxs-lookup"><span data-stu-id="e27ff-141">**Add New Project > Web > ASP.NET Core Web Application (.NET Framework)**</span></span>

* <span data-ttu-id="e27ff-142">**새 프로젝트 추가 > 클래식 Windows 데스크톱 > 클래스 라이브러리 (.NET Framework)**</span><span class="sxs-lookup"><span data-stu-id="e27ff-142">**Add New Project > Windows Classic Desktop > Class Library (.NET Framework)**</span></span>

* <span data-ttu-id="e27ff-143">**패키지 관리자 콘솔** (PMC) 두 프로젝트 모두에 대 한 명령을 실행 `Install-Package Entityframework`합니다.</span><span class="sxs-lookup"><span data-stu-id="e27ff-143">In **Package Manager Console** (PMC) for both projects, run the command `Install-Package Entityframework`.</span></span>

* <span data-ttu-id="e27ff-144">클래스 라이브러리 프로젝트에서 데이터 모델 클래스 및 컨텍스트 클래스의 구현을 만들 `IDbContextFactory`합니다.</span><span class="sxs-lookup"><span data-stu-id="e27ff-144">In the class library project, create data model classes and a context class, and an implementation of `IDbContextFactory`.</span></span>

* <span data-ttu-id="e27ff-145">클래스 라이브러리 프로젝트에 대 한 PMC에서 명령을 실행 `Enable-Migrations` 및 `Add-Migration Initial`합니다.</span><span class="sxs-lookup"><span data-stu-id="e27ff-145">In PMC for the class library project, run the commands `Enable-Migrations` and `Add-Migration Initial`.</span></span> <span data-ttu-id="e27ff-146">ASP.NET Core 프로젝트를 시작 프로젝트로 설정한 경우 추가 `-StartupProjectName EF6` 이러한 명령에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e27ff-146">If you have set the ASP.NET Core project as the startup project, add `-StartupProjectName EF6` to these commands.</span></span>

* <span data-ttu-id="e27ff-147">핵심 프로젝트에서 클래스 라이브러리 프로젝트에 대 한 프로젝트 참조를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="e27ff-147">In the Core project, add a project reference to the class library project.</span></span>

* <span data-ttu-id="e27ff-148">핵심 프로젝트에서에서 *Startup.cs*, DI에 대 한 컨텍스트를 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="e27ff-148">In the Core project, in *Startup.cs*, register the context for DI.</span></span>

* <span data-ttu-id="e27ff-149">핵심 프로젝트에서에서 *appsettings.json*, 연결 문자열을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="e27ff-149">In the Core project, in *appsettings.json*, add the connection string.</span></span>

* <span data-ttu-id="e27ff-150">핵심 프로젝트에서 컨트롤러와 읽기 및 데이터를 쓸 수 있는지 확인 하는 뷰를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="e27ff-150">In the Core project, add a controller and view(s) to verify that you can read and write data.</span></span> <span data-ttu-id="e27ff-151">(EF6 컨텍스트 클래스 라이브러리의 참조와 작동 하지 않는 ASP.NET Core MVC 스 캐 폴딩 참고 합니다.)</span><span class="sxs-lookup"><span data-stu-id="e27ff-151">(Note that ASP.NET Core MVC scaffolding won't work with the EF6 context referenced from the class library.)</span></span>

## <a name="summary"></a><span data-ttu-id="e27ff-152">요약</span><span class="sxs-lookup"><span data-stu-id="e27ff-152">Summary</span></span>

<span data-ttu-id="e27ff-153">이 문서는 ASP.NET Core 응용 프로그램에서 Entity Framework 6을 사용 하기 위한 기본 지침을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="e27ff-153">This article has provided basic guidance for using Entity Framework 6 in an ASP.NET Core application.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e27ff-154">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="e27ff-154">Additional Resources</span></span>

* [<span data-ttu-id="e27ff-155">Entity Framework-코드 기반 구성</span><span class="sxs-lookup"><span data-stu-id="e27ff-155">Entity Framework - Code-Based Configuration</span></span>](https://msdn.microsoft.com/data/jj680699.aspx)
