---
title: "데이터 보호를 구성 합니다. | Microsoft 문서"
author: rick-anderson
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 0e4881a3-a94d-4e35-9c1c-f025d65dcff0
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/configuration/overview
translationtype: Machine Translation
ms.sourcegitcommit: 010b730d2716f9f536fef889bc2f767afb648ef4
ms.openlocfilehash: 0783110e036291a8b06716d89903347e7b41efce
ms.lasthandoff: 03/23/2017

---
# <a name="configuring-data-protection"></a>데이터 보호를 구성합니다.

<a name=data-protection-configuring></a>

데이터 보호 시스템 초기화 될 때 일부 적용 [기본 설정](default-settings.md#data-protection-default-settings) 운영 환경을 기반으로 합니다. 이러한 설정은 일반적으로 단일 컴퓨터에서 실행 중인 응용 프로그램에 적합 합니다. 개발자는 이러한 변경 해야 할 수는 있는 경우도 있습니다 (아마도 이거나 준수 이유 때문 여러 컴퓨터 간에 분산 되는 응용 프로그램 때문에), 이러한 시나리오에 대 한 데이터 보호 시스템 풍부한 구성 API를 제공 합니다.

<a name=data-protection-configuration-callback></a>

확장 메서드는 IDataProtectionBuilder 반환 AddDataProtection 없는 함께 연결 하는 옵션을 다양 한 데이터 보호를 구성 하는 확장 메서드를 노출 하는 그 자체가 합니다. 예를 들어 % LOCALAPPDATA % (기본값) 대신 UNC 공유에서 키를 저장 하기 위해 시스템 다음과 같이 구성 합니다.

```csharp
public void ConfigureServices(IServiceCollection services)
   {
       services.AddDataProtection()
           .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"));

   }
   ```

>[!WARNING]
> 지 속성 키 위치를 변경 하는 경우 시스템 더 이상 자동으로 암호화 키 휴지 DPAPI는 적절 한 암호화 메커니즘 인지 알 수 없기 때문.

<a name=configuring-x509-certificate></a>

키를 보호 상태일 때 ProtectKeysWith * 구성 Api 중 하나를 호출 하 여 시스템을 구성할 수 있습니다. 아래 예제에서는 UNC 공유에서 키를 저장 및 휴지 상태의 특정 X.509 인증서와 해당 키를 암호화 하는 것이 좋습니다.

```csharp
public void ConfigureServices(IServiceCollection services)
     {
         services.AddDataProtection()
             .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"))
             .ProtectKeysWithCertificate("thumbprint");
     }
   ```

참조 [상태일 때 키 암호화](../implementation/key-encryption-at-rest.md#data-protection-implementation-key-encryption-at-rest) 더 많은 예제 및 기본 제공 키 암호화 메커니즘에 대 한 토론 합니다.

90 일 하는 대신 기본 키 수명 14 일을 사용 하 여 시스템을 구성 하려면 다음 예제를 살펴보세요.

```csharp
public void ConfigureServices(IServiceCollection services)
   {
       services.AddDataProtection()
           .SetDefaultKeyLifetime(TimeSpan.FromDays(14));
   }
   ```

기본적으로 동일한 물리적 키 저장소를 공유 하는 경우에 데이터 보호 시스템에서 다른 응용 프로그램을 격리 합니다. 그러면 응용 프로그램을 다른 사용자의 보호 된 페이로드를 파악 합니다. 두 개의 다른 응용 프로그램 간에 보호 된 페이로드를 공유 하려면 두 응용 프로그램에서 같이 동일한 응용 프로그램 이름을 전달 하는 시스템 구성는 아래 예제:

<a name=data-protection-code-sample-application-name></a>

<!-- literal_block {"ids": ["data-protection-code-sample-application-name"], "linenos": false, "names": ["data-protection-code-sample-application-name"], "xml:space": "preserve", "language": "csharp"} -->

```csharp
public void ConfigureServices(IServiceCollection services)
   {
       services.AddDataProtection()
           .SetApplicationName("my application");
   }
   ```

<a name=data-protection-configuring-disable-automatic-key-generation></a>

마지막으로, 여기서 원하지 않는 응용 프로그램을 자동으로 만료 거의 도달 하면 키를 롤링 하는 시나리오를 할 수 있습니다. 이 한 가지 예는 여기서 기본 응용 프로그램은 키 관리 문제를 담당 하 고 키 링의 읽기 전용 보기를 간단히 포함 하는 모든 보조 응용 프로그램 기본 / 보조 관계를 설정 하는 응용 프로그램을 수 있습니다. 보조 응용 프로그램 키 고리를 아래와 같이 시스템을 구성 하 여 읽기 전용으로 처리 하도록 구성할 수 있습니다.

```csharp
public void ConfigureServices(IServiceCollection services)
   {
     services.AddDataProtection()
         .DisableAutomaticKeyGeneration();
   }
   ```

<a name=data-protection-configuration-per-app-isolation></a>

## <a name="per-application-isolation"></a>응용 프로그램별 격리

ASP.NET Core 호스트에서 데이터 보호 시스템을 제공 하는 경우 해당 응용 프로그램이 동일한 작업자 프로세스 계정에서 실행 되 고 동일한 마스터 키 자료를 사용 하는 경우에 응용 프로그램에서 서로 분리 자동으로 됩니다. 이 다소 비슷합니다 IsolateApps 한정자 System.Web의에서 <machineKey> 요소입니다.

고유 테 넌 트로 로컬 컴퓨터에서 각 응용 프로그램을 고려 하 여 해당 격리 메커니즘 작동 하므로 자동으로 지정된 된 응용 프로그램에 대 한 루트 IDataProtector 포함 되어 응용 프로그램 ID는 판별자 있습니다. 응용 프로그램의 고유 ID는 두 위치 중 하나에서 제공 됩니다.

1. 응용 프로그램, IIS에서 호스팅되는 경우 고유 식별자는 응용 프로그램의 구성 경로입니다. 응용 프로그램은 팜 환경에 배포 하는 경우 팜의 모든 컴퓨터에 IIS 환경 비슷하게 구성 이라고 가정할이 값을 안정적 이어야 합니다.

2. 응용 프로그램은 IIS에서 호스팅되지 않는 하는 경우 고유 식별자는 응용 프로그램의 실제 경로입니다.

고유 식별자는 다시 설정-개별 응용 프로그램 및 컴퓨터 자체 유지 되도록 설계 되었습니다.

이 격리 메커니즘은 응용 프로그램 악성 되지 않는다고 가정 합니다. 악성 응용 프로그램이 동일한 작업자 프로세스 계정에서 실행 중인 다른 응용 프로그램에 항상 영향을 줄 수 있습니다. 응용 프로그램 상호 신뢰할 수 없는 공유 호스팅 환경에서 호스팅 공급자는 운영 체제 수준 등 응용 프로그램의 기본 키 저장소를 분리 하면 응용 프로그램을 격리할 수 있도록 단계를 수행 해야 합니다.

데이터 보호 시스템 (예: 경우 개발자 인스턴스화합니다 자신 DataProtectionProvider 구체적인 형식을 통해) ASP.NET 핵심 호스트에서 제공 하지 않는 응용 프로그램 격리는 기본적으로 사용 하지 않도록 설정 하 고 동일한 키 자료에서 지 원하는 모든 응용 프로그램으로 적절 한 목적으로 제공 페이로드를 공유할 수 있습니다. 이 환경에서 응용 프로그램 격리를 제공 하려면 구성 개체에서 SetApplicationName 메서드를 호출, 참조는 [코드 샘플](#data-protection-code-sample-application-name) 위에 있습니다.

<a name=data-protection-changing-algorithms></a>

## <a name="changing-algorithms"></a>알고리즘 변경

데이터 보호 스택 새로 생성 된 키에 의해 사용 되는 기본 알고리즘을 변경할 수 있습니다. 와 같이 구성 콜백에서 UseCryptographicAlgorithms를 호출 하는 가장 간단한 방법은이 작업을 수행 하는 것은 아래 예제입니다.

```csharp
services.AddDataProtection()
       .UseCryptographicAlgorithms(new AuthenticatedEncryptionSettings()
       {
           EncryptionAlgorithm = EncryptionAlgorithm.AES_256_CBC,
           ValidationAlgorithm = ValidationAlgorithm.HMACSHA256
       });
   ```

기본 EncryptionAlgorithm 및 ValidationAlgorithm은&256;-AES-CBC 및 HMACSHA256, 각각입니다. 기본 정책을 통해 시스템 관리자가 설정할 수 있습니다 [컴퓨터 단위 정책](machine-wide-policy.md), 하지만 UseCryptographicAlgorithms 명시적으로 호출 기본 정책을 재정의 합니다.

UseCryptographicAlgorithms 호출 (미리 정의 된 기본 제공 목록)에서 원하는 알고리즘을 지정 하려면 개발자를 개발자 알고리즘의 구현에 대해 걱정할 필요가 없습니다. 예를 들어, 데이터 보호 시스템 위의 시나리오에서 사용 하려고 합니다 AES CNG 구현을 Windows에서 실행 되는 경우, 그렇지 않으면 대체 됩니다 관리 되는 System.Security.Cryptography.Aes 클래스에 있습니다.

에 표시 된 대로 UseCustomCryptographicAlgorithms에 대 한 호출을 통해 원하는 경우 개발자는 구현을 수동으로 지정할 수는 아래 예제입니다.

>[!TIP]
> 알고리즘을 변경 하는 키 링에 있는 기존 키 적용 되지 않습니다. 새로 생성 된 키를만 영향을 줍니다.

<a name=data-protection-changing-algorithms-custom-managed></a>

### <a name="specifying-custom-managed-algorithms"></a>사용자 지정 관리 되는 알고리즘을 지정

사용자 지정 관리 되는 알고리즘을 지정 하려면 구현 형식을 가리키는 ManagedAuthenticatedEncryptionSettings 인스턴스를 만듭니다.

```csharp
serviceCollection.AddDataProtection()
       .UseCustomCryptographicAlgorithms(new ManagedAuthenticatedEncryptionSettings()
       {
           // a type that subclasses SymmetricAlgorithm
           EncryptionAlgorithmType = typeof(Aes),

           // specified in bits
           EncryptionAlgorithmKeySize = 256,

           // a type that subclasses KeyedHashAlgorithm
           ValidationAlgorithmType = typeof(HMACSHA256)
       });
   ```

일반적으로 * 형식 속성 콘크리트를 가리켜야 합니다. SymmetricAlgorithm와 KeyedHashAlgorithm, 구현 (공용 매개 변수가 없는 생성자)를 통해 인스턴스화할 수 있는 경우에 시스템 특수 한 경우와 같은 일부 값 typeof(Aes) 편의 위해.

> [!NOTE]
> ≥ 128 비트의 키 길이 ≥ 64 비트의 블록 크기는 SymmetricAlgorithm 있어야 하 고 PKCS #7 패딩 CBC 모드 암호화를 지원 해야 합니다. KeyedHashAlgorithm의 다이제스트 크기 여야 합니다. > = 128 비트 키 길이 해시 알고리즘의 다이제스트 길이를 지원 해야 합니다. KeyedHashAlgorithm HMAC를 엄격 하 게 필요 하지 않습니다.

<a name=data-protection-changing-algorithms-cng></a>

### <a name="specifying-custom-windows-cng-algorithms"></a>사용자 지정 Windows CNG 알고리즘 지정

CBC 모드 암호화 + HMAC 유효성 검사를 사용 하 여 사용자 지정 Windows CNG 알고리즘을 지정 하려면 알고리즘 정보를 포함 하는 CngCbcAuthenticatedEncryptionSettings 인스턴스를 만듭니다.

```csharp
services.AddDataProtection()
       .UseCustomCryptographicAlgorithms(new CngCbcAuthenticatedEncryptionSettings()
       {
           // passed to BCryptOpenAlgorithmProvider
           EncryptionAlgorithm = "AES",
           EncryptionAlgorithmProvider = null,

           // specified in bits
           EncryptionAlgorithmKeySize = 256,

           // passed to BCryptOpenAlgorithmProvider
           HashAlgorithm = "SHA256",
           HashAlgorithmProvider = null
       });
   ```

> [!NOTE]
> 대칭 블록 암호화 알고리즘 ≥ 128 비트의 키 길이 ≥ 64 비트의 블록 크기 있고 PKCS #7 패딩 CBC 모드 암호화를 지원 해야 합니다. 해시 알고리즘의 다이제스트 크기 여야 합니다. > = 128 비트 및 BCRYPT_ALG_HANDLE_HMAC_FLAG 플래그 열을 지원 해야 합니다. * 공급자 속성은 지정된 된 알고리즘에 대 한 기본 공급자를 사용 하 여 null로 설정할 수 있습니다. 참조는 [BCryptOpenAlgorithmProvider](https://msdn.microsoft.com/en-us/library/windows/desktop/aa375479(v=vs.85).aspx) 자세한 정보에 대 한 설명서입니다.

Galois/카운터 모드 암호화 + 유효성 검사를 사용 하 여 사용자 지정 Windows CNG 알고리즘을 지정 하려면 알고리즘 정보를 포함 하는 CngGcmAuthenticatedEncryptionSettings 인스턴스를 만듭니다.

```csharp
services.AddDataProtection()
       .UseCustomCryptographicAlgorithms(new CngGcmAuthenticatedEncryptionSettings()
       {
           // passed to BCryptOpenAlgorithmProvider
           EncryptionAlgorithm = "AES",
           EncryptionAlgorithmProvider = null,

           // specified in bits
           EncryptionAlgorithmKeySize = 256
       });
   });
   ```

> [!NOTE]
> 대칭 블록 암호화 알고리즘 ≥ 128 비트의 키 길이 정확 하 게 128 비트의 블록 크기 있고 GCM 암호화를 지원 해야 합니다. 지정된 된 알고리즘에 대 한 기본 공급자를 사용 하 여 null로 EncryptionAlgorithmProvider 속성을 설정할 수 있습니다. 참조는 [BCryptOpenAlgorithmProvider](https://msdn.microsoft.com/en-us/library/windows/desktop/aa375479(v=vs.85).aspx) 자세한 정보에 대 한 설명서입니다.

### <a name="specifying-other-custom-algorithms"></a>다른 사용자 지정 알고리즘을 지정

경우에 첫 번째 클래스 API로 서 노출 되지 데이터 보호 시스템은 거의 모든 종류의 알고리즘을 지정할 수 있도록 충분히 확장 가능 합니다. 예를 들어, HSM 내에 포함 된 모든 키를 유지 하 고 암호화 및 암호 해독 루틴의 핵심 사용자 지정 구현을 제공 하도록 수입니다. 자세한 내용은 핵심 암호화 확장성 섹션 IAuthenticatedEncryptorConfiguration를 참조 하십시오.

### <a name="see-also"></a>참고 항목

[비 DI 인식 시나리오](non-di-scenarios.md)

[컴퓨터 단위 정책](machine-wide-policy.md)

