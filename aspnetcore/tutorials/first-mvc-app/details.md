---
title: "세부 정보 및 삭제 메서드 검사"
author: rick-anderson
description: "기본적인 ASP.NET Core MVC 앱의 세부 정보 컨트롤러 메서드 및 보기"
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 03/07/2017
ms.topic: get-started-article
ms.technology: aspnet
ms.prod: asp.net-core
uid: tutorials/first-mvc-app/details
ms.openlocfilehash: 43394106c9074f9487e1065a37a88eb017833bae
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2017
---
# <a name="examining-the-details-and-delete-methods"></a><span data-ttu-id="578fc-104">세부 정보 및 삭제 메서드 검사</span><span class="sxs-lookup"><span data-stu-id="578fc-104">Examining the Details and Delete methods</span></span>

<span data-ttu-id="578fc-105">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="578fc-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="578fc-106">Movie 컨트롤러를 열고 `Details` 메서드를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="578fc-106">Open the Movie controller and examine the `Details` method:</span></span>

[!code-csharp[Main](start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_details)]

<span data-ttu-id="578fc-107">이 작업 메서드를 만든 MVC 스캐폴딩 엔진은 메서드를 호출하는 HTTP 요청을 보여 주는 설명을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="578fc-107">The MVC scaffolding engine that created this action method adds a comment showing an HTTP request that invokes the method.</span></span> <span data-ttu-id="578fc-108">이 경우 3개의 URL 세그먼트인 `Movies` 컨트롤러, `Details` 메서드 및 `id` 값을 가진 GET 요청입니다.</span><span class="sxs-lookup"><span data-stu-id="578fc-108">In this case it's a GET request with three URL segments, the `Movies` controller, the `Details` method and an `id` value.</span></span> <span data-ttu-id="578fc-109">이러한 세그먼트 회수는 *Startup.cs*에서 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="578fc-109">Recall these segments are defined in *Startup.cs*.</span></span>

[!code-csharp[Main](start-mvc/sample/MvcMovie/Startup.cs?highlight=5&name=snippet_1)]

