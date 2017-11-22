---
title: "ASP.NET Core 및 Windows용 Visual Studio를 사용하여 Web API 만들기"
author: rick-anderson
description: "ASP.NET Core MVC 및 Windows용 Visual Studio를 사용하여 Web API 빌드"
keywords: "ASP.NET Core, WebAPI, Web API, REST, HTTP, 서비스, HTTP 서비스"
ms.author: riande
manager: wpickett
ms.date: 08/15/2017
ms.topic: get-started-article
ms.technology: aspnet
ms.prod: asp.net-core
uid: tutorials/first-web-api
ms.openlocfilehash: 3ef6fb26eab123c9f6f8275ee1d979b090db0413
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2017
---
#<a name="create-a-web-api-with-aspnet-core-and-visual-studio-for-windows"></a><span data-ttu-id="7444e-104">ASP.NET Core 및 Windows용 Visual Studio를 사용하여 Web API 만들기</span><span class="sxs-lookup"><span data-stu-id="7444e-104">Create a web API with ASP.NET Core and Visual Studio for Windows</span></span>

<span data-ttu-id="7444e-105">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="7444e-105">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="7444e-106">이 자습서에서는 “할 일” 항목 목록을 관리하기 위한 웹 API를 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="7444e-106">This tutorial builds a web API for managing a list of "to-do" items.</span></span> <span data-ttu-id="7444e-107">UI(사용자 인터페이스)는 만들어지지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7444e-107">A user interface (UI) is not created.</span></span>

<span data-ttu-id="7444e-108">이 자습서는 세 가지 버전이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7444e-108">There are 3 versions of this tutorial:</span></span>

* <span data-ttu-id="7444e-109">Windows: Windows용 Visual Studio를 사용한 Web API(이 자습서)</span><span class="sxs-lookup"><span data-stu-id="7444e-109">Windows: Web API with Visual Studio for Windows (This tutorial)</span></span>
* <span data-ttu-id="7444e-110">macOS: [Mac용 Visual Studio를 사용한 Web API](xref:tutorials/first-web-api-mac)</span><span class="sxs-lookup"><span data-stu-id="7444e-110">macOS: [Web API with Visual Studio for Mac](xref:tutorials/first-web-api-mac)</span></span>
* <span data-ttu-id="7444e-111">macOS, Linux, Windows: [Visual Studio Code를 사용한 Web API](xref:tutorials/web-api-vsc)</span><span class="sxs-lookup"><span data-stu-id="7444e-111">macOS, Linux, Windows: [Web API with Visual Studio Code](xref:tutorials/web-api-vsc)</span></span>

<!-- WARNING: The code AND images in this doc are used by uid: tutorials/web-api-vsc, tutorials/first-web-api-mac and tutorials/first-web-api. If you change any code/images in this tutorial, update uid: tutorials/web-api-vsc -->

[!INCLUDE[intro to web API](../includes/webApi/intro.md)]

## <a name="prerequisites"></a><span data-ttu-id="7444e-112">필수 구성 요소</span><span class="sxs-lookup"><span data-stu-id="7444e-112">Prerequisites</span></span>

[!INCLUDE[install 2.0](../includes/install2.0.md)]

