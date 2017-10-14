---
title: "핵심 암호화 확장성"
author: rick-anderson
description: "IAuthenticatedEncryptor, IAuthenticatedEncryptorDescriptor, IAuthenticatedEncryptorDescriptorDeserializer, 및 최상위 팩터리에 설명합니다."
keywords: ASP.NET Core, IAuthenticatedEncryptor, IAuthenticatedEncryptorDescriptor, IAuthenticatedEncryptorDescriptorDeserializer
ms.author: riande
manager: wpickett
ms.date: 8/11/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/extensibility/core-crypto
ms.openlocfilehash: 738bf6841b5364f89fa1bcdd11fc71d9be83c23b
ms.sourcegitcommit: 8f4d4fad1ca27adf9e396f5c205c9875a3963664
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/13/2017
---
# <a name="core-cryptography-extensibility"></a><span data-ttu-id="57e5f-104">핵심 암호화 확장성</span><span class="sxs-lookup"><span data-stu-id="57e5f-104">Core cryptography extensibility</span></span>

<a name="data-protection-extensibility-core-crypto"></a>

>[!WARNING]
> <span data-ttu-id="57e5f-105">다음 인터페이스 중 하나를 구현 하는 형식은 스레드로부터 안전 해야 합니다. 여러 호출자에 대 한 합니다.</span><span class="sxs-lookup"><span data-stu-id="57e5f-105">Types that implement any of the following interfaces should be thread-safe for multiple callers.</span></span>

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptor"></a>

## <a name="iauthenticatedencryptor"></a><span data-ttu-id="57e5f-106">IAuthenticatedEncryptor</span><span class="sxs-lookup"><span data-stu-id="57e5f-106">IAuthenticatedEncryptor</span></span>

<span data-ttu-id="57e5f-107">**IAuthenticatedEncryptor** 인터페이스는 암호화 하위 시스템의 기본 빌딩 블록입니다.</span><span class="sxs-lookup"><span data-stu-id="57e5f-107">The **IAuthenticatedEncryptor** interface is the basic building block of the cryptographic subsystem.</span></span> <span data-ttu-id="57e5f-108">일반적으로 키 당 하나의 IAuthenticatedEncryptor 및 모든 암호화 키 자료 및 암호화 작업을 수행 하는 데 필요한 정보를 알고리즘 IAuthenticatedEncryptor 인스턴스 래핑합니다.</span><span class="sxs-lookup"><span data-stu-id="57e5f-108">There is generally one IAuthenticatedEncryptor per key, and the IAuthenticatedEncryptor instance wraps all cryptographic key material and algorithmic information necessary to perform cryptographic operations.</span></span>

<span data-ttu-id="57e5f-109">해당 이름에서 알 수 있듯이 형식은 인증 된 암호화 및 암호 해독 서비스 제공 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="57e5f-109">As its name suggests, the type is responsible for providing authenticated encryption and decryption services.</span></span> <span data-ttu-id="57e5f-110">다음 두 가지 Api를 노출합니다.</span><span class="sxs-lookup"><span data-stu-id="57e5f-110">It exposes the following two APIs.</span></span>

* <span data-ttu-id="57e5f-111">암호 해독 (ArraySegment<byte> ArraySegment 암호화 텍스트<byte> additionalAuthenticatedData): byte</span><span class="sxs-lookup"><span data-stu-id="57e5f-111">Decrypt(ArraySegment<byte> ciphertext, ArraySegment<byte> additionalAuthenticatedData) : byte[]</span></span>

* <span data-ttu-id="57e5f-112">암호화 (ArraySegment<byte> 일반 텍스트, ArraySegment<byte> additionalAuthenticatedData): byte</span><span class="sxs-lookup"><span data-stu-id="57e5f-112">Encrypt(ArraySegment<byte> plaintext, ArraySegment<byte> additionalAuthenticatedData) : byte[]</span></span>

