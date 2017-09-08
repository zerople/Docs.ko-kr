---
title: "키 관리 및 수명"
author: rick-anderson
description: "키 관리 및 수명에 설명합니다."
keywords: "ASP.NET Core 키 관리, DPAPI를 DataProtection"
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: ef7dad2a-7029-4ae5-8f06-1fbebedccaa4
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/configuration/default-settings
ms.openlocfilehash: 913eda69f88ef05a990d9465024f4fa6b08cd1b7
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/11/2017
---
# <a name="key-management-and-lifetime"></a><span data-ttu-id="d9b64-104">키 관리 및 수명</span><span class="sxs-lookup"><span data-stu-id="d9b64-104">Key management and lifetime</span></span>

<a name=data-protection-default-settings></a>

## <a name="key-management"></a><span data-ttu-id="d9b64-105">키 관리</span><span class="sxs-lookup"><span data-stu-id="d9b64-105">Key Management</span></span>

<span data-ttu-id="d9b64-106">시스템 운영 환경을 검색 하 고 좋은 구성이 필요 없는 동작 기본값을 제공 하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="d9b64-106">The system tries to detect its operational environment and provide good zero-configuration behavioral defaults.</span></span> <span data-ttu-id="d9b64-107">사용 되는 추론은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="d9b64-107">The heuristic used is as follows.</span></span>

1. <span data-ttu-id="d9b64-108">시스템은 Azure 웹 사이트에서 호스팅 중 이라면 키 "%HOME%\ASP.NET\DataProtection-Keys" 폴더에 저장 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d9b64-108">If the system is being hosted in Azure Web Sites, keys are persisted to the "%HOME%\ASP.NET\DataProtection-Keys" folder.</span></span> <span data-ttu-id="d9b64-109">이 폴더는 네트워크 저장소에서 지원 하 고 응용 프로그램을 호스팅하는 모든 컴퓨터에서 동기화 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d9b64-109">This folder is backed by network storage and is synchronized across all machines hosting the application.</span></span> <span data-ttu-id="d9b64-110">저장 된 상태의 키를 보호 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d9b64-110">Keys are not protected at rest.</span></span> <span data-ttu-id="d9b64-111">이 폴더에서 단일 배포 슬롯에서 응용 프로그램의 모든 인스턴스에 키 링을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d9b64-111">This folder supplies the key ring to all instances of an application in a single deployment slot.</span></span> <span data-ttu-id="d9b64-112">스테이징 및 프로덕션, 같은 별도 배포 슬롯 키 링을 공유 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d9b64-112">Separate deployment slots, such as Staging and Production, will not share a key ring.</span></span> <span data-ttu-id="d9b64-113">예를 들어 스테이징 및 프로덕션을 교체 하거나 A를 사용 하 여 배포 슬롯 간에 교환 / B 테스트, 데이터 보호를 사용 하 여 모든 시스템 이전 슬롯 내 키 링을 사용 하 여 저장 된 데이터를 해독할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="d9b64-113">When you swap between deployment slots, for example swapping Staging to Production or using A/B testing, any system using data protection will not be able to decrypt stored data using the key ring inside the previous slot.</span></span> <span data-ttu-id="d9b64-114">데이터 보호를 사용 하 여 쿠키를 보호 하기 위해 표준 ASP.NET 쿠키 미들웨어를 사용 하 여 ASP.NET 응용 프로그램에서 기록 되 고 사용자에 게이 일으킵니다.</span><span class="sxs-lookup"><span data-stu-id="d9b64-114">This will lead to users being logged out of an ASP.NET application that uses the standard ASP.NET cookie middleware, as it uses data protection to protect its cookies.</span></span> <span data-ttu-id="d9b64-115">슬롯에 관계 없이 키 링을 원하는 경우 Azure Blob 저장소, Azure 주요 자격 증명 모음 SQL 저장소 등의 외부 키 링 공급자를 사용 하거나 Redis 캐시 합니다.</span><span class="sxs-lookup"><span data-stu-id="d9b64-115">If you desire slot-independent key rings, use an external key ring provider, such as Azure Blob Storage, Azure Key Vault, a SQL store, or Redis cache.</span></span>

2. <span data-ttu-id="d9b64-116">사용자 프로필을 사용할 수 있는 경우 키 "%LOCALAPPDATA%\ASP.NET\DataProtection-Keys" 폴더에 저장 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d9b64-116">If the user profile is available, keys are persisted to the "%LOCALAPPDATA%\ASP.NET\DataProtection-Keys" folder.</span></span> <span data-ttu-id="d9b64-117">또한 운영 체제가 Windows 인 경우 DPAPI를 사용 하 여 암호화 되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d9b64-117">Additionally, if the operating system is Windows, they'll be encrypted at rest using DPAPI.</span></span>

3. <span data-ttu-id="d9b64-118">응용 프로그램을 IIS에서 호스트 하는 경우 키 하 게 포함 되었는지 작업자 프로세스 계정에만 있는 특별 한 레지스트리 키에서 HKLM 레지스트리 에서도 유지 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d9b64-118">If the application is hosted in IIS, keys are persisted to the HKLM registry in a special registry key that is ACLed only to the worker process account.</span></span> <span data-ttu-id="d9b64-119">DPAPI를 사용 하 여 키 암호화 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d9b64-119">Keys are encrypted at rest using DPAPI.</span></span>

