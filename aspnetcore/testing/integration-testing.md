---
title: "ASP.NET Core에서 통합 테스트"
author: ardalis
description: "응용 프로그램의 구성 요소가 제대로 작동 하는지 확인 하는 테스트 ASP.NET Core 통합을 사용 하는 방법."
keywords: "통합 테스트, Razor, ASP.NET Core"
ms.author: riande
manager: wpickett
ms.date: 09/25/2017
ms.topic: article
ms.assetid: 40d534f2-89b3-4b09-9c2c-3494bf9991c9
ms.technology: aspnet
ms.prod: asp.net-core
uid: testing/integration-testing
ms.openlocfilehash: fab1fb0e64debd8488713b3518cb3bc90182616b
ms.sourcegitcommit: 6e83c55eb0450a3073ef2b95fa5f5bcb20dbbf89
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/28/2017
---
# <a name="integration-testing-in-aspnet-core"></a><span data-ttu-id="4e97c-104">ASP.NET Core에서 통합 테스트</span><span class="sxs-lookup"><span data-stu-id="4e97c-104">Integration testing in ASP.NET Core</span></span>

<span data-ttu-id="4e97c-105">으로 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="4e97c-105">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="4e97c-106">통합 테스트 함께 어셈블되어 때 응용 프로그램의 구성 요소가 제대로 작동 하는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="4e97c-106">Integration testing ensures that an application's components function correctly when assembled together.</span></span> <span data-ttu-id="4e97c-107">ASP.NET Core 지원 통합 단위 테스트 프레임 워크 및 네트워크 오버 헤드 없이 요청을 처리 하는 데 사용할 수 있는 기본 제공 테스트 웹 호스트를 사용 하 여 테스트 합니다.</span><span class="sxs-lookup"><span data-stu-id="4e97c-107">ASP.NET Core supports integration testing using unit test frameworks and a built-in test web host that can be used to handle requests without network overhead.</span></span>

