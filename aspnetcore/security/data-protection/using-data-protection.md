---
title: "데이터 보호 Api를 사용 하 여 시작 | Microsoft 문서"
author: rick-anderson
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 39b7a73c-29d4-4137-b311-49529adcf3cb
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/using-data-protection
translationtype: Machine Translation
ms.sourcegitcommit: 010b730d2716f9f536fef889bc2f767afb648ef4
ms.openlocfilehash: c3dab9552ae4507587653a1ffd106c4a940ec041
ms.lasthandoff: 03/23/2017

---
# <a name="getting-started-with-the-data-protection-apis"></a>데이터 보호 Api를 시작 하기

<a name=security-data-protection-getting-started></a>

가장 간단한 보호 데이터에는 다음 단계로 구성 됩니다.

1. 데이터 보호 공급자에서 데이터 보호 기능을 만듭니다.

2. 보호 하려는 데이터와 함께 Protect 메서드를 호출 합니다.

3. 일반 텍스트를 다시 설정 하려는 데이터와 보호 해제 메서드를 호출 합니다.

ASP.NET SignalR 등 대부분의 프레임 워크는 이미 데이터 보호 시스템을 구성 하 고 종속성 주입을 통해 액세스 하는 서비스 컨테이너에 추가 합니다. 다음 예제에서는 종속성 주입을 위한 서비스 컨테이너를 구성 하 고 데이터 보호 스택 등록, DI 통해 데이터 보호 공급자를 받는, 보호기를 만들고 데이터를 보호 한 후 보호 해제

[!code-csharp[주](../../security/data-protection/using-data-protection/samples/protectunprotect.cs?highlight=26,34,35,36,37,38,39,40)]

보호기를 만들 때 하나 이상의 제공 해야 [목적 문자열](consumer-apis/purpose-strings.md)합니다. 예를 들어 "green"의 목적은 문자열을 사용 하 여 만든 보호기는 수 없습니다 "자주색" 용도 가진 보호기에서 제공 하는 데이터를 보호할 수, 목적은 문자열 소비자 간에 격리를 제공 합니다.

>[!TIP]
> IDataProtectionProvider 및 IDataProtector의 인스턴스는 스레드로부터 안전 여러 호출자입니다. 구성 요소는 IDataProtector CreateProtector 호출 하 여에 대 한 참조를 가져온 후이 참조 하는 데 사용할 보호와 보호 해제를 여러 번 호출 뿐입니다.
>
>보호 된 페이로드를 확인 하거나 해독할 수 없는 경우 보호 해제에 대 한 호출에서 CryptographicException를 throw 합니다. 일부 구성 요소 오류를 무시 하고자 하는 동안 작업을 보호 해제 인증 쿠키를 읽고 구성 요소 수이 오류를 처리 하 고 없는 쿠키를 전혀 것 처럼 요청을 처리 보다는 완전 한 요청이 실패 합니다. 이 동작을 방지 하는 구성 요소는 모든 예외를 무시 하는 대신 CryptographicException를 특별히 catch 해야 합니다.

