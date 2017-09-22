---
title: "키 저장소 공급자"
author: rick-anderson
description: "키 저장소 공급자"
keywords: "encryption,ASP.NET 코어"
ms.author: riande
manager: wpickett
ms.date: 01/14/2017
ms.topic: article
ms.assetid: 423e0a79-2f34-44c4-aaf3-146a53c39251
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/implementation/key-storage-providers
ms.openlocfilehash: 1c73608245e668c0810813e29f78f1ac3dacc414
ms.sourcegitcommit: 78d28178345a0eea91556e4cd1adad98b1446db8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2017
---
# <a name="key-storage-providers"></a><span data-ttu-id="4aa7e-104">키 저장소 공급자</span><span class="sxs-lookup"><span data-stu-id="4aa7e-104">Key storage providers</span></span>

<a name=data-protection-implementation-key-storage-providers></a>

<span data-ttu-id="4aa7e-105">기본적으로 데이터 보호 시스템 [경험적 접근을 사용](../configuration/default-settings.md#data-protection-default-settings) 암호화 키 자료 유지할지를 결정 하 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aa7e-105">By default the data protection system [employs a heuristic](../configuration/default-settings.md#data-protection-default-settings) to determine where cryptographic key material should be persisted.</span></span> <span data-ttu-id="4aa7e-106">개발자는 추론은 무시 하 고 수동으로 위치를 지정할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4aa7e-106">The developer can override the heuristic and manually specify the location.</span></span>

> [!NOTE]
> <span data-ttu-id="4aa7e-107">키 지 속성을 명시적 위치를 지정 하는 경우 데이터 보호 시스템은 추론은 제공 하는 rest 메커니즘에 기본 키 암호화 등록 해제 미사용에 더 이상 키를 암호화 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4aa7e-107">If you specify an explicit key persistence location, the data protection system will deregister the default key encryption at rest mechanism that the heuristic provided, so keys will no longer be encrypted at rest.</span></span> <span data-ttu-id="4aa7e-108">것을 또한 [명시적 키 암호화 메커니즘을 지정](key-encryption-at-rest.md#data-protection-implementation-key-encryption-at-rest-providers) 프로덕션 응용 프로그램에 대 한 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aa7e-108">It is recommended that you additionally [specify an explicit key encryption mechanism](key-encryption-at-rest.md#data-protection-implementation-key-encryption-at-rest-providers) for production applications.</span></span>

<span data-ttu-id="4aa7e-109">데이터 보호 시스템 여러 기본 키 저장소 공급자와 함께 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4aa7e-109">The data protection system ships with several in-box key storage providers.</span></span>

## <a name="file-system"></a><span data-ttu-id="4aa7e-110">파일 시스템</span><span class="sxs-lookup"><span data-stu-id="4aa7e-110">File system</span></span>

<span data-ttu-id="4aa7e-111">예상 많은 응용 프로그램을 파일 시스템을 기반으로 키 저장소에 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4aa7e-111">We anticipate that many apps will use a file system-based key repository.</span></span> <span data-ttu-id="4aa7e-112">이 구성 하려면 호출는 [PersistKeysToFileSystem](https://github.com/aspnet/DataProtection/blob/rel/1.1.0/src/Microsoft.AspNetCore.DataProtection/DataProtectionBuilderExtensions.cs) 아래와 같이 구성 루틴입니다.</span><span class="sxs-lookup"><span data-stu-id="4aa7e-112">To configure this, call the [PersistKeysToFileSystem](https://github.com/aspnet/DataProtection/blob/rel/1.1.0/src/Microsoft.AspNetCore.DataProtection/DataProtectionBuilderExtensions.cs) configuration routine as shown below.</span></span> <span data-ttu-id="4aa7e-113">제공 된 `DirectoryInfo` 키를 저장할 저장소를 가리키는 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aa7e-113">Provide a `DirectoryInfo` pointing to the repository where keys should be stored.</span></span>

```csharp
sc.AddDataProtection()
       // persist keys to a specific directory
       .PersistKeysToFileSystem(new DirectoryInfo(@"c:\temp-keys\"));
   ```

<span data-ttu-id="4aa7e-114">`DirectoryInfo` 로컬 컴퓨터에 디렉터리를 가리킬 수 있습니다 또는 네트워크 공유에 폴더를 가리킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4aa7e-114">The `DirectoryInfo` can point to a directory on the local machine, or it can point to a folder on a network share.</span></span> <span data-ttu-id="4aa7e-115">로컬 컴퓨터에 디렉터리를 가리키는 경우 (및 시나리오는 응용 프로그램에만 로컬 컴퓨터에서이 리포지토리를 사용 해야 합니다)를 사용 하는 것이 좋습니다 [Windows DPAPI](key-encryption-at-rest.md#data-protection-implementation-key-encryption-at-rest) 을 미사용 키를 암호화 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aa7e-115">If pointing to a directory on the local machine (and the scenario is that only applications on the local machine will need to use this repository), consider using [Windows DPAPI](key-encryption-at-rest.md#data-protection-implementation-key-encryption-at-rest) to encrypt the keys at rest.</span></span> <span data-ttu-id="4aa7e-116">그렇지 않으면 사용 하는 것이 좋습니다는 [X.509 인증서](key-encryption-at-rest.md#data-protection-implementation-key-encryption-at-rest) 저장 된 상태의 키를 암호화 하 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aa7e-116">Otherwise consider using an [X.509 certificate](key-encryption-at-rest.md#data-protection-implementation-key-encryption-at-rest) to encrypt keys at rest.</span></span>

## <a name="azure-and-redis"></a><span data-ttu-id="4aa7e-117">Azure 및 Redis</span><span class="sxs-lookup"><span data-stu-id="4aa7e-117">Azure and Redis</span></span>

<span data-ttu-id="4aa7e-118">`Microsoft.AspNetCore.DataProtection.AzureStorage` 및 `Microsoft.AspNetCore.DataProtection.Redis` 패키지 Redis 캐시 또는 Azure 저장소에 데이터 보호 키를 저장할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4aa7e-118">The `Microsoft.AspNetCore.DataProtection.AzureStorage` and `Microsoft.AspNetCore.DataProtection.Redis` packages allow storing your data protection keys in Azure Storage or a Redis cache.</span></span> <span data-ttu-id="4aa7e-119">웹 앱의 여러 인스턴스 키를 공유할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4aa7e-119">Keys can be shared across several instances of a web app.</span></span> <span data-ttu-id="4aa7e-120">ASP.NET Core 응용 프로그램 여러 서버에서 인증 쿠키 또는 CSRF 보호를 공유할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4aa7e-120">Your ASP.NET Core app can share authentication cookies or CSRF protection across multiple servers.</span></span> <span data-ttu-id="4aa7e-121">Azure를 구성 하려면 중 하나를 호출는 [PersistKeysToAzureBlobStorage](https://github.com/aspnet/DataProtection/blob/rel/1.1.0/src/Microsoft.AspNetCore.DataProtection.AzureStorage/AzureDataProtectionBuilderExtensions.cs) 아래와 같이 오버 로드 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aa7e-121">To configure on Azure, call one of the [PersistKeysToAzureBlobStorage](https://github.com/aspnet/DataProtection/blob/rel/1.1.0/src/Microsoft.AspNetCore.DataProtection.AzureStorage/AzureDataProtectionBuilderExtensions.cs) overloads as shown below.</span></span>

```
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blob URI including SAS token>"));

    services.AddMvc();
}
```

<span data-ttu-id="4aa7e-122">참고 항목에서 [Azure 테스트 코드](https://github.com/aspnet/DataProtection/blob/rel/1.1.0/samples/AzureBlob/Program.cs)합니다.</span><span class="sxs-lookup"><span data-stu-id="4aa7e-122">See also the [Azure test code](https://github.com/aspnet/DataProtection/blob/rel/1.1.0/samples/AzureBlob/Program.cs).</span></span>

<span data-ttu-id="4aa7e-123">Redis를 구성 하려면 중 하나를 호출는 [PersistKeysToRedis](https://github.com/aspnet/DataProtection/blob/rel/1.1.0/src/Microsoft.AspNetCore.DataProtection.Redis/RedisDataProtectionBuilderExtensions.cs) 아래와 같이 오버 로드 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aa7e-123">To configure on Redis, call one of the [PersistKeysToRedis](https://github.com/aspnet/DataProtection/blob/rel/1.1.0/src/Microsoft.AspNetCore.DataProtection.Redis/RedisDataProtectionBuilderExtensions.cs) overloads as shown below.</span></span>

```
public void ConfigureServices(IServiceCollection services)
{
    // Connect to Redis database.
    var redis = ConnectionMultiplexer.Connect("<URI>");
    services.AddDataProtection()
        .PersistKeysToRedis(redis, "DataProtection-Keys");

    services.AddMvc();
}
```

<span data-ttu-id="4aa7e-124">자세한 내용은 다음을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="4aa7e-124">See the following for more information:</span></span>

- [<span data-ttu-id="4aa7e-125">StackExchange.Redis ConnectionMultiplexer</span><span class="sxs-lookup"><span data-stu-id="4aa7e-125">StackExchange.Redis ConnectionMultiplexer</span></span>](https://github.com/StackExchange/StackExchange.Redis/blob/master/docs/Basics.md)
- [<span data-ttu-id="4aa7e-126">Azure Redis 캐시</span><span class="sxs-lookup"><span data-stu-id="4aa7e-126">Azure Redis Cache</span></span>](https://docs.microsoft.com/azure/redis-cache/cache-dotnet-how-to-use-azure-redis-cache#connect-to-the-cache)
- <span data-ttu-id="4aa7e-127">[테스트 코드 redis](https://github.com/aspnet/DataProtection/blob/rel/1.1.0/samples/Redis/Program.cs)합니다.</span><span class="sxs-lookup"><span data-stu-id="4aa7e-127">[Redis test code](https://github.com/aspnet/DataProtection/blob/rel/1.1.0/samples/Redis/Program.cs).</span></span>

## <a name="registry"></a><span data-ttu-id="4aa7e-128">레지스트리</span><span class="sxs-lookup"><span data-stu-id="4aa7e-128">Registry</span></span>

<span data-ttu-id="4aa7e-129">경우에 따라 응용 프로그램 파일 시스템에 대 한 쓰기 없을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4aa7e-129">Sometimes the app might not have write access to the file system.</span></span> <span data-ttu-id="4aa7e-130">응용 프로그램 (예: w3wp.exe의 응용 프로그램 풀 id)는 가상 서비스 계정으로 실행 되 고 있는 시나리오를 살펴보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="4aa7e-130">Consider a scenario where an app is running as a virtual service account (such as w3wp.exe's app pool identity).</span></span> <span data-ttu-id="4aa7e-131">이러한 경우 관리자가 될 수 있습니다 프로 비전 하 게 서비스 계정 id에 대 한 적절 한 포함 되었는지 있는 레지스트리 키.</span><span class="sxs-lookup"><span data-stu-id="4aa7e-131">In these cases, the administrator may have provisioned a registry key that is appropriate ACLed for the service account identity.</span></span> <span data-ttu-id="4aa7e-132">호출 된 [PersistKeysToRegistry](https://github.com/aspnet/DataProtection/blob/rel/1.1.0/src/Microsoft.AspNetCore.DataProtection/DataProtectionBuilderExtensions.cs) 아래와 같이 구성 루틴입니다.</span><span class="sxs-lookup"><span data-stu-id="4aa7e-132">Call the [PersistKeysToRegistry](https://github.com/aspnet/DataProtection/blob/rel/1.1.0/src/Microsoft.AspNetCore.DataProtection/DataProtectionBuilderExtensions.cs) configuration routine as shown below.</span></span> <span data-ttu-id="4aa7e-133">제공 된 `RegistryKey` 암호화 키/값을 저장할 위치를 가리킵니다.</span><span class="sxs-lookup"><span data-stu-id="4aa7e-133">Provide a `RegistryKey` pointing to the location where cryptographic keys/values should be stored.</span></span>

```csharp
   sc.AddDataProtection()
       // persist keys to a specific location in the system registry
       .PersistKeysToRegistry(Registry.CurrentUser.OpenSubKey(@"SOFTWARE\Sample\keys"));
   ```

<span data-ttu-id="4aa7e-134">시스템 레지스트리를 사용 하 여 지 속성 메커니즘으로 사용 하십시오 [Windows DPAPI](key-encryption-at-rest.md#data-protection-implementation-key-encryption-at-rest) 을 미사용 키를 암호화 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aa7e-134">If you use the system registry as a persistence mechanism, consider using [Windows DPAPI](key-encryption-at-rest.md#data-protection-implementation-key-encryption-at-rest) to encrypt the keys at rest.</span></span>

## <a name="custom-key-repository"></a><span data-ttu-id="4aa7e-135">사용자 지정 키 저장소</span><span class="sxs-lookup"><span data-stu-id="4aa7e-135">Custom key repository</span></span>

<span data-ttu-id="4aa7e-136">개발자는 사용자 지정을 제공 하 여 고유 키 지 속성 메커니즘을 지정할 수는 기본 메커니즘 적합 하지 않은 경우 `IXmlRepository`합니다.</span><span class="sxs-lookup"><span data-stu-id="4aa7e-136">If the in-box mechanisms are not appropriate, the developer can specify their own key persistence mechanism by providing a custom `IXmlRepository`.</span></span>
