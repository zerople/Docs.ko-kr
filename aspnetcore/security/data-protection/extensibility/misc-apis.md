---
title: "기타 API"
author: rick-anderson
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 512c6ba7-88ec-47e4-a656-6b30350b34e6
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/extensibility/misc-apis
ms.openlocfilehash: 541dd721a00495632f0d633577b55933c9be03fa
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/11/2017
---
# <a name="miscellaneous-apis"></a>기타 API

<a name=data-protection-extensibility-mics-apis></a>

>[!WARNING]
> 다음 인터페이스 중 하나를 구현 하는 형식은 스레드로부터 안전 해야 합니다. 여러 호출자에 대 한 합니다.

## <a name="isecret"></a>ISecret

ISecret 암호화 키 자료 같은 비밀 값을 나타냅니다. 다음 API 화면을 포함합니다.

* 길이: int

* Dispose (): void

* WriteSecretIntoBuffer (ArraySegment<byte> 버퍼): void

WriteSecretIntoBuffer 메서드는 원시 암호 값을 사용 하 여 제공 된 버퍼를 채웁니다. 이 API 변수로 버퍼는이 기회 호출자는 버퍼 개체를 고정 byte 직접는 반환 하지 않고 매개 변수는 관리 되는 가비지 수집기에 대 한 보안 노출을 제한 하는 이유입니다.

보안 형식은 처리 중인 메모리에 비밀 값 저장 된 ISecret의 구체적인 구현입니다. Windows 플랫폼에서 암호 값이를 통해 암호화 [CryptProtectMemory](https://msdn.microsoft.com/library/windows/desktop/aa380262(v=vs.85).aspx)합니다.
