---
title: "키를 갖는 해지 된 보호 해제 페이로드 | Microsoft 문서"
author: rick-anderson
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 6c4e6591-45d2-4d25-855e-062ad352d648
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/consumer-apis/dangerous-unprotect
translationtype: Machine Translation
ms.sourcegitcommit: 010b730d2716f9f536fef889bc2f767afb648ef4
ms.openlocfilehash: f218226eb640b31137dcd06db947753a6133eced
ms.lasthandoff: 03/23/2017

---
# <a name="unprotecting-payloads-whose-keys-have-been-revoked"></a>보호 해제 페이로드 키를 갖는 해지 되었습니다.

<a name=data-protection-consumer-apis-dangerous-unprotect></a>

ASP.NET 핵심 데이터 보호 Api는 주로 없습니다의 기밀 페이로드는 무한 한 지 속성. 와 같은 다른 기술은 [Windows CNG DPAPI](https://msdn.microsoft.com/en-us/library/windows/desktop/hh706794%28v=vs.85%29.aspx) 및 [Azure 권한 관리](https://technet.microsoft.com/en-us/library/jj585024.aspx) 무기한 저장 하는 시나리오에 보다 적합 한까지 강력한 키 관리 기능을 갖습니다. 즉, ASP.NET 핵심 데이터 보호 Api를 사용 하 여 기밀 데이터의 장기 보호에 대 한 개발자를 차단 하면 내용이 없습니다. 되므로 키가 사용 가능 하 고 유효한으로 IDataProtector.Unprotect 기존 페이로드를 항상 복구할 수 키 링에서 키가 제거 되지 않습니다.

그러나 문제는 개발자가 IDataProtector.Unprotect이 경우는 예외를 throw 합니다으로 해지 된 키로 보호 된 데이터 보호를 해제 하려고 할 때 발생 합니다. 이 최악의 사이트 방문자가 해야 할 수는 다시 로그인 하 고 이러한 종류의 페이로드 쉽게 다시 만들 수는 시스템에 의해 단기 / 임시 페이로드 (예: 인증 토큰의 경우)에 대 한 세밀 하 게 수 있습니다. 하지만 지속형된 페이로드에 대 한 보호 해제를 throw 하지 손실 될 수 있습니다 허용할 수 없는 데이터.

## <a name="ipersisteddataprotector"></a>IPersistedDataProtector

해지 된 키에 관계 없이 보호 되지 페이로드를 허용 하는 시나리오를 지원 하려면 데이터 보호 시스템 IPersistedDataProtector 형식을 포함 합니다. IPersistedDataProtector의 인스턴스를 가져오려면 일반적인 방식으로 IDataProtector의 인스턴스를 가져올 하 고 IPersistedDataProtector IDataProtector 캐스팅을 시도 합니다.

> [!NOTE]
> 일부 IDataProtector 인스턴스 IPersistedDataProtector로 캐스팅 될 수 있습니다. 연산자와 개발자는 C# 사용 해야 하거나 오류가 발생할 경우를 적절 하 게 처리 하기 위해 런타임 예외를 방지 하려면 잘못 된 캐스트 인해 발생 하며 정상적인 것 유사한 준비 합니다.

IPersistedDataProtector는 다음 API 화면을 표시합니다.

```csharp
DangerousUnprotect(byte[] protectedData, bool ignoreRevocationErrors,
     out bool requiresMigration, out bool wasRevoked) : byte[]
   ```

이 API (바이트 배열)로 보호 된 페이로드 하며 보호 되지 않는 페이로드를 반환 합니다. 문자열 기반 오버 로드가 있습니다. Out 매개 변수&2;는 다음과 같습니다.

* requiresMigration:이 페이로드를 보호 하는 데 사용 되는 키가 더 이상 활성 기본 키, 예를 들어,이 페이로드를 보호 하는 데 사용 된 키가 이전 및 이후 작업 롤링 키가 있는 경우 true로 설정 수행 될 위치입니다. 호출자에 게 비즈니스 요구에 따라 페이로드를 다시 보호 하는 것이 좋습니다. 할 수도 있습니다.

* wasRevoked:이 페이로드를 보호 하는 데 사용 되는 키가 해지 하는 경우 true로 설정 됩니다.

>[!WARNING]
> 주의 ignoreRevocationErrors를 전달 하는 경우: DangerousUnprotect 메서드 true로 설정 합니다. 이 메서드를 호출한 후 wasRevoked 값이 true 이면이 페이로드를 보호 하는 데 사용 되는 키가 해지 하 고 주의 대상으로 페이로드의 신뢰성을 처리 해야 합니다. 이 경우에 작업을 계속 보호 되지 않는 페이로드에 별도 일부 보증을 사용 하는 경우 인증 되었는지를 신뢰할 수 없는 웹 클라이언트에서 전송 되는 보다 안전한 데이터베이스에서 예를 들어 한다는 올 합니다.

[!code-none[주](dangerous-unprotect/samples/dangerous-unprotect.cs)]

