---
title: "보호 된 페이로드의 수명을 제한"
author: rick-anderson
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 000175e2-10fc-43dd-bfc2-51e004b97b44
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/consumer-apis/limited-lifetime-payloads
ms.openlocfilehash: 4ff13803b328c1e9dd2934c38c88b43f5798de03
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/11/2017
---
# <a name="limiting-the-lifetime-of-protected-payloads"></a><span data-ttu-id="4a898-103">보호 된 페이로드의 수명을 제한</span><span class="sxs-lookup"><span data-stu-id="4a898-103">Limiting the lifetime of protected payloads</span></span>

<span data-ttu-id="4a898-104">응용 프로그램 개발자가 설정된 된 시간 동안 후에 만료 되는 보호 된 페이로드를 작성 하려고 하는 위치 시나리오가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4a898-104">There are scenarios where the application developer wants to create a protected payload that expires after a set period of time.</span></span> <span data-ttu-id="4a898-105">예를 들어, 보호 된 페이로드는 1 시간 동안 유효 해야만 암호 다시 설정 토큰을 나타낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4a898-105">For instance, the protected payload might represent a password reset token that should only be valid for one hour.</span></span> <span data-ttu-id="4a898-106">개발자는 포함 된 만료 날짜를 포함 하는 고유한 페이로드 형식을 만들 수에 대 한 수 및 고급 개발자를 누르고, 이렇게 할 수 있습니다 이지만 대부분 개발자의 이러한 만료 관리 수 커집니다 번거로운.</span><span class="sxs-lookup"><span data-stu-id="4a898-106">It is certainly possible for the developer to create their own payload format that contains an embedded expiration date, and advanced developers may wish to do this anyway, but for the majority of developers managing these expirations can grow tedious.</span></span>

<span data-ttu-id="4a898-107">간단 하 게 하려면이 대상 우리의 개발자에 대 한 패키지 Microsoft.AspNetCore.DataProtection.Extensions 설정 시간이 지나면 자동으로 만료 되는 페이로드를 만들기 위한 유틸리티 Api를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="4a898-107">To make this easier for our developer audience, the package Microsoft.AspNetCore.DataProtection.Extensions contains utility APIs for creating payloads that automatically expire after a set period of time.</span></span> <span data-ttu-id="4a898-108">이러한 Api의 ITimeLimitedDataProtector 형식에서 중지합니다.</span><span class="sxs-lookup"><span data-stu-id="4a898-108">These APIs hang off of the ITimeLimitedDataProtector type.</span></span>

## <a name="api-usage"></a><span data-ttu-id="4a898-109">API 사용</span><span class="sxs-lookup"><span data-stu-id="4a898-109">API usage</span></span>

<span data-ttu-id="4a898-110">ITimeLimitedDataProtector 인터페이스는 보호 / 시간이 제한 된 보호를 해제 하 고 자체 페이로드를 만료에 대 한 핵심 인터페이스입니다.</span><span class="sxs-lookup"><span data-stu-id="4a898-110">The ITimeLimitedDataProtector interface is the core interface for protecting and unprotecting time-limited / self-expiring payloads.</span></span> <span data-ttu-id="4a898-111">ITimeLimitedDataProtector의 인스턴스를 만들려면 먼저 해야 일반 인스턴스의 [IDataProtector](overview.md) 특정 용도 사용 하 여 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="4a898-111">To create an instance of an ITimeLimitedDataProtector, you'll first need an instance of a regular [IDataProtector](overview.md) constructed with a specific purpose.</span></span> <span data-ttu-id="4a898-112">IDataProtector 인스턴스를 사용할 수 있는 기본 만료 기능으로는 보호기 복귀할 IDataProtector.ToTimeLimitedDataProtector 확장 메서드를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="4a898-112">Once the IDataProtector instance is available, call the IDataProtector.ToTimeLimitedDataProtector extension method to get back a protector with built-in expiration capabilities.</span></span>

<span data-ttu-id="4a898-113">ITimeLimitedDataProtector는 다음과 같은 API 화면 및 확장 메서드를 노출합니다.</span><span class="sxs-lookup"><span data-stu-id="4a898-113">ITimeLimitedDataProtector exposes the following API surface and extension methods:</span></span>

* <span data-ttu-id="4a898-114">CreateProtector (문자열 용도): 만들려는 사용할 수 있다는 점에서 ITimeLimitedDataProtector이 API는 기존 IDataProtectionProvider.CreateProtector 비슷합니다 [체인의 용도 위해](purpose-strings.md) 루트 시간이 제한 된 보호기를 합니다.</span><span class="sxs-lookup"><span data-stu-id="4a898-114">CreateProtector(string purpose) : ITimeLimitedDataProtector This API is similar to the existing IDataProtectionProvider.CreateProtector in that it can be used to create [purpose chains](purpose-strings.md) from a root time-limited protector.</span></span>

* <span data-ttu-id="4a898-115">(바이트 일반 텍스트, DateTimeOffset 만료) 보호: byte</span><span class="sxs-lookup"><span data-stu-id="4a898-115">Protect(byte[] plaintext, DateTimeOffset expiration) : byte[]</span></span>

* <span data-ttu-id="4a898-116">보호 (바이트 일반 텍스트, TimeSpan 수명): byte</span><span class="sxs-lookup"><span data-stu-id="4a898-116">Protect(byte[] plaintext, TimeSpan lifetime) : byte[]</span></span>

* <span data-ttu-id="4a898-117">(바이트 일반 텍스트)를 보호: byte</span><span class="sxs-lookup"><span data-stu-id="4a898-117">Protect(byte[] plaintext) : byte[]</span></span>