<span data-ttu-id="57e5f-113">암호화 메서드 서 암호화 일반 텍스트와 인증 태그를 포함 하는 blob를 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="57e5f-113">The Encrypt method returns a blob that includes the enciphered plaintext and an authentication tag.</span></span> <span data-ttu-id="57e5f-114">인증 태그 AAD 자체 필요한 마지막 페이로드에서 복구할 수 없는 경우에 추가 인증 된 데이터 (AAD)를 포함 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="57e5f-114">The authentication tag must encompass the additional authenticated data (AAD), though the AAD itself need not be recoverable from the final payload.</span></span> <span data-ttu-id="57e5f-115">암호 해독 메서드 인증 태그의 유효성을 검사 하 고 해독된 페이로드를 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="57e5f-115">The Decrypt method validates the authentication tag and returns the deciphered payload.</span></span> <span data-ttu-id="57e5f-116">CryptographicException를 (ArgumentNullException 제외 하 고 및 유사한 항목과) 모든 오류를 homogenized 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="57e5f-116">All failures (except ArgumentNullException and similar) should be homogenized to CryptographicException.</span></span>

> [!NOTE]
> <span data-ttu-id="57e5f-117">IAuthenticatedEncryptor 인스턴스 자체 키 자료를 포함 하도록 실제로 필요 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="57e5f-117">The IAuthenticatedEncryptor instance itself doesn't actually need to contain the key material.</span></span> <span data-ttu-id="57e5f-118">예를 들어, 모든 작업에 대 한 HSM 구현 위임 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57e5f-118">For example, the implementation could delegate to an HSM for all operations.</span></span>

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptorfactory"></a>
<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor"></a>

## <a name="how-to-create-an-iauthenticatedencryptor"></a><span data-ttu-id="57e5f-119">IAuthenticatedEncryptor를 만드는 방법</span><span class="sxs-lookup"><span data-stu-id="57e5f-119">How to create an IAuthenticatedEncryptor</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="57e5f-120">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="57e5f-120">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="57e5f-121">**IAuthenticatedEncryptorFactory** 인터페이스를 만드는 방법을 알고 있는 형식을 나타냅니다는 [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) 인스턴스.</span><span class="sxs-lookup"><span data-stu-id="57e5f-121">The **IAuthenticatedEncryptorFactory** interface represents a type that knows how to create an [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance.</span></span> <span data-ttu-id="57e5f-122">API는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="57e5f-122">Its API is as follows.</span></span>

* <span data-ttu-id="57e5f-123">CreateEncryptorInstance (IKey 키): IAuthenticatedEncryptor</span><span class="sxs-lookup"><span data-stu-id="57e5f-123">CreateEncryptorInstance(IKey key) : IAuthenticatedEncryptor</span></span>

<span data-ttu-id="57e5f-124">지정된 된 IKey 인스턴스에 대 한 해당 CreateEncryptorInstance 메서드에 의해 만들어진 모든 인증 된 암호기 고려할에서 같이 해당는 아래 코드 샘플입니다.</span><span class="sxs-lookup"><span data-stu-id="57e5f-124">For any given IKey instance, any authenticated encryptors created by its CreateEncryptorInstance method should be considered equivalent, as in the below code sample.</span></span>

