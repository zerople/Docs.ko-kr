---
title: "네이티브 모바일 응용 프로그램에 대 한 백 엔드 서비스 만들기"
author: ardalis
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 3b6a32f2-5af9-4ede-9b7f-17ab300526d0
ms.technology: aspnet
ms.prod: asp.net-core
uid: mobile/native-mobile-backend
ms.openlocfilehash: f2b74d6739112909ee05e036e904d5e30868fdbe
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/11/2017
---
# <a name="creating-backend-services-for-native-mobile-applications"></a><span data-ttu-id="28812-103">네이티브 모바일 응용 프로그램에 대 한 백 엔드 서비스 만들기</span><span class="sxs-lookup"><span data-stu-id="28812-103">Creating Backend Services for Native Mobile Applications</span></span>

<span data-ttu-id="28812-104">으로 [Steve Smith](http://ardalis.com)</span><span class="sxs-lookup"><span data-stu-id="28812-104">By [Steve Smith](http://ardalis.com)</span></span>

<span data-ttu-id="28812-105">모바일 앱 ASP.NET Core 백 엔드 서비스와 쉽게 통신할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="28812-105">Mobile apps can easily communicate with ASP.NET Core backend services.</span></span>

[<span data-ttu-id="28812-106">보기 또는 샘플 백 엔드 서비스 코드를 다운로드 합니다.</span><span class="sxs-lookup"><span data-stu-id="28812-106">View or download sample backend services code</span></span>](https://github.com/aspnet/Docs/tree/master/aspnetcore/mobile/native-mobile-backend/sample)

## <a name="the-sample-native-mobile-app"></a><span data-ttu-id="28812-107">샘플 기본 모바일 앱</span><span class="sxs-lookup"><span data-stu-id="28812-107">The Sample Native Mobile App</span></span>

<span data-ttu-id="28812-108">이 자습서에는 네이티브 모바일 앱을 지원 하기 위해 ASP.NET Core MVC를 사용 하 여 백 엔드 서비스를 만드는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="28812-108">This tutorial demonstrates how to create backend services using ASP.NET Core MVC to support native mobile apps.</span></span> <span data-ttu-id="28812-109">사용 하 여는 [Xamarin Forms ToDoRest 앱](https://developer.xamarin.com/guides/xamarin-forms/web-services/consuming/rest/) Android, iOS, Windows 유니버설 및 Window Phone 장치에 대 한 별도 네이티브 클라이언트를 포함 하는 native client로 합니다.</span><span class="sxs-lookup"><span data-stu-id="28812-109">It uses the [Xamarin Forms ToDoRest app](https://developer.xamarin.com/guides/xamarin-forms/web-services/consuming/rest/) as its native client, which includes separate native clients for Android, iOS, Windows Universal, and Window Phone devices.</span></span> <span data-ttu-id="28812-110">Xamarin 샘플 솔루션 다운로드 수 있을 뿐만 아니라 네이티브 응용 프로그램 (필요한 무료 Xamarin 도구가 설치)를 만들고 연결 된 자습서에 따라 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="28812-110">You can follow the linked tutorial to create the native app (and install the necessary free Xamarin tools), as well as download the Xamarin sample solution.</span></span> <span data-ttu-id="28812-111">Xamarin 샘플에는 ASP.NET Web API 2 서비스 프로젝트에서이 문서의 ASP.NET Core 응용 프로그램으로 바꿉니다 (클라이언트에 필요한 변경 내용이 없습니다) 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="28812-111">The Xamarin sample includes an ASP.NET Web API 2 services project, which this article's ASP.NET Core app replaces (with no changes required by the client).</span></span>

![Android smartphone에서 실행 되는 않는 Rest 응용 프로그램을](native-mobile-backend/_static/todo-android.png)

### <a name="features"></a><span data-ttu-id="28812-113">기능</span><span class="sxs-lookup"><span data-stu-id="28812-113">Features</span></span>

<span data-ttu-id="28812-114">ToDoRest 앱 나열 하 고, 추가, 삭제, 할 일 항목을 업데이트를 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="28812-114">The ToDoRest app supports listing, adding, deleting, and updating To-Do items.</span></span> <span data-ttu-id="28812-115">각 항목에는 ID, 이름, 메모, 및 아직 완료 되었는지 여부를 나타내는 속성에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="28812-115">Each item has an ID, a Name, Notes, and a property indicating whether it's been Done yet.</span></span>

<span data-ttu-id="28812-116">항목의 주 보기 위에 표시 된 것 처럼 각 항목의 이름을 나열 하 고 확인 표시가 사용을 완료 하는 경우를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="28812-116">The main view of the items, as shown above, lists each item's name and indicates if it is done with a checkmark.</span></span>

<span data-ttu-id="28812-117">탭의 `+` 아이콘은 추가 항목 대화 상자를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="28812-117">Tapping the `+` icon opens an add item dialog:</span></span>

![항목 추가 대화 상자](native-mobile-backend/_static/todo-android-new-item.png)

<span data-ttu-id="28812-119">기본 목록 화면에서 항목을 누르면 항목의 이름, 메모 및 완료 설정을 수정할 수 있습니다 또는 항목을 삭제할 수 있는 편집 대화 상자를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="28812-119">Tapping an item on the main list screen opens up an edit dialog where the item's Name, Notes, and Done settings can be modified, or the item can be deleted:</span></span>

![항목 대화 상자 편집](native-mobile-backend/_static/todo-android-edit-item.png)

<span data-ttu-id="28812-121">이 샘플은 기본적으로 읽기 전용 작업을 허용 하는 developer.xamarin.com에서 호스트 되는 백 엔드 서비스를 사용 하도록 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="28812-121">This sample is configured by default to use backend services hosted at developer.xamarin.com, which allow read-only operations.</span></span> <span data-ttu-id="28812-122">샘플을 테스트 하려면 직접 순서도 컴퓨터에서 실행 되는 다음 섹션에서 만든 ASP.NET Core 응용 프로그램에 대 한 응용 프로그램의 업데이트 해야 `RestUrl` 상수입니다.</span><span class="sxs-lookup"><span data-stu-id="28812-122">To test it out yourself against the ASP.NET Core app created in the next section running on your computer, you'll need to update the app's `RestUrl` constant.</span></span> <span data-ttu-id="28812-123">로 이동 된 `ToDoREST` 연 프로젝트는 *Constants.cs* 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="28812-123">Navigate to the `ToDoREST` project and open the *Constants.cs* file.</span></span> <span data-ttu-id="28812-124">대체는 `RestUrl` 컴퓨터의 IP를 포함 하는 url 주소 (localhost 또는 127.0.0.1,이 주소는 컴퓨터에서가 아니라 장치 에뮬레이터에서 사용 하므로).</span><span class="sxs-lookup"><span data-stu-id="28812-124">Replace the `RestUrl` with a URL that includes your machine's IP address (not localhost or 127.0.0.1, since this address is used from the device emulator, not from your machine).</span></span> <span data-ttu-id="28812-125">포트 번호 (5000)을 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="28812-125">Include the port number as well (5000).</span></span> <span data-ttu-id="28812-126">장치 작업할 서비스를 테스트 하기 위해이 포트에 대 한 액세스를 차단 하는 활성 방화벽 없는 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="28812-126">In order to test that your services work with a device, ensure you don't have an active firewall blocking access to this port.</span></span>

<!-- literal_block {"ids": [], "names": [], "highlight_args": {}, "backrefs": [], "dupnames": [], "linenos": false, "classes": [], "xml:space": "preserve", "language": "csharp"} -->

```csharp
// URL of REST service (Xamarin ReadOnly Service)
//public static string RestUrl = "http://developer.xamarin.com:8081/api/todoitems{0}";

// use your machine's IP address
public static string RestUrl = "http://192.168.1.207:5000/api/todoitems/{0}";
```

## <a name="creating-the-aspnet-core-project"></a><span data-ttu-id="28812-127">ASP.NET Core 프로젝트 만들기</span><span class="sxs-lookup"><span data-stu-id="28812-127">Creating the ASP.NET Core Project</span></span>

<span data-ttu-id="28812-128">Visual Studio에서 새 ASP.NET Core 웹 응용 프로그램을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="28812-128">Create a new ASP.NET Core Web Application in Visual Studio.</span></span> <span data-ttu-id="28812-129">웹 API 템플릿과 인증 안 함을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="28812-129">Choose the Web API template and No Authentication.</span></span> <span data-ttu-id="28812-130">프로젝트 이름을 *ToDoApi*합니다.</span><span class="sxs-lookup"><span data-stu-id="28812-130">Name the project *ToDoApi*.</span></span>

![선택한 웹 API 프로젝트 템플릿 사용 하 여 새 ASP.NET 웹 응용 프로그램 대화 상자](native-mobile-backend/_static/web-api-template.png)

<span data-ttu-id="28812-132">응용 프로그램은 5000 포트에 대 한 모든 요청에 응답 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="28812-132">The application should respond to all requests made to port 5000.</span></span> <span data-ttu-id="28812-133">업데이트 *Program.cs* 포함 하도록 `.UseUrls("http://*:5000")` 이 작업을 수행할:</span><span class="sxs-lookup"><span data-stu-id="28812-133">Update *Program.cs* to include `.UseUrls("http://*:5000")` to achieve this:</span></span>

<span data-ttu-id="28812-134">[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Program.cs?range=10-16&highlight=3)]</span><span class="sxs-lookup"><span data-stu-id="28812-134">[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Program.cs?range=10-16&highlight=3)]</span></span>

> [!NOTE]
> <span data-ttu-id="28812-135">뒤에 기본적으로 로컬이 아닌 요청을 무시 합니다. IIS Express가 아닌을 직접 응용 프로그램을 실행 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="28812-135">Make sure you run the application directly, rather than behind IIS Express, which ignores non-local requests by default.</span></span> <span data-ttu-id="28812-136">실행 `dotnet run` 명령 프롬프트에서 또는 Visual Studio 도구 모음에서 디버그 대상 드롭다운 목록에서 응용 프로그램 이름 프로필을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="28812-136">Run `dotnet run` from a command prompt, or choose the application name profile from the Debug Target dropdown in the Visual Studio toolbar.</span></span>

<span data-ttu-id="28812-137">할 일 항목을 나타내기 위해 모델 클래스를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="28812-137">Add a model class to represent To-Do items.</span></span> <span data-ttu-id="28812-138">표시 필수 필드를 사용 하 여 `[Required]` 특성:</span><span class="sxs-lookup"><span data-stu-id="28812-138">Mark required fields using the `[Required]` attribute:</span></span>

<span data-ttu-id="28812-139">[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Models/ToDoItem.cs)]</span><span class="sxs-lookup"><span data-stu-id="28812-139">[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Models/ToDoItem.cs)]</span></span>

