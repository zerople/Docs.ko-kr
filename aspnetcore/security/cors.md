---
title: "(CORS) 요청 크로스-원본 사용 | Microsoft 문서"
author: rick-anderson
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: f9d95e88-4d7e-4d0c-a8e1-47de1128d505
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/cors
translationtype: Machine Translation
ms.sourcegitcommit: 010b730d2716f9f536fef889bc2f767afb648ef4
ms.openlocfilehash: 80fb7022e0e92ea308a022c5a3c6369a6b198e3b
ms.lasthandoff: 03/23/2017

---
# <a name="enabling-cross-origin-requests-cors"></a>크로스-원본 요청 (CORS)를 사용 하도록 설정

여 [Mike Wasson](https://github.com/mikewasson) 및 [Shayne 보이 어](https://twitter.com/spboyer)

브라우저 보안 다른 도메인에 대 한 AJAX 요청에서 웹 페이지를 방지 합니다. 이 제한은 라고는 *동일 원본 정책*, 있으며 악의적인 사이트를 다른 사이트에서 중요 한 데이터를 읽을 수 없도록 합니다. 그러나 수 하려는 경우가 웹 앱에 크로스-원본 요청을 다른 사이트를 사용 합니다.

[교차 원본 자원 공유](http://www.w3.org/TR/cors/) (CORS)는 동일 원본 정책을 완화 하도록 서버를 허용 하는 W3C 표준입니다. CORS를 사용 하는 서버는 다른 사용자를 거부 하는 동안 일부 크로스-원본 요청을 명시적으로 허용할 수 있습니다. 예: CORS는 더욱 안전 하 고 이전 기술을 보다 융통성이 뛰어납니다 [JSONP](http://en.wikipedia.org/wiki/JSONP)합니다. 이 항목에서는 ASP.NET 핵심 응용 프로그램에서 CORS를 사용 하는 방법을 보여 줍니다.

## <a name="what-is-same-origin"></a>"같은 출처" 이란?

두 개의 Url 동일 원본이 있으면 동일한 구성표, 호스트 및 포트를 갖습니다. ([RFC 6454](http://tools.ietf.org/html/rfc6454))

이러한 두 개의 Url을 동일한 원본을 사용할 수 있습니다.

* `http://example.com/foo.html`

* `http://example.com/bar.html`

이러한 Url 이전 보다 다른 원본을 다음과 같이&2; 가지

* `http://example.net`-다른 도메인

* `http://example.com:9000/foo.html`-다른 포트

* `https://example.com/foo.html`-다른 구성표

* `http://www.example.com/foo.html`-다른 하위 도메인

> [!NOTE]
> Internet Explorer의 원본임을 비교할 때 포트를 고려 하지 않습니다.

## <a name="setting-up-cors"></a>CORS 설정

응용 프로그램 추가 대 한 CORS 설정에 `Microsoft.AspNetCore.Cors` 프로젝트에 패키지 합니다.

Startup.cs에 CORS 서비스를 추가 합니다.

[!code-csharp[주](cors/sample/src/CorsExamples/CorsExample1/Startup.cs?name=snippet_addcors)]

## <a name="enabling-cors-with-middleware"></a>미들웨어와 CORS 사용

전체 응용 프로그램에 대 한 CORS를 사용 하 여 요청 파이프라인 CORS 미들웨어 추가 사용 하도록 설정 하는 `UseCors` 확장 메서드. 참고는 CORS 미들웨어 앞에 야 정의 된 끝점 (예: 크로스-원본 요청을 지원 하 고 앱에서. 호출 하기 전에 `UseMvc`).

사용 하 여 CORS 미들웨어를 추가 하는 경우 크로스-원본 정책을 지정할 수는 `CorsPolicyBuilder` 클래스입니다. 이렇게 하는 데는 두 가지 방법이 있습니다. 첫 번째 람다를 사용 하 여 UseCors를 호출 하는 것입니다.

[!code-csharp[주](cors/sample/src/CorsExamples/CorsExample1/Startup.cs?highlight=11,12&range=22-38)]

람다는 CorsPolicyBuilder 개체를 사용합니다. 이 항목의 뒷부분에 나오는 구성 옵션을 모두 설명 합니다. 이 예제에서는 정책을 통해 크로스-원본 요청을 `http://example.com` 하 고 다른 원본에 없습니다.

메서드 호출을 연결할 수 있도록 CorsPolicyBuilder에 fluent API를 참고:

[!code-csharp[주](../security/cors/sample/src/CorsExamples/CorsExample3/Startup.cs?highlight=3&range=29-32)]

두 번째 방법은 하나 이상의 명명 된 CORS 정책 정의 다음 런타임에 정책 이름으로 선택 하는 것입니다.

[!code-csharp[주](cors/sample/src/CorsExamples/CorsExample2/Startup.cs?name=snippet_begin)]

이 예제에서는 "AllowSpecificOrigin" 라는 CORS 정책을 추가 합니다. 정책을 선택 하려면 이름을 UseCors에 전달 합니다.

## <a name="enabling-cors-in-mvc"></a>MVC에서 CORS 설정

또는 특정 CORS 작업당 컨트롤러당, 또는 모든 컨트롤러에 대해 전역적으로 적용 하려면 MVC를 사용할 수 있습니다. CORS를 사용 하도록 MVC를 사용 하는 경우 동일한 CORS 서비스 사용 되지만 CORS 미들웨어 않습니다.

### <a name="per-action"></a>작업 마다

특정 작업에 대 한 CORS 정책을 지정 하려면 추가 `[EnableCors]` 동작 하는 특성입니다. 정책 이름을 지정 합니다.

[!code-csharp[주](cors/sample/src/CorsExamples/CorsMVC/Controllers/HomeController.cs?range=10-17)]

### <a name="per-controller"></a>컨트롤러당

특정 컨트롤러에 대 한 CORS 정책을 지정 하려면 추가 `[EnableCors]` 특성을 컨트롤러 클래스입니다. 정책 이름을 지정 합니다.

[!code-csharp[주](cors/sample/src/CorsExamples/CorsMVC/Controllers/HomeController.cs?range=10-12)]

### <a name="globally"></a>전역적으로

수 있으므로 추가 하 여 모든 컨트롤러에 대 한 CORS을 전체적으로 사용할 수 있습니다는 `CorsAuthorizationFilterFactory` 전역 필터 컬렉션에 필터:

[!code-csharp[주](cors/sample/src/CorsExamples/CorsMVC/Startup2.cs?name=snippet_configureservices)]

우선 순위는: 컨트롤러를 글로벌 동작 합니다. 작업 수준 정책 컨트롤러 수준 정책 보다 우선 하며 컨트롤러 수준의 정책을 글로벌 정책 보다 우선 합니다.

### <a name="disable-cors"></a>CORS를 사용 하지 않도록 설정

CORS 컨트롤러나 동작에 대 한를 사용 하지는 `[DisableCors]` 특성입니다.

[!code-csharp[주](cors/sample/src/CorsExamples/CorsMVC/Controllers/HomeController.cs?range=19-23)]

## <a name="cors-policy-options"></a>CORS 정책 옵션

이 섹션에서는 CORS 정책에서 설정할 수 있는 다양 한 옵션을 설명 합니다.

* [허용 되 설정](#set-the-allowed-origins)

* [허용 된 HTTP 메서드 설정](#set-the-allowed-http-methods)

* [허용 된 요청 헤더를 설정 합니다.](#set-the-allowed-request-headers)

* [노출 된 응답 헤더를 설정 합니다.](#set-the-exposed-response-headers)

* [크로스-원본 요청에 자격 증명](#credentials-in-cross-origin-requests)

* [실행 전 만료 시간 설정](#set-the-preflight-expiration-time)

일부 옵션에 대 한 읽기 유용할 수 [작동 하는 방법을 CORS](#how-cors-works) 첫 번째입니다.

### <a name="set-the-allowed-origins"></a>허용 되 설정

하나 이상의 특정 원본 수 있도록 합니다.

[!code-csharp[주](cors/sample/src/CorsExamples/CorsExample4/Startup.cs?range=18-22)]

모든 원본 수 있도록 합니다.

[!code-csharp[주](cors/sample/src/CorsExamples/CorsExample4/Startup.cs??range=27-31)]

모든 원본에서 요청을 허용 하기 전에 신중히 고려해 야 합니다. 말 그대로 모든 웹 사이트에 응용 프로그램에 AJAX 호출을 만들 수 있음을 의미 합니다.

### <a name="set-the-allowed-http-methods"></a>허용 된 HTTP 메서드 설정

모든 HTTP 메서드에 수 있도록 합니다.

[!code-csharp[주](cors/sample/src/CorsExamples/CorsExample4/Startup.cs?range=44-49)]

사전 요청 및 액세스 제어-허용-방법 헤더에 영향을 줍니다.

### <a name="set-the-allowed-request-headers"></a>허용 된 요청 헤더를 설정 합니다.

CORS 실행 전 요청은 응용 프로그램에 의해 설정 된 HTTP 헤더를 나열 하는 제어 요청 헤더 액세스 헤더가 포함 될 수 있습니다 (의 소위 "요청 헤더 author").

특정 헤더를 허용 목록:

[!code-csharp[주](cors/sample/src/CorsExamples/CorsExample4/Startup.cs?range=53-58)]

수 있도록 모든 author 요청 헤더:

[!code-csharp[주](cors/sample/src/CorsExamples/CorsExample4/Startup.cs?range=62-67)]

브라우저에서 제어 요청 헤더 액세스를 설정 하는 방법을 완전히 일치 하지 않습니다. 이외의 다른 헤더 이외의 값으로 설정 하면 "*"를 포함 해야 적어도 "accept", "콘텐츠 형식", "출처"와 지원 하 고 모든 사용자 지정 헤더입니다.

### <a name="set-the-exposed-response-headers"></a>노출 된 응답 헤더를 설정 합니다.

기본적으로 브라우저 노출 하지 않습니다 모든 응용 프로그램에 대 한 응답 헤더. (참조 [http://www.w3.org/TR/cors/#simple-response-header](http://www.w3.org/TR/cors/#simple-response-header).) 기본적으로 사용할 수 있는 응답 헤더에는

* 캐시 제어

* Content-language

* 콘텐츠 형식

* 만료

* 마지막 수정

* Pragma

이러한 호출 하는 CORS 사양 *간단한 응답 헤더*합니다. 다른 헤더를 사용할 수 있도록 응용 프로그램:

[!code-csharp[주](cors/sample/src/CorsExamples/CorsExample4/Startup.cs?range=71-76)]

### <a name="credentials-in-cross-origin-requests"></a>크로스-원본 요청에 자격 증명

자격 증명에는 CORS 요청에 특별 한 처리가 필요 합니다. 기본적으로 브라우저 크로스-원본 요청에 자격 증명을 보내지 않습니다. 자격 증명 쿠키 뿐만 아니라 HTTP 인증 스키마를 포함합니다. 크로스-원본 요청에 자격 증명을 보내려면 클라이언트를 true로 XMLHttpRequest.withCredentials를 설정 해야 합니다.

XMLHttpRequest를 직접 사용:

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'http://www.example.com/api/test');
xhr.withCredentials = true;
```

JQuery에:

```jQuery
$.ajax({
  type: 'get',
  url: 'http://www.example.com/home',
  xhrFields: {
    withCredentials: true
}
```

또한 서버는 자격 증명을 허용 해야 합니다. 크로스-원본 자격 증명 수 있도록 합니다.

[!code-csharp[주](cors/sample/src/CorsExamples/CorsExample4/Startup.cs?range=80-85)]

이제 HTTP 응답에는 서버 크로스-원본 요청에 대 한 자격 증명을 허용 브라우저에 지시 하는 액세스 제어-허용-자격 증명 헤더가 포함 됩니다.

브라우저에서 자격 증명을 보내는 응답에는 유효한 액세스-제어--자격 증명 허용 헤더가 없는 경우, 브라우저 응용 프로그램에 대 한 응답을 노출 되지는 않습니다 하 고 AJAX 요청이 실패 합니다.

크로스-원본 자격 증명을 허용 하는 방법에 대 한 신중 하 게 때문일 것, 다른 도메인에서 웹 사이트 사용자가 인식 하지 않고 사용자 대신에 응용 프로그램에 로그인 한 사용자의 자격 증명을 보낼 수 있습니다. CORS 사양도 상태에 해당 설정을 원본 "*" (모든 원본) 액세스-제어--자격 증명 허용 헤더가 있으면 올바르지 않습니다.

### <a name="set-the-preflight-expiration-time"></a>실행 전 만료 시간 설정

액세스 제어-최대 기간 헤더 실행 전 요청에 대 한 응답을 캐시할 수 시간 지정 합니다. 이 헤더를 설정 합니다.

[!code-csharp[주](cors/sample/src/CorsExamples/CorsExample4/Startup.cs?range=89-94)]

<a name=cors-how-cors-works></a>

## <a name="how-cors-works"></a>CORS의 작동 원리

이 섹션에서는 CORS 요청은 HTTP 메시지의 수준에서 수행 되는 작업을 설명 합니다. 구성할 수 있도록 CORS 작동 방식을 이해 해야는 CORS 정책 올바르게 고 예상 대로 작동 하지 문제를 해결 합니다.

CORS 사양을 크로스-원본 요청을 사용 하도록 설정 하는 몇 가지 새로운 HTTP 헤더를 소개 합니다. 크로스-원본 요청;에 대 한 자동으로 이러한 헤더는 브라우저에서 CORS를 지 원하는 경우 설정 모든 JavaScript 코드에 특별 한 작업을 수행할 필요가 없습니다.

크로스-원본 요청 예를 들면 다음과 같습니다. "출처" 헤더는 요청을 수행 하는 사이트의 도메인을 제공 합니다.

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

서버 요청을 허용 하는 경우 액세스 제어-허용-원본 헤더를 설정 합니다. 이 헤더의 값 또는 원본 헤더와 일치는 와일드 카드 값 "*", 모든 원본 허용 되는 의미 합니다.:

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

응답에 대 한 액세스 제어-허용-원본 헤더 포함 되어 있지 않으면, AJAX 요청이 실패 합니다. 특히 브라우저 요청을 허용 하지 않습니다. 서버 성공적인 응답을 반환 하는 경우에 브라우저 해도 응답 클라이언트 응용 프로그램에서 사용할 수 있습니다.

### <a name="preflight-requests"></a>실행 전 요청

일부 CORS 요청에 대 한 브라우저에서 리소스에 대 한 실제 요청을 전송 하기 전에 "실행 전 요청" 라는 추가 요청을 보냅니다. 다음 조건이 충족 하는 경우 브라우저 실행 전 요청을 건너뛸 수 있습니다.

* 요청 메서드가 GET, HEAD, 또는 POST는 및

* 응용 프로그램, Accept-language, 콘텐츠 Accept-language, 이외의 모든 요청 헤더를 설정 하지 않습니다 콘텐츠 형식 또는 마지막-이벤트-ID 및

* Content-type 헤더 (경우 설정) 다음 중 하나입니다.

  * application/x-www-form-urlencoded

  * 다중 파트/양식 데이터

  * 텍스트/일반

요청 헤더에 대 한 규칙 setRequestHeader XMLHttpRequest 개체에서 호출 하 여 응용 프로그램을 설정 하는 헤더에 적용 됩니다. (CORS 사양을 이러한 작성자 요청 "헤더"를 호출합니다.) 사용자 에이전트, 호스트 또는 콘텐츠 길이 같은 브라우저, 설정할 수 있는 헤더에 규칙이 적용 되지 않습니다.

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

사전 요청 HTTP OPTIONS 메서드를 사용합니다. 두 가지 특별 한 헤더를 포함합니다.

* 액세스-제어-요청-방법: HTTP 메서드를 실제 요청에 사용 됩니다.

* 컨트롤-요청-헤더 액세스: 응용 프로그램이 실제 요청에서 설정 하는 요청 헤더의 목록. (다시이 포함 되지 않습니다 브라우저 설정 하는 헤더.)

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

허용 되는 방법을 나열 하는 액세스 제어-허용-방법 헤더 및 필요에 따라 허용 되는 헤더를 나열 하는 액세스 제어-허용-헤더만 헤더가 응답에 포함 되어 있습니다. 실행 전 요청이 성공 하면 앞에서 설명한 대로 브라우저 실제 요청을 보냅니다.

