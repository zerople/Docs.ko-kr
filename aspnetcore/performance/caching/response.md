---
title: "응답 캐시"
author: rick-anderson
description: "응답을 대역폭을 줄이고 성능을 향상 하려면 캐시를 사용 하는 방법에 설명 합니다."
keywords: "ASP.NET Core, 캐싱, HTTP 헤더 응답"
ms.author: riande
manager: wpickett
ms.date: 07/10/2017
ms.topic: article
ms.assetid: cb42035a-60b0-472e-a614-cb79f443f654
ms.prod: asp.net-core
uid: performance/caching/response
ms.openlocfilehash: af114401d2f6f183291caba3c015359afb737d93
ms.sourcegitcommit: 78d28178345a0eea91556e4cd1adad98b1446db8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2017
---
# <a name="response-caching"></a>응답 캐시

여 [John Luo](https://github.com/JunTaoLuo), [Rick Anderson](https://twitter.com/RickAndMSFT), 및 [Steve Smith](https://ardalis.com/)

[샘플 코드 보기 또는 다운로드](https://github.com/aspnet/Docs/tree/master/aspnetcore/performance/caching/response/sample)

## <a name="what-is-response-caching"></a>응답 캐시 하는 것이 무엇입니까

*응답 캐시* 응답을 캐시 관련 헤더를 추가 합니다. 이러한 헤더 클라이언트, 프록시 및 응답을 캐시 미들웨어 용도 지정 합니다. 응답 캐시 클라이언트 또는 프록시 웹 서버에 수행 된 요청 수를 줄일 수 있습니다. 응답 캐시 양을 줄일 수도 작업의 응답을 생성 하는 웹 서버 수행 합니다. 

캐시에 사용 되는 기본 HTTP 헤더는 `Cache-Control`합니다. 참조는 [HTTP 1.1 캐싱](https://tools.ietf.org/html/rfc7234#section-5.2) 자세한 정보에 대 한 합니다. 같은 캐시 지시문:

* [public](https://tools.ietf.org/html/rfc7234#section-5.2.2.5)
* [private](https://tools.ietf.org/html/rfc7234#section-5.2.2.6)
* [캐시 없음](https://tools.ietf.org/html/rfc7234#section-5.2.1.4)
* [Pragma](https://tools.ietf.org/html/rfc7234#section-5.4)
* [변경](https://tools.ietf.org/html/rfc7231#section-7.1.4)

웹 서버 캐싱 미들웨어의 응답을 추가 하 여 응답을 캐시할 수 있습니다. 참조 [미들웨어 캐싱 응답](middleware.md) 자세한 정보에 대 한 합니다.

## <a name="distributed-cache-tag-helper"></a>분산된 캐시 태그 도우미

[분산 캐시 태그 도우미](xref:mvc/views/tag-helpers/builtin-th/DistributedCacheTagHelper) 하면 분산 캐싱 합니다.


## <a name="responsecache-attribute"></a>ResponseCache 특성

`ResponseCacheAttribute` 응답 캐시에서 적절 한 헤더를 설정 하는 데 필요한 매개 변수를 지정 합니다. 참조 [ResponseCacheAttribute](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.responsecacheattribute) 에 대 한 설명은 매개 변수입니다.

>[!WARNING]
> 인증 된 클라이언트에 대 한 정보를 포함 하는 콘텐츠에 대 한 캐싱을 사용 하지 않도록 설정 합니다. Caching에 설정 해야 여부나 사용자가 로그인에 사용자의 id를 기반으로 하는 내용이 변경 되지 않습니다.

`VaryByQueryKeys string[]`(ASP.NET Core 1.1.0 필요 이상):으로 설정 하면 미들웨어 캐싱 응답 저장된 응답에 따라 달라 집니다 쿼리 키의 지정 된 목록의 값입니다. 캐싱 미들웨어의 응답을 설정 하려면 설정 해야 합니다는 `VaryByQueryKeys` 속성, 그렇지 않으면 런타임 예외가 throw 됩니다. 에 대 한 해당 HTTP 헤더가 없으면는 `VaryByQueryKeys` 속성입니다. 이 속성은 캐싱 미들웨어의 응답에서 처리 하는 HTTP 기능. 캐시 된 응답을 제공 하도록 미들웨어에 대 한 쿼리 문자열 및 쿼리 문자열 값을 이전 요청을 일치 해야 합니다. 예를 들어 다음 시퀀스를 살펴봅니다.

| 요청          | 결과 |
| ----------------- | ------------ | 
| `http://example.com?key1=value1` | 서버에서 반환 된 |
| `http://example.com?key1=value1` | 미들웨어에서 반환 |
| `http://example.com?key1=value2` | 서버에서 반환 된 |

첫 번째 요청이 서버에서 반환 하 고 미들웨어에 캐시 합니다. 이전 요청과 일치 하는 쿼리 문자열 때문에 두 번째 요청 미들웨어에서 반환 됩니다. 세 번째 요청에에서 없는 경우 미들웨어 캐시에서 쿼리 문자열 값인 이전 요청이 일치 하지 않으므로 

`ResponseCacheAttribute` 는 구성 하 고 만드는 데 사용 됩니다 (통해 `IFilterFactory`)는 `ResponseCacheFilter`합니다. `ResponseCacheFilter` 적절 한 HTTP 헤더 및 응답의 기능을 업데이트 하는 작업을 수행 합니다. 필터:

* 에 대 한 모든 기존 헤더를 제거 `Vary`, `Cache-Control`, 및 `Pragma`합니다. 
* 에 설정 된 속성에 따라 적절 한 헤더를 작성은 `ResponseCacheAttribute`합니다. 
* 업데이트 하는 경우에 캐싱 HTTP 기능 응답 `VaryByQueryKeys` 설정 됩니다.

### <a name="vary"></a>변경

이 헤더가 작성만 되 고 `VaryByHeader` 속성을 설정 합니다. 로 설정 되 고 `Vary` 속성의 값입니다. 다음 샘플에서는 `VaryByHeader` 속성입니다.

[!code-csharp[Main](response/sample/Controllers/HomeController.cs?name=snippet_VaryByHeader&highlight=1)]

브라우저 네트워크 도구와 함께 응답 헤더를 볼 수 있습니다. 다음 이미지에서 출력 가장자리 f12 키를 보여 줍니다.는 **네트워크** 때 탭의 `About2` 동작 메서드가 새로 고쳐집니다. 

![F12 출력에서 가장자리는 * * 네트워크 * * 'About2' 동작 메서드가 호출 될 때 탭](response/_static/vary.png)

### <a name="nostore-and-locationnone"></a>NoStore 및 Location.None

`NoStore`대부분의 다른 속성을 재정의합니다. 이 속성이로 설정 된 경우 `true`, `Cache-Control` 헤더 "store"로 설정 됩니다. 경우 `Location` 로 설정 된 `None`:

* `Cache-Control`이 `"no-store, no-cache"`로 설정됩니다. 
* `Pragma`이 `no-cache`로 설정됩니다. 

경우 `NoStore` 은 `false` 및 `Location` 은 `None`, `Cache-Control` 및 `Pragma` 로 설정 됩니다 `no-cache`합니다.

일반적으로 설정 `NoStore` 를 `true` 오류 페이지에 있습니다. 예:

[!code-csharp[Main](response/sample/Controllers/HomeController.cs?name=snippet1&highlight=1)]

이렇게 하면 다음과 같은 헤더가:

```javascript
Cache-Control: no-store,no-cache
Pragma: no-cache
```

### <a name="location-and-duration"></a>위치 및 기간

캐싱을 사용 하도록 설정 하려면 `Duration` 양수 값으로 설정 되어 있어야 하 고 `Location` 납입이 되어야 `Any` (기본값) 또는 `Client`합니다. 이 경우에 `Cache-Control` 헤더는 "최대-기간" 응답의 다음 위치 값으로 설정 됩니다.

> [!NOTE]
> `Location`옵션의 `Any` 및 `Client` 번역할 `Cache-Control` 의 헤더 값 `public` 및 `private`각각. 앞에서 설명한 대로 설정 `Location` 를 `None` 는 둘 다 설정 `Cache-Control` 및 `Pragma` 헤더를 `no-cache`합니다.

다음을 설정 하 여 생성 된 헤더를 보여 주는 예제는 `Duration` 기본 들어오거나 `Location` 값입니다.

[!code-csharp[Main](response/sample/Controllers/HomeController.cs?name=snippet_duration&highlight=1)]

다음 헤더를 생성합니다.

```javascript
Cache-Control: public,max-age=60
   ```

### <a name="cache-profiles"></a>캐시 프로필

복제 하는 대신 `ResponseCache` 에 MVC를 설정할 때 옵션으로 많은 컨트롤러 동작 특성에 캐시 프로필에서 설정을 구성할 수 있습니다는 `ConfigureServices` 메서드에서 `Startup`합니다. 기본적으로 참조 된 캐시 프로필의 값이 사용 됩니다는 `ResponseCache` 특성과 특성에 지정 된 임의 속성으로 재정의 됩니다.

캐시 프로필을 설정 합니다.

[!code-csharp[Main](response/sample/Startup.cs?name=snippet1)] 

캐시 프로필을 참조 합니다.

[!code-csharp[Main](response/sample/Controllers/HomeController.cs?name=snippet_controller&highlight=1,4)]

`ResponseCache` 모두 작업 (메서드) 뿐만 아니라 컨트롤러 (클래스)에 특성을 적용할 수 있습니다. 메서드 수준 특성 클래스 수준 특성에 지정 된 설정을 재정의 합니다.

위의 예제에서는 클래스 수준 특성 메서드 수준의 특성 기간이 60 초로 설정 캐시 프로필을 참조 하는 동안 30 초의 지속 시간을 지정 합니다.

결과 헤더:

```
Cache-Control: public,max-age=60
   ```

  ### <a name="additional-resources"></a>추가 리소스

* [사양에서 HTTP에서 캐싱](https://tools.ietf.org/html/rfc7234#section-3)
* [캐시 제어](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9)