<span data-ttu-id="28812-140">API 메서드 데이터로 작업 하는 방법이 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="28812-140">The API methods require some way to work with data.</span></span> <span data-ttu-id="28812-141">동일한를 사용 하 여 `IToDoRepository` 원래 Xamarin 샘플에서는 인터페이스:</span><span class="sxs-lookup"><span data-stu-id="28812-141">Use the same `IToDoRepository` interface the original Xamarin sample uses:</span></span>

<span data-ttu-id="28812-142">[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Interfaces/IToDoRepository.cs)]</span><span class="sxs-lookup"><span data-stu-id="28812-142">[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Interfaces/IToDoRepository.cs)]</span></span>

<span data-ttu-id="28812-143">이 샘플에 대 한 구현만 개인 항목 컬렉션을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="28812-143">For this sample, the implementation just uses a private collection of items:</span></span>

<span data-ttu-id="28812-144">[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Services/ToDoRepository.cs)]</span><span class="sxs-lookup"><span data-stu-id="28812-144">[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Services/ToDoRepository.cs)]</span></span>

<span data-ttu-id="28812-145">구성에서 구현을 *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="28812-145">Configure the implementation in *Startup.cs*:</span></span>

<span data-ttu-id="28812-146">[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Startup.cs?highlight=6&range=29-35)]</span><span class="sxs-lookup"><span data-stu-id="28812-146">[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Startup.cs?highlight=6&range=29-35)]</span></span>

