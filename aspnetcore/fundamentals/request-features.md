---
title: "ASP.NET 핵심에서 기능을 요청 하려면 | Microsoft 문서"
author: ardalis
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: d1fbd23c-2ff9-4216-b908-0201ff3afb7c
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/request-features
translationtype: Machine Translation
ms.sourcegitcommit: 0e62aa19779f7e4ed559279a4727e0faa616732b
ms.openlocfilehash: c92dadd58deac61e9400146c8879f668477b2fd3
ms.lasthandoff: 03/23/2017

---
# <a name="request-features-in-aspnet-core"></a>ASP.NET 핵심에서 기능을 요청

[Steve Smith](http://ardalis.com)

웹 서버 기능을 HTTP 요청 및 응답 처리 방식과 관련 된 인터페이스에 분리 되었습니다. 이러한 인터페이스를 만들고 응용 프로그램의 호스팅 파이프라인 수정할 서버 구현 및 미들웨어에서 사용 됩니다.

## <a name="feature-interfaces"></a>기능 인터페이스

HTTP 기능 인터페이스의 수를 정의 하는 ASP.NET 핵심 `Microsoft.AspNetCore.Http.Features` 를 하는 데 서버에서 지 원하는 기능을 식별 합니다. 다음과 같은 기능 인터페이스 요청을 처리 하 고 응답을 반환 합니다.

`IHttpRequestFeature`
프로토콜, 경로, 쿼리 문자열, 헤더 및 본문을 포함 하 여 HTTP 요청을의 구조를 정의 합니다.

`IHttpResponseFeature`
상태 코드, 헤더 및 응답의 본문을 포함 하 여 HTTP 응답의 구조를 정의 합니다.

`IHttpAuthenticationFeature`
에 따라 사용자를 식별 하기 위한 지원을 정의 `ClaimsPrincipal` 인증 처리기를 지정 하 고 있습니다.

`IHttpUpgradeFeature`
에 대 한 지원을 정의 [HTTP 업그레이드](https://tools.ietf.org/html/rfc2616.html#section-14.42), 서버 프로토콜 전환 하려는 경우 사용 하려는 클라이언트 프로토콜을 추가 하는 것을 지정할 수 있는 허용 합니다.

`IHttpBufferingFeature`
요청 및/또는 응답의 버퍼링을 사용 하지 않도록 설정 하기 위한 메서드를 정의 합니다.

`IHttpConnectionFeature`
로컬 및 원격 주소 및 포트에 대 한 속성을 정의합니다.

`IHttpRequestLifetimeFeature`
연결을 중단 하거나 요청을 종료 된 경우에 갑자기 중단 등 클라이언트 연결 끊기 기준으로 검색에 대 한 지원을 정의 합니다.

`IHttpSendFileFeature`
파일을 비동기적으로 보내기 위한 메서드를 정의 합니다.

`IHttpWebSocketFeature`
웹 소켓을 지원 하기 위한 API를 정의 합니다.

`IHttpRequestIdentifierFeature`
요청을 고유 하 게 식별 하는 구현 될 수 있는 속성을 추가 합니다.

`ISessionFeature`
정의 `ISessionFactory` 및 `ISession` 사용자 세션을 지원 하기 위한 추상화입니다.

`ITlsConnectionFeature`
클라이언트 인증서를 검색 하기 위한 API를 정의 합니다.

`ITlsTokenBindingFeature`
TLS 토큰 바인딩 매개 변수를 사용 하기 위한 메서드를 정의 합니다.

> [!NOTE]
> `ISessionFeature`서버 기능이 아니지만 의해 구현 되는 `SessionMiddleware` (참조 [응용 프로그램 상태 관리](app-state.md)).

## <a name="feature-collections"></a>컬렉션 기능

`Features` 속성 `HttpContext` 가져오고 현재 요청에 대 한 사용 가능한 HTTP 기능을 설정 하기 위한 인터페이스를 제공 합니다. 요청 컨텍스트 내 에서도 변경할 수 있는 기능 컬렉션 이므로 미들웨어 데 사용할 수는 컬렉션을 수정 하 고 추가 기능에 대 한 지원을 추가 합니다.

## <a name="middleware-and-request-features"></a>미들웨어 및 요청 기능

서버를 기능 컬렉션 생성을 담당 하는 동안 미들웨어 모두이 컬렉션에 추가 하 고 사용할 수는 컬렉션에서 기능. 예를 들어는 `StaticFileMiddleware` 액세스는 `IHttpSendFileFeature` 기능입니다. 기능이 설치 된 경우, 해당 실제 경로에서 요청된 된 정적 파일을 보내려면 사용 됩니다. 그렇지 않으면 느린 대체 사용 되는 파일 전송 합니다. 사용 가능한 경우는 `IHttpSendFileFeature` 운영 체제 파일을 열고 네트워크 카드에 직접 커널 모드 복사를 수행 하 수 있습니다.

또한 미들웨어는 서버에 의해 설정 된 기능 컬렉션에 추가할 수 있습니다. 도 미들웨어, 미들웨어 서버 기능을 확장할 수 있도록 하 여 기존 기능을 대체할 수 있습니다. 컬렉션에 추가 기능은 다른 미들웨어 또는 내부 응용 프로그램 자체 요청 파이프라인의 뒷부분에 나오는 즉시 사용할 수 있습니다.

사용자 지정 서버 구현 및 특정 미들웨어 향상 된 기능을 결합 하 여 정확 하 게 응용 프로그램에 필요한 기능 집합을 생성할 수 있습니다. 이 통해 서버에서 변경 하지 않고도 추가할 기능 및 최소한의 기능만 노출 되 고, 따라서 공격을 제한 하면 누락 된 노출 영역 및 성능을 향상 시킵니다.

## <a name="summary"></a>요약

기능 인터페이스에는 지정된 된 요청에서 지원할 수 있는 특정 HTTP 기능 정의 합니다. 서버 기능, 컬렉션 및 해당 서버에서 지 원하는 기능의 초기 집합을 정의 하지만 미들웨어를 사용 하 여 이러한 기능 향상 시킬 수 있습니다.

## <a name="additional-resources"></a>추가 리소스

* [서버](servers/index.md)

* [미들웨어](middleware.md)

* [.NET (OWIN)에 대 한 공개 웹 인터페이스](owin.md)