```csharp
// we have an IAuthenticatedEncryptorFactory instance and an IKey instance
IAuthenticatedEncryptorFactory factory = ...;
IKey key = ...;

// get an encryptor instance and perform an authenticated encryption operation
ArraySegment<byte> plaintext = new ArraySegment<byte>(Encoding.UTF8.GetBytes("plaintext"));
ArraySegment<byte> aad = new ArraySegment<byte>(Encoding.UTF8.GetBytes("AAD"));
var encryptor1 = factory.CreateEncryptorInstance(key);
byte[] ciphertext = encryptor1.Encrypt(plaintext, aad);

// get another encryptor instance and perform an authenticated decryption operation
var encryptor2 = factory.CreateEncryptorInstance(key);
byte[] roundTripped = encryptor2.Decrypt(new ArraySegment<byte>(ciphertext), aad);


// the 'roundTripped' and 'plaintext' buffers should be equivalent
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="57e5f-125">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="57e5f-125">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="57e5f-126">**IAuthenticatedEncryptorDescriptor** 인터페이스를 만드는 방법을 알고 있는 형식을 나타냅니다는 [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) 인스턴스.</span><span class="sxs-lookup"><span data-stu-id="57e5f-126">The **IAuthenticatedEncryptorDescriptor** interface represents a type that knows how to create an [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance.</span></span> <span data-ttu-id="57e5f-127">API는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="57e5f-127">Its API is as follows.</span></span>

* <span data-ttu-id="57e5f-128">CreateEncryptorInstance(): IAuthenticatedEncryptor</span><span class="sxs-lookup"><span data-stu-id="57e5f-128">CreateEncryptorInstance() : IAuthenticatedEncryptor</span></span>

* <span data-ttu-id="57e5f-129">ExportToXml(): XmlSerializedDescriptorInfo</span><span class="sxs-lookup"><span data-stu-id="57e5f-129">ExportToXml() : XmlSerializedDescriptorInfo</span></span>

<span data-ttu-id="57e5f-130">IAuthenticatedEncryptor, 처럼 IAuthenticatedEncryptorDescriptor의 인스턴스는 하나의 특정 키 래핑 간주 됩니다.</span><span class="sxs-lookup"><span data-stu-id="57e5f-130">Like IAuthenticatedEncryptor, an instance of IAuthenticatedEncryptorDescriptor is assumed to wrap one specific key.</span></span> <span data-ttu-id="57e5f-131">즉, 모든 지정된 IAuthenticatedEncryptorDescriptor 인스턴스에 대 한 해당 CreateEncryptorInstance 메서드에 의해 만들어진 모든 인증 된 암호기 고려 않아야 함을에서 같이 해당는 아래 코드 샘플입니다.</span><span class="sxs-lookup"><span data-stu-id="57e5f-131">This means that for any given IAuthenticatedEncryptorDescriptor instance, any authenticated encryptors created by its CreateEncryptorInstance method should be considered equivalent, as in the below code sample.</span></span>

```csharp
// we have an IAuthenticatedEncryptorDescriptor instance
IAuthenticatedEncryptorDescriptor descriptor = ...;

// get an encryptor instance and perform an authenticated encryption operation
ArraySegment<byte> plaintext = new ArraySegment<byte>(Encoding.UTF8.GetBytes("plaintext"));
ArraySegment<byte> aad = new ArraySegment<byte>(Encoding.UTF8.GetBytes("AAD"));
var encryptor1 = descriptor.CreateEncryptorInstance();
byte[] ciphertext = encryptor1.Encrypt(plaintext, aad);

// get another encryptor instance and perform an authenticated decryption operation
var encryptor2 = descriptor.CreateEncryptorInstance();
byte[] roundTripped = encryptor2.Decrypt(new ArraySegment<byte>(ciphertext), aad);


