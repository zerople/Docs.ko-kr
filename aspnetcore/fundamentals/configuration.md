---
title: "ASP.NET Core에 대 한 구성"
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
ms.openlocfilehash: a14bc7fbcdac9acddfdab4fcd6e40385ca48bcc4
ms.sourcegitcommit: 9cdbfd0d670d70b9c354216aabee260c52dad5ee
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/12/2017
---
<a name=fundamentals-configuration></a>

  # <a name="configuration-in-aspnet-core"></a><span data-ttu-id="7e6c7-104">ASP.NET Core에 대 한 구성</span><span class="sxs-lookup"><span data-stu-id="7e6c7-104">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="7e6c7-105">[Rick Anderson](https://twitter.com/RickAndMSFT), [표시 Michaelis](http://intellitect.com/author/mark-michaelis/), [Steve Smith](https://ardalis.com/), 및 [김 Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="7e6c7-105">[Rick Anderson](https://twitter.com/RickAndMSFT), [Mark Michaelis](http://intellitect.com/author/mark-michaelis/), [Steve Smith](https://ardalis.com/), and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="7e6c7-106">구성 API 앱 이름-값 쌍의 목록에 따라 구성 하는 방법을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6c7-106">The Configuration API provides a way of configuring an app based on a list of name-value pairs.</span></span> <span data-ttu-id="7e6c7-107">여러 원본에서 런타임에 구성 읽기입니다.</span><span class="sxs-lookup"><span data-stu-id="7e6c7-107">Configuration is read at runtime from multiple sources.</span></span> <span data-ttu-id="7e6c7-108">여러 수준 계층에 이름-값 쌍을 그룹화 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e6c7-108">The name-value pairs can be grouped into a multi-level hierarchy.</span></span> <span data-ttu-id="7e6c7-109">에 대 한 구성 공급자가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e6c7-109">There are configuration providers for:</span></span>

