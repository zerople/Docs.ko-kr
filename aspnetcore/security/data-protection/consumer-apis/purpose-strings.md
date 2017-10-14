---
title: "용도 문자열"
author: rick-anderson
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: c96ed361-c382-4980-8933-800e740cfc38
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/consumer-apis/purpose-strings
ms.openlocfilehash: 799c3dc2768e264307783efafee626a346a9362c
ms.sourcegitcommit: 8f4d4fad1ca27adf9e396f5c205c9875a3963664
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/13/2017
---
# <a name="purpose-strings"></a>용도 문자열

<a name="data-protection-consumer-apis-purposes"></a>

IDataProtectionProvider를 사용 하는 구성 요소는 고유한 전달 해야 *목적으로* CreateProtector 메서드의 매개 변수입니다. 목적 *매개 변수* 루트 암호화 키가 동일한 경우에 암호화 소비자 간에 격리 제공 하므로 보안에는 데이터 보호 시스템에 대 한 내재 된 합니다.

소비자는 용도 지정 하는 경우에 해당 고객에 게 고유 암호화 하위 키를 파생 시키는 목적 문자열이 루트 암호화 키와 함께 사용 됩니다. 응용 프로그램에서 다른 모든 암호화 소비자에 게 소비자 그래야만: 다른 구성 요소가 없으면 해당 페이로드를 읽을 수 있으며 모든 다른 구성 요소의 페이로드를 읽을 수 없습니다. 이 격리는 또한 부적합 모든 범주의 구성 요소에 대 한 공격을 렌더링합니다.

![용도 다이어그램 예제](purpose-strings/_static/purposes.png)

IDataProtector 인스턴스 A와 B 위의 다이어그램에서 **없습니다** 페이로드를 읽을 서로의만 자신의 합니다.

용도 문자열 본인 확인 될 필요가 없습니다. 단순히 다른 올바르게 동작 구성 요소가 없으면 동일한 목적 문자열로 제공 적이 됩니다 의미에서 고유 해야 합니다.

>[!TIP]
> 데이터 보호 Api를 사용 하는 구성 요소의 네임 스페이스 및 형식 이름을 사용 하는 좋은 경험상, 연습이 정보를 충돌 하지 것입니다.
>
>전달자 토큰 minting 담당 하는 Contoso에서 작성 된 구성 요소는 해당 용도 문자열로 Contoso.Security.BearerToken을 사용할 수 있습니다. 또는-더 좋은-Contoso.Security.BearerToken.v1의 목적은 문자열을 사용할 수 있습니다. Contoso.Security.BearerToken.v2의 목적으로 사용 하도록 이후 버전을 사용 하면 버전 번호를 추가 하 고 페이로드 이동 관련해 서 다양 한 버전 서로 완전히 격리는 합니다.

CreateProtector 목적으로 매개 변수는 문자열 배열 이므로 위의 수 대신로 지정 되어 있지 ["Contoso.Security.BearerToken", "v1"]입니다. 이 목적으로 계층 구조 설정를 통해 데이터 보호 시스템을 사용 하는 다중 테 넌 트 시나리오의 가능성을 엽니다.

<a name="data-protection-contoso-purpose"></a>

>[!WARNING]
> 구성 요소에서 신뢰할 수 없는 사용자 입력을 위해 체인에 대 한 입력의 유일한 출처로 허용 해야 합니다.
>
>예를 들어 구성 요소를 Contoso.Messaging.SecureMessage 보안 메시지를 저장 하기 위한 담당 하는 것이 좋습니다. 보안 메시징 구성 요소가 CreateProtector ([username])를 호출 하는 경우 악의적인 사용자 하기 위해 호출 하는 구성 요소를 가져올 사용자 이름 "Contoso.Security.BearerToken" 계정을 만들고 수 CreateProtector([" Contoso.Security.BearerToken"]), 따라서 있는 mint 페이로드를 보안 메시징 시스템을 실수로 인해으로 인식 인증 토큰입니다.
>
>메시징 구성 요소에 대 한 더 나은 목적으로 체인 CreateProtector 것 (["Contoso.Messaging.SecureMessage", "사용자: 사용자 이름"]), 적절 한 격리를 제공 하는 합니다.

IDataProtectionProvider 동작, IDataProtector를 목적으로 및에서 제공 되는 격리는 다음과 같습니다.

* 지정된 된 IDataProtectionProvider 개체에 대 한 CreateProtector 메서드 IDataProtector 개체를 이것을 만든 IDataProtectionProvider 개체와 메서드에 전달 된 하는 목적으로 매개 변수 모두에 연결 된 고유 하 게 만듭니다.

* 용도 매개 변수가 null 일 수 없습니다. (목적으로 배열로 지정 된 경우 즉, 배열의 길이가 0 인 되지 않아야 하 고 배열의 모든 요소는 null 이어야 합니다.) 빈 문자열 목적 기술적으로 사용할 수 있지만 않는 것이 좋습니다.

* 두 가지 목적으로 인수는 동일한 순서로 동일한 문자열 (서 수는 비교자를 사용 하 여)를 포함 하는 경우에 동일 합니다. 단일 용도 인수 해당 단일 요소 목적으로 배열 하는 것과 같습니다.

* 해당 하는 목적으로 매개 변수를 사용 하 여 해당 IDataProtectionProvider 개체에서 만들어진 경우에 두 IDataProtector 개체는 동일 합니다.

* 지정된 된 IDataProtector 개체에 대 한 경우에 Unprotect(protectedData)에 대 한 호출 원래 unprotectedData 반환 합니다 protectedData: 동등 IDataProtector 개체에 대 한 Protect(unprotectedData) = 합니다.

> [!NOTE]
> 다른 구성 요소와 충돌 하는 목적은 문자열에 일부 구성 요소 의도적으로 선택 하는 경우를 하려고 하지 않습니다. 이러한 구성 요소는 기본적으로 간주 되 악성 하며이 시스템에 작업자 프로세스 내의 악성 코드가 이미 실행 되 고 보안 보장을 제공 하 없습니다.
