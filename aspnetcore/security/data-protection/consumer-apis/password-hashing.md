---
title: "암호 해시"
author: rick-anderson
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 982a1eb2-1e6f-4909-896f-82784364472d
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/consumer-apis/password-hashing
ms.openlocfilehash: d4b39bf7feba8a29e0b5c9e56d53a85b82977b7e
ms.sourcegitcommit: 9cdbfd0d670d70b9c354216aabee260c52dad5ee
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/12/2017
---
# <a name="password-hashing"></a>암호 해시

패키지를 포함 하는 데이터 보호 코드 베이스 *Microsoft.AspNetCore.Cryptography.KeyDerivation* 는 암호화 키 파생 함수를 포함 합니다. 이 패키지는 독립 실행형 구성 요소와 데이터 보호 시스템의 나머지 부분에 의존 하지 않습니다. 사용할 수 없습니다 완전히 독립적입니다. 소스 편의 위해 기본 데이터 보호 코드와 함께 존재합니다.

패키지는 현재 메서드를 제공 `KeyDerivation.Pbkdf2` 사용 하 여 암호 해시를 허용 하는 [PBKDF2 알고리즘](https://tools.ietf.org/html/rfc2898#section-5.2)합니다. 이 API는.NET Framework의 기존와 매우 유사 [Rfc2898DeriveBytes 형식](https://docs.microsoft.com/dotnet/api/system.security.cryptography.rfc2898derivebytes), 하지만 세 가지 중요 한 차이가 있습니다.

1. `KeyDerivation.Pbkdf2` 여러 PRFs 사용 방법은 지원 (현재 `HMACSHA1`, `HMACSHA256`, 및 `HMACSHA512`) 인 반면는 `Rfc2898DeriveBytes` 형식에서 지원만 `HMACSHA1`합니다.

2. `KeyDerivation.Pbkdf2` 메서드는 현재 운영 체제를 검색 하 고 특정 한 경우에 훨씬 더 나은 성능을 제공 하는 루틴의 가장 최적화 된 구현을 선택 하려고 합니다. (Windows 8에서 약 10 배의 처리량을 제공 `Rfc2898DeriveBytes`.)

3. `KeyDerivation.Pbkdf2` 메서드에 필요한 모든 매개 변수를 지정 하려면 호출자 (솔트, PRF, 및 반복 횟수). `Rfc2898DeriveBytes` 유형 이들에 대 한 기본값을 제공 합니다.

[!code-csharp[Main](password-hashing/samples/passwordhasher.cs)]

ASP.NET Core Id에 대 한 소스 코드 참조 `PasswordHasher` 실제 환경에 대 한 입력 사례를 사용 합니다.
