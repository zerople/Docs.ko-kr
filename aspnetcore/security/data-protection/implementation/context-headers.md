---
title: "컨텍스트 헤더"
author: rick-anderson
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: d026a58c-67f4-411e-a410-c35f29c2c517
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/implementation/context-headers
ms.openlocfilehash: 5688ff2c36907231f88d45cef4ae1b1c60ab44ab
ms.sourcegitcommit: 67f54fabbfa4e3942f5bfe1f8a7fdfe4a7a75358
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/19/2017
---
# <a name="context-headers"></a><span data-ttu-id="7bbf4-103">컨텍스트 헤더</span><span class="sxs-lookup"><span data-stu-id="7bbf4-103">Context headers</span></span>

<a name=data-protection-implementation-context-headers></a>

## <a name="background-and-theory"></a><span data-ttu-id="7bbf4-104">배경 및 이론</span><span class="sxs-lookup"><span data-stu-id="7bbf4-104">Background and theory</span></span>

<span data-ttu-id="7bbf4-105">데이터 보호 시스템에 "키"를 제공할 수 있는 개체에는 암호화 서비스 인증을 의미 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bbf4-105">In the data protection system, a "key" means an object that can provide authenticated encryption services.</span></span> <span data-ttu-id="7bbf4-106">각 키는 고유 id (GUID)로 식별 되 고 함께 전달 알고리즘 정보 및 entropic 자료.</span><span class="sxs-lookup"><span data-stu-id="7bbf4-106">Each key is identified by a unique id (a GUID), and it carries with it algorithmic information and entropic material.</span></span> <span data-ttu-id="7bbf4-107">각 키 고유 엔트로피 하지만 시스템을 적용할 수 없는 운반 하 고 키 링의 기존 키의 알고리즘 정보를 수정 하 여 수동으로 키 링을 변경할 수 있는 개발자를 위한 계정 해야 것입니다.</span><span class="sxs-lookup"><span data-stu-id="7bbf4-107">It is intended that each key carry unique entropy, but the system cannot enforce that, and we also need to account for developers who might change the key ring manually by modifying the algorithmic information of an existing key in the key ring.</span></span> <span data-ttu-id="7bbf4-108">이러한 경우 지정 된 보안 요구 사항은 달성 하기 위해 데이터 보호 시스템에는의 개념이 [암호화 유연성](https://www.microsoft.com/en-us/research/publication/cryptographic-agility-and-its-relation-to-circular-encryption/), 안전 하 게 단일 entropic 값을 사용 하 여 여러 암호화 알고리즘에서 허용 하는 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bbf4-108">To achieve our security requirements given these cases the data protection system has a concept of [cryptographic agility](https://www.microsoft.com/en-us/research/publication/cryptographic-agility-and-its-relation-to-circular-encryption/), which allows securely using a single entropic value across multiple cryptographic algorithms.</span></span>

<span data-ttu-id="7bbf4-109">대부분의 암호화 유연성 지원 시스템 이렇게 페이로드 내부 알고리즘에 대 한 몇 가지 식별 정보가 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bbf4-109">Most systems which support cryptographic agility do so by including some identifying information about the algorithm inside the payload.</span></span> <span data-ttu-id="7bbf4-110">알고리즘의 OID는 일반적으로이 적합 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bbf4-110">The algorithm's OID is generally a good candidate for this.</span></span> <span data-ttu-id="7bbf4-111">그러나 발생 했습니다. 한 가지 문제는 동일한 알고리즘을 지정 하려면 여러 가지: "AES" (CNG) 관리 되는 Aes, AesManaged, AesCryptoServiceProvider, AesCng, 및 RijndaelManaged (부여 된 특정 매개 변수) 클래스는 모든 실제로 동일한 올바른 OID를 이러한 모든 내용의 매핑을 유지 관리 하 고 작업을 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bbf4-111">However, one problem that we ran into is that there are multiple ways to specify the same algorithm: "AES" (CNG) and the managed Aes, AesManaged, AesCryptoServiceProvider, AesCng, and RijndaelManaged (given specific parameters) classes are all actually the same thing, and we'd need to maintain a mapping of all of these to the correct OID.</span></span> <span data-ttu-id="7bbf4-112">개발자를 사용자 지정 알고리즘 (또는 AES의 다른 구현!)를 제공 하 려 해당 OID 인지 파악할 수 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bbf4-112">If a developer wanted to provide a custom algorithm (or even another implementation of AES!), they'd have to tell us its OID.</span></span> <span data-ttu-id="7bbf4-113">이 여분의 등록 단계를 수행 하면 시스템 구성 크다고 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bbf4-113">This extra registration step makes system configuration particularly painful.</span></span>

<span data-ttu-id="7bbf4-114">다시 실행, 잘못 된 방향에서 문제를 접근 된 म 결정 했습니다.</span><span class="sxs-lookup"><span data-stu-id="7bbf4-114">Stepping back, we decided that we were approaching the problem from the wrong direction.</span></span> <span data-ttu-id="7bbf4-115">OID 알려 알고리즘이 무엇 인지 하지만이에 실제로 우리는 크게 관심이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bbf4-115">An OID tells you what the algorithm is, but we don't actually care about this.</span></span> <span data-ttu-id="7bbf4-116">두 개의 서로 다른 알고리즘에는 단일 entropic 값을 안전 하 게 사용 해야 하는 경우 알고리즘은 실제로 알고 있어야 하기 위해 필요는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7bbf4-116">If we need to use a single entropic value securely in two different algorithms, it's not necessary for us to know what the algorithms actually are.</span></span> <span data-ttu-id="7bbf4-117">어떤 실제로 관심을 어떻게 동작 하는지입니다.</span><span class="sxs-lookup"><span data-stu-id="7bbf4-117">What we actually care about is how they behave.</span></span> <span data-ttu-id="7bbf4-118">모든 대칭 괜찮은 블록 암호화 알고리즘은 강력한 의사 난수 순열 (PRP)도: 입력 (체인 모드, IV, 일반 텍스트 키)를 수정 하 고 암호 텍스트 출력은 확률 과도 하 게 많아지지와 다에서를 수 다른 대칭 블록 암호 알고리즘은 동일한 입력입니다.</span><span class="sxs-lookup"><span data-stu-id="7bbf4-118">Any decent symmetric block cipher algorithm is also a strong pseudorandom permutation (PRP): fix the inputs (key, chaining mode, IV, plaintext) and the ciphertext output will with overwhelming probability be distinct from any other symmetric block cipher algorithm given the same inputs.</span></span> <span data-ttu-id="7bbf4-119">마찬가지로, 모든 괜찮은 키 지정된 해시 함수는 강력한 의사 난수 함수 (PRF) 되며 고정된 된 입력된 집합에 지정 된 출력 가져다 구분 됩니다 다른 키 지정된 해시 함수.</span><span class="sxs-lookup"><span data-stu-id="7bbf4-119">Similarly, any decent keyed hash function is also a strong pseudorandom function (PRF), and given a fixed input set its output will overwhelmingly be distinct from any other keyed hash function.</span></span>

<span data-ttu-id="7bbf4-120">컨텍스트 헤더를 구축할의 강력한 PRPs 및 PRFs이이 개념을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bbf4-120">We use this concept of strong PRPs and PRFs to build up a context header.</span></span> <span data-ttu-id="7bbf4-121">이 컨텍스트 헤더 기본적으로 역할 안정적인 지문 주어진된 작업에 사용 중인 알고리즘을 통해 하며 데이터 보호 시스템에 필요한 암호화 유연성을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bbf4-121">This context header essentially acts as a stable thumbprint over the algorithms in use for any given operation, and it provides the cryptographic agility needed by the data protection system.</span></span> <span data-ttu-id="7bbf4-122">이 헤더를 재현할 수 이며의 일환으로 나중에 사용 되는 [파생 프로세스를 하위 키](subkeyderivation.md#data-protection-implementation-subkey-derivation)합니다.</span><span class="sxs-lookup"><span data-stu-id="7bbf4-122">This header is reproducible and is used later as part of the [subkey derivation process](subkeyderivation.md#data-protection-implementation-subkey-derivation).</span></span> <span data-ttu-id="7bbf4-123">기본 알고리즘의 작업 모드에 따라 컨텍스트 헤더를 작성 하는 두 가지 방법으로 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bbf4-123">There are two different ways to build the context header depending on the modes of operation of the underlying algorithms.</span></span>

## <a name="cbc-mode-encryption--hmac-authentication"></a><span data-ttu-id="7bbf4-124">CBC 모드 암호화 + HMAC 인증</span><span class="sxs-lookup"><span data-stu-id="7bbf4-124">CBC-mode encryption + HMAC authentication</span></span>

<a name=data-protection-implementation-context-headers-cbc-components></a>

<span data-ttu-id="7bbf4-125">컨텍스트 헤더는 다음 구성 요소가 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bbf4-125">The context header consists of the following components:</span></span>

* <span data-ttu-id="7bbf4-126">[16 비트] 값 00 00 표식 "CBC 암호화 + HMAC 인증"을 의미 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bbf4-126">[16 bits] The value 00 00, which is a marker meaning "CBC encryption + HMAC authentication".</span></span>

* <span data-ttu-id="7bbf4-127">[32 비트] 키 길이 (바이트, big endian) 대칭 블록 암호화 알고리즘입니다.</span><span class="sxs-lookup"><span data-stu-id="7bbf4-127">[32 bits] The key length (in bytes, big-endian) of the symmetric block cipher algorithm.</span></span>

* <span data-ttu-id="7bbf4-128">[32 비트] 블록 크기 (바이트, big endian) 대칭 블록 암호화 알고리즘의 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bbf4-128">[32 bits] The block size (in bytes, big-endian) of the symmetric block cipher algorithm.</span></span>

* <span data-ttu-id="7bbf4-129">[32 비트] 키 길이 (바이트, big endian) HMAC 알고리즘의 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bbf4-129">[32 bits] The key length (in bytes, big-endian) of the HMAC algorithm.</span></span> <span data-ttu-id="7bbf4-130">(현재 키 크기는 항상 크기와 일치 다이제스트.)</span><span class="sxs-lookup"><span data-stu-id="7bbf4-130">(Currently the key size always matches the digest size.)</span></span>

* <span data-ttu-id="7bbf4-131">[32 비트] 다이제스트의 크기 (바이트, big endian) HMAC 알고리즘입니다.</span><span class="sxs-lookup"><span data-stu-id="7bbf4-131">[32 bits] The digest size (in bytes, big-endian) of the HMAC algorithm.</span></span>

* <span data-ttu-id="7bbf4-132">EncCBC (K_E, IV, ""), 지정 된 빈 문자열 입력 블록 대칭 암호화 알고리즘의 출력 인 및 IV가 모두 0 인 벡터입니다.</span><span class="sxs-lookup"><span data-stu-id="7bbf4-132">EncCBC(K_E, IV, ""), which is the output of the symmetric block cipher algorithm given an empty string input and where IV is an all-zero vector.</span></span> <span data-ttu-id="7bbf4-133">K_E 생성에 대 한 설명은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="7bbf4-133">The construction of K_E is described below.</span></span>

* <span data-ttu-id="7bbf4-134">MAC (K_H, ""), 지정 된 빈 문자열 입력 HMAC 알고리즘의 출력은입니다.</span><span class="sxs-lookup"><span data-stu-id="7bbf4-134">MAC(K_H, ""), which is the output of the HMAC algorithm given an empty string input.</span></span> <span data-ttu-id="7bbf4-135">K_H 생성에 대 한 설명은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="7bbf4-135">The construction of K_H is described below.</span></span>

<span data-ttu-id="7bbf4-136">이상적으로 모두 0 인 벡터 K_E 및 K_H 전달 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7bbf4-136">Ideally we could pass all-zero vectors for K_E and K_H.</span></span> <span data-ttu-id="7bbf4-137">그러나 여기서 기본 알고리즘은 있는지 여부를 확인 weak 키 (특히 DES 및 3DES) 작업을 수행 하기 전에 모두 0 인 벡터 같은 단순 또는 반복 가능한 패턴을 사용 하 여을 배제 하는 상황을 방지 하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bbf4-137">However, we want to avoid the situation where the underlying algorithm checks for the existence of weak keys before performing any operations (notably DES and 3DES), which precludes using a simple or repeatable pattern like an all-zero vector.</span></span>

<span data-ttu-id="7bbf4-138">대신, NIST SP800 108 KDF 카운터 모드로 사용 (참조 [NIST SP800-108](http://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-108.pdf), 초 5.1) 된 길이가 0 인 키, 레이블 및 컨텍스트 및 기본 PRF로 HMACSHA512 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bbf4-138">Instead, we use the NIST SP800-108 KDF in Counter Mode (see [NIST SP800-108](http://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-108.pdf), Sec. 5.1) with a zero-length key, label, and context and HMACSHA512 as the underlying PRF.</span></span> <span data-ttu-id="7bbf4-139">개체를 파생할 | K_E | + | K_H | 바이트의 출력을 다음 분해 결과 K_E 및 K_H 자체입니다.</span><span class="sxs-lookup"><span data-stu-id="7bbf4-139">We derive | K_E | + | K_H | bytes of output, then decompose the result into K_E and K_H themselves.</span></span> <span data-ttu-id="7bbf4-140">수학적으로 다음과 같이 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bbf4-140">Mathematically, this is represented as follows.</span></span>

<span data-ttu-id="7bbf4-141">(K_E | | K_H) SP800_108_CTR = (prf HMACSHA512 = 키 = ""을 label = "", 컨텍스트 = "")</span><span class="sxs-lookup"><span data-stu-id="7bbf4-141">( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")</span></span>

### <a name="example-aes-192-cbc--hmacsha256"></a><span data-ttu-id="7bbf4-142">예: AES-192-CBC + HMACSHA256</span><span class="sxs-lookup"><span data-stu-id="7bbf4-142">Example: AES-192-CBC + HMACSHA256</span></span>

<span data-ttu-id="7bbf4-143">예를 들어 블록 대칭 암호화 알고리즘은 AES-192-CBC 유효성 검사 알고리즘은 HMACSHA256 경우 고려해 야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bbf4-143">As an example, consider the case where the symmetric block cipher algorithm is AES-192-CBC and the validation algorithm is HMACSHA256.</span></span> <span data-ttu-id="7bbf4-144">시스템은 다음 단계를 사용 하 여 컨텍스트 헤더를 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bbf4-144">The system would generate the context header using the following steps.</span></span>

<span data-ttu-id="7bbf4-145">먼저, (K_E | | K_H) SP800_108_CTR = (prf HMACSHA512 = key = "", 레이블 = "", 컨텍스트 = ""), 여기서 | K_E | = 192 비트 및 | K_H | = 지정 된 알고리즘 당 256 비트입니다.</span><span class="sxs-lookup"><span data-stu-id="7bbf4-145">First, let ( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = ""), where | K_E | = 192 bits and | K_H | = 256 bits per the specified algorithms.</span></span> <span data-ttu-id="7bbf4-146">그러면 K_E 5BB6 =... 21DD 및 K_H A04A =... 아래 예제에서는 00A9:</span><span class="sxs-lookup"><span data-stu-id="7bbf4-146">This leads to K_E = 5BB6..21DD and K_H = A04A..00A9 in the example below:</span></span>

<!-- literal_block {"ids": [], "xml:space": "preserve"} -->

```
5B B6 C9 83 13 78 22 1D 8E 10 73 CA CF 65 8E B0
   61 62 42 71 CB 83 21 DD A0 4A 05 00 5B AB C0 A2
   49 6F A5 61 E3 E2 49 87 AA 63 55 CD 74 0A DA C4
   B7 92 3D BF 59 90 00 A9
   ```

<span data-ttu-id="7bbf4-147">다음으로, Enc_CBC 계산 (K_E, IV, "") AES-192-CBC IV 제공 = 0 * 및 위와 같은 K_E 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bbf4-147">Next, compute Enc_CBC (K_E, IV, "") for AES-192-CBC given IV = 0* and K_E as above.</span></span>

<span data-ttu-id="7bbf4-148">결과: F474B1872B3B53E4721DE19C0841DB6F =</span><span class="sxs-lookup"><span data-stu-id="7bbf4-148">result := F474B1872B3B53E4721DE19C0841DB6F</span></span>

<span data-ttu-id="7bbf4-149">다음으로 MAC을 계산 (K_H, "") HMACSHA256 위와 같은 K_H 제공에 대 한 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bbf4-149">Next, compute MAC(K_H, "") for HMACSHA256 given K_H as above.</span></span>

<span data-ttu-id="7bbf4-150">결과: D4791184B996092EE1202F36E8608FA8FBD98ABDFF5402F264B1D7211536220C =</span><span class="sxs-lookup"><span data-stu-id="7bbf4-150">result := D4791184B996092EE1202F36E8608FA8FBD98ABDFF5402F264B1D7211536220C</span></span>

<span data-ttu-id="7bbf4-151">아래 전체 컨텍스트 헤더를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="7bbf4-151">This produces the full context header below:</span></span>

<!-- literal_block {"ids": [], "xml:space": "preserve"} -->

```
00 00 00 00 00 18 00 00 00 10 00 00 00 20 00 00
   00 20 F4 74 B1 87 2B 3B 53 E4 72 1D E1 9C 08 41
   DB 6F D4 79 11 84 B9 96 09 2E E1 20 2F 36 E8 60
   8F A8 FB D9 8A BD FF 54 02 F2 64 B1 D7 21 15 36
   22 0C
   ```

<span data-ttu-id="7bbf4-152">이 컨텍스트 헤더에는 인증 된 암호화 알고리즘 쌍 (예: AES-192-CBC 암호화 + HMACSHA256 유효성 검사)의 지문입니다.</span><span class="sxs-lookup"><span data-stu-id="7bbf4-152">This context header is the thumbprint of the authenticated encryption algorithm pair (AES-192-CBC encryption + HMACSHA256 validation).</span></span> <span data-ttu-id="7bbf4-153">설명 된 대로 구성 요소 [위에](xref:security/data-protection/implementation/context-headers#data-protection-implementation-context-headers-cbc-components) 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bbf4-153">The components, as described [above](xref:security/data-protection/implementation/context-headers#data-protection-implementation-context-headers-cbc-components) are:</span></span>

* <span data-ttu-id="7bbf4-154">표식의 (00 00)</span><span class="sxs-lookup"><span data-stu-id="7bbf4-154">the marker (00 00)</span></span>

* <span data-ttu-id="7bbf4-155">블록 암호화 키 길이 (00 00 00 18)</span><span class="sxs-lookup"><span data-stu-id="7bbf4-155">the block cipher key length (00 00 00 18)</span></span>

* <span data-ttu-id="7bbf4-156">블록 암호화 블록 크기 (00 00 00 10)</span><span class="sxs-lookup"><span data-stu-id="7bbf4-156">the block cipher block size (00 00 00 10)</span></span>

* <span data-ttu-id="7bbf4-157">HMAC 키 길이 (00 00 00 20)</span><span class="sxs-lookup"><span data-stu-id="7bbf4-157">the HMAC key length (00 00 00 20)</span></span>

* <span data-ttu-id="7bbf4-158">HMAC 다이제스트 크기 (00 00 00 20)</span><span class="sxs-lookup"><span data-stu-id="7bbf4-158">the HMAC digest size (00 00 00 20)</span></span>

* <span data-ttu-id="7bbf4-159">블록 암호 PRP 출력 (F4 74-DB 6F) 및</span><span class="sxs-lookup"><span data-stu-id="7bbf4-159">the block cipher PRP output (F4 74 - DB 6F) and</span></span>

* <span data-ttu-id="7bbf4-160">HMAC PRF 출력 (D4 79-끝).</span><span class="sxs-lookup"><span data-stu-id="7bbf4-160">the HMAC PRF output (D4 79 - end).</span></span>

> [!NOTE]
> <span data-ttu-id="7bbf4-161">CBC 모드 암호화 + HMAC 인증 컨텍스트 헤더 SymmetricAlgorithm 및 KeyedHashAlgorithm 관리 되는 형식 또는 Windows CNG에서 알고리즘 구현에서 제공 하는 여부에 관계 없이 동일한 방식으로 빌드됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bbf4-161">The CBC-mode encryption + HMAC authentication context header is built the same way regardless of whether the algorithms implementations are provided by Windows CNG or by managed SymmetricAlgorithm and KeyedHashAlgorithm types.</span></span> <span data-ttu-id="7bbf4-162">서로 다른 운영 체제에서 실행 하지 않도록 할 수는 알고리즘의 구현 Os 간에 다를 경우에 안정적으로 동일한 컨텍스트 헤더를 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bbf4-162">This allows applications running on different operating systems to reliably produce the same context header even though the implementations of the algorithms differ between OSes.</span></span> <span data-ttu-id="7bbf4-163">(실제로 KeyedHashAlgorithm 아니어도 적절 한 HMAC입니다.</span><span class="sxs-lookup"><span data-stu-id="7bbf4-163">(In practice, the KeyedHashAlgorithm doesn't have to be a proper HMAC.</span></span> <span data-ttu-id="7bbf4-164">수 있는 키 지정된 해시 알고리즘 유형입니다.)</span><span class="sxs-lookup"><span data-stu-id="7bbf4-164">It can be any keyed hash algorithm type.)</span></span>

### <a name="example-3des-192-cbc--hmacsha1"></a><span data-ttu-id="7bbf4-165">예: 3DES-192-CBC + HMACSHA1</span><span class="sxs-lookup"><span data-stu-id="7bbf4-165">Example: 3DES-192-CBC + HMACSHA1</span></span>

<span data-ttu-id="7bbf4-166">먼저, (K_E | | K_H) SP800_108_CTR = (prf HMACSHA512 = key = "", 레이블 = "", 컨텍스트 = ""), 여기서 | K_E | = 192 비트 및 | K_H | = 지정 된 알고리즘 당 160 비트입니다.</span><span class="sxs-lookup"><span data-stu-id="7bbf4-166">First, let ( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = ""), where | K_E | = 192 bits and | K_H | = 160 bits per the specified algorithms.</span></span> <span data-ttu-id="7bbf4-167">그러면 K_E A219 =... E2BB 및 K_H DC4A =... 아래 예제에서는 B464:</span><span class="sxs-lookup"><span data-stu-id="7bbf4-167">This leads to K_E = A219..E2BB and K_H = DC4A..B464 in the example below:</span></span>

<!-- literal_block {"ids": [], "xml:space": "preserve"} -->

```
A2 19 60 2F 83 A9 13 EA B0 61 3A 39 B8 A6 7E 22
   61 D9 F8 6C 10 51 E2 BB DC 4A 00 D7 03 A2 48 3E
   D1 F7 5A 34 EB 28 3E D7 D4 67 B4 64
   ```

<span data-ttu-id="7bbf4-168">다음으로, Enc_CBC 계산 (K_E, IV, "") 3DES-192-CBC IV 제공 = 0 * 및 위와 같은 K_E 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bbf4-168">Next, compute Enc_CBC (K_E, IV, "") for 3DES-192-CBC given IV = 0* and K_E as above.</span></span>

<span data-ttu-id="7bbf4-169">결과: ABB100F81E53E10E =</span><span class="sxs-lookup"><span data-stu-id="7bbf4-169">result := ABB100F81E53E10E</span></span>

<span data-ttu-id="7bbf4-170">다음으로 MAC을 계산 (K_H, "") HMACSHA1 위와 같은 K_H 제공에 대 한 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bbf4-170">Next, compute MAC(K_H, "") for HMACSHA1 given K_H as above.</span></span>

<span data-ttu-id="7bbf4-171">결과: 76EB189B35CF03461DDF877CD9F4B1B4D63A7555 =</span><span class="sxs-lookup"><span data-stu-id="7bbf4-171">result := 76EB189B35CF03461DDF877CD9F4B1B4D63A7555</span></span>

<span data-ttu-id="7bbf4-172">이렇게 하면 인증 된 사용자의 지문 인 전체 컨텍스트 헤더 생성 아래에 표시 된 암호화 알고리즘 쌍 (3DES-192-CBC 암호화 + HMACSHA1 유효성 검사):</span><span class="sxs-lookup"><span data-stu-id="7bbf4-172">This produces the full context header which is a thumbprint of the authenticated encryption algorithm pair (3DES-192-CBC encryption + HMACSHA1 validation), shown below:</span></span>

<!-- literal_block {"ids": [], "xml:space": "preserve"} -->

```
00 00 00 00 00 18 00 00 00 08 00 00 00 14 00 00
   00 14 AB B1 00 F8 1E 53 E1 0E 76 EB 18 9B 35 CF
   03 46 1D DF 87 7C D9 F4 B1 B4 D6 3A 75 55
   ```

<span data-ttu-id="7bbf4-173">구성 요소는 다음과 같이 구분:</span><span class="sxs-lookup"><span data-stu-id="7bbf4-173">The components break down as follows:</span></span>

* <span data-ttu-id="7bbf4-174">표식의 (00 00)</span><span class="sxs-lookup"><span data-stu-id="7bbf4-174">the marker (00 00)</span></span>

* <span data-ttu-id="7bbf4-175">블록 암호화 키 길이 (00 00 00 18)</span><span class="sxs-lookup"><span data-stu-id="7bbf4-175">the block cipher key length (00 00 00 18)</span></span>

* <span data-ttu-id="7bbf4-176">블록 암호화 블록 크기 (00 00 00 08)</span><span class="sxs-lookup"><span data-stu-id="7bbf4-176">the block cipher block size (00 00 00 08)</span></span>

* <span data-ttu-id="7bbf4-177">HMAC 키 길이 (00 00 00 14)</span><span class="sxs-lookup"><span data-stu-id="7bbf4-177">the HMAC key length (00 00 00 14)</span></span>

* <span data-ttu-id="7bbf4-178">HMAC 다이제스트 크기 (00 00 00 14)</span><span class="sxs-lookup"><span data-stu-id="7bbf4-178">the HMAC digest size (00 00 00 14)</span></span>

* <span data-ttu-id="7bbf4-179">블록 암호 PRP 출력 (AB B1-E1 0E) 및</span><span class="sxs-lookup"><span data-stu-id="7bbf4-179">the block cipher PRP output (AB B1 - E1 0E) and</span></span>

* <span data-ttu-id="7bbf4-180">HMAC PRF 출력 (-76 EB 끝).</span><span class="sxs-lookup"><span data-stu-id="7bbf4-180">the HMAC PRF output (76 EB - end).</span></span>

## <a name="galoiscounter-mode-encryption--authentication"></a><span data-ttu-id="7bbf4-181">Galois/카운터 모드 암호화 + 인증</span><span class="sxs-lookup"><span data-stu-id="7bbf4-181">Galois/Counter Mode encryption + authentication</span></span>

<span data-ttu-id="7bbf4-182">컨텍스트 헤더는 다음 구성 요소가 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bbf4-182">The context header consists of the following components:</span></span>

* <span data-ttu-id="7bbf4-183">[16 비트] 00 값 01 표식 "GCM 암호화 + 인증"을 의미 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bbf4-183">[16 bits] The value 00 01, which is a marker meaning "GCM encryption + authentication".</span></span>

* <span data-ttu-id="7bbf4-184">[32 비트] 키 길이 (바이트, big endian) 대칭 블록 암호화 알고리즘입니다.</span><span class="sxs-lookup"><span data-stu-id="7bbf4-184">[32 bits] The key length (in bytes, big-endian) of the symmetric block cipher algorithm.</span></span>

* <span data-ttu-id="7bbf4-185">[32 비트] nonce 크기 (바이트, big endian) 인증 된 암호화 작업 중에 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bbf4-185">[32 bits] The nonce size (in bytes, big-endian) used during authenticated encryption operations.</span></span> <span data-ttu-id="7bbf4-186">(, 우리의 시스템에 대 한 nonce 크기로 고정 되어이 96 비트 =.)</span><span class="sxs-lookup"><span data-stu-id="7bbf4-186">(For our system, this is fixed at nonce size = 96 bits.)</span></span>

* <span data-ttu-id="7bbf4-187">[32 비트] 블록 크기 (바이트, big endian) 대칭 블록 암호화 알고리즘의 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bbf4-187">[32 bits] The block size (in bytes, big-endian) of the symmetric block cipher algorithm.</span></span> <span data-ttu-id="7bbf4-188">(GCM에 대 한 블록 크기에 고정 됩니다이 128 비트입니다.)</span><span class="sxs-lookup"><span data-stu-id="7bbf4-188">(For GCM, this is fixed at block size = 128 bits.)</span></span>

* <span data-ttu-id="7bbf4-189">[32 비트] 인증 태그 크기 (바이트, big endian) 인증 된 암호화 기능에서 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bbf4-189">[32 bits] The authentication tag size (in bytes, big-endian) produced by the authenticated encryption function.</span></span> <span data-ttu-id="7bbf4-190">(, 우리의 시스템에 대 한 태그 크기로 고정 되어이 128 비트입니다.)</span><span class="sxs-lookup"><span data-stu-id="7bbf4-190">(For our system, this is fixed at tag size = 128 bits.)</span></span>

* <span data-ttu-id="7bbf4-191">[128 비트] Enc_GCM 태그 (nonce, K_E ""), 지정 된 빈 문자열 입력 블록 대칭 암호화 알고리즘의 출력 인 및 nonce는 96 비트 모두 0 인 벡터입니다.</span><span class="sxs-lookup"><span data-stu-id="7bbf4-191">[128 bits] The tag of Enc_GCM (K_E, nonce, ""), which is the output of the symmetric block cipher algorithm given an empty string input and where nonce is a 96-bit all-zero vector.</span></span>

<span data-ttu-id="7bbf4-192">K_E는 CBC 암호화 + HMAC 인증 시나리오에서와 같이 동일한 메커니즘을 사용 하 여 파생 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bbf4-192">K_E is derived using the same mechanism as in the CBC encryption + HMAC authentication scenario.</span></span> <span data-ttu-id="7bbf4-193">그러나 플레이 여기에 없는 K_H 이므로 기본적으로 있는 | K_H | = 0, 알고리즘을 축소 하 고는 아래 폼입니다.</span><span class="sxs-lookup"><span data-stu-id="7bbf4-193">However, since there is no K_H in play here, we essentially have | K_H | = 0, and the algorithm collapses to the below form.</span></span>

<span data-ttu-id="7bbf4-194">K_E SP800_108_CTR = (prf HMACSHA512 = 키 = ""을 label = "", 컨텍스트 = "")</span><span class="sxs-lookup"><span data-stu-id="7bbf4-194">K_E = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")</span></span>

### <a name="example-aes-256-gcm"></a><span data-ttu-id="7bbf4-195">예: AES 256 GCM</span><span class="sxs-lookup"><span data-stu-id="7bbf4-195">Example: AES-256-GCM</span></span>

<span data-ttu-id="7bbf4-196">첫째, K_E 수 있도록 SP800_108_CTR = (prf HMACSHA512 = 키 = "", 레이블 = "", 컨텍스트 = ""), 여기서 | K_E | = 256 비트입니다.</span><span class="sxs-lookup"><span data-stu-id="7bbf4-196">First, let K_E = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = ""), where | K_E | = 256 bits.</span></span>

<span data-ttu-id="7bbf4-197">K_E: 22BC6F1B171C08C4AE2F27444AF8FC8B3087A90006CAEA91FDCFB47C1B8733B8 =</span><span class="sxs-lookup"><span data-stu-id="7bbf4-197">K_E := 22BC6F1B171C08C4AE2F27444AF8FC8B3087A90006CAEA91FDCFB47C1B8733B8</span></span>

<span data-ttu-id="7bbf4-198">다음으로 Enc_GCM의 인증 태그가 계산 (K_E, nonce, "") AES-256-GCM nonce 제공 = 096 및 K_E 위와 같이 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bbf4-198">Next, compute the authentication tag of Enc_GCM (K_E, nonce, "") for AES-256-GCM given nonce = 096 and K_E as above.</span></span>

<span data-ttu-id="7bbf4-199">결과: E7DCCE66DF855A323A6BB7BD7A59BE45 =</span><span class="sxs-lookup"><span data-stu-id="7bbf4-199">result := E7DCCE66DF855A323A6BB7BD7A59BE45</span></span>

<span data-ttu-id="7bbf4-200">아래 전체 컨텍스트 헤더를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="7bbf4-200">This produces the full context header below:</span></span>

<!-- literal_block {"ids": [], "xml:space": "preserve"} -->

```
00 01 00 00 00 20 00 00 00 0C 00 00 00 10 00 00
   00 10 E7 DC CE 66 DF 85 5A 32 3A 6B B7 BD 7A 59
   BE 45
   ```

<span data-ttu-id="7bbf4-201">구성 요소는 다음과 같이 구분:</span><span class="sxs-lookup"><span data-stu-id="7bbf4-201">The components break down as follows:</span></span>

   * <span data-ttu-id="7bbf4-202">표식의 (00 01)</span><span class="sxs-lookup"><span data-stu-id="7bbf4-202">the marker (00 01)</span></span>

   * <span data-ttu-id="7bbf4-203">블록 암호화 키 길이 (00 00 00 20)</span><span class="sxs-lookup"><span data-stu-id="7bbf4-203">the block cipher key length (00 00 00 20)</span></span>

   * <span data-ttu-id="7bbf4-204">nonce 크기 (00 00 00 0 C)</span><span class="sxs-lookup"><span data-stu-id="7bbf4-204">the nonce size (00 00 00 0C)</span></span>

   * <span data-ttu-id="7bbf4-205">블록 암호화 블록 크기 (00 00 00 10)</span><span class="sxs-lookup"><span data-stu-id="7bbf4-205">the block cipher block size (00 00 00 10)</span></span>

   * <span data-ttu-id="7bbf4-206">인증 태그 크기 (00 00 00 10) 및</span><span class="sxs-lookup"><span data-stu-id="7bbf4-206">the authentication tag size (00 00 00 10) and</span></span>

   * <span data-ttu-id="7bbf4-207">실행 블록 암호 인증 태그가 (E7 DC-끝).</span><span class="sxs-lookup"><span data-stu-id="7bbf4-207">the authentication tag from running the block cipher (E7 DC - end).</span></span>
