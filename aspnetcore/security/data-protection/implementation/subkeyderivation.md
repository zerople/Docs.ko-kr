---
title: "하위 키 파생 및 인증 된 암호화"
author: rick-anderson
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 34bb58a3-5a9a-41e5-b090-08f75b4bbefa
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/implementation/subkeyderivation
ms.openlocfilehash: 24ce71b417599bea22b7fae8b384db599f9e907c
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/11/2017
---
# <a name="subkey-derivation-and-authenticated-encryption"></a>하위 키 파생 및 인증 된 암호화

<a name=data-protection-implementation-subkey-derivation></a>

키 링에서 대부분의 키 엔트로피 형태의 되 고 나머지 "CBC 모드 암호화 + HMAC 유효성 검사" 없다는 알고리즘 정보가 더 또는 "GCM 암호화 + 유효성 검사"입니다. 이러한 경우이 키에 대 한 마스터 키 자료가 (또는 KM)로 포함 된 엔트로피 이라고 하 고 실제 암호화 작업에 대해 사용할 키를 파생 하는 키 파생 기능을 수행 했습니다.

> [!NOTE]
> 키가 추상이 고 아래와 같이 사용자 지정 구현을 동작 하지 않을 수 있습니다. 키에는 기본 제공 팩터리 중 하나를 사용 하는 것이 아니라 IAuthenticatedEncryptor 자체 구현을 제공 하는 경우에 더 이상이 섹션에서 설명 하는 메커니즘 적용 됩니다.

<a name=data-protection-implementation-subkey-derivation-aad></a>

## <a name="additional-authenticated-data-and-subkey-derivation"></a>추가 인증 된 데이터 및 하위 키 파생

IAuthenticatedEncryptor 인터페이스 모든 인증 된 암호화 작업에 대 한 핵심 인터페이스로 사용합니다. Encrypt에서는 두 개의 버퍼: 일반 텍스트 및 additionalAuthenticatedData (AAD). 일반 텍스트 콘텐츠 흐름 IDataProtector.Protect에 대 한 호출 하지 않고 그대로 AAD 시스템에 의해 생성 되 고 세 가지 구성 요소로 이루어져 있습니다.

1. 32 비트 매직 헤더 09 F0 C9 F0 데이터 보호 시스템의이 버전을 식별 하는입니다.

2. 128 비트 키 id입니다.

3. 이 작업을 수행 하는 IDataProtector를 생성 하는 목적은 체인에서 구성 되는 다양 한 길이의 문자열입니다.

AAD 세 구성 요소 모두의 튜플을 대 한 고유 이기 때문에 새에서 키를 파생 KM KM 자체 모든 페이지에서 암호화 작업을 사용 하는 대신 사용할 수 있습니다. 다음 키 파생 프로세스에 IAuthenticatedEncryptor.Encrypt에 모든 호출에 대해 수행이 됩니다.

(K_E, K_H) SP800_108_CTR_HMACSHA512 = (K_M, AAD contextHeader | | keyModifier)

