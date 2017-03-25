---
title: "소비자 Api 개요 | Microsoft 문서"
author: rick-anderson
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: f69beb9d-a519-43a8-857c-f6b01886a903
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/consumer-apis/overview
translationtype: Machine Translation
ms.sourcegitcommit: 010b730d2716f9f536fef889bc2f767afb648ef4
ms.openlocfilehash: 87b6bdb0764674442a10d5a29be9ca2cc2c4b4cb
ms.lasthandoff: 03/23/2017

---
# <a name="consumer-apis-overview"></a>소비자 Api 개요

IDataProtectionProvider 및 IDataProtector 인터페이스는 소비자는 데이터 보호 시스템을 사용 하는 기본 인터페이스입니다. Microsoft.AspNetCore.DataProtection.Abstractions 패키지에 배치 됩니다.

## <a name="idataprotectionprovider"></a>IDataProtectionProvider

공급자 인터페이스의 데이터 보호 시스템의 루트를 나타냅니다. 이 보호 하거나 데이터를 보호 해제를 직접 사용할 수 없습니다. 대신, 소비자 IDataProtectionProvider.CreateProtector(purpose), 여기서 용도 대상된 소비자 사용 사례를 설명 하는 문자열을 호출 하 여 한 IDataProtector에 대 한 참조를 가져와야 합니다. 참조 [목적 문자열](purpose-strings.md) 이 매개 변수 및 적절 한 값을 선택 하는 방법의 의도에 훨씬 더 많은 내용은 합니다.

## <a name="idataprotector"></a>IDataProtector

보호기 인터페이스는 CreateProtector를 호출 하 여 반환 하 고 보호 하 고 작업의 보호를 해제 하는 소비자가 수행 하는 데 사용할 수 있는이 인터페이스는 키를 누릅니다.

일부 데이터를 보호 하려면 데이터 Protect 메서드를 전달 합니다. Byte-> byte 변환 하는 메서드를 정의 하는 기본 인터페이스는 없지만 오버 로드 (확장 메서드로 제공) 문자열-> 문자열을 변환 합니다. 두 가지 방법으로 제공 되는 보안과 동일 합니다. 개발자는 어떤 오버 로드는 해당 사용 사례에 대 한 가장 편리 하 게 선택 해야 합니다. 오버 로드 선택 하 고, 보호에서 반환한 값에 관계 없이 메서드는 이제 보호 (서 암호화 및 변조에도 성능이 보장) 및 응용 프로그램이 신뢰할 수 없는 클라이언트에 보낼 수 있습니다.

이전에 보호 된 데이터의 보호를 해제 하려면 보호 된 데이터를 보호 해제 메서드를 전달 합니다. (Byte-개발자의 편의에 따라 및 문자열 기반 오버 로드 합니다.) 보호 된 페이로드 동일한 IDataProtector이에 대 한 보호에 대 한 이전 호출에서 생성 되었으면, 보호 해제 메서드는 원래 보호 되지 않는 페이로드를 반환 합니다. 보호 된 페이로드 변조 되었는지 또는 다른 IDataProtector를 통해 만들어진 보호 해제 메서드는 CryptographicException을 throw 합니다.

다른 IDataProtector와 동일한 개념 목적의 개념을 다시 연결합니다. 두 IDataProtector 인스턴스는 동일한 루트 IDataProtectionProvider 있지만 IDataProtectionProvider.CreateProtector에 대 한 호출에서 다른 용도로 문자열을 통해 생성 된 경우 간주 되므로 [다른 보호기](purpose-strings.md), 페이로드는 다른 생성 된 보호를 해제할 수 하나입니다.

## <a name="consuming-these-interfaces"></a>이러한 인터페이스를 사용합니다.

DI 인식 구성 요소를 의도 한 사용량이 구성의 생성자에는 IDataProtectionProvider 매개 변수를 사용 하 고 DI 시스템 구성 요소를 인스턴스화할 때 자동으로이 서비스를 제공 하 합니다.

> [!NOTE]
> 일부 응용 프로그램 (예: 콘솔 응용 프로그램 또는 ASP.NET 4.x 응용 프로그램) DI 인식 하므로 여기 설명 하는 메커니즘을 사용할 수 없습니다 아닐 수도 있습니다. 이러한 시나리오를 참조 하세요.는 [아닌 DI 인식 시나리오](../configuration/non-di-scenarios.md) DI를 통하지 않고 IDataProtection 공급자의 인스턴스를 가져오는 방법에 대 한 자세한 내용은 문서입니다.

다음 샘플 세 가지 개념을 보여 줍니다.

1. [추가 데이터 보호 시스템](../configuration/overview.md) 서비스 컨테이너에

2. DI를 사용 하 여 한 IDataProtectionProvider의 인스턴스를 수신 하 고

3. IDataProtector는 IDataProtectionProvider 만들고 사용 하 여 보호 하 고 데이터 보호를 해제 합니다.

[!code-csharp[주](../using-data-protection/samples/protectunprotect.cs?highlight=26,34,35,36,37,38,39,40)]

패키지 Microsoft.AspNetCore.DataProtection.Abstractions 개발자의 편의 IServiceProvider.GetDataProtector 확장 메서드를 포함합니다. 모두는 IDataProtectionProvider 서비스 공급자에서 검색 하 고 IDataProtectionProvider.CreateProtector 호출을 단일 작업으로 캡슐화 합니다. 다음 샘플의 사용법을 보여 줍니다.

[!code-csharp[주](./overview/samples/getdataprotector.cs?highlight=15)]

>[!TIP]
> IDataProtectionProvider 및 IDataProtector의 인스턴스는 스레드로부터 안전 여러 호출자입니다. 것은 참조 하는 보호를 여러 번 호출 하는 데 사용할 구성 요소는 IDataProtector CreateProtector 호출 하 여에 대 한 참조를 가져온 후 및 보호 된 페이로드를 확인 하거나 해독할 수 없는 경우 보호 해제를 Unprotect.A 호출 CryptographicException를 throw 합니다. 일부 구성 요소 오류를 무시 하고자 하는 동안 작업을 보호 해제 인증 쿠키를 읽고 구성 요소 수이 오류를 처리 하 고 없는 쿠키를 전혀 것 처럼 요청을 처리 보다는 완전 한 요청이 실패 합니다. 이 동작을 방지 하는 구성 요소는 모든 예외를 무시 하는 대신 CryptographicException를 특별히 catch 해야 합니다.

