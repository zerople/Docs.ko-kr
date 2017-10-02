---
title: "ASP.NET Core의 오류 처리"
author: ardalis
description: "ASP.NET Core 응용 프로그램에서 오류를 처리 하는 방법을 알아봅니다."
keywords: "ASP.NET Core, 오류 처리, 예외 처리"
ms.author: tdykstra
manager: wpickett
ms.date: 11/30/2016
ms.topic: article
ms.assetid: 4db51023-c8a6-4119-bbbe-3917e272c260
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/error-handling
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: de2ba0ff9ad17c198c06b510ecfb49f808721bdf
ms.sourcegitcommit: 732cd2684246e49e796836596643a8d37e20c46d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2017
---
# <a name="introduction-to-error-handling-in-aspnet-core"></a>ASP.NET Core의 오류 처리 소개

여 [Steve Smith](https://ardalis.com/) 및 [Tom Dykstra](https://github.com/tdykstra/)

이 문서에서는 일반적인 appoaches ASP.NET Core 응용 프로그램의 오류 처리를 다룹니다.

[보거나 다운로드 샘플 코드](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/error-handling/sample) ([다운로드 하는 방법을](xref:tutorials/index#how-to-download-a-sample))

## <a name="the-developer-exception-page"></a>개발자 예외 페이지

예외에 대 한 자세한 정보를 표시 하는 페이지를 표시 하는 응용 프로그램을 구성 하려면 설치는 `Microsoft.AspNetCore.Diagnostics` NuGet 패키지 하 고 행을 추가 하는 [시작 클래스에서 메서드를 구성](startup.md):

[!code-csharp[Main](error-handling/sample/Startup.cs?name=snippet_DevExceptionPage&highlight=7)]

배치 `UseDeveloperExceptionPage` 와 같은 예외를 catch 하려는 모든 미들웨어 전에 `app.UseMvc`합니다.

>[!WARNING]
> 개발자 예외 페이지를 사용 하도록 설정 **응용 프로그램 개발 환경에서 실행 중인 경우에**합니다. 프로덕션 환경에서 앱을 실행할 때 자세한 예외 정보를 공개적으로 공유 하는 것이 않으려는 합니다. [환경 구성에 대 한 자세한](environments.md)합니다.

개발자 예외 페이지를 보려면 샘플 응용 프로그램 설정 된 환경으로 실행 `Development`, 추가 `?throw=true` 응용 프로그램의 기본 URL에 있습니다. 예외 및 요청에 대 한 정보로 여러 탭 페이지에 포함 됩니다. 첫 번째 탭에는 스택 추적에 포함 됩니다. 

![스택 추적](error-handling/_static/developer-exception-page.png)

다음 탭 있는 경우는 쿼리 문자열 매개 변수를 나타냅니다.

![쿼리 문자열 매개 변수](error-handling/_static/developer-exception-page-query.png)

이 요청 된 쿠키를 포함 하지 않은 있지만에 표시 되는 경우는 **쿠키** 탭 합니다. 마지막으로 탭에 전달 된 헤더를 볼 수 있습니다.

![헤더](error-handling/_static/developer-exception-page-headers.png)

## <a name="configuring-a-custom-exception-handling-page"></a>페이지를 처리 하는 사용자 지정 예외를 구성 합니다.

앱 실행 중이지 않을 때 사용 하는 예외 처리기 페이지를 구성 하는 것이 좋습니다는 `Development` 환경입니다.

[!code-csharp[Main](error-handling/sample/Startup.cs?name=snippet_DevExceptionPage&highlight=11)]

MVC 응용 프로그램의 안 함 명시적으로 데코 레이트 HTTP 메서드 특성을 사용 하 여 오류 처리기 작업 메서드 같은 `HttpGet`합니다. 명시적 동사를 사용 하 여 메서드에서 일부 요청 하지 못하도록 수 없습니다.

```csharp
[Route("/Error")]
public IActionResult Index()
{
    // Handle error here
}
```

## <a name="configuring-status-code-pages"></a>상태 코드 페이지를 구성합니다.

기본적으로 응용 프로그램에 대 한 HTTP 상태 코드 500 (내부 서버 오류) 또는 404 (찾을 수 없음)와 같은 다양 한 상태 코드 페이지를 제공 하지 않습니다. 구성할 수는 `StatusCodePagesMiddleware` 줄을 추가 하 여는 `Configure` 메서드:

```csharp
app.UseStatusCodePages();
```

기본적으로이 미들웨어 404 등의 일반적인 상태 코드에 대 한 간단 하 고 텍스트 전용 처리기를 추가합니다.

![404 페이지](error-handling/_static/default-404-status-code.png)

미들웨어는 일부의 확장 메서드를 지원합니다. 람다 식에서 바라볼, 다른 콘텐츠 형식 및 형식 문자열을 사용 합니다.

[!code-csharp[Main](error-handling/sample/Startup.cs?name=snippet_StatusCodePages)]

```csharp
app.UseStatusCodePages("text/plain", "Status code page, status code: {0}");
```

리디렉션 확장 메서드가 있습니다. 하나 302 상태 코드를 클라이언트에 보내고 하나 클라이언트에는 원래 상태 코드를 반환 하지만 또한 리디렉션 URL에 대 한 처리기를 실행 합니다.

[!code-csharp[Main](error-handling/sample/Startup.cs?name=snippet_StatusCodePagesWithRedirect)]

```csharp
app.UseStatusCodePagesWithReExecute("/error/{0}");
```

특정 요청에 대 한 상태 코드 페이지를 사용 하지 않도록 설정 해야 할 경우이 수행할 수 있습니다.

```csharp
var statusCodePagesFeature = context.Features.Get<IStatusCodePagesFeature>();
if (statusCodePagesFeature != null)
{
  statusCodePagesFeature.Enabled = false;
}
```

## <a name="exception-handling-code"></a>예외 처리 코드

예외 처리 페이지의에서 코드는 예외가 발생할 수 있습니다. 종종 완전 한 정적 콘텐츠의 구성 프로덕션 오류 페이지에 대 한는 것이 좋습니다.

또한 유의 하십시오를 응답에 대 한 헤더를 보낸 응답의 상태 코드를 변경할 수 없습니다 및 예외 페이지 또는 처리기 실행할 수 있습니다. 응답을 완료 해야 하거나 연결이 중단 합니다.

## <a name="server-exception-handling"></a>서버 예외 처리

응용 프로그램에서 논리를 처리 하는 예외 뿐 아니라는 [서버](servers/index.md) 응용 프로그램 호스팅 몇 가지 예외 처리를 수행 합니다. 헤더를 보내기 전에 된 예외를 catch 하는 서버를 하는 경우 서버 본문이 없는 500 내부 서버 오류 응답을 보냅니다. 헤더를 보낸 후에 예외를 catch 하는 서버를 서버 연결을 닫습니다. 응용 프로그램으로 처리 되지 요청은 서버에서 처리 됩니다. 서버의 예외 발생 하는 모든 예외를 처리를 처리 합니다. 구성 된 모든 사용자 지정 오류 페이지 또는 예외 처리 미들웨어 또는 필터 안 함이 동작에 영향을이 있습니다.

## <a name="startup-exception-handling"></a>시작 예외 처리

호스팅 계층에만 응용 프로그램 시작 시 수행 되는 예외를 처리할 수 있습니다. 할 수 있습니다 [호스트 오류에 대 한 응답으로 시작 하는 동안 작동 하는 방법을 구성할](hosting.md#detailed-errors) 를 사용 하 여 `captureStartupErrors` 및 `detailedErrors` 키입니다.

호스트 주소/포트 바인딩 후에 오류가 발생 하는 경우 호스팅 캡처된 시작 오류에 대 한 오류 페이지만 표시할 수 있습니다. 어떤 이유로 든 실패 하면 모든 바인딩에 호스팅 계층 dotnet 프로세스가 충돌할 중요 한 예외를 기록 하 고 없음 오류 페이지가 표시 됩니다.

## <a name="aspnet-mvc-error-handling"></a>ASP.NET MVC 오류 처리

[MVC](../mvc/index.md) 앱은 예외 필터를 구성 하 고 모델 유효성 검사를 수행 하는 등의 오류를 처리 하기 위한 몇 가지 추가 옵션이 있습니다.

### <a name="exception-filters"></a>예외 필터

전역으로 또는 MVC 응용 프로그램에서-컨트롤러 또는 동작 당 기준 예외 필터를 구성할 수 있습니다. 이러한 필터 컨트롤러 작업 또는 다른 필터를 실행 하는 동안 발생 하는 처리 되지 않은 예외를 처리 하며 그렇지 않으면 호출 되지 않습니다. 예외 필터에 대 한 자세한 [필터](../mvc/controllers/filters.md)합니다.

>[!TIP]
> 예외 필터는 MVC 동작 내에서 발생 하는 예외를 트래핑 하는 데 유용 하지만 유연 하 게 오류 미들웨어를 처리 하지 않습니다. 일반적인 경우에 대 한 미들웨어를 선호 하 고만 해야 하는 필터를 사용 하 여 오류 처리 작업을 수행할 *다르게* MVC 작업 선택에 따라 합니다.

### <a name="handling-model-state-errors"></a>처리 모델 상태 오류

[유효성 검사 모델](../mvc/models/validation.md) 호출 되는 각 컨트롤러 동작 하기 전에 발생 하 고 검사 해야 하는 작업 메서드의 `ModelState.IsValid` 하 고 적절 하 게 대처 합니다.

일부 응용 프로그램 모델 유효성 검사 오류를 처리 하기 위한 표준 규칙에는 쿼리에서 수행 하도록 선택 하는 [필터](../mvc/controllers/filters.md) 이러한 정책을 구현 하는 적절 한 위치 수 있습니다. 잘못 된 모델 상태와 작업을 수행할 때 동작 하는 방법을 테스트 해야 합니다. 자세한 내용을 알아보세요 [컨트롤러 논리를 테스트](../mvc/controllers/testing.md)합니다.



