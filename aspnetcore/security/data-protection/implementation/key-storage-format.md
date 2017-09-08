---
title: "키 저장소 형식"
author: tdykstra
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: e8996478-f7bf-4b58-bab4-7fdb5d8556c5
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/implementation/key-storage-format
ms.openlocfilehash: e761eaa406a9691e3fa36881d42c1a0c1bd8a206
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/11/2017
---
# <a name="key-storage-format"></a><span data-ttu-id="6d927-103">키 저장소 형식</span><span class="sxs-lookup"><span data-stu-id="6d927-103">Key Storage Format</span></span>

<a name=data-protection-implementation-key-storage-format></a>

<span data-ttu-id="6d927-104">개체는 XML 표현에 저장 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6d927-104">Objects are stored at rest in XML representation.</span></span> <span data-ttu-id="6d927-105">키 저장소에 대 한 기본 디렉터리는 %LOCALAPPDATA%\ASP.NET\DataProtection-Keys\ 합니다.</span><span class="sxs-lookup"><span data-stu-id="6d927-105">The default directory for key storage is %LOCALAPPDATA%\ASP.NET\DataProtection-Keys\.</span></span>

## <a name="the-key-element"></a><span data-ttu-id="6d927-106">\<키 > 요소</span><span class="sxs-lookup"><span data-stu-id="6d927-106">The \<key> element</span></span>

<span data-ttu-id="6d927-107">키의 키 저장소에는 최상위 개체도 존재합니다.</span><span class="sxs-lookup"><span data-stu-id="6d927-107">Keys exist as top-level objects in the key repository.</span></span> <span data-ttu-id="6d927-108">키 파일 이름에는 규칙에 따라 **키-{guid}.xml**, 여기서 {guid}이 키의 id입니다.</span><span class="sxs-lookup"><span data-stu-id="6d927-108">By convention keys have the filename **key-{guid}.xml**, where {guid} is the id of the key.</span></span> <span data-ttu-id="6d927-109">이러한 각 파일에는 단일 키를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="6d927-109">Each such file contains a single key.</span></span> <span data-ttu-id="6d927-110">파일의 형식은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="6d927-110">The format of the file is as follows.</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<key id="80732141-ec8f-4b80-af9c-c4d2d1ff8901" version="1">
  <creationDate>2015-03-19T23:32:02.3949887Z</creationDate>
  <activationDate>2015-03-19T23:32:02.3839429Z</activationDate>
  <expirationDate>2015-06-17T23:32:02.3839429Z</expirationDate>
  <descriptor deserializerType="{deserializerType}">
    <descriptor>
      <encryption algorithm="AES_256_CBC" />
      <validation algorithm="HMACSHA256" />
      <enc:encryptedSecret decryptorType="{decryptorType}" xmlns:enc="...">
        <encryptedKey>
          <!-- This key is encrypted with Windows DPAPI. -->
          <value>AQAAANCM...8/zeP8lcwAg==</value>
        </encryptedKey>
      </enc:encryptedSecret>
    </descriptor>
  </descriptor>
