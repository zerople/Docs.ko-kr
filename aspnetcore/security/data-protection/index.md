---
title: "ASP.NET Core에서 데이터 보호"
author: rick-anderson
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 1f402da8-1052-4970-9835-9f9f16a02dbc
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/index
ms.openlocfilehash: 39f2ca96f8542de033274ea957b5c7736948c981
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/11/2017
---
# <a name="data-protection-in-aspnet-core-consumer-apis-configuration-extensibility-apis-and-implementation"></a><span data-ttu-id="b6944-103">ASP.NET Core에서 데이터 보호: 소비자 API, 구성, 확장성 API 및 구현</span><span class="sxs-lookup"><span data-stu-id="b6944-103">Data Protection in ASP.NET Core: Consumer APIs, configuration, extensibility APIs and implementation</span></span>

* [<span data-ttu-id="b6944-104">데이터 보호 소개</span><span class="sxs-lookup"><span data-stu-id="b6944-104">Introduction to Data Protection</span></span>](introduction.md)

* [<span data-ttu-id="b6944-105">데이터 보호 API 시작</span><span class="sxs-lookup"><span data-stu-id="b6944-105">Getting Started with the Data Protection APIs</span></span>](using-data-protection.md)

* [<span data-ttu-id="b6944-106">소비자 API</span><span class="sxs-lookup"><span data-stu-id="b6944-106">Consumer APIs</span></span>](consumer-apis/index.md)

  * [<span data-ttu-id="b6944-107">소비자 API 개요</span><span class="sxs-lookup"><span data-stu-id="b6944-107">Consumer APIs Overview</span></span>](consumer-apis/overview.md)

  * [<span data-ttu-id="b6944-108">용도 문자열</span><span class="sxs-lookup"><span data-stu-id="b6944-108">Purpose Strings</span></span>](consumer-apis/purpose-strings.md)

  * [<span data-ttu-id="b6944-109">용도 계층 구조 및 다중 테넌트</span><span class="sxs-lookup"><span data-stu-id="b6944-109">Purpose hierarchy and multi-tenancy</span></span>](consumer-apis/purpose-strings-multitenancy.md)

  * [<span data-ttu-id="b6944-110">암호 해시</span><span class="sxs-lookup"><span data-stu-id="b6944-110">Password Hashing</span></span>](consumer-apis/password-hashing.md)

  * [<span data-ttu-id="b6944-111">보호된 페이로드의 수명 제한</span><span class="sxs-lookup"><span data-stu-id="b6944-111">Limiting the lifetime of protected payloads</span></span>](consumer-apis/limited-lifetime-payloads.md)

  * [<span data-ttu-id="b6944-112">호출된 키가 속한 페이로드 보호 해제</span><span class="sxs-lookup"><span data-stu-id="b6944-112">Unprotecting payloads whose keys have been revoked</span></span>](consumer-apis/dangerous-unprotect.md)

* [<span data-ttu-id="b6944-113">구성</span><span class="sxs-lookup"><span data-stu-id="b6944-113">Configuration</span></span>](configuration/index.md)

  * [<span data-ttu-id="b6944-114">데이터 보호 구성</span><span class="sxs-lookup"><span data-stu-id="b6944-114">Configuring Data Protection</span></span>](configuration/overview.md)

  * [<span data-ttu-id="b6944-115">기본 설정</span><span class="sxs-lookup"><span data-stu-id="b6944-115">Default Settings</span></span>](configuration/default-settings.md)

  * [<span data-ttu-id="b6944-116">컴퓨터 수준 정책</span><span class="sxs-lookup"><span data-stu-id="b6944-116">Machine Wide Policy</span></span>](configuration/machine-wide-policy.md)

  * [<span data-ttu-id="b6944-117">비 DI 인식 시나리오</span><span class="sxs-lookup"><span data-stu-id="b6944-117">Non DI Aware Scenarios</span></span>](configuration/non-di-scenarios.md)

* [<span data-ttu-id="b6944-118">확장성 API</span><span class="sxs-lookup"><span data-stu-id="b6944-118">Extensibility APIs</span></span>](extensibility/index.md)

  * [<span data-ttu-id="b6944-119">Core 암호화 확장성</span><span class="sxs-lookup"><span data-stu-id="b6944-119">Core cryptography extensibility</span></span>](extensibility/core-crypto.md)

  * [<span data-ttu-id="b6944-120">키 관리 확장성</span><span class="sxs-lookup"><span data-stu-id="b6944-120">Key management extensibility</span></span>](extensibility/key-management.md)

  * [<span data-ttu-id="b6944-121">기타 API</span><span class="sxs-lookup"><span data-stu-id="b6944-121">Miscellaneous APIs</span></span>](extensibility/misc-apis.md)

* [<span data-ttu-id="b6944-122">구현</span><span class="sxs-lookup"><span data-stu-id="b6944-122">Implementation</span></span>](implementation/index.md)

  * [<span data-ttu-id="b6944-123">인증된 암호화 세부 정보.</span><span class="sxs-lookup"><span data-stu-id="b6944-123">Authenticated encryption details.</span></span>](implementation/authenticated-encryption-details.md)

  * [<span data-ttu-id="b6944-124">하위 키 파생 및 인증된 암호화</span><span class="sxs-lookup"><span data-stu-id="b6944-124">Subkey Derivation and Authenticated Encryption</span></span>](implementation/subkeyderivation.md)

  * [<span data-ttu-id="b6944-125">컨텍스트 헤더</span><span class="sxs-lookup"><span data-stu-id="b6944-125">Context headers</span></span>](implementation/context-headers.md)

  * [<span data-ttu-id="b6944-126">키 관리</span><span class="sxs-lookup"><span data-stu-id="b6944-126">Key Management</span></span>](implementation/key-management.md)

  * [<span data-ttu-id="b6944-127">키 저장소 공급자</span><span class="sxs-lookup"><span data-stu-id="b6944-127">Key Storage Providers</span></span>](implementation/key-storage-providers.md)

  * [<span data-ttu-id="b6944-128">미사용 키 암호화</span><span class="sxs-lookup"><span data-stu-id="b6944-128">Key Encryption At Rest</span></span>](implementation/key-encryption-at-rest.md)

  * [<span data-ttu-id="b6944-129">키 불변성 및 설정 변경</span><span class="sxs-lookup"><span data-stu-id="b6944-129">Key Immutability and Changing Settings</span></span>](implementation/key-immutability.md)

  * [<span data-ttu-id="b6944-130">키 저장소 형식</span><span class="sxs-lookup"><span data-stu-id="b6944-130">Key Storage Format</span></span>](implementation/key-storage-format.md)

  * [<span data-ttu-id="b6944-131">삭제되는 데이터 보호 공급자</span><span class="sxs-lookup"><span data-stu-id="b6944-131">Ephemeral data protection providers</span></span>](implementation/key-storage-ephemeral.md)

* [<span data-ttu-id="b6944-132">호환성</span><span class="sxs-lookup"><span data-stu-id="b6944-132">Compatibility</span></span>](compatibility/index.md)

  * [<span data-ttu-id="b6944-133">응용프로그램 쿠키 공유</span><span class="sxs-lookup"><span data-stu-id="b6944-133">Sharing cookies between applications</span></span>](compatibility/cookie-sharing.md)

  * [<span data-ttu-id="b6944-134">ASP.NET에서 <machineKey> 바꾸기</span><span class="sxs-lookup"><span data-stu-id="b6944-134">Replacing <machineKey> in ASP.NET</span></span>](compatibility/replacing-machinekey.md)