* <span data-ttu-id="4a898-118">(문자열 일반 텍스트, DateTimeOffset 만료) 보호: 문자열</span><span class="sxs-lookup"><span data-stu-id="4a898-118">Protect(string plaintext, DateTimeOffset expiration) : string</span></span>

* <span data-ttu-id="4a898-119">보호 (문자열 일반 텍스트, TimeSpan 수명): 문자열</span><span class="sxs-lookup"><span data-stu-id="4a898-119">Protect(string plaintext, TimeSpan lifetime) : string</span></span>

* <span data-ttu-id="4a898-120">(문자열 일반 텍스트)를 보호: 문자열</span><span class="sxs-lookup"><span data-stu-id="4a898-120">Protect(string plaintext) : string</span></span>

<span data-ttu-id="4a898-121">일반 텍스트만 시간은 핵심 보호 방법 외에도 페이로드의 만료 날짜를 지정할 수 있는 새로운 오버 로드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4a898-121">In addition to the core Protect methods which take only the plaintext, there are new overloads which allow specifying the payload's expiration date.</span></span> <span data-ttu-id="4a898-122">(DateTimeOffset)를 통해 절대 날짜 또는 현재 시스템 시간, TimeSpan 통해) (에서 상대 시간으로 만료 날짜를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4a898-122">The expiration date can be specified as an absolute date (via a DateTimeOffset) or as a relative time (from the current system time, via a TimeSpan).</span></span> <span data-ttu-id="4a898-123">만료 날짜를 사용 하지 않습니다는 오버 로드를 호출 하면 페이로드 가정 하지 만료 합니다.</span><span class="sxs-lookup"><span data-stu-id="4a898-123">If an overload which doesn't take an expiration is called, the payload is assumed never to expire.</span></span>

* <span data-ttu-id="4a898-124">(바이트 protectedData, DateTimeOffset 만료 아웃)를 보호 해제: byte</span><span class="sxs-lookup"><span data-stu-id="4a898-124">Unprotect(byte[] protectedData, out DateTimeOffset expiration) : byte[]</span></span>

* <span data-ttu-id="4a898-125">(ProtectedData 바이트)를 보호 해제: byte</span><span class="sxs-lookup"><span data-stu-id="4a898-125">Unprotect(byte[] protectedData) : byte[]</span></span>

* <span data-ttu-id="4a898-126">(DateTimeOffset 만료 아웃 문자열 protectedData)를 보호 해제: 문자열</span><span class="sxs-lookup"><span data-stu-id="4a898-126">Unprotect(string protectedData, out DateTimeOffset expiration) : string</span></span>

* <span data-ttu-id="4a898-127">(문자열 protectedData)를 보호 해제: 문자열</span><span class="sxs-lookup"><span data-stu-id="4a898-127">Unprotect(string protectedData) : string</span></span>

<span data-ttu-id="4a898-128">Unprotect 메서드는 원래 보호 되지 않는 데이터를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="4a898-128">The Unprotect methods return the original unprotected data.</span></span> <span data-ttu-id="4a898-129">페이로드 아직 만료 되지 않았는지, 절대 만료를 선택적 출력 매개 변수는 원래 보호 되지 않는 데이터와 함께 반환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4a898-129">If the payload hasn't yet expired, the absolute expiration is returned as an optional out parameter along with the original unprotected data.</span></span> <span data-ttu-id="4a898-130">페이로드 만료 되 면 CryptographicException Unprotect 메서드의 모든 오버 로드에 throw 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4a898-130">If the payload is expired, all overloads of the Unprotect method will throw CryptographicException.</span></span>

>[!WARNING]
> <span data-ttu-id="4a898-131">하지 장기 또는 무기한 보존 해야 하는 페이로드를 보호 하기 위해 이러한 Api를 사용 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="4a898-131">It is not advised to use these APIs to protect payloads which require long-term or indefinite persistence.</span></span> <span data-ttu-id="4a898-132">"있습니까 여유가 한 달이 지난 후 영구적으로 복구할 수 없게 하려면 보호 된 페이로드"?</span><span class="sxs-lookup"><span data-stu-id="4a898-132">"Can I afford for the protected payloads to be permanently unrecoverable after a month?"</span></span> <span data-ttu-id="4a898-133">바람직한 방법은;으로 사용할 수 있습니다. 대답 한 경우 다음 없습니다 개발자 대체 Api를 고려해 야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4a898-133">can serve as a good rule of thumb; if the answer is no then developers should consider alternative APIs.</span></span>

<span data-ttu-id="4a898-134">사용 하 여 아래 샘플은 [DI 아닌 코드 경로](../configuration/non-di-scenarios.md) 데이터 보호 시스템을 인스턴스화하기 위한 합니다.</span><span class="sxs-lookup"><span data-stu-id="4a898-134">The sample below uses the [non-DI code paths](../configuration/non-di-scenarios.md) for instantiating the data protection system.</span></span> <span data-ttu-id="4a898-135">이 샘플을 실행 하려면 먼저 Microsoft.AspNetCore.DataProtection.Extensions 패키지에 대 한 참조를 추가 했는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="4a898-135">To run this sample, ensure that you have first added a reference to the Microsoft.AspNetCore.DataProtection.Extensions package.</span></span>

<span data-ttu-id="4a898-136">[!code-none[Main](limited-lifetime-payloads/samples/limitedlifetimepayloads.cs)]</span><span class="sxs-lookup"><span data-stu-id="4a898-136">[!code-none[Main](limited-lifetime-payloads/samples/limitedlifetimepayloads.cs)]</span></span>
