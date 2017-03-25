---
title: "문자열 목적 | Microsoft 문서"
author: rick-anderson
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: c96ed361-c382-4980-8933-800e740cfc38
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/consumer-apis/purpose-strings
translationtype: Machine Translation
ms.sourcegitcommit: 010b730d2716f9f536fef889bc2f767afb648ef4
ms.openlocfilehash: 18fb4dfd945734fc7fdf945a2bbaa9088b76da8b
ms.lasthandoff: 03/23/2017

---
# <a name="purpose-strings"></a>목적은 문자열

<a name=data-protection-consumer-apis-purposes></a>

IDataProtectionProvider를 사용 하는 구성 요소에 고유한 통과 해야 *목적으로* CreateProtector 메서드의 매개 변수입니다. 목적 *매개 변수* 루트 암호화 키가 동일한 경우에 암호화 소비자 간에 격리를 제공 하므로 데이터 보호 시스템의 보안에 내재 된 합니다.

소비자는 용도 지정 하는 경우에 해당 소비자에 게 고유한 암호화 하위 키를 파생 하 목적 문자열이 루트 암호화 키와 함께 사용 됩니다. 응용 프로그램에서 다른 모든 암호화 소비자에 게 소비자 그래야만: 다른 구성 요소가 없으면 해당 페이로드를 읽을 수 있으며 모든 다른 구성 요소의 페이로드를 읽을 수 없습니다. 이 격리는 또한 부적합 모든 범주의 구성 요소에 대 한 공격을 렌더링합니다.

![목적은 다이어그램 예제](purpose-strings/_static/purposes.png)

IDataProtector 인스턴스 A와 B 위의 다이어그램에서 **없습니다** 서로의 페이로드만 읽기 자체입니다.

목적은 문자열은 기밀 이어야 할 필요 하지 않습니다. 단순히 다른 커지긴 구성 요소가 없으면 동일한 목적 문자열을 제공 합니다 적 의미에서 고유 해야 합니다.

>[!TIP]
> 데이터 보호 Api를 사용 하는 구성 요소의 네임 스페이스 및 형식 이름을 사용 하 여이 정보를 충돌 하지 것입니다 연습 좋은 경험으로 보아,입니다.
>
>전달자 토큰 minting 담당 하는 Contoso에서 작성 된 구성 요소는의 목적은 문자열이로 Contoso.Security.BearerToken를 사용할 수 있습니다. 또는-더욱-Contoso.Security.BearerToken.v1의 목적은 문자열로 사용할 수도 있습니다. Contoso.Security.BearerToken.v2 해당 목적으로 사용 하 여 이후 버전을 사용 하면 버전 번호를 추가 페이로드 이동와 관련해 서 다양 한 버전을 서로 완전히 격리 하겠습니다.

CreateProtector 목적으로 매개 변수는 문자열 배열 이므로 위의 수 대신로 지정 되어 있지 ["Contoso.Security.BearerToken", "v1"]입니다. 이 위해의 계층 구조 설정를 통해 데이터 보호 시스템을 사용 하는 다중 테 넌 트 시나리오의 가능성을 엽니다.

<a name=data-protection-contoso-purpose></a>

>[!WARNING]
> 구성 요소는 목적으로 체인에 대 한 입력의 유일한 원본 수에 대 한 신뢰할 수 없는 사용자 입력을 허용 하지 않아야 합니다.
>
>예를 들어 구성 요소를 Contoso.Messaging.SecureMessage 보안 메시지를 저장 하기 위한 담당 하는 것이 좋습니다. 보안 메시징 구성 요소를 호출 하는 경우 악의적인 사용자가 다음 CreateProtector ([username])를에서 만들 수 있습니다 계정 사용자 이름으로 "Contoso.Security.BearerToken" CreateProtector (["Contoso.Security.BearerToken"])를 호출 하는 구성 요소를 가져오려는 시도가, 인증 토큰으로 인식 하는 mint 페이로드를 보안 메시징 시스템을 실수로 인해 따라서 합니다.
>
>메시징 구성 요소에 대 한 더 나은 목적으로 체인이 CreateProtector 것 (["Contoso.Messaging.SecureMessage", "사용자: 사용자 이름"]), 적절 한 격리를 제공 하는 합니다.

IDataProtectionProvider 동작, IDataProtector, 용도 및 격리에서 제공 되는 다음과 같습니다.

* 지정된 된 IDataProtectionProvider 개체에 대 한는 CreateProtector 메서드는 그것을 만든 IDataProtectionProvider 개체와 메서드에 전달 된 하는 목적으로 매개 변수를 고유 하 게 관련이 IDataProtector 개체를 만듭니다.

* 목적은 매개 변수가 null 일 수 없습니다. (목적으로 배열로 지정 하는 경우 즉, 배열의 길이가&0; 인 되지 않아야 하 고 배열의 모든 요소에는 null 이어야 합니다.) 빈 문자열 목적은 기술적으로 허용 되지만 사용 하지 않고 있습니다.

* 두 가지 용도로 사용 인수는 동일한 순서로 동일한 문자열 (서 수는 비교자를 사용 하 여)를 포함 하는 경우에 동일 합니다. 단일 목적 인수 해당 단일 요소 목적으로 배열 하는 것과 같습니다.

* 두 IDataProtector 개체는 해당 하는 목적으로 매개 변수를 사용 하 여 해당 IDataProtectionProvider 개체에서 만들어진 경우에 동일 합니다.

* 지정된 된 IDataProtector 개체에 대 한 경우에 Unprotect(protectedData)에 대 한 호출 원래 unprotectedData 반환 합니다 protectedData: 동등한 IDataProtector 개체에 대 한 Protect(unprotectedData) =.

> [!NOTE]
> 경우 다른 구성 요소와 충돌 하는 목적 문자열에 일부 구성 요소가 의도적으로 선택 되지 예정입니다. 이러한 구성 요소는 기본적으로 간주 되 악성 하며이 시스템에 악성 코드는 작업자 프로세스 내부에서 이미 실행 중인 보안 보장을 제공 하 없습니다.