<span data-ttu-id="28812-147">이 시점에서 준비가 만들기는 *ToDoItemsController*합니다.</span><span class="sxs-lookup"><span data-stu-id="28812-147">At this point, you're ready to create the *ToDoItemsController*.</span></span>

> [!TIP]
> <span data-ttu-id="28812-148">만들기에 대 한 웹 Api에 알아보기 [건물 Your 첫 번째 Web API Core MVC ASP.NET 및 Visual Studio](../tutorials/first-web-api.md)합니다.</span><span class="sxs-lookup"><span data-stu-id="28812-148">Learn more about creating web APIs in [Building Your First Web API with ASP.NET Core MVC and Visual Studio](../tutorials/first-web-api.md).</span></span>

## <a name="creating-the-controller"></a><span data-ttu-id="28812-149">컨트롤러 만들기</span><span class="sxs-lookup"><span data-stu-id="28812-149">Creating the Controller</span></span>

<span data-ttu-id="28812-150">프로젝트에 새 컨트롤러 추가 *ToDoItemsController*합니다.</span><span class="sxs-lookup"><span data-stu-id="28812-150">Add a new controller to the project, *ToDoItemsController*.</span></span> <span data-ttu-id="28812-151">그 Microsoft.AspNetCore.Mvc.Controller에서 상속 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="28812-151">It should inherit from Microsoft.AspNetCore.Mvc.Controller.</span></span> <span data-ttu-id="28812-152">추가 `Route` 컨트롤러를 시작 하는 경로에 대 한 요청을 처리 합니다 나타내려면 특성 `api/todoitems`합니다.</span><span class="sxs-lookup"><span data-stu-id="28812-152">Add a `Route` attribute to indicate that the controller will handle requests made to paths starting with `api/todoitems`.</span></span> <span data-ttu-id="28812-153">`[controller]` 경로의 토큰은 컨트롤러의 이름으로 대체 (생략 하는 것은 `Controller` 접미사), 되며가 글로벌 경로 대 한 특히 유용 합니다.</span><span class="sxs-lookup"><span data-stu-id="28812-153">The `[controller]` token in the route is replaced by the name of the controller (omitting the `Controller` suffix), and is especially helpful for global routes.</span></span> <span data-ttu-id="28812-154">에 대 한 자세한 내용은 [라우팅](../fundamentals/routing.md)합니다.</span><span class="sxs-lookup"><span data-stu-id="28812-154">Learn more about [routing](../fundamentals/routing.md).</span></span>

