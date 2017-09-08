---
title: "키 불변성 및 설정 변경"
author: rick-anderson
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: fc911ae3-feca-4ffe-8b43-362bc632fe04
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/implementation/key-immutability
ms.openlocfilehash: 3afce8f84ebe3b709ea169c7db27f99829f157ab
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/11/2017
---
# <a name="key-immutability-and-changing-settings"></a>주요 불변성 및 설정 변경

개체는 백업 저장소에 유지 되 면 표현 영원히 고정 되어 있습니다. 새 데이터에는 백업 저장소에 추가할 수 있지만 기존 데이터를 변경할 수 없습니다. 이 동작의 주 목적은 데이터 손상을 방지 하려면입니다.

이 동작의 한 결과 키를 백업 저장소에 쓴 후 아닌지 변경할 수는입니다. IKeyManager를 사용 하 여 취소할 수 있지만 해당 생성, 활성화, 및 만료 날짜를 변경할 수 없습니다. 또한 해당 기본 알고리즘 정보, 마스터 키 자료 및 나머지 속성에서 암호화도 변경할 수 없는 합니다.

개발자는 설정 키 지 속성에 영향을 주는 변경 되 면 해당 변경 내용이 하지 실시 될 다음에 IKeyManager.CreateNewKey 명시적으로 호출을 통해 또는 데이터 보호 시스템의 자체를 통해 키가 생성 될 때까지 [자동 키 생성](key-management.md#data-protection-implementation-key-management) 동작 합니다. 지 속성 키에 영향을 주는 설정은 다음과 같습니다.

* [기본 키 수명](key-management.md#data-protection-implementation-key-management)

* [나머지 메커니즘에 키 암호화](key-encryption-at-rest.md#data-protection-implementation-key-encryption-at-rest)

* [키 내에 포함 된 알고리즘 정보](../configuration/overview.md#data-protection-changing-algorithms)

이러한 설정은 다음 자동 키 롤링 시간 이전의 조정이 시작 해야 할 경우 새 키 생성을 강제로 IKeyManager.CreateNewKey 명시적으로 호출 하는 것이 좋습니다. 명시적으로 활성화 날짜를 제공 해야 합니다. ({이제 + 2 일을 (를)이는 바람직한 방법은 전파에 대 한 변경에 대 한 시간을 허용 하도록) 및 호출에 대 한 만료 날짜입니다.

>[!TIP]
> 모든 응용 프로그램 저장소를 건드리지 IDataProtectionBuilder 확장 방법을 사용 하 여 동일한 설정을 지정 해야, 그렇지 않으면 지속형된 키의 속성 키 생성 루틴을 호출 하는 특정 응용 프로그램에 종속 됩니다. .
