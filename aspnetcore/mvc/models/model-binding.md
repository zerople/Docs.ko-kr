---
title: "모델 바인딩"
author: rick-anderson
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: b355a48e-a15c-4d58-b69c-899763613a97
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/models/model-binding
ms.openlocfilehash: 92085829d2a37a2aa6080aeb34a5e14be95e02d8
ms.sourcegitcommit: 6e83c55eb0450a3073ef2b95fa5f5bcb20dbbf89
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/28/2017
---
# <a name="model-binding"></a><span data-ttu-id="bc0ee-103">모델 바인딩</span><span class="sxs-lookup"><span data-stu-id="bc0ee-103">Model Binding</span></span>

<span data-ttu-id="bc0ee-104">으로 [Rachel Appel](https://github.com/rachelappel)</span><span class="sxs-lookup"><span data-stu-id="bc0ee-104">By [Rachel Appel](https://github.com/rachelappel)</span></span>

## <a name="introduction-to-model-binding"></a><span data-ttu-id="bc0ee-105">모델 바인딩 소개</span><span class="sxs-lookup"><span data-stu-id="bc0ee-105">Introduction to model binding</span></span>

<span data-ttu-id="bc0ee-106">ASP.NET Core mvc에서 모델 바인딩 동작 메서드 매개 변수를 HTTP 요청에서 데이터를 매핑합니다.</span><span class="sxs-lookup"><span data-stu-id="bc0ee-106">Model binding in ASP.NET Core MVC maps data from HTTP requests to action method parameters.</span></span> <span data-ttu-id="bc0ee-107">매개 변수 문자열, 정수, float, 등 단순 형식일 수 있습니다 또는 복합 형식이 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bc0ee-107">The parameters may be simple types such as strings, integers, or floats, or they may be complex types.</span></span> <span data-ttu-id="bc0ee-108">크기 또는 데이터의 복잡성에 관계 없이 자주 반복 되는 시나리오는 들어오는 데이터를 해당 하는 도구에 매핑 때문에 이것이 MVC의 유용한 기능입니다.</span><span class="sxs-lookup"><span data-stu-id="bc0ee-108">This is a great feature of MVC because mapping incoming data to a counterpart is an often repeated scenario, regardless of size or complexity of the data.</span></span> <span data-ttu-id="bc0ee-109">MVC는 하므로 개발자는 약간 다른 버전의 모든 응용 프로그램에서 같은 코드를 다시 작성 유지할 필요가 없습니다 바인딩을 도외시 함으로써이 문제를 해결 합니다.</span><span class="sxs-lookup"><span data-stu-id="bc0ee-109">MVC solves this problem by abstracting binding away so developers don't have to keep rewriting a slightly different version of that same code in every app.</span></span> <span data-ttu-id="bc0ee-110">사용자 고유의 텍스트 형식 변환기 코드를 작성 하는 것은 지루한, 오류가 발생할 가능성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bc0ee-110">Writing your own text to type converter code is tedious, and error prone.</span></span>

## <a name="how-model-binding-works"></a><span data-ttu-id="bc0ee-111">모델 바인딩 작동 방식</span><span class="sxs-lookup"><span data-stu-id="bc0ee-111">How model binding works</span></span>

<span data-ttu-id="bc0ee-112">MVC는 HTTP 요청을 받으면 컨트롤러의 특정 동작 메서드에 라우팅합니다 것입니다.</span><span class="sxs-lookup"><span data-stu-id="bc0ee-112">When MVC receives an HTTP request, it routes it to a specific action method of a controller.</span></span> <span data-ttu-id="bc0ee-113">동작 메서드는 경로 데이터에 포함 된 내용에 따라 실행를 결정 한 후 값 HTTP 요청에서 해당 작업 메서드의 매개 변수를 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="bc0ee-113">It determines which action method to run based on what is in the route data, then it binds values from the HTTP request to that action method's parameters.</span></span> <span data-ttu-id="bc0ee-114">예를 들어 다음 URL:</span><span class="sxs-lookup"><span data-stu-id="bc0ee-114">For example, consider the following URL:</span></span>

`http://contoso.com/movies/edit/2`

<span data-ttu-id="bc0ee-115">경로 템플릿을 다음과 같은, 이후 `{controller=Home}/{action=Index}/{id?}`, `movies/edit/2` 라우팅하는 `Movies` 컨트롤러 및 해당 `Edit` 작업 메서드.</span><span class="sxs-lookup"><span data-stu-id="bc0ee-115">Since the route template looks like this, `{controller=Home}/{action=Index}/{id?}`, `movies/edit/2` routes to the `Movies` controller, and its `Edit` action method.</span></span> <span data-ttu-id="bc0ee-116">또한 호출 하는 선택적 매개 변수 허용 `id`합니다.</span><span class="sxs-lookup"><span data-stu-id="bc0ee-116">It also accepts an optional parameter called `id`.</span></span> <span data-ttu-id="bc0ee-117">동작 메서드에 대 한 코드는 코드는 다음과 같아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="bc0ee-117">The code for the action method should look something like this:</span></span>

```csharp
public IActionResult Edit(int? id)
   ```

<span data-ttu-id="bc0ee-118">참고: URL 경로에 있는 문자열은 대/소문자 구분.</span><span class="sxs-lookup"><span data-stu-id="bc0ee-118">Note: The strings in the URL route are not case sensitive.</span></span>

<span data-ttu-id="bc0ee-119">MVC 요청 데이터를 이름으로 작업 매개 변수를 바인딩할 하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="bc0ee-119">MVC will try to bind request data to the action parameters by name.</span></span> <span data-ttu-id="bc0ee-120">MVC는 매개 변수 이름 및 설정 가능한 공용 속성의 이름을 사용 하 여 각 매개 변수에 값을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="bc0ee-120">MVC will look for values for each parameter using the parameter name and the names of its public settable properties.</span></span> <span data-ttu-id="bc0ee-121">위의 예제에서 유일한 action 매개 변수 이름은 `id`는 MVC 경로 값에 같은 이름의 값에 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="bc0ee-121">In the above example, the only action parameter is named `id`, which MVC binds to the value with the same name in the route values.</span></span> <span data-ttu-id="bc0ee-122">경로 값 외에도 MVC는 데이터 요청의 다양 한 부분에서 바인딩한 집합 순서로 작업을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="bc0ee-122">In addition to route values MVC will bind data from various parts of the request and it does so in a set order.</span></span> <span data-ttu-id="bc0ee-123">다음은 모델 바인딩 표시가 보이는 순서로 데이터 원본의 목록이입니다.</span><span class="sxs-lookup"><span data-stu-id="bc0ee-123">Below is a list of the data sources in the order that model binding looks through them:</span></span>

1. <span data-ttu-id="bc0ee-124">`Form values`: 이들은 POST 메서드를 사용 하 여 HTTP 요청에서 이동 하는 폼 값입니다.</span><span class="sxs-lookup"><span data-stu-id="bc0ee-124">`Form values`: These are form values that go in the HTTP request using the POST method.</span></span> <span data-ttu-id="bc0ee-125">(jQuery POST 요청 포함).</span><span class="sxs-lookup"><span data-stu-id="bc0ee-125">(including jQuery POST requests).</span></span>

2. <span data-ttu-id="bc0ee-126">`Route values`:에서 제공 하는 경로 값 집합이 [라우팅](../../fundamentals/routing.md)</span><span class="sxs-lookup"><span data-stu-id="bc0ee-126">`Route values`: The set of route values provided by [Routing](../../fundamentals/routing.md)</span></span>

3. <span data-ttu-id="bc0ee-127">`Query strings`쿼리 문자열: URI의 부분입니다.</span><span class="sxs-lookup"><span data-stu-id="bc0ee-127">`Query strings`: The query string part of the URI.</span></span>

<!-- DocFX BUG
The link works but generates an error when building with DocFX
@fundamentals/routing
[Routing](xref:fundamentals/routing)
-->

<span data-ttu-id="bc0ee-128">참고: 폼 값, 경로 데이터 및 쿼리 문자열은 모두 이름 값 쌍으로 저장 됩니다.</span><span class="sxs-lookup"><span data-stu-id="bc0ee-128">Note: Form values, route data, and query strings are all stored as name-value pairs.</span></span>

<span data-ttu-id="bc0ee-129">명명 된 키에 대 한 모델 바인딩 요청 이후 `id` 되어 있으므로 없습니다 라는 `id` 폼 값에 그 이후에 이동 해당 키를 찾고 경로 값에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bc0ee-129">Since model binding asked for a key named `id` and there is nothing named `id` in the form values, it moved on to the route values looking for that key.</span></span> <span data-ttu-id="bc0ee-130">이 예제에서 일치 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="bc0ee-130">In our example, it's a match.</span></span> <span data-ttu-id="bc0ee-131">바인딩이 일어나기 및 값 2 정수로 변환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="bc0ee-131">Binding happens, and the value is converted to the integer 2.</span></span> <span data-ttu-id="bc0ee-132">편집 (문자열 id)를 사용 하 여 동일한 요청 "2" 문자열로 변환 합니다.</span><span class="sxs-lookup"><span data-stu-id="bc0ee-132">The same request using Edit(string id) would convert to the string "2".</span></span>

<span data-ttu-id="bc0ee-133">지금까지 예에서는 단순 형식을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="bc0ee-133">So far the example uses simple types.</span></span> <span data-ttu-id="bc0ee-134">MVC에서 단순 유형은 모든.NET 기본 형식 또는 문자열 형식 변환기는 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="bc0ee-134">In MVC simple types are any .NET primitive type or type with a string type converter.</span></span> <span data-ttu-id="bc0ee-135">동작 메서드 매개 변수가 클래스와 같은 경우는 `Movie` 속성, MVC의 모델 바인딩을 원활 하 게 처리 처럼 단순 및 복합 유형이 포함 되어 있는 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="bc0ee-135">If the action method's parameter were a class such as the `Movie` type, which contains both simple and complex types as properties, MVC's model binding will still handle it nicely.</span></span> <span data-ttu-id="bc0ee-136">일치 하는 항목을 찾고 복합 형식의 속성을 이동 하 리플렉션 및 재귀를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="bc0ee-136">It uses reflection and recursion to traverse the properties of complex types looking for matches.</span></span> <span data-ttu-id="bc0ee-137">모델 바인딩 패턴을 찾고 *parameter_name.property_name* 속성에 값을 바인딩할 합니다.</span><span class="sxs-lookup"><span data-stu-id="bc0ee-137">Model binding looks for the pattern *parameter_name.property_name* to bind values to properties.</span></span> <span data-ttu-id="bc0ee-138">이 폼의 일치 하는 값을 찾지 못하는 경우 속성 이름만 사용 하 여 바인딩하려면 시도 합니다.</span><span class="sxs-lookup"><span data-stu-id="bc0ee-138">If it doesn't find matching values of this form, it will attempt to bind using just the property name.</span></span> <span data-ttu-id="bc0ee-139">와 같은 이러한 형식에 대 한 `Collection` 에 일치 하는 항목 형식, 모델 바인딩 찾습니다 *p a r a [index]* 또는 그냥 *[index]*합니다.</span><span class="sxs-lookup"><span data-stu-id="bc0ee-139">For those types such as `Collection` types, model binding looks for matches to *parameter_name[index]* or just *[index]*.</span></span> <span data-ttu-id="bc0ee-140">모델 바인딩에서는 `Dictionary` 요청 마찬가지로, 형식 *p a r a [key]* 또는 그냥 *[key]*키 모두 간단한 유형으로, 합니다.</span><span class="sxs-lookup"><span data-stu-id="bc0ee-140">Model binding treats  `Dictionary` types similarly, asking for *parameter_name[key]* or just *[key]*, as long as the keys are simple types.</span></span> <span data-ttu-id="bc0ee-141">HTML 필드 이름 및 동일한 모델 유형에 대해 생성 된 태그 도우미 지원 되는 키가 일치 합니다.</span><span class="sxs-lookup"><span data-stu-id="bc0ee-141">Keys that are supported match the field names HTML and tag helpers generated for the same model type.</span></span> <span data-ttu-id="bc0ee-142">이 통해 왕복 값 양식 필드에 계속의 편의 위해 사용자의 입력으로 채워진 예를 들어 때 만들기 또는 편집에서 바인딩된 데이터 유효성 검사를 통과 하지 못했습니다.</span><span class="sxs-lookup"><span data-stu-id="bc0ee-142">This enables round-tripping values so that the form fields remain filled with the user's input for their convenience, for example, when bound data from a create or edit did not pass validation.</span></span>

<span data-ttu-id="bc0ee-143">클래스 작업이 발생 하는 바인딩에 대 한 순서 대로 공용 기본 생성자가 있어야 하 고 바인딩되어야 멤버에는 쓰기 가능한 공용 속성 이어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="bc0ee-143">In order for binding to happen the class must have a public default constructor and member to be bound must be public writable properties.</span></span> <span data-ttu-id="bc0ee-144">클래스는 인스턴스화할 수 공용 기본 생성자를 사용 하 여 모델 바인딩 경우에 속성을 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bc0ee-144">When model binding happens the class will only be instantiated using the public default constructor, then the properties can be set.</span></span>

<span data-ttu-id="bc0ee-145">매개 변수가 바인딩되면 모델 바인딩을 찾고 해당 이름 가진 값에 대 한 중지 하 고에 대해 다음 매개 변수를 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="bc0ee-145">When a parameter is bound, model binding stops looking for values with that name and it moves on to bind the next parameter.</span></span> <span data-ttu-id="bc0ee-146">바인딩 실패, MVC 오류를 throw 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="bc0ee-146">If binding fails, MVC does not throw an error.</span></span> <span data-ttu-id="bc0ee-147">선택 하 여 모델 상태 오류 쿼리할 수 있습니다는 `ModelState.IsValid` 속성입니다.</span><span class="sxs-lookup"><span data-stu-id="bc0ee-147">You can query for model state errors by checking the `ModelState.IsValid` property.</span></span>

<span data-ttu-id="bc0ee-148">참고: 각 항목에는 컨트롤러의 `ModelState` 속성은 한 `ModelStateEntry` 포함 하는 `Errors property`합니다.</span><span class="sxs-lookup"><span data-stu-id="bc0ee-148">Note: Each entry in the controller's `ModelState` property is a `ModelStateEntry` containing an `Errors property`.</span></span> <span data-ttu-id="bc0ee-149">것은이 컬렉션을 직접 쿼리할 필요가 거의 없습니다.</span><span class="sxs-lookup"><span data-stu-id="bc0ee-149">It's rarely necessary to query this collection yourself.</span></span> <span data-ttu-id="bc0ee-150">대신 `ModelState.IsValid` 를 사용하세요.</span><span class="sxs-lookup"><span data-stu-id="bc0ee-150">Use `ModelState.IsValid` instead.</span></span>

<span data-ttu-id="bc0ee-151">또한 MVC 모델 바인딩 수행할 때 고려해 야 하는 특수 데이터 형식도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bc0ee-151">Additionally, there are some special data types that MVC must consider when performing model binding:</span></span>

* <span data-ttu-id="bc0ee-152">`IFormFile``IEnumerable<IFormFile>`: HTTP 요청의 일부인 하나 이상의 업로드 된 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="bc0ee-152">`IFormFile`, `IEnumerable<IFormFile>`: One or more uploaded files that are part of the HTTP request.</span></span>

* <span data-ttu-id="bc0ee-153">`CancellationToken`: 비동기 컨트롤러의 작업을 취소 하는 데 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="bc0ee-153">`CancellationToken`: Used to cancel activity in asynchronous controllers.</span></span>

<span data-ttu-id="bc0ee-154">이러한 형식은 클래스 형식에 액션 매개 변수 또는 속성에 바인딩할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bc0ee-154">These types can be bound to action parameters or to properties on a class type.</span></span>

<span data-ttu-id="bc0ee-155">모델 바인딩을 완료 되 면 [유효성 검사](validation.md) 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="bc0ee-155">Once model binding is complete, [Validation](validation.md) occurs.</span></span> <span data-ttu-id="bc0ee-156">기본 모델 바인딩 대부분의 개발 시나리오에 대 한 간단 하 게 합니다.</span><span class="sxs-lookup"><span data-stu-id="bc0ee-156">Default model binding works great for the vast majority of development scenarios.</span></span> <span data-ttu-id="bc0ee-157">또한 하므로 기본 제공 동작을 사용자 지정할 수 고유한 요구 사항이 있는 경우에 확장입니다.</span><span class="sxs-lookup"><span data-stu-id="bc0ee-157">It is also extensible so if you have unique needs you can customize the built-in behavior.</span></span>

## <a name="customize-model-binding-behavior-with-attributes"></a><span data-ttu-id="bc0ee-158">모델 바인딩 동작 특성으로 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="bc0ee-158">Customize model binding behavior with attributes</span></span>

<span data-ttu-id="bc0ee-159">MVC에는 다른 소스에 해당 기본 모델 바인딩 동작을 직접 사용할 수 있는 몇 가지 특성이 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bc0ee-159">MVC contains several attributes that you can use to direct its default model binding behavior to a different source.</span></span> <span data-ttu-id="bc0ee-160">예를 들어, 바인딩이 속성에 대해 필요한 지 여부 또는 것 전혀 사용 하 여 발생 해서는 안 되는 경우를 지정할 수 있습니다는 `[BindRequired]` 또는 `[BindNever]` 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="bc0ee-160">For example, you can specify whether binding is required for a property, or if it should never happen at all by using the `[BindRequired]` or `[BindNever]` attributes.</span></span> <span data-ttu-id="bc0ee-161">또는 기본 데이터 원본을 무시 하 고 모델 바인더의 데이터 원본을 지정할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bc0ee-161">Alternatively, you can override the default data source, and specify the model binder's data source.</span></span> <span data-ttu-id="bc0ee-162">다음은 모델 바인딩 특성의 목록:</span><span class="sxs-lookup"><span data-stu-id="bc0ee-162">Below is a list of model binding attributes:</span></span>

* <span data-ttu-id="bc0ee-163">`[BindRequired]`:이 특성 바인딩이 발생 수 없는 경우 모델 상태 오류를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="bc0ee-163">`[BindRequired]`: This attribute adds a model state error if binding cannot occur.</span></span>

* <span data-ttu-id="bc0ee-164">`[BindNever]`:이 매개 변수에 하지 바인딩할 모델 바인더를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="bc0ee-164">`[BindNever]`: Tells the model binder to never bind to this parameter.</span></span>

* <span data-ttu-id="bc0ee-165">`[FromHeader]``[FromQuery]`, `[FromRoute]`, `[FromForm]`: 이러한를 사용 하 여 적용 하려는 정확한 바인딩 소스를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="bc0ee-165">`[FromHeader]`, `[FromQuery]`, `[FromRoute]`, `[FromForm]`: Use these to specify the exact binding source you want to apply.</span></span>

* <span data-ttu-id="bc0ee-166">`[FromServices]`:이 특성은 [종속성 주입](../../fundamentals/dependency-injection.md) 서비스에서 매개 변수를 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="bc0ee-166">`[FromServices]`: This attribute uses [dependency injection](../../fundamentals/dependency-injection.md) to bind parameters from services.</span></span>

* <span data-ttu-id="bc0ee-167">`[FromBody]`: 요청 본문에서 데이터를 바인딩할 구성 된 포맷터를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="bc0ee-167">`[FromBody]`: Use the configured formatters to bind data from the request body.</span></span> <span data-ttu-id="bc0ee-168">포맷터 요청의 콘텐츠 형식에 따라 선택 됩니다.</span><span class="sxs-lookup"><span data-stu-id="bc0ee-168">The formatter is selected based on content type of the request.</span></span>

* <span data-ttu-id="bc0ee-169">`[ModelBinder]`: 기본 모델 바인더, 바인딩 소스 및 이름을 재정의 하는 데 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="bc0ee-169">`[ModelBinder]`: Used to override the default model binder, binding source and name.</span></span>

<span data-ttu-id="bc0ee-170">특성은 모델 바인딩의 기본 동작을 재정의 해야 할 매우 유용한 도구입니다.</span><span class="sxs-lookup"><span data-stu-id="bc0ee-170">Attributes are very helpful tools when you need to override the default behavior of model binding.</span></span>

## <a name="binding-formatted-data-from-the-request-body"></a><span data-ttu-id="bc0ee-171">요청 본문에서 데이터 형식이 지정 되는 바인딩</span><span class="sxs-lookup"><span data-stu-id="bc0ee-171">Binding formatted data from the request body</span></span>

<span data-ttu-id="bc0ee-172">요청 데이터는 다양 한 형식의 JSON, XML 및 다른 많은 요인을 포함 하 여 가져올 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bc0ee-172">Request data can come in a variety of formats including JSON, XML and many others.</span></span> <span data-ttu-id="bc0ee-173">[FromBody] 특성을 사용 하 여 데이터 요청 본문에는 매개 변수를 바인딩할 것인지 지정 MVC 포맷터의 구성 된 집합을 사용 하 여 해당 콘텐츠 형식에 따라 요청 데이터를 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="bc0ee-173">When you use the [FromBody] attribute to indicate that you want to bind a parameter to data in the request body, MVC uses a configured set of formatters to handle the request data based on its content type.</span></span> <span data-ttu-id="bc0ee-174">MVC에는 기본적으로는 `JsonInputFormatter` XML 및 다른 사용자 지정 형식도 처리 하기 위한 추가 포맷터 추가할 수 있지만 JSON 데이터를 처리에 대 한 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="bc0ee-174">By default MVC includes a `JsonInputFormatter` class for handling JSON data, but you can add additional formatters for handling XML and other custom formats.</span></span>

> [!NOTE]
> <span data-ttu-id="bc0ee-175">있을 수 최대로 데코레이팅 동작 마다 하나의 매개 변수 `[FromBody]`합니다.</span><span class="sxs-lookup"><span data-stu-id="bc0ee-175">There can be at most one parameter per action decorated with `[FromBody]`.</span></span> <span data-ttu-id="bc0ee-176">ASP.NET Core MVC 런타임에 포맷터에 요청 스트림의 읽을의 책임을 위임 합니다.</span><span class="sxs-lookup"><span data-stu-id="bc0ee-176">The ASP.NET Core MVC run-time delegates the responsibility of reading the request stream to the formatter.</span></span> <span data-ttu-id="bc0ee-177">요청 스트림을 매개 변수에 대해 읽기, 되 면 일반적으로 수 없으면 다른 바인딩에 대 한 다시 요청 스트림의 읽을 `[FromBody]` 매개 변수입니다.</span><span class="sxs-lookup"><span data-stu-id="bc0ee-177">Once the request stream is read for a parameter, it's generally not possible to read the request stream again for binding other `[FromBody]` parameters.</span></span>

> [!NOTE]
> <span data-ttu-id="bc0ee-178">`JsonInputFormatter` 기반을 기본 포맷터가 [Json.NET](https://www.newtonsoft.com/json)합니다.</span><span class="sxs-lookup"><span data-stu-id="bc0ee-178">The `JsonInputFormatter` is the default formatter and is based on [Json.NET](https://www.newtonsoft.com/json).</span></span>

<span data-ttu-id="bc0ee-179">ASP.NET 선택에 따라 입력된 포맷터는 [Content-type](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html) 헤더 및 매개 변수의 형식 있으면 다음을 지정 하지 않으면 적용 되는 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="bc0ee-179">ASP.NET selects input formatters based on the [Content-Type](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html) header and the type of the parameter, unless there is an attribute applied to it specifying otherwise.</span></span> <span data-ttu-id="bc0ee-180">XML을 사용 하 시겠습니까 아니면 다른 형식 구성 해야에서 하는 경우는 *Startup.cs* 파일인 없지만 년 5 월 첫 번째가에 대 한 참조를 얻으려고 `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet을 사용 하 여 합니다.</span><span class="sxs-lookup"><span data-stu-id="bc0ee-180">If you'd like to use XML or another format you must configure it in the *Startup.cs* file, but you may first have to obtain a reference to `Microsoft.AspNetCore.Mvc.Formatters.Xml` using NuGet.</span></span> <span data-ttu-id="bc0ee-181">시작 코드는 코드는 다음과 같아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="bc0ee-181">Your startup code should look something like this:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddXmlSerializerFormatters();
   }
```

<span data-ttu-id="bc0ee-182">코드에서 *Startup.cs* 파일에 포함 되어는 `ConfigureServices` 메서드는 `services` 인수 ASP.NET 응용 프로그램에 대 한 서비스를 만드는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bc0ee-182">Code in the *Startup.cs* file contains a `ConfigureServices` method with a `services` argument you can use to build up services for your ASP.NET app.</span></span> <span data-ttu-id="bc0ee-183">이 샘플에서는 XML 포맷터 MVC는이 응용 프로그램에 제공 하는 서비스로 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="bc0ee-183">In the sample, we are adding an XML formatter as a service that MVC will provide for this app.</span></span> <span data-ttu-id="bc0ee-184">`options` 에 전달 된 인수는 `AddMvc` 메서드 추가 하 고 응용 프로그램 시작 시 MVC에서 필터, 포맷터 및 기타 시스템 옵션을 관리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bc0ee-184">The `options` argument passed into the `AddMvc` method allows you to add and manage filters, formatters, and other system options from MVC upon app startup.</span></span> <span data-ttu-id="bc0ee-185">다음 적용 하는 `Consumes` 특성을 컨트롤러 클래스 또는 작업 메서드를 원하는 형식으로 작동 합니다.</span><span class="sxs-lookup"><span data-stu-id="bc0ee-185">Then apply the `Consumes` attribute to controller classes or action methods to work with the format you want.</span></span>

### <a name="custom-model-binding"></a><span data-ttu-id="bc0ee-186">사용자 지정 모델 바인딩</span><span class="sxs-lookup"><span data-stu-id="bc0ee-186">Custom Model Binding</span></span>

<span data-ttu-id="bc0ee-187">모델 바인딩을 고유의 사용자 지정 모델 바인더를 작성 하 여 확장할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bc0ee-187">You can extend model binding by writing your own custom model binders.</span></span> <span data-ttu-id="bc0ee-188">에 대 한 자세한 내용은 [사용자 지정 모델 바인딩](../advanced/custom-model-binding.md)합니다.</span><span class="sxs-lookup"><span data-stu-id="bc0ee-188">Learn more about [custom model binding](../advanced/custom-model-binding.md).</span></span>
