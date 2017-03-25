---
title: "ASP.NET Core 미들웨어 | Microsoft 문서"
author: rick-anderson
description: "미들웨어 및 요청 파이프라인에 설명합니다."
keywords: "ASP.NET Core, 미들웨어, 파이프라인, 대리자"
ms.author: riande
manager: wpickett
ms.date: 02/14/2017
ms.topic: article
ms.assetid: db9a86ab-46c2-40e0-baed-86e38c16af1f
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/middleware
translationtype: Machine Translation
ms.sourcegitcommit: 010b730d2716f9f536fef889bc2f767afb648ef4
ms.openlocfilehash: f1f64b6033c4c86f2feca7389f002de6582d8457
ms.lasthandoff: 03/23/2017

---
# <a name="aspnet-core-middleware-fundamentals"></a>ASP.NET Core 미들웨어 기본 사항

<a name=fundamentals-middleware></a>

여 [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [Steve Smith](http://ardalis.com)

[샘플 코드 보기 또는 다운로드](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/middleware/sample)

## <a name="what-is-middleware"></a>미들웨어는 무엇입니까

미들웨어는 요청 및 응답을 처리 하는 응용 프로그램 파이프라인에 조립 되는 소프트웨어입니다. 각 구성 요소는 파이프라인의 다음 구성 요소에 요청 전달 \ 것인지를 선택 하 고 이전 및 다음 구성 요소는 파이프라인에서 호출 이후 특정 작업을 수행할 수 합니다. 요청 대리자 요청 파이프라인을 작성 하는 데 사용 됩니다. 요청 대리자는 각 HTTP 요청을 처리합니다.

대리자를 사용 하 여 구성 된 요청 [실행](https://docs.microsoft.com/en-us/aspnet/core/api/microsoft.aspnetcore.builder.runextensions), [맵](https://docs.microsoft.com/en-us/aspnet/core/api/microsoft.aspnetcore.builder.mapextensions), 및 [사용](https://docs.microsoft.com/en-us/aspnet/core/api/microsoft.aspnetcore.builder.useextensions) 대 한 확장 메서드는 [IApplicationBuilder](https://docs.microsoft.com/en-us/aspnet/core/api/microsoft.aspnetcore.builder.iapplicationbuilder) 로 전달 되는 인스턴스는 `Configure` 에서 메서드는 `Startup` 클래스입니다. 개별 요청 대리자 (인라인 미들웨어) 무명 메서드를 인라인으로 지정된 될 수 또는 다시 사용할 수 있는 클래스에서 정의할 수 있습니다. 이러한 다시 사용할 수 있는 클래스와 인라인 무명 메서드는 *미들웨어*, 또는 *미들웨어 구성 요소*합니다. 요청 파이프라인의 각 미들웨어 구성 요소는 파이프라인의 다음 구성 요소를 호출 하거나 해당 하는 경우 체인 단락 (short-circuiting) 하는 일을 담당 합니다.

[미들웨어를 마이그레이션 HTTP 모듈](../migration/http-modules.md) ASP.NET 코어의 요청 파이프라인 및 이전 버전 간의 차이 설명 하 고 더 많은 미들웨어 샘플을 제공 합니다.

## <a name="creating-a-middleware-pipeline-with-iapplicationbuilder"></a>IApplicationBuilder 미들웨어 파이프라인 만들기

ASP.NET Core 요청 파이프라인 요청 대리자, 즉,이 다이어그램 (실행 다음과 검은색 화살표의 스레드)와 같이 호출의 시퀀스로 구성 됩니다.

![요청 처리 패턴 도착, 세 가지 미들웨어 및 응용 프로그램이 응답을 통해 처리 요청을 표시 합니다. 각 미들웨어 해당 논리를 실행 하 고 next () 문에서 다음 미들웨어에 요청을 전달한 합니다. 요청을 처리 하는 세 번째 미들웨어 후는 처리를 위한 추가 각 next () 문 뒤에 클라이언트에 대 한 응답으로 응용 프로그램에서 없어지기 전에 이전 두 미들웨어를 통해 다시 전달 합니다.](middleware/_static/request-delegate-pipeline.png)

각 대리자 이전 및 다음 대리자 이후 작업을 수행할 수 있습니다. 요청 파이프라인을 단락 (short-circuiting) 이라고 하는 다음 대리자에는 요청을 전달 하지 않고 대리자를 결정할 수도 있습니다. 단락 (short-circuiting)이를 방지할 수 있는 불필요 한 작업을 허용 하기 때문에 바람직합니다. 예를 들어 정적 파일 미들웨어는 정적 파일에 대 한 요청을 반환 하 고 파이프라인의 나머지 부분을 단락 (short-circuit) 수 있습니다. 예외 처리 대리자 파이프라인의 이후 단계에서 발생 하는 예외를 catch 할 수 있도록 파이프라인을 초기에 호출 해야 합니다.

가장 간단한 가능한 ASP.NET 핵심 응용 프로그램은 모든 요청을 처리 하는 단일 요청 대리자를 설정 합니다. 이 경우 실제 요청 파이프라인에 포함 되지 않습니다. 대신, 단일 익명 함수는 모든 HTTP 요청에 대 한 응답에서 호출 됩니다.

[!code-csharp[주](middleware/sample/Middleware/Startup.cs)]

첫 번째 [응용 프로그램입니다. 실행](https://docs.microsoft.com/en-us/aspnet/core/api/microsoft.aspnetcore.builder.runextensions) 대리자 파이프라인을 종료 합니다.

여러 요청 대리자와 함께 연결 하는 [응용 프로그램입니다. Use](https://docs.microsoft.com/en-us/aspnet/core/api/microsoft.aspnetcore.builder.useextensions). `next` 매개 변수는 파이프라인의 다음 대리자를 나타냅니다. (기억 하 여 파이프라인을 단락 수 한다는 *하지* 호출의 *다음* 매개 변수입니다.) 일반적으로 작업을 수행할 수는 다음 대리자 전후이 예제 에서처럼:

[!code-csharp[주](middleware/sample/Chain/Startup.cs?name=snippet1)]

>[!WARNING]
>신중 하 게 수정할 수는 `HttpResponse` 호출 후 `next`응답 수 이미 전송 된 클라이언트에 때문에 있습니다. 사용할 수 있습니다 [HttpResponse.HasStarted](https://docs.microsoft.com/en-us/aspnet/core/api/microsoft.aspnetcore.http.features.httpresponsefeature#Microsoft_AspNetCore_Http_Features_HttpResponseFeature_HasStarted) 는 헤더를 보낸 있는지 여부를 확인 합니다.

>[!WARNING]
> 마십시오 **하지** 호출 `next.Invoke` 호출한 후는 `write` 메서드. 미들웨어 구성 요소를 응답 또는 호출 하거나 생성 `next.Invoke`, 다르다는 것입니다.

## <a name="ordering"></a>순서 지정

미들웨어 구성 요소에 추가 되는 순서는 `Configure` 요청에서 호출 되는 순서와 역순 응답에 대 한 메서드를 정의 합니다. 이 순서 지정 하는 것이 보안, 성능 및 기능에 대 한 중요 합니다.

Configure 메서드 (아래 참조) 다음 미들웨어 구성 요소를 추가 합니다.

1. 예외/오류 처리
2. 정적 파일 서버
3. 인증
4. MVC

```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseExceptionHandler("/Home/Error"); // Call first to catch exceptions
                                            // thrown in the following middleware.

    app.UseStaticFiles();                   // Return static files and end pipeline.

    app.UseIdentity();                     // Authenticate before you access
                                           // secure resources.

    app.UseMvcWithDefaultRoute();          // Add MVC to the request pipeline.
}
```

위의 코드에서 `UseExceptionHandler` 파이프라인에 추가 하는 첫 번째 미들웨어 구성 요소-따라서 이후 호출에서 발생 하는 모든 예외를 catch 합니다.

정적 파일 미들웨어는 요청을 처리 하 고 나머지 구성 요소를 거치지 않고 단락 (short-circuit) 수 있도록 파이프라인 초기에 호출 됩니다. 정적 파일 미들웨어 제공 **없는** 권한 부여 검사 합니다. 비롯 한 모든 파일에 제공 *wwwroot*, 공개적으로 사용할 수 있습니다. 참조 [정적 파일 작업](xref:fundamentals/static-files) 정적 파일을 보호 하는 방법에 대 한 합니다.

요청이 정적 파일 미들웨어에서 처리 되지 않으면 Identity 미들웨어를 전달 됩니다 (`app.UseIdentity`), 인증을 수행 합니다. Identity 인증 되지 않은 요청 단락 (short-circuit) 하지 않습니다. Identity에서 요청을 인증 하지만 권한 부여 (및 거부) MVC가 특정 컨트롤러와 작업을 선택한 후에 발생 합니다.

다음 예제 응답 압축 미들웨어 하기 전에 정적 파일 미들웨어에서 정적 파일에 대 한 요청이 처리 되는 위치 순서 미들웨어입니다. 미들웨어가 순서 지정을 통해 정적 파일을 압축 되지 않습니다. MVC 응답이 [UseMvcWithDefaultRoute](https://docs.microsoft.com/en-us/aspnet/core/api/microsoft.aspnetcore.builder.mvcapplicationbuilderextensions#Microsoft_AspNetCore_Builder_MvcApplicationBuilderExtensions_UseMvcWithDefaultRoute_Microsoft_AspNetCore_Builder_IApplicationBuilder_) 압축 될 수 있습니다.

```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseStaticFiles();         // Static files not compressed
                                  // by middleware.
    app.UseResponseCompression();
    app.UseMvcWithDefaultRoute();
}
```

<a name=middleware-run-map-use></a>

### <a name="run-map-and-use"></a>실행, 지도 및 사용

사용 하 여 HTTP 파이프라인을 구성 하면 `Run`, `Map`, 및 `Use`합니다. `Run` 메서드 short-circuits 파이프라인 (즉,을 호출 하지 않습니다는 `next` 요청 대리자). `Run`규칙은 및 일부 미들웨어 구성 요소에 노출 될 수 있습니다 `Run[Middleware]` 파이프라인의 끝에 실행 되는 메서드.

`Map*`확장은 파이프라인 분기에 대 한 규칙으로 사용 됩니다. [지도](https://docs.microsoft.com/en-us/aspnet/core/api/microsoft.aspnetcore.builder.mapextensions) 주어진된 요청 경로의 일치 항목을 기반으로 하는 요청 파이프라인 분기 합니다. 지정 된 경로로 요청 경로의 시작 되 면 분기가 실행 됩니다.

[!code-csharp[주](middleware/sample/Chain/StartupMap.cs?name=snippet1)]

다음 표에서 요청 및 응답을 보여 줍니다. `http://localhost:1234` 앞의 코드를 사용 하 여:

| 요청 | 응답 |
| --- | --- |
| localhost:1234 | -맵 대리자의 번호입니다.  |
| localhost:1234 / m a p&1; | 맵 테스트 1 |
| localhost:1234 / map2 | 맵 테스트 2 |
| localhost:1234 / map3 | -맵 대리자의 번호입니다.  |

때 `Map` 가 사용 하 고, 일치 하는 경로 세그먼트에서 제거 됩니다 `HttpRequest.Path` 를 추가 하 고 `HttpRequest.PathBase` 각 요청에 대 한 합니다.

[MapWhen](https://docs.microsoft.com/en-us/aspnet/core/api/microsoft.aspnetcore.builder.mapwhenextensions) 요청 파이프라인 지정된 된 조건자의 결과에 따라 분기 합니다. 모든 조건자 형식의 `Func<HttpContext, bool>` 요청 파이프라인의 새 분기에 매핑하기 위해 사용할 수 있습니다. 다음 예에서 조건자는 쿼리 문자열 변수의 존재를 감지 하는 데 사용 `branch`:

[!code-csharp[주](middleware/sample/Chain/StartupMapWhen.cs?name=snippet1)]

다음 표에서 요청 및 응답을 보여 줍니다. `http://localhost:1234` 앞의 코드를 사용 하 여:

| 요청 | 응답 |
| --- | --- |
| localhost:1234 | -맵 대리자의 번호입니다.  |
| localhost:1234 /? 분기 마스터 = | 사용 되는 분기 마스터 =|

`Map`예를 들어 중첩 지원:

```csharp
app.Map("/level1", level1App => {
       level1App.Map("/level2a", level2AApp => {
           // "/level1/level2a"
           //...
       });
       level1App.Map("/level2b", level2BApp => {
           // "/level1/level2b"
           //...
       });
   });
   ```

`Map`또한 일치 시킬 수 여러 세그먼트를 한 번에 예를 들어:

 ```csharp
app.Map("/level1/level2", HandleMultiSeg);
```

## <a name="built-in-middleware"></a>기본 제공 미들웨어

ASP.NET Core 다음 미들웨어 구성 요소와 함께 제공 됩니다.

| 미들웨어 | 설명 |
| ----- | ------- |
| [인증](xref:security/authentication/identity) | 인증 지원을 제공합니다. |
| [CORS](xref:security/cors) | 크로스-원본 자원 공유를 구성합니다. |
| [응답 캐싱](xref:performance/caching/middleware) | 응답을 캐시에 대 한 지원을 제공 합니다. |
| [응답 압축](xref:performance/response-compression) | 응답을 압축 하는 것에 대 한 지원을 제공 합니다. |
| [라우팅](xref:fundamentals/routing) | 정의 하 고 요청 경로 제한 합니다. |
| [세션](xref:fundamentals/app-state) | 사용자 세션을 관리 하기 위한 지원을 제공 합니다. |
| [정적 파일](xref:fundamentals/static-files) | 정적 파일 및 디렉터리 검색을 처리 하는 것에 대 한 지원을 제공 합니다. |
| [URL 다시 쓰기 미들웨어](xref:fundamentals/url-rewriting) | Url 다시 쓰기 및 요청에 대 한 지원을 제공 합니다. |

<a name=middleware-writing-middleware></a>

## <a name="writing-middleware"></a>쓰기 미들웨어

일반적으로 미들웨어 클래스에 캡슐화 하 고 확장 메서드를 사용 하 여 노출 됩니다. 쿼리 문자열에서 현재 요청에 대 한 문화권을 설정 하는 다음과 같은 미들웨어를 고려해 야 합니다.

[!code-csharp[주](middleware/sample/Culture/StartupCulture.cs?name=snippet1)]

참고: 위의 샘플 코드는 미들웨어 구성 요소 만들기를 보여 주기 위해 사용 됩니다. 참조 [ 전역화 및 지역화](xref:fundamentals/localization) ASP.NET 핵심 기본 제공 지역화 지원 합니다.

예를 들어 culture에 전달 하 여 미들웨어를 테스트할 수 있습니다 `http://localhost:7997/?culture=no`합니다.

다음 코드는 미들웨어 대리자 클래스를 이동합니다.

[!code-csharp[주](middleware/sample/Culture/RequestCultureMiddleware.cs)]

다음 확장 메서드를 통해 미들웨어 노출 [IApplicationBuilder](https://docs.microsoft.com/en-us/aspnet/core/api/microsoft.aspnetcore.builder.iapplicationbuilder):

[!code-csharp[주](middleware/sample/Culture/RequestCultureMiddlewareExtensions.cs)]

다음 코드에서는 호출에서 미들웨어 `Configure`:

[!code-csharp[주](middleware/sample/Culture/Startup.cs?name=snippet1&highlight=5)]

미들웨어 따라야는 [명시적 종속성 원칙](http://deviq.com/explicit-dependencies-principle/) 생성자에서 종속성을 노출 하 여 합니다. 미들웨어 당 한 번 생성 됩니다 *응용 프로그램 수명*합니다. 참조 *요청별 종속성* 경우에는 아래 서비스 요청 내에서 미들웨어를 공유 해야 합니다.

미들웨어 구성 요소 종속성 주입 생성자 매개 변수를 통해 해당 종속성을 확인할 수 있습니다. [`UseMiddleware<T>`](https://docs.microsoft.com/en-us/aspnet/core/api/microsoft.aspnetcore.builder.usemiddlewareextensions#methods_summary)추가 매개 변수를 직접 받아들일 수도 있습니다.

### <a name="per-request-dependencies"></a>요청별 종속성

미들웨어 하지 요청별, 응용 프로그램 시작 시 생성 않으므로 *범위* 미들웨어 생성자에 의해 사용 되는 수명 서비스 요청 될 때마다 다른 종속성 주입 종류와 공유 되지 않습니다. 공유 해야 하는 경우는 *범위* 이러한 서비스를 추가, 미들웨어 및 다른 형식 간의 서비스는 `Invoke` 메서드의 서명입니다. `Invoke` 메서드는 종속성 주입으로 채우는 추가 매개 변수를 사용할 수 있습니다. 예:

```c#
public class MyMiddleware
{
    private readonly RequestDelegate _next;

    public MyMiddleware(RequestDelegate next)
    {
        _next = next;
    }

    public async Task Invoke(HttpContext httpContext, IMyScopedService svc)
    {
        svc.MyProperty = 1000;
        await _next(httpContext);
    }
}
```

## <a name="resources"></a>리소스

* [이 문서에 사용 되는 샘플 코드](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/middleware/sample)
* [미들웨어를 마이그레이션 HTTP 모듈](../migration/http-modules.md)
* [응용 프로그램 시작](startup.md)
* [기능을 요청](request-features.md)

