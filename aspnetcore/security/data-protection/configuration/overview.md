---
title: "데이터 보호를 구성합니다."
author: rick-anderson
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 0e4881a3-a94d-4e35-9c1c-f025d65dcff0
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/configuration/overview
ms.openlocfilehash: 9361dcec89a0f35067181523cc56637d629614ff
ms.sourcegitcommit: 6e83c55eb0450a3073ef2b95fa5f5bcb20dbbf89
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/28/2017
---
# <a name="configuring-data-protection"></a><span data-ttu-id="d9152-103">데이터 보호를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="d9152-103">Configuring data protection</span></span>

<a name=data-protection-configuring></a>

<span data-ttu-id="d9152-104">데이터 보호 시스템 초기화 될 때 일부 적용 [기본 설정](default-settings.md#data-protection-default-settings) 운영 환경에 따라 합니다.</span><span class="sxs-lookup"><span data-stu-id="d9152-104">When the data protection system is initialized it applies some [default settings](default-settings.md#data-protection-default-settings) based on the operational environment.</span></span> <span data-ttu-id="d9152-105">이 설정은 일반적으로 단일 컴퓨터에서 실행 중인 응용 프로그램에 유용 합니다.</span><span class="sxs-lookup"><span data-stu-id="d9152-105">These settings are generally good for applications running on a single machine.</span></span> <span data-ttu-id="d9152-106">개발자는 이러한 변경 해야 할 수는 있는 경우에 따라 (아마도 규정 준수 상의 이유로 또는 여러 컴퓨터 간에 분산 되는 응용 프로그램 때문에), 이러한 시나리오에 대 한 데이터 보호 시스템 풍부한 구성 API를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="d9152-106">There are some cases where a developer may want to change these (perhaps because their application is spread across multiple machines or for compliance reasons), and for these scenarios the data protection system offers a rich configuration API.</span></span>

<a name=data-protection-configuration-callback></a>

<span data-ttu-id="d9152-107">확장 메서드가 AddDataProtection는 IDataProtectionBuilder 반환 하는 확장 메서드를 함께 결합할 수 옵션을 다양 한 데이터 보호를 구성 하려면 논리 노출 합니다.</span><span class="sxs-lookup"><span data-stu-id="d9152-107">There is an extension method AddDataProtection which returns an IDataProtectionBuilder which itself exposes extension methods that you can chain together to configure various data protection options.</span></span> <span data-ttu-id="d9152-108">예를 들어 % LOCALAPPDATA % (기본값) 대신 UNC 공유에서 키를 저장 하려면 시스템이 다음과 같이 구성.</span><span class="sxs-lookup"><span data-stu-id="d9152-108">For instance, to store keys at a UNC share instead of %LOCALAPPDATA% (the default), configure the system as follows:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"));
}
```

>[!WARNING]
> <span data-ttu-id="d9152-109">지 속성 키 위치를 변경 하면 시스템 더 이상 자동으로 암호화 키 미사용 DPAPI는 적절 한 암호화 메커니즘 인지 알 수 없기 때문.</span><span class="sxs-lookup"><span data-stu-id="d9152-109">If you change the key persistence location, the system will no longer automatically encrypt keys at rest since it doesn't know whether DPAPI is an appropriate encryption mechanism.</span></span>

<a name=configuring-x509-certificate></a>

<span data-ttu-id="d9152-110">시스템을 미사용 키는 ProtectKeysWith 중 하나를 호출 하 여 보호를 구성할 수 있습니다\* 구성 Api입니다.</span><span class="sxs-lookup"><span data-stu-id="d9152-110">You can configure the system to protect keys at rest by calling any of the ProtectKeysWith\* configuration APIs.</span></span> <span data-ttu-id="d9152-111">아래 예제에서는 UNC 공유에서 키를 저장 하 고 저장 된 상태의 특정 X.509 인증서와 해당 키를 암호화 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="d9152-111">Consider the example below, which stores keys at a UNC share and encrypts those keys at rest with a specific X.509 certificate.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"))
        .ProtectKeysWithCertificate("thumbprint");
}
```

