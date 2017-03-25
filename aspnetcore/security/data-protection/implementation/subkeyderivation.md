---
title: "파생 및 인증 된 암호화 하위 | Microsoft 문서"
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
translationtype: Machine Translation
ms.sourcegitcommit: 010b730d2716f9f536fef889bc2f767afb648ef4
ms.openlocfilehash: c71b4f86e27981042040dcf3347498bd4e304254
ms.lasthandoff: 03/23/2017

---
# <a name="subkey-derivation-and-authenticated-encryption"></a>하위 키 파생 및 인증 된 암호화

<a name=data-protection-implementation-subkey-derivation></a>

대부분의 키에서 키 원의 일종의 엔트로피를 포함 하 고 "CBC 모드 암호화 + HMAC 유효성 검사" 내용의 알고리즘 정보를 갖습니다 또는 "GCM 암호화 + 유효성 검사"입니다. 이러한 경우 라는 포함 된 엔트로피가이 키에 대 한 마스터 키 자료가 (또는 KM) 및 실제 암호화 작업에 사용할 키를 파생 하는 키 파생 함수를 수행 했습니다.

> [!NOTE]
> 키가 추상이 고 아래와 같이 사용자 지정 구현을 작동 하지 않을 수 있습니다. 키에는 기본 제공 팩터리 중 하나를 사용 하는 것이 아니라 IAuthenticatedEncryptor 자체 구현을 제공 하는 경우에 더 이상이 섹션에서 설명 하는 메커니즘 적용 됩니다.

<a name=data-protection-implementation-subkey-derivation-aad></a>

## <a name="additional-authenticated-data-and-subkey-derivation"></a>추가 인증 된 데이터 및 하위 키 파생

IAuthenticatedEncryptor 인터페이스 모든 인증 된 암호화 작업에 대 한 핵심 인터페이스로 사용합니다. 해당 Encrypt 메서드는 두 개의 버퍼: 일반 텍스트와 additionalAuthenticatedData (AAD). 일반 텍스트 콘텐츠 흐름 IDataProtector.Protect 호출 하지 않고 그대로 AAD 시스템에 의해 생성 되 고 세 가지 구성 요소로 이루어져 있습니다.

1. 32 비트 마법 헤더 09 F0 C9 F0이이 버전의 데이터 보호 시스템을 식별 하는.

2. 128 비트 키 id입니다.

3. 이 작업을 수행 하는 IDataProtector를 생성 하는 목적은 체인에서 구성 되는 다양 한 길이의 문자열입니다.

AAD가 세 구성 요소 모두의 튜플에 대 한 고유 하므로 새로운에서 키를 파생 KM KM 자체 모든 우리의 암호화 작업을 사용 하는 대신 사용할 수 있습니다. IAuthenticatedEncryptor.Encrypt 모든 호출에 대 한 다음 키 파생 프로세스가 수행이 됩니다.

(K_E, K_H) = SP800_108_CTR_HMACSHA512 (K_M, AAD contextHeader | | keyModifier)

