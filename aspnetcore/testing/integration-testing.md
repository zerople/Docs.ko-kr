---
title: "ASP.NET Core에서 통합 테스트"
author: ardalis
description: "응용 프로그램의 구성 요소가 제대로 작동 하는지 확인 하는 테스트 ASP.NET Core 통합을 사용 하는 방법."
keywords: "ASP.NET Core 통합 테스트"
ms.author: riande
manager: wpickett
ms.date: 02/14/2017
ms.topic: article
ms.assetid: 40d534f2-89b3-4b09-9c2c-3494bf9991c9
ms.technology: aspnet
ms.prod: asp.net-core
uid: testing/integration-testing
ms.openlocfilehash: d30af6edc31fbce2ebb77c57be3fd78231c54b50
ms.sourcegitcommit: 418e6aa4ab79474ecc4d0a6af573a3759b113fe4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/05/2017
---
# <a name="integration-testing-in-aspnet-core"></a>ASP.NET Core에서 통합 테스트

으로 [Steve Smith](http://ardalis.com)

통합 테스트 함께 어셈블되어 때 응용 프로그램의 구성 요소가 제대로 작동 하는지 확인 합니다. ASP.NET Core 지원 통합 단위 테스트 프레임 워크 및 네트워크 오버 헤드 없이 요청을 처리 하는 데 사용할 수 있는 기본 제공 테스트 웹 호스트를 사용 하 여 테스트 합니다.

[샘플 코드 보기 또는 다운로드](https://github.com/aspnet/Docs/tree/master/aspnetcore/testing/integration-testing/sample)

## <a name="introduction-to-integration-testing"></a>통합 테스트 소개

통합 테스트 응용 프로그램의 다른 부분 올바르게 작동 하는지 함께 확인 합니다. 와 달리 [단위 테스트](https://docs.microsoft.com/dotnet/articles/core/testing/unit-testing-with-dotnet-test), 데이터베이스, 파일 시스템, 네트워크 리소스 또는 웹 요청 및 응답 예: 응용 프로그램 인프라 문제가 솔루션에 통합 테스트 합니다. 단위 테스트 fakes 또는 이러한 여러 가지이 문제 대신 모의 개체를 사용 하지만 통합 테스트의 목적은 시스템 이러한 시스템에 예상 대로 작동 하는지 확인 하는 것입니다.

통합 테스트를 더 큰 세그먼트의 코드를 실행 하 고 인프라 요소에 의존 하므로 경향이 훨씬 크기 단위 테스트 보다 느립니다. 따라서 것이 좋습니다는 통합 테스트의 수를 제한 하에 작성 특히 경우 단위 테스트와 동일한 동작을 테스트할 수 있습니다.

> [!NOTE]
> 통합 테스트 또는 단위 테스트를 사용 하 여 일부 동작을 테스트할 수 있습니다는 것이 거의 더 빠를 수 있으므로 단위 테스트를 선호 합니다. 수십 또는 수백 개의 서로 다른 여러 입력을 사용 하 여 단위 테스트 하지만 약간의 가장 중요 한 시나리오를 다룹니다 통합 테스트를 할 수 있습니다.

사용자 고유의 메서드 내에서 논리를 테스트는 일반적으로 단위 테스트의 도메인입니다. 예를 들어 ASP.NET Core 또는 데이터베이스와 프레임 워크 내에서 응용 프로그램이 작동 하는 방법을 테스트는 통합 테스트 여기서 이루어지는입니다. 데이터베이스에 행을 쓰고 다시 읽을 수 있는지를 확인 하려면 너무 많은 통합 테스트 필요 하지 않습니다. 데이터 액세스 코드의 모든 가능한 순열 테스트할 필요가 없습니다-충분 하면 응용 프로그램이 제대로 작동 한다는 확신을 테스트 해야 합니다.

## <a name="integration-testing-aspnet-core"></a>ASP.NET Core를 테스트 하는 통합

실행된 통합 테스트 하도록 설정 하려면을 테스트 프로젝트 만들기, ASP.NET Core 웹 프로젝트에 대 한 참조를 추가 및 test runner를 설치 해야 합니다. 이 프로세스에 설명 되어는 [단위 테스트](https://docs.microsoft.com/dotnet/articles/core/testing/unit-testing-with-dotnet-test) 테스트 및 테스트 및 테스트 클래스의 이름을 지정 하는 것에 대 한 권장 사항 실행 대 한 자세한 지침과 설명서입니다.

> [!NOTE]
> 단위 테스트 및 서로 다른 프로젝트를 사용 하 여 통합 테스트를 구분 합니다. 단위 테스트에는 인프라 문제가 실수로 인해 발생 하지 않는 고 쉽게 실행 하려면 테스트 집합을 선택할 수 있습니다.

### <a name="the-test-host"></a>테스트 호스트

ASP.NET Core 통합 테스트 프로젝트에 추가할 수 있으며, 호스팅하는 데 사용 ASP.NET Core 응용 프로그램의 경우 실제 웹 호스트에 대 한 필요 없이 서비스 테스트를 요청 하는 테스트 호스트를 포함 합니다. 제공 된 샘플에 사용 하도록 구성 된 통합 테스트 프로젝트에 포함 되어 [xUnit](https://xunit.github.io) 와 테스트 호스트 합니다. 사용 하 여는 `Microsoft.AspNetCore.TestHost` NuGet 패키지 합니다.

한 번의 `Microsoft.AspNetCore.TestHost` 패키지는 프로젝트에 포함 된를 만들고 구성할 수 있습니다는 `TestServer` 테스트에서 합니다. 다음 테스트에는 사이트의 루트에 대 한 요청 "Hello World!"을 반환 하는지 확인 하는 방법을 보여 줍니다. 고 해야 성공적으로 실행 된 기본값에 대 한 Visual Studio에서 만든 ASP.NET Core 빈 웹 템플릿.

[!code-csharp[Main](../testing/integration-testing/sample/test/PrimeWeb.IntegrationTests/PrimeWebDefaultRequestShould.cs?name=snippet_WebDefault&highlight=7,16,22)]

이 테스트 정렬 Act Assert 패턴을 사용 합니다. 인스턴스를 만드는 생성자에서 정렬 단계는 수행 `TestServer`합니다. 구성 된 `WebHostBuilder` 만드는 데 사용할 수는 `TestHost`이 예에서 `Configure` 테스트 (SUT)의 대상 시스템에 있는 메서드의 `Startup` 클래스에 전달 됩니다는 `WebHostBuilder`합니다. 이 메서드는의 요청 파이프라인을 구성 하는 `TestServer` SUT 서버 구성 하는 방법에 동일 하 게 합니다.

에 대 한 요청이 Act 부분의 테스트는 `TestServer` "/" 경로 및 응답에 대 한 인스턴스 문자열로 다시 읽혀집니다. 이 문자열은 "Hello World!"의 예상 되는 문자열이와 비교 됩니다. 일치 하는 경우 테스트를 통과 합니다. 그렇지 않으면, 요청이 실패 합니다.

이제 웹 응용 프로그램을 통해 확인 기능 prime 작동 하는지 확인 하려면 몇 가지 추가 통합 테스트를 추가할 수 있습니다.

[!code-csharp[Main](../testing/integration-testing/sample/test/PrimeWeb.IntegrationTests/PrimeWebCheckPrimeShould.cs?name=snippet_CheckPrime)]

참고 웹 응용 프로그램이 예상 대로 수행 하는 대신 하지만 이러한 테스트를 소수 검사기의 정확성을 테스트 하려면 다음 시도 하는 것은 아닙니다. 이미의 신뢰도 제공 하는 단위 테스트 검사 `PrimeService`여기에서 볼 수 있듯이,:

![테스트 탐색기](integration-testing/_static/test-explorer.png)

단위 테스트에 대해 자세히 알아볼 수 있습니다는 [단위 테스트](https://docs.microsoft.com/dotnet/articles/core/testing/unit-testing-with-dotnet-test) 문서.

## <a name="refactoring-to-use-middleware"></a>미들웨어를 사용 하도록 리팩터링

리팩터링은 동작을 변경 하지 않고 해당 디자인을 개선 하기 위해 응용 프로그램의 코드를 변경 하는 과정을 됩니다. 이 도움말 시스템의 동작은 동일 하 게 유지 하기 전에 및 변경 된 후 확인 하므로 테스트를 통과 집합이 있으면 이상적으로 수행 해야 합니다. 웹 응용 프로그램에서 초기화 검사 논리를 구현 하는 방식을 살펴보면 `Configure` 메서드를 표시 합니다.

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

이 코드는 작동 하지만 ASP.NET Core 응용 프로그램에서는 이것이 간단한도 이러한 종류의 기능을 구현 하려면 원하는 방법에 크게 못 미치는 쉽습니다. 무엇을 가정해 보세요는 `Configure` 다른 URL 끝점을 추가할 때마다이 처럼 많은 코드를 추가 하는 데 필요한 경우 메서드는 같습니다.

고려해 야 할 한 가지 옵션을 추가 하는 것 [MVC](xref:mvc/overview) 프라임 검사를 처리 하는 응용 프로그램 및 컨트롤러 만들기. 그러나 현재 하지 않는 것으로 간주 해야 다른 MVC 기능, 비트를 overkill 합니다.

그러나 ASP.NET Core 활용을 진행할 수 있습니다 [미들웨어](xref:fundamentals/middleware), 검사 논리는 자체 클래스에서 해 소수만 캡슐화 하 고 보다 잘 달성 하는 데 도움이 됩니다는 [문제의 분리](http://deviq.com/separation-of-concerns/) 에 `Configure` 메서드입니다.

미들웨어 매개 변수로 지정할 미들웨어 클래스 예상 있으므로를 사용 하 여 경로 허용 하려는 `RequestDelegate` 및 `PrimeCheckerOptions` 생성자의 인스턴스. 이 미들웨어 란 요청의 경로 일치 하지 않으면, 사용 하도록 구성을 하기만 하면 체인의 다음 미들웨어를 호출 하지 않습니다. 에 있는 구현 코드의 나머지 부분 `Configure` 되어 이제는 `Invoke` 메서드.

> [!NOTE]
> 미들웨어에 의존 하므로 `PrimeService` 서비스는 생성자와 함께이 서비스의 인스턴스 수도 요청 합니다. 프레임 워크를 통해이 서비스를 제공 합니다 [종속성 주입](xref:fundamentals/dependency-injection), 구성 된, 즉 가정 `ConfigureServices`합니다.

[!code-csharp[Main](../testing/integration-testing/sample/src/PrimeWeb/Middleware/PrimeCheckerMiddleware.cs?highlight=39-63)]

이 미들웨어 경로 일치 하는 경우 요청 대리자 체인에 끝점 역할 이므로에 대 한 호출이 `_next.Invoke` 이 미들웨어에서 요청을 처리 하는 경우.

이 미들웨어 있어야 하며 몇 가지 유용한 확장 메서드 생성을 보다 쉽게 구성 하는 리팩터링된와 `Configure` 메서드는 다음과 같습니다.

[!code-csharp[Main](../testing/integration-testing/sample/src/PrimeWeb/Startup.cs?highlight=9&range=19-33)]

이 리팩터링 다음 통합 테스트 모두 전달 하는 이후 계속 웹 응용 프로그램이 이전 처럼 작동 하는지 시킨다 고 확신 것입니다.

> [!NOTE]
> 리팩터링를 완료 하 고 테스트가 통과 한 후 소스 제어에 대 한 변경 내용을 적용 하는 것이 좋습니다. 테스트 기반 개발을 공유 하는 경우 [커밋 빨간색-녹색-리팩터링 주기를 추가 하는 것이 좋습니다.](http://ardalis.com/rgrc-is-the-new-red-green-refactor-for-test-first-development)합니다.

## <a name="resources"></a>리소스

* [단위 테스트](https://docs.microsoft.com/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* [미들웨어](xref:fundamentals/middleware)
* [테스트 컨트롤러](xref:mvc/controllers/testing)