NIST SP800 108 KDF 카운터 모드로 전화를 걸고 여기 (참조 [NIST SP800-108](http://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-108.pdf)초, 합니다. 5.1)는 다음 매개 변수:

* 키 파생 키 (KDK) K_M =

* PRF HMACSHA512 =

* 레이블 additionalAuthenticatedData =

* 상황에 맞는 contextHeader = | | keyModifier

컨텍스트 헤더의 가변 길이 이며 기본적으로 역할을 K_E 및 K_H를 파생 하는에서는 알고리즘의 손도장을 합니다. 키 한정자 암호화를 호출할 때마다에 대해 임의로 생성 된 128 비트 문자열이 며 확률 KE와 KH는 고유이 특정 인증 암호화 작업에 대 한 다른 모든 입력을 KDF 상수 경우에 과도 하 게 많아지지으로 확인 하는 데 사용 합니다.

CBC 모드 암호화 + HMAC 유효성 검사 작업에 대 한 | K_E | 블록 대칭 암호화 키의 길이 및 | K_H | HMAC 루틴의 다이제스트 크기가입니다. GCM 암호화에 대 한 유효성 검사 작업 + | K_H | = 0.

## <a name="cbc-mode-encryption--hmac-validation"></a>CBC 모드 암호화 + HMAC 유효성 검사

K_E 위 메커니즘을 통해 생성 되 면 임의 초기화 벡터를 생성 했습니다 고 일반 텍스트를 암호화 하는 대칭 블록 암호화 알고리즘을 실행 합니다. 초기화 벡터 및 암호화 텍스트 K_H MAC을 생성 하기 위해 키를 사용 하 여 초기화 HMAC 루틴을 통해 다음 실행 이 프로세스와 반환 값은 아래 그래픽으로 표시 됩니다.

![CBC 모드 프로세스 및 반환](subkeyderivation/_static/cbcprocess.png)

*출력: keyModifier = | | iv | | E_cbc (K_E, iv, 데이터) | | HMAC (K_H, iv | | E_cbc (K_E, iv, 데이터))*

> [!NOTE]
> IDataProtector.Protect 구현 됩니다 [매직 머리글과 키 id 앞에 추가](authenticated-encryption-details.md#data-protection-implementation-authenticated-encryption-details) 를 호출자에 게 반환 하기 전에 출력 합니다. 매직 머리글과 키 id는 암시적으로 하기 때문에 속하지 [AAD](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation-aad), 키 한정자는 KDF에 입력으로 제공 됩니다, 때문에이 경우 반환 된 마지막 페이로드는 모든 단일 바이트가 MAC에서 인증

## <a name="galoiscounter-mode-encryption--validation"></a>Galois/카운터 모드 암호화 + 유효성 검사

K_E 위 메커니즘을 통해 생성 되 면 임의 96 비트 nonce를 생성 했습니다 고을 일반 텍스트를 암호화 하 고 128 비트 인증 태그가 생성 블록 대칭 암호화 알고리즘을 실행 합니다.

![GCM 모드 프로세스 및 반환](subkeyderivation/_static/galoisprocess.png)

*출력: keyModifier = | | nonce | | E_gcm (nonce, 데이터, K_E) | | authTag*

> [!NOTE]
> GCM AAD의 개념을 고유 하 게 지 원하는 경우에에서는 여전히 저장 AAD 원래 KDF에만 해당 AAD 매개 변수에 대 한 GCM에 빈 문자열을 전달 하 고 있습니다. 그 이유는 두 가지가 있습니다. 첫째, [민첩성을 지원 하기 위해](context-headers.md#data-protection-implementation-context-headers) K_M를 사용 하 여 암호화 키로 직접 하려고 하지 않습니다. 또한 GCM 해당 입력에 대해 매우 엄격한 고유성 요구 사항을 적용 합니다. GCM 암호화 루틴의 두 개에서 호출 된 적이 또는 더 분명 가능성 (키, nonce) 동일한 입력된 데이터 집합 2 쌍을 초과할 수 없습니다 ^32입니다. 2 개 이상의 수행할 수 없습니다. 여기서 K_E를 해결 하는 경우 ^32 암호화 작업 전에 2의 위반을 실행 했습니다 ^-32를 제한 합니다. 이 처럼 매우 많은 수의 작업, 보이지만 트래픽이 많은 웹 서버는 단순한 일 전 부터는 이러한 키에 대 한 일반적인 수명 범위 내에 4 십억 요청을 통해 넣을 수 있습니다. 값 2의 준수할 수 ^-32 확률도 계속 해 서 128 비트 키 한정자와 근본적으로 지정 된 모든 K_M에 대 한 사용 가능한 작업 수를 확장 하는 96 비트 nonce를 사용 합니다. CBC 및 GCM 작업 사이의 KDF 코드 경로 공유 하겠습니다 디자인을 간소화 하 고 없기 때문에 KDF에서 AAD 이미 것으로 간주 GCM 루틴에 전달할 필요가 없습니다.
