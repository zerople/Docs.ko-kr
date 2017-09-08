---
title: "ASP.NET Core에서 기능을 요청"
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
ms.openlocfilehash: e8d04ef7df34fe1421b2c52f137511fc6baae674
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/11/2017
---
# <a name="request-features-in-aspnet-core"></a>ASP.NET Core에서 기능을 요청

으로 [Steve Smith](http://ardalis.com)

웹 HTTP 요청 및 응답에 관련 된 세부 인터페이스에 정의 된 서버 구현입니다. 이러한 인터페이스를 생성 및 호스팅 응용 프로그램의 파이프라인을 수정할 서버 구현 및 미들웨어에서 사용 됩니다.

## <a name="feature-interfaces"></a>기능 인터페이스

HTTP 기능 인터페이스의 수를 정의 하는 ASP.NET Core `Microsoft.AspNetCore.Http.Features` 는 하는 데 서버에서 지 원하는 기능을 식별 합니다. 요청을 처리 하 고 응답을 반환 하는 다음과 같은 기능 인터페이스:

`IHttpRequestFeature`프로토콜, 경로, 쿼리 문자열, 헤더 및 본문을 포함 하 여 HTTP 요청을의 구조를 정의 합니다.

`IHttpResponseFeature`상태 코드, 헤더 및 응답의 본문을 포함 하 여 HTTP 응답의 구조를 정의 합니다.

`IHttpAuthenticationFeature`에 따라 사용자를 식별 하기 위한 지원을 정의 `ClaimsPrincipal` 인증 처리기를 지정 하 고 있습니다.

`IHttpUpgradeFeature`에 대 한 지원을 정의 [HTTP 업그레이드](https://tools.ietf.org/html/rfc2616.html#section-14.42), 서버 프로토콜을 전환 하려는 경우 사용 하려는 클라이언트 프로토콜 추가 지정할 수 있도록 허용 합니다.

`IHttpBufferingFeature`요청 및/또는 응답 버퍼링을 사용 하지 않도록 설정 하기 위한 메서드를 정의 합니다.

`IHttpConnectionFeature`로컬 및 원격 주소 및 포트에 대 한 속성을 정의합니다.

`IHttpRequestLifetimeFeature`연결을 중지 하거나 검색 하는 경우 요청이 갑자기 중단 등으로가 종료 클라이언트 연결 끊기에 대 한 지원을 정의 합니다.

`IHttpSendFileFeature`비동기적으로 파일을 보내는 방법을 정의 합니다.

`IHttpWebSocketFeature`Websocket을 지원 하기 위한 API를 정의 합니다.

`IHttpRequestIdentifierFeature`요청을 고유 하 게 식별 하는 구현 될 수 있는 속성을 추가 합니다.

`ISessionFeature`정의 `ISessionFactory` 및 `ISession` 사용자 세션을 지원 하기 위한 추상화입니다.

`ITlsConnectionFeature`클라이언트 인증서를 검색 하기 위한 API를 정의 합니다.

`ITlsTokenBindingFeature`TLS 토큰 바인딩 매개 변수를 사용 하기 위한 메서드를 정의 합니다.

> [!NOTE]
> `ISessionFeature`서버 기능이 아니지만 의해 구현 되는 `SessionMiddleware` (참조 [관리 응용 프로그램 상태](app-state.md)).

## <a name="feature-collections"></a>컬렉션 기능

`Features` 속성 `HttpContext` 가져오고 현재 요청에 대 한 사용 가능한 HTTP 기능을 설정 하기 위한 인터페이스를 제공 합니다. 요청 컨텍스트 내 에서도 변경할 수 있는 기능 컬렉션 이므로 미들웨어 데 사용할 수는 컬렉션을 수정 하 고 추가 기능에 대 한 지원을 추가 합니다.

## <a name="middleware-and-request-features"></a>미들웨어 및 요청 기능

서버 기능 컬렉션을 만드는 책임이 상태인 미들웨어 수 모두이 컬렉션에 추가할 및 컬렉션에서 기능을 사용 합니다. 예를 들어는 `StaticFileMiddleware` 액세스는 `IHttpSendFileFeature` 기능입니다. 기능이 설치 된 경우, 실제 경로가에서 요청된 된 정적 파일을 보낼 수 ´ ù. 그렇지는 더 느린 대체 방법은 파일을 보내려면 사용 됩니다. 사용 가능한 경우는 `IHttpSendFileFeature` 운영 시스템이 직접 커널 모드 네트워크 카드에 복사를 수행 하 고 파일을 열 수 있습니다.

또한 미들웨어는 서버에서 설정한 기능 컬렉션에 추가할 수 있습니다. 도 서버의 기능을 보완할 미들웨어 허용 미들웨어에서 기존 기능을 바꿀 수 있습니다. 컬렉션에 추가 기능은 다른 미들웨어 또는 내부 응용 프로그램 자체 요청 파이프라인의 뒷부분에 나오는에 즉시 사용할 수 있습니다.

사용자 지정 서버 구현 및 특정 미들웨어의 향상 된 기능을 결합 함으로써 정확한 응용 프로그램에 필요한 기능 집합을 생성할 수 있습니다. 그러면 누락 된 서버에서 변경 하지 않고도 추가할 기능 및 보장 공격을 제한할 수는 최소한의 기능만 노출 되 영역 및 성능 향상을 노출 합니다.

## <a name="summary"></a>요약

기능 인터페이스를 지 원하는 지정된 된 요청 특정 HTTP 기능 정의 합니다. 서버 기능, 컬렉션 및 해당 서버에서 지 원하는 기능의 초기 집합을 정의 하지만 미들웨어를 사용 하 여 이러한 기능을 향상 시킬 수 있습니다.

## <a name="additional-resources"></a>추가 리소스

* [서버](servers/index.md)

* [미들웨어](middleware.md)

* [For.NET (OWIN) 열린 웹 인터페이스](owin.md)
