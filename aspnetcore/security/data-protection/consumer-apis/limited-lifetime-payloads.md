---
title: "페이로드를 보호 하의 수명을 제한 | Microsoft 문서"
author: rick-anderson
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 000175e2-10fc-43dd-bfc2-51e004b97b44
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/consumer-apis/limited-lifetime-payloads
translationtype: Machine Translation
ms.sourcegitcommit: 010b730d2716f9f536fef889bc2f767afb648ef4
ms.openlocfilehash: 4c25938b38853fbf7a1dd92ccc8175e9675ca33e
ms.lasthandoff: 03/23/2017

---
# <a name="limiting-the-lifetime-of-protected-payloads"></a>보호 된 페이로드의 수명을 제한

응용 프로그램 개발자가 설정된 된 시간 동안 설정 된 후에 만료 되는 보호 된 페이로드를 만들려는 시나리오가 있습니다. 예를 들어, 보호 된 페이로드는&1; 시간 동안 유효 해만 해야 하는 암호 재설정 토큰을 나타낼 수 있습니다. 확실히 된 포함 된 만료 날짜가 포함 된 고유한 페이로드 형식을 만들려는 개발자를 위한 및 고급 개발자 어쨌든 이렇게 하려는 있지만 대부분의 개발자에 대 한 이러한 만료 관리 증가할 수 지루한 작업입니다.

보다 쉽게이 우리의 개발자 대상에 대 한 패키지 Microsoft.AspNetCore.DataProtection.Extensions 시간이 지나면 자동으로 만료 되는 페이로드를 만들기 위한 유틸리티 Api를 포함 합니다. 이러한 Api의 ITimeLimitedDataProtector 형식에서 중지합니다.

## <a name="api-usage"></a>API 사용

ITimeLimitedDataProtector 인터페이스는 보호 하 고 시간 제한 된 보호 해제 / 자체 페이로드를 만료에 대 한 핵심 인터페이스입니다. ITimeLimitedDataProtector의 인스턴스를 만들려면 먼저 해야 일반 인스턴스의 [IDataProtector](overview.md) 특정 용도 사용 하 여 생성 합니다. IDataProtector 인스턴스를 사용할 수 있는 기본 만료 기능을 가진 보호기 복귀할 IDataProtector.ToTimeLimitedDataProtector 확장 메서드를 호출 합니다.

ITimeLimitedDataProtector 다음 API 화면 및 확장 메서드를 노출 합니다.

* CreateProtector (문자열 목적): ITimeLimitedDataProtector이 API는를 만드는 사용 될 수 있다는 점에서 기존 IDataProtectionProvider.CreateProtector 유사 [체인 목적](purpose-strings.md) 루트 시간 제한 된 보호기에서.

* (Byte 일반 텍스트, DateTimeOffset 만료) 보호: byte

* 보호 (byte 일반 텍스트, TimeSpan 수명): byte

* 보호 (byte 일반 텍스트): byte

* (문자열 일반 텍스트, DateTimeOffset 만료) 보호: 문자열

* 보호 (일반 텍스트 문자열, TimeSpan 수명): 문자열

* (문자열 일반 텍스트)을 보호: 문자열

일반 텍스트를 사용 하는 핵심 보호 메서드 외에도 페이로드의 만료 날짜를 지정할 수 있는 새로운 오버 로드가 있습니다. 절대 날짜 (DateTimeOffset)를 통해 또는 (현재 시스템 시간을 TimeSpan 통해)에서 상대 시간으로 만료 날짜를 지정할 수 있습니다. 만료를 사용 하지 않는 오버 로드를 호출 하면 만료에 페이로드 가정 합니다.

* (Byte protectedData, DateTimeOffset 만료 아웃)를 보호 해제: byte

* (Byte protectedData)를 보호 해제: byte

* (DateTimeOffset 만료 아웃 문자열 protectedData)를 보호 해제: 문자열

* (문자열 protectedData)를 보호 해제: 문자열

보호 해제 메서드는 원래 보호 되지 않는 데이터를 반환합니다. 페이로드 아직 만료 되지 않았는지, 절대 만료를 선택적 출력 매개 변수는 원래 보호 되지 않는 데이터와 함께 반환 됩니다. 페이로드 만료 되 면 보호 해제 메서드의 모든 오버 로드 CryptographicException 발생 시킵니다.

>[!WARNING]
> 하지 장기 또는 무한 한 지 속성을 필요한 페이로드를 보호 하기 위해 이러한 Api를 사용 하는 것이 좋습니다. "수용할 수 있는 있습니까 한 달이 지난 후 영구적으로 복구할 수 없게 하려면 보호 된 페이로드"? 좋은 경험으로 보아; 역할도 할 수 있습니다. 선택의 여지가 없습니다 다음 개발자 대체 Api를 고려해 야 합니다.

사용 하 여 아래 예제는 [DI 비 코드 경로](../configuration/non-di-scenarios.md) 데이터 보호 시스템을 인스턴스화하는 데 있습니다. 이 샘플을 실행 하려면 먼저 추가한 Microsoft.AspNetCore.DataProtection.Extensions 패키지에 대 한 참조를 확인 합니다.

[!code-none[주](limited-lifetime-payloads/samples/limitedlifetimepayloads.cs)]

