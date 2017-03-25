---
title: "요구 사항 처리기에서 종속성 주입 | Microsoft 문서"
author: rick-anderson
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 5fb6625c-173a-4feb-8380-73c9844dc23c
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/authorization/dependencyinjection
translationtype: Machine Translation
ms.sourcegitcommit: 010b730d2716f9f536fef889bc2f767afb648ef4
ms.openlocfilehash: 8e60e3cad392bdda0aa6cbde4f763a0c77ac9904
ms.lasthandoff: 03/23/2017

---
# <a name="dependency-injection-in-requirement-handlers"></a>요구 사항 처리기에서 종속성 주입

<a name=security-authorization-di></a>

[권한 부여 처리기를 등록 해야](policies.md#security-authorization-policies-based-handler-registration) 구성 하는 동안 서비스 컬렉션에 (사용 하 여 [종속성 주입](../../fundamentals/dependency-injection.md#fundamentals-dependency-injection)).

권한 부여 처리기의 내부 계산 하려는 규칙의 리포지토리를 있다고 가정 하 고 해당 저장소 서비스 컬렉션에 등록 된 합니다.  권한 부여를 해결 하 고 생성자 주입 하 합니다.

예를 들어 다음과 같이 ASP를 사용 하려고 합니다. 네트워크 인프라를 주입 하는 것의 로깅 `ILoggerFactory` 처리기에 있습니다. 이러한 처리기와 같습니다.

```csharp
public class LoggingAuthorizationHandler : AuthorizationHandler<MyRequirement>
   {
       ILogger _logger;

       public LoggingAuthorizationHandler(ILoggerFactory loggerFactory)
       {
           _logger = loggerFactory.CreateLogger(this.GetType().FullName);
       }

       protected override Task HandleRequirementAsync(AuthorizationHandlerContext context, MyRequirement requirement)
       {
           _logger.LogInformation("Inside my handler");
           // Check if the requirement is fulfilled.
           return Task.CompletedTask;
       }
   }
   ```

처리기를 등록 합니다 `services.AddSingleton()`:

```csharp
services.AddSingleton<IAuthorizationHandler, LoggingAuthorizationHandler>();
   ```

응용 프로그램이 시작 될 때 만들 처리기의 인스턴스 및 등록 된 삽입 DI `ILoggerFactory` 생성자에 있습니다.

> [!NOTE]
> Entity Framework를 사용 하는 처리기 singleton로 등록할 수 없습니다.

