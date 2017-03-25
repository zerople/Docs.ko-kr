---
title: "ASP.NET Core에서 세션 및 응용 프로그램 상태 | Microsoft 문서"
author: rick-anderson
description: "요청 간에 유지 응용 프로그램 및 사용자 (세션) 상태에 접근 합니다."
keywords: "ASP.NET Core, 응용 프로그램 상태, 세션 상태, 쿼리 문자열, 게시"
ms.author: riande
manager: wpickett
ms.date: 03/08/2017
ms.topic: article
ms.assetid: 18cda488-0769-4cb9-82f6-4c6685f2045d
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/app-state
ms.custom: H1Hack27Feb2017
translationtype: Machine Translation
ms.sourcegitcommit: f42a2e1e42a2231c3fc4898fe98f61c89176b555
ms.openlocfilehash: 2d06a34033ec08970bdde45ac39f1d0e2fe7bdf1
ms.lasthandoff: 03/23/2017

---

# <a name="introduction-to-session-and-application-state-in-aspnet-core"></a>ASP.NET Core에서 세션 및 응용 프로그램 상태 소개

여 [Rick Anderson](https://twitter.com/RickAndMSFT), [Steve Smith](http://ardalis.com), 및 [Diana LaRose](https://github.com/DianaLaRose)

HTTP는 상태 비저장 프로토콜입니다. 웹 서버 독립적인 요청으로는 각 HTTP 요청을 처리 하 고 이전 요청에서 사용자 값을 보존 하지는 않습니다. 이 문서에는 응용 프로그램 및 요청 사이 세션 상태를 유지 하기 위해 다른 방법을 설명 합니다. 

## <a name="session-state"></a>세션 상태

세션 상태는 사용자가 웹 앱을 탐색 하는 동안 사용자 데이터를 저장 하는 데 사용할 수 있는 ASP.NET 핵심 기능입니다. 세션 상태가 서버에 사전 또는 해시 테이블의 구성 된, 브라우저에서 요청을 통해 데이터를 유지 합니다. 세션 데이터를 캐시 하 여 백업 합니다.

ASP.NET Core 클라이언트를 각 요청과 함께 서버로 전송 되는 세션 ID를 포함 하는 쿠키를 지정 하 여 세션 상태를 유지 합니다. 서버 세션 ID를 사용 하 여 세션 데이터를 인출 합니다. 세션 쿠키를 브라우저에 특정 이기 때문에 브라우저에서 세션을 공유할 수 없습니다. 세션 쿠키는 브라우저 세션을 종료 하는 경우에 삭제 됩니다. 쿠키는 만료 된 세션에 대 한 수신 되 면 동일한 세션 쿠키를 사용 하는 새 세션이 만들어집니다. 

서버는 마지막 요청 이후 제한 된 시간에 대 한 세션을 유지합니다. 세션 시간 제한을 설정 하거나 기본값인 20 분을 사용할 수 있습니다. 세션 상태는 영구적으로 유지 될 필요가 없는 이지만 특정 세션에만 해당 되는 사용자 데이터를 저장 하기에 적합 합니다. 데이터에서에서 삭제 됩니다 백업 저장소 중 하나를 호출할 때 `Session.Clear` 하거나 데이터 저장소의 세션이 만료 되는 경우. 브라우저를 닫을 때 세션 쿠키를 삭제 하는 경우 또는 서버를 알지 못합니다.

> [!WARNING]
> 세션에서 중요 한 데이터를 저장 하지 마십시오. 클라이언트 수 하지 브라우저를 닫고 세션 쿠키의 선택을 취소 (및 일부 브라우저 세션 쿠키 활성화 상태로 유지 창에서). 또한 세션; 단일 사용자로 제한 될 수 있습니다. 다음 사용자는 동일한 세션으로 계속 될 수 있습니다.

메모리 내 세션 공급자는 로컬 서버에서 세션 데이터를 저장합니다. 서버 팜에서 웹 응용 프로그램을 실행 하려는 경우 특정 서버에 각 세션에 연결할 고정 세션을 사용 해야 합니다. Windows Azure 웹 사이트 플랫폼 고정 세션 (응용 프로그램 요청 라우팅 또는 ARR) 기본값으로 사용 됩니다. 그러나 고정 세션 확장성에 영향을 하 고 웹 응용 프로그램 업데이트 작업이 복잡 해질 수 있습니다. Redis를 사용 하 여 더 나은 옵션 또는 SQL Server 분산 캐시는 고정 세션 필요가 없습니다. 자세한 내용은 참조 [분산 캐시 작업](xref:performance/caching/distributed)합니다. 서비스 공급자를 설정에 대 한 자세한 내용은 참조 하십시오. [세션 구성](#configuring-session) 이 문서의 뒷부분에 나오는 합니다.

이 섹션의 나머지 부분에서는 사용자 데이터를 저장 하기 위한 옵션을 설명 합니다.

### <a name="tempdata"></a>TempData

ASP.NET Core MVC 노출는 [TempData](https://docs.microsoft.com/en-us/aspnet/core/api/microsoft.aspnetcore.mvc.controller#Microsoft_AspNetCore_Mvc_Controller_TempData) 속성에는 [컨트롤러](https://docs.microsoft.com/en-us/aspnet/core/api/microsoft.aspnetcore.mvc.controller)합니다. 이 속성 현재만 단일 요청에 대 한 데이터를 저장합니다.`TempData` 두 개 이상의 단일 요청에 대 한 데이터가 필요한 경우 리디렉션에 특히 유용 합니다. `TempData`세션 상태를 기반으로 빌드됩니다. 

## <a name="cookie-based-tempdata-provider"></a>쿠키 기반 TempData 공급자 

ASP.NET 핵심 1.1 이상에서는 사용자의 TempData를 쿠키에 저장 하려면 TempData 쿠키 기반 공급자를 사용할 수 있습니다. 쿠키 기반 TempData 공급자를 사용 하려면 등록 된 `CookieTempDataProvider` 서비스에 `ConfigureServices`:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();
    // Add CookieTempDataProvider after AddMvc and include ViewFeatures.
    // using Microsoft.AspNetCore.Mvc.ViewFeatures;
    services.AddSingleton<ITempDataProvider, CookieTempDataProvider>();
}
```

쿠키 데이터가 인코딩된는 [Base64UrlTextEncoder](https://docs.microsoft.com/en-us/aspnet/core/api/microsoft.aspnetcore.authentication.base64urltextencoder)합니다. 쿠키를 암호화 및 청크 분할 하기 때문에 단일 쿠키 크기 제한이 적용 되지 않습니다. 쿠키 데이터가 압축 되지 않으며와 같은 보안 문제를 일으킬 수 encryped 데이터를 압축 하기 때문에 [범죄](https://en.wikipedia.org/wiki/CRIME_(security_exploit)) 및 [위반](https://en.wikipedia.org/wiki/BREACH_(security_exploit)) 공격입니다. 쿠키 기반 TempData 공급자에 대 한 자세한 내용은 참조 하십시오. [CookieTempDataProvider](https://github.com/aspnet/Mvc/blob/dev/src/Microsoft.AspNetCore.Mvc.ViewFeatures/ViewFeatures/CookieTempDataProvider.cs)합니다.



### <a name="query-strings"></a>쿼리 문자열

새 요청의 쿼리 문자열에 추가 하 여 다른 요청에서 제한 된 양의 데이터를 전달할 수 있습니다. 이 상태에 포함 된 상태를 전자 메일 이나 소셜 네트워크를 통해 공유할 수 있는 링크를 허용 하는 영구적인 방식으로 캡처하는 데 유용 합니다. 그러나 이러한 이유로 하지 사용 해야 쿼리 문자열 중요 한 데이터입니다. 쉽게 공유 되는 것 외에도 쿼리 문자열에 데이터를 포함 하 여 만들 수에 대 한 기회 [교차 사이트 요청 위조 CSRF ()](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) 공격 사용자를 인증 하는 동안 악성 사이트 방문 하도록 속일 수 있습니다. 공격자가 다음 응용 프로그램에서 사용자 데이터를 도용 하거나 사용자를 대신 하 여 악의적인 작업을 수행할 수 있습니다. 유지 된 모든 응용 프로그램 또는 세션 상태는 CSRF 공격 으로부터 보호 해야 합니다. CSRF 공격에 대 한 자세한 내용은 참조 하십시오. [ASP.NET 핵심에서 방지 교차 사이트 요청 위조 XSRF/CSRF () 공격](../security/anti-request-forgery.md)합니다.

### <a name="post-data-and-hidden-fields"></a>게시 데이터 및 숨겨진된 필드

데이터 숨겨진된 폼 필드에 저장 하 고 요청에 다시 게시 될 수 있습니다. 이 여러 페이지로 구성 된 양식에서 일반적입니다. 그러나 클라이언트 잠재적으로 데이터를 변조할 수, 때문에 서버 해야 항상 유효성입니다. 

### <a name="cookies"></a>쿠키

쿠키는 웹 응용 프로그램에서 사용자 고유의 데이터를 저장 하는 방법을 제공 합니다. 쿠키는 각 요청과 함께 전송 되므로, 해당 크기를 최소로 유지 되어야 합니다. 이상적으로 서버에 저장 된 실제 데이터와 식별자만 쿠키에 저장 되어야 합니다. 대부분의 브라우저는 4096 바이트에 쿠키를 제한합니다. 또한 제한 된 수의 쿠키는 각 도메인에 대해 사용할 수 있습니다.  

변조 될 쿠키는 이기 때문에 서버에서 검증 해야 합니다. 클라이언트에서 쿠키의 내구성 사용자 작업 및 만료 될 수 없지만, 가장 안정적인 형식의 클라이언트에서 데이터 지 속성은 일반적으로.

쿠키는 자주 사용 개인 설정에 대 한 콘텐츠 알려진된 사용자에 대 한 사용자 지정 합니다. 사용자만 식별 및 대부분의 경우에서 인증 되지 않은 볼륨을 쿠키에 사용자 이름, 계정 이름 또는 고유한 사용자 ID (예: GUID)를 저장 하 여 쿠키를 일반적으로 보호할 수 있습니다. 그런 다음 사이트의 사용자 개인 설정 인프라에 액세스 하는 쿠키를 사용할 수 있습니다.

### <a name="httpcontextitems"></a>HttpContext.Items

`Items` 컬렉션은 데이터를 저장할 수 있는 좋은 위치가 하나의 특정 요청을 처리 동안에 필요 합니다. 컬렉션의 각 요청 후 삭제 됩니다. `Items` 컬렉션은 가장 요청 중의 여러 지점에서 작동 하 고 직접 매개 변수를 전달할 수 없습니다 될 때를 알리기 위해 구성 요소 또는 미들웨어에 대 한 방법으로 사용 됩니다. 자세한 내용은 참조 [HttpContext.Items 작업](#working-with-httpcontextitems)이 문서의 뒷부분에 나오는 합니다.

### <a name="cache"></a>캐시

캐싱은 데이터 저장 및 검색 하는 효율적인 방법입니다. 시간 및 기타 고려 사항에 따라 캐시 된 항목의 수명을 제어할 수 있습니다. 에 대 한 자세한 내용은 [캐싱](../performance/caching/index.md)합니다.

<a name=session></a>

## <a name="configuring-session"></a>세션 구성

`Microsoft.AspNetCore.Session` 패키지는 세션 상태를 관리 하기 위한 미들웨어를 제공 합니다. 세션 미들웨어를 사용 하도록 설정 하려면 `Startup`포함 해야 합니다.

- 중 하나는 [IDistributedCache](https://docs.microsoft.com/en-us/aspnet/core/api/microsoft.extensions.caching.distributed.idistributedcache) 메모리 캐시 합니다. `IDistributedCache` 구현이 세션에 대 한 백업 저장소로 사용 됩니다.
- [AddSession](https://docs.microsoft.com/en-us/aspnet/core/api/microsoft.extensions.dependencyinjection.sessionservicecollectionextensions#Microsoft_Extensions_DependencyInjection_SessionServiceCollectionExtensions_AddSession_Microsoft_Extensions_DependencyInjection_IServiceCollection_) 호출을 "Microsoft.AspNetCore.Session" NuGet 패키지입니다.
- [UseSession](https://docs.microsoft.com/en-us/aspnet/core/api/microsoft.aspnetcore.builder.sessionmiddlewareextensions#methods_) 를 호출 합니다.

다음 코드에는 메모리 내 세션 공급자를 설정 하는 방법을 보여 줍니다.

[!code-csharp[주](app-state/sample/src/WebAppSession/Startup.cs?highlight=11-19,24)]

세션을 참조할 수 있습니다 `HttpContext` 일단 설치 되 고 구성 합니다.

액세스 하려는 경우 `Session` 전에 `UseSession` 가 호출 된 예외 `InvalidOperationException: Session has not been configured for this application or request` 이 throw 됩니다.

새 하려고 하면 `Session` (즉, 세션 쿠키가 없는 만들어진)에 쓰기를 이미 시작한 후의 `Response` 스트림, 예외 `InvalidOperationException: The session cannot be established after the response has started` 이 throw 됩니다. 웹 서버 로그에서 예외를 확인할 수 있습니다. 브라우저에 표시 되지 않습니다.

### <a name="loading-session-asynchronously"></a>세션을 비동기적으로 로드 

기본 세션 레코드를 로드 하는 ASP.NET 코어의 기본 세션 공급자 [IDistributedCache](https://docs.microsoft.com/en-us/aspnet/core/api/microsoft.extensions.caching.distributed.idistributedcache) 경우에만 비동기적으로 저장소는 [ISession.LoadAsync](https://docs.microsoft.com/en-us/aspnet/core/api/microsoft.aspnetcore.http.isession#Microsoft_AspNetCore_Http_ISession_LoadAsync) 전에 메서드가 명시적으로 호출 되는 `TryGetValue`, `Set`, 또는 `Remove` 메서드. 경우 `LoadAsync` 가 호출 되지 않습니다 먼저 내부는 영향을 줄 수는 앱의 수평 확장 기능은 세션 레코드 동기적으로 로드 합니다.

이 패턴을 적용 하는 응용 프로그램을 래핑하는 [DistributedSessionStore](https://docs.microsoft.com/en-us/aspnet/core/api/microsoft.aspnetcore.session.distributedsessionstore) 및 [DistributedSession](https://docs.microsoft.com/en-us/aspnet/core/api/microsoft.aspnetcore.session.distributedsession) 구현 하면 예외가 throw 된 버전으로는 `LoadAsync` 하기 전에 메서드가 호출 되지 않습니다 `TryGetValue`, `Set`, 또는 `Remove`합니다. 서비스 컨테이너에 래핑된 버전을 등록 합니다.

### <a name="implementation-details"></a>구현 세부 정보

세션 쿠키를 사용 하 여 추적 하 고 단일 브라우저에서 요청을 식별 합니다. 기본적으로이 쿠키 이름은 "입니다. AspNet.Session "를 사용 하 여 경로"/"입니다. 쿠키 기본 도메인을 지정 하지 않으므로, 만들지 않는 클라이언트 쪽 스크립트에 사용할 수 있는 페이지 (때문에 `CookieHttpOnly` 기본값으로 `true`).

기본값을 재정의 세션을 사용 하 여 `SessionOptions`:

[!code-csharp[주](app-state/sample/src/WebAppSession/StartupCopy.cs?name=snippet1&highlight=8-12)]

서버에서 사용 하는 `IdleTimeout` 시간 세션 유휴 상태일 수 있는 내용이 중단 하기 전에 확인 하는 속성입니다. 이 속성은 쿠키의 기본 만료 무관 합니다. (에서 읽기 또는 쓰기) 세션 미들웨어를 통해 전달 되는 각 요청은 제한 시간이 다시 설정 합니다.

때문에 `Session` 는 *잠기지 않은*마지막 세션의 내용을 수정 하려고 모두 두 개의 요청에는 첫 번째 재정의 하는 경우. `Session`로 구현 되는 *일관 된 세션*, 즉, 모든 내용을 함께 저장 됩니다. (다른 키) 세션의 다른 부분을 수정 하는 두 개의 요청 서로 영향 계속 될 수 있습니다.

## <a name="setting-and-getting-session-values"></a>설정 및 세션 값 가져오기

세션을 통해 액세스는 `Session` 속성을 `HttpContext`합니다. 이 속성은 한 [ISession](https://docs.microsoft.com/en-us/aspnet/core/api/microsoft.aspnetcore.http.isession) 구현 합니다.

다음 예제에서는 설정 및 가져오기는 int와 문자열을 보여 줍니다.

[!code-csharp[주](app-state/sample/src/WebAppSession/Controllers/HomeController.cs?name=snippet1)]

다음 확장 메서드를 추가 하는 경우에 설정와 세션에 직렬화 가능 개체를 가져올 수 있습니다.

[!code-csharp[주](app-state/sample/src/WebAppSession/Extensions/SessionExtensions.cs)]

다음 샘플에는 설정 하 고 직렬화 가능 개체를 가져오는 방법을 보여 줍니다.

[!code-csharp[주](app-state/sample/src/WebAppSession/Controllers/HomeController.cs?name=snippet2)]


## <a name="working-with-httpcontextitems"></a>HttpContext.Items 작업

`HttpContext` 추상화 형식의 간단한 사전 컬렉션에 대 한 지원을 제공 `IDictionary<object, object>`라는 `Items`합니다. 이 컬렉션은 시작 위치에서 사용할 수는 *HttpRequest* 끝 각 요청에는 삭제 됩니다. 키가 지정 된 항목에 값을 할당 하거나 특정 키에 대 한 값을 요청 하 여 액세스할 수 있습니다.

아래 샘플에서 [미들웨어](middleware.md) 추가 `isVerified` 에 `Items` 컬렉션입니다.

```csharp
app.Use(async (context, next) =>
{
    // perform some verification
    context.Items["isVerified"] = true;
    await next.Invoke();
});
```

파이프라인의 뒷부분에 나오는 다른 미들웨어 예제에 액세스할 수 있습니다.

```csharp
app.Run(async (context) =>
{
    await context.Response.WriteAsync("Verified request? " + context.Items["isVerified"]);
});
```

참고: 키에 `Items` 는 간단한 문자열입니다. 여러 응용 프로그램에서 작업 해야 하는 미들웨어를 개발 하는 경우에 키 (예를 들어 "MyComponent.isVerified" 대신 "isVerified") 키 충돌을 방지 하기 고유 식별자와 접두사를 제공 하는 것이 좋습니다.

<a name=appstate-errors></a>

## <a name="application-state-data"></a>응용 프로그램 상태 데이터

사용 하 여 [종속성 주입](xref:fundamentals/dependency-injection) 모든 사용자에 게 데이터를 사용할 수 있도록 하려면:

1. 데이터를 포함 하는 서비스 정의 (예를 들어 라는 클래스 `MyAppData`).

```csharp
public class MyAppData
{
    // Declare properties/methods/etc.
} 
```
2. 서비스 클래스를 추가 `ConfigureServices` (예를 들어 `services.AddSingleton<MyAppData>();`합니다.
3. 각 컨트롤러에 데이터 서비스 클래스를 사용 합니다.

```csharp
public class MyController : Controller
{
    public MyController(MyAppData myService)
    {
        // Do something with the service (read some data from it, 
        // store it in a private field/property, etc.
    }
    }
} 
```

### <a name="common-errors-when-working-with-session"></a>세션 작업 시 일반적인 오류

* "'Microsoft.AspNetCore.Session.DistributedSessionStore'를 활성화 하는 동안 'Microsoft.Extensions.Caching.Distributed.IDistributedCache' 형식에 대 한 서비스를 확인할 수 없습니다."

  일반적으로는 하나 이상 구성 하지 못하면 `IDistributedCache` 구현 합니다. 자세한 내용은 참조 [분산 캐시 작업](xref:performance/caching/distributed) 및 [를 메모리 내 캐시에서](xref:performance/caching/memory)합니다.

### <a name="additional-resources"></a>추가 리소스


* [이 문서에 사용 되는 샘플 코드](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/app-state/sample/src/WebAppSession)

