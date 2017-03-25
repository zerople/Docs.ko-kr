---
title: "암호화 정보를 인증합니다. | Microsoft 문서"
author: rick-anderson
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 826e6d5d-9620-44e6-ad93-3b1d9969b70b
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/implementation/authenticated-encryption-details
translationtype: Machine Translation
ms.sourcegitcommit: 010b730d2716f9f536fef889bc2f767afb648ef4
ms.openlocfilehash: 6a1e989c144414eadbb707d4f42035a16686360f
ms.lasthandoff: 03/23/2017

---
# <a name="authenticated-encryption-details"></a>암호화 정보를 인증합니다.

<a name=data-protection-implementation-authenticated-encryption-details></a>

IDataProtector.Protect에 대 한 호출에는 인증된 암호화 작업입니다. Protect 메서드 신뢰성과 신뢰성을 소개 하며 IDataProtectionProvider 루트에서이 특정 IDataProtector 인스턴스를 파생 시키는 데 사용 된 목적 체인에 연결 합니다.

IDataProtector.Protect은 byte 일반 텍스트 매개 변수를 사용 하 고 바이트 보호 된 페이로드, 해당 형식은 아래에 설명 된를 생성 합니다. (있어 하는 확장 메서드 오버 로드가 문자열 일반 텍스트 매개 변수를 사용 하 고 문자열 보호 된 페이로드를 반환 합니다. 이 API를 사용 하는 경우 보호 된 페이로드 형식을 계속 해 서는 아래 구조를 생성 하지만 [base64url로 인코딩된](https://tools.ietf.org/html/rfc4648#section-5).)

## <a name="protected-payload-format"></a>보호 된 페이로드 형식

보호 된 페이로드 형식은 세 가지 기본 구성 요소가 구성 됩니다.

* 데이터 보호 시스템의 버전을 식별 하는 32 비트 마법 헤더입니다.

* 이 특정 페이로드를 보호 하는 데 사용 되는 키를 식별 하는 128 비트 키 id입니다.

* 보호 된 페이로드의 나머지 부분이 [이 키에 의해 캡슐화 암호기 특정](subkeyderivation.md#data-protection-implementation-subkey-derivation)합니다. 아래 예제에서는 키는 256-AES-CBC + HMACSHA256 암호기 나타내고 페이로드는 다음과 같이 더 세분화: *128 비트 키 한정자입니다.*128 비트 초기화 벡터입니다. *256-AES-CBC 출력의 48 바이트입니다.*HMACSHA256 인증 태그입니다.

샘플 보호 된 페이로드 아래에서 설명 됩니다.

<!-- literal_block {"xml:space": "preserve", "source": "security/data-protection/implementation/authenticated-encryption-details/_static/protectedpayload.txt", "ids": [], "linenos": true, "highlight_args": {"linenostart": 1}} -->

```
09 F0 C9 F0 80 9C 81 0C 19 66 19 40 95 36 53 F8
   AA FF EE 57 57 2F 40 4C 3F 7F CC 9D CC D9 32 3E
   84 17 99 16 EC BA 1F 4A A1 18 45 1F 2D 13 7A 28
   79 6B 86 9C F8 B7 84 F9 26 31 FC B1 86 0A F1 56
   61 CF 14 58 D3 51 6F CF 36 50 85 82 08 2D 3F 73
   5F B0 AD 9E 1A B2 AE 13 57 90 C8 F5 7C 95 4E 6A
   8A AA 06 EF 43 CA 19 62 84 7C 11 B2 C8 71 9D AA
   52 19 2E 5B 4C 1E 54 F0 55 BE 88 92 12 C1 4B 5E
   52 C9 74 A0
   ```

첫 번째 32 비트 또는 4 바이트 페이로드 형식 (09 F0 C9 F0) 버전을 확인 하는 마법 헤더에는

다음 128 비트 또는 16 바이트는 키 식별자 (80 9 C 81 0 C 19 66 19 40 95 36 53 F8 AA FF EE 57)

나머지는 페이로드를 포함 하 고 사용 하는 형식에 적용 됩니다.

>[!WARNING]
> 지정된 된 키를 보호 하는 모든 페이로드 동일한 20 바이트 (마법 값, 키 id) 헤더로 시작 됩니다. 관리자는 페이로드 생성 된 때를 대략적으로 진단 목적을 위해이 팩트를 사용할 수 있습니다. 예를 들어 위의 페이로드 {0c819c80-6619-4019-9536-53f8aaffee57} 키에 해당합니다. 이 특정 키의 정품 인증 날짜를 2015-01-01 및 만료 날짜를 2015-03-01 다음 가정 하는 것이 찾을 키 저장소를 확인 한 후 페이로드 (변조 되지) 하는 경우 생성 된 어느 쪽에 작은 오차를 팩터링 하 해당 창, 제공 또는 take 내에서.

