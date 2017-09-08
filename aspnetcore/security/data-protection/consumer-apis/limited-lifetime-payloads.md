---
title: "보호 된 페이로드의 수명을 제한"
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
ms.openlocfilehash: 4ff13803b328c1e9dd2934c38c88b43f5798de03
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/11/2017
---
# <a name="limiting-the-lifetime-of-protected-payloads"></a>보호 된 페이로드의 수명을 제한

응용 프로그램 개발자가 설정된 된 시간 동안 후에 만료 되는 보호 된 페이로드를 작성 하려고 하는 위치 시나리오가 있습니다. 예를 들어, 보호 된 페이로드는 1 시간 동안 유효 해야만 암호 다시 설정 토큰을 나타낼 수 있습니다. 개발자는 포함 된 만료 날짜를 포함 하는 고유한 페이로드 형식을 만들 수에 대 한 수 및 고급 개발자를 누르고, 이렇게 할 수 있습니다 이지만 대부분 개발자의 이러한 만료 관리 수 커집니다 번거로운.

간단 하 게 하려면이 대상 우리의 개발자에 대 한 패키지 Microsoft.AspNetCore.DataProtection.Extensions 설정 시간이 지나면 자동으로 만료 되는 페이로드를 만들기 위한 유틸리티 Api를 포함 합니다. 이러한 Api의 ITimeLimitedDataProtector 형식에서 중지합니다.

## <a name="api-usage"></a>API 사용

ITimeLimitedDataProtector 인터페이스는 보호 / 시간이 제한 된 보호를 해제 하 고 자체 페이로드를 만료에 대 한 핵심 인터페이스입니다. ITimeLimitedDataProtector의 인스턴스를 만들려면 먼저 해야 일반 인스턴스의 [IDataProtector](overview.md) 특정 용도 사용 하 여 생성 합니다. IDataProtector 인스턴스를 사용할 수 있는 기본 만료 기능으로는 보호기 복귀할 IDataProtector.ToTimeLimitedDataProtector 확장 메서드를 호출 합니다.

ITimeLimitedDataProtector는 다음과 같은 API 화면 및 확장 메서드를 노출합니다.

* CreateProtector (문자열 용도): 만들려는 사용할 수 있다는 점에서 ITimeLimitedDataProtector이 API는 기존 IDataProtectionProvider.CreateProtector 비슷합니다 [체인의 용도 위해](purpose-strings.md) 루트 시간이 제한 된 보호기를 합니다.

* (바이트 일반 텍스트, DateTimeOffset 만료) 보호: byte

* 보호 (바이트 일반 텍스트, TimeSpan 수명): byte

* (바이트 일반 텍스트)를 보호: byte

* (문자열 일반 텍스트, DateTimeOffset 만료) 보호: 문자열

* 보호 (문자열 일반 텍스트, TimeSpan 수명): 문자열

* (문자열 일반 텍스트)를 보호: 문자열

일반 텍스트만 시간은 핵심 보호 방법 외에도 페이로드의 만료 날짜를 지정할 수 있는 새로운 오버 로드가 있습니다. (DateTimeOffset)를 통해 절대 날짜 또는 현재 시스템 시간, TimeSpan 통해) (에서 상대 시간으로 만료 날짜를 지정할 수 있습니다. 만료 날짜를 사용 하지 않습니다는 오버 로드를 호출 하면 페이로드 가정 하지 만료 합니다.

* (바이트 protectedData, DateTimeOffset 만료 아웃)를 보호 해제: byte

* (ProtectedData 바이트)를 보호 해제: byte

* (DateTimeOffset 만료 아웃 문자열 protectedData)를 보호 해제: 문자열

* (문자열 protectedData)를 보호 해제: 문자열

Unprotect 메서드는 원래 보호 되지 않는 데이터를 반환합니다. 페이로드 아직 만료 되지 않았는지, 절대 만료를 선택적 출력 매개 변수는 원래 보호 되지 않는 데이터와 함께 반환 됩니다. 페이로드 만료 되 면 CryptographicException Unprotect 메서드의 모든 오버 로드에 throw 됩니다.

>[!WARNING]
> 하지 장기 또는 무기한 보존 해야 하는 페이로드를 보호 하기 위해 이러한 Api를 사용 하는 것이 좋습니다. "있습니까 여유가 한 달이 지난 후 영구적으로 복구할 수 없게 하려면 보호 된 페이로드"? 바람직한 방법은;으로 사용할 수 있습니다. 대답 한 경우 다음 없습니다 개발자 대체 Api를 고려해 야 합니다.

사용 하 여 아래 샘플은 [DI 아닌 코드 경로](../configuration/non-di-scenarios.md) 데이터 보호 시스템을 인스턴스화하기 위한 합니다. 이 샘플을 실행 하려면 먼저 Microsoft.AspNetCore.DataProtection.Extensions 패키지에 대 한 참조를 추가 했는지 확인 합니다.

[!code-none[Main](limited-lifetime-payloads/samples/limitedlifetimepayloads.cs)]
