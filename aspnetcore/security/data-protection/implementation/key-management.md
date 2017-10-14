---
title: "키 관리"
author: rick-anderson
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: fb9b807a-d143-4861-9ddb-005d8796afa3
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/implementation/key-management
ms.openlocfilehash: 687207cb6a1cea89166fd2b6172cdc0a013de4b3
ms.sourcegitcommit: 8f4d4fad1ca27adf9e396f5c205c9875a3963664
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/13/2017
---
# <a name="key-management"></a>키 관리

<a name="data-protection-implementation-key-management"></a>

데이터 보호 시스템에는 자동으로 보호 및 페이로드를 보호 해제 하는 데 사용 되는 마스터 키의 수명을 관리 합니다. 각 키는 4 개 단계 중 하나에 있을 수 있습니다.

* -키 키 링에 존재 하지만 아직 활성화 되지 않았습니다. 키 사용할 수 새 암호로 보호 작업에 대 한 충분 한 시간이 경과할 때까지 키에 준이 키 링을 사용 하는 모든 컴퓨터에 전파 합니다.

* Active-키 키 링에 있고 새 모든 보호 작업에 대해 사용 해야 합니다.

* 만료-키 자연 수명 실행 하 고 새 보호 작업에 대해 더 이상 사용 되지 않음을 합니다.

* 해지 되-키가 손상 되 고를 새 암호로 보호 작업에 대해 사용할 수 없습니다.