NIST SP800-108 KDF 카운터 모드에서 호출 하는 여기에서 (참조 [NIST SP800-108](http://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-108.pdf), 초입니다. 5.1) 다음 매개 변수를 사용 합니다.

* 키 파생 키 (KDK) K_M =

* PRF HMACSHA512 =

* 레이블 additionalAuthenticatedData =

* 상황에 맞는 contextHeader = | | keyModifier

컨텍스트 헤더의 가변 길이 이며 기본적으로 역할을 우리 파생할 K_E 및 K_H 알고리즘의 손도장을 합니다. 키 한정자 암호화를 각 호출에 대해 임의로 생성 된 128 비트 문자열 이며을 확률 KDF에 다른 모든 입력이 일정 하는 경우에 KE 및 KH이 특정 인증 암호화 작업에 대해 고유한 지 폭주를 보장 합니다.

CBC 모드 암호화 + HMAC 유효성 검사 작업에 대해 | K_E | 대칭 블록 암호화 키의 길이 및 | K_H | HMAC 루틴의 다이제스트 크기가입니다. GCM 암호화에 대 한 유효성 검사 작업 + | K_H | = 0입니다.

## <a name="cbc-mode-encryption--hmac-validation"></a>CBC 모드 암호화 + HMAC 유효성 검사

K_E 위 메커니즘을 통해 생성 되 면 임의 초기화 벡터를 생성 하 고 일반 텍스트를 암호화 하는 대칭 블록 암호화 알고리즘을 실행 합니다. 초기화 벡터 및 암호화 텍스트 K_H MAC을 생성 하는 키를 사용 하 여 초기화 되는 HMAC 루틴을 통해 다음 실행 반환 값과이 프로세스는 아래에 그래픽으로 표시 됩니다.

![CBC 모드 프로세스 및 반환](subkeyderivation/_static/cbcprocess.png)

*출력: keyModifier = | | iv | | E_cbc (K_E, iv, 데이터) | | HMAC (K_H, iv | | E_cbc (K_E, iv, 데이터))*

> [!NOTE]
> IDataProtector.Protect 구현 됩니다 [마법 머리글과 키 id 앞에 추가](authenticated-encryption-details.md#data-protection-implementation-authenticated-encryption-details) 를 호출자에 게 반환 하기 전에 출력 합니다. 매직 머리글과 키 id는 암시적으로 하기 때문에 속하지 [AAD](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation-aad), 반환 된 마지막 페이로드의 모든 단일 바이트는 MAC에서 인증 되는 키 한정자 KDF에 대 한 입력으로 제공 됩니다, 때문에 의미 하 고

## <a name="galoiscounter-mode-encryption--validation"></a>Galois/카운터 모드 암호화 + 유효성 검사

K_E 위 메커니즘을 통해 생성 되 면 임의의 96 비트 nonce를 생성 하 고 일반 텍스트를 암호화 하는 128 비트 인증 태그 생성 대칭 블록 암호화 알고리즘을 실행 합니다.

![GCM 모드 프로세스 및 반환](subkeyderivation/_static/galoisprocess.png)

*출력: keyModifier = | | nonce | | E_gcm (K_E, nonce, 데이터) | | authTag*

> [!NOTE]
> GCM AAD의 개념을 기본적으로 지원, 경우에 우리 여전히 저장 AAD 원래 KDF에만 해당 AAD 매개 변수에 대 한 GCM에 빈 문자열을 전달 하 고 있습니다. 이러한 이유로 두 가지입니다. 첫째, [민첩성을 지원 하기 위해](context-headers.md#data-protection-implementation-context-headers) K_M를 사용 하 여 암호화 키로 직접 하려고 하지 않습니다. 또한 GCM은 입력에 매우 엄격한 고유성 요구 사항을 적용합니다. GCM 암호화 루틴의 두 적 호출 또는 더 분명 가능성 (키, nonce) 동일한 입력된 데이터 집합 2 쌍을 초과할 수 없습니다 ^32입니다. 2 개 이상의 수행할 수 없습니다. K_E 해결 우리 ^32 암호화 작업 전에 2의 위반을 실행 했습니다 ^-32를 제한 합니다. 이 매우 많은 작업의 경우 처럼 보이지만 트래픽이 많은 웹 서버는 이러한 키에 대 한 일반적인 수명 이내인 단순한 일 이내에 4 십억 요청을 통해 이동할 수 있습니다. 2의 호환성을 유지 하려면 ^-32 확률 제한 128 비트 키 한정자와 근본적으로 지정 된 모든 K_M에 대 한 사용 가능한 작업 수를 확장 하는 96 비트 nonce를 사용 하 여 계속 합니다. CBC 및 GCM 작업 간의 KDF 코드 경로 공유 했습니다 디자인의 단순성을 위해 되며 AAD KDF에 이미 간주 되므로 GCM 루틴에 전달할 필요가 없습니다.

