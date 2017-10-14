---
title: "ASP.NET Core의 구성"
author: rick-anderson
description: "여러 원본에서 ASP.NET Core 응용 프로그램을 구성 하는 구성 API를 사용 하는 방법에 알아봅니다."
keywords: "ASP.NET Core, 구성, JSON, 구성"
ms.author: riande
manager: wpickett
ms.date: 6/24/2017
ms.topic: article
ms.assetid: b3a5984d-e172-42eb-8a48-547e4acb6806
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/configuration
ms.openlocfilehash: d626768fe1a485705e104a5c758cbdb0b46685a3
ms.sourcegitcommit: 8f4d4fad1ca27adf9e396f5c205c9875a3963664
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/13/2017
---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="31bbf-104">ASP.NET Core의 구성</span><span class="sxs-lookup"><span data-stu-id="31bbf-104">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="31bbf-105">[Rick Anderson](https://twitter.com/RickAndMSFT), [표시 Michaelis](http://intellitect.com/author/mark-michaelis/), [Steve Smith](https://ardalis.com/), [김 Roth](https://github.com/danroth27), 및 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="31bbf-105">[Rick Anderson](https://twitter.com/RickAndMSFT), [Mark Michaelis](http://intellitect.com/author/mark-michaelis/), [Steve Smith](https://ardalis.com/), [Daniel Roth](https://github.com/danroth27), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="31bbf-106">구성 API 앱 이름-값 쌍의 목록에 따라 구성 하는 방법을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-106">The Configuration API provides a way of configuring an app based on a list of name-value pairs.</span></span> <span data-ttu-id="31bbf-107">여러 원본에서 런타임에 구성 읽기입니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-107">Configuration is read at runtime from multiple sources.</span></span> <span data-ttu-id="31bbf-108">여러 수준 계층에 이름-값 쌍을 그룹화 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-108">The name-value pairs can be grouped into a multi-level hierarchy.</span></span> <span data-ttu-id="31bbf-109">에 대 한 구성 공급자가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-109">There are configuration providers for:</span></span>

* <span data-ttu-id="31bbf-110">파일 형식 (INI, JSON 및 XML)</span><span class="sxs-lookup"><span data-stu-id="31bbf-110">File formats (INI, JSON, and XML)</span></span>
* <span data-ttu-id="31bbf-111">명령줄 인수</span><span class="sxs-lookup"><span data-stu-id="31bbf-111">Command-line arguments</span></span>
* <span data-ttu-id="31bbf-112">환경 변수</span><span class="sxs-lookup"><span data-stu-id="31bbf-112">Environment variables</span></span>
* <span data-ttu-id="31bbf-113">메모리 내.NET 개체</span><span class="sxs-lookup"><span data-stu-id="31bbf-113">In-memory .NET objects</span></span>
* <span data-ttu-id="31bbf-114">암호화 된 사용자 저장소</span><span class="sxs-lookup"><span data-stu-id="31bbf-114">An encrypted user store</span></span>
* [<span data-ttu-id="31bbf-115">Azure 주요 자격 증명 모음</span><span class="sxs-lookup"><span data-stu-id="31bbf-115">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
* <span data-ttu-id="31bbf-116">사용자 지정 공급자가 설치 또는 만들기</span><span class="sxs-lookup"><span data-stu-id="31bbf-116">Custom providers, which you install or create</span></span>

<span data-ttu-id="31bbf-117">각 구성 값 문자열 키에 매핑됩니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-117">Each configuration value maps to a string key.</span></span> <span data-ttu-id="31bbf-118">사용자 지정 설정을 deserialize 하는 데 기본 바인딩인 지원은 [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) 개체 (속성이 있는 간단한.NET 클래스).</span><span class="sxs-lookup"><span data-stu-id="31bbf-118">There's built-in binding support to deserialize settings into a custom [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) object (a simple .NET class with properties).</span></span>

<span data-ttu-id="31bbf-119">[샘플 코드 보기 또는 다운로드](https://github.com/aspnet/docs/tree/master/aspnetcore/fundamentals/configuration/sample)([다운로드 방법](xref:tutorials/index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="31bbf-119">[View or download sample code](https://github.com/aspnet/docs/tree/master/aspnetcore/fundamentals/configuration/sample) ([how to download](xref:tutorials/index#how-to-download-a-sample))</span></span>

## <a name="simple-configuration"></a><span data-ttu-id="31bbf-120">단순 구성</span><span class="sxs-lookup"><span data-stu-id="31bbf-120">Simple configuration</span></span>

<span data-ttu-id="31bbf-121">다음 콘솔 응용 프로그램의 JSON 구성 공급자를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-121">The following console app uses the JSON configuration provider:</span></span>

[!code-csharp[Main](configuration/sample/ConfigJson/Program.cs)]

<span data-ttu-id="31bbf-122">응용 프로그램을 읽고 다음 구성 설정을 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-122">The app reads and displays the following configuration settings:</span></span>

[!code-json[Main](configuration/sample/ConfigJson/appsettings.json)]

<span data-ttu-id="31bbf-123">구성 노드는 콜론으로 구분 하는 이름-값 쌍의 계층적 목록으로 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-123">Configuration consists of a hierarchical list of name-value pairs in which the nodes are separated by a colon.</span></span> <span data-ttu-id="31bbf-124">액세스 값을 검색 하는 `Configuration` 인덱서를 해당 항목의 키:</span><span class="sxs-lookup"><span data-stu-id="31bbf-124">To retrieve a value, access the `Configuration` indexer with the corresponding item's key:</span></span>

```csharp
Console.WriteLine($"option1 = {Configuration["subsection:suboption1"]}");
```

<span data-ttu-id="31bbf-125">JSON 형식 구성 소스에서 배열 작업을 콜론으로 구분 된 문자열의 일부로 배열 인덱스를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-125">To work with arrays in JSON-formatted configuration sources, use a array index as part of the colon-separated string.</span></span> <span data-ttu-id="31bbf-126">다음 예제에서는 앞의 첫 번째 항목의 이름을 가져옵니다 `wizards` 배열:</span><span class="sxs-lookup"><span data-stu-id="31bbf-126">The following example gets the name of the first item in the preceding `wizards` array:</span></span>

```csharp
Console.Write($"{Configuration["wizards:0:Name"]}, ");
```

<span data-ttu-id="31bbf-127">하지만 기본 제공에 기록 하는 이름-값 쌍의 `Configuration` 공급자는 **하지** 유지 만들 수 있습니다 값을 저장 하는 사용자 지정 공급자입니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-127">Name-value pairs written to the built in `Configuration` providers are **not** persisted, however, you can create a custom provider that saves values.</span></span> <span data-ttu-id="31bbf-128">참조 [사용자 지정 구성 공급자](xref:fundamentals/configuration#custom-config-providers)합니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-128">See [custom configuration provider](xref:fundamentals/configuration#custom-config-providers).</span></span>

<span data-ttu-id="31bbf-129">앞에 나온 샘플 값을 읽을 구성 인덱서를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-129">The preceding sample uses the configuration indexer to read values.</span></span> <span data-ttu-id="31bbf-130">외부 액세스 구성으로 `Startup`를 사용 하 여는 [옵션 패턴](xref:fundamentals/configuration#options-config-objects)합니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-130">To access configuration outside of `Startup`, use the [options pattern](xref:fundamentals/configuration#options-config-objects).</span></span> <span data-ttu-id="31bbf-131">*옵션 패턴* 이 문서의 뒷부분에 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-131">The *options pattern* is shown later in this article.</span></span>

<span data-ttu-id="31bbf-132">예를 들어, 개발, 테스트 및 프로덕션 환경에 대 한 서로 다른 구성 설정을 일반적이.</span><span class="sxs-lookup"><span data-stu-id="31bbf-132">It's typical to have different configuration settings for different environments, for example, development, test, and production.</span></span> <span data-ttu-id="31bbf-133">`CreateDefaultBuilder` ASP.NET Core 2.x 응용 프로그램의 확장 메서드 (또는 사용 하 여 `AddJsonFile` 및 `AddEnvironmentVariables` ASP.NET Core 1.x 응용 프로그램에서 직접) JSON 파일 및 시스템 구성 소스 읽기에 대 한 구성 공급자를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-133">The `CreateDefaultBuilder` extension method in an ASP.NET Core 2.x app (or using `AddJsonFile` and `AddEnvironmentVariables` directly in an ASP.NET Core 1.x app) adds configuration providers for reading JSON files and system configuration sources:</span></span>

* <span data-ttu-id="31bbf-134">*appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="31bbf-134">*appsettings.json*</span></span>
* <span data-ttu-id="31bbf-135">*appsettings 합니다. \<EnvironmentName >.json*</span><span class="sxs-lookup"><span data-stu-id="31bbf-135">*appsettings.\<EnvironmentName>.json*</span></span>
* <span data-ttu-id="31bbf-136">환경 변수</span><span class="sxs-lookup"><span data-stu-id="31bbf-136">environment variables</span></span>

<span data-ttu-id="31bbf-137">참조 [AddJsonFile](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.configuration.jsonconfigurationextensions) 에 대 한 설명은 매개 변수입니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-137">See [AddJsonFile](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.configuration.jsonconfigurationextensions) for an explanation of the parameters.</span></span> <span data-ttu-id="31bbf-138">`reloadOnChange`ASP.NET Core 1.1에서 이상 에서만 지원 됩니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-138">`reloadOnChange` is only supported in ASP.NET Core 1.1 and higher.</span></span> 

<span data-ttu-id="31bbf-139">지정 된 순서 구성 소스를 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-139">Configuration sources are read in the order they are specified.</span></span> <span data-ttu-id="31bbf-140">위의 코드에서 환경 변수 마지막 읽혀집니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-140">In the code above, the environment variables are read last.</span></span> <span data-ttu-id="31bbf-141">두 이전 공급자에 설정 된 환경을 통해 설정 된 구성 값 바꾸어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-141">Any configuration values set through the environment would replace those set in the two previous providers.</span></span>

<span data-ttu-id="31bbf-142">환경 중 하나에 일반적으로 설정 되어 `Development`, `Staging`, 또는 `Production`합니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-142">The environment is typically set to one of `Development`, `Staging`, or `Production`.</span></span> <span data-ttu-id="31bbf-143">참조 [여러 환경 작업](environments.md) 자세한 정보에 대 한 합니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-143">See [Working with Multiple Environments](environments.md) for more information.</span></span>

<span data-ttu-id="31bbf-144">구성 고려 사항:</span><span class="sxs-lookup"><span data-stu-id="31bbf-144">Configuration considerations:</span></span>

* <span data-ttu-id="31bbf-145">`IOptionsSnapshot`변경 시 구성 데이터를 다시 로드할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-145">`IOptionsSnapshot` can reload configuration data when it changes.</span></span> <span data-ttu-id="31bbf-146">사용 하 여 `IOptionsSnapshot` 구성 데이터를 다시 로드 하는 경우.</span><span class="sxs-lookup"><span data-stu-id="31bbf-146">Use `IOptionsSnapshot` if you need to reload configuration data.</span></span>  <span data-ttu-id="31bbf-147">참조 [IOptionsSnapshot](#ioptionssnapshot) 자세한 정보에 대 한 합니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-147">See [IOptionsSnapshot](#ioptionssnapshot) for more information.</span></span>
* <span data-ttu-id="31bbf-148">구성 키 대/소문자를 구분 합니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-148">Configuration keys are case insensitive.</span></span>
* <span data-ttu-id="31bbf-149">가장 좋은 방법은 로컬 환경에서 배포 된 구성 파일을 설정 하는 아무 것도 재정의할 수 있도록 마지막으로, 환경 변수를 지정 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-149">A best practice is to specify environment variables last, so that the local environment can override anything set in deployed configuration files.</span></span>
* <span data-ttu-id="31bbf-150">**하지** 구성 공급자 코드에 또는 일반 텍스트로 구성 파일에 암호 또는 기타 중요 한 데이터를 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-150">**Never** store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span> <span data-ttu-id="31bbf-151">테스트 환경 하거나 사용자가 개발에서 생산 암호를 사용 하지 마십시오.</span><span class="sxs-lookup"><span data-stu-id="31bbf-151">Don't use production secrets in your development or test environments.</span></span> <span data-ttu-id="31bbf-152">대신 저장소로 실수로 커밋된 없습니다 하므로 프로젝트 트리 외부 암호를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-152">Instead, specify secrets outside the project tree, so they cannot be accidentally committed into your repository.</span></span> <span data-ttu-id="31bbf-153">에 대 한 자세한 내용은 [여러 환경 작업](environments.md) 및 관리 [개발 하는 동안 앱 암호의 안전한 저장소](../security/app-secrets.md)합니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-153">Learn more about [Working with Multiple Environments](environments.md) and managing [safe storage of app secrets during development](../security/app-secrets.md).</span></span>
* <span data-ttu-id="31bbf-154">경우 `:` 수 없습니다 시스템에서 환경 변수에서 사용 되는 대체 `:` 와 `__` (이중 밑줄).</span><span class="sxs-lookup"><span data-stu-id="31bbf-154">If `:` cannot be used in environment variables in your system,  replace `:`  with `__` (double underscore).</span></span>

<a name="options-config-objects"></a>

## <a name="using-options-and-configuration-objects"></a><span data-ttu-id="31bbf-155">옵션 및 구성 개체를 사용 하 여</span><span class="sxs-lookup"><span data-stu-id="31bbf-155">Using Options and configuration objects</span></span>

<span data-ttu-id="31bbf-156">옵션 패턴을 관련된 설정 그룹을 나타내는 사용자 지정 옵션 클래스를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-156">The options pattern uses custom options classes to represent a group of related settings.</span></span> <span data-ttu-id="31bbf-157">앱 내에서 각 기능에 대해 분리 된 클래스를 만드는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-157">We recommended that you create decoupled classes for each feature within your app.</span></span> <span data-ttu-id="31bbf-158">분리 된 클래스를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-158">Decoupled classes follow:</span></span>

* <span data-ttu-id="31bbf-159">[인터페이스 분리 원칙 (ISP)](http://deviq.com/interface-segregation-principle/) : 클래스를 사용 하는 구성 설정에 따라서만 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-159">The [Interface Segregation Principle (ISP)](http://deviq.com/interface-segregation-principle/) : Classes depend only on the configuration settings they use.</span></span>
* <span data-ttu-id="31bbf-160">[문제의 분리](http://deviq.com/separation-of-concerns/) : 응용 프로그램의 다른 부분에 대 한 설정을 종속 또는 서로 결합 합니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-160">[Separation of Concerns](http://deviq.com/separation-of-concerns/) : Settings for different parts of your app are not dependent or coupled with one another.</span></span>

<span data-ttu-id="31bbf-161">옵션 클래스는 추상 이어야 합니다. 매개 변수가 없는 public 생성자를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-161">The options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="31bbf-162">예:</span><span class="sxs-lookup"><span data-stu-id="31bbf-162">For example:</span></span>

[!code-csharp[Main](configuration/sample/UsingOptions/Models/MyOptions.cs)]

<a name="options-example"></a>

<span data-ttu-id="31bbf-163">다음 코드에서 JSON 구성 공급자가 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-163">In the following code, the JSON configuration provider is enabled.</span></span> <span data-ttu-id="31bbf-164">`MyOptions` 클래스는 서비스 컨테이너에 추가 되 고 구성에 연결 합니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-164">The `MyOptions` class is added to the service container and bound to configuration.</span></span>

[!code-csharp[Main](configuration/sample/UsingOptions/Startup.cs?name=snippet1&highlight=8,20-21)]

<span data-ttu-id="31bbf-165">다음 [컨트롤러](../mvc/controllers/index.md) 사용 하 여 [생성자 종속성 주입](xref:fundamentals/dependency-injection#what-is-dependency-injection) 에 [ `IOptions<TOptions>` ](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.options.ioptions-1) 설정에 액세스 하려면:</span><span class="sxs-lookup"><span data-stu-id="31bbf-165">The following [controller](../mvc/controllers/index.md)  uses [constructor Dependency Injection](xref:fundamentals/dependency-injection#what-is-dependency-injection) on [`IOptions<TOptions>`](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.options.ioptions-1) to access settings:</span></span>

[!code-csharp[Main](configuration/sample/UsingOptions/Controllers/HomeController.cs?name=snippet1)]

<span data-ttu-id="31bbf-166">다음으로 *appsettings.json* 파일:</span><span class="sxs-lookup"><span data-stu-id="31bbf-166">With the following *appsettings.json* file:</span></span>

[!code-json[Main](configuration/sample/UsingOptions/appsettings1.json)]

<span data-ttu-id="31bbf-167">`HomeController.Index` 메서드 반환 `option1 = value1_from_json, option2 = 2`합니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-167">The `HomeController.Index` method returns `option1 = value1_from_json, option2 = 2`.</span></span>

<span data-ttu-id="31bbf-168">일반적인 앱 단일 옵션 파일에 전체 구성을 바인딩 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-168">Typical apps won't bind the entire configuration to a single options file.</span></span> <span data-ttu-id="31bbf-169">나중에 사용 하는 방법을 보여 드리 려 `GetSection` 섹션에 바인딩할 합니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-169">Later on I'll show how to use `GetSection` to bind to a section.</span></span>

<span data-ttu-id="31bbf-170">다음 코드에서 두 번째 `IConfigureOptions<TOptions>` 서비스를 서비스 컨테이너에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-170">In the following code, a second `IConfigureOptions<TOptions>` service is added to the service container.</span></span> <span data-ttu-id="31bbf-171">대리자를 사용 하 여 바인딩을 구성 `MyOptions`합니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-171">It uses a delegate to configure the binding with `MyOptions`.</span></span>

[!code-csharp[Main](configuration/sample/UsingOptions/Startup2.cs?name=snippet1&highlight=9-13)]

<span data-ttu-id="31bbf-172">여러 구성 공급자를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-172">You can add multiple configuration providers.</span></span> <span data-ttu-id="31bbf-173">구성 공급자는 NuGet 패키지에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-173">Configuration providers are available in NuGet packages.</span></span> <span data-ttu-id="31bbf-174">등록 된 순서에 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-174">They are applied in order they are registered.</span></span>

<span data-ttu-id="31bbf-175">호출할 때마다 `Configure<TOptions>` 추가 `IConfigureOptions<TOptions>` 서비스를 서비스 컨테이너입니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-175">Each call to `Configure<TOptions>` adds an `IConfigureOptions<TOptions>` service to the service container.</span></span> <span data-ttu-id="31bbf-176">위의 예제에서는 값 `Option1` 및 `Option2` 에 지정 된 *appsettings.json* -하지만의 값 `Option1` 구성 된 대리자가 재정의 됩니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-176">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json* -- but the value of `Option1` is overridden by the configured delegate.</span></span> 

<span data-ttu-id="31bbf-177">둘 이상의 구성 서비스를 사용 하는 경우 마지막 구성 소스 지정 "wins" (구성 값을 설정).</span><span class="sxs-lookup"><span data-stu-id="31bbf-177">When more than one configuration service is enabled, the last configuration source specified "wins" (sets the configuration value).</span></span> <span data-ttu-id="31bbf-178">위의 코드는 `HomeController.Index` 메서드 반환 `option1 = value1_from_action, option2 = 2`합니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-178">In the preceding code, the `HomeController.Index` method returns `option1 = value1_from_action, option2 = 2`.</span></span>

<span data-ttu-id="31bbf-179">각 속성에 옵션 유형 폼의 구성 키에 바인딩된 구성 옵션을 바인딩할 때 `property[:sub-property:]`합니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-179">When you bind options to configuration, each property in your options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="31bbf-180">예를 들어는 `MyOptions.Option1` 속성의 키에 바인딩된 `Option1`에서 읽음는 `option1` 속성 *appsettings.json*합니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-180">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span> <span data-ttu-id="31bbf-181">하위 속성 샘플은이 문서의 뒷부분에 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-181">A sub-property sample is shown later in this article.</span></span>

<span data-ttu-id="31bbf-182">다음 코드에서는 세 번째 `IConfigureOptions<TOptions>` 서비스를 서비스 컨테이너에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-182">In the following code, a third `IConfigureOptions<TOptions>` service is added to the service container.</span></span> <span data-ttu-id="31bbf-183">바인딩할 `MySubOptions` 섹션 `subsection` 의 *appsettings.json* 파일:</span><span class="sxs-lookup"><span data-stu-id="31bbf-183">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[Main](configuration/sample/UsingOptions/Startup3.cs?name=snippet1&highlight=16-17)]

<span data-ttu-id="31bbf-184">참고:이 확장 방법을 사용 하려면는 `Microsoft.Extensions.Options.ConfigurationExtensions` NuGet 패키지 합니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-184">Note: This extension method requires the `Microsoft.Extensions.Options.ConfigurationExtensions` NuGet package.</span></span>

<span data-ttu-id="31bbf-185">다음을 사용 하 여 *appsettings.json* 파일:</span><span class="sxs-lookup"><span data-stu-id="31bbf-185">Using the following *appsettings.json* file:</span></span>

[!code-json[Main](configuration/sample/UsingOptions/appsettings.json)]

<span data-ttu-id="31bbf-186">`MySubOptions` 클래스:</span><span class="sxs-lookup"><span data-stu-id="31bbf-186">The `MySubOptions` class:</span></span>

[!code-csharp[Main](configuration/sample/UsingOptions/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="31bbf-187">다음으로 `Controller`:</span><span class="sxs-lookup"><span data-stu-id="31bbf-187">With the following `Controller`:</span></span>

[!code-csharp[Main](configuration/sample/UsingOptions/Controllers/HomeController2.cs?name=snippet1)]

<span data-ttu-id="31bbf-188">`subOption1 = subvalue1_from_json, subOption2 = 200`이 반환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-188">`subOption1 = subvalue1_from_json, subOption2 = 200` is returned.</span></span>

<span data-ttu-id="31bbf-189">옵션을 보기 모델에 제공 하거나 삽입할 수도 `IOptions<TOptions>` 보기에 직접:</span><span class="sxs-lookup"><span data-stu-id="31bbf-189">You can also supply options in a view model or inject `IOptions<TOptions>` directly into a view:</span></span>

[!code-html[Main](configuration/sample/UsingOptions/Views/Home/Index.cshtml?highlight=3-4,16-17,20-21)]

<a name="in-memory-provider"></a>

## <a name="ioptionssnapshot"></a><span data-ttu-id="31bbf-190">IOptionsSnapshot</span><span class="sxs-lookup"><span data-stu-id="31bbf-190">IOptionsSnapshot</span></span>

<span data-ttu-id="31bbf-191">*ASP.NET Core 1.1 이상이 필요합니다.*</span><span class="sxs-lookup"><span data-stu-id="31bbf-191">*Requires ASP.NET Core 1.1 or higher.*</span></span>

<span data-ttu-id="31bbf-192">`IOptionsSnapshot`구성 파일 변경 될 때 구성 데이터를 다시 로드를 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-192">`IOptionsSnapshot` supports reloading configuration data when the configuration file has changed.</span></span> <span data-ttu-id="31bbf-193">오버 헤드가 최소로 유지가 된 있습니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-193">It has minimal overhead.</span></span> <span data-ttu-id="31bbf-194">사용 하 여 `IOptionsSnapshot` 와 `reloadOnChange: true`, 옵션에 바인딩된 `IConfiguration` 변경 될 때 다시 로드 합니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-194">Using `IOptionsSnapshot` with `reloadOnChange: true`, the options are bound to `IConfiguration` and reloaded when changed.</span></span>

<span data-ttu-id="31bbf-195">다음 샘플에는 새로운 방법을 보여 줍니다 `IOptionsSnapshot` 후에 만들어진 *config.json* 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-195">The following sample demonstrates how a new `IOptionsSnapshot` is created after *config.json* changes.</span></span> <span data-ttu-id="31bbf-196">동일한 서버에 요청을 반환 합니다 시간 *config.json* 가 **하지** 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-196">Requests to the server will return the same time when *config.json* has **not** changed.</span></span> <span data-ttu-id="31bbf-197">첫 번째 요청 이후 *config.json* 변경 시간을 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-197">The first request after *config.json* changes will show a new time.</span></span>

[!code-csharp[Main](configuration/sample/IOptionsSnapshot2/Startup.cs?name=snippet1&highlight=1-9,13-18,32,33,52,53)]

<span data-ttu-id="31bbf-198">다음 그림에서는 서버 출력을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-198">The following image shows the server output:</span></span>

![이미지 표시 된 브라우저 "마지막 업데이트: 2016 년 11 월 22 일 오후 4시 43분"](configuration/_static/first.png)

<span data-ttu-id="31bbf-200">메시지 값 또는 표시 된 시간이 변경 되지 않습니다 브라우저 새로 고침 (때 *config.json* 변경 되지 않은).</span><span class="sxs-lookup"><span data-stu-id="31bbf-200">Refreshing the browser doesn't change the message value or time displayed (when *config.json* has not changed).</span></span>

<span data-ttu-id="31bbf-201">변경 하 고 저장 된 *config.json* 후 브라우저를 새로 고칩니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-201">Change and save the  *config.json* and then refresh the browser:</span></span>

![이미지 표시 된 브라우저 "마지막으로 업데이트를 e: 2016 년 11 월 22 일 오후 4시 53분"](configuration/_static/change.png)

## <a name="in-memory-provider-and-binding-to-a-poco-class"></a><span data-ttu-id="31bbf-203">메모리 내 공급자 및 POCO 클래스에 바인딩</span><span class="sxs-lookup"><span data-stu-id="31bbf-203">In-memory provider and binding to a POCO class</span></span>

<span data-ttu-id="31bbf-204">다음 샘플에는 클래스에 바인딩하고 메모리 내 공급자를 사용 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-204">The following sample shows how to use the in-memory provider and bind to a class:</span></span>

[!code-csharp[Main](configuration/sample/InMemory/Program.cs)]

<span data-ttu-id="31bbf-205">구성 값을 문자열로 반환 됩니다 있지만 바인딩 개체의 생성을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-205">Configuration values are returned as strings, but binding enables the construction of objects.</span></span> <span data-ttu-id="31bbf-206">바인딩은 POCO 개체 또는 전체 개체 그래프를 검색할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-206">Binding allows you to retrieve POCO objects or even entire object graphs.</span></span> <span data-ttu-id="31bbf-207">다음 샘플에서는에 바인딩하는 방법을 보여 줍니다. `MyWindow` ASP.NET Core MVC 응용 프로그램으로 옵션 패턴을 사용 하 고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-207">The following sample shows how to bind to `MyWindow` and use the options pattern with a ASP.NET Core MVC app:</span></span>

[!code-csharp[Main](configuration/sample/WebConfigBind/MyWindow.cs)]

[!code-json[Main](configuration/sample/WebConfigBind/appsettings.json)]

<span data-ttu-id="31bbf-208">사용자 지정 클래스 바인딩 `ConfigureServices` 호스트를 작성할 때:</span><span class="sxs-lookup"><span data-stu-id="31bbf-208">Bind the custom class in `ConfigureServices` when building the host:</span></span>

[!code-csharp[Main](configuration/sample/WebConfigBind/Program.cs?name=snippet1&highlight=3-4)]

<span data-ttu-id="31bbf-209">설정을 표시는 `HomeController`:</span><span class="sxs-lookup"><span data-stu-id="31bbf-209">Display the settings from the `HomeController`:</span></span>

[!code-csharp[Main](configuration/sample/WebConfigBind/Controllers/HomeController.cs)]

### <a name="getvalue"></a><span data-ttu-id="31bbf-210">GetValue</span><span class="sxs-lookup"><span data-stu-id="31bbf-210">GetValue</span></span>

<span data-ttu-id="31bbf-211">다음 샘플을 참조 하십시오.는 [GetValue<T> ](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.configuration.configurationbinder#Microsoft_Extensions_Configuration_ConfigurationBinder_GetValue_Microsoft_Extensions_Configuration_IConfiguration_System_Type_System_String_System_Object_) 확장 메서드:</span><span class="sxs-lookup"><span data-stu-id="31bbf-211">The following sample demonstrates the [GetValue<T>](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.configuration.configurationbinder#Microsoft_Extensions_Configuration_ConfigurationBinder_GetValue_Microsoft_Extensions_Configuration_IConfiguration_System_Type_System_String_System_Object_) extension method:</span></span>

[!code-csharp[Main](configuration/sample/InMemoryGetValue/Program.cs?highlight=27-29)]

<span data-ttu-id="31bbf-212">ConfigurationBinder `GetValue<T>` 메서드 기본값 (이 샘플에는 80)을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-212">The ConfigurationBinder's `GetValue<T>` method allows you to specify a default value (80 in the sample).</span></span> <span data-ttu-id="31bbf-213">`GetValue<T>`간단한 시나리오 되며 전체 섹션에 바인딩되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-213">`GetValue<T>` is for simple scenarios and does not bind to entire sections.</span></span> <span data-ttu-id="31bbf-214">`GetValue<T>`스칼라 값을 가져옵니다 `GetSection(key).Value` 특정 형식으로 변환 합니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-214">`GetValue<T>` gets scalar values from `GetSection(key).Value` converted to a specific type.</span></span>

## <a name="binding-to-an-object-graph"></a><span data-ttu-id="31bbf-215">개체 그래프에 바인딩</span><span class="sxs-lookup"><span data-stu-id="31bbf-215">Binding to an object graph</span></span>

<span data-ttu-id="31bbf-216">클래스의 각 개체를 재귀적으로 바인딩할을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-216">You can recursively bind to each object in a class.</span></span> <span data-ttu-id="31bbf-217">다음 사항을 고려 `AppOptions` 클래스:</span><span class="sxs-lookup"><span data-stu-id="31bbf-217">Consider the following `AppOptions` class:</span></span>

[!code-csharp[Main](configuration/sample/ObjectGraph/AppOptions.cs)]

<span data-ttu-id="31bbf-218">다음 샘플에 바인딩하는 `AppOptions` 클래스:</span><span class="sxs-lookup"><span data-stu-id="31bbf-218">The following sample binds to the `AppOptions` class:</span></span>

[!code-csharp[Main](configuration/sample/ObjectGraph/Program.cs?highlight=15-16)]

<span data-ttu-id="31bbf-219">**ASP.NET Core 1.1** 및 이상을 사용 하 여 `Get<T>`, 전체 섹션와 함께 작업 하 합니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-219">**ASP.NET Core 1.1** and higher can use  `Get<T>`, which works with entire sections.</span></span> <span data-ttu-id="31bbf-220">`Get<T>`사용 하 여 보다 자세한 convienent 수 `Bind`합니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-220">`Get<T>` can be more convienent than using `Bind`.</span></span> <span data-ttu-id="31bbf-221">다음 코드를 사용 하는 방법을 보여 줍니다 `Get<T>` 위의 샘플:</span><span class="sxs-lookup"><span data-stu-id="31bbf-221">The following code shows how to use `Get<T>` with the sample above:</span></span>

```csharp
var appConfig = config.GetSection("App").Get<AppOptions>();
```

<span data-ttu-id="31bbf-222">다음을 사용 하 여 *appsettings.json* 파일:</span><span class="sxs-lookup"><span data-stu-id="31bbf-222">Using the following *appsettings.json* file:</span></span>

[!code-json[Main](configuration/sample/ObjectGraph/appsettings.json)]

<span data-ttu-id="31bbf-223">프로그램은 표시 `Height 11`합니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-223">The program displays `Height 11`.</span></span>

<span data-ttu-id="31bbf-224">다음 코드를 장치에 사용할 수 구성을 테스트 합니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-224">The following code can be used to unit test the configuration:</span></span>

```csharp
[Fact]
public void CanBindObjectTree()
{
    var dict = new Dictionary<string, string>
        {
            {"App:Profile:Machine", "Rick"},
            {"App:Connection:Value", "connectionstring"},
            {"App:Window:Height", "11"},
            {"App:Window:Width", "11"}
        };
    var builder = new ConfigurationBuilder();
    builder.AddInMemoryCollection(dict);
    var config = builder.Build();

    var options = new AppOptions();
    config.GetSection("App").Bind(options);

    Assert.Equal("Rick", options.Profile.Machine);
    Assert.Equal(11, options.Window.Height);
    Assert.Equal(11, options.Window.Width);
    Assert.Equal("connectionstring", options.Connection.Value);
}
```

<a name="custom-config-providers"></a>

## <a name="basic-sample-of-entity-framework-custom-provider"></a><span data-ttu-id="31bbf-225">Entity Framework 사용자 지정 공급자의 기본 샘플</span><span class="sxs-lookup"><span data-stu-id="31bbf-225">Basic sample of Entity Framework custom provider</span></span>

<span data-ttu-id="31bbf-226">이 섹션에서는 EF를 사용 하 여 데이터베이스에서 이름-값 쌍을 읽을 수 있는 기본 구성 공급자가 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-226">In this section, a basic configuration provider that reads name-value pairs from a database using EF is created.</span></span> 

<span data-ttu-id="31bbf-227">정의 `ConfigurationValue` 데이터베이스의 구성 값을 저장 하기 위한 엔터티:</span><span class="sxs-lookup"><span data-stu-id="31bbf-227">Define a `ConfigurationValue` entity for storing configuration values in the database:</span></span>

[!code-csharp[Main](configuration/sample/CustomConfigurationProvider/ConfigurationValue.cs)]

<span data-ttu-id="31bbf-228">추가 `ConfigurationContext` 저장 하 고 구성된 된 값에 액세스 합니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-228">Add a `ConfigurationContext` to store and access the configured values:</span></span>

[!code-csharp[Main](configuration/sample/CustomConfigurationProvider/ConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="31bbf-229">구현 하는 클래스를 만드는 [IConfigurationSource](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.configuration.iconfigurationsource):</span><span class="sxs-lookup"><span data-stu-id="31bbf-229">Create an class that implements [IConfigurationSource](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.configuration.iconfigurationsource):</span></span>

[!code-csharp[Main](configuration/sample/CustomConfigurationProvider/EntityFrameworkConfigurationSource.cs?highlight=7)]

<span data-ttu-id="31bbf-230">상속 하 여 사용자 지정 구성 공급자를 만들기 [ConfigurationProvider](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.configuration.configurationprovider)합니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-230">Create the custom configuration provider by inheriting from [ConfigurationProvider](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.configuration.configurationprovider).</span></span>  <span data-ttu-id="31bbf-231">구성 공급자는 비어 있는 경우 데이터베이스를 초기화 합니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-231">The configuration provider initializes the database when it's empty:</span></span>

[!code-csharp[Main](configuration/sample/CustomConfigurationProvider/EntityFrameworkConfigurationProvider.cs?highlight=9,18-31,38-39)]

<span data-ttu-id="31bbf-232">("Value_from_ef_1" 및 "value_from_ef_2")은 데이터베이스의 강조 표시 된 값은 샘플 실행 될 때 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-232">The highlighted values from the database ("value_from_ef_1" and "value_from_ef_2") are displayed when the sample is run.</span></span>

<span data-ttu-id="31bbf-233">추가할 수는 `EFConfigSource` 구성 소스를 추가 하기 위한 확장 메서드:</span><span class="sxs-lookup"><span data-stu-id="31bbf-233">You can add an `EFConfigSource` extension method for adding the configuration source:</span></span>

[!code-csharp[Main](configuration/sample/CustomConfigurationProvider/EntityFrameworkExtensions.cs?highlight=12)]

<span data-ttu-id="31bbf-234">다음 코드에서는 사용자 지정을 사용 하는 방법을 보여 줍니다. `EFConfigProvider`:</span><span class="sxs-lookup"><span data-stu-id="31bbf-234">The following code shows how to use the custom `EFConfigProvider`:</span></span>

[!code-csharp[Main](configuration/sample/CustomConfigurationProvider/Program.cs?highlight=21-26)]

<span data-ttu-id="31bbf-235">사용자 지정을 추가 하는 샘플 참고 `EFConfigProvider` JSON 공급자 이므로 데이터베이스의 모든 설정의 설정을 재정의 합니다에서 *appsettings.json* 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-235">Note the sample adds the custom `EFConfigProvider` after the JSON provider, so any settings from the database will override settings from the *appsettings.json* file.</span></span>

<span data-ttu-id="31bbf-236">다음을 사용 하 여 *appsettings.json* 파일:</span><span class="sxs-lookup"><span data-stu-id="31bbf-236">Using the following *appsettings.json* file:</span></span>

[!code-json[Main](configuration/sample/CustomConfigurationProvider/appsettings.json)]

<span data-ttu-id="31bbf-237">다음이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-237">The following is displayed:</span></span>

```console
key1=value_from_ef_1
key2=value_from_ef_2
key3=value_from_json_3
```

## <a name="commandline-configuration-provider"></a><span data-ttu-id="31bbf-238">명령줄 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="31bbf-238">CommandLine configuration provider</span></span>

<span data-ttu-id="31bbf-239">[CommandLine 구성 공급자](/aspnet/core/api/microsoft.extensions.configuration.commandline.commandlineconfigurationprovider) 런타임에 구성에 대 한 키-값 쌍 명령줄 인수를 받습니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-239">The [CommandLine configuration provider](/aspnet/core/api/microsoft.extensions.configuration.commandline.commandlineconfigurationprovider) receives command-line argument key-value pairs for configuration at runtime.</span></span>

[<span data-ttu-id="31bbf-240">보기 또는 CommandLine 구성 샘플 다운로드</span><span class="sxs-lookup"><span data-stu-id="31bbf-240">View or download the CommandLine configuration sample</span></span>](https://github.com/aspnet/docs/tree/master/aspnetcore/fundamentals/configuration/sample/CommandLine)

### <a name="setting-up-the-provider"></a><span data-ttu-id="31bbf-241">공급자를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-241">Setting up the provider</span></span>

# <a name="basic-configurationtabbasicconfiguration"></a>[<span data-ttu-id="31bbf-242">기본 구성</span><span class="sxs-lookup"><span data-stu-id="31bbf-242">Basic Configuration</span></span>](#tab/basicconfiguration)

<span data-ttu-id="31bbf-243">명령줄 구성을 활성화 하려면 호출는 `AddCommandLine` 인스턴스의 확장 메서드 [ConfigurationBuilder](/api/microsoft.extensions.configuration.configurationbuilder):</span><span class="sxs-lookup"><span data-stu-id="31bbf-243">To activate command-line configuration, call the `AddCommandLine` extension method on an instance of [ConfigurationBuilder](/api/microsoft.extensions.configuration.configurationbuilder):</span></span>

[!code-csharp[Main](configuration/sample_snapshot/CommandLine/Program.cs?highlight=18,21)]

<span data-ttu-id="31bbf-244">코드를 실행 다음과 같은 출력이 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-244">Running the code, the following output is displayed:</span></span>

```console
MachineName: MairaPC
Left: 1980
```

<span data-ttu-id="31bbf-245">값을 변경 하는 명령줄에 키-값 쌍 인수를 전달 `Profile:MachineName` 및 `App:MainWindow:Left`:</span><span class="sxs-lookup"><span data-stu-id="31bbf-245">Passing argument key-value pairs on the command line changes the values of `Profile:MachineName` and `App:MainWindow:Left`:</span></span>

```console
dotnet run Profile:MachineName=BartPC App:MainWindow:Left=1979
```

<span data-ttu-id="31bbf-246">콘솔 창에 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-246">The console window displays:</span></span>

```console
MachineName: BartPC
Left: 1979
```

<span data-ttu-id="31bbf-247">명령줄 구성의 다른 구성 공급자가 제공 되는 구성을 재정의 하려면 `AddCommandLine` 마지막에 `ConfigurationBuilder`:</span><span class="sxs-lookup"><span data-stu-id="31bbf-247">To override configuration provided by other configuration providers with command-line configuration, call `AddCommandLine` last on `ConfigurationBuilder`:</span></span>

[!code-csharp[Main](configuration/sample_snapshot/CommandLine/Program2.cs?range=11-16&highlight=1,5)]

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="31bbf-248">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="31bbf-248">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="31bbf-249">정적 편의 메서드를 사용 하는 일반적인 ASP.NET Core 2.x 앱 `CreateDefaultBuilder` 호스트를 작성 합니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-249">Typical ASP.NET Core 2.x apps use the static convenience method `CreateDefaultBuilder` to build the host:</span></span>

[!code-csharp[Main](configuration/sample_snapshot/Program.cs?highlight=12)]

<span data-ttu-id="31bbf-250">`CreateDefaultBuilder`선택적 구성에서 로드 *appsettings.json*, *appsettings. { 환경}.json*, [사용자의 비밀](xref:security/app-secrets) (에 `Development` 환경), 환경 변수 및 명령줄 인수입니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-250">`CreateDefaultBuilder` loads optional configuration from *appsettings.json*, *appsettings.{Environment}.json*, [user secrets](xref:security/app-secrets) (in the `Development` environment), environment variables, and command-line arguments.</span></span> <span data-ttu-id="31bbf-251">명령줄 구성 공급자 마지막에 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-251">The CommandLine configuration provider is called last.</span></span> <span data-ttu-id="31bbf-252">공급자를 마지막 호출 구성 집합을 다른 구성 공급자가 재정의 하는 런타임 시 전달 되는 명령줄 인수 앞서 호출 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-252">Calling the provider last allows the command-line arguments passed at runtime to override configuration set by the other configuration providers called earlier.</span></span>

<span data-ttu-id="31bbf-253">위해 *appsettings* 파일은 `reloadOnChange` 를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-253">Note that for *appsettings* files that `reloadOnChange` is enabled.</span></span> <span data-ttu-id="31bbf-254">명령줄 인수는 일치 하는 구성 값이 재정의 됩니다는 *appsettings* 응용 프로그램 시작 후 파일이 변경 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-254">Command-line arguments are overridden if a matching configuration value in an *appsettings* file is changed after the app starts.</span></span>

> [!NOTE]
> <span data-ttu-id="31bbf-255">사용 하는 대신는 `CreateDefaultBuilder` 메서드를 사용 하 여 호스트를 만드는 [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) 하 고 사용 하 여 구성을 수동으로 작성 [ConfigurationBuilder](/api/microsoft.extensions.configuration.configurationbuilder) ASP.NET Core에서 지원 됩니다 2.x 합니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-255">As an alternative to using the `CreateDefaultBuilder` method, creating a host using [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) and manually building configuration with [ConfigurationBuilder](/api/microsoft.extensions.configuration.configurationbuilder) is supported in ASP.NET Core 2.x.</span></span> <span data-ttu-id="31bbf-256">자세한 내용은 ASP.NET Core 1.x 탭을 참조 하십시오.</span><span class="sxs-lookup"><span data-stu-id="31bbf-256">See the ASP.NET Core 1.x tab for more information.</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="31bbf-257">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="31bbf-257">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="31bbf-258">만들기는 [ConfigurationBuilder](/api/microsoft.extensions.configuration.configurationbuilder) 호출는 `AddCommandLine` CommandLine 구성 공급자를 사용 하는 메서드.</span><span class="sxs-lookup"><span data-stu-id="31bbf-258">Create a [ConfigurationBuilder](/api/microsoft.extensions.configuration.configurationbuilder) and call the `AddCommandLine` method to use the CommandLine configuration provider.</span></span> <span data-ttu-id="31bbf-259">공급자를 마지막 호출 구성 집합을 다른 구성 공급자가 재정의 하는 런타임 시 전달 되는 명령줄 인수 앞서 호출 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-259">Calling the provider last allows the command-line arguments passed at runtime to override configuration set by the other configuration providers called earlier.</span></span> <span data-ttu-id="31bbf-260">구성을 사용 하 여 적용 [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) 와 `UseConfiguration` 메서드:</span><span class="sxs-lookup"><span data-stu-id="31bbf-260">Apply the configuration to [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) with the `UseConfiguration` method:</span></span>

[!code-csharp[Main](configuration/sample_snapshot/CommandLine/Program2.cs?highlight=11,15,19)]

---

### <a name="arguments"></a><span data-ttu-id="31bbf-261">인수</span><span class="sxs-lookup"><span data-stu-id="31bbf-261">Arguments</span></span>

<span data-ttu-id="31bbf-262">명령줄에 전달 된 인수는 다음 표에 표시 된 두 형식 중 하나를 따라야 합니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-262">Arguments passed on the command line must conform to one of two formats shown in the following table.</span></span>

| <span data-ttu-id="31bbf-263">인수 형식</span><span class="sxs-lookup"><span data-stu-id="31bbf-263">Argument format</span></span>                                                     | <span data-ttu-id="31bbf-264">예제</span><span class="sxs-lookup"><span data-stu-id="31bbf-264">Example</span></span>        |
| ------------------------------------------------------------------- | :------------: |
| <span data-ttu-id="31bbf-265">단일 인수: 등호로 구분 된 키-값 쌍 (`=`)</span><span class="sxs-lookup"><span data-stu-id="31bbf-265">Single argument: a key-value pair separated by an equals sign (`=`)</span></span> | `key1=value`   |
| <span data-ttu-id="31bbf-266">두 개의 인수 순서: 공백으로 구분 되는 키-값 쌍</span><span class="sxs-lookup"><span data-stu-id="31bbf-266">Sequence of two arguments: a key-value pair separated by a space</span></span>    | `/key1 value1` |

<span data-ttu-id="31bbf-267">**단일 인수**</span><span class="sxs-lookup"><span data-stu-id="31bbf-267">**Single argument**</span></span>

<span data-ttu-id="31bbf-268">값은 등호 따라야 합니다. (`=`).</span><span class="sxs-lookup"><span data-stu-id="31bbf-268">The value must follow an equals sign (`=`).</span></span> <span data-ttu-id="31bbf-269">값은 null 일 수 있습니다 (예를 들어 `mykey=`).</span><span class="sxs-lookup"><span data-stu-id="31bbf-269">The value can be null (for example, `mykey=`).</span></span>

<span data-ttu-id="31bbf-270">키 접두사가 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-270">The key may have a prefix.</span></span>

| <span data-ttu-id="31bbf-271">키 접두사</span><span class="sxs-lookup"><span data-stu-id="31bbf-271">Key prefix</span></span>               | <span data-ttu-id="31bbf-272">예제</span><span class="sxs-lookup"><span data-stu-id="31bbf-272">Example</span></span>         |
| ------------------------ | :-------------: |
| <span data-ttu-id="31bbf-273">접두사 없음</span><span class="sxs-lookup"><span data-stu-id="31bbf-273">No prefix</span></span>                | `key1=value1`   |
| <span data-ttu-id="31bbf-274">단일 대시 (`-`) &#8224;</span><span class="sxs-lookup"><span data-stu-id="31bbf-274">Single dash (`-`)&#8224;</span></span> | `-key2=value2`  |
| <span data-ttu-id="31bbf-275">대시 두 개 (`--`)</span><span class="sxs-lookup"><span data-stu-id="31bbf-275">Two dashes (`--`)</span></span>        | `--key3=value3` |
| <span data-ttu-id="31bbf-276">슬래시 (`/`)</span><span class="sxs-lookup"><span data-stu-id="31bbf-276">Forward slash (`/`)</span></span>      | `/key4=value4`  |

<span data-ttu-id="31bbf-277">&#8224; 단일 대시 접두사를 사용 하 여 키 (`-`) 제공 되어야 [매핑 전환](#switch-mappings), 아래에서 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-277">&#8224;A key with a single dash prefix (`-`) must be provided in [switch mappings](#switch-mappings), described below.</span></span>

<span data-ttu-id="31bbf-278">예제 명령입니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-278">Example command:</span></span>

```console
dotnet run key1=value1 -key2=value2 --key3=value3 /key4=value4
```

<span data-ttu-id="31bbf-279">참고: 경우 `-key1` 에 존재 하지는 [매핑 전환](#switch-mappings) 구성 공급자에 게 제공는 `FormatException` throw 됩니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-279">Note: If `-key1` isn't present in the [switch mappings](#switch-mappings) given to the configuration provider, a `FormatException` is thrown.</span></span>

<span data-ttu-id="31bbf-280">**두 인수 중 시퀀스**</span><span class="sxs-lookup"><span data-stu-id="31bbf-280">**Sequence of two arguments**</span></span>

<span data-ttu-id="31bbf-281">값은 null 일 수 없으며 공백으로 구분 하 여 키를 수행 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-281">The value can't be null and must follow the key separated by a space.</span></span>

<span data-ttu-id="31bbf-282">키에 접두사가 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-282">The key must have a prefix.</span></span>

| <span data-ttu-id="31bbf-283">키 접두사</span><span class="sxs-lookup"><span data-stu-id="31bbf-283">Key prefix</span></span>               | <span data-ttu-id="31bbf-284">예제</span><span class="sxs-lookup"><span data-stu-id="31bbf-284">Example</span></span>         |
| ------------------------ | :-------------: |
| <span data-ttu-id="31bbf-285">단일 대시 (`-`) &#8224;</span><span class="sxs-lookup"><span data-stu-id="31bbf-285">Single dash (`-`)&#8224;</span></span> | `-key1 value1`  |
| <span data-ttu-id="31bbf-286">대시 두 개 (`--`)</span><span class="sxs-lookup"><span data-stu-id="31bbf-286">Two dashes (`--`)</span></span>        | `--key2 value2` |
| <span data-ttu-id="31bbf-287">슬래시 (`/`)</span><span class="sxs-lookup"><span data-stu-id="31bbf-287">Forward slash (`/`)</span></span>      | `/key3 value3`  |

<span data-ttu-id="31bbf-288">&#8224; 단일 대시 접두사를 사용 하 여 키 (`-`) 제공 되어야 [매핑 전환](#switch-mappings), 아래에서 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-288">&#8224;A key with a single dash prefix (`-`) must be provided in [switch mappings](#switch-mappings), described below.</span></span>

<span data-ttu-id="31bbf-289">예제 명령입니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-289">Example command:</span></span>

```console
dotnet run -key1 value1 --key2 value2 /key3 value3
```

<span data-ttu-id="31bbf-290">참고: 경우 `-key1` 에 존재 하지는 [매핑 전환](#switch-mappings) 구성 공급자에 게 제공는 `FormatException` throw 됩니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-290">Note: If `-key1` isn't present in the [switch mappings](#switch-mappings) given to the configuration provider, a `FormatException` is thrown.</span></span>

### <a name="duplicate-keys"></a><span data-ttu-id="31bbf-291">중복 키</span><span class="sxs-lookup"><span data-stu-id="31bbf-291">Duplicate keys</span></span>

<span data-ttu-id="31bbf-292">중복 키가 제공 하는 경우 마지막 키-값 쌍이 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-292">If duplicate keys are provided, the last key-value pair is used.</span></span>

### <a name="switch-mappings"></a><span data-ttu-id="31bbf-293">스위치 매핑</span><span class="sxs-lookup"><span data-stu-id="31bbf-293">Switch mappings</span></span>

<span data-ttu-id="31bbf-294">사용 하 여 구성을 수동으로 작성할 때 `ConfigurationBuilder`, 스위치 매핑 사전에 선택적으로 제공할 수는 `AddCommandLine` 메서드.</span><span class="sxs-lookup"><span data-stu-id="31bbf-294">When manually building configuration with `ConfigurationBuilder`, you can optionally provide a switch mappings dictionary to the `AddCommandLine` method.</span></span> <span data-ttu-id="31bbf-295">스위치 매핑을 통해 키 이름을 교체 논리를 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-295">Switch mappings allow you to provide key name replacement logic.</span></span>

<span data-ttu-id="31bbf-296">스위치 매핑 사전을 사용 되는 경우 명령줄 인수에 의해 제공 된 키와 일치 하는 키에 대 한 사전을 확인 됩니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-296">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="31bbf-297">명령줄 키가 사전에 없으면 사전 값 (키 교체) 구성을 설정 하려면 다시 전달 됩니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-297">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the configuration.</span></span> <span data-ttu-id="31bbf-298">스위치 매핑이 단일에 대시가 접두사로 명령줄 키 필요 (`-`).</span><span class="sxs-lookup"><span data-stu-id="31bbf-298">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="31bbf-299">매핑 사전 키 규칙을 전환 합니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-299">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="31bbf-300">스위치 대시로 시작 해야 합니다 (`-`) 나 더블 대시 (`--`).</span><span class="sxs-lookup"><span data-stu-id="31bbf-300">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="31bbf-301">스위치 매핑 사전에 중복 키를 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-301">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="31bbf-302">다음 예제에서는 `GetSwitchMappings` 메서드를 사용 하면 단일 대시를 사용 하 여 명령줄 인수 (`-`) 접두사를 입력 하 고 선행 하위 키 접두사를 방지 합니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-302">In the following example, the `GetSwitchMappings` method allows your command-line arguments to use a single dash (`-`) key prefix and avoid leading subkey prefixes.</span></span>

[!code-csharp[Main](configuration/sample/CommandLine/Program.cs?highlight=10-19,32)]

<span data-ttu-id="31bbf-303">사전 제공 된 명령줄 인수를 제공 하지 않고 `AddInMemoryCollection` 구성 값을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-303">Without providing command-line arguments, the dictionary provided to `AddInMemoryCollection` sets the configuration values.</span></span> <span data-ttu-id="31bbf-304">다음 명령을 사용 하 여 응용 프로그램을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-304">Run the app with the following command:</span></span>

```console
dotnet run
```

<span data-ttu-id="31bbf-305">콘솔 창에 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-305">The console window displays:</span></span>

```console
MachineName: RickPC
Left: 1980
```

<span data-ttu-id="31bbf-306">구성 설정에 전달 하려면 다음을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-306">Use the following to pass in configuration settings:</span></span>

```console
dotnet run /Profile:MachineName=DahliaPC /App:MainWindow:Left=1984
```

<span data-ttu-id="31bbf-307">콘솔 창에 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-307">The console window displays:</span></span>

```console
MachineName: DahliaPC
Left: 1984
```

<span data-ttu-id="31bbf-308">스위치 매핑 사전을 만든 후 다음 표에 표시 되는 데이터를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-308">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="31bbf-309">Key</span><span class="sxs-lookup"><span data-stu-id="31bbf-309">Key</span></span>            | <span data-ttu-id="31bbf-310">값</span><span class="sxs-lookup"><span data-stu-id="31bbf-310">Value</span></span>                 |
| -------------- | --------------------- |
| `-MachineName` | `Profile:MachineName` |
| `-Left`        | `App:MainWindow:Left` |

<span data-ttu-id="31bbf-311">키 전환 사전을 사용을 보여 주기 위해 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-311">To demonstrate key switching using the dictionary, run the following command:</span></span>

```console
dotnet run -MachineName=ChadPC -Left=1988
```

<span data-ttu-id="31bbf-312">명령줄 키 바뀝니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-312">The command-line keys are swapped.</span></span> <span data-ttu-id="31bbf-313">콘솔 창에 대 한 구성 값을 표시 `Profile:MachineName` 및 `App:MainWindow:Left`:</span><span class="sxs-lookup"><span data-stu-id="31bbf-313">The console window displays the configuration values for `Profile:MachineName` and `App:MainWindow:Left`:</span></span>

```console
MachineName: ChadPC
Left: 1988
```

## <a name="the-webconfig-file"></a><span data-ttu-id="31bbf-314">Web.config 파일</span><span class="sxs-lookup"><span data-stu-id="31bbf-314">The web.config file</span></span>

<span data-ttu-id="31bbf-315">A *web.config* 파일은 IIS 또는 IIS Express에서 응용 프로그램을 호스팅하는 경우에 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-315">A *web.config* file is required when you host the app in IIS or IIS-Express.</span></span> <span data-ttu-id="31bbf-316">*web.config* AspNetCoreModule IIS에서 앱을 시작 하도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-316">*web.config* turns on the AspNetCoreModule in IIS to launch your app.</span></span> <span data-ttu-id="31bbf-317">설정 *web.config* AspNetCoreModule iis 응용 프로그램을 시작 하 고 다른 IIS 설정 및 모듈 구성에 사용 하도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-317">Settings in *web.config* enable the AspNetCoreModule in IIS to launch your app and configure other IIS settings and modules.</span></span> <span data-ttu-id="31bbf-318">Visual Studio를 사용 하는 경우 삭제 *web.config*, Visual Studio는 새 이름을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-318">If you are using Visual Studio and delete *web.config*, Visual Studio will create a new one.</span></span>

## <a name="additional-notes"></a><span data-ttu-id="31bbf-319">추가 참고 사항</span><span class="sxs-lookup"><span data-stu-id="31bbf-319">Additional notes</span></span>

* <span data-ttu-id="31bbf-320">DI (dependency Injection) 후 까지의 설정 되지 않은 `ConfigureServices` 가 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-320">Dependency Injection (DI) is not set up until after `ConfigureServices` is invoked.</span></span>
* <span data-ttu-id="31bbf-321">구성 시스템이 DI 인식 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-321">The configuration system is not DI aware.</span></span>
* <span data-ttu-id="31bbf-322">`IConfiguration`두 가지 특수화에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-322">`IConfiguration` has two specializations:</span></span>
  * <span data-ttu-id="31bbf-323">`IConfigurationRoot`루트 노드에 대해 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-323">`IConfigurationRoot`  Used for the root node.</span></span> <span data-ttu-id="31bbf-324">다시 로드를 트리거할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-324">Can trigger a reload.</span></span>
  * <span data-ttu-id="31bbf-325">`IConfigurationSection`구성 값의 섹션을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-325">`IConfigurationSection`  Represents a section of configuration values.</span></span> <span data-ttu-id="31bbf-326">`GetSection` 및 `GetChildren` 메서드는 반환 된 `IConfigurationSection`합니다.</span><span class="sxs-lookup"><span data-stu-id="31bbf-326">The `GetSection` and `GetChildren` methods return an `IConfigurationSection`.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="31bbf-327">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="31bbf-327">Additional resources</span></span>

* [<span data-ttu-id="31bbf-328">여러 환경 사용</span><span class="sxs-lookup"><span data-stu-id="31bbf-328">Working with Multiple Environments</span></span>](environments.md)
* [<span data-ttu-id="31bbf-329">개발 중 안전한 앱 비밀 저장소</span><span class="sxs-lookup"><span data-stu-id="31bbf-329">Safe storage of app secrets during development</span></span>](../security/app-secrets.md)
* [<span data-ttu-id="31bbf-330">ASP.NET Core에서 호스팅</span><span class="sxs-lookup"><span data-stu-id="31bbf-330">Hosting in ASP.NET Core</span></span>](xref:fundamentals/hosting)
* [<span data-ttu-id="31bbf-331">종속성 주입</span><span class="sxs-lookup"><span data-stu-id="31bbf-331">Dependency Injection</span></span>](dependency-injection.md)
* [<span data-ttu-id="31bbf-332">Azure Key Vault 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="31bbf-332">Azure Key Vault configuration provider</span></span>](xref:security/key-vault-configuration)