<span data-ttu-id="28812-155">컨트롤러의 요구 사항은 `IToDoRepository` 를 작동; 컨트롤러의 생성자를 통해이 형식의 인스턴스를 요청 합니다.</span><span class="sxs-lookup"><span data-stu-id="28812-155">The controller requires an `IToDoRepository` to function; request an instance of this type through the controller's constructor.</span></span> <span data-ttu-id="28812-156">런타임 시이 인스턴스가 제공 됩니다에 대 한 프레임 워크의 지원을 사용 하 여 [종속성 주입](../fundamentals/dependency-injection.md)합니다.</span><span class="sxs-lookup"><span data-stu-id="28812-156">At runtime, this instance will be provided using the framework's support for [dependency injection](../fundamentals/dependency-injection.md).</span></span>

<span data-ttu-id="28812-157">[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=1-17&highlight=9,14)]</span><span class="sxs-lookup"><span data-stu-id="28812-157">[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=1-17&highlight=9,14)]</span></span>

<span data-ttu-id="28812-158">이 API는 데이터 원본에서 CRUD (만들기, 읽기, 업데이트, 삭제) 작업을 수행 4 개의 다른 HTTP 동사를 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="28812-158">This API supports four different HTTP verbs to perform CRUD (Create, Read, Update, Delete) operations on the data source.</span></span> <span data-ttu-id="28812-159">이 중 가장 간단한 HTTP GET 요청에 해당 하는 읽기 작업입니다.</span><span class="sxs-lookup"><span data-stu-id="28812-159">The simplest of these is the Read operation, which corresponds to an HTTP GET request.</span></span>

