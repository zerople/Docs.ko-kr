---
title: "용도 문자열"
author: rick-anderson
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: c96ed361-c382-4980-8933-800e740cfc38
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/consumer-apis/purpose-strings
ms.openlocfilehash: 799c3dc2768e264307783efafee626a346a9362c
ms.sourcegitcommit: 8f4d4fad1ca27adf9e396f5c205c9875a3963664
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/13/2017
---
# <a name="purpose-strings"></a><span data-ttu-id="7c2b0-103">용도 문자열</span><span class="sxs-lookup"><span data-stu-id="7c2b0-103">Purpose Strings</span></span>

<a name="data-protection-consumer-apis-purposes"></a>

<span data-ttu-id="7c2b0-104">IDataProtectionProvider를 사용 하는 구성 요소는 고유한 전달 해야 *목적으로* CreateProtector 메서드의 매개 변수입니다.</span><span class="sxs-lookup"><span data-stu-id="7c2b0-104">Components which consume IDataProtectionProvider must pass a unique *purposes* parameter to the CreateProtector method.</span></span> <span data-ttu-id="7c2b0-105">목적 *매개 변수* 루트 암호화 키가 동일한 경우에 암호화 소비자 간에 격리 제공 하므로 보안에는 데이터 보호 시스템에 대 한 내재 된 합니다.</span><span class="sxs-lookup"><span data-stu-id="7c2b0-105">The purposes *parameter* is inherent to the security of the data protection system, as it provides isolation between cryptographic consumers, even if the root cryptographic keys are the same.</span></span>

<span data-ttu-id="7c2b0-106">소비자는 용도 지정 하는 경우에 해당 고객에 게 고유 암호화 하위 키를 파생 시키는 목적 문자열이 루트 암호화 키와 함께 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7c2b0-106">When a consumer specifies a purpose, the purpose string is used along with the root cryptographic keys to derive cryptographic subkeys unique to that consumer.</span></span> <span data-ttu-id="7c2b0-107">응용 프로그램에서 다른 모든 암호화 소비자에 게 소비자 그래야만: 다른 구성 요소가 없으면 해당 페이로드를 읽을 수 있으며 모든 다른 구성 요소의 페이로드를 읽을 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7c2b0-107">This isolates the consumer from all other cryptographic consumers in the application: no other component can read its payloads, and it cannot read any other component's payloads.</span></span> <span data-ttu-id="7c2b0-108">이 격리는 또한 부적합 모든 범주의 구성 요소에 대 한 공격을 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="7c2b0-108">This isolation also renders infeasible entire categories of attack against the component.</span></span>

![용도 다이어그램 예제](purpose-strings/_static/purposes.png)

<span data-ttu-id="7c2b0-110">IDataProtector 인스턴스 A와 B 위의 다이어그램에서 **없습니다** 페이로드를 읽을 서로의만 자신의 합니다.</span><span class="sxs-lookup"><span data-stu-id="7c2b0-110">In the diagram above IDataProtector instances A and B **cannot** read each other's payloads, only their own.</span></span>

<span data-ttu-id="7c2b0-111">용도 문자열 본인 확인 될 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7c2b0-111">The purpose string doesn't have to be secret.</span></span> <span data-ttu-id="7c2b0-112">단순히 다른 올바르게 동작 구성 요소가 없으면 동일한 목적 문자열로 제공 적이 됩니다 의미에서 고유 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7c2b0-112">It should simply be unique in the sense that no other well-behaved component will ever provide the same purpose string.</span></span>

