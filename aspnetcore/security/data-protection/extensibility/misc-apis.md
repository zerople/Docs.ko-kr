---
title: "기타 API"
author: rick-anderson
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 512c6ba7-88ec-47e4-a656-6b30350b34e6
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/extensibility/misc-apis
ms.openlocfilehash: 72274a0da94a14bcd5af11d245ba626214fa2607
ms.sourcegitcommit: 8f4d4fad1ca27adf9e396f5c205c9875a3963664
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/13/2017
---
# <a name="miscellaneous-apis"></a><span data-ttu-id="027fd-103">기타 API</span><span class="sxs-lookup"><span data-stu-id="027fd-103">Miscellaneous APIs</span></span>

<a name="data-protection-extensibility-mics-apis"></a>

>[!WARNING]
> <span data-ttu-id="027fd-104">다음 인터페이스 중 하나를 구현 하는 형식은 스레드로부터 안전 해야 합니다. 여러 호출자에 대 한 합니다.</span><span class="sxs-lookup"><span data-stu-id="027fd-104">Types that implement any of the following interfaces should be thread-safe for multiple callers.</span></span>

## <a name="isecret"></a><span data-ttu-id="027fd-105">ISecret</span><span class="sxs-lookup"><span data-stu-id="027fd-105">ISecret</span></span>

<span data-ttu-id="027fd-106">ISecret 암호화 키 자료 같은 비밀 값을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="027fd-106">The ISecret interface represents a secret value, such as cryptographic key material.</span></span> <span data-ttu-id="027fd-107">다음 API 화면을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="027fd-107">It contains the following API surface.</span></span>

* <span data-ttu-id="027fd-108">길이: int</span><span class="sxs-lookup"><span data-stu-id="027fd-108">Length : int</span></span>

* <span data-ttu-id="027fd-109">Dispose (): void</span><span class="sxs-lookup"><span data-stu-id="027fd-109">Dispose() : void</span></span>

* <span data-ttu-id="027fd-110">WriteSecretIntoBuffer (ArraySegment<byte> 버퍼): void</span><span class="sxs-lookup"><span data-stu-id="027fd-110">WriteSecretIntoBuffer(ArraySegment<byte> buffer) : void</span></span>

<span data-ttu-id="027fd-111">WriteSecretIntoBuffer 메서드는 원시 암호 값을 사용 하 여 제공 된 버퍼를 채웁니다.</span><span class="sxs-lookup"><span data-stu-id="027fd-111">The WriteSecretIntoBuffer method populates the supplied buffer with the raw secret value.</span></span> <span data-ttu-id="027fd-112">이 API 변수로 버퍼는이 기회 호출자는 버퍼 개체를 고정 byte 직접는 반환 하지 않고 매개 변수는 관리 되는 가비지 수집기에 대 한 보안 노출을 제한 하는 이유입니다.</span><span class="sxs-lookup"><span data-stu-id="027fd-112">The reason this API takes the buffer as a parameter rather than returning a byte[] directly is that this gives the caller the opportunity to pin the buffer object, limiting secret exposure to the managed garbage collector.</span></span>

<span data-ttu-id="027fd-113">보안 형식은 처리 중인 메모리에 비밀 값 저장 된 ISecret의 구체적인 구현입니다.</span><span class="sxs-lookup"><span data-stu-id="027fd-113">The Secret type is a concrete implementation of ISecret where the secret value is stored in in-process memory.</span></span> <span data-ttu-id="027fd-114">Windows 플랫폼에서 암호 값이를 통해 암호화 [CryptProtectMemory](https://msdn.microsoft.com/library/windows/desktop/aa380262(v=vs.85).aspx)합니다.</span><span class="sxs-lookup"><span data-stu-id="027fd-114">On Windows platforms, the secret value is encrypted via [CryptProtectMemory](https://msdn.microsoft.com/library/windows/desktop/aa380262(v=vs.85).aspx).</span></span>
