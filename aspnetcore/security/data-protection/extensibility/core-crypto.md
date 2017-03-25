---
title: "핵심 암호화 확장성 | Microsoft 문서"
author: rick-anderson
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: edb4a776-f2ac-4502-8c26-0a66d3a3874c
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/extensibility/core-crypto
translationtype: Machine Translation
ms.sourcegitcommit: 010b730d2716f9f536fef889bc2f767afb648ef4
ms.openlocfilehash: 0d1b55d1e9fca7d472d6745f8b56ee307a851071
ms.lasthandoff: 03/23/2017

---
# <a name="core-cryptography-extensibility"></a>핵심 암호화 확장성

<a name=data-protection-extensibility-core-crypto></a>

>[!WARNING]
> 다음 인터페이스를 구현 하는 형식은 스레드로부터 안전 해야 합니다. 여러 호출자입니다.

<a name=data-protection-extensibility-core-crypto-iauthenticatedencryptor></a>

## <a name="iauthenticatedencryptor"></a>IAuthenticatedEncryptor

**IAuthenticatedEncryptor** 인터페이스는 암호화 하위 시스템의 기본 빌딩 블록입니다. 일반적으로 키 당 하나의 IAuthenticatedEncryptor 이며 IAuthenticatedEncryptor 인스턴스 모든 암호화 키 자료 및 암호화 작업을 수행 하는 데 필요한 알고리즘 정보를 래핑합니다.

이름에서 알 수 있듯이 형식은 인증 된 암호화 및 암호 해독 서비스 제공 해야 합니다. 다음 두 가지 Api를 노출합니다.

* 암호 해독 (ArraySegment<byte> ArraySegment 암호화 텍스트<byte> additionalAuthenticatedData): byte

* 암호화 (ArraySegment<byte> 일반 텍스트, ArraySegment<byte> additionalAuthenticatedData): byte

Encrypt 메서드는 서 암호화 일반 텍스트와 인증 태그를 포함 하는 blob를 반환 합니다. 인증 태그 자체는 AAD의 최종 페이로드에서 복구할 수 없게 필요 하지만 추가 인증 된 데이터 (AAD)를 포함 되어야 합니다. 암호 해독 메서드 인증 태그의 유효성을 검사 하 고 해독된 하는 페이로드를 반환 합니다. CryptographicException에 (ArgumentNullException 제외 하 고 및 이와 유사한)에 모든 오류를 homogenized 해야 합니다.

> [!NOTE]
> IAuthenticatedEncryptor 인스턴스 자체 키 자료를 포함 하도록 실제로 필요 하지 않습니다. 예를 들어, 모든 작업에 대 한 hsm 구현은 위임할 수 있습니다.

<a name=data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor></a>

## <a name="iauthenticatedencryptordescriptor"></a>IAuthenticatedEncryptorDescriptor

**IAuthenticatedEncryptorDescriptor** 인터페이스를 만드는 방법을 알고 있는 형식을 나타냅니다는 [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) 인스턴스. API는 다음과 같습니다.

* CreateEncryptorInstance(): IAuthenticatedEncryptor

* ExportToXml(): XmlSerializedDescriptorInfo

IAuthenticatedEncryptor, 처럼 IAuthenticatedEncryptorDescriptor의 인스턴스는 하나의 특정 키 래핑로 간주 됩니다. 즉,는 지정된 된 IAuthenticatedEncryptorDescriptor 인스턴스에서 대 한 해당 CreateEncryptorInstance 메서드에 의해 만들어진 모든 인증 된 암호기 고려해 야에서 같이 해당 하는 아래 코드 샘플입니다.

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

## <a name="xml-serialization"></a>XML Serialization

IAuthenticatedEncryptor IAuthenticatedEncryptorDescriptor 사이의 주요 차이점은 설명자 암호기를 만들고 올바른 인수를 제공 하는 방법을 알고 있어야 한다는 합니다. IAuthenticatedEncryptor를 구현이 SymmetricAlgorithm 및 KeyedHashAlgorithm 의존 하는 것이 좋습니다. 암호기의 작업은 이러한 형식을 사용 하는 하지만 그 반드시 있는지 알 수 없으므로, 이러한 종류의 출처는 응용 프로그램이 다시 시작 하는 경우 자체를 다시 만드는 방법에 대 한 적절 한 설명을 아웃 실제로 쓸 수 없습니다. 설명자는이 기반으로 더 높은 수준으로 작동합니다. 설명자 암호기 인스턴스를 만드는 방법을 알고 있으므로 (예: 필요한 알고리즘을 만드는 방법을 아는 것) 암호기 인스턴스 응용 프로그램을 다시 설정한 후 다시 만들 수 있도록 XML 형식에서 정보를 serialize 할 수 있습니다.

