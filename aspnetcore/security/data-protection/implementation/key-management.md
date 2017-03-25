---
title: "키 관리 | Microsoft 문서"
author: rick-anderson
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: fb9b807a-d143-4861-9ddb-005d8796afa3
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/implementation/key-management
translationtype: Machine Translation
ms.sourcegitcommit: 010b730d2716f9f536fef889bc2f767afb648ef4
ms.openlocfilehash: 22b72823129996299ffc2fc31e68600ba584cb83
ms.lasthandoff: 03/23/2017

---
# <a name="key-management"></a>키 관리

<a name=data-protection-implementation-key-management></a>

데이터 보호 시스템에는 자동으로 보호 하 고 페이로드 보호를 해제 하는 데 사용 되는 마스터 키의 수명을 관리 합니다. 각 키&4; 개 단계 중 하나에 있을 수 있습니다.

* 생성-키 키 링에 존재 하지만 아직 활성화 되지 않았습니다. 키 서는 안 사용할 새 암호로 보호 작업에 대 한 충분 한 시간이 경과 될 때까지 키가 유익한이 키 고리를 사용 하는 모든 컴퓨터에 전파 합니다.

* Active-키 키 고리 있으며 모든 새 암호로 보호 작업에 대해 사용 해야 합니다.

* 만료-키가 자연 스러운 수명 실행 되었고 새로운 보호 작업에 더 이상 사용할 합니다.

* 해지 되-키가 손상 되 고 새 보호 작업에 사용할 수 없습니다.