<span data-ttu-id="d9152-112">참조 [미사용 데이터 암호화 키](../implementation/key-encryption-at-rest.md#data-protection-implementation-key-encryption-at-rest) 추가 예제 및 기본 제공 키 암호화 메커니즘에 대 한 토론 합니다.</span><span class="sxs-lookup"><span data-stu-id="d9152-112">See [key encryption at rest](../implementation/key-encryption-at-rest.md#data-protection-implementation-key-encryption-at-rest) for more examples and for discussion on the built-in key encryption mechanisms.</span></span>

<span data-ttu-id="d9152-113">14 일의 기본 키 수명 90 일 대신 사용 하도록 시스템을 구성 하려면 다음 예제를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="d9152-113">To configure the system to use a default key lifetime of 14 days instead of 90 days, consider the following example:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .SetDefaultKeyLifetime(TimeSpan.FromDays(14));
}
```

<span data-ttu-id="d9152-114">기본적으로 데이터 보호 시스템에서는 다른 응용 프로그램이 동일한 물리적 키 저장소를 공유 하는 경우에 합니다.</span><span class="sxs-lookup"><span data-stu-id="d9152-114">By default the data protection system isolates applications from one another, even if they're sharing the same physical key repository.</span></span> <span data-ttu-id="d9152-115">이렇게 하면 다른 사용자의 보호 된 페이로드를 이해 하는에서 응용 프로그램을 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d9152-115">This prevents the applications from understanding each other's protected payloads.</span></span> <span data-ttu-id="d9152-116">두 개의 서로 다른 응용 프로그램 간에 보호 된 페이로드를 공유 하려면 두 응용 프로그램에서 같이 동일한 응용 프로그램 이름을 전달 하는 시스템 구성의 아래 예제:</span><span class="sxs-lookup"><span data-stu-id="d9152-116">To share protected payloads between two different applications, configure the system passing in the same application name for both applications as in the below example:</span></span>

<a name=data-protection-code-sample-application-name></a>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .SetApplicationName("my application");
}
```

<a name=data-protection-configuring-disable-automatic-key-generation></a>

