---
title: "크로스-원본 요청 (CORS)를 사용 하도록 설정"
author: rick-anderson
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 05/17/2017
ms.topic: article
ms.assetid: f9d95e88-4d7e-4d0c-a8e1-47de1128d505
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/cors
ms.openlocfilehash: 44936c6db8a003a61091ec9a7fd3be5786d89b5b
ms.sourcegitcommit: 8cafdd1dd409d5070d227100ba0e094c779ac47b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2017
---
# <a name="enabling-cross-origin-requests-cors"></a>크로스-원본 요청 (CORS)를 사용 하도록 설정

여 [Mike Wasson](https://github.com/mikewasson), [Shayne 보이 어](https://twitter.com/spboyer), 및 [Tom Dykstra](https://github.com/tdykstra)

브라우저 보안 다른 도메인에 대 한 AJAX 요청에서 웹 페이지를 방지 합니다. 이 제한은 라고는 *동일 원본 정책*, 악성 사이트를 다른 사이트에서 중요 한 데이터를 읽을 수 없도록 합니다. 하지만, 경우에 따라 다른 사이트 web API 대 크로스-원본 요청을 만들 수 있도록 필요할 수 있습니다.

[교차 원본 자원 공유](http://www.w3.org/TR/cors/) (CORS)는 동일 원본 정책을 완화 하도록 서버를 허용 하는 W3C 표준입니다. CORS를 사용 하는 서버는 다른 사용자를 거부 하는 동안 일부 크로스-원본 요청을 명시적으로 허용할 수 있습니다. 과 같은 CORS를 안전 하 고 이전 기술을 보다 좀 더 융통적 [JSONP](http://en.wikipedia.org/wiki/JSONP)합니다. 이 항목에서는 ASP.NET Core 응용 프로그램에서 CORS를 사용 하도록 설정 하는 방법을 보여 줍니다.

## <a name="what-is-same-origin"></a>"같은 출처" 이란?

두 Url에는 동일한 구성표, 호스트 및 포트 있는 경우 동일한 원본이 있어야 합니다. ([RFC 6454](http://tools.ietf.org/html/rfc6454))

이러한 두 Url에는 동일한 원본이 있어야 합니다.

* `http://example.com/foo.html`

* `http://example.com/bar.html`

이러한 Url 있는 두 이전 보다 다른 출처:

* `http://example.net`-다른 도메인

* `http://www.example.com/foo.html`-다른 하위 도메인

* `https://example.com/foo.html`-다른 구성표

* `http://example.com:9000/foo.html`-다른 포트

> [!NOTE]
> Internet Explorer origin을 비교할 때 포트를 고려 하지 않습니다.

## <a name="setting-up-cors"></a>CORS 설정

응용 프로그램에 대 한 CORS 추가를 설정 하는 `Microsoft.AspNetCore.Cors` 프로젝트에 패키지 합니다.

Startup.cs에 CORS 서비스를 추가 합니다.

[!code-csharp[Main](cors/sample/CorsExample1/Startup.cs?name=snippet_addcors)]

## <a name="enabling-cors-with-middleware"></a>CORS 미들웨어와 사용

사용 하려면 전체 응용 프로그램에 대 한 CORS 사용 하 여 요청 파이프라인에 CORS 미들웨어를 추가 `UseCors` 확장 메서드. 참고는 CORS 미들웨어 앞에 야 정의 된 끝점 (예: 교차 원본 요청을 지원 하 고 응용 프로그램에서. 호출 하기 전에 `UseMvc`).

사용 하 여 CORS 미들웨어를 추가 하는 경우 크로스-원본 정책을 지정할 수 있습니다는 `CorsPolicyBuilder` 클래스입니다. 이렇게 하는 데는 두 가지 방법이 있습니다. 첫 번째 람다를 사용 하 여 UseCors 호출입니다.

[!code-csharp[Main](cors/sample/CorsExample1/Startup.cs?highlight=11,12&range=22-38)]

**참고:** 후행 슬래시가 없는 URL을 지정 해야 합니다 (`/`). URL로 종료 하는 경우 `/`가 반환 됩니다 `false` 헤더가 없으면 반환 됩니다.

람다는는 `CorsPolicyBuilder` 개체입니다. 목록을 찾을 수 있습니다는 [구성 옵션](#cors-policy-options) 이 항목의 뒷부분에 나오는 합니다. 이 예제에서는 정책을 통해 크로스-원본 요청을 `http://example.com` 및 다른 원본이 없습니다.

메서드 호출을 체인화할 수 있도록 CorsPolicyBuilder fluent API에 있는지 참고:

[!code-csharp[Main](../security/cors/sample/CorsExample3/Startup.cs?highlight=3&range=29-32)]

하나 이상의 명명 된 CORS 정책을 정의 하 고 런타임 시 이름별 정책을 선택 하는 두 번째 방법이입니다.

[!code-csharp[Main](cors/sample/CorsExample2/Startup.cs?name=snippet_begin)]

이 예제에서는 "AllowSpecificOrigin" 라는 CORS 정책을 추가 합니다. 정책을 선택 하려면에 이름을 전달 `UseCors`합니다.

## <a name="enabling-cors-in-mvc"></a>MVC에 CORS 사용

또는 MVC를 사용 하 여 특정 CORS 작업당: 컨트롤러 당 또는 모든 컨트롤러에 대해 전역으로 적용 합니다. CORS를 사용 하도록 MVC를 사용 하는 경우 동일한 CORS 서비스는 사용 되지 않지만 CORS 미들웨어 않습니다.

### <a name="per-action"></a>작업 마다

특정 작업에 대 한 CORS 정책을 지정 하려면 추가 `[EnableCors]` 특성 작업을 합니다. 정책 이름을 지정 합니다.

[!code-csharp[Main](cors/sample/CorsMVC/Controllers/ValuesController.cs?name=EnableOnAction)]

### <a name="per-controller"></a>컨트롤러당

특정 컨트롤러에 대 한 CORS 정책을 지정 하려면 추가 `[EnableCors]` 특성을 컨트롤러 클래스입니다. 정책 이름을 지정 합니다.

[!code-csharp[Main](cors/sample/CorsMVC/Controllers/ValuesController.cs?name=EnableOnController)]

### <a name="globally"></a>전역으로

추가 하 여 위에 모든 컨트롤러에 대해 CORS를 전체적으로 사용할 수 있습니다는 `CorsAuthorizationFilterFactory` 필터를 전역 필터 컬렉션:

[!code-csharp[Main](cors/sample/CorsMVC/Startup2.cs?name=snippet_configureservices)]

우선 순위는: 작업, 컨트롤러, 전역 합니다. 작업 수준 정책 컨트롤러 수준 정책 보다 우선 적용 하 고 컨트롤러 수준 정책 글로벌 정책 보다 우선 적용 됩니다.

### <a name="disable-cors"></a>CORS를 사용 하지 않도록 설정

컨트롤러 또는 동작에 대 한 CORS를 해제 하려면 사용 하 여는 `[DisableCors]` 특성입니다.

[!code-csharp[Main](cors/sample/CorsMVC/Controllers/ValuesController.cs?name=DisableOnAction)]

## <a name="cors-policy-options"></a>CORS 정책 옵션

이 섹션에서는 CORS 정책에서 설정할 수 있는 다양 한 옵션을 설명 합니다.

* [허용 되는 원본을 설정 합니다.](#set-the-allowed-origins)

* [허용 된 HTTP 메서드 설정](#set-the-allowed-http-methods)

* [허용 된 요청 헤더 설정](#set-the-allowed-request-headers)

* [노출 된 응답 헤더 설정](#set-the-exposed-response-headers)

* [크로스-원본 요청에 자격 증명](#credentials-in-cross-origin-requests)

* [실행 전 만료 시간 설정](#set-the-preflight-expiration-time)

일부 옵션에 대 한 읽기 유용할 수 있습니다 [작동 하는 방법을 CORS](#how-cors-works) 첫 번째입니다.

### <a name="set-the-allowed-origins"></a>허용 되는 원본을 설정 합니다.

하나 이상의 특정 origin을 허용 합니다.

[!code-csharp[Main](cors/sample/CorsExample4/Startup.cs?range=19-23)]

모든 원본을 허용 합니다.

[!code-csharp[Main](cors/sample/CorsExample4/Startup.cs??range=27-31)]

모든 원본에서 요청을 허용 하기 전에 신중히 고려해 야 합니다. 모든 웹 사이트 API에 대 한 AJAX 호출을 만들 수 있음을 의미 합니다.

### <a name="set-the-allowed-http-methods"></a>허용 된 HTTP 메서드 설정

모든 HTTP 메서드를 허용 합니다.

[!code-csharp[Main](cors/sample/CorsExample4/Startup.cs?range=44-49)]

전 요청 및 액세스 제어-허용-방법 헤더에 영향을 줍니다.

### <a name="set-the-allowed-request-headers"></a>허용 된 요청 헤더 설정

CORS 실행 전 요청에는 응용 프로그램에 의해 설정 된 HTTP 헤더를 나열 하는 액세스 제어-요청 헤더 헤더가 포함 될 수 있습니다 (이라는 "요청 헤더 author").

특정 헤더를 허용 목록:

[!code-csharp[Main](cors/sample/CorsExample4/Startup.cs?range=53-58)]

허용 하려면 요청 헤더를 작성 모든:

[!code-csharp[Main](cors/sample/CorsExample4/Startup.cs?range=62-67)]

브라우저에서 액세스 제어-요청 헤더를 설정 하는 방법을 완전히 일치 하지 않습니다. 아닌 다른 헤더를 값으로 설정 하면 "*"를 포함 해야 이상 "동의", "콘텐츠 형식은" 및 "출처" + 지원 하 고 모든 사용자 지정 헤더입니다.

### <a name="set-the-exposed-response-headers"></a>노출 된 응답 헤더 설정

기본적으로 브라우저 노출 하지 않습니다 모든 응용 프로그램에 대 한 응답 헤더입니다. (참조 [http://www.w3.org/TR/cors/#simple-response-header](http://www.w3.org/TR/cors/#simple-response-header).) 기본적으로 사용할 수 있는 응답 헤더에는

* 캐시 제어

* Content-language

* 콘텐츠-유형

* 만료

* 마지막 수정

* Pragma

이러한 호출 하는 CORS 사양 *간단한 응답 헤더*합니다. 다른 헤더를 사용할 수 있도록 응용 프로그램:

[!code-csharp[Main](cors/sample/CorsExample4/Startup.cs?range=71-76)]

### <a name="credentials-in-cross-origin-requests"></a>크로스-원본 요청에 자격 증명

자격 증명에는 CORS 요청에 대 한 특별 한 처리가 필요 합니다. 기본적으로 브라우저 크로스-원본 요청에 자격 증명을 보내지 않습니다. 자격 증명 쿠키 뿐만 아니라 HTTP 인증 스키마를 포함 합니다. 클라이언트는 크로스-원본 요청에 자격 증명을 보내려면 true로 XMLHttpRequest.withCredentials 설정 해야 합니다.

직접 XMLHttpRequest를 사용 하 여:

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'http://www.example.com/api/test');
xhr.withCredentials = true;
```

jQuery:

```jQuery
$.ajax({
  type: 'get',
  url: 'http://www.example.com/home',
  xhrFields: {
    withCredentials: true
}
```

또한 서버 자격 증명을 허용 해야 합니다. 크로스-원본 자격 증명을 허용 합니다.

[!code-csharp[Main](cors/sample/CorsExample4/Startup.cs?range=80-85)]

이제 HTTP 응답에는 브라우저에 알려 서버 크로스-원본 요청에 대 한 자격 증명을 허용 하는 액세스-컨트롤--자격 증명 허용 헤더가 포함 됩니다.

브라우저에서 자격 증명을 보내는 응답에는 올바른 액세스-컨트롤--자격 증명 허용 헤더 표시 되지만 브라우저 응용 프로그램에 대 한 응답을 노출 되지는 않습니다 및 AJAX 요청이 실패 합니다.

크로스-원본 자격 증명을 허용 하는 방법에 대 한 때문에 신중을 기해야 다른 도메인에서 웹 사이트 사용자가 인식 하지 않고 사용자를 대신 하 여, 앱에 로그인 한 사용자의 자격 증명 보낼 수 있음을 의미 합니다. CORS 사양도 상태 해당 설정을 원본이 "*" (모든 원본을) 액세스-컨트롤--자격 증명 허용 헤더가 있으면 올바르지 않습니다.

### <a name="set-the-preflight-expiration-time"></a>실행 전 만료 시간 설정

액세스 제어-최대 기간 헤더 실행 전 요청에 대 한 응답을 캐시할 수 시간을 지정 합니다. 다음을 설정 하려면이 헤더

[!code-csharp[Main](cors/sample/CorsExample4/Startup.cs?range=89-94)]

<a name=cors-how-cors-works></a>

## <a name="how-cors-works"></a>CORS가 작동 하는 방법

이 섹션에서는 CORS 요청은 HTTP 메시지 수준에서 수행 되는 작업을 설명 합니다. 작동 방식을 이해 CORS CORS 정책을 올바르게 구성 하 고 예상 대로 작동 하지 않는 경우 문제를 해결할 수 있도록 하는 것이 유용 합니다.

CORS 사양 교차 원본 요청을 사용 하도록 설정 하는 몇 가지 새 HTTP 헤더를 소개 합니다. 크로스-원본 요청;에 대 한 자동으로 이러한 헤더를 설정 브라우저 CORS를 지 원하는 경우 JavaScript 코드에 특수 한 어떤 작업도 수행할 필요가 없습니다.

크로스-원본 요청의 예를 들면 다음과 같습니다. "원본" 헤더는 요청을 수행 하는 사이트의 도메인을 제공 합니다.

```
GET http://myservice.azurewebsites.net/api/test HTTP/1.1
Referer: http://myclient.azurewebsites.net/
Accept: */*
Accept-Language: en-US
Origin: http://myclient.azurewebsites.net
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.2; WOW64; Trident/6.0)
Host: myservice.azurewebsites.net
```

서버 요청을 허용 하는 경우 액세스 제어-허용-원본 헤더를 설정 합니다. 이 헤더의 값 원본 헤더 또는 와일드 카드 값 "*", 모든 원본을 허용 되는 의미 합니다.:

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Type: text/plain; charset=utf-8
Access-Control-Allow-Origin: http://myclient.azurewebsites.net
Date: Wed, 20 May 2015 06:27:30 GMT
Content-Length: 12

Test message
```

응답에 대 한 액세스 제어-허용-원본 헤더 포함 되어 있지 않으면, AJAX 요청이 실패 합니다. 특히, 브라우저 요청을 허용 하지 않습니다. 성공적인 응답을 반환 하는 서버, 경우에 브라우저 수 없습니다 응답 클라이언트 응용 프로그램에 사용할 수 있습니다.

### <a name="preflight-requests"></a>실행 전 요청

일부 CORS 요청에 대 한 브라우저의 리소스에 대 한 실제 요청을 보내기 전에 "실행 전 요청" 라는 추가 요청을 보냅니다. 다음 조건에 해당할 경우 브라우저에서 실행 전 요청을 건너뛸 수 있습니다.:

* 요청 메서드는 GET, HEAD 또는 POST 및

* 응용 프로그램 Accept, Accept-language, Content-language 이외의 모든 요청 헤더를 설정 하지 않는 콘텐츠 형식 또는 마지막-이벤트-ID 및

* 콘텐츠 형식 헤더 (하는 경우 설정) 다음 중 하나입니다.

  * application/x-www-form-urlencoded

  * multipart/폼 데이터

  * 텍스트/일반

요청 헤더에 대 한 규칙 setRequestHeader XMLHttpRequest 개체에서 호출 하 여 응용 프로그램을 설정 하는 헤더에 적용 됩니다. (이러한 작성자 요청 "헤더" CORS 사양을 호출합니다.) 규칙은 브라우저 등을 설정할 수, 사용자 에이전트, 호스트 또는 Content-length 헤더에 적용 되지 않습니다.

실행 전 요청의 예는 다음과 같습니다.

```
OPTIONS http://myservice.azurewebsites.net/api/test HTTP/1.1
Accept: */*
Origin: http://myclient.azurewebsites.net
Access-Control-Request-Method: PUT
Access-Control-Request-Headers: accept, x-my-custom-header
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.2; WOW64; Trident/6.0)
Host: myservice.azurewebsites.net
Content-Length: 0
```

전 요청의 HTTP OPTIONS 메서드를 사용합니다. 두 개의 특수 헤더를 포함합니다.

* 액세스-컨트롤-요청-방법: 실제 요청에 사용할 HTTP 메서드.

* 액세스 제어-요청-헤더만: 응용 프로그램은 실제 요청에 설정 하는 요청 헤더의 목록. (다시이 헤더가 포함 되지 않습니다 브라우저 설정입니다.)

다음은 서버에서 요청을 허용 하는 것으로 가정 하는 예제 응답이입니다.

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 0
Access-Control-Allow-Origin: http://myclient.azurewebsites.net
Access-Control-Allow-Headers: x-my-custom-header
Access-Control-Allow-Methods: PUT
Date: Wed, 20 May 2015 06:33:22 GMT
```

허용 되는 메서드를 나열 하는 액세스 제어-허용-방법 헤더 및 필요에 따라 허용된 된 헤더를 나열 하는 액세스 제어-허용-헤더만 헤더가 응답에 포함 되어 있습니다. 실행 전 요청이 성공 하면 앞에서 설명한 대로 브라우저 실제 요청을 보냅니다.