* <span data-ttu-id="7e6c7-110">파일 형식 (INI, JSON 및 XML)</span><span class="sxs-lookup"><span data-stu-id="7e6c7-110">File formats (INI, JSON, and XML)</span></span>
* <span data-ttu-id="7e6c7-111">명령줄 인수</span><span class="sxs-lookup"><span data-stu-id="7e6c7-111">Command-line arguments</span></span>
* <span data-ttu-id="7e6c7-112">환경 변수</span><span class="sxs-lookup"><span data-stu-id="7e6c7-112">Environment variables</span></span>
* <span data-ttu-id="7e6c7-113">메모리 내.NET 개체</span><span class="sxs-lookup"><span data-stu-id="7e6c7-113">In-memory .NET objects</span></span>
* <span data-ttu-id="7e6c7-114">암호화 된 사용자 저장소</span><span class="sxs-lookup"><span data-stu-id="7e6c7-114">An encrypted user store</span></span>
* [<span data-ttu-id="7e6c7-115">Azure 주요 자격 증명 모음</span><span class="sxs-lookup"><span data-stu-id="7e6c7-115">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
* <span data-ttu-id="7e6c7-116">사용자 지정 공급자가 설치 또는 만들기</span><span class="sxs-lookup"><span data-stu-id="7e6c7-116">Custom providers, which you install or create</span></span>

<span data-ttu-id="7e6c7-117">각 구성 값 문자열 키에 매핑됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e6c7-117">Each configuration value maps to a string key.</span></span> <span data-ttu-id="7e6c7-118">사용자 지정 설정을 deserialize 하는 데 기본 바인딩인 지원은 [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) 개체 (속성이 있는 간단한.NET 클래스).</span><span class="sxs-lookup"><span data-stu-id="7e6c7-118">There's built-in binding support to deserialize settings into a custom [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) object (a simple .NET class with properties).</span></span>

[<span data-ttu-id="7e6c7-119">샘플 코드 보기 또는 다운로드</span><span class="sxs-lookup"><span data-stu-id="7e6c7-119">View or download sample code</span></span>](https://github.com/aspnet/docs/tree/master/aspnetcore/fundamentals/configuration/sample)

## <a name="simple-configuration"></a><span data-ttu-id="7e6c7-120">단순 구성</span><span class="sxs-lookup"><span data-stu-id="7e6c7-120">Simple configuration</span></span>

<span data-ttu-id="7e6c7-121">다음 콘솔 응용 프로그램의 JSON 구성 공급자를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6c7-121">The following console app uses the JSON configuration provider:</span></span>

<span data-ttu-id="7e6c7-122">[!code-csharp[Main](configuration/sample/ConfigJson/Program.cs)]</span><span class="sxs-lookup"><span data-stu-id="7e6c7-122">[!code-csharp[Main](configuration/sample/ConfigJson/Program.cs)]</span></span>

<span data-ttu-id="7e6c7-123">응용 프로그램을 읽고 다음 구성 설정을 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6c7-123">The app reads and displays the following configuration settings:</span></span>

<span data-ttu-id="7e6c7-124">[!code-json[Main](configuration/sample/ConfigJson/appsettings.json)]</span><span class="sxs-lookup"><span data-stu-id="7e6c7-124">[!code-json[Main](configuration/sample/ConfigJson/appsettings.json)]</span></span>

<span data-ttu-id="7e6c7-125">구성 노드는 콜론으로 구분 하는 이름-값 쌍의 계층적 목록으로 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6c7-125">Configuration consists of a hierarchical list of name-value pairs in which the nodes are separated by a colon.</span></span> <span data-ttu-id="7e6c7-126">액세스 값을 검색 하는 `Configuration` 인덱서를 해당 항목의 키:</span><span class="sxs-lookup"><span data-stu-id="7e6c7-126">To retrieve a value, access the `Configuration` indexer with the corresponding item's key:</span></span>

```csharp
Console.WriteLine($"option1 = {Configuration["subsection:suboption1"]}");
```

<span data-ttu-id="7e6c7-127">JSON 형식 구성 소스에서 배열 작업을 콜론으로 구분 된 문자열의 일부로 배열 인덱스를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6c7-127">To work with arrays in JSON-formatted configuration sources, use a array index as part of the colon-separated string.</span></span> <span data-ttu-id="7e6c7-128">다음 예제에서는 앞의 첫 번째 항목의 이름을 가져옵니다 `wizards` 배열:</span><span class="sxs-lookup"><span data-stu-id="7e6c7-128">The following example gets the name of the first item in the preceding `wizards` array:</span></span>

```csharp
Console.Write($"{Configuration["wizards:0:Name"]}, ");
```

<span data-ttu-id="7e6c7-129">하지만 기본 제공에 기록 하는 이름-값 쌍의 `Configuration` 공급자는 **하지** 유지 만들 수 있습니다 값을 저장 하는 사용자 지정 공급자입니다.</span><span class="sxs-lookup"><span data-stu-id="7e6c7-129">Name-value pairs written to the built in `Configuration` providers are **not** persisted, however, you can create a custom provider that saves values.</span></span> <span data-ttu-id="7e6c7-130">참조 [사용자 지정 구성 공급자](xref:fundamentals/configuration#custom-config-providers)합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6c7-130">See [custom configuration provider](xref:fundamentals/configuration#custom-config-providers).</span></span>

<span data-ttu-id="7e6c7-131">앞에 나온 샘플 값을 읽을 구성 인덱서를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6c7-131">The preceding sample uses the configuration indexer to read values.</span></span> <span data-ttu-id="7e6c7-132">외부 액세스 구성으로 `Startup`를 사용 하 여는 [옵션 패턴](xref:fundamentals/configuration#options-config-objects)합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6c7-132">To access configuration outside of `Startup`, use the [options pattern](xref:fundamentals/configuration#options-config-objects).</span></span> <span data-ttu-id="7e6c7-133">*옵션 패턴* 이 문서의 뒷부분에 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e6c7-133">The *options pattern* is shown later in this article.</span></span>

<span data-ttu-id="7e6c7-134">예를 들어, 개발, 테스트 및 프로덕션 환경에 대 한 서로 다른 구성 설정을 일반적이.</span><span class="sxs-lookup"><span data-stu-id="7e6c7-134">It's typical to have different configuration settings for different environments, for example, development, test, and production.</span></span> <span data-ttu-id="7e6c7-135">`CreateDefaultBuilder` ASP.NET Core 2.x 응용 프로그램의 확장 메서드 (또는 사용 하 여 `AddJsonFile` 및 `AddEnvironmentVariables` ASP.NET Core 1.x 응용 프로그램에서 직접) JSON 파일 및 시스템 구성 소스 읽기에 대 한 구성 공급자를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6c7-135">The `CreateDefaultBuilder` extension method in an ASP.NET Core 2.x app (or using `AddJsonFile` and `AddEnvironmentVariables` directly in an ASP.NET Core 1.x app) adds configuration providers for reading JSON files and system configuration sources:</span></span>

* <span data-ttu-id="7e6c7-136">*appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="7e6c7-136">*appsettings.json*</span></span>
* <span data-ttu-id="7e6c7-137">* appsettings 합니다. \<EnvironmentName >.json</span><span class="sxs-lookup"><span data-stu-id="7e6c7-137">*appsettings.\<EnvironmentName>.json</span></span>
* <span data-ttu-id="7e6c7-138">환경 변수</span><span class="sxs-lookup"><span data-stu-id="7e6c7-138">environment variables</span></span>

<span data-ttu-id="7e6c7-139">참조 [AddJsonFile](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.configuration.jsonconfigurationextensions) 에 대 한 설명은 매개 변수입니다.</span><span class="sxs-lookup"><span data-stu-id="7e6c7-139">See [AddJsonFile](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.configuration.jsonconfigurationextensions) for an explanation of the parameters.</span></span> <span data-ttu-id="7e6c7-140">`reloadOnChange`ASP.NET Core 1.1에서 이상 에서만 지원 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e6c7-140">`reloadOnChange` is only supported in ASP.NET Core 1.1 and higher.</span></span> 

<span data-ttu-id="7e6c7-141">지정 된 순서 구성 소스를 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="7e6c7-141">Configuration sources are read in the order they are specified.</span></span> <span data-ttu-id="7e6c7-142">위의 코드에서 환경 변수 마지막 읽혀집니다.</span><span class="sxs-lookup"><span data-stu-id="7e6c7-142">In the code above, the environment variables are read last.</span></span> <span data-ttu-id="7e6c7-143">두 이전 공급자에 설정 된 환경을 통해 설정 된 구성 값 바꾸어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6c7-143">Any configuration values set through the environment would replace those set in the two previous providers.</span></span>

<span data-ttu-id="7e6c7-144">환경 중 하나에 일반적으로 설정 되어 `Development`, `Staging`, 또는 `Production`합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6c7-144">The environment is typically set to one of `Development`, `Staging`, or `Production`.</span></span> <span data-ttu-id="7e6c7-145">참조 [여러 환경 작업](environments.md) 자세한 정보에 대 한 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6c7-145">See [Working with Multiple Environments](environments.md) for more information.</span></span>

<span data-ttu-id="7e6c7-146">구성 고려 사항:</span><span class="sxs-lookup"><span data-stu-id="7e6c7-146">Configuration considerations:</span></span>

* <span data-ttu-id="7e6c7-147">`IOptionsSnapshot`변경 시 구성 데이터를 다시 로드할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e6c7-147">`IOptionsSnapshot` can reload configuration data when it changes.</span></span> <span data-ttu-id="7e6c7-148">사용 하 여 `IOptionsSnapshot` 구성 데이터를 다시 로드 하는 경우.</span><span class="sxs-lookup"><span data-stu-id="7e6c7-148">Use `IOptionsSnapshot` if you need to reload configuration data.</span></span>  <span data-ttu-id="7e6c7-149">참조 [IOptionsSnapshot](#ioptionssnapshot) 자세한 정보에 대 한 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6c7-149">See [IOptionsSnapshot](#ioptionssnapshot) for more information.</span></span>
* <span data-ttu-id="7e6c7-150">구성 키 대/소문자를 구분 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6c7-150">Configuration keys are case insensitive.</span></span>
* <span data-ttu-id="7e6c7-151">가장 좋은 방법은 로컬 환경에서 배포 된 구성 파일을 설정 하는 아무 것도 재정의할 수 있도록 마지막으로, 환경 변수를 지정 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="7e6c7-151">A best practice is to specify environment variables last, so that the local environment can override anything set in deployed configuration files.</span></span>
* <span data-ttu-id="7e6c7-152">**하지** 구성 공급자 코드에 또는 일반 텍스트로 구성 파일에 암호 또는 기타 중요 한 데이터를 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6c7-152">**Never** store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span> <span data-ttu-id="7e6c7-153">테스트 환경 하거나 사용자가 개발에서 생산 암호를 사용 하지 마십시오.</span><span class="sxs-lookup"><span data-stu-id="7e6c7-153">Don't use production secrets in your development or test environments.</span></span> <span data-ttu-id="7e6c7-154">대신 저장소로 실수로 커밋된 없습니다 하므로 프로젝트 트리 외부 암호를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6c7-154">Instead, specify secrets outside the project tree, so they cannot be accidentally committed into your repository.</span></span> <span data-ttu-id="7e6c7-155">에 대 한 자세한 내용은 [여러 환경 작업](environments.md) 및 관리 [개발 하는 동안 앱 암호의 안전한 저장소](../security/app-secrets.md)합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6c7-155">Learn more about [Working with Multiple Environments](environments.md) and managing [safe storage of app secrets during development](../security/app-secrets.md).</span></span>
* <span data-ttu-id="7e6c7-156">경우 `:` 수 없습니다 시스템에서 환경 변수에서 사용 되는 대체 `:` 와 `__` (이중 밑줄).</span><span class="sxs-lookup"><span data-stu-id="7e6c7-156">If `:` cannot be used in environment variables in your system,  replace `:`  with `__` (double underscore).</span></span>

<a name=options-config-objects></a>

## <a name="using-options-and-configuration-objects"></a><span data-ttu-id="7e6c7-157">옵션 및 구성 개체를 사용 하 여</span><span class="sxs-lookup"><span data-stu-id="7e6c7-157">Using Options and configuration objects</span></span>

<span data-ttu-id="7e6c7-158">옵션 패턴을 관련된 설정 그룹을 나타내는 사용자 지정 옵션 클래스를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6c7-158">The options pattern uses custom options classes to represent a group of related settings.</span></span> <span data-ttu-id="7e6c7-159">앱 내에서 각 기능에 대해 분리 된 클래스를 만드는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="7e6c7-159">We recommended that you create decoupled classes for each feature within your app.</span></span> <span data-ttu-id="7e6c7-160">분리 된 클래스를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6c7-160">Decoupled classes follow:</span></span>

* <span data-ttu-id="7e6c7-161">[인터페이스 분리 원칙 (ISP)](http://deviq.com/interface-segregation-principle/) : 클래스를 사용 하는 구성 설정에 따라서만 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6c7-161">The [Interface Segregation Principle (ISP)](http://deviq.com/interface-segregation-principle/) : Classes depend only on the configuration settings they use.</span></span>
* <span data-ttu-id="7e6c7-162">[문제의 분리](http://deviq.com/separation-of-concerns/) : 응용 프로그램의 다른 부분에 대 한 설정을 종속 또는 서로 결합 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6c7-162">[Separation of Concerns](http://deviq.com/separation-of-concerns/) : Settings for different parts of your app are not dependent or coupled with one another.</span></span>

<span data-ttu-id="7e6c7-163">옵션 클래스는 추상 이어야 합니다. 매개 변수가 없는 public 생성자를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6c7-163">The options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="7e6c7-164">예:</span><span class="sxs-lookup"><span data-stu-id="7e6c7-164">For example:</span></span>

<span data-ttu-id="7e6c7-165">[!code-csharp[Main](configuration/sample/UsingOptions/Models/MyOptions.cs)]</span><span class="sxs-lookup"><span data-stu-id="7e6c7-165">[!code-csharp[Main](configuration/sample/UsingOptions/Models/MyOptions.cs)]</span></span>

<a name=options-example></a>

<span data-ttu-id="7e6c7-166">다음 코드에서 JSON 구성 공급자가 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e6c7-166">In the following code, the JSON configuration provider is enabled.</span></span> <span data-ttu-id="7e6c7-167">`MyOptions` 클래스는 서비스 컨테이너에 추가 되 고 구성에 연결 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6c7-167">The `MyOptions` class is added to the service container and bound to configuration.</span></span>

<span data-ttu-id="7e6c7-168">[!code-csharp[Main](configuration/sample/UsingOptions/Startup.cs?name=snippet1&highlight=8,20-21)]</span><span class="sxs-lookup"><span data-stu-id="7e6c7-168">[!code-csharp[Main](configuration/sample/UsingOptions/Startup.cs?name=snippet1&highlight=8,20-21)]</span></span>

<span data-ttu-id="7e6c7-169">다음 [컨트롤러](../mvc/controllers/index.md) 사용 하 여 [생성자 종속성 주입](xref:fundamentals/dependency-injection#what-is-dependency-injection) 에 [ `IOptions<TOptions>` ](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.options.ioptions-1) 설정에 액세스 하려면:</span><span class="sxs-lookup"><span data-stu-id="7e6c7-169">The following [controller](../mvc/controllers/index.md)  uses [constructor Dependency Injection](xref:fundamentals/dependency-injection#what-is-dependency-injection) on [`IOptions<TOptions>`](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.options.ioptions-1) to access settings:</span></span>

<span data-ttu-id="7e6c7-170">[!code-csharp[Main](configuration/sample/UsingOptions/Controllers/HomeController.cs?name=snippet1)]</span><span class="sxs-lookup"><span data-stu-id="7e6c7-170">[!code-csharp[Main](configuration/sample/UsingOptions/Controllers/HomeController.cs?name=snippet1)]</span></span>

<span data-ttu-id="7e6c7-171">다음으로 *appsettings.json* 파일:</span><span class="sxs-lookup"><span data-stu-id="7e6c7-171">With the following *appsettings.json* file:</span></span>

<span data-ttu-id="7e6c7-172">[!code-json[Main](configuration/sample/UsingOptions/appsettings1.json)]</span><span class="sxs-lookup"><span data-stu-id="7e6c7-172">[!code-json[Main](configuration/sample/UsingOptions/appsettings1.json)]</span></span>

<span data-ttu-id="7e6c7-173">`HomeController.Index` 메서드 반환 `option1 = value1_from_json, option2 = 2`합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6c7-173">The `HomeController.Index` method returns `option1 = value1_from_json, option2 = 2`.</span></span>

<span data-ttu-id="7e6c7-174">일반적인 앱 단일 옵션 파일에 전체 구성을 바인딩 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7e6c7-174">Typical apps won't bind the entire configuration to a single options file.</span></span> <span data-ttu-id="7e6c7-175">나중에 사용 하는 방법을 보여 드리 려 `GetSection` 섹션에 바인딩할 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6c7-175">Later on I'll show how to use `GetSection` to bind to a section.</span></span>

<span data-ttu-id="7e6c7-176">다음 코드에서 두 번째 `IConfigureOptions<TOptions>` 서비스를 서비스 컨테이너에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6c7-176">In the following code, a second `IConfigureOptions<TOptions>` service is added to the service container.</span></span> <span data-ttu-id="7e6c7-177">대리자를 사용 하 여 바인딩을 구성 `MyOptions`합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6c7-177">It uses a delegate to configure the binding with `MyOptions`.</span></span>

<span data-ttu-id="7e6c7-178">[!code-csharp[Main](configuration/sample/UsingOptions/Startup2.cs?name=snippet1&highlight=9-13)]</span><span class="sxs-lookup"><span data-stu-id="7e6c7-178">[!code-csharp[Main](configuration/sample/UsingOptions/Startup2.cs?name=snippet1&highlight=9-13)]</span></span>

<span data-ttu-id="7e6c7-179">여러 구성 공급자를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e6c7-179">You can add multiple configuration providers.</span></span> <span data-ttu-id="7e6c7-180">구성 공급자는 NuGet 패키지에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e6c7-180">Configuration providers are available in NuGet packages.</span></span> <span data-ttu-id="7e6c7-181">등록 된 순서에 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e6c7-181">They are applied in order they are registered.</span></span>

<span data-ttu-id="7e6c7-182">호출할 때마다 `Configure<TOptions>` 추가 `IConfigureOptions<TOptions>` 서비스를 서비스 컨테이너입니다.</span><span class="sxs-lookup"><span data-stu-id="7e6c7-182">Each call to `Configure<TOptions>` adds an `IConfigureOptions<TOptions>` service to the service container.</span></span> <span data-ttu-id="7e6c7-183">위의 예제에서는 값 `Option1` 및 `Option2` 에 지정 된 *appsettings.json* -하지만의 값 `Option1` 구성 된 대리자가 재정의 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e6c7-183">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json* -- but the value of `Option1` is overridden by the configured delegate.</span></span> 

<span data-ttu-id="7e6c7-184">둘 이상의 구성 서비스를 사용 하는 경우 마지막 구성 소스 지정 "wins" (구성 값을 설정).</span><span class="sxs-lookup"><span data-stu-id="7e6c7-184">When more than one configuration service is enabled, the last configuration source specified "wins" (sets the configuration value).</span></span> <span data-ttu-id="7e6c7-185">위의 코드는 `HomeController.Index` 메서드 반환 `option1 = value1_from_action, option2 = 2`합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6c7-185">In the preceding code, the `HomeController.Index` method returns `option1 = value1_from_action, option2 = 2`.</span></span>

<span data-ttu-id="7e6c7-186">각 속성에 옵션 유형 폼의 구성 키에 바인딩된 구성 옵션을 바인딩할 때 `property[:sub-property:]`합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6c7-186">When you bind options to configuration, each property in your options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="7e6c7-187">예를 들어는 `MyOptions.Option1` 속성의 키에 바인딩된 `Option1`에서 읽음는 `option1` 속성 *appsettings.json*합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6c7-187">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span> <span data-ttu-id="7e6c7-188">하위 속성 샘플은이 문서의 뒷부분에 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e6c7-188">A sub-property sample is shown later in this article.</span></span>

<span data-ttu-id="7e6c7-189">다음 코드에서는 세 번째 `IConfigureOptions<TOptions>` 서비스를 서비스 컨테이너에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6c7-189">In the following code, a third `IConfigureOptions<TOptions>` service is added to the service container.</span></span> <span data-ttu-id="7e6c7-190">바인딩할 `MySubOptions` 섹션 `subsection` 의 *appsettings.json* 파일:</span><span class="sxs-lookup"><span data-stu-id="7e6c7-190">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

<span data-ttu-id="7e6c7-191">[!code-csharp[Main](configuration/sample/UsingOptions/Startup3.cs?name=snippet1&highlight=16-17)]</span><span class="sxs-lookup"><span data-stu-id="7e6c7-191">[!code-csharp[Main](configuration/sample/UsingOptions/Startup3.cs?name=snippet1&highlight=16-17)]</span></span>

<span data-ttu-id="7e6c7-192">참고:이 확장 방법을 사용 하려면는 `Microsoft.Extensions.Options.ConfigurationExtensions` NuGet 패키지 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6c7-192">Note: This extension method requires the `Microsoft.Extensions.Options.ConfigurationExtensions` NuGet package.</span></span>

<span data-ttu-id="7e6c7-193">다음을 사용 하 여 *appsettings.json* 파일:</span><span class="sxs-lookup"><span data-stu-id="7e6c7-193">Using the following *appsettings.json* file:</span></span>

<span data-ttu-id="7e6c7-194">[!code-json[Main](configuration/sample/UsingOptions/appsettings.json)]</span><span class="sxs-lookup"><span data-stu-id="7e6c7-194">[!code-json[Main](configuration/sample/UsingOptions/appsettings.json)]</span></span>

<span data-ttu-id="7e6c7-195">`MySubOptions` 클래스:</span><span class="sxs-lookup"><span data-stu-id="7e6c7-195">The `MySubOptions` class:</span></span>

<span data-ttu-id="7e6c7-196">[!code-csharp[Main](configuration/sample/UsingOptions/Models/MySubOptions.cs?name=snippet1)]</span><span class="sxs-lookup"><span data-stu-id="7e6c7-196">[!code-csharp[Main](configuration/sample/UsingOptions/Models/MySubOptions.cs?name=snippet1)]</span></span>

<span data-ttu-id="7e6c7-197">다음으로 `Controller`:</span><span class="sxs-lookup"><span data-stu-id="7e6c7-197">With the following `Controller`:</span></span>

<span data-ttu-id="7e6c7-198">[!code-csharp[Main](configuration/sample/UsingOptions/Controllers/HomeController2.cs?name=snippet1)]</span><span class="sxs-lookup"><span data-stu-id="7e6c7-198">[!code-csharp[Main](configuration/sample/UsingOptions/Controllers/HomeController2.cs?name=snippet1)]</span></span>

<span data-ttu-id="7e6c7-199">`subOption1 = subvalue1_from_json, subOption2 = 200`이 반환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e6c7-199">`subOption1 = subvalue1_from_json, subOption2 = 200` is returned.</span></span>

<span data-ttu-id="7e6c7-200">옵션을 보기 모델에 제공 하거나 삽입할 수도 `IOptions<TOptions>` 보기에 직접:</span><span class="sxs-lookup"><span data-stu-id="7e6c7-200">You can also supply options in a view model or inject `IOptions<TOptions>` directly into a view:</span></span>

<span data-ttu-id="7e6c7-201">[!code-html[Main](configuration/sample/UsingOptions/Views/Home/Index.cshtml?highlight=3-4,16-17,20-21)]</span><span class="sxs-lookup"><span data-stu-id="7e6c7-201">[!code-html[Main](configuration/sample/UsingOptions/Views/Home/Index.cshtml?highlight=3-4,16-17,20-21)]</span></span>

<a name=in-memory-provider></a>

## <a name="ioptionssnapshot"></a><span data-ttu-id="7e6c7-202">IOptionsSnapshot</span><span class="sxs-lookup"><span data-stu-id="7e6c7-202">IOptionsSnapshot</span></span>

<span data-ttu-id="7e6c7-203">*ASP.NET Core 1.1 이상이 필요합니다.*</span><span class="sxs-lookup"><span data-stu-id="7e6c7-203">*Requires ASP.NET Core 1.1 or higher.*</span></span>

<span data-ttu-id="7e6c7-204">`IOptionsSnapshot`구성 파일 변경 될 때 구성 데이터를 다시 로드를 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6c7-204">`IOptionsSnapshot` supports reloading configuration data when the configuration file has changed.</span></span> <span data-ttu-id="7e6c7-205">오버 헤드가 최소로 유지가 된 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e6c7-205">It has minimal overhead.</span></span> <span data-ttu-id="7e6c7-206">사용 하 여 `IOptionsSnapshot` 와 `reloadOnChange: true`, 옵션에 바인딩된 `IConfiguration` 변경 될 때 다시 로드 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6c7-206">Using `IOptionsSnapshot` with `reloadOnChange: true`, the options are bound to `IConfiguration` and reloaded when changed.</span></span>

<span data-ttu-id="7e6c7-207">다음 샘플에는 새로운 방법을 보여 줍니다 `IOptionsSnapshot` 후에 만들어진 *config.json* 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6c7-207">The following sample demonstrates how a new `IOptionsSnapshot` is created after *config.json* changes.</span></span> <span data-ttu-id="7e6c7-208">동일한 서버에 요청을 반환 합니다 시간 *config.json* 가 **하지** 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6c7-208">Requests to the server will return the same time when *config.json* has **not** changed.</span></span> <span data-ttu-id="7e6c7-209">첫 번째 요청 이후 *config.json* 변경 시간을 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6c7-209">The first request after *config.json* changes will show a new time.</span></span>

<span data-ttu-id="7e6c7-210">[!code-csharp[Main](configuration/sample/IOptionsSnapshot2/Startup.cs?name=snippet1&highlight=1-9,13-18,32,33,52,53)]</span><span class="sxs-lookup"><span data-stu-id="7e6c7-210">[!code-csharp[Main](configuration/sample/IOptionsSnapshot2/Startup.cs?name=snippet1&highlight=1-9,13-18,32,33,52,53)]</span></span>

<span data-ttu-id="7e6c7-211">다음 그림에서는 서버 출력을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="7e6c7-211">The following image shows the server output:</span></span>

![이미지 표시 된 브라우저 "마지막 업데이트: 2016 년 11 월 22 일 오후 4시 43분"](configuration/_static/first.png)

<span data-ttu-id="7e6c7-213">메시지 값 또는 표시 된 시간이 변경 되지 않습니다 브라우저 새로 고침 (때 *config.json* 변경 되지 않은).</span><span class="sxs-lookup"><span data-stu-id="7e6c7-213">Refreshing the browser doesn't change the message value or time displayed (when *config.json* has not changed).</span></span>

<span data-ttu-id="7e6c7-214">변경 하 고 저장 된 *config.json* 후 브라우저를 새로 고칩니다.</span><span class="sxs-lookup"><span data-stu-id="7e6c7-214">Change and save the  *config.json* and then refresh the browser:</span></span>

![이미지 표시 된 브라우저 "마지막으로 업데이트를 e: 2016 년 11 월 22 일 오후 4시 53분"](configuration/_static/change.png)

## <a name="in-memory-provider-and-binding-to-a-poco-class"></a><span data-ttu-id="7e6c7-216">메모리 내 공급자 및 POCO 클래스에 바인딩</span><span class="sxs-lookup"><span data-stu-id="7e6c7-216">In-memory provider and binding to a POCO class</span></span>

<span data-ttu-id="7e6c7-217">다음 샘플에는 클래스에 바인딩하고 메모리 내 공급자를 사용 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="7e6c7-217">The following sample shows how to use the in-memory provider and bind to a class:</span></span>

<span data-ttu-id="7e6c7-218">[!code-csharp[Main](configuration/sample/InMemory/Program.cs)]</span><span class="sxs-lookup"><span data-stu-id="7e6c7-218">[!code-csharp[Main](configuration/sample/InMemory/Program.cs)]</span></span>

<span data-ttu-id="7e6c7-219">구성 값을 문자열로 반환 됩니다 있지만 바인딩 개체의 생성을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6c7-219">Configuration values are returned as strings, but binding enables the construction of objects.</span></span> <span data-ttu-id="7e6c7-220">바인딩은 POCO 개체 또는 전체 개체 그래프를 검색할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e6c7-220">Binding allows you to retrieve POCO objects or even entire object graphs.</span></span> <span data-ttu-id="7e6c7-221">다음 샘플에서는에 바인딩하는 방법을 보여 줍니다. `MyWindow` ASP.NET Core MVC 응용 프로그램으로 옵션 패턴을 사용 하 고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e6c7-221">The following sample shows how to bind to `MyWindow` and use the options pattern with a ASP.NET Core MVC app:</span></span>

<span data-ttu-id="7e6c7-222">[!code-csharp[Main](configuration/sample/WebConfigBind/MyWindow.cs)]</span><span class="sxs-lookup"><span data-stu-id="7e6c7-222">[!code-csharp[Main](configuration/sample/WebConfigBind/MyWindow.cs)]</span></span>

<span data-ttu-id="7e6c7-223">[!code-json[Main](configuration/sample/WebConfigBind/appsettings.json)]</span><span class="sxs-lookup"><span data-stu-id="7e6c7-223">[!code-json[Main](configuration/sample/WebConfigBind/appsettings.json)]</span></span>

<span data-ttu-id="7e6c7-224">사용자 지정 클래스 바인딩 `ConfigureServices` 호스트를 작성할 때:</span><span class="sxs-lookup"><span data-stu-id="7e6c7-224">Bind the custom class in `ConfigureServices` when building the host:</span></span>

<span data-ttu-id="7e6c7-225">[!code-csharp[Main](configuration/sample/WebConfigBind/Program.cs?name=snippet1&highlight=3-4)]</span><span class="sxs-lookup"><span data-stu-id="7e6c7-225">[!code-csharp[Main](configuration/sample/WebConfigBind/Program.cs?name=snippet1&highlight=3-4)]</span></span>

<span data-ttu-id="7e6c7-226">설정을 표시는 `HomeController`:</span><span class="sxs-lookup"><span data-stu-id="7e6c7-226">Display the settings from the `HomeController`:</span></span>

<span data-ttu-id="7e6c7-227">[!code-csharp[Main](configuration/sample/WebConfigBind/Controllers/HomeController.cs)]</span><span class="sxs-lookup"><span data-stu-id="7e6c7-227">[!code-csharp[Main](configuration/sample/WebConfigBind/Controllers/HomeController.cs)]</span></span>

### <a name="getvalue"></a><span data-ttu-id="7e6c7-228">GetValue</span><span class="sxs-lookup"><span data-stu-id="7e6c7-228">GetValue</span></span>

<span data-ttu-id="7e6c7-229">다음 샘플을 참조 하십시오.는 [GetValue<T> ](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.configuration.configurationbinder#Microsoft_Extensions_Configuration_ConfigurationBinder_GetValue_Microsoft_Extensions_Configuration_IConfiguration_System_Type_System_String_System_Object_) 확장 메서드:</span><span class="sxs-lookup"><span data-stu-id="7e6c7-229">The following sample demonstrates the [GetValue<T>](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.configuration.configurationbinder#Microsoft_Extensions_Configuration_ConfigurationBinder_GetValue_Microsoft_Extensions_Configuration_IConfiguration_System_Type_System_String_System_Object_) extension method:</span></span>

<span data-ttu-id="7e6c7-230">[!code-csharp[Main](configuration/sample/InMemoryGetValue/Program.cs?highlight=27-29)]</span><span class="sxs-lookup"><span data-stu-id="7e6c7-230">[!code-csharp[Main](configuration/sample/InMemoryGetValue/Program.cs?highlight=27-29)]</span></span>

<span data-ttu-id="7e6c7-231">ConfigurationBinder `GetValue<T>` 메서드 기본값 (이 샘플에는 80)을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e6c7-231">The ConfigurationBinder's `GetValue<T>` method allows you to specify a default value (80 in the sample).</span></span> <span data-ttu-id="7e6c7-232">`GetValue<T>`간단한 시나리오 되며 전체 섹션에 바인딩되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7e6c7-232">`GetValue<T>` is for simple scenarios and does not bind to entire sections.</span></span> <span data-ttu-id="7e6c7-233">`GetValue<T>`스칼라 값을 가져옵니다 `GetSection(key).Value` 특정 형식으로 변환 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6c7-233">`GetValue<T>` gets scalar values from `GetSection(key).Value` converted to a specific type.</span></span>

## <a name="binding-to-an-object-graph"></a><span data-ttu-id="7e6c7-234">개체 그래프에 바인딩</span><span class="sxs-lookup"><span data-stu-id="7e6c7-234">Binding to an object graph</span></span>

<span data-ttu-id="7e6c7-235">클래스의 각 개체를 재귀적으로 바인딩할을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e6c7-235">You can recursively bind to each object in a class.</span></span> <span data-ttu-id="7e6c7-236">다음 사항을 고려 `AppOptions` 클래스:</span><span class="sxs-lookup"><span data-stu-id="7e6c7-236">Consider the following `AppOptions` class:</span></span>

<span data-ttu-id="7e6c7-237">[!code-csharp[Main](configuration/sample/ObjectGraph/AppOptions.cs)]</span><span class="sxs-lookup"><span data-stu-id="7e6c7-237">[!code-csharp[Main](configuration/sample/ObjectGraph/AppOptions.cs)]</span></span>

<span data-ttu-id="7e6c7-238">다음 샘플에 바인딩하는 `AppOptions` 클래스:</span><span class="sxs-lookup"><span data-stu-id="7e6c7-238">The following sample binds to the `AppOptions` class:</span></span>

<span data-ttu-id="7e6c7-239">[!code-csharp[Main](configuration/sample/ObjectGraph/Program.cs?highlight=15-16)]</span><span class="sxs-lookup"><span data-stu-id="7e6c7-239">[!code-csharp[Main](configuration/sample/ObjectGraph/Program.cs?highlight=15-16)]</span></span>

<span data-ttu-id="7e6c7-240">**ASP.NET Core 1.1** 및 이상을 사용 하 여 `Get<T>`, 전체 섹션와 함께 작업 하 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6c7-240">**ASP.NET Core 1.1** and higher can use  `Get<T>`, which works with entire sections.</span></span> <span data-ttu-id="7e6c7-241">`Get<T>`사용 하 여 보다 자세한 convienent 수 `Bind`합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6c7-241">`Get<T>` can be more convienent than using `Bind`.</span></span> <span data-ttu-id="7e6c7-242">다음 코드를 사용 하는 방법을 보여 줍니다 `Get<T>` 위의 샘플:</span><span class="sxs-lookup"><span data-stu-id="7e6c7-242">The following code shows how to use `Get<T>` with the sample above:</span></span>

```csharp
var appConfig = config.GetSection("App").Get<AppOptions>();
```

<span data-ttu-id="7e6c7-243">다음을 사용 하 여 *appsettings.json* 파일:</span><span class="sxs-lookup"><span data-stu-id="7e6c7-243">Using the following *appsettings.json* file:</span></span>

<span data-ttu-id="7e6c7-244">[!code-json[Main](configuration/sample/ObjectGraph/appsettings.json)]</span><span class="sxs-lookup"><span data-stu-id="7e6c7-244">[!code-json[Main](configuration/sample/ObjectGraph/appsettings.json)]</span></span>

<span data-ttu-id="7e6c7-245">프로그램은 표시 `Height 11`합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6c7-245">The program displays `Height 11`.</span></span>

<span data-ttu-id="7e6c7-246">다음 코드를 장치에 사용할 수 구성을 테스트 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6c7-246">The following code can be used to unit test the configuration:</span></span>

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

<a name=custom-config-providers></a>

## <a name="basic-sample-of-entity-framework-custom-provider"></a><span data-ttu-id="7e6c7-247">Entity Framework 사용자 지정 공급자의 기본 샘플</span><span class="sxs-lookup"><span data-stu-id="7e6c7-247">Basic sample of Entity Framework custom provider</span></span>

<span data-ttu-id="7e6c7-248">이 섹션에서는 EF를 사용 하 여 데이터베이스에서 이름-값 쌍을 읽을 수 있는 기본 구성 공급자가 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="7e6c7-248">In this section, a basic configuration provider that reads name-value pairs from a database using EF is created.</span></span> 

<span data-ttu-id="7e6c7-249">정의 `ConfigurationValue` 데이터베이스의 구성 값을 저장 하기 위한 엔터티:</span><span class="sxs-lookup"><span data-stu-id="7e6c7-249">Define a `ConfigurationValue` entity for storing configuration values in the database:</span></span>

<span data-ttu-id="7e6c7-250">[!code-csharp[Main](configuration/sample/CustomConfigurationProvider/ConfigurationValue.cs)]</span><span class="sxs-lookup"><span data-stu-id="7e6c7-250">[!code-csharp[Main](configuration/sample/CustomConfigurationProvider/ConfigurationValue.cs)]</span></span>

<span data-ttu-id="7e6c7-251">추가 `ConfigurationContext` 저장 하 고 구성된 된 값에 액세스 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6c7-251">Add a `ConfigurationContext` to store and access the configured values:</span></span>

<span data-ttu-id="7e6c7-252">[!code-csharp[Main](configuration/sample/CustomConfigurationProvider/ConfigurationContext.cs?name=snippet1)]</span><span class="sxs-lookup"><span data-stu-id="7e6c7-252">[!code-csharp[Main](configuration/sample/CustomConfigurationProvider/ConfigurationContext.cs?name=snippet1)]</span></span>

<span data-ttu-id="7e6c7-253">구현 하는 클래스를 만드는 [IConfigurationSource](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.configuration.iconfigurationsource):</span><span class="sxs-lookup"><span data-stu-id="7e6c7-253">Create an class that implements [IConfigurationSource](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.configuration.iconfigurationsource):</span></span>

<span data-ttu-id="7e6c7-254">[!code-csharp[Main](configuration/sample/CustomConfigurationProvider/EntityFrameworkConfigurationSource.cs?highlight=7)]</span><span class="sxs-lookup"><span data-stu-id="7e6c7-254">[!code-csharp[Main](configuration/sample/CustomConfigurationProvider/EntityFrameworkConfigurationSource.cs?highlight=7)]</span></span>

<span data-ttu-id="7e6c7-255">상속 하 여 사용자 지정 구성 공급자를 만들기 [ConfigurationProvider](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.configuration.configurationprovider)합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6c7-255">Create the custom configuration provider by inheriting from [ConfigurationProvider](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.configuration.configurationprovider).</span></span>  <span data-ttu-id="7e6c7-256">구성 공급자는 비어 있는 경우 데이터베이스를 초기화 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6c7-256">The configuration provider initializes the database when it's empty:</span></span>

<span data-ttu-id="7e6c7-257">[!code-csharp[Main](configuration/sample/CustomConfigurationProvider/EntityFrameworkConfigurationProvider.cs?highlight=9,18-31,38-39)]</span><span class="sxs-lookup"><span data-stu-id="7e6c7-257">[!code-csharp[Main](configuration/sample/CustomConfigurationProvider/EntityFrameworkConfigurationProvider.cs?highlight=9,18-31,38-39)]</span></span>

<span data-ttu-id="7e6c7-258">("Value_from_ef_1" 및 "value_from_ef_2")은 데이터베이스의 강조 표시 된 값은 샘플 실행 될 때 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e6c7-258">The highlighted values from the database ("value_from_ef_1" and "value_from_ef_2") are displayed when the sample is run.</span></span>

<span data-ttu-id="7e6c7-259">추가할 수는 `EFConfigSource` 구성 소스를 추가 하기 위한 확장 메서드:</span><span class="sxs-lookup"><span data-stu-id="7e6c7-259">You can add an `EFConfigSource` extension method for adding the configuration source:</span></span>

<span data-ttu-id="7e6c7-260">[!code-csharp[Main](configuration/sample/CustomConfigurationProvider/EntityFrameworkExtensions.cs?highlight=12)]</span><span class="sxs-lookup"><span data-stu-id="7e6c7-260">[!code-csharp[Main](configuration/sample/CustomConfigurationProvider/EntityFrameworkExtensions.cs?highlight=12)]</span></span>

<span data-ttu-id="7e6c7-261">다음 코드에서는 사용자 지정을 사용 하는 방법을 보여 줍니다. `EFConfigProvider`:</span><span class="sxs-lookup"><span data-stu-id="7e6c7-261">The following code shows how to use the custom `EFConfigProvider`:</span></span>

<span data-ttu-id="7e6c7-262">[!code-csharp[Main](configuration/sample/CustomConfigurationProvider/Program.cs?highlight=21-26)]</span><span class="sxs-lookup"><span data-stu-id="7e6c7-262">[!code-csharp[Main](configuration/sample/CustomConfigurationProvider/Program.cs?highlight=21-26)]</span></span>

<span data-ttu-id="7e6c7-263">사용자 지정을 추가 하는 샘플 참고 `EFConfigProvider` JSON 공급자 이므로 데이터베이스의 모든 설정의 설정을 재정의 합니다에서 *appsettings.json* 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="7e6c7-263">Note the sample adds the custom `EFConfigProvider` after the JSON provider, so any settings from the database will override settings from the *appsettings.json* file.</span></span>

<span data-ttu-id="7e6c7-264">다음을 사용 하 여 *appsettings.json* 파일:</span><span class="sxs-lookup"><span data-stu-id="7e6c7-264">Using the following *appsettings.json* file:</span></span>

<span data-ttu-id="7e6c7-265">[!code-json[Main](configuration/sample/CustomConfigurationProvider/appsettings.json)]</span><span class="sxs-lookup"><span data-stu-id="7e6c7-265">[!code-json[Main](configuration/sample/CustomConfigurationProvider/appsettings.json)]</span></span>

<span data-ttu-id="7e6c7-266">다음은 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e6c7-266">The following is displayed:</span></span>

```console
key1=value_from_ef_1
key2=value_from_ef_2
key3=value_from_json_3
```

## <a name="commandline-configuration-provider"></a><span data-ttu-id="7e6c7-267">명령줄 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="7e6c7-267">CommandLine configuration provider</span></span>

<span data-ttu-id="7e6c7-268">다음 샘플 마지막 CommandLine 구성 공급자를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6c7-268">The following sample enables the CommandLine configuration provider last:</span></span>

<span data-ttu-id="7e6c7-269">[!code-csharp[Main](configuration/sample/CommandLine/Program.cs)]</span><span class="sxs-lookup"><span data-stu-id="7e6c7-269">[!code-csharp[Main](configuration/sample/CommandLine/Program.cs)]</span></span>

<span data-ttu-id="7e6c7-270">구성 설정에 전달 하려면 다음을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6c7-270">Use the following to pass in configuration settings:</span></span>

```console
dotnet run /Profile:MachineName=Bob /App:MainWindow:Left=1234
```

<span data-ttu-id="7e6c7-271">표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6c7-271">Which displays:</span></span>

```console
Hello Bob
Left 1234
```

<span data-ttu-id="7e6c7-272">`GetSwitchMappings` 메서드를 사용 하면 `-` 대신 `/` 하 고 선행 하위 키 접두사를 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6c7-272">The `GetSwitchMappings` method allows you to use `-` rather than `/` and it strips the leading subkey prefixes.</span></span> <span data-ttu-id="7e6c7-273">예:</span><span class="sxs-lookup"><span data-stu-id="7e6c7-273">For example:</span></span>

```console
dotnet run -MachineName=Bob -Left=7734
```

<span data-ttu-id="7e6c7-274">표시:</span><span class="sxs-lookup"><span data-stu-id="7e6c7-274">Displays:</span></span>

```console
Hello Bob
Left 7734
```

<span data-ttu-id="7e6c7-275">명령줄 인수 (null 일 수) 값을 포함 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6c7-275">Command-line arguments must include a value (it can be null).</span></span> <span data-ttu-id="7e6c7-276">예:</span><span class="sxs-lookup"><span data-stu-id="7e6c7-276">For example:</span></span>

```console
dotnet run /Profile:MachineName=
```

<span data-ttu-id="7e6c7-277">정상 이지만</span><span class="sxs-lookup"><span data-stu-id="7e6c7-277">Is OK, but</span></span>

```console
dotnet run /Profile:MachineName
```

<span data-ttu-id="7e6c7-278">예외가 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6c7-278">results in an exception.</span></span> <span data-ttu-id="7e6c7-279">해당 스위치 매핑이 지원 되지 않습니다는 대 한-또는--의 명령줄 스위치 접두사를 지정 하는 경우 예외가 throw 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e6c7-279">An exception will be thrown if you specify a command-line switch prefix of - or -- for which there's no corresponding switch mapping.</span></span>

## <a name="the-webconfig-file"></a><span data-ttu-id="7e6c7-280">Web.config 파일</span><span class="sxs-lookup"><span data-stu-id="7e6c7-280">The web.config file</span></span>

<span data-ttu-id="7e6c7-281">A *web.config* 파일은 IIS 또는 IIS Express에서 응용 프로그램을 호스팅하는 경우에 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6c7-281">A *web.config* file is required when you host the app in IIS or IIS-Express.</span></span> <span data-ttu-id="7e6c7-282">*web.config* AspNetCoreModule IIS에서 앱을 시작 하도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6c7-282">*web.config* turns on the AspNetCoreModule in IIS to launch your app.</span></span> <span data-ttu-id="7e6c7-283">설정 *web.config* AspNetCoreModule iis 응용 프로그램을 시작 하 고 다른 IIS 설정 및 모듈 구성에 사용 하도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6c7-283">Settings in *web.config* enable the AspNetCoreModule in IIS to launch your app and configure other IIS settings and modules.</span></span> <span data-ttu-id="7e6c7-284">Visual Studio를 사용 하는 경우 삭제 *web.config*, Visual Studio는 새 이름을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7e6c7-284">If you are using Visual Studio and delete *web.config*, Visual Studio will create a new one.</span></span>

### <a name="additional-notes"></a><span data-ttu-id="7e6c7-285">추가 참고 사항</span><span class="sxs-lookup"><span data-stu-id="7e6c7-285">Additional notes</span></span>

* <span data-ttu-id="7e6c7-286">DI (dependency Injection) 후 까지의 설정 되지 않은 `ConfigureServices` 가 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e6c7-286">Dependency Injection (DI) is not set up until after `ConfigureServices` is invoked.</span></span>
* <span data-ttu-id="7e6c7-287">구성 시스템이 DI 인식 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7e6c7-287">The configuration system is not DI aware.</span></span>
* <span data-ttu-id="7e6c7-288">`IConfiguration`두 가지 특수화에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e6c7-288">`IConfiguration` has two specializations:</span></span>
  * <span data-ttu-id="7e6c7-289">`IConfigurationRoot`루트 노드에 대해 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6c7-289">`IConfigurationRoot`  Used for the root node.</span></span> <span data-ttu-id="7e6c7-290">다시 로드를 트리거할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e6c7-290">Can trigger a reload.</span></span>
  * <span data-ttu-id="7e6c7-291">`IConfigurationSection`구성 값의 섹션을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="7e6c7-291">`IConfigurationSection`  Represents a section of configuration values.</span></span> <span data-ttu-id="7e6c7-292">`GetSection` 및 `GetChildren` 메서드는 반환 된 `IConfigurationSection`합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6c7-292">The `GetSection` and `GetChildren` methods return an `IConfigurationSection`.</span></span>

### <a name="additional-resources"></a><span data-ttu-id="7e6c7-293">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="7e6c7-293">Additional resources</span></span>

* [<span data-ttu-id="7e6c7-294">여러 환경 사용</span><span class="sxs-lookup"><span data-stu-id="7e6c7-294">Working with Multiple Environments</span></span>](environments.md)
* [<span data-ttu-id="7e6c7-295">개발 중 안전한 앱 비밀 저장소</span><span class="sxs-lookup"><span data-stu-id="7e6c7-295">Safe storage of app secrets during development</span></span>](../security/app-secrets.md)
* [<span data-ttu-id="7e6c7-296">종속성 주입</span><span class="sxs-lookup"><span data-stu-id="7e6c7-296">Dependency Injection</span></span>](dependency-injection.md)
* [<span data-ttu-id="7e6c7-297">Azure Key Vault 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="7e6c7-297">Azure Key Vault configuration provider</span></span>](xref:security/key-vault-configuration)