<span data-ttu-id="d9152-117">마지막으로, 여기서 원하지 않는 만료 거의 도달 하면 자동으로 키를 롤백하기 위해 응용 프로그램 시나리오를 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d9152-117">Finally, you may have a scenario where you do not want an application to automatically roll keys as they approach expiration.</span></span> <span data-ttu-id="d9152-118">이러한 예로 여기서 기본 응용 프로그램은 키 관리 문제를 담당 하 고 키 링의 읽기 전용 보기를 단순히 포함 하는 모든 보조 응용 프로그램 기본 / 보조 관계를 설정 하는 응용 프로그램을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d9152-118">One example of this might be applications set up in a primary / secondary relationship, where only the primary application is responsible for key management concerns, and all secondary applications simply have a read-only view of the key ring.</span></span> <span data-ttu-id="d9152-119">아래와 같이 시스템을 구성 하 여 키 링 읽기 전용으로 처리 하도록 보조 응용 프로그램을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d9152-119">The secondary applications can be configured to treat the key ring as read-only by configuring the system as below:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .DisableAutomaticKeyGeneration();
}
```

<a name=data-protection-configuration-per-app-isolation></a>

## <a name="per-application-isolation"></a><span data-ttu-id="d9152-120">응용 프로그램 격리</span><span class="sxs-lookup"><span data-stu-id="d9152-120">Per-application isolation</span></span>

<span data-ttu-id="d9152-121">데이터 보호 시스템 ASP.NET Core 호스트에 의해 제공 되 면 해당 응용 프로그램이 동일한 작업자 프로세스 계정에서 실행 되 고 동일한 마스터 키 자료를 사용 하는 경우에 응용 프로그램을 서로 격리 자동으로 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d9152-121">When the data protection system is provided by an ASP.NET Core host, it will automatically isolate applications from one another, even if those applications are running under the same worker process account and are using the same master keying material.</span></span> <span data-ttu-id="d9152-122">이 비슷합니다는 IsolateApps 한정자 System.Web의에서 <machineKey> 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="d9152-122">This is somewhat similar to the IsolateApps modifier from System.Web's <machineKey> element.</span></span>

<span data-ttu-id="d9152-123">고유한 테 넌 트로 로컬 컴퓨터에서 각 응용 프로그램을 고려 하 여 격리 메커니즘 작동 하므로 자동으로 지정된 된 응용 프로그램에 대 한 루트 IDataProtector ID를 포함 응용 프로그램 판별자로 합니다.</span><span class="sxs-lookup"><span data-stu-id="d9152-123">The isolation mechanism works by considering each application on the local machine as a unique tenant, thus the IDataProtector rooted for any given application automatically includes the application ID as a discriminator.</span></span> <span data-ttu-id="d9152-124">응용 프로그램의 고유 ID 두 위치 중 하나에서 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d9152-124">The application's unique ID comes from one of two places.</span></span>

1. <span data-ttu-id="d9152-125">응용 프로그램을 IIS에서 호스트 하는 경우 고유 식별자는 응용 프로그램의 구성 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="d9152-125">If the application is hosted in IIS, the unique identifier is the application's configuration path.</span></span> <span data-ttu-id="d9152-126">팜 환경에 응용 프로그램을 배포한 경우 팜의 모든 컴퓨터에 IIS 환경 비슷하게 구성 있다고 가정할 경우이 값을 안정적 이어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d9152-126">If an application is deployed in a farm environment, this value should be stable assuming that the IIS environments are configured similarly across all machines in the farm.</span></span>

2. <span data-ttu-id="d9152-127">응용 프로그램은 IIS에서 호스트 되지 않는, 고유 식별자는 응용 프로그램의 실제 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="d9152-127">If the application is not hosted in IIS, the unique identifier is the physical path of the application.</span></span>

<span data-ttu-id="d9152-128">고유 식별자는 다시 설정-개별 응용 프로그램 및 컴퓨터 자체의 유지 되도록 설계 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="d9152-128">The unique identifier is designed to survive resets - both of the individual application and of the machine itself.</span></span>

<span data-ttu-id="d9152-129">이 격리 메커니즘에서는 응용 프로그램은 악성이 아닌지 가정 합니다.</span><span class="sxs-lookup"><span data-stu-id="d9152-129">This isolation mechanism assumes that the applications are not malicious.</span></span> <span data-ttu-id="d9152-130">악성 응용 프로그램 같은 작업자 프로세스 계정에서 실행 중인 다른 응용 프로그램에 항상 영향을 줄 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d9152-130">A malicious application can always impact any other application running under the same worker process account.</span></span> <span data-ttu-id="d9152-131">응용 프로그램 상호 신뢰할 수 없는 공유 호스팅 환경에서 호스팅 공급자는 여러 응용 프로그램에서 분리 응용 프로그램의 기본 키 저장소를 포함 하 여 운영 체제 수준 격리를 보장 하는 단계를 수행 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d9152-131">In a shared hosting environment where applications are mutually untrusted, the hosting provider should take steps to ensure OS-level isolation between applications, including separating the applications' underlying key repositories.</span></span>

<span data-ttu-id="d9152-132">데이터 보호 시스템 (예: 경우 개발자 인스턴스화하지 자신 DataProtectionProvider 구체적인 형식을 통해) ASP.NET Core 호스트에서 제공 하지 않는, 응용 프로그램 격리 기본적으로 비활성화 되 고 동일한 키 지정에서 지 원하는 모든 응용 프로그램 자료 적절 한 목적으로 제공으로 페이로드를 공유할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d9152-132">If the data protection system is not provided by an ASP.NET Core host (e.g., if the developer instantiates it himself via the DataProtectionProvider concrete type), application isolation is disabled by default, and all applications backed by the same keying material can share payloads as long as they provide the appropriate purposes.</span></span> <span data-ttu-id="d9152-133">이 환경에서 응용 프로그램 격리를 제공 하려면 구성 개체에서 SetApplicationName 메서드를 호출, 참조는 [코드 샘플](#data-protection-code-sample-application-name) 위에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d9152-133">To provide application isolation in this environment, call the SetApplicationName method on the configuration object, see the [code sample](#data-protection-code-sample-application-name) above.</span></span>

<a name=data-protection-changing-algorithms></a>

## <a name="changing-algorithms"></a><span data-ttu-id="d9152-134">알고리즘 변경</span><span class="sxs-lookup"><span data-stu-id="d9152-134">Changing algorithms</span></span>

<span data-ttu-id="d9152-135">데이터 보호 스택의 새로 생성 된 키로 사용 되는 기본 알고리즘을 변경 하는 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="d9152-135">The data protection stack allows changing the default algorithm used by newly-generated keys.</span></span> <span data-ttu-id="d9152-136">이 작업을 수행 하는 가장 간단한 방법은에서 같이 구성 콜백에서 UseCryptographicAlgorithms를 호출 하는 것은 아래 예제입니다.</span><span class="sxs-lookup"><span data-stu-id="d9152-136">The simplest way to do this is to call UseCryptographicAlgorithms from the configuration callback, as in the below example.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="d9152-137">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="d9152-137">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```csharp
services.AddDataProtection()
    .UseCryptographicAlgorithms(new AuthenticatedEncryptorConfiguration()
    {
        EncryptionAlgorithm = EncryptionAlgorithm.AES_256_CBC,
        ValidationAlgorithm = ValidationAlgorithm.HMACSHA256
    });
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="d9152-138">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="d9152-138">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