>[!TIP]
> <span data-ttu-id="7c2b0-113">데이터 보호 Api를 사용 하는 구성 요소의 네임 스페이스 및 형식 이름을 사용 하는 좋은 경험상, 연습이 정보를 충돌 하지 것입니다.</span><span class="sxs-lookup"><span data-stu-id="7c2b0-113">Using the namespace and type name of the component consuming the data protection APIs is a good rule of thumb, as in practice this information will never conflict.</span></span>
>
><span data-ttu-id="7c2b0-114">전달자 토큰 minting 담당 하는 Contoso에서 작성 된 구성 요소는 해당 용도 문자열로 Contoso.Security.BearerToken을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7c2b0-114">A Contoso-authored component which is responsible for minting bearer tokens might use Contoso.Security.BearerToken as its purpose string.</span></span> <span data-ttu-id="7c2b0-115">또는-더 좋은-Contoso.Security.BearerToken.v1의 목적은 문자열을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7c2b0-115">Or - even better - it might use Contoso.Security.BearerToken.v1 as its purpose string.</span></span> <span data-ttu-id="7c2b0-116">Contoso.Security.BearerToken.v2의 목적으로 사용 하도록 이후 버전을 사용 하면 버전 번호를 추가 하 고 페이로드 이동 관련해 서 다양 한 버전 서로 완전히 격리는 합니다.</span><span class="sxs-lookup"><span data-stu-id="7c2b0-116">Appending the version number allows a future version to use Contoso.Security.BearerToken.v2 as its purpose, and the different versions would be completely isolated from one another as far as payloads go.</span></span>

<span data-ttu-id="7c2b0-117">CreateProtector 목적으로 매개 변수는 문자열 배열 이므로 위의 수 대신로 지정 되어 있지 ["Contoso.Security.BearerToken", "v1"]입니다.</span><span class="sxs-lookup"><span data-stu-id="7c2b0-117">Since the purposes parameter to CreateProtector is a string array, the above could have been instead specified as [ "Contoso.Security.BearerToken", "v1" ].</span></span> <span data-ttu-id="7c2b0-118">이 목적으로 계층 구조 설정를 통해 데이터 보호 시스템을 사용 하는 다중 테 넌 트 시나리오의 가능성을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="7c2b0-118">This allows establishing a hierarchy of purposes and opens up the possibility of multi-tenancy scenarios with the data protection system.</span></span>

<a name="data-protection-contoso-purpose"></a>

>[!WARNING]
> <span data-ttu-id="7c2b0-119">구성 요소에서 신뢰할 수 없는 사용자 입력을 위해 체인에 대 한 입력의 유일한 출처로 허용 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7c2b0-119">Components should not allow untrusted user input to be the sole source of input for the purposes chain.</span></span>
>
><span data-ttu-id="7c2b0-120">예를 들어 구성 요소를 Contoso.Messaging.SecureMessage 보안 메시지를 저장 하기 위한 담당 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="7c2b0-120">For example, consider a component Contoso.Messaging.SecureMessage which is responsible for storing secure messages.</span></span> <span data-ttu-id="7c2b0-121">보안 메시징 구성 요소가 CreateProtector ([username])를 호출 하는 경우 악의적인 사용자 하기 위해 호출 하는 구성 요소를 가져올 사용자 이름 "Contoso.Security.BearerToken" 계정을 만들고 수 CreateProtector([" Contoso.Security.BearerToken"]), 따라서 있는 mint 페이로드를 보안 메시징 시스템을 실수로 인해으로 인식 인증 토큰입니다.</span><span class="sxs-lookup"><span data-stu-id="7c2b0-121">If the secure messaging component were to call CreateProtector([ username ]), then a malicious user might create an account with username "Contoso.Security.BearerToken" in an attempt to get the component to call CreateProtector([ "Contoso.Security.BearerToken" ]), thus inadvertently causing the secure messaging system to mint payloads that could be perceived as authentication tokens.</span></span>
>
><span data-ttu-id="7c2b0-122">메시징 구성 요소에 대 한 더 나은 목적으로 체인 CreateProtector 것 (["Contoso.Messaging.SecureMessage", "사용자: 사용자 이름"]), 적절 한 격리를 제공 하는 합니다.</span><span class="sxs-lookup"><span data-stu-id="7c2b0-122">A better purposes chain for the messaging component would be CreateProtector([ "Contoso.Messaging.SecureMessage", "User: username" ]), which provides proper isolation.</span></span>

