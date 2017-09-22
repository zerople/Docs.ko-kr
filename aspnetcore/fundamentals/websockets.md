---
title: "ASP.NET Core에서 Websocket을 지원합니다."
author: tdykstra
description: "Websocket 이란 ASP.NET Core 및 사용 하는 방법을 지원 합니다."
keywords: ASP.NET Core, WebSockets
ms.author: tdykstra
manager: wpickett
ms.date: 03/25/2017
ms.topic: article
ms.assetid: 0e0fedcd-a7b4-4479-8ae0-36eab0229d7e
ms.technology: aspnet
ms.prod: aspnet-core
uid: fundamentals/websockets
ms.openlocfilehash: 8a6b5cc8ca8ac17f0e4c5b23f20013130cd472c8
ms.sourcegitcommit: 78d28178345a0eea91556e4cd1adad98b1446db8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2017
---
# <a name="introduction-to-websockets-in-aspnet-core"></a>ASP.NET Core에서 Websocket 소개

여 [Tom Dykstra](https://github.com/tdykstra) 및 [Andrew Stanton 간호사](https://github.com/anurse)

이 문서에는 ASP.NET Core에서 Websocket을 시작 하는 방법을 설명 합니다. [WebSocket](https://wikipedia.org/wiki/WebSocket) 은 TCP 연결을 통한 영구 양방향 통신 채널을 사용 하도록 설정 하는 프로토콜입니다. 웹 응용 프로그램에서 실시간 기능이 원하는 곳을 채팅, 주식 종목, 게임 등의 응용 프로그램에 대 한 사용 됩니다.

[샘플 코드 보기 또는 다운로드](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/websockets/sample) 참조는 [다음 단계](#next-steps) 한 자세 합니다.


## <a name="prerequisites"></a>필수 구성 요소

* ASP.NET Core 1.1 (1.0에 실행 되지 않습니다)
* ASP.NET Core에서 실행 되는 모든 운영 체제:
  
  * Windows 7 / Windows Server 2008 이상
  * Linux
  * MacOS

* **예외**: iis에서 Windows에서 앱 실행 되거나 WebListener를 사용 해야 하는 경우:

  * Windows 8 / Windows Server 2012 이상
  * IIS 8 / IIS 8 Express
  * IIS에서 WebSocket은 사용할 수 있어야 합니다.

* 지원 되는 브라우저 http://caniuse.com/#feat=websockets를 참조 하십시오.

## <a name="when-to-use-it"></a>용도

소켓 연결으로 직접 작업 해야 할 때 Websocket을 사용 합니다. 예를 들어 실시간 게임에 대 한 가능한 최상의 성능을 할 수 있습니다.

[ASP.NET SignalR](https://docs.microsoft.com/aspnet/signalr/overview/getting-started/introduction-to-signalr) 풍부 제공 하지만 실시간 기능에 대 한 응용 프로그램 모델 ASP.NET에서는 ASP.NET Core 하지 에서만 실행 합니다. SignalR의 핵심 버전은 개발; 진행 상황을 수행 하려면 참조는 [SignalR Core 용 GitHub 리포지토리](https://github.com/aspnet/SignalR)합니다.

SignalR Core 기다려야 않으려면 이제 사용할 수 있습니다 Websocket 직접 합니다. 하지만 SignalR 제공할와 같은 기능을 개발 해야 할 수 있습니다.

* 광범위 한 대체 전송 방법으로 자동 대체를 사용 하 여 브라우저 버전을 지원 합니다.
* 자동 다시 연결은 연결을 삭제 하는 경우입니다.
* 서버에서 또는 그 반대로 메서드를 호출 하는 클라이언트에 대해 지원 합니다.
* 여러 서버에 맞게 크기 조정 지원 합니다.

## <a name="how-to-use-it"></a>사용 하는 방법

* 설치는 [Microsoft.AspNetCore.WebSockets](https://www.nuget.org/packages/Microsoft.AspNetCore.WebSockets/) 패키지 합니다.
* 미들웨어를 구성 합니다.
* WebSocket 요청을 수락 합니다.
* 메시지 보내기 및 받기.

### <a name="configure-the-middleware"></a>미들웨어를 구성 합니다.

Websocket 미들웨어에서 추가 `Configure` 의 메서드는 `Startup` 클래스입니다.

[!code-csharp[](websockets/sample/Startup.cs?name=UseWebSockets)]

다음 설정은 구성할 수 있습니다.

* `KeepAliveInterval`-프록시 연결을 유지 하도록 클라이언트에 "ping" 프레임을 보낼 빈도를 사용 하는 방법입니다.
* `ReceiveBufferSize`-데이터를 수신 하는 데 사용 되는 버퍼의 크기입니다. 고급 사용자만 자신의 데이터의 크기에 따라 성능 튜닝에이 변경할 해야 합니다.

[!code-csharp[](websockets/sample/Startup.cs?name=UseWebSocketsOptions)]

### <a name="accept-websocket-requests"></a>WebSocket 요청을 수락

요청 라이프 사이클의 뒷부분에 있는 위치 (의 뒷부분에 나오는 `Configure` 메서드 또는 예를 들어 MVC 동작에서) WebSocket 요청 인지 확인 하 고 들어오는 WebSocket 요청을 수락 합니다.

이 예제는에서 나중에 `Configure` 메서드.

[!code-csharp[](websockets/sample/Startup.cs?name=AcceptWebSocket&highlight=7)]

WebSocket 요청에 모든 URL 일 수도 있지만이 샘플 코드에 대 한 요청은 허용 `/ws`합니다.

### <a name="send-and-receive-messages"></a>메시지 보내기 및 받기

`AcceptWebSocketAsync` 메서드는 WebSocket 연결에 TCP 연결을 업그레이드 하 고 제공 된 [WebSocket](https://docs.microsoft.com/dotnet/core/api/system.net.websockets.websocket) 개체입니다. 메시지를 받거나 보내기 위해 WebSocket 개체를 사용 합니다.

WebSocket 요청을 수락 하는 앞에 표시 된 코드에서 전달 된 `WebSocket` 개체를 `Echo` 방법으로, 여기는 `Echo` 메서드. 코드는 메시지를 수신 하 고 동일한 메시지를 즉시 보냅니다. 클라이언트가 연결을 닫을 때까지 작업을 수행 하는 루프에 보관 됩니다. 

[!code-csharp[](websockets/sample/Startup.cs?name=Echo)]

WebSocket이이 루프를 시작 하기 전에 수락 하면 미들웨어 파이프라인 종료 됩니다.  소켓을 닫을 때 파이프라인을 해제 합니다. 즉,와 마찬가지로 WebSocket을 수락 하면 파이프라인에서 앞으로 이동 하 여 요청 중지 예를 들어 MVC 동작의 경우에 도달 하면 됩니다.  하지만 요청 파이프라인 백업 진행이 루프 끝나고 소켓을 닫습니다.

## <a name="next-steps"></a>다음 단계

[샘플 응용 프로그램](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/websockets/sample) 이 함께 제공 되는 문서는 간단한 에코 응용 프로그램입니다. WebSocket 연결 하는 웹 페이지가 고 서버 항목만 다시 클라이언트에 수신 된 모든 메시지. (해당 설정 있지 않은 IIS Express와 Visual Studio에서 실행) 하는 명령 프롬프트에서 실행 http://localhost:5000로 이동 합니다. 웹 페이지 왼쪽 위에 연결 상태를 보여 줍니다.

![웹 페이지의 초기 상태](websockets/_static/start.png)

선택 **연결** WebSocket 요청에 표시 된 URL을 보낼 수 있습니다.  테스트 메시지를 입력 하 고 선택 **보낼**합니다. 을 완료 한 후 선택 **닫기 소켓**합니다. **통신 로그** 섹션 열려 전송할 때마다를 보고 하 고 발생 하는 것으로 닫기 동작 합니다.

![웹 페이지의 초기 상태](websockets/_static/end.png)
