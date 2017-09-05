---
title: "키 관리 확장성"
author: rick-anderson
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 3606b251-8324-4485-8d52-582a2cd5cffb
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/extensibility/key-management
ms.openlocfilehash: fb74905660015b9a83503e1f74b25c66ae9df9e3
ms.sourcegitcommit: bd05f7ea8f87ad076ef6e8b704698ebcba5ca80c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2017
---
# <a name="key-management-extensibility"></a><span data-ttu-id="0e6f7-103">키 관리 확장성</span><span class="sxs-lookup"><span data-stu-id="0e6f7-103">Key management extensibility</span></span>

<a name=data-protection-extensibility-key-management></a>

>[!TIP]
> <span data-ttu-id="0e6f7-104">읽기는 [키 관리](../implementation/key-management.md#data-protection-implementation-key-management) 이러한 Api는 기본적인 개념 중 일부에 대해 설명 하는 대로이 섹션을 읽기 전에 섹션.</span><span class="sxs-lookup"><span data-stu-id="0e6f7-104">Read the [key management](../implementation/key-management.md#data-protection-implementation-key-management) section before reading this section, as it explains some of the fundamental concepts behind these APIs.</span></span>

>[!WARNING]
> <span data-ttu-id="0e6f7-105">다음 인터페이스 중 하나를 구현 하는 형식은 스레드로부터 안전 해야 합니다. 여러 호출자에 대 한 합니다.</span><span class="sxs-lookup"><span data-stu-id="0e6f7-105">Types that implement any of the following interfaces should be thread-safe for multiple callers.</span></span>

## <a name="key"></a><span data-ttu-id="0e6f7-106">Key</span><span class="sxs-lookup"><span data-stu-id="0e6f7-106">Key</span></span>

<span data-ttu-id="0e6f7-107">IKey 인터페이스에는 암호화 시스템에 있는 키의 기본 표현입니다.</span><span class="sxs-lookup"><span data-stu-id="0e6f7-107">The IKey interface is the basic representation of a key in cryptosystem.</span></span> <span data-ttu-id="0e6f7-108">용어 키의 "암호화 키 자료" 리터럴 관점에 없는 추상적인 의미에서 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="0e6f7-108">The term key is used here in the abstract sense, not in the literal sense of "cryptographic key material".</span></span> <span data-ttu-id="0e6f7-109">키에 다음 속성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0e6f7-109">A key has the following properties:</span></span>

* <span data-ttu-id="0e6f7-110">활성화, 생성 및 만료 날짜</span><span class="sxs-lookup"><span data-stu-id="0e6f7-110">Activation, creation, and expiration dates</span></span>

* <span data-ttu-id="0e6f7-111">해지 상태</span><span class="sxs-lookup"><span data-stu-id="0e6f7-111">Revocation status</span></span>

* <span data-ttu-id="0e6f7-112">키 식별자 (GUID)</span><span class="sxs-lookup"><span data-stu-id="0e6f7-112">Key identifier (a GUID)</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="0e6f7-113">ASP.NET 2.x 핵심</span><span class="sxs-lookup"><span data-stu-id="0e6f7-113">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="0e6f7-114">IKey 만드는 데 사용할 수 있는 CreateEncryptor 메서드를 노출 하는 또한는 [IAuthenticatedEncryptor](core-crypto.md#data-protection-extensibility-core-crypto-iauthenticatedencryptor) 인스턴스가이 키에 연결 합니다.</span><span class="sxs-lookup"><span data-stu-id="0e6f7-114">Additionally, IKey exposes a CreateEncryptor method which can be used to create an [IAuthenticatedEncryptor](core-crypto.md#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance tied to this key.</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="0e6f7-115">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="0e6f7-115">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="0e6f7-116">IKey 만드는 데 사용할 수 있는 CreateEncryptorInstance 메서드를 노출 하는 또한는 [IAuthenticatedEncryptor](core-crypto.md#data-protection-extensibility-core-crypto-iauthenticatedencryptor) 인스턴스가이 키에 연결 합니다.</span><span class="sxs-lookup"><span data-stu-id="0e6f7-116">Additionally, IKey exposes a CreateEncryptorInstance method which can be used to create an [IAuthenticatedEncryptor](core-crypto.md#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance tied to this key.</span></span>

---

> [!NOTE]
> <span data-ttu-id="0e6f7-117">API IKey 인스턴스로부터 원시 암호화 된 자료는 검색할 수는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="0e6f7-117">There is no API to retrieve the raw cryptographic material from an IKey instance.</span></span>

## <a name="ikeymanager"></a><span data-ttu-id="0e6f7-118">IKeyManager</span><span class="sxs-lookup"><span data-stu-id="0e6f7-118">IKeyManager</span></span>

<span data-ttu-id="0e6f7-119">IKeyManager 인터페이스 일반 키 저장소, 검색 및 조작 하는 일을 담당 하는 개체를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="0e6f7-119">The IKeyManager interface represents an object responsible for general key storage, retrieval, and manipulation.</span></span> <span data-ttu-id="0e6f7-120">세 가지 상위 수준 작업을 노출 합니다.</span><span class="sxs-lookup"><span data-stu-id="0e6f7-120">It exposes three high-level operations:</span></span>

* <span data-ttu-id="0e6f7-121">새 키를 만들고 저장소를 유지 합니다.</span><span class="sxs-lookup"><span data-stu-id="0e6f7-121">Create a new key and persist it to storage.</span></span>

* <span data-ttu-id="0e6f7-122">저장소에서 모든 키를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="0e6f7-122">Get all keys from storage.</span></span>

* <span data-ttu-id="0e6f7-123">하나 이상의 키를 해지 하 고 저장소는 해지 정보를 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="0e6f7-123">Revoke one or more keys and persist the revocation information to storage.</span></span>

>[!WARNING]
> <span data-ttu-id="0e6f7-124">IKeyManager를 작성 하는 고급 작업 및 대부분의 개발자는 시도 하지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="0e6f7-124">Writing an IKeyManager is a very advanced task, and the majority of developers should not attempt it.</span></span> <span data-ttu-id="0e6f7-125">대신 대부분의 개발자가 제공 하는 기능 사용 해야는 [XmlKeyManager](xref:security/data-protection/extensibility/key-management#data-protection-extensibility-key-management-xmlkeymanager) 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="0e6f7-125">Instead, most developers should take advantage of the facilities offered by the [XmlKeyManager](xref:security/data-protection/extensibility/key-management#data-protection-extensibility-key-management-xmlkeymanager) class.</span></span>

<a name=data-protection-extensibility-key-management-xmlkeymanager></a>

## <a name="xmlkeymanager"></a><span data-ttu-id="0e6f7-126">XmlKeyManager</span><span class="sxs-lookup"><span data-stu-id="0e6f7-126">XmlKeyManager</span></span>

<span data-ttu-id="0e6f7-127">XmlKeyManager 형식은 IKeyManager의 기본 구체적인 구현입니다.</span><span class="sxs-lookup"><span data-stu-id="0e6f7-127">The XmlKeyManager type is the in-box concrete implementation of IKeyManager.</span></span> <span data-ttu-id="0e6f7-128">키 위탁 및 대기 키의 암호화를 포함 하 여 몇 가지 유용한 기능을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="0e6f7-128">It provides several useful facilities, including key escrow and encryption of keys at rest.</span></span> <span data-ttu-id="0e6f7-129">이 시스템에 있는 키 XML 요소로 표시 됩니다 (특히 [XElement](https://msdn.microsoft.com/library/system.xml.linq.xelement(v=vs.110).aspx)).</span><span class="sxs-lookup"><span data-stu-id="0e6f7-129">Keys in this system are represented as XML elements (specifically, [XElement](https://msdn.microsoft.com/library/system.xml.linq.xelement(v=vs.110).aspx)).</span></span>

<span data-ttu-id="0e6f7-130">XmlKeyManager 해당 작업을 수행 하는 과정에서 다른 여러 가지 구성 요소에 따라 달라 집니다.</span><span class="sxs-lookup"><span data-stu-id="0e6f7-130">XmlKeyManager depends on several other components in the course of fulfilling its tasks:</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="0e6f7-131">ASP.NET 2.x 핵심</span><span class="sxs-lookup"><span data-stu-id="0e6f7-131">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

* <span data-ttu-id="0e6f7-132">AlgorithmConfiguration 새 키로 사용 되는 알고리즘을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="0e6f7-132">AlgorithmConfiguration, which dictates the algorithms used by new keys.</span></span>

* <span data-ttu-id="0e6f7-133">IXmlRepository, 키 저장소에 유지 되는 위치를 제어 합니다.</span><span class="sxs-lookup"><span data-stu-id="0e6f7-133">IXmlRepository, which controls where keys are persisted in storage.</span></span>

* <span data-ttu-id="0e6f7-134">IXmlEncryptor [선택 사항]를 통해 저장 된 상태의 키를 암호화할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0e6f7-134">IXmlEncryptor [optional], which allows encrypting keys at rest.</span></span>

* <span data-ttu-id="0e6f7-135">IKeyEscrowSink [선택 사항] 키 에스크로 서비스를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="0e6f7-135">IKeyEscrowSink [optional], which provides key escrow services.</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="0e6f7-136">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="0e6f7-136">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

* <span data-ttu-id="0e6f7-137">IXmlRepository, 키 저장소에 유지 되는 위치를 제어 합니다.</span><span class="sxs-lookup"><span data-stu-id="0e6f7-137">IXmlRepository, which controls where keys are persisted in storage.</span></span>

* <span data-ttu-id="0e6f7-138">IXmlEncryptor [선택 사항]를 통해 저장 된 상태의 키를 암호화할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0e6f7-138">IXmlEncryptor [optional], which allows encrypting keys at rest.</span></span>

* <span data-ttu-id="0e6f7-139">IKeyEscrowSink [선택 사항] 키 에스크로 서비스를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="0e6f7-139">IKeyEscrowSink [optional], which provides key escrow services.</span></span>

---

<span data-ttu-id="0e6f7-140">다음은 이러한 구성 요소는 연결 되는 방식을 함께 XmlKeyManager 내 나타내는 개략적인 다이어그램입니다.</span><span class="sxs-lookup"><span data-stu-id="0e6f7-140">Below are high-level diagrams which indicate how these components are wired together within XmlKeyManager.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="0e6f7-141">ASP.NET 2.x 핵심</span><span class="sxs-lookup"><span data-stu-id="0e6f7-141">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

   ![키 만들기](key-management/_static/keycreation2.png)

   <span data-ttu-id="0e6f7-143">*키 생성 / CreateNewKey*</span><span class="sxs-lookup"><span data-stu-id="0e6f7-143">*Key Creation / CreateNewKey*</span></span>

<span data-ttu-id="0e6f7-144">CreateNewKey 구현의 AlgorithmConfiguration 구성 요소는 다음 XML로 serialize 하는 고유한 IAuthenticatedEncryptorDescriptor 만들 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="0e6f7-144">In the implementation of CreateNewKey, the AlgorithmConfiguration component is used to create a unique IAuthenticatedEncryptorDescriptor, which is then serialized as XML.</span></span> <span data-ttu-id="0e6f7-145">키 에스크로 싱크가 있는 경우 원시 (암호화 되지 않은) XML 장기 저장에 대 한 싱크에 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="0e6f7-145">If a key escrow sink is present, the raw (unencrypted) XML is provided to the sink for long-term storage.</span></span> <span data-ttu-id="0e6f7-146">암호화 되지 않은 XML 그런 다음 실행 됩니다는 IXmlEncryptor 통해 (필요한 경우)를 암호화 된 XML 문서를 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="0e6f7-146">The unencrypted XML is then run through an IXmlEncryptor (if required) to generate the encrypted XML document.</span></span> <span data-ttu-id="0e6f7-147">암호화 된이 문서는 IXmlRepository 통해 장기 저장소에 유지 됩니다.</span><span class="sxs-lookup"><span data-stu-id="0e6f7-147">This encrypted document is persisted to long-term storage via the IXmlRepository.</span></span> <span data-ttu-id="0e6f7-148">(없음 IXmlEncryptor 구성 된 경우 암호화 되지 않은 문서에서에서 유지 되는 IXmlRepository.)</span><span class="sxs-lookup"><span data-stu-id="0e6f7-148">(If no IXmlEncryptor is configured, the unencrypted document is persisted in the IXmlRepository.)</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="0e6f7-149">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="0e6f7-149">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

   ![키 만들기](key-management/_static/keycreation1.png)

   <span data-ttu-id="0e6f7-151">*키 생성 / CreateNewKey*</span><span class="sxs-lookup"><span data-stu-id="0e6f7-151">*Key Creation / CreateNewKey*</span></span>

<span data-ttu-id="0e6f7-152">CreateNewKey 구현의 IAuthenticatedEncryptorConfiguration 구성 요소는 다음 XML로 serialize 하는 고유한 IAuthenticatedEncryptorDescriptor 만들 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="0e6f7-152">In the implementation of CreateNewKey, the IAuthenticatedEncryptorConfiguration component is used to create a unique IAuthenticatedEncryptorDescriptor, which is then serialized as XML.</span></span> <span data-ttu-id="0e6f7-153">키 에스크로 싱크가 있는 경우 원시 (암호화 되지 않은) XML 장기 저장에 대 한 싱크에 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="0e6f7-153">If a key escrow sink is present, the raw (unencrypted) XML is provided to the sink for long-term storage.</span></span> <span data-ttu-id="0e6f7-154">암호화 되지 않은 XML 그런 다음 실행 됩니다는 IXmlEncryptor 통해 (필요한 경우)를 암호화 된 XML 문서를 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="0e6f7-154">The unencrypted XML is then run through an IXmlEncryptor (if required) to generate the encrypted XML document.</span></span> <span data-ttu-id="0e6f7-155">암호화 된이 문서는 IXmlRepository 통해 장기 저장소에 유지 됩니다.</span><span class="sxs-lookup"><span data-stu-id="0e6f7-155">This encrypted document is persisted to long-term storage via the IXmlRepository.</span></span> <span data-ttu-id="0e6f7-156">(없음 IXmlEncryptor 구성 된 경우 암호화 되지 않은 문서에서에서 유지 되는 IXmlRepository.)</span><span class="sxs-lookup"><span data-stu-id="0e6f7-156">(If no IXmlEncryptor is configured, the unencrypted document is persisted in the IXmlRepository.)</span></span>

---

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="0e6f7-157">ASP.NET 2.x 핵심</span><span class="sxs-lookup"><span data-stu-id="0e6f7-157">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

   ![키 검색](key-management/_static/keyretrieval2.png)
   
# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="0e6f7-159">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="0e6f7-159">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

   ![키 검색](key-management/_static/keyretrieval1.png)

---

   <span data-ttu-id="0e6f7-161">*검색 키 / GetAllKeys*</span><span class="sxs-lookup"><span data-stu-id="0e6f7-161">*Key Retrieval / GetAllKeys*</span></span>

<span data-ttu-id="0e6f7-162">GetAllKeys 구현에서 기본 IXmlRepository에서 키 및 해지를 나타내는 XML 문서를 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="0e6f7-162">In the implementation of GetAllKeys, the XML documents representing keys and revocations are read from the underlying IXmlRepository.</span></span> <span data-ttu-id="0e6f7-163">이러한 문서를 암호화 하는 경우 시스템은 자동으로 암호 해독할 합니다.</span><span class="sxs-lookup"><span data-stu-id="0e6f7-163">If these documents are encrypted, the system will automatically decrypt them.</span></span> <span data-ttu-id="0e6f7-164">XmlKeyManager 다시 인스턴스로 IAuthenticatedEncryptorDescriptor, 그런 다음 개별 IKey 인스턴스에 래핑되는 문서를 deserialize 하는 데 적절 한 IAuthenticatedEncryptorDescriptorDeserializer 인스턴스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="0e6f7-164">XmlKeyManager creates the appropriate IAuthenticatedEncryptorDescriptorDeserializer instances to deserialize the documents back into IAuthenticatedEncryptorDescriptor instances, which are then wrapped in individual IKey instances.</span></span> <span data-ttu-id="0e6f7-165">이 컬렉션의 IKey 인스턴스 호출자에 게 반환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="0e6f7-165">This collection of IKey instances is returned to the caller.</span></span>

<span data-ttu-id="0e6f7-166">특정 XML 요소에 대 한 자세한 정보를 찾을 수는 [키 저장소 형식 문서](../implementation/key-storage-format.md#data-protection-implementation-key-storage-format)합니다.</span><span class="sxs-lookup"><span data-stu-id="0e6f7-166">Further information on the particular XML elements can be found in the [key storage format document](../implementation/key-storage-format.md#data-protection-implementation-key-storage-format).</span></span>

## <a name="ixmlrepository"></a><span data-ttu-id="0e6f7-167">IXmlRepository</span><span class="sxs-lookup"><span data-stu-id="0e6f7-167">IXmlRepository</span></span>

<span data-ttu-id="0e6f7-168">IXmlRepository 인터페이스에는 XML을 유지 하 고 백업 저장소에서 XML을 검색할 수 있는 형식을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="0e6f7-168">The IXmlRepository interface represents a type that can persist XML to and retrieve XML from a backing store.</span></span> <span data-ttu-id="0e6f7-169">두 개의 Api를 노출합니다.</span><span class="sxs-lookup"><span data-stu-id="0e6f7-169">It exposes two APIs:</span></span>

* <span data-ttu-id="0e6f7-170">GetAllElements(): IReadOnlyCollection<XElement></span><span class="sxs-lookup"><span data-stu-id="0e6f7-170">GetAllElements() : IReadOnlyCollection<XElement></span></span>

* <span data-ttu-id="0e6f7-171">StoreElement (XElement 요소, 문자열 friendlyName)</span><span class="sxs-lookup"><span data-stu-id="0e6f7-171">StoreElement(XElement element, string friendlyName)</span></span>

<span data-ttu-id="0e6f7-172">IXmlRepository의 구현을 통해 전달 되는 XML을 구문 분석할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="0e6f7-172">Implementations of IXmlRepository don't need to parse the XML passing through them.</span></span> <span data-ttu-id="0e6f7-173">불투명으로 XML 문서 처리를 생성 하 고 문서를 구문 분석 하는 방법에 대 한 걱정 더 높은 계층을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="0e6f7-173">They should treat the XML documents as opaque and let higher layers worry about generating and parsing the documents.</span></span>

<span data-ttu-id="0e6f7-174">기본 제공 구체적인 순위가 IXmlRepository를 구현 하는: FileSystemXmlRepository 및 RegistryXmlRepository 합니다.</span><span class="sxs-lookup"><span data-stu-id="0e6f7-174">There are two built-in concrete types which implement IXmlRepository: FileSystemXmlRepository and RegistryXmlRepository.</span></span> <span data-ttu-id="0e6f7-175">참조는 [키 저장소 공급자 문서](../implementation/key-storage-providers.md#data-protection-implementation-key-storage-providers) 자세한 정보에 대 한 합니다.</span><span class="sxs-lookup"><span data-stu-id="0e6f7-175">See the [key storage providers document](../implementation/key-storage-providers.md#data-protection-implementation-key-storage-providers) for more information.</span></span> <span data-ttu-id="0e6f7-176">사용자 지정 IXmlRepository 등록 것 될 다른 사용 하려면 적절 한 방식으로 백업 저장소, 예: Azure Blob 저장소입니다.</span><span class="sxs-lookup"><span data-stu-id="0e6f7-176">Registering a custom IXmlRepository would be the appropriate manner to use a different backing store, e.g., Azure Blob Storage.</span></span> <span data-ttu-id="0e6f7-177">기본 저장소 응용 프로그램 전체를 변경 하려면 사용자 지정 singleton IXmlRepository 서비스 공급자에 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="0e6f7-177">To change the default repository application-wide, register a custom singleton IXmlRepository in the service provider.</span></span>

<a name=data-protection-extensibility-key-management-ixmlencryptor></a>

## <a name="ixmlencryptor"></a><span data-ttu-id="0e6f7-178">IXmlEncryptor</span><span class="sxs-lookup"><span data-stu-id="0e6f7-178">IXmlEncryptor</span></span>

<span data-ttu-id="0e6f7-179">IXmlEncryptor 인터페이스에는 일반 텍스트 XML 요소를 암호화할 수 있는 형식을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="0e6f7-179">The IXmlEncryptor interface represents a type that can encrypt a plaintext XML element.</span></span> <span data-ttu-id="0e6f7-180">단일 API를 노출합니다.</span><span class="sxs-lookup"><span data-stu-id="0e6f7-180">It exposes a single API:</span></span>

* <span data-ttu-id="0e6f7-181">(XElement plaintextElement) 암호화: EncryptedXmlInfo</span><span class="sxs-lookup"><span data-stu-id="0e6f7-181">Encrypt(XElement plaintextElement) : EncryptedXmlInfo</span></span>

<span data-ttu-id="0e6f7-182">Serialize 된 IAuthenticatedEncryptorDescriptor에 "암호화 필요"로 표시 된 요소가 들어 있으면 그런 다음 XmlKeyManager는 해당 요소는 구성 된 IXmlEncryptor 암호화 메서드를 통해를 실행 서 암호화 요소 대신 계속 유지 됩니다. 보다는 IXmlRepository 일반 텍스트 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="0e6f7-182">If a serialized IAuthenticatedEncryptorDescriptor contains any elements marked as "requires encryption", then XmlKeyManager will run those elements through the configured IXmlEncryptor's Encrypt method, and it will persist the enciphered element rather than the plaintext element to the IXmlRepository.</span></span> <span data-ttu-id="0e6f7-183">Encrypt 메서드의 출력은 EncryptedXmlInfo 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="0e6f7-183">The output of the Encrypt method is an EncryptedXmlInfo object.</span></span> <span data-ttu-id="0e6f7-184">이 개체는 모두 결과 서 암호화 XElement와의 해당 요소를 해독 하는 데 사용할 수 있는 IXmlDecryptor를 나타내는 형식을 포함 하는 래퍼입니다.</span><span class="sxs-lookup"><span data-stu-id="0e6f7-184">This object is a wrapper which contains both the resultant enciphered XElement and the Type which represents an IXmlDecryptor which can be used to decipher the corresponding element.</span></span>

<span data-ttu-id="0e6f7-185">네 가지 기본 제공 구체적인 유형 IXmlEncryptor를 구현 하는: CertificateXmlEncryptor, DpapiNGXmlEncryptor, DpapiXmlEncryptor, 및 NullXmlEncryptor 합니다.</span><span class="sxs-lookup"><span data-stu-id="0e6f7-185">There are four built-in concrete types which implement IXmlEncryptor: CertificateXmlEncryptor, DpapiNGXmlEncryptor, DpapiXmlEncryptor, and NullXmlEncryptor.</span></span> <span data-ttu-id="0e6f7-186">참조는 [rest 문서에서 키 암호화](../implementation/key-encryption-at-rest.md#data-protection-implementation-key-encryption-at-rest) 자세한 정보에 대 한 합니다.</span><span class="sxs-lookup"><span data-stu-id="0e6f7-186">See the [key encryption at rest document](../implementation/key-encryption-at-rest.md#data-protection-implementation-key-encryption-at-rest) for more information.</span></span> <span data-ttu-id="0e6f7-187">전체 응용 프로그램의 기본 휴지 키 암호화 메커니즘을 변경 하려면 사용자 지정 singleton IXmlEncryptor 서비스 공급자에 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="0e6f7-187">To change the default key-encryption-at-rest mechanism application-wide, register a custom singleton IXmlEncryptor in the service provider.</span></span>

## <a name="ixmldecryptor"></a><span data-ttu-id="0e6f7-188">IXmlDecryptor</span><span class="sxs-lookup"><span data-stu-id="0e6f7-188">IXmlDecryptor</span></span>

<span data-ttu-id="0e6f7-189">IXmlDecryptor XElement 서 된 암호화는 IXmlEncryptor 통해를 해독 하는 방법을 알고 있는 형식을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="0e6f7-189">The IXmlDecryptor interface represents a type that knows how to decrypt an XElement that was enciphered via an IXmlEncryptor.</span></span> <span data-ttu-id="0e6f7-190">단일 API를 노출합니다.</span><span class="sxs-lookup"><span data-stu-id="0e6f7-190">It exposes a single API:</span></span>

* <span data-ttu-id="0e6f7-191">(XElement encryptedElement) 암호 해독: XElement</span><span class="sxs-lookup"><span data-stu-id="0e6f7-191">Decrypt(XElement encryptedElement) : XElement</span></span>

<span data-ttu-id="0e6f7-192">암호 해독 메서드 IXmlEncryptor.Encrypt에서 수행 된 암호화 실행 취소 합니다.</span><span class="sxs-lookup"><span data-stu-id="0e6f7-192">The Decrypt method undoes the encryption performed by IXmlEncryptor.Encrypt.</span></span> <span data-ttu-id="0e6f7-193">일반적으로 각 구체적인 IXmlEncryptor 구현은 해당 구체적인 IXmlDecryptor 구현을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="0e6f7-193">Generally each concrete IXmlEncryptor implementation will have a corresponding concrete IXmlDecryptor implementation.</span></span>

<span data-ttu-id="0e6f7-194">IXmlDecryptor를 구현 하는 형식에는 다음 두 명의 공용 생성자 중 하나 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="0e6f7-194">Types which implement IXmlDecryptor should have one of the following two public constructors:</span></span>

* <span data-ttu-id="0e6f7-195">.ctor(IServiceProvider)</span><span class="sxs-lookup"><span data-stu-id="0e6f7-195">.ctor(IServiceProvider)</span></span>

* <span data-ttu-id="0e6f7-196">.ctor()</span><span class="sxs-lookup"><span data-stu-id="0e6f7-196">.ctor()</span></span>

> [!NOTE]
> <span data-ttu-id="0e6f7-197">생성자에 전달 IServiceProvider는 null 일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0e6f7-197">The IServiceProvider passed to the constructor may be null.</span></span>

## <a name="ikeyescrowsink"></a><span data-ttu-id="0e6f7-198">IKeyEscrowSink</span><span class="sxs-lookup"><span data-stu-id="0e6f7-198">IKeyEscrowSink</span></span>

<span data-ttu-id="0e6f7-199">IKeyEscrowSink 에스크로 중요 한 정보를 수행할 수 있는 형식을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="0e6f7-199">The IKeyEscrowSink interface represents a type that can perform escrow of sensitive information.</span></span> <span data-ttu-id="0e6f7-200">Serialize 된 설명자 (예: 암호화 자료)가 중요 한 정보가 포함 될 수 있습니다 및 도입이 이것이 회수는 [IXmlEncryptor](xref:security/data-protection/extensibility/key-management#data-protection-extensibility-key-management-ixmlencryptor) 처음에 입력 합니다.</span><span class="sxs-lookup"><span data-stu-id="0e6f7-200">Recall that serialized descriptors might contain sensitive information (such as cryptographic material), and this is what led to the introduction of the [IXmlEncryptor](xref:security/data-protection/extensibility/key-management#data-protection-extensibility-key-management-ixmlencryptor) type in the first place.</span></span> <span data-ttu-id="0e6f7-201">그러나 실수로 인해가 오류가 발생할 및 keyrings 삭제할 수 있습니다 또는 손상 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="0e6f7-201">However, accidents happen, and keyrings can be deleted or become corrupted.</span></span>

<span data-ttu-id="0e6f7-202">에스크로 인터페이스에 구성 된 모든에서 변환 되기 전에 원시 serialize 된 XML에 대 한 액세스를 허용 하는 응급 이스케이프 해치 제공 [IXmlEncryptor](xref:security/data-protection/extensibility/key-management#data-protection-extensibility-key-management-ixmlencryptor)합니다.</span><span class="sxs-lookup"><span data-stu-id="0e6f7-202">The escrow interface provides an emergency escape hatch, allowing access to the raw serialized XML before it is transformed by any configured [IXmlEncryptor](xref:security/data-protection/extensibility/key-management#data-protection-extensibility-key-management-ixmlencryptor).</span></span> <span data-ttu-id="0e6f7-203">인터페이스는 단일 API를 노출합니다.</span><span class="sxs-lookup"><span data-stu-id="0e6f7-203">The interface exposes a single API:</span></span>

* <span data-ttu-id="0e6f7-204">저장소 (Guid keyId, XElement 요소)</span><span class="sxs-lookup"><span data-stu-id="0e6f7-204">Store(Guid keyId, XElement element)</span></span>

<span data-ttu-id="0e6f7-205">안전한 비즈니스 정책을 사용 하 여 일관 된 방식으로 제공된 된 요소를 처리 하는 데 IKeyEscrowSink 구현 됩니다.</span><span class="sxs-lookup"><span data-stu-id="0e6f7-205">It is up to the IKeyEscrowSink implementation to handle the provided element in a secure manner consistent with business policy.</span></span> <span data-ttu-id="0e6f7-206">여기서 인증서의 개인 키가 에스크로 되었습니다; 알려진된 회사 X.509 인증서를 사용 하 여 XML 요소를 암호화 하려면 에스크로 싱크에 대 한 한 가지 구현 될 수 있습니다. 이 CertificateXmlEncryptor 유형을 지원할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0e6f7-206">One possible implementation could be for the escrow sink to encrypt the XML element using a known corporate X.509 certificate where the certificate's private key has been escrowed; the CertificateXmlEncryptor type can assist with this.</span></span> <span data-ttu-id="0e6f7-207">또한 IKeyEscrowSink 구현은 제공된 된 요소를 적절 하 게 유지 합니다.</span><span class="sxs-lookup"><span data-stu-id="0e6f7-207">The IKeyEscrowSink implementation is also responsible for persisting the provided element appropriately.</span></span>

<span data-ttu-id="0e6f7-208">기본적으로 에스크로 메커니즘이 없으므로 활성화, 서버 관리자 수 있지만 [전체적으로이 옵션을 구성](../configuration/machine-wide-policy.md#data-protection-configuration-machinewidepolicy)합니다.</span><span class="sxs-lookup"><span data-stu-id="0e6f7-208">By default no escrow mechanism is enabled, though server administrators can [configure this globally](../configuration/machine-wide-policy.md#data-protection-configuration-machinewidepolicy).</span></span> <span data-ttu-id="0e6f7-209">도 구성할 수 있습니다 프로그래밍 방식으로 통해는 *IDataProtectionBuilder.AddKeyEscrowSink* 아래 예제와 같이 메서드.</span><span class="sxs-lookup"><span data-stu-id="0e6f7-209">It can also be configured programmatically via the *IDataProtectionBuilder.AddKeyEscrowSink* method as shown in the sample below.</span></span> <span data-ttu-id="0e6f7-210">*AddKeyEscrowSink* 메서드 오버 로드 미러는 *IServiceCollection.AddSingleton* 및 *IServiceCollection.AddInstance* IKeyEscrowSink와 오버 로드 인스턴스는 단일 항목으로 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="0e6f7-210">The *AddKeyEscrowSink* method overloads mirror the *IServiceCollection.AddSingleton* and *IServiceCollection.AddInstance* overloads, as IKeyEscrowSink instances are intended to be singletons.</span></span> <span data-ttu-id="0e6f7-211">여러 IKeyEscrowSink 인스턴스가 등록 되어 있으면 여러 메커니즘을 동시에 키를 위탁 수 있으므로 각 키 생성 시 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="0e6f7-211">If multiple IKeyEscrowSink instances are registered, each one will be called during key generation, so keys can be escrowed to multiple mechanisms simultaneously.</span></span>

<span data-ttu-id="0e6f7-212">API 자료 IKeyEscrowSink 인스턴스에서 읽을 수는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="0e6f7-212">There is no API to read material from an IKeyEscrowSink instance.</span></span> <span data-ttu-id="0e6f7-213">이 에스크로 메커니즘의 디자인 이론 일치: 키 자료를 신뢰할 수 있는 기관에 액세스할 수 있도록 하기 위한 것이 하며 응용 프로그램은 자체 아니므로 신뢰할 수 있는 기관, 자체 escrowed 자료에 대 한 액세스가 하지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="0e6f7-213">This is consistent with the design theory of the escrow mechanism: it's intended to make the key material accessible to a trusted authority, and since the application is itself not a trusted authority, it shouldn't have access to its own escrowed material.</span></span>

<span data-ttu-id="0e6f7-214">다음 샘플 코드 만들고 "CONTOSODomain Admins"의 구성원만 복구할 수는 키는 위탁 되는 위치는 IKeyEscrowSink 등록 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="0e6f7-214">The following sample code demonstrates creating and registering an IKeyEscrowSink where keys are escrowed such that only members of "CONTOSODomain Admins" can recover them.</span></span>

> [!NOTE]
> <span data-ttu-id="0e6f7-215">이 샘플을 실행 하려면 여야 도메인에 가입 된 Windows 8에서 Windows Server 2012 컴퓨터와 도메인 컨트롤러에는 Windows Server 2012 이상 이어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="0e6f7-215">To run this sample, you must be on a domain-joined Windows 8 / Windows Server 2012 machine, and the domain controller must be Windows Server 2012 or later.</span></span>

<span data-ttu-id="0e6f7-216">[!code-none[Main](key-management/samples/key-management-extensibility.cs)]</span><span class="sxs-lookup"><span data-stu-id="0e6f7-216">[!code-none[Main](key-management/samples/key-management-extensibility.cs)]</span></span>