### <a name="reading-items"></a><span data-ttu-id="28812-160">항목을 읽는</span><span class="sxs-lookup"><span data-stu-id="28812-160">Reading Items</span></span>

<span data-ttu-id="28812-161">GET 요청에 의해 이루어진다는 항목 목록을 요청 하는 `List` 메서드.</span><span class="sxs-lookup"><span data-stu-id="28812-161">Requesting a list of items is done with a GET request to the `List` method.</span></span> <span data-ttu-id="28812-162">`[HttpGet]` 특성에 `List` 메서드는이 작업은 GET 요청을 처리만 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="28812-162">The `[HttpGet]` attribute on the `List` method indicates that this action should only handle GET requests.</span></span> <span data-ttu-id="28812-163">이 작업에 대 한 경로 컨트롤러에 지정 된 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="28812-163">The route for this action is the route specified on the controller.</span></span> <span data-ttu-id="28812-164">반드시 작업 이름을 경로의 일부로 사용할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="28812-164">You don't necessarily need to use the action name as part of the route.</span></span> <span data-ttu-id="28812-165">각 작업에는 고유 하 고 명확한 경로 확인 하기만 하면 됩니다.</span><span class="sxs-lookup"><span data-stu-id="28812-165">You just need to ensure each action has a unique and unambiguous route.</span></span> <span data-ttu-id="28812-166">컨트롤러와 특정 경로를 작성 하는 메서드 수준에서 라우팅 특성을 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="28812-166">Routing attributes can be applied at both the controller and method levels to build up specific routes.</span></span>

<span data-ttu-id="28812-167">[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=19-23)]</span><span class="sxs-lookup"><span data-stu-id="28812-167">[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=19-23)]</span></span>

<span data-ttu-id="28812-168">`List` 메서드 200 OK 응답 코드와 JSON으로 serialize 할 일 항목을 모두 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="28812-168">The `List` method returns a 200 OK response code and all of the ToDo items, serialized as JSON.</span></span>

<span data-ttu-id="28812-169">와 같은 다양 한 도구를 사용 하 여 새 API 메서드를 테스트할 수 [우체부](https://www.getpostman.com/docs/), 여기에 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="28812-169">You can test your new API method using a variety of tools, such as [Postman](https://www.getpostman.com/docs/), shown here:</span></span>

![Todoitems 및 반환 되는 세 가지 항목에 대 한 JSON을 보여 주는 응답의 본문에 대 한 GET 요청을 보여 주는 우체부 콘솔](native-mobile-backend/_static/postman-get.png)

### <a name="creating-items"></a><span data-ttu-id="28812-171">항목 만들기</span><span class="sxs-lookup"><span data-stu-id="28812-171">Creating Items</span></span>

<span data-ttu-id="28812-172">일반적으로 HTTP POST 동사에 새 데이터 항목을 만드는 매핑됩니다.</span><span class="sxs-lookup"><span data-stu-id="28812-172">By convention, creating new data items is mapped to the HTTP POST verb.</span></span> <span data-ttu-id="28812-173">`Create` 메서드에 `[HttpPost]` 특성을 적용 하 고 수락는 `ToDoItem` 인스턴스.</span><span class="sxs-lookup"><span data-stu-id="28812-173">The `Create` method has an `[HttpPost]` attribute applied to it, and accepts a `ToDoItem` instance.</span></span> <span data-ttu-id="28812-174">이후는 `item` 은 게시물 본문에 전달할 인수,으로 데코레이팅되 어이 매개 변수는 `[FromBody]` 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="28812-174">Since the `item` argument will be passed in the body of the POST, this parameter is decorated with the `[FromBody]` attribute.</span></span>

<span data-ttu-id="28812-175">메서드 내부를 유효성 및 데이터 저장소에서 이전 존재에 대 한 항목이 선택 되는 및을 없는 문제가 발생 한 경우 저장소를 사용 하 여 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="28812-175">Inside the method, the item is checked for validity and prior existence in the data store, and if no issues occur, it is added using the repository.</span></span> <span data-ttu-id="28812-176">검사 `ModelState.IsValid` 수행 [유효성 검사 모델](../mvc/models/validation.md), 사용자 입력을 허용 하는 모든 API 메서드에서 수행 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="28812-176">Checking `ModelState.IsValid` performs [model validation](../mvc/models/validation.md), and should be done in every API method that accepts user input.</span></span>

<span data-ttu-id="28812-177">[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=25-46)]</span><span class="sxs-lookup"><span data-stu-id="28812-177">[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=25-46)]</span></span>