<span data-ttu-id="7444e-113">ASP.NET Core 1.1 버전에 대해서는 [이 PDF](https://github.com/aspnet/Docs/blob/master/aspnetcore/tutorials/first-web-api/_static/_webAPI.pdf)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7444e-113">See [this PDF](https://github.com/aspnet/Docs/blob/master/aspnetcore/tutorials/first-web-api/_static/_webAPI.pdf) for the ASP.NET Core 1.1 version.</span></span>

## <a name="create-the-project"></a><span data-ttu-id="7444e-114">프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7444e-114">Create the project</span></span>

<span data-ttu-id="7444e-115">Visual Studio에서 **파일** 메뉴 > **새로 만들기** > **프로젝트**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7444e-115">From Visual Studio, select **File** menu, > **New** > **Project**.</span></span>

<span data-ttu-id="7444e-116">**ASP.NET Core 웹 응용 프로그램(.NET Core)** 프로젝트 템플릿을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7444e-116">Select the **ASP.NET Core Web Application (.NET Core)** project template.</span></span> <span data-ttu-id="7444e-117">프로젝트 이름을 `TodoApi`로 지정하고 **확인**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7444e-117">Name the project `TodoApi` and select **OK**.</span></span>

![새 프로젝트 대화 상자](first-web-api/_static/new-project.png)

<span data-ttu-id="7444e-119">**새 ASP.NET Core 웹 응용 프로그램 - TodoApi** 대화 상자에서 **Web API** 템플릿을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7444e-119">In the **New ASP.NET Core Web Application - TodoApi** dialog, select the **Web API** template.</span></span> <span data-ttu-id="7444e-120">**확인**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7444e-120">Select **OK**.</span></span> <span data-ttu-id="7444e-121">**Docker 지원 사용**을 선택하지 **마세요**.</span><span class="sxs-lookup"><span data-stu-id="7444e-121">Do **not** select **Enable Docker Support**.</span></span>

![ASP.NET Core 템플릿에서 Web API 프로젝트 템플릿이 선택된 새 ASP.NET 웹 응용 프로그램 대화 상자](first-web-api/_static/web-api-project.png)

### <a name="launch-the-app"></a><span data-ttu-id="7444e-123">앱 시작</span><span class="sxs-lookup"><span data-stu-id="7444e-123">Launch the app</span></span>

<span data-ttu-id="7444e-124">Visual Studio에서 CTRL+F5를 눌러 앱을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="7444e-124">In Visual Studio, press CTRL+F5 to launch the app.</span></span> <span data-ttu-id="7444e-125">Visual Studio가 브라우저를 시작하고 `http://localhost:port/api/values`로 이동합니다. 여기서 *port*는 임의로 선택된 포트 번호입니다.</span><span class="sxs-lookup"><span data-stu-id="7444e-125">Visual Studio launches a browser and navigates to `http://localhost:port/api/values`, where *port* is a randomly chosen port number.</span></span> <span data-ttu-id="7444e-126">Chrome, Microsoft Edge 및 Firefox에는 다음 출력이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="7444e-126">Chrome, Microsoft Edge, and Firefox display the following output:</span></span>

```
["value1","value2"]
```

### <a name="add-a-model-class"></a><span data-ttu-id="7444e-127">모델 클래스 추가</span><span class="sxs-lookup"><span data-stu-id="7444e-127">Add a model class</span></span>

<span data-ttu-id="7444e-128">모델은 앱에서 데이터를 나타내는 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="7444e-128">A model is an object that represents the data in the app.</span></span> <span data-ttu-id="7444e-129">이 경우 유일한 모델은 할 일 항목입니다.</span><span class="sxs-lookup"><span data-stu-id="7444e-129">In this case, the only model is a to-do item.</span></span>

<span data-ttu-id="7444e-130">“Models” 폴더를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7444e-130">Add a folder named "Models".</span></span> <span data-ttu-id="7444e-131">솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="7444e-131">In Solution Explorer, right-click the project.</span></span> <span data-ttu-id="7444e-132">**추가** > **새 폴더**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7444e-132">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="7444e-133">폴더 이름을 *Models*로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7444e-133">Name the folder *Models*.</span></span>

<span data-ttu-id="7444e-134">참고: 모델 클래스는 프로젝트의 어디에서나 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7444e-134">Note: The model classes go anywhere in in the project.</span></span> <span data-ttu-id="7444e-135">*Models* 폴더는 모델 클래스에 대한 규칙에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7444e-135">The *Models* folder is used by convention for model classes.</span></span>

<span data-ttu-id="7444e-136">`TodoItem` 클래스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7444e-136">Add a `TodoItem` class.</span></span> <span data-ttu-id="7444e-137">*Models* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **클래스**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7444e-137">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="7444e-138">클래스 이름을 `TodoItem`로 지정하고 **추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7444e-138">Name the class `TodoItem` and select **Add**.</span></span>

<span data-ttu-id="7444e-139">`TodoItem` 클래스를 다음 코드로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="7444e-139">Update the `TodoItem` class with the following code:</span></span>

[!code-csharp[Main](first-web-api/sample/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="7444e-140">`TodoItem`이 만들어질 때 데이터베이스가 `Id`를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="7444e-140">The database generates the `Id` when a `TodoItem` is created.</span></span>

### <a name="create-the-database-context"></a><span data-ttu-id="7444e-141">데이터베이스 컨텍스트 만들기</span><span class="sxs-lookup"><span data-stu-id="7444e-141">Create the database context</span></span>

<span data-ttu-id="7444e-142">*데이터베이스 컨텍스트*는 특정 데이터 모델에 맞게 Entity Framework 기능을 조정하는 주 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="7444e-142">The *database context* is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="7444e-143">`Microsoft.EntityFrameworkCore.DbContext` 클래스에서 파생시키는 방식으로 이 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7444e-143">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

<span data-ttu-id="7444e-144">`TodoContext` 클래스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7444e-144">Add a `TodoContext` class.</span></span> <span data-ttu-id="7444e-145">*Models* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **클래스**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7444e-145">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="7444e-146">클래스 이름을 `TodoContext`로 지정하고 **추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7444e-146">Name the class `TodoContext` and select **Add**.</span></span>

<span data-ttu-id="7444e-147">클래스를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="7444e-147">Replace the class with the following code:</span></span>

[!code-csharp[Main](first-web-api/sample/TodoApi/Models/TodoContext.cs)]

[!INCLUDE[Register the database context](../includes/webApi/register_dbContext.md)]

### <a name="add-a-controller"></a><span data-ttu-id="7444e-148">컨트롤러 추가</span><span class="sxs-lookup"><span data-stu-id="7444e-148">Add a controller</span></span>

<span data-ttu-id="7444e-149">솔루션 탐색기에서 *Controllers* 폴더를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="7444e-149">In Solution Explorer, right-click the *Controllers* folder.</span></span> <span data-ttu-id="7444e-150">**추가** > **새 항목**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7444e-150">Select **Add** > **New Item**.</span></span> <span data-ttu-id="7444e-151">**새 항목 추가** 대화 상자에서 **Web API 컨트롤러 클래스** 템플릿을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7444e-151">In the **Add New Item** dialog, select the **Web API Controller Class** template.</span></span> <span data-ttu-id="7444e-152">클래스 이름을 `TodoController`로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7444e-152">Name the class `TodoController`.</span></span>

![검색 상자의 컨트롤러 및 Web API 컨트롤러가 선택된 새 항목 추가 대화 상자](first-web-api/_static/new_controller.png)

<span data-ttu-id="7444e-154">클래스를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="7444e-154">Replace the class with the following code:</span></span>

[!INCLUDE[code and get todo items](../includes/webApi/getTodoItems.md)]

### <a name="launch-the-app"></a><span data-ttu-id="7444e-155">앱 시작</span><span class="sxs-lookup"><span data-stu-id="7444e-155">Launch the app</span></span>

<span data-ttu-id="7444e-156">Visual Studio에서 CTRL+F5를 눌러 앱을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="7444e-156">In Visual Studio, press CTRL+F5 to launch the app.</span></span> <span data-ttu-id="7444e-157">Visual Studio가 브라우저를 시작하고 `http://localhost:port/api/values`로 이동합니다. 여기서 *port*는 임의로 선택된 포트 번호입니다.</span><span class="sxs-lookup"><span data-stu-id="7444e-157">Visual Studio launches a browser and navigates to `http://localhost:port/api/values`, where *port* is a randomly chosen port number.</span></span> <span data-ttu-id="7444e-158">`http://localhost:port/api/todo`의 `Todo` 컨트롤러로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="7444e-158">Navigate to the `Todo` controller at `http://localhost:port/api/todo`.</span></span>

[!INCLUDE[last part of web API](../includes/webApi/end.md)]

[!INCLUDE[next steps](../includes/webApi/next.md)]