</key>
```

<span data-ttu-id="6d927-111">\<키 > 요소는 다음과 같은 특성 및 자식 요소를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="6d927-111">The \<key> element contains the following attributes and child elements:</span></span>

* <span data-ttu-id="6d927-112">키 id입니다.</span><span class="sxs-lookup"><span data-stu-id="6d927-112">The key id.</span></span> <span data-ttu-id="6d927-113">이 값은 정식으로; 처리 파일 이름이 휴먼 가독성을 높이기 위해 못했던 단순히입니다.</span><span class="sxs-lookup"><span data-stu-id="6d927-113">This value is treated as authoritative; the filename is simply a nicety for human readability.</span></span>

* <span data-ttu-id="6d927-114">버전의는 \<키 > 요소를 1에서 현재 고정 합니다.</span><span class="sxs-lookup"><span data-stu-id="6d927-114">The version of the \<key> element, currently fixed at 1.</span></span>

* <span data-ttu-id="6d927-115">키의 생성, 활성화, 및 만료 날짜입니다.</span><span class="sxs-lookup"><span data-stu-id="6d927-115">The key's creation, activation, and expiration dates.</span></span>

* <span data-ttu-id="6d927-116">A \<설명자 >이 키에 포함 된 인증 된 암호화 구현에 대 한 정보를 포함 하는 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="6d927-116">A \<descriptor> element, which contains information on the authenticated encryption implementation contained within this key.</span></span>

<span data-ttu-id="6d927-117">위의 예제에서는 키의 id가 {80732141-ec8f-4b80-af9c-c4d2d1ff8901}, 만들어지고 2015 년 3 월 19 일에 활성화 된 및 90 일의 수명은 합니다.</span><span class="sxs-lookup"><span data-stu-id="6d927-117">In the above example, the key's id is {80732141-ec8f-4b80-af9c-c4d2d1ff8901}, it was created and activated on March 19, 2015, and it has a lifetime of 90 days.</span></span> <span data-ttu-id="6d927-118">(경우에 따라 활성화 날짜 약간 있을 수 있으며 다음이 예제와 같이 작성 날짜 전에 합니다.</span><span class="sxs-lookup"><span data-stu-id="6d927-118">(Occasionally the activation date might be slightly before the creation date as in this example.</span></span> <span data-ttu-id="6d927-119">이것은 Api 작동 하 고는 무시 해도 실제로 하는 방식에서 nit 인해.)</span><span class="sxs-lookup"><span data-stu-id="6d927-119">This is due to a nit in how the APIs work and is harmless in practice.)</span></span>

## <a name="the-descriptor-element"></a><span data-ttu-id="6d927-120">\<설명자 > 요소</span><span class="sxs-lookup"><span data-stu-id="6d927-120">The \<descriptor> element</span></span>

<span data-ttu-id="6d927-121">외부 \<설명자 > 요소는이 IAuthenticatedEncryptorDescriptorDeserializer를 구현 하는 형식의 정규화 된 어셈블리 이름을 특성 deserializerType 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="6d927-121">The outer \<descriptor> element contains an attribute deserializerType, which is the assembly-qualified name of a type which implements IAuthenticatedEncryptorDescriptorDeserializer.</span></span> <span data-ttu-id="6d927-122">이 형식은 내부 읽는 역할 \<설명자 > 요소 내에 포함 된 정보를 구문 분석 하 고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6d927-122">This type is responsible for reading the inner \<descriptor> element and for parsing the information contained within.</span></span>

<span data-ttu-id="6d927-123">특정 형식의 \<설명자 > 요소는 키에 의해 캡슐화 된 인증 된 암호기 구현에 따라 및 각 deserializer 유형에 대해이 약간 다른 형식을 필요로 합니다.</span><span class="sxs-lookup"><span data-stu-id="6d927-123">The particular format of the \<descriptor> element depends on the authenticated encryptor implementation encapsulated by the key, and each deserializer type expects a slightly different format for this.</span></span> <span data-ttu-id="6d927-124">일반적으로 하지만,이 요소를 포함 합니다 알고리즘 정보 (이름, 형식, Oid를 또는 유사한 곳) 및 비밀 키 자료.</span><span class="sxs-lookup"><span data-stu-id="6d927-124">In general, though, this element will contain algorithmic information (names, types, OIDs, or similar) and secret key material.</span></span> <span data-ttu-id="6d927-125">위의 예에서 설명자가이 키는 AES-256-CBC 암호화 + HMACSHA256 유효성 검사를 래핑하는 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="6d927-125">In the above example, the descriptor specifies that this key wraps AES-256-CBC encryption + HMACSHA256 validation.</span></span>

## <a name="the-encryptedsecret-element"></a><span data-ttu-id="6d927-126">\<encryptedSecret > 요소</span><span class="sxs-lookup"><span data-stu-id="6d927-126">The \<encryptedSecret> element</span></span>

<span data-ttu-id="6d927-127"><encryptedSecret> 비밀 키 자료의 암호화 된 형태를 포함 하는 요소 있을 수 경우 [비밀 미사용 데이터 암호화가 설정](key-encryption-at-rest.md#data-protection-implementation-key-encryption-at-rest)합니다.</span><span class="sxs-lookup"><span data-stu-id="6d927-127">An <encryptedSecret> element which contains the encrypted form of the secret key material may be present if [encryption of secrets at rest is enabled](key-encryption-at-rest.md#data-protection-implementation-key-encryption-at-rest).</span></span> <span data-ttu-id="6d927-128">특성 decryptorType IXmlDecryptor를 구현 하는 형식의 정규화 된 어셈블리 이름이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6d927-128">The attribute decryptorType will be the assembly-qualified name of a type which implements IXmlDecryptor.</span></span> <span data-ttu-id="6d927-129">이 형식은 내부 읽는 역할 <encryptedKey> 요소와 복구를 원래의 일반 텍스트로 암호를 해독 합니다.</span><span class="sxs-lookup"><span data-stu-id="6d927-129">This type is responsible for reading the inner <encryptedKey> element and decrypting it to recover the original plaintext.</span></span>

<span data-ttu-id="6d927-130">와 마찬가지로 \<설명자 >, 특정 형식의 <encryptedSecret> 요소 사용에서-휴지 암호화 메커니즘에 따라 달라 집니다.</span><span class="sxs-lookup"><span data-stu-id="6d927-130">As with \<descriptor>, the particular format of the <encryptedSecret> element depends on the at-rest encryption mechanism in use.</span></span> <span data-ttu-id="6d927-131">위의 예에 마스터 키가 주석 당 Windows DPAPI를 사용 하 여 암호화 합니다.</span><span class="sxs-lookup"><span data-stu-id="6d927-131">In the above example, the master key is encrypted using Windows DPAPI per the comment.</span></span>

## <a name="the-revocation-element"></a><span data-ttu-id="6d927-132">\<해지 > 요소</span><span class="sxs-lookup"><span data-stu-id="6d927-132">The \<revocation> element</span></span>

<span data-ttu-id="6d927-133">해지 키 저장소에는 최상위 개체도 존재합니다.</span><span class="sxs-lookup"><span data-stu-id="6d927-133">Revocations exist as top-level objects in the key repository.</span></span> <span data-ttu-id="6d927-134">규칙에 따라 해지는 파일 이름을 가진 **해지-{timestamp}.xml** (에 대 한 특정 날짜 이전의 모든 키를 해지) 또는 **해지-{guid}.xml** (예: 특정 키를 해지).</span><span class="sxs-lookup"><span data-stu-id="6d927-134">By convention revocations have the filename **revocation-{timestamp}.xml** (for revoking all keys before a specific date) or **revocation-{guid}.xml** (for revoking a specific key).</span></span> <span data-ttu-id="6d927-135">각 파일에는 단일 \<해지 > 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="6d927-135">Each file contains a single \<revocation> element.</span></span>

<span data-ttu-id="6d927-136">해지의 개별 키에 대 한 파일 내용이 됩니다 다음과 같이 합니다.</span><span class="sxs-lookup"><span data-stu-id="6d927-136">For revocations of individual keys, the file contents will be as below.</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T22:45:30.2616742Z</revocationDate>
  <key id="eb4fc299-8808-409d-8a34-23fc83d026c9" />
  <reason>human-readable reason</reason>
</revocation>
```

<span data-ttu-id="6d927-137">이 경우 지정된 된 키가 해지 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="6d927-137">In this case, only the specified key is revoked.</span></span> <span data-ttu-id="6d927-138">그러나 키 id가 "*",에서 같이 아래 예제에서는 모든 키를 가진 생성 날짜를 지정한 해지 날짜 전에 취소 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6d927-138">If the key id is "*", however, as in the below example, all keys whose creation date is prior to the specified revocation date are revoked.</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T15:45:45.7366491-07:00</revocationDate>
  <!-- All keys created before the revocation date are revoked. -->
  <key id="*" />
  <reason>human-readable reason</reason>
</revocation>
```

<span data-ttu-id="6d927-139">\<이유 > 요소는 시스템에서 읽지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6d927-139">The \<reason> element is never read by the system.</span></span> <span data-ttu-id="6d927-140">단순히 해지 사람이 읽을 수 있는 이유를 저장할 수 있는 편리한 위치는</span><span class="sxs-lookup"><span data-stu-id="6d927-140">It is simply a convenient place to store a human-readable reason for revocation.</span></span>
