---
title: "키 관리 확장성"
author: rick-anderson
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 3606b251-8324-4485-8d52-582a2cd5cffb
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/extensibility/key-management
ms.openlocfilehash: ed84b6dc257d5fd9e4c1cf6106df3c8bd6e14f64
ms.sourcegitcommit: 9cdbfd0d670d70b9c354216aabee260c52dad5ee
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/12/2017
---
# <a name="key-management-extensibility"></a>키 관리 확장성

<a name=data-protection-extensibility-key-management></a>

>[!TIP]
> 읽기는 [키 관리](../implementation/key-management.md#data-protection-implementation-key-management) 이러한 Api는 기본적인 개념 중 일부에 대해 설명 하는 대로이 섹션을 읽기 전에 섹션.

>[!WARNING]
> 다음 인터페이스 중 하나를 구현 하는 형식은 스레드로부터 안전 해야 합니다. 여러 호출자에 대 한 합니다.

## <a name="key"></a>Key

IKey 인터페이스에는 암호화 시스템에 있는 키의 기본 표현입니다. 용어 키의 "암호화 키 자료" 리터럴 관점에 없는 추상적인 의미에서 사용 됩니다. 키에 다음 속성이 있습니다.

* 활성화, 생성 및 만료 날짜

* 해지 상태

* 키 식별자 (GUID)

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

IKey 만드는 데 사용할 수 있는 CreateEncryptor 메서드를 노출 하는 또한는 [IAuthenticatedEncryptor](core-crypto.md#data-protection-extensibility-core-crypto-iauthenticatedencryptor) 인스턴스가이 키에 연결 합니다.

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

IKey 만드는 데 사용할 수 있는 CreateEncryptorInstance 메서드를 노출 하는 또한는 [IAuthenticatedEncryptor](core-crypto.md#data-protection-extensibility-core-crypto-iauthenticatedencryptor) 인스턴스가이 키에 연결 합니다.

---

> [!NOTE]
> API IKey 인스턴스로부터 원시 암호화 된 자료는 검색할 수는 없습니다.

## <a name="ikeymanager"></a>IKeyManager

IKeyManager 인터페이스 일반 키 저장소, 검색 및 조작 하는 일을 담당 하는 개체를 나타냅니다. 세 가지 상위 수준 작업을 노출 합니다.

* 새 키를 만들고 저장소를 유지 합니다.

* 저장소에서 모든 키를 가져옵니다.

* 하나 이상의 키를 해지 하 고 저장소는 해지 정보를 저장 합니다.

>[!WARNING]
> IKeyManager를 작성 하는 고급 작업 및 대부분의 개발자는 시도 하지 않아야 합니다. 대신 대부분의 개발자가 제공 하는 기능 사용 해야는 [XmlKeyManager](xref:security/data-protection/extensibility/key-management#data-protection-extensibility-key-management-xmlkeymanager) 클래스입니다.

<a name=data-protection-extensibility-key-management-xmlkeymanager></a>

## <a name="xmlkeymanager"></a>XmlKeyManager

XmlKeyManager 형식은 IKeyManager의 기본 구체적인 구현입니다. 키 위탁 및 대기 키의 암호화를 포함 하 여 몇 가지 유용한 기능을 제공 합니다. 이 시스템에 있는 키 XML 요소로 표시 됩니다 (특히 [XElement](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/linq/xelement-class-overview)합니다.

XmlKeyManager 해당 작업을 수행 하는 과정에서 다른 여러 가지 구성 요소에 따라 달라 집니다.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

* AlgorithmConfiguration 새 키로 사용 되는 알고리즘을 지정 합니다.

* IXmlRepository, 키 저장소에 유지 되는 위치를 제어 합니다.

* IXmlEncryptor [선택 사항]를 통해 저장 된 상태의 키를 암호화할 수 있습니다.

* IKeyEscrowSink [선택 사항] 키 에스크로 서비스를 제공 합니다.

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

* IXmlRepository, 키 저장소에 유지 되는 위치를 제어 합니다.

* IXmlEncryptor [선택 사항]를 통해 저장 된 상태의 키를 암호화할 수 있습니다.

* IKeyEscrowSink [선택 사항] 키 에스크로 서비스를 제공 합니다.

---

다음은 이러한 구성 요소는 연결 되는 방식을 함께 XmlKeyManager 내 나타내는 개략적인 다이어그램입니다.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

   ![키 만들기](key-management/_static/keycreation2.png)

   *키 생성 / CreateNewKey*

CreateNewKey 구현의 AlgorithmConfiguration 구성 요소는 다음 XML로 serialize 하는 고유한 IAuthenticatedEncryptorDescriptor 만들 하는 데 사용 됩니다. 키 에스크로 싱크가 있는 경우 원시 (암호화 되지 않은) XML 장기 저장에 대 한 싱크에 제공 됩니다. 암호화 되지 않은 XML 그런 다음 실행 됩니다는 IXmlEncryptor 통해 (필요한 경우)를 암호화 된 XML 문서를 생성 합니다. 암호화 된이 문서는 IXmlRepository 통해 장기 저장소에 유지 됩니다. (없음 IXmlEncryptor 구성 된 경우 암호화 되지 않은 문서에서에서 유지 되는 IXmlRepository.)

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

   ![키 만들기](key-management/_static/keycreation1.png)

   *키 생성 / CreateNewKey*

CreateNewKey 구현의 IAuthenticatedEncryptorConfiguration 구성 요소는 다음 XML로 serialize 하는 고유한 IAuthenticatedEncryptorDescriptor 만들 하는 데 사용 됩니다. 키 에스크로 싱크가 있는 경우 원시 (암호화 되지 않은) XML 장기 저장에 대 한 싱크에 제공 됩니다. 암호화 되지 않은 XML 그런 다음 실행 됩니다는 IXmlEncryptor 통해 (필요한 경우)를 암호화 된 XML 문서를 생성 합니다. 암호화 된이 문서는 IXmlRepository 통해 장기 저장소에 유지 됩니다. (없음 IXmlEncryptor 구성 된 경우 암호화 되지 않은 문서에서에서 유지 되는 IXmlRepository.)

---

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

   ![키 검색](key-management/_static/keyretrieval2.png)
   
# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

   ![키 검색](key-management/_static/keyretrieval1.png)

---

   *검색 키 / GetAllKeys*

GetAllKeys 구현에서 기본 IXmlRepository에서 키 및 해지를 나타내는 XML 문서를 읽습니다. 이러한 문서를 암호화 하는 경우 시스템은 자동으로 암호 해독할 합니다. XmlKeyManager 다시 인스턴스로 IAuthenticatedEncryptorDescriptor, 그런 다음 개별 IKey 인스턴스에 래핑되는 문서를 deserialize 하는 데 적절 한 IAuthenticatedEncryptorDescriptorDeserializer 인스턴스를 만듭니다. 이 컬렉션의 IKey 인스턴스 호출자에 게 반환 됩니다.

특정 XML 요소에 대 한 자세한 정보를 찾을 수는 [키 저장소 형식 문서](../implementation/key-storage-format.md#data-protection-implementation-key-storage-format)합니다.

## <a name="ixmlrepository"></a>IXmlRepository

IXmlRepository 인터페이스에는 XML을 유지 하 고 백업 저장소에서 XML을 검색할 수 있는 형식을 나타냅니다. 두 개의 Api를 노출합니다.

* GetAllElements(): IReadOnlyCollection<XElement>

* StoreElement (XElement 요소, 문자열 friendlyName)

IXmlRepository의 구현을 통해 전달 되는 XML을 구문 분석할 필요가 없습니다. 불투명으로 XML 문서 처리를 생성 하 고 문서를 구문 분석 하는 방법에 대 한 걱정 더 높은 계층을 사용 합니다.

기본 제공 구체적인 순위가 IXmlRepository를 구현 하는: FileSystemXmlRepository 및 RegistryXmlRepository 합니다. 참조는 [키 저장소 공급자 문서](../implementation/key-storage-providers.md#data-protection-implementation-key-storage-providers) 자세한 정보에 대 한 합니다. 사용자 지정 IXmlRepository 등록 것 될 다른 사용 하려면 적절 한 방식으로 백업 저장소, 예: Azure Blob 저장소입니다. 기본 저장소 응용 프로그램 전체를 변경 하려면 사용자 지정 singleton IXmlRepository 서비스 공급자에 등록 합니다.

<a name=data-protection-extensibility-key-management-ixmlencryptor></a>

## <a name="ixmlencryptor"></a>IXmlEncryptor

IXmlEncryptor 인터페이스에는 일반 텍스트 XML 요소를 암호화할 수 있는 형식을 나타냅니다. 단일 API를 노출합니다.

* (XElement plaintextElement) 암호화: EncryptedXmlInfo

Serialize 된 IAuthenticatedEncryptorDescriptor에 "암호화 필요"로 표시 된 요소가 들어 있으면 그런 다음 XmlKeyManager는 해당 요소는 구성 된 IXmlEncryptor 암호화 메서드를 통해를 실행 서 암호화 요소 대신 계속 유지 됩니다. 보다는 IXmlRepository 일반 텍스트 요소입니다. Encrypt 메서드의 출력은 EncryptedXmlInfo 개체입니다. 이 개체는 모두 결과 서 암호화 XElement와의 해당 요소를 해독 하는 데 사용할 수 있는 IXmlDecryptor를 나타내는 형식을 포함 하는 래퍼입니다.

네 가지 기본 제공 구체적인 유형 IXmlEncryptor를 구현 하는: CertificateXmlEncryptor, DpapiNGXmlEncryptor, DpapiXmlEncryptor, 및 NullXmlEncryptor 합니다. 참조는 [rest 문서에서 키 암호화](../implementation/key-encryption-at-rest.md#data-protection-implementation-key-encryption-at-rest) 자세한 정보에 대 한 합니다. 전체 응용 프로그램의 기본 휴지 키 암호화 메커니즘을 변경 하려면 사용자 지정 singleton IXmlEncryptor 서비스 공급자에 등록 합니다.

## <a name="ixmldecryptor"></a>IXmlDecryptor

IXmlDecryptor XElement 서 된 암호화는 IXmlEncryptor 통해를 해독 하는 방법을 알고 있는 형식을 나타냅니다. 단일 API를 노출합니다.

* (XElement encryptedElement) 암호 해독: XElement

암호 해독 메서드 IXmlEncryptor.Encrypt에서 수행 된 암호화 실행 취소 합니다. 일반적으로 각 구체적인 IXmlEncryptor 구현은 해당 구체적인 IXmlDecryptor 구현을 제공 합니다.

IXmlDecryptor를 구현 하는 형식에는 다음 두 명의 공용 생성자 중 하나 있어야 합니다.

* .ctor(IServiceProvider)

* .ctor()

> [!NOTE]
> 생성자에 전달 IServiceProvider는 null 일 수 있습니다.

## <a name="ikeyescrowsink"></a>IKeyEscrowSink

IKeyEscrowSink 에스크로 중요 한 정보를 수행할 수 있는 형식을 나타냅니다. Serialize 된 설명자 (예: 암호화 자료)가 중요 한 정보가 포함 될 수 있습니다 및 도입이 이것이 회수는 [IXmlEncryptor](xref:security/data-protection/extensibility/key-management#data-protection-extensibility-key-management-ixmlencryptor) 처음에 입력 합니다. 그러나 실수로 인해가 오류가 발생할 및 keyrings 삭제할 수 있습니다 또는 손상 되었습니다.

에스크로 인터페이스에 구성 된 모든에서 변환 되기 전에 원시 serialize 된 XML에 대 한 액세스를 허용 하는 응급 이스케이프 해치 제공 [IXmlEncryptor](xref:security/data-protection/extensibility/key-management#data-protection-extensibility-key-management-ixmlencryptor)합니다. 인터페이스는 단일 API를 노출합니다.

* 저장소 (Guid keyId, XElement 요소)

안전한 비즈니스 정책을 사용 하 여 일관 된 방식으로 제공된 된 요소를 처리 하는 데 IKeyEscrowSink 구현 됩니다. 여기서 인증서의 개인 키가 에스크로 되었습니다; 알려진된 회사 X.509 인증서를 사용 하 여 XML 요소를 암호화 하려면 에스크로 싱크에 대 한 한 가지 구현 될 수 있습니다. 이 CertificateXmlEncryptor 유형을 지원할 수 있습니다. 또한 IKeyEscrowSink 구현은 제공된 된 요소를 적절 하 게 유지 합니다.

기본적으로 에스크로 메커니즘이 없으므로 활성화, 서버 관리자 수 있지만 [전체적으로이 옵션을 구성](../configuration/machine-wide-policy.md#data-protection-configuration-machinewidepolicy)합니다. 도 구성할 수 있습니다 프로그래밍 방식으로 통해는 *IDataProtectionBuilder.AddKeyEscrowSink* 아래 예제와 같이 메서드. *AddKeyEscrowSink* 메서드 오버 로드 미러는 *IServiceCollection.AddSingleton* 및 *IServiceCollection.AddInstance* IKeyEscrowSink와 오버 로드 인스턴스는 단일 항목으로 사용 됩니다. 여러 IKeyEscrowSink 인스턴스가 등록 되어 있으면 여러 메커니즘을 동시에 키를 위탁 수 있으므로 각 키 생성 시 호출 됩니다.

API 자료 IKeyEscrowSink 인스턴스에서 읽을 수는 없습니다. 이 에스크로 메커니즘의 디자인 이론 일치: 키 자료를 신뢰할 수 있는 기관에 액세스할 수 있도록 하기 위한 것이 하며 응용 프로그램은 자체 아니므로 신뢰할 수 있는 기관, 자체 escrowed 자료에 대 한 액세스가 하지 않아야 합니다.

다음 샘플 코드 만들고 "CONTOSODomain Admins"의 구성원만 복구할 수는 키는 위탁 되는 위치는 IKeyEscrowSink 등록 하는 방법을 보여 줍니다.

> [!NOTE]
> 이 샘플을 실행 하려면 여야 도메인에 가입 된 Windows 8에서 Windows Server 2012 컴퓨터와 도메인 컨트롤러에는 Windows Server 2012 이상 이어야 합니다.

[!code-none[Main](key-management/samples/key-management-extensibility.cs)]
