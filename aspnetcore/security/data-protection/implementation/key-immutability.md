---
title: "주요 불변성 및 설정 변경 | Microsoft 문서"
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
translationtype: Machine Translation
ms.sourcegitcommit: 010b730d2716f9f536fef889bc2f767afb648ef4
ms.openlocfilehash: 2d473ebda5e8cdab76024174aac3d539571477b0
ms.lasthandoff: 03/23/2017

---
# <a name="key-immutability-and-changing-settings"></a>키 불변성과 설정 변경

개체는 백업 저장소에 유지 되 면 해당 표현 영원히 고정 되어 있습니다. 새 데이터에 백업 저장소에 추가할 수 있지만 기존 데이터를 변경할 수 없습니다. 이 동작의 주 목적은 데이터 손상을 방지 하는 것입니다.

이 동작의 이점 중 하나는 백업 저장소에 키를 쓴 후 있다는 것입니다 하지 변경할 수 있습니다. IKeyManager를 사용 하 여 취소할 수 있지만 해당 생성, 활성화 및 만료 날짜를 변경할 수 없습니다. 또한 해당 기본 알고리즘 정보, 마스터 키 자료를 나머지 속성에서 암호화 되지 않습니다도 변경할 수 있습니다.

개발자 키 지 속성에 영향을 주는 설정을 변경 되 면 해당 변경 내용을 다루지 것입니다 효과 IKeyManager.CreateNewKey 명시적으로 호출을 통해 또는 시스템을 통해 데이터 보호의 고유 키가 생성 전 까지는 [자동 키 생성](key-management.md#data-protection-implementation-key-management) 동작 합니다. 키 지 속성에 영향을 주는 설정은 다음과 같습니다.

* [기본 키 수명](key-management.md#data-protection-implementation-key-management)

* [나머지 메커니즘에 키 암호화](key-encryption-at-rest.md#data-protection-implementation-key-encryption-at-rest)

* [키 알고리즘 정보](../configuration/overview.md#data-protection-changing-algorithms)

이러한 설정은 다음 자동 키 롤링 시간 보다 이전 시작에 필요한 경우 새 키 생성을 강제로 IKeyManager.CreateNewKey 명시적으로 호출 하는 것이 좋습니다. 명시적으로 활성화 날짜를 제공 해야 합니다. ({이제 + 2 일}는 변경 내용을 전파 하기 위해 시간을 허용 하도록 좋은 경험으로 보아) 및 호출에 대 한 만료 날짜입니다.

>[!TIP]
> 저장소를 변경 하는 모든 응용 프로그램 IDataProtectionBuilder 확장 메서드를 사용한 동일한 설정을 지정 해야, 그렇지 않으면 지속형된 키의 속성 키를 생성 하는 루틴을 호출 하는 특정 응용 프로그램에 종속 됩니다.

