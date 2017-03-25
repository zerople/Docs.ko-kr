---
title: "ASP.NET Core 1.1의 새로운 기능 | Microsoft 문서"
author: rick-anderson
description: "ASP.NET 핵심 1.1의 새로운 기능"
keywords: ASP.NET Core, bower
ms.author: riande
manager: wpickett
ms.date: 02/14/2017
ms.topic: article
ms.assetid: 062f8353-d1bc-4e99-a821-c1d1bb162c47
ms.technology: aspnet
ms.prod: aspnet-core
uid: aspnetcore-1.1
translationtype: Machine Translation
ms.sourcegitcommit: 0ebb9b63931ccb26126740de08270eda9b1ea486
ms.openlocfilehash: c8847f22bc5d9c79b7badd13500ad7e6cfbebc4c
ms.lasthandoff: 03/23/2017

---

# <a name="whats-new-in-aspnet-core-11"></a>ASP.NET 핵심 1.1의 새로운 기능

ASP.NET Core 1.1에는 다음과 같은 새로운 기능이 포함 됩니다.

- [URL 다시 쓰기 미들웨어](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/url-rewriting)
- [응답의 캐싱 미들웨어](https://docs.microsoft.com/en-us/aspnet/core/performance/caching/middleware)
- [태그 도우미 구성 요소 보기](xref:mvc/views/view-components#invoking-a-view-component-as-a-tag-helper)
- [MVC 필터로 미들웨어](xref:mvc/controllers/filters#using-middleware-in-the-filter-pipeline)
- [쿠키 기반 TempData 공급자](xref:fundamentals/app-state#cookie-based-tempdata-provider )
- [Azure 앱 서비스 로깅 공급자](xref:fundamentals/logging#appservice)
- [Azure 키 자격 증명 모음 구성 공급자](xref:security/key-vault-configuration)
- [Azure 및 Redis 저장소의 데이터 보호 키 저장소](xref:security/data-protection/implementation/key-storage-providers#azure-and-redis)
- [Windows 용 WebListener 서버](xref:fundamentals/servers/weblistener)
- [Websocket 지원](#websockets-support)

## <a name="choosing-between-versions-10-and-11-of-aspnet-core"></a>버전 1.0 및 1.1의 ASP.NET 핵심 중에서 선택

ASP.NET Core 1.1 1.0 보다 더 많은 기능에 있습니다. 일반적으로 최신 버전을 사용 하는 것이 좋습니다.

## <a name="websockets-support"></a>Websocket 지원

새 미들웨어 구성 요소는 ASP.NET 핵심 1.1에서 Websocket 지원 합니다. 설치는 [Microsoft.AspNetCore.WebSockets](https://www.nuget.org/packages/Microsoft.AspNetCore.WebSockets/) 패키지를 찾아 코드를 추가 하는 `Configure` 의 메서드는 `Startup` 클래스:

```
app.UseWebSockets();
```

프로젝트에 액세스할 수 있는 다음는 `WebSockets` 속성에는 `HttpContext` 개체입니다.  파이프라인을 해석 하 고는 websocket 하 여 응용 프로그램으로 전달 요청와 상호 작용에 대 한 고유한 미들웨어를 작성할 수 있습니다.  다음과 같은 코드로 구성 된 파이프라인을이 미들웨어를 추가할 수 있습니다.

```
app.Use(async (context, next) =>
{
  if (context.WebSockets.IsWebSocketRequest)
  {
    var webSocket = await context.WebSockets.AcceptWebSocketAsync();
    await DoSomethingCool(context, webSocket);
  }
  else
  {
    await next();
  }
});
```

Websocket 개체에 `SendAsync` 및 `ReceiveAsync` 메서드.  샘플은 [Websocket 리포지토리](https://github.com/aspnet/WebSockets/tree/dev/samples)합니다.

## <a name="additional-information"></a>추가 정보

- [ASP.NET Core 1.1.0 릴리스 정보](https://github.com/aspnet/Home/releases/tag/1.1.0)

