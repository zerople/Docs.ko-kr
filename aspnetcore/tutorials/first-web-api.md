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
ms.openlocfilehash: 617b11cd7652e393c06446c62138802e4a4e90df
ms.sourcegitcommit: 67f54fabbfa4e3942f5bfe1f8a7fdfe4a7a75358
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/19/2017
---
#<a name="create-a-web-api-with-aspnet-core-and-visual-studio-for-windows"></a><span data-ttu-id="c05f3-104">ASP.NET Core 및 Windows용 Visual Studio를 사용하여 Web API 만들기</span><span class="sxs-lookup"><span data-stu-id="c05f3-104">Create a web API with ASP.NET Core and Visual Studio for Windows</span></span>

<span data-ttu-id="c05f3-105">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="c05f3-105">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="c05f3-106">이 자습서에서는 “할 일” 항목 목록을 관리하기 위한 웹 API를 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="c05f3-106">In this tutorial, you’ll build a web API for managing a list of "to-do" items.</span></span> <span data-ttu-id="c05f3-107">UI는 빌드하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c05f3-107">You won’t build a UI.</span></span>

<span data-ttu-id="c05f3-108">이 자습서는 세 가지 버전이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c05f3-108">There are 3 versions of this tutorial:</span></span>

* <span data-ttu-id="c05f3-109">Windows: Windows용 Visual Studio를 사용한 Web API(이 자습서)</span><span class="sxs-lookup"><span data-stu-id="c05f3-109">Windows: Web API with Visual Studio for Windows (This tutorial)</span></span>
* <span data-ttu-id="c05f3-110">macOS: [Mac용 Visual Studio를 사용한 Web API](xref:tutorials/first-web-api-mac)</span><span class="sxs-lookup"><span data-stu-id="c05f3-110">macOS: [Web API with Visual Studio for Mac](xref:tutorials/first-web-api-mac)</span></span>
* <span data-ttu-id="c05f3-111">macOS, Linux, Windows: [Visual Studio Code를 사용한 Web API](xref:tutorials/web-api-vsc)</span><span class="sxs-lookup"><span data-stu-id="c05f3-111">macOS, Linux, Windows: [Web API with Visual Studio Code](xref:tutorials/web-api-vsc)</span></span>

<!-- WARNING: The code AND images in this doc are used by uid: tutorials/web-api-vsc, tutorials/first-web-api-mac and tutorials/first-web-api. If you change any code/images in this tutorial, update uid: tutorials/web-api-vsc -->

[!INCLUDE[intro to web API](../includes/webApi/intro.md)]

## <a name="prerequisites"></a><span data-ttu-id="c05f3-112">필수 구성 요소</span><span class="sxs-lookup"><span data-stu-id="c05f3-112">Prerequisites</span></span>

[!INCLUDE[install 2.0](../includes/install2.0.md)]

