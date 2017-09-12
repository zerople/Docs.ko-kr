---
title: "컴퓨터 단위 정책"
author: rick-anderson
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 285ae47d-e0bf-4b03-b0a8-2b1fb18bc3a1
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/configuration/machine-wide-policy
ms.openlocfilehash: 7ada940acfbb7fb0887fd7c0cd722bf62f211248
ms.sourcegitcommit: 9cdbfd0d670d70b9c354216aabee260c52dad5ee
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/12/2017
---
# <a name="machine-wide-policy"></a>컴퓨터 단위 정책

<a name=data-protection-configuration-machinewidepolicy></a>

Windows에서 실행 하는 경우 데이터 보호 시스템 데이터 보호를 사용 하는 모든 응용 프로그램에 대 한 기본 시스템 수준의 정책 설정에 대 한 지원이 제한 합니다. 일반적인 개념은는 관리자 컴퓨터에서 모든 응용 프로그램을 수동으로 업데이트 하지 않고도 (예: 알고리즘 또는 키 사용 수명) 일부 기본 설정을 변경 하려면 싶어할 수 있습니다.

>[!WARNING]
> 시스템 관리자는 기본 정책을 설정할 수 있지만 이러한 적용할 수 없습니다. 응용 프로그램 개발자는 자신의 선택 중 하나가 지정 된 모든 값을 무시할 수입니다. 기본 정책은 개발자 일부 특정 설정에 대 한 명시적 값 지정 하지 않은 응용 프로그램을만 영향을 줍니다.

## <a name="setting-default-policy"></a>기본 정책 설정

기본 정책을 설정 하려면 관리자가 다음 키 아래 시스템 레지스트리에 알려진된 값을 설정할 수 있습니다.

레지스트리 키:`HKLM\SOFTWARE\Microsoft\DotNetPackages\Microsoft.AspNetCore.DataProtection`

64 비트 운영 체제에 32 비트 응용 프로그램의 동작을 적용 하는 경우 위의 키 Wow6432Node 해당 하는 구성도 해야 합니다.

지원 되는 값은 같습니다.

* EncryptionType [string]-데이터 보호에 사용할 알고리즘을 지정 합니다. 이 값 "CNG CBC", "CNG-GCM" 또는 "Managed" 이어야 하며 보다 자세히 설명 되어 [아래](#data-protection-encryption-types)합니다.

* [DWORD]-DefaultKeyLifetime 새로 생성 된 키에 대 한 수명을 지정 합니다. 이 값은 일 단위로 지정 되며 ≥ 7 이어야 합니다.

* [String]-KeyEscrowSinks 키 위탁 사용 되는 형식을 지정 합니다. 이 값은 세미콜론으로 구분 된 목록 키 에스크로 싱크를 여기서 목록의 각 요소는 어셈블리의 정규화 된 이름을 IKeyEscrowSink를 구현 하는 형식입니다.

<a name=data-protection-encryption-types></a>

### <a name="encryption-types"></a>암호화 종류

시스템 EncryptionType "CNG CBC" 이면 암호를 사용 하는 CBC 모드 대칭 블록 기밀성 및 HMAC에 대 한 신뢰성에 대 한 Windows CNG에서 제공 하는 서비스와 구성 됩니다 (참조 [사용자 지정 Windows CNG 알고리즘지정](overview.md#data-protection-changing-algorithms-cng)자세한 세부 정보에 대 한). 다음 추가 값이 지원 되는지, CngCbcAuthenticatedEncryptionSettings 형식의 속성에 해당 하는 각:

* EncryptionAlgorithm [string]-CNG에서 인식 하는 대칭 블록 암호화 알고리즘의 이름입니다. 이 알고리즘 CBC 모드에서 열립니다.

* EncryptionAlgorithmProvider [string]-EncryptionAlgorithm 알고리즘을 만들 수 있는 CNG 공급자 구현 이름입니다.

* EncryptionAlgorithmKeySize [DWORD]-대칭 블록 암호화 알고리즘에 대 한 파생 키의 길이 (비트)에 있습니다.

* HashAlgorithm [string]-CNG에서 인식 하는 해시 알고리즘의 이름입니다. 이 알고리즘 HMAC 모드에서 열립니다.

* HashAlgorithmProvider [string]-알고리즘 HashAlgorithm을 만들 수 있는 CNG 공급자 구현 이름입니다.

시스템 기밀성 및 인증에 대 한 Windows CNG에서 제공 하는 서비스와 Galois/카운터 모드 대칭 블록 암호화를 사용 하도록 구성 됩니다 EncryptionType "CNG GCM" 이면 (참조 [사용자 지정 Windows CNG 알고리즘지정](overview.md#data-protection-changing-algorithms-cng)자세한 세부 정보에 대 한). 다음 추가 값이 지원 되는지, CngGcmAuthenticatedEncryptionSettings 형식의 속성에 해당 하는 각:

* EncryptionAlgorithm [string]-CNG에서 인식 하는 대칭 블록 암호화 알고리즘의 이름입니다. 이 알고리즘 Galois/카운터 모드에서 열립니다.

* EncryptionAlgorithmProvider [string]-EncryptionAlgorithm 알고리즘을 만들 수 있는 CNG 공급자 구현 이름입니다.

* EncryptionAlgorithmKeySize [DWORD]-대칭 블록 암호화 알고리즘에 대 한 파생 키의 길이 (비트)에 있습니다.

EncryptionType이 "관리" 시스템 정품에 대 한 관리 되는 SymmetricAlgorithm 기밀성 및 KeyedHashAlgorithm을 사용 하도록 구성 됩니다 (참조 [지정 하면 사용자 지정 관리 알고리즘](overview.md#data-protection-changing-algorithms-custom-managed) 자세한 세부 정보에 대 한). 다음 추가 값이 지원 되는지, ManagedAuthenticatedEncryptionSettings 형식의 속성에 해당 하는 각:

* EncryptionAlgorithmType [string]-SymmetricAlgorithm를 구현 하는 형식의 어셈블리 한정 이름입니다.

* EncryptionAlgorithmKeySize [DWORD]-(비트)의 길이를 대칭 암호화 알고리즘에 대 한 파생 키입니다.

* ValidationAlgorithmType [string]-KeyedHashAlgorithm을 구현 하는 형식의 어셈블리 한정 이름입니다.

EncryptionType이 다른 값 (null이 아닌 / 비어 있음), 데이터 보호 시스템 시작 시 예외를 throw 합니다.

>[!WARNING]
> 형식 이름 (EncryptionAlgorithmType, ValidationAlgorithmType, KeyEscrowSinks)를 포함 하는 기본 정책 설정을 구성할 때 형식을 응용 프로그램에 제공 해야 합니다. 실제로이 응용 프로그램의 데스크톱 CLR에서 실행 중인 경우 이러한 형식을 포함 하는 어셈블리 되어야 함을 GACed 의미 합니다. 실행 되는 ASP.NET Core 응용 프로그램에 대 한 [.NET Core](https://www.microsoft.com/net/core), 이러한 형식을 포함 하는 패키지를 설치 해야 합니다.
