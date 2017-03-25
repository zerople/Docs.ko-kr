---
title: "ASP.NET 핵심 응용 프로그램에서 SSL을 적용 합니다. | Microsoft 문서"
author: rick-anderson
description: "웹 응용 프로그램에서 SSL을 요구 하는 방법과 IIS Express SSL을 사용 하도록 설정 하는 방법을 보여 줍니다."
keywords: ASP.NET Core, SSL, HTTPS, RequireHttpsAttribute, IIS Express
ms.author: riande
manager: wpickett
ms.date: 03/19/2017
ms.topic: article
ms.assetid: 4694e563-e91a-4ecd-b7ed-00b3f1eee2b5
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/enforcing-ssl
translationtype: Machine Translation
ms.sourcegitcommit: 6d3767417ae4b0ed7bc4e0ddd20180473ac0f408
ms.openlocfilehash: 9bc4adca1900863db3d6fe57d815f8cd9efba641
ms.lasthandoff: 03/23/2017

---
# <a name="enforcing-ssl-in-an-aspnet-core-app"></a>ASP.NET 핵심 응용 프로그램에서 SSL을 적용합니다.

이 문서에서는 방법:

- 모든 요청 (HTTPS 요청에만 해당)에 대해 SSL이 필요 합니다.
- 모든 HTTP 요청이 HTTPS로 리디렉션됩니다.
- IIS express SSL/HTTPS를 사용 하도록 설정 합니다.

## <a name="require-ssl"></a>SSL이 필요

[RequireHttpsAttribute](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.requirehttpsattribute) ssl을 사용 하는 데 사용 됩니다. 컨트롤러 또는이 특성을 가진 메서드를 데코레이팅 할 수 있습니다 또는 아래와 같이 전체적으로 적용할 수 있습니다.

다음 코드를 추가 `ConfigureServices` 에서 `Startup`:

[!code-csharp[주](authentication/accconfirm/sample/WebApp1/Startup.cs?name=snippet2&highlight=4-)]

위의 강조 표시 된 코드를 실행 하려면 모든 요청에 사용 하 여 `HTTPS`, 따라서 HTTP 요청은 무시 됩니다. 다음 강조 표시 된 코드 HTTPS로 모든 HTTP 요청을 리디렉션합니다.

[!code-csharp[주](authentication/accconfirm/sample/WebApp1/Startup.cs?name=snippet_AddRedirectToHttps&highlight=6-)]

참조 [URL 다시 쓰기 미들웨어](xref:fundamentals/url-rewriting) 에 대 한 자세한 내용은 합니다.

HTTPS를 전역적으로 요구 (`options.Filters.Add(new RequireHttpsAttribute());`)이 보안 모범 사례입니다. 적용 된 `[RequireHttps]` 컨트롤러에는 단점이 하지 않을 보장 새 컨트롤러 추가 프로젝트에이 보호 발생 합니다.

## <a name="set-up-iis-express-for-sslhttps"></a>IIS Express SSL/HTTPS에 대 한 설정

   * 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭 하 고 선택 **속성**합니다.
   * 왼쪽된 창에서 선택 **디버그**합니다.
   * 확인 **SSL 사용**
   * SSL URL을 복사 하 고 붙여 넣습니다는 **앱 URL**

![디버그 탭의 웹 응용 프로그램 속성](enforcing-ssl/_static/ssl.png)