<span data-ttu-id="7c2b0-123">IDataProtectionProvider 동작, IDataProtector를 목적으로 및에서 제공 되는 격리는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="7c2b0-123">The isolation provided by and behaviors of IDataProtectionProvider, IDataProtector, and purposes are as follows:</span></span>

* <span data-ttu-id="7c2b0-124">지정된 된 IDataProtectionProvider 개체에 대 한 CreateProtector 메서드 IDataProtector 개체를 이것을 만든 IDataProtectionProvider 개체와 메서드에 전달 된 하는 목적으로 매개 변수 모두에 연결 된 고유 하 게 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7c2b0-124">For a given IDataProtectionProvider object, the CreateProtector method will create an IDataProtector object uniquely tied to both the IDataProtectionProvider object which created it and the purposes parameter which was passed into the method.</span></span>

* <span data-ttu-id="7c2b0-125">용도 매개 변수가 null 일 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7c2b0-125">The purpose parameter must not be null.</span></span> <span data-ttu-id="7c2b0-126">(목적으로 배열로 지정 된 경우 즉, 배열의 길이가 0 인 되지 않아야 하 고 배열의 모든 요소는 null 이어야 합니다.) 빈 문자열 목적 기술적으로 사용할 수 있지만 않는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="7c2b0-126">(If purposes is specified as an array, this means that the array must not be of zero length and all elements of the array must be non-null.) An empty string purpose is technically allowed but is discouraged.</span></span>

* <span data-ttu-id="7c2b0-127">두 가지 목적으로 인수는 동일한 순서로 동일한 문자열 (서 수는 비교자를 사용 하 여)를 포함 하는 경우에 동일 합니다.</span><span class="sxs-lookup"><span data-stu-id="7c2b0-127">Two purposes arguments are equivalent if and only if they contain the same strings (using an ordinal comparer) in the same order.</span></span> <span data-ttu-id="7c2b0-128">단일 용도 인수 해당 단일 요소 목적으로 배열 하는 것과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="7c2b0-128">A single purpose argument is equivalent to the corresponding single-element purposes array.</span></span>

* <span data-ttu-id="7c2b0-129">해당 하는 목적으로 매개 변수를 사용 하 여 해당 IDataProtectionProvider 개체에서 만들어진 경우에 두 IDataProtector 개체는 동일 합니다.</span><span class="sxs-lookup"><span data-stu-id="7c2b0-129">Two IDataProtector objects are equivalent if and only if they are created from equivalent IDataProtectionProvider objects with equivalent purposes parameters.</span></span>

* <span data-ttu-id="7c2b0-130">지정된 된 IDataProtector 개체에 대 한 경우에 Unprotect(protectedData)에 대 한 호출 원래 unprotectedData 반환 합니다 protectedData: 동등 IDataProtector 개체에 대 한 Protect(unprotectedData) = 합니다.</span><span class="sxs-lookup"><span data-stu-id="7c2b0-130">For a given IDataProtector object, a call to Unprotect(protectedData) will return the original unprotectedData if and only if protectedData := Protect(unprotectedData) for an equivalent IDataProtector object.</span></span>

> [!NOTE]
> <span data-ttu-id="7c2b0-131">다른 구성 요소와 충돌 하는 목적은 문자열에 일부 구성 요소 의도적으로 선택 하는 경우를 하려고 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7c2b0-131">We're not considering the case where some component intentionally chooses a purpose string which is known to conflict with another component.</span></span> <span data-ttu-id="7c2b0-132">이러한 구성 요소는 기본적으로 간주 되 악성 하며이 시스템에 작업자 프로세스 내의 악성 코드가 이미 실행 되 고 보안 보장을 제공 하 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7c2b0-132">Such a component would essentially be considered malicious, and this system is not intended to provide security guarantees in the event that malicious code is already running inside of the worker process.</span></span>
