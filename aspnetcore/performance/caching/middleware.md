---
title: "응답 캐싱은 미들웨어 | Microsoft 문서"
author: guardrex
description: "구성 및 ASP.NET 핵심 응용 프로그램에서 응답 캐싱을 미들웨어를 사용 합니다."
keywords: "ASP.NET Core 응답 캐싱, 캐싱, ResponseCache, ResponseCaching, 캐시 제어, VaryByQueryKeys, 미들웨어"
ms.author: riande
manager: wpickett
ms.date: 03/13/2017
ms.topic: article
ms.assetid: f9267eab-2762-42ac-1638-4a25d2c9d67c
ms.prod: asp.net-core
uid: performance/caching/middleware
translationtype: Machine Translation
ms.sourcegitcommit: ec31052983921540dc13052848c77bb88edf3b19
ms.openlocfilehash: cbee8c10da657d21a2096b9a4c868b706ef70aeb
ms.lasthandoff: 03/23/2017

---
# <a name="response-caching-middleware"></a>응답의 캐싱 미들웨어

여 [Luke Latham](https://github.com/GuardRex) 및 [John Luo](https://github.com/JunTaoLuo)

[샘플 코드 보기 또는 다운로드](https://github.com/aspnet/Docs/tree/master/aspnetcore/performance/caching/middleware/sample)

이 문서 ASP.NET 핵심 응용 프로그램에서 응답 캐싱을 미들웨어를 구성 하는 방법에 자세히 설명 합니다. 미들웨어 응답은 캐시 가능한 경우, 저장소 응답 및 캐시에서 사용 되며 응답을 결정 합니다. HTTP 캐시에 대 한 소개와 `ResponseCache` 특성을 참조 하십시오 [응답 캐싱은](response.md)합니다.

## <a name="package"></a>패키지
미들웨어를 프로젝트에 포함 하려면 추가에 대 한 참조는 [ `Microsoft.AspNetCore.ResponseCaching` ](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCaching/) 패키지 합니다. 미들웨어는.NET Framework 4.5.1 또는 1.3 이상을.NET 표준에 따라 다릅니다. 이 기능은 이상 ASP.NET 핵심 1.1.0을 대상으로 하는 응용 프로그램에 사용할 수 있습니다.

## <a name="configuration"></a>구성
`ConfgureServices`, 미들웨어 서비스 컬렉션에 추가 합니다.

[!code-csharp[주](middleware/sample/Startup.cs?name=snippet1)]

요청을 처리할 때 미들웨어를 사용 하 여 응용 프로그램을 구성 합니다. 추가 하는 샘플 응용 프로그램을 [ `Cache-Control` ](https://tools.ietf.org/html/rfc7234#section-5.2) 헤더를 응답 메시지를 최대 10 초 동안 캐시 가능한 응답을 캐시 합니다. 샘플도 보냅니다는 [ `Vary` ](https://tools.ietf.org/html/rfc7231#section-7.1.4) 경우에만 응답을 제공 하는 캐시를 구성 하는 헤더는 [ `Accept-Encoding` ](https://tools.ietf.org/html/rfc7231#section-5.3.4) 원래 요청에서의 후속 요청 헤더와 일치 합니다.

[!code-csharp[주](middleware/sample/Startup.cs?name=snippet2)]

응답 캐싱은 미들웨어는만 200 (OK) 서버 응답을 캐시합니다. 모든 다른 응답을 포함 하 여 [오류 페이지](xref:fundamentals/error-handling), 미들웨어에서 무시 됩니다.

## <a name="options"></a>옵션
미들웨어 응답 캐시 제어에 대 한 두 가지 옵션이 표시 됩니다.

옵션 | 기본값
--- | ---
UseCaseSensitivePaths | <p>대/소문자 구분 경로에 응답이 캐시 되지 것입니다 결정 합니다.</p><p>기본값은 `false`입니다.</p>
MaximumBodySize | <p>(바이트)는 응답 본문에 가장 큰 캐시 가능한 크기입니다.</p>기본값은 `64 * 1024 * 1024` [64MB (67,108,864 바이트)]입니다.</p>

다음 예제에서는 응답을 캐시에 대 한 응답을 저장 하는 대/소문자 구분 경로 사용 하 여 1, 024 바이트 보다 작거나 미들웨어 구성 `/page1` 및 `/Page1` 별도로 합니다.

```csharp
services.AddResponseCaching(options =>
{
    options.UseCaseSensitivePaths = true;
    options.MaximumBodySize = 1024;
});
```

## <a name="varybyquerykeys-feature"></a>VaryByQueryKeys 기능
MVC를 사용 하는 경우는 `ResponseCache` 특성 응답 캐시에 대 한 적절 한 헤더를 설정 하는 데 필요한 매개 변수를 지정 합니다. 유일한 매개 변수는 `ResponseCache` 엄격 하 게 미들웨어를 필요로 하는 특성은 `VaryByQueryKeys`, 실제 HTTP 헤더에 일치 하지 않습니다. 자세한 내용은 참조 [ResponseCache 특성](response.md#responsecache-attribute)합니다.

MVC를 사용 하지 않을 때 응답 캐싱을 다르게 지정할 수 있습니다는 `VaryByQueryKeys` 기능을 사용 하는 `ResponseCachingFeature` 에서 직접는 `IFeatureCollection` 의 `HttpContext`합니다.

```csharp
var responseCachingFeature = context.HttpContext.Features.Get<IResponseCachingFeature>();
if (responseCachingFeature != null)
{
    responseCachingFeature.VaryByQueryKeys = new[] { "MyKey" };
}
```

## <a name="http-headers-used-by-response-caching-middleware"></a>응답 캐싱은 미들웨어에 사용 되는 HTTP 헤더
미들웨어 여 캐시 된 응답은 HTTP 응답 헤더를 통해 구성 됩니다. 관련 헤더는 캐시에 미치는 영향을에 메모와 함께 아래 나와 있습니다.

Header | 세부 정보
--- | --- |
권한 부여 | <p>응답 헤더에 있는 경우 캐시 되지 않습니다.</p>
캐시 제어 | <p>미들웨어는만 고려 하 여 명시적으로 설정 된 응답을 캐시는 `public` 캐시 지시문입니다.</p><p>캐시는 다음 매개 변수를 제어할 수 있습니다.</p><ul><li>최대 처리 기간</li><li>최대 부실</li><li>최소-새 항목</li><li>반드시 뒤 재검증</li><li>캐시 없음</li><li>아니요-저장소</li><li>전용 if-캐시</li><li>private</li><li>public</li><li>기간</li><li>프록시 뒤 재검증</li></ul><p>자세한 내용은 참조 [RFC 7231: 요청 캐시 제어 지시문](https://tools.ietf.org/html/rfc7234#section-5.2.1)합니다.</p>
Pragma | <p>A `Pragma: no-cache` 요청의 헤더와 동일한 효과 냅니다 `Cache-Control: no-cache`합니다. 이 헤더는 관련 지시문에 의해 재정의 `Cache-Control` 머리글이 있는 경우.</p><p>HTTP/1.0과 함께 이전 버전과 호환성에 대 한 것으로 간주 합니다.</p>
Set-cookie | <p>응답 헤더에 있는 경우 캐시 되지 않습니다.</p>
다 | <p>다른 헤더에서 캐시 된 응답을 변경할 수 있습니다. 예를 들어 포함 하 여 인코딩하여 응답을 캐시할 수 있습니다는 `Vary: Accept-Encoding` 응답 헤더를 사용 하 여 요청을 캐시 합니다는 헤더 `Accept-Encoding: gzip` 및 `Accept-Encoding: text/plain` 별도로 합니다. 응답의 헤더 값으로 `*` 는 저장 되지 않습니다.</p>
만료 | <p>이 헤더에 의해 부실 간주 응답을 저장 하거나 다른 재정의 되지 않는 경우를 검색할 수는 `Cache-Control` 헤더입니다.</p>
If-none-Match | <p>전체 응답 캐시에서 제공 되는 값이 `*` 및 `ETag` 응답은 제공 된 값이 하나라도 일치 하지 않습니다. 그렇지 않으면 304 (수정 되지 않음) 응답은 처리 됩니다.</p>
If-수정-이후 | <p>하는 경우는 `If-None-Match` 헤더가 없는, 전체 응답의 캐시 된 응답 날짜에 제공 된 값 보다 최신인 경우 캐시에서 처리 됩니다. 그렇지 않으면 304 (수정 되지 않음) 응답은 처리 됩니다.</p>
날짜 | <p>캐시에서 서비스를 제공 하는 경우는 `Date` 원래 응답에 제공 되지 않은 경우 미들웨어 여 헤더가 설정 되어 있습니다.</p>
콘텐츠 길이 | <p>캐시에서 서비스를 제공 하는 경우는 `Content-Length` 원래 응답에 제공 되지 않은 경우 미들웨어 여 헤더가 설정 되어 있습니다.</p>
보존 기간 | <p>`Age` 원래 응답에 보낸 헤더는 무시 됩니다. 미들웨어는 캐시 된 응답을 처리할 때 새 값을 계산 합니다.</p>

## <a name="troubleshooting"></a>문제 해결
캐싱 동작 하는 것은 예상 그다지, 응답은 캐시 가능한 수 있고 들어오는 헤더는 요청 및 응답의 나가는 헤더를 검사 하 여 캐시에서 제공 되 고 있는지를 확인 합니다. 응답이 캐시 됩니다 조건 아래에 나와 있습니다.

로깅 사용을 디버깅할 때 유용 합니다. 예를 들어 미들웨어 수 없는 이유는 응답은 캐시 되지 않고 및 캐시에서 검색 된 여부를 기록 합니다. 참조 [ASP.NET 핵심 로그인](xref:fundamentals/logging) 방법은 응용 프로그램에서 로깅을 사용 하도록 설정 합니다.

테스트 및 캐싱 동작 문제를 해결 하는 경우 브라우저는 바람직하지 않은 방법으로 캐시에 영향을 주는 요청 헤더를 설정할 수 있습니다. 예를 들어 브라우저 설정 수는 `Cache-Control` 헤더를 `no-cache` 페이지를 새로 고칩니다. 와 같은 도구를 사용 하 여 브라우저를 사용 하지 않고 [Fiddler](http://www.telerik.com/fiddler), [Firebug](http://getfirebug.com/), 또는 [Postman](https://www.getpostman.com/), 명시적으로 요청 헤더를 설정할 수 있습니다.

### <a name="conditions-for-caching"></a>캐시에 대 한 조건
* 요청 200 (정상) 응답이 서버에서 발생 해야 합니다.
* 요청 메서드가 GET 또는 HEAD 이어야 합니다.
* 같은 정적 파일 미들웨어 터미널 미들웨어 응답 캐싱을 미들웨어 전에 응답을 처리 해야 합니다.
* 권한 부여 헤더 없어야 합니다.
* `Cache-Control`헤더 매개 변수는 유효 해야 하 고 응답을 표시 되어야 합니다 `public` 표시 되어 있지 `private`합니다.
* `Pragma: no-cache` 헤더/값 하지 않아야 하는 경우는 `Cache-Control` 헤더도 나타나지 않습니다.는 `Cache-Control` 헤더 재정의 `Pragma` 머리글이 있는 경우.
* `Set-Cookie` 헤더 하지 않아야 합니다.
* `Vary`헤더 매개 변수 유효 해야 하며 해당 `*`합니다.
* `Content-Length` 헤더 값 (경우 설정)는 응답 본문의 크기와 일치 해야 합니다.
* `HttpSendFileFeature` 사용 되지 않습니다.
* 응답에 지정 된 대로 오래 되지 않아야는 `Expires` 헤더 및 `max-age` 및 `s-maxage` 지시문을 캐시 합니다.
* 응답 버퍼링 성공 하 고 응답의 총 길이가 구성된 된 한계 보다 작습니다.
* 응답에 따라 캐시 가능 해야 합니다.는 [RFC 7234](https://tools.ietf.org/html/rfc7234) 사양입니다. 예를 들어는 `no-store` 지시문 요청 또는 응답 헤더 필드에 없어야 합니다. 참조 *섹션 3: 캐시에서 응답을 저장* 대 한 자세한 내용은 RFC 문서입니다.

>[!NOTE]
> 교차 사이트 요청 위조 CSRF () 공격을 방지 하기 위해 보안 토큰을 생성 하기 위한 Antiforgery 시스템이 설정의 `Cache-Control` 및 `Pragma` 헤더를 `no-cache` 응답이 캐시 되지 않을 수 있도록 합니다.

## <a name="additional-resources"></a>추가 리소스

* [응용 프로그램 시작](xref:fundamentals/startup)
* [미들웨어](xref:fundamentals/middleware)

