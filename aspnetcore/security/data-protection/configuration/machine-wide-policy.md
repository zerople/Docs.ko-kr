---
title: "컴퓨터 단위 정책"
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
ms.openlocfilehash: 513726e209401d158ac98d5874942765751ac07d
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/11/2017
---
# <a name="machine-wide-policy"></a><span data-ttu-id="47c09-103">컴퓨터 단위 정책</span><span class="sxs-lookup"><span data-stu-id="47c09-103">Machine Wide Policy</span></span>

<a name=data-protection-configuration-machinewidepolicy></a>

<span data-ttu-id="47c09-104">Windows에서 실행 하는 경우 데이터 보호 시스템 데이터 보호를 사용 하는 모든 응용 프로그램에 대 한 기본 시스템 수준의 정책 설정에 대 한 지원이 제한 합니다.</span><span class="sxs-lookup"><span data-stu-id="47c09-104">When running on Windows, the data protection system has limited support for setting default machine-wide policy for all applications which consume data protection.</span></span> <span data-ttu-id="47c09-105">일반적인 개념은는 관리자 컴퓨터에서 모든 응용 프로그램을 수동으로 업데이트 하지 않고도 (예: 알고리즘 또는 키 사용 수명) 일부 기본 설정을 변경 하려면 싶어할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="47c09-105">The general idea is that an administrator might wish to change some default setting (such as algorithms used or key lifetime) without needing to manually update every application on the machine.</span></span>

>[!WARNING]
> <span data-ttu-id="47c09-106">시스템 관리자는 기본 정책을 설정할 수 있지만 이러한 적용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="47c09-106">The system administrator can set default policy, but they cannot enforce it.</span></span> <span data-ttu-id="47c09-107">응용 프로그램 개발자는 자신의 선택 중 하나가 지정 된 모든 값을 무시할 수입니다.</span><span class="sxs-lookup"><span data-stu-id="47c09-107">The application developer can always override any value with one of their own choosing.</span></span> <span data-ttu-id="47c09-108">기본 정책은 개발자 일부 특정 설정에 대 한 명시적 값 지정 하지 않은 응용 프로그램을만 영향을 줍니다.</span><span class="sxs-lookup"><span data-stu-id="47c09-108">The default policy only affects applications where the developer has not specified an explicit value for some particular setting.</span></span>

## <a name="setting-default-policy"></a><span data-ttu-id="47c09-109">기본 정책 설정</span><span class="sxs-lookup"><span data-stu-id="47c09-109">Setting default policy</span></span>

<span data-ttu-id="47c09-110">기본 정책을 설정 하려면 관리자가 다음 키 아래 시스템 레지스트리에 알려진된 값을 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="47c09-110">To set default policy, an administrator can set known values in the system registry under the following key.</span></span>

<span data-ttu-id="47c09-111">레지스트리 키:`HKLM\SOFTWARE\Microsoft\DotNetPackages\Microsoft.AspNetCore.DataProtection`</span><span class="sxs-lookup"><span data-stu-id="47c09-111">Reg key: `HKLM\SOFTWARE\Microsoft\DotNetPackages\Microsoft.AspNetCore.DataProtection`</span></span>

<span data-ttu-id="47c09-112">64 비트 운영 체제에 32 비트 응용 프로그램의 동작을 적용 하는 경우 위의 키 Wow6432Node 해당 하는 구성도 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="47c09-112">If you're on a 64-bit operating system and want to affect the behavior of 32-bit applications, remember also to configure the Wow6432Node equivalent of the above key.</span></span>

<span data-ttu-id="47c09-113">지원 되는 값은 같습니다.</span><span class="sxs-lookup"><span data-stu-id="47c09-113">The supported values are:</span></span>

