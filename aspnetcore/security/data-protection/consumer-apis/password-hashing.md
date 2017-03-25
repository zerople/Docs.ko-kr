---
title: "암호 해시 | Microsoft 문서"
author: rick-anderson
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 982a1eb2-1e6f-4909-896f-82784364472d
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/consumer-apis/password-hashing
translationtype: Machine Translation
ms.sourcegitcommit: 010b730d2716f9f536fef889bc2f767afb648ef4
ms.openlocfilehash: cedcca8929b6db7e91724a1c0be6ba2bb2ea9c47
ms.lasthandoff: 03/23/2017

---
# <a name="password-hashing"></a>암호 해시

패키지를 포함 하는 데이터 보호 코드 베이스 *Microsoft.AspNetCore.Cryptography.KeyDerivation* 는 암호화 키 파생 함수를 포함 합니다. 이 패키지는 독립 실행형 구성 요소 및 데이터 보호 시스템의 나머지 부분에 의존 하지 않습니다. 사용할 수 없습니다 완전히 독립적입니다. 소스 편의 위해 기본 데이터 보호 코드와 함께 존재합니다.

패키지는 메서드는 현재 제공 `KeyDerivation.Pbkdf2` 사용 하 여 암호 해시를 허용 하는 [PBKDF2 알고리즘](https://tools.ietf.org/html/rfc2898#section-5.2)합니다. 이 API는.NET Framework의 기존 매우 유사 [Rfc2898DeriveBytes 유형](https://msdn.microsoft.com/en-us/library/System.Security.Cryptography.Rfc2898DeriveBytes(v=vs.110).aspx), 있지만 세 가지 중요 한 차이가 있습니다.

1. `KeyDerivation.Pbkdf2` 메서드를 사용해 여러 PRFs 지원 (현재 `HMACSHA1`, `HMACSHA256`, 및 `HMACSHA512`) 반면는 `Rfc2898DeriveBytes` 형식에서 지원만 `HMACSHA1`합니다.

2. `KeyDerivation.Pbkdf2` 메서드는 현재 운영 체제를 검색 하 고 특정 한 경우에 훨씬 더 나은 성능을 제공 하는 루틴의 가장 최적화 정도가 높은 구현을 선택 하려고 합니다. (Windows 8에서 약 10 배의 처리량을 제공 `Rfc2898DeriveBytes`.)

3. `KeyDerivation.Pbkdf2` 메서드에 필요한 모든 매개 변수를 지정 하려면 호출자 (솔트, PRF, 및 반복 횟수). `Rfc2898DeriveBytes` 형식 이러한 기본값을 제공 합니다.

[!code-csharp[주](password-hashing/samples/passwordhasher.cs)]

ASP.NET 핵심 Id에 대 한 소스 코드를 보려면 `PasswordHasher` 실제 환경에 대 한 형식을 사용 사례입니다.