만든 활성 및 만료 된 키는 모두 사용할 수 있습니다 들어오는 페이로드를 보호 해제 합니다. 페이로드, 보호 해제를 기본적으로 키를 해지 하지 사용할 수 있지만 응용 프로그램 개발자 수 [이 동작을 재정의할](../consumer-apis/dangerous-unprotect.md#data-protection-consumer-apis-dangerous-unprotect) 필요한 경우.

>[!WARNING]
> 개발자 (예:에서 삭제 하 여 해당 파일은 파일 시스템) 키 링에서 키를 삭제 하려고 시도할 수 있습니다. 해당 시점에 키로 보호 하는 모든 데이터를 영구적으로 해독할 수 없습니다 및 재정의가 없습니다 응급 해지 된 키를 가진는 없습니다. 키 삭제가 실제로 삭제 동작이 며, 결과적으로 데이터 보호 시스템이이 작업을 수행 하기 위한 첫 번째 클래스 API가 없습니다.에서 제공 합니다.

## <a name="default-key-selection"></a>기본 키 선택

데이터 보호 시스템 백업 저장소에서 키 링 읽으면 키 링에서 "default" 키를 찾으려고 시도 합니다. 기본 키 보호는 새 작업에 사용 됩니다.

일반 추론 데이터 보호 시스템이 가장 최근 활성화 날짜의 기본 키로 사용 하 여 키를 선택 하는입니다. (허용 하기 위해 서버에 서버 클록 스큐 작은 오차가입니다.) 키가 만료 되었거나 해지 경우 응용 프로그램이 비활성화 되지 않은 자동 및 키 생성, 다음 당 즉시 정품 인증 된 새 키가 생성 하는 경우는 [키 만료 및 롤링](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management-expiration) 아래의 정책.

암시적 만료 날짜 전에 새 키 정품 인증 된 모든 키의으로 새 키 생성을 처리 해야 하는 다른 키로 변경 하지 않고 이유는 데이터 보호 시스템에서 새 키를 즉시 생성 합니다. 일반적인 개념은 새로운 키를 서로 다른 알고리즘 또는 오래 된 키 보다 휴지 암호화 메커니즘으로 구성 된 시스템은 현재 구성으로 대체 보다 것이 좋은입니다.

예외가 있습니다. 응용 프로그램 개발자가 경우 [자동 키 생성을 사용 하지 않도록 설정](../configuration/overview.md#data-protection-configuring-disable-automatic-key-generation), 데이터 보호 시스템 것을 기본 키를 선택 해야 합니다. 대체 (fallback)이 시나리오에서는 시스템 시간이 클러스터의 다른 컴퓨터에 전파 하는 키에 지정 된 우선 가장 최근에 활성화 날짜, 사용 하 여 비 해지 키를 선택 합니다. 대체 (fallback) 시스템 결과적으로 만료 된 기본 키를 선택 될 수 있습니다. 대체 (fallback) 시스템의 기본 키로 해지 된 키를 선택 하지 않습니다 및 키 링 비어 있거나 모든 키가 해지 되었습니다. 시스템에서 생성 합니다 초기화 시 오류가 발생 합니다.

<a name="data-protection-implementation-key-management-expiration"></a>

## <a name="key-expiration-and-rolling"></a>키 만료 및 롤링

키를 만들 때 {지금 + 2 일}는 활성화 날짜 및 {지금 + 90 일}의 만료 날짜가 자동으로 지정 됩니다. 정품 인증 시스템을 통해 전파 하는 데 키 시간을 제공 하기 전에 2 일 지연 합니다. 즉, 다른 응용 프로그램을 백업 저장소를 가리키는 따라서는 키에 전파 되는 활성 링 하는 경우 해야 할 수 있는 모든 응용 프로그램 사용 가능성을 최대화 자신의 다음 자동 새로 고침 기간에 키를 확인할 수 있습니다.

2 일 이내 만료 될 기본 키 및 키 링이 아직 없는 경우에 기본 키가 만료 시 활성화 되는 키 데이터 보호 시스템에는 키 링에 새 키를 자동으로 유지 됩니다. 이 새 키에 {기본 키의 만료 날짜}의 정품 인증 날짜는 및 {지금 + 90 일}의 만료 날짜가 있습니다. 이 통해 정기적으로 서비스의 중단 없이 키를 자동으로 시스템입니다.

경우도 즉시 정품 인증 키를 만들 위치입니다. 한 가지 예는 응용 프로그램 시간 동안 실행 되지 않은 하 고 키 링에 있는 모든 키 만료 된 것입니다. 이 경우 기본 2 일 활성화 지연 시간 없이 {지금}의 활성화 날짜 키에 제공 됩니다.

기본 키 수명은 90 일이 값은 다음 예제와 같이 구성할 수입니다.

```csharp
services.AddDataProtection()
       // use 14-day lifetime instead of 90-day lifetime
       .SetDefaultKeyLifetime(TimeSpan.FromDays(14));
   ```

관리자가 기본 시스템 차원의 변경할 수도 있지만 SetDefaultKeyLifetime 명시적으로 호출 모든 시스템 수준 정책에 우선 합니다. 기본 키 수명 7 일 보다 짧을 수 없습니다.

## <a name="automatic-keyring-refresh"></a>자동 keyring 새로 고침

데이터 보호 시스템 초기화 될 때 키 링 내부 저장소에서 읽고 메모리에 캐시 합니다. 이 캐시는 백업 저장소에 도달 하지 않고 계속 하려면 Unprotect 및 보호 작업을 허용 합니다. 자동으로 시스템은 약 24 시간 마다 또는 현재 기본 키가 만료 되 면 중 하나에 먼저 변경에 대 한 백업 저장소를 확인 해야 합니다.

>[!WARNING]
> 개발자는 매우 드물게 (있는 경우) 키 관리 Api를 직접 사용 해야 합니다. 위에서 설명한 대로 데이터 보호 시스템에서 자동 키 관리를 수행 합니다.

데이터 보호 시스템 IKeyManager를 검사 하 고 변경할 키 링에 사용할 수 있는 인터페이스를 노출 합니다. DI 시스템 IDataProtectionProvider의 인스턴스를 제공 하는 사용자에 대 한 IKeyManager의 인스턴스를 제공할 수도 있습니다. 또는 가져올 수 있습니다는 IKeyManager 직선 아래 예와 같이 IServiceProvider에서.

키 링 (새 키를 명시적으로 만드는 또는 해지 수행)을 수정 하는 모든 작업 메모리에 캐시를 무효화 됩니다. 다음 호출을 보호 하거나 보호 해제를 사용 하면 데이터 보호 시스템 키 링을 다시 읽고을 하면 캐시가 다시 생성 합니다.

아래 샘플 검사 하 고 호출 키를 기존 및 새 키를 수동으로 생성을 포함 하 여 키 링 조작할 IKeyManager 인터페이스를 사용 하 여 보여 줍니다.

[!code-none[Main](key-management/samples/key-management.cs)]

## <a name="key-storage"></a>키 저장소

데이터 보호 시스템에는 그에 따라 적절 한 키 저장소 위치 및 나머지 메커니즘에서 암호화를 자동으로 추론 하려고 하는 추론을 있습니다. 응용 프로그램 개발자가 구성할 수 이기도합니다. 다음 문서에는 이러한 메커니즘의 기본 구현에서 설명합니다.

* [기본 키 저장소 공급자](key-storage-providers.md#data-protection-implementation-key-storage-providers)

* [나머지 공급자에 기본 키 암호화](key-encryption-at-rest.md#data-protection-implementation-key-encryption-at-rest-providers)
