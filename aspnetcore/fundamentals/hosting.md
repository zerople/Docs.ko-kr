---
title: "ASP.NET Core에서 호스팅 | Microsoft Docs"
author: ardalis
description: "ASP.NET Core의 웹 호스트에 소개 합니다."
keywords: "ASP.NET Core, IWebHost 웹 호스트"
ms.author: riande
manager: wpickett
ms.date: 08/02/2017
ms.topic: article
ms.assetid: 4e45311d-8d56-46e2-b99d-6f65b648a277
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/hosting
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0725f3d2c130068094792e5ba9e17481155e4294
ms.sourcegitcommit: 74e22e08e3b08cb576e5184d16f4af5656c13c0c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2017
---
# <a name="introduction-to-hosting-in-aspnet-core"></a>ASP.NET Core에서 호스팅 소개

으로 [Steve Smith](http://ardalis.com)

ASP.NET Core 응용 프로그램을 실행 하려면 구성 하 고 사용 하 여 호스트를 시작할 `WebHostBuilder`합니다.

## <a name="what-is-a-host"></a>호스트 란?

ASP.NET Core 응용 프로그램에서는 한 *호스트* 를 실행 합니다. 호스트를 구현 해야 합니다는 `IWebHost` 기능 및 서비스의 컬렉션을 노출 하는 인터페이스 및 `Start` 메서드. 호스트의 인스턴스를 사용 하 여 일반적으로 생성 됩니다는 `WebHostBuilder`, 작성을 반환 하는 `WebHost` 인스턴스. `WebHost` 요청을 처리 하는 서버를 참조 합니다. 에 대 한 자세한 내용은 [서버](servers/index.md)합니다.

### <a name="what-is-the-difference-between-a-host-and-a-server"></a>호스트와 서버 간의 차이점은 무엇입니까?

호스트는 응용 프로그램 시작 및 수명 관리 담당 합니다. 서버는 HTTP 요청을 수락 하는 일을 담당 합니다. 호스트의 책임의 일부 포함 응용 프로그램의 서비스와 서버 조건이 사용 가능 하며 올바르게 구성 되었는지 확인 합니다. 호스트 서버에 대 한 래퍼로 것으로 생각할 수 있습니다. 호스트가; 특정 서버를 사용 하도록 구성 되어 서버는 호스트의 인식 하지 않습니다.

## <a name="setting-up-a-host"></a>호스트 설정

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

호스트의 인스턴스를 사용 하 여 만든 `WebHostBuilder`합니다. 응용 프로그램의 진입점에서 일반적으로 이렇게: `public static void Main` (프로젝트 템플릿에서 있는 한 *Program.cs* 파일). 일반적인 *Program.cs*표시 아래 사용 하는 방법을 보여 줍니다는 `WebHostBuilder` 호스트를 작성 합니다.

[!code-csharp[Main](../common/samples/WebApplication1/Program.cs?highlight=14,15,16,17,18,19,20,21)]

`WebHostBuilder` 담당 하는 호스트를 만드는 응용 프로그램에 대 한 서버 부트스트랩 합니다. `WebHostBuilder`구현 하는 서버를 제공 해야 `IServer` (`UseKestrel` 위의 코드에서). `UseKestrel`응용 프로그램에서 사용할 Kestrel 서버를 지정 합니다.

서버의 *콘텐츠 루트* MVC 뷰 파일과 같은 콘텐츠 파일에 대 한 검색 위치를 결정 합니다. 기본 콘텐츠 루트는 응용 프로그램이 실행 되는 폴더입니다.

> [!NOTE]
> 지정 `Directory.GetCurrentDirectory` 이 폴더에서 앱이 시작 될 때 콘텐츠 루트 응용 프로그램의 콘텐츠 루트로 웹 프로젝트의 루트 폴더 사용 됩니다 (예를 들어 호출 `dotnet run` 웹 프로젝트 폴더에서). 이 Visual Studio에서 사용 되는 기본 및 `dotnet new` 템플릿.

IIS는 역방향 프록시를 사용 하려면 호출 `UseIISIntegration` 호스트 빌드 과정의 일환으로 합니다. 

`UseIISIntegration` 구성 하지 않는 한 *서버*처럼 `UseKestrel` 않습니다. IIS에서 ASP.NET Core를 사용 하려면 둘 다 지정 해야 하면 `UseKestrel` 및 `UseIISIntegration`합니다. `UseKestrel`웹 서버를 만들고 응용 프로그램 호스트 합니다. `UseIISIntegration`IIS/IISExpress에서 사용 하는 환경 변수를 검사 하 고 포트에서 수신 하도록와 헤더를 사용 하는 등 설정을 구성 합니다.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET 2.x 핵심](#tab/aspnetcore2x)

호스트의 인스턴스를 사용 하 여 만든 `WebHostBuilder`합니다. 응용 프로그램의 진입점에서 일반적으로 이렇게: `public static void Main` (프로젝트 템플릿에서 있는 한 *Program.cs* 파일). 일반적인 *Program.cs*호출 아래에 표시 된, `CreateDefaultbuilder` 호스트 구축 하려면:

[!code-csharp[Main](../common/samples/WebApplication1DotNetCore2.0App/Program.cs?name=snippet_Main&highlight=9)]

`CreateDefaultbuilder`인스턴스를 만들고 `WebHostBuilder` 응용 프로그램에 대 한 서버 시작 되도록 하는 호스트를 작성 합니다. 호스트 필요는 [IServer를 구현 하는 서버](servers/index.md)합니다. 기본 제공 서버는 [Kestrel](servers/kestrel.md) 및 [HTTP.sys](servers/httpsys.md); `CreateDefaultbuilder` Kestrel는 기본적으로 사용 합니다.

`CreateDefaultbuilder`Kestrel 웹 서버와 구성 외에도 설정 작업을 수행 합니다.

* 설정 하는 콘텐츠 루트 `Directory.GetCurrentDirectory`합니다.
* 구성을 로드 합니다.
  * *appsettings.json*
  * *appsettings 합니다. \<EnvironmentName >.json*합니다.
  * 개발 환경에서 앱을 실행할 때 사용자의 비밀
  * 환경 변수
  * 제공 된 명령줄 인수
* 로깅 구성 섹션에 지정 된 규칙을 필터링 된 콘솔 및 디버그 출력에 대 한 로깅을 구성 합니다.
* IIS 통합할을 수 있습니다.
* 개발 환경에서 앱을 실행할 때 개발자 예외 페이지를 추가 합니다.

서버의 *콘텐츠 루트* MVC 뷰 파일과 같은 콘텐츠 파일에 대 한 검색 위치를 결정 합니다. 기본 콘텐츠 루트는 응용 프로그램이 실행 되는 폴더입니다.

> [!NOTE]
> 지정 `Directory.GetCurrentDirectory` 이 폴더에서 앱이 시작 될 때 콘텐츠 루트 응용 프로그램의 콘텐츠 루트로 웹 프로젝트의 루트 폴더 사용 됩니다 (예를 들어 호출 `dotnet run` 웹 프로젝트 폴더에서). 이 Visual Studio에서 사용 되는 기본 및 `dotnet new` 템플릿.

ASP.NET Core를 자동으로 호출 IIS를 사용 하 여 역방향 프록시로 때 `UseIISIntegration` 호스트 빌드 과정의 일환으로 합니다. 자세한 내용은 참조 [ASP.NET Core 모듈](xref:fundamentals/servers/aspnet-core-module)합니다.

`UseIISIntegration` 구성 하지 않는 한 *서버*처럼 `UseKestrel` 않습니다. `UseKestrel`웹 서버를 만들고 응용 프로그램 호스트 합니다. `UseIISIntegration`IIS/IISExpress에서 사용 하는 환경 변수를 검사 하 고 포트에서 수신 하도록와 헤더를 사용 하는 등 설정을 구성 합니다.

---

호스트 (및 ASP.NET Core 응용 프로그램) 구성의 최소 구현을 서버 및 응용 프로그램의 요청 파이프라인의 구성만 포함:

```csharp
var host = new WebHostBuilder()
    .UseKestrel()
    .Configure(app =>
    {
        app.Run(async (context) => await context.Response.WriteAsync("Hi!"));
    })
    .Build();

host.Run();
```

> [!NOTE]
> 제공할 수는 호스트를 설정할 때 `Configure` 및 `ConfigureServices` 방법, 대신 또는 지정 외에도 `Startup` 클래스 (이러한 방법-을 정의 해야 하는 참조 [응용 프로그램 시작](startup.md)). 여러 번 호출 `ConfigureServices` 서로 추가할;에 대 한 호출이 `Configure` 또는 `UseStartup` 이전 설정으로 바뀝니다.

## <a name="configuring-a-host"></a>호스트 구성

`WebHostBuilder` 메서드를 사용 하 여 직접도 설정할 수 있는 호스트에 대 한 대부분의 사용 가능한 구성 값을 설정 하기 위한 제공 `UseSetting` 와 연결 된 키입니다.

### <a name="host-configuration-values"></a>호스트 구성 값

**시작 오류 캡처**`bool`

키: `captureStartupErrors`합니다. 기본값은 `false`입니다. 때 `false`, 종료 하는 호스트에서 시작 결과 동안 오류가 발생 합니다. 때 `true`, 호스트에서 발생 한 예외를 캡처합니다는 `Startup` 클래스 및 서버를 시작 하려고 합니다. 오류 페이지가 표시 됩니다 (일반, 또는 자세한, 설정에 따라 자세한 오류, 아래) 모든 요청에 대 한 합니다. 사용 하 여 설정 된 `CaptureStartupErrors` 메서드.

참고: 앱 Kestrel 및 IIS를 실행 하는 경우 기본 동작은 시작 오류 캡처합니다. 

```csharp
new WebHostBuilder()
    .CaptureStartupErrors(true)
   ```

**루트 콘텐츠**`string`

키: `contentRoot`합니다. 기본값으로 설정 (Kestrel;에 대 한 응용 프로그램 어셈블리 파일이 있는 폴더 IIS에서 웹 프로젝트 루트 기본적으로 사용). 이 설정은 MVC 뷰 등의 콘텐츠 파일을 검색할 ASP.NET 코어는 시작 위치를 결정 합니다. 에 대 한 기본 경로로 사용도 [웹 루트 설정](#web-root-setting)합니다. 사용 하 여 설정 된 `UseContentRoot` 메서드. 경로가 존재 해야 합니다 또는 호스트 시작 되지 것입니다.

```csharp
new WebHostBuilder()
    .UseContentRoot("c:\\mywebsite")
   ```

**자세한 오류**`bool`

키: `detailedErrors`합니다. 기본값은 `false`입니다. 때 `true` (또는 환경 "개발"로 설정 된 경우) 응용 프로그램에는 일반 오류 페이지 대신 시작 예외의 세부 정보가 표시 됩니다. 사용 하 여 설정 `UseSetting`합니다.

```csharp
new WebHostBuilder()
    .UseSetting("detailedErrors", "true")
```

자세한 오류로 설정 된 경우 `false` 캡처 시작 오류 이며 `true`, 서버에 모든 요청에 대 한 응답에는 일반 오류 페이지가 표시 됩니다.

![일반 오류 페이지](hosting/_static/generic-error-page.png)

자세한 오류로 설정 된 경우 `true` 캡처 시작 오류 이며 `true`, 서버에 모든 요청에 대 한 응답에 자세한 오류 페이지가 표시 됩니다.

![자세한 오류 페이지](hosting/_static/detailed-error-page.png)

**환경**`string`

키: `environment`합니다. 기본값은 "Production"입니다. 값으로 설정할 수 있습니다. 프레임 워크에서 정의 된 값 "개발", "준비" 및 "Production"를 포함 합니다. 값은 대/소문자 구분 하지 않습니다. 참조 [여러 환경 작업](environments.md)합니다. 사용 하 여 설정 된 `UseEnvironment` 메서드.

```csharp
new WebHostBuilder()
    .UseEnvironment("Development")
```

> [!NOTE]
> 기본적으로 환경에서 읽기는 `ASPNETCORE_ENVIRONMENT` 환경 변수입니다. Visual Studio를 사용 하는 경우 환경 변수를 설정할 수 있습니다는 *launchSettings.json* 파일입니다.

<a id="server-urls"></a>

**서버 Url**`string`

키: `urls`합니다. 세미콜론 (;)으로 설정 분리 된 URL의 목록 prefixes 서버 응답 해야 합니다. 예를 들어, `http://localhost:123`을 입력합니다. 도메인/호스트 이름으로 대체 될 수 있습니다 "\*" 서버에서 모든 IP 주소에서 요청을 수신 대기 하거나 지정 된 포트 및 프로토콜을 사용 하 여 호스트를 나타냅니다 (예를 들어 `http://*:5000` 또는 `https://*:5001`). 프로토콜 (`http://` 또는 `https://`) 각 URL에 포함 되어 있어야 합니다. 접두사 구성 된 서버에서 해석 하는 지원 되는 형식의 서버 간에 달라 집니다.

```csharp
new WebHostBuilder()
    .UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002")
```

ASP.NET Core 2.0에서 Kestrel 구성이 자체 끝점 API와 지원 하지 않습니다 `https://` 에 `urls` 문자열입니다. 자세한 내용은 참조 [Kestrel 소개](xref:fundamentals/servers/kestrel?tabs=aspnetcore2x#endpoint-configuration)합니다.

**시작 어셈블리**`string`

키: `startupAssembly`합니다. 검색할 어셈블리 확인에서 `Startup` 클래스입니다. 사용 하 여 설정 된 `UseStartup` 메서드. 대신 특정 형식을 사용 하 여 참조 수 `WebHostBuilder.UseStartup<StartupType>`합니다. 여러 개인 경우 `UseStartup` 메서드가 호출 되어, 마지막 우선적으로 적용 합니다.

```csharp
new WebHostBuilder()
    .UseStartup("StartupAssemblyName")
```

<a name=web-root-setting></a>

**루트 웹**`string`

키: `webroot`합니다. 기본값은 지정 하지 않으면 `(Content Root Path)\wwwroot`있는 경우. 이 경로가 존재 하지 않는 경우 아무 파일 공급자가 사용 됩니다. 사용 하 여 설정 `UseWebRoot`합니다.

```csharp
new WebHostBuilder()
    .UseWebRoot("public")
```

### <a name="overriding-configuration"></a>구성을 무시

사용 하 여 [구성](configuration.md) 호스트에서 사용할 구성 값을 설정 합니다. 이러한 값은 이후에 재정의 될 수 있습니다. 이 사용 하 여 지정 됩니다 `UseConfiguration`합니다.

```csharp
public static void Main(string[] args)
{
    var config = new ConfigurationBuilder()
        .AddJsonFile("hosting.json", optional: true)
        .AddCommandLine(args)
        .Build();

    var host = new WebHostBuilder()
        .UseConfiguration(config)
        .UseKestrel()
        .Configure(app =>
        {
            app.Run(async (context) => await context.Response.WriteAsync("Hi!"));
        })
        .Build();

    host.Run();
}
```

위의 예에서 명령줄 인수 전달 될 수 있습니다에 호스트를 구성 또는 구성 설정에 선택적으로 지정할 수 있습니다는 *hosting.json* 파일입니다. 특정 URL에서 실행 하는 호스트를 지정 하려면 명령 프롬프트에서 원하는 값에 전달할 수 있습니다.

```console
dotnet run --urls "http://*:5000"
```

`Run` 메서드 웹 앱을 시작 하 고 호스트 종료 될 때까지 호출 스레드를 차단 합니다.

```csharp
host.Run();
```

호출 하 여 비블로킹 방식으로 호스트를 실행할 수 있습니다는 `Start` 메서드:

```csharp
using (host)
{
    host.Start();
    Console.ReadLine();
}
```

에 대 한 Url의 목록을 전달는 `Start` 메서드 하며 지정 된 Url에서 수신 합니다.

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

사용 중인 서버에 사용할 수 있는 다음 URL 형식에 따라 달라 집니다. 자세한 내용은 참조 [서버 Url](#server-urls) 이 문서의 앞부분에 있습니다.

> [!NOTE]
> `UseConfiguration` 확장 메서드는 현재에서 반환 되는 구성 섹션을 구문 분석할 수 없습니다 `GetSection` (예를 들어 `.UseConfiguration(Configuration.GetSection("section"))`합니다. `GetSection` 메서드 요청 섹션에 있는 구성 키를 필터링 되지만 섹션 이름에는 키 (예를 들어 `section:urls`, `section:environment`). `UseConfiguration` 메서드에서 예상 키와 일치 하도록는 `WebHostBuilder` 키 (예를 들어 `urls`, `environment`). 키에 대해 섹션 이름의 존재는 호스트 구성에서 섹션의 값을 방지 합니다. 이 문제는 향후 릴리스에서 해결될 예정입니다. 자세한 내용 및 해결 방법에 대 한 참조 [전체 키를 사용 하 여 WebHostBuilder.UseConfiguration에 구성 섹션을 전달](https://github.com/aspnet/Hosting/issues/839)합니다.

### <a name="ordering-importance"></a>순서 중요성

`WebHostBuilder`설정은 특정 환경 변수에서 읽은 먼저 경우 설정 합니다. 이러한 환경 변수 형식을 사용 해야 `ASPNETCORE_{configurationKey}`설정 예: Url을 설정 하는 서버는 기본적으로에서 수신 하므로 `ASPNETCORE_URLS`합니다.

구성을 지정 하 여 이러한 환경 변수 값을 재정의할 수 있습니다 (사용 하 여 `UseConfiguration`) 또는 값을 명시적으로 설정 하 여 (사용 하 여 `UseUrls` 예를 들어). 호스트는 선택한 옵션에 설정 하는 마지막 값을 사용 합니다. 이러한 이유로 `UseIISIntegration` 뒤에 나타나야 합니다 `UseUrls`IIS에서 동적으로 제공 된 URL을 없어지기 때문에, 합니다. 프로그래밍 방식으로 기본 URL 단일 값으로 설정 되었지만 구성으로 재정의 될 수 있도록 허용 하려면 호스트를 다음과 같이 구성할 수 있습니다.

```csharp
var config = new ConfigurationBuilder()
    .AddCommandLine(args)
    .Build();

var host = new WebHostBuilder()
    .UseUrls("http://*:1000") // default URL
    .UseConfiguration(config) // override from command line
    .UseKestrel()
    .Build();
```

## <a name="additional-resources"></a>추가 리소스

* [IIS를 사용 하 여 Windows에 게시](../publishing/iis.md)
* [Nginx를 사용 하 여 Linux에 게시](../publishing/linuxproduction.md)
* [Apache를 사용 하 여 Linux에 게시](../publishing/apache-proxy.md)
* [Windows 서비스의 호스트](xref:hosting/windows-service)