```csharp
services.AddDataProtection()
    .UseCryptographicAlgorithms(new AuthenticatedEncryptionSettings()
    {
        EncryptionAlgorithm = EncryptionAlgorithm.AES_256_CBC,
        ValidationAlgorithm = ValidationAlgorithm.HMACSHA256
    });
```

---

<span data-ttu-id="d9152-139">기본 EncryptionAlgorithm 및 ValidationAlgorithm은 AES-256-CBC 및 HMACSHA256, 각각입니다.</span><span class="sxs-lookup"><span data-stu-id="d9152-139">The default EncryptionAlgorithm and ValidationAlgorithm are AES-256-CBC and HMACSHA256, respectively.</span></span> <span data-ttu-id="d9152-140">기본 정책을 통해 시스템 관리자가 설정할 수 있습니다 [컴퓨터 넓은 정책](machine-wide-policy.md), 하지만 UseCryptographicAlgorithms 하기 위해 명시적 호출이 기본 정책을 재정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="d9152-140">The default policy can be set by a system administrator via [Machine Wide Policy](machine-wide-policy.md), but an explicit call to UseCryptographicAlgorithms will override the default policy.</span></span>

<span data-ttu-id="d9152-141">UseCryptographicAlgorithms 호출을 사용 하면 미리 정의 된 기본 제공 목록), (에서 원하는 알고리즘을 지정 하려면 개발자 및 개발자는 알고리즘의 구현에 걱정할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="d9152-141">Calling UseCryptographicAlgorithms will allow the developer to specify the desired algorithm (from a predefined built-in list), and the developer does not need to worry about the implementation of the algorithm.</span></span> <span data-ttu-id="d9152-142">예를 들어, 데이터 보호 시스템 위의 시나리오에서 사용 하도록 시도할 AES의 CNG 구현을 Windows에서 실행 되는 경우, 그렇지 않으면 대체 됩니다 관리 되는 System.Security.Cryptography.Aes 클래스에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d9152-142">For instance, in the scenario above the data protection system will attempt to use the CNG implementation of AES if running on Windows, otherwise it will fall back to the managed System.Security.Cryptography.Aes class.</span></span>

<span data-ttu-id="d9152-143">에 표시 된 대로 UseCustomCryptographicAlgorithms에 대 한 호출을 통해 원하는 경우 개발자 구현을 직접 지정할 수는 아래 예제입니다.</span><span class="sxs-lookup"><span data-stu-id="d9152-143">The developer can manually specify an implementation if desired via a call to UseCustomCryptographicAlgorithms, as show in the below examples.</span></span>

>[!TIP]
> <span data-ttu-id="d9152-144">알고리즘 변경 키 링의 기존 키 영향을 주지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d9152-144">Changing algorithms does not affect existing keys in the key ring.</span></span> <span data-ttu-id="d9152-145">새로 생성 된 키를만 영향을 줍니다.</span><span class="sxs-lookup"><span data-stu-id="d9152-145">It only affects newly-generated keys.</span></span>

<a name=data-protection-changing-algorithms-custom-managed></a>

### <a name="specifying-custom-managed-algorithms"></a><span data-ttu-id="d9152-146">관리 되는 사용자 지정 알고리즘을 지정</span><span class="sxs-lookup"><span data-stu-id="d9152-146">Specifying custom managed algorithms</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="d9152-147">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="d9152-147">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="d9152-148">관리 되는 사용자 지정 알고리즘을 지정 하려면 구현 형식을 가리키는 ManagedAuthenticatedEncryptorConfiguration 인스턴스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d9152-148">To specify custom managed algorithms, create a ManagedAuthenticatedEncryptorConfiguration instance that points to the implementation types.</span></span>

```csharp
serviceCollection.AddDataProtection()
    .UseCustomCryptographicAlgorithms(new ManagedAuthenticatedEncryptorConfiguration()
    {
        // a type that subclasses SymmetricAlgorithm
        EncryptionAlgorithmType = typeof(Aes),

        // specified in bits
        EncryptionAlgorithmKeySize = 256,

        // a type that subclasses KeyedHashAlgorithm
        ValidationAlgorithmType = typeof(HMACSHA256)
    });
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="d9152-149">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="d9152-149">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="d9152-150">관리 되는 사용자 지정 알고리즘을 지정 하려면 구현 형식을 가리키는 ManagedAuthenticatedEncryptionSettings 인스턴스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d9152-150">To specify custom managed algorithms, create a ManagedAuthenticatedEncryptionSettings instance that points to the implementation types.</span></span>

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

---

<span data-ttu-id="d9152-151">일반적으로 \*형식 속성 콘크리트를 가리켜야 SymmetricAlgorithm 및 KeyedHashAlgorithm을 구현의 (공용 매개 변수가 없는 생성자)를 통해 인스턴스화할 수 있는 경우에 시스템 특수 한 경우와 같은 일부 값 typeof(Aes) 편의 위해 .</span><span class="sxs-lookup"><span data-stu-id="d9152-151">Generally the \*Type properties must point to concrete, instantiable (via a public parameterless ctor) implementations of SymmetricAlgorithm and KeyedHashAlgorithm, though the system special-cases some values like typeof(Aes) for convenience.</span></span>

> [!NOTE]
> <span data-ttu-id="d9152-152">≥ 128 비트의 키 길이 및 ≥ 64 비트의 블록 크기는 SymmetricAlgorithm 있어야 하 고 PKCS #7 안쪽 여백 사용 하 여 CBC 모드 암호화를 지원 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d9152-152">The SymmetricAlgorithm must have a key length of ≥ 128 bits and a block size of ≥ 64 bits, and it must support CBC-mode encryption with PKCS #7 padding.</span></span> <span data-ttu-id="d9152-153">KeyedHashAlgorithm 다이제스트 크기인 있어야 합니다. > = 128 비트 키 길이 해시 알고리즘의 다이제스트 길이 지원 해야 하 고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d9152-153">The KeyedHashAlgorithm must have a digest size of >= 128 bits, and it must support keys of length equal to the hash algorithm's digest length.</span></span> <span data-ttu-id="d9152-154">KeyedHashAlgorithm HMAC를 엄격 하 게 필요 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d9152-154">The KeyedHashAlgorithm is not strictly required to be HMAC.</span></span>

<a name=data-protection-changing-algorithms-cng></a>

### <a name="specifying-custom-windows-cng-algorithms"></a><span data-ttu-id="d9152-155">사용자 지정 Windows CNG 알고리즘 지정</span><span class="sxs-lookup"><span data-stu-id="d9152-155">Specifying custom Windows CNG algorithms</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="d9152-156">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="d9152-156">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="d9152-157">CBC 모드 암호화 + HMAC 유효성 검사를 사용 하 여 사용자 지정 Windows CNG 알고리즘을 지정 하려면 알고리즘 정보를 포함 하는 CngCbcAuthenticatedEncryptorConfiguration 인스턴스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d9152-157">To specify a custom Windows CNG algorithm using CBC-mode encryption + HMAC validation, create a CngCbcAuthenticatedEncryptorConfiguration instance that contains the algorithmic information.</span></span>

```csharp
services.AddDataProtection()
    .UseCustomCryptographicAlgorithms(new CngCbcAuthenticatedEncryptorConfiguration()
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

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="d9152-158">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="d9152-158">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="d9152-159">CBC 모드 암호화 + HMAC 유효성 검사를 사용 하 여 사용자 지정 Windows CNG 알고리즘을 지정 하려면 알고리즘 정보를 포함 하는 CngCbcAuthenticatedEncryptionSettings 인스턴스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d9152-159">To specify a custom Windows CNG algorithm using CBC-mode encryption + HMAC validation, create a CngCbcAuthenticatedEncryptionSettings instance that contains the algorithmic information.</span></span>

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

---

> [!NOTE]
> <span data-ttu-id="d9152-160">블록 대칭 암호화 알고리즘 ≥ 128 비트의 키 길이 ≥ 64 비트의 블록 크기 있고 PKCS #7 안쪽 여백 사용 하 여 CBC 모드 암호화를 지원 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d9152-160">The symmetric block cipher algorithm must have a key length of ≥ 128 bits and a block size of ≥ 64 bits, and it must support CBC-mode encryption with PKCS #7 padding.</span></span> <span data-ttu-id="d9152-161">해시 알고리즘의 다이제스트 크기 있어야 합니다. > = 128 비트 및 BCRYPT_ALG_HANDLE_HMAC_FLAG 플래그로 열기를 지원 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d9152-161">The hash algorithm must have a digest size of >= 128 bits and must support being opened with the BCRYPT_ALG_HANDLE_HMAC_FLAG flag.</span></span> <span data-ttu-id="d9152-162">\*공급자 속성은 지정된 된 알고리즘에 대 한 기본 공급자를 사용 하려면 null로 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d9152-162">The \*Provider properties can be set to null to use the default provider for the specified algorithm.</span></span> <span data-ttu-id="d9152-163">참조는 [BCryptOpenAlgorithmProvider](https://msdn.microsoft.com/library/windows/desktop/aa375479(v=vs.85).aspx) 자세한 정보에 대 한 설명서입니다.</span><span class="sxs-lookup"><span data-stu-id="d9152-163">See the [BCryptOpenAlgorithmProvider](https://msdn.microsoft.com/library/windows/desktop/aa375479(v=vs.85).aspx) documentation for more information.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="d9152-164">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="d9152-164">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="d9152-165">Galois/카운터 모드 암호화 + 유효성 검사를 사용 하 여 사용자 지정 Windows CNG 알고리즘을 지정 하려면 알고리즘 정보를 포함 하는 CngGcmAuthenticatedEncryptorConfiguration 인스턴스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d9152-165">To specify a custom Windows CNG algorithm using Galois/Counter Mode encryption + validation, create a CngGcmAuthenticatedEncryptorConfiguration instance that contains the algorithmic information.</span></span>

```csharp
services.AddDataProtection()
    .UseCustomCryptographicAlgorithms(new CngGcmAuthenticatedEncryptorConfiguration()
    {
        // passed to BCryptOpenAlgorithmProvider
        EncryptionAlgorithm = "AES",
        EncryptionAlgorithmProvider = null,

        // specified in bits
        EncryptionAlgorithmKeySize = 256
    });
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="d9152-166">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="d9152-166">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="d9152-167">Galois/카운터 모드 암호화 + 유효성 검사를 사용 하 여 사용자 지정 Windows CNG 알고리즘을 지정 하려면 알고리즘 정보를 포함 하는 CngGcmAuthenticatedEncryptionSettings 인스턴스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d9152-167">To specify a custom Windows CNG algorithm using Galois/Counter Mode encryption + validation, create a CngGcmAuthenticatedEncryptionSettings instance that contains the algorithmic information.</span></span>

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
```

---

> [!NOTE]
> <span data-ttu-id="d9152-168">블록 대칭 암호화 알고리즘 ≥ 128 비트의 키 길이 정확 하 게 128 비트의 블록 크기 있고 GCM 암호화를 지원 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d9152-168">The symmetric block cipher algorithm must have a key length of ≥ 128 bits and a block size of exactly 128 bits, and it must support GCM encryption.</span></span> <span data-ttu-id="d9152-169">지정된 된 알고리즘에 대 한 기본 공급자를 사용 하려면 null로 EncryptionAlgorithmProvider 속성을 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d9152-169">The EncryptionAlgorithmProvider property can be set to null to use the default provider for the specified algorithm.</span></span> <span data-ttu-id="d9152-170">참조는 [BCryptOpenAlgorithmProvider](https://msdn.microsoft.com/library/windows/desktop/aa375479(v=vs.85).aspx) 자세한 정보에 대 한 설명서입니다.</span><span class="sxs-lookup"><span data-stu-id="d9152-170">See the [BCryptOpenAlgorithmProvider](https://msdn.microsoft.com/library/windows/desktop/aa375479(v=vs.85).aspx) documentation for more information.</span></span>

### <a name="specifying-other-custom-algorithms"></a><span data-ttu-id="d9152-171">다른 사용자 지정 알고리즘을 지정</span><span class="sxs-lookup"><span data-stu-id="d9152-171">Specifying other custom algorithms</span></span>

<span data-ttu-id="d9152-172">경우에 첫 번째 클래스 API로 노출 되지 데이터 보호 시스템은 거의 모든 종류의 알고리즘을 지정할 수 있을 만큼 확장 가능 합니다.</span><span class="sxs-lookup"><span data-stu-id="d9152-172">Though not exposed as a first-class API, the data protection system is extensible enough to allow specifying almost any kind of algorithm.</span></span> <span data-ttu-id="d9152-173">예를 들어 HSM 내에 포함 된 모든 키를 유지 하 고 암호화 및 암호 해독 루틴의 핵심 사용자 지정 구현을 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d9152-173">For example, it is possible to keep all keys contained within an HSM and to provide a custom implementation of the core encryption and decryption routines.</span></span> <span data-ttu-id="d9152-174">IAuthenticatedEncryptorConfiguration 핵심 암호화 확장성 섹션에 대 한 자세한 내용은 참조 하십시오.</span><span class="sxs-lookup"><span data-stu-id="d9152-174">See IAuthenticatedEncryptorConfiguration in the core cryptography extensibility section for more information.</span></span>

### <a name="see-also"></a><span data-ttu-id="d9152-175">참고 항목</span><span class="sxs-lookup"><span data-stu-id="d9152-175">See also</span></span>

* [<span data-ttu-id="d9152-176">비 DI 인식 시나리오</span><span class="sxs-lookup"><span data-stu-id="d9152-176">Non DI Aware Scenarios</span></span>](non-di-scenarios.md)
* [<span data-ttu-id="d9152-177">컴퓨터 수준 정책</span><span class="sxs-lookup"><span data-stu-id="d9152-177">Machine Wide Policy</span></span>](machine-wide-policy.md)
