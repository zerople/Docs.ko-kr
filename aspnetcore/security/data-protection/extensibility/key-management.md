---
title: "키 관리 확장성 | Microsoft 문서"
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
translationtype: Machine Translation
ms.sourcegitcommit: 010b730d2716f9f536fef889bc2f767afb648ef4
ms.openlocfilehash: c9530be6b9ff63a4f435fd98f6ef5b0ac0f50a59
ms.lasthandoff: 03/23/2017

---
# <a name="key-management-extensibility"></a>키 관리 확장성

<a name=data-protection-extensibility-key-management></a>

>[!TIP]
> 읽기는 [키 관리](../implementation/key-management.md#data-protection-implementation-key-management) 이러한 Api의 기본적인 개념 중 일부를 설명 하는 대로이 섹션을 읽기 전에 섹션입니다.

>[!WARNING]
> 다음 인터페이스를 구현 하는 형식은 스레드로부터 안전 해야 합니다. 여러 호출자입니다.

## <a name="key"></a>키

IKey 인터페이스에는 암호화 시스템에 있는 키의 기본 표현입니다. 용어 키의 "암호화 키 자료" 리터럴 의미에 없는 추상 의미에서 사용 됩니다. 키에는 다음과 같은 속성이 있습니다.

* 활성화, 생성 및 만료 날짜

* 해지 상태

* 키 식별자 (GUID)

IKey 만드는 데 사용할 수 있는 CreateEncryptorInstance 메서드를 노출 하는 또한는 [IAuthenticatedEncryptor](core-crypto.md#data-protection-extensibility-core-crypto-iauthenticatedencryptor) 인스턴스는이 키에 연결 합니다.

> [!NOTE]
> API IKey 인스턴스로부터 원시 암호화 된 자료를 검색 하는 없습니다.

## <a name="ikeymanager"></a>IKeyManager

IKeyManager 인터페이스는 일반 키 저장, 검색 및 조작 하는 일을 담당 하는 개체를 나타냅니다. 세 가지 개략적인 작업을 노출 합니다.

* 새 키를 만들고 저장소를 유지 합니다.

* 저장소에서 모든 키를 가져옵니다.

* 하나 이상의 키를 해지 하 고 저장소에 해지 정보를 유지 합니다.

>[!WARNING]
> IKeyManager는 고급 작업 작성과 대부분 개발자의 시도 하지 않아야 합니다. 대신 대부분의 개발자가 제공 하는 기능을 활용 취해야는 [XmlKeyManager](xref:security/data-protection/extensibility/key-management#data-protection-extensibility-key-management-xmlkeymanager) 클래스입니다.

<a name=data-protection-extensibility-key-management-xmlkeymanager></a>

## <a name="xmlkeymanager"></a>XmlKeyManager

XmlKeyManager 형식은 IKeyManager의 상자에서 구체적인 구현입니다. 키 위탁 및 휴지 키의 암호화를 포함 하 여 몇 가지 유용한 기능을 제공 합니다. 이 시스템의 키 XML 요소로 표현 됩니다 (특히, [XElement](https://msdn.microsoft.com/en-us/library/system.xml.linq.xelement(v=vs.110).aspx)).

XmlKeyManager 해당 작업을 수행 하는 과정에서 다른 구성 요소에 따라 달라 집니다.

* IAuthenticatedEncryptorConfiguration 새 키로 사용 되는 알고리즘을 지정 합니다.

* IXmlRepository, 키 저장소에 유지 되는 위치는 컨트롤입니다.

* IXmlEncryptor [옵션], 암호화 키 휴지 수 있도록 해줍니다.

* IKeyEscrowSink [옵션] 키 위탁 서비스를 제공 합니다.

다음은 이러한 구성 요소는 연결 되는 방식을 함께 XmlKeyManager 내에서 표시 하는 높은 수준의 다이어그램입니다.

   ![키 만들기](key-management/_static/keycreation.png)

   *키 생성 / CreateNewKey*

CreateNewKey 구현에서 IAuthenticatedEncryptorConfiguration 구성 요소는 다음 XML로 serialize 되는 고유한 IAuthenticatedEncryptorDescriptor를 만드는 데 사용 됩니다. 키 위탁 싱크가 있는 경우 원시 (암호화 되지 않음된) XML 장기 저장에 대 한 싱크에 제공 됩니다. 암호화 되지 않은 XML 그런 다음 실행 됩니다는 IXmlEncryptor를 통해 (필요한 경우)를 암호화 된 XML 문서를 생성 합니다. 이 암호화 된 문서는 IXmlRepository 통해 장기 저장소에 유지 됩니다. (없음 IXmlEncryptor 구성 된 경우 암호화 되지 않은 문서에에서 보관 됩니다는 IXmlRepository.)

   ![키 검색](key-management/_static/keyretrieval.png)

   *키 검색 / GetAllKeys*

GetAllKeys 구현에서 기본 IXmlRepository에서 키 및 해지를 나타내는 XML 문서를 읽습니다. 이러한 문서를 암호화 하는 경우 시스템은 자동으로 암호를 해독 하 합니다. XmlKeyManager는 IAuthenticatedEncryptorDescriptor 인스턴스는 개별 IKey 인스턴스에 래핑됩니다. 그런 다음에 다시 문서를 deserialize 하는 데 적절 한 IAuthenticatedEncryptorDescriptorDeserializer 인스턴스를 만듭니다. IKey 인스턴스의이 컬렉션은 호출자에 게 반환 됩니다.

특정 XML 요소에 대 한 자세한 정보를 찾을 수는 [키 저장소 형식 문서](../implementation/key-storage-format.md#data-protection-implementation-key-storage-format)합니다.

## <a name="ixmlrepository"></a>IXmlRepository

IXmlRepository XML을 유지 하 고 백업 저장소에서 XML을 검색할 수 있는 형식을 나타냅니다. 두 가지 Api를 노출 합니다.

* GetAllElements(): IReadOnlyCollection<XElement>

* StoreElement (XElement 요소, 문자열 friendlyName)

IXmlRepository의 구현을 통해 전달 하는 XML을 구문 분석할 필요가 없습니다. 불투명으로 XML 문서를 처리 하 고 보다 높은 계층을 생성 하 고 문서를 구문 분석 하는 방법에 대 한 걱정을 사용 해야 합니다.

IXmlRepository를 구현 하는 두 개의 기본 제공 구체적인 형식을 가지: FileSystemXmlRepository 및 RegistryXmlRepository 합니다. 참조는 [키 저장소 공급자 문서](../implementation/key-storage-providers.md#data-protection-implementation-key-storage-providers) 에 대 한 자세한 내용은 합니다. 사용자 지정 IXmlRepository 등록 것 될 다른 사용 하 여 적절 한 방식으로 백업 저장소, 예: Azure Blob 저장소입니다. 기본 저장소 응용 프로그램 전체를 변경 하려면 사용자 지정 singleton IXmlRepository 서비스 공급자에 등록 합니다.

<a name=data-protection-extensibility-key-management-ixmlencryptor></a>

## <a name="ixmlencryptor"></a>IXmlEncryptor

IXmlEncryptor 인터페이스에는 일반 텍스트 XML 요소를 암호화할 수 있는 형식을 나타냅니다. 단일 API를 노출 합니다.

* 암호화 (XElement plaintextElement): EncryptedXmlInfo

Serialize 된 IAuthenticatedEncryptorDescriptor "암호화 필요"로 표시 된 요소가 들어 있으면 다음 XmlKeyManager 실행 해당 요소는 구성 된 IXmlEncryptor Encrypt 메서드를 통해 되 고는 IXmlRepository 일반 텍스트 요소가 아니라 서 암호화 요소 유지 됩니다. Encrypt 메서드의 출력은 EncryptedXmlInfo 개체입니다. 이 개체는 결과 서 암호화 XElement와의 해당 요소를 해독을 사용할 수 있는 한 IXmlDecryptor를 나타내는 형식을 포함 하는 래퍼입니다.

네 가지 기본 제공 구체적인 형식이 IXmlEncryptor를 구현 하는: CertificateXmlEncryptor, DpapiNGXmlEncryptor, DpapiXmlEncryptor, 및 NullXmlEncryptor 합니다. 참조는 [rest 문서에서 키 암호화](../implementation/key-encryption-at-rest.md#data-protection-implementation-key-encryption-at-rest) 에 대 한 자세한 내용은 합니다. 전체 응용 프로그램의 기본 rest에서 키 암호화 메커니즘을 변경 하려면 사용자 지정 singleton IXmlEncryptor 서비스 공급자에 등록 합니다.

## <a name="ixmldecryptor"></a>IXmlDecryptor

IXmlDecryptor XElement 서 된 암호화는 IXmlEncryptor 통해를 해독 하는 방법을 알고 있는 형식을 나타냅니다. 단일 API를 노출 합니다.

* (XElement encryptedElement) 암호를 해독: XElement

암호 해독 메서드 IXmlEncryptor.Encrypt에서 수행 된 암호화를 실행 취소 합니다. 일반적으로 각 구체적인 IXmlEncryptor 구현에는 해당 구체적인 IXmlDecryptor 구현을 해야 합니다.

IXmlDecryptor를 구현 하는 형식에는 다음 두 public 생성자 중 하나 있어야 합니다.

* .ctor(IServiceProvider)

* .ctor()

> [!NOTE]
> 생성자에 전달 되는 IServiceProvider null 일 수 있습니다.

## <a name="ikeyescrowsink"></a>IKeyEscrowSink

IKeyEscrowSink 인터페이스에는 중요 한 정보를 위탁을 수행할 수 있는 형식을 나타냅니다. Serialize 된 설명자는 중요 한 정보 (예: 암호화 자료)가 포함 될 수 있습니다이 되어 소개 전이성은 [IXmlEncryptor](xref:security/data-protection/extensibility/key-management#data-protection-extensibility-key-management-ixmlencryptor) 애초에 입력 합니다. 그러나 그리고 사고가 발생할 때 keyrings 삭제할 수 또는 손상 되었습니다.

위탁 인터페이스를 사용 하는 구성으로 전송 하기 전에 원시 serialize 된 XML에 대 한 액세스를 허용 하는 긴급 이스케이프 해치 제공 [IXmlEncryptor](xref:security/data-protection/extensibility/key-management#data-protection-extensibility-key-management-ixmlencryptor)합니다. 인터페이스는 단일 API를 노출합니다.

* 저장소 (Guid keyId, XElement 요소)

비즈니스 정책을 사용 하 여 일치 하는 안전한 방식으로 제공된 된 요소를 처리 하도록 IKeyEscrowSink 구현 됩니다. 여기서는 인증서의 개인 키가 에스크로 되었습니다; 알려진된 회사 X.509 인증서를 사용 하 여 XML 요소를 암호화 하는 위탁 싱크에 대 한 한 가지 구현 될 수 있습니다. 이 CertificateXmlEncryptor 유형을 지원할 수 있습니다. 또한 IKeyEscrowSink 구현은 제공된 된 요소를 적절 하 게 유지 하기 위한 합니다.

기본적으로 없음 위탁 메커니즘을 사용 하지만 서버 관리자 수 [이 전역적으로 구성](../configuration/machine-wide-policy.md#data-protection-configuration-machinewidepolicy)합니다. 또한 구성할 수 있습니다 프로그래밍 방식으로 통해는 *IDataProtectionBuilder.AddKeyEscrowSink* 아래 샘플에 나와 있는 것 처럼 메서드. *AddKeyEscrowSink* 메서드 오버 로드 미러는 *IServiceCollection.AddSingleton* 및 *IServiceCollection.AddInstance* IKeyEscrowSink 인스턴스는 단일 항목 수를 의도 한 대로 오버 로드 합니다. 여러 IKeyEscrowSink 인스턴스가 등록 된 경우 각 키 여러 메커니즘을 동시에 위탁 할 수 있으므로 키 생성 하는 동안 호출 됩니다.

API IKeyEscrowSink 인스턴스로부터 자료를 읽을 수는 없습니다. 이 위탁 메커니즘의 디자인 이론 일치: 키 자료에는 신뢰할 수 있는 인증 기관에 액세스할 수 있도록 돕기 위한 것이 고 아니므로 응용 프로그램 자체를 신뢰할 수 있는 기관, 자체 escrowed 자료에 액세스할 수 없어야 합니다.

다음 샘플 코드를 만들고 "CONTOSODomain Admins"의 구성원만 복구할 수 있도록 키 위탁 하는 위치는 IKeyEscrowSink 등록 하는 방법을 보여 줍니다.

> [!NOTE]
> 이 샘플을 실행 하려면 사용 해야 도메인에 가입 된 Windows 8 / Windows Server 2012 컴퓨터와 도메인 컨트롤러에는 Windows Server 2012 이상 이어야 합니다.

[!code-none[주](key-management/samples/key-management-extensibility.cs)]