<span data-ttu-id="c05f3-113">ASP.NET Core 1.1 버전에 대해서는 [이 PDF](https://github.com/aspnet/Docs/blob/master/aspnetcore/tutorials/first-web-api/_static/_webAPI.pdf)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c05f3-113">See [this PDF](https://github.com/aspnet/Docs/blob/master/aspnetcore/tutorials/first-web-api/_static/_webAPI.pdf) for the ASP.NET Core 1.1 version.</span></span>

## <a name="create-the-project"></a><span data-ttu-id="c05f3-114">프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="c05f3-114">Create the project</span></span>

<span data-ttu-id="c05f3-115">Visual Studio에서 **파일** 메뉴 > **새로 만들기** > **프로젝트**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c05f3-115">From Visual Studio, select **File** menu, > **New** > **Project**.</span></span>

<span data-ttu-id="c05f3-116">**ASP.NET Core 웹 응용 프로그램(.NET Core)** 프로젝트 템플릿을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c05f3-116">Select the **ASP.NET Core Web Application (.NET Core)** project template.</span></span> <span data-ttu-id="c05f3-117">프로젝트 이름을 `TodoApi`로 지정하고 **확인**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c05f3-117">Name the project `TodoApi` and select **OK**.</span></span>

![새 프로젝트 대화 상자](first-web-api/_static/new-project.png)

<span data-ttu-id="c05f3-119">**새 ASP.NET Core 웹 응용 프로그램 - TodoApi** 대화 상자에서 **Web API** 템플릿을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c05f3-119">In the **New ASP.NET Core Web Application - TodoApi** dialog, select the **Web API** template.</span></span> <span data-ttu-id="c05f3-120">**확인**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c05f3-120">Select **OK**.</span></span> <span data-ttu-id="c05f3-121">**Docker 지원 사용**을 선택하지 **마세요**.</span><span class="sxs-lookup"><span data-stu-id="c05f3-121">Do **not** select **Enable Docker Support**.</span></span>

![ASP.NET Core 템플릿에서 Web API 프로젝트 템플릿이 선택된 새 ASP.NET 웹 응용 프로그램 대화 상자](first-web-api/_static/web-api-project.png)

### <a name="launch-the-app"></a><span data-ttu-id="c05f3-123">앱 시작</span><span class="sxs-lookup"><span data-stu-id="c05f3-123">Launch the app</span></span>

<span data-ttu-id="c05f3-124">Visual Studio에서 CTRL+F5를 눌러 앱을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="c05f3-124">In Visual Studio, press CTRL+F5 to launch the app.</span></span> <span data-ttu-id="c05f3-125">Visual Studio가 브라우저를 시작하고 `http://localhost:port/api/values`로 이동합니다. 여기서 *port*는 임의로 선택된 포트 번호입니다.</span><span class="sxs-lookup"><span data-stu-id="c05f3-125">Visual Studio launches a browser and navigates to `http://localhost:port/api/values`, where *port* is a randomly-chosen port number.</span></span> <span data-ttu-id="c05f3-126">Chrome, Edge 및 Firefox에는 다음 내용이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="c05f3-126">Chrome, Edge, and Firefox display the following:</span></span>

```
["value1","value2"]
``` 

### <a name="add-a-model-class"></a><span data-ttu-id="c05f3-127">모델 클래스 추가</span><span class="sxs-lookup"><span data-stu-id="c05f3-127">Add a model class</span></span>

<span data-ttu-id="c05f3-128">모델은 응용 프로그램에서 데이터를 나타내는 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="c05f3-128">A model is an object that represents the data in your application.</span></span> <span data-ttu-id="c05f3-129">이 경우 유일한 모델은 할 일 항목입니다.</span><span class="sxs-lookup"><span data-stu-id="c05f3-129">In this case, the only model is a to-do item.</span></span>

<span data-ttu-id="c05f3-130">“Models” 폴더를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="c05f3-130">Add a folder named "Models".</span></span> <span data-ttu-id="c05f3-131">솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="c05f3-131">In Solution Explorer, right-click the project.</span></span> <span data-ttu-id="c05f3-132">**추가** > **새 폴더**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c05f3-132">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="c05f3-133">폴더 이름을 *Models*로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="c05f3-133">Name the folder *Models*.</span></span>

<span data-ttu-id="c05f3-134">참고: 모델 클래스는 프로젝트의 아무 곳에나 이동할 수 있지만 일반적으로 *Models* 폴더를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c05f3-134">Note: The model classes go anywhere in your project, but the *Models* folder is used by convention.</span></span>

<span data-ttu-id="c05f3-135">`TodoItem` 클래스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="c05f3-135">Add a `TodoItem` class.</span></span> <span data-ttu-id="c05f3-136">*Models* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **클래스**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c05f3-136">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="c05f3-137">클래스 이름을 `TodoItem`로 지정하고 **추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c05f3-137">Name the class `TodoItem` and select **Add**.</span></span>

<span data-ttu-id="c05f3-138">생성된 코드를 다음으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="c05f3-138">Replace the generated code with the following:</span></span>

[!code-csharp[Main](first-web-api/sample/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="c05f3-139">`TodoItem`이 만들어질 때 데이터베이스가 `Id`를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="c05f3-139">The database generates the `Id` when a `TodoItem` is created.</span></span>

### <a name="create-the-database-context"></a><span data-ttu-id="c05f3-140">데이터베이스 컨텍스트 만들기</span><span class="sxs-lookup"><span data-stu-id="c05f3-140">Create the database context</span></span>

<span data-ttu-id="c05f3-141">*데이터베이스 컨텍스트*는 특정 데이터 모델에 맞게 Entity Framework 기능을 조정하는 주 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="c05f3-141">The *database context* is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="c05f3-142">`Microsoft.EntityFrameworkCore.DbContext` 클래스에서 파생시키는 방식으로 이 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="c05f3-142">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

<span data-ttu-id="c05f3-143">`TodoContext` 클래스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="c05f3-143">Add a `TodoContext` class.</span></span> <span data-ttu-id="c05f3-144">*Models* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **클래스**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c05f3-144">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="c05f3-145">클래스 이름을 `TodoContext`로 지정하고 **추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c05f3-145">Name the class `TodoContext` and select **Add**.</span></span>

<span data-ttu-id="c05f3-146">생성된 코드를 다음으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="c05f3-146">Replace the generated code with the following:</span></span>

[!code-csharp[Main](first-web-api/sample/TodoApi/Models/TodoContext.cs)]

[!INCLUDE[Register the database context](../includes/webApi/register_dbContext.md)]

### <a name="add-a-controller"></a><span data-ttu-id="c05f3-147">컨트롤러 추가</span><span class="sxs-lookup"><span data-stu-id="c05f3-147">Add a controller</span></span>

<span data-ttu-id="c05f3-148">솔루션 탐색기에서 *Controllers* 폴더를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="c05f3-148">In Solution Explorer, right-click the *Controllers* folder.</span></span> <span data-ttu-id="c05f3-149">**추가** > **새 항목**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c05f3-149">Select **Add** > **New Item**.</span></span> <span data-ttu-id="c05f3-150">**새 항목 추가** 대화 상자에서 **Web API 컨트롤러 클래스** 템플릿을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c05f3-150">In the **Add New Item** dialog, select the **Web  API Controller Class** template.</span></span> <span data-ttu-id="c05f3-151">클래스 이름을 `TodoController`로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="c05f3-151">Name the class `TodoController`.</span></span>

![검색 상자의 컨트롤러 및 Web API 컨트롤러가 선택된 새 항목 추가 대화 상자](first-web-api/_static/new_controller.png)

<span data-ttu-id="c05f3-153">생성된 코드를 다음으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="c05f3-153">Replace the generated code with the following:</span></span>

[!INCLUDE[code and get todo items](../includes/webApi/getTodoItems.md)]
  
### <a name="launch-the-app"></a><span data-ttu-id="c05f3-154">앱 시작</span><span class="sxs-lookup"><span data-stu-id="c05f3-154">Launch the app</span></span>

<span data-ttu-id="c05f3-155">Visual Studio에서 CTRL+F5를 눌러 앱을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="c05f3-155">In Visual Studio, press CTRL+F5 to launch the app.</span></span> <span data-ttu-id="c05f3-156">Visual Studio가 브라우저를 시작하고 `http://localhost:port/api/values`로 이동합니다. 여기서 *port*는 임의로 선택된 포트 번호입니다.</span><span class="sxs-lookup"><span data-stu-id="c05f3-156">Visual Studio launches a browser and navigates to `http://localhost:port/api/values`, where *port* is a randomly chosen port number.</span></span> <span data-ttu-id="c05f3-157">Chrome, Edge 또는 Firefox를 사용할 경우 데이터가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="c05f3-157">If you're using Chrome, Edge or Firefox, the data will be displayed.</span></span> <span data-ttu-id="c05f3-158">IE를 사용할 경우 IE에서 *values.json* 파일을 열거나 저장할지 묻는 메시지를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="c05f3-158">If you're using IE, IE will prompt to you open or save the *values.json* file.</span></span> <span data-ttu-id="c05f3-159">방금 만든 `Todo` 컨트롤러(`http://localhost:port/api/todo`)로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="c05f3-159">Navigate to the `Todo` controller we just created `http://localhost:port/api/todo`.</span></span>

[!INCLUDE[last part of web API](../includes/webApi/end.md)]

[!INCLUDE[next steps](../includes/webApi/next.md)]

