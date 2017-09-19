---
title: "키 저장소 공급자"
author: rick-anderson
description: "키 저장소 공급자"
keywords: "암호화, ASP.NET Core"
ms.author: riande
manager: wpickett
ms.date: 01/14/2017
ms.topic: article
ms.assetid: 423e0a79-2f34-44c4-aaf3-146a53c39251
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/implementation/key-storage-providers
ms.openlocfilehash: 0d604c1aab1b7bad5b9f5c7d21edf12e8db7c563
ms.sourcegitcommit: 67f54fabbfa4e3942f5bfe1f8a7fdfe4a7a75358
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/19/2017
---
# <a name="key-storage-providers"></a>키 저장소 공급자

<a name=data-protection-implementation-key-storage-providers></a>

기본적으로 데이터 보호 시스템 [경험적 접근을 사용](../configuration/default-settings.md#data-protection-default-settings) 암호화 키 자료 유지할지를 결정 하 합니다. 개발자는 추론은 무시 하 고 수동으로 위치를 지정할 수도 있습니다.

> [!NOTE]
> 키 지 속성을 명시적 위치를 지정 하는 경우 데이터 보호 시스템은 추론은 제공 하는 rest 메커니즘에 기본 키 암호화 등록 해제 미사용에 더 이상 키를 암호화 됩니다. 것을 또한 [명시적 키 암호화 메커니즘을 지정](key-encryption-at-rest.md#data-protection-implementation-key-encryption-at-rest-providers) 프로덕션 응용 프로그램에 대 한 합니다.

데이터 보호 시스템 여러 기본 키 저장소 공급자와 함께 제공 됩니다.

## <a name="file-system"></a>파일 시스템

예상 많은 응용 프로그램을 파일 시스템을 기반으로 키 저장소에 사용 됩니다. 이 구성 하려면 호출는 [PersistKeysToFileSystem](https://github.com/aspnet/DataProtection/blob/rel/1.1.0/src/Microsoft.AspNetCore.DataProtection/DataProtectionBuilderExtensions.cs) 아래와 같이 구성 루틴입니다. 제공 된 `DirectoryInfo` 키를 저장할 저장소를 가리키는 합니다.

```csharp
sc.AddDataProtection()
       // persist keys to a specific directory
       .PersistKeysToFileSystem(new DirectoryInfo(@"c:\temp-keys\"));
   ```

`DirectoryInfo` 로컬 컴퓨터에 디렉터리를 가리킬 수 있습니다 또는 네트워크 공유에 폴더를 가리킬 수 있습니다. 로컬 컴퓨터에 디렉터리를 가리키는 경우 (및 시나리오는 응용 프로그램에만 로컬 컴퓨터에서이 리포지토리를 사용 해야 합니다)를 사용 하는 것이 좋습니다 [Windows DPAPI](key-encryption-at-rest.md#data-protection-implementation-key-encryption-at-rest) 을 미사용 키를 암호화 합니다. 그렇지 않으면 사용 하는 것이 좋습니다는 [X.509 인증서](key-encryption-at-rest.md#data-protection-implementation-key-encryption-at-rest) 저장 된 상태의 키를 암호화 하 합니다.

## <a name="azure-and-redis"></a>Azure 및 Redis

`Microsoft.AspNetCore.DataProtection.AzureStorage` 및 `Microsoft.AspNetCore.DataProtection.Redis` 패키지 Redis 캐시 또는 Azure 저장소에 데이터 보호 키를 저장할 수 있습니다. 웹 앱의 여러 인스턴스 키를 공유할 수 있습니다. ASP.NET Core 응용 프로그램 여러 서버에서 인증 쿠키 또는 CSRF 보호를 공유할 수 있습니다. Azure를 구성 하려면 중 하나를 호출는 [PersistKeysToAzureBlobStorage](https://github.com/aspnet/DataProtection/blob/rel/1.1.0/src/Microsoft.AspNetCore.DataProtection.AzureStorage/AzureDataProtectionBuilderExtensions.cs) 아래와 같이 오버 로드 합니다.

```
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blob URI including SAS token>"));

    services.AddMvc();
}
```

참고 항목에서 [Azure 테스트 코드](https://github.com/aspnet/DataProtection/blob/rel/1.1.0/samples/AzureBlob/Program.cs)합니다.

Redis를 구성 하려면 중 하나를 호출는 [PersistKeysToRedis](https://github.com/aspnet/DataProtection/blob/rel/1.1.0/src/Microsoft.AspNetCore.DataProtection.Redis/RedisDataProtectionBuilderExtensions.cs) 아래와 같이 오버 로드 합니다.

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

자세한 내용은 다음을 참조하세요.

- [StackExchange.Redis ConnectionMultiplexer](https://github.com/StackExchange/StackExchange.Redis/blob/master/docs/Basics.md)
- [Azure Redis 캐시](https://docs.microsoft.com/azure/redis-cache/cache-dotnet-how-to-use-azure-redis-cache#connect-to-the-cache)
- [테스트 코드 redis](https://github.com/aspnet/DataProtection/blob/rel/1.1.0/samples/Redis/Program.cs)합니다.

## <a name="registry"></a>레지스트리

경우에 따라 응용 프로그램 파일 시스템에 대 한 쓰기 없을 수 있습니다. 응용 프로그램 (예: w3wp.exe의 응용 프로그램 풀 id)는 가상 서비스 계정으로 실행 되 고 있는 시나리오를 살펴보겠습니다. 이러한 경우 관리자가 될 수 있습니다 프로 비전 하 게 서비스 계정 id에 대 한 적절 한 포함 되었는지 있는 레지스트리 키. 호출 된 [PersistKeysToRegistry](https://github.com/aspnet/DataProtection/blob/rel/1.1.0/src/Microsoft.AspNetCore.DataProtection/DataProtectionBuilderExtensions.cs) 아래와 같이 구성 루틴입니다. 제공 된 `RegistryKey` 암호화 키/값을 저장할 위치를 가리킵니다.

```csharp
   sc.AddDataProtection()
       // persist keys to a specific location in the system registry
       .PersistKeysToRegistry(Registry.CurrentUser.OpenSubKey(@"SOFTWARE\Sample\keys"));
   ```

시스템 레지스트리를 사용 하 여 지 속성 메커니즘으로 사용 하십시오 [Windows DPAPI](key-encryption-at-rest.md#data-protection-implementation-key-encryption-at-rest) 을 미사용 키를 암호화 합니다.

## <a name="custom-key-repository"></a>사용자 지정 키 저장소

개발자는 사용자 지정을 제공 하 여 고유 키 지 속성 메커니즘을 지정할 수는 기본 메커니즘 적합 하지 않은 경우 `IXmlRepository`합니다.
