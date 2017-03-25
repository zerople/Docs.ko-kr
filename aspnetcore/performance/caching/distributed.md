---
title: "분산된 캐시 사용 | Microsoft 문서"
author: ardalis
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 02/14/2017
ms.topic: article
ms.assetid: 870f082d-6d43-453d-b311-45f3aeb4d2c5
ms.technology: aspnet
ms.prod: asp.net-core
uid: performance/caching/distributed
translationtype: Machine Translation
ms.sourcegitcommit: 0ebb9b63931ccb26126740de08270eda9b1ea486
ms.openlocfilehash: df4b5d79b270dcc7338e0c240ea259890b4d7594
ms.lasthandoff: 03/23/2017

---
# <a name="working-with-a-distributed-cache"></a>분산된 캐시 작업

[Steve Smith](http://ardalis.com)

클라우드 또는 서버 팜 환경에서 호스팅되는 경우에 특히 분산 된 캐시는 성능과 ASP.NET 핵심 응용 프로그램의 확장성을 개선할 수 있습니다. 이 문서에서는 ASP.NET 핵심 기본 제공 분산된 캐시 추상화 및 구현 작업 하는 방법을 설명 합니다.

[샘플 코드 보기 또는 다운로드](https://github.com/aspnet/Docs/tree/master/aspnetcore/performance/caching/distributed/sample)

## <a name="what-is-a-distributed-cache"></a>분산 캐시는 무엇입니까

분산된 캐시는 여러 응용 프로그램 서버에서 공유 됩니다 (참조 [캐싱 기본 사항](memory.md#caching-basics)). 캐시의 정보는 개별 웹 서버의 메모리에 저장 되지 및 캐시 된 데이터는 모든 응용 프로그램의 서버에 사용할 수 있습니다. 이 여러 가지 이점을 제공 합니다.

1. 캐시 된 데이터는 일관 된 모든 웹 서버에 있습니다. 사용자가 서버를 웹에 따라 해당 요청을 처리 하는 다른 결과가 표시 되지 않는

2. 캐시 된 데이터에는 웹 서버를 다시 시작 하 고 배포 하더라도 남아 있습니다. 개별 웹 서버를 제거 하거나 캐시에 영향을 주지 않고 추가할 수 있습니다.

3. 원본 데이터 저장소에 더 적은 요청 (보다 다 수의 메모리 내 캐시 또는 no 전혀를 캐시)에 있습니다.

> [!NOTE]
> SQL Server 분산 캐시를 사용 하는 경우 이러한 이점 중 일부 조건에 응용 프로그램의 원본 데이터에 대 한 보다 캐시에 대 한 별도 데이터베이스 인스턴스를 사용 하는 경우.

모든 캐시와 같은 분산된 캐시 크게 향상 시킬 수 응용 프로그램의 응답성 하므로 일반적으로 관계형 데이터베이스 (또는 웹 서비스)에서 보다 훨씬 빠르게 캐시에서 데이터를 검색할 수 있습니다.

캐시 구성은 구현에 따라 다릅니다. 이 문서에서는 Redis 둘 다 구성 하 고 SQL Server 분산 캐시 방법을 설명 합니다. 어떤 구현의 선택 하는 것에 관계 없이 응용 프로그램 상호 작용 하는 공통을 사용 하 여 캐시와 `IDistributedCache` 인터페이스입니다.

## <a name="the-idistributedcache-interface"></a>IDistributedCache 인터페이스

`IDistributedCache` 인터페이스는 동기 및 비동기 메서드를 포함 합니다. 인터페이스에서 항목을을 추가, 검색 및 분산된 캐시 구현에서 제거할 수 있습니다. `IDistributedCache` 인터페이스에 다음 메서드를 포함 합니다.

**Get, GetAsync**

문자열 키를 사용 하 고 캐시 된 항목으로 검색 한 `byte[]` 경우 캐시에서 발견 합니다.

**SetAsync 집합**

항목 추가 (으로 `byte[]`) 문자열 키를 사용 하 여 캐시에 있습니다.

**새로 고침, RefreshAsync**

해당 키를 다시 설정 하는 슬라이딩 만료 시간 제한 (있는 경우)에 따라 캐시에서 항목을 새로 고칩니다.

**RemoveAsync 제거**

해당 키에 따라 캐시 엔트리를 제거 합니다.

사용 하 여 `IDistributedCache` 인터페이스:

   1. 프로젝트 파일에 필요한 NuGet 패키지를 추가 합니다.

   2. 구성의 특정 구현 `IDistributedCache` 에 프로그램 `Startup` 클래스의 `ConfigureServices` 메서드에 있는 컨테이너에 추가 합니다.

   3. 응용 프로그램에서 [`Middleware](../../fundamentals/middleware.md) or MVC controller classes, request an instance of `IDistributedCache' 생성자에서. 인스턴스에 의해 제공 됩니다 [종속성 주입](../../fundamentals/dependency-injection.md) (DI).

> [!NOTE]
> 단일 항목 또는 범위 이름 수명은 사이 사용 하 여 없어도 `IDistributedCache` 인스턴스 (최소한 기본 구현에 대 한). 하나 필요할 때마다 인스턴스를 만들 수도 있습니다 (사용 하는 대신 [종속성 주입](../../fundamentals/dependency-injection.md)),이 어려워질 수 코드를 테스트 하지만 위반는 [명시적 종속성 원칙](http://deviq.com/explicit-dependencies-principle/)합니다.

다음 예제에서는의 인스턴스를 사용 하는 방법을 보여 줍니다. `IDistributedCache` 간단한 미들웨어 구성 요소에서:

[!code-csharp[주](./distributed/sample/src/DistCacheSample/StartTimeHeader.cs?highlight=15,18,21,27,28,29,30,31)]

위 코드에서 캐시 된 값은 읽을 있지만 작성 되지 않습니다. 이 샘플에서는 값은 때만 설정 서버, 시작 되 고 변경 되지 않습니다. 시작 하기 위해 최신 서버는 다중 서버 시나리오에서는 다른 서버에서 설정 된 이전 값을 덮어쓰게 됩니다. `Get` 및 `Set` 메서드를 사용 하는 `byte[]` 유형입니다. 따라서 문자열 값을 변환 해야 `Encoding.UTF8.GetString` (에 대 한 `Get`) 및 `Encoding.UTF8.GetBytes` (에 대 한 `Set`).

다음 코드에서 *Startup.cs* 설정 되는 값을 보여 줍니다.

[!code-csharp[주](./distributed/sample/src/DistCacheSample/Startup.cs?highlight=2,4,5,6&range=58-66)]

> [!NOTE]
> 이후 `IDistributedCache` 에 구성 되어는 `ConfigureServices` 은 사용할 수 있는 메서드를는 `Configure` 메서드에 매개 변수로 합니다. 매개 변수로 추가 하면 구성 된 인스턴스를 DI를 통해 제공할 수 있습니다.

## <a name="using-a-redis-distributed-cache"></a>분산 캐시는 Redis를 사용 하 여

[Redis](http://redis.io) 는 분산된 캐시로 흔히 사용 되는 오픈 소스 메모리 내 데이터 저장소입니다. 로컬에서 사용할 수 있습니다 하 고 구성할 수는 [Azure Redis Cache](https://azure.microsoft.com/en-us/services/cache/) ASP.NET 핵심 Azure 호스 티 드 앱에 대 한 합니다. ASP.NET 핵심 응용 프로그램을 사용 하 여 캐시 구현 구성는 `RedisDistributedCache` 인스턴스.

Redis 구현은에서 구성한 `ConfigureServices` 의 인스턴스를 요청 하 여 응용 프로그램 코드에 액세스 하 고 `IDistributedCache` (위 코드 참조).

샘플 코드에서는 `RedisCache` 구현이 위해 서버를 구성할 때 사용 됩니다는 `Staging` 환경입니다. 따라서는 `ConfigureStagingServices` 메서드 구성의 `RedisCache`:

[!code-csharp[주](./distributed/sample/src/DistCacheSample/Startup.cs?highlight=8,9,10,11,12,13&range=27-40)]

> [!NOTE]
> 로컬 컴퓨터에 Redis를 설치 하려면 chocolatey 패키지를 설치 [http://chocolatey.org/packages/redis-64/](http://chocolatey.org/packages/redis-64/) 실행 `redis-server` 명령 프롬프트에서.

## <a name="using-a-sql-server-distributed-cache"></a>SQL Server를 사용 하 여 분산 캐시

SqlServerCache 구현 하면 SQL Server 데이터베이스를 백업 저장소로 사용 하 여 분산된 캐시를 사용할 수 있습니다. SQL 서버를 만들려면 테이블 도구인 sql 캐시 도구를 사용 하 여 지정한 이름 및 스키마와 테이블을 만듭니다.

Sql 캐시 도구를 사용 하려면 추가 `SqlConfig.Tools` 에 `<ItemGroup>` 의 요소는 *.csproj* 파일을 실행 하는 dotnet 복원 합니다.

[!code-csharp[주](./distributed/sample/src/DistCacheSample/DistCacheSample.csproj?range=23-25)]

다음 명령을 실행 하 여 SqlConfig.Tools 테스트

```none
C:\DistCacheSample\src\DistCacheSample>dotnet sql-cache create --help
   ```

이제 sql 캐시 생성"명령을 실행 중인 sql server에 테이블을 만들 수 있습니다 sql 캐시 도구 사용, 옵션 및 명령 도움말을 표시 합니다.

```none
C:\DistCacheSample\src\DistCacheSample>dotnet sql-cache create "Data Source=(localdb)\v11.0;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
   info: Microsoft.Extensions.Caching.SqlConfig.Tools.Program[0]
       Table and index were created successfully.
   ```

생성된 된 테이블에는 다음 스키마를 있습니다.

![Sql Server 캐시 테이블](distributed/_static/SqlServerCacheTable.png)

모든 캐시 구현 처럼 앱 해야 get 및 set 캐시 값의 인스턴스를 사용 하 여 `IDistributedCache`이 아니라는 `SqlServerCache`합니다. 이 샘플 구현 `SqlServerCache` 에 `Production` 환경 (에 구성 되어 있으므로 `ConfigureProductionServices`).

[!code-csharp[주](./distributed/sample/src/DistCacheSample/Startup.cs?highlight=7,8,9,10,11,12&range=42-56)]

> [!NOTE]
> `ConnectionString` (및 필요에 따라 `SchemaName` 및 `TableName`)는 일반적으로 저장할 수 (예: UserSecrets), 소스 제어 외부 자격 증명을 포함할 수도 있습니다.

## <a name="recommendations"></a>권장 사항

어떤 구현을 결정할 때 `IDistributedCache` 기존 인프라 및 환경, 성능 요구 사항 및 팀의 경험을 기준으로 SQL Server 및 Redis 중에서 선택 앱에 대 한 바로 있습니다. 팀이 더 친숙 한 Redis를 사용한 작업을 적합 합니다. SQL Server 팀이 선호 하는 경우에 해당 구현에서 확신할을 수 있습니다. 참고 하면 전통적인 캐싱 솔루션을 데이터의 빠른 검색을 위한 수는 메모리 내 데이터를 저장 합니다. 캐시에 자주 사용 되는 데이터를 저장 하 고 SQL Server 또는 Azure 저장소와 같은 백 엔드 영구 저장소에 전체 데이터를 저장 해야 합니다. Redis 캐시는 SQL 캐시를 비교 하 여 높은 처리량과 짧은 대기 시간 제공 하는 캐싱 솔루션입니다.

추가 리소스:

* [메모리 내 캐시에서](memory.md)
* [Redis azure Cache](https://azure.microsoft.com/en-us/documentation/services/redis-cache/)
* [Azure에서 SQL 데이터베이스](https://azure.microsoft.com/en-us/documentation/services/sql-database/)

