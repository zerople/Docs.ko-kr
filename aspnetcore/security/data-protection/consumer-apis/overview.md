---
title: "소비자 Api 개요"
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
ms.openlocfilehash: d23a6ce50eef71f393124b9420f4ba473904d8b4
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/11/2017
---
# <a name="consumer-apis-overview"></a>소비자 Api 개요

IDataProtectionProvider 및 IDataProtector 인터페이스는 소비자는 데이터 보호 시스템을 사용 하는 기본 인터페이스입니다. Microsoft.AspNetCore.DataProtection.Abstractions 패키지에 있습니다.

## <a name="idataprotectionprovider"></a>IDataProtectionProvider

공급자 인터페이스는 데이터 보호 시스템의 루트를 나타냅니다. 해당 파일을 보호 하거나 데이터를 보호 해제를 직접 사용할 수 없습니다. 대신 소비자 IDataProtectionProvider.CreateProtector(purpose), 여기서 용도 대상된 소비자 사용 사례를 설명 하는 문자열을 호출 하 여 IDataProtector에 대 한 참조를 가져와야 합니다. 참조 [목적 문자열](purpose-strings.md) 이 매개 변수 및 적절 한 값을 선택 하는 방법의 의도에 훨씬 더 많은 정보에 대 한 합니다.

## <a name="idataprotector"></a>IDataProtector

보호기 인터페이스 CreateProtector에 대 한 호출에서 반환 되며 보호 하 고 작업 보호를 해제 하는이 인터페이스는 소비자가 수행 하는 데 사용할 수 있습니다.

일부 데이터를 보호 하려면 Protect 메서드에 데이터를 전달 합니다. 기본 인터페이스는 변환 byte byte-> 메서드를 정의 하지만 또한 오버 로드 (확장 메서드로 제공) 문자열을 변환 하는 문자열->. 두 가지 방법으로 제공 되는 보안은 동일 합니다. 개발자는 어떤 오버 로드는 해당 사용 사례에 대 한 가장 편리 하 게 선택 해야 합니다. 오버 로드를 선택한 보호에서 반환 된 값에 관계 없이 메서드는 이제 보호 (서 암호화 및 조작에도 성능이 보장) 및 응용 프로그램이 신뢰할 수 없는 클라이언트에 보낼 수 있습니다.

이전에 보호 된 일부 데이터 보호를 해제 하려면 보호 된 데이터 Unprotect 메서드를 전달 합니다. (Byte-개발자 편의 위해 기반 및 문자열 기반 오버 로드 합니다.) 보호 된 페이로드가 동일한 IDataProtector에 보호 한 이전 호출에서 생성 되었으면, Unprotect 메서드는 원래 보호 되지 않는 페이로드를 반환 합니다. 보호 된 페이로드 변조 되었거나, 다른 IDataProtector를 통해 만들어진 Unprotect 메서드 CryptographicException을 throw 합니다.

다른 IDataProtector와 동일한 개념 목적의 개념을 다시 연결합니다. 두 IDataProtector 인스턴스는 동일한 루트 IDataProtectionProvider 하지만 IDataProtectionProvider.CreateProtector에 대 한 호출에서 다른 용도로 문자열을 통해 생성 된 경우 간주 되므로 [다른 보호기](purpose-strings.md), 하나는 다른에서 생성 된 페이로드를 보호 해제 수 없습니다.

## <a name="consuming-these-interfaces"></a>이러한 인터페이스를 사용합니다.

DI 인식 구성 요소를 의도 한 사용량이 DI 시스템 구성 요소를 인스턴스화할 때 자동으로이 서비스를 제공 하 고 해당 생성자에는 IDataProtectionProvider 매개 변수를 사용 하는 구성 요소.

> [!NOTE]
> 일부 응용 프로그램 (예: 콘솔 응용 프로그램 또는 ASP.NET 4.x 응용 프로그램) DI 인식 하므로 여기서 설명 하는 메커니즘을 사용할 수 없습니다 아닐 수 있습니다. 이러한 시나리오를 참조에 대 한는 [비 DI 인식 시나리오](../configuration/non-di-scenarios.md) DI를 통하지 않고 IDataProtection 공급자의 인스턴스를 가져오는 방법에 대 한 자세한 내용은 문서.

다음 샘플에서는 세 가지 개념을 보여 줍니다.

1. [추가 데이터 보호 시스템](../configuration/overview.md) 서비스 컨테이너를

2. DI를 사용 하 여는 IDataProtectionProvider의 인스턴스를 수신 하 고

3. IDataProtector는 IDataProtectionProvider에서 만들고 사용 하 여 보호 하 고 데이터 보호를 해제 합니다.

[!code-csharp[Main](../using-data-protection/samples/protectunprotect.cs?highlight=26,34,35,36,37,38,39,40)]

Microsoft.AspNetCore.DataProtection.Abstractions 패키지 개발자 편의 위해 IServiceProvider.GetDataProtector 확장 메서드를 포함합니다. 서비스 공급자에서 프로그램 IDataProtectionProvider 검색 및 IDataProtectionProvider.CreateProtector 호출 모두 단일 작업으로 캡슐화 합니다. 다음 샘플의 사용법을 보여 줍니다.

[!code-csharp[Main](./overview/samples/getdataprotector.cs?highlight=15)]

>[!TIP]
> IDataProtectionProvider 및 IDataProtector의 인스턴스는 스레드로부터 안전 여러 호출자에 대 한 합니다. 구성 요소 호출 CreateProtector 통해 IDataProtector에 대 한 참조를 가져온 후 참조 하는 보호를 여러 번 호출에 사용할 및 보호 된 페이로드 수 없는 경우 Unprotect Unprotect.A 호출 CryptographicException 시킵니다 것은 확인 또는 해독 합니다. 일부 구성 요소 오류를 무시 하려는 경우가 있을 수 하는 동안 작업을 보호 해제 인증 쿠키를 읽는 구성 요소 수 있습니다이 오류를 처리 하 고 전혀 쿠키가 없는 이전의 요청 처럼 처리할 대신 완전 한 요청에 실패 합니다. 구체적으로이 동작을 방지 하는 구성 요소는 모든 예외를 받아들이지 않고 CryptographicException을 catch 해야 합니다.
