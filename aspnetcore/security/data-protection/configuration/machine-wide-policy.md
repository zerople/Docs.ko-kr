---
title: "컴퓨터 넓은 정책 | Microsoft 문서"
author: rick-anderson
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 285ae47d-e0bf-4b03-b0a8-2b1fb18bc3a1
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/configuration/machine-wide-policy
translationtype: Machine Translation
ms.sourcegitcommit: 010b730d2716f9f536fef889bc2f767afb648ef4
ms.openlocfilehash: 767c38232adbcbd018fd9fe31610d295f2ac1c5e
ms.lasthandoff: 03/23/2017

---
# <a name="machine-wide-policy"></a>컴퓨터 단위 정책

<a name=data-protection-configuration-machinewidepolicy></a>

Windows에서 실행 되 고 데이터 보호 시스템은 데이터 보호를 사용 하는 모든 응용 프로그램에 대 한 기본 컴퓨터 차원의 정책 설정에 대 한 지원이 제한적으로 합니다. 일반적인 개념은 관리자 수를 수동으로 컴퓨터에서 모든 응용 프로그램을 업데이트할 필요 없이 몇 가지 기본 설정 (예: 알고리즘 또는 키 사용 수명)를 변경 하려면.

>[!WARNING]
> 시스템 관리자는 기본 정책을 설정할 수 있지만 적용할 수 없습니다. 응용 프로그램 개발자 자신의 선택 중 하나로 값을 무시할 수 있습니다. 기본 정책은 응용 프로그램을 개발자가 몇 가지 특정 설정에 대 한 명시적 값을 지정 하지 않은만 영향을 줍니다.

## <a name="setting-default-policy"></a>기본 정책 설정

기본 정책을 설정 하려면 관리자 시스템 레지스트리에 다음 키 아래에 알려진된 값을 설정할 수 있습니다.

레지스트리 키:`HKLM\SOFTWARE\Microsoft\DotNetPackages\Microsoft.AspNetCore.DataProtection`

64 비트 운영 체제에 32 비트 응용 프로그램의 동작에 영향을 줄 경우 해야도 Wow6432Node 해당 하는 위의 키를 구성 합니다.

지원 되는 값은

* EncryptionType [문자열]-데이터 보호에 사용할 알고리즘을 지정 합니다. 이 값 "CNG CBC", "CNG-GCM" 또는 "관리" 이어야 하며 자세하게에서 설명 [아래](#data-protection-encryption-types)합니다.

* [DWORD]-DefaultKeyLifetime 새로 생성 된 키에 대 한 수명을 지정 합니다. 이 값을 일 단위로 지정 되며 ≥ 7 이어야 합니다.

* [문자열]-KeyEscrowSinks 키 위탁 기능에 사용할 수 있는 형식을 지정 합니다. 이 값은 키 위탁 싱크, 여기서 목록의 각 요소는 IKeyEscrowSink를 구현 하는 형식의 어셈블리 정규화 된 이름을 세미콜론으로 구분 된 목록.

<a name=data-protection-encryption-types></a>

### <a name="encryption-types"></a>암호화 유형

시스템 Windows CNG에서 제공 하는 서비스와 신뢰성에 대 한 기밀성 및 HMAC CBC 모드 대칭 블록 암호화를 사용 하도록 구성 됩니다 EncryptionType "CNG CBC" 이면 (참조 [사용자 지정 Windows CNG 알고리즘을 지정 하](overview.md#data-protection-changing-algorithms-cng) 대 한 자세한 내용은). 다음 추가 값이 지원 되는지, CngCbcAuthenticatedEncryptionSettings 형식의 속성에 해당 하는 각각:

* EncryptionAlgorithm [문자열]-CNG에서 인식 하는 대칭 블록 암호화 알고리즘의 이름입니다. 이 알고리즘 CBC 모드에서 열립니다.

* EncryptionAlgorithmProvider [문자열]-EncryptionAlgorithm 알고리즘을 만들 수 있는 CNG 공급자 구현의 이름입니다.

* EncryptionAlgorithmKeySize [DWORD]-길이 (비트)를 대칭 블록 암호화 알고리즘에 대 한 파생 키의 합니다.

* HashAlgorithm [문자열]-CNG에서 인식 하는 해시 알고리즘의 이름입니다. 이 알고리즘 HMAC 모드에서 열립니다.

* HashAlgorithmProvider [문자열]-알고리즘 HashAlgorithm을 만들 수 있는 CNG 공급자 구현의 이름입니다.

EncryptionType "CNG GCM" 이면 시스템 기밀성 및 인증에 대 한 Windows CNG에서 제공 하는 서비스와 Galois/카운터 모드 대칭 블록 암호를 사용 하 여 구성 됩니다 (참조 [사용자 지정 Windows CNG 알고리즘을 지정 하](overview.md#data-protection-changing-algorithms-cng) 대 한 자세한 내용은). 다음 추가 값이 지원 되는지, CngGcmAuthenticatedEncryptionSettings 형식의 속성에 해당 하는 각각:

* EncryptionAlgorithm [문자열]-CNG에서 인식 하는 대칭 블록 암호화 알고리즘의 이름입니다. 이 알고리즘 Galois/카운터 모드에서 열립니다.

* EncryptionAlgorithmProvider [문자열]-EncryptionAlgorithm 알고리즘을 만들 수 있는 CNG 공급자 구현의 이름입니다.

* EncryptionAlgorithmKeySize [DWORD]-길이 (비트)를 대칭 블록 암호화 알고리즘에 대 한 파생 키의 합니다.

EncryptionType이 "관리" 시스템 신뢰성에 대 한 기밀성 및 KeyedHashAlgorithm에 대 한 관리 되는 SymmetricAlgorithm를 사용 하 여 구성 됩니다 (참조 [지정 하면 사용자 지정 관리 알고리즘](overview.md#data-protection-changing-algorithms-custom-managed) 대 한 자세한 내용은). 다음 추가 값이 지원 되는지, ManagedAuthenticatedEncryptionSettings 형식의 속성에 해당 하는 각각:

* EncryptionAlgorithmType [문자열]-SymmetricAlgorithm를 구현 하는 형식의 어셈블리 한정 이름입니다.

* EncryptionAlgorithmKeySize [DWORD]-길이 (비트)를 대칭 암호화 알고리즘에 대 한 파생 키의 합니다.

* ValidationAlgorithmType [문자열]-KeyedHashAlgorithm을 구현 하는 형식의 어셈블리 한정 이름입니다.

EncryptionType (null 이외의 / 비어 있음) 다른 값이 있으면 데이터 보호 시스템 시작 시 예외가 throw 됩니다.

>[!WARNING]
> 형식 이름 (EncryptionAlgorithmType, ValidationAlgorithmType, KeyEscrowSinks)를 포함 하는 기본 정책 설정을 구성할 때 형식을 응용 프로그램에 있어야 합니다. 실제로이 응용 프로그램의 데스크톱 CLR에서 실행 중인 경우 이러한 형식을 포함 하는 어셈블리 되어야 함을 없었기 의미 합니다. 실행 되는 ASP.NET 핵심 응용 프로그램에 대 한 [.NET Core](https://microsoft.com/net/core), 이러한 형식을 포함 하는 패키지를 설치 해야 합니다.