* <span data-ttu-id="47c09-114">EncryptionType [string]-데이터 보호에 사용할 알고리즘을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="47c09-114">EncryptionType [string] - specifies which algorithms should be used for data protection.</span></span> <span data-ttu-id="47c09-115">이 값 "CNG CBC", "CNG-GCM" 또는 "Managed" 이어야 하며 보다 자세히 설명 되어 [아래](#data-protection-encryption-types)합니다.</span><span class="sxs-lookup"><span data-stu-id="47c09-115">This value must be "CNG-CBC", "CNG-GCM", or "Managed" and is described in more detail [below](#data-protection-encryption-types).</span></span>

* <span data-ttu-id="47c09-116">[DWORD]-DefaultKeyLifetime 새로 생성 된 키에 대 한 수명을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="47c09-116">DefaultKeyLifetime [DWORD] - specifies the lifetime for newly-generated keys.</span></span> <span data-ttu-id="47c09-117">이 값은 일 단위로 지정 되며 ≥ 7 이어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="47c09-117">This value is specified in days and must be ≥ 7.</span></span>

* <span data-ttu-id="47c09-118">[String]-KeyEscrowSinks 키 위탁 사용 되는 형식을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="47c09-118">KeyEscrowSinks [string] - specifies the types which will be used for key escrow.</span></span> <span data-ttu-id="47c09-119">이 값은 세미콜론으로 구분 된 목록 키 에스크로 싱크를 여기서 목록의 각 요소는 어셈블리의 정규화 된 이름을 IKeyEscrowSink를 구현 하는 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="47c09-119">This value is a semicolon-delimited list of key escrow sinks, where each element in the list is the assembly qualified name of a type which implements IKeyEscrowSink.</span></span>

<a name=data-protection-encryption-types></a>

### <a name="encryption-types"></a><span data-ttu-id="47c09-120">암호화 종류</span><span class="sxs-lookup"><span data-stu-id="47c09-120">Encryption types</span></span>

<span data-ttu-id="47c09-121">시스템 EncryptionType "CNG CBC" 이면 암호를 사용 하는 CBC 모드 대칭 블록 기밀성 및 HMAC에 대 한 신뢰성에 대 한 Windows CNG에서 제공 하는 서비스와 구성 됩니다 (참조 [사용자 지정 Windows CNG 알고리즘지정](overview.md#data-protection-changing-algorithms-cng)자세한 세부 정보에 대 한).</span><span class="sxs-lookup"><span data-stu-id="47c09-121">If EncryptionType is "CNG-CBC", the system will be configured to use a CBC-mode symmetric block cipher for confidentiality and HMAC for authenticity with services provided by Windows CNG (see [Specifying custom Windows CNG algorithms](overview.md#data-protection-changing-algorithms-cng) for more details).</span></span> <span data-ttu-id="47c09-122">다음 추가 값이 지원 되는지, CngCbcAuthenticatedEncryptionSettings 형식의 속성에 해당 하는 각:</span><span class="sxs-lookup"><span data-stu-id="47c09-122">The following additional values are supported, each of which corresponds to a property on the CngCbcAuthenticatedEncryptionSettings type:</span></span>

* <span data-ttu-id="47c09-123">EncryptionAlgorithm [string]-CNG에서 인식 하는 대칭 블록 암호화 알고리즘의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="47c09-123">EncryptionAlgorithm [string] - the name of a symmetric block cipher algorithm understood by CNG.</span></span> <span data-ttu-id="47c09-124">이 알고리즘 CBC 모드에서 열립니다.</span><span class="sxs-lookup"><span data-stu-id="47c09-124">This algorithm will be opened in CBC mode.</span></span>

* <span data-ttu-id="47c09-125">EncryptionAlgorithmProvider [string]-EncryptionAlgorithm 알고리즘을 만들 수 있는 CNG 공급자 구현 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="47c09-125">EncryptionAlgorithmProvider [string] - the name of the CNG provider implementation which can produce the algorithm EncryptionAlgorithm.</span></span>

* <span data-ttu-id="47c09-126">EncryptionAlgorithmKeySize [DWORD]-대칭 블록 암호화 알고리즘에 대 한 파생 키의 길이 (비트)에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="47c09-126">EncryptionAlgorithmKeySize [DWORD] - the length (in bits) of the key to derive for the symmetric block cipher algorithm.</span></span>

* <span data-ttu-id="47c09-127">HashAlgorithm [string]-CNG에서 인식 하는 해시 알고리즘의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="47c09-127">HashAlgorithm [string] - the name of a hash algorithm understood by CNG.</span></span> <span data-ttu-id="47c09-128">이 알고리즘 HMAC 모드에서 열립니다.</span><span class="sxs-lookup"><span data-stu-id="47c09-128">This algorithm will be opened in HMAC mode.</span></span>

* <span data-ttu-id="47c09-129">HashAlgorithmProvider [string]-알고리즘 HashAlgorithm을 만들 수 있는 CNG 공급자 구현 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="47c09-129">HashAlgorithmProvider [string] - the name of the CNG provider implementation which can produce the algorithm HashAlgorithm.</span></span>

<span data-ttu-id="47c09-130">시스템 기밀성 및 인증에 대 한 Windows CNG에서 제공 하는 서비스와 Galois/카운터 모드 대칭 블록 암호화를 사용 하도록 구성 됩니다 EncryptionType "CNG GCM" 이면 (참조 [사용자 지정 Windows CNG 알고리즘지정](overview.md#data-protection-changing-algorithms-cng)자세한 세부 정보에 대 한).</span><span class="sxs-lookup"><span data-stu-id="47c09-130">If EncryptionType is "CNG-GCM", the system will be configured to use a Galois/Counter Mode symmetric block cipher for confidentiality and authenticity with services provided by Windows CNG (see [Specifying custom Windows CNG algorithms](overview.md#data-protection-changing-algorithms-cng) for more details).</span></span> <span data-ttu-id="47c09-131">다음 추가 값이 지원 되는지, CngGcmAuthenticatedEncryptionSettings 형식의 속성에 해당 하는 각:</span><span class="sxs-lookup"><span data-stu-id="47c09-131">The following additional values are supported, each of which corresponds to a property on the CngGcmAuthenticatedEncryptionSettings type:</span></span>

* <span data-ttu-id="47c09-132">EncryptionAlgorithm [string]-CNG에서 인식 하는 대칭 블록 암호화 알고리즘의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="47c09-132">EncryptionAlgorithm [string] - the name of a symmetric block cipher algorithm understood by CNG.</span></span> <span data-ttu-id="47c09-133">이 알고리즘 Galois/카운터 모드에서 열립니다.</span><span class="sxs-lookup"><span data-stu-id="47c09-133">This algorithm will be opened in Galois/Counter Mode.</span></span>

* <span data-ttu-id="47c09-134">EncryptionAlgorithmProvider [string]-EncryptionAlgorithm 알고리즘을 만들 수 있는 CNG 공급자 구현 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="47c09-134">EncryptionAlgorithmProvider [string] - the name of the CNG provider implementation which can produce the algorithm EncryptionAlgorithm.</span></span>

* <span data-ttu-id="47c09-135">EncryptionAlgorithmKeySize [DWORD]-대칭 블록 암호화 알고리즘에 대 한 파생 키의 길이 (비트)에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="47c09-135">EncryptionAlgorithmKeySize [DWORD] - the length (in bits) of the key to derive for the symmetric block cipher algorithm.</span></span>

<span data-ttu-id="47c09-136">EncryptionType이 "관리" 시스템 정품에 대 한 관리 되는 SymmetricAlgorithm 기밀성 및 KeyedHashAlgorithm을 사용 하도록 구성 됩니다 (참조 [지정 하면 사용자 지정 관리 알고리즘](overview.md#data-protection-changing-algorithms-custom-managed) 자세한 세부 정보에 대 한).</span><span class="sxs-lookup"><span data-stu-id="47c09-136">If EncryptionType is "Managed", the system will be configured to use a managed SymmetricAlgorithm for confidentiality and KeyedHashAlgorithm for authenticity (see [Specifying custom managed algorithms](overview.md#data-protection-changing-algorithms-custom-managed) for more details).</span></span> <span data-ttu-id="47c09-137">다음 추가 값이 지원 되는지, ManagedAuthenticatedEncryptionSettings 형식의 속성에 해당 하는 각:</span><span class="sxs-lookup"><span data-stu-id="47c09-137">The following additional values are supported, each of which corresponds to a property on the ManagedAuthenticatedEncryptionSettings type:</span></span>

* <span data-ttu-id="47c09-138">EncryptionAlgorithmType [string]-SymmetricAlgorithm를 구현 하는 형식의 어셈블리 한정 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="47c09-138">EncryptionAlgorithmType [string] - the assembly-qualified name of a type which implements SymmetricAlgorithm.</span></span>

* <span data-ttu-id="47c09-139">EncryptionAlgorithmKeySize [DWORD]-(비트)의 길이를 대칭 암호화 알고리즘에 대 한 파생 키입니다.</span><span class="sxs-lookup"><span data-stu-id="47c09-139">EncryptionAlgorithmKeySize [DWORD] - the length (in bits) of the key to derive for the symmetric encryption algorithm.</span></span>

* <span data-ttu-id="47c09-140">ValidationAlgorithmType [string]-KeyedHashAlgorithm을 구현 하는 형식의 어셈블리 한정 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="47c09-140">ValidationAlgorithmType [string] - the assembly-qualified name of a type which implements KeyedHashAlgorithm.</span></span>

<span data-ttu-id="47c09-141">EncryptionType이 다른 값 (null이 아닌 / 비어 있음), 데이터 보호 시스템 시작 시 예외를 throw 합니다.</span><span class="sxs-lookup"><span data-stu-id="47c09-141">If EncryptionType has any other value (other than null / empty), the data protection system will throw an exception at startup.</span></span>

>[!WARNING]
> <span data-ttu-id="47c09-142">형식 이름 (EncryptionAlgorithmType, ValidationAlgorithmType, KeyEscrowSinks)를 포함 하는 기본 정책 설정을 구성할 때 형식을 응용 프로그램에 제공 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="47c09-142">When configuring a default policy setting that involves type names (EncryptionAlgorithmType, ValidationAlgorithmType, KeyEscrowSinks), the types must be available to the application.</span></span> <span data-ttu-id="47c09-143">실제로이 응용 프로그램의 데스크톱 CLR에서 실행 중인 경우 이러한 형식을 포함 하는 어셈블리 되어야 함을 GACed 의미 합니다.</span><span class="sxs-lookup"><span data-stu-id="47c09-143">In practice, this means that for applications running on Desktop CLR, the assemblies which contain these types should be GACed.</span></span> <span data-ttu-id="47c09-144">실행 되는 ASP.NET Core 응용 프로그램에 대 한 [.NET Core](https://microsoft.com/net/core), 이러한 형식을 포함 하는 패키지를 설치 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="47c09-144">For ASP.NET Core applications running on [.NET Core](https://microsoft.com/net/core), the packages which contain these types should be installed.</span></span>