4. <span data-ttu-id="d9b64-120">이러한 조건 중가 일치 하는 경우 현재 프로세스 외부에서 키 유지 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d9b64-120">If none of these conditions matches, keys are not persisted outside of the current process.</span></span> <span data-ttu-id="d9b64-121">종료 되 면 프로세스 생성 된 모든 키가 손실 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d9b64-121">When the process shuts down, all generated keys will be lost.</span></span>

<span data-ttu-id="d9b64-122">개발자는 항상 모든 권한 및 키가 저장 하는 방법과 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d9b64-122">The developer is always in full control and can override how and where keys are stored.</span></span> <span data-ttu-id="d9b64-123">위의 처음 세 옵션 해야 방법과 유사 대부분 응용 프로그램에 대 한 좋은 기본값 ASP.NET <machineKey> 자동 생성 루틴 이전에 작동 합니다.</span><span class="sxs-lookup"><span data-stu-id="d9b64-123">The first three options above should good defaults for most applications similar to how the ASP.NET <machineKey> auto-generation routines worked in the past.</span></span> <span data-ttu-id="d9b64-124">최종 대체 백 옵션은 진정으로 지정 해야 하는 유일한 시나리오 [구성](overview.md) 선행 키 지 속성을 원하는 하지만이 대체 드문 경우에만 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="d9b64-124">The final, fall back option is the only scenario that truly requires the developer to specify [configuration](overview.md) upfront if they want key persistence, but this fall-back would only occur in rare situations.</span></span>

>[!WARNING]
> <span data-ttu-id="d9b64-125">개발자는이 추론을 재정의 하 고 데이터 보호 시스템에 특정 키 저장소를 가리키는, 미사용 키의 자동 암호화 비활성화 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d9b64-125">If the developer overrides this heuristic and points the data protection system at a specific key repository, automatic encryption of keys at rest will be disabled.</span></span> <span data-ttu-id="d9b64-126">저장 된 상태의 보호를 통해 다시 사용 되도록 설정 될 수 있습니다 [구성](overview.md)합니다.</span><span class="sxs-lookup"><span data-stu-id="d9b64-126">At rest protection can be re-enabled via [configuration](overview.md).</span></span>

## <a name="key-lifetime"></a><span data-ttu-id="d9b64-127">키 수명</span><span class="sxs-lookup"><span data-stu-id="d9b64-127">Key Lifetime</span></span>

<span data-ttu-id="d9b64-128">기본적으로 키에는 90 일 수명을 가집니다.</span><span class="sxs-lookup"><span data-stu-id="d9b64-128">Keys by default have a 90-day lifetime.</span></span> <span data-ttu-id="d9b64-129">키가 만료 되 면 시스템은 자동으로 새 키를 생성 하 여 새 키를 활성 키로 설정.</span><span class="sxs-lookup"><span data-stu-id="d9b64-129">When a key expires, the system will automatically generate a new key and set the new key as the active key.</span></span> <span data-ttu-id="d9b64-130">사용 중지 된 키 사람과 보호 되는 모든 데이터를 해독할 수 여전히 시스템에 남아 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d9b64-130">As long as retired keys remain on the system you will still be able to decrypt any data protected with them.</span></span> <span data-ttu-id="d9b64-131">참조 [키 관리](../implementation/key-management.md#data-protection-implementation-key-management-expiration) 자세한 정보에 대 한 합니다.</span><span class="sxs-lookup"><span data-stu-id="d9b64-131">See [key management](../implementation/key-management.md#data-protection-implementation-key-management-expiration) for more information.</span></span>

## <a name="default-algorithms"></a><span data-ttu-id="d9b64-132">기본 알고리즘</span><span class="sxs-lookup"><span data-stu-id="d9b64-132">Default Algorithms</span></span>

<span data-ttu-id="d9b64-133">기본 페이로드 보호 알고리즘 사용은 AES-256-CBC 기밀성 및 HMACSHA256에 대 한 신뢰성에 대 한 합니다.</span><span class="sxs-lookup"><span data-stu-id="d9b64-133">The default payload protection algorithm used is AES-256-CBC for confidentiality and HMACSHA256 for authenticity.</span></span> <span data-ttu-id="d9b64-134">페이로드 당 별로 이러한 알고리즘에 사용 되는 두 개의 하위 키를 파생 롤백 90 일 마다 512 비트 마스터 키가 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d9b64-134">A 512-bit master key, rolled every 90 days, is used to derive the two sub-keys used for these algorithms on a per-payload basis.</span></span> <span data-ttu-id="d9b64-135">참조 [파생 하위](../implementation/subkeyderivation.md#data-protection-implementation-subkey-derivation-aad) 자세한 정보에 대 한 합니다.</span><span class="sxs-lookup"><span data-stu-id="d9b64-135">See [subkey derivation](../implementation/subkeyderivation.md#data-protection-implementation-subkey-derivation-aad) for more information.</span></span>