<span data-ttu-id="578fc-110">EF를 통해 `SingleOrDefaultAsync` 메서드를 사용하는 데이터를 쉽게 검색할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="578fc-110">EF makes it easy to search for data using the `SingleOrDefaultAsync` method.</span></span> <span data-ttu-id="578fc-111">메서드에 기본 구성된 중요한 보안 기능은 검색 메서드가 이를 사용하여 어떠한 작업을 시도하기 전에 동영상을 발견했는지 코드에서 확인하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="578fc-111">An important security feature built into the method is that the code verifies that the search method has found a movie before it tries to do anything with it.</span></span> <span data-ttu-id="578fc-112">예를 들어 해커는 링크에서 만든 URL을 `http://localhost:xxxx/Movies/Details/1`에서 `http://localhost:xxxx/Movies/Details/12345`(또는 실제 동영상을 표시하지 않는 다른 값)와 같은 URL로 변경하여 사이트에 오류를 발생시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="578fc-112">For example, a hacker could introduce errors into the site by changing the URL created by the links from `http://localhost:xxxx/Movies/Details/1` to something like  `http://localhost:xxxx/Movies/Details/12345` (or some other value that doesn't represent an actual movie).</span></span> <span data-ttu-id="578fc-113">Null 동영상을 검사하지 않은 경우 앱은 예외를 throw합니다.</span><span class="sxs-lookup"><span data-stu-id="578fc-113">If you did not check for a null movie, the app would throw an exception.</span></span>

<span data-ttu-id="578fc-114">`Delete` 및 `DeleteConfirmed` 메서드를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="578fc-114">Examine the `Delete` and `DeleteConfirmed` methods.</span></span>

[!code-csharp[Main](start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_delete)]

<span data-ttu-id="578fc-115">`HTTP GET Delete` 메서드는 지정된 동영상을 삭제하지 않고 삭제를 제출(HttpPost)할 수 있는 동영상의 보기를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="578fc-115">Note that the `HTTP GET Delete` method doesn't delete the specified movie, it returns a view of the movie where you can submit (HttpPost) the deletion.</span></span> <span data-ttu-id="578fc-116">GET 요청에 대한 응답에서 삭제 작업 수행(또는 해당 문제를 위해 편집 작업 수행, 작업 만들기 또는 데이터를 변경하는 기타 작업)은 보안 허점을 야기합니다.</span><span class="sxs-lookup"><span data-stu-id="578fc-116">Performing a delete operation in response to a GET request (or for that matter, performing an edit operation, create operation, or any other operation that changes data) opens up a security hole.</span></span>

<span data-ttu-id="578fc-117">데이터를 삭제하는 `[HttpPost]` 메서드는 HTTP POST 메서드에 고유한 서명 또는 이름을 제공하기 위해 `DeleteConfirmed`로 이름이 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="578fc-117">The `[HttpPost]` method that deletes the data is named `DeleteConfirmed` to give the HTTP POST method a unique signature or name.</span></span> <span data-ttu-id="578fc-118">두 개의 메서드 서명은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="578fc-118">The two method signatures are shown below:</span></span>

[!code-csharp[Main](start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_delete2)]

[!code-csharp[Main](start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_delete3)]


<span data-ttu-id="578fc-119">CLR(공용 언어 런타임)은 고유한 매개 변수 서명을 갖기 위해 오버로드된 메서드가 필요합니다(동일한 메서드 이름이지만 다른 매개 변수의 목록).</span><span class="sxs-lookup"><span data-stu-id="578fc-119">The common language runtime (CLR) requires overloaded methods to have a unique parameter signature (same method name but different list of parameters).</span></span> <span data-ttu-id="578fc-120">그러나 여기에서 두 개의 `Delete` 메서드가 필요합니다. 하나는 GET에 대한 것이며 다른 하나는 POST에 대한 것입니다. 두 메서드에는 동일한 매개 변수 서명이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="578fc-120">However, here you need two `Delete` methods -- one for GET and one for POST -- that both have the same parameter signature.</span></span> <span data-ttu-id="578fc-121">(모두 매개 변수로 단일 정수를 허용해야 합니다.)</span><span class="sxs-lookup"><span data-stu-id="578fc-121">(They both need to accept a single integer as a parameter.)</span></span>

<span data-ttu-id="578fc-122">이 문제에 대한 두 가지의 방법이 있습니다. 하나는 메서드에 서로 다른 이름을 지정하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="578fc-122">There are two approaches to this problem, one is to give the methods different names.</span></span> <span data-ttu-id="578fc-123">앞의 예에서 스캐폴딩 메커니즘이 수행한 것입니다.</span><span class="sxs-lookup"><span data-stu-id="578fc-123">That's what the scaffolding mechanism did in the preceding example.</span></span> <span data-ttu-id="578fc-124">그러나 이는 작은 문제를 가져옵니다. ASP.NET은 URL의 세그먼트를 이름으로 작업 메서드에 매핑하고 메서드의 이름을 바꾸면 정상적으로 라우팅하여 해당 메서드를 찾을 수 없게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="578fc-124">However, this introduces a small problem: ASP.NET maps segments of a URL to action methods by name, and if you rename a method, routing normally wouldn't be able to find that method.</span></span> <span data-ttu-id="578fc-125">솔루션은 예제에서 확인한 것으로, `ActionName("Delete")` 특성을 `DeleteConfirmed` 메서드에 추가하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="578fc-125">The solution is what you see in the example, which is to add the `ActionName("Delete")` attribute to the `DeleteConfirmed` method.</span></span> <span data-ttu-id="578fc-126">해당 특성은 POST 요청에 대한 /Delete/를 포함하는 URL이 `DeleteConfirmed` 메서드를 찾도록 라우팅 시스템에 대한 매핑을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="578fc-126">That attribute performs mapping for the routing system so that a URL that includes /Delete/ for a POST request will find the `DeleteConfirmed` method.</span></span>

<span data-ttu-id="578fc-127">동일한 이름 및 서명을 가진 메서드에 대한 또 다른 일반적인 해결책은 POST 메서드의 서명을 추가(사용되지 않는) 매개 변수를 포함하도록 인위적으로 변경하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="578fc-127">Another common work around for methods that have identical names and signatures is to artificially change the signature of the POST method to include an extra (unused) parameter.</span></span> <span data-ttu-id="578fc-128">즉, `notUsed` 매개 변수를 추가했을 때 이전 게시에서 수행했던 작업입니다.</span><span class="sxs-lookup"><span data-stu-id="578fc-128">That's what we did in a previous post when we added the `notUsed` parameter.</span></span> <span data-ttu-id="578fc-129">`[HttpPost] Delete` 메서드에 대해 여기에서 동일한 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="578fc-129">You could do the same thing here for the `[HttpPost] Delete` method:</span></span>

```csharp
// POST: Movies/Delete/6
[ValidateAntiForgeryToken]
public async Task<IActionResult> Delete(int id, bool notUsed)
```

### <a name="publish-to-azure"></a><span data-ttu-id="578fc-130">Azure에 게시</span><span class="sxs-lookup"><span data-stu-id="578fc-130">Publish to Azure</span></span>

<span data-ttu-id="578fc-131">Visual Studio를 사용하여 Azure에 이 앱을 게시하는 방법에 관한 지침은 [Visual Studio를 사용하여 Azure App Service에 ASP.NET Core 웹앱 게시](xref:tutorials/publish-to-azure-webapp-using-vs)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="578fc-131">See [Publish an ASP.NET Core web app to Azure App Service using Visual Studio](xref:tutorials/publish-to-azure-webapp-using-vs) for instructions on how to publish this app to Azure using Visual Studio.</span></span>  <span data-ttu-id="578fc-132">[명령줄](xref:tutorials/publish-to-azure-webapp-using-cli)에서도 앱을 게시할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="578fc-132">The app can also be published from the [command line](xref:tutorials/publish-to-azure-webapp-using-cli).</span></span>

<span data-ttu-id="578fc-133">ASP.NET Core MVC에 대한 이 소개를 완료해 주셔서 감사합니다.</span><span class="sxs-lookup"><span data-stu-id="578fc-133">Thanks for completing this introduction to ASP.NET Core MVC.</span></span> <span data-ttu-id="578fc-134">의견을 남겨 주세요.</span><span class="sxs-lookup"><span data-stu-id="578fc-134">We appreciate any comments you leave.</span></span> <span data-ttu-id="578fc-135">[MVC 및 EF Core 시작](xref:data/ef-mvc/intro)은 이 자습서에 대한 뛰어난 후속편입니다.</span><span class="sxs-lookup"><span data-stu-id="578fc-135">[Getting started with MVC and EF Core](xref:data/ef-mvc/intro) is an excellent follow up to this tutorial.</span></span>

>[!div class="step-by-step"]
[<span data-ttu-id="578fc-136">이전</span><span class="sxs-lookup"><span data-stu-id="578fc-136">Previous</span></span>](validation.md)