생성 된, 활성화 및 만료 된 키 모두 데 사용할 수 있습니다 들어오는 페이로드 보호를 해제 합니다. 기본적으로 키를 해지 된 페이로드를 보호 해제를 사용할 수 없습니다 있지만 응용 프로그램 개발자는 [이 동작을 재정의할](../consumer-apis/dangerous-unprotect.md#data-protection-consumer-apis-dangerous-unprotect) 필요한 경우.

>[!WARNING]
> 개발자 (예:에서 삭제 하 여 해당 파일은 파일 시스템) 키 링에서 키를 삭제 하 려 할 수 있습니다. 이 시점에서 키로 보호 되는 모든 데이터가 영구적으로 해독할 수 이며 응급 재정의 해지 된 키와 함께 제공 되는 진정한 삭제 동작을가 키를 삭제 하 고 데이터 보호 시스템이이 작업을 수행 하기 위한 첫 번째 클래스 API를 노출 하는 결과적으로 합니다.

## <a name="default-key-selection"></a>기본 키 선택

데이터 보호 시스템 백업 저장소에서 키 고리 읽으면 키 링에서 "기본" 키를 찾으려고 시도 합니다. 기본 키 보호 하는 새 작업에 사용 됩니다.

일반 추론은 데이터 보호 시스템 가장 최근 정품 인증 날짜의 기본 키로 사용 하 여 키를 선택 합니다. (서버에 서버 클록 오차를 허용 하는 작은 오차가입니다.) 키가 만료 되었거나 해지 하는 경우 응용 프로그램이 비활성화 되지 않은 자동 및 키 생성을 경우 즉시 정품 인증 당 새 키가 생성는 [키 만료 및 롤링](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management-expiration) 아래 정책입니다.

데이터 보호 시스템 이유는 새 키를 즉시 생성 하지 않고 다른 키로 대체는 새 키 전에 활성화 되었던 모든 키의 만료 날짜 암시적으로 새 키 생성을 처리 해야 한다는. 일반적인 생각은 다양 한 알고리즘 또는 이전 키 보다 정지 된 암호화 메커니즘으로 새 키 구성 하 고 시스템 라면 현재 구성을 대체를 통해 것이 좋습니다.

예외가 있습니다. 응용 프로그램 개발자가 경우 [자동 키 생성을 사용 하지 않도록 설정](../configuration/overview.md#data-protection-configuring-disable-automatic-key-generation), 데이터 보호 시스템의 기본 키로 선택 해야 합니다. 대체 (fallback)이 시나리오에서는 시스템 클러스터의 다른 컴퓨터에 전파 하는 동안 키를 제공 하는 기본 설정으로 가장 최근에 활성화 날짜, 사용 하 여 비 해지 된 키를 선택 합니다. 대체 (fallback) 시스템은 결과적으로 만료 된 기본 키를 선택 끝날 수도 있습니다. 대체 (fallback) 시스템 됩니다 하지 해지 된 키를 기본 키로 선택한 키 링이 비어 있거나 모든 키가 해지 된 경우 시스템에서 생성 합니다 초기화 시 오류가 발생 합니다.

<a name=data-protection-implementation-key-management-expiration></a>

## <a name="key-expiration-and-rolling"></a>키 만료 및 롤링

키를 만들 때 {현재 날짜 + 2 일}의 정품 인증 날짜는 및 {현재 날짜 + 90 일}의 만료 날짜는 자동으로 지정 됩니다. 2 일 지연 활성화 하기 전에 시스템을 통해 전파 되는 데 키 시간이 주어 집니다. 즉, 다른 응용 프로그램을 백업 저장소를 가리키는 따라서는 키 고리에 전파 되는 활성 때 해야 할 수 있는 모든 응용 프로그램 사용 가능성을 최대화 들은 다음 자동 새로 고침 기간 동안에는 키를 확인할 수 있습니다.

기본 키 2 일 내에 만료 되 고 키 고리 아직 없는 경우에 기본 키의 만료 시 활성화 되는 키의 데이터 보호 시스템에는 키 링에 새 키를 자동으로 유지 됩니다. 이 새 키에는 정품 인증 날짜의 {기본 키의 만료 날짜} 및 {현재 날짜 + 90 일}의 만료 날짜가 있습니다. 이 통해 자동으로 정기적으로 서비스의 중단 없이 키를 롤링 하 시스템입니다.

경우도 즉시 정품 인증 키를 만들 위치입니다. 한 가지 예는 한 번에 대 한 응용 프로그램이 실행 되지 않은 하 고 모든 키 키 링에는 만료 된 것입니다. 이 경우 일반 2 일 활성화 지연 시간 없이 {이제}의 정품 인증 날짜는 키에 부여 됩니다.

기본 키 수명은 90 일이 값은 다음 예제와 같이 구성할 수입니다.

```csharp
services.AddDataProtection()
       // use 14-day lifetime instead of 90-day lifetime
       .SetDefaultKeyLifetime(TimeSpan.FromDays(14));
   ```

관리자가 기본 시스템 차원 변경할 수도 있지만 SetDefaultKeyLifetime 명시적으로 호출 모든 시스템 수준 정책에 우선 합니다. 기본 키 수명 7 일 미만인 수 없습니다.

## <a name="automatic-keyring-refresh"></a>인증 키를 자동 새로 고침

초기화 될 때 데이터 보호 시스템, 키 고리 기본 저장소에서 읽고 메모리에 캐시 합니다. 이 캐시 보호와 보호 해제 작업을을 백업 저장소에 접하지 않고도 진행할 수 있습니다. 자동으로 시스템은 약 24 시간 마다 또는 현재 기본 키가 만료 되 면 중 하나에 먼저 변경에 대 한 백업 저장소를 확인 해야 합니다.

>[!WARNING]
> 개발자는 거의 (있는 경우) 키 관리 Api를 직접 사용 해야 합니다. 위에서 설명한 것 처럼 데이터 보호 시스템에서 자동 키 관리를 수행 합니다.

데이터 보호 시스템 IKeyManager를 검사 하 고 변경할 키 고리를 사용할 수 있는 인터페이스를 노출 합니다. IDataProtectionProvider의 인스턴스를 제공 하는 DI 시스템 프로그램 소비에 대 한 IKeyManager의 인스턴스를 제공할 수도 있습니다. 또는에서 가져올 수 있습니다는 IKeyManager 직선 다음 예와 같이 IServiceProvider입니다.

(새 키를 명시적으로 만드는 또는 해지 수행) 키 고리를 수정 하는 모든 작업에는 메모리 내 캐시를 무효화 됩니다. 다음 호출을 보호 하거나 보호 해제 데이터 보호 시스템 키 링을 다시 읽고을 하면 캐시가 다시 생성 하면 됩니다.

아래 샘플은 IKeyManager 인터페이스를 사용 하 여 검사 하 고 호출 키를 기존 및 새 키를 수동으로 생성을 포함 하 여 키 링을 조작 하는 방법을 보여 줍니다.

[!code-none[주](key-management/samples/key-management.cs)]

## <a name="key-storage"></a>키 저장소

데이터 보호 시스템에는 적절 한 키 저장소 위치 및 나머지 메커니즘에서 암호화를 자동으로 추론 하려고 시도 하면 디코딩하는 추론 합니다. 응용 프로그램 개발자가 구성 가능한 이기도합니다. 다음 문서에는 이러한 메커니즘의 기본 구현에서 설명합니다.

* [기본 키 저장소 공급자](key-storage-providers.md#data-protection-implementation-key-storage-providers)

* [나머지 공급자에 기본 키 암호화](key-encryption-at-rest.md#data-protection-implementation-key-encryption-at-rest-providers)

