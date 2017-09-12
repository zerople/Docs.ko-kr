---
title: "분산된 캐시 사용"
author: ardalis
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 02/14/2017
ms.topic: article
ms.assetid: 870f082d-6d43-453d-b311-45f3aeb4d2c5
ms.technology: aspnet
ms.prod: asp.net-core
uid: performance/caching/distributed
ms.openlocfilehash: abf680fef9de175082c1e4f4cebc2b9648f18a28
ms.sourcegitcommit: 9cdbfd0d670d70b9c354216aabee260c52dad5ee
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/12/2017
---
# <a name="working-with-a-distributed-cache"></a>분산된 캐시 사용

으로 [Steve Smith](https://ardalis.com/)

분산 된 캐시 클라우드 또는 서버 팜 환경에서 호스팅되는 경우에 특히 성능 및 ASP.NET Core 응용 프로그램의 확장성을 개선할 수 있습니다. 이 문서에서는 ASP.NET Core 기본 제공 분산된 캐시 추상화 및 구현 작업 하는 방법을 설명 합니다.

[샘플 코드 보기 또는 다운로드](https://github.com/aspnet/Docs/tree/master/aspnetcore/performance/caching/distributed/sample)

## <a name="what-is-a-distributed-cache"></a>분산 캐시 이란

분산된 캐시 여러 응용 프로그램 서버에서 공유 됩니다 (참조 [기본 사항 캐싱](memory.md#caching-basics)). 캐시에 정보가 개별 웹 서버의 메모리에 저장 되지 않으며 캐시 된 데이터는 모든 응용 프로그램의 서버에 사용할 수 있습니다. 이 여러 가지 이점을 제공합니다.

1. 캐시 된 데이터는 모든 웹 서버에서 일관 됩니다. 사용자가 서버를 웹에 따라 해당 요청을 처리 하는 다른 결과가 표시 되지 않으면

2. 캐시 된 데이터 웹 서버 다시 시작 하 고 배포 후에 유지 합니다. 개별 웹 서버를 제거 하거나 캐시에 영향을 주지 않고 추가할 수 있습니다.

3. 원본 데이터 저장소에 내용이 (아니요 또는 다 수의 메모리 내 캐시와 전혀를 캐시) 보다 더 적은 요청 합니다.

> [!NOTE]
> SQL Server 분산 캐시를 사용 하는 경우 이러한 장점 중 일부는 응용 프로그램의 원본 데이터에 대 한 보다 캐시에 대 한 별도 데이터베이스 인스턴스를 사용 하는 경우 true만 합니다.

모든 캐시 같은 분산된 캐시 획기적으로 향상는 응용 프로그램의 응답성 일반적으로 관계형 데이터베이스 (또는 웹 서비스)에서 보다 훨씬 빠르게 캐시에서 데이터를 검색할 수 있습니다.

캐시 구성은 구현에 따라 다릅니다. 이 문서에서는 어떻게 Redis 둘 다 구성 하 고 SQL Server 분산 캐시를 설명 합니다. 어떤 구현을 선택 하는 것에 관계 없이 응용 프로그램와 상호 작용 하는 공통을 사용 하 여 캐시 `IDistributedCache` 인터페이스입니다.

## <a name="the-idistributedcache-interface"></a>IDistributedCache 인터페이스

`IDistributedCache` 인터페이스는 동기 및 비동기 메서드를 포함 합니다. 인터페이스에는 항목을을 추가, 검색 및 분산된 캐시 구현에서 제거할 수 있습니다. `IDistributedCache` 인터페이스는 다음 메서드를 포함 합니다.

**Get, GetAsync**

문자열 키를 사용 하 고 캐시 된 항목으로 검색 한 `byte[]` 경우 캐시에서 발견 합니다.

**SetAsync 집합**

항목 추가 (으로 `byte[]`) 문자열 키를 사용 하 여 캐시에 있습니다.

**새로 고침, RefreshAsync**

해당 키를 다시 설정 하는 상대 (sliding) 만료 시간 제한 (있는 경우)에 따라 캐시에서 항목을 새로 고칩니다.

**를 제거합니다 하 고 비동기적으로 제거**

해당 키에 따라 캐시 엔트리를 제거 합니다.

사용 하 여 `IDistributedCache` 인터페이스:

   1. 프로젝트 파일에 필요한 NuGet 패키지를 추가 합니다.

   2. 구성의 특정 구현 `IDistributedCache` 에 프로그램 `Startup` 클래스의 `ConfigureServices` 메서드를 하 고 있는 컨테이너에 추가 합니다.

   3. 응용 프로그램의 [`Middleware](../../fundamentals/middleware.md) or MVC controller classes, request an instance of `IDistributedCache' 생성자에서 합니다. 인스턴스에 의해 제공 됩니다 [종속성 주입](../../fundamentals/dependency-injection.md) (DI).

> [!NOTE]
> 사용에 대 한 단일 항목 또는 Scoped 수명이 필요가 없는 `IDistributedCache` 인스턴스 (최소한 기본 구현에 대 한). 하나 필요할 때마다 인스턴스를 만들 수도 있습니다 (사용 하는 대신 [종속성 주입](../../fundamentals/dependency-injection.md)),이 어려워지며 코드를 테스트 하려면 하지만 위반는 [명시적 종속성 원칙](http://deviq.com/explicit-dependencies-principle/)합니다.

인스턴스를 사용 하는 방법을 보여 주는 다음 예제 `IDistributedCache` 간단한 미들웨어 구성 요소에서:

[!code-csharp[Main](./distributed/sample/src/DistCacheSample/StartTimeHeader.cs?highlight=15,18,21,27,28,29,30,31)]

위의 코드에서 캐시 된 값이 읽고 이지만 작성 되지 않습니다. 이 샘플에서는 값은 경우에 설정 하는 서버, 시작 되 고 변경 되지 않습니다. 다중 서버 시나리오에서 가장 최근 시작 하도록 서버에 다른 서버에서 설정 된 모든 이전 값을 덮어쓰게 됩니다. `Get` 및 `Set` 메서드 사용은 `byte[]` 형식입니다. 따라서 문자열 값을 변환 해야 `Encoding.UTF8.GetString` (에 대 한 `Get`) 및 `Encoding.UTF8.GetBytes` (에 대 한 `Set`).

다음 코드에서 *Startup.cs* 설정 되는 값을 보여 줍니다.

[!code-csharp[Main](./distributed/sample/src/DistCacheSample/Startup.cs?highlight=2,4,5,6&range=58-66)]

> [!NOTE]
> 이후 `IDistributedCache` 에 구성 된는 `ConfigureServices` 은 사용할 수 있는 메서드를는 `Configure` 메서드에 매개 변수로 합니다. 매개 변수로 추가 하면 DI를 통해 제공 되는 구성 된 인스턴스.

## <a name="using-a-redis-distributed-cache"></a>Redis를 사용 하 여 분산 캐시

[Redis](https://redis.io/) 는 분산된 캐시로 흔히 사용 되는 오픈 소스 메모리 내 데이터 저장소입니다. 로컬에서 사용할 수 있습니다 및 구성할 수 있습니다는 [Azure Redis Cache](https://azure.microsoft.com/services/cache/) ASP.NET Core Azure 호스팅 응용 프로그램에 대 한 합니다. ASP.NET Core 응용 프로그램을 사용 하 여 캐시 구현 구성는 `RedisDistributedCache` 인스턴스.

Redis 구현을 구성 `ConfigureServices` 의 인스턴스를 요청 하 여 앱 코드에서 액세스 하 고 `IDistributedCache` (위의 코드 참조).

샘플 코드에서는 `RedisCache` 구현이 위해 서버를 구성할 때 사용 됩니다는 `Staging` 환경입니다. 따라서는 `ConfigureStagingServices` 메서드 구성는 `RedisCache`:

[!code-csharp[Main](./distributed/sample/src/DistCacheSample/Startup.cs?highlight=8,9,10,11,12,13&range=27-40)]

> [!NOTE]
> Redis를 로컬 컴퓨터에 설치 하려면 chocolatey 패키지 설치 [https://chocolatey.org/packages/redis-64/](https://chocolatey.org/packages/redis-64/) 실행 `redis-server` 명령 프롬프트에서 합니다.

## <a name="using-a-sql-server-distributed-cache"></a>SQL Server를 사용 하 여 분산 캐시

SqlServerCache 구현 하면 해당 백업 저장소로 SQL Server 데이터베이스를 사용 하 여 분산된 캐시를 사용할 수 있습니다. SQL 서버를 만들려면 테이블 sql 캐시 도구인 도구를 사용 하 여 지정한 이름 및 스키마와 테이블을 만듭니다.

Sql 캐시 도구를 사용 하려면 추가 `SqlConfig.Tools` 에 `<ItemGroup>` 의 요소는 *.csproj* 파일 고 dotnet 복원을 실행 합니다.

[!code-csharp[Main](./distributed/sample/src/DistCacheSample/DistCacheSample.csproj?range=23-25)]

다음 명령을 실행 하 여 SqlConfig.Tools 테스트

```none
C:\DistCacheSample\src\DistCacheSample>dotnet sql-cache create --help
   ```

이제 "sql 캐시 만들기" 명령을 실행 중인 sql server로 테이블을 만들 수 있습니다 sql 캐시 도구 사용, 옵션 및 명령 도움말을 표시 합니다.

```none
C:\DistCacheSample\src\DistCacheSample>dotnet sql-cache create "Data Source=(localdb)\v11.0;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
   info: Microsoft.Extensions.Caching.SqlConfig.Tools.Program[0]
       Table and index were created successfully.
   ```

만든된 테이블에는 다음 스키마에 있습니다.

![SqlServer 캐시 테이블](distributed/_static/SqlServerCacheTable.png)

모든 캐시 구현와 같은 응용 프로그램 해야 get 및 set의 인스턴스를 사용 하 여 캐시 값 `IDistributedCache`이 아니라는 `SqlServerCache`합니다. 이 샘플 구현 `SqlServerCache` 에 `Production` 환경 (에 구성 되어 있으므로 `ConfigureProductionServices`).

[!code-csharp[Main](./distributed/sample/src/DistCacheSample/Startup.cs?highlight=7,8,9,10,11,12&range=42-56)]

> [!NOTE]
> `ConnectionString` (및 필요에 따라 `SchemaName` 및 `TableName`) 일반적으로 저장 되어야 합니다 (예: UserSecrets) 소스 제어를 벗어나야 자격 증명을 포함 될 수 있습니다.

## <a name="recommendations"></a>권장 사항

어떤 구현을 결정할 때 `IDistributedCache` Redis 중에서 선택할 응용 프로그램에 대 한 오른쪽 및 기존 인프라 및 환경, 성능 요구 사항 및 팀의 환경에 따라 SQL Server가 있습니다. 팀이 더 친숙 Redis와 함께 작업을 경우 매우 적합 합니다. SQL Server 팀이 선호 하는 경우 구현에도 안정적 수 있습니다. 기존의 캐싱 솔루션에는 데이터의 빠른 검색을 허용 하는 메모리 내 데이터 저장을 참고 합니다. 캐시에서 자주 사용 되는 데이터를 저장 하 고 SQL Server 또는 Azure 저장소와 같은 백 엔드 영구 저장소에 전체 데이터를 저장 해야 합니다. Redis 캐시는 SQL 캐시를 비교 하 여 높은 처리량과 낮은 대기 시간 제공 하는 캐싱 솔루션입니다.

추가 리소스:

* [에 메모리 캐싱은](memory.md)
* [Redis Cache Azure에서](https://azure.microsoft.com/documentation/services/redis-cache/)
* [Azure에서 SQL 데이터베이스](https://azure.microsoft.com/documentation/services/sql-database/)
