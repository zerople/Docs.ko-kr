---
title: "ASP.NET Core에서 호스팅"
author: guardrex
description: "ASP.NET Core 응용 프로그램 시작 및 수명 관리를 담당 하는 웹 호스트에 알아봅니다."
keywords: "ASP.NET Core 웹 IWebHost, WebHostBuilder, IHostingEnvironment, IApplicationLifetime 호스트"
ms.author: riande
manager: wpickett
ms.date: 09/10/2017
ms.topic: article
ms.assetid: 4e45311d-8d56-46e2-b99d-6f65b648a277
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/hosting
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4eb57cf80399abdb7c6d05546ea2b0d5718c56c3
ms.sourcegitcommit: 0a3f215b4f665afc6f2678642968eea698102346
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/18/2017
---
# <a name="hosting-in-aspnet-core"></a>ASP.NET Core에서 호스팅

으로 [Luke Latham](https://github.com/guardrex)

ASP.NET Core 응용 프로그램 구성 및 실행 한 *호스트*, 응용 프로그램 시작 및 수명 관리 담당 하는 합니다. 여기에 최소한 호스트 서버 및 요청 처리 파이프라인을 구성합니다.

## <a name="setting-up-a-host"></a>호스트 설정

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

인스턴스를 사용 하 여 호스트 만들기 [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder)합니다. 이 일반적으로 응용 프로그램의 진입점에서 수행 되어는 `Main` 메서드. 프로젝트 템플릿에서 `Main` 에 위치한 *Program.cs*합니다. 일반적인 *Program.cs* 호출 [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) 설정은 호스트를 시작 하려면:

[!code-csharp[Main](../common/samples/WebApplication1DotNetCore2.0App/Program.cs?name=snippet_Main)]

`CreateDefaultBuilder`다음 작업을 수행합니다.

* 구성 [Kestrel](servers/kestrel.md) 웹 서버로 합니다. Kestrel 기본 옵션을 참조 하십시오. [는 Kestrel 옵션 섹션에서 ASP.NET Core 웹 서버 구현 Kestrel](xref:fundamentals/servers/kestrel#kestrel-options)합니다.
* 설정 하는 콘텐츠 루트 [Directory.GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory)합니다.
* 선택적 구성에서 로드 합니다.
  * *appsettings.json*합니다.
  * *appsettings 합니다. {환경}.json*합니다.
  * [사용자의 비밀](xref:security/app-secrets) 응용 프로그램을 실행 하는 경우는 `Development` 환경입니다.
  * 환경 변수.
  * 명령줄 인수입니다.
* 구성 [로깅](xref:fundamentals/logging) 사용 하 여 콘솔 및 디버그 출력에 대 한 [로그 필터링](xref:fundamentals/logging#log-filtering) 의 로깅 구성 섹션에 지정 된 규칙은 *appsettings.json* 또는 *appsettings 합니다. {환경}.json* 파일입니다.
* 뒤에 IIS를 실행 하면 [IIS 통합](xref:publishing/iis) 기본 경로 포트를 구성 하 여 서버 수신 대기할 때 사용 하는 [ASP.NET Core 모듈](xref:fundamentals/servers/aspnet-core-module)합니다. 모듈은 IIS와 Kestrel 간의 역방향 프록시를 만듭니다. 또한 응용 프로그램을 구성 [시작 오류 캡처](#capture-startup-errors)합니다. IIS 기본 옵션을 참조 하십시오. [IIS의 IIS와 Windows에서 호스트 ASP.NET Core 섹션 옵션](xref:publishing/iis#iis-options)합니다.

*콘텐츠 루트* 호스트 MVC 뷰 파일과 같은 콘텐츠 파일을 검색 하는 위치를 결정 합니다. 기본 콘텐츠 루트는 [Directory.GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory)합니다. 이 인해 응용 프로그램 루트 폴더에서 시작 될 때 웹 프로젝트의 루트 폴더 콘텐츠 루트로 사용 하 여 (예를 들어 호출 [실행 dotnet](/dotnet/core/tools/dotnet-run) 프로젝트 폴더에서). 사용 되는 기본 이것이 [Visual Studio](https://www.visualstudio.com/) 및 [dotnet 새 템플릿을](/dotnet/core/tools/dotnet-new)합니다.

참조 [ASP.NET Core에서 구성을](xref:fundamentals/configuration) 응용 프로그램 구성에 대 한 자세한 내용은 합니다.

> [!NOTE]
> 정적을 사용 하는 대신 `CreateDefaultBuilder` 에서 호스트를 만드는 메서드를 [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) 은 지원 되는 ASP.NET Core 방법을 2.x 합니다. 자세한 내용은 ASP.NET Core 1.x 탭을 참조 하십시오.

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

인스턴스를 사용 하 여 호스트 만들기 [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder)합니다. 이 일반적으로 응용 프로그램의 진입점에서 수행 되어는 `Main` 메서드. 프로젝트 템플릿에서 `Main` 에 위치한 *Program.cs*합니다. 다음 *Program.cs* 사용 하는 방법을 보여 줍니다. `WebHostBuilder` 호스트 빌드하려면:

[!code-csharp[Main](../common/samples/WebApplication1/Program.cs)]

`WebHostBuilder`필요는 [IServer를 구현 하는 서버](servers/index.md)합니다. 기본 제공 서버는 [Kestrel](servers/kestrel.md) 및 [HTTP.sys](servers/httpsys.md) (HTTP.sys ASP.NET 코어 2.0이 출시 되기 전에 호출 된 [WebListener](xref:fundamentals/servers/weblistener)). 이 예제는 [UseKestrel 확장 메서드](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel?view=aspnetcore-1.1) Kestrel 서버를 지정 합니다.

*콘텐츠 루트* 호스트 MVC 뷰 파일과 같은 콘텐츠 파일을 검색 하는 위치를 결정 합니다. 에 제공 되는 기본 콘텐츠 루트 `UseContentRoot` 은 [Directory.GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory?view=netcore-1.1)합니다. 이 인해 응용 프로그램 루트 폴더에서 시작 될 때 웹 프로젝트의 루트 폴더 콘텐츠 루트로 사용 하 여 (예를 들어 호출 [실행 dotnet](/dotnet/core/tools/dotnet-run) 프로젝트 폴더에서). 사용 되는 기본 이것이 [Visual Studio](https://www.visualstudio.com/) 및 [dotnet 새 템플릿을](/dotnet/core/tools/dotnet-new)합니다.

IIS는 역방향 프록시를 사용 하려면 호출 [UseIISIntegration](/aspnet/core/api/microsoft.aspnetcore.hosting.webhostbuilderiisextensions) 호스트 빌드 과정의 일환으로 합니다. `UseIISIntegration`구성 하지 않는 한 *서버*처럼 [UseKestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel?view=aspnetcore-1.1) 않습니다. `UseIISIntegration`구성 요소의 기본 경로 및 사용 하는 경우 서버에서 수신 대기 해야 하는 포트는 [ASP.NET Core 모듈](xref:fundamentals/servers/aspnet-core-module) Kestrel와 IIS 사이 역방향 프록시를 만들 수 있습니다. IIS에서 ASP.NET Core를 사용 하려면 둘 다 지정 해야 하면 `UseKestrel` 및 `UseIISIntegration`합니다. `UseIISIntegration`IIS 또는 IIS Express 뒤에서 실행 하는 경우에 활성화 됩니다. 자세한 내용은 참조 [ASP.NET Core 모듈 소개](xref:fundamentals/servers/aspnet-core-module) 및 [ASP.NET Core 모듈 구성 참조](xref:hosting/aspnet-core-module)합니다.

호스트 (및 ASP.NET Core 응용 프로그램)를 구성 하는 최소 구현을 서버와 응용 프로그램의 요청 파이프라인의 구성을 지정 하는 포함 됩니다.

```csharp
var host = new WebHostBuilder()
    .UseKestrel()
    .Configure(app =>
    {
        app.Run(context => context.Response.WriteAsync("Hello World!"));
    })
    .Build();

host.Run();
```

---

제공할 수는 호스트를 설정할 때 [구성](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configure?view=aspnetcore-1.1) 및 [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.configureservices?view=aspnetcore-1.1) 메서드. 지정 하는 경우는 `Startup` 정의 해야 클래스는 `Configure` 메서드. 자세한 내용은 참조 [에서 ASP.NET Core 응용 프로그램 시작](startup.md)합니다. 여러 번 호출 `ConfigureServices` 서로에 추가 합니다. 여러 번 호출 `Configure` 또는 `UseStartup` 에 `WebHostBuilder` 이전 설정을 대체 합니다.

## <a name="host-configuration-values"></a>호스트 구성 값

[WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) 와 직접 설정 될 수 있는 호스트에 대 한 대부분의 사용 가능한 구성 값을 설정 하기 위한 메서드를 제공 [UseSetting](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.usesetting) 와 연결 된 키입니다. 사용 하 여 값을 설정할 때 `UseSetting`, 유형에 관계 없이 (따옴표로 묶음)을 문자열로 설정 됩니다.

### <a name="capture-startup-errors"></a>시작 오류 캡처

이 설정은 시작 오류의 캡처를 제어합니다.

**키**: captureStartupErrors  
**형식**: *bool* (`true` 또는 `1`)  
**기본**: 기본값으로 `false` Kestrel 며 기본값은 IIS 뒤에 있는 앱이 실행 하지 않는 한 `true`합니다.  
**사용 하 여 설정**:`CaptureStartupErrors`

때 `false`, 종료 하는 호스트에서 시작 결과 동안 오류가 발생 합니다. 때 `true`, 호스트 시작 하는 동안 예외를 캡처하고 서버를 시작 하려고 합니다.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

```csharp
WebHost.CreateDefaultBuilder(args)
    .CaptureStartupErrors(true)
    ...
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

```csharp
var host = new WebHostBuilder()
    .CaptureStartupErrors(true)
    ...
```

---

### <a name="content-root"></a>콘텐츠 루트

이 설정은 결정 ASP.NET Core MVC 뷰 등의 콘텐츠 파일을 검색 하기 시작 합니다. 

**키**: contentRoot  
**형식**: *문자열*  
**기본**: 응용 프로그램 어셈블리 파일이 있는 폴더를 기본값으로 합니다.  
**사용 하 여 설정**:`UseContentRoot`

에 대 한 기본 경로로 콘텐츠 루트는 또한는 [웹 루트 설정을](#web-root)합니다. 경로가 존재 하지 않는 경우 호스트가 시작 되지 않습니다.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\mywebsite")
    ...
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

```csharp
var host = new WebHostBuilder()
    .UseContentRoot("c:\\mywebsite")
    ...
```

---

### <a name="detailed-errors"></a>자세한 오류

오류를 캡처해야 하나요 자세히 설명 하는 경우 결정 합니다.

**키**: detailedErrors  
**형식**: *bool* (`true` 또는 `1`)  
**기본**: false  
**사용 하 여 설정**:`UseSetting`

사용 하는 경우 (또는 경우는 <a href="#environment">환경</a> 로 설정 되어 `Development`), 응용 프로그램 자세한 예외를 캡처합니다.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.DetailedErrorsKey, "true")
    ...
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

```csharp
var host = new WebHostBuilder()
    .UseSetting(WebHostDefaults.DetailedErrorsKey, "true")
    ...
```

---

### <a name="environment"></a>환경

앱의 환경을 설정합니다.

**키**: 환경  
**형식**: *문자열*  
**기본**: 프로덕션  
**사용 하 여 설정**:`UseEnvironment`

설정할 수 있습니다는 *환경* 값으로. 프레임 워크에서 정의 된 값 포함 `Development`, `Staging`, 및 `Production`합니다. 값에 대/소문자 구분 되지 않습니다. 기본적으로는 *환경* 에서 읽기는 `ASPNETCORE_ENVIRONMENT` 환경 변수입니다. 사용 하는 경우 [Visual Studio](https://www.visualstudio.com/), 환경 변수를 설정할 수 있습니다는 *launchSettings.json* 파일입니다. 자세한 내용은 [여러 환경 사용](xref:fundamentals/environments)을 참조하세요.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    ...
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

```csharp
var host = new WebHostBuilder()
    .UseEnvironment("Development")
    ...
```

---

### <a name="hosting-startup-assemblies"></a>호스팅 시작 어셈블리

응용 프로그램의 호스팅 시작 어셈블리를 설정합니다.

**키**: hostingStartupAssemblies  
**형식**: *문자열*  
**기본**: 빈 문자열  
**사용 하 여 설정**:`UseSetting`

호스팅 시작 로드할 어셈블리를 시작할 때의 세미콜론으로 구분 된 문자열입니다. 이 기능은 ASP.NET 코어 2.0의 새로운 기능입니다.

구성 기본값을 빈 문자열로 있지만 호스팅 시작 어셈블리는 항상 응용 프로그램의 어셈블리를 포함 합니다. 호스팅 시작 어셈블리를 제공 하면 해당 파일은 응용 프로그램 시작 하는 동안 일반적인 서비스를 빌드할 때 로드에 대 한 응용 프로그램의 어셈블리에 추가 됩니다.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2")
    ...
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

이 기능은 ASP.NET Core에서 사용할 수 없는 1.x 합니다.

---

### <a name="prefer-hosting-urls"></a>Url을 호스트 하는 것이 좋습니다.

호스트와 구성 된 Url을 수신 해야 하는지 여부를 나타냅니다는 `WebHostBuilder` 대신 사용 하 여 구성 하는 것은 `IServer` 구현 합니다.

**키**: preferHostingUrls  
**형식**: *bool* (`true` 또는 `1`)  
**기본**: true  
**사용 하 여 설정**:`PreferHostingUrls`

이 기능은 ASP.NET 코어 2.0의 새로운 기능입니다.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

```csharp
WebHost.CreateDefaultBuilder(args)
    .PreferHostingUrls(false)
    ...
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

이 기능은 ASP.NET Core에서 사용할 수 없는 1.x 합니다.

---

### <a name="prevent-hosting-startup"></a>호스팅 시작 방지

호스팅 시작 어셈블리, 응용 프로그램의 어셈블리를 포함 하 여 자동 로드를 방지 합니다.

**키**: preventHostingStartup  
**형식**: *bool* (`true` 또는 `1`)  
**기본**: false  
**사용 하 여 설정**:`UseSetting`

이 기능은 ASP.NET 코어 2.0의 새로운 기능입니다.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.PreventHostingStartupKey, "true")
    ...
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

이 기능은 ASP.NET Core에서 사용할 수 없는 1.x 합니다.

---

### <a name="server-urls"></a>서버 Url

IP 주소 또는 서버에서 요청을 수신 해야 하는 포트와 프로토콜 인 호스트 주소를 나타냅니다.

**키**: url  
**형식**: *문자열*  
**기본**: http://localhost:5000  
**사용 하 여 설정**:`UseUrls`

세미콜론으로 구분 된로 설정 (;) URL의 목록 prefixes 서버 응답 해야 합니다. 예를 들어, `http://localhost:123`을 입력합니다. 사용 하 여 "\*" 서버 모든 IP 주소 또는 지정 된 포트 및 프로토콜을 사용 하 여 호스트 이름에 대 한 요청에 대 한 수신 대기 해야 함을 나타내기 위해 (예를 들어 `http://*:5000`). 프로토콜 (`http://` 또는 `https://`) 각 URL에 포함 되어 있어야 합니다. 지원 되는 형식의 서버 마다 다릅니다.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002")
    ...
```

Kestrel 자체 끝점 구성 API에 있습니다. 자세한 내용은 [ASP.NET Core의 Kestrel 웹 서버 구현](xref:fundamentals/servers/kestrel?tabs=aspnetcore2x#endpoint-configuration)을 참조하세요.

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

```csharp
var host = new WebHostBuilder()
    .UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002")
    ...
```

---

### <a name="shutdown-timeout"></a>시스템 종료 제한 시간

웹 호스트를 종료 될 때까지 기다리는 시간을 지정 합니다.

**키**: shutdownTimeoutSeconds  
**형식**: *int*  
**기본**: 5  
**사용 하 여 설정**:`UseShutdownTimeout`

키를 허용 하지만 *int* 와 `UseSetting` (예를 들어 `.UseSetting(WebHostDefaults.ShutdownTimeoutKey, "10")`), `UseShutdownTimeout` 확장 메서드를 사용는 `TimeSpan`합니다. 이 기능은 ASP.NET 코어 2.0의 새로운 기능입니다.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseShutdownTimeout(TimeSpan.FromSeconds(10))
    ...
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

이 기능은 ASP.NET Core에서 사용할 수 없는 1.x 합니다.

---

### <a name="startup-assembly"></a>시작 어셈블리

검색할 어셈블리 확인에서 `Startup` 클래스입니다.

**키**: startupAssembly  
**형식**: *문자열*  
**기본**: 응용 프로그램의 어셈블리  
**사용 하 여 설정**:`UseStartup`

이름으로 어셈블리를 참조할 수 있습니다 (`string`) 또는 형식 (`TStartup`). 여러 개인 경우 `UseStartup` 메서드가 호출 되어, 마지막 우선적으로 적용 합니다.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseStartup("StartupAssemblyName")
    ...
```

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseStartup<TStartup>()
    ...
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

```csharp
var host = new WebHostBuilder()
    .UseStartup("StartupAssemblyName")
    ...
```

```csharp
var host = new WebHostBuilder()
    .UseStartup<TStartup>()
    ...
```

---

### <a name="web-root"></a>웹 루트

응용 프로그램의 정적 자산에 대 한 상대 경로 설정합니다.

**키**: webroot  
**형식**: *문자열*  
**기본**: 기본값은 "(Content Root)/wwwroot 지정 하지 않으면" 경로가 존재 하는 경우. 경로가 존재 하지 않는 경우 아무 파일 공급자가 사용 됩니다.  
**사용 하 여 설정**:`UseWebRoot`

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseWebRoot("public")
    ...
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

```csharp
var host = new WebHostBuilder()
    .UseWebRoot("public")
    ...
```

---

## <a name="overriding-configuration"></a>구성을 무시

사용 하 여 [구성](configuration.md) 호스트를 구성할 수 있습니다. 다음 예제에서는 호스트 구성을 필요에 따라 지정에 *hosting.json* 파일입니다. 모든 구성에서 로드 된 *hosting.json* 명령줄 인수 파일을 덮어쓸 수 있습니다. 기본 제공된 구성 (에 `config`) 사용 하 여 호스트를 구성 하는 데는 `UseConfiguration`합니다.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

*hosting.json*:

```json
{
    urls: "http://*:5005"
}
```

제공 하는 구성을 재정의 `UseUrls` 와 *hosting.json* config 첫 번째, 명령줄 인수 구성 두 번째:

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        BuildWebHost(args).Run();
    }

    public static IWebHost BuildWebHost(string[] args)
    {
        var config = new ConfigurationBuilder()
            .SetBasePath(Directory.GetCurrentDirectory())
            .AddJsonFile("hosting.json", optional: true)
            .AddCommandLine(args)
            .Build();

        return WebHost.CreateDefaultBuilder(args)
            .UseUrls("http://*:5000")
            .UseConfiguration(config)
            .Configure(app =>
            {
                app.Run(context => 
                    context.Response.WriteAsync("Hello, World!"));
            })
            .Build();
    }
}
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

*hosting.json*:

```json
{
    urls: "http://*:5005"
}
```

제공 하는 구성을 재정의 `UseUrls` 와 *hosting.json* config 첫 번째, 명령줄 인수 구성 두 번째:

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        var config = new ConfigurationBuilder()
            .SetBasePath(Directory.GetCurrentDirectory())
            .AddJsonFile("hosting.json", optional: true)
            .AddCommandLine(args)
            .Build();

        var host = new WebHostBuilder()
            .UseUrls("http://*:5000")
            .UseConfiguration(config)
            .UseKestrel()
            .Configure(app =>
            {
                app.Run(context => 
                    context.Response.WriteAsync("Hello, World!"));
            })
            .Build();

        host.Run();
    }
}
```

---

> [!NOTE]
> `UseConfiguration` 확장 메서드는 현재에서 반환 되는 구성 섹션을 구문 분석할 수 없습니다 `GetSection` (예를 들어 `.UseConfiguration(Configuration.GetSection("section"))`합니다. `GetSection` 메서드 요청 섹션에 있는 구성 키를 필터링 되지만 섹션 이름에는 키 (예를 들어 `section:urls`, `section:environment`). `UseConfiguration` 메서드에서 예상 키와 일치 하도록는 `WebHostBuilder` 키 (예를 들어 `urls`, `environment`). 키에 대해 섹션 이름의 존재는 호스트 구성에서 섹션의 값을 방지 합니다. 이 문제는 향후 릴리스에서 해결될 예정입니다. 자세한 내용 및 해결 방법에 대 한 참조 [전체 키를 사용 하 여 WebHostBuilder.UseConfiguration에 구성 섹션을 전달](https://github.com/aspnet/Hosting/issues/839)합니다.

특정 URL에서 실행 하는 호스트를 지정 하려면 전달할 수 있습니다는 원하는 값에 명령 프롬프트에서 실행할 때 `dotnet run`합니다. 명령줄 인수 재정의 `urls` 에서 값의 *hosting.json* 포트 8080에서 수신 하는 파일 및 서버:

```console
dotnet run --urls "http://*:8080"
```

## <a name="ordering-importance"></a>순서 중요성

일부는 `WebHostBuilder` 설정을 먼저 읽습니다에서 환경 변수를 설정 합니다. 이러한 환경 변수 형식을 사용 하 여 `ASPNETCORE_{configurationKey}`합니다. 설정 하면 기본적으로에서 서버가 수신 하는 Url을 설정 하려면 `ASPNETCORE_URLS`합니다.

구성을 지정 하 여 이러한 환경 변수 값을 재정의할 수 있습니다 (사용 하 여 `UseConfiguration`) 또는 값을 명시적으로 설정 하 여 (사용 하 여 `UseSetting` 명시적 확장 메서드 중 하나 또는 같은 `UseUrls`). 호스트 선택한 옵션에 설정 하는 마지막 값을 사용 합니다. 프로그래밍 방식으로 값 중 하나에 기본 URL을 설정 되었지만 구성으로 재정의 될 수 있도록 하려는 경우 URL을 설정 후 명령줄 구성을 사용할 수 있습니다. 참조 [덮어씁니다 구성](#overriding-configuration)합니다.

## <a name="starting-the-host"></a>호스트를 시작합니다.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

**실행**

`Run` 메서드 웹 앱을 시작 하 고 호스트 종료 될 때까지 호출 스레드를 차단 합니다.

```csharp
host.Run();
```

**Start**

호출 하 여 비블로킹 방식으로 호스트를 실행할 수 있습니다는 `Start` 메서드:

```csharp
using (host)
{
    host.Start();
    Console.ReadLine();
}
```

에 대 한 Url 목록을 전달 하는 경우는 `Start` 지정 된 Url에서 수신 대기 메서드:

```csharp
var urls = new List<string>()
{
    "http://*:5000",
    "http://localhost:5001"
};

var host = new WebHostBuilder()
    .UseKestrel()
    .UseStartup<Startup>()
    .Start(urls.ToArray());

using (host)
{
    Console.ReadLine();
}
```

초기화 하 고의 미리 구성 된 기본값을 사용 하 여 새 호스트를 시작할 수 있습니다 `CreateDefaultBuilder` 정적 편의 메서드를 사용 합니다. 이러한 메서드는을 콘솔 출력 하지 않고 서버를 시작 [WaitForShutdown](/dotnet/api/microsoft.aspnetcore.hosting.webhostextensions.waitforshutdown) (Ctrl-C/SIGINT 또는 SIGTERM) 중단 될 때까지 기다리는:

**시작 (RequestDelegate 앱)**

로 시작는 `RequestDelegate`:

```csharp
using (var host = WebHost.Start(app => app.Response.WriteAsync("Hello, World!")))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

브라우저에서 요청 수행 `http://localhost:5000` "Hello World!" 응답을 받을 수 `WaitForShutdown`(Ctrl-C/SIGINT 또는 SIGTERM) 중단이 발생 될 때까지 차단 됩니다. 응용 프로그램 표시는 `Console.WriteLine` 메시지 및 대기 키 누르기를 종료 합니다.

**시작 (string url, RequestDelegate 앱)**

URL로 시작 하 고 `RequestDelegate`:

```csharp
using (var host = WebHost.Start("http://localhost:8080", app => app.Response.WriteAsync("Hello, World!")))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

것과 동일한 결과가 생성 **시작 (RequestDelegate app)**를 제외 하 고 응용 프로그램에 대해 응답 `http://localhost:8080`합니다.

**시작 (동작<IRouteBuilder> routeBuilder)**

인스턴스를 사용 하 여 `IRouteBuilder` ([Microsoft.AspNetCore.Routing](https://www.nuget.org/packages/Microsoft.AspNetCore.Routing/)) 라우팅 미들웨어를 사용 하려면:

```csharp
using (var host = WebHost.Start(router => router
    .MapGet("hello/{name}", (req, res, data) => 
        res.WriteAsync($"Hello, {data.Values["name"]}!"))
    .MapGet("buenosdias/{name}", (req, res, data) => 
        res.WriteAsync($"Buenos dias, {data.Values["name"]}!"))
    .MapGet("throw/{message?}", (req, res, data) => 
        throw new Exception((string)data.Values["message"] ?? "Uh oh!"))
    .MapGet("{greeting}/{name}", (req, res, data) => 
        res.WriteAsync($"{data.Values["greeting"]}, {data.Values["name"]}!"))
    .MapGet("", (req, res, data) => res.WriteAsync("Hello, World!"))))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

이 예제에서는 다음 브라우저 요청에서 사용 합니다.

| 요청                                    | 응답                                 |
| ------------------------------------------ | ---------------------------------------- |
| `http://localhost:5000/hello/Martin`       | Hello, Martin!                           |
| `http://localhost:5000/buenosdias/Catrina` | Catrina 부에노스아이레스 dias!                    |
| `http://localhost:5000/throw/ooops!`       | "Ooops!" 문자열을 사용 하 여 예외를 throw합니다. |
| `http://localhost:5000/throw`              | 문자열을 사용 하 여 예외를 throw "Uh 오!" |
| `http://localhost:5000/Sante/Kevin`        | Sante, Kevin!                            |
| `http://localhost:5000`                    | Hello World!                             |

`WaitForShutdown`(Ctrl-C/SIGINT 또는 SIGTERM) 중단이 발생 될 때까지 차단 됩니다. 응용 프로그램 표시는 `Console.WriteLine` 메시지 및 대기 키 누르기를 종료 합니다.

**시작 (문자열 url 동작<IRouteBuilder> routeBuilder)**

URL과의 인스턴스를 사용 하 여 `IRouteBuilder`:

```csharp
using (var host = WebHost.Start("http://localhost:8080", router => router
    .MapGet("hello/{name}", (req, res, data) => 
        res.WriteAsync($"Hello, {data.Values["name"]}!"))
    .MapGet("buenosdias/{name}", (req, res, data) => 
        res.WriteAsync($"Buenos dias, {data.Values["name"]}!"))
    .MapGet("throw/{message?}", (req, res, data) => 
        throw new Exception((string)data.Values["message"] ?? "Uh oh!"))
    .MapGet("{greeting}/{name}", (req, res, data) => 
        res.WriteAsync($"{data.Values["greeting"]}, {data.Values["name"]}!"))
    .MapGet("", (req, res, data) => res.WriteAsync("Hello, World!"))))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

것과 동일한 결과가 생성 **시작 (동작<IRouteBuilder> routeBuilder)**를 제외 하 고 응용 프로그램에서 응답 `http://localhost:8080`합니다.

**StartWith (동작<IApplicationBuilder> 앱)**

구성 하는 대리자를 제공는 `IApplicationBuilder`:

```csharp
using (var host = WebHost.StartWith(app => 
    app.Use(next => 
    {
        return async context => 
        {
            await context.Response.WriteAsync("Hello World!");
        };
    })))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

브라우저에서 요청 수행 `http://localhost:5000` "Hello World!" 응답을 받을 수 `WaitForShutdown`(Ctrl-C/SIGINT 또는 SIGTERM) 중단이 발생 될 때까지 차단 됩니다. 응용 프로그램 표시는 `Console.WriteLine` 메시지 및 대기 키 누르기를 종료 합니다.

**StartWith (문자열 url 동작<IApplicationBuilder> 앱)**

URL을 구성 하기 위한 대리자를 제공는 `IApplicationBuilder`:

```csharp
using (var host = WebHost.StartWith("http://localhost:8080", app => 
    app.Use(next => 
    {
        return async context => 
        {
            await context.Response.WriteAsync("Hello World!");
        };
    })))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

것과 동일한 결과가 생성 **StartWith (동작<IApplicationBuilder> 응용 프로그램)**를 제외 하 고 응용 프로그램에 대해 응답 `http://localhost:8080`합니다.

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

**실행**

`Run` 메서드 웹 앱을 시작 하 고 호스트 종료 될 때까지 호출 스레드를 차단 합니다.

```csharp
host.Run();
```

**Start**

호출 하 여 비블로킹 방식으로 호스트를 실행할 수 있습니다는 `Start` 메서드:

```csharp
using (host)
{
    host.Start();
    Console.ReadLine();
}
```

에 대 한 Url 목록을 전달 하는 경우는 `Start` 지정 된 Url에서 수신 대기 메서드:


```csharp
var urls = new List<string>()
{
    "http://*:5000",
    "http://localhost:5001"
};

var host = new WebHostBuilder()
    .UseKestrel()
    .UseStartup<Startup>()
    .Start(urls.ToArray());

using (host)
{
    Console.ReadLine();
}
```

---

## <a name="ihostingenvironment-interface"></a>IHostingEnvironment 인터페이스

[IHostingEnvironment 인터페이스](/aspnet/core/api/microsoft.aspnetcore.hosting.ihostingenvironment) 응용 프로그램의 웹 호스팅 환경에 대 한 정보를 제공 합니다. 사용할 수 있습니다 [생성자 삽입](xref:fundamentals/dependency-injection) 얻으려고는 `IHostingEnvironment` 속성 및 확장 메서드를 사용 하려면:

```csharp
public class CustomFileReader
{
    private readonly IHostingEnvironment _env;

    public CustomFileReader(IHostingEnvironment env)
    {
        _env = env;
    }

    public string ReadFile(string filePath)
    {
        var fileProvider = _env.WebRootFileProvider;
        // Process the file here
    }
}
```

사용할 수는 [규칙 기반 접근 방식을](xref:fundamentals/environments#startup-conventions) 를 환경에 따라 시작할 때 응용 프로그램을 구성 합니다. 삽입할 수 있습니다는 `IHostingEnvironment` 에 `Startup` 생성자에서 사용 하기 위해 `ConfigureServices`:

```csharp
public class Startup
{
    public Startup(IHostingEnvironment env)
    {
        HostingEnvironment = env;
    }

    public IHostingEnvironment HostingEnvironment { get; }

    public void ConfigureServices(IServiceCollection services)
    {
        if (HostingEnvironment.IsDevelopment())
        {
            // Development configuration
        }
        else
        {
            // Staging/Production configuration
        }

        var contentRootPath = HostingEnvironment.ContentRootPath;
    }
}
```

> [!NOTE]
> 이외에 `IsDevelopment` 확장 메서드를 `IHostingEnvironment` 제공 `IsStaging`, `IsProduction`, 및 `IsEnvironment(string environmentName)` 메서드. 참조 [여러 환경 작업](xref:fundamentals/environments) 대 한 자세한 내용은 합니다.

`IHostingEnvironment` 서비스에 직접도 삽입 될 수 있습니다는 `Configure` 처리 파이프라인을 설정 하기 위한 메서드:

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        // In Development, use the developer exception page
        app.UseDeveloperExceptionPage();
    }
    else
    {
        // In Staging/Production, route exceptions to /error
        app.UseExceptionHandler("/error");
    }

    var contentRootPath = env.ContentRootPath;
}
```

삽입할 수 있으며 `IHostingEnvironment` 에 `Invoke` 메서드를 사용자 지정을 만들 때 [미들웨어](xref:fundamentals/middleware#writing-middleware):

```csharp
public async Task Invoke(HttpContext context, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        // Configure middleware for Development
    }
    else
    {
        // Configure middleware for Staging/Production
    }

    var contentRootPath = env.ContentRootPath;
}
```

## <a name="iapplicationlifetime-interface"></a>IApplicationLifetime 인터페이스

[IApplicationLifetime 인터페이스](/aspnet/core/api/microsoft.aspnetcore.hosting.iapplicationlifetime) 후 시작 및 종료 작업을 수행할 수 있습니다. 인터페이스에서 세 가지 속성으로 등록할 수 있는 취소 토큰은 `Action` 시작 및 종료 이벤트를 정의 하는 메서드. 또한 한 `StopApplication` 메서드.

| 취소 토큰    | 트리거된 경우 &#8230; |
| --------------------- | --------------------- |
| `ApplicationStarted`  | 호스트 시작 완벽 하 게 했습니다. |
| `ApplicationStopping` | 호스트에서 정상 종료를 수행 하 고 있습니다. 요청은 계속 처리할 수 있습니다. 이 이벤트가 완료 될 때까지 차단을 종료 합니다. |
| `ApplicationStopped`  | 호스트에서 정상 종료를 완료 합니다. 모든 요청을 완전히 처리 되어야 합니다. 이 이벤트가 완료 될 때까지 차단을 종료 합니다. |

| 메서드            | 작업                                           |
| ----------------- | ------------------------------------------------ |
| `StopApplication` | 현재 응용 프로그램의 종료를 요청 합니다. |

```csharp
public class Startup 
{
    public void Configure(IApplicationBuilder app, IApplicationLifetime appLifetime) 
    {
        appLifetime.ApplicationStarted.Register(OnStarted);
        appLifetime.ApplicationStopping.Register(OnStopping);
        appLifetime.ApplicationStopped.Register(OnStopped);

        Console.CancelKeyPress += (sender, eventArgs) =>
        {
            appLifetime.StopApplication();
            // Don't terminate the process immediately, wait for the Main thread to exit gracefully.
            eventArgs.Cancel = true;
        };
    }

    private void OnStarted()
    {
        // Perform post-startup activities here
    }

    private void OnStopping()
    {
        // Perform on-stopping activities here
    }

    private void OnStopped()
    {
        // Perform post-stopped activities here
    }
}
```

## <a name="troubleshooting-systemargumentexception"></a>System.ArgumentException 문제 해결

**ASP.NET Core 2.0만에 적용 됩니다.**

삽입 하 여 호스트를 작성 하는 경우 `IStartup` 호출 보다는 종속성 주입 컨테이너에 직접 `UseStartup` 또는 `Configure`, 다음과 같은 오류가 발생할 수 있습니다: `Unhandled Exception: System.ArgumentException: A valid non-empty application name must be provided`합니다.

이 때문에 발생는 [applicationName(ApplicationKey)](/aspnet/core/api/microsoft.aspnetcore.hosting.webhostdefaults#Microsoft_AspNetCore_Hosting_WebHostDefaults_ApplicationKey) (현재 어셈블리)를 검색 하는 데 필요한 `HostingStartupAttributes`합니다. 수동으로 삽입할 경우 `IStartup` 종속성 주입 컨테이너에는 다음 호출을 추가 하면 `WebHostBuilder` 지정 된 어셈블리 이름:

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting("applicationName", "<Assembly Name>")
    ...
```

Dummy 또는 추가할 `Configure` 하 여 `WebHostBuilder`로 설정 하는 `applicationName`(`ApplicationKey`) 자동으로:

```csharp
WebHost.CreateDefaultBuilder(args)
    .Configure(_ => { })
    ...
```

**참고**:는 유일한 및 ASP.NET 코어 2.0 릴리스로 필요한 경우에 호출 하지 않으면 `UseStartup` 또는 `Configure`합니다.

자세한 내용은 참조 [공지: Microsoft.Extensions.PlatformAbstractions 되었습니다 (주석)를 제거](https://github.com/aspnet/Announcements/issues/237#issuecomment-323786938) 및 [StartupInjection 샘플](https://github.com/aspnet/Hosting/blob/8377d226f1e6e1a97dabdb6769a845eeccc829ed/samples/SampleStartups/StartupInjection.cs)합니다.

## <a name="additional-resources"></a>추가 리소스

* [IIS를 사용 하 여 Windows에 게시](../publishing/iis.md)
* [Nginx를 사용 하 여 Linux에 게시](../publishing/linuxproduction.md)
* [Apache를 사용 하 여 Linux에 게시](../publishing/apache-proxy.md)
* [Windows 서비스의 호스트](xref:hosting/windows-service)