// the 'roundTripped' and 'plaintext' buffers should be equivalent
```

---

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor"></a>

## <a name="iauthenticatedencryptordescriptor-aspnet-core-2x-only"></a><span data-ttu-id="57e5f-132">IAuthenticatedEncryptorDescriptor (ASP.NET 2.x만를 코어)</span><span class="sxs-lookup"><span data-stu-id="57e5f-132">IAuthenticatedEncryptorDescriptor (ASP.NET Core 2.x only)</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="57e5f-133">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="57e5f-133">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="57e5f-134">**IAuthenticatedEncryptorDescriptor** 인터페이스 자신을 XML로 내보낼 수 있는 형식을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="57e5f-134">The **IAuthenticatedEncryptorDescriptor** interface represents a type that knows how to export itself to XML.</span></span> <span data-ttu-id="57e5f-135">API는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="57e5f-135">Its API is as follows.</span></span>

* <span data-ttu-id="57e5f-136">ExportToXml(): XmlSerializedDescriptorInfo</span><span class="sxs-lookup"><span data-stu-id="57e5f-136">ExportToXml() : XmlSerializedDescriptorInfo</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="57e5f-137">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="57e5f-137">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

---

## <a name="xml-serialization"></a><span data-ttu-id="57e5f-138">XML Serialization</span><span class="sxs-lookup"><span data-stu-id="57e5f-138">XML Serialization</span></span>

<span data-ttu-id="57e5f-139">IAuthenticatedEncryptor 및 IAuthenticatedEncryptorDescriptor 간의 주요 차이점은 설명자 암호기를 만들고 유효한 인수를 제공 하는 방법을 알고 있는 합니다.</span><span class="sxs-lookup"><span data-stu-id="57e5f-139">The primary difference between IAuthenticatedEncryptor and IAuthenticatedEncryptorDescriptor is that the descriptor knows how to create the encryptor and supply it with valid arguments.</span></span> <span data-ttu-id="57e5f-140">IAuthenticatedEncryptor를 구현이 SymmetricAlgorithm 및 KeyedHashAlgorithm 의존 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="57e5f-140">Consider an IAuthenticatedEncryptor whose implementation relies on SymmetricAlgorithm and KeyedHashAlgorithm.</span></span> <span data-ttu-id="57e5f-141">암호기의 역할 이러한 형식을 사용 하는 하지만 반드시 있는지 알 수 없으므로, 이러한 형식은 있었던 응용 프로그램 다시 시작 될 경우 자신을 다시 만드는 방법에 대 한 적절 한 설명을 아웃 실제로 쓸 수는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="57e5f-141">The encryptor's job is to consume these types, but it doesn't necessarily know where these types came from, so it can't really write out a proper description of how to recreate itself if the application restarts.</span></span> <span data-ttu-id="57e5f-142">설명자가를 기반으로 더 높은 수준으로 동작합니다.</span><span class="sxs-lookup"><span data-stu-id="57e5f-142">The descriptor acts as a higher level on top of this.</span></span> <span data-ttu-id="57e5f-143">설명자 암호기 인스턴스를 만드는 방법을 알고 있으므로 (예: 필요한 알고리즘을 만드는 방법을 아는 것) 응용 프로그램 기본 설정 후 암호기 인스턴스 다시 만들 수 있도록 XML 형식의 정보를 serialize 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57e5f-143">Since the descriptor knows how to create the encryptor instance (e.g., it knows how to create the required algorithms), it can serialize that knowledge in XML form so that the encryptor instance can be recreated after an application reset.</span></span>

<a name="data-protection-extensibility-core-crypto-exporttoxml"></a>

<span data-ttu-id="57e5f-144">설명자의 ExportToXml 루틴을 통해 serialize 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57e5f-144">The descriptor can be serialized via its ExportToXml routine.</span></span> <span data-ttu-id="57e5f-145">이 루틴에는 두 개의 속성이 포함 된 XmlSerializedDescriptorInfo 반환: 설명자 및 형식을 나타내는 XElement 표현을 [IAuthenticatedEncryptorDescriptorDeserializer](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer) 수 있음 해당 XElement 제공이 설명자 부활 하는 데 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="57e5f-145">This routine returns an XmlSerializedDescriptorInfo which contains two properties: the XElement representation of the descriptor and the Type which represents an [IAuthenticatedEncryptorDescriptorDeserializer](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer) which can be used to resurrect this descriptor given the corresponding XElement.</span></span>

<span data-ttu-id="57e5f-146">Serialize 된 설명자는 암호화 키 자료 같은 중요 한 정보를 포함할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57e5f-146">The serialized descriptor may contain sensitive information such as cryptographic key material.</span></span> <span data-ttu-id="57e5f-147">데이터 보호 시스템에서 저장소에 유지가 전에 정보를 암호화 하기 위해 기본적으로 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="57e5f-147">The data protection system has built-in support for encrypting information before it's persisted to storage.</span></span> <span data-ttu-id="57e5f-148">를 이용 하려면이 설명자 (xmlns "http://schemas.asp.net/2015/03/dataProtection") 값 "true" 특성 이름 "requiresEncryption"를 사용 하 여 중요 한 정보를 포함 하는 요소를 표시 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="57e5f-148">To take advantage of this, the descriptor should mark the element which contains sensitive information with the attribute name "requiresEncryption" (xmlns "http://schemas.asp.net/2015/03/dataProtection"), value "true".</span></span>

>[!TIP]
> <span data-ttu-id="57e5f-149">이 특성을 설정 하기 위한 도우미 API 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57e5f-149">There's a helper API for setting this attribute.</span></span> <span data-ttu-id="57e5f-150">XElement.MarkAsRequiresEncryption() Microsoft.AspNetCore.DataProtection.AuthenticatedEncryption.ConfigurationModel 네임 스페이스에 있는 확장 메서드를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="57e5f-150">Call the extension method XElement.MarkAsRequiresEncryption() located in namespace Microsoft.AspNetCore.DataProtection.AuthenticatedEncryption.ConfigurationModel.</span></span>

<span data-ttu-id="57e5f-151">Serialize 된 설명자 중요 한 정보를 포함 하지 않는 경우 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57e5f-151">There can also be cases where the serialized descriptor doesn't contain sensitive information.</span></span> <span data-ttu-id="57e5f-152">HSM에 저장 된 암호화 키의 경우 다시 생각해 보세요.</span><span class="sxs-lookup"><span data-stu-id="57e5f-152">Consider again the case of a cryptographic key stored in an HSM.</span></span> <span data-ttu-id="57e5f-153">설명자는 HSM 자료 일반 텍스트 형식으로 노출 되지는 않습니다 이후 자체 직렬화 할 때 키 자료 쓸 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="57e5f-153">The descriptor cannot write out the key material when serializing itself since the HSM will not expose the material in plaintext form.</span></span> <span data-ttu-id="57e5f-154">대신, 설명자 (HSM이 방식으로 내보낼 수 있음) 하는 경우 키 또는 키에 대 한 HSM의 고유 식별자의 키 래핑된 버전 기록 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57e5f-154">Instead, the descriptor might write out the key-wrapped version of the key (if the HSM allows export in this fashion) or the HSM's own unique identifier for the key.</span></span>

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer"></a>

## <a name="iauthenticatedencryptordescriptordeserializer"></a><span data-ttu-id="57e5f-155">IAuthenticatedEncryptorDescriptorDeserializer</span><span class="sxs-lookup"><span data-stu-id="57e5f-155">IAuthenticatedEncryptorDescriptorDeserializer</span></span>

<span data-ttu-id="57e5f-156">**IAuthenticatedEncryptorDescriptorDeserializer** 인터페이스 XElement에서 IAuthenticatedEncryptorDescriptor 인스턴스를 deserialize 하는 방법을 알고 있는 형식을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="57e5f-156">The **IAuthenticatedEncryptorDescriptorDeserializer** interface represents a type that knows how to deserialize an IAuthenticatedEncryptorDescriptor instance from an XElement.</span></span> <span data-ttu-id="57e5f-157">단일 메서드를 노출합니다.</span><span class="sxs-lookup"><span data-stu-id="57e5f-157">It exposes a single method:</span></span>

* <span data-ttu-id="57e5f-158">(XElement 요소) ImportFromXml: IAuthenticatedEncryptorDescriptor</span><span class="sxs-lookup"><span data-stu-id="57e5f-158">ImportFromXml(XElement element) : IAuthenticatedEncryptorDescriptor</span></span>

<span data-ttu-id="57e5f-159">ImportFromXml 메서드가 사용 하 여 반환 된 XElement [IAuthenticatedEncryptorDescriptor.ExportToXml](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-exporttoxml) 원래 IAuthenticatedEncryptorDescriptor의 해당을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="57e5f-159">The ImportFromXml method takes the XElement that was returned by [IAuthenticatedEncryptorDescriptor.ExportToXml](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-exporttoxml) and creates an equivalent of the original IAuthenticatedEncryptorDescriptor.</span></span>

<span data-ttu-id="57e5f-160">IAuthenticatedEncryptorDescriptorDeserializer를 구현 하는 형식에는 다음 두 명의 공용 생성자 중 하나 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="57e5f-160">Types which implement IAuthenticatedEncryptorDescriptorDeserializer should have one of the following two public constructors:</span></span>

* <span data-ttu-id="57e5f-161">.ctor(IServiceProvider)</span><span class="sxs-lookup"><span data-stu-id="57e5f-161">.ctor(IServiceProvider)</span></span>

* <span data-ttu-id="57e5f-162">.ctor()</span><span class="sxs-lookup"><span data-stu-id="57e5f-162">.ctor()</span></span>

> [!NOTE]
> <span data-ttu-id="57e5f-163">생성자에 전달 IServiceProvider는 null 일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57e5f-163">The IServiceProvider passed to the constructor may be null.</span></span>

## <a name="the-top-level-factory"></a><span data-ttu-id="57e5f-164">최상위 팩터리</span><span class="sxs-lookup"><span data-stu-id="57e5f-164">The top-level factory</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="57e5f-165">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="57e5f-165">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="57e5f-166">**AlgorithmConfiguration** 클래스를 만드는 방법을 알고 있는 형식을 나타내는 [IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) 인스턴스.</span><span class="sxs-lookup"><span data-stu-id="57e5f-166">The **AlgorithmConfiguration** class represents a type which knows how to create [IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) instances.</span></span> <span data-ttu-id="57e5f-167">단일 API를 노출합니다.</span><span class="sxs-lookup"><span data-stu-id="57e5f-167">It exposes a single API.</span></span>

* <span data-ttu-id="57e5f-168">CreateNewDescriptor(): IAuthenticatedEncryptorDescriptor</span><span class="sxs-lookup"><span data-stu-id="57e5f-168">CreateNewDescriptor() : IAuthenticatedEncryptorDescriptor</span></span>

<span data-ttu-id="57e5f-169">AlgorithmConfiguration 최상위 팩터리 라고 생각 됩니다.</span><span class="sxs-lookup"><span data-stu-id="57e5f-169">Think of AlgorithmConfiguration as the top-level factory.</span></span> <span data-ttu-id="57e5f-170">구성을 템플릿으로 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="57e5f-170">The configuration serves as a template.</span></span> <span data-ttu-id="57e5f-171">래핑 알고리즘 정보 (예:이 구성을 생성 하는 AES-128-GCM 마스터 키로 설명자) 아직 특정 키와 연결 되지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="57e5f-171">It wraps algorithmic information (e.g., this configuration produces descriptors with an AES-128-GCM master key), but it is not yet associated with a specific key.</span></span>

<span data-ttu-id="57e5f-172">CreateNewDescriptor 라고, 새로운 키 자료가이 호출에만 만들어지고 새 IAuthenticatedEncryptorDescriptor 생성 되는 자료를 사용 하는 데 필요한 정보를 알고리즘 및이 키 자료 래핑합니다.</span><span class="sxs-lookup"><span data-stu-id="57e5f-172">When CreateNewDescriptor is called, fresh key material is created solely for this call, and a new IAuthenticatedEncryptorDescriptor is produced which wraps this key material and the algorithmic information required to consume the material.</span></span> <span data-ttu-id="57e5f-173">키 자료 될 수 있습니다 소프트웨어에서 만든 (고 메모리에 보관), 만들고가 HSM을 내 보유할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="57e5f-173">The key material could be created in software (and held in memory), it could be created and held within an HSM, and so on.</span></span> <span data-ttu-id="57e5f-174">중요 한 점은 CreateNewDescriptor 두 번 호출 된 해당 IAuthenticatedEncryptorDescriptor 인스턴스를 만들지 마십시오입니다.</span><span class="sxs-lookup"><span data-stu-id="57e5f-174">The crucial point is that any two calls to CreateNewDescriptor should never create equivalent IAuthenticatedEncryptorDescriptor instances.</span></span>

<span data-ttu-id="57e5f-175">와 같은 키를 만드는 루틴에 대 한 진입점으로 적합 AlgorithmConfiguration 유형은 [자동 키 롤링](../implementation/key-management.md#data-protection-implementation-key-management)합니다.</span><span class="sxs-lookup"><span data-stu-id="57e5f-175">The AlgorithmConfiguration type serves as the entry point for key creation routines such as [automatic key rolling](../implementation/key-management.md#data-protection-implementation-key-management).</span></span> <span data-ttu-id="57e5f-176">모든 향후 키에 대 한 구현을 변경 하려면 KeyManagementOptions AuthenticatedEncryptorConfiguration 속성을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="57e5f-176">To change the implementation for all future keys, set the AuthenticatedEncryptorConfiguration property in KeyManagementOptions.</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="57e5f-177">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="57e5f-177">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="57e5f-178">**IAuthenticatedEncryptorConfiguration** 인터페이스를 만드는 방법을 알고 있는 형식을 나타냅니다 [IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) 인스턴스.</span><span class="sxs-lookup"><span data-stu-id="57e5f-178">The **IAuthenticatedEncryptorConfiguration** interface represents a type which knows how to create [IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) instances.</span></span> <span data-ttu-id="57e5f-179">단일 API를 노출합니다.</span><span class="sxs-lookup"><span data-stu-id="57e5f-179">It exposes a single API.</span></span>

* <span data-ttu-id="57e5f-180">CreateNewDescriptor(): IAuthenticatedEncryptorDescriptor</span><span class="sxs-lookup"><span data-stu-id="57e5f-180">CreateNewDescriptor() : IAuthenticatedEncryptorDescriptor</span></span>

<span data-ttu-id="57e5f-181">IAuthenticatedEncryptorConfiguration 최상위 팩터리 라고 생각 됩니다.</span><span class="sxs-lookup"><span data-stu-id="57e5f-181">Think of IAuthenticatedEncryptorConfiguration as the top-level factory.</span></span> <span data-ttu-id="57e5f-182">구성을 템플릿으로 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="57e5f-182">The configuration serves as a template.</span></span> <span data-ttu-id="57e5f-183">래핑 알고리즘 정보 (예:이 구성을 생성 하는 AES-128-GCM 마스터 키로 설명자) 아직 특정 키와 연결 되지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="57e5f-183">It wraps algorithmic information (e.g., this configuration produces descriptors with an AES-128-GCM master key), but it is not yet associated with a specific key.</span></span>

<span data-ttu-id="57e5f-184">CreateNewDescriptor 라고, 새로운 키 자료가이 호출에만 만들어지고 새 IAuthenticatedEncryptorDescriptor 생성 되는 자료를 사용 하는 데 필요한 정보를 알고리즘 및이 키 자료 래핑합니다.</span><span class="sxs-lookup"><span data-stu-id="57e5f-184">When CreateNewDescriptor is called, fresh key material is created solely for this call, and a new IAuthenticatedEncryptorDescriptor is produced which wraps this key material and the algorithmic information required to consume the material.</span></span> <span data-ttu-id="57e5f-185">키 자료 될 수 있습니다 소프트웨어에서 만든 (고 메모리에 보관), 만들고가 HSM을 내 보유할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="57e5f-185">The key material could be created in software (and held in memory), it could be created and held within an HSM, and so on.</span></span> <span data-ttu-id="57e5f-186">중요 한 점은 CreateNewDescriptor 두 번 호출 된 해당 IAuthenticatedEncryptorDescriptor 인스턴스를 만들지 마십시오입니다.</span><span class="sxs-lookup"><span data-stu-id="57e5f-186">The crucial point is that any two calls to CreateNewDescriptor should never create equivalent IAuthenticatedEncryptorDescriptor instances.</span></span>

<span data-ttu-id="57e5f-187">와 같은 키를 만드는 루틴에 대 한 진입점으로 적합 IAuthenticatedEncryptorConfiguration 유형은 [자동 키 롤링](../implementation/key-management.md#data-protection-implementation-key-management)합니다.</span><span class="sxs-lookup"><span data-stu-id="57e5f-187">The IAuthenticatedEncryptorConfiguration type serves as the entry point for key creation routines such as [automatic key rolling](../implementation/key-management.md#data-protection-implementation-key-management).</span></span> <span data-ttu-id="57e5f-188">모든 향후 키에 대 한 구현을 변경 하려면 단일 IAuthenticatedEncryptorConfiguration 서비스 컨테이너에 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="57e5f-188">To change the implementation for all future keys, register a singleton IAuthenticatedEncryptorConfiguration in the service container.</span></span>

---