<a name=data-protection-extensibility-core-crypto-exporttoxml></a>

설명자의 ExportToXml 루틴을 통해 serialize 할 수 있습니다. 이 루틴에는 두 개의 속성을 포함 하는 XmlSerializedDescriptorInfo 반환: 설명자를 나타내는 형식을 XElement 표현을 [IAuthenticatedEncryptorDescriptorDeserializer](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer) 부활 해당 XElement 지정이 설명자에 사용할 수 있는 합니다.

Serialize 된 설명자는 암호화 키 자료와 같은 중요 한 정보를 포함할 수 있습니다. 데이터 보호 시스템에 기본적으로 지원 저장소에 유지 된 전에 정보를 암호화 합니다. 이 활용 하려면 설명자 (xmlns "http://schemas.asp.net/2015/03/dataProtection") 값 "true" 특성 이름 "requiresEncryption"를 사용 하 여 중요 한 정보를 포함 하는 요소를 표시 해야 합니다.

>[!TIP]
> 이 특성을 설정 하기 위한 도우미 API 있습니다. XElement.MarkAsRequiresEncryption() Microsoft.AspNetCore.DataProtection.AuthenticatedEncryption.ConfigurationModel 네임 스페이스에 있는 확장 메서드를 호출 합니다.

Serialize 된 설명자 중요 한 정보가 포함 되어 있지 않습니다 사례 수도 있습니다. 다시 HSM에 저장 된 암호화 키의 경우를 생각해 보십시오. 설명자는 HSM 자료 일반 텍스트 형식으로 노출 되지는 않습니다 이후 자체를 직렬화 할 때 키 자료를 쓸 수 없습니다. 대신, 설명자 (HSM이 방식으로 내보낼 수 있음) 하는 경우 키 또는 키에 대 한 HSM의 고유 식별자의 키 래핑된 버전을 기록 될 수 있습니다.

<a name=data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer></a>

## <a name="iauthenticatedencryptordescriptordeserializer"></a>IAuthenticatedEncryptorDescriptorDeserializer

**IAuthenticatedEncryptorDescriptorDeserializer** 인터페이스 XElement에서 IAuthenticatedEncryptorDescriptor 인스턴스를 deserialize 하는 방법을 알고 있는 형식을 나타냅니다. 단일 메서드를 노출 합니다.

* ImportFromXml (XElement 요소): IAuthenticatedEncryptorDescriptor

ImportFromXml 메서드에서 사용 하 여 반환 된 XElement [IAuthenticatedEncryptorDescriptor.ExportToXml](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-exporttoxml) 원래 IAuthenticatedEncryptorDescriptor 개를 만듭니다.

IAuthenticatedEncryptorDescriptorDeserializer를 구현 하는 형식에는 다음 두 public 생성자 중 하나 있어야 합니다.

* .ctor(IServiceProvider)

* .ctor()

> [!NOTE]
> 생성자에 전달 되는 IServiceProvider null 일 수 있습니다.

## <a name="iauthenticatedencryptorconfiguration"></a>IAuthenticatedEncryptorConfiguration

**IAuthenticatedEncryptorConfiguration** 인터페이스를 만드는 방법을 알고 있는 형식을 나타냅니다 [IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) 인스턴스. 단일 API를 노출합니다.

* CreateNewDescriptor(): IAuthenticatedEncryptorDescriptor

최상위 팩터리로 IAuthenticatedEncryptorConfiguration 생각 합니다. 템플릿과 같은 역할을 구성 합니다. 래핑 알고리즘 정보 (예:이 구성에서 AES-128-GCM 마스터 키가 있는 설명자)을 아직 특정 키와 연결 되지 않았습니다.

CreateNewDescriptor 라고, 새로운 키 자료가이 호출에만 만들어지고 새 IAuthenticatedEncryptorDescriptor 생성 되는이 키 자료 및 자료를 사용 하는 데 필요한 정보를 알고리즘 래핑합니다. 키 자료 수 수를 만들고 소프트웨어에서 (메모리에 보관)를 만들고가 HSM 내 보유 될 수 있습니다. 중요 한 점은 CreateNewDescriptor 두 번 호출 된 해당 IAuthenticatedEncryptorDescriptor 인스턴스를 만들지 마십시오입니다.

IAuthenticatedEncryptorConfiguration 형식 등의 역할 키 생성 루틴에 대 한 진입점으로 [자동 키 롤링](../implementation/key-management.md#data-protection-implementation-key-management)합니다. 이후 모든 키에 대 한 구현을 변경 하려면 단일 IAuthenticatedEncryptorConfiguration 서비스 컨테이너에 등록 합니다.

