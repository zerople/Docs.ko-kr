---
title: "구성 마이그레이션"
author: ardalis
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 8468d859-ff32-4a92-9e62-08c4a9e36594
ms.technology: aspnet
ms.prod: asp.net-core
uid: migration/configuration
ms.openlocfilehash: 4cf2227db22fbfd7f0c6239dad0d0a470c35d28c
ms.sourcegitcommit: 732cd2684246e49e796836596643a8d37e20c46d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2017
---
# <a name="migrating-configuration"></a><span data-ttu-id="47014-103">구성 마이그레이션</span><span class="sxs-lookup"><span data-stu-id="47014-103">Migrating Configuration</span></span>

<span data-ttu-id="47014-104">여 [Steve Smith](https://ardalis.com/) 및 [Scott Addie](https://scottaddie.com)</span><span class="sxs-lookup"><span data-stu-id="47014-104">By [Steve Smith](https://ardalis.com/) and [Scott Addie](https://scottaddie.com)</span></span>

<span data-ttu-id="47014-105">이전 문서에서는 하기 시작 [ASP.NET Core mvc ASP.NET MVC 프로젝트 마이그레이션](mvc.md)합니다.</span><span class="sxs-lookup"><span data-stu-id="47014-105">In the previous article, we began [migrating an ASP.NET MVC project to ASP.NET Core MVC](mvc.md).</span></span> <span data-ttu-id="47014-106">이 문서에서는 구성을 마이그레이션 했습니다.</span><span class="sxs-lookup"><span data-stu-id="47014-106">In this article, we migrate configuration.</span></span>

<span data-ttu-id="47014-107">[보거나 다운로드 샘플 코드](https://github.com/aspnet/Docs/tree/master/aspnetcore/migration/configuration/samples) ([다운로드 하는 방법을](xref:tutorials/index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="47014-107">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/migration/configuration/samples) ([how to download](xref:tutorials/index#how-to-download-a-sample))</span></span>

## <a name="setup-configuration"></a><span data-ttu-id="47014-108">설치 구성</span><span class="sxs-lookup"><span data-stu-id="47014-108">Setup Configuration</span></span>

<span data-ttu-id="47014-109">ASP.NET Core 더 이상 사용 하지는 *Global.asax* 및 *web.config* 이전 버전의 ASP.NET 사용 하는 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="47014-109">ASP.NET Core no longer uses the *Global.asax* and *web.config* files that previous versions of ASP.NET utilized.</span></span> <span data-ttu-id="47014-110">이전 버전의 ASP.NET 응용 프로그램 시작 논리에 배치 된는 `Application_StartUp` 메서드 내에서 *Global.asax*합니다.</span><span class="sxs-lookup"><span data-stu-id="47014-110">In earlier versions of ASP.NET, application startup logic was placed in an `Application_StartUp` method within *Global.asax*.</span></span> <span data-ttu-id="47014-111">ASP.NET MVC의 뒷부분에 나오는 *Startup.cs* 고 파일; 프로젝트의 루트에 포함 된 응용 프로그램이 시작 될 때 호출 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="47014-111">Later, in ASP.NET MVC, a *Startup.cs* file was included in the root of the project; and, it was called when the application started.</span></span> <span data-ttu-id="47014-112">ASP.NET Core가이 접근 방법을 채택 완전히 모든 시작 논리에 배치 하 여는 *Startup.cs* 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="47014-112">ASP.NET Core has adopted this approach completely by placing all startup logic in the *Startup.cs* file.</span></span>

<span data-ttu-id="47014-113">*web.config* 파일 에서도 ASP.NET Core 대체 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="47014-113">The *web.config* file has also been replaced in ASP.NET Core.</span></span> <span data-ttu-id="47014-114">자체 구성 이제을 구성할 수 있으며 응용 프로그램 시작에서 설명한 절차의 일부로 *Startup.cs*합니다.</span><span class="sxs-lookup"><span data-stu-id="47014-114">Configuration itself can now be configured, as part of the application startup procedure described in *Startup.cs*.</span></span> <span data-ttu-id="47014-115">구성 XML 파일을 사용할 수 있지만 일반적으로 ASP.NET Core 프로젝트에에서 배치 됩니다 구성 값을 JSON 형식 파일을 같은 *appsettings.json*합니다.</span><span class="sxs-lookup"><span data-stu-id="47014-115">Configuration can still utilize XML files, but typically ASP.NET Core projects will place configuration values in a JSON-formatted file, such as *appsettings.json*.</span></span> <span data-ttu-id="47014-116">ASP.NET Core 구성 시스템 환경 관련 값에 대 한 안전 하 고 강력한 위치를 제공할 수 있는 환경 변수를도 쉽게 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="47014-116">ASP.NET Core's configuration system can also easily access environment variables, which can provide a more secure and robust location for environment-specific values.</span></span> <span data-ttu-id="47014-117">연결 문자열 및 소스 제어에 체크 인할 수 해야 하는 API 키와 같은 기밀 정보에 대 한 경우 특히 유용 합니다.</span><span class="sxs-lookup"><span data-stu-id="47014-117">This is especially true for secrets like connection strings and API keys that should not be checked into source control.</span></span> <span data-ttu-id="47014-118">참조 [구성](../fundamentals/configuration.md) ASP.NET Core에서 구성에 대 한 자세한 내용을 보려면 합니다.</span><span class="sxs-lookup"><span data-stu-id="47014-118">See [Configuration](../fundamentals/configuration.md) to learn more about configuration in ASP.NET Core.</span></span>

<span data-ttu-id="47014-119">ASP.NET Core 일부만 마이그레이션된 프로젝트와 시작 되며이 문서에 대 한 [이전 문서](mvc.md)합니다.</span><span class="sxs-lookup"><span data-stu-id="47014-119">For this article, we are starting with the partially-migrated ASP.NET Core project from [the previous article](mvc.md).</span></span> <span data-ttu-id="47014-120">구성 설정, 다음 생성자 및 속성을 추가 하는 *Startup.cs* 프로젝트의 루트에 있는 파일:</span><span class="sxs-lookup"><span data-stu-id="47014-120">To setup configuration, add the following constructor and property to the *Startup.cs* file located in the root of the project:</span></span>

[!code-csharp[Main](configuration/samples/WebApp1/src/WebApp1/Startup.cs?range=11-21)]

<span data-ttu-id="47014-121">이 시점에서 즉 참고는 *Startup.cs* 파일 컴파일되지 않고 다음 코드를 추가 해야 하는 대로 `using` 문:</span><span class="sxs-lookup"><span data-stu-id="47014-121">Note that at this point, the *Startup.cs* file will not compile, as we still need to add the following `using` statement:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="47014-122">추가 *appsettings.json* 적절 한 항목 템플릿을 사용 하 여 프로젝트의 루트에 파일:</span><span class="sxs-lookup"><span data-stu-id="47014-122">Add an *appsettings.json* file to the root of the project using the appropriate item template:</span></span>

![AppSettings JSON 추가](configuration/_static/add-appsettings-json.png)

## <a name="migrate-configuration-settings-from-webconfig"></a><span data-ttu-id="47014-124">Web.config에서 구성 설정을 마이그레이션하십시오</span><span class="sxs-lookup"><span data-stu-id="47014-124">Migrate Configuration Settings from web.config</span></span>

<span data-ttu-id="47014-125">ASP.NET MVC 프로젝트 진행에 필요한 데이터베이스 연결 문자열을 포함 *web.config*에 `<connectionStrings>` 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="47014-125">Our ASP.NET MVC project included the required database connection string in *web.config*, in the `<connectionStrings>` element.</span></span> <span data-ttu-id="47014-126">ASP.NET Core 프로젝트 진행에 하겠습니다에이 정보를 저장 하는 *appsettings.json* 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="47014-126">In our ASP.NET Core project, we are going to store this information in the *appsettings.json* file.</span></span> <span data-ttu-id="47014-127">열기 *appsettings.json*, 다음을 이미 포함 하는 것을 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="47014-127">Open *appsettings.json*, and note that it already includes the following:</span></span>

[!code-json[Main](../migration/configuration/samples/WebApp1/src/WebApp1/appsettings.json?highlight=4)]


<span data-ttu-id="47014-128">위에 표시 하는 강조 표시 된 줄에서 데이터베이스의 이름을 변경 **_CHANGE_ME** 데이터베이스의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="47014-128">In the highlighted line depicted above, change the name of the database from **_CHANGE_ME** to the name of your database.</span></span>

## <a name="summary"></a><span data-ttu-id="47014-129">요약</span><span class="sxs-lookup"><span data-stu-id="47014-129">Summary</span></span>

<span data-ttu-id="47014-130">ASP.NET Core 응용 프로그램에 대 한 모든 시작 논리는 필요한 서비스 및 종속성 수 정의 되어 있으며 구성 된 단일 파일을 배치 합니다.</span><span class="sxs-lookup"><span data-stu-id="47014-130">ASP.NET Core places all startup logic for the application in a single file, in which the necessary services and dependencies can be defined and configured.</span></span> <span data-ttu-id="47014-131">대체는 *web.config* 다양 한 환경 변수 뿐만 아니라 JSON와 같은 파일 형식의 활용할 수 있는 유연한 구성 기능으로는 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="47014-131">It replaces the *web.config* file with a flexible configuration feature that can leverage a variety of file formats, such as JSON, as well as environment variables.</span></span>
