---
title: "응답 캐싱은 | Microsoft 문서"
author: rick-anderson
description: "응답 하기 위해 대역폭을 줄이고 성능을 향상 시킬 캐싱을 사용 하는 방법에 설명 합니다."
keywords: "ASP.NET Core, 캐싱, HTTP 헤더는 응답"
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: cb42035a-60b0-472e-a614-cb79f443f654
ms.prod: asp.net-core
uid: performance/caching/response
translationtype: Machine Translation
ms.sourcegitcommit: 010b730d2716f9f536fef889bc2f767afb648ef4
ms.openlocfilehash: 9b58cea9622642ee6a71cb29e0213ad5cc06e349
ms.lasthandoff: 03/23/2017

---
# <a name="response-caching"></a>응답 캐싱

여 [John Luo](https://github.com/JunTaoLuo), [Rick Anderson](https://twitter.com/RickAndMSFT), 및 [Steve Smith](http://ardalis.com)

[샘플 코드 보기 또는 다운로드](https://github.com/aspnet/Docs/tree/master/aspnetcore/performance/caching/response/sample)

## <a name="what-is-response-caching"></a>응답 캐싱은 하는 것이 무엇입니까

*응답 캐싱은* 캐시 관련 헤더가 응답에 추가 합니다. 이러한 헤더 지정 방법을 클라이언트, 프록시 및 미들웨어 응답을 캐시 합니다. 응답 캐싱은 웹 서버에는 클라이언트 또는 프록시를 통해 요청 수를 줄일 수 있습니다. 응답 캐싱은 양을 줄일 수도 작업의 응답을 생성 하는 웹 서버 수행 합니다. 

캐싱에 사용 되는 기본 HTTP 헤더는 `Cache-Control`합니다. 참조는 [HTTP 1.1 캐싱](https://tools.ietf.org/html/rfc7234#section-5.2) 에 대 한 자세한 내용은 합니다. 일반적인 캐시 지시문:

* [public](https://tools.ietf.org/html/rfc7234#section-5.2.2.5)
* [private](https://tools.ietf.org/html/rfc7234#section-5.2.2.6)
* [캐시 없음](https://tools.ietf.org/html/rfc7234#section-5.2.1.4)
* [Pragma](https://tools.ietf.org/html/rfc7234#section-5.4)
* [다](https://tools.ietf.org/html/rfc7231#section-7.1.4)

웹 서버는 미들웨어 캐싱 응답을 추가 하 여 응답을 캐시할 수 있습니다. 참조 [미들웨어 캐싱 응답](middleware.md) 에 대 한 자세한 내용은 합니다.

## <a name="responsecache-attribute"></a>ResponseCache 특성

`ResponseCacheAttribute` 응답 캐시에서 적절 한 헤더를 설정 하는 데 필요한 매개 변수를 지정 합니다. 참조 [ResponseCacheAttribute](https://docs.microsoft.com/en-us/aspnet/core/api/microsoft.aspnetcore.mvc.responsecacheattribute) 에 대 한 설명은 매개 변수입니다.

`VaryByQueryKeys string[]`(ASP.NET 핵심 1.1.0 필요 이상): 설정 되 면, 미들웨어 캐싱 응답 저장된 응답에 따라 달라 집니다 주어진된 목록이 쿼리 키의 값입니다. 미들웨어 캐싱 응답 설정 하도록 설정 해야는 `VaryByQueryKeys` 속성, 그렇지 않으면 런타임 예외가 throw 됩니다. 에 대 한 해당 HTTP 헤더가 `VaryByQueryKeys` 속성입니다. 이 속성은 미들웨어 캐싱 응답에서 처리 하는 HTTP 기능입니다. 캐시 된 응답을 제공 하는 미들웨어, 쿼리 문자열 및 쿼리 문자열 값에는 이전 요청을 일치 해야 합니다. 예를 들어 다음 시퀀스를 살펴봅니다.

| 요청          | 결과 |
| ----------------- | ------------ | 
| `http://example.com?key1=value1` | 서버에서 반환 |
| `http://example.com?key1=value1` | 미들웨어에서 반환 된 |
| `http://example.com?key1=value2` | 서버에서 반환 |

첫 번째 요청이 서버에서 반환 하 고 미들웨어에 캐시 됩니다. 두 번째 요청은 쿼리 문자열에는 이전 요청 일치 하기 때문에 미들웨어에 의해 반환 됩니다. 세 번째 요청에에서 없는 경우 미들웨어 캐시는 쿼리 문자열 값과 일치 하는 이전 요청 때문에 

`ResponseCacheAttribute` 구성 하 고 만들어야 하는 데 사용 됩니다 (통해 `IFilterFactory`)는 `ResponseCacheFilter`합니다. `ResponseCacheFilter` 적절 한 HTTP 헤더 및 응답의 기능 업데이트 작업을 수행 합니다. 필터:

* 에 대 한 모든 기존 헤더를 제거 `Vary`, `Cache-Control`, 및 `Pragma`합니다. 
* 에 설정 된 속성에 따라 적절 한 헤더를 작성은 `ResponseCacheAttribute`합니다. 
* 업데이트 하는 경우 HTTP 기능 캐싱을 응답 `VaryByQueryKeys` 설정 됩니다.

### <a name="vary"></a>다

이 헤더 기록만 되는 `VaryByHeader` 속성을 설정 합니다. 로 설정 되 고 `Vary` 속성의 값입니다. 다음 샘플에서는 `VaryByHeader` 속성입니다.

[!code-csharp[주](response/sample/Controllers/HomeController.cs?name=snippet_VaryByHeader&highlight=1)]

브라우저 네트워크 도구로 응답 헤더를 볼 수 있습니다. 다음 이미지에서 출력 하 고 가장자리 f12 키를 보여 줍니다.는 **네트워크** 때 탭의 `About2` 작업 메서드가 새로 고쳐집니다. 

![F12 출력에서 가장자리는 * * 네트워크 * * 탭 'About2' 작업 메서드를 호출할 때](response/_static/vary.png)

### <a name="nostore-and-locationnone"></a>NoStore 및 Location.None

`NoStore`대부분의 다른 속성을 재정의합니다. 이 속성 설정 된 경우 `true`, `Cache-Control` 헤더 "store"로 설정 됩니다. 경우 `Location` 로 설정 된 `None`:

* `Cache-Control`이 `"no-store, no-cache"`로 설정됩니다. 
* `Pragma`이 `no-cache`로 설정됩니다. 

If `NoStore` is `false` and `Location` is `None`,  `Cache-Control` and `Pragma` will be set to `no-cache`.

일반적으로 설정 `NoStore` 를 `true` 오류 페이지에 있습니다. 예:

[!code-csharp[주](response/sample/Controllers/HomeController.cs?name=snippet1&highlight=1)]

이 다음과 같은 헤더가 발생 합니다.

```javascript
Cache-Control: no-store,no-cache
Pragma: no-cache
```

### <a name="location-and-duration"></a>위치 및 기간

캐싱을 사용 하도록 설정 하려면 `Duration` 양수 값으로 설정 되어 있어야 하 고 `Location` 납입이 되어야 `Any` (기본값) 또는 `Client`합니다. 이 경우에 `Cache-Control` 헤더 뒤에 "최대 보존 기간" 응답의 위치 값으로 설정 됩니다.

> [!NOTE]
> `Location`옵션의 `Any` 및 `Client` 입장 `Cache-Control` 의 헤더 값 `public` 및 `private`각각. 이전에 언급 했 듯이 설정 `Location` 를 `None` 모두 설정 됩니다 `Cache-Control` 및 `Pragma` 헤더를 `no-cache`합니다.

아래 설정 하 여 생성 된 헤더를 보여 주는 예제는 `Duration` 기본 자리를 비울 때 `Location` 값입니다.

[!code-csharp[주](response/sample/Controllers/HomeController.cs?name=snippet_duration&highlight=1)]

다음 헤더를 생성합니다.

```javascript
Cache-Control: public,max-age=60
   ```

### <a name="cache-profiles"></a>캐시 프로필

복제 하는 대신 `ResponseCache` 에서 MVC를 설정할 때 옵션으로 많은 컨트롤러 동작 특성을 캐시 프로필에서 설정을 구성할 수 있습니다는 `ConfigureServices` 메서드에서 `Startup`합니다. 참조 된 캐시 프로필에서 찾은 값에서 기본값으로 사용 됩니다는 `ResponseCache` 특성과 특성에 지정 된 모든 속성으로 재정의 됩니다.

캐시 프로필을 설정 합니다.

[!code-csharp[주](response/sample/Startup.cs?name=snippet1)] 

캐시 프로필을 참조 합니다.

[!code-csharp[주](response/sample/Controllers/HomeController.cs?name=snippet_controller&highlight=1,4)]

`ResponseCache` 모두 컨트롤러 (클래스 클래스) 뿐만 아니라 작업 (메서드)에 특성을 적용할 수 있습니다. 메서드 수준 특성이 클래스 수준 특성에 지정 된 설정을 재정의 합니다.

위의 예제에서는 클래스 수준 특성을 메서드 수준 특성을 60 초로 설정 하는 기간이 포함 된 캐시 프로필을 참조 하는 동안 30 초의 지속 시간을 지정 합니다.

결과 헤더:

```
Cache-Control: public,max-age=60
   ```

  ### <a name="additional-resources"></a>추가 리소스

* [HTTP 사양에서 캐싱](https://tools.ietf.org/html/rfc7234#section-3)
* [캐시 제어](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9)