[<span data-ttu-id="4e97c-108">샘플 코드 보기 또는 다운로드</span><span class="sxs-lookup"><span data-stu-id="4e97c-108">View or download sample code</span></span>](https://github.com/aspnet/Docs/tree/master/aspnetcore/testing/integration-testing/sample)

## <a name="introduction-to-integration-testing"></a><span data-ttu-id="4e97c-109">통합 테스트 소개</span><span class="sxs-lookup"><span data-stu-id="4e97c-109">Introduction to integration testing</span></span>

<span data-ttu-id="4e97c-110">통합 테스트 응용 프로그램의 다른 부분 올바르게 작동 하는지 함께 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="4e97c-110">Integration tests verify that different parts of an application work correctly together.</span></span> <span data-ttu-id="4e97c-111">와 달리 [단위 테스트](https://docs.microsoft.com/dotnet/articles/core/testing/unit-testing-with-dotnet-test), 데이터베이스, 파일 시스템, 네트워크 리소스 또는 웹 요청 및 응답 예: 응용 프로그램 인프라 문제가 솔루션에 통합 테스트 합니다.</span><span class="sxs-lookup"><span data-stu-id="4e97c-111">Unlike [Unit testing](https://docs.microsoft.com/dotnet/articles/core/testing/unit-testing-with-dotnet-test), integration tests frequently involve application infrastructure concerns, such as a database, file system, network resources, or web requests and responses.</span></span> <span data-ttu-id="4e97c-112">단위 테스트 fakes 또는 이러한 여러 가지이 문제 대신 모의 개체를 사용 하지만 통합 테스트의 목적은 시스템 이러한 시스템에 예상 대로 작동 하는지 확인 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="4e97c-112">Unit tests use fakes or mock objects in place of these concerns, but the purpose of integration tests is to confirm that the system works as expected with these systems.</span></span>

<span data-ttu-id="4e97c-113">통합 테스트를 더 큰 세그먼트의 코드를 실행 하 고 인프라 요소에 의존 하므로 경향이 훨씬 크기 단위 테스트 보다 느립니다.</span><span class="sxs-lookup"><span data-stu-id="4e97c-113">Integration tests, because they exercise larger segments of code and because they rely on infrastructure elements, tend to be orders of magnitude slower than unit tests.</span></span> <span data-ttu-id="4e97c-114">따라서 것이 좋습니다는 통합 테스트의 수를 제한 하에 작성 특히 경우 단위 테스트와 동일한 동작을 테스트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4e97c-114">Thus, it's a good idea to limit how many integration tests you write, especially if you can test the same behavior with a unit test.</span></span>

> [!NOTE]
> <span data-ttu-id="4e97c-115">통합 테스트 또는 단위 테스트를 사용 하 여 일부 동작을 테스트할 수 있습니다는 것이 거의 더 빠를 수 있으므로 단위 테스트를 선호 합니다.</span><span class="sxs-lookup"><span data-stu-id="4e97c-115">If some behavior can be tested using either a unit test or an integration test, prefer the unit test, since it will be almost always be faster.</span></span> <span data-ttu-id="4e97c-116">수십 또는 수백 개의 서로 다른 여러 입력을 사용 하 여 단위 테스트 하지만 약간의 가장 중요 한 시나리오를 다룹니다 통합 테스트를 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4e97c-116">You might have dozens or hundreds of unit tests with many different inputs but just a handful of integration tests covering the most important scenarios.</span></span>

<span data-ttu-id="4e97c-117">사용자 고유의 메서드 내에서 논리를 테스트는 일반적으로 단위 테스트의 도메인입니다.</span><span class="sxs-lookup"><span data-stu-id="4e97c-117">Testing the logic within your own methods is usually the domain of unit tests.</span></span> <span data-ttu-id="4e97c-118">예를 들어 ASP.NET Core 또는 데이터베이스와 프레임 워크 내에서 응용 프로그램이 작동 하는 방법을 테스트는 통합 테스트 여기서 이루어지는입니다.</span><span class="sxs-lookup"><span data-stu-id="4e97c-118">Testing how your application works within its framework, for example with ASP.NET Core, or with a database is where integration tests come into play.</span></span> <span data-ttu-id="4e97c-119">데이터베이스에 행을 쓰고 다시 읽을 수 있는지를 확인 하려면 너무 많은 통합 테스트 필요 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4e97c-119">It doesn't take too many integration tests to confirm that you're able to write a row to the database and read it back.</span></span> <span data-ttu-id="4e97c-120">데이터 액세스 코드의 모든 가능한 순열 테스트할 필요가 없습니다-충분 하면 응용 프로그램이 제대로 작동 한다는 확신을 테스트 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4e97c-120">You don't need to test every possible permutation of your data access code - you only need to test enough to give you confidence that your application is working properly.</span></span>

## <a name="integration-testing-aspnet-core"></a><span data-ttu-id="4e97c-121">ASP.NET Core를 테스트 하는 통합</span><span class="sxs-lookup"><span data-stu-id="4e97c-121">Integration testing ASP.NET Core</span></span>

<span data-ttu-id="4e97c-122">실행된 통합 테스트 하도록 설정 하려면을 테스트 프로젝트 만들기, ASP.NET Core 웹 프로젝트에 대 한 참조를 추가 및 test runner를 설치 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4e97c-122">To get set up to run integration tests, you'll need to create a test project, add a reference to your ASP.NET Core web project, and install a test runner.</span></span> <span data-ttu-id="4e97c-123">이 프로세스에 설명 되어는 [단위 테스트](https://docs.microsoft.com/dotnet/articles/core/testing/unit-testing-with-dotnet-test) 테스트 및 테스트 및 테스트 클래스의 이름을 지정 하는 것에 대 한 권장 사항 실행 대 한 자세한 지침과 설명서입니다.</span><span class="sxs-lookup"><span data-stu-id="4e97c-123">This process is described in the [Unit testing](https://docs.microsoft.com/dotnet/articles/core/testing/unit-testing-with-dotnet-test) documentation, along with more detailed instructions on running tests and recommendations for naming your tests and test classes.</span></span>

> [!NOTE]
> <span data-ttu-id="4e97c-124">단위 테스트 및 서로 다른 프로젝트를 사용 하 여 통합 테스트를 구분 합니다.</span><span class="sxs-lookup"><span data-stu-id="4e97c-124">Separate your unit tests and your integration tests using different projects.</span></span> <span data-ttu-id="4e97c-125">단위 테스트에는 인프라 문제가 실수로 인해 발생 하지 않는 고 쉽게 실행 하려면 테스트 집합을 선택할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4e97c-125">This helps ensure you don't accidentally introduce infrastructure concerns into your unit tests and lets you easily choose which set of tests to run.</span></span>

### <a name="the-test-host"></a><span data-ttu-id="4e97c-126">테스트 호스트</span><span class="sxs-lookup"><span data-stu-id="4e97c-126">The Test Host</span></span>

<span data-ttu-id="4e97c-127">ASP.NET Core 통합 테스트 프로젝트에 추가할 수 있으며, 호스팅하는 데 사용 ASP.NET Core 응용 프로그램의 경우 실제 웹 호스트에 대 한 필요 없이 서비스 테스트를 요청 하는 테스트 호스트를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="4e97c-127">ASP.NET Core includes a test host that can be added to integration test projects and used to host ASP.NET Core applications, serving test requests without the need for a real web host.</span></span> <span data-ttu-id="4e97c-128">제공 된 샘플에 사용 하도록 구성 된 통합 테스트 프로젝트에 포함 되어 [xUnit](https://xunit.github.io) 와 테스트 호스트 합니다.</span><span class="sxs-lookup"><span data-stu-id="4e97c-128">The provided sample includes an integration test project which has been configured to use [xUnit](https://xunit.github.io) and the Test Host.</span></span> <span data-ttu-id="4e97c-129">사용 하 여는 `Microsoft.AspNetCore.TestHost` NuGet 패키지 합니다.</span><span class="sxs-lookup"><span data-stu-id="4e97c-129">It uses the `Microsoft.AspNetCore.TestHost` NuGet package.</span></span>

<span data-ttu-id="4e97c-130">한 번의 `Microsoft.AspNetCore.TestHost` 패키지는 프로젝트에 포함 된를 만들고 구성할 수 있습니다는 `TestServer` 테스트에서 합니다.</span><span class="sxs-lookup"><span data-stu-id="4e97c-130">Once the `Microsoft.AspNetCore.TestHost` package is included in the project, you'll be able to create and configure a `TestServer` in your tests.</span></span> <span data-ttu-id="4e97c-131">다음 테스트에는 사이트의 루트에 대 한 요청 "Hello World!"을 반환 하는지 확인 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="4e97c-131">The following test shows how to verify that a request made to the root of a site returns "Hello World!"</span></span> <span data-ttu-id="4e97c-132">고 해야 성공적으로 실행 된 기본값에 대 한 Visual Studio에서 만든 ASP.NET Core 빈 웹 템플릿.</span><span class="sxs-lookup"><span data-stu-id="4e97c-132">and should run successfully against the default ASP.NET Core Empty Web template created by Visual Studio.</span></span>

[!code-csharp[Main](../testing/integration-testing/sample/test/PrimeWeb.IntegrationTests/PrimeWebDefaultRequestShould.cs?name=snippet_WebDefault&highlight=7,16,22)]

<span data-ttu-id="4e97c-133">이 테스트 정렬 Act Assert 패턴을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="4e97c-133">This test is using the Arrange-Act-Assert pattern.</span></span> <span data-ttu-id="4e97c-134">인스턴스를 만드는 생성자에서 정렬 단계는 수행 `TestServer`합니다.</span><span class="sxs-lookup"><span data-stu-id="4e97c-134">The Arrange step is done in the constructor, which creates an instance of `TestServer`.</span></span> <span data-ttu-id="4e97c-135">구성 된 `WebHostBuilder` 만드는 데 사용할 수는 `TestHost`이 예에서 `Configure` 테스트 (SUT)의 대상 시스템에 있는 메서드의 `Startup` 클래스에 전달 됩니다는 `WebHostBuilder`합니다.</span><span class="sxs-lookup"><span data-stu-id="4e97c-135">A configured `WebHostBuilder` will be used to create a `TestHost`; in this example, the `Configure` method from the system under test (SUT)'s `Startup` class is passed to the `WebHostBuilder`.</span></span> <span data-ttu-id="4e97c-136">이 메서드는의 요청 파이프라인을 구성 하는 `TestServer` SUT 서버 구성 하는 방법에 동일 하 게 합니다.</span><span class="sxs-lookup"><span data-stu-id="4e97c-136">This method will be used to configure the request pipeline of the `TestServer` identically to how the SUT server would be configured.</span></span>

<span data-ttu-id="4e97c-137">에 대 한 요청이 Act 부분의 테스트는 `TestServer` "/" 경로 및 응답에 대 한 인스턴스 문자열로 다시 읽혀집니다.</span><span class="sxs-lookup"><span data-stu-id="4e97c-137">In the Act portion of the test, a request is made to the `TestServer` instance for the "/" path, and the response is read back into a string.</span></span> <span data-ttu-id="4e97c-138">이 문자열은 "Hello World!"의 예상 되는 문자열이와 비교 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4e97c-138">This string is compared with the expected string of "Hello World!".</span></span> <span data-ttu-id="4e97c-139">일치 하는 경우 테스트를 통과 합니다. 그렇지 않으면, 요청이 실패 합니다.</span><span class="sxs-lookup"><span data-stu-id="4e97c-139">If they match, the test passes; otherwise, it fails.</span></span>

<span data-ttu-id="4e97c-140">이제 웹 응용 프로그램을 통해 확인 기능 prime 작동 하는지 확인 하려면 몇 가지 추가 통합 테스트를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4e97c-140">Now you can add a few additional integration tests to confirm that the prime checking functionality works via the web application:</span></span>

[!code-csharp[Main](../testing/integration-testing/sample/test/PrimeWeb.IntegrationTests/PrimeWebCheckPrimeShould.cs?name=snippet_CheckPrime)]

<span data-ttu-id="4e97c-141">참고 웹 응용 프로그램이 예상 대로 수행 하는 대신 하지만 이러한 테스트를 소수 검사기의 정확성을 테스트 하려면 다음 시도 하는 것은 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="4e97c-141">Note that you're not really trying to test the correctness of the prime number checker with these tests but rather that the web application is doing what you expect.</span></span> <span data-ttu-id="4e97c-142">이미의 신뢰도 제공 하는 단위 테스트 검사 `PrimeService`여기에서 볼 수 있듯이,:</span><span class="sxs-lookup"><span data-stu-id="4e97c-142">You already have unit test coverage that gives you confidence in `PrimeService`, as you can see here:</span></span>

![테스트 탐색기](integration-testing/_static/test-explorer.png)

<span data-ttu-id="4e97c-144">단위 테스트에 대해 자세히 알아볼 수 있습니다는 [단위 테스트](https://docs.microsoft.com/dotnet/articles/core/testing/unit-testing-with-dotnet-test) 문서.</span><span class="sxs-lookup"><span data-stu-id="4e97c-144">You can learn more about the unit tests in the [Unit testing](https://docs.microsoft.com/dotnet/articles/core/testing/unit-testing-with-dotnet-test) article.</span></span>


### <a name="integration-testing-mvcrazor"></a><span data-ttu-id="4e97c-145">통합 Mvc Razor/테스트</span><span class="sxs-lookup"><span data-stu-id="4e97c-145">Integration testing Mvc/Razor</span></span>

<span data-ttu-id="4e97c-146">Razor 뷰를 포함 하는 테스트 프로젝트를 사용 하려면 `<PreserveCompilationContext>` 로 설정에서 true는 *.csproj* 파일:</span><span class="sxs-lookup"><span data-stu-id="4e97c-146">Test projects that contain Razor views require `<PreserveCompilationContext>` be set to true in the *.csproj* file:</span></span>


```xml
    <PreserveCompilationContext>true</PreserveCompilationContext>
```

<span data-ttu-id="4e97c-147">이 요소가 없습니다. 프로젝트에는 다음과 유사한 오류가 생성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4e97c-147">Projects missing this element will generate an error similar to the following:</span></span>
```
Microsoft.AspNetCore.Mvc.Razor.Compilation.CompilationFailedException: 'One or more compilation failures occurred:
ooebhccx.1bd(4,62): error CS0012: The type 'Attribute' is defined in an assembly that is not referenced. You must add a reference to assembly 'netstandard, Version=2.0.0.0, Culture=neutral, PublicKeyToken=cc7b13ffcd2ddd51'.
```


## <a name="refactoring-to-use-middleware"></a><span data-ttu-id="4e97c-148">미들웨어를 사용 하도록 리팩터링</span><span class="sxs-lookup"><span data-stu-id="4e97c-148">Refactoring to use middleware</span></span>

<span data-ttu-id="4e97c-149">리팩터링은 동작을 변경 하지 않고 해당 디자인을 개선 하기 위해 응용 프로그램의 코드를 변경 하는 과정을 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4e97c-149">Refactoring is the process of changing an application's code to improve its design without changing its behavior.</span></span> <span data-ttu-id="4e97c-150">이 도움말 시스템의 동작은 동일 하 게 유지 하기 전에 및 변경 된 후 확인 하므로 테스트를 통과 집합이 있으면 이상적으로 수행 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4e97c-150">It should ideally be done when there is a suite of passing tests, since these help ensure the system's behavior remains the same before and after the changes.</span></span> <span data-ttu-id="4e97c-151">웹 응용 프로그램에서 초기화 검사 논리를 구현 하는 방식을 살펴보면 `Configure` 메서드를 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="4e97c-151">Looking at the way in which the prime checking logic is implemented in the web application's `Configure` method, you see:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }

    app.Run(async (context) =>
    {
        if (context.Request.Path.Value.Contains("checkprime"))
        {
            int numberToCheck;
            try
            {
                numberToCheck = int.Parse(context.Request.QueryString.Value.Replace("?", ""));
                var primeService = new PrimeService();
                if (primeService.IsPrime(numberToCheck))
                {
                    await context.Response.WriteAsync($"{numberToCheck} is prime!");
                }
                else
                {
                    await context.Response.WriteAsync($"{numberToCheck} is NOT prime!");
                }
            }
            catch
            {
                await context.Response.WriteAsync("Pass in a number to check in the form /checkprime?5");
            }
        }
        else
        {
            await context.Response.WriteAsync("Hello World!");
        }
    });
}
```

<span data-ttu-id="4e97c-152">이 코드는 작동 하지만 ASP.NET Core 응용 프로그램에서는 이것이 간단한도 이러한 종류의 기능을 구현 하려면 원하는 방법에 크게 못 미치는 쉽습니다.</span><span class="sxs-lookup"><span data-stu-id="4e97c-152">This code works, but it's far from how you would like to implement this kind of functionality in an ASP.NET Core application, even as simple as this is.</span></span> <span data-ttu-id="4e97c-153">무엇을 가정해 보세요는 `Configure` 다른 URL 끝점을 추가할 때마다이 처럼 많은 코드를 추가 하는 데 필요한 경우 메서드는 같습니다.</span><span class="sxs-lookup"><span data-stu-id="4e97c-153">Imagine what the `Configure` method would look like if you needed to add this much code to it every time you add another URL endpoint!</span></span>

<span data-ttu-id="4e97c-154">고려해 야 할 한 가지 옵션을 추가 하는 것 [MVC](xref:mvc/overview) 프라임 검사를 처리 하는 응용 프로그램 및 컨트롤러 만들기.</span><span class="sxs-lookup"><span data-stu-id="4e97c-154">One option to consider is adding [MVC](xref:mvc/overview) to the application and creating a controller to handle the prime checking.</span></span> <span data-ttu-id="4e97c-155">그러나 현재 하지 않는 것으로 간주 해야 다른 MVC 기능, 비트를 overkill 합니다.</span><span class="sxs-lookup"><span data-stu-id="4e97c-155">However, assuming you don't currently need any other MVC functionality, that's a bit overkill.</span></span>

<span data-ttu-id="4e97c-156">그러나 ASP.NET Core 활용을 진행할 수 있습니다 [미들웨어](xref:fundamentals/middleware), 검사 논리는 자체 클래스에서 해 소수만 캡슐화 하 고 보다 잘 달성 하는 데 도움이 됩니다는 [문제의 분리](http://deviq.com/separation-of-concerns/) 에 `Configure` 메서드입니다.</span><span class="sxs-lookup"><span data-stu-id="4e97c-156">You can, however, take advantage of ASP.NET Core [middleware](xref:fundamentals/middleware), which will help us encapsulate the prime checking logic in its own class and achieve better [separation of concerns](http://deviq.com/separation-of-concerns/) in the `Configure` method.</span></span>

<span data-ttu-id="4e97c-157">미들웨어 매개 변수로 지정할 미들웨어 클래스 예상 있으므로를 사용 하 여 경로 허용 하려는 `RequestDelegate` 및 `PrimeCheckerOptions` 생성자의 인스턴스.</span><span class="sxs-lookup"><span data-stu-id="4e97c-157">You want to allow the path the middleware uses to be specified as a parameter, so the middleware class expects a `RequestDelegate` and a `PrimeCheckerOptions` instance in its constructor.</span></span> <span data-ttu-id="4e97c-158">이 미들웨어 란 요청의 경로 일치 하지 않으면, 사용 하도록 구성을 하기만 하면 체인의 다음 미들웨어를 호출 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4e97c-158">If the path of the request doesn't match what this middleware is configured to expect, you simply call the next middleware in the chain and do nothing further.</span></span> <span data-ttu-id="4e97c-159">에 있는 구현 코드의 나머지 부분 `Configure` 되어 이제는 `Invoke` 메서드.</span><span class="sxs-lookup"><span data-stu-id="4e97c-159">The rest of the implementation code that was in `Configure` is now in the `Invoke` method.</span></span>

> [!NOTE]
> <span data-ttu-id="4e97c-160">미들웨어에 의존 하므로 `PrimeService` 서비스는 생성자와 함께이 서비스의 인스턴스 수도 요청 합니다.</span><span class="sxs-lookup"><span data-stu-id="4e97c-160">Since the middleware depends on the `PrimeService` service, you're also requesting an instance of this service with the constructor.</span></span> <span data-ttu-id="4e97c-161">프레임 워크를 통해이 서비스를 제공 합니다 [종속성 주입](xref:fundamentals/dependency-injection), 구성 된, 즉 가정 `ConfigureServices`합니다.</span><span class="sxs-lookup"><span data-stu-id="4e97c-161">The framework will provide this service via [Dependency Injection](xref:fundamentals/dependency-injection), assuming it has been configured, for example in `ConfigureServices`.</span></span>

[!code-csharp[Main](../testing/integration-testing/sample/src/PrimeWeb/Middleware/PrimeCheckerMiddleware.cs?highlight=39-63)]

<span data-ttu-id="4e97c-162">이 미들웨어 경로 일치 하는 경우 요청 대리자 체인에 끝점 역할 이므로에 대 한 호출이 `_next.Invoke` 이 미들웨어에서 요청을 처리 하는 경우.</span><span class="sxs-lookup"><span data-stu-id="4e97c-162">Since this middleware acts as an endpoint in the request delegate chain when its path matches, there is no call to `_next.Invoke` when this middleware handles the request.</span></span>

<span data-ttu-id="4e97c-163">이 미들웨어 있어야 하며 몇 가지 유용한 확장 메서드 생성을 보다 쉽게 구성 하는 리팩터링된와 `Configure` 메서드는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="4e97c-163">With this middleware in place and some helpful extension methods created to make configuring it easier, the refactored `Configure` method looks like this:</span></span>

[!code-csharp[Main](../testing/integration-testing/sample/src/PrimeWeb/Startup.cs?highlight=9&range=19-33)]

<span data-ttu-id="4e97c-164">이 리팩터링 다음 통합 테스트 모두 전달 하는 이후 계속 웹 응용 프로그램이 이전 처럼 작동 하는지 시킨다 고 확신 것입니다.</span><span class="sxs-lookup"><span data-stu-id="4e97c-164">Following this refactoring, you're confident that the web application still works as before, since your integration tests are all passing.</span></span>

> [!NOTE]
> <span data-ttu-id="4e97c-165">리팩터링를 완료 하 고 테스트가 통과 한 후 소스 제어에 대 한 변경 내용을 적용 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="4e97c-165">It's a good idea to commit your changes to source control after you complete a refactoring and your tests pass.</span></span> <span data-ttu-id="4e97c-166">테스트 기반 개발을 공유 하는 경우 [커밋 빨간색-녹색-리팩터링 주기를 추가 하는 것이 좋습니다.](https://ardalis.com/rgrc-is-the-new-red-green-refactor-for-test-first-development)합니다.</span><span class="sxs-lookup"><span data-stu-id="4e97c-166">If you're practicing Test Driven Development, [consider adding Commit to your Red-Green-Refactor cycle](https://ardalis.com/rgrc-is-the-new-red-green-refactor-for-test-first-development).</span></span>

## <a name="resources"></a><span data-ttu-id="4e97c-167">리소스</span><span class="sxs-lookup"><span data-stu-id="4e97c-167">Resources</span></span>

* [<span data-ttu-id="4e97c-168">유닛 테스트</span><span class="sxs-lookup"><span data-stu-id="4e97c-168">Unit testing</span></span>](https://docs.microsoft.com/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* [<span data-ttu-id="4e97c-169">미들웨어</span><span class="sxs-lookup"><span data-stu-id="4e97c-169">Middleware</span></span>](xref:fundamentals/middleware)
* [<span data-ttu-id="4e97c-170">컨트롤러 테스트</span><span class="sxs-lookup"><span data-stu-id="4e97c-170">Testing controllers</span></span>](xref:mvc/controllers/testing)
