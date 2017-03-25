---
title: "ASP.NET Core에서 호스팅 | Microsoft 문서"
author: ardalis
description: "ASP.NET Core의 웹 호스트에 소개 합니다."
keywords: "ASP.NET Core, IWebHost 웹 호스트"
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 4e45311d-8d56-46e2-b99d-6f65b648a277
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/hosting
ms.custom: H1Hack27Feb2017
translationtype: Machine Translation
ms.sourcegitcommit: 7036781b7adc3a481abbf4c4c6b6d7cbcbc92348
ms.openlocfilehash: 4e29e249a638b8056671c0c2695e93347235cf54
ms.lasthandoff: 03/23/2017

---
# <a name="introduction-to-hosting-in-aspnet-core"></a>ASP.NET Core에서 호스팅 소개

[Steve Smith](http://ardalis.com)

ASP.NET 핵심 응용 프로그램을 실행 하려면 구성 하 고 사용 하 여 호스트를 시작할 `WebHostBuilder`합니다.

## <a name="what-is-a-host"></a>호스트는 무엇입니까?

ASP.NET 핵심 응용 프로그램에서는 *호스트* 를 실행 합니다. 호스트를 구현 해야는 `IWebHost` 기능 및 서비스의 컬렉션을 노출 하는 인터페이스 및 `Start` 메서드. 호스트는 일반적으로 생성 하 여 인스턴스를 사용 하는 `WebHostBuilder`, 빌드에 반환 하는 한 `WebHost` 인스턴스. `WebHost` 요청을 처리 하는 서버를 참조 합니다. 에 대 한 자세한 내용은 [서버](servers/index.md)합니다.

### <a name="what-is-the-difference-between-a-host-and-a-server"></a>호스트와 서버 간의 차이점은 무엇입니까?

호스트 응용 프로그램 시작 및 수명 관리 담당합니다. 서버는 HTTP 요청을 수락 하는 일을 담당 합니다. 호스트의 책임의 일부 서버와 응용 프로그램의 서비스 사용 가능 하 고 올바르게 구성 되도록 포함 되어 있습니다. 호스트 서버에 대 한 래퍼로 것으로 생각할 수 있습니다. 호스트는 특정 서버를 사용 하도록 구성 된 서버가 해당 호스트의 인식 되지 않습니다.

## <a name="setting-up-a-host"></a>호스트 설정

호스트의 인스턴스를 사용 하 여 만든 `WebHostBuilder`합니다. 일반적으로 응용 프로그램의 진입점에서 수행 됩니다: `public static void Main`, (프로젝트 템플릿에서 있는 한 *Program.cs* 파일). 일반적인 *Program.cs*나와 아래에서는 사용 하는 방법을 보여 줍니다는 `WebHostBuilder` 호스트를 만들 수 있습니다.

[!code-csharp[주](../common/samples/WebApplication1/Program.cs?highlight=14,15,16,17,18,19,20,21)]

`WebHostBuilder` 담당 하는 호스트를 만드는 응용 프로그램에 대 한 서버 부트스트랩 합니다. `WebHostBuilder`구현 하는 서버를 제공 해야 `IServer` (`UseKestrel` 위 코드에서). `UseKestrel`응용 프로그램에서 사용할 Kestrel 서버를 지정 합니다.

서버의 *콘텐츠 루트* MVC 뷰 파일과 같은 콘텐츠 파일에 대 한 검색 위치를 결정 합니다. 기본 콘텐츠 루트는 응용 프로그램이 실행 하는 폴더입니다.

> [!NOTE]
> 지정 `Directory.GetCurrentDirectory` 콘텐츠 루트에서 사용할 웹 프로젝트의 루트 폴더 응용 프로그램의 콘텐츠 루트도이 폴더에서 앱이 시작 될 때 (예를 들어 호출 `dotnet run` 웹 프로젝트 폴더에서). 이 Visual Studio에서 사용 되는 기본값 및 `dotnet new` 템플릿.

Iis에서 응용 프로그램 작동 해야 하는 경우는 `UseIISIntegration` 호스트 빌드의 일부로 메서드를 호출 해야 합니다. 구성 하지 않는 참고는 *서버*마찬가지로 `UseKestrel` 않습니다. IIS에서 ASP.NET 코어를 사용 하려면 모두 지정 해야 `UseKestrel` 및 `UseIISIntegration`합니다. Kestrel은 프록시 뒤에 실행 되도록 설계 하 고 직접 인터넷 연결 배포 해서는 안 됩니다. `UseIISIntegration`역방향 프록시 서버와 IIS를 지정합니다.

> [!NOTE]
> `UseKestrel`및 `UseIISIntegration` 은 매우 다양 한 작업입니다. IIS는 역방향 프록시로 사용 됩니다. `UseKestrel`웹 서버를 만들고 코드를 호스트 합니다. `UseIISIntegration`역방향 프록시 서버와 IIS를 지정합니다. 또한 IIS/IISExpress에서 사용 하는 환경 변수를 검사 하 고 동적 포트와 같은 결정을 내립니다 등을 설정 하는 헤더를 사용 합니다. 그러나 처리 하지 않습니다 또는 만들기는 `IServer`합니다.

호스트 (및 ASP.NET 핵심 응용 프로그램) 구성의 최소 구현을 서버 및 응용 프로그램의 요청 파이프라인의 구성만 포함 됩니다.

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
> 호스트를 설정할 때 제공할 수 있습니다 `Configure` 및 `ConfigureServices` 메서드를 대신 또는 이러한 지정 외에 `Startup` 클래스 (이러한 메서드를 정의 해야 하는 참조 [응용 프로그램 시작](startup.md)). 여러 번 호출 `ConfigureServices` 서로 추가할;에 대 한 호출이 `Configure` 또는 `UseStartup` 이전 설정을 덮어씁니다.

## <a name="configuring-a-host"></a>호스트 구성

`WebHostBuilder` 메서드를 사용 하 여 직접도 설정할 수 있는 호스트에 대 한 대부분의 사용 가능한 구성 값을 설정 하기 위한 제공 `UseSetting` 와 연결 된 키입니다. 예를 들어, 응용 프로그램 이름을 지정 하려면:

```csharp
new WebHostBuilder()
    .UseSetting("applicationName", "MyApp")
```

### <a name="host-configuration-values"></a>호스트 구성 값

**응용 프로그램 이름**`string`

Key: `applicationName`. 반환 되는 값을 지정 하는이 구성 설정은 `IHostingEnvironment.ApplicationName`합니다.

**시작 오류 캡처**`bool`

Key: `captureStartupErrors`. 기본값은 `false`입니다. 때 `false`를 종료 하는 호스트에서 결과 시작 하는 동안 오류가 발생 합니다. 때 `true`, 호스트에서 발생 한 예외를 캡처하는 `Startup` 클래스 및 서버를 시작 하려고 합니다. 오류 페이지가 표시 됩니다 (일반, 또는 자세한 설정에 따라 자세한 오류를 아래)에 대 한 모든 요청 합니다. 사용 하 여 설정 된 `CaptureStartupErrors` 메서드.

```csharp
new WebHostBuilder()
    .CaptureStartupErrors(true)
   ```

**콘텐츠 루트**`string`

Key: `contentRoot`. 기본값 (Kestrel;에 대 한 응용 프로그램 어셈블리 파일이 있는 폴더에 IIS에서 웹 프로젝트 루트 기본적으로 사용). 이 설정은 MVC 뷰 등의 콘텐츠 파일을 검색할 ASP.NET 핵심은 시작 위치를 결정 합니다. 에 대 한 기본 경로로 사용도 [웹 루트 설정](#web-root-setting)합니다. 사용 하 여 설정 된 `UseContentRoot` 메서드. 경로가 존재 해야 하거나 호스트 시작 되지 것입니다.

```csharp
new WebHostBuilder()
    .UseContentRoot("c:\\mywebsite")
   ```

**자세한 오류**`bool`

Key: `detailedErrors`. 기본값은 `false`입니다. 때 `true` (또는 환경이 "개발"로 설정 된 경우) 앱이 일반 오류 페이지 대신 시작 예외의 세부 정보를 표시 합니다. 사용 하 여 설정 `UseSetting`합니다.

```csharp
new WebHostBuilder()
    .UseSetting("detailedErrors", "true")
```

상세 오류 정보로 설정 되 면 `false` 캡처 시작 오류는 `true`, 일반 오류 페이지를 서버에 모든 요청에 대 한 응답에 표시 됩니다.

![일반 오류 페이지](hosting/_static/generic-error-page.png)

상세 오류 정보로 설정 되 면 `true` 캡처 시작 오류는 `true`, 자세한 오류 페이지가 서버에 모든 요청에 대 한 응답에 표시 됩니다.

![자세한 오류 페이지](hosting/_static/detailed-error-page.png)

**환경**`string`

Key: `environment`. 기본값은 "프로덕션"입니다. 값으로 설정할 수 있습니다. 프레임 워크에서 정의 된 값 "개발", "준비" 및 "프로덕션"를 포함 합니다. 값은 대 소문자를 구분 하지 않습니다. 참조 [여러 환경 작업](environments.md)합니다. 사용 하 여 설정 된 `UseEnvironment` 메서드.

```csharp
new WebHostBuilder()
    .UseEnvironment("Development")
```

> [!NOTE]
> 기본적으로는 환경에서 읽기는 `ASPNETCORE_ENVIRONMENT` 환경 변수입니다. Visual Studio를 사용 하 여 환경 변수 설정할 수 있습니다는 *변경* 파일입니다.

**서버 Url**`string`

Key: `urls`. 세미콜론 (;)으로 설정 하는 서버 응답 해야 접두사 URL의 목록 구분 합니다. 예를 들어, `http://localhost:123`을 입력합니다. 도메인/호스트 이름으로 바꿀 수 있습니다 "\*" 서버는 모든 IP 주소에서 요청을 수신 대기 또는 지정 된 포트 및 프로토콜을 사용 하 여 호스트를 나타내기 위해 (예를 들어 `http://*:5000` 또는 `https://*:5001`). 프로토콜 (`http://` 또는 `https://`) 각 URL에 포함 되어야 합니다. 접두사 구성 된 서버에 의해 해석 됩니다. 지원 되는 형식은 서버 간에 달라 집니다.

```csharp
new WebHostBuilder()
    .UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002")
```

**시작 어셈블리**`string`

Key: `startupAssembly`. 검색할 어셈블리 확인에서 `Startup` 클래스입니다. 사용 하 여 설정 된 `UseStartup` 메서드. 대신 특정 형식을 사용 하 여 참조 수 `WebHostBuilder.UseStartup<StartupType>`합니다. 여러 개 있는 경우 `UseStartup` 메서드가 호출 됩니다, 마지막에 우선 합니다.

```csharp
new WebHostBuilder()
    .UseStartup("StartupAssemblyName")
```

<a name=web-root-setting></a>

**루트 웹**`string`

Key: `webroot`. 기본값은 지정 하지 않으면 `(Content Root Path)\wwwroot`있는 경우. 이 경로가 존재 하지 않으면 아무 파일 공급자가 사용 됩니다. 사용 하 여 설정 `UseWebRoot`합니다.

```csharp
new WebHostBuilder()
    .UseWebRoot("public")
```

### <a name="overriding-configuration"></a>구성을 무시

사용 하 여 [구성](configuration.md) 호스트에서 사용할 구성 값을 설정 합니다. 이러한 값은 이후에 재정의할 수 있습니다. 이 사용 하 여 지정 됩니다 `UseConfiguration`합니다.

<!-- literal_block {"ids": [], "linenos": false, "xml:space": "preserve", "language": "csharp", "highlight_args": {"hl_lines": [3, 4, 5, 6, 9]}} -->

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

위의 예제에서 명령줄 인수에 전달 될 수는 호스트를 구성 또는 구성 설정에 선택적으로 지정할 수 있습니다는 *hosting.json* 파일입니다. 특정 URL에서 실행 하는 호스트를 지정 하려면 명령 프롬프트에서 원하는 값에 전달할 수 있습니다.

```console
dotnet run --urls "http://*:5000"
```

`Run` 메서드는 웹 응용 프로그램을 시작 하 고 호스트가 종료 될 때까지 호출 스레드를 차단 합니다.

```csharp
host.Run();
```

호출 하 여 호스트에서 비블로킹 방식으로 실행할 수 있습니다 해당 `Start` 메서드:

```csharp
using (host)
{
    host.Start();
    Console.ReadLine();
}
```

에 대 한 Url의 목록을 전달할는 `Start` 메서드가 있고 지정 된 Url에서 수신 합니다.

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

### <a name="ordering-importance"></a>순서 중요성

`WebHostBuilder`설정이 특정 환경 변수에서 읽은 먼저 경우 설정 합니다. 이러한 환경 변수 형식을 사용 해야 `ASPNETCORE_{configurationKey}`이므로 설정 예: Url을 설정 하는 서버가 기본적으로 수신 대기할 `ASPNETCORE_URLS`합니다.

구성을 지정 하 여 이러한 환경 변수 값을 재정의할 수 있습니다 (사용 하 여 `UseConfiguration`) 또는 값을 명시적으로 설정 하 여 (사용 하 여 `UseUrls` 예를 들어). 호스트는 선택한 옵션에 설정 하는 마지막 값을 사용 합니다. 이러한 이유로 `UseIISIntegration` 뒤에 나타나야 `UseUrls`IIS에서 동적으로 제공 된 URL을 없어지기 때문에. 프로그래밍 방식으로 기본 URL 하나의 값을 설정 하지만 구성으로 재정의 될 수 있도록 하려는 경우 다음과 같이 호스트를 구성할 수 있습니다.

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

* [IIS에 게시](../publishing/iis.md)

* [Linux 프로덕션 환경에 게시](../publishing/linuxproduction.md)

* ASP.NET 핵심 Windows 서비스 호스팅

* 다른 응용 프로그램에 포함 된 ASP.NET 핵심 호스팅

* [Apache 웹 서버를 사용 하 여 역방향 프록시로](../publishing/apache-proxy.md)

