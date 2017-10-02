---
title: "ASP.NET Core의 응답 캐싱"
author: rick-anderson
description: "응답을 대역폭을 줄이고 성능을 향상 하려면 캐시를 사용 하는 방법에 알아봅니다."
keywords: "ASP.NET Core, 캐싱, HTTP 헤더 응답"
ms.author: riande
manager: wpickett
ms.date: 09/20/2017
ms.topic: article
ms.assetid: cb42035a-60b0-472e-a614-cb79f443f654
ms.prod: asp.net-core
uid: performance/caching/response
ms.openlocfilehash: 79d9246632aae0fe9c3629fd7202842836828151
ms.sourcegitcommit: 732cd2684246e49e796836596643a8d37e20c46d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2017
---
# <a name="response-caching-in-aspnet-core"></a>ASP.NET Core의 응답 캐싱

여 [John Luo](https://github.com/JunTaoLuo), [Rick Anderson](https://twitter.com/RickAndMSFT), [Steve Smith](https://ardalis.com/), 및 [Luke Latham](https://github.com/guardrex)

[보거나 다운로드 샘플 코드](https://github.com/aspnet/Docs/tree/master/aspnetcore/performance/caching/response/sample) ([다운로드 하는 방법을](xref:tutorials/index#how-to-download-a-sample))

응답 캐시 클라이언트 또는 프록시 웹 서버에 수행 된 요청 수가 줄어듭니다. 응답 캐시도 줄일 수 작업의 응답을 생성 하는 웹 서버 수행 합니다. 응답 캐시 클라이언트, 프록시 및 응답을 캐시 미들웨어 용도 지정 하는 헤더에 의해 제어 됩니다.

추가 하면 웹 서버 응답을 캐시할 수 [응답 캐싱 미들웨어](xref:performance/caching/middleware)합니다.

## <a name="http-based-response-caching"></a>응답 HTTP 기반 캐싱

[HTTP 1.1 캐싱 사양](https://tools.ietf.org/html/rfc7234) 인터넷 캐시 동작 방식에 대해 설명 합니다. 캐시에 사용 되는 기본 HTTP 헤더는 [캐시 제어](https://tools.ietf.org/html/rfc7234#section-5.2), 캐시를 지정 하는 데 사용 되는 *지시문*합니다. 지시문은 요청 하 게 클라이언트에서 서버로 하면서 되 고 응답 하 게 서버에서 클라이언트로 다시의 캐싱 동작을 제어 합니다. 프록시 서버를 통해 이동 하는 요청 및 응답 하 고 프록시 서버 또한 HTTP 1.1 캐싱 사양을 준수 해야 합니다.

일반적인 `Cache-Control` 지시문 다음 표에 나와 있습니다.

| 지시문                                                       | 작업 |
| --------------------------------------------------------------- | ------ |
| [public](https://tools.ietf.org/html/rfc7234#section-5.2.2.5)   | 캐시 된 응답을 저장할 수 있습니다. |
| [private](https://tools.ietf.org/html/rfc7234#section-5.2.2.6)  | 공유 캐시에서 응답을 저장 되어야 합니다. 개인 캐시는 저장 하 고 응답을 다시 사용할 수 있습니다. |
| [최대 처리 기간](https://tools.ietf.org/html/rfc7234#section-5.2.1.1)  | 클라이언트는 오래 된 시간 (초) 지정 된 숫자 보다 큰 경우 응답을 수락 하지 않습니다. 예: `max-age=60` (60 초) `max-age=2592000` (1 월) |
| [캐시 없음](https://tools.ietf.org/html/rfc7234#section-5.2.1.4) | **요청에서**: 요청을 충족 하도록 저장된 응답 캐시를 사용 하지 않아야 합니다. 참고:는 원본 서버는 클라이언트에 대 한 응답을 다시 생성 하 고 미들웨어 캐시에 저장 된 응답을 업데이트 합니다.<br><br>**응답에 대해**: 원본 서버에서 유효성을 검사 하지 않고 후속 요청에 대 한 응답을 사용할 수 있어야 합니다. |
| [저장소 아니요](https://tools.ietf.org/html/rfc7234#section-5.2.1.5) | **요청에서**: 캐시 요청을 저장 하지 않아야 합니다.<br><br>**응답에 대해**: 캐시 응답의 모든 부분을 저장 하지 않아야 합니다. |

캐시의 역할을 하는 다른 캐시 헤더는 다음 표에 표시 됩니다.

| Header                                                     | 함수 |
| ---------------------------------------------------------- | -------- |
| [보존 기간](https://tools.ietf.org/html/rfc7234#section-5.1)     | 예상 응답 이후의 초 단위 시간의 양 생성 되었거나 원본 서버에서 성공적으로 유효성을 검사 합니다. |
| [만료](https://tools.ietf.org/html/rfc7234#section-5.3) | 날짜/시간 응답 초과가 부실 코드가 됩니다. |
| [Pragma](https://tools.ietf.org/html/rfc7234#section-5.4)  | HTTP/1.0와의 호환성 설정에 대 한 캐시 하는 이전 버전과 대 한 존재 `no-cache` 동작 합니다. 경우는 `Cache-Control` 헤더가 있으면는 `Pragma` 헤더는 무시 됩니다. |
| [변경](https://tools.ietf.org/html/rfc7231#section-7.1.4)  | 지정 된 캐시 된 응답 해야 보내지 않도록 하지 않는 한 모든의 `Vary` 헤더 필드에 캐시 된 응답의 원래 요청 하 고 새 요청이 모두 일치 합니다. |

## <a name="http-based-caching-respects-request-cache-control-directives"></a>캐싱 측면 HTTP 기반 요청 캐시 제어 지시문

[캐시 컨트롤 헤더에 대 한 HTTP 1.1 캐싱 사양이](https://tools.ietf.org/html/rfc7234#section-5.2) (를) 처리할 유효한 캐시 필요 `Cache-Control` 클라이언트에서 보낸 헤더입니다. 클라이언트가 사용 하 여 요청을 할 수는 `no-cache` 헤더 값과 강제로 서버에 대 한 모든 요청에 대 한 새 응답을 생성 합니다.

클라이언트를 구분 하지 않고 항상 `Cache-Control` HTTP 캐싱의 목표를 고려 하는 경우 요청 헤더는 것이 좋습니다. 공식 사양에서 캐싱 클라이언트, 프록시, 및 서버 네트워크를 통해 요청을 만족의 대기 시간 및 네트워크 오버 헤드를 줄이기 위해 위한 것입니다. 원본 서버의 부하를 제어 하는 방법 반드시 합니다.

사용 하는 경우이 캐싱 동작을 통해 현재 개발자 컨트롤이 없는 [응답 캐싱 미들웨어](xref:performance/caching/middleware) 미들웨어 사양 캐싱 공식 준수 때문에 있습니다. [미들웨어 향상 될](https://github.com/aspnet/ResponseCaching/issues/96) 는 요청을 무시할 미들웨어 구성 허용 `Cache-Control` 된 캐시 된 응답을 제공 하도록 결정할 때 헤더입니다. 이 제공 하면 기회 서버에서 보다 효율적으로 제어할 부하를 미들웨어를 사용 합니다.

## <a name="other-caching-technology-in-aspnet-core"></a>ASP.NET Core의 기타 캐싱 기술

### <a name="in-memory-caching"></a>메모리 내 캐싱

메모리 내 캐싱 서버 메모리를 사용 하 여 캐시 된 데이터를 저장 합니다. 이러한 유형의 캐싱은 단일 서버 또는 여러 서버를 사용 하 여에 적합 한 *고정 세션*합니다. 고정 세션은 클라이언트에서 요청 처리를 위해 동일한 서버에 라우팅되 항상 의미 합니다.

자세한 내용은 참조 [ASP.NET Core의 메모리 내 캐싱 소개](xref:performance/caching/memory)합니다.

### <a name="distributed-cache"></a>분산된 캐시

분산된 캐시를 사용 하 여 응용 프로그램 클라우드 또는 서버 팜에서 호스트 될 때 메모리에 데이터를 저장 합니다. 캐시 요청을 처리 하는 서버 간에 공유 됩니다. 클라이언트는 그룹의 모든 서버에서 처리 하는 요청을 제출할 수 및 클라이언트에 대 한 캐시 된 데이터를 사용할 수 있습니다. ASP.NET Core에서는 SQL Server 및 분산 Redis 캐시를 제공합니다.

자세한 내용은 참조 [분산된 캐시 작업](xref:performance/caching/distributed)합니다.

### <a name="cache-tag-helper"></a>캐시 태그 도우미

캐시 태그 도우미와 MVC 뷰 또는 Razor 페이지 콘텐츠를 캐시할 수 있습니다. 캐시 태그 도우미에서는 데이터를 저장할 메모리 내 캐시를 사용 합니다.

자세한 내용은 참조 [캐시 태그 도우미 ASP.NET Core mvc에서](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper)합니다.

### <a name="distributed-cache-tag-helper"></a>분산된 캐시 태그 도우미

분산 캐시 태그 도우미와 MVC 뷰 또는 분산된 클라우드 또는 웹 팜 시나리오에 대 한 Razor 페이지 콘텐츠를 캐시할 수 있습니다. 분산 캐시 태그 도우미를 사용 하 여 SQL Server 또는 Redis 데이터를 저장 합니다.

자세한 내용은 참조 [분산 캐시 태그 도우미](xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper)합니다.

## <a name="responsecache-attribute"></a>ResponseCache 특성

`ResponseCacheAttribute` 응답 캐시에서 적절 한 헤더를 설정 하는 데 필요한 매개 변수를 지정 합니다. 참조 [ResponseCacheAttribute](/aspnet/core/api/microsoft.aspnetcore.mvc.responsecacheattribute) 에 대 한 설명은 매개 변수입니다.

> [!WARNING]
> 인증 된 클라이언트에 대 한 정보를 포함 하는 콘텐츠에 대 한 캐싱을 사용 하지 않도록 설정 합니다. 사용자의 id 또는 사용자의 로그인 여부에 따라 변경 되지 않는 콘텐츠에 대 한 캐싱만 설정 해야 합니다.

`VaryByQueryKeys string[]`(ASP.NET Core 1.1 이상 필요):으로 설정 하면 응답 캐싱 미들웨어 저장된 응답이 변경 쿼리 키의 지정 된 목록 값에 의해 합니다. 응답의 캐싱 미들웨어를 설정 하려면 설정 해야 합니다는 `VaryByQueryKeys` 속성; 그렇지 않으면 런타임 예외가 throw 됩니다. 에 대 한 해당 HTTP 헤더가 없으면는 `VaryByQueryKeys` 속성입니다. 이 속성은 응답 캐싱 미들웨어가 처리 하는 HTTP 기능. 캐시 된 응답을 제공 하도록 미들웨어에 대 한 쿼리 문자열 및 쿼리 문자열 값을 이전 요청을 일치 해야 합니다. 요청 및 결과 다음 표에 표시 된 순서를 예로 들 수 있습니다.

| 요청                          | 결과                   |
| -------------------------------- | ------------------------ |
| `http://example.com?key1=value1` | 서버에서 반환 된     |
| `http://example.com?key1=value1` | 미들웨어에서 반환 |
| `http://example.com?key1=value2` | 서버에서 반환 된     |

첫 번째 요청이 서버에서 반환 하 고 미들웨어에 캐시 합니다. 이전 요청과 일치 하는 쿼리 문자열 때문에 두 번째 요청 미들웨어에서 반환 됩니다. 세 번째 요청에에서 없는 경우 미들웨어 캐시에서 쿼리 문자열 값인 이전 요청이 일치 하지 않으므로 

`ResponseCacheAttribute` 는 구성 하 고 만드는 데 사용 됩니다 (통해 `IFilterFactory`)는 `ResponseCacheFilter`합니다. `ResponseCacheFilter` 적절 한 HTTP 헤더 및 응답의 기능을 업데이트 하는 작업을 수행 합니다. 필터:

* 에 대 한 모든 기존 헤더를 제거 `Vary`, `Cache-Control`, 및 `Pragma`합니다. 
* 에 설정 된 속성에 따라 적절 한 헤더를 작성은 `ResponseCacheAttribute`합니다. 
* 업데이트 하는 경우에 캐싱 HTTP 기능 응답 `VaryByQueryKeys` 설정 됩니다.

### <a name="vary"></a>변경

이 헤더가 작성만 되 고 `VaryByHeader` 속성을 설정 합니다. 로 설정 되 고 `Vary` 속성의 값입니다. 다음 샘플에서는 `VaryByHeader` 속성:

[!code-csharp[Main](response/sample/Controllers/HomeController.cs?name=snippet_VaryByHeader&highlight=1)]

브라우저의 네트워크 도구를 사용 하 여 응답 헤더를 볼 수 있습니다. 다음 이미지에서 출력 가장자리 f12 키를 보여 줍니다.는 **네트워크** 때 탭는 `About2` 동작 메서드가 새로 고쳐집니다.

![About2 동작 메서드가 호출 될 때 네트워크 탭에서 F12 출력 가장자리](response/_static/vary.png)

### <a name="nostore-and-locationnone"></a>NoStore 및 Location.None

`NoStore`대부분의 다른 속성을 재정의합니다. 이 속성이로 설정 된 경우 `true`, `Cache-Control` 헤더가로 설정 되 `no-store`합니다. 경우 `Location` 로 설정 된 `None`:

* `Cache-Control`이 `no-store,no-cache`로 설정됩니다.
* `Pragma`이 `no-cache`로 설정됩니다.

경우 `NoStore` 은 `false` 및 `Location` 은 `None`, `Cache-Control` 및 `Pragma` 로 설정 `no-cache`합니다.

일반적으로 설정 `NoStore` 를 `true` 오류 페이지에 있습니다. 예:

[!code-csharp[Main](response/sample/Controllers/HomeController.cs?name=snippet1&highlight=1)]

그 결과 다음과 같은 헤더가 같습니다.

```
Cache-Control: no-store,no-cache
Pragma: no-cache
```

### <a name="location-and-duration"></a>위치 및 기간

캐싱을 사용 하도록 설정 하려면 `Duration` 양수 값으로 설정 되어 있어야 하 고 `Location` 납입이 되어야 `Any` (기본값) 또는 `Client`합니다. 이 경우에 `Cache-Control` 헤더 뒤 위치 값으로 설정 됩니다는 `max-age` 응답 합니다.

> [!NOTE]
> `Location`옵션의 `Any` 및 `Client` 번역할 `Cache-Control` 의 헤더 값 `public` 및 `private`각각. 앞에서 설명한 대로 설정 `Location` 를 `None` 두 설정 `Cache-Control` 및 `Pragma` 헤더를 `no-cache`합니다.

다음을 설정 하 여 생성 된 헤더를 보여 주는 예제는 `Duration` 기본 들어오거나 `Location` 값:

[!code-csharp[Main](response/sample/Controllers/HomeController.cs?name=snippet_duration&highlight=1)]

다음 헤더를 생성합니다.

```
Cache-Control: public,max-age=60
```

### <a name="cache-profiles"></a>캐시 프로필

복제 하는 대신 `ResponseCache` 에 MVC를 설정할 때 옵션으로 많은 컨트롤러 동작 특성에 캐시 프로필에서 설정을 구성할 수 있습니다는 `ConfigureServices` 메서드에서 `Startup`합니다. 참조 된 캐시 프로필에서 찾은 값에서 기본값으로 사용 되는 `ResponseCache` 특성 및 특성에 지정 된 모든 속성에 의해 무시 됩니다.

캐시 프로필을 설정 합니다.

[!code-csharp[Main](response/sample/Startup.cs?name=snippet1)] 

캐시 프로필을 참조 합니다.

[!code-csharp[Main](response/sample/Controllers/HomeController.cs?name=snippet_controller&highlight=1,4)]

`ResponseCache` 모두 작업 (메서드) 및 컨트롤러 (클래스)에 특성을 적용할 수 있습니다. 메서드 수준 특성 클래스 수준 특성에 지정 된 설정을 무시 합니다.

위의 예제에서는 클래스 수준 특성 메서드 수준의 특성 기간이 60 초로 설정 캐시 프로필을 참조 하는 동안 30 초의 지속 시간을 지정 합니다.

결과 헤더:

```
Cache-Control: public,max-age=60
```

## <a name="additional-resources"></a>추가 리소스

* [사양에서 HTTP에서 캐싱](https://tools.ietf.org/html/rfc7234#section-3)
* [캐시 제어](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9)
* [응답 캐싱 미들웨어](xref:performance/caching/middleware)