<span data-ttu-id="28812-178">샘플에서는 모바일 클라이언트에 전달 되는 오류 코드를 포함 하는 열거형을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="28812-178">The sample uses an enum containing error codes that are passed to the mobile client:</span></span>

<span data-ttu-id="28812-179">[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=91-99)]</span><span class="sxs-lookup"><span data-stu-id="28812-179">[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=91-99)]</span></span>

<span data-ttu-id="28812-180">우체부를 사용 하 여 JSON 형식의 요청 본문에 새 개체를 제공 하는 POST 동사를 선택 하 여 새 항목 추가 테스트 합니다.</span><span class="sxs-lookup"><span data-stu-id="28812-180">Test adding new items using Postman by choosing the POST verb providing the new object in JSON format in the Body of the request.</span></span> <span data-ttu-id="28812-181">지정 하는 요청 헤더를 추가할도 해야는 `Content-Type` 의 `application/json`합니다.</span><span class="sxs-lookup"><span data-stu-id="28812-181">You should also add a request header specifying a `Content-Type` of `application/json`.</span></span>

![POST 및 응답을 보여 주는 우체부 콘솔](native-mobile-backend/_static/postman-post.png)

<span data-ttu-id="28812-183">메서드는 응답에서 새로 만든된 항목을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="28812-183">The method returns the newly created item in the response.</span></span>

### <a name="updating-items"></a><span data-ttu-id="28812-184">항목 업데이트</span><span class="sxs-lookup"><span data-stu-id="28812-184">Updating Items</span></span>

<span data-ttu-id="28812-185">레코드를 수정 이루어진다는 HTTP PUT 요청을 사용 하 여 합니다.</span><span class="sxs-lookup"><span data-stu-id="28812-185">Modifying records is done using HTTP PUT requests.</span></span> <span data-ttu-id="28812-186">이 변경 외의 `Edit` 메서드는 거의 동일 `Create`합니다.</span><span class="sxs-lookup"><span data-stu-id="28812-186">Other than this change, the `Edit` method is almost identical to `Create`.</span></span> <span data-ttu-id="28812-187">레코드를 찾을 수 없을 경우, `Edit` 작업 반환 됩니다는 `NotFound` (404) 응답 합니다.</span><span class="sxs-lookup"><span data-stu-id="28812-187">Note that if the record isn't found, the `Edit` action will return a `NotFound` (404) response.</span></span>

<span data-ttu-id="28812-188">[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=48-69)]</span><span class="sxs-lookup"><span data-stu-id="28812-188">[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=48-69)]</span></span>

<span data-ttu-id="28812-189">우체부를 테스트 하려면 PUT를 동사를 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="28812-189">To test with Postman, change the verb to PUT.</span></span> <span data-ttu-id="28812-190">요청 본문에서 업데이트 된 개체 데이터를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="28812-190">Specify the updated object data in the Body of the request.</span></span>

![PUT 및 응답을 보여 주는 우체부 콘솔](native-mobile-backend/_static/postman-put.png)

<span data-ttu-id="28812-192">이 메서드는 반환 된 `NoContent` (204) 응답 기존 API와 일관성을 위해 로그인이 성공 합니다.</span><span class="sxs-lookup"><span data-stu-id="28812-192">This method returns a `NoContent` (204) response when successful, for consistency with the pre-existing API.</span></span>

