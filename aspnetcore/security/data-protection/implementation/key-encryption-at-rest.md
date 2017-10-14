---
title: "미사용 데이터 암호화 키"
author: rick-anderson
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: f2bbbf4e-0945-43ce-be59-8bf19e448798
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/implementation/key-encryption-at-rest
ms.openlocfilehash: 5d0eb4036a3d491336cbe9357779c150b5cbb236
ms.sourcegitcommit: 8f4d4fad1ca27adf9e396f5c205c9875a3963664
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/13/2017
---
# <a name="key-encryption-at-rest"></a>미사용 데이터 암호화 키

<a name="data-protection-implementation-key-encryption-at-rest"></a>

기본적으로 데이터 보호 시스템 [경험적 접근을 사용](../configuration/default-settings.md#data-protection-default-settings) 어떻게 암호화 키 자료를 확인 하려면 휴지 암호화 해야 합니다. 개발자는 추론은 무시 하 고 수동으로 키를 미사용 암호화 해야 하는 방법을 지정할 수도 있습니다.

> [!NOTE]
> 나머지 메커니즘에는 명시적 키 암호화를 지정 하는 경우 데이터 보호 시스템의 추론은 제공 하는 기본 키 저장소 메커니즘 등록을 취소 합니다. 수행 해야 [명시적 키 저장소 메커니즘을 지정](key-storage-providers.md#data-protection-implementation-key-storage-providers), 그렇지 않으면 데이터 보호 시스템 시작에 실패 합니다.

<a name="data-protection-implementation-key-encryption-at-rest-providers"></a>

데이터 보호 시스템 세 가지 기본 키 암호화 메커니즘 함께 제공 됩니다.

## <a name="windows-dpapi"></a>Windows DPAPI

*이 메커니즘은 Windows 에서만 사용할 수 있습니다.*

통해 Windows DPAPI를 사용 하면 키 자료를 암호화할 [cryptprotectdata가](https://msdn.microsoft.com/library/windows/desktop/aa380261(v=vs.85).aspx) 전에 저장소에 유지 되 고 있습니다. DPAPI는 현재 시스템 외부에서 되지 읽을 수 있는 데이터에 대 한 적절 한 암호화 메커니즘 (Active Directory까지 이러한 키를 백업할 수는; 참조 하지만 [DPAPI 및 로밍 프로필](https://support.microsoft.com/kb/309408/#6)). 예를 DPAPI 키 휴지 암호화를 구성 합니다.

```csharp
sc.AddDataProtection()
       // only the local user account can decrypt the keys
       .ProtectKeysWithDpapi();
   ```

ProtectKeysWithDpapi 매개 변수 없이 호출 되 면 현재 Windows 사용자 계정 으로만 지속형된 키 자료를 읽을 수 있습니다. 컴퓨터 (뿐 아니라 현재 사용자 계정)의 모든 사용자 계정이 되도록 키 자료를 해독할 수와 같이 필요에 따라 지정할 수 있습니다는 아래 예제입니다.

```csharp
sc.AddDataProtection()
       // all user accounts on the machine can decrypt the keys
       .ProtectKeysWithDpapi(protectToLocalMachine: true);
   ```

## <a name="x509-certificate"></a>X.509 인증서

*이 메커니즘에서 사용할 수 없으면 `.NET Core 1.0` 또는 `1.1`합니다.*

여러 컴퓨터 간에 분산 되는 응용 프로그램 편리한 컴퓨터 간에 공유 하는 X.509 인증서를 배포 하 고 미사용 키의 암호화에 대 한이 인증서를 사용 하려면 응용 프로그램을 구성할 수도 있습니다. 예를 보려면 아래를 참조 하십시오.

```csharp
sc.AddDataProtection()
       // searches the cert store for the cert with this thumbprint
       .ProtectKeysWithCertificate("3BCE558E2AD3E0E34A7743EAB5AEA2A9BD2575A0");
   ```

.NET Framework 제한으로 인해 CAPI 개인 키가 있는 인증서만 지원 됩니다. 참조 [인증서 기반 암호화와 Windows DPAPI-NG](#data-protection-implementation-key-encryption-at-rest-dpapi-ng) 아래 가능한 해결 방법에 이러한 제한 사항에 대 한 합니다.

<a name="data-protection-implementation-key-encryption-at-rest-dpapi-ng"></a>

## <a name="windows-dpapi-ng"></a>Windows DPAPI NG

*이 메커니즘은 Windows 8에만 사용할 수 있는 / Windows Server 2012 이상.*

Windows 8 부터는 운영 체제 DPAPI NG (CNG DPAPI 라고도 함)를 지원 합니다. Microsoft 사용 시나리오는 다음과 같이 배치합니다.

   그러나 클라우드 컴퓨팅 종종 해야 해당 콘텐츠를 암호화 된 컴퓨터에 다른 암호를 해독할 수 있습니다. 따라서 Windows 8 부터는 Microsoft 확장 된 클라우드 시나리오를 포함 하는 비교적 간단 하므로 API를 사용 하는 것입니다. DPAPI-NG 라는이 새로운 API를 사용 하는 서로 다른 컴퓨터에 적절 한 인증 및 권한 부여 후 해제 하는 데 사용할 수 있는 보안 주체 집합에이 보호 하 여 비밀 (키, 암호, 키 자료) 및 메시지를 안전 하 게 공유할 수 있습니다.

   [CNG DPAPI에 대 한](https://msdn.microsoft.com/library/windows/desktop/hh706794(v=vs.85).aspx)

주 서버는 보호 설명자 규칙으로 인코딩됩니다. 고려는 아래 예제에서는 암호화 키 자료를 도메인에 가입 된 사용자만 지정 된 SID 가진 키 자료를 해독할 수 있습니다.

```csharp
sc.AddDataProtection()
     // uses the descriptor rule "SID=S-1-5-21-..."
     .ProtectKeysWithDpapiNG("SID=S-1-5-21-...",
       flags: DpapiNGProtectionDescriptorFlags.None);
   ```

ProtectKeysWithDpapiNG 매개 변수가 없는 오버 로드를 이기도합니다. 이 규칙을 지정 하기 위한 편리한 방법 "SID 마이닝 =" 여기서 내 것은 현재 Windows 사용자 계정의 SID.

```csharp
sc.AddDataProtection()
     // uses the descriptor rule "SID={current account SID}"
     .ProtectKeysWithDpapiNG();
   ```

이 시나리오에서 AD 도메인 컨트롤러는 DPAPI NG 작업에 의해 사용 된 암호화 키를 배포 하는 일을 담당 합니다. 대상 사용자 (제공 하는 프로세스가 실행 되는 해당 id에서) 도메인에 가입 된 컴퓨터에서 암호화 된 페이로드를 해독할 수 됩니다.

## <a name="certificate-based-encryption-with-windows-dpapi-ng"></a>인증서 기반 암호화와 Windows DPAPI-NG

Windows 8.1 실행 하는 경우 / Windows Server 2012 R2 이상 버전에서는 사용할 수 있습니다 Windows DPAPI NG 인증서 기반 암호화를 수행 하는 응용 프로그램에서 실행 중인 경우에 [.NET Core](https://www.microsoft.com/net/core)합니다. 를 이용 하려면이 규칙 설명자 문자열을 사용 하 여 "인증서 HashId:thumbprint =" 지문을 사용 하는 인증서의 16 진수로 인코딩된 SHA1 지문을 위치입니다. 예를 보려면 아래를 참조 하십시오.

```csharp
sc.AddDataProtection()
       // searches the cert store for the cert with this thumbprint
       .ProtectKeysWithDpapiNG("CERTIFICATE=HashId:3BCE558E2AD3E0E34A7743EAB5AEA2A9BD2575A0",
           flags: DpapiNGProtectionDescriptorFlags.None);
   ```

Windows 8.1이 리포지토리를 가리켜야 하는 응용 프로그램을 실행 해야 / Windows Server 2012 R2 또는 나중에이 키를 알아볼 수 있습니다.

## <a name="custom-key-encryption"></a>사용자 지정 키 암호화

기본 메커니즘 적합 하지 않은 경우 개발자는 사용자 지정 IXmlEncryptor 제공 하 여 고유 키 암호화 메커니즘을 지정할 수 있습니다.