### <a name="deleting-items"></a><span data-ttu-id="28812-193">항목 삭제</span><span class="sxs-lookup"><span data-stu-id="28812-193">Deleting Items</span></span>

<span data-ttu-id="28812-194">레코드를 삭제 하려면 삭제할 항목의 ID를 전달 하는 서비스에 대 한 삭제 요청 합니다.</span><span class="sxs-lookup"><span data-stu-id="28812-194">Deleting records is accomplished by making DELETE requests to the service, and passing the ID of the item to be deleted.</span></span> <span data-ttu-id="28812-195">업데이트를 사용 하면 존재 하지 않는 항목에 대 한 요청은 수신으로 `NotFound` 응답 합니다.</span><span class="sxs-lookup"><span data-stu-id="28812-195">As with updates, requests for items that don't exist will receive `NotFound` responses.</span></span> <span data-ttu-id="28812-196">그렇지 않으면, 요청이 성공 표시 됩니다는 `NoContent` (204) 응답 합니다.</span><span class="sxs-lookup"><span data-stu-id="28812-196">Otherwise, a successful request will get a `NoContent` (204) response.</span></span>

<span data-ttu-id="28812-197">[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=71-88)]</span><span class="sxs-lookup"><span data-stu-id="28812-197">[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=71-88)]</span></span>

<span data-ttu-id="28812-198">Note 삭제 기능을 테스트할 때 요청 본문에 필요한은 nothing입니다.</span><span class="sxs-lookup"><span data-stu-id="28812-198">Note that when testing the delete functionality, nothing is required in the Body of the request.</span></span>

![삭제 및 응답을 보여 주는 우체부 콘솔](native-mobile-backend/_static/postman-delete.png)

## <a name="common-web-api-conventions"></a><span data-ttu-id="28812-200">공통 Web API 규칙</span><span class="sxs-lookup"><span data-stu-id="28812-200">Common Web API Conventions</span></span>

<span data-ttu-id="28812-201">앱에 대 한 백 엔드 서비스를 개발 하는 경우 규칙 또는 처리 일반적인 문제에 대 한 일관 된 집합을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="28812-201">As you develop the backend services for your app, you will want to come up with a consistent set of conventions or policies for handling cross-cutting concerns.</span></span> <span data-ttu-id="28812-202">예를 들어 위에 표시 된 서비스에서 받은 특정 발견 되지 않은 레코드에 대 한 요청을 `NotFound` 응답 대신 `BadRequest` 응답 합니다.</span><span class="sxs-lookup"><span data-stu-id="28812-202">For example, in the service shown above, requests for specific records that weren't found received a `NotFound` response, rather than a `BadRequest` response.</span></span> <span data-ttu-id="28812-203">항상 확인 하는 모델에 바인딩되어 형식에 전달 된이 서비스에 명령을 마찬가지로, `ModelState.IsValid` 반환 하 고는 `BadRequest` 잘못 된 모델 유형에 대 한 합니다.</span><span class="sxs-lookup"><span data-stu-id="28812-203">Similarly, commands made to this service that passed in model bound types always checked `ModelState.IsValid` and returned a `BadRequest` for invalid model types.</span></span>

<span data-ttu-id="28812-204">Api에 대 한 일반적인 정책을 식별 했으면, 일반적으로 캡슐화 할 수 있습니다에 [필터](../mvc/controllers/filters.md)합니다.</span><span class="sxs-lookup"><span data-stu-id="28812-204">Once you've identified a common policy for your APIs, you can usually encapsulate it in a [filter](../mvc/controllers/filters.md).</span></span> <span data-ttu-id="28812-205">에 대 한 자세한 내용은 [ASP.NET Core MVC 응용 프로그램에서 공통 API 정책을 캡슐화 하는 방법을](https://msdn.microsoft.com/magazine/mt767699.aspx)합니다.</span><span class="sxs-lookup"><span data-stu-id="28812-205">Learn more about [how to encapsulate common API policies in ASP.NET Core MVC applications](https://msdn.microsoft.com/magazine/mt767699.aspx).</span></span>
